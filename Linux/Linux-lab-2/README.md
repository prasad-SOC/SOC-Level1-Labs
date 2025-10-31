# 🧠 Lab 2 — Small Escalation & Persistence 
---

### 🎯 Objective 

You are a SOC Level 1 analyst investigating an alert about unusual `sudo` activity and a new cron entry on `webserver-02`. In this lab you will:

* Review authentication and system logs to spot suspicious `sudo`/`su` behavior,
* Identify obfuscated or one-line payload execution (base64, `| sh`, `curl|sh`),
* Find and confirm simple persistence via cron,
* Prepare a short `triage_report.txt` to escalate to SOC Level 2.

This lab focuses on detection and evidence collection suitable for an L1 analyst. No deep forensics or system changes are required.

---

### ⚙️ Setup (short)

Download `auth.log`, `syslog`, `bash_history`, and `crontab_snapshot` from the **logs** section and move them to a dedicated working directory inside your Linux VM, for example:

```bash
mkdir -p ~/lab2/logs
# move the downloaded files into ~/lab2/logs
cd ~/lab2/logs
ls
# you should see: auth.log  syslog  bash_history  crontab_snapshot
```

---

### 🧩 Stage 1 — Quick Recon

**Goal:** Get a quick view of the files and find obvious suspicious entries.

**Tasks:**

* Check file sizes and sample content:

  * `wc -l *`
  * `head -n 20 bash_history`
  * `tail -n 30 syslog`
* Run a broad search for key terms:

  * `grep -n -E "sudo|su|cron|base64|curl\||wget|/tmp|persist|/tmp/persist.sh|crontab" *`
* Note any suspicious users, timestamps, or commands.

---

### 🧩 Stage 2 — Sudo & Privilege Review

**Goal:** Identify suspicious `sudo` or `su` activity.

**Tasks:**

* Search for `sudo`/`su` entries in `syslog` and `auth.log`:

  * `grep -n "sudo" syslog || grep -n "sudo" auth.log`
  * `grep -n "su:" syslog`
* Find which user ran `sudo`, what command was executed, and when.
* Note if `session opened for user root` appears after `sudo` or `su`.

---

### 🧩 Stage 3 — Command Obfuscation & One-liners

**Goal:** Detect obfuscated commands and one-line payload execution.

**Tasks:**

* Inspect `bash_history` for suspicious one-liners:

  * `grep -n -E "base64|eval|python -c|curl -s|\| sh|wget .* -O /tmp" bash_history`
* Copy the suspicious command lines into a scratch file and decode base64 strings if needed (do **not** execute them).
* Note the exact commands and which user ran them.

---

### 🧩 Stage 4 — Persistence via Cron

**Goal:** Confirm whether a cron job was created for persistence.

**Tasks:**

* Open `crontab_snapshot` and search for recent entries by non-standard users:

  * `cat crontab_snapshot` or `grep -n "persist" crontab_snapshot`
* Correlate timestamps with `syslog` entries showing `/tmp/persist.sh` or cron runs.
* Record the cron line and owner (e.g., `*/10 * * * * ubuntu /bin/sh /tmp/persist.sh`).

---

### 🧩 Stage 5 — Triage Report & Escalation

**Goal:** Prepare a short escalation package for SOC Level 2.

**Tasks:**

* Create `triage_report.txt` in `~/lab2/` with these sections:

  1. **Summary (1–2 lines):** what you found and severity.
  2. **Timeline (3–6 bullets):** timestamps and short notes (sudo use, base64 execution, cron creation).
  3. **Key excerpts (up to 8 lines):** include `syslog` and `bash_history` lines showing the sudo action, the base64/one-liner, and the cron entry.
  4. **Recommended immediate actions:** disable affected account, preserve logs, escalate to L2.
* Save the file as `triage_report.txt` in `~/lab2/`.

---

### ✅ After completing the lab

After you finish, compare your findings with **`answers.md`** in this folder to validate your work.

---
