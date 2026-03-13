---
layout: default
title: Cloud Computing Fundamentals
description: Discover how cloud computing helps businesses move faster, do more, and scale with less effort.
---

🔗 [Link to the Room](https://tryhackme.com/room/cloudcomputingfundamentals)

# 📚 Study Notes #

### Why use the cloud
- Moves apps and files from a single device to online access anywhere.
- Makes apps more reliable, accessible, and scalable.

### Server Evolution to Cloud
- Cloud didn’t appear suddenly; it evolved from physical servers → virtualized servers → cloud.
- Goal is to reduce costs, use resources efficiently, and simplify scaling.

![image](https://github.com/user-attachments/assets/2e9beba0-410e-4247-b81a-6bf9e71fbdde)

### Cloud Benefits & Characteristics
- **Scalability**: Easily adjust resources as needed.
- **On-demand self-service**: Create/remove servers or storage instantly.
- **Pay-as-you-go**: Pay only for what you use.
- **Security**: Providers protect infrastructure.
- **High availability**: Apps run even if part of the system fails.
- **Global access**: Users can access apps anywhere.

### Types of Cloud Deployment
- **Public Cloud**: Affordable, easy to scale, no infrastructure management; ideal for startups and global apps.
- **Private Cloud**: Greater control and compliance; used by banks, healthcare, governments.
- **Hybrid Cloud**: Combines public and private; good for sensitive data + scalability.

### Cloud Service Models
- **IaaS** (Infrastructure as a Service): Rent servers/storage; you manage OS & apps.
- **PaaS** (Platform as a Service): Provider manages OS/infrastructure; you focus on app development.
- **SaaS** (Software as a Service): Complete apps provided online; provider handles everything (e.g., Gmail, Zoom).

![image](https://github.com/user-attachments/assets/7b7e7253-48bd-4a6c-8728-bf6428d31ecc)

### Major Cloud Vendors
- **AWS**: Industry leader, global reach, extensive services.
- **Microsoft Azure**: Strong in enterprise & hybrid cloud.
- **Google Cloud Platform (GCP)**: Data analytics, AI/ML tools.
- **Alibaba Cloud**: Major in Asia, global services.
- **IBM Cloud**: Hybrid & AI-focused.
- **Oracle Cloud**: Enterprise apps & databases.

### Cloud in Action (Examples)
- **Netflix**: Scales globally, handles peak demand.
- **Spotify**: Manages millions of songs & users, scales quickly.
- **Instagram**: Stores & delivers massive media worldwide.
- **E-commerce sites**: Handle traffic spikes without permanent infrastructure.

### Key Idea:
- Cloud allows companies to scale easily, reduce costs, stay reliable, and focus on products rather than hardware.
- You can apply the same approach to deploy your cybersecurity training app in a simulated cloud.

---
><details><summary>❓What is the characteristic of cloud environments that enables you to handle an unexpected increase in access to your application?</summary>Scalability</details>
---
><details><summary>❓What is the most common type of cloud deployment used?</summary>Public Cloud</details>
---
><details><summary>❓Suppose you want to deploy an application to the internet, focusing only on application development and leaving infrastructure to others. What type of cloud service is the best?</summary>PaaS</details>
---

## Deploying a Cloud Instance

- **Basic Cloud Terminology** (AWS)
  - **EC2 (Elastic Compute Cloud)**:
    - A virtual computer/server in the cloud.
    - Has CPU and RAM like a physical computer.
    - Each EC2 instance = one virtual computer added to your environment.
  - **Instance Type (e.g., t2, t3, m5)**:
    - Determines the power of the virtual computer (CPU & RAM).
    - Bigger instances: more power, higher cost.
    - Smaller instances: less power, lower cost.

### Deploying your environment

- in this room you will create three virtual computers (EC2 instances) - this aligns with the IaaS model.

1. **Pick a Region** where your resources will live.
![image](https://github.com/user-attachments/assets/61fbba1d-9453-4428-b58b-b6ca5e394b8b)

2. **Create Virtual Machine** with instance type`t3.micro`
![image](https://github.com/user-attachments/assets/0cec242f-c495-48d8-83ad-d87fa0a0bd33)

3. **Create another two machines** with instance type `m5.large`
![image](https://github.com/user-attachments/assets/aa3d4c8b-8d89-40c7-9c75-6bbf2f816a13)

4. **Do Billing Analysis** by checking how much each type of instance is costing you.
![image](https://github.com/user-attachments/assets/485a70e2-d46a-4f9b-8270-17eaa6641e86)

5. **Reduce costs by stoping instances** by: Go to the `instances` block and click the `stop` button for both `study-machine-1` and `study-machine-2`
![image](https://github.com/user-attachments/assets/acb6288e-c667-44de-a323-7d32296eaa95)
![image](https://github.com/user-attachments/assets/e0125713-2650-40ef-84a0-0f955e5b234a)


---
><details><summary>❓What is the total cost of credits of the entire environment if study-machine-1 and study-machine-2  are stopped?</summary>30</details>
---
><details><summary>❓How many credits does an m5.large EC2 instance cost per month?</summary>70</details>
---
><details><summary>❓What is the total cost of credits if only the new instances we created are running?</summary>150</details>
---
><details><summary>❓What would be the total running cost of the environment if a third t3a.small study machine (study-machine-3) were added alongside study-machine-1 and study-machine-2?</summary>188</details>
---                                

>[!NOTE]
>**Key Terminology:** <br>
>**Public Cloud** - Cloud services you access over the internet that many people and companies share. <br>
>**Private Cloud** - A cloud built just for one company, so they have more control and security. <br>
>**Hybrid Cloud** - A mix of public and private clouds that can work together and share data. <br>
>**IaaS** - A service where you rent basic computer parts like servers and storage from the cloud. <br>
>**PaaS** - A service that gives you a ready-to-use environment to build and run apps without managing servers. <br>
>**SaaS** - Software you use online without installing anything, like Gmail or Zoom. <br>
>**EC2** - Amazon’s cloud computers that you can quickly create, use, and resize whenever you need them. <br>
