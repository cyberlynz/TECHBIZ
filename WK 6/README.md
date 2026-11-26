# Week 6 — Metasploit, Password Cracking and Capstone Penetration Test

Week 6 brought together the work I had been practising in the earlier weeks. I moved from learning how to identify and understand vulnerabilities to testing a vulnerable service, working with password hashes, and completing a full penetration test on a lab machine.

This folder contains the three tasks I completed during the week. Each practical task is documented in my own words, with the commands I used, what I observed, and screenshots placed beside the steps they support. The capstone is kept as a professional penetration-testing report, with the findings, evidence, impact and remediation recommendations presented separately.

## Task folders

| Task | Focus | What I covered |
|---|---|---|
| [Task 1 — Metasploit: Exploiting a Vulnerable Machine](Task%201/README.md) | Metasploit Framework | Searching for a matching module, configuring the target and callback addresses, running the exploit and confirming the access gained. |
| [Task 2 — Password Cracking: Understanding and Testing Hashes](Task%202/README.md) | John the Ripper | Generating a sample hash, identifying the MD5 format, testing it with the RockYou wordlist and reviewing safer password storage. |
| [Task 3 — Capstone: Full Penetration Test and Professional Report](Task%203/README.md) | Full penetration test | Reconnaissance, scanning, enumeration, exploitation, evidence collection, risk ratings and remediation. |

## Main lab details

| Item | Details |
|---|---|
| Attacking system | Kali Linux |
| Vulnerable practice machines | Metasploitable 2 and the assigned Basic Pentesting capstone target |
| Main tools | Metasploit, Nmap, Gobuster, enum4linux, Hydra, John the Ripper and SSH |
| Evidence | Task-specific screenshots stored in each task's `screenshots` folder |

## What I worked on

**Metasploit:** I used the vulnerable FTP service on Metasploitable 2 to practise the exploit workflow. I checked the service version, searched for the matching module, supplied the required options and verified the resulting session in the target shell.

**Password hashes:** I created a sample MD5 hash and used John the Ripper to test it with a wordlist. I recorded what the output meant and why MD5 should not be used for password storage.

**Capstone:** I brought the earlier skills together in a structured assessment. I gathered information about the target, identified exposed services and directories, enumerated SMB users, tested authentication and documented the access I was able to obtain. The report explains the security impact of each finding and gives remediation recommendations.

## Results from my tasks

The Metasploit exercise demonstrated how a vulnerable service could lead to root-level command execution. The password-cracking exercise showed how a weak password stored as a fast hash could be recovered with a common wordlist. The capstone identified multiple weaknesses across exposed services, information disclosure and authentication. The individual task reports contain the supporting evidence and detailed explanations.

## Repository layout

```text
WK 6/
├── README.md
├── Task 1/
│   ├── README.md
│   └── screenshots/
├── Task 2/
│   ├── README.md
│   └── screenshots/
├── Task 3/
│   ├── README.md
│   └── screenshots/
└── Screenshots/
    └── README.md
```

The overview is kept as an index. The screenshots are included in the task documents at the relevant points rather than being added as decoration to this page.
