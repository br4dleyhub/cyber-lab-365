# Offensive Security

## Day 5 — Practical Enumeration

### 

### Step 6 — Network Scanning \& Attack Surface Mapping



##### **Objective**



***The objective of this phase is to perform full reconnaissance and enumeration of the lab environment from the attacker’s perspective (Kali Linux).***



###### At the end of Day 5, the goal is to:

* Identify all live hosts
* Discover open ports and running services
* Enumerate versions, configurations, and weaknesses
* Compare hardened vs intentionally vulnerable systems
* Decide which target is the priority for exploitation in Step 7
* This phase focuses only on practical actions. Documentation reflects real commands and real outputs.





##### **🧪 Lab Environment**



Attacker ---> Kali Linux — 192.168.187.50



Targets  ---> Windows — 192.168.187.60 (Hardened)



&nbsp;	 ---> Metasploitable2 — 192.168.187.40 (Intentionally vulnerable)



Ubuntu Server was removed prior to this phase and is not part of Day 5 activities.





##### **Step 1 — Network Reachability Verification**



###### Before any scanning or exploitation, basic connectivity must be verified.



Commands Used:

* ping 192.168.187.60
* ping 192.168.187.40



Purpose:

* Confirm targets are online
* Validate network configuration
* Observe ICMP behavior (allowed or blocked)



Observation:

* Metasploitable2 responds to ICMP
* Windows may restrict ICMP depending on firewall rules



Screenshots:

[kalipingwindows](/04-windows-hardening/screenshots/kalipingwindows.png)



[kalipingmetasploitable2](/04-windows-hardening/screenshots/kalipingmetasploitable2.png)





##### **Step 2 — Full Network Scan (Windows)**



Command Used:

* nmap -A -p- 192.168.187.60



Purpose:

* Identify all open TCP ports
* Detect OS and service versions
* Evaluate attack surface of a hardened system



Key Findings:

* Limited number of open ports
* Services appear up to date
* No obvious critical vulnerabilities



This confirms **Windows is not the primary exploitation target** but will be important later for:

1. Blue team monitoring
2. SIEM ingestion
3. Incident response simulation



Screenshots:

[fullnmapwindows1](/04-windows-hardening/screenshots/fullnmapwindows1.png)



[fullnmapwindows2](/04-windows-hardening/screenshots/fullnmapwindows2.png)





##### **Step 3 — Full Network Scan (Metasploitable2)**



Command Used:

* nmap -A -p- 192.168.187.40



Purpose:

* Expose the complete attack surface
* Identify outdated and vulnerable services
* Prepare for exploitation in Step 7



Key Findings:

1. Large number of open ports
2. Multiple legacy services detected
3. Clear presence of known vulnerable software versions
4. Metasploitable2 is confirmed as the primary exploitation target.



Screenshots:



[fullnmapmetasploitable2-1](/04-windows-hardening/screenshots/fullnmapmetasploitable2-1.png)



[fullnmapmetasploitable2-2](/04-windows-hardening/screenshots/fullnmapmetasploitable2-2.png)



[fullnmapmetasploitable2-3](/04-windows-hardening/screenshots/fullnmapmetasploitable2-3.png)



[fullnmapmetasploitable2-4](/04-windows-hardening/screenshots/fullnmapmetasploitable2-4.png)



[fullnmapmetasploitable2-5](/04-windows-hardening/screenshots/fullnmapmetasploitable2-5.png)



##### **Step 4 — Web Service Enumeration**



##### *4.1 Web Technology Fingerprinting*



Command Used:

* whatweb http://192.168.187.40



Purpose:

* Identify web server technologies
* Detect outdated frameworks
* Prepare targeted web exploits



Screenshot:

[websitemetasploitable2](/04-windows-hardening/screenshots/websitemetasploitable2.png)



##### *4.2 Web Vulnerability Scanning*



Command Used:

* nikto -h 192.168.187.40



Purpose:

* Detect web misconfigurations
* Identify vulnerable scripts and directories
* Highlight outdated Apache/PHP components



Key Findings:

1. Multiple vulnerabilities detected
2. Dangerous default configurations present
3. Web services are exploitable



Screenshots:



[niktometasploitable2-1](/04-windows-hardening/screenshots/niktometasploitable2-1.png)



[niktometasploitable2-2](/04-windows-hardening/screenshots/niktometasploitable2-2.png)





##### **Step 5 — SMB Enumeration (Windows \& Metasploitable2)**



Command Used:

* enum4linux -a 192.168.187.60
* enum4linux -a 192.168.187.40



Purpose:

* Enumerate SMB shares
* Discover usernames and groups
* Gather OS and domain information



Observations:

* Windows: Minimal information disclosed (properly hardened)
* Metasploitable2: Significant information leakage (users, shares)



***This confirms Metasploitable2 is misconfigured by design and ideal for learning exploitation techniques.***



Screenshots:



[enum4linux-windows](/04-windows-hardening/screenshots/enum4linux-windows.png)



[enum4linux-metasploitable2](/04-windows-hardening/screenshots/enum4linux-metasploitable2.png)



[enum4linux-metasploitable2-1](/04-windows-hardening/screenshots/enum4linux-metasploitable2-1.png)



[enum4linux-metasploitable2-2](/04-windows-hardening/screenshots/enum4linux-metasploitable2-2.png)



[enum4linux-metasploitable2-3](/04-windows-hardening/screenshots/enum4linux-metasploitable2-3.png)



[enum4linux-metasploitable2-4](/04-windows-hardening/screenshots/enum4linux-metasploitable2-4.png)



[enum4linux-metasploitable2-5](/04-windows-hardening/screenshots/enum4linux-metasploitable2-5.png)



[enum4linux-metasploitable2-6](/04-windows-hardening/screenshots/enum4linux-metasploitable2-6.png)





##### **Step 6 — Service Version Enumeration (FTP \& SSH)**



###### FTP Banner Enumeration



Command Used

* nmap -sV -p21 192.168.187.40



Key Finding:

* vsftpd 2.3.4 detected
* Known backdoor vulnerability
* Strong candidate for initial exploitation



Screenshot:



[ftp-banner-version](/04-windows-hardening/screenshots/ftp-banner-version.png)





###### SSH Version Enumeration



Commands Used

* nmap -sV -p22 192.168.187.40
* nmap -sV -p22 192.168.187.60



Observations:

* Metasploitable2 uses outdated OpenSSH
* Windows SSH service is more recent



Screenshot:



[ssh-version-metasploitable2](/04-windows-hardening/screenshots/ssh-version-metasploitable2.png)



[ssh-version-windows](/04-windows-hardening/screenshots/ssh-version-windows.png)





##### Analysis \& Attacker Mindset

Machine	Security           Level	            Attack Priority

Windows	Hardened	          Low

Metasploitable2	       Highly Vulnerable      High (Primary Target)



##### Primary Attack Vectors Identified



* FTP (vsftpd 2.3.4
* SMB
* Web services
* Legacy daemons





##### **Day 5 Outcome**

*By completing Phase 4 — Step 6 — Day 5, the following skills were demonstrated:*

* *Real-world reconnaissance methodology*
* *Professional enumeration workflow*
* *Ability to distinguish hardened vs vulnerable systems*
* *Preparation for controlled exploitation*
* *Evidence-based decision making*
