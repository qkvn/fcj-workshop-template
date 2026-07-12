---
title: "Preparing IAM Role for Lambda"
date: 2026-06-21
weight: 3
chapter: false
pre: ""
---

# Step 1: Preparing IAM Role for Lambda

### Introduction

IAM Role is the permission that Lambda uses to access necessary AWS services in the workshop such as Amazon S3, Amazon SQS, Amazon Rekognition, and Amazon Textract.

In this step, you will create an IAM Role for Lambda and attach necessary policies so Lambda can read data, receive messages, and write logs during image processing.

---

### Implementation Steps

1. Access the **AWS Console**, find the **IAM** service.

![Find IAM Service](./images/image1.png)

2. Select **Roles**, then select **Create role**.

![Select Roles and Create role](./images/image2.png)

3. In the **Trusted entity type** section, select **AWS service**.

4. In the **Use case** section, select **Lambda**, then select **Next**.

![Select AWS service and Lambda](./images/image3.png)

5. Find and attach the necessary policies for Lambda in sequence.

![Find policy for Lambda](./images/image4.png)

![Select policy for Lambda](./images/image5.png)

![Attach AWSLambdaBasicExecutionRole policy](./images/image6.png)

![Attach AmazonS3ReadOnlyAccess policy](./images/image7.png)

![Attach AmazonSQSFullAccess policy](./images/image8.png)

![Attach Rekognition and Textract policy](./images/image9.png)

6. Name the role **Lambda-ImageProcessing-Role**, then select **Create role**.

![Name IAM Role](./images/image10.png)

![Create IAM Role](./images/image11.png)

---

### Security Notes

In production environments, instead of using AWS's built-in policies, you should write your own **Custom Policy** to limit permissions only to specific buckets or queues.

This is a security best practice following the **Least Privilege** principle, which means granting only necessary permissions and avoiding excessive permissions.

![Custom policy following Least Privilege principle](./images/image12.png)
