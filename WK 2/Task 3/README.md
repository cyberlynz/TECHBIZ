# Week 2, Task 3 — Vulnerable Lab Setup: Metasploitable 2

<p align="center">
  <img src="https://img.shields.io/badge/Task-03-404040?style=flat-square&labelColor=6366F1" alt="Task 3" />
  <img src="https://img.shields.io/badge/Lab-Metasploitable%202-E87500?style=flat-square&labelColor=000000" alt="Metasploitable 2" />
  <img src="https://img.shields.io/badge/Virtualization-VirtualBox%207.2-0070C0?style=flat-square&labelColor=000000&logo=virtualbox&logoColor=white" alt="VirtualBox" />
</p>

**Internship:** Ethical Hacking Internship, TechBiz Academy  
**Author:** Collins Okike

## 1. Objective

The goal of this task was to set up a target machine that I can use in later tasks and prove that my Kali Linux VM can reach it. I chose **Metasploitable 2**, an Ubuntu Linux VM that is intentionally vulnerable and designed for security practice.

I used a private VirtualBox NAT Network so the two VMs could communicate inside the lab without putting the vulnerable target directly on my normal network.

## 2. Lab overview

| Item | Details |
|---|---|
| Host computer | Windows 10 (amd64) |
| Virtualization software | Oracle VirtualBox 7.2.16 |
| Attacker VM | kali-linux (Kali Linux, 64-bit) |
| Target VM | Metasploitable2 (Ubuntu Linux, 32-bit) |
| Network type | NAT Network named \`NatNetwork\`, on Adapter 1 of both VMs |
| Network adapter | Intel PRO/1000 MT Desktop on both VMs |
| Network prefix | \`10.0.0.0/24\` |
| Kali IP address | \`10.0.0.3\` on \`eth0\` |
| Target IP address | \`10.0.0.6\` on \`eth0\`, mask \`255.255.255.0\` |
| Target login used in the lab | \`msfadmin / msfadmin\` (default) |

## 3. Key terms

- **Target machine:** A computer intentionally set up for controlled security testing.
- **Metasploitable 2:** An intentionally vulnerable Linux VM containing older services and web applications for practice.
- **NAT Network:** A VirtualBox network type that places attached VMs on a shared private network so they can communicate.
- **DHCP:** The service that automatically assigns IP addresses to devices on the network.
- **Ping (ICMP):** A basic test that sends packets to an IP address and waits for replies.

## 4. Why I used a NAT Network

I used a NAT Network because both VMs needed to communicate with each other. Plain NAT would give each VM its own private connection and would not provide the shared network required for this lab.

I also chose this setup for safety. Metasploitable 2 is intentionally insecure, so I did not use a Bridged adapter that could expose it to my real network. Both VMs used the same network name, \`NatNetwork\`.

## 5. Setting up the lab

### 5.1 Import Metasploitable 2

I downloaded and extracted the Metasploitable 2 archive. In VirtualBox I created a VM named \`Metasploitable2\`, selected Linux with Ubuntu (32-bit), assigned 512 MB of RAM, and selected the existing \`.vmdk\` virtual disk.

![Screenshot: Metasploitable2 imported in VirtualBox](screenshots/01-metasploitable2-virtualbox.jpg)

### 5.2 Put Kali on the same network

My Kali VM was already installed from Week 1. I opened its settings and set Adapter 1 to the same NAT Network, \`NatNetwork\`. The adapter name had to match on both VMs so they would be on the same network.

![Screenshot: kali-linux in VirtualBox on the same NAT Network](screenshots/02-kali-virtualbox.jpg)

### 5.3 Create the NAT Network

In VirtualBox I opened Tools, then Network, then the NAT Networks tab, and created a network named \`NatNetwork\` with DHCP enabled. My NatNetwork used the IPv4 prefix \`10.0.0.0/24\`, which is why both VMs received addresses in the \`10.0.0.x\` range.

![Screenshot: VirtualBox NAT Networks showing NatNetwork, IPv4 prefix and DHCP](screenshots/03-natnetwork-configuration.jpg)

### 5.4 Start the target and find its IP

I started Metasploitable 2 and logged in with the default account (\`msfadmin / msfadmin\`). I checked its network address with:

\`\`\`bash
ifconfig
\`\`\`

The target address was \`10.0.0.6\` on interface \`eth0\`, with the mask \`255.255.255.0\`.

![Screenshot: ifconfig on Metasploitable2 showing 10.0.0.6](screenshots/04-metasploitable2-ifconfig.jpg)

### 5.5 Start Kali and check its IP

Next I started Kali and checked that it also received an address from the NAT Network.

\`\`\`bash
ip a
\`\`\`

The Kali address was \`10.0.0.3/24\` on \`eth0\`. Both addresses were in the same \`10.0.0.0/24\` subnet.

![Screenshot: ip a on Kali showing eth0 with 10.0.0.3/24](screenshots/05-kali-ip-configuration.jpg)

## 6. Testing connectivity from Kali

From Kali I pinged the target IP with four packets.

\`\`\`bash
ping -c 4 10.0.0.6
\`\`\`

**My result:** Four packets were transmitted and four were received, with **0% packet loss**. The average round-trip time was **0.459 ms**. This confirmed that Kali (\`10.0.0.3\`) could reach Metasploitable 2 (\`10.0.0.6\`) on the lab network.

![Screenshot: successful ping from Kali to Metasploitable2 with 0% packet loss](screenshots/06-kali-to-metasploitable-ping.jpg)

### Open the target web page

As an extra check I opened the target in the Kali browser:

\`\`\`text
http://10.0.0.6/
\`\`\`

The page loaded and listed the web applications available on Metasploitable 2, including TWiki, phpMyAdmin, Mutillidae, DVWA and WebDAV. This showed that more than ping connectivity was working.

![Screenshot: Metasploitable 2 web page opened from the Kali browser](screenshots/07-metasploitable-webpage.jpg)

### TryHackMe DVWA room

The report also includes the screenshot of the TryHackMe DVWA room.

![Screenshot: TryHackMe DVWA room](screenshots/08-tryhackme-dvwa-completed.jpg)

## 7. Problems faced

I did not encounter any errors during this task. Both VMs received IP addresses from \`NatNetwork\`, and the ping to \`10.0.0.6\` had 0% packet loss.

## 8. Conclusion

I imported Metasploitable 2 into VirtualBox and connected it to the same NAT Network as my Kali VM. The successful ping confirmed that the two VMs could communicate. I also recorded the target address, \`10.0.0.6\`, for the scanning tasks that follow.

This setup gives me a dedicated lab environment where I can practise network scanning and security testing against an intentionally vulnerable target.
