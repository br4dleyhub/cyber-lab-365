## DAY 4

## SSH HARDENING \& SECURING REMOTE ACCESS



##### ***OBJECTIVE***

##### <i>Strengthen the security of the Ubuntu machine by modifying SSH settings, reducing the attack surface, and understanding how attackers target remote access services.</i>



###### INSTALLING AND STARTING THE SSH SERVICE



###### Commands executed:

* sudo apt install openssh-server -y
* sudo systemctl enable ssh
* sudo systemctl start ssh
* sudo systemctl status ssh



###### Result:

OpenSSH was installed, enabled at startup, and is now active on the system.



###### What I learned:



* *SSH is not always installed by default.*
* *"systemctl enable" makes the service persistent across reboots.*
* *"systemctl status ssh" shows whether the daemon is running.*



###### Screenshots :



ssh-installation.png



ssh-service-status.png









##### CHANGING THE DEFAULT SSH PORT



###### Reason:

*Port 22 is the most scanned port on the internet.*

*Changing it reduces automated brute-force attempts and forces attackers to detect the service before attacking.*



###### Configuration file edited:

sudo nano /etc/ssh/sshd\_config



Changed:

***#Port 22***

To:

***Port 2222***



###### Restarted SSH:

sudo systemctl restart ssh



###### Verification:

ss -tulpn | grep 2222



###### What I learned:



* *SSH binds to the port defined in sshd\_config.*
* *Attackers constantly scan port 22 for weak credentials.*
* *Changing the port does not replace security, but it reduces noise significantly.*
* *"ss -tulpn" displays all active listening ports.*



###### Screenshots :

###### 

sshd\_config-port-change.png



ssh-new-port.png







###### DISABLING ROOT LOGIN OVER SSH



###### Reason:

*Root is the #1 target during brute-force attacks.*

*Disabling root login forces attackers to compromise a normal user first, strengthening the overall security posture.*



###### Configuration file edited:

sudo nano /etc/ssh/sshd\_config



Changed:

***PermitRootLogin prohibit-password***

To:

***PermitRootLogin no***



###### Restarted SSH:

sudo systemctl restart ssh



Verification:

**sshd -T | grep rootlogin**

Expected output:

**permitrootlogin no**



###### What I learned:



* *SSH refuses authentication attempts for the root user.*
* *Only normal users can log in and they must escalate with "sudo".*
* *"sshd -T" shows the effective live configuration, not just the file.*



###### Screenshots :



sshd\_config-rootlogin.png



permitrootlogin-verification.png







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



ssh-port-test.png



ssh-root-login-failed.png







###### **DAY 4 SUMMARY**



***Today, I successfully applied foundational SSH security measures:***



1. ***Installed and enabled the OpenSSH server.***

2. ***Changed the default SSH port from 22 to 2222.***

3. ***Disabled root login to block direct privileged access attempts.***

4. ***Verified service behavior using network and configuration commands.***

5. ***Learned how attackers enumerate port 22 and how blue teams reduce exposure.***



***This hardening significantly improves the security posture of the machine by reducing brute-force attempts and removing direct access to the root account.***

