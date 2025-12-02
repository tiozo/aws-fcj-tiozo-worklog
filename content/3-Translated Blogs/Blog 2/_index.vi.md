---
title: "Translated Blogs"
weight: 1
chapter: false
pre: "<b> 2. </b>"
---

# Dịch Blog (Từ Beta đến đột phá)

## Từ Beta đến đột phá

Từ thử nghiệm đến thành công đột phá: Mở rộng quy mô AI trong y tế từ giai đoạn POC đến vận hành thực tế để tạo ra tác động thiết thực mỗi ngày.

*Tác giả: Sim Yuanwei Aaron, Hardeep Arora*
*Ngày đăng: ngày 06 tháng 05 năm 2025*

**Danh mục:** [Amazon Bedrock](https://aws.amazon.com/blogs/publicsector/category/artificial-intelligence/amazon-machine-learning/amazon-bedrock/), [Artificial Intelligence](https://aws.amazon.com/blogs/publicsector/category/artificial-intelligence/), [Foundation models](https://aws.amazon.com/blogs/publicsector/category/artificial-intelligence/generative-ai/foundation-models/), [Generative AI](https://aws.amazon.com/blogs/publicsector/category/artificial-intelligence/generative-ai/), [Healthcare](https://aws.amazon.com/blogs/publicsector/category/public-sector/healthcare-public-sector/), [Partner solutions](https://aws.amazon.com/blogs/publicsector/category/post-types/partner-solutions/), [Public Sector](https://aws.amazon.com/blogs/publicsector/category/public-sector/)

## Giới thiệu:

[AI tạo sinh (Generative AI)](https://aws.amazon.com/ai/generative-ai/) đang định nghĩa lại khái niệm y tế dự phòng bằng cách mang lại những trải nghiệm được cá nhân hóa, có tính ứng dụng cao và linh hoạt. Những trải nghiệm này giúp thu hẹp khoảng cách giữa lời khuyên lâm sàng truyền thống và các tư vấn không thường xuyên, từ đó hỗ trợ mỗi cá nhân đưa ra các lựa chọn sống lành mạnh hơn mỗi ngày. Khi các hệ thống y tế trên toàn cầu mở rộng từ việc "**chăm sóc khi bị bệnh**" (**sick care**) sang cả "**chăm sóc để sống khỏe**" (**well care**), những công nghệ mới nổi này hứa hẹn tiềm năng to lớn trong việc thu hút, tạo động lực và trao quyền cho mọi người quản lý sức khỏe của mình một cách chủ động.

Mặc dù vậy, việc chuyển đổi các giải pháp ứng dụng AI từ giai đoạn **chứng minh khái niệm (POC)** sang vận hành không phải là một nhiệm vụ đơn giản. Ngoài việc phải vượt qua các thách thức cả về kỹ thuật và phi kỹ thuật thường gặp ở bất kỳ nền tảng nào khi đưa vào vận hành, quy trình này còn đòi hỏi sự thiết kế cẩn thận, sự xác thực từ các bên liên quan và người dùng, cùng với việc tập trung không ngừng vào trải nghiệm của người dùng cuối dựa trên nền tảng y học hành vi dựa trên bằng chứng.

Trong bài đăng này, chúng tôi sẽ thảo luận về **Health Kaki**, một người bạn đồng hành sức khỏe sử dụng AI tạo sinh với [mô hình nền tảng (FM)](https://aws.amazon.com/what-is/foundation-models/) trên [Amazon Bedrock](https://aws.amazon.com/bedrock/), và nêu bật cách đội ngũ đã vượt qua những thách thức trong việc mở rộng quy mô từ ý tưởng đến triển khai trong thực tế.

## Health Kaki: Một người bạn đồng hành sức khỏe, dành riêng cho tôi

**Health Kaki** (với 'kaki' bắt nguồn từ tiếng Malay, có nghĩa là 'bạn thân' hoặc 'bạn đồng hành') là một nền tảng AI tạo sinh được thiết kế để trao quyền cho mỗi cá nhân có thể chủ động trong các lựa chọn về sức khỏe và lối sống của mình thông qua tương tác kỹ thuật số được cá nhân hóa. [Bản PoC](https://temus.com/case-study/health-kaki-a-genai-powered-resident-health-companion/) của Health Kaki [được đồng phát triển](https://www.synapxe.sg/media-releases/innovation/synapxe-new-innovation-lab) bởi Synapxe và Temus với sự hỗ trợ từ [Amazon Web Services (AWS)](https://aws.amazon.com/) và tham vấn từ Hội đồng Xúc tiến Sức khỏe (HPB) của Singapore cho Bộ Y tế Singapore (MOH), nhằm hỗ trợ kế hoạch y tế [HealthierSG](https://www.healthiersg.gov.sg/).

Nền tảng này khai thác sức mạnh của các công cụ AI sử dụng Amazon Bedrock và [mô hình ngôn ngữ lớn (LLM)](https://aws.amazon.com/what-is/large-language-model/) **Claude 3.5 Sonnet** của Anthropic để tạo ra các kế hoạch ăn uống và tập luyện cá nhân hóa từ nguồn tài nguyên và thông tin phong phú trên toàn hệ sinh thái y tế công cộng của Singapore. Các kế hoạch này phù hợp với mục tiêu sức khỏe, sở thích văn hóa, và khuyến nghị lâm sàng của người dùng.

Điều làm cho công nghệ này trở nên mạnh mẽ là ngày nay nó có thể tạo ra vô số hoán vị lựa chọn và thông tin để thúc đẩy một lối sống lành mạnh hơn.

Ví dụ, người dùng có thể nhập một câu lệnh bằng ngôn ngữ tự nhiên:

> Hôm nay tôi muốn ăn chay Thứ Hai. Health Kaki, đổi món ăn được đề xuất này từ gà sang đậu phụ… à và điều chỉnh thời gian nấu cũng như thông tin dinh dưỡng để tôi biết các chỉ số đa lượng (macros) của mình được đảm bảo.

Và Health Kaki tạo ra một kế hoạch ăn uống và các đề xuất được cá nhân hóa, như được hiển thị trong ảnh chụp màn hình sau.

**Minh họa 1. Kế hoạch ăn uống và đề xuất cá nhân hóa của Health Kaki**

Hoặc, người dùng có thể nhập câu lệnh này:

> Health Kaki, hôm qua tôi đã thức khuya làm việc, bạn có thể tìm một lớp yoga gần tôi không? Nhân tiện, lớp học đó có đủ điều kiện để được giảm giá bằng thẻ [PAssion Card](https://www.onepa.gov.sg/passion-card/passion-card-membership/passion-card-faqs) tại Trung tâm Cộng đồng địa phương của tôi không?

Và Health Kaki cung cấp một kế hoạch và các đề xuất tập thể dục được cá nhân hóa, như được hiển thị trong ảnh chụp màn hình sau.

**Minh họa 2. Kế hoạch tập thể dục và đề xuất cá nhân hóa của Health Kaki**

Ảnh chụp màn hình sau đây cho thấy một cuộc trao đổi câu hỏi từ người dùng và câu trả lời từ Health Kaki trên ứng dụng. Tính năng chatbot cho phép người dùng hỏi về chế độ ăn uống và tập thể dục, cũng như cập nhật sở thích của họ.

**Minh họa 3. Trợ lý Health Kaki**

Nói tóm lại, mục tiêu đã được tìm kiếm từ lâu về việc đưa ra sự can thiệp đúng đắn, cho đúng người, vào đúng thời điểm cuối cùng cũng đã ở trong tầm tay với những đổi mới trong LLM và AI tạo sinh.

## Thiết kế giải pháp một cách đúng đắn, ngay từ khi bắt đầu

Việc hào hứng lao vào các công nghệ mới để thử nghiệm ngay lập tức là điều rất hấp dẫn. Suy cho cùng, đó chẳng phải là bản chất của sự đổi mới và tinh thần linh hoạt hay sao? Chúng tôi đồng ý rằng cách tiếp cận đó có giá trị và phù hợp trong một số thời điểm và hoàn cảnh nhất định. Tuy nhiên, nếu mục tiêu thực sự là mở rộng quy mô một giải pháp AI từ PoC lên vận hành, thì một hướng đi khác sẽ có khả năng thành công cao hơn.

Thay vì vội vàng xây dựng giải pháp hoặc để công nghệ hiện tại dẫn dắt hướng đi, bạn nên tập trung vào việc thấu hiểu sâu sắc các nhu cầu, quy trình hiện tại và những ràng buộc. Từ đó, xác định các tính năng và chức năng cốt lõi sẽ giúp giải pháp thực sự hiệu quả. Hãy xem xét các yếu tố sau:

* **Tính khả thi (Viability):** Một trong những khía cạnh then chốt khi chuyển từ PoC sang vận hành là xác thực tính khả thi của nó, vốn bắt nguồn từ vấn đề mà bạn đang giải quyết. Cụ thể: Vấn đề đó có quy mô lớn đến đâu, và giải pháp này giải quyết nguyên nhân gốc rễ của nó như thế nào? Thị trường hiện không thiếu các ứng dụng và nền tảng kỹ thuật số cung cấp giải pháp quản lý sức khỏe và bệnh tật chung chung. Phần lớn trong số đó đã thất bại, đôi khi là những thất bại nặng nề. Điều này càng trở nên quan trọng hơn trong bối cảnh LLM và AI tạo sinh, nơi mà chi phí phát triển, vận hành và duy trì thường chưa có tiền lệ rõ ràng. Chi phí sẽ tăng lên đáng kể khi sản phẩm mở rộng quy mô và mức độ tương tác của người dùng tăng cao.
* **Đánh giá và Thử nghiệm (Evaluation and testing):** LLM và AI tạo sinh đang trỗi dậy và phát triển với tốc độ chưa từng thấy. Do đó, việc hiểu rõ hiệu suất của hệ thống trong các điều kiện khác nhau là bắt buộc—và những điều kiện đó sẽ thay đổi khi giải pháp tiến từ PoC, sang chứng minh giá trị, rồi đến vận hành. Health Kaki đã sử dụng phương pháp thử nghiệm theo kịch bản, đánh giá dựa trên số liệu, và xác thực lặp lại để tinh chỉnh tính năng và kiểm chứng độ tin cậy. Các chỉ số như **điểm trung thực (faithfulness scores)** và **mức độ liên quan của câu trả lời (answer relevancy)** đã cung cấp những insight giá trị về kết quả của mô hình. Càng tiến gần đến giai đoạn sản xuất, mức độ nghiêm ngặt và các loại hình thử nghiệm càng phát triển và gia tăng. Chỉ với cách tiếp cận đa chiều và tinh tế này, thay vì một quy trình kiểm thử an toàn rập khuôn (one-size-fits-all), giải pháp mới có thể cân bằng giữa sự đổi mới và tính an toàn.
* **Đối tượng (Audience):** Chúng ta cần vượt ra ngoài những khái niệm đã lỗi thời về người dùng (users) và kiểm thử chấp nhận của người dùng (**user acceptance testing - UAT**). Hãy xem xét ai mới là người thực sự sử dụng giải pháp. Các nhà công nghệ cần nhận thức rằng trong việc ra quyết định về sức khỏe và các hành động sau đó, không tồn tại một "**người dùng**" đơn lẻ. Ví dụ, bác sĩ có thể đưa ra khuyến nghị sức khỏe (như giảm đường), bệnh nhân có thể đồng tình rằng đó là mục tiêu quan trọng, công ty bảo hiểm hoặc chính phủ có thể chi trả cho dịch vụ, nhưng người chăm sóc gia đình mới là người quyết định bữa tối ăn gì. Các công nghệ mới cũng có thể gây ra tâm lý e ngại về những điều chưa biết hoặc về sự an toàn cho bất kỳ ai trong số các bên liên quan này, và điều đó cũng cần được tính đến. Vì vậy, một giải pháp số trong ngành y tế phải dung hòa được những góc nhìn phức tạp và có vẻ mâu thuẫn này trong quá trình đánh giá.

Để thấy rõ hơn sự phức tạp đó: trong quá trình thử nghiệm, bệnh nhân có thể nói rằng họ không cần hướng dẫn chi tiết về cách giảm đường. Nhưng khi họ bất ngờ bị chẩn đoán mắc một bệnh cấp tính hoặc nhiều bệnh lý cùng lúc, họ có thể sẽ thay đổi suy nghĩ. Hoàn cảnh sức khỏe và cuộc sống của chúng ta luôn thay đổi, do đó những gì người dùng cần và xem trọng cũng sẽ thay đổi theo.

## Vượt qua các thách thức kỹ thuật

Như mọi sự đổi mới khác, luôn có vô số thách thức, bao gồm những **ẩn số đã biết (known unknowns)** và quan trọng hơn là những **ẩn số chưa biết (unknown unknowns)**. Trong quá trình phát triển Health Kaki, một số thách thức kỹ thuật dưới đây đã được giải quyết bằng cách sử dụng các kỹ thuật tạo câu lệnh (**prompting**) nâng cao và tích hợp một cơ sở tri thức theo ngữ cảnh. Các cuộc đánh giá mô hình nghiêm ngặt, sử dụng các tiêu chuẩn như điểm trung thực (**faithfulness scores**) và bảng xếp hạng của Large Model Systems Organization (LMSYS), cho thấy mô hình **Claude 3.5 Sonnet** của Anthropic đã liên tục mang lại kết quả đầu ra đáng tin cậy và nhận biết ngữ cảnh tốt.

Đội ngũ đã đối mặt với nhiều thách thức kỹ thuật khi phát triển Health Kaki, bao gồm:

* **Chất lượng và sự đa dạng của dữ liệu:** Nền tảng cho việc mở rộng quy mô AI thành công là duy trì được nguồn dữ liệu đa dạng, chất lượng cao, phản ánh được các sắc thái đặc thù của cộng đồng dân cư. Đối với Health Kaki, điều này có nghĩa là phải xử lý được bối cảnh văn hóa và ẩm thực độc đáo của Singapore, bao gồm thói quen ăn uống halal và ăn chay, ảnh hưởng của y học cổ truyền Trung Quốc, và các sở thích tập luyện đa dạng. Để giải quyết vấn đề này, đội ngũ đã áp dụng phương pháp kết hợp giữa con người và AI. Dữ liệu từ các nguồn đáng tin cậy như Hội đồng Xúc tiến Sức khỏe (Health Promotion Board) đã được làm giàu siêu dữ liệu (**metadata**) bằng LLM. Các chuyên gia con người đã xác thực và tinh chỉnh dữ liệu này để đảm bảo tính phù hợp về văn hóa và độ chính xác theo ngữ cảnh. Quy trình nghiêm ngặt này đã đặt nền móng cho việc tạo ra các đề xuất tùy chỉnh, tạo được sự đồng cảm với người dùng.
* **Hạ tầng và thiết kế trải nghiệm:** Triển khai giải pháp AI ở quy mô lớn đòi hỏi sự cân bằng tinh tế giữa một hạ tầng mạnh mẽ và một trải nghiệm người dùng liền mạch. Ví dụ, các hệ thống AI tạo sinh, dù mạnh mẽ, có thể gây ra độ trễ trong tính toán, ảnh hưởng đến khả năng phản hồi thời gian thực. Health Kaki đã khắc phục điều này bằng cách áp dụng các chiến lược **tải dần (progressive loading)**. Các chiến lược này cung cấp cho người dùng những nội dung trung gian hấp dẫn trong khi các kết quả cá nhân hóa đang được xử lý. Việc thử nghiệm người dùng trên diện rộng đã xác nhận cách tiếp cận này, với những người tham gia đánh giá cao thiết kế UX chu đáo và các chỉ báo tiến trình rõ ràng giúp giảm thiểu cảm giác phải chờ đợi.
* **Các công cụ cân bằng giữa độ chính xác và khả năng mở rộng:** Việc lựa chọn đúng mô hình và công cụ là vô cùng thiết yếu. Khả năng đã được chứng minh của Claude 3.5 Sonnet từ Anthropic trong việc cân bằng giữa cá nhân hóa, khả năng mở rộng và độ chính xác đã khiến nó trở thành nền tảng trong kiến trúc của Health Kaki. Các chỉ số như tính dễ sử dụng, tốc độ xử lý và sự linh hoạt đã được phân tích để hiểu rõ khả năng của các mô hình trong việc xử lý các yêu cầu luôn thay đổi và tích hợp liền mạch vào nền tảng Health Kaki.
* **Tính nhất quán trong cá nhân hóa:** Để AI tạo sinh có thể xây dựng được lòng tin, các đề xuất không chỉ cần được cá nhân hóa sâu sắc mà còn phải chính xác một cách nhất quán. Việc duy trì sự cân bằng này qua các lần tương tác đã đặt ra một thách thức đáng kể cho Health Kaki.

## Cơ chế bảo vệ (Guardrails)

Việc triển khai các **cơ chế bảo vệ (guardrails)** mạnh mẽ là một khía cạnh cực kỳ quan trọng của dự án Health Kaki. Các rào chắn này đóng vai trò như những cơ chế bảo vệ thiết yếu, giúp các khuyến nghị sức khỏe do AI tạo ra luôn phù hợp, an toàn và đáng tin cậy. Đội ngũ Health Kaki đã tiếp cận thách thức này bằng cách hợp tác chặt chẽ với các chuyên gia lĩnh vực (**subject matter experts**) để xác định một bộ thông số toàn diện. Các cơ chế bảo vệ này bao gồm nhiều khía cạnh khác nhau, bao gồm các lưu ý về chế độ ăn uống, hướng dẫn tập luyện, các biện pháp phòng ngừa theo tình trạng sức khỏe và các yếu tố lối sống. Bằng cách tích hợp các cơ chế bảo vệ này vào lõi của bộ máy đề xuất, Health Kaki có thể mang đến các kế hoạch sức khỏe cá nhân hóa không chỉ hấp dẫn mà còn phù hợp với hồ sơ sức khỏe và nhu cầu riêng biệt của mỗi người dùng. Cách tiếp cận này thể hiện cam kết triển khai AI một cách có trách nhiệm trong công nghệ y tế, cân bằng giữa sự đổi mới với sự an toàn và hiệu quả tổng thể cho người dùng.