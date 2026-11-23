# Task 2 — Cross-Site Scripting (XSS)

**Student:** Collins  
**Internship:** Ethical Hacking Internship, TechBiz Academy  
**Task:** Week 5 · Task 2  
**Lab target:** DVWA, Low security

## Objective

I tested Reflected XSS and Stored XSS in DVWA, compared how the payloads behaved, and used a cookie-reading proof of concept to understand the impact of JavaScript executing in the page's origin.

## Lab setup

- Started DVWA in my Kali lab and logged in through the browser.
- Set the DVWA security level to **Low**.
- Opened the XSS (Reflected) and XSS (Stored) modules.

## Practical steps and results

### 1. Reflected XSS

Payload:

```html
<script>alert(1)</script>
```

I entered the payload in the reflected page's name field. An alert containing the number 1 appeared. The payload was carried in the URL's `name` parameter and returned in the response, rather than being saved by the application.

### 2. Stored XSS

I used the guestbook form:

- **Name:** `Test`
- **Message:** `<script>alert('Stored XSS')</script>`

The alert appeared after I submitted the guestbook entry. I reopened the Stored XSS page without submitting another entry, and the alert appeared again. This showed that the payload had been stored and was executed when the page displayed the saved entry. I then used the guestbook's Clear Guestbook control to remove the test entry.

### 3. Demonstrate cookie exposure

In the reflected page, I tested:

```html
<script>alert(document.cookie)</script>
```

The popup displayed the `security=low` cookie and the `PHPSESSID` session cookie. I kept this test inside my own lab and did not transmit the cookie elsewhere. The result demonstrated that JavaScript could read the session cookie in this lab configuration.

## Reflected XSS compared with Stored XSS

| Detail | Reflected XSS | Stored XSS |
|---|---|---|
| Payload location | In the request, commonly a URL parameter | Saved by the application, for example in a database |
| How it runs | Returned in the response to a crafted request | Runs when a page displays the stored content |
| User interaction | A victim generally has to open the crafted link | A visitor can be affected by loading the affected page |
| Persistence | Not saved by the application | Remains until the stored content is removed |
| DVWA example | XSS (Reflected) name parameter | XSS (Stored) guestbook message |

The report also explains DOM-based XSS, where client-side JavaScript places untrusted data into the page and the vulnerable flow can occur entirely in the browser.

## TryHackMe practice

I completed the [XSS room](https://tryhackme.com/room/axss) at 100%. The room covered Reflected XSS, Stored XSS and DOM-based XSS using the training applications described in my report.

## Security impact

Depending on the vulnerable page and the victim's permissions, XSS can be used to:

- Read accessible session data when cookies are not protected with HttpOnly.
- Perform actions in the victim's authenticated browser session.
- Display deceptive content or alter the page.
- Redirect users to malicious sites.
- Capture keystrokes entered into the affected page.

## Remediation

- Encode output for the correct HTML/JavaScript context so untrusted input is rendered as text.
- Validate and allow-list input where practical; do not rely on filtering alone.
- Deploy a Content Security Policy as an additional defence.
- Set session cookies with HttpOnly, Secure and appropriate SameSite attributes.
- Use frameworks that escape output automatically.
- Avoid unsafe DOM sinks such as assigning untrusted input to `innerHTML`; prefer `textContent`.
- Use a WAF as an additional layer rather than a substitute for secure application code.
- Monitor requests for suspicious strings such as `<script>`, `onerror=` and URL-encoded equivalents.

## Verification at DVWA Impossible security

I changed DVWA to **Impossible** and submitted the same reflected payload. No alert appeared; the page displayed `<script>alert(1)</script>` as plain text. This demonstrated output encoding in the tested page. The report also notes the presence of a `user_token` in the URL at this security level.

## Evidence

The submitted 10-page PDF report contains the practical screenshots. The following page references identify the evidence in that source report:

| Evidence | Source report page |
|---|---:|
| DVWA security level set to Low | 3 |
| Reflected XSS alert popup and response | 4 |
| Stored XSS alert after guestbook submission and page reload | 5–6 |
| Cookie popup showing PHPSESSID | 7 |
| TryHackMe XSS room completion | 8 |
| Same payload displayed as text at DVWA Impossible | 10 |

**Original report:** `Week5_Task2_XSS_Collins.pdf` (10 pages; supplied separately in this conversation).

## Conclusion

I confirmed both reflected and stored XSS in the DVWA Low security environment. The reload test made the persistence of Stored XSS clear, while the cookie test demonstrated why session-cookie protection matters. Testing the same payload at Impossible security showed how output encoding prevents the browser from executing the injected script.
