# Week 1, Task 3 — Building a Hacking Lab: VirtualBox and Kali Linux

<p align="center">
  <img src="https://img.shields.io/badge/Task-03-404040?style=flat-square&labelColor=6366F1" alt="Task 3" />
  <img src="https://img.shields.io/badge/Ver-VirtualBox%207.2-0070C0?style=flat-square&labelColor=000000&logo=virtualbox&logoColor=white" alt="VirtualBox version 7.2" />
  <img src="https://img.shields.io/badge/OS-Kali%20Linux-E87500?style=flat-square&labelColor=000000&logo=kalilinux&logoColor=white" alt="Kali Linux" />
</p>

**Internship:** Ethical Hacking, TechBiz Academy  
**Author:** Collins Okike

## 1. Introduction

For this task, I set up a basic cybersecurity lab on my Windows 10 computer. I used Oracle VirtualBox to run Kali Linux in a virtual machine, then checked the system from the Kali terminal and completed the TryHackMe Linux Fundamentals Part 1 room.

## 2. Tools I used

| Tool | Purpose |
|---|---|
| Oracle VirtualBox 7.2.16 | To run Kali Linux as a virtual machine |
| Kali Linux VirtualBox image | The Linux environment for the lab |
| 7-Zip | To extract the downloaded VM files |
| Kali Terminal | To run Linux commands and check the network configuration |
| TryHackMe — Linux Fundamentals Part 1 | To practise basic Linux commands |

## 3. Installing VirtualBox

I downloaded VirtualBox 7.2.16 and ran the installer on my Windows 10 computer. During installation, the setup window showed the support-driver installation step.

![VirtualBox installer running](evidence/01-virtualbox-installer.jpg)

After installation, I opened VirtualBox Manager.

![VirtualBox Manager on first launch](evidence/02-virtualbox-first-launch.jpg)

## 4. Importing and starting Kali Linux

I downloaded the pre-built Kali Linux VirtualBox image, extracted it with 7-Zip and opened the existing `.vbox` file in VirtualBox Manager. After importing the machine, I checked that Kali appeared in the list of virtual machines.

![Kali Linux virtual machine in VirtualBox Manager](evidence/03-virtualbox-kali-vm-manager.jpg)

I started the VM and logged in using the credentials recorded in my report: `kali` / `kali`. The Kali desktop loaded successfully.

![Kali Linux desktop after login](evidence/04-kali-linux-desktop.jpg)

I then opened the terminal to start checking the system.

![Kali terminal open](evidence/05-kali-terminal.jpg)

## 5. Commands I ran

### Check the current user

```bash
whoami
```

The output was `kali`, which confirmed the account I was using in the terminal.

### Check network interfaces

```bash
ip a
```

This command displayed the available network interfaces and their addressing information. The values recorded in my report were:

| Field | Result |
|---|---|
| Interface | `eth0` |
| IPv4 address | `10.0.0.3/24` |
| MAC address | `08:00:27:63:b0:05` |
| IPv6 address | `fe80::a413:d56d:1797:240/64` |
| Loopback address | `127.0.0.1` |

![Kali command output and network details](evidence/06-kali-command-checks.jpg)

The `lo` interface is the loopback interface, which lets the computer communicate with itself. The `10.0.0.3/24` address is a private IPv4 address. These are the values from this lab session, so they may be different in another session or network setup.

### Refresh package information

```bash
sudo apt update
```

I used this command to refresh the list of available packages from the configured Kali repositories. It updates package information; it does not upgrade installed packages by itself.

![Kali package update output](evidence/07-apt-update-output.jpg)

## 6. TryHackMe — Linux Fundamentals Part 1

I completed the free Linux Fundamentals Part 1 room on TryHackMe. The room introduced Linux commands I can use to navigate folders, view files and search through text.

![Linux Fundamentals Part 1 completed](evidence/08-tryhackme-linux-fundamentals-completed.jpg)

Some of the commands and operators I covered were:

| Command or operator | What it does |
|---|---|
| `whoami` | Shows the current username |
| `echo` | Prints text |
| `ls` | Lists files and folders |
| `cd` | Changes directory |
| `cat` | Displays file contents |
| `pwd` | Shows the current working directory |
| `find` | Searches for files |
| `grep` | Searches for text |
| `&` | Runs a command in the background |
| `&&` | Runs the next command if the previous one succeeds |
| `>` | Writes output to a file, replacing its contents |
| `>>` | Appends output to a file |

## 7. Conclusion

I installed VirtualBox, imported Kali Linux and confirmed that I could log in and use the terminal. I checked the current user, reviewed the VM's network configuration and refreshed the package list. Completing Linux Fundamentals Part 1 also gave me more practice with commands I will continue using as I move into the next labs.
