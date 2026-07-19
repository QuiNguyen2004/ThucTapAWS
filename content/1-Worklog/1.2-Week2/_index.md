---
title: "Week 2 Worklog"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---


### Week 2 Objectives:

* Connect and get acquainted with members of First Cloud AI Journey.
* Understand basic AWS services, how to use the console & CLI.

### Tasks to be implemented this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| :---: | :--- | :---: | :---: | :--- |
| **1** | - Learn Cloud Deployment Models (Public, Private, Hybrid Cloud).<br>- Study AWS Global Infrastructure (Regions & Availability Zones). | 07/05/2026 | 07/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **2** | - Study Module 1: AWS Foundations & Security.<br>- Understand the Shared Responsibility Model.<br>- Configure IAM Security: Create IAM Users, enable MFA, and apply the Least Privilege principle. | 08/05/2026 | 08/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **3** | - Study Module 2: AWS Compute Services.<br>- Deep dive into Amazon EC2: Instance types, AMIs, EBS, and the EC2 Lifecycle. | 09/05/2026 | 09/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **4** | - **Hands-on Lab:**<br>&emsp; + Launch an EC2 virtual server.<br>&emsp; + Establish a remote SSH connection to the instance.<br>&emsp; + Deploy the Employee Directory sample application. | 10/05/2026 | 10/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **5** | - Research the evolution of cloud compute:<br>&emsp; + Containers (AWS Fargate).<br>&emsp; + Serverless & Event-driven computing (AWS Lambda).<br>- Practice Use-case analysis to select the appropriate Compute solution (Right-sizing). | 11/05/2026 | 11/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

---

### Process:
* Understanding cloud computing deployment models
  * Cloud computing allows developers and IT departments to focus on what matters most by avoiding tasks like procurement, maintenance, and capacity planning. As cloud computing has grown in popularity, several different deployment strategies have emerged to meet the specific needs of different users. Each type of deployment method provides you with different levels of control, flexibility, and management. Understanding the differences between these deployment strategies can help you decide which set of services is right for your needs.
  ![Cloud computing deployment models](/images/Worklog/mohinhdientoandammay.jpg)
  
**1. Learning Approach:**
* **Visual Learning:** Carefully analyzing architectural diagrams, such as the "Cloud Computing Deployment Models" diagram, to clearly see the differences in control, flexibility, and resource management.
* **Use-case Driven:** Instead of just reading theory, I followed the actual deployment flow of the sample "Employee Directory" application. This helped me understand exactly which AWS services are required to build a real-world application.
* **Compare and Contrast:** Continuously asking comparative questions between services (e.g., When to use EC2 vs. when to use Lambda?) to hone my decision-making skills in solution architecture.

**2. Key Takeaways:**

* **From Module 1: AWS Foundation & Security**
    * **Shared Responsibility Model:** This is the guiding principle for security. AWS protects the physical infrastructure ("Security **OF** the Cloud"), while I (the user) am responsible for securing my data, firewall configurations, and source code ("Security **IN** the Cloud").
    * **Identity and Access Management (IAM):** The golden rule is to **never** use the Root account for daily tasks. Always create an IAM User, enable MFA (Multi-Factor Authentication), and grant permissions based on the "Principle of Least Privilege".

* **From Module 2: AWS Compute Services**
    * **Amazon EC2 (Virtual Servers):** It's not just about creating a virtual machine; understanding the "EC2 Lifecycle"—from Launch to Stop, and finally Terminate—is crucial for cost optimization.
    * **The Evolution of Compute (Containers & Serverless):**
        * *Containers (AWS Fargate):* Packaging applications to run consistently anywhere without worrying about the underlying server operating system.
        * *Serverless (AWS Lambda):* The most modern compute model, allowing code execution directly in response to events (event-driven) without provisioning any servers. Notably, you only pay for the exact milliseconds of execution time.
    * **Right-sizing Skills:** The most important lesson is that there is no "best" service, only the "most suitable" one. Choose EC2 when you need full control over the OS, choose Containers for Microservices, and choose Lambda for fast, auto-scaling tasks.
    ![Amazon Elastic Compute Cloud](/images/Worklog/AmazonElasticComputeCloudEN.jpg)
### Week 2 Achievements:

* Understood what AWS is and mastered the basic service groups: 
  * Compute
  * Storage
  * Networking 
  * Database

* Successfully created and configured an AWS Free Tier account.

* Became familiar with the AWS Management Console and learned how to find, access, and use services via the web interface.

* Installed and configured AWS CLI on the computer, including:

* Used AWS CLI to perform basic operations such as:
  
* Acquired the ability to connect between the web interface and CLI to manage AWS resources in parallel.
* **Completed Module 1: Introduction to Amazon Web Services**
  * **Overview & Infrastructure:** Mastered the definition of AWS, the global infrastructure map, and the methods of interacting with AWS.
  * **Security & IAM:** Clearly understood the AWS Shared Responsibility Model, how to protect the root user account, and Identity and Access Management (IAM).
  * **Practice:** Illustrated security deployment with IAM and hosted the Employee Directory application.

* **Completed Module 2: AWS Compute**
  * **Amazon EC2:** Learned the concept of compute as a service, the getting started guide, and the lifecycle of an EC2 instance.
  * **Container & Serverless:** Expanded knowledge on container services and serverless computing through AWS Fargate and AWS Lambda.
  * **Practice & Application:** Launched the actual Employee Directory application on EC2 and learned how to analyze and select the appropriate compute service for specific problems.
  ---

