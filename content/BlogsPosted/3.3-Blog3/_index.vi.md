---
title: "Blog 3"
date: 2026-07-11
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# ĐẢM BẢO CÔNG BẰNG TRONG GAME: PHÁT HIỆN VÀ NGĂN CHẶN GIAN LẬN QUA ẢNH ĐẠI DIỆN VỚI AMAZON TEXTRACT

Trong quá trình phát triển và vận hành các trò chơi trực tuyến nhiều người chơi (multiplayer online games), việc duy trì một môi trường chơi game công bằng và lành mạnh luôn là ưu tiên hàng đầu. Thông thường, các hệ thống bảo mật tập trung vào việc chống hack/cheat trong mã nguồn trò chơi, nhưng lại dễ bỏ qua một hình thức gian lận tinh vi: **lạm dụng ảnh đại diện (profile picture)**. Để ngăn chặn các hành vi nhúng thông tin liên lạc trái phép vào ảnh đại diện, AWS đã đề xuất một kiến trúc **Serverless** sử dụng **Amazon Textract** để tự động quét, phát hiện và làm mờ văn bản ngay khi hình ảnh được tải lên.

### Vấn đề: Giới hạn của phương pháp kiểm duyệt truyền thống

* Người chơi có hành vi gian lận thường nhúng số điện thoại, đường dẫn Discord, hoặc mã phòng chat riêng tư trực tiếp vào ảnh đại diện để liên lạc bí mật, thông đồng gian lận theo cặp hoặc lôi kéo người chơi ra khỏi hệ thống
* Cách làm truyền thống: tự triển khai máy chủ riêng (như EC2) chạy các thư viện xử lý ảnh nặng (OpenCV) hoặc tự huấn luyện mô hình OCR
* Ba hạn chế lớn của phương pháp truyền thống:
  * **Chi phí duy trì cao**: Phải duy trì máy chủ xử lý hoạt động 24/7 kể cả khi tần suất tải ảnh của người dùng không cao
  * **Hiệu năng chậm và nghẽn cổ chai**: Hệ thống dễ bị quá tải khi có lượng lớn người chơi đăng ký mới hoặc tải ảnh đồng loạt
  * **Độ chính xác hạn chế**: Khó nhận diện chính xác các kiểu chữ viết tay, phông chữ cách điệu trong game hoặc văn bản bị biến dạng màu sắc

### Hướng đi mới: Kiến trúc Serverless tự động hóa với Amazon Textract

* Thay vì tự quản lý máy chủ OCR phức tạp, hệ thống sử dụng mô hình **Serverless** kết hợp dịch vụ AI được quản lý (**Amazon Textract**) để xử lý hình ảnh hoàn toàn tự động theo luồng sự kiện
* Luồng hoạt động:
  1. **Tải ảnh lên (Upload)**: Người dùng tải ảnh đại diện lên Amazon S3 Bucket chính (Primary Bucket), sự kiện này ngay lập tức kích hoạt hàm AWS Lambda
  2. **Trích xuất tọa độ văn bản**: Hàm Lambda gửi hình ảnh sang **Amazon Textract**. Dịch vụ AI này tự động phân tích, phát hiện toàn bộ văn bản (cả in ấn lẫn viết tay) và trả về tọa độ chính xác (Bounding Boxes) của các vùng chứa văn bản
  3. **Xử lý làm mờ tự động**: Lambda nhận tọa độ văn bản, sử dụng bộ lọc Gaussian Blur để làm mờ chính xác các vùng chứa thông tin nhạy cảm
  4. **Lưu trữ an toàn**: Hình ảnh đã làm mờ được lưu tạm thời vào một S3 processing bucket (để tránh vòng lặp kích hoạt sự kiện vô hạn), sau đó ghi đè lên hình ảnh gốc trong Primary Bucket
  5. **Gửi cảnh báo**: **Amazon SNS** gửi thông báo qua email đến đội ngũ quản trị game (Game Admin) kèm thông tin tài khoản vừa cố tình nhúng văn bản vào ảnh đại diện

### Một số kỹ thuật triển khai đáng chú ý

* **Kiến trúc hướng sự kiện (Event-driven)**: Sử dụng S3 Event Notifications kích hoạt Lambda chỉ khi có ảnh mới được tải lên, loại bỏ hoàn toàn chi phí máy chủ nhàn rỗi
* **Độ chính xác cao từ AI được quản lý**: Amazon Textract xử lý nhận diện văn bản vượt trội trên nhiều ngôn ngữ, phông chữ phức tạp và chữ viết tay
* **Kiểm duyệt tự động không ảnh hưởng trải nghiệm**: Chỉ áp dụng làm mờ (Gaussian Blur) đúng vùng chứa văn bản vi phạm, giữ nguyên chất lượng thẩm mỹ của phần hình ảnh còn lại
* **Khả năng tự động mở rộng (Auto-scaling)**: Lambda và Textract tự động mở rộng theo lưu lượng thực tế, xử lý mượt mà ngay cả trong thời gian cao điểm
* **Tối ưu chi phí theo mức sử dụng (Pay-per-use)**: Chỉ trả phí cho số mili-giây thực thi Lambda và số lượng ảnh thực sự được quét qua Textract

### Bài học rút ra

* Thay vì tự viết và bảo trì các bộ lọc hay mô hình AI phức tạp từ đầu, việc tận dụng các **dịch vụ AI được quản lý sẵn trên đám mây** mang lại độ chính xác cao hơn và tiết kiệm đáng kể thời gian phát triển
* Tư duy **Serverless kết hợp Event-driven** là lời giải tối ưu cho các bài toán kiểm duyệt nội dung người dùng (UGC), đảm bảo cả ba yếu tố: tốc độ xử lý nhanh, bảo mật tự động và chi phí vận hành tối ưu

### Hình ảnh minh họa kiến trúc

![Kiến trúc tự động phát hiện và làm mờ văn bản trên ảnh đại diện với Amazon Textract](/content/BlogsPosted/image/blog3.jpg)

### Nguồn tham khảo & Bài viết đã đăng

- **Bài đăng trên cộng đồng AWS Study Group FCJ:** [Xem bài viết trên Facebook]()
- **Bài viết gốc từ AWS GameTech Blog:** [Ensuring fair play by detecting and preventing profile alterations with Amazon Textract](https://aws.amazon.com/blogs/gametech/ensuring-fair-play-by-detecting-and-preventing-profile-alterations-with-amazon-textract/)
