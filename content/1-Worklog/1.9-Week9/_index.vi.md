---
title: "Worklog Tuần 9"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---

### Mục tiêu triển khai:

* **Thiết kế Cơ sở dữ liệu quan hệ (RDBMS):** Chuyển hóa các yêu cầu nghiệp vụ của hệ thống Pharmacare AI thành mô hình thực thể kết hợp (ERD), đảm bảo chuẩn hóa dữ liệu và tính toàn vẹn thông qua các ràng buộc (Constraints/Foreign Keys).
* **Tích hợp hạ tầng Vector Database cho AI:** Cài đặt và ứng dụng extension `pgvector` trực tiếp trên PostgreSQL. Đây là bước đột phá giúp lưu trữ và truy xuất các vector embeddings 1536 chiều, tạo nền tảng cốt lõi cho tính năng AI Chatbot (RAG).
* **Quản trị vòng đời luồng thương mại điện tử:** Thiết kế hoàn chỉnh kiến trúc dữ liệu xuyên suốt từ khâu quản lý tài khoản, danh mục sản phẩm, theo dõi tồn kho, cho đến xử lý giỏ hàng và thanh toán đơn hàng.

### Các công việc cần triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành |
| :--- | :--- | :--- | :--- |
| 2 | **Thiết kế ERD & Nhóm bảng Người dùng:** <br> - Phác thảo sơ đồ ERD tổng thể. <br> - Viết script tạo các bảng `Vai_tro`, `Nguoi_dung`, `Dia_chi` với các ràng buộc RESTRICT và CASCADE phù hợp. | 22/06/2025 | 22/06/2025 |
| 3 | **Xây dựng Nhóm bảng Sản phẩm & Kho hàng:** <br> - Khởi tạo các bảng `Danh_muc`, `Thuong_hieu`, `Nha_thuoc`, `San_pham`. <br> - Thiết lập bảng lõi trung tâm `Phan_loai_san_pham` và `Ton_kho` để kết nối giỏ hàng/đơn hàng. | 23/06/2025 | 23/06/2025 |
| 4 | **Cấu trúc Nhóm bảng Giỏ hàng & Đơn hàng:** <br> - Triển khai luồng dữ liệu mua sắm với `Gio_hang`, `Chi_tiet_gio_hang`. <br> - Xây dựng hệ thống lưu vết giao dịch qua bảng `Don_hang`, `Chi_tiet_don_hang`, `Thanh_toan`. | 24/06/2025 | 24/06/2025 |
| 5 | **Tích hợp Vector Database (AI RAG):** <br> - Kích hoạt extension `pgvector`. <br> - Tạo bảng `Thong_tin_thuoc` và `Thong_tin_thuoc_vector` cấu hình cột `VECTOR(1536)` tương thích Amazon Bedrock/OpenAI. | 25/06/2025 | 25/06/2025 |
| 6 | **Tối ưu hóa hiệu suất & Kiểm thử (Testing):** <br> - Đánh chỉ mục (Indexing) dạng HNSW cho cột Vector để tối ưu hóa tốc độ tìm kiếm `vector_cosine_ops`. <br> - Rà soát toàn bộ khóa chính (PK) và khóa ngoại (FK) trên sơ đồ. | 26/06/2025 | 26/06/2025 |

---

**Sơ đồ Thực thể Kết hợp (ERD) - Pharmacare AI:**
![Sơ đồ ERD Pharmacare AI](/images/Worklog/database.jpg)

### Chi tiết quá trình thực hiện:

Kiến trúc cơ sở dữ liệu được chia thành 4 phân hệ chính hoạt động chặt chẽ với nhau:

#### 1. Nhóm Quản lý Người dùng & Phân quyền (User Management)
* Xây dựng nền tảng định danh với bảng `Nguoi_dung` liên kết chặt chẽ với bảng `Vai_tro` (Role-based access control). 
* Quản lý thông tin giao hàng thông qua bảng `Dia_chi` với ràng buộc `ON DELETE CASCADE`, đảm bảo không để lại rác dữ liệu khi một tài khoản người dùng bị xóa khỏi hệ thống.

#### 2. Nhóm Sản phẩm & Kho hàng (Product & Inventory)
* Bố trí dữ liệu theo mô hình chuẩn hóa. Bảng `San_pham` đóng vai trò lưu trữ thông tin chung, trong khi bảng `Phan_loai_san_pham` được thiết kế làm bảng lõi trung tâm lưu trữ giá trị cụ thể và biến thể của từng mặt hàng.
* Giải quyết bài toán quản lý đa chi nhánh thông qua bảng `Ton_kho`, liên kết trực tiếp giữa biến thể sản phẩm (`Ma_phan_loai`) và cơ sở phân phối (`Ma_nha_thuoc`).

#### 3. Nhóm Giỏ hàng & Đơn hàng (E-commerce Flow)
* Thiết lập cấu trúc `Gio_hang` với quan hệ 1-1 với `Nguoi_dung` (`UNIQUE NOT NULL`).
* Luồng thanh toán được bóc tách rõ ràng: từ `Chi_tiet_gio_hang` chuyển hóa thành `Don_hang` và `Chi_tiet_don_hang`. Thông tin giao dịch cuối cùng được ghi nhận tại bảng `Thanh_toan` gắn liền với `Phuong_thuc_thanh_toan`. Các ràng buộc khóa ngoại (ví dụ: `ON DELETE RESTRICT` tại chi tiết đơn hàng) được thiết lập cực kỳ cẩn thận để chống việc vô tình xóa lịch sử giao dịch tài chính.

#### 4. Nhóm Phục vụ AI Chatbot & Vector Search (AI Integration)
* Kích hoạt thành công extension `CREATE EXTENSION IF NOT EXISTS pgvector;` để biến PostgreSQL thành một cơ sở dữ liệu vector thực thụ.
* Thiết kế bảng `Thong_tin_thuoc_vector` lưu trữ các khối văn bản (chunks) và nhúng chúng vào cột `Embedding VECTOR(1536)`.
* Tối ưu hóa truy vấn nội hàm bằng cách tạo Index dạng Hierarchical Navigable Small World (`HNSW`), hỗ trợ các phép toán tương đồng cosine (`vector_cosine_ops`), cho phép AI lấy ngữ cảnh y khoa siêu tốc để phản hồi người dùng.

---

### Kết quả đạt được:

* **Sẵn sàng tích hợp Backend:** Sơ đồ ERD và script SQL đã cung cấp một cấu trúc dữ liệu minh bạch, chuẩn xác, sẵn sàng để Backend kết nối thông qua các ORM (như Hibernate/Sequelize).
* **Nền tảng RAG mạnh mẽ:** Việc ứng dụng `pgvector` ngay trong cơ sở dữ liệu quan hệ giúp tiết kiệm chi phí vận hành (không cần thuê thêm một dịch vụ Vector DB độc lập), đồng thời duy trì được tính nhất quán của dữ liệu (ACID) giữa thông tin thuốc thông thường và dữ liệu vector cho AI.
* **Đảm bảo tính toàn vẹn (Data Integrity):** Hệ thống khóa chính (PK) và khóa ngoại (FK) đan xen logic giúp loại bỏ hoàn toàn nguy cơ dữ liệu mồ côi (orphan records), đặc biệt quan trọng trong các module liên quan đến hóa đơn và thanh toán.


