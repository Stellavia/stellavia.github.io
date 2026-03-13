---
layout: default
title: Operating System Security
description: This room introduces users to operating system security and demonstrates SSH authentication on Linux. 
---

🔗 [Link to the Room](https://tryhackme.com/room/operatingsystemsecurity)

## 🏷️ Tabe of Contents

1. [Intro to Operating System Security](#intro-to-operating-system-security)<br>
2. [Common Examples of OS Security](#common-examples-of-os-security)<br>
3. [Practical Example of OS Security](#practical-example-of-os-security)<br>
  3.1 [Step-by-Step OS Attack Chain](#step-by-step-os-attack-chain)<br>

---

# 📚 Study Notes 

# Intro to Operating System Security 

- An operating system (OS) is what makes a computer or phone usable. Hardware (CPU, memory, keyboard, screen, storage) can’t do anything on its own — the OS controls it and lets apps run.
- Apps like browsers, messaging apps, and email cannot talk directly to hardware. They must go through the operating system, which sets the rules.

- Different devices use different operating systems:
  - Computers: Windows, macOS, Linux
  - Phones: Android, iOS
  - Servers: Windows Server, Linux, Solaris, AIX
  
- Devices store very sensitive data (messages and photos, e-mails, passwords, banking apps, work or school files) and because of this, the operating system must be secured.

- Operating system security protects three key things which are the foundation of OS security:
  - **Confidentiality**: Only authorized people can see your data
  - **Integrity**: Data cannot be changed or tampered with
  - **Availability**: Your system is usable when you need it

&nbsp;

---
><details><summary>❓Which of the following is not an operating system? AIX, Android, Chrome OS, Solaris, Thunderbird</summary>Thunderbird</details>
>✅Solution: It's an e-mail client application.
---

&nbsp;


# Common Examples of OS Security 

- **Operating system security protects confidentiality** (who can see data), **integrity** (data can’t be changed) and **availability** (system is usable)


- Attackers commonly target three weaknesses:

  **1. Weak authentication** (bad passwords) - authentication proves who you are. <br>
      - It can be something you know (password, PIN), something you are (fingerprint), or something you have (phone/SMS) <br>
      - Passwords are attacked the most as many people use easy or reused passwords (e.g., 123456, password, qwerty). <br>
      - Easy passwords let attackers access private accounts and data. <br>
      - ⚠️ Strong, unique passwords are essential. <br>

  **2. Weak file permissions** - systems should follow least privilege: only the right people can access files. <br>
      - Weak permissions allow attackers to read files they shouldn’t (break confidentiality), change files they shouldn’t (break integrity). <br>

  **3. Malicious programs (malware)** - Malware can attack all three security pillars. <br>
      - e.g. trojans can give attackers access to your system and files. <br>
      - e.g. ransomware locks (encrypts) files and demands money to unlock them, attacking availability. <br>

&nbsp;

---
><details><summary>❓Which of the following is a strong password, in your opinion? iloveyou, 1q2w3e4r5t, LearnM00r, qwertyuiop  </summary>LearnM00r</details>
>✅Solution: It uses uppercase, lowercase, numbers, it's not a common dictionary word, not a keyboard pattern and it's harder to guess or brute-force.
---

&nbsp;

# Practical Example of OS Security 

- This example shows how attackers break into a system using weak passwords.
  - An attacker finds a username, tries to guess the password, tries to gain admin/root access (Called root on Linux, Android, macOS, called administrator on Windows as these accounts have full system control)

- In the example, a Linux system is attacked using SSH (remote login with Username: sammie and password: dragon)
  - using this weak password, the attacker successfully logs in.
  - after logging in, the attacker can view files, read sensitive information, look for other users (johnny, linda), try to guess their passwords too and also attempt privilege escalation.

## Step-by-Step OS Attack Chain 

1. **Information discovery**: Attacker finds a username (e.g. sammie); password is exposed on a sticky note (dragon).
2. **Initial access**: Attacker connects remotely using SSH amd uses the guessed password to log in successfully.
3. **User verification**: Runs `whoami` to confirm access as sammie.
4. **System exploration**: Uses `ls` to list files and `cat` to read sensitive documents = gains insight into system usage and stored data.
5. *Credential discovery**: Uses `history` to see past commands and learns about other users on the system (johnny, linda).
6. **Lateral movement**: Tries to access other user accounts. Attempts password guessing using `ssh johnny@MACHINE_IP`
or `su - johnny`
7. **Privilege escalation** (goal): Attempts to gain root/administrator access which means getting full control over the system if successful.

&nbsp;

---
><details><summary>❓Based on the top 7 passwords, let’s try to find Johnny’s password. What is the password for the user johnny?</summary>abc123</details>
>✅Solution: In the task 2 you have list of most common passwords. Check the 7th one.
---
><details><summary>❓Once you are logged in as Johnny, use the command `history` to check the commands that Johnny has typed. We expect Johnny to have mistakenly typed the root password instead of a command. What is the root password?</summary>happyHack!NG</details>
---
><details><summary>❓While logged in as Johnny, use the command `su - root` to switch to the root account. Display the contents of the file flag.txt in the root directory. What is the content of the file?</summary>THM{**********}</details>
>✅Solution: Use command `cat /root/flag.txt`
---

&nbsp;
