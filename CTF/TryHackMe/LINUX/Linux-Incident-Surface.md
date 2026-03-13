---
layout: default
title: Linux Incident Surface
description: Explore various areas of Incident Surface in Linux and how to identify the footprints of the incident.
---

🔗 [Link to the Room](https://tryhackme.com/room/linuxincidentsurface)

## 🏷️ Table of Contents

1. [Lab Connection](#lab-connection)<br>
2. [Linux Incident Surface - An Overview](#linux-incident-surface---an-overview)<br>
  2.1 [Linux Attack Surface](#linux-attack-surface)<br>
  2.2 [Linux Incident Surface](#linux-incident-surface)<br>
3. [Processes and Network Communication](#processes-and-network-communication)<br>
  3.1 [Investigating Processes](#investigating-processes)<br>
  3.2 [Process with Network Connection](#process-with-network-connection)<br>
  3.3 [Investigating Network Communication](#investigating-network-communication)<br>
  3.4 [Where Processes Fit in the Linux Incident Surface](#where-processes-fit-in-the-linux-incident-surface)<br>
4. [Persistence](#persistence)<br>
  4.1 [Persistence: Taking Foothold](#persistence-taking-foothold)<br>
  4.2 [Activity 1: Account Creation](#activity-1-account-creation)<br>
  4.3 [Identifying the Footprints](#identifying-the-footprints)<br>
  4.4 [Activity 2: Cron Job](#activity-2-cron-job)<br>
  4.5 [Examining Malicious Cronjobs](#examining-malicious-cronjobs)<br>
  4.6 [Activity 2 Services](#activity-2-services)<br>
  4.7 [Examining the Running Service](#examining-the-running-service)<br>
5. [Footprints on Disk](#footprints-on-disk)<br>
  5.1 [File System and Directories](#file-system-and-directories)<br>
  5.2 [Investigating Malicious Package](#investigating-malicious-package)<br>
  5.3 [Investigate the Suspicious Installed Package](#investigate-the-suspicious-installed-package)<br>
6. [Linux Logs](#linux-logs)<br>
  6.1 [Syslog](#syslog)<br>
  6.2 [Messages](#messages)<br>
  6.3 [Authentication Logs](#authentication-logs)<br>

---

# 📚 Study Notes 

The Linux Incident Surface focuses on all potential points or sources in the Linux system where an incident could occur or traces of incidents could be found. This could lead to a security breach, which could also be part of the Linux Attack Surface.

Linux Attack Surface refers to various entry points where an attack or unauthorized attempt could be made to enter the system or gain unauthorized attempts.



<!-- NO QUESTIONS HERE -->

&nbsp;

# Lab Connection

&nbsp;

<img width="387" height="143" alt="image" src="https://github.com/user-attachments/assets/4a4c96dc-411f-4779-9799-d57cdd45f892" />

Note: All the important files are placed in the /home/activities/processes directory.

&nbsp;

---
><details><summary>❓Connect with the lab. How many files and folders are in the /home/activities/processes directory?</summary>3</details>
>✅Run ls -la /home/activities/processes
---

&nbsp;

# Linux Incident Surface - An Overview

&nbsp;

The **Linux Incident Surface** is all the places on a Linux system where traces of an attack can be found. It’s what security analysts check after a system has been compromised.

| Concept | What it is | Goal |
|---------|------------|------|
| **Attack Surface** | Points where an attacker could try to break in (open ports, vulnerable software, running services, network connections) | Reduce the number of ways attackers can get in |
| **Incident Surface** | Areas where signs of an attack can be detected and investigated (logs, running processes, network traffic, file integrity) | Detect, respond to, and recover from an attack |

Understanding the incident surface helps analysts quickly spot attacks, limit damage, restore systems, and prevent future incidents.

&nbsp;

## Linux Attack Surface

The Linux Attack Surface refers to all the points of interaction in a Linux system where an adversary might attempt to exploit vulnerabilities to gain unauthorized access or carry out malicious activities. 

Some of the key entry points that could be identified as part of the Linux Attack Surface are: Open ports, running services, running software or applications with vulnerabilities, network communication

The primary goal is to minimize the attack surface by reducing potential weaknesses from the areas the attacker could exploit. Some of the steps that are involved in achieving this goal are:
- Identifying and patching the vulnerabilities
- Minimizing the usage of unwanted services
- Check the interfaces where the user interacts
- Minimizing the publically exposed services, applications, ports, etc

&nbsp;

## Linux Incident Surface

It refers to all the system areas involved in the detection, management, and response to an actual security incident (post-compromise). 
It includes where security breaches may be detected and analyzed and where a response plan must be implemented to mitigate the incident.

Key points where we could find the incident traces:
- System logs
- auth.log, syslog, krnl.log, etc
- Network traffic
- bRunning processes
- Running services
- The integrity of the files and processes

<!-- NO QUESTIONS HERE -->

&nbsp;

# Processes and Network Communication

&nbsp;

- Processes and network communication are key areas when investigating incidents on Linux systems.  
- Monitoring running processes, especially those that communicate over the network, can help identify suspicious activity.  
- Processes may leave footprints showing attacks, malware execution, or unauthorized connections.  
- Running processes are part of the system areas that forensic analysts check for potential evidence of compromise.

<img width="100" height="100" alt="5e8dd9a4a45e18443162feab-1726082309841" src="https://github.com/user-attachments/assets/81a7d599-3f14-4244-acb8-d8bec06e002d" />

&nbsp;

## Investigating Processes

### Activity # 1: Create and Run a Simple Process
In the `/home/activities/processes` directory, there is a code called `simple.c`, which runs on the system when executed. 
Note: It is important to note that all the commands we are running will be through the `root` user. Run the command `sudo su` to change the user from default to root.

1. **Compiling the Code** - run `gcc simple.c -o /tmp/simple` to compile and create an executable program
<img width="639" height="89" alt="image" src="https://github.com/user-attachments/assets/a16a124a-0f40-48dd-8abe-aabb0c2cfff0" />

2. **Detecting the Footprints** - use `ps aux` to examine the running processes on the Linux.
<img width="861" height="653" alt="5e8dd9a4a45e18443162feab-1726068688931" src="https://github.com/user-attachments/assets/bb465215-2d0e-4f15-ad24-ecfa1e3fe57a" />

`ps aux` displays all processes for all users in a detailed format. 
Flags:
- `a`: Shows processes for all users.
- `u`: Displays user-oriented format (includes user and start time).
- `x`: Includes processes not attached to a terminal (useful for finding background processes).

Filter out the output with `simple` process by running `ps aux | grep simple`
<img width="837" height="82" alt="5e8dd9a4a45e18443162feab-1726084760603" src="https://github.com/user-attachments/assets/dabea7c8-17d7-441b-bf97-ed8e7a8288f4" />

The output provides the following information:
- `USER`: The user who owns the process.
- `PID`: Process ID.
- `%CPU`: CPU usage percentage.
- `%MEM`: Memory usage percentage.
- `VSZ`: Virtual memory size.
- `RSS`: Resident Set Size (memory currently used).
- `TTY`: Terminal associated with the process.
- `STAT`: Process state (e.g., R for running, S for sleeping, Z for zombie).
- `START`: Start time of the process.
- `COMMAND`: Command that started the process.

Examine the files/resources connected with this process by using `lsof` with `PID`. As `PID` use the one assigned to our process which is 49782. Hence the syntax is `lsof -p 49782`
<img width="891" height="259" alt="5e8dd9a4a45e18443162feab-1726084649872" src="https://github.com/user-attachments/assets/95beb962-a11c-448d-b6fa-751646716457" />

&nbsp;

## Process with Network Connection

In many incident scenarios, processes communicating via network to an external IP or the server are worth investigating. Therefore, examining the processes of making network connections and hunting down suspicious ones is very important.

Hence we will execute a process called `netcom` placed in the `home/activities/processes`.
To execute the process use `./netcom` - this will establish a network connection to a remote IP.

&nbsp;

## Investigating Network Communication

Use `ps aux | grep netcom` to filter the results to see whether the process is running on the system.
<img width="842" height="63" alt="5e8dd9a4a45e18443162feab-1726090661088" src="https://github.com/user-attachments/assets/1c18af1d-6d96-432f-b4b3-3bcd11115c94" />
The output confirms that the process is indeed running and has been assigned PID 267490, which will be different in your case.

In another terminal run `lsof -i -P -n` to see if there is any network connection associated with this PID.
<img width="1086" height="500" alt="5e8dd9a4a45e18443162feab-1726090877436" src="https://github.com/user-attachments/assets/47015abe-2005-46ea-9289-96da5cf5841d" />

Let's break down the query and the flags first for better understanding:
- `lsof`: It stands of List Open Files. This command displays the information about the files opened by the processes.
- `-i`: This flag shows information about the network connections, including sockets and open network files.
- `-P`: This flag is used to display the port numbers.
- `-n`: This flag shows the IP address instead of resolving them to hostnames.

To explore processes and its network connection use `osquery`.
To start `osquery`, open a new terminal and run the `osqueryi` as a root
<img width="679" height="148" alt="5e8dd9a4a45e18443162feab-1726497883151" src="https://github.com/user-attachments/assets/844105a5-7627-4c43-98fe-dbd5310c89a9" />

Narrow down the resurt to display the network connection associated with this PID using `SELECT pid, fd, socket, local_address, remote_address FROM process_open_sockets WHERE pid = 267490;`
<img width="1026" height="122" alt="5e8dd9a4a45e18443162feab-1726091590966" src="https://github.com/user-attachments/assets/861ca97f-3ddc-45c6-b84d-92fe20a86389" />

&nbsp;

## Where Processes Fit in the Linux Incident Surface

Processes can be exploited, manipulated, or used by attackers to execute malicious activities. It is important to investigate the processes running on the system from various angles. The following points are a few use cases of the incidents related to processes:

- A process running from a tmp directory (context matters).
- A suspicious child-parent process.
- Process with a suspicious network connection.
- Orphan process. Not all orphan processes are suspicious, but those with no parent process associated after execution are worth investigating.
- Suspicious processes that are running through a cronjob.
- System-related processes or binaries running from the tmp directory or user directories.

&nbsp;

---
><details><summary>❓What is the remote IP to which the process netcom establishes the connection?</summary>68.53.23.246</details>
---
><details><summary>❓Update the osquery command. What is the remote port the netcom process is communicating to?</summary>443</details>
---

&nbsp;

# Persistence

&nbsp;

**Persistence** refers to techniques attackers use to maintain access to a compromised system even after the initial exploit.  

To understand how incidents are detected on a Linux endpoint, we will:
1. Perform the attack  
2. Examine how and where the attack leaves footprints 

&nbsp;

## Persistence: Taking Foothold

Investigating persistence is crucial, as it’s often one of the first steps an intruder takes after post-exploitation.  
Some of the attack actions that can result in persistence on a Linux machine are explained below:

&nbsp;
## Activity 1: Account Creation

In an assumed compromised scenario, let's pretend to be Alice, who has got hold of the system. Start by creating a backdoor account using following commands to create an account `attacker` and will be added into the sudo group:
`sudo useradd attacker -G sudo`
`sudo passwd attacker`
`echo "attacker ALL=(ALL:ALL) ALL" | sudo tee -a /etc/sudoers`

<img width="862" height="208" alt="image" src="https://github.com/user-attachments/assets/c9bb1858-9de8-4578-94c5-00e2654799bd" />

&nbsp;

## Identifying the Footprints

Let's pretend to be Bob, a blue teamer trying to examine various incident surfaces to identify the footprints of the backdoor account that was created.

1. Examining Logs:
One of the key places we could begin looking at would be the logs. 
All common logs can be found at the `/var/log/` location, as shown below:

<img width="909" height="591" alt="image" src="https://github.com/user-attachments/assets/4435acd1-1fa3-4af6-8d25-0051b68703ef" />

2. Examining auth.log
- use `cat auth.log | grep useradd` command
<img width="1113" height="186" alt="image" src="https://github.com/user-attachments/assets/89a69bd8-81cc-49b2-9b76-bd2e35586f7f" />

3. Examining /etc/passwd File
- use `cat /etc/passwd`
<img width="857" height="352" alt="image" src="https://github.com/user-attachments/assets/b1e6f43c-3d0a-4fc4-a1dd-6a03e346827c" />

In the output, we can see all the accounts, including the one we just created. Some of the information this file contains are:
- Username.
- the password placeholder is represented by x or *, indicating that the password is stored in the/etc/shadow file.
- User ID assigned to the user
- Group ID assigned to the user.
- User's home directory.
- Path to user's default shell.

&nbsp;

## Activity 2: Cron Job

Another persistence method is cron jobs, which attackers can use to maintain persistent access to a compromised system. Cron is a time-based job scheduler in Unix-like systems that allows tasks (scripts, commands, or programs) to be executed automatically at specified intervals.

To create a malicious cron job, we can modify the crontab file or use the crontab command to edit scheduled jobs for the current user or system using `crontab -e` which will open the crontab file, and we can add entry.

Examples of Crontab Entry:
- `@reboot /path/to/malicious/script.sh`: This command will execute the `script.sh` at every reboot.
- `* * * * * root /path/to/malicious/script.sh`: This command will execute `script.sh` every minute with root privileges.


<img width="758" height="567" alt="image" src="https://github.com/user-attachments/assets/5c96a7bc-de15-4e4e-90cf-1f313a4220bc" />

&nbsp;

## Examining Malicious Cronjobs

Explore `/var/spool/cron/crontabs/[username]` to explore the cronjobs associated with each user, as shown below:
<img width="541" height="198" alt="5e8dd9a4a45e18443162feab-1726095701901" src="https://github.com/user-attachments/assets/7c7005b8-cc6e-450b-bbc0-6be393772f0c" />

&nbsp;

## Activity 2 Services

Another way to achieve persistence on a compromised system is installing a service on the Linux server that will run in the background and start on every reboot. Let's learn how to investigate this activity by first creating a service and installing it on the disk:

1. **Create a Configuration File**: use `sudo nano /etc/systemd/system/suspicious.service`
- Add the following content to the configuration file:
  <img width="410" height="275" alt="image" src="https://github.com/user-attachments/assets/6771f4d9-338d-4a2c-95fb-34f61adf9f65" />

This configuration file will create a service and will execute the mentioned process. 
- `ExecStart`: Specifies the command to run the collector program. Adjust the path as necessary.
- `Restart=on-failure`: Ensures the service restarts if it fails.
- `User and Group`: Runs the service under the specified user and group. Adjust as needed for your security requirements.

2. **Enable and Start Service**
- reload the systemd manager config: `sudo systemctl daemon-reload`
- enable the service to run at startup: `sudo systemctl enable suspicious.service`
- start the service right away: `sudo systemctl start suspicious.service`

<img width="1087" height="127" alt="5e8dd9a4a45e18443162feab-1726094550783" src="https://github.com/user-attachments/assets/0565fa27-7604-434f-8a3c-e7d133439b94" />

Check status of the service with `sudo systemctl status suspicious.service`

<img width="892" height="214" alt="5e8dd9a4a45e18443162feab-1726094644510" src="https://github.com/user-attachments/assets/2644fc76-4ade-4d44-9e35-400b91c6f768" />

&nbsp;

## Examining the Running Service

Now that we know how an adversary could install and run the service in the background, let's see how we can find this service's footprints on the system.

1. **Reviewing the Directory** - all services installed and enabled on the Linux can be found in `/etc/systemd/system`
<img width="1404" height="270" alt="5e8dd9a4a45e18443162feab-1726094951470" src="https://github.com/user-attachments/assets/dbcc7985-9634-4416-ad24-2ec6a1749af9" />

2. **Evidence in the Logs** - look at the `/var/log/systlog`; search for suspicious by running `cat /var/log/syslog | grep suspicious` 
<img width="1354" height="277" alt="5e8dd9a4a45e18443162feab-1726095117167" src="https://github.com/user-attachments/assets/de6182be-0c4e-4a23-aeec-a4ccc4e13f18" />

3. **Examining  Journalctl** - by using `sudo journalctl -u suspicious`
<img width="1352" height="313" alt="5e8dd9a4a45e18443162feab-1726095214921" src="https://github.com/user-attachments/assets/9e9b7584-090c-44bb-9bf6-00afbba260f4" />

&nbsp;

---
><details><summary>❓What is the default path that contains all the installed services in Linux?</summary>/etc/systemd/system</details>
---
><details><summary>❓Which suspicious service was found to be running on the host?</summary>benign.service</details>
---
><details><summary>❓What process does this service point to?</summary>benign</details>
---
><details><summary>❓Before getting this service stopped on 11th Sept, how many log entries were observed in the journalctl against this service?</summary>7</details>
---

&nbsp;

# Footprints on Disk

&nbsp;

Attackers often leave **traces on the filesystem** during their activities. 
From a **forensics and incident response** perspective, these areas-called **incident surfaces** - are critical to examine.

- **Disk Image:** A snapshot of the disk that preserves all data, including deleted files and system artifacts.
- **Incident Surfaces:** Areas on the disk that attackers may target, such as configuration files, logs, and user data.
- **Purpose:** By analyzing these surfaces, forensic analysts can uncover:
  - Evidence of compromise
  - Attack patterns
  - Tools or malware used
  - Actions performed by the attacker

 Investigating disk footprints is key to understanding the **scope and impact of a security incident**.

<img width="750" height="800" alt="5e8dd9a4a45e18443162feab-1726081573301" src="https://github.com/user-attachments/assets/5b54fd96-bf95-42d0-bc09-dfb921f7341e" />

&nbsp;

## File System and Directories

In the Linux filesystem, some files or directories contain sensitive information and can keep track of any attack attempt. 
Some of the key places are Configuration Files:
- `/etc/passwd` contains information about the user accounts.
<img width="842" height="438" alt="5e8dd9a4a45e18443162feab-1725839971884" src="https://github.com/user-attachments/assets/74eaa038-2cd0-4aad-947c-3306f1ca8a21" />

- `/etc/shadow` contains hashed passwords for user accounts.
<img width="1290" height="492" alt="5e8dd9a4a45e18443162feab-1725840080237" src="https://github.com/user-attachments/assets/6332eb22-173d-42bf-970c-f9731d97b015" />

- `/etc/group` defines groups and the users associated with them. Groups are used to manage permissions and organize users with similar privileges.
<img width="571" height="627" alt="5e8dd9a4a45e18443162feab-1726533006246" src="https://github.com/user-attachments/assets/2f91134f-6ac0-4ee9-93fe-d5de3a9a7503" />

- `/etc/sudoers` configures sudo permissions, which can be a target for privilege escalation.
<img width="939" height="561" alt="5e8dd9a4a45e18443162feab-1726081751300" src="https://github.com/user-attachments/assets/0b0b3bca-ede9-426d-a14c-9df6957005fc" />

&nbsp;

## Investigating Malicious Package

Attackers can leverage Debian packages to compromise a Linux system. 
This can happen in two main ways:
1. **Attacker-created packages:** Malicious packages installed intentionally to execute harmful actions.  
2. **User-tricked packages:** A user is deceived into installing a malicious package, unknowingly allowing compromise.

**Practical Exercise:**  

1.** Create Directory**: run `mkdir` to create a directory for the package, e.g.: `mkdir malicious-package`

2. **Create Control File**: It should contain info or metadata about the package we will build and install on this host. Open a file in any text editor within the DEBIAN folder and name it control. Add the content mentioned below to the file before saving it:

`Package: malicious-package` <br>
`Version: 1.0` <br>
`Section: base` <br>
`Priority: optional` <br>
`Architecture: all` <br>
`Maintainer: attacker@test.com` <br>
`Description: This is a malicious Package`

3. **Add Malicious Script**: create a malicious script with the content below and save it as `postinst` and place it in the DEBIAN directory:
`#!/bin/bash`
`# Malicious post-installation script`
`# It will run this script after installation`
`# Print a suspicious message - for demonstration`
`echo "something suspicious"`

4. **Make the Script Executable**: change permission with `chmod 775`, syntax: `chmod 755 malicious-package/DEBIAN/postinst`

5. **Build the Package**: run `dpkg-deb --build malicious-package`

6. **Install the Package**: run `dpkg -i malicious-package.deb`

&nbsp;

## Investigate the Suspicious Installed Package

1. **Check the Installed Packages**: run `dpkg -l`
<img width="1637" height="169" alt="5e8dd9a4a45e18443162feab-1726567411451" src="https://github.com/user-attachments/assets/72052896-9973-4ff7-9548-a37699db70eb" />
- this command will display all the installed packages on the disk.

2. **Examining dpkg.log**: run `grep " install " /var/log/dpkg.log`

&nbsp;

---
><details><summary>❓Create a suspicious Debian package on the disk by following the steps mentioned in the task. How many log entries are observed in the dpkg.log file associated with this installation acitvity?</summary>6</details>
---
><details><summary>❓What package was installed on the system on the 17th of September, 2024?</summary>c2comm</details>
---

&nbsp;

# Linux Logs

&nbsp;

Logs are records of events that happen on a system.  
They help administrators and security analysts understand **what happened on a machine**, detect attacks, and investigate incidents.

Most Linux logs are stored in **/var/log**

&nbsp;

## Syslog

- Location: `/var/log/syslog`
- Records **system-wide events**
- Includes messages from the **kernel, services, and applications**

Useful for:
- detecting system errors
- troubleshooting services
- monitoring overall system health

<img width="1331" height="453" alt="5e8dd9a4a45e18443162feab-1725847386528" src="https://github.com/user-attachments/assets/cbbd0549-8c8d-4fbb-8145-c59d6d23cf5a" />

&nbsp;

## Messages

- Location: `/var/log/messages`
- Stores **system messages and kernel logs**
- Tracks hardware and system-level activity

Useful for:
- diagnosing system problems
- detecting hardware issues
- identifying suspicious kernel activity

Example threat indicator: repeated **kernel panic messages** (possible denial-of-service attempt)

&nbsp;

## Authentication Logs

- Location: `/var/log/auth.log`
- Records **authentication attempts**
- Includes: successful logins, failed login attempts, privilege escalation attempts (sudo)

Useful for detecting:
- brute-force attacks
- unauthorized login attempts
- suspicious login times

Example indicator: many failed login attempts from the same IP address

<img width="1076" height="549" alt="5e8dd9a4a45e18443162feab-1725851831948" src="https://github.com/user-attachments/assets/0a3585c1-19ef-4ec1-afee-3dcb56fe2f05" />

Logs may reveal signs of an attack, such as:
- multiple **failed login attempts**
- successful login at **unusual times** (late night or weekends)
- **suspicious network communication**
- system errors or unusual crashes
- **new user accounts created** on sensitive servers
- unexpected **outbound traffic from a web server**

Linux logs are critical for **monitoring system health**, **detecting attacks**, **investigating incidents** and **understanding system activity**

>[!NOTE]
> When analyzing Linux logs, these three commands are essential for quickly filtering, viewing, and monitoring system activity:
> 1. `grep` - **search for Patterns in Logs**. `grep` lets you search log files for specific keywords or patterns.
> 2. `journalctl` - **view systemd logs**. `journalctl` is powerful for checking system and service activity.
> 3. `tail -f` - **monitor logs live**. It lets you watch log files as they're updated, perfect for live monitoring.
> 

&nbsp;

---
><details><summary>❓Examine the auth.log files. Which user attempted to connect with SSH on 11th Sept 2024?</summary>saqib</details>
---
><details><summary>❓From which IP was this failed SSH connection attempt made?</summary>10.11.75.247</details>
---

&nbsp;
