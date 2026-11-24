# Week 4 — Vulnerability Scanning & Web Application Security

<p align="center">
  <img src="https://img.shields.io/badge/Week-04-404040?style=flat-square&labelColor=238F89" alt="Week 4" />
  <img src="https://img.shields.io/badge/Focus-Vulnerability%20Scanning%20%7C%20Web%20Security-404040?style=flat-square&labelColor=C00000" alt="Vulnerability scanning and web security" />
  <img src="https://img.shields.io/badge/Lab-Metasploitable%202%20%7C%20DVWA-E87500?style=flat-square&labelColor=000000" alt="Metasploitable 2 and DVWA" />
</p>

During Week 4 at TechBiz Academy, I moved from network enumeration into vulnerability scanning and web application security. The three tasks covered Nmap vulnerability checks against my Metasploitable 2 lab, HTTP fundamentals and request interception with Burp Suite on DVWA, and the OWASP Top 10 (2025).

---

## Task folders

| Task | Topic | Areas covered |
|---|---|---|
| [**Task 1 →**](Task%201/README.md) | Vulnerability Scanning Basics | Nmap NSE vulnerability scripts, CVE research, issue severity, remediation priorities, scan limitations, Nessus and OpenVAS concepts |
| [**Task 2 →**](Task%202/README.md) | How the Web Works + Burp Suite Intro | HTTP requests and responses, HTTP vs HTTPS, GET and POST, status codes, cookies and sessions, Burp Proxy and DVWA request modification |
| [**Task 3 →**](Task%203/README.md) | OWASP Top 10 (2025): Web Vulnerabilities Overview | Ten OWASP categories, A01/A04/A05 in detail, real-world examples, DVWA mapping and the TryHackMe OWASP module |

## Lab details

| Task | Environment / scope | Recorded details |
|---|---|---|
| Task 1 | Kali Linux to my own Metasploitable 2 VM | Kali 10.0.0.3; target 10.0.0.6; private 10.0.0.x lab network |
| Task 2 | Burp Suite Community Edition and local DVWA | DVWA http://127.0.0.1:42001; Burp proxy listener 127.0.0.1:8080 |
| Task 3 | OWASP Top 10 (2025), DVWA and TryHackMe | Reference and learning exercise; DVWA modules mapped to the closest OWASP categories in the report |

Task 1 scanning and Task 2 request interception were performed in my own training environment. The published Burp screenshots have session identifiers and anti-CSRF values masked. Task 3 is a reference and learning exercise using the OWASP list, DVWA and TryHackMe.

## What I practised

- Running nmap -sV --script vuln against Metasploitable 2 and saving the results for review.
- Checking CVE details and distinguishing a confirmed scanner result from version-based matches that still need validation.
- Reading CVSS severity scores and recording remediation priorities.
- Comparing the purpose of Nessus and OpenVAS with Nmap's vulnerability scripts.
- Reading HTTP request and response lines, headers, bodies and status codes.
- Comparing GET and POST requests in DVWA and observing how cookies maintain a session.
- Setting up Burp Suite Proxy, intercepting a login request, modifying the request body and checking the response and HTTP history.
- Reviewing the OWASP Top 10 (2025), real-world examples and DVWA's related modules.

## Main results at a glance

| Task | Main recorded result |
|---|---|
| Task 1 | Nmap reported 23 open ports and 977 closed ports. The vsftpd 2.3.4 backdoor on port 21 was confirmed when the script returned uid=0(root). Other version-based matches needed manual validation. |
| Task 2 | I intercepted a DVWA login POST request, changed the password value to password123 and forwarded it. DVWA displayed Login failed; HTTP history showed the edited POST requests returning 302, followed by a 200 response for the login page. |
| Task 3 | I reviewed all ten OWASP Top 10 (2025) categories, studied A01, A04 and A05 in detail, recorded one real-world example for each category, mapped DVWA modules and completed the three TryHackMe rooms. |

## Screenshot gallery

The three reports contained 18 evidence screenshots in total. Five are currently published in the GitHub task folders and embedded in the relevant report sections. The full 18-image set is included in the accompanying rebuilt Week 4 package.

**[Open the Week 4 screenshot gallery →](Screenshots/README.md)**

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