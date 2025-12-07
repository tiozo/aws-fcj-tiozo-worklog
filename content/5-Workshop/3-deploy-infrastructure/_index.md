---
title: "Deploy Infrastructure"
date: "`date +'%Y-%m-%d'`"
weight: 3
chapter: false
pre: "<b>3. </b>"
---

## Deploy with Terraform

### Step 1: Initialize Terraform

Navigate to the FraudTF directory and initialize:

```bash
cd FraudTF
terraform init
```

Expected output:
```
Initializing the backend...
Initializing provider plugins...
- Finding hashicorp/aws versions matching "~> 5.0"...
- Installing hashicorp/aws v5.x.x...

Terraform has been successfully initialized!
```

### Step 2: Review Deployment Plan

Preview what will be created:

```bash
terraform plan
```

This will show:
- 2 SageMaker models
- 2 SageMaker endpoint configurations
- 2 SageMaker endpoints
- 2 Lambda functions
- 1 API Gateway
- 2 S3 buckets
- 1 Kinesis stream
- IAM roles and policies

### Step 3: Deploy Infrastructure

Apply the Terraform configuration:

```bash
terraform apply
```

Type `yes` when prompted.

**Deployment time**: ~10-15 minutes (SageMaker endpoints take the longest)

### Step 4: Upload Model Artifacts

After infrastructure is created, upload your models:

```bash
# Get bucket name
MODEL_BUCKET=$(terraform output -raw model_bucket_name)

# Upload Autoencoder model
aws s3 cp models/autoencoder/model.tar.gz s3://$MODEL_BUCKET/autoencoder/

# Upload XGBoost model
aws s3 cp models/xgboost/model.tar.gz s3://$MODEL_BUCKET/xgboost/
```

Verify upload:
```bash
aws s3 ls s3://$MODEL_BUCKET/ --recursive
```

### Step 5: Get Deployment Outputs

Retrieve important endpoints and names:

```bash
# API endpoint URL
terraform output predict_endpoint_url

# Model bucket name
terraform output model_bucket_name

# Result bucket name
terraform output result_bucket_name

# Kinesis stream name
terraform output kinesis_stream_name

# SageMaker endpoint names
terraform output model_a_endpoint_name
terraform output model_b_endpoint_name
```

Save these values for testing!

### Step 6: Verify Deployment

**Check SageMaker endpoints**:
```bash
aws sagemaker list-endpoints
```

Status should be `InService`.

**Check Lambda functions**:
```bash
aws lambda list-functions | grep fraud
```

**Check API Gateway**:
```bash
API_URL=$(terraform output -raw predict_endpoint_url)
echo $API_URL
```

## Infrastructure Components Created

### S3 Buckets
- **Model bucket**: Stores ML model artifacts
  - Versioning enabled
  - Encryption enabled (AES256)
  - Public access blocked

- **Result bucket**: Stores predictions and logs
  - Same security settings as model bucket

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
  - Timeout: 30 seconds
  - Memory: 256 MB
  - Invokes both SageMaker endpoints

- **Stream Handler**:
  - Runtime: Python 3.11
  - Triggered by Kinesis stream
  - Processes batch data

### API Gateway
- Type: HTTP API
- Endpoint: `/predict`
- Method: POST
- CORS: Enabled
- Integration: Lambda proxy

### Kinesis Stream
- Shard count: 1
- Retention: 24 hours
- Encryption: Enabled

### IAM Roles
- **SageMaker role**: Access to S3 model bucket
- **Lambda role**: Invoke SageMaker, write to S3, read from Kinesis
- **API Gateway role**: Invoke Lambda

## Troubleshooting

**Issue**: Terraform apply fails with "endpoint already exists"
```bash
# Solution: Destroy and recreate
terraform destroy
terraform apply
```

**Issue**: SageMaker endpoint stuck in "Creating"
```bash
# Check CloudWatch logs
aws logs tail /aws/sagemaker/Endpoints/[endpoint-name] --follow
```

**Issue**: Lambda timeout
```bash
# Increase timeout in variables.tf
variable "lambda_timeout" {
  default = 60  # Increase from 30
}
```

## Next Steps

Infrastructure is ready! Proceed to [Testing the Model](../4-testing/).
