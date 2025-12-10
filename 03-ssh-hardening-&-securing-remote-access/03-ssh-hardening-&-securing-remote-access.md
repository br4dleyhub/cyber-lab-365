## DAY 3 — LINUX HARDENING (Ubuntu Server)

## 

### VM: Ubuntu Server — IP 192.168.187.70

## 

#### CREATING 3 USERS WITH DIFFERENT PRIVILEGE LEVELS

## 

##### **COMMANDS EXECUTED:**

* ###### ***sudo adduser user\_basic***
* ###### ***sudo adduser user\_advanced***
* ###### ***sudo adduser user\_admin***

## 

###### *sudo usermod -aG sudo user\_advanced*

###### *sudo usermod -aG sudo user\_admin*

## 

##### **WHAT I LEARNED:**

1. ###### ***How Linux handles users and privilege levels.***
2. ###### ***Difference between a standard user and a sudo user.***
3. ###### ***Why least privilege is important in cybersecurity.***
4. ###### ***How companies separate user roles to reduce risk.***
5. ###### ***How attackers exploit weak user configurations during privilege escalation.***

## 

##### **WHAT HAPPENS TECHNICALLY:**

* ###### *"adduser" creates a user, a home directory, and assigns permissions.*
* ###### *"usermod -aG sudo" adds a user into the sudo group.*
* ###### *Members of the sudo group can run administrative commands.*
* ###### *user\_basic = no admin privileges.*
* ###### *user\_advanced and user\_admin = elevated privileges.*

## 

##### **SCREENSHOTS:**

Output of: cat /etc/passwd | tail

Output of: groups user\_advanced

Output of: groups user\_admin

## 

## 

###### *Created three different privilege-level users:*

1. ###### *user\_basic: no administrative privileges*
2. ###### *user\_advanced: sudo privileges*
3. ###### *user\_admin: sudo privileges*

  ### This demonstrates the principle of least privilege and shows how Linux separates normal users from administrators.

## 

##### **CREATING A CRON JOB THAT RUNS EVERY 2 MINUTES**

## 

###### **COMMAND:**

* ###### *crontab -e*

## 

###### At the bottom of the file, added:

*\*/2 \* \* \* \* echo "Cron working" >> /home/brad/cron-test.txt*

## 

##### **WHAT I LEARNED:**

1. ###### *How Linux automation works.*
2. ###### *How cron scheduling syntax operates.*
3. ###### *Why automation is essential in real systems.*
3. 
*###### Cybersecurity relevance: attackers use cron for persistence after compromise.*

## 

##### **WHAT HAPPENS TECHNICALLY:**

* ###### *Every 2 minutes, the cron daemon executes the command.*
* ###### *It writes "Cron working" into the cron-test.txt file.*
* ###### *If the file does not exist, cron automatically creates it.*
* ###### *This confirms the automation is functioning correctly.*

## 

##### SCREENSHOTS:

The crontab -e file showing the cron entry.

The growing cron-test.txt file (after 2–4 minutes).

## 

###### *Configured a cron job to run every 2 minutes.*

###### *It writes text to a file to verify that automation is active.*

###### *Cron is heavily used for legitimate system tasks and is also abused in attacker persistence techniques.*

## 

##### **CHANGING THE DEFAULT SSH PORT**

## 

##### CONFIGURATION EDITED:

* ###### *sudo nano /etc/ssh/sshd\_config*

## 

##### Changed:

* ###### ***#Port 22***

##### To:

* ###### ***Port 2222***

## 

##### Restarted SSH:

* ###### sudo systemctl restart ssh

## 

##### Verification:

* ###### sudo systemctl status ssh

## 

##### **WHAT I LEARNED:**

1. ##### How SSH configuration works.
2. ##### Why port 22 is constantly scanned by attackers.
3. ##### How changing ports reduces noise and automated brute-force attempts.
4. ##### How blue teams harden remote access by modifying service ports.

## 

##### **WHAT HAPPENS TECHNICALLY:**

* ###### Ubuntu stops listening on port 22.
* ###### SSH daemon rebinds to port 2222.
* ###### Automated bots scanning port 22 no longer find the service.

## 

##### SCREENSHOTS:

sshd\_config showing "Port 2222"

ss -tulpn | grep ssh showing SSH on port 2222

## 

###### *Changed the SSH service port from 22 to 2222.*

###### *Restarted SSH and confirmed the new port is active.*

###### *This reduces automated attack attempts and forces attackers to first discover the new port.*

## 

#### **SSH CONNECTION TEST FROM KALI MACHINE**

## 

###### *Using Kali Linux, attempted to connect to the Ubuntu server:*

## 

##### Successful connection:

###### *ssh -p 2222 user\_advanced@192.168.187.70*

###### *(Worked because the correct port was specified.)*

## 

##### Failed connection:

###### *ssh root@192.168.187.70*

###### *(Denied because root login is disabled and also because no port was specified. SSH defaults to port 22, which is now closed.)*

## 

###### **This confirms that:**

## 

* ###### *The new SSH port (2222) works correctly.*
* ###### *Direct root login is fully blocked.*
* ###### *Port 22 is no longer accepting connections.*
* ###### *DISABLING ROOT LOGIN OVER SSH*

## 

##### EDITED FILE:

###### **sudo nano /etc/ssh/sshd\_config**

## 

##### Changed:

* ###### *PermitRootLogin prohib password*

##### To:

* ###### *PermitRootLogin no*

## 

##### Restart SSH:

###### sudo systemctl restart ssh

## 

##### Verification:

###### sshd -T | grep rootlogin

## 

##### Expected output:

###### *permitrootlogin no*

## 

##### **WHAT I LEARNED:**

1. ##### *Why root login is extremely dangerous.*
2. ##### *How brute-force attacks always target "root" first.*
3. ##### *How preventing root SSH access reduces the attack surface.*
4. ##### *Why administrators use normal accounts + sudo instead.*

## 

##### **WHAT HAPPENS TECHNICALLY:**

1. ###### SSH instantly rejects all login attempts using the root user.
2. ###### Attackers must now first compromise a normal account.
3. ###### Privilege escalation becomes significantly harder.

## 

##### **SCREENSHOTS:**

###### The PermitRootLogin no line in sshd\_config.

###### The restart command.

###### The verification output (permitrootlogin no).

###### 

<<<<<<< HEAD:03-ssh-hardening-&-securing-remote-access.md
##### *Disabled root login over SSH.*
=======
[ssh_config on port 22](/screenshots/ssh-port-22.png)
>>>>>>> e10bb142ad112a3935f7424e8c242a60b690728e:03-ssh-hardening-&-securing-remote-access/03-ssh-hardening-&-securing-remote-access.md

##### *This forces attackers to compromise a user account instead of directly attacking root, improving security.*

## 

<<<<<<< HEAD:03-ssh-hardening-&-securing-remote-access.md
=======
[ssh_config on port 2222](/screenshots/ssh-change-port-2222.png)
>>>>>>> e10bb142ad112a3935f7424e8c242a60b690728e:03-ssh-hardening-&-securing-remote-access/03-ssh-hardening-&-securing-remote-access.md


#### **INSTALLING AND CONFIGURING UFW FIREWALL**

## 

##### COMMANDS:

* ###### **sudo apt install ufw -y**
* ###### **sudo ufw allow 2222/tcp**
* ###### **sudo ufw enable**
* ###### **sudo ufw status**

## 

##### **WHAT I LEARNED:**

1. ##### *Basic Linux firewall management.*
2. ##### *How firewalls control inbound network traffic.*
3. ##### *Why organizations restrict exposed services.*
4. ##### *How attackers use port scanning to find open doors.*

## 

##### **WHAT HAPPENS TECHNICALLY:**

1. ###### *UFW starts blocking all incoming connections except allowed ones.*
2. ###### *Only SSH on port 2222 is accessible.*
3. ###### *Every other port becomes protected.*

## 

##### **SCREENSHOTS:**

###### ufw status output showing port 2222 allowed.

## 

##### *Enabled the UFW firewall and allowed only port 2222 for SSH.*

##### *This significantly reduces the system's attack surface.*

##### 





## <b>DAY 4 SUMMARY</b>

## 

#### *Today, I completed core Linux hardening operations:*

#### *Created three users with different privilege levels.*

#### *Set up a cron job for automation and persistence awareness.*

#### *Changed SSH's default port to reduce automated attacks.*

#### *Disabled root login to block direct privileged access.*

#### *Installed and configured UFW to limit exposed services.*

#### *Tested SSH connectivity from Kali to confirm the new port works and root login is blocked.*

#### *Verified service behavior, permissions, and port activity.*

#### 

<<<<<<< HEAD:03-ssh-hardening-&-securing-remote-access.md
#### *These techniques are essential for securing real Linux servers and understanding how attackers attempt to bypass weak configurations.*
=======

###### What I learned:



* *SSH refuses authentication attempts for the root user.*
* *Only normal users can log in and they must escalate with "sudo".*
* *"sshd -T" shows the effective live configuration, not just the file.*



###### Screenshots :



[ssh_config rootLogin-yes](/screenshots/permitrootlogin-yes.png)



[ssh_config rootLogin-no](/screenshots/permitrootlogin-no.png)






TESTING THE NEW CONFIGURATION



Tests performed:



Verified the new SSH port is active:

***sudo systemctl status ssh***



Verified port ***2222*** is **listening**.



Attempted SSH connection using the new port from Kali machine:

**ssh -p 2222 username@IP**



Attempted to log in directly as root (should fail):

**ssh root@IP**



###### Observations:



1. ***SSH now rejects root logins.***

2. ***Only the new port accepts connections.***

3. ***Attackers scanning port 22 will no longer detect the service immediately.***







###### Screenshots :



[ssh-port-test-success](/screenshots/sshconnectionkali-ubuntu-success.png)



[ssh-port-test-failed](/screenshots/sshconnectionkali-ubuntu-failed.png)






###### **DAY 4 SUMMARY**



***Today, I successfully applied foundational SSH security measures:***



1. ***Installed and enabled the OpenSSH server.***

2. ***Changed the default SSH port from 22 to 2222.***

3. ***Disabled root login to block direct privileged access attempts.***

4. ***Verified service behavior using network and configuration commands.***

5. ***Learned how attackers enumerate port 22 and how blue teams reduce exposure.***



***This hardening significantly improves the security posture of the machine by reducing brute-force attempts and removing direct access to the root account.***
>>>>>>> e10bb142ad112a3935f7424e8c242a60b690728e:03-ssh-hardening-&-securing-remote-access/03-ssh-hardening-&-securing-remote-access.md

