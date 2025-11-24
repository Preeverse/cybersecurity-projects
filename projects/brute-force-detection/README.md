# 🛡️ Brute Force Attack Detection & SOC Investigation  
### Splunk SIEM | Active Directory | Hydra | SOC Workflow

---

## 1. Overview

This lab simulates a brute-force authentication attack against an Active Directory Domain Controller, log ingestion into Splunk, alerting, and a complete SOC analyst investigation.

This use case demonstrates the full **NIST 800-61 Incident Response lifecycle**, including Preparation, Detection & Analysis, Containment/Eradication/Recovery, and Lessons Learned and **MITRE ATT&CK T1110 (Brute Force)**.

### 2 Lab Components

| Hostname | Role | Description |
|----------|------|-------------|
| **DC01 (Windows Server)** | Active Directory Domain Controller + Splunk Forwarder | Sends Windows Security Logs (4625, 4624, 4740) to Splunk |
| **WIN11-SIEM (Windows 11)** | Splunk Enterprise Server | Hosts indexes, alerts, dashboards |
| **Kali Linux** | Attacker Machine | Runs Hydra brute-force attack against DC01 |


![Lab Topology](screenshots/Lab_topology.png)

---

## 3. Preparation

1. Steps to install Splunk Enterprise and Splunk Universal Forwarder are explained in [Splunk Enterprise and UF Installation](/projects/siem/splunk/splunk_installation/)

2. See [Splunk-Index-Alerts-Dashboard Creation](/projects/siem/splunk/index-alert-dashboard_config/) for indexing logs, alerts and dashboard configuration using SPL (Search Processing Language) queries.

3. Set the Account Lockout Policy in the Active Directory Domain Controller as a preventive measure for brute-force attacks. Refer [AD_Account Lockout Policy](/projects/active-directory/account-lockout-policy/) for detailed steps.

---

## 4. Attack Simulation Summary

The attacker (Kali Linux) performs a brute-force password attack targeting the `TestAccount` account on **DC01**. Refer [Hydra Brute Force Attack Simulation](/projects/attack-simulations/hydra-bruteforce-attack/)

### 🛣️ **Network Flow**

Kali Linux (Hydra)
↓
DC01 → Splunk Forwarder
↓
WIN11-SIEM (Splunk Enterprise)
↓
Correlation Rule → Scheduled Alert → Email Notification / Incident Ticket Creation 
↓
SOC Analyst Investigation

---

## 5. Identification - Alert Received

### SOC Analyst Workflow (Starts After Alert Received)
The SOC analyst receives **two alerts**:

The above brute-force attack generates rapid Windows Event ID **4625** (failed login attempts).  
DC01’s Splunk Universal Forwarder sends these logs to **WIN11-SIEM**, triggering a scheduled alert when a threshold is exceeded and followed by an account lockout event done by the Active Directory Domain policy configured.

Two alerts are generated:

1. **Alert 1 - Brute Force Attempt Detected**  (Multiple logs with Event ID 4625)
2. **Alert 2 - Account Lockout Event** (Event ID 4740)

---

## 6. Analysis

### Initial Triage

The analyst opens Splunk to validate the alert details.
Refer [Splunk Log analysis](/projects/siem/splunk/splunk-log-analysis/) covering detailed steps involved in log analysis.

### 🔎 **Key Fields Checked in Logs**

| Field | Purpose | Triage Findings |
|-------|---------|----------------|
| **Source IP Address** | Identify attacker system (Kali Linux) | **192.168.0.5** observed as the sole source → confirms single-origin brute-force attempt |
| **Account Name (TargetUserName)** | Determine the user under attack | Repeated failures for **TestAccount** → attacker targeting one specific account |
| **Machine Name / Workstation Name** | Confirm authentication endpoint | All failed login events originated from **DC01** → attempts hitting the Domain Controller directly |
| **Timestamp** | Analyze login frequency & pattern | ~200 attempts within 2 minutes → automated attack behavior consistent with Hydra |
| **Event Codes** | 4625 (fail), 4624 (success), 4740 (lockout) | Continuous **4625**, no **4624 success**, followed by **4740** → account lockout triggered successfully |
| **Geolocation (if external)** | Identify external threat origin | Internal private IP detected → confirms activity from controlled Kali attacker within lab network |

### Next Level Investigation

| Step | Details |
|------|---------|
| **1. Login Behavior Review** | The analyst checks:<br>• When the account was last successfully used<br>• Whether the user normally logs in from this source<br>• Any suspicious time-of-day activity<br><br>**Splunk Query:**<br>`index=winevent_security EventCode=4624 Account_Name="testuser" | table _time, IP_Address, Workstation_Name`<br><br>**Result:** No successful logins → brute-force unsuccessful. 🖥️ |
| **2. Lateral Movement Check** | Check for unusual authentications following the brute-force attempt.<br><br>**Splunk Query:**<br>`index=winevent_security EventCode=4624 | where _time >= relative_time(now(), "-30m")`<br><br>**Result:** No lateral movement detected. 🔒 |
| **3. Security Controls Verification** | The analyst validates preventive controls:<br>• **Account Lockout Policy:** Event 4740 confirms forced lockout → AD Setup<br>• **Password Policy:** Complexity, minimum length, and lockout thresholds verified<br>• **MFA Status:** Checked for privileged accounts → recommended to enable |
| **4. Attacker IP Threat Intelligence Check** | Quick analyst checks:<br>• WHOIS lookup<br>• Reputation lookup (VirusTotal, AbuseIPDB, Talos)<br>• Internal vs external determination<br><br>**Result:** Internal lab IP → known attacker system → Attack Simulation |


---

## 7. Containment

Account is already locked out as a containment step by the Active Directory.

---

## 8. Eradication and Recovery

- Reset **testuser** password → AD Setup
- Unlock account → AD Setup 
- Notify system owner 
- Validate no persistence mechanisms exist 
- Update SIEM thresholds → Splunk Config 
- Add IP block rule for prevention

---

## 9. Lessons Learned

###  Technical Lessons
- Effective log forwarding is essential  
- SIEM thresholds must reflect real attack behavior  
- Account lockout policies mitigate brute-force attacks  
- Event ID correlation strengthens detection accuracy  
- No successful logons confirmed attack containment  

###  Operational / SOC Process Lessons
- SOC analysts must validate alerts  
- Internal IPs can still represent threat sources  
- Runbooks streamline SOC workflows  

###  Configuration & Policy Lessons
- Strong password policies reduce brute-force success  
- MFA significantly enhances security  
- SIEM dashboards improve authentication visibility  

###  Strategic Lessons
- Automated attacks require behavioral detection  
- Defense must be multi-layered  
- Post-incident hardening improves overall resilience  

---

# Summary

This project demonstrates a full end-to-end brute-force attack simulation using Hydra against an Active Directory Domain Controller, with Windows Security Logs forwarded to Splunk for detection. The SIEM generates alerts for excessive failed logins (Event ID 4625) and an account lockout (Event ID 4740), initiating a SOC investigation workflow covering identification, triage, analysis, containment, eradication, and recovery within a controlled lab environment.

---

## 📄 SOC Incident Report

The full SOC Incident Response report for this lab exercise is available here:

[View the Brute Force Attack SOC Report](report.pdf)

---
