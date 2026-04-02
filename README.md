# Case Study: Forensic Analysis & Remediation of Web-Based Attack Vectors

## 🛡️ Project Overview
This project is a deep-dive forensic analysis of a compromised Linux-based infrastructure (`10.6.6.0/24` and `172.17.0.0/24`). Originally simulated as an offensive security exercise, this report reconstructs the attack lifecycle from a **Blue Team perspective** to identify systemic vulnerabilities, assess business risk, and provide enterprise-grade remediation strategies. 

By analyzing the specific attack paths that led to credential exposure and unauthorized access, this case study bridges the gap between vulnerability identification and practical defense.

---

## 🏗️ Investigative Environment
* **Target Infrastructure:** Linux / Apache / Samba Environment
* **Investigative Platform:** Preconfigured Kali Linux VM
* **Network Scope:** `10.6.6.0/24` and `172.17.0.0/24`
* **Tools Utilized:** Wireshark (Network Forensics), Nmap (Service Auditing), Nikto (Web Vulnerability Scanning), John the Ripper (Credential Recovery)

---

## 🔍 Key Findings & Security Posture Assessment

### **1. Critical: Remote Credential Exposure via SQL Injection (P1)**
* **Vulnerability:** Insecure Input Handling in the Web Application.
* **Attack Path:** The application failed to sanitize user inputs, allowing for out-of-band data extraction. This enabled the recovery of administrative password hashes from the backend database, which were cracked offline and used to establish an unauthorized SSH session.
* **Business Risk:** Complete takeover of the web server and potential lateral movement via cracked credentials.
* **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application), T1078 (Valid Accounts).
* **Remediation:** Implementation of Prepared Statements (Parameterized Queries), strict server-side input validation, and migration to a salted hashing algorithm (e.g., Argon2).

### **2. High: Sensitive Data Leakage via Directory Indexing (P2)**
* **Vulnerability:** Improper Web Server Configuration (Apache).
* **Attack Path:** Automated reconnaissance identified that `Options +Indexes` was enabled on the root directory, exposing internal configuration files and documentation to unauthenticated users via direct browser navigation.
* **Business Risk:** Provides attackers with immediate environmental visibility (file names, folder structures) with zero exploit effort.
* **MITRE ATT&CK:** T1595 (Active Scanning), T1083 (File and Directory Discovery).
* **Remediation:** Disable directory browsing globally in `httpd.conf` or `.htaccess` and move sensitive files outside the web root.

### **3. High: Unauthorized Access to Internal File Shares (P2)**
* **Vulnerability:** Anonymous Access Enabled on SMB Shares.
* **Attack Path:** The Samba service was misconfigured to allow `guest` access, permitting unauthenticated users to enumerate shares and retrieve sensitive internal documentation without credentials.
* **MITRE ATT&CK:** T1135 (Network Share Discovery), T1039 (Data from Network Shared Drive).
* **Remediation:** Enforce SMB signing, disable guest/anonymous access, and restrict share access to specific authenticated groups via least-privilege principles.

### **4. Medium: Network Traffic Reconstruction & Data Exposure (P3)**
* **Vulnerability:** Cleartext HTTP Traffic exposing operational details.
* **Analysis:** Forensic analysis of network traffic logs (PCAP) identified anomalous HTTP GET requests targeting non-public URI paths (`/data`). This unencrypted traffic pattern provided the initial intelligence required to target specific internal resources.
* **MITRE ATT&CK:** T1040 (Network Sniffing), T1592 (Gather Victim Host Information).
* **Remediation:** Deprecate cleartext HTTP in favor of TLS encryption for all internal web services.

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

```
### **📝 Post-Mortem Methodology Note**

During the final phase of the investigation, forensic reconstruction of the traffic flow was conducted to verify target host identification. In instances where live resources were decommissioned post-incident, data was validated against historical packet capture signatures to ensure a 100% accurate reconstruction of the attack path.

**Disclaimer**: *This analysis was conducted in an authorized, isolated educational lab environment for forensic training purposes. No real-world systems were targeted or compromised during this case study.*

