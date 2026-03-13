---
layout: default
title: Linux System Hardening
description: Learn how to improve the security posture of your Linux systems.
---

🔗 [Link to the Room](https://tryhackme.com/room/linuxsystemhardening)

## 🏷️ Table of Contents  

1. [Learning Objectives](#learning-objectives)<br>
2. [Physical Security](#physical-security)<br>
3. [Filesystem Partitioning and Encryption](#filesystem-partitioning-and-encryption)<br>
4. [Firewall](#firewall)<br>
  4.1 [Key points](#key-points)<br>
5. [Remote Access](#remote-access)<br>
  5.1 [Key protections](#key-protections)<br>
6. [Securing User Accounts](#securing-user-accounts)<br>
  6.1 [Best practices](#best-practices)<br>
7. [Software and Services](#software-and-services)<br>
8. [Update and Upgrade Policies](#update-and-upgrade-policies)<br>
  8.1 [Key points](#key-points)<br>
9. [Audit and Log Configuration](#audit-and-log-configuration)<br>
  9.1 [Key takeaways](#key-takeaways)<br>

---

# 📚 Study Notes 

- Linux is a secure and cost-effective alternative to systems like Windows Server.
- Hosting on Linux can save money on licensing and hardware.
- While not always the best choice, it works well in many cases.
- Before using it, it’s important to secure the system, a process called **Linux hardening**.


# Learning Objectives 

- in this room you will learn how to **secure Linux** by focusing on:
  - Physical security
  - Encrypting filesystems
  - Setting up firewalls
  - Managing remote access
  - Controlling software and services
  - Keeping the system updated
  - Monitoring logs

<img width="595" height="382" alt="image" src="https://github.com/user-attachments/assets/ab5dd837-9e0f-4cd6-aa4b-82f83ad79670" />

&nbsp;

# Physical Security 

- Physical security is the **first layer of defense**.
- If an attacker can access your computer physically, they can steal disks or reset passwords, even on Linux.
- This is why the saying goes: **“boot access = root access.”**

- **To protect your system**:
  - Keep computers in **secure locations**.
  - Use complex system passwords.
  - For personal systems, consider adding a BIOS/UEFI boot password.
  - You can also **add a GRUB password** to **prevent unauthorized boot changes** or **root access** (`grub2-mkpasswd-pbkdf2 `generates the hash).

&nbsp;

>[!WARNING]
>**GRUB passwords aren’t useful for cloud servers**, since you can’t access the physical machine.

&nbsp;

- Physical security is crucial because attackers with physical access can cause serious damage. 
- GRUB passwords and boot passwords add extra layers, but you should also have a plan if disks are stolen.  

&nbsp;

>[!NOTE]
> - **GRUB** [The GRand Unified Bootloader] **is a program that starts your Linux system when you turn on your computer**. It lets you **choose which operating system to boot** and can be used to access advanced boot options.

&nbsp;

---
><details><summary>❓ What command can you use to create a password for the GRUB bootloader?</summary>grub2-mkpasswd-pbkdf2</details>
>✅Solution: It's a tool that creates a secure password hash for GRUB, so only authorized users can change boot settings or access root.
---
><details><summary>❓What does PBKDF2 stand for? </summary>Password-Based Key Derivation Function 2</details>
---

&nbsp;

# Filesystem Partitioning and Encryption 

- Disk **encryption protects data** if a device is stolen or physically accessed.
- Encrypted data is unreadable without the correct password, making the disk useless to attackers.

- Most Linux systems use **LUKS** [Linux Unified Key Setup] for disk encryption.
- LUKS **encrypts a partition** using a **master key**, which is protected by one or more user passwords = this allows multiple users to unlock the same encrypted disk with different passwords.
- LUKS supports strong encryption algorithms and securely derives keys from passwords using **PBKDF2** = The actual data on the disk is encrypted with the master key.

&nbsp;

>[!NOTE]
> - **LUKS** [Linux Unified Key Setup] is a tool that **encrypts Linux disks** so data can’t be read without a password.
>
> - **PBKDF2** is a **method** that **turns a password into a strong encryption key**.
>
> - **Cryptsetup** is a **command-line tool** to set up and manage encrypted Linux partitions.
  
&nbsp;

- Linux distributions usually provide a graphical way to encrypt disks, but encryption can also be set up from the command line using **cryptsetup**. Once encrypted, the partition must be unlocked, formatted, and mounted before use.

- **Encryption is an essential layer of security**, especially to protect data if disks or devices are stolen.

&nbsp;

---
><details><summary>❓What does LUKS stand for?</summary>Linux Unified Key Setup</details>
---
><details><summary>❓We cannot attach external storage to the VM, so we have created a /home/tryhackme/secretvault.img file instead. It is encrypted with the password 2N9EdZYNkszEE3Ad. To access it, you need to open it using cryptsetup and then mount it to an empty directory, such as myvault. What is the flag in the secret vault?</summary>THM{****_***_***}</details>
>✅Solution: Check the Question Hint provided by THM: `sudo cryptsetup open --type luks secretvault.img myvault && sudo mount /dev/mapper/myvault myvault/`. Then you can read your flag with command `cat task3_flag.txt`
---

&nbsp;

# Firewall 

- A firewall **controls** which **network traffic** can enter or leave a system, **protecting** it from attacks.
- On Linux, firewalls are usually host-based, meaning they protect a single system, and they work by controlling traffic based on ports rather than processes.

## Key points 
- **Purpose**: Block unauthorized access, prevent clients from acting as servers, and control network traffic.
- **Linux firewalls**: Modern Linux firewalls are stateful, tracking ongoing connections for better security.
- **Netfilter**: The core packet-filtering system in the Linux kernel. Front-ends like iptables and nftables let you manage rules (**iptables** uses chains: INPUT, OUTPUT, FORWARD to allow or block traffic amd **nftables** is newer, more efficient, and also controls traffic through tables and chains).
- **Simpler tools**: Front-ends like **UFW** [Uncomplicated Firewall] make firewall setup easier. e.g. `ufw allow 22/tcp` allows SSH traffic.
- **Firewall policy**: Decide whether to block all and allow exceptions (safer) or allow all and block exceptions (more convenient).

&nbsp;

>[!NOTE]
> - **iptables** – A Linux tool to set firewall rules for controlling network traffic using netfilter.
>
> - **nftables** – A modern Linux firewall tool that manages traffic more efficiently than iptables.
>   
> - **UFW** – “Uncomplicated Firewall,” a simple tool to allow or block network traffic on Linux.

&nbsp;

>[!CAUTION]
>**Firewalls are essential for Linux security, but rules must be planned carefully, especially as exceptions grow.**

&nbsp;

---
><details><summary>❓There is a firewall running on the Linux VM. It is allowing port 22 TCP as we can ssh into the machine. It is allowing another TCP port; what is it?</summary>12526</details>
>✅Solution: Run command `sudo ufw status`
---
><details><summary>❓What is the allowed UDP port?</summary>14298</details>
>✅Solution: It's in the previous output.
---

&nbsp;

# Remote Access 

- Remote access lets you control a system from anywhere, but it also **exposes the system to attacks** like ***password sniffing***, guessing, or ***exploiting services***.

## Key protections 
- **Use encrypted protocols** (e.g., SSH) instead of cleartext ones like Telnet.
- **Prevent password guessing** by **disabling root login** (`PermitRootLogin no`) and by using strong passwords or, better, public key authentication.
- **Set up SSH keys** by generating keys with `ssh-keygen -t rsa` and copy the public key to the server with `ssh-copy-id username@server`.
- Configure SSH server (`/etc/ssh/sshd_config`) to **enable key authentication** (`PubkeyAuthentication yes`) and **disable password login** (`PasswordAuthentication no`).

&nbsp;

> [!IMPORTANT] 
> **Using SSH keys instead of passwords makes remote access secure and reduces the risk of attackers guessing credentials.**

&nbsp;

>[!NOTE]
> - **SSH** – A secure protocol for remote login and file transfer on Linux.
>
> - **ssh-keygen** – A tool that creates a public/private key pair for secure SSH access.
>
> - **ssh-copy-id** – A command that copies your SSH public key to a server for passwordless login.

&nbsp;

---
><details><summary>❓What flag is hidden in the sshd_config file?  </summary>THM{******_***_*****}</details>
>✅Solution: Use command: `cat /etc/ssh/sshd_config`
---

&nbsp;

# Securing User Accounts 

- The root account has full control over the system and should not be used for everyday work. Mistakes made as root can easily break the system.

<img width="444" height="241" alt="image" src="https://github.com/user-attachments/assets/a011830c-255f-4ffd-b7d9-5c3d460bca83" />

&nbsp;

## Best practices 

  - **Use sudo**: Create a normal user for admin tasks and add it to the `sudo` (Debian/Ubuntu) or `wheel` (RedHat/Fedora) group + Use sudo only when root privileges are needed.
  - **Disable root login**: After setting up a sudo user, disable direct root access by setting its shell to `/sbin/nologin`.
  - **Enforce strong passwords**: Use `libpwquality` to require longer, more complex passwords and prevent password reuse.
  - **Disable unused accounts**: Disable accounts that no longer need access by setting their shell to `/sbin/nologin`. Do the same for service accounts (e.g., `www-data`, `nginx`) to prevent interactive logins if compromised.

&nbsp;

>[!IMPORTANT]
> **Limiting root usage, enforcing strong passwords, and disabling unused accounts greatly reduces the risk of system compromise.**

&nbsp;

>[!NOTE]
> - **sudo** – Lets a regular user **run commands with root** (administrative) privileges.
>
> - **sudoers** – A group of users **allowed to use sudo** on a Linux system.
>
> - **/sbin/nologin** – A shell that **prevents** a user or service account **from logging** in interactively. 

&nbsp;

---
><details><summary>❓One way to disable an account is to edit the `passwd` file and change the account’s shell. What is the suggested value to use for the shell?</summary>/sbin/nologin</details>
>✅Solution: On Linux, each user account has a login shell defined in the /etc/passwd file. The shell determines what program runs when the user logs in. To disable an account without deleting it, you can change its shell to a special value that prevents interactive logins. This means the user or service cannot open a shell session, effectively disabling their ability to log in while keeping the account intact for system purposes instead of removing the account.
---
><details><summary>❓What is the name of the RedHat and Fedora systems sudoers group?</summary>wheel</details>
---
><details><summary>❓What is the name of the sudoers group on Debian and Ubuntu systems?</summary>sudo</details>
---
><details><summary>❓Other than tryhackme and ubuntu, what is the username that belongs to the sudoers group?</summary>blacksmith</details>
>✅Solution: Run commmand: `cat /etc/group | grep sudo`
---

&nbsp;


# Software and Services 

- Installing software and running services increases potential security risks.
  
- To reduce your attack surface:
  - **Disable unnecessary services**: Remove or disable software you don’t need, like web servers, to minimize vulnerabilities.
  - **Block unneeded ports**: Adjust your firewall to block network ports for services you don’t use (e.g., block ports 80/443 if no web server).
  - **Avoid legacy protocol**: Use secure alternatives (e.g., SSH instead of Telnet, SFTP instead of TFTP) because old network protocols are insecure and should be avoided.
  - **Remove identification strings**: Don’t reveal software versions or system info in network responses to prevent attackers from gathering intelligence.

&nbsp;

---
><details><summary>❓Besides FTPS, what is another secure replacement for TFTP and FTP? </summary>SFTP</details>
>✅Solution: Secure File Transfer Protocol, a safe way to transfer files over SSH.
---

&nbsp;

# Update and Upgrade Policies 

- Keeping your Linux system up-to-date is essential to protect against security vulnerabilities.

## Key points 
  - **Updating packages**: Debian/Ubuntu: apt update + apt upgrade, RedHat/Fedora: dnf update (newer) or yum update (older)
  - **Use supported distributions**: Ubuntu LTS: Long Term Support releases (e.g., 20.04, 22.04) get 5 years of free updates, plus optional Extended Security Maintenance (ESM). RedHat Enterprise Linux: Up to 12 years of support in phases (full support, maintenance, extended life).
  - **Kernel updates**: Keep the kernel updated to fix critical vulnerabilities like “Dirty COW,” which can allow root access.
  - **Automatic updates**: Configure updates to install automatically when possible, and monitor security news to respond quickly to vulnerabilities.

&nbsp;

>[!IMPORTANT]
> **Keeping both software and the kernel up-to-date significantly reduces the risk of attacks.**

&nbsp;

<img src="https://github.com/user-attachments/assets/c667cddd-4989-4cd4-b423-f2028309190c" width="300">

&nbsp;

---
><details><summary>❓What command would you use to update an older Red Hat system?</summary>yum update</details>
---
><details><summary>❓What command would you use to update a modern Fedora system?</summary>dnf update</details>
---
><details><summary>❓What two commands are required to update a Debian system? (Connect the two commands with &&.)</summary>apt update && apt upgrade</details>
---
><details><summary>❓What does yum stand for?</summary>Yellowdog Updater, Modified</details>
---
><details><summary>❓What does dnf stand for?</summary>Dandified YUM</details>
---
><details><summary>❓What flag is hidden in the sources.list file?</summary>THM{***_********_**********_******}</details>
>✅Solution: Run `find / -type f -name sources.list 2>/dev/null` to find the file, then read it with `cat /etc/apt/sources.list` to read the flag.
---

&nbsp;

# Audit and Log Configuration 

- Most log files on Linux systems are stored in the `/var/log` directory.
- Here are a few of the logs that can be referenced when looking into threats:

|path|explanation|
|--------------------|------------------------------------------------------------------------------------------------------|
|`/var/log/messages`|a general log for Linux systems|
|`/var/log/auth.log` | a log file that lists all authentication attempts (Debian-based systems)|
| `/var/log/secure` | a log file that lists all authentication attempts (Red Hat and Fedora-based systems)|
|`/var/log/utmp`|an access log that contains information regarding users that are currently logged into the system|
| `/var/log/wtmp`| an access log that contains information for all users that have logged in and out of the system|
|`/var/log/kern.log` | a log file containing messages from the kernel|
| `/var/log/boot.log` | a log file that contains start-up messages and boot information|

- **to view last few lines** use `tail`, e.g. `tail -n 12 boot.log` will display the last 12 lines.
- or you can also use `grep`, e.g. `grep FAILED boot.log`, which will show ou only the lines with the word `FAILED`.

&nbsp;

---
><details><summary>❓What command can you use to display the last 15 lines of kern.log?</summary>tail -n 15 kern.log</details>
---
><details><summary>❓What command can you use to display the lines containing the word denied in the file secure?</summary>grep denied secure</details>
---

&nbsp;

- Hardening a Linux system is about following good practices to reduce risks.

## Key takeaways 
  - **Document host information** – Keep track of system details.
  - **Test changes first** – Apply and verify updates or configurations on a test system before production.
  - **Document all changes** – Record everything you do to maintain clarity and accountability.

&nbsp;

>[!IMPORTANT]
>**Following these basic guidelines helps maintain a secure and well-managed Linux system, and the level of documentation may grow with the size of your environment.** 
