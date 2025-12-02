---
title: "Blog 1"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

# Using Amazon GuardDuty Malware Protection to scan uploads to Amazon S3

## Using AWS Transfer Family and GuardDuty for Malware Protection

How to use AWS Transfer Family and GuardDuty to protect against malware

*Authors: James Abbot, Suhas Pasricha, Santhosh Srinivasan*
*Published: April 30, 2025*

**Categories:** [Advanced (300)](https://aws.amazon.com/blogs/security/category/learning-levels/advanced-300/), [Amazon EventBridge](https://aws.amazon.com/blogs/security/category/application-integration/amazon-eventbridge/), [Amazon GuardDuty](https://aws.amazon.com/blogs/security/category/security-identity-compliance/amazon-guardduty/), [Amazon Simple Notification Service (SNS)](https://aws.amazon.com/blogs/security/category/messaging/amazon-simple-notification-service-sns/), [Amazon Simple Storage Service (S3)](https://aws.amazon.com/blogs/security/category/storage/amazon-simple-storage-services-s3/), [AWS Lambda](https://aws.amazon.com/blogs/security/category/compute/aws-lambda/), [AWS Step Functions](https://aws.amazon.com/blogs/security/category/application-services/aws-step-functions/), [AWS Transfer Family](https://aws.amazon.com/blogs/security/category/migration/aws-transfer-family/), [Security, Identity, & Compliance](https://aws.amazon.com/blogs/security/category/security-identity-compliance/), [Storage](https://aws.amazon.com/blogs/security/category/storage/), [Technical How-to](https://aws.amazon.com/blogs/security/category/post-types/technical-how-to/)

Organizations often need to securely share files with external parties over the internet. Allowing public access to a file transfer server exposes organizations to potential threats, such as malware-infected files uploaded by malicious actors or inadvertently by legitimate users. To mitigate this risk, companies can implement steps to ensure that files received through public channels are scanned for malware before processing.

This post demonstrates how to use **AWS Transfer Family** and **Amazon GuardDuty** to scan files uploaded via a secure FTP (SFTP) server for malware as part of an overall file transfer workflow. For readers who may have read a previous blog post on this topic, the key difference is that this solution is fully managed and doesn't require deploying compute resources. GuardDuty automatically updates malware signatures every 15 minutes instead of using a container image for scanning, avoiding the need for manual patching to keep signatures updated.

## Prerequisites:

To deploy the solution in this post, you will need:

* An AWS account: You need access to AWS to deploy this solution. If you don't have an account yet, see [Get started building on AWS today](https://aws.amazon.com/getting-started/).
* AWS CLI: Install and configure the AWS Command Line Interface (AWS CLI) to authenticate with your AWS account. Set up environment variables for your AWS account using access tokens and secret access keys for your environment.
* Git: You will use Git to download the example source code from GitHub.
* Terraform: You will use Terraform to run automation. Follow the Terraform installation guide to download and set up Terraform.

## Solution overview

This solution uses **Transfer Family** and **GuardDuty**. Transfer Family provides a secure file transfer service that you can use to set up an SFTP server, and GuardDuty is an intelligent threat detection service. GuardDuty monitors malicious activities and anomalous behavior to protect AWS accounts, workloads, and data. At a high level, the solution uses the following steps:

* A user uploads a file through the Transfer Family SFTP server.
* A **workflow** managed by Transfer Family calls **AWS Lambda** to execute an **AWS Step Functions** workflow. This process only starts after the file is successfully uploaded.
* **Partial uploads** to the SFTP server will call a Lambda error handling function to report incomplete upload errors.
* An AWS Step Functions **state machine** calls a Lambda function to move uploaded files to an **Amazon Simple Storage Service (Amazon S3)** bucket for processing and then starts scanning with GuardDuty.
* GuardDuty scan results are sent as a **callback** to AWS Step Functions.
* Infected files are moved or cleaned.
* The workflow sends results to users through an **Amazon Simple Notification Service (Amazon SNS)** **topic**. This can be error notifications, notifications about malicious files detected during scanning, or notifications about successful uploads and files that have been scanned clean for further processing.

## Solution architecture and detailed walkthrough

This solution uses GuardDuty's **Malware Protection for S3** feature to scan newly uploaded objects in S3 buckets. You can use this GuardDuty feature to set up a malware protection plan for an S3 bucket at the bucket level or to monitor specific object prefixes.

**Figure 1: Solution architecture.**

The following steps (see illustration 1) describe the workflow of this solution, starting from when a file is uploaded until it is scanned and marked as safe or infected, leading to subsequent steps that can be customized based on your use case.

1. A file is uploaded using **SFTP** protocol through **Transfer Family**.
2. If the file is uploaded successfully, Transfer Family uploads the file to an S3 bucket named **Unscanned** and the **Managed Workflow Complete** process is triggered. This is the process used to handle successful uploads and calls the Lambda function **Step Function Invoker**.
3. **Step Function Invoker** starts the state machine and begins the first step in the process by calling the Lambda function **GuardDuty – Scan**.
4. The **GuardDuty – Scan** function moves the file to the **Processing** bucket. This is the bucket from which files will be scanned.
5. When an object upload activity is detected, GuardDuty automatically scans that object. In this implementation, a malware protection plan is created for the **Processing** bucket.
6. When the scan is complete, GuardDuty publishes the scan results to **Amazon EventBridge**.
7. An EventBridge rule has been created to call a Lambda **Callback** function whenever a scan completion event occurs. EventBridge will call the function with an event containing the scan results.
8. The Lambda **Callback** function notifies the **GuardDuty – Scan** task using the **callback task integration pattern**. The results of the Amazon GuardDuty scan are returned to the **GuardDuty – Scan** function and these results are passed to the **Move File** task.
9. If the result is a clean scan with no threats detected, the **Move File** task places the file in the S3 **Clean** bucket, indicating that the file has been successfully scanned and is safe for further processing. At this point, the **Move File** function will publish a message to the SNS **Success** topic to notify subscribers.
10. If the result shows the file is malicious, the **Move File** function will instead move the file to the S3 **Quarantine** bucket for further investigation. The function will also delete the file from the **Processing** bucket and publish a message to the **Error** topic on SNS to notify users about a potentially malicious file that was uploaded.
11. If the file upload fails and the file is not fully uploaded, then Transfer Family will trigger the **Managed Workflow Partial** process. **Managed Workflow Partial** is an error handling process and calls the **Error Publisher** function, which is used to report errors that occur anywhere in the process. The **Error Publisher** function determines the type of error—whether it's due to a partial upload or another issue in the process—and sets the corresponding error status. It then publishes an error message to the **Error Topic** on SNS.
12. The **GuardDuty – Scan** task has a **timeout** to ensure that an event is published to the **Error Topic** to request manual intervention for further investigation if the file is not successfully scanned. If the **GuardDuty – Scan** task fails, the Lambda function **Error clean up** will be called.
13. Finally, there is an **S3 Lifecycle policy** attached to the **Processing** bucket. This ensures that no files are left in the **Processing** bucket for more than one day.

## Source code repository

The [AWS-samples](https://github.com/aws-samples/aws-terraform-sftp-malware-protection) repository on GitHub has a sample implementation developed with Terraform and Python-based Lambda functions to implement this solution. The same solution can also be deployed using other infrastructure as code tools or through the AWS Management Console.

## Deployment

To deploy this solution, follow these steps:

1. Clone the repository:
```bash
git clone https://github.com/aws-samples/aws-terraform-sftp-malware-protection.git
cd aws-terraform-sftp-malware-protection
```

2. Initialize Terraform:
```bash
terraform init
```

3. Plan the deployment:
```bash
terraform plan
```

4. Apply the configuration:
```bash
terraform apply
```

## Testing the solution

After deployment, you can test the solution by:

1. Connecting to the SFTP server using the credentials provided in the Terraform output
2. Uploading test files (both clean and malicious samples)
3. Monitoring the SNS topics for notifications
4. Checking the S3 buckets to see file movement based on scan results

## Cleanup

To avoid ongoing charges, remember to clean up the resources when you're done testing:

```bash
terraform destroy
```

## Conclusion

This solution provides a fully managed approach to scanning uploaded files for malware using AWS Transfer Family and Amazon GuardDuty. By leveraging GuardDuty's Malware Protection for S3, organizations can automatically scan files without managing compute resources or updating malware signatures manually. The solution is scalable, cost-effective, and integrates well with existing AWS services for comprehensive file transfer security.

## Additional resources

* [AWS Transfer Family User Guide](https://docs.aws.amazon.com/transfer/)
* [Amazon GuardDuty User Guide](https://docs.aws.amazon.com/guardduty/)
* [GuardDuty Malware Protection for S3](https://docs.aws.amazon.com/guardduty/latest/ug/malware-protection.html)
* [AWS Step Functions Developer Guide](https://docs.aws.amazon.com/step-functions/)
