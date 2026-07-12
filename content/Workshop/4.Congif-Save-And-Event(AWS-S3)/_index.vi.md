---
title: "Cấu hình lưu trữ và sự kiện Amazon S3"
date: 2026-06-21
weight: 5
chapter: false
pre: ""
---

# Bước 3: Cấu hình lưu trữ và sự kiện Amazon S3

### Mục tiêu

Trong bước này, bạn sẽ tạo S3 bucket để lưu ảnh và cấu hình để mỗi khi có ảnh mới được tải lên, Amazon S3 tự động gửi thông báo vào SQS queue đã tạo ở bước trước.

---

### 3.1 - Tạo S3 Bucket

1. Truy cập **Amazon S3**, sau đó chọn **Create bucket**.

![Tạo S3 bucket](./images/image19.png)

2. Đặt tên bucket theo định dạng **logistics-raw-images-&lt;tên-bạn&gt;**.

Tên bucket phải là duy nhất trên toàn bộ AWS.

![Đặt tên S3 bucket](./images/image20.png)

3. Chọn Region **ap-southeast-1 (Singapore)** để gần Việt Nam.

![Chọn Region Singapore](./images/image21.png)

4. Giữ nguyên các cài đặt mặc định, sau đó chọn **Create bucket**.

![Giữ cài đặt mặc định](./images/image22.png)

![Tạo bucket thành công](./images/image23.png)

---

### 3.2 - Cấp quyền cho S3 gửi thông báo vào SQS

1. Vào **SQS Console**, chọn queue **image-processing-queue**.

2. Mở tab **Access policy**, chọn chỉnh sửa policy.

3. Thêm policy cho phép S3 gửi message vào SQS.

![Mở Access policy của SQS](./images/image24.png)

Ví dụ policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "s3.amazonaws.com"
      },
      "Action": "sqs:SendMessage",
      "Resource": "arn:aws:sqs:ap-southeast-1:<account-id>:image-processing-queue",
      "Condition": {
        "ArnLike": {
          "aws:SourceArn": "arn:aws:s3:::logistics-raw-images-<ten-ban>"
        }
      }
    }
  ]
}
```

Thay **&lt;account-id&gt;** bằng AWS Account ID của bạn và thay **logistics-raw-images-&lt;ten-ban&gt;** bằng tên bucket đã tạo.

![Thêm policy cho S3 gửi message vào SQS](./images/image25.png)

---

### 3.3 - Cấu hình S3 Event Notification

1. Vào bucket vừa tạo, mở tab **Properties**, kéo xuống phần **Event notifications**, sau đó chọn **Create event notification**.

![Mở Event notifications](./images/image26.png)

![Create event notification](./images/image27.png)

2. Đặt tên event là **new-image-uploaded**.

![Đặt tên event notification](./images/image28.png)

3. Ở phần **Event types**, tích chọn **s3:ObjectCreated:***.

![Chọn event type ObjectCreated](./images/image29.png)

4. Ở phần **Prefix/Suffix**, nhập các suffix ảnh như **.jpg**, **.jpeg**, **.png** để chỉ kích hoạt khi có ảnh tải lên.

Cấu hình này giúp bỏ qua các file không phải ảnh như `.txt` hoặc `.pdf`.

![Cấu hình suffix file ảnh](./images/image30.png)

5. Ở phần **Destination**, chọn **SQS Queue**, sau đó chọn queue **image-processing-queue**.

![Chọn SQS queue làm destination](./images/image31.png)

6. Chọn **Save changes** để lưu cấu hình.

![Lưu event notification](./images/image32.png)
