---
layout: default
title: Windows Memory and Processes
description: Analyze a memory dump of a Windows host and uncover malicious processes.
---

🔗 [Link to the Room](https://tryhackme.com/room/windowsmemoryandprocs)

# 🏷️ Table of Contents

1. [Scenario Information](#scenario-information)<br>
2. [Windows Process Architecture](#windows-process-architecture)<br>
   2.1 [Memory preparation process explained](#memory-preparation-process-explained)<br>
3. [Initial Triage of a New Memory Dump](#initial-triage-of-a-new-memory-dump)<br>
4. [Linking Processes](#linking-processes)<br>
   4.1 [Task](#task)<br>
   4.2. [Key Observations](#key-observations)<br>
5. [Digging Deeper](#digging-deeper)<br>
6. [Dumping the Process Memory](#dumping-the-process-memory)<br>
7. [Putting it all together](#putting-it-al-together)<br> 
  7.1 [Key Findings](#key-findings)<br> 
  7.2 [Reconstructed Attack Flow](#reconstructed-attack-fow)<br>

---

# 📚 Study Notes #

- Malware can run only in memory, so memory analysis is an important forensic skill.
- Tools like Volatility and Redline help extract data, but analysts must interpret and connect the findings themselves.

- This room focuses on **analyzing a Windows memory dump** to identify processes, investigate attacks, and report results.

# Scenario Information

- You are part of an incident response team investigating a possible compromise at TryHatMe, an online hat store.
- A full memory dump of a Windows host was already collected, and this case represents your first real investigation with guidance from a senior analyst.
- The incident (THM-0001) was escalated on May 5th, 2025, after a potentially compromised Windows system (WIN-001) was identified.
- A full memory dump was taken to preserve evidence, including host details, timestamp, and hash to ensure integrity.

<img width="5084" height="3320" alt="image" src="https://github.com/user-attachments/assets/3aea08b2-e4e5-49b1-9a8b-6f3059c35a10" />

# Windows Process Architecture

- When you open a program, Windows builds a few internal “records” so it can keep track of what the program is doing in memory.

- Windows uses four core structures (important pieces): EPROCESS, ETHREAD, PEB, and TEB.
  - `EPROCESS`: **is Windows’ main file for a program**. It tells the system: this program exists, here’s its ID, and here are its threads. It exists in kernel space.
  - `ETHREAD`: **represents a single task inside the program**. A program can have many threads, and each one does part of the work. It also exists in kernel space.
  - `PEB` [Process Environment Block]: **is the program’s settings and startup info**. It stores things like command-line arguments and loaded DLLs. PEB exists in user space.
  - `TEB` [Thread Environment Block]: **is the personal notebook for each thread**. It keeps thread-specific data like stack info and thread-local storage. It also exists in user space. 

- When a program starts, Windows creates all four structures as they are linked together with pointers, so Windows can jump between them easily.

# 📌 Memory preparation process explained 

1. **Memory is allocated for the program executable (EXE) as well as for internal Windows structures** like the **PEB** (process information) and the **TEB** (thread information). <br>
   Basically this means that before a program can run, Windows needs to set up space in RAM and organize it. Think of it like setting up a desk before you start working.
 - **Windows loads the program file (.exe) from disk into RAM**. The code and data inside the EXE are mapped into the process’s memory, which means that Windows now knows where the prtogram's code lives, where its data lives and where execution should start = *without this process the CPU has nothing to execute*.
2. **Windows creates a PEB** structure in user space. This **stores important process info** like cmd arguments, environment variables, loaded DLLs or debugging flags. The program uses this info while running and you probably won't even notice it.

3. Next one is **memory for the TEB**. Every thread gets its own TEB. Here **Windows allocates memory for thread's stack** (where function calls and local variables live), thread-local storage (TLS) and error handling data. Without TEB, a thread wouldn't know where its stack is or how to handle errors.

&nbsp;

>[!NOTE]  
>**Thread** is a single line of execution inside a program. In a simpler words: a **PROCESS is the the whole program** and a **THREAD is a worker inside that program doing tasks**.
>
>E.g. Your browser is one process; Loading a webpage, playing a video, responding to clicks = different threads.

&nbsp;

- **Only after this setup does the program actually start running.**

- Memory forensic tools rely on these structures to extract process data.

- Understanding these links helps identify malicious or hidden activity.

&nbsp;

---
><details><summary>❓ What field is used to keep track of all the active processes? Only enter the fields' name.</summary>ActiveProcessLinks</details>
>✅Solution: *This field inside the EPROCESS structure used to link all currently running processes together in a list. Windows then walks this list to know which processes are active.*

---  
><details><summary>❓What field is used to store the PID of a process? Only enter the fields' name. </summary> UniqueProcessId</details>
>✅Solution: *It's field inside the EPROCESS structure. Windows uses this value to uniquely identify each running process.*

---
&nbsp;  <!-- Toto pridáva prázdny riadok navyše -->

# Initial Triage of a New Memory Dump 

  - Within this task you'll **analyze a Windows memory dump** to find suspicious processes using **Volatility 3** on an Ubuntu VM.

  - **Steps**:<br>
     1\. **Verify the memory dump** - check MD5 hash to ensure integrity. <br>
     2\. **Extract processes using Volatility** - `windows.pslist` (active processes), `windows.psscan` (all processes, including terminated/unlinked), `windows.pstree` (processes with parent-child relationships), `windows.psxview` (cross-checks processes and threads for anomalies)<br>
     3\. **Analyze process output** - look for suspicious names, paths, or masquerading processes (e.g., `scvhost.exe`, `dockerupdater.exe`) and compare extracted processes to a baseline to identify unusual or unexpected processes.<br>
     4\. **Remove false positives** (legitimate processes missing from baseline, truncated names, etc.)<br>

- Now you have to investigate links between suspicious processes and other processes to uncover hidden malicious activity.

&nbsp;

---
><details><summary>❓What is the PID of the csrss.exe process that has 12 threads? You can use the pslist.txt file to find the answer. </summary>440</details>
>✅Solution: *The `pslist.txt` contains the output of Volatility's `Windows.pslist` module, which looks like a table. Search for `csrss.exe` in it. Search for the one that has 12 threads as Windows can have several csrss.exe threads running.*

--- 
><details><summary>❓What is the (memory) Offset(V) of the process with PID 5672? You can use the pslist.txt file to find the answer. </summary>0x990b29293080 </details>
>✅Solution: *In the `pslist.txt`* search for PID 5672 and look at the Offset(V) column, there's your answer.

---

&nbsp;

# Linking Processes 

- Before analyzing suspicious processes in detail, **check if they are connected to other processes**.

- Malware often runs as a chain of processes, where one process spawns another to carry out malicious activity.

e.g.: 
```explorer.exe → cmd.exe → powershell.exe → svchost.exe → asyncrat.exe```
A malicious LNK file triggers cmd.exe, which starts PowerShell to download the payload, svchost.exe is used to masquerade as a system process, and finally the malware (asyncrat.exe) runs.

&nbsp;

>[!NOTE]
> The **processtree.txt file maps parent-child process relationships** and can be searched by PID to identify the process name.

&nbsp;

## Task 

1. Generate a process tree using **Volatility**: `vol3 -f memory_dump windows.pstree > processtree.txt`
2. Simplify the tree to show PID, PPID, and ImageFileName: `cut -d$'\t' -f1,2,3 processtree.txt`

<img width="437" height="219" alt="image" src="https://github.com/user-attachments/assets/9a320d27-3280-4d58-ba94-a6ab560c6697" />

<img width="515" height="504" alt="image" src="https://github.com/user-attachments/assets/4dd3ba48-f2e0-4c4e-8b4b-9d9f633e141e" />

## Key Observations 

- Multiple "update” processes are chained unusually, suggesting suspicious activity.
- Presence of conhost.exe indicates possible network activity.
- The chain is highly likely malicious based on process names, hierarchy, and behavior.

&nbsp;

---
><details><summary>❓What is the parentID (PPID) of the services.exe (PID 664) process? Use the processtree.txt file to answer the question. </summary>524</details>
---
><details><summary>❓What is the ImageFileName of the process that has the PID 7788? Use the processtree.txt file to answer the question. </summary>FTK Imager.exe</details>
>✅Solution: *`processtree.txt` is the output of Volatility's `windows.pstree` module. Use `less processtree.txt` then scroll or use `/7788` to search.*

---

&nbsp;

>[!NOTE]
>*Use `pslist` to see what exists, and `pstree` to see how it happened.
>
> **pslist** - *What is running?* - **shows a flat list of processes found in memory**; focuses on PID, process name, thread count, memory offset - to identify suspicious process names, find PIDs, threads and offsets and to get an overview of running processes.
>  - e.g. "*What is the PID of* `csrss.exe` *with 12 threads*?" or "*What is the Offset(V) of PID 5672*?"
>    
> **pstree** - *Who spawned whom?* - **shows processes in a parent-child tree structure**; focuses on process relationships and execution chains - it's best used to reconstruct attack chains, see how malware was launched or identify suspicious parent-child behavior.
>  - e.g. "*Which process started* `powershell.exe`?" or "*What process is associated with PID 7788*?"

&nbsp;

# Digging Deeper 

- Not all malicious processes remain visible in active process lists.
- Attackers may hide, unlink, or terminate processes to evade detection.
- Volatility modules like psscan and psxview help uncover these hidden artifacts.

&nbsp;

>[!IMPORTANT]
>Here I recommend to follow steps on THM as I am not going to copy paste all of it here. These are only my summary notes.

&nbsp;

- **PSSCAN** – Finding Hidden or Terminated Processes
  - **Scans memory for all process structures**, including terminated processes, unlinked or hidden processes
  - **Method**: run `windows.psscan` and compare results with `windows.pslist` - processes found in psscan but not in pslist may require investigation.
  - **Result**: Several processes appeared only in psscan (e.g., `svchost.exe`, `ctfmon.exe`) but all identified processes were common Windows processes and appeared benign.
  - **Verification Checks**: 1. Confirm executable path (e.g., C:\Windows\System32\). 2. Review loaded DLLs for injection or hollowing. 3. Ensure active processes have at least one thread. 4. Check Exit Time consistency. 5. Dump and analyze process memory if needed.

- **PSXVIEW** – Cross-Referencing Process Visibility
  - **Uses multiple detection methods to cross-check process visibility**; highlights processes missing from `pslist` but present in other scans.
  - **Method**: Run `windows.psxview` and filter for entries where `pslist == False`.
  - **Result**: Processes flagged by psxview matched those seen in psscan; no strong indicators of process hiding or manipulation were found.

&nbsp;

- No additional malicious or hidden processes were identified using psscan or psxview.
- The investigation now shifts to deeper analysis of previously identified suspicious processes.

&nbsp;


| Tool |	What it actually does |	Simple way to think about it |	When to use it |
| ------- | ------------------------------------------------- | ------------------------------------------- | ---------------------- | 
| `pslist` |	Lists processes the OS currently knows about	| “What Windows says is running right now”	| Baseline view |
| `psscan` |	Scans raw memory for process remnants |	“Dig through memory rubble to find any process that ever existed” |	Finding hidden or terminated processes |
| `psxview` | Compares multiple process detection methods |	“Who shows up on some lists but not others?” |	Detecting stealthy or unlinked processes | 

&nbsp;

---
><details><summary>❓What is the number of processes that have 0 Threads? Use the psscan.txt file to answer the question. </summary>3</details>
---
><details><summary>❓What is the number of processes that have the Exit Time filled in? Use the psxview.txt file to answer the question. </summary>3</details>
---

&nbsp;

# Dumping the Process Memory 

- After identifying a suspicious process chain starting from WINWORD.EXE, the next step is to confirm malicious behavior by dumping and analyzing process memory using Volatility.
- Our goal is to extract executables and data from suspicious processes, identify indicators of compromise (IOCs) and collect artifacts for deeper malware analysis.

1. **Finding Executable Paths**  (`windows.dlllist`)
  - The `windows.dlllist` module is used to identify the main executable path and review loaded DLLs.
  - This helps determine whether a process is running from a legitimate system location or an unusual user-controlled path.
  - Key finding: `pdfupdater.exe`, `windows-update.exe`, and `updater.exe` all run from user directories, which is highly suspicious; `WINWORD.EXE` runs from a legitimate Office path.

2 . **Dumping Process Memory** (`windows.dumpfiles`)
  - Suspicious processes are dumped from memory to extract executables, embedded documents, and configuration or payload data.
  - Processes dumped: `WINWORD.EXE`, `pdfupdater.exe`, `windows-update.exe`, `updater.exe` (excluded: `cmd.exe`, `conhost.exe`, `powershell.exe` — better analyzed with other modules)

&nbsp;

Dumped File Types
| Type |	Description |
| ------------------- | -------------------------------------------------------------- |
| `ImageSectionObject` |	Mapped executable images (.exe, .dll, injected PE files) |
| `DataSectionObject` |	Data files (configs, logs, unpacked payloads, documents) |

&nbsp;

3. **Artifact Discovery**  
  - Macro-enabled Word documents found: `cv-resume-test.docm` and `Normal.dotm`
  - Macro files are commonly abused to execute malicious VBA code *(MITRE: T1059.005 – Visual Basic)*
  - Extracted executables and data files: `pdfupdater.exe`, `windows-update.exe`, `updater.exe` = These names imitate legitimate software but behave inconsistently with real update mechanisms.

4. **Key Conclusions**
  - Multiple executables masquerade as update processes.
  - All suspicious binaries originate from user-writable locations.
  - Microsoft Word likely acted as the initial infection vector via a malicious macro document.
  - The extracted executables require full malware analysis.

&nbsp;

---
><details><summary>❓What is the path of the process with PID 7788? </summary>C:\Program Files\AccessData\FTK Imager\FTK Imager.exe</details>
---
><details><summary>❓Dump the process with PID 7788. What is the name of the dumped file that represents the executable? </summary>file.0x990b2ae1ed40.0x990b29954a20.ImageSectionObject.FTK Imager.exe.img</details>
>✅Solution: While in the `~/7788` use command `ls | grep -E ".exe" -i`

---

&nbsp;

➡️ Next step: Static and dynamic malware analysis of the dumped executables.

&nbsp;

# Putting it all together 

- During the memory investigation, multiple suspicious processes and artifacts were discovered. While some findings still require deeper malware analysis, the collected evidence allows partial reconstruction of the attacker’s kill chain.

## Key Findings 

- A suspicious process chain starts with WINWORD.EXE
- Macro-enabled Word documents were found in memory: `cv-resume-test.docm` and `normal.dotm`
- Several fake “update” executables were launched from user directories
- One executable was placed in the `Startup folder`, suggesting persistence
- Multiple process memory dumps and executables were successfully extracted for further analysis

<img width="757" height="422" alt="image" src="https://github.com/user-attachments/assets/917c66c4-7a73-4f18-88b5-b20d89478d54" />

## Reconstructed Attack Flow 

&nbsp;

**1. Initial Access — Phishing**
  - Likely entry method: phishing email with malicious Word macro document
  - User opens the document → Word executes embedded macro code
  - MITRE: T1566 – Phishing

**2. Execution — Malicious Macro**
  - WINWORD.EXE opens macro-enabled document
  - Macro runs Visual Basic code
  - Downloads and launches pdfupdater.exe
  - MITRE: T1059.005 – Visual Basic Execution

**3. Persistence — Startup Folder Abuse**
  - `pdfupdater.exe` launches windows-update.exe
  - Located in user Startup folder `AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\` = this means it runs automatically at logon
  - MITRE: Boot or Logon Initialization – Startup Items

**4. Command & Control (Suspected)**
   - `windows-update.exe` likely acts as C2 client, reverse shell and downloader for next stages = needs deeper binary analysis to confirm

**5. Additional Stage — Unknown Role**
  - `updater.exe` launched by the suspected C2 component (exact purpose unclear yet)
  - Possible functions: data exfiltration, system discovery, lateral movement, payload staging
  - Further malware analysis required.

&nbsp;

>[!NOTE]
> | Code | Description |
> | ------------------------------------------ | -------------------------------- |
> | ``vol3 -f mem.mem windows.pslist > pslist.txt`` | list active processes |
> | ``vol3 -f mem.mem windows.pstree > processtree.txt`` | show process tree (parent/child relationship |
> | ``cut -d$'\t' -f1,2,3 processtree.txt`` | filter useful columns (PID / PPID / mame) |
> | ``vol3 -f mem.mem windows.psscan > psscan.txt`` | scan all process objects (even hidden/terminated) |
> | ``vol3 -f mem.mem windows.psxview > psxview.txt`` | cross-view process checks |
> | ``awk 'NR==3 \|\| $4 == "False"' psxview.txt`` | show only suspicious mismatches |
> | ``awk '{print $1,$3}' pslist.txt \| sort > pslist_processed.txt``
> | ``awk '{print $1,$3}' psscan.txt \| sort > psscan_processed.txt`` | compare pslist vs psscan (prep files) |
> | ``comm -23 psscan_processed.txt pslist_processed.txt`` | find processes missing from pslist |
> | ``awk '$5 == 0 {count++} END {print count}' psscan.txt`` | count processes with 0 threads from psscan) |
> | ``grep 7788 processtree.txt`` | find process by PID in pstree |
> | ``grep 5672 pslist.txt`` | find process by PID in pslist |
> | ``vol3 -f mem.mem windows.dlllist --pid PID > PID_dlllist.txt`` | list loaded DLLs + executable path |
> | ``cat 5252_dlllist.txt`` | to view use cat |
> | ``mkdir PID; cd PID; vol3 -f ../mem.mem windows.dumpfiles --pid PID`` | dump process files |
> | ``ls PID \| grep -E ".docm\|.dotm" -i`` | find macro Word files |
> | ``ls PID \| grep -E ".exe\|.dat" -i`` | find executables and data files |
> | ``file filename.dat`` | identify real file type |
> | ``strings suspicious_file.img \| less`` | extract readable strings |
> | ``strings file.img \| grep -i http`` | search inside strings |
> | ``strings file.img \| grep -i powershell`` | search inside strings |
> | ``strings file.img \| grep -i cmd`` | search inside strings |

&nbsp;

---
><details><summary>❓What is the name of the likely compromised user? </summary>operator</details>
---
><details><summary>❓What is the ID assigned to the MITRE Tactic Command and Control? </summary>TA0011</details>
---

&nbsp;
