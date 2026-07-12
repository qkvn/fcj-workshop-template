---
title: "PART 3: BLOGS POSTED"
menuTitle: "Blogs posted"
date: 2026-07-24
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

This section introduces the technical blog posts that my team and I researched, authored, and shared on the AWS Study Group community.

<div style="text-align: left;">

### Blog List

1. [Blog 1 — Samsung resolves the "price mismatch" problem with AWS Lambda Response Streaming](3.1-Blog1/)  
   This blog post explains how Samsung leveraged AWS Lambda Response Streaming to completely overcome latency bottlenecks in their global e-commerce platform. By eliminating intermediate cache layers and directly querying the Pricing Engine in real time, Samsung reduced P90 latency by 98% (from 4,500 ms to 50 ms) and consolidated their infrastructure from over 100 servers down to just 5–10 Lambda functions.

2. [Blog 2 — S3 Intelligent-Tiering: When your data storage "knows" which tier to stay in](3.2-Blog2/)  
   This blog post introduces Amazon S3 Intelligent-Tiering, a storage class that automatically monitors object access patterns and moves data to the most cost-effective access tier without operational overhead or manual intervention. The mechanism features three automatic access tiers (Frequent Access, Infrequent Access, and Archive Instant Access) along with two optional archive tiers, making it an ideal cost-optimization solution for data lakes with unpredictable access patterns.

3. [Blog 3 — Ensuring fair play in gaming: Detecting and preventing profile alterations with Amazon Textract](3.3-Blog3/)  
   This blog post presents a serverless architecture using Amazon Textract to automatically detect and obscure unauthorized embedded text in gaming profile images. When a user uploads an image to Amazon S3, an AWS Lambda function automatically triggers Amazon Textract to extract text bounding boxes, blurs violating regions using Gaussian Blur, and sends alert notifications to administrators via Amazon SNS—ensuring a fair and secure gaming environment with high accuracy and zero server management overhead.

</div>
