---
layout: default
title: Authentication Bypass
description: Learn how to defeat logins and other authentication mechanisms to allow you access to unpermitted areas.
---

🔗 [Link to the Room](https://tryhackme.com/room/authenticationbypass)

## 🏷️ Table of Contents

1. [Username Enumeration](#username-enumeration)<br>
2. [Brute Force](#brute-force)<br>
3. [Logic Flaw](#logic-flaw)<br>
  3.1 [Logic Flaw Practical](#logic-flaw-practical)<br>
4. [Cookie Tampering](#cookie-tampering)<br>
  4.1 [Plain Text Cookies](#plain-text-cookies)<br>
  4.2 [Hashing](#hashing)<br>
  4.3 [Encoding](#encoding)<br>

--- 

# 📚 Study Notes 

# Username Enumeration

Username enumeration is the process of discovering valid usernames on a system. This can help when testing authentication mechanisms later.

Websites sometimes reveal useful information through error messages.

Example on the signup page `http://MACHINE_IP/customers/signup`
If you try to register with the username admin, the site returns: _"An account with this username already exists."_
This message confirms that the username is valid.

We can automate this process using [ffuf](https://github.com/ffuf/ffuf) with a wordlist of common usernames:
`user@tryhackme$ ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://MACHINE_IP/customers/signup -mr "username already exists"`
- **-w**  wordlist containing possible usernames
- **-X POST** specifies the POST request method
- **-d**  data sent in the form (FUZZ is replaced with wordlist entries)
- **-H**  adds headers (here we define form data format)
- **-u**  target URL
- **-mr**  matches responses containing specific text that confirms a valid username

Task:
1. Run the command with ffuf.
2. Identify the valid usernames discovered.
3. Save them in a file called `valid_usernames.txt`

&nbsp;

---  
><details><summary>❓What is the username starting with si*** ?</summary>simon</details>
---  
><details><summary>❓What is the username starting with st*** ?</summary>steve</details>
---  
><details><summary>❓What is the username starting with ro**** ?</summary>robert</details>
---

# Brute Force

After collecting valid usernames (he valid_usernames.txt file), we can attempt a brute force attack on the login page.
**Target**: `http://MACHINE_IP/customers/login`
_Note: If you created your valid_usernames file by piping the output from ffuf directly you may have difficulty with this task. Clean your data, or copy just the names into a new file._

A brute force attack **automatically tries many passwords** against one or more usernames until a valid login is found.

Run the command from the directory where valid_usernames.txt is stored: `user@tryhackme$ ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://MACHINE_IP/customers/login -fc 200`
- **-w** loads two wordlists
- **W1** usernames (valid_usernames.txt)
- **W2** passwords
- **-X POST** uses a POST request
- **-d** sends login data (username + password)
- **-H** sets the form data content type
- **-u** target login page
- **-fc 200** filters responses with status code 200, helping reveal successful logins

If successful, the command will reveal one valid username and password combination.

&nbsp;

---  
><details><summary>❓What is the valid username and password (format: username/password)?</summary>steve/thunder</details>
---

# Logic Flaw

A logic flaw happens when the normal logic of an application can be bypassed or manipulated.
Attackers exploit mistakes in how the application processes requests.
Logic flaws can appear anywhere in a web app, but they are common in authentication systems.

Example code:
<!-- ⚠️ add image ⚠️ -->

This code checks if the URL starts with /admin and then verifies if the user is an admin.

However, because the check requires an exact match, requesting `/adMin` bypasses the check.
The authentication is skipped and the page is shown.

**This is a logic flaw caused by case-sensitive comparison.**

## Logic Flaw Practical

Target: `http://MACHINE_IP/customers/reset`

The reset process normally works like this:
1. Enter the account email.
2. Enter the username.
3. A password reset email is sent to the account owner.

Example valid account: _robert@acmeitsupport.thm_

During the reset request the email is sent in the URL (GET request). The username is sent in the POST request body.

Example request: `user@tryhackme$ curl 'http://MACHINE_IP/customers/reset?email=robert%40acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert'`
The application later uses the PHP variable `$_REQUES` - it contains both GET and POST data.
If the same parameter exists in both, POST values override GET values.

We can add another email parameter in the POST data to control where the reset email is sent.
Example: `user@tryhackme$ curl 'http://MACHINE_IP/customers/reset?email=robert%40acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert&email=attacker@hacker.com'`
Now the reset email will be sent to the attacker’s email instead of Robert’s.

Practical Attack Steps:
1. Create a customer account on the website.
2. You will receive an email in this format: `{username}@customer.acmeitsupport.thm`
3. Run the exploit again but use your new email: `user@tryhackme:~$ curl 'http://MACHINE_IP/customers/reset?email=robert@acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert&email={username}@customer.acmeitsupport.thm'`
4. A support ticket will appear in your account containing a login link for Robert.
5. Log in as _Robert_, view his tickets, and retrieve the flag.

&nbsp;

---  
><details><summary>❓What is the flag from Robert's support ticket?</summary>THM{A***_B*****_C*******}</details>
<!-- THM{AUTH_BYPASS_COMPLETE} --> 
---

# Cookie Tampering

Cookie tampering means **modifying cookies stored in the browser** to change how a website treats your session.

By editing cookies, an attacker may gain:
- Unauthorized access
- Access to another user’s account
- Higher privileges (e.g., admin)


## Plain Text Cookies

Some cookies store values in plain text, making them easy to understand and modify.

Example cookies:
`Set-Cookie: logged_in=true; Max-Age=3600; Path=/`
`Set-Cookie: admin=false; Max-Age=3600; Path=/`

They control `logged_in` (whether the user is logged in) and `admin` (whether the user has admin privileges).
If these values are not protected, an attacker can modify them to gain admin access.

1. **Normal request** - request the target page with `curl http://MACHINE_IP/cookie-test`. You'll get response: _Not Logged In_
2. **Logged in as user** - logged_in cookie set to true and admin set to false: `curl -H "Cookie: logged_in=true; admin=false" http://MACHINE_IP/cookie-test`. You'll get response: _Logged In As A User_
3. **Logged in as admin** - set both the logged_in and admin cookie to true: `curl -H "Cookie: logged_in=true; admin=true" http://MACHINE_IP/cookie-test`. You'll get response: _Logged In As An Admin_

## Hashing

Sometimes cookie values appear as long random strings. These are usually hashes.
A hash is a one‑way transformation of data.

Example hashes of the value 1:

| Method  | Hash Output |
|--------|-------------|
| MD5    | `c4ca4238a0b923820dcc509a6f75849b` |
| SHA-1  | `356a192b7913b04c54574d18c28d46e6395428ab` |
| SHA-256| `6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b` |

Hashes cannot be reversed, but databases like [CrackStation](https://crackstation.net/) store known hashes and their original values, which helps attackers identify them.

## Encoding

Encoding converts data into a format that can be safely transmitted as text.
Unlike hashing, encoding can be reversed.

Common encoding types:
- **Base32** uses A–Z and 2–7
- **Base64** uses a–z, A–Z, 0–9, +, /, and =

Example cookie: Set-Cookie: session=eyJpZCI6MSwiYWRtaW4iOmZhbHNlfQ==

When base64 decoded, it becomes `{"id":1,"admin": false}`
An attacker could modify it to `{"id":1,"admin": true}`

Then re‑encode it to base64, giving themselves admin access. 

&nbsp;

---  
><details><summary>❓What is the flag from changing the plain text cookie values?</summary>THM{C*****_T*******G}</details>
<!-- THM{COOKIE_TAMPERING} -->
---
><details><summary>❓What is the value of the md5 hash 3b2a1053e3270077456a79192070aa78 ?</summary>463729</details>
---
><details><summary>❓What is the base64 decoded value of VEhNe0JBU0U2NF9FTkNPRElOR30= ?</summary>THM{B*****_E*******}</details>
<!-- THM{BASE64_ENCODING} -->
---
><details><summary>❓Encode the following value using base64 {"id":1,"admin":true}</summary>eyJpZCI6MSwiYWRtaW4iOnRydWV9</details>
---
