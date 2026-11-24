# Week 4, Task 1 — Vulnerability Scanning Basics

<p align="center">
  <img src="https://img.shields.io/badge/Task-01-404040?style=flat-square&labelColor=6366F1" alt="Task 1" />
  <img src="https://img.shields.io/badge/Tool-Nmap%20NSE-404040?style=flat-square&labelColor=238F89" alt="Nmap NSE" />
  <img src="https://img.shields.io/badge/Target-Metasploitable%202-E87500?style=flat-square&labelColor=000000" alt="Metasploitable 2" />
</p>

**Internship:** Ethical Hacking Internship, TechBiz Academy  
**Author:** Collins Okike  
**Attacker:** Kali Linux — 10.0.0.3  
**Target:** Metasploitable 2 — 10.0.0.6

## 1. Introduction

In this task I moved from finding open services to checking whether any of them had known weaknesses. I used Nmap's vulnerability scripts against my Metasploitable 2 machine, then researched selected results using their CVE numbers.

I also learned that a scanner result needs to be checked before I call it a vulnerability. Some scripts actually test a weakness, while others match the detected software version against a database. Those two types of result should not be reported in the same way.

## 2. Lab setup and scope

| Machine | Address | Purpose |
|---|---|---|
| Kali Linux VM | 10.0.0.3 | Running Nmap and reviewing the results |
| Metasploitable 2 VM | 10.0.0.6 | Intentionally vulnerable lab target |
| Network | 10.0.0.x | Private lab network shared by both VMs |

I only scanned my own Metasploitable 2 machine. I kept the activity inside the private lab because the target is deliberately vulnerable.

## 3. Running the vulnerability scan

The main tool was Nmap 7.991. I ran:

~~~bash
nmap -sV --script vuln 10.0.0.6 -oN VulnMS.txt
~~~

I used the options as follows:

| Option | What it does |
|---|---|
| -sV | Tries to identify the service and version running on each open port. |
| --script vuln | Runs NSE scripts from the vulnerability category. |
| -oN VulnMS.txt | Saves the normal output so I can read it after the scan. |

The output included checks such as ftp-vsftpd-backdoor, ssl-poodle, ssl-dh-params, http-trace, rmi-vuln-classloader and vulners. The vulners script was useful for research, but its CVE matches were based on version information and were not proof that every issue affected this machine.

## 4. What Nmap reported

Nmap showed that the host was up, with **23 open ports** and **977 closed ports** in the default 1,000-port TCP scan. These were the findings I selected for closer review.

| Port / service | What appeared in the scan | How I treated the result |
|---|---|---|
| 21/tcp — vsftpd 2.3.4 | The ftp-vsftpd-backdoor script reported the service as vulnerable and the id command returned uid=0(root). | Confirmed by the script's observed result. |
| 25/tcp — Postfix | SSL POODLE (CVE-2014-3566) and anonymous Diffie-Hellman were reported. | Findings to review in the server's TLS configuration. |
| 53/tcp — BIND 9.4.2 | vulners listed CVEs for the detected version, including CVE-2008-0122. | Version-based matches; verify whether the installed package is affected. |
| 80/tcp — Apache 2.2.8 | HTTP TRACE was enabled. Stored/DOM XSS checks did not find those issues; the SQL injection script returned an error. | TRACE was observed; the failed script did not provide a result. |
| 1099/tcp — Java RMI | rmi-vuln-classloader reported that the registry allowed remote class loading. | Important exposure to investigate; Nmap did not show a CVE number. |

### What stood out to me

The vsftpd result was the clearest finding. Nmap did more than identify an old version: the script ran the id command through the backdoor and returned uid=0(root). That is why I treated it differently from the BIND CVE list, which was generated from a version match.

The scan also reported that the SMTP server was not vulnerable to the Exim issue CVE-2010-4344. Two checks did not complete: sslv2-drown and http-sql-injection returned errors. I did not treat either error as a clean result.

## 5. Other exposed services I noted

The scan showed additional services that would also need attention during a real assessment.

| Port / service | Observation | Why I noted it |
|---|---|---|
| 23/tcp — Telnet | Linux telnetd was open. | Telnet does not encrypt the login session. |
| 512–514/tcp — r-services | rexecd, rlogin and tcpwrapped were reported. | These are old remote-access services and should be reviewed or disabled. |
| 1524/tcp — bindshell | Metasploitable root shell was exposed. | A remotely reachable root shell is a serious exposure. |
| 139/445 — Samba | Samba service was present. | I would check the exact installed version and whether file sharing is needed. |
| 2049/tcp — NFS | NFS and rpcbind information appeared. | Shares and access restrictions should be reviewed. |
| 2121/tcp — ProFTPD 1.3.1 | The version matched CVE-2019-12815 in the vulners output. | This was another version match to validate. |
| 80/tcp — Apache | HTTP TRACE was enabled. | I would disable TRACE if it is not needed and review the old server version. |

## 6. Researching the main findings

I used the CVE identifiers and NVD to understand what the scan had found. These were the main points from my research.

### vsftpd 2.3.4 — CVE-2011-2523

The report describes a backdoored vsftpd 2.3.4 download from 30 June to 3 July 2011. A specially formed username could cause the server to open a shell on port 6200. In my scan, the script returned uid=0(root), showing that the lab target's service exposed a root shell.

**What I would recommend:** Replace the backdoored copy with a clean supported release, verify downloaded software, and block ports 21 and 6200 if FTP is not needed.

### SSL POODLE — CVE-2014-3566

POODLE is a weakness in SSL 3.0's CBC padding. The report explains that a man-in-the-middle attacker could recover parts of encrypted information after repeated interactions.

**What I would recommend:** Disable SSL 3.0 and use TLS 1.2 or higher.

### Anonymous Diffie-Hellman

The ssl-dh-params script reported TLS_DH_anon_WITH_DES_CBC_SHA with a 1024-bit modulus. Anonymous Diffie-Hellman does not authenticate the server, which creates a man-in-the-middle risk. Nmap did not provide a CVE or score for this result; the report rated it Medium.

**What I would recommend:** Disable anonymous cipher suites and use stronger Diffie-Hellman groups of at least 2048 bits.

### BIND 9.4.2 — CVE-2008-0122

The vulners script matched the BIND version to CVE-2008-0122, an off-by-one error in the inet_network function in libbind. The report records a CVSS v2 score of 10.0. However, it also notes that Debian's security tracker said its bind9 package did not use the old libbind code.

**How I treated it:** I kept it as a result that needs package-level validation rather than claiming it was confirmed on my target.

### Java RMI on port 1099

The rmi-vuln-classloader script reported that the registry allowed classes to be loaded from remote URLs. Nmap did not show a CVE number, but the report connected the behaviour to possible remote code execution.

**What I would recommend:** Disable the RMI registry if it is not needed, restrict port 1099 with a firewall and use a supported Java configuration that prevents unsafe remote class loading.

## 7. Severity and remediation priority

I used the CVSS ranges from my research to help understand the severity of a finding.

| CVSS score | Severity | How I would prioritise it |
|---|---|---|
| 9.0–10.0 | Critical | Fix first and verify the exposure is removed. |
| 7.0–8.9 | High | Schedule a prompt fix. |
| 4.0–6.9 | Medium | Plan remediation and check whether it can combine with other weaknesses. |
| 0.1–3.9 | Low | Fix when practical or document an accepted risk. |

Based on the results in this lab, my remediation order would be:

1. Replace the backdoored vsftpd installation and remove unnecessary FTP access.
2. Disable or firewall the Java RMI registry and the bindshell on port 1524.
3. Review BIND 9.4.2 and confirm which CVEs apply to the installed package.
4. Disable Telnet and the legacy r-services; use SSH instead.
5. Disable SSL 3.0 and anonymous Diffie-Hellman.
6. Disable Apache TRACE and review the old Apache installation.

I would not rank a finding only by the highest number in a scanner output. For example, the report notes that one Apache CVE listed with a 10.0 score concerned the Windows-only mod_isapi module, so that match did not apply to the Linux target.

## 8. Nessus, OpenVAS and what I learned

I also reviewed Nessus and OpenVAS. Nessus Essentials is Tenable's free edition, with the report noting a limit of 16 IP addresses. OpenVAS is a free and open-source vulnerability scanner. Both are dedicated vulnerability-scanning tools, while Nmap's NSE scripts are useful for a quicker initial check. I did not run a Nessus scan because it was optional for this task.

### TryHackMe — Vulnerabilities 101

![TryHackMe Vulnerabilities 101 room completed](screenshots/06-tryhackme-vulnerabilities-101-completed.webp)

The room introduced vulnerability types and the difference between CVSS and VPR. I learned that CVSS describes technical severity, while VPR helps consider risk in an organisation's context. It also introduced NVD for researching CVEs and Exploit-DB for looking up exploit information.

The point I took away was that vulnerabilities can be chained. A small detail, such as an exposed software version, may help an attacker identify a weakness that can be combined with another issue.

## 9. Limitations and conclusion

There were a few limits to this scan:

- Nmap checked the 1,000 most common TCP ports by default; UDP and other TCP ports were not covered in this run.
- The sslv2-drown and http-sql-injection scripts returned errors, so those checks were incomplete.
- The vulners results were matched by version and needed manual confirmation.
- A vulnerability scan can miss issues; an empty result does not prove that a system is secure.
- Metasploitable 2 is deliberately vulnerable and does not represent a normal production server.

This task helped me understand that vulnerability scanning is only the starting point. I need to read the output, confirm which findings really apply, explain the impact and give a practical fix. The clearest result in my scan was the vsftpd backdoor because Nmap demonstrated the root-level result instead of only listing a possible CVE match.