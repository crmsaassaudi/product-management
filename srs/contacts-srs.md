# SRS — Phân hệ Quản lý Khách hàng & Danh bạ Doanh nghiệp (Contacts & Accounts Management)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA (Version 2.0) |
| **Module** | CRM — Phân hệ Quản lý Khách hàng & Danh bạ Doanh nghiệp (Contacts & Accounts Management) |
| **Ngày cập nhật** | 2026-08-28 |
| **Phiên bản** | v2.0 (Target Standard) |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`object-manager-srs.md`](./object-manager-srs.md), [`omnichat-srs.md`](./omnichat-srs.md), [`onboarding-srs.md`](./onboarding-srs.md) |

## Ghi chú về nguồn gốc tài liệu

Tài liệu này được xây dựng thông qua quy trình chuẩn hoá 4 bước:
1. **Khảo sát toàn diện mã nguồn thực tế (As-Is):** Khảo sát toàn bộ hệ thống API, schemas, workers, merge services, scoring engines, import/export processors và logic phân quyền của `crm-api` (`src/contacts/`, `src/accounts/`) và `crm-web` (`src/features/contacts/`, `src/features/accounts/`).
2. **Rà soát & Đối chiếu Chuyên sâu:** Kiểm tra chéo từng quy tắc xử lý trùng lặp, sổ cái hoàn tác gộp (Unmerge Ledger), ma trận vòng đời khách hàng, quan hệ đa tổ chức (Multi-Affiliations) và bảo vệ dữ liệu nhạy cảm (Field Masking).
3. **Chuẩn hoá Nghiệp vụ Business First:** Đối chiếu với các chuẩn mực B2B SaaS CRM quốc tế (HubSpot Contacts, Salesforce Lead/Contact/Account Architecture, Pipedrive People/Organizations, Zoho CRM), loại bỏ các giới hạn kỹ thuật và bổ sung các quy trình chuyển đổi khách hàng tiềm năng (Lead Conversion) chuẩn mực.
4. **Đóng băng Đặc tả Mục tiêu:** Hoàn thiện bộ 30 tính năng nghiệp vụ cốt lõi, ma trận phân quyền, 6 kịch bản UAT và danh mục chính sách nghiệp vụ.

**Quy ước nhãn trạng thái:** Mỗi tính năng (FEAT) và quy tắc nghiệp vụ (BR) được gắn nhãn trạng thái:
- **`[Đã triển khai]`** — Phản ánh các tính năng nền tảng đã sẵn sàng và đang vận hành thực tế trong hệ thống.
- **`[Yêu cầu mới]`** — Các tính năng và quy tắc nâng cấp chuẩn Business To-Be được bổ sung để hoàn thiện trải nghiệm quản trị khách hàng toàn diện.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả chi tiết toàn bộ nghiệp vụ quản trị dữ liệu khách hàng cá nhân (Contacts) và tổ chức doanh nghiệp (Accounts) trong hệ thống CRM B2B SaaS:
1. **Quản trị Danh bạ Khách hàng Cá nhân (Contacts):** Thu thập, lưu trữ, làm giàu thông tin và quản trị danh tính 360 độ của mọi cá nhân tương tác với doanh nghiệp.
2. **Quản trị Danh bạ Tổ chức Doanh nghiệp (Accounts):** Quản lý hồ sơ công ty, mã số thuế, ngành nghề, cấu trúc tập đoàn Công ty Mẹ - Con (Parent-Child Hierarchy) và danh sách nhân sự liên hệ trực thuộc.
3. **Mạng lưới Quan hệ Đa chiều (Multi-Affiliations & Person Relations):** Quản lý mối quan hệ một cá nhân làm việc cho nhiều công ty cùng lúc và mạng lưới quan hệ người-với-người (Báo cáo trực tiếp, Giới thiệu, Đối tác).
4. **Vòng đời Khách hàng & Chuyển đổi Tiềm năng (Lifecycle Stages & Lead Conversion):** Định vị mức độ trưởng thành của khách hàng qua 7 giai đoạn và quy trình thẩm định chuyển đổi Khách hàng tiềm năng (Lead) thành Liên hệ chính thức + Doanh nghiệp + Cơ hội bán hàng chỉ bằng 1 thao tác nguyên tử.
5. **Điểm Tiềm năng & Chấm điểm Tự động (Lead Scoring & Decay):** Đánh giá mức độ tiềm năng theo hồ sơ và tần suất tương tác để ưu tiên phân bổ cho đội ngũ kinh doanh.
6. **Chất lượng Dữ liệu & Xử lý Trùng lặp (Deduplication, Merge & Unmerge):** Tự động phát hiện trùng lặp, xem trước tác động gộp, gộp bản ghi an toàn kèm Sổ cái Hoàn tác Gộp (Unmerge Ledger).
7. **Nhập / Xuất Dữ liệu Thông minh (Smart Bulk Import/Export):** Trình trợ lý nhập khẩu Excel/CSV dung lượng lớn (50MB) có tự động ánh xạ cột và xuất báo cáo lỗi chi tiết.
8. **Dòng thời gian Hoạt động Hợp nhất 360 độ (Unified Customer Timeline):** Bảng luồng thông tin trung tâm tập hợp mọi ghi chú, vé hỗ trợ, cơ hội bán hàng, nhiệm vụ và hội thoại đa kênh.

### 1.2 Phạm vi

Tài liệu bao gồm 10 nhóm chức năng cốt lõi:
- **Nhóm A: Quản trị Hồ sơ Khách hàng Cá nhân (Contacts):** Tạo mới, cập nhật, chi tiết 360 độ, phân loại, gắn nhãn (Tags), mặt nạ bảo vệ dữ liệu nhạy cảm (Field Masking) và Thùng rác phục hồi.
- **Nhóm B: Quản trị Hồ sơ Tổ chức & Doanh nghiệp (Accounts):** Tạo mới, cập nhật, cấu trúc Công ty Mẹ - Con, quản lý thuế/ngành nghề và danh sách nhân sự trực thuộc.
- **Nhóm C: Mạng lưới Quan hệ Đa chiều (Multi-Affiliations & Person Relations):** Quan hệ người - công ty đa năng và quan hệ người - người.
- **Nhóm D: Vòng đời Khách hàng & Chuyển đổi Tiềm năng (Lifecycle & Lead Conversion):** Chuẩn 7 giai đoạn vòng đời, ma trận chuyển đổi và quy trình chuyển đổi Lead 1-click.
- **Nhóm E: Điểm Tiềm năng & Chấm điểm Tự động (Lead Scoring):** Chấm điểm tiềm năng theo hồ sơ và hành vi tương tác, cơ chế suy giảm điểm theo thời gian.
- **Nhóm F: Nhận diện Trùng lặp & Gộp Bản ghi An toàn (Deduplication, Merge & Unmerge):** Kiểm tra trùng lặp, Preview Merge, Gộp có sổ cái và Hoàn tác gộp bản ghi (Unmerge).
- **Nhóm G: Nhập Dữ liệu Thông minh qua Hàng đợi (Smart Bulk Import):** Upload Excel/CSV 50MB, tự động ánh xạ cột, xử lý bất đồng bộ và báo cáo lỗi chi tiết.
- **Nhóm H: Xuất Dữ liệu & Danh sách Hiển thị Tùy chỉnh (Export & List Views):** Xuất dữ liệu luồng CSV bảo mật và danh sách hiển thị dùng chung (Shared List Views).
- **Nhóm I: Dòng thời gian Hoạt động Hợp nhất 360 độ (Unified Timeline & Customer Context):** Hợp nhất dòng thời gian và API ngữ cảnh khách hàng cho Hộp thư Omni-channel.
- **Nhóm J: Quản trị Định danh, Đồng thuận & Khả năng Tiếp cận (Identities, Consent & Deliverability):** Quản lý kênh liên lạc chính, trạng thái Bounced/Verified, Đồng thuận nhận tin (Opt-in Consent) và Định danh dùng chung (Shared Identifiers).

**Ngoài phạm vi (thuộc về các tài liệu SRS chuyên biệt khác):**
- **Nghiệp vụ Quản trị Giao tiếp Đa kênh & Hộp thư chung (Omni-channel Inbox & Conversations):** Thuộc về [`omnichat-srs.md`](./omnichat-srs.md).
- **Nghiệp vụ Cấu hình Phễu Bán hàng & Bảng Kanban Cơ hội (Deals & Pipelines):** Thuộc về [`deals-pipeline-srs.md`](./deals-pipeline-srs.md).
- **Nghiệp vụ Quản trị Vé Hỗ trợ & SLA Chăm sóc khách hàng (Tickets & Customer Service):** Thuộc về [`tickets-srs.md`](./tickets-srs.md).
- **Nghiệp vụ Phân quyền Trường chi tiết & Tùy biến Bố cục Layout (Field-Level Security & Layouts):** Thuộc về [`object-manager-srs.md`](./object-manager-srs.md).
- **Chi tiết Ma trận Phân quyền ABAC & Cây tổ chức:** Thuộc về [`iam-tenant-authorization.md`](./iam-tenant-authorization.md).

### 1.3 Đối tượng đọc

- **Product Owner / Business Analyst:** Chuẩn mực đặc tả nghiệp vụ quản lý dữ liệu khách hàng để thiết kế tính năng và nghiệm thu sản phẩm.
- **Đội ngũ Phát triển (Frontend / Backend):** Căn cứ thiết kế API, schemas, thuật toán xử lý dữ liệu và trải nghiệm giao diện người dùng.
- **Đội ngũ Kiểm thử (QA/QC):** Thiết kế bộ kịch bản kiểm thử tích hợp và kiểm thử chức năng chuyên sâu.
- **Đội ngũ Kinh doanh & Marketing:** Nắm rõ quy trình quản lý danh bạ, luồng chuyển đổi tiềm năng và quản trị chất lượng dữ liệu khách hàng.

### 1.4 Thuật ngữ & Viết tắt

| Thuật ngữ | Định nghĩa nghiệp vụ |
| --- | --- |
| **Khách hàng Cá nhân (Contact)** | Thực thể đại diện cho một con người cụ thể trong CRM kèm toàn bộ thông tin liên hệ và lịch sử tương tác. |
| **Tổ chức / Doanh nghiệp (Account)** | Thực thể đại diện cho một pháp nhân, công ty, tập đoàn hoặc cơ quan đối tác kinh doanh. |
| **Giai đoạn Vòng đời (Lifecycle Stage)** | Vị trí của khách hàng trong phễu chuyển đổi: *Subscriber -> Lead -> MQL -> SQL -> Opportunity -> Customer -> Evangelist*. |
| **Chuyển đổi Tiềm năng (Lead Conversion)** | Thao tác thẩm định nâng cấp Lead thành Contact chính thức, liên kết/tạo Account và Deal tương ứng. |
| **Bản ghi Chính (Master Record)** | Bản ghi sống sót và giữ lại định danh sau khi thực hiện giao dịch gộp hai khách hàng hoặc hai doanh nghiệp. |
| **Sổ cái Hoàn tác Gộp (Unmerge Ledger)** | Bảng lưu vết lịch sử gộp cho phép khôi phục lại trạng thái ban đầu trước khi gộp nếu có sai sót. |
| **Quan hệ Đa tổ chức (Multi-Affiliations)** | Khả năng liên kết một cá nhân với nhiều công ty cùng lúc với các chức danh và vai trò khác nhau. |
| **Dòng thời gian 360 độ (Unified Timeline)** | Luồng hiển thị hợp nhất toàn bộ tương tác, ghi chú, vé, cơ hội và công việc của một khách hàng. |
| **Điểm Tiềm năng (Lead Score)** | Điểm số tự động đánh giá độ nóng của khách hàng dựa trên hồ sơ và mức độ tương tác thực tế. |
| **Đồng thuận Nhận tin (Opt-in Consent)** | Trạng thái đồng ý nhận thông tin tiếp thị/quảng bá của khách hàng theo chuẩn GDPR và chống thư rác. |

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

Trong vận hành kinh doanh B2B và B2C, dữ liệu khách hàng thường bị phân tán, trùng lặp và thiếu nhất quán:
- Nhân viên kinh doanh không nắm được lịch sử tương tác trước đó của đồng nghiệp hoặc bộ phận hỗ trợ với khách hàng.
- Dữ liệu bị trùng lặp do nhập từ nhiều nguồn (Website, Livechat, Excel, Facebook, sự kiện) gây lãng phí nguồn lực và trải nghiệm khách hàng kém.
- Một cá nhân đóng nhiều vai trò tại nhiều công ty khác nhau nhưng hệ thống CRM truyền thống chỉ cho phép gán vào một công ty duy nhất.
- Khách hàng tiềm năng (Leads) bị bỏ quên hoặc chuyển đổi thủ công rời rạc, làm mất liên kết giữa người liên hệ, công ty và cơ hội bán hàng.
- Rủi ro lộ lọt thông tin cá nhân nhạy cảm (PII) khi không có cơ chế che giấu trường dữ liệu (Field Masking).

Module Contacts & Accounts giải quyết triệt để các bài toán trên bằng cách cung cấp một nền tảng quản trị danh bạ 360 độ mạnh mẽ, tự động phát hiện trùng lặp, hỗ trợ quan hệ đa chiều, hợp nhất dòng thời gian hoạt động và bảo vệ dữ liệu nhạy cảm theo tiêu chuẩn quốc tế.

### 2.2 Vai trò người dùng (Actor)

| Actor | Mô tả vai trò và quyền hạn |
| --- | --- |
| **Nhân viên Kinh doanh (Sales Representative)** | Tạo mới, chăm sóc khách hàng cá nhân/doanh nghiệp, cập nhật giai đoạn vòng đời và theo dõi dòng thời gian tương tác. |
| **Quản lý Kinh doanh (Sales Manager)** | Phân công khách hàng cho nhân viên, duyệt chuyển đổi khách hàng tiềm năng, theo dõi điểm số và báo cáo danh bạ. |
| **Nhân viên Hỗ trợ Khách hàng (Support Agent)** | Tra cứu ngữ cảnh khách hàng 360 độ khi tiếp nhận hội thoại/vé hỗ trợ, cập nhật kênh liên lạc và ghi chú. |
| **Nhân viên Marketing (Marketing Specialist)** | Xuất danh sách khách hàng phục vụ chiến dịch, quản lý quy tắc chấm điểm tiềm năng (Lead Scoring Rules), cấu hình trạng thái Opt-in/Opt-out từng kênh, xem báo cáo funnel chuyển đổi. **Không có quyền Xóa, Gộp hay Hoàn tác gộp bản ghi.** |
| **Quản lý Marketing (Marketing Manager)** | Toàn quyền chức năng Marketing: Cấu hình Lead Scoring, Lifecycle tự động, quy tắc Nurturing; phê duyệt xuất dữ liệu quy mô lớn; xem báo cáo UTM Source và Revenue Attribution. |
| **Quản trị viên Không gian làm việc (Tenant Admin)** | Quản lý cấu hình trường dữ liệu, thực thi gộp bản ghi trùng lặp, hoàn tác gộp, nhập/xuất dữ liệu hàng loạt và cấu hình quy tắc chấm điểm. |
| **Chủ sở hữu Không gian làm việc (Tenant Owner)** | Toàn quyền quản trị danh bạ, xem toàn bộ dữ liệu tổ chức và cấu hình chính sách bảo mật dữ liệu nhạy cảm. |
| **Tiến trình Hệ thống (System Engine / Background Workers)** | Tự động tính toán điểm tiềm năng (Scoring), xử lý tệp nhập khẩu/xuất khẩu bất đồng bộ (BullMQ) và quét đồng bộ danh tính đa kênh. |

### 2.3 Bảng tổng hợp 30 tính năng nghiệp vụ

| Nhóm | Mã FEAT | Tên tính năng nghiệp vụ | Trạng thái |
| --- | --- | --- | --- |
| **A. Quản trị Khách hàng Cá nhân** | `FEAT-01` | Tạo mới & Quản lý Thông tin Khách hàng Cá nhân (Contact CRUD) | `[Đã triển khai]` |
| | `FEAT-02` | Hồ sơ Chi tiết Khách hàng 360 độ (360-Degree Customer Profile) | `[Đã triển khai]` |
| | `FEAT-03` | Quản lý Thẻ phân loại Hàng loạt (Bulk Tagging & Tag Management) | `[Đã triển khai]` |
| | `FEAT-04` | Bảo vệ Dữ liệu Nhạy cảm & Mở khóa Mặt nạ (Field Masking & Unmask) | `[Đã triển khai]` |
| | `FEAT-05` | Thùng rác Khách hàng & Phục hồi Bản ghi (Contact Recycle Bin & Restore) | `[Đã triển khai]` |
| **B. Quản trị Doanh nghiệp & Tổ chức** | `FEAT-06` | Tạo mới & Quản lý Thông tin Doanh nghiệp (Account CRUD) | `[Đã triển khai]` |
| | `FEAT-07` | Cấu trúc Cây Doanh nghiệp Công ty Mẹ - Con (Parent-Child Hierarchy) | `[Đã triển khai]` |
| | `FEAT-08` | Hồ sơ Chi tiết Doanh nghiệp & Danh sách Nhân sự Liên hệ | `[Đã triển khai]` |
| | `FEAT-09` | Thùng rác Doanh nghiệp & Phục hồi Bản ghi (Account Recycle Bin & Restore) | `[Đã triển khai]` |
| **C. Mạng lưới Quan hệ Đa chiều** | `FEAT-10` | Quan hệ Đa Doanh nghiệp của Cá nhân (Multi-Company Affiliations) | `[Đã triển khai]` |
| | `FEAT-11` | Mạng lưới Quan hệ Giữa các Cá nhân (Person-to-Person Relations) | `[Đã triển khai]` |
| **D. Vòng đời & Chuyển đổi Tiềm năng** | `FEAT-12` | Quản trị Giai đoạn Vòng đời Khách hàng (7 Lifecycle Stages) | `[Đã triển khai]` |
| | `FEAT-13` | Lịch sử Chuyển đổi Giai đoạn Vòng đời (Stage Transition History) | `[Đã triển khai]` |
| | `FEAT-14` | Quy trình Chuyển đổi Khách hàng Tiềm năng 1-Click (Lead Conversion) | `[Yêu cầu mới]` |
| **E. Điểm Tiềm năng & Chấm điểm** | `FEAT-15` | Động cơ Chấm điểm Tiềm năng Tự động (Lead Scoring Engine) | `[Đã triển khai]` |
| | `FEAT-16` | Cơ chế Suy giảm Điểm Tiềm năng theo Thời gian (Score Decay Engine) | `[Yêu cầu mới]` |
| **F. Xử lý Trùng lặp & Gộp Bản ghi** | `FEAT-17` | Tự động Nhận diện & Kiểm tra Trùng lặp Khách hàng (Duplicate Check) | `[Đã triển khai]` |
| | `FEAT-18` | Xem trước Tác động Gộp Bản ghi (Merge Preview & Impact Analysis) | `[Đã triển khai]` |
| | `FEAT-19` | Gộp Khách hàng Cá nhân & Doanh nghiệp An toàn (Contact/Account Merge) | `[Đã triển khai]` |
| | `FEAT-20` | Sổ cái Hoàn tác Gộp Bản ghi (Unmerge Contacts/Accounts Ledger) | `[Đã triển khai]` |
| | `FEAT-21` | Khôi phục Tự động Giao dịch Gộp Bị Lỗi (Recover Failed Merge) | `[Đã triển khai]` |
| **G. Nhập Dữ liệu Thông minh** | `FEAT-22` | Tải lên & Tiếp nhận Tệp Nhập khẩu Excel/CSV Dung lượng lớn (50MB) | `[Đã triển khai]` |
| | `FEAT-23` | Trợ lý Tự động Ánh xạ Cột Dữ liệu (Auto Field Mapping Wizard) | `[Yêu cầu mới]` |
| | `FEAT-24` | Xử lý Nhập khẩu Hàng đợi & Xuất Báo cáo Lỗi Chi tiết (Import Error Report) | `[Đã triển khai]` |
| **H. Xuất Dữ liệu & Danh sách** | `FEAT-25` | Xuất Dữ liệu Khách hàng Dạng Luồng An toàn qua Token (Streaming Export) | `[Đã triển khai]` |
| | `FEAT-26` | Quản lý & Tích hợp Danh sách Hiển thị Dùng chung (Shared List Views) | `[Đã triển khai]` |
| **I. Dòng thời gian 360 & Ngữ cảnh** | `FEAT-27` | Dòng thời gian Hoạt động Hợp nhất 360 độ (Unified Customer Timeline) | `[Đã triển khai]` |
| | `FEAT-28` | Cung cấp Ngữ cảnh Khách hàng 1 Chạm cho Omni Inbox (Customer Context API) | `[Đã triển khai]` |
| **J. Định danh & Đồng thuận** | `FEAT-29` | Quản lý Danh tính Đa kênh & Trạng thái Tiếp cận (Identities & Deliverability)| `[Đã triển khai]` |
| | `FEAT-30` | Quản lý Trạng thái Đồng thuận Tiếp thị & Định danh Dùng chung (Consent) | `[Đã triển khai]` |

---

## 3. Đặc tả yêu cầu chức năng

## A. QUẢN TRỊ KHÁCH HÀNG CÁ NHÂN (CONTACTS MANAGEMENT)

### FEAT-01 — Tạo mới & Quản lý Thông tin Khách hàng Cá nhân (Contact CRUD) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép người dùng tạo mới, tra cứu danh sách, xem chi tiết, cập nhật và xóa khách hàng cá nhân trong không gian làm việc.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-01.1 (Thông tin bắt buộc)`: Bắt buộc phải có ít nhất một phương thức liên lạc chính: `email` (đúng chuẩn RFC 5322) HOẶC `phone` (chuẩn hoá theo chuẩn quốc tế E.164). Họ và Tên (`fullName`) là trường **khuyến khích** nhưng không bắt buộc — nếu không có Tên, hệ thống tự động hiển thị tên fallback từ prefix email (ví dụ: `ceo@company.com` → tên hiển thị: `ceo`) hoặc số điện thoại. Trường hợp tiếp nhận từ kênh Livechat/Khách vãng lai chưa có thông tin, hệ thống tự động gán tên tạm `Khách vãng lai #{ID}` theo định nghĩa Hồ sơ Khách hàng Tạm (Provisional Record).
- `BR-01.2 (Kiểm tra định dạng)`: Địa chỉ email phải đúng chuẩn RFC 5322; Số điện thoại được tự động chuẩn hoá theo chuẩn quốc tế E.164 (ví dụ: `+84901234567`, `+966501234567`).
- `BR-01.3 (Phân bổ quyền sở hữu)`: Khi tạo mới, người tạo tự động được gán làm Người phụ trách (`ownerId`), Đơn vị tổ chức được gán theo Đơn vị tổ chức của người tạo (`orgUnitId`), trừ khi được chỉ định khác bởi người có quyền.
- `BR-01.4 (Phân quyền truy cập theo ABAC)`: Người dùng chỉ được xem/sửa các liên hệ thuộc phạm vi dữ liệu được gán (Cá nhân / Phòng ban / Cây phòng ban / Toàn tổ chức).

---

### FEAT-02 — Hồ sơ Chi tiết Khách hàng 360 độ (360-Degree Customer Profile) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Màn hình tổng hợp toàn diện mọi thông tin của khách hàng: Thông tin cá nhân, Doanh nghiệp trực thuộc, Giai đoạn vòng đời, Điểm tiềm năng, Các cơ hội bán hàng, Vé hỗ trợ, Công việc, Ghi chú và Lịch sử tương tác.

**Actor:** Mọi người dùng có quyền xem Contact.

**Quy tắc nghiệp vụ:**
- `BR-02.1`: Hiển thị bố cục chuẩn gồm: Panel tóm tắt bên trái (Thông tin chính, Điểm tiềm năng, Trạng thái liên lạc), Khu vực trung tâm (Dòng thời gian 360 độ, Tab Ghi chú, Tab Công việc, Tab Vé hỗ trợ, Tab Cơ hội) và Panel liên kết bên phải (Doanh nghiệp trực thuộc, Mối quan hệ cá nhân).
- `BR-02.2 (Hành động nhanh & Phân quyền)`: Cho phép thực hiện các hành động nhanh ngay trên hồ sơ: Gửi email, Tạo cuộc gọi, Tạo ghi chú nhanh, Tạo công việc mới, Tạo vé hỗ trợ mới, Chuyển giai đoạn vòng đời. Thao tác "Tạo cơ hội mới" (Deal) chỉ hiển thị khi người dùng sở hữu quyền `deals:create`; Nhân viên Hỗ trợ (Support Agent) không có quyền tạo Deal sẽ bị ẩn nút này hoặc chuyển thành "Gợi ý Cơ hội" (Lead Referral).

---

### FEAT-03 — Quản lý Thẻ phân loại Hàng loạt (Bulk Tagging & Tag Management) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép gắn hoặc gỡ nhiều thẻ phân loại (Tags) cho một hoặc hàng loạt khách hàng cùng lúc để phục vụ việc lọc và phân khúc chiến dịch.

**Actor:** Nhân viên Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-03.1`: Hỗ trợ chọn nhiều khách hàng trên danh sách và thực hiện gắn/gỡ thẻ hàng loạt (`POST /api/v1/contacts/bulk-tag`).
- `BR-03.2`: Tên thẻ không phân biệt chữ hoa/thường, tự động cắt tỉa khoảng trắng và không vượt quá 50 ký tự mỗi thẻ.

---

### FEAT-04 — Bảo vệ Dữ liệu Nhạy cảm & Mở khóa Mặt nạ (Field Masking & Unmask) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Các trường dữ liệu nhạy cảm (Số điện thoại cá nhân, Số CCCD/Hộ chiếu, Email cá nhân) được hiển thị dưới dạng mặt nạ che giấu một phần (ví dụ: `090****567`). Chỉ người dùng có quyền `unmask` mới được phép mở khóa xem đầy đủ.

**Actor:** Người dùng có quyền `unmask` trên phân hệ Contacts.

**Quy tắc nghiệp vụ:**
- `BR-04.1 (Mặt nạ mặc định)`: Dữ liệu trả về qua API danh sách/chi tiết mặc định bị che một phần theo chính sách bảo mật trường (Field-Level Security).
- `BR-04.2 (Mở khóa có kiểm toán)`: Khi người dùng bấm biểu tượng "Mắt" để mở khóa xem dữ liệu thật (`POST /api/v1/contacts/:id/unmask-fields`), hệ thống kiểm tra quyền `unmask`, trả về giá trị giải mã và tự động ghi nhật ký kiểm toán bảo mật (Audit Log) ghi nhận ai đã mở khóa xem trường nào vào lúc nào.

---

### FEAT-05 — Thùng rác Khách hàng & Phục hồi Bản ghi (Contact Recycle Bin & Restore) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi xóa một khách hàng, hệ thống thực hiện Xóa mềm (Soft Delete) và đưa vào Thùng rác trong 30 ngày. Cho phép người có thẩm quyền khôi phục lại nguyên vẹn bản ghi.

**Actor:** Quản trị viên Workspace, Người có quyền `delete` trên Contacts.

**Quy tắc nghiệp vụ:**
- `BR-05.1 (Xóa mềm)`: Đánh dấu `deletedAt = now()`, ẩn bản ghi khỏi toàn bộ danh sách tìm kiếm và báo cáo thông thường.
- `BR-05.2 (Danh sách Thùng rác)`: Cung cấp màn hình Thùng rác (`GET /api/v1/contacts/recycle-bin`) hiển thị danh sách các bản ghi đã xóa kèm ngày xóa và người thực hiện xóa.
- `BR-05.3 (Khôi phục bản ghi)`: Người có quyền `delete` được phép khôi phục bản ghi (`POST /api/v1/contacts/:id/restore`), xóa cờ `deletedAt` và phục hồi lại toàn bộ các liên kết dữ liệu cũ.
- `BR-05.4 (Dọn dẹp vĩnh viễn)`: Tiến trình hệ thống tự động xóa vĩnh viễn (Hard Delete) các bản ghi nằm trong Thùng rác quá 30 ngày.
- `BR-05.5 (Xử lý Thực thể Con khi Xóa mềm) [Yêu cầu mới]`: Khi một Contact bị xóa mềm vào Thùng rác, các Vé hỗ trợ (Tickets) và Cơ hội bán hàng (Deals) đang mở của khách hàng đó không bị xóa mà được gắn nhãn cảnh báo `[Khách hàng trong thùng rác]`; hệ thống tự động khóa tính năng gửi phản hồi công khai (Public Reply) trên vé cho đến khi Contact được khôi phục.

---

## B. QUẢN TRỊ DOANH NGHIỆP & TỔ CHỨC (ACCOUNTS MANAGEMENT)

### FEAT-06 — Tạo mới & Quản lý Thông tin Doanh nghiệp (Account CRUD) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Quản lý danh bạ các công ty, tổ chức đối tác kinh doanh với đầy đủ thông tin pháp nhân và thương mại.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-06.1 (Thông tin doanh nghiệp)`: Bao gồm Tên công ty (bắt buộc), Tên thương mại/Viết tắt, Mã số thuế / Mã định danh doanh nghiệp, Ngành nghề kinh doanh, Quy mô nhân sự, Doanh thu hàng năm, Website, Địa chỉ trụ sở, Số điện thoại tổng đài.
- `BR-06.2 (Định danh duy nhất)`: Mã số thuế hoặc Tên miền website (Domain) được dùng làm căn cứ tự động kiểm tra trùng lặp doanh nghiệp.

---

### FEAT-07 — Cấu trúc Cây Doanh nghiệp Công ty Mẹ - Con (Parent-Child Hierarchy) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép thiết lập quan hệ phân cấp giữa Công ty Mẹ (Holding/Tập đoàn) và các Công ty Con (Subsidiaries / Chi nhánh thành viên).

**Actor:** Nhân viên Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-07.1`: Mỗi doanh nghiệp có thể khai báo một Doanh nghiệp Mẹ (`parentAccountId`).
- `BR-07.2 (Chống vòng lặp)`: Hệ thống kiểm tra nghiêm ngặt ngăn chặn quan hệ vòng tròn (Công ty A là mẹ của B, B không thể là mẹ của A).
- `BR-07.3 (Báo cáo hợp nhất)`: Cho phép xem sơ đồ cây tổ chức và xem báo cáo tổng doanh số / số lượng cơ hội hợp nhất của toàn bộ tập đoàn.

---

### FEAT-08 — Hồ sơ Chi tiết Doanh nghiệp & Danh sách Nhân sự Liên hệ `[Đã triển khai]`

**Mô tả nghiệp vụ:** Màn hình 360 độ của Doanh nghiệp hiển thị danh sách tất cả các nhân sự liên hệ (Contacts) thuộc công ty, các Cơ hội bán hàng, Vé hỗ trợ và Dòng thời gian tương tác của toàn bộ nhân sự trực thuộc công ty đó.

**Actor:** Mọi người dùng có quyền xem Account.

**Quy tắc nghiệp vụ:**
- `BR-08.1`: Hiển thị danh sách nhân sự liên hệ kèm chức danh, số điện thoại, email và đánh dấu ai là Người liên hệ chính (Primary Contact).
- `BR-08.2`: Dòng thời gian của Doanh nghiệp tự động tổng hợp dòng thời gian của tất cả các nhân sự liên hệ thuộc doanh nghiệp đó.

---

### FEAT-09 — Thùng rác Doanh nghiệp & Phục hồi Bản ghi (Account Recycle Bin & Restore) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Xóa mềm và khôi phục doanh nghiệp tương tự cơ chế của Contact.

**Actor:** Quản trị viên Workspace, Người có quyền `delete` trên Accounts.

**Quy tắc nghiệp vụ:**
- `BR-09.1`: Khi xóa doanh nghiệp, các liên hệ trực thuộc không bị xóa mà chuyển trường doanh nghiệp sang trạng thái rỗng hoặc chuyển sang công ty khác theo chỉ định.

---

## C. MẠNG LƯỚI QUAN HỆ ĐA CHIỀU (MULTI-AFFILIATIONS & PERSON RELATIONS)

### FEAT-10 — Quan hệ Đa Doanh nghiệp của Cá nhân (Multi-Company Affiliations) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Giải quyết bài toán một cá nhân làm việc cho nhiều công ty cùng lúc (ví dụ: Giám đốc tại Công ty A, đồng thời là Cố vấn cấp cao tại Công ty B và Cổ đông tại Công ty C).

**Actor:** Nhân viên Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-10.1 (Bản ghi liên kết Affiliation)`: Mỗi liên kết giữa 1 Contact và 1 Account lưu trữ: Chức danh (Job Title), Phòng ban công tác, Vai trò (Chính / Phụ / Cố vấn / Cổ đông), Ngày bắt đầu, Ngày kết thúc, Trạng thái (Đang công tác / Đã nghỉ việc).
- `BR-10.2 (Doanh nghiệp chính)`: Mỗi Contact có duy nhất 1 Doanh nghiệp chính (`isPrimary = true`) để hiển thị mặc định trên danh sách và báo cáo tổng quan.
- `BR-10.3`: Cho phép thêm, sửa, xóa các liên kết công ty qua API `/api/v1/contacts/:id/affiliations`.

---

### FEAT-11 — Mạng lưới Quan hệ Giữa các Cá nhân (Person-to-Person Relations) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Thiết lập mạng lưới liên kết trực tiếp giữa hai con người trong CRM để phục vụ chiến lược bán hàng theo mạng lưới quan hệ (Relationship-based Selling).

**Actor:** Nhân viên Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-11.1 (Loại quan hệ)`: Hỗ trợ các loại quan hệ chuẩn:
  - **Quản lý trực tiếp / Cấp dưới (Reports To / Subordinate)**
  - **Người giới thiệu / Được giới thiệu (Referred By / Referee)**
  - **Thành viên gia đình (Family / Household)**
  - **Đối tác kinh doanh (Business Partner)**
  - **Trợ lý / Người đại diện (Assistant / Proxy)**
- `BR-11.2 (Tính đối xứng)`: Khi tạo quan hệ "A là Quản lý của B", hệ thống tự động nhận diện chiều ngược lại "B là Cấp dưới của A".

---

## D. VÒNG ĐỜI KHÁCH HÀNG & CHUYỂN ĐỔI TIỀM NĂNG (LIFECYCLE & LEAD CONVERSION)

### FEAT-12 — Quản trị Giai đoạn Vòng đời Khách hàng (9 Lifecycle Stages) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Phân loại khách hàng theo đúng vị trí trên hành trình trải nghiệm qua các giai đoạn chuẩn quốc tế, bao gồm đầy đủ các trạng thái "Đang nuôi dưỡng", "Đã loại" và "Đã rời bỏ".

**Actor:** Nhân viên Kinh doanh, Nhân viên Marketing, Quản trị viên Workspace.

**Chi tiết các giai đoạn vòng đời:**
1. **Người Đăng ký (Subscriber):** Khách mới đăng ký nhận bản tin/tài liệu, chưa có nhu cầu mua rõ ràng.
2. **Khách hàng Tiềm năng (Lead):** Đã để lại thông tin liên hệ và thể hiện sự quan tâm ban đầu.
3. **Tiềm năng Đủ điều kiện Tiếp thị (Marketing Qualified Lead — MQL):** Đã tương tác nhiều lần qua các chiến dịch tiếp thị và đạt điểm tiềm năng ban đầu.
4. **Tiềm năng Đủ điều kiện Bán hàng (Sales Qualified Lead — SQL):** Đã được đội ngũ kinh doanh thẩm định trực tiếp và sẵn sàng trao đổi cơ hội mua hàng.
5. **Cơ hội Kinh doanh (Opportunity):** Đang có ít nhất một Cơ hội bán hàng (Deal) đang mở trên phễu.
6. **Đang Nuôi dưỡng (Nurturing):** Lead chưa sẵn sàng mua ngay (hết ngân sách, chờ phê duyệt nội bộ) nhưng vẫn có tiềm năng. Tiếp tục được chăm sóc qua chiến dịch định kỳ.
7. **Khách hàng Chính thức (Customer):** Đã ký hợp đồng hoặc phát sinh giao dịch mua hàng thành công.
8. **Khách hàng Trung thành / Đại sứ (Evangelist):** Khách hàng gắn bó lâu năm, sẵn sàng giới thiệu khách hàng mới.
9. **Đã Rời bỏ (Churned / Former Customer):** Khách hàng đã hủy dịch vụ hoặc chấm dứt hợp đồng. Không tiếp tục nhận Email Marketing tự động trừ khi có chiến dịch Win-Back được phê duyệt riêng.
10. **Đã Loại (Disqualified):** Lead không phù hợp với tập khách hàng mục tiêu (Sai ngành, Không đủ ngân sách, Lừa đảo/Spam). Bị loại khỏi mọi chiến dịch marketing.

**Quy tắc nghiệp vụ:**
- `BR-12.1 (Chính sách chuyển đổi giai đoạn)`: Tuân thủ quy tắc chuyển đổi có kiểm soát; khi chuyển giai đoạn, hệ thống ghi nhận thời điểm chuyển và người thực hiện.
- `BR-12.2 (Tự động nâng cấp)`: Khi một liên hệ được tạo mới một Deal, hệ thống tự động nâng cấp giai đoạn lên tối thiểu là `Opportunity`. Khi Deal chuyển sang `Closed Won`, hệ thống tự động nâng cấp lên `Customer`.
- `BR-12.3 (Quy tắc Đa Cơ hội & Xử lý khi Deal Thất bại) [Yêu cầu mới]`:
  - Khách hàng đã đạt giai đoạn `Customer` (do có ít nhất 1 Deal `Closed Won`) sẽ **không bị hạ hạng** khi có các Deal Upsell/Cross-sell tiếp theo bị `Closed Lost`.
  - Đối với Contact chưa từng là `Customer`: khi tất cả Deal đều `Closed Lost`, hệ thống **không tự động hạ cấp** mà chuyển sang giai đoạn `Nurturing` và yêu cầu Sales nhập "Lý do không chuyển đổi" để Marketing có kịch bản tái tiếp cận phù hợp.
- `BR-12.4 (Chuyển Disqualified)`: Chỉ người dùng có quyền `contacts:disqualify` (Sales Manager trở lên) mới được phép chuyển Contact sang trạng thái `Disqualified`. Bắt buộc nhập lý do loại từ danh mục chuẩn.
- `BR-12.5 (Chuyển Churned)`: Khi Contact chuyển sang `Churned`, tự động gửi thông báo nội bộ đến Account Manager/Sales Manager phụ trách và tắt toàn bộ chiến dịch Marketing tự động.

---

### FEAT-13 — Lịch sử Chuyển đổi Giai đoạn Vòng đời (Stage Transition History) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Lưu vết toàn bộ lịch sử thăng hạng/hạ hạng giai đoạn vòng đời của khách hàng để phục vụ phân tích tỷ lệ chuyển đổi và vận tốc bán hàng (Sales Velocity).

**Actor:** Mọi người dùng có quyền xem Contact.

**Quy tắc nghiệp vụ:**
- `BR-13.1`: Ghi nhận: Giai đoạn trước, Giai đoạn sau, Thời gian ở giai đoạn cũ (thời lượng tính bằng ngày/giờ), Lý do chuyển đổi, Người thực hiện.
- `BR-13.2`: Cung cấp API tra cứu lịch sử `/api/v1/contacts/:id/stage-history`.

---

### FEAT-14 — Quy trình Chuyển đổi Khách hàng Tiềm năng 1-Click (Lead Conversion) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khi một Khách hàng tiềm năng (Lead) được thẩm định đủ điều kiện mua hàng, cho phép nhân viên kinh doanh kích hoạt quy trình Chuyển đổi (Convert Lead) chỉ bằng 1 thao tác bấm nút.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh.

**Luồng chính chuyển đổi:**
1. Người dùng bấm nút **"Chuyển đổi Tiềm năng" (Convert Lead)** trên hồ sơ Lead.
2. Hộp thoại chuyển đổi hiển thị với 3 tùy chọn liên kết:
   - **Liên hệ (Contact):** Nâng cấp bản ghi hiện tại thành Contact chính thức (giai đoạn `SQL` hoặc `Opportunity`).
   - **Doanh nghiệp (Account):** Chọn liên kết với một Doanh nghiệp đã có sẵn hoặc tự động tạo mới Doanh nghiệp từ tên công ty của Lead.
   - **Cơ hội Bán hàng (Deal):** Tùy chọn tạo ngay một Cơ hội bán hàng mới (nhập Tên Deal, Giá trị dự kiến, Phễu bán hàng và Giai đoạn khởi đầu).
3. Người dùng bấm "Xác nhận chuyển đổi".
4. **Hệ thống thực thi giao dịch nguyên tử (Atomic Transaction):** Cập nhật Contact, tạo/liên kết Account, tạo Deal, gán quyền sở hữu đồng nhất và chuyển hướng người dùng đến Cơ hội bán hàng vừa tạo.

**Quy tắc nghiệp vụ:**
- `BR-14.1 (Rollback toàn phần khi lỗi)`: Nếu bất kỳ bước nào trong giao dịch nguyên tử thất bại, toàn bộ giao dịch bị hủy (rollback). Không tạo Contact/Account/Deal ở trạng thái dang dở.
- `BR-14.2 (Hoàn tác Chuyển đổi — Undo Conversion) [Yêu cầu mới]`: Trong vòng **24 giờ** sau khi chuyển đổi thành công, Sales Manager trở lên được phép thực hiện **Hoàn tác Chuyển đổi (Undo Lead Conversion)** với điều kiện Deal vừa tạo chưa có bất kỳ hoạt động giao dịch thực tế nào (chưa có ghi chú, chưa chuyển stage, chưa đính kèm tài liệu). Khi hoàn tác: (a) Deal vừa tạo bị xóa mềm; (b) Account vừa tạo bị xóa mềm nếu chưa có Contact nào khác liên kết; (c) Contact trở về giai đoạn `Lead`; (d) Hệ thống ghi Audit Log đầy đủ.

---

### FEAT-31 — Phân bổ Khách hàng Tiềm năng Tự động (Lead Routing Engine) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khi Lead được tạo tự động từ các kênh kỹ thuật số (Website Form, API, Chatbot, Quảng cáo) mà không có "người tạo" trực tiếp, hệ thống tự động phân bổ người phụ trách (Owner) theo bộ quy tắc định sẵn.

**Actor:** Tiến trình Hệ thống, Quản lý Kinh doanh (cấu hình quy tắc), Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-31.1 (Phân bổ Round-robin)`: Khi không có quy tắc đặc biệt nào khớp, hệ thống phân bổ Lead theo vòng lần lượt (Round-robin) đều nhau cho tất cả thành viên Sales đang hoạt động trong nhóm.
- `BR-31.2 (Phân bổ theo Vùng địa lý)`: Nếu Lead có trường `country` hoặc `province`, ưu tiên phân bổ cho Sales quản lý vùng địa lý tương ứng (Territory-based).
- `BR-31.3 (Phân bổ theo Ngành nghề)`: Nếu Lead có trường `industry`, ưu tiên phân bổ cho Sales chuyên ngành tương ứng.
- `BR-31.4 (Fallback khi không khớp)`: Khi không có quy tắc nào khớp hoặc không có Sales khả dụng, Lead được đưa vào hàng đợi "Unassigned" và gửi thông báo cho Quản lý Kinh doanh phân công thủ công.
- `BR-31.5 (Quyền cấu hình)`: Chỉ Quản lý Kinh doanh và Quản trị viên Workspace mới được phép tạo, sửa và xóa quy tắc phân bổ.

---

### FEAT-32 — Theo dõi Nguồn gốc Khách hàng Tiềm năng (UTM Source Tracking) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Tự động ghi nhận nguồn gốc của mỗi Lead/Contact (kênh quảng cáo, chiến dịch marketing, từ khóa tìm kiếm) để đo lường hiệu quả Marketing ROI và tối ưu ngân sách.

**Actor:** Tiến trình Hệ thống (tự động ghi nhận), Nhân viên Marketing (xem báo cáo), Quản lý Marketing (phân tích ROI).

**Quy tắc nghiệp vụ:**
- `BR-32.1 (Trường nguồn gốc hệ thống)`: Mỗi Contact/Lead tự động ghi nhận: `leadSource` (Kênh chính: Website, Facebook Ads, Google Ads, Referral, Event, Cold Outreach, Import), `originalSourceDetail` (Chi tiết kênh con).
- `BR-32.2 (Lưu tham số UTM)`: Khi Lead được tạo qua form web hoặc API, hệ thống tự động ghi nhận và lưu trữ toàn bộ tham số UTM: `utm_source`, `utm_medium`, `utm_campaign`, `utm_content`, `utm_term`.
- `BR-32.3 (Không ghi đè nguồn gốc)`: Trường `leadSource` và các tham số UTM được ghi nhận 1 lần tại thời điểm tạo bản ghi và **không được phép ghi đè** sau đó (nguyên tắc First-touch Attribution). Người dùng chỉ có thể xem, không được phép sửa.
- `BR-32.4 (Quyền xem báo cáo UTM)`: Chỉ người dùng có vai trò Nhân viên Marketing trở lên mới được phép xem báo cáo phân tích nguồn gốc khách hàng và Revenue Attribution theo kênh.

---

## E. ĐIỂM TIỀM NĂNG & CHẤM ĐIỂM TỰ ĐỘNG (LEAD SCORING ENGINE)


### FEAT-15 — Động cơ Chấm điểm Tiềm năng Tự động (Lead Scoring Engine) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động tính toán điểm số tiềm năng (0 - 100 điểm) cho khách hàng dựa trên thuộc tính hồ sơ (Fit Score) và hành vi tương tác thực tế (Engagement Score).

**Actor:** Tiến trình Hệ thống.

**Quy tắc chấm điểm:**
- `BR-15.1 (Điểm hồ sơ - Profile Fit)`:
  - Có Email doanh nghiệp hợp lệ: +10 điểm.
  - Có Số điện thoại di động: +10 điểm.
  - Có chức danh quản lý (Director/VP/C-Level): +20 điểm.
  - Thuộc ngành nghề mục tiêu: +15 điểm.
- `BR-15.2 (Điểm tương tác - Engagement)`:
  - Mở email chiến dịch: +5 điểm / lần.
  - Nhấp vào liên kết trong email/tin nhắn: +10 điểm / lần.
  - Gửi tin nhắn qua Livechat/WhatsApp: +15 điểm.
  - Đặt lịch hẹn / Tham gia demo: +30 điểm.
- `BR-15.3 (Giới hạn Trần Điểm & Tần suất) [Yêu cầu mới]`: Tổng điểm tiềm năng được chuẩn hoá trong khoảng từ 0 đến 100 điểm (`0 <= Lead Score <= 100`). Điểm cộng tương tác cho mỗi loại hành vi (như mở email, nhấp link) được giới hạn tối đa 1 lần cộng điểm / ngày / loại hành vi cho mỗi khách hàng để chống gian lận điểm số.

---

### FEAT-16 — Cơ chế Suy giảm Điểm Tiềm năng theo Thời gian (Score Decay Engine) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khách hàng không có tương tác trong một khoảng thời gian sẽ bị tự động giảm điểm tiềm năng để phản ánh độ nguội của cơ hội.

**Actor:** Tiến trình Hệ thống (Daily Cron).

**Quy tắc nghiệp vụ:**
- `BR-16.1`: Tiến trình hệ thống chạy lúc 02:00 hằng ngày kiểm tra: Nếu khách hàng không có bất kỳ tương tác nào trong vòng **14 ngày**, điểm tương tác giảm **10%** trên điểm tương tác hiện có (`engagementScore = Math.floor(engagementScore * 0.9)`). Mỗi chu kỳ 14 ngày không tương tác chỉ bị trừ 1 lần cho tới khi chạm mốc 30 ngày.
- `BR-16.2`: Nếu không có tương tác trong vòng **30 ngày**, điểm tương tác giảm tiếp **25%** (`engagementScore = Math.floor(engagementScore * 0.75)`).
- `BR-16.3`: Điểm tiềm năng không bao giờ bị âm (sàn là 0 điểm).

---

## F. XỬ LÝ TRÙNG LẶP & GỘP BẢN GHI AN TOÀN (DEDUPLICATION, MERGE & UNMERGE)

### FEAT-17 — Tự động Nhận diện & Kiểm tra Trùng lặp Khách hàng (Duplicate Check) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động cảnh báo trùng lặp theo thời gian thực khi người dùng đang nhập thông tin hoặc cung cấp công cụ quét trùng lặp toàn hệ thống.

**Actor:** Mọi người dùng tạo/sửa Contact, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-17.1 (Tiêu chí trùng lặp)`: Trùng khớp chính xác Địa chỉ Email, hoặc Số điện thoại (chuẩn hoá E.164), hoặc trùng khớp Họ tên kết hợp Tên công ty.
- `BR-17.2 (Cảnh báo tức thì)`: Khi gõ email/SĐT trong form tạo mới, hệ thống tự động gọi API `/api/v1/contacts/check-duplicate` để hiển thị cảnh báo viền vàng kèm liên kết đến bản ghi đã có.

---

### FEAT-18 — Xem trước Tác động Gộp Bản ghi (Merge Preview & Impact Analysis) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Trước khi thực hiện gộp hai bản ghi, hệ thống cung cấp màn hình Xem trước (Preview Merge) hiển thị rõ ràng giá trị nào sẽ được giữ lại, giá trị nào bị ghi đè và số lượng bản ghi con (Deals, Tickets, Tasks, Notes) sẽ được chuyển giao.

**Actor:** Quản trị viên Workspace, Người có quyền `delete` trên Contacts/Accounts.

**Quy tắc nghiệp vụ:**
- `BR-18.1`: Gọi API `/api/v1/contacts/:id/merge-preview?targetId=...` để tính toán chính xác bảng so sánh hai cột của Bản ghi A và Bản ghi B.
- `BR-18.2`: Cho phép người dùng chủ động chọn từng trường dữ liệu cụ thể muốn giữ lại từ bản ghi A hay bản ghi B trước khi bấm gộp.

---

### FEAT-19 — Gộp Khách hàng Cá nhân & Doanh nghiệp An toàn (Merge Contacts/Accounts) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Thực thi giao dịch gộp hai bản ghi trùng lặp: Giữ lại Bản ghi Chính (Master Record), chuyển toàn bộ bản ghi con sang Bản ghi Chính, xóa mềm Bản ghi Phụ (Losing Record) và ghi nhận Sổ cái Hoàn tác.

**Actor:** Quản trị viên Workspace, Người có quyền `delete` trên Contacts/Accounts.

**Quy tắc nghiệp vụ:**
- `BR-19.1 (Quyền hạn bắt buộc)`: Thao tác Gộp bắt buộc yêu cầu quyền `delete` (bởi vì bản ghi phụ sẽ bị xóa sau khi gộp).
- `BR-19.2 (Chuyển giao toàn bộ dữ liệu liên quan)`: Toàn bộ Notes, Tasks, Tickets, Deals, Lịch sử Hội thoại và Mối quan hệ của bản ghi phụ được tự động tái liên kết (re-parented) sang Bản ghi Chính.
- `BR-19.3 (Ghi nhận Sổ cái Gộp)`: Tạo bản ghi sổ cái trong `contact_merges` ghi nhận chi tiết: `masterContactId`, `mergedContactId`, ảnh chụp dữ liệu gốc (snapshot) của bản ghi phụ và người thực hiện gộp.
- `BR-19.4 (Xử lý Xung đột Vai trò Liên hệ trên Deal) [Yêu cầu mới]`: Khi gộp hai Contact mà cả hai đều đang tham gia vào cùng một Deal với các vai trò mua hàng khác nhau (Contact Roles on Deals), hệ thống giữ lại vai trò có thứ bậc ưu tiên cao nhất theo chuẩn: `Decision Maker` > `Champion` > `Technical Evaluator` > `Influencer` > `Purchaser`.

---

### FEAT-20 — Sổ cái Hoàn tác Gộp Bản ghi (Unmerge Contacts/Accounts Ledger) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép khôi phục lại trạng thái ban đầu trước khi gộp nếu phát hiện thao tác gộp nhầm lẫn.

**Actor:** Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-20.1`: Người dùng truy cập Lịch sử gộp bản ghi (`GET /api/v1/contacts/:id/merge-history`), bấm nút **"Hoàn tác gộp" (Unmerge)** (`POST /api/v1/contacts/merges/:mergeId/unmerge`).
- `BR-20.2`: Hệ thống hồi sinh bản ghi phụ đã bị xóa mềm, trả lại các trường dữ liệu theo snapshot trong sổ cái và hoàn trả lại các bản ghi con nguyên thủy về đúng chủ sở hữu cũ.

---

### FEAT-21 — Khôi phục Tự động Giao dịch Gộp Bị Lỗi (Recover Failed Merge) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Công cụ hỗ trợ xử lý sự cố khi một giao dịch gộp bị gián đoạn giữa chừng do mất kết nối mạng hoặc lỗi cơ sở dữ liệu.

**Actor:** Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-21.1`: Cung cấp API `/api/v1/contacts/merges/:mergeId/recover` cho phép hoàn tất nốt các bước chuyển giao bản ghi con còn dang dở hoặc tự động rollback về trạng thái an toàn.

---

## G. NHẬP DỮ LIỆU THÔNG MINH QUA HÀNG ĐỢI (SMART BULK IMPORT)

### FEAT-22 — Tải lên & Tiếp nhận Tệp Nhập khẩu Excel/CSV Dung lượng lớn (50MB) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hỗ trợ tải lên tệp danh bạ Excel (.xlsx) hoặc CSV dung lượng tối đa **50 MB** để nhập khẩu hàng loạt vào hệ thống.

**Actor:** Quản trị viên Workspace, Người có quyền `import` trên Contacts/Accounts.

**Quy tắc nghiệp vụ:**
- `BR-22.1`: Tệp tải lên được lưu tạm vào ổ đĩa bền vững và đẩy tác vụ vào hàng đợi BullMQ để xử lý bất đồng bộ, không giữ tệp trên bộ nhớ heap của máy chủ API.
- `BR-22.2`: Giới hạn dung lượng tối đa 50MB (`IMPORT_MAX_FILE_BYTES`).

---

### FEAT-23 — Trợ lý Tự động Ánh xạ Cột Dữ liệu (Auto Field Mapping Wizard) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Giao diện trực quan tự động quét các tiêu đề cột trong tệp Excel/CSV và đề xuất ánh xạ chính xác với các trường dữ liệu tương ứng trong CRM (Họ tên, SĐT, Email, Tên công ty, Chức danh, Địa chỉ).

**Actor:** Người dùng thực hiện Nhập dữ liệu.

**Quy tắc nghiệp vụ:**
- `BR-23.1`: Tự động nhận diện các tiêu đề cột phổ biến bằng cả tiếng Việt, tiếng Ả Rập và tiếng Anh (ví dụ: "Số điện thoại", "Phone", "Mobile", "Email", "Họ tên", "Full Name").
- `BR-23.2`: Cho phép người dùng chỉnh sửa ánh xạ thủ công hoặc bỏ qua các cột không cần nhập.
- `BR-23.3`: Cho phép chọn chiến lược xử lý trùng lặp khi nhập: **Bỏ qua bản ghi trùng**, **Cập nhật đè dữ liệu mới vào bản ghi cũ** hoặc **Tạo bản ghi mới**.
- `BR-23.4 (Lookup Key bắt buộc khi Cập nhật) [Yêu cầu mới]`: Khi người dùng chọn chiến lược "Cập nhật đè dữ liệu cũ", hệ thống **bắt buộc** người dùng phải chọn trường định danh (Lookup Key) để tìm bản ghi cũ. Các tùy chọn Lookup Key: (a) **Contact ID nội bộ** (ưu tiên cao nhất, chính xác nhất); (b) **Địa chỉ Email**; (c) **Số điện thoại**. Nếu không có dòng nào khớp Lookup Key, hệ thống xử lý dòng đó như "Tạo mới" và đánh dấu cảnh báo trong báo cáo kết quả.

---

### FEAT-24 — Xử lý Nhập khẩu Hàng đợi & Xuất Báo cáo Lỗi Chi tiết (Import Error Report) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tiến trình worker xử lý tệp theo từng khối (chunking), kiểm tra tính hợp lệ từng dòng, nhập dữ liệu hợp lệ và xuất tệp báo cáo chi tiết các dòng bị lỗi để người dùng sửa đổi.

**Actor:** Tiến trình Hệ thống, Người dùng thực hiện Nhập dữ liệu.

**Quy tắc nghiệp vụ:**
- `BR-24.1 (Theo dõi tiến trình)`: Cung cấp API theo dõi trạng thái tiến trình thời gian thực (`GET /api/v1/contacts/import-status/:jobId`) hiển thị số dòng đã xử lý, số dòng thành công, số dòng lỗi.
- `BR-24.2 (Tệp báo cáo lỗi)`: Nếu có dòng bị lỗi (sai định dạng email, thiếu tên bắt buộc), hệ thống tạo tệp báo cáo lỗi kèm cột nguyên nhân cụ thể và cấp mã token an toàn để người dùng tải về sửa chữa (`GET /api/v1/contacts/import-report/:token`).

---

## H. XUẤT DỮ LIỆU & DANH SÁCH HIỂN THỊ TÙY CHỈNH (EXPORT & LIST VIEWS)

### FEAT-25 — Xuất Dữ liệu Khách hàng Dạng Luồng An toàn qua Token (Streaming Export) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép xuất danh sách khách hàng ra tệp CSV theo các bộ lọc tùy chỉnh với cơ chế truyền luồng (Streaming) chống nghẽn bộ nhớ và bảo vệ tải về bằng token an toàn dùng 1 lần.

**Actor:** Quản trị viên Workspace, Người có quyền `export` trên Contacts/Accounts.

**Quy tắc nghiệp vụ:**
- `BR-25.1`: Xuất dữ liệu qua hàng đợi bất đồng bộ, hỗ trợ xuất hàng chục nghìn bản ghi mà không làm chậm hệ thống.
- `BR-25.2`: Đường dẫn tải về được bảo vệ bằng mã token bảo mật có thời hạn 24 giờ (`GET /api/v1/contacts/export-download/:token`), mã hóa tên tệp theo chuẩn RFC 5987 để chống tấn công Header Injection.

---

### FEAT-26 — Quản lý & Tích hợp Danh sách Hiển thị Dùng chung (Shared List Views) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tích hợp với phân hệ List Views để hiển thị danh bạ theo các bộ lọc thông minh: "Khách hàng của tôi", "Khách hàng tiềm năng mới trong tuần", "Khách hàng VIP", "Khách hàng chưa có hoạt động trong 30 ngày".

**Actor:** Mọi người dùng trong Workspace.

**Quy tắc nghiệp vụ:**
- `BR-26.1`: Hỗ trợ lưu trữ cấu hình cột hiển thị, bộ lọc điều kiện kết hợp (AND/OR) và thứ tự sắp xếp.

---

## I. DÒNG THỜI GIAN 360 & NGỮ CẢNH KHÁCH HÀNG (TIMELINE & CONTEXT)

### FEAT-27 — Dòng thời gian Hoạt động Hợp nhất 360 độ (Unified Customer Timeline) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Một nguồn cấp dữ liệu duy nhất (`GET /api/v1/contacts/:id/timeline`) tập hợp toàn bộ mọi sự kiện liên quan đến khách hàng theo thứ tự thời gian đảo ngược.

**Actor:** Mọi người dùng có quyền xem Contact.

**Chi tiết các nguồn sự kiện hợp nhất:**
- **Ghi chú (Notes):** Các nội dung trao đổi nội bộ của nhân viên.
- **Vé hỗ trợ (Tickets):** Các yêu cầu hỗ trợ kỹ thuật và khiếu nại của khách hàng.
- **Cơ hội bán hàng (Deals):** Các cơ hội đang mở và lịch sử chuyển giai đoạn bán hàng.
- **Nhiệm vụ & Lịch hẹn (Tasks & Events):** Các cuộc gọi, cuộc họp, công việc cần làm.
- **Hội thoại Đa kênh (Conversations):** Lịch sử các phiên chat qua Livechat, WhatsApp, Zalo, Facebook.
- **Thay đổi Trạng thái:** Lịch sử đổi giai đoạn vòng đời, gộp bản ghi, thay đổi người phụ trách.

---

### FEAT-28 — Cung cấp Ngữ cảnh Khách hàng 1 Chạm cho Omni Inbox (Customer Context API) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp API chuyên biệt siêu nhanh (`GET /api/v1/contacts/:id/customer-context`) phục vụ riêng cho thanh panel thông tin khách hàng trong Hộp thư Omni-channel Inbox, giúp tư vấn viên nắm trọn vẹn thông tin khách hàng trong 1 round-trip duy nhất.

**Actor:** Nhân viên Hỗ trợ Khách hàng, Tư vấn viên Livechat.

**Quy tắc nghiệp vụ:**
- `BR-28.1`: Trả về đồng thời: Thông tin liên hệ, Doanh nghiệp trực thuộc, Giai đoạn vòng đời, 3 Cơ hội bán hàng gần nhất, 3 Vé hỗ trợ gần nhất và Ghi chú ghim đầu trang.
- `BR-28.2`: Thời gian phản hồi API tối ưu dưới **50ms** qua cơ chế truy vấn song song có kiểm soát phân quyền.

---

## J. ĐỊNH DANH ĐA KÊNH, ĐỒNG THUẬN & KHẢ NĂNG TIẾP CẬN

### FEAT-29 — Quản lý Danh tính Đa kênh & Trạng thái Tiếp cận (Identities & Deliverability) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Quản lý toàn bộ các kênh định danh có thể tiếp cận của khách hàng (Email cá nhân, Email công việc, SĐT di động, SĐT bàn, WhatsApp ID, Facebook PSID, Zalo ID) dưới dạng các bản ghi danh tính độc lập.

**Actor:** Mọi người dùng có quyền quản lý Contact.

**Quy tắc nghiệp vụ:**
- `BR-29.1`: Mỗi loại kênh có duy nhất 1 định danh chính (`isPrimary = true`).
- `BR-29.2`: Theo dõi trạng thái kỹ thuật: `VERIFIED` (Đã gửi nhận thành công), `BOUNCED` (Email hỏng / SĐT không tồn tại), `UNVERIFIED` (Chưa kiểm tra).
- `BR-29.3`: Khi một địa chỉ bị đánh dấu `BOUNCED`, hệ thống tự động loại trừ địa chỉ đó khỏi các chiến dịch gửi email/tin nhắn tự động để bảo vệ uy tín tên miền (Domain Reputation).

---

### FEAT-30 — Quản lý Trạng thái Đồng thuận Tiếp thị & Định danh Dùng chung (Consent) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Quản lý trạng thái đồng ý nhận tin tiếp thị (Opt-in Consent) theo chuẩn quốc tế và gắn nhãn Định danh dùng chung (Shared Identifier).

**Actor:** Nhân viên Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-30.1 (Đồng thuận & Hủy nhận tin theo từng Kênh)`: Lưu trữ trạng thái `OPT_IN` / `OPT_OUT` độc lập cho từng kênh truyền thông: `emailOptOut`, `smsOptOut`, `whatsappOptOut`, `zaloOptOut`. Khi khách hàng hủy nhận tin trên một kênh (ví dụ Email), hệ thống chỉ chặn kênh đó mà không làm ảnh hưởng tới sự đồng thuận trên các kênh khác (Zalo/SMS), trừ khi khách hàng chọn "Hủy nhận tin trên toàn bộ mọi kênh" (`PATCH /api/v1/contacts/:id/identities/:identityId/consent`).
- `BR-30.2 (Định danh dùng chung - Shared Identifier)`: Cho phép đánh dấu một số điện thoại/email là "Định danh dùng chung" (ví dụ số tổng đài công ty, số lễ tân) để ngăn chặn việc hệ thống tự động gộp nhầm các khách hàng khác nhau dùng chung số điện thoại đó vào cùng một hồ sơ.

---

## 4. Yêu cầu phi chức năng

### 4.1 Hiệu năng & Khả năng đáp ứng (Performance)
- **NFR-01 (Thời gian tìm kiếm & lọc danh bạ):** Tìm kiếm khách hàng theo tên, email, SĐT hoặc lọc theo danh sách phản hồi dưới **300ms** (p95) trên tập dữ liệu 1,000,000 bản ghi.
- **NFR-02 (Thời gian tải Dòng thời gian 360 độ):** API Timeline và Customer Context phản hồi dưới **150ms** qua cơ chế đánh chỉ mục chuyên sâu.
- **NFR-03 (Tốc độ xử lý Nhập khẩu dữ liệu):** Worker nhập khẩu xử lý tối thiểu **1,000 dòng/giây** đối với tệp Excel/CSV dung lượng lớn.

### 4.2 Độ tin cậy & Toàn vẹn Dữ liệu (Reliability & ACID)
- **NFR-04 (Giao dịch Gộp & Hoàn tác nguyên tử):** Thao tác Gộp (Merge) và Hoàn tác gộp (Unmerge) thực thi trong MongoDB Database Transaction duy nhất. Nếu có lỗi ở bất kỳ bước chuyển giao nào, toàn bộ giao dịch phải được rollback 100%.
- **NFR-05 (Bảo toàn Sổ cái Gộp):** Bản ghi sổ cái trong `contact_merges` được lưu trữ vĩnh viễn và không bị xóa kể cả khi bản ghi chính bị xóa mềm.

### 4.3 An toàn & Bảo mật (Security)
- **NFR-06 (Bảo vệ dữ liệu nhạy cảm FLS):** Áp dụng nghiêm ngặt chính sách bảo mật cấp trường (Field-Level Security). Người không có quyền `unmask` chỉ thấy dữ liệu bị che giấu.
- **NFR-07 (Nhật ký kiểm toán truy cập):** Mọi thao tác Mở khóa mặt nạ (Unmask), Xuất dữ liệu (Export), Gộp bản ghi (Merge) và Xóa bản ghi (Delete) bắt buộc được ghi nhật ký kiểm toán không thể sửa đổi (Immutable Audit Log).

---

## 5. Ma trận quyền truy cập tính năng

| Mã FEAT | Tên tính năng nghiệp vụ | Nhân viên (Sales Rep) | Nhân viên Hỗ trợ | Quản lý (Sales Mgr) | Quản trị viên (Admin) | Chủ sở hữu (Owner) |
| --- | --- | :---: | :---: | :---: | :---: | :---: |
| `FEAT-01` | Tạo & Quản lý Contact | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-02` | Xem Hồ sơ Chi tiết 360 | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-03` | Gắn nhãn Thẻ hàng loạt | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-04` | Mở khóa Mặt nạ Dữ liệu | Có quyền* | Có quyền* | Có quyền* | **Toàn quyền** | **Toàn quyền** |
| `FEAT-05` | Thùng rác & Phục hồi Contact | — | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-06` | Tạo & Quản lý Account | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-07` | Cây Doanh nghiệp Mẹ - Con | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-08` | Xem Chi tiết Doanh nghiệp | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-09` | Thùng rác & Phục hồi Account | — | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-10` | Quan hệ Đa Doanh nghiệp | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-11` | Quan hệ Giữa các Cá nhân | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-12` | Quản trị Vòng đời Khách hàng | Scope gán | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-13` | Xem Lịch sử Giai đoạn | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-14` | Chuyển đổi Lead 1-Click | Scope gán | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-15` | Động cơ Chấm điểm Lead | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-16` | Suy giảm Điểm Tiềm năng | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-17` | Kiểm tra Trùng lặp | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-18` | Xem trước Tác động Gộp | — | — | Có quyền `delete` | **Cho phép** | **Cho phép** |
| `FEAT-19` | Thực thi Gộp Bản ghi | — | — | Có quyền `delete` | **Cho phép** | **Cho phép** |
| `FEAT-20` | Hoàn tác Gộp (Unmerge) | — | — | — | **Cho phép** | **Cho phép** |
| `FEAT-21` | Khôi phục Gộp Lỗi | — | — | — | **Cho phép** | **Cho phép** |
| `FEAT-22` | Tải tệp Nhập khẩu Excel | — | — | Có quyền `import`| **Cho phép** | **Cho phép** |
| `FEAT-23` | Trợ lý Ánh xạ Cột | — | — | Có quyền `import`| **Cho phép** | **Cho phép** |
| `FEAT-24` | Nhập khẩu & Báo cáo Lỗi | — | — | Có quyền `import`| **Cho phép** | **Cho phép** |
| `FEAT-25` | Xuất Dữ liệu CSV qua Token | — | — | Có quyền `export`| **Cho phép** | **Cho phép** |
| `FEAT-26` | Danh sách Hiển thị Dùng chung| **Cho phép** | **Cho phép** | **Cho phép** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-27` | Dòng thời gian 360 độ | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-28` | Customer Context cho Omni | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-29` | Quản lý Danh tính Đa kênh | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-30` | Quản lý Đồng thuận & Shared | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |

*\*Ghi chú: Quyền `unmask` yêu cầu vai trò được cấp quyền chuyên biệt `contacts:unmask`.*

---

## 6. Kịch bản chấp nhận tổng hợp (UAT)

### Kịch bản 1: Tạo mới Khách hàng Cá nhân & Tra cứu Hồ sơ 360 Độ
1. Nhân viên kinh doanh bấm "Thêm khách hàng", nhập Họ tên "Trần Thị Mai", Email `mai.tran@vinafoods.vn`, SĐT `0908123456`, Công ty "Công ty CP Thực phẩm Vina".
2. **Kỳ vọng:** Hệ thống lưu thành công, tự động chuẩn hoá SĐT thành `+84908123456`, gán giai đoạn `Lead`, tạo Đơn vị tổ chức theo phòng ban của nhân viên và mở màn hình Hồ sơ 360 độ hiển thị đầy đủ thông tin.

---

### Kịch bản 2: Quy trình Chuyển đổi Khách hàng Tiềm năng 1-Click (Lead Conversion)
1. Nhân viên thẩm định Lead "Trần Thị Mai" đã sẵn sàng mua hàng, bấm nút "Chuyển đổi Tiềm năng".
2. Trong hộp thoại chuyển đổi:
   - Chọn tạo Doanh nghiệp mới "Công ty CP Thực phẩm Vina".
   - Chọn tạo Cơ hội mới: "Hợp đồng Cung ứng Nông sản Q3", Giá trị dự kiến 500,000,000 VND, giai đoạn "Đề xuất báo giá".
3. Bấm "Xác nhận chuyển đổi".
4. **Kỳ vọng:** Hệ thống thực thi giao dịch nguyên tử: Nâng cấp Contact lên giai đoạn `Opportunity`, tạo Account "Công ty CP Thực phẩm Vina", tạo Deal trị giá 500 triệu và chuyển hướng nhân viên vào màn hình Deal vừa tạo.

---

### Kịch bản 3: Nhận diện Trùng lặp, Gộp Bản ghi & Hoàn tác Gộp (Unmerge)
1. Nhân viên tạo khách hàng mới với email `mai.tran@vinafoods.vn`. Hệ thống hiển thị cảnh báo viền vàng: "Khách hàng này đã tồn tại trong hệ thống!".
2. Quản trị viên mở công cụ Gộp bản ghi giữa Bản ghi A (cũ) và Bản ghi B (mới).
3. Xem trước (Preview Merge), chọn Bản ghi A làm Master Record và bấm "Xác nhận gộp".
4. **Kỳ vọng Gộp:** Bản ghi B bị xóa mềm, toàn bộ ghi chú và công việc của B chuyển sang A, Sổ cái `contact_merges` ghi nhận 1 dòng lịch sử.
5. Sau đó, Quản trị viên vào Lịch sử gộp, bấm nút "Hoàn tác gộp" (Unmerge).
6. **Kỳ vọng Hoàn tác:** Bản ghi B được khôi phục nguyên vẹn, các dữ liệu công việc cũ của B được trả về đúng vị trí ban đầu.

---

### Kịch bản 4: Nhập khẩu Danh bạ 10,000 dòng từ Excel có Tự động Ánh xạ & Báo cáo Lỗi
1. Quản trị viên tải lên tệp `Danh_sach_khach_hang_2026.xlsx` dung lượng 15MB chứa 10,000 dòng.
2. Trợ lý ánh xạ tự động nhận diện các cột: "Họ và tên" -> `name`, "Điện thoại" -> `phone`, "Email" -> `email`, "Công ty" -> `companyName`.
3. Bấm "Bắt đầu nhập khẩu".
4. **Kỳ vọng:** Worker xử lý ngầm, thanh tiến trình hiển thị 100%. Kết quả: 9,850 dòng thành công, 150 dòng lỗi (do sai định dạng email).
5. Quản trị viên bấm tải "Tệp báo cáo lỗi", mở tệp thấy rõ 150 dòng kèm cột "Lý do lỗi: Email không hợp lệ".

---

### Kịch bản 5: Thiết lập Mối quan hệ Đa Doanh nghiệp (Multi-Affiliations)
1. Khách hàng "Nguyễn Văn Hùng" là Tổng giám đốc tại "Công ty Đầu tư Hùng Cường", đồng thời là Thành viên HĐQT tại "Ngân hàng Thương mại Á Châu".
2. Nhân viên vào Hồ sơ ông Hùng → Tab "Doanh nghiệp trực thuộc" → bấm "Thêm liên kết".
3. Chọn công ty "Ngân hàng Thương mại Á Châu", nhập chức danh "Thành viên HĐQT", vai trò "Cố vấn cấp cao".
4. **Kỳ vọng:** Hồ sơ ông Hùng hiển thị đầy đủ 2 công ty công tác; đồng thời mở hồ sơ "Ngân hàng Thương mại Á Châu" thấy ông Hùng xuất hiện trong danh sách nhân sự cấp cao.

---

### Kịch bản 6: Bảo vệ Dữ liệu Nhạy cảm & Mở khóa Mặt nạ (Field Masking & Audit)
1. Nhân viên kinh doanh chưa có quyền `unmask` mở hồ sơ khách hàng "Trần Thị Mai".
2. **Kỳ vọng:** Số điện thoại hiển thị `090****567`, Email hiển thị `m***@vinafoods.vn`.
3. Quản lý kinh doanh có quyền `contacts:unmask` mở hồ sơ và bấm biểu tượng "Mắt".
4. **Kỳ vọng:** Số điện thoại giải mã hiển thị đầy đủ `0908123456`, hệ thống tự động ghi 1 bản ghi vào Nhật ký kiểm toán (Audit Trail) ghi nhận Quản lý đã xem số điện thoại của bà Mai lúc 14:30.

---

## 7. Giới hạn hiện tại & Vấn đề chính sách cần quyết định tiếp

1. **Chính sách Tự động Làm giàu Dữ liệu Doanh nghiệp (Third-Party Data Enrichment):**
   - *Vấn đề:* Có nên tích hợp API bên thứ ba (Tổng cục Thuế / Clearbit / ZoomInfo) để khi người dùng gõ Mã số thuế hoặc Tên miền website, hệ thống tự động điền Tên công ty, Địa chỉ trụ sở và Ngành nghề không?
   - *Đề xuất PM:* Đưa vào lộ trình giai đoạn tiếp theo; trước mắt hỗ trợ nhập liệu và kiểm tra trùng lặp theo mã số thuế.
2. **Quy tắc Giải quyết Xung đột khi Gộp Trường Đa trị (Multi-Value Conflict Strategy):**
   - *Vấn đề:* Khi gộp 2 khách hàng mà cả 2 đều có nhiều số điện thoại/email khác nhau, có nên giữ lại tất cả dưới dạng mảng hay chỉ giữ số chính?
   - *Đề xuất PM:* Mặc định giữ lại tất cả các email/SĐT hợp lệ thành danh sách định danh phụ, người dùng chỉ định 1 địa chỉ duy nhất làm `isPrimary`.
3. **Quy định Thời gian Tự động Dọn dẹp Thùng rác (Recycle Bin Retention Policy):**
   - *Vấn đề:* Thời gian lưu trữ bản ghi trong Thùng rác trước khi xóa vĩnh viễn là 30 ngày hay 90 ngày?
   - *Đề xuất PM:* Áp dụng chuẩn 30 ngày cho gói tiêu chuẩn và cho phép tùy biến lên 90 ngày cho gói Enterprise.
