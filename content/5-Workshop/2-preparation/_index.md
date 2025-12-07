---
title: "Preparation"
date: "`date +'%Y-%m-%d'`"
weight: 2
chapter: false
pre: "<b>2. </b>"
---

## Prerequisites Check

### 1. AWS Account Setup

Ensure you have an AWS account with appropriate permissions:
- SageMaker full access
- Lambda full access
- S3 full access
- API Gateway full access
- Kinesis full access
- IAM role creation

### 2. Install Required Tools

**AWS CLI**:
```bash
# Check version
aws --version

# Configure credentials
aws configure
# Enter: Access Key ID, Secret Access Key, Region (us-east-1), Output format (json)
```

**Terraform**:
```bash
# Check version (need >= 1.0)
terraform --version

# If not installed, download from: https://www.terraform.io/downloads
```

**Python**:
```bash
# Check version (need >= 3.8)
python3 --version

# Install required packages
pip3 install boto3 requests
```

### 3. Download Workshop Files

**Download from Google Drive**:
[Download FraudTF folder and models](https://drive.google.com/drive/folders/15xlnf2SR9ZUuJ4iaUyV6NUrCJOg5eYd-?usp=sharing)

The folder contains:
- Terraform configurations (*.tf files)
- Lambda code (lambda_code/ directory)
- Trained models (autoencoder and xgboost)

```bash
# Extract and navigate to the folder
cd ~/workspace/FraudTF
```

### 4. Prepare Model Files

You need two trained models in `.tar.gz` format:

**Directory structure**:
```
FraudTF/
├── models/
│   ├── autoencoder/
│   │   └── model.tar.gz
│   └── xgboost/
│       └── model.tar.gz
```

**Model requirements**:
- **Autoencoder**: PyTorch model with `inference.py` script (required for custom containers or non-AWS native frameworks like sklearn)
- **XGBoost**: Model in standard format (no `inference.py` needed - uses AWS native XGBoost container)

### 5. Verify Terraform Configuration

Check the `variables.tf` file:
```bash
cd FraudTF
cat variables.tf
```

Default values:
- `aws_region`: us-east-1
- `project_name`: aiml-serverless
- Instance type: ml.m5.large

You can customize by creating `terraform.tfvars`:
```hcl
aws_region   = "us-east-1"
project_name = "fraud-detection"
```

### 6. Verify AWS Permissions

Test AWS access:
```bash
# List S3 buckets
aws s3 ls

# Check SageMaker access
aws sagemaker list-endpoints

# Check Lambda access
aws lambda list-functions
```

## Estimated Costs

Before deploying, understand the costs:

| Service | Resource | Estimated Cost |
|---------|----------|----------------|
| SageMaker | 2 x ml.m5.large | $0.269/hour |
| Lambda | 1M requests | $0.20 |
| API Gateway | 1M requests | $1.00 |
| S3 | Storage + requests | < $1.00 |
| Kinesis | 1 shard | $0.015/hour |

**Total**: ~$50-200/month depending on usage

**Important**: Remember to clean up resources after the workshop!

## Next Steps

Once all prerequisites are met, proceed to [Deploy Infrastructure](../3-deploy-infrastructure/).
