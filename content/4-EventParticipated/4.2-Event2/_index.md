---
title: "Event 2"
date: 2026-07-09
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Event Report “AWS First Cloud Journey: From Virtualization and Serverless to AI in Security”

### Event Objectives

- Explore the cloud migration journey with AWS.
- Deep dive into modern system architecture: comparing Virtualization (Virtual Machines) and Containerization (Docker).
- Identify practical challenges when deploying Serverless architectures (AWS Lambda, DynamoDB).
- Apply Machine Learning combined with AWS WAF to build a Network Intrusion Detection System (NIDS).
- Career orientation and practical experience sharing on the path from Helpdesk/Sysadmin to DevOps.

### Speakers & Agenda

- **09:20 - 10:00 | Nguyen Quoc Bao**
- **10:00 - 10:35 | Nguyen Huynh Quoc Bao**
- **10:35 - 10:50 | Viet Phat**
- **10:50 - 11:10 | Le Hoang Gia Dai** - Security Expert (Topic: WAF + ML for Cyber Attack Detection)
- **11:10 - 12:00 | Tran Trung Vinh** - (Topic: Career journey from Sysadmin to DevOps & Interview stories at Central Retail Group)

*(Note: Technical topics on Containers and Serverless were covered during the morning sessions by Quoc Bao, Huynh Quoc Bao, and Viet Phat).*

### Key Highlights

#### 1. The Architecture Battle: Virtual Machines vs. Containers
- **Fundamental Differences**: VMs are "Heavyweight" because they run a separate Guest OS on a Hypervisor, leading to slow boot times (minutes) and high resource consumption. Meanwhile, Containers (Docker) are "Lightweight," sharing the Host OS, and booting up in milliseconds.
- **Optimization**: Containers help package applications (App + Bins/Lib) compactly, delivering Native performance, saving RAM, and allowing hundreds to thousands of containers to run on the same infrastructure instead of just a few dozen VMs.

#### 2. Challenges in Serverless Deployment (AWS Lambda & DynamoDB)
- **State Management (Stateless Lambda)**: AWS Lambda does not store data in memory between requests. All states (e.g., Game state) must be continuously queried and stored in DynamoDB, requiring clever architectural design to avoid latency.
- **Cost Issues (DynamoDB Scan Cost)**: Using the `ScanCommand` to scan entire datasets (e.g., finding a player in a table) becomes increasingly slow and expensive as the system scales up. Optimization via Query and Index is necessary.
- **Stale Connections (GoneException)**: Effectively handling abruptly disconnected players so the Matchmaking system does not send wasted messages.

#### 3. WAF & Machine Learning for Network Intrusion Detection Systems (Le Hoang Gia Dai)
- **Limitations of Signature-based Detection**: Analyzed why traditional signature-based WAF rules are insufficient against modern attacks (Zero-day, Behavioral anomalies).
- **NIDS Solution with ML**: Using Machine Learning for behavior-based detection. The model is trained on the standard `CSE-CIC-IDS2018` dataset.
- **Operational Workflow**: Building a real-time dashboard for monitoring, then correlating predictions from NIDS with AWS WAF events to enhance threat detection capabilities in Cloud environments.

#### 4. Career Journey: From Sysadmin to DevOps (Tran Trung Vinh)
- **Mindset Shift**: Cloud is not just a new technology; it is a "new way of thinking." Moving from manual installation to automation (Infrastructure as Code).
- **Interview Experience**: Valuable lessons and personal development strategies drawn from the interview process at large corporations (Central Retail Group).

### Key Takeaways

#### Architectural & Cloud Mindset
- **Right Tool for the Right Job**: No technology is absolutely perfect. Depending on cost and performance requirements, choose VM or Container; design databases carefully to avoid cost overruns (Scan vs. Query).
- **DevOps Mindset**: Infrastructure is now treated as "Code". System stability relies heavily on automation and monitoring capabilities.

#### Modern Security Mindset
- Gained a deeper understanding of the limits of static rules in Firewalls/WAFs. Modern security inevitably requires AI/ML intervention to analyze abnormal network patterns rather than relying solely on known rules.

### Application to Work & Study

- **Packaging Personal Projects**: Instead of running Java (Spring Boot) or Python (Flask) applications directly on the host machine and causing environment conflicts, I will start writing `Dockerfile`s to containerize my web apps, making deployment to servers much easier.
- **Upgrading Security Mindset (SIEM/Wazuh)**: The knowledge from Mr. Gia Dai's session on WAF + ML perfectly complements my security research direction. I can apply this "Event Correlation" mindset to the Wazuh SIEM system to write more effective custom rules for my "Session Hijacking and Fixation Prevention" project.
- **Cloud Cost Control**: If I deploy small demos on AWS in the future, the lesson on `DynamoDB Scan Cost` will help me be more cautious in database design to avoid exceeding the Free Tier limits.

### Personal Experience at the Event

The AWS First Cloud Journey event provided a highly "Industry-focused" perspective rather than just textbook theory.

- **Technically**: Seeing the actual architectures deployed by AWS teams helped me visualize the path to becoming a Systems/Security Engineer more clearly. Dissecting the "weaknesses" of Serverless (like Stateless Lambda) demonstrated the speakers' deep expertise.
- **Career Orientation**: The session sharing the journey from IT Helpdesk to Sysadmin and DevOps by Mr. Trung Vinh was highly motivating. It outlined a clear roadmap, helping students like me know what skill sets to equip (from OS and Network to Cloud and Automation) to prepare for upcoming interviews.
- **Event Atmosphere**: Professional yet open. The case studies on applying AI to WAF truly opened a new direction for the information security projects I am pursuing.

#### Event Photos

***(Knowledge on Containers vs VMs)***
<div style="display: flex; flex-direction: row; justify-content: center; gap: 2%; margin-bottom: 15px;">
  <img src="/ThucTapAWS/images/events/event2/anh6.jpg" style="width: 35%; margin: 0;" alt="VM and Container Comparison">
  <img src="/ThucTapAWS/images/events/event2/anh7.jpg" style="width: 35%; margin: 0;" alt="Detailed differences between VM and Container">
</div>

***(Topic: AWS WAF & Machine Learning by speaker Le Hoang Gia Dai)***
<div style="display: flex; flex-direction: row; justify-content: center; gap: 2%; margin-bottom: 15px;">
  <img src="/ThucTapAWS/images/events/event2/anh9.jpg" style="width: 35%; margin: 0;" alt="WAF + ML for Cyber Attack Detection">
  <img src="/ThucTapAWS/images/events/event2/anh10.jpg" style="width: 35%; margin: 0;" alt="Agenda combining AWS WAF and ML">
</div>

***(Practical challenges with AWS and career orientation)***
<div style="display: flex; flex-direction: row; justify-content: center; gap: 2%; margin-bottom: 15px;">
  <img src="/ThucTapAWS/images/events/event2/anh4.jpg" style="width: 35%; margin: 0;" alt="Challenges with Serverless">
  <img src="/ThucTapAWS/images/events/event2/anh12.jpg" style="width: 35%; margin: 0;" alt="Career journey">
</div>

> In short, the event served as a perfect bridge between the Network Administration/Security knowledge I am learning in school and practical enterprise Cloud standards. It reinforced my determination to master network infrastructure and integrate AI into security workflows.