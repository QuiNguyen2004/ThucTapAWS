---
title: "Week 10 Worklog"
date: 2026-07-17
weight: 2
chapter: false
pre: " <b> 1.10. </b> "
---

### Implementation Objectives:

* **Cloud Database Provisioning (AWS RDS):** Migrate the relational database schema (ERD) and PostgreSQL architecture designed in Week 9 to Amazon RDS (Managed Service), ensuring High Availability (Multi-AZ) and complete isolation within Private Subnets.
* **Network Security & Credentials Management:** Integrate AWS Secrets Manager and Interface VPC Endpoint (AWS PrivateLink) to secure data communication pipelines, completely eliminating the need to hardcode sensitive database credentials in the application source code.
* **Database Migration & `pgvector` Cloud Activation:** Initialize the relational schema tables (User Management, Product Catalog, E-commerce flow) and enable the vector processing extension directly on the RDS PostgreSQL instance.
* **Knowledge Data Ingestion Pipeline for AI (RAG):** Develop an automated pipeline to process medical documents, perform text chunking, generate 1536-dimensional vector embeddings, and ingest them into the database to prepare the knowledge base for the AI Chatbot.

### Weekly Task Breakdown:

| Day | Task Description | Start Date | End Date |
| :--- | :--- | :--- | :--- |
| Mon | **DB Subnet Group Configuration & Amazon RDS Provisioning:** <br> - Configure `pharmacare-db-subnet-group` across 2 Private DB Subnets. <br> - Provision the RDS PostgreSQL instance (`pharmacare-db`, `db.t4g.micro` class) attached to the `pharmacare-rds-sg` security group. | 26/06/2026 | 26/06/2026 |
| Tue | **Secrets Manager & Private VPC Endpoint Setup:** <br> - Configure dynamic master database credential management via AWS Secrets Manager. <br> - Provision `pharmacare-secretsmanager-vpce` (Port 443) to allow backend services to securely retrieve secrets entirely within the internal VPC network. | 27/06/2026 | 27/06/2026 |
| Wed | **Cloud Database Migration Execution:** <br> - Establish a secure connection to the RDS instance via Bastion Host / VPN. <br> - Execute DDL scripts to create the 4 core schema modules (User, Product, E-commerce, AI) and enable the `pgvector` extension. | 28/06/2026 | 28/06/2026 |
| Thu | **AI RAG Data Ingestion Pipeline Development:** <br> - Develop Python/Java scripts to extract and process drug information data (`Thong_tin_thuoc`). <br> - Integrate the 1536-dimensional Embedding API to transform raw text into vectors and store them in the `Thong_tin_thuoc_vector` table. | 29/06/2026 | 29/06/2026 |
| Fri | **Integration Testing & HNSW Index Optimization:** <br> - Evaluate cosine similarity query performance (`vector_cosine_ops`) on large vector datasets within RDS. <br> - Conduct network security audits and Multi-AZ failover resilience testing. | 30/06/2026 | 30/06/2026 |

---

**PharmaCare AI - Overall Architecture & Data Ingestion Pipeline:**
![PharmaCare AI Overall Architecture](/ThucTapAWS/images/5-Workshop/5.1-Workshop-overview/dia01.png)

### Detailed Implementation Process:

This week, the operational focus shifted from theoretical design (Local/ERD) to hands-on deployment on Cloud Infrastructure and real-world data preparation for the AI system:

#### 1. Amazon RDS PostgreSQL Infrastructure Provisioning (Multi-AZ & Isolation)
* Configured the **DB Subnet Group** (`pharmacare-db-subnet-group`) spanning two isolated subnets (`10.0.11.0/24` and `10.0.12.0/24`) across two Availability Zones (`ap-southeast-1a` and `ap-southeast-1b`).
* Deployed the `pharmacare-db` database server (PostgreSQL Engine). Strictly enforcing the **"Least Privilege"** security principle, the database instance has no Public IP and only accepts Inbound connections on Port `5432` from the Backend tier's security group (`pharmacare-rds-sg`).

#### 2. Credential Security via AWS Secrets Manager & PrivateLink
* Instead of statically declaring (hardcoding) database connection strings, all authentication credentials are encrypted and dynamically managed by **AWS Secrets Manager**.
* To optimize data transfer costs and eliminate public internet exposure, an **Interface VPC Endpoint** (`pharmacare-secretsmanager-vpce`) was provisioned. When microservices running in Private App Subnets authenticate with the database, the HTTPS traffic (Port 443) routed through AWS PrivateLink remains strictly within the internal AWS network rather than traversing the NAT Gateway or public internet.

#### 3. ERD Synchronization & `pgvector` Configuration on RDS
* Established a secure networking pipeline into the VPC to execute the SQL Migration scripts prepared in Week 9.
* Successfully initialized the 4 core relational entity modules: User Management (`Nguoi_dung`, `Vai_tro`), Inventory & Product Catalog (`San_pham`, `Ton_kho`), E-commerce Workflow (`Gio_hang`, `Don_hang`, `Thanh_toan`), and AI vector datasets.
* Verified the operational stability of the `pgvector` extension on the managed RDS PostgreSQL environment by executing `CREATE EXTENSION IF NOT EXISTS pgvector;`, enabling robust high-dimensional vector storage and querying.

#### 4. Knowledge Data Ingestion Pipeline Construction (RAG)
* Built an automated ingestion workflow to process specialized medical documents (drug instruction manuals, pharmacological ingredients, contraindications).
* **Chunking & Embedding:** Large medical texts were segmented into contextually relevant chunks (semantic chunking). The system invoked an LLM Embedding API to convert each text segment into 1536-dimensional vector representations.
* **Storage & Indexing:** Loaded all vector embeddings into the `Thong_tin_thuoc_vector` table. Created a **Hierarchical Navigable Small World (HNSW)** index utilizing the `vector_cosine_ops` operator, enabling the AI Chatbot to perform ultra-fast semantic similarity searches (sub-50ms latency).

---

### Key Achievements:

* **Robust Cloud Data Infrastructure:** The relational database and Vector DB systems are running stably on Amazon RDS, fully prepared for seamless integration with upcoming Backend API modules (Spring Boot/Node.js) in subsequent weeks.
* **Zero Public Exposure Security:** Achieved enterprise-grade security standards by completely isolating the database within Private Subnets, combined with dynamic secret management via AWS Secrets Manager and internal PrivateLink endpoints.
* **Successful AI Knowledge Base Initialization:** A substantial volume of preliminary drug information was successfully vectorized and indexed using HNSW on PostgreSQL. This serves as the critical foundation for building an intelligent, highly accurate medical consultation AI (RAG Chatbot) while effectively mitigating AI hallucination risks.