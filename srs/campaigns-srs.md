# SRS — Phân hệ Quản lý Chiến dịch Tiếp thị & Truyền thông Đa kênh (Marketing Campaigns & Mass Messaging)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA (Version 2.0) |
| **Module** | CRM — Phân hệ Quản lý Chiến dịch Tiếp thị & Truyền thông Đa kênh (Marketing Campaigns & Mass Messaging) |
| **Ngày cập nhật** | 2026-08-28 |
| **Phiên bản** | v2.0 (Target Standard) |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`contacts-srs.md`](./contacts-srs.md), [`omnichat-srs.md`](./omnichat-srs.md), [`deals-pipeline-srs.md`](./deals-pipeline-srs.md), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) |

## Ghi chú về nguồn gốc tài liệu

Tài liệu này được xây dựng thông qua quy trình chuẩn hoá 4 bước:
1. **Khảo sát toàn diện mã nguồn thực tế (As-Is):** Khảo sát toàn bộ hệ thống API, schemas, audience filtering services, dispatch/send queues (BullMQ), campaign cancel ledgers, sender management và test send runners trong `crm-api` (`src/campaigns/`) và `crm-web` (`src/features/campaigns/`).
2. **Rà soát & Đối chiếu Chuyên sâu:** Kiểm tra chéo từng quy tắc lọc phân khúc đối tượng (`CampaignAudienceService`), tính toán số lượng tiếp cận khả dụng (`estimatedReachable`), phân tách quyền hạn phát sóng (`campaigns:launch` vs `campaigns:edit`), sổ cái người nhận (`campaign_recipients`) và cơ chế tự động xử lý hủy đăng ký (Unsubscribe / Consent).
3. **Chuẩn hoá Nghiệp vụ Business First:** Đối chiếu với các chuẩn mực B2B SaaS Marketing Automation hàng đầu thế giới (HubSpot Marketing Hub, Mailchimp, ActiveCampaign, Braze, Klaviyo), loại bỏ các rào cản kỹ thuật và xây dựng hệ thống tiếp thị đa kênh chuyên nghiệp.
4. **Đóng băng Đặc tả Mục tiêu:** Hoàn thiện bộ 30 tính năng nghiệp vụ cốt lõi, ma trận phân quyền, 6 kịch bản UAT và danh mục chính sách nghiệp vụ.

**Quy ước nhãn trạng thái:** Mỗi tính năng (FEAT) và quy tắc nghiệp vụ (BR) được gắn nhãn trạng thái:
- **`[Đã triển khai]`** — Phản ánh các tính năng nền tảng đã sẵn sàng và đang vận hành thực tế trong hệ thống.
- **`[Yêu cầu mới]`** — Các tính năng và quy tắc nâng cấp chuẩn Business To-Be được bổ sung để hoàn thiện trải nghiệm tiếp thị đa kênh toàn diện.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả chi tiết toàn bộ nghiệp vụ lập kế hoạch, lọc phân khúc khách hàng, soạn thảo thông điệp cá nhân hóa, phát sóng hàng loạt và đo lường hiệu quả các chiến dịch truyền thông đa kênh:
1. **Quản trị Chiến dịch Tiếp thị Đa kênh (Multi-Channel Campaigns):** Hỗ trợ phát sóng thông điệp qua 4 kênh chủ lực: **Email Marketing**, **WhatsApp Broadcast**, **Zalo ZNS / Zalo OA** và **SMS Brandname**.
2. **Phân khúc Khách hàng Thông minh & Tính toán Khả dụng (Audience Segmentation & Reachability):** Lọc đối tượng mục tiêu theo nhiều tiêu chí kết hợp và tự động tính toán số lượng người nhận khả dụng thực tế sau khi loại trừ các địa chỉ lỗi hoặc từ chối nhận tin.
3. **Cá nhân hóa Nội dung Động (Dynamic Personalization):** Tự động trộn các trường thông tin của khách hàng (Tên, Công ty, Mã ưu đãi, Giá trị giao dịch) vào nội dung tin nhắn.
4. **Quy trình Kiểm thử & Gửi Thử nghiệm (Test Send & Content Validation):** Cho phép người phụ trách gửi thử nghiệm tin nhắn tới địa chỉ cá nhân để kiểm tra giao diện hiển thị trước khi kích hoạt phát sóng chính thức.
5. **Điều phối Phát sóng Độc lập qua Hàng đợi (Asynchronous Queue Dispatching):** Xử lý gửi hàng chục nghìn tin nhắn qua hàng đợi BullMQ mà không làm nghẽn hệ thống, hỗ trợ Đặt lịch phát sóng, Tạm dừng, Tiếp tục và Hủy an toàn.
6. **Sổ cái Người nhận Tin Minh bạch (Send Ledger & Audit):** Lưu vết chi tiết trạng thái gửi tới từng người nhận riêng lẻ (`SENT`, `DELIVERED`, `OPENED`, `CLICKED`, `FAILED`, `BOUNCED`, `REFUSED`) và hỗ trợ Thử lại các lỗi kỹ thuật (Retry Failed).
7. **Tuân thủ Chuẩn mực Chống Thư rác Quốc tế (GDPR / CAN-SPAM Compliance):** Bắt buộc chèn liên kết Hủy đăng ký (Unsubscribe) và tự động đồng bộ trạng thái đồng thuận vào hồ sơ khách hàng.
8. **Đo lường Hiệu quả Chiến dịch (Campaign Analytics):** Theo dõi theo thời gian thực Tỷ lệ mở (Open Rate), Tỷ lệ nhấp (CTR), Tỷ lệ hỏng (Bounce Rate) và Tỷ lệ chuyển đổi thành Cơ hội bán hàng (Deal Conversion).

### 1.2 Phạm vi

Tài liệu bao gồm 10 nhóm chức năng cốt lõi:
- **Nhóm A: Quản trị Chiến dịch Tiếp thị (Campaigns CRUD):** Tạo mới, chỉnh sửa bản nháp, nhân bản chiến dịch (Duplicate), xóa và cấp mã định danh.
- **Nhóm B: Phân khúc Khách hàng & Ước tính Khả dụng:** Lọc đối tượng theo thẻ/vòng đời/điểm số, Xem trước quy mô (`previewAudience`), Xem mẫu danh sách (`sampleAudience`) và tính toán `estimatedReachable`.
- **Nhóm C: Đa kênh Truyền thông & Tài khoản Phát sóng:** Quản lý tài khoản gửi Email (SES/SMTP), WhatsApp Business, Zalo OA, SMS Gateway (`listSenders`).
- **Nhóm D: Soạn thảo Nội dung & Cá nhân hóa:** Trình soạn thảo HTML/Trực quan, Trộn biến cá nhân hóa (`{{name}}`, `{{company}}`), Mẫu tin nhắn phê duyệt sẵn (Approved Templates).
- **Nhóm E: Kiểm thử & Gửi Thử nghiệm:** Gửi thử nghiệm 1 tin (`testSend`), Kiểm tra hiển thị đa thiết bị.
- **Nhóm F: Điều phối Phát sóng & Quản lý Tiến trình:** Phát sóng ngay (`launch`), Đặt lịch hẹn giờ (`scheduledAt`), Tạm dừng (`pause`), Tiếp tục (`resume`), Hủy (`cancel`).
- **Nhóm G: Sổ cái Người nhận & Thử lại Lỗi:** Sổ cái `campaign_recipients`, theo dõi trạng thái phân phát, Thử lại các bản ghi lỗi (`retryFailed`).
- **Nhóm H: Kiểm soát Tuân thủ & Chống Thư rác:** Tự động chèn link Unsubscribe, tự động cập nhật trạng thái `OPT_OUT`, bảo vệ danh tiếng tên miền.
- **Nhóm I: Đo lường Hiệu quả & Báo cáo Tiếp thị:** Báo cáo thời gian thực: Tỷ lệ gửi thành công, Tỷ lệ mở, Tỷ lệ nhấp chuột (CTR), Bản đồ nhiệt liên kết.
- **Nhóm J: Tích hợp Đa kênh & Hộp thư Tiếp nhận:** Tự động điều hướng tin nhắn phản hồi của khách hàng vào Hộp thư Omni-channel Inbox.

**Ngoài phạm vi (thuộc về các tài liệu SRS chuyên biệt khác):**
- **Nghiệp vụ Trực chat & Hộp thư Tiếp nhận Khách hàng:** Thuộc về [`omnichat-srs.md`](./omnichat-srs.md).
- **Nghiệp vụ Quản lý Hồ sơ & Trạng thái Đồng thuận:** Thuộc về [`contacts-srs.md`](./contacts-srs.md).

### 1.3 Đối tượng đọc

- **Product Owner / Business Analyst:** Chuẩn mực đặc tả nghiệp vụ Marketing Automation để thiết kế tính năng và nghiệm thu hệ thống.
- **Đội ngũ Phát triển (Frontend / Backend):** Căn cứ thiết kế API, schemas, hàng đợi điều phối BullMQ và thuật toán phân tích chỉ số tiếp thị.
- **Đội ngũ Kiểm thử (QA/QC):** Thiết kế bộ kịch bản kiểm thử tải trọng lớn cho luồng phát sóng hàng loạt và đo lường sự kiện webhook mở/nhấp.
- **Chuyên viên Tiếp thị & Truyền thông (Marketer / Growth Lead):** Nắm rõ quy trình xây dựng chiến dịch, quy chuẩn gửi tin và kỹ thuật phân tích hiệu quả.

### 1.4 Thuật ngữ & Viết tắt

| Thuật ngữ | Định nghĩa nghiệp vụ |
| --- | --- |
| **Chiến dịch Tiếp thị (Campaign)** | Đợt phát sóng thông điệp hàng loạt tới danh sách khách hàng mục tiêu qua Email, WhatsApp, Zalo hoặc SMS. |
| **Phân khúc Mục tiêu (Audience)** | Bộ tiêu chí lọc danh sách khách hàng nhận tin theo thuộc tính và hành vi tương tác. |
| **Tiếp cận Khả dụng (Estimated Reachable)** | Số lượng người nhận thực tế sau khi đã lọc bỏ địa chỉ hỏng hoặc từ chối nhận tin. |
| **Gửi Thử nghiệm (Test Send)** | Thao tác gửi 1 tin nhắn thử nghiệm tới địa chỉ cá nhân của người tạo để duyệt nội dung. |
| **Sổ cái Người nhận (Send Ledger)** | Nhật ký chi tiết trạng thái gửi tin tới từng khách hàng riêng lẻ. |
| **Tỷ lệ Mở (Open Rate)** | Tỷ lệ % người nhận đã mở xem email trên tổng số tin nhắn gửi thành công. |
| **Tỷ lệ Nhấp chuột (CTR)** | Tỷ lệ % người nhận đã nhấp vào ít nhất một đường dẫn liên kết trong nội dung tin nhắn. |
| **Hủy Nhận tin (Unsubscribe)** | Hành động của khách hàng từ chối tiếp tục nhận các thông điệp tiếp thị trong tương lai. |

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

Trong hoạt động tiếp thị và chăm sóc khách hàng hàng loạt:
- Gửi tin nhắn đại trà không phân khúc dẫn đến tỷ lệ hủy nhận tin cao, làm hỏng uy tín tên miền (Domain Reputation) và nguy cơ bị khóa tài khoản gửi (WhatsApp/Zalo/Email).
- Không đo lường được hiệu quả: Có bao nhiêu người mở email? Ai đã nhấp vào đường link báo giá? Chiến dịch này mang lại bao nhiêu cơ hội bán hàng?
- Nhân viên gửi nhầm nội dung lỗi hoặc sai mã giảm giá do không có quy trình Gửi thử nghiệm (Test Send) và phân tách quyền phê duyệt phát sóng (`campaigns:launch`).
- Khi khách hàng phản hồi lại tin nhắn tiếp thị, tin nhắn bị thất lạc không được chuyển về Hộp thư chăm sóc khách hàng để tư vấn viên kịp thời hỗ trợ.

Module Marketing Campaigns & Mass Messaging giải quyết triệt để các vấn đề trên bằng cách cung cấp công cụ phân khúc đối tượng thông minh, hệ thống gửi thử nghiệm an toàn, điều phối phát sóng bất đồng bộ qua hàng đợi, sổ cái người nhận chi tiết và báo cáo đo lường chuyển đổi toàn diện.

### 2.2 Vai trò người dùng (Actor)

| Actor | Mô tả vai trò và quyền hạn |
| --- | --- |
| **Chuyên viên Tiếp thị (Marketing Specialist)** | Tạo mới chiến dịch, thiết lập phân khúc đối tượng, soạn thảo nội dung, thực hiện gửi thử nghiệm (Test Send). |
| **Trưởng phòng Tiếp thị (Marketing Manager / Lead)** | Phê duyệt nội dung, duyệt phát sóng chiến dịch (`campaigns:launch`), theo dõi tiến trình và phân tích báo cáo hiệu quả. |
| **Nhân viên Tư vấn / Bán hàng (Sales / Support Agent)** | Tiếp nhận các tin nhắn phản hồi của khách hàng từ chiến dịch trong Hộp thư Omni Inbox để tư vấn chốt đơn. |
| **Quản trị viên Không gian làm việc (Tenant Admin)** | Cấu hình tài khoản gửi (Email SMTP/SES, WhatsApp WABA, Zalo OA, SMS Gateway) và quản lý hạn ngạch gửi tin. |
| **Chủ sở hữu Không gian làm việc (Tenant Owner)** | Toàn quyền quản trị phân hệ chiến dịch và kiểm soát ngân sách truyền thông của tổ chức. |
| **Khách hàng (Customer / Recipient)** | Nhận thông điệp, mở xem, nhấp liên kết và thực hiện quyền hủy nhận tin (Unsubscribe). |
| **Tiến trình Hệ thống (System Engine / BullMQ Workers)** | Điều phối hàng đợi gửi tin theo từng khối, tiếp nhận webhook sự kiện mở/nhấp và cập nhật sổ cái người nhận. |

### 2.3 Bảng tổng hợp 30 tính năng nghiệp vụ

| Nhóm | Mã FEAT | Tên tính năng nghiệp vụ | Trạng thái |
| --- | --- | --- | --- |
| **A. Quản trị Chiến dịch** | `FEAT-01` | Tạo mới & Quản lý Chiến dịch Tiếp thị (Campaign CRUD) | `[Đã triển khai]` |
| | `FEAT-02` | Nhân bản Chiến dịch Nhanh chóng (Duplicate Campaign) | `[Đã triển khai]` |
| | `FEAT-03` | Cấp Mã Định danh Chiến dịch Duy nhất (Campaign Unique Code) | `[Đã triển khai]` |
| | `FEAT-04` | Xóa & Lưu trữ Chiến dịch Tiếp thị (Delete Campaign) | `[Đã triển khai]` |
| **B. Phân khúc & Ước tính Khả dụng** | `FEAT-05` | Bộ lọc Phân khúc Khách hàng Mục tiêu Đa điều kiện (Audience Filter) | `[Đã triển khai]` |
| | `FEAT-06` | Xem trước Quy mô Tệp Khách hàng (Audience Sizing & Preview) | `[Đã triển khai]` |
| | `FEAT-07` | Xem Mẫu Danh sách Khách hàng Mục tiêu (Audience Sampling) | `[Đã triển khai]` |
| | `FEAT-08` | Tự động Tính toán Số lượng Tiếp cận Khả dụng (Estimated Reachable) | `[Đã triển khai]` |
| **C. Đa kênh Truyền thông & Tài khoản**| `FEAT-09` | Hỗ trợ 4 Kênh Phát sóng Chủ lực (Email, WhatsApp, Zalo, SMS) | `[Đã triển khai]` |
| | `FEAT-10` | Quản lý & Lựa chọn Tài khoản Gửi Tin (Sender Accounts Management) | `[Đã triển khai]` |
| **D. Soạn thảo & Cá nhân hóa** | `FEAT-11` | Trình Soạn thảo Nội dung Trực quan & HTML (Rich Content Editor) | `[Đã triển khai]` |
| | `FEAT-12` | Trộn Biến Cá nhân hóa Động (Dynamic Merge Tags / Personalization) | `[Đã triển khai]` |
| | `FEAT-13` | Quản lý Mẫu Tin nhắn Đăng ký Trước (Approved Message Templates) | `[Đã triển khai]` |
| **E. Kiểm thử & Gửi Thử nghiệm** | `FEAT-14` | Gửi Thử nghiệm 1 Tin nhắn Duyệt Định dạng (Test Send Feature) | `[Đã triển khai]` |
| | `FEAT-15` | Kiểm tra Tỷ lệ Hiển thị & Cảnh báo Bộ lọc Thư rác (Spam Score Check) | `[Yêu cầu mới]` |
| **F. Điều phối Phát sóng & Tiến trình**| `FEAT-16` | Phát sóng Chiến dịch Tức thì (Launch Immediate Campaign) | `[Đã triển khai]` |
| | `FEAT-17` | Đặt Lịch Hẹn giờ Phát sóng trong Tương lai (Scheduled Broadcast) | `[Đã triển khai]` |
| | `FEAT-18` | Tạm dừng & Tiếp tục Chiến dịch Đang phát sóng (Pause & Resume Campaign)| `[Đã triển khai]` |
| | `FEAT-19` | Hủy Phát sóng Chiến dịch An toàn (Cancel Campaign & Ledger Lock) | `[Đã triển khai]` |
| **G. Sổ cái Người nhận & Thử lại** | `FEAT-20` | Sổ cái Chi tiết Từng Người Nhận Tin (Recipient Send Ledger) | `[Đã triển khai]` |
| | `FEAT-21` | Thử lại Hàng loạt các Tin nhắn Gặp Lỗi Kỹ thuật (Retry Failed Sends) | `[Đã triển khai]` |
| | `FEAT-22` | Theo dõi Lý do Gửi Thất bại Chi tiết (Detailed Error Tracking) | `[Đã triển khai]` |
| **H. Kiểm soát Tuân thủ & Chống Spam**| `FEAT-23` | Tự động Chèn Liên kết Hủy Nhận tin (Mandatory Unsubscribe Link) | `[Đã triển khai]` |
| | `FEAT-24` | Tự động Cập nhật Trạng thái Đồng thuận & Chặn Gửi (Opt-out Enforcement)| `[Đã triển khai]` |
| | `FEAT-25` | Tự động Loại trừ Địa chỉ Hỏng & Đánh dấu Bounced (Bounce Handling) | `[Đã triển khai]` |
| **I. Đo lường Hiệu quả & Báo cáo** | `FEAT-26` | Báo cáo Hiệu suất Phân phát & Tỷ lệ Mở (Delivery & Open Rates) | `[Đã triển khai]` |
| | `FEAT-27` | Báo cáo Tỷ lệ Nhấp chuột & Bản đồ Nhiệt Liên kết (CTR & Click Map) | `[Đã triển khai]` |
| | `FEAT-28` | Đo lường Tỷ lệ Chuyển đổi thành Cơ hội Bán hàng (Deal Attribution) | `[Yêu cầu mới]` |
| **J. Tích hợp Đa kênh Tiếp nhận** | `FEAT-29` | Tự động Điều hướng Tin nhắn Phản hồi về Hộp thư Omni Inbox | `[Đã triển khai]` |
| | `FEAT-30` | Tự động Gắn Thẻ Phân loại sau khi Tương tác Chiến dịch (Auto Tagging) | `[Yêu cầu mới]` |

---

## 3. Đặc tả yêu cầu chức năng

## A. QUẢN TRỊ CHIẾN DỊCH TIẾP THỊ (CAMPAIGNS MANAGEMENT)

### FEAT-01 — Tạo mới & Quản lý Chiến dịch Tiếp thị (Campaign CRUD) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép chuyên viên tiếp thị tạo mới chiến dịch, lưu bản nháp (Draft), chỉnh sửa thông tin và theo dõi danh sách các chiến dịch trong không gian làm việc.

**Actor:** Chuyên viên Tiếp thị, Trưởng phòng Tiếp thị.

**Quy tắc nghiệp vụ:**
- `BR-01.1 (Thông tin bắt buộc)`: Tên chiến dịch (`name`), Kênh truyền thông (`channel`), Bộ lọc đối tượng (`audienceFilter`), Nội dung thông điệp (`content`), Tài khoản gửi (`senderAccountId`).
- `BR-01.2 (Bảo vệ bản nháp)`: Chiến dịch ở trạng thái `DRAFT` hoặc `SCHEDULED` được phép chỉnh sửa; khi đã chuyển sang `SENDING` hoặc `COMPLETED`, nội dung và phân khúc bị khóa không thể chỉnh sửa.

---

### FEAT-02 — Nhân bản Chiến dịch Nhanh chóng (Duplicate Campaign) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép nhân bản một chiến dịch đã có (`POST /api/v1/campaigns/:id/duplicate`) để tái sử dụng phân khúc đối tượng và nội dung cho các đợt gửi tin tiếp theo.

**Actor:** Chuyên viên Tiếp thị.

**Quy tắc nghiệp vụ:**
- `BR-02.1`: Bản sao mới được tạo ở trạng thái `DRAFT`, tên tự động thêm hậu tố "(Copy)" và sinh mã code mới.

---

### FEAT-03 — Cấp Mã Định danh Chiến dịch Duy nhất (Campaign Unique Code) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Mỗi chiến dịch được tự động cấp một mã code viết hoa duy nhất (ví dụ: `CMP-2026-X89Q`) để phục vụ theo dõi tham số UTM và đối soát báo cáo tài chính.

**Actor:** Tiến trình Hệ thống.

---

### FEAT-04 — Xóa & Lưu trữ Chiến dịch Tiếp thị `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép xóa chiến dịch ở trạng thái Bản nháp hoặc Lưu trữ các chiến dịch cũ không còn hoạt động qua API `DELETE /api/v1/campaigns/:id`.

**Actor:** Quản trị viên Workspace, Người có quyền `delete` trên Campaigns.

---

## B. PHÂN KHÚC KHÁCH HÀNG & ƯỚC TÍNH KHẢ DỤNG (AUDIENCE & REACHABILITY)

### FEAT-05 — Bộ lọc Phân khúc Khách hàng Mục tiêu Đa điều kiện `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp công cụ lọc động kết hợp linh hoạt: Thẻ phân loại (Tags), Giai đoạn vòng đời (Lifecycle Stage), Điểm tiềm năng (Lead Score), Trường tùy biến (Custom Fields), Khu vực địa lý và Thời điểm tạo khách hàng.

**Actor:** Chuyên viên Tiếp thị.

---

### FEAT-06 — Xem trước Quy mô Tệp Khách hàng (Audience Preview) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Trước khi lưu chiến dịch, hệ thống cung cấp API `/api/v1/campaigns/audience-preview` tính toán tức thì:
- `total`: Tổng số khách hàng khớp bộ lọc.
- `estimatedReachable`: Số lượng khách hàng thực tế có thể tiếp cận được.

**Actor:** Chuyên viên Tiếp thị.

---

### FEAT-07 — Xem Mẫu Danh sách Khách hàng Mục tiêu (Audience Sampling) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp API `/api/v1/campaigns/audience-sample` hiển thị mẫu 5-10 khách hàng đầu tiên khớp bộ lọc để chuyên viên kiểm tra độ chính xác của tiêu chí lọc trước khi gửi.

**Actor:** Chuyên viên Tiếp thị.

---

### FEAT-08 — Tự động Tính toán Tiếp cận Khả dụng & Giới hạn Tần suất `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động loại trừ các khách hàng không thể gửi tin hoặc chạm giới hạn tần suất nhận tin để bảo vệ uy tín thương hiệu:
- **Loại trừ 1:** Khách hàng không có Email (nếu là kênh Email) hoặc không có SĐT (nếu là kênh Zalo/WhatsApp/SMS).
- **Loại trừ 2:** Địa chỉ bị đánh dấu `BOUNCED` (Email hỏng / SĐT không tồn tại).
- **Loại trừ 3:** Khách hàng đã từ chối nhận tin trên kênh này (`channelOptOut === true`).
- **Loại trừ 4:** Định danh bị đánh dấu là "Định danh dùng chung" (Shared Identifier).

**Quy tắc nghiệp vụ:**
- `BR-08.1`: Tính toán số lượng tiếp cận thực tế sau khi khấu trừ 4 nhóm loại trừ trên.
- `BR-08.2 (Giới hạn Tần suất Tiếp cận - Frequency Capping) [Yêu cầu mới]`: Tự động loại trừ khách hàng nếu khách hàng đó đã nhận từ **2 tin nhắn tiếp thị trở lên trong vòng 7 ngày gần nhất** trên cùng kênh truyền thông, nhằm chống làm phiền và giảm thiểu tỷ lệ hủy đăng ký.
- `BR-08.3 (Khử trùng lặp danh sách người nhận - Audience Deduplication) [Yêu cầu mới]`: Khi chiến dịch nhắm đến nhiều Segment cùng lúc, có thể xảy ra tình huống một Contact nằm trong nhiều Segment khác nhau. Hệ thống **bắt buộc** thực hiện khử trùng lặp (Deduplication) theo `contactId` trên toàn bộ danh sách người nhận hợp nhất **trước khi** đẩy vào hàng đợi phát sóng. Mỗi Contact chỉ nhận tin **tối đa 1 lần** trong cùng một chiến dịch, bất kể Contact đó thuộc bao nhiêu Segment.

---

## C. ĐA KÊNH TRUYỀN THÔNG & TÀI KHOẢN PHÁT SÓNG

### FEAT-09 — Hỗ trợ 4 Kênh Phát sóng Chủ lực `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hỗ trợ phát sóng thông điệp qua:
- **`EMAIL`:** Gửi bản tin HTML/Email tiếp thị dung lượng lớn.
- **`WHATSAPP`:** Gửi tin nhắn Broadcast qua WhatsApp Business API.
- **`ZALO`:** Gửi tin nhắn thông báo ZNS hoặc tin truyền thông qua Zalo Official Account.
- **`SMS`:** Gửi tin nhắn SMS Brandname định danh thương hiệu.

---

### FEAT-10 — Quản lý & Lựa chọn Tài khoản Gửi Tin (Sender Accounts) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp API `GET /api/v1/campaigns/senders/:channelType` hiển thị danh sách các tài khoản phát sóng đang hoạt động (ví dụ: Chọn gửi từ `marketing@congty.com` hay `cskh@congty.com`).

**Actor:** Chuyên viên Tiếp thị.

---

## D. SOẠN THẢO NỘI DUNG & CÁ NHÂN HÓA (CONTENT & PERSONALIZATION)

### FEAT-11 — Trình Soạn thảo Nội dung Trực quan & HTML `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép soạn thảo email với giao diện kéo thả trực quan hoặc chỉnh sửa mã nguồn HTML chuyên nghiệp, chèn hình ảnh, nút bấm CTA và định dạng văn bản phong phú.

**Actor:** Chuyên viên Tiếp thị.

---

### FEAT-12 — Trộn Biến Cá nhân hóa Động (Dynamic Merge Tags) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hỗ trợ chèn các thẻ biến động vào nội dung: `{{name}}` (Họ tên khách hàng), `{{company}}` (Tên công ty), `{{title}}` (Chức danh), `{{leadScore}}` (Điểm tiềm năng).

**Actor:** Chuyên viên Tiếp thị.

**Quy tắc nghiệp vụ:**
- `BR-12.1`: Khi gửi tin, hệ thống tự động thay thế thẻ biến bằng giá trị thực tế của từng khách hàng; nếu trường dữ liệu bị rỗng, hệ thống áp dụng giá trị dự phòng mặc định (ví dụ: "Quý khách").

---

### FEAT-13 — Quản lý Mẫu Tin nhắn Đăng ký Trước (Approved Templates) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Đối với kênh WhatsApp và Zalo ZNS, bắt buộc phải sử dụng các mẫu tin nhắn đã được Meta / Zalo phê duyệt trước (`templateId`).

**Actor:** Chuyên viên Tiếp thị.

---

## E. KIỂM THỬ & GỬI THỬ NGHIỆM (TEST SEND)

### FEAT-14 — Gửi Thử nghiệm 1 Tin nhắn Duyệt Định dạng (Test Send) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp chức năng gửi 1 tin nhắn thử nghiệm (`POST /api/v1/campaigns/:id/test-send`) tới địa chỉ email hoặc số điện thoại của người duyệt trước khi phát sóng.

**Actor:** Người có quyền `campaigns:launch`.

**Quy tắc nghiệp vụ:**
- `BR-14.1`: Sử dụng cổng gửi thật và tài khoản thật của tenant để người duyệt nhìn thấy chính xác 100% định dạng hiển thị trong hộp thư thực tế.

---

### FEAT-15 — Kiểm tra Tỷ lệ Hiển thị & Cảnh báo Bộ lọc Thư rác `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Tự động quét nội dung email để cảnh báo các từ khóa nhạy cảm dễ bị bộ lọc thư rác chặn (ví dụ: "Miễn phí 100%", "Kiếm tiền nhanh", viết HOA toàn bộ tiêu đề).

**Actor:** Chuyên viên Tiếp thị.

---

## F. ĐIỀU PHỐI PHÁT SÓNG & QUẢN LÝ TIẾN TRÌNH (DISPATCHING & LIFECYCLE)

### FEAT-16 — Phát sóng Chiến dịch Tức thì (Launch Immediate) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi bấm "Phát sóng ngay" (`POST /api/v1/campaigns/:id/launch`), hệ thống kiểm tra quyền `campaigns:launch`, chuyển trạng thái sang `SENDING` và đẩy toàn bộ danh sách người nhận vào hàng đợi BullMQ.

**Actor:** Trưởng phòng Tiếp thị, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-16.1 (Phân tách quyền hạn & Phê duyệt Kép)`: Quyền soạn thảo (`campaigns:edit`) không cho phép kích hoạt phát sóng; thao tác phát sóng bắt buộc yêu cầu quyền `campaigns:launch`. Nếu không gian làm việc bật chính sách "Phê duyệt Kép" (Four-Eyes Principle), người duyệt phát sóng bắt buộc phải là người khác với người tạo bản nháp.
- `BR-16.2`: Chuyển trạng thái sang `SENDING` và khóa toàn bộ nội dung và phân khúc chiến dịch ở chế độ chỉ đọc.
- `BR-16.3 (Luồng Yêu cầu Phê duyệt) [Yêu cầu mới]`: Khi người tạo hoàn thành bản nháp và gửi đi phê duyệt, chiến dịch chuyển sang trạng thái `PENDING_APPROVAL`. Người có quyền `campaigns:launch` nhận thông báo kèm liên kết Preview đầy đủ chiến dịch.
- `BR-16.4 (Luồng Từ chối Phê duyệt — Reject/Rework) [Yêu cầu mới]`: Khi Reviewer từ chối (Reject), chiến dịch **bắt buộc** quay về trạng thái `DRAFT_REJECTED` kèm: (a) Ghi chú lý do từ chối của Reviewer (bắt buộc, tối thiểu 20 ký tự); (b) Thông báo đến người tạo với nội dung lý do từ chối cụ thể. Người tạo chỉnh sửa và re-submit; chiến dịch quay về `PENDING_APPROVAL`. Số lần re-submit không bị giới hạn nhưng mỗi lần tạo 1 bản ghi trong Lịch sử Phê duyệt (Approval History).
- `BR-16.5 (Hủy Yêu cầu Phê duyệt)`: Người tạo có thể tự hủy yêu cầu phê duyệt (`PENDING_APPROVAL → DRAFT`) để chỉnh sửa lại trước khi re-submit.

---

### FEAT-17 — Đặt Lịch Hẹn giờ Phát sóng trong Tương lai (Scheduled) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép chọn Ngày và Giờ phát sóng trong tương lai (`scheduledAt`). Tiến trình `CampaignScheduler` tự động kích hoạt phát sóng đúng thời điểm đã hẹn.

**Actor:** Trưởng phòng Tiếp thị.

---

### FEAT-18 — Tạm dừng & Tiếp tục Chiến dịch Đang phát sóng `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép Tạm dừng (`POST /api/v1/campaigns/:id/pause`) để kiểm tra nếu phát hiện sự cố, và Tiếp tục (`POST /api/v1/campaigns/:id/resume`) gửi nốt các tin nhắn còn lại trong hàng đợi.

**Actor:** Người có quyền `campaigns:launch`.

---

### FEAT-19 — Hủy Phát sóng Chiến dịch An toàn (Cancel Campaign) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép Hủy bỏ hoàn toàn chiến dịch đang phát sóng (`POST /api/v1/campaigns/:id/cancel`).

**Actor:** Người có quyền `campaigns:launch`.

**Quy tắc nghiệp vụ:**
- `BR-19.1`: Hệ thống xóa các công việc gửi tin chưa thực hiện trong hàng đợi BullMQ, khóa sổ cái người nhận và ghi nhận thời điểm hủy `cancelledAt = now()`.

---

## G. SỔ CÁI NGƯỜI NHẬN & THỬ LẠI LỖI (SEND LEDGER & RETRY)

### FEAT-20 — Sổ cái Chi tiết Từng Người Nhận Tin (Send Ledger) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Màn hình quản lý chi tiết danh sách toàn bộ người nhận (`GET /api/v1/campaigns/:id/recipients`) hiển thị rõ: Họ tên, Điểm đến (Email/SĐT), Trạng thái gửi, Thời điểm gửi, Thời điểm mở xem và Lý do lỗi cụ thể nếu có.

**Actor:** Mọi người dùng có quyền xem Campaign.

---

### FEAT-21 — Thử lại Hàng loạt các Tin nhắn Gặp Lỗi Kỹ thuật `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp nút "Thử lại tin nhắn lỗi" (`POST /api/v1/campaigns/:id/retry-failed`) cho phép quét và gửi lại cho toàn bộ các khách hàng có trạng thái `FAILED` do lỗi gián đoạn mạng tạm thời.

**Actor:** Người có quyền `campaigns:launch`.

---

### FEAT-22 — Theo dõi Lý do Gửi Thất bại Chi tiết `[Đã triển khai]`

**Mô tả nghiệp vụ:** Lưu trữ mã lỗi chi tiết từ nhà cung cấp dịch vụ gửi tin (ví dụ: `SMTP 550 Mailbox full`, `WhatsApp User not registered`, `SMS Invalid phone number`).

**Actor:** Chuyên viên Tiếp thị, Quản trị viên.

---

## H. KIỂM SOÁT TUÂN THỦ & CHỐNG THƯ RÁC (COMPLIANCE & OPT-OUT)

### FEAT-23 — Tự động Chèn Liên kết Hủy Nhận tin (Unsubscribe Link) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Mọi email tiếp thị được hệ thống tự động chèn liên kết "Hủy nhận bản tin" ở chân trang theo chuẩn quốc tế CAN-SPAM.

**Actor:** Tiến trình Hệ thống.

---

### FEAT-24 — Tự động Cập nhật Trạng thái Đồng thuận & Chặn Gửi `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi khách hàng nhấp vào liên kết Hủy nhận tin (Unsubscribe), hệ thống mở trang tùy chọn và tự động cập nhật trạng thái đồng thuận.

**Actor:** Khách hàng, Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-24.1 (Hủy nhận tin theo từng Kênh)`: Mặc định thao tác hủy nhận tin chỉ cập nhật cờ từ chối trên đúng kênh phát sóng của chiến dịch đó (ví dụ: hủy từ email thì gán `emailOptOut = true`, không làm ảnh hưởng tới kênh WhatsApp hay SMS).
- `BR-24.2 (Hủy toàn bộ Kênh)`: Trên trang xác nhận hủy nhận tin, cung cấp thêm tùy chọn: *"Tôi muốn ngừng nhận mọi thông điệp tiếp thị qua tất cả các kênh (Email, WhatsApp, Zalo, SMS)"*. Nếu khách hàng tích chọn, hệ thống cập nhật đồng thời toàn bộ các cờ `OptOut = true` trên hồ sơ Contact.

---

### FEAT-25 — Tự động Loại trừ Địa chỉ Hỏng (Bounce Handling) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi nhận được webhook thông báo Email không tồn tại (Hard Bounce), hệ thống tự động đánh dấu địa chỉ đó là `BOUNCED` để không bao giờ gửi lại, bảo vệ danh tiếng máy chủ gửi tin.

**Actor:** Tiến trình Hệ thống.

---

## I. ĐO LƯỜNG HIỆU QUẢ & BÁO CÁO TIẾP THỊ (CAMPAIGN ANALYTICS)

### FEAT-26 — Báo cáo Hiệu suất Phân phát & Tỷ lệ Mở `[Đã triển khai]`

**Mô tả nghiệp vụ:** Bảng điều khiển thời gian thực hiển thị: Tổng số gửi (Total Sent), Tỷ lệ gửi thành công (Delivery Rate %), Tỷ lệ mở xem (Open Rate %) và Tỷ lệ hỏng (Bounce Rate %).

**Actor:** Mọi người dùng có quyền xem Campaign.

---

### FEAT-27 — Báo cáo Tỷ lệ Nhấp chuột & Bản đồ Nhiệt Liên kết (CTR) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Thống kê số lượt nhấp chuột (Total Clicks & Unique Clicks), Tỷ lệ nhấp trên lượt mở (Click-to-Open Rate) và danh sách các đường link trong email được khách hàng nhấp nhiều nhất.

**Actor:** Chuyên viên Tiếp thị, Trưởng phòng Tiếp thị.

---

### FEAT-28 — Đo lường Tỷ lệ Chuyển đổi thành Cơ hội Bán hàng `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Đo lường có bao nhiêu Cơ hội bán hàng (Deals) và Doanh thu phát sinh trực tiếp từ những khách hàng đã nhấp vào chiến dịch tiếp thị này.

**Actor:** Trưởng phòng Tiếp thị, Giám đốc Kinh doanh.

---

## J. TÍCH HỢP ĐA KÊNH TIẾP NHẬN & CHĂM SÓC

### FEAT-29 — Tự động Điều hướng Tin nhắn Phản hồi về Hộp thư Omni Inbox `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi khách hàng trả lời lại tin nhắn WhatsApp hoặc email từ chiến dịch, thông điệp được tự động đưa vào Hộp thư Omni Inbox và phân công cho tư vấn viên phụ trách khách hàng đó.

**Actor:** Khách hàng, Nhân viên Tư vấn.

---

### FEAT-30 — Tự động Gắn Thẻ Phân loại sau khi Tương tác Chiến dịch `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Tự động gắn thẻ phân loại cho khách hàng (ví dụ: gắn thẻ `Quan-tam-giai-phap-AI`) ngay khi khách hàng nhấp vào đường link liên quan trong email.

**Actor:** Tiến trình Hệ thống.

---

## 4. Yêu cầu phi chức năng

### 4.1 Hiệu năng & Khả năng đáp ứng (Performance)
- **NFR-01 (Tốc độ Xử lý Hàng đợi Phát sóng):** Hệ thống worker BullMQ xử lý phát sóng tối thiểu **10,000 tin nhắn / phút** mà không làm ảnh hưởng tới các API vận hành khác.
- **NFR-02 (Thời gian Tính toán Phân khúc Khách hàng):** API Audience Preview và Sample phản hồi dưới **300ms** (p95) trên tập dữ liệu 1,000,000 khách hàng.
- **NFR-03 (Độ trễ Tiếp nhận Webhook Mở/Nhấp):** Sự kiện khách hàng mở email hoặc nhấp link được ghi nhận vào sổ cái trong vòng dưới **2 giây**.

### 4.2 Độ tin cậy & Toàn vẹn Dữ liệu (Reliability & ACID)
- **NFR-04 (Chống Gửi Trùng lặp - Idempotency):** Mỗi người nhận trong chiến dịch chỉ được gửi đúng 1 lần duy nhất, kể cả khi worker gặp sự cố khởi động lại.
- **NFR-05 (Bảo toàn Sổ cái Người nhận):** Sổ cái `campaign_recipients` là bất biến và lưu trữ đầy đủ lịch sử gửi phục vụ đối soát viễn thông.

### 4.3 An toàn & Bảo mật (Security)
- **NFR-06 (Bảo mật Quyền Phát sóng Phân cấp):** Áp dụng nghiêm ngặt quyền `campaigns:launch` kết hợp ABAC. Nhân viên không có quyền không thể kích hoạt gửi tin ra bên ngoài.
- **NFR-07 (Bảo vệ Danh tiếng Tên miền):** Tự động điều tiết tốc độ gửi (Rate Limiting) theo ngưỡng an toàn của từng nhà cung cấp để chống bị liệt vào danh sách đen (Blacklist).

---

## 5. Ma trận quyền truy cập tính năng

| Mã FEAT | Tên tính năng nghiệp vụ | Chuyên viên (Marketer) | Trưởng nhóm (Lead) | Quản trị viên (Admin) | Chủ sở hữu (Owner) |
| --- | --- | :---: | :---: | :---: | :---: |
| `FEAT-01` | Tạo & Quản lý Campaign | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-02` | Nhân bản Campaign | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-03` | Cấp Mã Campaign Code | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-04` | Xóa & Lưu trữ Campaign | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-05` | Cấu hình Bộ lọc Đối tượng | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-06` | Xem trước Quy mô Tệp | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-07` | Xem Mẫu Danh sách Khách | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-08` | Tính Số lượng Khả dụng | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-09` | Chọn Kênh Phát sóng | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-10` | Chọn Tài khoản Gửi | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-11` | Soạn thảo Nội dung HTML | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-12` | Trộn Biến Cá nhân hóa | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-13` | Sử dụng Mẫu Đăng ký Trước| Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-14` | Gửi Thử nghiệm Test Send | Có quyền `launch`* | Có quyền `launch` | **Toàn quyền** | **Toàn quyền** |
| `FEAT-15` | Kiểm tra Cảnh báo Spam | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-16` | Phát sóng Tức thì (Launch) | — | Có quyền `launch` | **Toàn quyền** | **Toàn quyền** |
| `FEAT-17` | Đặt Lịch Hẹn giờ Phát sóng | — | Có quyền `launch` | **Toàn quyền** | **Toàn quyền** |
| `FEAT-18` | Tạm dừng & Tiếp tục Gửi | — | Có quyền `launch` | **Toàn quyền** | **Toàn quyền** |
| `FEAT-19` | Hủy Phát sóng (Cancel) | — | Có quyền `launch` | **Toàn quyền** | **Toàn quyền** |
| `FEAT-20` | Xem Sổ cái Người nhận | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-21` | Thử lại Gửi Tin Lỗi | — | Có quyền `launch` | **Toàn quyền** | **Toàn quyền** |
| `FEAT-22` | Xem Lý do Lỗi Chi tiết | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-23` | Tự động Chèn Link Hủy tin | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-24` | Chặn Gửi Khách Opt-out | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-25` | Tự động Xử lý Bounced | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-26` | Báo cáo Tỷ lệ Mở (Open) | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-27` | Báo cáo Tỷ lệ Nhấp (CTR) | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-28` | Đo lường Doanh số Chuyển đổi| Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-29` | Điều hướng về Omni Inbox | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-30` | Tự động Gắn Thẻ Phân loại | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |

---

## 6. Kịch bản chấp nhận tổng hợp (UAT)

### Kịch bản 1: Tạo Chiến dịch Email, Lọc Phân khúc & Ước tính Tiếp cận Khả dụng
1. Chuyên viên tiếp thị tạo chiến dịch: "Bản tin Khuyến mãi Mùa Thu 2026", Kênh: `EMAIL`.
2. Thiết lập bộ lọc đối tượng: Thẻ phân loại chứa `VIP` VÀ Giai đoạn vòng đời là `Customer`.
3. Bấm "Xem trước quy mô tệp".
4. **Kỳ vọng:** Hệ thống hiển thị: "Tổng số khách hàng khớp: 5,200 | Số lượng tiếp cận khả dụng: 5,050 (Đã loại trừ 120 khách thiếu email và 30 khách đã hủy nhận tin)".

---

### Kịch bản 2: Gửi Thử nghiệm (Test Send) Kiểm tra Nội dung & Nút Liên kết
1. Chuyên viên soạn nội dung email kèm hình banner và nút CTA "Nhận Ưu Đãi 20%".
2. Bấm nút "Gửi thử nghiệm", nhập email cá nhân `marketer@congty.vn`.
3. **Kỳ vọng:** Hệ thống gửi ngay 1 email mẫu tới hộp thư cá nhân, kiểm tra tiêu đề, ảnh hiển thị sắc nét và các biến `{{name}}` hiển thị đúng định dạng.

---

### Kịch bản 3: Phê duyệt Phát sóng Chiến dịch & Theo dõi Tiến trình
1. Trưởng phòng tiếp thị có quyền `campaigns:launch` mở chiến dịch và bấm nút "Phát sóng ngay".
2. Hệ thống chuyển trạng thái sang `SENDING`, thanh tiến trình hiển thị tỷ lệ % đã gửi theo thời gian thực.
3. Sau 3 phút, toàn bộ 5,050 email được phân phát hoàn tất, trạng thái chuyển sang `COMPLETED`.
4. **Kỳ vọng:** Sổ cái người nhận hiển thị 5,050 bản ghi `SENT`, không có lỗi nghẽn hệ thống.

---

### Kịch bản 4: Khách hàng Bấm Hủy Nhận tin (Unsubscribe)
1. Khách hàng nhận được email chiến dịch, kéo xuống chân trang và nhấp vào liên kết "Hủy nhận bản tin tiếp thị".
2. Màn hình xác nhận hiển thị thông báo: "Bạn đã hủy đăng ký nhận tin thành công".
3. **Kỳ vọng hệ thống:** Địa chỉ email của khách hàng tự động được gắn cờ `OPT_OUT = true`. Ở chiến dịch tiếp theo, hệ thống tự động loại trừ khách hàng này khỏi danh sách gửi tin.

---

### Kịch bản 5: Tạm dừng & Tiếp tục Khi Phát hiện Sự cố
1. Trong khi chiến dịch đang phát sóng được 20% (1,000 / 5,000 tin), Trưởng phòng phát hiện đường link đích trên website bị lỗi máy chủ.
2. Trưởng phòng bấm "Tạm dừng phát sóng" (`Pause`).
3. **Kỳ vọng:** Hệ thống lập tức đóng băng hàng đợi, trạng thái chuyển sang `PAUSED`, ngừng gửi các tin nhắn còn lại.
4. Sau khi IT sửa xong website, Trưởng phòng bấm "Tiếp tục phát sóng" (`Resume`).
5. **Kỳ vọng:** Hệ thống tiếp tục gửi nốt 4,000 tin nhắn còn lại từ vị trí đã dừng.

---

### Kịch bản 6: Thử lại các Tin nhắn Gặp Lỗi Mạng (Retry Failed)
1. Trong chiến dịch WhatsApp Broadcast 3,000 tin nhắn, có 2,900 tin gửi thành công và 100 tin bị `FAILED` do nghẽn mạng API Meta tạm thời.
2. Chuyên viên mở Sổ cái người nhận, lọc danh sách 100 khách hàng bị lỗi, bấm nút "Thử lại tin nhắn lỗi" (`Retry Failed`).
3. **Kỳ vọng:** Hệ thống đẩy 100 bản ghi lỗi vào lại hàng đợi gửi tin và cập nhật trạng thái thành công sau khi cổng mạng thông suốt.

---

## 7. Giới hạn hiện tại & Vấn đề chính sách cần quyết định tiếp

1. **Chiến dịch Nuôi dưỡng Tự động Nhiều Bước (Multi-Step Drip Sequences):**
   - *Vấn đề:* Có hỗ trợ luồng gửi tin tự động nhiều bước (*Nếu khách mở email -> sau 2 ngày gửi tiếp WhatsApp, nếu không mở -> sau 3 ngày gửi lại email*) không?
   - *Đề xuất PM:* Đưa tính năng Marketing Automation Drip Workflows vào lộ trình nâng cấp phân hệ Tự động hóa tiếp thị chuyên sâu.
2. **Kiểm soát Hạn ngạch Gửi Tin Hằng ngày (Daily Sending Quota Limits):**
   - *Vấn đề:* Cần cấu hình giới hạn số lượng tin nhắn tối đa mà 1 tenant được gửi trong ngày để tránh phát sinh chi phí viễn thông ngoài tầm kiểm soát?
   - *Đề xuất PM:* Áp dụng chính sách trần hạn ngạch theo gói thuê bao của doanh nghiệp (ví dụ gói Pro: 50,000 tin/ngày).
3. **Kiểm tra A/B Testing Tiêu đề & Nội dung:**
   - *Vấn đề:* Cho phép gửi thử 2 phiên bản tiêu đề A và B cho 10% tệp khách hàng, phiên bản nào có tỷ lệ mở cao hơn sẽ tự động gửi cho 90% còn lại?
   - *Đề xuất PM:* Đưa vào lộ trình phiên bản tiếp theo.

---

## K. DỰ PHÒNG KÊNH & TỐI ƯU PHÁT SÓNG (CHANNEL FALLBACK & DELIVERY OPTIMIZATION)

### FEAT-31 — Dự phòng Kênh Gửi Tin Tự động (Channel Fallback Rules) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khi tin nhắn gửi qua kênh chính thất bại (Zalo ZNS bị từ chối, WhatsApp OA bị block, Email bị Bounce), hệ thống tự động chuyển sang kênh dự phòng theo thứ tự đã cấu hình, đảm bảo thông điệp tiếp cận được khách hàng dù kênh chính có sự cố.

**Actor:** Trưởng phòng Tiếp thị (cấu hình quy tắc fallback), Tiến trình Hệ thống (thực thi tự động).

**Quy tắc nghiệp vụ:**
- `BR-31.1 (Cấu hình chuỗi Fallback)`: Mỗi chiến dịch cho phép cấu hình chuỗi kênh dự phòng (Fallback Chain) tối đa 3 bước. Ví dụ: Zalo ZNS → SMS → Email. Người tạo chiến dịch có thể tắt hoặc điều chỉnh chuỗi fallback theo nhu cầu.
- `BR-31.2 (Điều kiện kích hoạt Fallback)`: Kênh dự phòng được kích hoạt khi kênh trước đó gặp lỗi không thể tự giải quyết (Non-retryable error): Zalo ZNS bị từ chối do nội dung vi phạm chính sách, WhatsApp 24h session hết hạn, Email bị hard bounce.
- `BR-31.3 (Thời gian chờ giữa các bước Fallback)`: Sau khi xác định gửi kênh chính thất bại, hệ thống chờ tối đa **5 phút** (cấu hình được) trước khi thử kênh tiếp theo để tránh gây phiền cho khách hàng khi nhận 2 tin quá gần nhau.
- `BR-31.4 (Ghi nhận trong Send Ledger)`: Mỗi lần fallback được ghi nhận rõ ràng trong Sổ cái Người nhận (Send Ledger): Kênh chính đã thử, Lý do thất bại, Kênh fallback được chọn, Kết quả gửi kênh fallback.
- `BR-31.5 (Deduplication vẫn áp dụng)`: Cơ chế khử trùng lặp (BR-08.3) và Frequency Capping (BR-08.2) áp dụng cho **tổng số tin đã gửi** (bao gồm cả tin từ kênh fallback) để tránh Contact nhận nhiều hơn giới hạn cho phép trong một chiến dịch.
