---
title: "Creating Amazon SQS Message Queue"
date: 2026-06-21
weight: 4
chapter: false
pre: ""
---

# Step 2: Creating Amazon SQS Message Queue

### Introduction

Amazon SQS acts as an intermediate queue between Amazon S3 and AWS Lambda.

When users upload images to S3, S3 sends a notification to SQS. Lambda reads messages from SQS and processes them gradually, helping the system remain stable when many images are uploaded simultaneously.

---

### Implementation Steps

1. Access the **AWS Console**, find the **Amazon SQS** service, then select **Create queue**.

![Find Amazon SQS](./images/image13.png)

2. Select queue type as **Standard Queue**.

Don't select **FIFO Queue** in this workshop because the goal is to process images asynchronously with high scalability, without requiring strict message ordering.

![Select Standard Queue](./images/image14.png)

3. Name the queue **image-processing-queue**.

![Name SQS queue](./images/image15.png)

4. Configure important parameters for the queue.

![Configure SQS parameters](./images/image16.png)

![Check SQS configuration](./images/image17.png)

5. Select **Create queue** to create the queue.

6. After creation, copy the **ARN** of the queue. This ARN will be used in the S3 Event Notification configuration step.

![Copy ARN of SQS queue](./images/image18.png)
