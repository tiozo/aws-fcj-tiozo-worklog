---
title: "Chuẩn bị"
date: "`date +'%Y-%m-%d'`"
weight: 2
chapter: false
pre: "<b>2. </b>"
---

## Kiểm tra Yêu cầu

### 1. Thiết lập Tài khoản AWS

Đảm bảo bạn có tài khoản AWS với quyền phù hợp:
- SageMaker full access
- Lambda full access
- S3 full access
- API Gateway full access
- Kinesis full access
- IAM role creation

### 2. Cài đặt Công cụ Cần thiết

**AWS CLI**:
```bash
# Kiểm tra version
aws --version

# Cấu hình credentials
aws configure
# Nhập: Access Key ID, Secret Access Key, Region (us-east-1), Output format (json)
```

**Terraform**:
```bash
# Kiểm tra version (cần >= 1.0)
terraform --version

# Nếu chưa cài, tải từ: https://www.terraform.io/downloads
```

**Python**:
```bash
# Kiểm tra version (cần >= 3.8)
python3 --version

# Cài đặt packages cần thiết
pip3 install boto3 requests
```

### 3. Tải Workshop Files

**Tải từ Google Drive**:
[Tải folder FraudTF và models](https://drive.google.com/drive/folders/15xlnf2SR9ZUuJ4iaUyV6NUrCJOg5eYd-?usp=sharing)

Folder chứa:
- Terraform configurations (các file *.tf)
- Lambda code (thư mục lambda_code/)
- Trained models (autoencoder và xgboost)

```bash
# Giải nén và di chuyển đến folder
cd ~/workspace/FraudTF
```

### 4. Chuẩn bị Model Files

Bạn cần hai trained models ở định dạng `.tar.gz`:

**Cấu trúc thư mục**:
```
FraudTF/
├── models/
│   ├── autoencoder/
│   │   └── model.tar.gz
│   └── xgboost/
│       └── model.tar.gz
```

**Yêu cầu model**:
- **Autoencoder**: PyTorch model với script `inference.py` (cần thiết khi sử dụng custom container hoặc framework không phải native AWS)
- **XGBoost**: Model ở định dạng chuẩn (không cần `inference.py` vì sử dụng AWS native XGBoost container)

### 5. Kiểm tra Terraform Configuration

Xem file `variables.tf`:
```bash
cd FraudTF
cat variables.tf
```

Giá trị mặc định:
- `aws_region`: us-east-1
- `project_name`: aiml-serverless
- Instance type: ml.m5.large

Bạn có thể tùy chỉnh bằng cách tạo `terraform.tfvars`:
```hcl
aws_region   = "us-east-1"
project_name = "fraud-detection"
```

### 6. Xác minh AWS Permissions

Test AWS access:
```bash
# List S3 buckets
aws s3 ls

# Kiểm tra SageMaker access
aws sagemaker list-endpoints

# Kiểm tra Lambda access
aws lambda list-functions
```

## Ước tính Chi phí

Trước khi deploy, hiểu rõ chi phí:

| Dịch vụ | Tài nguyên | Chi phí Ước tính |
|---------|----------|----------------|
| SageMaker | 2 x ml.m5.large | $0.269/giờ |
| Lambda | 1M requests | $0.20 |
| API Gateway | 1M requests | $1.00 |
| S3 | Storage + requests | < $1.00 |
| Kinesis | 1 shard | $0.015/giờ |

**Tổng**: ~$50-200/tháng tùy usage

**Quan trọng**: Nhớ dọn dẹp tài nguyên sau workshop!

## Bước Tiếp theo

Khi đã đáp ứng tất cả yêu cầu, tiến hành [Triển khai Infrastructure](../3-deploy-infrastructure/).
