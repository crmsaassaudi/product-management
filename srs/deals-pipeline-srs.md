# SRS — Phân hệ Quản lý Cơ hội & Phễu Bán hàng (Deals & Pipelines Management)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA (Version 2.0) |
| **Module** | CRM — Phân hệ Quản lý Cơ hội & Phễu Bán hàng (Deals & Pipelines Management) |
| **Ngày cập nhật** | 2026-08-28 |
| **Phiên bản** | v2.0 (Target Standard) |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`contacts-srs.md`](./contacts-srs.md), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`object-manager-srs.md`](./object-manager-srs.md) |

## Ghi chú về nguồn gốc tài liệu

Tài liệu này được xây dựng thông qua quy trình chuẩn hoá 4 bước:
1. **Khảo sát toàn diện mã nguồn thực tế (As-Is):** Khảo sát toàn bộ hệ thống API, schemas, Kanban board services, pipeline migration guards, follow-up notification engines, import/export processors và deal reports trong `crm-api` (`src/deals/`, `src/deal-settings/`, `src/reports/deal/`) và `crm-web` (`src/features/deals/`).
2. **Rà soát & Đối chiếu Chuyên sâu:** Kiểm tra chéo từng quy tắc di chuyển phễu (Pipeline Migration & Archival Guard), lưu vết thời gian tại từng giai đoạn (`durationMs`), cảnh báo cơ hội nguội lạnh (Stale Deals), phân vai trò liên hệ trong cơ hội (Contact Roles on Deals) và dự báo doanh số có trọng số.
3. **Chuẩn hoá Nghiệp vụ Business First:** Đối chiếu với các chuẩn mực B2B SaaS CRM hàng đầu thế giới (Pipedrive Visual Pipelines, HubSpot Deals & Revenue Forecast, Salesforce Opportunities & Contact Roles, Zoho CRM), loại bỏ các ràng buộc kỹ thuật thô và hoàn thiện quy trình bán hàng chuyên nghiệp.
4. **Đóng băng Đặc tả Mục tiêu:** Hoàn thiện bộ 30 tính năng nghiệp vụ cốt lõi, ma trận phân quyền, 6 kịch bản UAT và danh mục chính sách nghiệp vụ.

**Quy ước nhãn trạng thái:** Mỗi tính năng (FEAT) và quy tắc nghiệp vụ (BR) được gắn nhãn trạng thái:
- **`[Đã triển khai]`** — Phản ánh các tính năng nền tảng đã sẵn sàng và đang vận hành thực tế trong hệ thống.
- **`[Yêu cầu mới]`** — Các tính năng và quy tắc nâng cấp chuẩn Business To-Be được bổ sung để hoàn thiện trải nghiệm quản trị bán hàng toàn diện.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả chi tiết toàn bộ nghiệp vụ quản trị Cơ hội bán hàng (Deals) và Phễu bán hàng (Pipelines) trong hệ thống CRM B2B SaaS:
1. **Quản trị Cơ hội Bán hàng (Deals Management):** Thu thập, theo dõi giá trị tiền tệ, ngày dự kiến chốt (Expected Close Date) và tiến trình thương lượng của từng giao dịch.
2. **Nhiều Phễu Bán hàng Linh hoạt (Multiple Pipelines):** Cho phép doanh nghiệp quản trị song song nhiều quy trình bán hàng độc lập cho các dòng sản phẩm, dịch vụ hoặc thị trường khác nhau.
3. **Bảng Kanban Trực quan (Visual Kanban Board):** Không gian làm việc trực quan cho nhân viên kinh doanh kéo thả cơ hội qua các giai đoạn, tự động cập nhật tổng giá trị và số lượng cơ hội theo thời gian thực.
4. **Đo lường Thời gian tại Giai đoạn & Vận tốc Bán hàng (Sales Velocity & Bottlenecks):** Đo lường chính xác số ngày cơ hội nằm yên tại từng bước để phát hiện điểm nghẽn và tối ưu hóa chu kỳ bán hàng.
5. **Cảnh báo Cơ hội Nguội & Nhắc nhở Chăm sóc (Follow-up Reminders & Stale Deal Detection):** Tự động phát hiện các cơ hội không có hoạt động trong thời gian dài và phát sinh thông báo nhắc nhở chăm sóc trước hạn chót.
6. **Quản lý Thất bại & Phân tích Thắng/Thua (Loss Reasons & Win/Loss Analysis):** Bắt buộc nhập lý do thất bại chuẩn hóa khi đóng deal thất bại để phục vụ phân tích cải tiến sản phẩm và chiến lược giá.
7. **Vai trò Liên hệ trong Giao dịch (Contact Roles on Deals):** Quản lý ma trận nhân sự tham gia vào quá trình mua hàng của khách hàng (Người ra quyết định, Người đánh giá kỹ thuật, Người bảo trợ).
8. **Dự báo Doanh thu Bán hàng (Revenue Forecasting & Weighted Pipeline):** Dự báo doanh thu kỳ vọng theo xác suất thành công của từng giai đoạn và đo lường tỷ lệ chuyển đổi toàn diện.

### 1.2 Phạm vi

Tài liệu bao gồm 10 nhóm chức năng cốt lõi:
- **Nhóm A: Quản trị Cơ hội Bán hàng (Deals):** Tạo mới, cập nhật, chi tiết 360 độ, giá trị tiền tệ, ngày dự kiến đóng, mặt nạ dữ liệu nhạy cảm và Thùng rác phục hồi.
- **Nhóm B: Phễu Bán hàng & Giai đoạn (Multiple Pipelines & Stages):** Quản trị nhiều phễu độc lập, cấu hình giai đoạn & xác suất win, sắp xếp thứ tự giai đoạn, lưu trữ & di chuyển phễu an toàn.
- **Nhóm C: Bảng Kanban Cơ hội Trực quan (Deals Kanban Board):** Hiển thị thẻ cơ hội phân cột, tính tổng giá trị & số lượng thời gian thực, kéo thả chuyển giai đoạn, phân trang Keyset mượt mà.
- **Nhóm D: Lịch sử Giai đoạn & Vận tốc Bán hàng (Stage Duration & Sales Velocity):** Ghi nhận thời gian lưu tại từng giai đoạn (`durationMs`), phân tích điểm nghẽn phễu bán hàng.
- **Nhóm E: Chăm sóc & Cảnh báo Cơ hội Nguội (Follow-up Reminders & Stale Deals):** Lịch hẹn chăm sóc tiếp theo (`nextFollowUpAt`), cảnh báo cơ hội nguội (>14 ngày), tự động chạm hoạt động.
- **Nhóm F: Quản lý Thất bại & Phân tích Thắng/Thua (Loss Reasons & Win/Loss Analysis):** Bắt buộc nhập lý do thất bại khi Closed Lost, phân tích tỷ lệ thắng/thua theo nguồn và đối thủ.
- **Nhóm G: Vai trò Liên hệ & Nguồn gốc Giao dịch (Contact Roles & Attribution):** Gắn nhiều liên hệ với vai trò (Decision Maker, Champion, Influencer), theo dõi nguồn gốc chiến dịch (UTM Attribution, Deal Sources).
- **Nhóm H: Dự báo Doanh thu & Báo cáo Bán hàng (Revenue Forecasting & Reports):** Doanh thu có trọng số (Weighted Pipeline), Báo cáo tỷ lệ chuyển đổi từng giai đoạn, Bảng xếp hạng nhân viên kinh doanh.
- **Nhóm I: Thao tác Hàng loạt & Nhập/Xuất Dữ liệu (Bulk Operations, Import/Export):** Cập nhật/gắn thẻ/xóa hàng loạt, Import Excel 50MB qua BullMQ có báo cáo lỗi, Export CSV bảo mật qua token.
- **Nhóm J: Dòng thời gian Hoạt động & Tích hợp Vé Hỗ trợ (Timeline & Support Linkage):** Dòng thời gian trao đổi, ghi chú, cuộc gọi, liên kết vé hỗ trợ phát sinh từ deal.

**Ngoài phạm vi (thuộc về các tài liệu SRS chuyên biệt khác):**
- **Nghiệp vụ Quản trị Hồ sơ Khách hàng & Doanh nghiệp:** Thuộc về [`contacts-srs.md`](./contacts-srs.md).
- **Nghiệp vụ Quản lý Giao tiếp Đa kênh & Hộp thư chung:** Thuộc về [`omnichat-srs.md`](./omnichat-srs.md).
- **Chi tiết Bảng giá Gói cước & Thanh toán Thuê bao:** Thuộc về [`billing-subscription-srs.md`](./billing-subscription-srs.md).

### 1.3 Đối tượng đọc

- **Product Owner / Business Analyst:** Nguồn tài liệu đặc tả chuẩn mực để quản lý backlog và nghiệm thu tính năng phễu bán hàng.
- **Đội ngũ Phát triển (Frontend / Backend):** Căn cứ thiết kế API, schemas, thuật toán tính toán dự báo doanh số và trải nghiệm bảng kéo thả Kanban.
- **Đội ngũ Kiểm thử (QA/QC):** Thiết kế bộ kịch bản kiểm thử tích hợp đầu cuối cho toàn bộ quy trình bán hàng.
- **Giám đốc Kinh doanh & Đội ngũ Bán hàng (Sales Team):** Nắm rõ các quy tắc vận hành phễu bán hàng, quy chuẩn chốt deal và cơ chế đo lường chỉ số KPI bán hàng.

### 1.4 Thuật ngữ & Viết tắt

| Thuật ngữ | Định nghĩa nghiệp vụ |
| --- | --- |
| **Cơ hội Bán hàng (Deal / Opportunity)** | Giao dịch kinh doanh tiềm năng giữa doanh nghiệp và khách hàng với giá trị tiền tệ và thời hạn chốt cụ thể. |
| **Phễu Bán hàng (Sales Pipeline)** | Quy trình gồm các giai đoạn tuần tự từ khi tiếp nhận cơ hội đến khi chốt hợp đồng. |
| **Xác suất Thành công (Win Probability)** | Tỷ lệ % khả năng thành công được gán cho từng giai đoạn để tính toán dự báo doanh thu. |
| **Bảng Kanban Cơ hội (Deals Kanban Board)** | Giao diện dạng cột thẻ trực quan hỗ trợ kéo thả cơ hội giữa các giai đoạn bán hàng. |
| **Thời gian tại Giai đoạn (Time in Stage)** | Số ngày/giờ một cơ hội nằm yên tại một giai đoạn cụ thể trên phễu. |
| **Cơ hội Nguội Lạnh (Stale Deal)** | Cơ hội không có bất kỳ tương tác nào vượt quá số ngày quy định (ví dụ >14 ngày). |
| **Doanh thu Dự báo có Trọng số (Weighted Forecast)** | Doanh thu kỳ vọng tính bằng: $\sum (\text{Giá trị Cơ hội} \times \text{Xác suất Giai đoạn})$. |
| **Lý do Thất bại (Loss Reason)** | Nguyên nhân chuẩn hóa bắt buộc khai báo khi cơ hội bị đóng ở trạng thái thất bại. |
| **Vai trò Liên hệ (Contact Roles on Deals)** | Định vị vai trò của các nhân sự khách hàng tham gia vào quá trình mua hàng. |

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

Trong hoạt động kinh doanh B2B và bán lẻ giá trị cao, các doanh nghiệp thường gặp các vấn đề nghiêm trọng:
- Cơ hội bán hàng bị thất lạc hoặc không có lịch chăm sóc tiếp theo, dẫn đến tỷ lệ rớt khách hàng cao.
- Không nắm được giá trị doanh thu dự kiến trong tương lai (Pipeline Visibility) để chủ động kế hoạch tài chính và nguồn lực.
- Không phát hiện được điểm nghẽn: Cơ hội bị tắc ở giai đoạn Báo giá hay giai đoạn Đàm phán? Mất bao nhiêu ngày để chuyển một deal từ Khảo sát sang Ký kết?
- Áp dụng một quy trình cứng nhắc cho mọi sản phẩm, trong khi bán hàng Dự án B2B cần quy trình khác hoàn toàn với bán hàng Bán lẻ/Dịch vụ ngắn hạn.
- Thiếu dữ liệu phân tích nguyên nhân vì sao khách hàng từ chối (do Giá, do Tính năng hay do Đối thủ cạnh tranh?).

Module Deals & Pipelines giải quyết toàn bộ các bài toán trên bằng cách cung cấp một hệ thống quản trị phễu bán hàng linh hoạt, giao diện Kanban mượt mà, công cụ cảnh báo cơ hội nguội tự động, dự báo doanh thu chính xác và phân tích nguyên nhân thắng/thua chi tiết.

### 2.2 Vai trò người dùng (Actor)

| Actor | Mô tả vai trò và quyền hạn |
| --- | --- |
| **Nhân viên Kinh doanh (Sales Representative)** | Tạo mới cơ hội, cập nhật giá trị, kéo thả chuyển giai đoạn trên bảng Kanban, ghi nhận nhật ký cuộc gọi/email và đặt lịch chăm sóc tiếp theo. |
| **Quản lý Kinh doanh (Sales Manager)** | Theo dõi bảng Kanban của toàn đội ngũ, duyệt di chuyển cơ hội giá trị lớn, phân tích báo cáo dự báo doanh thu và phân công cơ hội. |
| **Giám đốc Kinh doanh (VP of Sales / Head of Sales)** | Xem báo cáo hợp nhất toàn công ty, thiết lập mục tiêu doanh số, theo dõi vận tốc bán hàng và đánh giá hiệu quả từng phễu. |
| **Người cộng tác Cơ hội (Deal Collaborator — Pre-sales / Legal / Finance)** | Tham gia vào Cơ hội với quyền hạn giới hạn: Xem toàn bộ thông tin cơ hội, thêm ghi chú và đính kèm tài liệu. **Không được chuyển giai đoạn, không được thay đổi Giá trị hoặc Chiết khấu, không được xóa cơ hội.** Vai trò này phù hợp với Pre-sales Engineer, Bộ phận Pháp chế (Legal) và Phòng Tài chính khi cần tham gia vào Deal mà không có quyền Sales đầy đủ. |
| **Quản trị viên Không gian làm việc (Tenant Admin)** | Tạo mới và cấu hình nhiều phễu bán hàng, thiết lập giai đoạn & xác suất win, quản lý danh mục lý do thất bại và nguồn cơ hội. |
| **Chủ sở hữu Không gian làm việc (Tenant Owner)** | Toàn quyền cấu hình và xem toàn bộ các phễu bán hàng và báo cáo tài chính của tổ chức. |
| **Tiến trình Hệ thống (System Engine / Background Workers)** | Tự động quét gửi thông báo nhắc nhở chăm sóc (`DealFollowUpService`), kiểm tra cơ hội nguội và xử lý tệp import/export bất đồng bộ. |

### 2.3 Bảng tổng hợp 30 tính năng nghiệp vụ

| Nhóm | Mã FEAT | Tên tính năng nghiệp vụ | Trạng thái |
| --- | --- | --- | --- |
| **A. Quản trị Cơ hội Bán hàng** | `FEAT-01` | Tạo mới & Quản lý Cơ hội Bán hàng (Deal CRUD) | `[Đã triển khai]` |
| | `FEAT-02` | Hồ sơ Chi tiết Cơ hội 360 độ (Deal 360 View & Overview Panel) | `[Đã triển khai]` |
| | `FEAT-03` | Quản lý Tiền tệ, Giá trị & Ngày dự kiến chốt (Multi-Currency & Close Date) | `[Đã triển khai]` |
| | `FEAT-04` | Bảo vệ Dữ liệu Nhạy cảm Giá trị Giao dịch (Field Masking & FLS) | `[Đã triển khai]` |
| | `FEAT-05` | Thùng rác Cơ hội & Phục hồi Bản ghi (Deal Recycle Bin & Restore) | `[Đã triển khai]` |
| **B. Phễu Bán hàng & Giai đoạn** | `FEAT-06` | Quản trị Nhiều Phễu Bán hàng Độc lập (Multiple Pipelines Management) | `[Đã triển khai]` |
| | `FEAT-07` | Thiết lập Giai đoạn & Tỷ lệ Xác suất Thành công (Stages & Win Probability) | `[Đã triển khai]` |
| | `FEAT-08` | Sắp xếp Thứ tự Giai đoạn Linh hoạt (Reorder Pipeline Stages) | `[Đã triển khai]` |
| | `FEAT-09` | Lưu trữ & Di chuyển Cơ hội khi Đóng Phễu (Pipeline Archival & Migration Guard)| `[Đã triển khai]` |
| | `FEAT-10` | Xóa Giai đoạn Bán hàng & Di chuyển Cơ hội An toàn (Delete Stage Guard) | `[Đã triển khai]` |
| **C. Bảng Kanban Trực quan** | `FEAT-11` | Bảng Kanban Trực quan Tổng hợp Giá trị Thời gian thực (Board Summary API) | `[Đã triển khai]` |
| | `FEAT-12` | Phân trang Keyset Mượt mà theo Từng Cột Kanban (Board Column API) | `[Đã triển khai]` |
| | `FEAT-13` | Kéo thả Chuyển Giai đoạn Tức thì (Drag-and-Drop Stage Transition) | `[Đã triển khai]` |
| | `FEAT-14` | Bộ lọc Thông minh trên Bảng Kanban (Kanban Smart Filters) | `[Đã triển khai]` |
| **D. Lịch sử Giai đoạn & Vận tốc** | `FEAT-15` | Ghi nhận Chi tiết Lịch sử Giai đoạn & Thời gian Lưu (Stage Duration ms) | `[Đã triển khai]` |
| | `FEAT-16` | Phân tích Điểm nghẽn Quy trình & Vận tốc Bán hàng (Sales Velocity Analytics)| `[Yêu cầu mới]` |
| **E. Cảnh báo Nguội & Nhắc nhở** | `FEAT-17` | Thiết lập Lịch Chăm sóc Tiếp theo (Next Follow-up Scheduling) | `[Đã triển khai]` |
| | `FEAT-18` | Tiến trình Tự động Quét & Bắn Thông báo Nhắc nhở (Follow-up Due Sweep) | `[Đã triển khai]` |
| | `FEAT-19` | Tự động Nhận diện & Cảnh báo Cơ hội Nguội Lạnh (Stale Deal Detection) | `[Đã triển khai]` |
| | `FEAT-20` | Tự động Chạm Hoạt động Đánh thức Cơ hội (Touch Activity Mechanism) | `[Đã triển khai]` |
| **F. Quản lý Thắng/Thua & Thất bại** | `FEAT-21` | Quy trình Đóng Cơ hội Thành công (Mark as Closed Won) | `[Đã triển khai]` |
| | `FEAT-22` | Bắt buộc Khai báo Lý do Thất bại khi Đóng Thua (Mark as Closed Lost) | `[Đã triển khai]` |
| | `FEAT-23` | Quản lý Danh mục Lý do Thất bại & Nguồn Giao dịch (Loss Reasons & Sources) | `[Đã triển khai]` |
| **G. Vai trò Nhân sự & Nguồn gốc** | `FEAT-24` | Gắn Vai trò Nhân sự Khách hàng trong Cơ hội (Contact Roles on Deals) | `[Đã triển khai]` |
| | `FEAT-25` | Theo dõi Nguồn gốc Tiếp thị & Chiến dịch (Campaign UTM Attribution) | `[Đã triển khai]` |
| **H. Dự báo Doanh thu & Báo cáo** | `FEAT-26` | Động cơ Dự báo Doanh thu có Trọng số (Weighted Revenue Forecasting Engine) | `[Yêu cầu mới]` |
| | `FEAT-27` | Báo cáo Tỷ lệ Chuyển đổi Phễu & Bảng Xếp hạng Kinh doanh (Sales Leaderboard)| `[Đã triển khai]` |
| **I. Thao tác Hàng loạt & Nhập/Xuất** | `FEAT-28` | Thao tác Cập nhật, Gắn thẻ & Xóa Hàng loạt (Bulk Deal Operations) | `[Đã triển khai]` |
| | `FEAT-29` | Nhập / Xuất Danh sách Cơ hội Dung lượng lớn 50MB qua Hàng đợi (Import/Export)| `[Đã triển khai]` |
| **J. Dòng thời gian & Tích hợp** | `FEAT-30` | Dòng thời gian Hoạt động Hợp nhất & Liên kết Vé Hỗ trợ (Deal Linked Tickets)| `[Đã triển khai]` |
| **K. Quy trình Kiểm soát Tiến độ** | `FEAT-31` | Rào cản Điều kiện Chuyển Giai đoạn (Stage Entry Requirements / Stage-Gate Rules) | `[Yêu cầu mới]` |
| | `FEAT-32` | Trạng thái Tạm ngưng Cơ hội (On Hold Deal Status) | `[Yêu cầu mới]` |
| | `FEAT-33` | Quy trình Phê duyệt Chiết khấu Cơ hội (Discount Approval Workflow) | `[Yêu cầu mới]` |

---

## 3. Đặc tả yêu cầu chức năng

## A. QUẢN TRỊ CƠ HỘI BÁN HÀNG (DEALS MANAGEMENT)

### FEAT-01 — Tạo mới & Quản lý Cơ hội Bán hàng (Deal CRUD) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép nhân viên kinh doanh tạo mới cơ hội bán hàng, liên kết với Khách hàng cá nhân (Contacts) và Doanh nghiệp (Accounts), chỉ định Phễu bán hàng và Giai đoạn ban đầu.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-01.1 (Thông tin bắt buộc)`: Bắt buộc phải có Tên cơ hội (`title`), Phễu bán hàng (`pipelineId`) và Giai đoạn khởi đầu (`stageId`).
- `BR-01.2 (Giá trị, Sản phẩm & Tiền tệ mặc định)`: Giá trị (`value`) mặc định là `0`, loại tiền tệ (`currency`) mặc định theo cấu hình tiền tệ của workspace (ví dụ: `SAR`, `VND`, `USD`). Cho phép gắn Danh mục Sản phẩm/Dịch vụ (Line Items): Mỗi dòng lưu SKU, Tên sản phẩm, Số lượng, Đơn giá, Chiết khấu (%) và Thuế (%). Tổng giá trị Deal tự động tính bằng tổng thành tiền của toàn bộ các dòng sản phẩm (`value = sum(quantity * unitPrice * (1 - discount/100) * (1 + tax/100))`).
- `BR-01.3 (Tự động gán quyền sở hữu)`: Người tạo tự động được gán làm Người phụ trách (`ownerId`) và gán cờ `ownerAssignedExplicitly = true`. Đơn vị tổ chức được tự động gán theo phòng ban của người tạo (`orgUnitId`).
- `BR-01.4 (Liên kết tài khoản)`: Nếu tạo Deal gắn với một Doanh nghiệp (`accountId`), hệ thống tự động đồng bộ tên công ty (`accountName`) vào bản ghi Deal.

---

### FEAT-02 — Hồ sơ Chi tiết Cơ hội 360 độ (Deal 360 View) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Màn hình quản trị toàn diện của một cơ hội: Thanh chỉ báo giai đoạn bán hàng trên đỉnh (Stage Progress Bar), thông tin tài chính, liên hệ chính, các bên liên quan, dòng thời gian trao đổi, ghi chú, công việc và vé hỗ trợ liên quan.

**Actor:** Mọi người dùng có quyền xem Deal.

**Quy tắc nghiệp vụ:**
- `BR-02.1`: Cho phép đổi giai đoạn bán hàng chỉ bằng 1 nhấp chuột trực tiếp trên thanh tiến trình giai đoạn.
- `BR-02.2`: Hiển thị rõ số ngày cơ hội đã nằm ở giai đoạn hiện tại (`stageEnteredAt`) và ngày hoạt động gần nhất (`lastActivityAt`).

---

### FEAT-03 — Quản lý Tiền tệ, Giá trị & Ngày dự kiến chốt (Multi-Currency & Close Date) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Quản lý giá trị tiền tệ của giao dịch và ngày cam kết chốt hợp đồng (`closeDate`) để phục vụ dự báo tài chính.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh.

**Quy tắc nghiệp vụ:**
- `BR-03.1`: Hỗ trợ nhập giá trị tiền tệ dương. Khi chuyển đổi loại tiền tệ, hệ thống ghi nhận đúng mã ISO tiền tệ (`currency`).
- `BR-03.2`: Ngày dự kiến chốt (`closeDate`) được dùng làm căn cứ nhóm cơ hội vào các tháng/quý tài chính trong báo cáo dự báo doanh thu.
- `BR-03.3 (Quy đổi Tỷ giá Đa tiền tệ cho Báo cáo) [Yêu cầu mới]`: Hệ thống duy trì Bảng tỷ giá hối đoái đối với Đồng tiền Cơ sở (Base Currency) của không gian làm việc. Khi cơ hội sử dụng loại tiền tệ khác, hệ thống tự động quy đổi giá trị về Đồng tiền Cơ sở để tính toán tổng hợp Doanh thu Dự báo và Bảng xếp hạng doanh số.

---

### FEAT-04 — Bảo vệ Dữ liệu Nhạy cảm Giá trị Giao dịch (Field Masking & FLS) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Áp dụng chính sách bảo mật cấp trường (FLS) đối với trường Giá trị giao dịch (`value`) và Chiết khấu. Người dùng không có quyền xem tài chính sẽ thấy giá trị bị che giấu.

**Actor:** Người dùng có quyền `deals:view` kết hợp chính sách trường.

**Quy tắc nghiệp vụ:**
- `BR-04.1`: Tích hợp với `FieldPolicyInterceptor` của Object Manager, che giấu hoặc ẩn trường giá trị đối với các nhóm người dùng bị giới hạn quyền.

---

### FEAT-05 — Thùng rác Cơ hội & Phục hồi Bản ghi (Deal Recycle Bin & Restore) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi xóa một cơ hội, hệ thống thực hiện Xóa mềm (Soft Delete) và lưu trữ trong Thùng rác 30 ngày trước khi xóa vĩnh viễn.

**Actor:** Quản trị viên Workspace, Người có quyền `delete` trên Deals.

**Quy tắc nghiệp vụ:**
- `BR-05.1`: Đánh dấu `deletedAt = now()`, ẩn cơ hội khỏi bảng Kanban và báo cáo doanh số.
- `BR-05.2`: Cung cấp API phục hồi (`POST /api/v1/deals/:id/restore`) yêu cầu quyền `delete` và quyền xem lại bản ghi cũ theo ABAC, khôi phục lại thẻ cơ hội trên đúng cột Kanban cũ.

---

## B. PHỄU BÁN HÀNG & GIAI ĐOẠN (MULTIPLE PIPELINES & STAGES)

### FEAT-06 — Quản trị Nhiều Phễu Bán hàng Độc lập (Multiple Pipelines Management) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép doanh nghiệp tạo và quản lý nhiều phễu bán hàng độc lập (ví dụ: "Phễu Bán Phần Mềm SaaS", "Phễu Dịch Vụ Tư Vấn", "Phễu Khách Hàng Doanh Nghiệp Lớn").

**Actor:** Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-06.1 (Tên phễu & Phễu mặc định)`: Mỗi phễu có Tên riêng biệt và hệ thống luôn duy trì đúng 1 phễu làm Phễu Mặc định (`isDefault = true`).
- `BR-06.2 (Tra cứu nhanh)`: Toàn bộ nhân viên kinh doanh có quyền `deals:view` đều được phép đọc danh mục các phễu (`GET /api/v1/deal-settings/pipelines`) để chọn lựa khi thao tác.

---

### FEAT-07 — Thiết lập Giai đoạn & Tỷ lệ Xác suất Thành công (Stages & Win Probability) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép định nghĩa các giai đoạn tuần tự của từng phễu và gán tỷ lệ % xác suất thành công cho mỗi giai đoạn.

**Actor:** Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-07.1 (Tỷ lệ xác suất)`: Xác suất thành công (`probability`) là số nguyên từ `0%` đến `100%`.
- `BR-07.2 (Giai đoạn đóng)`: Mỗi phễu bắt buộc có 2 giai đoạn kết thúc chuẩn: **Thành công (`Closed Won` — 100% xác suất)** và **Thất bại (`Closed Lost` — 0% xác suất)**.

---

### FEAT-08 — Sắp xếp Thứ tự Giai đoạn Linh hoạt (Reorder Pipeline Stages) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép kéo thả thay đổi thứ tự hiển thị của các giai đoạn bán hàng trên phễu (`PATCH /api/v1/deal-settings/stages/reorder/:pipelineId`).

**Actor:** Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-08.1`: Cập nhật lại trường trọng số thứ tự (`order`) của toàn bộ các giai đoạn trong phễu theo mảng `stageIds` được gửi lên.

---

### FEAT-09 — Lưu trữ & Di chuyển Cơ hội khi Đóng Phễu (Pipeline Archival Guard) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi muốn đóng/ngừng sử dụng một phễu bán hàng (`DELETE /api/v1/deal-settings/pipelines/:id`), hệ thống bắt buộc quản trị viên phải chỉ định phương án xử lý cho các cơ hội đang mở bên trong phễu đó.

**Actor:** Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-09.1 (Hai phương án hợp lệ)`:
  - **Phương án 1 (Di chuyển sang phễu khác có Ma trận Ánh xạ):** Quản trị viên thực hiện thiết lập Ma trận Ánh xạ Giai đoạn (Stage Mapping Matrix) ghép từng giai đoạn của phễu cũ sang giai đoạn tương ứng của phễu mới (`migrateToPipelineId`). Toàn bộ cơ hội đang mở sẽ được di chuyển theo đúng giai đoạn tương ứng thay vì dồn về một giai đoạn duy nhất.
  - **Phương án 2 (Đóng băng chỉ đọc):** Chỉ định `keepReadOnly = true`. Toàn bộ các cơ hội đang mở trong phễu sẽ bị khóa ở trạng thái chỉ đọc để bảo toàn lịch sử.
- `BR-09.2 (Chặn xóa trống)`: Nếu không chỉ định 1 trong 2 phương án trên mà phễu vẫn còn cơ hội đang mở, hệ thống **bắt buộc từ chối lệnh xóa/lưu trữ**.

---

### FEAT-10 — Xóa Giai đoạn Bán hàng & Di chuyển Cơ hội An toàn (Delete Stage Guard) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi xóa một giai đoạn bán hàng (`DELETE /api/v1/deal-settings/stages/:id`), nếu giai đoạn đó đang chứa cơ hội bán hàng, bắt buộc quản trị viên phải chỉ định giai đoạn đích để tiếp nhận các cơ hội đó.

**Actor:** Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-10.1`: Truyền `targetStageId` trong body yêu cầu. Hệ thống tự động chuyển toàn bộ cơ hội sang giai đoạn đích trước khi xóa bỏ giai đoạn cũ.

---

## C. BẢNG KANBAN CƠ HỘI TRỰC QUAN (DEALS KANBAN BOARD)

### FEAT-11 — Bảng Kanban Trực quan Tổng hợp Giá trị Thời gian thực (Board Summary API) `[Đã triển khai]`

**Mô tả nghiệp vụ:** API chuyên biệt siêu tối ưu (`GET /api/v1/deals/board`) phục vụ hiển thị thanh tiêu đề của từng cột Kanban: Đếm chính xác tổng số lượng cơ hội và tổng giá trị tiền tệ của toàn bộ giai đoạn trong phễu theo thời gian thực.

**Actor:** Mọi người dùng có quyền xem Deal.

**Quy tắc nghiệp vụ:**
- `BR-11.1`: Tính toán tổng hợp (`$group` sum value & count) trực tiếp trên cơ sở dữ liệu có áp dụng bộ lọc phân quyền ABAC của người dùng, đảm bảo số liệu trên đầu cột luôn phản ánh chính xác 100% dữ liệu thực tế.

---

### FEAT-12 — Phân trang Keyset Mượt mà theo Từng Cột Kanban (Board Column API) `[Đã triển khai]`

**Mô tả nghiệp vụ:** API lấy danh sách các thẻ cơ hội cho từng cột riêng biệt (`GET /api/v1/deals/board/column?pipelineId=...&stageId=...`) hỗ trợ cuộn vô tận (Infinite Scroll) bằng cơ chế phân trang con trỏ (Keyset Cursor Pagination).

**Actor:** Mọi người dùng có quyền xem Deal.

**Quy tắc nghiệp vụ:**
- `BR-12.1`: Mỗi cột tải độc lập từng trang (mặc định 20 thẻ / lần), cuộn đến đâu tải thêm đến đó, không làm đơ gián đoạn trình duyệt khi phễu có hàng nghìn cơ hội.

---

### FEAT-13 — Kéo thả Chuyển Giai đoạn Tức thì (Drag-and-Drop Stage Transition) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép nhân viên kinh doanh dùng chuột hoặc thao tác chạm kéo thả một thẻ cơ hội từ cột này sang cột khác trên bảng Kanban để cập nhật tiến trình bán hàng tức thì.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh.

**Quy tắc nghiệp vụ:**
- `BR-13.1`: Kéo thả kích hoạt lệnh cập nhật giai đoạn (`PATCH /api/v1/deals/:id`).
- `BR-13.2`: Tự động cập nhật `stageId`, tính toán thời gian lưu tại giai đoạn cũ và ghi thêm 1 dòng vào lịch sử chuyển giai đoạn (`stageHistory`).

---

### FEAT-14 — Bộ lọc Thông minh trên Bảng Kanban (Kanban Smart Filters) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp thanh bộ lọc nhanh trên đầu bảng Kanban: Lọc theo Người phụ trách ("Cơ hội của tôi" / "Cơ hội của nhóm"), Lọc theo Thời gian dự kiến chốt (Tháng này, Quý này), Lọc theo Thẻ phân loại (Tags) và Lọc theo Trạng thái hoạt động (Đang hoạt động / Cơ hội nguội lạnh).

**Actor:** Mọi người dùng có quyền xem Deal.

**Quy tắc nghiệp vụ:**
- `BR-14.1`: Khi áp dụng bộ lọc, cả số liệu tổng trên đầu cột (Summary) và danh sách thẻ bên dưới (Column) đều được cập nhật đồng bộ tức thì.

---

## D. LỊCH SỬ GIAI ĐOẠN & VẬN TỐC BÁN HÀNG (STAGE DURATION & VELOCITY)

### FEAT-15 — Ghi nhận Chi tiết Lịch sử Giai đoạn & Thời gian Lưu (Stage Duration) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Mỗi khi một cơ hội chuyển giai đoạn, hệ thống tự động ghi lại thời điểm chuyển, người thực hiện và tính toán chính xác số mili-giây (`durationMs`) mà cơ hội đã lưu lại ở giai đoạn trước đó.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-15.1`: Cấu trúc bản ghi `stageHistory`: `{ fromStageId, toStageId, changedAt, changedById, durationMs }`.
- `BR-15.2`: Tính toán `durationMs` ngay tại thời điểm ghi dữ liệu (`durationMs = now - stageEnteredAt`), loại bỏ nhu cầu tính toán lặp lại khi xuất báo cáo phân tích.

---

### FEAT-16 — Phân tích Điểm nghẽn Quy trình & Vận tốc Bán hàng (Sales Velocity) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cung cấp biểu đồ phân tích thời gian trung bình cơ hội nằm ở từng bước trên phễu để nhà quản lý nhận diện ngay các điểm nghẽn (ví dụ: giai đoạn Đàm phán mất trung bình tới 28 ngày).

**Actor:** Quản lý Kinh doanh, Giám đốc Kinh doanh, Quản trị viên.

**Quy tắc nghiệp vụ:**
- `BR-16.1`: Báo cáo chỉ ra: Giai đoạn có thời gian lưu trung bình dài nhất, Tỷ lệ rơi rụng (Drop-off rate) cao nhất và Vận tốc chu kỳ bán hàng trung bình từ ngày mở đến ngày chốt thành công.

---

## E. CẢNH BÁO CƠ HỘI NGUỘI & NHẮC NHỞ CHĂM SÓC (FOLLOW-UP & STALE DEALS)

### FEAT-17 — Thiết lập Lịch Chăm sóc Tiếp theo (Next Follow-up Scheduling) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép nhân viên kinh doanh thiết lập thời điểm cam kết tương tác tiếp theo (`nextFollowUpAt`) với khách hàng (ví dụ: gọi lại vào 09:00 sáng mai).

**Actor:** Nhân viên Kinh doanh.

**Quy tắc nghiệp vụ:**
- `BR-17.1`: Trường `nextFollowUpAt` được hiển thị nổi bật trực tiếp trên thẻ Kanban (màu xanh nếu chưa đến hạn, màu đỏ nếu đã quá hạn chăm sóc).

---

### FEAT-18 — Tiến trình Tự động Quét & Bắn Thông báo Nhắc nhở (Follow-up Due Sweep) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tiến trình ngầm định kỳ quét các cơ hội đã đến hạn hoặc sắp đến hạn chăm sóc để gửi thông báo tức thì tới người phụ trách.

**Actor:** Tiến trình Hệ thống (`DealFollowUpService`).

**Quy tắc nghiệp vụ:**
- `BR-18.1`: Tiến trình chạy định kỳ mỗi **5 phút** quét các cơ hội có `nextFollowUpAt <= now()` và `followUpNotifiedAt === null`.
- `BR-18.2`: Phát thông báo trong ứng dụng (In-App Notification) và đánh dấu `followUpNotifiedAt = now()` để đảm bảo mỗi lịch hẹn chỉ gửi thông báo đúng 1 lần duy nhất.

---

### FEAT-19 — Tự động Nhận diện & Cảnh báo Cơ hội Nguội Lạnh (Stale Deal Detection) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động phát hiện các cơ hội bán hàng đang mở nhưng đã bị bỏ quên không có bất kỳ tương tác nào (không có cuộc gọi, ghi chú, cập nhật) vượt quá ngưỡng thời gian quy định (mặc định 14 ngày).

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-19.1`: So sánh trường `lastActivityAt` với thời điểm hiện tại. Nếu `now - lastActivityAt > 14 ngày`, cơ hội được đánh dấu cờ Cơ hội Nguội (Stale Deal). Các hành vi được công nhận làm mới `lastActivityAt` gồm: Ghi nhận cuộc gọi/họp/email, chuyển giai đoạn phễu, hoàn thành task liên kết hoặc nhận phản hồi từ khách hàng (loại trừ thao tác chỉnh sửa thuộc tính trường nội bộ của quản trị viên).
- `BR-19.2`: Thẻ Kanban của cơ hội nguội hiển thị biểu tượng ngọn lửa tắt màu xám kèm số ngày không hoạt động để nhắc nhở người phụ trách.

---

### FEAT-20 — Tự động Chạm Hoạt động Đánh thức Cơ hội (Touch Activity Mechanism) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Mỗi khi nhân viên ghi nhận 1 cuộc gọi, tạo 1 ghi chú, gửi 1 email hoặc hoàn thành 1 công việc trên Deal (`POST /api/v1/deals/:id/activities`), hệ thống tự động làm mới mốc thời gian `lastActivityAt = now()`.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-20.1`: Thao tác ghi nhận hoạt động sẽ lập tức xóa bỏ trạng thái Cơ hội Nguội và đưa deal trở lại danh sách hoạt động tích cực.

---

## F. QUẢN LÝ THẮNG/THUA & PHÂN TÍCH THẤT BẠI (WIN/LOSS & LOSS REASONS)

### FEAT-21 — Quy trình Đóng Cơ hội Thành công & Lý do Thắng (Closed Won) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi chốt hợp đồng thành công, nhân viên kéo thẻ hoặc bấm nút "Đóng Thành Công" (Closed Won) và tùy chọn ghi nhận Lý do Thành công (Win Reasons).

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh.

**Quy tắc nghiệp vụ:**
- `BR-21.1`: Hệ thống cập nhật `stageId` sang giai đoạn Won, gán `probability = 100%`, ghi nhận `wonAt = now()`.
- `BR-21.2`: Cho phép chọn Lý do Thành công (`winReason`) từ danh mục chuẩn (Giá cạnh tranh, Tính năng vượt trội, Uy tín thương hiệu, Dịch vụ hỗ trợ tốt) để phục vụ phân tích chiến lược bán hàng.
- `BR-21.3`: Tự động nâng cấp giai đoạn vòng đời của khách hàng liên hệ liên quan lên `Customer`.

---

### FEAT-22 — Bắt buộc Khai báo Lý do Thất bại khi Đóng Thua (Mark as Closed Lost) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi giao dịch thất bại, nhân viên chuyển cơ hội sang "Đóng Thất Bại" (Closed Lost). Hệ thống hiển thị hộp thoại bắt buộc người dùng chọn Lý do Thất bại chuẩn hóa trước khi hoàn tất.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh.

**Quy tắc nghiệp vụ:**
- `BR-22.1 (Bắt buộc lý do)`: Bắt buộc phải chọn 1 lý do từ danh mục chuẩn (`lostReason`) và cho phép nhập ghi chú chi tiết giải thích thêm.
- `BR-22.2`: Hệ thống cập nhật `stageId` sang Lost, gán `probability = 0%`, ghi nhận `lostAt = now()`.

---

### FEAT-23 — Quản lý Danh mục Lý do Thắng/Thua & Nguồn Giao dịch (Win/Loss Reasons & Sources) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép quản trị viên cấu hình danh mục lý do thành công (Win Reasons), danh mục lý do thất bại (Loss Reasons: Giá cao, Thiếu tính năng, Chọn đối thủ X, Hết ngân sách) và danh mục Nguồn cơ hội (Website, Quảng cáo Google, Sự kiện, Đối tác giới thiệu).

**Actor:** Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-23.1`: Cung cấp API quản lý nguồn cơ hội và lý do thắng/thua (`/api/v1/deal-settings/sources`, `/api/v1/deal-settings/reasons`).

---

## G. VAI TRÒ NHÂN SỰ & NGUỒN GỐC GIAO DỊCH (CONTACT ROLES & ATTRIBUTION)

### FEAT-24 — Gắn Vai trò Nhân sự Khách hàng trong Cơ hội (Contact Roles on Deals) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép gắn nhiều nhân sự liên hệ vào cùng 1 Cơ hội bán hàng với các vai trò quyết định mua hàng khác nhau.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh.

**Quy tắc nghiệp vụ:**
- `BR-24.1`: Cấu trúc `contactRoles`: `{ contactId, role, isPrimary }`.
- `BR-24.2`: Các vai trò chuẩn:
  - **Người ra quyết định (Decision Maker)**
  - **Người đánh giá kỹ thuật (Technical Evaluator)**
  - **Người bảo trợ nội bộ (Champion / Sponsor)**
  - **Người mua hàng / Kế toán (Purchaser / Billing Contact)**
  - **Người ảnh hưởng (Influencer)**
- `BR-24.3`: Mỗi cơ hội có duy nhất 1 Người liên hệ chính (`isPrimary = true`).

---

### FEAT-25 — Theo dõi Nguồn gốc Tiếp thị & Chiến dịch (Campaign UTM Attribution) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Lưu trữ các tham số tiếp thị nguồn (`utmSource`, `utmMedium`, `utmCampaign`) được kế thừa tự động từ trang đích hoặc form đăng ký ban đầu của khách hàng.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-25.1`: Giúp đội ngũ Marketing và Sales đo lường chính xác chiến dịch tiếp thị nào mang lại doanh thu thực tế cao nhất (Campaign ROI).

---

## H. DỰ BÁO DOANH THU & BÁO CÁO BÁN HÀNG (REVENUE FORECASTING & REPORTS)

### FEAT-26 — Động cơ Dự báo Doanh thu có Trọng số (Weighted Pipeline Forecasting) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Tự động tính toán doanh thu kỳ vọng theo thời gian thực dựa trên giá trị và xác suất thành công của từng cơ hội đang mở.

**Actor:** Quản lý Kinh doanh, Giám đốc Kinh doanh.

**Công thức tính toán:**
$$\text{Doanh thu Dự báo Kỳ vọng} = \sum_{i=1}^{n} \left( \text{Giá trị Cơ hội}_i \times \text{Xác suất Giai đoạn}_i \right)$$

**Quy tắc nghiệp vụ:**
- `BR-26.1`: Cho phép lọc dự báo theo Tháng, Quý, Năm tài chính và theo từng Nhân viên / Phòng ban kinh doanh.

---

### FEAT-27 — Báo cáo Tỷ lệ Chuyển đổi Phễu & Bảng Xếp hạng Kinh doanh `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp biểu đồ tỷ lệ chuyển đổi phễu (Pipeline Conversion Funnel) và Bảng xếp hạng doanh số nhân viên (Sales Leaderboard).

**Actor:** Mọi người dùng có quyền xem báo cáo bán hàng.

**Quy tắc nghiệp vụ:**
- `BR-27.1`: Đo lường tỷ lệ chuyển đổi từ bước A sang bước B, tỷ lệ Win Rate tổng thể và Giá trị hợp đồng trung bình (Average Deal Size).

---

## I. THAO TÁC HÀNG LOẠT & NHẬP/XUẤT DỮ LIỆU (BULK & IMPORT/EXPORT)

### FEAT-28 — Thao tác Cập nhật, Gắn thẻ & Xóa Hàng loạt (Bulk Deal Operations) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép chọn nhiều cơ hội cùng lúc để chuyển giai đoạn hàng loạt, đổi người phụ trách hàng loạt, gắn thẻ phân loại hoặc xóa hàng loạt.

**Actor:** Quản lý Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-28.1`: Thực thi an toàn qua API `/api/v1/deals/bulk`, kiểm tra phân quyền trên từng ID; các ID không có quyền sẽ được đưa vào danh sách `skipped` thay vì làm lỗi toàn bộ yêu cầu.

---

### FEAT-29 — Nhập / Xuất Danh sách Cơ hội Dung lượng lớn 50MB qua Hàng đợi `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tải lên tệp Excel/CSV chứa danh sách cơ hội để nhập khẩu hàng loạt hoặc xuất dữ liệu cơ hội ra tệp CSV an toàn qua token.

**Actor:** Quản trị viên Workspace, Người có quyền `import`/`export` trên Deals.

**Quy tắc nghiệp vụ:**
- `BR-29.1`: Xử lý qua hàng đợi BullMQ, tệp xuất được bảo vệ bằng token tải về có thời hạn 24 giờ.

---

## J. DÒNG THỜI GIAN HOẠT ĐỘNG & LIÊN KẾT VÉ HỖ TRỢ

### FEAT-30 — Dòng thời gian Hoạt động Hợp nhất & Liên kết Vé Hỗ trợ (Linked Tickets) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Lưu trữ toàn bộ dòng thời gian tương tác trực tiếp trên Deal và tra cứu các Vé hỗ trợ kỹ thuật liên quan đến thương vụ này (`GET /api/v1/deals/:id/tickets`).

**Actor:** Nhân viên Kinh doanh, Nhân viên Hỗ trợ.

**Quy tắc nghiệp vụ:**
- `BR-30.1`: Cho phép nhân viên kinh doanh biết ngay khách hàng đang có khiếu nại hay sự cố kỹ thuật nào trước khi bước vào cuộc đàm phán chốt hợp đồng.
- `BR-30.2 (Cảnh báo Rủi ro Kỹ thuật trên Kanban) [Yêu cầu mới]`: Nếu một Deal có ít nhất một Vé hỗ trợ liên kết đang mở ở mức độ ưu tiên `URGENT` hoặc `HIGH`, thẻ Deal trên bảng Kanban tự động hiển thị biểu tượng cờ cảnh báo màu vàng để nhắc nhở nhân viên kinh doanh giải tỏa rào cản kỹ thuật trước khi đàm phán chốt hợp đồng.

---

## K. QUY TRÌNH KIỂM SOÁT TIẾN ĐỘ CƠ HỘI (DEAL PROCESS CONTROLS)

### FEAT-31 — Rào cản Điều kiện Chuyển Giai đoạn (Stage Entry Requirements / Stage-Gate Rules) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép Quản trị viên cấu hình danh sách điều kiện bắt buộc phải thỏa mãn trước khi một Cơ hội được phép chuyển sang giai đoạn tiếp theo, đảm bảo dữ liệu Pipeline phản ánh đúng thực trạng thương vụ.

**Actor:** Quản trị viên Workspace (cấu hình quy tắc), Nhân viên Kinh doanh (bị áp dụng quy tắc).

**Quy tắc nghiệp vụ:**
- `BR-31.1 (Cấu hình điều kiện vào Stage)`: Mỗi giai đoạn trong một Pipeline có thể được gắn danh sách "Stage Entry Requirements" gồm: (a) **Trường bắt buộc phải điền** (ví dụ: Line Items không được trống khi vào giai đoạn "Báo giá"); (b) **Tài liệu bắt buộc phải đính kèm** (ví dụ: phải có file Hợp đồng bản scan khi vào "Closed Won"); (c) **Phê duyệt của Manager** (ví dụ: Deal giá trị lớn hơn ngưỡng quy định).
- `BR-31.2 (Chặn chuyển giai đoạn)`: Khi người dùng kéo thả hoặc thao tác chuyển giai đoạn mà không đáp ứng đủ điều kiện, hệ thống từ chối thao tác và hiển thị thông báo rõ ràng từng điều kiện còn thiếu.
- `BR-31.3 (Hồ sơ Requirements)`: Mọi lần từ chối chuyển giai đoạn được ghi nhận vào Audit Log của Deal để Manager theo dõi.
- `BR-31.4 (Bỏ qua trong trường hợp khẩn cấp)`: Chỉ Quản lý Kinh doanh và Quản trị viên Workspace mới có thể "bỏ qua" (Override) điều kiện Stage-Gate với bắt buộc nhập lý do; hành động này được ghi vào Audit Log.

---

### FEAT-32 — Trạng thái Tạm ngưng Cơ hội (On Hold Deal Status) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép đặt Cơ hội vào trạng thái "Tạm ngưng" (On Hold) khi khách hàng hoãn quyết định do lý do ngoài ý muốn (hết ngân sách năm, chờ phê duyệt nội bộ, thay đổi nhân sự quyết định), mà không buộc phải đóng là "Thất bại" và không làm méo Forecast.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh.

**Quy tắc nghiệp vụ:**
- `BR-32.1 (Điều kiện chuyển On Hold)`: Khi chuyển Deal sang trạng thái `ON_HOLD`, bắt buộc người dùng nhập: (a) **Lý do tạm ngưng** từ danh mục chuẩn (Chờ ngân sách, Thay đổi nhân sự, Chờ phê duyệt kỹ thuật, Khác); (b) **Ngày dự kiến tái kích hoạt** (`expectedReactivationDate`).
- `BR-32.2 (Ẩn khỏi Forecast & Stale Detection)`: Deal ở trạng thái `ON_HOLD` **không được tính** vào Doanh thu Dự báo có Trọng số (Weighted Forecast) và **không kích hoạt** cảnh báo Cơ hội Nguội (Stale Deal Detection) trong thời gian tạm ngưng.
- `BR-32.3 (Vị trí trên Kanban)`: Deal ở trạng thái `ON_HOLD` hiển thị trong một cột riêng "Tạm ngưng" trên Bảng Kanban (không nằm lẫn với các cột giai đoạn hoạt động).
- `BR-32.4 (Nhắc nhở tái kích hoạt)`: Khi đến `expectedReactivationDate`, hệ thống tự động gửi thông báo nhắc nhở Người phụ trách và Quản lý Kinh doanh để xem xét tái kích hoạt hoặc kéo dài thêm thời gian tạm ngưng.
- `BR-32.5 (Tái kích hoạt)`: Khi Deal được tái kích hoạt từ `ON_HOLD`, tự động quay về giai đoạn Pipeline cuối cùng trước khi tạm ngưng và bắt đầu lại cơ chế Stale Detection.

---

### FEAT-33 — Quy trình Phê duyệt Chiết khấu Cơ hội (Discount Approval Workflow) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khi mức chiết khấu trên Cơ hội vượt quá ngưỡng quy định, bắt buộc trình Quản lý phê duyệt trước khi trình báo giá chính thức cho khách hàng, kiểm soát biên lợi nhuận và tránh cuộc đua xuống đáy về giá.

**Actor:** Nhân viên Kinh doanh (yêu cầu phê duyệt), Quản lý Kinh doanh / Giám đốc Kinh doanh (người phê duyệt), Quản trị viên Workspace (cấu hình ngưỡng).

**Quy tắc nghiệp vụ:**
- `BR-33.1 (Cấu hình Ngưỡng Phê duyệt)`: Quản trị viên cấu hình ngưỡng chiết khấu theo phân cấp: (a) Mức 1 (ví dụ: ≤15%): Sales tự quyết định; (b) Mức 2 (ví dụ: 15%-30%): Cần phê duyệt của Sales Manager; (c) Mức 3 (ví dụ: >30%): Cần phê duyệt của Giám đốc Kinh doanh (VP of Sales).
- `BR-33.2 (Khóa Deal khi chờ duyệt)`: Khi có yêu cầu phê duyệt chiết khấu đang chờ, Deal bị khóa ở chế độ chỉ đọc (Read-only) đối với trường Giá trị và Line Items; nhân viên không thể thay đổi chiết khấu cho đến khi có kết quả phê duyệt.
- `BR-33.3 (Luồng phê duyệt)`: Người phê duyệt nhận thông báo trong CRM (và Email) với đầy đủ ngữ cảnh: Tên Deal, Khách hàng, Giá trị gốc, Mức chiết khấu yêu cầu, Lý do giải thích của Sales. Người phê duyệt có 3 hành động: (a) **Duyệt** → Deal mở khóa, Sales tiếp tục; (b) **Từ chối** → Sales nhận thông báo và có thể điều chỉnh lại mức chiết khấu; (c) **Yêu cầu làm rõ** → Sales phải cung cấp thêm thông tin.
- `BR-33.4 (Audit & Báo cáo)`: Mọi quyết định phê duyệt/từ chối chiết khấu được ghi vào Audit Log của Deal và tổng hợp vào Báo cáo Phê duyệt Chiết khấu để Giám đốc theo dõi xu hướng chiết khấu của đội ngũ.

---

## 4. Yêu cầu phi chức năng


### 4.1 Hiệu năng & Khả năng đáp ứng (Performance)
- **NFR-01 (Tốc độ tải Bảng Kanban):** API Board Summary và tải cột Kanban phản hồi dưới **200ms** (p95) trên phễu có 50,000 cơ hội.
- **NFR-02 (Tối ưu hóa Phân trang Keyset):** Phân trang thẻ Kanban không bị suy giảm hiệu năng khi người dùng cuộn đến trang thứ 100.
- **NFR-03 (Tính toán Báo cáo Doanh số):** Báo cáo dự báo doanh thu và bảng xếp hạng phản hồi dưới **500ms** qua cơ chế tổng hợp chỉ mục tối ưu.

### 4.2 Độ tin cậy & Toàn vẹn Dữ liệu (Reliability & ACID)
- **NFR-04 (An toàn Di chuyển Phễu):** Thao tác lưu trữ phễu và di chuyển hàng loạt cơ hội sang phễu khác thực thi trong 1 Database Transaction nguyên tử.
- **NFR-05 (Tính toàn vẹn Lịch sử Giai đoạn):** Bản ghi `stageHistory` là bất biến (Append-Only), không thể bị sửa đổi hoặc xóa bỏ thủ công.

### 4.3 An toàn & Bảo mật (Security)
- **NFR-06 (Bảo mật Phân quyền ABAC):** Áp dụng nghiêm ngặt phạm vi dữ liệu (Cá nhân / Phòng ban / Cây phòng ban / Toàn tổ chức). Nhân viên không thể xem hoặc kéo thả cơ hội ngoài phạm vi được phân công.
- **NFR-07 (Bảo mật Tải tệp Xuất dữ liệu):** Đường dẫn tải file export được mã hóa bằng token ngẫu nhiên dùng 1 lần, chống tải trộm từ Internet.

---

## 5. Ma trận quyền truy cập tính năng

| Mã FEAT | Tên tính năng nghiệp vụ | Nhân viên (Sales Rep) | Nhân viên Hỗ trợ | Quản lý (Sales Mgr) | Quản trị viên (Admin) | Chủ sở hữu (Owner) |
| --- | --- | :---: | :---: | :---: | :---: | :---: |
| `FEAT-01` | Tạo & Quản lý Deal | Scope gán | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-02` | Xem Hồ sơ Deal 360 | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-03` | Cập nhật Giá trị & Close Date | Scope gán | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-04` | Xem Giá trị Nhạy cảm FLS | Có quyền* | — | Có quyền* | **Toàn quyền** | **Toàn quyền** |
| `FEAT-05` | Thùng rác & Phục hồi Deal | — | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-06` | Xem & Quản trị Pipelines | Xem danh sách | Xem danh sách | Xem danh sách | **Toàn quyền** | **Toàn quyền** |
| `FEAT-07` | Cấu hình Giai đoạn & Xác suất | — | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-08` | Sắp xếp Thứ tự Giai đoạn | — | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-09` | Lưu trữ & Di chuyển Phễu | — | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-10` | Xóa Giai đoạn & Chuyển Deal | — | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-11` | Bảng Kanban Board Summary | Scope gán | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-12` | Tải thẻ Cột Kanban Column | Scope gán | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-13` | Kéo thả Chuyển Giai đoạn | Scope gán | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-14` | Bộ lọc Thông minh Kanban | **Cho phép** | — | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-15` | Ghi nhận Lịch sử Giai đoạn | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-16` | Phân tích Vận tốc Bán hàng | — | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-17` | Đặt Lịch Chăm sóc Follow-up | Scope gán | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-18` | Nhận Thông báo Nhắc nhở | **Cho phép** | — | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-19` | Cảnh báo Cơ hội Nguội | **Cho phép** | — | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-20` | Đánh thức Cơ hội (Activity) | Scope gán | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-21` | Đóng Deal Thắng (Won) | Scope gán | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-22` | Đóng Deal Thua (Lost) | Scope gán | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-23` | Cấu hình Lý do Thất bại & Nguồn | — | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-24` | Gắn Vai trò Nhân sự Mua hàng | Scope gán | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-25` | Xem Nguồn gốc Chiến dịch UTM | Scope gán | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-26` | Dự báo Doanh thu có Trọng số | — | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-27` | Báo cáo Phễu & Bảng Xếp hạng | Scope gán | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-28` | Thao tác Hàng loạt Bulk | — | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-29` | Nhập / Xuất File Excel 50MB | — | — | Có quyền `import`/`export`| **Toàn quyền** | **Toàn quyền** |
| `FEAT-30` | Tra cứu Vé Hỗ trợ Liên kết | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |

---

## 6. Kịch bản chấp nhận tổng hợp (UAT)

### Kịch bản 1: Tạo mới Cơ hội, Kéo thả Kanban & Đóng Thắng (Closed Won)
1. Nhân viên kinh doanh tạo Deal mới: "Hợp đồng Bản quyền CRM 50 Seats", Giá trị 300,000,000 VND, gắn Doanh nghiệp "Công ty Cổ phần Đại Phát", ngày dự kiến chốt 30/09/2026.
2. Trên bảng Kanban, Deal xuất hiện ở cột "Tiếp cận ban đầu".
3. Sau buổi họp trình bày giải pháp, nhân viên kéo thẻ sang cột "Trình bày giải pháp/Demo". Cột Kanban tự động cập nhật lại tổng số tiền.
4. Sau khi ký hợp đồng và nhận thanh toán, nhân viên kéo thẻ sang cột "Closed Won".
5. **Kỳ vọng:** Trạng thái deal chuyển sang Won, xác suất thành công đạt 100%, ghi nhận ngày thắng và tự động nâng hạng khách hàng liên quan lên `Customer`.

---

### Kịch bản 2: Đóng Cơ hội Thất bại & Bắt buộc Khai báo Lý do
1. Nhân viên kinh doanh kéo một cơ hội trị giá 150 triệu vào cột "Closed Lost".
2. **Kỳ vọng giao diện:** Hệ thống lập tức hiển thị hộp thoại: "Xác nhận đóng cơ hội thất bại", bắt buộc chọn Lý do thất bại từ danh sách thả xuống.
3. Nhân viên chọn lý do "Giá quá cao so với ngân sách" và nhập ghi chú: "Khách hàng chọn gói của đối thủ vì chi phí thấp hơn 30%".
4. Bấm "Xác nhận".
5. **Kỳ vọng hệ thống:** Deal chuyển sang trạng thái Lost, xác suất về 0%, lưu vết lý do và ghi nhận vào báo cáo phân tích nguyên nhân thất bại.

---

### Kịch bản 3: Đặt Lịch Hẹn Chăm sóc & Nhận Thông báo Nhắc nhở Tự động
1. Nhân viên thiết lập lịch chăm sóc tiếp theo: `nextFollowUpAt = 09:00 ngày 29/08/2026`.
2. Thẻ cơ hội trên bảng Kanban hiển thị dòng chữ màu xanh: "Lịch hẹn: 09:00 29/08".
3. Lúc 09:00 ngày 29/08, tiến trình `DealFollowUpService` quét phát hiện lịch hẹn đến hạn.
4. **Kỳ vọng:** Hệ thống phát thông báo chuông (In-App Notification) trên góc màn hình của nhân viên: "Bạn có lịch chăm sóc cơ hội [Hợp đồng Bản quyền CRM] ngay bây giờ!". Thẻ Kanban chuyển sang màu cam cảnh báo.

---

### Kịch bản 4: Cảnh báo Cơ hội Nguội Lạnh & Đánh thức Hoạt động
1. Một cơ hội bán hàng đã nằm yên ở giai đoạn "Báo giá" suốt 16 ngày không có bất kỳ tương tác nào.
2. **Kỳ vọng hiển thị:** Thẻ cơ hội tự động hiển thị biểu tượng ngọn lửa xám kèm cảnh báo: "16 ngày không có hoạt động".
3. Nhân viên gọi điện cho khách hàng và bấm vào nút "Ghi nhận cuộc gọi" trên hồ sơ Deal, nhập nội dung trao đổi.
4. **Kỳ vọng hệ thống:** Mốc `lastActivityAt` được làm mới về thời điểm hiện tại, biểu tượng cảnh báo nguội biến mất ngay lập tức.

---

### Kịch bản 5: Lưu trữ Phễu Bán hàng & Di chuyển Cơ hội An toàn
1. Quản trị viên muốn đóng phễu cũ "Phễu Dịch Vụ 2025" để chuyển sang phễu mới "Phễu Dịch Vụ 2026".
2. Trong phễu cũ hiện còn 15 cơ hội đang mở.
3. Quản trị viên bấm "Lưu trữ phễu", chọn chuyển toàn bộ cơ hội sang "Phễu Dịch Vụ 2026" tại giai đoạn "Khảo sát nhu cầu".
4. **Kỳ vọng:** Hệ thống thực thi giao dịch di chuyển an toàn 15 cơ hội sang phễu mới và đóng lưu trữ phễu cũ mà không làm mất bất kỳ dữ liệu nào.

---

### Kịch bản 6: Tính toán Dự báo Doanh thu Bán hàng có Trọng số
1. Doanh nghiệp có 3 cơ hội bán hàng dự kiến chốt trong Tháng 9:
   - Deal A: Giá trị 100 triệu, Giai đoạn "Khảo sát" (Xác suất 20%) -> Giá trị kỳ vọng: 20 triệu.
   - Deal B: Giá trị 200 triệu, Giai đoạn "Báo giá" (Xác suất 50%) -> Giá trị kỳ vọng: 100 triệu.
   - Deal C: Giá trị 500 triệu, Giai đoạn "Đàm phán" (Xác suất 80%) -> Giá trị kỳ vọng: 400 triệu.
2. Quản lý kinh doanh mở Báo cáo Dự báo Doanh thu Tháng 9.
3. **Kỳ vọng báo cáo:** Tổng giá trị đường ống (Total Pipeline) hiển thị: **800 triệu VND**; Doanh thu dự báo có trọng số (Weighted Forecast) hiển thị chính xác: $20 + 100 + 400 =$ **520 triệu VND**.

---

## 7. Giới hạn hiện tại & Vấn đề chính sách cần quyết định tiếp

1. **Chính sách Phân chia Doanh số & Hoa hồng Đồng phụ trách (Commission Splits):**
   - *Vấn đề:* Khi một hợp đồng lớn có 2 hoặc nhiều nhân viên kinh doanh cùng tham gia chăm sóc, tỷ lệ phân chia doanh số (%) sẽ được cấu hình như thế nào?
   - *Đề xuất PM:* Trong phiên bản hiện tại, 1 Deal có 1 Người phụ trách chính (`ownerId`). Tính năng chia sẻ doanh số (Opportunity Splits) sẽ đưa vào giai đoạn tiếp theo.
2. **Tích hợp Danh mục Báo giá & Cấu hình Sản phẩm (CPQ / Price Books):**
   - *Vấn đề:* Có cho phép nhân viên chọn từng sản phẩm từ Bảng giá (Price Book), tính chiết khấu và tự động kết xuất ra tệp PDF Báo giá trực tiếp từ Deal không?
   - *Đề xuất PM:* Đưa tính năng Báo giá PDF & Quản lý Sản phẩm vào lộ trình nâng cấp phân hệ Bán hàng nâng cao.
3. **Quy định Tự động Đóng Cơ hội Quá hạn (Auto-Close Expired Deals Policy):**
   - *Vấn đề:* Nếu cơ hội đã quá ngày dự kiến đóng (`closeDate`) hơn 60 ngày mà không có hoạt động, có nên tự động chuyển sang `Closed Lost` không?
   - *Đề xuất PM:* Không tự động đóng để tránh can thiệp ngoài ý muốn; hệ thống áp dụng cảnh báo Cơ hội Nguội để người quản lý chủ động xử lý.
