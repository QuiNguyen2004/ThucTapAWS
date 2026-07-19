---
title: "Worklog Tuần 3"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---


### Mục tiêu tuần 3:

* Kết nối, làm quen với các thành viên trong First Cloud AI Journey.
* Hiểu dịch vụ AWS cơ bản, cách dùng console & CLI.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| :---: | :--- | :---: | :---: | :--- |
| **1** | - Tìm hiểu tổng quan về Amazon VPC (Virtual Private Cloud) và dải địa chỉ IP (CIDR block).<br>- Nghiên cứu mô hình phân vùng Subnet theo Availability Zones. | 14/05/2026 | 14/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **2** | - Học Module 3: AWS Networking.<br>- Phân biệt giữa Public Subnet và Private Subnet.<br>- Tìm hiểu cơ chế hoạt động của Internet Gateway (IGW) và NAT Gateway. | 15/05/2026 | 15/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **3** | - Tìm hiểu cơ chế định tuyến lưu lượng (Routing).<br>- Cách cấu hình và kết nối Route Tables cho các Subnet.<br>- So sánh tường lửa bảo mật: Security Groups (Stateful) vs Network ACLs (Stateless). | 16/05/2026 | 16/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **4** | - **Thực hành Lab:**<br>&emsp; + Tự tạo và cấu hình một VPC tùy chỉnh từ đầu.<br>&emsp; + Thiết lập Public Subnets, Route Tables và gắn Internet Gateway.<br>&emsp; + Tái khởi chạy ứng dụng *Employee Directory* vào VPC vừa cấu hình. | 17/05/2026 | 17/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **5** | - Tổng hợp kiến thức mạng AWS.<br>- Hoàn thành **Module 3 Knowledge Check** và nhận chứng nhận kỹ năng AWS Technical Essentials. | 18/05/2026 | 18/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

---

### Quá trình học tập và tích lũy kiến thức:

Trọng tâm của tuần này là sự chuyển dịch từ việc tạo ra các máy chủ (Compute) sang việc xây dựng "ngôi nhà" (Network) an toàn cho chúng. Quá trình học tập tập trung vào việc hiểu cách các thành phần mạng giao tiếp và bảo vệ lẫn nhau trong môi trường đám mây.

**1. Phương pháp tiếp cận kiến thức:**
* **Học theo mô hình Lớp (Layered Approach):** Tiếp cận kiến thức từ ngoài vào trong: Region -> VPC -> Availability Zone -> Subnet -> EC2 Instance.
* **Tư duy Security First:** Luôn đặt câu hỏi "Làm sao để bảo mật?" ở mỗi thành phần mạng (VPC, Subnet, Instance).
* **Thực hành có chủ đích:** Bài Lab *Relaunching the Employee Directory Application* không chỉ là việc tạo lại ứng dụng mà là việc đưa ứng dụng đó vào một môi trường mạng do chính mình thiết kế và kiểm soát.

**2. Các kiến thức trọng tâm cần ghi nhớ (Key Takeaways từ Module 3):**

* **Amazon VPC (Virtual Private Cloud):** Đây là mạng ảo dành riêng cho tài khoản AWS của mình. Nó cô lập về mặt logic với các mạng ảo khác trên đám mây AWS, cho phép kiểm soát hoàn toàn môi trường mạng ảo (chọn dải IP, tạo Subnet, cấu hình Route Table).
![Mô hình triển khai điện toán đám mây](/images/Worklog/AmazonVPC.jpg)
* **Subnetting & Phân vùng mạng:**
    * *Public Subnet:* Nơi đặt các tài nguyên cần truy cập Internet trực tiếp (ví dụ: Web Server, Load Balancer). Phải được gắn một route hướng ra Internet Gateway.
    * *Private Subnet:* Nơi đặt các tài nguyên nội bộ, không cho phép truy cập trực tiếp từ Internet (ví dụ: Database, Backend App). Để Private Subnet kết nối Internet (ví dụ tải bản cập nhật) một cách an toàn, cần dùng NAT Gateway.
 ![Creating a subnet](/images/Worklog/Creatingsubnet.jpg)
* **Định tuyến (Routing):** *Route Tables* là bộ não định tuyến của mạng. Nó chứa các quy tắc (routes) xác định lưu lượng mạng từ Subnet hoặc Gateway sẽ được hướng đi đâu.
* **Bảo mật mạng (VPC Security) :**
    * *Security Groups (SG):* Hoạt động ở cấp độ Instance (máy chủ EC2). Nó có tính trạng thái (stateful) - nếu cho phép request đi vào thì response tương ứng sẽ tự động được cho phép đi ra.
    * *Network Access Control Lists (NACLs):* Hoạt động ở cấp độ Subnet. Nó phi trạng thái (stateless) - bạn phải cấu hình tường minh cả luật inbound và outbound.

### Kết quả đạt được tuần 3:

* Hoàn thành xuất sắc toàn bộ lý thuyết và bài kiểm tra **Module 3: AWS Networking**.
* Nắm vững cơ chế hoạt động của Amazon VPC và cách phân bổ tài nguyên theo kiến trúc đa vùng khả dụng (Multi-AZ).
* Biết cách thiết kế và cấu hình mạng lưới (Subnet, Route Table, Internet Gateway) để định tuyến lưu lượng truy cập chính xác.
* Hiểu rõ sự khác biệt và cách kết hợp giữa Security Groups và Network ACLs để tạo ra các lớp phòng thủ mạng nhiều tầng.
* Thực hiện thành công việc tái khởi chạy ứng dụng "Employee Directory" trong môi trường EC2 đã được tùy chỉnh mạng, kết hợp giữa kiến thức Compute của tuần 2 và Networking của tuần 3.
 ![Chứng chỉ](/images/Worklog/AWSTechnicalEssentials.jpg)