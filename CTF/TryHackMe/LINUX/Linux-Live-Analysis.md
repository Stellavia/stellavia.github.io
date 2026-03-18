---
layout: default
title: Linux Live Analysis
description: Learn how to perform live forensics on a Linux host.
---

🔗 [Link to the Room](https://tryhackme.com/room/linuxliveanalysis)

## 🏷️ Table of Contents

1. [Introduction and Lab connection](#introduction-and-lab-connection)<br>
2. [Live Forensics: An Overview](#live-forensics-an-overview)<br>
3. [Tool of the Trade: Osquery](#tool-of-the-trade-osquery)<br>
  3.1 [Osquery Table Schemas](#osquery-table-schemas)<br>
  3.2 [Users Account](#users-account)<br>
  3.3 [Process Information](#process-information)<br>
4. [System Profiling](#system-profiling)<br>
  4.1 [Basic System Information](#basic-system-information)<br>
  4.2 [Hostname](#hostname)<br>
  4.3 [uptime](#uptime)<br>
  4.4 [Hardware Information](#hardware-information)<br>
  4.5 [Disk Free](#disk-free)<br>
  4.6 [List of Block Devices](#list-of-block-devices)<br>
  4.7 [Free Storage](#free-storage)<br>
  4.8 [Debian Package Manager Packages](#debian-package-manager-packages)<br>
  4.9 [Network Profiling](#network-profiling)<br>
5. [Hunting for Processes](#hunting-for-processes)<br>
  5.1 [Hunting for Suspicious Process](#hunting-for-suspicious-process)<br>
  5.2 [Processes Running From the tmp Directory](#processes-running-from-the-tmp-directory)<br>
  5.3 [Hunting for Fileless Malware (Process)](#hunting-for-fileless-malware-process)<br>
  5.4 [Orphan Processes](#orphan-processes)<br>
  5.5 [Finding Processes Launched from User Directories](#finding-processes-launched-from-user-directories)<br>
6. [Investigating Network Connections](#investigating-network-connections)<br>
  6.1 [Listing Network Communication](#listing-network-communication)<br>
  6.2 [Network Connections](#network-connections)<br>
  6.3 [Remote Connection](#remote-connection)<br>
  6.4 [Examining DNS Queries](#examining-dns-queries)<br>
  6.5 [Listing Down Network Interfaces](#listing-down-network-interfaces)<br>
  6.6 [List Network Connections](#list-network-connections)<br>
7. [TTP Footprints on Disk](#ttp-footprints-on-disk)<br>
  7.1 [Open Files](#open-files)<br>
  7.2 [Files Being Accessed From the tmp Directory](#files-being-accessed-from-the-tmp-directory)<br>
  7.3 [Hidden Files](#hidden-files)<br>
  7.4 [Recently Modified Files](#recently-modified-files)<br>
  7.5 [Recently Modified Binaries](#recently-modified-binaries)<br>
  7.6 [Finding Suspicious Packages](#finding-suspicious-packages)<br>
8. [Persistence: Establishing Foothold](#persistence-establishing-foothold)<br>
  8.1 [Investigating the Initialization Services](#investigating-the-initialization-services)<br>
  8.2 [Hunting for a Backdoor Account](#hunting-for-a-backdoor-account)<br>
  8.3 [Examining Cron Jobs](#examining-cron-jobs)<br>

---

# 📚 Study Notes 

&nbsp;

# Introduction and Lab connection

&nbsp;

Linux powers many servers, cloud services, and supercomputers due to its reliability and performance, which also makes it a major target for cyberattacks.

As a SOC analyst, your task is to perform live forensics on a compromised Linux server to identify attack footprints and assess the extent of the damage.

<!-- NO QUESTIONS HERE -->

&nbsp;

# Live Forensics: An Overview

&nbsp;

From a forensic perspective, it is crucial to collect volatile data from memory and disk, with primary focus on capturing data from the running system.

Key data investigators collect from a **running Linux system**:

| Data Type | Why It Matters |
|-----------|---------------|
| Running Processes | Shows active programs and possible malicious processes. |
| Open Files | Reveals which files processes are accessing or modifying. |
| Memory Structures | May contain passwords, keys, or exploit traces. |
| Network Connections | Identifies suspicious external connections or data exfiltration. |
| Listening Services | Helps detect unauthorized services or backdoors. |
| Logged-in Users | Shows who is currently accessing the system. |
| User Activity | Command history can reveal attacker actions. |
| In-Memory Logs | Provides real-time system events before they are written to disk. |
| Network Interfaces | Shows IP, MAC, and routing details that may indicate tampering. |
| Temporary Files | `/tmp` and `/var/tmp` may contain scripts or attack artifacts. |

<!-- NO QUESTIONS HERE -->

&nbsp;

# Tool of the Trade: Osquery

&nbsp;

osquery is an endpoint monitoring tool that lets you query system information using SQL-like commands to gather detailed data for investigations.

To open the interactive session run `osquery` in the terminal as **root**. 
To display the help options run `.help` command. 

![image](https://github.com/user-attachments/assets/e8b36285-ad54-4cc0-8d09-529ec00a2b82)

&nbsp;

## Osquery Table Schemas

Check [osquery website](https://osquery.io/schema/5.12.1/) to view table schemas:

![image](https://github.com/user-attachments/assets/58129f6e-9d96-4120-8d6c-448aa7f338f2)

&nbsp;

## Users Account

To retrieve info about the user accounts created on the host use Search Query: `Select username, uid, description from users;`

![image](https://github.com/user-attachments/assets/ed7dba54-92d4-48ca-98fe-b8f19e25486c)


- **uid**: User ID.
- **username**: Username.
- **tty**: Terminal type.
- **pid**: Process ID of the user's login shell.
- **type**: Type of login (user, login, remote).
- **host**: Hostname or IP address of the remote host.
- **time**: Login time.

&nbsp;

## Process Information

To get the info about the running processes use Query: `Select pid, name, parent,path from processes;`

![image](https://github.com/user-attachments/assets/c2628fd4-7933-4382-8e38-1f092ab052fe)

&nbsp;

---
><details><summary>❓What hostname is returned after running the following query? Query: select * from etc_hosts where address = `0.0.0.0`;</summary>attacker.thm</details>
---
><details><summary>❓On the official website, how many tables are listed for Linux OS?</summary>154</details>
---

&nbsp;

# System Profiling

&nbsp;

The first step in an investigation is collecting detailed system information to understand the infected machine’s state and environment.

System profiling is an important forensic step used to gather key information about a system, such as configuration details, logged-in users, installed applications, and hardware setup.

Make sure to switch to **root** by using `sudo su` command.

&nbsp;

## Basic System Information

To extract basic information about the system use `uname -a` command.
 This command output provides comprehensive details about the system, including its kernel version, architecture, hostname, and the date and time when the kernel was compiled.

![image](https://github.com/user-attachments/assets/b63f4571-29c1-42f3-a6b9-786dc670c608)


This command output provides comprehensive details about the system, including its kernel version, architecture, hostname, and the date and time when the kernel was compiled. Let's break down the information we retrieved from this command:
- **Linux**: The operating system name.
- **cybertees**: This value shows the system hostname.
- **5.15.0-1063-aws**: The kernel version.
- **#69~20.04.1-Ubuntu**: The specific build number and Ubuntu version.
- **SMP**: Indicates that the kernel is an SMP (Symmetric Multi-Processing) kernel, which means it supports multiple CPUs.
- **Fri May 10 19:20:12 UTC 2024**: The date and time when the kernel was compiled.
- **x86_64**: These values indicate the architecture of the processor/kernel and hardware platform.
- **GNU/Linux**: Indicates that the system is a GNU/Linux system.

&nbsp;
## Hostname

To see details about the hostname and related settings run `hostnamectl` command.

![image](https://github.com/user-attachments/assets/9cca9b67-ea74-444c-bb16-8aa270e82dfa)

- **Static hostname**: This is the permanent hostname assigned to the system. It's set in /etc/hostname and remains consistent across reboots.
- **Icon name**: This is a standardized name that represents the type of computer, often used in desktop environments to show appropriate icons.
- **Chassis**: This indicates the chassis type of the machine. In this case, it is vm, meaning a virtual machine.
- **Machine ID**: This is a unique identifier for the machine, typically stored in /etc/machine-id.
- **Boot ID**: This is a unique identifier for the current boot session, which changes every time the system is rebooted.
- **Virtualization**: This indicates the virtualization technology in use. In this case, xen is used, which is a hypervisor providing virtualization.
- **Operating System**: This provides information about the OS, which is Ubuntu 20.04.6 LTS

&nbsp;

## uptime

To see a quick snapshot of your system's current status, including the time, how long it's been running, how many users are logged in, and how busy the system is run `uptime` command.

![image](https://github.com/user-attachments/assets/3e11329a-506c-47a8-aba9-da1b8bdf3900)

&nbsp;

## Hardware Information

To display detailed info about the CPU architecture run `lscpu` command.

![image](https://github.com/user-attachments/assets/dba6cf57-d933-4629-aeda-6ac235cb0d1d)

- **Architecture**: It shows the CPU architecture (e.g., x86_64 for 64-bit processors).
- **CPU op-mode(s)**: This field shows the CPU modes supported (e.g., 32-bit and 64-bit).
- **CPU(s)**: The number of CPUs/cores available.
- **Model name**: This value shows the full name of the CPU.
- **Virtualization**: Whether the CPU supports virtualization

&nbsp;

## Disk Free

To see the amount of disk space used and available on the system run `df -h` command.

![image](https://github.com/user-attachments/assets/3d097ab4-e186-4d57-9864-470898771b8e)

The df command is crucial for monitoring disk usage. It helps you see how much space is used and how much is available, which is essential for managing storage resources.

&nbsp;

## List of Block Devices

To see information about block devices, such as disks and partitions, including their sizes, mount points, and other relevant details use `lsblk` command.

![image](https://github.com/user-attachments/assets/900683b3-e628-4d8c-9526-7766090eaffb)

&nbsp;

## Free Storage

To display memory usage info in human-readable format use `free -h` command.

![image](https://github.com/user-attachments/assets/f48dff8d-d0b5-4476-9ac8-cc9412235312)

&nbsp;

## Debian Package Manager Packages

Debian-based systems use package managers like dpkg and apt to install, update, and remove software.

### 1- dpkg

To list down the installed packages use `dpkg -l` command.

![image](https://github.com/user-attachments/assets/955ca7b6-8cf4-45e7-8648-dbed323e339b)

### 2 - apt

To list down all the packages installed through apt use `apt` command.

![image](https://github.com/user-attachments/assets/e5810f7c-e91b-42b9-bd23-959471c0f8f2)

&nbsp;

## Network Profiling

Commands like `ifconfig` or `ip a` show network interface details such as IP address, MAC address, network mask, and connection status.

![image](https://github.com/user-attachments/assets/1250306c-d037-4231-9277-3427e6c2b910)

To display routing table use command `ip r` or `route`

![image](https://github.com/user-attachments/assets/65b620af-8505-47f2-b2c2-fd5167d05d3e)

To show socket statistics and active connections use command `ss` or `netstat`

![image](https://github.com/user-attachments/assets/2a9e24f2-7c19-45c4-859e-522e7af60176)

&nbsp;

---
><details><summary>❓What is the Machine ID of the machine we are investigating?</summary>dc7c8ac5c09a4bbfaf3d09d399f10d96</details>
---
><details><summary>❓What is the architecture of the host we are investigating?</summary>x86_64</details>
---

&nbsp;

# Hunting for Processes

&nbsp;

From a forensics point of view, finding out what process is running on the suspected host and narrowing it down to identify the odd-looking process is crucial. It helps understand what's happening in the system.

| Command  | What It Does |
|----------|--------------|
| ps       | Shows a snapshot of currently running processes. |
| top      | Shows real-time process activity and system resource usage. |
| htop     | Like top, but easier to read and manage with colors and extra features. |
| pstree   | Shows processes in a tree format to see parent-child relationships. |
| pidof    | Finds the process ID (PID) of a program by its name. |
| pgrep    | Searches for processes by name or other attributes. |
| lsof     | Lists open files and the processes using them. |
| netstat  | Shows network connections and listening ports for processes. |
| strace   | Tracks system calls of a process to see exactly what it’s doing. |
| vmstat   | Displays memory and CPU stats to monitor overall system performance. |

&nbsp;

## Hunting for Suspicious Process

To list down all the running processes use Search Query: `SELECT pid, name, path, state FROM processes;`

![image](https://github.com/user-attachments/assets/c02d1209-62e6-4c1f-9ce2-c555d330080e)

The command retrieves a list of all currently running processes on the system and provides the following details for each process:
- **pid**: The unique process ID
- **name**: The name of the executable
- **path**: The full filesystem path to the executable
- **state**: The current state of the process (Idle / Sleep, etc.)

&nbsp;

## Processes Running From the tmp Directory

To narrow down the result to only show the processes running from the `/tmp/` or `/var/tmp/` directory use Query: `SELECT pid, name, path FROM processes WHERE path LIKE '/tmp/%' OR path LIKE '/var/tmp/%';`

![image](https://github.com/user-attachments/assets/b5f40612-ce11-4e33-b235-7739f56b7da3)

Two processes were initiated from the tmp directories, indicating the host is infected.

&nbsp;

## Hunting for Fileless Malware (Process)

To list the processes executing on the host but not on the disk run Search Query: `SELECT pid, name, path, cmdline, start_time FROM processes WHERE on_disk = 0;`D

![image](https://github.com/user-attachments/assets/03f3d8ba-4a9c-44a6-8562-3ee159120d36)

>[!NOTE]
>Not every process without a presence on disk can be indicated as suspicious.
>We have to investigate them further to determine.
>In the above query, the column on_disk can have two values: **1** indicates it is on the disk, and **0** indicates the process is not on the disk.

A fileless malware can have the following characteristics that we can observe:
- **No Disk Footprint**: It does not leave files on the disk, making it harder to detect using traditional file-based antivirus and security solutions.
- **Memory-Resident**: Operates entirely in the system's memory.
- **Persistence**: You might use scheduled tasks or other means to achieve persistence without placing files on the disk.

&nbsp;

## Orphan Processes

Normally, every process in Linux has a parent process. This parent-child relationship forms a process tree that can be viewed by the tool `pstree`. Intruders can create a process that becomes an orphan.

To list the processes without parent processes use Search Query: `SELECT pid, name, parent, path FROM processes WHERE parent NOT IN (SELECT pid from processes);`

![image](https://github.com/user-attachments/assets/25267e70-f236-4d98-bc42-02e1eb5d0d90)

&nbsp;

## Finding Processes Launched from User Directories

In the context of a server, if the process is running from the user directory, that process could be marked for further investigation. One of the main reasons is that, typically, system processes run from the standard system directories.

To search in the list of running processes and see which method is running from the user directory use Search Query: `SELECT pid, name, path, cmdline, start_time FROM processes WHERE path LIKE '/home/%' OR path LIKE '/Users/%';`

![image](https://github.com/user-attachments/assets/9dfdd710-c428-4af7-9853-870a1b07f81b)

The above search queries demonstrate a few angles of examining the running processes from a suspicious mindset. They could be good Indicators of Compromise (IOC) and can be used for further investigation and the creation of host-based detection rules.

&nbsp;

---
><details><summary>❓What is the name of the process running from the tmp directory? (Note: Not Hidden one)</summary>sshdd</details>
---
><details><summary>❓What is the name of the suspicious process running in the memory of the infected host?</summary>.systm_updater</details>
---
><details><summary>❓What is the name of the process runniing from the user directory?</summary>rdp_updater</details>
---

&nbsp;

# Investigating Network Connections

&nbsp;

Let's look at the network communication or connection initiated on this host, which could be identified as suspicious.

&nbsp;

## Listing Network Communication

To examine the network connections on the Linux host, there are various built-in command-line tools that we can use:

| Command   | What It Does |
|-----------|--------------|
| netstat   | Shows current network connections and open ports. |
| ss        | Same as netstat, but faster and more detailed. |
| tcpdump   | Captures network traffic to see what’s being sent/received. |
| iftop     | Shows which programs are using the most internet bandwidth. |
| lsof      | Lists open files and network connections for each program. |
| iptables  | Manages firewall rules and monitors network traffic. |
| nmap      | Scans your network to find connected devices and open ports. |
| ping      | Checks if another device on the network is reachable. |
| traceroute| Shows the path your data takes to reach another device. |
| dig       | Checks website addresses and DNS info. |
| hostname  | Shows the computer’s name and IP address. |
| ifconfig  | Shows info about your network interfaces. |
| ip        | Modern replacement for ifconfig; manages network settings. |
| arp       | Maps IP addresses to physical hardware addresses. |
| route     | Shows how your computer sends data to other networks. |
| curl      | Downloads or sends data to a website or server. |
| wget      | Downloads files from the internet. |
| netcat    | Sends or receives data across the network; useful for testing. |
| whois     | Shows who owns a domain name. |
| nslookup  | Finds the IP address for a website or vice versa. |

&nbsp;

## Network Connections

To retrieve information about network connections established by various processes on the system run Query: `SELECT pid, family, remote_address, remote_port, local_address, local_port, state FROM process_open_sockets LIMIT 20;`
It selects entries from the process_open_sockets table.

![image](https://github.com/user-attachments/assets/279a5980-cd7a-4bc3-8314-ea6b2fb05539)

&nbsp;

## Remote Connection

To list down all the network connections with a remote connection use Search Query: `SELECT pid, fd, socket, local_address, remote_address, local_port, remote_port FROM process_open_sockets WHERE remote_address IS NOT NULL;`

&nbsp;

## Examining DNS Queries

To retrieve info about the DNS queries on this host use Search Query: `SELECT * FROM dns_resolvers;`

![image](https://github.com/user-attachments/assets/67c5bb7f-c842-4bbe-ab5d-d32126e25b05)

&nbsp;

## Listing Down Network Interfaces

To retrieve the info about the network interface use Search Query: `SELECT * FROM interface_addresses;`

![image](https://github.com/user-attachments/assets/9550888e-5600-4708-b107-3e16f47e28c7)

&nbsp;

## List Network Connections

To lust down the listening ports use Search query: `SELECT * FROM listening_ports;`

![image](https://github.com/user-attachments/assets/f93a79c7-045e-4db3-9b5e-ef6fa29bfab2)

&nbsp;

---
><details><summary>❓What is the state of the local port that is listening on port 80?</summary>ESTABLISHED</details>
---

&nbsp;

# TTP Footprints on Disk

&nbsp;

After spotting suspicious processes and network activity, the next step is to check for malicious or altered files on the disk.

&nbsp;

## Open Files

List all the files opened.
Search Query: `SELECT pid, fd, path FROM process_open_files;`
This query will list all files that have been opened and are associated with some process. We can locate them through their respective pid.

![image](https://github.com/user-attachments/assets/46c17724-f26c-4ac7-8c11-8fe18e6f59ff)

&nbsp;

## Files Being Accessed From the tmp Directory

Filter the query to only show the files accessed from the `/tmp/`.
Search Query: `SELECT pid, fd, path FROM process_open_files where path LIKE '/tmp/%';`
This query will search for the processes that have opened files on the system. For this query, we only look at the files accessed from the `/tmp/` directory. In an actual investigation, we will have to look at various other locations.

![image](https://github.com/user-attachments/assets/92cad64f-b7ec-4741-a64c-95645a7e5cee)

Check ID 556.

Identify the process name using this pid with Search Query: `select pid, name, path from processes where pid = '556';`

![image](https://github.com/user-attachments/assets/33db62fb-c98b-4e20-9982-f989c04319cb)

&nbsp;

## Hidden Files

Use Search Query: `SELECT filename, path, directory, size, type FROM file WHERE path LIKE '/.%';` to examine  the root directory to track down hidden files or folders. In a real investigation, we will also need to examine other locations.

![image](https://github.com/user-attachments/assets/ab740139-0150-4a91-90aa-f893fae0b263)

Similarly, we can update the query to look at other directories like `/tmp/`, `/etc/`, `/usr/bin/`, etc.

&nbsp;

## Recently Modified Files

Use Search Query: `SELECT filename, path, directory, type, size FROM file WHERE path LIKE '/etc/%' AND (mtime > (strftime('%s', 'now') - 86400));` to see which file was recently modified.

![image](https://github.com/user-attachments/assets/19082fa3-b8f6-46bf-9120-ecc0a7bd22cb)

This query will look at the modified time (mtime) and list down the recently modified files. During a live investigation, this could be very useful in tracking down the system files or binaries that were recently modified.

&nbsp;

## Recently Modified Binaries

Look at the modification time and see which binary was modified recently by running Search Query: `SELECT filename, path, directory, mtime FROM file WHERE path LIKE '/opt/%' OR path LIKE '/bin/' AND (mtime > (strftime('%s', 'now') - 86400));`

![image](https://github.com/user-attachments/assets/662148c9-a27f-4ef4-976b-e9db07a7e1d8)

The above query only looks at the files and binaries updated in the last 24 hours in the `/opt/` or `/bin/` directories. 
We can update the time to get the updated results.

Note: There is expected to be no result when it is executed on the attached VM.

&nbsp;

## Finding Suspicious Packages

Search for the term `install` in the `/var/log/dpkg.log` file, which contains all the information about installed / uninstalled packages.

![image](https://github.com/user-attachments/assets/8a5e1578-4a1c-4a17-aecc-310c004e6c54)

There is a suspicious package installed on the 26th of June, 2024.

Run `dpkg -l | grep <REDACTED>` 
![image](https://github.com/user-attachments/assets/79559d47-de4e-4bf8-86da-fd7ef7d217c4)

&nbsp;

---
><details><summary>❓Investigate the opened files. What is the opened file associated with the suspicious process running on the system?</summary>keylogger.log</details>
---
><details><summary>❓What is the name of the process that is associated with the suspicious file found in the above question?</summary>sshdd</details>
---
><details><summary>❓What is the name of the hidden binary found in the root directory?</summary>.systmd</details>
---
><details><summary>❓What is the name of the suspicious package installed on the host?</summary>datacollector</details>
---
><details><summary>❓The suspicious package contains a secret code. What is the code hidden in the description?</summary>{***_**_******_*******}</details>
<!-- {NOT_SO_BENIGN_Package} -->
---

&nbsp;

# Persistence: Establishing Foothold

&nbsp;

After gaining a foothold, an intruder would first try to establish persistence to maintain hidden, ongoing access, and we can detect this on a Linux host by examining key system areas.

&nbsp;

## Investigating the Initialization Services

Adversaries often set up services on the infected host to maintain persistence and evade detection, which are stored in `/etc/systemd/system`, so checking this directory for unusual services can reveal hidden malicious activity.

![image](https://github.com/user-attachments/assets/5bc6fb6a-cea3-4f14-810e-0301fa8d7ad6)

Use `cat` command to read the content of the services.

![image](https://github.com/user-attachments/assets/f00cdf9e-c460-4c71-8c4a-daf418fd65e7)

It seems one of the services is using the Netcat utility to open a listening port that can be used as a backdoor entry.

&nbsp;

## Hunting for a Backdoor Account

Creating a backdoor user account is a common technique used by attackers to maintain access, so examining the system’s user list can help identify suspicious or unexpected accounts.

Search Query: `select username, directory from users;`

![image](https://github.com/user-attachments/assets/90ed4078-cb06-49a6-a88e-8065ef3e5bfe)

You can see the suspicious user account created on this host.

List down the names of the users from the `/etc/passwd` file by running `cut -d  : -f1 /etc/passwd`

![image](https://github.com/user-attachments/assets/8d51975c-92ff-4b28-a590-063c18fb5b28)

&nbsp;

## Examining Cron Jobs

Cron is a time-based job scheduler in Unix-like operating systems.
Intruders can create cron jobs to execute malicious scripts regularly, ensuring their activity continues after rebooting.

Examine the cron tables by running `crontab -l`

![image](https://github.com/user-attachments/assets/e1b4b353-1701-4b8c-9d12-37caf1103ef0)

It looks like a process from a hidden directory located in the backdoor account's directory.

&nbsp;

---
><details><summary>❓Which suspicious service was observed to be installed on this infected machine using netcat?</summary>systm.service</details>
---
><details><summary>❓What is the full path of the process found in the cron table?</summary>/home/badactor/storage/.secret_docs/rdp_updater</details>
---

&nbsp;
