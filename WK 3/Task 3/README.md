# Week 3, Task 3 — Enumeration: Digging Deeper into Services

<p align="center">
  <img src="https://img.shields.io/badge/Task-03-404040?style=flat-square&labelColor=6366F1" alt="Task 3" />
  <img src="https://img.shields.io/badge/Focus-Service%20Enumeration-404040?style=flat-square&labelColor=C00000" alt="Service enumeration" />
  <img src="https://img.shields.io/badge/Target-SMB%20%7C%20FTP%20%7C%20SSH-404040?style=flat-square&labelColor=238F89" alt="SMB FTP SSH" />
</p>

**Internship:** Ethical Hacking Internship, TechBiz Academy  
**Author:** Collins Okike  
**Target:** Metasploitable 2 (\`10.0.0.6\`)

## 1. Introduction

Enumeration follows scanning. In Week 3, Task 2, I used Nmap to identify open ports and services. In this task I queried selected services for more detail, including software versions, SMB shares, usernames and password-policy information.

Enumeration is active because it sends requests directly to the target, where those requests may be logged. I carried it out only against my own Metasploitable 2 lab machine. In a real assessment, I would need permission before performing these checks.

## 2. Lab setup and target services

| Machine | Name in the report | Address |
|---|---|---|
| Attacker | Kali Linux (\`kali-linux\`) | \`10.0.0.3\` |
| Target | Metasploitable 2 (\`Metasploitable2\`) | \`10.0.0.6\` |

The main service selected for enumeration was SMB on ports 139 and 445. SMB is used for file and printer sharing; port 139 is the older NetBIOS session service, while port 445 carries SMB directly over TCP. Samba is the open-source SMB implementation running on the Linux target. I also checked FTP and SSH banners because the task called for them.

I started with the Nmap version scan already recorded in Task 2:

\`\`\`bash
nmap -sV 10.0.0.6
\`\`\`

The scan showed 23 open ports and 977 closed ports. The relevant services were:

| Port | Service | Version reported by Nmap |
|---|---|---|
| \`21/tcp\` | FTP | vsftpd 2.3.4 |
| \`22/tcp\` | SSH | OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0) |
| \`139/tcp\` | netbios-ssn | Samba smbd 3.X–4.X (WORKGROUP) |
| \`445/tcp\` | netbios-ssn | Samba smbd 3.X–4.X (WORKGROUP) |

Nmap gave a version range for Samba, so I used enum4linux to try to identify the exact version.

![Nmap service-version scan of 10.0.0.6 from Task 2](screenshots/01-nmap-service-version-scan-reference.jpg)

## 3. SMB enumeration with enum4linux

I ran:

\`\`\`bash
enum4linux -a 10.0.0.6
\`\`\`

The \`-a\` option runs the available checks together. In this task I used it to query the workgroup and NetBIOS information, OS information, null-session access, users, shares, groups and password policy.

![enum4linux part 1: target information, workgroup, NetBIOS names and session check](screenshots/02-enum4linux-target-workgroup-and-session.jpg)

![enum4linux part 2: Samba OS information and start of the users list](screenshots/03-enum4linux-samba-version-and-user-list.jpg)

![enum4linux part 3: remaining users list](screenshots/04-enum4linux-rest-of-user-list.jpg)

![enum4linux part 4: share enumeration and share mapping](screenshots/05-enum4linux-share-enumeration-and-mapping.jpg)

![enum4linux part 5: password policy information](screenshots/06-enum4linux-password-policy.jpg)

![enum4linux part 6: RID cycling and end of the run](screenshots/07-enum4linux-rid-cycling.jpg)

### Results recorded

| Item | Result |
|---|---|
| Workgroup / domain | \`WORKGROUP\` |
| NetBIOS machine name | \`METASPLOITABLE\` |
| Null session | Allowed; enum4linux reported a session using an empty username and password |
| Samba version / OS | Samba \`3.0.20-Debian\`, obtained from \`srvinfo\` |
| User accounts | 35 accounts through RPC; examples included \`msfadmin\`, \`user\`, \`service\`, \`root\`, \`postgres\`, \`mysql\`, \`ftp\`, \`proftpd\`, \`sshd\`, \`telnetd\` and \`tomcat55\`; RID cycling also showed accounts such as Administrator, nobody, daemon, bin and sys |
| Shares | \`print$\` (Printer Drivers), \`tmp\` (comment: \`oh noes!\`), \`opt\`, \`IPC$\` and \`ADMIN$\` |
| Share mapping | \`tmp\` showed Mapping: OK and Listing: OK; \`print$\`, \`opt\` and \`ADMIN$\` were denied |
| Password policy | Minimum length 5; no password history; maximum age not set; complexity off; no account-lockout threshold. A partial \`rpcclient\` result showed minimum length 0. |

The most significant result was that the server allowed a null session. I did not need a username or password to retrieve the user list, shares and password-policy information. The discovered usernames could help an attacker avoid guessing account names. The weak password settings recorded in the report increase the risk of password guessing. The \`tmp\` share was also accessible without a password, so I checked it separately with smbclient.

The reported Samba version, 3.0.20-Debian, is old and falls within the range associated with CVE-2007-2447 (the username map script issue). I did **not** test or exploit that issue in this task; I recorded the version as a weakness to report.

## 4. Checking shares with smbclient

I used smbclient to list the available shares and then connect to \`tmp\` without a password:

\`\`\`bash
smbclient -L //10.0.0.6 -N
smbclient //10.0.0.6/tmp -N
\`\`\`

\`-L\` lists the shares on the server, and \`-N\` tells smbclient not to prompt for a password. After connecting to \`tmp\`, I used \`ls\` to list its contents.

![smbclient share listing on 10.0.0.6](screenshots/08-smbclient-share-listing.jpg)

![smbclient connected to the tmp share and listing files](screenshots/09-smbclient-tmp-share-files.jpg)

### Results recorded

- The shares listed were \`print$\`, \`tmp\`, \`opt\`, \`IPC$\` and \`ADMIN$\`.
- The workgroup was \`WORKGROUP\`, and the master was \`METASPLOITABLE\`.
- Anonymous login succeeded when listing shares and when connecting to \`tmp\`.
- The \`tmp\` listing showed \`.ICE-unix\`, \`.X11-unix\`, \`.X0-lock\` and \`4622.jsvc_up\`.
- The output also showed the share's available space.

The smbclient results agreed with enum4linux: \`tmp\` was the share I could map and list. The listed items appeared to be temporary service files. I did not see sensitive content, and I did not upload, download or change any files. Even so, an anonymously accessible share could expose private files or be misused to store files, so it should not be left open without a need.

## 5. Banner grabbing with netcat

A service may send a text banner when a client connects. I used netcat to read the FTP and SSH banners and compare them with the versions reported by Nmap.

### 5.1 FTP — port 21

\`\`\`bash
nc 10.0.0.6 21
\`\`\`

The banner was:

\`\`\`text
220 (vsFTPd 2.3.4)
\`\`\`

This identified the service as vsftpd 2.3.4.

![FTP banner grabbed with netcat on port 21](screenshots/10-netcat-ftp-banner.jpg)

### 5.2 SSH — port 22

\`\`\`bash
nc 10.0.0.6 22
\`\`\`

The banner was:

\`\`\`text
SSH-2.0-OpenSSH_4.7p1 Debian-8ubuntu1
\`\`\`

This identified OpenSSH 4.7p1 using SSH protocol 2.0. The \`Debian-8ubuntu1\` text identifies the package build string recorded in the banner.

![SSH banner grabbed with netcat on port 22](screenshots/11-netcat-ssh-banner.jpg)

The two banners matched the versions from the Nmap service scan. The report notes that vsftpd 2.3.4 is associated with the backdoor issue CVE-2011-2523 and that OpenSSH 4.7p1 is an old release. I did not test or exploit either service; I recorded the versions as weaknesses that should be reviewed. Netcat remained connected after displaying the banner, so I closed the connections myself.

## 6. TryHackMe rooms

I completed the Vulnversity and Nmap rooms.

### 6.1 Vulnversity

The room covered a path from reconnaissance to root access in the training environment. Nmap identified open ports and services, Gobuster found a hidden upload directory, and Burp Suite was used to understand which file extension passed the upload filter. The room then demonstrated a reverse-shell foothold and privilege escalation through a SUID binary.

The main lesson I recorded was that several small weaknesses can combine into a larger compromise. From a defensive perspective, the room highlighted the need to review upload filters, elevated processes and SUID files.

![TryHackMe Vulnversity room completed](screenshots/12-tryhackme-vulnversity-completed.jpg)

### 6.2 Nmap

The Nmap room covered TCP Connect, SYN, UDP, NULL, FIN, Xmas and Ping Sweep scans, and how Nmap identifies open, closed and filtered ports. It reinforced that scan types have different trade-offs involving speed, stealth and accuracy. The scan choice should match the target environment and the objective of an authorised assessment.

![TryHackMe Nmap room completed](screenshots/13-tryhackme-nmap-completed.jpg)

## 7. Summary of findings

| Finding | Service / source | Why it matters in the report |
|---|---|---|
| vsftpd 2.3.4 | FTP (21); Nmap and netcat | Old version associated with a known backdoor; update or remove it |
| OpenSSH 4.7p1 Debian-8ubuntu1 | SSH (22); Nmap and netcat | Very old version that may have known vulnerabilities |
| Samba 3.0.20-Debian | SMB (139/445); enum4linux | Old version within the range associated with known vulnerabilities |
| Shares \`print$\`, \`tmp\`, \`opt\`, \`IPC$\` and \`ADMIN$\`; \`tmp\` opened anonymously | SMB (139/445); enum4linux and smbclient | Open shares can expose files to users who should not have access |
| Null session / anonymous login allowed | SMB (139/445); enum4linux and smbclient | Allows users, shares and policy details to be retrieved without credentials |
| 35 users found, including \`msfadmin\`, \`user\`, \`service\`, \`root\` and \`postgres\` | SMB (139/445); enum4linux | Valid usernames can make password guessing easier |
| Minimum password length 5, no complexity and no lockout | SMB (139/445); enum4linux | Weak password policy makes password guessing easier |

## 8. Challenges faced

During the enum4linux run, the tool could not retrieve OS information through smbclient and printed a \`Can't understand response\` message while mapping shares. The run still completed and retrieved OS information through \`srvinfo\`, including Samba version 3.0.20-Debian. smbclient also reported that it was reconnecting with SMB1 to list the workgroup, which was expected for this old Samba server. Nmap provided only a Samba version range, so enum4linux was needed to obtain the exact version. Netcat kept the connection open after the banner appeared, so I closed it manually.

## 9. Conclusion

In this task I enumerated services on my Metasploitable 2 target (\`10.0.0.6\`) using enum4linux, smbclient and netcat, and completed the TryHackMe Vulnversity and Nmap rooms. I stayed within enumeration and did not exploit anything.

The main findings were an SMB null session, Samba 3.0.20-Debian, five shares (with \`tmp\` accessible anonymously), 35 user accounts, a weak password policy, vsftpd 2.3.4 and OpenSSH 4.7p1. The results also showed how the tools support each other: Nmap identified the ports and provided an initial version range, enum4linux supplied more SMB details, smbclient confirmed share access and netcat confirmed the FTP and SSH banners.

The central lesson was that scanning tells me which services are available, while enumeration shows what those services disclose. These are findings I would report for remediation. I would need written permission before conducting equivalent activity against a real organisation's systems.
