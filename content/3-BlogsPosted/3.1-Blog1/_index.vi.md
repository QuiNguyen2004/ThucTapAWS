---
title: "Blog 1"
date: 2026-07-06
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---
# AWS Multi-AZ và Multi-Region: Giải pháp đảm bảo tính sẵn sàng cao cho hệ thống Cloud
Quản lý quyền truy cập (permission management) trong các môi trường Kubernetes quy mô lớn luôn là một bài toán nan giải đối với các kỹ sư DevOps và Security. Làm sao để đảm bảo nguyên tắc "đặc quyền tối thiểu" (least privilege) cho từng microservice mà không biến hệ thống IAM Roles trở thành một "bãi rác" khó kiểm soát?

Bài viết này sẽ giới thiệu cách giải quyết triệt để bài toán trên bằng cách kết hợp sức mạnh của Amazon EKS Pod Identity và tính năng Session Policies mới, giúp bạn chuyển dịch từ mô hình phân quyền cồng kềnh sang một kiến trúc bảo mật tập trung, tinh gọn và tự động hóa hoàn toàn.

## Phần 1: Những thách thức từ kiến trúc cũ (Cơ chế IRSA truyền thống)

Trước khi có EKS Pod Identity, phương pháp tiêu chuẩn để cấp quyền AWS cho Pod là IAM Roles for Service Accounts (IRSA). Mặc dù là một bước tiến lớn so với việc gắn IAM Role trực tiếp vào EC2 Node, IRSA vẫn tồn tại những rào cản vận hành lớn khi quy mô hệ thống tăng lên:

* **Bùng nổ số lượng IAM Roles (IAM Role Proliferation):** Để tuân thủ nguyên tắc đặc quyền tối thiểu, mỗi microservice (hoặc nhóm Pod) cần một IAM Role riêng biệt với các policy được tinh chỉnh. Trong các kiến trúc microservice phức tạp với hàng trăm dịch vụ, con số này có thể lên tới hàng trăm, thậm chí hàng nghìn IAM Roles, gây khó khăn lớn cho việc quản lý, kiểm toán (audit) và gắn thẻ (tagging).
* **Vận hành thủ công rườm rà:** Việc thiết lập IRSA đòi hỏi kỹ sư phải thực hiện ánh xạ (mapping) thủ công giữa Kubernetes Service Account và IAM Role thông qua các đoạn annotation phức tạp trong file manifest. Quá trình này tốn nhiều thời gian trong CI/CD pipelines và dễ dẫn đến lỗi cấu hình.
* **Rủi ro lạm dụng quyền (Over-permissioning):** Do gánh nặng vận hành khi phải quản lý quá nhiều Role, các team phát triển thường có xu hướng "lười biếng" bằng cách dùng chung một vài IAM Role có quyền hạn rộng hơn mức cần thiết cho nhiều dịch vụ khác nhau. Điều này tạo ra những lỗ hổng bảo mật nghiêm trọng.

## Phần 2: Giải pháp kiến trúc 3 lớp với EKS Pod Identity và Session Policies

Để giải quyết các thách thức trên, Amazon đã giới thiệu EKS Pod Identity. Giải pháp này không chỉ đơn giản hóa luồng xác thực mà còn tích hợp hoàn hảo với Session Policies để áp dụng các bộ lọc bảo mật động ngay tại thời điểm runtime.

Kiến trúc mới này được chia thành 3 lớp rõ rệt:

### Lớp 1: Identity Layer (Lớp Định danh K8s)
Tại đây, hệ thống tập trung vào danh tính của ứng dụng bên trong Kubernetes. Các Pod ứng dụng không cần phải biết hoặc khai báo ARN của IAM Role trực tiếp. Thay vào đó, chúng định danh đơn giản thông qua một Kubernetes Service Account tiêu chuẩn.

Mối quan hệ giữa Service Account và IAM Role được quản lý tập trung và đồng bộ bởi EKS Pod Identity Agent—một component chạy dưới dạng DaemonSet trên mỗi worker node. Agent này đóng vai trò như một "người đại diện" (proxy), thay mặt Pod xử lý toàn bộ quá trình trao đổi credential.
* **Luồng đi:** `[EKS Cluster]` -> `[Pods]` -> `[EKS Pod Identity Agent]`

### Lớp 2: Policy & Control Layer (Lớp Chính sách & Điều khiển AWS)
Đây là "trái tim" của giải pháp mới. Chúng ta sử dụng Pod Identity Associations để thực hiện ánh xạ giữa Kubernetes Service Account (ở Layer 1) với một IAM Role dùng chung (ở Layer 2).

Điểm đột phá nằm ở đây: Khi Pod gửi request để lấy credential, dịch vụ AWS STS (Security Token Service) sẽ được gọi. EKS Pod Identity Agent sẽ tự động chèn thêm một Session Policy (Chính sách phiên) vào quá trình này.

Session Policy hoạt động như một bộ lọc bổ sung. Quyền hạn thực tế mà Pod nhận được sẽ là phần giao (intersection) giữa IAM Role gốc và Session Policy này. Điều đó có nghĩa là:
* Chúng ta có thể dùng một IAM Role chung cho nhiều Pod.
* Nhưng mỗi Pod (thông qua Session Policy) sẽ chỉ được cấp đúng tập hợp quyền hạn tối thiểu cần thiết cho riêng nó.
* **Luồng đi:** `[AWS STS]` + `[Session Policies (Scope-down JSON)]` -> Tạo ra `[Temporary Credentials]`

### Lớp 3: Target Resource Layer (Lớp Tài nguyên Đích)
Các dịch vụ AWS ở "hậu phương" như Amazon S3, Amazon DynamoDB, hoặc Amazon SQS sẽ tiếp nhận request từ Pod.

Lúc này, các request đã được thu hẹp quyền một cách an toàn và tự động. Các dịch vụ này không cần biết và cũng không quan tâm đến cấu trúc Kubernetes bên trong. Chúng chỉ đơn giản là thực thi yêu cầu dựa trên "Temporary Credentials" (Credential tạm thời) đã bị giới hạn bởi Session Policy.
* **Luồng đi:** `[Amazon S3]` | `[Amazon DynamoDB]` | `[Amazon SQS]`

## Phần 3: Sơ đồ tổng quan kiến trúc

Để hình dung rõ hơn luồng dữ liệu và các thành phần tương tác, mời bạn xem sơ đồ kiến trúc giải pháp EKS Pod Identity kết hợp Session Policies:

![Kiến trúc EC2 vs Kiến trúc Lambda](/images/Blog/blog1.jpg)

*Hình 1: Sơ đồ chi tiết luồng xác thực và phân quyền (Nguồn: Dựa trên luồng hoạt động của Amazon EKS).*

* **Phần 1 (Màu xanh):** Quá trình khởi tạo ban đầu, đăng ký IAM Role và tạo Association.
* **Phần 2 (Các bước 3, 4, 4a, 4b):** Luồng xác thực và ủy quyền tại thời điểm runtime, nơi EKS Pod Identity Agent và AWS STS tương tác để cấp Temporary Credentials và áp dụng Session Policy.
* **Phần 3 (Bước 5):** Pod sử dụng credential đã được thu hẹp quyền để truy cập Amazon S3.

## Phần 4: Kết luận

Việc chuyển dịch từ mô hình IRSA truyền thống sang kiến trúc EKS Pod Identity kết hợp Session Policies mang lại những lợi ích to lớn và ngay lập tức cho các tổ chức đang vận hành Kubernetes:

* **Tối ưu vận hành:** Rút ngắn đáng kể thời gian cấu hình phân quyền từ hàng giờ (cho hàng trăm Role) xuống còn vài phút (chỉ cần cấu hình một Association đơn giản).
* **Giải quyết bài toán bùng nổ IAM Role:** Loại bỏ hoàn toàn gánh nặng quản lý số lượng lớn IAM Roles. Theo thực tế triển khai, giải pháp này giúp giảm tới 80% số lượng IAM Role cần phải quản lý trong hệ thống.
* **Tăng cường bảo mật tuyệt đối:** Đảm bảo 100% các Pod tuân thủ nghiêm ngặt nguyên tắc "Least Privilege" nhờ khả năng thu hẹp quyền động qua Session Policies, ngay cả khi chúng dùng chung một IAM Role gốc.

Đây là một bước tiến quan trọng trong việc hiện đại hóa và bảo mật hóa các cụm Amazon EKS, giúp đội ngũ vận hành tập trung vào việc phát triển sản phẩm thay vì loay hoay với các cấu hình phân quyền phức tạp.

**Link bài viết gốc tham khảo:** [Amazon EKS Pod Identity: a new way for applications on EKS to obtain IAM credentials | AWS Containers Blog](https://aws.amazon.com/blogs/containers/amazon-eks-pod-identity-a-new-way-for-applications-on-eks-to-obtain-iam-credentials/)