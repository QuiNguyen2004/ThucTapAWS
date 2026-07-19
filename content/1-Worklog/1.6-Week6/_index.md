---
title: "Week 6 Worklog"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---


### Deployment Objectives:

* **Building High Availability Architecture:** Ensure the FCJ Management application remains stable and experiences zero-downtime even in the event of server or Data Center (Availability Zone) failures.
* **Automated Scalability (Auto Scaling):** Implement Amazon EC2 Auto Scaling so the system automatically adds servers (Scale out) during traffic spikes and removes them (Scale in) during off-peak hours to optimize idle costs.
* **Intelligent Traffic Distribution (Load Balancing):** Set up Elastic Load Balancing (ELB) to evenly distribute incoming user traffic across healthy servers, preventing localized overloads.
* **Mastering Configuration Packaging:** Gain proficiency in using Launch Templates to standardize and automate the provisioning of multiple EC2 instances with identical standard configurations.

### Weekly Tasks to be Implemented:

| Day | Task | Start Date | End Date | Resources |
| :--- | :--- | :--- | :--- | :--- |
| Mon | **Theory Research & Environment Prep:** <br> - Analyze Scaling strategies (Manual, Dynamic, Scheduled, Predictive). <br> - Review the architecture of the FCJ Management application on the base virtual machine. | 01/06/2025 | 01/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Tue | **Creating Launch Template:** <br> - Package standard server configurations (AMI with pre-installed app, Instance Type, Key Pair). <br> - Configure Security Groups and basic Network settings for the Template. | 02/06/2025 | 02/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Wed | **Setting up Elastic Load Balancer:** <br> - Initialize the Target Group and configure Health Check mechanisms. <br> - Create a public-facing Application Load Balancer (ALB) linked to the Target Group. | 03/06/2025 | 03/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Thu | **Configuring Amazon EC2 Auto Scaling Group (ASG):** <br> - Create an ASG based on the established Launch Template. <br> - Define the network (VPC/Subnets) and connect the ASG to the Load Balancer. <br> - Configure Dynamic Scaling policies (e.g., Target Tracking based on CPU utilization). | 04/06/2025 | 04/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Fri | **System Testing & Cleanup:** <br> - Use a stress testing tool to artificially spike CPU usage and trigger a Scale out. <br> - Stop the stress load to observe the Scale in process. <br> - Delete resources (ELB, ASG, Launch Template) to prevent unexpected charges. | 05/06/2025 | 05/06/2025 | <https://cloudjourney.awsstudygroup.com/> |

---

### Detailed Implementation Process:

#### 1. Configuration Packaging with Launch Templates
* Instead of creating EC2 instances manually, configured a **Launch Template**. This blueprint contains all technical specifications (AMI of the FCJ Management VM with the pre-installed application, network configuration, firewalls, and User Data startup scripts if any). 
* Utilizing Launch Templates combined with versioning makes application lifecycle management significantly easier: when there is a new code update, simply create a new version for the template, and the ASG will automatically use this version for subsequently generated servers.

#### 2. Traffic Routing Configuration with Elastic Load Balancing (ELB)
* **Creating a Target Group:** Grouped the destination resources that will process requests. The crucial point in this step is setting up **Health Checks**. Configured the ELB to continuously send pings/requests to a specific application path (e.g., `/health`). If the server returns an error code (like 500) or times out, the ELB will mark that server as "Unhealthy" and stop sending traffic to it.
* **Creating a Load Balancer:** Deployed an Application Load Balancer (ALB) located in the Public Subnets. This ALB acts as the system's single "front door," receiving all Internet requests (HTTP/HTTPS) and distributing them (round-robin or path-based routing) down to the Target Group.

#### 3. Setting up Elasticity with Auto Scaling Group (ASG)
* Initialized the ASG using the newly created Launch Template. Configured the ASG to span across multiple Availability Zones to increase redundancy.
* **ALB Integration:** Linked the ASG with the Load Balancer's Target Group. When the ASG spawns a new server, this server is automatically registered into the Target Group, ready to receive loads.
* **Scaling Policies:** Applied a **Dynamic Scaling** strategy using the **Target Tracking** method. Specifically, established the average CPU Utilization threshold for the entire group at 50%.
    * If CPU > 50%: The system automatically adds instances (Scale out).
    * If CPU < 50%: The system automatically removes instances (Scale in) after a cooldown period.

#### 4. Testing Scenario
* Copied the Load Balancer's DNS Name and pasted it into the browser, confirming the FCJ Management application loaded successfully. Accessed it multiple times to observe the requests being routed between different servers (identified via the Private IP displayed on the web page).
* Logged into an EC2 instance and used the `stress` tool to push CPU consumption to 100%. 
* Observed on CloudWatch and the Auto Scaling interface: The system immediately triggered an Alarm, and the ASG automatically started `Launching a new EC2 instance`. When the stress command was turned off, a short while later, the system automatically began `Terminating EC2 instance`, returning the state to the original Desired Capacity.

---

### Achieved Results:

* **Self-healing and Highly Responsive System:** The deployed architecture demonstrated intelligent load-bearing capabilities. The testing process showed that during simulated high traffic, the system did not crash but smoothly scaled out its computing power.
* **Performance & Resource Cost Optimization:** Solved the balancing problem between performance and cost. AWS Auto Scaling ensures the business only pays for the number of servers truly needed at any given time.
* **Backend Infrastructure Security:** Using a Load Balancer as the traffic reception point allows completely hiding the EC2 servers hosting the application inside the Private Subnet, preventing direct attacks from the Internet against the system servers.