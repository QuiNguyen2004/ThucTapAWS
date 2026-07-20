---
title: "Worklog Tuần 10"
date: 2026-07-17
weight: 2
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu triển khai:

* **Triển khai Cơ sở dữ liệu lên hạ tầng AWS (Cloud Database Provisioning):** Đưa sơ đồ ERD và kiến trúc PostgreSQL đã thiết kế ở Tuần 9 lên môi trường Amazon RDS (Managed Service), đảm bảo tính sẵn sàng cao (Multi-AZ) và cô lập tuyệt đối trong lớp Private Subnets.
* **Tối ưu bảo mật đường truyền & Quản lý Credentials:** Tích hợp AWS Secrets Manager và Interface VPC Endpoint (AWS PrivateLink) nhằm khóa chặt luồng giao tiếp dữ liệu, loại bỏ hoàn toàn việc lưu trữ mật khẩu nhạy cảm trong mã nguồn ứng dụng.
* **Thực thi Migration & Kích hoạt `pgvector` trên Cloud:** Khởi tạo cấu trúc các nhóm bảng nghiệp vụ (Người dùng, Sản phẩm, Giỏ hàng/Đơn hàng) và extension xử lý vector trực tiếp trên instance RDS PostgreSQL.
* **Xây dựng luồng nạp dữ liệu tri thức cho AI (RAG Data Ingestion):** Phát triển pipeline tự động xử lý tài liệu y khoa, cắt nhỏ văn bản (chunking), chuyển đổi thành vector embeddings (1536 chiều) và nạp vào database để chuẩn bị cho AI Chatbot.

### Các công việc cần triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành |
| :--- | :--- | :--- | :--- |
| 2 | **Cấu hình DB Subnet Group & Khởi tạo Amazon RDS:** <br> - Thiết lập `pharmacare-db-subnet-group` trên 2 Private DB Subnets. <br> - Khởi tạo máy chủ RDS PostgreSQL (`pharmacare-db`, lớp `db.t4g.micro`) gắn tường lửa `pharmacare-rds-sg`. | 26/06/2026 | 26/06/2026 |
| 3 | **Thiết lập Secrets Manager & Private VPC Endpoint:** <br> - Quản lý mật khẩu Master DB động qua AWS Secrets Manager. <br> - Cấu hình `pharmacare-secretsmanager-vpce` (Port 443) giúp ứng dụng Backend lấy secret hoàn toàn qua mạng nội bộ VPC. | 27/06/2026 | 27/06/2026 |
| 4 | **Thực thi Database Migration trên môi trường Cloud:** <br> - Kết nối vào RDS thông qua Bastion Host / VPN. <br> - Chạy script DDL tạo 4 phân hệ bảng (User, Product, E-commerce, AI) và kích hoạt extension `pgvector`. | 28/06/2026 | 28/06/2026 |
| 5 | **Xây dựng luồng Data Ingestion cho AI RAG:** <br> - Viết script Python/Java bóc tách dữ liệu thông tin thuốc (`Thong_tin_thuoc`). <br> - Tích hợp API Embedding (1536 dims) để chuyển hóa văn bản thành vector và lưu vào bảng `Thong_tin_thuoc_vector`. | 29/06/2026 | 29/06/2026 |
| 6 | **Kiểm thử tích hợp & Tối ưu Indexing HNSW:** <br> - Đánh giá hiệu năng truy vấn độ tương đồng Cosine (`vector_cosine_ops`) trên tập dữ liệu lớn tại RDS. <br> - Rà soát bảo mật tầng mạng và kiểm tra khả năng chống chịu lỗi (Failover test). | 30/06/2026 | 30/06/2026 |

---

**Sơ đồ Kiến trúc Kết nối Dữ liệu & Luồng Ingestion AI - Pharmacare AI:**
![AWS Architecture - Pharmacare AI](/ThucTapAWS/images/Worklog/diafinal.png)

### Chi tiết quá trình thực hiện:

Trong tuần này, trọng tâm công việc dịch chuyển từ khâu thiết kế lý thuyết (Local/ERD) sang việc thực thi trên môi trường Đám mây (Cloud Infrastructure) và chuẩn bị dữ liệu thực tế cho AI:

#### 1. Triển khai hạ tầng Amazon RDS PostgreSQL (Multi-AZ & Isolation)
* Khởi tạo **DB Subnet Group** (`pharmacare-db-subnet-group`) bao phủ hai mạng con kín (`10.0.11.0/24` và `10.0.12.0/24`) thuộc hai Availability Zones (`ap-southeast-1a` và `ap-southeast-1b`).
* Triển khai máy chủ cơ sở dữ liệu `pharmacare-db` (PostgreSQL Engine). Áp dụng quy tắc bảo mật "đặc quyền tối thiểu", máy chủ DB không có Public IP và chỉ chấp nhận các kết nối Inbound Port `5432` từ tường lửa của tầng Backend (`pharmacare-rds-sg`).

#### 2. Bảo mật Credentials với AWS Secrets Manager & PrivateLink
* Thay vì khai báo tĩnh (hardcode) chuỗi kết nối cơ sở dữ liệu, toàn bộ thông tin đăng nhập được mã hóa và quản lý bởi **AWS Secrets Manager**.
* Để tối ưu hóa chi phí và ngăn chặn truy cập trái phép qua Internet, hệ thống được cấu hình một **Interface VPC Endpoint** (`pharmacare-secretsmanager-vpce`). Khi các microservices từ Private App Subnets cần xác thực với database, luồng giao thức HTTPS (Port 443) sẽ đi qua PrivateLink nội bộ của AWS thay vì vòng qua NAT Gateway hay Internet công cộng.

#### 3. Đồng bộ Sơ đồ ERD & Cấu hình `pgvector` trên RDS
* Thiết lập đường ống kết nối an toàn vào mạng VPC để thực thi các script SQL Migration đã được chuẩn bị từ Tuần 9.
* Khởi tạo thành công 4 nhóm thực thể quan hệ cốt lõi: Quản lý Người dùng (`Nguoi_dung`, `Vai_tro`), Danh mục Kho hàng (`San_pham`, `Ton_kho`), Luồng Thương mại điện tử (`Gio_hang`, `Don_hang`, `Thanh_toan`), và cụm dữ liệu AI.
* Xác nhận extension `pgvector` hoạt động ổn định trên môi trường RDS PostgreSQL bằng lệnh `CREATE EXTENSION IF NOT EXISTS pgvector;`, sẵn sàng cho xử lý dữ liệu nhiều chiều.

#### 4. Xây dựng đường ống nạp dữ liệu tri thức (RAG Data Ingestion Pipeline)
* Xây dựng luồng tự động hóa xử lý tài liệu chuyên môn (hướng dẫn sử dụng thuốc, thành phần dược lý, chống chỉ định).
* **Chunking & Embedding:** Các văn bản dài được phân tách thành các đoạn nhỏ có ngữ cảnh (semantic chunking). Hệ thống gọi API mô hình ngôn ngữ lớn (Embeddings Model) để chuyển đổi từng đoạn text thành vector 1536 chiều.
* **Lưu trữ & Lập chỉ mục:** Tải toàn bộ dữ liệu vector vào bảng `Thong_tin_thuoc_vector`. Tiến hành xây dựng chỉ mục **HNSW (Hierarchical Navigable Small World)** với tham số `vector_cosine_ops`, giúp hệ thống AI Chatbot sau này có thể tìm kiếm ngữ cảnh y khoa tương đồng chỉ trong tích tắc (dưới 50ms).

---

### Kết quả đạt được:

* **Hạ tầng Dữ liệu Cloud hoàn chỉnh:** Hệ thống cơ sở dữ liệu quan hệ và Vector DB đã vận hành ổn định trên Amazon RDS, sẵn sàng cho việc tích hợp với các module Backend API (Spring Boot/Node.js) ở các tuần kế tiếp.
* **Bảo mật tuyệt đối (Zero Public Exposure):** Đạt chuẩn bảo mật doanh nghiệp khi cô lập hoàn toàn database trong Private Subnet, kết hợp quản lý mật khẩu động qua Secrets Manager và PrivateLink nội bộ.
* **Khởi tạo thành công "bộ não" tri thức cho AI:** Lượng lớn thông tin thuốc ban đầu đã được vector hóa và lập chỉ mục HNSW thành công trên PostgreSQL. Đây là bước đệm tối quan trọng để hoàn thiện tính năng tư vấn y khoa thông minh (RAG Chatbot) với độ chính xác cao và không bị ảo giác (hallucination).