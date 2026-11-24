# Week 4, Task 3 — OWASP Top 10 (2025): Web Vulnerabilities Overview

<p align="center">
  <img src="https://img.shields.io/badge/Task-03-404040?style=flat-square&labelColor=6366F1" alt="Task 3" />
  <img src="https://img.shields.io/badge/Reference-OWASP%20Top%2010%202025-404040?style=flat-square&labelColor=C00000" alt="OWASP Top 10 2025" />
  <img src="https://img.shields.io/badge/Practice-DVWA%20%7C%20TryHackMe-404040?style=flat-square&labelColor=238F89" alt="DVWA and TryHackMe" />
</p>

**Internship:** Ethical Hacking Internship, TechBiz Academy  
**Author:** Collins Okike  
**Reference list:** OWASP Top 10 (2025)

## 1. Introduction

OWASP stands for the Open Web Application Security Project. It publishes free guidance and tools about web application security. In this task I used the OWASP Top 10 (2025) list to study ten web application risk categories, examine Broken Access Control, Cryptographic Failures and Injection in more detail, record one real-world example for each category, and map DVWA modules to the closest OWASP categories. I also completed the TryHackMe OWASP Top 10 (2025) module and watched the recommended videos.

## 2. The OWASP Top 10 (2025) — one-line overview

| ID | Category | Meaning recorded in my notes |
|---|---|---|
| A01 | Broken Access Control | Users can access data or functions they are not allowed to use. The 2025 category also includes SSRF. |
| A02 | Security Misconfiguration | Unsafe settings, default passwords, public storage or unnecessary features left enabled. |
| A03 | Software Supply Chain Failures | Trusting libraries, updates and build tools that are old, vulnerable or tampered with. |
| A04 | Cryptographic Failures | Sensitive data is not adequately protected because encryption is missing, weak or used incorrectly. |
| A05 | Injection | Untrusted input is treated as part of a query or command, including SQL injection, command injection and XSS. |
| A06 | Insecure Design | The application has a security weakness built into its design, even when the code has no implementation bug. |
| A07 | Authentication Failures | Weak login or session handling allows an attacker to impersonate a legitimate user. |
| A08 | Software or Data Integrity Failures | Code, updates or data are trusted without checking that they have not been changed. |
| A09 | Security Logging & Alerting Failures | Attacks are not logged or alerted on, so defenders may not notice or respond in time. |
| A10 | Mishandling of Exceptional Conditions | Poor error handling or fail-open behaviour can expose information or bypass a security check. |

### 2.1 Changes from the 2021 list noted in the report

- SSRF, which appeared as A10 in 2021, was merged into A01 Broken Access Control.
- A03 Software Supply Chain Failures and A10 Mishandling of Exceptional Conditions were added as new categories.
- Security Misconfiguration moved from fifth place to second place.
- Cryptographic Failures and Injection moved to A04 and A05, and Insecure Design moved to A06.
- Some category names changed, including Security Logging & Alerting and Authentication Failures.

## 3. Three Categories Studied in Detail

### 3.1 A01 — Broken Access Control

Access control determines what each user is allowed to do. It is broken when those checks are missing or can be bypassed. In the 2025 list, this category also includes Server-Side Request Forgery (SSRF).

**How it can happen:**

- Changing an ID in a URL to access another person's data (IDOR), such as changing /invoice?id=1001 to /invoice?id=1002.
- A normal user opening an administrator page by typing its address directly (forced browsing).
- Changing a hidden field or cookie to try to obtain higher privileges.
- Supplying a URL that the server fetches, then using SSRF to reach internal systems accessible to the server.

**Impact:** An attacker may read, modify or delete other users' data, or take over administrative functions.  
**Prevention recorded:** Deny access by default, check permissions on the server for every request, use appropriate roles, validate and restrict URLs fetched by the server, and log access-control failures.

### 3.2 A04 — Cryptographic Failures

The report notes that this category was previously called Sensitive Data Exposure. The updated name focuses on the underlying cause: missing, weak or incorrectly implemented cryptography. Data at risk may include passwords, payment-card data, health records or personal details.

**How it can happen:**

- Sending data over plain HTTP rather than HTTPS.
- Storing passwords with weak hashes such as MD5 or SHA-1, or without a salt.
- Using old or weak encryption algorithms or hard-coding cryptographic keys in source code.
- Failing to check certificates or using default or guessable keys.

**Impact:** If a database is exposed, an attacker may read or crack the data and reuse passwords on other services.  
**Prevention recorded:** Use HTTPS throughout, store passwords with strong salted password-hashing functions such as bcrypt, scrypt or Argon2, use modern encryption, manage keys safely and avoid storing sensitive data that is not needed.

### 3.3 A05 — Injection

Injection occurs when user input is sent to an interpreter, such as a database, operating system, browser or template engine, and is treated as a command rather than ordinary data. The report includes XSS in this category.

Common types include SQL injection, OS command injection, template injection (SSTI) and XSS. The report uses these examples:

~~~text
' OR '1'='1
~~~

A vulnerable login form that joins strings into a SQL query may treat this input as a condition that is true for every row.

~~~text
127.0.0.1; whoami
~~~

In a vulnerable command-injection field, the semicolon can separate the expected input from an additional command.

**Impact:** Data theft, login bypass, data loss or even full control of the server.  
**Prevention recorded:** Use parameterized queries or prepared statements, validate input on the server, escape output to prevent XSS and give database accounts only the permissions they need.

## 4. Real-World Examples Recorded for the Ten Categories

The following examples and incident details are reproduced from the report's research notes and reference list.

| ID | Example | Year | Incident summary recorded |
|---|---|---:|---|
| A01 | First American Financial | 2019 | Sequential document links and a missing login check allowed other people's mortgage documents to be viewed by changing a URL digit; about 885 million files were exposed. |
| A02 | Accenture | 2017 | Four AWS S3 buckets were left publicly accessible and contained internal data, including cloud credentials and keys. |
| A03 | SolarWinds | 2020 | Malicious code was inserted into the Orion software build and distributed as a normal update; up to about 18,000 customers may have downloaded affected versions. |
| A04 | Adobe | 2013 | Customer passwords were encrypted with 3DES in ECB mode rather than hashed and salted, and password hints were stored in plaintext; about 150 million accounts were in the leaked data. |
| A05 | Heartland Payment Systems | 2008 | SQL injection was used to gain access to the network and install sniffer software; the US Department of Justice said data from more than 130 million cards was stolen. |
| A06 | Parler | 2021 | Sequential post IDs, an unauthenticated public API and no rate limiting allowed tens of terabytes of posts, including deleted posts, to be downloaded. |
| A07 | 23andMe | 2023 | Credential stuffing accessed about 14,000 accounts; information for about 6.9 million people was exposed through the DNA Relatives feature. |
| A08 | Codecov | 2021 | A leaked cloud-storage key was used to alter the Bash Uploader script from late January to April 2021; affected build pipelines could send environment variables such as tokens and keys. |
| A09 | Target | 2013 | FireEye detection alerts were not acted on in time; about 40 million payment cards were stolen, according to the cited US Senate report. |
| A10 | gdm3 login manager (CVE-2020-16125) | 2020 | If the accounts service could not be reached in time, the login manager could start the new-account setup program; on Ubuntu this could let a local user create a privileged account. |

## 5. Mapping DVWA Modules to the OWASP Top 10 (2025)

DVWA is intentionally vulnerable and is used as a practice application. I mapped each module to the closest OWASP 2025 category. Some modules could relate to more than one category; the notes below retain the report's primary mapping and secondary observations.

| DVWA module | OWASP 2025 | Reason recorded |
|---|---|---|
| Brute Force | A07 | The login has no protection against repeated password guessing. |
| Command Injection | A05 | User input is passed to the system shell. |
| CSRF | A01 | The application does not check that the request was genuinely intended by the user; the report maps it to access control. |
| File Inclusion | A05 | A page parameter decides which file loads; path traversal can also relate to A01. |
| File Upload | A06 | The upload rules were not designed safely; it can also relate to A02. |
| Insecure CAPTCHA | A07 | The CAPTCHA check can be bypassed, weakening the login/reset process. |
| SQL Injection | A05 | User input goes directly into the SQL query. |
| SQL Injection (Blind) | A05 | The same injection weakness is inferred from true/false behaviour rather than directly displayed results. |
| Weak Session IDs | A07 | Predictable session IDs may allow session takeover. |
| XSS (Reflected) | A05 | Input is returned in the page and runs as script. |
| XSS (Stored) | A05 | The script is stored on the server and runs for users who open the page. |
| XSS (DOM) | A05 | Page JavaScript handles URL input in a way that causes script execution. |
| CSP Bypass | A02 | The Content Security Policy is configured in a way that can be bypassed. |
| JavaScript | A06 | The application trusts security checks carried out in the browser, which can be changed. |
| Authorisation Bypass | A01 | A user can open pages or data intended for another role. |
| Open HTTP Redirect | A01 | The application redirects to an address supplied by the user; the report also notes a relationship to A06. |
| Cryptography | A04 | Demonstrates weak or incorrectly used encryption; the report notes this module is present only in newer DVWA versions. |

The report notes that DVWA does not have direct modules for A03, A08, A09 and A10. These areas depend more on the wider system, including dependencies, build pipelines, monitoring and error handling, so they are harder to represent in a small intentionally vulnerable web application.

## 6. TryHackMe: OWASP Top 10 (2025) Module

The module on the account was divided into three rooms: IAAA Failures, Application Design Flaws and Insecure Data Handling. I completed all three rooms.

### 6.1 OWASP Top 10 2025: IAAA Failures

This room explained Identity, Authentication, Authorization and Accountability (IAAA), and how each part helps verify users and their actions. It covered Broken Access Control, Authentication Failures and Security Logging & Alerting Failures.

For Broken Access Control, the room highlighted the need for the server to check who can access each resource on every request. Changing an ID in a URL to view another user's data is an IDOR and, when the user's role remains the same, horizontal privilege escalation. Authentication weaknesses included username enumeration, weak passwords without lockout, flawed login or registration logic and poor session handling. The logging section showed that defenders need records of who did what, when and from where. The room's fixes included server-side checks, rate limiting, session rotation, central logging and alerts.

### 6.2 OWASP Top 10 2025: Application Design Flaws

This room covered Security Misconfiguration, Software Supply Chain Failures, Cryptographic Failures and Insecure Design. Its main message was that security needs to be considered from the beginning of system design, with clear requirements, realistic threat assumptions and vetted components.

The report notes examples such as default passwords, exposed services, open cloud storage and verbose errors for misconfiguration; compromised or unverified libraries, updates or AI models for supply-chain failures; weak algorithms and hard-coded keys for cryptographic failures; and flawed application logic or architecture for insecure design. The room also introduced AI-related risks such as prompt injection and blindly trusting model output.

### 6.3 OWASP Top 10 2025: Insecure Data Handling

This room covered Cryptographic Failures, Injection, and Software or Data Integrity Failures. It reinforced the use of strong, vetted cryptography and slow password-hashing functions such as bcrypt, scrypt and Argon2. It also covered SQL injection, command injection, AI prompts and SSTI, with parameterized queries as a key prevention measure. The integrity section explained why trust boundaries and checks such as checksums matter, including in CI/CD pipelines. The practical labs included weak note encryption, SSTI and a Python deserialization attack.

### 6.4 How the Three Rooms Connect

The three rooms move from identity and access (who is allowed to do what), to the way systems are designed and configured, and then to how applications handle data and input. Together, they help me consider where an attack may begin, what defenders need to log and how to describe a finding in a report.

![TryHackMe OWASP Top 10 2025 module showing the three rooms completed](screenshots/01-tryhackme-owasp-top-10-2025-all-rooms-completed.webp)

## 7. Conclusion

This task helped me see how many web application attacks come from recurring design, configuration and coding mistakes. Broken Access Control, Cryptographic Failures and Injection were the three categories I studied in more detail. Mapping DVWA modules to the OWASP list connected practical lab weaknesses to broader risk categories, while the real-world examples showed how these issues can affect organisations. I also completed the three rooms in the TryHackMe OWASP Top 10 (2025) module.

As a future security analyst, I can use these categories to understand patterns such as SQL injection attempts in web logs or repeated login failures that may indicate brute force or credential stuffing.

## 8. References

- OWASP Top 10:2025 — https://owasp.org/Top10/2025
- TryHackMe OWASP Top 10 (2025) module — https://tryhackme.com/module/owasp-top-10-2025
- Damn Vulnerable Web Application (DVWA) — https://github.com/digininja/DVWA
- First American Financial — Krebs on Security, May 2019: https://krebsonsecurity.com/2019/05/first-american-financial-corp-leaked-hundreds-of-millions-of-title-insurance-records/
- Accenture — UpGuard, October 2017: https://upguard.com/breaches/cloud-leak-accenture
- SolarWinds — An Investigative Update of the Cyberattack: https://www.solarwinds.com/blog/an-investigative-update-of-the-cyberattack
- Adobe breach — Have I Been Pwned: https://haveibeenpwned.com/breach/Adobe
- Heartland Payment Systems — US Department of Justice, August 2009: https://www.justice.gov/opa/pr/2009/August/09-crm-810.html
- Parler — Salt Security: https://salt.security/blog/unpacking-the-parler-data-breach
- 23andMe — NBC News, December 2023: https://www.nbcnews.com/tech/security/23andme-hackers-gained-access-data-69-million-people-rcna127990
- Codecov — SecurityWeek: https://www.securityweek.com/codecov-bash-uploader-dev-tool-compromised-supply-chain-hack
- Target — US Senate Commerce Committee, A Kill Chain Analysis of the 2013 Target Data Breach (March 2014): https://www.commerce.senate.gov
- CVE-2020-16125 / gdm3 — Debian Security Tracker: https://security-tracker.debian.org/tracker/CVE-2020-16125