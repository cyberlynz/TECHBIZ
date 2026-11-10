# Week 3 — Footprinting, Scanning & Enumeration

<p align="center">
  <img src="https://img.shields.io/badge/Week-03-404040?style=flat-square&labelColor=238F89" alt="Week 3" />
  <img src="https://img.shields.io/badge/Focus-Recon%20%7C%20Scanning%20%7C%20Enumeration-404040?style=flat-square&labelColor=C00000" alt="Recon scanning enumeration" />
  <img src="https://img.shields.io/badge/Lab-Metasploitable%202-E87500?style=flat-square&labelColor=000000" alt="Metasploitable 2" />
</p>

During Week 3 at TechBiz Academy, I moved from reconnaissance into active network scanning and service enumeration. The three tasks covered passive OSINT on a public domain, Nmap scanning in my isolated Metasploitable 2 lab, and follow-up enumeration of SMB, FTP and SSH services on the lab target.

---

## Task folders

| Task | Topic | Areas covered |
|---|---|---|
| [**Task 1 →**](Task%201/README.md) | Footprinting & OSINT (Passive Reconnaissance) | WHOIS, Google dorks, theHarvester, crt.sh, DNSDumpster, Wayback Machine, Shodan and four TryHackMe rooms |
| [**Task 2 →**](Task%202/README.md) | Network Scanning with Nmap | Host discovery, service/version detection, aggressive scanning, OS/script output and 23 open ports on Metasploitable 2 |
| [**Task 3 →**](Task%203/README.md) | Enumeration — Digging Deeper into Services | enum4linux, SMB shares, null-session access, password policy, FTP/SSH banner grabbing and two TryHackMe rooms |

## Lab details for Tasks 2 and 3

| Item | Recorded value |
|---|---|
| Attacker machine | Kali Linux (\`10.0.0.3\`) |
| Target machine | Metasploitable 2 (\`10.0.0.6\`) |
| Network | \`10.0.0.0/24\` |
| Main enumeration service | SMB on TCP ports 139 and 445 |

Task 1 was a separate passive OSINT exercise against \`techbiz-sec.com\`. It did not involve the lab target or direct scanning. Tasks 2 and 3 were active checks against my own Metasploitable 2 virtual machine.

## What I practised

- Collecting publicly available information about a domain without directly interacting with its systems.
- Reading WHOIS records and reviewing indexed pages, public documents, DNS records, certificate transparency records and archived pages.
- Comparing OSINT results from several sources and recording tool limitations.
- Discovering live hosts with \`nmap -sn\` and identifying service versions with \`nmap -sV\`.
- Reviewing the additional OS, script and traceroute details returned by \`nmap -A\`.
- Enumerating SMB workgroup information, user accounts, shares and password policy with \`enum4linux\`.
- Listing and checking an SMB share with \`smbclient\` and reading FTP/SSH banners with netcat.
- Separating observed results from unverified third-party data and recording the scope and authorisation boundaries of each task.

## Main results at a glance

| Task | Main recorded result |
|---|---|
| Task 1 | WHOIS, Google, theHarvester, DNSDumpster, crt.sh and Wayback Machine results contributed different pieces of the public footprint; Shodan returned no results for the searched domain. |
| Task 2 | Nmap found four live hosts on \`10.0.0.0/24\`; the service scan reported 23 open ports and 977 closed ports on \`10.0.0.6\`. |
| Task 3 | SMB allowed a null session; enum4linux found 35 accounts and five shares; the \`tmp\` share could be listed anonymously; FTP and SSH banners matched the Nmap version scan. |

## Screenshot gallery

The screenshots extracted from the three Week 3 reports are stored in each task's \`screenshots/\` folder and embedded in the corresponding write-up.

**[Open the Week 3 screenshot gallery →](Screenshots/README.md)**

---

<p align="center">
  <a href="../README.md">← Portfolio home</a>
  &nbsp;·&nbsp;
  <a href="Task%201/README.md">Task 1</a>
  &nbsp;·&nbsp;
  <a href="Task%202/README.md">Task 2</a>
  &nbsp;·&nbsp;
  <a href="Task%203/README.md">Task 3</a>
</p>
