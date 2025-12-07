---
title: "Monitoring with CloudWatch"
date: "`date +'%Y-%m-%d'`"
weight: 5
chapter: false
pre: "<b>5. </b>"
---

## CloudWatch Monitoring (Optional)

Monitor your fraud detection system using Amazon CloudWatch.

## Lambda Function Metrics

### View Lambda Invocations

```bash
# Get function name
FUNCTION_NAME=$(terraform output -raw api_handler_function_name)

# View recent logs
aws logs tail /aws/lambda/$FUNCTION_NAME --follow
```

### Create CloudWatch Dashboard

Navigate to CloudWatch Console and create a dashboard:

1. Go to **CloudWatch** → **Dashboards** → **Create dashboard**
2. Name: `fraud-detection-dashboard`
3. Add widgets:

**Widget 1: Lambda Invocations**
- Type: Line graph
- Metric: `AWS/Lambda` → `Invocations`
- Function: Select your API handler function
- Statistic: Sum
- Period: 5 minutes

**Widget 2: Lambda Duration**
- Type: Line graph
- Metric: `AWS/Lambda` → `Duration`
- Function: Select your API handler function
- Statistic: Average
- Period: 5 minutes

**Widget 3: Lambda Errors**
- Type: Number
- Metric: `AWS/Lambda` → `Errors`
- Function: Select your API handler function
- Statistic: Sum
- Period: 1 hour

## SageMaker Endpoint Metrics

### Monitor Model Invocations

**Widget 4: Model Invocations**
- Type: Line graph
- Metric: `AWS/SageMaker` → `ModelInvocations`
- Endpoint: Select both endpoints
- Statistic: Sum
- Period: 5 minutes

**Widget 5: Model Latency**
- Type: Line graph
- Metric: `AWS/SageMaker` → `ModelLatency`
- Endpoint: Select both endpoints
- Statistic: Average
- Period: 5 minutes

### Check Endpoint Status

```bash
# Check Model A (Autoencoder)
MODEL_A=$(terraform output -raw model_a_endpoint_name)
aws sagemaker describe-endpoint --endpoint-name $MODEL_A

# Check Model B (XGBoost)
MODEL_B=$(terraform output -raw model_b_endpoint_name)
aws sagemaker describe-endpoint --endpoint-name $MODEL_B
```

## API Gateway Metrics

**Widget 6: API Requests**
- Type: Line graph
- Metric: `AWS/ApiGateway` → `Count`
- API: Select your API
- Statistic: Sum
- Period: 5 minutes

**Widget 7: API Latency**
- Type: Line graph
- Metric: `AWS/ApiGateway` → `Latency`
- API: Select your API
- Statistic: Average
- Period: 5 minutes

**Widget 8: API Errors**
- Type: Number
- Metric: `AWS/ApiGateway` → `4XXError` and `5XXError`
- API: Select your API
- Statistic: Sum
- Period: 1 hour

## Create CloudWatch Alarms

### Alarm 1: High Error Rate

```bash
aws cloudwatch put-metric-alarm \
  --alarm-name fraud-api-high-errors \
  --alarm-description "Alert when API error rate is high" \
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
  --alarm-description "Alert when API latency is high" \
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
  --alarm-description "Alert when SageMaker model has errors" \
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

Navigate to **CloudWatch** → **Logs Insights** and run queries:

**Query 1: Find Fraud Detections**
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

Add custom metric to Lambda:

```python
import boto3
cloudwatch = boto3.client('cloudwatch')

# In lambda_handler, after prediction:
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

### View Custom Metrics

Create widget for custom metrics:
- Namespace: `FraudDetection`
- Metric: `FraudDetected`
- Statistic: Sum

## S3 Monitoring

### Check Prediction Storage

```bash
RESULT_BUCKET=$(terraform output -raw result_bucket_name)

# Count predictions today
aws s3 ls s3://$RESULT_BUCKET/predictions/$(date +%Y/%m/%d)/ | wc -l

# Check bucket size
aws s3 ls s3://$RESULT_BUCKET --recursive --summarize | grep "Total Size"
```

## Cost Monitoring

### Enable Cost Explorer

1. Go to **AWS Cost Explorer**
2. Filter by service:
   - SageMaker
   - Lambda
   - API Gateway
   - S3
3. View daily costs

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

## Monitoring Best Practices

1. **Set up alarms** for critical metrics
2. **Review logs daily** for errors
3. **Monitor costs** to avoid surprises
4. **Track fraud detection rate** over time
5. **Optimize based on metrics** (e.g., reduce instance size if underutilized)

## Next Steps

Monitoring is configured! Proceed to [Cleanup](../6-cleanup/).
