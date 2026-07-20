---
title: "Week 8 Worklog"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.8. </b> "
---

### Deployment Objectives:

* **Designing Practical System Architecture:** Brainstorm and sketch a comprehensive AWS architecture diagram for the "Pharmacare AI" project.
* **AI Integration:** Build an AI processing pipeline applying the RAG (Retrieval-Augmented Generation) model by combining Amazon Bedrock and the OpenSearch vector storage service.
* **Optimizing Security and Serverless Architecture:** Decouple the system into independent Layers, combining the deployment of Serverless services (Lambda, API Gateway) and securing databases within an internal network (Private DB Subnet).

### Weekly Tasks to be Implemented:

| Day | Task | Start Date | End Date |
| :--- | :--- | :--- | :--- |
| Mon | **Deploying Global & Frontend Layer:** <br> - Set up Amazon Route 53 (DNS) and Amazon CloudFront for content delivery. <br> - Apply AWS WAF to protect the application and host the static Web UI on Amazon S3. <br> - Integrate Amazon Cognito for Login/JWT Token authentication management. | 15/06/2025 | 15/06/2025 |
| Tue | **Setting up Backend API Layer & Database:** <br> - Build the API flow with Amazon API Gateway connecting to AWS Lambda Backend. <br> - Initialize Amazon RDS (Pharmacy Database) with a Multi-AZ Replication architecture in the VPC. <br> - Configure AWS Secrets Manager to secure DB Credentials. | 16/06/2025 | 16/06/2025 |
| Wed | **Building AI Chatbot / RAG Layer:** <br> - Set up AWS Lambda (Chatbot Handler) to receive processing flows from the Frontend. <br> - Integrate Amazon Bedrock as the central LLM/AI brain. | 17/06/2025 | 17/06/2025 |
| Thu | **Deploying Indexing & Vector Store:** <br> - Initialize Amazon OpenSearch Service to act as the Vector Store located in the Private DB Subnet. <br> - Write a script for AWS Lambda (Indexing) to automate the ingestion of documents from Amazon S3 (Knowledge Docs) into OpenSearch. | 18/06/2025 | 18/06/2025 |
| Fri | **Finalizing Monitoring & Security Layer:** <br> - Configure Amazon CloudWatch to collect Logs & Metrics, combined with CloudWatch Alarms and Amazon SNS to send alerts. <br> - Set up AWS Backup for data retention and configure AWS IAM permissions (Generic group) for the entire system. | 19/06/2025 | 19/06/2025 |

---
**Overall project architecture diagram:**
![AWS Architecture - Pharmacare AI](/ThucTapAWS/images/Worklog/diafinal.png)

### Detailed Implementation Process:

Based on the Pharmacare AI architectural design, the system is broken down into logically interacting sub-systems (Layers):

#### 1. User Traffic Routing (Global & Frontend Layer)
* User traffic (1) is initially resolved through **Amazon Route 53**, then routed (2) to the **Amazon CloudFront** CDN network. 
* CloudFront is responsible for pulling static interface content (3) from **Amazon S3 (S3 Web UI Bucket)**, while all requests are strictly protected by the **AWS WAF** firewall.
* For user session identification and security, the system uses **Amazon Cognito** (4) to issue and manage JWT Tokens (Login).

#### 2. Core Business Logic (Backend API & Database)
* Business-related requests (5) from the Frontend are sent to **Amazon API Gateway**, which then triggers (6) **AWS Lambda (Lambda Backend API)** acting as the logic processor.
* To securely connect to the **Amazon RDS (Pharmacy Database)** located deep within the **Private DB Subnet (AZ-A and AZ-B)** (7), the backend Lambda does not hardcode passwords but calls **AWS Secrets Manager** to retrieve credentials safely. RDS is also configured with *Multi A-Z Replication* for fault tolerance.

#### 3. RAG AI Integration (AI Chatbot & Index Layer)
* This is the heart of the Pharmacare AI project. Chat requests (8) go straight from the Frontend to the **AWS Lambda (Chatbot Handler)**.
* This Lambda acts as an intermediary communicating (9) with **Amazon Bedrock (LLM)**. To equip the AI model with specialized medical and pharmaceutical knowledge, Amazon Bedrock retrieves context (10) from the vector database, **Amazon OpenSearch Service**.
* Simultaneously, in the Index layer, another **AWS Lambda (Indexing)** stream continuously monitors **Amazon S3 (S3 Knowledge Docs)**. When a new medical document arrives, this Lambda chunks and embeds (11) it into the OpenSearch Vector Store.

#### 4. Risk Management (Monitoring & Security)
* All operational logs and system metrics (12) are centrally pushed to **Amazon CloudWatch**. 
* When a threshold-exceeding incident occurs, **CloudWatch Alarms** triggers (13) **Amazon SNS** to send alerts to the operations team. 
* Additionally, data protection processes are fortified (14) by **AWS Backup** and the **AWS IAM** permission system applied throughout the architecture.

---

### Achieved Results:

* **Ready for IaC Construction:** Clearly designing and decoupling each component in the diagram has created highly favorable conditions for writing infrastructure code (Terraform/CloudFormation) in the upcoming steps.
* **Perfecting the RAG Model:** Successfully applied the Retrieval-Augmented Generation theory to a real-world application. The combination of Bedrock (language generation) and OpenSearch (providing specialized context) thoroughly resolves the "hallucination" issue common in standard AI Chatbots.
* **Elevating System Design Mindset:** Encapsulating databases (RDS and OpenSearch) within VPC Private Subnets, while centrally managing passwords using Secrets Manager, ensures the application meets the strictest security standards right from the design phase (Security by Design).