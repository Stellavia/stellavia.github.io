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

<img width="332" height="101" alt="image" src="https://github.com/user-attachments/assets/83cf6891-6944-48fe-857f-34af98eeed90" />

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

<img width="412" height="61" alt="image" src="https://github.com/user-attachments/assets/21131768-02a4-4b09-b1c7-3ea40879ffa0" />

Tip: You can list the contents of a directory without having to navigate to it by using ls and the name of the directory. For example `ls Pictures`

&nbsp;

## Changing Our Current Directory with cd

cd is short for change directory, hence to open directory (folder) use `cd Your_Directory`
Then to check its content use `ls` again:

<img width="547" height="64" alt="image" src="https://github.com/user-attachments/assets/b82420cb-1cca-4f57-9560-311363690c66" />

In this case, there are 4 pictures of dogs.

&nbsp;

## Outputting the Contents of a File with cat

`Cat` is short for concatenating and it's a fantastic way for us to output the contents of files (not just text files).

<img width="392" height="103" alt="image" src="https://github.com/user-attachments/assets/dd6343e8-05f2-4504-8976-dab501f9d9a8" />

Tip: You can use `cat` to output the contents of a file within directories without having to navigate to it by using cat and the name of the directory. For example: `cat /home/ubuntu/Documents/todo.txt`

&nbsp;

## Finding out the full Path to our Current Working Directory with pwd

`pwd` stands for **print working directory**.

<img width="291" height="81" alt="image" src="https://github.com/user-attachments/assets/71c83c96-6d7f-49d8-a7bb-469104dd8405" />

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

<img width="381" height="82" alt="image" src="https://github.com/user-attachments/assets/dbb36a6a-3711-4b8a-932b-9ad96cce35bb" />

Using `find` to find any file with the extension of .txt:

<img width="326" height="100" alt="image" src="https://github.com/user-attachments/assets/dc76a130-f68d-4dd1-9e0c-fe0f1fc60fce" />

&nbsp;

## Using Grep

`grep` in Linux **searches inside files for specific text** and shows the lines where it appears (e.g., `grep "81.143.211.90" access.log` finds all log entries containing that IP address).

Using `wc` to count the number of entries in access.log:

<img width="320" height="83" alt="image" src="https://github.com/user-attachments/assets/48ca15e3-232b-4500-85ba-3cb33ba9eb43" />

Using `grep` to find any entries with the IP address of "81.143.211.90" in access.log:

<img width="1184" height="89" alt="image" src="https://github.com/user-attachments/assets/e6db553a-041c-40fb-b749-d70c98e95d3e" />

`Grep` has searched through this file and has shown you any entries of what you've provided and that is contained within this log file for the IP.

&nbsp;

## Searching Recursively with grep

Using `grep -R` lets you search for specific text **across all files and subfolders in a directory**, showing where the text appears and which file contains it.

**-R** means recursive option.

Example:
To search for a variable across all files in the current directory and its subfolders: `grep -R "PRETTY_NAME" /etc/`
Example Output:
<img width="321" height="83" alt="image" src="https://github.com/user-attachments/assets/6f16b2f5-a145-45f5-a63b-5302c9b9392c" />
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
