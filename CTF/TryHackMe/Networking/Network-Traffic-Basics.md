---
layout: default
title: Network Traffic Basics
description: Learn what network analysis is, why it is essential, how to collect network traffic and which tools are available
---

🔗 [Link to the Room](https://tryhackme.com/room/networktrafficbasics)

## 🏷️ Table of Contents

1. [What is the Purpose of Network Traffic Analysis](#what-is-the-purpose-of-network-traffic-analysis)<br>
2. [What Network Traffic can we observe?](#what-network-traffic-can-we-observe)<br>
2.1 [Application Layer](#application-layer)<br>
2.2 [Transport Layer](#transport-layer)<br>
2.3 [Internet Layer](#internet-layer)<br>
2.4 [Link Layer](#link-layer)<br>
3. [Network Traffic Sources and Flows](#network-traffic-sources-and-flows)<br>
3.1 [Traffic Sources](#traffic-sources)<br>
3.2 [Traffic Flow Types](#traffic-flow-types)<br>
5. [How can we observe Network Traffic?](#how-can-we-observe-network-traffic)<br>

---

# 📚 Study Notes 

&nbsp;

- **Network Traffic Analysis (NTA)** is the process of capturing, inspecting, and analyzing network data as it moves across a network. 
- Its goal is to **provide full visibility into what is happening inside and outside the network**. 
NTA is more than just using tools like Wireshark — it combines log correlation, deep packet inspection, and network flow analysis to achieve specific security goals.

&nbsp;

- NTA involves collecting traffic data, examining packets, reviewing logs, and analyzing network flows to understand communication patterns. 
- Analysts compare normal network behavior (baseline) with unusual activity to detect potential threats. 
- It is a key skill for both blue team (defensive) and red team (offensive) roles, especially for SOC analysts monitoring security events.

- Strong networking fundamentals make NTA much easier to understand.
- Recommended background knowledge: **Pre Security** and **Networking Essentials**.

&nbsp;

# What is the Purpose of Network Traffic Analysis

- **Network Traffic Analysis (NTA) helps** security teams **understand what is happening inside and outside a network**.
- Logs alone often don’t provide enough detail to determine whether activity is malicious.
- By inspecting actual network traffic content, analysts can uncover hidden threats such as command-and-control (C2) communication or data exfiltration.

- Basic logs (like DNS logs from a firewall) show metadata such as source IP, queried domain, query type, and timestamps.
- However, they usually do not show the actual content of the communication.

![image](https://github.com/user-attachments/assets/0cce753a-224f-4386-bbf2-41ec20e45deb)



- For example, repeated DNS queries to the same domain with different subdomains may indicate **DNS tunneling** or **beaconing**.
- By capturing and inspecting packets, analysts can examine DNS responses and detect hidden data or encoded C2 commands (such as suspicious TXT records).
- This deeper inspection allows analysts to confirm whether the activity is malicious.

- The packet capture fragment below shows the content of a DNS reply that contains C2 commands:
  
![image](https://github.com/user-attachments/assets/8e91a639-79aa-4f63-8a3a-88a5d0f98042)


- Logs provide limited visibility — mostly metadata, not full content.
- Repeated or unusual DNS queries may indicate tunneling or beaconing.
- Packet inspection reveals the actual content of communication.
- NTA helps detect threats like data exfiltration, malware downloads, and lateral movement.
- SOC analysts use NTA to validate alerts and reconstruct attacks.

>[!TIP]
>Always compare activity against a network baseline — abnormal spikes in DNS or HTTP traffic often signal compromise. <br>
>DNS TXT records are commonly abused for C2 communication. <br>
>If logs raise suspicion, move to packet-level analysis to confirm what’s really happening.
>

&nbsp;

---  
><details><summary>❓What is the name of the technique used to smuggle C2 commands via DNS?</summary>DNS tunneling</details>
---

&nbsp;

# What Network Traffic can we observe?

![image](https://github.com/user-attachments/assets/ca0c8ea1-0e8c-4a13-bf0f-d61136b6f76e)

&nbsp;

## Application Layer

- At this layer, we see application headers and the actual payload (data).
- - The structure depends on the protocol (e.g., HTTP, DNS, FTP).

- For example, in an HTTP request we can see the requested file (e.g., suspicious_package.zip), the server response code (e.g., 200 OK), metadata like content type and file size.
  
![image](https://github.com/user-attachments/assets/d75e7a7b-f267-4b04-945f-4c134a81b9e5)

&nbsp;

- However, logs typically do not show the actual file content (the ZIP file itself). Only packet capture allows inspection of the full payload.

- Key Things We Can Observe
  - Requested resources (URLs, filenames)
  - Response codes (200, 404, etc.)
  - Headers (User-Agent, Content-Type, etc.)
  - Full payload (only via packet capture)

>[!NOTE]
> **Logs show what was requested — packet captures show what was actually delivered.**
>This is critical when checking for malware downloads or data exfiltration.
>

&nbsp;

## Transport Layer

- Here, data is segmented and encapsulated using TCP or UDP headers.
- Logs often show source and destination port, TCP flags (SYN, ACK, PSH), basic connection info,
- But full packet captures reveal additional details like sequence numbers, acknowledgment numbers, window sizes
- These fields are important for detecting attacks like session hijacking; for example a sudden jump in TCP sequence numbers may indicate a malicious packet injection attempt.

- Key Things We Can Observe
  - TCP 3-way handshake behavior
  - Port usage patterns
  - Suspicious flag combinations
  - Sequence number anomalies

&nbsp;

>[!CAUTION]
> **A large, unexpected jump in TCP sequence numbers can signal session injection or hijacking attempts.**
>

&nbsp;

## Internet Layer

- This layer adds the IP header.
- Logs usually include source IP, destination IP, TTL (Time To Live)
- However, full packet inspection reveals fragment offset, total length, fragmentation flags
- These are essential when detecting fragmentation attacks, such as overlapping fragments used to bypass IDS systems.

- Key Things We Can Observe
  - IP communication paths
  - Fragmentation behavior
  - TTL anomalies
  - Suspicious packet splitting

- Overlapping IP fragments can be used to evade detection systems, only full packet inspection reveals these manipulation attempts.

&nbsp;

## Link Layer

- At this layer, packets receive MAC addressing information.
- Logs usually show source MAC and destination MAC
- But full captures help detect ARP poisoning, MAC spoofing, duplicate MAC usage across interfaces, excessive gratuitous ARP traffic

- For example in an ARP poisoning attack, one device may repeatedly claim to own multiple IP addresses using the same MAC address.

&nbsp;

![image](https://github.com/user-attachments/assets/871522cc-9b77-4ae9-9f95-601da9b7c983)

&nbsp;

- Key Things We Can Observe
  - MAC address mappings
  - ARP request and reply behavior
  - Spoofed MAC addresses

>[!CAUTION]
> If one MAC address keeps claiming multiple IPs, you may be seeing ARP poisoning in action.

&nbsp;

Each TCP/IP layer gives us different visibility into network behavior. Logs only provide partial insight, while packet captures reveal the complete picture.

- Understanding what can be observed at every layer allows analysts to detect hidden attacks, investigate suspicious behavior, identify evasion techniques, validate security alerts

- Network traffic analysis isn’t just about watching traffic — it’s about understanding how every layer contributes to the full story.

&nbsp;

---  
><details><summary>❓Look at the HTTP example in the task and answer the following question: What is the size of the ZIP attachment included in the HTTP response? Note down the answer in bytes.</summary>10485760</details>
---  
><details><summary>❓Which attack do attackers use to try to evade an IDS?</summary>fragmentation</details>
---  
><details><summary>❓What field in the TCP header can we use to detect session hijacking?</summary>sequence number</details>
---

&nbsp;

# Network Traffic Sources and Flows

- When analyzing traffic, ask yourself two questions:

1. Where is this traffic coming from? (Source type)
2. Where is it going? (Flow direction)

- Everything fits into those two dimensions.

- We can group the **sources** into two categories: **Intermediary** and **Endpoint**
- The **flows** we can also group into two categories: **North-South** (Traffic that exits or enters the LAN and passes the firewall) and East-West (Traffic that stays within the LAN (including LAN that extends to the cloud))

&nbsp;

## Traffic Sources
### Intermediary Devices (Traffic Pass-Through Devices)

- These are infrastructure components.
- They don’t generate much user data — they control, forward, inspect, or log traffic.

- Examples: Firewalls (NGFW), Routers, Switches, IDS/IPS, Web proxies, Access points, Wireless controllers
- Traffic they generate are mostly control and management protocols, such as: Routing (OSPF, BGP, EIGRP), Monitoring (SNMP), logging (Syslog), network support (ARP, DHCP, STP), diagnostics (ICMP - ping)

- From a security perspective: Traffic from these devices is usually predictable. If a firewall suddenly starts sending unusual outbound connections? That’s suspicious.

&nbsp;

### Endpoint Devices (Where Data Actually Lives)

- These generate most of the bandwidth.
- Examples: Workstations, Servers, Virtual machines, Cloud workloads, IoT devices, Printers, Mobile devices
- These devices initiate sessions, download files, access shares, authenticate, communicate with apps

- If something malicious happens, it almost always starts here.

&nbsp;

## Traffic Flow Types

- Now the really important part.

- Think in terms of direction of movement.

&nbsp;

### North–South Traffic (LAN ↔ Internet)

- Traffic entering or leaving your network.
- This always crosses firewall, edge router, security inspection
- Common Protocols: HTTPS, DNS, SSH, VPN, SMTP, RDP
- Each has: **Ingress (incoming)** and **egress (outgoing)**

- Security Insight:
  - This is usually heavily monitored because data exfiltration happens here, malware C2 communication happens here, phishing downloads happen here

&nbsp;

### East–West Traffic (Internal ↔ Internal)

- Traffic that stays inside your LAN (or hybrid cloud LAN).
- Example: Workstation (File server), Host (Domain controller), App server (Database server)
- This is where lateral movement happens after compromise.

- And here’s the scary part: East–West traffic is often less monitored - Attackers love this.

&nbsp;

## FLOW Example

&nbsp;

### HTTPS with TLS Inspection (Proxy in the middle)

- Flow:
  Client > Proxy > Internet Server
  Server > Proxy > Client

- A host requests a website; this request is sent to the NGFW, which includes a web proxy. The web proxy will act as the web server and simultaneously establish a new TCP session with the actual web server and forward the clients' requests. When the web proxy receives the answer from the web server, it inspects its contents and then forwards it to the host if deemed safe. To summarize, we have two sessions, one between the client and the proxy and the other between the proxy and the web server. From the client's point of view, it has established a session with the web server.

![image](https://github.com/user-attachments/assets/a1332a81-578f-43ee-9b27-1c031e4c22c4)


- There are actually two TCP sessions: <br>
  \1. Client ↔ Proxy <br>
  \2. Proxy ↔ Web Server <br>

- The proxy decrypts, inspects content, re-encrypts, forwards if safe

- If malware downloads a ZIP file, the proxy can inspect it — but only if TLS inspection is enabled.

&nbsp;

### External DNS Flow

- Flow:
  Host > Internal DNS > Firewall > Internet DNS
  Internet DNS > Firewall > Internal DNS > Host

- DNS traffic within a corporate network starts when a host sends a DNS query. The host sends the query to the internal DNS server on port 53, which will then act on behalf of the host. First, it will check if it has an answer to the query in its cache; if not, it will send the query via the router, through the firewall, to the configured DNS servers. The answer will then follow the same path to the internal DNS server, which will then forward it to the host. The network diagram below shows a simplified flow.

![image](https://github.com/user-attachments/assets/cd4064e8-e2e2-41cf-97d1-bc2641455d86)

- The internal DNS server acts on behalf of the host, caches results and controls resolution

>[!CAUTION]
> DNS tunneling, beaconing, or strange domains can indicate malware.
> 

&nbsp;

### SMB + Kerberos (Internal Authentication Flow)

- Flow:
  Host > Domain Controller (Kerberos ticket)
  Host > File Server (SMB session using ticket)

![image](https://github.com/user-attachments/assets/b49ffcd6-d8e6-4155-8e44-4f14678c3e47)

- When a host opens a share to, for example, \\FILESERVER\MARKETING, an SMB session is set up. First, authentication is done via Kerberos. When a user logged in on the host, it authenticated with the Key Distribution Center on the Domain Controller and received a Ticket Granting Ticket to request "service authentication tickets". Now, the host requests a service ticket using the Ticket Granting Ticket it received earlier. The host then uses this ticket to establish the SMB connection. Once the SMB session is set up, the host can access the share. Below we see a simplified network diagram of the flow.


---  
><details><summary>❓Which category of devices generates the most traffic in a network?</summary>endpoint</details>
---  
><details><summary>❓Before an SMB session can be established, which service needs to be contacted first for authentication?</summary>kerberos</details>
---  
><details><summary>❓What does TLS stand for?</summary>Transport Layer Security</details>
---

&nbsp;

# How can we observe Network Traffic?

- Network traffic analysis is about combining multiple data sources, correlating them, finding patterns and turning observations into action

- There are three main ways to observe network traffic:
  \1. **Logs** <br>
  \2. **Full Packet Capture (FPC)** <br>
  \3. **Network Statistics (Flow Data)** <br>

- Each has strengths and limitations. Mature environments use all three together.

&nbsp;

## Logs – Your First Layer of Visibility

- Logs are usually the starting point in investigations.
- Every system and protocol has logging capabilities — but there is no universal logging standard
- Each vendor decides what to log, how to format it and which fields to include

- Vendors usually log:
  - Source IP
  - Destination IP
  - Source/destination ports
  - Protocol
  - Status codes
  - Usernames (if authentication)
  - Timestamps

- They do NOT log full packets - No full payload, No complete TCP headers, No fragment offsets, No full context

- example: **Linux authentication log Syslog format** and **Apache Access Log CLF format**:

![image](https://github.com/user-attachments/assets/43c302c4-b329-4d1b-8bb4-a8e4cb28de12)

- We can see user `gensane`, source IP `192.168.1.50`, port `52234`, service `SSH`, status `Accepted`, but we cannot see the password or packet contents.
- In the Apache log we can see client IP, timestamp, HTTP method, resource requested, response code (200), response size, user-agent, but we cannot see full HTTP headers or payload and file content.

&nbsp;

## Log Forwarding Protocols

- Even though logging formats differ, some protocols standardize log transport: Syslog and SNMP
- These send logs from devices to central collectors (SIEM, log server).

>[!CAUTION]
> If something suspicious happens and the logs don’t include the necessary fields, you must correlate multiple log sources, look at full packet capture and analyze flow statistics.
>
>Logs are visibility — but limited visibility.
>

&nbsp;

## Full Packet Capture (FPC)

- This is the most complete form of network observation.
- Instead of metadata, you capture full Ethernet frame, full IP header, full TCP/UDP header, full payload,... 

Everything.

- **This allows**:
  - Deep forensic analysis
  - Payload inspection
  - Sequence number tracking
  - Fragmentation detection
  - ARP poisoning detection
  - Session hijacking detection

&nbsp;

## How to Capture Full Packets

- There are two primary methods: a **TAP (Test Access Point)** and **Port Mirroring (SPAN)**

![image](https://github.com/user-attachments/assets/542f97db-2098-4dee-90b8-9188c16eebab)

&nbsp;

## Physical Network TAP

- A TAP (Test Access Point) is a physical inline device placed between two network devices and it copies traffic passively, then forwards a copy to a monitoring system
- it operates at link layer, no MAC address, no IP address
- Copies raw electrical/light signals
- it has near-zero performance impact
- Cannot be detected by attackers easily
- This is the gold standard for high-security environments.

&nbsp;

## Port Mirroring (SPAN)

- This is a software-based method.
- The switch duplicates packets from one interface to another.
- Example (Cisco SPAN):

![image](https://github.com/user-attachments/assets/3b425cf9-df07-448b-b07f-f2023a1501d1)

- meaning:
  - Traffic entering/exiting Fa0/1
  - Is duplicated
  - Sent to Fa0/2
  - Where monitoring device is connected


- Port Mirroring works on physical switches, virtual switches (e.g., VMware vSwitch), cloud environments (e.g., AWS VPC Traffic Mirroring)

&nbsp;

## TAP vs Mirroring

|Feature|TAP|Port Mirroring|
|--------------------|-------------------|----------|
|Performance impact|near zero|can impact performance|
|Detectable by attacker|very difficult|possibly|
|Hardware required|yes|no|
|Flexibility|physical placement needed|software configurable|

- High throughput mirrored ports can cause dropped packets or performance degradation

![image](https://github.com/user-attachments/assets/2cdd1dfa-3307-4171-991f-95bf9af9fe0f)

&nbsp;

## Best Practices for Full Packet Capture

1.Placement: 
  - You must choose capture location carefully:
    - Before firewall?
    - After firewall?
    - Between internal segments?
    - Near domain controllers?
  - Placement determines what you see.

2. Duration & Storage Requirements:
  - Full packet capture consumes massive storage.
  - Example: Capturing 1 Gbps continuously for 24 hours ≈ 10.8 TB
  - Now imagine: 10 Gbps or 40 Gbps
  - Storage planning is critical.

3. Network Statistics (Flow Data):
  - This is not full packets — it’s metadata about flows.
  - Instead of capturing every packet, we collect summaries like:
    - Source IP
    - Destination IP
    - Source port
    - Destination port
    - Protocol
    - Byte count
    - Packet count
    - Duration
   
## Tools for Packet Analysis

- Common tools: 
  - Wireshark (GUI, detailed analysis)
  - TCPdump (CLI capture tool)
  - Snort (IDS)
  - Suricata (IDS/IPS)
  - Zeek (network analysis framework)

- These tools allow packet inspection, filtering, protocol decoding or detection rules

## Network Statistics

- Great way to find anomalies in your network is to gather metadata about the data flowing through the network, such as counting the number of DNS requests that a host sends out.
- A few protocols facilitate this like **NetFlow** or **IPFIX** 

### NetFlow

- Developed by Cisco.
- Collects metadata about flows.
- Great for detecting Command & Control (C2),data exfiltration, lateral movement, beaconing patterns
- It does **NOT** capture payloads.

- NetFlow output example:
![img](https://github.com/user-attachments/assets/18e6e8cd-1fc7-4175-8ce3-5bd06cabb966)


### IPFIX

- The Internet Protocol Flow Information Export protocol
- Successor to NetFlow.
- Key differences: Vendor-neutral (IETF standard), more flexible field selection, templating support and extended metadata options

- NetFlow v9 introduced templating and IPFIX formalized and standardized it.

- To implement NetFlow or IPFIX, we don't need a whole new set of infrastructure or dedicated servers. Most vendors implement these protocols standard in their devices. We just have to enable and configure the protocol and have a place to send the metadata. You don't need a dedicated server for collecting this data; many NGFWs, IPS, and IDS have an implementation to collect and analyze flow data.


---  
><details><summary>❓What is the lag found in the HTTP traffic in scenario 1? The flag has the format THM{}</summary>THM{F*************E}</details>
<!-- THM{FoundTheMalware} -->
---  
><details><summary>❓What is the flag found in the DNS traffic in scenario 2? The flag has the format THM{}</summary>THM{C************d}</details>
<!-- THM{C2CommandFound} -->
---
