---
layout: default
title: Network Services
description: Learn about, then enumerate and exploit a variety of network services and misconfigurations.
---

🔗 [Link to the Room](https://tryhackme.com/room/networkservices)

## 🏷️ Table of Contents

1. [Understanding SMB](#Understanding-SMB)<br>
2. [Enumerating SMB](#Enumerating-SMB)<br>
3. [Exploiting SMB](#Exploiting-SMB)<br>
4. [Understanding Telnet](#Understanding-Telnet)<br>
5. [Enumerating Telnet](#Enumerating-telnet)<br>
6. [Exploiting Telnet](#Exploiting-Telnet)<br>
7. [Understanding FTP](#Understanding-ftp)<br>
8. [Enumerating FTP](#Enumerating-ftp)<br>
9. [Exploiting FTP](#Exploiting-ftp)<br>

---

# 📚 Study Notes 


## Understanding SMB

- SMB is a network protocol that allows clients to access and share resources like files, printers, and other services over a network.
-  It works on a client-server model, where servers host resources and clients connect to use them. 
- SMB enables these actions remotely, making network resources feel like local ones.

- SMB operates as a request–response protocol: clients send requests and servers respond. 
- Connections typically use TCP/IP (NetBIOS over TCP/IP), NetBEUI, or IPX/SPX. 
- Once connected, clients can access shared folders, read and write files, and use printers or other shared services.

![image](https://github.com/user-attachments/assets/d4ffcd30-5f54-4542-964e-5e10ac0150c3)


- Supported by Windows since Windows 95.
- Samba provides SMB support for Linux/Unix systems.
- Often targeted in network attacks due to misconfigurations or weak permissions.
  
---  
><details><summary>❓What does SMB stand for?</summary>Server Message Block</details>
---  
><details><summary>❓What type of protocol is SMB?</summary>response-request</details>
---  
><details><summary>❓What protocol suite do clients use to connect to the server?</summary>TCP/IP</details>
---  
><details><summary>❓What systems does Samba run on?</summary>Unix</details>
---

## Enumerating SMB

- SMB enumeration is the process of collecting information from SMB services on a target machine to find useful data and possible attack paths. 
- It’s an important early step in security testing because it helps avoid using the wrong exploits and saves time. 
- Good enumeration often reveals users, shares, and system details.

**First, I recommend you to start the target machine and wait for it to fully boot.**

- Enumeration begins with a port scan to discover open services and system details. 
- If SMB is available, you can then check for shared folders and exposed data. 
- Tools like Enum4linux automate SMB enumeration and pull useful information from both Windows and Linux systems.

- **Enumeration** = information gathering before attempting exploitation.
- SMB shares can contain surprisingly sensitive files.
- Port scanning comes first (to find out as much info as you can about the services, apps, OS, ...), SMB probing comes after.
- **Enum4linux** is a go-to tool for quick SMB info gathering.

The syntax of Enum4Linux: `enum4linux [options] ip`

|TAG|FUNCTION|
|:---:|---------------------------------|
|-U|get userlist|
|-M|get machine list|
|-N|get namelist dump (different from -U and-M)|
|-S|get sharelist|
|-P|get password policy information|
|-G|get group and member list|
|-a|all of the above (full basic enumeration)|

&nbsp;

>[!TIP]
>Enum4linux runs multiple Samba-based checks in one command, which makes it great for fast lab work.
>Using the -a option performs a full basic enumeration in one go — perfect for a first sweep.
>

&nbsp;

---  
><details><summary>❓Conduct an nmap scan of your choosing, How many ports are open?</summary>3</details>
---  
><details><summary>❓What ports is SMB running on? Provide the ports in ascending order.</summary>139/445</details>
---  
><details><summary>❓Let's get started with Enum4Linux, conduct a full basic enumeration. For starters, what is the workgroup name?</summary>WORKGROUP</details>
---  
><details><summary>❓What comes up as the name of the machine?</summary>POLOSMB</details>
---  
><details><summary>❓What operating system version is running?</summary>6.1</details>
---  
><details><summary>❓What share sticks out as something we might want to investigate?</summary>profiles</details>
---

## Exploiting SMB

- SMB exploitation can happen through real vulnerabilities, but more often it succeeds because of system misconfigurations. 
- A common example is anonymous SMB share access, where anyone can connect and read files. 
- This kind of access can expose sensitive data and help an attacker move toward getting a shell.

- After enumeration, you identify the SMB share location and any interesting shares worth accessing. 
- Instead of exploiting a software bug, you connect directly if permissions are too open. 
- The SMBClient tool (part of the Samba suite) is used to remotely connect and interact with SMB shares.

&nbsp;

- **Connection syntax**: `smbclient //[IP]/[SHARE] -U [USERNAME] -p [PORT]`
- **Example**: `smbclient //10.10.10.10/secrets -U Anonymous -p 445`

- Once connected, you can browse and download files from the share.

&nbsp;

- SMB attacks are often successful due to misconfiguration, not just CVEs.
- Anonymous share access is a common and dangerous mistake.
- Enumeration tells you which shares exist and which look valuable.
- SMBClient is the standard tool to connect to SMB shares.
- You can run help inside SMBClient to see available commands.

&nbsp;

**Most useful SMBClient commands to remember**: 

|command|what it does|
|--------------|---------------------------------|
|**`ls`** or `dir`|list files and folders|
|**`cd`** [dir]|change directory|
|**`get`** [file]|download a file to your attack machine|

&nbsp;

---  
><details><summary>❓What would be the correct syntax to access an SMB share called "secret" as user "suit" on a machine with the IP 10.10.10.2 on the default port?</summary>smbclient //10.10.10.2/secret -U suit -p 445</details>
---  
><details><summary>❓Great! Now you've got a hang of the syntax, let's have a go at trying to exploit this vulnerability. You have a list of users, the name of the share (smb) and a suspected vulnerability.</summary>No answer needed</details>
---
><details><summary>❓Does the share allow anonymous access? Y/N?</summary>Y</details>
---  
><details><summary>❓Great! Have a look around for any interesting documents that could contain valuable information. Who can we assume this profile folder belongs to?</summary>John Cactus</details>
---  
><details><summary>❓What service has been configured to allow him to work from home?</summary>ssh</details>
---  
><details><summary>❓Okay! Now we know this, what directory on the share should we look in?</summary>.ssh</details>
---  
><details><summary>❓This directory contains authentication keys that allow a user to authenticate themselves on, and then access, a server. Which of these keys is most useful to us?</summary>id_rsa</details>
---  
><details><summary>❓What is the smb.txt flag?</summary>THM{***_**_***_**?}</details>
---

## Understanding Telnet

- Telnet is an application protocol that lets you connect to and run commands on a remote machine using a telnet client. 
- After connecting, your session acts like a virtual terminal on the remote host. 
- It was widely used for remote management but is now mostly outdated.

- A user connects by running the telnet command from a terminal, the telnet client establishes a connection to the telnet server and opens an interactive command session. 
- You can connect using `telnet [ip] [port]`
- Once connected, you can execute commands directly on the remote system through the telnet prompt.

- Telnet provides remote command-line access to another machine.
- It creates a virtual terminal session over the network.
- It does not encrypt traffic, everything is sent in plain text and because of this, it has mostly been replaced by SSH.

>[!CAUTION]
> If you see Telnet enabled during enumeration, it’s often a security red flag.
> Since credentials travel in clear text, Telnet services are risky and commonly targeted in security testing labs.
>

&nbsp;

---  
><details><summary>❓Is Telnet a client-server protocol (Y/N)?</summary>Y</details>
---  
><details><summary>❓What has slowly replaced Telnet?</summary>SSH</details>
---  
><details><summary>❓How wouold you connect to a Telnet server with the IP 10.10.10.3 on port 23?</summary>telnet 10.10.10.3 23</details>
---  
><details><summary>❓The lack of what, means that all Telnet communication is in plaintext?</summary>encryption</details>
---

## Enumerating Telnet

- Telnet enumeration is about carefully checking a target system for exposed Telnet services and related weaknesses. 
- Some vulnerabilities are not obvious at first, so a thorough and methodical approach is important. 
- Proper enumeration helps reveal which services are running and how they might be accessed.

- Begin enumeration with a **port scan** to discover open ports, running services, and system details. 
- Use `Nmap` to scan the target and check whether Telnet is available and listening. 
- The scan results help guide your next investigation steps.
- **Port scanning is the first step in finding Telnet services.**

>[!NOTE]
> Telnet commonly runs on port 23, so pay extra attention if you see it open in scan results.
>Service banners from scans can sometimes reveal useful version or configuration details.
>

&nbsp;

---  
><details><summary>❓How many ports are open on the target machine? Note: you may need to scan non-standard ports too.</summary>1</details>
---  
><details><summary>❓What port is this?</summary>8012</details>
---  
><details><summary>❓This port is unassigned, but still lists the protocol it's using, what protocol is this?</summary>tcp</details>
---  
><details><summary>❓Now re-run the nmap scan, without the -p- tag, how many ports show up as open?</summary>0</details>
---  
><details><summary>❓Based on the title returned to us, what do we think this port could be used for?</summary>a backdoor</details>
---  
><details><summary>❓Who could it belong to? Gathering possible usernames is an important step in enumeration.</summary>Skidy</details>
---

## Exploiting Telnet


- Telnet exploitation usually works because the protocol is insecure by design and often badly configured. 
- It sends all data in plain text and typically has weak access controls. 
- While official vulnerabilities (CVEs) exist, real-world attacks more often succeed due to misconfiguration or exposed backdoor services.

- After enumeration, you may discover a Telnet service running — sometimes even labeled as a backdoor — along with possible usernames. 
- You can try connecting directly to the Telnet port using the telnet client. 
- If access is gained, this foothold can be used to launch a reverse shell, where the target machine connects back to your attacker machine to provide command execution.

- **Connection syntax**: `telnet [ip] [port]`

- A **reverse shell** means the target system opens a connection back to your listening machine, giving you remote command control.

- **Telnet is insecure**: no encryption + weak access control.
- Exploits are often based on misconfiguration, not just software bugs.
- **CVEs** are public records of known **security flaws with ID** numbers.
- Enumeration may reveal clues like service labels or usernames.

![image](https://github.com/user-attachments/assets/b5be0f1f-2eac-4598-bcd8-55159db9f152)

- **Telnet access can be used as a step toward getting a reverse shell.**

> [!TIP]
> Useful CVE search resources:
> https://www.cvedetails.com/
> https://cve.mitre.org/
>
> Always check both known CVEs and configuration mistakes — labs often rely more on misconfigurations than patched vulnerabilities.
>

&nbsp;

---  
><details><summary>❓Great! It's and open telnet connection! What welcome message do we receive?</summary>SKIDY'S BACKDOOR.</details>
---  
><details><summary>❓Let's try to executing some commands, do we get a return on any input we enter into the telnet session? (Y/N)</summary>N</details>
---  
><details><summary>❓Now, use the command "ping [local THM IP\ -c 1" through the telnet session to see if we're able to execute system commands. Do we receive any pings? Note, you need to preface this with .RUN (Y/N)</summary>Y</details>
---  
><details><summary>❓What word does the generated payload start with?</summary>mkfifo</details>
---  
><details><summary>❓What would the command look like for the listening port we selected in our payload?</summary>nc -lvnp 4444</details>
---  
><details><summary>❓Success! What is the contents of flag.txt?</summary>THM{***_***_***_******_****}</details>
---

## Understanding FTP

- **FTP (File Transfer Protocol)** is a network protocol used to transfer files between a client and a server. 
- It follows a client–server model and is designed to move files efficiently across a network. 
- FTP is widely supported but has security weaknesses when used without encryption.

- FTP uses two separate channels during a session. 
- The command (control) channel sends commands and server replies, while the data channel handles the actual file transfer. 
- The client connects first, the server validates login credentials, and then the session opens so commands and file transfers can happen.

- FTP can work in two modes:
    - In **Active mode**, the client opens a port and the server connects back to it. 
    - In **Passive mode**, the server opens a port and the client connects to the server — this is more firewall-friendly.

- FTP is used for **remote file transfer**.
- It uses **two** channels: one **for commands**, one **for data**.
- Login is required before file operations begin.
- Supports **Active** and **Passive** connection modes.
- Not secure by default — data is usually **unencrypted**.

>[!IMPORTANT]
> Default FTP port is usually 21 for commands. <br>
> Because standard FTP is not encrypted, many systems now prefer SFTP or FTPS for secure file transfers. <br>
> The official FTP specification is documented in RFC 959 by the IETF. <br>

---  
><details><summary>❓What communications model does FTP use?</summary>client-server</details>
---  
><details><summary>❓What's the standard FTP port?</summary>21</details>
---  
><details><summary>❓How many modes of FTP connection are there?</summary>2</details>
---

## Enumerating FTP

- **FTP enumeration** is the **process of checking an FTP service for accessible files**, weak settings, and login options. 
- A common finding is **anonymous FTP login**, which can expose useful data. 
- This method is frequently used in CTFs and also reflects real-world misconfigured FTP servers.

- Begin with an `Nmap` port scan to confirm that FTP is running and reachable. 
- Then use an FTP client to attempt login — especially anonymous access — and explore available files and directories for useful information.

- Most Linux systems already include an **FTP client** and you can test by typing `ftp` in the terminal. 
- If it’s missing, install it with your package manager.

- **Enumeration still starts with a port scan**.
- Try anonymous FTP login — it’s a common misconfiguration.
- FTP clients are usually preinstalled on Kali/Parrot/Linux.
- Use tool `--help` or `man` tool when you forget command syntax.
- Accessible files may contain clues for gaining a shell.

- Some older FTP servers respond differently to the cwd command depending on whether a user home directory exists — even before login.
- This can be abused to discover valid usernames on legacy systems.
- Documented example: **Exploit‑DB entry 20745**.

---  
><details><summary>❓How many ports are open on the target machine?</summary>3</details>
---  
><details><summary>❓What port is ftp running on?</summary>21</details>
---  
><details><summary>❓What variant of FTP is running on it?</summary>vsftpd</details>
---  
><details><summary>❓What is the name of the file in the anonymous FTP directory?</summary>PUBLIC_NOTICE.txt</details>
---  
><details><summary>❓What do we think a possible username could be?</summary>mike</details>
---

## Exploiting FTP

- FTP exploitation is often possible because FTP traffic is not encrypted, meaning usernames and passwords travel in plain text.
- This makes interception attacks possible and weak credentials especially dangerous. 
- In many labs and real cases, the main weakness is default or weak passwords on FTP accounts.

- After enumeration confirms an FTP server and possibly a valid username, the next step is to try password attacks. 
- A common method is brute forcing with a password list. 
- The Hydra tool is frequently used for this because it supports many protocols and performs fast dictionary attacks against login services like FTP.

- **Example command** structure: `hydra -t 4 -l [user] -P [wordlist] -vV [target-ip] ftp`

- Hydra will try many password combinations against the FTP service until a valid login is found.

- FTP sends commands and data in plain text.
- Credentials can be stolen with man-in-the-middle attacks.
- **Weak or default passwords are a common entry point**.
- Brute force attacks are often used when a username is known.
- **Hydra** is a popular **tool for password attacks on FTP** and other services.

- Important Hydra options to remember:
  
|option|usage|
|:--:|---------------------------------------|
|**-l**|single username|
|**-P**|password wordlist|
|**-t**|parallel attempts (speed control)|
|**-vV**|very verbose output|

- Hydra supports 50+ protocols — useful far beyond just FTP.
  
---  
><details><summary>❓What is the password for the user "mike"?</summary>password</details>
---  
><details><summary>❓What is ftp.txt?</summary>THM{***_***_***_***_****}</details>
---  
