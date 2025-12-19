# **ATTACK SIMULATION (KALI → UBUNTU)**





***To ensure the lab is realistic, SOC-grade, and interview-ready, I performed all attacks from Kali Linux, not from the Ubuntu/Splunk host itself.***



**This approach allowed me to simulate:**



1. A real external attacker
2. Real network-based authentication attempts
3. Authentic security events
4. Proper log ingestion by Splunk
5. A full attacker → victim → SIEM workflow





##### **Objective**



*My objective in this phase was to generate real attack activity on the Ubuntu server so that Splunk could:*

* Ingest the logs
* Detect malicious behavior
* Trigger security alerts
* Support investigation from a SOC analyst perspective



###### **Lab Topology**

**Role	           Machine	          Network**

**Attacker	 Kali Linux	           VMnet2**

**Victim + SIEM	 Ubuntu Server (Splunk)    VMnet2**

**Analyst	Windows  Host (Splunk Web)	   VMnet2**



*All machines were connected to VMnet2, and I confirmed network connectivity between Kali and Ubuntu before starting the attack simulation.*





##### **STEP 10.1 — Preparing the Ubuntu Victim**



* **Ensuring SSH was running**



On the Ubuntu server, I verified the SSH service:

* sudo systemctl status ssh





###### The service was not running, I enabled and started it:

* sudo systemctl start ssh
* sudo systemctl enable ssh





###### **Confirming log ingestion by Splunk**



*To ensure authentication logs were being monitored, I ran:*

* sudo /opt/splunk/bin/splunk list monitor





**I confirmed that the following log file was listed:**

* /var/log/auth.log





*This verified that Splunk was ingesting SSH authentication events.*





##### **STEP 10.2 — Launching the Attack from Kali (Mandatory)**



*⚠️ This attack was intentionally executed from Kali Linux to simulate a real external threat.*



From Kali, I attempted to SSH into the Ubuntu server using an invalid username:

* **ssh fakeuser@192.168.187.70**



*I repeatedly entered an incorrect password 6–10 times, generating multiple failed authentication attempts.*



These actions produced logs such as:

**Failed password for invalid user fakeuser from <KALI\_IP>**





###### Screenshot



* \[kali\_ssh\_attack](/\*\*\*\*\*/\*\*\*\*\*/\*\*\*\*\*/kali\_ssh\_attack.png)







##### **STEP 10.3 — Verifying Attack Logs on Ubuntu**



On the Ubuntu server, I verified that the attack activity was logged:

* **sudo tail -n 20 /var/log/auth.log**





I observed entries similar to:



*Failed password for invalid user fakeuser from 192.168.187.X*





This confirmed that the attack attempts were successfully logged by the system.





###### Screenshot



* \[linux\_auth\_log\_attack](/\*\*\*\*\*/\*\*\*\*\*/\*\*\*\*\*/linux\_auth\_log\_attack.png)







##### **STEP 10.4 — Detecting the Attack in Splunk**



**Using Splunk Web, I ran the following search to identify brute-force behavior:**



* index=\* source="/var/log/auth.log" "Failed password"| rex "from (?<src\_ip>\\d+\\.\\d+\\.\\d+\\.\\d+)"| stats count by src\_ip| where count >= 5| sort -count





The results clearly showed multiple failed login attempts originating from Kali’s IP address, confirming successful detection.





###### Screenshot



* \[linux\_bruteforce\_detected](/\*\*\*\*\*/\*\*\*\*\*/\*\*\*\*\*/linux\_bruteforce\_detected.png)







##### **STEP 10.5 — Creating a SOC Alert**



*I saved the detection search as a Splunk alert with the following configuration:*



**Setting	Value**

1. Alert Name         ---> Linux SSH Brute Force
2. Type               ---> Scheduled 
3. Time Range	      ---> Last 5 minutes
4. Trigger Condition  ---> Number of results > 0
5. Severity	      ---> High





###### Screenshot



* \[linux\_bruteforce\_alert\_created](/\*\*\*\*\*/\*\*\*\*\*/\*\*\*\*\*/linux\_bruteforce\_alert\_created.png)







##### **STEP 10.6 — Triggering \& Validating the Alert**



*To validate the alert, I repeated the failed SSH attempts from Kali and waited approximately 1–2 minutes.*

*I then navigated to Splunk → Alerts, where the alert successfully triggered, confirming the detection and alerting pipeline was functioning correctly.*



###### Screenshot



* \[alert\_triggered](/\*\*\*\*\*/\*\*\*\*\*/\*\*\*\*\*/alert\_triggered.png)







##### **STEP 10.7 — SOC Investigation**



After the alert fired, I opened the alert results and conducted a basic investigation using:



* index=\* source="/var/log/auth.log"





From the events, I identified:



1. The attacker IP address
2. The number of authentication attempts
3. The time window of the attack
4. The targeted username(s)
5. This step completed the full SOC workflow: attack → detection → alert → investigation.



###### Screenshot



* \[incident\_investigation](/\*\*\*\*\*/\*\*\*\*\*/\*\*\*\*\*/incident\_investigation.png)







###### Final Summary



***In this phase, I successfully simulated a real SSH brute-force attack from Kali Linux against an Ubuntu server. By ingesting, detecting, alerting, and investigating the attack in Splunk, I validated an end-to-end SOC workflow using realistic attacker behavior and authentic system logs.***

