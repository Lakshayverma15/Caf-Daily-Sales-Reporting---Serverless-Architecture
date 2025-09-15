# Cafe-Daily-Sales-Reporting---Serverless-Architecture
Café Daily Sales Reporting - Serverless Architecture

## Overview
This project implements a **serverless solution** to generate **daily sales reports** for a café using **AWS Lambda, Amazon RDS, SNS, and EventBridge**.  

The system automates the reporting process, sending daily sales summaries via email to café owners, **reducing costs and improving performance** by eliminating resource-intensive cron jobs on EC2 instances.  

**Key Features:**
- **Serverless reporting:** No dedicated EC2 instance required.
- **Secure database access:** Lambda inside a VPC connects to RDS securely.
- **Automated email delivery:** Reports sent via Amazon SNS to multiple subscribers.
- **Daily scheduling:** EventBridge triggers the Lambda function daily.
- **Decoupled architecture:** Separate Lambdas for data extraction and report generation.

- 
- **DataExtractor Lambda:** Extracts sales data from RDS in a private VPC subnet.  
- **SalesAnalysisReport Lambda:** Formats the report and publishes it to SNS.  
- **SNS Topic:** Handles email delivery to subscribers.  
- **EventBridge:** Automates daily execution of the reporting Lambda.

---

## Daily Report Example

**Subject:** Daily Café Sales Report – 15 Sep 2025  

Product | Units Sold | Revenue
Espresso | 120 | $360
Latte | 90 | $270
Cappuccino | 75 | $225
Mocha | 60 | $180
Croissant | 50 | $100
Muffin | 40 | $80

Total Units Sold: 435
Total Revenue: $1,215

Top Selling Product: Espresso
Lowest Selling Product: Muffin

Notes:

Latte promotion increased sales by 20% compared to yesterday.

Inventory for Croissants may need replenishment tomorrow.

---

## AWS Services Used
- **AWS Lambda:** Executes code for data extraction and report generation.  
- **Amazon RDS:** Stores the café’s sales data.  
- **Amazon SNS:** Sends reports to email subscribers.  
- **Amazon EventBridge:** Schedules daily Lambda execution.  
- **AWS Secrets Manager:** Stores and retrieves database credentials securely.  
- **VPC & Security Groups:** Secure access from Lambda to RDS.

---

## Setup Instructions

### 1. Configure Database Access
- Place your RDS instance in a **private subnet**.  
- Create a **security group** for Lambda (`LambdaSG`) and allow inbound MySQL traffic from it.  

### 2. Create DataExtractor Lambda
- Name: `salesAnalysisReportDataExtractor`  
- Runtime: Python 3.11  
- Role: `salesAnalysisReportDERole`  
- VPC: Private subnets  
- Security Group: LambdaSG  
- Upload `salesAnalysisReportDataExtractor.zip`  
- Memory: 128 MB, Timeout: 30 sec  

### 3. Create Sales Analysis Report Lambda
- Name: `salesAnalysisReport`  
- Runtime: Python 3.11  
- Role: `salesAnalysisReportRole`  
- Upload `salesAnalysisReport.zip`  
- Memory: 128 MB, Timeout: 30 sec  
- Add environment variable `topicARN` (SNS topic ARN)  

### 4. Create SNS Topic
- Name: `SalesReportTopic`  
- Subscribe email addresses (e.g., café owners) and confirm subscriptions.  

### 5. Configure EventBridge Rule
- Schedule the `salesAnalysisReport` Lambda **daily** using cron expression in UTC.  
- Use existing role: `mySchedulerRole`.

---

## How It Works
1. **EventBridge** triggers `salesAnalysisReport` Lambda daily.  
2. Lambda invokes `salesAnalysisReportDataExtractor` to fetch sales data from RDS.  
3. Report Lambda formats the data into a readable report.  
4. Report is published to **SNS**, delivering emails to subscribers.  

---

## Troubleshooting
- **No email received:** Check SNS subscription confirmation and CloudWatch logs.  
- **Lambda timeout:** Increase Lambda timeout if data extraction takes longer than 30s.  
- **Database connection errors:** Verify security group rules and VPC configuration.

---

## Authors
- **Sofía (Role in lab):** Implemented the serverless architecture and automated reporting.  
- **Nikhil & Olivia:** Supported architecture review and optimization.  

---

## License
This project is for educational purposes and demonstration of **AWS Serverless solutions**.  
