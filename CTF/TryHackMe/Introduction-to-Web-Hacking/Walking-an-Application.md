---
layout: default
title: Walking an Application
description: Manually review a web application for security issues using only your browsers developer tools. Hacking with just your browser, no tools or scripts.
---

🔗 [Link to the Room](https://tryhackme.com/room/walkinganapplication)

## 🏷️ Table of Contents

1. [Walking An Application](#walking-an-application)<br>
2. [Exploring The Website](#exploring-the-website)<br>
3. [Viewing The Page Source](#viewing-the-page-source)<br>
4. [Developer Tools: Inspector](#developer-tools-inspector)<br>
  4.1 [Example: Paywall](#example-paywall)<br>
5. [Developer Tools: Debugger](#developer-tools-debugger)<br>
6. [Developer Tools: Network](#developer-tools-network)<br>

---

# 📚 Study Notes 

&nbsp;

# Walking An Application

In this room you'll learn how to manually review a web application for security issues using built-in browser tools like View Source, Inspector, Debugger, and Network—techniques that often reveal vulnerabilities automated scanners miss.

Don't forget to deploy the VM.

&nbsp;

# Exploring The Website

Our goal is to find interactive parts of a website that could have security issues.  
First step is to explore the site in the browser and note down each page/feature, and write a short summary.  

Acme IT Support - Website Overview (example)

| Feature               | URL                     | What it Does |
|-----------------------|------------------------|--------------|
| Home Page             | /                      | Shows what Acme IT Support does, with a company photo. |
| Latest News           | /news                  | Lists the latest news articles. Each article has a unique link. |
| News Article          | /news/article?id=1     | Shows a single news article. Some are for premium customers only. |
| Contact Page          | /contact               | Form to send a message to the company (name, email, message). |
| Customers             | /customers             | Redirects to the login page. |
| Customer Login        | /customers/login       | Login form with username and password. |
| Customer Signup       | /customers/signup      | Form to create a new account (username, email, password). |
| Reset Password        | /customers/reset       | Form to reset your password using your email. |
| Customer Dashboard    | /customers             | Shows your submitted tickets and a button to create a new ticket. |
| Create Ticket         | /customers/ticket/new  | Form to submit a new IT issue, with optional file upload. |
| Customer Account      | /customers/account     | Allows editing username, email, and password. |
| Customer Logout       | /customers/logout      | Logs you out of the account. |

&nbsp;

# Viewing The Page Source

The page source is the code a website sends to your browser. 
It includes **HTML** (defines the content of the page), **CSS** (controls how the page looks), **JavaScript** (adds interactivity)  
Looking at the page source can help you find useful information, like hidden links or comments left by the developer.

- **How to view it:**  
  1. Right-click on the webpage > "View Page Source"  
  2. Or type `view-source:` before the website URL (e.g. `view-source:https://example.com`)  
  3. Some browsers have it under menus like Developer Tools > View Source  

- **Things to notice:**  
  - **Comments:** `<!-- ... -->` – notes left by developers; not visible on the page.  
  - **Links:** `<a href="...">` – shows where links go.  
  - **Hidden links:** Sometimes there are links to pages not shown on the site; can reveal private sections.  
  - **External files:** CSS, JS, and images are often included in separate files. Sometimes misconfigured directories can reveal extra files.  
  - **Frameworks:** Many sites use frameworks to save development time. Page source may reveal which framework and version is in use, which can hint at potential vulnerabilities.  

Focus on useful clues like hidden links, comments, or outdated frameworks rather than trying to understand every line of code.


Example of contact page link on line 31:
![image](https://github.com/user-attachments/assets/666ca948-0b2d-484e-baa5-9dcd4f5a99fa)

Note: There's a hidden link to a page starting with "secr", view this link to get a flag. 

&nbsp;

---  
><details><summary>❓What is the flag from the HTML comment?</summary>THM{H***_C*******_A**_D********}</details>
<!-- THM{HTML_COMMENTS_ARE_DANGEROUS} -->
---  
><details><summary>❓What is the flag from the secret link?</summary>THM{N**_A_S*****_A******}</details>
<!-- THM{NOT_A_SECRET_ANYMORE} -->
---  
><details><summary>❓What is the directory listing flag?</summary>THM{I******_D********_P**********}</details>
<!-- THM{INVALID_DIRECTORY_PERMISSIONS} -->
---  
><details><summary>❓What is the framework flag?</summary>THM{K***_Y***_S*******_U******}</details>
<!-- THM{KEEP_YOUR_SOFTWARE_UPDATED} -->
---

&nbsp;

# Developer Tools: Inspector

Modern browsers include **Developer Tools**, which help developers and security testers understand how a website works behind the scenes.

Pentesters often use three main tools:
- **Inspector** – View and edit elements on a webpage
- **Debugger** – Analyze and troubleshoot JavaScript code
- **Network** – See requests sent between the browser and the server

The page source doesn't always represent what's shown on a webpage; this is because CSS, JavaScript and user interaction can change the content and style of the page, which means we need a way to view what's been displayed in the browser window at this exact time. For that we have **Inspector**.

The **Inspector** shows what the webpage currently looks like in the browser.

With the Inspector you can:
- View the page structure
- Edit elements temporarily
- Test changes without affecting the real website

## Example: Paywall

Sometimes websites block content using a **paywall** (a message that requires payment to continue).

![image](https://github.com/user-attachments/assets/53cbbdad-6492-45ec-89e2-e114a7269cdf)


Using the Inspector, you can:
1. Right‑click the blocking message
2. Click **Inspect**
3. Find the element that contains the paywall (e.g. `DIV` element with the class `premium-customer-blocker`)
4. Change its display setting (for example from `display: block` to `display: none`)

This hides the blocking element and reveals the content underneath.

![image](https://github.com/user-attachments/assets/43f0741d-87e3-44a5-95ae-fa7061d8d086)

> Changes made in Developer Tools **only affect your browser temporarily**. Refreshing the page restores the original website.

&nbsp;

---  
><details><summary>❓What is the flag behind the paywall?</summary>THM{N**_S*_H*****}</details>
<!-- THM{NOT_SO_HIDDEN} -->
---

&nbsp;

# Developer Tools: Debugger

- The **Debugger** (called **Sources** in Chrome) lets you inspect and analyze JavaScript running on a webpage.
- It shows all files the page uses, including JavaScript stored in folders like `assets`.
- Many JavaScript files are **minified or obfuscated**, making them harder to read. The **Pretty Print `{}`** button reformats the code to improve readability.
- You can set **breakpoints** (pause points in the code) to stop JavaScript from running and observe what happens.

Example: Pausing the script that removed a red popup allowed the hidden content to remain visible.

![image](https://github.com/user-attachments/assets/b4514a11-d18a-43ae-9201-5461eede82ff)

&nbsp;

---  
><details><summary>❓What is the flag behind the paywall?</summary>THM{C***_M*_I*_Y**_C**}</details>
<!-- THM{CATCH_ME_IF_YOU_CAN} -->
---

&nbsp;

# Developer Tools: Network

The **Network tab** shows all requests a webpage sends to the server (files, data, images, etc.).
- Refreshing the page with the Network tab open lets you see everything the site loads.
- Actions like submitting forms also appear here.

Example: Submitting the contact form sends data in the background using **AJAX**, which can be inspected in the Network tab to see where the data was sent.

![image](https://github.com/user-attachments/assets/8b8a5a1b-d072-44ad-8f41-bb8bc6fc3d89)

&nbsp;

---  
><details><summary>❓What is the flag behind the paywall?</summary>THM{G**_A***_F***}</details>
<!-- THM{GOT_AJAX_FLAG} -->
---

