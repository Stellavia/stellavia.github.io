---
layout: default
title: Linux Fundamentas Part 2
description: Continue your learning Linux journey with part two. You will be learning how to log in to a Linux machine using SSH, how to advance your commands, file system interaction
---

🔗 [Link to the Room](https://tryhackme.com/room/linuxfundamentalspart2)

## 🏷️ Table of Contents

1. [Introduction](#introduction)<br>
  1.1 [What is SSH?](#what-is-ssh)<br>
2. [Introduction to Flags and Switches](#introduction-to-flags-and-switches)<br>
  2.1 [The Manual Page](#the-manual-page)<br>
3. [Filesystem Interaction Continued](#filesystem-interaction-continued)<br>
  3.1 [Creating Files and Folders](#creating-files-and-folders)<br>
  3.2 [Removing Files and Folders](#removing-files-and-folders)<br>
  3.3 [Copying and Moving Files and Folders](#copying-and-moving-files-and-folders)<br>
  3.4 [Determining File Type](#determining-file-type)<br>
5. [Permissions 101](#permissions-101)<br>
  5.1 [Briefly: The Differences Between Users and Groups](#briefly-the-differences-between-users-and-groups)<br>
  5.2 [Switching Between Users](#switching-between-users)<br>
  5.3 [Understanding File Permissions in Numeric Format](#understanding-file-permissions-in-numeric-format)<br>
  5.4 [Converting Symbolic Permissions to Numbers](#converting-symbolic-permissions-to-numbers)<br>
  5.5 [More Common Examples](#more-common-examples)<br>
  5.6 [Why This Matters](#why-this-matters)<br>
6. [Common Directories](#common-directories)<br>
  6.1 [etc](#etc)<br>
  6.2 [var](#var)<br>
  6.3 [root](#root)<br>
  6.4 [tmp](#tmp)<br>

---

# Introduction

In this task, instead of using the in-browser terminal, we’re connecting to a remote Linux machine using **SSH** (Secure Shell).

## What is SSH?

SSH is a way to remotely connect to another computer securely.
It encrypts everything you type, so data traveling over the internet cannot be easily read by anyone else.
Once the data reaches the remote machine, it is decrypted so the commands can run.

SSH allows you to run commands on a remote device as if you were sitting in front of it.
All communication is encrypted, keeping your data safe over the internet.

SSH is like a secure remote control for a Linux machine.

![image](https://github.com/user-attachments/assets/7ea4dbf5-f8b7-4cef-9dd6-7090d9d540c3)

It's time for you to deploy your machines on thm.

&nbsp;

# Introduction to Flags and Switches

&nbsp;

Many Linux commands can be **extended with extra options**, called **flags** or **switches**.
Flags are added to commands with a **hyphen** (e.g., `-a`) or **two hyphens** for long options (e.g., `--all`).
Without flags, commands run their default behavior.

Example: `ls` Command
- `ls` lists files in the current folder, hides hidden files by default.
- `ls -a` shows all files, including hidden ones that start with `.` like `.hiddenfolder`.

![image](https://github.com/user-attachments/assets/6769d1e7-716b-4023-948e-2e7c5c8c0443)
![image](https://github.com/user-attachments/assets/8cdd712b-87b4-4aad-bff7-bc7787a038f7)

Most commands have a `--help` option, which lists all available flags and what they do: `ls --help`

&nbsp;

## The Manual Page

Every Linux command has a manual page (man page) with full documentation.
Syntax example: `man ls`

![image](https://github.com/user-attachments/assets/80efe324-11c1-434f-abd1-1e4a6ba947ac)

The man page tells you what the command does, which flags are available and examples of usage.

**Flags and switches let you control how commands behave, and man pages are your built-in guide to understand them.**

&nbsp;

---  
><details><summary>❓What directional arrow key would we use to navigate down the manual page?</summary>down</details>
---  
><details><summary>❓What flag would we use to display the output in a "human-readable" way?</summary>-h</details>
---

&nbsp;

# Filesystem Interaction Continued

&nbsp;

In Linux, you interact with files and folders using simple commands. 
Here’s a quick guide:

| Command | What It Does |
|---------|--------------|
|    `touch`   | Create a **blank file** |
|    `mkdir`   | Create a **new folder** |
|    `rm`      | Remove a file or folder (**use `-R` for folders**) |
|    `cp`      | **Copy** a file or folder |
|    `mv`      | **Move or rename** a file/folder |
|    `file`    | **Check the type** of a file |

&nbsp;

## Creating Files and Folders

To create a file use `touch file_name`
`touch` will create a blank file.
To edit file (add content) use commands like `vi`, `nano` or so.

![image](https://github.com/user-attachments/assets/5c786a57-82c2-47e4-94a2-c7f7557c7a2b)

To create a directory (new folder) use `mkdir folder_name`
`mkdir`literally means **make directory**

![image](https://github.com/user-attachments/assets/5f9ca54b-570f-43e1-92c6-0d80ef8ea578)

&nbsp;

## Removing Files and Folders

To remove file use `rm file_name`
To remove a folder recursively use option `-R`. Syntax example: `rm -R directory_name`

![image](https://github.com/user-attachments/assets/17024b87-b146-49fd-a7b9-cbe047a9861a)

&nbsp;

## Copying and Moving Files and Folders 

To copy a file use `cp` followed by the name of the file you want to be copied, then followed by the name of the new copy.

![image](https://github.com/user-attachments/assets/dfa61f18-0324-4077-a3d1-42dd0faab442)


To move a file use `mv`.
Note: `mv` will merge or modify the second file that you provide as an argument. You can use `mv` to rename a file (or folder).
You can move files into folders by providing the folder name as the second argument.

Example of renaming the file `note2` to be named `note3`:

![image](https://github.com/user-attachments/assets/60ad568a-ab42-4e6e-a980-bfe582bef83e)

&nbsp;

## Determining File Type

Linux files don’t always rely on extensions.
Use the `file` command to check what a file actually contains:

![image](https://github.com/user-attachments/assets/7c13383b-4b5f-45e9-86ee-3145215a6ac3)

&nbsp;

---  
><details><summary>❓How would you create the file named "newnote"?</summary>touch newnote</details>
---  
><details><summary>❓On the deployable machine, what is the file type of "unknown1" in "tryhackme's" home directory?</summary>ASCII text</details>
---  
><details><summary>❓How would we move the file "myfile" to the directory "myfolder"</summary>mv myfile myfolder</details>
---  
><details><summary>❓What are the contents of this file?</summary>THM{**********}</details>
<!-- THM{FILESYSTEM} --> 
---

&nbsp;

# Permissions 101

&nbsp;

In Linux, **files and folders have permissions** that control who can read, write, or execute them. Not all users can access all files, and permissions help keep your system secure.

Use `ls -l` or `ls -lh` to see files and their permissions:

![image](https://github.com/user-attachments/assets/58740329-58e9-499f-9c6f-6fb31817387b)

The first column shows permissions:

**r** = read 
**w** = write 
**x** = execute 

Permissions are grouped into **Owner** | **Group** | **Others**.

&nbsp;

## Briefly: The Differences Between Users and Groups

a real-world context: the system user that runs a web server must have permissions to read and write files for an effective web application. However, companies such as web hosting companies will have to want to allow their customers to upload their own files for their website without being the webserver system user -- compromising the security of every other customer. 

&nbsp;

## Switching Between Users

To switch to another user use `su user_name`

![image](https://github.com/user-attachments/assets/d17efa07-691d-4935-8a7d-581e1b0702bd)

To switch to another user and inherit this user's environment use `su -l user_name`
`-l` stands for `--login`

![image](https://github.com/user-attachments/assets/dbd00fe1-c5e9-4b02-bb50-e1395f7cc718)

&nbsp;
## Understanding File Permissions in Numeric Format

In Linux, every file and directory has a set of permissions that control who can read, write, or execute it. These permissions are often displayed in symbolic format, such as: **rwxrwxrwx**


|Section	| Applies To |	Example |
|---------|------------|----------|
|First 3|Owner|rwx|
|Next 3|Group|rwx|
|Last 3|Others|rwx|

Each letter represents a specific permission:
**r** = read
**w** = write
**x**= execute

&nbsp;
## Converting Symbolic Permissions to Numbers


Each permission has a numeric value:

|Permission |	Value |
|------------|------------|
|Read (r)	|4|
|Write (w)	|2|
|Execute (x)	|1|

To calculate the numeric value, we add the values together for each group.

![image]https://github.com/user-attachments/assets/7e6e5127-1b33-4f1c-9606-89cf6511ee0d)

&nbsp;

## More Common Examples

|Symbolic	|Numeric	|Meaning|
|---------|------------|----------|
|rwxr-xr-x|755|Owner can do everything, others can read and execute|
|rw-r--r--|644|Owner can read/write, others can only read|
|rwx------|700|Only the owner has access|

&nbsp;

## Why This Matters

Many commands like `chmod` use numeric permissions, for example `chmod 750 system_overview.txt`
This means:
  - Owner: full access <br>
  - Group: read + execute <br>
  - Others: no access <br>

&nbsp;

---  
><details><summary>❓On the deployable machine, who is the owner of "important"?</summary>user2</details>
---  
><details><summary>❓What would the command be to swich to the user "user2"</summary>su user2</details>
---  
><details><summary>❓Output the contents of "important", what is the flag?</summary>THM{**_*****}</details>
<!-- THM{SU_USER2} -->
---

&nbsp;

# Common Directories

&nbsp;

Linux has several important root directories that store system, user, and temporary data. 

## etc

System Configuration - **/etc** is root directory is one of the most important root directories on your system. 
- Contains **system files** used by the OS.
- Examples:
  - `sudoers` lists users/groups allowed to run commands as **root**.
  - `passwd` & `shadow` store user passwords in **encrypted** format (SHA512).

![image](https://github.com/user-attachments/assets/68c7aa97-387b-4f51-9fdb-8267cd8b6140)

&nbsp;

## var

Variable Data - **/var** stores frequently updated data from services and applications.
Log files are written in `/var/log`

![image](https://github.com/user-attachments/assets/404b09f4-9da7-4ed9-82be-3f36a8e2c5d3)

&nbsp;

## root

Root User Home - Home directory for the **root** user (superuser).
Different from `/home`, which is for **regular users**.

&nbsp;

## tmp

Short for **temporary**, used for short-lived files.
Cleared automatically on reboot.
Any user can write here, useful for pentesting scripts or temporary storage.

![image](https://github.com/user-attachments/assets/61b0544f-3f57-4bcd-9493-2a03179deea0)

&nbsp;

---  
><details><summary>❓What is the directory path that would we expect logs to be stored in?</summary>/var/log</details>
---  
><details><summary>❓What root directory is similar to how RAM on a computer works?</summary>/tmp</details>
---  
><details><summary>❓Name the home directory of the root user</summary>/root</details>
---

&nbsp;
