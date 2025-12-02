---
title : "AWS Cloud Mastery Series 3"
date : 2024-11-29
weight : 3
chapter : false
---

# AWS Cloud Mastery Series #3

## Thông tin sự kiện
- **Tên sự kiện:** AWS Cloud Mastery Series #3
- **Ngày và giờ:** Thứ 7, 29 tháng 11, 2024 - 8:30 AM - 12:00 PM GMT+7
- **Địa điểm:** Tòa nhà Bitexco Financial Tower
- **Vai trò:** Người tham dự

## Mô tả sự kiện
Phần thứ ba của chuỗi sự kiện AWS Cloud Mastery Series tập trung vào Nền tảng Bảo mật và năm trụ cột của kiến trúc bảo mật AWS. Workshop toàn diện này bao gồm các thực hành bảo mật hiện đại, phát hiện mối đe dọa, bảo vệ hạ tầng, mã hóa dữ liệu và ứng phó sự cố trong môi trường cloud.

## Các phiên tham dự

### Khai mạc & Nền tảng Bảo mật
- **Thời gian:** 8:30 – 8:50 AM
- **Nội dung:**
  - Vai trò Security Pillar trong Well-Architected
  - Nguyên tắc cốt lõi: Least Privilege – Zero Trust – Defense in Depth
  - Shared Responsibility Model
  - Top threats trong môi trường cloud tại Việt Nam

### ⭐ Trụ cột 1 — Identity & Access Management
- **Thời gian:** 8:50 – 9:30 AM | Kiến trúc IAM Hiện đại
- **Nội dung:**
  - IAM: Users, Roles, Policies – tránh long-term credentials
  - IAM Identity Center: SSO, permission sets
  - SCP & permission boundaries cho multi-account
  - MFA, credential rotation, Access Analyzer
  - Mini Demo: Validate IAM Policy + simulate access

### ⭐ Trụ cột 2 — Detection
- **Thời gian:** 9:30 – 9:55 AM | Phát hiện & Giám sát Liên tục
- **Nội dung:**
  - CloudTrail (org-level), GuardDuty, Security Hub
  - Logging tại mọi tầng: VPC Flow Logs, ALB/S3 logs
  - Alerting & automation với EventBridge
  - Detection-as-Code (infrastructure + rules)

### Giải lao
- **Thời gian:** 9:55 – 10:10 AM

### ⭐ Trụ cột 3 — Infrastructure Protection
- **Thời gian:** 10:10 – 10:40 AM | Bảo mật Mạng & Workload
- **Nội dung:**
  - VPC segmentation, private vs public placement
  - Security Groups vs NACLs: mô hình áp dụng
  - WAF + Shield + Network Firewall
  - Workload protection: EC2, ECS/EKS security basics

### ⭐ Trụ cột 4 — Data Protection
- **Thời gian:** 10:40 – 11:10 AM | Mã hóa, Keys & Secrets
- **Nội dung:**
  - KMS: key policies, grants, rotation
  - Encryption at-rest & in-transit: S3, EBS, RDS, DynamoDB
  - Secrets Manager & Parameter Store — patterns rotation
  - Data classification & access guardrails

### ⭐ Trụ cột 5 — Incident Response
- **Thời gian:** 11:10 – 11:40 AM | IR Playbook & Automation
- **Nội dung:**
  - IR lifecycle theo AWS
  - Playbook scenarios:
    - Compromised IAM key
    - S3 public exposure
    - EC2 malware detection
  - Snapshot, isolation, evidence collection
  - Auto-response bằng Lambda/Step Functions

### Tổng kết & Q&A
- **Thời gian:** 11:40 – 12:00 PM
- **Nội dung:**
  - Tổng kết 5 pillars
  - Common pitfalls & thực tế doanh nghiệp Việt Nam
  - Roadmap security learning (Security Specialty, SA Pro)

## Kết quả và Giá trị thu được

### Hiểu biết về Kiến trúc Bảo mật
- **Security Pillar của Well-Architected:** Hiểu sâu về các nguyên tắc bảo mật và cách triển khai
- **Năm Trụ cột:** Tìm hiểu chi tiết về IAM, Detection, Infrastructure Protection, Data Protection và Incident Response
- **Best Practices:** Học các thực hành bảo mật chuẩn ngành và những lỗi thường gặp cần tránh

### Phát triển Kỹ năng Kỹ thuật
- **Kiến trúc IAM:** Các mô hình quản lý danh tính và truy cập hiện đại bao gồm SSO và chiến lược multi-account
- **Giám sát Bảo mật:** Kinh nghiệm thực hành với CloudTrail, GuardDuty và Security Hub
- **Mã hóa & Quản lý Key:** Kiến thức thực tế về KMS, các mô hình mã hóa và quản lý secrets
- **Ứng phó Sự cố:** Phương pháp có cấu trúc để xử lý sự cố bảo mật và tự động hóa

### Ứng dụng Thực tế
- **Kinh nghiệm Demo:** Thực hành validation IAM policy và mô phỏng truy cập
- **Tình huống Thực tế:** Playbook ứng phó sự cố cho các mối đe dọa bảo mật phổ biến
- **Bối cảnh Doanh nghiệp:** Hiểu biết về thách thức bảo mật cụ thể của doanh nghiệp Việt Nam
- **Phát triển Nghề nghiệp:** Lộ trình rõ ràng cho các chứng chỉ AWS Security Specialty và Solutions Architect Professional

Workshop bảo mật toàn diện này cung cấp kiến thức thiết yếu để triển khai kiến trúc bảo mật mạnh mẽ trong môi trường AWS cloud và thiết lập nền tảng vững chắc cho các chứng chỉ bảo mật nâng cao.