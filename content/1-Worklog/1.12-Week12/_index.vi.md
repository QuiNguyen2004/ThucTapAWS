---
title: "Worklog Tuần 12"
date: 2026-07-17
weight: 4
chapter: false
pre: " <b> 1.12. </b> "
---

### Mục tiêu triển khai:

* **Xây dựng Backend Serverless Core API:** Khởi tạo hàm AWS Lambda `pharmacare-backend-api` (Node.js 22.x) hoạt động bên trong Private Subnet của VPC, đóng vai trò xử lý toàn bộ logic nghiệp vụ cốt lõi (danh mục sản phẩm, cửa hàng, giỏ hàng, đơn hàng và thông tin cá nhân) cho hệ thống **PharmaCare**.
* **Triển khai Cổng giao tiếp API Gateway (HTTP API):** Thiết lập Amazon API Gateway HTTP API làm cổng tiếp nhận yêu cầu từ client, cấu hình định tuyến (routing) cho các endpoint public/private và quản lý chính sách CORS tương thích với môi trường Frontend (ReactJS/Vite).
* **Tích hợp Bảo mật tầng API với Cognito JWT Authorizer:** Cấu hình authorizer `pharmacare-cognito-authorizer` trên API Gateway sử dụng JSON Web Token (JWT) phát hành bởi Amazon Cognito, giúp khóa chặt các route dữ liệu cá nhân (`/profile`, `/cart`, `/orders`) theo nguyên tắc Zero Trust.
* **Kiểm thử tích hợp End-to-End (E2E Testing):** Xây dựng ứng dụng kiểm thử Frontend bằng ReactJS (Vite), tích hợp xác thực OpenID Connect (`react-oidc-context`) để kiểm tra toàn bộ luồng giao tiếp từ Client -> API Gateway (JWT Validation) -> Lambda (Secrets Manager) -> RDS PostgreSQL.

### Các công việc cần triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành |
| :--- | :--- | :--- | :--- |
| 2 | **Khởi tạo & Thiết lập Tài nguyên Lambda Backend:** <br> - Tạo hàm Lambda `pharmacare-backend-api` kết nối vào Private Subnet và gán Security Group. <br> - Tối ưu cấu hình: Memory `256 MB`, Ephemeral storage `512 MB`, Timeout `10 giây` và gán thực thi quyền IAM `pharmacare-lambda-role`. | 10/07/2026 | 10/07/2026 |
| 3 | **Phát triển Mã nguồn & Quản trị Connection Pool:** <br> - Khởi tạo project Node.js cục bộ, cài đặt các thư viện `pg` và `@aws-sdk/client-secrets-manager`. <br> - Viết logic xử lý routing trong `index.mjs`, cấu hình đọc secret `RDS_SECRET_ARN` và nén gói `function.zip` deploy lên Lambda. | 11/07/2026 | 11/07/2026 |
| 4 | **Triển khai Amazon API Gateway HTTP API & CORS:** <br> - Khởi tạo HTTP API, tích hợp integration target với hàm Lambda `pharmacare-backend-api`. <br> - Khai báo các route public (`/health`, `/products`, `/categories`, `/stores`) trên stage `$default` và cấu hình chính sách CORS cho localhost. | 12/07/2026 | 12/07/2026 |
| 5 | **Tích hợp Cognito JWT Authorizer & Khóa Protected Routes:** <br> - Khởi tạo `pharmacare-cognito-authorizer` kết nối với User Pool và App Client. <br> - Gắn Authorizer vào nhóm các route bảo mật (`/profile`, `/cart/*`, `/orders/*`) để từ chối các request thiếu Bearer Token hợp lệ. | 13/07/2026 | 13/07/2026 |
| 6 | **Xây dựng ReactJS Test Interface & Kiểm thử tích hợp:** <br> - Khởi tạo project ReactJS bằng Vite, cấu hình đăng nhập OIDC với tài khoản Customer. <br> - Thực thi các kịch bản kiểm thử API public/private, kiểm toán phản hồi JSON và rà soát nhật ký hệ thống trên CloudWatch. | 14/07/2026 | 14/07/2026 |

---

### Chi tiết quá trình thực hiện:

Trong tuần này, trọng tâm kiến trúc dịch chuyển sang việc kết nối các khối hạ tầng rời rạc (VPC, RDS, Cognito) thành một luồng backend hoàn chỉnh, tuân thủ mô hình Serverless Modern API:

#### 1. Xây dựng và Tối ưu hóa Lambda Backend Serverless
* **Khởi tạo hàm Lambda:** Tạo hàm mới với định danh `pharmacare-backend-api` (runtime Node.js 22.x) nằm hoàn toàn trong Private App Subnets của VPC, đảm bảo cô lập với Internet công cộng.

![Tổng quan hàm Lambda backend](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/01-lambda-overview.png)

* **Cấu hình tài nguyên thực thi:** Vào mục General configuration, điều chỉnh bộ nhớ Memory lên `256 MB`, Ephemeral storage `512 MB`, nâng Timeout lên `10 giây` và gán IAM Role `pharmacare-lambda-role` nhằm đủ thời gian cho việc khởi tạo kết nối DB và đọc secret.

![Cấu hình Memory, Timeout và IAM Role](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/02-general-configuration.png)

* **Thiết lập biến môi trường:** Cấu hình các key `DB_NAME` (`pharmacare_ai`) và `RDS_SECRET_ARN` để Lambda tự động truy xuất mật khẩu cơ sở dữ liệu từ AWS Secrets Manager thay vì ghi tĩnh vào code.

![Cấu hình biến môi trường cho Lambda](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/03-environment-variables.png)

#### 2. Phát triển Mã nguồn & Đóng gói Triển khai
* Khởi tạo dự án Node.js tại VS Code, cài đặt thư viện `pg` (PostgreSQL client) và `@aws-sdk/client-secrets-manager`.

![Khởi tạo project và cài đặt thư viện Node.js](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/04-backend-project-dependencies.png)

* Viết mã nguồn `index.mjs` phân tích HTTP method và path để định tuyến truy vấn DB, sau đó nén toàn bộ mã nguồn cùng `node_modules` thành tệp `function.zip` bằng PowerShell.

![Đóng gói source code thành function.zip](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/05-package-lambda-code.png)

* Tải tệp `function.zip` lên AWS Lambda Console và nhấn **Deploy** để áp dụng phiên bản mã nguồn backend mới nhất.

![Mã nguồn backend sau khi tải lên Lambda](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/06-upload-lambda-code.png)

#### 3. Khởi tạo Cổng Giao tiếp Amazon API Gateway HTTP API
* Truy cập API Gateway Console, lựa chọn loại **HTTP API** nhằm tối ưu hóa chi phí và tốc độ phản hồi cho mô hình Serverless.

![Chọn loại Amazon API Gateway HTTP API](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/07-select-http-api.png)

* Cấu hình Integration target liên kết trực tiếp HTTP API với hàm Lambda `pharmacare-backend-api`.

![Tích hợp HTTP API với Lambda backend](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/08-configure-api-integration.png)

* Khai báo nhóm các route công khai (Public Routes) không yêu cầu xác thực như: `GET /health`, `GET /products`, `GET /categories` và `GET /stores`.

![Khai báo các route public](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/09-configure-public-routes.png)

* Thiết lập stage `$default` và bật tính năng **Auto-deploy** để mọi thay đổi về route được tự động áp dụng real-time.

![Cấu hình stage mặc định cho HTTP API](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/10-define-default-stage.png)

![Kiểm tra cấu hình trước khi tạo API](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/11-review-create-api.png)

* Xác nhận danh sách các endpoint route đã được khởi tạo thành công trên hệ thống.

![Danh sách route sau khi tạo HTTP API](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/12-api-routes-created.png)

* Cấu hình chính sách **CORS** cho phép các origin của Frontend local (`http://localhost:5173`) đi kèm các HTTP methods và header `Authorization`.

![Cấu hình CORS cho ReactJS](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/13-configure-cors.png)

#### 4. Bảo mật Tầng API với Cognito JWT Authorizer
* Khởi tạo Authorizer mới thuộc loại **JWT**, liên kết trực tiếp tới Identity source là header `Authorization` đối chiếu với Issuer URL của Cognito User Pool và Audience của App Client.

![Tạo Cognito JWT Authorizer](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/14-create-jwt-authorizer.png)

![JWT Authorizer sau khi được tạo](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/15-authorizer-created.png)

* Tiến hành gắn Authorizer (`pharmacare-cognito-authorizer`) vào nhóm các route bảo mật riêng tư (`/profile`, `/cart`, `/orders`), từ chối mọi yêu cầu truy cập trái phép hoặc không có token.

![Gắn Cognito Authorizer vào route GET profile](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/16-attach-authorizer-route.png)

* Kiểm thử quá trình đăng nhập và trả về mã xác thực thông qua tài khoản Customer trên hệ thống Cognito.

![Kiểm tra đăng nhập bằng tài khoản Cognito](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/17-cognito-login-test.png)

#### 5. Kiểm thử Tích hợp End-to-End với ReactJS Frontend
* Khởi tạo dự án Frontend ReactJS bằng Vite, cài đặt thư viện `react-oidc-context` và khởi chạy server phát triển local.

![Tạo ứng dụng ReactJS bằng Vite](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/18-create-react-vite.png)

![Ứng dụng ReactJS chạy trên máy local](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/19-react-development-server.png)

* Xây dựng mã nguồn giao diện kiểm thử trong `App.jsx`, hỗ trợ gửi request kèm HTTP Header `Authorization: Bearer <access_token>`.

![Code giao diện ReactJS dùng để kiểm thử](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/20-react-test-interface-code.png)

* **Kết quả thực thi E2E:** Giao diện kiểm thử hiển thị đầy đủ phản hồi JSON từ hạ tầng AWS: các API public hoạt động tự do, trong khi các API protected (như `/profile`, `/cart`) chỉ phản hồi chính xác khi người dùng đã đăng nhập và gửi kèm chuỗi JWT Token hợp lệ.

![Kết quả kiểm thử frontend, API Gateway và Lambda](/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/21-api-test-result.png)

---

### Kết quả đạt được:

* **Hoàn thiện luồng Backend Serverless bảo mật kép:** Đã kết nối thành công chuỗi giao tiếp từ Client -> API Gateway -> Lambda -> Secrets Manager -> RDS PostgreSQL.
* **Tối ưu hóa hiệu năng và bảo mật:** Áp dụng nguyên tắc Zero Trust bằng cách kiểm tra quyền hạn ngay tại cổng API Gateway với Cognito JWT Authorizer, đồng thời giữ máy chủ DB an toàn tuyệt đối bên trong Private Subnet.
* **Chuẩn bị sẵn sàng cho Frontend & AI:** Bộ API cốt lõi đã hoàn toàn ổn định, cung cấp đầy đủ các endpoint để tích hợp giao diện e-commerce chính thức và tính năng AI RAG Chatbot.