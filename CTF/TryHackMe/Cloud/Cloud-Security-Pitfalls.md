---
layout: default
title: Cloud Security Pitfalls
description: Explore the risks companies face when migrating to the cloud, and learn how to address them in a SOC.
---

🔗 [Link to the Room](https://tryhackme.com/room/cloudsecuritypitfalls)


# 📚 Study Notes #

- Many companies move their systems from on-premises to the cloud to save money and improve stability and security. However, the cloud also brings new security risks. If these risks are not understood, cloud systems can be less secure than before.
- This beginner room explains common cloud security risks and mistakes. It also shows how a SOC analyst can help protect cloud environments.

# Learning Objectives 

- Learn the **cloud models**: **IaaS, PaaS, SaaS**
- Understand basic cloud security risks
- Learn the basics of cloud security
- Understand why monitoring cloud systems is challenging for SOC teams

# What Is Cloud 

- The **cloud means using computers and services over the internet** instead of owning your own servers.
- You don’t buy or maintain hardware, you just use it and pay for what you need.
- e.g. AWS, Google Drive

☁️ **IaaS – You manage the OS**

- **Infrastructure as a Service**
- You get a virtual computer, then you install and manage the operating system and software.
- The cloud provider manages the hardware.
- e.g. AWS, Azure, Google Cloud
- Basically it's like renting an empty apartment, you only bring the furniture.

☁️ **PaaS – You write code**

- **Platform as a Service**
- You write code, Click deploy, the provider handles servers, OS, and setup
- Examples: Vercel, Heroku, Google App Engine, TryHackMe rooms
- It's like renting a furnished apartment, you just move in.

☁️ **SaaS – You just use it**

- **Software as a Service**
- Ready-to-use software; no installation; works in a browser;
- e.g. Google Docs. Gmail, Dropbox, Slack
- Basically like staying in a hotel when everything is done for you.

&nbsp;

>[!NOTE]
>
>✅ **IaaS** → you manage most things 
>
>✅ **PaaS** → you manage code only 
>
>✅ **SaaS** → you manage nothing 

&nbsp;

---
><details><summary>❓Which cloud model allows you to migrate a big on-premises network to the cloud?</summary>IaaS</details>
**Solution**: This is mentioned in the text as well as you can easily google it :)
---
><details><summary>❓Which cloud model do Elastic Cloud and CrowdStrike Falcon fit into? Note: You may need to perform external research to answer this question. </summary>SaaS</details>
**Solution**: Same as previous one - search for it on the web.
---

&nbsp;

# Security of the Cloud 

- The cloud uses the same tech as on-prem systems (Linux, TCP/IP, etc.).
- The cloud is not magic — it’s basically someone else’s computer.
- Cloud systems can be attacked, just like local computers.
- Security of the provider’s infrastructure is called “Security of the cloud”.


## Risk: Cloud Provider Vulnerabilities 

- Big providers (AWS, Google Cloud) are well secured, but not perfect - If breached, attackers often target large customers = This is a supply chain risk.

- **Always apply basic defenses**:
  - Network segmentation
  - Login monitoring
  - Endpoint monitoring
  - Smaller or less-known providers are higher risk

- Past incidents show:
  - Malware deployed to all VMs (IaaS breaches)
  - Sensitive data leaked (SaaS breaches)

- Choose cloud providers carefully and be careful what data you trust them with.


# Risk: Poor Visibility in the Cloud 

- Users cannot see inside the cloud provider’s internal systems
- Some attacks happen entirely **inside SaaS** infrastructure
- SOC teams cannot access provider internal logs, this makes detection very difficult

&nbsp;

> **SaaS risks**: token theft, data exfiltration, shadow IT (employees using unapproved SaaS tools) especially this can lead to unexpected data breaches.

&nbsp;

## Example Cloud Incidents 

**1. Okta** (SaaS, 2023): Support system breached; session tokens exposed; attackers logged into customer tenants. <br>
**2. BeyondTrust** (SaaS, 2024): Remote Support SaaS compromised; attackers gained remote access to customers. <br>
**3. Google Cloud** (IaaS, 2025): Cloud Run vulnerability; unauthorized access to container images. <br>

&nbsp;

>***Cloud ≠ fully safe*** <br>
>**You trade control and visibility for convenience.**

&nbsp;

---
><details><summary>❓Is the cloud provider responsible for securing and monitoring its own infrastructure (Yea/Nay)? </summary> Yea </details>
**Solution**: Cloud providers are responsible for securing and monitoring their own infrastructure (physical data centers, HW, networking, Virtualization layer) = this is called "Security of the cloud". You as a customer are responsible for what you put in.
---
><details><summary>❓But should you trust the cloud provider without watching for supply chain threats? (Yea/Nay)</summary> Nay </details>
**Solution**: Cloud providers can be breached and that would affect many customers at once.
---

&nbsp;

# Security in the Cloud 

- You are responsible for securing your cloud resources: VMs (IaaS), Apps (PaaS), SaaS accounts & credentials.
- Treat cloud resources like on-prem systems → monitor & harden them.

## Cloud Migration Pitfalls 

- Moving old VMs or files to the cloud doesn’t make them secure as cloud has new, cloud-specific threats.
- On-premises security practices often don’t work in the cloud.
- Example: Weak passwords without MFA are dangerous in public clouds.

## Logging in the Cloud 

- You can’t install your usual SIEM tools in the cloud like you do on-premises. Instead, you have to use the cloud provider’s logging tools (e.g., AWS CloudTrail)

- Problems you might face with cloud logs:
  - Extra cost – some logs require paid plans
  - Messy or incomplete logs – not all info is there or easy to read
  - Hard to connect to your SIEM, especially for SaaS apps

## Cloud Incidents 

- Capital One (AWS misconfig) → 1M Social Insurance Numbers stolen
- Various SaaS breaches → caused by weak passwords, leaked API keys, stolen cookies → attackers steal data

&nbsp;

---
><details><summary>❓Does moving an unpatched server to the cloud make it secure again? (Yea/Nay) </summary> Nay </details>
**Solution**: Moving an old, unpatched server to the cloud doesn't fix its vulnerabilities as the server has the same security flaws as it had.
---
&nbsp;

>**Cloud ≠ automatic security — it helps with availability and hardware, but doesn't fix your mistakes.**

&nbsp;

---
><details><summary>❓What is the first major obstacle to integrating most cloud products with a SIEM?  </summary> Paid Logs </details>
**Solution**: Many cloud providers charge extra to export logs or use advanced logging features and if your SIEM relies on these logs, you can’t collect everything for free.
---

&nbsp;

# What to Protect & Monitor

- **SaaS** (easiest) - ingest logs via API, watch for risky actions (e.g., public docs, suspicious logins, data exports)
- **IaaS** (harder) - monitor: Workloads (VMs, containers), cloud services (databases, storage), control plane (cloud admin console logins/actions)

## Cloud Monitoring Challenges

- On-premises tools (EDR, SIEM) often don’t work the same in the cloud
- **Cloud limitations**: Containers & auto-scaling (EDR not supported), SIEM integration may be tricky, forensics limited (memory/disk access)
- **Logging volume**: On-prem > IaaS > SaaS = this means the **amount of logs** you get **decreases** as you move **from on-premises systems to cloud services**.

&nbsp;

|Model|Logging Capabilities|
|:------------:|------------------------------------------------------------|
| **On-premises** |you control everything, you can log everything from servers, apps, network, endpoints|
|**IaaS**| you control the VMs and workloads, but the cloud provider controls the underlying infrastructure, hence fewer logs available|
|**SaaS**| you only see user activity and app-level events which means least amount of logs|

&nbsp;

>**The less control you have, the less logging data you get → harder to monitor everything.**

&nbsp;

## Common Cloud Security Tools 

|tool|what does it do|
|------|------------------------------------|
|**CASB**| enforce cloud security policies|
|**CWPP**| protect workloads from malware|
|**CSPM**| alert on misconfigurations|

- Even without these, SIEM + logs = decent SOC coverage

## Recommended Actions

**1. List your clouds**: know where critical data lives <br>
**2. Know the risks**: plan for possible cloud breaches <br>
**3. Enable cloud logs**: SaaS + IaaS + PaaS <br>
**4. Enable workload logs**: treat VMs like on-premises systems <br>
**5. Collect the logs**: forward to SIEM (cloud logs aren’t kept long) <br>
**6. Monitor for anomalies**: detect suspicious logins and admin actions <br>

&nbsp;

---
><details><summary>❓What term describes cloud compute resources like VMs or containers? </summary> Workloads </details>
---
><details><summary>❓Which of the mentioned cloud security tools do Falco and Tetragon fit into? Note: You may need to perform external research to answer this question.</summary> CWPP</details>
---

&nbsp;

# Challenge 

- In this challenge you need to visit static site to get two flags. Happy exercising!

&nbsp;

---
><details><summary>❓What is the flag you get after completing the first exercise?</summary>THM{****_**_*_********}</details>
---
><details><summary>❓What is the flag you get after completing the second exercise?</summary>THM{*****_***_*****_**********}</details>
---

&nbsp;

# Summary 
- Learned IaaS, PaaS, SaaS differences
- Discovered cloud risks and migration pitfalls
- Saw that protecting cloud resources is challenging
- Logging and SOC monitoring in the cloud is harder than on-prem
