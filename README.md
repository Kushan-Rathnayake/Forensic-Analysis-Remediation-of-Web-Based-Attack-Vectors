# Case Study: Forensic Analysis & Remediation of Web-Based Attack Vectors

## 🛡️ Project Overview
[cite_start]This project is a deep-dive forensic analysis of a compromised Linux-based infrastructure (`10.6.6.0/24` and `172.17.0.0/24`)[cite: 15]. [cite_start]Originally simulated as an offensive security exercise, this report reconstructs the attack lifecycle from a **Blue Team perspective** to identify systemic vulnerabilities, assess business risk, and provide enterprise-grade remediation strategies[cite: 4]. 

[cite_start]By analyzing the specific attack paths that led to credential exposure and unauthorized access, this case study bridges the gap between vulnerability identification and practical defense[cite: 5, 23].

---

## 🏗️ Investigative Environment
* **Target Infrastructure:** Linux / Apache / Samba Environment
* [cite_start]**Investigative Platform:** Preconfigured Kali Linux VM [cite: 13]
* [cite_start]**Network Scope:** `10.6.6.0/24` and `172.17.0.0/24` [cite: 15]
* **Tools Utilized:** Wireshark (Network Forensics), Nmap (Service Auditing), Nikto (Web Vulnerability Scanning), John the Ripper (Credential Recovery)

---

## 🔍 Key Findings & Security Posture Assessment

### **1. [cite_start]Critical: Remote Credential Exposure via SQL Injection (P1)** [cite: 47, 48]
* **Vulnerability:** Insecure Input Handling in the Web Application[cite: 141].
* **Attack Path:** The application failed to sanitize user inputs, allowing for out-of-band data extraction. [cite_start]This enabled the recovery of administrative password hashes from the backend database, which were cracked offline and used to establish an unauthorized SSH session[cite: 134, 136, 137, 138].
* **Business Risk:** Complete takeover of the web server and potential lateral movement via cracked credentials.
* [cite_start]**MITRE ATT&CK:** T1190 (Exploit Public-Facing Application), T1078 (Valid Accounts)[cite: 154].
* **Remediation:** Implementation of Prepared Statements (Parameterized Queries), strict server-side input validation, and migration to a salted hashing algorithm (e.g., Argon2)[cite: 183, 185].

### **2. [cite_start]High: Sensitive Data Leakage via Directory Indexing (P2)** [cite: 49, 51]
* **Vulnerability:** Improper Web Server Configuration (Apache)[cite: 235].
* [cite_start]**Attack Path:** Automated reconnaissance identified that `Options +Indexes` was enabled on the root directory, exposing internal configuration files and documentation to unauthenticated users via direct browser navigation[cite: 228, 229, 230].
* [cite_start]**Business Risk:** Provides attackers with immediate environmental visibility (file names, folder structures) with zero exploit effort[cite: 238, 239, 240].
* **MITRE ATT&CK:** T1595 (Active Scanning), T1083 (File and Directory Discovery)[cite: 247, 248].
* [cite_start]**Remediation:** Disable directory browsing globally in `httpd.conf` or `.htaccess` and move sensitive files outside the web root[cite: 278, 279].

### **3. [cite_start]High: Unauthorized Access to Internal File Shares (P2)** [cite: 52, 54]
* **Vulnerability:** Anonymous Access Enabled on SMB Shares[cite: 432].
* [cite_start]**Attack Path:** The Samba service was misconfigured to allow `guest` access, permitting unauthenticated users to enumerate shares and retrieve sensitive internal documentation without credentials[cite: 427, 428, 429].
* [cite_start]**MITRE ATT&CK:** T1135 (Network Share Discovery), T1039 (Data from Network Shared Drive)[cite: 443, 444].
* **Remediation:** Enforce SMB signing, disable guest/anonymous access, and restrict share access to specific authenticated groups via least-privilege principles[cite: 472, 473].

### **4. [cite_start]Medium: Network Traffic Reconstruction & Data Exposure (P3)** [cite: 55, 57]
* **Vulnerability:** Cleartext HTTP Traffic exposing operational details[cite: 709].
* [cite_start]**Analysis:** Forensic analysis of network traffic logs (PCAP) identified anomalous HTTP GET requests targeting non-public URI paths (`/data`)[cite: 488]. [cite_start]This unencrypted traffic pattern provided the initial intelligence required to target specific internal resources[cite: 490].
* **MITRE ATT&CK:** T1040 (Network Sniffing), T1592 (Gather Victim Host Information)[cite: 695, 696].
* [cite_start]**Remediation:** Deprecate cleartext HTTP in favor of TLS encryption for all internal web services[cite: 727].

---

## 🛠️ Detection Engineering & Prevention
To ensure these vulnerabilities are actively monitored, the following detection logic was developed. 

**Sigma Rule: Detect SQL Injection Patterns in Web Logs**
```yaml
title: Detect SQL Injection Patterns in Apache Logs
status: experimental
description: Detects common SQL injection patterns (UNION, SELECT, information_schema) in web server logs to prevent credential exposure.
logsource:
    category: webserver
detection:
    selection:
        cs-uri-query|contains:
            - "UNION SELECT"
            - "ORDER BY"
            - "information_schema"
            - "1=1"
    condition: selection
falsepositives:
    - Authorized Penetration Testing / DAST Scanning
level: critical
tags:
    - attack.t1190
