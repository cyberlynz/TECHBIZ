# Week 2, Task 1 — Linux Fundamentals: Commands, Files & Permissions

<p align="center">
  <img src="https://img.shields.io/badge/Task-01-404040?style=flat-square&labelColor=6366F1" alt="Task 1" />
  <img src="https://img.shields.io/badge/Skill-Linux%20Fundamentals-404040?style=flat-square&labelColor=C00000" alt="Linux fundamentals" />
  <img src="https://img.shields.io/badge/Environment-Kali%20Linux-E87500?style=flat-square&labelColor=000000&logo=kalilinux&logoColor=white" alt="Kali Linux" />
</p>

**Internship:** Ethical Hacking, TechBiz Academy  
**Author:** Collins Okike

## 1. Introduction

For this task I practised using Linux in Kali. Before this, I had mostly used graphical menus, so the terminal felt unfamiliar. My goal was to get comfortable moving around the file system, running common commands, creating and editing files, changing permissions, and installing software with `apt`.

I also watched two supporting videos: **Linux basics for hackers beginners tutorial** and **Linux file permissions chmod explained**.

## 2. Linux file-system locations

Linux organizes everything under one root directory, `/`. The main locations I looked at were:

| Path | Purpose | Why it matters |
|---|---|---|
| `/` | Root of the whole file system | Everything starts here. |
| `/home` | Personal home directories | My practice files are under `/home/kali`. |
| `/etc` | System-wide configuration files | For example, `/etc/passwd` lists user accounts. |
| `/var` | Changing system data, including logs and caches | Logs in `/var/log` can provide clues during an investigation. |

I found it helpful to think of `/etc` as the place where system settings are kept, and `/var` as the place where the system records what it has been doing.

## 3. The twenty basic commands

I practised these commands in the Kali terminal.

| # | Command | Purpose | Example I ran |
|---:|---|---|---|
| 1 | `pwd` | Prints the current working directory. | `pwd` |
| 2 | `ls` | Lists files and folders. | `ls` |
| 3 | `ls -la` | Lists all entries, including hidden files, with permissions and sizes. | `ls -la` |
| 4 | `cd` | Changes to another directory. | `cd /home/kali` |
| 5 | `cd ..` | Moves up one directory level. | `cd ..` |
| 6 | `whoami` | Shows the logged-in username. | `whoami` |
| 7 | `clear` | Clears the terminal screen. | `clear` |
| 8 | `man` | Opens a command's manual page. | `man ls` |
| 9 | `cat` | Prints file contents. | `cat notes.txt` |
| 10 | `less` | Displays a long file one screen at a time. | `less notes.txt` |
| 11 | `touch` | Creates an empty file or updates its timestamp. | `touch notes.txt` |
| 12 | `mkdir` | Creates a directory. | `mkdir practice` |
| 13 | `cp` | Copies a file or folder. | `cp notes.txt notes-copy.txt` |
| 14 | `mv` | Moves or renames a file. | `mv notes-copy.txt copy.txt` |
| 15 | `rm` | Deletes a file. | `rm copy.txt` |
| 16 | `rmdir` | Deletes an empty directory. | `rmdir empty-folder` |
| 17 | `echo` | Prints text or writes it to a file with redirection. | `echo 'Linux practice'` |
| 18 | `grep` | Searches text for a word or pattern. | `grep 'Linux' notes.txt` |
| 19 | `chmod` | Changes file or directory permissions. | `chmod 755 script.sh` |
| 20 | `sudo` | Runs a command with administrator (root) privileges. | `sudo apt update` |

I also practised three additional commands that are useful when looking at files:

- `head -n 5 /etc/passwd` displays the first five lines of the user list.
- `id` displays my user ID and the groups I belong to.
- `find /home -name "*.txt"` searches for text files under `/home`.

### Navigating the file system

I ran `pwd`, `ls`, `cd`, `cd ..` and `whoami`. The working directory printed by `pwd` was `/home/kali`. I entered Downloads, listed its contents, and then returned to `/home/kali`. The `whoami` command printed `kali`.

![Screenshot 1: pwd, ls, cd and whoami in the Kali terminal](evidence/01-file-system-navigation.webp)

The additional checks showed that the first five entries in `/etc/passwd` were root, daemon, bin, sys and sync. The `id` output showed that my account was `kali` (UID 1000) and belonged to the `sudo` group. The `find` command returned text files under my home directory, including files in my Firefox profile, local PKI folder and John the Ripper folder.

![Screenshot 2: head, id and find output in the Kali terminal](evidence/02-extra-linux-commands.webp)

## 4. Practical exercise — create, write and read a file

I kept this exercise inside my home directory so I would not change anything important in the system.

```bash
mkdir -p ~/TechBiz
cd ~/TechBiz
echo 'Practising Linux commands in Kali.' > notes.txt
cat notes.txt
```

The commands created the `TechBiz` folder, moved me into it, wrote a line into `notes.txt`, and printed the saved content back to the terminal. The output was:

```text
Practising Linux commands in Kali.
```

This confirmed that the file was created and saved correctly.

![Screenshot 3: creating a folder, writing to notes.txt and reading it with cat](evidence/03-create-write-read-file.webp)

## 5. Changing file permissions

I checked permissions with `ls -l`, created `script.sh`, and made it executable.

```bash
touch script.sh
ls -l script.sh
chmod 755 script.sh
ls -l script.sh
```

Each permission digit is calculated using read = 4, write = 2 and execute = 1.

| Who | Digit | Permissions | Meaning |
|---|---:|---|---|
| Owner | 7 | `rwx` (4+2+1) | Can read, write and run the file. |
| Group | 5 | `r-x` (4+1) | Can read and run it, but not change it. |
| Others | 5 | `r-x` (4+1) | Can read and run it, but not change it. |

I compared this with `644`, which is a common setting for ordinary text files. The owner can read and write, while group and others can only read. It does not give execute permission.

**My result:** Before `chmod`, `script.sh` showed `-rw-rw-r--`. After `chmod 755 script.sh`, it showed `-rwxr-xr-x`. The permissions on `notes.txt` did not change. I learned that giving execute permission to everyone can be risky, so using the smallest permission that works is the safer choice.

![Screenshot 4: touch, ls -l, chmod 755 and ls -l in the Kali terminal](evidence/04-file-permissions-chmod.webp)

## 6. Ownership and sudo

I checked the owner and group of `notes.txt`, then ran:

```bash
ls -l notes.txt
sudo chown $USER:$USER notes.txt
```

The `chown` command changes file ownership. I used `sudo` because changing ownership normally requires administrator rights. The owner and group were already `kali`, so the command finished without an error but did not produce a visible ownership change. I did not run `ls -l` again after the command, so the screenshot does not show a separate after-check.

I learned to use `sudo` carefully because root privileges can make system-wide changes, and a mistake in a privileged command can cause damage.

![Screenshot 5: ls -l and sudo chown in the Kali terminal](evidence/05-file-ownership-sudo.webp)

## 7. Package management with apt

I ran the following commands:

```bash
sudo apt update
sudo apt install nmap -y
nmap --version
```

- `sudo apt update` refreshed the package lists. It did not install anything by itself.
- `sudo apt install nmap -y` installed Nmap or upgraded it if it was already present.
- `nmap --version` checked that Nmap could run from the terminal.

**My result:** The package-list update reported that 630 packages could be upgraded. Nmap was already on my system, so apt upgraded it instead of installing it fresh. The summary showed four packages upgraded — `nmap`, `ndiff`, `nmap-common` and `zenmap` — and zero newly installed. The command finished without errors. `nmap --version` printed **Nmap 7.991**.

![Screenshot 6: apt update, apt install nmap -y and nmap --version in the Kali terminal](evidence/06-apt-nmap-installation.webp)

## 8. What I learned and conclusion

This task gave me practical experience using the Linux terminal. I practised the file system, twenty basic commands, file creation, permissions with `chmod`, ownership with `chown`, `sudo`, and package installation with `apt`.

The most useful lesson for me was understanding permissions. Reading a string such as `-rw-r--r--` and seeing how `755` gives more access than `644` helped me understand least privilege. I also learned to check manual pages before using commands such as `rm`, because mistakes in the terminal cannot always be undone.

I now see the terminal as a normal part of cybersecurity work, not something to avoid. Nmap is one of the tools I will use in later tasks, and this exercise helped me confirm that it runs in my Kali environment.
