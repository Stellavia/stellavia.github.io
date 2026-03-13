---
layout: default
title: Hardening Basics Part 2
description: Continue learning about hardening
---

🔗 [Link to the Room](https://tryhackme.com/room/hardeningbasicspart2)

## 🏷️ Table of Contents

1. [Chapter 3 Quiz](#chapter-3-quiz)<br>
2. [GNU Privacy Guard](#gnu-privacy-guard)<br>
  2.1 [Pretty Good Privacy (PGP)](#pretty-good-privacy-pgp)<br>
  2.2 [How PGP encryption works (step by step)](#how-pgp-encryption-works-step-by-step)<br>
  2.3 [GNU Privacy Guard (GPG)](#gnu-privacy-guard-gpg)<br>
  2.4 [Creating GPG keys](#creating-gpg-keys)<br>
3. [Encrypting Your Files](#encrypting-your-files)<br>
  3.1 [Symmetric Encryption (password-based)](#symmetric-encryption-password-based)<br>
  3.2 [Decrypting](#decrypting)<br>
  3.3 [Asymmetric Encryption (key-based)](#asymmetric-encryption-key-based)<br>
  3.4 [Sharing public keys](#sharing-public-keys)<br>
  3.5 [Encrypting a file for someone else](#encrypting-a-file-for-someone-else)<br>
  3.6 [Decrypting the file](#decrypting-the-file)<br>
4. [SSH Protocol 1](#ssh-protocol-1)<br>
5. [Creating an SSH Key Set](#creating-an-ssh-key-set)<br>
  5.1 [Generating SSH keys](#generating-ssh-keys)<br>
  5.2 [Copying your public key to a remote server](#copying-your-public-key-to-a-remote-server)<br>
6. [Disable Username and Password SSH Login](#disable-username-and-password-ssh-login)<br>
7. [X11 Forwarding and SSH Tunneling](#x11-forwarding-and-ssh-tunneling)<br>
  7.1 [Turn off X11 Forwarding](#turn-off-x11-forwarding)<br>
  7.2 [SSH Tunneling](#ssh-tunneling)<br>
8. [Improving SSH Logging](#improving-ssh-logging)<br>
9. [Chapter 4: Mandatory Access Control](#chapter-4-mandatory-access-control)<br>
10. [Introduction to AppArmor](#introduction-to-apparmor)<br>
  10.1 [AppArmor Configuration](#apparmor-configuration)<br>
  10.2 [Adding More Profiles](#adding-more-profiles)<br>
11. [AppArmor Command Line Utilities](#apparmor-command-line-utilities)<br>
  11.1 [AppArmor Modes](#apparmor-modes)<br>
  11.2 [AppArmor Command-Line Utilities](#apparmor-command-line-utilities)<br>
12. [Chapter 4 Quiz](#chapter-4-quiz)<br>
13. [Chapter 3: SSH and Encryption](#chapter-3-ssh-and-encryption)<br>

---

# 📚 Study Notes #

>[!NOTE]
>All tasks for this room were completed using Ubuntu 18.04 LTS. That being said, pretty much everything that applies to 18.04 can apply to 20.04 as well. If you take what you learn out of this room and try to apply it in the real world for practice and fun and something does not work, be sure to check the documentation for what you are trying to do.
>

# Chapter 3 Quiz

---
><details><summary>❓Which SSH Protocol version is the most secure?</summary>2</details>
---
><details><summary>❓This is a random, arbitrary number, used as the session key, that is used to encrzpt GPG.</summary>nonce</details>
---
><details><summary>❓Yey/Ney - GPG is based on the OpenPGP standard</summary>Yey</details>
---
><details><summary>❓What is the command to symmetrically encrypt a file with GPG?</summary>gpg -c</details>
---
><details><summary>❓What is the command to asymmetrically encrypt a file with GPG?</summary>gpg -e</details>
---
><details><summary>❓What is the command to create SSH keys?</summary>ssh-keygen</details>
---
><details><summary>❓Where are ssh keys stored in a user's home directory?</summary>.ssh</details>
---
><details><summary>❓What option needs to be set to select the type of key to generate for SSH?</summary>-t</details>
---
><details><summary>❓The SSH configuration options presented in this chapter were found in what file (full path)?</summary>/etc/ssh/sshd_config</details>
---

# GNU Privacy Guard

- To understand GPG, you first need to understand PGP, the system it’s based on.

## Pretty Good Privacy (PGP) 

- PGP is a system used to encrypt and decrypt emails and files.
- It uses both types of encryption: **Asymmetric encryption** (public & private keys) and **Symmetric encryption** (a shared secret key)

## How PGP encryption works (step by step)

1. You write an email
2. A random one-time session key is created (This is also called a nonce)
3. The email is encrypted using this session key
4. The session key itself is encrypted using the recipient’s public key
5. Both the encrypted email and encrypted session key are sent

To decrypt:
1. The recipient uses their private key to unlock the session key
2. The session key is then used to decrypt the message back to readable text

## GNU Privacy Guard (GPG)
- GPG is an open-source implementation of the OpenPGP standard (which PGP follows).
- In simple terms: GPG = the modern, free version of PGP
- It’s widely trusted and heavily tested

- GPG offers:
  - Easy encryption for emails and files
  - Extremely strong security (PGP is considered practically unbreakable)
  - Asymmetric encryption, so you don’t need to share passwords
  - Better overall security compared to password-based encryption
    
- GPG also comes pre-installed on Ubuntu, which makes it very convenient.

## Creating GPG keys

- Before using GPG, you must create a key pair: **Public key** (shared with others) and **Private key** (kept secret)
- You generate keys with `gpg --gen-key`
- What happens next:
  - GPG asks for basic info (name, email, etc.)
  - It generates random data for the keys
  - You may be asked to move your mouse or type to add randomness
- Once finished:
  - GPG creates configuration files in your home directory
  - A `.gnupg` directory is created

- To check that your keys were created successfully use `gpg --list-keys`

# Encrypting Your Files

- GPG lets you encrypt files in two different ways:
  1. Symmetric encryption (one shared secret)
. Asymmetric encryption (public + private keys)


## 1. Symmetric Encryption (password-based)

- Symmetric encryption uses one secret (a passphrase) to both encrypt the file and decrypt the file
- Anyone who knows the passphrase can read the file.

- How it works:
  - You encrypt a file with `gpg -c yourfile.txt`
  - What happens: GPG asks you to enter a passphrase; the file is encrypted into a `.gpg` file, you’ll need the same passphrase to decrypt it

>[!IMPORTANT]
> This passphrase is not your GPG key passphrase
> GPG may leave the original file unencrypted
> You should delete it using rm or shred if it’s sensitive
>

## Decrypting

- To read the file use `gpg -d yourfile.txt.gpg`
- You’ll be prompted for the passphrase, then the contents are displayed.

## 2. Asymmetric Encryption (key-based)

- Asymmetric encryption uses two keys public key (encrypt) and private key (decrypt)

>[!IMPORTANT]
> Public keys can be shared
> Private keys must NEVER be shared
>

## 3. Sharing public keys

- To export a public key use `gpg --export -a -o publickey.asc`
- This creates a text-based (ASCII armored) public key file.
- To import someone else’s public key use `gpg --import publickey.asc`
- Once imported, you can encrypt files for that person.

## 4. Encrypting a file for someone else

Example
- Nick encrypts a file like this: `gpg -e document.txt`
- GPG will ask who the recipient is (based on imported public keys).
- The encrypted file can be sent over email, chat, file sharing, even insecure channels

## 5. Decrypting the file
- When Spooky decrypts: `gpg -d document.txt.gpg`
- He is prompted for his private key passphrase.
- After that, the file is decrypted and readable.

>[!NOTE]
> Symmetric encryption is simple and fast, but requires sharing a secret
> Asymmetric encryption is more secure and scalable
> GPG allows safe file sharing without exchanging passwords
> Private keys are extremely sensitive — protect them at all costs
>

# SSH Protocol 1

- In your `/etc/ssh/sshd_config` file, if you see `Protocol 1` or `Protocol 1, 2`
- you need to disable Protocol version 1 asap.
- It's available to run on Legacy machines but has been compromised and is no longer considered secure. SSH Protocol Version 2 is the current, more secure version of SSH.

# Creating an SSH Key Set

- Logging in with a password is less secure, hence SSH keys are the safest way to log in
- Keys consist of a public key (shared) and a private key (kept secret)

## Generating SSH keys

- as a user run `ssh-keygen`
- Defaults to creating keys in `~/.ssh/`
- Generates both public (`id_rsa.pub`) and private (`id_rsa`) keys

![image](https://github.com/user-attachments/assets/7454448a-d79e-42f2-9d5d-0f6a7e7ad985)


>[!IMPORTANT]
>Public key = shared with the server
>Private key = never share

## Copying your public key to a remote server

- Use `ssh-copy-id` (example: `ssh-copy-id username@remote-host`)
  - this automatically copies your public key to the server
- or do manual copy = on the remote server run:
  `mkdir -p ~/.ssh`
  `cat /home/user/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys`
  `chmod -R go= ~/.ssh`
- this adds your public key to `authorized_keys` and fixes permissions to secure the `.ssh` directory

- By default, ssh-keygen uses RSA 2048 bits.
- You can create stronger keys following NIST recommendations:
  - RSA 3072 bits: `ssh-keygen -t rsa -b 3072`
  - ECDSA 384 bits (smaller, faster, just as strong): `ssh-keygen -t ecdsa -b 384`
- Max ECDSA is 521 bits, but 384 is recommended for security and efficiency.


>[!IMPORTANT]
> SSH keys = safer than passwords
> Always protect your private key
> Share only your public key
> Use modern algorithms (RSA ≥3072 or ECDSA ≥384) for stronger security
> Correct permissions on .ssh and authorized_keys are critical
>

# Disable Username and Password SSH Login

- Verify that your key exchange login works, otherwise you might lock yourself or other users out of the system.
-  To do this, go to the `/etc/ssh/sshd_config` file and edit the line `PasswordAuthentication yes` to `PasswordAuthentication no`
-  Afterwards restart SSH to apply changes by `sudo systemctl restart sshd`

# X11 Forwarding and SSH Tunneling

- You've connected to your workstation that has SSH enabled and you go about your work on the command-line. Everything is going great. But then you run into a problem. You need to run a program that only has a GUI.  How would you accomplish that via SSH?  That's what X11 Forwarding is for. X11 allows you to forward GUI application displays to your local environment (thought it has to have a GUI itself, right?). However, X11 has some flaws that make it dangerous to use. So let's look at turning it off.

## Turn off X11 Forwarding

- To turn off X11 Forwarding you need to change setting in `sshd_config` specifically `X11 Forwarding yes` to `X11 Forwarding no`

## SSH Tunneling

- Lets someone forward network traffic through your SSH connection
- Example: Access a blocked website by tunneling through your home SSH server
- Why block it:
  - Can bypass network restrictions
  - Can be abused to create unauthorized tunnels into your network

- To disable it, in `/etc/ssh/sshd_config`, set these options: 

![image](https://github.com/user-attachments/assets/fd5e7711-2c4d-4ba9-9428-b7df7eea8358)


- Afterwards restart ssh with `sudo systemctl restart sshd`
- This hardens SSH and prevents unauthorized port forwarding or tunneling.

>[!NOTE]
> X11 Forwarding - disable unless you really need GUI over SSH
> SSH Tunneling - disable to prevent unauthorized network access
> Both settings improve SSH security on your server.



# Improving SSH Logging

- A log file is created any time someone logs in with a Protocol that uses SSH.
- By default, Ubuntu stores this log file in /var/log/auth.log

![image](https://github.com/user-attachments/assets/4b6d1491-a42f-4c95-ab41-8702cfbc604e)


- There's a few different levels of logging that you can find in the man pages of `sshd_config`: QUIET, FATAL, ERROR, INFO, VERBOSE, DEBUG1, DEBUG2, DEBUG3
- INFO is the default setting.
- To change the logging level go to `/etc/ssh/sshd_config` and look for the line `#LogLevel INFO` - uncomment that line and change it to any of the available levels.

# Chapter 4: Mandatory Access Control

- MAC is a type of access control that defines who can access what on a system
- Other types of access control include:
  - Discretionary Access Control (DAC)
  - Role-Based Access Control (RBAC)
  - Rule-Based Access Control
- MAC is considered the strongest form of access control because it gives strict control over permissions

- Linux has several ways to implement MAC
- Two popular tools are SELinux and AppArmor


# Introduction to AppArmor

- Preinstalled on Ubuntu → no extra tools needed
- Implements Mandatory Access Control (MAC)
- Protects system data and applications from malicious actors
- Easier to use than SELinux, especially for custom profiles

- Benefits
  - Applications have their own profiles, simplifying management
  - Supports custom profiles with simpler syntax than SELinux
  - Helps protect confidentiality and control access

## AppArmor Configuration

- Profiles directory: `/etc/apparmor.d
- Contains all AppArmor profiles, e.g. `sbin.dhclient` and `usr.* files`
- Abstractions directory acts like an includes folder and contains partial profiles that can be reused in custom profiles

- Rules define who can access what:
  **r** - read
  **w** - write
  **m** - executable mapping (mmap)

- `@{HOME}` variable allows rules to apply to any user’s home directory
- Partial profiles are templates, not full profiles

## Adding More Profiles

- You can install additional profiles with `sudo apt install apparmor-profiles apparmor-profiles-extra`

>[!IMPORTANT]
>AppArmor lets you control program access with reusable, understandable profiles while making custom hardening easier than SELinux.
>


# AppArmor Command Line Utilities

- to check AppArmor status use `aa-status` - it shows whether AppArmor is loaded, the mode, and how many profiles are active.

## AppArmor Modes
- **Enforce** actively enforces the rules in a profile
- **Complain** allows disallowed actions but logs them
- **Audit** enforces rules and logs both allowed and disallowed actions (logs in /var/log/audit/audit.log or system log)
- Modes let you control how strict AppArmor is for different programs.

## AppArmor Command-Line Utilities

- First, install the utilities by `sudo apt install apparmor-utils`
- Then you can use:
  `aa-enforce <profile>` = Switch profile to enforce mode
  `aa-disable <profile>` = Disable a profile
  `aa-audit <profile>` = Switch profile to audit mode
  `aa-complain <profile>` = Switch profile to complain mode

>[!IMPORTANT]
> AppArmor profiles define rules for programs
> Modes control how strict these rules are
> Command-line utilities let you enforce, audit, complain, or disable profiles
> Always check status after changes with aa-status
>


# Chapter 4 Quiz

---
><details><summary>❓Where are the AppArmor profiles located?</summary>/etc/apparmor.d</details>
---
><details><summary>❓This directory includes partial profiles to be used in your own custom profiles</summary>abstractions</details>
---
><details><summary>❓This punctuation mark is REQUIRED at the end of every rule in a profile</summary>.</details>
---
><details><summary>❓This AppArmor mode enforces the profiles but also logs them</summary>audit</details>
---
><details><summary>❓This command checks the status of AppArmor</summary>aa-status</details>
---

# Chapter 3: SSH and Encryption

- Encryption is essential for protecting data
- You can encrypt almost anything: Personal files (documents, photos, videos), work or corporate documents, even entire hard drives
- Knowing how to implement encryption at home or work helps to keep personal data safe from compromise and prevent sensitive work information from being stolen

>[!IMPORTANT]
> Encryption is a core skill for security and privacy.
>
