# DAY 4 — LINUX HARDENING (Ubuntu Server)

## VM: Ubuntu Server — IP 192.168.187.70


# CREATING 3 USERS WITH DIFFERENT PRIVILEGE LEVELS

## COMMANDS EXECUTED:
* sudo adduser user_basic
* sudo adduser user_advanced
* sudo adduser user_admin

* sudo usermod -aG sudo user_advanced
* sudo usermod -aG sudo user_admin

## WHAT I LEARNED:
* How Linux handles users and privilege levels.
* Difference between a standard user and a sudo user.
* Why least privilege is important in cybersecurity.
* How companies separate user roles to reduce risk.
* How attackers exploit weak user configurations during privilege escalation.

## WHAT HAPPENS TECHNICALLY:
* "adduser" creates a user, a home directory, and assigns permissions.
* "usermod -aG sudo" adds a user into the sudo group.
* Members of the sudo group can run administrative commands.
* user_basic = no admin privileges.
* user_advanced and user_admin = elevated privileges.

## SCREENSHOTS:
[passwd-tail](/03-ssh-hardening-&-securing-remote-access/screenshots/passwd-tail.png)  
[groups-user-advanced](/03-ssh-hardening-&-securing-remote-access/screenshots/groups-user-advanced.png)  
[groups-user-admin](/03-ssh-hardening-&-securing-remote-access/screenshots/groups-user-admin.png)

Created three different privilege-level users:
* user_basic: no administrative privileges
* user_advanced: sudo privileges
* user_admin: sudo privileges

This demonstrates the principle of least privilege and shows how Linux separates normal users from administrators.



# CREATING A CRON JOB THAT RUNS EVERY 2 MINUTES

## COMMAND:
* crontab -e

## At the bottom of the file, added:
*/2 * * * * echo "Cron working" >> /home/user_basic/cron-test.txt

## WHAT I LEARNED:
* How Linux automation works.
* How cron scheduling syntax operates.
* Why automation is essential in real systems.
* Cybersecurity relevance: attackers use cron for persistence after compromise.

## WHAT HAPPENS TECHNICALLY:
* Every 2 minutes, the cron daemon executes the command.
* It writes "Cron working" into the cron-test.txt file.
* If the file does not exist, cron automatically creates it.
* This confirms the automation is functioning correctly.

## SCREENSHOTS:
[crontab-entry](/03-ssh-hardening-&-securing-remote-access/screenshots/crontab-entry.png)  
[cron-test-file](/03-ssh-hardening-&-securing-remote-access/screenshots/cron-test-file.png)

Configured a cron job to run every 2 minutes.  
It writes text to a file to verify that automation is active.  
Cron is heavily used for legitimate system tasks and is also abused in attacker persistence techniques.



# CHANGING THE DEFAULT SSH PORT

## CONFIGURATION EDITED:
* sudo nano /etc/ssh/sshd_config

## Changed:
* #Port 22  
To:  
* Port 2222

## Restarted SSH:
* sudo systemctl restart ssh

## Verification:
* ss -tulpn | grep 2222

## WHAT I LEARNED:
* How SSH configuration works.
* Why port 22 is constantly scanned by attackers.
* How changing ports reduces noise and automated brute-force attempts.
* How blue teams harden remote access by modifying service ports.

## WHAT HAPPENS TECHNICALLY:
* Ubuntu stops listening on port 22.
* SSH daemon rebinds to port 2222.
* Automated bots scanning port 22 no longer find the service.

## SCREENSHOTS:
[sshd-config-port2222](/03-ssh-hardening-&-securing-remote-access/screenshots/sshd-config-port2222.png)  
[ssh-port-listening](/03-ssh-hardening-&-securing-remote-access/screenshots/ssh-port-listening.png)

Changed the SSH service port from 22 to 2222.  
Restarted SSH and confirmed the new port is active.  
This reduces automated attack attempts and forces attackers to first discover the new port.



# SSH CONNECTION TEST FROM KALI MACHINE

## Successful connection:
ssh -p 2222 user_advanced@192.168.187.70  
(Worked because the correct port was specified.)

## Failed connection:
ssh root@192.168.187.70  
(Denied because root login is disabled AND because no port was specified.  
SSH defaults to port 22, which is now closed.)

## This confirms that:
* The new SSH port (2222) works correctly.
* Direct root login is fully blocked.
* Port 22 is no longer accepting connections.

## SCREENSHOTS:
[ssh-test-success](/03-ssh-hardening-&-securing-remote-access/screenshots/sshconnectionkali-ubuntu-success.png)  
[ssh-test-failed](/03-ssh-hardening-&-securing-remote-access/screenshots/sshconnectionkali-ubuntu-failed.png)



# DISABLING ROOT LOGIN OVER SSH

## EDITED FILE:
* sudo nano /etc/ssh/sshd_config

## Changed:
* PermitRootLogin yes  
To:  
* PermitRootLogin no

## Restart SSH:
* sudo systemctl restart ssh

## Verification:
* sshd -T | grep rootlogin  
Expected: permitrootlogin no

## WHAT I LEARNED:
* Why root login is extremely dangerous.
* How brute-force attacks always target "root" first.
* How preventing root SSH access reduces the attack surface.
* Why administrators use normal accounts + sudo instead.

## WHAT HAPPENS TECHNICALLY:
* SSH instantly rejects all login attempts using the root user.
* Attackers must now first compromise a normal account.
* Privilege escalation becomes significantly harder.

## SCREENSHOTS:
[permitrootlogin-no](/03-ssh-hardening-&-securing-remote-access/screenshots/permitrootlogin-no.png)  
[ssh-restart](/03-ssh-hardening-&-securing-remote-access/screenshots/ssh-restart.png)  
[rootlogin-verification](/03-ssh-hardening-&-securing-remote-access/screenshots/rootlogin-verification.png)

Disabled root login over SSH.  
This forces attackers to compromise a user account instead of directly attacking root.



# INSTALLING AND CONFIGURING UFW FIREWALL

## COMMANDS:
* sudo apt install ufw -y
* sudo ufw allow 2222/tcp
* sudo ufw enable
* sudo ufw status

## WHAT I LEARNED:
* Basic Linux firewall management.
* How firewalls control inbound network traffic.
* Why organizations restrict exposed services.
* How attackers use port scanning to find open doors.

## WHAT HAPPENS TECHNICALLY:
* UFW starts blocking all incoming connections except allowed ones.
* Only SSH on port 2222 is accessible.
* Every other port becomes protected.

## SCREENSHOTS:
[ufw-status](/03-ssh-hardening-&-securing-remote-access/screenshots/ufw-status.png)

Enabled the UFW firewall and allowed only port 2222 for SSH.  
This significantly reduces the system's attack surface.



# DAY 4 SUMMARY

Today, I completed core Linux hardening operations:

* Created three users with different privilege levels.
* Set up a cron job for automation and persistence awareness.
* Changed SSH's default port to reduce automated attacks.
* Disabled root login to block direct privileged access.
* Installed and configured UFW to limit exposed services.
* Tested SSH connectivity from Kali to confirm the new port works and root login is blocked.
* Verified service behavior, permissions, and port activity.

These techniques are essential for securing real Linux servers and understanding how attackers attempt to bypass weak configurations.
