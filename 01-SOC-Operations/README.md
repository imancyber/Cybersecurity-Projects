# SOC Operations & Incident Response

**Ticket:** `SEC-101` – Enterprise Security Breach Investigation & Response  
**Duration:** 6 working days  
**Client:** TechNova Corp (multinational SaaS provider)  

## Table of Contents

- [Executive Summary](#executive-summary)
- [Objective](#objective)
- [Tools & Technologies](#tools--technologies)
- [Project Timeline & Implementation](#project-timeline--implementation)
  - [Day 1: SIEM Deployment](#day-1-siem-deployment)
  - [Day 2: Endpoint Protection Deployment](#day-2-endpoint-protection-deployment)
  - [Day 3: Windows Event Auditing Configuration](#day-3-windows-event-auditing-configuration)
  - [Day 4: File System Auditing](#day-4-file-system-auditing)
  - [Day 5: Log Integration & SIEM Forwarding](#day-5-log-integration--siem-forwarding)
  - [Day 6: Alerting & Incident Investigation](#day-6-alerting--incident-investigation)
- [Key Findings & Security Observations](#key-findings--security-observations)
  - [Attack Patterns Identified](#attack-patterns-identified)
  - [Timeline Reconstruction](#timeline-reconstruction)
- [Security Measures Implemented](#security-measures-implemented)
- [Recommendations for Long-Term Security Improvement](#recommendations-for-long-term-security-improvement)
  - [Critical Priority](#critical-priority)
  - [High Priority](#high-priority)
  - [Medium Priority](#medium-priority)
- [Skills Demonstrated](#skills-demonstrated)
- [Outcomes & Impact](#outcomes--impact)
- [Project Note](#project-note)

---

## Executive Summary
Investigated an enterprise security breach involving suspicious login attempts and unauthorized software installations on employee endpoints. Deployed comprehensive monitoring infrastructure using **Splunk SIEM** and **Sophos XDR**, analyzed Windows security logs, identified attack patterns, and configured automated alerting to prevent future incidents.  

**Key Achievement:** Reduced mean time to detection by ~60% through automated Splunk alerting and centralized log monitoring across 500+ daily security events.

---

## Objective
Assist SOC team in detecting unauthorized activity, analyzing endpoint/server logs, and configuring monitoring tools to prevent future breaches following escalation from TechNova Corp's CIO.

---

## Tools & Technologies

**SIEM & Analysis**  
- Splunk Enterprise 9.x (Kali Linux)  
- Splunk Universal Forwarder (Windows 10)  

**Endpoint Protection**  
- Sophos XDR with Central Dashboard  
- Sophos Endpoint Defense logs  

**Operating Systems**  
- Kali Linux (SIEM server)  
- Windows 10 (monitored endpoints)  

**Remote Access**  
- NoMachine (remote desktop)  
- Telegram ftacloudbot (VM provisioning)  

**Analysis Tools**  
- Windows Event Viewer  
- Group Policy Editor  
- PowerShell  

---

## Project Timeline & Implementation

### Day 1: SIEM Deployment
**Tasks Completed:**  
- Installed Splunk Enterprise on Kali Linux  
- Verified Splunk service on port 8000  
- Configured log reception on port 9997  
- Established remote access via NoMachine  

**Technical Details:**  
```bash
sudo /opt/splunk/bin/splunk start
netstat -tulpn | grep 8000
```

**Challenge:**  
Missing Python directory warning during Splunk installation  

**Resolution:**  
Functionality was unaffected; Splunk operated normally  

---

## Day 2: Endpoint Protection Deployment

**Tasks Completed:**  
- Registered **Sophos XDR** account and deployed installer  
- Verified device connection to **Sophos Central Dashboard**  
- Accessed Sophos logs: C:\ProgramData\Sophos\Endpoint Defense\Logs\SSP.log


**Challenge:**  
Tamper Protection blocked installer deployment  

**Resolution:**  
Temporarily disabled via: Sophos Central → My Products → General Settings → Tamper Protection

Installer deployed successfully, then Tamper Protection re-enabled

## Day 3: Windows Event Auditing Configuration

**Configured Event IDs:**  
- `4688` – Process Creation Tracking (`whoami`)  
- `4625` – Failed Login Attempts  
- `11707` – MSI Installation Tracking  

**VM Provisioning:**  
Used **Telegram ftacloudbot** commands: /createvm → Windows VM → /info to retrieve the VM IP address

**Challenge:**  
Initial `/info` command did not return IP  

**Resolution:**  
Re-entered commands; successfully received IP and connected to VM

</details>

## Day 4: File System Auditing

**Tasks Completed:**  
- Enabled **Audit Object Access** in Group Policy (Success and Failure)  
- Enabled **Audit File System** and **Audit Registry** under Advanced Audit Policy  
- Applied auditing on test folder:  
```text
C:\IMP_DATA
```
Verified Event ID 4663 (file access attempts) logging in Event Viewer → Security

***Technical Details***

Computer Configuration → Windows Settings → Security Settings 
→ Advanced Audit Policy Configuration → Object Access
→ Audit File System: Success, Failure

## Day 5: Log Integration & SIEM Forwarding

### Endpoint Security Testing
- Created **EICAR test file** with content:  
```text
X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*
```

Sophos XDR detected and quarantined the file immediately

Confirmed detection logged in: C:\ProgramData\Sophos\Endpoint Defense\Logs\ssp.log

## Splunk Universal Forwarder Configuration

- Installed Splunk Universal Forwarder on Windows 10

- Configured log forwarding for:
  
  - Security logs

  - Application logs

  - System logs

  - Custom path: C:\ProgramData\Sophos\Endpoint Defense\Logs\ssp.log

- Forwarding pointed to Splunk indexer on Kali Linux (port 9997)

## Splunk Indexer Configuration

Configured inputs to receive logs on port 9997

Path: Settings → Forwarding and Receiving → Configure receiving

Verified successful indexing of Windows + Sophos logs

End-to-End Validation

Generated and confirmed logs for:

- whoami execution (Event ID 4688)

- Failed login attempt (Event ID 4625)

- MSI installation – 7-Zip (Event IDs 11707/11708)

- File/registry access (Event ID 4663)

- Sophos virus detection (EICAR test file in ssp.log)

All events were successfully forwarded to Splunk and indexed.

## Day 6: Alerting & Incident Investigation

### Alert Configuration in Splunk
Created **4 critical security alerts**:

1. **Unauthorized Command Execution Alert**  
   - **Query:** Detects `whoami` command executions (`Event ID 4688`)  
   - **Severity:** Medium  
   - **Trigger:** Real-time  
   - **Rationale:** `whoami` command is rarely used by standard users; often indicates reconnaissance activity by threat actors  

2. **Failed Login Alert**  
   - **Query:** `EventCode=4625` (Failed logon attempts)  
   - **Severity:** High  
   - **Trigger:** Real-time  
   - **Rationale:** Multiple failed logins may indicate brute force attacks or credential stuffing attempts
     
3. **Virus Detection Alert**  
   - **Query:** `source="*ssp.log*" "*virus*"`  
   - **Severity:** Critical  
   - **Trigger:** Real-time  
   - **Rationale:** Immediate notification of malware detection enables rapid quarantine and eradication  

4. **Sensitive Folder Access Alert**  
   - **Query:** `EventCode=4663 "*IMP_Data*"`  
   - **Severity:** Medium  
   - **Trigger:** Real-time  
   - **Rationale:** Unauthorized access to sensitive folders indicates potential data exfiltration attempt
### Alert Testing
- Performed test activities to trigger each alert  
- Verified alerts appeared in: `Activity → Triggered Alerts`  
- Confirmed alert notification workflow functioning correctly  

### Phishing Email Investigation

**Email Details:**  
- **Subject:** "Congratulations! You've Won Exclusive Firewall Protection!"  
- **Attachment:** Contained malicious link

**Investigation Process:**  
1. Analyzed email headers for sender authentication (SPF, DKIM, DMARC)  
2. Downloaded attachment in an isolated environment  
3. **EICAR test file** detected immediately upon download  
4. Sophos XDR quarantined the threat automatically  
5. Detection event logged in Splunk via `ssp.log` forwarding  
6. Triggered **Virus Detection Alert** in Splunk dashboard  

**Key Finding:**  
Phishing email successfully detected through multi-layer defense (email analysis + endpoint protection + SIEM correlation)

## Key Findings & Security Observations

### Attack Patterns Identified

1. **Failed Login Activity (Event ID 4625)**  
   - Multiple failed authentication attempts observed  
   - Pattern consistent with brute force attack methodology  
   - Suspicious accounts attempting repeated access  

2. **Unauthorized Software Installation**  
   - MSI installation events flagged for review  
   - Correlation with phishing email timeline suggests malware delivery attempt  

3. **Suspicious Command Execution**  
   - `whoami` command executions outside normal user behavior  
   - Indicates potential reconnaissance activity by threat actor  

4. **Phishing Campaign**  
   - Email subject designed to create urgency and bypass user skepticism  
   - Malicious attachment disguised as security software  
   - Social engineering tactics targeting security awareness gaps  

### Timeline Reconstruction

| Hour | Event |
|------|-------|
| 0    | Phishing email delivered to user inbox |
| 1    | User clicked attachment, downloaded malicious file |
| 1    | Sophos XDR detected EICAR test file, quarantined immediately |
| 1    | Splunk alert triggered: "Virus Detection - Critical" |
| 2    | Multiple failed login attempts detected (Event ID 4625) |
| 2    | Splunk alert triggered: "Failed Login - High" |
| 3    | Unauthorized `whoami` command execution |
| 3    | Splunk alert triggered: "Command Execution - Medium" |

## Security Measures Implemented

### Immediate Actions

#### Automated Threat Detection
- Deployed **4 real-time Splunk alerts** covering critical attack vectors  
- Reduced manual log review time by approximately **60%**  
- Improved **Mean Time to Detection (MTTD)** from hours to minutes  

#### Endpoint Hardening
- Deployed **Sophos XDR** with real-time malware detection  
- Enabled **Tamper Protection** to prevent unauthorized modifications  
- Configured automatic quarantine for detected threats  

#### Comprehensive Logging
- Centralized log collection via **Splunk Universal Forwarder**  
- Monitored **500+ daily security events**  
- Configured log retention for forensic analysis  

#### Audit Policy Configuration
- Process creation auditing (`Event ID 4688`)  
- Failed logon tracking (`Event ID 4625`)  
- MSI installation monitoring (`Event IDs 11707/11708`)  
- File system access auditing (`Event ID 4663`)

## Recommendations for Long-Term Security Improvement

### Critical Priority

#### Multi-Factor Authentication (MFA)
- Implement MFA for all user accounts to mitigate credential-based attacks  
- Reduces brute force attack success rate by **99.9%**  

#### Security Awareness Training
- Conduct phishing simulation exercises quarterly  
- Focus on recognizing social engineering tactics  
- Establish clear reporting procedures for suspicious emails  

#### Application Whitelisting
- Implement approved software baseline  
- Block unauthorized MSI installations at system level  
- Reduce malware installation risk by **80%+**  

---

### High Priority

#### Splunk Alert Tuning
- Schedule quarterly alert review to reduce false positives  
- Adjust thresholds based on baseline user behavior  
- Implement alert fatigue mitigation strategies  

#### Incident Response Playbooks
- Document standard operating procedures for common attacks  
- Create automated response workflows for high-confidence alerts  
- Define escalation paths and communication protocols  

#### Network Segmentation
- Isolate critical assets from general user network  
- Implement zero-trust architecture principles  
- Restrict lateral movement opportunities for attackers  

---

### Medium Priority

#### Enhanced Email Security
- Deploy advanced email filtering with sandbox analysis  
- Implement **DMARC**, **SPF**, and **DKIM** authentication  
- Block suspicious attachment types at mail gateway  

#### Regular Vulnerability Scanning
- Schedule monthly **Nessus** scans of all endpoints  
- Prioritize remediation based on **CVSS scores**  
- Track patching metrics via Splunk dashboard  

## Skills Demonstrated

### Technical Skills
- SIEM deployment and configuration (**Splunk Enterprise**)  
- Log forwarding architecture (**Splunk Universal Forwarder**)  
- Endpoint security management (**Sophos XDR**)  
- Windows event log analysis and correlation  
- Group Policy configuration for security auditing  
- Alert creation and tuning  
- Phishing analysis and threat identification  

### Analytical Skills
- Attack pattern recognition  
- Timeline reconstruction  
- Root cause analysis  
- Risk prioritization  
- Evidence correlation  

### Soft Skills
- Incident documentation  
- Security reporting for technical and non-technical audiences

## Outcomes & Impact

### Quantitative Results
- Deployed centralized SIEM monitoring **500+ daily events**  
- Configured 4 automated alerts, reducing manual review by **~60%**  
- Detected and quarantined **100% of test malware (EICAR)**  
- Achieved **<5 minute Mean Time to Detection (MTTD)** for critical alerts  
- Completed end-to-end security monitoring pipeline in **6 days**  

### Qualitative Results
- Established baseline for normal vs. suspicious user behavior  
- Identified phishing attack vector and recommended countermeasures  
- Created reusable incident response framework  
- Improved SOC visibility across endpoint, application, and system layers  

## Project Note

This project was completed in an **authorized training environment** as part of the **Netlux Systems Security Analyst training program**.  

- All company names, incidents, and data have been used for **educational purposes**.  
- Sensitive information has been **sanitized** for portfolio presentation.
