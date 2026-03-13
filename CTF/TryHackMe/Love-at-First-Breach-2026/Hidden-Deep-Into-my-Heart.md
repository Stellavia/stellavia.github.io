---
layout: default
title: Hidden Deep Into my Heart
description: Find what's hidden deep inside this website.
---

🔗 [Link to the Room](https://tryhackme.com/room/lafb2026e9)

![image](https://github.com/user-attachments/assets/616a49fd-b09b-43b2-b5bf-5835d3e09333)

## 🏷️Table of Contents

1. [Initial Recon](#initial-recon)<br>
1.1 [Directory Enumeration](#directory-enumeration)<br>
2. [robots.txt Discovery](#robots-txt-discovery)<br>
3. [Manually request the hidden path](#manually-request-the-hidden-path)<br>
4. [I TRIED and I FAILED MANY TIMES](#i-tried-and-i-failed-many-times)<br>
5. [Then I decided to STOP COMPLICATING my life](#then-i-decided-to-stop-complicating-my-life)<br>
6. [Conclusion](#conclusion)<br>

---

# Enumerate a Flask web app, discover hidden paths, gain admin access and retrieve the flag. Eh.

![image](https://github.com/user-attachments/assets/9bc7172e-c5aa-4e04-85d6-4424991bc057)

&nbsp;

# Initial Recon

I started with basic web enumeration against the target service on port 5000.

## Directory Enumeration 

I used GOBUSTER to brute force common directories and files, syntax: `gobuster dir -u http://TARGET_IP:5000/ -w /usr/share/wordlists/dirb/common.txt` 
The outcome:<br>
  /robots (JACKPOT!)<br>
  /console (returned 400 which indicates route exists)

# robots.txt Discovery

Hence, I fetched robots file with command: `curl http://TARGET_IP:5000/robots.txt`
Inside the .txt file I found some data that I understood as a password into the mentioned "hidden" directory (bare with me).

# Manually request the hidden path

... with command: `curl -i http://TARGET_IP:5000/HIDDEN_DIRECTORY_NAME/`

Afterwards server returned the HTML with secret message:

*<title>HIDDEN_DIRECTORY_NAMe</title>
"You've found the secret HIDDEN_DIRECTORY_NAME, but there's more to discover..."*

# I TRIED and I FAILED MANY TIMES

- admin login endpoint with curl POST requests: `curl -s -X POST http://TARGET_IP:5000/SECRET_DIRECTORY_NAME/administrator -d "username=test&password=test" | wc -c`
- tried robots.txt pwd
- tried alternate usernames
- tried SQL injection payloads
- tried auth header tests

# Then I decided to STOP COMPLICATING my life

I went back into the browser, changed the URL, logged into the secret directory and got flag.

---
><details><summary>❓What is the flag?</summary>THM{l***_**_**_***_******_**t}</details>
---

# Conclusion

**Sometimes, it’s best to just stick to the simplest path.**
