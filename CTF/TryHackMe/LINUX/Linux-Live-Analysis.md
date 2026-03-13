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

![image](https://github.com/user-attachments/assets/ee070ed3-51cb-4fa6-b235-ef825b619781)

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

<img width="657" height="794" alt="image" src="https://github.com/user-attachments/assets/3ea6927d-28fd-4013-90ee-a9c26f1f88cc" />

&nbsp;

## Osquery Table Schemas

Check [osquery website](https://osquery.io/schema/5.12.1/) to view table schemas:

![image](https://github.com/user-attachments/assets/9a27f633-e600-4830-afd4-36cb467a954c)

&nbsp;

## Users Account

To retrieve info about the user accounts created on the host use Search Query: `Select username, uid, description from users;`

![image](https://github.com/user-attachments/assets/345e03f1-7326-4516-9efe-3dfbfb63c0bb)

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

![image](https://github.com/user-attachments/assets/56b2f676-2873-4842-8247-7f27c53d3d7d)

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

![image](https://github.com/user-attachments/assets/5ff91e0e-1a53-4cfd-8f33-c83f84c5d5d3)

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

![image](https://github.com/user-attachments/assets/a43a8680-eb86-4ff9-914d-475a8ea7cff4)

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

![image](https://github.com/user-attachments/assets/56efdd87-1ce5-49a4-bf47-f4e5a7599f92)

&nbsp;

## Hardware Information

To display detailed info about the CPU architecture run `lscpu` command.

![image](https://github.com/user-attachments/assets/b333501f-9877-426f-a0a0-fc9d05f66d60)

- **Architecture**: It shows the CPU architecture (e.g., x86_64 for 64-bit processors).
- **CPU op-mode(s)**: This field shows the CPU modes supported (e.g., 32-bit and 64-bit).
- **CPU(s)**: The number of CPUs/cores available.
- **Model name**: This value shows the full name of the CPU.
- **Virtualization**: Whether the CPU supports virtualization

&nbsp;

## Disk Free

To see the amount of disk space used and available on the system run `df -h` command.

![image](https://github.com/user-attachments/assets/eb0da5e1-f2ea-414e-9ee0-0ef1d38a933c)

The df command is crucial for monitoring disk usage. It helps you see how much space is used and how much is available, which is essential for managing storage resources.

&nbsp;

## List of Block Devices

To see information about block devices, such as disks and partitions, including their sizes, mount points, and other relevant details use `lsblk` command.

![image](https://github.com/user-attachments/assets/ee51cd76-d689-46dd-b098-8b3f5897a16c)

&nbsp;

## Free Storage

To display memory usage info in human-readable format use `free -h` command.

![image](https://github.com/user-attachments/assets/a30bf380-5fc4-4ab3-b584-38c034fde12d)

&nbsp;

## Debian Package Manager Packages

Debian-based systems use package managers like dpkg and apt to install, update, and remove software.

### 1- dpkg

To list down the installed packages use `dpkg -l` command.

![image](https://github.com/user-attachments/assets/4286c7be-24f2-430f-8528-77072690ad03)

### 2 - apt

To list down all the packages installed through apt use `apt` command.

![image](https://github.com/user-attachments/assets/b9ad7a86-57d0-4021-8161-bdc3e1ef6519)

&nbsp;

## Network Profiling

Commands like `ifconfig` or `ip a` show network interface details such as IP address, MAC address, network mask, and connection status.

![image](https://github.com/user-attachments/assets/6d27bfc0-66c7-4f6e-99ee-31d4b11d27bc)

To display routing table use command `ip r` or `route`

![image](https://github.com/user-attachments/assets/c7b6bc4b-16fc-4707-ab45-945633d061e7)

To show socket statistics and active connections use command `ss` or `netstat`

![image](https://github.com/user-attachments/assets/3de2d27e-1a74-469e-af1d-be9f2ec2eaa0)

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

![image](https://github.com/user-attachments/assets/430d7b6b-0d2a-44c3-b1d4-d0ee2d32839c)

The command retrieves a list of all currently running processes on the system and provides the following details for each process:
- **pid**: The unique process ID
- **name**: The name of the executable
- **path**: The full filesystem path to the executable
- **state**: The current state of the process (Idle / Sleep, etc.)

&nbsp;

## Processes Running From the tmp Directory

To narrow down the result to only show the processes running from the `/tmp/` or `/var/tmp/` directory use Query: `SELECT pid, name, path FROM processes WHERE path LIKE '/tmp/%' OR path LIKE '/var/tmp/%';`

![image](https://github.com/user-attachments/assets/cec919a8-29e4-4862-9ffa-63102763b7fd)

Two processes were initiated from the tmp directories, indicating the host is infected.

&nbsp;

## Hunting for Fileless Malware (Process)

To list the processes executing on the host but not on the disk run Search Query: `SELECT pid, name, path, cmdline, start_time FROM processes WHERE on_disk = 0;`D

![image](https://github.com/user-attachments/assets/249a10ce-6116-4ab8-b85e-205ad228387f)

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

<img width="870" height="204" alt="image" src="https://github.com/user-attachments/assets/04b89958-f2a7-4619-9612-3865f81d1912" />

&nbsp;

## Finding Processes Launched from User Directories

In the context of a server, if the process is running from the user directory, that process could be marked for further investigation. One of the main reasons is that, typically, system processes run from the standard system directories.

To search in the list of running processes and see which method is running from the user directory use Search Query: `SELECT pid, name, path, cmdline, start_time FROM processes WHERE path LIKE '/home/%' OR path LIKE '/Users/%';`

<img width="985" height="181" alt="image" src="https://github.com/user-attachments/assets/8e78b936-8124-4899-88c3-837d7c304777" />

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

<img width="1041" height="599" alt="image" src="https://github.com/user-attachments/assets/7c590c20-6072-4b71-b764-f45f1f86ed10" />

&nbsp;

## Remote Connection

To list down all the network connections with a remote connection use Search Query: `SELECT pid, fd, socket, local_address, remote_address, local_port, remote_port FROM process_open_sockets WHERE remote_address IS NOT NULL;`

<img width="1386" height="663" alt="image" src="https://github.com/user-attachments/assets/deae6be3-4940-47a5-8408-52eff2486e1d" />


&nbsp;

## Examining DNS Queries

To retrieve info about the DNS queries on this host use Search Query: `SELECT * FROM dns_resolvers;`

<img width="686" height="198" alt="image" src="https://github.com/user-attachments/assets/455cd21e-42d9-465a-bd6e-15fa3cdf47c6" />

&nbsp;

## Listing Down Network Interfaces

To retrieve the info about the network interface use Search Query: `SELECT * FROM interface_addresses;`

<img width="839" height="243" alt="image" src="https://github.com/user-attachments/assets/7db0ef82-6f8b-470f-8d26-c324e8279c6f" />

&nbsp;

## List Network Connections

To lust down the listening ports use Search query: `SELECT * FROM listening_ports;`

<img width="781" height="376" alt="image" src="https://github.com/user-attachments/assets/a6272308-c754-4c25-8505-ed55b24bf006" />

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

<img width="557" height="446" alt="image" src="https://github.com/user-attachments/assets/0a8f0b52-d853-4bb6-bd1f-7b3111e55c53" />

&nbsp;

## Files Being Accessed From the tmp Directory

Filter the query to only show the files accessed from the `/tmp/`.
Search Query: `SELECT pid, fd, path FROM process_open_files where path LIKE '/tmp/%';`
This query will search for the processes that have opened files on the system. For this query, we only look at the files accessed from the `/tmp/` directory. In an actual investigation, we will have to look at various other locations.

<img width="688" height="263" alt="image" src="https://github.com/user-attachments/assets/3f79119b-b1e3-4f28-934c-bac0635cb9aa" />

Check ID 556.

Identify the process name using this pid with Search Query: `select pid, name, path from processes where pid = '556';`

<img width="685" height="183" alt="image" src="https://github.com/user-attachments/assets/602ec62f-ff50-4d95-b85f-afb498462b0b" />

&nbsp;

## Hidden Files

Use Search Query: `SELECT filename, path, directory, size, type FROM file WHERE path LIKE '/.%';` to examine  the root directory to track down hidden files or folders. In a real investigation, we will also need to examine other locations.

<img width="775" height="234" alt="image" src="https://github.com/user-attachments/assets/b6412d8b-b77d-41c4-9265-80d33fe83fcc" />

Similarly, we can update the query to look at other directories like `/tmp/`, `/etc/`, `/usr/bin/`, etc.

&nbsp;

## Recently Modified Files

Use Search Query: `SELECT filename, path, directory, type, size FROM file WHERE path LIKE '/etc/%' AND (mtime > (strftime('%s', 'now') - 86400));` to see which file was recently modified.

<img width="1103" height="205" alt="image" src="https://github.com/user-attachments/assets/ea538df5-32eb-43fc-8e82-7254eb5e488f" />

This query will look at the modified time (mtime) and list down the recently modified files. During a live investigation, this could be very useful in tracking down the system files or binaries that were recently modified.

&nbsp;

## Recently Modified Binaries

Look at the modification time and see which binary was modified recently by running Search Query: `SELECT filename, path, directory, mtime FROM file WHERE path LIKE '/opt/%' OR path LIKE '/bin/' AND (mtime > (strftime('%s', 'now') - 86400));`

<img width="1308" height="241" alt="image" src="https://github.com/user-attachments/assets/5941ae14-50c4-4d6f-81cb-046c194fb702" />

The above query only looks at the files and binaries updated in the last 24 hours in the `/opt/` or `/bin/` directories. 
We can update the time to get the updated results.

Note: There is expected to be no result when it is executed on the attached VM.

&nbsp;

## Finding Suspicious Packages

Search for the term `install` in the `/var/log/dpkg.log` file, which contains all the information about installed / uninstalled packages.

<img width="856" height="205" alt="image" src="https://github.com/user-attachments/assets/ddf98392-adf7-4a6b-a9af-d65f7c1e2a96" />

There is a suspicious package installed on the 26th of June, 2024.

Run `dpkg -l | grep <REDACTED>` 
<img width="789" height="142" alt="image" src="https://github.com/user-attachments/assets/7f7f133e-9193-434d-96f7-f159e91dea44" />

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

<img width="456" height="275" alt="image" src="https://github.com/user-attachments/assets/2adabc11-49a3-46ff-92b3-05fbb600ed86" />

Use `cat` command to read the content of the services.

<img width="500" height="188" alt="5e8dd9a4a45e18443162feab-1720623327859" src="https://github.com/user-attachments/assets/93138655-237e-436a-9cff-03be8062a379" />

It seems one of the services is using the Netcat utility to open a listening port that can be used as a backdoor entry.

&nbsp;

## Hunting for a Backdoor Account

Creating a backdoor user account is a common technique used by attackers to maintain access, so examining the system’s user list can help identify suspicious or unexpected accounts.

Search Query: `select username, directory from users;`

<img width="510" height="359" alt="image" src="https://github.com/user-attachments/assets/d09c20a2-2d16-4f35-b44d-8028b457bf01" />

You can see the suspicious user account created on this host.

List down the names of the users from the `/etc/passwd` file by running `cut -d  : -f1 /etc/passwd`

<img width="478" height="286" alt="image" src="https://github.com/user-attachments/assets/900f78a5-0918-47eb-9fb9-63a64eed28ca" />

&nbsp;

## Examining Cron Jobs

Cron is a time-based job scheduler in Unix-like operating systems.
Intruders can create cron jobs to execute malicious scripts regularly, ensuring their activity continues after rebooting.

Examine the cron tables by running `crontab -l`

<img width="563" height="607" alt="image" src="https://github.com/user-attachments/assets/25c60e83-48fa-4c55-9b6d-6fba0681ae7d" />

It looks like a process from a hidden directory located in the backdoor account's directory.

&nbsp;

---
><details><summary>❓Which suspicious service was observed to be installed on this infected machine using netcat?</summary>systm.service</details>
---
><details><summary>❓What is the full path of the process found in the cron table?</summary>/home/badactor/storage/.secret_docs/rdp_updater</details>
---

&nbsp;
