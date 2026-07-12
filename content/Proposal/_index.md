---
title: "Proposal"
date: 2026-06-21
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Automated solution for monitoring the quality of goods based on Serverless and AI/ML

### Executive Summary

In the Logistics sector, businesses are under significant pressure in terms of processing time, data accuracy, and the ability to control goods losses. The current monitoring of goods quality in warehouses still largely depends on human labor, leading to slow issue handling, fragmented data, and difficulty updating in real time.

The proposed team develops the system **"Automated Solution for Monitoring Goods Quality Based on Serverless Architecture and Artificial Intelligence (AI/ML)"**. This is a software system that uses Amazon Web Services (AWS) services, allowing delivery staff or customers to take photos of damaged packages such as torn, broken, dented, or otherwise damaged items, and then upload the images directly to the system for automatic analysis.

The system uses AI/ML to identify the extent of damage to goods, extract shipment information, store evidence, and send alerts to relevant departments. As a result, the incident handling process can be reduced from several hours or days to just a few seconds, while helping the business reduce operating costs, minimize manual errors, and improve service quality.

---

### Problem Statement

#### Current Issues

Based on real-world observations, the recording and handling of damaged goods currently face several weaknesses in the operational process:

- **Manual, time-consuming, and error-prone process:** When damage is detected, employees often have to take photos with their personal phones, send them through chat groups, or create paper forms. The assessment team then has to consolidate this scattered information to re-enter it into the ERP system and process refund procedures for customers. This process takes a lot of time and easily causes data confusion.

- **Lack of automation and immediate alerts:** Since there is no software that automatically analyzes images and reads order information, warehouse managers often do not know immediately the extent of damage to goods in order to make timely handling decisions. Slow responses negatively affect customer experience and service reputation.

- **Lack of system scalability:** During peak seasons, the volume of goods arriving at warehouses increases sharply. Manual inspection processes cannot handle many orders in parallel, leading to overload, missed damaged goods, or allowing defective products to continue reaching end consumers.

- **Fragmented and hard-to-retrieve data storage:** Existing incident records may be scattered across notebooks, Excel files, or chat groups. When needing to calculate loss rates by month/quarter for reporting to senior management, staff must review them manually, which is time-consuming and prone to errors.

- **High operating costs:** The business must maintain specialized personnel at each warehouse location to inspect, enter data, and handle incidents. In addition to labor costs, information errors and prolonged compensation disputes also reduce profitability and the reputation of the Logistics business.

---

### Proposed Solution

The team proposes building an automated system for monitoring goods quality based on Serverless architecture and AI/ML. The system focuses on the following core capabilities:

- **AI-powered automation:** The system uses Amazon Rekognition to analyze images of goods and detect signs of damage. The analysis results may include status labels, damage severity, and confidence scores. Amazon Textract is used to extract shipment codes, addresses, or related text information from images without manual entry.

- **Real-time asynchronous processing:** Images uploaded to Amazon S3 will trigger an automated processing flow. Image data, shipping codes, and analysis results are synchronized to Amazon DynamoDB for lookup, statistics, and report generation. This processing method helps shorten the time for recording and response from several days to just a few seconds.

- **Automatically scalable Serverless infrastructure:** The system runs on AWS Serverless services, which can automatically scale when the number of images spikes during peak seasons. The business does not need to manually manage servers while still ensuring the ability to process many requests simultaneously.

- **Centralized and transparent data management:** All scan history, evidence images, shipment codes, and processing statuses are stored centrally. Managers can look up history at any time, check evidence, and export statistical reports on damaged goods.

- **Optimization of operating costs:** Integrating AI into the process helps reduce errors compared to manual data entry, reduces the need for specialized staff at each warehouse, shortens dispute resolution time, and improves reliability in compensation handling.

---

### Solution Architecture

#### Overall Architecture
![Image 1](/content/Proposal/image/DiagramStructure.png)

The system is designed using an event-driven architecture model, using AWS services to automatically scale according to actual traffic. The entire lifecycle of a goods quality monitoring request is divided into 4 main technology layers:

| Architecture Layer | Role | Proposed AWS Services |
| --- | --- | --- |
| Frontend & Auth | Provides the web interface, manages login/registration, issues JWT tokens, and controls API access | AWS Amplify, Amazon Cognito, Amazon API Gateway |
| Storage | Receives and stores images of damaged goods uploaded by users | Amazon S3 |
| Backend & AI/ML | Handles image upload events, analyzes images, extracts shipment information, evaluates damage levels, and updates data | Amazon SQS, AWS Lambda, Amazon Rekognition, Amazon Textract, Amazon DynamoDB |
| Monitoring & Security | Monitors system activity, manages logs, and enforces access control following the least privilege principle | Amazon CloudWatch, AWS IAM |

#### Main Processing Flow

| Step | Description |
| --- | --- |
| 1 | The user logs into the system through the web interface deployed using AWS Amplify and authenticated by Amazon Cognito. |
| 2 | The user uploads an image of a damaged package to the system. The image is stored in the Amazon S3 Raw Image Bucket. |
| 3 | An S3 Event Notification is triggered after the image is successfully uploaded and sends the processing information to Amazon SQS. |
| 4 | The Lambda orchestrator-function reads the event from SQS, then calls Amazon Rekognition to detect damage and Amazon Textract to read the shipment code/text information. |
| 5 | The Lambda evaluator-function evaluates the damage level based on AI/ML results and determines the processing status. |
| 6 | The Lambda processor-function saves the results to Amazon DynamoDB, updates the record status, and triggers alerts if needed. |
| 7 | Amazon SNS sends Email/SMS notifications to warehouse managers or the handling team when a serious incident is detected. |
| 8 | Amazon CloudWatch records logs, metrics, and supports error monitoring throughout the operation. |

---

### AWS Services Used

#### Frontend & Auth

- **AWS Amplify:** Hosts and distributes the web interface for users.
- **Amazon Cognito:** Manages login, registration, and JWT token issuance.
- **Amazon API Gateway:** Receives API requests, validates tokens, and forwards requests to the backend.

#### Storage

- **Amazon S3:** Stores original images of goods, acting as the entry point for incoming data.

#### Backend Serverless & AI/ML

- **Amazon SQS:** Acts as an intermediate queue, helping the system process asynchronously and avoid overload when many images are uploaded simultaneously.
- **AWS Lambda orchestrator-function:** Reads events from SQS and orchestrates AI/ML analysis tasks.
- **Amazon Rekognition:** Analyzes images to detect signs of damage in packages.
- **Amazon Textract:** Extracts text, shipment codes, or delivery information from images.
- **AWS Lambda evaluator-function:** Evaluates the damage level and classifies the processing status.
- **AWS Lambda processor-function:** Saves results, updates records, and triggers subsequent processing steps.
- **Amazon DynamoDB:** Stores analysis results, processing history, order status, and data for reporting.
- **Amazon SNS:** Sends Email/SMS alerts to relevant departments.

#### Monitoring & Security

- **Amazon CloudWatch:** Monitors logs, metrics, error alerts, and system performance.
- **AWS IAM:** Manages access permissions according to the least privilege principle, ensuring each service only has the permissions it needs to operate.

---

### Risk Assessment

| Risk | Impact | Mitigation Approach |
| --- | --- | --- |
| Poor input image quality | AI may misidentify or fail to extract shipment codes | Instruct users to take clear, well-lit photos; check image format and size before upload |
| AI/ML results are not fully accurate in some cases | May lead to incorrect damage severity classification | Allow managers to confirm manually in cases with low confidence |
| High traffic during peak seasons | The system may experience delays if queues are not well controlled | Use SQS to regulate load, Lambda to scale automatically, and CloudWatch to monitor backlog |
| Leakage of image data or shipment information | Affects customer data security | Apply Cognito, IAM least privilege, S3 access restrictions, and encrypt data at rest |
| Costs increase if a large number of images are processed | Affects operating budget | Monitor usage with CloudWatch, set cost alerts, and optimize image size before analysis |

---

### Expected Results

- Shorten the time to record and handle goods incidents from several hours or days to just a few seconds.
- Automatically identify signs of damage using AI, reducing reliance on manual inspection.
- Automatically extract shipment codes and related information, minimizing data entry errors.
- Centralize incident data, evidence images, and processing status in a single system.
- Help warehouse managers look up history, calculate loss rates, and export reports quickly.
- Increase scalability during peak seasons through Serverless architecture.
- Reduce operating personnel costs, reduce compensation disputes, and improve the reputation of the Logistics service.

---

### Conclusion

The automated solution for monitoring goods quality based on Serverless and AI/ML helps Logistics companies transform incident handling processes from manual to automated, centralized, and scalable. The combination of Amazon S3, SQS, Lambda, Rekognition, Textract, DynamoDB, SNS, CloudWatch, and IAM creates an architecture suitable for real-time image processing tasks while optimizing operating costs and improving customer service quality.
