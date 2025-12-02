---
title: "Blog 1"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

# Dịch Blog (Sử dụng AWS Transfer Family và GuardDuty)

## Sử dụng AWS Transfer Family và GuardDuty

Cách sử dụng AWS Transfer Family và GuardDuty để bảo vệ khỏi phần mềm độc hại (malware)

*Tác giả: James Abbot, Suhas Pasricha, Santhosh Srinivasan*
*Ngày đăng: Ngày 30 tháng 04 năm 2025*

**Danh mục:** [Advanced (300)](https://aws.amazon.com/blogs/security/category/learning-levels/advanced-300/), [Amazon EventBridge](https://aws.amazon.com/blogs/security/category/application-integration/amazon-eventbridge/), [Amazon GuardDuty](https://aws.amazon.com/blogs/security/category/security-identity-compliance/amazon-guardduty/), [Amazon Simple Notification Service (SNS)](https://aws.amazon.com/blogs/security/category/messaging/amazon-simple-notification-service-sns/), [Amazon Simple Storage Service (S3)](https://aws.amazon.com/blogs/security/category/storage/amazon-simple-storage-services-s3/), [AWS Lambda](https://aws.amazon.com/blogs/security/category/compute/aws-lambda/), [AWS Step Functions](https://aws.amazon.com/blogs/security/category/application-services/aws-step-functions/), [AWS Transfer Family](https://aws.amazon.com/blogs/security/category/migration/aws-transfer-family/), [Security, Identity, & Compliance](https://aws.amazon.com/blogs/security/category/security-identity-compliance/), [Storage](https://aws.amazon.com/blogs/security/category/storage/), [Technical How-to](https://aws.amazon.com/blogs/security/category/post-types/technical-how-to/)

Các tổ chức thường cần chia sẻ tệp tin một cách an toàn với các bên bên ngoài qua internet. Việc cho phép truy cập công khai vào một máy chủ truyền tệp khiến tổ chức đối mặt với các mối đe dọa tiềm ẩn, chẳng hạn như các tệp tin nhiễm phần mềm độc hại (malware) do các tác nhân xấu hoặc do người dùng hợp pháp vô tình tải lên. Để giảm thiểu rủi ro này, các công ty có thể thực hiện các bước để đảm bảo rằng các tệp tin nhận được qua các kênh công cộng được quét tìm phần mềm độc hại trước khi xử lý.

Bài viết này trình bày cách sử dụng **AWS Transfer Family** và **Amazon GuardDuty** để quét các tệp được tải lên qua máy chủ SFTP (secure FTP) nhằm tìm phần mềm độc hại, như một phần của một quy trình truyền tệp tổng thể. Đối với những độc giả có thể đã đọc một bài blog trước đó về chủ đề này, điểm khác biệt chính là giải pháp này được quản lý hoàn toàn và không yêu cầu triển khai tài nguyên tính toán. GuardDuty tự động cập nhật các chữ ký phần mềm độc hại mỗi 15 phút thay vì sử dụng một ảnh container (container image) để quét, tránh được việc phải vá lỗi thủ công để giữ cho các chữ ký được cập nhật.

## Điều kiện tiên quyết:

Để triển khai giải pháp trong bài viết này, bạn sẽ cần:

* Một tài khoản AWS: Bạn cần quyền truy cập vào AWS để triển khai giải pháp này. Nếu bạn chưa có tài khoản, hãy xem [Bắt đầu xây dựng trên AWS ngay hôm nay](https://aws.amazon.com/getting-started/).
* AWS CLI: Cài đặt và cấu hình Giao diện Dòng lệnh AWS (AWS CLI) để xác thực với tài khoản AWS của bạn. Thiết lập các biến môi trường cho tài khoản AWS của bạn bằng cách sử dụng access token và secret access key cho môi trường của bạn.
* Git: Bạn sẽ sử dụng Git để tải mã nguồn ví dụ từ GitHub.
* Terraform: Bạn sẽ sử dụng Terraform để chạy tự động hóa. Làm theo hướng dẫn cài đặt Terraform để tải xuống và thiết lập Terraform.

## Tổng quan về giải pháp

Giải pháp này sử dụng **Transfer Family** và **GuardDuty**. Transfer Family cung cấp một dịch vụ truyền tệp an toàn mà bạn có thể sử dụng để thiết lập một máy chủ SFTP, và GuardDuty là một dịch vụ phát hiện mối đe dọa thông minh. GuardDuty giám sát các hoạt động độc hại và hành vi bất thường để bảo vệ các tài khoản, khối lượng công việc và dữ liệu của AWS. Ở cấp độ cao, giải pháp sử dụng các bước sau:

* Một người dùng tải lên một tệp tin thông qua máy chủ SFTP của Transfer Family.
* Một quy trình công việc (**workflow**) do Transfer Family quản lý sẽ gọi **AWS Lambda** để thực thi một quy trình công việc của **AWS Step Functions**. Quy trình này chỉ bắt đầu sau khi tệp được tải lên thành công.
* Việc tải lên một phần (**partial uploads**) tới máy chủ SFTP sẽ gọi một hàm Lambda xử lý lỗi để báo cáo lỗi tải lên không hoàn tất.
* Một máy trạng thái (**state machine**) của AWS Step Functions gọi một hàm Lambda để di chuyển các tệp đã tải lên đến một bucket **Amazon Simple Storage Service (Amazon S3)** để xử lý và sau đó bắt đầu quét bằng GuardDuty.
* Kết quả quét của GuardDuty được gửi dưới dạng một lệnh gọi lại (**callback**) đến AWS Step Functions.
* Các tệp bị nhiễm độc sẽ được di chuyển hoặc làm sạch.
* Quy trình công việc gửi kết quả cho người dùng thông qua một chủ đề (**topic**) của **Amazon Simple Notification Service (Amazon SNS)**. Đây có thể là thông báo về lỗi, về việc phát hiện tệp độc hại trong quá trình quét, hoặc thông báo về việc tải lên thành công và tệp đã được quét sạch để xử lý tiếp.

## Kiến trúc giải pháp và hướng dẫn chi tiết

Giải pháp này sử dụng tính năng **Bảo vệ khỏi Phần mềm độc hại cho S3 (Malware Protection for S3)** của GuardDuty để quét các đối tượng mới được tải lên bucket S3. Bạn có thể sử dụng tính năng này của GuardDuty để thiết lập một kế hoạch bảo vệ khỏi phần mềm độc hại cho một bucket S3 ở cấp độ bucket hoặc để theo dõi các tiền tố đối tượng (object prefixes) cụ thể.

**Hình 1: Kiến trúc của giải pháp.**

Các bước sau (xem qua minh họa 1) mô tả quy trình công việc của giải pháp này, bắt đầu từ thời điểm tệp được tải lên cho đến khi nó được quét và đánh dấu là an toàn hoặc bị nhiễm độc, dẫn đến các bước tiếp theo có thể được tùy chỉnh dựa trên trường hợp sử dụng của bạn.

1.  Một tệp được tải lên bằng giao thức **SFTP** thông qua **Transfer Family**.
2.  Nếu tệp được tải lên thành công, Transfer Family sẽ tải tệp lên bucket S3 có tên là **Unscanned** và quy trình **Managed Workflow Complete** được kích hoạt. Đây là quy trình được sử dụng để xử lý các lần tải lên thành công và gọi hàm Lambda **Step Function Invoker**.
3.  **Step Function Invoker** khởi động máy trạng thái và bắt đầu bước đầu tiên trong quy trình bằng cách gọi hàm Lambda **GuardDuty – Scan**.
4.  Hàm **GuardDuty – Scan** di chuyển tệp đến bucket **Processing**. Đây là bucket mà từ đó các tệp sẽ được quét.
5.  Khi một hoạt động tải lên đối tượng được phát hiện, GuardDuty sẽ tự động quét đối tượng đó. Trong triển khai này, một kế hoạch bảo vệ khỏi phần mềm độc hại được tạo cho bucket **Processing**.
6.  Khi quá trình quét hoàn tất, GuardDuty sẽ công bố kết quả quét lên **Amazon EventBridge**.
7.  Một quy tắc (rule) của EventBridge đã được tạo để gọi một hàm Lambda **Callback** bất cứ khi nào một sự kiện quét hoàn tất. EventBridge sẽ gọi hàm với một sự kiện chứa kết quả quét.
8.  Hàm Lambda **Callback** thông báo cho tác vụ **GuardDuty – Scan** bằng cách sử dụng mô hình tích hợp tác vụ gọi lại (**callback task integration pattern**). kết quả của quá trình quét Amazon GuardDuty được trả về cho hàm **GuardDuty – Scan** và các kết quả này được chuyển đến tác vụ **Move File**.
9.  Nếu kết quả là một lần quét sạch không có mối đe dọa nào được phát hiện, tác vụ **Move File** sẽ đặt tệp vào bucket S3 **Clean**, cho biết rằng tệp đã được quét thành công và an toàn để xử lý tiếp. Tại thời điểm này, hàm **Move File** sẽ công bố một thông báo đến chủ đề SNS **Success** để thông báo cho những người đăng ký.
10. Nếu kết quả cho thấy tệp là độc hại, hàm **Move File** sẽ thay vào đó di chuyển tệp đến bucket S3 **Quarantine** để điều tra thêm. Hàm cũng sẽ xóa tệp khỏi bucket **Processing** và công bố một thông báo trong chủ đề **Error** trên SNS để thông báo cho người dùng về một tệp có khả năng độc hại đã được tải lên.
11. Nếu việc tải lên tệp không thành công và tệp không được tải lên đầy đủ, thì Transfer Family sẽ kích hoạt quy trình **Managed Workflow Partial**. **Managed Workflow Partial** là một quy trình xử lý lỗi và gọi hàm **Error Publisher**, được sử dụng để báo cáo các lỗi xảy ra ở bất kỳ đâu trong quy trình. Hàm **Error Publisher** xác định loại lỗi—cho dù là do tải lên một phần hay một vấn đề ở nơi khác trong quy trình—và đặt trạng thái lỗi tương ứng. Sau đó, nó sẽ công bố một thông báo lỗi đến **Error Topic** trên SNS.
12. Tác vụ **GuardDuty – Scan** có một khoảng thời gian chờ (**timeout**) để đảm bảo rằng một sự kiện được công bố đến **Error Topic** để yêu cầu can thiệp thủ công để điều tra thêm nếu tệp không được quét thành công. Nếu tác vụ **GuardDuty – Scan** thất bại, hàm Lambda **Error clean up** sẽ được gọi.
13. Cuối cùng, có một chính sách vòng đời S3 (**S3 Lifecycle policy**) được đính kèm vào bucket **Processing**. Điều này để đảm bảo rằng không có tệp nào bị bỏ lại trong bucket **Processing** quá một ngày.

## Kho mã nguồn

Kho lưu trữ [AWS-samples](https://github.com/aws-samples/aws-terraform-sftp-malware-protection) trên GitHub có một triển khai mẫu được phát triển bằng Terraform và các hàm Lambda dựa trên Python để thực hiện giải pháp này. Giải pháp tương tự cũng có thể được triển khai bằng [AWS CloudFormation](https://aws.amazon.com/cloudformation). Mã nguồn có các thành phần cần thiết để triển khai toàn bộ quy trình nhằm minh họa khả năng của Transfer Family và kế hoạch bảo vệ khỏi phần mềm độc hại của GuardDuty.

## Cài đặt giải pháp

Sử dụng các bước sau để triển khai giải pháp này vào môi trường thử nghiệm của bạn.

1.  Sao chép (clone) kho lưu trữ về thư mục làm việc của bạn bằng Git.
2.  Điều hướng đến thư mục gốc của dự án bạn vừa sao chép.
3.  Cập nhật tệp `locals.tf` của Terraform với các giá trị bạn chọn cho tên bucket S3, tên máy chủ SFTP và các biến khác.
4.  Chạy lệnh `terraform plan`. Nếu mọi thứ trông ổn, hãy chạy `terraform apply` và nhập `yes` để tạo các tài nguyên.

## Dọn dẹp

Sau khi thử nghiệm và khám phá giải pháp, điều quan trọng là phải dọn dẹp các tài nguyên bạn đã tạo để tránh phát sinh chi phí không cần thiết. Để xóa các tài nguyên được tạo bởi giải pháp này, hãy điều hướng đến thư mục gốc của dự án bạn đã sao chép và chạy lệnh sau:

`terraform destroy`

Lệnh này sẽ xóa các tài nguyên được tạo bởi Terraform, bao gồm máy chủ SFTP, các bucket S3, các hàm Lambda và các thành phần khác. Xác nhận việc xóa bằng cách nhập `yes` khi được yêu cầu.

## Kết luận

Bằng cách sử dụng phương pháp được nêu trong bài viết, bạn có thể đảm bảo rằng các tệp nhận được qua SFTP và được tải lên bucket S3 của bạn được quét tìm các mối đe dọa và an toàn cho việc xử lý tiếp theo. Giải pháp này làm giảm bề mặt tiếp xúc rủi ro bằng cách đảm bảo rằng các tệp được tải lên công khai được quét trong một môi trường an toàn trước khi chúng được gửi đến các thành phần khác của hệ thống của bạn. Nếu bạn có phản hồi về bài viết này, hãy gửi bình luận trong phần Bình luận bên dưới.

## Về các tác giả

| |
| :---: |
| **James Abbott** Principal Solutions Architect tại AWS. Làm việc trong mảng Dịch vụ Tài chính Toàn cầu. Ngoài giờ làm việc, anh thích đạp xe leo núi ở North Carolina. |
| **Suhas Pasricha** Sr. Cloud Application Architect thuộc đội ngũ Dịch vụ Chuyên nghiệp tại AWS. Chuyên môn chính trong việc xây dựng và hiện đại hóa các ứng dụng doanh nghiệp quy mô lớn trên cloud, đặc biệt tập trung vào lĩnh vực dịch vụ tài chính. |
| **Santhosh Srinivasan** Cloud Infrastructure Architect tại AWS thuộc đội ngũ Dịch vụ Chuyên nghiệp của AWS. Anh có kinh nghiệm về phát triển web và tự động hóa hạ tầng. Tại Amazon, anh đã giúp khách hàng thiết lập và vận hành môi trường đám mây và landing zone trên quy mô toàn doanh nghiệp. Khi rảnh rỗi, anh thích đọc sách và chơi trò chơi điện tử. |