---
layout: default
title: Content Discovery
description: Learn the various ways of discovering hidden or private content on a webserver that could lead to new vulnerabilities.
---

🔗 [Link to the Room]()

## 🏷️ Table of Contents

1. [What is Content Discovery?](#)<br>
2. [Manual Discovery: Robots.txt](#)<br>
3. [Manual Discovery: Favicon](#)<br>
4. [Manual Discovery: Sitemap.xml](#)<br>
5. [Manual Discovery: HTTP Headers](#)<br>
6. [Manual Discovery: Framework Stack](#)<br>
7. [Manual Discovery: Google Hacking and Dorking](#)<br>
8. [OSINT: Wappalyzer](#)<br>
9. [OSINT: Wayback Machine](#)<br>
10. [OSINT: GitHub](#)<br>
11. [OSINT: S3 Buckets](#)<br>
12. [Automated Discovery](#)<br>

--- 

# 📚 Study Notes 

# What is Content Discovery?

Content discovery means looking for hidden or less obvious parts of a website, such as old pages, backup files, or staff-only sections that regular visitors don’t normally see. 
These can be found by checking the site manually, using automated tools, or gathering information from publicly available sources (OSINT).

Now it is time to start your AttackBox :)

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

---  
><details><summary>❓What is the directory in the robots.txt that isn't allowed to be viewed by web crawlers?</summary>/staff-portal</details>
---

# Manual Discovery: Favicon

A favicon is the small icon you see in a browser tab next to a website’s name.

<!-- add picture -->

Sometimes websites are built using pre-made frameworks, and developers forget to replace the default favicon that comes with them. If that happens, the icon can reveal which framework or technology the website is using.

Security testers can compare the favicon with known icons in databases like the OWASP [favicon database](https://wiki.owasp.org/index.php/OWASP_favicon_database) to identify the technology behind the website. Once the framework is known, it becomes easier to research possible weaknesses or common vulnerabilities related to it.



# Manual Discovery: Sitemap.xml
# Manual Discovery: HTTP Headers
# Manual Discovery: Framework Stack
# Manual Discovery: Google Hacking and Dorking
# OSINT: Wappalyzer
# OSINT: Wayback Machine
# OSINT: GitHub
# OSINT: S3 Buckets
# Automated Discovery


