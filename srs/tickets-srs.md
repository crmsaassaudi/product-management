# SRS — Phân hệ Quản lý Vé Hỗ trợ & Dịch vụ Khách hàng (Tickets & Customer Service Management)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA (Version 2.0) |
| **Module** | CRM — Phân hệ Quản lý Vé Hỗ trợ & Dịch vụ Khách hàng (Tickets & Customer Service Management) |
| **Ngày cập nhật** | 2026-08-28 |
| **Phiên bản** | v2.0 (Target Standard) |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`contacts-srs.md`](./contacts-srs.md), [`deals-pipeline-srs.md`](./deals-pipeline-srs.md), [`omnichat-srs.md`](./omnichat-srs.md), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) |

## Ghi chú về nguồn gốc tài liệu

Tài liệu này được xây dựng thông qua quy trình chuẩn hoá 4 bước:
1. **Khảo sát toàn diện mã nguồn thực tế (As-Is):** Khảo sát toàn bộ hệ thống API, schemas, SLA projectors, CSAT controllers, ticket message schemas, parent-child hierarchies, import/export processors và ticket reports trong `crm-api` (`src/tickets/`, `src/ticket-settings/`, `src/reports/ticket/`) và `crm-web` (`src/features/tickets/`).
2. **Rà soát & Đối chiếu Chuyên sâu:** Kiểm tra chéo từng quy tắc tạm dừng/tiếp tục đồng hồ SLA (SLA Pause & Resume), đồng bộ phản hồi từ hội thoại đa kênh (`TicketConversationReplyListener`), phân cấp vé cha - con (Parent-Child Tickets), gộp vé hỗ trợ (Ticket Merge) và cơ chế khảo sát mức độ hài lòng (CSAT).
3. **Chuẩn hoá Nghiệp vụ Business First:** Đối chiếu với các chuẩn mực B2B SaaS Helpdesk/Service CRM hàng đầu thế giới (Zendesk Support, Freshdesk, HubSpot Service Hub, Salesforce Service Cloud), loại bỏ các rào cản kỹ thuật và xây dựng quy trình dịch vụ khách hàng chuyên nghiệp.
4. **Đóng băng Đặc tả Mục tiêu:** Hoàn thiện bộ 30 tính năng nghiệp vụ cốt lõi, ma trận phân quyền, 6 kịch bản UAT và danh mục chính sách nghiệp vụ.

**Quy ước nhãn trạng thái:** Mỗi tính năng (FEAT) và quy tắc nghiệp vụ (BR) được gắn nhãn trạng thái:
- **`[Đã triển khai]`** — Phản ánh các tính năng nền tảng đã sẵn sàng và đang vận hành thực tế trong hệ thống.
- **`[Yêu cầu mới]`** — Các tính năng và quy tắc nâng cấp chuẩn Business To-Be được bổ sung để hoàn thiện trải nghiệm dịch vụ khách hàng toàn diện.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả chi tiết toàn bộ nghiệp vụ tiếp nhận, xử lý, cam kết chất lượng dịch vụ (SLA) và đo lường sự hài lòng của khách hàng (CSAT) thông qua hệ thống Vé Hỗ trợ (Tickets):
1. **Quản trị Vòng đời Vé Hỗ trợ (Tickets Management):** Tiếp nhận đa kênh (Email, Livechat, WhatsApp, Biểu mẫu, Hotline), cấp phát mã định danh duy nhất (ví dụ: `TK-10023`), phân loại mức độ ưu tiên và theo dõi tiến trình xử lý.
2. **Cam kết Chất lượng Dịch vụ (SLA Management & Escalation):** Thiết lập chính sách SLA thời gian phản hồi đầu tiên và thời gian giải quyết theo mức độ ưu tiên, cảnh báo vi phạm SLA và cơ chế tự động tạm dừng đếm giờ khi chờ khách hàng phản hồi.
3. **Trao đổi Đa luồng Minh bạch (Public Reply vs Internal Note):** Tách bạch giữa thông điệp phản hồi công khai gửi tới khách hàng và ghi chú trao đổi nội bộ bảo mật giữa các nhân viên hỗ trợ.
4. **Đo lường Mức độ Hài lòng (CSAT Surveys):** Tự động kích hoạt khảo sát 1-5 sao ngay sau khi giải quyết vé để thu thập phản hồi và cải thiện chất lượng dịch vụ.
5. **Quản trị Sự cố Lớn & Phân cấp Vé (Parent-Child Tickets):** Liên kết nhiều vé khiếu nại của khách hàng vào một sự cố gốc (Major Incident), cho phép cập nhật trạng thái và phản hồi hàng loạt.
6. **Xử lý Trùng lặp & Gộp Vé (Ticket Deduplication & Merge):** Hợp nhất các yêu cầu trùng lặp của cùng một khách hàng về một vé duy nhất để tối ưu hóa nguồn lực hỗ trợ.
7. **Liên kết Thương mại & Cơ hội Bán hàng (Deal-Ticket Linkage):** Kết nối vé hỗ trợ với Cơ hội bán hàng để đội ngũ kinh doanh nắm rõ tình trạng kỹ thuật trước khi đàm phán hợp đồng.
8. **Báo cáo Hiệu suất Dịch vụ (Customer Service Analytics):** Đo lường khối lượng vé tiếp nhận, Thời gian phản hồi đầu tiên (FRT), Tỷ lệ giải quyết lần đầu (FCR) và Tỷ lệ tuân thủ SLA.

### 1.2 Phạm vi

Tài liệu bao gồm 10 nhóm chức năng cốt lõi:
- **Nhóm A: Quản trị Vé Hỗ trợ (Tickets CRUD):** Tạo mới, cập nhật, chi tiết 360 độ, phân loại Mức độ ưu tiên, Loại vé, Nguồn tiếp nhận và Thùng rác phục hồi.
- **Nhóm B: Quy trình Trạng thái & Mã Phân loại Giải pháp:** Vòng đời vé 5 bước, bắt buộc nhập Mã Giải pháp (Resolution Code) khi giải quyết, cơ chế Mở lại vé (Reopen).
- **Nhóm C: Cam kết Chất lượng Dịch vụ (SLA Management & Tracking):** Đồng hồ đếm ngược phản hồi đầu tiên & thời gian giải quyết, Cảnh báo vi phạm SLA, Tự động Tạm dừng / Tiếp tục đếm giờ SLA (Pause & Resume).
- **Nhóm D: Trao đổi & Tương tác Đa luồng (Messaging & Collaboration):** Phản hồi công khai tới khách hàng, Ghi chú nội bộ bảo mật, Đính kèm tệp và Nhắc tên đồng nghiệp (@Mention).
- **Nhóm E: Khảo sát Mức độ Hài lòng Khách hàng (CSAT Surveys):** Tự động gửi khảo sát 1-5 sao, thu thập nhận xét và tính điểm CSAT trung bình.
- **Nhóm F: Xử lý Trùng lặp & Gộp Vé Hỗ trợ (Ticket Merge):** Gộp nhiều vé trùng lặp thành Master Ticket, chuyển giao toàn bộ tin nhắn trao đổi và đóng vé phụ.
- **Nhóm G: Cấu trúc Sự cố Lớn Vé Cha - Vé Con (Parent-Child Hierarchy):** Liên kết Vé Cha và Vé Con, cập nhật trạng thái và gửi phản hồi hàng loạt tới tất cả các vé con.
- **Nhóm H: Liên kết Thương mại & Cơ hội Bán hàng (Deal & Contact Linkage):** Gắn vé hỗ trợ với Cơ hội bán hàng (`dealId`) và Doanh nghiệp (`accountId`).
- **Nhóm I: Thao tác Hàng loạt & Nhập/Xuất Dữ liệu (Bulk Operations, Import/Export):** Gán nhân viên hàng loạt, đổi trạng thái hàng loạt, Import Excel 50MB và Export CSV bảo mật qua token.
- **Nhóm J: Báo cáo Hiệu suất Dịch vụ & SLA (Support Performance & Reports):** Báo cáo Khối lượng vé (Ticket Volume), Thời gian phản hồi đầu tiên (FRT), Tỷ lệ tuân thủ SLA và Điểm CSAT.

**Ngoài phạm vi (thuộc về các tài liệu SRS chuyên biệt khác):**
- **Nghiệp vụ Quản trị Kênh trò chuyện Đa kênh Thời gian thực:** Thuộc về [`omnichat-srs.md`](./omnichat-srs.md).
- **Nghiệp vụ Quản trị Cơ hội Bán hàng & Phễu Bán hàng:** Thuộc về [`deals-pipeline-srs.md`](./deals-pipeline-srs.md).
- **Nghiệp vụ Quản trị Hồ sơ Khách hàng & Danh bạ:** Thuộc về [`contacts-srs.md`](./contacts-srs.md).

### 1.3 Đối tượng đọc

- **Product Owner / Business Analyst:** Chuẩn mực đặc tả nghiệp vụ Helpdesk / Customer Service để thiết kế tính năng và nghiệm thu sản phẩm.
- **Đội ngũ Phát triển (Frontend / Backend):** Căn cứ thiết kế API, schemas, động cơ tính toán SLA theo thời gian thực và trải nghiệm giao diện người dùng.
- **Đội ngũ Kiểm thử (QA/QC):** Thiết kế bộ kịch bản kiểm thử tích hợp chuyên sâu cho toàn bộ luồng xử lý vé và đồng hồ SLA.
- **Trưởng bộ phận Dịch vụ Khách hàng (Head of Customer Service / Support Lead):** Nắm rõ các quy tắc vận hành vé, chính sách cam kết SLA và cơ chế đo lường KPI nhân viên hỗ trợ.

### 1.4 Thuật ngữ & Viết tắt

| Thuật ngữ | Định nghĩa nghiệp vụ |
| --- | --- |
| **Vé Hỗ trợ (Ticket / Support Case)** | Thực thể đại diện cho một yêu cầu trợ giúp hoặc khiếu nại của khách hàng, có mã định danh duy nhất (ví dụ: `TK-10023`). |
| **Cam kết Chất lượng Dịch vụ (SLA)** | Thỏa thuận về thời gian phản hồi đầu tiên và thời gian giải quyết tối đa theo mức độ ưu tiên. |
| **Vi phạm SLA (SLA Breach)** | Trạng thái cảnh báo khi vé vượt quá thời hạn cam kết mà chưa được phản hồi hoặc giải quyết. |
| **Tạm dừng / Tiếp tục SLA (SLA Pause & Resume)** | Cơ chế đóng băng đồng hồ đếm ngược SLA khi vé ở trạng thái chờ khách hàng phản hồi. |
| **Khảo sát Hài lòng (CSAT)** | Đánh giá chất lượng dịch vụ (thang điểm 1-5 sao) do khách hàng chấm điểm sau khi hoàn tất vé. |
| **Mã Giải pháp (Resolution Code)** | Phân loại nguyên nhân và cách khắc phục bắt buộc khai báo khi giải quyết vé. |
| **Vé Cha - Vé Con (Parent-Child Tickets)** | Mô hình liên kết sự cố diện rộng với nhiều yêu cầu khiếu nại riêng lẻ để xử lý hàng loạt. |
| **Ghi chú Nội bộ (Internal Note)** | Thông điệp trao đổi riêng giữa các nhân viên trong công ty, khách hàng không nhìn thấy. |
| **Phản hồi Công khai (Public Reply)** | Thông điệp trả lời chính thức gửi tới khách hàng qua email hoặc kênh chat. |

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

Trong hoạt động chăm sóc khách hàng và hỗ trợ kỹ thuật, các doanh nghiệp thường gặp các vấn đề lớn:
- Yêu cầu của khách hàng bị bỏ sót, phản hồi chậm trễ do phân tán qua nhiều kênh (Email, Chat, Điện thoại) không có mã theo dõi tập trung.
- Không có cam kết chất lượng dịch vụ (SLA) rõ ràng, dẫn đến các khách hàng VIP hoặc các sự cố khẩn cấp bị đối xử ngang hàng với các câu hỏi thông thường.
- Khi xảy ra sự cố diện rộng (ví dụ hệ thống bảo trì), nhân viên hỗ trợ phải trả lời thủ công hàng trăm email giống nhau thay vì có cơ chế quản lý sự cố Cha - Con để phản hồi hàng loạt.
- Thiếu công cụ đo lường mức độ hài lòng khách hàng (CSAT) sau mỗi lần hỗ trợ để đánh giá năng lực nhân viên.
- Thiếu phân loại nguyên nhân xử lý (Resolution Code) để báo cáo cho đội ngũ kỹ thuật cải tiến sản phẩm.

Module Tickets & Customer Service giải quyết toàn diện các vấn đề trên bằng cách xây dựng một trung tâm tiếp nhận yêu cầu tập trung, hệ thống đếm ngược SLA tự động thông minh, cơ chế khảo sát CSAT tự động, quy trình phân cấp vé Cha - Con và báo cáo hiệu suất chuyên sâu.

### 2.2 Vai trò người dùng (Actor)

| Actor | Mô tả vai trò và quyền hạn |
| --- | --- |
| **Nhân viên Hỗ trợ Khách hàng (Support Agent)** | Tiếp nhận vé, gửi phản hồi công khai tới khách hàng, ghi chú nội bộ, tạm dừng/tiếp tục SLA và giải quyết vé. |
| **Trưởng nhóm Hỗ trợ (Support Lead / Manager)** | Phân công vé cho nhân viên, cấu hình chính sách SLA, xử lý các vé vi phạm SLA leo thang, xem báo cáo hiệu suất và điểm CSAT. |
| **Nhân viên Kinh doanh (Sales Representative)** | Tra cứu các vé hỗ trợ liên kết với Cơ hội bán hàng (`dealId`) hoặc Khách hàng mình đang phụ trách để nắm bắt tình hình. |
| **Quản trị viên Không gian làm việc (Tenant Admin)** | Cấu hình danh mục trạng thái vé, nguồn tiếp nhận, loại vé, mã giải pháp và chính sách cam kết chất lượng SLA. |
| **Chủ sở hữu Không gian làm việc (Tenant Owner)** | Toàn quyền xem và cấu hình toàn bộ phân hệ vé hỗ trợ và báo cáo chất lượng dịch vụ. |
| **Khách hàng (Customer / End-User)** | Gửi yêu cầu trợ giúp, nhận email phản hồi từ nhân viên và thực hiện đánh giá khảo sát hài lòng CSAT (1-5 sao). |
| **Tiến trình Hệ thống (System Engine / Background Workers)** | Tự động tính toán hạn chót SLA, quét phát hiện vi phạm SLA, gửi email khảo sát CSAT và xử lý tệp import/export. |

### 2.3 Bảng tổng hợp 30 tính năng nghiệp vụ

| Nhóm | Mã FEAT | Tên tính năng nghiệp vụ | Trạng thái |
| --- | --- | --- | --- |
| **A. Quản trị Vé Hỗ trợ** | `FEAT-01` | Tạo mới & Quản lý Vé Hỗ trợ Đa kênh (Ticket CRUD) | `[Đã triển khai]` |
| | `FEAT-02` | Mã Định danh Vé Duy nhất Toàn hệ thống (Ticket Number Auto-Gen) | `[Đã triển khai]` |
| | `FEAT-03` | Hồ sơ Chi tiết Vé Hỗ trợ 360 độ (Ticket 360 View) | `[Đã triển khai]` |
| | `FEAT-04` | Phân loại Mức độ Ưu tiên, Loại vé & Nguồn tiếp nhận (Priority, Type, Source) | `[Đã triển khai]` |
| | `FEAT-05` | Thùng rác Vé Hỗ trợ & Phục hồi Bản ghi (Ticket Recycle Bin & Restore) | `[Đã triển khai]` |
| **B. Quy trình Trạng thái & Giải pháp** | `FEAT-06` | Quản trị Vòng đời Trạng thái Vé Hỗ trợ (5 Ticket Statuses) | `[Đã triển khai]` |
| | `FEAT-07` | Bắt buộc Khai báo Mã Giải pháp khi Giải quyết Vé (Resolution Codes) | `[Đã triển khai]` |
| | `FEAT-08` | Quy trình Mở lại Vé Hỗ trợ (Ticket Reopen Policy) | `[Đã triển khai]` |
| | `FEAT-09` | Tự động Đóng Vé sau Thời gian Ân hạn (Auto-Close Resolved Tickets) | `[Yêu cầu mới]` |
| **C. Cam kết Chất lượng Dịch vụ (SLA)** | `FEAT-10` | Cấu hình Chính sách Cam kết SLA theo Mức độ Ưu tiên (SLA Policies Config) | `[Đã triển khai]` |
| | `FEAT-11` | Đồng hồ Đếm ngược Thời gian Phản hồi Đầu tiên (First Response Time SLA) | `[Đã triển khai]` |
| | `FEAT-12` | Đồng hồ Đếm ngược Thời gian Giải quyết Hoàn tất (Resolution Time SLA) | `[Đã triển khai]` |
| | `FEAT-13` | Tự động Nhận diện & Cảnh báo Vi phạm SLA (SLA Breach Alert) | `[Đã triển khai]` |
| | `FEAT-14` | Tự động Tạm dừng & Tiếp tục Đếm giờ SLA khi Chờ Khách hàng (SLA Pause/Resume) | `[Đã triển khai]` |
| **D. Trao đổi & Tương tác Đa luồng** | `FEAT-15` | Phản hồi Công khai tới Khách hàng qua Email / Kênh chat (Public Reply) | `[Đã triển khai]` |
| | `FEAT-16` | Ghi chú Trao đổi Nội bộ Bảo mật (Internal Private Notes) | `[Đã triển khai]` |
| | `FEAT-17` | Đính kèm Tệp Đa phương tiện & Quản lý Tài liệu (Ticket Attachments) | `[Đã triển khai]` |
| | `FEAT-18` | Nhắc tên Đồng nghiệp & Thông báo Cộng tác (@Mention Collaboration) | `[Yêu cầu mới]` |
| **E. Khảo sát Mức độ Hài lòng (CSAT)** | `FEAT-19` | Tự động Kích hoạt Gửi Khảo sát Đánh giá CSAT 1-5 Sao (CSAT Trigger) | `[Đã triển khai]` |
| | `FEAT-20` | Thu thập Điểm số & Nhận xét Đánh giá Hài lòng (CSAT Feedback Collection)| `[Đã triển khai]` |
| **F. Xử lý Trùng lặp & Gộp Vé** | `FEAT-21` | Gộp Nhiều Vé Hỗ trợ Trùng lặp An toàn (Merge Tickets) | `[Đã triển khai]` |
| | `FEAT-22` | Chuyển giao Lịch sử Trao đổi & Đóng Vé Phụ khi Gộp (Merge Re-parenting) | `[Đã triển khai]` |
| **G. Quản lý Sự cố Lớn Vé Cha - Con** | `FEAT-23` | Thiết lập Cấu trúc Phân cấp Vé Cha - Vé Con (Parent-Child Tickets) | `[Đã triển khai]` |
| | `FEAT-24` | Đồng bộ Trạng thái & Gửi Phản hồi Hàng loạt cho Vé Con (Bulk Sync to Children)| `[Đã triển khai]` |
| **H. Liên kết Cơ hội Bán hàng** | `FEAT-25` | Liên kết Vé Hỗ trợ với Cơ hội Bán hàng (Link Ticket to Deal) | `[Đã triển khai]` |
| | `FEAT-26` | Tra cứu Danh sách Vé Hỗ trợ theo Cơ hội Bán hàng (Find Tickets by Deal) | `[Đã triển khai]` |
| **I. Thao tác Hàng loạt & Nhập/Xuất** | `FEAT-27` | Thao tác Gán Người phụ trách, Gắn thẻ & Đổi Trạng thái Hàng loạt (Bulk Ops) | `[Đã triển khai]` |
| | `FEAT-28` | Nhập / Xuất Danh sách Vé Dung lượng lớn 50MB qua Hàng đợi (Import/Export)| `[Đã triển khai]` |
| **J. Báo cáo Hiệu suất Dịch vụ & SLA** | `FEAT-29` | Báo cáo Khối lượng Tiếp nhận & Tỷ lệ Giải quyết (Ticket Volume & Resolution) | `[Đã triển khai]` |
| | `FEAT-30` | Báo cáo Tỷ lệ Tuân thủ SLA & Điểm CSAT theo Đội ngũ (SLA Compliance & CSAT)| `[Đã triển khai]` |
| **K. Điều phối & Kiểm soát SLA** | `FEAT-31` | Quy tắc Phân bổ Vé Tự động (Ticket Auto-Assignment Rules) | `[Yêu cầu mới]` |
| | `FEAT-32` | Trạng thái Chờ Bên Thứ Ba & Pause SLA (Pending 3rd Party Status) | `[Yêu cầu mới]` |
| | `FEAT-33` | Leo thang SLA Tự động (SLA Breach Escalation Matrix) | `[Yêu cầu mới]` |

---

## 3. Đặc tả yêu cầu chức năng

## A. QUẢN TRỊ VÉ HỖ TRỢ (TICKETS MANAGEMENT)

### FEAT-01 — Tạo mới & Quản lý Vé Hỗ trợ Đa kênh (Ticket CRUD) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép tiếp nhận và tạo mới vé hỗ trợ từ nhiều nguồn (Khách gửi email, Chatbot chuyển tiếp, Nhân viên tạo thủ công khi nhận điện thoại).

**Actor:** Nhân viên Hỗ trợ, Khách hàng, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-01.1 (Thông tin bắt buộc)`: Tiêu đề yêu cầu (`title`), Nội dung chi tiết (`description`), Mức độ ưu tiên (`priority`), Khách hàng liên hệ (`contactId` hoặc Email).
- `BR-01.2 (Tự động gán quyền sở hữu)`: Nếu tạo thủ công bởi nhân viên, nhân viên đó tự động được gán làm Người phụ trách (`ownerId`); nếu tạo tự động qua kênh chat/email, vé được đưa vào Hàng đợi tiếp nhận chung (Unassigned Pool) của phòng ban hỗ trợ.

---

### FEAT-02 — Mã Định danh Vé Duy nhất Toàn hệ thống (Ticket Number Auto-Gen) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Mỗi vé hỗ trợ khi được tạo ra sẽ tự động được hệ thống cấp phát một mã số thứ tự duy nhất có tiền tố chuẩn (ví dụ: `TK-10001`, `TK-10002`).

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-02.1`: Mã số vé tự động tăng dần đều (Auto-incrementing sequence), không trùng lặp trong toàn bộ không gian làm việc.
- `BR-02.2`: Cho phép người dùng tìm kiếm vé tức thì bằng cách gõ trực tiếp mã số vé (ví dụ: gõ `TK-10023` hoặc `10023`).

---

### FEAT-03 — Hồ sơ Chi tiết Vé Hỗ trợ 360 độ (Ticket 360 View) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Màn hình làm việc trung tâm của tư vấn viên: Thanh trạng thái quy trình, Đồng hồ đếm ngược SLA trên đầu trang, Khung hội thoại trao đổi ở giữa và Panel thông tin khách hàng/công ty/cơ hội bán hàng bên phải.

**Actor:** Mọi người dùng có quyền xem Ticket.

**Quy tắc nghiệp vụ:**
- `BR-03.1`: Hiển thị rõ ràng đồng hồ đếm ngược SLA (màu xanh nếu còn nhiều thời gian, màu cam nếu sắp hết hạn dưới 1 giờ, màu đỏ nhấp nháy nếu đã vi phạm SLA).

---

### FEAT-04 — Phân loại Mức độ Ưu tiên, Loại vé & Nguồn tiếp nhận `[Đã triển khai]`

**Mô tả nghiệp vụ:** Phân loại vé hỗ trợ theo 4 trục chuẩn hóa để phục vụ điều phối và áp dụng chính sách cam kết chất lượng.

**Actor:** Nhân viên Hỗ trợ, Quản lý Hỗ trợ.

**Chi tiết các danh mục phân loại:**
- **Mức độ Ưu tiên (`priority`):** `LOW` (Thấp), `MEDIUM` (Trung bình), `HIGH` (Cao), `URGENT` (Khẩn cấp).
- **Loại vé (`typeId`):** Câu hỏi thường gặp (Question), Sự cố kỹ thuật (Incident), Lỗi phần mềm (Problem), Yêu cầu tính năng (Feature Request), Nhiệm vụ dịch vụ (Task).
- **Nguồn tiếp nhận (`sourceId`):** Email, Livechat Widget, WhatsApp, Hotline, Biểu mẫu Web Form, Mạng xã hội.

---

### FEAT-05 — Thùng rác Vé Hỗ trợ & Phục hồi Bản ghi (Ticket Recycle Bin & Restore) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi xóa vé hỗ trợ, hệ thống thực hiện Xóa mềm (Soft Delete) và lưu trong Thùng rác 30 ngày trước khi xóa vĩnh viễn.

**Actor:** Quản trị viên Workspace, Người có quyền `delete` trên Tickets.

**Quy tắc nghiệp vụ:**
- `BR-05.1`: Ẩn vé khỏi danh sách xử lý hằng ngày và báo cáo SLA. Cho phép phục hồi lại nguyên vẹn bản ghi qua API `POST /api/v1/tickets/:id/restore`.

---

## B. QUY TRÌNH TRẠNG THÁI & MÃ GIẢI PHÁP (STATUSES & RESOLUTION CODES)

### FEAT-06 — Quản trị Vòng đời Trạng thái Vé Hỗ trợ (5 Ticket Statuses) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Quản lý quy trình xử lý vé qua 5 trạng thái chuẩn hóa:

**Chi tiết 5 trạng thái vòng đời:**
1. **Mới tiếp nhận (New / Open):** Vé vừa được tạo, đang chờ nhân viên phản hồi đầu tiên.
2. **Đang xử lý (In Progress):** Nhân viên đang trực tiếp kiểm tra và khắc phục sự cố.
3. **Đang chờ khách hàng (Pending Customer):** Đã gửi phản hồi và đang chờ khách hàng cung cấp thêm thông tin (Đồng hồ SLA tự động tạm dừng).
4. **Đã giải quyết (Resolved):** Nhân viên đã khắc phục xong sự cố và gửi giải pháp tới khách hàng.
5. **Đã đóng (Closed):** Vé hoàn tất hoàn toàn, bị khóa không cho chỉnh sửa thêm.

---

### FEAT-07 — Bắt buộc Khai báo Mã Giải pháp khi Giải quyết Vé (Resolution Codes) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi nhân viên chuyển trạng thái vé sang "Đã giải quyết" (`Resolved`), hệ thống bắt buộc nhân viên phải chọn một Mã Giải pháp chuẩn hóa từ danh mục.

**Actor:** Nhân viên Hỗ trợ.

**Quy tắc nghiệp vụ:**
- `BR-07.1`: Hộp thoại yêu cầu chọn 1 mã giải pháp: *Đã hướng dẫn sử dụng, Đã sửa lỗi phần mềm, Lỗi cấu hình mạng của khách hàng, Lỗi do bên thứ ba, Hoàn tiền / Đổi trả, Không thể tái hiện lỗi*.
- `BR-07.2`: Lưu vết `resolutionCodeId` và ghi nhận thời điểm giải quyết `resolvedAt = now()`.

---

### FEAT-08 — Quy trình Mở lại Vé Hỗ trợ (Ticket Reopen Policy) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Nếu khách hàng phản hồi lại sau khi vé đã được đánh dấu Đã giải quyết (Resolved), hệ thống tự động chuyển trạng thái vé quay trở lại "Đang xử lý" (In Progress).

**Actor:** Khách hàng, Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-08.1`: Khi nhận được tin nhắn/email mới từ khách hàng trên một vé `Resolved`, hệ thống tự động mở lại vé và gửi thông báo tới nhân viên phụ trách.
- `BR-08.2 (Tính toán SLA khi Mở lại Vé) [Yêu cầu mới]`: Khi vé bị mở lại (`Reopened`), mốc `resolvedAt` cũ bị hủy bỏ; đồng hồ Resolution SLA tiếp tục đếm cộng dồn thời gian xử lý thực tế từ mốc Reopen (loại trừ khoảng thời gian vé đã nằm ở trạng thái `Resolved`). Vé chỉ bị tính là Vi phạm SLA nếu tổng thời gian xử lý thực tế vượt quá hạn ngạch cam kết ban đầu.

---

### FEAT-09 — Tự động Đóng Vé sau Thời gian Ân hạn (Auto-Close Resolved Tickets) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Nếu vé ở trạng thái "Đã giải quyết" (Resolved) quá **72 giờ** mà khách hàng không có phản hồi thêm hoặc không có khiếu nại, hệ thống tự động chuyển trạng thái vé sang "Đã đóng" (`Closed`).

**Actor:** Tiến trình Hệ thống (Daily Cron).

**Quy tắc nghiệp vụ:**
- `BR-09.1`: Thời gian ân hạn 72 giờ được tính theo 72 Giờ Lịch thực tế (Calendar Hours) kể từ mốc `resolvedAt`. Sau 72 giờ, vé bị khóa vĩnh viễn (`Closed`), không cho phép chỉnh sửa nội dung; mọi email mới từ khách hàng sau khi vé đã Đóng sẽ tự động tạo thành một Vé Hỗ trợ Mới kèm liên kết tham chiếu tới vé cũ.

---

## C. CAM KẾT CHẤT LƯỢNG DỊCH VỤ (SLA MANAGEMENT & TRACKING)

### FEAT-10 — Cấu hình Chính sách Cam kết SLA theo Mức độ Ưu tiên `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép doanh nghiệp thiết lập bảng quy chuẩn thời gian phản hồi đầu tiên và thời gian giải quyết tối đa cho từng mức độ ưu tiên.

**Actor:** Quản trị viên Workspace.

**Bảng Ma trận SLA Mặc định:**
| Mức độ Ưu tiên | Thời hạn Phản hồi Đầu tiên (First Response Due) | Thời hạn Giải quyết Xong (Resolution Due) |
|---|---|---|
| **Khẩn cấp (`URGENT`)** | **15 phút** | **2 giờ** |
| **Cao (`HIGH`)** | **1 giờ** | **8 giờ** |
| **Trung bình (`MEDIUM`)** | **4 giờ** | **24 giờ** |
| **Thấp (`LOW`)** | **8 giờ** | **48 giờ** |

**Quy tắc nghiệp vụ:**
- `BR-10.1`: Cho phép cấu hình thời hạn First Response và Resolution Time theo 4 mức độ ưu tiên.
- `BR-10.2 (Lịch làm việc & Giờ hành chính trong SLA) [Yêu cầu mới]`: Hỗ trợ 2 chế độ tính toán thời gian cam kết SLA:
  - **Hỗ trợ Liên tục 24/7:** Áp dụng cho mức `URGENT` hoặc khách hàng VIP Enterprise (đồng hồ đếm liên tục mọi ngày trong tuần, kể cả ngày lễ).
  - **Giờ Hành chính 8x5 (Business Hours Calendar):** Áp dụng cho các mức `HIGH`, `MEDIUM`, `LOW` (chỉ đếm giờ trong khung 08:00 - 17:30 Thứ 2 - Thứ 6, tự động đóng băng đếm giờ vào ban đêm, cuối tuần và các ngày lễ quốc gia).

---

### FEAT-11 — Đồng hồ Đếm ngược Thời gian Phản hồi Đầu tiên (First Response SLA) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hệ thống tự động tính toán mốc `firstResponseDueAt = createdAt + SLA_First_Response`. Khi nhân viên gửi tin nhắn phản hồi công khai đầu tiên, hệ thống ghi nhận `firstResponseAt = now()` và dừng đồng hồ phản hồi.

**Actor:** Tiến trình Hệ thống, Nhân viên Hỗ trợ.

**Quy tắc nghiệp vụ:**
- `BR-11.1`: Ghi chú nội bộ không được tính là phản hồi đầu tiên; chỉ có tin nhắn gửi tới khách hàng (Public Reply) mới được công nhận hoàn tất cam kết phản hồi đầu tiên.
- `BR-11.2 (Loại trừ Thông báo Tự động) [Yêu cầu mới]`: Các email xác nhận tiếp nhận tự động của hệ thống (Auto-Responder / Acknowledgment Email) **tuyệt đối không** được tính là Phản hồi đầu tiên; chỉ thông điệp do nhân viên hỗ trợ (con người thật) soạn thảo và gửi đi mới được chốt mốc `firstResponseAt`.

---

### FEAT-12 — Đồng hồ Đếm ngược Thời gian Giải quyết Hoàn tất (Resolution SLA) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hệ thống tự động tính toán mốc `resolutionDueAt = createdAt + SLA_Resolution`. Đồng hồ đếm ngược liên tục cho đến khi vé chuyển sang trạng thái `Resolved`.

**Actor:** Tiến trình Hệ thống.

---

### FEAT-13 — Tự động Nhận diện & Cảnh báo Vi phạm SLA (SLA Breach Alert) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Nếu thời điểm hiện tại vượt quá `firstResponseDueAt` mà chưa phản hồi, hoặc vượt quá `resolutionDueAt` mà chưa giải quyết xong, hệ thống tự động gắn cờ `slaBreached = true`.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-13.1`: Vé vi phạm SLA được đánh dấu nhãn đỏ nổi bật trên danh sách và tự động phát thông báo cảnh báo leo thang (Escalation Alert) tới Trưởng nhóm hỗ trợ.

---

### FEAT-14 — Tự động Tạm dừng & Tiếp tục Đếm giờ SLA khi Chờ Khách hàng (SLA Pause/Resume) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi nhân viên chuyển vé sang trạng thái "Đang chờ khách hàng" (`Pending Customer`), đồng hồ đếm ngược SLA tự động đóng băng (`POST /api/v1/tickets/:id/sla/pause`). Khi khách hàng trả lời hoặc nhân viên tiếp tục xử lý, đồng hồ được kích hoạt lại (`POST /api/v1/tickets/:id/sla/resume`) và mốc hạn chót SLA được tự động cộng thêm khoảng thời gian đã tạm dừng (`slaPauseDurationMs`).

**Actor:** Nhân viên Hỗ trợ, Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-14.1`: Đảm bảo đánh giá đúng năng lực nhân viên, không tính thời gian chờ khách hàng vào chỉ số xử lý của nhân viên hỗ trợ.

---

## D. TRAO ĐỔI & TƯƠNG TÁC ĐA LUỒNG (MESSAGING & COLLABORATION)

### FEAT-15 — Phản hồi Công khai tới Khách hàng qua Email / Kênh chat (Public Reply) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép nhân viên soạn và gửi câu trả lời chính thức tới khách hàng. Thông điệp được tự động gửi qua email hoặc kênh chat mà khách hàng đã dùng để mở vé.

**Actor:** Nhân viên Hỗ trợ.

**Quy tắc nghiệp vụ:**
- `BR-15.1`: Tin nhắn được lưu vào `ticket_messages` với cờ `isInternal = false`, tự động gửi thông báo email tới khách hàng và cập nhật dòng thời gian.

---

### FEAT-16 — Ghi chú Trao đổi Nội bộ Bảo mật (Internal Private Notes) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép các nhân viên hỗ trợ, kỹ thuật viên và quản lý trao đổi ý kiến riêng tư ngay trên vé. Khách hàng tuyệt đối không nhìn thấy các ghi chú này.

**Actor:** Nhân viên Hỗ trợ, Kỹ thuật viên.

**Quy tắc nghiệp vụ:**
- `BR-16.1`: Tin nhắn được gắn cờ `isInternal = true`, hiển thị với khung màu vàng nhạt đặc trưng trên giao diện để phân biệt rõ ràng với tin nhắn công khai.

---

### FEAT-17 — Đính kèm Tệp Đa phương tiện & Quản lý Tài liệu `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hỗ trợ đính kèm ảnh chụp màn hình sự cố (Screenshots), tệp nhật ký lỗi (Log files) hoặc tài liệu hướng dẫn (PDF) với dung lượng tối đa 25MB mỗi tệp.

**Actor:** Nhân viên Hỗ trợ, Khách hàng.

---

### FEAT-18 — Nhắc tên Đồng nghiệp & Thông báo Cộng tác (@Mention) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Trong ghi chú nội bộ, nhân viên có thể gõ `@TenDongNghiep` để yêu cầu hỗ trợ chuyên môn. Hệ thống tự động gửi thông báo trực tiếp tới đồng nghiệp được nhắc tên.

**Actor:** Nhân viên Hỗ trợ.

---

## E. KHẢO SÁT MỨC ĐỘ HÀI LÒNG KHÁCH HÀNG (CSAT SURVEYS)

### FEAT-19 — Tự động Kích hoạt Gửi Khảo sát Đánh giá CSAT 1-5 Sao `[Đã triển khai]`

**Mô tả nghiệp vụ:** Ngay khi vé chuyển sang trạng thái `Resolved`, hệ thống tự động gửi một email/tin nhắn ngắn mời khách hàng đánh giá chất lượng phục vụ theo thang điểm 1 đến 5 sao.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-19.1`: Email mang tiêu đề: *"Bạn đánh giá thế nào về chất lượng hỗ trợ của [Tên nhân viên] cho yêu cầu [Mã vé]?"*.
- `BR-19.2`: Khách hàng có thể nhấp trực tiếp vào 1 trong 5 biểu tượng sao ngay trong email để chấm điểm mà không cần đăng nhập.

---

### FEAT-20 — Thu thập Điểm số & Nhận xét Đánh giá Hài lòng `[Đã triển khai]`

**Mô tả nghiệp vụ:** Ghi nhận điểm số (`rating`: 1 - 5 sao), ý kiến đóng góp (`feedback`) và thời điểm đánh giá (`ratedAt = now()`) vào hồ sơ vé qua API `POST /api/v1/tickets/:id/csat`.

**Actor:** Khách hàng.

**Quy tắc nghiệp vụ:**
- `BR-20.1`: Điểm số CSAT được tính toán tự động vào bảng xếp hạng chất lượng phục vụ của từng nhân viên và toàn bộ phòng ban.

---

## F. XỬ LÝ TRÙNG LẶP & GỘP VÉ HỖ TRỢ (TICKET MERGE)

### FEAT-21 — Gộp Nhiều Vé Hỗ trợ Trùng lặp An toàn (Merge Tickets) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi khách hàng gửi nhiều email/tin nhắn cho cùng một vấn đề, nhân viên hỗ trợ thực hiện Gộp các vé phụ vào Vé Chính (Master Ticket) qua API `POST /api/v1/tickets/:id/merge`.

**Actor:** Nhân viên Hỗ trợ có quyền `delete` trên Tickets.

**Quy tắc nghiệp vụ:**
- `BR-21.1 (Yêu cầu quyền hạn)`: Thao tác gộp bắt buộc yêu cầu quyền `delete` vì vé phụ sẽ bị đóng và xóa mềm sau khi gộp.
- `BR-21.2`: Vé phụ được tự động chuyển sang trạng thái `Closed` kèm ghi chú: "Vé đã được gộp vào vé [Mã Vé Chính]".

---

### FEAT-22 — Chuyển giao Lịch sử Trao đổi & Đóng Vé Phụ khi Gộp `[Đã triển khai]`

**Mô tả nghiệp vụ:** Toàn bộ tin nhắn trao đổi, hình ảnh đính kèm và ghi chú nội bộ của vé phụ được tự động chuyển giao và hiển thị liền mạch trên dòng thời gian của Vé Chính.

**Actor:** Tiến trình Hệ thống.

---

## G. QUẢN LÝ SỰ CỐ LỚN VÉ CHA - CON (PARENT-CHILD TICKETS)

### FEAT-23 — Thiết lập Cấu trúc Phân cấp Vé Cha - Vé Con `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép liên kết một Vé Cha (Major Incident — ví dụ: "Máy chủ Cổng thanh toán bảo trì đột xuất") với nhiều Vé Con (Sub-tickets) của từng khách hàng khiếu nại qua API `PATCH /api/v1/tickets/:id/set-parent`.

**Actor:** Nhân viên Hỗ trợ, Quản lý Hỗ trợ.

**Quy tắc nghiệp vụ:**
- `BR-23.1`: Cung cấp API tra cứu danh sách vé con (`GET /api/v1/tickets/:id/children`).

---

### FEAT-24 — Đồng bộ Trạng thái & Gửi Phản hồi Hàng loạt cho Vé Con `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi sự cố trên Vé Cha được giải quyết, nhân viên chỉ cần giải quyết Vé Cha và chọn "Đồng bộ phản hồi cho tất cả vé con". Hệ thống tự động gửi tin nhắn giải pháp và chuyển trạng thái `Resolved` cho toàn bộ các vé con trực thuộc.

**Actor:** Nhân viên Hỗ trợ, Quản lý Hỗ trợ.

**Quy tắc nghiệp vụ:**
- `BR-24.1`: Giúp giải phóng 90% thời gian xử lý thủ công khi xảy ra các sự cố kỹ thuật diện rộng.

---

## H. LIÊN KẾT THƯƠNG MẠI & CƠ HỘI BÁN HÀNG

### FEAT-25 — Liên kết Vé Hỗ trợ với Cơ hội Bán hàng (Link Ticket to Deal) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép gắn vé hỗ trợ với một Cơ hội bán hàng cụ thể (`dealId`) qua API `PATCH /api/v1/tickets/:id/link-deal`.

**Actor:** Nhân viên Hỗ trợ, Nhân viên Kinh doanh.

**Quy tắc nghiệp vụ:**
- `BR-25.1`: Giúp đội ngũ kinh doanh nắm bắt ngay các rào cản kỹ thuật mà khách hàng đang gặp phải trong giai đoạn đàm phán hợp đồng.
- `BR-25.2 (Tính Độc lập của Vé Hỗ trợ khi Deal Đóng) [Yêu cầu mới]`: Khi một Cơ hội bán hàng chuyển sang trạng thái kết thúc (`Closed Won` hoặc `Closed Lost`), các Vé hỗ trợ kỹ thuật liên kết vẫn duy trì vòng đời xử lý độc lập và không bị tự động đóng hay hủy bỏ.

---

### FEAT-26 — Tra cứu Danh sách Vé Hỗ trợ theo Cơ hội Bán hàng `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp API chuyên biệt (`GET /api/v1/tickets/by-deal/:dealId`) hiển thị toàn bộ các vé hỗ trợ đang mở hoặc đã giải quyết của thương vụ bán hàng đó.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh.

---

## I. THAO TÁC HÀNG LOẠT & NHẬP/XUẤT DỮ LIỆU (BULK & IMPORT/EXPORT)

### FEAT-27 — Thao tác Gán Người phụ trách, Gắn thẻ & Đổi Trạng thái Hàng loạt `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép chọn nhiều vé cùng lúc để chuyển giao cho nhân viên khác, gắn thẻ phân loại hoặc đổi trạng thái hàng loạt qua API `/api/v1/tickets/bulk-tag`.

**Actor:** Quản lý Hỗ trợ, Quản trị viên Workspace.

---

### FEAT-28 — Nhập / Xuất Danh sách Vé Dung lượng lớn 50MB qua Hàng đợi `[Đã triển khai]`

**Mô tả nghiệp vụ:** Nhập danh sách vé từ Excel/CSV hoặc xuất báo cáo vé ra tệp CSV bảo mật qua token an toàn 24 giờ.

**Actor:** Quản trị viên Workspace, Người có quyền `import`/`export` trên Tickets.

---

## J. BÁO CÁO HIỆU SUẤT DỊCH VỤ & SLA (SUPPORT ANALYTICS)

### FEAT-29 — Báo cáo Khối lượng Tiếp nhận & Tỷ lệ Giải quyết (Ticket Volume) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Biểu đồ thống kê số lượng vé tạo mới, số lượng vé đã giải quyết, số lượng vé tồn đọng theo Ngày, Tuần, Tháng và theo từng Kênh tiếp nhận.

**Actor:** Quản lý Hỗ trợ, Giám đốc Dịch vụ.

---

### FEAT-30 — Báo cáo Tỷ lệ Tuân thủ SLA & Điểm CSAT theo Đội ngũ `[Đã triển khai]`

**Mô tả nghiệp vụ:** Báo cáo đo lường chất lượng chuyên sâu: Tỷ lệ tuân thủ SLA (% đạt cam kết), Thời gian phản hồi đầu tiên trung bình (phút), Thời gian giải quyết trung bình (giờ) và Điểm hài lòng CSAT trung bình (1-5 sao) của từng nhân viên.

**Actor:** Quản lý Hỗ trợ, Giám đốc Dịch vụ.

---

## 4. Yêu cầu phi chức năng

### 4.1 Hiệu năng & Khả năng đáp ứng (Performance)
- **NFR-01 (Thời gian tải danh sách vé):** Tải danh sách vé và bộ lọc phản hồi dưới **250ms** (p95) trên tập dữ liệu 500,000 vé.
- **NFR-02 (Độ chính xác Đồng hồ SLA):** Hệ thống cập nhật trạng thái đếm ngược SLA chính xác theo thời gian thực (sai số dưới 1 giây).
- **NFR-03 (Tốc độ gửi Thông báo):** Thông báo vi phạm SLA phát sinh và chuyển tới người quản lý dưới **3 giây**.

### 4.2 Độ tin cậy & Toàn vẹn Dữ liệu (Reliability & ACID)
- **NFR-04 (Tính nguyên tử khi Gộp vé):** Thao tác gộp vé và chuyển giao toàn bộ tin nhắn thực thi trong 1 Database Transaction duy nhất.
- **NFR-05 (Bảo toàn Lịch sử Trao đổi):** Tin nhắn và ghi chú nội bộ trên vé hỗ trợ không thể bị xóa sửa đổi lén lút sau khi đã gửi, đảm bảo tính toàn vẹn chứng từ dịch vụ.

### 4.3 An toàn & Bảo mật (Security)
- **NFR-06 (Bảo mật Ghi chú Nội bộ):** Ghi chú nội bộ (`isInternal = true`) tuyệt đối không bao giờ bị rò rỉ ra các API trả về cho khách hàng hoặc qua email gửi khách.
- **NFR-07 (Bảo vệ Tệp đính kèm):** Tệp đính kèm được quét mã độc tự động và lưu trữ trên vùng lưu trữ đám mây bảo mật có phân quyền truy cập.

---

## 5. Ma trận quyền truy cập tính năng

| Mã FEAT | Tên tính năng nghiệp vụ | Nhân viên (Sales Rep) | Nhân viên Hỗ trợ | Quản lý Hỗ trợ | Quản trị viên (Admin) | Chủ sở hữu (Owner) |
| --- | --- | :---: | :---: | :---: | :---: | :---: |
| `FEAT-01` | Tạo & Quản lý Ticket | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-02` | Tra cứu Mã số Ticket | **Cho phép** | **Cho phép** | **Cho phép** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-03` | Xem Hồ sơ Chi tiết 360 | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-04` | Phân loại Priority/Type | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-05` | Thùng rác & Phục hồi Ticket | — | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-06` | Cập nhật Trạng thái Vé | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-07` | Nhập Mã Giải pháp | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-08` | Mở lại Vé (Reopen) | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-09` | Tự động Đóng Vé (Cron) | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-10` | Cấu hình Chính sách SLA | — | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-11` | Đồng hồ Phản hồi Đầu tiên | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-12` | Đồng hồ Thời gian Giải quyết | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-13` | Cảnh báo Vi phạm SLA | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-14` | Tạm dừng / Tiếp tục SLA | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-15` | Phản hồi Công khai Khách | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-16` | Ghi chú Nội bộ Bảo mật | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-17` | Đính kèm Tệp Tài liệu | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-18` | Nhắc tên Đồng nghiệp | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-19` | Tự động Gửi Khảo sát CSAT | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-20` | Xem Điểm số & Nhận xét CSAT| Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-21` | Gộp Vé Trùng lặp (Merge) | — | — | Có quyền `delete` | **Cho phép** | **Cho phép** |
| `FEAT-22` | Chuyển Tin nhắn khi Gộp | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-23` | Cấu trúc Vé Cha - Vé Con | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-24` | Đồng bộ Giải pháp Vé Con | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-25` | Gắn Vé với Deal | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-26` | Tra cứu Vé theo Deal | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-27` | Thao tác Hàng loạt Bulk | — | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-28` | Nhập / Xuất File Excel 50MB | — | — | Có quyền `import`/`export`| **Toàn quyền** | **Toàn quyền** |
| `FEAT-29` | Báo cáo Khối lượng Tiếp nhận | — | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-30` | Báo cáo SLA & Điểm CSAT | — | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |

---

## 6. Kịch bản chấp nhận tổng hợp (UAT)

### Kịch bản 1: Tiếp nhận Yêu cầu Khẩn cấp, Phản hồi Đầu tiên & Giải quyết Đạt SLA
1. Khách hàng gửi email phản ánh: "Hệ thống đăng nhập bị gián đoạn", mức độ ưu tiên tự động gán là `URGENT` (SLA Phản hồi: 15 phút, SLA Giải quyết: 2 giờ).
2. Hệ thống cấp mã số `TK-10045`, đồng hồ SLA hiển thị đếm ngược 15:00 phút.
3. Sau 5 phút, nhân viên hỗ trợ gửi phản hồi công khai: "Chào anh, chúng tôi đã tiếp nhận sự cố và đội ngũ kỹ thuật đang kiểm tra máy chủ.".
4. **Kỳ vọng:** Mốc phản hồi đầu tiên được ghi nhận thành công sau 5 phút (Đạt SLA), đồng hồ phản hồi đầu tiên dừng lại; đồng hồ giải quyết tiếp tục đếm ngược còn 01:55:00.
5. Sau 45 phút xử lý, nhân viên gửi email thông báo sự cố đã khắc phục, chọn trạng thái "Đã giải quyết" (`Resolved`) và chọn Mã giải pháp "Đã sửa lỗi phần mềm".
6. **Kỳ vọng kết quả:** Vé hoàn tất đạt chuẩn SLA (Tổng thời gian 50 phút < 2 giờ), hệ thống tự động kích hoạt gửi email khảo sát CSAT tới khách hàng.

---

### Kịch bản 2: Tạm dừng & Tiếp tục Đồng hồ SLA khi Chờ Khách hàng
1. Vé đang ở trạng thái "Đang xử lý", đồng hồ giải quyết còn lại 04:00:00.
2. Nhân viên cần khách hàng gửi ảnh chụp màn hình thông báo lỗi, gửi email hướng dẫn và chuyển trạng thái vé sang "Đang chờ khách hàng" (`Pending Customer`).
3. **Kỳ vọng hệ thống:** Đồng hồ SLA lập tức hiển thị nhãn "TẠM DỪNG (PAUSED)", thời gian đóng băng ở mốc 04:00:00.
4. Sau 24 giờ, khách hàng gửi email đính kèm ảnh chụp màn hình.
5. **Kỳ vọng:** Hệ thống tự động chuyển trạng thái vé về "Đang xử lý", đồng hồ SLA tiếp tục đếm ngược từ mốc 04:00:00 (Hạn chốt giải quyết tự động được dời thêm 24 giờ tương ứng).

---

### Kịch bản 3: Đánh giá Khảo sát Hài lòng CSAT 5 Sao
1. Sau khi vé `TK-10045` được giải quyết, khách hàng nhận được email khảo sát.
2. Khách hàng nhấp vào biểu tượng 5 sao trong email và nhập nhận xét: "Nhân viên hỗ trợ rất nhiệt tình và giải quyết sự cố siêu nhanh!".
3. **Kỳ vọng:** Điểm số 5 sao và nhận xét được lưu vào hồ sơ vé, bảng báo cáo CSAT của nhân viên hỗ trợ được cộng thêm 1 lượt đánh giá xuất sắc (100% CSAT).

---

### Kịch bản 4: Gộp Hai Vé Hỗ trợ Trùng lặp (Merge Tickets)
1. Khách hàng gửi liên tiếp 2 yêu cầu cùng lúc: Vé `TK-10050` ("Không in được hóa đơn") và Vé `TK-10051` ("Gửi thêm thông tin lỗi in hóa đơn").
2. Nhân viên mở vé `TK-10050` (Master), bấm "Gộp vé", chọn mã vé cần gộp là `TK-10051`.
3. Bấm "Xác nhận gộp".
4. **Kỳ vọng:** Vé `TK-10051` tự động đóng; toàn bộ nội dung và tệp đính kèm của `TK-10051` được chuyển sang hiển thị đầy đủ trên dòng thời gian của `TK-10050`.

---

### Kịch bản 5: Quản lý Sự cố Lớn Vé Cha - Vé Con (Parent-Child Incident)
1. Sự cố cổng thanh toán bảo trì khiến 30 khách hàng cùng gửi khiếu nại.
2. Trưởng nhóm tạo Vé Cha `TK-20000` ("Sự cố Cổng thanh toán Techcombank gián đoạn").
3. Trưởng nhóm liên kết 30 vé của khách hàng làm Vé Con dưới `TK-20000`.
4. Khi sự cố kỹ thuật hoàn tất, Trưởng nhóm giải quyết Vé Cha `TK-20000`, nhập nội dung giải pháp và chọn "Đồng bộ cho tất cả vé con".
5. **Kỳ vọng:** Toàn bộ 30 vé con tự động được chuyển sang trạng thái `Resolved` và tự động gửi 30 email thông báo giải pháp tới 30 khách hàng đồng thời.

---

### Kịch bản 6: Liên kết Vé Hỗ trợ với Cơ hội Bán hàng (Deal Linkage)
1. Khách hàng thuộc cơ hội bán hàng trị giá 1 tỷ "Hợp đồng Triển khai CRM Toàn quốc" gửi vé khiếu nại `TK-10080` về việc cấu hình máy chủ.
2. Nhân viên hỗ trợ mở vé `TK-10080`, bấm "Gắn Cơ hội bán hàng", chọn Deal tương ứng.
3. **Kỳ vọng:** Nhân viên kinh doanh phụ trách Deal khi mở hồ sơ Deal sẽ nhìn thấy ngay cảnh báo có vé `TK-10080` đang mở, kịp thời phối hợp với đội ngũ hỗ trợ để giải quyết trước ngày ký hợp đồng.

---

## 7. Giới hạn hiện tại & Vấn đề chính sách cần quyết định tiếp

1. **Chính sách Tự động Đóng Vé sau khi Giải quyết (Auto-Close Grace Period):**
   - *Quyết định đã chốt (v2.1):* Áp dụng **72 giờ Calendar Hours** (không phải Business Hours) kể từ khi vé chuyển sang `Resolved`. Khách hàng có thể phản hồi trong 72 giờ để tự động Reopen. Sau 72 giờ không có phản hồi, hệ thống tự động chuyển sang `Closed`.

2. **Chính sách Đa Ngôn ngữ cho Mẫu Email Khảo sát CSAT:**
   - *Đề xuất PM:* Tự động lấy theo trường `locale` của khách hàng trong hồ sơ Contact.

---

## K. ĐIỀU PHỐI & KIỂM SOÁT SLA (TICKET ROUTING & SLA GOVERNANCE)

### FEAT-31 — Quy tắc Phân bổ Vé Tự động (Ticket Auto-Assignment Rules) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khi vé hỗ trợ được tạo tự động từ Email, Chatbot hoặc API mà không có người tiếp nhận trực tiếp, hệ thống tự động phân bổ vé cho Nhân viên phù hợp theo bộ quy tắc định sẵn thay vì để tồn đọng trong hàng đợi không có chủ.

**Actor:** Tiến trình Hệ thống (thực thi), Trưởng nhóm Hỗ trợ (cấu hình quy tắc), Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-31.1 (Phân bổ theo Nhóm kỹ năng)`: Vé được phân bổ đến Nhóm Hỗ trợ (Support Team Queue) phù hợp dựa trên: (a) **Nguồn vé** (Email Helpdesk → Team A; Chat → Team B; API → Team C); (b) **Loại vé** (Kỹ thuật → L2 Team; Thanh toán → Finance Team; Tư vấn → Sales Support).
- `BR-31.2 (Phân bổ Round-robin trong nhóm)`: Trong mỗi nhóm, vé được phân bổ theo vòng lần lượt (Round-robin) dựa trên Workload hiện tại (số vé đang mở) của từng thành viên — người có ít vé nhất nhận vé tiếp theo.
- `BR-31.3 (Fallback về Unassigned Pool)`: Khi không có thành viên nào khả dụng trong nhóm (tất cả đang vắng mặt hoặc nghỉ phép), vé được giữ trong "Unassigned Pool" và gửi cảnh báo ngay đến Trưởng nhóm Hỗ trợ.
- `BR-31.4 (Thời gian phân bổ)`: Quá trình tự động phân bổ phải hoàn tất trong vòng 30 giây kể từ khi vé được tạo để đảm bảo đồng hồ SLA First Response bắt đầu với đúng người chịu trách nhiệm.
- `BR-31.5 (Quyền cấu hình)`: Chỉ Trưởng nhóm Hỗ trợ và Quản trị viên Workspace mới được tạo và chỉnh sửa quy tắc phân bổ.

---

### FEAT-32 — Trạng thái Chờ Bên Thứ Ba & Pause SLA (Pending 3rd Party Status) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khi việc giải quyết vé phụ thuộc vào hành động của Nhà cung cấp bên thứ ba (Vendor), Nhà mạng, Đối tác giao hàng hoặc đội nội bộ khác, cho phép đặt vé vào trạng thái chờ đặc biệt và tạm dừng đồng hồ SLA để Agent không bị tính vi phạm trong thời gian chờ ngoài tầm kiểm soát.

**Actor:** Nhân viên Hỗ trợ, Trưởng nhóm Hỗ trợ.

**Quy tắc nghiệp vụ:**
- `BR-32.1 (Chuyển trạng thái Pending Vendor)`: Khi chuyển vé sang `PENDING_VENDOR`, bắt buộc nhập: (a) **Tên bên thứ ba** đang xử lý; (b) **Mã yêu cầu / Ticket number của Vendor** (nếu có); (c) **Ngày dự kiến phản hồi** từ Vendor.
- `BR-32.2 (Pause SLA khi Pending Vendor)`: Khi vé ở trạng thái `PENDING_VENDOR`, đồng hồ Resolution Time SLA **tự động tạm dừng** tương tự như cơ chế `PENDING_CUSTOMER`. Đồng hồ tiếp tục chạy ngay khi Agent cập nhật trạng thái vé ra khỏi `PENDING_VENDOR`.
- `BR-32.3 (Phân biệt rõ Pending Customer vs Pending Vendor)`: Báo cáo SLA hiển thị rõ thời gian "Tạm dừng do chờ Khách hàng" và "Tạm dừng do chờ Vendor" riêng biệt để phân tích trách nhiệm chính xác.
- `BR-32.4 (Nhắc nhở khi quá hạn Vendor)`: Nếu đến ngày dự kiến phản hồi của Vendor mà vé vẫn còn ở `PENDING_VENDOR`, hệ thống tự động gửi cảnh báo đến Trưởng nhóm Hỗ trợ để can thiệp leo thang với Vendor.

---

### FEAT-33 — Leo thang SLA Tự động (SLA Breach Escalation Matrix) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Thay vì chỉ gửi cảnh báo thụ động khi vi phạm SLA, hệ thống tự động thực hiện hành động leo thang cụ thể (Re-assign, Thông báo cấp cao) theo ma trận đã cấu hình, đảm bảo vé nghiêm trọng không bị bỏ sót.

**Actor:** Tiến trình Hệ thống (thực thi leo thang), Trưởng nhóm Hỗ trợ (cấu hình ma trận), Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-33.1 (Cấu hình Ma trận Leo thang)`: Quản trị viên cấu hình ma trận leo thang cho từng mức độ ưu tiên (URGENT, HIGH, MEDIUM, LOW) với tối đa 3 cấp bậc leo thang. Ví dụ với URGENT: (a) SLA 30% còn lại → Thông báo Trưởng nhóm; (b) SLA 10% còn lại → Re-assign cho Trưởng nhóm xử lý trực tiếp; (c) SLA vi phạm → Thông báo Giám đốc Dịch vụ + ghi vào Incident Report.
- `BR-33.2 (Hành động leo thang)`: Các hành động leo thang có thể được cấu hình: (a) Gửi thông báo đến danh sách người nhận cụ thể; (b) Tự động chuyển quyền sở hữu vé (Re-assign) cho người được chỉ định; (c) Thêm nhãn "SLA ESCALATED" vào vé để ưu tiên hiển thị trên Dashboard.
- `BR-33.3 (Audit leo thang)`: Mọi hành động leo thang được ghi vào Audit Log của vé với thông tin: Thời điểm leo thang, Mức leo thang, Hành động thực hiện, Người nhận thông báo.
- `BR-33.4 (Báo cáo Xu hướng Leo thang)`: Hệ thống cung cấp báo cáo Escalation Rate theo Nhân viên, Nhóm và Khoảng thời gian để Quản lý xác định điểm yếu trong quy trình xử lý.
