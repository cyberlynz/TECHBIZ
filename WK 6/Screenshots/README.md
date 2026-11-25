# Week 6 — Screenshot Gallery

<p align="center">
  <img src="https://img.shields.io/badge/Week-06-404040?style=flat-square&labelColor=238F89" alt="Week 6" />
  <img src="https://img.shields.io/badge/Collection-26%20Screenshots-404040?style=flat-square&labelColor=0070C0" alt="26 screenshots" />
</p>

These are the screenshots I took while completing the three Week 6 tasks. Each image is also included in the matching task write-up beside the step or result it supports.

## Task 1 — Metasploit

- [msfconsole search results for the vsftpd 2.3.4 backdoor module](../Task%201/screenshots/01-msfconsole-search-vsftpd.webp)
- [Metasploit options, RHOSTS/LHOST settings and exploit output](../Task%201/screenshots/02-msfconsole-options-rhosts-lhost-exploit.webp)
- [whoami and id output confirming root access](../Task%201/screenshots/03-meterpreter-shell-root-whoami-id.webp)
- [TryHackMe Metasploit Introduction room completed](../Task%201/screenshots/04-tryhackme-metasploit-introduction-completed.webp)
- [TryHackMe Blue room completed](../Task%201/screenshots/05-tryhackme-blue-completed.webp)

## Task 2 — Password Cracking (Hashes)

- [John the Ripper cracking the sample MD5 hash with rockyou.txt](../Task%202/screenshots/01-john-the-ripper-md5-hash-rockyou.webp)
- [TryHackMe Crack the Hash room completed](../Task%202/screenshots/02-tryhackme-crack-the-hash-completed.webp)

## Task 3 — Capstone: Full Penetration Test

- [Nmap version scan showing four open ports](../Task%203/screenshots/01-nmap-version-scan-four-open-ports.webp)
- [Gobuster finding the development directory](../Task%203/screenshots/02-gobuster-finds-development-directory.webp)
- [Development directory listing with dev.txt and j.txt](../Task%203/screenshots/03-development-directory-listing-dev-txt-j-txt.webp)
- [Contents of development dev.txt](../Task%203/screenshots/04-development-dev-txt-contents.webp)
- [Contents of development j.txt](../Task%203/screenshots/05-development-j-txt-contents.webp)
- [enum4linux target information and usernames](../Task%203/screenshots/06-enum4linux-target-information-usernames.webp)
- [enum4linux null/anonymous session result](../Task%203/screenshots/07-enum4linux-null-session-permitted.webp)
- [enum4linux share enumeration and password policy](../Task%203/screenshots/08-enum4linux-share-enumeration-password-policy.webp)
- [Password policy: minimum length five, complexity disabled](../Task%203/screenshots/09-enum4linux-password-policy-five-no-complexity.webp)
- [enum4linux group and RID-cycling enumeration](../Task%203/screenshots/10-enum4linux-group-rid-cycling.webp)
- [RID cycling revealing kay, jan and ubuntu](../Task%203/screenshots/11-rid-cycling-users-kay-jan-ubuntu.webp)
- [Hydra recovering jan's SSH password](../Task%203/screenshots/12-hydra-cracks-jan-ssh-password.webp)
- [Confirmed SSH shell access as jan](../Task%203/screenshots/13-ssh-shell-access-as-jan.webp)
- [SUID check and kay home-directory permissions](../Task%203/screenshots/14-suid-check-kay-home-directory-permissions.webp)
- [Kay home listing with pass.bak and .ssh directory](../Task%203/screenshots/15-kay-home-listing-passbak-and-ssh-key.webp)
- [Kay private SSH key read from jan's shell](../Task%203/screenshots/16-kay-private-ssh-key-from-jan-shell.webp)
- [Private key saved locally and chmod 600 applied](../Task%203/screenshots/17-save-kay-private-key-chmod-600.webp)
- [John cracking the passphrase for kay's private SSH key](../Task%203/screenshots/18-john-cracks-kay-key-passphrase.webp)
- [Confirmed kay shell and pass.bak contents](../Task%203/screenshots/19-kay-shell-and-passbak-contents.webp)
