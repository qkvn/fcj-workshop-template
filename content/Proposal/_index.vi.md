---
title: "Đề xuất dự án"
date: 2026-06-21
weight: 2
chapter: true
pre: " <b> 2. </b> "
---

# Giải pháp tự động hóa giám sát chất lượng hàng hóa dựa trên Serverless và AI/ML

### Tóm tắt điều hành

Trong lĩnh vực Logistics, doanh nghiệp đang chịu áp lực lớn về thời gian xử lý, độ chính xác dữ liệu và khả năng kiểm soát tổn thất hàng hóa. Việc giám sát chất lượng hàng hóa tại kho hiện nay phần lớn vẫn phụ thuộc vào con người, dẫn đến tình trạng xử lý sự cố chậm, dữ liệu bị phân tán và khó cập nhật theo thời gian thực.

Nhóm đề xuất xây dựng hệ thống **"Giải pháp tự động hóa giám sát chất lượng hàng hóa dựa trên kiến trúc Serverless và Trí tuệ nhân tạo (AI/ML)"**. Đây là một hệ thống phần mềm ứng dụng các dịch vụ của Amazon Web Services (AWS), cho phép nhân viên giao hàng hoặc khách hàng chụp ảnh kiện hàng gặp sự cố như rách, vỡ, móp méo hoặc hư hỏng, sau đó tải trực tiếp hình ảnh lên hệ thống để được phân tích tự động.

Hệ thống sử dụng AI/ML để nhận diện mức độ hư hại của hàng hóa, trích xuất thông tin vận đơn, lưu trữ bằng chứng và gửi cảnh báo cho bộ phận liên quan. Nhờ đó, quy trình xử lý sự cố có thể được rút ngắn từ vài giờ hoặc vài ngày xuống còn vài giây, đồng thời giúp doanh nghiệp giảm chi phí vận hành, hạn chế sai sót thủ công và nâng cao chất lượng dịch vụ.

---

### Tuyên bố vấn đề

#### Vấn đề hiện tại

Theo khảo sát thực tế, việc ghi nhận và xử lý hàng hóa hư hỏng hiện nay đang gặp nhiều điểm lỗi trong quy trình vận hành:

- **Quy trình thủ công, tốn thời gian và dễ sai sót:** Khi phát hiện hàng hóa bị hư hỏng, nhân viên thường phải tự chụp ảnh bằng điện thoại cá nhân, gửi qua nhóm chat hoặc lập tờ trình giấy. Bộ phận thẩm định sau đó phải tổng hợp các thông tin rời rạc này để nhập lại vào hệ thống ERP và xử lý thủ tục bồi hoàn cho khách hàng. Quy trình này mất nhiều thời gian và dễ gây nhầm lẫn dữ liệu.

- **Thiếu khả năng tự động hóa và cảnh báo tức thời:** Do chưa có phần mềm tự động phân tích hình ảnh và đọc thông tin đơn hàng, quản lý kho thường không biết ngay mức độ hư hỏng của hàng hóa để đưa ra quyết định xử lý kịp thời. Việc phản hồi chậm làm ảnh hưởng đến trải nghiệm khách hàng và uy tín dịch vụ.

- **Thiếu khả năng mở rộng hệ thống:** Vào mùa cao điểm, số lượng hàng hóa đổ về kho tăng mạnh. Quy trình kiểm tra thủ công không thể xử lý song song nhiều đơn hàng cùng lúc, dẫn đến quá tải, bỏ sót hàng hóa hư hỏng hoặc để sản phẩm lỗi tiếp tục đi đến tay người tiêu dùng.

- **Dữ liệu lưu trữ phân mảnh, khó truy xuất:** Hồ sơ sự cố hiện có thể nằm rải rác trong sổ tay, file Excel hoặc các nhóm chat. Khi cần thống kê tỷ lệ tổn thất theo tháng/quý để báo cáo cho ban giám đốc, nhân viên phải rà soát thủ công, mất thời gian và dễ sai lệch.

- **Chi phí vận hành cao:** Doanh nghiệp phải duy trì nhân sự chuyên trách tại từng điểm kho để kiểm tra, nhập liệu và xử lý sự cố. Bên cạnh chi phí nhân công, các sai sót thông tin và tranh chấp đền bù kéo dài cũng làm suy giảm lợi nhuận và uy tín của doanh nghiệp Logistics.

---

### Giải pháp đề xuất

Nhóm đề xuất xây dựng hệ thống tự động hóa quy trình giám sát chất lượng hàng hóa dựa trên kiến trúc Serverless và AI/ML. Hệ thống tập trung vào các khả năng cốt lõi sau:

- **Tự động hóa bằng AI thông minh:** Hệ thống sử dụng Amazon Rekognition để phân tích hình ảnh hàng hóa và nhận diện dấu hiệu hư hỏng. Kết quả phân tích có thể bao gồm nhãn trạng thái, mức độ hư hại và điểm tin cậy. Amazon Textract được dùng để trích xuất mã vận đơn, địa chỉ hoặc các thông tin văn bản liên quan từ ảnh mà không cần nhập tay.

- **Xử lý bất đồng bộ theo thời gian thực:** Hình ảnh sau khi được tải lên Amazon S3 sẽ kích hoạt luồng xử lý tự động. Dữ liệu hình ảnh, mã vận đơn và kết quả phân tích được đồng bộ về Amazon DynamoDB để tra cứu, thống kê và xuất báo cáo. Cách xử lý này giúp rút ngắn thời gian ghi nhận và phản hồi từ vài ngày xuống còn vài giây.

- **Hạ tầng Serverless tự động co giãn:** Hệ thống chạy trên các dịch vụ Serverless của AWS, có thể tự động mở rộng khi số lượng ảnh tăng đột biến vào mùa cao điểm. Doanh nghiệp không cần quản lý máy chủ thủ công nhưng vẫn đảm bảo khả năng xử lý nhiều yêu cầu cùng lúc.

- **Quản lý tập trung và minh bạch dữ liệu:** Tất cả lịch sử quét, hình ảnh bằng chứng, mã vận đơn và trạng thái xử lý được lưu trữ tập trung. Người quản lý có thể tra cứu lịch sử bất kỳ lúc nào, kiểm tra bằng chứng và xuất báo cáo thống kê tình hình hàng hóa hư hỏng.

- **Tối ưu hóa chi phí vận hành:** Việc đưa AI vào quy trình giúp giảm sai sót so với nhập liệu thủ công, giảm nhu cầu nhân sự chuyên trách tại từng kho, rút ngắn thời gian giải quyết tranh chấp và nâng cao độ tin cậy trong xử lý bồi hoàn.

---

### Kiến trúc giải pháp

#### Kiến trúc tổng quan
![Hình ảnh 1](/content/Proposal/image/DiagramStructure.png)

Hệ thống được thiết kế theo mô hình kiến trúc hướng sự kiện, sử dụng các dịch vụ AWS để tự động co giãn theo lưu lượng thực tế. Toàn bộ vòng đời của một yêu cầu giám sát chất lượng hàng hóa được chia thành 4 lớp công nghệ chính:

| Lớp kiến trúc | Vai trò | Dịch vụ AWS đề xuất |
| --- | --- | --- |
| Frontend & Auth | Cung cấp giao diện web, quản lý đăng nhập/đăng ký, cấp JWT Token và kiểm soát quyền truy cập API | AWS Amplify, Amazon Cognito, Amazon API Gateway |
| Storage | Tiếp nhận và lưu trữ ảnh hàng hóa hư hỏng do người dùng tải lên | Amazon S3 |
| Backend & AI/ML | Xử lý sự kiện tải ảnh, phân tích hình ảnh, trích xuất thông tin vận đơn, đánh giá mức độ hư hại và cập nhật dữ liệu | Amazon SQS, AWS Lambda, Amazon Rekognition, Amazon Textract, Amazon DynamoDB |
| Giám sát & Bảo mật | Theo dõi hoạt động hệ thống, quản lý log, phân quyền truy cập theo nguyên tắc least privilege | Amazon CloudWatch, AWS IAM |

#### Luồng xử lý chính

| Bước | Mô tả |
| --- | --- |
| 1 | Người dùng đăng nhập vào hệ thống thông qua giao diện web được triển khai bằng AWS Amplify và xác thực bằng Amazon Cognito. |
| 2 | Người dùng tải ảnh kiện hàng bị hư hỏng lên hệ thống. Ảnh được lưu vào Amazon S3 Raw Image Bucket. |
| 3 | S3 Event Notification phát sinh sự kiện sau khi ảnh được tải lên thành công và đưa thông tin xử lý vào Amazon SQS. |
| 4 | Lambda orchestrator-function đọc sự kiện từ SQS, sau đó gọi Amazon Rekognition để phát hiện hư hỏng và Amazon Textract để đọc mã vận đơn/thông tin văn bản. |
| 5 | Lambda evaluator-function đánh giá mức độ hư hại dựa trên kết quả AI/ML và xác định trạng thái xử lý. |
| 6 | Lambda processor-function lưu kết quả vào Amazon DynamoDB, cập nhật trạng thái hồ sơ và kích hoạt cảnh báo nếu cần. |
| 7 | Amazon SNS gửi thông báo Email/SMS cho quản lý kho hoặc bộ phận xử lý khi phát hiện sự cố nghiêm trọng. |
| 8 | Amazon CloudWatch ghi nhận log, metrics và hỗ trợ theo dõi lỗi trong toàn bộ quá trình vận hành. |

---

### Các dịch vụ AWS sử dụng

#### Frontend & Auth

- **AWS Amplify:** Lưu trữ và phân phối giao diện web cho người dùng.
- **Amazon Cognito:** Quản lý đăng nhập, đăng ký và cấp JWT Token.
- **Amazon API Gateway:** Tiếp nhận API request, kiểm tra token và chuyển tiếp yêu cầu đến backend.

#### Storage

- **Amazon S3:** Lưu trữ ảnh gốc của hàng hóa, đóng vai trò điểm tiếp nhận dữ liệu đầu vào cho hệ thống.

#### Backend & AI/ML

- **Amazon SQS:** Hàng đợi tin nhắn để xử lý bất đồng bộ các sự kiện tải ảnh.
- **AWS Lambda:** Các function xử lý logic chính: orchestrator, evaluator, processor.
- **Amazon Rekognition:** Phân tích hình ảnh để phát hiện hư hỏng.
- **Amazon Textract:** Trích xuất thông tin văn bản từ ảnh (mã vận đơn, địa chỉ).
- **Amazon DynamoDB:** Lưu trữ kết quả phân tích và trạng thái hồ sơ.

#### Giám sát & Bảo mật

- **Amazon CloudWatch:** Giám sát log, metrics và hoạt động hệ thống.
- **AWS IAM:** Quản lý quyền truy cập theo nguyên tắc least privilege.

---

### Chi phí triển khai

#### Ước tính chi phí hàng tháng

| Dịch vụ | Sử dụng | Chi phí ước tính (USD) |
| --- | --- | --- |
| Amazon S3 | Lưu trữ 10,000 ảnh/tháng, trung bình 2MB/ảnh | ~$0.20 |
| Amazon SQS | 1 triệu tin nhắn/tháng | ~$0.40 |
| AWS Lambda | 100,000 invokes/tháng, trung bình 512MB memory, 500ms duration | ~$0.50 |
| Amazon Rekognition | 100,000 lần phân tích ảnh/tháng | ~$10.00 |
| Amazon Textract | 100,000 lần trích xuất văn bản/tháng | ~$15.00 |
| Amazon DynamoDB | 50GB lưu trữ, 1 triệu read/write units/tháng | ~$15.00 |
| Amazon SNS | 1,000 thông báo/tháng | ~$0.50 |
| AWS Amplify | Hosting web app | ~$0.00 (free tier) |
| Amazon Cognito | 50,000 MAU | ~$0.00 (free tier) |
| **Tổng cộng** | | **~$41.60/tháng** |

---

### Lợi ích mong đợi

- **Giảm thời gian xử lý sự cố:** Từ vài ngày xuống còn vài giây
- **Giảm chi phí vận hành:** Giảm nhân sự thủ công, giảm sai sót
- **Tăng độ chính xác:** AI/ML phân tích thay vì con người
- **Khả năng mở rộng:** Tự động co giãn theo lưu lượng
- **Minh bạch dữ liệu:** Lưu trữ tập trung, dễ tra cứu
