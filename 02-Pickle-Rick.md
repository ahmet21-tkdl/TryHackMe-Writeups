# TryHackMe: Pickle Rick Write-up

**Goal:** Rick and Morty themed room. Find 3 ingredients (flags) using web enumeration and command injection.

## Reconnaissance
Checked the source code (`Ctrl+U`) of the main web page right away. Found a developer comment containing a username: `R1ckRul3s`.

Then ran Gobuster to find hidden files, adding php and txt extensions:
```bash
gobuster dir -u http://<TARGET_IP> -w /usr/share/wordlists/dirb/common.txt -x php,txt
```
Found `robots.txt`. Checked it in the browser and saw a strange string: `[REDACTED]`. Used this as the password for `R1ckRul3s` on `login.php`.

## Command Injection & Filter Bypass
Logging in brought me to a command panel (`portal.php`). I tried typing `ls -la` and saw the first flag file (`Sup3rS3cretPickl3Ingred.txt`). 

When I tried to read it with `cat`, the app blocked me! The creator had blacklisted `cat`.
To bypass this, I used `tac` (which prints files in reverse):
```bash
tac Sup3rS3cretPickl3Ingred.txt
```
It worked and showed the first ingredient: THM{[REDACTED]}. Used the same trick to find the second ingredient: THM{[REDACTED]}.

## Privilege Escalation
Checked my sudo rights via the web shell:
```bash
sudo -l
```
Turned out `www-data` had `(ALL) NOPASSWD: ALL` access! No complex exploit needed. I just read the root flag directly:
```bash
sudo tac /root/3rd.txt
```

## Lessons Learned & Mitigation 
- **Input Sanitization:** The command injection vulnerability on `portal.php` happened because there was no input sanitization. Blacklisting commands like `cat` is easily bypassed. User input must be strictly validated.
- **Sudo Permissions:** The `www-data` user should never have `(ALL) NOPASSWD: ALL` rights. This configuration flaw turned a basic web vulnerability into a full server compromise.
