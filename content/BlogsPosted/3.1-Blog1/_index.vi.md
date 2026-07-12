---
title: "Blog 1"
date: 2026-07-08
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# SAMSUNG XỬ LÝ BÀI TOÁN "GIÁ BỊ LỆCH" NHỜ AWS LAMBDA RESPONSE STREAMING

Khi vận hành hệ thống E-commerce ở quy mô lớn, một trong những bài toán khó nhất là tính toán và hiển thị giá theo thời gian thực (real-time pricing) cho từng người dùng mà không làm chậm tốc độ tải trang. Samsung đã ứng dụng **AWS Lambda Response Streaming** để giải quyết triệt để vấn đề độ trễ này cho hệ thống e-commerce toàn cầu của họ.

### Vấn đề: giá hiển thị và giá thanh toán không khớp nhau

* Samsung.com bán nhiều loại sản phẩm với vô số biến thể (phiên bản, khuyến mãi, khu vực), khiến việc tính giá trở nên phức tạp
* Cách làm cũ: dùng cron job chạy mỗi giờ để tính trước giá cho mọi tổ hợp sản phẩm rồi lưu vào cache
* Hai hệ quả tiêu cực của cách làm cũ:
  * **Bùng nổ tổ hợp**: số bản ghi cần tính trước tăng vọt dù phần lớn không ai xem tới, gây lãng phí tài nguyên
  * **Độ trễ đồng bộ**: giá cache không cập nhật kịp khi có flash sale, dẫn đến hiện tượng giá lệch giữa lúc xem sản phẩm và lúc thanh toán ("cart shock"), ảnh hưởng đến lòng tin khách hàng
* Gốc rễ vấn đề: bất kỳ lớp cache trung gian nào cũng sẽ dần lệch khỏi dữ liệu gốc theo thời gian

### Hướng đi mới: bỏ cache trung gian, truy vấn trực tiếp nguồn dữ liệu

* Samsung xây dựng **Bulk Arbitration Engine** - một lớp điều phối không lưu trạng thái (stateless), truy vấn trực tiếp Pricing Engine theo thời gian thực thay vì tra cache cũ
* Luồng hoạt động:
  1. Trình duyệt gửi một request duy nhất yêu cầu giá cho khoảng 30 SKU
  2. Một hàm AWS Lambda tách request thành 30 lệnh gọi song song tới Pricing Engine
  3. Kết quả trả về tới đâu, Lambda **stream** ngay dữ liệu đó về trình duyệt tới đó, không cần chờ đủ 30 kết quả
* Công nghệ cốt lõi: **AWS Lambda Response Streaming** kết hợp **Amazon CloudFront** trỏ thẳng vào Lambda, cache những gì cache được ngay ở tầng edge gần người dùng nhất
* Lưu ý: kiến trúc thực tế không đi qua Amazon API Gateway - CloudFront trỏ thẳng tới Lambda làm origin

### Giải pháp kỹ thuật: trả dữ liệu theo luồng thay vì "all-or-nothing"

* Hàm Lambda được bọc bằng `awslambda.streamifyResponse()`, cho phép đẩy dữ liệu ra ngay khi có kết quả thay vì đợi xử lý xong toàn bộ
* 30 lệnh gọi tới Pricing Engine chạy song song; mỗi khi có giá của một SKU, dữ liệu được ghi ngay vào response dưới dạng **NDJSON** (mỗi dòng một object JSON)
* Dữ liệu được nén **GZIP ở mức ưu tiên tốc độ (Z_BEST_SPEED)** trước khi gửi, giúp giảm dung lượng đáng kể mà không tốn thêm thời gian xử lý
* Nhờ đó, chỉ số **Time to First Byte (TTFB)** giảm mạnh, người dùng thấy giá xuất hiện gần như tức thì

### Một số kỹ thuật triển khai đáng chú ý

* **Nén request vào GET thay vì dùng POST**: mã hóa toàn bộ 30 SKU và thông tin liên quan thành một chuỗi query gọn (dưới 800 ký tự) để nhét vào URL của GET request, nhờ đó CloudFront có thể cache được (POST thì CDN không cache)
* **Định dạng NDJSON**: giúp trình duyệt xử lý và hiển thị giá ngay khi từng dòng dữ liệu về tới, không cần chờ toàn bộ phản hồi
* **Nén GZIP ưu tiên tốc độ**: giúp giảm khoảng 76% kích thước phản hồi (từ 170KB xuống 40KB) mà vẫn đảm bảo tốc độ xử lý
* **Chịu lỗi từng phần (partial failure)**: nếu một trong 30 sản phẩm bị lỗi khi lấy giá, hệ thống chỉ báo lỗi cho riêng sản phẩm đó, các sản phẩm còn lại vẫn hiển thị bình thường
* **Giới hạn 30 sản phẩm/lần gọi**: cân bằng giữa thời gian xử lý của Lambda (tránh timeout) và số lượng request chạy song song

### Kết quả tối ưu qua 4 giai đoạn (load test với K6, mô phỏng 500 người dùng đồng thời)

| Giai đoạn | Thay đổi kỹ thuật | Độ trễ P90 |
|---|---|---|
| 1 - Ban đầu | Kết nối qua VPN chung, Lambda buffer toàn bộ phản hồi, chưa nén | 4.500 ms |
| 2 | Lambda trong VPC riêng + Provisioned Concurrency | 1.000 ms |
| 3 | Bật HTTP/2 + nén GZIP | 218 ms |
| 4 - Production | Thêm cache ở CloudFront, 95% traffic phục vụ tại edge | **50 ms** |

* Trung bình cứ 20 request thì chỉ 1 request thực sự "chạm" tới Lambda, 19 request còn lại được phục vụ ngay tại edge của CloudFront
* Số lượng máy chủ cần duy trì giảm từ hơn 100 máy (auto-scale mùa cao điểm) xuống chỉ còn 5-10 hàm Lambda, giảm đáng kể chi phí và gánh nặng vận hành

### Bài học rút ra

* Thay vì cố làm cache "thông minh hơn", đôi khi giải pháp đúng là **bỏ hẳn cache trung gian** và tối ưu đường truyền trực tiếp tới nguồn dữ liệu
* Cache luôn đi kèm đánh đổi giữa tốc độ và độ chính xác - với dữ liệu biến động liên tục như giá bán, độ chính xác quan trọng hơn
* Tư duy **streaming** (trả kết quả ngay khi có, không đợi xử lý xong toàn bộ) là cách tiếp cận có thể áp dụng cho nhiều bài toán khác cần tổng hợp dữ liệu từ nhiều nguồn trong thời gian thực: danh mục sản phẩm, tồn kho, hệ thống gợi ý...

### Hình ảnh minh họa kiến trúc

![Kiến trúc Real-time Pricing của Samsung trên AWS Lambda Response Streaming](/BlogsPosted/image/Samsung_AWS.jpg)

### Nguồn tham khảo & Bài viết đã đăng

- **Bài đăng trên cộng đồng AWS Study Group FCJ:** [Xem bài viết trên Facebook](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2206091146822512/)
- **Bài viết gốc từ AWS Architecture Blog:** [How Samsung achieved real-time pricing with AWS Lambda Response Streaming](https://aws.amazon.com/blogs/architecture/how-samsung-achieved-real-time-pricing-with-aws-lambda-response-streaming/)
