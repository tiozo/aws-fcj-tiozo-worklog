---
title: "Giới thiệu"
date: "`date +'%Y-%m-%d'`"
weight: 1
chapter: false
pre: "<b>1. </b>"
---

## Tổng quan Workshop

Workshop này hướng dẫn triển khai hệ thống phát hiện gian lận (Fraud Detection) sử dụng Machine Learning trên AWS. Hệ thống sử dụng ensemble model kết hợp XGBoost và Autoencoder để phát hiện giao dịch gian lận theo thời gian thực.

## Kiến trúc Hệ thống

### Luồng API Đồng bộ (Synchronous)
```
Client → API Gateway → Lambda → SageMaker Endpoints → Response
                                    ↓
                                   S3 (lưu kết quả)
```

### Luồng Stream Bất đồng bộ (Asynchronous)
```
Data Source → Kinesis Stream → Lambda → S3 (batch processing)
```

## Các Thành phần Chính

1. **Amazon SageMaker**: 
   - 2 endpoints (XGBoost + Autoencoder)
   - Instance type: ml.m5.large
   - Real-time inference

2. **AWS Lambda**:
   - API Handler: Xử lý requests từ API Gateway
   - Stream Handler: Xử lý data từ Kinesis

3. **Amazon API Gateway**:
   - HTTP API endpoint `/predict`
   - CORS enabled

4. **Amazon Kinesis**:
   - Data Stream cho batch processing
   - 1 shard (có thể scale)

5. **Amazon S3**:
   - Model bucket: Lưu model artifacts
   - Result bucket: Lưu predictions và logs

6. **AWS IAM**:
   - Roles với least privilege principle

## Ensemble Model

**XGBoost (80% weight)**:
- Supervised learning
- 15 features
- Phát hiện patterns từ labeled data

**Autoencoder (20% weight)**:
- Unsupervised learning  
- 13 features
- Phát hiện anomalies

**Threshold**: 0.7835 (F1 Score: 0.8381)

## Features Được Sử Dụng

Input từ client:
- `amount`: Số tiền giao dịch
- `user_id`: ID người dùng
- `payee_id`: ID người nhận
- `timestamp`: Thời gian giao dịch

Hệ thống tự động tạo 15 features cho ML model bao gồm time-based, amount-based, velocity và behavioral features.

## Lợi ích của Kiến trúc

- **Serverless**: Không cần quản lý servers
- **Scalable**: Auto-scaling theo traffic
- **Cost-effective**: Pay per use
- **Real-time**: Latency thấp cho fraud detection
- **Infrastructure as Code**: Dễ dàng replicate và version control
