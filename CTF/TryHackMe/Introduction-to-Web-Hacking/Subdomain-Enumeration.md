---
layout: default
title: Subdomain Enumeration
description: Learn the various ways of discovering subdomains to expand your attack surface of a target.
---

🔗 [Link to the Room](https://tryhackme.com/room/subdomainenumeration)

## 🏷️ Table of Contents

1. [Brief](#brief)<br>
2. [OSINT: SSL and TLS Certificates](#osint-ssl-and-tls-certificates)<br>
3. [OSINT: Search Engines](#osint-search-engines)<br>
4. [DNS Bruteforce](#dns-bruteforce)<br>
5. [OSINT: Sublist3r](#osint-sublister)<br>
6. [Virtual Hosts](#virtual-hosts)<br>

---

# 📚 Study Notes 

# Brief

Subdomain enumeration means finding additional subdomains of a website. 
This helps us discover more parts of the site that could potentially have security weaknesses.

This is time for you to start the Machine :)

&nbsp;

---  
><details><summary>❓What is a subdomain enumeration method beginning with B?</summary>Brute Force</details>
---  
><details><summary>❓What is a subdomain enumeration method beginning with O?</summary>OSINT</details>
---  
><details><summary>❓What is a subdomain enumeration method beginning with V?</summary>Virtual Host</details>
---

# OSINT: SSL and TLS Certificates

When an SSL/TLS certificate is created for a domain by a **Certificate Authority (CA)**, it is recorded in **Certificate Transparency (CT)** logs.
CT logs are **public records of issued certificates**. Their main purpose is to detect malicious or mistakenly issued certificates.
For security researchers, these logs are useful because they can reveal subdomains linked to a domain.

Useful tool is [crt](https://crt.sh) - a searchable database of SSL/TLS certificates (both current and historical).
Certificates often include subdomains, which allows us to discover additional parts of a website.

To get an answer go to crt.sh > search tryhackme.com > find the certificate logged on 2020‑12‑26 > Identify the subdomain listed in that certificate.

&nbsp;

---  
><details><summary>❓What domain was logged on crt.sh at 2020-12-26?</summary>store.tryhackme.com</details>
---

# OSINT: Search Engines

Search engines index billions of websites, which makes them useful for finding subdomains.

You can use advanced search operators to filter results and reveal subdomains.

Example Google search: `site:*.domain.com -site:www.domain.com` 
What it does:
- `site:*.domain.com` - shows results from all subdomains of domain.com
- `site:www.domain.com` - excludes the main www subdomain
This helps reveal other _subdomains belonging to the same domain_.

### Practice example:

Search on Google: `site:*.tryhackme.com -site:www.tryhackme.com`
This query should reveal a subdomain of tryhackme.com that you can use to answer the task question.

&nbsp;

---  
><details><summary>❓What is the TryHackMe subdomain beginning with S discovered using the above Google search?</summary>store.tryhackme.com</details>
---

# DNS Bruteforce

Bruteforce DNS enumeration means trying many possible subdomain names from a wordlist of common subdomains.
This method can involve hundreds or even millions of requests, so it is usually done with automated tools.

Common tool is dnsrecon, which is used to automate subdomain discovery by testing names from a wordlist.

How it works: The tool uses a list of common subdomain names. It sends DNS requests to check if those subdomains exist. Any valid responses reveal real subdomains.

Your task:
1. Open the static site using **View Site**.
2. Click **Run DNSrecon Request** to start the simulation.
3. Use the discovered subdomain to answer the question.

&nbsp;

---  
><details><summary>❓What is the first subdomain found with the dnsrecon tool?</summary>api.acmeitsupport.thm</details>
---

# OSINT: Sublist3r

To speed up OSINT subdomain discovery, we can automate different discovery techniques using specialized tools.

Common tool is [Sublist3r](https://www.kali.org/tools/sublist3r/) which automates subdomain enumeration using multiple OSINT sources (search engines, certificates, and other public data).
This makes the process faster and more efficient than doing everything manually.

Practice task:
1. Click View Site to open the static site.
2. Run the Sublist3r simulation.
3. Identify the new subdomain it discovers to answer the question.

&nbsp;

---  
><details><summary>❓What is the first subdomain discovered by sublist3r?</summary>web55.acmeitsupport.thm</details>
---

# Virtual Hosts

Some subdomains are not publicly listed in DNS. These may include things like development sites or admin panels. 
Instead, they might only exist in **Private DNS servers** or the **hosts file** on a developer's machine (Linux: `/etc/hosts` or Windows: `c:\windows\system32\drivers\etc\hosts`)

A web server can host multiple websites on the same server.
It determines which website to show by checking the Host header in the HTTP request.

By modifying the Host header, we can sometimes discover hidden websites (virtual hosts).

Similar to DNS brute forcing, we can test many possible subdomains from a wordlist and watch how the server responds.

Start the AttackBox and then try the following command against the Acme IT Support machine to discover a new subdomain.
`ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://MACHINE_IP`
- `-w` specifies the wordlist
- `-H` modifies the Host header
- `FUZZ` placeholder where wordlist entries are inserted
- `-u` target URL

Because every request may return a response, we filter out common results using page size filtering.
`ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://MACHINE_IP -fs {size}`
- `-fs` filters responses by size, ignoring results with the specified page size.

The above command should have revealed two positive results that we haven't come across before.

&nbsp;

---  
><details><summary>❓What is the first subdomain discovered?</summary>delta</details>
---
><details><summary>❓What is the second subdomain discovered?</summary>yellow</details>
---
