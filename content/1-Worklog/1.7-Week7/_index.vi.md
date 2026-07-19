---
title: "Worklog Tuần 7"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu triển khai:

* **Xây dựng tư duy FinOps (Tài chính Cloud):** Nắm vững cách quản trị rủi ro tài chính khi vận hành hệ thống trên nền tảng AWS, đảm bảo cân bằng giữa hiệu suất kỹ thuật và chi phí.
* **Ngăn chặn rủi ro vượt ngân sách:** Sử dụng AWS Budget để thiết lập các chốt chặn tài chính, tự động cảnh báo nhằm tránh tình trạng "billing shock" (hóa đơn tăng vọt ngoài ý muốn) do lỗi cấu hình hoặc quên tắt tài nguyên.
* **Tối ưu hóa chi phí dài hạn:** Hiểu rõ và cấu hình giám sát cho các mô hình cam kết sử dụng như Reserved Instances (RI) và Savings Plans để cắt giảm chi phí hạ tầng.
* **Làm chủ hệ thống cảnh báo:** Cấu hình thành công luồng thông báo (Notifications) để gửi email đến đúng người quản trị khi ngân sách tiệm cận mức giới hạn.

### Các công việc cần triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| :--- | :--- | :--- | :--- | :--- |
| 2 | **Phân tích chi phí & Lên kế hoạch:** <br> - Đánh giá mức tiêu thụ chi phí hiện tại của tài khoản AWS. <br> - Xác định các mục tiêu và hạn mức chi tiêu (Budget Goals) cho dự án. | 08/06/2025 | 08/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | **Thiết lập Cost & Usage Budget:** <br> - Tạo Cost Budget: Giám sát tổng chi phí thực tế và dự báo. <br> - Tạo Usage Budget: Giới hạn số giờ chạy của một dịch vụ cụ thể (vd: EC2). | 09/06/2025 | 09/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | **Thiết lập Ngân sách tối ưu hóa (RI & Savings Plans):** <br> - Tìm hiểu cơ chế giảm giá của Reserved Instances. <br> - Khởi tạo RI Budget & Savings Plans Budget để đo lường mức độ sử dụng hiệu quả (Utilization/Coverage). | 10/06/2025 | 10/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | **Cấu hình Ngưỡng cảnh báo (Alert Thresholds):** <br> - Thiết lập các mốc cảnh báo (vd: 80% để chuẩn bị xử lý, 100% cho mức báo động đỏ). <br> - Cấu hình danh sách email nhận thông báo cảnh báo tự động. | 11/06/2025 | 11/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | **Giám sát tổng thể & Dọn dẹp tài nguyên:** <br> - Review lại bảng điều khiển (Dashboard) của AWS Budgets. <br> - Xóa các ngân sách bài lab (Clean Up Resources) để hoàn tất quy trình thực hành. | 12/06/2025 | 12/06/2025 | <https://cloudjourney.awsstudygroup.com/> |

---

### Chi tiết quá trình thực hiện:

#### 1. Hoạch định ngân sách tổng thể (Cost & Usage Budget)
* **Cost Budget (Ngân sách chi phí):** Đã tiến hành thiết lập mức trần chi phí cho toàn bộ tài khoản AWS. Điểm nổi bật là việc cấu hình giám sát cả *Chi phí thực tế (Actual costs)* lẫn *Chi phí dự báo (Forecasted costs)*, giúp phát hiện sớm xu hướng tiêu hao ngân sách ngay từ giữa tháng thay vì đợi đến cuối kỳ đối soát.
* **Usage Budget (Ngân sách sử dụng):** Giám sát chi tiết ở mức độ tài nguyên thay vì tiền tệ. Ví dụ: Thiết lập giới hạn 750 giờ chạy cho dịch vụ EC2 (mức miễn phí của Free Tier). Nếu tài khoản chạy quá số giờ này, hệ thống sẽ báo động, giúp tránh việc treo máy chủ test qua đêm một cách vô ích.

#### 2. Áp dụng các mô hình tiết kiệm (RI & Savings Plans)
* **RI Budget:** Thiết lập theo dõi cho các máy chủ đã cam kết trả trước (Reserved Instances). Budget này giúp đánh giá xem các máy chủ đã mua có đang được sử dụng hết công suất hay không.
* **Savings Plans Budget:** Cấu hình đo lường cho mô hình Savings Plans. Qua quá trình phân tích, nhận thấy Savings Plans mang lại sự linh hoạt vượt trội hơn RI vì cho phép thay đổi cấu hình phần cứng (Instance type) hoặc chuyển đổi hệ điều hành mà vẫn giữ nguyên được mức chiết khấu.

#### 3. Thiết lập hệ thống phòng vệ (Alert & Notifications)
* **Ngưỡng cảnh báo đa cấp độ:** Thiết lập mốc cảnh báo theo từng nấc: 50%, 80% và 100% so với ngân sách đã định.
* **Tự động hóa thông báo:** Liên kết trực tiếp các cảnh báo này với địa chỉ Email của nhóm quản trị hệ thống. Khi chỉ số chạm ngưỡng, một email thông báo sẽ được AWS tự động bắn ra ngay lập tức, đảm bảo thông tin luôn đến tay người chịu trách nhiệm.

---

### Kết quả đạt được:

* **Hình thành tư duy chủ động (Proactive Control):** Đã loại bỏ hoàn toàn việc bị động trước các hóa đơn AWS. Hệ thống tự động theo dõi và cảnh báo (Timely Alerts) giúp ngay lập tức phát hiện các dịch vụ ngốn tiền bất thường hoặc bị cấu hình sai.
* **Kiểm soát dòng tiền hiệu quả:** Biết cách sử dụng bảng điều khiển để nhận diện các dịch vụ dư thừa, từ đó có cơ sở dữ liệu thực tế để áp dụng Savings Plans nhằm cắt giảm tối đa chi phí chạy hạ tầng.
* **Kỹ năng phân bổ chi phí (Cost Allocation):** Nắm được kỹ năng gán tags và phân tách chi phí cho từng module/dự án khác nhau. Đây là năng lực cốt lõi giúp kỹ sư Cloud có thể lập bảng dự toán (Estimation) chính xác khi đề xuất giải pháp kỹ thuật lên cấp quản lý.
