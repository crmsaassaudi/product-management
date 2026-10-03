# SRS — Hội thoại Đa kênh & Hộp thư Tiếp nhận (Omnichat)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA |
| **Module** | Omnichat — tiếp nhận, phân công, xử lý và trả lời hội thoại khách hàng qua nhiều kênh (Facebook, Instagram, WhatsApp, Zalo, TikTok, Telegram, Email, Live Chat website) |
| **Ngày viết** | 2026-08-22 |
| **Ngày cập nhật** | 2026-10-02 |
| **Phiên bản** | v2.0 (Chuẩn hóa Nghiệp vụ Thuần túy — đồng bộ phân quyền với [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) v5.0: hội thoại là bản ghi công việc của đơn vị tiếp nhận, hàng đợi theo đơn vị, quyền viết theo ô × mức, khai báo trường nhạy cảm; thay Baseline 2026-08-24; bổ sung sau rà soát độc lập: đề nghị chuyển theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`, hộp thư email thuộc một phân hệ, tạo vé từ hội thoại, Bot dùng mô hình AI là Tác nhân AI; vòng 2: kết ca theo đề nghị chuyển, danh sách đích chuyển hàng đợi mặc định rỗng, chuyển phân hệ hộp thư email; vòng 3: Xin ý kiến là lượt cấp Chỉ đọc, trạng thái tạm dừng xóa trong biên bản) |
| **Neo khảo sát ban đầu** | `crm-api` @ `6cb0f24657fbaa856b149df59905925562eb5416` · `crm-web` @ `e65eb38aca4993ff00d2e94d56387746f0ba22f3` · `livechat-widget` @ `acc00d01e10bebd92414207496578ea3cd29c21f` (2026-08-22) — mốc khảo sát hệ thống khi viết bản đầu; từ v2.0 tài liệu đặc tả trạng thái nghiệp vụ mục tiêu, không mô tả hiện trạng triển khai |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary, mục "Omnichat"), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`contacts-srs.md`](./contacts-srs.md), [`campaigns-srs.md`](./campaigns-srs.md), [`tickets-srs.md`](./tickets-srs.md), [ADR-0005](../docs/adr/0005-omnichat-work-unit-session-vs-case.md), [ADR-0008](../docs/adr/0008-data-subject-deletion-contract-omnichat-tickets.md), [ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md) |

## Ghi chú về nguồn gốc tài liệu

Omnichat được xây dựng và đưa vào vận hành trước khi có SRS. Bản đầu được viết bằng cách khảo sát hành vi hệ thống đang vận hành rồi đối chiếu với kỳ vọng đúng của một hệ thống contact center qua các vòng review nghiệp vụ với BA/PM.

Từ v2.0, tài liệu đặc tả **một trạng thái nghiệp vụ mục tiêu duy nhất**: mọi quy tắc ở Mục 3 là yêu cầu bắt buộc như nhau; nơi nào hệ thống làm khác tài liệu thì hệ thống phải sửa theo tài liệu. Tài liệu không dùng nhãn trạng thái triển khai. Những nhu cầu thật nhưng chưa chốt được phương án gom tại Mục 7. Các ngưỡng thời gian, số lần, số lượng là **tham số cấu hình của doanh nghiệp** (Phụ lục B), không phải hằng số của sản phẩm. Phân quyền của phân hệ dẫn chiếu hợp đồng quyền chung tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) v5.0 và không định nghĩa lại các khái niệm của tài liệu đó. Các quyết định đã chốt ghi tại Phụ lục C.

---

## 1. Giới thiệu

### 1.1 Mục đích

Tài liệu đặc tả toàn bộ yêu cầu chức năng và phi chức năng của module **Omnichat** — trung tâm tiếp nhận và xử lý hội thoại của một tổng đài đa kênh (contact center), cho phép doanh nghiệp tiếp nhận, phân công, trả lời và theo dõi chất lượng phục vụ khách hàng nhắn tin qua nhiều kênh khác nhau trong cùng một nơi làm việc duy nhất.

### 1.2 Phạm vi

Tài liệu bao trùm toàn bộ hành trình một hội thoại và toàn bộ lớp vận hành đội ngũ xung quanh nó, gồm 36 tính năng (`FEAT-01` – `FEAT-36`) xếp theo ba mảng.

**Hành trình hội thoại:** kết nối kênh giao tiếp và khai báo đơn vị tiếp nhận, tiếp nhận và nhận diện khách hàng, tự động phân công cho Agent, hàng đợi theo đơn vị tiếp nhận và lời mời nhận việc, chuyển tiếp giữa các Agent và giữa các hàng đợi, cam kết thời gian phản hồi (SLA) và leo thang khi vi phạm, trả lời tự động bằng Bot và bàn giao cho người, gửi tin nhắn đi, tự động đóng hội thoại không hoạt động, khảo sát hài lòng khách hàng (CSAT), ghi chú/lịch sử xử lý, tìm kiếm tin nhắn, và trải nghiệm trò chuyện của khách hàng trên widget Live Chat nhúng website.

**Lớp vận hành đội ngũ:** giám sát và can thiệp thời gian thực, quản lý chất lượng hội thoại, ca trực và bàn giao ca, kỹ năng và định tuyến theo kỹ năng, danh mục lý do xử lý và nhãn, chặn spam và xử lý lạm dụng, quy tắc tự động hóa, báo cáo vận hành và hiệu suất.

**Quản trị doanh nghiệp:** cấu hình hộp thư, mẫu tin nhắn nhanh, giờ làm việc, chính sách lưu trữ/xuất/xóa dữ liệu, che dữ liệu nhạy cảm trong hội thoại, nhật ký thay đổi cấu hình và nhật ký truy cập dữ liệu khách hàng, khai báo quyền của phân hệ theo mô hình ô × mức.

**Ngoài phạm vi:**

- Thủ tục đăng ký, xác minh và phê duyệt tài khoản doanh nghiệp với từng nhà cung cấp kênh (Facebook, WhatsApp...) — việc doanh nghiệp làm trực tiếp với nhà cung cấp trước khi kết nối vào hệ thống.
- Chiến dịch gửi tin nhắn hàng loạt (Campaign/Broadcast) — thuộc [`campaigns-srs.md`](./campaigns-srs.md). Phân hệ này cung cấp tài khoản kênh dùng chung cho chiến dịch ([`campaigns-srs.md`](./campaigns-srs.md) `BR-10.1`), tiếp nhận tin khách trả lời chiến dịch ([`campaigns-srs.md`](./campaigns-srs.md) `BR-35.1`) và ưu tiên tin trả lời của Agent trước tin chiến dịch (`BR-12.6`).
- Hợp đồng quyền chung (vai trò, mức truy cập, đơn vị tổ chức, hàng đợi, che dữ liệu, tạm ngưng và rời workspace) — thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md); tài liệu này chỉ khai báo phần phân hệ phải khai báo theo hợp đồng đó (Mục 5).
- Hồ sơ khách hàng, khách hàng tiềm năng, phân bổ khách hàng tiềm năng, gộp hồ sơ và quyền chủ thể dữ liệu — thuộc [`contacts-srs.md`](./contacts-srs.md); tài liệu này chỉ đặc tả điểm nối với hội thoại.
- Các đối tượng nghiệp vụ khác của CRM (Cơ hội, Vé hỗ trợ, Công việc) — gồm cả quy tắc phân công lẫn chỉ số hiệu suất của chúng, kể cả việc tôn trọng giới hạn tải của nhân viên khi phân công các đối tượng đó. Khi Báo cáo hiệu suất Agent cần hiển thị cạnh nhau năng suất hội thoại và năng suất các đối tượng khác, phần số liệu của các đối tượng đó do SRS tương ứng đặc tả.
- Trung tâm cuộc gọi thoại (Voice/Call Center) — không phải một kênh của Omnichat.

**Tham chiếu:** giới hạn tải khi phân công các đối tượng khác của CRM → issue [#14](https://github.com/crmsaassaudi/product-management/issues/14).

### 1.3 Đối tượng đọc

- Business Analyst / Product Owner: hiểu đúng hành vi nghiệp vụ trước khi đề xuất thay đổi.
- QA: làm căn cứ viết test case chấp nhận.
- Trưởng nhóm/Giám sát viên contact center: hiểu năng lực vận hành của hệ thống (định tuyến, SLA, báo cáo) khi tư vấn quy trình cho khách hàng doanh nghiệp.
- Kỹ sư phát triển: hiểu doanh nghiệp và khách hàng cần gì trước khi xây dựng — tài liệu nói *phải làm gì*, phương án xây dựng do đội phát triển quyết định.

### 1.4 Thuật ngữ & viết tắt

Các khái niệm phân quyền — **Mức truy cập** (Không có / Chỉ của mình / Đơn vị của mình / Đơn vị và các đơn vị con / Toàn workspace), **Bản ghi của mình**, **Bản ghi thuộc một đơn vị**, **Bản ghi công việc**, **Bản ghi chờ phân công**, **Sàn bắt buộc**, **Người phụ trách đơn vị**, **Đơn vị chính / Đơn vị kiêm nhiệm**, **Thu hẹp / Nới rộng quyền**, **Người có toàn quyền**, **Tác nhân AI** — dùng đúng định nghĩa tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4 và [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-34`, không định nghĩa lại ở đây.

| Thuật ngữ | Giải thích |
| --- | --- |
| **Hội thoại (Conversation)** | Một phiên trao đổi tin nhắn giữa một khách hàng và doanh nghiệp, gắn với đúng một kênh. Có vòng đời riêng: Đang mở, Tạm hoãn, Chờ đóng, Đã giải quyết, Đã đóng. Về phân quyền, hội thoại là một **bản ghi công việc** của đơn vị tiếp nhận (`BR-05.12`). |
| **Kênh (Channel)** | Một kết nối cụ thể tới một nền tảng giao tiếp (một Trang Facebook, một số WhatsApp, một Zalo OA, một hộp thư email, một widget Live Chat...), thuộc về đúng một doanh nghiệp (workspace). Mỗi kênh là một **nguồn** theo nghĩa của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10`. |
| **Đơn vị tiếp nhận hội thoại của kênh** | Đơn vị tổ chức mà mọi hội thoại phát sinh từ kênh thuộc về (`BR-01.9`, `BR-05.11`). |
| **Đơn vị tiếp nhận khách hàng của kênh** | Đơn vị tổ chức mà Hồ sơ khách hàng tạm và khách hàng tiềm năng sinh ra từ kênh thuộc về cho tới khi có Người phụ trách (`BR-01.9`, `BR-02.10`). |
| **Hàng đợi hội thoại** | Nơi các hội thoại chưa có Người phụ trách chờ được nhận. Mỗi Hộp thư là một hàng đợi; kênh không thuộc Hộp thư nào là một hàng đợi riêng. Mỗi hàng đợi thuộc đúng một đơn vị tiếp nhận (`BR-05.11`, `BR-17.4`). |
| **Hộp thư/Nhóm hội thoại (Inbox)** | Một hàng đợi gộp nhiều kênh cùng đơn vị tiếp nhận, có tập Agent đủ điều kiện được phân công và có thể có chính sách định tuyến/SLA/Bot riêng khác với mặc định của doanh nghiệp. Gán Agent vào Hộp thư quyết định ai được **phân công**, không quyết định ai được **xem** (`BR-17.1`). |
| **Nhóm xử lý** | Tập Agent được chọn làm đích định tuyến hoặc đích chuyển tiếp (một Hộp thư, một đơn vị, hoặc một Nhóm theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4). Chọn một nhóm xử lý không cấp quyền gì cho các thành viên của nó. |
| **Hồ sơ khách hàng tạm** | Xem [`CONTEXT.md`](../CONTEXT.md) và [`contacts-srs.md`](./contacts-srs.md) `BR-01.1b`. |
| **Lời mời nhận hội thoại** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Ưu tiên người phụ trách trước đó** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Cửa sổ phản hồi** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Ngưỡng an toàn đóng hàng loạt** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Nhận việc (tự nhận)** | Một thành viên tự gán mình làm Người phụ trách của một hội thoại đang chờ trong hàng đợi — là thao tác **Gán** theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.11` (`BR-05.5`). |
| **Trả về hàng đợi** | Bỏ Người phụ trách của một hội thoại chưa kết thúc để nó quay lại hàng đợi của đơn vị tiếp nhận mà nó đang thuộc (`BR-05.14`). |
| **Cam kết thời gian phản hồi (SLA)** | Thời hạn nội bộ mà doanh nghiệp tự đặt ra cho việc phản hồi/giải quyết một hội thoại, dùng để đo và cảnh báo khi trễ hẹn — không phải cam kết hợp đồng với khách hàng. |
| **Leo thang (Escalation)** | Hành động tự động (cảnh báo, thông báo cấp quản lý, chuyển việc) được kích hoạt khi một hội thoại vi phạm SLA. |
| **Tự động đóng hội thoại (Auto-close)** | Cơ chế tự động chuyển một hội thoại không có hoạt động trong một khoảng thời gian sang Đã giải quyết/Đã đóng, có cảnh báo trước cho khách hàng. |
| **Bot** | Kịch bản trả lời tự động (xây dựng ở một công cụ riêng ngoài Omnichat) tiếp nhận và trả lời hội thoại thay Agent cho tới khi bàn giao cho người hoặc kết thúc. Bot chạy theo kịch bản cố định, không đưa dữ liệu cho mô hình AI, thì không phải Tác nhân AI; bước nào của Bot đưa dữ liệu cho mô hình AI là Tác nhân AI ở bước đó theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4 và chỉ nhận giá trị đã che (`BR-11.7`). |
| **Bàn giao (Handoff)** | Thời điểm Bot chuyển quyền xử lý một hội thoại sang một Agent, một nhóm xử lý, hoặc hàng đợi. |
| **CSAT (Customer Satisfaction)** | Điểm hài lòng (1–5) khách hàng tự chấm sau khi hội thoại được giải quyết. |
| **Mẫu tin nhắn nhanh (Canned Response)** | Nội dung soạn sẵn Agent chèn nhanh vào hội thoại thay vì gõ lại từ đầu. |
| **Thời gian xử lý trung bình (AHT)** | Thời lượng trung bình một Agent dành để xử lý xong một hội thoại, tính từ lúc nhận tới lúc hoàn tất Xử lý sau hội thoại, dùng để hoạch định nhân sự. |
| **Thuộc tính kênh** | Tập đặc điểm nghiệp vụ mỗi kênh khai báo khi kết nối (đồng thời hay không đồng thời, có Cửa sổ phản hồi hay không, có tin nhắn mẫu phê duyệt trước hay không, định danh người gửi có được nền tảng xác minh hay không, khả năng tin nhắn dạng nút bấm). Quy tắc nghiệp vụ luôn viện dẫn thuộc tính, không viện dẫn tên kênh — xem `BR-01.8`. |
| **Xử lý sau hội thoại (Wrap-up)** | Khoảng thời gian Agent hoàn tất phần việc còn lại của một hội thoại vừa kết thúc trước khi sẵn sàng nhận việc mới — xem `BR-06.5`. |
| **Lý do xử lý (Disposition)** | Mã phân loại kết quả một hội thoại do doanh nghiệp tự định nghĩa, Agent chọn khi giải quyết — xem `FEAT-29`. |
| **Nhãn (Tag)** | Từ khóa gắn thêm lên hội thoại để lọc, định tuyến, tự động hóa và phân tích; gắn được nhiều và bất cứ lúc nào — xem `FEAT-29`. |
| **Kỹ năng (Skill)** | Năng lực cụ thể của một Agent (ngôn ngữ, dòng sản phẩm, cấp độ chuyên môn, thẩm quyền xử lý khiếu nại) dùng làm điều kiện định tuyến — xem `FEAT-30`. |
| **Phân khúc khách hàng** | Cách doanh nghiệp phân loại khách hàng theo giá trị hoặc cam kết phục vụ (Thường, Ưu tiên, VIP). Thuộc hồ sơ khách hàng trong CRM; Omnichat chỉ đọc — xem `BR-08.11`. |
| **Tỷ lệ khách bỏ cuộc (Abandonment Rate)** | Tỷ lệ khách hàng rời đi trước khi có Agent nào tiếp nhận — xem `BR-19.4`. |
| **Ca trực** | Khoảng thời gian một Agent được xếp lịch phải trực; đối chiếu với trạng thái làm việc thực tế ra mức tuân thủ ca — xem `FEAT-28`. |
| **Tạm dừng xóa theo yêu cầu pháp lý (Legal Hold)** | Trạng thái làm ngưng hiệu lực thời hạn lưu trữ và yêu cầu xóa trên dữ liệu đang trong diện tranh chấp/điều tra — xem `BR-23.7`. |
| **Định danh dùng chung** | Số điện thoại hoặc email được đánh dấu không thuộc riêng một cá nhân; không bao giờ là căn cứ tự động gộp hồ sơ — xem `BR-02.4b`. |
| **Thời hạn lưu trữ** | Khoảng thời gian doanh nghiệp cam kết giữ nội dung hội thoại và tệp đính kèm — xem `FEAT-23`. |
| **Giờ làm việc** | Lịch làm việc dùng làm căn cứ tính SLA, gửi thông báo ngoài giờ và quyết định nhận hội thoại mới vào hàng đợi — xem `FEAT-24`. |

**Vị trí công việc và quyền.** Agent, Trưởng nhóm, Giám sát viên, Chuyên viên chất lượng và Quản trị viên (Mục 2.3) là **vị trí công việc** dùng để kể nghiệp vụ, không phải tên vai trò mang năng lực cứng. Năng lực của mỗi người là giá trị của các ô (Hội thoại × thao tác) và các quyền quản trị của phân hệ tại Mục 5, đặt được trên vai trò bất kỳ ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 2). Trong các quy tắc, "Trưởng nhóm" khi là **người nhận thông báo** nghĩa là Người phụ trách đơn vị tiếp nhận của hội thoại; "Giám sát viên" khi là người nhận thông báo nghĩa là Người phụ trách các đơn vị cấp trên trong nhánh của đơn vị đó, hoặc người được chỉ định trong chính sách tương ứng.

### 1.5 Tài liệu tham khảo

- [`CONTEXT.md`](../CONTEXT.md) — glossary dùng chung, mục "Omnichat" và "IAM & Phân quyền Workspace".
- [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) v5.0 — hợp đồng quyền: ma trận ô × mức, vai trò dựng sẵn, mức truy cập và hàng đợi, thứ tự hợp nhất quyền, che dữ liệu, nhật ký quyền, tạm ngưng và rời workspace (Nhóm B, E, G, I, J, K của tài liệu đó).
- [`contacts-srs.md`](./contacts-srs.md) — hồ sơ khách hàng, Hồ sơ khách hàng tạm, phân bổ khách hàng tiềm năng, quyền đọc tự động của tuyến hỗ trợ, quyền chủ thể dữ liệu.
- [ADR-0001](../docs/adr/0001-group-policy-conflict-resolution.md) — phân quyền trường hai chiều, hạn chế thắng.
- [ADR-0003](../docs/adr/0003-permission-config-audit-log-fail-closed.md) và [ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md) — nhật ký cấu hình quyền đóng khi lỗi với thao tác nới rộng; thao tác thu hẹp không bị chặn, nhật ký ghi bù.
- [ADR-0005](../docs/adr/0005-omnichat-work-unit-session-vs-case.md) — đơn vị công việc: Phiên trên kênh và Vụ việc của khách hàng.
- [ADR-0008](../docs/adr/0008-data-subject-deletion-contract-omnichat-tickets.md) — hợp đồng xóa dữ liệu theo quyền chủ thể giữa Contacts, Omnichat và Tickets.

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

Khách hàng ngày nay liên hệ doanh nghiệp qua rất nhiều kênh khác nhau — Facebook, Instagram, WhatsApp, Zalo, TikTok, Telegram, email, hoặc khung chat ngay trên website — và kỳ vọng được phản hồi nhanh, nhất quán, dù họ chọn kênh nào. Nếu mỗi kênh phải xử lý ở một công cụ riêng, doanh nghiệp không thể đảm bảo tốc độ phản hồi, dễ bỏ sót tin nhắn, và không có cách nào đo lường chất lượng phục vụ một cách tổng thể. Khi doanh nghiệp có nhiều chi nhánh hoặc nhiều đội, thêm một vấn đề nữa: hội thoại của đội này không được lộ sang đội kia, nhưng vẫn phải chuyển được sang đúng đội khi khách nhắn nhầm chỗ.

Omnichat giải quyết vấn đề này bằng cách gộp mọi kênh giao tiếp vào **một nơi làm việc duy nhất cho Agent**, gắn mỗi kênh với một **đơn vị tiếp nhận** để hội thoại luôn thuộc về đúng đội, tự động phân phối hội thoại đến đúng người có khả năng xử lý, đo lường thời gian phản hồi và mức độ hài lòng của khách hàng, và tự động xử lý những phần việc lặp lại (trả lời ngoài giờ, đóng hội thoại đã xong việc, nhắc nhở khi sắp trễ hẹn) để Agent tập trung vào việc trò chuyện thực sự.

**Phân khúc doanh nghiệp mục tiêu.** Sản phẩm phục vụ đồng thời ba nhóm, theo độ phức tạp vận hành tăng dần: doanh nghiệp nhỏ dùng vài kênh với một đội chung; doanh nghiệp tầm trung có nhiều đội/nhiều dòng sản phẩm, cần Hộp thư riêng, cam kết SLA riêng và báo cáo theo đội; và đơn vị dịch vụ khách hàng thuê ngoài (BPO) phục vụ nhiều khách hàng doanh nghiệp trên cùng một hệ thống, cần quản lý chất lượng, ca trực và báo cáo tách bạch theo từng khách hàng của họ. Mọi yêu cầu PHẢI dùng được ở nhóm nhỏ nhất mà không bắt họ cấu hình những thứ không cần, và PHẢI mở rộng được tới nhóm lớn nhất mà không phải thay mô hình.

### 2.2 Các kênh giao tiếp được hỗ trợ

| Kênh | Đặc điểm nghiệp vụ |
| --- | --- |
| **Facebook** | Tin nhắn qua Trang Facebook (Messenger). |
| **Instagram** | Tin nhắn qua tài khoản doanh nghiệp Instagram, bao gồm cả nhắc tên trong story và chia sẻ bài viết. |
| **WhatsApp** | Tin nhắn qua số WhatsApp Business; danh tính khách hàng chính là số điện thoại của họ. |
| **Zalo** | Tin nhắn qua Official Account (OA); liên kết media của Zalo hết hạn nhanh hơn các kênh khác nên hệ thống tự sao lưu lại để giữ đúng cam kết `BR-02.6`. |
| **TikTok** | Tin nhắn trực tiếp qua tài khoản doanh nghiệp TikTok. |
| **Telegram** | Tin nhắn qua bot Telegram của doanh nghiệp. |
| **Email** | Nhận/gửi qua hộp thư doanh nghiệp; không giới hạn thời gian phản hồi. |
| **Live Chat** | Khung chat nhúng trực tiếp trên website doanh nghiệp; không giới hạn thời gian phản hồi. |

Mỗi kênh trong bảng trên, khi kết nối, PHẢI có đơn vị tiếp nhận hội thoại và đơn vị tiếp nhận khách hàng (`BR-01.9`). Ví dụ một doanh nghiệp có hai chi nhánh: Trang Facebook và Zalo OA của chi nhánh Hà Nội tiếp nhận vào đơn vị "Hỗ trợ – Hà Nội"; widget Live Chat trên website chung tiếp nhận vào "Hỗ trợ" và bật "gồm các đơn vị con" để cả hai chi nhánh cùng nhận; hộp thư email hỗ trợ doanh nghiệp tiếp nhận vào "Chăm sóc khách hàng doanh nghiệp".

Trừ Email và Live Chat, các kênh nhắn tin còn lại đều áp **Cửa sổ phản hồi** — giới hạn do chính nền tảng kênh đặt ra, không phải chính sách của doanh nghiệp. Độ dài cửa sổ khác nhau giữa các kênh và có thể được nền tảng thay đổi bất cứ lúc nào, nên tài liệu không cố định một con số. Hệ thống PHẢI luôn cho Agent thấy thời hạn còn lại **của đúng kênh đang xử lý**, và chặn/cho phép đúng theo giới hạn đang có hiệu lực của kênh đó (`BR-12.2`, `BR-12.9`, `BR-21.4`). Sau khi hết cửa sổ, một số kênh cho phép chủ động liên hệ lại bằng tin nhắn mẫu đã được nền tảng phê duyệt trước; các kênh còn lại thì doanh nghiệp mất khả năng chủ động liên hệ khách trên kênh đó (`BR-12.9`).

### 2.3 Vai trò người dùng (Actor)

Các vị trí dưới đây là vị trí công việc (Mục 1.4). Cột cuối nêu cách thiết lập mặc định bằng vai trò dựng sẵn — danh sách vai trò dựng sẵn và mức mặc định chung là của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`; ma trận chi tiết của loại dữ liệu Hội thoại do Mục 5 khai báo.

| Actor | Vai trò trong module | Thiết lập mặc định |
| --- | --- | --- |
| **Khách hàng (Contact)** | Người nhắn tin qua một trong các kênh. Có thể đã có hồ sơ trong CRM, hoặc là người lạ nhắn lần đầu (Hồ sơ khách hàng tạm). | Không phải thành viên workspace. |
| **Agent** | Nhân viên trực tiếp trò chuyện, xử lý hội thoại: nhận việc, trả lời, chuyển tiếp, ghi chú, giải quyết/đóng hội thoại. | Vai trò dựng sẵn **Nhân viên Hỗ trợ**, là thành viên của đơn vị tiếp nhận. |
| **Trưởng nhóm (Team Leader)** | Điều hành một đội Agent trong ca: theo dõi hàng đợi của đội, nhắc bài, gán lại việc trong đội, xử lý bàn giao khi hết ca. Là cấp đầu tiên nhận cảnh báo. | Vai trò dựng sẵn **Quản lý**; là Người phụ trách đơn vị của đội. |
| **Giám sát viên (Supervisor)** | Chịu trách nhiệm kết quả phục vụ trên nhiều đội/nhiều kênh: theo dõi cam kết SLA, điều phối tải giữa các đội, xem báo cáo tổng hợp, nhận leo thang khi Trưởng nhóm không xử lý kịp. | Vai trò dựng sẵn **Quản lý**; là Người phụ trách đơn vị cấp trên trong nhánh, nên phạm vi Đơn vị và các đơn vị con phủ nhiều đội. |
| **Chuyên viên chất lượng (QA)** | Chấm điểm chất lượng hội thoại theo phiếu chấm, phản hồi kết quả cho Agent, phát hiện lỗi quy trình lặp lại. Không tham gia phục vụ khách hàng. | Không có vai trò dựng sẵn tương ứng; doanh nghiệp tạo vai trò tự tạo theo gợi ý tại Mục 5.3. |
| **Quản trị viên (Admin)** | Cấu hình kênh, hộp thư, quy tắc phân công và kỹ năng, chính sách SLA/leo thang/tự động đóng, giờ làm việc, danh mục lý do xử lý và nhãn, quy tắc tự động hóa, chính sách lưu trữ dữ liệu, mẫu tin nhắn. | Người có toàn quyền, hoặc thành viên được cấp các quyền quản trị của phân hệ tại Mục 5.2. Đặt và đổi đơn vị tiếp nhận của kênh luôn cần Người có toàn quyền (`BR-01.9`). |
| **Bot** | Hệ thống trả lời tự động, tiếp nhận và xử lý hội thoại trước khi bàn giao cho người. Omnichat chịu trách nhiệm về thời điểm gọi Bot, thời điểm bàn giao, và ranh giới quyền của Bot. | Không phải thành viên; chỉ chạm tới đúng hội thoại được giao (`BR-11.7`). |
| **Hệ thống** | Tiếp nhận tin nhắn, phân công tự động, đo SLA, tự động đóng, tạo Hồ sơ khách hàng tạm, áp quy tắc tự động hóa. | Hành động trong phạm vi nguồn và hàng đợi đã khai báo. |
| **Hệ thống bên ngoài** | Các hệ thống ngoài CRM của doanh nghiệp trao đổi dữ liệu với Omnichat — ví dụ hệ thống đơn hàng. Omnichat là bên đọc/ghi có kiểm soát, không sở hữu các dữ liệu đó. Hồ sơ khách hàng, phân khúc và vé hỗ trợ là phân hệ trong cùng CRM, nối với Omnichat qua `BR-02.10` và `BR-21.12`. | — |

### 2.4 Quy ước thời gian nghiệp vụ

Mọi mốc và thời hạn nghiệp vụ trong tài liệu — cam kết SLA, thời hạn lời mời, khoảng chờ trước khi kết luận Ngoại tuyến, Xử lý sau hội thoại, tự động đóng, hạn hiệu lực khảo sát, thời hạn lưu trữ, ca trực — được xác định theo **múi giờ của workspace** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-04`), không theo múi giờ máy chủ hay múi giờ cá nhân. Giờ làm việc dùng để tính cam kết là lịch áp dụng cho hội thoại theo `BR-24.3`; lịch chung của doanh nghiệp chính là lịch làm việc của workspace. Thời hạn tính bằng phút/giờ trôi liên tục; thời hạn tính bằng "giờ làm việc" chỉ trôi trong giờ làm việc của lịch áp dụng; thời hạn tính bằng ngày kết thúc lúc 23:59:59 ngày cuối theo múi giờ workspace. Mọi thời điểm hiển thị cho người dùng theo múi giờ cá nhân của họ.

**Lý do nghiệp vụ:** một hội thoại phải vi phạm cam kết cùng một lúc với Agent, Trưởng nhóm và báo cáo; tính theo múi giờ cá nhân thì một đội trải nhiều múi giờ nhìn thấy ba thời điểm vi phạm khác nhau.

### 2.5 Nguyên tắc nghiệp vụ nền tảng

1. **Hội thoại thuộc về đội tiếp nhận, không thuộc về cá nhân.** Mỗi hội thoại là bản ghi công việc của đơn vị tiếp nhận của kênh, ở lại đơn vị đó suốt vòng đời; Người phụ trách chỉ quyết định ai đang xử lý (`BR-05.12`).
2. **Quyền là cấu hình chung.** Mọi năng lực của phân hệ là giá trị của một ô (Hội thoại × thao tác) hoặc một quyền quản trị (Mục 5), không gắn cứng cho một vị trí hay một tên vai trò ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 2).
3. **Không có hội thoại vô chủ.** Một hội thoại chưa kết thúc luôn có Người phụ trách hoặc đang nằm trong một hàng đợi xác định — kể cả khi người phụ trách bị tạm ngưng hay rời workspace (`BR-05.14`).
4. **Ai không được xem ở màn hình xử lý thì không được xem ở bất kỳ đâu** (`NFR-8`).
5. **Thu hồi không chờ nhật ký.** Thao tác thu hẹp quyền trong phân hệ không bị chặn bởi sự cố nhật ký; nhật ký được ghi bù (`BR-25.5`, [ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md)).
6. **Tham số thuộc doanh nghiệp.** Mọi ngưỡng có thể khác giữa các doanh nghiệp là tham số tại Phụ lục B.

### 2.6 Bảng tổng hợp tính năng nghiệp vụ

| Mảng | Mã | Tính năng |
| --- | --- | --- |
| Hành trình hội thoại | `FEAT-01` | Kết nối kênh giao tiếp & đơn vị tiếp nhận |
| | `FEAT-02` | Tiếp nhận & nhận diện khách hàng qua các kênh |
| | `FEAT-03` | Vòng đời hội thoại |
| | `FEAT-04` | Tự động phân công hội thoại |
| | `FEAT-05` | Hàng đợi theo đơn vị tiếp nhận & lời mời nhận hội thoại |
| | `FEAT-06` | Trạng thái làm việc của Agent |
| | `FEAT-07` | Chuyển tiếp hội thoại |
| | `FEAT-08` | Cam kết thời gian phản hồi (SLA) |
| | `FEAT-09` | Leo thang khi vi phạm SLA |
| | `FEAT-10` | Tự động đóng hội thoại không hoạt động |
| | `FEAT-11` | Bot trả lời tự động & bàn giao cho Agent |
| | `FEAT-12` | Gửi tin nhắn cho khách hàng |
| | `FEAT-13` | Không bỏ lỡ việc và không trùng việc |
| | `FEAT-14` | Khảo sát hài lòng khách hàng (CSAT) |
| | `FEAT-15` | Ghi chú, biểu tượng phản hồi nhanh & lịch sử xử lý |
| | `FEAT-16` | Tìm kiếm nội dung tin nhắn |
| Quản trị doanh nghiệp | `FEAT-17` | Quản lý hộp thư (Inbox) |
| | `FEAT-18` | Mẫu tin nhắn nhanh (Canned Response) |
| Lớp vận hành đội ngũ | `FEAT-19` | Báo cáo vận hành Omnichannel |
| | `FEAT-20` | Báo cáo hiệu suất Agent |
| Hành trình hội thoại | `FEAT-21` | Giao diện xử lý hội thoại của Agent |
| | `FEAT-22` | Trải nghiệm trò chuyện của khách hàng trên Live Chat Widget |
| Quản trị doanh nghiệp | `FEAT-23` | Lưu trữ, xuất, xóa và che dữ liệu hội thoại |
| | `FEAT-24` | Giờ làm việc & lịch nghỉ |
| | `FEAT-25` | Nhật ký thay đổi cấu hình Omnichat |
| Lớp vận hành đội ngũ | `FEAT-26` | Giám sát và can thiệp thời gian thực |
| | `FEAT-27` | Quản lý chất lượng hội thoại (QA) |
| | `FEAT-28` | Ca trực, tuân thủ ca & bàn giao ca |
| | `FEAT-29` | Danh mục lý do xử lý & quản trị nhãn |
| | `FEAT-30` | Kỹ năng & định tuyến theo kỹ năng |
| | `FEAT-31` | Chặn spam & xử lý lạm dụng |
| Hành trình hội thoại | `FEAT-32` | Yêu cầu liên hệ lại |
| Lớp vận hành đội ngũ | `FEAT-33` | Quy tắc tự động hóa |
| Quản trị doanh nghiệp | `FEAT-34` | Chế độ thử nghiệm cấu hình |
| | `FEAT-35` | Nhật ký truy cập dữ liệu khách hàng |
| Hành trình hội thoại | `FEAT-36` | Trao đổi nhiều bên trên kênh Email |

### 2.7 Mục tiêu kinh doanh & Chỉ số thành công

Bốn mục tiêu dưới đây là căn cứ xếp thứ tự ưu tiên khi phải chọn giữa các yêu cầu; một yêu cầu không phục vụ mục tiêu nào cần được chất vấn lại trước khi đưa vào kế hoạch.

| # | Mục tiêu | Đo bằng |
| --- | --- | --- |
| 1 | **Không khách hàng nào bị bỏ sót hoặc bị bỏ rơi giữa chừng** | Tỷ lệ hội thoại được phản hồi trong cam kết; tỷ lệ khách bỏ cuộc trước khi được phục vụ; số hội thoại quá hạn không ai phụ trách |
| 2 | **Một Agent phục vụ được nhiều khách hơn mà chất lượng không giảm** | Số hội thoại hoàn tất trên mỗi Agent mỗi ca; thời gian xử lý trung bình; điểm chất lượng và điểm CSAT đi kèm |
| 3 | **Giám sát viên điều hành được đội ngũ mà không cần chờ báo cáo cuối ngày** | Thời gian từ lúc phát sinh vi phạm tới lúc có người can thiệp; tỷ lệ vi phạm SLA được xử lý trước khi khách hàng phàn nàn |
| 4 | **Doanh nghiệp tự cấu hình được quy trình của mình mà không cần nhà cung cấp can thiệp** | Tỷ lệ doanh nghiệp tự đổi quy tắc định tuyến/SLA/tự động hóa trong 90 ngày đầu; số yêu cầu tùy chỉnh phải chuyển cho đội phát triển |

### 2.8 Luồng nghiệp vụ đầu – cuối

Quản trị viên kết nối kênh và Người có toàn quyền đặt đơn vị tiếp nhận (`FEAT-01`) → khách nhắn tin, hệ thống nhận diện khách hoặc tạo Hồ sơ khách hàng tạm thuộc đơn vị tiếp nhận khách hàng của kênh (`FEAT-02`) → hội thoại mới thuộc đơn vị tiếp nhận hội thoại, có thể qua Bot trước (`FEAT-11`) → phân công tự động hoặc vào hàng đợi của đơn vị, thành viên đơn vị tự nhận (`FEAT-04`, `FEAT-05`) → Agent trả lời trong cam kết SLA (`FEAT-08`, `FEAT-12`), chuyển tiếp cho người khác hoặc chuyển sang hàng đợi của đơn vị khác khi cần (`FEAT-07`, `BR-05.15`) → giải quyết, Xử lý sau hội thoại, khảo sát CSAT (`FEAT-03`, `FEAT-06`, `FEAT-14`) → số liệu vào báo cáo theo đúng phạm vi người xem (`FEAT-19`, `FEAT-20`). Khi Agent bị tạm ngưng hoặc rời workspace, hội thoại đang mở của họ được trả về hàng đợi của đơn vị tiếp nhận hoặc giao cho người xử lý thay (`BR-05.14`).

---

## 3. Đặc tả yêu cầu chức năng

### FEAT-01 — Kết nối kênh giao tiếp & đơn vị tiếp nhận

**Mô tả nghiệp vụ:** Cho phép quản trị viên kết nối tài khoản doanh nghiệp trên từng nền tảng vào hệ thống để bắt đầu tiếp nhận và trả lời tin nhắn khách hàng qua kênh đó. Mỗi kênh khi kết nối PHẢI khai báo **Thuộc tính kênh** — căn cứ để mọi quy tắc khác áp dụng đúng mà không cần biết tên kênh — và PHẢI có **đơn vị tiếp nhận** để hội thoại và hồ sơ khách hàng sinh ra từ kênh thuộc về đúng đội.

**Vai trò sử dụng chính:** Người có quyền quản trị Quản lý kênh hội thoại (kết nối, cấu hình); Người có toàn quyền (đặt và đổi đơn vị tiếp nhận).

**Điều kiện tiên quyết:** Doanh nghiệp đã có tài khoản trên nền tảng kênh; cây tổ chức có ít nhất một đơn vị.

**Luồng chính:**

1. Quản trị viên chọn loại kênh muốn kết nối.
2. Quản trị viên chứng minh doanh nghiệp có quyền sử dụng tài khoản đó trên nền tảng kênh — đăng nhập xác thực với nền tảng và chọn đúng tài khoản/trang, hoặc khai báo thông tin kết nối do nền tảng cấp.
3. Hệ thống ghi nhận Thuộc tính kênh (`BR-01.8`).
4. Hệ thống đề xuất đơn vị tiếp nhận hội thoại và đơn vị tiếp nhận khách hàng theo mặc định của loại kênh (`BR-01.9`); người có toàn quyền xác nhận hoặc chọn đơn vị khác.
5. Hệ thống xác nhận kết nối thành công; kênh bắt đầu phục vụ được khách hàng và quản trị viên nhìn thấy rõ điều đó.

**Luồng ngoại lệ:**

- Kênh chưa có đơn vị tiếp nhận → không kích hoạt được; màn hình nêu rõ cần Người có toàn quyền đặt đơn vị tiếp nhận.
- Tài khoản kênh đang kết nối cho một doanh nghiệp khác → từ chối (`BR-01.1`).

**Quy tắc nghiệp vụ:**

- **`BR-01.1` (Một kênh, một doanh nghiệp):** Một kênh PHẢI thuộc đúng một doanh nghiệp tại một thời điểm — không được kết nối cùng một tài khoản kênh cho hai doanh nghiệp khác nhau.

  **Lý do nghiệp vụ:** tin nhắn của khách gửi một doanh nghiệp lọt sang doanh nghiệp khác là sự cố rò rỉ dữ liệu, và không bên nào chịu trách nhiệm trả lời.

- **`BR-01.2` (Tình trạng kênh luôn rõ ràng):** Quản trị viên PHẢI luôn biết một kênh **có đang phục vụ được khách hàng hay không**, và nếu không thì nguyên nhân thuộc nhóm nào cùng việc cần làm để khôi phục (chưa hoàn tất kết nối, chưa có đơn vị tiếp nhận, doanh nghiệp chủ động ngắt, hay đang gặp trục trặc). Kênh KHÔNG ĐƯỢC ở trạng thái mơ hồ.

  **Lý do nghiệp vụ:** một kênh ngừng hoạt động mà không ai biết là toàn bộ khách trên kênh đó bị bỏ rơi trong im lặng.

- **`BR-01.3` (Kiểm tra định kỳ):** Hệ thống PHẢI tự động kiểm tra định kỳ tình trạng hoạt động của từng kênh đang kết nối và cảnh báo quản trị viên khi kênh mất kết nối hoặc quyền truy cập sắp/đã hết hạn.

  **Lý do nghiệp vụ:** quyền truy cập do nền tảng cấp có hạn; phát hiện sau khi hết hạn là đã mất tin nhắn của khách.

- **`BR-01.4` (Ngắt có thể phục hồi):** Ngắt kết nối một kênh PHẢI là thao tác phục hồi được (kết nối lại); xóa hẳn một kênh PHẢI yêu cầu ngắt kết nối trước. Hội thoại đã phát sinh từ kênh vẫn thuộc đơn vị tiếp nhận của chúng sau khi kênh bị ngắt hoặc xóa.

  **Lý do nghiệp vụ:** ngắt thường là tạm thời (đổi mật khẩu, đổi tài khoản quản trị trang); xóa nhầm một kênh không được kéo theo mất quyền theo dõi lịch sử của cả đội.

- **`BR-01.5` (Cấu hình theo kênh):** Quản trị viên PHẢI cấu hình được theo từng kênh: tin nhắn tự động trả lời, giờ làm việc riêng (nếu khác lịch chung), và quy tắc phân công mặc định riêng cho kênh.

  **Lý do nghiệp vụ:** cùng một doanh nghiệp, Zalo OA phục vụ khách lẻ cả ngày còn email phục vụ khách doanh nghiệp giờ hành chính.

- **`BR-01.6` (Thông tin gửi nhận thư):** Với kênh có Thuộc tính trao đổi bằng thư, quản trị viên PHẢI cấu hình được riêng thông tin gửi/nhận thư.

  **Lý do nghiệp vụ:** địa chỉ hiển thị, địa chỉ trả lời và tên người gửi là một phần thương hiệu của doanh nghiệp với khách hàng.

- **`BR-01.7` (Thông tin xác thực không hiển thị lại):** Thông tin xác thực đăng nhập của từng kênh PHẢI được bảo mật, không bao giờ hiển thị lại cho bất kỳ ai sau khi đã lưu.

  **Lý do nghiệp vụ:** ai có thông tin này là gửi được tin nhắn nhân danh doanh nghiệp từ bên ngoài hệ thống.

- **`BR-01.8` (Thuộc tính kênh):** Mỗi kênh khi kết nối PHẢI khai báo được **Thuộc tính kênh**, tối thiểu gồm: (a) trò chuyện **đồng thời** (khách đang chờ ngay trên màn hình) hay **không đồng thời**; (b) có áp **Cửa sổ phản hồi** hay không; (c) có cho phép chủ động liên hệ lại bằng **tin nhắn mẫu đã phê duyệt trước** hay không; (d) **định danh người gửi có được chính nền tảng xác minh là của một cá nhân** hay không; (e) có hỗ trợ **tin nhắn dạng nút bấm** hay không và tối đa bao nhiêu lựa chọn. Mọi quy tắc khác trong tài liệu PHẢI viện dẫn thuộc tính, KHÔNG ĐƯỢC viện dẫn tên kênh cụ thể.

  **Lý do nghiệp vụ:** để một kênh mới chỉ cần khai báo thuộc tính là chạy đúng, không phải sửa quy tắc và không phải sửa tài liệu.

- **`BR-01.9` (Đơn vị tiếp nhận của kênh):** Mỗi kênh PHẢI có hai đơn vị tiếp nhận, khai báo theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10` (a): (1) **đơn vị tiếp nhận hội thoại** — nơi mọi hội thoại của kênh thuộc về (`BR-05.11`); (2) **đơn vị tiếp nhận khách hàng** — nơi Hồ sơ khách hàng tạm và khách hàng tiềm năng sinh ra từ kênh thuộc về cho tới khi có Người phụ trách (`BR-02.10`). Mặc định của mỗi đơn vị lấy theo loại kênh tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-35-01` (hai loại nguồn: "hội thoại từ kênh [loại kênh]" và "khách hàng tiềm năng từ kênh [loại kênh]"), có ghi đè theo đơn vị; cách tìm mặc định, ai được chọn khác mặc định, ai được đổi, lựa chọn ở lại/chuyển khi đổi và bật/tắt "gồm các đơn vị con" theo đúng [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.12` — chỉ Người có toàn quyền đổi đơn vị tiếp nhận của một kênh đang có. Kênh chưa có đơn vị tiếp nhận hội thoại không kích hoạt được.

  **Lý do nghiệp vụ:** đơn vị tiếp nhận quyết định cả một đội thấy hàng đợi của kênh bất kể mức Xem của từng người; để người kết nối kênh tự chọn là để họ tự mở dữ liệu khách hàng cho đội mình. Hai đơn vị tách nhau vì hội thoại là việc của đội hỗ trợ, còn khách hàng tiềm năng là việc của đội kinh doanh.

- **`BR-01.10` (Hộp thư email thuộc đúng một phân hệ):** Mỗi hộp thư email là nguồn của **đúng một** phân hệ — Omnichat (mỗi thư là hội thoại) hoặc Vé hỗ trợ (mỗi thư tạo hoặc nối vào vé) theo [`tickets-srs.md`](./tickets-srs.md) `BR-34.10` — do Người có toàn quyền chọn khi kết nối; không hộp thư nào là nguồn của cả hai. Đổi phân hệ của một hộp thư đang kết nối là **một thao tác "chuyển phân hệ" duy nhất**, không phải ngắt kết nối rồi kết nối lại, và chỉ Người có toàn quyền thực hiện. Từ thời điểm xác nhận, thư mới đến đi vào phân hệ mới, không có khoảng trống nào mà thư không thuộc phân hệ nào; hội thoại và vé đã có giữ nguyên ở phân hệ cũ với đơn vị tiếp nhận hiện tại của chúng (thư trả lời vào một hội thoại cũ vẫn nối vào hội thoại đó cho tới khi hội thoại Đã đóng). Hộp thư chuyển sang Omnichat phải có đủ cả đơn vị tiếp nhận hội thoại và đơn vị tiếp nhận khách hàng (`BR-01.9`); thiếu một trong hai thì không xác nhận được. Lượt chuyển ghi nhật ký thay đổi cấu hình quyền (người thực hiện, thời điểm, phân hệ trước và sau) và đóng khi lỗi (`BR-25.2`).

  **Lý do nghiệp vụ:** cùng một thư sinh ra cả hội thoại lẫn vé là hai đội cùng trả lời một khách, hai đồng hồ cam kết và hai đơn vị tiếp nhận cho cùng một việc — giống hệt rủi ro một tài khoản kênh thuộc hai doanh nghiệp (`BR-01.1`).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-01.1.1` | Zalo OA đang kết nối cho doanh nghiệp A | Quản trị viên doanh nghiệp B kết nối cùng OA | Từ chối, nêu tài khoản đã kết nối cho doanh nghiệp khác, không nêu tên doanh nghiệp đó |
| `AC-01.2.1` | Kênh mất quyền truy cập | Quản trị viên mở danh sách kênh | Kênh hiện "Không phục vụ được", nguyên nhân "Trục trặc kết nối" và nút kết nối lại |
| `AC-01.2.2` | Kênh đã xác thực nhưng chưa có đơn vị tiếp nhận | Mở danh sách kênh | Kênh hiện "Không phục vụ được — chưa có đơn vị tiếp nhận" |
| `AC-01.3.1` | Quyền truy cập của Trang Facebook sắp hết hạn | Tới mốc kiểm tra định kỳ | Quản trị viên nhận cảnh báo trước khi hết hạn |
| `AC-01.4.1` | Kênh đang kết nối | Bấm "Xóa kênh" | Từ chối, yêu cầu ngắt kết nối trước |
| `AC-01.4.2` | Kênh đã ngắt kết nối | Kết nối lại | Kênh phục vụ lại; lịch sử hội thoại cũ vẫn còn và vẫn thuộc đơn vị tiếp nhận cũ |
| `AC-01.5.1` | Email đặt giờ làm việc riêng 08:00–17:00, lịch chung 24/7 | Khách gửi thư lúc 20:00 | Thông báo ngoài giờ theo lịch riêng của kênh email |
| `AC-01.6.1` | Kênh thư điện tử mới | Quản trị viên mở cấu hình | Nhập được riêng địa chỉ gửi, địa chỉ trả lời và tên hiển thị |
| `AC-01.7.1` | Thông tin kết nối đã lưu | Chính người đã nhập mở lại cấu hình | Chỉ thấy ký hiệu che; không có cách xem lại giá trị |
| `AC-01.8.1` | Một loại kênh mới khai báo "đồng thời, không có Cửa sổ phản hồi" | Kết nối và nhận tin | Kênh áp đúng điểm tải, cam kết im lặng tối đa và không có đồng hồ cửa sổ, không cần sửa quy tắc nào |
| `AC-01.9.1` | Đơn vị tiếp nhận mặc định theo loại nguồn của tài liệu IAM: hội thoại từ Zalo → "Hỗ trợ", khách hàng tiềm năng từ Zalo → "Kinh doanh" | Quản trị viên (không có toàn quyền) kết nối Zalo OA mới | Kênh nhận mặc định "Hỗ trợ" và "Kinh doanh"; lựa chọn đơn vị khác bị vô hiệu kèm "cần Người có toàn quyền" |
| `AC-01.9.2` | Chi nhánh Đà Nẵng ghi đè: hội thoại từ Facebook → "Hỗ trợ – Đà Nẵng"; người kết nối có Đơn vị chính "Hỗ trợ – Đà Nẵng" | Kết nối Trang Facebook của chi nhánh | Đơn vị tiếp nhận hội thoại đề xuất là "Hỗ trợ – Đà Nẵng" |
| `AC-01.9.3` | Kênh đang có 40 hội thoại chưa kết thúc | Người có toàn quyền đổi đơn vị tiếp nhận hội thoại, chọn "ở lại" | 40 hội thoại vẫn thuộc đơn vị cũ; hội thoại mới thuộc đơn vị mới; màn hình đã báo trước số hội thoại và số người có phạm vi thay đổi |
| `AC-01.9.4` | Không ai đặt mặc định cho loại kênh Telegram | Kết nối bot Telegram | Kênh không kích hoạt được tới khi Người có toàn quyền đặt đơn vị tiếp nhận |
| `AC-01.10.1` | Hộp thư hotro@ đang là nguồn của phân hệ Vé hỗ trợ | Quản trị viên kết nối hotro@ làm kênh Omnichat | Từ chối, nêu hộp thư đang là nguồn của Vé hỗ trợ và thao tác "Chuyển phân hệ" |
| `AC-01.10.2` | Kết nối hộp thư email mới | Bước chọn phân hệ | Bắt buộc chọn Omnichat hoặc Vé hỗ trợ trước khi kích hoạt; không có lựa chọn "cả hai" |
| `AC-01.10.3` | Hộp thư hotro@ thuộc Omnichat, có 30 hội thoại mở | Người có toàn quyền "Chuyển phân hệ" sang Vé hỗ trợ, xác nhận lúc 10:00 | Thư đến từ 10:00 thành vé, không thư nào bị lỡ; 30 hội thoại vẫn ở Omnichat và xử lý tiếp được; nhật ký cấu hình quyền có dòng chuyển phân hệ |
| `AC-01.10.4` | Quản trị viên không có toàn quyền | Tìm thao tác "Chuyển phân hệ" | Không khả dụng |
| `AC-01.10.5` | Nhật ký cấu hình quyền gặp sự cố | Người có toàn quyền chuyển phân hệ | Thao tác bị hủy, hộp thư giữ phân hệ cũ |
| `AC-01.10.6` | Hộp thư thuộc Vé hỗ trợ; chưa đặt đơn vị tiếp nhận khách hàng cho hộp thư này | Người có toàn quyền chuyển phân hệ sang Omnichat | Nút xác nhận bị vô hiệu tới khi đặt đủ hai đơn vị tiếp nhận |

**Tham chiếu:** `BR-01.8` → issue [#71](https://github.com/crmsaassaudi/product-management/issues/71).

---

### FEAT-02 — Tiếp nhận & nhận diện khách hàng qua các kênh

**Mô tả nghiệp vụ:** Nhận tin nhắn khách hàng gửi tới từ bất kỳ kênh nào đang kết nối, hiển thị thống nhất trong một khung chat, và xác định đúng khách hàng đó là ai trong CRM (nếu đã có hồ sơ). Hồ sơ khách hàng sinh ra từ hội thoại đi theo đúng mô hình bản ghi chờ phân công của [`contacts-srs.md`](./contacts-srs.md).

**Vai trò sử dụng chính:** Hệ thống (tiếp nhận, nhận diện); Agent (xác nhận gợi ý khớp, liên kết hồ sơ); Khách hàng (nguồn tin nhắn).

**Điều kiện tiên quyết:** Kênh đang phục vụ được khách hàng (`BR-01.2`).

**Luồng chính:**

1. Khách hàng gửi tin nhắn (văn bản, ảnh, video, tệp, vị trí, nhãn dán, hoặc bấm một nút tương tác) qua một kênh đang kết nối.
2. Hệ thống xác thực tin nhắn thực sự đến từ đúng nền tảng kênh (chống giả mạo).
3. Hệ thống chuẩn hóa nội dung về định dạng hiển thị thống nhất.
4. Hệ thống tìm khách hàng đã có hồ sơ khớp với người gửi (theo liên kết đã lưu, hoặc theo email/số điện thoại tùy kênh).
5. Nếu tìm thấy, gắn tin nhắn vào đúng hồ sơ; nếu không, tạo Hồ sơ khách hàng tạm thuộc đơn vị tiếp nhận khách hàng của kênh (`BR-02.10`).
6. Tin nhắn được gắn vào hội thoại đang mở của khách hàng trên kênh đó, hoặc tạo hội thoại mới thuộc đơn vị tiếp nhận hội thoại của kênh (`BR-05.12`).

**Quy tắc nghiệp vụ:**

- **`BR-02.1` (Xác thực nguồn):** Hệ thống PHẢI xác thực mọi tin nhắn đến thực sự từ đúng nền tảng kênh trước khi xử lý.

  **Lý do nghiệp vụ:** một tin nhắn giả mạo hiển thị như tin khách thật có thể lừa Agent tiết lộ thông tin của khách.

- **`BR-02.2` (Một tin, một lần):** Một tin nhắn khách hàng gửi một lần PHẢI chỉ xuất hiện đúng một lần trong khung chat và chỉ được tính đúng một lần trong mọi báo cáo, kể cả khi hệ thống nhận được nhiều bản sao của cùng tin nhắn đó.

  **Lý do nghiệp vụ:** tin trùng làm Agent trả lời hai lần và thổi phồng khối lượng công việc trong báo cáo.

- **`BR-02.3` (Điều kiện tự động gộp):** Hệ thống chỉ được **tự động** gộp một Hồ sơ khách hàng tạm vào một hồ sơ đã có khi thỏa đồng thời: (a) kênh khai báo **định danh người gửi được nền tảng xác minh là của một cá nhân** (`BR-01.8`); (b) định danh đó chưa được đánh dấu là **Định danh dùng chung**; (c) doanh nghiệp chưa tắt tự động gộp (`CFG-02-01`). Điều kiện (a) là điều kiện cần, không phải điều kiện đủ.

  **Lý do nghiệp vụ:** gộp nhầm hai người thành một khách hàng là hậu quả không chấp nhận được — người này đọc được lịch sử của người kia.

- **`BR-02.4` (Gợi ý khớp thay vì tự gộp):** Với mọi trường hợp không thỏa `BR-02.3`, hệ thống KHÔNG ĐƯỢC tự động gộp chỉ vì trùng số điện thoại/email. Hệ thống PHẢI hiển thị gợi ý khớp cho Agent và chỉ gộp khi Agent xác nhận. Gợi ý chỉ nêu những hồ sơ mà Agent được phép thấy theo mức (Khách hàng, Xem) của mình cộng quyền đọc tự động khi đang xử lý hội thoại ([`contacts-srs.md`](./contacts-srs.md) `BR-35.4`); hồ sơ nằm ngoài phạm vi đó hiển thị "Có hồ sơ khớp ngoài phạm vi của bạn" để Agent chuyển cho người có quyền, không lộ tên. Mọi giá trị định danh trong gợi ý hiển thị theo mẫu che của `BR-23.9`.

  **Lý do nghiệp vụ:** số điện thoại/email trùng trên các kênh không có định danh xác minh không đủ tin cậy — có thể là số hotline dùng chung, email của công ty đối tác; còn gợi ý khớp là đường lộ dữ liệu dễ bị bỏ quên nhất (`NFR-8`).

- **`BR-02.4b` (Định danh dùng chung):** Agent hoặc Quản trị viên PHẢI đánh dấu được một số điện thoại/email là **Định danh dùng chung** khi có (Hội thoại, Sửa) bao phủ hội thoại đang chứa định danh đó. Từ thời điểm được đánh dấu, định danh đó không bao giờ được dùng để tự động gộp trên bất kỳ kênh nào — chỉ hiển thị gợi ý. Doanh nghiệp CÓ THỂ tắt hẳn tự động gộp cho toàn workspace (`CFG-02-01`).

  **Lý do nghiệp vụ:** số tổng đài của đại lý hay email chung của một phòng ban là của nhiều người; một lần gộp sai theo định danh đó sẽ lặp lại với mọi người dùng chung nó.

- **`BR-02.5` (Hồ sơ khách hàng tạm):** Khi chưa nhận diện được khách hàng nào khớp, hệ thống PHẢI tự tạo một Hồ sơ khách hàng tạm theo [`contacts-srs.md`](./contacts-srs.md) `BR-01.1b`, `BR-01.1c` để hội thoại có nơi gắn vào. Người có (Hội thoại, Sửa) bao phủ hội thoại CÓ THỂ liên kết hội thoại với một hồ sơ có sẵn mà mình được phép thấy bất kỳ lúc nào; tạo khách hàng tiềm năng mới từ hội thoại theo `BR-02.10`.

  **Lý do nghiệp vụ:** không có hồ sơ tạm thì lịch sử trao đổi với khách vãng lai mất ngay khi khách để lại số điện thoại ở lần sau.

- **`BR-02.6` (Xem lại đầy đủ trong thời hạn lưu trữ):** Toàn bộ nội dung khách gửi, gồm tệp đính kèm, PHẢI xem lại được đầy đủ trong suốt **thời hạn lưu trữ** (`FEAT-23`), độc lập với việc nền tảng kênh còn giữ nội dung đó hay không.

  **Lý do nghiệp vụ:** khi có khiếu nại, doanh nghiệp phải đưa ra được bằng chứng; nền tảng kênh xóa media theo chính sách riêng của họ.

- **`BR-02.7` (Thông báo ngoài giờ):** Nếu đang ngoài giờ làm việc (`FEAT-24`) và chưa có Agent nào xử lý hội thoại, hệ thống CÓ THỂ tự động gửi thông báo ngoài giờ cho khách, theo cấu hình của doanh nghiệp/kênh.

  **Lý do nghiệp vụ:** khách biết doanh nghiệp đã nhận tin và khi nào được trả lời thì ít nhắn dồn và ít bỏ đi sang đối thủ.

- **`BR-02.8` (Gộp phải gỡ lại được):** Mọi thao tác gộp hồ sơ khách hàng từ Omnichat PHẢI gỡ lại được theo [`contacts-srs.md`](./contacts-srs.md) `FEAT-20`. Khi gỡ, mỗi hội thoại PHẢI quay về đúng hồ sơ mà nó phát sinh. Người gộp và người gỡ PHẢI được ghi lại trên hồ sơ khách hàng.

  **Lý do nghiệp vụ:** gộp nhầm là không tránh được hoàn toàn; không gỡ được thì tin nhắn của người này nằm lại vĩnh viễn trong hồ sơ của người kia.

- **`BR-02.9` (Hội thoại song song của cùng khách hàng):** Khi một khách hàng có nhiều hội thoại mở cùng lúc trên nhiều kênh, hệ thống PHẢI cho người đang xử lý một trong số đó thấy các hội thoại song song còn lại và ai đang phụ trách chúng — trong phạm vi mức (Hội thoại, Xem) của người đó; hội thoại song song ngoài phạm vi hiển thị là "Đang có hội thoại khác do đơn vị khác xử lý" kèm tên đơn vị, không lộ nội dung. Hệ thống PHẢI cảnh báo cho Người phụ trách đơn vị tiếp nhận của các hội thoại liên quan. Đây là mức bảo vệ tối thiểu bắt buộc, độc lập với cách Vụ việc của khách hàng được đặc tả (Mục 7, câu 1).

  **Lý do nghiệp vụ:** cùng một người được hai Agent phục vụ song song mà không ai biết là một sự cố với khách hàng, đồng thời làm sai lệch khối lượng công việc và điểm CSAT; nhưng biết là "có người khác đang xử lý" không được trở thành cách đọc nội dung của đơn vị khác.

- **`BR-02.10` (Hồ sơ khách hàng sinh ra từ hội thoại là bản ghi chờ phân công):** Hồ sơ khách hàng tạm do hệ thống tạo (`BR-02.5`) và khách hàng tiềm năng tạo từ hội thoại — khi hồ sơ tạm được bổ sung email/số điện thoại hợp lệ và trở thành khách hàng chính thức ([`contacts-srs.md`](./contacts-srs.md) `BR-01.1b`), khi người dùng bấm "Tạo khách hàng tiềm năng từ hội thoại", hoặc khi quy tắc tự động hóa tạo (`FEAT-33`) — đều là **bản ghi chờ phân công** của phân hệ Khách hàng theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.11`, `BR-35.10` (b):
  - thuộc **đơn vị tiếp nhận khách hàng của kênh** (`BR-01.9`) cho tới khi có Người phụ trách, sau đó theo Người phụ trách như mọi khách hàng; hồ sơ không thuộc đơn vị tiếp nhận hội thoại chỉ vì phát sinh từ hội thoại;
  - được phân bổ theo [`contacts-srs.md`](./contacts-srs.md) `FEAT-31` với nguồn gốc là kênh của hội thoại ([`contacts-srs.md`](./contacts-srs.md) `FEAT-32`); Hồ sơ khách hàng tạm không được phân bổ cho tới khi trở thành khách hàng chính thức;
  - người bấm tạo cần (Khách hàng, Tạo) = Có và **không** tự trở thành Người phụ trách; người đó nhận được khách hàng tiềm năng về mình chỉ bằng cách nhận việc từ hàng đợi khách hàng khi đủ điều kiện của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.11`;
  - người đang xử lý hội thoại đọc được hồ sơ gắn với hội thoại qua quyền đọc tự động của tuyến hỗ trợ ([`contacts-srs.md`](./contacts-srs.md) `BR-35.4`), hết hiệu lực khi hội thoại đóng;
  - khi Người phụ trách của khách hàng tiềm năng chưa chuyển đổi bị tạm ngưng hoặc rời workspace, trả về hàng đợi theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.13` — về hàng đợi hiện tại của nguồn là kênh đã sinh ra nó.

  **Lý do nghiệp vụ:** Agent hỗ trợ không phải người bán; nếu khách hàng tiềm năng sinh ra từ hội thoại tự thuộc về Agent hoặc đội hỗ trợ, đội kinh doanh không bao giờ thấy nó trong hàng đợi phân bổ và cam kết liên hệ lần đầu của [`contacts-srs.md`](./contacts-srs.md) `BR-31.7` không chạy.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-02.1.1` | Một tin tự xưng đến từ Trang Facebook nhưng không qua xác thực của nền tảng | Hệ thống nhận tin | Tin bị loại, không hiển thị cho Agent, đội vận hành nhận cảnh báo |
| `AC-02.2.1` | Nền tảng gửi lại cùng một tin do lỗi mạng | Hệ thống nhận bản sao | Khung chat chỉ có một tin; báo cáo khối lượng tin chỉ đếm một |
| `AC-02.3.1` | Kênh có định danh xác minh, định danh chưa đánh dấu dùng chung, tự động gộp đang bật | Khách cũ nhắn lại từ số đã có hồ sơ | Hội thoại gắn ngay vào hồ sơ có sẵn |
| `AC-02.3.2` | Doanh nghiệp tắt tự động gộp | Khách nhắn từ số đã có hồ sơ trên kênh có định danh xác minh | Chỉ hiện gợi ý khớp, không tự gộp |
| `AC-02.4.1` | Khách nhắn qua kênh không có định danh xác minh, trùng số điện thoại một hồ sơ trong phạm vi của Agent | Agent mở hội thoại | Thấy gợi ý "Có vẻ đây là [tên]"; hồ sơ chưa gộp tới khi Agent xác nhận |
| `AC-02.4.2` | Hồ sơ trùng nằm ngoài phạm vi Xem của Agent | Agent mở hội thoại | Thấy "Có hồ sơ khớp ngoài phạm vi của bạn", không thấy tên hay số điện thoại đầy đủ |
| `AC-02.4b.1` | Số tổng đài đại lý đã đánh dấu Định danh dùng chung | Tin mới tới từ số đó trên kênh có định danh xác minh | Không tự gộp; chỉ hiện gợi ý |
| `AC-02.4b.2` | Agent không có (Hội thoại, Sửa) bao phủ hội thoại | Mở hội thoại, tìm thao tác "Đánh dấu Định danh dùng chung" | Thao tác bị vô hiệu kèm lý do |
| `AC-02.5.1` | Khách lạ nhắn lần đầu qua Live Chat, chưa có email/số điện thoại | Hệ thống nhận tin | Hồ sơ "Khách vãng lai #…" được tạo và gắn với hội thoại |
| `AC-02.5.2` | Agent đang phụ trách hội thoại của Hồ sơ khách hàng tạm | Liên kết hội thoại với một khách hàng có sẵn trong phạm vi của mình | Hội thoại chuyển sang hồ sơ đó; dòng thời gian ghi người liên kết |
| `AC-02.6.1` | Ảnh khách gửi qua Zalo 60 ngày trước, nền tảng đã xóa liên kết gốc, còn trong thời hạn lưu trữ | Agent mở lại ảnh | Xem được đầy đủ |
| `AC-02.7.1` | Ngoài giờ, doanh nghiệp bật thông báo ngoài giờ, chưa ai nhận hội thoại | Khách nhắn tin | Khách nhận thông báo ngoài giờ một lần |
| `AC-02.8.1` | Hai hồ sơ đã bị gộp nhầm, mỗi hồ sơ có một hội thoại | Người có quyền gỡ gộp | Hai hồ sơ độc lập; mỗi hội thoại về đúng hồ sơ ban đầu; hồ sơ ghi người gộp và người gỡ |
| `AC-02.9.1` | Cùng khách mở hội thoại trên Facebook (do A phụ trách) và Zalo (do B phụ trách), cùng đơn vị tiếp nhận | A mở hội thoại Facebook | A thấy hội thoại Zalo song song và B đang phụ trách; Người phụ trách đơn vị nhận cảnh báo |
| `AC-02.9.2` | Hội thoại Zalo thuộc đơn vị tiếp nhận "Hỗ trợ – Đà Nẵng", ngoài phạm vi Xem của A | A mở hội thoại Facebook | A thấy "Đang có hội thoại khác do Hỗ trợ – Đà Nẵng xử lý", không thấy nội dung |
| `AC-02.10.1` | Kênh Zalo: đơn vị tiếp nhận hội thoại "Hỗ trợ", đơn vị tiếp nhận khách hàng "Kinh doanh" | Hồ sơ tạm được bổ sung số điện thoại hợp lệ, không trùng ai | Hồ sơ thành khách hàng tiềm năng chưa có Người phụ trách, thuộc "Kinh doanh", vào hàng đợi phân bổ của [`contacts-srs.md`](./contacts-srs.md); nguồn gốc ghi "Zalo" |
| `AC-02.10.2` | Agent có (Khách hàng, Tạo) = Có, đang phụ trách hội thoại | Bấm "Tạo khách hàng tiềm năng từ hội thoại" | Khách hàng tiềm năng được tạo, không có Người phụ trách, thuộc đơn vị tiếp nhận khách hàng của kênh; Agent không trở thành Người phụ trách |
| `AC-02.10.3` | Agent có (Khách hàng, Tạo) = Không có | Tìm thao tác tạo khách hàng tiềm năng trong hội thoại | Thao tác bị vô hiệu kèm "Vai trò của bạn không có quyền tạo khách hàng" |
| `AC-02.10.4` | Thành viên "Hỗ trợ" không thuộc "Kinh doanh", có (Khách hàng, Xem) = Đơn vị của mình | Mở danh sách khách hàng tiềm năng chưa phân công | Không thấy khách hàng tiềm năng vừa tạo từ hội thoại; vẫn đọc được hồ sơ đó khi đang xử lý hội thoại gắn với nó |
| `AC-02.10.5` | Người phụ trách của một khách hàng tiềm năng chưa chuyển đổi sinh ra từ Zalo bị tạm ngưng, người thực hiện chọn "trả về hàng đợi" | Xác nhận | Khách hàng tiềm năng về hàng đợi khách hàng hiện tại của kênh Zalo, không về đơn vị của người bị tạm ngưng |

**Tham chiếu:** `BR-02.4` → issue [#15](https://github.com/crmsaassaudi/product-management/issues/15). `BR-02.3`, `BR-02.4b`, `BR-02.8`, `BR-02.9` → issue [#62](https://github.com/crmsaassaudi/product-management/issues/62).

---

### FEAT-03 — Vòng đời hội thoại

**Mô tả nghiệp vụ:** Mỗi hội thoại có một trạng thái rõ ràng phản ánh nó đang cần xử lý hay đã xong, giúp Agent và Giám sát viên biết việc gì cần làm và tránh bỏ sót.

**Vai trò sử dụng chính:** Agent (đổi trạng thái thủ công, cần (Hội thoại, Sửa) bao phủ hội thoại); Hệ thống (đổi trạng thái tự động theo quy tắc).

**Điều kiện tiên quyết:** Không có.

**Các trạng thái chuẩn:** Đang mở, Tạm hoãn, Chờ đóng (đang trong thời gian ân hạn trước khi tự động đóng), Đã giải quyết, Đã đóng. Đã giải quyết và Đã đóng là hai **trạng thái kết thúc**; mọi trạng thái còn lại là **chưa kết thúc**. Doanh nghiệp CÓ THỂ bổ sung trạng thái chờ của riêng mình theo `BR-03.8`.

**Luồng chính:**

1. Hội thoại mới bắt đầu ở Đang mở.
2. Agent xử lý xong, đánh dấu Đã giải quyết (hoặc hệ thống tự động đóng — `FEAT-10`).
3. Nếu khách nhắn lại: hội thoại Đã giải quyết được mở lại (nếu còn trong hạn) hoặc một hội thoại mới được tạo; hội thoại Đã đóng luôn tạo hội thoại mới.

**Quy tắc nghiệp vụ:**

- **`BR-03.1` (Trạng thái khởi đầu):** Hội thoại mới của một khách hàng PHẢI bắt đầu ở Đang mở.

  **Lý do nghiệp vụ:** một hội thoại mới bắt đầu ở bất kỳ trạng thái nào khác sẽ không vào danh sách việc và không chạy cam kết.

- **`BR-03.2` (Mở lại hội thoại đã giải quyết):** Khi khách nhắn lại vào một hội thoại Đã giải quyết, hệ thống PHẢI mở lại đúng hội thoại đó nếu còn trong khoảng cho phép mở lại (theo chính sách tự động đóng đã áp dụng cho hội thoại đó), hoặc tạo hội thoại mới nếu đã quá hạn hoặc chính sách quy định luôn tạo mới.

  **Lý do nghiệp vụ:** khách hỏi tiếp ngay sau khi được giải quyết là cùng một việc; tách thành hội thoại mới làm Agent mất ngữ cảnh và thổi phồng khối lượng.

- **`BR-03.3` (Hội thoại đã đóng không mở lại):** Khách nhắn lại vào một hội thoại Đã đóng PHẢI luôn tạo hội thoại mới.

  **Lý do nghiệp vụ:** Đã đóng là chốt sổ; mở lại sẽ làm thay đổi số liệu của kỳ báo cáo đã khép.

- **`BR-03.4` (Chu kỳ giải quyết mới):** Khi một hội thoại được mở lại, một **chu kỳ giải quyết mới** bắt đầu: thông tin giải quyết của chu kỳ trước (người giải quyết, lý do xử lý, ghi chú) KHÔNG ĐƯỢC xóa mà PHẢI giữ nguyên trong lịch sử. Màn hình làm việc chỉ hiển thị chu kỳ đang mở. Hội thoại mở lại vẫn thuộc đơn vị tiếp nhận của nó (`BR-05.12`); nếu Người phụ trách cũ không còn đủ điều kiện nhận việc theo `BR-04.1` (a), (b) — đã Tạm ngưng, Đã rời, hoặc không còn là thành viên đơn vị tiếp nhận — hệ thống bỏ Người phụ trách và phân công lại theo `FEAT-04`.

  **Lý do nghiệp vụ:** lịch sử chu kỳ trước là căn cứ của tỷ lệ mở lại và đánh giá chất lượng; còn giao lại hội thoại mở lại cho người đã nghỉ là để khách chờ một người không bao giờ trả lời.

- **`BR-03.5` (Lý do tạm hoãn):** Hệ thống PHẢI lưu lý do cụ thể ngay trên hội thoại mỗi khi nó chuyển sang Tạm hoãn (Agent tự tạm ẩn, ngoài giờ làm việc, hủy chờ đóng do không có hoạt động mới).

  **Lý do nghiệp vụ:** Giám sát viên phải biết vì sao một hội thoại đang nằm im mà không phải hỏi lại người đã thao tác.

- **`BR-03.6` (Thứ tự thời gian thực):** Mọi tin nhắn trong hội thoại PHẢI hiển thị đúng theo thứ tự thời gian thực tế phát sinh, kể cả khi một tin đến muộn hơn do lỗi mạng.

  **Lý do nghiệp vụ:** tin hiển thị sai thứ tự làm câu trả lời của Agent trông như trả lời nhầm câu hỏi.

- **`BR-03.7` (Khách nhắn vào hội thoại tạm hoãn):** Khi khách nhắn vào một hội thoại đang Tạm hoãn, hội thoại PHẢI tự động trở lại Đang mở, hiện lại trong danh sách việc của người phụ trách, và các cam kết tiếp tục chạy theo `BR-08.7`.

  **Lý do nghiệp vụ:** khách không phải chờ tới khi Agent nhớ ra hội thoại đó.

- **`BR-03.8` (Trạng thái chờ tự định nghĩa):** Doanh nghiệp PHẢI định nghĩa thêm được **trạng thái chờ của riêng mình** (chờ khách bổ sung chứng từ, chờ bộ phận kỹ thuật, chờ duyệt hoàn tiền). Mỗi trạng thái tự định nghĩa PHẢI khai báo nó tương đương trạng thái chuẩn nào (chỉ được tương đương một trạng thái chưa kết thúc), để SLA, tự động đóng, trả về hàng đợi và báo cáo hiểu đúng.

  **Lý do nghiệp vụ:** ép mọi quy trình chờ vào một trạng thái Tạm hoãn khiến báo cáo không nói được **đang chờ ai**; còn một trạng thái nằm ngoài mọi phép đo là chỗ hội thoại biến mất khỏi cam kết.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-03.1.1` | Khách mới nhắn lần đầu | Hội thoại được tạo | Trạng thái Đang mở, có trong hàng đợi của đơn vị tiếp nhận |
| `AC-03.2.1` | Hội thoại Đã giải quyết 2 giờ trước, hạn mở lại 24 giờ | Khách nhắn lại | Cùng hội thoại mở lại, Đang mở |
| `AC-03.2.2` | Hội thoại Đã giải quyết 3 ngày trước, hạn mở lại 24 giờ | Khách nhắn lại | Hội thoại mới được tạo; hội thoại cũ giữ nguyên |
| `AC-03.3.1` | Hội thoại Đã đóng | Khách nhắn lại sau 5 phút | Luôn tạo hội thoại mới |
| `AC-03.4.1` | Hội thoại đã giải quyết với lý do "Đổi hàng", người giải quyết A | Hội thoại mở lại | Lịch sử vẫn tra được A và "Đổi hàng"; màn hình làm việc chỉ hiện chu kỳ mới |
| `AC-03.4.2` | A đã bị tạm ngưng từ hôm qua | Khách nhắn lại vào hội thoại A đã giải quyết, còn trong hạn | Hội thoại mở lại không có Người phụ trách và được phân công trong đơn vị tiếp nhận |
| `AC-03.5.1` | Agent tạm ẩn hội thoại | Giám sát viên mở hội thoại | Thấy lý do "Agent tạm ẩn" kèm người và thời điểm |
| `AC-03.6.1` | Tin của khách gửi lúc 10:00 đến hệ thống muộn sau tin lúc 10:01 | Agent xem khung chat | Tin 10:00 nằm trước tin 10:01 |
| `AC-03.7.1` | Hội thoại Tạm hoãn | Khách nhắn tin | Hội thoại về Đang mở, hiện lại trong danh sách của người phụ trách, đồng hồ cam kết chạy tiếp |
| `AC-03.8.1` | Doanh nghiệp tạo trạng thái "Chờ chứng từ" tương đương Tạm hoãn | Agent chuyển hội thoại sang "Chờ chứng từ" | Đồng hồ cam kết tạm dừng như Tạm hoãn; báo cáo phân bố trạng thái hiện riêng "Chờ chứng từ" |
| `AC-03.8.2` | Quản trị viên tạo trạng thái mới | Khai báo tương đương "Đã đóng" | Lựa chọn bị vô hiệu ngay trên màn hình, nêu trạng thái tự định nghĩa chỉ tương đương trạng thái chưa kết thúc |

**Tham chiếu:** `BR-03.5` → issue [#16](https://github.com/crmsaassaudi/product-management/issues/16). `BR-03.4`, `BR-03.7`, `BR-03.8` → issue [#73](https://github.com/crmsaassaudi/product-management/issues/73).

---

### FEAT-04 — Tự động phân công hội thoại

**Mô tả nghiệp vụ:** Hệ thống tự động chọn Agent phù hợp nhất cho một hội thoại trong số các thành viên đủ điều kiện của đơn vị tiếp nhận, dựa trên năng lực hỗ trợ kênh, kỹ năng, tải công việc hiện tại và quy tắc riêng doanh nghiệp tự định nghĩa.

**Vai trò sử dụng chính:** Hệ thống (thực hiện); Người có quyền quản trị Quản lý Hộp thư & định tuyến (cấu hình quy tắc); Agent (người nhận việc).

**Điều kiện tiên quyết:** Hội thoại chưa có Người phụ trách và thuộc một hàng đợi xác định.

**Luồng chính:**

1. Có hội thoại cần phân công (mới tạo, Bot bàn giao, được trả về hàng đợi, hoặc Người phụ trách không còn đủ điều kiện).
2. Hệ thống đánh giá các quy tắc phân công đã cấu hình theo thứ tự ưu tiên.
3. Xác định nhóm xử lý/Agent đích theo quy tắc khớp đầu tiên, hoặc theo cấu hình mặc định.
4. Lọc những Agent đủ điều kiện (`BR-04.1`), có đủ kỹ năng mà hội thoại đòi hỏi (`FEAT-30`).
5. Chọn một Agent theo chiến lược đã cấu hình, rồi gửi lời mời hoặc gán thẳng (`FEAT-05`).

**Quy tắc nghiệp vụ:**

- **`BR-04.1` (Điều kiện được phân công):** Hệ thống chỉ được chọn một Agent khi đồng thời: (a) người đó Đang hoạt động trong workspace và là thành viên của đơn vị tiếp nhận của hội thoại — Đơn vị chính hoặc kiêm nhiệm ở bất kỳ mức nào, hoặc thuộc đơn vị con khi hàng đợi bật "gồm các đơn vị con" (`BR-05.13`); (b) có (Hội thoại, Sửa) và (Hội thoại, Gán) khác Không có, và không có nguồn chặn nào áp lên hội thoại ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`); (c) đang Sẵn sàng nhận việc (`BR-06.2`); (d) còn khả năng nhận thêm theo `BR-04.7`; (e) được gán vào Hộp thư của hội thoại, nếu hội thoại thuộc một Hộp thư (`BR-17.1`). Quy tắc áp dụng cho **mọi chiến lược phân công**, kể cả chiến lược bổ sung về sau.

  **Lý do nghiệp vụ:** giao hội thoại cho người không trả lời được (không có quyền sửa) hoặc không còn thuộc đội là để khách chờ vô ích; còn giao cho người ngoài đơn vị tiếp nhận là một đường lộ dữ liệu không qua phân quyền.

- **`BR-04.2` (Ưu tiên người phụ trách trước đó):** Khách hàng cũ quay lại PHẢI được ưu tiên gán cho đúng Agent đã phụ trách họ gần đây (trong thời hạn `CFG-04-03`), nếu Agent đó vẫn thỏa `BR-04.1`; nếu không, hệ thống chuyển sang các chiến lược thông thường.

  **Lý do nghiệp vụ:** khách quen gặp lại đúng người thì không phải kể lại từ đầu; nhưng ưu tiên không được thắng điều kiện về quyền và đơn vị.

- **`BR-04.3` (Quy tắc theo điều kiện):** Doanh nghiệp PHẢI cấu hình được quy tắc phân công theo điều kiện tùy chỉnh, có thứ tự ưu tiên rõ ràng. Tập điều kiện tối thiểu: kênh và Thuộc tính kênh, nội dung tin nhắn, **ngôn ngữ khách hàng đang dùng**, nhãn, phân khúc khách hàng, kỹ năng đòi hỏi, Hộp thư, trong/ngoài giờ làm việc. Quy tắc không có điều kiện là quy tắc mặc định. Tập điều kiện này dùng chung với `FEAT-33`. Quy tắc phân công chỉ chọn Agent bên trong đơn vị tiếp nhận của hội thoại; đưa hội thoại sang đơn vị khác là chuyển hàng đợi (`BR-05.15`), không phải một kết quả phân công.

  **Lý do nghiệp vụ:** mỗi doanh nghiệp có cách chia việc riêng; nhưng để quy tắc định tuyến lặng lẽ đổi đơn vị của hội thoại là để một cấu hình định tuyến mở dữ liệu cho cả một đội khác mà không qua người có thẩm quyền.

- **`BR-04.4` (Chiến lược chọn Agent):** Doanh nghiệp PHẢI chọn được cách chọn Agent trong số ứng viên đủ điều kiện (`CFG-04-02`); tối thiểu: vòng lần lượt, người ít việc nhất, theo tải trọng số, và chỉ đẩy vào hàng đợi chờ tự nhận.

  **Lý do nghiệp vụ:** đội nhỏ cần chia đều, đội lớn cần cân tải; một chiến lược cứng không hợp với cả hai.

- **`BR-04.5` (Không ai đủ điều kiện):** Nếu không có Agent nào đủ điều kiện, hội thoại PHẢI vào hàng đợi chờ của đơn vị tiếp nhận (`FEAT-05`).

  **Lý do nghiệp vụ:** hội thoại không được "biến mất" khi đội đang bận hết.

- **`BR-04.6` (Vết phân công):** Mọi quyết định phân công (thành công hay không, vì lý do gì) PHẢI được ghi lại; Agent nhận việc PHẢI xem được ngay trên hội thoại **vì sao việc này tới tay mình** (khớp quy tắc nào, Bot bàn giao, ưu tiên người phụ trách trước đó, tự nhận, được trả về hàng đợi rồi phân công lại).

  **Lý do nghiệp vụ:** không giải thích được vì sao một hội thoại tới tay ai thì không sửa được cấu hình định tuyến sai.

- **`BR-04.7` (Điểm tải theo Thuộc tính kênh):** Năng lực nhận việc của một Agent PHẢI tính theo **điểm tải có trọng số theo Thuộc tính kênh** (`CFG-04-01`): mỗi loại kênh chiếm bao nhiêu điểm, và tổng điểm tối đa của một Agent. Hội thoại đang chờ khách phản hồi CÓ THỂ chiếm ít điểm hơn.

  **Lý do nghiệp vụ:** năm phiên trò chuyện đồng thời là quá tải, năm hội thoại không đồng thời là bình thường — một con số phẳng hoặc bỏ phí năng lực, hoặc làm Agent quá tải mà hệ thống không biết.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-04.1.1` | Đơn vị tiếp nhận "Hỗ trợ – Hà Nội"; B Sẵn sàng nhưng chỉ thuộc "Hỗ trợ – Đà Nẵng" | Hội thoại mới của kênh Hà Nội cần phân công | B không bao giờ được chọn |
| `AC-04.1.2` | C thuộc đơn vị tiếp nhận nhưng vai trò có (Hội thoại, Sửa) = Không có | Hội thoại cần phân công | C không được chọn |
| `AC-04.1.3` | Hàng đợi "Hỗ trợ" bật "gồm các đơn vị con"; D thuộc "Hỗ trợ – Đà Nẵng" là đơn vị con | Hội thoại cần phân công | D là ứng viên hợp lệ |
| `AC-04.1.4` | E thuộc đơn vị tiếp nhận ở mức kiêm nhiệm Chỉ xem, có (Hội thoại, Sửa) và Gán khác Không có | Hội thoại cần phân công | E là ứng viên hợp lệ |
| `AC-04.2.1` | A phụ trách khách 3 ngày trước, thời hạn ưu tiên 7 ngày, A còn năng lực | Khách nhắn lại | Hội thoại gán thẳng cho A |
| `AC-04.2.2` | A đã chuyển sang đơn vị khác, không còn thuộc đơn vị tiếp nhận | Khách nhắn lại | Không ưu tiên A; phân công theo chiến lược thông thường |
| `AC-04.3.1` | Quy tắc "ngôn ngữ = tiếng Anh → nhóm English" | Khách nhắn bằng tiếng Anh | Hội thoại tới nhóm English trong cùng đơn vị tiếp nhận |
| `AC-04.3.2` | Quản trị viên chọn đích của quy tắc phân công là một Agent ngoài đơn vị tiếp nhận | Chọn đích | Lựa chọn bị đánh dấu không hợp lệ ngay trên màn hình, nêu "đích nằm ngoài đơn vị tiếp nhận — dùng chuyển hàng đợi" |
| `AC-04.4.1` | Chiến lược "người ít việc nhất"; A giữ 2 điểm tải, B giữ 5 | Hội thoại cần phân công | Chọn A |
| `AC-04.5.1` | Mọi Agent của đơn vị đều Vắng mặt | Hội thoại mới | Hội thoại vào hàng đợi của đơn vị tiếp nhận |
| `AC-04.6.1` | Hội thoại được gán do khớp quy tắc "VIP" | Agent mở hội thoại | Thấy "Được gán theo quy tắc VIP" |
| `AC-04.7.1` | Live Chat chiếm 3 điểm, email 1 điểm, tối đa 9 điểm | A giữ 3 phiên Live Chat; B giữ 3 hội thoại email | A không nhận thêm; B nhận thêm được |

**Tham chiếu:** `BR-04.7` → issue [#72](https://github.com/crmsaassaudi/product-management/issues/72).

---

### FEAT-05 — Hàng đợi theo đơn vị tiếp nhận & lời mời nhận hội thoại

**Mô tả nghiệp vụ:** Khi một hội thoại chưa có Agent xử lý ngay, hệ thống xếp nó vào hàng đợi của đơn vị tiếp nhận và chủ động mời từng Agent phù hợp lần lượt, thay vì để hội thoại nằm im chờ ai đó tình cờ nhìn thấy. Mục này là phần khai báo hàng đợi của phân hệ theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10`, `BR-35.11`, `BR-35.12`, `BR-35.13`, `BR-35.14`.

**Khai báo hàng đợi của phân hệ:**

| Hạng mục khai báo | Khai báo của Omnichat |
| --- | --- |
| Nguồn | Từng kênh đã kết nối — mỗi Trang Facebook, tài khoản Instagram, số WhatsApp, Zalo OA, tài khoản TikTok, bot Telegram, hộp thư email, widget Live Chat (`BR-01.9`) |
| Đơn vị tiếp nhận | Đơn vị tiếp nhận hội thoại của kênh, mặc định theo loại kênh tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-35-01` (`BR-05.11`) |
| Hàng đợi | Mỗi Hộp thư là một hàng đợi; kênh không thuộc Hộp thư nào là một hàng đợi riêng; mọi hàng đợi thuộc đúng một đơn vị tiếp nhận (`BR-05.11`, `BR-17.4`) |
| Loại bản ghi | Hội thoại và Yêu cầu liên hệ lại là **bản ghi công việc** (`BR-05.12`); hồ sơ khách hàng sinh ra từ hội thoại là **bản ghi chờ phân công** của phân hệ Khách hàng (`BR-02.10`) |
| "Gồm các đơn vị con" | Hỗ trợ theo từng kênh (`BR-05.13`) |
| Nhận việc | Là thao tác Gán (`BR-05.5`) |
| Trả về hàng đợi | Áp cho hội thoại chưa kết thúc khi Người phụ trách tạm ngưng, rời workspace, chuyển đơn vị hoặc tự trả (`BR-05.14`) |
| Chuyển hàng đợi | Được phép tới các hàng đợi trong danh sách đích của hàng đợi nguồn (`BR-05.15`, `CFG-05-08`) |

**Vai trò sử dụng chính:** Hệ thống (điều phối); Agent (nhận/từ chối lời mời, tự nhận); Trưởng nhóm, Giám sát viên (theo dõi, chuyển hàng đợi, trả về hàng đợi); Người có toàn quyền (đơn vị tiếp nhận, "gồm các đơn vị con").

**Điều kiện tiên quyết:** Kênh của hội thoại có đơn vị tiếp nhận hội thoại.

**Luồng chính:**

1. Hội thoại chưa có Người phụ trách vào hàng đợi của đơn vị tiếp nhận.
2. Thành viên đơn vị tiếp nhận thấy hội thoại ở dạng tóm tắt (`BR-05.7`).
3. Theo cách phân phối đã chọn, hệ thống gửi Lời mời nhận hội thoại cho một Agent phù hợp, hoặc gán thẳng, hoặc chờ Agent tự nhận.
4. Agent nhận lời mời hoặc tự nhận → trở thành Người phụ trách và đọc được toàn văn.
5. Hội thoại đến nhầm đội được chuyển sang hàng đợi của đơn vị khác (`BR-05.15`).

**Quy tắc nghiệp vụ:**

- **`BR-05.1` (Thứ tự hàng đợi):** Hội thoại trong hàng đợi PHẢI được sắp theo độ ưu tiên và thời gian chờ; hội thoại chờ càng lâu PHẢI được tự động tăng dần độ ưu tiên.

  **Lý do nghiệp vụ:** không tăng ưu tiên theo thời gian chờ thì hội thoại ưu tiên thấp bị "chìm" vô thời hạn khi hàng đợi luôn có việc mới.

- **`BR-05.2` (Một lời mời tại một thời điểm):** Khi có Agent phù hợp đang rảnh, hệ thống PHẢI gửi Lời mời nhận hội thoại tới đúng một Agent tại một thời điểm; Agent có một khoảng thời gian (`CFG-05-03`) để nhận hoặc từ chối.

  **Lý do nghiệp vụ:** mời nhiều người cùng lúc tạo tranh giành và làm số liệu từ chối mất ý nghĩa.

- **`BR-05.3` (Lời mời bị bỏ lỡ):** Agent bỏ lỡ hoặc từ chối lời mời PHẢI được loại khỏi lượt mời kế tiếp cho đúng hội thoại đó trong cùng vòng mời; hội thoại tiếp tục được mời cho Agent phù hợp khác; sau số lượt không thành công (`CFG-05-04`), hội thoại quay lại chờ ở hàng đợi. Ràng buộc nghiệp vụ thật nằm ở phía khách: **tổng thời gian khách chờ trước khi có người nhận** PHẢI không vượt ngưỡng doanh nghiệp cam kết (`CFG-05-05`); chạm ngưỡng thì áp `BR-05.6`.

  **Lý do nghiệp vụ:** đếm số lượt mời không bảo vệ khách; đồng hồ chờ của khách mới là cam kết.

- **`BR-05.4` (Cách phân phối):** Doanh nghiệp CÓ THỂ chọn một trong ba cách phân phối hội thoại đang chờ (`CFG-05-01`): tự động gán thẳng, gửi lời mời (mặc định), hoặc chỉ hiển thị trong hàng đợi để Agent chủ động nhận.

  **Lý do nghiệp vụ:** đội trực Live Chat cần gán thẳng để giữ tốc độ; đội email chuyên sâu cần tự chọn việc phù hợp chuyên môn.

- **`BR-05.5` (Tự nhận việc là thao tác Gán):** Thành viên của đơn vị tiếp nhận (Đơn vị chính hoặc kiêm nhiệm ở bất kỳ mức nào, hoặc đơn vị con khi bật `BR-05.13`) có (Hội thoại, Xem) khác Không có thấy được các hội thoại chưa có Người phụ trách của hàng đợi, bất kể mức Xem — một nguồn nới phạm vi theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.11`. Người đó **tự nhận** được hội thoại — tự gán mình làm Người phụ trách — khi thỏa đủ điều kiện được phân công tại `BR-04.1` (gồm (Hội thoại, Gán) khác Không có và không bị nguồn chặn). Sau khi nhận, mọi ô mức Chỉ của mình của người đó phủ hội thoại. Trả lời một hội thoại chưa có Người phụ trách cũng là tự nhận (`BR-12.5`). Nhận việc là ngoại lệ tường minh của nguyên tắc "không ai tự cấp quyền cho mình" và không cần phê duyệt.

  **Lý do nghiệp vụ:** mọi mức truy cập tính theo Người phụ trách; không có quy tắc này, hội thoại mới chưa gán không ai thấy và đội không tự nhận việc được. Nhưng nhận việc phải là thao tác Gán có kiểm soát, để vai trò không được giao việc (ví dụ chỉ đọc để kiểm toán) không thể tự kéo hội thoại về mình.

- **`BR-05.6` (Chờ quá lâu):** Khi một hội thoại chờ quá ngưỡng (`CFG-05-05`), hệ thống PHẢI cảnh báo Người phụ trách đơn vị tiếp nhận, và CÓ THỂ tự động chuyển hội thoại sang một nhóm xử lý khác đang rảnh **trong cùng đơn vị tiếp nhận**. Chuyển sang hàng đợi của đơn vị khác chỉ diễn ra theo `BR-05.15` khi Người có toàn quyền đã khai báo hàng đợi dự phòng trong danh sách đích và bật tự chuyển khi quá ngưỡng (`CFG-05-08`, `CFG-05-11`, `BR-25.2`).

  **Lý do nghiệp vụ:** khách chờ quá cam kết cần người can thiệp ngay; nhưng tự động đẩy hội thoại sang đội khác là đổi ai được xem nó, nên chỉ được làm theo đích đã khai báo trước.

- **`BR-05.7` (Thông tin tóm tắt trước khi nhận):** Agent PHẢI có đủ thông tin để chọn việc đúng thứ tự ưu tiên **trước khi** nhận. Với mỗi hội thoại đang chờ, người thấy hàng đợi PHẢI thấy: kênh, khách hàng (hoặc Hồ sơ khách hàng tạm, định danh hiển thị theo mẫu che `BR-23.9`), nhãn, thời gian đã chờ, tình trạng SLA, và trích đoạn ngắn tin nhắn đầu tiên (độ dài `CFG-05-07`, dữ liệu nhạy cảm đã che). Toàn văn chỉ mở cho người có quyền xem đầy đủ nội dung theo `BR-23.9` — gồm người có thao tác đặc thù (Hội thoại, Xem toàn văn khi chưa nhận) bao phủ hội thoại (Mục 5.1). Hai Agent cùng chọn một hội thoại được xử lý ở `BR-13.3`.

  **Lý do nghiệp vụ:** giấu hết thông tin thì Agent chỉ còn cách nhận việc ngẫu nhiên; mở toàn văn cho mọi người thấy hàng đợi thì người chưa nhận việc đọc được nội dung khách mà không ai chịu trách nhiệm trả lời.

- **`BR-05.8` (Ngoài giờ không nhận vào hàng đợi):** Ngoài giờ làm việc, nếu doanh nghiệp cấu hình không nhận hội thoại mới vào hàng đợi (`CFG-05-10`), hệ thống PHẢI xử lý theo kịch bản ngoài giờ (thông báo tự động và Yêu cầu liên hệ lại theo `FEAT-32`).

  **Lý do nghiệp vụ:** hàng đợi không ai trực là một lời hứa trả lời không có người thực hiện.

- **`BR-05.9` (Khách biết mình còn chờ bao lâu):** Khách đang chờ PHẢI được cho biết còn chờ bao lâu — thời gian chờ dự kiến hoặc vị trí trong hàng đợi (`CFG-05-06`). Doanh nghiệp CÓ THỂ tắt hiển thị này, nhưng khi đã tắt PHẢI thay bằng thông điệp xác nhận đã tiếp nhận.

  **Lý do nghiệp vụ:** chờ trong im lặng là nguyên nhân hàng đầu khiến khách bỏ đi, và một khách bỏ đi tốn kém hơn một khách phải chờ có thông báo.

- **`BR-05.10` (Ghi nhận bỏ cuộc):** Khi khách rời đi trước lúc có Agent tiếp nhận, hệ thống PHẢI ghi nhận một lần **bỏ cuộc** kèm thời gian đã chờ (`BR-19.4`).

  **Lý do nghiệp vụ:** lặng lẽ đóng như hội thoại không hoạt động thì doanh nghiệp thiếu người vẫn thấy chỉ số đẹp.

- **`BR-05.11` (Đơn vị tiếp nhận và hàng đợi):** Mỗi hàng đợi hội thoại thuộc đúng một đơn vị tiếp nhận: hàng đợi của một kênh độc lập thuộc đơn vị tiếp nhận hội thoại của kênh đó (`BR-01.9`); Hộp thư chỉ gộp các kênh có cùng đơn vị tiếp nhận hội thoại và thuộc chính đơn vị đó (`BR-17.4`). Một đơn vị CÓ THỂ có nhiều hàng đợi. Hệ thống PHẢI hiển thị cho mỗi hàng đợi đơn vị tiếp nhận của nó và việc có "gồm các đơn vị con" hay không.

  **Lý do nghiệp vụ:** "hàng đợi của đội nào" phải trả lời được bằng một đơn vị duy nhất, vì chính đơn vị đó quyết định ai thấy hội thoại chưa có người nhận.

- **`BR-05.12` (Hội thoại là bản ghi công việc):** Hội thoại là **bản ghi công việc** theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.11`, `BR-35.10` (b): nó thuộc đơn vị tiếp nhận hội thoại của kênh tại thời điểm tạo, **suốt vòng đời** — không đổi khi đổi Người phụ trách, khi chuyển tiếp cho một người có Đơn vị chính ở nơi khác nhưng là thành viên kiêm nhiệm của đơn vị tiếp nhận, khi được mở lại, hay khi Người phụ trách chuyển đơn vị. Hội thoại chỉ đổi đơn vị khi được chuyển hàng đợi (`BR-05.15`) hoặc khi Người có toàn quyền đổi đơn vị tiếp nhận của kênh và chọn "chuyển" ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.12`). Yêu cầu liên hệ lại (`FEAT-32`) là bản ghi công việc thuộc đơn vị của hội thoại gốc.

  **Lý do nghiệp vụ:** đội cần theo dõi được hội thoại sau khi một người đã nhận, Trưởng nhóm cần thấy việc của đội mình kể cả khi một thành viên đội khác được mời hỗ trợ, và báo cáo theo đội không được thay đổi chỉ vì một người chuyển phòng.

- **`BR-05.13` (Hàng đợi gồm các đơn vị con):** Người có toàn quyền bật được "gồm các đơn vị con" cho đơn vị tiếp nhận hội thoại của một kênh ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10`, `BR-35.12`) để một hàng đợi dùng chung cho cả nhánh (ví dụ widget Live Chat chung tiếp nhận vào "Hỗ trợ" và mọi chi nhánh con cùng nhận). Khi bật: hội thoại của kênh tính là thuộc mọi đơn vị trong nhánh; thành viên của mọi đơn vị trong nhánh thấy hàng đợi và đủ điều kiện được phân công theo `BR-04.1`; màn hình bật PHẢI báo trước rằng các đơn vị trong nhánh sẽ thấy hội thoại của nhau theo mức của mình, kèm số người có phạm vi thay đổi. Tắt lại là thao tác thu hẹp (`BR-25.5`).

  **Lý do nghiệp vụ:** doanh nghiệp nhiều chi nhánh thường chỉ có một website và một số hotline; không có lựa chọn này thì hoặc phải dồn mọi chi nhánh vào một đơn vị, hoặc phải nhân bản kênh.

- **`BR-05.14` (Trả về hàng đợi):** "Trả về hàng đợi" bỏ Người phụ trách của một hội thoại **chưa kết thúc** và đưa nó về hàng đợi mà nó đang thuộc, trong **đơn vị tiếp nhận hiện tại của hội thoại** — không về đơn vị của người vừa bị bỏ ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.13`). Áp trong các tình huống:
  - (a) **Người phụ trách bị tạm ngưng** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-42`): việc tạm ngưng luôn thực hiện ngay. Phân hệ khai báo **hội thoại chưa kết thúc trên kênh trò chuyện đồng thời là loại bản ghi cần phản hồi tức thời** theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-42.4`: chúng được Hệ thống trả về hàng đợi ngay trong lượt tạm ngưng (`BR-06.6`). Sau đó, như một thao tác riêng, với hội thoại còn lại lựa chọn được đề xuất sẵn là "trả về hàng đợi", bên cạnh "chuyển tạm cho người xử lý thay" và "giữ nguyên"; người xử lý thay phải thỏa `BR-04.1` (a), (b) và [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.2`, `BR-43.4`;
  - (b) **Người phụ trách rời workspace** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-43`): bước "Bản ghi đang phụ trách" của loại Hội thoại chọn được người nhận hoặc "trả về hàng đợi"; người nhận phải thỏa `BR-04.1` (a), (b) và [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.2`, `BR-43.4`;
  - (c) **Người phụ trách đổi Đơn vị chính**: xử lý hội thoại chưa kết thúc là **một bước bắt buộc** trong danh sách bước của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-11.9`, `BR-11.5`, **không có lựa chọn chọn sẵn**. Lựa chọn: "bàn giao lại" cho người thỏa `BR-04.1` (a), (b) và [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.2`, `BR-43.4`; "trả về hàng đợi"; hoặc "giữ Người phụ trách" — chỉ có khi người đó sau thay đổi vẫn là thành viên đơn vị tiếp nhận của hội thoại; hội thoại không bao giờ đổi đơn vị theo người (`BR-05.12`);
  - (d) **Người phụ trách tự trả** một hội thoại về hàng đợi khi không xử lý được, kèm lý do.

  Khi trả về: hội thoại giữ nguyên lịch sử, nhãn, ghi chú bàn giao và thời gian khách đã chờ; các cam kết SLA tiếp tục chạy, không khởi động lại; hội thoại được phân công lại theo `FEAT-04`. Nếu sau thời hạn `CFG-05-09` kể từ khi người phụ trách bị tạm ngưng hoặc bắt đầu quy trình rời mà hội thoại của họ vẫn chưa được xử lý theo một trong các lựa chọn, Người phụ trách đơn vị tiếp nhận và Người có toàn quyền nhận cảnh báo kèm danh sách và thao tác trả về hàng đợi một chạm. Khi người bị tạm ngưng được kích hoạt lại, hội thoại chưa kết thúc đã chuyển tạm cho người xử lý thay được **đề xuất trả về người cũ** tới người xử lý thay và quản lý trực tiếp ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-42.4`); hội thoại đã trả về hàng đợi không thuộc đề xuất; trả về người cũ là một đề nghị chuyển theo `BR-07.3`.

  **Lý do nghiệp vụ:** việc chưa xong của người rời đi phải quay lại nơi cả đội nhìn thấy, thay vì dồn cho một người nhận hoặc treo dưới tên người không còn đăng nhập; khách không được phải chờ lại từ đầu chỉ vì nội bộ doanh nghiệp thay người.

- **`BR-05.15` (Chuyển hội thoại sang hàng đợi khác):** Người có (Hội thoại, Gán) bao phủ một hội thoại chưa kết thúc chuyển được nó sang một hàng đợi khác theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.14`, — kể cả Người phụ trách chỉ có (Hội thoại, Gán) = Chỉ của mình, vì chuyển hàng đợi là ngoại lệ tường minh của quy tắc đích tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6` — với các quy định của phân hệ:
  - chỉ chuyển được tới hàng đợi nằm trong **danh sách đích được phép** của hàng đợi nguồn (`CFG-05-08`), do Người có toàn quyền khai báo (`BR-25.2`); **mặc định danh sách rỗng** — chưa khai báo thì không chuyển được sang hàng đợi nào khác. Trong Phiên triển khai, khai báo đích cho hàng đợi chưa có hội thoại có hiệu lực ngay, còn với hàng đợi đã có hội thoại chỉ ở dạng soạn sẵn, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-08.7` (như [`tickets-srs.md`](./tickets-srs.md) `BR-34.8`); bỏ một đích là thu hẹp, không bị chặn khi nhật ký lỗi (`BR-25.5`);
  - PHẢI kèm **lý do chuyển** chọn từ danh mục lý do chuyển tiếp (`BR-29.3`);
  - nếu hàng đợi đích thuộc đơn vị tiếp nhận khác, hội thoại thuộc đơn vị mới từ lúc chuyển và mất Người phụ trách nếu người đó không là thành viên đơn vị mới; chuyển giữa hai hàng đợi cùng đơn vị không đổi đơn vị và không đổi Người phụ trách trừ khi người chuyển chọn bỏ;
  - cam kết SLA đang chạy giữ nguyên mốc bắt đầu; nếu hàng đợi đích có Chính sách SLA khác, thời hạn được tính lại theo chính sách đích từ mốc bắt đầu cũ;
  - khách được thông báo theo `BR-07.10`; lượt chuyển ghi vào lịch sử hội thoại (`BR-15.4`) gồm hàng đợi nguồn, hàng đợi đích, người chuyển và lý do.

  **Lý do nghiệp vụ:** khách nhắn nhầm chi nhánh hay cần chuyên môn của đội khác là việc hằng ngày; nhưng mỗi lượt chuyển sang đơn vị khác là đổi cả một đội được xem hội thoại, nên phải đi theo đích đã khai báo và để lại lý do.

- **`BR-05.16` (Đơn vị kiêm nhiệm hết hạn hoặc bị gỡ):** Khi Đơn vị kiêm nhiệm của một người hết hạn hoặc bị gỡ ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-11.10`), với các hội thoại chưa kết thúc người đó đang phụ trách thuộc đơn vị tiếp nhận ấy mà người đó không còn là thành viên: hội thoại trên kênh trò chuyện đồng thời — loại cần phản hồi tức thời theo `BR-05.14` (a) — được Hệ thống trả về hàng đợi ngay theo ngoại lệ của chính quy tắc đó cho loại này, như `BR-06.6`; hội thoại loại khác chỉ được **đề xuất** trả về hàng đợi cho quản lý trực tiếp và Người phụ trách đơn vị ấy, kèm thao tác một chạm.

  **Lý do nghiệp vụ:** khách đang chờ trên khung chat không thể đợi ai duyệt đề xuất; hội thoại không đồng thời thì để người có trách nhiệm quyết định, vì có thể còn người khác trong đơn vị nên nhận.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-05.1.1` | Hai hội thoại cùng ưu tiên, một chờ 10 phút, một chờ 1 phút | Agent mở hàng đợi | Hội thoại chờ 10 phút đứng trước |
| `AC-05.1.2` | Hội thoại ưu tiên thấp chờ quá lâu | Hội thoại ưu tiên thường mới vào | Ưu tiên của hội thoại chờ lâu đã tăng, không bị đẩy xuống cuối |
| `AC-05.2.1` | Ba Agent phù hợp đang rảnh | Hội thoại mới vào hàng đợi | Chỉ một Agent nhận lời mời, có đồng hồ đếm ngược |
| `AC-05.3.1` | A bỏ lỡ lời mời | Hết thời hạn lời mời | Lời mời chuyển cho B; A không được mời lại hội thoại đó trong vòng này |
| `AC-05.3.2` | Khách đã chờ chạm ngưỡng cam kết | Lời mời vẫn đang chạy vòng | Người phụ trách đơn vị tiếp nhận nhận cảnh báo theo `BR-05.6` |
| `AC-05.4.1` | Doanh nghiệp chọn "chỉ hiển thị trong hàng đợi" | Hội thoại mới vào | Không ai nhận lời mời; hội thoại chờ Agent tự nhận |
| `AC-05.5.1` | A thuộc đơn vị tiếp nhận, (Hội thoại, Xem) = Chỉ của mình, Gán = Chỉ của mình | A mở hàng đợi | A thấy các hội thoại chưa có người phụ trách của hàng đợi; tự nhận được; sau khi nhận đọc được toàn văn |
| `AC-05.5.2` | F thuộc đơn vị tiếp nhận với vai trò dựng sẵn Kiểm toán ((Hội thoại, Gán) = Không có) | F mở hội thoại đang chờ | Nút "Nhận" bị vô hiệu, nêu "Vai trò của bạn không có quyền nhận việc" |
| `AC-05.5.3` | G có (Hội thoại, Xem) = Toàn workspace nhưng không thuộc đơn vị tiếp nhận | G mở hội thoại đang chờ của đơn vị đó | G thấy hội thoại theo mức Xem của mình nhưng nút "Nhận" bị vô hiệu vì không thuộc đơn vị tiếp nhận |
| `AC-05.5.4` | Một lượt chặn trên bản ghi áp lên A cho hội thoại X | A tìm X trong hàng đợi | X không hiện với A; A không nhận được X |
| `AC-05.6.1` | Hội thoại chờ vượt ngưỡng; không khai báo hàng đợi dự phòng | Tới ngưỡng | Người phụ trách đơn vị tiếp nhận nhận cảnh báo; hội thoại không rời đơn vị |
| `AC-05.6.2` | Người có toàn quyền khai báo hàng đợi dự phòng của đơn vị khác trong danh sách đích và bật tự chuyển khi quá ngưỡng | Hội thoại chờ vượt ngưỡng | Hội thoại chuyển sang hàng đợi dự phòng; lịch sử ghi "Hệ thống — quá ngưỡng chờ" |
| `AC-05.6.3` | Quản trị viên có quyền Quản lý Hộp thư & định tuyến nhưng không có toàn quyền | Mở cấu hình tự chuyển khi quá ngưỡng hoặc danh sách đích | Lựa chọn bị vô hiệu kèm "cần Người có toàn quyền" |
| `AC-05.7.1` | A (Nhân viên Hỗ trợ mặc định) mở một hội thoại đang chờ | Xem chi tiết | Thấy kênh, khách (định danh che), nhãn, thời gian chờ, SLA và trích đoạn tin đầu; không thấy toàn văn |
| `AC-05.7.2` | Trưởng nhóm có (Hội thoại, Xem toàn văn khi chưa nhận) = Đơn vị và các đơn vị con | Mở cùng hội thoại | Đọc được toàn văn mà không cần nhận |
| `AC-05.7.3` | Tin đầu của khách chứa số thẻ thanh toán | A xem trích đoạn | Số thẻ hiện ở dạng đã che |
| `AC-05.8.1` | Ngoài giờ, cấu hình không nhận vào hàng đợi | Khách nhắn | Khách nhận thông báo ngoài giờ và lời mời để lại Yêu cầu liên hệ lại; không có hội thoại chờ trong hàng đợi |
| `AC-05.9.1` | Hiển thị thời gian chờ dự kiến đang bật | Khách vào hàng đợi | Khách thấy thời gian chờ dự kiến |
| `AC-05.9.2` | Doanh nghiệp tắt hiển thị thời gian chờ | Khách vào hàng đợi | Khách nhận thông điệp "Đã tiếp nhận yêu cầu của bạn" |
| `AC-05.10.1` | Khách đóng widget sau 4 phút chờ, chưa ai nhận | Hệ thống ghi nhận | Một lần bỏ cuộc với thời gian chờ 4 phút, tách khỏi hội thoại không hoạt động |
| `AC-05.11.1` | Quản trị viên mở danh sách hàng đợi | Xem | Mỗi hàng đợi hiện đơn vị tiếp nhận và dấu "gồm các đơn vị con" nếu bật |
| `AC-05.12.1` | Hội thoại thuộc "Hỗ trợ – Hà Nội"; chuyên gia H có Đơn vị chính "Kỹ thuật", kiêm nhiệm "Hỗ trợ – Hà Nội" | A đề nghị chuyển cho H, H chấp nhận | H là Người phụ trách; hội thoại vẫn thuộc "Hỗ trợ – Hà Nội"; Trưởng nhóm Hà Nội vẫn thấy nó |
| `AC-05.12.2` | Người phụ trách A chuyển Đơn vị chính sang "Kinh doanh" nhưng giữ kiêm nhiệm "Hỗ trợ – Hà Nội" | Mở hội thoại A đang phụ trách | Hội thoại vẫn thuộc "Hỗ trợ – Hà Nội"; A vẫn là Người phụ trách |
| `AC-05.13.1` | Người có toàn quyền bật "gồm các đơn vị con" cho widget Live Chat tiếp nhận vào "Hỗ trợ" | Màn hình xác nhận | Hiện trước "các đơn vị Hỗ trợ – Hà Nội, Hỗ trợ – Đà Nẵng sẽ thấy hội thoại của nhau" và số người có phạm vi thay đổi |
| `AC-05.13.2` | Quản trị viên không có toàn quyền | Mở cấu hình đơn vị tiếp nhận của kênh | Lựa chọn "gồm các đơn vị con" bị vô hiệu, nêu cần Người có toàn quyền |
| `AC-05.14.1` | A đang phụ trách 5 hội thoại email chưa kết thúc; A bị tạm ngưng | Người thực hiện xác nhận lựa chọn đề xuất "trả về hàng đợi" | 5 hội thoại không có Người phụ trách, về hàng đợi của đơn vị tiếp nhận của từng hội thoại; thời gian chờ và SLA chạy tiếp |
| `AC-05.14.2` | A thuộc "Hỗ trợ – Hà Nội" nhưng phụ trách một hội thoại thuộc "Hỗ trợ" (đơn vị cha) | A bị tạm ngưng, chọn trả về hàng đợi | Hội thoại về hàng đợi của "Hỗ trợ", không về "Hỗ trợ – Hà Nội" |
| `AC-05.14.3` | A bị tạm ngưng | Người thực hiện mở lựa chọn cho hội thoại Live Chat của A | "Giữ nguyên" không khả dụng cho hội thoại trên kênh đồng thời, nêu lý do |
| `AC-05.14.4` | A bắt đầu quy trình rời workspace với 3 hội thoại | Chọn người nhận B không thuộc đơn vị tiếp nhận | Từ chối B, nêu B không thuộc đơn vị tiếp nhận của hội thoại |
| `AC-05.14.5` | A bị tạm ngưng, không ai xử lý 4 hội thoại của A, thời hạn cảnh báo 15 phút | Hết 15 phút | Người phụ trách đơn vị tiếp nhận và Người có toàn quyền nhận cảnh báo kèm danh sách và nút trả về hàng đợi |
| `AC-05.14.6` | A muốn tự trả một hội thoại về hàng đợi | Mở thao tác "Trả về hàng đợi" | Nút xác nhận chỉ bật khi đã chọn lý do |
| `AC-05.14.7` | A đổi Đơn vị chính khỏi "Hỗ trợ – Hà Nội", không còn kiêm nhiệm, đang giữ 3 hội thoại Hà Nội | Mở danh sách bước đổi đơn vị | Có bước bắt buộc "Hội thoại đang phụ trách: 3" chưa chọn sẵn lựa chọn nào; không có "giữ Người phụ trách"; thay đổi không lưu được tới khi chọn "bàn giao lại" hoặc "trả về hàng đợi" |
| `AC-05.14.8` | A đổi Đơn vị chính nhưng vẫn kiêm nhiệm "Hỗ trợ – Hà Nội" | Mở bước xử lý hội thoại | Có thêm lựa chọn "giữ Người phụ trách"; vẫn không chọn sẵn |
| `AC-05.14.9` | A bị tạm ngưng; người thực hiện chọn người xử lý thay K có (Hội thoại, Gán) không bao phủ các hội thoại đó, K chính là người thực hiện | Xác nhận | Từ chối theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.4`, nêu ô vượt trần |
| `AC-05.14.10` | A bị tạm ngưng; 3 hội thoại email chuyển tạm cho K, 2 hội thoại trả về hàng đợi; A được kích hoạt lại | Kích hoạt lại | K và quản lý trực tiếp nhận đề xuất trả các hội thoại còn chưa kết thúc trong 3 hội thoại đó về A; 2 hội thoại đã về hàng đợi không có trong đề xuất |
| `AC-05.15.1` | Hàng đợi Hà Nội có danh sách đích gồm hàng đợi Đà Nẵng; Trưởng nhóm Hà Nội có (Hội thoại, Gán) = Đơn vị và các đơn vị con | Chuyển một hội thoại sang Đà Nẵng kèm lý do "Khách ở Đà Nẵng" | Hội thoại thuộc "Hỗ trợ – Đà Nẵng"; Người phụ trách cũ (chỉ thuộc Hà Nội) bị bỏ; Agent Hà Nội không còn thấy hội thoại theo mức Đơn vị của mình; lịch sử ghi nguồn, đích, người chuyển, lý do |
| `AC-05.15.2` | Hàng đợi đích không nằm trong danh sách đích được phép | Mở danh sách đích chuyển | Hàng đợi đó không có trong danh sách |
| `AC-05.15.3` | A có (Hội thoại, Gán) = Chỉ của mình, đang phụ trách hội thoại | Chuyển hội thoại sang hàng đợi được phép | Chuyển thành công |
| `AC-05.15.4` | A có (Hội thoại, Gán) = Chỉ của mình, hội thoại do B phụ trách | Tìm thao tác chuyển hàng đợi | Thao tác không khả dụng |
| `AC-05.15.5` | Hội thoại còn 20 phút tới hạn phản hồi lần đầu theo chính sách nguồn; hàng đợi đích có cam kết phản hồi lần đầu khác | Chuyển sang hàng đợi đích | Hạn được tính lại theo chính sách đích từ mốc bắt đầu cũ; không khởi động lại từ đầu |
| `AC-05.15.6` | Workspace mới, chưa ai khai báo danh sách đích | Trưởng nhóm mở thao tác chuyển hàng đợi | Danh sách đích trống, nêu "Người có toàn quyền chưa khai báo hàng đợi được chuyển tới" |
| `AC-05.16.1` | Kiêm nhiệm "Hỗ trợ – Đà Nẵng" của A hết hạn; A giữ 1 phiên Live Chat và 2 hội thoại email của Đà Nẵng | Tới ngày hết hạn | Phiên Live Chat về hàng đợi Đà Nẵng ngay; 2 hội thoại email vẫn do A phụ trách; quản lý trực tiếp và Người phụ trách Đà Nẵng nhận đề xuất trả về hàng đợi |

**Tham chiếu:** `BR-05.7` → issue [#63](https://github.com/crmsaassaudi/product-management/issues/63). `BR-05.9`, `BR-05.10` → issue [#76](https://github.com/crmsaassaudi/product-management/issues/76).

---

### FEAT-06 — Trạng thái làm việc của Agent

**Mô tả nghiệp vụ:** Theo dõi Agent nào đang thực sự sẵn sàng nhận việc, để hệ thống chỉ phân công cho người xử lý được ngay, và cung cấp số liệu cho báo cáo thời gian làm việc.

**Vai trò sử dụng chính:** Agent (tự đặt trạng thái); Hệ thống (đặt Ngoại tuyến, Xử lý sau hội thoại).

**Điều kiện tiên quyết:** Thành viên Đang hoạt động trong workspace.

**Luồng chính:**

1. Agent chọn trạng thái làm việc.
2. Hệ thống dùng trạng thái đó làm điều kiện phân công (`BR-06.2`) và ghi lại mọi lần đổi (`BR-06.4`).

**Quy tắc nghiệp vụ:**

- **`BR-06.1` (Danh mục trạng thái):** Agent PHẢI tự chọn trạng thái làm việc (Sẵn sàng, Vắng mặt, Nghỉ giải lao, Đang họp, Đang đào tạo); Ngoại tuyến chỉ do hệ thống đặt khi Agent mất kết nối hoặc phiên chấm dứt; **Xử lý sau hội thoại** do hệ thống đặt theo `BR-06.5`. Doanh nghiệp PHẢI bổ sung được trạng thái riêng (`CFG-06-03`) và khai báo mỗi trạng thái có nhận việc mới không, có tính là thời gian làm việc không.

  **Lý do nghiệp vụ:** báo cáo thời gian làm việc chỉ đúng khi trạng thái phản ánh đúng việc Agent đang làm.

- **`BR-06.2` (Điều kiện nhận việc mới):** Agent chỉ nhận được hội thoại mới khi đồng thời: đang chọn Sẵn sàng, đang thực sự kết nối, và chưa đạt giới hạn năng lực (`BR-04.7`).

  **Lý do nghiệp vụ:** giao việc cho người đang họp là để khách chờ.

- **`BR-06.3` (Kết luận Ngoại tuyến):** Hệ thống chỉ được đặt một Agent sang Ngoại tuyến khi đã đủ căn cứ người đó thực sự dừng làm việc; khoảng chờ trước khi kết luận do doanh nghiệp cấu hình (`CFG-06-01`). Trong khoảng chờ, hội thoại đang xử lý PHẢI giữ nguyên người phụ trách, và Agent quay lại trong khoảng chờ PHẢI tiếp tục đúng công việc đang dở. Phiên bị chấm dứt vì tạm ngưng hoặc mất quyền ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-42.1`) là Ngoại tuyến ngay, không có khoảng chờ.

  **Lý do nghiệp vụ:** một lần rớt mạng vài giây không được làm khách bị chuyển sang người khác giữa chừng; nhưng người đã bị cắt quyền thì không còn quay lại.

- **`BR-06.4` (Ghi lại mọi lần đổi):** Mọi lần đổi trạng thái làm việc PHẢI được ghi lại, phục vụ báo cáo thời gian làm việc (`FEAT-20`) và tuân thủ ca (`FEAT-28`).

  **Lý do nghiệp vụ:** không có vết thì không đối chiếu được ca trực với thực tế.

- **`BR-06.5` (Xử lý sau hội thoại):** Khi Agent kết thúc một hội thoại, doanh nghiệp PHẢI cấu hình được một khoảng **Xử lý sau hội thoại** (`CFG-06-02`) để Agent hoàn tất chọn lý do xử lý, ghi chú, cập nhật hồ sơ trước khi nhận việc mới. Trong khoảng đó Agent KHÔNG ĐƯỢC nhận việc mới; thời gian đó PHẢI tính vào AHT; Agent PHẢI kết thúc sớm được khi đã xong.

  **Lý do nghiệp vụ:** việc mới ập tới ngay khi vừa đóng hội thoại cũ thì Agent bỏ qua phần ghi nhận và mọi chỉ số phân tích nguyên nhân liên hệ mất giá trị.

- **`BR-06.6` (Hội thoại đồng thời của Agent Ngoại tuyến):** Khi một Agent chuyển sang Ngoại tuyến (hết khoảng chờ `BR-06.3`, hoặc phiên chấm dứt do tạm ngưng/mất quyền), các hội thoại chưa kết thúc của Agent đó trên kênh **trò chuyện đồng thời** — loại cần phản hồi tức thời theo `BR-05.14` (a) — PHẢI được hệ thống trả về hàng đợi của đơn vị tiếp nhận của chúng theo `BR-05.14` và phân công lại; hội thoại trên kênh không đồng thời giữ nguyên Người phụ trách cho tới khi có quyết định theo `BR-05.14`. Ghi chú bàn giao và lý do "Người phụ trách ngoại tuyến" hiển thị cho người nhận mới.

  **Lý do nghiệp vụ:** khách trên khung chat đang chờ ngay trên màn hình; để họ treo dưới tên một người đã ngoại tuyến là bỏ rơi khách. Hội thoại không đồng thời thì chờ được người có thẩm quyền quyết định giao cho ai.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-06.1.1` | Doanh nghiệp tạo trạng thái "Hỗ trợ nội bộ" — không nhận việc, có tính thời gian làm việc | Agent chọn trạng thái đó | Không nhận việc mới; thời gian vẫn tính vào thời gian làm việc |
| `AC-06.1.2` | Agent mở danh sách trạng thái | Tìm "Ngoại tuyến" | Không tự chọn được Ngoại tuyến |
| `AC-06.2.1` | Agent đặt Vắng mặt | Hội thoại mới cần phân công | Agent không nhận lời mời |
| `AC-06.3.1` | Khoảng chờ 2 phút | Agent rớt mạng 40 giây rồi quay lại | Vẫn giữ mọi hội thoại đang xử lý, nội dung đang dở còn nguyên |
| `AC-06.3.2` | Agent đang trực bị tạm ngưng | Tạm ngưng có hiệu lực | Agent Ngoại tuyến ngay, không chờ khoảng chờ |
| `AC-06.4.1` | Agent đổi trạng thái 6 lần trong ca | Giám sát viên xem báo cáo thời gian làm việc | Thấy đủ 6 lần đổi với thời điểm |
| `AC-06.5.1` | Xử lý sau hội thoại 60 giây | Agent đóng hội thoại | Không nhận việc mới trong 60 giây; thời gian đó có trong AHT |
| `AC-06.5.2` | Agent xong ghi nhận sau 20 giây | Bấm "Sẵn sàng" | Kết thúc sớm, nhận việc mới được ngay |
| `AC-06.6.1` | A giữ 2 phiên Live Chat và 3 hội thoại email, hết khoảng chờ Ngoại tuyến | A vẫn không quay lại | 2 phiên Live Chat về hàng đợi và được phân công lại; 3 hội thoại email vẫn do A phụ trách |
| `AC-06.6.2` | Tình huống AC-06.6.1 | Agent mới nhận phiên Live Chat | Thấy lý do "Người phụ trách ngoại tuyến" và ghi chú bàn giao (nếu có) |

**Tham chiếu:** `BR-06.5` → issue [#72](https://github.com/crmsaassaudi/product-management/issues/72).

---

### FEAT-07 — Chuyển tiếp hội thoại

**Mô tả nghiệp vụ:** Cho phép chuyển một hội thoại đang xử lý sang một Agent khác hoặc một nhóm xử lý khác, khi cần chuyên môn khác, đổi ca làm việc, hoặc muốn xin ý kiến đồng nghiệp mà không rời khỏi hội thoại. Chuyển sang hàng đợi của một đơn vị tiếp nhận khác đi theo `BR-05.15`.

**Vai trò sử dụng chính:** Người phụ trách hội thoại; người có (Hội thoại, Gán) bao phủ hội thoại (Trưởng nhóm, Giám sát viên); Agent đích.

**Điều kiện tiên quyết:** Hội thoại chưa kết thúc.

**Luồng chính:**

1. Người khởi tạo chọn "Chuyển tiếp", chọn Agent cụ thể hoặc một nhóm xử lý, chọn kiểu: **Chuyển hẳn** (không cần bên kia xác nhận), **Chuyển có xác nhận** (bên nhận phải đồng ý mới đổi Người phụ trách), hoặc **Xin ý kiến** (mời đồng nghiệp cùng xem/tư vấn, người chuyển vẫn giữ hội thoại).
2. Với Chuyển có xác nhận/Xin ý kiến: Agent đích nhận yêu cầu, có thời hạn để đồng ý hoặc từ chối.
3. Nếu đồng ý, quyền phụ trách (hoặc quyền tư vấn tạm thời) chuyển sang Agent đích.

**Quy tắc nghiệp vụ:**

- **`BR-07.1` (Kiểu chuyển tiếp):** Tối thiểu PHẢI có ba kiểu: Chuyển hẳn, Chuyển có xác nhận, Xin ý kiến; mô hình chuyển tiếp PHẢI mở rộng thêm kiểu mới được mà không phá vỡ các quy tắc còn lại.

  **Lý do nghiệp vụ:** bàn giao hẳn, bàn giao cần người nhận đồng ý và hỏi ý kiến là ba nhu cầu khác nhau về trách nhiệm.

- **`BR-07.2` (Đích chuyển tiếp):** Yêu cầu chuyển tiếp PHẢI chỉ định rõ Agent đích hoặc nhóm xử lý đích; chuyển cho một nhóm PHẢI luôn là Chuyển hẳn. Đích là một Agent: người đó PHẢI thỏa `BR-04.1` (a), (b) — Đang hoạt động, là thành viên đơn vị tiếp nhận của hội thoại (hoặc của nhánh khi bật `BR-05.13`), có (Hội thoại, Sửa) và (Hội thoại, Gán) khác Không có, đã Xem được hội thoại theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6` (gồm nguồn nới của hàng đợi) và không bị nguồn chặn nào; với Chuyển có xác nhận, các điều kiện này được xét lại tại thời điểm người nhận chấp nhận. Người ngoài đơn vị tiếp nhận chỉ tham gia được qua Xin ý kiến (`BR-07.6`). Đích là nhóm xử lý trong cùng đơn vị tiếp nhận: hội thoại về hàng đợi của nhóm đó và được phân công lại. Đích là nhóm xử lý hoặc Hộp thư thuộc **đơn vị tiếp nhận khác**: thao tác là chuyển hàng đợi và chịu toàn bộ `BR-05.15`.

  **Lý do nghiệp vụ:** Người phụ trách hội thoại phải là người cả đội tiếp nhận nhìn thấy và chịu trách nhiệm cùng, nhất quán với điều kiện người nhận bàn giao vé tại [`tickets-srs.md`](./tickets-srs.md) `BR-12.6`; chuyên gia đội khác giúp được qua Xin ý kiến mà hội thoại không rời khỏi đội và không mở quyền phụ trách cho người ngoài đội.

- **`BR-07.3` (Ai được khởi tạo và cách có hiệu lực):** Theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`:
  - **Người phụ trách hiện tại**, kể cả khi (Hội thoại, Gán) chỉ là Chỉ của mình, luôn có: (a) trả về hàng đợi mà hội thoại đang thuộc (`BR-05.14` (d)), hoặc Chuyển hẳn cho một nhóm xử lý trong cùng đơn vị tiếp nhận — cùng là đưa hội thoại về hàng đợi của đơn vị; (b) **đề nghị chuyển** cho một người cụ thể — kiểu Chuyển có xác nhận — chỉ có hiệu lực khi người nhận chấp nhận và tại lúc chấp nhận người nhận tự đạt điều kiện của `BR-07.2`; đề nghị hết hạn theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-25-01` hoặc khi Người phụ trách đổi; (c) chuyển hàng đợi theo `BR-05.15`.
  - **Xin ý kiến**: Người phụ trách hiện tại hoặc người có (Hội thoại, Sửa) bao phủ hội thoại mở được, theo `BR-07.6`; Xin ý kiến không đổi Người phụ trách.
  - **Chuyển hẳn cho một Agent cụ thể** (không cần người nhận chấp nhận) và gán lại hội thoại của người khác cần (Hội thoại, Gán) từ Đơn vị của mình trở lên bao phủ hội thoại, và người nhận phải nằm trong mức Gán đó của người thực hiện sau khi đổi.
  - Tự gán cho mình một hội thoại đang có Người phụ trách khác chỉ được khi mức của chính mình trên mọi ô bị ảnh hưởng (Sửa, Xuất, Gán, Can thiệp) đã bao phủ hội thoại trước khi gán, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.4`; nhận hội thoại chưa có người phụ trách là `BR-05.5`.

  **Lý do nghiệp vụ:** người phụ trách lúc nào cũng phải có đường trả lại hoặc chuyển việc mình không xử lý được; nhưng giao thẳng việc cho người khác là quyết định thay họ, nên chỉ người có quyền gán trên phạm vi đơn vị được làm, còn đề nghị thì phải để người nhận tự chấp nhận bằng điều kiện của chính họ.

- **`BR-07.4` (Thời hạn phản hồi):** Đề nghị chuyển (Chuyển có xác nhận) hết hạn theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-25-01` hoặc khi Người phụ trách đổi. Hội thoại trên kênh trò chuyện đồng thời là loại cần phản hồi tức thời, nên phân hệ khai báo mặc định riêng của tham số đó cho loại này (`CFG-07-04`, mặc định 5 phút — mức thấp nhất trong miền giá trị của tài liệu IAM). Lời mời Xin ý kiến hết hạn theo `CFG-07-01`. Hết hạn mà chưa phản hồi thì yêu cầu tự hủy và người khởi tạo được báo.

  **Lý do nghiệp vụ:** yêu cầu treo vô thời hạn làm người chuyển tưởng đã có người nhận; khách đang chờ trên khung chat không thể chờ một đề nghị ba ngày.

- **`BR-07.5` (Đích phải còn năng lực):** Agent đích chỉ nhận được chuyển tiếp nếu còn khả năng nhận thêm việc; nếu không đủ khả năng tại thời điểm xác nhận, hệ thống PHẢI từ chối và báo cả hai bên.

  **Lý do nghiệp vụ:** chuyển tiếp không được là đường vòng vượt giới hạn tải của `BR-04.7`.

- **`BR-07.6` (Xin ý kiến):** Người phụ trách hiện tại, hoặc người có (Hội thoại, Sửa) bao phủ hội thoại, mở được một lượt Xin ý kiến. Người được mời PHẢI Đang hoạt động, có (Hội thoại, Xem) khác Không có và không bị nguồn chặn nào trên hội thoại ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`); người được mời có thể ở ngoài đơn vị tiếp nhận. Quyền đọc toàn văn của người được mời là một **lượt cấp trên bản ghi do phân hệ sinh ra**, mức trần Chỉ đọc, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.5`: chỉ cho đọc và ghi ghi chú nội bộ, không trả lời khách, không đổi trạng thái, và **tự thu hồi ngay khi lượt Xin ý kiến kết thúc**. Lượt Xin ý kiến tự kết thúc khi: người mở kết thúc; lời mời hết hạn chưa được nhận (`BR-07.4`); quá thời lượng tối đa của một lượt (`CFG-07-05`); hội thoại kết thúc; Người phụ trách đổi; hội thoại được trả về hàng đợi; hoặc hội thoại chuyển sang đơn vị tiếp nhận khác. Trong suốt lượt, Người phụ trách không đổi. Xin ý kiến **không** có lựa chọn chuyển quyền phụ trách cho người tư vấn; muốn giao hội thoại thì dùng đề nghị chuyển theo `BR-07.3` (b), người nhận phải đạt `BR-07.2` ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`).

  **Lý do nghiệp vụ:** người tư vấn cần đọc để tư vấn, nhưng quyền đọc đó là ngoại lệ có thời hạn, không được thành đường vòng để giao hội thoại cho người ngoài đội hay để người không chịu trách nhiệm trả lời khách.

- **`BR-07.7` (Một yêu cầu treo):** Một hội thoại chỉ có tối đa một yêu cầu chuyển tiếp đang chờ tại một thời điểm. Lời mời Xin ý kiến chưa được nhận tính là yêu cầu đang chờ; lượt Xin ý kiến đã có người tham gia thì không, nên Người phụ trách vẫn đề nghị chuyển được trong lúc đang có người tư vấn (`AC-07.6.5`).

  **Lý do nghiệp vụ:** hai yêu cầu song song có thể cùng được chấp nhận, tạo hai người phụ trách.

- **`BR-07.8` (Tự hủy khi hội thoại kết thúc):** Nếu hội thoại được giải quyết/đóng trong lúc có yêu cầu chuyển tiếp treo, yêu cầu đó PHẢI tự hủy.

  **Lý do nghiệp vụ:** nhận một hội thoại đã kết thúc là nhận việc không còn tồn tại.

- **`BR-07.9` (Lý do chuyển tiếp):** Mọi yêu cầu chuyển tiếp PHẢI kèm **lý do chuyển tiếp** chọn từ danh mục (`BR-29.3`); doanh nghiệp CÓ THỂ đặt lý do là bắt buộc (`CFG-07-02`).

  **Lý do nghiệp vụ:** tỷ lệ và nguyên nhân chuyển tiếp là chỉ số phát hiện định tuyến sai và thiếu kỹ năng; người nhận cũng cần biết mình được giao vì sao.

- **`BR-07.10` (Thông báo cho khách):** Khi hội thoại đổi Người phụ trách hoặc đổi hàng đợi, khách PHẢI được thông báo rõ là đang được chuyển sang ai hoặc bộ phận nào, trừ khi doanh nghiệp cấu hình khác (`CFG-07-03`).

  **Lý do nghiệp vụ:** đột nhiên đổi giọng văn và cách xưng hô mà không giải thích khiến khách phải kể lại từ đầu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-07.1.1` | Trưởng nhóm có (Hội thoại, Gán) = Đơn vị và các đơn vị con mở một hội thoại của đội | Mở menu chuyển tiếp | Thấy đủ ba kiểu Chuyển hẳn, Chuyển có xác nhận, Xin ý kiến |
| `AC-07.2.1` | A chuyển hẳn cho nhóm xử lý "Thanh toán" trong cùng đơn vị tiếp nhận | Xác nhận | Hội thoại vào hàng đợi/được gán theo quy tắc của nhóm đó, không cần ai xác nhận; đơn vị không đổi |
| `AC-07.2.2` | A chọn kiểu Chuyển có xác nhận với đích là một nhóm | Chọn kiểu | Kiểu đó không chọn được khi đích là nhóm |
| `AC-07.2.3` | A chọn đích là Hộp thư thuộc đơn vị tiếp nhận khác, không nằm trong danh sách đích được phép | Tìm đích | Hộp thư đó không có trong danh sách |
| `AC-07.2.4` | Đích là Agent K có (Hội thoại, Sửa) = Không có | Chọn K | Từ chối, nêu K không có quyền xử lý hội thoại |
| `AC-07.3.1` | B không phụ trách hội thoại và có (Hội thoại, Gán) = Chỉ của mình | Tìm nút chuyển tiếp | Không khả dụng |
| `AC-07.3.2` | A là Người phụ trách, (Hội thoại, Gán) = Chỉ của mình | Mở menu chuyển tiếp | Có "Trả về hàng đợi", "Chuyển cho nhóm xử lý của đơn vị", "Đề nghị chuyển cho một người" và "Chuyển hàng đợi"; không có "Chuyển hẳn cho một Agent" |
| `AC-07.3.3` | A đề nghị chuyển cho B; trước khi B chấp nhận, B bị gỡ khỏi đơn vị tiếp nhận | B bấm chấp nhận | Từ chối, nêu B không còn đạt điều kiện nhận việc; hội thoại vẫn do A phụ trách |
| `AC-07.3.4` | Trưởng nhóm có (Hội thoại, Gán) = Đơn vị của mình | Chuyển hẳn hội thoại của A cho C cùng đơn vị | Có hiệu lực ngay, không cần C chấp nhận |
| `AC-07.3.5` | Giám sát viên có (Hội thoại, Gán) = Đơn vị và các đơn vị con nhưng (Hội thoại, Can thiệp) = Không có; A là Người phụ trách | Giám sát viên tự gán hội thoại của A cho mình | Từ chối theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.4`, nêu ô Can thiệp vượt trần |
| `AC-07.4.1` | [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-25-01` = 1 giờ | B không phản hồi đề nghị chuyển trong 1 giờ | Đề nghị tự hủy; A nhận thông báo; hội thoại vẫn do A phụ trách |
| `AC-07.4.2` | Thời hạn Xin ý kiến 2 phút | Đồng nghiệp không phản hồi | Lời mời tự hủy; A nhận thông báo |
| `AC-07.4.3` | Mặc định, hội thoại Live Chat | B không chấp nhận đề nghị chuyển trong 5 phút | Đề nghị tự hủy; A nhận thông báo |
| `AC-07.5.1` | Agent đích đã đạt giới hạn tải | Đồng ý chuyển có xác nhận | Từ chối; cả hai bên nhận thông báo |
| `AC-07.6.1` | A (Người phụ trách) mời chuyên gia B thuộc "Kỹ thuật", ngoài đơn vị tiếp nhận, có (Hội thoại, Xem) = Chỉ của mình | B tham gia | B đọc được toàn văn, chỉ ghi được ghi chú nội bộ, không gửi được tin cho khách; A vẫn là Người phụ trách |
| `AC-07.6.2` | Tiếp nối AC-07.6.1 | A kết thúc Xin ý kiến | Lượt cấp của B bị thu hồi ngay; B mở lại hội thoại không còn đọc được toàn văn |
| `AC-07.6.3` | C có (Hội thoại, Xem) = Không có | A mời C xin ý kiến | Từ chối, nêu C không đạt điều kiện người được mời |
| `AC-07.6.4` | Có lượt chặn trên bản ghi áp lên D cho hội thoại này | A mời D | Từ chối, không nêu nội dung lượt chặn |
| `AC-07.6.5` | Lượt Xin ý kiến đang mở | A tìm thao tác "chuyển quyền phụ trách cho người tư vấn" | Không có; chỉ có "Kết thúc" và thao tác đề nghị chuyển thông thường |
| `AC-07.6.6` | E không phụ trách hội thoại, (Hội thoại, Sửa) = Chỉ của mình | Tìm thao tác Xin ý kiến | Không khả dụng |
| `AC-07.6.8` | B đang tham gia Xin ý kiến | A trả hội thoại về hàng đợi | Lượt Xin ý kiến tự kết thúc; B mất quyền đọc toàn văn ngay |
| `AC-07.6.9` | B đang tham gia Xin ý kiến | Hội thoại được chuyển sang hàng đợi của đơn vị khác, hoặc A chuyển cho C | Lượt Xin ý kiến tự kết thúc; B mất quyền đọc toàn văn ngay |
| `AC-07.6.10` | Thời lượng tối đa 60 phút | Lượt Xin ý kiến kéo dài 60 phút | Lượt tự kết thúc; A và B được báo |
| `AC-07.6.7` | Trưởng nhóm có (Hội thoại, Sửa) bao phủ hội thoại của A | Mở Xin ý kiến cho hội thoại đó | Mở được; A vẫn là Người phụ trách |
| `AC-07.7.2` | B đang tham gia Xin ý kiến | A đề nghị chuyển cho C | Đề nghị được tạo; không bị chặn vì "đã có yêu cầu treo" |
| `AC-07.7.1` | Hội thoại đang có một yêu cầu chuyển tiếp treo | Tạo yêu cầu thứ hai | Từ chối |
| `AC-07.8.1` | Yêu cầu chuyển tiếp đang treo | A giải quyết hội thoại | Yêu cầu tự hủy |
| `AC-07.9.1` | Lý do chuyển tiếp bắt buộc | Gửi yêu cầu không chọn lý do | Nút gửi bị vô hiệu tới khi chọn lý do |
| `AC-07.10.1` | Thông báo cho khách đang bật | Hội thoại chuyển sang bộ phận Thanh toán | Khách nhận "Bạn đang được chuyển tới bộ phận Thanh toán" |

**Tham chiếu:** `BR-07.9`, `BR-07.10` → issue [#74](https://github.com/crmsaassaudi/product-management/issues/74).

---

### FEAT-08 — Cam kết thời gian phản hồi (SLA)

**Mô tả nghiệp vụ:** Đo và cảnh báo thời gian doanh nghiệp phản hồi/giải quyết hội thoại theo cam kết nội bộ doanh nghiệp tự đặt ra, để đảm bảo tốc độ phục vụ nhất quán.

**Vai trò sử dụng chính:** Hệ thống (đo); Người có quyền quản trị Quản lý cam kết phục vụ (cấu hình); Agent, Trưởng nhóm, Giám sát viên (theo dõi).

**Điều kiện tiên quyết:** Có ít nhất một Chính sách SLA (`CFG-08-01`) hoặc dùng đo tốc độ không cam kết (`BR-08.6`).

**Luồng chính:**

1. Quản trị viên định nghĩa Chính sách SLA: loại cam kết (phản hồi lần đầu, phản hồi các lần sau, giải quyết xong, im lặng tối đa giữa chừng), thời hạn theo phân khúc khách hàng, phạm vi áp dụng.
2. Hội thoại mới tự động được gắn cam kết phản hồi lần đầu và cam kết giải quyết xong.
3. Khi khách nhắn, đồng hồ phản hồi bắt đầu/khởi động lại; khi Agent trả lời, đồng hồ dừng và ghi nhận đáp ứng hay vi phạm.
4. Khi hội thoại Tạm hoãn, mọi đồng hồ tạm dừng; khi trở lại Đang mở, đồng hồ tiếp tục từ thời gian còn lại.

**Quy tắc nghiệp vụ:**

- **`BR-08.1` (Chính sách theo phân khúc):** Quản trị viên PHẢI định nghĩa được Chính sách SLA riêng theo từng phân khúc khách hàng, cho từng loại cam kết (`CFG-08-01`).

  **Lý do nghiệp vụ:** khách VIP được hứa phản hồi nhanh hơn khách thường; một chính sách chung không đo được lời hứa đó.

- **`BR-08.2` (Chính sách theo Hộp thư):** Một Hộp thư CÓ THỂ áp dụng một Chính sách SLA riêng khác mặc định của doanh nghiệp.

  **Lý do nghiệp vụ:** đội khiếu nại và đội tư vấn bán hàng có cam kết khác nhau.

- **`BR-08.3` (Cam kết luôn chạy):** Hội thoại mới tạo hoặc vừa mở lại PHẢI đồng thời có cam kết phản hồi lần đầu và cam kết giải quyết xong đang chạy.

  **Lý do nghiệp vụ:** hội thoại không có cam kết là hội thoại không ai bị cảnh báo khi bỏ quên.

- **`BR-08.4` (Tính theo giờ làm việc):** Thời hạn cam kết PHẢI tính theo giờ làm việc áp dụng cho hội thoại (`BR-24.3`) — thời gian ngoài giờ không tính vào thời hạn.

  **Lý do nghiệp vụ:** tính cả đêm và cuối tuần sinh ra vi phạm ảo, làm hỏng đánh giá đội ngũ.

- **`BR-08.5` (Đúng loại đồng hồ):** Khi khách nhắn, hệ thống PHẢI khởi động (hoặc khởi động lại) đúng loại đồng hồ (lần đầu nếu chưa từng phản hồi, các lần sau nếu đã từng).

  **Lý do nghiệp vụ:** phản hồi lần đầu và các lần sau có thời hạn và ý nghĩa khác nhau.

- **`BR-08.6` (Luôn ghi nhận mốc phản hồi):** Khi Agent trả lời, hệ thống PHẢI ghi nhận mốc phản hồi và dừng mọi đồng hồ phản hồi đang chạy — độc lập với việc hội thoại có Chính sách SLA hay không.

  **Lý do nghiệp vụ:** doanh nghiệp chưa đặt cam kết vẫn cần biết tốc độ phản hồi thật để đặt cam kết hợp lý.

- **`BR-08.7` (Tạm dừng khi tạm hoãn):** Khi hội thoại chuyển Tạm hoãn, mọi đồng hồ PHẢI tạm dừng; khi trở lại Đang mở, đồng hồ PHẢI tiếp tục từ thời gian còn lại.

  **Lý do nghiệp vụ:** thời gian chờ khách bổ sung thông tin không phải lỗi của doanh nghiệp.

- **`BR-08.8` (Chốt khi kết thúc):** Khi hội thoại Đã giải quyết/Đã đóng, cam kết giải quyết xong PHẢI được chốt; các cam kết phản hồi còn chạy PHẢI hủy.

  **Lý do nghiệp vụ:** hội thoại đã xong không được tiếp tục sinh vi phạm.

- **`BR-08.9` (Chu kỳ cam kết mới khi mở lại):** Khi hội thoại mở lại, mọi cam kết cũ (kể cả đã hoàn thành/vi phạm) PHẢI được giữ trong lịch sử và một chu kỳ cam kết mới bắt đầu.

  **Lý do nghiệp vụ:** đo chu kỳ mới bằng đồng hồ cũ thì hoặc vi phạm ngay, hoặc không bao giờ vi phạm.

- **`BR-08.10` (Một đồng hồ hiển thị):** Mỗi hội thoại PHẢI hiển thị một đồng hồ đếm ngược duy nhất — cam kết gần tới hạn nhất.

  **Lý do nghiệp vụ:** nhiều đồng hồ cùng lúc làm Agent không biết đâu là việc gấp.

- **`BR-08.11` (Phân khúc từ hồ sơ CRM):** **Phân khúc khách hàng** dùng cho SLA và phân công PHẢI đọc từ hồ sơ khách hàng trong CRM; Omnichat KHÔNG ĐƯỢC tự định nghĩa danh sách phân khúc riêng. Hội thoại gắn với Hồ sơ khách hàng tạm chưa có phân khúc áp cam kết mặc định và tự áp lại cam kết đúng ngay khi được liên kết với hồ sơ thật.

  **Lý do nghiệp vụ:** hai danh sách phân khúc sẽ lệch nhau, và khách VIP bị phục vụ như khách thường chỉ vì nhắn qua kênh chat.

- **`BR-08.12` (Im lặng tối đa giữa chừng):** Với kênh có Thuộc tính trò chuyện đồng thời, doanh nghiệp PHẢI đặt được cam kết **thời gian im lặng tối đa giữa chừng** (`CFG-08-02`). Vượt ngưỡng PHẢI cảnh báo Agent và Trưởng nhóm.

  **Lý do nghiệp vụ:** cam kết phản hồi không bắt được tình huống Agent đã nhận việc rồi để khách treo trên màn hình — tình huống khách bực nhất.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-08.1.1` | Chính sách: VIP phản hồi lần đầu 5 phút, Thường 30 phút | Hội thoại mới của khách VIP | Đồng hồ phản hồi lần đầu 5 phút |
| `AC-08.2.1` | Hộp thư "Khiếu nại" có chính sách riêng | Hội thoại mới thuộc Hộp thư đó | Áp chính sách của Hộp thư, không áp mặc định |
| `AC-08.3.1` | Hội thoại vừa mở lại | Xem hội thoại | Có cả cam kết phản hồi lần đầu và giải quyết xong đang chạy |
| `AC-08.4.1` | Giờ làm việc 08:00–17:00; cam kết 2 giờ làm việc | Khách nhắn lúc 16:30 | Hạn là 09:30 ngày làm việc kế tiếp |
| `AC-08.5.1` | Hội thoại đã từng được phản hồi | Khách nhắn tiếp | Đồng hồ "phản hồi các lần sau" khởi động |
| `AC-08.6.1` | Hội thoại không có chính sách SLA nào áp dụng | Agent trả lời | Mốc phản hồi được ghi nhận và có trong báo cáo tốc độ |
| `AC-08.7.1` | Còn 10 phút tới hạn | Tạm hoãn 2 giờ rồi khách nhắn lại | Đồng hồ tiếp tục với 10 phút còn lại |
| `AC-08.8.1` | Cam kết phản hồi đang chạy | Agent giải quyết hội thoại | Cam kết giải quyết xong được chốt; cam kết phản hồi bị hủy, không sinh vi phạm |
| `AC-08.9.1` | Chu kỳ trước đã vi phạm phản hồi lần đầu | Hội thoại mở lại | Lịch sử giữ vi phạm cũ; chu kỳ mới có đồng hồ mới |
| `AC-08.10.1` | Cam kết phản hồi còn 3 phút, giải quyết còn 2 giờ | Agent nhìn danh sách | Đồng hồ hiển thị 3 phút |
| `AC-08.11.1` | Hội thoại của Hồ sơ khách hàng tạm đang áp cam kết mặc định | Agent liên kết với hồ sơ VIP | Cam kết VIP được áp ngay |
| `AC-08.12.1` | Ngưỡng im lặng 3 phút trên Live Chat, cam kết phản hồi các lần sau 5 phút | Agent đã nhận, không gửi gì trong 3 phút | Agent và Trưởng nhóm nhận cảnh báo dù cam kết phản hồi chưa tới hạn |

**Tham chiếu:** `BR-08.11`, `BR-08.12` → issue [#75](https://github.com/crmsaassaudi/product-management/issues/75). Lịch làm việc khi tính cam kết → [ADR-0006](../docs/adr/0006-sla-operating-hours-and-business-calendar-strategy.md).

---

### FEAT-09 — Leo thang khi vi phạm SLA

**Mô tả nghiệp vụ:** Khi một hội thoại có nguy cơ hoặc đã vi phạm cam kết, hệ thống tự động thực hiện hành động cảnh báo phù hợp, để sự cố được xử lý trước khi ảnh hưởng tới khách hàng.

**Vai trò sử dụng chính:** Hệ thống (thực hiện); Người có quyền quản trị Quản lý cam kết phục vụ (cấu hình); người nhận cảnh báo.

**Điều kiện tiên quyết:** Có Chính sách SLA đang áp dụng.

**Luồng chính:**

1. Quản trị viên định nghĩa Chính sách leo thang gắn với một Chính sách SLA: kích hoạt khi "sắp vi phạm" hay "đã vi phạm", sau bao lâu, hành động gì.
2. Khi hội thoại vi phạm/sắp vi phạm, hệ thống chờ đúng khoảng đã cấu hình rồi thực hiện hành động.
3. Nếu hội thoại đã xong trước khi tới lượt, hành động tự hủy.

**Quy tắc nghiệp vụ:**

- **`BR-09.1` (Gắn với một chính sách SLA):** Chính sách leo thang PHẢI gắn với đúng một Chính sách SLA, xác định độ trễ kích hoạt và danh sách hành động.

  **Lý do nghiệp vụ:** leo thang không gắn với cam kết thì không biết đang leo thang vì lời hứa nào.

- **`BR-09.2` (Các hành động):** Hệ thống PHẢI hỗ trợ: đánh dấu cảnh báo trên hội thoại, thông báo cho một người cụ thể, thông báo cho Người phụ trách đơn vị tiếp nhận và các cấp phụ trách đơn vị phía trên (leo lên N cấp trong nhánh), hoặc tự động chuyển hội thoại cho người khác **trong cùng đơn vị tiếp nhận**. Người nhận được chỉ định trong chính sách phải đạt `BR-07.2` và nằm trong mức (Hội thoại, Gán) của người cấu hình chính sách; tự động chuyển sang hàng đợi đơn vị khác chỉ tới đích trong danh sách `BR-05.15` và chỉ khi ô (Hội thoại, Gán) của người cấu hình bao phủ hội thoại của hàng đợi nguồn, nhất quán với [`tickets-srs.md`](./tickets-srs.md) `BR-18.2`, `BR-10.6`. Khi thực hiện, người nhận được xét lại; không còn đạt thì hội thoại vào hàng đợi của đơn vị tiếp nhận và người cấu hình được báo.

  **Lý do nghiệp vụ:** "cấp quản lý cao hơn" phải xác định được từ cây tổ chức, không phải từ một danh sách tên cố định dễ lỗi thời.

- **`BR-09.3` (Kiểm tra trước khi thực hiện):** Trước khi thực hiện, hệ thống PHẢI kiểm tra hội thoại vẫn chưa kết thúc; nếu đã xong, hành động tự hủy.

  **Lý do nghiệp vụ:** cảnh báo cho việc đã xong làm người nhận coi nhẹ mọi cảnh báo sau.

- **`BR-09.4` (Không chồng chéo):** Nếu hội thoại vi phạm nghiêm trọng hơn, hệ thống PHẢI thay hành động cũ đang chờ bằng hành động mới, không thực hiện chồng chéo cho cùng một lần vi phạm.

  **Lý do nghiệp vụ:** năm thông báo cho cùng một việc là tiếng ồn, không phải leo thang.

- **`BR-09.5` (Gửi đúng người):** Thông báo leo thang PHẢI gửi tới đúng người được chỉ định hoặc đúng cấp phụ trách được xác định, không gửi tràn lan; người nhận chỉ thấy nội dung hội thoại trong phạm vi mức (Hội thoại, Xem) của mình — thông báo tới người ngoài phạm vi chỉ nêu mã hội thoại, đơn vị tiếp nhận và mức vi phạm.

  **Lý do nghiệp vụ:** thông báo là một đường lộ dữ liệu (`NFR-8`); người được báo để can thiệp không tự động có quyền đọc nội dung.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-09.1.1` | Quản trị viên tạo chính sách leo thang | Lưu mà không chọn Chính sách SLA | Nút lưu bị vô hiệu kèm yêu cầu chọn một Chính sách SLA |
| `AC-09.2.1` | Leo thang "lên 2 cấp" khi đã vi phạm 15 phút | Hội thoại thuộc "Hỗ trợ – Hà Nội" vi phạm 15 phút | Người phụ trách "Hỗ trợ – Hà Nội" và "Hỗ trợ" nhận thông báo |
| `AC-09.2.2` | Hành động "tự chuyển cho người khác" | Tới lượt thực hiện | Hội thoại được gán cho Agent khác đủ điều kiện trong cùng đơn vị tiếp nhận |
| `AC-09.2.3` | Người cấu hình có (Hội thoại, Gán) = Đơn vị của mình ở "Hỗ trợ – Hà Nội" | Chỉ định người nhận tự chuyển thuộc "Hỗ trợ – Đà Nẵng" | Người đó không có trong danh sách chọn |
| `AC-09.2.4` | Người nhận được chỉ định đã bị tạm ngưng khi tới lượt | Hành động tự chuyển chạy | Hội thoại vào hàng đợi của đơn vị tiếp nhận; người cấu hình được báo |
| `AC-09.3.1` | Hội thoại được giải quyết trước lượt leo thang | Tới lượt | Không có thông báo nào |
| `AC-09.4.1` | Đang chờ leo thang "sắp vi phạm" | Hội thoại chuyển sang "đã vi phạm" | Chỉ hành động của "đã vi phạm" được thực hiện |
| `AC-09.5.1` | Người được chỉ định nhận cảnh báo không có (Hội thoại, Xem) bao phủ hội thoại | Nhận thông báo | Thấy mã hội thoại, đơn vị tiếp nhận, mức vi phạm; không thấy nội dung |

---

### FEAT-10 — Tự động đóng hội thoại không hoạt động

**Mô tả nghiệp vụ:** Tự động chuyển các hội thoại không còn hoạt động sang Đã giải quyết/Đã đóng sau một khoảng thời gian, giữ danh sách hội thoại phản ánh đúng việc cần xử lý — có cảnh báo trước cho khách để không đóng đột ngột.

**Vai trò sử dụng chính:** Hệ thống (thực hiện); Người có quyền quản trị Quản lý cam kết phục vụ (cấu hình); Agent có (Hội thoại, Sửa) bao phủ hội thoại (can thiệp từng hội thoại).

**Điều kiện tiên quyết:** Có Chính sách tự động đóng (`CFG-10-01`).

**Luồng chính:**

1. Quản trị viên định nghĩa Chính sách tự động đóng: điều kiện áp dụng, thời gian không hoạt động, có cảnh báo trước không, thời gian ân hạn, trạng thái đích, cách xử lý khi khách nhắn lại.
2. Hội thoại đạt ngưỡng không hoạt động; nếu bật cảnh báo, hệ thống hỏi khách còn cần hỗ trợ không và chờ ân hạn.
3. Khách phản hồi hoặc có hoạt động mới → hủy việc đóng.
4. Hết ân hạn không phản hồi → đóng theo trạng thái đích.

**Quy tắc nghiệp vụ:**

- **`BR-10.1` (Chính sách tự động đóng):** Quản trị viên PHẢI cấu hình được Chính sách tự động đóng theo điều kiện áp dụng (kênh, nhãn, Hộp thư), thời gian không hoạt động và trạng thái đích (`CFG-10-01`).

  **Lý do nghiệp vụ:** hội thoại email chờ khách vài ngày là bình thường, hội thoại Live Chat im 30 phút là đã xong.

- **`BR-10.2` (Không bao giờ tự đóng):** Doanh nghiệp CÓ THỂ chọn "không bao giờ tự động đóng" cho một số nhóm hội thoại.

  **Lý do nghiệp vụ:** hội thoại đang chờ xử lý nội bộ đặc biệt không được đóng chỉ vì khách im lặng.

- **`BR-10.3` (Cảnh báo trước khi đóng):** Nếu chính sách bật cảnh báo, hệ thống PHẢI gửi tin hỏi khách trước khi đóng và CÓ THỂ lặp lại theo cấu hình. Ngoại lệ: không gửi tin hỏi tới khách có tin gắn nhãn "Có thể là lời từ chối nhận tin" chưa xử lý (`BR-10.9`).

  **Lý do nghiệp vụ:** đóng đột ngột làm khách tưởng bị bỏ rơi; còn gửi tin chủ động tới người có thể đã từ chối nhận tin là rủi ro tuân thủ.

- **`BR-10.4` (Hoạt động thật hủy việc đóng):** Bất kỳ hoạt động thực sự nào từ khách hoặc Agent trong lúc chờ đóng PHẢI hủy việc đóng ngay; tin cảnh báo tự động của chính hệ thống không tính là hoạt động.

  **Lý do nghiệp vụ:** nếu tin cảnh báo tự nó hủy việc đóng, hội thoại sẽ không bao giờ đóng được.

- **`BR-10.5` (Can thiệp từng hội thoại):** Người có (Hội thoại, Sửa) bao phủ hội thoại PHẢI tự đặt được: miễn trừ tự động đóng, hoãn thêm, hoặc ép đóng vào một thời điểm chỉ định.

  **Lý do nghiệp vụ:** Agent biết những điều chính sách chung không biết (khách hẹn gửi chứng từ sau ba ngày).

- **`BR-10.6` (Khách nhắn lại sau khi tự đóng):** Khi hội thoại được tự đóng dưới trạng thái Đã giải quyết, doanh nghiệp PHẢI cấu hình được cách xử lý khi khách nhắn lại: luôn mở lại, luôn tạo mới, hoặc mở lại nếu trong một khoảng thời gian.

  **Lý do nghiệp vụ:** cùng một câu hỏi tiếp nối nên ở cùng hội thoại; câu hỏi mới sau nhiều ngày nên là việc mới.

- **`BR-10.7` (Hoãn do Ngưỡng an toàn đóng hàng loạt):** Khi việc đóng bị hoãn vì chạm Ngưỡng an toàn đóng hàng loạt, hệ thống PHẢI tự đóng lại hội thoại ngay khi tình trạng bất thường kết thúc, và — trừ hội thoại còn tin gắn nhãn chưa xử lý theo `BR-10.9` — không để hội thoại chờ quá thời hạn doanh nghiệp cấu hình (`CFG-10-02`).

  **Lý do nghiệp vụ:** cơ chế bảo vệ không được biến thành nơi hội thoại treo vô thời hạn chờ người can thiệp tay.

- **`BR-10.8` (Lý do chưa đóng hiển thị trên hội thoại):** Khi một hội thoại đủ điều kiện đóng nhưng chưa đóng, người xem hội thoại PHẢI biết **lý do ngay trên hội thoại**, phân biệt tối thiểu: đang hoãn vì Ngưỡng an toàn đóng hàng loạt, còn tin gắn nhãn chưa xử lý, hay vì nguyên nhân khác. Mỗi lần Ngưỡng an toàn kích hoạt PHẢI sinh cảnh báo tới Người phụ trách các đơn vị tiếp nhận bị ảnh hưởng, kèm số hội thoại và kênh liên quan.

  **Lý do nghiệp vụ:** không biết vì sao hội thoại chưa đóng thì người vận hành không biết nên đợi hay nên xử lý.

- **`BR-10.9` (Tin có thể là lời từ chối nhận tin):** Khi hội thoại còn tin mang nhãn "Có thể là lời từ chối nhận tin" chưa xử lý ([`campaigns-srs.md`](./campaigns-srs.md) `BR-26.3`), hội thoại PHẢI KHÔNG chuyển được sang Đã giải quyết hay Đã đóng bằng bất kỳ đường nào (Agent đánh dấu, đóng hàng loạt, ép đóng theo `BR-10.5`, tự động đóng); lý do hiển thị theo `BR-10.8`.

  **Lý do nghiệp vụ:** đóng hội thoại trước khi xử lý lời từ chối nhận tin là để doanh nghiệp tiếp tục gửi tin cho người đã từ chối.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-10.1.1` | Chính sách Live Chat: không hoạt động 30 phút → Đã giải quyết | Hội thoại Live Chat im 30 phút, không bật cảnh báo | Hội thoại chuyển Đã giải quyết |
| `AC-10.2.1` | Nhãn "Chờ kỹ thuật" thuộc nhóm không bao giờ tự đóng | Hội thoại mang nhãn đó im 10 ngày | Không bị đóng |
| `AC-10.3.1` | Chính sách bật cảnh báo, ân hạn 30 phút | Hội thoại đạt ngưỡng | Khách nhận tin hỏi trước khi đóng |
| `AC-10.3.2` | Hội thoại còn tin gắn nhãn "Có thể là lời từ chối nhận tin" chưa xử lý | Hội thoại đạt ngưỡng | Không gửi tin hỏi |
| `AC-10.4.1` | Đang Chờ đóng | Khách trả lời | Việc đóng bị hủy, hội thoại về Đang mở |
| `AC-10.4.2` | Đang Chờ đóng | Hệ thống gửi tin cảnh báo lần hai | Việc đóng không bị hủy |
| `AC-10.5.1` | Agent có (Hội thoại, Sửa) bao phủ | Đặt miễn trừ tự động đóng | Hội thoại không bị tự đóng tới khi gỡ miễn trừ |
| `AC-10.6.1` | Cấu hình "mở lại nếu trong 24 giờ" | Khách nhắn lại sau 30 giờ | Tạo hội thoại mới |
| `AC-10.7.1` | Ngưỡng an toàn kích hoạt, thời hạn tối đa 10 phút | Tình trạng bất thường kéo dài 30 phút | Hội thoại bị hoãn được đóng trong vòng 10 phút |
| `AC-10.8.1` | Hội thoại đang hoãn vì Ngưỡng an toàn | Giám sát viên mở hội thoại | Thấy lý do "Hoãn do Ngưỡng an toàn đóng hàng loạt" |
| `AC-10.8.2` | Ngưỡng an toàn kích hoạt trên kênh Zalo | Kích hoạt | Người phụ trách các đơn vị tiếp nhận bị ảnh hưởng nhận cảnh báo kèm số hội thoại và kênh |
| `AC-10.9.1` | Hội thoại còn tin gắn nhãn "Có thể là lời từ chối nhận tin" | Agent bấm "Giải quyết" | Nút bị vô hiệu kèm lý do "còn tin gắn nhãn chưa xử lý" |
| `AC-10.9.2` | Tình huống AC-10.9.1 | Đóng hàng loạt gồm hội thoại đó | Hội thoại đó không bị đóng; màn hình xác nhận nêu số hội thoại bị loại và lý do |

**Tham chiếu:** `BR-10.7` → issue [#17](https://github.com/crmsaassaudi/product-management/issues/17). `BR-10.8` → issue [#18](https://github.com/crmsaassaudi/product-management/issues/18).

---

### FEAT-11 — Bot trả lời tự động & bàn giao cho Agent

**Mô tả nghiệp vụ:** Cho phép một kịch bản trả lời tự động (Bot) tiếp nhận và xử lý hội thoại thay Agent trong giai đoạn đầu (chào hỏi, thu thập thông tin, trả lời câu hỏi thường gặp), rồi bàn giao đúng lúc cho Agent phù hợp.

**Vai trò sử dụng chính:** Bot; Agent (nhận bàn giao, tắt/bật Bot); Người có quyền quản trị Quản lý Bot (cấu hình).

**Điều kiện tiên quyết:** Kênh và hội thoại đều đang cho phép Bot (`BR-11.1`).

**Luồng chính:**

1. Tin nhắn khách mới tới; nếu kênh/hội thoại đang bật Bot, hệ thống chuyển cho Bot.
2. Bot trả lời theo kịch bản.
3. Khi cần con người, Bot bàn giao — cho một Agent cụ thể, một nhóm xử lý, hoặc hàng đợi của đơn vị tiếp nhận.
4. Agent tắt Bot bất kỳ lúc nào để tự tiếp quản, và bật lại nếu muốn.

**Quy tắc nghiệp vụ:**

- **`BR-11.1` (Hai cấp cho phép):** Bot chỉ được kích hoạt khi cả cấu hình cấp kênh và cấp hội thoại đều cho phép.

  **Lý do nghiệp vụ:** Agent cần tắt được Bot cho riêng một hội thoại nhạy cảm mà không tắt cả kênh.

- **`BR-11.2` (Chế độ theo kênh):** Doanh nghiệp CÓ THỂ chọn theo từng kênh một trong ba chế độ (`CFG-11-01`): Bot trước rồi luôn bàn giao khi kết thúc kịch bản; Bot xử lý toàn bộ và chỉ bàn giao khi không có kịch bản phù hợp; hoặc tắt Bot.

  **Lý do nghiệp vụ:** kênh bán hàng muốn người vào sớm, kênh hỏi đáp thường gặp muốn Bot tự xong.

- **`BR-11.3` (Bàn giao phải hợp lệ):** Khi Bot bàn giao có chỉ định Agent/nhóm, hệ thống PHẢI kiểm tra đích còn hợp lệ — Agent thỏa `BR-04.1` (a), (b), nhóm thuộc cùng đơn vị tiếp nhận — và hỗ trợ đúng kênh; nếu không, bàn giao vào hàng đợi của đơn vị tiếp nhận của hội thoại. Bot không bao giờ chuyển hội thoại sang đơn vị tiếp nhận khác.

  **Lý do nghiệp vụ:** kịch bản Bot được viết từ trước và không biết ai vừa nghỉ việc; còn để kịch bản tự đổi đơn vị của hội thoại là để một cấu hình bên ngoài phân quyền quyết định ai được xem.

- **`BR-11.4` (Tắt Bot để tiếp quản):** Agent có (Hội thoại, Sửa) bao phủ hội thoại PHẢI tắt được Bot để tiếp quản ngay, và bật lại khi muốn.

  **Lý do nghiệp vụ:** khách đang bực vì Bot trả lời vòng vo cần người ngay.

- **`BR-11.5` (Bỏ qua phản hồi muộn):** Phản hồi từ Bot đến muộn sau khi Agent đã tiếp quản hoặc Bot đã bàn giao PHẢI bị bỏ qua.

  **Lý do nghiệp vụ:** Bot chen vào sau khi người đã tiếp quản làm khách bối rối và có thể nói ngược điều Agent vừa nói.

- **`BR-11.6` (Agent trả lời thì tắt Bot):** Khi Agent trả lời một hội thoại mà Bot đang hoạt động, hệ thống PHẢI tự tắt Bot cho hội thoại đó, trừ khi doanh nghiệp cấu hình khác (`CFG-11-02`).

  **Lý do nghiệp vụ:** Bot và Agent cùng trả lời chồng chéo là trải nghiệm tệ nhất.

- **`BR-11.7` (Ranh giới dữ liệu của Bot):** Bot chỉ nhận và trả lời nội dung của **đúng hội thoại đang được giao cho nó**, không đọc được hội thoại khác hay hồ sơ khách hàng ngoài những trường doanh nghiệp chủ động truyền cho kịch bản. Bước kịch bản cố định nhận nội dung như người phụ trách nhưng không nhận dữ liệu nhạy cảm đã che theo `BR-23.8` ở dạng đầy đủ. **Mọi bước của Bot đưa dữ liệu cho mô hình AI** là Tác nhân AI ở bước đó ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4) và chỉ nhận giá trị đã che của mọi trường nhạy cảm tại `BR-23.9`, gồm nội dung tin nhắn ở dạng đã che theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-40.1`.

  **Lý do nghiệp vụ:** Bot chạy ngoài tầm mắt của Agent; quyền đọc của nó phải vừa đủ để phục vụ đúng một khách, và đầu ra của mô hình AI có thể ra khỏi tầm kiểm soát của nhật ký nên không được nhận dữ liệu đầy đủ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-11.1.1` | Kênh bật Bot, Agent đã tắt Bot cho hội thoại X | Khách nhắn vào X | Bot không trả lời |
| `AC-11.2.1` | Kênh đặt chế độ "tắt Bot" | Khách mới nhắn | Hội thoại vào phân công cho Agent ngay |
| `AC-11.3.1` | Bot chỉ định bàn giao cho Agent đã nghỉ việc | Bot bàn giao | Hội thoại vào hàng đợi của đơn vị tiếp nhận |
| `AC-11.3.2` | Bot chỉ định nhóm thuộc đơn vị tiếp nhận khác | Bot bàn giao | Hội thoại vào hàng đợi của đơn vị tiếp nhận của chính nó, không đổi đơn vị |
| `AC-11.4.1` | Bot đang trả lời | Agent tắt Bot | Agent tiếp quản ngay |
| `AC-11.5.1` | Agent vừa tiếp quản | Một phản hồi Bot đến muộn | Phản hồi đó không hiện với khách |
| `AC-11.6.1` | Bot đang hoạt động, cấu hình mặc định | Agent gửi tin | Bot tự tắt cho hội thoại đó |
| `AC-11.7.1` | Bot theo kịch bản cố định; khách gõ số thẻ thanh toán | Bot nhận tin | Bước kịch bản nhận "[số thẻ đã che]", không nhận số thẻ đầy đủ |
| `AC-11.7.2` | Kịch bản có một bước gửi câu hỏi của khách cho mô hình AI để trả lời | Khách gõ số điện thoại và địa chỉ email trong câu hỏi | Bước đó nhận nội dung và định danh ở dạng đã che; nhật ký ghi bước là Tác nhân AI |

---

### FEAT-12 — Gửi tin nhắn cho khách hàng

**Mô tả nghiệp vụ:** Cho phép Agent (hoặc Bot, hoặc hệ thống) gửi tin nhắn trả lời khách qua đúng kênh của hội thoại, đáng tin cậy và tuân thủ giới hạn của từng nền tảng.

**Vai trò sử dụng chính:** Agent (cần (Hội thoại, Sửa) bao phủ hội thoại); Bot; Hệ thống (tin tự động).

**Điều kiện tiên quyết:** Hội thoại chưa Đã đóng.

**Luồng chính:**

1. Agent soạn tin (văn bản, tệp, tin nhắn mẫu, tin có nút bấm) và gửi.
2. Hệ thống kiểm tra hội thoại còn được phép gửi (chưa đóng, còn trong Cửa sổ phản hồi nếu kênh có giới hạn).
3. Tin được gửi qua đúng kênh; trạng thái gửi cập nhật cho Agent (đang gửi → đã gửi → đã nhận → đã đọc, hoặc lỗi).

**Quy tắc nghiệp vụ:**

- **`BR-12.1` (Không gửi vào hội thoại đã đóng):** Hệ thống PHẢI từ chối gửi tin vào hội thoại Đã đóng.

  **Lý do nghiệp vụ:** tin gửi vào hội thoại đã chốt sổ không ai theo dõi phản hồi.

- **`BR-12.2` (Hết Cửa sổ phản hồi):** Khi đã hết Cửa sổ phản hồi trên kênh có giới hạn này, hệ thống PHẢI ngăn gửi tin tự do; với kênh có Thuộc tính **tin nhắn mẫu đã phê duyệt trước** (`BR-01.8`), Agent CÓ THỂ vẫn gửi loại tin này.

  **Lý do nghiệp vụ:** vi phạm giới hạn của nền tảng có thể khiến doanh nghiệp bị khóa kênh.

- **`BR-12.3` (Tự thích ứng nội dung):** Hệ thống PHẢI tự thích ứng nội dung theo khả năng của kênh (`BR-01.8`) — chuyển nút bấm thành danh sách lựa chọn đánh số khi kênh không hỗ trợ — và PHẢI báo trước cho Agent rằng khách sẽ thấy nội dung khác lúc soạn.

  **Lý do nghiệp vụ:** Agent cần biết khách thực sự nhìn thấy gì.

- **`BR-12.4` (Gửi lại khi chắc chắn):** Khi tin gửi thất bại và hệ thống biết chắc khách chưa nhận, hệ thống CHO PHÉP gửi lại. Khi không biết chắc, hệ thống KHÔNG ĐƯỢC tự gửi lại: tin PHẢI ở trạng thái không chắc chắn kèm chỉ dẫn để Agent quyết định.

  **Lý do nghiệp vụ:** khách nhận hai lần cùng một tin là trải nghiệm tệ hơn việc Agent chủ động gửi lại.

- **`BR-12.5` (Trả lời hội thoại chưa có người phụ trách là tự nhận):** Khi một người trả lời hội thoại chưa có Người phụ trách, thao tác đó là tự nhận việc theo `BR-05.5`: chỉ thực hiện được khi người đó thỏa điều kiện nhận việc, và hội thoại được gán cho người đó trong cùng thao tác. Người không nhận được việc nhưng có (Hội thoại, Can thiệp) bao phủ hội thoại trả lời được theo `BR-26.5` (tiếp quản).

  **Lý do nghiệp vụ:** trả lời mà không trở thành người chịu trách nhiệm là để khách nhận câu trả lời từ một người không ai theo dõi tiếp.

- **`BR-12.6` (Ưu tiên tin trả lời khách đang chờ):** Khi kênh giới hạn số tin doanh nghiệp được gửi trong một khoảng thời gian, tin Agent trả lời trực tiếp cho khách đang chờ PHẢI luôn được ưu tiên trước mọi loại tin khác trên kênh đó (thông báo tự động, lời mời khảo sát, tin chiến dịch).

  **Lý do nghiệp vụ:** khách đang chờ không được bị trả lời chậm vì tin tiếp thị.

- **`BR-12.7` (Đánh dấu tin tự động):** Tin hệ thống tự gửi thay mặt doanh nghiệp (thông báo ngoài giờ, cảnh báo trước khi đóng, lời mời CSAT) PHẢI được đánh dấu khác tin Agent gửi.

  **Lý do nghiệp vụ:** tính nhầm tin tự động là hoạt động của Agent làm sai báo cáo tốc độ và quy tắc tự động đóng.

- **`BR-12.8` (Không treo ở "đang gửi"):** Hệ thống KHÔNG ĐƯỢC để một tin dừng vô thời hạn ở "đang gửi": quá thời hạn (`CFG-12-01`), tin PHẢI chuyển sang thất bại hoặc không chắc chắn (`BR-12.4`) kèm chỉ dẫn.

  **Lý do nghiệp vụ:** Agent phải luôn biết chắc tin đã tới khách hay chưa.

- **`BR-12.9` (Cửa sổ phản hồi sắp hết):** Khi Cửa sổ phản hồi sắp hết hoặc đã hết, hệ thống PHẢI: (a) cảnh báo Agent và Trưởng nhóm **trước khi** hết hạn (`CFG-12-02`) với hội thoại khách còn chờ trả lời; (b) khi đã hết và kênh không có tin nhắn mẫu, gợi ý các cách liên hệ khác đã có trong hồ sơ khách (hiển thị theo mẫu che `BR-23.9`), và ghi nhận hội thoại thuộc diện "không liên hệ lại được trên kênh gốc".

  **Lý do nghiệp vụ:** mất cửa sổ là mất khả năng chủ động liên hệ khách trên kênh đó; Giám sát viên cần biết quy mô tình huống này.

- **`BR-12.10` (Thu hồi tin gửi nhầm):** Agent PHẢI thu hồi được một tin vừa gửi nhầm trong phạm vi kênh cho phép; khi kênh không cho phép, hệ thống PHẢI nói rõ ngay lúc thao tác. Mọi lần thu hồi PHẢI để lại vết (ai, lúc nào, nội dung gốc) theo `BR-15.4`, và nội dung gốc PHẢI tra cứu được khi có khiếu nại bởi người có quyền xem đầy đủ nội dung (`BR-23.9`).

  **Lý do nghiệp vụ:** thu hồi là để khách không đọc phải, không phải để xóa dấu vết.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-12.1.1` | Hội thoại Đã đóng | Agent mở ô soạn tin | Ô soạn bị khóa kèm lý do "Hội thoại đã đóng" |
| `AC-12.2.1` | Kênh có Cửa sổ phản hồi và có tin nhắn mẫu; cửa sổ đã hết | Agent soạn tin tự do | Chặn gửi; gợi ý dùng tin nhắn mẫu |
| `AC-12.3.1` | Kênh không hỗ trợ nút bấm | Agent soạn tin có 3 nút | Trước khi gửi, Agent thấy bản xem trước dạng danh sách đánh số |
| `AC-12.4.1` | Kết quả gửi không rõ | Hệ thống nhận kết quả mơ hồ | Tin ở trạng thái không chắc chắn kèm chỉ dẫn; không tự gửi lại |
| `AC-12.5.1` | A thỏa điều kiện nhận việc; hội thoại chưa có Người phụ trách | A gửi tin trả lời | Hội thoại gán cho A trong cùng thao tác |
| `AC-12.5.2` | B có (Hội thoại, Gán) = Không có | B mở hội thoại chưa có người phụ trách | Ô soạn tin bị khóa kèm lý do "Bạn không nhận được việc này" |
| `AC-12.6.1` | Kênh đang chạm giới hạn số tin, có tin chiến dịch đang chờ | Agent trả lời khách đang chờ | Tin của Agent đi trước tin chiến dịch |
| `AC-12.7.1` | Hệ thống gửi thông báo ngoài giờ | Xem báo cáo tốc độ phản hồi | Tin đó không được tính là phản hồi của Agent |
| `AC-12.8.1` | Tin ở "đang gửi" quá thời hạn | Hết thời hạn | Tin chuyển sang thất bại/không chắc chắn kèm chỉ dẫn |
| `AC-12.9.1` | Khách còn chờ trả lời, cửa sổ còn 1 giờ, cảnh báo trước 2 giờ | Tới mốc | Agent và Trưởng nhóm đã nhận cảnh báo |
| `AC-12.9.2` | Cửa sổ đã hết, kênh không có tin nhắn mẫu | Agent mở hội thoại | Thấy gợi ý email/số điện thoại (đã che theo mẫu) trong hồ sơ; hội thoại có dấu "không liên hệ lại được trên kênh gốc" |
| `AC-12.10.1` | Kênh cho phép thu hồi | Agent thu hồi tin gửi nhầm | Khách không còn thấy tin; lịch sử ghi ai thu hồi, lúc nào, nội dung gốc |
| `AC-12.10.2` | Kênh không cho phép thu hồi | Agent bấm thu hồi | Hệ thống báo rõ "Kênh này không cho phép thu hồi" |

**Tham chiếu:** `BR-12.9`, `BR-12.10` → issue [#64](https://github.com/crmsaassaudi/product-management/issues/64).

---

### FEAT-13 — Không bỏ lỡ việc và không trùng việc

**Mô tả nghiệp vụ:** Bảo đảm hai điều với đội đang trực: không ai bỏ lỡ một việc đang chờ mình, và không hai người cùng làm một việc rồi chồng chéo trước mặt khách.

**Vai trò sử dụng chính:** Agent; Trưởng nhóm; Giám sát viên.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Có sự kiện trên một hội thoại (tin mới, đổi trạng thái, đổi người phụ trách, lời mời).
2. Hệ thống đưa cập nhật tới đúng những người liên quan và có quyền xem.

**Quy tắc nghiệp vụ:**

- **`BR-13.1` (Cập nhật tức thời):** Mọi tin mới, thay đổi trạng thái, thay đổi người phụ trách và lời mời nhận hội thoại PHẢI xuất hiện ngay trên màn hình của những người liên quan, không đòi hỏi tự thao tác để thấy.

  **Lý do nghiệp vụ:** Agent phải làm mới màn hình mới thấy tin của khách là khách chờ vô ích.

- **`BR-13.2` (Chỉ nhận cập nhật trong phạm vi):** Một người chỉ nhận được cập nhật của hội thoại nằm trong phạm vi mức (Hội thoại, Xem) của mình cộng các nguồn nới phạm vi đang áp ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`) — gồm hàng đợi của đơn vị tiếp nhận mình thuộc; nội dung trong cập nhật hiển thị theo `BR-23.9`.

  **Lý do nghiệp vụ:** cập nhật tức thời là một đường lộ dữ liệu như mọi màn hình khác (`NFR-8`).

- **`BR-13.3` (Một hội thoại, một người nhận):** Khi hai người cùng lúc nhận một hội thoại đang chờ, hệ thống PHẢI bảo đảm chỉ đúng một người nhận được; người còn lại được báo hội thoại đã có người nhận.

  **Lý do nghiệp vụ:** hai người cùng trả lời một khách là sự cố trước mặt khách.

- **`BR-13.4` (Cảnh báo đang soạn):** Khi một người đang soạn tin cho một hội thoại, những người khác đang mở hội thoại đó PHẢI thấy rõ ai đang soạn. Đây là **cảnh báo, không phải khóa**.

  **Lý do nghiệp vụ:** có tình huống hợp lệ cần người thứ hai trả lời (tiếp quản gấp theo `BR-26.5`, bàn giao giữa chừng).

- **`BR-13.5` (Thông báo cá nhân đúng người):** Thông báo cá nhân (yêu cầu chuyển tiếp, leo thang, được nhắc tên trong ghi chú) PHẢI gửi đúng người liên quan, không tràn lan cả đội.

  **Lý do nghiệp vụ:** thông báo tràn lan làm mọi người bỏ qua mọi thông báo.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-13.1.1` | A đang phụ trách và mở danh sách | Khách gửi tin mới | Tin hiện ngay trên màn hình A, không phải làm mới |
| `AC-13.2.1` | B thuộc "Hỗ trợ – Đà Nẵng", (Hội thoại, Xem) = Đơn vị của mình | Một hội thoại của "Hỗ trợ – Hà Nội" có tin mới | B không nhận cập nhật nào |
| `AC-13.2.2` | B thuộc đơn vị tiếp nhận, (Hội thoại, Xem) = Chỉ của mình | Hội thoại mới vào hàng đợi của đơn vị | B nhận cập nhật ở dạng tóm tắt theo `BR-05.7` |
| `AC-13.3.1` | Hai Agent cùng bấm "Nhận" một hội thoại | Gần như đồng thời | Chỉ một người nhận được; người kia thấy "Đã có người nhận" |
| `AC-13.4.1` | A đang soạn | B mở cùng hội thoại | B thấy "A đang soạn tin"; ô soạn của B không bị khóa |
| `AC-13.5.1` | A được nhắc tên trong ghi chú | Ghi chú được lưu | Chỉ A nhận thông báo |

---

### FEAT-14 — Khảo sát hài lòng khách hàng (CSAT)

**Mô tả nghiệp vụ:** Sau khi một hội thoại được giải quyết, tự động mời khách chấm điểm hài lòng, để đo chất lượng phục vụ không chỉ bằng tốc độ.

**Vai trò sử dụng chính:** Khách hàng (chấm điểm); Hệ thống (gửi khảo sát); người xem báo cáo.

**Điều kiện tiên quyết:** Kênh hỗ trợ khảo sát.

**Luồng chính:**

1. Hội thoại chuyển Đã giải quyết.
2. Hệ thống gửi lời mời khảo sát qua đúng kênh (ngay trên Live Chat, hoặc đường dẫn khảo sát qua kênh khác).
3. Khách chấm 1–5 sao, có thể kèm nhận xét.
4. Điểm được ghi nhận và tính vào báo cáo.

**Quy tắc nghiệp vụ:**

- **`BR-14.1` (Tự động gửi):** Hệ thống PHẢI tự gửi lời mời khảo sát khi hội thoại chuyển Đã giải quyết, với kênh có hỗ trợ khảo sát.

  **Lý do nghiệp vụ:** gửi tay thì chỉ những hội thoại suôn sẻ được khảo sát, điểm bị thiên lệch.

- **`BR-14.2` (Chấm một lần, có thời hạn):** Mỗi lời mời PHẢI chỉ chấm được một lần và chỉ còn hiệu lực trong thời hạn cấu hình (`CFG-14-01`); quá hạn thì không dùng được và hội thoại ghi nhận là không có phản hồi.

  **Lý do nghiệp vụ:** chấm nhiều lần hoặc chấm sau nhiều tuần làm điểm mất giá trị đo lường.

- **`BR-14.3` (Báo cáo CSAT):** Hệ thống PHẢI cung cấp báo cáo CSAT: số khảo sát đã gửi, tỷ lệ phản hồi, điểm trung bình, phân bố theo mức, trung bình theo Agent và theo kênh — trong phạm vi mức (Hội thoại, Xem) của người xem (`BR-19.2`).

  **Lý do nghiệp vụ:** điểm trung bình không có tỷ lệ phản hồi đi kèm thì không biết đáng tin tới đâu.

- **`BR-14.4` (CSAT trong báo cáo vận hành):** Điểm CSAT trung bình PHẢI nằm trong **Báo cáo vận hành Omnichannel** (`FEAT-19`), dùng cùng bộ lọc ngày/kênh với các số liệu khác.

  **Lý do nghiệp vụ:** đối chiếu chất lượng với khối lượng trên hai màn hình riêng là đối chiếu không ai làm.

- **`BR-14.5` (CSAT trong báo cáo hiệu suất):** Điểm CSAT trung bình theo Agent PHẢI là một cột trong **Báo cáo hiệu suất Agent** (`FEAT-20`), cạnh các chỉ số tốc độ.

  **Lý do nghiệp vụ:** đánh giá Agent chỉ bằng tốc độ là thưởng cho việc đóng vội.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-14.1.1` | Kênh Live Chat có khảo sát | Agent giải quyết hội thoại | Khách thấy lời mời chấm điểm ngay trong widget |
| `AC-14.2.1` | Khách đã chấm 4 sao | Mở lại đường dẫn khảo sát | Không chấm lại được |
| `AC-14.2.2` | Thời hạn 7 ngày | Khách mở đường dẫn sau 8 ngày | Đường dẫn hết hiệu lực; hội thoại ghi "không có phản hồi" |
| `AC-14.3.1` | Trưởng nhóm có (Hội thoại, Xem) = Đơn vị và các đơn vị con | Mở báo cáo CSAT | Chỉ thấy số liệu của hội thoại trong nhánh đơn vị mình |
| `AC-14.4.1` | Báo cáo vận hành lọc theo kênh WhatsApp, tuần này | Xem | CSAT trung bình hiển thị theo đúng bộ lọc đó |
| `AC-14.5.1` | Mở Báo cáo hiệu suất Agent | Xem | Mỗi Agent có cột CSAT trung bình cạnh AHT |

**Tham chiếu:** `BR-14.4` → issue [#19](https://github.com/crmsaassaudi/product-management/issues/19). `BR-14.5` → issue [#20](https://github.com/crmsaassaudi/product-management/issues/20).

---

### FEAT-15 — Ghi chú, biểu tượng phản hồi nhanh & lịch sử xử lý

**Mô tả nghiệp vụ:** Cho phép ghi chú nội bộ trong lúc xử lý hội thoại (không hiển thị cho khách), phản hồi nhanh bằng biểu tượng trên một tin nhắn, và lưu toàn bộ các mốc quan trọng của hội thoại để tra cứu. Thả biểu tượng là thao tác của người, không phải năng lực phân tích cảm xúc (Mục 7).

**Vai trò sử dụng chính:** Agent và người có (Hội thoại, Sửa) bao phủ hội thoại (ghi chú, thả biểu tượng); người được mời Xin ý kiến (ghi chú); Khách hàng (thả biểu tượng trên kênh hỗ trợ); Hệ thống (ghi lịch sử).

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Người xử lý thêm ghi chú nội bộ hoặc ghim ghi chú bàn giao.
2. Hệ thống ghi mọi mốc quan trọng vào dòng thời gian của hội thoại.

**Quy tắc nghiệp vụ:**

- **`BR-15.1` (Ghi chú không lộ cho khách):** Ghi chú nội bộ PHẢI không hiển thị cho khách dưới bất kỳ hình thức nào.

  **Lý do nghiệp vụ:** ghi chú chứa nhận định nội bộ về khách; lộ ra là sự cố uy tín.

- **`BR-15.2` (Ghi chú bàn giao):** Người xử lý CÓ THỂ ghim một ghi chú làm "Ghi chú bàn giao" nổi bật đầu hội thoại; chỉ một ghi chú được ghim tại một thời điểm.

  **Lý do nghiệp vụ:** người nhận bàn giao (chuyển tiếp, trả về hàng đợi, đổi ca) cần biết ngay việc đang dở.

- **`BR-15.3` (Một biểu tượng mỗi người):** Mỗi người chỉ có một biểu tượng đang thả trên một tin tại một thời điểm; thả biểu tượng khác thay biểu tượng cũ.

  **Lý do nghiệp vụ:** nhiều biểu tượng của cùng một người trên một tin không mang thêm ý nghĩa.

- **`BR-15.4` (Dòng thời gian đầy đủ):** Hệ thống PHẢI tự ghi các mốc quan trọng (tạo, mở lại, đổi trạng thái, đổi người phụ trách, trả về hàng đợi, chuyển hàng đợi kèm nguồn/đích/lý do, thêm/xóa nhãn, chọn lý do xử lý, chuyển tiếp kèm lý do, thu hồi tin, vi phạm SLA, leo thang, bàn giao Bot, can thiệp của Trưởng nhóm, đánh dấu spam) thành một dòng thời gian, phân biệt hành động của người, của Bot và của hệ thống.

  **Lý do nghiệp vụ:** không có dòng thời gian thì không trả lời được "vì sao hội thoại ở tình trạng này".

- **`BR-15.5` (Khoảng trống phải nhìn thấy được):** Lịch sử xử lý là cam kết **đầy đủ** theo `BR-15.4`. Ghi lịch sử không được làm gián đoạn phục vụ khách, nhưng hệ thống KHÔNG ĐƯỢC âm thầm bỏ qua một mốc không ghi được: PHẢI đánh dấu khoảng trống tại đúng vị trí trong dòng thời gian và cảnh báo đội vận hành. Thao tác thay đổi cấu hình quyền của Omnichat áp `BR-25.2`.

  **Lý do nghiệp vụ:** người đọc về sau hiểu nhầm một mốc chưa từng xảy ra còn tệ hơn biết là đang thiếu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-15.1.1` | Agent thêm ghi chú nội bộ | Khách xem hội thoại trên mọi kênh, gồm widget | Không thấy ghi chú dưới bất kỳ dạng nào |
| `AC-15.2.1` | Đã có ghi chú ghim | Ghim ghi chú thứ hai | Ghi chú thứ hai thay ghi chú cũ ở vị trí ghim |
| `AC-15.3.1` | Agent đã thả "thích" | Thả "tim" lên cùng tin | Chỉ còn "tim" |
| `AC-15.4.1` | Hội thoại được chuyển hàng đợi rồi trả về hàng đợi | Mở dòng thời gian | Thấy cả hai mốc theo đúng thứ tự, kèm người thực hiện và lý do |
| `AC-15.4.2` | Bot bàn giao rồi hệ thống tự đóng | Mở dòng thời gian | Phân biệt rõ mốc của Bot và mốc của hệ thống |
| `AC-15.5.1` | Một mốc không ghi được | Mở dòng thời gian | Thấy dấu "thiếu dữ liệu" tại đúng vị trí; đội vận hành nhận cảnh báo |

**Tham chiếu:** `BR-15.5` → issue [#65](https://github.com/crmsaassaudi/product-management/issues/65).

---

### FEAT-16 — Tìm kiếm nội dung tin nhắn

**Mô tả nghiệp vụ:** Cho phép tìm lại một tin nhắn cụ thể trong một hội thoại hoặc của một khách hàng, và rà soát theo từ khóa trên toàn bộ phạm vi người tìm được phép xem.

**Vai trò sử dụng chính:** Agent; Trưởng nhóm; Giám sát viên; Chuyên viên chất lượng.

**Điều kiện tiên quyết:** (Hội thoại, Xem) khác Không có.

**Luồng chính:**

1. Nhập từ khóa, chọn phạm vi (hội thoại này, khách hàng này, toàn bộ phạm vi của tôi).
2. Hệ thống trả kết quả kèm đoạn trích.

**Quy tắc nghiệp vụ:**

- **`BR-16.1` (Phạm vi tìm kiếm theo mức Xem):** Phạm vi tìm kiếm PHẢI đi theo đúng mức (Hội thoại, Xem) của người tìm cộng các nguồn nới phạm vi ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`), không theo giới hạn cố định. Kết quả PHẢI không bao giờ chứa hội thoại ngoài phạm vi đó; hội thoại chưa có người phụ trách mà người tìm chỉ thấy qua hàng đợi chỉ được tìm trên trích đoạn tóm tắt (`BR-05.7`), trừ khi người đó có quyền xem toàn văn (`BR-23.9`).

  **Lý do nghiệp vụ:** rà mọi hội thoại nhắc tới một sự cố sản phẩm là nhu cầu có thật; nhưng tìm kiếm là cách nhanh nhất để đọc nội dung ngoài phạm vi nếu không bị giới hạn.

- **`BR-16.2` (Đoạn trích kết quả):** Kết quả PHẢI hiển thị đoạn trích ngắn quanh từ khóa, đã áp mẫu che của `BR-23.9`.

  **Lý do nghiệp vụ:** người tìm cần nhận ra đúng ngữ cảnh trước khi mở; đoạn trích không được là đường xem dữ liệu nhạy cảm.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-16.1.1` | A có (Hội thoại, Xem) = Đơn vị của mình | Tìm "lỗi thanh toán" trong phạm vi của tôi | Chỉ có hội thoại thuộc đơn vị của A và hội thoại A phụ trách |
| `AC-16.1.2` | Giám sát viên có (Hội thoại, Xem) = Đơn vị và các đơn vị con | Tìm "khuyến mãi tháng 9" | Thấy hội thoại của mọi đội trong nhánh, không thấy đội ngoài nhánh |
| `AC-16.1.3` | Hội thoại chưa có người phụ trách chỉ nhắc từ khóa ở tin thứ năm; A chỉ thấy qua hàng đợi | A tìm từ khóa đó | Hội thoại không có trong kết quả của A |
| `AC-16.2.1` | Tin chứa số điện thoại khách | Tìm theo một từ trong tin | Đoạn trích hiện số điện thoại đã che theo mẫu |

**Tham chiếu:** `BR-16.1` → issue [#66](https://github.com/crmsaassaudi/product-management/issues/66).

---

### FEAT-17 — Quản lý hộp thư (Inbox)

**Mô tả nghiệp vụ:** Cho phép gộp nhiều kênh cùng đơn vị tiếp nhận vào một Hộp thư — một hàng đợi chung — với tập Agent đủ điều kiện và chính sách riêng (phân công, SLA, Bot) khác mặc định toàn doanh nghiệp.

**Vai trò sử dụng chính:** Người có quyền quản trị Quản lý Hộp thư & định tuyến.

**Điều kiện tiên quyết:** Các kênh muốn gộp đã có đơn vị tiếp nhận hội thoại.

**Luồng chính:**

1. Tạo Hộp thư, chọn các kênh (cùng đơn vị tiếp nhận).
2. Chọn tập Agent/nhóm xử lý được phân công.
3. Tùy chọn quy tắc phân công, Chính sách SLA, cấu hình Bot riêng.

**Quy tắc nghiệp vụ:**

- **`BR-17.1` (Tập Agent được phân công):** Một Hộp thư PHẢI gắn được với một hoặc nhiều nhóm xử lý/Agent được **phân công**; chỉ chọn được thành viên của đơn vị tiếp nhận của Hộp thư (hoặc của nhánh khi bật `BR-05.13`). Việc gắn này chỉ quyết định ai được phân công và mời nhận việc (`BR-04.1` (e)), KHÔNG quyết định ai được xem hội thoại — quyền xem đi theo mức (Hội thoại, Xem) và nguồn nới hàng đợi.

  **Lý do nghiệp vụ:** để danh sách phân công đồng thời là danh sách quyền xem thì mỗi lần chỉnh định tuyến là một lần đổi quyền ngoài mô hình ô × mức.

- **`BR-17.2` (Cấu hình riêng):** Một Hộp thư CÓ THỂ chỉ định riêng quy tắc phân công, Chính sách SLA và cấu hình Bot; không chỉ định thì dùng mặc định.

  **Lý do nghiệp vụ:** doanh nghiệp nhiều dòng sản phẩm cần mỗi đội vận hành theo cách riêng mà không tách hệ thống.

- **`BR-17.3` (Không xác định được cấu hình riêng):** Nếu tại một thời điểm hệ thống không xác định được cấu hình riêng của Hộp thư, hội thoại vẫn PHẢI được tiếp nhận theo cấu hình mặc định; nhưng PHẢI được đánh dấu là đang chạy theo mặc định, PHẢI cảnh báo Người phụ trách đơn vị tiếp nhận, và PHẢI được áp lại cấu hình riêng (gồm SLA) ngay khi xác định lại được. Đơn vị tiếp nhận của hội thoại không bao giờ bị thay theo mặc định trong tình huống này.

  **Lý do nghiệp vụ:** hội thoại Hộp thư ưu tiên bị phục vụ theo cam kết mặc định mà không ai biết là sự cố; còn rơi về một đơn vị mặc định là lộ dữ liệu.

- **`BR-17.4` (Hộp thư thuộc một đơn vị tiếp nhận):** Một Hộp thư thuộc đúng một đơn vị tiếp nhận và chỉ gộp được các kênh có cùng đơn vị tiếp nhận hội thoại với nó. Thêm một kênh khác đơn vị vào Hộp thư bị từ chối; muốn gộp thì Người có toàn quyền phải đổi đơn vị tiếp nhận của kênh trước ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.12`).

  **Lý do nghiệp vụ:** một hàng đợi gộp kênh của hai đơn vị sẽ không trả lời được hội thoại của nó thuộc về ai, và người cấu hình Hộp thư sẽ thành người đổi đơn vị tiếp nhận mà không có thẩm quyền.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-17.1.1` | Hộp thư "VIP" chỉ gán nhóm xử lý "VIP" | Hội thoại mới vào Hộp thư | Chỉ thành viên nhóm "VIP" được mời nhận |
| `AC-17.1.2` | A thuộc đơn vị tiếp nhận, (Hội thoại, Xem) = Đơn vị của mình, không thuộc nhóm "VIP" | A mở danh sách hội thoại | A vẫn thấy hội thoại của Hộp thư "VIP" theo mức Xem, nhưng không được mời nhận |
| `AC-17.1.3` | Quản trị viên thêm một Agent ngoài đơn vị tiếp nhận vào Hộp thư | Chọn Agent | Agent đó không có trong danh sách chọn |
| `AC-17.2.1` | Hộp thư có Chính sách SLA riêng | Hội thoại thuộc Hộp thư | Áp chính sách riêng |
| `AC-17.3.1` | Không xác định được cấu hình riêng của Hộp thư ưu tiên | Hội thoại mới | Hội thoại được tiếp nhận, đánh dấu "đang chạy theo mặc định"; Người phụ trách đơn vị nhận cảnh báo; khi xác định lại được, cam kết đúng được áp lại |
| `AC-17.4.1` | Hộp thư thuộc "Hỗ trợ – Hà Nội" | Thêm kênh có đơn vị tiếp nhận "Hỗ trợ – Đà Nẵng" | Từ chối, nêu kênh khác đơn vị tiếp nhận |

**Tham chiếu:** `BR-17.3` → issue [#67](https://github.com/crmsaassaudi/product-management/issues/67).

---

### FEAT-18 — Mẫu tin nhắn nhanh (Canned Response)

**Mô tả nghiệp vụ:** Cho phép soạn sẵn các nội dung trả lời hay dùng và chèn nhanh vào hội thoại bằng một lệnh gõ tắt.

**Vai trò sử dụng chính:** Agent (mẫu riêng); Người có quyền quản trị Quản lý mẫu tin nhắn dùng chung.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Tạo mẫu, chọn phạm vi hiển thị, gán lệnh gõ tắt.
2. Khi soạn tin, gõ lệnh tắt để chèn mẫu.

**Quy tắc nghiệp vụ:**

- **`BR-18.1` (Phạm vi hiển thị):** Mẫu tin nhắn PHẢI có phạm vi hiển thị: chỉ riêng người tạo, dùng chung cho một đơn vị (hoặc nhánh), hoặc dùng chung toàn workspace. Người tạo mẫu dùng chung chỉ chọn được đơn vị nằm trong phạm vi quản trị của mình; toàn workspace cần quyền quản trị Quản lý mẫu tin nhắn dùng chung.

  **Lý do nghiệp vụ:** mẫu của đội khiếu nại chứa hướng dẫn nội bộ không dành cho đội bán hàng.

- **`BR-18.2` (Lệnh gõ tắt):** Mẫu CÓ THỂ gán một lệnh gõ tắt ngắn.

  **Lý do nghiệp vụ:** chèn mẫu bằng chuột chậm hơn gõ lại.

- **`BR-18.3` (Ai sửa được):** Mẫu riêng chỉ người tạo sửa/xóa được; mẫu dùng chung chỉ người có quyền quản trị Quản lý mẫu tin nhắn dùng chung trong phạm vi của mẫu sửa/xóa được.

  **Lý do nghiệp vụ:** một mẫu dùng chung bị một người sửa sai sẽ gửi sai thông tin tới khách của cả đội.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-18.1.1` | Mẫu dùng chung cho "Khiếu nại" | Agent đội "Bán hàng" tìm mẫu | Không thấy mẫu đó |
| `AC-18.2.1` | Mẫu có lệnh tắt "/doitra" | Agent gõ "/doitra" | Nội dung mẫu được chèn vào ô soạn |
| `AC-18.3.1` | Agent không có quyền quản trị mẫu dùng chung | Mở mẫu dùng chung toàn workspace | Chỉ dùng được, không sửa/xóa được |

---

### FEAT-19 — Báo cáo vận hành Omnichannel

**Mô tả nghiệp vụ:** Cung cấp bức tranh tổng thể về khối lượng, tốc độ và chất lượng xử lý hội thoại trong phạm vi người xem, theo khoảng thời gian và bộ lọc tùy chọn.

**Vai trò sử dụng chính:** Trưởng nhóm; Giám sát viên; Quản lý; mọi người có (Hội thoại, Xem) khác Không có (trong phạm vi của mình).

**Điều kiện tiên quyết:** Không có.

**Nội dung báo cáo:** khối lượng hội thoại theo thời gian; tỷ lệ theo kênh; tốc độ phản hồi/giải quyết và tỷ lệ đáp ứng SLA; phân bố theo trạng thái và lý do xử lý; khối lượng tin theo loại/chiều/kênh; hiệu quả Bot; giờ cao điểm; phân tích theo nhãn; tỷ lệ mở lại; tỷ lệ khách bỏ cuộc; tỷ lệ chuyển tiếp, chuyển hàng đợi và lý do; số hội thoại được trả về hàng đợi.

**Luồng chính:**

1. Mở báo cáo, chọn khoảng thời gian và bộ lọc (kênh, Agent, Hộp thư, đơn vị).
2. Xem số liệu; bấm xuyên xuống danh sách hội thoại.

**Quy tắc nghiệp vụ:**

- **`BR-19.1` (Loại trừ hội thoại chưa phản hồi):** Thời gian phản hồi lần đầu trung bình PHẢI loại trừ hội thoại chưa từng được phản hồi.

  **Lý do nghiệp vụ:** tính các hội thoại đó là "0 phút" làm số liệu đẹp hơn thực tế.

- **`BR-19.2` (Số liệu theo phạm vi người xem):** Mọi số liệu PHẢI chỉ tổng hợp trên hội thoại nằm trong phạm vi mức (Hội thoại, Xem) của người xem cộng các nguồn nới phạm vi ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`), và tôn trọng lượt chặn trên bản ghi ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.3`).

  **Lý do nghiệp vụ:** số liệu tổng hợp cũng là dữ liệu; một Trưởng nhóm không được biết khối lượng của đội khác qua báo cáo khi không được xem hội thoại của đội đó.

- **`BR-19.3` (Có CSAT):** Báo cáo PHẢI có CSAT trung bình (`BR-14.4`), dùng chung bộ lọc.

  **Lý do nghiệp vụ:** xem khối lượng mà không xem chất lượng là nhìn một nửa bức tranh.

- **`BR-19.4` (Bỏ cuộc và chuyển tiếp):** Báo cáo PHẢI có **tỷ lệ khách bỏ cuộc** (`BR-05.10`), **tỷ lệ chuyển tiếp** (`BR-07.9`) và **tỷ lệ chuyển hàng đợi** (`BR-05.15`).

  **Lý do nghiệp vụ:** tỷ lệ đáp ứng SLA chỉ nói về khách đã được phục vụ; tỷ lệ chuyển hàng đợi cao là dấu hiệu đơn vị tiếp nhận của kênh đặt sai.

- **`BR-19.5` (Bấm xuyên xuống):** Mọi con số PHẢI **bấm xuyên xuống được** ra danh sách hội thoại tạo nên nó, trong cùng phạm vi của `BR-19.2`, với nội dung hiển thị theo `BR-23.9`.

  **Lý do nghiệp vụ:** thao tác thường xuyên nhất của Giám sát viên là mở xem những hội thoại sau con số.

- **`BR-19.6` (Xuất và gửi định kỳ):** Báo cáo PHẢI xuất được ra tệp và gửi định kỳ tới người nhận do doanh nghiệp cấu hình; người đặt lịch cần quyền quản trị Gửi báo cáo định kỳ. Tệp và bản gửi PHẢI tuân đúng phạm vi của **từng người nhận**, không theo phạm vi của người đặt lịch.

  **Lý do nghiệp vụ:** bản gửi định kỳ là đường vòng phổ biến nhất để số liệu tới tay người không được xem.

- **`BR-19.7` (Theo Hộp thư, đơn vị, kỳ trước):** Báo cáo PHẢI xem được theo **Hộp thư, đơn vị tiếp nhận và nhóm xử lý**, không chỉ theo kênh và Agent, và PHẢI so sánh được với kỳ trước.

  **Lý do nghiệp vụ:** đơn vị tiếp nhận là nơi trách nhiệm được chia; không báo cáo theo nó thì không quy được trách nhiệm.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-19.1.1` | 10 hội thoại, 2 chưa từng được phản hồi | Xem thời gian phản hồi lần đầu trung bình | Tính trên 8 hội thoại |
| `AC-19.2.1` | Trưởng nhóm Hà Nội có (Hội thoại, Xem) = Đơn vị và các đơn vị con | Mở báo cáo | Chỉ có số liệu của "Hỗ trợ – Hà Nội" và đơn vị con |
| `AC-19.2.2` | Một hội thoại có lượt chặn áp lên người xem | Mở báo cáo | Hội thoại đó không được tính |
| `AC-19.3.1` | Đổi bộ lọc kênh | Xem báo cáo | CSAT đổi theo cùng bộ lọc |
| `AC-19.4.1` | Tuần có 5 lần khách bỏ cuộc và 12 lượt chuyển hàng đợi | Xem báo cáo | Hiện tỷ lệ bỏ cuộc và tỷ lệ chuyển hàng đợi kèm lý do phổ biến |
| `AC-19.5.1` | Con số "hội thoại vi phạm SLA" = 7 | Bấm vào con số | Mở đúng 7 hội thoại trong phạm vi người xem |
| `AC-19.6.1` | Giám sát viên đặt lịch gửi báo cáo tuần cho một Trưởng nhóm có phạm vi hẹp hơn | Tới lịch gửi | Trưởng nhóm nhận bản chỉ chứa số liệu trong phạm vi của Trưởng nhóm |
| `AC-19.6.2` | Người không có quyền Gửi báo cáo định kỳ | Tìm thao tác đặt lịch | Không khả dụng |
| `AC-19.7.1` | Chọn xem theo đơn vị tiếp nhận, so sánh với tuần trước | Xem | Hai kỳ hiển thị cạnh nhau theo từng đơn vị |

**Tham chiếu:** `BR-19.3` → issue [#21](https://github.com/crmsaassaudi/product-management/issues/21). `BR-19.4`, `BR-19.5`, `BR-19.6`, `BR-19.7` → issue [#77](https://github.com/crmsaassaudi/product-management/issues/77).

---

### FEAT-20 — Báo cáo hiệu suất Agent

**Mô tả nghiệp vụ:** Đo năng suất và chất lượng làm việc của từng Agent, phục vụ hoạch định nhân sự và đánh giá công bằng.

**Vai trò sử dụng chính:** Trưởng nhóm; Giám sát viên; Agent (xem số liệu của chính mình).

**Điều kiện tiên quyết:** Không có.

**Nội dung báo cáo:** thời gian ở mỗi trạng thái làm việc, mức tuân thủ ca (`FEAT-28`), AHT, tỷ lệ bận việc so với trực tuyến, số hội thoại đã xử lý, điểm chất lượng (`FEAT-27`), điểm CSAT, bảng xếp hạng tổng hợp.

**Luồng chính:**

1. Mở báo cáo, chọn kỳ và đội.
2. Xem chỉ số từng Agent và bảng xếp hạng.

**Quy tắc nghiệp vụ:**

- **`BR-20.1` (Nhóm theo Thuộc tính kênh):** Báo cáo PHẢI tổng hợp năng suất theo nhóm phân định bằng Thuộc tính kênh (`BR-01.8`): **đồng thời** và **không đồng thời**. Một kênh mới tự rơi vào đúng nhóm. Năng suất các đối tượng khác của CRM do SRS tương ứng đóng góp.

  **Lý do nghiệp vụ:** danh sách tên kênh thay đổi, bản chất đồng thời/không đồng thời thì không.

- **`BR-20.2` (AHT tách theo Thuộc tính kênh):** AHT PHẢI tách riêng **AHT trò chuyện đồng thời** và **AHT trao đổi không đồng thời**, KHÔNG ĐƯỢC gộp thành một con số; xem được chi tiết tới từng kênh trong mỗi nhóm; và PHẢI tính cả **Xử lý sau hội thoại** (`BR-06.5`).

  **Lý do nghiệp vụ:** gộp hai nhóm vài phút và vài giờ cho ra một trung bình không dùng được để hoạch định nhân sự — mục đích duy nhất của chỉ số này.

- **`BR-20.3` (CSAT trong bảng hiệu suất):** CSAT trung bình của từng Agent (`BR-14.5`) PHẢI xuất hiện cạnh các chỉ số tốc độ.

  **Lý do nghiệp vụ:** năng suất luôn phải đi kèm chất lượng.

- **`BR-20.4` (Loại người quá ít dữ liệu):** Bảng xếp hạng PHẢI loại người có quá ít thời gian trực tuyến hoặc quá ít việc trong kỳ (`CFG-20-01`).

  **Lý do nghiệp vụ:** xếp hạng trên vài hội thoại là xếp hạng ngẫu nhiên.

- **`BR-20.5` (Xếp hạng ba mặt):** Bảng xếp hạng KHÔNG ĐƯỢC xây chỉ trên tốc độ; một Agent chỉ được xếp hạng khi có đủ khối lượng, chất lượng (điểm chấm hoặc CSAT) và tuân thủ ca.

  **Lý do nghiệp vụ:** xếp hạng theo tốc độ thưởng cho đóng vội và phạt người nhận việc khó.

- **`BR-20.6` (Phạm vi người xem và người được đo):** Người xem chỉ thấy chỉ số của những Agent mà số liệu được tổng hợp từ hội thoại trong phạm vi (Hội thoại, Xem) của mình (`BR-19.2`); mỗi Agent luôn xem được chỉ số của chính mình. Hội thoại có can thiệp của người khác (`BR-26.7`) được đánh dấu tách riêng trong chỉ số của Agent.

  **Lý do nghiệp vụ:** chỉ số hiệu suất là dữ liệu về con người; còn tính kết quả can thiệp của Trưởng nhóm vào thành tích của Agent làm chỉ số mất ý nghĩa.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-20.1.1` | Doanh nghiệp thêm kênh mới khai báo "đồng thời" | Xem báo cáo kỳ sau | Kênh mới nằm trong nhóm đồng thời mà không cần cấu hình báo cáo |
| `AC-20.2.1` | Agent xử lý cả Live Chat và email | Xem AHT | Hai con số AHT riêng; mở được chi tiết từng kênh; AHT có tính Xử lý sau hội thoại |
| `AC-20.3.1` | Mở bảng hiệu suất | Xem | Cột CSAT và điểm chất lượng nằm cạnh tốc độ |
| `AC-20.4.1` | Ngưỡng tối thiểu 20 hội thoại | Agent mới xử lý 5 hội thoại trong kỳ | Không có trong bảng xếp hạng |
| `AC-20.5.1` | Agent nhanh nhất có điểm chất lượng thấp | Xem bảng xếp hạng | Agent đó không đứng đầu |
| `AC-20.5.2` | Agent chưa có điểm chấm và CSAT nào trong kỳ | Xem bảng xếp hạng | Agent đó không được xếp hạng, ghi "chưa đủ dữ liệu chất lượng" |
| `AC-20.6.1` | Agent A mở báo cáo | Xem | A thấy chỉ số của chính mình dù (Hội thoại, Xem) = Chỉ của mình |
| `AC-20.6.2` | Trưởng nhóm đã tiếp quản một hội thoại của A | Xem chỉ số của A | Hội thoại đó được đánh dấu "có can thiệp", tách khỏi kết quả tự làm |

**Tham chiếu:** `BR-20.3` → issue [#23](https://github.com/crmsaassaudi/product-management/issues/23). `BR-20.1`, `BR-20.2`, `BR-20.5` → issue [#77](https://github.com/crmsaassaudi/product-management/issues/77) — thay cho issue [#22](https://github.com/crmsaassaudi/product-management/issues/22).

---

### FEAT-21 — Giao diện xử lý hội thoại của Agent

**Mô tả nghiệp vụ:** Không gian làm việc chính của Agent — xem danh sách hội thoại, trò chuyện với khách, và thao tác mọi nghiệp vụ — được thiết kế để xử lý nhanh, không bỏ sót, và luôn biết trạng thái từng hội thoại.

**Vai trò sử dụng chính:** Agent.

**Điều kiện tiên quyết:** (Hội thoại, Xem) khác Không có.

**Luồng chính:**

1. Agent đăng nhập, thấy danh sách hội thoại lọc theo phạm vi (của tôi, hàng đợi của đơn vị tôi, theo kênh...).
2. Mở một hội thoại để xem lịch sử, thông tin khách liên quan (theo quyền đọc tự động của [`contacts-srs.md`](./contacts-srs.md) `BR-35.4`).
3. Soạn và gửi tin, chèn mẫu, thả biểu tượng, hoặc thao tác quản lý hội thoại.

**Quy tắc nghiệp vụ:**

- **`BR-21.1` (Bộ lọc):** Agent PHẢI lọc được theo: trạng thái, người/nhóm được gán, hàng đợi, đơn vị tiếp nhận, kênh, nhãn, phân khúc, có tin chưa đọc, đang/sắp vi phạm SLA, khoảng thời gian; và PHẢI lưu được bộ lọc thường dùng. Bộ lọc chỉ thu hẹp trong phạm vi được phép, không mở rộng.

  **Lý do nghiệp vụ:** Agent xử lý hàng chục hội thoại mỗi ca cần tìm đúng việc gấp trong vài giây.

- **`BR-21.2` (Không biến mất giữa chừng):** Hội thoại Agent đang xem hoặc đang soạn dở PHẢI luôn hiển thị trong danh sách của Agent, kể cả khi vừa đổi trạng thái khiến nó không còn khớp bộ lọc — trừ khi Agent vừa mất quyền xem nó (chuyển hàng đợi sang đơn vị khác, bị chặn), khi đó hội thoại đóng lại kèm thông báo lý do.

  **Lý do nghiệp vụ:** hội thoại biến mất giữa lúc thao tác làm Agent tưởng mất dữ liệu; nhưng thu hồi quyền phải có hiệu lực ngay.

- **`BR-21.3` (Phím tắt):** Agent PHẢI thao tác được nghiệp vụ chính (nhận, chuyển tiếp, giải quyết, tiếp quản) bằng phím tắt.

  **Lý do nghiệp vụ:** thao tác lặp lại hàng trăm lần một ngày bằng chuột là lãng phí có hệ thống.

- **`BR-21.4` (Ô soạn bị khóa có lý do):** Ô soạn tin PHẢI bị khóa và hiển thị rõ lý do kèm cách xử lý tiếp khi: hết Cửa sổ phản hồi (`BR-12.9`), hội thoại Đã đóng, Agent Ngoại tuyến, Agent không có (Hội thoại, Sửa) bao phủ hội thoại, hoặc hội thoại chưa có người phụ trách mà Agent không nhận được việc (`BR-12.5`). Người khác đang soạn KHÔNG khóa ô soạn (`BR-13.4`).

  **Lý do nghiệp vụ:** Agent phải biết ngay lý do, không đoán mò.

- **`BR-21.5` (Ghi chú giải quyết bắt buộc):** Doanh nghiệp CÓ THỂ yêu cầu bắt buộc ghi lý do/ghi chú khi giải quyết (`CFG-21-01`).

  **Lý do nghiệp vụ:** báo cáo nguyên nhân liên hệ cần dữ liệu đầy đủ.

- **`BR-21.6` (Âm thanh thông báo):** Hệ thống PHẢI phát âm thanh khi có tin mới trong lúc Agent không đang xem đúng hội thoại đó hoặc không đang mở cửa sổ làm việc.

  **Lý do nghiệp vụ:** Agent làm nhiều việc cùng lúc không nhìn màn hình liên tục.

- **`BR-21.7` (Lời mời có đếm ngược):** Lời mời nhận hội thoại PHẢI hiển thị rõ với thời gian đếm ngược.

  **Lý do nghiệp vụ:** Agent không biết còn bao lâu sẽ để lỡ lời mời.

- **`BR-21.8` (Liên kết hồ sơ):** Agent CÓ THỂ tìm và liên kết/gộp hội thoại của Hồ sơ khách hàng tạm với một khách hàng có sẵn trong phạm vi được thấy (`BR-02.4`), gỡ lại một lần gộp sai (`BR-02.8`), và đánh dấu Định danh dùng chung (`BR-02.4b`).

  **Lý do nghiệp vụ:** nhận diện khách là việc Agent làm giữa cuộc trò chuyện, không phải ở một màn hình khác.

- **`BR-21.9` (Tóm tắt hàng đợi):** Với hàng đợi của đơn vị mình thuộc, hệ thống PHẢI hiển thị số khách đang chờ và thời gian chờ lâu nhất.

  **Lý do nghiệp vụ:** Agent rảnh tay cần biết có việc đang chờ để chủ động nhận.

- **`BR-21.10` (Giữ nội dung đang soạn):** Nội dung đang soạn dở PHẢI được giữ cho tới khi Agent gửi hoặc tự xóa, kể cả khi chuyển hội thoại, đóng màn hình hoặc gián đoạn kết nối.

  **Lý do nghiệp vụ:** mất một đoạn trả lời dài là thiệt hại năng suất trực tiếp và khách phải chờ lại.

- **`BR-21.11` (Thao tác hàng loạt):** Agent PHẢI thao tác hàng loạt được trên nhiều hội thoại đã chọn — tối thiểu: gắn nhãn, chuyển tiếp, giải quyết kèm lý do xử lý. Mỗi hội thoại trong lô chịu đúng kiểm tra quyền như thao tác đơn lẻ; hội thoại không đủ quyền bị loại khỏi lô. Màn hình PHẢI hiển thị trước số hội thoại sẽ bị tác động và số bị loại kèm lý do, và PHẢI để vết theo `BR-15.4`.

  **Lý do nghiệp vụ:** sau cao điểm Agent có thể phải đóng hàng chục hội thoại cùng một kết luận; nhưng thao tác hàng loạt không được là đường vòng vượt quyền.

- **`BR-21.12` (Tạo vé từ hội thoại):** Người có (Hội thoại, Sửa) bao phủ hội thoại và (Vé hỗ trợ, Tạo) = Có PHẢI tạo được một vé hỗ trợ từ hội thoại. Vé thuộc loại nguồn **"Vé từ hội thoại"** theo [`tickets-srs.md`](./tickets-srs.md) `BR-34.2`: hàng đợi mặc định lấy theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-35-01` cho loại nguồn đó, tìm từ **đơn vị tiếp nhận hiện tại của hội thoại** (sau mọi lượt chuyển hàng đợi, `BR-05.15`) đi lên tới đơn vị gốc, lấy giá trị gần nhất; không tìm thấy thì thao tác tạo vé bị chặn kèm giải thích cần Người có toàn quyền khai báo. Người tạo chỉ chọn được hàng đợi khác trong danh sách được chuyển tới của hàng đợi mặc định đó theo phân hệ Vé hỗ trợ. Nội dung chép sang vé dùng **dạng che hạn chế nhất** của `BR-23.9`, bất kể quyền của người tạo: số điện thoại, email, định danh kênh theo mẫu che mặc định; dữ liệu nhạy cảm khách gõ vào ở dạng ký hiệu che; bản gốc không bao giờ được chép và không mở lại được trên vé, nhất quán với [`tickets-srs.md`](./tickets-srs.md) `BR-45.12`. Hội thoại và vé liên kết hai chiều, lượt tạo ghi vào dòng thời gian (`BR-15.4`). Tạo vé không đổi Người phụ trách hay đơn vị của hội thoại.

  **Lý do nghiệp vụ:** việc vượt quá một cuộc trò chuyện (bảo hành, khiếu nại cần xử lý nhiều ngày) phải sang đúng đội đang chịu trách nhiệm hội thoại, không về đơn vị gốc của kênh mà hội thoại đã rời; còn chép bản gốc dữ liệu nhạy cảm sang vé là mở đường đọc mà `BR-23.9` đã đóng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-21.1.1` | Agent lưu bộ lọc "Đang vi phạm SLA của tôi" | Mở lại hôm sau | Bộ lọc còn và cho đúng kết quả |
| `AC-21.2.1` | Agent đang soạn dở một hội thoại vừa chuyển sang Tạm hoãn, bộ lọc "Đang mở" | Nhìn danh sách | Hội thoại vẫn hiện tới khi Agent rời đi |
| `AC-21.2.2` | Agent đang xem một hội thoại vừa bị chuyển hàng đợi sang đơn vị Agent không thuộc | Chuyển hàng đợi có hiệu lực | Hội thoại đóng lại trên màn hình Agent kèm "Hội thoại đã được chuyển sang đơn vị khác" |
| `AC-21.3.1` | Agent dùng phím tắt "giải quyết" | Nhấn phím | Hội thoại được giải quyết như bấm nút |
| `AC-21.4.1` | Agent có (Hội thoại, Sửa) = Chỉ của mình, mở hội thoại do người khác phụ trách | Nhìn ô soạn | Ô soạn bị khóa kèm "Bạn không phụ trách hội thoại này" |
| `AC-21.5.1` | Ghi chú giải quyết bắt buộc | Bấm giải quyết không ghi chú | Nút xác nhận bị vô hiệu tới khi có ghi chú |
| `AC-21.6.1` | Agent đang ở hội thoại khác | Tin mới tới hội thoại A | Có âm thanh thông báo |
| `AC-21.7.1` | Có lời mời mới | Nhìn màn hình | Lời mời hiện nổi bật kèm đồng hồ đếm ngược |
| `AC-21.8.1` | Hội thoại của Hồ sơ khách hàng tạm | Agent tìm khách hàng trong phạm vi và liên kết | Hội thoại gắn vào hồ sơ đó |
| `AC-21.9.1` | Hàng đợi của đơn vị Agent có 4 khách chờ, lâu nhất 6 phút | Agent nhìn màn hình | Thấy "4 đang chờ · lâu nhất 6 phút" |
| `AC-21.10.1` | Agent soạn dở rồi chuyển sang hội thoại khác | Quay lại | Nội dung soạn dở còn nguyên |
| `AC-21.11.1` | Agent chọn 30 hội thoại, 2 trong số đó không do mình phụ trách | Giải quyết hàng loạt với cùng lý do | Màn hình báo trước "28 sẽ được giải quyết, 2 bị loại: không có quyền"; 28 được giải quyết, mỗi cái có vết |
| `AC-21.12.1` | Hội thoại từ Zalo OA Hà Nội đã được chuyển sang "Hỗ trợ – Đà Nẵng"; chi nhánh Đà Nẵng khai báo mặc định "Vé từ hội thoại" = "Hỗ trợ – Đà Nẵng" | Agent Đà Nẵng tạo vé từ hội thoại | Hàng đợi chọn sẵn của vé là "Hỗ trợ – Đà Nẵng"; vé và hội thoại liên kết hai chiều |
| `AC-21.12.2` | Người tạo là Người phụ trách, đang thấy số điện thoại khách đầy đủ; hội thoại chứa số thẻ đã che | Tạo vé | Nội dung trên vé giữ "[số thẻ đã che]" và số điện thoại theo mẫu che mặc định (4 số cuối), không theo mức thấy của người tạo |
| `AC-21.12.3` | Agent có (Vé hỗ trợ, Tạo) = Không có | Tìm thao tác tạo vé | Thao tác bị vô hiệu kèm lý do |
| `AC-21.12.4` | Không đơn vị nào từ đơn vị hiện tại của hội thoại tới gốc, và workspace, khai báo mặc định "Vé từ hội thoại" | Agent bấm tạo vé | Thao tác bị chặn kèm giải thích cần Người có toàn quyền khai báo đơn vị tiếp nhận cho loại nguồn này |

**Tham chiếu:** `BR-21.10`, `BR-21.11` → issue [#78](https://github.com/crmsaassaudi/product-management/issues/78).

---

### FEAT-22 — Trải nghiệm trò chuyện của khách hàng trên Live Chat Widget

**Mô tả nghiệp vụ:** Cung cấp khung chat nhúng trên website doanh nghiệp để khách truy cập trò chuyện với Agent (hoặc Bot) mà không cần rời trang hay cài ứng dụng.

**Vai trò sử dụng chính:** Khách truy cập website.

**Điều kiện tiên quyết:** Widget đã được nhúng và kênh Live Chat có đơn vị tiếp nhận.

**Luồng chính:**

1. Khách thấy nút mở khung chat.
2. Mở khung chat, (tùy cấu hình) điền thông tin cơ bản.
3. Gửi tin, nhận phản hồi theo thời gian thực; gửi kèm hình ảnh/tệp/ghi âm.
4. Khách quay lại (kể cả lần sau, đổi trang) vẫn tiếp tục đúng hội thoại đang dở.

**Quy tắc nghiệp vụ:**

- **`BR-22.1` (Nhận ra khách quay lại):** Widget NÊN nhận ra lại đúng khách khi họ quay lại trên cùng thiết bị và trình duyệt. Việc ghi nhớ PHẢI được thông báo cho khách theo [`contacts-srs.md`](./contacts-srs.md) `BR-01.1c`, và khách PHẢI tự kết thúc phiên và xóa lịch sử khỏi thiết bị được (`BR-23.6`). Khi không nhận ra lại được, widget PHẢI cho khách tự nối lại hội thoại cũ bằng email hoặc số điện thoại đã cung cấp, sau khi xác minh khách thực sự sở hữu định danh đó.

  **Lý do nghiệp vụ:** khách phải kể lại từ đầu mỗi lần đổi thiết bị là lý do họ bỏ kênh chat; nhưng nối lại chỉ bằng việc gõ một email là để người lạ đọc lịch sử của người khác.

- **`BR-22.2` (Trạng thái gửi):** Widget PHẢI hiển thị trạng thái gửi của từng tin và cho phép gửi lại khi thất bại.

  **Lý do nghiệp vụ:** khách không biết tin đã đi chưa sẽ gửi lại nhiều lần.

- **`BR-22.3` (Thông tin trước khi chat):** Widget CÓ THỂ yêu cầu khách điền thông tin trước khi chat lần đầu (`CFG-22-01`).

  **Lý do nghiệp vụ:** biết tên và nhu cầu trước giúp định tuyến đúng ngay từ đầu.

- **`BR-22.4` (Báo ngoài giờ):** Ngoài giờ làm việc, widget PHẢI báo rõ hiện không có ai trực tuyến.

  **Lý do nghiệp vụ:** khách chờ một người không có mặt là trải nghiệm tệ hơn biết trước để để lại yêu cầu.

- **`BR-22.5` (Kết thúc rõ ràng):** Khi hội thoại kết thúc, widget PHẢI báo rõ và cho khách bắt đầu hội thoại mới.

  **Lý do nghiệp vụ:** khách nhắn vào một hội thoại đã kết thúc mà không biết sẽ tưởng bị bỏ rơi.

- **`BR-22.6` (Chấm CSAT tại chỗ):** Widget PHẢI cho khách chấm CSAT ngay tại chỗ.

  **Lý do nghiệp vụ:** bắt khách rời website để chấm điểm làm tỷ lệ phản hồi giảm mạnh.

- **`BR-22.7` (Thiết bị và ngôn ngữ):** Widget PHẢI dùng được trên máy tính và di động, tự thích ứng ngôn ngữ theo trình duyệt.

  **Lý do nghiệp vụ:** phần lớn khách truy cập website từ điện thoại.

- **`BR-22.8` (Thương hiệu):** Doanh nghiệp PHẢI tùy biến được màu sắc, biểu tượng, vị trí, lời chào, ảnh và tên hiển thị của người trả lời mà không cần nhà cung cấp.

  **Lý do nghiệp vụ:** widget mang thương hiệu doanh nghiệp; không tùy biến được thì họ không nhúng.

- **`BR-22.9` (Mời chủ động):** Doanh nghiệp CÓ THỂ cấu hình widget chủ động mời trò chuyện theo hành vi trên website (`CFG-22-02`); lời mời PHẢI giới hạn số lần với cùng một khách và dừng khi khách từ chối.

  **Lý do nghiệp vụ:** lời mời đúng lúc tăng chuyển đổi; lời mời lặp lại gây phiền và làm khách rời trang.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-22.1.1` | Khách quay lại sau vài ngày, cùng thiết bị | Mở widget | Thấy đúng lịch sử cũ |
| `AC-22.1.2` | Khách đổi thiết bị | Nhập email đã cung cấp trước đó và xác minh | Nối lại được hội thoại cũ |
| `AC-22.1.3` | Một người nhập email của người khác | Không xác minh được | Không nối lại; không lộ lịch sử |
| `AC-22.2.1` | Mất mạng khi gửi | Tin thất bại | Hiện "gửi thất bại" và nút gửi lại, nội dung còn nguyên |
| `AC-22.3.1` | Doanh nghiệp bắt buộc tên và email | Khách mở widget lần đầu | Nhập đủ mới bắt đầu chat được |
| `AC-22.4.1` | Ngoài giờ | Khách mở widget | Thấy "Hiện không có ai trực tuyến" và lời mời để lại yêu cầu |
| `AC-22.5.1` | Agent đóng hội thoại | Khách nhìn widget | Thấy thông báo kết thúc và nút bắt đầu hội thoại mới |
| `AC-22.6.1` | Được mời chấm điểm | Khách chấm trong widget | Điểm được ghi nhận, không rời website |
| `AC-22.7.1` | Trình duyệt di động tiếng Anh | Mở widget | Hiển thị đúng trên di động, giao diện tiếng Anh |
| `AC-22.8.1` | Quản trị viên đổi màu và lời chào | Lưu | Widget trên website đổi theo, không cần nhà cung cấp |
| `AC-22.9.1` | Giới hạn 2 lời mời mỗi khách | Khách từ chối lời mời đầu | Không có lời mời nào nữa với khách đó |

**Tham chiếu:** `BR-22.1`, `BR-22.8`, `BR-22.9` → issue [#79](https://github.com/crmsaassaudi/product-management/issues/79).

---

### FEAT-23 — Lưu trữ, xuất, xóa và che dữ liệu hội thoại

**Mô tả nghiệp vụ:** Xác định doanh nghiệp giữ nội dung trao đổi với khách trong bao lâu, ai lấy được nó ra, xóa đi thế nào khi khách hoặc pháp luật yêu cầu, và những dữ liệu nào trong hội thoại được che khi hiển thị. Đây vừa là cam kết của doanh nghiệp với khách của họ, vừa là điều khoản hợp đồng giữa doanh nghiệp và nhà cung cấp; nhiều quy tắc khác (`BR-02.6`, `BR-22.1`) dựa vào cam kết này. Mục này là phần khai báo trường nhạy cảm của loại dữ liệu Hội thoại theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-40`.

**Vai trò sử dụng chính:** Người có quyền quản trị Quản lý lưu trữ dữ liệu hội thoại (cấu hình, Tạm dừng xóa); người có (Hội thoại, Xuất) bao phủ hội thoại (xuất); người thực hiện yêu cầu của chủ thể dữ liệu theo [`contacts-srs.md`](./contacts-srs.md) `FEAT-33`; Khách hàng.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Doanh nghiệp đặt thời hạn lưu trữ và danh mục dữ liệu nhạy cảm cần phát hiện.
2. Hệ thống áp mẫu che khi hiển thị, tìm kiếm, xuất và thông báo.
3. Hết thời hạn hoặc có yêu cầu xóa → dữ liệu được xóa, trừ phần đang Tạm dừng xóa.

**Quy tắc nghiệp vụ:**

- **`BR-23.1` (Thời hạn lưu trữ):** Doanh nghiệp PHẢI cấu hình được **Thời hạn lưu trữ** cho nội dung hội thoại và tệp đính kèm (`CFG-23-01`). Trong thời hạn, nội dung PHẢI xem lại được đầy đủ bởi người có quyền; hết thời hạn, nội dung PHẢI được xóa tự động.

  **Lý do nghiệp vụ:** giữ dữ liệu cá nhân lâu hơn cam kết là rủi ro tuân thủ; xóa sớm hơn là mất bằng chứng.

- **`BR-23.2` (Số liệu tổng hợp tách riêng):** Thời hạn lưu **số liệu tổng hợp** (khối lượng, tốc độ, CSAT) PHẢI tách khỏi thời hạn lưu nội dung (`CFG-23-02`); xóa nội dung không làm mất số liệu lịch sử vận hành.

  **Lý do nghiệp vụ:** doanh nghiệp cần so sánh năng lực phục vụ qua nhiều năm mà không cần giữ nội dung.

- **`BR-23.3` (Xóa theo yêu cầu của khách):** Khi khách yêu cầu xóa dữ liệu cá nhân, doanh nghiệp PHẢI thực hiện được trên toàn bộ hội thoại của khách đó ở mọi kênh và mọi đơn vị tiếp nhận trong một lần thao tác, và PHẢI nhận được kết quả cho biên bản của [`contacts-srs.md`](./contacts-srs.md) `FEAT-33`: xác nhận hoàn tất, hoặc với phần đang Tạm dừng xóa — trạng thái "Đang tạm dừng theo yêu cầu pháp lý" theo `BR-23.7`. Người thực hiện không cần mức Xem bao phủ từng hội thoại; thẩm quyền đi theo quy trình quyền chủ thể dữ liệu của tài liệu đó.

  **Lý do nghiệp vụ:** hội thoại của một khách nằm rải rác ở nhiều đội; bắt người thực hiện phải có quyền xem tất cả là cấp quyền đọc rộng chỉ để xóa.

- **`BR-23.4` (Xuất nội dung):** Người có (Hội thoại, Xuất) bao phủ một hội thoại PHẢI xuất được toàn bộ nội dung ra một tệp đọc được, phục vụ khiếu nại/tranh chấp. Tệp xuất áp mẫu che của `BR-23.9` theo quyền của người xuất; ghi chú nội bộ chỉ được kèm theo nếu người xuất xem được ghi chú nội bộ, và tệp PHẢI ghi rõ có kèm hay không. Mỗi lần xuất ghi vào nhật ký truy cập (`FEAT-35`).

  **Lý do nghiệp vụ:** tệp xuất rời khỏi hệ thống và không còn chịu bất kỳ kiểm soát nào sau đó.

- **`BR-23.5` (Lấy dữ liệu khi ngừng dùng):** Khi doanh nghiệp ngừng sử dụng hệ thống, họ PHẢI lấy được toàn bộ dữ liệu hội thoại trong một khoảng thời gian được thông báo trước (`CFG-23-05`).

  **Lý do nghiệp vụ:** dữ liệu khách hàng là tài sản của doanh nghiệp, không phải của nhà cung cấp.

- **`BR-23.6` (Khách tự xóa trên thiết bị):** Khách truy cập website PHẢI tự kết thúc phiên và xóa lịch sử trò chuyện khỏi thiết bị của mình được, độc lập với dữ liệu doanh nghiệp lưu.

  **Lý do nghiệp vụ:** máy tính dùng chung (thư viện, quán cà phê) không được để người sau đọc cuộc trò chuyện của người trước.

- **`BR-23.7` (Tạm dừng xóa theo yêu cầu pháp lý):** Chỉ **Người có toàn quyền** hoặc **Người phụ trách Bảo vệ Dữ liệu** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-47`) đặt và gỡ được **Tạm dừng xóa theo yêu cầu pháp lý** lên dữ liệu của một hội thoại hoặc một khách hàng, kèm căn cứ và ngày dự kiến gỡ; đây không phải một quyền quản trị cấp được cho người khác. Trong thời gian tạm dừng, `BR-23.1` không xóa phần dữ liệu đó. Khi có yêu cầu xóa theo `BR-23.3` chạm tới phần đang tạm dừng: phần đó không bị xóa; kết quả trả về biên bản của [`contacts-srs.md`](./contacts-srs.md) `FEAT-33` mang trạng thái **"Đang tạm dừng theo yêu cầu pháp lý"** kèm căn cứ và ngày dự kiến gỡ; yêu cầu xóa giữ ở trạng thái **chưa hoàn tất** cho phần đó. Khi gỡ tạm dừng, phần còn lại của mọi yêu cầu xóa đang chờ được thực thi ngay và kết quả cập nhật vào biên bản; thời hạn lưu trữ trở lại hiệu lực ngay. Mỗi lần đặt/gỡ ghi ai, căn cứ gì, dự kiến tới khi nào. Nhất quán với [`tickets-srs.md`](./tickets-srs.md) `BR-45.10` và [ADR-0008](../docs/adr/0008-data-subject-deletion-contract-omnichat-tickets.md) (điều khoản 3).

  **Lý do nghiệp vụ:** tranh chấp thường kéo dài hơn thời hạn lưu trữ, nên xóa mất bằng chứng là rủi ro lớn; nhưng nói "đã xóa" khi dữ liệu còn là sai sự thật, và đóng yêu cầu khi còn phần bị hoãn thì phần đó không bao giờ được xóa. Một quyết định giữ dữ liệu cá nhân ngược ý khách phải thuộc người chịu trách nhiệm cao nhất về dữ liệu.

- **`BR-23.8` (Phát hiện dữ liệu nhạy cảm khách gõ vào):** Hệ thống PHẢI phát hiện dữ liệu nhạy cảm mà khách gõ trực tiếp vào hội thoại (số thẻ thanh toán, mã định danh cá nhân, mã xác thực một lần) theo danh mục doanh nghiệp cấu hình (`CFG-23-03`), và áp mẫu che của `BR-23.9`. Dữ liệu đã phát hiện KHÔNG ĐƯỢC hiển thị đầy đủ cho Agent, không nằm trong kết quả tìm kiếm, không nằm trong tệp xuất và không chuyển cho Bot ở dạng đầy đủ.

  **Lý do nghiệp vụ:** khách vẫn gõ những thông tin này dù được dặn không nên; lưu và hiển thị nguyên văn là doanh nghiệp gánh nghĩa vụ tuân thủ mà họ không định gánh.

- **`BR-23.9` (Trường nhạy cảm của loại dữ liệu Hội thoại):** Phân hệ khai báo các trường nhạy cảm của hệ thống sau cho loại dữ liệu Hội thoại theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-40`. Mẫu che áp ở mọi nơi dữ liệu xuất hiện: màn hình xử lý, hàng đợi, tìm kiếm, thông báo, báo cáo bấm xuyên xuống, bản gửi định kỳ, tệp xuất. Các lớp che chồng nhau áp lớp hạn chế nhất và không lượt cấp trên bản ghi nào nới được ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`, `BR-40.3`). Tác nhân AI luôn nhận giá trị đã che — hằng số hệ thống theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-40.1`, không tham số hay quyền nào thay đổi được.

  | Trường nhạy cảm | Mẫu che | Ai xem đầy đủ |
  | --- | --- | --- |
  | **Nội dung tin nhắn và tệp đính kèm** | Trích đoạn ngắn của tin đầu tiên (độ dài `CFG-05-07`); tệp đính kèm chỉ hiện tên loại tệp, không mở được | (a) Người phụ trách hội thoại; (b) người đang được mời Xin ý kiến, qua lượt cấp trên bản ghi mức trần Chỉ đọc, thu hồi khi lượt kết thúc (`BR-07.6`); (c) với hội thoại **đã có** Người phụ trách: mọi người có (Hội thoại, Xem) bao phủ hội thoại; (d) với hội thoại **chưa có** Người phụ trách: người có (Hội thoại, Xem toàn văn khi chưa nhận) bao phủ hội thoại; (e) người có (Hội thoại, Chấm chất lượng) bao phủ hội thoại khi đang chấm; (f) bước kịch bản cố định của Bot trong chính hội thoại, không đưa dữ liệu cho mô hình AI (`BR-11.7`); bước Bot dùng mô hình AI chỉ nhận giá trị đã che |
  | **Số điện thoại, email và định danh kênh của người gửi** | Theo mẫu che mặc định của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-40` (số điện thoại giữ 4 số cuối; email giữ ký tự đầu và tên miền; định danh kênh thay bằng ký hiệu che) | Theo đúng mức hiển thị mà [`contacts-srs.md`](./contacts-srs.md) `FEAT-04` áp cho kênh liên lạc của hồ sơ khách hàng gắn với hội thoại (gồm Hồ sơ khách hàng tạm). Trả lời khách qua kênh không đòi hỏi thấy định danh đầy đủ |
  | **Dữ liệu nhạy cảm khách gõ vào (`BR-23.8`)** | Thay toàn bộ bằng ký hiệu che kèm loại dữ liệu (ví dụ "[số thẻ đã che]") | Mặc định không ai. Chỉ khi doanh nghiệp chọn lưu bản gốc có kiểm soát (`CFG-23-04`): người có quyền quản trị Xem dữ liệu nhạy cảm trong hội thoại, mỗi lần xem ghi vào `FEAT-35` |

  Phân hệ không khai báo Sàn bắt buộc trên ô nào của loại Hội thoại; việc Tác nhân AI luôn bị che và việc không ai xem đầy đủ dữ liệu nhạy cảm khi doanh nghiệp không lưu bản gốc là hằng số hệ thống.

  **Lý do nghiệp vụ:** hội thoại là nơi tập trung nhiều dữ liệu cá nhân nhất của CRM và đi qua nhiều màn hình nhất; nếu mỗi màn hình tự quyết định che gì, chỗ nào quên che chính là chỗ rò rỉ. Toàn văn không bị che với người đã có quyền xem hội thoại đã có người phụ trách, vì đọc là công việc hằng ngày của đội; nhưng hội thoại chưa ai nhận thì chưa ai chịu trách nhiệm, nên chỉ mở cho người được giao quyền đó.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-23.1.1` | Thời hạn lưu trữ 12 tháng | Hội thoại 13 tháng tuổi | Nội dung không còn xem được |
| `AC-23.2.1` | Tình huống AC-23.1.1 | Mở báo cáo khối lượng của kỳ đó | Số liệu tổng hợp vẫn còn |
| `AC-23.3.1` | Khách có hội thoại trên Facebook (đơn vị Hà Nội) và WhatsApp (đơn vị Đà Nẵng) | Người thực hiện yêu cầu xóa theo quy trình chủ thể dữ liệu | Cả hai được xóa trong một lần; nhận được xác nhận hoàn tất cho biên bản |
| `AC-23.4.1` | Người có (Hội thoại, Xuất) bao phủ, không xem được ghi chú nội bộ | Xuất hội thoại | Tệp không có ghi chú nội bộ và ghi rõ "không kèm ghi chú nội bộ"; số điện thoại khách che theo quyền của người xuất |
| `AC-23.4.2` | Người có (Hội thoại, Xuất) = Không có | Tìm thao tác xuất | Không khả dụng |
| `AC-23.5.1` | Doanh nghiệp thông báo ngừng dùng | Trong thời hạn đã báo | Tải được toàn bộ dữ liệu hội thoại |
| `AC-23.6.1` | Khách trên máy dùng chung | Bấm "Kết thúc và xóa lịch sử" | Người mở widget sau trên máy đó không thấy cuộc trò chuyện |
| `AC-23.7.1` | Hội thoại đang Tạm dừng xóa, đã quá thời hạn lưu trữ | Tới mốc xóa | Không bị xóa; tra được ai đặt, căn cứ, dự kiến tới khi nào |
| `AC-23.7.2` | Gỡ Tạm dừng xóa trên hội thoại đã quá thời hạn | Gỡ | Hội thoại bị xóa theo thời hạn lưu trữ |
| `AC-23.7.3` | Khách X có 3 hội thoại, 1 hội thoại đang Tạm dừng xóa | Thực hiện yêu cầu xóa của X | 2 hội thoại bị xóa; biên bản ghi hội thoại thứ ba "Đang tạm dừng theo yêu cầu pháp lý" kèm căn cứ và ngày dự kiến gỡ; yêu cầu xóa ở trạng thái chưa hoàn tất |
| `AC-23.7.4` | Tiếp nối AC-23.7.3 | Người phụ trách Bảo vệ Dữ liệu gỡ tạm dừng | Hội thoại thứ ba bị xóa ngay; biên bản cập nhật hoàn tất cho phần đó |
| `AC-23.7.5` | F có quyền quản trị Quản lý lưu trữ dữ liệu hội thoại, không có toàn quyền, không phải Người phụ trách Bảo vệ Dữ liệu | Tìm thao tác đặt Tạm dừng xóa | Không khả dụng |
| `AC-23.7.6` | Người có toàn quyền | Đặt Tạm dừng xóa không nhập căn cứ | Nút xác nhận bị vô hiệu tới khi có căn cứ và ngày dự kiến gỡ |
| `AC-23.8.1` | Khách gõ số thẻ thanh toán | Agent phụ trách xem hội thoại | Thấy "[số thẻ đã che]" |
| `AC-23.8.2` | Tình huống AC-23.8.1 | Tìm theo 4 số đầu của thẻ | Hội thoại không có trong kết quả theo số thẻ |
| `AC-23.9.1` | Hội thoại đã có Người phụ trách B; A cùng đơn vị, (Hội thoại, Xem) = Đơn vị của mình | A mở hội thoại | A đọc được toàn văn |
| `AC-23.9.2` | Hội thoại chưa có người phụ trách; A (Nhân viên Hỗ trợ mặc định) | A mở hội thoại | A chỉ thấy trích đoạn; tệp đính kèm chỉ hiện loại tệp |
| `AC-23.9.3` | Hồ sơ khách gắn với hội thoại nằm ngoài phạm vi thông thường của A; A đang phụ trách hội thoại | A xem số điện thoại khách | Che một phần theo [`contacts-srs.md`](./contacts-srs.md) `FEAT-04`; A vẫn trả lời khách bình thường |
| `AC-23.9.4` | Trợ lý AI tóm tắt hội thoại cho người dùng có toàn quyền | Trợ lý đọc hội thoại | Nhận số điện thoại, email, dữ liệu nhạy cảm và nội dung ở dạng đã che |
| `AC-23.9.5` | Doanh nghiệp chọn lưu bản gốc có kiểm soát; C có quyền quản trị Xem dữ liệu nhạy cảm trong hội thoại | C bấm xem số thẻ đã che | Thấy giá trị đầy đủ; nhật ký truy cập ghi C, hội thoại, thời điểm |
| `AC-23.9.6` | Doanh nghiệp không lưu bản gốc (mặc định) | Người có toàn quyền tìm cách xem số thẻ đầy đủ | Không có cách nào xem |
| `AC-23.9.7` | Báo cáo gửi định kỳ có danh sách hội thoại | Người nhận mở bản gửi | Định danh khách che theo quyền của người nhận |

**Tham chiếu:** `FEAT-23` → issue [#68](https://github.com/crmsaassaudi/product-management/issues/68). Hợp đồng xóa theo quyền chủ thể dữ liệu → [ADR-0008](../docs/adr/0008-data-subject-deletion-contract-omnichat-tickets.md).

---

### FEAT-24 — Giờ làm việc & lịch nghỉ

**Mô tả nghiệp vụ:** Giờ làm việc là căn cứ tính cam kết SLA, quyết định khi nào gửi thông báo ngoài giờ, khi nào ngừng nhận hội thoại mới vào hàng đợi, và khi nào widget báo không có ai trực (`BR-01.5`, `BR-02.7`, `BR-05.8`, `BR-08.4`, `BR-22.4`).

**Vai trò sử dụng chính:** Người có quyền quản trị Quản lý giờ làm việc hội thoại; Agent/Giám sát viên (thấy trạng thái trong/ngoài giờ).

**Điều kiện tiên quyết:** Workspace đã có múi giờ và lịch làm việc chung ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-04`).

**Luồng chính:**

1. Dùng lịch làm việc chung của workspace hoặc khai báo lịch riêng cho kênh/Hộp thư.
2. Khai báo lịch nghỉ lễ và ngày nghỉ đột xuất.
3. Hệ thống áp lịch đúng cho từng hội thoại.

**Quy tắc nghiệp vụ:**

- **`BR-24.1` (Lịch theo ngày trong tuần):** Lịch chung của Omnichat là lịch làm việc của workspace ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-04`) theo từng ngày trong tuần và múi giờ workspace; Omnichat không định nghĩa một lịch chung thứ hai.

  **Lý do nghiệp vụ:** hai lịch chung sẽ lệch nhau và cùng một ngày lễ được tính khác nhau ở hai phân hệ.

- **`BR-24.2` (Lịch nghỉ):** Doanh nghiệp PHẢI khai báo được **lịch nghỉ lễ** và ngày nghỉ đột xuất; ngày nghỉ tính là ngoài giờ cho mọi mục đích: SLA, thông báo tự động, nhận vào hàng đợi.

  **Lý do nghiệp vụ:** ngày nghỉ không khai báo được thì mọi hội thoại trong ngày đó vi phạm ảo.

- **`BR-24.3` (Lịch riêng và thứ tự áp dụng):** Một kênh hoặc một Hộp thư CÓ THỂ có lịch riêng (`CFG-24-01`). Thứ tự áp dụng: lịch riêng của Hộp thư › lịch riêng của kênh › lịch chung của workspace.

  **Lý do nghiệp vụ:** đội VIP trực 24/7 trong khi đội thường trực giờ hành chính.

- **`BR-24.4` (Không hồi tố):** Mọi phép tính thời hạn (`BR-08.4`) PHẢI dùng đúng lịch áp dụng tại thời điểm phát sinh; đổi lịch KHÔNG ĐƯỢC thay đổi hồi tố kết quả đã chốt.

  **Lý do nghiệp vụ:** sửa lịch hôm nay không được làm đẹp hay xấu số liệu tháng trước.

- **`BR-24.5` (Hiển thị trong/ngoài giờ):** Agent và Giám sát viên PHẢI thấy được kênh/Hộp thư mình đang trực đang trong hay ngoài giờ, và thời điểm chuyển gần nhất.

  **Lý do nghiệp vụ:** Agent cần biết vì sao khách đang nhận thông báo ngoài giờ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-24.1.1` | Lịch làm việc workspace Thứ Hai – Thứ Sáu 08:00–17:30 | Mở cấu hình giờ làm việc Omnichat | Lịch chung hiển thị đúng lịch workspace, sửa ở một chỗ |
| `AC-24.2.1` | Ngày 02/09 khai báo nghỉ lễ | Hội thoại phát sinh ngày đó | Không tính vào thời hạn cam kết; khách nhận thông báo ngoài giờ nếu bật |
| `AC-24.3.1` | Hộp thư VIP có lịch 24/7, kênh có lịch hành chính | Hội thoại thuộc Hộp thư VIP lúc 22:00 | Áp lịch 24/7 |
| `AC-24.4.1` | Hội thoại tuần trước đã chốt "đáp ứng" | Sửa lịch hôm nay | Kết quả vẫn "đáp ứng" |
| `AC-24.5.1` | Kênh đang ngoài giờ | Agent nhìn màn hình | Thấy "Ngoài giờ — mở lại 08:00 Thứ Hai" |

**Tham chiếu:** `FEAT-24` → issue [#69](https://github.com/crmsaassaudi/product-management/issues/69).

---

### FEAT-25 — Nhật ký thay đổi cấu hình Omnichat

**Mô tả nghiệp vụ:** Lưu vết ai đã đổi cấu hình gì trong Omnichat, để điều tra được khi một đội đột nhiên không nhận được hội thoại, một cam kết đột nhiên khác đi, hay một nhóm người đột nhiên thấy dữ liệu trước đó họ không thấy.

**Vai trò sử dụng chính:** Người có quyền quản trị Xem nhật ký cấu hình Omnichat; người thực hiện thay đổi.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Mỗi thay đổi cấu hình được ghi vết.
2. Người có quyền tra cứu theo người, loại cấu hình, thời gian.

**Quy tắc nghiệp vụ:**

- **`BR-25.1` (Phạm vi ghi vết):** Mọi thay đổi cấu hình vận hành — kênh, Hộp thư, tập Agent được phân công, quy tắc phân công, Chính sách SLA, leo thang, tự động đóng, mẫu tin nhắn dùng chung, Bot, giờ làm việc, danh mục lý do và nhãn, quy tắc tự động hóa — PHẢI được ghi: ai đổi, đổi gì, từ giá trị nào sang giá trị nào, lúc nào.

  **Lý do nghiệp vụ:** sự cố vận hành thường bắt đầu từ một thay đổi cấu hình mà không ai nhớ đã làm.

- **`BR-25.2` (Thay đổi ai-thấy-gì thuộc nhật ký cấu hình quyền):** Những thay đổi làm đổi **ai được làm gì hoặc ai thấy gì** — đặt/đổi đơn vị tiếp nhận của kênh và bật/tắt "gồm các đơn vị con" (`BR-01.9`, `BR-05.13`), danh sách hàng đợi đích được phép chuyển tới và bật tự chuyển khi quá ngưỡng (`CFG-05-08`, `CFG-05-11`) — hai tham số này nới phạm vi cho cả một đơn vị nên chỉ Người có toàn quyền đặt —, chuyển phân hệ của hộp thư email (`BR-01.10`), phạm vi hiển thị của mẫu tin nhắn dùng chung (`BR-18.1`), điều chỉnh các ô Hội thoại và quyền quản trị của phân hệ trên vai trò (Mục 5), lưu bản gốc dữ liệu nhạy cảm (`CFG-23-04`) — thuộc **nhật ký thay đổi cấu hình quyền** của workspace ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-41`), không thuộc một nhật ký riêng của Omnichat. Thao tác nới rộng hoặc trung tính trong nhóm này đóng khi lỗi: không ghi được nhật ký thì thao tác bị hủy ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.5`); thao tác thu hẹp theo `BR-25.5`.

  **Lý do nghiệp vụ:** mất vết của một lượt mở hàng đợi cho cả một chi nhánh là mất khả năng chứng minh ai đã cho ai xem dữ liệu khách hàng.

- **`BR-25.3` (Tra cứu):** Người có quyền PHẢI tra cứu được nhật ký theo người thực hiện, loại cấu hình và khoảng thời gian.

  **Lý do nghiệp vụ:** nhật ký không tra được thì như không có.

- **`BR-25.4` (Ngoài phạm vi):** Thao tác chỉ xem cấu hình và thao tác trong chế độ thử nghiệm (`FEAT-34`) KHÔNG thuộc phạm vi nhật ký này.

  **Lý do nghiệp vụ:** ghi cả thao tác không làm thay đổi gì làm nhật ký ngập tiếng ồn.

- **`BR-25.5` (Thao tác thu hẹp không bị chặn, nhật ký ghi bù):** Thao tác thu hẹp quyền của phân hệ — tắt "gồm các đơn vị con", bỏ một hàng đợi khỏi danh sách đích hoặc tắt tự chuyển, thu hẹp phạm vi hiển thị mẫu dùng chung, gỡ quyền xem dữ liệu nhạy cảm, chuyển sang không lưu bản gốc dữ liệu nhạy cảm, và lượt Hệ thống trả hội thoại trò chuyện đồng thời về hàng đợi **ngay trong lượt tạm ngưng** (`BR-06.6`) như một phần của thao tác tạm ngưng — luôn thực hiện được kể cả khi nhật ký gặp sự cố; sự kiện được giữ để ghi bù, gắn dấu "ghi bù" kèm thời điểm thao tác thật ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.6`, [ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md)). Các thao tác xử lý hội thoại **sau** lượt tạm ngưng hoặc trong quy trình rời workspace (trả về hàng đợi, chuyển tạm, giao người nhận) là nới rộng và đóng khi lỗi theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.5`. Thao tác vừa thu hẹp vừa nới rộng là nới rộng.

  **Lý do nghiệp vụ:** chặn một lượt thu hồi vì nhật ký lỗi là giữ nguyên quyền xem dữ liệu khách đúng lúc doanh nghiệp cần cắt khẩn cấp; ghi bù vẫn giữ đủ vết.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-25.1.1` | Một đội ngừng nhận hội thoại | Tra nhật ký theo Hộp thư của đội | Thấy ai đã gỡ đội khỏi tập phân công, giá trị trước/sau, lúc nào |
| `AC-25.2.1` | Nhật ký cấu hình quyền gặp sự cố | Người có toàn quyền bật "gồm các đơn vị con" | Thao tác bị hủy, nhận thông báo lỗi |
| `AC-25.2.2` | Người có toàn quyền đổi đơn vị tiếp nhận của kênh | Thành công | Lượt đổi có trong nhật ký thay đổi cấu hình quyền của workspace |
| `AC-25.3.1` | Có 200 thay đổi trong tháng | Lọc theo người thực hiện và loại "Chính sách SLA" | Chỉ thấy các thay đổi khớp |
| `AC-25.4.1` | Quản trị viên chạy thử một quy tắc ở chế độ thử nghiệm | Tra nhật ký | Không có dòng nào cho lượt chạy thử |
| `AC-25.5.1` | Nhật ký cấu hình quyền gặp sự cố | Người có toàn quyền tắt "gồm các đơn vị con" | Thao tác thành công ngay; khi nhật ký phục hồi, dòng nhật ký xuất hiện với dấu "ghi bù" và thời điểm thao tác thật |
| `AC-25.5.2` | Nhật ký gặp sự cố; A giữ 2 phiên Live Chat và 3 hội thoại email | A bị tạm ngưng | Tạm ngưng thành công; 2 phiên Live Chat về hàng đợi ngay; nhật ký được ghi bù |
| `AC-25.5.3` | Tiếp nối AC-25.5.2, nhật ký vẫn sự cố | Người thực hiện chọn trả 3 hội thoại email về hàng đợi | Thao tác bị hủy, nhận thông báo lỗi; làm lại được khi nhật ký phục hồi; cảnh báo `CFG-05-09` vẫn chạy |

**Tham chiếu:** `BR-25.2` → [ADR-0003](../docs/adr/0003-permission-config-audit-log-fail-closed.md). `BR-25.5` → [ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md). `FEAT-25` → issue [#70](https://github.com/crmsaassaudi/product-management/issues/70).

---

### FEAT-26 — Giám sát và can thiệp thời gian thực

**Mô tả nghiệp vụ:** Cho người điều hành một chỗ duy nhất nhìn thấy tình hình phục vụ **đang diễn ra** và can thiệp ngay, thay vì phát hiện vấn đề khi đọc báo cáo cuối ngày. Cảnh báo từng hội thoại riêng lẻ (`FEAT-09`) không cho thấy toàn cảnh để biết nên can thiệp vào đâu trước.

**Vai trò sử dụng chính:** Trưởng nhóm; Giám sát viên — tức người có (Hội thoại, Xem) bao phủ các đội cần theo dõi và (Hội thoại, Can thiệp) cho các thao tác can thiệp.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Mở bảng điều hành; thấy hàng đợi, vi phạm và trạng thái Agent trong phạm vi của mình.
2. Chạm ngưỡng → nhận cảnh báo chủ động.
3. Nhắc bài, tiếp quản, hoặc gán lại hàng loạt.

**Quy tắc nghiệp vụ:**

- **`BR-26.1` (Toàn cảnh cập nhật liên tục):** Người điều hành PHẢI thấy, cập nhật liên tục: số khách đang chờ và thời gian chờ lâu nhất theo từng hàng đợi/Hộp thư/kênh; hội thoại đang/sắp vi phạm; trạng thái từng Agent kèm số việc đang giữ so với năng lực.

  **Lý do nghiệp vụ:** không thấy toàn cảnh thì không biết đội nào đang cháy.

- **`BR-26.2` (Giới hạn theo phạm vi):** Mọi số liệu trên bảng điều hành PHẢI giới hạn đúng trong phạm vi mức (Hội thoại, Xem) của người xem cộng hàng đợi của đơn vị mình thuộc; trạng thái Agent chỉ hiện cho các thành viên của những đơn vị đó.

  **Lý do nghiệp vụ:** một Trưởng nhóm không nhìn thấy tình hình của đội khác chỉ vì cùng mở một màn hình.

- **`BR-26.3` (Ngưỡng cảnh báo vận hành):** Doanh nghiệp PHẢI đặt được ngưỡng cảnh báo theo hàng đợi/Hộp thư/kênh (`CFG-26-01`: số khách chờ, thời gian chờ lâu nhất, số Agent sẵn sàng tối thiểu); chạm ngưỡng PHẢI cảnh báo Người phụ trách đơn vị tiếp nhận của hàng đợi.

  **Lý do nghiệp vụ:** người điều hành không nhìn màn hình liên tục.

- **`BR-26.4` (Nhắc bài):** Người có (Hội thoại, Can thiệp) bao phủ hội thoại PHẢI **nhắc bài riêng cho Agent** trong lúc hội thoại đang diễn ra; khách KHÔNG ĐƯỢC thấy nội dung nhắc dưới bất kỳ hình thức nào.

  **Lý do nghiệp vụ:** kèm cặp đúng lúc giúp Agent mới xử lý được ca khó mà không phải chuyển đi.

- **`BR-26.5` (Tiếp quản):** Người có (Hội thoại, Can thiệp) bao phủ hội thoại PHẢI **tiếp quản** được hội thoại đang diễn ra: trả lời trực tiếp khách và tắt Bot nếu đang chạy. Tiếp quản **không đổi Người phụ trách** và không đổi đơn vị tiếp nhận; Agent đang phụ trách PHẢI được thông báo và vẫn là người chịu trách nhiệm. Muốn nhận hẳn hội thoại về mình thì là thao tác gán, chịu `BR-07.3` (gồm [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.4`).

  **Lý do nghiệp vụ:** khách đang khiếu nại gay gắt cần người có thẩm quyền ngay, không chờ chuyển tiếp; nhưng nếu tiếp quản tự đổi Người phụ trách thì ô Can thiệp thành đường vòng của ô Gán.

- **`BR-26.6` (Gán lại hàng loạt):** Người có (Hội thoại, Gán) bao phủ các hội thoại PHẢI **gán lại hàng loạt** hội thoại của một Agent sang người khác hoặc trả về hàng đợi trong một thao tác — khi Agent nghỉ đột xuất hoặc hết ca. Người nhận phải thỏa `BR-04.1` (a), (b). Màn hình PHẢI hiển thị trước số hội thoại bị tác động và PHẢI để vết theo `BR-15.4`. Khi Agent bị tạm ngưng hoặc rời workspace, việc xử lý hội thoại đi theo `BR-05.14`.

  **Lý do nghiệp vụ:** gán lại từng hội thoại một khi một người vắng mặt là để khách chờ trong lúc người điều hành bấm chuột.

- **`BR-26.7` (Ghi lại can thiệp):** Mọi can thiệp theo `BR-26.4`, `BR-26.5`, `BR-26.6` PHẢI được ghi lại kèm người thực hiện và lý do.

  **Lý do nghiệp vụ:** không phân biệt được kết quả tự làm và kết quả có người can thiệp thì mọi chỉ số hiệu suất cá nhân mất ý nghĩa (`BR-20.6`).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-26.1.1` | Hàng đợi Live Chat có 7 khách chờ, lâu nhất 5 phút | Trưởng nhóm mở bảng điều hành | Thấy "7 đang chờ · lâu nhất 5 phút", cập nhật không cần làm mới |
| `AC-26.2.1` | Trưởng nhóm Hà Nội có (Hội thoại, Xem) = Đơn vị và các đơn vị con | Mở bảng điều hành | Không thấy hàng đợi và Agent của Đà Nẵng |
| `AC-26.3.1` | Ngưỡng 5 khách chờ | Khách thứ 6 vào hàng đợi | Người phụ trách đơn vị tiếp nhận nhận cảnh báo |
| `AC-26.4.1` | Trưởng nhóm nhắc bài cho Agent | Khách nhìn khung chat | Không thấy nội dung nhắc |
| `AC-26.4.2` | Người có (Hội thoại, Can thiệp) = Không có | Tìm thao tác nhắc bài | Không khả dụng |
| `AC-26.5.1` | Giám sát viên tiếp quản | Xác nhận | Agent cũ nhận thông báo; hội thoại vẫn thuộc đơn vị tiếp nhận cũ |
| `AC-26.5.2` | Giám sát viên tiếp quản hội thoại của A | Mở thông tin hội thoại | Người phụ trách vẫn là A; dòng thời gian ghi lượt tiếp quản |
| `AC-26.6.1` | Agent nghỉ đột xuất với 8 hội thoại | Trưởng nhóm chọn gán lại hàng loạt cho Agent B | Màn hình báo trước 8 hội thoại; xác nhận xong cả 8 do B phụ trách, mỗi cái có vết |
| `AC-26.6.2` | Người nhận được chọn không thuộc đơn vị tiếp nhận | Chọn người nhận | Người đó không có trong danh sách |
| `AC-26.7.1` | Có một lượt tiếp quản | Xem dòng thời gian | Ghi người tiếp quản, lúc nào, lý do |

**Tham chiếu:** `FEAT-26` → issue [#80](https://github.com/crmsaassaudi/product-management/issues/80).

---

### FEAT-27 — Quản lý chất lượng hội thoại (QA)

**Mô tả nghiệp vụ:** Đo chất lượng phục vụ bằng việc chấm điểm nội dung hội thoại theo tiêu chí doanh nghiệp tự đặt, thay vì chỉ dựa vào tốc độ và CSAT — vốn có tỷ lệ phản hồi thấp và thiên lệch về hai đầu cảm xúc.

**Vai trò sử dụng chính:** Chuyên viên chất lượng (người có (Hội thoại, Chấm chất lượng) bao phủ hội thoại); người có quyền quản trị Quản lý chất lượng hội thoại (phiếu chấm, lấy mẫu, hiệu chuẩn); Agent (người được chấm).

**Điều kiện tiên quyết:** Có ít nhất một phiếu chấm.

**Luồng chính:**

1. Doanh nghiệp dựng phiếu chấm và quy tắc lấy mẫu.
2. Hệ thống lấy mẫu, hoặc ai đó gắn cờ hội thoại cần chấm.
3. Người chấm chấm điểm; Agent nhận kết quả và có thể phúc khảo.

**Quy tắc nghiệp vụ:**

- **`BR-27.1` (Phiếu chấm):** Doanh nghiệp PHẢI tự định nghĩa được phiếu chấm: tiêu chí, trọng số, thang điểm, và tiêu chí lỗi nghiêm trọng làm điểm tổng về 0.

  **Lý do nghiệp vụ:** chuẩn chất lượng của ngân hàng và của cửa hàng thời trang khác nhau.

- **`BR-27.2` (Lấy mẫu):** Doanh nghiệp PHẢI cấu hình được cách lấy mẫu (`CFG-27-01`): số lượng hoặc tỷ lệ trên mỗi Agent mỗi kỳ, và điều kiện lọc (kênh, nhãn, lý do xử lý, vi phạm SLA, CSAT thấp, chuyển tiếp nhiều lần). Mẫu chỉ lấy trong các hội thoại thuộc phạm vi (Hội thoại, Chấm chất lượng) của người chấm.

  **Lý do nghiệp vụ:** chấm ngẫu nhiên bỏ sót đúng những hội thoại có vấn đề; chấm ngoài phạm vi là đọc nội dung không được giao.

- **`BR-27.3` (Gắn cờ cần chấm):** Người có (Hội thoại, Xem) bao phủ hội thoại PHẢI gắn cờ được một hội thoại cần chấm ngay khi phát hiện.

  **Lý do nghiệp vụ:** ca xấu phát hiện hôm nay không được chờ tới kỳ lấy mẫu sau.

- **`BR-27.4` (Phản hồi và phúc khảo):** Kết quả chấm PHẢI tới đúng Agent được chấm kèm nhận xét; Agent PHẢI phúc khảo được; kết quả phúc khảo PHẢI được ghi lại.

  **Lý do nghiệp vụ:** kết quả không phản hồi thì không cải thiện được gì; không cho phúc khảo thì Agent coi là không công bằng.

- **`BR-27.5` (Hiệu chuẩn):** Doanh nghiệp PHẢI hiệu chuẩn được giữa các người chấm — nhiều người cùng chấm một hội thoại và đối chiếu chênh lệch.

  **Lý do nghiệp vụ:** điểm chất lượng phải nói lên chất lượng thật, không phải độ khó tính của người chấm.

- **`BR-27.6` (Điểm chất lượng trong báo cáo hiệu suất):** Điểm chất lượng trung bình từng Agent PHẢI xuất hiện trong `FEAT-20`, cạnh tốc độ và CSAT.

  **Lý do nghiệp vụ:** điểm chất lượng tách khỏi đánh giá hiệu suất thì không ai dùng.

- **`BR-27.7` (Chấm không làm đổi hội thoại):** Việc chấm KHÔNG ĐƯỢC làm thay đổi nội dung, trạng thái hay lịch sử của hội thoại.

  **Lý do nghiệp vụ:** hội thoại là bằng chứng; người chấm không được sửa bằng chứng.

- **`BR-27.8` (Người chấm độc lập):** Một người KHÔNG ĐƯỢC chấm hội thoại mà chính mình từng phụ trách, từng được mời Xin ý kiến, hoặc từng can thiệp; quy tắc áp như nhau cho mọi vai trò.

  **Lý do nghiệp vụ:** tự chấm việc của mình làm điểm chất lượng mất tính độc lập — lý do tồn tại của nó.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-27.1.1` | Quản trị viên dựng phiếu 5 tiêu chí, 1 tiêu chí nghiêm trọng | Người chấm đánh trượt tiêu chí nghiêm trọng | Điểm tổng = 0 |
| `AC-27.2.1` | Lấy mẫu 5 hội thoại mỗi Agent mỗi tuần, ưu tiên CSAT thấp | Hết tuần | Mỗi Agent có 5 hội thoại được lấy mẫu, có các hội thoại CSAT thấp |
| `AC-27.2.2` | Người chấm có (Hội thoại, Chấm chất lượng) = Đơn vị của mình | Lấy mẫu | Không có hội thoại ngoài đơn vị của người chấm |
| `AC-27.3.1` | Trưởng nhóm thấy một hội thoại có vấn đề | Gắn cờ "cần chấm" | Hội thoại vào danh sách chờ chấm ngay |
| `AC-27.4.1` | Agent nhận kết quả chấm | Gửi phúc khảo | Phúc khảo được ghi lại; kết quả phúc khảo hiện cạnh kết quả gốc |
| `AC-27.5.1` | Ba người cùng chấm một hội thoại | Xem hiệu chuẩn | Thấy chênh lệch điểm từng tiêu chí |
| `AC-27.6.1` | Mở Báo cáo hiệu suất Agent | Xem | Có cột điểm chất lượng cạnh CSAT |
| `AC-27.7.1` | Người chấm hoàn tất chấm | Mở hội thoại | Nội dung, trạng thái, lịch sử không đổi (ngoài mốc "đã được chấm") |
| `AC-27.8.1` | Trưởng nhóm có (Hội thoại, Chấm chất lượng) từng tiếp quản hội thoại X | Mở X để chấm | Thao tác chấm bị vô hiệu kèm lý do "bạn đã tham gia hội thoại này" |

**Tham chiếu:** `FEAT-27` → issue [#81](https://github.com/crmsaassaudi/product-management/issues/81).

---

### FEAT-28 — Ca trực, tuân thủ ca & bàn giao ca

**Mô tả nghiệp vụ:** Cho doanh nghiệp xếp lịch trực, đối chiếu lịch với thực tế, và bảo đảm không hội thoại nào bị bỏ rơi khi đổi ca. Chỉ biết **ai đang trực tuyến** mà không biết **ai đáng lẽ phải trực tuyến** thì không phát hiện được thiếu người trước khi khách phải chờ.

**Vai trò sử dụng chính:** Người có quyền quản trị Xếp ca trực; Trưởng nhóm (điều chỉnh trong ca); Agent.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Xếp ca cho Agent theo ngày, khung giờ, hàng đợi phụ trách.
2. Hệ thống đối chiếu ca với trạng thái thực tế.
3. Hết ca: Agent bàn giao hội thoại còn mở rồi mới kết thúc ca.

**Quy tắc nghiệp vụ:**

- **`BR-28.1` (Xếp ca):** Người có quyền Xếp ca trực PHẢI xếp được ca cho Agent theo ngày và khung giờ, gắn với hàng đợi/Hộp thư Agent phụ trách trong ca; chỉ xếp được Agent vào hàng đợi của đơn vị mà Agent là thành viên, và chỉ xếp cho thành viên của đơn vị mà ô (Hội thoại, Gán) của người xếp bao phủ hội thoại của đơn vị đó, nhất quán với [`tickets-srs.md`](./tickets-srs.md) `BR-11.1`.

  **Lý do nghiệp vụ:** xếp một người vào ca của hàng đợi mà họ không thuộc là xếp ca không ai trực được.

- **`BR-28.2` (Tuân thủ ca):** Hệ thống PHẢI đối chiếu ca với trạng thái thực tế (`BR-06.4`) và đưa ra **mức tuân thủ ca** dùng trong `FEAT-20`.

  **Lý do nghiệp vụ:** không đo tuân thủ thì lịch ca chỉ là tờ giấy.

- **`BR-28.3` (Cảnh báo thiếu người trước):** Khi số Agent sẵn sàng thấp hơn mức xếp lịch cho một khung giờ, hệ thống PHẢI cảnh báo Người phụ trách đơn vị tiếp nhận **trước khi** hàng đợi ùn lại.

  **Lý do nghiệp vụ:** biết thiếu người sau khi khách chờ quá hạn là đã muộn.

- **`BR-28.4` (Không kết thúc ca khi còn việc):** Agent KHÔNG ĐƯỢC kết thúc ca khi còn hội thoại chưa kết thúc. Hệ thống PHẢI liệt kê hội thoại còn giữ, yêu cầu ghi chú bàn giao (`BR-15.2`) và cho Agent chọn với từng hội thoại theo `BR-07.3`: (a) **đề nghị chuyển** cho một người được đề xuất, đạt điều kiện người nhận tại `BR-07.2` (xét lại lúc chấp nhận) — ca chỉ kết thúc khi mọi đề nghị đã được chấp nhận, hoặc khi Agent bấm kết thúc ca thì hội thoại có đề nghị chưa được chấp nhận được trả về hàng đợi; (b) **trả về hàng đợi** (`BR-05.14` (d)). Giao thẳng — người nhận cũng phải đạt `BR-07.2` — chỉ có khi Agent có (Hội thoại, Gán) từ Đơn vị của mình trở lên. Người phụ trách đơn vị của Agent hoặc người có quyền Xếp ca trực CÓ THỂ cho bỏ qua trong trường hợp khẩn cấp; việc bỏ qua PHẢI được ghi lại.

  **Lý do nghiệp vụ:** khách nhắn tiếp sáng hôm sau không được gặp một hội thoại không ai nhận.

- **`BR-28.5` (Căn cứ xếp ca):** Doanh nghiệp PHẢI xem được **khối lượng hội thoại theo giờ trong ngày và ngày trong tuần của các kỳ trước** theo từng hàng đợi làm căn cứ xếp ca, trong phạm vi `BR-19.2`.

  **Lý do nghiệp vụ:** không có căn cứ thì xếp ca là phỏng đoán.

- **`BR-28.6` (Tôn trọng lịch nghỉ):** Ca trực PHẢI tôn trọng Giờ làm việc và lịch nghỉ (`FEAT-24`); xếp ca vào ngày nghỉ đã khai báo PHẢI bị cảnh báo.

  **Lý do nghiệp vụ:** ca xếp vào ngày lễ mà không ai nhận ra là ca không ai đi làm.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-28.1.1` | Người có quyền Xếp ca trực | Xếp lịch tuần cho đội | Lịch được lưu; mỗi ca gắn hàng đợi của đơn vị Agent thuộc |
| `AC-28.1.2` | Chọn hàng đợi của đơn vị Agent không thuộc | Lưu ca | Hàng đợi đó không có trong danh sách chọn |
| `AC-28.1.3` | Người có quyền Xếp ca trực, (Hội thoại, Gán) = Đơn vị của mình ở "Hỗ trợ – Hà Nội" | Xếp ca cho Agent "Hỗ trợ – Đà Nẵng" | Agent đó không có trong danh sách chọn |
| `AC-28.2.1` | Agent được xếp 08:00–17:00, Sẵn sàng từ 08:30 | Xem tuân thủ | Mức tuân thủ phản ánh 30 phút trễ |
| `AC-28.3.1` | Khung 14:00 xếp 6 người, chỉ 4 Sẵn sàng | 14:00 | Người phụ trách đơn vị tiếp nhận nhận cảnh báo trước khi hàng đợi chạm ngưỡng |
| `AC-28.4.1` | Agent (Gán = Chỉ của mình) còn 6 hội thoại mở lúc hết ca | Bấm kết thúc ca | Hệ thống liệt kê 6 hội thoại; mỗi hội thoại chỉ có "đề nghị chuyển" (kèm người được đề xuất) hoặc "trả về hàng đợi"; không có "giao thẳng" |
| `AC-28.4.2` | Trường hợp khẩn cấp | Người phụ trách đơn vị cho bỏ qua | Agent kết thúc ca; việc bỏ qua được ghi lại |
| `AC-28.4.3` | Agent đề nghị chuyển 4 hội thoại (3 được chấp nhận), trả về hàng đợi 2 | Bấm kết thúc ca | Hội thoại có đề nghị chưa được chấp nhận được trả về hàng đợi; ca kết thúc; mỗi hội thoại có vết |
| `AC-28.4.4` | Agent có (Hội thoại, Gán) = Đơn vị của mình | Bấm kết thúc ca | Có thêm "giao thẳng" cho người cùng đơn vị, có hiệu lực ngay |
| `AC-28.5.1` | Mở căn cứ xếp ca | Chọn 4 tuần trước | Thấy khối lượng theo giờ và ngày của từng hàng đợi trong phạm vi |
| `AC-28.6.1` | Ngày 02/09 là ngày nghỉ | Xếp ca vào ngày đó | Hiện cảnh báo trước khi lưu |

**Tham chiếu:** `FEAT-28` → issue [#82](https://github.com/crmsaassaudi/product-management/issues/82).

---

### FEAT-29 — Danh mục lý do xử lý & quản trị nhãn

**Mô tả nghiệp vụ:** Định nghĩa hai công cụ phân loại mà nhiều tính năng khác phụ thuộc vào: **Lý do xử lý** (kết quả một hội thoại) và **Nhãn** (từ khóa gắn thêm để lọc, định tuyến, tự động hóa, phân tích); kèm danh mục lý do chuyển tiếp dùng cho chuyển tiếp và chuyển hàng đợi.

**Vai trò sử dụng chính:** Người có quyền quản trị Quản lý danh mục lý do & nhãn; Agent (chọn khi xử lý); người dùng kết quả phân tích.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Quản trị viên định nghĩa danh mục lý do xử lý, lý do chuyển tiếp, nhãn.
2. Agent chọn lý do khi giải quyết và gắn nhãn trong lúc xử lý.

**Quy tắc nghiệp vụ:**

- **`BR-29.1` (Danh mục lý do có phân cấp):** Doanh nghiệp PHẢI định nghĩa được danh mục lý do xử lý có phân cấp (nhóm nguyên nhân › nguyên nhân cụ thể), và đặt được danh mục riêng theo Hộp thư hoặc kênh.

  **Lý do nghiệp vụ:** đội bảo hành và đội bán hàng có nguyên nhân liên hệ khác nhau.

- **`BR-29.2` (Lý do bắt buộc):** Doanh nghiệp PHẢI cấu hình được lý do xử lý là bắt buộc khi giải quyết (`CFG-29-11`).

  **Lý do nghiệp vụ:** báo cáo nguyên nhân liên hệ mà phần lớn là "không xác định" thì vô dụng.

- **`BR-29.3` (Danh mục lý do chuyển tiếp):** Doanh nghiệp PHẢI định nghĩa được danh mục lý do chuyển tiếp, tách khỏi lý do xử lý, dùng cho `BR-07.9` và `BR-05.15`.

  **Lý do nghiệp vụ:** "vì sao phải chuyển" và "khách liên hệ vì chuyện gì" là hai câu hỏi khác nhau.

- **`BR-29.4` (Gắn nhiều nhãn):** Một hội thoại PHẢI gắn được nhiều nhãn, ở bất kỳ thời điểm nào, bởi người có (Hội thoại, Sửa) bao phủ hội thoại hoặc bởi quy tắc tự động hóa (`FEAT-33`).

  **Lý do nghiệp vụ:** một hội thoại có thể vừa là khiếu nại vừa liên quan khuyến mãi.

- **`BR-29.5` (Kiểm soát tạo nhãn):** Doanh nghiệp PHẢI kiểm soát được ai tạo nhãn mới (`CFG-29-12`); khi để mở, hệ thống PHẢI gợi ý nhãn đã có trước khi cho tạo mới.

  **Lý do nghiệp vụ:** nhiều nhãn cùng nghĩa làm hỏng phân tích.

- **`BR-29.6` (Gộp, đổi tên, ngừng dùng):** Quản trị viên PHẢI gộp, đổi tên và ngừng sử dụng được một nhãn hoặc lý do; ngừng dùng thì không chọn được cho hội thoại mới nhưng dữ liệu lịch sử giữ nguyên; báo cáo các kỳ trước KHÔNG ĐƯỢC thay đổi.

  **Lý do nghiệp vụ:** danh mục phải dọn được mà không xóa lịch sử.

- **`BR-29.7` (Ghi vết đổi tên/gộp):** Đổi tên hoặc gộp PHẢI được ghi lại theo `FEAT-25`.

  **Lý do nghiệp vụ:** nó làm thay đổi cách đọc mọi báo cáo dùng tới nhãn/lý do đó.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-29.1.1` | Hộp thư "Bảo hành" có danh mục riêng | Agent giải quyết hội thoại của Hộp thư đó | Chỉ thấy lý do của danh mục "Bảo hành" |
| `AC-29.2.1` | Lý do xử lý bắt buộc | Agent giải quyết không chọn lý do | Nút xác nhận bị vô hiệu tới khi chọn |
| `AC-29.3.1` | Danh mục lý do chuyển tiếp có "Sai chi nhánh" | Chuyển hàng đợi | Chọn được "Sai chi nhánh"; không thấy lý do xử lý trong danh sách |
| `AC-29.4.1` | Quy tắc tự động gắn nhãn "VIP" | Agent gắn thêm "Khiếu nại" | Hội thoại mang cả hai nhãn |
| `AC-29.5.1` | Agent được phép tạo nhãn | Gõ "khieu nai" khi đã có "Khiếu nại" | Hệ thống gợi ý "Khiếu nại" trước khi cho tạo mới |
| `AC-29.6.1` | Gộp "Khiếu nại" và "Phàn nàn" | Mở báo cáo tháng trước | Con số tháng trước không đổi; dữ liệu lịch sử còn |
| `AC-29.7.1` | Đổi tên một nhãn | Tra nhật ký cấu hình | Có dòng ghi tên cũ, tên mới, người đổi |

**Tham chiếu:** `FEAT-29` → issue [#83](https://github.com/crmsaassaudi/product-management/issues/83).

---

### FEAT-30 — Kỹ năng & định tuyến theo kỹ năng

**Mô tả nghiệp vụ:** Cho doanh nghiệp mô tả **Agent nào làm được việc gì** và định tuyến hội thoại theo đó. Chỉ chọn người theo kênh được phép và tải công việc thì khách nói ngôn ngữ khác hoặc hỏi về một dòng sản phẩm chuyên biệt vẫn có thể rơi vào Agent không xử lý được, và cách sửa duy nhất là Agent tự chuyển tiếp bằng tay — đẩy chi phí định tuyến sang con người.

**Vai trò sử dụng chính:** Người có quyền quản trị Quản lý Hộp thư & định tuyến (khai báo danh mục kỹ năng); người có quyền quản trị Gán kỹ năng cho Agent; Hệ thống (áp dụng khi phân công).

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Khai báo danh mục kỹ năng; gán kỹ năng và mức thành thạo cho Agent.
2. Hội thoại xác định kỹ năng đòi hỏi.
3. Phân công ưu tiên Agent đủ kỹ năng trong đơn vị tiếp nhận.

**Quy tắc nghiệp vụ:**

- **`BR-30.1` (Danh mục và gán kỹ năng):** Doanh nghiệp PHẢI khai báo được danh mục kỹ năng riêng (ngôn ngữ, dòng sản phẩm, cấp độ chuyên môn, thẩm quyền xử lý khiếu nại...) và gán kỹ năng cho từng Agent kèm mức thành thạo. Người gán chỉ gán được cho thành viên của đơn vị mà ô (Hội thoại, Gán) của mình bao phủ hội thoại của đơn vị đó, nhất quán với [`tickets-srs.md`](./tickets-srs.md) `BR-11.1`. Gán kỹ năng chỉ ảnh hưởng định tuyến, không cấp quyền xem hay sửa.

  **Lý do nghiệp vụ:** kỹ năng là điều kiện định tuyến, không phải đường cấp quyền; Trưởng nhóm gán kỹ năng cho đội mình không được tác động tới đội khác.

- **`BR-30.2` (Kỹ năng đòi hỏi của hội thoại):** Một hội thoại PHẢI xác định được kỹ năng nó đòi hỏi — từ quy tắc phân công (`BR-04.3`), nhãn, Hộp thư, ngôn ngữ khách hàng, hoặc do Bot xác định trước khi bàn giao.

  **Lý do nghiệp vụ:** không biết hội thoại cần gì thì không định tuyến theo kỹ năng được.

- **`BR-30.3` (Ưu tiên người đủ kỹ năng):** Khi có kỹ năng yêu cầu, hệ thống PHẢI ưu tiên Agent đủ kỹ năng và có mức thành thạo cao hơn, trong số người thỏa `BR-04.1`.

  **Lý do nghiệp vụ:** người rảnh nhất không phải người giải quyết được việc.

- **`BR-30.4` (Không ai đủ kỹ năng):** Doanh nghiệp PHẢI cấu hình được cách xử lý khi **không có ai đủ kỹ năng đang sẵn sàng** (`CFG-30-01`): chờ thêm rồi hạ dần yêu cầu, mở rộng sang nhóm xử lý khác trong cùng đơn vị tiếp nhận, chuyển hàng đợi tới đích đã khai báo (`BR-05.15`), hoặc giữ trong hàng đợi và cảnh báo. Hệ thống KHÔNG ĐƯỢC âm thầm bỏ qua yêu cầu kỹ năng — buộc hạ chuẩn thì hội thoại PHẢI được đánh dấu và người nhận PHẢI biết mình nhận việc ngoài kỹ năng.

  **Lý do nghiệp vụ:** hoặc khách chờ quá lâu, hoặc khách gặp người không xử lý được — doanh nghiệp phải tự chọn và biết mình đã chọn gì.

- **`BR-30.5` (Phân bố kỹ năng so với nhu cầu):** Giám sát viên PHẢI thấy phân bố kỹ năng của đội so với nhu cầu thực tế theo kỳ, trong phạm vi `BR-19.2`.

  **Lý do nghiệp vụ:** đó là căn cứ để đào tạo hoặc tuyển thêm đúng kỹ năng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-30.1.1` | Trưởng nhóm Hà Nội có (Hội thoại, Gán) = Đơn vị và các đơn vị con, có quyền Gán kỹ năng cho Agent | Gán kỹ năng cho một Agent Đà Nẵng | Agent đó không có trong danh sách chọn |
| `AC-30.1.2` | Agent được gán kỹ năng "Tiếng Nhật" | Mở hội thoại của đơn vị khác | Không xem được thêm hội thoại nào chỉ vì có kỹ năng |
| `AC-30.2.1` | Bot xác định khách hỏi về bảo hành | Bot bàn giao | Hội thoại mang kỹ năng đòi hỏi "Bảo hành" |
| `AC-30.3.1` | Khách nhắn tiếng Anh; A rảnh không biết tiếng Anh, B bận vừa phải biết tiếng Anh | Phân công | Hội thoại tới B |
| `AC-30.4.1` | Cấu hình "chờ 2 phút rồi hạ chuẩn" | Không ai đủ kỹ năng trong 2 phút | Hội thoại tới người không đủ kỹ năng, mang dấu "ngoài kỹ năng"; người nhận thấy rõ |
| `AC-30.4.2` | Cấu hình "giữ trong hàng đợi và cảnh báo" | Không ai đủ kỹ năng | Hội thoại chờ; Người phụ trách đơn vị tiếp nhận nhận cảnh báo |
| `AC-30.5.1` | Giám sát viên mở phân bố kỹ năng của tháng | Xem | Thấy số hội thoại cần "Tiếng Anh" so với số giờ trực của Agent có kỹ năng đó |

**Tham chiếu:** `FEAT-30` → issue [#84](https://github.com/crmsaassaudi/product-management/issues/84).

---

### FEAT-31 — Chặn spam & xử lý lạm dụng

**Mô tả nghiệp vụ:** Bảo vệ đội ngũ và số liệu vận hành khỏi tin nhắn rác và người dùng lạm dụng. Nếu spam được đối xử như hội thoại thật thì nó chạy cam kết SLA, sinh vi phạm, kích hoạt leo thang, chiếm năng lực Agent và làm sai lệch báo cáo.

**Vai trò sử dụng chính:** Agent (đánh dấu spam, báo cáo lạm dụng); người có quyền quản trị Chặn người gửi; Hệ thống.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Agent đánh dấu spam hoặc báo cáo lạm dụng.
2. Người có quyền chặn người gửi khi cần.
3. Hệ thống loại spam khỏi cam kết và chỉ số, đếm riêng.

**Quy tắc nghiệp vụ:**

- **`BR-31.1` (Đánh dấu spam):** Người có (Hội thoại, Sửa) bao phủ hội thoại PHẢI đánh dấu được hội thoại là **spam**. Hội thoại spam PHẢI dừng mọi đồng hồ cam kết, không kích hoạt leo thang, không tính vào khối lượng và chỉ số tốc độ — nhưng PHẢI đếm được riêng.

  **Lý do nghiệp vụ:** một đêm spam không được làm hỏng số liệu tháng; nhưng doanh nghiệp vẫn cần biết quy mô spam.

- **`BR-31.2` (Chặn người gửi):** Người có quyền quản trị Chặn người gửi PHẢI chặn được một người gửi, phạm vi theo kênh (chỉ các kênh có đơn vị tiếp nhận nằm trong mức (Hội thoại, Xem) của người chặn) hoặc toàn workspace (cần Người có toàn quyền). Tin từ người bị chặn PHẢI không tạo hội thoại mới và không hiện trong danh sách việc.

  **Lý do nghiệp vụ:** chặn toàn workspace làm mọi đội mất một khách; quyết định đó không thuộc một Trưởng nhóm.

- **`BR-31.3` (Gỡ chặn và ghi vết):** Chặn PHẢI gỡ lại được; mọi lần chặn/gỡ PHẢI ghi ai, lý do gì.

  **Lý do nghiệp vụ:** chặn nhầm khách thật là mất khách trong im lặng, nên phải soi lại được.

- **`BR-31.4` (Cảnh báo bất thường):** Hệ thống PHẢI cảnh báo khi một định danh phát sinh số hội thoại mới bất thường trong thời gian ngắn (`CFG-31-01`), tới Người phụ trách đơn vị tiếp nhận của kênh.

  **Lý do nghiệp vụ:** phát hiện sớm trước khi số liệu bị bóp méo.

- **`BR-31.5` (Báo cáo lạm dụng):** Agent PHẢI báo cáo được hành vi lạm dụng (chửi bới, quấy rối, đe dọa) lên Người phụ trách đơn vị tiếp nhận ngay trong hội thoại. Doanh nghiệp PHẢI cấu hình được xử lý tiếp (`CFG-31-02`): gán cho người có thẩm quyền trong đơn vị, gửi cảnh báo tới khách, hoặc kết thúc hội thoại kèm lý do. Agent KHÔNG ĐƯỢC bị buộc tiếp tục phục vụ hội thoại đã xác nhận là lạm dụng.

  **Lý do nghiệp vụ:** để Agent một mình chịu khách lạm dụng là rủi ro an toàn lao động và giữ người.

- **`BR-31.6` (Hoàn tác spam):** Đánh dấu spam nhầm PHẢI hoàn tác được và hội thoại quay lại phục vụ bình thường, cam kết tính lại từ thời điểm hoàn tác.

  **Lý do nghiệp vụ:** khách thật bị đánh dấu nhầm phải được phục vụ lại ngay.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-31.1.1` | Một đêm 300 hội thoại spam được đánh dấu | Xem báo cáo tháng | Không tăng vi phạm, không giảm tốc độ phản hồi; có dòng "spam: 300" |
| `AC-31.2.1` | Trưởng nhóm có quyền Chặn người gửi | Chặn một số trên Zalo OA của đơn vị mình | Tin mới từ số đó không tạo hội thoại |
| `AC-31.2.2` | Trưởng nhóm không có toàn quyền | Chọn chặn toàn workspace | Lựa chọn bị vô hiệu kèm "cần Người có toàn quyền" |
| `AC-31.3.1` | Người gửi đã bị chặn | Gỡ chặn | Tin mới tạo hội thoại bình thường; nhật ký ghi người gỡ và lý do |
| `AC-31.4.1` | Ngưỡng 10 hội thoại mới trong 5 phút | Một định danh tạo 12 hội thoại | Người phụ trách đơn vị tiếp nhận nhận cảnh báo |
| `AC-31.5.1` | Agent báo cáo lạm dụng; cấu hình "gán cho người có thẩm quyền" | Gửi báo cáo | Hội thoại chuyển cho người có thẩm quyền trong đơn vị; Agent không còn phụ trách |
| `AC-31.6.1` | Hội thoại bị đánh dấu spam nhầm | Hoàn tác | Hội thoại về Đang mở, đồng hồ cam kết chạy từ lúc hoàn tác |

**Tham chiếu:** `FEAT-31` → issue [#85](https://github.com/crmsaassaudi/product-management/issues/85).

---

### FEAT-32 — Yêu cầu liên hệ lại

**Mô tả nghiệp vụ:** Cho khách một lối ra khi doanh nghiệp không phục vụ được ngay — ngoài giờ, hàng đợi quá tải, hoặc kênh đã hết Cửa sổ phản hồi. Chỉ kết thúc bằng một tin tự động thì khách không có cách để lại yêu cầu và không ai chịu trách nhiệm liên hệ lại.

**Vai trò sử dụng chính:** Khách hàng (để lại yêu cầu); Agent (thực hiện, cần (Hội thoại, Tạo) = Có khi phải bắt đầu hội thoại mới tới khách); Trưởng nhóm (theo dõi).

**Điều kiện tiên quyết:** Doanh nghiệp không phục vụ được ngay.

**Luồng chính:**

1. Khách để lại yêu cầu kèm cách liên hệ và khung giờ thuận tiện.
2. Yêu cầu vào hàng đợi của đơn vị tiếp nhận của hội thoại gốc như một việc có cam kết.
3. Agent nhận và liên hệ lại; hội thoại gốc nối tiếp.

**Quy tắc nghiệp vụ:**

- **`BR-32.1` (Để lại yêu cầu):** Khi doanh nghiệp không phục vụ được ngay, khách PHẢI để lại được **Yêu cầu liên hệ lại** kèm cách liên hệ mong muốn và khung giờ thuận tiện.

  **Lý do nghiệp vụ:** khách được hứa liên hệ lại ít bỏ sang đối thủ hơn khách chỉ nhận một tin tự động.

- **`BR-32.2` (Việc có người phụ trách và cam kết):** Yêu cầu liên hệ lại PHẢI là một **bản ghi công việc** thuộc đơn vị tiếp nhận của hội thoại gốc (`BR-05.12`), đi qua đúng cơ chế hàng đợi, phân công và cam kết SLA như hội thoại — KHÔNG ĐƯỢC là một tin trôi trong hàng đợi. Quá hạn PHẢI leo thang theo `FEAT-09`; khi người phụ trách bị tạm ngưng hoặc rời đi, áp `BR-05.14`.

  **Lý do nghiệp vụ:** lời hứa liên hệ lại không có người chịu trách nhiệm là lời hứa sẽ bị quên.

- **`BR-32.3` (Nối tiếp đúng mạch):** Khi liên hệ lại, hội thoại gốc PHẢI được nối tiếp, giữ nguyên lịch sử.

  **Lý do nghiệp vụ:** khách không phải kể lại từ đầu.

- **`BR-32.4` (Xác nhận cho khách):** Khách PHẢI nhận xác nhận yêu cầu đã ghi nhận, kèm khoảng thời gian dự kiến được liên hệ.

  **Lý do nghiệp vụ:** không có xác nhận, khách nhắn lại nhiều lần và tạo việc trùng.

- **`BR-32.5` (Tự khép khi khách quay lại):** Nếu khách chủ động quay lại trước khi được liên hệ, yêu cầu PHẢI tự khép lại.

  **Lý do nghiệp vụ:** tránh liên hệ trùng một việc đã xong.

- **`BR-32.6` (Đo lường):** Doanh nghiệp PHẢI đo được số yêu cầu, tỷ lệ thực hiện đúng hạn và tỷ lệ không liên hệ lại được, trong phạm vi `BR-19.2`.

  **Lý do nghiệp vụ:** đây là thước đo trực tiếp năng lực phục vụ ngoài giờ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-32.1.1` | Khách nhắn lúc 22:00 ngoài giờ | Widget mời để lại yêu cầu | Khách nhập số điện thoại và khung giờ "9h–11h" |
| `AC-32.2.1` | Hội thoại gốc thuộc "Hỗ trợ – Hà Nội" | Yêu cầu được tạo | Yêu cầu thuộc "Hỗ trợ – Hà Nội", vào hàng đợi, có cam kết |
| `AC-32.2.2` | Yêu cầu quá hạn | Tới hạn | Leo thang như hội thoại vi phạm cam kết |
| `AC-32.3.1` | Agent liên hệ lại | Mở yêu cầu | Thấy nguyên nội dung khách đã nhắn tối qua |
| `AC-32.4.1` | Khách để lại yêu cầu | Gửi | Nhận "Chúng tôi sẽ liên hệ trong khung 9h–11h" |
| `AC-32.5.1` | Khách nhắn lại lúc 08:30 trước khi được liên hệ | Hệ thống nhận tin | Yêu cầu tự khép, tin vào hội thoại gốc |
| `AC-32.6.1` | Mở báo cáo tháng | Xem | Thấy số yêu cầu, tỷ lệ đúng hạn, tỷ lệ không liên hệ được |

**Tham chiếu:** `FEAT-32` → issue [#76](https://github.com/crmsaassaudi/product-management/issues/76).

---

### FEAT-33 — Quy tắc tự động hóa

**Mô tả nghiệp vụ:** Một nơi duy nhất để doanh nghiệp tự đặt quy tắc "khi xảy ra việc này, nếu thỏa điều kiện kia, thì làm việc nọ". Nếu tự động hóa nằm rải rác trong phân công, tự động đóng, leo thang và Bot — mỗi cái một mô hình điều kiện — thì mọi nhu cầu tự động hóa mới đều phải chờ nhà cung cấp.

**Vai trò sử dụng chính:** Người có quyền quản trị Quản lý quy tắc tự động hóa; Hệ thống (thực thi).

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Đặt quy tắc Sự kiện → Điều kiện → Hành động.
2. Thử nghiệm (`FEAT-34`), rồi bật.
3. Hệ thống thực thi và ghi vết trên hội thoại.

**Quy tắc nghiệp vụ:**

- **`BR-33.1` (Mô hình quy tắc):** Doanh nghiệp PHẢI tự đặt được quy tắc theo mô hình **Sự kiện → Điều kiện → Hành động**.

  **Lý do nghiệp vụ:** nhu cầu tự động hóa của mỗi doanh nghiệp khác nhau và thay đổi liên tục.

- **`BR-33.2` (Sự kiện):** Tập sự kiện tối thiểu: hội thoại được tạo, khách gửi tin, đổi trạng thái, đổi người phụ trách, trả về hàng đợi, chuyển hàng đợi, gắn/gỡ nhãn, sắp/đã vi phạm cam kết, Bot bàn giao, giải quyết, nhận điểm CSAT.

  **Lý do nghiệp vụ:** thiếu sự kiện thì quy tắc không bắt được đúng thời điểm cần làm.

- **`BR-33.3` (Điều kiện dùng chung):** Tập điều kiện PHẢI dùng chung với `BR-04.3`.

  **Lý do nghiệp vụ:** cùng một khái niệm chỉ định nghĩa một lần trong toàn hệ thống.

- **`BR-33.4` (Hành động và ranh giới quyền):** Tập hành động tối thiểu: gắn/gỡ nhãn, đặt ưu tiên, gán cho người/nhóm xử lý/Hộp thư **trong cùng đơn vị tiếp nhận**, chuyển hàng đợi tới đích đã khai báo (`BR-05.15`), đổi trạng thái, gửi tin cho khách, thông báo nội bộ, áp Chính sách SLA khác, gắn cờ cần chấm (`FEAT-27`), tạo khách hàng tiềm năng từ hội thoại (`BR-02.10`). Quy tắc là tiến trình chạy thay người tạo/bật nó: mỗi hành động chỉ chạm tới hội thoại nằm trong phần giao của mức lúc bật và mức hiện tại của người đó ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`); khi người đó bị tạm ngưng hoặc rời workspace, quy tắc tạm dừng và người quản lý trực tiếp cùng Người có toàn quyền được báo để chuyển người khởi chạy hoặc dừng hẳn ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-43`); kích hoạt lại người đó **không** tự chạy tiếp quy tắc — người đó hoặc Người có toàn quyền chọn chạy tiếp.

  **Lý do nghiệp vụ:** quy tắc tự động hóa không được là đường vòng để làm điều người tạo không tự làm được.

- **`BR-33.5` (Thứ tự và thử trước):** Quy tắc PHẢI có thứ tự ưu tiên, bật/tắt được, và **thử nghiệm được trước khi áp cho khách thật** (`FEAT-34`).

  **Lý do nghiệp vụ:** quy tắc sai chạm thẳng tới khách hàng.

- **`BR-33.6` (Chống vòng lặp và giới hạn):** Hệ thống PHẢI ngăn quy tắc kích hoạt lẫn nhau thành vòng lặp và giới hạn số lần một quy tắc tác động lên cùng một hội thoại trong một khoảng thời gian (`CFG-33-01`).

  **Lý do nghiệp vụ:** một cấu hình sai không được dẫn tới gửi hàng loạt tin cho khách.

- **`BR-33.7` (Vết trên hội thoại):** Mỗi hội thoại PHẢI tra được quy tắc nào đã tác động, lúc nào, kết quả ra sao.

  **Lý do nghiệp vụ:** không thì người vận hành không giải thích được tình trạng hội thoại.

- **`BR-33.8` (Ghi nhật ký thay đổi):** Thay đổi quy tắc PHẢI ghi theo `FEAT-25`.

  **Lý do nghiệp vụ:** quy tắc đổi là cách vận hành đổi.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-33.1.1` | Quản trị viên | Đặt quy tắc "nhãn Khiếu nại + phân khúc VIP → ưu tiên cao nhất + báo Người phụ trách đơn vị" | Lưu và chạy được, không cần nhà cung cấp |
| `AC-33.2.1` | Quy tắc theo sự kiện "trả về hàng đợi" | Một hội thoại được trả về | Quy tắc chạy |
| `AC-33.3.1` | Điều kiện "ngôn ngữ khách hàng" | Mở trình soạn quy tắc tự động hóa | Điều kiện có cùng tên và lựa chọn như ở quy tắc phân công |
| `AC-33.4.1` | Quy tắc có hành động "gán cho Agent" | Chọn Agent ngoài đơn vị tiếp nhận | Agent đó không có trong danh sách |
| `AC-33.4.2` | Người bật quy tắc bị tạm ngưng | Tạm ngưng có hiệu lực | Quy tắc tạm dừng; quản lý trực tiếp và Người có toàn quyền được báo để chuyển người khởi chạy hoặc dừng hẳn |
| `AC-33.4.3` | Tiếp nối AC-33.4.2, người đó được kích hoạt lại | Kích hoạt lại | Quy tắc vẫn tạm dừng tới khi người đó hoặc Người có toàn quyền chọn chạy tiếp |
| `AC-33.5.1` | Quy tắc mới tạo | Chưa thử nghiệm | Có thể chạy thử trước khi bật cho khách thật |
| `AC-33.6.1` | Hai quy tắc gắn/gỡ nhãn qua lại | Kích hoạt | Hệ thống dừng vòng lặp; khách không nhận tin lặp |
| `AC-33.7.1` | Mở hội thoại | Xem | Thấy quy tắc nào đã tác động, lúc nào, kết quả |
| `AC-33.8.1` | Sửa một quy tắc | Tra nhật ký cấu hình | Có dòng ghi trước/sau |

**Tham chiếu:** `FEAT-33` → issue [#86](https://github.com/crmsaassaudi/product-management/issues/86).

---

### FEAT-34 — Chế độ thử nghiệm cấu hình

**Mô tả nghiệp vụ:** Cho quản trị viên thử một thay đổi cấu hình trước khi nó chạm tới khách thật. Định tuyến, SLA, Bot và tự động hóa đều là những thứ mà cấu hình sai gây hậu quả trực tiếp lên khách và chỉ bị phát hiện sau khi đã xảy ra.

**Vai trò sử dụng chính:** Người có quyền quản trị Thử nghiệm cấu hình hội thoại.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Chọn quy tắc/cấu hình cần thử.
2. Chạy trên dữ liệu giả định hoặc đối chiếu với hội thoại quá khứ.
3. Xem kết quả rồi quyết định lưu.

**Quy tắc nghiệp vụ:**

- **`BR-34.1` (Thử trên dữ liệu giả định):** Người có quyền PHẢI thử một quy tắc trên dữ liệu giả định và xem trước kết quả (hội thoại đi đâu, cam kết nào, hành động nào) mà không tác động tới hội thoại thật.

  **Lý do nghiệp vụ:** phát hiện cấu hình sai trước khi khách gặp phải.

- **`BR-34.2` (Đối chiếu quá khứ):** Người có quyền PHẢI đối chiếu một quy tắc mới với hội thoại đã phát sinh để thấy kết quả khác đi thế nào; chỉ trên hội thoại nằm trong mức (Hội thoại, Xem) của mình, nội dung theo `BR-23.9`.

  **Lý do nghiệp vụ:** thử nghiệm không được là đường đọc dữ liệu ngoài phạm vi.

- **`BR-34.3` (Kênh thử nghiệm):** Doanh nghiệp PHẢI có **kênh thử nghiệm** chạy thử toàn bộ hành trình; hội thoại trên kênh thử nghiệm PHẢI bị loại khỏi mọi báo cáo và chỉ số đánh giá Agent.

  **Lý do nghiệp vụ:** dữ liệu thử lẫn vào vận hành làm sai báo cáo.

- **`BR-34.4` (Không gửi tới khách thật):** Thử nghiệm KHÔNG ĐƯỢC gửi bất kỳ tin nào tới khách thật.

  **Lý do nghiệp vụ:** một tin thử lọt tới khách là sự cố uy tín.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-34.1.1` | Sửa quy tắc định tuyến | Bấm thử với hội thoại giả định | Thấy hội thoại sẽ đi đâu trước khi lưu |
| `AC-34.2.1` | Người thử có (Hội thoại, Xem) = Đơn vị của mình | Đối chiếu với tháng trước | Chỉ đối chiếu trên hội thoại thuộc phạm vi của mình |
| `AC-34.3.1` | Hội thoại trên kênh thử nghiệm | Xem báo cáo vận hành | Không có hội thoại đó |
| `AC-34.4.1` | Thử quy tắc có hành động gửi tin | Chạy thử | Không có tin nào tới khách thật |

**Tham chiếu:** `FEAT-34` → issue [#87](https://github.com/crmsaassaudi/product-management/issues/87).

---

### FEAT-35 — Nhật ký truy cập dữ liệu khách hàng

**Mô tả nghiệp vụ:** Lưu vết **ai đã xem nội dung trao đổi của khách hàng nào**. Khác `FEAT-25` (thay đổi cấu hình), mục này ghi việc đọc dữ liệu — điều doanh nghiệp phải chứng minh được khi có khiếu nại rò rỉ, và là điều khoản bắt buộc với đơn vị dịch vụ thuê ngoài.

**Vai trò sử dụng chính:** Người có quyền quản trị Xem nhật ký truy cập dữ liệu khách hàng.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Mỗi lần xem nội dung hội thoại được ghi vết.
2. Người có quyền tra cứu theo người xem, khách hàng, thời gian.

**Quy tắc nghiệp vụ:**

- **`BR-35.1` (Ghi mọi lần xem):** Mọi lần xem nội dung hội thoại của một khách PHẢI được ghi: ai xem, khách nào, lúc nào, qua đường nào (màn hình xử lý, hàng đợi, tìm kiếm, báo cáo bấm xuyên, xuất tệp, chấm chất lượng, Xin ý kiến, xem dữ liệu nhạy cảm đã che), và lượt xem có thuộc diện "đọc ngoài phạm vi phụ trách" theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.9` hay không.

  **Lý do nghiệp vụ:** chứng minh được ai đã tiếp cận dữ liệu là nghĩa vụ khi có khiếu nại.

- **`BR-35.2` (Tra cứu):** Nhật ký PHẢI tra cứu được theo người xem, theo khách hàng và theo khoảng thời gian.

  **Lý do nghiệp vụ:** câu hỏi điều tra luôn bắt đầu từ một người hoặc một khách.

- **`BR-35.3` (Cảnh báo bất thường):** Hệ thống PHẢI cảnh báo khi một người truy cập với khối lượng bất thường so với công việc của họ, hoặc tập trung vào một khách họ không phụ trách (`CFG-35-12`).

  **Lý do nghiệp vụ:** thu thập dữ liệu trái phép thường trông như công việc bình thường nếu không so với khối lượng.

- **`BR-35.4` (Không sửa xóa được):** Nhật ký KHÔNG ĐƯỢC sửa hay xóa bởi bất kỳ ai, kể cả Người có toàn quyền; thời hạn lưu do doanh nghiệp cấu hình (`CFG-35-11`), độc lập với `BR-23.1`.

  **Lý do nghiệp vụ:** không ai được xóa vết của chính mình.

- **`BR-35.5` (Trả lời chủ thể dữ liệu):** Khi khách thực hiện quyền theo `BR-23.3`, doanh nghiệp PHẢI trả lời được ai trong doanh nghiệp đã tiếp cận dữ liệu của họ.

  **Lý do nghiệp vụ:** đó là một phần quyền của chủ thể dữ liệu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-35.1.1` | Một người mở hội thoại qua báo cáo bấm xuyên | Tra nhật ký | Có dòng ghi người, khách, thời điểm, đường "báo cáo" |
| `AC-35.1.2` | Người có Xem rộng hơn Sửa mở hội thoại không do mình phụ trách | Tra nhật ký | Dòng được đánh dấu "đọc ngoài phạm vi phụ trách" |
| `AC-35.2.1` | Khiếu nại rò rỉ của khách X | Lọc theo khách X | Thấy mọi người đã xem và lúc nào |
| `AC-35.3.1` | Một người xem 400 hội thoại trong 1 giờ, gấp nhiều lần bình thường | Hệ thống phát hiện | Sinh cảnh báo |
| `AC-35.4.1` | Người có toàn quyền | Tìm thao tác xóa dòng nhật ký | Không có |
| `AC-35.5.1` | Khách yêu cầu biết ai đã xem dữ liệu của mình | Tra nhật ký theo khách | Xuất được danh sách cho biên bản trả lời |

**Tham chiếu:** `FEAT-35` → issue [#88](https://github.com/crmsaassaudi/product-management/issues/88).

---

### FEAT-36 — Trao đổi nhiều bên trên kênh Email

**Mô tả nghiệp vụ:** Email có tiêu đề, nhiều người cùng nhận, và thường phải kéo bên thứ ba ngoài hệ thống vào cuộc. Mô hình một-khách-một-doanh-nghiệp không diễn đạt được điều đó, trong khi email là kênh chính của nhóm khách hàng doanh nghiệp.

**Vai trò sử dụng chính:** Khách hàng; Agent (cần (Hội thoại, Sửa) bao phủ hội thoại); Bên thứ ba bên ngoài.

**Điều kiện tiên quyết:** Kênh có Thuộc tính trao đổi bằng thư.

**Luồng chính:**

1. Thư đến tạo/nối vào hội thoại theo mạch thư.
2. Agent trả lời, thêm người nhận, chuyển tiếp ra bên thứ ba.
3. Phản hồi của bên thứ ba quay về đúng hội thoại.

**Quy tắc nghiệp vụ:**

- **`BR-36.1` (Tiêu đề và mạch thư):** Hội thoại email PHẢI giữ và hiển thị tiêu đề thư; đổi tiêu đề giữa chừng KHÔNG ĐƯỢC tự tách hội thoại mới nếu vẫn cùng mạch.

  **Lý do nghiệp vụ:** khách hay sửa tiêu đề khi trả lời; tách mạch làm mất ngữ cảnh.

- **`BR-36.2` (Người nhận):** Agent PHẢI thêm được người nhận đồng thời và người nhận ẩn, và PHẢI thấy rõ ai đang có mặt trong mạch trước khi gửi.

  **Lý do nghiệp vụ:** gửi nhầm cho người ngoài là rò rỉ thông tin.

- **`BR-36.3` (Chuyển ra bên thứ ba):** Agent PHẢI chuyển tiếp hội thoại ra bên thứ ba ngoài hệ thống và nhận phản hồi vào đúng hội thoại; nội dung gửi ra áp mẫu che `BR-23.9` theo quyền của Agent. Hội thoại vẫn thuộc đơn vị tiếp nhận của nó.

  **Lý do nghiệp vụ:** mạch trao đổi không được đứt sang hộp thư cá nhân.

- **`BR-36.4` (Phân biệt bên thứ ba):** Khi người ngoài trả lời vào mạch, hệ thống PHẢI phân biệt rõ đó là bên thứ ba, và nội dung đó KHÔNG ĐƯỢC khởi động lại đồng hồ phản hồi với khách.

  **Lý do nghiệp vụ:** thư của nhà cung cấp không phải câu hỏi của khách.

- **`BR-36.5` (Thu gọn trích dẫn):** Nội dung trích dẫn và chữ ký PHẢI được thu gọn.

  **Lý do nghiệp vụ:** Agent cần thấy ngay phần mới.

- **`BR-36.6` (Chữ ký):** Doanh nghiệp PHẢI cấu hình được chữ ký theo kênh hoặc Hộp thư.

  **Lý do nghiệp vụ:** chữ ký là một phần thương hiệu và thông tin liên hệ chính thức.

- **`BR-36.7` (Tách và gộp):** Agent PHẢI tách một thư thành hội thoại riêng khi là vụ việc khác, và gộp hai hội thoại email khi là cùng vụ việc; chỉ gộp được hai hội thoại cùng đơn vị tiếp nhận mà Agent có (Hội thoại, Sửa) bao phủ cả hai; mọi thao tác để lại vết theo `BR-15.4`.

  **Lý do nghiệp vụ:** gộp hội thoại của hai đơn vị là đổi ai được xem mà không qua chuyển hàng đợi.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-36.1.1` | Khách sửa tiêu đề khi trả lời | Thư đến | Vẫn nằm trong cùng hội thoại |
| `AC-36.2.1` | Agent soạn trả lời | Trước khi gửi | Thấy danh sách người nhận, người nhận đồng thời, người nhận ẩn |
| `AC-36.3.1` | Agent kéo bộ phận kỹ thuật bên ngoài vào mạch thư | Kỹ thuật trả lời | Phản hồi vào đúng hội thoại, không sang hộp thư cá nhân |
| `AC-36.4.1` | Bên thứ ba trả lời | Hệ thống nhận | Đánh dấu "bên thứ ba"; đồng hồ phản hồi với khách không khởi động lại |
| `AC-36.5.1` | Thư có 10 lớp trích dẫn | Agent mở | Chỉ thấy phần mới, trích dẫn thu gọn |
| `AC-36.6.1` | Hộp thư có chữ ký riêng | Agent gửi thư | Thư mang chữ ký của Hộp thư |
| `AC-36.7.1` | Hai hội thoại email thuộc hai đơn vị tiếp nhận khác nhau | Agent chọn gộp | Thao tác bị vô hiệu kèm lý do khác đơn vị tiếp nhận |

**Tham chiếu:** `FEAT-36` → issue [#89](https://github.com/crmsaassaudi/product-management/issues/89).

---

## 4. Yêu cầu phi chức năng

### 4.1 Bảo mật & Phân quyền

- **`NFR-1`** (Xác thực nguồn tin nhắn): Mọi tin tự xưng đến từ một kênh PHẢI được xác thực thực sự đến từ đúng nền tảng trước khi xử lý và hiển thị.
- **`NFR-2`** (Cách ly dữ liệu theo doanh nghiệp): Hội thoại, khách hàng, cấu hình của một doanh nghiệp không bao giờ hiển thị hoặc trộn lẫn với doanh nghiệp khác.
- **`NFR-3`** (Phân quyền truy cập media/tệp): Tệp đính kèm chỉ truy cập được bởi người có quyền xem đầy đủ nội dung hội thoại đó theo `BR-23.9`.
- **`NFR-4`** (Bảo mật thông tin kết nối kênh): Thông tin kết nối kênh không bao giờ hiển thị lại cho bất kỳ ai sau khi lưu, và không lộ qua màn hình, báo cáo, tệp xuất hay thông báo lỗi nào.
- **`NFR-18`** (Một hợp đồng quyền): Mọi quyết định truy cập của phân hệ — ai thấy, ai sửa, ai nhận việc, ai xem đầy đủ — PHẢI giải thích được bằng ô × mức, nguồn nới phạm vi, nguồn chặn và che dữ liệu theo đúng thứ tự [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`; phân hệ không có cơ chế quyền riêng thứ hai. Lỗi khi tính quyền dẫn tới kết quả hẹp hơn, không rộng hơn.

### 4.2 Toàn vẹn & Nhất quán dữ liệu

- **`NFR-5`** (Không ghi nhận trùng): Một tin hoặc hành động của khách không bao giờ được ghi nhận hai lần — trong khung chat, lịch sử hay báo cáo.
- **`NFR-6`** (Không mất tin nhắn khách hàng): Mọi tin khách đã gửi thành công trên kênh PHẢI đến được Agent, kể cả khi hệ thống gián đoạn tạm thời; tiếp nhận chậm bất thường PHẢI có cảnh báo.
- **`NFR-7`** (Không gửi trùng khi không chắc chắn): Không xác định được tin đã tới khách hay chưa thì KHÔNG ĐƯỢC tự gửi lại (`BR-12.4`).
- **`NFR-8`** (Quyền xem là duy nhất, ở mọi nơi): Dữ liệu một người không được xem tại màn hình xử lý thì không được lộ ở bất kỳ nơi nào khác: hàng đợi, báo cáo, bấm xuyên xuống, tìm kiếm, thông báo, gợi ý khớp khách hàng, bản gửi định kỳ, tệp xuất, quy tắc tự động hóa, chế độ thử nghiệm.

### 4.3 Hiệu năng & Vận hành

- **`NFR-9`** (Số liệu đúng, hoặc nhìn thấy được là đang thiếu): Ghi lịch sử và số liệu không làm chậm việc phục vụ; số liệu thiếu PHẢI nhận biết được ngay trên báo cáo (`BR-15.5`).
- **`NFR-10`** (Một doanh nghiệp không ảnh hưởng doanh nghiệp khác): Chất lượng phục vụ của một doanh nghiệp không suy giảm vì khối lượng tăng đột biến của doanh nghiệp khác.
- **`NFR-11`** (Kết quả rõ ràng khi nhiều người cùng thao tác): Kết quả cuối cùng PHẢI rõ ràng và giống nhau với mọi người đang nhìn (`FEAT-13`).
- **`NFR-12`** (Quy mô không làm đổi cách vận hành): Doanh nghiệp hàng trăm Agent, hàng chục Hộp thư và đơn vị tiếp nhận, hàng chục nghìn hội thoại mỗi ngày PHẢI dùng đúng các quy tắc này; thao tác không trả kết quả kịp PHẢI nói rõ và cho cách khác.
- **`NFR-19`** (Thu hồi có hiệu lực ngay): Thu hẹp quyền (tạm ngưng, chuyển hàng đợi sang đơn vị khác, gỡ khỏi đơn vị, tắt "gồm các đơn vị con") có hiệu lực từ thao tác kế tiếp của người bị ảnh hưởng trên mọi màn hình đang mở (`BR-21.2`), và không bị chặn bởi sự cố nhật ký (`BR-25.5`).

### 4.4 Ngôn ngữ & Trải nghiệm

- **`NFR-13`** (Đa ngôn ngữ giao diện): Giao diện Agent và widget PHẢI hiển thị theo ngôn ngữ người dùng chọn/trình duyệt của khách.
- **`NFR-14`** (Ngôn ngữ của cuộc trò chuyện): Ngôn ngữ khách đang dùng PHẢI nhận biết được và dùng được làm điều kiện định tuyến (`BR-04.3`, `FEAT-30`).

### 4.5 Dữ liệu khách hàng

- **`NFR-15`** (Không giữ dữ liệu quá cam kết): Không giữ nội dung và tệp quá thời hạn lưu trữ (`FEAT-23`), trừ phần đang Tạm dừng xóa (`BR-23.7`) có căn cứ ghi rõ.
- **`NFR-16`** (Xóa là xóa thật): Dữ liệu đã xóa theo yêu cầu PHẢI không còn truy cập được ở bất kỳ đâu — khung chat, tìm kiếm, gợi ý khớp, bấm xuyên xuống, tệp xuất — và doanh nghiệp PHẢI có bằng chứng hoàn tất.
- **`NFR-17`** (Truy cập dữ liệu khách hàng luôn có vết): Mọi lần xem nội dung hội thoại PHẢI để vết (`FEAT-35`), kể cả khi trong phạm vi quyền.

---

## 5. Ma trận quyền truy cập tính năng

Phân hệ khai báo phần quyền của mình theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-24`, `FEAT-25`: các ô của loại dữ liệu **Hội thoại** (mức theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-34`) và các **quyền quản trị** của phân hệ (có/không). Danh sách vai trò dựng sẵn là của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`; bảng dưới chỉ khai báo giá trị mặc định chi tiết của loại Hội thoại cho các vai trò đó. Doanh nghiệp điều chỉnh từng ô của vai trò dựng sẵn qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02`, hoặc tạo vai trò tự tạo. Không năng lực nào của phân hệ gắn cứng cho một tên vai trò hay vị trí công việc.

### 5.1 Ô của loại dữ liệu Hội thoại và mức mặc định

| Ô (Hội thoại × thao tác) | Ý nghĩa nghiệp vụ | Nhân viên Hỗ trợ | Quản lý | Kiểm toán | Chỉ xem bản ghi được giao | Nhân viên Kinh doanh, Marketing, Quản lý Marketing, Kiểm toán quyền |
| --- | --- | --- | --- | --- | --- | --- |
| Xem | Danh sách, chi tiết, tìm kiếm, báo cáo, bảng điều hành (`BR-13.2`, `BR-16.1`, `BR-19.2`, `BR-26.2`) | Đơn vị của mình | Đơn vị và các đơn vị con | Toàn workspace | Chỉ của mình | Không có |
| Tạo | Chủ động bắt đầu hội thoại mới tới khách (liên hệ lại bằng tin mẫu, `FEAT-32`) | Có | Có | Không có | Không có | Không có |
| Sửa | Trả lời, ghi chú, đổi trạng thái, gắn nhãn, đánh dấu spam, liên kết hồ sơ, can thiệp tự động đóng | Chỉ của mình | Đơn vị và các đơn vị con | Không có | Không có | Không có |
| Gán | Chỉ của mình: nhận việc, trả về hàng đợi, đề nghị chuyển, chuyển hàng đợi hội thoại mình phụ trách (`BR-05.5`, `BR-05.14`, `BR-05.15`, `BR-07.3`). Từ Đơn vị của mình trở lên: thêm gán thẳng, Chuyển hẳn cho một Agent, gán lại hàng loạt (`BR-07.3`, `BR-26.6`) | Chỉ của mình | Đơn vị và các đơn vị con | Không có | Không có | Không có |
| Xuất | Xuất nội dung hội thoại ra tệp (`BR-23.4`) | Không có | Không có | Không có | Không có | Không có |
| Xoá, Nhập | Không mở chức năng nào: hội thoại không xóa từng cái bằng tay (chỉ xóa theo `FEAT-23`) và không nhập từ tệp | — | — | — | — | — |
| Xem toàn văn khi chưa nhận *(thao tác đặc thù)* | Đọc toàn văn hội thoại chưa có Người phụ trách (`BR-05.7`, `BR-23.9`) | Không có | Đơn vị và các đơn vị con | Toàn workspace | Không có | Không có |
| Can thiệp *(thao tác đặc thù)* | Nhắc bài, tiếp quản (`BR-26.4`, `BR-26.5`, `BR-12.5`) | Không có | Đơn vị và các đơn vị con | Không có | Không có | Không có |
| Chấm chất lượng *(thao tác đặc thù)* | Chấm điểm, hiệu chuẩn (`FEAT-27`) | Không có | Không có | Không có | Không có | Không có |

Mọi ô tuân [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.1` (không thao tác nào rộng hơn Xem). Thao tác đặc thù hiển thị thành dòng riêng ở chế độ Cơ bản ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.4`). Ngoài mức theo ô, phạm vi còn được nới bởi nguồn hàng đợi (`BR-05.5`) và quyền đọc tự động hồ sơ khách hàng khi đang xử lý hội thoại ([`contacts-srs.md`](./contacts-srs.md) `BR-35.4`), và bị chặn bởi lượt chặn trên bản ghi, theo thứ tự [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`. Vai trò Nhân viên Hỗ trợ mặc định không có (Khách hàng, Tạo) nên không tạo được khách hàng tiềm năng từ hội thoại (`BR-02.10`); doanh nghiệp muốn Agent làm việc đó điều chỉnh ô qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02`. Phân hệ không khai báo Sàn bắt buộc trên ô nào (`BR-23.9`).

### 5.2 Quyền quản trị của phân hệ

| Quyền quản trị | Phạm vi nghiệp vụ | Mặc định trên vai trò dựng sẵn |
| --- | --- | --- |
| Quản lý kênh hội thoại | Kết nối, ngắt, cấu hình kênh và Thuộc tính kênh (`FEAT-01`); không gồm đặt/đổi đơn vị tiếp nhận — việc đó luôn cần Người có toàn quyền (`BR-01.9`) | Không có |
| Quản lý Hộp thư & định tuyến | Hộp thư, tập Agent được phân công, quy tắc phân công, danh mục kỹ năng, ngưỡng chờ (`FEAT-04`, `FEAT-05`, `FEAT-17`, `FEAT-30`) — chỉ cho hàng đợi mà ô (Hội thoại, Gán) của người đó bao phủ hội thoại của hàng đợi, nhất quán với [`tickets-srs.md`](./tickets-srs.md) `BR-10.6`. Danh sách đích chuyển hàng đợi và tự chuyển khi quá ngưỡng chỉ Người có toàn quyền (`BR-25.2`) | Không có |
| Quản lý cam kết phục vụ | Chính sách SLA, leo thang, tự động đóng (`FEAT-08` – `FEAT-10`) | Không có |
| Quản lý giờ làm việc hội thoại | Lịch riêng của kênh/Hộp thư, lịch nghỉ (`FEAT-24`) | Không có |
| Quản lý Bot | Chế độ Bot theo kênh (`FEAT-11`) | Không có |
| Quản lý danh mục lý do & nhãn | `FEAT-29` | Không có |
| Gán kỹ năng cho Agent | `BR-30.1` — chỉ cho thành viên của đơn vị mà ô (Hội thoại, Gán) của người đó bao phủ | Quản lý |
| Quản lý quy tắc tự động hóa | `FEAT-33` | Không có |
| Thử nghiệm cấu hình hội thoại | `FEAT-34` | Không có |
| Quản lý mẫu tin nhắn dùng chung | `FEAT-18` | Quản lý (phạm vi đơn vị của mình) |
| Quản lý chất lượng hội thoại | Phiếu chấm, lấy mẫu, hiệu chuẩn (`FEAT-27`) | Không có |
| Xếp ca trực | `FEAT-28` — chỉ cho thành viên của đơn vị mà ô (Hội thoại, Gán) của người đó bao phủ | Quản lý |
| Chặn người gửi | `BR-31.2` | Quản lý |
| Gửi báo cáo định kỳ | `BR-19.6` | Quản lý |
| Quản lý lưu trữ dữ liệu hội thoại | Thời hạn lưu trữ, danh mục dữ liệu nhạy cảm (`BR-23.1`, `BR-23.8`). Không gồm đặt/gỡ Tạm dừng xóa — việc đó chỉ Người có toàn quyền hoặc Người phụ trách Bảo vệ Dữ liệu (`BR-23.7`) | Không có |
| Xem dữ liệu nhạy cảm trong hội thoại | Chỉ có tác dụng khi doanh nghiệp lưu bản gốc (`BR-23.9`, `CFG-23-04`); chỉ Người có toàn quyền cấp | Không có |
| Xem nhật ký cấu hình Omnichat | `FEAT-25` | Kiểm toán, Kiểm toán quyền |
| Xem nhật ký truy cập dữ liệu khách hàng | `FEAT-35` | Kiểm toán |

Người có toàn quyền có mọi quyền trên. Thực hiện yêu cầu xóa dữ liệu của chủ thể dữ liệu đi theo thẩm quyền của [`contacts-srs.md`](./contacts-srs.md) `FEAT-33` (`BR-23.3`).

### 5.3 Vị trí công việc → cách thiết lập

| Vị trí | Thiết lập gợi ý |
| --- | --- |
| Agent | Vai trò Nhân viên Hỗ trợ; là thành viên (Đơn vị chính hoặc kiêm nhiệm) của đơn vị tiếp nhận các kênh mình trực |
| Trưởng nhóm | Vai trò Quản lý; Người phụ trách đơn vị của đội |
| Giám sát viên | Vai trò Quản lý; Người phụ trách đơn vị cấp trên trong nhánh, nên Đơn vị và các đơn vị con phủ nhiều đội |
| Chuyên viên chất lượng | Vai trò tự tạo: (Hội thoại, Xem) và (Hội thoại, Chấm chất lượng) = Đơn vị và các đơn vị con hoặc Toàn workspace; Sửa, Gán, Can thiệp = Không có; quyền quản trị Quản lý chất lượng hội thoại. Tính độc lập được bảo đảm bởi `BR-27.8`, không bởi tên vai trò |
| Quản trị viên | Cấp bậc Quản trị viên, hoặc thành viên được cấp các quyền quản trị ở Mục 5.2 |

Ba nguyên tắc đọc mục này:

1. **Phân tách trách nhiệm là cấu hình, không phải mặc định ẩn.** Người cấu hình hệ thống ở doanh nghiệp nhỏ cũng là người trực máy chỉ khi được cấp các ô Hội thoại tương ứng.
2. **Không ai tự nhận việc ngoài đơn vị tiếp nhận.** Nhận việc luôn cần là thành viên đơn vị tiếp nhận và có ô Gán (`BR-05.5`).
3. **Không ai xóa được vết của chính mình.** Không vai trò nào, kể cả Người có toàn quyền, sửa hay xóa được nội dung của `FEAT-25` và `FEAT-35`.

---

## 6. Kịch bản chấp nhận tổng hợp

1. **Khách hàng cũ nhắn lại đúng người quen:** Khách từng được Agent A hỗ trợ qua Facebook Messenger nhắn lại trong thời hạn ưu tiên. A trực tuyến, vẫn thuộc đơn vị tiếp nhận của Trang Facebook và còn năng lực → hội thoại gán thẳng cho A, không qua hàng đợi (`BR-04.2`).
2. **Nhận diện sai kênh không tự động gộp nhầm:** Khách nhắn qua Instagram với số điện thoại trùng một khách VIP có sẵn (thực chất là người khác, trùng số hotline). Hệ thống chỉ hiển thị gợi ý "có vẻ trùng khớp" — với số điện thoại đã che — Agent kiểm tra thấy không đúng nên bỏ qua, giữ hai hồ sơ riêng (`BR-02.4`).
3. **Bot bàn giao đúng lúc, đúng người:** Khách nhắn "tôi muốn gặp nhân viên" khi đang chat với Bot. Người/nhóm Bot chỉ định không còn hợp lệ (đã nghỉ việc/đổi ca) → hội thoại vào hàng đợi của đơn vị tiếp nhận, không bàn giao vào chỗ trống và không đổi đơn vị (`BR-11.3`).
4. **SLA tạm dừng đúng lúc khách im lặng:** Agent trả lời xong, khách không phản hồi 2 giờ, Agent Tạm hoãn. Đồng hồ phản hồi lần sau dừng; khách nhắn lại → hội thoại về Đang mở và đồng hồ tiếp tục (`BR-08.7`, `BR-03.7`).
5. **Tự động đóng có cảnh báo trước:** Hội thoại không hoạt động 24 giờ theo chính sách; hệ thống hỏi khách còn cần hỗ trợ không, chờ 30 phút. Khách phản hồi → hội thoại tiếp tục bình thường (`BR-10.3`, `BR-10.4`).
6. **Bảo vệ khi hàng loạt hội thoại cùng lúc đủ điều kiện đóng:** Trục trặc phía kênh khiến rất nhiều hội thoại cùng đạt ngưỡng. Ngưỡng an toàn kích hoạt, hoãn bớt và cảnh báo Người phụ trách các đơn vị tiếp nhận bị ảnh hưởng kèm số hội thoại; hội thoại bị hoãn được đóng trong thời hạn cấu hình; mở hội thoại bất kỳ là thấy lý do (`BR-10.7`, `BR-10.8`).
7. **CSAT và khối lượng nhìn được cùng một chỗ:** Giám sát viên thấy khối lượng tuần này trên WhatsApp tăng đột biến và ngay trong cùng báo cáo thấy CSAT kênh đó giảm — trong đúng phạm vi các đội mình phụ trách (`BR-14.4`, `BR-19.2`).
8. **Hoạch định nhân sự dựa trên AHT đúng bản chất kênh:** Giám sát viên tính số Agent cần thêm cho đội Live Chat riêng với đội nhắn tin mạng xã hội, vì AHT đồng thời và không đồng thời được tách riêng (`BR-20.2`).
9. **Số điện thoại dùng chung không kéo theo gộp nhầm:** Một đại lý dùng chung một số WhatsApp cho nhiều nhân viên. Agent đánh dấu số đó là Định danh dùng chung; từ đó mọi tin từ số này chỉ hiển thị gợi ý khớp, kể cả trên WhatsApp (`BR-02.4b`).
10. **Hội thoại ưu tiên không âm thầm tụt cam kết:** Hội thoại thuộc Hộp thư khách ưu tiên phát sinh đúng lúc không xác định được cấu hình riêng. Hội thoại vẫn được tiếp nhận, đánh dấu đang chạy theo mặc định, Người phụ trách đơn vị nhận cảnh báo; cấu hình đúng được áp lại khi xác định được; đơn vị tiếp nhận không đổi (`BR-17.3`).
11. **Khách yêu cầu xóa dữ liệu:** Khách từng nhắn qua Facebook (đơn vị Hà Nội) và WhatsApp (đơn vị Đà Nẵng) yêu cầu xóa. Người thực hiện theo quy trình chủ thể dữ liệu xóa một lần cho cả hai, nhận xác nhận hoàn tất; số liệu tổng hợp của kỳ đó vẫn còn (`BR-23.3`, `BR-23.2`).
12. **Phát hiện ùn tắc trước khi khách chờ quá hạn:** Đầu giờ chiều, hàng đợi Live Chat tăng nhanh vì hai Agent nghỉ đột xuất. Bảng điều hành chạm ngưỡng, Trưởng nhóm nhận thông báo và gán lại hàng loạt việc của hai người vắng sang người còn rảnh trong đơn vị — hoặc chuyển phần vượt sang hàng đợi dự phòng của chi nhánh khác đã khai báo trong danh sách đích (`BR-26.3`, `BR-26.6`, `BR-05.15`).
13. **Đánh giá Agent không chỉ bằng tốc độ:** Agent đứng đầu về số hội thoại trong tháng có điểm chấm thấp (thường đóng khi khách chưa được giải đáp hết) và CSAT thấp hơn trung bình. Bảng xếp hạng phản ánh cả ba mặt nên Agent không đứng đầu (`BR-20.5`).
14. **Kết ca không bỏ lại việc:** Agent (Gán = Chỉ của mình) hết ca lúc 18h với 6 hội thoại mở. Hệ thống liệt kê đủ 6, đề xuất người ca sau trong cùng đơn vị tiếp nhận, yêu cầu ghi chú bàn giao. Agent đề nghị chuyển 4 hội thoại và trả 2 về hàng đợi; 3 đề nghị được chấp nhận, đề nghị còn lại chưa ai nhận nên hội thoại đó về hàng đợi khi Agent kết thúc ca. Sáng hôm sau khách nhắn tiếp gặp đúng người đã nhận (`BR-28.4`).
15. **Một đợt spam không làm hỏng số liệu tháng:** Hàng trăm tin rác vào kênh Facebook trong một đêm. Agent đánh dấu spam, Trưởng nhóm (có quyền Chặn người gửi) chặn nguồn gửi trên kênh của đơn vị mình; hội thoại spam dừng cam kết, không leo thang, không vào chỉ số tốc độ; doanh nghiệp vẫn thấy quy mô spam (`FEAT-31`).
16. **Doanh nghiệp tự đổi quy trình mà không cần nhà cung cấp:** Quản trị viên tự đặt quy tắc tự động hóa cho hội thoại gắn nhãn "khiếu nại" của khách VIP, thử trên dữ liệu giả định và đối chiếu với hội thoại tháng trước trong phạm vi của mình, rồi mới bật (`FEAT-33`, `FEAT-34`).
17. **Khách ngoài giờ không rơi vào khoảng trống:** Khách nhắn lúc 22h. Widget báo không có ai trực và mời để lại yêu cầu liên hệ lại. Sáng hôm sau yêu cầu xuất hiện trong hàng đợi của đơn vị tiếp nhận như một việc có cam kết; Agent liên hệ lại và thấy nguyên nội dung tối qua (`FEAT-32`).
18. **Hàng đợi chung cho cả nhánh và tự nhận việc:** Website chung có một widget Live Chat tiếp nhận vào "Hỗ trợ", Người có toàn quyền bật "gồm các đơn vị con". Agent ở "Hỗ trợ – Hà Nội" và "Hỗ trợ – Đà Nẵng" đều thấy hàng đợi ở dạng tóm tắt; Agent Đà Nẵng tự nhận một hội thoại (thao tác Gán) và đọc được toàn văn sau khi nhận; một nhân viên kinh doanh không thuộc nhánh Hỗ trợ không thấy hàng đợi đó (`BR-05.5`, `BR-05.7`, `BR-05.13`).
19. **Agent bị tạm ngưng giữa ca:** Agent A bị tạm ngưng lúc 15:00 khi đang giữ 2 phiên Live Chat và 4 hội thoại email, đúng lúc nhật ký đang sự cố. Tạm ngưng có hiệu lực ngay và 2 phiên Live Chat — loại cần phản hồi tức thời — về hàng đợi ngay trong lượt tạm ngưng, nhật ký được ghi bù. Lượt xử lý 4 hội thoại email là thao tác riêng, nới rộng: khi nhật ký còn sự cố thì bị hủy kèm thông báo; khi phục hồi, người thực hiện xác nhận "trả về hàng đợi", 4 hội thoại về hàng đợi của đơn vị tiếp nhận của từng hội thoại, thời gian chờ và SLA chạy tiếp (`BR-05.14`, `BR-06.6`, `BR-25.5`).
20. **Agent nghỉ việc:** Quy trình rời workspace của B có bước "Hội thoại đang phụ trách: 12". Người thực hiện chọn trả 10 hội thoại về hàng đợi và giao 2 hội thoại khách VIP cho C cùng đơn vị tiếp nhận; chọn D ở đơn vị khác bị từ chối. Biên bản rời workspace ghi đủ (`BR-05.14`).
21. **Khách nhắn nhầm chi nhánh:** Khách ở Đà Nẵng nhắn vào Zalo OA của chi nhánh Hà Nội. Trưởng nhóm Hà Nội chuyển hội thoại sang hàng đợi Đà Nẵng (nằm trong danh sách đích) kèm lý do "Sai chi nhánh"; hội thoại thuộc "Hỗ trợ – Đà Nẵng", khách được báo đang được chuyển tới chi nhánh Đà Nẵng, SLA giữ mốc bắt đầu cũ; Agent Hà Nội không còn thấy hội thoại theo mức Đơn vị của mình (`BR-05.15`).
22. **Khách hàng tiềm năng từ hội thoại tới đúng đội kinh doanh:** Khách vãng lai trên Live Chat để lại số điện thoại hợp lệ. Hồ sơ tạm trở thành khách hàng tiềm năng chưa có Người phụ trách thuộc đơn vị tiếp nhận khách hàng của kênh ("Kinh doanh"), vào hàng đợi phân bổ của phân hệ Khách hàng; Agent hỗ trợ vẫn đọc được hồ sơ khi đang xử lý hội thoại nhưng không trở thành người phụ trách khách hàng (`BR-02.10`).
23. **Che dữ liệu nhạy cảm trong hội thoại:** Khách gõ số thẻ thanh toán và số điện thoại vào chat. Agent phụ trách thấy "[số thẻ đã che]" và số điện thoại che theo mức của phân hệ Khách hàng; tìm kiếm theo số thẻ không ra hội thoại; tệp xuất và trợ lý AI chỉ nhận giá trị đã che (`BR-23.8`, `BR-23.9`).
24. **Agent tự trả việc hoặc đề nghị chuyển:** Agent A (Gán = Chỉ của mình) không xử lý được một hội thoại kỹ thuật. A không có "Chuyển hẳn cho một Agent", nên đề nghị chuyển cho chuyên gia H — H kiêm nhiệm đơn vị tiếp nhận; H chấp nhận và trở thành Người phụ trách. Một hội thoại khác A trả về hàng đợi kèm lý do. Trưởng nhóm (Gán = Đơn vị của mình) chuyển hẳn một hội thoại cho C cùng đơn vị mà không cần C chấp nhận (`BR-07.3`).
25. **Từ hội thoại sang vé:** Hội thoại Zalo của chi nhánh Hà Nội đã được chuyển sang "Hỗ trợ – Đà Nẵng". Khách cần đổi bảo hành nhiều ngày; Agent Đà Nẵng tạo vé từ hội thoại; vé vào hàng đợi Đà Nẵng, mang số điện thoại đã che, và liên kết hai chiều với hội thoại (`BR-21.12`).

---

## 7. Giới hạn hiện tại & vấn đề tồn đọng

Mục này chỉ chứa nhu cầu nghiệp vụ có thật nhưng **chưa chốt được phương án**. Mọi điểm đã chốt được đặc tả tại Mục 3 và ghi ở Phụ lục C.

| # | Nhu cầu / câu hỏi cần quyết định | Vì sao chưa quyết được là rủi ro | Liên quan |
| --- | --- | --- | --- |
| 1 | **Quy tắc chi tiết của Vụ việc khách hàng.** Mô hình hai tầng Phiên trên kênh / Vụ việc đã chốt tại ADR-0005; chưa chốt: vụ việc thuộc đơn vị tiếp nhận nào khi các phiên đến từ kênh của các đơn vị khác nhau, và cách chuyển SLA, CSAT, phân công, báo cáo sang đo trên vụ việc. | Áp vụ việc mà không chốt đơn vị sở hữu thì một vụ việc có thể làm hội thoại của đơn vị này lộ sang đơn vị kia; `BR-02.9` chỉ là mức bảo vệ tối thiểu. | `BR-02.9`, `FEAT-08`, `FEAT-14`, `FEAT-19`, [ADR-0005](../docs/adr/0005-omnichat-work-unit-session-vs-case.md) |
| 2 | **Cam kết thời gian phản hồi có được dùng làm cam kết hợp đồng với khách hàng cuối không?** | Đơn vị dịch vụ thuê ngoài ký cam kết có ràng buộc tài chính; mở ra thì kéo theo yêu cầu bằng chứng, loại trừ và phê duyệt điều chỉnh số liệu. | `FEAT-08`, `FEAT-19` |
| 3 | **Thời hạn lưu trữ mặc định** cho nội dung hội thoại và tệp đính kèm. | Đây là cam kết thương mại và pháp lý; chưa có mặc định thì không bán được cho khách có yêu cầu tuân thủ. Tham số vẫn do doanh nghiệp đặt (`CFG-23-01`). | `FEAT-23` |
| 4 | **Ngưỡng thời gian chờ tối đa khuyến nghị** trước khi hội thoại phải có người nhận. | Mỗi doanh nghiệp tự đặt (`CFG-05-05`) nhưng sản phẩm chưa có mức khuyến nghị, nên không cảnh báo được cấu hình bất hợp lý. | `BR-05.3`, `BR-05.6`, `BR-05.9` |
| 5 | **Khi hết Cửa sổ phản hồi trên kênh không có tin nhắn mẫu, được chủ động liên hệ khách qua kênh khác tới mức nào?** | Dùng định danh khách để lại ở kênh A để liên hệ qua kênh B là quyết định về sự đồng ý của khách, cần chốt ở cấp sản phẩm/pháp lý cùng [`contacts-srs.md`](./contacts-srs.md) `FEAT-30`. | `BR-12.9`, `FEAT-32` |
| 6 | **Khách có được sửa điểm CSAT đã chấm không?** | Ảnh hưởng trực tiếp độ tin cậy của CSAT dùng để đánh giá Agent. | `BR-14.2`, `BR-20.3` |
| 7 | **"Chờ đóng" có nên là trạng thái hiển thị ra báo cáo không?** | Tách riêng làm thay đổi cách đọc phân bố trạng thái và có thể làm số "đang mở" thấp hơn thực tế. | `FEAT-03`, `BR-03.8`, `FEAT-19` |
| 8 | **Điểm chất lượng và CSAT dùng để thưởng/phạt hay chỉ để kèm cặp?** | Hai cách dùng dẫn tới hai thiết kế khác nhau về tỷ lệ lấy mẫu, phúc khảo, minh bạch. | `FEAT-27`, `BR-20.5` |
| 9 | **Khi không có Agent đủ kỹ năng, mặc định khuyến nghị là chờ hay hạ chuẩn?** | Chọn sai là khách chờ quá lâu hoặc gặp người không xử lý được. | `BR-30.4` |
| 10 | **Tiếp nhận tin trả lời qua Zalo ZNS và đầu số SMS hai chiều.** | Khách trả lời chiến dịch qua hai đường này hiện không thành hội thoại; cần chốt cùng [`campaigns-srs.md`](./campaigns-srs.md) Mục 1.6. | `FEAT-01`, `FEAT-02` |
| 11 | **Phân tích cảm xúc và ý định khách hàng từ nội dung trao đổi.** | Không tự phát hiện được hội thoại đang xấu đi để can thiệp sớm; lấy mẫu chấm chất lượng phải dựa vào dấu hiệu gián tiếp. Phải tương thích với việc Tác nhân AI chỉ nhận nội dung đã che (`BR-23.9`). | `FEAT-15`, `BR-27.2` |
| 12 | **Gợi ý nội dung trả lời, tóm tắt hội thoại, dịch tự động cho Agent.** | Cùng ràng buộc với câu 11: một trợ lý AI chỉ nhận nội dung đã che thì không tóm tắt được nội dung; cần quyết định cùng chủ tài liệu IAM. | `FEAT-18`, `BR-23.9` |
| 13 | **Các hình thức tương tác đặc thù theo kênh** (trả lời story, bình luận hoặc tin phát sinh từ quảng cáo) có xử lý như một hội thoại đầy đủ không. | Bỏ ngoài thì mất khách; đưa vào thì phải chốt đơn vị tiếp nhận và cam kết cho từng loại. | `FEAT-01`, `FEAT-02` |
| 14 | **Lưu trữ dữ liệu theo vùng địa lý.** | Điều kiện tiên quyết với một số ngành và thị trường; quyết định ở cấp sản phẩm, không phải tham số của doanh nghiệp. | `FEAT-23` |

**Tham chiếu:** câu 1 → [ADR-0005](../docs/adr/0005-omnichat-work-unit-session-vs-case.md) (issue [#90](https://github.com/crmsaassaudi/product-management/issues/90)).

---

## Phụ lục A — Danh mục Khái niệm Nghiệp vụ

> Đây là mô tả nghiệp vụ, không phải thiết kế dữ liệu, và không mang tính ràng buộc. Cách tổ chức lưu trữ thuộc thẩm quyền đội phát triển.

| Khái niệm | Thuộc tính nghiệp vụ chính | Quan hệ |
| --- | --- | --- |
| Kênh | Loại kênh, Thuộc tính kênh, tình trạng phục vụ, đơn vị tiếp nhận hội thoại, đơn vị tiếp nhận khách hàng, "gồm các đơn vị con", lịch riêng | Thuộc một workspace; thuộc tối đa một Hộp thư |
| Hàng đợi hội thoại | Đơn vị tiếp nhận, danh sách đích chuyển được phép, cách phân phối | Là một Hộp thư hoặc một kênh độc lập |
| Hộp thư | Tập Agent được phân công, quy tắc phân công, Chính sách SLA, Bot, lịch, chữ ký | Gộp các kênh cùng đơn vị tiếp nhận |
| Hội thoại | Trạng thái, Người phụ trách, đơn vị tiếp nhận, nhãn, lý do xử lý, chu kỳ giải quyết, cam kết | Bản ghi công việc; gắn với một kênh và một hồ sơ khách hàng |
| Lời mời nhận hội thoại | Agent được mời, thời hạn, kết quả | Thuộc một hội thoại |
| Yêu cầu liên hệ lại | Cách liên hệ, khung giờ, hạn cam kết | Bản ghi công việc; nối với hội thoại gốc |
| Lượt chuyển hàng đợi | Hàng đợi nguồn, đích, người chuyển, lý do, thời điểm | Thuộc một hội thoại |
| Chính sách SLA / leo thang / tự động đóng | Loại cam kết, thời hạn, phân khúc, hành động | Áp cho hội thoại qua Hộp thư hoặc mặc định |
| Kỹ năng | Tên, mức thành thạo | Gán cho Agent; đòi hỏi bởi hội thoại |
| Ca trực | Agent, khung giờ, hàng đợi | Đối chiếu với trạng thái làm việc |
| Phiếu chấm chất lượng | Tiêu chí, trọng số, tiêu chí nghiêm trọng | Kết quả chấm gắn với hội thoại và người chấm |
| Nhật ký truy cập dữ liệu khách hàng | Người xem, khách hàng, thời điểm, đường xem, đọc ngoài phạm vi phụ trách | Chỉ ghi, không sửa xóa |

---

## Phụ lục B — Danh mục Tham số Cấu hình theo Workspace

**Mức độ tự do:** **Tự do** — đặt bất kỳ giá trị nào trong miền. **Có sàn bắt buộc** — có giới hạn không nới được, kèm lý do. Thẩm quyền ghi "Q: …" là quyền quản trị tại Mục 5.2. Mã `CFG-05-02` không dùng trong tài liệu này để tránh nhầm với mã cũ đã chuyển thành [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02`; tham số của `FEAT-29`, `FEAT-35` đánh số từ 11 để không trùng số với tham số cùng nhóm của tài liệu IAM. Đơn vị tiếp nhận mặc định theo loại kênh là tham số của tài liệu IAM ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-35-01`), không lặp lại ở đây.

| Mã | Quy tắc | Nội dung | Mặc định | Miền giá trị | Thẩm quyền | Mức độ tự do |
| --- | --- | --- | --- | --- | --- | --- |
| `CFG-02-01` | `BR-02.3`, `BR-02.4b` | Bật/tắt tự động gộp Hồ sơ khách hàng tạm | Bật | Bật / Tắt | Q: Quản lý kênh hội thoại | Tự do |
| `CFG-04-01` | `BR-04.7` | Điểm tải theo Thuộc tính kênh và tổng điểm tối đa của Agent | Do doanh nghiệp đặt khi cấu hình định tuyến | Số dương theo từng nhóm kênh | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-04-02` | `BR-04.4` | Chiến lược chọn Agent | Vòng lần lượt | Vòng lần lượt / Ít việc nhất / Tải trọng số / Chỉ hàng đợi | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-04-03` | `BR-04.2` | Thời hạn ưu tiên người phụ trách trước đó | Do doanh nghiệp đặt | Số ngày, hoặc tắt | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-05-01` | `BR-05.4` | Cách phân phối hội thoại đang chờ | Gửi lời mời | Gán thẳng / Gửi lời mời / Chỉ hiển thị | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-05-03` | `BR-05.2` | Thời hạn phản hồi lời mời | Do doanh nghiệp đặt | Số giây | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-05-04` | `BR-05.3` | Số lượt mời không thành công trước khi quay lại hàng đợi | Do doanh nghiệp đặt | Số nguyên dương | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-05-05` | `BR-05.3`, `BR-05.6` | Ngưỡng thời gian chờ tối đa của khách | Do doanh nghiệp đặt (Mục 7 câu 4) | Số phút | Q: Quản lý Hộp thư & định tuyến (trong phạm vi Gán) | Tự do |
| `CFG-05-06` | `BR-05.9` | Cách báo thời gian chờ cho khách | Thời gian chờ dự kiến | Thời gian dự kiến / Vị trí / Tắt (thay bằng xác nhận đã tiếp nhận) | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-05-07` | `BR-05.7`, `BR-23.9` | Độ dài trích đoạn tin đầu tiên khi chưa nhận | Do doanh nghiệp đặt | Số ký tự, tối đa một tin | Q: Quản lý Hộp thư & định tuyến | **Có sàn bắt buộc** — không quá toàn bộ tin đầu tiên, để trích đoạn không thành đường đọc toàn văn |
| `CFG-05-08` | `BR-05.15`, `BR-05.6` | Danh sách hàng đợi đích được phép chuyển tới, theo từng hàng đợi nguồn | Rỗng (không chuyển được tới khi khai báo) | Tập con các hàng đợi | Người có toàn quyền; trong Phiên triển khai có hiệu lực ngay với hàng đợi chưa có hội thoại, chỉ soạn sẵn với hàng đợi đã có hội thoại. Thêm đích là nới rộng, đóng khi lỗi (`BR-25.2`); bỏ đích là thu hẹp, không bị chặn khi nhật ký lỗi (`BR-25.5`) | Tự do |
| `CFG-05-09` | `BR-05.14` | Thời hạn cảnh báo khi hội thoại của người bị tạm ngưng/rời đi chưa được xử lý | 15 phút | Số phút | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-05-10` | `BR-05.8` | Nhận hội thoại mới vào hàng đợi ngoài giờ | Nhận | Nhận / Không nhận | Q: Quản lý giờ làm việc hội thoại | Tự do |
| `CFG-05-11` | `BR-05.6` | Bật tự chuyển sang hàng đợi dự phòng của đơn vị khác khi quá ngưỡng chờ | Tắt | Bật / Tắt, theo hàng đợi | Người có toàn quyền; thuộc nhật ký thay đổi cấu hình quyền, đóng khi lỗi (`BR-25.2`) | Tự do |
| `CFG-06-01` | `BR-06.3` | Khoảng chờ trước khi kết luận Ngoại tuyến | Do doanh nghiệp đặt | Số giây/phút | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-06-02` | `BR-06.5` | Thời lượng Xử lý sau hội thoại | Tắt | Tắt, hoặc số giây theo Thuộc tính kênh | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-06-03` | `BR-06.1` | Trạng thái làm việc tự định nghĩa | Không có | Tên; nhận việc có/không; tính thời gian làm việc có/không | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-07-01` | `BR-07.4` | Thời hạn phản hồi lời mời Xin ý kiến (đề nghị chuyển dùng [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-25-01`) | 5 phút | 1 – 60 phút | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-07-05` | `BR-07.6` | Thời lượng tối đa của một lượt Xin ý kiến | 60 phút | 5 phút – 24 giờ | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-07-02` | `BR-07.9` | Lý do chuyển tiếp bắt buộc | Không bắt buộc | Bắt buộc / Không | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-07-03` | `BR-07.10` | Thông báo cho khách khi đổi người phụ trách/hàng đợi | Bật | Bật / Tắt, theo kênh | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-07-04` | `BR-07.4` | Mặc định riêng của hạn đề nghị chuyển ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-25-01`) cho hội thoại trên kênh trò chuyện đồng thời | 5 phút | Theo miền của tham số IAM (5 phút – 14 ngày) | Người có toàn quyền | Tự do |
| `CFG-08-01` | `BR-08.1` | Chính sách SLA | Không có chính sách | Loại cam kết × phân khúc × thời hạn | Q: Quản lý cam kết phục vụ | Tự do |
| `CFG-08-02` | `BR-08.12` | Thời gian im lặng tối đa giữa chừng | Tắt | Số phút, theo kênh đồng thời | Q: Quản lý cam kết phục vụ | Tự do |
| `CFG-10-01` | `BR-10.1` | Chính sách tự động đóng | Không có chính sách | Điều kiện, thời gian, cảnh báo, ân hạn, trạng thái đích | Q: Quản lý cam kết phục vụ | Tự do |
| `CFG-10-02` | `BR-10.7` | Thời hạn tối đa hoãn đóng do Ngưỡng an toàn | 10 phút | Số phút | Q: Quản lý cam kết phục vụ | Tự do |
| `CFG-11-01` | `BR-11.2` | Chế độ Bot theo kênh | Tắt Bot | Bot trước rồi bàn giao / Bot toàn bộ / Tắt | Q: Quản lý Bot | Tự do |
| `CFG-11-02` | `BR-11.6` | Tự tắt Bot khi Agent trả lời | Bật | Bật / Tắt | Q: Quản lý Bot | Tự do |
| `CFG-12-01` | `BR-12.8` | Thời hạn chờ ở trạng thái "đang gửi" | Do doanh nghiệp đặt | Số giây | Q: Quản lý kênh hội thoại | Tự do |
| `CFG-12-02` | `BR-12.9` | Thời điểm cảnh báo trước khi hết Cửa sổ phản hồi | Do doanh nghiệp đặt | Số giờ trước hạn | Q: Quản lý cam kết phục vụ | Tự do |
| `CFG-14-01` | `BR-14.2` | Thời hạn hiệu lực lời mời khảo sát | 7 ngày | Số ngày | Q: Quản lý cam kết phục vụ | Tự do |
| `CFG-20-01` | `BR-20.4` | Ngưỡng tối thiểu để vào bảng xếp hạng | Do doanh nghiệp đặt | Số giờ trực, số hội thoại | Q: Quản lý chất lượng hội thoại | Tự do |
| `CFG-21-01` | `BR-21.5` | Ghi chú giải quyết bắt buộc | Không bắt buộc | Bắt buộc / Không | Q: Quản lý cam kết phục vụ | Tự do |
| `CFG-22-01` | `BR-22.3` | Thông tin khách phải điền trước khi chat | Không yêu cầu | Tập trường | Q: Quản lý kênh hội thoại | Tự do |
| `CFG-22-02` | `BR-22.9` | Lời mời chủ động của widget và giới hạn số lần | Tắt | Điều kiện hành vi; số lần tối đa mỗi khách | Q: Quản lý kênh hội thoại | Tự do |
| `CFG-23-01` | `BR-23.1` | Thời hạn lưu trữ nội dung và tệp đính kèm | Chưa chốt mặc định (Mục 7 câu 3) | Số tháng, tối đa theo gói | Q: Quản lý lưu trữ dữ liệu hội thoại | Tự do |
| `CFG-23-02` | `BR-23.2` | Thời hạn lưu số liệu tổng hợp | Do doanh nghiệp đặt | Số năm | Q: Quản lý lưu trữ dữ liệu hội thoại | Tự do |
| `CFG-23-03` | `BR-23.8` | Danh mục dữ liệu nhạy cảm cần phát hiện | Số thẻ thanh toán, mã xác thực một lần | Tập loại dữ liệu, gồm mẫu do doanh nghiệp khai báo | Q: Quản lý lưu trữ dữ liệu hội thoại | Tự do |
| `CFG-23-04` | `BR-23.9` | Lưu bản gốc dữ liệu nhạy cảm đã che | Không lưu | Không lưu / Lưu có kiểm soát | Người có toàn quyền | Tự do |
| `CFG-23-05` | `BR-23.5` | Thời gian lấy dữ liệu khi ngừng dùng | Theo hợp đồng | Số ngày | Chủ sở hữu | Tự do |
| `CFG-24-01` | `BR-24.3` | Lịch làm việc riêng của kênh/Hộp thư | Dùng lịch workspace | Lịch theo ngày, ngày nghỉ | Q: Quản lý giờ làm việc hội thoại | Tự do |
| `CFG-26-01` | `BR-26.3` | Ngưỡng cảnh báo vận hành theo hàng đợi | Tắt | Số khách chờ, thời gian chờ lâu nhất, số Agent sẵn sàng tối thiểu | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-27-01` | `BR-27.2` | Quy tắc lấy mẫu chấm chất lượng | Tắt | Số lượng/tỷ lệ mỗi Agent mỗi kỳ, điều kiện lọc | Q: Quản lý chất lượng hội thoại | Tự do |
| `CFG-29-11` | `BR-29.2` | Lý do xử lý bắt buộc khi giải quyết | Không bắt buộc | Bắt buộc / Không, theo Hộp thư | Q: Quản lý danh mục lý do & nhãn | Tự do |
| `CFG-29-12` | `BR-29.5` | Ai được tạo nhãn mới | Chỉ người có quyền quản trị | Chỉ quản trị / Mọi người có (Hội thoại, Sửa) | Q: Quản lý danh mục lý do & nhãn | Tự do |
| `CFG-30-01` | `BR-30.4` | Xử lý khi không ai đủ kỹ năng | Giữ trong hàng đợi và cảnh báo | Hạ dần / Mở rộng nhóm / Chuyển hàng đợi / Giữ và cảnh báo | Q: Quản lý Hộp thư & định tuyến | Tự do |
| `CFG-31-01` | `BR-31.4` | Ngưỡng hội thoại mới bất thường của một định danh | Do doanh nghiệp đặt | Số hội thoại trong số phút | Q: Chặn người gửi | Tự do |
| `CFG-31-02` | `BR-31.5` | Xử lý khi xác nhận lạm dụng | Gán cho người có thẩm quyền | Gán / Cảnh báo khách / Kết thúc kèm lý do | Q: Quản lý cam kết phục vụ | Tự do |
| `CFG-33-01` | `BR-33.6` | Giới hạn số lần một quy tắc tác động lên một hội thoại | Do doanh nghiệp đặt | Số lần trong khoảng thời gian | Q: Quản lý quy tắc tự động hóa | **Có sàn bắt buộc** — luôn có giới hạn, không tắt được, để cấu hình sai không gửi hàng loạt tin cho khách |
| `CFG-35-11` | `BR-35.4` | Thời hạn lưu nhật ký truy cập dữ liệu khách hàng | Do doanh nghiệp đặt | Số năm, tối đa theo gói | Chủ sở hữu | Tự do |
| `CFG-35-12` | `BR-35.3` | Ngưỡng truy cập bất thường | Do doanh nghiệp đặt | Hệ số so với khối lượng bình thường | Q: Xem nhật ký truy cập dữ liệu khách hàng | Tự do |

---

## Phụ lục C — Nhật ký Mâu thuẫn & Quyết định đã chốt

| # | Mâu thuẫn / vấn đề | Quyết định | Áp tại |
| --- | --- | --- | --- |
| C.1 | Tài liệu dùng nhãn trạng thái triển khai và câu văn tiến độ ("hiện hệ thống chưa…") | Bỏ toàn bộ nhãn; mô tả nghiệp vụ viết lại thành vấn đề nghiệp vụ; mọi BR là yêu cầu bắt buộc như nhau | Toàn tài liệu |
| C.2 | Không có quy định hội thoại thuộc đơn vị nào, nên không áp được mức Đơn vị của mình và hàng đợi theo IAM v5.0 | Hội thoại là bản ghi công việc của đơn vị tiếp nhận hội thoại của kênh, suốt vòng đời | `BR-01.9`, `BR-05.12` |
| C.3 | Phải khai báo đơn vị tiếp nhận cho từng nguồn | Mỗi kênh (Zalo OA, Trang Facebook, widget Live Chat, hộp thư email…) có hai đơn vị tiếp nhận: hội thoại và khách hàng; mặc định theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-35-01`, đặt/đổi theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.12` | `BR-01.9` |
| C.4 | Hộp thư vừa là danh sách phân công vừa ngầm là danh sách quyền xem; có thể gộp kênh của hai đơn vị | Hộp thư là một hàng đợi thuộc đúng một đơn vị tiếp nhận; gán Agent vào Hộp thư chỉ quyết định phân công, không quyết định quyền xem | `BR-17.1`, `BR-17.4`, `BR-05.11` |
| C.5 | Hàng đợi dùng chung nhiều chi nhánh | Hỗ trợ "gồm các đơn vị con" theo kênh, chỉ Người có toàn quyền bật | `BR-05.13` |
| C.6 | Tự nhận việc chỉ cần "đủ điều kiện kênh và năng lực" | Tự nhận là thao tác Gán: cần là thành viên đơn vị tiếp nhận và (Hội thoại, Gán) khác Không có; trả lời hội thoại chưa có người phụ trách cũng là tự nhận | `BR-05.5`, `BR-12.5` |
| C.7 | Người phụ trách bị tạm ngưng/rời đi không có quy tắc cho hội thoại | Trả về hàng đợi của đơn vị tiếp nhận của hội thoại (đề xuất sẵn), hoặc người xử lý thay/người nhận đủ điều kiện; hội thoại trò chuyện đồng thời tự trả về khi Agent ngoại tuyến; cảnh báo nếu chưa xử lý sau `CFG-05-09` | `BR-05.14`, `BR-06.6` |
| C.8 | Chuyển hội thoại sang đội khác không có khai báo đích được phép | Chuyển hàng đợi theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.14`, chỉ tới danh sách đích `CFG-05-08`, bắt buộc lý do; quy tắc phân công, Bot, leo thang không được tự đổi đơn vị | `BR-05.15`, `BR-04.3`, `BR-11.3`, `BR-09.2`, `BR-07.2` |
| C.9 | Agent tạo "hồ sơ CRM chính thức" từ hội thoại và tự thành người phụ trách | Hồ sơ tạm và khách hàng tiềm năng sinh ra từ hội thoại là bản ghi chờ phân công của phân hệ Khách hàng, thuộc đơn vị tiếp nhận khách hàng của kênh; người tạo cần (Khách hàng, Tạo) và không tự thành người phụ trách | `BR-02.10`, `BR-02.5` |
| C.10 | Ma trận quyền gắn năng lực cho tên vị trí (Agent, Trưởng nhóm…) với ký hiệu ✅/⚪ | Thay bằng ô Hội thoại × mức và quyền quản trị của phân hệ; giá trị mặc định cho các vai trò dựng sẵn của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`; điều chỉnh qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02`; vị trí công việc chỉ là gợi ý thiết lập | Mục 5, Mục 1.4, Mục 2.3 |
| C.11 | Mục 7 câu "Phân quyền trường áp tới đâu với dữ liệu khách hàng hiển thị trong Omnichat" chưa quyết | Chốt bằng khai báo trường nhạy cảm theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-40`: nội dung tin nhắn (che trích đoạn khi chưa có người phụ trách), số điện thoại/email theo mức của phân hệ Khách hàng, dữ liệu nhạy cảm khách gõ vào (mặc định không ai xem đầy đủ) | `BR-23.9`, `BR-05.7` |
| C.12 | Bot đọc nội dung tin nhắn trong khi Tác nhân AI luôn bị che | Bot không phải Tác nhân AI theo IAM; chỉ đọc đúng hội thoại được giao, không nhận dữ liệu nhạy cảm đầy đủ; tác nhân AI dùng trong kịch bản Bot vẫn chịu [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-40.1` | `BR-11.7`, `BR-23.9` |
| C.13 | Nhật ký cấu hình quyền của Omnichat áp "Nguyên tắc đóng" cho mọi thao tác, kể cả thu hồi | Nới rộng/trung tính đóng khi lỗi ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.5`); thu hẹp không bị chặn, ghi bù ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.6`, ADR-0010); danh sách thao tác ai-thấy-gì cập nhật theo mô hình mới | `BR-25.2`, `BR-25.5` |
| C.14 | Mục 7 chứa câu đã chốt (đơn vị công việc — ADR-0005) và mục "Ranh giới hiện tại của sản phẩm" mang tính hiện trạng | Mô hình hai tầng ghi là đã chốt; Mục 7 chỉ giữ phần chưa chốt (đơn vị sở hữu vụ việc, đo SLA/CSAT trên vụ việc); các ranh giới hiện trạng chuyển thành nhu cầu chưa chốt (câu 10–14) hoặc Ngoài phạm vi ở Mục 1.2 | Mục 7, Mục 1.2 |
| C.15 | Lịch chung của Omnichat có thể khác lịch làm việc của workspace | Lịch chung là lịch làm việc của workspace ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-04`); Omnichat chỉ thêm lịch riêng kênh/Hộp thư; quy ước thời gian chốt ở Mục 2.4 | `BR-24.1`, Mục 2.4 |
| C.16 | Người chấm chất lượng độc lập chỉ được bảo đảm bằng tên vai trò | Quy tắc chung: không chấm hội thoại mình từng phụ trách, tư vấn hoặc can thiệp | `BR-27.8` |
| C.17 | Thông báo, gợi ý khớp, bản gửi định kỳ, quy tắc tự động hóa và thử nghiệm là đường lộ dữ liệu ngoài phạm vi | Mỗi đường đi theo mức Xem của người nhận/người chạy và mẫu che `BR-23.9`; quy tắc tự động hóa chạy trong phần giao theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8` | `BR-02.4`, `BR-09.5`, `BR-19.6`, `BR-33.4`, `BR-34.2`, `NFR-8` |
| C.18 | Gộp hai hội thoại email khác đơn vị là đường vòng của chuyển hàng đợi | Chỉ gộp được hội thoại cùng đơn vị tiếp nhận | `BR-36.7` |
| C.19 | Nối lại hội thoại cũ trên widget chỉ bằng email/số điện thoại khách tự gõ | Phải xác minh khách sở hữu định danh | `BR-22.1` |
| C.20 | Người phụ trách có Gán = Chỉ của mình "Chuyển hẳn" thẳng cho người khác, trái [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6` | Người phụ trách luôn có trả về hàng đợi, đề nghị chuyển (có hiệu lực khi người nhận chấp nhận và tự đạt điều kiện nhận việc, hết hạn theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-25-01`) và chuyển hàng đợi; gán thẳng cần Gán từ Đơn vị của mình | `BR-07.3`, `BR-07.4`, Mục 5.1 |
| C.21 | Người nhận ngoài đơn vị tiếp nhận được làm Người phụ trách, lệch [`tickets-srs.md`](./tickets-srs.md) `BR-12.6` | Căn chỉnh với tickets: người nhận phải là thành viên đơn vị tiếp nhận, Đang hoạt động, Xem được hội thoại, không bị chặn; người ngoài đội chỉ tham gia qua Xin ý kiến | `BR-07.2`, `BR-05.12` |
| C.22 | Người xử lý thay/người nhận khi tạm ngưng, rời đi không chịu trần tự nhận | Thêm [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.2`, `BR-43.4`; tự gán hội thoại đang có người phụ trách khác cũng chịu [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.4` | `BR-05.14`, `BR-07.3` |
| C.23 | Danh sách đích chuyển hàng đợi và tự chuyển khi quá ngưỡng nới phạm vi cho cả một đơn vị nhưng do quản trị định tuyến đặt | Chỉ Người có toàn quyền; thuộc nhật ký thay đổi cấu hình quyền, đóng khi lỗi; tách `CFG-05-11` khỏi `CFG-05-05` | `BR-25.2`, `BR-05.6`, `CFG-05-08`, `CFG-05-11` |
| C.24 | Mọi lượt trả hội thoại về hàng đợi khi tạm ngưng đều được coi là thu hẹp | Chỉ lượt Hệ thống trả hội thoại đồng thời ngay trong lượt tạm ngưng là thu hẹp (ghi bù); xử lý sau đó là nới rộng, đóng khi lỗi theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.5` | `BR-25.5`, `BR-06.6`, Kịch bản 19 |
| C.25 | Đổi đơn vị mặc định trả hội thoại về hàng đợi | Là bước bắt buộc trong danh sách [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-11.9`, `BR-11.5`, không chọn sẵn; "giữ Người phụ trách" chỉ khi vẫn là thành viên đơn vị tiếp nhận | `BR-05.14` (c) |
| C.26 | Hội thoại đồng thời có thể bị "giữ nguyên" khi người phụ trách bị tạm ngưng | Khai báo là loại cần phản hồi tức thời theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-42.4` | `BR-05.14` (a), `BR-06.6` |
| C.27 | Một hộp thư email có thể là nguồn của cả Omnichat và Vé hỗ trợ; tickets bị coi là hệ thống bên ngoài | Mỗi hộp thư là nguồn của đúng một phân hệ, chọn khi kết nối; vé hỗ trợ là phân hệ trong CRM, nối qua "tạo vé từ hội thoại" vào đơn vị hiện tại của hội thoại theo [`tickets-srs.md`](./tickets-srs.md) `BR-34.2`, nội dung giữ giá trị đã che | `BR-01.10`, `BR-21.12`, Mục 2.3 |
| C.28 | Bot luôn được đọc nội dung đầy đủ | Bước kịch bản cố định đọc như người phụ trách; bước đưa dữ liệu cho mô hình AI là Tác nhân AI theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4, chỉ nhận giá trị đã che (thay phần tương ứng của C.12) | `BR-11.7`, `BR-23.9`, Mục 1.4 |
| C.29 | Quyền định tuyến, gán kỹ năng, xếp ca không giới hạn phạm vi | Giới hạn theo ô (Hội thoại, Gán), nhất quán với [`tickets-srs.md`](./tickets-srs.md) `BR-10.6`, `BR-11.1` | Mục 5.2, `BR-28.1`, `BR-30.1` |
| C.30 | Quy tắc tự động hóa của người bị tạm ngưng tự chạy lại; không báo quản lý | Báo quản lý trực tiếp và Người có toàn quyền; kích hoạt lại không tự chạy tiếp theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8` | `BR-33.4` |
| C.31 | Tiếp quản có đổi Người phụ trách hay không chưa rõ | Không đổi; nhận hẳn về mình là thao tác gán theo `BR-07.3` | `BR-26.5` |
| C.32 | Vé tạo từ hội thoại chép giá trị che theo quyền người tạo, có thể là giá trị đầy đủ | Luôn dạng che hạn chế nhất, nhất quán với [`tickets-srs.md`](./tickets-srs.md) `BR-45.12` | `BR-21.12` |
| C.33 | Kết ca với Gán = Chỉ của mình chưa rõ đường bàn giao | Đề nghị chuyển (chưa được chấp nhận thì về hàng đợi khi kết thúc ca) hoặc trả về hàng đợi; giao thẳng cần Gán từ Đơn vị của mình | `BR-28.4`, Kịch bản 14 |
| C.34 | Danh sách đích chuyển hàng đợi mặc định "mọi hàng đợi" là nới phạm vi ngầm (quyết định chung với tickets) | Mặc định rỗng; Người có toàn quyền khai báo, kể cả trong Phiên triển khai | `CFG-05-08`, `BR-05.15` |
| C.35 | Đổi phân hệ hộp thư bằng ngắt rồi kết nối lại tạo khoảng trống mất thư (quyết định chung với tickets) | Một thao tác "Chuyển phân hệ" của Người có toàn quyền, không khoảng trống, dữ liệu cũ ở lại, ghi nhật ký cấu hình quyền | `BR-01.10`, `BR-25.2` |
| C.36 | Hạn đề nghị chuyển mặc định 3 ngày quá dài cho hội thoại trực tiếp | Mặc định riêng 5 phút cho kênh đồng thời (mức thấp nhất của miền IAM, nên không đặt 2 phút) | `BR-07.4`, `CFG-07-04` |
| C.37 | Không có quy tắc khi kiêm nhiệm hết hạn | Hội thoại đồng thời về hàng đợi ngay; loại khác chỉ đề xuất trả về | `BR-05.16` |
| C.38 | Kích hoạt lại không đề xuất trả hội thoại về người cũ | Đề xuất tới người xử lý thay và quản lý trực tiếp, không gồm hội thoại đã về hàng đợi | `BR-05.14` |
| C.39 | Xin ý kiến cho phép chuyển hẳn quyền phụ trách cho người tư vấn, kể cả người ngoài đội; quyền đọc của người tư vấn không có cơ sở trong mô hình quyền | Người mở: Người phụ trách hoặc người có Sửa bao phủ; người được mời Đang hoạt động, Xem khác Không có, không bị chặn; quyền đọc là lượt cấp mức trần Chỉ đọc theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.5`, thu hồi khi kết thúc; bỏ lựa chọn chuyển hẳn, giao việc dùng đề nghị chuyển | `BR-07.6`, `BR-07.3`, `BR-23.9` |
| C.40 | Tạm dừng xóa làm yêu cầu xóa im lặng hoặc báo "đã xóa" sai sự thật | Trạng thái "Đang tạm dừng theo yêu cầu pháp lý" trả về biên bản, yêu cầu chưa hoàn tất, gỡ thì thực thi ngay, theo [ADR-0008](../docs/adr/0008-data-subject-deletion-contract-omnichat-tickets.md) và [`tickets-srs.md`](./tickets-srs.md) `BR-45.10` | `BR-23.7`, `BR-23.3` |
| C.41 | Đặt Tạm dừng xóa là quyền quản trị cấp được (quyết định chung với tickets) | Chỉ Người có toàn quyền hoặc Người phụ trách Bảo vệ Dữ liệu; không cấp được cho người khác | `BR-23.7`, Mục 5.2 |
| C.42 | Kiêm nhiệm hết hạn: trả về ngay hội thoại đồng thời chưa có căn cứ trong IAM | Dẫn chiếu ngoại lệ cho loại cần phản hồi tức thời của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-11.10` | `BR-05.16` |
| C.43 | Danh sách đích chuyển hàng đợi trong Phiên triển khai và khi nhật ký lỗi chưa rõ | Hàng đợi đã có hội thoại chỉ soạn sẵn; bỏ đích là thu hẹp, không bị chặn | `CFG-05-08`, `BR-05.15` |
| C.44 | Chuyển phân hệ sang Omnichat chỉ cần đơn vị tiếp nhận hội thoại | Cần cả hai đơn vị tiếp nhận | `BR-01.10` |
| C.45 | Che dữ liệu với Tác nhân AI diễn đạt khác nhau giữa các tài liệu (quyết định chung) | Dùng chung cụm "hằng số hệ thống" | `BR-23.9` |
| C.46 | Kết ca và tự chuyển khi leo thang dùng điều kiện người nhận riêng; tự chuyển không giới hạn theo phạm vi của người cấu hình | Dẫn chiếu thẳng `BR-07.2`; tự chuyển chỉ trong mức Gán của người cấu hình, nhất quán với tickets | `BR-28.4`, `BR-09.2` |
| C.47 | Lượt Xin ý kiến có thể kéo dài sau khi hội thoại đổi người phụ trách, về hàng đợi hoặc sang đơn vị khác; chưa rõ có tính là yêu cầu treo không; thời hạn lời mời không có mặc định | Tự kết thúc ở các sự kiện đó và khi quá thời lượng tối đa `CFG-07-05`; chỉ lời mời chưa được nhận là yêu cầu treo; `CFG-07-01` mặc định 5 phút, miền 1 – 60 phút | `BR-07.6`, `BR-07.7`, `CFG-07-01`, `CFG-07-05` |
