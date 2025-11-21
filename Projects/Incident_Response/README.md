# Incident Response using SIEM

- Incident Response refers to the steps taken by an organization after a privacy breach or cyberattack.
- The end objective of incident response is to reduce the damage, minimize recovery time and expenses, and prevent secondary effects like ruined brand reputation.
- It is essential for businesses to have a well-defined incident response strategy.


inputs.conf
cd C:\Program Files\SplunkUniversalForwarder\etc\system\local\
splunk restart

inputs.conf controls how the forwarder collects data
index sample search query
index=windows sourcetype="WinEventLog:Security"
index=windows EventCode=4625 | head 10 --> for ffailed logons (to check brute force attacks)
gpupdate /force
Event ID 4740 = account lockout.
Correlation of 4625 → 4740
index=windows (EventCode=4625 OR EventCode=4740)
| eval EventType=case(EventCode==4625,"Failed Logon",EventCode==4740,"Account Lockout")
| table _time, EventType, TargetUserName, Account_Name, Source_Network_Address, CallerHost, Workstation_Name, Message
| sort _time


Alert creation and Dashboard
  ## Project Objective (What you will demonstrate)

You will show:

1. Windows logs ingested into Splunk - done

2. Hydra SMB brute-force attack from Kali against DC01

3. Detection using SPL queries (failed logons, brute force pattern)

Implementing prevention: Account Lockout Policy on DC01

Re-running attack to confirm account lockout triggers

Analyst report + screenshots proving end-to-end detection & response