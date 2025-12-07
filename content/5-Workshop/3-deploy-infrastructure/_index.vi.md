---
title: "Triển khai Infrastructure"
date: "`date +'%Y-%m-%d'`"
weight: 3
chapter: false
pre: "<b>3. </b>"
---

## Triển khai với Terraform

### Bước 1: Khởi tạo Terraform

Di chuyển đến thư mục FraudTF và khởi tạo:

```bash
cd FraudTF
terraform init
```

Kết quả mong đợi:
```
Initializing the backend...
Initializing provider plugins...
- Finding hashicorp/aws versions matching "~> 5.0"...
- Installing hashicorp/aws v5.x.x...

Terraform has been successfully initialized!
```

### Bước 2: Xem lại Deployment Plan

Xem trước những gì sẽ được tạo:

```bash
terraform plan
```

Sẽ hiển thị:
- 2 SageMaker models
- 2 SageMaker endpoint configurations
- 2 SageMaker endpoints
- 2 Lambda functions
- 1 API Gateway
- 2 S3 buckets
- 1 Kinesis stream
- IAM roles và policies

### Bước 3: Triển khai Infrastructure

Apply Terraform configuration:

```bash
terraform apply
```

Nhập `yes` khi được hỏi.

**Thời gian deploy**: ~10-15 phút (SageMaker endpoints mất nhiều thời gian nhất)

### Bước 4: Upload Model Artifacts

Sau khi infrastructure được tạo, upload models:

```bash
# Lấy tên bucket
MODEL_BUCKET=$(terraform output -raw model_bucket_name)

# Upload Autoencoder model
aws s3 cp models/autoencoder/model.tar.gz s3://$MODEL_BUCKET/autoencoder/

# Upload XGBoost model
aws s3 cp models/xgboost/model.tar.gz s3://$MODEL_BUCKET/xgboost/
```

Xác minh upload:
```bash
aws s3 ls s3://$MODEL_BUCKET/ --recursive
```

### Bước 5: Lấy Deployment Outputs

Lấy các endpoints và names quan trọng:

```bash
# API endpoint URL
terraform output predict_endpoint_url

# Tên model bucket
terraform output model_bucket_name

# Tên result bucket
terraform output result_bucket_name

# Tên Kinesis stream
terraform output kinesis_stream_name

# Tên SageMaker endpoints
terraform output model_a_endpoint_name
terraform output model_b_endpoint_name
```

Lưu lại các giá trị này để testing!

### Bước 6: Xác minh Deployment

**Kiểm tra SageMaker endpoints**:
```bash
aws sagemaker list-endpoints
```

Status phải là `InService`.

**Kiểm tra Lambda functions**:
```bash
aws lambda list-functions | grep fraud
```

**Kiểm tra API Gateway**:
```bash
API_URL=$(terraform output -raw predict_endpoint_url)
echo $API_URL
```

## Các Thành phần Infrastructure Đã Tạo

### S3 Buckets
- **Model bucket**: Lưu ML model artifacts
  - Versioning enabled
  - Encryption enabled (AES256)
  - Public access blocked

- **Result bucket**: Lưu predictions và logs
  - Cùng security settings với model bucket

### SageMaker Resources
- **Model A (Autoencoder)**:
  - Instance: ml.m5.large
  - Container: PyTorch inference
  - Model location: s3://bucket/autoencoder/model.tar.gz

- **Model B (XGBoost)**:
  - Instance: ml.m5.large
  - Container: XGBoost inference
  - Model location: s3://bucket/xgboost/model.tar.gz

### Lambda Functions
- **API Handler**: 
  - Runtime: Python 3.11
  - Timeout: 30 giây
  - Memory: 256 MB
  - Gọi cả hai SageMaker endpoints

- **Stream Handler**:
  - Runtime: Python 3.11
  - Triggered bởi Kinesis stream
  - Xử lý batch data

### API Gateway
- Type: HTTP API
- Endpoint: `/predict`
- Method: POST
- CORS: Enabled
- Integration: Lambda proxy

### Kinesis Stream
- Shard count: 1
- Retention: 24 giờ
- Encryption: Enabled

### IAM Roles
- **SageMaker role**: Truy cập S3 model bucket
- **Lambda role**: Invoke SageMaker, ghi vào S3, đọc từ Kinesis
- **API Gateway role**: Invoke Lambda

## Xử lý Sự cố

**Vấn đề**: Terraform apply fails với "endpoint already exists"
```bash
# Giải pháp: Destroy và tạo lại
terraform destroy
terraform apply
```

**Vấn đề**: SageMaker endpoint bị stuck ở "Creating"
```bash
# Kiểm tra CloudWatch logs
aws logs tail /aws/sagemaker/Endpoints/[endpoint-name] --follow
```

**Vấn đề**: Lambda timeout
```bash
# Tăng timeout trong variables.tf
variable "lambda_timeout" {
  default = 60  # Tăng từ 30
}
```

## Bước Tiếp theo

Infrastructure đã sẵn sàng! Tiến hành [Testing Model](../4-testing/).
