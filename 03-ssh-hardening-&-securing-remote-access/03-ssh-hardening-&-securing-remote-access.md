# DAY 4 — SSH HARDENING & SECURING REMOTE ACCESS

## INSTALLING OPENSSH SERVER

### Commands executed:
* sudo apt update
* sudo apt install openssh-server
* sudo systemctl status ssh

### What I learned:
* SSH is the secure protocol used for remote administration.
* The sshd daemon handles authentication + remote connections.
* Checking the service status ensures SSH is running correctly.

### Screenshots:
[ssh-service-running](/03-ssh-hardening-&-securing-remote-access/screenshots/ssh-service-running.png)



# CHANGING THE DEFAULT SSH PORT (22 → 2222)

### Configuration edited:
* sudo nano /etc/ssh/sshd_config

### Changed:
* #Port 22  
to  
* Port 2222

### Restarted SSH:
* sudo systemctl restart ssh

### Verification:
* sudo systemctl status ssh
* ss -tulpn | grep ssh

### What I learned:
* Port 22 is constantly scanned by bots and brute-forcers.
* Changing the port reduces automated attacks and noise.
* SSH daemon rebinds to the new port after restart.

### What happens technically:
* SSH stops listening on port 22.
* It now listens only on port 2222.
* Attackers scanning the default port see nothing.

### Screenshots:
[ssh-port-change-config](/03-ssh-hardening-&-securing-remote-access/screenshots/ssh-port-config.png)  
[ssh-port-change-status](/03-ssh-hardening-&-securing-remote-access/screenshots/ssh-port-status.png)



# DISABLING ROOT LOGIN OVER SSH

### Edited file:
* sudo nano /etc/ssh/sshd_config

### Changed:
* PermitRootLogin prohibit-password  
to  
* PermitRootLogin no

### Restart:
* sudo systemctl restart ssh

### Verification:
* sshd -T | grep rootlogin  
Expected output: `permitrootlogin no`

### What I learned:
* Root login is a top attack target.
* Blocking it removes direct privileged access.
* Admins should always log in with a normal user + sudo.

### What happens technically:
* SSH instantly rejects all root login attempts.
* Attackers must compromise a normal user first.
* Privilege escalation becomes much harder.

### Screenshots:
[permitrootlogin-before](/03-ssh-hardening-&-securing-remote-access/screenshots/permitrootlogin-yes.png)  
[permitrootlogin-after](/03-ssh-hardening-&-securing-remote-access/screenshots/permitrootlogin-no.png)



# TESTING SSH CONNECTIONS FROM KALI

### Successful connection:
* ssh -p 2222 user_advanced@192.168.187.70  
(Worked because I used the correct port.)

### Failed connection:
* ssh root@192.168.187.70  
(Failed because root login is disabled AND it defaults to port 22, which is closed.)

### What this confirms:
* Port 2222 is active and working.
* Root login is fully blocked.
* Port 22 is no longer accepting connections.

### Screenshots:
[ssh-test-success](/03-ssh-hardening-&-securing-remote-access/screenshots/sshconnectionkali-ubuntu-success.png)  
[ssh-test-failed](/03-ssh-hardening-&-securing-remote-access/screenshots/sshconnectionkali-ubuntu-failed.png)



# INSTALLING & CONFIGURING UFW FIREWALL

### Commands:
* sudo apt install ufw -y
* sudo ufw allow 2222/tcp
* sudo ufw enable
* sudo ufw status

### What I learned:
* UFW manages inbound traffic easily.
* Only allowed ports remain reachable.
* Firewalls reduce exposed attack surfaces.

### What happens technically:
* UFW blocks ALL incoming traffic except allowed ports.
* SSH is allowed ONLY on 2222.
* Everything else becomes protected.

### Screenshots:
[ufw-status](/03-ssh-hardening-&-securing-remote-access/screenshots/ufw-status.png)



# PERSONAL TESTS — SSH CONNECTIONS FROM MY KALI MACHINE

### 1. Successful SSH login (correct port)
* ssh -p 2222 user_advanced@192.168.187.70  

### 2. Failed SSH login (root + wrong port)
* ssh root@192.168.187.70  
→ Denied because:
  - Root login = disabled  
  - Port 22 = closed  

### What I learned from the test:
* Always specify the port after changing SSH default settings.
* Root login should NEVER be used for security reasons.
* Attackers scanning port 22 now detect nothing.



# DAY 4 SUMMARY

### Today, I successfully applied key SSH hardening techniques:
* Installed and configured the OpenSSH server.
* Changed SSH port from 22 → 2222.
* Disabled root login for stronger security.
* Tested SSH access from Kali.
* Verified firewall, port behavior, and SSH configuration.
* Learned how attackers scan port 22 and why teams hide SSH behind custom ports.

### Overall:
**These changes significantly reduce brute-force attempts and protect privileged access.**
