# Week 4, Task 1 — Vulnerability Scanning Basics

<p align="center">
  <img src="https://img.shields.io/badge/Task-01-404040?style=flat-square&labelColor=6366F1" alt="Task 1" />
  <img src="https://img.shields.io/badge/Tool-Nmap%20NSE-404040?style=flat-square&labelColor=238F89" alt="Nmap NSE" />
  <img src="https://img.shields.io/badge/Target-Metasploitable%202-E87500?style=flat-square&labelColor=000000" alt="Metasploitable 2" />
</p>

**Internship:** Ethical Hacking Internship, TechBiz Academy  
**Author:** Collins Okike  
**Attacker:** Kali Linux VM (10.0.0.3)  
**Target:** Metasploitable 2 VM (10.0.0.6)  
**Network:** Private 10.0.0.x lab network

## 1. Objective and scope

In this task I learned how automated vulnerability scanning works. I used Nmap with its vulnerability NSE scripts against my Metasploitable 2 lab machine, selected findings for further research using their CVE numbers, and reviewed the purpose of Nessus and OpenVAS. I wanted to understand not only what a scanner reports, but also how serious a finding is and what remediation should be recommended.

Metasploitable 2 is intentionally vulnerable. I scanned only my own lab machine. Scanning systems without ownership or permission is outside the scope of this work.

## 2. Tool and command

I used Nmap version 7.991 on Kali Linux. The command was:

~~~bash
nmap -sV --script vuln 10.0.0.6 -oN VulnMS.txt
~~~

| Option | Purpose |
|---|---|
| -sV | Detect service versions so findings can be matched to software versions. |
| --script vuln | Run NSE scripts in the vulnerability category against discovered services. |
| -oN VulnMS.txt | Save the normal-format output for later review. |

Scripts shown in the output included ftp-vsftpd-backdoor, ssl-poodle, ssl-dh-params, sslv2-drown, smtp-vuln-cve2010-4344, http-trace, http-stored-xss, http-dombased-xss, http-sql-injection, rmi-vuln-classloader and vulners. The vulners script matches detected software versions to CVEs in its database; those matches are leads, not proof that every listed vulnerability is exploitable on the target.

## 3. Method

1. Ran the Nmap scan with service/version detection and the vuln script category against 10.0.0.6.
2. Saved the output to VulnMS.txt and reviewed it from the beginning.
3. Marked the entries where Nmap reported VULNERABLE, along with CVE numbers and scores.
4. Selected the clearest and most serious findings, including the result that Nmap actually confirmed.
5. Looked up the CVEs on the NVD website and in additional write-ups.
6. Rated the findings by severity and recorded remediation actions.

## 4. Scan results

Nmap reported that the host was up, with **23 open ports** and **977 closed ports** in the 1,000-port default TCP scan.

### 4.1 vsftpd 2.3.4 backdoor — TCP 21

Nmap marked the FTP service as VULNERABLE (Exploitable) for CVE-2011-2523. The script ran the id command through the backdoor and returned uid=0(root). This was the finding that the scan confirmed with an observed root result.

### 4.2 SSL POODLE and anonymous Diffie-Hellman — TCP 25

On Postfix SMTP port 25, Nmap reported SSL POODLE (CVE-2014-3566) and an anonymous Diffie-Hellman key-exchange weakness. Both were marked vulnerable in the scan. The sslv2-drown script returned an error, so that check did not complete. Telnet on port 23 was also open.

### 4.3 ISC BIND 9.4.2 — TCP/UDP 53 service identification

The SMTP script reported that the server was not vulnerable to the Exim issue CVE-2010-4344. The DNS service was identified as ISC BIND 9.4.2. The vulners output listed multiple CVEs for that version, including CVE-2008-0122 with a score of 10.0. This is a version-based match and needed validation against the actual package build.

### 4.4 Apache httpd 2.2.8 — TCP 80

Nmap showed HTTP TRACE enabled. It did not find stored or DOM-based XSS in the tested checks, and the http-sql-injection script returned an error. The version-based vulners list included several high-scoring entries; these were not treated as confirmed vulnerabilities solely because of the version match.

### 4.5 Java RMI and other exposed services

The rmi-vuln-classloader script marked Java RMI on port 1099 as vulnerable because the registry allowed class loading from remote URLs. Other services seen included a bindshell on port 1524, r-services on ports 512–514, Samba on 139 and 445, NFS on 2049, and ProFTPD 1.3.1 on port 2121. The vulners output associated ProFTPD with CVE-2019-12815 and a score of 9.8; that version-based result also required confirmation.

### 4.6 Scan interpretation and limitations seen during the run

Two scripts (sslv2-drown and http-sql-injection) failed with errors, so those checks did not finish. The version-based vulners results were possible issues, not proven findings. The vsftpd backdoor was the only finding described in the report as fully confirmed because Nmap executed a command and returned uid=0(root).

## 5. Main findings

| Port / service | Finding recorded | CVE | Severity recorded |
|---|---|---|---|
| 21 / vsftpd 2.3.4 | FTP backdoor; Nmap confirmed a root shell (uid=0) | CVE-2011-2523 | Critical (9.8) |
| 25 / Postfix smtpd | SSL POODLE; SSL 3.0 still allowed | CVE-2014-3566 | Medium (4.3) |
| 25 / Postfix smtpd | Anonymous Diffie-Hellman key exchange; man-in-the-middle risk | None shown by Nmap | Medium (analyst rating) |
| 53 / ISC BIND 9.4.2 | Old DNS server version matched multiple CVEs; inet_network overflow researched | CVE-2008-0122 | Critical (10.0, CVSS v2) — version match to validate |
| 1099 / Java RMI | Registry allowed classes to be loaded from remote URLs | None shown by Nmap | High / Critical (analyst assessment) |

## 6. Other exposed services

| Port / service | What the scan showed | Why it matters |
|---|---|---|
| 23 / Telnet | Linux telnetd open | Telnet sends information, including passwords, in plaintext; SSH should be used instead. |
| 512–514 / r-services | rexecd, rlogin and tcpwrapped | Legacy remote-login services with weak or no encryption. |
| 1524 / bindshell | Metasploitable root shell | A service that gives a root shell to anyone who connects should not be exposed. |
| 139, 445 / Samba | smbd 3.X–4.X range | Old Samba versions have known issues; the exact package version should be checked. |
| 2049 / NFS (111 / rpcbind) | NFS 2–4 and rpcinfo listing | Unrestricted shares may be mountable by other machines; rpcbind also reveals service information. |
| 2121 / ProFTPD 1.3.1 | CVE-2019-12815 appeared in the vulners list with 9.8 | Old FTP version; the result is version-based and should be confirmed. |
| 80 / Apache 2.2.8 | HTTP TRACE enabled | TRACE should be disabled and the old Apache release reviewed. |

## 7. CVE research and technical interpretation

### 7.1 CVE-2011-2523 — vsftpd 2.3.4 backdoor

The report describes a backdoor inserted into a particular vsftpd 2.3.4 download between 30 June and 3 July 2011. A username ending with :) caused the server to open a shell on port 6200. The affected copy was not representative of normal vsftpd releases, but the vulnerable copy was present in Metasploitable 2. The report records CVSS 9.8 (v3.1) and 10.0 (v2), and Nmap returned uid=0(root).

**Impact:** A remote attacker could obtain a root command shell without a normal authenticated login.  
**Remediation recorded:** Replace the backdoored copy with a clean official release, verify downloaded files, and block ports 21 and 6200 when FTP is not required.

### 7.2 CVE-2014-3566 — SSL POODLE

POODLE is a weakness in SSL 3.0 that affects CBC-mode padding. A man-in-the-middle attacker may be able to recover parts of encrypted data over repeated interactions. The report records 4.3 under CVSS v2 (Medium) and 3.4 under CVSS v3.1 (Low).

**Impact:** Private data such as session cookies or mail content could be exposed to an attacker positioned in the connection.  
**Remediation recorded:** Disable SSL 3.0 and allow TLS 1.2 or higher.

### 7.3 Anonymous Diffie-Hellman — no CVE shown

The ssl-dh-params script showed TLS_DH_anon_WITH_DES_CBC_SHA with a 1024-bit modulus. Anonymous Diffie-Hellman does not authenticate the server, leaving the connection exposed to an active man-in-the-middle. The report assigned Medium severity as an analyst rating; Nmap did not provide a CVE or score.

**Remediation recorded:** Disable anonymous cipher suites and use Diffie-Hellman groups of 2048 bits or more.

### 7.4 CVE-2008-0122 — ISC BIND 9.4.2 version match

The report describes an off-by-one error in the inet_network function in libbind, which may cause a buffer overflow when specially crafted input is processed. NVD's CVSS v2 score was recorded as 10.0. The report also notes an important qualification: the vulners result was based on the detected version, and Debian's security tracker indicated that its bind9 package did not use the old libbind code. The issue therefore needed confirmation before being reported as applicable to this target.

**Potential impact:** Denial of service and possibly code execution, depending on the affected code path.  
**Remediation recorded:** Upgrade BIND to a supported release and verify whether the specific vulnerability affects the installed package.

### 7.5 Java RMI registry remote class loading

The rmi-vuln-classloader script marked port 1099 vulnerable because the registry's default configuration allowed classes to be loaded from remote URLs. Nmap did not show a CVE number; it linked to the Metasploit java_rmi_server module. The report rated the issue High to Critical because it could result in remote code execution.

**Remediation recorded:** Disable the RMI registry if it is not required, firewall port 1099, and use a current Java version with safe remote-class-loading settings.

## 8. Severity and remediation priority

The report used CVSS scores from 0 to 10 and the following severity bands:

| CVSS score | Severity | Response described in the report |
|---|---|---|
| 9.0–10.0 | Critical | Fix first; these usually have serious impact and may be easier to exploit. |
| 7.0–8.9 | High | Fix soon. |
| 4.0–6.9 | Medium | Plan the fix and consider whether it can be chained with other issues. |
| 0.1–3.9 | Low | Fix when practical or formally accept the risk. |

A high score in a version-matching database is not enough by itself. For example, the vulners output included CVE-2010-0425 for Apache 2.2.8 with a score of 10.0, but the issue concerns the Windows-only mod_isapi module. The report notes that Ubuntu lists the 8.04 base version as not affected. Findings must therefore be checked against the actual operating system, package and enabled modules.

The remediation order recorded was:

1. Replace the backdoored vsftpd 2.3.4 installation and block ports 21 and 6200 if FTP is unnecessary.
2. Disable or firewall Java RMI on port 1099 and the bindshell on port 1524.
3. Upgrade BIND and confirm which reported CVEs apply to the installed package.
4. Disable Telnet and r-services, using SSH instead.
5. Disable SSL 3.0 and anonymous Diffie-Hellman; use TLS 1.2 or higher and DH groups of at least 2048 bits.
6. Disable HTTP TRACE and update the old Apache 2.2.8 installation.

## 9. Scan limitations

- Nmap checked the 1,000 most common TCP ports by default; other TCP ports and UDP were not checked in this run.
- The sslv2-drown and http-sql-injection scripts failed, so their checks were incomplete.
- vulners matches software version strings, and some matches may not apply to the target's actual package build or enabled modules.
- A scan can miss weaknesses; no finding does not prove that a system is secure.
- Metasploitable 2 is intentionally vulnerable and does not represent a typical production server.

## 10. Nessus and OpenVAS — concept review

The report reviewed Nessus and OpenVAS as dedicated vulnerability scanners. Nessus is made by Tenable; the report notes that Nessus Essentials can scan up to 16 IP addresses and produces findings arranged by severity. OpenVAS is described as a free and open-source alternative. Nmap's vulnerability scripts are useful for a quick check, but their results are less comprehensive and need manual validation. A Nessus Essentials scan was not run because it was optional for this task.

## 11. TryHackMe: Vulnerabilities 101

The room covered what a vulnerability is, five main types, and the difference between CVSS (technical severity) and VPR (risk in an organisational context). It also introduced NVD for CVE tracking and Exploit-DB for researching exploits by software name and version. The report's key lesson was that vulnerabilities can be chained: a small piece of exposed information can help identify a relevant exploit when combined with other findings.

## 12. Nmap Scripting Engine reference

The Nmap reference page explained script categories such as safe, intrusive, vuln and exploit, and how to combine them with not, and and or. It also covered --script-args, --script-help and --script-updatedb. The report notes that NSE scripts are not sandboxed, so selecting an appropriate script category matters for safety as well as scan results.

## 13. What I learned

- A vulnerability scan identifies possible weaknesses; it is not the same as a penetration test that attempts to exploit them.
- Each finding needs validation because scanners can report false positives and some checks can fail.
- CVE identifiers help locate technical details, while CVSS scores help organise remediation priorities.
- Small weaknesses can be chained into a more serious attack path.
- Nmap NSE scripts are not sandboxed, so I need to choose script categories carefully, especially outside a lab.
- An analyst needs to scan, validate, rank and clearly report findings with practical remediation steps.

## 14. Conclusion

Running nmap --script vuln against Metasploitable 2 produced a long list of findings within a few minutes. The most important result was the vsftpd 2.3.4 backdoor (CVE-2011-2523), which Nmap confirmed by obtaining a root identity. I also reviewed SSL POODLE, anonymous Diffie-Hellman, the old BIND version and Java RMI. The main lesson was that scanning is only the first step: the output must be checked, prioritised and explained with a realistic remediation recommendation.