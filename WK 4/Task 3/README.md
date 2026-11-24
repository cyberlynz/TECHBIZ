# Week 4, Task 3 — OWASP Top 10 (2025): Web Vulnerabilities Overview

<p align="center">
  <img src="https://img.shields.io/badge/Task-03-404040?style=flat-square&labelColor=6366F1" alt="Task 3" />
  <img src="https://img.shields.io/badge/Reference-OWASP%20Top%2010%202025-404040?style=flat-square&labelColor=C00000" alt="OWASP Top 10 2025" />
  <img src="https://img.shields.io/badge/Practice-DVWA%20%7C%20TryHackMe-404040?style=flat-square&labelColor=238F89" alt="DVWA and TryHackMe" />
</p>

**Internship:** Ethical Hacking Internship, TechBiz Academy  
**Author:** Collins Okike  
**Reference:** OWASP Top 10 (2025)

## 1. Introduction

For this task I used the OWASP Top 10 (2025) to organise my understanding of common web application risks. I wrote a short meaning for each category, studied three of them in more detail, looked at a real-world example for each item, and matched the DVWA modules to the closest OWASP categories.

I also completed the three rooms in the TryHackMe OWASP Top 10 (2025) module. This task was mainly research and learning; it was not a separate penetration test against a live website.

## 2. The ten OWASP categories

This is the list I reviewed, with each category explained in my own words.

| ID | Category | What it means |
|---|---|---|
| A01 | Broken Access Control | A user can access data or functions they should not be allowed to use. The category also includes SSRF. |
| A02 | Security Misconfiguration | Unsafe defaults, exposed services, public storage or unnecessary features are left enabled. |
| A03 | Software Supply Chain Failures | A system relies on components, updates or build tools that are vulnerable or have been tampered with. |
| A04 | Cryptographic Failures | Sensitive information is not protected properly because encryption is missing, weak or incorrectly used. |
| A05 | Injection | Untrusted input is treated as a query or command, such as SQL injection, command injection or XSS. |
| A06 | Insecure Design | A security weakness is built into the way the application was planned. |
| A07 | Authentication Failures | Weak login or session handling makes it possible to impersonate a real user. |
| A08 | Software or Data Integrity Failures | Code, updates or data are trusted without checking that they have not been changed. |
| A09 | Security Logging & Alerting Failures | Attacks are not logged or alerted on, making them harder to detect and investigate. |
| A10 | Mishandling of Exceptional Conditions | Poor error handling or fail-open behaviour can expose data or skip a security check. |

### What changed from the 2021 list

- SSRF, which appeared as A10 in 2021, was included under A01 Broken Access Control.
- A03 Software Supply Chain Failures and A10 Mishandling of Exceptional Conditions were added.
- Security Misconfiguration moved from fifth place to second.
- Cryptographic Failures and Injection moved to A04 and A05, and Insecure Design moved to A06.
- Some category names changed, including Security Logging & Alerting and Authentication Failures.

## 3. Three categories I studied in more detail

### A01 — Broken Access Control

Access control decides what each user is allowed to see or do. It becomes a problem when the application does not check those permissions properly.

Examples I noted:
- Changing an ID in a URL to view another person's record (IDOR).
- Opening an administrator page directly while logged in as a normal user.
- Changing a hidden field or cookie to try to gain extra privileges.
- Using SSRF to make a server send requests to internal systems it can reach.

**Why it matters:** An attacker may read, change or delete another user's information, or access administrator functions.

**Prevention:** Deny access by default, check permissions on the server for every request, use appropriate roles, restrict URLs fetched by the server and log access-control failures.

### A04 — Cryptographic Failures

This category used to be called Sensitive Data Exposure. The updated name focuses on the underlying cryptography problem. Sensitive information can include passwords, payment-card data, health records and personal details.

Examples I noted:
- Sending information over HTTP rather than HTTPS.
- Storing passwords using weak hashes such as MD5 or SHA-1, or without a salt.
- Using old encryption algorithms or hard-coded keys.
- Failing to check certificates or using default keys.

**Why it matters:** If a database is exposed, weakly protected data may be read or passwords may be cracked and reused elsewhere.

**Prevention:** Use HTTPS, use strong password-hashing functions such as bcrypt, scrypt or Argon2, use modern encryption, protect keys and avoid storing data that is not needed.

### A05 — Injection

Injection happens when an application sends untrusted input to something that interprets it as a command or query. The examples in my notes included SQL injection, operating-system command injection, template injection and XSS.

~~~text
' OR '1'='1
~~~

This is an example of input that may change the logic of a poorly constructed SQL query.

~~~text
127.0.0.1; whoami
~~~

This shows how an extra command could be added when an application passes user input unsafely to a system shell.

**Why it matters:** Injection can lead to data theft, login bypass, data loss or control of the server.

**Prevention:** Use prepared statements or parameterized queries, validate input on the server, escape output to prevent XSS and give database accounts only the permissions they need.

## 4. Real-world examples I reviewed

I matched one incident or example to each category. These are the examples recorded in my research notes.

| OWASP item | Example | Year | What happened |
|---|---|---:|---|
| A01 | First American Financial | 2019 | Sequential document links and missing access checks allowed other people's mortgage documents to be viewed by changing a URL. |
| A02 | Accenture | 2017 | Four AWS S3 buckets were left publicly accessible and contained internal data, including credentials and keys. |
| A03 | SolarWinds | 2020 | Malicious code was inserted into the Orion build process and distributed through a normal software update. |
| A04 | Adobe | 2013 | Customer passwords were encrypted using 3DES in ECB mode instead of being hashed and salted; password hints were also exposed. |
| A05 | Heartland Payment Systems | 2008 | SQL injection was used to gain access to the network and install sniffer software; the cited DOJ source reported more than 130 million cards affected. |
| A06 | Parler | 2021 | Sequential post IDs, an unauthenticated public API and no rate limiting allowed large amounts of post data to be downloaded. |
| A07 | 23andMe | 2023 | Credential stuffing accessed about 14,000 accounts; data relating to about 6.9 million people was exposed through the DNA Relatives feature. |
| A08 | Codecov | 2021 | A leaked storage key was used to alter the Bash Uploader script, which could expose environment variables from affected build pipelines. |
| A09 | Target | 2013 | FireEye alerts were not acted on in time; the cited US Senate report recorded about 40 million payment cards stolen. |
| A10 | gdm3 login manager — CVE-2020-16125 | 2020 | A login-manager error-handling issue could start the new-account setup program and allow a local user to create a privileged account on affected Ubuntu systems. |

## 5. Matching DVWA modules to OWASP 2025

I used DVWA as a practice application and matched each module to the closest OWASP category. Some modules could fit more than one category, so the table keeps the primary match from my notes.

| DVWA module | OWASP category | Why I matched it there |
|---|---|---|
| Brute Force | A07 — Authentication Failures | The login has no protection against repeated password guessing. |
| Command Injection | A05 — Injection | User input is passed to the system shell. |
| CSRF | A01 — Broken Access Control | The application does not properly check whether the request was intended by the user. |
| File Inclusion | A05 — Injection | A page parameter controls which file is loaded; path traversal can also relate to A01. |
| File Upload | A06 — Insecure Design | Upload rules were not designed safely; it can also relate to A02. |
| Insecure CAPTCHA | A07 — Authentication Failures | The CAPTCHA check can be bypassed during login or reset. |
| SQL Injection | A05 — Injection | User input is included in a SQL query unsafely. |
| SQL Injection (Blind) | A05 — Injection | The same weakness is inferred from true/false responses rather than displayed query results. |
| Weak Session IDs | A07 — Authentication Failures | Predictable session identifiers may allow a session to be taken over. |
| XSS (Reflected) | A05 — Injection | User input is returned by the page and runs as script. |
| XSS (Stored) | A05 — Injection | A script is stored by the application and runs when another user opens the page. |
| XSS (DOM) | A05 — Injection | Page JavaScript handles URL input unsafely. |
| CSP Bypass | A02 — Security Misconfiguration | The Content Security Policy is configured in a way that can be bypassed. |
| JavaScript | A06 — Insecure Design | The application relies on security checks in the browser that a user can change. |
| Authorisation Bypass | A01 — Broken Access Control | A user can open pages or data intended for another role. |
| Open HTTP Redirect | A01 — Broken Access Control | The application redirects to an address supplied by the user; it can also relate to A06. |
| Cryptography | A04 — Cryptographic Failures | The module demonstrates weak or incorrectly used encryption; it is present only in newer DVWA versions. |

The report noted that DVWA does not have direct modules for A03, A08, A09 and A10. Those areas depend more on the wider system, such as dependencies, build pipelines, monitoring and error handling.

## 6. TryHackMe — OWASP Top 10 (2025)

I completed all three rooms in the OWASP Top 10 (2025) module.

### Room 1 — IAAA Failures

This room covered Identity, Authentication, Authorization and Accountability. I learned how broken access control, authentication weaknesses and poor logging affect the way an application checks users and records their actions. It also covered IDOR, weak passwords, session handling, rate limiting and central logging.

### Room 2 — Application Design Flaws

This room covered Security Misconfiguration, Software Supply Chain Failures, Cryptographic Failures and Insecure Design. The main lesson was that security needs to be considered when a system is designed, not added only after development. The room also discussed default passwords, exposed services, unverified dependencies, weak cryptography and AI-related risks.

### Room 3 — Insecure Data Handling

This room covered Cryptographic Failures, Injection and Software or Data Integrity Failures. I reviewed password hashing, prepared statements, unsafe input, template injection and the need to verify the origin and integrity of code, updates and data.

![TryHackMe OWASP Top 10 (2025) module showing the three rooms completed](screenshots/01-tryhackme-owasp-top-10-2025-all-rooms-completed.webp)

The three rooms connected the topics for me: who is allowed to access something, how the application is designed and configured, and how it handles information and user input.

## 7. What I learned and conclusion

This task helped me organise web application weaknesses into categories I can recognise during practical testing. Broken Access Control is about checking who can do what, Cryptographic Failures are about protecting sensitive information, and Injection is about treating user input safely.

Mapping DVWA modules to the OWASP list helped connect the lab exercises to broader application risks. The real-world examples also showed that these weaknesses are not only theoretical. I can use the categories when reviewing web requests, investigating repeated login failures or explaining a finding in a security report.

## 8. References

- OWASP Top 10 (2025) — https://owasp.org/Top10/2025
- TryHackMe OWASP Top 10 (2025) module — https://tryhackme.com/module/owasp-top-10-2025
- Damn Vulnerable Web Application — https://github.com/digininja/DVWA
- First American Financial — Krebs on Security, May 2019.
- Accenture — UpGuard, October 2017.
- SolarWinds — An Investigative Update of the Cyberattack.
- Adobe — Have I Been Pwned.
- Heartland Payment Systems — US Department of Justice, August 2009.
- Parler — Salt Security.
- 23andMe — NBC News, December 2023.
- Codecov — SecurityWeek.
- Target — US Senate Commerce Committee, A Kill Chain Analysis of the 2013 Target Data Breach.
- CVE-2020-16125 — Debian Security Tracker.