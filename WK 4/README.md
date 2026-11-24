# Week 4 — Vulnerability Scanning, Burp Suite & OWASP Top 10

<p align="center">
  <img src="https://img.shields.io/badge/Week-04-404040?style=flat-square&labelColor=238F89" alt="Week 4" />
  <img src="https://img.shields.io/badge/Focus-Vulnerability%20Scanning%20%7C%20Web%20Security-404040?style=flat-square&labelColor=C00000" alt="Vulnerability scanning and web security" />
  <img src="https://img.shields.io/badge/Lab-Metasploitable%202%20%7C%20DVWA-E87500?style=flat-square&labelColor=000000" alt="Metasploitable 2 and DVWA" />
</p>

During Week 4 at TechBiz Academy, I moved from finding open services into checking for known weaknesses and understanding web application traffic. I used Nmap against my Metasploitable 2 lab, inspected HTTP requests with Burp Suite on DVWA, and studied the OWASP Top 10 (2025).

---

## Task folders

| Task | Topic | What I worked on |
|---|---|---|
| [**Task 1 →**](Task%201/README.md) | Vulnerability Scanning Basics | Nmap NSE checks, CVE research, validating scan results, severity and remediation |
| [**Task 2 →**](Task%202/README.md) | How the Web Works + Burp Suite Intro | HTTP requests and responses, GET and POST, cookies, Burp Proxy and modifying a DVWA login request |
| [**Task 3 →**](Task%203/README.md) | OWASP Top 10 (2025) | Ten risk categories, A01/A04/A05 in detail, real-world examples, DVWA mapping and TryHackMe |

## Lab details

| Item | Recorded value |
|---|---|
| Attacker machine | Kali Linux — 10.0.0.3 |
| Target machine | Metasploitable 2 — 10.0.0.6 |
| Network | Private 10.0.0.x lab network |
| Web application | Local DVWA — http://127.0.0.1:42001 |
| Burp Proxy listener | 127.0.0.1:8080 |

Task 1 scanning and Task 2 request interception were carried out in my own training environment. Task 3 was a study and mapping exercise using the OWASP list, DVWA and TryHackMe.

## What I practised

- Running Nmap vulnerability scripts and saving the output for review.
- Checking CVE information instead of treating every scanner match as a confirmed vulnerability.
- Comparing findings by severity and deciding which ones should be fixed first.
- Reading HTTP request lines, headers, response codes and cookies.
- Intercepting a DVWA login request in Burp, changing a value in the request body and checking the response.
- Relating common web application weaknesses to the OWASP Top 10 (2025).

## Main results

| Task | What I found or completed |
|---|---|
| Task 1 | Nmap reported 23 open ports and 977 closed ports. The vsftpd 2.3.4 backdoor check returned uid=0(root). Other results, including BIND CVE matches, needed manual validation. |
| Task 2 | I changed the password value in an intercepted DVWA login request to password123. The login failed, and Burp HTTP history showed the edited POST requests returning 302 before the login page loaded with 200. |
| Task 3 | I reviewed all ten OWASP categories, studied Broken Access Control, Cryptographic Failures and Injection, mapped DVWA modules and completed the three TryHackMe rooms. |

## Evidence shown in the task pages

The captures below are also embedded in the matching task write-ups, beside the steps and findings they support.

### Task 1 — Vulnerability Scanning Basics

![Nmap confirmation of the vsftpd 2.3.4 backdoor result on port 21](Task%201/screenshots/01-nmap-vsftpd-backdoor-port-21.webp)

[Open Task 1 write-up →](Task%201/README.md)

### Task 2 — HTTP and Burp Suite

![Burp Suite intercepting the DVWA POST login request](Task%202/screenshots/05-burp-intercepted-post-login-request.webp)

[Open Task 2 write-up →](Task%202/README.md)

### Task 3 — OWASP Top 10 (2025)

![TryHackMe OWASP Top 10 (2025) module showing all three rooms completed](Task%203/screenshots/01-tryhackme-owasp-top-10-2025-all-rooms-completed.webp)

[Open Task 3 write-up →](Task%203/README.md)

**[Browse the complete Week 4 screenshot gallery →](Screenshots/README.md)**

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