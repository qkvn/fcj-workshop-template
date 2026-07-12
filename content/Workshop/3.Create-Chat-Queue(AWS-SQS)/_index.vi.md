---
title: "Tạo hàng đợi tin nhắn Amazon SQS"
date: 2026-06-21
weight: 4
chapter: false
pre: ""
---

# Bước 2: Tạo hàng đợi tin nhắn Amazon SQS

### Giới thiệu

Amazon SQS đóng vai trò là hàng đợi trung gian giữa Amazon S3 và AWS Lambda.

Khi người dùng tải ảnh lên S3, S3 sẽ gửi thông báo vào SQS. Lambda sẽ đọc message từ SQS và xử lý dần dần, giúp hệ thống ổn định hơn khi có nhiều ảnh được tải lên cùng lúc.

---

### Các bước thực hiện

1. Truy cập **AWS Console**, tìm dịch vụ **Amazon SQS**, sau đó chọn **Create queue**.

![Tìm Amazon SQS](./images/image13.png)

2. Chọn loại queue là **Standard Queue**.

Không chọn **FIFO Queue** trong workshop này vì mục tiêu là xử lý ảnh bất đồng bộ với khả năng mở rộng cao, không yêu cầu thứ tự tuyệt đối.

![Chọn Standard Queue](./images/image14.png)

3. Đặt tên queue là **image-processing-queue**.

![Đặt tên SQS queue](./images/image15.png)

4. Cấu hình các thông số quan trọng cho queue.

![Cấu hình thông số SQS](./images/image16.png)

![Kiểm tra cấu hình SQS](./images/image17.png)

5. Chọn **Create queue** để tạo hàng đợi.

6. Sau khi tạo xong, copy lại **ARN** của queue. ARN này sẽ được dùng ở bước cấu hình S3 Event Notification.

![Copy ARN của SQS queue](./images/image18.png)
