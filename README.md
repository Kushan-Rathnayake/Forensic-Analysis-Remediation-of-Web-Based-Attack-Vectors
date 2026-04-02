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
* **Attack Path:** The application failed to sanitize user inputs, allowing for out-of-band data extraction. This enabled the recovery of administrative
