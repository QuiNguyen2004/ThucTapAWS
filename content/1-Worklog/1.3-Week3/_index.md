---
title: "Week 3 Worklog"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:

* Connect and get acquainted with members of First Cloud AI Journey.
* Understand basic AWS services, how to use the console & CLI.

### Tasks to be implemented this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| :---: | :--- | :---: | :---: | :--- |
| **1** | - Learn the overview of Amazon VPC (Virtual Private Cloud) and CIDR blocks.<br>- Study Subnet partitioning models across Availability Zones. | 14/05/2026 | 14/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **2** | - Study Module 3: AWS Networking.<br>- Differentiate between Public Subnets and Private Subnets.<br>- Understand the working mechanisms of Internet Gateways (IGW) and NAT Gateways. | 15/05/2026 | 15/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **3** | - Study network traffic routing mechanisms.<br>- Learn how to configure and associate Route Tables for Subnets.<br>- Compare security firewalls: Security Groups (Stateful) vs. Network ACLs (Stateless). | 16/05/2026 | 16/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **4** | - **Hands-on Lab:**<br>&emsp; + Create and configure a custom VPC from scratch.<br>&emsp; + Set up Public Subnets, Route Tables, and attach an Internet Gateway.<br>&emsp; + Relaunch the *Employee Directory* application into the newly configured VPC. | 17/05/2026 | 17/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **5** | - Consolidate AWS networking knowledge.<br>- Complete the **Module 3 Knowledge Check** and earn the AWS Technical Essentials certificate. | 18/05/2026 | 18/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

---

### Learning Process and Knowledge Accumulation:

The focus of this week is the transition from creating servers (Compute) to building a secure "home" (Network) for them. The learning process centers on understanding how network components communicate and protect each other in a cloud environment.

**1. Learning Approach:**
* **Layered Approach:** Approaching knowledge from the outside in: Region -> VPC -> Availability Zone -> Subnet -> EC2 Instance.
* **Security First Mindset:** Always asking "How to secure this?" at each network component (VPC, Subnet, Instance).
* **Purposeful Practice:** The lab *Relaunching the Employee Directory Application* is not just about recreating the app, but placing it into a network environment designed and controlled by oneself.

**2. Key Takeaways from Module 3:**

* **Amazon VPC (Virtual Private Cloud):** This is a virtual network dedicated to your AWS account. It is logically isolated from other virtual networks in the AWS cloud, giving you complete control over your virtual networking environment (choosing IP ranges, creating subnets, configuring route tables).
![Amazon VPC Architecture](/ThucTapAWS/images/Worklog/AmazonVPC.jpg)
* **Subnetting & Network Partitioning:**
    * *Public Subnet:* Where resources that need direct Internet access are placed (e.g., Web Servers, Load Balancers). Must be associated with a route pointing to an Internet Gateway.
    * *Private Subnet:* Where internal resources are placed, not allowing direct access from the Internet (e.g., Databases, Backend Apps). For a Private Subnet to connect to the Internet securely (e.g., to download updates), a NAT Gateway is required.
    ![Creating a subnet](/ThucTapAWS/images/Worklog/Creatingsubnet.jpg)
* **Routing:** *Route Tables* act as the routing brain of the network. They contain rules (routes) that determine where network traffic from a subnet or gateway is directed.
* **VPC Security:**
    * *Security Groups (SG):* Operate at the instance level (EC2 instances). They are stateful - if an inbound request is allowed, the corresponding outbound response is automatically allowed.
    * *Network Access Control Lists (NACLs):* Operate at the subnet level. They are stateless - you must explicitly configure both inbound and outbound rules.

### Week 3 Achievements:

* Successfully completed all theory and the **Module 3: AWS Networking** knowledge check.
* Mastered the operational mechanism of Amazon VPC and how to allocate resources according to a Multi-AZ architecture.
* Know how to design and configure a network (Subnets, Route Tables, Internet Gateways) to route traffic accurately.
* Clearly understand the differences and how to combine Security Groups and Network ACLs to create multi-layered network defense.
* Successfully relaunched the "Employee Directory" application within a customized network EC2 environment, combining Compute knowledge from week 2 and Networking knowledge from week 3.
![Chứng chỉ](/ThucTapAWS/images/Worklog/AWSTechnicalEssentials.jpg)