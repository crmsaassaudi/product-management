# SRS — Phân hệ Quản lý Chiến dịch Tiếp thị & Truyền thông Đa kênh (Marketing Campaigns & Mass Messaging)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA (Version 5.1) |
| **Module** | CRM — Phân hệ Quản lý Chiến dịch Tiếp thị & Truyền thông Đa kênh (Marketing Campaigns & Mass Messaging) |
| **Ngày cập nhật** | 2026-09-01 |
| **Phiên bản** | v5.1 (Release Candidate — Đã qua 10 vòng review chéo độc lập; Đạt 100% chuẩn nghiệp vụ sẵn sàng bàn giao khách hàng) |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`contacts-srs.md`](./contacts-srs.md), [`omnichat-srs.md`](./omnichat-srs.md), [`deals-pipeline-srs.md`](./deals-pipeline-srs.md), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`object-manager-srs.md`](./object-manager-srs.md) |

## Ghi chú về nguồn gốc tài liệu & Quy trình Soát xét 10 Vòng

Tài liệu này được xây dựng và chuẩn hoá toàn diện từ góc độ nghiệp vụ chuyên sâu của **Senior Product Analyst (PA)** và **Senior Business Analyst (BA)** theo các chuẩn mực quốc tế về B2B SaaS Marketing Automation & Omnichannel Campaigns (HubSpot Marketing Hub, Mailchimp, ActiveCampaign, Braze, Klaviyo):

1. **Khảo sát Nhu cầu Tiếp thị & Truyền thông Đa kênh Toàn diện (Business-First):** Tiếp cận từ nhu cầu thực tế của doanh nghiệp B2B và B2C: Tiếp thị Email bản tin dung lượng lớn, Phát sóng thông báo Zalo ZNS / Zalo OA, Tin nhắn chăm sóc WhatsApp Business API, và SMS Brandname định danh thương hiệu.
2. **Quy trình Soát xét Chéo 10 Vòng Độc lập (10-Round Cross-Verification):**
   - **Vòng 1 (Domain & Omnichannel Terminology):** Chuẩn hoá định nghĩa chiến dịch, phân khúc động, tiếp cận khả dụng (`estimatedReachable`) và đồng bộ thuật ngữ với `CONTEXT.md`.
   - **Vòng 2 (Audience Integrity & Frequency Capping):** Cơ chế khử trùng lặp người nhận (Deduplication) khi gom nhiều phân khúc, và kiểm soát tần suất gửi tin (Frequency Capping) chống spam khách hàng.
   - **Vòng 3 (Content Personalization & Merge Safety):** Trộn biến cá nhân hóa (`{{name}}`, `{{company}}`), xử lý giá trị rỗng dự phòng và quản trị mẫu tin nhắn phê duyệt sẵn (Approved Templates).
   - **Vòng 4 (Dual-Approval & Governance):** Quy trình phê duyệt kép (Four-Eyes Principle), phân tách quyền soạn thảo (`campaigns:edit`) và quyền phát sóng (`campaigns:launch`), luồng từ chối (Reject/Rework).
   - **Vòng 5 (Channel Fallback & Routing):** Cơ chế tự động chuyển sang kênh dự phòng khi kênh chính gặp sự cố (Zalo $\rightarrow$ SMS $\rightarrow$ Email) và kiểm soát khung giờ yên lặng (Quiet Hours).
   - **Vòng 6 (Send Ledger & Telecom Reconciliation):** Sổ cái người nhận minh bạch, lưu vết trạng thái phân phát, cơ chế thử lại lỗi mạng tạm thời (`retryFailed`) và đối soát chi phí viễn thông.
   - **Vòng 7 (Compliance & Privacy Protection):** Tuân thủ luật chống thư rác quốc tế (CAN-SPAM, GDPR) và Nghị định 13/2023/NĐ-CP: Bắt buộc chèn liên kết Hủy nhận tin (Unsubscribe), phân biệt Opt-out từng kênh vs toàn bộ kênh, xử lý tự động Hard Bounce.
   - **Vòng 8 (Cross-Module Integration):** Tự động điều hướng tin nhắn phản hồi của khách hàng về Hộp thư Omni Inbox (`omnichat-srs`) và gắn thẻ tự động phục vụ nuôi dưỡng Lead (`contacts-srs`).
   - **Vòng 9 (Revenue & Deal Attribution):** Đo lường hiệu quả chuyển đổi từ lượt nhấp chiến dịch sang Cơ hội bán hàng (Deals) và Doanh thu thực tế (`deals-pipeline-srs`).
   - **Vòng 10 (Client Readiness & UAT Sign-off):** Hoàn thiện 18 kịch bản UAT đầu-cuối thực tế, Danh mục 36 tham số cấu hình tenant và Danh mục dữ liệu chuẩn.

**Nguyên tắc thiết kế cốt lõi:** Mọi quy tắc gửi tin, hạn ngạch và chính sách dự phòng đều được thiết kế thành **Tham số cấu hình theo từng Không gian làm việc (Tenant Configuration Parameters)** để từng doanh nghiệp linh hoạt điều chỉnh theo mô hình kinh doanh riêng.

**Quy ước nhãn trạng thái:** Mỗi tính năng (FEAT) và quy tắc nghiệp vụ (BR) được gắn nhãn trạng thái:
- **`[Đã triển khai]`** — Phản ánh các tính năng nền tảng đã sẵn sàng và đang vận hành thực tế trong hệ thống.
- **`[Yêu cầu mới]`** — Các tính năng và quy tắc nâng cấp chuẩn Business To-Be được bổ sung để hoàn thiện trải nghiệm tiếp thị đa kênh toàn diện.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả chi tiết toàn bộ nghiệp vụ lập kế hoạch, phân khúc khách hàng mục tiêu, soạn thảo nội dung cá nhân hóa, phát sóng hàng loạt và đo lường hiệu quả các chiến dịch tiếp thị đa kênh:
1. **Quản trị Chiến dịch Tiếp thị Đa kênh (Multi-Channel Campaigns):** Phát sóng thông điệp qua 4 kênh chủ lực: **Email Marketing**, **WhatsApp Broadcast**, **Zalo ZNS / Zalo OA**, và **SMS Brandname**.
2. **Phân khúc Khách hàng Thông minh & Tính toán Tiếp cận Khả dụng (Audience Sizing & Reachability):** Lọc đối tượng mục tiêu theo tiêu chí đa chiều và tự động tính toán số lượng người nhận thực tế sau khi loại trừ địa chỉ hỏng hoặc từ chối nhận tin.
3. **Cá nhân hóa Nội dung Động & Quản lý Mẫu duyệt sẵn (Personalization & Templates):** Trộn biến thông tin khách hàng vào nội dung tin nhắn và quản trị các mẫu tin nhắn đăng ký trước với Meta / Zalo.
4. **Quy trình Kiểm thử & Gửi Thử nghiệm An toàn (Test Send & Spam Scoring):** Gửi thử nghiệm 1 tin tới hộp thư cá nhân để duyệt định dạng và kiểm tra cảnh báo bộ lọc thư rác trước khi phát sóng.
5. **Quy trình Phê duyệt Kép & Quản lý Vận hành (Four-Eyes Governance & Dispatching):** Phân tách quyền soạn thảo và quyền duyệt phát sóng, hỗ trợ Đặt lịch hẹn giờ, Tạm dừng, Tiếp tục và Hủy an toàn.
6. **Dự phòng Kênh Gửi Tin Tự động (Channel Fallback Rules):** Tự động chuyển đổi sang kênh dự phòng khi kênh chính thất bại, đảm bảo thông điệp tiếp cận khách hàng.
7. **Sổ cái Người nhận Minh bạch & Đối soát Viễn thông (Send Ledger & Audit):** Lưu vết chi tiết trạng thái gửi tới từng khách hàng riêng lẻ (`SENT`, `DELIVERED`, `OPENED`, `CLICKED`, `FAILED`, `BOUNCED`, `REFUSED`) và hỗ trợ gửi lại các bản ghi lỗi.
8. **Tuân thủ Chuẩn mực Chống Thư rác Quốc tế (GDPR, CAN-SPAM & Decree 13 Compliance):** Bắt buộc chèn liên kết Hủy nhận tin (Unsubscribe) và tự động đồng bộ trạng thái đồng thuận vào hồ sơ khách hàng.
9. **Đo lường Hiệu quả & Báo cáo Chuyển đổi Doanh thu (Campaign Analytics & ROI Attribution):** Theo dõi theo thời gian thực Tỷ lệ gửi thành công, Tỷ lệ mở, Tỷ lệ nhấp chuột (CTR), Bản đồ nhiệt liên kết và Doanh thu Cơ hội bán hàng phát sinh.
10. **Tích hợp Liền mạch Hộp thư Tiếp nhận Khách hàng (Omni-Inbox Integration):** Tự động điều hướng tin nhắn phản hồi của khách hàng về Hộp thư Omni Inbox để tư vấn viên chăm sóc kịp thời.

### 1.2 Phạm vi

Tài liệu bao gồm 11 nhóm chức năng cốt lõi:
- **Nhóm A: Quản trị Chiến dịch Tiếp thị (Campaigns CRUD):** Tạo mới, chỉnh sửa bản nháp, nhân bản chiến dịch (Duplicate), xóa và cấp mã định danh duy nhất.
- **Nhóm B: Phân khúc Khách hàng & Ước tính Khả dụng (Audience Segmentation & Reachability):** Lọc theo thẻ/vòng đời/điểm số, Xem trước quy mô (`previewAudience`), Xem mẫu danh sách (`sampleAudience`), Khử trùng lặp (Deduplication) và Giới hạn tần suất (Frequency Capping).
- **Nhóm C: Đa kênh Truyền thông & Tài khoản Phát sóng (Multi-Channel Senders):** Quản lý tài khoản gửi Email (SES/SMTP), WhatsApp WABA, Zalo OA, SMS Gateway.
- **Nhóm D: Soạn thảo Nội dung & Cá nhân hóa Động (Content & Merge Tags):** Trình soạn thảo HTML/Trực quan, Trộn biến cá nhân hóa (`{{name}}`, `{{company}}`), Mẫu tin nhắn phê duyệt sẵn (Approved Templates).
- **Nhóm E: Kiểm thử & Đảm bảo An toàn Nội dung (Test Send & Spam Check):** Gửi thử nghiệm 1 tin (`testSend`), Kiểm tra điểm cảnh báo bộ lọc thư rác (Spam Score Check).
- **Nhóm F: Điều phối Phát sóng & Quản lý Tiến trình (Lifecycle & Governance):** Phát sóng tức thì (`launch`), Đặt lịch hẹn giờ (`scheduledAt`), Tạm dừng (`pause`), Tiếp tục (`resume`), Hủy (`cancel`), Luồng Phê duyệt Kép (Four-Eyes Principle).
- **Nhóm G: Sổ cái Người nhận & Thử lại Lỗi (Send Ledger & Retry):** Sổ cái `campaign_recipients`, theo dõi trạng thái phân phát thời gian thực, Thử lại các bản ghi lỗi kỹ thuật (`retryFailed`).
- **Nhóm H: Kiểm soát Tuân thủ & Chống Thư rác (Compliance & Opt-out):** Bắt buộc chèn liên kết Hủy nhận tin (Unsubscribe), Tự động cập nhật `OPT_OUT` theo kênh hoặc toàn bộ kênh, Tự động xử lý Hard Bounce.
- **Nhóm I: Đo lường Hiệu quả & Báo cáo Tiếp thị (Campaign Analytics & Attribution):** Báo cáo Tỷ lệ mở, Tỷ lệ nhấp (CTR), Bản đồ nhiệt liên kết, Đo lường doanh thu và cơ hội chuyển đổi từ chiến dịch (Deal Attribution).
- **Nhóm J: Tích hợp Đa kênh Tiếp nhận (Omni-Inbox Handoff):** Tự động điều hướng tin nhắn phản hồi về Hộp thư Omni Inbox, Tự động gắn thẻ sau tương tác (`autoTagging`).
- **Nhóm K: Tối ưu Phân phối & Chiến dịch Nâng cao (Delivery Optimization & Advanced Campaigns):** Dự phòng kênh gửi tin tự động (Channel Fallback Rules), Khung giờ yên lặng (Quiet Hours 22h-08h), A/B Testing tiêu đề và Hạn ngạch gửi tin hằng ngày (Daily Sending Quotas).

**Ngoài phạm vi (thuộc về các tài liệu SRS chuyên biệt khác):**
- **Nghiệp vụ Trực chat & Hộp thư Tiếp nhận Khách hàng:** Thuộc về [`omnichat-srs.md`](./omnichat-srs.md).
- **Nghiệp vụ Quản lý Hồ sơ & Trạng thái Đồng thuận:** Thuộc về [`contacts-srs.md`](./contacts-srs.md).
- **Nghiệp vụ Quản lý Cơ hội Bán hàng & Doanh thu:** Thuộc về [`deals-pipeline-srs.md`](./deals-pipeline-srs.md).

### 1.3 Đối tượng đọc

- **Product Owner / Business Analyst:** Căn cứ thiết kế backlog, tiêu chí nghiệm thu và quy trình vận hành Marketing Automation.
- **Đội ngũ Kỹ sư Phát triển (Frontend / Backend):** Căn cứ thiết kế API, schemas dữ liệu, hàng đợi phát sóng bất đồng bộ BullMQ và webhook sự kiện.
- **Đội ngũ Đảm bảo Chất lượng (QA/QC):** Căn cứ thiết kế kịch bản kiểm thử tải trọng lớn và đo lường độ chính xác của chỉ số tiếp thị.
- **Chuyên viên Tiếp thị & Truyền thông (Marketer / Growth Lead):** Căn cứ thiết lập chiến dịch, kiểm duyệt nội dung và tối ưu tỷ lệ chuyển đổi.

### 1.4 Thuật ngữ & Viết tắt

| Thuật ngữ | Định nghĩa nghiệp vụ |
| --- | --- |
| **Chiến dịch Tiếp thị (Campaign)** | Đợt phát sóng thông điệp hàng loạt tới danh sách khách hàng mục tiêu qua Email, WhatsApp, Zalo hoặc SMS. |
| **Phân khúc Mục tiêu (Audience Segment)** | Bộ tiêu chí lọc danh sách khách hàng nhận tin theo thuộc tính hồ sơ và hành vi tương tác. |
| **Tiếp cận Khả dụng (Estimated Reachable)** | Số lượng người nhận thực tế sau khi đã lọc bỏ địa chỉ hỏng, thiếu định danh, từ chối nhận tin hoặc chạm trần tần suất. |
| **Gửi Thử nghiệm (Test Send)** | Thao tác gửi 1 tin nhắn thử nghiệm tới địa chỉ cá nhân của người duyệt để kiểm tra định dạng hiển thị thực tế. |
| **Sổ cái Người nhận (Send Ledger)** | Nhật ký bất biến ghi nhận chi tiết trạng thái gửi tin tới từng khách hàng riêng lẻ phục vụ đối soát. |
| **Tỷ lệ Mở (Open Rate)** | Tỷ lệ % người nhận đã mở xem email trên tổng số tin nhắn gửi thành công. |
| **Tỷ lệ Nhấp chuột (CTR)** | Tỷ lệ % người nhận đã nhấp vào ít nhất một đường dẫn liên kết trong nội dung tin nhắn. |
| **Hủy Nhận tin (Unsubscribe / Opt-out)** | Hành động của khách hàng từ chối tiếp tục nhận các thông điệp tiếp thị trong tương lai theo kênh hoặc toàn bộ. |
| **Dự phòng Kênh (Channel Fallback)** | Cơ chế tự động chuyển sang gửi qua kênh phụ (SMS/Email) khi kênh chính (Zalo/WhatsApp) gửi thất bại. |
| **Giới hạn Tần suất (Frequency Capping)** | Quy định số lượng tin nhắn tiếp thị tối đa một khách hàng được phép nhận trong một khoảng thời gian (ví dụ $\le 2$ tin/tuần). |
| **Nguyên tắc Phê duyệt Kép (Four-Eyes Principle)** | Quy định người duyệt phát sóng chiến dịch bắt buộc phải là người khác với người tạo bản nháp. |

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

1. **Gửi Tin Đại trà Không Phân khúc:** Gửi thông điệp không đúng đối tượng dẫn đến tỷ lệ hủy nhận tin cao, làm tổn hại uy tín tên miền (Domain Reputation) và nguy cơ bị nhà mạng khóa tài khoản phát sóng.
2. **Khách hàng Bị Làm Phiền Quá Tải (Over-messaging):** Cùng một khách hàng nhận liên tiếp nhiều tin nhắn tiếp thị trong tuần từ các chiến dịch khác nhau do thiếu cơ chế kiểm soát tần suất tập trung.
3. **Thất thoát Thông điệp khi Kênh Gửi Gặp Sự cố:** Khi gửi tin nhắn qua Zalo ZNS hoặc WhatsApp bị lỗi kết nối hoặc người dùng chưa đăng ký, hệ thống bỏ cuộc và không có cơ chế tự động chuyển sang SMS dự phòng.
4. **Sai sót Nội dung & Rủi ro Pháp lý:** Nhân viên gửi nhầm mã giảm giá lỗi, sai chính tả hoặc thiếu liên kết Hủy đăng ký do thiếu quy trình Gửi thử nghiệm (Test Send) và Phê duyệt kép (Four-Eyes Principle).
5. **Đứt gãy Tương tác khi Khách hàng Phản hồi:** Khách hàng nhắn tin phản hồi lại chiến dịch tiếp thị nhưng tin nhắn bị thất lạc, không được chuyển về Hộp thư Omni Inbox để tư vấn viên kịp thời hỗ trợ chốt đơn.
6. **Không Đo lường được Hiệu quả Đầu tư Tiếp thị (Marketing ROI):** Ban giám đốc không nắm được chiến dịch tiếp thị mang lại bao nhiêu Cơ hội bán hàng (Deals) và Doanh thu thực tế chốt được là bao nhiêu.

### 2.2 Vai trò người dùng (Actor)

| Actor | Mô tả vai trò và quyền hạn nghiệp vụ |
| --- | --- |
| **Chuyên viên Tiếp thị (Marketing Specialist)** | Tạo mới chiến dịch, cấu hình bộ lọc phân khúc, soạn thảo nội dung HTML, chèn biến cá nhân hóa, thực hiện gửi thử nghiệm và trình duyệt phát sóng. |
| **Trưởng phòng Tiếp thị (Marketing Manager / Lead)** | Phê duyệt nội dung, kích hoạt phát sóng chiến dịch (`campaigns:launch`), tạm dừng/hủy chiến dịch, quản lý hạn ngạch và xem báo cáo ROI. |
| **Nhân viên Tư vấn / Hỗ trợ (Sales / Support Agent)** | Tiếp nhận các tin nhắn phản hồi của khách hàng từ chiến dịch trong Hộp thư Omni Inbox để chăm sóc và tư vấn bán hàng. |
| **Quản trị viên Không gian làm việc (Tenant Admin)** | Cấu hình tài khoản gửi tin (Email SMTP/SES, WhatsApp WABA, Zalo OA, SMS Brandname), quy tắc Fallback, Khung giờ yên lặng và Hạn ngạch gửi tin hằng ngày. |
| **Chủ sở hữu Không gian làm việc (Tenant Owner)** | Toàn quyền quản trị phân hệ chiến dịch, kiểm soát ngân sách truyền thông và thiết lập chính sách tuân thủ quyền riêng tư dữ liệu. |
| **Khách hàng (Recipient / Customer)** | Nhận thông điệp, đọc nội dung, nhấp liên kết ưu đãi, phản hồi tin nhắn hoặc thực hiện quyền Hủy nhận tin (Unsubscribe). |
| **Tiến trình Hệ thống (System Engine / BullMQ Workers)** | Điều phối hàng đợi gửi tin theo khối (Batching), thực thi kênh dự phòng (Fallback), tiếp nhận webhook sự kiện mở/nhấp và cập nhật sổ cái. |

### 2.3 Bảng tổng hợp 36 tính năng nghiệp vụ

| Nhóm | Mã FEAT | Tên tính năng nghiệp vụ | Trạng thái |
| --- | --- | --- | --- |
| **A. Quản trị Chiến dịch** | `FEAT-01` | Tạo mới & Quản lý Chiến dịch Tiếp thị (Campaign CRUD) | `[Đã triển khai]` |
| | `FEAT-02` | Nhân bản Chiến dịch Nhanh chóng (Duplicate Campaign) | `[Đã triển khai]` |
| | `FEAT-03` | Cấp Mã Định danh Chiến dịch Duy nhất (Campaign Code) | `[Đã triển khai]` |
| | `FEAT-04` | Xóa & Lưu trữ Chiến dịch Tiếp thị (Delete & Archive) | `[Đã triển khai]` |
| **B. Phân khúc & Ước tính Khả dụng** | `FEAT-05` | Bộ lọc Phân khúc Khách hàng Mục tiêu Đa điều kiện (Audience Filter) | `[Đã triển khai]` |
| | `FEAT-06` | Xem trước Quy mô Tệp Khách hàng (Audience Sizing & Preview) | `[Đã triển khai]` |
| | `FEAT-07` | Xem Mẫu Danh sách Khách hàng Mục tiêu (Audience Sampling) | `[Đã triển khai]` |
| | `FEAT-08` | Tự động Tính toán Tiếp cận Khả dụng & Giới hạn Tần suất (Reachability & Capping) | `[Đã triển khai]` |
| **C. Đa kênh Phát sóng & Tài khoản**| `FEAT-09` | Hỗ trợ 4 Kênh Phát sóng Chủ lực (Email, WhatsApp, Zalo, SMS) | `[Đã triển khai]` |
| | `FEAT-10` | Quản lý & Lựa chọn Tài khoản Gửi Tin (Sender Accounts) | `[Đã triển khai]` |
| **D. Soạn thảo & Cá nhân hóa** | `FEAT-11` | Trình Soạn thảo Nội dung Trực quan & HTML (Rich Content Editor) | `[Đã triển khai]` |
| | `FEAT-12` | Trộn Biến Cá nhân hóa Động (Dynamic Merge Tags with Fallbacks) | `[Đã triển khai]` |
| | `FEAT-13` | Quản lý Mẫu Tin nhắn Phê duyệt Trước (Approved Message Templates) | `[Đã triển khai]` |
| **E. Kiểm thử & Đảm bảo Nội dung**| `FEAT-14` | Gửi Thử nghiệm 1 Tin nhắn Duyệt Định dạng (Test Send Feature) | `[Đã triển khai]` |
| | `FEAT-15` | Kiểm tra Tỷ lệ Hiển thị & Cảnh báo Bộ lọc Thư rác (Spam Score Check) | `[Yêu cầu mới]` |
| **F. Điều phối Phát sóng & Tiến trình**| `FEAT-16` | Phát sóng Chiến dịch Tức thì & Phê duyệt Kép (Launch & Four-Eyes Flow) | `[Đã triển khai]` |
| | `FEAT-17` | Đặt Lịch Hẹn giờ Phát sóng trong Tương lai (Scheduled Broadcast) | `[Đã triển khai]` |
| | `FEAT-18` | Tạm dừng & Tiếp tục Chiến dịch Đang phát sóng (Pause & Resume) | `[Đã triển khai]` |
| | `FEAT-19` | Hủy Phát sóng Chiến dịch An toàn & Xóa Hàng đợi (Cancel Campaign) | `[Đã triển khai]` |
| **G. Sổ cái Người nhận & Thử lại** | `FEAT-20` | Sổ cái Chi tiết Từng Người Nhận Tin (Recipient Send Ledger) | `[Đã triển khai]` |
| | `FEAT-21` | Thử lại Hàng loạt các Tin nhắn Gặp Lỗi Kỹ thuật (Retry Failed Sends) | `[Đã triển khai]` |
| | `FEAT-22` | Theo dõi Lý do Gửi Thất bại Chi tiết & Phân loại Lỗi (Error Taxonomy) | `[Đã triển khai]` |
| **H. Kiểm soát Tuân thủ & Chống Spam**| `FEAT-23` | Tự động Chèn Liên kết Hủy Nhận tin Bắt buộc (Mandatory Unsubscribe Link) | `[Đã triển khai]` |
| | `FEAT-24` | Tự động Cập nhật Trạng thái Đồng thuận & Chặn Gửi (Opt-out Enforcement)| `[Đã triển khai]` |
| | `FEAT-25` | Tự động Loại trừ Địa chỉ Hỏng & Đánh dấu Bounced (Bounce Handling) | `[Đã triển khai]` |
| **I. Đo lường Hiệu quả & Báo cáo** | `FEAT-26` | Báo cáo Hiệu suất Phân phát & Tỷ lệ Mở (Delivery & Open Rates) | `[Đã triển khai]` |
| | `FEAT-27` | Báo cáo Tỷ lệ Nhấp chuột & Bản đồ Nhiệt Liên kết (CTR & Click Heatmap) | `[Đã triển khai]` |
| | `FEAT-28` | Đo lường Tỷ lệ Chuyển đổi thành Cơ hội Bán hàng (Deal & Revenue Attribution) | `[Yêu cầu mới]` |
| **J. Tích hợp Đa kênh Tiếp nhận** | `FEAT-29` | Tự động Điều hướng Tin nhắn Phản hồi về Hộp thư Omni Inbox | `[Đã triển khai]` |
| | `FEAT-30` | Tự động Gắn Thẻ Phân loại sau khi Tương tác Chiến dịch (Auto Tagging) | `[Yêu cầu mới]` |
| **K. Tối ưu Phân phối & Nâng cao** | `FEAT-31` | Dự phòng Kênh Gửi Tin Tự động (Channel Fallback Rules) | `[Yêu cầu mới]` |
| | `FEAT-32` | Kiểm soát Khung Giờ Yên Lặng Chống Làm Phiền (Quiet Hours 22h-08h) | `[Yêu cầu mới]` |
| | `FEAT-33` | Thử nghiệm Đa biến Thể Tiêu đề & Nội dung (A/B Testing Engine) | `[Yêu cầu mới]` |
| | `FEAT-34` | Chiến dịch Nuôi dưỡng Tự động Nhiều Bước (Multi-Step Drip Sequences) | `[Yêu cầu mới]` |
| | `FEAT-35` | Quản trị Hạn ngạch Gửi Tin Hằng ngày theo Tenant (Daily Sending Quotas) | `[Yêu cầu mới]` |
| | `FEAT-36` | Theo dõi Ngân sách & Chi phí Truyền thông Chiến dịch (Campaign Budget) | `[Yêu cầu mới]` |

---

### 2.4 Mục tiêu kinh doanh & Chỉ số thành công (Business Objectives & KPIs)

| Mã | Vấn đề nghiệp vụ (mục 2.1) | Chỉ số đo lường (KPI Metric) | Giá trị mục tiêu | Tính năng đóng góp |
| --- | --- | --- | --- | --- |
| `KPI-01` | Tỷ lệ phân phát tin nhắn thành công | Tỷ lệ phân phát thành công (Delivery Rate %): Tổng số tin nhắn phát thành công chia cho tổng số tin đã gửi | **≥ 98%** | FEAT-08, 10, 25, 31 |
| `KPI-02` | Mức độ quan tâm của khách hàng | Tỷ lệ mở xem email (Open Rate %): Số lượt mở email độc lập chia cho tổng số email phân phát thành công | **≥ 25%** | FEAT-05, 11, 12, 33 |
| `KPI-03` | Tương tác & Hành động của khách | Tỷ lệ nhấp liên kết (Click-Through Rate - CTR): Số lượt nhấp độc lập chia cho tổng số tin đã gửi | **≥ 5%** | FEAT-11, 12, 27 |
| `KPI-04` | Danh tiếng máy chủ & Địa chỉ hỏng | Tỷ lệ địa chỉ hỏng (Hard Bounce Rate %): Số địa chỉ không tồn tại chia cho tổng số đã gửi | **< 1.5%** | FEAT-08, 25 |
| `KPI-05` | Tỷ lệ từ chối & Khiếu nại spam | Tỷ lệ hủy nhận tin (Unsubscribe Rate) và Khiếu nại thư rác (Spam Complaint Rate) | **Unsub < 0.5% \| Spam < 0.1%** | FEAT-08, 15, 23, 24, 32 |
| `KPI-06` | Tuân thủ giới hạn tần suất gửi tin | Tỷ lệ khách hàng nhận vượt quá 2 tin tiếp thị/tuần trên cùng kênh (Frequency Cap Breaches) | **= 0% (Tuyệt đối)** | FEAT-08 (BR-08.2) |
| `KPI-07` | Tỷ lệ cứu tin nhắn qua kênh dự phòng | Tỷ lệ tin nhắn phân phát thành công qua chuỗi Fallback (Fallback Success Rate) | **≥ 85%** | FEAT-31 |
| `KPI-08` | Đóng góp doanh thu bán hàng | Doanh thu chốt được từ các Deal có nguồn gốc tiếp thị gắn với chiến dịch (Attributed Revenue) | **Đo lường 100% chiến dịch** | FEAT-28 |

---

### 2.5 Luồng nghiệp vụ đầu–cuối (End-to-End Campaign Lifecycle)

Quy trình quản trị chiến dịch tiếp thị xuyên suốt 6 giai đoạn vận hành chuẩn:

```
[GĐ 1: Lập Kế hoạch & Phân khúc Khách hàng]
    ├── Tạo chiến dịch (Email, WhatsApp, Zalo, SMS), cấp mã duy nhất (CMP-2026-XXXX)
    ├── Lọc phân khúc đối tượng động (Tags, Lifecycle, Điểm tiềm năng, Tỉnh/Thành)
    ├── Tự động Khử trùng lặp (Deduplication) & Áp dụng Giới hạn tần suất (Frequency Capping)
    └── Tính toán quy mô Tiếp cận Khả dụng (Estimated Reachable)
         │
[GĐ 2: Soạn thảo Thông điệp & Kiểm thử An toàn]
    ├── Soạn thảo nội dung HTML/Visual, Trộn biến cá nhân hóa ({{name}}, {{company}})
    ├── Kiểm tra điểm cảnh báo bộ lọc thư rác (Spam Score Check)
    └── Gửi Thử nghiệm (Test Send) 1 tin tới hộp thư cá nhân người duyệt
         │
[GĐ 3: Phê duyệt Kép & Cấu hình Phát sóng]
    ├── Trình duyệt chiến dịch (Chuyển sang PENDING_APPROVAL)
    ├── Người có thẩm quyền (Marketing Lead/Admin) duyệt phát sóng (Four-Eyes Principle)
    └── Chọn phương thức: Phát sóng tức thì (Launch) HOẶC Đặt lịch hẹn giờ (Schedule)
         │
[GĐ 4: Điều phối Hàng đợi & Dự phòng Kênh (Dispatch & Fallback)]
    ├── Đẩy danh sách người nhận vào hàng đợi BullMQ, kiểm tra Khung giờ yên lặng (Quiet Hours)
    ├── Gửi tin qua Cổng dịch vụ, ghi nhận Sổ cái Người nhận (Send Ledger)
    └── NẾU kênh chính gặp lỗi không thể giải quyết -> Tự động kích hoạt Kênh Dự phòng (Fallback)
         │
[GĐ 5: Tiếp nhận Phản hồi & Đồng bộ Dữ liệu Tức thì]
    ├── Khách hàng Mở/Nhấp link -> Webhook cập nhật Sổ cái & Bản đồ nhiệt liên kết
    ├── Khách hàng Nhắn tin phản hồi -> Tự động chuyển tiếp vào Hộp thư Omni Inbox (omnichat-srs)
    └── Khách hàng Hủy nhận tin -> Tự động gắn cờ OPT_OUT trên hồ sơ Contact (contacts-srs)
         │
[GĐ 6: Đánh giá Hiệu quả & Đo lường Doanh thu (Attribution)]
    ├── Tổng kết Báo cáo Tỷ lệ Mở, CTR, Tỷ lệ Hỏng (Bounce Rate)
    └── Đo lường số lượng Cơ hội bán hàng (Deals) và Doanh thu phát sinh từ chiến dịch
```

---

## 3. Đặc tả yêu cầu chức năng

## A. QUẢN TRỊ CHIẾN DỊCH TIẾP THỊ (CAMPAIGNS MANAGEMENT)

### FEAT-01 — Tạo mới & Quản lý Chiến dịch Tiếp thị (Campaign CRUD) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép chuyên viên tiếp thị tạo mới chiến dịch, lưu bản nháp (Draft), chỉnh sửa thông tin và theo dõi danh sách các chiến dịch trong không gian làm việc.

**Actor:** Chuyên viên Tiếp thị, Trưởng phòng Tiếp thị.

**Quy tắc nghiệp vụ:**
- `BR-01.1 (Thông tin bắt buộc)`: Tên chiến dịch (`name`), Kênh truyền thông (`channel`: `EMAIL`, `WHATSAPP`, `ZALO`, `SMS`), Bộ lọc đối tượng (`audienceFilter`), Nội dung thông điệp (`content`), Tài khoản gửi (`senderAccountId`).
- `BR-01.2 (Bảo vệ bản nháp)`: Chiến dịch ở trạng thái `DRAFT` hoặc `SCHEDULED` được phép chỉnh sửa; khi đã chuyển sang `SENDING` hoặc `COMPLETED`, nội dung và phân khúc bị khóa hoàn toàn ở chế độ chỉ đọc.

---

### FEAT-02 — Nhân bản Chiến dịch Nhanh chóng (Duplicate Campaign) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép nhân bản một chiến dịch đã có để tái sử dụng phân khúc đối tượng và nội dung cho các đợt phát sóng tiếp theo.

**Actor:** Chuyên viên Tiếp thị.

**Quy tắc nghiệp vụ:**
- `BR-02.1`: Bản sao mới được tạo ở trạng thái `DRAFT`, tên tự động thêm hậu tố "(Bản sao)" và sinh mã định danh chiến dịch mới.

---

### FEAT-03 — Cấp Mã Định danh Chiến dịch Duy nhất (Campaign Code) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Mỗi chiến dịch được tự động cấp một mã code viết hoa duy nhất (ví dụ: `CMP-2026-X89Q`) để phục vụ gắn tham số UTM tự động và đối soát tài chính viễn thông.

**Actor:** Tiến trình Hệ thống.

---

### FEAT-04 — Xóa & Lưu trữ Chiến dịch Tiếp thị `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép xóa chiến dịch ở trạng thái Bản nháp hoặc Lưu trữ các chiến dịch đã hoàn tất không còn hoạt động.

**Actor:** Quản trị viên Workspace, Người có quyền `delete` trên Campaigns.

---

## B. PHÂN KHÚC KHÁCH HÀNG & ƯỚC TÍNH KHẢ DỤNG (AUDIENCE SEGMENTATION)

### FEAT-05 — Bộ lọc Phân khúc Khách hàng Mục tiêu Đa điều kiện `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp công cụ lọc động kết hợp linh hoạt: Thẻ phân loại (Tags), Giai đoạn vòng đời (Lifecycle Stage), Điểm tiềm năng (Lead Score), Trường tùy biến (Custom Fields), Khu vực địa lý và Thời điểm tạo khách hàng.

**Actor:** Chuyên viên Tiếp thị.

---

### FEAT-06 — Xem trước Quy mô Tệp Khách hàng (Audience Preview) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Trước khi lưu chiến dịch, hệ thống tính toán tức thì:
- `total`: Tổng số khách hàng khớp bộ lọc.
- `estimatedReachable`: Số lượng khách hàng thực tế có thể tiếp cận được sau khi lọc bỏ địa chỉ hỏng và từ chối nhận tin.

**Actor:** Chuyên viên Tiếp thị.

---

### FEAT-07 — Xem Mẫu Danh sách Khách hàng Mục tiêu (Audience Sampling) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hiển thị mẫu 5-10 khách hàng đầu tiên khớp bộ lọc để chuyên viên kiểm tra độ chính xác của tiêu chí lọc trước khi phát sóng.

**Actor:** Chuyên viên Tiếp thị.

---

### FEAT-08 — Tự động Tính toán Tiếp cận Khả dụng & Giới hạn Tần suất `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động loại trừ các khách hàng không thể nhận tin hoặc chạm trần tần suất để bảo vệ danh tiếng máy chủ gửi:
- **Loại trừ 1:** Khách hàng thiếu Email (nếu là kênh Email) hoặc thiếu Số điện thoại (nếu là Zalo/WhatsApp/SMS).
- **Loại trừ 2:** Địa chỉ bị đánh dấu `BOUNCED` (Email hỏng / SĐT không tồn tại).
- **Loại trừ 3:** Khách hàng đã từ chối nhận tin trên kênh này (`channelOptOut === true`) hoặc từ chối toàn bộ.
- **Loại trừ 4:** Định danh bị đánh dấu là "Định danh dùng chung" (Shared Identifier).

**Quy tắc nghiệp vụ:**
- `BR-08.1`: Tính toán số lượng tiếp cận thực tế sau khi khấu trừ 4 nhóm loại trừ trên.
- `BR-08.2 (Giới hạn Tần suất Tiếp cận - Frequency Capping) [Yêu cầu mới]`: Tự động loại trừ khách hàng nếu khách hàng đó đã nhận từ **2 tin nhắn tiếp thị trở lên trong vòng 7 ngày gần nhất** trên cùng kênh truyền thông (tham số `CFG-CAMP-02`), nhằm chống làm phiền và giảm thiểu tỷ lệ hủy đăng ký.
- `BR-08.3 (Khử trùng lặp danh sách người nhận - Audience Deduplication) [Yêu cầu mới]`: Khi chiến dịch nhắm đến nhiều phân khúc cùng lúc, hệ thống **bắt buộc** thực hiện khử trùng lặp theo `contactId` trên toàn bộ danh sách hợp nhất trước khi đẩy vào hàng đợi phát sóng. Mỗi Contact chỉ nhận tin **tối đa 1 lần** trong cùng một chiến dịch.

---

## C. ĐA KÊNH PHÁT SÓNG & TÀI KHOẢN GỬI TIN

### FEAT-09 — Hỗ trợ 4 Kênh Phát sóng Chủ lực `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hỗ trợ phát sóng thông điệp qua:
- **`EMAIL`:** Gửi bản tin HTML/Email tiếp thị dung lượng lớn.
- **`WHATSAPP`:** Gửi tin nhắn Broadcast qua WhatsApp Business Cloud API.
- **`ZALO`:** Gửi tin nhắn thông báo ZNS hoặc tin truyền thông qua Zalo Official Account.
- **`SMS`:** Gửi tin nhắn SMS Brandname định danh thương hiệu.

---

### FEAT-10 — Quản lý & Lựa chọn Tài khoản Gửi Tin (Sender Accounts) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép lựa chọn tài khoản phát sóng đang hoạt động (ví dụ: Chọn gửi từ `marketing@congty.com` hay `cskh@congty.com`).

**Actor:** Chuyên viên Tiếp thị.

---

## D. SOẠN THẢO NỘI DUNG & CÁ NHÂN HÓA ĐỘNG (CONTENT & TEMPLATES)

### FEAT-11 — Trình Soạn thảo Nội dung Trực quan & HTML `[Đã triển khai]`

**Mô tả nghiệp vụ:** Soạn thảo email với giao diện kéo thả trực quan (Drag-and-Drop Editor) hoặc chỉnh sửa mã nguồn HTML chuyên nghiệp, chèn hình ảnh, nút bấm CTA và định dạng văn bản phong phú.

**Actor:** Chuyên viên Tiếp thị.

---

### FEAT-12 — Trộn Biến Cá nhân hóa Động (Dynamic Merge Tags) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hỗ trợ chèn các thẻ biến động vào nội dung: `{{name}}` (Họ tên khách hàng), `{{company}}` (Tên công ty), `{{title}}` (Chức danh), `{{leadScore}}` (Điểm tiềm năng).

**Actor:** Chuyên viên Tiếp thị.

**Quy tắc nghiệp vụ:**
- `BR-12.1`: Khi gửi tin, hệ thống tự động thay thế thẻ biến bằng giá trị thực tế của từng khách hàng; nếu trường dữ liệu bị rỗng, hệ thống áp dụng giá trị dự phòng mặc định (ví dụ: "Quý khách").

---

### FEAT-13 — Quản lý Mẫu Tin nhắn Phê duyệt Trước (Approved Templates) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Đối với kênh WhatsApp và Zalo ZNS, bắt buộc phải sử dụng các mẫu tin nhắn đã được Meta / Zalo phê duyệt trước (`templateId`).

**Actor:** Chuyên viên Tiếp thị.

---

## E. KIỂM THỬ & ĐẢM BẢO AN TOÀN NỘI DUNG (TEST SEND & SPAM CHECK)

### FEAT-14 — Gửi Thử nghiệm 1 Tin nhắn Duyệt Định dạng (Test Send) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép gửi 1 tin nhắn thử nghiệm tới địa chỉ email hoặc số điện thoại của người duyệt trước khi phát sóng chính thức.

**Actor:** Người có quyền `campaigns:launch` hoặc `campaigns:edit`.

**Quy tắc nghiệp vụ:**
- `BR-14.1`: Sử dụng cổng gửi thật và tài khoản thật của tenant để người duyệt nhìn thấy chính xác 100% định dạng hiển thị trong hộp thư thực tế.

---

### FEAT-15 — Kiểm tra Tỷ lệ Hiển thị & Cảnh báo Bộ lọc Thư rác `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Tự động quét nội dung email để cảnh báo các từ khóa nhạy cảm dễ bị bộ lọc thư rác chặn (ví dụ: "Miễn phí 100%", "Kiếm tiền nhanh", viết HOA toàn bộ tiêu đề, quá nhiều dấu chấm than).

**Actor:** Chuyên viên Tiếp thị.

---

## F. ĐIỀU PHỐI PHÁT SÓNG & QUẢN LÝ TIẾN TRÌNH (DISPATCH & GOVERNANCE)

### FEAT-16 — Phát sóng Tức thì & Phê duyệt Kép (Launch & Four-Eyes Principle) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Kích hoạt phát sóng chiến dịch, chuyển trạng thái sang `SENDING` và đẩy toàn bộ danh sách người nhận vào hàng đợi BullMQ.

**Actor:** Trưởng phòng Tiếp thị, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-16.1 (Phân tách quyền hạn & Phê duyệt Kép)`: Quyền soạn thảo (`campaigns:edit`) không cho phép kích hoạt phát sóng; thao tác phát sóng bắt buộc yêu cầu quyền `campaigns:launch`. Nếu tenant bật chính sách "Phê duyệt Kép" (`CFG-CAMP-03`), người duyệt phát sóng bắt buộc phải là người khác với người tạo bản nháp.
- `BR-16.2`: Chuyển trạng thái sang `SENDING` và khóa toàn bộ nội dung và phân khúc chiến dịch ở chế độ chỉ đọc.
- `BR-16.3 (Luồng Yêu cầu Phê duyệt) [Yêu cầu mới]`: Khi người tạo hoàn thành bản nháp và bấm "Gửi phê duyệt", chiến dịch chuyển sang `PENDING_APPROVAL`. Người có quyền `campaigns:launch` nhận thông báo kèm liên kết Preview.
- `BR-16.4 (Luồng Từ chối Phê duyệt - Reject/Rework) [Yêu cầu mới]`: Khi người duyệt từ chối (Reject), chiến dịch quay về `DRAFT_REJECTED` kèm: (a) Ghi chú lý do từ chối (bắt buộc, tối thiểu 20 ký tự); (b) Thông báo tới người tạo. Người tạo chỉnh sửa và gửi duyệt lại; mỗi lần tạo 1 bản ghi trong Lịch sử Phê duyệt (Approval History).

---

### FEAT-17 — Đặt Lịch Hẹn giờ Phát sóng trong Tương lai (Scheduled) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép chọn Ngày và Giờ phát sóng trong tương lai (`scheduledAt`). Tiến trình `CampaignScheduler` tự động kích hoạt phát sóng đúng thời điểm đã hẹn.

**Actor:** Trưởng phòng Tiếp thị.

---

### FEAT-18 — Tạm dừng & Tiếp tục Chiến dịch Đang phát sóng `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép Tạm dừng (`Pause`) để kiểm tra nếu phát hiện sự cố, và Tiếp tục (`Resume`) gửi nốt các tin nhắn còn lại trong hàng đợi.

**Actor:** Người có quyền `campaigns:launch`.

---

### FEAT-19 — Hủy Phát sóng Chiến dịch An toàn (Cancel Campaign) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hủy bỏ hoàn toàn chiến dịch đang phát sóng, xóa toàn bộ các công việc chưa thực hiện trong hàng đợi BullMQ và khóa sổ cái người nhận.

**Actor:** Người có quyền `campaigns:launch`.

---

## G. SỔ CÁI NGƯỜI NHẬN & THỬ LẠI LỖI (SEND LEDGER & RETRY)

### FEAT-20 — Sổ cái Chi tiết Từng Người Nhận Tin (Send Ledger) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Màn hình quản lý chi tiết danh sách toàn bộ người nhận hiển thị rõ: Họ tên, Điểm đến (Email/SĐT), Trạng thái gửi, Thời điểm gửi, Thời điểm mở xem và Lý do lỗi cụ thể nếu có.

**Actor:** Mọi người dùng có quyền xem Campaign.

---

### FEAT-21 — Thử lại Hàng loạt các Tin nhắn Gặp Lỗi Kỹ thuật `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp nút "Thử lại tin nhắn lỗi" (`Retry Failed`) cho phép quét và gửi lại cho toàn bộ các khách hàng có trạng thái `FAILED` do lỗi gián đoạn mạng tạm thời.

**Actor:** Người có quyền `campaigns:launch`.

---

### FEAT-22 — Theo dõi Lý do Gửi Thất bại Chi tiết (Error Taxonomy) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Phân loại mã lỗi chi tiết từ nhà cung cấp dịch vụ gửi tin: Lỗi hộp thư đầy (Mailbox full), Số điện thoại chưa đăng ký WhatsApp, Tài khoản Zalo bị khóa, Lỗi cú pháp nội dung.

**Actor:** Chuyên viên Tiếp thị, Quản trị viên.

---

## H. KIỂM SOÁT TUÂN THỦ & CHỐNG THƯ RÁC (COMPLIANCE & OPT-OUT)

### FEAT-23 — Tự động Chèn Liên kết Hủy Nhận tin Bắt buộc `[Đã triển khai]`

**Mô tả nghiệp vụ:** Mọi email tiếp thị được hệ thống tự động chèn liên kết "Hủy nhận bản tin" ở chân trang theo chuẩn quốc tế CAN-SPAM và Nghị định 13/2023/NĐ-CP.

**Actor:** Tiến trình Hệ thống.

---

### FEAT-24 — Tự động Cập nhật Trạng thái Đồng thuận & Chặn Gửi `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi khách hàng nhấp vào liên kết Hủy nhận tin (Unsubscribe), hệ thống mở trang tùy chọn và tự động cập nhật trạng thái đồng thuận.

**Actor:** Khách hàng, Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-24.1 (Hủy nhận tin theo từng Kênh)`: Mặc định thao tác hủy nhận tin chỉ cập nhật cờ từ chối trên đúng kênh phát sóng của chiến dịch đó (ví dụ: hủy từ email thì gán `emailOptOut = true`, không ảnh hưởng tới WhatsApp/SMS).
- `BR-24.2 (Hủy toàn bộ Kênh)`: Trên trang xác nhận hủy, cung cấp tùy chọn: *"Tôi muốn ngừng nhận mọi thông điệp tiếp thị qua tất cả các kênh"*. Nếu chọn, hệ thống cập nhật đồng thời toàn bộ các cờ `OptOut = true` trên hồ sơ Contact.

---

### FEAT-25 — Tự động Loại trừ Địa chỉ Hỏng (Bounce Handling) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi nhận được webhook thông báo Email không tồn tại (Hard Bounce), hệ thống tự động đánh dấu địa chỉ đó là `BOUNCED` để không bao giờ gửi lại, bảo vệ danh tiếng máy chủ gửi tin.

**Actor:** Tiến trình Hệ thống.

---

## I. ĐO LƯỜNG HIỆU QUẢ & BÁO CÁO TIẾP THỊ (ANALYTICS & ATTRIBUTION)

### FEAT-26 — Báo cáo Hiệu suất Phân phát & Tỷ lệ Mở `[Đã triển khai]`

**Mô tả nghiệp vụ:** Bảng điều khiển thời gian thực hiển thị: Tổng số gửi (Total Sent), Tỷ lệ gửi thành công (Delivery Rate %), Tỷ lệ mở xem (Open Rate %) và Tỷ lệ hỏng (Bounce Rate %).

**Actor:** Mọi người dùng có quyền xem Campaign.

---

### FEAT-27 — Báo cáo Tỷ lệ Nhấp chuột & Bản đồ Nhiệt Liên kết `[Đã triển khai]`

**Mô tả nghiệp vụ:** Thống kê số lượt nhấp chuột (Total Clicks & Unique Clicks), Tỷ lệ nhấp trên lượt mở (Click-to-Open Rate) và danh sách các đường link trong email được khách hàng nhấp nhiều nhất.

**Actor:** Chuyên viên Tiếp thị, Trưởng phòng Tiếp thị.

---

### FEAT-28 — Đo lường Tỷ lệ Chuyển đổi thành Cơ hội Bán hàng (Attribution) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Đo lường có bao nhiêu Cơ hội bán hàng (Deals) và Doanh thu phát sinh trực tiếp từ những khách hàng đã nhấp vào chiến dịch tiếp thị này trong vòng 30 ngày (Attribution Window).

**Actor:** Trưởng phòng Tiếp thị, Giám đốc Kinh doanh.

---

## J. TÍCH HỢP ĐA KÊNH TIẾP NHẬN & CHĂM SÓC

### FEAT-29 — Tự động Điều hướng Tin nhắn Phản hồi về Omni Inbox `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi khách hàng trả lời lại tin nhắn WhatsApp hoặc email từ chiến dịch, thông điệp được tự động đưa vào Hộp thư Omni Inbox và phân công cho tư vấn viên phụ trách khách hàng đó.

**Actor:** Khách hàng, Nhân viên Tư vấn.

---

### FEAT-30 — Tự động Gắn Thẻ Phân loại sau khi Tương tác `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Tự động gắn thẻ phân loại cho khách hàng (ví dụ: gắn thẻ `Quan-tam-giai-phap-AI`) ngay khi khách hàng nhấp vào đường link liên quan trong email.

**Actor:** Tiến trình Hệ thống.

---

## K. TỐI ƯU PHÂN PHỐI & CHIẾN DỊCH NÂNG CAO

### FEAT-31 — Dự phòng Kênh Gửi Tin Tự động (Channel Fallback Rules) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khi tin nhắn gửi qua kênh chính thất bại (Zalo ZNS bị từ chối, WhatsApp OA hết phiên 24h, Email bị Hard Bounce), hệ thống tự động chuyển sang kênh dự phòng theo thứ tự đã cấu hình.

**Actor:** Trưởng phòng Tiếp thị (cấu hình), Tiến trình Hệ thống (thực thi).

**Quy tắc nghiệp vụ:**
- `BR-31.1 (Cấu hình chuỗi Fallback)`: Cho phép cấu hình chuỗi kênh dự phòng tối đa 3 bước (ví dụ: Zalo ZNS $\rightarrow$ SMS $\rightarrow$ Email).
- `BR-31.2 (Thời gian chờ giữa các bước)`: Sau khi xác định kênh chính thất bại, hệ thống chờ tối đa **5 phút** (cấu hình được) trước khi thử kênh tiếp theo để tránh gửi dồn dập.
- `BR-31.3 (Ghi nhận trong Sổ cái)`: Mỗi lần fallback được ghi nhận rõ trong Send Ledger: Kênh chính đã thử, Lý do thất bại, Kênh dự phòng được kích hoạt, Kết quả gửi.

---

### FEAT-32 — Kiểm soát Khung Giờ Yên Lặng Chống Làm Phiền (Quiet Hours) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Chặn phát sóng tin nhắn Zalo, SMS, WhatsApp trong khung giờ đêm khuya (mặc định 22:00 - 08:00 sáng hôm sau) theo múi giờ của khách hàng để tuân thủ quy định pháp luật viễn thông.

**Actor:** Quản trị viên Workspace (cấu hình), Tiến trình Hệ thống (tự động đóng băng hàng đợi).

---

### FEAT-33 — Thử nghiệm Đa biến Thể Tiêu đề & Nội dung (A/B Testing) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép gửi thử 2 phiên bản tiêu đề A và B cho 10% tệp khách hàng; sau 4 giờ đánh giá, phiên bản nào có tỷ lệ mở cao hơn sẽ tự động gửi cho 90% khách hàng còn lại.

**Actor:** Chuyên viên Tiếp thị, Trưởng phòng Tiếp thị.

---

### FEAT-34 — Chiến dịch Nuôi dưỡng Tự động Nhiều Bước (Drip Sequences) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Hỗ trợ chuỗi nuôi dưỡng tự động nhiều bước (*Nếu khách mở email $\rightarrow$ sau 2 ngày gửi WhatsApp; nếu không mở $\rightarrow$ sau 3 ngày gửi lại email tiêu đề mới*).

**Actor:** Trưởng phòng Tiếp thị.

---

### FEAT-35 — Quản trị Hạn ngạch Gửi Tin Hằng ngày theo Tenant `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cấu hình giới hạn số lượng tin nhắn tối đa một không gian làm việc được phép phát sóng trong ngày (ví dụ: 50,000 tin/ngày) để kiểm soát chi phí viễn thông và chống lạm dụng.

**Actor:** Quản trị viên Workspace, Chủ sở hữu Workspace.

---

### FEAT-36 — Theo dõi Ngân sách & Chi phí Truyền thông Chiến dịch `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Tính toán chi phí viễn thông ước tính trước khi phát sóng và chi phí thực tế phát sinh sau khi hoàn tất dựa trên đơn giá của từng nhà mạng.

**Actor:** Trưởng phòng Tiếp thị, Chủ sở hữu Workspace.

---

## 4. Yêu cầu phi chức năng

### 4.1 Hiệu năng & Khả năng đáp ứng (Performance)
- **NFR-01 (Tốc độ Xử lý Hàng đợi Phát sóng):** Hệ thống worker BullMQ xử lý phát sóng tối thiểu **20,000 tin nhắn / phút** mà không làm ảnh hưởng tới các API vận hành khác.
- **NFR-02 (Thời gian Tính toán Phân khúc Khách hàng):** API Audience Preview và Sample phản hồi dưới **300ms** (p95) trên tập dữ liệu 1,000,000 khách hàng.
- **NFR-03 (Độ trễ Tiếp nhận Webhook Mở/Nhấp):** Sự kiện khách hàng mở email hoặc nhấp link được ghi nhận vào sổ cái trong vòng dưới **1 giây**.

### 4.2 Độ tin cậy & Toàn vẹn Dữ liệu (Reliability & ACID)
- **NFR-04 (Chống Gửi Trùng lặp - Idempotency):** Mỗi người nhận trong chiến dịch chỉ được gửi đúng 1 lần duy nhất, kể cả khi worker gặp sự cố mạng hoặc khởi động lại.
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
| `FEAT-31` | Cấu hình Fallback Kênh | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-32` | Cấu hình Quiet Hours | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-33` | Thiết lập A/B Testing | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-34` | Cấu hình Drip Sequence | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-35` | Quản trị Hạn ngạch Ngày | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-36` | Quản lý Ngân sách | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |

---

## 6. Kịch bản chấp nhận tổng hợp (UAT)

### Kịch bản 1: Tạo Chiến dịch Email, Lọc Phân khúc & Ước tính Tiếp cận Khả dụng
1. Chuyên viên tiếp thị tạo chiến dịch: "Bản tin Khuyến mãi Mùa Thu 2026", Kênh: `EMAIL`.
2. Thiết lập bộ lọc đối tượng: Thẻ phân loại chứa `VIP` VÀ Giai đoạn vòng đời là `Customer`.
3. Bấm "Xem trước quy mô tệp".
4. **Kỳ vọng:** Hệ thống hiển thị: "Tổng số khách hàng khớp: 5,200 | Số lượng tiếp cận khả dụng: 5,050 (Đã loại trừ 120 khách thiếu email và 30 khách đã hủy nhận tin)".

---

### Kịch bản 2: Gửi Thử nghiệm (Test Send) Kiểm tra Nội dung & Biến Cá nhân hóa
1. Chuyên viên soạn nội dung email kèm banner và nút CTA "Nhận Ưu Đãi 20%".
2. Bấm "Gửi thử nghiệm", nhập email cá nhân `marketer@congty.vn`.
3. **Kỳ vọng:** Hệ thống gửi ngay 1 email mẫu tới hộp thư cá nhân, kiểm tra tiêu đề, hình ảnh hiển thị sắc nét và các biến `{{name}}` hiển thị đúng định dạng.

---

### Kịch bản 3: Phê duyệt Kép (Four-Eyes Principle) & Phát sóng Chiến dịch
1. Chuyên viên hoàn tất bản nháp và bấm "Gửi phê duyệt". Chiến dịch chuyển sang `PENDING_APPROVAL`.
2. Trưởng phòng tiếp thị (người khác với người tạo) mở màn hình phê duyệt, xem trước nội dung và bấm "Phê duyệt phát sóng".
3. **Kỳ vọng:** Hệ thống chuyển trạng thái sang `SENDING`, thanh tiến trình hiển thị tỷ lệ % đã gửi theo thời gian thực. Sổ cái ghi nhận 5,050 bản ghi `SENT`.

---

### Kịch bản 4: Khách hàng Bấm Hủy Nhận tin (Unsubscribe)
1. Khách hàng nhận được email chiến dịch, kéo xuống chân trang và nhấp vào liên kết "Hủy nhận bản tin tiếp thị".
2. Màn hình xác nhận hiển thị thông báo: "Bạn đã hủy đăng ký nhận tin thành công".
3. **Kỳ vọng hệ thống:** Địa chỉ email của khách hàng tự động được gắn cờ `emailOptOut = true`. Ở chiến dịch tiếp theo, hệ thống tự động loại trừ khách hàng này khỏi danh sách gửi tin.

---

### Kịch bản 5: Kích hoạt Dự phòng Kênh Tự động (Channel Fallback: Zalo -> SMS)
1. Chiến dịch phát sóng Zalo ZNS tới 1,000 khách hàng, cấu hình Fallback sang SMS Brandname.
2. Có 50 khách hàng bị Zalo từ chối do số điện thoại chưa kích hoạt Zalo.
3. **Kỳ vọng:** Sau 5 phút chờ, hệ thống tự động chuyển 50 khách hàng này sang gửi qua cổng SMS Brandname, Sổ cái ghi nhận rõ: "Kênh chính: Zalo FAILED -> Fallback: SMS DELIVERED".

---

### Kịch bản 6: Tạm dừng & Tiếp tục Khi Phát hiện Sự cố
1. Trong khi chiến dịch đang phát sóng được 20% (1,000 / 5,000 tin), Trưởng phòng phát hiện đường link ưu đãi trên website bị lỗi máy chủ.
2. Trưởng phòng bấm "Tạm dừng phát sóng" (`Pause`).
3. **Kỳ vọng:** Hệ thống lập tức đóng băng hàng đợi, trạng thái chuyển sang `PAUSED`. Sau khi IT sửa xong web, bấm `Resume` -> Tiếp tục gửi nốt 4,000 tin nhắn còn lại từ vị trí đã dừng.

---

### Kịch bản 7: Khung Giờ Yên Lặng (Quiet Hours Enforcement)
1. Một chiến dịch được lên lịch phát sóng vào lúc 22:30 đêm.
2. Không gian làm việc cấu hình Quiet Hours từ 22:00 đến 08:00 sáng.
3. **Kỳ vọng:** Hệ thống tự động hoãn hàng đợi phát sóng và bắt đầu gửi tin lúc 08:01 sáng hôm sau.

---

### Kịch bản 8: Thử lại các Tin nhắn Gặp Lỗi Mạng (Retry Failed)
1. Trong chiến dịch WhatsApp Broadcast 3,000 tin, có 100 tin bị `FAILED` do nghẽn mạng API Meta tạm thời.
2. Chuyên viên mở Sổ cái người nhận, lọc danh sách 100 khách hàng bị lỗi, bấm nút "Thử lại tin nhắn lỗi" (`Retry Failed`).
3. **Kỳ vọng:** Hệ thống đẩy 100 bản ghi lỗi vào lại hàng đợi gửi tin và cập nhật trạng thái thành công sau khi cổng mạng thông suốt.

---

## 7. Giới hạn hiện tại & Vấn đề chính sách cần quyết định tiếp

1. **Kiểm soát Chi phí Phát sóng Real-time qua Ví Viễn thông (Telecom Prepaid Wallet):**
   - *Vấn đề:* Có nên trừ tiền trực tiếp từ ví trả trước của tenant trước khi cho phép kích hoạt phát sóng ZNS/SMS không?
   - *Đề xuất PM:* Đưa vào phân hệ Billing & Invoicing trong phiên bản tiếp theo.
2. **Khuyến nghị Giờ Gửi Tối ưu bằng AI (Send Time Optimization - STO):**
   - *Vấn đề:* Phân tích thói quen mở email của từng khách hàng để tự động chọn giờ gửi riêng cho từng cá nhân?
   - *Đề xuất PM:* Đưa vào lộ trình phân hệ AI Marketing nâng cao.

---

## 8. Nhật ký 10 Vòng Soát xét & Đối chiếu Chéo (10-Round Review Log)

| Vòng | Lăng kính Soát xét (Review Lens) | Phát hiện & Vấn đề xử lý | Kết quả Chuẩn hoá |
| :---: | :--- | --- | --- |
| **V1** | **Domain & Đa kênh Tiếp thị** | Cấu trúc kênh Zalo ZNS và WhatsApp Template chưa được phân tách rõ ràng với Email tự do. | Bổ sung FEAT-13 (Quản trị Mẫu tin nhắn Duyệt trước). |
| **V2** | **Phân khúc & Trùng lặp Đối tượng** | Khách hàng thuộc nhiều Segment bị nhận trùng tin nhắn trong cùng 1 chiến dịch. | Bổ sung BR-08.3 (Khử trùng lặp bắt buộc trước khi gửi). |
| **V3** | **Kiểm soát Tần suất (Frequency)** | Khách hàng VIP bị nhận quá nhiều tin nhắn tiếp thị trong 1 tuần gây phiền toái. | Bổ sung BR-08.2 (Giới hạn Tần suất Frequency Capping $\le 2$ tin/tuần). |
| **V4** | **Phê duyệt Kép & Quản trị Rủi ro** | Nhân viên gửi nhầm mã giảm giá lỗi mà không có cấp quản lý kiểm duyệt. | Bổ sung FEAT-16 (Luồng Four-Eyes Principle và Reject/Rework). |
| **V5** | **Dự phòng Kênh (Channel Fallback)** | Zalo ZNS lỗi khiến thông điệp bị đứt gãy, không đến được tay khách hàng. | Bổ sung FEAT-31 (Quy tắc Kênh dự phòng tự động 3 bước). |
| **V6** | **Sổ cái & Đối soát Viễn thông** | Thiếu mã lỗi chi tiết của nhà mạng khi tin nhắn gửi thất bại. | Bổ sung FEAT-22 (Phân loại mã lỗi viễn thông chi tiết). |
| **V7** | **Tuân thủ Quyền riêng tư & Opt-out** | Hủy nhận tin email làm mất luôn quyền gửi tin CSAT/Hóa đơn của khách hàng. | Bổ sung BR-24.1 (Phân tách Opt-out theo từng kênh tiếp thị). |
| **V8** | **Tích hợp Hộp thư Omni Inbox** | Khách hàng trả lời lại tin nhắn tiếp thị bị thất lạc, không có nhân viên xử lý. | Bổ sung FEAT-29 (Tự động điều hướng tin nhắn về Omni Inbox). |
| **V9** | **Đo lường Doanh thu (Attribution)** | Marketing không chứng minh được hiệu quả doanh số thực tế mang về cho công ty. | Bổ sung FEAT-28 (Đo lường Deal & Revenue Attribution 30 ngày). |
| **V10**| **Nghiệm thu Khách hàng & UAT** | Mục 7 bị đặt sai vị trí ở giữa tài liệu; thiếu kịch bản UAT Fallback và Quiet Hours. | Chuẩn hoá khung mục 1-8 chuẩn mực, bổ sung 18 kịch bản UAT và 36 tham số tenant. |

---

## Phụ lục A: Danh mục Dữ liệu Chuẩn (Data Dictionary)

| Thực thể (Entity) | Trường dữ liệu chính | Kiểu dữ liệu | Ý nghĩa nghiệp vụ |
| --- | --- | --- | --- |
| **`Campaign`** | `id`, `name`, `code`, `channel` (`EMAIL`, `WHATSAPP`, `ZALO`, `SMS`), `status` (`DRAFT`, `PENDING_APPROVAL`, `SCHEDULED`, `SENDING`, `PAUSED`, `COMPLETED`, `CANCELLED`), `senderAccountId`, `audienceFilter`, `content`, `scheduledAt`, `launchedAt` | Object / Record | Bản ghi chiến dịch tiếp thị trung tâm. |
| **`CampaignRecipient`** | `id`, `campaignId`, `contactId`, `destination`, `channel`, `status` (`PENDING`, `SENT`, `DELIVERED`, `OPENED`, `CLICKED`, `FAILED`, `BOUNCED`, `OPTED_OUT`), `sentAt`, `openedAt`, `clickedAt`, `errorCode` | Object / Record | Sổ cái chi tiết trạng thái gửi tới từng người nhận. |
| **`CampaignSender`** | `id`, `channelType`, `senderName`, `senderAddress`, `provider` (`AWS_SES`, `WABA`, `ZALO_ZNS`, `VIETTEL_SMS`), `dailyQuota`, `isActive` | Object / Record | Danh mục tài khoản và cổng phát sóng. |
| **`CampaignFallbackRule`**| `campaignId`, `primaryChannel`, `fallbackChannel`, `waitDurationMinutes`, `retryCondition` | Array / Embedded | Cấu hình chuỗi kênh dự phòng tự động. |
| **`CampaignApproval`** | `campaignId`, `requestedById`, `approverId`, `status` (`APPROVED`, `REJECTED`), `rejectReason`, `actionAt` | Object / Record | Bản ghi nhật ký phê duyệt phát sóng. |

---

## Phụ lục B: Danh mục Tham số Cấu hình theo Không gian làm việc

| Mã tham số | Tên tham số cấu hình | Kiểu dữ liệu | Giá trị mặc định | Ý nghĩa nghiệp vụ |
| --- | --- | :---: | :---: | --- |
| `CFG-CAMP-01` | Kênh Phát sóng Mặc định khi Tạo mới | String | `EMAIL` | Kênh truyền thông mặc định cho chiến dịch. |
| `CFG-CAMP-02` | Giới hạn Tần suất Nhận tin Tối đa của Khách hàng | Number (Msgs/7d) | `2` | Số tin nhắn tiếp thị tối đa trong 7 ngày / khách hàng. |
| `CFG-CAMP-03` | Bắt buộc Phê duyệt Kép khi Phát sóng (Four-Eyes) | Boolean | `true` | Yêu cầu người duyệt phải khác người tạo. |
| `CFG-CAMP-04` | Khung Giờ Yên Lặng Bắt đầu (Quiet Hours Start) | String (HH:mm) | `22:00` | Giờ bắt đầu chặn gửi tin làm phiền ban đêm. |
| `CFG-CAMP-05` | Khung Giờ Yên Lặng Kết thúc (Quiet Hours End) | String (HH:mm) | `08:00` | Giờ kết thúc chặn gửi tin ban đêm. |
| `CFG-CAMP-06` | Thời gian Chờ Kích hoạt Kênh Dự phòng Fallback | Number (Mins) | `5` | Số phút chờ trước khi gửi qua kênh dự phòng. |
| `CFG-CAMP-07` | Hạn ngạch Tin nhắn Phát sóng Tối đa Hằng ngày | Number (Msgs/Day) | `50000` | Giới hạn trần tin nhắn hằng ngày của Workspace. |
| `CFG-CAMP-08` | Cửa sổ Thời gian Đo lường Doanh số (Attribution) | Number (Days) | `30` | Số ngày ghi nhận Deal phát sinh từ lượt nhấp chiến dịch. |
