# 📘 Wireshark SOC Level-1 Lab

**PCAP:** `soc_l1_lab.pcap`
**Difficulty:** Beginner / SOC Analyst – Level 1
**Objective:** Analyze basic network traffic and identify suspicious activity using Wireshark.

---

## 🔧 Setup

1. Download the file: **`soc_l1_lab.pcap`**
2. Open it in **Wireshark**
3. Use display filters as needed to answer the questions

---

# 📝 Questions & Answers

### **1. What is the attacker’s IP address?**

**Answer:** `192.168.1.5`

---

### **2. What is the target machine’s IP address?**

**Answer:** `192.168.1.10`

---

### **3. How many SYN packets were sent to the target?**

**Answer:** `4`

---

### **4. What ports were scanned?**

**Answer:** `22, 23, 80, 445`

---

### **5. What type of scan does this traffic indicate?**

**Answer:** `SYN Scan (Half-open scan)`

---

### **6. Is this scanning activity suspicious for a SOC L1 alert?**

**Answer:** `Yes`

---

### **7. What severity would you assign to this event?**

**Answer:** `Medium`

---

### **8. What is the likely purpose of this scan?**

**Answer:** `Reconnaissance / Service enumeration`

---

### **9. Is there any successful connection to any port?**

**Answer:** `No`

---

### **10. What is the recommendation for this alert?**

**Answer:** `Block or monitor the source IP and review firewall logs for additional activity.`

---
