---
title: "Event 2"
date: 2026-07-09
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Bài thu hoạch “AWS First Cloud Journey: Từ Ảo hóa, Serverless đến AI trong Bảo mật”

### Mục Đích Của Sự Kiện

- Khám phá hành trình chuyển dịch lên nền tảng điện toán đám mây (Cloud Computing) với AWS.
- Phân tích sâu về kiến trúc hệ thống hiện đại: so sánh giữa Virtualization (Máy ảo) và Containerization (Docker).
- Nhận diện các thách thức thực tế khi triển khai kiến trúc Serverless (AWS Lambda, DynamoDB).
- Ứng dụng Machine Learning kết hợp với AWS WAF để xây dựng hệ thống phát hiện xâm nhập mạng (NIDS).
- Định hướng nghề nghiệp và chia sẻ kinh nghiệm thực tế từ lộ trình Helpdesk/Sysadmin tiến tới DevOps.

### Danh Sách Diễn Giả & Thời Gian

- **09:20 - 10:00 | Nguyễn Quốc Bảo** - **10:00 - 10:35 | Nguyễn Huỳnh Quốc Bảo** - **10:35 - 10:50 | Việt Phát** - **10:50 - 11:10 | Lê Hoàng Gia Đại** - Chuyên gia Bảo mật (Chủ đề: WAF + ML for Cyber Attack Detection)
- **11:10 - 12:00 | Trần Trung Vinh** - (Chủ đề: Hành trình nghề nghiệp từ Sysadmin đến DevOps & Câu chuyện phỏng vấn tại Central Retail Group)

*(Ghi chú: Các chủ đề kỹ thuật về Container và Serverless được phân bổ trong các phiên sáng của các diễn giả Quốc Bảo, Huỳnh Quốc Bảo và Việt Phát).*

### Nội Dung Nổi Bật

#### 1. Cuộc chiến Kiến trúc: Virtual Machines vs Containers
- **Bản chất khác biệt**: VM (Máy ảo) mang tính "Heavyweight" vì chạy hệ điều hành riêng (Guest OS) trên Hypervisor, dẫn đến khởi động chậm (vài phút) và tốn tài nguyên. Trong khi đó, Container (Docker) là "Lightweight", dùng chung Host OS, khởi động tính bằng mili-giây.
- **Tối ưu hóa**: Container giúp đóng gói ứng dụng (App + Bins/Lib) gọn nhẹ, mang lại hiệu năng Native, tiết kiệm RAM và cho phép chạy hàng trăm đến hàng ngàn container trên cùng một hạ tầng thay vì chỉ vài chục VM.

#### 2. Thách thức khi triển khai Serverless (AWS Lambda & DynamoDB)
- **Quản lý trạng thái (Stateless Lambda)**: AWS Lambda không lưu trữ dữ liệu trên memory giữa các request. Mọi trạng thái (ví dụ: Game state) đều phải được query và lưu trữ liên tục từ DynamoDB, đòi hỏi thiết kế kiến trúc khéo léo để tránh độ trễ.
- **Bài toán chi phí (DynamoDB Scan Cost)**: Việc sử dụng lệnh `ScanCommand` để quét toàn bộ dữ liệu (VD: tìm player trong bảng) sẽ ngày càng chậm và đắt đỏ khi hệ thống scale up. Cần tối ưu bằng Query và Index.
- **Kết nối chết (Stale Connections - GoneException)**: Xử lý triệt để các người chơi ngắt kết nối đột ngột để hệ thống Matchmaking không gửi message lãng phí.

#### 3. WAF & Machine Learning cho Network Intrusion Detection System (Lê Hoàng Gia Đại)
- **Giới hạn của Signature-based**: Phân tích lý do tại sao các bộ luật dựa trên chữ ký (Signature) truyền thống của WAF là không đủ để chống lại các cuộc tấn công hiện đại (Zero-day, Behavior anomalies).
- **Giải pháp NIDS với ML**: Sử dụng Machine Learning để phân tích hành vi (Behavior-based detection). Mô hình được train trên tập dữ liệu chuẩn `CSE-CIC-IDS2018`.
- **Workflow vận hành**: Xây dựng Real-time dashboard để monitor, sau đó tương quan (Correlate) các dự đoán từ NIDS với các sự kiện của AWS WAF để nâng cao khả năng phát hiện mối đe dọa trên môi trường Cloud.

#### 4. Hành trình Nghề nghiệp: Từ Sysadmin đến DevOps (Trần Trung Vinh)
- **Chuyển đổi tư duy (Mindset Shift)**: Cloud không chỉ là một công nghệ mới, nó là một "cách tư duy mới". Chuyển từ việc cài đặt thủ công sang tự động hóa (Infrastructure as Code).
- **Kinh nghiệm phỏng vấn**: Những bài học xương máu và chiến lược phát triển bản thân đúc kết từ quá trình phỏng vấn tại các tập đoàn lớn (Central Retail Group).

### Những Gì Học Được

#### Tư Duy Kiến Trúc & Cloud
- **Right Tool for the Right Job**: Không có công nghệ nào là hoàn hảo tuyệt đối. Tùy vào bài toán chi phí và hiệu năng để chọn VM hay Container; chọn thiết kế DB sao cho không bị dội chi phí (Scan vs Query).
- **DevOps Mindset**: Khái niệm về hạ tầng giờ đây đã chuyển thành "Code". Sự ổn định của hệ thống phụ thuộc vào khả năng tự động hóa và giám sát.

#### Tư Duy Bảo Mật Hiện Đại
- Hiểu sâu hơn về giới hạn của các Rule tĩnh trong Firewall/WAF. Bảo mật hiện đại bắt buộc phải có sự can thiệp của AI/ML để phân tích các pattern bất thường của mạng thay vì chỉ dựa vào các rule đã biết.

### Ứng Dụng Vào Công Việc & Học Tập

- **Đóng gói dự án cá nhân**: Thay vì chạy các ứng dụng Java (Spring Boot) hay Python (Flask) trực tiếp trên máy host gây xung đột môi trường, tôi sẽ bắt đầu viết `Dockerfile` để container hóa các web app của mình, phục vụ cho việc deploy lên server dễ dàng hơn.
- **Nâng cấp tư duy Security (SIEM/Wazuh)**: Kiến thức từ phiên của anh Gia Đại về WAF + ML bổ trợ cực tốt cho định hướng nghiên cứu bảo mật của tôi. Tôi có thể áp dụng tư duy "Tương quan log" (Correlating events) này vào hệ thống Wazuh SIEM để viết các custom rule hiệu quả hơn cho đồ án "Session Hijacking and Fixation Prevention".
- **Kiểm soát chi phí Cloud**: Nếu có triển khai các Demo nhỏ lên AWS trong tương lai, bài học về `DynamoDB Scan Cost` sẽ giúp tôi cẩn trọng hơn trong việc thiết kế cơ sở dữ liệu để không bị vượt quá giới hạn Free Tier.

### Trải nghiệm trong event

Sự kiện AWS First Cloud Journey mang lại một góc nhìn đậm chất "Industry" (Thực tiễn công nghiệp) thay vì chỉ là lý thuyết sách vở.

- **Về mặt kỹ thuật**: Được nhìn thấy những kiến trúc thực tế mà các team AWS đang triển khai giúp tôi hình dung rõ ràng hơn về con đường trở thành một Kỹ sư Hệ thống/Bảo mật. Việc mổ xẻ những "điểm yếu" của Serverless (như Stateless Lambda) cho thấy sự chuyên sâu của các diễn giả.
- **Về mặt định hướng**: Session chia sẻ hành trình từ IT Helpdesk lên Sysadmin và DevOps của anh Trung Vinh truyền động lực rất lớn. Nó vạch ra một lộ trình rõ ràng, giúp sinh viên như tôi biết cần trang bị những bộ kỹ năng nào (từ OS, Network đến Cloud, Automation) để chuẩn bị cho các đợt phỏng vấn sắp tới.
- **Không khí sự kiện**: Chuyên nghiệp nhưng cởi mở. Các case study về ứng dụng AI vào WAF thật sự mở ra hướng đi mới cho các dự án an toàn thông tin mà tôi đang theo đuổi.

#### Một số hình ảnh tại sự kiện

***(Kiến thức về Container vs VM)***
<div style="display: flex; flex-direction: row; justify-content: center; gap: 2%; margin-bottom: 15px;">
  <img src="/ThucTapAWS/images/events/event2/anh6.jpg" style="width: 35%; margin: 0;" alt="So sánh VM và Container">
  <img src="/ThucTapAWS/images/events/event2/anh7.jpg" style="width: 35%; margin: 0;" alt="Chi tiết khác biệt VM và Container">
</div>

***(Chủ đề AWS WAF & Machine Learning của diễn giả Lê Hoàng Gia Đại)***
<div style="display: flex; flex-direction: row; justify-content: center; gap: 2%; margin-bottom: 15px;">
  <img src="/ThucTapAWS/images/events/event2/anh9.jpg" style="width: 35%; margin: 0;" alt="WAF + ML for Cyber Attack Detection">
  <img src="/ThucTapAWS/images/events/event2/anh10.jpg" style="width: 35%; margin: 0;" alt="Agenda kết hợp AWS WAF và ML">
</div>

***(Những thách thức thực tế với AWS và định hướng nghề nghiệp)***
<div style="display: flex; flex-direction: row; justify-content: center; gap: 2%; margin-bottom: 15px;">
  <img src="/ThucTapAWS/images/events/event2/anh4.jpg" style="width: 35%; margin: 0;" alt="Thách thức với Serverless">
  <img src="/ThucTapAWS/images/events/event2/anh12.jpg" style="width: 35%; margin: 0;" alt="Hành trình nghề nghiệp">
</div>

> Tóm lại, sự kiện là cầu nối hoàn hảo giữa kiến thức Quản trị mạng/Bảo mật tôi đang học ở trường với các tiêu chuẩn Cloud thực tế của doanh nghiệp. Nó củng cố thêm quyết tâm của tôi trong việc làm chủ hạ tầng mạng và tích hợp AI vào quy trình bảo mật.