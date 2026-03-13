---
layout: default
title: Supplemental Memory
description: Investigate lateral movement, credential theft, and additional adversary actions in a memory dump.
---
🔗 [Link to the Room](https://tryhackme.com/room/supplementalmemory)

## 🏷️ 

# 📚 Study Notes #

- As a DFIR team member, you are tasked with analyzing a Windows workstation memory image suspected of compromise.
- This room is intended to strengthen your memory analysis skills and better understand the value of memory dumps during incident response.
  
## Learning Objectives ##
- Uncover the TryHatMe breach with just a memory dump.
- Identify suspicious processes and network connections.
- Explore traces of execution and discovery actions.
- Detect signs of potential lateral movement and credential dumping.

## Scenario ##
- The CEO’s workstation was compromised, and an attacker stole Domain Admin credentials.
- Your task is to analyze a memory dump from a high-privilege workstation (WIN-015) to identify signs of lateral movement or data exfiltration.

&nbsp;

>[!NOTE]
>## 📌 Introduction to Volatility Plugin ##
> - Volatility is a memory forensics framework. <br>
> - Plugins are its built-in analysis commands, each designed to extract specific artifacts from memory. <br>
> - a **Volatility** plugin is a module that tells Volatility what kind of analysis to run on a memory dump. <br>
> - Example of what plugins can do: list running processes (pslist, pstree), find hidden or injected processes (psscan), extract network connections (netscan), dump credentials or hashes (lsadump, hashdump), ... <br>
> - Basically when someone says that he is using a Volatily plugin, it means that he's running a specific Volatily command to analyze one aspect of the memory image. <br>

&nbsp;

- in the room's example `vol -f WIN-015-20250522-111717.dmp windows.psscan` the `vol` is Volatility 3 command, `-f WIN-015-20250522-111717.dm` is the memory dump file you're analyzing and `windows.psscan` is the Volatility 3 plugin that's being used.
  
- `windows.psscan` scans memory for process objects (running or terminated processes, hidden or unlinked procesess often used by malware) >> this is useful when you suspect process hiding, `pslist` might miss something, or you are hunting for malicious activity.

  ##  Lateral Movement and Discovery ##

- as a next step you are going to prove or disprove the team's suspicions regarding traces of the threat actor's movement to the WIN-015 host.

### Use memory analysis to identify lateral movement techniques ###

< will add screenshots > 



&nbsp;

---

><details><summary>❓The IR team suspects that the threat actor may have performed lateral movement to this host. Which executed process provides evidence of this activity?</summary>wmiprvse.exe</details>
>✅Solution: Run (without brackets) [vol -f WIN-015-20250522-111717.dmp  windows.pstree > pstree.txt], [cat pstree.txt | awk '{ print $1, $2, $3, $4 }']

---

><details><summary>❓What is the MITRE technique ID associated with the lateral movement method used by the threat actor?</summary>T1021.006</details>
>✅Solution: https://attack.mitre.org/techniques/T1021/006/

---

><details><summary>❓Which other process was executed as part of the lateral movement activity to this host?</summary>TeamsView.exe</details>
>✅Solution: You'll find it in a previous output.

---

><details><summary>❓What is the Security Identifier (SID) of the user account under which the process was executed on this host?</summary>S-1-5-21-3147497877-3647478928-1701467185-1008</details>
>✅Solution: Run (without brackets) [vol -f WIN-015-20250522-111717.dmp windows.getsids > getsids.txt], [cat getsids.txt | grep TeamsView.exe]

---

><details><summary>❓What is the name of the domain-related security group the user account was a member of?</summary>Domain Users</details>
>✅Solution: You'll find it in a previous output.

---

><details><summary>❓Which processes related to discovery activity were executed by the threat actor on this host? Format: In alphabetical order</summary>ipconfig.exe, systeminfo.exe, whoami.exe</details>
>✅Solution: cat precooked/pslist.txt | grep 1672

---

><details><summary>❓What is the Command and Control IP address that the threat actor connected to from this host as a result of the previously executed actions? Format: IP Address:Port</summary>34.244.169.133:1995</details>
>✅Solution: cat precooked/netscan.txt | grep TeamsView

---

&nbsp;


 ##  Privilege Escalation and Credential Dumping ##

 - Check service-related processes for unusual behavior: Attackers may abuse misconfigured services to gain higher privileges.
 - Look at which users are running suspicious processes: Unexpected or high-privilege users can indicate misuse.
   
 - In the room you can see examples of how to identify potential signs of privilege escalation through memory analysis:
   - ***Detection of Malicious Process Execution*** 543mal.exe by running `vol -f apt41.dmp windows.pstree`
   - ***Detecting privilege levels of malicious execution*** by running `vol -f apt41.dmp windows.getsids --pid 1612`
   - ***Detecting privilege escalation via service*** by running `vol -f apt41.dmp windows.pstree`
   - ***Detecting privilege levels of malicious execution*** by running `vol -f apt41.dmp windows.getsids --pid 1531`
  - In the example, the attacker initially operated under the user account michael.brown, executing 543mal.exe as a reverse shell, and later escalated privileges to svc_backup.
  
  - The privilege escalation was achieved through service manipulation, exploiting misconfigured services present on the system and as a result, up.exe was executed.

&nbsp;

---

><details><summary>❓Conduct a deeper investigation and identify another suspicious process on the host. Provide a full path to the process in your answer. </summary>C:\Windows\Temp\pan.exe</details>
>✅Solution: cat precooked/cmdline.txt and search for pan.exe

---

><details><summary>❓Which account was used to execute this malicious process? </summary>Local System</details>
>✅Solution: cat getsids.txt | grep 4840 

---

><details><summary>❓What was the malicious command line executed by the process?</summary>privilege::debug sekurlsa::logonpasswords</details>
>✅Solution: cat precooked/cmdline.txt | grep 4840

---

><details><summary>❓Given the command line from the previous question, which well-known hacker tool is most likely the malicious process?</summary>Mimikatz</details>
>✅Solution: You have to search web for "*which hacker tool uses privilege::debug to enable debug privileges*" or "*Which hacker tool uses sekurlsa::logonpasswords" for dumping credentials from LSASS*" 

---

><details><summary>❓Which MITRE ATT&CK technique ID corresponds to the method the attacker employed to evade detection, as identified in the previous steps?</summary>T1036</details>
>✅Solution: https://attack.mitre.org/techniques/T1036/

---

&nbsp;


## Summary ##
- The attacker moved through the network, gained higher privileges, and stole credentials. 
- Access keys may also have been taken, but this needs further investigation. 
- The breach is more serious than first thought, and significant work is needed to assess the damage and restore normal operations.

**Recap of what you covered:**
- Detected lateral movement activity through memory artefacts.
- Investigated malicious process execution and system discovery behaviour.
- Uncovered privilege escalation and credential dumping indicators.

## ✨ Bonus ##
- After finishing this room you'll receive **Memory Sletuh** Badge!
    
    <img width="590" height="266" alt="image" src="https://github.com/user-attachments/assets/449ed6e9-edcb-4073-9a7a-46fb068f4c8d" />
