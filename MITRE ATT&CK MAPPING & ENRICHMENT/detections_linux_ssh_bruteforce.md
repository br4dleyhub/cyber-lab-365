# **PHASE 7 — MITRE ATT\&CK MAPPING \& ENRICHMENT**



*Objective*



***In this phase, I mapped the detected SSH brute-force attack to the MITRE ATT\&CK framework and enriched the detection with threat context.***

***This step transforms raw detection into SOC-grade, intelligence-driven monitoring, aligning the lab with real-world blue team practices.***





##### **Context Recap (From Phase 6)**



In the previous phase, I successfully:

1. Generated a real SSH brute-force attack from Kali Linux
2. Ingested Linux authentication logs into Splunk
3. Detected malicious behavior using SPL
4. Triggered and validated a Splunk alert
5. Investigated the incident from a SOC analyst perspective



***Phase 7 focuses on standardizing and professionalizing that detection.***



##
##



##### **STEP 7.1 — Identify the MITRE ATT\&CK Technique**



Based on the observed behavior:

* Repeated failed SSH login attempts
* Invalid usernames
* External source IP
* High attempt frequency



*I identified the attack as a credential access technique.*

&nbsp;



######          		**MITRE ATT\&CK Mapping**



**Field	 ---------------------------------- Value**

&nbsp;		Tactic	---------------------- Credential Access

&nbsp;		Technique	 ------------------- Brute Force

&nbsp;		Sub-Technique	---------------- T1110.001 — Password Guessing

&nbsp;		Platform --------------------- Linux

&nbsp;		Data Source	------------------ Authentication Logs

&nbsp;		Detection Type --------------- Behavioral



**This confirms the attack aligns with a known and documented adversary technique, not an anomaly or false positive.**



##
##



##### **STEP 7.2 — Update Splunk Alert with MITRE Context**



*I updated the existing Splunk alert (Linux SSH Brute Force) to include MITRE ATT\&CK context.*



**Alert Description** 

    *This alert detects multiple failed SSH authentication attempts consistent with MITRE ATT\&CK technique T1110.001 (Password Guessing), which indicates potential brute-force credential access activity.*





###### Screenshot 


[alert_with_mitre_mapping](/MITRE ATT&CK MAPPING & ENRICHMENT/screenshots/alert_with_mitre_mapping.png)





###### **This ensures the alert is:**



1. Understandable by any SOC analyst
2. Easily mapped to threat intelligence frameworks
3. Suitable for escalation and reporting



##
##



##### **STEP 7.3 — Create a MITRE-Aligned Detection Document**



*I documented the detection logic in a dedicated markdown file to simulate how detections are tracked in professional SOC environments.*



File Location

/detections\_linux\_ssh\_bruteforce.md



Detection Documentation Includes:

* Detection objective
* SPL query
* MITRE ATT\&CK mapping
* Data sources
* False positive considerations
* Recommended response actions



*This step reinforces documentation discipline, which is critical in real SOC operations.*


##
##



##### **STEP 7.4 — Enrichment \& Timeline Analysis**



*To enrich the investigation, I analyzed the attack timeline and frequency to better understand attacker behavior.*



Enrichment SPL

*     index=\* source="/var/log/auth.log" "Failed password"| rex "from (?<src\_ip>\\d+\\.\\d+\\.\\d+\\.\\d+)"| stats count earliest(\_time) latest(\_time) by src\_ip





This allowed me to identify:

1. Attacker IP address
2. Number of attempts
3. Time window of the attack



###### Screenshot



[attack_timeline](/MITRE ATT&CK MAPPING & ENRICHMENT/screenshots/attack_timeline.png)







##### **STEP 7.5 — SOC Readiness Validation**



By completing MITRE mapping and enrichment, I validated that this detection is:

✔ Technically accurate

✔ Threat-intelligence aligned

✔ SOC and interview ready



Lab Status Update

**🟢 Phase 6 completed — Attack simulation, detection, alerting, and investigation validated**  

**🟢 Phase 7 completed — MITRE ATT\&CK mapping and enrichment implemented**





###### Phase 7 Outcome



At the end of this phase, I successfully transformed a raw detection into a fully contextualized SOC detection, aligned with:

1. MITRE ATT\&CK
2. Real-world blue team workflows
3. Analyst documentation standards
4. This phase demonstrates my ability to think and operate like a SOC analyst, not just run tools.
