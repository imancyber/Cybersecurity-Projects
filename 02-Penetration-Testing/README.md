# Penetration Testing & Vulnerability Research

**Ticket:** `PT-301` – Penetration Testing & Vulnerability Research
**Duration:** 2 working days  
**Client:** Initech Financial Services

---

## Table of Contents
- [Executive Summary](#executive-summary)
- [Objective](#objective)
- [Scope of Engagement](#scope-of-engagement)
- [Methodology](#methodology)
- [Tools & Technologies](#tools--technologies)
- [Findings & Analysis](#findings--analysis)
  - [Critical Findings](#critical-findings)
  - [High & Medium Findings](#high--medium-findings)
- [Recommendations](#recommendations)
- [Skills Demonstrated](#skills-demonstrated)
- [Outcomes & Impact](#outcomes--impact)
- [Project Note](#project-note)

---

## Executive Summary
Conducted a **black-box penetration test** for **Initech Financial Services**, a mid-sized fintech company providing online payment processing solutions. The objective was to identify, exploit, and assess vulnerabilities within a legacy Linux server exposed to the internet.  

The engagement simulated a real-world attack from an external threat actor with no prior knowledge of internal systems.  

**Key Achievement:**  
Gained remote shell access via exploitation of a vulnerable FTP service (**vsftpd 2.3.4**) and successfully demonstrated post-exploitation techniques including password hash extraction and sensitive data access.  

**Risk Rating:** High  
- Vulnerabilities allowed **unauthorized remote access**, **data exposure**, and **privilege escalation**.  

---

## Objective
To evaluate the security posture of the client’s external infrastructure by identifying, exploiting, and documenting vulnerabilities in a controlled environment, and providing actionable remediation steps to strengthen defenses.

---

## Scope of Engagement

**Target System:**  
- Legacy Linux server (Metasploitable2 environment)  

**In-Scope Services:**  
- FTP (Port 21)  
- SSH (Port 22)  
- Web and application directories  

**Goals:**  
- Identify vulnerabilities and misconfigurations  
- Exploit at least one high-risk vulnerability  
- Demonstrate privilege escalation and data exposure risks  
- Provide remediation recommendations  

---

## Methodology

### Day 1: Reconnaissance & Exploitation
- Conducted **Nmap scan** to identify open ports and running services  
- Discovered vulnerable FTP service (**vsftpd 2.3.4**)  
- Researched associated CVE (**CVE-2011-2523**) – backdoor command execution vulnerability  
- Utilized **Metasploit Framework** to exploit vsftpd 2.3.4  
- Established remote shell access to target system  
- Verified access with commands:  
  ```bash
  whoami
  uname -a
  ```
- Gained initial access to the client’s server via 162.243.174.218

### Day 2: Post-Exploitation & Reporting

**Post-Exploitation Activities**
- Upgraded to an interactive shell:
```bash
/bin/bash -i
```

- Navigated to sensitive directory and reviewed contents:
  ```bash
  cd /home/InitechFinancial/
  cat Q4_Financial_Projections.txt
  ```
- Extracted password hashes from:
  ```bash
  /etc/shadow
  ```
- Used John the Ripper to crack the msfadmin user hash and verified cracked credentials, confirming weak password practices.

- Performed system cleanup:
  - Removed temporary test directories and artifacts created during the engagement.
  - Verified system state restored to original baseline.
  - Documented findings and remediation steps for client reporting.

## Tools & Technologies

### Scanning & Reconnaissance
- **Nmap**  
- **Searchsploit / Exploit-DB**

### Exploitation
- **Metasploit Framework** (Kali Linux)

### Post-Exploitation
- **John the Ripper**  
- **Linux utilities:** `cat`, `whoami`, `uname`, `pwd`

### Reporting
- **Penetration Testing Report Template** (Netlux Systems)

## Project Note

This project was completed in an **authorized training environment** as part of the **Netlux Systems Security Analyst training program**.  

- All company names, incidents, and data have been used for **educational purposes**.  
- Sensitive information has been **sanitized** for portfolio presentation.

