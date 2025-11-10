---
title: "Proposal"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

# NỀN TẢNG PHÁT HIỆN GIAN LẬN VÀ XỬ LÝ THANH TOÁN ĐƯỢC HỖ TRỢ BỞI ML

### Tóm Tắt Điều Hành

Dự án này là một **nền tảng phát hiện gian lận và xử lý thanh toán toàn diện** sử dụng **AWS Services** và **Machine Learning** để cung cấp khả năng xử lý thanh toán an toàn, có thể mở rộng và thông minh với tính năng phát hiện gian lận thời gian thực.

**Điểm Nổi Bật Chính:**

🔹 **Phát Hiện Gian Lận Tiên Tiến Được Hỗ Trợ Bởi ML**

Tích hợp các mô hình phát hiện gian lận XGBoost và phát hiện bất thường Autopilot để nhận diện các giao dịch đáng ngờ trong thời gian thực, bảo vệ cả người bán và khách hàng khỏi các hoạt động gian lận.

🔹 **Kiến Trúc Xử Lý Thanh Toán Có Thể Mở Rộng**

Được thiết kế với các worker ECS Fargate xử lý giao dịch thanh toán thông qua một pipeline phức tạp (Validator, Proposer, Worker, Executor, Finalizer), đảm bảo xử lý thanh toán đáng tin cậy và có thể mở rộng.

🔹 **Xử Lý Dữ Liệu Thời Gian Thực & Phân Tích**

Triển khai các pipeline dữ liệu streaming sử dụng Kinesis Firehose và các hàm Lambda để tổng hợp dữ liệu thời gian thực, cho phép phát hiện gian lận ngay lập tức và business intelligence thông qua dashboard QuickSight.

🔹 **Bảo Mật & Tuân Thủ Cấp Doanh Nghiệp**

Kiến trúc bảo mật đa lớp bao gồm AWS WAF, VPC PrivateLink, Secrets Manager, và giám sát toàn diện với CloudWatch và CloudTrail để tuân thủ audit.

🔹 **Tech Stack Cloud-Native Hiện Đại**

    Backend: ECS Fargate với microservices được container hóa
    
    Frontend: Static Web được host trên AWS với CloudFront CDN
    
    Cloud: VPC, ALB, API Gateway, Lambda, S3, StyleDB
    
    ML/AI: SageMaker, XGBoost, Autopilot, Kinesis Firehose
    
    DevOps: GitLab CI/CD với xác thực OIDC

### Phát Biểu Vấn Đề

**Thách Thức Hiện Tại:**

Các hệ thống xử lý thanh toán truyền thống thiếu khả năng phát hiện gian lận thời gian thực, dẫn đến tổn thất tài chính đáng kể. Nhiều giải pháp hiện tại hoặc quá đắt đỏ cho các doanh nghiệp nhỏ hoặc thiếu các mô hình ML phức tạp cần thiết để phát hiện gian lận chính xác.

Các hệ thống phát hiện gian lận hiện tại thường có tỷ lệ dương tính giả cao, chặn các giao dịch hợp pháp và tạo ra trải nghiệm khách hàng kém. Ngoài ra, hầu hết các hệ thống thiếu khả năng xử lý thời gian thực và audit trail toàn diện cần thiết cho việc tuân thủ.

**Giải Pháp Của Chúng Tôi:**

Chúng tôi cung cấp một nền tảng phát hiện gian lận được hỗ trợ bởi ML từ đầu đến cuối với khả năng xử lý thời gian thực. Hệ thống sử dụng các mô hình machine learning tiên tiến để nhận diện chính xác các giao dịch gian lận trong khi giảm thiểu dương tính giả.

Nền tảng cung cấp giám sát và phân tích toàn diện thông qua CloudWatch và QuickSight, cho phép các doanh nghiệp theo dõi hiệu suất, chi phí và các mẫu gian lận trong thời gian thực.

### Tổng Quan Kiến Trúc Giải Pháp

![Tổng Quan Kiến Trúc](/images/2-Proposal/architecture.png)

```markdown
**Tương Tác Người Dùng Frontend**

1. **Luồng Xác Thực Người Dùng:** Người dùng demo truy cập ứng dụng thông qua frontend web tĩnh được host trên AWS, với các request được định tuyến qua API Gateway đến lớp bảo mật để xác thực.

2. **Quản Lý Truy Cập An Toàn:** API Gateway xác thực các request thông qua AWS WAF để lọc bảo mật, với thông tin đăng nhập được xác minh với AWS Secrets Manager.

**Xử Lý Lớp Ứng Dụng**

3. **Định Tuyến Mạng VPC:** Các request đã xác thực vào VPC thông qua VPC PrivateLink, với Lớp Ứng Dụng định tuyến request đến Lớp ML để dự đoán phát hiện gian lận.

4. **Luồng Xử Lý Thanh Toán:** Các request thanh toán kích hoạt ECS Fargate Payment Workers xử lý giao dịch thông qua các handler chuyên biệt (Validator, Proposer, Worker, Executor, Finalizer).

**Pipeline Machine Learning**

5. **Tổng Hợp Dữ Liệu:** Các hàm Lambda Stream Handle thu thập và tổng hợp dữ liệu giao dịch, lưu trữ trong Work History Subnet (StyleDB) và streaming đến Kinesis Firehose.

6. **Huấn Luyện và Suy Luận Mô Hình ML:** Kinesis Firehose stream dữ liệu đến Lớp ML nơi các mô hình XGBoost Fraud Detection và Autopilot Anomaly Detection phân tích giao dịch, với kết quả được hiển thị trong QuickSight.

**Quản Lý Dữ Liệu**

7. **Hoạt Động Cơ Sở Dữ Liệu:** Temporal Config (ECS trên EC2) quản lý orchestration workflow, với dữ liệu thanh toán đã xử lý được lưu trữ trong các bucket StyleDB.

8. **Xử Lý Dữ Liệu Lambda:** API Gateway kích hoạt các hàm Lambda để tổng hợp dữ liệu, với ALB phân phối traffic đến ECS Execution Roles.

**Giám Sát và CI/CD**

9. **Giám Sát Hệ Thống:** CloudWatch giám sát sức khỏe hệ thống và CloudTrail theo dõi các cuộc gọi API để tuân thủ audit.

10. **Pipeline Triển Khai:** GitLab CI/CD với xác thực OIDC quản lý triển khai đến các repository Infrastructure, Application và ML.
```

### Giả Định

**Giả Định Cốt Lõi**

Vùng: Tất cả giá cả dựa trên us-east-1 (N. Virginia).

ECS Fargate: Tối ưu kích thước container (0.25 vCPU, 0.5GB RAM cho dev; 1 vCPU, 2GB RAM cho prod) với auto-scaling.

Cơ Sở Dữ Liệu: RDS MySQL với triển khai Multi-AZ cho production, single-AZ cho development.

Mô Hình ML: SageMaker real-time endpoint với loại instance tối ưu (ml.t3.medium cho dev, ml.m5.large cho prod).

Giả Định Traffic: 10K API call/tháng (dev), 1M API call/tháng (prod); 100GB truyền dữ liệu/tháng (prod).

AWS Free Tier: Tối đa hóa cho môi trường phát triển. Production giả định giá sau free-tier.

Tối Ưu Chi Phí: Spot instance khi có thể, reserved capacity cho workload có thể dự đoán, và tài nguyên được định cỡ phù hợp.

Tuyên Bố Miễn Trừ: Phân tích này là ước tính dựa trên giá AWS hiện tại. Chi phí có thể thay đổi đáng kể dựa trên khối lượng giao dịch thực tế, nhu cầu xử lý dữ liệu và mẫu sử dụng mô hình ML.

### Phân Tích Chi Phí

| Dịch Vụ | Cấu Hình | Free Tier (Chi Phí Hàng Tháng) | Phát Triển (Chi Phí Hàng Tháng) | Production (Chi Phí Hàng Tháng) | Ghi Chú |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **CloudFront** | Static Web CDN | **$0.00** (1TB truyền) | **$0.00** | **~$15.00** | Phân phối nội dung toàn cầu |
| **S3** | Nhiều bucket | **$0.00** (5GB lưu trữ) | **$0.00** | **~$8.00** | Dữ liệu ứng dụng, mô hình, thanh toán |
| **API Gateway** | REST API calls | **$0.00** (1M request) | **$0.00** | **~$35.00** | Mỗi triệu API request |
| **Application Load Balancer** | Phân phối traffic | **~$18.00** | **~$18.00** | **~$18.00** | Chi phí cố định hàng tháng |
| **VPC PrivateLink** | Kết nối an toàn | **~$7.20** | **~$7.20** | **~$7.20** | Mỗi endpoint mỗi giờ |
| **ECS Fargate** | Payment Workers | **~$25.00** | **~$25.00** | **~$120.00** | Tối ưu kích thước container |
| **EC2** | Temporal Config | **$0.00** (750h t3.micro) | **$0.00** | **~$15.00** | t3.small cho production |
| **Lambda** | Xử lý dữ liệu | **$0.00** (1M request) | **$0.00** | **~$12.00** | Stream processing và tổng hợp |
| **SageMaker** | ML model inference | **~$18.00** | **~$18.00** | **~$85.00** | Tối ưu loại instance |
| **Kinesis Firehose** | Data streaming | **~$3.00** | **~$3.00** | **~$18.00** | Ingestion dữ liệu thời gian thực |
| **StyleDB (RDS)** | Database storage | **$0.00** (750h db.t3.micro) | **$0.00** | **~$45.00** | Multi-AZ cho production |
| **Secrets Manager** | Lưu trữ credential | **~$2.00** | **~$2.00** | **~$5.00** | Mỗi secret mỗi tháng |
| **CloudWatch** | Giám sát & logging | **$0.00** (10 metric, 5GB) | **$0.00** | **~$25.00** | Giám sát toàn diện |
| **CloudTrail** | API audit logging | **$0.00** (1 trail miễn phí) | **$0.00** | **~$8.00** | Data event bổ sung |
| **WAF** | Web Application Firewall | **~$8.00** | **~$8.00** | **~$35.00** | Lọc bảo mật |
| **QuickSight** | Trực quan hóa dữ liệu | **$0.00** (1 user miễn phí) | **$0.00** | **~$18.00** | Business intelligence |
| **Data Transfer** | Giao tiếp giữa các dịch vụ | **~$2.00** | **~$2.00** | **~$12.00** | VPC và internet egress |
| **GitLab Runner** | CI/CD compute | **$0.00** (400 phút miễn phí) | **$0.00** | **~$15.00** | Phút build bổ sung |
| **---** | **---** | **---** | **---** | **---** | **---** |
| **Tổng Chi Phí Ước Tính** | | **~$83.20 / tháng** | **~$83.20 / tháng** | **~$456.20 / tháng** | Free Tier = Chi phí phát triển |

### Đánh Giá Rủi Ro

| Danh Mục Rủi Ro | Mô Tả Rủi Ro | Khả Năng | Tác Động | Chiến Lược Giảm Thiểu |
| :--- | :--- | :--- | :--- | :--- |
| **1. Hiệu Suất Mô Hình ML** | **Tỷ Lệ Dương Tính Giả:** Các mô hình ML nhận diện sai các giao dịch hợp pháp là gian lận, dẫn đến sự không hài lòng của khách hàng và mất doanh thu. | **Trung Bình** | **Cao** | **- Huấn Luyện Mô Hình Liên Tục:** Triển khai feedback loop để liên tục huấn luyện lại mô hình với dữ liệu mới. <br> **- A/B Testing:** Triển khai nhiều phiên bản mô hình và so sánh hiệu suất. <br> **- Quy Trình Xem Xét Thủ Công:** Triển khai xem xét thủ công cho các trường hợp biên. |
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

2. **Kiến Trúc Cloud Có Thể Mở Rộng:**
   * Các dịch vụ được container hóa auto-scaling sử dụng ECS Fargate
   * Pipeline xử lý và streaming dữ liệu thời gian thực
   * Các tính năng bảo mật và tuân thủ cấp doanh nghiệp

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

2. **Kiến Trúc Cloud Doanh Nghiệp:**
   * Thiết kế hệ thống xử lý thanh toán an toàn, có thể mở rộng
   * Triển khai kiến trúc microservices với container
   * Quản lý pipeline dữ liệu phức tạp và hệ thống streaming

3. **Bảo Mật và Tuân Thủ:**
   * Triển khai xử lý thanh toán tuân thủ PCI DSS
   * Thiết kế kiến trúc bảo mật đa lớp
   * Hệ thống audit trail và báo cáo tuân thủ

### Giá Trị Portfolio

#### Tích Hợp ML Tiên Tiến
* **Phát Hiện Gian Lận Thời Gian Thực:** Chứng minh khả năng triển khai các mô hình ML production cho các chức năng kinh doanh quan trọng
* **Kiến Trúc Đa Mô Hình:** Thể hiện chuyên môn trong việc orchestrate nhiều mô hình ML (XGBoost, Autopilot) để phát hiện gian lận toàn diện
* **Pipeline ML Streaming:** Triển khai xử lý dữ liệu thời gian thực và suy luận mô hình ở quy mô lớn

#### Kiến Trúc Doanh Nghiệp
* **Chuyên Môn Xử Lý Thanh Toán:** Thể hiện hiểu biết về xử lý giao dịch tài chính phức tạp
* **Thiết Kế Microservices:** Chứng minh kiến trúc container hóa hiện đại với ECS Fargate
* **Phương Pháp Security-First:** Triển khai bảo mật cấp doanh nghiệp với WAF, VPC và giám sát toàn diện

#### DevOps và Tự Động Hóa
* **Tích Hợp GitLab CI/CD:** Pipeline triển khai tiên tiến với xác thực OIDC
* **Infrastructure as Code:** Khả năng triển khai và scaling tự động
* **Giám Sát Toàn Diện:** Khả năng quan sát đầy đủ với CloudWatch, CloudTrail và QuickSight

### Kết Luận

Dự án này chứng minh một **nền tảng phát hiện gian lận và xử lý thanh toán được hỗ trợ bởi ML cấp production** thể hiện:

#### **Hoạt Động ML Tiên Tiến**
Phát hiện gian lận thời gian thực với các mô hình XGBoost và Autopilot
Pipeline dữ liệu streaming với Kinesis Firehose
Phân tích toàn diện với dashboard QuickSight

#### **Kiến Trúc Doanh Nghiệp**
Xử lý thanh toán có thể mở rộng với ECS Fargate workers
Bảo mật đa lớp với WAF và VPC PrivateLink
Thiết kế high-availability với khả năng auto-scaling

#### **Bảo Mật và Tuân Thủ**
Xử lý thanh toán tuân thủ PCI DSS
Mã hóa end-to-end và quản lý credential an toàn
Audit trail toàn diện với CloudTrail

**Timeline**: 4 tháng | Team: 5 người | Budget: $83-456/tháng (Free Tier/Dev-Prod)

**Dự án này chứng minh chuyên môn trong ML operations, kiến trúc cloud doanh nghiệp và xử lý thanh toán an toàn, làm cho nó trở thành một portfolio piece xuất sắc cho các vai trò ML Engineering, Cloud Architecture và FinTech.**

## Phụ Lục

A. Github Repo: https://github.com/tiozo/FCJ-Fraud-Detection-Platform

B. Thông tin liên hệ: 
- Trưởng dự án: Võ Minh Thuận
- Email: ```azinz850@gmail.com```
- WhatsApp/Zalo: ```0908517568```