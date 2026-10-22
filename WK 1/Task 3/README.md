# WK 1 — Task 3: Lab Setup — VirtualBox + Kali Linux

[← Back to Week 1](../README.md) · [Task 1](../Task%201/README.md) · [Task 2](../Task%202/README.md)

This task documents the virtual lab setup and commands recorded in the submitted Week 1 report. Network values are specific to the original lab session and may change.

---

# 🐉 Task 3 — VirtualBox and Kali Linux Lab Setup

## Objective

Prepare a basic cybersecurity practice environment by running Kali Linux in VirtualBox, exploring the desktop, and verifying the system using introductory terminal commands.

## 1. Tools and Environment

| Component | Role in the lab |
|---|---|
| **Windows 10** | Host operating system used for the setup |
| **Oracle VirtualBox 7.2.16** | Runs the Kali Linux virtual machine |
| **Kali Linux VirtualBox image** | Pre-built Linux environment for cybersecurity learning |
| **7-Zip** | Extracts the downloaded virtual-machine archive |
| **Kali Terminal** | Runs Linux commands and checks system/network information |
| **TryHackMe — Linux Fundamentals Part 1** | Introductory practice with Linux commands and file navigation |

A virtual machine lets me run a separate operating system on the host without replacing Windows as the computer's main operating system. It provides a practical environment for learning Linux and security tools.

> A virtual machine is not a guarantee of isolation from every risk. Network mode, shared folders, clipboard integration, and other settings affect how it interacts with the host and other systems.

## 2. Setup Procedure

### Step 1 — Install VirtualBox

I downloaded and installed VirtualBox on my Windows 10 computer using the installer defaults. After installation, I opened VirtualBox Manager to confirm that the application was available.

**Verification:** VirtualBox Manager opened successfully.

### Step 2 — Download the pre-built Kali Linux VM

I used the pre-built VirtualBox image from the official Kali Linux website. This method avoids manually installing the operating system from an ISO image.

I extracted the downloaded archive using 7-Zip.

### Step 3 — Import Kali Linux

In VirtualBox, I selected **Machine → Add** and chose the extracted `.vbox` file. The Kali virtual machine then appeared in VirtualBox Manager.

### Step 4 — Start Kali and log in

I started the virtual machine and reached the Kali Linux desktop. My report records that I used the default credentials supplied for that image:

- **Username:** `kali`
- **Password:** `kali`

These are the credentials recorded for my lab session; default credentials may vary by image and should be changed when appropriate.

### Step 5 — Open the terminal

After logging in, I opened the Kali terminal and ran the commands required for the exercise:

```bash
whoami
ip a
sudo apt update
```

These checks confirmed the logged-in username, displayed the network interfaces, and refreshed the package metadata.

## 3. Commands Run and What They Do

| Command | Purpose | Recorded result / interpretation |
|---|---|---|
| `whoami` | Displays the current username. | Output was `kali`, showing that I was using the normal `kali` account rather than root. |
| `ip a` | Lists network interfaces and address information. | Displayed the loopback interface and the virtual Ethernet interface, including address and interface details. |
| `sudo apt update` | Refreshes package information from configured repositories. | My report records that the command completed without errors. It refreshes package lists; it does not by itself upgrade all installed packages. |

### Why these commands matter

- **User context:** `whoami` helps me confirm which account is executing a command.
- **Network visibility:** `ip a` helps identify interfaces, assigned IP addresses, MAC addresses, and interface state.
- **Package maintenance:** `sudo apt update` refreshes the available package index so package-management tools can identify current versions from configured repositories.

## 4. Recorded Network Results

The following values are from the terminal output documented in my Week 1 report.

| Field | Recorded value | What it tells me |
|---|---|---|
| Network interface | `eth0` | The virtual machine's Ethernet interface |
| IPv4 address | `10.0.0.3/24` | The address recorded for the Kali interface |
| MAC address | `08:00:27:63:b0:05` | The virtual network adapter's link-layer address |
| IPv6 address | `fe80::a413:d56d:1797:240/64` | A link-local IPv6 address |
| Loopback IPv4 | `127.0.0.1` | The local host loopback address |

The `/24` prefix indicates a 24-bit IPv4 network prefix, equivalent to the subnet mask `255.255.255.0`. The `fe80::/10` range is used for IPv6 link-local addresses, which are not globally routable.

The exact values depend on the virtual network and may change if the VM is moved to another network or its addressing configuration changes.

### Host Windows network check

My networking report separately recorded these Windows host values:

| Field | Recorded value |
|---|---|
| Private IPv4 address | `10.0.0.4` |
| Subnet mask | `255.255.255.0` |
| Default gateway | `10.0.0.1` |
| DHCP server | `10.0.0.2` |
| Physical Address | `08-00-27-F4-0F-34` |

These are the values documented in that report. The Windows host and Kali VM results are shown separately because they belong to different interfaces/environments.

## 5. Linux Fundamentals Practice

I completed the free **Linux Fundamentals Part 1** room on TryHackMe as part of this task. The room introduced basic terminal navigation, file handling, and command chaining.

| Command / operator | What it does |
|---|---|
| `whoami` | Displays the current user. |
| `echo` | Prints text or variable values. |
| `ls` | Lists directory contents. |
| `cd` | Changes the current directory. |
| `pwd` | Displays the current working directory. |
| `cat` | Prints file contents. |
| `find` | Searches for files and directories. |
| `grep` | Searches text for matching patterns. |
| `&` | Runs a command in the background in a shell. |
| `&&` | Runs the next command only if the previous command succeeds. |
| `>` | Redirects output to a file, replacing its existing contents. |
| `>>` | Appends output to a file. |

These commands are useful for moving around a Linux system, inspecting files, and searching logs. They are basic skills that I will need when using security tools and reviewing command output.

## 6. Lab Setup Outcome

By the end of the task, I had:

- Installed VirtualBox on the Windows host.
- Imported and started the pre-built Kali Linux virtual machine.
- Logged in and opened the Kali terminal.
- Checked the current user with `whoami`.
- Inspected the network interfaces with `ip a`.
- Refreshed package metadata using `sudo apt update`.
- Completed the Linux Fundamentals Part 1 learning activity.

The screenshots and terminal outputs are recorded in my submitted Week 1 report. They document the setup process and the results observed during the exercise.

---
