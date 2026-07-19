---
title: "Blog 3"
date: 2026-07-06
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---
# Bảo Vệ Dữ Liệu Nhạy Cảm Với Data Masking Trong Amazon RDS for Oracle
Trong quá trình thực tập và tìm hiểu về AWS Database, mình có đọc được một bài viết khá hay về **Data Masking trong Amazon RDS for Oracle**. Đây là một chủ đề mà trước đây mình chưa từng tìm hiểu, nên muốn chia sẻ lại những gì mình học được với mọi người.

Trước đây mình nghĩ rằng khi cần tạo môi trường Development hoặc UAT thì chỉ cần restore bản backup của Production là xong. Tuy nhiên, sau khi đọc bài viết này mình mới nhận ra rằng cách làm đó có thể làm lộ rất nhiều dữ liệu nhạy cảm của khách hàng.

**Ví dụ như:**
* Họ và tên
* Email
* Số điện thoại
* Địa chỉ
* Thông tin cá nhân khác

Nếu những dữ liệu này được đưa sang môi trường Dev/Test mà không có biện pháp bảo vệ thì sẽ tiềm ẩn nhiều rủi ro về bảo mật.

## Data Masking là gì?

Theo mình hiểu, Data Masking là quá trình thay thế dữ liệu thật bằng dữ liệu giả nhưng vẫn giữ nguyên định dạng để hệ thống có thể hoạt động và kiểm thử bình thường.

**Ví dụ:**

| Dữ liệu gốc | Sau khi Mask |
| :--- | :--- |
| Nguyễn Văn A | Customer001 |
| loc@gmail.com | user001@example.com |
| 0909123456 | 0909XXXXXX |

Nhờ vậy, lập trình viên và tester vẫn có thể sử dụng dữ liệu để phát triển hoặc kiểm thử mà không nhìn thấy thông tin thật của khách hàng.

## Quy trình hoạt động

Khi xem sơ đồ trên, mình hiểu quy trình hoạt động như sau:

![Sơ đồ quy trình hoạt động](/images/Blog/blog3.jpg)

1. **EventBridge Scheduler:** Dịch vụ này sẽ tự động kích hoạt quy trình theo lịch đã cấu hình, giúp không cần chạy thủ công mỗi lần cần làm mới dữ liệu.
2. **Restore Snapshot:** Hệ thống sẽ lấy Snapshot từ cơ sở dữ liệu Production và khôi phục thành một RDS Instance tạm để thực hiện các bước tiếp theo.
3. **AWS Secrets Manager:** Thay vì lưu tài khoản và mật khẩu trực tiếp trong script, thông tin đăng nhập sẽ được lấy từ AWS Secrets Manager. Đây là một cách làm an toàn hơn và cũng là Best Practice của AWS.
4. **Thực hiện Data Masking:** Sau khi cơ sở dữ liệu được khôi phục, Systems Manager sẽ chạy Masking Script để che giấu các dữ liệu nhạy cảm.
5. **Tạo Snapshot mới:** Sau khi hoàn tất Data Masking, hệ thống sẽ tạo một Snapshot mới chứa dữ liệu đã được che giấu.
6. **Restore cho môi trường Dev/UAT:** Cuối cùng, Snapshot đã được Mask sẽ được restore thành cơ sở dữ liệu dành cho Developer hoặc Tester sử dụng.

## Điều mình thấy hay

Điều mình ấn tượng nhất là AWS không chỉ hướng dẫn cách che giấu dữ liệu mà còn kết hợp nhiều dịch vụ để tự động hóa toàn bộ quy trình.

Một số dịch vụ được sử dụng gồm:
* Amazon EventBridge Scheduler
* AWS Step Functions
* AWS Systems Manager
* AWS Secrets Manager
* Amazon RDS for Oracle

Mỗi dịch vụ đảm nhận một nhiệm vụ riêng, giúp quy trình diễn ra tự động và giảm rất nhiều thao tác thủ công.

## Những điều mình học được

Sau khi tìm hiểu bài viết này, mình rút ra một vài điều:
* Không nên sử dụng trực tiếp dữ liệu Production cho môi trường Development hoặc Testing.
* Data Masking là một giải pháp giúp bảo vệ dữ liệu nhạy cảm mà vẫn đảm bảo dữ liệu có thể dùng để kiểm thử.
* AWS Secrets Manager giúp quản lý thông tin đăng nhập an toàn hơn thay vì lưu trong mã nguồn hoặc script.
* Việc tự động hóa quy trình bằng các dịch vụ AWS giúp tiết kiệm thời gian và giảm sai sót.

## Kết luận

Đây là một chủ đề khá mới đối với mình và cũng giúp mình hiểu thêm về cách AWS hỗ trợ bảo vệ dữ liệu trong các hệ thống thực tế.

Qua bài viết này, mình nhận ra rằng khi xây dựng một hệ thống trên Cloud thì bảo mật dữ liệu không chỉ dừng lại ở việc phân quyền hay mã hóa, mà còn cần đảm bảo dữ liệu được xử lý an toàn khi đưa sang các môi trường Development hoặc Testing.

---
**Tham khảo:** [AWS Database Blog – Data Masking in Amazon RDS for Oracle](https://aws.amazon.com/blogs/database/)