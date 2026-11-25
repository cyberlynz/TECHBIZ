# Week 6 — Metasploit, Password Cracking & Capstone Penetration Test

<p align="center">
  <img src="https://img.shields.io/badge/Week-06-404040?style=flat-square&labelColor=238F89" alt="Week 6" />
  <img src="https://img.shields.io/badge/Focus-Exploitation%20%26%20Credential%20Security-404040?style=flat-square&labelColor=C00000" alt="Exploitation and credential security" />
  <img src="https://img.shields.io/badge/Labs-Metasploitable%202%20%7C%20TryHackMe-404040?style=flat-square&labelColor=000000" alt="Metasploitable 2 and TryHackMe labs" />
</p>

This week, I worked on three tasks covering exploitation with Metasploit, password cracking with John the Ripper, and a full penetration test on TryHackMe's Basic Pentesting machine. I recorded the commands I used, what happened during each test, the screenshots from the practical work and the main things I learned.

---

## Task folders

| Task | Topic | Areas covered |
|---|---|---|
| [**Task 1 →**](Task%201/README.md) | Metasploit — Exploit a Vulnerable Machine | Searching for the vsftpd 2.3.4 backdoor module, configuring RHOSTS and LHOST, opening a Meterpreter session, confirming root access, and completing the Metasploit Introduction and Blue rooms |
| [**Task 2 →**](Task%202/README.md) | Password Cracking — Hashes (John / Hashcat) | MD5, SHA-1, SHA-256 and bcrypt, dictionary attacks, creating a sample hash, cracking it with John the Ripper and completing TryHackMe Crack the Hash |
| [**Task 3 →**](Task%203/README.md) | Capstone — Full Pentest & Professional Report | Nmap scanning, Gobuster directory discovery, exposed development notes, anonymous SMB enumeration, Hydra SSH password testing, and a second foothold through Kay's SSH key |

## Main lab details

| Task | Recorded setup |
|---|---|
| Task 1 — Metasploit | Kali Linux (10.0.0.3) attacking Metasploitable 2 (10.0.0.6) over the VirtualBox NAT Network (NatNetwork) |
| Task 2 — Password cracking | Kali Linux terminal using /usr/share/wordlists/rockyou.txt; TryHackMe AttackBox for Crack the Hash |
| Task 3 — Capstone | TryHackMe Basic Pentesting room, target 10.128.157.162, Kali Linux connected to the TryHackMe lab |

## What I worked on

- Used the Metasploit workflow of search, use, show options, set and exploit against the vsftpd 2.3.4 backdoor on Metasploitable 2.
- Confirmed the resulting session by entering a system shell and checking whoami and id.
- Generated a sample MD5 hash and used John the Ripper with rockyou.txt to recover the original plaintext password.
- Compared password hashing with encryption and reviewed why slow, salted password-hashing algorithms are preferred.
- Followed the reconnaissance, scanning, enumeration and exploitation stages on the Basic Pentesting target, recording five findings and their severity ratings.

## Results from my tasks

| Task | Main result |
|---|---|
| Task 1 — Metasploit | The exploit/unix/ftp/vsftpd_234_backdoor module opened a session against Metasploitable 2. After using shell, whoami returned root and id returned uid=0(root) gid=0(root). |
| Task 2 — Password cracking | John the Ripper cracked the sample Raw-MD5 hash in under 3 seconds and recovered col12345, matching the plaintext used to create the hash. |
| Task 3 — Capstone | Recorded five findings: one Low scanning finding, two enumeration findings rated Medium and Low, and two High exploitation findings. The engagement reached working SSH access as both jan and kay. |

## Screenshot gallery

I saved the screenshots in the folders for each task. I also added them to the write-ups beside the steps and results they support.

**[Open the Week 6 screenshot gallery →](Screenshots/README.md)**

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
