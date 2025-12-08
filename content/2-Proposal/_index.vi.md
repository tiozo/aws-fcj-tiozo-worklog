---
title: "Proposal"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

# NỀN TẢNG PHÁT HIỆN GIAN LẬN VÀ XỬ LÝ THANH TOÁN ĐƯỢC HỖ TRỢ BỞI ML

### Tóm tắt điều hành

Dự án này là một **nền tảng phát hiện gian lận và xử lý thanh toán toàn diện** sử dụng **AWS Services** và **Machine Learning** để cung cấp khả năng xử lý thanh toán an toàn, có thể mở rộng và thông minh với tính năng phát hiện gian lận thời gian thực.

**Điểm Nổi Bật Chính:**

🔹 **Phát Hiện Gian Lận Tiên Tiến Được Hỗ Trợ Bởi ML**

Tích hợp các mô hình phát hiện gian lận XGBoost và phát hiện bất thường Autoencoder để nhận diện các giao dịch đáng ngờ trong thời gian thực, bảo vệ cả người bán và khách hàng khỏi các hoạt động gian lận.

🔹 **Kiến Trúc Xử Lý Thanh Toán Hybrid Cloud**

Được thiết kế với các server backend on-premise xử lý giao dịch thanh toán thông qua một pipeline phức tạp (Validator, Proposer, Worker, Executor, Finalizer), được tích hợp an toàn với các dịch vụ AWS cloud thông qua Site-to-Site VPN.

🔹 **Xử Lý Dữ Liệu Thời Gian Thực & Phân Tích**

Triển khai các pipeline dữ liệu streaming sử dụng Kinesis Firehose và các hàm Lambda để tổng hợp dữ liệu thời gian thực, cho phép phát hiện gian lận ngay lập tức và business intelligence thông qua dashboard QuickSight.

🔹 **Bảo Mật & Tuân Thủ Cấp Doanh Nghiệp**

Kiến trúc bảo mật đa lớp bao gồm AWS WAF, VPC PrivateLink, Secrets Manager, và giám sát toàn diện với CloudWatch và CloudTrail để tuân thủ audit.

🔹 **Tech Stack Cloud-Native Hiện Đại**

    Backend: Server on-premise với kiến trúc thanh toán modular
    
    Frontend: Static Web được host trên AWS với CloudFront CDN
    
    Cloud: VPC, Site-to-Site VPN, API Gateway, Lambda, S3
    
    ML/AI: SageMaker, XGBoost, Autoencoder, Kinesis Firehose
    
    DevOps: GitLab CI/CD với xác thực OIDC

### Phát Biểu Vấn Đề

**Thách Thức Hiện Tại:**

Các hệ thống xử lý thanh toán truyền thống thiếu khả năng phát hiện gian lận thời gian thực, dẫn đến tổn thất tài chính đáng kể. Nhiều giải pháp hiện tại hoặc quá đắt đỏ cho các doanh nghiệp nhỏ hoặc thiếu các mô hình ML phức tạp cần thiết để phát hiện gian lận chính xác.

Các hệ thống phát hiện gian lận hiện tại thường có tỷ lệ dương tính giả cao, chặn các giao dịch hợp pháp và tạo ra trải nghiệm khách hàng kém. Ngoài ra, hầu hết các hệ thống thiếu khả năng xử lý thời gian thực và audit trail toàn diện cần thiết cho việc tuân thủ.

**Giải Pháp Của Chúng Tôi:**

Chúng tôi cung cấp một nền tảng phát hiện gian lận hybrid cloud được hỗ trợ bởi ML kết hợp xử lý thanh toán on-premise với khả năng ML của AWS cloud. Hệ thống sử dụng các mô hình machine learning tiên tiến được triển khai trên AWS để nhận diện chính xác các giao dịch gian lận trong khi duy trì xử lý thanh toán trên hạ tầng on-premise an toàn.

Nền tảng cung cấp giám sát và phân tích toàn diện thông qua CloudWatch và QuickSight, cho phép các doanh nghiệp theo dõi hiệu suất, chi phí và các mẫu gian lận trong thời gian thực.

### Tổng Quan Kiến Trúc Giải Pháp

![Tổng Quan Kiến Trúc](/images/2-Proposal/architecture.png)

#### 1. Luồng Người Dùng Public (Public Access)

Người dùng Internet truy cập ứng dụng web:

**(1) User Request:** Người dùng gửi yêu cầu từ phía Client (Web/Mobile).

**(2) CDN & Caching:** Request đi qua Amazon CloudFront. Dịch vụ này phân phối nội dung tĩnh từ S3 Bucket (Static Web) để tăng tốc độ tải trang.

**(3) Security Layer:** Request động (API call) được lọc qua AWS WAF (Web Application Firewall) để chặn các cuộc tấn công phổ biến trước khi vào hệ thống.

**(9) API Entry Point:** Các request hợp lệ được chuyển đến API Gateway. Đây là cổng tiếp nhận chính, chịu trách nhiệm định tuyến request và quản lý traffic.

#### 2. Luồng Tích Hợp On-Premise (Private Integration)

Kết nối bảo mật từ backend thanh toán on-premise lên AWS:

**(5) VPN Tunnel:** Các server thanh toán on-premise gửi dữ liệu giao dịch qua đường truyền mã hóa Site-to-Site VPN.

**(6) Private Network Routing:** Traffic đi vào AWS VPC, được điều hướng qua Network Interface nội bộ.

**(4) VPC PrivateLink:** Traffic đi qua VPC Interface Endpoint, cho phép backend on-premise giao tiếp với các dịch vụ AWS ML một cách an toàn.

**(8) Internal API Call:** Từ PrivateLink, request được chuyển tiếp an toàn vào API Gateway để thực hiện ML inference (mà không cần đi qua Internet công cộng).

#### 3. Luồng Xử Lý Dữ Liệu & Machine Learning (Core Logic)

Logic nghiệp vụ chính sau khi API Gateway tiếp nhận request:

**(15) Request Handling:** API Gateway kích hoạt Lambda Function (api_handler).

**(10) Real-time Inference:** Nếu là tác vụ cần phản hồi ngay (như phát hiện gian lận), Lambda gọi sang model ML (XGBoost/Autoencoder) để lấy kết quả dự đoán tức thì.

**(12) Data Stream:** Dữ liệu log hoặc giao dịch được Lambda đẩy vào luồng Kinesis Firehose để xử lý bất đồng bộ, tránh làm chậm phản hồi của API.

**(13) Data Lake:** Kinesis Firehose gom dữ liệu và ghi xuống S3 Results Bucket để lưu trữ lâu dài.

**(14) Analytics:** Amazon QuickSight kết nối trực tiếp với S3 để hiển thị biểu đồ báo cáo (Dashboard) cho đội ngũ vận hành.

#### 4. Luồng Triển Khai (CI/CD DevOps)

Quy trình tự động hóa cập nhật mã nguồn:

**(16) Source Code:** Developer đẩy code lên GitLab Repository.

**(17) Deployment Pipeline:** GitLab Runner (được cấp quyền qua IAM Role) tự động thực thi pipeline:
  - Cập nhật code cho Lambda Function
  - Deploy model mới lên ML Layer
  - Upload static file mới lên S3 Bucket

### Giả Định

**Giả Định Cốt Lõi**

Vùng: Tất cả giá cả dựa trên us-east-1 (N. Virginia).

Kiến Trúc Hybrid: Backend thanh toán on-premise được tích hợp với các dịch vụ AWS serverless ML thông qua Site-to-Site VPN.

Mô Hình ML: SageMaker real-time endpoint với loại instance tối ưu (ml.t3.medium cho dev, ml.m5.xlarge cho prod).

Giả Định Traffic: 10K API call/tháng (dev), 1M API call/tháng (prod); 50GB truyền dữ liệu/tháng (prod).

Backend On-Premise: Các server xử lý thanh toán (Validator, Proposer, Worker, Executor, Finalizer) chạy on-premise, kết nối với AWS qua Site-to-Site VPN để thực hiện ML inference.

AWS Free Tier: Tối đa hóa cho môi trường phát triển. Production giả định giá sau free-tier.

Tối Ưu Chi Phí: Spot instance khi có thể, reserved capacity cho workload có thể dự đoán, và tài nguyên được định cỡ phù hợp.

Tuyên Bố Miễn Trừ: Phân tích này là ước tính dựa trên giá AWS hiện tại. Chi phí có thể thay đổi đáng kể dựa trên khối lượng giao dịch thực tế, nhu cầu xử lý dữ liệu và mẫu sử dụng mô hình ML.

### Phân Tích Chi Phí

| Dịch Vụ | Cấu Hình | Free Tier (Chi Phí Hàng Tháng) | Phát Triển (Chi Phí Hàng Tháng) | Production (Chi Phí Hàng Tháng) | Ghi Chú |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **CloudFront** | Static Web CDN | **$0.00** (1TB truyền) | **$0.00** | **~$15.00** | Phân phối nội dung toàn cầu cho static web |
| **S3** | Static Web + Results | **$0.00** (5GB lưu trữ) | **$0.00** | **~$8.00** | File tĩnh, kết quả ML, data lake |
| **API Gateway** | REST API calls | **$0.00** (1M request) | **$0.00** | **~$35.00** | Cổng API chính |
| **Site-to-Site VPN** | Kết nối on-premise | **~$36.00** | **~$36.00** | **~$36.00** | VPN connection + truyền dữ liệu |
| **VPC PrivateLink** | Interface Endpoint | **~$7.20** | **~$7.20** | **~$7.20** | Endpoint tích hợp riêng tư |
| **Lambda** | API handler + xử lý | **$0.00** (1M request) | **$0.00** | **~$20.00** | Xử lý request, gọi ML inference |
| **SageMaker Endpoint** | XGBoost + Autoencoder | **~$35.00** | **~$35.00** | **~$150.00** | ML inference thời gian thực (ml.t3.medium → ml.m5.xlarge) |
| **Kinesis Firehose** | Data streaming | **~$3.00** | **~$3.00** | **~$18.00** | Ingestion dữ liệu bất đồng bộ vào S3 |
| **QuickSight** | Dashboard phân tích | **$0.00** (1 user miễn phí) | **$0.00** | **~$18.00** | Trực quan hóa business intelligence |
| **WAF** | Web Application Firewall | **~$8.00** | **~$8.00** | **~$35.00** | Lớp bảo mật cho CloudFront/API Gateway |
| **CloudWatch** | Giám sát & logging | **$0.00** (10 metric, 5GB) | **$0.00** | **~$25.00** | Giám sát hệ thống và logs |
| **CloudTrail** | API audit logging | **$0.00** (1 trail miễn phí) | **$0.00** | **~$8.00** | Tuân thủ và audit trail |
| **Secrets Manager** | Lưu trữ credential | **~$2.00** | **~$2.00** | **~$5.00** | Quản lý credential an toàn |
| **IAM Role** | GitLab OIDC | **$0.00** | **$0.00** | **$0.00** | Miễn phí - Xác thực CI/CD |
| **Data Transfer** | Inter-service + egress | **~$2.00** | **~$2.00** | **~$15.00** | VPC, VPN và truyền dữ liệu internet |
| **GitLab Runner** | CI/CD compute | **$0.00** (400 phút miễn phí) | **$0.00** | **~$15.00** | Phút thực thi pipeline |
| **---** | **---** | **---** | **---** | **---** | **---** |
| **Tổng Chi Phí Ước Tính** | | **~$93.20 / tháng** | **~$93.20 / tháng** | **~$365.20 / tháng** | Kiến trúc serverless với tích hợp VPN |

### Đánh Giá Rủi Ro

| Danh Mục Rủi Ro | Mô Tả Rủi Ro | Khả Năng | Tác Động | Chiến Lược Giảm Thiểu |
| :--- | :--- | :--- | :--- | :--- |
| **1. Hiệu Suất Mô Hình ML** | **Tỷ Lệ Dương Tính Giả:** Các mô hình ML nhận diện sai các giao dịch hợp pháp là gian lận, dẫn đến sự không hài lòng của khách hàng và mất doanh thu. | **Trung Bình** | **Cao** | **- Huấn Luyện Mô Hình Liên Tục:** Triển khai feedback loop để liên tục huấn luyện lại mô hình với dữ liệu mới. <br> **- A/B Testing:** Triển khai nhiều phiên bản mô hình và so sánh hiệu suất. <br> **- Quy Trình Xem Xét Thủ Công:** Triển khai xem xét thủ công cho các trường hợp biên giới. |
| **2. Khả Năng Mở Rộng** | **Tăng Đột Biến Traffic:** Sự gia tăng đột ngột trong khối lượng giao dịch làm quá tải hệ thống, gây ra độ trễ xử lý hoặc lỗi. | **Trung Bình** | **Cao** | **- Auto Scaling:** Cấu hình Lambda và SageMaker để tự động mở rộng. <br> **- Load Testing:** Kiểm tra hiệu suất thường xuyên để xác định điểm nghẽn. <br> **- Circuit Breakers:** Triển khai mẫu circuit breaker để ngăn chặn lỗi cascade. <br> **- Dung Lượng On-Premise:** Mở rộng server backend thanh toán dựa trên tải. |
| **3. Bảo Mật** | **Vi Phạm Dữ Liệu:** Dữ liệu thanh toán và khách hàng nhạy cảm bị xâm phạm do các lỗ hổng bảo mật. | **Thấp** | **Nghiêm Trọng** | **- Mã Hóa:** Mã hóa end-to-end cho tất cả dữ liệu trong quá trình truyền và lưu trữ. <br> **- Kiểm Tra Bảo Mật Thường Xuyên:** Kiểm tra thâm nhập và đánh giá lỗ hổng hàng quý. <br> **- Tuân Thủ:** Duy trì tuân thủ PCI DSS cho xử lý thanh toán. |
| **4. Quản Lý Chi Phí** | **Tăng Đột Biến Chi Phí:** Chi phí inference mô hình ML hoặc chi phí xử lý dữ liệu vượt quá ngân sách do khối lượng giao dịch cao. | **Trung Bình** | **Trung Bình** | **- Giám Sát Chi Phí:** AWS Budgets và cảnh báo cho ngưỡng chi phí. <br> **- Tối Ưu Tài Nguyên:** Xem xét và tối ưu hóa việc sử dụng tài nguyên thường xuyên. <br> **- Reserved Capacity:** Sử dụng reserved instance cho workload có thể dự đoán. | mới. <br> **- A/B Testing:** Triển khai nhiều phiên bản mô hình và so sánh hiệu suất. <br> **- Quy Trình Xem Xét Thủ Công:** Triển khai xem xét thủ công cho các trường hợp biên. |
| **2. Khả Năng Mở Rộng** | **Tăng Đột Biến Traffic:** Sự gia tăng đột ngột trong khối lượng giao dịch làm quá tải hệ thống, gây ra độ trễ xử lý hoặc lỗi. | **Trung Bình** | **Cao** | **- Auto Scaling:** Cấu hình ECS Fargate và Lambda cho auto scaling. <br> **- Load Testing:** Kiểm tra hiệu suất thường xuyên để xác định bottleneck. <br> **- Circuit Breakers:** Triển khai pattern circuit breaker để ngăn cascade failure. |
| **3. Bảo Mật** | **Vi Phạm Dữ Liệu:** Dữ liệu thanh toán và khách hàng nhạy cảm bị xâm phạm do lỗ hổng bảo mật. | **Thấp** | **Nghiêm Trọng** | **- Mã Hóa:** Mã hóa end-to-end cho tất cả dữ liệu trong quá trình truyền và lưu trữ. <br> **- Audit Bảo Mật Thường Xuyên:** Kiểm tra penetration testing và đánh giá lỗ hổng hàng quý. <br> **- Tuân Thủ:** Duy trì tuân thủ PCI DSS cho xử lý thanh toán. |
| **4. Quản Lý Chi Phí** | **Tăng Đột Biến Chi Phí:** Chi phí suy luận mô hình ML hoặc chi phí xử lý dữ liệu vượt quá ngân sách do khối lượng giao dịch cao. | **Trung Bình** | **Trung Bình** | **- Giám Sát Chi Phí:** AWS Budgets và cảnh báo cho ngưỡng chi phí. <br> **- Tối Ưu Hóa Tài Nguyên:** Xem xét và tối ưu hóa sử dụng tài nguyên thường xuyên. <br> **- Reserved Capacity:** Sử dụng reserved instance cho workload có thể dự đoán. |

### Kết Quả

#### Kết Quả Kỹ Thuật

Khi hoàn thành, dự án sẽ cung cấp:

1. **Nền Tảng Phát Hiện Gian Lận Sẵn Sàng Production:**
   * Một ứng dụng web hoạt động đầy đủ với khả năng phát hiện gian lận thời gian thực
   * Các mô hình ML có khả năng xử lý hàng nghìn giao dịch mỗi phút
   * Pipeline xử lý thanh toán toàn diện với nhiều giai đoạn xác thực

2. **Kiến Trúc Hybrid Cloud:**
   * Xử lý thanh toán on-premise với tích hợp AWS ML
   * Pipeline xử lý và streaming dữ liệu thời gian thực
   * Bảo mật cấp doanh nghiệp với VPN và PrivateLink

3. **Phân Tích và Giám Sát Tiên Tiến:**
   * Dashboard thời gian thực cho các metric phát hiện gian lận
   * Audit trail toàn diện và báo cáo tuân thủ
   * Hệ thống giám sát hiệu suất và cảnh báo

#### Kết Quả Học Tập

Dự án này cung cấp kinh nghiệm trong:

1. **Machine Learning Operations (MLOps):**
   * Triển khai và quản lý các mô hình ML trong production
   * Suy luận mô hình thời gian thực và huấn luyện liên tục
   * A/B testing và giám sát hiệu suất mô hình

2. **Kiến Trúc Hybrid Cloud:**
   * Thiết kế hệ thống xử lý thanh toán hybrid cloud an toàn
   * Triển khai các mẫu tích hợp on-premise to cloud
   * Quản lý pipeline dữ liệu phức tạp và hệ thống streaming

3. **Bảo Mật và Tuân Thủ:**
   * Triển khai xử lý thanh toán tuân thủ PCI DSS
   * Thiết kế kiến trúc bảo mật đa lớp
   * Hệ thống audit trail và báo cáo tuân thủ

### Giá Trị Portfolio

#### Tích Hợp ML Tiên Tiến
* **Phát Hiện Gian Lận Thời Gian Thực:** Chứng minh khả năng triển khai các mô hình ML production cho các chức năng kinh doanh quan trọng
* **Kiến Trúc Đa Mô Hình:** Thể hiện chuyên môn trong việc orchestrate nhiều mô hình ML (XGBoost, Autoencoder) để phát hiện gian lận toàn diện
* **Pipeline ML Streaming:** Triển khai xử lý dữ liệu thời gian thực và suy luận mô hình ở quy mô lớn

#### Kiến Trúc Hybrid Cloud
* **Chuyên Môn Xử Lý Thanh Toán:** Thể hiện hiểu biết về xử lý giao dịch tài chính phức tạp on-premise
* **Thiết Kế Tích Hợp Cloud:** Chứng minh kiến trúc hybrid cloud an toàn với VPN và PrivateLink
* **Phương Pháp Security-First:** Triển khai bảo mật cấp doanh nghiệp với WAF, VPC, mã hóa VPN và giám sát toàn diện

#### DevOps và Tự Động Hóa
* **Tích Hợp GitLab CI/CD:** Pipeline triển khai tiên tiến với xác thực OIDC
* **Infrastructure as Code:** Khả năng triển khai và scaling tự động
* **Giám Sát Toàn Diện:** Khả năng quan sát đầy đủ với CloudWatch, CloudTrail và QuickSight

### Kết Luận

Dự án này chứng minh một **nền tảng phát hiện gian lận và xử lý thanh toán được hỗ trợ bởi ML cấp production** thể hiện:

#### **Hoạt Động ML Tiên Tiến**
Phát hiện gian lận thời gian thực với các mô hình XGBoost và Autoencoder
Pipeline dữ liệu streaming với Kinesis Firehose
Phân tích toàn diện với dashboard QuickSight

#### **Kiến Trúc Hybrid Cloud**
Xử lý thanh toán on-premise với tích hợp AWS ML
Bảo mật đa lớp với VPN, WAF và VPC PrivateLink
Thiết kế high-availability với khả năng auto-scaling ML services

#### **Bảo Mật và Tuân Thủ**
Xử lý thanh toán tuân thủ PCI DSS
Mã hóa end-to-end và quản lý credential an toàn
Audit trail toàn diện với CloudTrail

**Timeline**: 4 tháng | Team: 3 người | Budget: $93-365/tháng (Free Tier/Dev-Prod)

**Dự án này chứng minh chuyên môn trong ML operations, kiến trúc cloud doanh nghiệp và xử lý thanh toán an toàn, làm cho nó trở thành một portfolio piece xuất sắc cho các vai trò ML Engineering, Cloud Architecture và FinTech.**

## Phụ Lục

A. Gitlab repo: (Chưa có sẵn, mã nguồn hoàn chỉnh đã được nộp cho VPBank Hackathon)

B. Thông tin liên hệ:  
- Trưởng dự án: Võ Minh Thuận
- Email: ```azinz850@gmail.com```
- WhatsApp/Zalo: ```0908517568```

C. Tải Tài Liệu Proposal Đầy Đủ: [ML-Fraud-Detection-Proposal.docx](/files/ML-Fraud-Detection-Proposal.docx)