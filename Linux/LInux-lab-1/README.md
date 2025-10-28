# 🧠 Lab 1 — Linux Server Triage

> **Prerequisite (for instructor):** ensure the `logs/` folder contains the three files used for this lab: `auth.log`, `syslog`, and `apache_access.log`. These files should be realistic but short enough for beginners.

---

### 🎯 Objective (detailed)

You are a SOC Level 1 analyst on shift. Your SIEM flagged `webserver-01` for suspicious SSH activity. In this lab you will:

* Triaging the provided logs to determine whether an intrusion occurred,
* Identify suspicious IPs, usernames, and actions (brute-force, successful login, possible persistence),
* Correlate evidence across authentication, system, and web server logs,
* Prepare a concise `triage_report.txt` suitable for escalation to SOC Level 2.

This lab focuses on detection and triage only — no system remediation or in-depth forensics is required.

---

### ⚙️ Setup (short & direct)

Download the `auth.log`, `syslog`, and `apache_access.log` files from the **logs** section, then move them into a dedicated working directory inside your Linux VM, for example:

```bash
mkdir -p ~/lab1/logs
# move the downloaded files into ~/lab1/logs
cd ~/lab1/logs
ls
# you should see: auth.log  syslog  apache_access.log
```

---

### 🧩 Stage 1 — Quick Recon

**Goal:** Quickly get familiar with the dataset and spot obvious anomalies.

**Tasks:**

* Use `head`, `tail`, and `wc -l` to inspect file sizes and first/last lines:

  * `head -n 20 auth.log`
  * `tail -n 20 syslog`
  * `wc -l apache_access.log`
* Run broad greps to find likely suspicious terms:

  * `grep -n -E "Failed password|Invalid user|Accepted password|sudo|cron|wget|curl|payload|base64" *`
* Write down any obvious suspicious IPs, usernames, or timestamps in a scratch file.

---

### 🧩 Stage 2 — Authentication Review

**Goal:** Confirm if a brute-force occurred and whether any login succeeded.

**Tasks:**

* Count and summarize failed attempts by IP and username:

  * `grep "Failed password" auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -nr`
  * `grep "Failed password" auth.log | awk '{print $11}' | sort | uniq -c | sort -nr` (adjust field if needed)
* Find successful logins and correlate to IPs/users:

  * `grep "Accepted password" auth.log`
  * Note timestamps and user accounts involved (e.g., `svc_backup`).
* Check for PAM/session messages that show opened sessions:

  * `grep "session opened" auth.log`

---

### 🧩 Stage 3 — System Activity & Persistence Indicators

**Goal:** Look for evidence that an attacker executed commands or created persistence.

**Tasks:**

* Search `syslog` for `wget`, `curl`, `sh`, or `crontab` related entries:

  * `grep -n -E "wget|curl|/tmp|payload|cron|crontab" syslog`
* Identify any `su`/`sudo` or session entries tied to the suspicious user.

  * `grep -n "svc_backup" syslog`
* Note any cron entries created by non-standard users or unusual intervals.

---

### 🧩 Stage 4 — Web Server Correlation

**Goal:** See whether web requests match the timeline or show exploitation attempts.

**Tasks:**

* Search for suspicious patterns in `apache_access.log`:

  * `grep -n -E "\(\.|UNION|/etc/passwd|payload|cmd=|phpmyadmin|wp-login.php|shell" apache_access.log`
* Note requests that return `200` for suspicious paths (e.g., `/payload.sh`) and their timestamps.
* Correlate those timestamps with `Accepted password` or `wget` events from `syslog`/`auth.log`.

---

### 🧩 Stage 5 — Triage Report & Escalation

**Goal:** Prepare a short, clear escalation package for SOC Level 2.

**Tasks:**

* Create `triage_report.txt` with the following sections:

  1. **Summary (1–2 lines):** what you found and severity (e.g., "Confirmed intrusion: attacker gained shell via SSH on svc_backup").
  2. **Timeline (3–6 bullets):** timestamps, event type (failed login, accepted login, payload download, cron created), IP addresses, and users.
  3. **Key log excerpts (paste up to 10 lines total):** include `auth.log` and `syslog` lines showing the accepted login and the cron/payload activity.
  4. **Recommended immediate actions:** short items (isolate host, preserve logs, reset credentials, escalate to IR/L2).
* Save the file as `triage_report.txt` in the `~/lab1/` folder.

---

### ✅ After completing the lab

After you finish, compare your findings with **`answers.md`** in this folder to validate your work.

---

### 📂 Deliverables (what to submit)

* `triage_report.txt` (required)
* optional: any command outputs or notes you used during the investigation

---
