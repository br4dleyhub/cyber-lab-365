#### **DAY 4 — WINDOWS HARDENING**  
_VM: WINDOWS 10_

Today, I completed the full Windows hardening workflow. This included user management, password and lockout policies, log analysis, RDP configuration, PowerShell scripting, and firewall rules.

---

### **Create Users & Groups**

**1. Open Computer Management**  
`Win + R` → `compmgmt.msc`  
Navigate to: **Local Users and Groups → Users**

**2. Create three users**  
Right-click → **New User…**

**User 1 — Basic User**  
- Username: `win_basic`  
- Password: anything  
- ✔ Password never expires  
- ✘ User must change password at next logon  

**User 2 — Advanced User**  
- Username: `win_advanced`  
- Same password rules  

**User 3 — Admin User**  
- Username: `win_admin`  
- Strong password  

Click **Create → Close**.

**3. Assign groups**

**`win_basic`**  
- Default (no change)

**`win_advanced`**  
- Member Of → Add → **Power Users**

**`win_admin`**  
- Member Of → Add → **Administrators**

**Screenshots:**  
[userlistwin](/04-windows-hardening/screenshots/userlistwin.png)  
[win_advanced_group_membership](/04-windows-hardening/screenshots/win_advanced_group_membership.png)  
[win_admin_group_membership](/04-windows-hardening/screenshots/win_admin_group_membership.png)

---

### **Create GPO for Password & Lockout Policy**

**1. Open Local Group Policy Editor**  
`Win + R` → `gpedit.msc`

Navigate to:  
**Computer Configuration → Windows Settings → Security Settings → Account Policies → Password Policy**

**2. Configure password policies**

- Minimum password length: **12**  
- Enforce password history: **5**  
- Maximum password age: **30 days**  
- Minimum password age: **1 day**  
- Password must meet complexity: **Enabled**

**3. Configure Account Lockout Policy**

Navigate to: **Account Lockout Policy**

- Account lockout threshold: **5**  
- Account lockout duration: **15 minutes**  
- Reset account counter after: **15 minutes**

**Screenshots:**  
[password_policy_page](/04-windows-hardening/screenshots/password_policy_page.png)  
[lockout_policy_page](/04-windows-hardening/screenshots/lockout_policy_page.png)

#### *What I learned*
- Windows stores security policy in the registry (`HKLM\SECURITY\Policy`)  
- Lockout policies reduce brute-force attacks  
- Password aging and complexity improve account security  

---

### **Enable & Read Event Viewer Logs**

**1. Open Event Viewer**  
`Win + R` → `eventvwr.msc`  
Navigate to: **Windows Logs → Security**

This log is essential for SOC monitoring.

**2. Generate logs**

Perform actions such as:  
- Wrong password attempts  
- Switching users  
- Creating/removing accounts  
- Editing password policies  

These trigger key event IDs:

- **4624** — Successful logon  
- **4625** — Failed logon  
- **4720** — User account created  
- **4728** — User added to a group  
- **4732** — User added to Admin group  
- **1102** — Security logs cleared (critical)

**3. Analyze one event**

Look for:  
- Originating account  
- Logon type  
- Network address  
- User privileges  

**Screenshots:**  
[security_log_list](/04-windows-hardening/screenshots/security_log_list.png)  
[one_event_with_details](/04-windows-hardening/screenshots/one_event_with_details.png)

#### *What I learned*
- How SOC teams monitor authentication  
- How brute-force attempts appear in logs  
- How to analyze security-critical Windows events  

---

### **Enable RDP (Remote Desktop)**

**1. Open RDP settings**  
Run: `SystemPropertiesRemote.exe`  
or go to: **Settings → System → Remote Desktop**

**2. Enable RDP**  
- ✔ Allow remote connections to this computer  
- ✔ Allow through Windows Firewall  

**Screenshot:**  
[RDP_enabled](/04-windows-hardening/screenshots/RDP_enabled.png)

#### *Why this matters*
- RDP is a top attack vector for ransomware  
- Used in lateral movement during intrusions  
- Must be secured and monitored carefully  

---

### **Create a PowerShell Automation Script**

This script collects basic system information automatically.

**1. Create the script**  
Open Notepad → save as `system-info.ps1`

```powershell```
Write-Output "=== SYSTEM INFO REPORT ==="

Get-Date

Get-ComputerInfo | Select-Object CsName, WindowsVersion, OsArchitecture

Get-LocalUser

Get-LocalGroupMember -Group "Administrators"

Get-WmiObject Win32_LogicalDisk 


Save → Desktop.


2\. Run the script
powershell -ExecutionPolicy Bypass -File .\\system-info.ps1

If the window opens and closes instantly, run from an open PowerShell with:
powershell -NoExit -File .\\system-info.ps1 or add at the end: Read-Host "Press Enter to exit"


Screenshots :

[system-info-script](/04-windows-hardening/screenshots/system-info-script.png)
[powershell\_output](/04-windows-hardening/screenshots/powershell\_output.png)


###### **What I learned**

1. *Basic PowerShell scripting*
2. *How analysts collect system info quickly*
3. *Execution policies*


###### **Windows Firewall Basics** 

1\. Open firewall console

control firewall.cpl
* *Click Advanced Settings*

2\. Check inbound rules
Scroll and take note of rules like:

* *RDP (TCP 3389)*
* File and Printer Sharing
* Windows Remote Management
* SSH if installed


3\. Create a custom rule (practice)
Inbound Rules → New Rule
Choose: Port
TCP
Port: 3389 (RDP)
Allow the connection
Name: RDP-Custom


Screenshots :

[advanced\_firewall\_window](/04-windows-hardening/screenshots/advanced\_firewall\_window.png)
[custom\_rule](/04-windows-hardening/screenshots/custom\_rule.png)


###### **What I learned**

1. *How host-based firewalls work*
2. *Why RDP, SMB, and remote services are dangerous if exposed*
3. *How SOC teams detect blocked/allowed traffic*


###### DAY 4 IS 100% COMPLETE 

*✔ Users created (win\_basic / win\_advanced / win\_admin)*

*✔ Groups configured (Power Users / Administrators)*

*✔ Password \& lockout policy created*

*✔ Event Viewer logs generated + reviewed*

*✔ RDP enabled*

*✔ PowerShell script created + executed*

*✔ Windows Firewall checked + new rule created*


###### **WHAT I DID TODAY:**

* I created Windows users and assigned privilege levels (win\_basic, win\_advanced, win\_admin).
* I configured password and lockout policies via Local Group Policy.
* I generated and analyzed authentication and audit logs in Event Viewer.
* I enabled Remote Desktop and validated firewall rules.
* I wrote and executed a PowerShell script to collect system information.
* I reviewed Windows Firewall rules and created a custom rule.


###### **WHAT I LEARNED:**

1. *How Windows manages accounts, groups, and privileges.*
2. *How to enforce security policy via Group Policy and the registry.*
3. *How Event Viewer records key security events and what to look for.*
4. *How to secure and test remote access (RDP).*
5. *How to write simple PowerShell scripts and manage execution policies.*
6. *How host-based firewall rules control network exposure.*


###### **WHY IT IS IMPORTANT:**

* ***Implementing least privilege and strong password policies reduces the chance of account compromise.***
* ***Account lockouts protect against brute-force attacks.***
* ***Event logs are essential for detecting and investigating security incidents.***
* ***Securing RDP prevents a common entry vector for ransomware and lateral movement.***
* ***PowerShell skills allow fast collection of evidence and automation for response.***
* ***Firewall rules limit the system’s attack surface and help prevent unauthorized access.***
