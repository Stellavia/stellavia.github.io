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


# Introduction

In this task, instead of using the in-browser terminal, we’re connecting to a remote Linux machine using **SSH** (Secure Shell).

## What is SSH?

SSH is a way to remotely connect to another computer securely.
It encrypts everything you type, so data traveling over the internet cannot be easily read by anyone else.
Once the data reaches the remote machine, it is decrypted so the commands can run.

SSH allows you to run commands on a remote device as if you were sitting in front of it.
All communication is encrypted, keeping your data safe over the internet.

SSH is like a secure remote control for a Linux machine.

<img width="1360" height="489" alt="image" src="https://github.com/user-attachments/assets/dd366f7e-36fa-4dce-93cb-8544c7d32987" />

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

<img width="440" height="117" alt="image" src="https://github.com/user-attachments/assets/8da32f6b-04e7-4a57-abfa-105d1dfbd0eb" />
<img width="416" height="116" alt="image" src="https://github.com/user-attachments/assets/5eeb5595-4492-44f9-932a-82083cadbdaa" />

Most commands have a `--help` option, which lists all available flags and what they do: `ls --help`

&nbsp;

## The Manual Page

Every Linux command has a manual page (man page) with full documentation.
Syntax example: `man ls`

<img width="577" height="484" alt="image" src="https://github.com/user-attachments/assets/9dbbba8b-abfa-4073-b1cf-1e002fdb36bd" />

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

<img width="420" height="110" alt="image" src="https://github.com/user-attachments/assets/7dff06bf-ad14-4810-aaa1-50e2b357505d" />

To create a directory (new folder) use `mkdir folder_name`
`mkdir`literally means **make directory**

<img width="403" height="120" alt="image" src="https://github.com/user-attachments/assets/2f38be85-9aa1-41ff-92ac-0211e2eebf37" />

&nbsp;

## Removing Files and Folders

To remove file use `rm file_name`
To remove a folder recursively use option `-R`. Syntax example: `rm -R directory_name`

<img width="436" height="255" alt="image" src="https://github.com/user-attachments/assets/7c57a43b-24f8-4457-a328-5d2d1c7ab32d" />

&nbsp;

## Copying and Moving Files and Folders 

To copy a file use `cp` followed by the name of the file you want to be copied, then followed by the name of the new copy.

<img width="363" height="116" alt="image" src="https://github.com/user-attachments/assets/6e23d655-e1aa-4384-8a93-4e8879083b65" />

To move a file use `mv`.
Note: `mv` will merge or modify the second file that you provide as an argument. You can use `mv` to rename a file (or folder).
You can move files into folders by providing the folder name as the second argument.

Example of renaming the file `note2` to be named `note3`:

<img width="384" height="118" alt="image" src="https://github.com/user-attachments/assets/55991f0a-2343-4192-93a6-6b866ab25740" />

&nbsp;

## Determining File Type

Linux files don’t always rely on extensions.
Use the `file` command to check what a file actually contains:

<img width="442" height="101" alt="image" src="https://github.com/user-attachments/assets/f6e23abd-d3bf-4a76-8ea5-2a1441b4617a" />

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

<img width="490" height="112" alt="image" src="https://github.com/user-attachments/assets/4b260d62-d719-45ef-bc5f-7fdf2fbe3c0e" />

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

<img width="422" height="126" alt="image" src="https://github.com/user-attachments/assets/c7408e97-1f52-42b6-b565-627a646bbfbc" />

To switch to another user and inherit this user's environment use `su -l user_name`
`-l` stands for `--login`

<img width="446" height="130" alt="image" src="https://github.com/user-attachments/assets/7b21654b-0255-473c-a78d-e5f571ec5026" />


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

<img width="603" height="418" alt="image" src="https://github.com/user-attachments/assets/95c01e05-5d0c-4e1c-baf0-1499e50a2ba1" />

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

&nbsp;

## etc

System Configuration - **/etc** is root directory is one of the most important root directories on your system. 
- Contains **system files** used by the OS.
- Examples:
  - `sudoers` lists users/groups allowed to run commands as **root**.
  - `passwd` & `shadow` store user passwords in **encrypted** format (SHA512).

<img width="449" height="103" alt="image" src="https://github.com/user-attachments/assets/961e42d9-2d9c-4911-b33a-f5deff1f1dc6" />

&nbsp;

## var

Variable Data - **/var** stores frequently updated data from services and applications.
Log files are written in `/var/log`

<img width="448" height="93" alt="image" src="https://github.com/user-attachments/assets/55a42867-1af3-44e5-b14f-c69ebbef9273" />

&nbsp;

## root

Root User Home - Home directory for the **root** user (superuser).
Different from `/home`, which is for **regular users**.

&nbsp;

## tmp

Short for **temporary**, used for short-lived files.
Cleared automatically on reboot.
Any user can write here, useful for pentesting scripts or temporary storage.

<img width="441" height="103" alt="image" src="https://github.com/user-attachments/assets/81023d15-2453-47e2-92a6-b5cfe72d109e" />

&nbsp;

---  
><details><summary>❓What is the directory path that would we expect logs to be stored in?</summary>/var/log</details>
---  
><details><summary>❓What root directory is similar to how RAM on a computer works?</summary>/tmp</details>
---  
><details><summary>❓Name the home directory of the root user</summary>/root</details>
---

&nbsp;
