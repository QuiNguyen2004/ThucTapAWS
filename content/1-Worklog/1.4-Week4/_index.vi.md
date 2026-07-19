---
title: "Worklog Tuần 4"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu triển khai:

* **Xây dựng hạ tầng mạng lõi trên AWS:** Thiết lập Amazon VPC theo chuẩn "production-ready", phân hoạch mạng rõ ràng giữa các vùng public/private và đảm bảo tính sẵn sàng cao (High Availability) bằng cách triển khai trên nhiều Availability Zones (Multi-AZ).
* **Tối ưu hóa bảo mật và quản trị:** Triển khai các máy chủ ảo (EC2) an toàn, áp dụng nguyên tắc đặc quyền tối thiểu (least privilege) với Security Groups và loại bỏ hoàn toàn việc mở cổng SSH (port 22) ra Internet bằng cách sử dụng AWS Systems Manager (SSM).
* **Xây dựng hệ thống mạng lai (Hybrid Cloud):** Thiết lập và cấu hình đường hầm bảo mật AWS Site-to-Site VPN (sử dụng giao thức IPsec) để kết nối an toàn giữa mạng nội bộ doanh nghiệp (On-premise) và đám mây AWS.
* **Giám sát và phân tích lưu lượng:** Bật và cấu hình VPC Flow Logs để thu thập dữ liệu gói tin mạng, tạo tiền đề cho việc phân tích an toàn thông tin và tích hợp với các hệ thống SIEM giám sát sự cố sau này.

### Các công việc cần triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| :--- | :--- | :--- | :--- | :--- |
| 2 | **Chuẩn bị hạ tầng mạng (VPC & Subnets):** <br> - Khởi tạo VPC chính với dải mạng `10.10.0.0/16`. <br> - Phân bổ 4 Subnets qua 2 AZs (AZ 1 & AZ 2) để đảm bảo dự phòng. <br> - Gắn Internet Gateway (IGW) cho kết nối mạng ngoài. <br> - Thiết lập NAT Gateway tại Public Subnet cho phép Private Subnet truy cập Internet một chiều. | 18/05/2025 | 18/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | **Định tuyến & Thiết lập Tường lửa ảo:** <br> - Tạo và cấu hình Route Tables (Public điều hướng ra IGW, Private điều hướng ra NAT). <br> - Thiết lập Security Groups (cấu hình Inbound/Outbound rules chặt chẽ). <br> - Kích hoạt VPC Flow Logs đẩy log về CloudWatch. | 19/05/2025 | 19/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | **Triển khai Amazon EC2 Instances:** <br> - Khởi chạy EC2 Public (chạy Web server/Bastion) và EC2 Private (Backend/Database). <br> - Thiết lập EC2 Instance Connect Endpoint (EIC). <br> - Cấu hình IAM Role để kết nối máy chủ an toàn qua Session Manager. | 20/05/2025 | 20/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | **Cấu hình Gateway cho Site-to-Site VPN:** <br> - Khởi tạo Virtual Private Gateway (VPG) và Attach vào VPC hiện tại. <br> - Tạo Customer Gateway (CGW) khai báo IP Public của môi trường On-premise. <br> - Thiết lập máy chủ giả lập On-premise (sử dụng OpenSwan). | 21/05/2025 | 21/05/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | **Hoàn thiện VPN Tunnel & Kiểm thử toàn hệ thống:** <br> - Thiết lập Site-to-Site VPN Connection, liên kết VPG và CGW. <br> - Lựa chọn cơ chế định tuyến (Static Routing/BGP). <br> - Sử dụng VPC Reachability Analyzer để kiểm tra đường đi của gói tin. <br> - Thực hiện Ping test qua lại giữa các Private IP của 2 môi trường. | 22/05/2025 | 22/05/2025 | <https://cloudjourney.awsstudygroup.com/> |

---

### Chi tiết quá trình thực hiện:

#### 1. Xây dựng nền tảng mạng (Amazon VPC)
* **Không gian mạng (CIDR):** Đã tạo một VPC cô lập hoàn toàn với dải IPv4 là `10.10.0.0/16`.
* **Chia nhỏ mạng (Subnetting):** Để đảm bảo tính sẵn sàng cao, hệ thống được chia thành 4 subnet rải đều trên 2 Availability Zones:
    * **AZ 1:** Public Subnet (`10.10.1.0/24`) và Private Subnet (`10.10.3.0/24`).
    * **AZ 2:** Public Subnet (`10.10.2.0/24`) và Private Subnet (`10.10.4.0/24`).
* **Cửa ngõ Internet (Gateways):** * Khởi tạo **Internet Gateway (IGW)** và gắn vào VPC để cung cấp đường ra Internet cho các tài nguyên ở vùng Public.
    * Triển khai **NAT Gateway** (cần cấp phát Elastic IP) đặt tại Public Subnet của AZ 1. Công cụ này đóng vai trò sống còn giúp các máy chủ nằm sâu trong vùng Private có thể tải bản vá lỗi bảo mật hoặc thư viện từ Internet mà không bị lộ địa chỉ IP thật ra bên ngoài.

#### 2. Phân luồng định tuyến và bảo mật lớp mạng
* **Route Tables:** * *Public Route Table:* Thêm route `0.0.0.0/0` trỏ tới IGW.
    * *Private Route Table:* Thêm route `0.0.0.0/0` trỏ tới NAT Gateway.
* **Security Group (Stateful Firewall):** Thiết lập các quy tắc tường lửa ảo ở cấp độ Instance. Chỉ cho phép các luồng traffic cần thiết (ví dụ: HTTP/HTTPS từ mọi nơi vào Public EC2, SSH/ICMP chỉ từ vùng Public mạng nội bộ vào Private EC2) và từ chối mọi yêu cầu khả nghi.
* **Networking Monitoring:** Kích hoạt **VPC Flow Logs**, toàn bộ thông tin về IP nguồn, IP đích, port và giao thức (Accept/Reject) đều được ghi nhận lại, phục vụ đắc lực cho công tác truy vết nếu xảy ra tấn công mạng.

#### 3. Triển khai máy chủ và kết nối an toàn (Amazon EC2)
* Khởi chạy các máy chủ Amazon Linux 2023 vào đúng các Subnet đã quy hoạch.
* Thay vì cấu hình mở port 22 (SSH) tiềm ẩn nhiều rủi ro bị brute-force, quá trình thực hành áp dụng giải pháp **AWS Systems Manager (SSM) Session Manager**. Giải pháp này cho phép mở luồng terminal trực tiếp trên trình duyệt một cách an toàn thông qua IAM Role, hoàn toàn cách ly máy chủ khỏi các rủi ro từ Internet.

#### 4. Xây dựng đường hầm IPsec (Site-to-Site VPN)
* **Khởi tạo điểm neo:** * Ở phía AWS: Tạo **Virtual Private Gateway (VPG)**. VPG đóng vai trò là điểm kết cuối (endpoint) trên mép mạng của AWS VPC.
    * Ở phía khách hàng (On-premise): Tạo **Customer Gateway (CGW)** để định danh thiết bị mạng ngoại vi.
* **Xây dựng đường hầm (Tunnel):** Khởi tạo VPN Connection liên kết giữa VPG và CGW. Mỗi VPN Connection mặc định cung cấp 2 đường hầm (Tunnels) hoạt động độc lập để đảm bảo High Availability trong trường hợp một đường truyền vật lý gặp sự cố.
* **Cấu hình thiết bị trạm:** Cài đặt và tinh chỉnh cấu hình IPsec trên máy chủ giả lập mạng nội bộ (sử dụng phần mềm mã nguồn mở **OpenSwan**). Cấu hình Pre-Shared Key (PSK) và các thuật toán mã hóa (Encryption/Authentication algorithms) sao cho khớp đồng bộ với thông số kỹ thuật do AWS sinh ra.

---

### Kết quả đạt được:

* **Hoàn thiện hạ tầng mạng vững chắc:** Tự tay thiết kế và cấu hình thành công một kiến trúc mạng Multi-AZ trên AWS, hiểu sâu sắc vòng đời của một gói tin từ Internet đi qua IGW, qua sự phân luồng của Route Table, sự kiểm duyệt của Security Group để đến được EC2.
* **Xác thực kết nối VPN thành công:** Hai hệ thống mạng độc lập (VPC `10.10.x.x` và mạng On-premise mô phỏng `10.11.x.x`) đã được "nối" lại với nhau.
* **Kiểm thử thực tế (Testing):** Đã thực hiện lệnh `ping` trực tiếp từ một máy chủ Private EC2 bên trong AWS VPC đến địa chỉ IP nội bộ của máy chủ On-premise. Gói tin ICMP phản hồi thành công (Reply received) chứng minh đường hầm IPsec đang hoạt động mã hóa dữ liệu hoàn hảo, đáp ứng đúng kịch bản mở rộng Data Center của các doanh nghiệp thực tế.