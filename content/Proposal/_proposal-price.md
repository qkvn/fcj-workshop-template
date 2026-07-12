---
title: "Proposal: Ước tính chi phí vận hành hệ thống trên AWS"
date: 2026-06-21
weight: 2
chapter: false
pre: ""
---

# Ước tính chi phí vận hành hệ thống trên AWS

### Tóm tắt điều hành

Tài liệu này ước tính chi phí AWS cho hệ thống phát hiện và xử lý hàng hóa hư hỏng trong kho vận, dựa trên kiến trúc serverless sử dụng Amplify, Cognito, API Gateway, S3, SQS, Lambda, Rekognition, Textract, DynamoDB, SNS, CloudWatch và IAM.

Ước tính được xây dựng cho kịch bản thử nghiệm / vận hành thực tế trong 1 tuần, với khối lượng khoảng 245 ảnh/tuần và ~30 người dùng hoạt động. Trong phạm vi giả định này, tổng chi phí thực tế của tài khoản AWS mới vẫn là $0,00 do toàn bộ mức sử dụng nằm trong Free Tier tương ứng. Nếu tính theo đơn giá chuẩn mà không áp Free Tier, tổng chi phí 1 tuần vào khoảng $1,57 USD.

---

### 1. Tổng quan kiến trúc hệ thống

Hệ thống serverless trên AWS dùng để phát hiện và xử lý hàng hóa hư hỏng trong kho vận. Người dùng truy cập giao diện web được lưu trữ trên AWS Amplify, đăng nhập qua Amazon Cognito để nhận JWT token, sau đó gọi API qua Amazon API Gateway để tải ảnh hàng hóa lên Amazon S3.

Khi ảnh được tải lên, S3 phát sự kiện đẩy vào Amazon SQS để tránh quá tải khi nhiều ảnh được gửi cùng lúc. SQS kích hoạt chuỗi AWS Lambda gồm xử lý ảnh, gọi Amazon Rekognition để phát hiện hư hỏng, gọi Amazon Textract để đọc mã vận đơn hoặc địa chỉ trên nhãn hàng, sau đó ghi kết quả vào Amazon DynamoDB và gửi cảnh báo qua Amazon SNS khi độ tin cậy của AI dưới 80%.

Toàn bộ hệ thống được giám sát bởi Amazon CloudWatch và áp dụng nguyên tắc least-privilege qua AWS IAM cho từng hàm Lambda. Báo cáo này ước tính chi phí AWS cho 12 dịch vụ xuất hiện trong bản vẽ, cho một tuần sử dụng.

---

### 2. Giả định sử dụng làm cơ sở ước tính

Các giả định dưới đây được dùng làm cơ sở tính toán:

- Khối lượng ảnh xử lý: khoảng 35 ảnh/ngày x 7 ngày, tương đương 245 ảnh/tuần.
- Người dùng hoạt động: khoảng 30 nhân viên/khách hàng đăng nhập và sử dụng web app trong tuần.
- Tài khoản AWS: tài khoản mới, còn trong 12 tháng AWS Free Tier.
- Vùng giá tham khảo: US East (N. Virginia).

---

### 3. Bảng ước tính chi phí theo từng dịch vụ AWS (1 tuần)

| Dịch vụ AWS | Khối lượng sử dụng ước tính (1 tuần) | Đơn giá tham khảo (US East, N. Virginia) | Chi phí theo đơn giá chuẩn (chưa áp Free Tier) | Hạn mức AWS Free Tier áp dụng | Chi phí thực tế ước tính (tài khoản mới, đã áp Free Tier) |
| --- | --- | --- | --- | --- | --- |
| AWS Amplify (Hosting giao diện web) | ~5 lần build, ~0,5 GB lưu trên CDN, ~0,5 GB dữ liệu truyền cho ~30 người dùng | $0,01/phút build, $0,023/GB-tháng, $0,15/GB truyền dữ liệu | ≈ $0,23 | 1.000 phút build + 5 GB lưu trữ + 15 GB truyền dữ liệu/tháng (12 tháng đầu) | $0,00 |
| Amazon Cognito (Xác thực đăng nhập) | ~30 người dùng hoạt động (MAU) | $0,015/MAU | ≈ $0,45 | 10.000 MAU/tháng | $0,00 |
| Amazon API Gateway (REST API) | ~1.800 lượt gọi API | $3,50/1 triệu lượt gọi | ≈ $0,006 | 1.000.000 lượt gọi/tháng (12 tháng đầu) | $0,00 |
| Amazon S3 (Raw Image Bucket) | 245 lượt PUT + 245 lượt GET, ~0,5 GB lưu trữ | $0,005/1.000 PUT, $0,0004/1.000 GET, $0,023/GB-tháng | ≈ $0,004 | 2.000 PUT + 20.000 GET + 5 GB lưu trữ/tháng | $0,00 |
| Amazon SQS (Hàng đợi xử lý) | ~735 request | $0,40/1 triệu request | ≈ $0,0003 | 1.000.000 request/tháng | $0,00 |
| AWS Lambda (3 hàm) | ~800 lượt gọi, ~800 GB-giây | $0,20/1 triệu lượt gọi + $0,0000166667/GB-giây | ≈ $0,013 | 1.000.000 lượt gọi + 400.000 GB-giây/tháng | $0,00 |
| Amazon Rekognition (Phát hiện hư hỏng) | 245 ảnh xử lý | $0,001/ảnh | ≈ $0,245 | 5.000 ảnh/tháng (12 tháng đầu) | $0,00 |
| Amazon Textract (Đọc vận đơn) | 245 trang | $0,0015/trang | ≈ $0,368 | 1.000 trang/tháng (3 tháng đầu) | $0,00 |
| Amazon DynamoDB (Lưu log & lịch sử) | 245 lượt ghi + 300 lượt đọc | $0,625/1 triệu WRU, $0,125/1 triệu RRU | ≈ $0,0002 | 25 WCU/RCU + 25 GB lưu trữ/tháng | $0,00 |
| Amazon SNS (Gửi cảnh báo Email/SMS) | ~25 email cảnh báo | $2,00/100.000 email đã gửi | ≈ $0,0005 | 1.000 email/tháng (12 tháng đầu) | $0,00 |
| Amazon CloudWatch (Log, Metric, Alarm) | ~30 MB log, 3-5 metric, 3-5 alarm | $0,50/GB log, $0,30/metric-tháng, $0,10/alarm-tháng | ≈ $0,10 – $0,40 | 5 GB log + 10 metric + 10 alarm/tháng | $0,00 |
| AWS IAM (Phân quyền least-privilege) | Không tính phí theo lượt sử dụng | Miễn phí | $0,00 | Luôn miễn phí | $0,00 |

---

### 4. Tổng hợp chi phí

**Tổng theo đơn giá chuẩn (chưa áp Free Tier):** khoảng  $1,57 USD

Tổng chi phí thực tế ước tính (tài khoản mới, đã áp Free Tier): **$0,00 USD**

Hai dòng trên phản ánh hai góc nhìn:

- Chi phí theo đơn giá chuẩn nếu không có Free Tier.
- Chi phí thực tế sẽ xuất hiện trên hóa đơn AWS trong tuần thử nghiệm, bằng $0,00 vì toàn bộ khối lượng sử dụng nằm trong hạn mức Free Tier.

---

### 5. Dự báo khi mở rộng quy mô hoặc hết hạn Free Tier

Nếu hệ thống vận hành liên tục trong một tháng đầy đủ, phần lớn dịch vụ vẫn nằm trong hạn mức Free Tier hàng tháng. Điểm cần lưu ý nhất là Amazon Textract vì hạn mức miễn phí chỉ kéo dài 3 tháng đầu. Tuy nhiên, ở quy mô hiện tại, tổng chi phí AWS hàng tháng dự kiến vẫn rất thấp, ngay cả khi không còn Free Tier.

---

### 6. Hạn chế của ước tính và khuyến nghị

- Đây là ước tính dựa trên giả định lưu lượng, không phải số liệu billing thực tế.
- Số liệu chính xác nhất là theo dõi trực tiếp trong AWS Cost Explorer sau khi hệ thống chạy thật.
- Giá tham khảo theo vùng US East (N. Virginia); giá tại vùng khác có thể chênh lệch.
- Chưa bao gồm các chi phí ngoài phạm vi bản vẽ như Support Plan, domain, Route 53, WAF hoặc chi phí nhân sự vận hành.

---

### 7. Nguồn tham khảo

- AWS Lambda Pricing: https://aws.amazon.com/lambda/pricing/
- Amazon API Gateway Pricing: https://aws.amazon.com/api-gateway/pricing/
- Amazon S3 Pricing: https://aws.amazon.com/s3/pricing/
- Amazon SQS Pricing: https://aws.amazon.com/sqs/pricing/
- Amazon Rekognition Pricing: https://aws.amazon.com/rekognition/pricing/
- Amazon Textract Pricing: https://aws.amazon.com/textract/pricing/
- Amazon DynamoDB Pricing: https://aws.amazon.com/dynamodb/pricing/on-demand
- Amazon SNS Pricing: https://aws.amazon.com/sns/pricing/
- Amazon Cognito Pricing: https://aws.amazon.com/cognito/pricing/
- Amazon CloudWatch Pricing: https://aws.amazon.com/cloudwatch/pricing/
- AWS Amplify Pricing: https://aws.amazon.com/amplify/pricing/
- AWS Free Tier: https://aws.amazon.com/free/
