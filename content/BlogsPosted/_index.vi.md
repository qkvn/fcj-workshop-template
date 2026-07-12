---
title: "PHẦN 3: CÁC BÀI BLOGS ĐÃ ĐĂNG"
menuTitle: "Các bài blogs đã đăng"
date: 2026-07-24
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Phần này giới thiệu các bài blog kỹ thuật mà tôi và nhóm đã nghiên cứu, biên soạn và chia sẻ trên cộng đồng AWS Study Group.

<div style="text-align: left;">

### Danh sách bài blog

1. [Blog 1 — Samsung xử lý bài toán "giá bị lệch" nhờ AWS Lambda Response Streaming](3.1-Blog1/)  
   Bài blog giải thích cách Samsung ứng dụng AWS Lambda Response Streaming để khắc phục triệt để bài toán độ trễ trong hệ thống thương mại điện tử toàn cầu. Bằng cách loại bỏ các lớp cache trung gian và truy vấn trực tiếp Pricing Engine theo thời gian thực, Samsung đã giảm 98% độ trễ P90 (từ 4.500 ms xuống còn 50 ms) và tối ưu hóa hạ tầng từ hơn 100 máy chủ xuống chỉ còn 5–10 hàm Lambda.

2. [Blog 2 — S3 Intelligent-Tiering: Khi lưu trữ dữ liệu tự "biết" nên nằm ở tầng nào](3.2-Blog2/)  
   Bài blog giới thiệu Amazon S3 Intelligent-Tiering, lớp lưu trữ (storage class) tự động theo dõi tần suất truy cập của từng object và luân chuyển dữ liệu vào đúng tầng lưu trữ phù hợp mà không cần can thiệp thủ công hay tốn phí giám sát. Cơ chế này bao gồm 3 tầng truy cập tự động (Frequent Access, Infrequent Access, Archive Instant Access) cùng 2 tầng lưu trữ sâu tùy chọn, mang lại giải pháp tối ưu chi phí hiệu quả cho các data lake có mô hình truy cập khó dự đoán.

3. [Blog 3 — Đảm bảo công bằng trong game: Phát hiện và ngăn chặn gian lận qua ảnh đại diện với Amazon Textract](3.3-Blog3/)  
   Bài blog trình bày giải pháp kiến trúc Serverless sử dụng Amazon Textract để tự động phát hiện và xử lý văn bản nhúng trái phép trên ảnh đại diện game. Khi người dùng tải ảnh lên Amazon S3, hàm AWS Lambda tự động gọi Amazon Textract để trích xuất tọa độ văn bản, làm mờ vùng vi phạm bằng thuật toán Gaussian Blur và gửi thông báo qua Amazon SNS tới quản trị viên — giúp bảo vệ môi trường game công bằng, an toàn với độ chính xác cao mà không tốn chi phí quản lý máy chủ.

</div>
