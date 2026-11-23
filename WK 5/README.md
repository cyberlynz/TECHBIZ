# Week 5 — Web Application Attacks & Authentication Testing

**Internship:** Ethical Hacking Internship, TechBiz Academy  
**Student:** Collins  
**Focus:** SQL Injection, Cross-Site Scripting (XSS), and brute-force authentication testing

---

## Overview

Week 5 focused on three common web and authentication weaknesses. I worked through the exercises in controlled DVWA lab environments and completed the related TryHackMe practice rooms.

| Task | Topic | Practical work |
|---|---|---|
| [Task 1 — SQL Injection](Task%201/README.md) | SQL Injection (SQLi) | Tested a tautology payload, identified the query column count, enumerated database tables and columns, and extracted usernames with password hashes |
| [Task 2 — Cross-Site Scripting](Task%202/README.md) | Reflected and Stored XSS | Tested JavaScript injection in the reflected page and guestbook, checked cookie exposure, and verified output encoding at DVWA Impossible security |
| [Task 3 — Brute Force & Authentication Attacks](Task%203/README.md) | Hydra | Used rockyou.txt against the DVWA brute-force page, verified the discovered credentials manually, and completed the Hydra and Brute It rooms |

## Key learning outcomes

- Recognize how unsafe SQL query construction can expose database contents.
- Distinguish reflected, stored, and DOM-based XSS by where the payload is processed or stored.
- Understand why output encoding, parameterized queries, and secure session-cookie attributes matter.
- Configure Hydra for a web login form using the correct request method, parameter names, cookie and failure response.
- Relate weak passwords and missing rate limits or account lockout to practical authentication risk.
- Document findings and remediation rather than treating successful exploitation as the end of an assessment.

## Evidence and reports

Each task page summarizes the practical steps and results recorded in my submitted report. The original PDF reports contain the full write-ups and screenshots. The evidence index lists the report pages where each screenshot appears.

- [Week 5 evidence index](Screenshots/README.md)
- [Task 1 evidence and report notes](Task%201/README.md#evidence)
- [Task 2 evidence and report notes](Task%202/README.md#evidence)
- [Task 3 evidence and report notes](Task%203/README.md#evidence)

## Lab scope

All exploitation described here was performed against my own DVWA lab or the designated TryHackMe training environments. These techniques should only be used against systems for which explicit authorization has been granted.
