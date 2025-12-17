#### **DEFENSIVE SECURITY**



*Phase 5 — Step 9: Windows Log Forwarding to Splunk (SIEM Lab)*



###### ***Objective***

###### 

###### ***Deploy a real endpoint agent (Splunk Universal Forwarder) on Windows and forward security-relevant logs to the Ubuntu Splunk SIEM. This simulates how enterprise endpoints send telemetry to a SOC.***



##### **🧱 Lab Architecture**



1. **Splunk Enterprise (Receiver / Indexer): Ubuntu Server**
2. **Splunk Universal Forwarder (Sender): Windows 10**
3. **Forwarding Port: 9997**
4. **Management Port (Web UI): 8000**
5. **Network: VMware VMnet2 (internal lab network)**





##### **STEP 9.1 — Verify Splunk Is Running on Ubuntu**



Before accepting logs, Splunk must be running and listening.



**Commands**

* sudo /opt/splunk/bin/splunk status



**Expected result: splunkd is running**



Screenshot



* splunk\_service\_running





##### **STEP 9.2 — Enable Receiving on Splunk (Port 9997)**



Splunk must be configured to receive forwarded logs.



**Command**

sudo /opt/splunk/bin/splunk enable listen 9997



**Verify:**

* *sudo /opt/splunk/bin/splunk list listen*



**Expected result:**

* 9997



Screenshot



* splunk\_receiving\_enabled





##### **STEP 9.3 — Verify Linux Log Ingestion (Sanity Check)**



Before adding Windows logs, confirm Splunk can ingest local Linux logs.



**Command**

* sudo /opt/splunk/bin/splunk add monitor /var/log/auth.log
* Splunk Web Search
* source="/var/log/auth.log"



Screenshots



* linux\_auth\_log\_ingested



* linux\_logs\_ingested





##### **STEP 9.4 — Install Splunk Universal Forwarder on Windows**



Installation Configuration



Receiver IP: 192.168.187.70



Receiver Port: 9997



Run as Service: Enabled



*After installation, confirm the forwarder is running.*



**Verification (PowerShell)**

* cd "C:\\Program Files\\SplunkUniversalForwarder\\bin"
* .\\splunk.exe status



**Expected: splunkd is running**





Screenshots



* windows\_uf\_installed



* splunk\_status\_ok





##### **STEP 9.5 — Verify Forwarder Connection**



Ensure the Windows forwarder is correctly connected to the Splunk receiver.



**Command (Windows)**

* .\\splunk.exe list forward-server



Expected output:



**Active forwards:**

*     **192.168.187.70:9997**



Screenshot



* windows\_forwarder\_connected





##### **STEP 9.6 — Configure Windows Event Log Inputs**



Windows logs are enabled via inputs configuration.



Configuration File

* *C:\\Program Files\\SplunkUniversalForwarder\\etc\\system\\local\\inputs.conf*



Example configuration:



**\[WinEventLog:Security]**

**disabled = 0**



**\[WinEventLog:System]**

**disabled = 0**



**\[WinEventLog:Application]**

**disabled = 0**



Restart forwarder:

* *.\\splunk.exe restart*





Screenshot



windows\_inputs\_conf





##### STEP 9.7 — Verify Windows Logs in Splunk



Splunk Search

* *index=\* sourcetype=WinEventLog\**



Or more specific:

* *index=\* source="WinEventLog:Security"*





Screenshot



* *windows\_eventlogs\_in\_splunk*







###### **SOC Relevance**



***This phase simulates enterprise endpoint telemetry ingestion, a core SOC function:***

1. ***Endpoint monitoring***
2. ***Authentication tracking***
3. ***Incident detection readiness***



