---
title: "Kiểm thử hệ thống"
date: 2026-06-21
weight: 7
chapter: false
pre: ""
---

# Bước 5: Kiểm thử hệ thống

### Mục tiêu

Xác nhận toàn bộ luồng **S3 -> SQS -> Lambda -> AI** hoạt động đúng.

Trong bước này, bạn sẽ upload ảnh lên S3, kiểm tra Lambda xử lý message từ SQS và xem kết quả phân tích trong CloudWatch Logs.

---

### 5.1 - Chuẩn bị ảnh test

Chuẩn bị ít nhất 2 loại ảnh:

- Ảnh kiện hàng bị móp hoặc rách để kiểm tra Amazon Rekognition phát hiện đúng nội dung ảnh.
- Ảnh có nhãn dán với mã vận đơn rõ ràng để kiểm tra Amazon Textract trích xuất đúng văn bản.

---

### 5.2 - Tải ảnh lên S3

1. Vào S3 bucket **logistics-raw-images-&lt;tên-bạn&gt;**, sau đó chọn **Upload**.

2. Tải lên khoảng **5-10 ảnh** cùng lúc để kiểm tra khả năng xử lý song song.

3. Chọn **Upload** để bắt đầu tải ảnh.

![Upload ảnh lên S3](./images/image13.png)

![Hoàn tất upload ảnh](./images/image14.png)

---

### 5.3 - Kiểm tra CloudWatch Logs

1. Truy cập **CloudWatch**, chọn **Log groups**.

2. Tìm log group **/aws/lambda/image-quality-processor**.

3. Chọn log stream mới nhất và kiểm tra output từ Lambda.

![Kiểm tra CloudWatch Log groups](./images/image15.png)

![Kiểm tra output Lambda](./images/image16.png)

---

### 5.4 - Kiểm tra SQS đã xử lý sạch

1. Vào **SQS Console**, chọn queue **image-processing-queue**.

2. Chọn **Send and receive messages**.

3. Chọn **Poll for messages**.

Nếu queue trống, nghĩa là Lambda đã xử lý hết toàn bộ tin nhắn thành công.

![Kiểm tra message trong SQS](./images/image17.png)
