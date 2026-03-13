---
layout: default
title: Introductory Networking
description: An introduction to networking theory and basic networking tools.
---

🔗 [Link to the Room](https://tryhackme.com/room/introtonetworking)

## 🏷️ An introduction to networking theory and basic networking tools

1. [The OSI Model: An Overview](#the-osi-model-an-overview) <br>
2. [Encapsulation](#encapsulation) <br>
2.1 [Encapsulation Process (Data Naming)](#encapsulation-process-data-naming)<br>
2.2 [De-encapsulation](#de-encapsulation)<br>
3. [The TCP/IP Model](#the-tcpip-model) <br>
3.1 [TCP/IP vs OSI Model](#tcpip-vs-osi-model)<br>
3.2 [TCP/IP as a Protocol Suite](#tcpip-as-a-protocol-suite)<br>
3.3 [TCP and the Three-Way Handshake](#tcp-and-the-three-way-handshake)<br>
3.4 [History](#history)<br>
4. [Networking Tools: Ping](#networking-tools-ping) <br>
4.1 [How Ping Works](#how-ping-works)<br>
5. [Networking Tools: Traceroute](#networking-tools-traceroute) <br>
5.1 [What Traceroute Does](#what-traceroute-does)<br>
5.2 [Protocol Differences](protocol-differences#)<br>
5.3 [Ping vs Traceroute - Side-by-Side Comparison](#ping-vs-traceroute---side-by-side-comparison)<br>
7. [Networking Tools: WHOIS](#networking-tools-whois) <br>
7.1 [Installation Notes (Linux)](#installation-notes-linux)<br>
8. [Networking Tools: Dig](#networking-tools-dig) <br>
8.1 [What Is DNS?](#what-is-dns)<br>
8.2 [How DNS Resolution Works](#how-dns-resolution-works)<br>
8.3 [Dig: Manual DNS Queries](#dig-manual-dns-queries)<br>
8.4 [Understanding Dig Output](#understanding-dig-output)<br>

---
   
# 📚 Study Notes 


# The OSI Model: An Overview

- The OSI (Open Systems Interconnection) Model is a standardised framework used to explain how computer networks function. While real-world networking mainly relies on the more compact TCP/IP model, the OSI model is widely used for learning because it breaks networking concepts down into clear, logical layers.

- The OSI model is made up of seven layers, each with a specific responsibility in the process of sending and receiving data across a network.

|Layer|Name|
|:--:|------------------|
|7|Application|
|6|Presentation|
|5|Session|
|4|Transport|
|3|Network|
|2|Data Link|
|1|Physical|

## Layer 7 – Application

- The Application layer provides networking capabilities directly to applications running on a computer.
- It acts as the interface that applications use to send data over a network.
- This layer works almost exclusively with software applications.
- Once data is ready, it is passed down to the presentation layer.

## Layer 6 – Presentation

- The Presentation layer receives data from the application layer and ensures it is in a standardised format that the receiving system can understand.
- It is responsible for data translation, encryption and decryption, compression and decompression
- After these transformations are complete, the data is passed to the session layer.

## Layer 5 – Session

- The Session layer is responsible for establishing, maintaining, and terminating communication sessions between two systems.
- If a session cannot be established, an error is returned and communication stops.
- If successful, the session layer maintains the connection and synchronises communication with the remote system.
- Each session is unique, which allows multiple connections to exist at the same time without data getting mixed up (for example, opening multiple browser tabs simultaneously).
- Once the session is established, data moves down to the transport layer.

## Layer 4 – Transport

- The Transport layer plays a critical role in data transmission.
- Its responsibilities include protocol selection
- The two most common transport protocols are **TCP (Transmission Control Protocol)** - connection-based, reliable transmission, ensures packets arrive correctly and in order, retransmits lost data, used when accuracy matters (e.g. file transfers, loading web pages) and **UDP (User Datagram Protocol)** - connectionless, faster but unreliable, no retransmission of lost packets, used when speed matters more than accuracy (e.g. video streaming, VoIP)

- **Segmentation** - once a protocol is chosen, the transport layer splits data into smaller pieces - **Segments** (TCP) and **Datagrams** (UDP) - this makes data easier and more reliable to transmit.

## Layer 3 – Network

- The Network layer is responsible for determining where data needs to go and how to get it there.
- It uses logical addressing, such as IP addresses
- Determines the best route across a network (routing)
- Organises and categorises networks
- The most common logical addressing format is IPv4, for example 192.168.1.1, which is commonly used by home routers.

### Layer 2 – Data Link

- The Data Link layer handles physical addressing and prepares data for transmission over the physical medium.
- Key responsibilities: Adds the MAC (Media Access Control) address of the destination device, formats data into frames suitable for transmission, detects transmission errors

- Every network-enabled device has a **Network Interface Card** (NIC) with a unique **MAC address** assigned by the manufacturer. While MAC addresses are hardware-based and cannot be changed, they can be spoofed.

- Although IP addresses identify where data should go logically, MAC addresses identify exactly which device should receive it on the local network.

## Layer 1 – Physical

- The Physical layer deals directly with hardware and raw data transmission.
- Converts binary data into electrical, optical, or radio signals
- Transmits signals across cables or wireless media
- Receives incoming signals and converts them back into binary data
- This layer includes cables, connectors, voltages, radio frequencies, and all physical networking components.


---  
><details><summary>❓Which layer would choose to send data over TCP or UDP?</summary>4</details>
---  
><details><summary>❓Which layer checks received information to make sure that it hasn't been corrupted?</summary>2</details>
---  
><details><summary>❓In which layer would data be formatted in preparation for transmission?</summary>2</details>
---  
><details><summary>❓Which layer transmits and receives data?</summary>1</details>
---  
><details><summary>❓Which layer encrypts, compress, or otherwise transforms the initial data to give it a standardised format?</summary>6</details>
---  
><details><summary>❓Which layer tracks communications between the host and receiving computers?</summary>5</details>
---  
><details><summary>❓Which layer accepts communication request from applications?</summary>7</details>
---  
><details><summary>❓Which layer handles logical addressing?</summary>3</details>
---  
><details><summary>❓When sending data over TCP, what would you call the "bite-sized" pieces of data?</summary>Segments</details>
---  
><details><summary>❓[Research] Which layer would the FTP protocol communicate with?</summary>7</details>
---  
><details><summary>❓Which transport layer protocol would be best suited to transmit a live video?</summary>UDP</details>
---

# Encapsulation

- Encapsulation is the process used to prepare data for transmission across a network.
- As data moves down through the OSI layers, each layer adds its own information to the transmission.
- This extra information is used to ensure the data reaches the correct destination and arrives intact.

- Each layer adds a header to the beginning of the data that contains information specific to that layer.
- For example:
  - The Network layer adds source and destination IP addresses
  - The Transport layer adds information related to the chosen protocol (TCP or UDP)
- The Data Link layer is slightly different, as it adds both a header at the start and a trailer at the end
- The trailer is used to verify that the data was not corrupted during transmission.
- This also improves security, since any attempt to intercept or modify the data would break the trailer and be detected.

- This entire process of adding layer-specific information is known as encapsulation.
- It allows data to be sent reliably from one computer to another.

<img width="1718" height="822" alt="image" src="https://github.com/user-attachments/assets/bb6808b7-e21e-4474-8077-3c57cbfd0a6f" />

## Encapsulation Process (Data Naming)

- As data is encapsulated, it is referred to by different names at different stages of the OSI model:

|OSI Layer|Name of Data|
|------------------------------|--------------------------------------------------|
|Layers 7–5 (Application, Presentation, Session)|Data|
|Layer 4 (Transport)|Segment (TCP) / Datagram (UDP)|
|Layer 3 (Network)|Packet|
|Layer 2 (Data Link)|Frame|
|Layer 1 (Physical)|Bits|

By the time the data is transmitted across the network, the frame has been broken down into bits.

## De-encapsulation

- When the destination computer receives the transmission, it performs the reverse process, starting at the Physical layer and moving upward through the OSI model.
- At each layer the added headers and trailer are removed and the remaining data is passed up to the next layer
- This process is called de-encapsulation, and it continues until the data reaches the Application layer, where it can be used by the receiving application.

---  
><details><summary>❓</summary>How would you refer to data at layer 2 of the encapsulation process (with the OSI model)?</summary>Frames</details>
---  
><details><summary>❓How would you refer to data at layer 4 of the encapsulation process (with the OSI model), if the UDP protocol has been selected?</summary>Datagrams</details>
---  
><details><summary>❓What process would a computer perform on a received message?</summary>De-encapsulation</details>
---  
><details><summary>❓Which is the only layer of the OSI model to add a trailer during encapsulation?</summary>Data Link</details>
---  
><details><summary>❓Does encapsulation provide an extra layer of security (Aye/Nay)?</summary>Aye</details>
---

# The TCP/IP Model

- The TCP/IP model is the foundation of real-world networking.
- It is older than the OSI model and, while very similar in purpose, is more compact and practical.
- The TCP/IP model uses four layers, which together cover the same networking functions as the seven layers of the OSI model.

|Layer|Name|
|--|------------------------|
|4|Application|
|3|Transport|
|2|Internet|
|1|Network Interface|

>[!NOTE]
>Some modern sources describe TCP/IP as a five-layer model, splitting the Network Interface layer into Data Link and Physical layers (similar to the OSI model). <br>
While this interpretation is widely accepted, the official definition remains the four-layer model described in RFC 1122. <br>
>Both versions are considered valid. <br>
>

## TCP/IP vs OSI Model

- Although the TCP/IP model is used in practice, the OSI model is still widely taught.
- This is because the OSI model is more detailed and rigid, making it easier to understand the theory behind networking before moving on to real-world implementations.

- The two models align closely, with the TCP/IP layers combining multiple OSI layers into fewer, broader categories.

- Encapsulation and de-encapsulation work exactly the same way in both models:
  - A header is added at each layer during encapsulation
  - Headers are removed in reverse order during de-encapsulation

## TCP/IP as a Protocol Suite

- TCP/IP is not just a layered model — it is a suite of protocols, meaning a collection of rules that define how data is transmitted across a network.
- The model takes its name from two of the most important protocols **TCP** (Transmission Control Protocol) – controls reliable data transfer and **IP** (Internet Protocol) – controls packet addressing and routing

- Many other protocols exist within the TCP/IP suite, which are typically covered in more advanced networking topics.

## TCP and the Three-Way Handshake

- TCP is a connection-based protocol, meaning a stable connection must be established before any data is sent. This setup process is known as the three-way handshake.

- Three-Way Handshake Process
  1. **SYN** – The client sends a packet with the SYN (synchronise) bit set, requesting a connection <br>
  2. **SYN-ACK** – The server responds with a packet containing both SYN and ACK (acknowledgement) bits <br>
  3. **ACK** – The client replies with a packet containing the ACK bit, confirming the connection <br>

- Once this handshake is complete a reliable connection is established, data can be transmitted safely, lost or corrupted data is re-sent, making the connection appear lossless

## History

- Originally, there was no standardisation in networking.
- Different manufacturers used their own methods, which made systems incompatible with one another.

- TCP/IP was introduced in 1982 by the U.S. Department of Defense (DoD) to create a universal networking standard
- This solved compatibility and consistency problems

- Later, the OSI model was introduced by the International Organization for Standardization (ISO)

- Today TCP/IP remains the foundation of modern networking and OSI is mainly used as a structured learning and reference model

<img width="533" height="376" alt="image" src="https://github.com/user-attachments/assets/c5723e27-573d-4b99-a6ec-617bd289cf35" />


---  
><details><summary>❓Which model was introduced first, OSI or TCP/IP?</summary>TCP/IP</details>
---  
><details><summary>❓Which layer of the TCP/IP model covers the functionality of the Transport layer of the OSI model (Full Name)?</summary>Transport</details>
---  
><details><summary>❓Which layer of the TCP/IP model covers the functionality of the Session layer of the OSI model (Full Name)?</summary>Application</details>
---  
><details><summary>❓The Network Interface layer of the TCP/IP model covers the functionality of two layers in the OSI model. These layers are Data Link, and? ...(Full Name)?</summary>Physical</details>
---  
><details><summary>❓Which layer of TCP/IP model handles the functionality of OSI network layer?</summary>Internet</details>
---  
><details><summary>❓What kind of protocol is TCP?</summary>Connection-based</details>
---  
><details><summary>❓What is SYN short for?</summary>Synchronise</details>
---  
><details><summary>❓What is the second step of the three way handshake?</summary>SYN/ACK</details>
---  
><details><summary>❓What is the short name for "Acknowledgement" segment in the three-way handshake?</summary>ACK</details>
---

# Networking Tools: Ping

- The `ping` command is used to test whether a connection to a remote host is possible.
- Common use cases include checking connectivity to a website on the internet, verifying communication between devices on a local network, confirming whether a system is reachable and responding

## How Ping Works

- `ping` operates using the **ICMP (Internet Control Message Protocol)**, which is part of the TCP/IP protocol suite.

- ICMP operates at Network layer of the OSI model and Internet layer of the TCP/IP model

- `ping` sends ICMP echo requests and waits for echo replies - A successful reply indicates that the target is reachable

<img width="446" height="34" alt="image" src="https://github.com/user-attachments/assets/444a7a9c-f538-4ff3-b884-17768b635fcf" />

- When pinging a website, the command resolves the **domain name to an IP address** and displays the IP address of the server being contacted
- This makes `ping` useful not only for connectivity testing, but also for identifying the IP address associated with a domain.

- For additional options and syntax details on Linux, you can use `man ping`.



---  
><details><summary>❓What command would you use to ping the bbc.co.uk website?</summary>ping bbc.co.uk</details>
---  
><details><summary>❓Ping muirlandoracle.co.uk What is the IPv4 address?</summary>217.160.0.152</details>
---  
><details><summary>❓What switch lets you change the interval of sent ping requests?</summary>-i</details>
---  
><details><summary>❓What switch would allow you to restrict requests to IPv4?</summary>-4</details>
---  
><details><summary>❓What switch would give you a more verbose output?</summary>-v</details>
---

# Networking Tools: Traceroute

- `Traceroute` is a natural follow-up to the `ping` command.
- While `ping` tells you whether a destination is reachable, `traceroute` **shows you how your request gets there**.

- It is used to **map the path that network traffic takes from your computer to a target machine**.


## What Traceroute Does

- The internet is made up of countless interconnected routers, servers, and endpoints.
- When you request data from a remote resource, your traffic does not travel directly — it passes through multiple intermediate systems along the way.

- `Traceroute` allows you to see each hop between your system and the destination, identify where delays or failures occur and understand the route taken through the network

- Each hop represents a device (usually a router) that forwards your traffic closer to its final destination.

## Protocol Differences

- `Traceroute` behaves slightly differently depending on the operating system:
  - Windows (`tracert`) - uses ICMP, the same protocol used by `ping`, by default
  - Linux / Unix (`traceroute`) - uses UDP by default

- In both cases, the protocol used can be changed using command-line options.

## Ping vs Traceroute - Side-by-Side Comparison

|Feature|ping|traceroute|
|--------------------|-------------------------|---------------------------------------------------------------------|
|**Purpose**|Checks if a target is reachable|Shows the path taken to reach a target|
|**Main Question Answered**|“Can I reach this host?”|“How do I get to this host?”|
|**Protocol Used**|ICMP|ICMP (Windows) / UDP (Linux & Unix by default)|
|**OSI Layer**|Network layer|Network layer|
|**TCP/IP Layer**|Internet layer|Internet layer|
|**Output**|Success/failure, latency, packet loss|List of intermediate hops with response times|
|**Shows IP Address**|Yes (resolves domain to IP)|Yes (for each hop)|
|**Shows Network Path**|No|Yes|
|**Common Use Case**|Basic connectivity testing|Diagnosing routing and latency issues|
|**Typical First Step**|Yes|Usually after ping succeeds|
|**Availability**|Available on all major OS|Available on all major OS|
|**Speed & Simplicity**|Very fast and simple|More detailed, slightly slower|


- On the picture below you can see that it took 13 hops to get from router (_gateway) to the Google server at 216.58.205.46
<img width="811" height="252" alt="image" src="https://github.com/user-attachments/assets/c5ea8014-2c5a-4234-8d51-a64cc95d6dcb" />


---  
><details><summary>❓What switch would you use to specify an interface when using Traceroute?</summary>-i</details>
---  
><details><summary>❓What switch would you use if you wanted to use TCP SYN requests when tracing the route?</summary>-T</details>
---  
><details><summary>❓[Lateral Thinking] Which layer of the TCP/IP model will traceroute run on by default (Windows)?</summary>Internet</details>
>✅Solution: On Windows tracert uses ICMP which is the same protocol used by ping. ICMP operates at Network layer in the OSI model and Internet layer in the TCP/IP model.
---

# Networking Tools: WHOIS

**Domain Names: The Human-Friendly Internet**

- Remembering the IP address of every website would be impractical.
- Domain names solve this problem by providing human-readable names that translate to IP addresses behind the scenes.
- Instead of typing an IP address, you can type tryhackme.com - The domain is then resolved to the correct IP address automatically
- This translation process allows users to access websites easily without memorising numerical addresses.

- Domain names are managed and leased by companies known as **Domain Registrars**.

- `WHOIS` is a tool used to query information about a domain’s registration.
- A `WHOIS lookup` can provide details such as the domain registrar, registration and expiration dates, name servers, administrative and technical contact details (where available)

>[!NOTE]
>In Europe, personal registration details are typically redacted due to privacy regulations.
>In other regions, WHOIS results may reveal more information.
>

## Installation Notes (Linux)

- On Debian-based systems, `WHOIS` may need to be installed manually by running ` sudo apt update && sudo apt-get install whois`

- `Whois lookups` are very easy to perform. Just use `whois <domain>` to get a list of available information about the domain registration:
<img width="928" height="438" alt="image" src="https://github.com/user-attachments/assets/eccb47df-55a3-46f1-a117-619aa70c1853" />



---  
><details><summary>❓What is the registrant postal code for facebook.com</summary>94025</details>
---  
><details><summary>❓When was the facebook.com domain first registered (Format: DD/MM/YYYY)</summary>29/03/1997</details>
---  
><details><summary>❓Which city is the registrant based in?</summary>Redmont</details>
---  
><details><summary>❓[OSINT] What is the name of the golf course that is near the registrant address for microsoft.com?</summary>Bellevue Golf Course</details>
---  
><details><summary>❓What is the registered Tech Email for microsoft.com?</summary>msnhst@microsoft.com</details>
---

# Networking Tools: Dig

## What Is DNS?

- The **Domain Name System (DNS)** is a TCP/IP protocol that **converts domain names** (which humans use) **into IP addresses** (which computers understand).
- For example: You type www.google.com, DNS resolves it to an IP address, your computer then connects to that IP address
- This entire process happens automatically in the background whenever you visit a website.

## How DNS Resolution Works

- When you request a website, your computer resolves the domain name in the following order:

1. **Hosts File**
- The computer first checks its local **hosts file**
- This file contains manual IP = domain mappings
- It is an older system but still takes highest priority
- Rarely used in modern environments

2. **Local DNS Cache**
- If no hosts file entry exists, the system checks its local DNS cache
- If the domain was resolved recently, the cached IP is used
- If not found, the process continues

3. **Recursive DNS Server**
- The computer sends a query to a recursive DNS server
- These are usually provided by your ISP or public providers like Google DNS or OpenDNS
- The address of the recursive server is stored in your router or system
- Recursive servers also maintain their own cache
- If the recursive server does not have the answer cached, it continues the lookup process.

4. **Root Name Servers**
- Root servers direct queries to the correct **Top-Level Domain (TLD)** servers
- Historically there were 13 root server IP addresses
- Today there are many physical servers using those same addresses via load balancing
- The client is routed to the nearest available root server

5. **Top-Level Domain (TLD) Servers**
- TLD servers manage domain extensions such as `.com`, `.org`, `.co.uk`
- The root server forwards the query to the appropriate TLD server
- TLD servers know which **Authoritative Name Servers** hold the domain’s records

6. **Authoritative Name Servers**
- These servers store the actual DNS records for domains
- Every domain has its DNS records stored on an authoritative server
- This server responds with the final IP address for the domain
- Once this response is returned, your computer can connect to the website.

## Dig: Manual DNS Queries

- While DNS resolution usually happens automatically, it can be performed manually using the dig command.
- `dig` is commonly pre-installed on Linux systems
- It allows direct queries to specific DNS servers
- Useful for troubleshooting DNS issues
- syntax: `dig <domain> @<dns-server-ip>`

<img width="616" height="366" alt="image" src="https://github.com/user-attachments/assets/9ccc0ab0-1f31-43e1-b258-6820f9ec8a1b" />


## Understanding Dig Output

- The output of dig contains a lot of information.
- The most important section for basic usage is `ANSWER` section and `TTL` (Time To Live)
- `ANSWER` section shows the resolved IP address for the queried domain, confirms whether the query succeeded and indicates that the server returned a valid response with no errors
- `TTL` (Time To Live) defines how long a DNS record can be cached, it is measured in seconds
- Once the TTL expires, the record must be queried again instead of using the cached version
- for example: a `TTL` of 157 seconds means the record will expire in 2 minutes and 37 seconds
<img width="450" height="147" alt="image" src="https://github.com/user-attachments/assets/313c9211-4c7d-4505-9547-eb6d9d00e752" />


---  
><details><summary>❓What is DNS short for?</summary>Domain Name System</details>
---  
><details><summary>❓What is the first type of DNS server your computer would query when you search for a domain?</summary>Recursive</details>
---  
><details><summary>❓What type of DNS server contains records specific to domain extensions (i.e. .com, .co.uk*, etc)*? Use the long version of the name.</summary>Top-Level Domain</details>
---  
><details><summary>❓Where is the very first place your computer would look to find the IP address of a domain?</summary>Hosts File</details>
---  
><details><summary>❓[Research] Google runs two public DNS servers. One of them can be queried with the IP 8.8.8.8, what is the IP address of the other one?</summary>8.8.4.4</details>
---  
><details><summary>❓If a DNS query has a TTL of 24 hours, what number would the dig query show?</summary>86400</details>
---
