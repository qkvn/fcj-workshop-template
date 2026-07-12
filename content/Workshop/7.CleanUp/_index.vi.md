---
title: "Dọn dẹp tài nguyên"
date: 2026-06-21
weight: 8
chapter: false
pre: ""
---

# Bước 6: Dọn dẹp tài nguyên

### Mục tiêu

Sau khi hoàn thành workshop, bạn cần xóa các tài nguyên đã tạo để tránh phát sinh chi phí không cần thiết.

---

### 1. Xóa SQS Queue

1. Truy cập **Amazon SQS**.

2. Chọn queue đã tạo trong workshop, sau đó chọn **Delete**.

![Xóa SQS queue](./images/image1.png)

3. Nhập **Confirm** để xác nhận xóa queue.

![Xác nhận xóa SQS queue](./images/image2.png)

---

### 2. Xóa Lambda Function

1. Truy cập **AWS Lambda**.

2. Chọn function đã tạo trong workshop, sau đó chọn **Delete**.

![Xóa Lambda function](./images/image3.png)

3. Nhập xác nhận để xóa function.

![Xác nhận xóa Lambda function](./images/image4.png)

---

### 3. Xóa S3 Bucket

1. Truy cập **Amazon S3**.

2. Vào bucket đã tạo trong workshop.

3. Chọn bucket cần xóa, sau đó chọn **Delete**.

Lưu ý: S3 bucket cần được làm trống trước khi xóa. Nếu bucket còn object, hãy xóa toàn bộ object trong bucket trước.

![Xóa S3 bucket](./images/image5.png)

---

### 4. Xóa IAM Role

1. Truy cập **IAM**.

2. Chọn **Roles**.

3. Chọn IAM Role **Lambda-ImageProcessing-Role** đã tạo trong workshop, sau đó xóa role.

![Xóa IAM Role](./images/image6.png)
