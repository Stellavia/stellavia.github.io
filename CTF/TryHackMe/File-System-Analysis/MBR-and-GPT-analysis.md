---
layout: default
title: MBR and GPT analysis
description: Learn how MBR and GPT forensics are carried out to identify attacks during the boot process.
---

🔗 [Link to the Room](https://tryhackme.com/room/mbrandgptanalysis)

## 🏷️ Table of Contents

1. [Introduction](#introduction)<br>
2. [Boot Process](#boot-process)<br>
  2.1 [Power-On the System](#power-on-the-system)<br>
  2.2 [Power-On-Self-Test (POST)](#power-on-self-test-post)<br>
  2.3 [Locate the Bootable Device](#locate-the-bootable-device)<br>
3. [What if MBR?](#what-if-mbr)<br>
  3.1 [Analyzing the MBR](#analyzing-the-mbr)<br>
  3.2 [Bootloader Code (Bytes 0-445)](#bootloader-code-bytes-0-445)<br>
  3.3 [Partitions Table (Bytes 446-509)](#partitions-table-bytes-446-509)<br>
  3.4 [MBR Signature (Bytes 510-511)](#mbr-signature-bytes-510-511)<br>
4. [Threats Targeting MBR](#threats-targeting-mbr)<br>
5. [MBR Tampering Case](#mbr-tampering-case)<br>
  5.1 [Scenario](#scenario)<br>
  5.2 [Instructions](#instructions)<br>
6. [What if GPT?](#what-if-gpt)<br>
  6.1 [Protective MBR](#protective-mbr)<br>
  6.2 [Primary GPT Header](#primary-gpt-header)<br>
  6.3 [Partition Entry Array](#partition-entry-array)<br>
  6.4 [Backup GPT Header](#backup-gpt-header)<br>
  6.5 [Backup Partition Entry Array](#backup-partition-entry-array)<br>
7. [Threats Targeting GPT](#threats-targeting-gpt)<br>
8. [UEFI Bootkit Case](#uefi-bootkit-case)<br>

---

# 📚Introduction

&nbsp;

Think of a computer’s disk like a large building that stores information.
Computers store data as binary (1s and 0s), but without organization it would quickly become chaotic.

To keep things organized, the disk is divided into partitions, similar to rooms in a building. Each partition stores different types of data. For example:
- One partition might store the operating system
- Another might store personal files
- Another might store applications

In Windows, these partitions appear as drive letters such as C:, D:, or E:. Other operating systems may show them differently.

<img width="658" height="800" alt="image" src="https://github.com/user-attachments/assets/dbae413c-52bb-4f7a-931c-70d475687a7a" />

Even with partitions, the computer still needs a map that tells it:
- Where each partition starts and ends
- What partitions exist on the disk
- How to access them

This map is created using partitioning schemes such as **Master Boot Record (MBR)** and **GUID Partition Table (GPT)**

These act like a blueprint of the building, describing all the rooms (partitions).

The MBR or GPT is stored in the **very first sector of the disk**, and it is very important because it also **helps start the computer during the boot process**.


Because MBR and GPT are involved in starting the system, attackers sometimes target them.
Examples of attacks include **bootkits** (malware hidden in the boot process) and **tampering with the partition table** to make the system unable to start

&nbsp;

---
><details><summary>❓What are the separate sections on a disk known as?</summary>partitions</details>
---
><details><summary>❓Which type of malware infects the boot process?</summary>bootkits</details>
---

&nbsp;

# Boot Process


The boot process is what happens when you turn on a computer. <br>
It is the sequence of steps the computer follows to get everything ready so you can use it.

Think of it like waking up in the morning and getting ready for the day.

1. The computer powers on and checks its hardware (like the CPU, memory, and storage).<br>
2. It loads the operating system (such as Microsoft Windows or Linux) into memory.<br>
3. Once everything is ready, the system shows the login screen or desktop, allowing the user to interact with it.<br>

In simple terms, the boot process **prepares the computer so it can run the operating system and accept user commands**.

<img width="450" height="1140" alt="image" src="https://github.com/user-attachments/assets/1a2942cf-b60e-4934-8ece-3a6fe4003c80" />

&nbsp;

## Power-On the System

The boot process begins when you press the power button on your computer.

When this happens:
1. Electricity flows to the motherboard, which connects all the computer’s components.
2. The CPU (the computer’s main processor) receives power first.
3. The CPU then looks for instructions on what to do next.

These instructions are stored in special firmware on the motherboard called either **BIOS (Basic Input/Output System)** or **UEFI (Unified Extensible Firmware Interface)**.

Their job is to **start the computer and check that all hardware is working properly** before the operating system loads.

Both BIOS and UEFI start the computer, but UEFI is the newer and more advanced version.

| Feature | BIOS | UEFI |
|--------|------|------|
| Age | Older technology used for decades | Modern replacement |
| Disk support | Up to about 2 TB | Up to 9 zettabytes (extremely large) |
| Boot security | Basic | Includes Secure Boot for better security |
| Partition system | Uses MBR | Uses GPT |



On Microsoft Windows, you can check easily what the system uses (if BIOS or UEFI firmware):
1. Press Windows + R
2. Type msinfo32
3. Press Enter
4. Look for BIOS Mode

Possible results: **Legacy** (your system uses BIOS) or **UEFI** (your system uses UEFI firmware)

<img width="2312" height="1288" alt="image" src="https://github.com/user-attachments/assets/a8656cd0-5711-4174-9511-77d0a9f3d883" />

>[!NOTE]
>When a computer powers on, BIOS or UEFI acts like the system’s startup manager.
It checks the hardware and begins the process of loading the operating system.
>

&nbsp;

## Power-On-Self-Test (POST)

After the computer powers on and the firmware starts running, the system performs a **Power-On Self-Test (POST)**.

**POST** is a quick hardware check performed by the computer’s startup firmware such as BIOS or UEFI.

Its job is to make sure that the main hardware components are working correctly before the operating system loads.

During POST, the computer checks things like:
- Memory (RAM)
- Keyboard
- Storage devices\
- Other essential hardware

Sometimes you may hear one or more beeps during this process. These sounds are beep codes, which the system uses to signal hardware problems.

For example a missing keyboard might trigger a message like “Keyboard not found” or multiple beeps can indicate hardware errors

<img width="1140" height="800" alt="image" src="https://github.com/user-attachments/assets/c6b86c67-edd2-4bf7-a532-9b4a2ccac5b7" />

&nbsp;

## Locate the Bootable Device

After the computer checks its hardware with the Power-On Self-Test (POST), the firmware (such as BIOS or UEFI) looks for a bootable device.

A bootable device is a storage device that contains an operating system the computer can start, e.g. SSD, HDD, USB drive

Once the system finds a bootable device, it begins reading the very first part of that disk.

The **first sector of the disk** contains important information about how the disk is organized.
This information is stored using one of two partitioning systems **Master Boot Record (MBR)** and **GUID Partition Table (GPT)**

These act like a **map of the disk**, showing:
- Where partitions start and end
- Which partition contains the operating system
- How the boot process should continue

Once this information is read, MBR or GPT takes control of the next stage of the boot process.

<img width="1140" height="800" alt="image" src="https://github.com/user-attachments/assets/6139d916-d8cb-4720-81b2-2a6df677be5c" />

On Microsoft Windows, you can check the disk partition type (whether disk is using MBR or GPT) with PowerShell terminal: `Get-Disk`

>[!NOTE]
>At this stage of the boot process, the system finds the building and reads its map — which is either MBR or GPT — so it knows where the operating system is located.
>

&nbsp;

---
><details><summary>❓What is the name of the hardware diagnostic check performed during the boot process?</summary>Power-On-Self-Test</details>
---
><details><summary>❓Which firmware supports a GPT partitioning scheme?</summary>UEFI</details>
---
><details><summary>❓Which device has the operating system to boot the system?</summary>bootable device</details>
---

&nbsp;

# What if MBR?

&nbsp;

This is a time to start VM 

<img width="373" height="139" alt="image" src="https://github.com/user-attachments/assets/300984f7-94e8-4dc2-ba4a-f9f14b00494b" />

&nbsp;

## Analyzing the MBR

In the previous step of the boot process, the computer found the bootable disk. Now the system reads the very first part of that disk, called the **Master Boot Record (MBR)**.

Although many modern systems now use **GUID Partition Table (GPT)**, MBR is still important to understand because some systems still rely on it and it is often analyzed during digital forensics or malware investigations.

A disk is divided into small units called sectors where each sector typically stores 512 bytes of data.
The **first sector of the disk** contains the MBR - **MBR is always located in Sector 0**.

<img width="452" height="160" alt="image" src="https://github.com/user-attachments/assets/b7b28c20-111f-4fb2-b573-889cf9dda6b7" />

Investigators, analysts and us in this case can examine the MBR by opening a disk image in a hexadecimal editor such as HxD (available in the taskbar of the attached machine).

A hex editor displays raw disk data in hexadecimal format, which computers use internally.

Open the HxD tool > click the File button in the options tab > select Open from the options > input the file's location to be opened - in our case it's `C:\Analysis\MBR` - select this file to examine the MBR:

<img width="2318" height="1278" alt="image" src="https://github.com/user-attachments/assets/48cb1d41-3b73-4112-aeda-368b138ad7ce" />

This will open it in hexadecimal format (your MBR will be different).

<img width="1288" height="1072" alt="image" src="https://github.com/user-attachments/assets/e14db907-6178-40e0-a8f9-32f73814c051" />

We can easily analyze the MBR code by starting from the first line, but how do we know where this MBR code ends? The answer to this question is straightforward. Every two digits coupled in hexadecimal represents 1 byte, and once the first 512 bytes of the disk completes, the MBR has been ended. So, in the hexadecimal editor we are using, 16 bytes are present in each row, meaning that the first 32 rows of the disk would be the whole MBR. Another way to spot the end is by looking at the MBR signature. The MBR signature is represented by 55 AA, which marks the end of the MBR code. You can look for these hexadecimal digits to identify where the MBR ends.

Hence: 512 bytes ÷ 16 bytes per row = 32 rows
- The MBR is exactly 512 bytes long.
- Every two hexadecimal characters = 1 byte
- Hex editors often show 16 bytes per row
- This means the first 32 rows contain the entire MBR
- The end of the MBR is marked by a special signature `55 AA`

<img width="1262" height="1206" alt="image" src="https://github.com/user-attachments/assets/4d5f89c2-1a60-45c2-b4b3-4abc936296ca" />

1. highlighted portion represents the bytes offset
2. represents the actual hexadecimal bytes
3. represents the ASCII-converted text of the hexadecimal bytes

Every two hexadecimal digits present a single byte. These 512 bytes of the MBR are further divided into three portions. The following screenshot highlights each of the three portions of the MBR with different colors.
<img width="1266" height="1080" alt="image" src="https://github.com/user-attachments/assets/f81670c2-d547-4883-922e-32950bf12182" />

Structure of the MBR - The 512 bytes of the MBR are divided into three main sections:
<img width="1140" height="800" alt="image" src="https://github.com/user-attachments/assets/3ee850e0-59e9-47ab-9803-a2dbbb1e1525" />
1. **Boot Code** - Instructions used to start the operating system
2. **Partition Table** - Information about disk partitions
3. **MBR Signature** - Marks the end of the MBR (55 AA)

The MBR is very important because it controls the early stage of the boot process.

Attackers sometimes modify it to install bootkits, hide malware or prevent the system from booting.

Because of this, security analysts and forensic investigators often examine the MBR when investigating compromised systems.

>[!NOTE]
>The MBR is the first 512 bytes of a disk and contains the instructions and information needed to start the operating system and locate disk partitions.
>

&nbsp;

## Bootloader Code (Bytes 0-445)

The first component of the MBR is the Bootloader code. They cover 446 out of the total 512 bytes of the MBR:
<img width="2110" height="1596" alt="image" src="https://github.com/user-attachments/assets/75fb1463-c418-45d4-8280-1428b08168c9" />

Its main job is to start the boot process by finding the bootable partition on the disk.

Think of it like a navigator: as soon as the computer starts, the bootloader looks at the disk’s partition table and decides which partition has the operating system to load next.

The initial bootloader is the first thing executed in the MBR.
It works hand-in-hand with the partition table to continue the boot process.
For advanced study, this code can be disassembled into assembly language, but that’s more advanced than needed here.

>[!NOTE]
>The Bootloader code is like the first guide the computer uses to locate the operating system and continue the booting process.
>

&nbsp;

## Partitions Table (Bytes 446-509)

The second part of the MBR is the Partition Table, which occupies 64 bytes (from byte 446 to 509).

Its purpose is to **describe all the partitions on the disk** and tell the bootloader where to find the bootable partition.

How it Works in the Boot Process
1. The initial bootloader (first 446 bytes) runs.
2. It looks at the partition table to find the bootable partition.
3. It then loads the second bootloader from that partition.
4. The second bootloader finally loads the operating system kernel.


Structure of the Partition Table - total: 4 × 16 bytes = 64 bytes.
- An MBR disk can have up to 4 partitions.
- Each partition uses 16 bytes in the table 
- Four partitions (each with 16 bytes) are highlighted with different colors:
- 
<img width="1232" height="166" alt="image" src="https://github.com/user-attachments/assets/a93a4014-b363-416a-a9a6-ddf35c39e4a7" />

The table contains details like:
- Partition type (bootable or not)
- Start and end addresses of the partition
- Size of the partition
- 
<img width="1732" height="996" alt="image" src="https://github.com/user-attachments/assets/59ef389a-9a1f-41ed-a46e-36a9dca3cd09" />

>[!NOTE]
>The partition table is like a map of the building, helping the bootloader locate the room (partition) containing the operating system.
>

The screenshot below shows the first partition from the partition table of the MBR. All the bytes have some specific meaning. Some bytes individually represent a field, while others are grouped together to form a field. We have highlighted all these different byte groups with different colors. 

<img width="1256" height="70" alt="image" src="https://github.com/user-attachments/assets/021f10b3-0e60-4dcb-8f73-71d3ce759893" />

The table below shows the fields represented by these bytes. 


| Bytes Position | Bytes Length | Bytes         | Field Name        |
|----------------|-------------|---------------|--------------------|
| 0              | 1           | 80            | Boot Indicator      |
| 1–3            | 3           | 20 21 00      | Starting CHS Address|
| 4              | 1           | 07            | Partition Type      |
| 5–7            | 3           | FE FF FF      | Ending CHS Address  |
| 8–11           | 4           | 00 08 00 00   | Starting LBA Address|
| 12–15          | 4           | 00 B0 23 03   | Number of Sectors   |


1. **Boot Indicator** – Tells if the partition can start the computer. <br>
  - `80` = bootable (usually the C: drive in Windows) <br>
  - `00` = not bootable <br>
  ><details><summary>more</summary>This byte tells you whether the partition is bootable or not. A bootable partition contains files necessary for the operating system to boot. This boot indicator can only have one of the two values: 80 or 00. If it's 80, it means that the partition is bootable; else, if it's 00, it means that the partition is not bootable.  In Windows based systems, C: is the partition that is typically bootable. If visualized through the partition table, this partition would have the boot indicator set to 80.</details>
2. **Starting CHS Address** – Shows the physical start of the partition on the disk. Mostly outdated, we now use LBA instead.<br>
  ><details><summary>more</summary>Cylinder Head Sector (CHS) is the 3 bytes that tell you where this partition is starting from on the disk. It will give you the starting physical address of the partition, such as the cylinder, head, and sector number. This field is not that important as we now have the simplified logical address of the partition in the form of the Starting LBA Address discussed ahead.</details>
3. **Partition Type** – Shows the filesystem of the partition (e.g., NTFS, FAT32).<br>
  ><details><summary>more</summary>Every partition uses a filesystem such as NTFS, FAT32, etc. This byte indicates the filesystem of the partition. The partition we are taking as a reference has this byte as 07, which means it is an NTFS partition. Every filesystem has its own unique byte.</details>
4. **Ending CHS Address** – Shows the physical end of the partition. Also mostly replaced by LBA.<br>
  ><details><summary>more</summary>The last 3 bytes at the end of the CHS Address indicates the physical location where the partition ends on the disk. This field is also not that important, just like the stated CHS address, as we mostly use the logical address (LBA) instead of the physical address.</details>
5. **Starting LBA Address** – The logical start of the partition, easier to use for locating it on the disk.<br>
  ><details><summary>more</summary>ogical Block Addressing (LBA) is the logical address that indicates the start of the partition. We saw that the Starting CHS Address also gives us the starting address of the partition, but because CHS gives you the physical address of the partition, it becomes difficult for us to locate it. However, the Starting LBA Address gives you the logical address of the partition rather than the physical address. You can use it to easily find the start of the partition on the disk in a hexadecimal editor. By locating the partition on the disk, you can also carve data from a disk's hidden or deleted partitions. Based on the partition we are analyzing as a reference with 00 08 00 00 LBA, we will use this logical address to locate our partition later in this task.</details>
6. **Number of Sectors** – Tells how big the partition is, in terms of disk sectors.



>[!NOTE]
>These fields help the computer know which partition to boot from, where it starts, and how large it is, and they are also useful in forensics or recovering deleted data.
>












&nbsp;

## MBR Signature (Bytes 510-511)







&nbsp;

---
><details><summary>❓Which component of the MBR contains the details of all the partitions present on the disk?</summary>partition  table</details>
---
><details><summary>❓What is the standard sector size of a disk in bytes?</summary>512</details>
---
><details><summary>❓Which component of the MBR is responsible for finding the bootable partition?</summary>bootloader code</details>
---
><details><summary>❓What is the magic number inside the MBR?</summary>55 AA</details>
---
><details><summary>❓What is the maximum number of partitions MBR can support?</summary>4</details>
---
><details><summary>❓What is the size of the second partition in hte MBR file found in C:\Analysis\MBR\ ? (rounded to the nearest GB)</summary>16</details>
---

&nbsp;

# Threats Targeting MBR

&nbsp;




<!-- NO QUESTIONS -->





&nbsp;

# MBR Tampering Case

&nbsp;





## Scenario



## Instructions





&nbsp;

---
><details><summary>❓How many partitions are on the disk?</summary>1</details>
---
><details><summary>❓What is the first byte at the starting LBA of the partition? (represented by two hexadecimal digits)</summary>EB</details>
---
><details><summary>❓What is the type of the partition? </summary>NTFS</details>
---
><details><summary>❓What is the size of the partition? (rounded to the nearest GB)</summary>32</details>
---
><details><summary>❓What is the flag hidden in the Administrator's Documents folder?</summary>THM{C***_T**_M**}</details>
---

&nbsp;

# What if GPT?

&nbsp;




## Protective MBR



## Primary GPT Header


## Partition Entry Array


## Backup GPT Header



## Backup Partition Entry Array




&nbsp;

---
><details><summary>❓How many partitions are supported by the GPT?</summary>128</details>
---

<!-- ADD ANSWER -->

><details><summary>❓What is the partition type GUID of the 2nd partition given in the attached GPT file?</summary></details>
---

&nbsp;

# Threats Targeting GPT

&nbsp;




<!-- NO QUESTIONS -->



&nbsp;

# UEFI Bootkit Case

&nbsp;


---
><details><summary>❓Which partition has the bootloader in it?</summary>EFI System Partition</details>
---
><details><summary>❓What is the malicious string embedded in the bootloader?</summary>Hello, EFI Bootkit!</details>
---

&nbsp;
