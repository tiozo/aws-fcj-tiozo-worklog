---
title: "Cleanup"
date: "`date +'%Y-%m-%d'`"
weight: 6
chapter: false
pre: "<b>6. </b>"
---

## Clean Up Resources

**Important**: To avoid ongoing charges, clean up all resources created during this workshop.

## Step 1: Empty S3 Buckets

Terraform cannot delete non-empty S3 buckets. Empty them first:

```bash
# Get bucket names
MODEL_BUCKET=$(terraform output -raw model_bucket_name)
RESULT_BUCKET=$(terraform output -raw result_bucket_name)

# Empty model bucket
aws s3 rm s3://$MODEL_BUCKET --recursive

# Empty result bucket
aws s3 rm s3://$RESULT_BUCKET --recursive

# Verify buckets are empty
aws s3 ls s3://$MODEL_BUCKET
aws s3 ls s3://$RESULT_BUCKET
```

## Step 2: Destroy Infrastructure with Terraform

Navigate to the FraudTF directory and destroy all resources:

```bash
cd FraudTF
terraform destroy
```

Type `yes` when prompted.

**Destruction time**: ~5-10 minutes

### Resources Being Deleted

Terraform will delete:
- ✓ 2 SageMaker endpoints
- ✓ 2 SageMaker endpoint configurations
- ✓ 2 SageMaker models
- ✓ 2 Lambda functions
- ✓ 1 API Gateway
- ✓ 2 S3 buckets
- ✓ 1 Kinesis stream
- ✓ IAM roles and policies
- ✓ CloudWatch log groups

## Step 3: Verify Deletion

### Check SageMaker Endpoints

```bash
aws sagemaker list-endpoints
```

Should return empty or not show your endpoints.

### Check Lambda Functions

```bash
aws lambda list-functions | grep fraud
```

Should return nothing.

### Check S3 Buckets

```bash
aws s3 ls | grep aiml-serverless
```

Should return nothing.

### Check API Gateway

```bash
aws apigatewayv2 get-apis | grep fraud
```

Should return nothing.

## Step 4: Delete CloudWatch Resources (Optional)

If you created custom dashboards or alarms:

### Delete Dashboard

```bash
aws cloudwatch delete-dashboards --dashboard-names fraud-detection-dashboard
```

### Delete Alarms

```bash
# List alarms
aws cloudwatch describe-alarms | grep fraud

# Delete specific alarms
aws cloudwatch delete-alarms --alarm-names \
  fraud-api-high-errors \
  fraud-api-high-latency \
  sagemaker-model-errors
```

### Delete Log Groups

CloudWatch log groups may persist:

```bash
# List log groups
aws logs describe-log-groups | grep fraud

# Delete log groups
aws logs delete-log-group --log-group-name /aws/lambda/[function-name]
aws logs delete-log-group --log-group-name /aws/sagemaker/Endpoints/[endpoint-name]
```

## Step 5: Check for Remaining Resources

### Use AWS Console

1. Go to **AWS Console** → **Resource Groups & Tag Editor**
2. Search for resources with tag or name containing "fraud" or "aiml-serverless"
3. Manually delete any remaining resources

### Check Costs

1. Go to **AWS Cost Explorer**
2. Filter by service (SageMaker, Lambda, API Gateway, S3)
3. Verify costs are decreasing

## Troubleshooting Cleanup

### Issue: Terraform destroy fails

**Error**: "Resource still in use"

```bash
# Solution: Wait a few minutes and retry
sleep 300
terraform destroy
```

### Issue: S3 bucket not empty

**Error**: "BucketNotEmpty"

```bash
# Solution: Force delete all objects including versions
aws s3api delete-objects \
  --bucket $BUCKET_NAME \
  --delete "$(aws s3api list-object-versions \
    --bucket $BUCKET_NAME \
    --query '{Objects: Versions[].{Key:Key,VersionId:VersionId}}' \
    --output json)"
```

### Issue: SageMaker endpoint stuck in "Deleting"

```bash
# Check status
aws sagemaker describe-endpoint --endpoint-name [endpoint-name]

# Wait for deletion to complete (can take 5-10 minutes)
```

### Issue: IAM role in use

**Error**: "Cannot delete entity, must detach all policies first"

```bash
# List attached policies
aws iam list-attached-role-policies --role-name [role-name]

# Detach policies manually
aws iam detach-role-policy \
  --role-name [role-name] \
  --policy-arn [policy-arn]
```

## Manual Cleanup Checklist

If Terraform destroy fails, manually delete in this order:

1. ☐ SageMaker endpoints
2. ☐ SageMaker endpoint configurations
3. ☐ SageMaker models
4. ☐ Lambda functions
5. ☐ API Gateway
6. ☐ Kinesis stream
7. ☐ S3 buckets (after emptying)
8. ☐ IAM roles and policies
9. ☐ CloudWatch log groups
10. ☐ CloudWatch alarms and dashboards

## Cost Verification

After cleanup, verify no ongoing charges:

1. Wait 24 hours
2. Check **AWS Cost Explorer**
3. Verify no charges for:
   - SageMaker
   - Lambda (should be minimal)
   - API Gateway (should be minimal)
   - S3 (should be minimal)
   - Kinesis

## Cleanup Complete!

You have successfully:
- ✓ Deployed a fraud detection ML system
- ✓ Tested the API with real transactions
- ✓ Monitored with CloudWatch
- ✓ Cleaned up all resources

## What You Learned

- Deploy ML models to SageMaker
- Use Terraform for infrastructure as code
- Create serverless APIs with Lambda and API Gateway
- Process streaming data with Kinesis
- Monitor with CloudWatch
- Manage AWS costs effectively

## Next Steps

- Explore other AWS ML services (Bedrock, Rekognition, Comprehend)
- Build more complex ML pipelines
- Implement CI/CD for ML models
- Learn about MLOps best practices

Thank you for completing this workshop!
