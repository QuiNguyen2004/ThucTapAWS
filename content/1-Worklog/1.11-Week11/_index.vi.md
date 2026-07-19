---
title: "Worklog Tuần 11"
date: 2026-07-17
weight: 3
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu triển khai:

* **Tự động hóa Migration Cơ sở dữ liệu với AWS Lambda:** Triển khai kiến trúc Serverless (AWS Lambda) chạy hoàn toàn trong mạng riêng nội bộ (Private VPC) để thực thi các kịch bản DDL/DML, khởi tạo cấu trúc bảng PostgreSQL cho hệ thống **PharmaCare AI** mà không cần can thiệp thủ công từ bên ngoài.
* **Tích hợp Quản lý Secrets & Bảo mật luồng thực thi:** Áp dụng mô hình đặc quyền tối thiểu (Least Privilege) thông qua IAM Role, cho phép hàm Lambda tự động giải mã và truy xuất mật khẩu Master DB từ AWS Secrets Manager, loại bỏ tuyệt đối việc hardcode thông tin nhạy cảm trong mã nguồn ứng dụng.
* **Xây dựng Hệ thống Xác thực Tập trung (Centralized Identity Management):** Triển khai **Amazon Cognito User Pool** làm dịch vụ quản lý danh tính cho hệ thống, chịu trách nhiệm xử lý toàn bộ luồng đăng ký, đăng nhập, xác minh tài khoản và cấp phát JWT Token.
* **Thiết lập Phân quyền theo Vai trò (RBAC - Role-Based Access Control):** Cấu hình các nhóm quyền hạn (`Admin` và `Customer`) trong Cognito, tạo cơ sở để kiểm soát truy cập API Gateway và phân quyền bảo mật cho các module chức năng của hệ thống.

### Các công việc cần triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành |
| :--- | :--- | :--- | :--- |
| 2 | **Thiết lập IAM Role & Quyền thực thi cho Lambda:** <br> - Khởi tạo IAM Role `pharmacare-lambda-role`. <br> - Đính kèm các chính sách: `AWSLambdaBasicExecutionRole`, `AWSLambdaVPCAccessExecutionRole` và custom inline policy `pharmacare-read-rds-secret-policy`. | 03/07/2026 | 03/07/2026 |
| 3 | **Khởi tạo & Cấu hình Hàm Lambda Migration:** <br> - Tạo hàm Lambda `pharmacare-db-migration` (Node.js 22.x, x86_64) kết nối vào 2 Private Subnets của `pharmacare-vpc`. <br> - Điều chỉnh tài nguyên: Memory `256 MB`, Timeout `60 giây` để ngăn ngắt kết nối khi xử lý tải trọng lớn. | 04/07/2026 | 04/07/2026 |
| 4 | **Đóng gói Mã nguồn & Thực thi Migration lên RDS:** <br> - Viết mã nguồn `index.mjs` tích hợp thư viện `pg` và `@aws-sdk/client-secrets-manager`. <br> - Nén gói `function.zip`, tải lên Lambda và kích hoạt tự động hóa tạo bảng trên Amazon RDS PostgreSQL. | 05/07/2026 | 05/07/2026 |
| 5 | **Triển khai Amazon Cognito User Pool & App Client:** <br> - Khởi tạo `pharmacare-user-pool` quản lý vòng đời tài khoản người dùng. <br> - Cấu hình App Client (Client ID) để chuẩn bị cho việc tích hợp với giao diện Frontend ReactJS. | 06/07/2026 | 06/07/2026 |
| 6 | **Cấu hình Nhóm người dùng (Groups) & Kiểm thử Token:** <br> - Tạo các nhóm quyền hạn `Admin` và `Customer` trong Cognito. <br> - Kiểm thử luồng Đăng ký/Đăng nhập, xác thực chữ ký JWT Token và ghi nhận nhật ký hệ thống trên Amazon CloudWatch Logs. | 07/07/2026 | 07/07/2026 |

---

### Chi tiết quá trình thực hiện:

Trong tuần này, hệ thống tiến thêm một bước quan trọng trong việc hoàn thiện kiến trúc nền tảng (Infrastructure as a Platform) bằng cách tự động hóa quy trình quản trị dữ liệu và tích hợp lớp bảo mật định danh người dùng:

#### 1. Thiết lập Quyền Thực thi IAM Role cho Lambda
* Mở bảng điều khiển IAM Console, khởi tạo role mới với tên `pharmacare-lambda-role`.
* Áp dụng nguyên tắc đặc quyền tối thiểu (Least Privilege) thông qua việc đính kèm 3 chính sách cốt lõi: quyền ghi log CloudWatch (`AWSLambdaBasicExecutionRole`), quyền khởi tạo card mạng ENI trong VPC (`AWSLambdaVPCAccessExecutionRole`), và quyền truy xuất secret từ AWS Secrets Manager (`pharmacare-read-rds-secret-policy`).

![Thiết lập IAM Role cho Lambda](/images/Worklog/Tuan11/lam1.png)

#### 2. Khởi tạo Hàm Lambda Migration trong VPC
* Khởi tạo hàm Lambda `pharmacare-db-migration` sử dụng runtime Node.js 22.x (kiến trúc x86_64).
* Kết nối trực tiếp hàm vào 2 Private Subnets của mạng `pharmacare-vpc` và gán Security Group dành riêng cho Lambda, cho phép giao tiếp nội bộ an toàn tới máy chủ Amazon RDS PostgreSQL.

![Cấu hình khởi tạo Lambda Migration](/images/Worklog/Tuan11/lam2.png)

#### 3. Tối ưu Tài nguyên & Thời gian chờ (Timeout/Memory)
* Các câu lệnh SQL khởi tạo cấu trúc bảng phức tạp (như người dùng, sản phẩm, giỏ hàng, và các bảng vector embedding cho AI Chatbot) đòi hỏi thời gian thực thi I/O mạng liên tục.
* Do đó, hệ thống đã điều chỉnh bộ nhớ (Memory) lên **256 MB** và nâng thời gian chờ (Timeout) từ 3 giây mặc định lên **60 giây (1 phút)**, khắc phục hoàn toàn tình trạng lỗi ngắt kết nối giữa chừng (Timeout Exception).

![Cấu hình Timeout và Memory cho Lambda](/images/Worklog/Tuan11/lam3.png)

![Xác nhận lưu cấu hình General settings](/images/Worklog/Tuan11/lam4.png)

#### 4. Quản trị Cấu hình qua Biến môi trường (Environment Variables)
* Tách biệt cấu hình hạ tầng khỏi mã nguồn bằng cách thiết lập các cặp giá trị định danh: `DB_HOST`, `DB_NAME` (`pharmacare_ai`), `DB_PORT` (`5432`), và `RDS_SECRET_ARN`.
* Cơ chế này đảm bảo hàm Lambda tự động gọi API Secrets Manager để lấy chuỗi mật khẩu giải mã một cách động theo thời gian thực.

![Thiết lập biến môi trường cho Lambda](/images/Worklog/Tuan11/lam5.png)

#### 5. Đóng gói Mã nguồn & Triển khai lên Môi trường Cloud
* Tại môi trường VS Code cục bộ, viết mã nguồn `index.mjs` và cài đặt các thư viện phụ thuộc (`pg`, `@aws-sdk/client-secrets-manager`). Sử dụng lệnh PowerShell `Compress-Archive` để nén mã nguồn cùng `node_modules` thành gói `function.zip`.

![Khởi tạo và nén mã nguồn lần 1 trong VS Code](/images/Worklog/Tuan11/lam6.png)

* Thực hiện tải tệp `function.zip` trực tiếp lên tab Code của AWS Lambda Console.

![Giao diện tải tệp nén ZIP cập nhật Lambda lần 1](/images/Worklog/Tuan11/lam7.png)

* Rà soát và kiểm thử lại cấu trúc mã nguồn SQL Migration ngay trên giao diện trình biên soạn của Lambda Console để đảm bảo sự khớp nối hoàn hảo.

![Kiểm tra mã nguồn Lambda sau khi update lần 1](/images/Worklog/Tuan11/lam8.png)

* Khi có sự điều chỉnh hoặc bổ sung bảng dữ liệu mới, tiếp tục lặp lại chu trình nén tệp và tải lên để đồng bộ hóa cấu trúc cơ sở dữ liệu trên môi trường Cloud.

![Giao diện tải tệp nén ZIP cập nhật Lambda lần cuối](/images/Worklog/Tuan11/lam9.png)

![Mã nguồn Migration hoàn chỉnh sau khi cập nhật thành công](/images/Worklog/Tuan11/lam10.png)

#### 6. Triển khai Hệ thống Xác thực Tập trung Amazon Cognito User Pool
* **Khởi tạo User Pool & App Client:** Triển khai `pharmacare-user-pool` làm trung tâm quản lý tài khoản, hỗ trợ đầy đủ các tính năng xác minh qua Email/SMS và khôi phục mật khẩu. Tạo App Client cung cấp Client ID phục vụ tích hợp với giao diện Frontend ReactJS.
* **Phân quyền Role-Based Access Control (RBAC):** Khởi tạo 2 nhóm người dùng chiến lược:
  * `Admin`: Nhóm quản trị viên có đặc quyền quản lý sản phẩm, kho hàng và báo cáo.
  * `Customer`: Nhóm khách hàng được cấp quyền mua sắm và tương tác với AI Chatbot.
* **Tích hợp JWT Token:** Khi người dùng đăng nhập thành công, Cognito cấp phát JSON Web Token (JWT). Token này là chìa khóa định danh được Frontend gửi kèm trong mỗi HTTP Request tới API Gateway ở tuần tiếp theo.

---

### Kết quả đạt được:

* **Tự động hóa luồng Deployment Dữ liệu:** Thay thế hoàn toàn các thao tác tạo bảng DB thủ công tiềm ẩn rủi ro bằng kiến trúc Serverless Lambda chạy trong mạng kín VPC, đảm bảo tính nhất quán và bảo mật dữ liệu tuyệt đối.
* **Thiết lập nền tảng Bảo mật Định danh chuẩn Doanh nghiệp:** Xây dựng thành công hệ thống xác thực tập trung Amazon Cognito, sẵn sàng tích hợp với Frontend ReactJS và API Gateway.
* **Khả năng quan sát toàn diện (Observability):** Toàn bộ nhật ký thực thi migration, trạng thái tạo bảng và log xác thực người dùng đều được ghi nhận real-time trên **Amazon CloudWatch Logs**, giúp đội ngũ kỹ sư kiểm soát hệ thống chặt chẽ.