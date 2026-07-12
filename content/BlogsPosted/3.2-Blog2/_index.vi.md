---
title: "Blog 2"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# [AWS STORAGE] S3 INTELLIGENT-TIERING – KHI LƯU TRỮ DỮ LIỆU TỰ "BIẾT" NÊN NẰM Ở TẦNG NÀO

Khi xây dựng data lake trên S3, một bài toán thường gặp là dữ liệu ngày càng nhiều nhưng mô hình truy cập của từng loại dữ liệu lại rất khó đoán trước - có file được đọc liên tục, có file chỉ đọc vài lần rồi bỏ đó, cũng có file gần như không ai đụng tới sau khi upload. Nếu để tất cả nằm ở S3 Standard thì chi phí sẽ đội lên đáng kể, còn nếu rà soát thủ công để chuyển storage class thì lại tốn công vận hành. **S3 Intelligent-Tiering** ra đời để giải quyết chính bài toán này.

### Ý tưởng cốt lõi

* S3 Intelligent-Tiering tự "quan sát" hành vi truy cập của từng object rồi tự động xếp vào đúng tầng chi phí phù hợp, không cần con người can thiệp
* Thay vì cố dự đoán trước dữ liệu nào sẽ "nguội" để chuyển tầng thủ công, hệ thống tự quan sát hành vi thực tế rồi tự quyết định
* Đặc biệt phù hợp với các data lake có nguồn dữ liệu đa dạng, nơi việc đoán trước pattern truy cập gần như bất khả thi

### 3 tầng hoạt động tự động (không cần cấu hình thêm)

* **Frequent Access** - tầng mặc định khi object mới được tạo, dành cho dữ liệu đang được truy cập thường xuyên
* **Infrequent Access** - nếu object không được đụng tới trong 30 ngày, tự động chuyển xuống tầng này với chi phí thấp hơn
* **Archive Instant Access** - sau 90 ngày không truy cập, object tiếp tục chuyển xuống tầng rẻ hơn nữa, nhưng vẫn truy xuất được ngay lập tức khi cần

### 2 tầng archive không đồng bộ (tùy chọn bật thêm)

* **Archive Access** - sau tối thiểu 90 ngày không truy cập, thời gian phục hồi khoảng 3-5 giờ
* **Deep Archive Access** - sau tối thiểu 180 ngày không truy cập, thời gian phục hồi trong vòng 12 giờ
* Mốc thời gian chuyển tầng linh hoạt, có thể kéo dài tới 730 ngày tùy nhu cầu của từng team

### Về chi phí

* Không tính phí truy xuất dữ liệu (data retrieval) ở bất kỳ tầng nào, kể cả khi object đang nằm ở archive
* Chi phí phát sinh chủ yếu là một khoản phí giám sát nhỏ theo từng object mỗi tháng
* Các object dưới 128 KB được **miễn phí giám sát**, rất có lợi cho hệ thống có nhiều file nhỏ (log, metadata, thumbnail...)

### Khi nào nên dùng, khi nào không

* Phù hợp khi mô hình truy cập dữ liệu khó đoán trước hoặc thay đổi theo thời gian
* Không phải giải pháp tối ưu cho mọi trường hợp: nếu đã biết chắc chắn một tập dữ liệu chỉ đọc một lần rồi lưu trữ lâu dài (ví dụ backup định kỳ), chuyển thẳng sang Glacier hoặc Standard-IA theo cách thủ công có thể tối ưu hơn, vì tránh được khoản phí giám sát hàng tháng (trừ nhóm object dưới 128 KB)

### Bài học rút ra

* Giá trị lớn nhất của Intelligent-Tiering không nằm ở việc rẻ hơn bao nhiêu phần trăm, mà ở việc **thay đổi cách tiếp cận bài toán chi phí lưu trữ**: để hệ thống tự động hóa việc theo dõi và ra quyết định dựa trên dữ liệu hành vi thực tế, thay vì con người phải liên tục giám sát
* Đây là một mảnh ghép phù hợp trong tư duy tối ưu chi phí cloud hiện đại, đặc biệt với các hệ thống data lake có nguồn dữ liệu đa dạng và khó dự đoán

### Hình ảnh minh họa

![Cơ chế hoạt động của Amazon S3 Intelligent-Tiering](/content/BlogsPosted/image/Intelligent-Tiering.jpg)

### Nguồn tham khảo
- **Bài đăng trên cộng đồng AWS Study Group FCJ:** [Xem bài viết trên Facebook](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2205182253580068/?rdid=npZRVT1ZR54BwOQp#)
- **Bài viết gốc từ AWS Storage Blog:** [Manage Amazon S3 storage costs granularly and at scale using S3 Intelligent-Tiering](https://aws.amazon.com/vi/blogs/storage/manage-amazon-s3-storage-costs-granularly-and-at-scale-using-s3-intelligent-tiering/)
