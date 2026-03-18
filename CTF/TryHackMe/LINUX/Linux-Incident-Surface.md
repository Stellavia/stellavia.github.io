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

# Lab Connection

&nbsp;

![image](https://github.com/user-attachments/assets/299c67af-2447-45ad-a08e-5e56dd16ccaa)

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

&nbsp;

## Investigating Processes

### Activity # 1: Create and Run a Simple Process
In the `/home/activities/processes` directory, there is a code called `simple.c`, which runs on the system when executed. 
Note: It is important to note that all the commands we are running will be through the `root` user. Run the command `sudo su` to change the user from default to root.

1. **Compiling the Code** - run `gcc simple.c -o /tmp/simple` to compile and create an executable program
![image](https://github.com/user-attachments/assets/128eadc7-32fe-472e-b185-8bfc792f503d)

2. **Detecting the Footprints** - use `ps aux` to examine the running processes on the Linux.
![image](https://github.com/user-attachments/assets/083209d0-299b-4471-9517-152cafa70954)

`ps aux` displays all processes for all users in a detailed format. 
Flags:
- `a`: Shows processes for all users.
- `u`: Displays user-oriented format (includes user and start time).
- `x`: Includes processes not attached to a terminal (useful for finding background processes).

Filter out the output with `simple` process by running `ps aux | grep simple`
![image](https://github.com/user-attachments/assets/65457195-1a65-48e5-9384-f82968aef497)

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
![image](https://github.com/user-attachments/assets/0fcdace0-214e-4269-b6e9-6192beb9e712)

&nbsp;

## Process with Network Connection

In many incident scenarios, processes communicating via network to an external IP or the server are worth investigating. Therefore, examining the processes of making network connections and hunting down suspicious ones is very important.

Hence we will execute a process called `netcom` placed in the `home/activities/processes`.
To execute the process use `./netcom` - this will establish a network connection to a remote IP.

&nbsp;

## Investigating Network Communication

Use `ps aux | grep netcom` to filter the results to see whether the process is running on the system.
![image](https://github.com/user-attachments/assets/35ed94b1-56eb-4995-880c-e02c7d11c8c1)

The output confirms that the process is indeed running and has been assigned PID 267490, which will be different in your case.

In another terminal run `lsof -i -P -n` to see if there is any network connection associated with this PID.
![image](https://github.com/user-attachments/assets/c43c0d7c-ce3c-45b4-bf3c-b6510e20a908)

Let's break down the query and the flags first for better understanding:
- `lsof`: It stands of List Open Files. This command displays the information about the files opened by the processes.
- `-i`: This flag shows information about the network connections, including sockets and open network files.
- `-P`: This flag is used to display the port numbers.
- `-n`: This flag shows the IP address instead of resolving them to hostnames.

To explore processes and its network connection use `osquery`.
To start `osquery`, open a new terminal and run the `osqueryi` as a root
![image](https://github.com/user-attachments/assets/6e3349c8-24e1-4637-a112-064b607ed7a3)

Narrow down the resurt to display the network connection associated with this PID using `SELECT pid, fd, socket, local_address, remote_address FROM process_open_sockets WHERE pid = 267490;`
![image](https://github.com/user-attachments/assets/86b96c94-2635-4484-9d77-bc9007da0644)

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

![image](https://github.com/user-attachments/assets/d9c3a48b-ce75-4d39-a1ab-adb77b6572fe)

&nbsp;

## Identifying the Footprints

Let's pretend to be Bob, a blue teamer trying to examine various incident surfaces to identify the footprints of the backdoor account that was created.

1. Examining Logs:
One of the key places we could begin looking at would be the logs. 
All common logs can be found at the `/var/log/` location, as shown below:

![image](https://github.com/user-attachments/assets/60d77762-4f4e-4063-a19e-4f01c44bf7a5)

2. Examining auth.log
- use `cat auth.log | grep useradd` command
![image](https://github.com/user-attachments/assets/2deb3486-52a9-4ac2-b1ec-b73e7ae66818)

3. Examining /etc/passwd File
- use `cat /etc/passwd`
![image](https://github.com/user-attachments/assets/17c5d530-77b2-4108-b17a-ae6f375a2a2a)

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

![image](https://github.com/user-attachments/assets/12eca165-d46c-4c2b-9d82-08e25cfab32d)

&nbsp;

## Examining Malicious Cronjobs

Explore `/var/spool/cron/crontabs/[username]` to explore the cronjobs associated with each user, as shown below:

![image](https://github.com/user-attachments/assets/d92b43f0-0214-4d0a-9549-704daad094a2)

&nbsp;

## Activity 2 Services

Another way to achieve persistence on a compromised system is installing a service on the Linux server that will run in the background and start on every reboot. Let's learn how to investigate this activity by first creating a service and installing it on the disk:

1. **Create a Configuration File**: use `sudo nano /etc/systemd/system/suspicious.service`
- Add the following content to the configuration file:
![image](https://github.com/user-attachments/assets/085fb8fd-6cda-47f3-ad25-2522a928a8a1)

This configuration file will create a service and will execute the mentioned process. 
- `ExecStart`: Specifies the command to run the collector program. Adjust the path as necessary.
- `Restart=on-failure`: Ensures the service restarts if it fails.
- `User and Group`: Runs the service under the specified user and group. Adjust as needed for your security requirements.

2. **Enable and Start Service**
- reload the systemd manager config: `sudo systemctl daemon-reload`
- enable the service to run at startup: `sudo systemctl enable suspicious.service`
- start the service right away: `sudo systemctl start suspicious.service`

![image](https://github.com/user-attachments/assets/f4f592a2-8d1f-495d-987f-af11ca52f6df)

Check status of the service with `sudo systemctl status suspicious.service`

![image](https://github.com/user-attachments/assets/8d78d31f-5ba2-42f9-a26f-90833bafc029)

&nbsp;

## Examining the Running Service

Now that we know how an adversary could install and run the service in the background, let's see how we can find this service's footprints on the system.

1. **Reviewing the Directory** - all services installed and enabled on the Linux can be found in `/etc/systemd/system`
![image](https://github.com/user-attachments/assets/d69aa17d-d1e5-49d6-9b51-39c67f32189b)

2. **Evidence in the Logs** - look at the `/var/log/systlog`; search for suspicious by running `cat /var/log/syslog | grep suspicious` 
![image](https://github.com/user-attachments/assets/5a9f5db0-e54d-4c28-acb3-a5402f8ff537)

3. **Examining  Journalctl** - by using `sudo journalctl -u suspicious`
![image](https://github.com/user-attachments/assets/354d3acd-63d0-4621-93ce-ca50870b04a2)

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

&nbsp;

## File System and Directories

In the Linux filesystem, some files or directories contain sensitive information and can keep track of any attack attempt. 
Some of the key places are Configuration Files:
- `/etc/passwd` contains information about the user accounts.
![image](https://github.com/user-attachments/assets/290d50c3-1020-4667-9c25-e3ece89278d4)

- `/etc/shadow` contains hashed passwords for user accounts.
![image](https://github.com/user-attachments/assets/468221fb-c155-48f9-8200-372f98b9f596)

- `/etc/group` defines groups and the users associated with them. Groups are used to manage permissions and organize users with similar privileges.
![image](https://github.com/user-attachments/assets/4c9508cf-b059-4b7e-8eae-cc97618c26e6)

- `/etc/sudoers` configures sudo permissions, which can be a target for privilege escalation.
![image](https://github.com/user-attachments/assets/854448ca-8539-4e3c-b80e-8fca75ecb226)

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
![image](https://github.com/user-attachments/assets/d8aff651-bd76-4f64-b7f6-81924527004c)

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

![image](https://github.com/user-attachments/assets/6fbd6a64-50fe-4b11-a9f2-87b4fe90b789)

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

![image](https://github.com/user-attachments/assets/3ef06615-ed38-499a-9d75-b85080640da2)

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
