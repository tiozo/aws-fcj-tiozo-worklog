---
title: "Nhật ký tuần 3"
weight: 3
chapter: false
pre: "<b> 1.3 </b>"
---

## Mục Tiêu Tuần 3

- Bắt đầu làm việc trên dự án.
- Hoàn thành nhiệm vụ đã nhận và publish lên github.
- Chuyển đổi dự án từ Java backend sang Serverless.
- Kết nối với Bedrock API (LLama Maverick 17B), khởi tạo phản hồi tốt.
- Bắt đầu sử dụng hệ thống cơ bản cho hệ thống gắn thẻ bán tự động cho mỗi bài toán.

## Nhiệm Vụ Thực Hiện Trong Tuần

| Ngày | Nhiệm Vụ | Ngày Bắt Đầu | Ngày Kết Thúc | Tài Liệu Tham Khảo |
|---|---|---|---|---|
| 1 | - Hoàn thiện nhiệm vụ cho iteration đầu tiên. Tập trung vào tạo hệ thống gắn thẻ bán tự động.<br>- Học n8n, để tự động gắn thẻ với Bedrock API. | 22/9/2025 | 22/9/2025 |  |
| 2 | - Kiểm tra kết nối n8n với AWS S3, Bedrock API để chuyển đổi bài toán toán học tiếng Việt sang ngôn ngữ Markdown (Mathjax).<br>- Hoàn thành nhiệm vụ backup legacy (PDF/image endpoint receiver sử dụng Java với SDK Amazon Corretto).<br>- Tạo widget Cloudwatch để giám sát chi phí sử dụng non-root user. (Từ sự kiện FCJ-left over document)<br>- Thiết lập truyền file từ backend local (test) đến AWS S3. | 23/9/2025 | 23/9/2025 | - https://saviorab1.github.io/fcj-leftover/11-monitoring-with-cloudwatch/11.4-create-cost-calculation-widget/<br>- https://programming.am/monitoring-s3-buckets-using-amazon-cloudwatch-6546a01fb763 |
| 3 | - Kiểm tra Metrics từ AWS S3, Giám sát BucketByteSize, AllRequest để quản lý chi phí với S3 (0.3 USD cho 10000 metrics, 1 metric mỗi phút nếu có gì xảy ra)<br>- Học vector database (Qdrant)<br>- Hoàn thành nhiệm vụ dự án (Cắt markdown đã chuyển đổi thành từng câu lệnh với câu trả lời được lưu trong Database.) | 24/9/2025 | 24/9/2025 | - https://docs.aws.amazon.com/AmazonS3/latest/userguide/configure-request-metrics-bucket.html<br>- https://programming.am/monitoring-s3-buckets-using-amazon-cloudwatch-6546a01fb763<br>- https://aws.amazon.com/cloudwatch/pricing/ |
| 4 | - Học cách tích hợp Bedrock với Java backend (Hosted trên legacy server tại nhà).<br>- Thực hành hands-on và thử nghiệm với nhiệm vụ PDF sang Markdown. (Lấy Object từ S3 input/* đến Bedrock, lấy respond prompt -> <Date>.md -> đến S3 output/).<br>- Thiết lập IAM với Nguyên tắc Đặc quyền Tối thiểu. | 25/9/2025 | 25/9/2025 | - https://aws.amazon.com/vi/awstv/watch/9c98d05f7e0/<br>- https://docs.aws.amazon.com/wellarchitected/latest/framework/sec_permissions_least_privileges.html |
| 5 | - Sửa lỗi mất output do xử lý nhiều file mỗi ngày.<br>- Kiểm tra chức năng và push lên github. Hiểu inference model là gì khi gọi từ Java. | 26/9/2025 | 28/9/2025 | - https://github.com/tiozo/FCJ-Problem_Suggest |
