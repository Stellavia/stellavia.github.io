---
layout: default
title: IDOR
description: Learn how to find and exploit IDOR vulnerabilities in a web application giving you access to data that you shouldn't have.
---

🔗 [Link to the Room](https://tryhackme.com/room/idor)

## 🏷️ Table of Contents

1. [What is an IDOR?](#what-is-an-idor)<br>
2. [An IDOR Example](#an-idor-example)<br>
3. [Finding IDORs in Encoded IDs](#finding-idors-in-encoded-ids)<br>
  3.1 [Useful Tools](#useful-tools)<br>
4. [Finding IDORs in Hashed IDs](#finding-idors-in-hashed-ids)<br>
5. [Finding IDORs in Unpredictable IDs](#finding-idors-in-unpredictable-ids)<br>
6. [Where are IDORs located](#where-are-idors-located)<br>
7. [A Practical IDOR Example](#a-practical-idor-example)<br>

--- 

# 📚 Study Notes 

# What is an IDOR?

IDOR stands for **Insecure Direct Object Reference**.
It is a type of access control vulnerability.

This vulnerability occurs when a web application trusts user input too much when retrieving objects such as files, data, documents

If the server does not properly check permissions, a user may be able to access objects belonging to other users simply by modifying the input (for example, changing an ID in a URL).

In short: **IDOR happens when applications fail to verify that a user is allowed to access a specific resource**.

&nbsp;

---  
><details><summary>❓What does IDOR stand for?</summary>Insecure Direct Object Reference</details>
---

# An IDOR Example

Imagine you signed up for a service and your profile URL is `http://online-service.thm/profile?user_id=1305`
You can see your own profile information.
If you change the user_id to another number `http://online-service.thm/profile?user_id=1000`
and can access another user's data, this is an IDOR vulnerability.
The website should check that the requested user_id belongs to the logged-in user before showing any data.

Task
1. Click the _View Site_ button.
2. If successful, you may receive a flag by exploiting this IDOR vulnerability.

&nbsp;

---  
><details><summary>❓What is the Flag from the IDOR example website?</summary>THM{I***-V***-F****}</details>
<!-- THM{IDOR-VULN-FOUND} -->
---

# Finding IDORs in Encoded IDs

When data is passed between web pages (via POST data, query strings, or cookies), developers often encode the data before sending it.
Encoding converts data into a format the server can easily read, usually using characters like: `a-z`,  `A-Z`, `0-9`, `=`

The most common encoding method on the web is **Base64**.
Base64 strings are often easy to recognize and can usually be decoded, modified, and re‑encoded.

How Attackers Test Encoded Data:
1. Capture the encoded value from a request (URL, cookie, or POST data).
2. Decode it to see the original data.
3. Modify the value (for example change an ID or privilege).
4. Re‑encode it.
5. Send the request again and observe if the response changes.

## Useful Tools

Decode Base64: https://www.base64decode.org/
Encode Base64: https://www.base64encode.org/

&nbsp;

---  
><details><summary>❓What is a common type of encoding used by websites?</summary>base64</details>
---

# Finding IDORs in Hashed IDs

Sometimes websites hash ID values before sending them to the user. This makes them look like random strings instead of simple numbers.

For example, if the ID 123 is hashed using MD5, it becomes `202cb962ac59075b964b07152d234b70`.

Even though hashes look random, they may still follow a **predictable pattern**, such as being the hash of a simple number (1, 2, 3, 4, etc.).
Attackers can try to identify the original value behind the hash.

You can test hashes using online databases that store known hash results.
Useful tool is [crackstation](https://crackstation.net/)  - this service contains billions of known hashes and may reveal the original value if it exists in the database.

&nbsp;

---  
><details><summary>❓What is a common algorithm used for hashing IDs?</summary>md5</details>
---

# Finding IDORs in Unpredictable IDs

Sometimes IDs cannot be decoded or reversed using encoding or hash‑cracking methods.
In these cases, a good way to test for IDOR vulnerabilities is to use multiple accounts.

Testing Method:
1. Create two user accounts.
2. Log in with the first account and observe its ID.
3. Replace that ID with the ID of the second account.
4. Check if you can access the other user's data.

If you can view another user's content while logged in as a different user (or even without being logged in), the application likely has an IDOR vulnerability.

This happens when the server does not verify that the requested data belongs to the logged‑in user. 

&nbsp;

---  
><details><summary>❓What is the minimum number of accounts you need to create to check for IDORs between accounts?</summary>2</details>
---

# Where are IDORs located

The vulnerable endpoint is not always visible in the browser’s address bar. It may appear in other parts of a web application.

Common places to find them:
- AJAX requests loaded by the browser
- JavaScript files
- Hidden or undocumented parameters

Sometimes developers leave unused or hidden parameters in the application that remain accessible in production.
Example endpoint: `/user/details`
This might normally display your own user information using your session.

Through parameter mining, you might discover an extra parameter: `/user/details?user_id=123`

If changing the user_id allows you to view another user's data, the application likely has an IDOR vulnerability. 

# A Practical IDOR Example

Start the machine and open the lab link in a new browser tab: `https://LAB_WEB_URL.p.thmlabs.com`

1. Go to the Customers section.
2. Create an account and log in.
3. Open the Your Account tab.

You will see your username and email already filled in.

Open Developer Tools > Network tab, then refresh the page.
You should see a request to this endpoint: `/api/v1/customer?id={user_id}`

The server returns your user information in JSON format, including user ID, username and email.
This means the application retrieves user data based on the **id** parameter in the URL.

Since the user data depends on the id parameter, try modifying it.

Example: `/api/v1/customer?id=1`, `/api/v1/customer?id=3`
If changing the ID reveals other users’ information, the application is vulnerable to IDOR.

Task
1. Modify the id parameter in the request.
2. Check if you can view other users' data.
3. Use the discovered information to answer the questions.

&nbsp;

---  
><details><summary>❓What is the username for user id 1?</summary>adam84</details>
---
><details><summary>❓What is the email address for user id 3?</summary>j@fakemail.thm</details>
---
