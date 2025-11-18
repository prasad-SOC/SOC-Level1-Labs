**Use only built-in Linux commands**

---

## **📌 Scenario**

You are a SOC Level-1 analyst reviewing a new Linux machine.
Your job is to **audit** the system using Linux commands and perform **basic hardening checks**.

---

# **🧪 STAGE 1 — User & Group Audit**

### **🔹 Task 1: List all users**

**Command:**

```bash
cat /etc/passwd
```

---

### **🔹 Task 2: List users who have a valid shell**

**Command:**

```bash
grep -E "bash|sh" /etc/passwd
```

---

### **🔹 Task 3: Find which users have sudo privileges**

**Command:**

```bash
getent group sudo
```

---

### **🔹 Task 4: List locked or disabled accounts**

**Command:**

```bash
passwd -S --all
```

---

# **🧪 STAGE 2 — SSH Security Review**

### **🔹 Task 5: Check if root login is allowed**

**Command:**

```bash
grep -i "PermitRootLogin" /etc/ssh/sshd_config
```

---

### **🔹 Task 6: Check if password authentication is enabled**

**Command:**

```bash
grep -i "PasswordAuthentication" /etc/ssh/sshd_config
```

---

### **🔹 Task 7: Find SSH listening ports**

**Command:**

```bash
ss -tulpn | grep ssh
```

---

# **🧪 STAGE 3 — Cron Jobs & Scheduled Tasks**

### **🔹 Task 8: List system-wide scheduled cron jobs**

**Command:**

```bash
ls -l /etc/cron.*
```

---

### **🔹 Task 9: List root's cron jobs**

**Command:**

```bash
sudo crontab -l
```

---

# **🧪 STAGE 4 — File Permissions Audit**

### **🔹 Task 10: Check permissions of passwd & shadow**

**Command:**

```bash
ls -l /etc/passwd /etc/shadow
```

---

### **🔹 Task 11: Find all world-writable files**

**Command:**

```bash
sudo find / -type f -perm -0002 2>/dev/null
```

---

### **🔹 Task 12: Check home directory permissions**

**Command:**

```bash
ls -ld /home/*
```

---

# **🧪 STAGE 5 — Service & Process Review**

### **🔹 Task 13: List all active services**

**Command:**

```bash
systemctl --type=service --state=running
```

---

### **🔹 Task 14: List all running processes**

**Command:**

```bash
ps aux
```

---

### **🔹 Task 15: Find all open ports**

**Command:**

```bash
ss -tulpn
```

---

# **🧪 STAGE 6 — Password & Authentication Policies**

### **🔹 Task 16: Check minimum password length**

**Command:**

```bash
grep -i PASS_MIN_LEN /etc/login.defs
```

---

### **🔹 Task 17: Check password aging for a user**

**Command:**

```bash
sudo chage -l username
```

---

# **🧪 STAGE 7 — Firewall & Network Hardening**

### **🔹 Task 18: Check UFW status**

**Command:**

```bash
sudo ufw status
```

---

### **🔹 Task 19: List iptables rules**

**Command:**

```bash
sudo iptables -L -n -v
```

---

# **🧪 STAGE 8 — Startup Script Audit**

### **🔹 Task 20: List systemd startup services**

**Command:**

```bash
systemctl list-unit-files --type=service
```

---

### **🔹 Task 21: Check if rc.local exists and is executable**

**Command:**

```bash
ls -l /etc/rc.local
```

---
