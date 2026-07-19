---
title: "Week 4 Worklog"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---

### Deployment Objectives:

* **Building Core AWS Network Infrastructure:** Establish a "production-ready" Amazon VPC, clearly segregating public and private zones, and ensuring High Availability via a Multi-AZ (Availability Zone) deployment.
* **Optimizing Security and Management:** Deploy secure virtual machines (EC2), apply the principle of least privilege using Security Groups, and completely eliminate the need to expose the SSH port (port 22) to the Internet by utilizing AWS Systems Manager (SSM).
* **Establishing a Hybrid Cloud Network:** Set up and configure a secure AWS Site-to-Site VPN tunnel (using the IPsec protocol) to safely bridge the enterprise's On-premise network with the AWS Cloud.
* **Monitoring and Traffic Analysis:** Enable and configure VPC Flow Logs to capture network packet data, laying the groundwork for future security incident analysis and SIEM integration.

### Weekly Tasks to be Implemented:

| Day | Task | Start Date | End Date | Resources |
| :--- | :--- | :--- | :--- | :--- |
| Mon | **Network Infrastructure Prep (VPC & Subnets):** <br> - Initialize the main VPC with a `10.10.0.0/16` CIDR block. <br> - Allocate 4 Subnets across 2 AZs (AZ 1 & AZ 2) for redundancy. <br> - Attach an Internet Gateway (IGW) for external connectivity. <br> - Set up a NAT Gateway in the Public Subnet to allow one-way outbound Internet access for the Private Subnets. | 18/05/2025 | 18/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Tue | **Routing & Virtual Firewall Setup:** <br> - Create and configure Route Tables (Public routes to IGW, Private routes to NAT). <br> - Configure Security Groups (strict Inbound/Outbound rules). <br> - Enable VPC Flow Logs to push traffic logs to CloudWatch. | 19/05/2025 | 19/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Wed | **Deploying Amazon EC2 Instances:** <br> - Launch a Public EC2 (Web server/Bastion) and a Private EC2 (Backend/Database). <br> - Set up an EC2 Instance Connect Endpoint (EIC). <br> - Configure IAM Roles for secure server access via Session Manager. | 20/05/2025 | 20/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Thu | **Gateway Configuration for Site-to-Site VPN:** <br> - Create a Virtual Private Gateway (VPG) and attach it to the current VPC. <br> - Create a Customer Gateway (CGW) declaring the On-premise environment's Public IP. <br> - Set up an On-premise emulator server (using OpenSwan). | 21/05/2025 | 21/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Fri | **Finalizing VPN Tunnel & System Testing:** <br> - Establish the Site-to-Site VPN Connection, linking the VPG and CGW. <br> - Select the routing mechanism (Static Routing/BGP). <br> - Use VPC Reachability Analyzer to verify packet paths. <br> - Perform Ping tests back and forth between the Private IPs of the two environments. | 22/05/2025 | 22/05/2025 | <https://cloudjourney.awsstudygroup.com/> |

---

### Detailed Implementation Process:

#### 1. Building the Network Foundation (Amazon VPC)
* **Network Space (CIDR):** Created a completely isolated VPC with the IPv4 CIDR block `10.10.0.0/16`.
* **Subnetting:** To ensure High Availability, the system was divided into 4 subnets distributed evenly across 2 Availability Zones:
    * **AZ 1:** Public Subnet (`10.10.1.0/24`) and Private Subnet (`10.10.3.0/24`).
    * **AZ 2:** Public Subnet (`10.10.2.0/24`) and Private Subnet (`10.10.4.0/24`).
* **Internet Gateways:** * Initialized an **Internet Gateway (IGW)** and attached it to the VPC to provide an Internet route for resources in the Public zone.
    * Deployed a **NAT Gateway** (requiring an allocated Elastic IP) situated in the Public Subnet of AZ 1. This is a critical component that allows backend servers located deep within the Private zone to download security patches or libraries from the Internet without exposing their actual IP addresses to the outside world.

#### 2. Routing and Network Layer Security
* **Route Tables:** * *Public Route Table:* Added a `0.0.0.0/0` route pointing to the IGW.
    * *Private Route Table:* Added a `0.0.0.0/0` route pointing to the NAT Gateway.
* **Security Group (Stateful Firewall):** Established virtual firewall rules at the Instance level. Only necessary traffic flows are allowed (e.g., HTTP/HTTPS from anywhere into the Public EC2, SSH/ICMP only from the internal Public network into the Private EC2), rejecting all suspicious requests.
* **Network Monitoring:** Activated **VPC Flow Logs**. All information regarding source IP, destination IP, ports, and protocols (Accept/Reject) is recorded, serving as an invaluable tool for tracing in the event of a cyberattack.

#### 3. Server Deployment and Secure Access (Amazon EC2)
* Launched Amazon Linux 2023 instances into their correctly designated Subnets.
* Instead of opening port 22 (SSH), which carries a high risk of brute-force attacks, the practical implementation utilized **AWS Systems Manager (SSM) Session Manager**. This solution safely opens a terminal stream directly in the browser via an IAM Role, completely isolating the server from Internet-based risks.

#### 4. Building the IPsec Tunnel (Site-to-Site VPN)
* **Initializing Anchor Points:** * On the AWS side: Created a **Virtual Private Gateway (VPG)**. The VPG acts as the endpoint on the edge of the AWS VPC network.
    * On the customer side (On-premise): Created a **Customer Gateway (CGW)** to identify the external network device.
* **Building the Tunnel:** Initialized the VPN Connection linking the VPG and CGW. Each VPN Connection defaults to providing 2 independent tunnels to ensure High Availability in case one physical connection fails.
* **Edge Device Configuration:** Installed and fine-tuned the IPsec configuration on the simulated internal network server (using the open-source software **OpenSwan**). Configured the Pre-Shared Key (PSK) and encryption/authentication algorithms to perfectly match the specifications generated by AWS.

---

### Achieved Results:

* **Robust Network Infrastructure:** Successfully designed and manually configured a Multi-AZ network architecture on AWS, gaining a deep understanding of a packet's lifecycle—from the Internet, through the IGW, routed by the Route Table, and inspected by the Security Group before reaching the EC2 instance.
* **Successful VPN Validation:** The two independent networks (VPC `10.10.x.x` and the simulated On-premise network `10.11.x.x`) were successfully "bridged" together.
* **Practical Testing:** Executed a direct `ping` command from a Private EC2 instance inside the AWS VPC to the internal IP address of the On-premise server. The successfully received ICMP packet (Reply received) proves the IPsec tunnel is actively and flawlessly encrypting data, meeting the real-world Data Center expansion scenarios of enterprises.