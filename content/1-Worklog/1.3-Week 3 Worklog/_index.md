---
title: "Week 3 worklog"
weight: 3
chapter: false
pre: "<b> 1.3 </b>"
---

## Week 3 Objectives

- Start working on project.
- Finish the received task, and publish it to github.
- Covert the project from Java backend toward Serverless. 
- Connect to bedrock API (LLama Maverick 17B), initialize an a good respond.
- Start using the basic system, for semi-automated tagging system for each problems.

## Tasks to be carried out this week

| Day | Tasks | Start Dat | End Date | References |
|---|---|---|---|---|
| 1 | - Finalized the task for first iteration. Focus on creating semi-automated tagging system.<br>- Learn n8n, for tagging automation with Bedrocks API. | 22/9/2025 | 22/9/2025 |  |
| 2 | - Test out n8n connection with AWS S3, Bedrock API for converting Vietnamese math problems to Markdown language (Mathjax).<br>- Finish the legacy backup task (PDF/image endpoint receiver using Java with SDK Amazon Corretto). <br>- Create a Cloudwatch widget, for cost monitoring using non-root user. (From event FCJ-left over document)<br>- Established a file transfer from local backend (test) to AWS S3.  | 23/9/2025 | 23/9/2025 | - https://saviorab1.github.io/fcj-leftover/11-monitoring-with-cloudwatch/11.4-create-cost-calculation-widget/<br>- https://programming.am/monitoring-s3-buckets-using-amazon-cloudwatch-6546a01fb763 |
| 3 | - Test out Metrics from AWS S3, Monitored BucketByteSize, AllRequest for cost manage with S3 (0.3 USD per 10000 metrics, 1 metric per minute if something occur within)<br>- Study vector database (Qdrant) <br>- Finish out project task (Slicing transcripted markdown to each statement with answer saved in Database.)  | 24/9/2025 | 24/9/2025 | - https://docs.aws.amazon.com/AmazonS3/latest/userguide/configure-request-metrics-bucket.html<br>- https://programming.am/monitoring-s3-buckets-using-amazon-cloudwatch-6546a01fb763<br>- https://aws.amazon.com/cloudwatch/pricing/ |
| 4 | - Study how to intergrate Bedrock with Java backend (Hosted on legacy server at home).<br>- Hands on practice, and trails with task PDF to Markdown. (Take Object from S3 input/* to Bedrock, take respond prompt -> <Date>.md -> to S3 output/).<br>- Setup an IAM with Principle of Least Priveleges. | 25/9/2025 | 25/9/2025 | - https://aws.amazon.com/vi/awstv/watch/9c98d05f7e0/<br>- https://docs.aws.amazon.com/wellarchitected/latest/framework/sec_permissions_least_privileges.html |
| 5 | - Fixed output lost due to multiple files process per day. <br>- Tested function and push to github. Understand what is inference model when calling from Java. | 26/9/2025 | 28/9/2025 | - https://github.com/tiozo/FCJ-Problem_Suggest |