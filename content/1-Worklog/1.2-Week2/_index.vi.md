---
title: "Worklog Tuần 2"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---


### Mục tiêu tuần 2:

* Kết nối, làm quen với các thành viên trong First Cloud AI Journey.
* Hiểu dịch vụ AWS cơ bản, cách dùng console & CLI.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| :---: | :--- | :---: | :---: | :--- |
| **1** | - Tìm hiểu Mô hình triển khai điện toán đám mây (Public, Private, Hybrid Cloud).<br>- Nghiên cứu cơ sở hạ tầng toàn cầu của AWS (Regions & Availability Zones). | 07/05/2026 | 07/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **2** | - Học Module 1: Nền tảng & Bảo mật AWS.<br>- Tìm hiểu Mô hình trách nhiệm chung.<br>- Cấu hình bảo mật IAM: Tạo IAM User, thiết lập MFA và áp dụng nguyên tắc đặc quyền tối thiểu (Least Privilege). | 08/05/2026 | 08/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **3** | - Học Module 2: Dịch vụ Điện toán AWS (Compute).<br>- Tìm hiểu sâu về Amazon EC2: Các loại instance (Instance types), AMI, EBS và vòng đời của EC2 (Lifecycle). | 09/05/2026 | 09/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **4** | - **Thực hành Lab:**<br>&emsp; + Khởi chạy máy chủ ảo EC2.<br>&emsp; + Remote kết nối SSH vào instance.<br>&emsp; + Triển khai ứng dụng mẫu Danh bạ nhân viên (Employee Directory). | 10/05/2026 | 10/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| **5** | - Nghiên cứu sự tiến hóa của điện toán đám mây:<br>&emsp; + Containers (AWS Fargate).<br>&emsp; + Serverless & Event-driven computing (AWS Lambda).<br>- Thực hành phân tích Use-case để lựa chọn giải pháp Compute phù hợp (Right-sizing). | 11/05/2026 | 11/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

---
### Quá trình:
* Tìm hiểu mô hình triển khai điện toán đám mây
 * Điện toán đám mây cho phép các nhà phát triển và bộ phận CNTT tập trung vào những điều quan trọng nhất bằng cách tránh các công việc như mua sắm, bảo trì và lập kế hoạch dung lượng. Khi điện toán đám mây ngày càng phổ biến, nhiều chiến lược triển khai đã xuất hiện để đáp ứng nhu cầu cụ thể của người dùng khác nhau. Mỗi loại phương pháp triển khai cung cấp cho bạn các mức độ kiểm soát, tính linh hoạt và quản lý khác nhau. Hiểu được sự khác biệt giữa các chiến lược triển khai này có thể giúp bạn quyết định bộ dịch vụ nào phù hợp với nhu cầu của mình
 ![Mô hình triển khai điện toán đám mây](/images/Worklog/mohinhdientoandammay.jpg)
 **1. Phương pháp tiếp cận kiến thức:**
*   **Học qua trực quan hóa (Visual Learning):** Phân tích kỹ các sơ đồ kiến trúc, ví dụ như sơ đồ "Mô hình triển khai điện toán đám mây", để thấy rõ sự khác biệt về quyền kiểm soát, tính linh hoạt và cách quản lý tài nguyên.
*   **Học qua ví dụ thực tế (Use-case Driven):** Thay vì chỉ đọc lý thuyết, tôi theo dõi luồng triển khai thực tế của ứng dụng mẫu "Danh bạ nhân viên" (Employee Directory). Cách này giúp tôi hiểu rõ một ứng dụng thực tế sẽ cần những dịch vụ AWS nào cấu thành.
*   **Đối chiếu và So sánh:** Liên tục đặt câu hỏi so sánh giữa các dịch vụ (ví dụ: Khi nào dùng EC2, khi nào dùng Lambda?) để rèn luyện kỹ năng ra quyết định lựa chọn giải pháp (Architecting).

**2. Các kiến thức trọng tâm cần ghi nhớ (Key Takeaways):**

*   **Từ Module 1: Nền tảng & Bảo mật AWS**
    *   **Mô hình trách nhiệm chung (Shared Responsibility Model):** Đây là kim chỉ nam về bảo mật. AWS bảo vệ hạ tầng vật lý ("Security **OF** the Cloud"), còn tôi (người dùng) phải tự chịu trách nhiệm bảo mật dữ liệu, cấu hình tường lửa và mã nguồn của mình ("Security **IN** the Cloud").
    *   **Quản lý danh tính và quyền truy cập (IAM):** Nguyên tắc sống còn là **không bao giờ** sử dụng tài khoản Root cho các tác vụ hàng ngày. Phải luôn tạo IAM User, thiết lập MFA (Xác thực đa yếu tố) và cấp quyền theo "Nguyên tắc đặc quyền tối thiểu" (Least Privilege).

*   **Từ Module 2: Dịch vụ Điện toán (AWS Compute)**
    *   **Amazon EC2 (Máy chủ ảo):** Không chỉ là việc tạo máy ảo, mà quan trọng là hiểu "Vòng đời của EC2" (Lifecycle) từ lúc khởi chạy (Launch), dừng (Stop) cho đến khi chấm dứt (Terminate) để tối ưu chi phí.
    *   **Sự tiến hóa của tính toán (Containers & Serverless):**
        *   *Containers (AWS Fargate):* Đóng gói ứng dụng để chạy đồng nhất ở mọi nơi mà không cần bận tâm đến hệ điều hành máy chủ.
        *   *Serverless (AWS Lambda):* Mô hình điện toán hiện đại nhất, cho phép chạy code trực tiếp để phản hồi lại các sự kiện (event-driven) mà không cần cấu hình bất kỳ máy chủ nào. Đặc biệt, chỉ phải trả tiền cho thời gian mili-giây code thực thi.
    *   **Kỹ năng lựa chọn dịch vụ (Right-sizing):** Bài học quan trọng nhất là không có dịch vụ nào tốt nhất, chỉ có dịch vụ phù hợp nhất. Chọn EC2 khi cần toàn quyền kiểm soát hệ điều hành, chọn Container cho ứng dụng Microservices, và chọn Lambda cho các tác vụ chạy nhanh, tự động hóa mở rộng.
    ![Mô hình triển khai điện toán đám mây](/images/Worklog/AmazonElasticComputeCloudVI.jpg)
 
### Kết quả đạt được tuần 2:

* Hiểu AWS là gì và nắm được các nhóm dịch vụ cơ bản: 
  * Compute
  * Storage
  * Networking 
  * Database
  * Mô hình triển khai điện toán đám mây
  
* Đã tạo và cấu hình AWS Free Tier account thành công.

* Làm quen với AWS Management Console và biết cách tìm, truy cập, sử dụng dịch vụ từ giao diện web.

* Cài đặt và cấu hình AWS CLI trên máy tính bao gồm:

* Sử dụng AWS CLI để thực hiện các thao tác cơ bản như:
 
* **Hoàn thành Module 1: Giới thiệu về Amazon Web Services**
  * **Tổng quan & Hạ tầng:** Nắm vững định nghĩa AWS, sơ đồ cơ sở hạ tầng toàn cầu và các phương thức tương tác với AWS.
  * **Bảo mật & IAM:** Hiểu rõ Mô hình Trách nhiệm Chung của AWS, cách bảo vệ tài khoản người dùng gốc và quản lý danh tính, quyền truy cập (IAM).
  * **Thực hành:** Minh họa triển khai bảo mật với IAM và lưu trữ ứng dụng Danh bạ Nhân viên.

* **Hoàn thành Module 2: Điện toán AWS (Compute)**
  * **Amazon EC2:** Tìm hiểu khái niệm điện toán như một dịch vụ, hướng dẫn bắt đầu và vòng đời của phiên bản EC2.
  * **Container & Serverless:** Mở rộng kiến thức về các dịch vụ container và điện toán phi máy chủ (Serverless) thông qua AWS Fargate và AWS Lambda.
  * **Thực hành & Ứng dụng:** Khởi chạy ứng dụng Danh bạ Nhân viên thực tế trên EC2 và học cách phân tích, lựa chọn dịch vụ điện toán phù hợp với từng bài toán.



