---
title: "Giám sát với CloudWatch"
date: "`date +'%Y-%m-%d'`"
weight: 5
chapter: false
pre: "<b>5. </b>"
---

## Giám sát CloudWatch (Tùy chọn)

Giám sát hệ thống fraud detection sử dụng Amazon CloudWatch.

## Lambda Function Metrics

### Xem Lambda Invocations

```bash
# Lấy function name
FUNCTION_NAME=$(terraform output -raw api_handler_function_name)

# Xem logs gần đây
aws logs tail /aws/lambda/$FUNCTION_NAME --follow
```

### Tạo CloudWatch Dashboard

Truy cập CloudWatch Console và tạo dashboard:

1. Vào **CloudWatch** → **Dashboards** → **Create dashboard**
2. Tên: `fraud-detection-dashboard`
3. Thêm widgets:

**Widget 1: Lambda Invocations**
- Type: Line graph
- Metric: `AWS/Lambda` → `Invocations`
- Function: Chọn API handler function
- Statistic: Sum
- Period: 5 phút

**Widget 2: Lambda Duration**
- Type: Line graph
- Metric: `AWS/Lambda` → `Duration`
- Function: Chọn API handler function
- Statistic: Average
- Period: 5 phút

**Widget 3: Lambda Errors**
- Type: Number
- Metric: `AWS/Lambda` → `Errors`
- Function: Chọn API handler function
- Statistic: Sum
- Period: 1 giờ

## SageMaker Endpoint Metrics

### Giám sát Model Invocations

**Widget 4: Model Invocations**
- Type: Line graph
- Metric: `AWS/SageMaker` → `ModelInvocations`
- Endpoint: Chọn cả hai endpoints
- Statistic: Sum
- Period: 5 phút

**Widget 5: Model Latency**
- Type: Line graph
- Metric: `AWS/SageMaker` → `ModelLatency`
- Endpoint: Chọn cả hai endpoints
- Statistic: Average
- Period: 5 phút

### Kiểm tra Endpoint Status

```bash
# Kiểm tra Model A (Autoencoder)
MODEL_A=$(terraform output -raw model_a_endpoint_name)
aws sagemaker describe-endpoint --endpoint-name $MODEL_A

# Kiểm tra Model B (XGBoost)
MODEL_B=$(terraform output -raw model_b_endpoint_name)
aws sagemaker describe-endpoint --endpoint-name $MODEL_B
```

## API Gateway Metrics

**Widget 6: API Requests**
- Type: Line graph
- Metric: `AWS/ApiGateway` → `Count`
- API: Chọn API của bạn
- Statistic: Sum
- Period: 5 phút

**Widget 7: API Latency**
- Type: Line graph
- Metric: `AWS/ApiGateway` → `Latency`
- API: Chọn API của bạn
- Statistic: Average
- Period: 5 phút

**Widget 8: API Errors**
- Type: Number
- Metric: `AWS/ApiGateway` → `4XXError` và `5XXError`
- API: Chọn API của bạn
- Statistic: Sum
- Period: 1 giờ

## Tạo CloudWatch Alarms

### Alarm 1: High Error Rate

```bash
aws cloudwatch put-metric-alarm \
  --alarm-name fraud-api-high-errors \
  --alarm-description "Cảnh báo khi API error rate cao" \
  --metric-name Errors \
  --namespace AWS/Lambda \
  --statistic Sum \
  --period 300 \
  --threshold 10 \
  --comparison-operator GreaterThanThreshold \
  --evaluation-periods 1 \
  --dimensions Name=FunctionName,Value=$FUNCTION_NAME
```

### Alarm 2: High Latency

```bash
aws cloudwatch put-metric-alarm \
  --alarm-name fraud-api-high-latency \
  --alarm-description "Cảnh báo khi API latency cao" \
  --metric-name Duration \
  --namespace AWS/Lambda \
  --statistic Average \
  --period 300 \
  --threshold 5000 \
  --comparison-operator GreaterThanThreshold \
  --evaluation-periods 2 \
  --dimensions Name=FunctionName,Value=$FUNCTION_NAME
```

### Alarm 3: SageMaker Endpoint Failures

```bash
aws cloudwatch put-metric-alarm \
  --alarm-name sagemaker-model-errors \
  --alarm-description "Cảnh báo khi SageMaker model có lỗi" \
  --metric-name ModelInvocation4XXErrors \
  --namespace AWS/SageMaker \
  --statistic Sum \
  --period 300 \
  --threshold 5 \
  --comparison-operator GreaterThanThreshold \
  --evaluation-periods 1 \
  --dimensions Name=EndpointName,Value=$MODEL_A
```

## CloudWatch Logs Insights

### Query Lambda Logs

Vào **CloudWatch** → **Logs Insights** và chạy queries:

**Query 1: Tìm Fraud Detections**
```sql
fields @timestamp, @message
| filter @message like /is_fraud.*true/
| sort @timestamp desc
| limit 20
```

**Query 2: Average Ensemble Score**
```sql
fields @timestamp, ensemble_score
| parse @message /ensemble_score.*?(\d+\.\d+)/
| stats avg(ensemble_score) by bin(5m)
```

**Query 3: Error Analysis**
```sql
fields @timestamp, @message
| filter @message like /Error/
| sort @timestamp desc
| limit 50
```

## Custom Metrics

### Log Fraud Detection Rate

Thêm custom metric vào Lambda:

```python
import boto3
cloudwatch = boto3.client('cloudwatch')

# Trong lambda_handler, sau prediction:
cloudwatch.put_metric_data(
    Namespace='FraudDetection',
    MetricData=[
        {
            'MetricName': 'FraudDetected',
            'Value': 1 if is_fraud else 0,
            'Unit': 'Count'
        },
        {
            'MetricName': 'EnsembleScore',
            'Value': ensemble_score,
            'Unit': 'None'
        }
    ]
)
```

### Xem Custom Metrics

Tạo widget cho custom metrics:
- Namespace: `FraudDetection`
- Metric: `FraudDetected`
- Statistic: Sum

## S3 Monitoring

### Kiểm tra Prediction Storage

```bash
RESULT_BUCKET=$(terraform output -raw result_bucket_name)

# Đếm predictions hôm nay
aws s3 ls s3://$RESULT_BUCKET/predictions/$(date +%Y/%m/%d)/ | wc -l

# Kiểm tra bucket size
aws s3 ls s3://$RESULT_BUCKET --recursive --summarize | grep "Total Size"
```

## Cost Monitoring

### Enable Cost Explorer

1. Vào **AWS Cost Explorer**
2. Filter theo service:
   - SageMaker
   - Lambda
   - API Gateway
   - S3
3. Xem daily costs

### Set Budget Alert

```bash
aws budgets create-budget \
  --account-id $(aws sts get-caller-identity --query Account --output text) \
  --budget file://budget.json
```

**budget.json**:
```json
{
  "BudgetName": "fraud-detection-budget",
  "BudgetLimit": {
    "Amount": "100",
    "Unit": "USD"
  },
  "TimeUnit": "MONTHLY",
  "BudgetType": "COST"
}
```

## Best Practices Giám sát

1. **Thiết lập alarms** cho critical metrics
2. **Review logs hàng ngày** để tìm errors
3. **Giám sát costs** để tránh bất ngờ
4. **Track fraud detection rate** theo thời gian
5. **Tối ưu dựa trên metrics** (ví dụ: giảm instance size nếu underutilized)

## Bước Tiếp theo

Monitoring đã được cấu hình! Tiến hành [Cleanup](../6-cleanup/).
