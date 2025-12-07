---
title: "Introduction"
date: "`date +'%Y-%m-%d'`"
weight: 1
chapter: false
pre: "<b>1. </b>"
---

## Workshop Overview

This workshop guides you through deploying a Fraud Detection system using Machine Learning on AWS. The system uses an ensemble model combining XGBoost and Autoencoder to detect fraudulent transactions in real-time.

## System Architecture

### Synchronous API Flow
```
Client → API Gateway → Lambda → SageMaker Endpoints → Response
                                    ↓
                                   S3 (save results)
```

### Asynchronous Stream Flow
```
Data Source → Kinesis Stream → Lambda → S3 (batch processing)
```

## Main Components

1. **Amazon SageMaker**: 
   - 2 endpoints (XGBoost + Autoencoder)
   - Instance type: ml.m5.large
   - Real-time inference

2. **AWS Lambda**:
   - API Handler: Process requests from API Gateway
   - Stream Handler: Process data from Kinesis

3. **Amazon API Gateway**:
   - HTTP API endpoint `/predict`
   - CORS enabled

4. **Amazon Kinesis**:
   - Data Stream for batch processing
   - 1 shard (scalable)

5. **Amazon S3**:
   - Model bucket: Store model artifacts
   - Result bucket: Store predictions and logs

6. **AWS IAM**:
   - Roles with least privilege principle

## Ensemble Model

**XGBoost (80% weight)**:
- Supervised learning
- 15 features
- Detect patterns from labeled data

**Autoencoder (20% weight)**:
- Unsupervised learning  
- 13 features
- Detect anomalies

**Threshold**: 0.7835 (F1 Score: 0.8381)

## Features Used

Input from client:
- `amount`: Transaction amount
- `user_id`: User ID
- `payee_id`: Payee ID
- `timestamp`: Transaction timestamp

The system automatically generates 15 features for ML models including time-based, amount-based, velocity, and behavioral features.

## Architecture Benefits

- **Serverless**: No server management
- **Scalable**: Auto-scaling with traffic
- **Cost-effective**: Pay per use
- **Real-time**: Low latency for fraud detection
- **Infrastructure as Code**: Easy to replicate and version control
