---
title: "Các bài blog đã đăng"
date: 2026-07-06
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Trong quá trình thực tập và tìm hiểu về AWS, em đã tổng hợp kiến thức và đúc kết thành các bài viết chia sẻ kỹ thuật (Blogs). Việc viết blog không chỉ giúp củng cố lại lý thuyết đã học mà còn chia sẻ các góc nhìn thực tế về kiến trúc hệ thống đến cộng đồng. 

Dưới đây là danh sách các bài blog em đã hoàn thành và đăng tải:

### [Blog 1 - EKS Pod Identity và Session Policies: Kiến trúc phân quyền tập trung trên Kubernetes](3.1-Blog1/)
Bài viết phân tích cách giải quyết bài toán quản lý quyền truy cập (permission management) nan giải trong các môi trường Kubernetes quy mô lớn. Bằng cách kết hợp sức mạnh của Amazon EKS Pod Identity và tính năng Session Policies, bài viết hướng dẫn chuyển đổi từ cơ chế IRSA cồng kềnh truyền thống sang một kiến trúc bảo mật 3 lớp tinh gọn, tự động hóa hoàn toàn và đảm bảo nguyên tắc "đặc quyền tối thiểu" mà không gây quá tải số lượng IAM Roles.

### [Blog 2 - AWS Well-Architected Framework: Tư Duy Thiết Kế Đám Mây Không Ngừng Tiến Hóa](3.2-Blog2/)
Dựa trên bản cập nhật mới nhất của AWS, bài viết này mang đến thông điệp: WAF không phải là một bộ checklist tĩnh, mà là một cẩm nang sống động. Nội dung khai thác nghệ thuật đánh đổi (trade-offs) giữa 6 trụ cột cốt lõi, cách phá vỡ ranh giới giao tiếp giữa Kỹ thuật, Tài chính và Ban điều hành, đồng thời khẳng định việc đánh giá kiến trúc phải là một hành trình liên tục trong suốt vòng đời của hệ thống Cloud.

### [Blog 3 - Bảo Vệ Dữ Liệu Nhạy Cảm Với Data Masking Trong Amazon RDS for Oracle](3.3-Blog3/)
Bài viết chia sẻ góc nhìn thực tế về tầm quan trọng của việc bảo mật dữ liệu khi tạo môi trường Development hoặc UAT từ dữ liệu Production. Thông qua việc phân tích luồng hoạt động tự động hóa kết hợp giữa Amazon EventBridge, Step Functions, Systems Manager và Secrets Manager, bài viết hướng dẫn cách che giấu (masking) các thông tin nhạy cảm của khách hàng, giúp đội ngũ lập trình và kiểm thử làm việc an toàn mà không gây rủi ro lộ lọt dữ liệu.