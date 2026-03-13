---
layout: default
title: What is Networking?
description: Begin learning the fundamentas of computer networking in this bite-sized and interactive module.
---

🔗 [Link to the Room](https://tryhackme.com/room/whatisnetworking)

## 🏷️ Table of Contents

1. [What is Networking?](#what-is-networking)<br>
2. [What is the Internet?](#what-is-the-internet)<br>
    2.1 [History of the Internet](#history-of-the-internet)<br>
    2.2 [Structure of the Internet](#structure-of-the-internet)<br>
3. [Identifying Devices on a Network](#identifying-devices-on-a-network)<br>
    3.1 [Device Identification Analogy](#device-identification-analogy)<br>
    3.2 [IP Addresses](#ip-addresses)<br>
    3.3 [Public IP Address](#public-ip-address)<br>
    3.4 [IPv4 vs IPv6](#ipv4-vs-ipv6)<br>
    3.5 [MAC Addresses](#mac-addresses)<br>
    3.6 [MAC Address Spoofing](#mac-address-spoofing)<br>
    3.7 [MAC Filtering in Public Wi‑Fi](#mac-filtering-in-public-wifi)<br>
4. [Ping (ICMP)](#ping-icmp)<br>

---

# 📚 Study Notes 


# What is Networking?

- At its most basic level, a network is simply things that are connected.

- A helpful way to think about this is through everyday examples, such as a friendship group, where people are connected by shared interests, activities, or goals.

- Networks exist everywhere, not just in computing. 
- For example:
    - A city’s public transportation system
    - National electricity and power grids
    - Postal systems for sending letters and parcels
    - Social interactions, such as meeting and greeting neighbours

- All of these systems involve connected entities working together to share resources or information.

- In computing, networking follows the same concept but applies it to technological devices.

- Examples of networked devices include laptops and smartphones, servers and routers, security cameras and traffic lights, industrial and agricultural systems

- A computer network can consist of as few as two devices or billions of devices connected globally

- Devices are networked so they can communicate, share data, and access resources.

- Networks are deeply integrated into modern life, supporting systems such as weather data collection, power distribution, traffic management or communication and information access

- Because networks underpin so much of today’s infrastructure, understanding networking is essential in cybersecurity. 
- Securing systems requires knowing how devices connect, communicate, and interact.

&nbsp;

---  
><details><summary>❓What is the key term for devices that are connected together?</summary>Network</details>
---

&nbsp;

# What is the Internet?


- The Internet is essentially **one giant network made up of many smaller networks connected together**.

&nbsp;

## History of the Internet

- Late 1960s: The **ARPANET project**, funded by the U.S. Department of Defense, created the first documented network
- 1989: Tim Berners-Lee invented the World Wide Web (WWW), transforming the Internet into a global system for storing and sharing information
- Since then, the Internet has become the vast, interconnected network we use today

&nbsp;

## Structure of the Internet

- The Internet is made up of private networks (smaller, individual networks) and public networks (networks connecting private networks — the Internet itself)

>[!NOTE]
> In short, a network can be private or public, with the Internet being the largest example of a public network.
>

&nbsp;

---  
><details><summary>❓Who invented the World Wide Web?</summary>Tim Berners-Lee</details>
---

&nbsp;

# Identifying Devices on a Network


- For devices to communicate reliably on a network, they must be **identifiable**. Just like humans can be identified in more than one way, network devices also have two forms of identification.

&nbsp;

## Device Identification Analogy

- Devices are similar to people where name is an IP Address (can change) and fingerprint is MAC Address (hardware-based, intended to be unique)
- Both are used to identify devices, but they serve different purposes.

&nbsp;

## IP Addresses

- An **IP (Internet Protocol)** address identifies a device on a network for a period of time.
- **Key Properties**:
    - Written as four numbers (IPv4) separated into octets
    - Assigned through IP addressing and subnetting methods
    - Can change over time (dynamic assignment)
    - Cannot be used by more than one device at the same time within the same network
    - Follows standard networking protocols so devices can communicate consistently
 
&nbsp;

## Public IP Address

- Used to identify a network/device on the Internet
- Assigned by an Internet Service Provider (ISP)
- Usually shared by multiple devices behind a router (via NAT)
- Costs money as part of your internet service

>[!IMPORTANT]
> Devices inside the same private network talk using private IPs
> Traffic going out to the Internet appears to come from the same public IP
> 

&nbsp;

## IPv4 vs IPv6

&nbsp;

**IPv4**

- Current widely used version
- Uses 32-bit addressing
- Supports about 4.29 billion addresses
- Address shortage due to massive device growth

&nbsp;

**IPv6**

- Newer version designed to solve IPv4 exhaustion
- Uses 128-bit addressing
- Supports an extremely large number of addresses (2¹²⁸)
- More efficient and modern design
- Written in hexadecimal format

&nbsp;

## MAC Addresses

- A **MAC (Media Access Control)** address identifies a device’s physical network interface.
- **Key Properties**:
    - Assigned at the factory to the network interface card (NIC)
    - Intended to be globally unique
    - Acts like a hardware serial number
    - Format: 12-character hexadecimal, separated by colons, e.g. `a4:c3:f0:85:ac:2d`

- **Structure**: First half identifies the manufacturer and the second half is unique device identifier

&nbsp;

## MAC Address Spoofing

* Although MAC addresses are hardware-based, they can be spoofed (faked).
- MAC Spoofing means, that device pretends to use another device’s MAC address, used in both testing and attacks and can bypass weak security systems

&nbsp;

>[!CAUTION]
> If a firewall allows traffic from a specific MAC address (e.g., admin device), an attacker who spoofs that MAC address may be incorrectly trusted.
>

&nbsp;

## MAC Filtering in Public Wi‑Fi

- Some public networks (cafes, hotels, guest Wi‑Fi) use MAC-based access control:
    - Access or speed is granted per MAC address
    - Payment may unlock access for one device only
    - Changing (spoofing) a MAC address can sometimes bypass these controls if poorly implemented

&nbsp;

---  
><details><summary>❓What does the term "IP" stand for?</summary>Internet Protocol</details>
---  
><details><summary>❓What is each section of an IP address called?</summary>Octet</details>
---  
><details><summary>❓How many sections (in digits) does an IPv4 address have?</summary>4</details>
---  
><details><summary>❓What does the term "MAC" stand for?</summary>Media Access Control</details>
---  
><details><summary>❓Deploy the interactive lab using the "View Site" button and spoof your MAC address to access the site. What is the flag?</summary>THM{***_**_*********}</details>
---

&nbsp;

# Ping (ICMP)

- `Ping` is a basic network troubleshooting tool used to test connectivity between two devices.
- It works using **ICMP (Internet Control Message Protocol)**.
- `Ping` sends an ICMP Echo Request packet to a target device and the target replies with an ICMP Echo Reply packet.
- This confirms:
    - Whether the device is reachable
    - Whether the connection is working
    - How fast packets travel between devices

- **Ping Measures**:
    - **Round-trip time (latency)** — how long packets take to go to the target and back
    - **Packet success/loss** — whether packets are received or dropped
    - Helps judge connection reliability and performance

- Basic command: `ping <IP address or domain>`

- Typical output shows packets sent/received, packet loss % and minimum / maximum / average response time (ms)

&nbsp;

---  
><details><summary>❓What protcol does ping use?</summary>ICMP</details>
---  
><details><summary>❓What is the syntax to ping 10.10.10.10?</summary>ping 10.10.10.10</details>
---  
><details><summary>❓What flag do you get when you ping 8.8.8.8?</summary>THM{*_******_***_******}</details>
---  
