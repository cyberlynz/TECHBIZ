# Week 6, Task 2 — Password Cracking: Hashes (John / Hashcat)

<p align="center">
  <img src="https://img.shields.io/badge/Task-02-404040?style=flat-square&labelColor=6366F1" alt="Task 2" />
  <img src="https://img.shields.io/badge/Tool-John%20the%20Ripper-404040?style=flat-square&labelColor=C00000" alt="John the Ripper" />
  <img src="https://img.shields.io/badge/Practice-Hash%20Cracking-404040?style=flat-square&labelColor=238F89" alt="Hash cracking" />
</p>

**Internship:** Ethical Hacking Internship, TechBiz Academy  
**Author:** Collins Okike  
**Attacker environment:** Kali Linux  
**Wordlist:** /usr/share/wordlists/rockyou.txt

## 1. Introduction

This task focused on password hashing and password cracking. I learned how passwords are stored as hashes, how tools such as John the Ripper and Hashcat use wordlists to recover a matching plaintext password, and why hashing is different from encryption. I created a sample MD5 hash, saved it to a file and used John the Ripper with rockyou.txt to test whether the original password could be recovered.

## 2. Objectives

- Understand how password hashing works, including MD5, SHA and bcrypt.
- Create a sample hash and confirm I can reproduce it.
- Use John the Ripper or Hashcat to run a wordlist attack against a hash.
- Verify that a recovered password matches the original plaintext.
- Explain the difference between hashing and encryption.

## 3. Understanding Password Hashing

### 3.1 What hashing does

Before this task, I thought hashing and encryption were almost the same thing. I learned that hashing is designed to be one-way: the password is passed through a function and produces a hash that is not meant to be converted back into the original password. A system stores the hash and compares a newly entered password's hash with the stored value when someone logs in.

### 3.2 Common hash types

| Algorithm | Output length | Notes from my research |
|---|---|---|
| MD5 | 128-bit (32 hexadecimal characters) | Fast and broken for security use, although it is still found in older systems. |
| SHA-1 | 160-bit (40 hexadecimal characters) | Faster than SHA-256 and considered weak for security use. |
| SHA-256 | 256-bit (64 hexadecimal characters) | Stronger than MD5 and SHA-1, but still fast and not designed for password storage by itself. |
| bcrypt | Variable, salted | Deliberately slow and includes a salt, making it suitable for password storage. |

MD5 and SHA-1 are fast, which makes large numbers of guesses possible. bcrypt is designed to take more time per guess, which makes wordlist and brute-force attacks more expensive.

### 3.3 Cracking techniques

| Technique | How it works |
|---|---|
| Dictionary / wordlist attack | Hashes each word in a wordlist and compares it with the target hash. |
| Brute-force attack | Tries every possible character combination. It can take a long time as the possible combinations increase. |
| Rule-based attack | Applies changes to words in a wordlist, such as changing capitalisation or adding numbers. |

I mainly used the dictionary method in this practical because John the Ripper can use rockyou.txt as its wordlist.

## 4. Lab Setup

For the sample hash, I used the Kali Linux terminal. For the TryHackMe exercise, I used the TryHackMe AttackBox. The wordlist supplied with Kali was located at /usr/share/wordlists/rockyou.txt.

## 5. Creating and Cracking a Sample Hash

### 5.1 Creating the hash

I first generated an MD5 hash from a known plaintext password so I could check whether the cracking process recovered the same value:

~~~bash
echo -n 'col12345' | md5sum
~~~

This produced the hash:

~~~text
a65d21fe25907792ccbd9a2c1b18b967
~~~

I saved the hash to hash.txt, then ran John the Ripper with the Raw-MD5 format and the rockyou.txt wordlist:

~~~bash
echo 'a65d21fe25907792ccbd9a2c1b18b967' > hash.txt
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
~~~

John identified the hash as Raw-MD5, loaded it and started the wordlist attack. The report recorded a rate of roughly 2,338 candidate passwords per second. John found a match almost immediately and completed the session.

![John the Ripper cracking the sample MD5 hash with rockyou.txt](screenshots/01-john-the-ripper-md5-hash-rockyou.jpg)

**My result:** John cracked the hash in under 3 seconds and recovered col12345, which matched the plaintext I used when I created the hash.

### 5.2 What I learned

John did not reverse the MD5 hash. It hashed each candidate from the wordlist using MD5 and checked whether the result matched the target hash. This exercise showed me how quickly a weak password with a simple number pattern can be recovered when it is present in a common wordlist.

## 6. TryHackMe — Crack the Hash

I completed the free [Crack the Hash room](https://tryhackme.com/room/crackthehash). The room involved identifying hash types from their length and format, then using the appropriate wordlist attack with Hashcat or John the Ripper. The hashes included unsalted MD5, SHA-1 and SHA-256, a bcrypt hash, and two salted hashes where the salt had to be combined with the hash correctly before Hashcat could process them.

![TryHackMe Crack the Hash room completed](screenshots/02-tryhackme-crack-the-hash-completed.png)

## 7. Hashing vs Encryption

The main difference I learned is that hashing is one-way by design. Cracking a hash does not decrypt it; a tool guesses a password, hashes that guess using the same algorithm and checks whether the result matches. This is why common passwords can be recovered quickly, especially when the system uses a fast hash such as MD5.

Encryption is different because it is reversible with the correct key. Hashing is used for verification and integrity, while encryption is used when information needs to be protected and later read again. For password storage, a unique salt and a deliberately slow algorithm such as bcrypt are important because they make large-scale guessing more difficult.

## 8. Remediation

Based on what I learned in this task, I would recommend:

- Use a slow, salted password-hashing algorithm such as bcrypt, scrypt or Argon2 instead of fast algorithms such as MD5 or SHA-1.
- Encourage strong, unique passwords so common wordlists like rockyou.txt are less likely to contain them.
- Give every password its own unique salt so attackers cannot reuse precomputed hash tables across accounts.
- Add protections such as account lockout, rate limiting and multi-factor authentication so a recovered password alone is not enough to access an account.

## 9. Conclusion

Before this task, I did not fully understand why hashing was different from encryption. Cracking a sample hash myself helped me understand that the process is about guessing a password and checking its hash, not reversing the hash. Between the sample exercise and the TryHackMe Crack the Hash room, I practised identifying hash types and using John the Ripper. I also understood why fast algorithms such as MD5 are not suitable for password storage and why unique salts and slower algorithms matter.
