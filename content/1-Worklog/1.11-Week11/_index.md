---
title: "Week 11 Worklog"
date: 2026-06-28
weight: 3
chapter: false
pre: " <b> 1.11. </b> "
---

### Implementation Objectives:

* **Automated Database Migration via AWS Lambda:** Deploy a Serverless architecture (AWS Lambda) running entirely inside a Private VPC to automatically execute DDL/DML scripts and initialize the PostgreSQL database schema for the **PharmaCare AI** system without manual external intervention.
* **Secrets Management & Execution Pipeline Security:** Implement the Least Privilege security model via IAM Roles, enabling the Lambda function to securely retrieve and decrypt master database credentials from AWS Secrets Manager, completely eliminating hardcoded sensitive data in the application source code.
* **Centralized Identity Management System:** Provision **Amazon Cognito User Pool** as the core identity provider responsible for managing user registration, authentication, account verification workflows, and JWT Token issuance.
* **Role-Based Access Control (RBAC):** Configure distinct user groups (`Admin` and `Customer`) within Amazon Cognito, establishing the foundation for API Gateway access control and granular microservice authorization across functional modules.

### Weekly Task Breakdown:

| Day | Task Description | Start Date | End Date |
| :--- | :--- | :--- | :--- |
| Mon | **IAM Role & Lambda Execution Permission Setup:** <br> - Provision the `pharmacare-lambda-role` IAM Role. <br> - Attach required policies: `AWSLambdaBasicExecutionRole`, `AWSLambdaVPCAccessExecutionRole`, and a custom inline policy `pharmacare-read-rds-secret-policy`. | 03/07/2026 | 03/07/2026 |
| Tue | **Lambda Migration Function Provisioning & Configuration:** <br> - Create the `pharmacare-db-migration` Lambda function (Node.js 22.x, x86_64) connected to 2 Private Subnets inside `pharmacare-vpc`. <br> - Optimize resource allocation: set Memory to `256 MB` and Timeout to `60 seconds` to prevent connection drops during heavy processing loads. | 04/07/2026 | 04/07/2026 |
| Wed | **Source Code Packaging & RDS Migration Execution:** <br> - Develop `index.mjs` integrating `pg` and `@aws-sdk/client-secrets-manager` libraries. <br> - Compress into `function.zip`, upload to AWS Lambda, and trigger automated table initialization on the Amazon RDS PostgreSQL instance. | 05/07/2026 | 05/07/2026 |
| Thu | **Amazon Cognito User Pool & App Client Provisioning:** <br> - Provision `pharmacare-user-pool` to manage end-user account lifecycles. <br> - Configure the App Client (generating a Client ID) to prepare for seamless integration with the ReactJS Frontend. | 06/07/2026 | 06/07/2026 |
| Fri | **User Groups (RBAC) Configuration & Token Testing:** <br> - Create `Admin` and `Customer` user groups within Cognito. <br> - Conduct end-to-end testing of Sign-Up/Sign-In workflows, verify JWT Token signature validation, and audit execution logs via Amazon CloudWatch Logs. | 07/07/2026 | 07/07/2026 |

---

### Detailed Implementation Process:

This week, the system achieved a major milestone in platform maturation (Infrastructure as a Platform) by automating database schema management and integrating an enterprise-grade user authentication layer:

#### 1. IAM Role & Execution Permission Setup for Lambda
* Opened the IAM Management Console and provisioned a new role named `pharmacare-lambda-role`.
* Enforced the Least Privilege principle by attaching three foundational policies: CloudWatch logging access (`AWSLambdaBasicExecutionRole`), VPC Elastic Network Interface (ENI) provisioning (`AWSLambdaVPCAccessExecutionRole`), and customized secret retrieval from AWS Secrets Manager (`pharmacare-read-rds-secret-policy`).

![IAM Role Setup for Lambda](/ThucTapAWS/images/Worklog/Tuan11/lam1.png)

#### 2. Provisioning the Migration Lambda Function in VPC
* Provisioned the `pharmacare-db-migration` Lambda function utilizing the Node.js 22.x runtime (x86_64 architecture).
* Attached the function directly to two Private Subnets within `pharmacare-vpc` and assigned a dedicated Lambda Security Group, allowing secure internal communication to the Amazon RDS PostgreSQL instance.

![Lambda Migration Provisioning Configuration](/ThucTapAWS/images/Worklog/Tuan11/lam2.png)

#### 3. Optimizing Execution Resources & Timeout Settings
* Executing SQL scripts to initialize complex table schemas (users, product catalogs, shopping carts, and vector embedding tables for the AI Chatbot) requires continuous network I/O and processing time.
* Consequently, memory allocation was increased to **256 MB** and the execution timeout was raised from the 3-second default to **60 seconds (1 minute)**, successfully preventing Timeout Exceptions during heavy DDL migrations.

![Memory and Timeout Configuration for Lambda](/ThucTapAWS/images/Worklog/Tuan11/lam3.png)

![Confirming General Settings Save](/ThucTapAWS/images/Worklog/Tuan11/lam4.png)

#### 4. Infrastructure Configuration via Environment Variables
* Decoupled infrastructure configurations from application code by defining key-value pairs: `DB_HOST`, `DB_NAME` (`pharmacare_ai`), `DB_PORT` (`5432`), and `RDS_SECRET_ARN`.
* This mechanism ensures that the Lambda function dynamically calls the Secrets Manager API at runtime to securely fetch and decrypt database credentials.

![Environment Variables Configuration for Lambda](/imagesimageses/Worklog/Tuan11/lam5.png)

#### 5. Code Packaging & Cloud Deployment Workflow
* In a local VS Code environment, developed `index.mjs` and installed necessary dependencies (`pg`, `@aws-sdk/client-secrets-manager`). Used the PowerShell `Compress-Archive` command to bundle the source code and `node_modules` into a `function.zip` archive.

![Initial Code Packaging in VS Code](/ThucTapAWS/images/Worklog/Tuan11/lam6.png)

* Uploaded the `function.zip` artifact directly via the Code tab in the AWS Lambda Management Console.

![Uploading ZIP Archive to Lambda Console - Initial Deploy](/ThucTapAWS/images/Worklog/Tuan11/lam7.png)

* Inspected and verified the SQL migration scripts directly within the Lambda Console's embedded code editor to ensure structural accuracy.

![Verifying Lambda Source Code After Initial Update](/ThucTapAWS/images/Worklog/Tuan11/lam8.png)

* When schema modifications or additional tables were required, repeated the compression and upload cycle to keep the cloud database structure synchronized.

![Uploading ZIP Archive to Lambda Console - Final Update](/ThucTapAWS/images/Worklog/Tuan11/lam9.png)

![Complete Migration Source Code After Successful Update](/ThucTapAWS/images/Worklog/Tuan11/lam10.png)

#### 6. Amazon Cognito Centralized Authentication Deployment
* **User Pool & App Client Provisioning:** Deployed `pharmacare-user-pool` as the centralized identity store, enabling automated email/SMS verification and password recovery workflows. Generated an App Client with a unique Client ID to support ReactJS frontend integration.
* **Role-Based Access Control (RBAC):** Provisioned two foundational user groups:
  * `Admin`: Administrative staff granted elevated privileges for inventory control, product catalog management, and analytics reporting.
  * `Customer`: End-users restricted to e-commerce shopping workflows and AI medical consultation (Chatbot) interactions.
* **JWT Token Integration:** Upon successful authentication, Cognito issues a signed JSON Web Token (JWT). This token acts as the cryptographic bearer identity attached to outgoing HTTP requests routed to Amazon API Gateway in subsequent phases.

---

### Key Achievements:

* **Automated Database Deployment Pipeline:** Replaced error-prone manual schema creation with a serverless Lambda migration workflow executing securely inside a private VPC, ensuring structural consistency and strict data isolation.
* **Enterprise-Grade Identity Security Foundation:** Established a robust centralized authentication framework using Amazon Cognito, ready for ReactJS Frontend and API Gateway integration.
* **Comprehensive Observability:** Real-time execution logs, schema migration status reports, and user authentication events were fully integrated into **Amazon CloudWatch Logs**, enabling complete operational visibility for the engineering team.