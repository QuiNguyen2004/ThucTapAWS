---
title: "Week 9 Worklog"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---

### Deployment Objectives:

* **Designing Relational Database Management System (RDBMS):** Transform the business requirements of the Pharmacare AI system into an Entity-Relationship Diagram (ERD), ensuring data normalization and integrity through structural Constraints/Foreign Keys.
* **Integrating Vector Database Infrastructure for AI:** Install and apply the `pgvector` extension directly on PostgreSQL. This is a breakthrough step that helps store and retrieve 1536-dimensional vector embeddings, creating the core foundation for the AI Chatbot (RAG) feature.
* **Managing E-commerce Lifecycle Flow:** Design a complete data architecture spanning from account management, product catalogs, and inventory tracking, to shopping cart processing and order payments.

### Weekly Tasks to be Implemented:

| Day | Task | Start Date | End Date |
| :--- | :--- | :--- | :--- |
| Mon | **Designing ERD & User Tables:** <br> - Sketch the overall ERD. <br> - Write scripts to create `Vai_tro`, `Nguoi_dung`, `Dia_chi` tables with appropriate RESTRICT and CASCADE constraints. | 22/06/2025 | 22/06/2025 |
| Tue | **Building Product & Inventory Tables:** <br> - Initialize `Danh_muc`, `Thuong_hieu`, `Nha_thuoc`, `San_pham` tables. <br> - Set up the core central tables `Phan_loai_san_pham` and `Ton_kho` to connect carts/orders. | 23/06/2025 | 23/06/2025 |
| Wed | **Structuring Cart & Order Tables:** <br> - Deploy the shopping data flow with `Gio_hang`, `Chi_tiet_gio_hang`. <br> - Build a transaction tracking system via `Don_hang`, `Chi_tiet_don_hang`, `Thanh_toan` tables. | 24/06/2025 | 24/06/2025 |
| Thu | **Integrating Vector Database (AI RAG):** <br> - Enable the `pgvector` extension. <br> - Create `Thong_tin_thuoc` and `Thong_tin_thuoc_vector` tables, configuring the `VECTOR(1536)` column compatible with Amazon Bedrock/OpenAI. | 25/06/2025 | 25/06/2025 |
| Fri | **Performance Optimization & Testing:** <br> - Apply HNSW Indexing for the Vector column to optimize `vector_cosine_ops` search speed. <br> - Review all Primary Keys (PK) and Foreign Keys (FK) on the schema. | 26/06/2025 | 26/06/2025 |

---

**Entity-Relationship Diagram (ERD) - Pharmacare AI:**
![ERD Pharmacare AI](/images/Worklog/database.jpg)

### Detailed Implementation Process:

The database architecture is divided into 4 main sub-systems that interact closely with each other:

#### 1. User Management & Authorization Group
* Built an identity foundation with the `Nguoi_dung` table tightly linked to the `Vai_tro` table (Role-based access control). 
* Managed delivery information through the `Dia_chi` table with an `ON DELETE CASCADE` constraint, ensuring no orphaned data is left behind when a user account is removed from the system.

#### 2. Product & Inventory Group
* Arranged data according to normalization models. The `San_pham` table acts as general storage, while the `Phan_loai_san_pham` table is designed as the central core table storing specific values and variants of each item.
* Solved the multi-branch management problem through the `Ton_kho` table, linking directly between product variants (`Ma_phan_loai`) and distribution facilities (`Ma_nha_thuoc`).

#### 3. Cart & Order Group (E-commerce Flow)
* Established the `Gio_hang` structure with a 1-1 relationship to `Nguoi_dung` (`UNIQUE NOT NULL`).
* The payment flow is clearly separated: from `Chi_tiet_gio_hang` transformed into `Don_hang` and `Chi_tiet_don_hang`. Final transaction information is recorded in the `Thanh_toan` table associated with `Phuong_thuc_thanh_toan`. Foreign key constraints (e.g., `ON DELETE RESTRICT` at order details) are set up extremely carefully to prevent the accidental deletion of financial transaction history.

#### 4. AI Chatbot & Vector Search Group
* Successfully enabled the extension using `CREATE EXTENSION IF NOT EXISTS pgvector;` to turn PostgreSQL into a true vector database.
* Designed the `Thong_tin_thuoc_vector` table to store text chunks and embed them into the `Embedding VECTOR(1536)` column.
* Optimized intrinsic queries by creating a Hierarchical Navigable Small World (`HNSW`) index, supporting cosine similarity operations (`vector_cosine_ops`), allowing the AI to fetch medical context at lightning speed to respond to users.

---

### Achieved Results:

* **Backend Integration Ready:** The ERD and SQL scripts have provided a transparent, accurate data structure, ready for the Backend to connect via ORMs (like Hibernate/Sequelize).
* **Robust RAG Foundation:** Applying `pgvector` right inside the relational database saves operational costs (no need to rent a separate independent Vector DB service) while maintaining data consistency (ACID) between standard drug information and vector data for AI.
* **Ensured Data Integrity:** The logical interlocking of Primary Key (PK) and Foreign Key (FK) systems completely eliminates the risk of orphaned records, which is especially crucial in modules related to invoices and payments.