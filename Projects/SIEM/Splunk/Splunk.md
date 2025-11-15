# Setting up a SIEM Solution with Splunk Enterprise in a Windows Domain Lab

## Overview

In this project, I configured and deployed **Splunk Enterprise** within a Windows domain environment as a SIEM (Security Information and Event Management) solution. The lab setup enables centralized collection and analysis of event logs from multiple domain-joined workstations and servers, providing a foundation for advanced security monitoring.

---
**Tools Used**: Splunk Enterprise, Windows Defender Firewall, Windows 11 Workstations, Domain Controller.
**Skills Demonstrated**: SIEM Deployment, Log Management, Network Security Fundamentals.
  
---

## Lab Topology
![Lab Topology](Screenshots/Lab_topology.png)

---

## **Step1: Install and Configure the Splunk Enterprise Application**


- Installed Splunk Enterprise on a Windows 11 domain member workstation using the Domain Administrator account to ensure seamless log collection across the domain.
    
- Created a local Splunk admin account with a secure password for accessing the Splunk web application.
    

![Splunk_installer](Screenshots/Splunk_installer.png)


**_Key insight:_** Installing with domain admin privileges facilitates log access from networked devices.

![domain_account](Screenshots/domain_account.png)

 - In addition to this,  _A local account with a password needs to be created that will be used to log into the Splunk Enterprise web application._

 - In the fly-out window, in the **DATA** pane, select **Forwarding and receiving**.
   
![Data_pane](Screenshots/Data_pane.png)


 - Added Port details in the Receive Data section.

![Port](Screenshots/Port.png)

**_Note:_** _The_ **_Splunk Enterprise_** _application was configured to listen for network traffic. The Windows Firewall needs to be configured to ensure the traffic can be received on the port._

---

### **Step2: Windows Defender Firewall Rule to allow Splunk Activity**

On **Windows Defender Firewall with Advanced Security**, selected **Inbound Rules** and  **New Rule** was set. Selected **Port** on the **Rule Type** window. Entered the port number in the **Specific local ports** field: Specified a meaningful custom name like "Splunk" and a description for the rule.

All set! Now lets go to Client configuration

---

### **Step 3: Configuring a Splunk Client**

- Installed and configured the Splunk Universal Forwarder on domain member workstations and servers to forward event logs to the Splunk server.

![splunk_client](Screenshots/splunk_client.png)


- Configured forwarding using domain admin credentials for authentication.

![forwarder_domain](Screenshots/forwarder_domain.png)
- Entered Splunk server IP and listening port on clients to ensure proper log forwarding.

![splunk_hostname](Screenshots/splunk_hostname.png)

---

### **Step 4: Collecting and Searching Logs in Splunk**


- Logged into the Splunk Enterprise web UI on the server using the local Splunk admin account.
- Configured new server class names and selected specific event log types to monitor.

![Server_class](Screenshots/Server_class.png)

 - In **Select Event Logs** pane, chose the necessary event log types.

![Event_logs](Screenshots/Event_logs.png)


- Verified successful forwarding of logs from clients to Splunk and performed sample event searches.

![Search](Screenshots/Search.png)

- Below is a sample search result of Event Logs forwarded from the Splunk Client to the Splunk Server.

![Sample_Result](Screenshots/Sample_Result.png)

---

## Lessons Learned & Next Steps

- **_Importance of proper user account selection during installation for cross-device log collection._**
- **_Necessity of configuring firewall rules to allow data traffic._**
- **_Practical experience with deploying Splunk Universal Forwarder for centralized log aggregation._**
- **_Next steps include integrating alerting rules and investigating threat detection use cases._**

---
