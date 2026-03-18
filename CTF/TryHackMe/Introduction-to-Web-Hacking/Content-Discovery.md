---
layout: default
title: Content Discovery
description: Learn the various ways of discovering hidden or private content on a webserver that could lead to new vulnerabilities.
---

🔗 [Link to the Room](https://tryhackme.com/room/contentdiscovery)

## 🏷️ Table of Contents

1. [What is Content Discovery?](#what-is-content-discovery)<br>
2. [Manual Discovery: Robots.txt](#manual-discovery-robotstxt)<br>
3. [Manual Discovery: Favicon](#manual-discovery-favicon)<br>
  3.1 [Practical Exercise](#practical-exercise)<br>
4. [Manual Discovery: Sitemap.xml](#manual-discovery-sitemapxml)<br>
5. [Manual Discovery: HTTP Headers](#manual-discovery-http-headers)<br>
6. [Manual Discovery: Framework Stack](#manual-discovery-framework-stack)<br>
7. [Manual Discovery: Google Hacking and Dorking](#manual-discovery-google-hacking-and-dorking)<br>
8. [OSINT: Wappalyzer](#osint-wappalyzer)<br>
9. [OSINT: Wayback Machine](#osint-wayback-machine)<br>
10. [OSINT: GitHub](#osint-github)<br>
11. [OSINT: S3 Buckets](#osint-s3-buckets)<br>
12. [Automated Discovery](#automated-discovery)<br>
  12.1 [What is Automated Discovery?](#what-is-automated-discovery)<br>
  12.2 [What are wordlists?](#what-are-wordlists)<br>
  12.3 [Automation Tools](#automation-tools)<br>

--- 

# 📚 Study Notes 

# What is Content Discovery?

Content discovery means looking for hidden or less obvious parts of a website, such as old pages, backup files, or staff-only sections that regular visitors don’t normally see. 
These can be found by checking the site manually, using automated tools, or gathering information from publicly available sources (OSINT).

Now it is time to start your AttackBox :)

&nbsp;

---  
><details><summary>❓What is the Content Discovery method that begins with M?</summary>Manually</details>
---  
><details><summary>❓What is the Content Discovery method that begins with A?</summary>Automated</details>
---  
><details><summary>❓What is the Content Discovery method that begins with O?</summary>OSINT</details>
---

# Manual Discovery: Robots.txt

One simple place to check when looking for hidden website content is the robots.txt file. This file tells search engines which parts of a website they should not index or show in search results.

Website owners often use it to hide areas such as admin panels, private customer files, or other sensitive pages from search engines. While this doesn’t actually secure those pages, it can accidentally reveal useful locations to someone testing the website.

For beginners in security testing, the robots.txt file can act like a small map of pages the website owner prefers people not to find.

Look at the robots.txt file on the Acme IT Support website: open Firefox on the AttackBox and check the URL: `http://MACHINE_IP/robots.txt` 

&nbsp;

---  
><details><summary>❓What is the directory in the robots.txt that isn't allowed to be viewed by web crawlers?</summary>/staff-portal</details>
---

# Manual Discovery: Favicon

A favicon is the small icon you see in a browser tab next to a website’s name.

![image](https://github.com/user-attachments/assets/600cebc6-6f05-4324-b453-52092584ffc3)

Sometimes websites are built using pre-made frameworks, and developers forget to replace the default favicon that comes with them. If that happens, the icon can reveal which framework or technology the website is using.

Security testers can compare the favicon with known icons in databases like the OWASP [favicon database](https://wiki.owasp.org/index.php/OWASP_favicon_database) to identify the technology behind the website. Once the framework is known, it becomes easier to research possible weaknesses or common vulnerabilities related to it.

## Practical Exercise:

1. Open the site `https://static-labs.tryhackme.cloud/sites/favicon/` in **Firefox** on the **AttackBox** and **check the browser tab icon**, which shows the website’s favicon, indicating the site is using one.

Viewing the page source you'll see line six contains a link to the images/favicon.ico file. 

![image](https://github.com/user-attachments/assets/3ab716bb-4df4-406e-8796-81f08aa4fd29)

2. To download the favicon and get its md5 hash value run: `curl https://static-labs.tryhackme.cloud/sites/favicon/images/favicon.ico | md5sum`
_Note: This curl will fail on the AttackBox if you are a free user, in which case you should use a VM for this. If your hash ends with 427e then your curl failed, and you may need to try it again._

To run it on Windows in Powershell run:
PS C:\> `curl https://static-labs.tryhackme.cloud/sites/favicon/images/favicon.ico -UseBasicParsing -o favicon.ico`
PS C:\> `Get-FileHash .\favicon.ico -Algorithm MD5`

&nbsp;

---  
><details><summary>❓What framework did the favicon belong to?</summary>cgiirc</details>
---

# Manual Discovery: Sitemap.xml

The sitemap.xml file lists pages that a website owner wants search engines to index. 
It can sometimes reveal hard-to-find pages or old parts of the website that are still accessible.

Look at the sitemap.xml file on the Acme IT Support website by opening `http://MACHINE_IP/sitemap.xml` in the FireFox browser on your AttackBox.

&nbsp;

---  
><details><summary>❓What is the path of the secret area that can be found in the sitemap.xml file?</summary>/s3cr3t-area</details>
---

# Manual Discovery: HTTP Headers

When you visit a website, the server sends HTTP headers with its response. 
These headers can reveal useful information such as the web server software and programming language being used (for example, NGINX or PHP). 
This information can help security testers check if the website is running outdated or vulnerable software.

To find headers run `curl http://MACHINE_IP -v`, where `-v` switch will enable verbose mode, which will output info you want. 
You will find something very interesting ;)

&nbsp;

---  
><details><summary>❓What is the flag value from the X-FLAG header?</summary>THM{H*****_F***}</details>
<!-- THM{HEADER_FLAG} -->
---

# Manual Discovery: Framework Stack

After identifying the framework a website uses (for example from the favicon or page source), you can research it on its official website. This can help you learn how the framework works and where hidden files or features might exist, which can lead to discovering more website content.

When checking the page source of the Acme IT Support website, you can find a comment that includes a link to the framework’s website. By reading the framework’s documentation, you can discover the location of the admin panel, which can then be accessed on the target website.

&nbsp;

---  
><details><summary>❓What is the flag from the framework's administration portal?</summary>THM{C*****_D*****_C**********}</details>
<!-- THM{CHANGE_DEFAULT_CREDENTIALS} -->
---

# Manual Discovery: Google Hacking and Dorking

**OSINT (Open-Source Intelligence)** tools are publicly available resources that help gather information about a target website.

**Google Hacking** (also called Google Dorking) is a technique that uses advanced search operators in Google Search to find specific information on the internet.

Instead of a normal search, you use special filters (operators) to narrow down results and locate hidden or sensitive information such as admin pages, documents, or login portals.

## Common Google Dork Operators

| Operator | Example | What it Does |
|----------|---------|--------------|
| `site:` | `site:tryhackme.com` | Shows results only from a specific website |
| `inurl:` | `inurl:admin` | Finds pages that contain the word in the URL |
| `filetype:` | `filetype:pdf` | Finds specific file types |
| `intitle:` | `intitle:admin` | Finds pages that contain the word in the page title |

More info about google hacking is [here](https://en.wikipedia.org/wiki/Google_hacking)

&nbsp;

---  
><details><summary>❓What Google dork operator can be used to only show results from a particular site?</summary>site:</details>
---

# OSINT: Wappalyzer

[Wappalyzer](https://www.wappalyzer.com/) is an online tool and browser extension that helps identify what technologies a website uses, such as frameworks, Content Management Systems (CMS), payment processors and much more, and it can even find version numbers as well.

&nbsp;

---  
><details><summary>❓What online tool can be used to identify what technologies a website is running?</summary>Wappalyzer</details>
---

# OSINT: Wayback Machine

The [Wayback Machine](https://archive.org/web/) is a historical archive of websites that dates back to the late 90s. You can search a domain name, and it will show you all the times the service scraped the web page and saved the contents. This service can help uncover old pages that may still be active on the current website.

&nbsp;

---  
><details><summary>❓What is the website address for the Wayback Machine?</summary>https://archive.org/web/</details>
---

# OSINT: GitHub

**Git** is a tool that tracks changes in project files. It helps teams work together by keeping a history of edits and showing who changed what.

Basic workflow:
- A user edits files on their computer
- They **commit** the changes with a short message
- They **push** the changes to a central storage called a **repository**
- Other users **pull** the changes to update their own copies

**GitHub** is a website that hosts Git repositories online.

Repositories can be:
- **Public** - anyone can see them
- **Private** - only authorized users can access them

&nbsp;

---  
><details><summary>❓What is Git?</summary>version control system</details>
---

# OSINT: S3 Buckets

**S3 Buckets** (AWS Storage) are a cloud storage service provided by **Amazon AWS**.  
They allow users to store files, images, backups, or even static websites that can be accessed through **HTTP or HTTPS**.

The owner of an S3 bucket can set different permissions:
- **Public** - anyone can access the files
- **Private** - only authorized users can access them
- **Writable** - users may be able to upload files

Sometimes these permissions are **misconfigured**, which can accidentally expose files that should not be public.

S3 Buckets format: http(s)://{bucket-name}.s3.amazonaws.com
- name is decided by the owner, e.g. http://tryhackme-assets.s3.amazonaws.com/

&nbsp;

---  
><details><summary>❓What URL format do Amazon S3 buckets end in?</summary>.s3.amazonaws.com</details>
---

# Automated Discovery

## What is Automated Discovery?

**Automated discovery** means using tools to find hidden files and directories on a website instead of searching manually.

These tools send **many requests** (sometimes thousands or millions) to a web server to check if certain files or folders exist.  
This can reveal **content that wasn’t publicly linked or visible before**.

## What are wordlists?

**Wordlists** are text files that contain many commonly used words.

In web discovery, wordlists usually contain **common file names and directory names** that tools test on a website.

Example uses of wordlists:
- **Password cracking** - list of common passwords
- **Web discovery** - list of common directories and files

An excellent resource for wordlists that is preinstalled on the THM AttackBox is [this](https://github.com/danielmiessler/SecLists) which Daniel Miessler curates.

## Automation Tools

- there are many different content discovery tools available, but in this room are mentioned only three (which are also preinstalled on this attack box) - ffuf, dirb and gobuster.

- on the AttackBox execute these commands:
1. **ffuf**: `user@machine$ ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -u http://MACHINE_IP/FUZZ`
2. **dirb**: `user@machine$ dirb http://MACHINE_IP/ /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt`
3. **Gobuster**: `user@machine$ gobuster dir --url http://MACHINE_IP/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt`

&nbsp;

---  
><details><summary>❓What is the name of the directory beginning "/mo...." that was discovered?</summary>/monthly</details>
---  
><details><summary>❓What is the name of the log file that was discovered?</summary>/development.log</details>
---
