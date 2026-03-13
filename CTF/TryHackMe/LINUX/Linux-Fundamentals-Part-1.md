---
layout: default
title: Linux Fundamentals Part 1
description: Embark on the journey of learning the fundamentals of Linux. Learn to run some of the first essential commands on an interactive terminal
---

🔗 [Link to the Room](https://tryhackme.com/room/linuxfundamentalspart1)

## 🏷️ Table of Contents

1. [A Bit of Background on Linux](#a-bit-of-background-on-linux)<br>
  1.1 [Where is Linux Used?](#where-is-linux-used)<br>
  1.2 [Flavours of Linux](#flavours-of-linux)<br>
3. [Interacting With Your First Linux Machine (In-Browser)](#interacting-with-your-first-linux-machine-in-browser)<br>
4. [Running Your First few Commands](#running-your-first-few-commands)<br>
5. [Interacting with the Filesystem!](#interacting-with-the-filesystem)<br>
  5.1 [Listing Files in Our Current Directory with ls](#listing-files-in-our-current-directory-with-ls)<br>
  5.2 [Changing Our Current Directory with cd](#changing-our-current-directory-with-cd)<br>
  5.3 [Outputting the Contents of a File with cat](#outputting-the-contents-of-a-file-with-cat)<br>
  5.4 [Finding out the full Path to our Current Working Directory with pwd](#finding-out-the-full-path-to-our-current-working-directory-with-pwd)<br>
6. [Searching for files](#searching-for-files)<br>
  6.1 [Using Find](#using-find)<br>
  6.2 [Using Grep](#using-grep)<br>
  6.3 [Searching Recursively with grep](#searching-recursively-with-grep)<br>
7. [An Introduction to Shell Operators](#an-introduction-to-shell-operators)<br>


# 📚 Study Notes 

# A Bit of Background on Linux
  
&nbsp;

## Where is Linux Used?

- websites
- car entertainment/control panels
- Point of Sale (PoS) systems - checkout tills, registers, ...
- critical infrastructures - traffic light controllers, industrial sensors, ...

&nbsp;

## Flavours of Linux

The name "Linux" is actually an umbrella term for multiple OS's that are based on **UNIX** (another operating system). 
For example, **Ubuntu** & **Debian** are distributions of Linux.
You can also run Ubuntu as a server (such as websites & web applications) or as a fully-fledged desktop.
Note: Ubuntu Server can run on systems with only 512MB of RAM!

Similar to how you have different versions Windows (7, 8 and 10), there are many different versions/distributions of Linux.

&nbsp;

---  
><details><summary>❓Research: What year was the first release of Linux operating system?</summary>1991</details>
---

&nbsp;

# Interacting With Your First Linux Machine (In-Browser)

&nbsp;

This room has a Ubuntu Linux machine that you can interact with all within your browser.

Now is your turn to deploy your first Linux machine.

<!-- NO QUESTIONS --> 

&nbsp;

# Running Your First few Commands

&nbsp;

In Linux there is often no **GUI** (Graphical User Interface) or what is also known as a desktop environment
Most of the time we interact with these systems using **terminal**, which is **text-based**.

At first you need to learn basics such as how to navigate to these files, output their contents or create some new ones.

|Command|Description|
|--------|-----------------------------------------------------|
|echo|Ouptut any text that you provide|
|whoami|Find out what user you're currently logged in as|


To echo **single word** use `echo word`
To echo **string** (sentence) enclose it in double quotes: `echo "More words"
Example:

![image](https://github.com/user-attachments/assets/88608c78-3a43-4b32-8d8e-20c3afbdd5bf)


&nbsp;

---  
><details><summary>❓If we wanted to output the text "TryHackMe", what would our command be?</summary>echo TryHackMe</details>
---  
><details><summary>❓What is the username of who you're logged in as on your deployed Linux machine?</summary>tryhackme</details>
---

&nbsp;

# Interacting with the Filesystem!

&nbsp;


|Command|Full Name|
|-----|------------------------|
|ls|listing|
|cd|change directory|
|cat|concentrate|
|pwd|print working directory|

&nbsp;

## Listing Files in Our Current Directory with ls

![image](https://github.com/user-attachments/assets/3b457ede-2786-4d33-8f0c-56cc9429be70)


Tip: You can list the contents of a directory without having to navigate to it by using ls and the name of the directory. For example `ls Pictures`

&nbsp;

## Changing Our Current Directory with cd

cd is short for change directory, hence to open directory (folder) use `cd Your_Directory`
Then to check its content use `ls` again:

![image](https://github.com/user-attachments/assets/a80b128e-6805-4f38-ac4e-50c091f982aa)

In this case, there are 4 pictures of dogs.

&nbsp;

## Outputting the Contents of a File with cat

`Cat` is short for concatenating and it's a fantastic way for us to output the contents of files (not just text files).

![image](https://github.com/user-attachments/assets/aa9fbafe-ff7d-4edb-9939-15c1ee85ecef)


Tip: You can use `cat` to output the contents of a file within directories without having to navigate to it by using cat and the name of the directory. For example: `cat /home/ubuntu/Documents/todo.txt`

&nbsp;

## Finding out the full Path to our Current Working Directory with pwd

`pwd` stands for **print working directory**.

![image](https://github.com/user-attachments/assets/667ae920-d099-4054-aaa1-6820dbd6d0ad)


&nbsp;

---  
><details><summary>❓On the Linux machine that you deploy, how many folders are there?</summary>4</details>
---  
><details><summary>❓Which directory contains a file?</summary>folder4</details>
---  
><details><summary>❓What is the contents of this file?</summary>Hello World!</details>
---  
><details><summary>❓Use the cd command to navigate to this file and find out the new current working directory. What is the path?</summary>/home/tryhackme/folder4</details>
---

&nbsp;

# Searching for files

&nbsp;

You can use `find` let you quickly search the whole system instead of manually navigating with `cd` and `ls`.

&nbsp;

## Using Find

`find` in Linux is used to quickly search for files inside folders and subfolders; you can **search by an exact filename** (e.g., `find -name passwords.txt`) or by a **pattern** like all .txt files using a **wildcard** (`find -name "*.txt"`).


Using `find` to find a file with the name of passwords.txt:

![image](https://github.com/user-attachments/assets/5f632c07-6a69-4996-b0f6-8258f7ec9eae)

Using `find` to find any file with the extension of .txt:

![image](https://github.com/user-attachments/assets/ed506125-6b00-4fac-b6ad-f128b2e6c576)

&nbsp;

## Using Grep

`grep` in Linux **searches inside files for specific text** and shows the lines where it appears (e.g., `grep "81.143.211.90" access.log` finds all log entries containing that IP address).

Using `wc` to count the number of entries in access.log:

![image](https://github.com/user-attachments/assets/ef9473ba-0634-441d-8e1c-5342ff5304dd)


Using `grep` to find any entries with the IP address of "81.143.211.90" in access.log:

![image](https://github.com/user-attachments/assets/410c9b12-e220-4eb8-bd5b-2b4a3bd11b72)


`Grep` has searched through this file and has shown you any entries of what you've provided and that is contained within this log file for the IP.

&nbsp;

## Searching Recursively with grep

Using `grep -R` lets you search for specific text **across all files and subfolders in a directory**, showing where the text appears and which file contains it.

**-R** means recursive option.

Example:
To search for a variable across all files in the current directory and its subfolders: `grep -R "PRETTY_NAME" /etc/`
Example Output:
![image](https://github.com/user-attachments/assets/565442eb-4168-4d7e-b06a-21ffb20eabab)

The file path is shown before the matching line, making it easy to identify where the result was found.

&nbsp;

---  
><details><summary>❓Use grep on "access.log" to find the flag that has a prefix of "THM". What is the flag? Note: The "access.log" file is located in "/home/tryhackme/" directory.</summary>THM{******}</details>
<!-- THM{ACCESS} -->
---

&nbsp;

# An Introduction to Shell Operators

&nbsp;

**Shell operators** in Linux let you control how commands run: 
  - `&` runs a command in the background
  - `&&` runs the next command only if the first succeeds
  - `>` sends command output to a file (overwriting it)
  - `>>` adds output to the end of a file without deleting existing content

&nbsp;

---  
><details><summary>❓If we wanted to run a command in the background, what operator would we want to use?</summary>&</details>
---    
><details><summary>❓If we wanted to replace the contents of a file named "passwords" with the word "password123", what would my command be?</summary>echo password123 > passwords</details>
---  
><details><summary>❓Now if we wanted to add "tryhackme" to this file named "passwords" but also keep "passwords123", what would my command be</summary>echo tryhackme >> passwords</details>
---

&nbsp;
