# Blue Team Analysis of a Cisco Ethical Hacker Final Capstone

## Overview
This project is a beginner-level blue-team analysis based on a Cisco Ethical Hacker Final Capstone completed in a controlled lab environment.

Although the capstone was originally designed as an offensive security exercise, this project reframes the activity from a defender’s perspective. The goal was to analyze how each attack succeeded, identify the underlying security weakness, explain the risk from a blue-team viewpoint, and provide practical remediation recommendations.

The project focuses on four main challenge areas:

- SQL injection
- Web server directory exposure
- Open Samba shares
- Packet capture (PCAP) analysis

This project was created as a portfolio and learning artifact to demonstrate beginner-level defensive thinking, structured reporting, and the ability to convert offensive lab work into a blue-team security analysis.

---

## Lab Context
- **Environment:** Controlled educational lab
- **Platform:** Oracle VirtualBox
- **Analyst system:** Preconfigured Kali Linux VM
- **Networks in scope:** `10.6.6.0/24` and `172.17.0.0/24`
- **Activity type:** Authorized capstone simulation

---

## Project Objectives
The objectives of this project were to:

- identify the main weakness involved in each challenge
- explain how the attacker was able to succeed
- interpret the activity from a defender’s perspective
- summarize the main risks created by these weaknesses
- recommend practical remediation actions

---

## Tools Used
- Kali Linux
- Firefox
- Nmap
- Nikto
- John the Ripper
- smbclient
- Wireshark
- SSH

---

## Key Findings

### 1. SQL Injection Enabled Credential Exposure
A vulnerable web input field allowed SQL injection, which exposed account-related information including a username and password hash. The recovered hash was cracked and used to access the target system.

**Blue-team takeaway:** insecure input handling can lead directly to credential exposure and follow-on compromise.

### 2. Web Server Directory Listing Exposed Sensitive Files
Web reconnaissance identified indexed directories that were openly browsable in the browser. This allowed exposed files to be discovered through direct navigation.

**Blue-team takeaway:** weak web-server configuration can expose internal resources even without advanced exploitation.

### 3. Open Samba Share Allowed Unauthorized File Access
SMB enumeration revealed shares with anonymous access. The accessible share contents were browsed and a file containing the challenge flag was retrieved.

**Blue-team takeaway:** weak file-share permissions can expose internal data with minimal attacker effort.

### 4. PCAP Analysis Revealed Sensitive Information
Analysis of a packet capture identified a relevant target host and a sensitive web-accessible data path. This showed how passive traffic analysis can reveal actionable intelligence.

**Blue-team takeaway:** network traffic itself can leak valuable information about systems and sensitive resources.

---

## Skills Demonstrated
- Reconnaissance and enumeration analysis
- Web application security analysis
- Identification of insecure configuration
- Credential exposure analysis
- File-share exposure analysis
- Basic packet capture analysis
- Blue-team reporting and remediation writing
- Translation of offensive activity into defensive findings

---

## Project Structure
```
blue-team-analysis-cisco-ethical-hacker-capstone/
|-- README.md
|-- report/
|     |-- Blue-Team-Analysis-Report.pdf
|-- screenshots/
      |-- 01-vulnerable-sql-input-identified.png
      |-- 02-sqli-user-data-exposed.png
      |-- 03-gordon-brown-and-hash-exposed.png
      |-- 04-gordonb-password-cracked.png
      |-- 05-challenge1-ssh-access-and-flag.png
      |-- 06-nikto-directory-indexing-exposed.png
      |-- 07-directory-listing-visible-browser.png
      |-- 08-challenge2-flag-visible.png
      |-- 09-nmap-smb-services-identified-1.png
      |-- 10-nmap-smb-services-identified-2.png
      |-- 11-samba-share-enumeration-anonymous-access.png
      |-- 12-challenge3-samba-access-and-flag.png
      |-- 13-sa-pcap-opened-in-wireshark.png
      |-- 14-pcap-url-and-target-identified.png
      |-- 15-challenge4-answer-key-confirmation.png
```
## Notes
Challenge 4 documentation required confirmation from the lab answer material because the live web resource was unavailable during final documentation. This is stated transparently in the full report.

---

## Disclaimer
This project was completed in an authorized educational lab environment for training and portfolio purposes only. No unauthorized testing was performed against real-world systems or networks.

---
