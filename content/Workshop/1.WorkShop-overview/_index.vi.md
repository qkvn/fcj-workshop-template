---
title: "Tổng quan về Workshop "
date: 2026-06-21
weight: 2
chapter: false
pre: ""
---

# Xây dựng luồng xử lý hình ảnh bất đồng bộ sử dụng Amazon S3, SQS và AWS Lambda

### Tóm tắt về workshop

Workshop này hướng dẫn bạn xây dựng một luồng xử lý ảnh bất đồng bộ (asynchronous) theo mô hình Event‑Driven trên AWS, giải quyết bài toán “nhiều người dùng upload ảnh cùng lúc” nhưng vẫn đảm bảo hệ thống ổn định, dễ mở rộng và kiểm soát chi phí.

---

### Mục tiêu chính   

- Thiết kế kiến trúc **S3 → SQS → Lambda** để xử lý ảnh theo hàng đợi, tránh Lambda bị gọi ồ ạt khi có nhiều upload đồng thời.
- Viết Lambda (Python) để:
  - Nhận message từ SQS (chứa sự kiện từ S3)
  - Tải ảnh từ S3
  - Gọi Amazon Rekognition để gợi ý nhãn/nhận diện tình trạng (ví dụ: kiện hàng hư hỏng)
  - Gọi Amazon Textract để trích xuất văn bản trên nhãn dán (mã vận đơn, thông tin tuyến, SĐT, …)
  - Ghi kết quả ra CloudWatch Logs để quan sát và kiểm thử
### Bạn sẽ học được gì
  - Vì sao cần SQS làm “buffer” giữa S3 và Lambda trong hệ thống có tải cao.
  - Cách cấu hình IAM Role theo nguyên tắc Least Privilege (cấp đúng quyền cần thiết).
  - Cách tạo & cấu hình:
    - S3 bucket và Event Notification
    - SQS Standard Queue với các thông số quan trọng (Visibility Timeout, retention…)
    - Lambda Trigger từ SQS và xử lý từng message (batch size nhỏ để dễ quan sát)
  - Quy trình Testing & Validation end‑to‑end và Clean up tài nguyên để tránh phát sinh chi phí.