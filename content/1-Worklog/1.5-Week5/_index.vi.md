---
title: "Worklog Tuần 5"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu triển khai:

* **Làm chủ dịch vụ Compute cốt lõi:** Nắm vững các kỹ năng vận hành và quản trị vòng đời của máy chủ ảo Amazon EC2 (Elastic Compute Cloud) trên AWS.
* **Đảm bảo an toàn và tính linh hoạt dữ liệu:** Thực hành các thao tác sao lưu dự phòng (Backup) và nhân bản hệ thống thông qua việc tạo bản ghi trạng thái ổ cứng (EBS Snapshots) và đóng gói hệ điều hành (Amazon Machine Images - AMI).
* **Kỹ năng xử lý sự cố (Troubleshooting):** Thực hiện thành công các kịch bản khôi phục quyền truy cập máy chủ (trên cả Windows và Linux) khi bị mất khóa bảo mật (Key Pairs).
* **Triển khai ứng dụng thực tế (Application Deployment):** Tự tay thiết lập môi trường máy chủ (LAMP Stack, Node.js) và đưa một ứng dụng Web Full-stack (AWS User Management Application) lên môi trường đám mây để phục vụ người dùng.

### Các công việc cần triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| :--- | :--- | :--- | :--- | :--- |
| 2 | **Chuẩn bị hạ tầng & Bảo mật (Preparation):** <br> - Khởi tạo VPC cô lập cho môi trường Windows và Linux. <br> - Thiết lập Security Groups làm tường lửa trạng thái (Stateful Firewall), cấu hình các quy tắc chặn/mở port nghiêm ngặt. | 24/05/2025 | 24/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | **Quản lý vòng đời máy chủ EC2 (Phần 1):** <br> - Thực hành thay đổi cấu hình phần cứng máy chủ (Modify Instance Type). <br> - Tạo và quản lý sao lưu dữ liệu ổ cứng (EBS Snapshots). <br> - Đóng gói máy chủ thành Custom AMI. <br> - Khởi chạy máy chủ mới hoàn toàn từ Custom AMI vừa tạo. | 25/05/2025 | 25/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | **Xử lý sự cố & Truy cập từ xa (Phần 2):** <br> - Thực hiện kịch bản khôi phục quyền truy cập cho EC2 Linux khi mất Key Pair. <br> - Thực hiện kịch bản khôi phục quyền truy cập cho EC2 Windows. <br> - Cấu hình giao diện Remote Desktop (RDP) để điều khiển máy chủ EC2-Ubuntu. | 26/05/2025 | 26/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | **Thiết lập môi trường Web Server:** <br> - Cài đặt ngăn xếp LAMP (Linux, Apache, MySQL, PHP) trên Amazon Linux 2023. <br> - Chạy kịch bản `mysql_secure_installation` để bảo mật database. <br> - Cài đặt môi trường Node.js Runtime (sử dụng phiên bản LTS). | 28/05/2025 | 28/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | **Triển khai ứng dụng quản lý người dùng (CRUD App):** <br> - Clone source code ứng dụng (Node.js, Express) từ repository. <br> - Cài đặt thư viện (npm) và cấu hình chuỗi kết nối Database. <br> - Cấu hình Security Group mở port `3000`. <br> - Khởi chạy server và kiểm thử các tính năng CRUD. | 29/05/2025 | 29/05/2025 | <https://cloudjourney.awsstudygroup.com/> |

---

### Chi tiết quá trình thực hiện:

#### 1. Chuẩn bị nền tảng mạng và Tường lửa (Preparation)
* **VPC Isolation:** Xây dựng các không gian mạng (VPC) riêng biệt để phục vụ cho việc khởi chạy các hệ điều hành khác nhau (Microsoft Windows Server 2025 và Amazon Linux 2023), tránh xung đột cấu hình mạng.
* **Cấu hình Security Group:** Thiết lập các chốt chặn bảo mật ở cấp độ máy chủ. Quá trình này đặc biệt lưu ý đến nguyên tắc đặc quyền tối thiểu: chỉ mở cổng `22` (SSH) đối với Linux, cổng `3389` (RDP) đối với Windows cho các IP quản trị được chỉ định, và khóa toàn bộ các luồng truy cập không hợp lệ để ngăn chặn rủi ro rà quét cổng.

#### 2. Vận hành và Quản trị Amazon EC2 cơ bản
* **Scale Up/Down phần cứng:** Thực hiện thao tác Modify Instance Type để thay đổi quy mô tài nguyên (CPU, RAM) của máy chủ đang chạy mà không làm mất dữ liệu, đáp ứng nhu cầu linh hoạt của ứng dụng.
* **Chiến lược Backup (EBS & AMI):**
    * Chụp ảnh trạng thái ổ cứng (Create EBS Snapshot) để lưu trữ các điểm khôi phục dữ liệu an toàn.
    * Từ máy chủ đang hoạt động ổn định, tiến hành "đóng băng" và tạo thành một Amazon Machine Image (Custom AMI). Sử dụng AMI này để khởi tạo nhanh hàng loạt máy chủ có cùng cấu hình, tiết kiệm thời gian cài đặt lại từ đầu.
* **Disaster Recovery (Khôi phục thảm họa):** Giả lập tình huống quản trị viên làm mất tệp tin `.pem` (khóa bảo mật). Thực hành các thủ thuật can thiệp vào volume hoặc sử dụng User Data để reset thông tin đăng nhập, qua đó lấy lại thành công quyền kiểm soát máy chủ Linux và Windows.

#### 3. Triển khai ứng dụng Web Full-stack trên Cloud
* **Xây dựng nền móng (LAMP Stack):** Cài đặt thành công hệ sinh thái LAMP. Đặc biệt chú trọng bước thiết lập mật khẩu root mạnh và xóa bỏ các database test mặc định thông qua lệnh `mysql_secure_installation` nhằm vá các lỗ hổng bảo mật cơ sở dữ liệu sơ đẳng nhất.
* **Thiết lập Node.js:** Cài đặt Node.js môi trường LTS (Long Term Support) để đảm bảo ứng dụng nhận được các bản cập nhật bảo mật liên tục trong quá trình vận hành (Production).
* **Deploy Application:** * Tải mã nguồn ứng dụng *AWS User Management Application* (xây dựng bằng Express và Express-Handlebars).
    * Thiết lập kết nối an toàn giữa tầng backend (Node.js) và tầng database (MySQL/MariaDB).
    * Tinh chỉnh lại Security Group của máy chủ EC2: bổ sung rule cho phép luồng Inbound Traffic qua port `3000` (cổng mặc định của ứng dụng Node.js) để người dùng cuối có thể truy cập thông qua trình duyệt web.

---

### Kết quả đạt được:

* **Quản trị Compute toàn diện:** Đã nắm rõ cách thức cấu hình, thay đổi tài nguyên và tự động hóa việc nhân bản máy chủ EC2 thông qua AMI, sẵn sàng cho việc xây dựng các kiến trúc có khả năng phục hồi cao.
* **Làm chủ rủi ro vận hành:** Tự tin xử lý các tình huống khó như mất thông tin xác thực (Credentials) mà không làm gián đoạn hoặc mất mát dữ liệu hiện có trên máy chủ.
* **Hoàn thiện luồng triển khai phần mềm (Deployment Flow):** Đã kết hợp thành công giữa kiến thức quản trị hạ tầng Cloud (EC2, Security Groups) và kỹ năng phát triển Web (Thiết lập môi trường, cấu hình kết nối DB). Ứng dụng quản lý người dùng đã được đưa lên Internet hoạt động trơn tru, đáp ứng đầy đủ các thao tác CRUD (Create, Read, Update, Delete) trên giao diện tương tác thực tế.