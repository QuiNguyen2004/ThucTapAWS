---
title: "Worklog Tuần 8"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu triển khai:

* **Thiết kế kiến trúc hệ thống thực tế:** Lên ý tưởng và phác thảo bản vẽ kiến trúc AWS toàn diện cho dự án "Pharmacare AI".
* **Tích hợp trí tuệ nhân tạo (AI Integration):** Xây dựng luồng xử lý AI ứng dụng mô hình RAG (Retrieval-Augmented Generation) thông qua việc kết hợp Amazon Bedrock và dịch vụ lưu trữ vector OpenSearch.
* **Tối ưu hóa bảo mật và kiến trúc Serverless:** Phân tách hệ thống thành các phân lớp (Layers) độc lập, kết hợp triển khai các dịch vụ Serverless (Lambda, API Gateway) và bảo mật cơ sở dữ liệu trong mạng nội bộ (Private DB Subnet).

### Các công việc cần triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành |
| :--- | :--- | :--- | :--- |
| 2 | **Triển khai Global & Frontend Layer:** <br> - Thiết lập Amazon Route 53 (DNS) và Amazon CloudFront phân phối nội dung. <br> - Áp dụng AWS WAF bảo vệ ứng dụng và lưu trữ Web UI tĩnh trên Amazon S3. <br> - Tích hợp Amazon Cognito để quản lý xác thực Login/JWT Token. | 15/06/2025 | 15/06/2025 |
| 3 | **Thiết lập Backend API Layer & Database:** <br> - Xây dựng luồng API với Amazon API Gateway kết nối với AWS Lambda Backend. <br> - Khởi tạo Amazon RDS (Pharmacy Database) với kiến trúc Multi-AZ Replication trong VPC. <br> - Cấu hình AWS Secrets Manager để bảo mật DB Credential. | 16/06/2025 | 16/06/2025 |
| 4 | **Xây dựng AI Chatbot / RAG Layer:** <br> - Thiết lập AWS Lambda (Chatbot Handler) nhận luồng xử lý từ Frontend. <br> - Tích hợp Amazon Bedrock đóng vai trò làm LLM/Bộ não AI trung tâm. | 17/06/2025 | 17/06/2025 |
| 5 | **Triển khai Indexing & Vector Store:** <br> - Khởi tạo Amazon OpenSearch Service đóng vai trò là Vector Store nằm trong Private DB Subnet. <br> - Viết script cho AWS Lambda (Indexing) để tự động hóa việc đưa tài liệu từ Amazon S3 (Knowledge Docs) vào OpenSearch. | 18/06/2025 | 18/06/2025 |
| 6 | **Hoàn thiện Monitoring & Security Layer:** <br> - Cấu hình Amazon CloudWatch thu thập Log & Metrics, kết hợp CloudWatch Alarms và Amazon SNS để gửi cảnh báo. <br> - Thiết lập AWS Backup sao lưu dữ liệu và cấu hình phân quyền AWS IAM (Generic group) cho toàn hệ thống. | 19/06/2025 | 19/06/2025 |

---
**Sơ đồ kiến trúc tổng thể của dự án:**
![AWS Architecture - Pharmacare AI](/images/Worklog/AWS_Architecture-PharmacareAI.png)
### Chi tiết quá trình thực hiện:

Dựa trên bản thiết kế kiến trúc Pharmacare AI, hệ thống được phân rã thành các phân hệ (Layers) tương tác logic với nhau:

#### 1. Xử lý luồng truy cập người dùng (Global & Frontend Layer)
* Luồng truy cập của User (1) ban đầu được phân giải thông qua **Amazon Route 53**, sau đó điều hướng (2) đến mạng lưới CDN của **Amazon CloudFront**. 
* CloudFront chịu trách nhiệm kéo nội dung giao diện tĩnh (3) từ **Amazon S3 (S3 Web UI Bucket)**, trong khi toàn bộ request được bảo vệ chặt chẽ bởi tường lửa **AWS WAF**.
* Đối với việc định danh và bảo mật phiên người dùng, hệ thống sử dụng **Amazon Cognito** (4) để cấp phát và quản lý JWT Token (Login).

#### 2. Xử lý nghiệp vụ lõi (Backend API & Database)
* Các yêu cầu mang tính nghiệp vụ (5) từ Frontend sẽ được gửi tới **Amazon API Gateway**, sau đó kích hoạt (6) các **AWS Lambda (Lambda Backend API)** đóng vai trò xử lý logic.
* Để kết nối an toàn với cơ sở dữ liệu **Amazon RDS (Pharmacy Database)** nằm sâu trong **Private DB Subnet (AZ-A và AZ-B)** (7), Lambda backend không lưu cứng mật khẩu mà phải gọi đến **AWS Secrets Manager** để lấy thông tin xác thực (DB Credential) một cách an toàn. RDS cũng được cấu hình *Multi A-Z Replication* để dự phòng lỗi.

#### 3. Tích hợp RAG AI (AI Chatbot & Index Layer)
* Đây là trái tim của dự án Pharmacare AI. Yêu cầu chat (8) đi thẳng từ Frontend vào **AWS Lambda (Chatbot Handler)**.
* Lambda này đóng vai trò trung gian giao tiếp (9) với **Amazon Bedrock (LLM)**. Để mô hình AI có kiến thức chuyên ngành y dược, Amazon Bedrock tiến hành truy xuất ngữ cảnh (10) từ cơ sở dữ liệu vector là **Amazon OpenSearch Service**.
* Song song đó, ở phân lớp Index, một luồng **AWS Lambda (Indexing)** khác sẽ liên tục theo dõi **Amazon S3 (S3 Knowledge Docs)**. Khi có tài liệu y khoa mới, Lambda này sẽ băm nhỏ và nhúng (11) vào Vector Store của OpenSearch.

#### 4. Quản trị rủi ro (Monitoring & Security)
* Toàn bộ nhật ký hoạt động và số liệu hệ thống (12) được đẩy tập trung về **Amazon CloudWatch**. 
* Khi có sự cố vượt ngưỡng, **CloudWatch Alarms** sẽ kích hoạt (13) **Amazon SNS** để gửi cảnh báo đến đội ngũ vận hành. 
* Ngoài ra, quy trình bảo vệ dữ liệu được củng cố (14) nhờ **AWS Backup** và hệ thống phân quyền **AWS IAM** áp dụng xuyên suốt kiến trúc.

---

### Kết quả đạt được:

* **Sẵn sàng cho việc xây dựng IaC:** Việc thiết kế và bóc tách rõ ràng từng component trong sơ đồ đã tạo điều kiện vô cùng thuận lợi để bắt đầu viết mã hạ tầng (Terraform/CloudFormation) cho các bước tiếp theo.
* **Hoàn thiện mô hình RAG:** Áp dụng thành công lý thuyết Retrieval-Augmented Generation vào một ứng dụng thực tế. Sự kết hợp giữa Bedrock (tạo sinh ngôn ngữ) và OpenSearch (cung cấp context chuyên ngành) giải quyết triệt để vấn đề "ảo giác" (hallucination) thường gặp ở các AI Chatbot thông thường.
* **Nâng tầm tư duy thiết kế hệ thống:** Việc đóng gói cơ sở dữ liệu (RDS và OpenSearch) vào trong các VPC Private Subnets, đồng thời quản lý mật khẩu tập trung bằng Secrets Manager đã đảm bảo ứng dụng đáp ứng các tiêu chuẩn bảo mật khắt khe nhất ngay từ khâu thiết kế (Security by Design).

