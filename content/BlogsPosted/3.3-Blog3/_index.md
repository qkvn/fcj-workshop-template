---
title: "Blog 3"
date: 2026-07-11
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# [AWS GAMETECH] ENSURING FAIR PLAY IN GAMING: DETECTING AND PREVENTING PROFILE ALTERATIONS WITH AMAZON TEXTRACT

In multiplayer online games, maintaining a fair and safe gaming environment is a top priority. While traditional security measures focus on code-level anti-cheat systems, subtle exploits like **profile picture abuse** are frequently overlooked. Bad actors often embed phone numbers, Discord links, or private chat room codes directly into their avatars to communicate covertly, coordinate cheating, or lure players away from the platform. To resolve this challenge automatically, AWS introduced a **Serverless architecture** using **Amazon Textract** to scan, detect, and redact text on images immediately upon upload.

### The Problem: Limitations of Traditional Approaches

* Cheating players often embed phone numbers, Discord invites, or private room codes directly into avatar images to communicate covertly or bypass game chat filters.
* Traditional approach: Deploying dedicated EC2 servers running heavy image processing libraries (like OpenCV) or self-trained OCR models.
* Three major drawbacks of traditional approaches:
  * **High Maintenance Costs**: Requires maintaining 24/7 server infrastructure even during low-upload intervals.
  * **Performance Bottlenecks**: Systems struggle to scale smoothly during sudden influxes of new user registrations or concurrent profile updates.
  * **Limited Accuracy**: Poor OCR accuracy when dealing with handwritten characters, stylized gaming fonts, or distorted text backgrounds.

### A New Approach: Automated Serverless Architecture with Amazon Textract

* Instead of managing complex custom OCR servers, the system leverages a **Serverless** model combined with managed AI services (**Amazon Textract**) for automated event-driven image moderation.
* Workflow:
  1. **Image Upload**: A user uploads a profile picture to the primary Amazon S3 bucket, immediately triggering an AWS Lambda function.
  2. **Text Detection & Coordinate Extraction**: The Lambda function invokes **Amazon Textract**. This AI service analyzes the image, detects printed and handwritten text, and returns precise bounding box coordinates of text areas.
  3. **Automated Blurring**: Lambda processes the bounding box coordinates and applies a Gaussian Blur filter to accurately obscure areas containing sensitive text.
  4. **Secure Storage**: The processed image is temporarily saved to an S3 processing bucket (to prevent infinite event notification loops), then overwrites the original avatar in the primary bucket.
  5. **Admin Alerting**: **Amazon SNS** sends an email notification to the Game Admin team detailing which user account attempted to embed unauthorized text in their profile picture.

### Key Technical Implementation Highlights

* **Event-Driven Architecture**: S3 Event Notifications trigger Lambda execution only when an image upload occurs, eliminating idle infrastructure costs.
* **High-Accuracy Managed AI**: Amazon Textract provides robust text detection across diverse languages, stylized fonts, and handwriting styles.
* **Non-Destructive Content Moderation**: Gaussian blur is applied specifically over offending text regions while preserving the visual quality of the avatar artwork.
* **Automatic Scalability**: Lambda and Textract automatically scale to meet peak traffic demands without manual provisioning.
* **Pay-Per-Use Cost Efficiency**: Costs scale strictly with actual millisecond compute usage and images processed.

### Lessons Learned

* Rather than building and maintaining custom OCR pipelines from scratch, leveraging cloud-native **Managed AI Services** reduces engineering overhead and achieves higher accuracy.
* Combining **Serverless and Event-Driven patterns** provides an optimal solution for user-generated content (UGC) moderation, delivering low latency, automated enforcement, and cost-effective scaling.

### Architecture Diagram

![Automated text detection and redaction architecture using Amazon Textract](/BlogsPosted/image/blog3.jpg)

### References

- **Post on AWS Study Group FCJ Community:** [View post on Facebook]()
- **Original AWS GameTech Blog Post:** [Ensuring fair play by detecting and preventing profile alterations with Amazon Textract](https://aws.amazon.com/blogs/gametech/ensuring-fair-play-by-detecting-and-preventing-profile-alterations-with-amazon-textract/)
