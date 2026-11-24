# Week 4 — Vulnerability Scanning & Web Application Security

<p align="center">
  <img src="https://img.shields.io/badge/Week-04-404040?style=flat-square&labelColor=238F89" alt="Week 4" />
  <img src="https://img.shields.io/badge/Focus-Vulnerability%20Scanning%20%7C%20Web%20Security-404040?style=flat-square&labelColor=C00000" alt="Vulnerability scanning and web security" />
  <img src="https://img.shields.io/badge/Lab-Metasploitable%202%20%7C%20DVWA-E87500?style=flat-square&labelColor=000000" alt="Metasploitable 2 and DVWA labs" />
</p>

During Week 4 at TechBiz Academy, I moved from network enumeration into vulnerability scanning and web application security. I used Nmap's vulnerability NSE scripts against my Metasploitable 2 lab, learned how HTTP requests and responses work, intercepted and modified a DVWA login request with Burp Suite, and studied the OWASP Top 10 (2025).

## Task folders

| Task | Topic | Areas covered |
|---|---|---|
| [**Task 1 →**](Task%201/README.md) | Vulnerability Scanning Basics | Nmap NSE vuln scripts, CVE research, CVSS severity, false positives, Nessus and OpenVAS concepts |
| [**Task 2 →**](Task%202/README.md) | How the Web Works + Burp Suite Intro | HTTP requests and responses, GET vs POST, status codes, cookies and sessions, Burp Proxy and DVWA request modification |
| [**Task 3 →**](Task%203/README.md) | OWASP Top 10 (2025) | Ten web risk categories, three categories in detail, real-world examples, DVWA mapping and TryHackMe module |

## Lab details

| Task | Environment / scope | Recorded details |
|---|---|---|
| Task 1 | Kali Linux to my own Metasploitable 2 VM | Kali 10.0.0.3; target 10.0.0.6; private 10.0.0.x lab network |
| Task 2 | Burp Suite Community Edition and local DVWA | DVWA at http://127.0.0.1:42001; Burp proxy listener 127.0.0.1:8080 |
| Task 3 | OWASP Top 10 (2025), DVWA and TryHackMe | Reference and learning exercise; DVWA category mapping follows the report's closest-fit approach |

All active scanning and request manipulation described in the reports took place in my own local training environment. Session identifiers and anti-CSRF values from the Burp captures are masked in the public write-up.

## What I practised

- Running Nmap service/version detection with the NSE vuln category and saving scan output for review.
- Distinguishing an Nmap-confirmed vulnerability from a possible issue matched only by software version.
- Researching CVEs, reading CVSS scores and ranking findings by impact and remediation priority.
- Comparing Nmap vulnerability scripts with the dedicated-scanner concepts behind Nessus and OpenVAS.
- Reading HTTP request lines, headers, bodies, response status codes and cookies.
- Comparing GET and POST requests on DVWA and observing where submitted values appear.
- Configuring Burp Suite as a local proxy, intercepting a POST request, changing its body and reviewing the response and HTTP history.
- Mapping common DVWA modules to the OWASP Top 10 (2025) categories.

## Main results at a glance

| Task | Main recorded result |
|---|---|
| Task 1 | Nmap reported 23 open TCP ports on Metasploitable 2. The vsftpd 2.3.4 backdoor on port 21 was confirmed by the script returning uid=0(root). Other CVE/version matches and service weaknesses required manual validation. |
| Task 2 | Burp intercepted the DVWA login POST request. Changing password to password123 caused DVWA to return a login failure; HTTP history showed the edited POST requests returning 302, followed by a 200 response for the login page. |
| Task 3 | I reviewed all ten OWASP Top 10 (2025) categories, studied A01, A04 and A05 in detail, mapped DVWA modules to the categories and completed all three rooms in the TryHackMe module. |

## What I took away

This week connected vulnerability scanning with manual validation and web application testing. The scan output gave me possible weaknesses to investigate, while Burp showed how the server processes requests that can be changed by a client. The OWASP categories helped me organise common application risks and connect lab exercises to wider security concepts.

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