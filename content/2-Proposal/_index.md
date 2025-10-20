---
title: "Proposal"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

# AN OCR PLATFORM FOR MATH PROBLEM SUPPORT VIETNAMESE.

### Executive Summary

The project is an **open-source** version of Mathpix or Pix2text. That's utilize the **AWS Services** to help the user to host an OCR platform on math problems.

**Key Highlights:**

🔹 **Effortless Deployment**

Features a fully automated CI/CD pipeline using AWS CodePipeline and CodeBuild. Once configured, any code push automatically builds, tests, and deploys the entire application stack, making it incredibly simple for anyone to host and maintain their own instance.

🔹 **Cost-Effective & Self-Hosted**

Designed for self-hosting on your own AWS account, giving you full control. The architecture leverages a 'pay-as-you-go' model and is optimized to work with the AWS Free Tier, minimizing costs for individual users and small teams.

🔹 **Intelligent Semantic Search**

Go beyond simple keyword matching. Our system allows you to search through your entire history of uploaded problems based on their mathematical meaning, helping you find conceptually similar questions instantly.

🔹 **Easy Problem Organization & Retrieval**

Never lose track of your work. Assign custom tags to your problems, allowing you to easily filter, find, and revise specific topics or chapters later on. This makes it a powerful tool for studying and knowledge management.

🔹 **Modern & Powerful Tech Stack**

    Backend: Java (Amazon Corretto 21) with Spring Boot

    Frontend: React + Vite

    Cloud: AWS EC2 (t3.medium Spot Instance), RDS MySQL, KMS, CloudWatch

    DevOps: AWS CodePipeline, CodeBuild

    AI/Search: Qdrant Vector Database

### Problem Statement

**Current Challenges:**

All the current platform for OCR have high fees for each time uses or only free for about 3-4 documents, which isn't a cost efficient for students who needs to uses on their daily basics.  

As Mathpix require 4.99 USD per month with 1000 PDF files, which may be a waste since each user have different cost. And Pix2Text though advertised as free alternative, the free version lacks of saving feature for revision, and also need to "recharge".  

**Our Solutions:**

We provide a pay-as-you-go model as you only need to pay for what you uses, or handle the hosting yourself as we provided Infrastructure as Code and the code ready to be built on AWS.  

Provided a monitoring method, for users to view via CloudWatch on AWS, to know the cost for each time they uses the system.

### Solution Architecture Overview

![Architecture Overview](/images/2-Proposal/architecture.png)

```markdown
1.  **User Request:** The user accesses the frontend of the application, which is a static website hosted in an S3 bucket and served globally via CloudFront for low latency.

2.  **API Call:** The frontend sends an HTTPS request (containing the uploaded image/PDF) to the backend Java application running on an EC2 instance within a secure VPC.

3.  **AI Processing (Orchestration):** The Java application orchestrates the AI workflow.
    * **3a. OCR Request:** The application sends the image data to the **Claude Sonnet 3.7** model in AWS Bedrock. Claude analyzes the image and returns the transcribed mathematical equation as a LaTeX string.
    * **3b. Embedding Request:** The application sends the extracted LaTeX string to the **Titan Embedding V2** model. Titan converts the string into a numerical vector that captures its semantic meaning.

4.  **Data Storage & Retrieval:** The application interacts with the databases.
    * **4a. Vector Search:** The generated vector is used to query the **Qdrant Vector Database** to find the IDs of the most similar problems already in the system.
    * **4b. Data Fetching:** The application uses the IDs from Qdrant to query the **MySQL Database** on RDS to retrieve the full details (problem statement, solution, tags) for each similar problem.

5.  **Temporary Storage:** The initial image/PDF uploaded by the user is stored in a dedicated S3 bucket (`S3_Application`). This serves as a temporary record of the input.

6.  **Monitoring & Logging:** All application activities, logs, performance metrics, and costs are sent to **AWS CloudWatch**.
    * **6a. Application Logs:** The EC2 instance sends its logs to CloudWatch.
    * **6b. S3 Access Logs:** S3 access events (like `PutObject`) are logged for monitoring and auditing.

7.  **Bedrock API Calls:** This represents the underlying API calls made from the Java application to the Bedrock service to invoke the Claude and Titan models.

### Pre-Production Workflow (Developer CI/CD)

8.  **Developer Push:** An authorized **IAM User** (the developer) pushes new code to a source code repository (e.g., GitHub, AWS CodeCommit).

9.  **CI/CD Pipeline Execution:** The code push triggers the **AWS CodePipeline**.
    * **Source Stage:** Fetches the latest code.
    * **Build Stage (CodeBuild):** Compiles the Java code, runs tests, and packages the application into a `.jar` artifact. This artifact is then stored securely in an S3 bucket, encrypted using a **KMS key**.
    * **Deploy Stage:** The pipeline proceeds to the deployment stage.

10. **Automated Deployment:**
    * **AWS CloudFormation** is used to provision or update the entire infrastructure stack (VPC, EC2, RDS, etc.) based on templates. This ensures consistent and repeatable deployments.
    * The new application artifact from the S3 bucket is deployed to the EC2 instance(s).
```

### Asssumetion

Core Assumptions

Region: All pricing is based on us-east-1 (N. Virginia).

EC2 Instance: The architecture uses a t3.large Spot Instance, providing an average saving of ~70% compared to On-Demand pricing.

Database Instance: An RDS db.t3.micro is used for the MySQL database.

AWS Free Tier: Applied to the Normal User profile. The High-Usage profile assumes the 12-month Free Tier period for services like RDS has expired.

Disclaimer: This analysis is an estimate based on pricing data from October 2025. Prices for AWS services, especially Spot Instances, are subject to change.

### Combined Cost Breakdown (t3.large)

| Service | Configuration | Normal User (Monthly Cost) | High-Usage User (Monthly Cost) | Notes |
| :--- | :--- | :--- | :--- | :--- |
| **Amazon EC2** | `t3.large` Spot Instance | **~$18.22** | **~$18.22** | Core application server cost is fixed. |
| **Amazon RDS** | `db.t3.micro` (MySQL) | **$0.00** | **~$12.41** | Normal user is covered by Free Tier; High-usage assumes Free Tier expired. |
| **Amazon S3** | Standard Storage | **$0.00** | **~$0.10** | Storage/requests are minimal and mostly covered by the Free Tier. |
| **AWS Bedrock** | Claude Sonnet & Titan | **~$0.18** | **~$5.27** | This cost scales directly with the number of pages processed. |
| **CodePipeline/Build**| CI/CD Pipeline | **$0.00** | **~$0.25** | High-usage user exceeds the free monthly build minutes. |
| **Data & Monitoring** | CloudWatch & Data Transfer | **$0.00** | **~$0.50** | A nominal fee for increased logging and data egress. |
| **---** | **---** | **---** | **---** | **---** |
| **Total Estimated Cost** | | **~$18.40 / month** | **~$36.75 / month** | |

### Combined Cost Breakdown (t3.medium)

| Service                  | Configuration                | Normal User (Monthly Cost) | High-Usage User (Monthly Cost) | Notes                                                                      |
| :----------------------- | :--------------------------- | :------------------------- | :----------------------------- | :------------------------------------------------------------------------- |
| **Amazon EC2** | `t3.medium` Spot Instance    | **~$9.11** | **~$9.11** | Core application server cost is fixed and significantly lower than `t3.large`. |
| **Amazon RDS** | `db.t3.micro` (MySQL)        | **$0.00** | **~$12.41** | Normal user is covered by Free Tier; High-usage assumes Free Tier expired. |
| **Amazon S3** | Standard Storage             | **$0.00** | **~$0.10** | Storage/requests are minimal and mostly covered by the Free Tier.          |
| **AWS Bedrock** | Claude Sonnet & Titan        | **~$0.18** | **~$5.27** | This cost scales directly with the number of pages processed.              |
| **CodePipeline/Build** | CI/CD Pipeline               | **$0.00** | **~$0.25** | High-usage user exceeds the free monthly build minutes.                    |
| **Data & Monitoring** | CloudWatch & Data Transfer   | **$0.00** | **~$0.50** | A nominal fee for increased logging and data egress.                       |
| **---** | **---** | **---** | **---** | **---** |
| **Total Estimated Cost** |                              | **~$9.29 / month** | **~$27.64 / month** |                                                                            |

### Conclusion

By switching to a `t3.medium` Spot Instance, the architecture becomes exceptionally affordable.

* For a **Normal User**, the monthly cost is now **under $10**, making it almost negligible.
* For a **High-Usage User**, the cost is reduced to **well under $30 per month**, a significant saving from the previous estimate.

This change proves that the application can be run with a very minimal financial commitment, maximizing its accessibility.

### Risk Assessment

| Risk Category | Risk Description | Likelihood | Impact | Mitigation Strategy |
| :--- | :--- | :--- | :--- | :--- |
| **1. Technical & Operational** | **EC2 Spot Instance Interruption:** The core application server (EC2 Spot Instance) is terminated by AWS due to capacity demands, causing service downtime. | **Medium** | **High** | **- Automated Recovery:** Implement a stateless application design so a new instance can launch and take over seamlessly. <br> **- Diversified Instance Types:** Configure the Spot Fleet/ASG to request multiple instance types (`t3.medium`, `t3a.medium`, `t4g.medium`) to increase the chances of getting an instance. <br> **- Graceful Shutdown:** Implement logic to handle the two-minute interruption notice to save any in-progress work. |
| | **OCR/Embedding Inaccuracy:** The AWS Bedrock models (Claude, Titan) fail to accurately transcribe or understand complex, handwritten, or poorly formatted equations, leading to incorrect results. | **Medium** | **Medium** | **- Model Selection:** Continuously evaluate and choose the best-performing model versions for the task. <br> **- User Feedback Loop:** Implement a feature allowing users to correct transcriptions, which can be used to fine-tune models or improve pre-processing. <br> **- Image Pre-processing:** Add steps to automatically clean up, sharpen, and standardize images before sending them to the OCR model to improve accuracy. |
| | **Database Performance Bottleneck:** As the number of saved problems grows, the MySQL database or the Qdrant vector database slows down, impacting search and retrieval times. | **Low** | **Medium** | **- Database Indexing:** Ensure all frequently queried columns in MySQL are properly indexed. <br> **- Qdrant Optimization:** Follow Qdrant best practices for indexing and segment configuration as the dataset grows. <br> **- Caching:** Implement a caching layer (e.g., Redis or an in-memory cache) for frequently accessed problems. |
| **2. Cost & Financial** | **Unexpected Cost Spikes:** A bug in the application or unintended high usage leads to a massive number of calls to AWS Bedrock, resulting in a significantly higher-than-expected monthly bill. | **Low** | **High** | **- AWS Budgets & Alarms:** Set up strict AWS Budgets to send alerts when costs exceed predefined thresholds (e.g., $50, $100). <br> **- Application-Level Rate Limiting:** Implement logic in the backend to limit the number of documents a single user or IP can process per hour/day to prevent abuse. <br> **- Cost Monitoring:** Regularly review AWS Cost Explorer to understand spending patterns. |
| | **Spot Instance Price Volatility:** The market price for `t3.medium` Spot Instances increases significantly, raising the fixed operational cost. | **Low** | **Low** | **- Diversified Instance Selection:** As mentioned above, allowing the system to choose from multiple instance types helps find the cheapest available option. <br> **- Fallback to On-Demand (Manual):** For a mission-critical deployment, have a manual plan to switch to an On-Demand instance if Spot prices become unstable for a prolonged period. |
| **3. Security** | **Insecure IAM Role Configuration:** The IAM roles for the EC2 instance or CodePipeline are overly permissive, granting excessive access to AWS resources (e.g., full S3 access instead of just to a specific bucket). | **Medium** | **High** | **- Principle of Least Privilege:** Strictly define IAM policies to grant only the minimum required permissions. For example, the EC2 role should only have `PutObject` and `GetObject` access to the `S3_Application` bucket. <br> **- Regular Audits:** Use AWS IAM Access Analyzer to regularly review and identify overly permissive roles. |
| | **Vulnerabilities in Dependencies:** The open-source libraries used in the Java backend (Spring Boot) or React frontend have known security vulnerabilities (e.g., Log4Shell, XSS) that could be exploited. | **High** | **High** | **- Automated Dependency Scanning:** Integrate tools like GitHub Dependabot, `npm audit`, or Snyk directly into the CI/CD pipeline to automatically scan for and flag vulnerable dependencies. <br> **- Regular Patching:** Establish a routine to update dependencies to their latest stable versions. |
| **4. Project & Usability**| **Complex User Setup:** Non-technical users find the process of setting up an AWS account, configuring IAM roles, and deploying the application too complicated, hindering adoption. | **High**| **Medium**| **- Comprehensive Documentation:** Create clear, step-by-step guides with screenshots and videos. <br> **- "One-Click" Deployment:** Provide a CloudFormation or Terraform template that automates the entire infrastructure setup process with a single command or click. <br> **- Community Support:** Set up a Discord server or GitHub Discussions board for users to ask questions and help each other. |
| **5. Technical & Performance** | **Slow PDF Processing Time:** PDFs are extremely slow to process. This is caused by the need to **rasterize** each page (PDF -> Image) and then use a slow, expensive **vision OCR model** on that image, leading to a poor user experience. | **High** | **Medium** | **- Hybrid Processing:** Implement a "smart" pre-processor. First, try to extract *digital text* directly from the PDF (using a library like PDFBox). <br> **- If digital text exists:** Send this *text* (not an image) to the model. This is much faster and cheaper. <br> **- If digital text fails (scanned PDF):** Only then fall back to the slow image rasterization and vision OCR path. <br> **- Parallelization:** For multi-page PDFs, process all pages asynchronously in parallel, not one by one. <br> **- UX Management:** For the slow path, provide a clear progress bar and estimated wait time. |

### Outcomes

#### Technical Outcomes

Upon completion, the project will deliver the following tangible technical assets:

1.  **A Fully Functional, Self-Hostable Web Application:**
    * A live, deployed web application accessible via a public URL.
    * The application will successfully accept image (`.jpg`, `.png`) and PDF uploads, perform OCR on mathematical equations, and return a list of semantically similar problems from its database.
    * The entire stack will be designed for easy self-hosting by any user with an AWS account.

2.  **An Automated & Secure CI/CD Pipeline:**
    * A production-ready CI/CD pipeline using AWS CodePipeline and CodeBuild.
    * This pipeline will automatically build, test, and deploy any new code pushed to the main branch, ensuring reliability and simplifying maintenance.
    * The pipeline will be defined as code (CloudFormation), making it reusable and easy to set up.

3.  **A Scalable and Cost-Optimized Cloud Architecture:**
    * A well-architected cloud environment on AWS that is secure, scalable, and highly cost-effective.
    * Demonstrated successful use of EC2 Spot Instances to reduce compute costs by over 70%.
    * Effective integration of managed services including RDS for the relational database, S3 for storage, and CloudWatch for monitoring.

4.  **An Open-Source Codebase for Community Contribution:**
    * A clean, well-documented, and professional open-source project hosted on GitHub.
    * The repository will include a `README.md` with clear deployment instructions, architectural diagrams, and a guide for future contributors.

#### Learning Outcomes

This project will provide deep, hands-on experience across several critical domains in modern software engineering:

1.  **Deepened Expertise in Cloud Engineering (AWS):**
    * Proficiency in designing, deploying, and managing a complete cloud application from the ground up.
    * Practical skills in configuring core AWS services, including VPC networking, IAM security roles, EC2, S3, RDS, and CloudWatch.

2.  **Practical Experience with AI/ML Model Integration:**
    * Hands-on experience integrating and orchestrating multiple large language and embedding models (AWS Bedrock) within a traditional backend application.
    * Understanding the performance trade-offs and cost implications of using different AI models for tasks like OCR and semantic search.

3.  **Advanced DevOps and Automation Skills:**
    * Mastery of building a CI/CD pipeline from scratch, including writing build specifications, managing deployment artifacts, and automating infrastructure provisioning using CloudFormation.
    * Experience in implementing best practices for security and monitoring within an automated deployment workflow.

4.  **Full-Stack Development Proficiency:**
    * End-to-end development experience across the entire technology stack: building a modern frontend with React, developing a robust backend with Java (Spring Boot), and managing both relational (MySQL) and vector (Qdrant) databases.

5.  **Project Management and Architectural Design:**
    * Demonstrated ability to take a complex project from concept to completion. This includes performing requirements analysis, designing a system architecture, creating a dataflow, conducting cost analysis, assessing risks, and delivering a final, polished product.

### Portfolio Value

#### AI-Powered Cloud Architecture

* **Advanced AI Model Integration:** Demonstrates the ability to orchestrate multiple AI services (AWS Bedrock) for a complex workflow, combining a vision-based OCR model (Claude) with a semantic embedding model (Titan).
* **Specialized Search Implementation:** Utilizes a dedicated vector database (Qdrant) to implement high-speed, accurate semantic search, a core component of modern AI applications.
* **Scalable, Decoupled Design:** Features a robust cloud-native architecture using AWS services, designed to handle processing-intensive tasks efficiently.

#### DevSecOps & Automation

* **Automated CI/CD Pipeline:** Implements a full CI/CD pipeline with AWS CodePipeline and CodeBuild to automatically build, test, and deploy the application, ensuring consistency and reliability.
* **Infrastructure as Code (IaC):** The entire AWS infrastructure is defined in CloudFormation, enabling automated, repeatable, and secure deployments for any user.
* **Comprehensive Monitoring:** Leverages AWS CloudWatch for detailed logging and monitoring of all application and infrastructure components.
* **Built-in Security:** Uses AWS KMS for encrypting build artifacts and enforces the principle of least privilege with finely-tuned IAM roles.

#### AWS Proficiency & Cost Optimization

* **Deep AWS Integration:** Integrates over 10 core AWS services, including EC2, S3, RDS, Bedrock, CodePipeline, CloudWatch, KMS, and IAM, demonstrating a broad and practical knowledge of the AWS ecosystem.
* **Extreme Cost-Efficiency:** The architecture is meticulously designed for minimal cost, leveraging **EC2 Spot Instances** to reduce compute costs by over 70%.
* **Free Tier Maximization:** The design is optimized to utilize the AWS Free Tier, bringing the monthly cost for normal usage to **under $10**.

#### Key Differentiators

* **Production-Grade AI Workflow:** This isn't just a simple API call; it's a multi-step AI chain that mimics real-world production systems for OCR and semantic analysis.
* **Focus on Cost-Effective Self-Hosting:** A key feature is the ability for anyone to deploy a powerful, personal AI tool on their own cloud account for a fraction of the cost of commercial services.
* **Reusable Deployment Patterns:** The project's "one-click" CloudFormation deployment demonstrates a high level of DevOps maturity and makes the technology accessible to a wider audience.
* **Efficient Hybrid Data Storage:** Successfully combines a traditional relational database (MySQL) for structured data with a modern vector database (Qdrant) for high-performance AI search.


### Conclusion
The project is an **open-source, self-hostable AI-powered tool** that demonstrates excellence in modern cloud engineering and DevOps, showcasing:

#### **AI-Powered Cloud Architecture**

Advanced AI model orchestration (AWS Bedrock: Claude & Titan)

High-performance semantic search with a vector database (Qdrant)

Scalable, stateless application design for high availability

Efficient hybrid data storage (MySQL + Vector DB)

#### **DevSecOps Implementation**

Fully automated CI/CD pipeline (CodePipeline + CodeBuild)

Infrastructure as Code for "one-click" deployment (CloudFormation)

KMS encryption for all build artifacts and sensitive data

Comprehensive monitoring and logging (CloudWatch)

Security-first design with least-privilege IAM roles

#### **Cost-Optimized Design**

Extreme cost-efficiency (under $10/month for normal usage)

EC2 Spot Instances for over 70% reduction in compute costs

Maximized use of the AWS Free Tier

#### **AWS Expertise**

Deep, practical integration with 10+ core AWS services

Best practices in cloud security and cost management

Robust, scalable, and resilient architecture

**Timeline**: 2 months | Team: 3 people | Budget: ~$9-10/month (Normal Usage)

**This project demonstrates the end-to-end implementation of a production-grade AI application on a secure, cost-effective, and fully automated cloud infrastructure, making it a strong portfolio piece for Cloud Engineering, AI/ML Engineering, and DevOps roles.**

## Appendices

A. Github Repo: https://github.com/tiozo/FCJ-Problem_Suggest

B. Contacts info: 
- Project Lead: Vo Minh Thuan
- Email: ```azinz850@gmail.com```
- WhatsApp/Zalo: ```0908517568```