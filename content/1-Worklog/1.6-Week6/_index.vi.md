---
title: "Worklog Tuần 6"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---


### Mục tiêu triển khai:

* **Xây dựng kiến trúc High Availability (Tính sẵn sàng cao):** Đảm bảo ứng dụng FCJ Management luôn hoạt động ổn định và không bị gián đoạn (zero-downtime) ngay cả khi có sự cố xảy ra ở cấp độ máy chủ hay Data Center (Availability Zone).
* **Tự động hóa khả năng mở rộng (Auto Scaling):** Ứng dụng Amazon EC2 Auto Scaling để hệ thống tự động tăng số lượng máy chủ (Scale out) khi lưu lượng truy cập tăng đột biến và tự động giảm (Scale in) khi thấp điểm nhằm tối ưu hóa chi phí rỗi.
* **Phân phối tải thông minh (Load Balancing):** Thiết lập Elastic Load Balancing (ELB) để điều hướng luồng truy cập của người dùng một cách đồng đều đến các máy chủ khỏe mạnh, ngăn chặn tình trạng quá tải cục bộ.
* **Làm chủ quy trình đóng gói cấu hình:** Nắm vững cách sử dụng Launch Templates để chuẩn hóa và tự động hóa việc khởi tạo hàng loạt máy chủ EC2 với cùng một cấu hình tiêu chuẩn.

### Các công việc cần triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| :--- | :--- | :--- | :--- | :--- |
| 2 | **Nghiên cứu lý thuyết & Chuẩn bị môi trường:** <br> - Phân tích các chiến lược Scaling (Manual, Dynamic, Scheduled, Predictive). <br> - Xem xét lại kiến trúc của ứng dụng FCJ Management trên máy ảo cơ sở. | 01/06/2025 | 01/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | **Tạo Launch Template:** <br> - Đóng gói cấu hình máy chủ chuẩn (AMI chứa sẵn ứng dụng, Instance Type, Key Pair). <br> - Cấu hình Security Group và các thiết lập Network cơ bản cho Template. | 02/06/2025 | 02/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | **Thiết lập Cân bằng tải (Elastic Load Balancer):** <br> - Khởi tạo Target Group và cấu hình cơ chế Health Check. <br> - Tạo Application Load Balancer (ALB) public-facing, liên kết với Target Group. | 03/06/2025 | 03/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | **Cấu hình Amazon EC2 Auto Scaling Group (ASG):** <br> - Tạo ASG dựa trên Launch Template đã thiết lập. <br> - Xác định mạng (VPC/Subnets) và kết nối ASG với Load Balancer. <br> - Cấu hình chính sách Dynamic Scaling (vd: Target Tracking theo CPU utilization). | 04/06/2025 | 04/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | **Kiểm thử hệ thống & Dọn dẹp (Test & Cleanup):** <br> - Dùng công cụ tạo tải giả lập (Stress test) đẩy mức sử dụng CPU lên cao để kích hoạt Scale out. <br> - Ngừng tạo tải để quan sát quá trình Scale in. <br> - Xóa các tài nguyên (ELB, ASG, Launch Template) để tránh phát sinh chi phí. | 05/06/2025 | 05/06/2025 | <https://cloudjourney.awsstudygroup.com/> |

---

### Chi tiết quá trình thực hiện:

#### 1. Đóng gói cấu hình với Launch Templates
* Thay vì tạo EC2 thủ công, tiến hành cấu hình **Launch Template**. Đây là bản thiết kế chứa toàn bộ thông số kỹ thuật (AMI của máy ảo FCJ Management đã có sẵn ứng dụng, cấu hình mạng, tường lửa, và các tập lệnh khởi động User Data nếu có). 
* Việc sử dụng Launch Template kết hợp đánh dấu phiên bản (versioning) giúp dễ dàng quản lý vòng đời ứng dụng: khi có bản cập nhật code mới, chỉ cần tạo version mới cho template và ASG sẽ tự động dùng bản này cho các máy chủ sinh ra sau đó.

#### 2. Cấu hình phân luồng truy cập với Elastic Load Balancing (ELB)
* **Tạo Target Group:** Nhóm các tài nguyên đích sẽ xử lý request. Điểm mấu chốt ở bước này là thiết lập **Health Checks** (Kiểm tra sức khỏe). Cấu hình cho ELB liên tục gửi ping/request đến một đường dẫn cụ thể (vd: `/health`) của ứng dụng. Nếu máy chủ phản hồi mã lỗi (như 500) hoặc timeout, ELB sẽ đánh dấu máy chủ đó là "Unhealthy" và ngừng gửi traffic vào đó.
* **Tạo Load Balancer:** Triển khai một Application Load Balancer (ALB) nằm ở vùng Public Subnets. ALB này đóng vai trò là "mặt tiền" duy nhất của hệ thống, nhận toàn bộ request từ Internet (HTTP/HTTPS) và phân phối tĩnh tiến/động tiến (round-robin hoặc routing theo path) xuống Target Group.

#### 3. Thiết lập độ co giãn với Auto Scaling Group (ASG)
* Khởi tạo ASG sử dụng Launch Template vừa tạo. Cấu hình ASG trải dài trên nhiều Availability Zones để tăng tính dự phòng.
* **Tích hợp ALB:** Liên kết ASG với Target Group của Load Balancer. Khi ASG sinh ra một máy chủ mới, máy chủ này sẽ tự động được đăng ký (register) vào Target Group để sẵn sàng nhận tải.
* **Chiến lược mở rộng (Scaling Policies):** Áp dụng chiến lược **Dynamic Scaling** (mở rộng động) sử dụng phương pháp **Target Tracking**. Cụ thể, thiết lập ngưỡng CPU Utilization trung bình của toàn nhóm là 50%.
    * Nếu CPU > 50%: Hệ thống tự động thêm máy (Scale out).
    * Nếu CPU < 50%: Hệ thống tự động xóa bớt máy (Scale in) sau một khoảng thời gian chờ (Cooldown period).

#### 4. Kịch bản Kiểm thử (Testing)
* Copy DNS Name của Load Balancer dán vào trình duyệt, xác nhận ứng dụng FCJ Management load thành công. Truy cập nhiều lần để thấy request được luân chuyển giữa các máy chủ khác nhau (nhận diện qua Private IP hiển thị trên trang web).
* Đăng nhập vào một máy chủ EC2, sử dụng công cụ `stress` để đẩy mức tiêu thụ CPU lên 100%. 
* Quan sát trên CloudWatch và giao diện Auto Scaling: Hệ thống lập tức kích hoạt cảnh báo Alarm, ASG tự động `Launching a new EC2 instance`. Khi tắt lệnh stress, một lúc sau hệ thống tự động `Terminating EC2 instance` đưa trạng thái về ban đầu (Desired Capacity).

---

### Kết quả đạt được:

* **Hệ thống tự phục hồi và đáp ứng linh hoạt:** Kiến trúc triển khai đã chứng minh được khả năng chịu tải thông minh. Quá trình kiểm thử cho thấy khi lượng truy cập mô phỏng tăng cao, hệ thống không hề bị sập (crash) mà tự động nới rộng quy mô sức mạnh tính toán một cách mượt mà.
* **Tối ưu hóa hiệu năng & Chi phí tài nguyên:** Giải quyết bài toán cân bằng giữa hiệu suất và chi phí. AWS Auto Scaling đảm bảo doanh nghiệp chỉ phải trả tiền cho số lượng máy chủ thực sự cần thiết tại từng thời điểm.
* **Bảo mật hạ tầng Backend:** Việc sử dụng Load Balancer làm điểm tiếp nhận traffic cho phép giấu hoàn toàn các máy chủ EC2 chứa ứng dụng vào Private Subnet, ngăn chặn các cuộc tấn công trực tiếp từ bên ngoài Internet vào máy chủ hệ thống.