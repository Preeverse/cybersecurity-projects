# Account Lockout Policy Creation

## Objective
Configure and enforce an Account Lockout Policy on the Domain Controller (DC01) to prevent successful brute‑force attacks and limit unauthorized authentication attempts.

---

## Steps Performed

### 1. Open Group Policy Management
- Access **Group Policy Management Console (GPMC)** on DC01.
- Navigate to:
  `Default Domain Policy → Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy`

  ![acc-lockout-nav](screenshots/acc_lockout_nav.png)

### 2. Configure Required Settings
The following values were applied:

- **Account Lockout Threshold:** 5 invalid logon attempts  
- **Account Lockout Duration:** 15 minutes  
- **Reset Account Lockout Counter:** 15 minutes  

These settings ensure that repeated failed authentication attempts automatically trigger a temporary lockout, stopping brute‑force attacks early.

## 3. Apply and Force Policy Update
- Run the following command on DC01 from a cmd window and Win11 endpoints:

`gpupdate /force`

## 4. Re‑Run Brute-Force Attack to Validate Prevention
### Attack Source
- **Kali Linux** using Hydra to brute‑force the **Administrator** account on DC01.

### Expected Behavior
- After 5 failed logons (Event ID **4625**),  
- Windows should trigger an **Account Lockout** (Event ID **4740**)  
- Hydra’s attempts should stop immediately due to the lockout.

### Outcome
- Lockout event occurred exactly at threshold.
- Hydra was unable to proceed beyond that point.
- Windows Security logs confirmed the lockout sequence.

## 5. Validation in Splunk

### SPL Used in Splunk

`index=windows (EventCode=4625 OR EventCode=4740) | sort _time`

---

## Analyst Summary
- Account Lockout Policy successfully prevented brute‑force attacks.
- Detection (4625), lockout (4740), and prevention were visible in Splunk.
- System demonstrated end‑to‑end security control: Attack → Detection → Prevention → Validation.

This prevention step effectively protects privileged accounts such as Administrator.

## Conclusion

By implementing and validating a strict Account Lockout Policy, brute‑force attacks are reliably stopped after a controlled threshold. This forms a core defensive measure within Windows environments and enhances the integrity of authentication security.

---
