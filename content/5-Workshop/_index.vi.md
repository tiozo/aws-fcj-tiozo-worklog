---
title: "Triển khai ML Model lên Amazon SageMaker"
date: "`date +'%Y-%m-%d'`"
weight: 5
chapter: false
---

# Triển khai Machine Learning Model lên Amazon SageMaker

## Tổng quan

Trong workshop này, bạn sẽ học cách triển khai một machine learning model đã được train sẵn lên Amazon SageMaker sử dụng Terraform cho infrastructure as code. Workshop bao gồm toàn bộ vòng đời triển khai từ provisioning infrastructure đến testing và cleanup.

## Mục tiêu Workshop

Sau khi hoàn thành workshop, bạn sẽ có thể:
- Hiểu kiến trúc triển khai Amazon SageMaker
- Sử dụng Terraform để provision SageMaker infrastructure
- Triển khai pre-trained ML model lên SageMaker endpoint
- Test model đã deploy với API requests
- Giám sát hiệu suất model sử dụng CloudWatch
- Dọn dẹp tài nguyên đúng cách

## Các dịch vụ AWS sử dụng

- **Amazon SageMaker**: Triển khai và host machine learning models
- **Amazon S3**: Lưu trữ model artifacts
- **Amazon CloudWatch**: Giám sát endpoint metrics và logs
- **AWS IAM**: Quản lý permissions và roles
- **Amazon API Gateway** (tùy chọn): Tạo REST API cho model inference

## Yêu cầu trước khi bắt đầu

Trước khi bắt đầu workshop, đảm bảo bạn có:

1. **Tài khoản AWS**: Tài khoản AWS đang hoạt động với quyền phù hợp
2. **AWS CLI**: Đã cài đặt và cấu hình với credentials của bạn
   ```bash
   aws --version
   aws configure
   ```
3. **Terraform**: Phiên bản 1.0 trở lên
   ```bash
   terraform --version
   ```
4. **Python**: Phiên bản 3.8 trở lên với pip
   ```bash
   python3 --version
   ```
5. **Git**: Để clone repository
6. **Kiến thức cơ bản**: 
   - Các dịch vụ AWS cơ bản
   - Terraform cơ bản
   - Lập trình Python
   - Khái niệm machine learning

## Chi phí ước tính

Chạy workshop này có thể phát sinh chi phí AWS. Chi phí ước tính:
- SageMaker endpoint: ~$0.05-0.10 mỗi giờ
- S3 storage: Tối thiểu (< $0.01)
- CloudWatch: Tối thiểu (< $0.01)

**Quan trọng**: Nhớ dọn dẹp tài nguyên sau khi hoàn thành workshop để tránh phát sinh chi phí.

## Thời gian Workshop

Khoảng 60-90 phút

## Cấu trúc Workshop

1. [Giới thiệu](1-introduction/)
2. [Chuẩn bị](2-preparation/)
3. [Triển khai Infrastructure](3-deploy-infrastructure/)
4. [Testing Model](4-testing/)
5. [Giám sát với CloudWatch](5-monitoring/)
6. [Dọn dẹp](6-cleanup/)

Hãy bắt đầu!
