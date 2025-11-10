---
title: "Nhật ký tuần 5"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

## Mục Tiêu Tuần 5

- Hoàn thành Markdown sang Qdrant
- Hoàn thành giai đoạn 1 của dự án. (Mở rộng do thiếu kỹ năng kỹ thuật để triển khai)
- Tìm cách kết hợp PDF đã chia nhỏ, với thứ tự đúng và hình ảnh cho mỗi câu lệnh được liên kết chính xác trong Qdrant.
- Chuẩn bị cho kỳ thi, tiếp tục học bảo mật AWS.

## Nhiệm Vụ Thực Hiện Trong Tuần

| Ngày | Nhiệm Vụ | Ngày Bắt Đầu | Ngày Kết Thúc | Tài Liệu Tham Khảo |
|---|---|---|---|---|
| 1 | - Triển khai phương pháp trong Python để trích xuất hình ảnh/bản phác thảo/minh họa từ PDF<br>- Xử lý thêm các trường hợp hình ảnh cho mỗi câu lệnh bài toán<br>- Thay đổi prompt LLM để xử lý bảng trong PDF sang Markdown<br>- Tiếp tục học Qdrant và soạn thảo trường nào cần thiết, sẽ được nhúng cho input. | 6/10/2025 | 6/10/2025 |  |
| 2 | - Triển khai markdown sang database (MySQL & Qdrant).<br>- Khởi tạo models để review (Đã được Quang xem xét và chấp nhận).<br>- Học cách ngăn chặn vòng lặp/Quan hệ giữa các models trong Java. | 7/10/2025 | 7/10/2025 | - https://vladmihalcea.com/the-best-way-to-use-the-manytomany-annotation-with-jpa-and-hibernate/<br>- https://vladmihalcea.com/tag/many-to-many/<br>- https://vladmihalcea.com/the-best-way-to-map-a-onetomany-association-with-jpa-and-hibernate/ |
| 3 | - Học cho kỳ thi. | 8/10/2025 | 8/10/2025 |  |
| 4 | - Triển khai các models đã chấp nhận vào database (Không có hình ảnh).<br>- Gặp và giải quyết vấn đề trong models ngăn cản việc lưu (Quan hệ One to One giữa Statement và Figure). | 9/10/2025 | 9/10/2025 |  |
| 5 | - Triển khai lưu hình ảnh vào databases. (Kết nối đầy đủ giữa statement và figure)<br>- Kiểm tra lưu trên S3, và sửa nơi nó lưu một phần, làm cho nó tuân thủ Atomicity trong thực hành ACID. | 10/10/2025 | 10/10/2025 |  |
