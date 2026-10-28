# 🛡️ Cybersecurity & Penetration Testing Portfolio

Welcome to my cybersecurity repository! This space serves as a documentation of my practical learning journey, penetration testing methodologies, and step-by-step Capture The Flag (CTF) write-ups. 

My approach focuses on manual enumeration, understanding the underlying vulnerabilities, and applying real-world troubleshooting and OPSEC principles (such as redacting sensitive credentials and flags).

## 📂 TryHackMe Write-ups

Here are my detailed write-ups for the machines I have successfully compromised. They are listed in chronological order to reflect my learning curve:

1. [Basic Pentesting](./01-Basic-Pentesting.md) - *Web Enum, Hydra SSH Brute-force, SUID Exploitation*
2. [Pickle Rick](./02-Pickle-Rick.md) - *Source Code Inspection, Command Injection, Filter Bypass*
3. [RootMe](./03-RootMe.md) - *File Upload Bypass (.phtml), Reverse Shells, Python SUID*
4. [Bounty Hacker](./04-Bounty-Hacker.md) - *FTP Anonymous Access, SSH Brute-force, Tar Sudo Exploitation*

## ⚙️ Core Methodology

When approaching a new target, I follow a strict penetration testing lifecycle:
- **Reconnaissance & Enumeration:** Nmap, Gobuster, Wappalyzer, Enum4linux.
- **Initial Access:** Identifying misconfigurations, weak credentials, and web vulnerabilities.
- **Privilege Escalation:** Sudo misconfigurations, SUID binaries (GTFOBins), and internal access token harvesting.
- **OPSEC & Troubleshooting:** Modifying payloads to bypass filters, managing connection timeouts, and redacting sensitive data.

*Disclaimer: All write-ups and scripts in this repository are for educational purposes and apply only to authorized CTF environments.*
