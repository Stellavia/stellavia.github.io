---
layout: default
title: Linux Fundamentas Part 3
description: Power-up your Linux skills and get hands-on with some common utilities that you are likely to use day-to-day!
---

🔗 [Link to the Room](https://tryhackme.com/room/linuxfundamentalspart3)

## 🏷️ Table of Contents

1. [Terminal Text Editors](#terminal-text-editors)<br>
  1.1 [Nano](#nano)<br>
  1.2 [VIM](#vim)<br>
2. [General/Useful Utilities](#generaluseful-utilities)<br>
  2.1 [Downloading Files with Wget](#downloading-files-with-wget)<br>
  2.2 [Transferring Files From Your Host: SCP using SSH](#transferring-files-from-your-host-scp-using-ssh)<br>
  2.3 [Serving Files From Your Host - WEB](#serving-files-from-your-host---web)<br>
3. [Processes 101](#processes-101)<br>
  3.1 [Managing Processes](#managing-processes)<br>
  3.2 [How do Processes Start?](#how-do-processes-start)<br>
  3.3 [Getting Processes/Services to Start on Boot](#getting-processesservices-to-start-on-boot)<br>
  3.4 [An Introduction to Backgrounding and Foregrounding in Linux](#an-introduction-to-backgrounding-and-foregrounding-in-linux)<br>
  3.5 [Foregrounding a process](#foregrounding-a-process)<br>
4. [Maintaining Your System: Automation](#maintaining-your-system-automation)<br>
5. [Maintaining Your System: Package Management](#maintaining-your-system-package-management)<br>
  5.1 [Introducing Packages and Software Repos](#introducing-packages-and-software-repos)<br>
  5.2 [Managing Your Repositories - Adding and Removing](#managing-your-repositories---adding-and-removing)<br>
6. [Maintaining Your System: Logs](#maintaining-your-system-logs)<br>

&nbsp;

---
# Terminal Text Editors

&nbsp;

Instead of using `echo` and `>` to write files, Linux offers **terminal text editors** that make editing multi-line files much easier.  

&nbsp;

## Nano

- Beginner-Friendly
- to open or create a file use `nano file_name`

<img width="552" height="104" alt="image" src="https://github.com/user-attachments/assets/7c2aa149-407e-430c-be56-2943aee9130b" />

Navigate with arrow keys and add new lines with Enter.

Common shortcuts (press Ctrl + key):

- ^**X** - Exit
- ^**O** - Save (Write Out)
- ^**K** - Cut text
- ^**U** - Paste text
- ^**W** - Search

You can type multiple lines of text, search, copy/paste, or jump to specific lines easily.

Nano has a few features that are easy to remember & covers the most general things you would want out of a text editor, including:
- Searching for text
- Copying and Pasting
- Jumping to a line number
- Finding out what line number you are on

To exit press ctrl and **X**.

&nbsp;

## VIM

Powerful but with a steeper learning curve.
Benefits:
- Customizable keyboard shortcuts.
- Syntax highlighting (great for coding).
- Available on almost all Linux terminals.
- Useful for software development or managing complex files.

&nbsp;

---  
><details><summary>❓Edit "task3" located in "tryhacke"'s home directory using Nano. What is the flag?</summary>THM{****_*******}</details>
<!-- THM{TEXT_EDITORS} -->
---

# General/Useful Utilities

&nbsp;

Linux has some essential tools to **download, transfer, and serve files**. 

## Downloading Files with Wget

- fundamental feature of computing is the ability to transfer files.
- `wget`allows us to download files from the web via HTTP (as if you were accessing the file in your browser).
- example: `wget https://assets.tryhackme.com/additional/linux-fundamentals/part3/myfile.txt`

&nbsp;

## Transferring Files From Your Host: SCP using SSH

- **SCP** = **Secure Copy using SSH** (encrypted transfer).
- it allows you to transfer files between two computers using the SSH protocol to provide both authentication and encryption.

- example of copying from local to remote: `scp localfile username@remoteIP:/remote/path/filename`
  - Variable: The IP address of the remote system: 192.168.1.30
  - Variable: User on the remote system: ubuntu
  - Variable: Name of the file on the local system: important.txt
  - Variable: Name that we wish to store the file as on the remote system: transferred.txt

- example of copying from remote to local: `scp username@remoteIP:/remote/path/filename localfile`

&nbsp;

## Serving Files From Your Host - WEB

Ubuntu machines come pre-packaged with python3. Python helpfully provides a lightweight and easy-to-use module called "HTTPServer". This module turns your computer into a quick and easy web server that you can use to serve your own files, where they can then be downloaded by another computing using commands such as `curl` and `wget`. 


Python3's "HTTPServer" will serve the files in the directory where you run the command, but this can be changed by providing options that can be found within the manual pages. Simply, all we need to do is run `python3 -m  http.server` in the terminal to start the module! In the snippet below, we are serving from a directory called "webserver", which has a single named "file".

<img width="540" height="97" alt="image" src="https://github.com/user-attachments/assets/68b3d905-8c87-45c1-a78c-42946a96af3b" />

Use `wget` to download the fine using the MACHINE_IP and the name of the file.
Remember, because the python3 server is running port 8000, you will need to specify this within your wget command: `wget http://MACHINE_IP:8000/myfile`

Now open a new terminal to use `wget`and leave that one you have started the Python3 web server it. Once you start the Python3 web server, it will run in that terminal until you cancel it.

Example:
<img width="1121" height="309" alt="image" src="https://github.com/user-attachments/assets/afbc562f-4472-4516-9ef7-bf74b3bf106e" />


<img width="837" height="817" alt="14de6e0470d50317f3b24f4f9aa9297a" src="https://github.com/user-attachments/assets/4d77b474-e373-4875-9171-f39e8e380718" />

&nbsp;

---  
><details><summary>❓Download the file http://MACHINE_IP:8000/.flag.txt onto the TryHackMe AttackBox. Remember, you will need to do this in a new terminal. What are the contents?</summary>THM{W***_W********}</details>
<!-- THM{WGET_WEBSERVER} -->
---

&nbsp;

# Processes 101

&nbsp;

A **process** is simply a program that is currently running on your system.
- Each process is managed by the **Linux kernel**.
- Every process has a **PID (Process ID)**.
- PIDs increase as new processes start.
- e.g. the 60th process started would have **PID 60**

&nbsp;

## Viewing Processes

Command `ps` shows running processes in your current session.
Information shown may include PID, CPU usage, status, command being run.

<img width="308" height="193" alt="ps1" src="https://github.com/user-attachments/assets/02761d7c-9143-416c-9c90-f8b136248013" />

Command `ps aux` shows all processes on the system, including processes from other users or system processes.

<img width="679" height="158" alt="ps2" src="https://github.com/user-attachments/assets/1615df3c-9588-40e4-89b0-8af362899729" />

_Note we can see a total of 5 processes -- note how we now have "root"  and "cmnatic"_

Command `top` shows real-time process information.
It shopws live CPU usage, memory usage or updating process list.

<img width="719" height="239" alt="top1" src="https://github.com/user-attachments/assets/0889fdea-6b2f-40bf-8aa4-55ab06788851" />

&nbsp;

## Managing Processes

To stop process use `kill` command, e.g. `kill PID` --> `kill 1337`

- Some of the signals that we can send to a process when it is killed:

|Signal	|Purpose|
|----------|----------------------------------|
|SIGTERM|	Stop process safely (allows cleanup)|
|SIGKILL|	Force kill immediately|
|SIGSTOP|	Pause/suspend process|

&nbsp;

## How do Processes Start?

Linux ses namespaces to organize system resources.

Think of namespaces like slices of a cake where each slice gets CPU, RAM, and resources. Processes are isolated from others.

The **First Process** when Linux boots has an **ID** of **0**. This process is the system's **init** on Ubuntu (e.g. **systemd**) which manages system services and processes.

Programs you start become child processes of systemd. This means that it is controlled by systemd, but will run as its own process (although sharing the resources from systemd) to make it easier for us to identify and the likes. 

<img width="919" height="62" alt="process1" src="https://github.com/user-attachments/assets/ea6bc9d8-a8d6-49ea-a7f5-9bd5ae00bd81" />

&nbsp;

## Getting Processes/Services to Start on Boot

Many services (like web servers or databases) start automatically when the system boots.
Linux manages these with `systemctl`.
Syntax: `systemctl [option] [service]`

Examples:
 - to start a service: `systemctl start apache2` <br>
 - to stop a service: `systemctl stop apache2`<br>

Common options:

|Command	|Purpose|
|---------|---------------|
|start	|Start service|
|stop	|Stop service|
|enable	|Start on system boot|
|disable	|Prevent starting on boot|
|status	|Check service status|

&nbsp;

## An Introduction to Backgrounding and Foregrounding in Linux

Processes can run in two modes: foreground and background.
**Foreground** runs directly in your terminal - You must wait for it to finish before running another command.
**Background** runs without blocking your terminal.
Add `&` to run a command in background: `command &`
e.g.: `echo "Hi THM" &` insteadd of output them, you'll see the process ID.

<img width="382" height="137" alt="bg1" src="https://github.com/user-attachments/assets/d5e10d59-667b-40d3-8004-70fa2b415f70" />

This script will keep on repeating "This will keep on looping until I stop!" until I stop or suspend the process. By using Ctrl + Z (as indicated by T^Z). Now our terminal is no longer filled up with messages -- until we foreground it.

<img width="548" height="353" alt="bg2" src="https://github.com/user-attachments/assets/8ef9103b-7bb0-4bde-bba1-e1ccb7540303" />

&nbsp;

## Foregrounding a process

Now that we have a process running in the background, for example, our script "background.sh" which can be confirmed by using the ps aux command, we can back-pedal and bring this process back to the foreground to interact with.

<img width="1160" height="114" alt="bg3" src="https://github.com/user-attachments/assets/fdb7f207-f370-44c4-b005-869acec760b6" />

With our process backgrounded using either Ctrl + Z or the & operator, we can use fg to bring this back to focus:

<img width="333" height="106" alt="bg4" src="https://github.com/user-attachments/assets/42636efa-8f07-4111-9c6f-b76fd46afe5f" />

&nbsp;

---  
><details><summary>❓If we were to launch a process where the previous ID was "300", what would the ID of this new process be?</summary>301</details>
---  
><details><summary>❓If we wanted to cleanly kill a process, what signal would we send it?</summary>SIGTERM</details>
---  
><details><summary>❓Locate the process that is running on the deployed instance (MACHINE_IP). What flag is given?</summary>THM{*********}</details>
<!-- THM{PROCESSES} -->
---  
><details><summary>❓What command would we use to stop the service "myservice"?</summary>systemctl stop myservice</details>
---  
><details><summary>❓What comman would we use to start the same service on the boot=up of the system?</summary>systemctl enable myservice</details>
---  
><details><summary>❓What command would we use to bring a previously backgrounded process back to the foreground</summary>fg</details>
---

&nbsp;

# Maintaining Your System: Automation

&nbsp;

Sometimes you want tasks to **run automatically** on your system.  
Examples: Running scripts, backing up files, starting applications, performing maintenance tasks, ...
Linux can automate these tasks using **cron jobs**.

**Cron** is a background service that runs scheduled tasks automatically.
It starts when the system boots and keeps running in the background.
These scheduled tasks are called **cron jobs**.

`crontab` is the tool used to **create and manage cron jobs**.
It allows users to schedule commands or scripts to run **at specific times or intervals**.
Example tasks you might automate: Daily backups, system updates, running scripts every hour, cleaning temporary files

>[!NOTE]
> **Cron** is the service that runs scheduled tasks  
> **Crontab** is the tool used to configure those tasks
>

<img width="718" height="494" alt="cron1" src="https://github.com/user-attachments/assets/5ecbbeb3-5b8d-4b72-a8ab-d04c9299f006" />

A crontab is simply a special file with formatting that is recognised by the cron process to execute each line step-by-step. 
Crontabs require 6 specific values:

|.|Value	|Description|
|--|-----|-----------------------------|
|1.|MIN	|What minute to execute at|
|2.|HOUR|What hour to execute at|
|3.|DOM	|What day of the month to execute at|
|4.|MON	|What month of the year to execute at|
|5.|DOW	|What day of the week to execute at|
|6.|CMD	|The actual command that will be executed|

example:"To backup "cmnatic"'s Documents every 12 hours use: `0 */12 * * * cp -R /home/cmnatic/Documents /var/backups/`

Crontabs support a **wildcard symbol (`*`)**.
The `*` means **"any value"** for that time field.
This is useful when you **don't care about a specific time unit**, but still want the task to run regularly.

Example idea: Run a task **every 12 hours** where you don't care about the exact day, month, or year, hence use `*` for those fields (0 */12 * * * command).

To edit crontab use `crontab -e`, then select an editor to edit your crontab.

<img width="749" height="533" alt="cron3" src="https://github.com/user-attachments/assets/5cbc8593-e6e3-45cd-9d3d-e7cf635ba586" />

For more info check [Crontab Generator](https://crontab-generator.org/) and [crontab guru](https://crontab.guru/)

&nbsp;

---  
><details><summary>❓When will the crontab on the deployed instanece (MACHINE_IP) run?</summary>@reboot</details>
---

&nbsp;

# Maintaining Your System: Package Management

&nbsp;

## Introducing Packages and Software Repos

In Linux, software is usually installed as **packages**.

Developers can submit their software to **APT repositories** (software libraries).  
If approved, the program becomes available for users to install easily.

This system highlights two important Linux principles: **Accessibility** – software is easy to install and manage and **Open source** – many tools are developed and shared by the community.

<img width="895" height="374" alt="apt2" src="https://github.com/user-attachments/assets/f9c03e60-d7b2-48cb-95f7-c6d5ed1c2f28" />

Additional repositories can be added by using the add-apt-repositorycommand or by listing another provider.

&nbsp;

## Managing Your Repositories - Adding and Removing

Linux uses **APT (Advanced Package Tool)** to install, update, and remove software packages.

The `apt` command is part of the **APT package management system**, which helps you install software, update software, remove software, manage software repositories.

Sometimes software is **not included in the default Ubuntu repositories**.
In that case, you can **add a new repository manually** by using `add-apt-repository` command.
Whilst you can install software through the use of package installers such as `dpkg`, the benefits of `apt` means that whenever we update our system -- the repository that contains the pieces of software that we add also gets checked for updates. 

Exercise:
1. Download the GPG key and use apt-key to trust it: `wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add - `
2. Now that the key was added to our trusted list, we can now add Sublime Text 3's repository to our apt sources list. A good practice is to have a separate file for every different community/3rd party repository that we add.
3. Create a file named sublime-text.list in /etc/apt/sources.list.d and enter the repository information:
<img width="680" height="102" alt="sources1" src="https://github.com/user-attachments/assets/b6e90dab-efcc-4fc6-a1d1-6589e20baa0f" />
4. use Nano or other text editor to add and save the Sublime Text 3 repo into this newly created file:
<img width="609" height="93" alt="sources2" src="https://github.com/user-attachments/assets/5cef5bac-e853-4d25-b9d1-b4adb7234c7d" />
5. Update apt to recogniye this new entry by using the `apt update` command.
6. Once successfully updated, install the software using `apt install sublime-text`

**Removing packages** is done by using the `add-apt-repository --remove ppa:PPA_Name/ppa` command or by manually deleting the file that we previously added to. <br>
Once removed, we can just use `apt remove [software-name-here]` i.e. `apt remove sublime-text`

<!-- NO QUESTIONS -->

&nbsp;

# Maintaining Your System: Logs

&nbsp;

Linux systems store **log files** to record activity from the operating system, applications, and services.
Most log files are located in **/var/log**

Linux automatically manages many log files using a process called **log rotation**.
Log rotation archives old logs, creates new log files and prevents logs from growing too large.

Some services create their own logs inside `/var/log`.
- Apache2 Web Server: Records web traffic and server activity.
- Fail2Ban: Monitors login attempts and blocks suspicious activity such as **brute-force attacks**.
- UFW Firewall: Tracks firewall activity and blocked connections.

<img width="730" height="528" alt="log1" src="https://github.com/user-attachments/assets/de50bc0f-6fac-42eb-91df-f7490147f1d9" />

Web servers commonly generate two important log types:

| Log Type | Purpose |
|--------|--------|
| **access.log** | Records every request made to the web server |
| **error.log** | Stores server errors and issues |

These logs help administrators diagnose performance problems, investigate security incidents or monitor website traffic.

<img width="843" height="217" alt="log2" src="https://github.com/user-attachments/assets/ea4e1998-8052-4570-9d74-f61bc87cab91" />

Some logs track **system-level activity**, including system events, service activity, user actions, login and authentication attempts.

These logs are essential for **system monitoring and security investigations**.

&nbsp;

Look for the apache2 logs on the deployable Linux machine.

---  
><details><summary>❓What is the IP address of the user who visited the site?</summary>10.9.232.111</details>
---  
><details><summary>❓What file did they access?</summary>catsanddogs.jpg</details>
---

&nbsp;
