# Index Creation & Universal Forwarder Inputs Configuration  

---

##  Objective  
This project unit focuses on two essential components of Splunk ingestion setup:

1. Creating a custom index in Splunk Enterprise (server side)  
2. Configuring the Splunk Universal Forwarder (client side) to send logs into that specific index  

This is the foundation for structured Windows event log ingestion and enables all detection and analysis tasks later.

---

##  Environment Overview  

| Component | Role |
|----------|------|
| **Win11** | Splunk Enterprise Server |
| **DC01** | Domain Controller — Windows Event Logs source |
| **Universal Forwarder** | Installed on DC01 |
| **Index Used** | `windows` |

---

# 1️⃣ Creating a Custom Index in Splunk Enterprise  

**Navigation:**  
`Splunk Web → Settings → Indexes → New Index`

### Steps  
1. Click **New Index**  
![Index-Nav](screenshots/index_nav.png)
2. Configure the following:  
   - **Index Name:** `windows`  
   - **Data Type:** Events  
   - Leave other settings as default  
3. Save the index and verify its creation by searchign it in Search Index field.
![windows-verifyindex](screenshots/windows_index.png)

---

# 2️⃣ Configuring Universal Forwarder to Send Logs to the Index  

Universal Forwarder config files are stored in:

`C:\Program Files\SplunkUniversalForwarder\etc\system\local\`


Edit the file: `inputs.conf` to include the necessary Windows logs. Here we mention Security, System and Application Event logs.

![inputs-conf](screenshots\inputs_conf.png)

### Explanation  

| Setting                  | Purpose                                                   |
|--------------------------|-----------------------------------------------------------|
| `disabled = 0`           | Enables the log source                                    |
| `index = windows`        | Sends logs to your custom index                           |
| `WinEventLog://Security` | Forwards Windows Security Logs (4624, 4625, 4740, etc.)   |

---

## 🔄 Restart the Universal Forwarder  

After editing `inputs.conf`, restart the service in the cmd.exe window to apply changes:

`cd C:\Program Files\SplunkUniversalForwarder\bin`

Use splunk CLI to restart the splunk universal forwarder

`splunk restart`

---

# 3️⃣ Validate Log Ingestion in Splunk  

Run the following SPL query to check if logs are flowing:

`index=windows | head 20`

![sample_search](screenshots/sample_search.png)

You should see Security, System, and Application event logs from DC01.

---

# 4️⃣ Conclusion 
  
The same methodology used here can be applied to a wide range of systems, including:

- Linux servers  
- Sysmon (advanced Windows telemetry)  
- Firewalls and IDS/IPS devices  
- Cloud applications  
- Any application capable of generating logs  

Following this standardized workflow ensures:

- Consistent log organization  
- Easier troubleshooting and maintenance  
- Better visibility and control over data sources  
- Faster development of detections, dashboards, and use-cases  
- A more manageable and scalable security monitoring environment  

---

# 5️⃣ What This Enables Next 

Completing this step enables:

- Brute-force attack simulation (Hydra)  
- Detection of Event ID **4625** (failed logons)  
- Detection of Event ID **4740** (account lockouts)  
- Dashboard creation  
- Use-case development and alerting  
- Full SIEM visibility into Windows authentication logs  

---






