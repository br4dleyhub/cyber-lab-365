## **Offensive Security**



#### Day 7- STEP 8 - *Password Cracking (John the Ripper \& Hashcat)*



##### *Objective*



***Understand how attackers crack real password hashes after gaining system access, and evaluate password strength using real extracted hashes from Metasploitable2.***

***This step simulates a post-exploitation credential attack, a critical phase in real-world intrusions.***





###### **Why This Step Matters**



After exploitation and privilege escalation (Step 7), attackers rarely stop at shell access.

They attempt to:

* Extract credentials
* Crack passwords offline
* Reuse credentials on other services or machines
* This step connects exploitation → persistence → lateral movement.





**🧪 Lab Context**

* **Attacker: Kali Linux**
* **Target: Metasploitable2**
* **Hash Source: /etc/shadow**
* **Hash Type: md5crypt ($1$)**





##### **Step 1 — Preparing the Hashes**



After gaining root access on Metasploitable2, the password hashes were extracted from /etc/shadow and saved locally on Kali.

The file contains:

1. Usernames
2. Password hashes
3. Account metadata
4. Not all entries are crackable (some accounts are locked or system-only).



**Screenshot**



\[nano-metasploitable-hashes-txt](/07-Password-Cracking/screenshots/nano-metasploitable-hashes-txt.png)







##### **Step 2 — Identify Supported Hash Formats (John)**



Before cracking, it is essential to confirm that the tool supports the hash format.

* john --list=formats



*This command lists all hash formats supported by John the Ripper.*





###### **Why this matters**



Using the wrong hash format leads to wasted time and failed attacks.



**Screenshot**



\[john-list-format](/07-Password-Cracking/screenshots/john-list-format.png)







##### **Step 3 — Password Cracking with John the Ripper**



###### Command Used

* john metasploitable\_hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt



###### **What this does**

* Loads all valid password hashes
* Uses rockyou.txt as a dictionary
* Attempts offline cracking (fast \& stealthy)



**Screenshot**



\[john-running](/07-Password-Cracking/screenshots/john-running.png)







##### **Step 4 — Viewing Cracked Passwords**



* john --show metasploitable\_hashes.txt



**Result:**

Several weak passwords were successfully cracked, such as:

* Simple dictionary words
* Reused usernames
* Very low-entropy passwords



**Screenshot**



\[showing-john-result](/07-Password-Cracking/screenshots/showing-john-result.png)







##### **Step 5 — Understanding Uncracked Hashes**



*Not all hashes were cracked.*



**Reasons include:**

1. Passwords not present in wordlists
2. Higher entropy
3. Stronger password choices
4. This highlights why password policy matters and why dictionary attacks are not always sufficient.





##### ***🧠 Skills Learned***



###### ***By completing this step, the following skills were demonstrated:***

* ###### <i>Extracting and handling real password hashes</i>
* ###### <i>Identifying Unix hash formats</i>
* ###### <i>Performing dictionary-based cracking</i>
* ###### <i>Understanding hash entropy</i>
* ###### <i>Interpreting cracking failures</i>
* ###### <i>Differentiating offensive tooling (John vs Hashcat)</i>





###### **End-State Validation (Day 7)**



*By the end of Phase 4 — Step 8 — Day 7, the attacker can:*

* *Crack weak passwords from real systems*
* *Explain why some hashes remain secure*
* *Prepare hashes correctly for cracking tools*
* *Understand how credential attacks enable lateral movement*





