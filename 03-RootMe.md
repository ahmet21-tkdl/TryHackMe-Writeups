# TryHackMe: RootMe Write-up

**Goal:** Upload a backdoor, get a reverse shell, find SUID binaries for root escalation.

## Web Reconnaissance
Ran Gobuster on port 80:
```bash
gobuster dir -u http://<TARGET_IP> -w /usr/share/wordlists/dirb/common.txt
```
Found `/panel` (file upload page) and `/uploads` (where files go).

## Gaining Access (File Upload Bypass)
I prepared a standard PHP reverse shell script, setting my VPN IP and port 4444. 
When I tried uploading `shell.php`, the server threw an error blocking `.php` extensions. 

To bypass this, I simply renamed the file to `shell.phtml`. The server accepted it!
I started a Netcat listener on my machine:
```bash
nc -lvnp 4444
```
Then clicked my uploaded file in `/uploads/shell.phtml`. Boom, got a reverse shell as `www-data`. Read the user flag: `THM{[REDACTED]}`.

## Privilege Escalation (SUID Python)
To get root, I searched the system for files with the SUID bit set:
```bash
find / -type f -user root -perm -4000 2>/dev/null
```
Noticed `/usr/bin/python` had SUID permissions, which is a huge misconfiguration. Checked GTFOBins and ran this to pop a root shell:
```bash
python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```
Typed `whoami`, saw `root`, and grabbed the final flag from `/root/root.txt`: `THM{[REDACTED]}`.

## Lessons Learned & Mitigation
- **File Upload Restrictions:** Blacklisting specific extensions like `.php` is not enough to secure a file upload feature, as attackers can easily bypass it using alternatives like `.phtml`. Implement strict whitelisting (e.g., allowing only `.jpg`, `.png`) and disable script execution in the upload directory.
- **SUID Misconfigurations:** System binaries like `python` should never have the SUID bit set, as they can easily spawn shells and execute commands. This flaw directly allowed a low-privileged web user to escalate to root.
