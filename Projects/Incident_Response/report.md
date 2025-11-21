Windows Authentication Security & Brute Force Detection – Analyst Report

Prepared by: [Your Name]
Date: [Insert Date]
Environment:

DC01 – Domain Controller (Windows Server)

Win11 – Splunk Enterprise Server

Kali Linux – Attacker machine

Universal Forwarder installed on DC01

Index used in Splunk: windows

1. Objective

The aim of this exercise was to:

Simulate a brute-force attack against a Windows domain account using Hydra from a Kali Linux host.

Monitor Windows Event Logs (4625 – failed logons) in Splunk for detection.

Detect account lockout events (4740) triggered by the attack.

Demonstrate end-to-end SOC analyst workflow, including detection, analysis, and prevention.

2. Data Collection

Windows Event Logs were ingested from DC01 into Splunk using a Universal Forwarder:

Event ID 4625 – Failed logon attempts

Event ID 4740 – Account lockout events

Configuration:

Splunk Universal Forwarder installed on DC01.

Inputs configured via inputs.conf to collect Security logs.

Index: windows

Splunk Enterprise running on Win11, receiving forwarded logs on port 9997.

All logs were verified to be flowing correctly before the attack.

Screenshots Captured:

Indexes list confirming windows index exists

Initial logs flowing into Splunk

3. Attack Simulation

Tool Used: Hydra (Kali Linux)
Target: DC01 – Administrator account
Command Example:

hydra -l Administrator -P /usr/share/wordlists/rockyou.txt smb://<DC01-IP>


Observation:
Multiple failed login attempts generated within a short time frame.

Screenshot: Hydra attack running on Kali (terminal output).

4. Detection and Analysis
4.1 Failed Logon Analysis

SPL Used:

index=windows EventCode=4625


Findings:

Numerous failed logons detected from Kali’s IP.

Targeted account: Administrator.

Frequency indicates brute-force activity.

Screenshot: Search results showing failed logon events.

4.2 Account Lockout Events

SPL Used:

index=windows EventCode=4740


Findings:

Account lockout triggered after repeated failed logons.

Locked account: Administrator

Lockout confirms prevention policy is active.

Screenshot: Search results showing 4740 events.

4.3 Optional Correlation Timeline

SPL Used:

index=windows (EventCode=4625 OR EventCode=4740)


Displays events in chronological order, showing failed logons immediately followed by account lockout.

Demonstrates clear attack → response sequence.

Screenshot: Timeline view with 4625 and 4740 events.

5. Prevention Measures

Account Lockout Policy configured in Group Policy on DC01:

Threshold: 5 failed logons

Duration: 15 minutes

Reset Counter: 15 minutes

Post-policy, re-running the Hydra attack resulted in immediate account lockout after threshold reached.

Screenshot: Account Lockout Policy settings in GPO.

6. Dashboard Monitoring

A Splunk dashboard was created to visualize:

Total failed logons (single value)

Failed logons over time (line chart)

Top source IPs attempting logons (bar chart)

Targeted usernames (bar chart)

Account lockout events (table)

Screenshot: Full dashboard showing failed logons and lockout events.

7. Analyst Insights

The attack was successfully detected in near-real-time using Splunk.

Event ID 4625 effectively identifies brute-force attempts.

Event ID 4740 confirms preventive controls (account lockout) are working.

Simple SPL queries are sufficient to analyze and correlate failed logons and lockouts.

Dashboard provides SOC-ready visual overview for monitoring and reporting.

8. Conclusion

This project demonstrates:

End-to-end detection workflow: Attack → Logging → Detection → Prevention

SOC analyst skills: Log analysis, SPL queries, dashboard creation, and policy verification

Portfolio relevance: Clear evidence of the ability to detect and respond to brute-force attacks using Splunk

Next Steps / Recommendations:

Expand to include Sysmon logs for process-level monitoring

Add alerting rules in Splunk for repeated failed logons to trigger SOC notifications

Integrate firewall logs for network-level visibility

This report is structured to show evidence, analysis, and preventive action, making it perfect for a cybersecurity portfolio.