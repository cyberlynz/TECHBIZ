# Week 5 — Web Application Testing & Authentication Security

<p align="center">
  <img src="https://img.shields.io/badge/Week-05-404040?style=flat-square&labelColor=238F89" alt="Week 5" />
  <img src="https://img.shields.io/badge/Focus-Web%20Application%20Security-404040?style=flat-square&labelColor=C00000" alt="Web application security" />
  <img src="https://img.shields.io/badge/Lab-DVWA-404040?style=flat-square&labelColor=000000" alt="DVWA lab" />
</p>

During Week 5 at TechBiz Academy, I practised three areas of application security: SQL Injection, Cross-Site Scripting and brute-force authentication testing. I used DVWA in my own lab for the practical exercises, documented the payloads and results, and completed the related TryHackMe rooms.

---

## Task folders

| Task | Topic | Areas covered |
|---|---|---|
| [**Task 1 →**](Task%201/README.md) | SQL Injection | DVWA Low security, tautology testing, `ORDER BY` column counting, `UNION SELECT`, `information_schema` enumeration, username and password-hash extraction |
| [**Task 2 →**](Task%202/README.md) | Cross-Site Scripting (XSS) | Reflected XSS, Stored XSS, guestbook persistence, cookie exposure, TryHackMe XSS practice and output-encoding verification at DVWA Impossible security |
| [**Task 3 →**](Task%203/README.md) | Brute Force & Authentication Attacks | Locating `rockyou.txt`, configuring Hydra for a web login form, verifying the discovered credential and completing Hydra / Brute It practice |

## Main lab details

| Item | Recorded setup |
|---|---|
| Vulnerable application | Damn Vulnerable Web Application (DVWA) |
| SQL Injection and XSS security level | Low |
| Hydra target | DVWA Brute Force page at `http://127.0.0.1:42001` |
| Attacker environment in the Hydra report | Kali Linux (`10.0.0.3`) |
| Main tools | Browser, DVWA, Hydra, `rockyou.txt` |

## What I practised

- Tested how unsanitized application input can change a SQL query and expose database records.
- Compared Reflected and Stored XSS and checked what a script could read from the page's cookies.
- Verified that output encoding at DVWA Impossible security displayed the submitted script as plain text.
- Used Hydra's web-form module with the form's request path, parameters, session cookie and failure response.
- Confirmed the credential found by Hydra by signing in manually, then recorded defensive controls for the findings.

## Main results at a glance

| Task | Main result |
|---|---|
| Task 1 — SQL Injection | Identified a two-column query and extracted five username/hash pairs from the `users` table. The `admin` and `smithy` accounts shared the same MD5 hash. |
| Task 2 — XSS | Confirmed Reflected and Stored XSS at Low security. The cookie test displayed `security=low` and `PHPSESSID`; the same reflected payload was shown as text at Impossible security. |
| Task 3 — Hydra | Hydra found `admin:password` on the fourth attempt. I confirmed the login manually, and no account lockout was observed during the test. |

## Screenshot gallery

The screenshots extracted from my three Week 5 task reports are stored in the matching task folders and embedded beside the relevant steps in each write-up.

**[Open the Week 5 screenshot gallery →](Screenshots/README.md)**

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
