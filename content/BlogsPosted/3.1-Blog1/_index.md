---
title: "Blog 1"
date: 2026-07-08
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# HOW SAMSUNG ACHIEVED REAL-TIME PRICING WITH AWS LAMBDA RESPONSE STREAMING

Operating a large-scale E-commerce platform comes with one of the toughest challenges: calculating and displaying real-time pricing for each user without degrading page loading speed. Samsung leveraged **AWS Lambda Response Streaming** to completely resolve this latency challenge for their global e-commerce system.

### The Problem: Discrepancies Between Displayed Prices and Checkout Prices

* Samsung.com sells a vast range of products with countless combinations (variants, promotions, regional pricing), making price calculation highly complex.
* Previous approach: Used hourly cron jobs to precompute prices for all product combinations and stored them in cache layers.
* Two major drawbacks of the legacy approach:
  * **Combination Explosion**: The number of precomputed records surged drastically even though most combinations were rarely viewed, wasting significant storage and compute resources.
  * **Synchronization Latency**: Cached prices failed to update immediately during flash sales, causing price discrepancies between browsing and checkout ("cart shock") and eroding customer trust.
* Root cause: Any intermediate caching layer will eventually drift from the authoritative source of truth over time.

### A New Approach: Eliminate Intermediate Caching and Query the Data Source Directly

* Samsung built a **Bulk Arbitration Engine** — a stateless orchestration layer that queries the Pricing Engine directly in real time instead of checking stale caches.
* Operational workflow:
  1. The browser sends a single request asking for pricing data on approximately 30 SKUs.
  2. An AWS Lambda function splits the request into 30 parallel calls to the authoritative Pricing Engine.
  3. As each SKU result returns, Lambda immediately **streams** that data back to the browser without waiting for all 30 calls to finish.
* Core technology: **AWS Lambda Response Streaming** combined with **Amazon CloudFront** pointing directly to Lambda, caching static elements at edge locations closest to end users.
* Note: The production architecture bypasses Amazon API Gateway — CloudFront connects directly to Lambda as an origin.

### Technical Solution: Stream Data Instead of "All-or-Nothing" Responses

* The Lambda handler is wrapped with `awslambda.streamifyResponse()`, enabling it to flush data chunks immediately upon availability rather than buffering the entire payload.
* 30 parallel requests are sent to the Pricing Engine; as soon as an SKU's price arrives, it is written immediately into the response stream formatted as **NDJSON** (Newline Delimited JSON, one JSON object per line).
* Data is compressed using **GZIP at maximum speed priority (Z_BEST_SPEED)** before transmission, significantly reducing bandwidth footprint with negligible CPU overhead.
* Consequently, **Time to First Byte (TTFB)** dropped sharply, allowing end users to see prices appear almost instantly.

### Notable Implementation Techniques

* **Packing Requests into GET Instead of POST**: All 30 SKUs and metadata are encoded into a concise query string (under 800 characters) inside a GET request URL, enabling CloudFront caching (CDN edge servers generally do not cache POST requests).
* **NDJSON Format**: Allows the browser to parse and render pricing data line by line as it arrives, without waiting for the entire HTTP response body to close.
* **Speed-Optimized GZIP Compression**: Reduces response payload size by ~76% (from 170 KB down to 40 KB) while preserving low latency.
* **Partial Failure Handling**: If an error occurs while fetching the price for 1 out of 30 items, the system reports an error specifically for that SKU while the remaining 29 items display normally.
* **Batch Size Limit of 30 Products**: Balances Lambda execution duration (preventing timeouts) against downstream concurrency limits.

### Optimization Results Across 4 Phases (Load Testing with K6, 500 Concurrent Users)

| Phase | Technical Improvements | P90 Latency |
|---|---|---|
| 1 - Baseline | Shared VPN connection, Lambda buffered entire payload, uncompressed | 4,500 ms |
| 2 | Lambda inside dedicated VPC + Provisioned Concurrency | 1,000 ms |
| 3 | Enabled HTTP/2 + GZIP compression | 218 ms |
| 4 - Production | Added CloudFront caching, 95% of traffic served at edge | **50 ms** |

* On average, only 1 out of every 20 requests actually invokes Lambda; the remaining 19 requests are served directly from CloudFront edge caches.
* The number of origin servers required dropped from over 100 auto-scaled instances during peak traffic down to just 5–10 Lambda functions, drastically lowering infrastructure costs and operational overhead.

### Key Takeaways

* Instead of attempting to build "smarter caches," the right architectural decision is sometimes to **eliminate intermediate caching entirely** and optimize the direct pathway to the authoritative data source.
* Caching always entails a trade-off between speed and accuracy — for highly dynamic data such as pricing, accuracy takes priority.
* A **streaming** mindset (delivering partial results immediately rather than waiting for completion) can be applied to many real-time aggregation workloads: product catalogs, inventory levels, recommendation engines, and more.

### Architecture Diagram

![Samsung Real-Time Pricing Architecture with AWS Lambda Response Streaming](/content/BlogsPosted/image/Samsung_AWS.jpg)

### References & Published Posts

- **Post on AWS Study Group FCJ Community:** [View post on Facebook](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2206091146822512/)
- **Original AWS Architecture Blog Post:** [How Samsung achieved real-time pricing with AWS Lambda Response Streaming](https://aws.amazon.com/blogs/architecture/how-samsung-achieved-real-time-pricing-with-aws-lambda-response-streaming/)
