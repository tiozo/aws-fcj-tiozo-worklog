---
title: "Dọn dẹp"
date: "`date +'%Y-%m-%d'`"
weight: 6
chapter: false
pre: "<b>6. </b>"
---

## Dọn dẹp Tài nguyên

**Quan trọng**: Để tránh phát sinh chi phí, dọn dẹp tất cả tài nguyên đã tạo trong workshop.

## Bước 1: Làm rỗng S3 Buckets

Terraform không thể xóa S3 buckets có dữ liệu. Làm rỗng chúng trước:

```bash
# Lấy tên buckets
MODEL_BUCKET=$(terraform output -raw model_bucket_name)
RESULT_BUCKET=$(terraform output -raw result_bucket_name)

# Làm rỗng model bucket
aws s3 rm s3://$MODEL_BUCKET --recursive

# Làm rỗng result bucket
aws s3 rm s3://$RESULT_BUCKET --recursive

# Xác minh buckets đã rỗng
aws s3 ls s3://$MODEL_BUCKET
aws s3 ls s3://$RESULT_BUCKET
```

## Bước 2: Destroy Infrastructure với Terraform

Di chuyển đến thư mục FraudTF và destroy tất cả resources:

```bash
cd FraudTF
terraform destroy
```

Nhập `yes` khi được hỏi.

**Thời gian destroy**: ~5-10 phút

### Tài nguyên Đang Bị Xóa

Terraform sẽ xóa:
- ✓ 2 SageMaker endpoints
- ✓ 2 SageMaker endpoint configurations
- ✓ 2 SageMaker models
- ✓ 2 Lambda functions
- ✓ 1 API Gateway
- ✓ 2 S3 buckets
- ✓ 1 Kinesis stream
- ✓ IAM roles và policies
- ✓ CloudWatch log groups

## Bước 3: Xác minh Đã Xóa

### Kiểm tra SageMaker Endpoints

```bash
aws sagemaker list-endpoints
```

Nên trả về rỗng hoặc không hiển thị endpoints của bạn.

### Kiểm tra Lambda Functions

```bash
aws lambda list-functions | grep fraud
```

Nên không trả về gì.

### Kiểm tra S3 Buckets

```bash
aws s3 ls | grep aiml-serverless
```

Nên không trả về gì.

### Kiểm tra API Gateway

```bash
aws apigatewayv2 get-apis | grep fraud
```

Nên không trả về gì.

## Bước 4: Xóa CloudWatch Resources (Tùy chọn)

Nếu bạn tạo custom dashboards hoặc alarms:

### Xóa Dashboard

```bash
aws cloudwatch delete-dashboards --dashboard-names fraud-detection-dashboard
```

### Xóa Alarms

```bash
# List alarms
aws cloudwatch describe-alarms | grep fraud

# Xóa specific alarms
aws cloudwatch delete-alarms --alarm-names \
  fraud-api-high-errors \
  fraud-api-high-latency \
  sagemaker-model-errors
```

### Xóa Log Groups

CloudWatch log groups có thể còn lại:

```bash
# List log groups
aws logs describe-log-groups | grep fraud

# Xóa log groups
aws logs delete-log-group --log-group-name /aws/lambda/[function-name]
aws logs delete-log-group --log-group-name /aws/sagemaker/Endpoints/[endpoint-name]
```

## Bước 5: Kiểm tra Tài nguyên Còn lại

### Sử dụng AWS Console

1. Vào **AWS Console** → **Resource Groups & Tag Editor**
2. Tìm resources có tag hoặc tên chứa "fraud" hoặc "aiml-serverless"
3. Xóa thủ công các resources còn lại

### Kiểm tra Costs

1. Vào **AWS Cost Explorer**
2. Filter theo service (SageMaker, Lambda, API Gateway, S3)
3. Xác minh costs đang giảm

## Xử lý Sự cố Cleanup

### Vấn đề: Terraform destroy fails

**Lỗi**: "Resource still in use"

```bash
# Giải pháp: Đợi vài phút và thử lại
sleep 300
terraform destroy
```

### Vấn đề: S3 bucket không rỗng

**Lỗi**: "BucketNotEmpty"

```bash
# Giải pháp: Force delete tất cả objects bao gồm versions
aws s3api delete-objects \
  --bucket $BUCKET_NAME \
  --delete "$(aws s3api list-object-versions \
    --bucket $BUCKET_NAME \
    --query '{Objects: Versions[].{Key:Key,VersionId:VersionId}}' \
    --output json)"
```

### Vấn đề: SageMaker endpoint stuck ở "Deleting"

```bash
# Kiểm tra status
aws sagemaker describe-endpoint --endpoint-name [endpoint-name]

# Đợi deletion hoàn thành (có thể mất 5-10 phút)
```

### Vấn đề: IAM role đang được sử dụng

**Lỗi**: "Cannot delete entity, must detach all policies first"

```bash
# List attached policies
aws iam list-attached-role-policies --role-name [role-name]

# Detach policies thủ công
aws iam detach-role-policy \
  --role-name [role-name] \
  --policy-arn [policy-arn]
```

## Checklist Cleanup Thủ công

Nếu Terraform destroy fails, xóa thủ công theo thứ tự:

1. ☐ SageMaker endpoints
2. ☐ SageMaker endpoint configurations
3. ☐ SageMaker models
4. ☐ Lambda functions
5. ☐ API Gateway
6. ☐ Kinesis stream
7. ☐ S3 buckets (sau khi làm rỗng)
8. ☐ IAM roles và policies
9. ☐ CloudWatch log groups
10. ☐ CloudWatch alarms và dashboards

## Xác minh Chi phí

Sau cleanup, xác minh không còn phát sinh chi phí:

1. Đợi 24 giờ
2. Kiểm tra **AWS Cost Explorer**
3. Xác minh không có charges cho:
   - SageMaker
   - Lambda (nên là tối thiểu)
   - API Gateway (nên là tối thiểu)
   - S3 (nên là tối thiểu)
   - Kinesis

## Cleanup Hoàn tất!

Bạn đã thành công:
- ✓ Deploy hệ thống ML fraud detection
- ✓ Test API với real transactions
- ✓ Giám sát với CloudWatch
- ✓ Dọn dẹp tất cả resources

## Những gì Bạn Đã Học

- Deploy ML models lên SageMaker
- Sử dụng Terraform cho infrastructure as code
- Tạo serverless APIs với Lambda và API Gateway
- Xử lý streaming data với Kinesis
- Giám sát với CloudWatch
- Quản lý AWS costs hiệu quả

## Bước Tiếp theo

- Khám phá các AWS ML services khác (Bedrock, Rekognition, Comprehend)
- Xây dựng ML pipelines phức tạp hơn
- Implement CI/CD cho ML models
- Học về MLOps best practices

Cảm ơn bạn đã hoàn thành workshop này!
