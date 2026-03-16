---
layout: default
title: File Inclusion
description: This room introduces file inclusion vulnerabilities, including Local File Inclusion (LFI), Remote File Inclusion (RFI), and directory traversal.
---

🔗 [Link to the Room](https://tryhackme.com/room/fileinc)

## 🏷️ Table of Contents

1. [Introduction](#introduction)<br>
  1.1 [What is File inclusion?](#what-is-file-inclusion)<br>
  1.2 [Why do File inclusion vulnerabilities happen?](#why-do-file-inclusion-vulnerabilities-happen)<br>
  1.3 [What is the risk of File inclusion?](#what-is-the-risk-of-file-inclusion)<br>
2. [Path Traversal](#path-traversal)<br>
3. [Local File Inclusion: LFI](#local-file-inclusion-lfi)<br>
4. [Local File Inclusion: LFI Continued](#local-file-inclusion-lfi-continued)<br>
5. [Local File Inclusion: RFI](#local-file-inclusion-rfi)<br>
6. [Remediation](#remediation)<br>
7. [Challenge](#challenge)<br>

--- 

# 📚 Study Notes 

# Introduction

## What is File inclusion?

File inclusion is a vulnerability that occurs when a web application allows users to load files from the server using input parameters without proper validation.
Applications often use parameters in URLs to retrieve files, such as images, documents, or other content.
Example request: `http://webapp.thm/get.php?file=userCV.pdf`
Here:
- **file** - parameter
- **userCV.pdf** - file requested from the server
The application reads the parameter and loads the specified file.

<!-- ⚠️ add image ⚠️ -->

Common types include of Inclusion:
- **LFI (Local File Inclusion)** - accessing files stored on the same server
- **RFI (Remote File Inclusion)** - loading files from a remote server
- **Directory Traversal** - navigating outside the intended directory to access other files

## Why do File inclusion vulnerabilities happen?

File inclusion vulnerabilities usually occur because user input is not properly validated or sanitized.

If the application trusts user input, attackers may manipulate the parameter to access unintended files.


## What is the risk of File inclusion?

Attackers can use file inclusion vulnerabilities to:
- Read sensitive files (source code, credentials, configuration files)
- Access system files
- Reveal application logic
- Potentially achieve Remote Code Execution (RCE) if they can upload or control included files

Because of this, file inclusion vulnerabilities can lead to serious security breaches. 

<!-- no questions -->

# Path Traversal
# Local File Inclusion: LFI
# Local File Inclusion: LFI Continued
# Local File Inclusion: RFI
# Remediation
# Challenge


&nbsp;

---  
><details><summary>❓</summary></details>
---
