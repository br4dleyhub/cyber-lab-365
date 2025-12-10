DAY 4 — LINUX HARDENING (Ubuntu Server)
VM: Ubuntu Server — IP 192.168.187.70
1. Creating 3 Users with Different Privilege Levels
Commands Executed
sudo adduser user_basic
sudo adduser user_advanced
sudo adduser user_admin

sudo usermod -aG sudo user_advanced
sudo usermod -aG sudo user_admin

What I Learned

How Linux handles users and privilege levels.

Difference between a standard user and a sudo user.

Why least privilege is important in cybersecurity.

How companies separate user roles to reduce risk.

How attackers exploit weak configurations during privilege escalation.

What Happens Technically

adduser creates the user + home directory.

usermod -aG sudo assigns sudo privileges.

user_basic = no administrative privileges.

user_advanced + user_admin = elevated privileges.

Screenshots

/etc/passwd tail output
groups user_advanced
groups user_admin

Summary

Created 3 users with different privilege levels to demonstrate the principle of least privilege and how Linux separates normal users from administrators.

2. Creating a Cron Job That Runs Every 2 Minutes
Cron Entry

In crontab -e, added:

*/2 * * * * echo "Cron working" >> /home/brad/cron-test.txt

What I Learned

How Linux automation works.

Cron scheduling syntax.

Why automation is essential in real systems.

How attackers abuse cron for persistence.

What Happens Technically

Cron daemon executes the command every 2 minutes.

Appends “Cron working” to the file.

If the file doesn’t exist, cron creates it.

Screenshots

Cron entry in crontab -e

Growing cron-test.txt after a few minutes

Summary

Configured a cron job that verifies automation works. Cron is widely used for both legitimate admin tasks and malicious persistence.

3. Changing the Default SSH Port
Edited File
sudo nano /etc/ssh/sshd_config

Changed

From:

#Port 22


To:

Port 2222

Restart SSH
sudo systemctl restart ssh

Verification
ss -tulpn | grep 2222

What I Learned

How SSH configuration works.

Why port 22 is targeted by automated scans.

How changing ports reduces brute-force attempts.

How blue teams modify and harden services.

What Happens Technically

Ubuntu stops listening on port 22.

SSH daemon binds to port 2222.

Attackers scanning port 22 no longer detect SSH.

Screenshots

sshd_config showing Port 2222

ss -tulpn | grep ssh showing SSH on 2222

Summary

SSH now runs on port 2222, reducing automated attacks.

4. SSH Connection Test from Kali Linux
Successful connection:
ssh -p 2222 user_advanced@192.168.187.70


✔️ Worked — correct username and correct port.

Failed connection:
ssh root@192.168.187.70


❌ Failed because:

root login is disabled

default port 22 is closed

This Confirms:

SSH on port 2222 is working.

Root login is fully blocked.

Port 22 is no longer open.

Screenshots

Success: ssh -p 2222 ...

Fail: ssh root@...

5. Disabling Root Login over SSH
Edited File
sudo nano /etc/ssh/sshd_config

Changed

From:

PermitRootLogin prohibit-password


To:

PermitRootLogin no

Restart
sudo systemctl restart ssh

Verification
sshd -T | grep rootlogin


Expected:

permitrootlogin no

What I Learned

Root login is dangerous because attackers always target "root".

Disabling it reduces brute-force risk.

Forces attackers to compromise a normal account first.

Administrators should always use a normal user + sudo.

Technical Effects

SSH rejects all root login attempts.

Privilege escalation becomes harder.

Screenshots

PermitRootLogin no

Restart command

Verification output

6. Installing and Configuring UFW Firewall
Commands
sudo apt install ufw -y
sudo ufw allow 2222/tcp
sudo ufw enable
sudo ufw status

What I Learned

How firewalls control inbound traffic.

Why organizations restrict open ports.

How attackers scan for vulnerabilities.

What Happens Technically

UFW blocks all ports except allowed ones.

Only SSH on port 2222 is accessible.

Every other port is protected.

Screenshots

ufw status showing port 2222 allowed

Summary

Firewall configured to allow only port 2222, reducing attack surface.

DAY 4 SUMMARY

Today I completed core Linux hardening operations:

Created users with different privilege levels.

Configured a cron job to run every 2 minutes.

Changed SSH default port to 2222.

Disabled root login.

Installed and configured UFW firewall.

Successfully tested SSH connections (success + denied).

Verified security configurations using system commands.

These techniques are essential for securing real Linux servers and understanding how attackers attempt to bypass weak configurations.
