---
title: "Week 5 Worklog"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---

### Deployment Objectives:

* **Mastering Core Compute Services:** Grasp the operational skills and lifecycle management of Amazon EC2 (Elastic Compute Cloud) virtual servers on AWS.
* **Ensuring Data Safety and Flexibility:** Practice backup operations and system cloning by creating volume state records (EBS Snapshots) and packaging operating systems (Amazon Machine Images - AMI).
* **Troubleshooting Skills:** Successfully execute recovery scenarios to regain server access (on both Windows and Linux) when security keys (Key Pairs) are lost.
* **Practical Application Deployment:** Manually set up server environments (LAMP Stack, Node.js) and deploy a Full-stack Web application (AWS User Management Application) to the cloud environment to serve end-users.

### Weekly Tasks to be Implemented:

| Day | Task | Start Date | End Date | Resources |
| :--- | :--- | :--- | :--- | :--- |
| Mon | **Infrastructure & Security Prep (Preparation):** <br> - Initialize isolated VPCs for Windows and Linux environments. <br> - Set up Security Groups as Stateful Firewalls, configuring strict port blocking/opening rules. | 25/05/2025 | 25/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Tue | **EC2 Server Lifecycle Management (Part 1):** <br> - Practice modifying server hardware configurations (Modify Instance Type). <br> - Create and manage hard drive data backups (EBS Snapshots). <br> - Package a server into a Custom AMI. <br> - Launch a completely new server from the newly created Custom AMI. | 26/05/2025 | 26/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Wed | **Troubleshooting & Remote Access (Part 2):** <br> - Execute the access recovery scenario for Linux EC2 when the Key Pair is lost. <br> - Execute the access recovery scenario for Windows EC2. <br> - Configure the Remote Desktop Protocol (RDP) interface to control the EC2-Ubuntu server. | 27/05/2025 | 27/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Thu | **Web Server Environment Setup:** <br> - Install the LAMP stack (Linux, Apache, MySQL, PHP) on Amazon Linux 2023. <br> - Run the `mysql_secure_installation` script to secure the database. <br> - Install the Node.js Runtime environment (using the LTS version). | 28/05/2025 | 28/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Fri | **User Management Application Deployment (CRUD App):** <br> - Clone the application source code (Node.js, Express) from the repository. <br> - Install dependencies (npm) and configure the Database connection string. <br> - Configure the Security Group to open port `3000`. <br> - Launch the server and test CRUD functionalities. | 29/05/2025 | 29/05/2025 | <https://cloudjourney.awsstudygroup.com/> |

---

### Detailed Implementation Process:

#### 1. Network Foundation and Firewall Preparation
* **VPC Isolation:** Built distinct network spaces (VPCs) to serve the launching of different operating systems (Microsoft Windows Server 2025 and Amazon Linux 2023), preventing network configuration conflicts.
* **Security Group Configuration:** Established security checkpoints at the server level. This process paid special attention to the principle of least privilege: only opening port `22` (SSH) for Linux and port `3389` (RDP) for Windows to designated administrative IPs, while locking down all invalid access flows to prevent port scanning risks.

#### 2. Basic Amazon EC2 Operations and Administration
* **Hardware Scale Up/Down:** Executed the Modify Instance Type operation to change the resource scale (CPU, RAM) of a running server without losing data, meeting the flexible demands of the application.
* **Backup Strategy (EBS & AMI):**
    * Captured hard drive states (Create EBS Snapshot) to store secure data recovery points.
    * From a stably running server, proceeded to "freeze" it and create an Amazon Machine Image (Custom AMI). Utilized this AMI to rapidly initialize a batch of servers with identical configurations, saving time on from-scratch installations.
* **Disaster Recovery (Troubleshooting):** Simulated a scenario where the administrator loses the `.pem` file (security key). Practiced techniques involving volume intervention or using User Data to reset login credentials, thereby successfully regaining control of both Linux and Windows servers.

#### 3. Full-stack Web Application Deployment on Cloud
* **Building the Foundation (LAMP Stack):** Successfully installed the LAMP ecosystem. Specifically focused on the step of setting a strong root password and removing default test databases via the `mysql_secure_installation` command to patch the most rudimentary database vulnerabilities.
* **Node.js Setup:** Installed Node.js LTS (Long Term Support) environment to ensure the application receives continuous security updates during production operation.
* **Deploy Application:** * Downloaded the source code for the *AWS User Management Application* (built with Express and Express-Handlebars).
    * Established a secure connection between the backend tier (Node.js) and the database tier (MySQL/MariaDB).
    * Fine-tuned the EC2 server's Security Group: added a rule allowing Inbound Traffic through port `3000` (the default port for Node.js applications) so end-users can access it via a web browser.

---

### Achieved Results:

* **Comprehensive Compute Administration:** Gained a clear understanding of how to configure, modify resources, and automate EC2 server cloning via AMI, readying for the construction of highly resilient architectures.
* **Mastering Operational Risks:** Confidently handled difficult situations such as lost credentials without disrupting or losing existing data on the server.
* **Perfecting the Software Deployment Flow:** Successfully combined Cloud infrastructure administration knowledge (EC2, Security Groups) with Web development skills (Environment setup, DB connection configuration). The user management application was successfully pushed to the Internet, running smoothly and fully responding to CRUD (Create, Read, Update, Delete) operations on a real interactive interface.