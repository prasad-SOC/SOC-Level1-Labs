# 🧠 Lab 2 — Small Escalation & Persistence (Linux)

---

### 🎯 Objective (detailed)

You are a SOC Level 1 analyst investigating an alert about unusual `sudo` activity and a newly created cron entry on `webserver-02`. In this lab you will:

* Review authentication and system logs to identify suspicious `sudo`/`su` behavior,
* Detect obfuscated or one-line payload execution (base64, `| sh`, `curl|sh`),
* Confirm simple persistence via cron entries,
* Prepare a concise `triage_report.txt` suitable for escalation to SOC Level 2.

This lab focuses on detection and evidence collection only — do **not** perform remediation or execute any commands from the files.

---

### ⚙️ Setup (exact & safe)

1. From the repo, download the following files from the **logs** section into your local machine: `auth.log`, `syslog`, `bash_history`, `crontab_snapshot`.
2. Open your Linux VM and create a dedicated working directory, then move the downloaded files into it. Example:

```bash
mkdir -p ~/lab2/logs
# Use your VM's file browser or the terminal to move the downloaded files into ~/lab2/logs
mv ~/Downloads/auth.log ~/lab2/logs/
mv ~/Downloads/syslog ~/lab2/logs/
mv ~/Downloads/bash_history ~/lab2/logs/
mv ~/Downloads/crontab_snapshot ~/lab2/logs/
cd ~/lab2/logs
ls -l
# you should see: auth.log  syslog  bash_history  crontab_snapshot
```

**Important safety notes (read before you continue):**

* These files are for **reading only**. **Do not execute** any commands you find inside `bash_history` or `crontab_snapshot`.
* If you need to inspect what a base64 string decodes to, **decode to a file** (do not pipe to `sh`):

  ```bash
  echo "<base64>" | base64 -d > /tmp/decoded_payload.txt
  less /tmp/decoded_payload.txt
  ```
* Treat `crontab_snapshot` as evidence. **Do not install** it on a real system unless you are in an isolated, disposable lab VM.

---

### 🧩 Stage 1 — Quick Recon

**Goal:** Understand the dataset and find obvious anomalies.

**Tasks:**

* Check file sizes and sample content:

  * `wc -l *`
  * `head -n 20 bash_history`
  * `tail -n 30 syslog`
* Run a broad search for likely indicators:

  * `grep -n -E "sudo|su|cron|base64|curl\||wget|/tmp|persist|crontab" *`
* Note suspicious users, timestamps, or commands into a scratch file (for later inclusion in `triage_report.txt`).

---

### 🧩 Stage 2 — Sudo & Privilege Review

**Goal:** Find who used sudo/su and whether it led to root actions.

**Tasks:**

* Search for sudo/su and session messages:

  * `grep -n "sudo" syslog || grep -n "sudo" auth.log`
  * `grep -n "su:" syslog`
  * `grep -n "session opened" auth.log`
* Identify the user, the command run with `sudo`, and the timestamp.
* Note if a `session opened for user root` appears after a suspicious sudo usage.

---

### 🧩 Stage 3 — Command Obfuscation & One-liners

**Goal:** Detect obfuscated commands and one-liners that execute payloads.

**Tasks:**

* Inspect `bash_history` safely:

  * `less bash_history`
  * `grep -n -E "base64|eval|python -c|curl -s|\| sh|wget .* -O /tmp" bash_history`
* Copy suspicious lines into a safe file (do not run):

  * `grep -n -E "base64|curl -s|wget .* -O /tmp|\| sh|python -c" bash_history > suspicious_cmds.txt`
* If needed, decode base64 safely to a file to inspect contents (do not execute):

  ```bash
  echo "aGVsbG8gd29ybGQ=" | base64 -d > /tmp/decoded_payload.txt
  less /tmp/decoded_payload.txt
  ```

---

### 🧩 Stage 4 — Persistence via Cron

**Goal:** Confirm whether a cron job was added for persistence and who owns it.

**Tasks:**

* View the crontab snapshot:

  * `less crontab_snapshot`
  * `grep -n -E "persist|/tmp|curl|wget|sh" crontab_snapshot`
* Note any cron lines created by non-standard users (e.g., `*/10 * * * * ubuntu /bin/sh /tmp/persist.sh`).
* Correlate cron timestamps with entries in `syslog` indicating the cron ran or `/tmp/persist.sh` execution.

---

### 🧩 Stage 5 — Triage Report & Escalation

**Goal:** Build a short escalation package for SOC Level 2.

**Tasks:**

* Create `triage_report.txt` in `~/lab2/` with:

  1. **Summary (1–2 lines):** what you found and severity.
  2. **Timeline (3–6 bullets):** timestamps and short notes (e.g., sudo use, base64 execution, cron creation).
  3. **Key excerpts (up to 8 lines):** copy relevant lines from `syslog` and `bash_history` showing sudo, the one-liner, and the cron entry.
  4. **Recommended immediate actions:** disable affected account, preserve logs, block IPs, escalate to L2.
* Save `triage_report.txt` in `~/lab2/` and keep it concise.

---

### ✅ After completing the lab

After you finish, compare your findings with **`answers.md`** in this folder to validate your work.

---
