# TryHackMe: Bounty Hacker Write-up

**Goal:** FTP enumeration, SSH brute-forcing, exploiting `tar` sudo rights.

## Reconnaissance & Troubleshooting
Started with Nmap, but it failed instantly: `Host seems down`. 
Realized my OpenVPN connection dropped (ping gave 100% packet loss). Restarted OpenVPN (`sudo openvpn ...`) and rebooted the THM machine to get a clean IP. 

Once stable, Nmap showed ports 21 (FTP), 22 (SSH), and 80 (HTTP) open.

## FTP & Idle Timeout Issues
Connected to FTP using `anonymous` (blank password):
```bash
ftp <TARGET_IP>
```
While looking around, the connection suddenly dropped with a `Not connected` error because of an idle timeout. Logged back in quickly and pulled the files (`task.txt` and `locks.txt`) to my local machine using `get`.

Inside `task.txt`, I found a user named `lin`. `locks.txt` had a list of passwords.

## Initial Access (Hydra)
Used Hydra to brute-force SSH with the user `lin` and the wordlist:
```bash
hydra -l lin -P locks.txt ssh://<TARGET_IP>
```
It found the password (`[REDACTED]`). Logged in via SSH and got the user flag: `THM{[REDACTED]}`.

## Privilege Escalation
Ran `sudo -l` and found I could run `/bin/tar` as root without a password. 

*Mistake:* At first, I ran the GTFOBins payload without `sudo`, so `whoami` still returned `lin`. 
*Fix:* Exited, added `sudo` to the front, and ran the checkpoint exploit:
```bash
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```
Checked `whoami` -> `root`. Went to `/root/root.txt` and finished the room: `THM{[REDACTED]}`.

## Lessons Learned & Mitigation
- **Anonymous FTP Access:** The initial foothold was gained because the FTP server allowed anonymous logins. This must be disabled in the FTP configuration file (e.g., `anonymous_enable=NO`).
- **Password Policy:** The user `lin` had a password that was easily crackable using a small, localized wordlist. Enforcing strong password policies prevents brute-force attacks.
- **Principle of Least Privilege:** Users should not be given `sudo` access to binaries like `tar`, which have built-in features to execute external commands. This misconfiguration allowed a standard user to easily spawn a root shell.
