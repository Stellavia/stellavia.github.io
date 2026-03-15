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

<!-- ⚠️ add image ⚠️ -->

Sometimes websites are built using pre-made frameworks, and developers forget to replace the default favicon that comes with them. If that happens, the icon can reveal which framework or technology the website is using.

Security testers can compare the favicon with known icons in databases like the OWASP [favicon database](https://wiki.owasp.org/index.php/OWASP_favicon_database) to identify the technology behind the website. Once the framework is known, it becomes easier to research possible weaknesses or common vulnerabilities related to it.

## Practical Exercise:

1. Open the site `https://static-labs.tryhackme.cloud/sites/favicon/` in **Firefox** on the **AttackBox** and **check the browser tab icon**, which shows the website’s favicon, indicating the site is using one.

Viewing the page source you'll see line six contains a link to the images/favicon.ico file. 

<!-- ⚠️ add image ⚠️ -->

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
# OSINT: Wappalyzer
# OSINT: Wayback Machine
# OSINT: GitHub
# OSINT: S3 Buckets
# Automated Discovery


