---
layout: default
title: TShark The Basics
description: Learn the basics of TShark and take your protocol and PCAP analysis skills a step further.
---

🔗 [Link to the Room](https://tryhackme.com/room/tsharkthebasics)

# 🏷️ Table of Contents

1. [Command-Line Packet Analysis Hints](#command-line-packet-analysis-hints)<br>
2. [TShark Fundamentals I](#tshark-fundamentals-i)<br>
  2.1 [Sniffing](#sniffing)<br>
3. [TShark Fundamentals I](#tshark-fundamentals-i)<br>
  3.1 [Reading capture files](#reading-capture-files)<br>
  3.2 [Writing capture data](#writing-capture-data)<br>
  3.3 [Viewing packet bytes](#viewing-packet-bytes)<br>
  3.4 [Verbose packet analysis](#verbose-packet-analysis)<br>
4. [TShark Fundamentals II](#tshark-fundamentals-II)<br>
5. [Packet Filtering Parameters](#packet-filtering-parameters)<br>
  5.1 [Capture filters (live filtering)](#capture-filters-live-filtering)<br>
  5.2 [Display filters (post-capture filtering)](#display-filters-post-capture-filtering)<br>
6. [TShark Fundamentals IV](#tshark-fundamentals-iv)<br>
  6.1 [Type qualifiers](#type-qualifiers)<br>
  6.2 [Direction qualifiers](#direction-qualifiers)<br>
  6.3 [Protocol qualifiers](#protocol-qualifiers)<br>
7. [TShark Fundamentals V](#tshark-fundamentals-v)<br>
  7.1 [IP filtering](#ip-filtering)<br>
  7.2 [TCP filtering](#tcp-filtering)<br>
  7.3 [HTTP filtering](#http-filtering)<br>
  7.4 [DNS filtering](#dns-filtering)<br>

---

# 📚 Study Notes #
- TShark is an open-source, command-line network traffic analyzer developed by the Wireshark team. 
- It offers most of Wireshark’s features but runs entirely in the terminal, making it ideal for scripting, automation, and detailed packet analysis. 
- TShark can be used both as a CLI version of Wireshark and similarly to tcpdump, with stronger filtering and inspection capabilities.

# Command-Line Packet Analysis Hints 
**TShark and Supplemental CLI Tools**

- TShark is a command-line (text-based) tool, which makes it well suited for deep packet analysis, data extraction, and automation using scripts. 
- One of its biggest advantages is that its output can be easily combined (piped) with other command-line tools to process traffic efficiently.

- Common command-line tools used alongside TShark include:

|Tool|Purpose|
|----------|-------------------------------------------------------------------------------------------------------|
|capinfos|Displays summary information about a capture file and is usually the first step before analysis|
|grep|Searches for specific text patterns|
|cut|Extracts specific parts of each line|
|uniq|Removes duplicate lines or values|
|nl|Adds line numbers to output|
|sed|Edits and transforms text streams|
|awk|Performs advanced pattern matching and data processing|

- Before starting analysis, follow the instruction in the room - review the capture file using `capinfos`. 
- This provides key details such as packet count, capture duration, timestamps, packet sizes, data rates, file hashes, and interface information, helping analysts understand the capture’s scope before deeper investigation.

![image](https://github.com/user-attachments/assets/0b887d06-6c40-4612-ad24-d304610249ca)

&nbsp;

---
><details><summary>❓View the details of the demo.pcapng file with "capinfos". What is the "RIPEMD160" value?</summary>6ef5f0c165a1db4a3cad3116b0c5bcc0cf6b9ab7</details>
>✅Solution: In [~/Desktop/exercise-files] run [capinfos demo.pcapng] and search for [RIPEMD160].
---

&nbsp;

# TShark Fundamentals I 
**Main Parameters I**

TShark is a command-line (text-based) network analysis tool, which makes it well suited for structured, step-by-step packet analysis. It includes many built-in parameters that help analysts control what information is displayed. Learning these parameters is important to avoid being overwhelmed by TShark’s very detailed output. Superuser (sudo) privileges are required to sniff live traffic and to list available network interfaces.

Common TShark parameters:

|Parameter|Purpose|
|:------------:|-----------------------------------------------------|
|**-h**|Displays the help page with commonly used options|
|**-v**|Shows the TShark version information|
|**-D**|Lists all available network interfaces|
|**-i**|Specifies which interface to use for live traffic capture|
|**No parameter**|Starts sniffing traffic using the default interface (similar to tcpdump)|


## Sniffing

- TShark supports live packet sniffing, which is one of its core features.
- A system may have multiple network interfaces, each serving different purposes, so selecting the correct interface is important for accurate traffic capture.

![image](https://github.com/user-attachments/assets/0304ef24-07ea-4039-a744-021345f50e37)

- When no interface is specified, TShark automatically uses the first available interface (usually interface 1).
- Running TShark without parameters is equivalent to using -i 1.
- Users can select a different interface using the -i option, and TShark always displays the interface being used at the start of the capture.

![image](https://github.com/user-attachments/assets/585ee1c9-5a54-4740-853a-e1e0e55c4484)

- Overall, understanding interfaces and basic parameters allows analysts to control live traffic capture effectively and perform focused network analysis.

---
><details><summary>❓What is the installed TShark version in the given VM?</summary>3.2.3</details>
>✅Solution: Run command [tshark -v]
---
><details><summary>❓List the available interfaces with TShark. What is the number of available interfaces in the given VM?</summary>12</details>
>✅Solution: List interfaces with [sudo tshark -D]
---

# TShark Fundamentals I 
**Main Parameters II**

TShark provides several important parameters that help analysts read, limit, save, and inspect packet data efficiently. These options are essential for controlling output and focusing on relevant traffic during analysis.

Key parameters:

|Parameter|Purpose|
|:--:|-----------------------------------------------------------------------------------------------------|
|**-r**|Reads packets from a capture (PCAP) file instead of live traffic|
|**-c**|Limits the number of packets processed or displayed|
|**-w**|Writes captured or filtered packets to a new PCAP file|
|**-V**|Enables verbose output with full packet details (similar to Wireshark’s Packet Details pane)|
|**-q**|Quiet mode; suppresses packet output in the terminal|
|**-x**|Displays packet contents in hex and ASCII format|

## Reading capture files
- TShark can analyze existing PCAP files using the `-r` option.
- The number of displayed packets can be limited with `-c`, which helps reduce output and focus on specific packets.
![image](https://github.com/user-attachments/assets/2c7529f1-5dba-499c-8d61-eed3c1593271)

## Writing capture data
- Using the `-w` option, TShark can save captured or filtered packets to a new file.
- This is useful for isolating suspicious traffic, performing further analysis later, or sharing relevant data with other investigators.
![image](https://github.com/user-attachments/assets/efb71607-3833-4b3c-96c1-3b9f405e1794)

## Viewing packet bytes
- The `-x` option shows packet data in hex and ASCII format.
- Because this produces large and detailed output, it is most effective after filtering or reducing the number of packets.
![image](https://github.com/user-attachments/assets/0fa94f22-eac8-461d-9641-86375aba6b71)

## Verbose packet analysis
- By default, TShark displays one-line summaries for each packet.
- The -V option enables full packet details, providing deep protocol-level information.
- While verbose mode is powerful, it generates long output and is best used after filtering specific packets rather than on large packet sets.
![image](https://github.com/user-attachments/assets/7f4ab4b0-43ff-4b43-a277-c2589c79eb99)

Overall, these parameters highlight TShark’s strength as a command-line alternative to Wireshark, offering strong capabilities for detailed analysis, scripting, and automation when used efficiently.

![image](https://github.com/user-attachments/assets/aeaf9053-713b-4261-90c3-4124d3163146)


---
><details><summary>❓Read the "demo.pcapng" file with TShark. What are the assigned TCP flags in the 29th packet?</summary>PSH, ACK</details>
>✅Solution: Run [tshark -r demo.pcapng]
---
><details><summary>❓What is the "Ack" value of the 25th packet?</summary>12421</details>
>✅Solution: Run [tshark -r demo.pcapng -T fields -e tcp.ack -Y frame.number==25]
---
><details><summary>❓What is the "Window size value" of the 9th packet?</summary>9660</details>
>✅Solution: Run command [tshark -r demo.pcapng -T fields -e tcp.window_size -Y frame.number==9]
---

# TShark Fundamentals II 
**Capture Conditions**

- TShark allows analysts to control how long traffic is captured and how capture files are created.
- These capture condition parameters are used only during live packet sniffing and cannot be applied when reading existing PCAP files.

- There are two main types of capture condition parameters: **autostop** and **ring buffer** options.

- **Autostop parameters (-a)**: Autostop options define conditions for a single capture run and stop the capture once the condition is met.
  - **duration** – Stops capturing after a specified number of seconds: `tshark -w test.pcap -a duration:1`
  - **filesize** – Stops capturing after the capture file reaches a defined size (KB): `tshark -w test.pcap -a filesize:10`
  - **files** – Stops capturing after a specified number of output files are created: `tshark -w test.pcap -a filesize:10 -a files:3`

- **Ring buffer parameters (-b)**: Ring buffer options create an infinite capture loop and continuously write traffic to files.
  - **duration** – Creates a new file after a set number of seconds: `tshark -w test.pcap -b duration:1`
  - **filesize** – Creates a new file after reaching a specified file size (KB): `tshark -w test.pcap -b filesize:10`
  - **files** – Limits the number of files and overwrites the oldest file once the limit is reachedL `tshark -w test.pcap -b filesize:10 -b files:3`

>[!IMPORTANT]
>Capture condition parameters only work in live capture mode
>They are used to control file size, duration, and file rotation during sniffing
>To extract or filter packets from an existing PCAP file, read and write options must be used instead
>Autostop (-a) and ring buffer (-b) parameters can be combined
?Infinite loops created with ring buffer options must include at least one autostop condition to stop the capture safely

![image](https://github.com/user-attachments/assets/dd16647a-5077-4d30-8482-fdd9b9331279)

- Overall, these parameters are useful for managing long or high-volume captures by automatically splitting traffic into organized, size-controlled files during live analysis.

---
><details><summary>❓Which parameter can help analysts to create a continuous capture dump?</summary>-b</details>
---
><details><summary>❓Can we combine autostop and ring buffer parameters with TShark? y/n</summary>y</details>
---

# Packet Filtering Parameters 
**Capture and Display Filters**
- TShark supports two types of packet filtering: **capture filtering** and **display filtering**.
- These filters work at different stages of packet analysis and serve different purposes.

## Capture filters (live filtering):
C- apture filters are applied before or during live traffic capture. 
Their goal is to save only specific types of traffic into the capture file. 
Once the capture starts, these filters cannot be changed. 
Capture filters use **Berkeley Packet Filter (BPF)** syntax and provide basic filtering based on protocol, IP ranges, ports, and traffic direction. 
This helps limit file size and keep capture data focused.

## Display filters (post-capture filtering):
- Display filters are applied **after traffic has been captured**.
- They do not modify the capture file but instead control which packets are visible during analysis.
- Display filters use Wireshark display filter syntax and allow detailed, flexible, and changeable packet investigation.

**TShark filtering parameters:**

|Parameter|Purpose|
|:--:|----------------------------------------------------------------------|
|-f|Capture filters (BPF syntax, same as Wireshark capture filters)|
|-Y|Display filters (same syntax as Wireshark display filters)|

- Because TShark is the command-line version of Wireshark, it uses different filter parameters depending on whether filtering is done during capture or after capture.
- Capture filters define the scope of collected traffic, while display filters are used for in-depth analysis without altering the captured packets.

---
><details><summary>❓Which parameter is used to set "Capture Filters"?</summary>-f</details>
---
><details><summary>❓Which parameter is used to set "Display Filters"?</summary>-Y</details>
---

# TShark Fundamentals IV 
**Packet Filtering Options: Capture Filters**

- TShark uses Wireshark capture filter syntax, which is based on Berkeley Packet Filters (BPF).
- Capture filters are applied before live traffic capture and determine which packets are saved.
- Boolean operators (such as and, or, not) can also be used to build more precise filters.

- Capture filters are built using three main qualifiers: type, direction, and protocol.

## Type qualifiers
- Define what kind of target is being filtered. 
- If no type is specified, host is used by default.

- **host** – Filter by a specific IP address or hostname - `tshark -f "host 10.10.10.10"`
- **net** – Filter by a network range (CIDR) - `tshark -f "net 10.10.10.0/24"`
- **port** – Filter a single port - `tshark -f "port 80"`
- **portrange** – Filter a range of ports - `tshark -f "portrange 80-100"`

## Direction qualifiers
- Define the traffic flow.
- If not specified, traffic from both directions is captured.

- **src** – Source traffic only - `tshark -f "src host 10.10.10.10"`
- **dst** – Destination traffic only - `tshark -f "dst host 10.10.10.10"`

## Protocol qualifiers
- Filter traffic by protocol type.

- Common protocols: arp, ether, icmp, ip, ip6, tcp, udp
  - **Filtering TCP** - `tshark -f "tcp"
- **MAC address filtering** can be done using ether host - `tshark -f "ether host F8:DB:C5:A2:5D:81"`
- **Protocols** can also be filtered using IANA IP protocol numbers (e.g., ICMP = 1) - `tshark -f "ip proto 1"`

Testing capture filters:
- Traffic can be generated using tools like curl (HTTP traffic) and netcat (TCP/UDP traffic) while TShark captures packets in real time. 
- Using split-terminal tools like `Terminator` makes it easier to generate traffic and observe captured packets simultaneously.
![image](https://github.com/user-attachments/assets/efc0caba-bdeb-4e35-9da6-da9538991cc6)

Common capture filter use cases:

|Capture Filter Category|Details|Commands example|
|:--------------:|---------------------------------------------------------------|------------------------------------|
|**Host filtering**|Capture traffic to or from a specific host|curl tryhackme.com; tshark -f "host tryhackme.com"
|**IP filtering**|Capture traffic related to a specific IP address|nc 10.10.10.10 4444 -vw 5; tshark -f "host 10.10.10.10"|
|**Port filtering**|Capture traffic on a specific port|nc 10.10.10.10 -vw 5l tshark -f "port 4444"|
|**Protocol filtering**|Capture traffic using a specific protocol|nc -u 10.10.10.10 4444 -vw 5; tshark -f "udp"|

- Overall, capture filters are used to reduce noise during live captures by collecting only relevant traffic, keeping capture files smaller and more focused for later analysis.

---
><details><summary>❓What is the number of packets with SYN bytes?</summary>2</details>
>✅Solution: Run command [tshark -r demo.pcapng -Y “tcp.flags.syn == 1” | wc -l]
---
><details><summary>❓What is the number of packets sent to the IP address "10.10.10.10"?</summary>7</details>
>✅Solution: Run command [tshark -r demo.pcapng -Y “ip.dst == 10.10.10.10” | wc -l]
---
><details><summary>❓What is the number of packets with ACK bytes?</summary>8</details>
>✅Solution: Run command [tshark -r demo.pcapng -Y “tcp.flags.ack == 1” | wc -l]
---

# TShark Fundamentals V 
Packet Filtering Options: Display Filters

- TShark uses **Wireshark display filter syntax** for post-capture packet analysis.
- Display filters are applied **after traffic has been captured** and are used to reduce the number of visible packets without modifying the capture file.
- Boolean operators can be used to create precise and flexible filters.

- Display filters allow detailed investigation of packets by protocol, IP address, ports, and protocol-specific fields.
- Reference resources such as Wireshark’s Display Filter Reference and the Display Filter Expression menu help identify valid filter fields.

## Common display filter examples:

## IP filtering
- Filter by IP address (any direction) - `tshark -Y 'ip.addr == 10.10.10.10'`
- Filter by network range - `tshark -Y 'ip.addr == 10.10.10.0/24'`
- Filter by source - `tshark -Y 'ip.src == 10.10.10.10'`
- Filter by destination IP - `tshark -Y 'ip.dst == 10.10.10.10'`

## TCP filtering
- Filter by TCP port - `tshark -Y 'tcp.port == 80'`
- Filter by source port - `tshark -Y 'tcp.srcport == 80'`

## HTTP filtering
- Filter all HTTP traffic (packets) - `tshark -Y 'http'`
- Filter HTTP responses by status code (e.g., 200) - `tshark -Y "http.response.code == 200"`

## DNS filtering
- Filter all DNS traffic (packets) - `tshark -Y 'dns'`
- Filter specific DNS query types (e.g., A records) - `tshark -Y 'dns.qry.type == 1'`

>[!IMPORTANT]
>TShark does not renumber packets after filtering.
>It keeps the original packet numbers from the capture file and only displays packets that match the filter.
>This can make it confusing to determine how many packets matched the filter.
>To solve this, the `nl` command can be used to add line numbers to the filtered output, making it easy to count the total number of displayed packets.

![image](https://github.com/user-attachments/assets/f29c67ed-f201-4421-9aeb-1783765b529c)

- Look at the above example. There are two matched packets, but the associated numbers don't start from zero or one; "13" and "17" are assigned to these filtered packets. Keeping track of these numbers and calculating the "total number of filtered packets" can be confusing if your filter retrieves more than a handful of packets. Another example is shown below.

![image](https://github.com/user-attachments/assets/e305f466-f460-4ace-95c9-b359ab801389)

- The usage of the nl command is shown below.

![image](https://github.com/user-attachments/assets/f9dddd90-270c-4f79-8797-83e75fe04dd1)

- Overall, display filters are a powerful way to analyze captured traffic in detail, offering flexibility similar to Wireshark’s GUI while remaining well suited for command-line workflows and scripting.

---
><details><summary>❓What is the number of packets with a "65.208.228.223" IP address?</summary>34</details>
>✅Solution: Run command [tshark -r demo.pcapng -Y “ip.addr == 65.208.228.223” | wc -l]
---
><details><summary>❓What is the number of packets with a "TCP port 3371"?</summary>7</details>
>✅Solution: Run command [tshark -r demo.pcapng -Y “tcp.port == 3371” | wc -l]
---
><details><summary>❓What is the number of packets with a "145.254.160.237" IP address as a source address?</summary>20</details>
>✅Solution: Run command [tshark -r demo.pcapng -Y “ip.src == 145.254.160.237” | wc -l]
---
><details><summary>❓Rerun the previous query and look at the output. What is the packet number of the "Duplicate" packet?</summary>37</details>
>✅Solution: Run command [tshark -r demo.pcapng -Y ‘ip.src == 145.254.160.237 and udp.analysis.duplicate’ -T fields -e frame.number]
