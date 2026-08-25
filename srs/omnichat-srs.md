# SRS — Omnichat

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — vừa mô tả hành vi hiện tại, vừa là chuẩn cho phát triển tiếp theo (xem "Ghi chú về nguồn gốc tài liệu") |
| **Module** | Omnichat — tiếp nhận, phân công, xử lý và trả lời hội thoại khách hàng qua nhiều kênh (Facebook, Instagram, WhatsApp, Zalo, TikTok, Telegram, Email, Live Chat website) |
| **Ngày viết** | 2026-08-22 |
| **Neo phiên bản hệ thống** | `crm-api` @ `6cb0f24657fbaa856b149df59905925562eb5416` · `crm-web` @ `e65eb38aca4993ff00d2e94d56387746f0ba22f3` · `livechat-widget` @ `acc00d01e10bebd92414207496578ea3cd29c21f` (2026-08-22) — tài liệu mô tả hành vi hệ thống tại các commit này, không tự động đúng mãi mãi |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary, mục "Omnichat") |

## Ghi chú về nguồn gốc tài liệu

Omnichat đã được xây dựng và đưa vào vận hành mà chưa từng có SRS. Tài liệu được viết bằng hai bước: (1) khảo sát hành vi hệ thống đang vận hành, sau đó (2) các vòng review nghiệp vụ với BA/PM để đối chiếu hành vi đó với kỳ vọng đúng của một hệ thống contact center và chốt lại những điểm cần thay đổi.

**Nguyên tắc biên soạn:** đây là tài liệu nghiệp vụ. Nó đặc tả *hệ thống phải làm gì cho doanh nghiệp và khách hàng*, không đặc tả *hệ thống được xây dựng bằng cách nào*. Mọi quy tắc trong tài liệu PHẢI diễn đạt được bằng ngôn ngữ nghiệp vụ và kiểm chứng được bằng hành vi quan sát từ bên ngoài. Giới hạn của nền tảng kênh hoặc của hạ tầng chỉ được nêu khi nó thực sự ràng buộc một cam kết nghiệp vụ, và khi đó phải nêu kèm cam kết bị ràng buộc cùng cách hệ thống ứng xử — không nêu cơ chế. Các ngưỡng thời gian, số lần, số lượng đều là **tham số cấu hình của doanh nghiệp**, không phải hằng số của sản phẩm; tài liệu chỉ cố định một con số khi con số đó chính là cam kết nghiệp vụ.

Các mục `[Đã triển khai]` phản ánh hành vi đã khảo sát tại thời điểm neo phiên bản; các mục/quy tắc `[Yêu cầu mới]` là những điểm BA/PM đã chốt phương án nhưng hệ thống hiện tại chưa phản ánh đúng.

**Quy ước nhãn trạng thái:** mỗi tính năng (FEAT) được gắn nhãn ngay sau tiêu đề:

- **[Đã triển khai]** — hành vi đã được xác minh khớp với hệ thống đang chạy tại các commit neo ở trên.
- **[Yêu cầu mới]** — đã được BA/PM chốt phương án nhưng hệ thống hiện tại chưa phản ánh đúng; đội phát triển cần lên kế hoạch xây dựng. Tham chiếu GitHub issue tương ứng (nếu có) được ghi ở cuối mục, dưới dòng **Tham chiếu**.

Trong một FEAT đã `[Đã triển khai]`, nếu một quy tắc nghiệp vụ (BR) cụ thể là quyết định mới, BR đó được đánh dấu riêng `[Yêu cầu mới]` ngay sau mã số — các BR không có nhãn kế thừa trạng thái của FEAT chứa nó. Với thay đổi tính năng trong tương lai, tài liệu này cần được cập nhật song song (và neo lại commit mới), không để trôi khỏi thực tế vận hành.

---

## 1. Giới thiệu

### 1.1 Mục đích

Tài liệu đặc tả toàn bộ yêu cầu chức năng và phi chức năng của module **Omnichat** — trung tâm tiếp nhận và xử lý hội thoại của một tổng đài đa kênh (contact center), cho phép doanh nghiệp tiếp nhận, phân công, trả lời và theo dõi chất lượng phục vụ khách hàng nhắn tin qua nhiều kênh khác nhau trong cùng một nơi làm việc duy nhất.

### 1.2 Phạm vi

Tài liệu bao trùm toàn bộ hành trình một hội thoại và toàn bộ lớp vận hành đội ngũ xung quanh nó.

**Hành trình hội thoại:** kết nối kênh giao tiếp, tiếp nhận và nhận diện khách hàng, tự động phân công cho Agent, hàng đợi và lời mời nhận việc, chuyển tiếp giữa các Agent, cam kết thời gian phản hồi (SLA) và leo thang khi vi phạm, trả lời tự động bằng Bot và bàn giao cho người, gửi tin nhắn đi, tự động đóng hội thoại không hoạt động, khảo sát hài lòng khách hàng (CSAT), ghi chú/lịch sử xử lý, tìm kiếm tin nhắn, và trải nghiệm trò chuyện của khách hàng trên widget Live Chat nhúng website.

**Lớp vận hành đội ngũ:** giám sát và can thiệp thời gian thực, quản lý chất lượng hội thoại, ca trực và bàn giao ca, kỹ năng và định tuyến theo kỹ năng, danh mục lý do xử lý và nhãn, chặn spam và xử lý lạm dụng, quy tắc tự động hóa, báo cáo vận hành và hiệu suất.

**Quản trị doanh nghiệp:** cấu hình hộp thư, mẫu tin nhắn nhanh, giờ làm việc, chính sách lưu trữ/xuất/xóa dữ liệu, nhật ký thay đổi cấu hình và nhật ký truy cập dữ liệu khách hàng.

**Ngoài phạm vi:**

- Thủ tục đăng ký, xác minh và phê duyệt tài khoản doanh nghiệp với từng nhà cung cấp kênh (Facebook, WhatsApp...) — đây là việc doanh nghiệp làm trực tiếp với nhà cung cấp trước khi kết nối vào hệ thống; tài liệu này chỉ mô tả những gì quản trị viên/Agent nhìn thấy và thao tác được bên trong Omnichat.
- Chiến dịch gửi tin nhắn hàng loạt (Campaign/Broadcast) — chưa tồn tại trong hệ thống, xem Mục 7.
- Các đối tượng nghiệp vụ khác của CRM (Liên hệ, Tài khoản, Cơ hội, Ticket, Công việc) — bao gồm cả quy tắc phân công lẫn chỉ số hiệu suất của chúng. Tài liệu này chỉ đặc tả hội thoại; khi Báo cáo hiệu suất Agent cần hiển thị cạnh nhau năng suất hội thoại và năng suất các đối tượng khác, phần đóng góp số liệu của các đối tượng đó do SRS tương ứng đặc tả (xem Mục 7).
- Trung tâm cuộc gọi thoại (Voice/Call Center) — hiện chưa là một kênh của Omnichat.

### 1.3 Đối tượng đọc

- Business Analyst / Product Owner: hiểu đúng hành vi hiện tại trước khi đề xuất thay đổi.
- QA: làm căn cứ viết test case chấp nhận.
- Trưởng nhóm/Giám sát viên contact center: hiểu năng lực vận hành thực tế của hệ thống (định tuyến, SLA, báo cáo) khi tư vấn quy trình cho khách hàng doanh nghiệp.
- Kỹ sư phát triển: hiểu doanh nghiệp và khách hàng cần gì trước khi bắt tay xây dựng — tài liệu này nói *phải làm gì*, phương án xây dựng do đội phát triển quyết định sau và không nằm trong phạm vi tài liệu.

### 1.4 Thuật ngữ & viết tắt

| Thuật ngữ | Giải thích |
| --- | --- |
| **Hội thoại (Conversation)** | Một phiên trao đổi tin nhắn giữa một khách hàng và doanh nghiệp, gắn với đúng một kênh. Có vòng đời riêng: Đang mở, Tạm hoãn, Chờ đóng, Đã giải quyết, Đã đóng. |
| **Kênh (Channel)** | Một kết nối cụ thể tới một nền tảng giao tiếp (một Trang Facebook, một số WhatsApp, một hộp thư email...), thuộc về đúng một doanh nghiệp (tenant). |
| **Hộp thư/Nhóm hội thoại (Inbox)** | Một nhóm định tuyến gộp nhiều kênh và một tập Agent/nhóm được phép xử lý, có thể có chính sách định tuyến/SLA/Bot riêng khác với mặc định của doanh nghiệp. |
| **Hồ sơ khách hàng tạm** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Lời mời nhận hội thoại** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Ưu tiên người phụ trách trước đó** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Cửa sổ phản hồi** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Ngưỡng an toàn đóng hàng loạt** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Cam kết thời gian phản hồi (SLA)** | Thời hạn nội bộ mà doanh nghiệp tự đặt ra cho việc phản hồi/giải quyết một hội thoại, dùng để đo và cảnh báo khi trễ hẹn — không phải cam kết hợp đồng với khách hàng. |
| **Leo thang (Escalation)** | Hành động tự động (cảnh báo, thông báo cấp quản lý, chuyển việc) được kích hoạt khi một hội thoại vi phạm SLA. |
| **Tự động đóng hội thoại (Auto-close)** | Cơ chế tự động chuyển một hội thoại không có hoạt động trong một khoảng thời gian sang trạng thái Đã giải quyết/Đã đóng, có cảnh báo trước cho khách hàng. |
| **Bot** | Kịch bản trả lời tự động (xây dựng ở một công cụ riêng ngoài Omnichat) có thể tiếp nhận và trả lời hội thoại thay Agent cho tới khi bàn giao lại cho người hoặc kết thúc. |
| **Bàn giao (Handoff)** | Thời điểm Bot chuyển quyền xử lý một hội thoại sang cho một Agent, một nhóm cụ thể, hoặc hàng đợi chung. |
| **CSAT (Customer Satisfaction)** | Điểm hài lòng (1–5) khách hàng tự chấm sau khi hội thoại được giải quyết. |
| **Mẫu tin nhắn nhanh (Canned Response)** | Nội dung soạn sẵn Agent có thể chèn nhanh vào hội thoại thay vì gõ lại từ đầu. |
| **Thời gian xử lý trung bình (AHT – Average Handle Time)** | Thời lượng trung bình một Agent dành để xử lý xong một hội thoại, tính từ lúc nhận tới lúc hoàn tất Xử lý sau hội thoại, dùng để hoạch định nhân sự. |
| **Thuộc tính kênh** | Tập đặc điểm nghiệp vụ mà mỗi kênh khai báo khi kết nối (đồng thời hay không đồng thời, có Cửa sổ phản hồi hay không, có tin nhắn mẫu phê duyệt trước hay không, định danh người gửi có được nền tảng xác minh hay không). Quy tắc nghiệp vụ trong tài liệu này luôn viện dẫn thuộc tính, không viện dẫn tên kênh — xem BR-01.8. |
| **Xử lý sau hội thoại (Wrap-up)** | Khoảng thời gian Agent hoàn tất phần việc còn lại của một hội thoại vừa kết thúc (chọn lý do xử lý, ghi chú, cập nhật hồ sơ khách hàng) trước khi sẵn sàng nhận việc mới — xem BR-06.5. |
| **Lý do xử lý (Disposition)** | Mã phân loại kết quả một hội thoại do doanh nghiệp tự định nghĩa, Agent chọn khi giải quyết. Là căn cứ phân tích nguyên nhân liên hệ trong báo cáo — xem FEAT-29. |
| **Nhãn (Tag)** | Từ khóa gắn thêm lên hội thoại để lọc, định tuyến, tự động hóa và phân tích. Khác Lý do xử lý ở chỗ nhãn gắn được nhiều và gắn được bất cứ lúc nào — xem FEAT-29. |
| **Kỹ năng (Skill)** | Năng lực cụ thể của một Agent (ngôn ngữ, dòng sản phẩm, cấp độ chuyên môn, thẩm quyền xử lý khiếu nại) dùng làm điều kiện định tuyến — xem FEAT-30. |
| **Phân khúc khách hàng** | Cách doanh nghiệp phân loại khách hàng theo giá trị hoặc theo cam kết phục vụ (ví dụ: Thường, Ưu tiên, VIP). Thuộc hồ sơ khách hàng trong CRM, Omnichat chỉ đọc để làm điều kiện định tuyến và cam kết SLA — xem BR-08.11. |
| **Tỷ lệ khách bỏ cuộc (Abandonment Rate)** | Tỷ lệ khách hàng chủ động rời đi trước khi có Agent nào tiếp nhận hội thoại của họ. Cùng với tỷ lệ đáp ứng SLA, đây là hai chỉ số cốt lõi đo năng lực phục vụ — xem BR-19.4. |
| **Ca trực** | Khoảng thời gian một Agent được xếp lịch phải trực. Khác với trạng thái làm việc (điều Agent tự chọn tại một thời điểm) — đối chiếu hai thứ này ra mức tuân thủ ca, xem FEAT-28. |
| **Tạm dừng xóa theo yêu cầu pháp lý (Legal Hold)** | Trạng thái đặt lên dữ liệu của một hội thoại/khách hàng đang trong diện tranh chấp hoặc điều tra, làm ngưng hiệu lực thời hạn lưu trữ cho tới khi được gỡ — xem BR-23.7. |
| **Định danh dùng chung** | Một số điện thoại hoặc email được đánh dấu là không thuộc riêng một cá nhân (số tổng đài đại lý, máy bàn văn phòng, email chung của một phòng ban). Không bao giờ được dùng làm căn cứ tự động gộp hồ sơ khách hàng — xem BR-02.4b. |
| **Thời hạn lưu trữ** | Khoảng thời gian doanh nghiệp cam kết giữ lại nội dung hội thoại và tệp đính kèm, sau đó dữ liệu được xóa tự động — xem FEAT-23. |
| **Giờ làm việc** | Lịch làm việc doanh nghiệp khai báo (theo ngày trong tuần, múi giờ, lịch nghỉ lễ), dùng làm căn cứ tính cam kết SLA, gửi thông báo ngoài giờ, và quyết định có nhận hội thoại mới vào hàng đợi hay không — xem FEAT-24. |

### 1.5 Tài liệu tham khảo

- [`CONTEXT.md`](../CONTEXT.md) — glossary thuật ngữ nghiệp vụ dùng chung cho các SRS trong `product-management`, mục "Omnichat".
- [ADR-0001](../docs/adr/0001-group-policy-conflict-resolution.md) — mô hình phân quyền trường hai chiều (mức truy cập và mức hiển thị giá trị), liên quan tới NFR-8 và Mục 7.2.
- [ADR-0003](../docs/adr/0003-permission-config-audit-log-fail-closed.md) — Nguyên tắc đóng và quy tắc ghi vết cho thay đổi cấu hình quyền, liên quan tới BR-15.5 và FEAT-25.

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

Khách hàng ngày nay liên hệ doanh nghiệp qua rất nhiều kênh khác nhau — Facebook, Instagram, WhatsApp, Zalo, TikTok, Telegram, email, hoặc khung chat ngay trên website — và kỳ vọng được phản hồi nhanh, nhất quán, dù họ chọn kênh nào. Nếu mỗi kênh phải xử lý ở một công cụ riêng, doanh nghiệp không thể đảm bảo tốc độ phản hồi, dễ bỏ sót tin nhắn, và không có cách nào đo lường chất lượng phục vụ một cách tổng thể.

Omnichat giải quyết vấn đề này bằng cách gộp mọi kênh giao tiếp vào **một nơi làm việc duy nhất cho Agent**, tự động phân phối hội thoại đến đúng người có khả năng xử lý, đo lường thời gian phản hồi và mức độ hài lòng của khách hàng, và tự động xử lý những phần việc lặp lại (trả lời ngoài giờ, đóng hội thoại đã xong việc, nhắc nhở khi sắp trễ hẹn) để Agent tập trung vào việc trò chuyện thực sự.

**Mục tiêu kinh doanh và chỉ số thành công.** Bốn mục tiêu dưới đây là căn cứ xếp thứ tự ưu tiên khi phải chọn giữa các yêu cầu trong tài liệu này; một yêu cầu không phục vụ mục tiêu nào trong số đó cần được chất vấn lại trước khi đưa vào kế hoạch.

| # | Mục tiêu | Đo bằng |
| --- | --- | --- |
| 1 | **Không khách hàng nào bị bỏ sót hoặc bị bỏ rơi giữa chừng** | Tỷ lệ hội thoại được phản hồi trong cam kết; tỷ lệ khách bỏ cuộc trước khi được phục vụ; số hội thoại quá hạn không ai phụ trách |
| 2 | **Một Agent phục vụ được nhiều khách hơn mà chất lượng không giảm** | Số hội thoại hoàn tất trên mỗi Agent mỗi ca; thời gian xử lý trung bình; điểm chất lượng và điểm CSAT đi kèm |
| 3 | **Giám sát viên điều hành được đội ngũ mà không cần chờ báo cáo cuối ngày** | Thời gian từ lúc phát sinh vi phạm tới lúc có người can thiệp; tỷ lệ vi phạm SLA được xử lý trước khi khách hàng phàn nàn |
| 4 | **Doanh nghiệp tự cấu hình được quy trình của mình mà không cần nhà cung cấp can thiệp** | Tỷ lệ doanh nghiệp tự đổi quy tắc định tuyến/SLA/tự động hóa trong 90 ngày đầu; số yêu cầu tùy chỉnh phải chuyển cho đội phát triển |

**Phân khúc doanh nghiệp mục tiêu.** Tài liệu này đặc tả một sản phẩm phục vụ đồng thời ba nhóm, theo thứ tự độ phức tạp vận hành tăng dần: doanh nghiệp nhỏ dùng vài kênh với một đội chung; doanh nghiệp tầm trung có nhiều đội/nhiều dòng sản phẩm, cần Hộp thư riêng, cam kết SLA riêng và báo cáo theo đội; và đơn vị dịch vụ khách hàng thuê ngoài (BPO) phục vụ nhiều khách hàng doanh nghiệp trên cùng một hệ thống, cần quản lý chất lượng, ca trực và báo cáo tách bạch theo từng khách hàng của họ. Mọi yêu cầu trong tài liệu PHẢI dùng được ở nhóm nhỏ nhất mà không bắt họ cấu hình những thứ không cần, và PHẢI mở rộng được tới nhóm lớn nhất mà không phải thay mô hình.

### 2.2 Các kênh giao tiếp được hỗ trợ

| Kênh | Đặc điểm nghiệp vụ |
| --- | --- |
| **Facebook** | Tin nhắn qua Trang Facebook (Messenger). |
| **Instagram** | Tin nhắn qua tài khoản doanh nghiệp Instagram, bao gồm cả nhắc tên trong story và chia sẻ bài viết. |
| **WhatsApp** | Tin nhắn qua số WhatsApp Business; danh tính khách hàng chính là số điện thoại của họ. |
| **Zalo** | Tin nhắn qua Official Account (OA); liên kết media của Zalo hết hạn nhanh hơn các kênh khác nên hệ thống tự sao lưu lại. |
| **TikTok** | Tin nhắn trực tiếp qua tài khoản doanh nghiệp TikTok. |
| **Telegram** | Tin nhắn qua bot Telegram của doanh nghiệp. |
| **Email** | Nhận/gửi qua hộp thư doanh nghiệp; không giới hạn thời gian phản hồi. |
| **Live Chat** | Khung chat nhúng trực tiếp trên website doanh nghiệp; không giới hạn thời gian phản hồi. |

Trừ Email và Live Chat, các kênh nhắn tin còn lại đều áp **Cửa sổ phản hồi** — giới hạn do chính nền tảng kênh đặt ra, không phải chính sách của doanh nghiệp. Độ dài cửa sổ khác nhau giữa các kênh và có thể được nền tảng thay đổi bất cứ lúc nào, nên tài liệu này không cố định một con số. Yêu cầu nghiệp vụ đặt ra là: hệ thống PHẢI luôn cho Agent thấy thời hạn còn lại **của đúng kênh đang xử lý**, và chặn/cho phép đúng theo giới hạn đang có hiệu lực của kênh đó (xem BR-12.2, BR-12.9, BR-21.4).

Sau khi hết cửa sổ, một số kênh cho phép chủ động liên hệ lại bằng tin nhắn mẫu đã được nền tảng phê duyệt trước; các kênh còn lại thì doanh nghiệp mất hoàn toàn khả năng chủ động liên hệ khách trên kênh đó — đây là một hệ quả nghiệp vụ thật (khách đang cần hỗ trợ mà không liên hệ lại được), được xử lý ở BR-12.9.

### 2.3 Vai trò người dùng (Actor)

| Actor | Vai trò trong module |
| --- | --- |
| **Khách hàng (Contact)** | Người nhắn tin qua một trong các kênh trên. Có thể là khách hàng đã có hồ sơ trong CRM, hoặc người lạ mới nhắn tin lần đầu (xem Hồ sơ khách hàng tạm). |
| **Agent** | Nhân viên trực tiếp trò chuyện, xử lý hội thoại: nhận việc, trả lời, chuyển tiếp, ghi chú, giải quyết/đóng hội thoại. |
| **Trưởng nhóm (Team Leader)** | Người trực tiếp điều hành một đội Agent trong ca: theo dõi hàng đợi của đội theo thời gian thực, nhắc bài cho Agent giữa chừng, gán lại việc trong nội bộ đội, xử lý bàn giao khi hết ca. Là cấp đầu tiên nhận cảnh báo leo thang. |
| **Giám sát viên (Supervisor)** | Người chịu trách nhiệm kết quả phục vụ trên nhiều đội/nhiều kênh: đặt và theo dõi cam kết SLA, điều phối tải giữa các đội, xem báo cáo tổng hợp, nhận leo thang khi Trưởng nhóm không xử lý kịp. Có mọi quyền của Trưởng nhóm trên phạm vi rộng hơn. |
| **Chuyên viên chất lượng (QA)** | Người chấm điểm chất lượng hội thoại theo phiếu chấm, phản hồi kết quả cho Agent, và phát hiện lỗi quy trình lặp lại. Không tham gia phục vụ khách hàng. |
| **Quản trị viên (Admin)** | Cấu hình kênh, hộp thư, quy tắc phân công và kỹ năng, chính sách SLA/leo thang/tự động đóng, giờ làm việc, danh mục lý do xử lý và nhãn, quy tắc tự động hóa, chính sách lưu trữ dữ liệu, mẫu tin nhắn. |
| **Bot** | Hệ thống trả lời tự động, tiếp nhận và xử lý hội thoại trước khi bàn giao cho người (nếu cần). Kịch bản Bot do doanh nghiệp xây ở một công cụ riêng; Omnichat chịu trách nhiệm về thời điểm gọi Bot, thời điểm bàn giao, và ranh giới quyền của Bot. |
| **Hệ thống bên ngoài** | Các hệ thống của doanh nghiệp trao đổi dữ liệu với Omnichat — hồ sơ khách hàng và phân khúc từ CRM, đơn hàng, hệ thống ticket. Omnichat là bên đọc/ghi có kiểm soát, không phải nơi sở hữu các dữ liệu đó. |

### 2.4 Nhóm tính năng

1. Kết nối kênh giao tiếp
2. Tiếp nhận & nhận diện khách hàng
3. Vòng đời hội thoại
4. Tự động phân công hội thoại
5. Hàng đợi & lời mời nhận hội thoại
6. Trạng thái làm việc của Agent
7. Chuyển tiếp hội thoại
8. Cam kết thời gian phản hồi (SLA)
9. Leo thang khi vi phạm SLA
10. Tự động đóng hội thoại không hoạt động
11. Bot trả lời tự động & bàn giao cho Agent
12. Gửi tin nhắn cho khách hàng
13. Không bỏ lỡ việc và không trùng việc
14. Khảo sát hài lòng khách hàng (CSAT)
15. Ghi chú, biểu tượng phản hồi nhanh & lịch sử xử lý
16. Tìm kiếm nội dung tin nhắn
17. Quản lý hộp thư (Inbox)
18. Mẫu tin nhắn nhanh (Canned Response)
19. Báo cáo vận hành Omnichannel
20. Báo cáo hiệu suất Agent
21. Giao diện xử lý hội thoại của Agent
22. Trải nghiệm trò chuyện của khách hàng trên Live Chat Widget
23. Lưu trữ, xuất và xóa dữ liệu hội thoại
24. Giờ làm việc & lịch nghỉ
25. Nhật ký thay đổi cấu hình Omnichat
26. Giám sát và can thiệp thời gian thực
27. Quản lý chất lượng hội thoại (QA)
28. Ca trực, tuân thủ ca & bàn giao ca
29. Danh mục lý do xử lý & quản trị nhãn
30. Kỹ năng & định tuyến theo kỹ năng
31. Chặn spam & xử lý lạm dụng
32. Yêu cầu liên hệ lại
33. Quy tắc tự động hóa
34. Chế độ thử nghiệm cấu hình
35. Nhật ký truy cập dữ liệu khách hàng
36. Trao đổi nhiều bên trên kênh Email

---

## 3. Đặc tả yêu cầu chức năng

### FEAT-01 — Kết nối kênh giao tiếp `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép quản trị viên kết nối tài khoản doanh nghiệp trên từng nền tảng vào hệ thống, để bắt đầu tiếp nhận và trả lời tin nhắn của khách hàng qua kênh đó. Mỗi kênh khi kết nối PHẢI khai báo **Thuộc tính kênh** của mình — đây là căn cứ để mọi quy tắc nghiệp vụ khác trong tài liệu áp dụng đúng, không cần biết tên kênh.

**Actor:** Quản trị viên.

**Luồng chính:**

1. Quản trị viên chọn loại kênh muốn kết nối.
2. Quản trị viên chứng minh doanh nghiệp có quyền sử dụng tài khoản đó trên nền tảng kênh — bằng cách đăng nhập xác thực với nền tảng và chọn đúng tài khoản/trang doanh nghiệp, hoặc bằng cách khai báo thông tin kết nối do nền tảng cấp, tùy cách nền tảng đó cho phép.
3. Hệ thống ghi nhận Thuộc tính kênh tương ứng (xem BR-01.8).
4. Hệ thống xác nhận kết nối thành công, kênh bắt đầu phục vụ được khách hàng, và quản trị viên nhìn thấy rõ điều đó.

**Quy tắc nghiệp vụ:**

- BR-01.1: Một kênh PHẢI thuộc đúng một doanh nghiệp tại một thời điểm — không được kết nối cùng một tài khoản kênh cho hai doanh nghiệp khác nhau.
- BR-01.2: Quản trị viên PHẢI luôn biết được một kênh **hiện có phục vụ được khách hàng hay không**, và nếu không thì nguyên nhân thuộc nhóm nào cùng việc cần làm để khôi phục (chưa hoàn tất kết nối, doanh nghiệp chủ động ngắt, hay đang gặp trục trặc). Kênh KHÔNG ĐƯỢC ở trạng thái mơ hồ mà quản trị viên phải tự suy đoán.
- BR-01.3: Hệ thống PHẢI tự động kiểm tra định kỳ tình trạng hoạt động của từng kênh đang kết nối, và cảnh báo cho quản trị viên khi kênh mất kết nối hoặc quyền truy cập sắp/đã hết hạn.
- BR-01.4: Ngắt kết nối một kênh PHẢI là thao tác có thể phục hồi (kết nối lại); xóa hẳn một kênh PHẢI yêu cầu ngắt kết nối trước.
- BR-01.5: Quản trị viên PHẢI cấu hình được theo từng kênh: tin nhắn tự động trả lời, giờ làm việc riêng (nếu khác giờ làm việc chung), và quy tắc phân công mặc định riêng cho kênh đó.
- BR-01.6: Với kênh Email, quản trị viên PHẢI cấu hình được riêng thông tin gửi/nhận thư.
- BR-01.7: Thông tin xác thực đăng nhập của từng kênh PHẢI được bảo mật, không bao giờ hiển thị lại cho quản trị viên sau khi đã lưu.
- BR-01.8 `[Yêu cầu mới]`: Mỗi kênh khi kết nối PHẢI khai báo được **Thuộc tính kênh** của mình, tối thiểu gồm: (a) trò chuyện **đồng thời** (khách hàng đang chờ ngay trên màn hình) hay **không đồng thời** (khách hàng không nhất thiết chờ); (b) có áp **Cửa sổ phản hồi** hay không; (c) có cho phép chủ động liên hệ lại bằng **tin nhắn mẫu đã phê duyệt trước** hay không; (d) **định danh người gửi có được chính nền tảng xác minh là của một cá nhân** hay không; (e) có hỗ trợ **tin nhắn dạng nút bấm** hay không và tối đa bao nhiêu lựa chọn. Mọi quy tắc nghiệp vụ khác trong tài liệu này PHẢI viện dẫn thuộc tính, KHÔNG ĐƯỢC viện dẫn tên kênh cụ thể — để một kênh mới chỉ cần khai báo thuộc tính là chạy đúng, không phải sửa quy tắc và không phải sửa tài liệu.

**Tiêu chí chấp nhận:**

- Kênh mới kết nối phục vụ được khách hàng ngay, và quản trị viên nhìn thấy rõ tình trạng đó.
- Kênh mất kết nối/hết hạn quyền truy cập luôn tạo cảnh báo cho quản trị viên, không âm thầm ngừng hoạt động.
- Thêm một loại kênh mới chỉ cần khai báo Thuộc tính kênh; không quy tắc nghiệp vụ nào trong tài liệu phải sửa theo.

**Tham chiếu:** BR-01.8 → issue [#71](https://github.com/crmsaassaudi/product-management/issues/71).

---

### FEAT-02 — Tiếp nhận & nhận diện khách hàng qua các kênh `[Đã triển khai]`

**Mô tả nghiệp vụ:** Nhận tin nhắn khách hàng gửi tới từ bất kỳ kênh nào đang kết nối, hiển thị thống nhất trong một khung chat, và xác định đúng khách hàng đó là ai trong hệ thống CRM (nếu đã có hồ sơ).

**Actor:** Khách hàng (nguồn tin nhắn); hệ thống thực hiện tự động; Agent là người nhìn thấy kết quả nhận diện và có thể can thiệp.

**Luồng chính:**

1. Khách hàng gửi tin nhắn (văn bản, ảnh, video, tệp, vị trí, nhãn dán, hoặc bấm một nút tương tác) qua một kênh đang kết nối.
2. Hệ thống xác thực tin nhắn thực sự đến từ đúng nền tảng kênh đó (chống giả mạo).
3. Hệ thống chuẩn hóa nội dung về định dạng hiển thị thống nhất.
4. Hệ thống tìm kiếm khách hàng đã có hồ sơ khớp với người gửi (theo liên kết đã lưu từ trước, hoặc theo email/số điện thoại tùy kênh).
5. Nếu tìm thấy, gắn tin nhắn vào đúng hồ sơ khách hàng đó; nếu không, tạo một Hồ sơ khách hàng tạm.
6. Tin nhắn được gắn vào hội thoại đang mở của khách hàng đó, hoặc tạo hội thoại mới nếu chưa có.

**Quy tắc nghiệp vụ:**

- BR-02.1: Hệ thống PHẢI xác thực mọi tin nhắn đến thực sự từ đúng nền tảng kênh trước khi xử lý, để chặn tin nhắn giả mạo.
- BR-02.2: Một tin nhắn khách hàng gửi một lần PHẢI chỉ xuất hiện đúng một lần trong khung chat của Agent và chỉ được tính đúng một lần trong mọi báo cáo, kể cả khi hệ thống nhận được nhiều bản sao của cùng tin nhắn đó.
- BR-02.3 `[Yêu cầu mới]`: Hệ thống chỉ được **tự động** gộp một Hồ sơ khách hàng tạm vào một hồ sơ khách hàng đã có khi thỏa đồng thời hai điều kiện: (a) kênh đó khai báo **định danh người gửi được nền tảng xác minh là của một cá nhân** (Thuộc tính kênh, BR-01.8), chứ không phải do khách tự khai; và (b) định danh đó chưa được đánh dấu là **Định danh dùng chung**. Chuẩn rủi ro áp dụng thống nhất cho mọi kênh: gộp nhầm hai người thành một khách hàng là hậu quả không chấp nhận được, nên điều kiện (a) là điều kiện *cần*, không phải điều kiện *đủ*.
- BR-02.4 `[Yêu cầu mới]`: Với mọi trường hợp không thỏa BR-02.3, hệ thống KHÔNG ĐƯỢC tự động gộp Hồ sơ khách hàng tạm vào một hồ sơ có sẵn chỉ vì trùng số điện thoại/email. Thay vào đó, hệ thống PHẢI hiển thị gợi ý khớp cho Agent (ví dụ: "Có vẻ đây là [Tên khách hàng] đã có trong hệ thống") và chỉ gộp khi Agent xác nhận. Lý do: số điện thoại/email trùng nhau trên các kênh này không đủ tin cậy — có thể là số hotline dùng chung, email của một công ty đối tác.
- BR-02.4b `[Yêu cầu mới]`: Agent hoặc Quản trị viên PHẢI đánh dấu được một số điện thoại/email là **Định danh dùng chung** (số tổng đài của đại lý, số máy bàn văn phòng, email chung của một phòng ban). Từ thời điểm được đánh dấu, định danh đó không bao giờ được dùng để tự động gộp trên bất kỳ kênh nào, kể cả kênh có định danh được nền tảng xác minh — chỉ hiển thị gợi ý cho Agent. Doanh nghiệp CÓ THỂ tắt hẳn tự động gộp cho toàn hệ thống nếu mô hình kinh doanh của họ có nhiều khách dùng chung một số.
- BR-02.5: Khi chưa nhận diện được khách hàng nào khớp, hệ thống PHẢI tự tạo một Hồ sơ khách hàng tạm để hội thoại có nơi gắn vào; Agent CÓ THỂ tự liên kết hội thoại đó với một hồ sơ CRM có sẵn, hoặc tạo hồ sơ CRM chính thức mới, bất kỳ lúc nào trong lúc trò chuyện.
- BR-02.6: Toàn bộ nội dung khách hàng gửi, bao gồm tệp đính kèm (ảnh/video/tài liệu), PHẢI xem lại được đầy đủ trong suốt **thời hạn lưu trữ** mà doanh nghiệp đã cam kết (xem FEAT-23), độc lập với việc nền tảng kênh còn giữ nội dung đó hay không.
- BR-02.7: Nếu doanh nghiệp đang ngoài giờ làm việc (xem FEAT-24) và chưa có Agent nào xử lý hội thoại, hệ thống CÓ THỂ tự động gửi tin nhắn thông báo ngoài giờ cho khách hàng, theo cấu hình của doanh nghiệp/kênh.
- BR-02.9 `[Yêu cầu mới]`: Khi một khách hàng đang có nhiều hội thoại mở cùng lúc trên nhiều kênh khác nhau, hệ thống PHẢI cho Agent đang xử lý một trong số đó nhìn thấy các hội thoại song song còn lại của cùng khách hàng đó và ai đang phụ trách chúng, và PHẢI cảnh báo cho Trưởng nhóm. Lý do: cùng một người được hai Agent phục vụ song song mà không ai biết là một sự cố với khách hàng, đồng thời làm sai lệch khối lượng công việc và điểm CSAT. Hệ thống hiện chưa hợp nhất các hội thoại này thành một mạch duy nhất (xem Mục 7) — quy tắc này là mức bảo vệ tối thiểu bắt buộc trong lúc chưa hợp nhất.
- BR-02.8 `[Yêu cầu mới]`: Mọi thao tác gộp hồ sơ khách hàng PHẢI gỡ lại được. Khi gỡ, hai hồ sơ trở lại độc lập và mỗi hội thoại PHẢI quay về đúng hồ sơ mà nó phát sinh, không để tin nhắn của người này nằm lại trong hồ sơ của người kia. Người thực hiện gộp và người thực hiện gỡ PHẢI được ghi lại trên hồ sơ khách hàng.

**Tiêu chí chấp nhận:**

- Tin nhắn từ mọi kênh đang kết nối đều hiển thị được trong cùng một khung chat thống nhất cho Agent.
- Gửi lại một tin nhắn do lỗi mạng không tạo ra tin nhắn trùng lặp trên khung chat.
- Khách hàng trên kênh có định danh được nền tảng xác minh luôn được nhận diện đúng là cùng một người khi nhắn lại.
- Khách hàng nhắn qua kênh không có định danh xác minh, dù trùng số điện thoại với một hồ sơ có sẵn, **không** tự động gộp — chỉ hiển thị gợi ý cho Agent xác nhận.
- Số điện thoại đã đánh dấu là Định danh dùng chung không bao giờ tự động gộp, kể cả trên kênh có định danh xác minh.
- Một khách hàng nhắn đồng thời trên hai kênh thì cả hai Agent đều nhìn thấy hội thoại song song của người kia, không ai phục vụ trong mù.
- Một lần gộp nhầm gỡ lại được, và sau khi gỡ mỗi hội thoại nằm đúng hồ sơ ban đầu của nó.

**Tham chiếu:** BR-02.4 → issue [#15](https://github.com/crmsaassaudi/product-management/issues/15). BR-02.3, BR-02.4b, BR-02.8, BR-02.9 → issue [#62](https://github.com/crmsaassaudi/product-management/issues/62).

---

### FEAT-03 — Vòng đời hội thoại `[Đã triển khai]`

**Mô tả nghiệp vụ:** Mỗi hội thoại có một trạng thái rõ ràng phản ánh nó đang cần xử lý hay đã xong, giúp Agent và Giám sát viên biết việc gì cần làm và tránh bỏ sót.

**Actor:** Agent (thay đổi trạng thái thủ công); hệ thống (thay đổi trạng thái tự động theo quy tắc).

**Các trạng thái chuẩn:** Đang mở, Tạm hoãn, Chờ đóng (đang trong thời gian ân hạn trước khi tự động đóng), Đã giải quyết, Đã đóng. Doanh nghiệp CÓ THỂ bổ sung trạng thái chờ của riêng mình theo BR-03.8.

**Luồng chính:**

1. Hội thoại mới của một khách hàng bắt đầu ở trạng thái Đang mở.
2. Agent xử lý xong, đánh dấu Đã giải quyết (hoặc hệ thống tự động đóng — xem FEAT-10).
3. Nếu khách hàng nhắn tin lại: hội thoại đã Đã giải quyết được mở lại (nếu còn trong thời hạn cho phép) hoặc một hội thoại mới được tạo; hội thoại đã Đã đóng luôn tạo hội thoại mới.

**Quy tắc nghiệp vụ:**

- BR-03.1: Hội thoại mới của một khách hàng PHẢI bắt đầu ở trạng thái Đang mở.
- BR-03.2: Khi khách hàng nhắn tin lại vào một hội thoại Đã giải quyết, hệ thống PHẢI mở lại đúng hội thoại đó nếu còn trong khoảng thời gian cho phép mở lại (theo chính sách tự động đóng đã áp dụng cho hội thoại đó), hoặc tạo một hội thoại mới nếu đã quá hạn hoặc chính sách quy định luôn tạo hội thoại mới.
- BR-03.3: Khách hàng nhắn tin lại vào một hội thoại Đã đóng PHẢI luôn tạo một hội thoại mới — không bao giờ mở lại hội thoại đã đóng.
- BR-03.4: Khi một hội thoại được mở lại, một **chu kỳ giải quyết mới** bắt đầu: thông tin giải quyết của chu kỳ trước (người giải quyết, lý do xử lý, ghi chú) KHÔNG ĐƯỢC xóa mà PHẢI giữ nguyên trong lịch sử hội thoại, vì đó là căn cứ của chỉ số tỷ lệ mở lại và của việc đánh giá chất lượng xử lý. Màn hình làm việc chỉ hiển thị chu kỳ đang mở, không hiển thị lẫn kết quả của chu kỳ đã khép lại. Hệ thống tự động tìm Agent xử lý nếu hội thoại chưa có ai đang phụ trách.
- BR-03.5 `[Yêu cầu mới]`: Hệ thống PHẢI lưu lại lý do cụ thể ngay trên chính hội thoại mỗi khi nó chuyển sang trạng thái Tạm hoãn (ví dụ: Agent tự tạm ẩn, ngoài giờ làm việc, hủy chờ đóng do không có hoạt động mới), để Agent và Giám sát viên tra cứu và thống kê được ngay từ hội thoại đó, không phải hỏi lại người đã thao tác.
- BR-03.6: Mọi tin nhắn trong một hội thoại PHẢI được hiển thị đúng theo thứ tự thời gian thực tế phát sinh, kể cả khi một tin nhắn đến muộn hơn do lỗi mạng.
- BR-03.7 `[Yêu cầu mới]`: Khi khách hàng nhắn tin vào một hội thoại đang Tạm hoãn, hội thoại PHẢI tự động trở lại Đang mở, hiện lại trong danh sách việc của người phụ trách, và các cam kết thời gian phản hồi tiếp tục chạy theo BR-08.7 — khách hàng không phải chờ tới khi Agent nhớ ra hội thoại đó.
- BR-03.8 `[Yêu cầu mới]`: Doanh nghiệp PHẢI định nghĩa thêm được **trạng thái chờ của riêng mình** phản ánh đúng quy trình của họ (ví dụ: chờ khách bổ sung chứng từ, chờ bộ phận kỹ thuật, chờ duyệt hoàn tiền). Mỗi trạng thái tự định nghĩa PHẢI khai báo nó tương đương với trạng thái chuẩn nào, để cam kết SLA, quy tắc tự động đóng và báo cáo hiểu đúng — hệ thống KHÔNG ĐƯỢC để một trạng thái tự định nghĩa nằm ngoài mọi phép đo. Lý do: mỗi doanh nghiệp có một quy trình chờ khác nhau, và ép tất cả vào đúng một trạng thái Tạm hoãn khiến báo cáo không nói được **đang chờ ai**.

**Tiêu chí chấp nhận:**

- Khách nhắn lại sau khi hội thoại "Đã giải quyết", trong hạn mở lại → tiếp tục đúng hội thoại cũ.
- Khách nhắn lại sau khi hội thoại "Đã đóng", hoặc đã quá hạn mở lại → luôn là hội thoại mới.
- Khách nhắn vào hội thoại đang Tạm hoãn thì hội thoại tự trở lại danh sách việc của người phụ trách ngay.
- Hội thoại mở lại vẫn tra cứu được ai đã giải quyết nó ở chu kỳ trước và với lý do gì.
- Lý do tạm hoãn tra cứu được ngay trên hội thoại, không phải hỏi lại người đã thao tác.

**Tham chiếu:** BR-03.5 → issue [#16](https://github.com/crmsaassaudi/product-management/issues/16). BR-03.4, BR-03.7, BR-03.8 → issue [#73](https://github.com/crmsaassaudi/product-management/issues/73).

---

### FEAT-04 — Tự động phân công hội thoại `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hệ thống tự động chọn đúng Agent phù hợp nhất để xử lý một hội thoại mới, dựa trên năng lực hỗ trợ kênh, tải công việc hiện tại, và quy tắc riêng doanh nghiệp tự định nghĩa — đảm bảo khách hàng được phục vụ nhanh nhất có thể mà không cần Agent tự tìm việc.

**Actor:** Hệ thống (tự động thực hiện); Quản trị viên (cấu hình quy tắc); Agent (người nhận việc).

**Luồng chính:**

1. Có hội thoại mới cần phân công (hoặc cần gán lại do Agent trước đó mất kết nối).
2. Hệ thống đánh giá các quy tắc phân công doanh nghiệp đã cấu hình, theo đúng thứ tự ưu tiên.
3. Xác định nhóm/Agent đích theo quy tắc khớp đầu tiên, hoặc theo cấu hình mặc định nếu không quy tắc nào khớp.
4. Lọc trong số đó những Agent đang sẵn sàng, có đủ kỹ năng mà hội thoại đòi hỏi (xem FEAT-30), đủ điều kiện hỗ trợ đúng kênh của hội thoại, và còn khả năng nhận thêm việc.
5. Chọn một Agent theo chiến lược đã cấu hình, rồi gửi lời mời nhận hội thoại (xem FEAT-05).

**Quy tắc nghiệp vụ:**

- BR-04.1: Hệ thống PHẢI chỉ chọn Agent đang ở trạng thái sẵn sàng nhận việc và còn khả năng nhận thêm (chưa đạt giới hạn năng lực theo BR-04.7) — quy tắc này áp dụng cho **mọi chiến lược phân công** dùng cho hội thoại, kể cả các chiến lược được bổ sung về sau, không có ngoại lệ.
- BR-04.2 (Ưu tiên người phụ trách trước đó): Khách hàng cũ quay lại PHẢI được ưu tiên gán cho đúng Agent đã phụ trách họ gần đây, nếu Agent đó còn khả năng nhận thêm việc; nếu Agent đó không còn khả năng nhận thêm hoặc đã quá lâu không tương tác, hệ thống chuyển sang các chiến lược phân công thông thường.
- BR-04.3: Doanh nghiệp PHẢI cấu hình được quy tắc phân công theo điều kiện tùy chỉnh, có thứ tự ưu tiên rõ ràng. Tập điều kiện tối thiểu PHẢI gồm: kênh và Thuộc tính kênh, nội dung tin nhắn, **ngôn ngữ khách hàng đang dùng**, nhãn gắn trên hội thoại, phân khúc khách hàng, kỹ năng mà hội thoại đòi hỏi, Hộp thư, và trong/ngoài giờ làm việc. Một quy tắc không có điều kiện nào PHẢI được coi là quy tắc mặc định, luôn khớp nếu không quy tắc nào khác khớp trước. Tập điều kiện này PHẢI dùng chung với Quy tắc tự động hóa (FEAT-33), không định nghĩa hai bộ điều kiện khác nhau cho cùng một khái niệm.
- BR-04.4: Doanh nghiệp PHẢI chọn được cách hệ thống chọn Agent trong số các ứng viên đủ điều kiện; tối thiểu PHẢI có bốn cách: theo vòng lần lượt, người đang ít việc nhất, theo tải trọng số công việc, và chỉ đẩy vào hàng đợi chờ Agent tự nhận.
- BR-04.5: Nếu không có Agent nào đủ điều kiện tại thời điểm phân công, hội thoại PHẢI được đưa vào hàng đợi chờ (xem FEAT-05) — không được bỏ sót không xử lý gì.
- BR-04.6: Mọi quyết định phân công (thành công hay không, và vì lý do gì) PHẢI được ghi lại đầy đủ để tra cứu sau này; Agent nhận việc PHẢI xem được ngay trên hội thoại **vì sao việc này tới tay mình** (khớp quy tắc nào, do Bot bàn giao, do ưu tiên người phụ trách trước đó, hay do tự nhận từ hàng đợi).
- BR-04.7 `[Yêu cầu mới]`: Năng lực nhận việc của một Agent PHẢI tính theo **điểm tải có trọng số theo Thuộc tính kênh**, không theo số hội thoại phẳng: doanh nghiệp cấu hình mỗi loại kênh chiếm bao nhiêu điểm tải, và tổng điểm tải tối đa của một Agent. Lý do: năm phiên trò chuyện đồng thời là quá tải, trong khi năm hội thoại không đồng thời là bình thường — dùng một con số phẳng thì hoặc là bỏ phí năng lực, hoặc là làm Agent quá tải mà hệ thống không biết. Hội thoại đang ở trạng thái chờ khách phản hồi CÓ THỂ được cấu hình chiếm ít điểm tải hơn hội thoại đang trao đổi.

**Tiêu chí chấp nhận:**

- Hội thoại mới luôn được gán cho một Agent hoặc đưa vào hàng đợi — không bao giờ "biến mất" mà không ai biết.
- Khách hàng quen quay lại được ưu tiên gặp đúng Agent cũ nếu Agent đó còn khả năng nhận thêm việc.
- Một Agent đang giữ ba phiên trò chuyện đồng thời không nhận thêm việc, trong khi một Agent giữ ba hội thoại không đồng thời vẫn nhận thêm được.
- Agent mở một hội thoại vừa nhận là biết ngay vì sao việc đó tới tay mình.
- Lịch sử phân công của từng hội thoại tra cứu được đầy đủ.

**Tham chiếu:** BR-04.7 → issue [#72](https://github.com/crmsaassaudi/product-management/issues/72).

---

### FEAT-05 — Hàng đợi & lời mời nhận hội thoại `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi một hội thoại chưa có Agent xử lý ngay, hệ thống xếp vào hàng đợi và chủ động mời từng Agent phù hợp lần lượt, thay vì để hội thoại nằm im chờ ai đó tình cờ nhìn thấy.

**Actor:** Hệ thống (tự động điều phối); Agent (nhận/từ chối lời mời, hoặc tự chọn nhận từ hàng đợi); Giám sát viên (theo dõi, can thiệp khi cần).

**Quy tắc nghiệp vụ:**

- BR-05.1: Hội thoại trong hàng đợi PHẢI được sắp xếp theo độ ưu tiên và thời gian chờ; hội thoại chờ càng lâu PHẢI được tự động tăng dần độ ưu tiên, để không bị "chìm" phía cuối hàng đợi.
- BR-05.2: Khi có Agent phù hợp đang rảnh, hệ thống PHẢI gửi Lời mời nhận hội thoại tới đúng một Agent tại một thời điểm; Agent có một khoảng thời gian nhất định để nhận hoặc từ chối lời mời đó.
- BR-05.3: Agent bỏ lỡ hoặc từ chối lời mời PHẢI được loại khỏi lượt mời kế tiếp cho đúng hội thoại đó (trong cùng vòng mời), và hội thoại PHẢI tiếp tục được mời cho Agent phù hợp khác; sau số lượt mời không thành công do doanh nghiệp cấu hình, hội thoại quay lại chờ ở hàng đợi. Ràng buộc nghiệp vụ thật nằm ở phía khách hàng chứ không ở số lượt mời: **tổng thời gian một khách hàng phải chờ trước khi có người nhận** PHẢI không vượt quá ngưỡng doanh nghiệp cam kết; chạm ngưỡng thì áp dụng BR-05.6.
- BR-05.4: Doanh nghiệp CÓ THỂ chọn một trong ba cách phân phối hội thoại đang chờ: tự động gán thẳng (không cần Agent xác nhận), gửi lời mời để Agent tự nhận/từ chối (mặc định), hoặc chỉ hiển thị trong hàng đợi để Agent chủ động chọn nhận.
- BR-05.5: Agent CÓ THỂ chủ động tự nhận một hội thoại đang chờ trong hàng đợi, miễn còn đủ điều kiện (đúng kênh được phép hỗ trợ, còn khả năng nhận thêm việc).
- BR-05.6: Khi một hội thoại chờ quá lâu vượt ngưỡng cấu hình, hệ thống PHẢI cảnh báo cho Giám sát viên, và CÓ THỂ tự động chuyển hội thoại đó sang một nhóm khác đang rảnh hơn.
- BR-05.7 `[Yêu cầu mới]`: Agent PHẢI có đủ thông tin để chọn việc đúng thứ tự ưu tiên **trước khi** nhận. Với mỗi hội thoại đang chờ trong hàng đợi, Agent thường PHẢI thấy: kênh, khách hàng (hoặc Hồ sơ khách hàng tạm), nhãn, thời gian đã chờ, tình trạng cam kết SLA, và trích đoạn ngắn tin nhắn đầu tiên của khách. Toàn bộ nội dung hội thoại chỉ mở ra sau khi Agent nhận; Giám sát viên xem được đầy đủ mọi lúc. Tình huống hai Agent cùng chọn một hội thoại được xử lý ở BR-13.3 — không xử lý bằng cách giấu thông tin khỏi Agent, vì như vậy Agent chỉ còn cách nhận việc một cách ngẫu nhiên.
- BR-05.8: Ngoài giờ làm việc (xem FEAT-24), nếu doanh nghiệp cấu hình không nhận hội thoại mới vào hàng đợi, hệ thống PHẢI xử lý theo kịch bản ngoài giờ (thông báo tự động và Yêu cầu liên hệ lại theo FEAT-32) thay vì tạo hàng đợi chờ.
- BR-05.9 `[Yêu cầu mới]`: Khách hàng đang chờ trong hàng đợi PHẢI được cho biết mình còn phải chờ bao lâu — bằng thời gian chờ dự kiến hoặc vị trí trong hàng đợi, theo cấu hình của doanh nghiệp. Doanh nghiệp CÓ THỂ tắt hiển thị này, nhưng khi đã tắt thì PHẢI thay bằng một thông điệp xác nhận đã tiếp nhận. Lý do: chờ trong im lặng hoàn toàn là nguyên nhân hàng đầu khiến khách hàng bỏ đi, và một khách bỏ đi tốn kém hơn một khách phải chờ có thông báo.
- BR-05.10 `[Yêu cầu mới]`: Khi một khách hàng rời đi trước lúc có Agent tiếp nhận, hệ thống PHẢI ghi nhận đó là một lần **bỏ cuộc** kèm thời gian khách đã chờ, để đo được theo BR-19.4 — không được lặng lẽ đóng hội thoại như một hội thoại không hoạt động bình thường.

**Tiêu chí chấp nhận:**

- Hội thoại chờ lâu tự động tăng ưu tiên, không bị bỏ quên.
- Lời mời bị bỏ lỡ luôn được chuyển tiếp cho Agent khác, không treo vô thời hạn.
- Khách hàng chờ trong hàng đợi luôn nhận được ít nhất một thông điệp cho biết đã được tiếp nhận và còn phải chờ bao lâu.
- Khách hàng bỏ đi giữa lúc chờ được đếm riêng, không lẫn với hội thoại đã phục vụ xong.
- Agent thường thấy đủ thông tin tóm tắt để chọn đúng việc gấp trong hàng đợi, nhưng chỉ đọc được toàn bộ nội dung sau khi đã nhận.

**Tham chiếu:** BR-05.7 → issue [#63](https://github.com/crmsaassaudi/product-management/issues/63). BR-05.9, BR-05.10 → issue [#76](https://github.com/crmsaassaudi/product-management/issues/76).

---

### FEAT-06 — Trạng thái làm việc của Agent `[Đã triển khai]`

**Mô tả nghiệp vụ:** Theo dõi Agent nào đang thực sự sẵn sàng nhận việc, để hệ thống chỉ phân công hội thoại cho người có thể xử lý ngay, và cung cấp số liệu cho báo cáo thời gian làm việc.

**Actor:** Agent.

**Quy tắc nghiệp vụ:**

- BR-06.1: Agent PHẢI tự chọn trạng thái làm việc của mình (ví dụ: Sẵn sàng, Vắng mặt, Nghỉ giải lao, Đang họp, Đang đào tạo); trạng thái Ngoại tuyến chỉ do hệ thống tự đặt khi phát hiện Agent mất kết nối, và trạng thái **Xử lý sau hội thoại** do hệ thống tự đặt theo BR-06.5. Doanh nghiệp PHẢI bổ sung được trạng thái của riêng mình và khai báo mỗi trạng thái có được nhận việc mới hay không, có được tính là thời gian làm việc hay không.
- BR-06.2: Agent chỉ nhận được hội thoại mới khi đồng thời thỏa cả ba điều kiện: đang chọn trạng thái Sẵn sàng, đang thực sự kết nối vào hệ thống, và chưa đạt giới hạn số hội thoại tối đa được xử lý cùng lúc.
- BR-06.3: Hệ thống chỉ được tự đặt một Agent sang Ngoại tuyến khi đã đủ căn cứ người đó thực sự dừng làm việc, không phải vì một gián đoạn thoáng qua; khoảng chờ trước khi kết luận do doanh nghiệp cấu hình. Trong khoảng chờ đó, hội thoại đang xử lý PHẢI giữ nguyên người phụ trách, và Agent quay lại trong khoảng chờ PHẢI tiếp tục được đúng công việc đang dở.
- BR-06.4: Mọi lần đổi trạng thái làm việc của Agent PHẢI được ghi lại, phục vụ báo cáo thời gian làm việc theo ngày (xem FEAT-20) và đo tuân thủ ca (xem FEAT-28).
- BR-06.5 `[Yêu cầu mới]`: Khi một Agent kết thúc một hội thoại, doanh nghiệp PHẢI cấu hình được một khoảng **Xử lý sau hội thoại** để Agent hoàn tất chọn lý do xử lý, ghi chú và cập nhật hồ sơ khách hàng trước khi nhận việc mới. Trong khoảng đó Agent KHÔNG ĐƯỢC nhận việc mới, và thời gian đó PHẢI được tính vào thời gian xử lý trung bình. Agent PHẢI kết thúc sớm được khoảng này khi đã xong. Lý do: nếu không có khoảng này, việc mới ập tới ngay khi vừa đóng hội thoại cũ, Agent bỏ qua phần ghi nhận và mọi chỉ số phân tích nguyên nhân liên hệ mất giá trị.

**Tiêu chí chấp nhận:**

- Agent đặt trạng thái "Vắng mặt" thì không nhận được hội thoại mới nữa.
- Agent bị gián đoạn ngắn rồi quay lại vẫn giữ nguyên các hội thoại đang xử lý dở, không bị chuyển sang người khác.
- Agent vừa đóng một hội thoại không bị đẩy việc mới ngay, và thời gian ghi nhận đó xuất hiện trong AHT.

**Tham chiếu:** BR-06.5 → issue [#72](https://github.com/crmsaassaudi/product-management/issues/72).

---

### FEAT-07 — Chuyển tiếp hội thoại `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép Agent chuyển một hội thoại đang xử lý sang một Agent khác hoặc một nhóm khác, khi cần chuyên môn khác, đổi ca làm việc, hoặc muốn xin ý kiến đồng nghiệp mà không rời khỏi hội thoại.

**Actor:** Agent (nguồn và đích); Giám sát viên (có thể khởi tạo/nhận thay).

**Luồng chính:**

1. Agent đang phụ trách chọn "Chuyển tiếp", chọn Agent cụ thể hoặc một nhóm, chọn kiểu chuyển tiếp: **Chuyển hẳn** (không cần bên kia xác nhận, dùng khi chuyển cho một nhóm hoặc do Giám sát viên thực hiện), **Chuyển có xác nhận** (bên nhận phải đồng ý mới chính thức đổi người phụ trách), hoặc **Xin ý kiến** (mời một đồng nghiệp cùng xem/tư vấn, người chuyển vẫn giữ hội thoại).
2. Với "Chuyển có xác nhận"/"Xin ý kiến": Agent đích nhận được yêu cầu, có thời hạn để đồng ý hoặc từ chối.
3. Nếu đồng ý, quyền phụ trách hội thoại (hoặc quyền tư vấn tạm thời) chuyển sang Agent đích.

**Quy tắc nghiệp vụ:**

- BR-07.1: Tối thiểu PHẢI có ba kiểu chuyển tiếp: Chuyển hẳn, Chuyển có xác nhận, Xin ý kiến; mô hình chuyển tiếp PHẢI mở rộng thêm kiểu mới được mà không phá vỡ các quy tắc còn lại của mục này.
- BR-07.2: Yêu cầu chuyển tiếp PHẢI chỉ định rõ Agent đích hoặc nhóm đích; chuyển cho một nhóm (không chỉ định Agent cụ thể) PHẢI luôn là kiểu Chuyển hẳn.
- BR-07.3: Chỉ Agent đang phụ trách hội thoại hoặc người có quyền quản lý phân công (Giám sát viên) mới được khởi tạo chuyển tiếp.
- BR-07.4: Yêu cầu Chuyển có xác nhận/Xin ý kiến PHẢI có thời hạn phản hồi; hết hạn mà chưa phản hồi thì yêu cầu tự động hủy.
- BR-07.5: Agent đích chỉ được nhận chuyển tiếp nếu còn khả năng nhận thêm việc; nếu không đủ khả năng tại đúng thời điểm xác nhận, hệ thống PHẢI từ chối và báo cho cả hai bên.
- BR-07.6: Trong lúc "Xin ý kiến" đang diễn ra, Agent gốc PHẢI vẫn là người chịu trách nhiệm chính; khi kết thúc, Agent gốc chọn giữ nguyên hoặc chuyển hẳn quyền phụ trách sang người vừa tư vấn.
- BR-07.7: Một hội thoại chỉ được có tối đa một yêu cầu chuyển tiếp đang chờ xử lý tại một thời điểm.
- BR-07.8: Nếu hội thoại được giải quyết/đóng trong lúc đang có yêu cầu chuyển tiếp treo, yêu cầu đó PHẢI tự động hủy.
- BR-07.9 `[Yêu cầu mới]`: Mọi yêu cầu chuyển tiếp PHẢI kèm **lý do chuyển tiếp** chọn từ danh mục doanh nghiệp định nghĩa (xem FEAT-29). Doanh nghiệp CÓ THỂ đặt lý do là bắt buộc. Lý do: tỷ lệ chuyển tiếp và nguyên nhân chuyển tiếp là chỉ số phát hiện định tuyến sai và Agent thiếu kỹ năng — không thu thập được thì không cải thiện được, và người nhận việc cũng không biết mình được giao vì lý do gì.
- BR-07.10 `[Yêu cầu mới]`: Khi hội thoại đổi người phụ trách, khách hàng PHẢI được thông báo rõ là đang được chuyển sang ai hoặc sang bộ phận nào, trừ khi doanh nghiệp chủ động cấu hình khác. Lý do: đang trò chuyện với một người rồi đột nhiên giọng văn và cách xưng hô đổi hẳn mà không giải thích là một trải nghiệm khiến khách hàng phải kể lại câu chuyện từ đầu.

**Tiêu chí chấp nhận:**

- Chuyển hẳn cho một nhóm không cần ai xác nhận, hội thoại vào hàng đợi/được gán ngay theo quy tắc của nhóm đó.
- Chuyển có xác nhận chỉ đổi người phụ trách sau khi Agent đích đồng ý.
- Yêu cầu chuyển tiếp quá hạn tự hủy, không treo vô thời hạn.
- Khách hàng biết mình vừa được chuyển sang bộ phận nào, không bị đổi người trong im lặng.
- Giám sát viên thống kê được tỷ lệ chuyển tiếp và lý do chuyển tiếp nhiều nhất.

**Tham chiếu:** BR-07.9, BR-07.10 → issue [#74](https://github.com/crmsaassaudi/product-management/issues/74).

---

### FEAT-08 — Cam kết thời gian phản hồi (SLA) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Đo và cảnh báo thời gian doanh nghiệp phản hồi/giải quyết hội thoại của khách hàng, theo cam kết nội bộ doanh nghiệp tự đặt ra, để đảm bảo tốc độ phục vụ nhất quán.

**Actor:** Hệ thống (đo lường tự động); Quản trị viên (cấu hình cam kết); Agent/Giám sát viên (theo dõi đếm ngược).

**Luồng chính:**

1. Quản trị viên định nghĩa Chính sách SLA: loại cam kết (phản hồi lần đầu, phản hồi các lần sau, giải quyết xong, hoặc im lặng tối đa giữa chừng), thời hạn theo từng phân khúc khách hàng, và phạm vi áp dụng.
2. Hội thoại mới tự động được gắn cam kết phản hồi lần đầu và cam kết giải quyết xong.
3. Khi khách hàng nhắn tin, đồng hồ đếm ngược phản hồi bắt đầu/khởi động lại; khi Agent trả lời, đồng hồ đó dừng lại và ghi nhận đã đáp ứng hay đã vi phạm.
4. Khi hội thoại Tạm hoãn, mọi đồng hồ đang chạy tạm dừng; khi trở lại Đang mở, đồng hồ tiếp tục từ thời gian còn lại.

**Quy tắc nghiệp vụ:**

- BR-08.1: Quản trị viên PHẢI định nghĩa được Chính sách SLA riêng theo từng phân khúc khách hàng, cho từng loại cam kết (phản hồi lần đầu, phản hồi các lần sau, giải quyết xong).
- BR-08.2: Một Hộp thư (Inbox) CÓ THỂ áp dụng một Chính sách SLA riêng, khác với chính sách mặc định của doanh nghiệp.
- BR-08.3: Hội thoại mới tạo hoặc vừa được mở lại PHẢI đồng thời có cam kết phản hồi lần đầu và cam kết giải quyết xong đang chạy.
- BR-08.4: Thời hạn cam kết PHẢI được tính theo giờ làm việc của doanh nghiệp (nếu doanh nghiệp có cấu hình giờ làm việc) — thời gian ngoài giờ làm việc không tính vào thời hạn.
- BR-08.5: Khi khách hàng nhắn tin, hệ thống PHẢI khởi động (hoặc khởi động lại) đúng loại đồng hồ cam kết phù hợp (lần đầu nếu chưa từng phản hồi, các lần sau nếu đã từng phản hồi).
- BR-08.6: Khi Agent trả lời, hệ thống PHẢI ghi nhận mốc phản hồi và dừng mọi đồng hồ phản hồi đang chạy — độc lập với việc hội thoại có Chính sách SLA áp dụng hay không (để luôn đo được tốc độ phản hồi thực tế phục vụ báo cáo).
- BR-08.7: Khi hội thoại chuyển Tạm hoãn, mọi đồng hồ đang chạy PHẢI tạm dừng; khi trở lại Đang mở, đồng hồ PHẢI tiếp tục tính từ thời gian còn lại, không tính thêm thời gian tạm dừng vào thời hạn.
- BR-08.8: Khi hội thoại Đã giải quyết/Đã đóng, cam kết giải quyết xong PHẢI được chốt lại; các cam kết phản hồi còn đang chạy PHẢI hủy.
- BR-08.9: Khi hội thoại mở lại, mọi cam kết cũ (kể cả đã hoàn thành/vi phạm) PHẢI được thay bằng một chu kỳ cam kết mới.
- BR-08.10: Agent/Giám sát viên PHẢI nhìn thấy một đồng hồ đếm ngược duy nhất trên mỗi hội thoại, hiển thị cam kết gần tới hạn nhất trong số các cam kết đang áp dụng.
- BR-08.11 `[Yêu cầu mới]`: **Phân khúc khách hàng** dùng làm điều kiện của Chính sách SLA và quy tắc phân công PHẢI đọc từ hồ sơ khách hàng trong CRM, là một nguồn duy nhất dùng chung cho toàn hệ thống; Omnichat KHÔNG ĐƯỢC tự định nghĩa một danh sách phân khúc riêng. Khi một hội thoại gắn với Hồ sơ khách hàng tạm chưa xác định được phân khúc, hệ thống PHẢI áp cam kết mặc định và tự áp lại cam kết đúng ngay khi hội thoại được liên kết với hồ sơ khách hàng thật.
- BR-08.12 `[Yêu cầu mới]`: Với kênh có Thuộc tính **trò chuyện đồng thời**, doanh nghiệp PHẢI đặt được thêm một cam kết **thời gian im lặng tối đa giữa chừng**: khoảng thời gian dài nhất mà một hội thoại đã có người phụ trách được phép không có phản hồi nào từ phía doanh nghiệp. Vượt ngưỡng PHẢI cảnh báo cho Agent và Trưởng nhóm. Lý do: cam kết phản hồi lần đầu và các lần sau không bắt được tình huống Agent đã nhận việc rồi để khách treo trên màn hình — đó lại chính là tình huống khách hàng bực nhất.

**Tiêu chí chấp nhận:**

- Hội thoại mới luôn có đồng hồ đếm ngược cam kết phản hồi và giải quyết đang chạy.
- Tạm hoãn hội thoại dừng đếm ngược; mở lại tiếp tục đúng từ thời gian còn lại.
- Thời hạn cam kết tính đúng theo giờ làm việc, không bị "trôi" thêm vì thời gian ngoài giờ.
- Khách hàng VIP được áp đúng cam kết của phân khúc đó ngay khi hội thoại được nhận diện là của họ.
- Hội thoại trò chuyện trực tiếp bị bỏ im quá ngưỡng luôn có cảnh báo, kể cả khi cam kết phản hồi chưa tới hạn.

**Tham chiếu:** BR-08.11, BR-08.12 → issue [#75](https://github.com/crmsaassaudi/product-management/issues/75).

---

### FEAT-09 — Leo thang khi vi phạm SLA `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi một hội thoại có nguy cơ hoặc đã vi phạm cam kết thời gian phản hồi, hệ thống tự động thực hiện hành động cảnh báo phù hợp, để sự cố được xử lý trước khi ảnh hưởng tới khách hàng.

**Actor:** Hệ thống (tự động thực hiện); Quản trị viên (cấu hình chính sách); Giám sát viên/Quản lý (người nhận cảnh báo).

**Luồng chính:**

1. Quản trị viên định nghĩa Chính sách leo thang, gắn với một Chính sách SLA cụ thể: kích hoạt khi "sắp vi phạm" hay "đã vi phạm", sau bao lâu, và hành động cần thực hiện.
2. Khi hội thoại vi phạm/sắp vi phạm SLA tương ứng, hệ thống chờ đúng khoảng thời gian đã cấu hình rồi thực hiện hành động.
3. Nếu hội thoại đã được xử lý xong trước khi tới lượt thực hiện, hành động leo thang tự hủy.

**Quy tắc nghiệp vụ:**

- BR-09.1: Chính sách leo thang PHẢI gắn với đúng một Chính sách SLA, xác định độ trễ kích hoạt, và danh sách hành động cần thực hiện.
- BR-09.2: Hệ thống PHẢI hỗ trợ các hành động: đánh dấu cảnh báo trên hội thoại, thông báo cho một người cụ thể, thông báo cho cấp quản lý cao hơn (leo lên N cấp), hoặc tự động chuyển hội thoại cho người khác.
- BR-09.3: Trước khi thực hiện, hệ thống PHẢI kiểm tra hội thoại vẫn còn đang cần xử lý (chưa được giải quyết/đóng) — nếu đã xong việc, hành động leo thang không còn cần thiết và tự hủy.
- BR-09.4: Nếu hội thoại tiếp tục vi phạm nghiêm trọng hơn, hệ thống PHẢI thay thế hành động leo thang cũ đang chờ bằng hành động mới, không thực hiện chồng chéo nhiều lần cho cùng một lần vi phạm.
- BR-09.5: Thông báo leo thang PHẢI gửi trực tiếp tới đúng người được chỉ định (hoặc đúng cấp quản lý được xác định), không gửi tràn lan tới cả đội.

**Tiêu chí chấp nhận:**

- Hội thoại được giải quyết trước khi tới lượt leo thang thì không có cảnh báo nào được gửi.
- Người được chỉ định nhận cảnh báo nhận được thông báo đúng lúc, đúng nội dung hội thoại vi phạm.

---

### FEAT-10 — Tự động đóng hội thoại không hoạt động `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động chuyển các hội thoại không còn hoạt động (khách hàng không phản hồi, việc đã xong nhưng Agent quên đóng) sang trạng thái Đã giải quyết/Đã đóng sau một khoảng thời gian, giữ danh sách hội thoại luôn gọn gàng và phản ánh đúng việc thực sự cần xử lý — có cảnh báo trước cho khách hàng để không đóng đột ngột.

**Actor:** Hệ thống (tự động thực hiện); Quản trị viên (cấu hình chính sách); Agent (có thể can thiệp từng hội thoại cụ thể).

**Luồng chính:**

1. Quản trị viên định nghĩa Chính sách tự động đóng: điều kiện áp dụng, khoảng thời gian không hoạt động cho phép, có cảnh báo trước hay không, thời gian ân hạn sau cảnh báo, trạng thái đích (Đã giải quyết hoặc Đã đóng), và cách xử lý khi khách nhắn lại sau đó.
2. Khi một hội thoại đạt ngưỡng thời gian không hoạt động, nếu chính sách có bật cảnh báo, hệ thống gửi tin nhắn hỏi khách hàng còn cần hỗ trợ không, và chờ thêm một khoảng thời gian ân hạn.
3. Nếu khách hàng phản hồi hoặc có hoạt động mới trong lúc chờ, việc đóng bị hủy, hội thoại quay lại bình thường.
4. Nếu hết thời gian ân hạn mà không có phản hồi, hội thoại được đóng theo trạng thái đích đã cấu hình.

**Quy tắc nghiệp vụ:**

- BR-10.1: Quản trị viên PHẢI cấu hình được Chính sách tự động đóng theo điều kiện áp dụng (ví dụ theo kênh, theo tag), khoảng thời gian không hoạt động, và trạng thái đích.
- BR-10.2: Doanh nghiệp CÓ THỂ chọn "không bao giờ tự động đóng" cho một số nhóm hội thoại cụ thể (ví dụ hội thoại đang chờ xử lý nội bộ đặc biệt).
- BR-10.3: Nếu chính sách bật cảnh báo trước, hệ thống PHẢI gửi tin nhắn hỏi khách hàng trước khi đóng, và CÓ THỂ lặp lại cảnh báo một số lần theo cấu hình nếu khách hàng vẫn im lặng.
- BR-10.4: Bất kỳ hoạt động thực sự nào từ khách hàng hoặc Agent trong lúc đang chờ đóng PHẢI hủy việc đóng ngay lập tức, đưa hội thoại quay lại trạng thái xử lý bình thường — tin nhắn cảnh báo tự động của chính hệ thống không được tính là một "hoạt động" làm hủy việc đóng.
- BR-10.5: Agent PHẢI có thể tự đặt riêng cho một hội thoại cụ thể: miễn trừ tự động đóng, tạm hoãn việc đóng thêm một khoảng thời gian, hoặc ép đóng ngay vào một thời điểm chỉ định.
- BR-10.6: Khi hội thoại được đóng tự động dưới trạng thái Đã giải quyết, doanh nghiệp PHẢI cấu hình được cách xử lý nếu khách hàng nhắn lại sau đó: luôn mở lại hội thoại cũ, luôn tạo hội thoại mới, hoặc mở lại nếu trong một khoảng thời gian nhất định (nếu quá thời gian đó thì tạo mới).
- BR-10.7 `[Yêu cầu mới]`: Khi việc đóng tự động của một hội thoại bị hoãn lại vì chạm Ngưỡng an toàn đóng hàng loạt, hệ thống PHẢI tự đóng lại hội thoại đó ngay khi tình trạng bất thường kết thúc, và trong mọi trường hợp không để hội thoại chờ quá thời hạn doanh nghiệp cấu hình (mặc định 10 phút) — không được để hội thoại treo cho tới khi có hoạt động mới hoặc tới khi quản trị viên can thiệp thủ công.
- BR-10.8 `[Yêu cầu mới]`: Khi một hội thoại đã đủ điều kiện đóng nhưng chưa được đóng, Giám sát viên PHẢI biết được **lý do ngay trên chính hội thoại đó**, phân biệt tối thiểu hai trường hợp: đang hoãn vì Ngưỡng an toàn đóng hàng loạt (doanh nghiệp đang ở tình trạng bất thường, cần xem xét), hay chưa đóng được vì một nguyên nhân khác (cần đội vận hành xử lý). Ngoài ra, mỗi lần Ngưỡng an toàn đóng hàng loạt kích hoạt PHẢI sinh một cảnh báo tới Giám sát viên, kèm số lượng hội thoại bị ảnh hưởng và kênh liên quan.

**Tiêu chí chấp nhận:**

- Hội thoại nhận được cảnh báo trước khi bị đóng tự động (nếu chính sách bật cảnh báo), không bao giờ đóng đột ngột không báo trước.
- Khách hàng phản hồi trong lúc đang chờ đóng thì hội thoại được giữ lại bình thường.
- Hội thoại bị hoãn đóng do bảo vệ hệ thống luôn được đóng lại trong thời hạn doanh nghiệp cấu hình, không bị bỏ quên.
- Giám sát viên mở một hội thoại đáng lẽ đã đóng là thấy ngay lý do vì sao nó chưa đóng.

**Tham chiếu:** BR-10.7 → issue [#17](https://github.com/crmsaassaudi/product-management/issues/17). BR-10.8 → issue [#18](https://github.com/crmsaassaudi/product-management/issues/18).

---

### FEAT-11 — Bot trả lời tự động & bàn giao cho Agent `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép một kịch bản trả lời tự động (Bot) tiếp nhận và xử lý hội thoại thay Agent trong giai đoạn đầu (ví dụ chào hỏi, thu thập thông tin, trả lời câu hỏi thường gặp), rồi bàn giao đúng lúc cho Agent phù hợp khi cần con người xử lý tiếp.

**Actor:** Bot (hệ thống trả lời tự động, cấu hình ở một công cụ riêng); Agent (nhận bàn giao, có thể chủ động tắt/bật Bot).

**Luồng chính:**

1. Tin nhắn khách hàng mới tới, nếu kênh/hội thoại đang bật Bot, hệ thống chuyển cho Bot xử lý.
2. Bot trả lời một hoặc nhiều tin nhắn dựa trên kịch bản đã cấu hình.
3. Khi Bot xác định cần con người xử lý tiếp (ví dụ khách yêu cầu gặp nhân viên, hoặc kịch bản kết thúc), Bot bàn giao hội thoại — có thể chỉ định bàn giao cho một Agent cụ thể, một nhóm cụ thể, hoặc hàng đợi chung.
4. Agent PHẢI có thể chủ động tắt Bot bất kỳ lúc nào để tự tiếp quản hội thoại, và bật lại Bot nếu muốn giao lại.

**Quy tắc nghiệp vụ:**

- BR-11.1: Bot chỉ được kích hoạt cho hội thoại khi cả cấu hình cấp kênh và cấp hội thoại đều đang cho phép Bot hoạt động.
- BR-11.2: Doanh nghiệp CÓ THỂ cấu hình theo từng kênh một trong ba chế độ: Bot xử lý trước rồi luôn bàn giao cho người khi kết thúc kịch bản, Bot xử lý toàn bộ và chỉ bàn giao khi không có kịch bản phù hợp, hoặc tắt hẳn Bot (mọi hội thoại luôn cần Agent xử lý ngay từ đầu).
- BR-11.3: Khi Bot bàn giao có chỉ định Agent/nhóm cụ thể, hệ thống PHẢI kiểm tra Agent/nhóm đó còn hợp lệ và đủ điều kiện hỗ trợ đúng kênh; nếu không, hệ thống PHẢI tự chuyển về bàn giao cho hàng đợi chung thay vì bàn giao nhầm.
- BR-11.4: Agent PHẢI có thể chủ động tắt Bot để tự tiếp quản hội thoại ngay lập tức, và bật lại Bot khi muốn.
- BR-11.5: Một phản hồi từ Bot đến muộn sau khi Agent đã tiếp quản hoặc Bot đã bàn giao xong PHẢI bị bỏ qua, không được "hồi sinh" lại phiên Bot đã kết thúc.
- BR-11.6: Khi Agent trả lời một hội thoại mà Bot đang hoạt động, hệ thống PHẢI tự động tắt Bot cho hội thoại đó (trừ khi doanh nghiệp cấu hình khác), tránh việc Bot và Agent cùng trả lời chồng chéo.

**Tiêu chí chấp nhận:**

- Hội thoại bàn giao đúng người/nhóm được chỉ định khi hợp lệ; bàn giao vào hàng đợi chung khi chỉ định không còn hợp lệ.
- Agent tắt Bot là tiếp quản được ngay, không có phản hồi Bot nào chen vào sau đó.
- Agent trả lời trong lúc Bot đang chạy sẽ tự động tắt Bot cho hội thoại đó.

---

### FEAT-12 — Gửi tin nhắn cho khách hàng `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép Agent (hoặc Bot, hoặc hệ thống) gửi tin nhắn trả lời khách hàng qua đúng kênh của hội thoại, đảm bảo tin nhắn được gửi đáng tin cậy và tuân thủ giới hạn của từng nền tảng kênh.

**Actor:** Agent; Bot; Hệ thống (tin nhắn tự động như thông báo ngoài giờ, cảnh báo trước khi đóng, khảo sát CSAT).

**Luồng chính:**

1. Agent soạn tin nhắn (văn bản, ảnh/video/tệp, tin nhắn mẫu, hoặc tin nhắn có nút bấm tương tác) và gửi.
2. Hệ thống kiểm tra hội thoại còn được phép gửi tin (chưa đóng, còn trong Cửa sổ phản hồi nếu kênh có giới hạn).
3. Tin nhắn được gửi qua đúng kênh; trạng thái gửi được cập nhật cho Agent thấy (đang gửi → đã gửi → đã nhận → đã đọc, hoặc gửi lỗi).

**Quy tắc nghiệp vụ:**

- BR-12.1: Hệ thống PHẢI từ chối gửi tin nhắn vào một hội thoại Đã đóng.
- BR-12.2: Khi đã hết Cửa sổ phản hồi trên một kênh có áp giới hạn này, hệ thống PHẢI ngăn gửi tin nhắn tự do; với kênh khai báo Thuộc tính **có tin nhắn mẫu đã phê duyệt trước** (BR-01.8), Agent CÓ THỂ vẫn gửi được bằng loại tin nhắn này để chủ động liên hệ lại.
- BR-12.3: Mỗi kênh khai báo khả năng hiển thị tin nhắn dạng tương tác của mình (BR-01.8). Hệ thống PHẢI tự thích ứng nội dung theo khả năng của kênh đang gửi — chuyển thành danh sách lựa chọn dạng văn bản đánh số khi kênh không hỗ trợ nút — và PHẢI cho Agent biết trước khi gửi rằng khách hàng sẽ nhìn thấy nội dung khác với lúc soạn.
- BR-12.4: Khi một tin nhắn gửi thất bại và hệ thống biết chắc khách hàng chưa nhận được, hệ thống CHO PHÉP gửi lại. Khi hệ thống không biết chắc khách hàng đã nhận hay chưa, hệ thống KHÔNG ĐƯỢC tự gửi lại: tin nhắn PHẢI hiển thị ở trạng thái không chắc chắn kèm chỉ dẫn rõ ràng để Agent tự quyết định — vì khách hàng nhận hai lần cùng một tin nhắn là trải nghiệm tệ hơn việc Agent phải chủ động gửi lại.
- BR-12.5: Khi Agent trả lời một hội thoại chưa có ai phụ trách, hệ thống PHẢI tự động gán hội thoại đó cho Agent vừa trả lời.
- BR-12.6: Khi một kênh giới hạn số lượng tin nhắn doanh nghiệp được phép gửi trong một khoảng thời gian, tin nhắn Agent trả lời trực tiếp cho một khách hàng đang chờ PHẢI luôn được ưu tiên gửi trước mọi loại tin nhắn khác của doanh nghiệp trên kênh đó (thông báo tự động, lời mời khảo sát, và tin nhắn hàng loạt nếu về sau doanh nghiệp có tính năng này) — không bao giờ để một khách hàng đang chờ bị trả lời chậm vì các loại tin nhắn đó.
- BR-12.7: Tin nhắn hệ thống tự động gửi thay mặt doanh nghiệp (thông báo ngoài giờ, cảnh báo trước khi đóng, link khảo sát CSAT) PHẢI được đánh dấu khác với tin nhắn do Agent chủ động gửi, để không bị tính nhầm là hoạt động của Agent trong các báo cáo/quy tắc tự động đóng.
- BR-12.8: Agent PHẢI luôn biết chắc một tin nhắn đã tới khách hàng hay chưa. Hệ thống KHÔNG ĐƯỢC để một tin nhắn dừng vô thời hạn ở trạng thái "đang gửi": quá thời hạn chờ do doanh nghiệp cấu hình, tin nhắn PHẢI chuyển sang trạng thái thất bại (hoặc không chắc chắn, theo BR-12.4) kèm chỉ dẫn thao tác tiếp theo.
- BR-12.9 `[Yêu cầu mới]`: Khi Cửa sổ phản hồi của một kênh sắp hết hoặc đã hết, doanh nghiệp sắp/đã mất khả năng chủ động liên hệ khách trên kênh đó. Hệ thống PHẢI: (a) cảnh báo Agent và Trưởng nhóm **trước khi** cửa sổ hết hạn với những hội thoại khách còn đang chờ trả lời, để còn kịp phản hồi; (b) khi đã hết hạn và kênh không có tin nhắn mẫu, gợi ý cho Agent các cách liên hệ khác đã có trong hồ sơ khách hàng (email, số điện thoại, kênh khác khách từng dùng), và ghi nhận hội thoại thuộc diện "không liên hệ lại được trên kênh gốc" để Giám sát viên theo dõi được quy mô tình huống này.
- BR-12.10 `[Yêu cầu mới]`: Agent PHẢI thu hồi được một tin nhắn vừa gửi nhầm, trong phạm vi kênh đó còn cho phép. Khi kênh không cho phép thu hồi, hệ thống PHẢI nói rõ điều đó ngay lúc Agent thao tác thay vì báo lỗi mơ hồ. Mọi lần thu hồi PHẢI để lại vết trong lịch sử hội thoại (ai thu hồi, lúc nào, nội dung gốc) theo BR-15.4, và nội dung gốc PHẢI vẫn tra cứu được khi có khiếu nại — thu hồi là để khách hàng không đọc phải, không phải để xóa dấu vết.

**Tiêu chí chấp nhận:**

- Không gửi được tin nhắn vào hội thoại đã đóng.
- Hết Cửa sổ phản hồi trên kênh giới hạn thì chặn gửi tin tự do, gợi ý dùng tin nhắn mẫu nếu kênh hỗ trợ.
- Tin nhắn có kết quả gửi không rõ ràng không bao giờ tự động gửi lại (tránh gửi trùng cho khách).
- Khi kênh giới hạn số tin được gửi, tin trả lời khách hàng đang chờ luôn đi trước tin nhắn tự động của hệ thống.
- Hội thoại sắp hết Cửa sổ phản hồi mà khách chưa được trả lời luôn có cảnh báo trước, không im lặng cho tới lúc hết hạn.
- Tin nhắn gửi nhầm thu hồi được trên kênh cho phép, và vết thu hồi vẫn còn trong lịch sử để đối chất khi cần.

**Tham chiếu:** BR-12.9, BR-12.10 → issue [#64](https://github.com/crmsaassaudi/product-management/issues/64).

---

### FEAT-13 — Không bỏ lỡ việc và không trùng việc `[Đã triển khai]`

**Mô tả nghiệp vụ:** Bảo đảm hai điều với đội ngũ đang trực: không ai bỏ lỡ một việc đang chờ mình, và không hai người cùng làm một việc rồi chồng chéo trước mặt khách hàng.

**Actor:** Agent; Giám sát viên.

**Quy tắc nghiệp vụ:**

- BR-13.1: Mọi tin nhắn mới, thay đổi trạng thái, thay đổi người phụ trách, và lời mời nhận hội thoại PHẢI xuất hiện ngay trên màn hình của (các) Agent liên quan, không đòi hỏi Agent phải tự thao tác gì để thấy được.
- BR-13.2: Agent chỉ được nhận cập nhật của những hội thoại mình có quyền xem — không nhận được thông tin của hội thoại thuộc kênh/nhóm mình không được phân công hỗ trợ.
- BR-13.3: Khi hai Agent cùng lúc cố nhận một hội thoại đang chờ trong hàng đợi, hệ thống PHẢI đảm bảo chỉ đúng một người nhận được, người còn lại nhận thông báo hội thoại đã có người nhận.
- BR-13.4: Khi một Agent đang soạn tin cho một hội thoại, hệ thống PHẢI cho những người khác đang mở hội thoại đó thấy rõ ai đang soạn. Đây là **cảnh báo, không phải khóa**: hệ thống không chặn người thứ hai trả lời, vì có tình huống nghiệp vụ hợp lệ cần điều đó (Giám sát viên can thiệp gấp, Agent bàn giao giữa chừng).
- BR-13.5: Thông báo cá nhân (được yêu cầu chuyển tiếp, bị leo thang tới, được nhắc tên trong ghi chú...) PHẢI gửi đúng tới người liên quan, không thông báo tràn lan cho cả đội.

**Tiêu chí chấp nhận:**

- Tin nhắn khách hàng mới hiện ngay trên màn hình Agent đang phụ trách, không phải chờ hay tự thao tác gì.
- Hai Agent cùng bấm nhận 1 hội thoại đang chờ, chỉ đúng 1 người nhận được.

---

### FEAT-14 — Khảo sát hài lòng khách hàng (CSAT) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Sau khi một hội thoại được giải quyết, tự động mời khách hàng chấm điểm mức độ hài lòng, để doanh nghiệp đo lường chất lượng phục vụ không chỉ dựa vào tốc độ phản hồi.

**Actor:** Khách hàng (người chấm điểm); Hệ thống (tự động gửi khảo sát); Giám sát viên (xem báo cáo).

**Luồng chính:**

1. Hội thoại chuyển sang Đã giải quyết.
2. Hệ thống tự động gửi lời mời khảo sát cho khách hàng qua đúng kênh của hội thoại đó (hiển thị trực tiếp trên Live Chat, hoặc gửi đường dẫn khảo sát qua các kênh khác).
3. Khách hàng chấm điểm 1–5 sao, có thể kèm nhận xét.
4. Điểm số được ghi nhận, hiển thị cho Agent/Giám sát viên và tính vào báo cáo.

**Quy tắc nghiệp vụ:**

- BR-14.1: Hệ thống PHẢI tự động gửi lời mời khảo sát khi hội thoại chuyển sang Đã giải quyết, với các kênh có hỗ trợ khảo sát.
- BR-14.2: Mỗi lời mời khảo sát PHẢI chỉ chấm điểm được đúng một lần, và chỉ còn hiệu lực trong thời hạn do doanh nghiệp cấu hình (mặc định 7 ngày); quá hạn thì lời mời không dùng được nữa và hội thoại đó được ghi nhận là không có phản hồi khảo sát.
- BR-14.3: Hệ thống PHẢI cung cấp báo cáo CSAT: tổng số khảo sát đã gửi, tỷ lệ phản hồi, điểm trung bình, phân bố điểm theo từng mức, điểm trung bình theo Agent và theo kênh.
- BR-14.4 `[Yêu cầu mới]`: Điểm CSAT trung bình PHẢI được đưa vào **cùng Báo cáo vận hành Omnichannel tổng quan** (FEAT-19), theo cùng bộ lọc ngày/kênh mà các số liệu khác trong báo cáo đó đang dùng — không bắt Giám sát viên phải mở một màn hình riêng để đối chiếu số liệu chất lượng với số liệu khối lượng công việc.
- BR-14.5 `[Yêu cầu mới]`: Điểm CSAT trung bình theo từng Agent PHẢI xuất hiện như một cột trong **Báo cáo hiệu suất Agent** (FEAT-20), cạnh các chỉ số tốc độ xử lý, để không đánh giá năng suất Agent chỉ dựa trên tốc độ mà bỏ qua chất lượng phục vụ.

**Tiêu chí chấp nhận:**

- Khách hàng nhận được lời mời khảo sát ngay khi hội thoại được giải quyết.
- Một đường dẫn khảo sát chỉ chấm điểm được đúng một lần.
- Báo cáo vận hành Omnichannel và Báo cáo hiệu suất Agent đều hiển thị điểm CSAT trung bình, dùng chung bộ lọc với các số liệu khác trong cùng báo cáo.

**Tham chiếu:** BR-14.4 → issue [#19](https://github.com/crmsaassaudi/product-management/issues/19). BR-14.5 → issue [#20](https://github.com/crmsaassaudi/product-management/issues/20).

---

### FEAT-15 — Ghi chú, biểu tượng phản hồi nhanh & lịch sử xử lý `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép Agent ghi chú nội bộ trong lúc xử lý hội thoại (không hiển thị cho khách hàng), phản hồi nhanh bằng biểu tượng trên một tin nhắn, và lưu lại toàn bộ lịch sử các mốc quan trọng của hội thoại để tra cứu sau này. Đây là thao tác thả biểu tượng lên tin nhắn, KHÔNG phải năng lực phân tích cảm xúc hay ý định của khách hàng — năng lực đó chưa có, xem Mục 7.

**Actor:** Agent (viết ghi chú, thả biểu tượng); Khách hàng (thả biểu tượng trên các kênh hỗ trợ); Hệ thống (tự động ghi lịch sử).

**Quy tắc nghiệp vụ:**

- BR-15.1: Ghi chú nội bộ PHẢI không hiển thị cho khách hàng dưới bất kỳ hình thức nào.
- BR-15.2: Agent CÓ THỂ ghim một ghi chú làm "Ghi chú bàn giao" nổi bật đầu hội thoại, hữu ích khi chuyển tiếp cho người khác; chỉ một ghi chú được ghim tại một thời điểm cho mỗi hội thoại.
- BR-15.3: Mỗi người (khách hàng hoặc Agent) chỉ được có một biểu tượng đang thả trên một tin nhắn tại một thời điểm; thả biểu tượng khác sẽ thay thế biểu tượng cũ.
- BR-15.4: Hệ thống PHẢI tự động ghi lại các mốc quan trọng của hội thoại (tạo mới, mở lại, đổi trạng thái, đổi người/nhóm phụ trách, thêm/xóa nhãn, chọn lý do xử lý, chuyển tiếp kèm lý do, thu hồi tin nhắn, vi phạm SLA, bị leo thang, bàn giao Bot, đánh dấu spam...) thành một dòng thời gian xem lại được. Dòng thời gian này PHẢI phân biệt rõ hành động của người, của Bot và của hệ thống tự động.
- BR-15.5 `[Yêu cầu mới]`: Lịch sử xử lý là một cam kết **đầy đủ** theo BR-15.4. Việc ghi lịch sử không được làm gián đoạn việc phục vụ khách hàng, nhưng hệ thống cũng KHÔNG ĐƯỢC âm thầm bỏ qua một mốc không ghi lại được: khi đó hệ thống PHẢI đánh dấu rõ một khoảng trống tại đúng vị trí trong dòng thời gian và cảnh báo cho đội vận hành, thay vì để người đọc về sau hiểu nhầm rằng mốc đó chưa từng xảy ra. Với riêng nhóm thao tác thay đổi cấu hình quyền truy cập của Omnichat, áp dụng quy tắc chặt hơn tại BR-25.2.

**Tiêu chí chấp nhận:**

- Ghi chú nội bộ không bao giờ lộ ra phía khách hàng.
- Một mốc không ghi lại được luôn để lại dấu vết "thiếu dữ liệu" nhìn thấy được, không biến mất im lặng.
- Mọi mốc quan trọng của hội thoại tra cứu được đầy đủ theo đúng trình tự thời gian.

**Tham chiếu:** BR-15.5 → issue [#65](https://github.com/crmsaassaudi/product-management/issues/65).

---

### FEAT-16 — Tìm kiếm nội dung tin nhắn `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép Agent tìm lại một tin nhắn cụ thể trong lịch sử trò chuyện của một hội thoại hoặc của một khách hàng, và cho phép Giám sát viên rà soát theo từ khóa trên toàn bộ phạm vi mình quản lý, thay vì phải cuộn tay qua toàn bộ lịch sử.

**Actor:** Agent; Trưởng nhóm; Giám sát viên; Chuyên viên chất lượng (trong phạm vi được phép).

**Quy tắc nghiệp vụ:**

- BR-16.1 `[Yêu cầu mới]`: Phạm vi tìm kiếm PHẢI đi theo đúng phạm vi dữ liệu mà người tìm được phép xem, không theo một giới hạn cố định: Agent tìm được trong các hội thoại mình có quyền xem; Giám sát viên tìm được trên toàn bộ hội thoại thuộc phạm vi quản lý của mình — đây là nhu cầu nghiệp vụ có thật (rà mọi hội thoại có nhắc tới một sự cố sản phẩm, một chương trình khuyến mãi, một đối tác). Kết quả tìm kiếm PHẢI không bao giờ chứa hội thoại nằm ngoài phạm vi của người tìm.
- BR-16.2: Kết quả tìm kiếm PHẢI hiển thị đoạn trích ngắn quanh từ khóa để Agent nhận ra đúng ngữ cảnh trước khi mở toàn bộ tin nhắn.

**Tiêu chí chấp nhận:**

- Agent tìm được đúng tin nhắn cần tìm trong một hội thoại/khách hàng cụ thể mà không cần cuộn thủ công.
- Giám sát viên rà được toàn bộ hội thoại trong phạm vi quản lý theo một từ khóa, và không thấy hội thoại ngoài phạm vi đó.

**Tham chiếu:** BR-16.1 → issue [#66](https://github.com/crmsaassaudi/product-management/issues/66).

---

### FEAT-17 — Quản lý hộp thư (Inbox) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép doanh nghiệp gộp nhiều kênh vào một Hộp thư chung, gán riêng nhóm/Agent phụ trách và chính sách riêng (phân công, SLA, Bot) khác với mặc định toàn doanh nghiệp — phù hợp với doanh nghiệp có nhiều đội/nhiều dòng sản phẩm cùng dùng chung hệ thống.

**Actor:** Quản trị viên.

**Quy tắc nghiệp vụ:**

- BR-17.1: Một Hộp thư PHẢI gắn được với một hoặc nhiều nhóm/Agent được phép xử lý.
- BR-17.2: Một Hộp thư CÓ THỂ chỉ định riêng một Quy tắc phân công, một Chính sách SLA, và một cấu hình Bot khác với mặc định toàn doanh nghiệp; nếu không chỉ định, Hộp thư dùng theo cấu hình mặc định.
- BR-17.3 `[Yêu cầu mới]`: Nếu tại một thời điểm hệ thống không xác định được cấu hình riêng của Hộp thư, hội thoại vẫn PHẢI được tiếp nhận và xử lý theo cấu hình mặc định — không chặn khách hàng lại. Nhưng hệ thống KHÔNG ĐƯỢC âm thầm coi như Hộp thư đó không có cấu hình riêng: hội thoại PHẢI được đánh dấu là đang chạy theo cấu hình mặc định, PHẢI cảnh báo cho Giám sát viên, và PHẢI được áp lại đúng cấu hình riêng (bao gồm cam kết SLA) ngay khi xác định lại được. Lý do: một hội thoại thuộc Hộp thư ưu tiên bị phục vụ theo cam kết mặc định mà không ai biết là một sự cố với khách hàng, không phải một tình huống chấp nhận được.

**Tiêu chí chấp nhận:**

- Hội thoại thuộc kênh nằm trong một Hộp thư có cấu hình riêng thì áp dụng đúng cấu hình riêng đó, không áp nhầm cấu hình mặc định.
- Hội thoại phải chạy tạm theo cấu hình mặc định luôn được đánh dấu và được báo cho Giám sát viên, không im lặng.

**Tham chiếu:** BR-17.3 → issue [#67](https://github.com/crmsaassaudi/product-management/issues/67).

---

### FEAT-18 — Mẫu tin nhắn nhanh (Canned Response) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép Agent soạn sẵn các nội dung trả lời hay dùng (câu chào, hướng dẫn thường gặp, chính sách đổi trả...) và chèn nhanh vào hội thoại bằng một lệnh gõ tắt, thay vì soạn lại từ đầu mỗi lần.

**Actor:** Agent; Quản trị viên (quản lý mẫu dùng chung).

**Quy tắc nghiệp vụ:**

- BR-18.1: Mẫu tin nhắn PHẢI có phạm vi hiển thị: chỉ riêng người tạo, dùng chung cho một nhóm, hoặc dùng chung toàn doanh nghiệp.
- BR-18.2: Mẫu tin nhắn CÓ THỂ gán một lệnh gõ tắt ngắn để chèn nhanh khi soạn tin.
- BR-18.3: Mẫu tin nhắn chỉ riêng người tạo thì chỉ người đó (hoặc quản trị viên) được sửa/xóa; mẫu dùng chung cho nhóm/toàn doanh nghiệp thì mọi người trong phạm vi đó đều sửa được.

**Tiêu chí chấp nhận:**

- Agent gõ lệnh tắt đã gán cho một mẫu, nội dung mẫu được chèn ngay vào ô soạn tin.

---

### FEAT-19 — Báo cáo vận hành Omnichannel `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp cho Giám sát viên bức tranh tổng thể về khối lượng, tốc độ và chất lượng xử lý hội thoại trên toàn doanh nghiệp, theo khoảng thời gian và bộ lọc tùy chọn.

**Actor:** Giám sát viên; Quản lý.

**Nội dung báo cáo:**

- Khối lượng hội thoại theo thời gian (mới tạo, đã giải quyết).
- Tỷ lệ hội thoại theo từng kênh.
- Tốc độ phản hồi/giải quyết (thời gian phản hồi lần đầu, thời gian chờ được gán, thời gian giải quyết), tỷ lệ đáp ứng cam kết SLA.
- Phân bố hội thoại theo trạng thái, theo lý do giải quyết.
- Khối lượng tin nhắn theo loại/chiều/kênh.
- Hiệu quả Bot: tỷ lệ hội thoại Bot tự giải quyết xong, tỷ lệ bàn giao cho người.
- Giờ cao điểm theo ngày trong tuần/giờ trong ngày.
- Phân tích theo nhãn (tag) gắn trên hội thoại.
- Tỷ lệ hội thoại được mở lại sau khi đã giải quyết.
- Tỷ lệ khách bỏ cuộc trước khi được tiếp nhận, và thời gian chờ trung bình của nhóm bỏ cuộc.
- Tỷ lệ chuyển tiếp và các lý do chuyển tiếp phổ biến nhất.
- Phân bố theo lý do xử lý, dùng để phân tích nguyên nhân khách hàng liên hệ.

**Quy tắc nghiệp vụ:**

- BR-19.1: Thời gian phản hồi lần đầu trung bình PHẢI loại trừ những hội thoại chưa từng được Agent nào phản hồi — không được tính các hội thoại đó là "0 phút", vì sẽ làm sai lệch số liệu theo hướng tốt hơn thực tế.
- BR-19.2: Mọi số liệu trong báo cáo PHẢI chỉ tổng hợp trên những hội thoại mà người xem báo cáo có quyền nhìn thấy (theo phạm vi dữ liệu được phân quyền), không lộ số liệu của hội thoại ngoài phạm vi.
- BR-19.3 `[Yêu cầu mới]`: Báo cáo PHẢI có thêm chỉ số điểm CSAT trung bình (xem BR-14.4), dùng chung khoảng thời gian/kênh/agent đang lọc với các chỉ số khác của báo cáo này.
- BR-19.4 `[Yêu cầu mới]`: Báo cáo PHẢI có **tỷ lệ khách bỏ cuộc** (theo BR-05.10) và **tỷ lệ chuyển tiếp** (theo BR-07.9). Lý do: tỷ lệ đáp ứng SLA chỉ nói về những khách đã được phục vụ; nếu không đo số khách bỏ đi giữa chừng, một doanh nghiệp thiếu người vẫn có thể nhìn thấy chỉ số SLA đẹp.
- BR-19.5 `[Yêu cầu mới]`: Mọi con số tổng hợp trong báo cáo PHẢI **bấm xuyên xuống được** ra danh sách hội thoại tạo nên con số đó, trong phạm vi dữ liệu người xem được phép thấy. Lý do: thao tác thường xuyên nhất của Giám sát viên không phải là đọc con số mà là mở ra xem những hội thoại nằm sau con số đó.
- BR-19.6 `[Yêu cầu mới]`: Báo cáo PHẢI xuất được ra tệp và PHẢI gửi được định kỳ theo lịch tới danh sách người nhận do doanh nghiệp cấu hình, kèm cùng bộ lọc đang dùng. Tệp xuất và bản gửi định kỳ PHẢI tuân đúng phạm vi dữ liệu của người nhận, không được lộ số liệu ngoài phạm vi.
- BR-19.7 `[Yêu cầu mới]`: Báo cáo PHẢI xem được theo **Hộp thư, nhóm và đội**, không chỉ theo kênh và theo Agent, và PHẢI so sánh được với một kỳ trước đó. Lý do: Hộp thư là đơn vị tổ chức mà doanh nghiệp dùng để chia trách nhiệm (FEAT-17); không báo cáo theo đơn vị đó thì không quy được trách nhiệm cho ai.

**Tiêu chí chấp nhận:**

- Thay đổi bộ lọc ngày/kênh/agent áp dụng đồng thời cho mọi chỉ số trong báo cáo, bao gồm cả CSAT.
- Giám sát viên chỉ thấy số liệu tổng hợp trong phạm vi dữ liệu được phân quyền.
- Bấm vào con số "hội thoại vi phạm SLA" là mở ra đúng danh sách các hội thoại đó.
- Báo cáo tuần tự động gửi tới người nhận đã cấu hình, không cần ai thao tác tay.
- So sánh được tuần này với tuần trước trên cùng một màn hình.

**Tham chiếu:** BR-19.3 → issue [#21](https://github.com/crmsaassaudi/product-management/issues/21). BR-19.4, BR-19.5, BR-19.6, BR-19.7 → issue [#77](https://github.com/crmsaassaudi/product-management/issues/77).

---

### FEAT-20 — Báo cáo hiệu suất Agent `[Đã triển khai]`

**Mô tả nghiệp vụ:** Đo lường năng suất và chất lượng làm việc của từng Agent, phục vụ hoạch định nhân sự (cần bao nhiêu Agent để đáp ứng khối lượng công việc) và đánh giá hiệu suất công bằng.

**Actor:** Giám sát viên; Quản lý.

**Nội dung báo cáo:** thời gian Agent ở mỗi trạng thái làm việc trong ngày, mức tuân thủ ca (xem FEAT-28), thời gian xử lý trung bình, tỷ lệ thời gian bận việc so với thời gian trực tuyến, số lượng hội thoại đã xử lý, điểm chất lượng (xem FEAT-27), điểm CSAT, và bảng xếp hạng hiệu suất tổng hợp.

**Quy tắc nghiệp vụ:**

- BR-20.1: Báo cáo PHẢI tổng hợp năng suất **theo nhóm công việc phân định bằng Thuộc tính kênh** (BR-01.8), không theo danh sách tên kênh: nhóm trò chuyện **đồng thời** và nhóm trao đổi **không đồng thời**. Một kênh mới tự rơi vào đúng nhóm theo thuộc tính đã khai báo, không phải sửa lại báo cáo. Nếu doanh nghiệp muốn xem cạnh nhau năng suất hội thoại và năng suất các đối tượng nghiệp vụ khác của CRM, phần số liệu của các đối tượng đó do SRS tương ứng đặc tả và đóng góp vào — không đặc tả tại đây.
- BR-20.2 `[Yêu cầu mới]`: Thời gian xử lý trung bình (AHT) PHẢI được tách riêng theo Thuộc tính kênh, KHÔNG ĐƯỢC gộp thành một con số duy nhất:
  - **AHT trò chuyện đồng thời** — cho các kênh mà khách hàng đang chờ ngay trên màn hình, thường xử lý trong vài phút.
  - **AHT trao đổi không đồng thời** — cho các kênh mà khách hàng không nhất thiết chờ ngay, một hội thoại có thể kéo dài nhiều giờ hoặc nhiều ngày.

    Lý do tách: bản chất hai nhóm này khác nhau tới mức gộp chung sẽ cho ra một con số trung bình không dùng được để hoạch định nhân sự — vốn là mục đích duy nhất của chỉ số này. Doanh nghiệp PHẢI xem được AHT chi tiết tới từng kênh bên trong mỗi nhóm.
  - AHT PHẢI tính cả khoảng **Xử lý sau hội thoại** (BR-06.5), vì đó là thời gian thật Agent bỏ ra cho hội thoại đó.
- BR-20.3 `[Yêu cầu mới]`: Điểm CSAT trung bình của từng Agent (xem BR-14.5) PHẢI xuất hiện trong bảng hiệu suất, cạnh các chỉ số tốc độ xử lý, để đánh giá năng suất luôn đi kèm với chất lượng phục vụ.
- BR-20.4: Bảng xếp hạng hiệu suất tổng hợp PHẢI loại trừ những Agent có quá ít thời gian trực tuyến hoặc quá ít việc đã xử lý trong kỳ báo cáo, thay vì xếp hạng không công bằng dựa trên quá ít dữ liệu.
- BR-20.5 `[Yêu cầu mới]`: Bảng xếp hạng hiệu suất KHÔNG ĐƯỢC xây chỉ trên các chỉ số tốc độ. Một Agent chỉ được xếp hạng khi có đủ cả ba mặt trong kỳ: khối lượng, chất lượng (điểm chấm theo FEAT-27 hoặc CSAT), và tuân thủ ca. Lý do: xếp hạng chỉ theo tốc độ sẽ thưởng cho hành vi đóng hội thoại vội và phạt người nhận việc khó — đúng ngược lại điều doanh nghiệp cần.

**Tiêu chí chấp nhận:**

- Báo cáo hiển thị riêng biệt AHT của nhóm đồng thời và nhóm không đồng thời, không gộp chung một con số.
- Cột CSAT trung bình và cột điểm chất lượng xuất hiện cho từng Agent trong cùng bảng hiệu suất.
- Agent có quá ít dữ liệu trong kỳ không xuất hiện trong bảng xếp hạng gây hiểu lầm.
- Một Agent xử lý nhanh nhưng điểm chất lượng thấp không đứng đầu bảng xếp hạng.

**Tham chiếu:** BR-20.3 → issue [#23](https://github.com/crmsaassaudi/product-management/issues/23). BR-20.1, BR-20.2, BR-20.5 → issue [#77](https://github.com/crmsaassaudi/product-management/issues/77) — thay cho issue [#22](https://github.com/crmsaassaudi/product-management/issues/22), vốn đặc tả BR-20.2 theo danh sách tên kênh.

---

### FEAT-21 — Giao diện xử lý hội thoại của Agent `[Đã triển khai]`

**Mô tả nghiệp vụ:** Không gian làm việc chính của Agent — nơi xem danh sách hội thoại, trò chuyện với khách hàng, và thao tác mọi nghiệp vụ (gán, chuyển tiếp, ghi chú, đóng...) — được thiết kế để Agent xử lý nhanh, không bỏ sót, và luôn biết trạng thái hiện tại của từng hội thoại.

**Actor:** Agent.

**Luồng chính:**

1. Agent đăng nhập, thấy danh sách hội thoại được lọc theo phạm vi được phân công (của tôi, hàng đợi nhóm, theo kênh...).
2. Chọn một hội thoại để mở khung chat, xem lịch sử, thông tin khách hàng liên quan (cơ hội bán hàng, yêu cầu hỗ trợ đã có...).
3. Soạn và gửi tin nhắn, đính kèm file, chèn mẫu tin nhắn nhanh, thả biểu tượng phản hồi nhanh, hoặc thực hiện các thao tác quản lý hội thoại (gán, chuyển tiếp, ghi chú, giải quyết/đóng).

**Quy tắc nghiệp vụ:**

- BR-21.1: Agent PHẢI lọc được danh sách hội thoại theo: trạng thái, người/nhóm được gán, kênh, nhãn, phân khúc khách hàng, có tin chưa đọc, đang vi phạm/sắp vi phạm SLA, và khoảng thời gian; và PHẢI lưu lại được các bộ lọc mình dùng thường xuyên.
- BR-21.2: Một hội thoại Agent đang xem hoặc đang soạn dở tin nhắn PHẢI luôn hiển thị trong danh sách của Agent đó, kể cả khi vừa đổi trạng thái khiến nó không còn khớp bộ lọc đang chọn — tránh hội thoại "biến mất" đột ngột giữa lúc đang thao tác.
- BR-21.3: Agent PHẢI thao tác được các nghiệp vụ chính (gán/chuyển tiếp/giải quyết/tiếp quản) bằng phím tắt, không bắt buộc phải dùng chuột cho các thao tác lặp lại nhiều lần trong ngày.
- BR-21.4: Ô soạn tin PHẢI bị khóa và hiển thị rõ lý do kèm cách xử lý tiếp theo khi: hết Cửa sổ phản hồi của kênh (xem BR-12.9), hội thoại đã Đã đóng, Agent đang Ngoại tuyến, hoặc Agent không có quyền trả lời hội thoại đó — Agent phải biết ngay lý do, không đoán mò. Việc một Agent khác đang soạn tin KHÔNG khóa ô soạn tin, chỉ hiển thị cảnh báo theo BR-13.4.
- BR-21.5: Khi giải quyết một hội thoại, doanh nghiệp CÓ THỂ yêu cầu Agent bắt buộc ghi lý do/ghi chú giải quyết trước khi xác nhận.
- BR-21.6: Hệ thống PHẢI phát âm thanh thông báo khi có tin nhắn khách hàng mới trong lúc Agent không đang xem đúng hội thoại đó hoặc không đang mở tab trình duyệt, để không bỏ lỡ.
- BR-21.7: Khi có lời mời nhận hội thoại mới, hệ thống PHẢI hiển thị rõ ràng với thời gian đếm ngược còn lại để phản hồi.
- BR-21.8: Agent CÓ THỂ tìm kiếm và liên kết/gộp một hội thoại của Hồ sơ khách hàng tạm với một khách hàng có sẵn trong CRM (thao tác xác nhận thủ công theo BR-02.4), gỡ lại một lần gộp sai (BR-02.8), và đánh dấu một số điện thoại/email là Định danh dùng chung (BR-02.4b).
- BR-21.9: Với hội thoại đang chờ trong hàng đợi mà Agent chưa nhận, hệ thống PHẢI hiển thị số lượng khách đang chờ và thời gian chờ lâu nhất, để Agent chủ động nhận thêm việc khi rảnh.
- BR-21.10 `[Yêu cầu mới]`: Nội dung Agent đang soạn dở PHẢI được giữ lại cho tới khi Agent gửi hoặc chủ động xóa, kể cả khi Agent chuyển sang hội thoại khác, đóng màn hình, hoặc bị gián đoạn kết nối. Lý do: mất một đoạn trả lời dài đã soạn là thiệt hại năng suất trực tiếp và khiến khách hàng phải chờ lại từ đầu.
- BR-21.11 `[Yêu cầu mới]`: Agent PHẢI thao tác được hàng loạt trên nhiều hội thoại đã chọn — tối thiểu: gắn nhãn, chuyển tiếp, và giải quyết kèm lý do xử lý. Mọi thao tác hàng loạt PHẢI hiển thị trước số hội thoại sẽ bị tác động và PHẢI để lại vết theo BR-15.4. Lý do: sau một đợt cao điểm hoặc một sự cố sản phẩm, một Agent có thể phải xử lý hàng chục hội thoại cùng một kết luận; bắt thao tác từng cái là lãng phí có hệ thống.

**Tiêu chí chấp nhận:**

- Agent tìm được và xử lý hội thoại cần thiết mà không cần rời màn hình chính.
- Không có hội thoại nào "biến mất" bất ngờ khỏi danh sách trong lúc Agent đang thao tác trên nó.
- Ô soạn tin bị khóa luôn kèm lý do rõ ràng.
- Đoạn tin nhắn soạn dở vẫn còn nguyên sau khi Agent chuyển đi hội thoại khác rồi quay lại.
- Đóng 30 hội thoại cùng một lý do xử lý làm được trong một thao tác.

**Tham chiếu:** BR-21.10, BR-21.11 → issue [#78](https://github.com/crmsaassaudi/product-management/issues/78).

---

### FEAT-22 — Trải nghiệm trò chuyện của khách hàng trên Live Chat Widget `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp một khung chat nhúng trực tiếp trên website của doanh nghiệp để khách truy cập website có thể trò chuyện trực tiếp với Agent (hoặc Bot) mà không cần rời trang hay cài đặt thêm ứng dụng.

**Actor:** Khách truy cập website (Visitor).

**Luồng chính:**

1. Khách truy cập website thấy nút mở khung chat ở góc màn hình.
2. Mở khung chat, (tùy cấu hình) điền một số thông tin cơ bản trước khi bắt đầu (tên, email...).
3. Gửi tin nhắn, nhận phản hồi của Agent/Bot theo thời gian thực; có thể gửi kèm hình ảnh/tệp/ghi âm.
4. Khách hàng quay lại website (kể cả lần sau, kể cả đổi trang) vẫn tiếp tục đúng hội thoại đang dở, không phải bắt đầu lại từ đầu.

**Quy tắc nghiệp vụ:**

- BR-22.1: Widget NÊN nhận ra lại đúng một khách truy cập khi họ quay lại website trên cùng thiết bị và trình duyệt, để hội thoại của họ tiếp diễn đúng mạch. Việc ghi nhớ này PHẢI thông báo được cho khách truy cập theo yêu cầu pháp lý áp dụng cho doanh nghiệp đó, và khách PHẢI có cách tự kết thúc phiên và xóa lịch sử trò chuyện khỏi thiết bị của mình (xem FEAT-23). Khi không nhận ra lại được — khách đổi thiết bị, đổi trình duyệt, hoặc đã tự xóa dữ liệu — hệ thống KHÔNG ĐƯỢC coi như đó là một người hoàn toàn mới một cách im lặng: widget PHẢI cho khách tự nối lại hội thoại cũ bằng email hoặc số điện thoại họ đã cung cấp trước đó.
- BR-22.2: Widget PHẢI hiển thị rõ trạng thái gửi của từng tin nhắn khách hàng gửi đi (đang gửi, đã gửi, đã nhận, đã đọc), và cho phép gửi lại nếu gửi thất bại.
- BR-22.3: Widget CÓ THỂ yêu cầu khách điền một số thông tin (tên, email, nhu cầu) trước khi bắt đầu trò chuyện lần đầu, theo cấu hình doanh nghiệp.
- BR-22.4: Khi ngoài giờ làm việc, widget PHẢI thông báo rõ cho khách hàng biết doanh nghiệp hiện không có ai trực tuyến.
- BR-22.5: Khi hội thoại kết thúc (khách hàng chủ động hoặc Agent đóng), widget PHẢI thông báo rõ và cho khách hàng bắt đầu một hội thoại mới nếu muốn tiếp tục.
- BR-22.6: Widget PHẢI cho khách hàng chấm điểm hài lòng (CSAT) ngay tại chỗ khi được mời, không cần rời khỏi website.
- BR-22.7: Widget PHẢI hoạt động đúng trên cả máy tính và thiết bị di động, tự thích ứng ngôn ngữ hiển thị theo ngôn ngữ trình duyệt của khách truy cập.
- BR-22.8 `[Yêu cầu mới]`: Doanh nghiệp PHẢI tùy biến được nhận diện thương hiệu của widget — màu sắc, biểu tượng, vị trí trên trang, lời chào mở đầu, ảnh và tên hiển thị của người trả lời — mà không cần nhà cung cấp can thiệp. Lý do: widget xuất hiện trên website của khách hàng doanh nghiệp và mang thương hiệu của họ; một khung chat không tùy biến được là một khung chat họ sẽ không nhúng.
- BR-22.9 `[Yêu cầu mới]`: Doanh nghiệp CÓ THỂ cấu hình widget **chủ động mời trò chuyện** theo hành vi của khách trên website (trang đang xem, thời gian ở lại trang, số lần quay lại, giá trị giỏ hàng). Lời mời chủ động PHẢI giới hạn số lần với cùng một khách và PHẢI dừng lại khi khách từ chối, không lặp đi lặp lại gây phiền.

**Tiêu chí chấp nhận:**

- Khách truy cập quay lại website sau vài ngày trên cùng thiết bị vẫn thấy đúng lịch sử trò chuyện cũ.
- Khách truy cập đổi sang thiết bị khác vẫn nối lại được hội thoại cũ bằng email/số điện thoại đã cung cấp.
- Khách truy cập tự xóa được lịch sử trò chuyện trên thiết bị của mình.
- Doanh nghiệp tự đổi được màu sắc, biểu tượng và lời chào của widget mà không cần yêu cầu nhà cung cấp.
- Tin nhắn gửi thất bại do mất mạng có thể gửi lại được, không mất nội dung đã soạn.
- Widget hiển thị đúng và dùng được trên di động.

**Tham chiếu:** BR-22.1, BR-22.8, BR-22.9 → issue [#79](https://github.com/crmsaassaudi/product-management/issues/79).

---

### FEAT-23 — Lưu trữ, xuất và xóa dữ liệu hội thoại `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Xác định doanh nghiệp giữ lại nội dung trao đổi với khách hàng trong bao lâu, ai lấy được nó ra, và xóa đi thế nào khi khách hàng hoặc pháp luật yêu cầu. Đây vừa là cam kết doanh nghiệp đưa ra với khách hàng của họ, vừa là điều khoản trong hợp đồng giữa doanh nghiệp và nhà cung cấp hệ thống. Nhiều quy tắc trong tài liệu này (BR-02.6, BR-22.1) đã viện dẫn cam kết đó nhưng chưa có mục nào đặc tả nó.

**Actor:** Quản trị viên (cấu hình chính sách); Giám sát viên (xuất dữ liệu phục vụ khiếu nại/tranh chấp); Khách hàng (người yêu cầu xóa dữ liệu của mình).

**Quy tắc nghiệp vụ:**

- BR-23.1: Doanh nghiệp PHẢI cấu hình được **Thời hạn lưu trữ** cho nội dung hội thoại và tệp đính kèm. Trong suốt thời hạn đó nội dung PHẢI xem lại được đầy đủ; hết thời hạn, nội dung PHẢI được xóa tự động.
- BR-23.2: Thời hạn lưu trữ **số liệu tổng hợp** phục vụ báo cáo (khối lượng, tốc độ phản hồi, CSAT) PHẢI tách khỏi thời hạn lưu trữ nội dung hội thoại — xóa nội dung theo BR-23.1 không được làm mất số liệu lịch sử vận hành của doanh nghiệp.
- BR-23.3: Khi một khách hàng yêu cầu xóa dữ liệu cá nhân của họ, doanh nghiệp PHẢI thực hiện được yêu cầu đó trên toàn bộ hội thoại của khách hàng đó ở mọi kênh trong một lần thao tác, và PHẢI nhận được xác nhận việc xóa đã hoàn tất để trả lời lại khách hàng.
- BR-23.4: Giám sát viên PHẢI xuất được toàn bộ nội dung một hội thoại ra một tệp đọc được, phục vụ khiếu nại hoặc tranh chấp; ghi chú nội bộ chỉ được kèm theo nếu người xuất có quyền xem ghi chú nội bộ, và tệp xuất PHẢI ghi rõ có kèm ghi chú nội bộ hay không.
- BR-23.5: Khi doanh nghiệp ngừng sử dụng hệ thống, họ PHẢI lấy được toàn bộ dữ liệu hội thoại của mình trong một khoảng thời gian được thông báo trước, trước khi dữ liệu bị xóa.
- BR-23.6: Khách truy cập website PHẢI tự kết thúc phiên trò chuyện và xóa lịch sử trò chuyện khỏi thiết bị của mình được, độc lập với dữ liệu doanh nghiệp lưu ở phía hệ thống.
- BR-23.7 `[Yêu cầu mới]`: Doanh nghiệp PHẢI đặt được **Tạm dừng xóa theo yêu cầu pháp lý** lên dữ liệu của một hội thoại hoặc một khách hàng đang trong diện tranh chấp/điều tra. Trong thời gian tạm dừng, thời hạn lưu trữ (BR-23.1) và yêu cầu xóa dữ liệu của khách hàng (BR-23.3) KHÔNG được thi hành trên phần dữ liệu đó; khi gỡ tạm dừng, các quy tắc trở lại hiệu lực ngay. Mỗi lần đặt/gỡ PHẢI ghi rõ ai đặt, căn cứ gì, và dự kiến tới khi nào. Lý do: một tranh chấp thường kéo dài hơn thời hạn lưu trữ, và xóa mất bằng chứng giữa lúc đang tranh chấp là rủi ro pháp lý lớn hơn nhiều so với việc giữ dữ liệu thêm một thời gian có căn cứ.
- BR-23.8 `[Yêu cầu mới]`: Hệ thống PHẢI phát hiện và che dữ liệu nhạy cảm mà khách hàng gõ trực tiếp vào khung chat (số thẻ thanh toán, mã định danh cá nhân, mã xác thực một lần), theo danh mục doanh nghiệp cấu hình. Dữ liệu đã che KHÔNG ĐƯỢC hiển thị đầy đủ cho Agent, không nằm trong kết quả tìm kiếm, và không nằm trong tệp xuất ra. Lý do: khách hàng vẫn gõ những thông tin này vào chat dù được dặn không nên, và một khi đã lưu nguyên văn thì doanh nghiệp gánh nghĩa vụ tuân thủ mà họ không định gánh.

**Tiêu chí chấp nhận:**

- Hội thoại quá Thời hạn lưu trữ không còn xem lại được, nhưng số liệu tổng hợp của kỳ đó vẫn còn trong báo cáo.
- Một yêu cầu xóa dữ liệu của khách hàng xử lý được trên mọi kênh trong một lần, kèm xác nhận hoàn tất.
- Hội thoại đang bị Tạm dừng xóa theo yêu cầu pháp lý không bị xóa dù đã quá thời hạn lưu trữ, và lý do tạm dừng tra cứu được.
- Giám sát viên xuất được hội thoại ra tệp để gửi kèm hồ sơ khiếu nại.
- Số thẻ khách hàng gõ vào chat không hiển thị đầy đủ cho Agent và không nằm trong tệp xuất.

**Tham chiếu:** FEAT-23 → issue [#68](https://github.com/crmsaassaudi/product-management/issues/68).

---

### FEAT-24 — Giờ làm việc & lịch nghỉ `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Giờ làm việc là căn cứ tính cam kết thời gian phản hồi, quyết định khi nào gửi thông báo ngoài giờ, khi nào ngừng nhận hội thoại mới vào hàng đợi, và khi nào widget báo với khách rằng hiện không có ai trực tuyến. Đây là một đối tượng nghiệp vụ chịu lực cho nhiều tính năng khác (BR-01.5, BR-02.7, BR-05.8, BR-08.4, BR-22.4) nhưng chưa từng được đặc tả.

**Actor:** Quản trị viên (khai báo lịch); Agent/Giám sát viên (người thấy trạng thái trong/ngoài giờ).

**Quy tắc nghiệp vụ:**

- BR-24.1: Doanh nghiệp PHẢI khai báo được lịch làm việc theo từng ngày trong tuần, kèm múi giờ áp dụng.
- BR-24.2: Doanh nghiệp PHẢI khai báo được **lịch nghỉ lễ** và ngày nghỉ đột xuất. Ngày nghỉ được tính là ngoài giờ làm việc cho mọi mục đích: tính cam kết SLA, gửi thông báo tự động, và quyết định nhận hội thoại vào hàng đợi.
- BR-24.3: Một kênh hoặc một Hộp thư CÓ THỂ có lịch làm việc riêng khác lịch chung của doanh nghiệp. Khi một hội thoại thuộc phạm vi nhiều lịch, thứ tự áp dụng là: lịch riêng của Hộp thư › lịch riêng của kênh › lịch chung của doanh nghiệp.
- BR-24.4: Mọi phép tính thời hạn cam kết (BR-08.4) PHẢI dùng đúng lịch áp dụng cho hội thoại đó tại thời điểm phát sinh. Thay đổi lịch làm việc KHÔNG ĐƯỢC làm thay đổi hồi tố kết quả đáp ứng/vi phạm của các hội thoại đã chốt.
- BR-24.5: Agent và Giám sát viên PHẢI nhìn thấy được doanh nghiệp/kênh mình đang trực hiện đang trong hay ngoài giờ làm việc, và thời điểm chuyển tiếp gần nhất.

**Tiêu chí chấp nhận:**

- Ngày nghỉ lễ đã khai báo không bị tính vào thời hạn cam kết phản hồi.
- Hộp thư có lịch riêng áp dụng đúng lịch riêng, không áp nhầm lịch chung của doanh nghiệp.
- Sửa lịch làm việc hôm nay không làm đổi kết quả SLA của các hội thoại đã chốt trước đó.

**Tham chiếu:** FEAT-24 → issue [#69](https://github.com/crmsaassaudi/product-management/issues/69).

---

### FEAT-25 — Nhật ký thay đổi cấu hình Omnichat `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Lưu vết ai đã đổi cấu hình gì trong Omnichat, để điều tra được khi một đội đột nhiên không nhận được hội thoại, một cam kết thời gian phản hồi đột nhiên khác đi, hay một nhóm Agent đột nhiên thấy dữ liệu mà trước đó họ không thấy.

**Actor:** Quản trị viên (người thực hiện thay đổi và người tra cứu); Giám sát viên (tra cứu khi điều tra sự cố vận hành).

**Quy tắc nghiệp vụ:**

- BR-25.1: Mọi thay đổi cấu hình vận hành của Omnichat — kênh, Hộp thư, quy tắc phân công, Chính sách SLA, chính sách leo thang, chính sách tự động đóng, mẫu tin nhắn dùng chung, cấu hình Bot, giờ làm việc — PHẢI được ghi lại: ai đổi, đổi gì, từ giá trị nào sang giá trị nào, vào lúc nào.
- BR-25.2: Những thay đổi làm đổi **ai được thấy gì** trong workspace — cụ thể: gán nhóm/Agent vào Hộp thư (BR-17.1), phạm vi hiển thị mẫu tin nhắn (BR-18.1), phạm vi dữ liệu của báo cáo (BR-19.2), và quyền xem hội thoại đang chờ trong hàng đợi (BR-05.7) — thuộc **Nguyên tắc đóng cho Nhật ký cấu hình quyền** của toàn hệ thống. Nhóm này áp dụng đúng quy tắc chặt hơn đã chốt ở cấp tổ chức, không áp một quy tắc riêng của Omnichat, và không áp quy tắc chung tại BR-15.5.
- BR-25.3: Quản trị viên PHẢI tra cứu được nhật ký này theo người thực hiện, theo loại cấu hình, và theo khoảng thời gian.
- BR-25.4: Thao tác chỉ xem cấu hình, và thao tác thử nghiệm cấu hình trong môi trường thử (FEAT-34), KHÔNG thuộc phạm vi nhật ký này.

**Tiêu chí chấp nhận:**

- Một đội đột nhiên ngừng nhận hội thoại thì tra ra được ai đã đổi cấu hình Hộp thư, đổi gì, lúc nào.
- Thay đổi thuộc nhóm BR-25.2 luôn có vết, không có trường hợp thao tác thành công mà thiếu vết.

**Tham chiếu:** BR-25.2 → [ADR-0003](../docs/adr/0003-permission-config-audit-log-fail-closed.md). FEAT-25 → issue [#70](https://github.com/crmsaassaudi/product-management/issues/70).

---

### FEAT-26 — Giám sát và can thiệp thời gian thực `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho Trưởng nhóm và Giám sát viên một chỗ duy nhất nhìn thấy tình hình phục vụ **đang diễn ra** và can thiệp ngay, thay vì phát hiện vấn đề khi đọc báo cáo cuối ngày. Hệ thống hiện chỉ cảnh báo từng hội thoại riêng lẻ (FEAT-09) mà không có nơi nào nhìn được toàn cảnh, nên người điều hành không biết nên can thiệp vào đâu trước.

**Actor:** Trưởng nhóm; Giám sát viên.

**Quy tắc nghiệp vụ:**

- BR-26.1: Trưởng nhóm/Giám sát viên PHẢI nhìn thấy, cập nhật liên tục và không phải tự thao tác gì: số khách đang chờ và thời gian chờ lâu nhất theo từng hàng đợi/Hộp thư/kênh; danh sách hội thoại đang vi phạm hoặc sắp vi phạm cam kết; và trạng thái làm việc hiện tại của từng Agent kèm số việc đang giữ so với năng lực còn lại.
- BR-26.2: Mọi số liệu trên màn hình này PHẢI giới hạn đúng trong phạm vi đội/Hộp thư mà người xem chịu trách nhiệm — một Trưởng nhóm không nhìn thấy tình hình của đội khác.
- BR-26.3: Doanh nghiệp PHẢI đặt được ngưỡng cảnh báo vận hành theo Hộp thư/kênh (số khách chờ, thời gian chờ lâu nhất, số Agent sẵn sàng tối thiểu). Chạm ngưỡng PHẢI cảnh báo chủ động tới người chịu trách nhiệm, không chờ họ tự nhìn thấy.
- BR-26.4: Trưởng nhóm/Giám sát viên PHẢI **nhắc bài riêng cho Agent** ngay trong lúc hội thoại đang diễn ra, và khách hàng KHÔNG ĐƯỢC nhìn thấy nội dung nhắc đó dưới bất kỳ hình thức nào.
- BR-26.5: Trưởng nhóm/Giám sát viên PHẢI **tiếp quản** một hội thoại đang diễn ra khi cần can thiệp gấp; Agent đang phụ trách PHẢI được thông báo rõ và biết mình còn trách nhiệm gì.
- BR-26.6: Trưởng nhóm/Giám sát viên PHẢI **gán lại hàng loạt** hội thoại của một Agent sang người/nhóm khác trong một thao tác — dùng khi Agent nghỉ đột xuất, hết ca, hoặc nghỉ việc. Thao tác này PHẢI hiển thị trước số hội thoại bị tác động và PHẢI để lại vết theo BR-15.4.
- BR-26.7: Mọi can thiệp theo BR-26.4, BR-26.5, BR-26.6 PHẢI được ghi lại kèm người thực hiện và lý do, để phân biệt được đâu là kết quả làm việc của Agent và đâu là kết quả có người can thiệp — nếu không, mọi chỉ số hiệu suất cá nhân đều mất ý nghĩa.

**Tiêu chí chấp nhận:**

- Trưởng nhóm biết đội mình đang có bao nhiêu khách chờ và người chờ lâu nhất bao lâu, ngay tại thời điểm đang nhìn.
- Hàng đợi vượt ngưỡng thì người chịu trách nhiệm nhận cảnh báo, không phải tự phát hiện.
- Nhắc bài cho Agent giữa chừng hội thoại không lọt ra phía khách hàng.
- Một Agent nghỉ đột xuất thì toàn bộ việc đang giữ chuyển sang người khác trong một thao tác.

**Tham chiếu:** FEAT-26 → issue [#80](https://github.com/crmsaassaudi/product-management/issues/80).

---

### FEAT-27 — Quản lý chất lượng hội thoại (QA) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Đo chất lượng phục vụ bằng việc chấm điểm nội dung hội thoại theo tiêu chí doanh nghiệp tự đặt, thay vì chỉ dựa vào tốc độ và điểm CSAT. Điểm CSAT có tỷ lệ phản hồi thấp và thiên lệch về hai đầu cảm xúc, nên không đủ để đánh giá con người và không chỉ ra được sai ở khâu nào.

**Actor:** Chuyên viên chất lượng (người chấm); Agent (người được chấm và phản hồi); Trưởng nhóm/Giám sát viên (người dùng kết quả).

**Quy tắc nghiệp vụ:**

- BR-27.1: Doanh nghiệp PHẢI tự định nghĩa được **phiếu chấm chất lượng**: các tiêu chí, trọng số từng tiêu chí, thang điểm, và các tiêu chí thuộc diện lỗi nghiêm trọng làm điểm tổng về 0 bất kể các tiêu chí khác.
- BR-27.2: Doanh nghiệp PHẢI cấu hình được cách **lấy mẫu hội thoại để chấm**: theo số lượng hoặc tỷ lệ trên mỗi Agent mỗi kỳ, và theo điều kiện lọc (kênh, nhãn, lý do xử lý, hội thoại vi phạm SLA, hội thoại có điểm CSAT thấp, hội thoại bị chuyển tiếp nhiều lần).
- BR-27.3: Bất kỳ ai có quyền cũng PHẢI **gắn cờ một hội thoại cần được chấm** ngay khi phát hiện, không phải chờ tới kỳ lấy mẫu.
- BR-27.4: Kết quả chấm PHẢI được phản hồi tới đúng Agent liên quan kèm nhận xét cụ thể, và Agent PHẢI **phúc khảo** được nếu không đồng ý; kết quả phúc khảo PHẢI được ghi lại. Lý do: một kết quả chấm không phản hồi lại cho người bị chấm thì không cải thiện được gì, và một kết quả không cho phúc khảo sẽ bị Agent coi là không công bằng.
- BR-27.5: Doanh nghiệp PHẢI **hiệu chuẩn** được giữa các Chuyên viên chất lượng — cho nhiều người cùng chấm một hội thoại và đối chiếu chênh lệch — để điểm chất lượng nói lên chất lượng thật chứ không phải độ khó tính của người chấm.
- BR-27.6: Điểm chất lượng trung bình của từng Agent PHẢI xuất hiện trong Báo cáo hiệu suất Agent (FEAT-20), cạnh các chỉ số tốc độ và điểm CSAT.
- BR-27.7: Việc chấm điểm KHÔNG ĐƯỢC làm thay đổi nội dung, trạng thái hay lịch sử của hội thoại được chấm.

**Tiêu chí chấp nhận:**

- Doanh nghiệp tự dựng được phiếu chấm của mình mà không cần nhà cung cấp can thiệp.
- Mỗi Agent đều có đủ số hội thoại được chấm trong kỳ theo tỷ lệ đã cấu hình.
- Agent nhìn thấy kết quả chấm của mình kèm nhận xét, và gửi phúc khảo được.
- Điểm chất lượng đứng cạnh tốc độ và CSAT trong cùng bảng đánh giá.

**Tham chiếu:** FEAT-27 → issue [#81](https://github.com/crmsaassaudi/product-management/issues/81).

---

### FEAT-28 — Ca trực, tuân thủ ca & bàn giao ca `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho doanh nghiệp xếp lịch trực cho Agent, đối chiếu lịch đó với thực tế, và bảo đảm không hội thoại nào bị bỏ rơi khi đổi ca. Hiện hệ thống chỉ biết **ai đang trực tuyến**, không biết **ai đáng lẽ phải trực tuyến** — nên không phát hiện được thiếu người trước khi khách hàng phải chờ, và không có quy tắc nào cho thời điểm một Agent kết thúc ca với hội thoại còn đang mở.

**Actor:** Giám sát viên (xếp lịch); Trưởng nhóm (điều chỉnh trong ca, duyệt bàn giao); Agent.

**Quy tắc nghiệp vụ:**

- BR-28.1: Doanh nghiệp PHẢI xếp được **ca trực** cho từng Agent theo ngày và khung giờ, gắn với Hộp thư/kênh mà Agent đó phụ trách trong ca.
- BR-28.2: Hệ thống PHẢI đối chiếu ca trực đã xếp với trạng thái làm việc thực tế (BR-06.4) và đưa ra **mức tuân thủ ca**, dùng trong Báo cáo hiệu suất Agent (FEAT-20).
- BR-28.3: Khi số Agent thực tế sẵn sàng thấp hơn mức đã xếp lịch cho một khung giờ, hệ thống PHẢI cảnh báo cho Trưởng nhóm **trước khi** hàng đợi ùn lại, không phải sau khi khách đã chờ quá hạn.
- BR-28.4: Một Agent KHÔNG ĐƯỢC kết thúc ca khi còn hội thoại đang mở chưa xử lý xong. Hệ thống PHẢI: liệt kê các hội thoại còn giữ, đề xuất người nhận phù hợp, yêu cầu ghi chú bàn giao cho từng hội thoại (BR-15.2), và chỉ cho kết thúc ca sau khi mọi hội thoại đều có người nhận hoặc đã trở lại hàng đợi. Trưởng nhóm CÓ THỂ bỏ qua ràng buộc này trong trường hợp khẩn cấp, và việc bỏ qua PHẢI được ghi lại.
- BR-28.5: Doanh nghiệp PHẢI xem được **khối lượng hội thoại theo giờ trong ngày và theo ngày trong tuần của các kỳ trước** như căn cứ xếp ca — nếu không, việc xếp ca chỉ là phỏng đoán và Báo cáo hiệu suất Agent không phục vụ được mục đích hoạch định nhân sự mà nó tuyên bố.
- BR-28.6: Ca trực PHẢI tôn trọng Giờ làm việc và lịch nghỉ đã khai báo (FEAT-24); xếp ca vào ngày nghỉ đã khai báo PHẢI bị cảnh báo.

**Tiêu chí chấp nhận:**

- Giám sát viên xếp được lịch trực tuần cho cả đội và nhìn được mức tuân thủ sau đó.
- Một khung giờ thiếu người so với lịch thì Trưởng nhóm biết trước khi khách hàng bị ảnh hưởng.
- Agent hết ca không bỏ lại hội thoại nào không có người nhận.
- Số liệu khối lượng theo giờ của các tuần trước dùng được trực tiếp để xếp ca tuần sau.

**Tham chiếu:** FEAT-28 → issue [#82](https://github.com/crmsaassaudi/product-management/issues/82).

---

### FEAT-29 — Danh mục lý do xử lý & quản trị nhãn `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Định nghĩa hai công cụ phân loại mà nhiều tính năng khác trong tài liệu này đang phụ thuộc vào nhưng chưa nơi nào đặc tả: **Lý do xử lý** (kết quả một hội thoại) và **Nhãn** (từ khóa gắn thêm để lọc, định tuyến, tự động hóa và phân tích). Không có hai thứ này thì báo cáo phân tích nguyên nhân khách hàng liên hệ, phân tích theo nhãn và quy tắc tự động đóng theo nhãn đều không có dữ liệu để chạy.

**Actor:** Quản trị viên (định nghĩa danh mục); Agent (chọn khi xử lý); Giám sát viên/Chuyên viên chất lượng (người dùng kết quả phân tích).

**Quy tắc nghiệp vụ:**

- BR-29.1: Doanh nghiệp PHẢI định nghĩa được **danh mục lý do xử lý** có phân cấp (nhóm nguyên nhân › nguyên nhân cụ thể), và PHẢI đặt được danh mục riêng theo Hộp thư hoặc theo kênh khi các đội có bản chất công việc khác nhau.
- BR-29.2: Doanh nghiệp PHẢI cấu hình được lý do xử lý là **bắt buộc chọn** khi giải quyết hội thoại; khi bắt buộc, Agent không hoàn tất được việc giải quyết nếu chưa chọn.
- BR-29.3: Doanh nghiệp PHẢI định nghĩa được **danh mục lý do chuyển tiếp** dùng cho BR-07.9, tách khỏi danh mục lý do xử lý.
- BR-29.4: Một hội thoại PHẢI gắn được nhiều nhãn cùng lúc, ở bất kỳ thời điểm nào trong vòng đời, bởi cả người lẫn quy tắc tự động hóa (FEAT-33).
- BR-29.5: Doanh nghiệp PHẢI kiểm soát được ai được tạo nhãn mới; khi để mở cho Agent tự tạo, hệ thống PHẢI gợi ý nhãn đã có trước khi cho tạo mới, để tránh nhiều nhãn cùng nghĩa làm hỏng phân tích.
- BR-29.6: Quản trị viên PHẢI **gộp, đổi tên và ngừng sử dụng** một nhãn hoặc một lý do xử lý. Ngừng sử dụng nghĩa là không chọn được cho hội thoại mới nhưng dữ liệu lịch sử vẫn giữ nguyên; báo cáo của các kỳ trước KHÔNG ĐƯỢC thay đổi vì thao tác này.
- BR-29.7: Đổi tên hoặc gộp PHẢI được ghi lại theo FEAT-25, vì nó làm thay đổi cách đọc mọi báo cáo dùng tới nhãn/lý do đó.

**Tiêu chí chấp nhận:**

- Agent không đóng được hội thoại nếu doanh nghiệp đã đặt lý do xử lý là bắt buộc.
- Báo cáo phân bố theo lý do xử lý có dữ liệu đầy đủ, không có nhóm "không xác định" chiếm phần lớn.
- Hai nhãn cùng nghĩa gộp lại được mà không mất dữ liệu lịch sử.
- Ngừng sử dụng một nhãn không làm đổi con số của báo cáo các kỳ trước.

**Tham chiếu:** FEAT-29 → issue [#83](https://github.com/crmsaassaudi/product-management/issues/83).

---

### FEAT-30 — Kỹ năng & định tuyến theo kỹ năng `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép doanh nghiệp mô tả **Agent nào làm được việc gì** và định tuyến hội thoại theo đó. Hiện hệ thống chỉ chọn người theo kênh được phép hỗ trợ và tải công việc, nên một khách hàng nói ngôn ngữ khác hoặc hỏi về một dòng sản phẩm chuyên biệt vẫn có thể rơi vào Agent không xử lý được — và cách duy nhất để sửa là Agent tự chuyển tiếp bằng tay, tức là đẩy chi phí định tuyến sang cho con người.

**Actor:** Quản trị viên (khai báo kỹ năng); Giám sát viên (gán kỹ năng cho Agent); Hệ thống (áp dụng khi phân công).

**Quy tắc nghiệp vụ:**

- BR-30.1: Doanh nghiệp PHẢI khai báo được **danh mục kỹ năng** của riêng mình (ngôn ngữ, dòng sản phẩm, cấp độ chuyên môn, thẩm quyền xử lý khiếu nại...), và gán kỹ năng cho từng Agent kèm **mức thành thạo**.
- BR-30.2: Một hội thoại PHẢI xác định được kỹ năng nó đòi hỏi — từ quy tắc phân công (BR-04.3), từ nhãn đã gắn, từ Hộp thư, từ ngôn ngữ khách hàng đang dùng, hoặc do Bot xác định trước khi bàn giao.
- BR-30.3: Khi có kỹ năng yêu cầu, hệ thống PHẢI ưu tiên Agent đủ kỹ năng và có mức thành thạo cao hơn, trong số những người còn khả năng nhận việc theo BR-04.7.
- BR-30.4: Doanh nghiệp PHẢI cấu hình được cách xử lý khi **không có ai đủ kỹ năng đang sẵn sàng**: chờ thêm một khoảng rồi hạ dần yêu cầu kỹ năng, hoặc mở rộng sang nhóm khác, hoặc giữ trong hàng đợi và cảnh báo. Hệ thống KHÔNG ĐƯỢC âm thầm bỏ qua yêu cầu kỹ năng — nếu buộc phải hạ chuẩn, hội thoại PHẢI được đánh dấu và người phụ trách PHẢI biết mình đang nhận việc ngoài kỹ năng của mình.
- BR-30.5: Giám sát viên PHẢI thấy được **phân bố kỹ năng của đội so với nhu cầu thực tế** theo kỳ, để biết cần đào tạo thêm hoặc tuyển thêm kỹ năng nào.

**Tiêu chí chấp nhận:**

- Khách hàng nói một ngôn ngữ được gán cho Agent xử lý được ngôn ngữ đó, không phải cho người rảnh nhất.
- Không ai đủ kỹ năng thì hội thoại được xử lý theo đúng cách doanh nghiệp đã chọn, không rơi vào im lặng.
- Hội thoại buộc phải hạ chuẩn kỹ năng luôn được đánh dấu, người nhận biết rõ.

**Tham chiếu:** FEAT-30 → issue [#84](https://github.com/crmsaassaudi/product-management/issues/84).

---

### FEAT-31 — Chặn spam & xử lý lạm dụng `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Bảo vệ đội ngũ và số liệu vận hành khỏi tin nhắn rác và người dùng lạm dụng. Mọi kênh công khai đều nhận spam; nếu spam được đối xử như hội thoại thật thì nó chạy cam kết SLA, sinh vi phạm, kích hoạt leo thang, chiếm năng lực của Agent và làm sai lệch mọi con số báo cáo.

**Actor:** Agent (đánh dấu); Trưởng nhóm/Quản trị viên (chặn và gỡ chặn); Hệ thống (áp dụng).

**Quy tắc nghiệp vụ:**

- BR-31.1: Agent PHẢI đánh dấu được một hội thoại là **spam**. Hội thoại đã đánh dấu spam PHẢI dừng mọi đồng hồ cam kết, không kích hoạt leo thang, không tính vào khối lượng công việc và không tính vào các chỉ số tốc độ trong báo cáo — nhưng vẫn PHẢI đếm được riêng để doanh nghiệp biết quy mô spam mình đang nhận.
- BR-31.2: Trưởng nhóm/Quản trị viên PHẢI **chặn** được một người gửi, phạm vi theo kênh hoặc toàn bộ doanh nghiệp. Tin nhắn từ người bị chặn PHẢI không tạo hội thoại mới và không hiện trong danh sách việc của Agent.
- BR-31.3: Việc chặn PHẢI **gỡ lại được**, và mọi lần chặn/gỡ chặn PHẢI ghi rõ ai thực hiện và vì lý do gì. Lý do: chặn nhầm một khách hàng thật là mất một khách hàng trong im lặng, nên thao tác này phải soi lại được.
- BR-31.4: Hệ thống PHẢI cảnh báo khi một định danh phát sinh số lượng hội thoại mới bất thường trong thời gian ngắn, để người vận hành xem xét trước khi số liệu bị bóp méo.
- BR-31.5: Agent PHẢI **báo cáo hành vi lạm dụng** (chửi bới, quấy rối, đe dọa) từ một khách hàng lên Trưởng nhóm ngay trong hội thoại. Doanh nghiệp PHẢI cấu hình được cách xử lý tiếp theo: gán cho người có thẩm quyền, gửi cảnh báo tới khách hàng, hoặc kết thúc hội thoại kèm lý do. Agent KHÔNG ĐƯỢC bị buộc phải tiếp tục phục vụ một hội thoại đã được xác nhận là lạm dụng.
- BR-31.6: Đánh dấu spam nhầm PHẢI hoàn tác được, và hội thoại quay lại đúng trạng thái phục vụ bình thường.

**Tiêu chí chấp nhận:**

- Một đợt spam không làm tăng số vi phạm cam kết và không làm giảm chỉ số tốc độ phản hồi.
- Người gửi đã chặn không tạo được hội thoại mới, và gỡ chặn được khi cần.
- Agent gặp khách hàng lạm dụng chuyển được vụ việc lên trên ngay, không phải tự chịu.
- Doanh nghiệp biết được mình nhận bao nhiêu spam trong kỳ.

**Tham chiếu:** FEAT-31 → issue [#85](https://github.com/crmsaassaudi/product-management/issues/85).

---

### FEAT-32 — Yêu cầu liên hệ lại `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho khách hàng một lối ra khi doanh nghiệp không phục vụ được ngay — ngoài giờ làm việc, hàng đợi quá tải, hoặc kênh đã hết Cửa sổ phản hồi. Hiện các tình huống này chỉ kết thúc bằng một tin nhắn tự động; khách hàng không có cách nào để lại yêu cầu và không ai chịu trách nhiệm về việc liên hệ lại.

**Actor:** Khách hàng (người để lại yêu cầu); Agent (người thực hiện); Trưởng nhóm (người theo dõi).

**Quy tắc nghiệp vụ:**

- BR-32.1: Khi doanh nghiệp không phục vụ được ngay, khách hàng PHẢI để lại được một **Yêu cầu liên hệ lại** kèm cách liên hệ mong muốn và khoảng thời gian thuận tiện.
- BR-32.2: Một Yêu cầu liên hệ lại PHẢI là **một việc có người phụ trách và có cam kết thời hạn**, đi qua đúng cơ chế phân công và cam kết SLA như một hội thoại — KHÔNG ĐƯỢC là một tin nhắn trôi trong hàng đợi. Quá hạn PHẢI leo thang theo FEAT-09.
- BR-32.3: Khi Agent thực hiện liên hệ lại, hội thoại gốc PHẢI được nối tiếp đúng mạch, giữ nguyên lịch sử — khách hàng không phải kể lại từ đầu.
- BR-32.4: Khách hàng PHẢI nhận được xác nhận yêu cầu đã được ghi nhận, kèm khoảng thời gian dự kiến được liên hệ lại.
- BR-32.5: Nếu khách hàng chủ động quay lại trước khi được liên hệ, yêu cầu PHẢI tự khép lại để tránh liên hệ trùng.
- BR-32.6: Doanh nghiệp PHẢI đo được số yêu cầu liên hệ lại, tỷ lệ thực hiện đúng hạn, và tỷ lệ không liên hệ lại được — đây là thước đo trực tiếp của năng lực phục vụ ngoài giờ.

**Tiêu chí chấp nhận:**

- Khách hàng nhắn ngoài giờ để lại được yêu cầu và nhận xác nhận, không chỉ nhận một tin nhắn tự động rồi thôi.
- Yêu cầu liên hệ lại quá hạn thì leo thang như một hội thoại vi phạm cam kết.
- Agent liên hệ lại thấy nguyên lịch sử trao đổi trước đó.

**Tham chiếu:** FEAT-32 → issue [#76](https://github.com/crmsaassaudi/product-management/issues/76).

---

### FEAT-33 — Quy tắc tự động hóa `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Một nơi duy nhất để doanh nghiệp tự đặt ra các quy tắc "khi xảy ra việc này, nếu thỏa điều kiện kia, thì làm việc nọ". Hiện việc tự động hóa nằm rải rác trong bốn tính năng cứng — quy tắc phân công, chính sách tự động đóng, chính sách leo thang, và Bot — mỗi cái có mô hình điều kiện riêng, không dùng lại được cho nhau. Hệ quả là mọi nhu cầu tự động hóa mới của một doanh nghiệp đều phải chờ nhà cung cấp làm thêm tính năng.

**Actor:** Quản trị viên (đặt quy tắc); Hệ thống (thực thi).

**Quy tắc nghiệp vụ:**

- BR-33.1: Doanh nghiệp PHẢI tự đặt được quy tắc theo mô hình **Sự kiện → Điều kiện → Hành động**, không cần nhà cung cấp can thiệp.
- BR-33.2: Tập **sự kiện** tối thiểu PHẢI gồm: hội thoại được tạo, khách hàng gửi tin nhắn, hội thoại đổi trạng thái, đổi người phụ trách, được gắn/gỡ nhãn, sắp/đã vi phạm cam kết, Bot bàn giao, hội thoại được giải quyết, và nhận được điểm CSAT.
- BR-33.3: Tập **điều kiện** PHẢI dùng chung với quy tắc phân công (BR-04.3) — cùng một khái niệm nghiệp vụ chỉ được định nghĩa một lần trong toàn hệ thống.
- BR-33.4: Tập **hành động** tối thiểu PHẢI gồm: gắn/gỡ nhãn, đặt độ ưu tiên, gán cho người/nhóm/Hộp thư, đổi trạng thái, gửi tin nhắn cho khách hàng, thông báo nội bộ cho một người/vai trò, áp một Chính sách SLA khác, và gắn cờ cần chấm chất lượng (FEAT-27).
- BR-33.5: Quy tắc PHẢI có thứ tự ưu tiên rõ ràng, bật/tắt được, và **thử nghiệm được trước khi áp cho khách hàng thật** (FEAT-34).
- BR-33.6: Hệ thống PHẢI ngăn quy tắc kích hoạt lẫn nhau thành vòng lặp, và PHẢI giới hạn số lần một quy tắc tác động lên cùng một hội thoại trong một khoảng thời gian — một cấu hình sai KHÔNG ĐƯỢC dẫn tới việc gửi hàng loạt tin nhắn cho khách hàng.
- BR-33.7: Mỗi hội thoại PHẢI tra cứu được **quy tắc nào đã tác động lên nó, lúc nào và kết quả ra sao**; nếu không, người vận hành không giải thích được vì sao hội thoại lại ở tình trạng hiện tại.
- BR-33.8: Thay đổi quy tắc tự động hóa PHẢI được ghi lại theo FEAT-25.

**Tiêu chí chấp nhận:**

- Doanh nghiệp tự đặt được quy tắc "hội thoại gắn nhãn khiếu nại của khách VIP thì đặt ưu tiên cao nhất và báo Trưởng nhóm" mà không cần yêu cầu nhà cung cấp.
- Một quy tắc cấu hình sai không gửi được hàng loạt tin nhắn cho khách hàng.
- Mở một hội thoại là biết được quy tắc nào đã tác động lên nó.

**Tham chiếu:** FEAT-33 → issue [#86](https://github.com/crmsaassaudi/product-management/issues/86).

---

### FEAT-34 — Chế độ thử nghiệm cấu hình `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho quản trị viên thử một thay đổi cấu hình trước khi nó chạm tới khách hàng thật. Định tuyến, cam kết SLA, kịch bản Bot và quy tắc tự động hóa đều là những thứ mà một cấu hình sai gây hậu quả trực tiếp lên khách hàng và chỉ bị phát hiện sau khi đã xảy ra.

**Actor:** Quản trị viên.

**Quy tắc nghiệp vụ:**

- BR-34.1: Quản trị viên PHẢI **thử một quy tắc trên dữ liệu giả định** và xem trước kết quả (hội thoại sẽ đi đâu, cam kết nào được áp, hành động nào chạy) mà không tác động tới bất kỳ hội thoại thật nào.
- BR-34.2: Quản trị viên PHẢI **đối chiếu một quy tắc mới với các hội thoại đã phát sinh trong quá khứ** để thấy nếu quy tắc đó đã có hiệu lực thì kết quả sẽ khác đi thế nào.
- BR-34.3: Doanh nghiệp PHẢI có **kênh thử nghiệm** để chạy thử toàn bộ hành trình mà không lẫn vào dữ liệu vận hành: hội thoại trên kênh thử nghiệm PHẢI được loại khỏi mọi báo cáo và mọi chỉ số đánh giá Agent.
- BR-34.4: Thao tác thử nghiệm KHÔNG ĐƯỢC gửi bất kỳ tin nhắn nào tới khách hàng thật.

**Tiêu chí chấp nhận:**

- Đổi một quy tắc định tuyến thì xem trước được hội thoại sẽ đi đâu trước khi lưu.
- Hội thoại trên kênh thử nghiệm không xuất hiện trong báo cáo vận hành.
- Không có tin nhắn nào tới khách hàng thật phát sinh từ thao tác thử nghiệm.

**Tham chiếu:** FEAT-34 → issue [#87](https://github.com/crmsaassaudi/product-management/issues/87).

---

### FEAT-35 — Nhật ký truy cập dữ liệu khách hàng `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Lưu vết **ai đã xem nội dung trao đổi của khách hàng nào**. Khác với FEAT-25 vốn chỉ ghi lại thay đổi cấu hình, mục này ghi lại việc đọc dữ liệu — thứ mà doanh nghiệp phải chứng minh được khi có khiếu nại rò rỉ thông tin, và là điều khoản bắt buộc với đơn vị dịch vụ thuê ngoài phục vụ nhiều khách hàng doanh nghiệp trên cùng hệ thống.

**Actor:** Quản trị viên (người tra cứu); Giám sát viên (điều tra sự cố).

**Quy tắc nghiệp vụ:**

- BR-35.1: Mọi lần xem nội dung hội thoại của một khách hàng PHẢI được ghi lại: ai xem, xem hội thoại của khách hàng nào, vào lúc nào, và qua đường nào (màn hình xử lý, tìm kiếm, báo cáo, xuất tệp, chấm chất lượng).
- BR-35.2: Nhật ký này PHẢI tra cứu được theo người xem, theo khách hàng, và theo khoảng thời gian.
- BR-35.3: Hệ thống PHẢI cảnh báo khi một người truy cập dữ liệu khách hàng với khối lượng bất thường so với công việc của họ, hoặc truy cập tập trung vào một khách hàng mà họ không phụ trách.
- BR-35.4: Nhật ký này KHÔNG ĐƯỢC sửa hay xóa bởi bất kỳ ai, kể cả Quản trị viên; thời hạn lưu do doanh nghiệp cấu hình và độc lập với thời hạn lưu trữ nội dung hội thoại (BR-23.1).
- BR-35.5: Chính khách hàng cuối, khi thực hiện quyền của mình theo BR-23.3, PHẢI được doanh nghiệp trả lời được câu hỏi ai trong doanh nghiệp đã tiếp cận dữ liệu của họ.

**Tiêu chí chấp nhận:**

- Khi có khiếu nại rò rỉ thông tin, doanh nghiệp tra ra được ai đã xem hội thoại của khách hàng đó và lúc nào.
- Một người xem hàng loạt hội thoại ngoài phạm vi công việc của mình luôn sinh cảnh báo.
- Không ai xóa được vết truy cập của chính mình.

**Tham chiếu:** FEAT-35 → issue [#88](https://github.com/crmsaassaudi/product-management/issues/88).

---

### FEAT-36 — Trao đổi nhiều bên trên kênh Email `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Email không vận hành như một khung chat hai người: nó có tiêu đề, có nhiều người cùng nhận, và thường xuyên phải kéo thêm bên thứ ba ở ngoài hệ thống vào cuộc. Mô hình hội thoại một-khách-một-doanh-nghiệp hiện tại không diễn đạt được điều đó, trong khi email là kênh chính của nhóm khách hàng doanh nghiệp — nhóm trả tiền nhiều nhất.

**Actor:** Khách hàng; Agent; Bên thứ ba bên ngoài (đối tác, nhà cung cấp, bộ phận nội bộ chưa dùng hệ thống).

**Quy tắc nghiệp vụ:**

- BR-36.1: Hội thoại trên kênh Email PHẢI giữ và hiển thị **tiêu đề thư**; đổi tiêu đề giữa chừng KHÔNG ĐƯỢC tự tách thành hội thoại mới nếu đó vẫn là cùng một mạch trao đổi.
- BR-36.2: Agent PHẢI thêm được người nhận đồng thời và người nhận ẩn vào một thư trả lời, và PHẢI nhìn rõ ai đang có mặt trong mạch trao đổi trước khi gửi.
- BR-36.3: Agent PHẢI **chuyển tiếp một hội thoại ra bên thứ ba bên ngoài hệ thống** và nhận lại phản hồi của họ vào đúng hội thoại đó, để mạch trao đổi không bị đứt sang hộp thư cá nhân.
- BR-36.4: Khi một người ngoài trả lời vào mạch trao đổi, hệ thống PHẢI phân biệt rõ đó là bên thứ ba chứ không phải khách hàng, và nội dung đó KHÔNG ĐƯỢC làm khởi động lại đồng hồ cam kết phản hồi với khách hàng.
- BR-36.5: Nội dung trích dẫn của các thư trước và chữ ký PHẢI được thu gọn để Agent thấy ngay phần nội dung mới, không phải cuộn qua toàn bộ mạch thư.
- BR-36.6: Doanh nghiệp PHẢI cấu hình được **chữ ký** áp dụng cho thư gửi đi, theo kênh hoặc theo Hộp thư.
- BR-36.7: Agent PHẢI **tách** một thư ra thành hội thoại riêng khi nó thực chất là một vụ việc khác, và **gộp** hai hội thoại email khi chúng là cùng một vụ việc; mọi thao tác tách/gộp PHẢI để lại vết theo BR-15.4.

**Tiêu chí chấp nhận:**

- Một mạch thư có ba bên tham gia vẫn nằm trong một hội thoại duy nhất, phân biệt rõ ai là khách hàng.
- Thư của bên thứ ba không làm sai lệch chỉ số tốc độ phản hồi với khách hàng.
- Agent kéo được bộ phận kỹ thuật vào mạch thư mà không phải chuyển sang hộp thư cá nhân.

**Tham chiếu:** FEAT-36 → issue [#89](https://github.com/crmsaassaudi/product-management/issues/89).

---


## 4. Yêu cầu phi chức năng

### 4.1 Bảo mật & Phân quyền

- **NFR-1 (Xác thực nguồn tin nhắn):** Mọi tin nhắn tự xưng đến từ một kênh PHẢI được xác thực thực sự đến từ đúng nền tảng đó trước khi được xử lý và hiển thị cho Agent, tránh tin nhắn giả mạo.
- **NFR-2 (Cách ly dữ liệu theo doanh nghiệp):** Hội thoại, khách hàng, cấu hình của một doanh nghiệp không bao giờ hiển thị hoặc trộn lẫn với dữ liệu của doanh nghiệp khác.
- **NFR-3 (Phân quyền truy cập media/tệp):** Hình ảnh/tệp đính kèm trong hội thoại chỉ truy cập được bởi người có quyền xem đúng hội thoại/doanh nghiệp đó.
- **NFR-4 (Bảo mật thông tin kết nối kênh):** Thông tin doanh nghiệp dùng để kết nối một kênh không bao giờ hiển thị lại cho bất kỳ ai sau khi đã lưu, kể cả cho chính người đã nhập, và không lộ ra qua bất kỳ màn hình, báo cáo, tệp xuất, hay thông báo lỗi nào.

### 4.2 Toàn vẹn & Nhất quán dữ liệu

- **NFR-5 (Không ghi nhận trùng):** Một tin nhắn hoặc một hành động của khách hàng không bao giờ được ghi nhận hai lần — không trùng trong khung chat, không trùng trong lịch sử hội thoại, không trùng trong báo cáo.
- **NFR-6 (Không mất tin nhắn khách hàng):** Mọi tin nhắn khách hàng đã gửi thành công trên kênh PHẢI đến được Agent, kể cả khi hệ thống gặp gián đoạn tạm thời — có thể chậm hơn bình thường, nhưng không được mất. Nếu việc tiếp nhận bị chậm bất thường, hệ thống PHẢI cảnh báo thay vì im lặng, để doanh nghiệp biết mình đang trả lời khách trễ vì lý do ngoài tầm kiểm soát của Agent.
- **NFR-7 (Không gửi trùng khi không chắc chắn):** Khi không xác định được một tin nhắn gửi đi đã tới khách hàng hay chưa, hệ thống KHÔNG ĐƯỢC tự động gửi lại — khách hàng nhận trùng một tin nhắn là trải nghiệm tệ hơn việc Agent chủ động gửi lại (xem BR-12.4).
- **NFR-8 (Quyền xem là duy nhất, ở mọi nơi):** Dữ liệu một người không được phép xem tại màn hình xử lý hội thoại thì cũng không được lộ ra ở bất kỳ nơi nào khác: báo cáo, bấm xuyên xuống từ báo cáo, tìm kiếm, thông báo, gợi ý khớp khách hàng, bản gửi báo cáo định kỳ, hay nội dung xuất ra tệp.

### 4.3 Hiệu năng & Vận hành

- **NFR-9 (Số liệu phải đúng, hoặc phải nhìn thấy được là đang thiếu):** Việc ghi nhận lịch sử và số liệu không được làm chậm việc phục vụ khách hàng; đổi lại, số liệu bị thiếu PHẢI nhận biết được ngay trên chính báo cáo, không được hiển thị như số liệu đầy đủ (xem BR-15.5). Một con số sai mà người đọc tin là đúng gây thiệt hại lớn hơn một con số thiếu mà người đọc biết là thiếu.
- **NFR-10 (Một doanh nghiệp không ảnh hưởng doanh nghiệp khác):** Chất lượng phục vụ khách hàng của một doanh nghiệp không được suy giảm vì khối lượng hội thoại tăng đột biến của một doanh nghiệp khác dùng chung hệ thống.
- **NFR-11 (Kết quả rõ ràng khi nhiều người cùng thao tác):** Khi nhiều người cùng thao tác trên một hội thoại tại cùng thời điểm, kết quả cuối cùng PHẢI rõ ràng và giống nhau với mọi người đang nhìn vào (các quy tắc cụ thể ở FEAT-13).
- **NFR-12 (Quy mô một doanh nghiệp không làm đổi cách sản phẩm vận hành):** Một doanh nghiệp có hàng trăm Agent, hàng chục Hộp thư và hàng chục nghìn hội thoại mỗi ngày PHẢI dùng đúng các quy tắc nghiệp vụ trong tài liệu này, không cần một mô hình vận hành riêng. Ở quy mô đó, mọi màn hình danh sách, tìm kiếm và báo cáo PHẢI vẫn dùng được để ra quyết định trong ca — nếu một thao tác không thể trả kết quả kịp, hệ thống PHẢI nói rõ điều đó và cho cách khác, không để người dùng chờ trong vô định.

### 4.4 Ngôn ngữ & Trải nghiệm

- **NFR-13 (Đa ngôn ngữ giao diện):** Giao diện Agent và widget Live Chat PHẢI hiển thị đúng theo ngôn ngữ người dùng đang chọn/trình duyệt của khách truy cập.
- **NFR-14 (Ngôn ngữ của cuộc trò chuyện):** Ngôn ngữ khách hàng đang dùng PHẢI nhận biết được và PHẢI dùng được làm điều kiện định tuyến (BR-04.3, FEAT-30); một khách hàng KHÔNG ĐƯỢC bị gán cho Agent không xử lý được ngôn ngữ của họ chỉ vì Agent đó đang rảnh.

### 4.5 Dữ liệu khách hàng

- **NFR-15 (Không giữ dữ liệu quá cam kết):** Hệ thống KHÔNG ĐƯỢC giữ nội dung hội thoại và tệp đính kèm quá thời hạn lưu trữ doanh nghiệp đã cấu hình (xem FEAT-23), trừ phần dữ liệu đang bị Tạm dừng xóa theo yêu cầu pháp lý (BR-23.7) — trường hợp này PHẢI có căn cứ ghi rõ, không phải một ngoại lệ mặc định.
- **NFR-16 (Xóa là xóa thật):** Khi một yêu cầu xóa dữ liệu khách hàng được thực hiện, dữ liệu đó PHẢI không còn truy cập được ở bất kỳ đâu trong sản phẩm — khung chat, tìm kiếm, gợi ý khớp khách hàng, bấm xuyên xuống từ báo cáo, tệp xuất ra — và doanh nghiệp PHẢI có bằng chứng việc xóa đã hoàn tất.
- **NFR-17 (Truy cập dữ liệu khách hàng luôn có vết):** Mọi lần một người xem nội dung hội thoại của một khách hàng PHẢI để lại vết tra cứu được (xem FEAT-35), kể cả khi việc xem đó nằm trong phạm vi quyền được cấp.

---

## 5. Ma trận quyền truy cập tính năng

Ký hiệu: ✅ được phép · ⚪ chỉ được phép khi quản trị viên cấp thêm quyền · ❌ không được phép · – không áp dụng. Trưởng nhóm áp dụng trong phạm vi đội mình phụ trách; Giám sát viên áp dụng trên nhiều đội.

| Tính năng | Agent | Trưởng nhóm | Giám sát viên | Chuyên viên chất lượng | Quản trị viên | Bot |
| --- | :---: | :---: | :---: | :---: | :---: | :---: |
| Kết nối/cấu hình kênh | ❌ | ❌ | ❌ | ❌ | ✅ | – |
| Xử lý hội thoại được gán (trả lời, ghi chú, đóng) | ✅ | ✅ | ✅ | ❌ | ⚪ | ✅ (tới lúc bàn giao) |
| Nhận hội thoại từ hàng đợi | ✅ | ✅ | ✅ | ❌ | ⚪ | – |
| Xem thông tin tóm tắt hội thoại đang chờ để chọn việc (BR-05.7) | ✅ | ✅ | ✅ | ❌ | ⚪ | – |
| Xem toàn bộ nội dung hội thoại chưa ai nhận | ❌ | ✅ | ✅ | ⚪ | ⚪ | – |
| Chuyển tiếp hội thoại | ✅ (của mình) | ✅ (mọi hội thoại) | ✅ (mọi hội thoại) | ❌ | ⚪ (của mình) | ✅ (bàn giao theo BR-11.3) |
| Bảng điều hành thời gian thực (FEAT-26) | ❌ | ✅ (đội mình) | ✅ | ❌ | ⚪ | – |
| Nhắc bài, tiếp quản, gán lại hàng loạt (BR-26.4→26.6) | ❌ | ✅ | ✅ | ❌ | ❌ | – |
| Chấm điểm chất lượng, hiệu chuẩn (FEAT-27) | ❌ | ⚪ | ⚪ | ✅ | ⚪ | – |
| Xem kết quả chấm của chính mình, gửi phúc khảo | ✅ | ✅ | ✅ | – | – | – |
| Xếp ca trực, xem tuân thủ ca (FEAT-28) | ❌ (chỉ xem ca của mình) | ⚪ | ✅ | ❌ | ⚪ | – |
| Gộp/gỡ gộp hồ sơ khách hàng, đánh dấu Định danh dùng chung | ✅ | ✅ | ✅ | ❌ | ✅ | ❌ |
| Đánh dấu spam | ✅ | ✅ | ✅ | ❌ | ✅ | ❌ |
| Chặn/gỡ chặn người gửi (BR-31.2) | ❌ | ✅ | ✅ | ❌ | ✅ | – |
| Quản lý danh mục lý do xử lý & nhãn (FEAT-29) | ❌ | ⚪ | ⚪ | ❌ | ✅ | – |
| Khai báo kỹ năng, gán kỹ năng cho Agent (FEAT-30) | ❌ | ⚪ (gán trong đội) | ✅ | ❌ | ✅ | – |
| Cấu hình phân công/SLA/leo thang/tự động đóng/giờ làm việc/tự động hóa | ❌ | ❌ | ❌ | ❌ | ✅ | – |
| Thử nghiệm cấu hình (FEAT-34) | ❌ | ❌ | ⚪ | ❌ | ✅ | – |
| Xem báo cáo vận hành & hiệu suất | ⚪ (dữ liệu của mình) | ✅ (đội mình) | ✅ | ⚪ (chỉ chỉ số chất lượng) | ✅ | – |
| Đặt lịch gửi báo cáo định kỳ (BR-19.6) | ❌ | ⚪ | ✅ | ❌ | ✅ | – |
| Quản lý Hộp thư, mẫu tin nhắn dùng chung | ❌ | ⚪ | ⚪ | ❌ | ✅ | – |
| Xuất nội dung hội thoại ra tệp (BR-23.4) | ❌ | ⚪ | ✅ | ⚪ | ✅ | – |
| Cấu hình lưu trữ, đặt Tạm dừng xóa, thực hiện yêu cầu xóa dữ liệu | ❌ | ❌ | ❌ | ❌ | ✅ | – |
| Tra cứu Nhật ký thay đổi cấu hình (FEAT-25) | ❌ | ❌ | ⚪ | ❌ | ✅ | – |
| Tra cứu Nhật ký truy cập dữ liệu khách hàng (FEAT-35) | ❌ | ❌ | ⚪ | ❌ | ✅ | – |
| Đặt trạng thái làm việc của bản thân | ✅ | ✅ | ✅ | ✅ | ✅ | – |

Ba nguyên tắc đọc ma trận này:

1. **Phân tách trách nhiệm là mặc định.** Quản trị viên được đánh ⚪ ở các dòng xử lý hội thoại vì trong doanh nghiệp nhỏ, người cấu hình hệ thống cũng chính là người trực máy — nhưng đó là quyền cấp thêm có chủ đích, không mặc định.
2. **Người chấm chất lượng không phục vụ khách hàng.** Chuyên viên chất lượng đọc được nội dung để chấm nhưng không trả lời, không đổi trạng thái, không cấu hình — để kết quả chấm giữ được tính độc lập.
3. **Không ai xóa được vết của chính mình.** Không vai trò nào, kể cả Quản trị viên, được sửa hay xóa nội dung của FEAT-25 và FEAT-35.

---

## 6. Kịch bản chấp nhận tổng hợp

1. **Khách hàng cũ nhắn lại đúng người quen:** Một khách hàng đã từng được Agent A hỗ trợ qua Facebook Messenger nhắn tin lại trong vòng thời hạn ưu tiên. Agent A đang trực tuyến và còn khả năng nhận thêm việc → hội thoại tự động gán thẳng cho Agent A, không qua hàng đợi.
2. **Nhận diện sai kênh không tự động gộp nhầm:** Một khách hàng nhắn qua Instagram với số điện thoại trùng với một khách hàng VIP có sẵn trong CRM (thực chất là một người khác, trùng ngẫu nhiên số hotline). Hệ thống chỉ hiển thị gợi ý "có vẻ trùng khớp" cho Agent, không tự gộp — Agent kiểm tra thấy không đúng nên bỏ qua gợi ý, giữ nguyên là hai hồ sơ riêng biệt.
3. **Bot bàn giao đúng lúc, đúng người:** Khách hàng nhắn "tôi muốn gặp nhân viên" trong lúc đang trò chuyện với Bot. Bot bàn giao hội thoại; nếu người/nhóm được Bot chỉ định không còn hợp lệ (đã nghỉ việc/đổi ca), hệ thống tự chuyển hội thoại vào hàng đợi chung thay vì bàn giao vào chỗ trống.
4. **SLA tạm dừng đúng lúc khách hàng im lặng:** Agent trả lời xong, khách hàng không phản hồi trong 2 giờ, Agent chủ động Tạm hoãn hội thoại để làm việc khác. Đồng hồ cam kết phản hồi lần sau dừng lại trong lúc tạm hoãn; khi khách hàng nhắn lại, hội thoại tự động về Đang mở và đồng hồ tiếp tục.
5. **Tự động đóng có cảnh báo trước, không đóng đột ngột:** Hội thoại không hoạt động 24 giờ theo chính sách; hệ thống gửi tin nhắn hỏi khách còn cần hỗ trợ không, chờ thêm 30 phút. Khách phản hồi trong lúc chờ → hội thoại tiếp tục bình thường, không bị đóng.
6. **Hệ thống bảo vệ khi hàng loạt hội thoại cùng lúc đủ điều kiện đóng:** Một trục trặc ở phía kênh khiến rất nhiều hội thoại cùng lúc đạt ngưỡng tự động đóng. Ngưỡng an toàn đóng hàng loạt kích hoạt, hoãn bớt số lượng đóng cùng lúc và gửi cảnh báo cho Giám sát viên kèm số hội thoại bị ảnh hưởng; các hội thoại bị hoãn được đóng lại trong thời hạn doanh nghiệp cấu hình, không bị bỏ quên. Giám sát viên mở một hội thoại bất kỳ trong số đó là thấy ngay lý do nó chưa đóng.
7. **CSAT và khối lượng công việc nhìn được cùng một chỗ:** Giám sát viên mở Báo cáo vận hành Omnichannel để xem khối lượng hội thoại tuần này tăng đột biến trên kênh WhatsApp; ngay trong cùng báo cáo, thấy điểm CSAT trung bình của kênh đó cũng giảm — kết luận được ngay đội ngũ đang quá tải ảnh hưởng tới chất lượng, không cần đối chiếu chéo 2 báo cáo riêng.
8. **Hoạch định nhân sự dựa trên AHT đúng bản chất kênh:** Giám sát viên xem Báo cáo hiệu suất Agent để tính số Agent cần thêm cho đội Live Chat riêng biệt với đội Nhắn tin mạng xã hội, vì hai nhóm này có AHT rất khác nhau (vài phút so với vài giờ) — nếu bị gộp chung, con số trung bình sẽ không dùng được cho việc này.
9. **Số điện thoại dùng chung không kéo theo gộp nhầm:** Một đại lý dùng chung một số WhatsApp cho nhiều nhân viên nhắn tin cho doanh nghiệp. Sau lần đầu phát hiện, Agent đánh dấu số đó là Định danh dùng chung; từ đó mọi tin nhắn tới từ số này chỉ hiển thị gợi ý khớp, không tự gộp — kể cả trên WhatsApp.
10. **Hội thoại ưu tiên không bị âm thầm tụt xuống cam kết mặc định:** Một hội thoại thuộc Hộp thư khách hàng ưu tiên phát sinh đúng lúc hệ thống không xác định được cấu hình riêng của Hộp thư đó. Hội thoại vẫn được tiếp nhận ngay (khách không bị chặn), nhưng được đánh dấu là đang chạy theo cam kết mặc định và Giám sát viên nhận cảnh báo; khi cấu hình riêng xác định lại được, cam kết đúng được áp lại cho hội thoại đó.
11. **Khách hàng yêu cầu xóa dữ liệu:** Một khách hàng từng nhắn tin qua Facebook và WhatsApp yêu cầu doanh nghiệp xóa toàn bộ dữ liệu cá nhân của họ. Quản trị viên thực hiện yêu cầu một lần cho cả hai kênh, nhận được xác nhận việc xóa đã hoàn tất để trả lời khách hàng — trong khi số liệu tổng hợp về khối lượng hội thoại của kỳ đó vẫn còn nguyên trong báo cáo.
12. **Trưởng nhóm phát hiện ùn tắc trước khi khách hàng phải chờ quá hạn:** Đầu giờ chiều, số khách chờ trên hàng đợi Live Chat tăng nhanh vì hai Agent nghỉ đột xuất. Bảng điều hành thời gian thực chạm ngưỡng cảnh báo, Trưởng nhóm nhận thông báo, gán lại việc của hai người vắng mặt sang đội còn rảnh trong một thao tác — trước khi hội thoại đầu tiên chạm hạn cam kết.
13. **Đánh giá Agent không chỉ bằng tốc độ:** Một Agent đứng đầu về số hội thoại xử lý trong tháng. Kết quả chấm chất lượng cho thấy Agent này thường đóng hội thoại khi khách chưa được giải đáp hết, và điểm CSAT của Agent thấp hơn trung bình đội. Bảng xếp hạng hiệu suất phản ánh cả ba mặt nên Agent này không đứng đầu, và Trưởng nhóm có căn cứ cụ thể để kèm cặp.
14. **Kết ca không bỏ lại việc:** Một Agent hết ca lúc 18h khi còn 6 hội thoại đang mở. Hệ thống liệt kê đủ 6 hội thoại, đề xuất người ca sau phù hợp, yêu cầu ghi chú bàn giao từng cái; Agent chỉ kết thúc ca được sau khi cả 6 đều có người nhận. Sáng hôm sau khách hàng nhắn tiếp và gặp đúng người đã nhận bàn giao, không phải kể lại từ đầu.
15. **Một đợt spam không làm hỏng số liệu tháng:** Một chiến dịch tin nhắn rác gửi hàng trăm tin vào kênh Facebook trong một đêm. Agent trực đánh dấu spam và Trưởng nhóm chặn nguồn gửi; các hội thoại đó dừng đồng hồ cam kết, không kích hoạt leo thang, không vào chỉ số tốc độ — báo cáo tháng vẫn phản ánh đúng năng lực phục vụ thật, và doanh nghiệp vẫn thấy được quy mô spam đã nhận.
16. **Doanh nghiệp tự đổi quy trình mà không cần nhà cung cấp:** Doanh nghiệp muốn mọi hội thoại gắn nhãn "khiếu nại" của khách hàng phân khúc VIP phải được ưu tiên cao nhất và báo ngay cho Trưởng nhóm. Quản trị viên tự đặt quy tắc tự động hóa, thử nghiệm trên dữ liệu giả định và đối chiếu với hội thoại tháng trước để thấy tác động, rồi mới bật cho khách hàng thật.
17. **Khách hàng ngoài giờ không rơi vào khoảng trống:** Khách hàng nhắn lúc 22h. Widget báo rõ hiện không có ai trực và mời để lại yêu cầu liên hệ lại kèm khung giờ thuận tiện. Sáng hôm sau yêu cầu này xuất hiện như một việc có người phụ trách và có thời hạn; Agent liên hệ lại và thấy nguyên nội dung khách đã nhắn tối qua.

---

## 7. Giới hạn hiện tại & vấn đề tồn đọng

Mục này nêu hai loại nội dung: **ranh giới hiện tại của sản phẩm** (để tránh kỳ vọng sai khi tư vấn khách hàng) và **các câu hỏi nghiệp vụ chưa có quyết định** (cần chốt trước khi xây dựng). Những điểm đã chốt phương án nhưng chưa xây dựng nằm ở nhãn `[Yêu cầu mới]` tại Mục 3, không lặp lại ở đây.

### 7.1 Ranh giới hiện tại của sản phẩm

1. **Chưa có tính năng gửi tin nhắn hàng loạt/chiến dịch (Campaign, Broadcast)** — Omnichat hiện chỉ phục vụ hội thoại hai chiều do khách hàng chủ động bắt đầu, do Agent/Bot trả lời, hoặc do doanh nghiệp chủ động liên hệ lại theo FEAT-32.
2. **Chưa phân tích được cảm xúc và ý định của khách hàng từ nội dung trao đổi** — hệ thống chỉ ghi nhận biểu tượng phản hồi nhanh do người thả (FEAT-15), không tự đánh giá khách đang hài lòng hay bức xúc. Hệ quả: không tự phát hiện được hội thoại đang xấu đi để can thiệp sớm, và việc lấy mẫu chấm chất lượng (BR-27.2) phải dựa vào các dấu hiệu gián tiếp.
3. **Chưa gợi ý được nội dung trả lời cho Agent** — mẫu tin nhắn nhanh (FEAT-18) là công cụ tra cứu thủ công; hệ thống không đề xuất câu trả lời phù hợp theo ngữ cảnh, không tự tóm tắt hội thoại khi bàn giao hoặc khi đóng, và không tự dịch giữa ngôn ngữ khách hàng và ngôn ngữ Agent.
4. **Một số hình thức tương tác đặc thù theo kênh chưa được xử lý như một hội thoại đầy đủ** — ví dụ trả lời story trên Instagram, bình luận hoặc tin nhắn phát sinh từ quảng cáo trên TikTok.
5. **Chưa có nơi lưu trữ dữ liệu theo vùng địa lý** — doanh nghiệp có ràng buộc dữ liệu phải nằm trong một quốc gia/khu vực cụ thể chưa được đáp ứng. Đây là điều kiện tiên quyết với một số ngành và một số thị trường.
6. **Phân công cho các đối tượng nghiệp vụ khác của CRM chưa tôn trọng giới hạn tải của nhân viên** — một nhân viên đã đạt giới hạn công việc vẫn có thể được chọn nếu đang là người ít việc nhất trong nhóm. Điều này **không** ảnh hưởng tới phân công hội thoại của Omnichat (BR-04.1 và BR-04.7 áp dụng đầy đủ cho hội thoại), và thuộc phạm vi SRS/backlog của các đối tượng đó, không thuộc tài liệu này.

   **Tham chiếu:** → issue [#14](https://github.com/crmsaassaudi/product-management/issues/14).

### 7.2 Câu hỏi nghiệp vụ chưa có quyết định

Sáu câu hỏi đầu là các quyết định về mô hình và cam kết, cần chốt trước khi thiết kế; bốn câu sau là các quyết định về chính sách, chốt được song song với việc xây dựng.

| # | Câu hỏi cần quyết định | Vì sao chưa quyết được là rủi ro | Liên quan |
| --- | --- | --- | --- |
| 1 | **[Đã chốt — xem ADR-0005] Đơn vị công việc của Omnichat là một phiên trên một kênh, hay một vụ việc của một con người?** Tách bạch 2 tầng: Phiên trên kênh (Channel Session - đơn vị kỹ thuật/cửa sổ phản hồi) và Vụ việc của khách hàng (Customer Case - đơn vị nghiệp vụ/SLA/CSAT/phân công). | Đã giải quyết theo [ADR-0005](../docs/adr/0005-omnichat-work-unit-session-vs-case.md): Tách Channel Session và Customer Case, liên kết các phiên đa kênh của cùng khách hàng vào 1 vụ việc duy nhất. | BR-02.9, FEAT-08, FEAT-19, [ADR-0005](../docs/adr/0005-omnichat-work-unit-session-vs-case.md) |
| 2 | **Cam kết thời gian phản hồi có được dùng làm cam kết hợp đồng với khách hàng cuối không?** Hiện tài liệu định nghĩa SLA là cam kết nội bộ để doanh nghiệp tự đo. | Đơn vị dịch vụ thuê ngoài ký cam kết có ràng buộc tài chính với khách hàng doanh nghiệp của họ và phải báo cáo đối chiếu. Giữ nguyên định nghĩa nội bộ thì không phục vụ được nhóm khách hàng này; mở ra thì kéo theo yêu cầu về bằng chứng, về loại trừ và về phê duyệt điều chỉnh số liệu. | FEAT-08, FEAT-19 |
| 3 | **Thời hạn lưu trữ mặc định** cho nội dung hội thoại và tệp đính kèm là bao lâu? | Đây là một cam kết thương mại và pháp lý với khách hàng doanh nghiệp, không phải một tham số kỹ thuật. Chưa có con số thì không bán được cho khách có yêu cầu tuân thủ, và cũng không có mốc để xóa dữ liệu. | FEAT-23 |
| 4 | **Ngưỡng thời gian chờ tối đa** của một khách hàng trước khi hội thoại phải có người nhận là bao nhiêu? | Đây mới là cam kết thật với khách hàng cuối; hiện mỗi doanh nghiệp tự đặt và sản phẩm không có mức khuyến nghị, nên không cảnh báo được khi một doanh nghiệp cấu hình bất hợp lý. | BR-05.3, BR-05.6, BR-05.9 |
| 5 | **Phân quyền trường áp tới đâu với dữ liệu khách hàng hiển thị trong Omnichat?** | Số điện thoại/email của khách hiển thị ngay trong khung xử lý hội thoại. Quy tắc chung của hệ thống đã phân biệt *được xem tới mức nào* và *được nhìn thấy giá trị tới mức nào* (che một phần/che hoàn toàn), nhưng chưa quyết định Omnichat áp tới đâu — hiện chỉ có hai trạng thái thấy/không thấy. Quyết định này cũng chi phối cách thực hiện BR-23.8. | NFR-8, BR-23.8, [ADR-0001](../docs/adr/0001-group-policy-conflict-resolution.md) |
| 6 | **Khi hết Cửa sổ phản hồi trên kênh không có tin nhắn mẫu, doanh nghiệp được phép chủ động liên hệ khách bằng kênh khác tới mức nào?** | BR-12.9 mới dừng ở mức gợi ý cho Agent các cách liên hệ khác. Việc dùng email/số điện thoại khách để lại ở kênh A để liên hệ qua kênh B là một quyết định về sự đồng ý của khách hàng, cần chốt ở cấp sản phẩm/pháp lý. | BR-12.9, FEAT-32 |
| 7 | **Khách hàng có được sửa điểm CSAT đã chấm hay không?** | Hiện mỗi lời mời chỉ chấm được một lần và không sửa được. Chưa rà lại xem đây là lựa chọn đúng về nghiệp vụ hay chỉ là hệ quả của cách làm ban đầu — nó ảnh hưởng trực tiếp tới độ tin cậy của chỉ số CSAT dùng để đánh giá Agent. | BR-14.2, BR-20.3 |
| 8 | **"Chờ đóng" có nên là một trạng thái vòng đời hiển thị ra báo cáo không?** | Với Agent và Giám sát viên, hội thoại ở trạng thái này vẫn đang mở. Tách nó thành một trạng thái riêng làm thay đổi cách đọc bảng phân bố hội thoại theo trạng thái, và có thể khiến số hội thoại "đang mở" nhìn thấp hơn thực tế. Quyết định này gắn với việc doanh nghiệp được tự định nghĩa trạng thái chờ theo BR-03.8. | FEAT-03, BR-03.8, FEAT-19 |
| 9 | **Điểm chất lượng và điểm CSAT có được dùng làm căn cứ thưởng/phạt hay chỉ để kèm cặp?** | Cùng một bộ số liệu nhưng hai cách dùng dẫn tới hai thiết kế khác nhau về tỷ lệ lấy mẫu, quyền phúc khảo và mức độ minh bạch. Quyết định muộn sẽ khiến đội ngũ mất niềm tin vào chỉ số ngay từ kỳ đánh giá đầu tiên. | FEAT-27, BR-20.5 |
| 10 | **Khi không có Agent nào đủ kỹ năng, mặc định của sản phẩm là chờ hay là hạ chuẩn?** | BR-30.4 buộc doanh nghiệp phải chọn, nhưng sản phẩm chưa có khuyến nghị mặc định. Chọn sai một lần là hoặc khách chờ quá lâu, hoặc khách gặp người không xử lý được việc của họ — cả hai đều là thất bại nhìn từ phía khách hàng. | BR-30.4 |

**Tham chiếu:** câu 1 → [ADR-0005](../docs/adr/0005-omnichat-work-unit-session-vs-case.md) (issue [#90](https://github.com/crmsaassaudi/product-management/issues/90)).
