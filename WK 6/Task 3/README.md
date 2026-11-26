# Week 6, Task 3 — Capstone: Full Penetration Test & Professional Report

<p align="center">
  <img src="https://img.shields.io/badge/Task-03-404040?style=flat-square&labelColor=6366F1" alt="Task 3" />
  <img src="https://img.shields.io/badge/Focus-Full%20Penetration%20Test-404040?style=flat-square&labelColor=C00000" alt="Full penetration test" />
  <img src="https://img.shields.io/badge/Target-TryHackMe%20Basic%20Pentesting-404040?style=flat-square&labelColor=238F89" alt="TryHackMe Basic Pentesting" />
</p>

**Internship:** Ethical Hacking Internship, TechBiz Academy  
**Author:** Collins Okike  
**Target:** TryHackMe Basic Pentesting — `10.128.157.162` (address during my lab session)  
**Testing machine:** Kali Linux connected to the TryHackMe environment  
**Overall engagement risk:** High

## 1. Executive Summary

For my final practical task, I carried out a penetration test against the TryHackMe **Basic Pentesting** machine. I used the process I had practised during the internship: confirming the target, scanning its exposed services, checking the information those services revealed, and then testing whether the weaknesses could be used to obtain access.

The test showed how several small security mistakes can combine into a serious compromise. I found that the web server exposed a `/development` directory containing internal notes. The notes pointed to two user accounts and mentioned that one account had a weak password. I then used an unauthenticated SMB session to confirm the usernames and retrieve the password policy. That information helped me focus my SSH password test on the `jan` account. After recovering the password and logging in, I found that `kay`'s home directory and private SSH key were readable by another local user. I recovered the key, cracked its passphrase offline and was able to access the `kay` account as well.

The test resulted in five findings: service/version disclosure, an exposed development directory, anonymous SMB enumeration, a weak SSH password for `jan`, and an exposed private key and backup password file for `kay`. The first three findings provided information or helped identify accounts; the last two resulted in working account access. I rated the overall engagement High because an attacker who followed the same path could gain access to two user accounts through weak credentials and poor file permissions.

No software memory-corruption exploit was needed in this attack chain. The main problems were information exposure, weak authentication and access controls that allowed one account to reach another user's files.

## 2. Scope, target and rules

I confirmed the IP address assigned to my TryHackMe Basic Pentesting room and used that address as the scope for the exercise. The test was carried out from my Kali Linux environment connected to TryHackMe.

| Item | Detail |
|---|---|
| Target | TryHackMe Basic Pentesting room |
| Target IP during testing | `10.128.157.162` |
| Attacking machine | Kali Linux connected to the TryHackMe lab |
| Scope | The assigned Basic Pentesting target only |
| Authorisation | Testing carried out in the authorised TryHackMe training environment |
| Main stages | Reconnaissance, scanning, enumeration and exploitation |

The IP address belongs to the room instance I used during the exercise. TryHackMe may assign a different address when a new instance is started, so the address above is a record of this test and should not be assumed to remain active.

## 3. Methodology and tools

I kept the stages in order because each result helped decide what to check next.

| Stage | What I wanted to establish | Tools used |
|---|---|---|
| Reconnaissance | Confirm the assigned target and keep the test within scope | TryHackMe room details |
| Scanning | Identify open ports, services and version information | Nmap |
| Web enumeration | Check for directories and files that were not linked from the main page | Gobuster and browser |
| SMB enumeration | Check whether SMB allowed anonymous access and whether it disclosed users or policy information | enum4linux |
| Credential testing | Check the weak-password lead against the SSH service | Hydra and `rockyou.txt` |
| Account access and file-permission checks | Verify the first foothold and check whether sensitive files were readable | SSH, `find`, `ls`, `cat` |
| Offline key analysis | Extract and test the passphrase hash from the private key | ssh2john and John the Ripper |

I used the output from each stage to guide the next stage. For example, the Nmap scan showed that HTTP and SMB were available, Gobuster found the development directory on the web server, and a note inside that directory indicated that SMB had been configured. That made SMB enumeration a relevant next step rather than a random scan.

## 4. Scanning — F1: Open ports and service/version information disclosed (Low)

### 4.1 Run the Nmap version scan

I started by checking which services were exposed on the target and what versions they reported:

```bash
nmap -sV 10.128.157.162
```

The `-sV` option asks Nmap to probe discovered ports to identify the service and, where possible, its version. I used this because knowing that a port is open is only the first part of understanding the attack surface. Knowing whether it is running SSH, HTTP or SMB helps me decide what kind of enumeration is relevant.

![Nmap version scan showing the four open ports on the target](screenshots/01-nmap-version-scan-four-open-ports.png)

### 4.2 What I found

The scan identified four open TCP ports:

| Port | Service | Version information recorded | Why I checked it further |
|---:|---|---|---|
| 22 | SSH | OpenSSH 8.2p1 on Ubuntu | This became the service I tested after finding a weak-password lead. |
| 80 | HTTP | Apache httpd 2.4.41 on Ubuntu | I used the web server to look for exposed directories and files. |
| 139 | NetBIOS/SMB | Samba smbd 4 | SMB could reveal host or account information if it was poorly configured. |
| 445 | SMB | Samba smbd 4 | This was used for the SMB enumeration stage. |

Nmap's OS fingerprint also identified the host as Linux running an Ubuntu kernel. These results gave me three clear paths to investigate: HTTP for web content, SMB for network-share and account information, and SSH for remote login. The scan did not prove that any of these services was vulnerable by itself; it identified what was listening and helped me plan the next checks.

### 4.3 Finding assessment

I recorded this as **F1 — Open ports and service/version information disclosed (Low)**. Exposing a service banner does not automatically give an attacker access, and services need to be discoverable to function. However, version information can help an attacker narrow down which weaknesses to research. This is why I rated it Low rather than treating the scan output as a direct compromise.

## 5. Web enumeration — F2: Public `/development` directory exposes internal notes (Medium)

### 5.1 Look for directories that are not linked from the home page

Because Nmap showed Apache on port 80, I checked for web directories that were not obvious from the main page. I used Gobuster with the DirBuster medium directory list:

```bash
gobuster dir -u http://10.128.157.162 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

Gobuster tests words from the list as possible directory or file names and reports paths that the server responds to. The result included `/development` with HTTP status `301`.

![Gobuster finds the exposed development directory](screenshots/02-gobuster-finds-development-directory.png)

A `301` response is a redirect, not an error. It told me that the path existed and that the server was redirecting the browser, so I opened the directory to see what was available there.

### 5.2 Review the directory listing

The directory opened without asking me to authenticate. Apache displayed an index listing with two text files: `dev.txt` and `j.txt`.

![Directory listing of development showing dev.txt and j.txt](screenshots/03-development-directory-listing-dev-txt-j-txt.png)

This was a security issue because internal files were stored in a web-accessible location. A directory listing also made it easier to discover the filenames. I opened both files and reviewed the information they contained.

### 5.3 Read `dev.txt`

The first file was a developer note/changelog. It mentioned recent SMB configuration and stated that Apache Struts 2.5.12 had been tested on the server.

![Contents of the exposed dev.txt file](screenshots/04-development-dev-txt-contents.png)

The SMB reference gave me a reason to check the SMB service identified by Nmap. The Struts version was also worth noting because older Struts releases have had serious security issues, although I did not demonstrate a Struts exploit in this assessment. I kept that as technology information to investigate in a fuller engagement rather than claiming it was a confirmed vulnerability on this host.

### 5.4 Read `j.txt`

The second file contained an internal message from a staff member identified as `K` to another staff member identified as `J`. The message said that `J`'s password had been recovered easily during an audit of `/etc/shadow` and asked the recipient to change it to comply with the organisation's password policy.

![Contents of the exposed j.txt internal message](screenshots/05-development-j-txt-contents.png)

This was more useful than a normal software note because it gave me a specific lead: one of the accounts associated with the host was known to have a weak password. The file did not disclose the password itself, but it showed that sensitive staff communication was publicly readable and suggested a likely authentication weakness. I later correlated the initials in the message with the usernames found through SMB.

### 5.5 Finding assessment

I recorded this as **F2 — Public `/development` directory leaking internal notes (Medium)**. The directory did not give me a shell on its own, so I did not rate it High. However, it exposed internal information that directly helped guide the later account attack. Removing the directory from the public web root or protecting it with access controls would have prevented this information from being available to an unauthenticated visitor.

## 6. SMB enumeration — F3: Anonymous session reveals usernames and password policy (Low)

### 6.1 Enumerate the SMB service

The developer note had mentioned SMB, and Nmap had already shown ports 139 and 445 open. I used enum4linux to gather information from the SMB service:

```bash
enum4linux -a 10.128.157.162
```

The `-a` option requests a broad set of enumeration checks. I reviewed the output for host details, shares, session permissions, password policy and usernames.

![enum4linux target information and usernames](screenshots/06-enum4linux-target-information-usernames.jpg)

### 6.2 Check whether an anonymous session is allowed

One of the results showed that the target accepted a null or anonymous SMB session. In this context, a null session means the tool could make an SMB connection without supplying a normal username and password.

![enum4linux confirms that a null or anonymous session is permitted](screenshots/07-enum4linux-null-session-permitted.png)

This did not mean that every file on the system was available to an unauthenticated user. It meant that the server was willing to provide some SMB information without normal authentication. I continued to check exactly what could be retrieved through that session.

### 6.3 Review shares and the password policy

The enumeration included share information and a password-policy lookup.

![enum4linux share enumeration and password-policy lookup](screenshots/08-enum4linux-share-enumeration-password-policy.jpg)

The policy information showed a minimum password length of five characters and that complexity requirements were disabled.

![Password policy showing minimum length of five and complexity disabled](screenshots/09-enum4linux-password-policy-five-no-complexity.jpg)

A short minimum length and no complexity requirement make it easier for users to choose predictable passwords. A strong password policy should encourage longer passwords and block commonly used or compromised passwords. Password complexity rules alone are not enough, but in this case the minimum length of five was particularly weak.

### 6.4 Use RID cycling to identify local users

The tool then performed group and RID enumeration. A Relative Identifier (RID) is the part of a Windows-style security identifier that distinguishes one account or group from another within a security authority. In this lab, enum4linux was able to query the SMB service and resolve local account names through RID cycling.

![enum4linux beginning group and RID-cycling enumeration](screenshots/10-enum4linux-group-rid-cycling.jpg)

The completed results revealed three local usernames: `kay`, `jan` and `ubuntu`.

![RID cycling reveals kay, jan and ubuntu](screenshots/11-rid-cycling-users-kay-jan-ubuntu.jpg)

The usernames helped me connect the anonymous SMB results with the initials in `j.txt`. The note referred to `K` and `J`, and the enumeration gave me the matching account names `kay` and `jan`. Along with the weak policy information, this made the `jan` account a specific candidate for the next authorised check.

### 6.5 Finding assessment

I recorded this as **F3 — Usernames and password-policy information disclosed through anonymous SMB (Low)**. Usernames and policy details are useful to an attacker, but the information alone does not provide an authenticated session. The finding became more important when combined with F2, because the leaked note and the SMB enumeration pointed to the same two accounts and indicated which one had a weak password.

## 7. Exploitation — F4: Weak SSH password for `jan` (High)

### 7.1 Test the weak-password lead

After correlating the exposed message with the SMB usernames, I tested the `jan` account against SSH using the `rockyou.txt` wordlist. I used Hydra for the online wordlist test:

```bash
hydra -l jan -P /usr/share/wordlists/rockyou.txt ssh://10.128.157.162
```

Here, `-l jan` specified the single username and `-P` supplied the password list. The target service was SSH on the target IP. This was a targeted test of the account identified during the earlier enumeration, not a general scan across other hosts.

![Hydra recovers the weak SSH password for jan](screenshots/12-hydra-cracks-jan-ssh-password.jpg)

**My result:** Hydra reported a valid SSH credential for `jan` and recovered the password `armando`. The result confirmed that the weak-password information in the exposed note was actionable.

### 7.2 Verify the credential with SSH

I used the recovered credential to connect to the target:

```bash
ssh jan@10.128.157.162
```

After accepting the host key prompt and authenticating, I had an interactive shell as `jan`. I checked the session and listed the directory contents to confirm that I was working inside the target account.

![SSH login and interactive shell as jan](screenshots/13-ssh-shell-access-as-jan.jpg)

This was a significant change from the earlier findings. The web directory and SMB checks disclosed information, but this step provided an authenticated shell. I rated it High because a remote user could obtain a working account using a common wordlist password without needing a software exploit.

## 8. Exploitation — F5: Exposed SSH key and backup password file for `kay` (High)

### 8.1 Check for privilege-related files and home-directory permissions

Once I had a shell as `jan`, I checked for SUID files that might provide another path to access. I used:

```bash
find / -perm -4000 2>/dev/null
```

The `-perm -4000` test looks for files with the SUID permission bit set. The `2>/dev/null` part suppresses permission-denied messages so the output is easier to review. My recorded result showed standard snap-packaged system binaries rather than a custom SUID binary that I could use for this exercise.

I then checked the home directory:

```bash
ls -la /home/
```

The permissions on `/home/kay` allowed other local users to read and enter the directory (`drwxr-xr-x`). A home directory can be traversable when users need shared access, but private account files should not be readable by unrelated users.

![SUID check and permissions showing kay's home directory is browsable](screenshots/14-suid-check-kay-home-directory-permissions.png)

### 8.2 Inspect the files available under kay's home directory

I listed the directory as `jan`:

```bash
cd /home/kay
ls -la
```

The listing showed a backup file named `pass.bak` and a `.ssh` directory. The `.ssh` directory contained the `id_rsa` private-key file.

![kay's home directory listing with pass.bak and the .ssh private-key directory](screenshots/15-kay-home-listing-passbak-and-ssh-key.jpg)

The important issue was not only that another user's home directory could be browsed. The private SSH key itself was also readable from the `jan` account. Private keys are authentication material and should be restricted to the account that owns them. The presence of `pass.bak` was another concern because a backup file in the same location may retain sensitive information after it should have been removed.

### 8.3 Read and copy the private key for offline testing

From the `jan` shell, I read the private key file:

```bash
cat .ssh/id_rsa
```

The file contents could be copied to my Kali machine for offline passphrase analysis. I saved the key locally as `Kay_rsa` and corrected its local permissions:

```bash
chmod 600 Kay_rsa
```

The `600` permission gives the file read and write access to its owner only, with no permissions for the group or other users. SSH checks private-key file permissions and may refuse to use a key if other accounts can read it.

![kay's private SSH key was readable from the jan shell](screenshots/16-kay-private-ssh-key-from-jan-shell.png)

![Private key saved locally and permissions restricted with chmod 600](screenshots/17-save-kay-private-key-chmod-600.jpg)

This demonstrated how a file-permission mistake can let one user obtain another user's authentication material. The key was still protected by a passphrase, so possession of the key file alone was not enough for a successful login.

### 8.4 Extract and crack the SSH key passphrase hash

I first tried to use the private key to connect as `kay`. SSH requested the key's passphrase, so I could not use the key immediately. Instead, I extracted a password-cracking format from the key with `ssh2john`:

```bash
ssh -i Kay_rsa kay@10.128.157.162
ssh2john Kay_rsa > kay.txt
john kay.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

`ssh2john` converts supported SSH private-key data into a format that John the Ripper can test. It does not remove the passphrase or decrypt the key automatically. John then tests candidate passphrases from the wordlist against the extracted hash.

![John the Ripper cracks the passphrase protecting kay's SSH private key](screenshots/18-john-cracks-kay-key-passphrase.png)

The passphrase was recovered successfully. This connected the earlier password-hash exercise with a real step in the capstone: in Task 2 I tested a simple MD5 hash, while here I used the same general idea of testing guesses offline against a protected credential format.

### 8.5 Log in as kay and inspect the backup file

With the key passphrase recovered, I used the key to log in as `kay` and checked the backup file:

```bash
ssh -i Kay_rsa kay@10.128.157.162
cat pass.bak
```

I obtained a shell as `kay`. The `pass.bak` file contained:

```text
heresareallystrongpasswordthatfollowsthepasswordpolicy$$
```

![Confirmed shell access as kay and the contents of pass.bak](screenshots/19-kay-shell-and-passbak-contents.jpg)

This provided a second account foothold. I did not need to exploit a software flaw to reach it. The combination of a browsable home directory, a readable private key and a leftover backup password file exposed information that should have been restricted to the account owner.

### 8.6 Finding assessment

I recorded this as **F5 — Exposed SSH private key and backup password file for `kay` (High)**. The private key and backup file were accessible from the `jan` account, and the key's passphrase could be recovered with a wordlist. This resulted in access to the `kay` account. The issue demonstrates why home-directory permissions, private-key permissions and secure handling of backup files need to be checked together.

## 9. Findings summary and severity

I used the impact of each issue and how directly it enabled access to assign the severity ratings below.

| ID | Finding | Severity | Stage | Main impact |
|---|---|---|---|---|
| F1 | Open ports and service/version information disclosed | Low | Scanning | Helped identify the exposed services and narrow later checks. |
| F2 | Public `/development` directory exposed internal notes | Medium | Web enumeration | Disclosed internal information and a lead about a weak account password. |
| F3 | Anonymous SMB session disclosed usernames and password policy | Low | SMB enumeration | Revealed `kay`, `jan` and `ubuntu`, as well as a weak minimum-length policy. |
| F4 | Weak SSH password for `jan` | High | Exploitation | Allowed a remote SSH login and an interactive shell as `jan`. |
| F5 | Exposed SSH private key and backup password file for `kay` | High | Post-access enumeration and exploitation | Allowed access to `kay` after the key passphrase was recovered. |

The low-rated issues still mattered because they contributed information that made the later tests more focused. F2 and F3 helped me identify the likely account and understand why a wordlist test could succeed. F4 gave me the first shell, and F5 showed how a file-permission weakness could expose a second account after the first foothold had been gained.

## 10. Remediation recommendations

| Finding | Recommended fix | Why it matters |
|---|---|---|
| F1 — Service/version disclosure | Keep exposed services patched and supported. Disable unnecessary services and reduce version details in banners where practical. | This reduces the exposed attack surface and makes it harder to identify outdated software from a quick scan. |
| F2 — Public `/development` directory | Remove internal development material from the public web root. Disable directory listing and require authentication for any internal content that must remain online. | Internal notes should not be downloadable by unauthenticated visitors. |
| F3 — Anonymous SMB enumeration | Disable null/anonymous SMB access where it is not required. Restrict share permissions and review what account and policy information can be queried without authentication. Enforce a stronger minimum password length and block common passwords. | Anonymous users should not be able to enumerate account information or retrieve internal password-policy details unnecessarily. |
| F4 — Weak SSH password | Require long, unique passwords or use SSH public-key authentication. Disable password authentication where operationally possible, and add MFA or other additional controls for remote access. Monitor failed login attempts and apply appropriate rate limiting. | A common wordlist should not be enough to obtain a remote shell. |
| F5 — Exposed key and backup file | Restrict home directories and `.ssh` directories to their owners. Set private-key permissions to owner-only, protect private keys with strong unique passphrases, and securely remove backup files containing credentials. Review group membership and file ownership regularly. | One user should not be able to read another user's authentication material or password backups. |

I would prioritise F4 and F5 because they resulted in account access. I would also fix F2 and F3 early because they provided the information that made the credential attack easier. Fixing only the final login weaknesses would leave the information-disclosure path available for another attacker to use.

## 11. Conclusion

This capstone brought together the main skills I practised during the internship. I confirmed the target, used Nmap to identify its services, used Gobuster and a browser to find exposed web content, and used enum4linux to understand what the SMB service disclosed. I then tested the weak-password lead with Hydra, confirmed the SSH login as `jan`, and followed the exposed file permissions to obtain the private key and backup file associated with `kay`.

The exercise showed me how an attack can develop through a chain of weaknesses. A public directory disclosed internal notes, anonymous SMB enumeration confirmed the account names, a weak SSH password provided the first foothold, and poor home-directory permissions exposed another user's private key. Each issue made the next step easier. The most important lesson was that security depends on more than patching software: access controls, credential practices and the way internal files are stored can be just as important.

The engagement resulted in two user-account footholds and five documented findings, with an overall risk rating of High. The recommendations focus on removing exposed internal files, restricting anonymous SMB access, strengthening SSH authentication and correcting ownership and permissions on private user data.