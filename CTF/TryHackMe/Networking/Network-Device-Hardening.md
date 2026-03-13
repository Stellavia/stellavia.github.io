---
layout: default
title: Network Device Hardening
description: Learn techniques for securing and protecting network devices from potential threats and attacks.
---

🔗 [Link to the Room](https://tryhackme.com/room/networkdevicehardening)

## 🏷️Table of Contents

1. [Common Threat and Attack Vectors](#common-threat-and-attack-vectors)<br>
  1.1 [Difference between Network Devices and Endpoint Devices](#difference-between-network-devices-and-endpoint-devices)<br>
1.2 [Common Threats and Attack Vectors of Network Devices](#common-threats-and-attack-vectors-of-network-devices)<br>
2. [Common Hardening Techniques](#common-hardening-techniques)<br>
  2.1 [General Techniques](#general-techniques)<br>
  2.2 [Importance of Secure Protocols](#importance-of-secure-protocols)<br>
  2.3 [Removal/Blocking of Insecure Protocols](#removalblocking-of-insecure-protocols)<br>
  2.4 [Implementation of Monitoring and Logging Controls](#implementation-of-monitoring-and-logging-controls)<br>
3. [Hardening Virtual Private Networks](#hardening-virtual-private-networks)<br>
  3.1 [Connecting to the Machine](#connecting-to-the-machine)<br>
  3.2 [Standard Hardening Practices](#standard-hardening-practices)<br>
4. [Hardening Routers, Switches and Firewalls](#hardening-routers-switches-and-firewalls)<br>
  4.1 [Recommended Hardening Techniques](#recommended-hardening-techniques)<br>
5. [Hardening Routers, Switches and Firewalls - More Techniques](#hardening-routers-switches-and-firewalls---more-techniques)<br>
  5.1 [Recommended Hardening Techniques](#recommended-hardening-techniques)<br>
  5.2 [Additional Techniques in an Enterprise Environment](#additional-techniques-in-an-enterprise-environment)<br>
6. [Important Tools for Network Monitoring](#important-tools-for-network-monitoring)<br>

---

# 📚 Study Notes #

<!-- INTRODUCTION HERE -->
Network devices are the basic parts that make modern networks work. They help send, receive, control, and protect data as it moves between computers and networks.

There are different types of network devices. Some are very simple, like **hubs** and **repeaters**, which just **pass** **data** along. Others are more advanced, like **switches** and **routers**, which **direct traffic**. There are also devices that **balance traffic**, create **secure connections** (VPNs), and **protect networks** from attacks (intrusion prevention systems).

&nbsp;

# Common Threat and Attack Vectors

&nbsp;

## Difference between Network Devices and Endpoint Devices

Before learning more, it’s important to understand the difference:

- **Endpoint devices** are devices that send or receive data on a network.<br>
Examples: laptops, desktops, smartphones, tablets, printers, servers, and IoT devices.<br>
They are at the edge of the network and are used directly by people.

- **Network devices** manage and control the flow of data between endpoint devices.<br>
Examples: switches, routers, firewalls.<br>
They direct, filter, and protect network traffic.

&nbsp;

>[!NOTE]
> **Endpoints use the network.** <br>
> **Network devices run and manage the network.**

&nbsp;

<img width="1140" height="449" alt="image" src="https://github.com/user-attachments/assets/7fc38e2e-bcc6-4d9b-ae48-d08cb777bc20" />

## Common Threats and Attack Vectors of Network Devices

Modern ICT networks have grown rapidly and connected the world, however, this growth has also increased cyber attacks.

**Network devices are the backbone of these systems, so they must be secured.**

To protect **Confidentiality**, **Integrity**, and **Availability (CIA)**, we need to apply security hardening to network devices.

**Goals of security hardening:**
- Prevent unauthorized access and attacks
- Enforce access control policies
- Stop data theft
- Keep important systems available and running


|Threat|Description|Attack Vector|
|--------------------|----------------------------------|-----------------------------------------|
|Unauthorised access|Gain unauthorised control of a network device, and then the complete network.|Password attacks (brute force, dictionary & hybrid), Exploit known vulnerabilities, e.g. RCE, Social Engineering/Phishing attack to trick network administrators into disclosing sensitive information such as usernames and passwords of devices|
|Denial of Service (DoS)| Disruption of critical devices and services to make them unavailable to genuine users.| Flooding devices with fake requests, exploiting vulnerabilities in logical or resource handling, manipulating network packets|
|Man-in-the-Middle Attacks|Intercept the network requests between two parties by masquerading as each other to steal sensitive information or alter/manipulate the requests.|ARP spoofing, DNS spoofing, Rogue access points|
|Privilege escalation|Gaining higher-level privileges or rights to perform restricted actions, e.g. accessing sensitive information or executing malicious code.|Weak passwords or use of the same passwords for user and admin accounts, exploiting vulnerabilities, misconfigurations|
|Bandwidth theft/ hotlinking|Linking a bandwidth-intensive resource (image or video) from an external website to its original website, without permission. This can cause increased traffic to the original website.|Scraping large volumes of data, DoS attacks, malware attacks|

&nbsp;

---
><details><summary>❓The device that is used to control and manage network resource is called?</summary>Network device</details>
---
><details><summary>❓A threat vector that includes disruption of critical devices and services to make them unavailable to genuine users is called?</summary>Denial of Service</details>
---

&nbsp;

# Common Hardening Techniques

&nbsp;

## General Techniques
Hardening techniques are meant to reduce the attack surface of a system or network by removing unnecessary functionality, limiting access, and implementing various security controls. <br>
Some standard methods are mentioned below:

- **Updating & Patching**: Ensuring the latest version of the Operating System and underlying applications of all devices and systems and installing regular security patches is the core hardening measure. Outdated OS and applications contain vulnerabilities that attackers can exploit.
- **Disabling unnecessary services & ports**: Turn off services and close ports that are not needed. This reduces entry points for attackers.
- **Principle of Least Privilege (POLP)**: Restrict users and processes to only the minimum necessary permissions required to perform their functions.
- **Logs Monitoring**: Implement a log monitoring system to monitor for unusual activity or security events.
- **Backup regularly**: Take routine backups of systems and configurations as they can help recover from a security incident or system failure.
- **Enforcing Strong Passwords**: Change default passwords. Use strong passwords (at least 10 characters) with lowercase, uppercase, numbers, and special characters. This protects against dictionary and brute-force attacks.
- **Multi-Factor Authentication (MFA)**: Use two or more verification methods to access a system. E.g., something you know (password) and something you have (biometrics or a device).


## Importance of Secure Protocols

Secure protocols are important for network device hardening. They **protect against unauthorized access** and data breaches.

They encrypt data sent between devices, so attackers cannot read or intercept it. They also help prevent man-in-the-middle attacks and other network attacks.

By using secure protocols, only authorized users can access sensitive data and manage systems.

**Common secure protocols**: HTTPS, SSH, SSL/TLS, IPsec


## Removal/Blocking of Insecure Protocols 

Besides using secure protocols, it is also important to remove or **block insecure protocols**. This reduces the attack surface and makes attacks harder.

Some **protocols send data in plain text** (not encrypted), which attackers can easily read. E.g., **FTP, HTTP, Telnet**, and **SMTP**.

Some protocols are secure by design (such as LDAP, RDP, and SIPS), but if they are configured incorrectly, attackers may still exploit them.

## Implementation of Monitoring and Logging Controls

Logging on network devices is important to detect attacks, troubleshoot problems, and meet regulations. <br> 
It keeps a record of what happens on the device, which helps with forensics, auditing, and troubleshooting.

Common logging techniques:

- **Syslog**: Sends log messages to a central server for storage and analysis.
- **SNMP**: Sends notifications (traps) to a management system when certain events happen.
- **NetFlow**: Collects and analyzes network traffic for monitoring and security.
- **Packet Captures**: Records network traffic for detailed analysis using tools like Wireshark.

&nbsp;

---
><details><summary>❓Suppose you are configuring a router; which of the following could be considered an insecure protocol: A: HTTPS, B: FTP, C: SSH, D: IPsec</summary>B</details>
---
><details><summary>❓The protocol for sending log messages to a centralised server for storage and analyse is called?</summary>Syslog</details>
---

&nbsp;

# Hardening Virtual Private Networks

&nbsp;

**Virtual Private Networks (VPNs) are important for protecting data and privacy**, especially with remote work and online communication.

Hardening VPNs means adding extra security to make them safer from cyberattacks. This includes multi-factor authentication (MFA) and strong encryption.

These steps make it harder for hackers to access the network and help keep data safe, improving overall security and peace of mind.

<img width="550" height="284" alt="image" src="https://github.com/user-attachments/assets/d84e515d-3aac-440f-925d-065776bd4f1a" />


## Connecting to the Machine

In this room we are using an Ubuntu machine with OpenVPN installed roughout the room. You can start the virtual machine by clicking Start Machine. The machine will start in a split-screen view. In case the VM is not visible, use the blue Show Split View button at the top-right of the page. Please wait 3-5 minutes to make sure the VM is fully booted.

## Standard Hardening Practices

**VPN servers have server-side and client-side settings in a config file**. Admins should understand and edit this file following best security practices.

**Open the server config file**: `sudo nano /etc/openvpn/server/server.conf`

**Save changes**: `Ctrl+O` → `Enter`, then `Ctrl+X` to exit

**Restart OpenVPN service**: `sudo systemctl restart openvpn-server@server.service`

### Key Hardening Practices:

- **Use strong encryption**: Set a secure cipher in the config file (AES-128-CBC, AES-256-CBC, etc).<br>
  - Configure the VPN gateway to use strong encryption to protect data in transit. The cipher directive in the config file can be used to select the encryption scheme.<br>
  - The possible options for cipher include AES, Blowfish, Camellia, and more.
  - E.g. AES-128-CBC mode means to use the AES encryption algorithm with a key size of 128-bit in Cipher Block Chaining (CBC) mode, as seen below. AES-256-CBC is typically considered one of the strongest cipher encryption nowadays.<br>
<img width="469" height="276" alt="image" src="https://github.com/user-attachments/assets/92e2ed7a-c603-4c39-8441-3d3f187b5507" />

- **Keep VPN software updated**: Regularly apply updates and security patches of the VPN gateway software. e.g. `sudo apt upgrade openvpn`

- **Implement strong authentication**: Use secure hashing algorithms (SHA256, SHA512, etc.) with TLS.
  -  We can use the auth directive to specify the exact algorithm in the OpenVPN configuration file to ensure that a secure hashing algorithm will be used for packet authentication. 
    
<img width="481" height="270" alt="image" src="https://github.com/user-attachments/assets/e3ff0ec1-0503-426e-9055-655971118f79" />

- **Change default settings**: Replace default usernames and passwords with unique ones to prevent unauthorized access.

- **Enable Perfect Forward Secrecy (PFS)**: PFS in OpenVPN generates unique session keys for each session to strengthen the security of the VPN connection. Because of this, even if a hacker successfully obtained a session key, they could not use it to decode more sessions. For each session, PFS generates a new set of encryption keys, preventing the possibility of remotely decrypting previously acquired material. 
  - Enable with `tls-crypt` in the OpenVPN config file to enable PFS.
  - Generate key: `sudo openvpn --genkey --secret my.key` and should be placed in the same directory on the server.
  - Choosing the appropriate cipher and auth, like cipher AES-256-CBC and auth SHA 256, supports PFS if combined with tls-crypt.

<img width="527" height="309" alt="image" src="https://github.com/user-attachments/assets/501a0159-40c4-40b9-8a10-408e4fdffe71" />

&nbsp;

- **Dedicated VPN users**: Create a specific user account/group with limited permissions to run the VPN server.

&nbsp;

---
><details><summary>❓Update the config file to use cipher AES-128-CBC. What is the flag value linked with the cipher directive?</summary>THM{C*****_U******_***1}</details>
<!-- THM{CIPHER_UPDATED_1101} -->
---
><details><summary>❓Update the config file to use auth SHA512. What is the flag value linked with the auth directive?</summary>THM{A***_U******_**3}</details>
<!-- THM{AUTH_UPDATED_123} -->
---
><details><summary>❓As per the config file, what is the port number for the OpenVPN server?</summary>1194</details>
---

&nbsp;

# Hardening Routers, Switches and Firewalls

&nbsp;

- Routers and switches need to be hardened to keep a network secure and reliable. <br>
- They are essential devices and often the first line of defence against attacks. <br>
- Hardening them helps prevent unauthorized access, data breaches, and service disruptions. 
It also improves performance, strengthens protection against cyberattacks, and supports compliance with regulations. <br>
- Without proper hardening, these devices can be vulnerable to attacks like denial of service (DoS) and network profiling.

- In the previous task, we studied **how to harden a network device using a configuration file** on a Command Line Interface (CLI).
- - In this task, we will use the **web interface** to learn different methods **for hardening a router**. We will be using a router that has **OpenWrt** installed, which is a _free and open-source Linux-based operating system_ for embedded devices. 

 - You can access the OpenWrt web interface at your `MACHINE_IP:8080` with username `root` and password `TryHackMe`

&nbsp;

## Recommended Hardening Techniques

- **Setting up the device:**
  - When setting up a network device, you should configure important details like the hostname, timezone, and logging.
  - These settings help with incident response if something goes wrong. For example, logging should be enabled (with the default level set to Debug) so all events are recorded.
  - The correct timezone and time synchronization are also important to accurately track when events happen.
  - In OpenWrt, these settings can be configured under System > System.

&nbsp; 

<img width="950" height="582" alt="image" src="https://github.com/user-attachments/assets/bc3b21f0-9236-4a51-b3f7-3e015bcc61bc" />

&nbsp;

- **Change default credentials**: 
  - change the default password in OpenWrt through `System` > `Administration`, enter a new password, and click the `Save` button.

&nbsp;

<img width="970" height="404" alt="image" src="https://github.com/user-attachments/assets/37f86bbc-5839-419c-b5eb-ea4786b4acd6" />

&nbsp;

- **Enable secure network protocols**:
  - To protect network traffic and ensure confidentiality, integrity, and availability, secure protocols must be enabled.
  - Protocols like HTTPS, SSH, and SSL/TLS encrypt communication and authentication, helping prevent unauthorized access, eavesdropping, data breaches, and man-in-the-middle attacks.
  - In OpenWrt, SSH can be enabled under `System` > `Administration` > `SSH Access`, where you choose the interface and port, then click `Save & Apply`. You can also add public SSH keys to allow secure, passwordless login.

&nbsp;

<img width="986" height="645" alt="image" src="https://github.com/user-attachments/assets/952d844d-1976-4d46-8076-22c86252f8ad" />

&nbsp;

- **Disabling unnecessary scripts**:
  - Most network devices run startup scripts to handle automatic tasks, such as executing scheduled cron jobs.
  - Attackers may try to add malicious scripts to these startup processes to maintain persistent access.
  - In OpenWrt, you can manage (add, remove, or set priority for) startup scripts under `System` > `Startup`.

&nbsp;

<img width="955" height="646" alt="image" src="https://github.com/user-attachments/assets/0c72b403-d2d9-45a4-99a6-d4fd17b9dc21" />

&nbsp;

- **Securing Wi-Fi**:
  - If the router has Wi-Fi capabilities, securing the Wi-Fi by enabling strong encryption like WPA2/WPA3, disabling SSID broadcast, changing default passwords, and more.
  
&nbsp;

---
><details><summary>❓Update the password of the router to TryHackMe123</summary>No answer needed</details>
---
><details><summary>❓WHat is the default SSH port configured for OpenWrt in the attached VM?</summary>22</details>
---
><details><summary>❓Go thourhg the General Settings option Under the System tab in the attached VM. The administrator has left a special message in the Notes section. What is the flag value?</summary>THM{S*******1}</details>
<!-- THM{SYSTEM101} -->
---
><details><summary>❓What is the default system log buffer size value for the OpenWrt router in the attached VM?</summary>64</details>
---
><details><summary>❓What is the start priority for the script uhttpd?</summary>50</details>
---

&nbsp;

# Hardening Routers, Switches and Firewalls - More Techniques

&nbsp;


## Recommended Hardening Techniques

- **Manage traffic rules**:
  - Network devices let you create traffic rules to allow or block specific network traffic.
  - e.g. if user data is being sent to an attacker’s command and control (C2) server, you can create a rule to block traffic going to that IP address.
  - In OpenWrt, traffic rules can be managed under `Network` > `Firewall` > `Traffic Rules`, where you click `Add` to create a new rule.

&nbsp;

<img width="947" height="648" alt="image" src="https://github.com/user-attachments/assets/0e140f81-ad3a-4040-8420-db43b51a283f" />

&nbsp;

- **Monitor traffic**:
  - As a network administrator, it’s important to monitor network traffic, including uploads and downloads over time.
  - e.g. if an email server is sending a large amount of data to an unknown IP address, it could indicate data theft. Monitoring traffic helps you detect such issues early and take action quickly.
  - In OpenWrt, you can view real-time traffic graphs under `Status` > `Realtime Graph` > `Traffic`.

&nbsp;

<img width="956" height="550" alt="image" src="https://github.com/user-attachments/assets/d1cacc15-51cf-4e16-ac7c-d977deb17de9" />
_Note: Since no client is connected with the network device, you won't see any traffic in the real-time traffic statistics on the target machine._ 

&nbsp;

- **Configuring port forwarding**: 
  - Port forwarding lets a firewall route incoming traffic from the internet to a specific device or service on your internal network.
  - It allows hosting applications or remote access while blocking other unwanted traffic. However, if not configured securely, it can expose devices to attacks, and attackers might add malicious rules to connect to external servers.
  - In OpenWrt, port forwarding can be set up under `Network` > `Firewall` > `Port Forwards`, then click `Add`.

&nbsp;

<img width="960" height="419" alt="image" src="https://github.com/user-attachments/assets/b441cf97-0496-4b7e-8141-9652b94bcd9e" />

&nbsp;

- **Monitoring scheduled tasks**:
  - It is important to monitor scheduled tasks to confirm that the original scheduled tasks lists are not modified by a threat actor.
  - To add or remove scheduled tasks, which in our case are handled by cron, navigate to `System` > `Scheduled Tasks`, `add` the new cron job, and click `Save`.

&nbsp;

<img width="958" height="221" alt="image" src="https://github.com/user-attachments/assets/bc47d76b-ff8e-40df-b905-e134234ef6b4" />

&nbsp;

- **Update firmware**:
  - It is essential to update the firmware and installed packages on a regular basis to avoid any know/unknown attacks. 
  - you can update the firmware through `System` > `Software`.

&nbsp;

<img width="956" height="633" alt="image" src="https://github.com/user-attachments/assets/a37f0f35-b7b1-471f-8804-7c7601d4ff52" />

&nbsp;

## Additional Techniques in an Enterprise Environment

Enterprise networks have many devices, which increases the attack surface for attackers.

While there’s no one-size-fits-all method to harden devices, some key security practices can help protect them.

- **Configuring port security**:
  - This includes limiting the number of MAC addresses registered on a switch port and taking particular action whenever unauthorised access is detected.
  - Enabling port security enables an administrator that data is coming from a valid source and will be forwarded to a legitimate receiver.
- **Preventing ARP spoofing**:
  - ARP spoofing is one of the most common vectors for launching man-in-the-middle attacks on the network.
  - The threat can be mitigated by enabling static ARP tables and implementing MAC address filtering. 
- **Preventing rogue DHCP servers:**
  - The attacker creates a spoofed DHCP server that can be later on used for assigning IPs to clients and launching MITM attacks.
  - Mitigation measures to prevent such attacks include configuring static DHCP binding and ensuring no unknown devices are added to a network through network mapping tools.
- **Enabling IPv6**:
  - Unlike IPv4, IPv6 has built-in support of IPsec that can be used to secure network communication and provide confidentiality, integrity, and authenticity.
  - Moreover, this will help in protection against MITM, eavesdropping, and tampering of packets in transit.

&nbsp;

---
><details><summary>❓What is the name of the rule that accepts ICMP traffic from source zone WAN and destination zone as this device?</summary>Allow-Ping</details>
---
><details><summary>❓What is the name of the rule that forwards data coming from WAN port 9001 to LAN port 9002?</summary>THM_PORT</details>
---
><details><summary>❓What is the version number for the available apk package?</summary>2.12.2-1</details>
---

&nbsp;

# Important Tools for Network Monitoring

&nbsp;

Network monitoring tools help maintain network security and performance by capturing and analyzing real-time traffic.<br> 
They allow administrators to identify and resolve issues such as bandwidth bottlenecks, outages, and security threats.

Some commonly used tools and their usage:

|Tool|Usage Description|
|-------------------------|---------------------------------------------------------------------------|
|**Nagios**|A popular open-source software for monitoring systems, networks, and infrastructure. It provides real-time monitoring and alerting for various services and applications.|
|**SolarWinds Network Performance Monitor**|A comprehensive network monitoring tool that provides real-time visibility into network performance and availability. It includes network mapping, automated network discovery, and customisable dashboards.|
|**PRTG**|An all-in-one network monitoring tool that provides comprehensive performance and availability monitoring. It includes real-time traffic analysis, custom dashboards, and customisable alerts.|
|**Zabbix**|A powerful open-source network monitoring tool that provides real-time network performance and availability monitoring. It includes features such as customisable dashboards, network mapping, and alerting.|

&nbsp;

---
><details><summary>❓Are network monitoring tools capable of detecting bandwidth bottlenecks? (yea/nay)</summary>yea</details>
---
&nbsp;

<!-- NO QUESTIONS -->
