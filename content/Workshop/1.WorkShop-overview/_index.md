---
title: "Workshop Overview"
date: 2026-06-21
weight: 2
chapter: false
pre: ""
---

# Building Asynchronous Image Processing Pipeline Using Amazon S3, SQS, and AWS Lambda

### Workshop Overview Summary

This workshop guides you through building an asynchronous image processing pipeline (Event-Driven) on AWS, solving the problem of "many users uploading images simultaneously" while ensuring system stability, scalability, and cost control.

---

### Main Objectives

- Design an **S3 → SQS → Lambda** architecture to process images in a queue, preventing Lambda from being overwhelmed when multiple uploads occur simultaneously.
- Write Lambda (Python) to:
  - Receive messages from SQS (containing events from S3)
  - Load images from S3
  - Call Amazon Rekognition to suggest labels/identify conditions (e.g., damaged packages)
  - Call Amazon Textract to extract text on labels (shipment codes, route information, phone numbers, etc.)
  - Write results to CloudWatch Logs for observation and testing

### What You Will Learn
  - Why SQS is needed as a "buffer" between S3 and Lambda in high-load systems.
  - How to configure IAM Role following the Least Privilege principle (granting only necessary permissions).
  - How to create & configure:
    - S3 bucket and Event Notification
    - SQS Standard Queue with important parameters (Visibility Timeout, retention, etc.)
    - Lambda Trigger from SQS and process each message (small batch size for easy observation)
  - End-to-end Testing & Validation procedures and cleanup resources to avoid unexpected costs.
