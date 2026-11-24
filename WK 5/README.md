# Week 5 — Web Application Testing & Authentication Security

<p align="center">
  <img src="https://img.shields.io/badge/Week-05-404040?style=flat-square&labelColor=238F89" alt="Week 5" />
  <img src="https://img.shields.io/badge/Focus-Web%20Application%20Security-404040?style=flat-square&labelColor=C00000" alt="Web application security" />
  <img src="https://img.shields.io/badge/Lab-DVWA-404040?style=flat-square&labelColor=000000" alt="DVWA lab" />
</p>

This week, I worked on three tasks covering SQL Injection, XSS and password guessing with Hydra. I carried out the practical work on DVWA running in my lab, took screenshots as I went along and wrote down the results. I also completed the TryHackMe rooms connected to the tasks.

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
| Attacker environment used in Task 3 | Kali Linux (`10.0.0.3`) |
| Main tools | Browser, DVWA, Hydra, `rockyou.txt` |

## What I worked on

- Tested how unsanitized application input can change a SQL query and expose database records.
- Compared Reflected and Stored XSS and checked what a script could read from the page's cookies.
- Verified that output encoding at DVWA Impossible security displayed the submitted script as plain text.
- Used Hydra's web-form module with the form's request path, parameters, session cookie and failure response.
- Confirmed the credential found by Hydra by signing in manually, then recorded defensive controls for the findings.

## Results from my tasks

| Task | Main result |
|---|---|
| Task 1 — SQL Injection | Identified a two-column query and extracted five username/hash pairs from the `users` table. The `admin` and `smithy` accounts shared the same MD5 hash. |
| Task 2 — XSS | Confirmed Reflected and Stored XSS at Low security. The cookie test displayed `security=low` and `PHPSESSID`; the same reflected payload was shown as text at Impossible security. |
| Task 3 — Hydra | Hydra found `admin:password` on the fourth attempt. I confirmed the login manually, and no account lockout was observed during the test. |

## Screenshot gallery

I saved the screenshots in the folders for each task. I also added them to the write-ups so it is easier to see what I did at each stage.

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
