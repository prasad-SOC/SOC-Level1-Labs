This lab is designed to help you practice Nmap from a SOC Level-1 perspective. You will work with four Nmap scan output files and answer investigation-based questions. Each task includes the steps you should follow to find the answer, but **the actual answers will be placed in `answers.md`**.

## 📁 Files Provided

* `nmap_host_discovery.txt`
* `nmap_basic_scan.txt`
* `nmap_service_version.txt`
* `nmap_os_detection.txt`

All tasks below are based only on these four files.

---

## 📝 **Task 1: Host Discovery**

**File:** `nmap_host_discovery.txt`

### **Question:**

1. How many hosts are active in the network?
2. List the IP addresses of all active hosts.

### **Steps to Answer:**

* Open the host discovery file.
* Count all lines that say **"Host is up"**.
* Note the IP address from each "Nmap scan report" line.

---

## 📝 **Task 2: Basic Port Scan**

**File:** `nmap_basic_scan.txt`

### **Question:**

1. What ports are open on the target host?
2. Which port seems unusual or suspicious for a normal workstation?

### **Steps to Answer:**

* Open the file and look for lines under **PORT STATE SERVICE**.
* List all ports marked as **open**.
* Identify if any port is not commonly used on a regular system.

---

## 📝 **Task 3: Service & Version Detection**

**File:** `nmap_service_version.txt`

### **Questions:**

1. What service versions are running on each open port?
2. Is any service version outdated or potentially vulnerable?

### **Steps to Answer:**

* Look at entries under **VERSION**.
* Check versions of services like SSH, HTTP, SMB, and MySQL.
* Identify versions that appear old based on typical update cycles.

---

## 📝 **Task 4: SMB Investigation**

**File:** `nmap_service_version.txt`

### **Questions:**

1. Which SMB version is running?
2. Does it look like a legacy or modern implementation?

### **Steps to Answer:**

* Find ports **139** and **445**.
* Review the **Samba** version string.
* Compare it to typical SMB versions you know.

---

## 📝 **Task 5: OS Detection**

**File:** `nmap_os_detection.txt`

### **Questions:**

1. What operating system is detected on the target?
2. What OS kernel range is Nmap suggesting?
3. What is the network distance (hop count)?

### **Steps to Answer:**

* Look for the **Running:** field.
* Read the OS details and kernel range.
* Look for **Network Distance**.

---

## 📝 **Task 6: Identify the Host Type**

**Files:** `nmap_basic_scan.txt`, `nmap_service_version.txt`, `nmap_os_detection.txt`

### **Question:**

1. Based on open ports and operating system, what kind of machine is this (server, VM, workstation)?

### **Steps to Answer:**

* Review open ports for typical server services.
* Check OS details.
* Look for clues like VMware MAC address.

---

## 📝 **Task 7: Detect Potential Security Risks**

**File:** `nmap_service_version.txt`

### **Questions:**

1. Which service poses the largest security risk?
2. Why does this service seem risky?

### **Steps to Answer:**

* Look for old or outdated versions.
* Check services that are commonly exploited (SMB, MySQL, HTTP).

---

## 📝 **Task 8: Check for Misconfigurations**

**Files:** `nmap_basic_scan.txt`, `nmap_service_version.txt`

### **Question:**

1. Is there any service that should not be exposed on a workstation?

### **Steps to Answer:**

* Review typical workstation ports (SSH, HTTP may be unusual).
* Compare list of open services to what a normal user machine should have.

---

## 📝 **Task 9: Network Role Identification**

**Files:** All four

### **Question:**

1. Based on all findings, what role is this machine likely serving?

### **Steps to Answer:**

* Combine OS type, service versions, and open ports.
* Determine if it matches a developer machine, server, or test VM.

---

## 📝 **Task 10: SOC Analyst Summary**

**Files:** All four

### **Question:**

1. Provide a short SOC-style summary of your findings.

### **Steps to Answer:**

* Summarize open ports.
* Mention OS.
* Highlight any risky services.
* Provide a one-line recommendation.

---

Complete all questions using the four scan files. Once done, compare with `answers.md`.

Good luck and keep learning!
