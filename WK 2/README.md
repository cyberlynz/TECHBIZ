# Week 2 — Linux Fundamentals, Networking Tools & Vulnerable Lab Setup

<p align="center">
  <img src="https://img.shields.io/badge/Week-02-404040?style=flat-square&labelColor=238F89" alt="Week 2" />
  <img src="https://img.shields.io/badge/Focus-Linux%20%26%20Networking-404040?style=flat-square&labelColor=C00000" alt="Linux and networking" />
  <img src="https://img.shields.io/badge/Lab-Metasploitable%202-E87500?style=flat-square&labelColor=000000" alt="Metasploitable 2 lab" />
</p>

During Week 2 at TechBiz Academy, I practised Linux commands and permissions, used basic networking tools, worked through subnetting, and prepared an isolated vulnerable lab with Metasploitable 2. I documented the commands I ran, what I observed and the results from each task.

---

## Task folders

| Task | Topic | Areas covered |
|---|---|---|
| [**Task 1 →**](Task%201/README.md) | Linux Fundamentals | Linux directories, 20 basic commands, file creation, permissions, ownership, sudo and apt |
| [**Task 2 →**](Task%202/README.md) | Networking Tools & Subnetting Basics | ping, traceroute, nslookup, dig, netstat, CIDR notation and TryHackMe DNS/HTTP rooms |
| [**Task 3 →**](Task%203/README.md) | Vulnerable Lab Setup — Metasploitable 2 | VirtualBox, NAT Network, Kali-to-target connectivity and the target's web applications |

## Main lab details

| Item | Recorded value |
|---|---|
| Attacker machine | Kali Linux |
| Target machine | Metasploitable 2 |
| Virtualization | Oracle VirtualBox 7.2.16 |
| Shared network | NatNetwork |
| Network prefix | 10.0.0.0/24 |
| Kali address | 10.0.0.3 |
| Metasploitable 2 address | 10.0.0.6 |

## What I practised

- Navigating the Linux file system and working with files from the terminal.
- Reading and changing permissions with \`ls -l\` and \`chmod\`.
- Checking ownership and using \`sudo\` when administrator privileges are required.
- Updating packages, installing or upgrading Nmap with \`apt\`, and checking its version.
- Checking host reachability, tracing network paths and looking up DNS records.
- Reviewing listening ports and active network sockets.
- Calculating usable host addresses from CIDR notation.
- Building a private lab network and confirming that Kali could reach Metasploitable 2.

## Screenshot evidence

I placed the screenshots extracted from my Week 2 task reports in the matching task folders and embedded them in the task write-ups.

**[Open the Week 2 screenshot gallery →](Screenshots/README.md)**

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
