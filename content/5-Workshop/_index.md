---
title: "Deploy ML Model to Amazon SageMaker"
date: "`date +'%Y-%m-%d'`"
weight: 5
chapter: false
---

# Deploy Machine Learning Model to Amazon SageMaker

## Overview

In this workshop, you will learn how to deploy a pre-trained machine learning model to Amazon SageMaker using Terraform for infrastructure as code. The workshop covers the complete deployment lifecycle from infrastructure provisioning to testing and cleanup.

## Workshop Objectives

By the end of this workshop, you will be able to:
- Understand Amazon SageMaker deployment architecture
- Use Terraform to provision SageMaker infrastructure
- Deploy a pre-trained ML model to SageMaker endpoint
- Test the deployed model with API requests
- Monitor model performance using CloudWatch
- Clean up resources properly

## AWS Services Used

- **Amazon SageMaker**: Deploy and host machine learning models
- **Amazon S3**: Store model artifacts
- **Amazon CloudWatch**: Monitor endpoint metrics and logs
- **AWS IAM**: Manage permissions and roles
- **Amazon API Gateway** (optional): Create REST API for model inference

## Prerequisites

Before starting this workshop, ensure you have:

1. **AWS Account**: An active AWS account with appropriate permissions
2. **AWS CLI**: Installed and configured with your credentials
   ```bash
   aws --version
   aws configure
   ```
3. **Terraform**: Version 1.0 or later
   ```bash
   terraform --version
   ```
4. **Python**: Version 3.8 or later with pip
   ```bash
   python3 --version
   ```
5. **Git**: For cloning the repository
6. **Basic Knowledge**: 
   - AWS services fundamentals
   - Terraform basics
   - Python programming
   - Machine learning concepts

## Estimated Cost

Running this workshop may incur AWS charges. Estimated costs:
- SageMaker endpoint: ~$0.05-0.10 per hour
- S3 storage: Minimal (< $0.01)
- CloudWatch: Minimal (< $0.01)

**Important**: Remember to clean up resources after completing the workshop to avoid ongoing charges.

## Workshop Duration

Approximately 60-90 minutes

## Workshop Structure

1. [Introduction](1-introduction/)
2. [Preparation](2-preparation/)
3. [Deploy Infrastructure](3-deploy-infrastructure/)
4. [Testing the Model](4-testing/)
5. [Monitoring with CloudWatch](5-monitoring/)
6. [Cleanup](6-cleanup/)

Let's get started!
