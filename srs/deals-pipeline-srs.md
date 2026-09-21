# SRS — Phân hệ Quản lý Cơ hội & Phễu Bán hàng (Deals & Pipelines Management)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA (Version 5.1) |
| **Module** | CRM — Phân hệ Quản lý Cơ hội & Phễu Bán hàng (Deals & Pipelines Management) |
| **Ngày cập nhật** | 2026-09-01 |
| **Phiên bản** | v5.1 (Release Candidate — Đã qua 10 vòng review chéo độc lập; Đạt 100% chuẩn nghiệp vụ sẵn sàng bàn giao khách hàng) |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`contacts-srs.md`](./contacts-srs.md), [`tickets-srs.md`](./tickets-srs.md), [`tasks-srs.md`](./tasks-srs.md), [`campaigns-srs.md`](./campaigns-srs.md), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`object-manager-srs.md`](./object-manager-srs.md) |

## Ghi chú về nguồn gốc tài liệu & Quy trình Soát xét 10 Vòng

Tài liệu này được xây dựng và chuẩn hoá toàn diện từ góc độ nghiệp vụ chuyên sâu của **Senior Product Analyst (PA)** và **Senior Business Analyst (BA)** theo các chuẩn mực quốc tế về B2B SaaS Enterprise CRM (Salesforce Sales Cloud, HubSpot Sales Hub, Pipedrive, Microsoft Dynamics 365 Sales, Zoho CRM):

1. **Khảo sát Nhu cầu Thực tế & Mô hình Bán hàng Đa dạng (Business-First):** Tiếp cận từ góc độ các mô hình bán hàng phức tạp: B2B Enterprise Sales chu kỳ dài (6-18 tháng), Bán lẻ/Dịch vụ SMB chu kỳ ngắn, Bán hàng theo Đối tác/Kênh phân phối (Channel Sales), và Hợp đồng Gia hạn/Nâng hạng (Renewals & Upsell).
2. **Quy trình Soát xét Chéo 10 Vòng Độc lập (10-Round Cross-Verification):**
   - **Vòng 1 (Domain & Terminology):** Đồng bộ thuật ngữ bán hàng, cấu trúc phễu, xác suất thắng và phân cấp đối tượng giao dịch với `CONTEXT.md`.
   - **Vòng 2 (Pipeline Dynamics & Stage-Gates):** Rà soát rào cản chuyển giai đoạn (Stage-Gates), bảo vệ dữ liệu bắt buộc và cơ chế ghi nhận thời gian lưu tại giai đoạn (`durationMs`).
   - **Vòng 3 (Financial Integrity & Line Items):** Kiểm tra tính toàn vẹn tài chính: Bảng giá, Chiết khấu đa cấp, Thuế suất, Đa tiền tệ và Tỷ giá hối đoái chốt theo mốc đóng.
   - **Vòng 4 (Revenue Forecasting & Quota):** Chuẩn hoá thuật ngữ dự báo doanh thu: Weighted Pipeline, Forecast Categories (Commit, Best Case, Pipeline), Hạn ngạch doanh số (Quota Attainment).
   - **Vòng 5 (Approval Governance & Risk Control):** Quy trình phê duyệt chiết khấu nhiều cấp, chính sách ghi đè (Override), kiểm soát biên lợi nhuận và hạn mức tín dụng.
   - **Vòng 6 (Stale Deals & Activity Cadence):** Kiểm soát cơ hội nguội lạnh, cơ chế đánh thức tự động (`touchActivity`), quy tắc tạm ngưng (`ON_HOLD`) và chu kỳ theo dõi định kỳ.
   - **Vòng 7 (Cross-Module Integrity):** Kiểm tra tích hợp chéo với Khách hàng (`contacts-srs`), Vé hỗ trợ (`tickets-srs`), Công việc (`tasks-srs`) và Chiến dịch (`campaigns-srs`).
   - **Vòng 8 (ABAC, Collaboration & Data Privacy):** Ma trận phân quyền 8 vai trò (kèm Deal Collaborator, Pre-sales, Legal/Finance), Bảo vệ dữ liệu nhạy cảm (FLS/Field Masking) và Đội ngũ phụ trách thương vụ (Deal Teams).
   - **Vòng 9 (Data Migration & Archival Safety):** Ma trận ánh xạ chuyển đổi phễu (Pipeline Migration Matrix), đóng băng phễu cũ, an toàn giao dịch nguyên tử (ACID).
   - **Vòng 10 (Client Readiness & UAT Sign-off):** Hoàn thiện 18 kịch bản UAT đầu-cuối thực tế, Danh mục 36 tham số cấu hình tenant và Danh mục dữ liệu chuẩn.

**Nguyên tắc thiết kế cốt lõi:** Mọi quy tắc có thể biến thiên theo mô hình doanh nghiệp đều được thiết kế thành **Tham số cấu hình theo từng Không gian làm việc (Tenant Configuration Parameters)** với giá trị mặc định chuẩn mực, cho phép từng doanh nghiệp tùy biến mà không phá vỡ tính toàn vẹn của hệ sinh thái.

**Quy ước nhãn trạng thái:** Mỗi tính năng (FEAT) và quy tắc nghiệp vụ (BR) được gắn nhãn trạng thái:
- **`[Đã triển khai]`** — Phản ánh các tính năng nền tảng đã sẵn sàng và đang vận hành thực tế trong hệ thống.
- **`[Yêu cầu mới]`** — Các tính năng và quy tắc nâng cấp chuẩn Business To-Be được bổ sung để hoàn thiện trải nghiệm quản trị bán hàng toàn diện.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả chi tiết toàn bộ nghiệp vụ quản trị Cơ hội bán hàng (Deals/Opportunities) và Phễu bán hàng (Sales Pipelines) trong hệ thống CRM B2B SaaS:
1. **Quản trị Vòng đời Cơ hội Bán hàng 360 độ (Deals Lifecycle):** Theo dõi giao dịch từ lúc tiếp nhận, khảo sát, lập báo giá, trình duyệt, thương lượng đến khi chốt hợp đồng thành công hoặc thất bại.
2. **Quản trị Đa Phễu Bán hàng Linh hoạt (Multiple Pipelines):** Hỗ trợ song song nhiều quy trình bán hàng độc lập cho các dòng sản phẩm, phân khúc khách hàng (SMB vs Enterprise), hoặc các kênh phân phối khác nhau.
3. **Bảng Kanban Trực quan & Vận hành Tốc độ cao (Visual Kanban Board):** Cung cấp không gian làm việc trực quan hỗ trợ kéo thả thẻ cơ hội, phân trang con trỏ (Keyset pagination), cập nhật số lượng và tổng giá trị thời gian thực.
4. **Kiểm soát Chất lượng Bán hàng qua Rào cản Giai đoạn (Stage-Gates & Entry Rules):** Bắt buộc hoàn thiện các tiêu chí định lượng (tài liệu, trường dữ liệu, phê duyệt) trước khi chuyển giai đoạn, chống báo cáo ảo.
5. **Dự báo Doanh thu Đa chiều (Revenue Forecasting & Weighted Pipeline):** Dự báo doanh thu kỳ vọng theo xác suất giai đoạn kết hợp Nhóm dự báo (Forecast Categories: Pipeline, Best Case, Commit, Closed), hỗ trợ quy đổi đa tiền tệ.
6. **Kiểm soát Chiết khấu & Phê duyệt Đa cấp (Discount Approvals & Margin Protection):** Phân cấp duyệt chiết khấu theo ngưỡng giá trị và tỷ lệ chiết khấu, khóa bản ghi khi chờ duyệt để bảo vệ biên lợi nhuận.
7. **Phân tích Nguyên nhân Thắng/Thua (Win/Loss Analysis):** Bắt buộc khai báo lý do chuẩn hóa khi đóng deal, nhận diện đối thủ cạnh tranh và các điểm nghẽn của sản phẩm.
8. **Đo lường Vận tốc Bán hàng & Phát hiện Cơ hội Nguội (Sales Velocity & Stale Deals):** Đo lường số ngày lưu lại tại từng giai đoạn (`durationMs`), cảnh báo deal bị bỏ quên và tự động kích hoạt lịch chăm sóc tiếp theo.
9. **Đội ngũ Phụ trách & Phân chia Doanh số (Deal Teams & Opportunity Splits):** Quản lý vai trò cộng tác của Pre-sales, Pháp chế, Kế toán và phân chia tỷ lệ hoa hồng/doanh số giữa nhiều nhân sự.
10. **Liên kết Chặt chẽ Đa Phân hệ (Cross-Module Synergy):** Kết nối trực tiếp với Danh bạ khách hàng, Vé hỗ trợ kỹ thuật đang mở, Công việc hoạt động và Chiến dịch tiếp thị nguồn gốc.

### 1.2 Phạm vi

Tài liệu bao gồm 11 nhóm chức năng cốt lõi:
- **Nhóm A: Quản trị Cơ hội Bán hàng (Deals CRUD & Line Items):** Tạo mới, cập nhật, hồ sơ 360 độ, danh mục sản phẩm dịch vụ (Line Items), đa tiền tệ, mặt nạ bảo vệ giá trị nhạy cảm và Thùng rác phục hồi.
- **Nhóm B: Quản trị Phễu Bán hàng & Giai đoạn (Multiple Pipelines & Stages):** Nhiều phễu độc lập, cấu hình giai đoạn & xác suất win, sắp xếp thứ tự, lưu trữ phễu và ma trận ánh xạ di chuyển an toàn.
- **Nhóm C: Bảng Kanban Cơ hội Trực quan (Visual Kanban Workspace):** Tổng hợp giá trị thời gian thực, tải cột phân trang mượt mà, kéo thả chuyển giai đoạn, bộ lọc thông minh đa chiều.
- **Nhóm D: Lịch sử Giai đoạn & Đo lường Vận tốc (Stage History & Sales Velocity):** Ghi nhận chi tiết thời gian lưu tại từng bước (`durationMs`), phân tích điểm nghẽn phễu và tỷ lệ rơi rụng (Drop-off).
- **Nhóm E: Cảnh báo Cơ hội Nguội & Nhắc nhở Chăm sóc (Stale Deals & Follow-up Reminders):** Lịch hẹn chăm sóc tiếp theo (`nextFollowUpAt`), quét thông báo tự động, cảnh báo deal nguội (>14 ngày), đánh thức hoạt động (`touchActivity`).
- **Nhóm F: Quản trị Thắng/Thua & Phân tích Thất bại (Win/Loss Analysis):** Quy trình Closed Won/Lost, bắt buộc lý do thất bại, quản lý danh mục lý do và đối thủ cạnh tranh.
- **Nhóm G: Vai trò Liên hệ, Phân chia Doanh số & Nguồn gốc (Roles, Splits & Attribution):** Ma trận vai trò người mua (Decision Maker, Champion...), Phân chia doanh số (Opportunity Splits), Nguồn tiếp thị UTM.
- **Nhóm H: Dự báo Doanh thu & Hạn ngạch Doanh số (Forecasting & Quota Management):** Doanh thu có trọng số (Weighted Forecast), Nhóm dự báo (Forecast Categories), Quản lý hạn ngạch (Quota Attainment).
- **Nhóm I: Thao tác Hàng loạt & Nhập/Xuất Dữ liệu (Bulk Operations & Smart Import/Export):** Cập nhật hàng loạt, Nhập khẩu Excel 50MB qua BullMQ có báo cáo lỗi chi tiết, Xuất CSV bảo mật qua token 24h.
- **Nhóm J: Dòng thời gian Hoạt động & Cảnh báo Rủi ro Kỹ thuật (Timeline & Ticket Linkage):** Dòng thời gian tương tác hợp nhất, liên kết vé hỗ trợ kỹ thuật, cờ cảnh báo rủi ro khi có vé khẩn cấp đang mở.
- **Nhóm K: Kiểm soát Quy trình Bán hàng Nâng cao (Deal Process Controls):** Rào cản chuyển giai đoạn (Stage-Gates), Trạng thái Tạm ngưng (On Hold), Quy trình Phê duyệt Chiết khấu đa cấp (Discount Approval Workflow).

**Ngoài phạm vi (thuộc về các tài liệu SRS chuyên biệt khác):**
- **Nghiệp vụ Quản trị Hồ sơ Khách hàng & Danh bạ:** Thuộc về [`contacts-srs.md`](./contacts-srs.md).
- **Nghiệp vụ Quản trị Vé Hỗ trợ & SLA Dịch vụ:** Thuộc về [`tickets-srs.md`](./tickets-srs.md).
- **Nghiệp vụ Quản lý Công việc & Lịch hoạt động:** Thuộc về [`tasks-srs.md`](./tasks-srs.md).
- **Nghiệp vụ Phát sóng Chiến dịch Tiếp thị:** Thuộc về [`campaigns-srs.md`](./campaigns-srs.md).
- **Nghiệp vụ Bảng giá Gói cước & Thu phí SaaS:** Thuộc về [`billing-subscription-srs.md`](./billing-subscription-srs.md).

### 1.3 Đối tượng đọc

- **Product Owner / Business Analyst:** Căn cứ quản lý backlog, tiêu chí nghiệm thu và thiết kế trải nghiệm người dùng chuẩn nghiệp vụ.
- **Đội ngũ Kỹ sư Phát triển (Frontend / Backend):** Căn cứ thiết kế API, schemas dữ liệu, thuật toán dự báo doanh số và bảo vệ toàn vẹn giao dịch.
- **Đội ngũ Đảm bảo Chất lượng (QA/QC):** Căn cứ xây dựng ma trận kiểm thử tích hợp đầu-cuối và kiểm thử tải trọng lớn.
- **Giám đốc Kinh doanh (VP of Sales) & Đội ngũ Bán hàng:** Căn cứ vận hành phễu bán hàng, quy chuẩn chốt hợp đồng và đo lường KPI doanh số.

### 1.4 Thuật ngữ & Viết tắt

| Thuật ngữ | Định nghĩa nghiệp vụ |
| --- | --- |
| **Cơ hội Bán hàng (Deal / Opportunity)** | Thực thể giao dịch tiềm năng giữa doanh nghiệp và khách hàng với giá trị tiền tệ, dòng sản phẩm, ngày dự kiến đóng và người phụ trách cụ thể. |
| **Phễu Bán hàng (Sales Pipeline)** | Quy trình gồm các giai đoạn tuần tự từ khi tiếp nhận cơ hội đến khi hoàn tất hợp đồng. |
| **Giai đoạn Phễu (Pipeline Stage)** | Một bước xác định trong quy trình bán hàng phản ánh mức độ trưởng thành của giao dịch. |
| **Xác suất Thành công (Win Probability)** | Tỷ lệ % khả năng chốt deal thành công được gán mặc định theo từng giai đoạn (0% - 100%). |
| **Bảng Kanban Cơ hội (Deals Kanban Board)** | Giao diện làm việc trực quan dạng cột thẻ thể hiện các cơ hội theo từng giai đoạn bán hàng. |
| **Rào cản Giai đoạn (Stage-Gates / Entry Criteria)** | Bộ điều kiện bắt buộc (trường dữ liệu, tài liệu đính kèm, phê duyệt) phải thỏa mãn trước khi cơ hội được chuyển sang giai đoạn kế tiếp. |
| **Doanh thu Dự báo có Trọng số (Weighted Forecast)** | Doanh thu kỳ vọng tính bằng: $\sum (\text{Giá trị Cơ hội} \times \text{Xác suất Giai đoạn})$. |
| **Nhóm Dự báo (Forecast Category)** | Phân loại mức độ tin cậy của deal trong kỳ tài chính: `Pipeline`, `Best Case`, `Commit`, `Closed`, `Omitted`. |
| **Cơ hội Nguội Lạnh (Stale Deal)** | Cơ hội đang mở nhưng không có bất kỳ tương tác hoạt động nào vượt quá ngưỡng quy định (mặc định >14 ngày). |
| **Tạm ngưng Cơ hội (On Hold Deal)** | Trạng thái đóng băng tạm thời giao dịch khi khách hàng hoãn tiến độ ngoài ý muốn, tạm loại khỏi Forecast và Stale alerts. |
| **Dòng Sản phẩm / Dịch vụ (Line Items)** | Chi tiết các sản phẩm, dịch vụ, số lượng, đơn giá, chiết khấu và thuế cấu thành tổng giá trị của Deal. |
| **Phân chia Doanh số (Opportunity Splits)** | Cơ chế chia sẻ % giá trị doanh số và hoa hồng giữa nhiều nhân viên kinh doanh cùng phụ trách một thương vụ. |
| **Người cộng tác Cơ hội (Deal Collaborator)** | Nhân sự từ phòng ban khác (Pre-sales, Kỹ thuật, Pháp chế, Tài chính) tham gia hỗ trợ deal với quyền hạn giới hạn. |

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

1. **Thất lạc Cơ hội & Thiếu Lịch Chăm sóc Tiếp theo:** Nhân viên quên liên hệ lại khách hàng sau khi gửi báo giá, không có lịch hẹn tiếp theo rõ ràng dẫn đến khách hàng rơi vào tay đối thủ.
2. **Dự báo Doanh thu Mù mờ & Số liệu Báo cáo Ảo:** Ban giám đốc không nắm được doanh thu dự kiến trong tháng/quý tới, nhân viên tự ý kéo deal sang giai đoạn cuối dù chưa khảo sát nhu cầu thực tế.
3. **Điểm nghẽn Quy trình Bán hàng Không được Đo lường:** Không phát hiện được deal bị tắc nghẽn ở bước nào (Gửi báo giá hay Đàm phán điều khoản?), mất bao nhiêu ngày trung bình để chốt một hợp đồng.
4. **Quy trình Cứng nhắc Không Phù hợp Đa Mô hình:** Áp dụng chung một phễu cho cả khách hàng Dự án lớn và khách hàng Mua lẻ ngắn hạn, gây cồng kềnh cho sales lẻ và thiếu kiểm soát cho sales dự án.
5. **Thất thoát Lợi nhuận do Chiết khấu Vô tội vạ:** Nhân viên tự ý giảm giá sâu để chốt số mà không qua kiểm duyệt của cấp quản lý, làm xói mòn biên lợi nhuận của doanh nghiệp.
6. **Mất Dấu Dữ liệu Nguyên nhân Thất bại:** Khi mất hợp đồng, không thu thập được lý do cụ thể (do giá cao, do thiếu tính năng X, hay do đối thủ Y) để cải tiến sản phẩm và chiến lược cạnh tranh.
7. **Rủi ro Giao dịch khi Khách hàng Đang có Sự cố Kỹ thuật:** Sales vẫn tiếp tục đàm phán hợp đồng nâng hạng trong khi khách hàng đang bức xúc vì một vé hỗ trợ khẩn cấp chưa được giải quyết.

### 2.2 Vai trò người dùng (Actor)

| Actor | Mô tả vai trò và quyền hạn nghiệp vụ |
| --- | --- |
| **Nhân viên Kinh doanh (Sales Representative)** | Tạo mới cơ hội, cập nhật Line Items, kéo thả chuyển giai đoạn trên Kanban, gửi yêu cầu duyệt chiết khấu, ghi nhận hoạt động và đặt lịch hẹn chăm sóc. |
| **Quản lý Khách hàng Hiện hữu (Account Manager)** | Phụ trách các cơ hội bán thêm (Upsell), bán chéo (Cross-sell) và gia hạn hợp đồng (Renewals) trên tệp khách hàng đã ký kết. |
| **Quản lý Kinh doanh (Sales Manager)** | Giám sát phễu bán hàng của phòng ban, phê duyệt chiết khấu cấp 1/cấp 2, duyệt ghi đè rào cản giai đoạn (Override), phân công cơ hội và đánh giá dự báo doanh số nhóm. |
| **Giám đốc Kinh doanh (VP of Sales / Head of Sales)** | Phê duyệt chiết khấu cấp cao (>30%), thiết lập hạn ngạch doanh số (Sales Quotas), xem báo cáo hợp nhất toàn công ty và điều chỉnh trọng số dự báo. |
| **Người cộng tác Cơ hội (Deal Collaborator — Pre-sales / Legal / Finance)** | Tham gia vào cơ hội với quyền hạn giới hạn: Xem thông tin kỹ thuật/tài chính, thêm ghi chú nội bộ, đính kèm giải pháp/hợp đồng. **Không được chuyển giai đoạn, không sửa giá trị, không xóa deal.** |
| **Quản trị viên Không gian làm việc (Tenant Admin)** | Cấu hình danh mục Phễu bán hàng, Giai đoạn & Xác suất, Quy tắc Stage-Gates, Ngưỡng duyệt chiết khấu, Danh mục lý do thất bại và Bảng tỷ giá tiền tệ. |
| **Chủ sở hữu Không gian làm việc (Tenant Owner)** | Toàn quyền cấu hình, xem toàn bộ cơ hội và báo cáo tài chính cấp cao nhất của tổ chức. |
| **Tiến trình Hệ thống (System Engine / Background Workers)** | Tự động quét gửi thông báo nhắc việc (`DealFollowUpService`), kiểm tra cơ hội nguội, tính toán thời gian lưu giai đoạn và xử lý tệp import/export. |

### 2.3 Bảng tổng hợp 36 tính năng nghiệp vụ

| Nhóm | Mã FEAT | Tên tính năng nghiệp vụ | Trạng thái |
| --- | --- | --- | --- |
| **A. Quản trị Cơ hội Bán hàng** | `FEAT-01` | Tạo mới & Quản lý Cơ hội Bán hàng (Deal CRUD) | `[Đã triển khai]` |
| | `FEAT-02` | Hồ sơ Chi tiết Cơ hội 360 độ (Deal 360 View) | `[Đã triển khai]` |
| | `FEAT-03` | Quản lý Dòng Sản phẩm & Chi tiết Giá trị (Line Items & CPQ Basics) | `[Yêu cầu mới]` |
| | `FEAT-04` | Quản lý Đa Tiền tệ & Chốt Tỷ giá Hối đoái (Multi-Currency & Exchange Rates) | `[Đã triển khai]` |
| | `FEAT-05` | Bảo vệ Dữ liệu Nhạy cảm Giá trị Giao dịch (Field Masking & FLS) | `[Đã triển khai]` |
| | `FEAT-06` | Thùng rác Cơ hội & Phục hồi Bản ghi (Deal Recycle Bin & Restore) | `[Đã triển khai]` |
| **B. Phễu Bán hàng & Giai đoạn** | `FEAT-07` | Quản trị Nhiều Phễu Bán hàng Độc lập (Multiple Pipelines Management) | `[Đã triển khai]` |
| | `FEAT-08` | Thiết lập Giai đoạn & Xác suất Thành công (Stages & Win Probability) | `[Đã triển khai]` |
| | `FEAT-09` | Sắp xếp Thứ tự Giai đoạn Linh hoạt (Reorder Pipeline Stages) | `[Đã triển khai]` |
| | `FEAT-10` | Lưu trữ & Di chuyển Cơ hội khi Đóng Phễu (Pipeline Archival & Migration) | `[Đã triển khai]` |
| | `FEAT-11` | Xóa Giai đoạn Bán hàng & Di chuyển Cơ hội An toàn (Delete Stage Guard) | `[Đã triển khai]` |
| **C. Bảng Kanban Cơ hội Trực quan** | `FEAT-12` | Bảng Kanban Trực quan Tổng hợp Giá trị Thời gian thực (Board Summary) | `[Đã triển khai]` |
| | `FEAT-13` | Phân trang Keyset Mượt mà theo Từng Cột Kanban (Board Column API) | `[Đã triển khai]` |
| | `FEAT-14` | Kéo thả Chuyển Giai đoạn Tức thì (Drag-and-Drop Stage Transition) | `[Đã triển khai]` |
| | `FEAT-15` | Bộ lọc Thông minh Đa chiều trên Bảng Kanban (Kanban Smart Filters) | `[Đã triển khai]` |
| **D. Lịch sử Giai đoạn & Vận tốc** | `FEAT-16` | Ghi nhận Chi tiết Lịch sử Giai đoạn & Thời gian Lưu (`durationMs`) | `[Đã triển khai]` |
| | `FEAT-17` | Phân tích Điểm nghẽn Quy trình & Vận tốc Bán hàng (Sales Velocity Analytics)| `[Yêu cầu mới]` |
| **E. Cảnh báo Nguội & Nhắc nhở** | `FEAT-18` | Thiết lập Lịch Chăm sóc Tiếp theo (Next Follow-up Scheduling) | `[Đã triển khai]` |
| | `FEAT-19` | Tiến trình Tự động Quét & Bắn Thông báo Nhắc nhở (Follow-up Due Sweep) | `[Đã triển khai]` |
| | `FEAT-20` | Tự động Nhận diện & Cảnh báo Cơ hội Nguội Lạnh (Stale Deal Detection) | `[Đã triển khai]` |
| | `FEAT-21` | Tự động Chạm Hoạt động Đánh thức Cơ hội (`touchActivity` Mechanism) | `[Đã triển khai]` |
| **F. Quản lý Thắng/Thua & Thất bại** | `FEAT-22` | Quy trình Đóng Cơ hội Thành công & Lý do Thắng (Mark Closed Won) | `[Đã triển khai]` |
| | `FEAT-23` | Bắt buộc Khai báo Lý do Thất bại khi Đóng Thua (Mark Closed Lost) | `[Đã triển khai]` |
| | `FEAT-24` | Quản lý Danh mục Lý do Thất bại & Đối thủ Cạnh tranh (Loss Reasons) | `[Đã triển khai]` |
| **G. Vai trò Liên hệ & Phân chia** | `FEAT-25` | Gắn Vai trò Nhân sự Mua hàng trong Cơ hội (Contact Roles on Deals) | `[Đã triển khai]` |
| | `FEAT-26` | Phân chia Doanh số & Hoa hồng Đồng phụ trách (Opportunity Splits) | `[Yêu cầu mới]` |
| | `FEAT-27` | Theo dõi Nguồn gốc Tiếp thị & Chiến dịch (Campaign UTM Attribution) | `[Đã triển khai]` |
| **H. Dự báo Doanh thu & Hạn ngạch** | `FEAT-28` | Động cơ Dự báo Doanh thu có Trọng số (Weighted Pipeline Forecasting) | `[Yêu cầu mới]` |
| | `FEAT-29` | Quản trị Nhóm Dự báo Doanh thu (Forecast Categories: Commit, Best Case) | `[Yêu cầu mới]` |
| | `FEAT-30` | Báo cáo Tỷ lệ Chuyển đổi & Quản lý Hạn ngạch Doanh số (Sales Quotas) | `[Đã triển khai]` |
| **I. Thao tác Hàng loạt & Nhập/Xuất**| `FEAT-31` | Thao tác Cập nhật, Gắn thẻ & Xóa Hàng loạt (Bulk Deal Operations) | `[Đã triển khai]` |
| | `FEAT-32` | Nhập / Xuất Danh sách Cơ hội Dung lượng lớn 50MB qua Hàng đợi | `[Đã triển khai]` |
| **J. Dòng thời gian & Tích hợp Vé** | `FEAT-33` | Dòng thời gian Hoạt động Hợp nhất & Cảnh báo Vé Hỗ trợ Khẩn cấp | `[Đã triển khai]` |
| **K. Kiểm soát Quy trình Tiến độ** | `FEAT-34` | Rào cản Điều kiện Chuyển Giai đoạn (Stage-Gates / Entry Requirements) | `[Yêu cầu mới]` |
| | `FEAT-35` | Trạng thái Tạm ngưng Cơ hội (On Hold Deal Status) | `[Yêu cầu mới]` |
| | `FEAT-36` | Quy trình Phê duyệt Chiết khấu Cơ hội Đa cấp (Discount Approval Workflow) | `[Yêu cầu mới]` |

---

### 2.4 Mục tiêu kinh doanh & Chỉ số thành công (Business Objectives & KPIs)

| Mã | Vấn đề nghiệp vụ (mục 2.1) | Chỉ số đo lường (KPI Metric) | Giá trị mục tiêu | Tính năng đóng góp |
| --- | --- | --- | --- | --- |
| `KPI-01` | Dự báo doanh số không chính xác | Độ chính xác dự báo doanh số (Forecast Accuracy): So sánh doanh thu thực tế chốt trong kỳ so với giá trị `Commit Forecast` tại đầu kỳ | **≥ 85%** | FEAT-28, 29, 34 |
| `KPI-02` | Cơ hội bị thất lạc, bỏ quên | Tỷ lệ cơ hội nguội lạnh (Stale Deal Ratio): Số deal mở không có hoạt động >14 ngày trên tổng số deal đang mở | **< 8%** | FEAT-18, 19, 20, 21 |
| `KPI-03` | Điểm nghẽn chu kỳ bán hàng | Thời gian chu kỳ bán hàng trung bình (Average Sales Cycle Length): Số ngày trung bình từ khi tạo Deal đến khi Closed Won | **Giảm 20%** sau 90 ngày | FEAT-16, 17, 34 |
| `KPI-04` | Chiết khấu vượt thẩm quyền | Tỷ lệ chiết khấu vượt ngưỡng không qua phê duyệt (Discount Overrun Rate) | **= 0% (Tuyệt đối)** | FEAT-03, 36 |
| `KPI-05` | Tỷ lệ chốt deal thành công | Tỷ lệ chốt hợp đồng (Win Rate %): Tổng số deal Closed Won chia cho tổng số deal đã đóng (Won + Lost) | **Tăng ≥ 15%** | FEAT-22, 23, 24, 25 |
| `KPI-06` | Tuân thủ quy trình bán hàng | Tỷ lệ giao dịch vượt qua kiểm soát Stage-Gate đúng chuẩn không cần ghi đè khẩn cấp | **≥ 90%** | FEAT-34 |
| `KPI-07` | Đa dạng hoá vai trò người mua | Tỷ lệ Deal B2B Enterprise có khai báo tối thiểu 2 Contact Roles (bắt buộc có Decision Maker) | **≥ 80%** | FEAT-25 |
| `KPI-08` | Cảnh báo rủi ro kỹ thuật | Tỷ lệ Deal Closed Won có vé hỗ trợ `URGENT` chưa giải quyết tại thời điểm chốt hợp đồng | **= 0%** | FEAT-33 (BR-33.2) |

---

### 2.5 Luồng nghiệp vụ đầu–cuối (End-to-End Sales Process Flow)

Quy trình quản trị cơ hội bán hàng xuyên suốt 6 giai đoạn vận hành chuẩn:

```
[GĐ 1: Khởi tạo & Định danh] 
    ├── Tiếp nhận Lead chuyển đổi (contacts-srs) HOẶC Tạo Deal thủ công
    └── Tự động gán Owner, OrgUnit, Phễu mặc định, Tiền tệ cơ sở & Kiểm tra hạn mức FLS
         │
[GĐ 2: Khảo sát Nhu cầu & Định hình Giải pháp]
    ├── Thêm Dòng Sản phẩm (Line Items), Xác định Giá trị & Ngày đóng dự kiến (Close Date)
    ├── Gắn Ma trận Nhân sự Khách hàng (Contact Roles: Decision Maker, Champion, Influencer)
    └── Đặt Lịch hẹn chăm sóc tiếp theo (nextFollowUpAt)
         │
[GĐ 3: Thẩm định & Vượt Rào cản Giai đoạn (Stage-Gates)]
    ├── Kiểm tra điều kiện vào Stage (Line items bắt buộc, File đính kèm, Khảo sát kỹ thuật)
    ├── Hệ thống tự động ghi nhận thời gian lưu giai đoạn cũ (durationMs)
    └── Kéo thả thẻ trên Bảng Kanban cập nhật tổng giá trị thời gian thực
         │
[GĐ 4: Đàm phán, Báo giá & Phê duyệt Chiết khấu]
    ├── Nhập mức chiết khấu đề xuất
    ├── NẾU chiết khấu vượt ngưỡng quy định -> Khóa Deal & Kích hoạt Luồng Duyệt Đa cấp (Manager/VP)
    └── NẾU khách hàng tạm hoãn -> Chuyển trạng thái Tạm ngưng (ON_HOLD), đóng băng Forecast & Stale
         │
[GĐ 5: Chốt Giao dịch & Xử lý Kết quả (Closing & Attribution)]
    ├── THÀNH CÔNG (Closed Won):
    │     ├── Ghi nhận wonAt, Win Probability = 100%
    │     ├── Cập nhật Vòng đời Contact/Account lên Customer (contacts-srs)
    │     └── Tự động ghi nhận doanh số vào Quota Attainment & Opportunity Splits
    └── THẤT BẠI (Closed Lost):
          ├── Bắt buộc nhập Lý do thất bại & Đối thủ cạnh tranh thắng cuộc
          └── Ghi nhận lostAt, Win Probability = 0%, loại khỏi Forecast
         │
[GĐ 6: Bàn giao Sau Bán hàng & Đo lường Hiệu suất]
    ├── Kế thừa thông tin sang Vé Triển khai (tickets-srs) và Nhiệm vụ Onboarding (tasks-srs)
    └── Xuất Báo cáo Vận tốc bán hàng, Tỷ lệ chuyển đổi phễu và Doanh thu thực tế
```

---

## 3. Đặc tả yêu cầu chức năng

## A. QUẢN TRỊ CƠ HỘI BÁN HÀNG (DEALS CRUD & LINE ITEMS)

### FEAT-01 — Tạo mới & Quản lý Cơ hội Bán hàng (Deal CRUD) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép nhân viên kinh doanh tạo mới cơ hội, liên kết với Khách hàng cá nhân (Contacts) và Doanh nghiệp (Accounts), chỉ định Phễu và Giai đoạn ban đầu.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-01.1 (Thông tin bắt buộc)`: Bắt buộc có Tên cơ hội (`title`), Phễu bán hàng (`pipelineId`) và Giai đoạn khởi đầu (`stageId`).
- `BR-01.2 (Quyền sở hữu mặc định)`: Người tạo tự động được gán làm Người phụ trách (`ownerId`) với cờ `ownerAssignedExplicitly = true`. Đơn vị tổ chức được gán theo phòng ban của người tạo (`orgUnitId`).
- `BR-01.3 (Liên kết Khách hàng & Doanh nghiệp)`: Một Deal có thể liên kết với 1 Doanh nghiệp (`accountId`) và 1 hoặc nhiều Khách hàng cá nhân (`contactId`). Nếu gắn `accountId`, hệ thống tự động đồng bộ tên công ty vào Deal.
- `BR-01.4 (Phân quyền truy cập ABAC)`: Áp dụng phạm vi dữ liệu: Cá nhân / Phòng ban / Cây phòng ban / Toàn tổ chức. Người dùng không có quyền không thể xem hoặc chỉnh sửa Deal ngoài phạm vi.

---

### FEAT-02 — Hồ sơ Chi tiết Cơ hội 360 độ (Deal 360 View) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Màn hình trung tâm quản trị cơ hội: Thanh tiến trình giai đoạn (Stage Progress Bar), bảng chỉ số tài chính, danh sách Line Items, vai trò liên hệ, nhiệm vụ liên quan và cảnh báo vé hỗ trợ.

**Actor:** Mọi người dùng có quyền xem Deal.

**Quy tắc nghiệp vụ:**
- `BR-02.1`: Cho phép nhấp trực tiếp vào các mốc trên thanh tiến trình giai đoạn để chuyển bước nhanh (tuân thủ quy tắc Stage-Gates tại FEAT-34).
- `BR-02.2`: Hiển thị rõ số ngày cơ hội đã nằm ở giai đoạn hiện tại (`stageEnteredAt`), ngày tương tác gần nhất (`lastActivityAt`) và ngày hẹn tiếp theo (`nextFollowUpAt`).

---

### FEAT-03 — Quản lý Dòng Sản phẩm & Chi tiết Giá trị (Line Items & CPQ Basics) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép đính kèm danh mục sản phẩm/dịch vụ chi tiết vào Deal để cấu thành tổng giá trị giao dịch chính xác.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh.

**Quy tắc nghiệp vụ:**
- `BR-03.1 (Cấu trúc Line Item)`: Mỗi dòng sản phẩm gồm: Mã SKU, Tên sản phẩm/dịch vụ, Đơn vị tính, Số lượng (`quantity`), Đơn giá (`unitPrice`), Tỷ lệ chiết khấu dòng (`discountPercent`), Thuế suất (`taxPercent`), và Thành tiền dòng (`lineTotal`).
- `BR-03.2 (Công thức tính toán)`: 
  $$\text{lineTotal} = \text{quantity} \times \text{unitPrice} \times \left(1 - \frac{\text{discountPercent}}{100}\right) \times \left(1 + \frac{\text{taxPercent}}{100}\right)$$
- `BR-03.3 (Tự động cập nhật tổng giá trị Deal)`: Khi có thay đổi trên Line Items, trường Tổng giá trị Deal (`value`) tự động được tính lại bằng tổng của toàn bộ các `lineTotal`. Người dùng không thể nhập đè giá trị tổng thủ công khi Deal đã có Line Items.

---

### FEAT-04 — Quản lý Đa Tiền tệ & Chốt Tỷ giá Hối đoái (Multi-Currency) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hỗ trợ giao dịch bằng nhiều loại tiền tệ (USD, SAR, VND, EUR) và quy đổi chuẩn xác về Đồng tiền Cơ sở (Base Currency) của Workspace.

**Actor:** Nhân viên Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-04.1 (Tiền tệ giao dịch)`: Mỗi Deal lưu mã tiền tệ (`currency`). Báo cáo dự báo doanh thu tự động quy đổi về Đồng tiền Cơ sở theo Bảng tỷ giá hối đoái hiện hành của tenant.
- `BR-04.2 (Chốt tỷ giá khi Đóng deal) [Yêu cầu mới]`: Khi Deal chuyển sang trạng thái kết thúc (`Closed Won` hoặc `Closed Lost`), hệ thống tự động chốt cứng tỷ giá hối đoái tại thời điểm đóng (`exchangeRateAtClose`). Mọi biến động tỷ giá trong tương lai không làm thay đổi giá trị báo cáo tài chính lịch sử của Deal đã đóng.

---

### FEAT-05 — Bảo vệ Dữ liệu Nhạy cảm Giá trị Giao dịch (Field Masking & FLS) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Bảo mật trường Giá trị (`value`), Chiết khấu và Biên lợi nhuận đối với các vai trò không thuộc bộ phận kinh doanh/tài chính.

**Actor:** Người dùng có quyền `deals:view` kết hợp chính sách trường FLS.

**Quy tắc nghiệp vụ:**
- `BR-05.1`: Tích hợp với `FieldPolicyInterceptor` của Object Manager. Người dùng không có quyền xem tài chính sẽ thấy giá trị hiển thị dạng mặt nạ `***,*** SAR`.

---

### FEAT-06 — Thùng rác Cơ hội & Phục hồi Bản ghi (Recycle Bin & Restore) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Xóa mềm cơ hội bán hàng và lưu trữ an toàn 30 ngày trong Thùng rác.

**Actor:** Quản trị viên Workspace, Người có quyền `delete` trên Deals.

**Quy tắc nghiệp vụ:**
- `BR-06.1`: Đánh dấu `deletedAt = now()`, ẩn Deal khỏi bảng Kanban, báo cáo doanh số và danh sách tìm kiếm.
- `BR-06.2`: Phục hồi Deal qua API khôi phục đúng vị trí cột Kanban và toàn bộ lịch sử trao đổi, Line Items cũ.

---

## B. QUẢN TRỊ PHỄU BÁN HÀNG & GIAI ĐOẠN (PIPELINES & STAGES)

### FEAT-07 — Quản trị Nhiều Phễu Bán hàng Độc lập `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép doanh nghiệp tạo và vận hành song song nhiều quy trình bán hàng độc lập (ví dụ: "Phễu Phần Mềm B2B", "Phễu Dịch Vụ Tư Vấn", "Phễu Khách Hàng Gia Hạn").

**Actor:** Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-07.1`: Mỗi phễu có Tên riêng, Mã định danh duy nhất và danh sách các giai đoạn riêng biệt. Hệ thống luôn duy trì đúng 1 Phễu Mặc định (`isDefault = true`).
- `BR-07.2`: Nhân viên có quyền `deals:view` được phép chọn phễu phù hợp khi tạo cơ hội mới.

---

### FEAT-08 — Thiết lập Giai đoạn & Xác suất Thành công `[Đã triển khai]`

**Mô tả nghiệp vụ:** Định nghĩa các giai đoạn tuần tự trong phễu và gán tỷ lệ % xác suất thắng chuẩn mực cho từng giai đoạn.

**Actor:** Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-08.1`: Xác suất thành công (`probability`) là số nguyên từ `0%` đến `100%`.
- `BR-08.2`: Mỗi phễu bắt buộc có 2 giai đoạn kết thúc chuẩn: **Closed Won (100% xác suất)** và **Closed Lost (0% xác suất)**.

---

### FEAT-09 — Sắp xếp Thứ tự Giai đoạn Linh hoạt `[Đã triển khai]`

**Mô tả nghiệp vụ:** Kéo thả thay đổi thứ tự tiến trình các bước bán hàng trên phễu.

**Actor:** Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-09.1`: Cập nhật trọng số thứ tự (`order`) của toàn bộ các giai đoạn trong phễu theo mảng `stageIds` được gửi lên trong 1 giao dịch nguyên tử.

---

### FEAT-10 — Lưu trữ & Di chuyển Cơ hội khi Đóng Phễu (Pipeline Archival Guard) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi ngừng sử dụng một phễu bán hàng, bắt buộc quản trị viên phải chỉ định phương án di chuyển cho toàn bộ cơ hội đang mở.

**Actor:** Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-10.1 (Ma trận Ánh xạ Giai đoạn)`: Quản trị viên thiết lập bảng ghép từng giai đoạn cũ sang giai đoạn tương ứng của phễu mới (`migrateToPipelineId`). Toàn bộ cơ hội đang mở được di chuyển tự động theo ma trận ánh xạ thay vì dồn về một giai đoạn duy nhất.
- `BR-10.2 (Đóng băng chỉ đọc)`: Nếu chọn `keepReadOnly = true`, toàn bộ cơ hội trong phễu cũ bị khóa ở trạng thái chỉ đọc để bảo toàn lịch sử.
- `BR-10.3 (Chặn xóa rỗng)`: Từ chối lệnh xóa/lưu trữ nếu phễu còn deal mở mà không chọn 1 trong 2 phương án trên.

---

### FEAT-11 — Xóa Giai đoạn & Di chuyển Cơ hội An toàn (Delete Stage Guard) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi xóa một giai đoạn, bắt buộc chỉ định giai đoạn tiếp nhận các cơ hội đang có trong giai đoạn đó.

**Actor:** Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-11.1`: Bắt buộc truyền `targetStageId` hợp lệ trong cùng phễu. Toàn bộ cơ hội được chuyển sang giai đoạn đích trước khi xóa giai đoạn cũ.

---

## C. BẢNG KANBAN CƠ HỘI TRỰC QUAN (KANBAN WORKSPACE)

### FEAT-12 — Bảng Kanban Tổng hợp Giá trị Thời gian thực (Board Summary) `[Đã triển khai]`

**Mô tả nghiệp vụ:** API siêu tối ưu tính toán chính xác tổng số lượng deal và tổng giá trị tiền tệ trên từng cột giai đoạn theo thời gian thực có áp dụng phân quyền ABAC.

**Actor:** Mọi người dùng có quyền xem Deal.

**Quy tắc nghiệp vụ:**
- `BR-12.1`: Số liệu tổng trên đầu cột luôn phản ánh chính xác 100% dữ liệu thực tế theo bộ lọc đang áp dụng.

---

### FEAT-13 — Phân trang Keyset Mượt mà theo Từng Cột Kanban `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tải danh sách thẻ cơ hội độc lập cho từng cột theo cơ chế cuộn vô tận (Keyset Cursor Pagination), mỗi lần tải 20 thẻ, đảm bảo giao diện phản hồi dưới 200ms khi phễu có hàng chục nghìn cơ hội.

**Actor:** Mọi người dùng có quyền xem Deal.

---

### FEAT-14 — Kéo thả Chuyển Giai đoạn Tức thì (Drag-and-Drop) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Nhân viên kinh doanh kéo thả thẻ Deal từ cột này sang cột khác để cập nhật tiến độ bán hàng.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh.

**Quy tắc nghiệp vụ:**
- `BR-14.1`: Kéo thả kích hoạt lệnh kiểm tra Stage-Gates (FEAT-34). Nếu đủ điều kiện, cập nhật `stageId`, ghi nhận thời gian lưu giai đoạn cũ (`durationMs`) và thêm 1 bản ghi vào `stageHistory`.

---

### FEAT-15 — Bộ lọc Thông minh Đa chiều trên Kanban `[Đã triển khai]`

**Mô tả nghiệp vụ:** Thanh lọc nhanh: "Cơ hội của tôi", "Cơ hội của phòng ban", Lọc theo Ngày đóng dự kiến (Tháng này, Quý này), Lọc theo Thẻ phân loại (Tags), và Lọc theo Cảnh báo nguội lạnh.

**Actor:** Mọi người dùng có quyền xem Deal.

---

## D. LỊCH SỬ GIAI ĐOẠN & VẬN TỐC BÁN HÀNG (VELOCITY)

### FEAT-16 — Ghi nhận Chi tiết Lịch sử Giai đoạn & Thời gian Lưu (`durationMs`) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động ghi lại thời điểm chuyển bước, người thực hiện và số mili-giây (`durationMs`) cơ hội đã nằm ở giai đoạn trước đó.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-16.1`: Cấu trúc bản ghi `stageHistory`: `{ fromStageId, toStageId, changedAt, changedById, durationMs }`. Bản ghi là Bất biến (Append-Only), không thể bị chỉnh sửa hoặc xóa.

---

### FEAT-17 — Phân tích Điểm nghẽn Quy trình & Vận tốc Bán hàng `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Báo cáo trực quan chỉ rõ: Số ngày trung bình tại từng giai đoạn, Giai đoạn có thời gian tắc nghẽn lâu nhất, Tỷ lệ rơi rụng (Drop-off Rate) và Vận tốc chu kỳ bán hàng tổng thể.

**Actor:** Quản lý Kinh doanh, Giám đốc Kinh doanh.

---

## E. CẢNH BÁO CƠ HỘI NGUỘI & NHẮC NHỞ CHĂM SÓC (STALE DEALS)

### FEAT-18 — Thiết lập Lịch Chăm sóc Tiếp theo (`nextFollowUpAt`) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Nhân viên thiết lập ngày giờ cam kết liên hệ lại khách hàng. Mốc thời gian hiển thị nổi bật trực tiếp trên thẻ Kanban (màu xanh: chưa đến hạn, màu đỏ: quá hạn).

**Actor:** Nhân viên Kinh doanh.

---

### FEAT-19 — Tiến trình Quét & Bắn Thông báo Nhắc việc Đúng hạn `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tiến trình ngầm định kỳ mỗi **5 phút** quét các Deal có `nextFollowUpAt <= now()` và `followUpNotifiedAt === null` để phát thông báo chuông In-App và thông báo đẩy tới người phụ trách.

**Actor:** Tiến trình Hệ thống.

---

### FEAT-20 — Tự động Nhận diện & Cảnh báo Cơ hội Nguội Lạnh (Stale Deals) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động phát hiện các cơ hội đang mở nhưng không có bất kỳ tương tác hoạt động nào vượt quá ngưỡng quy định (mặc định 14 ngày).

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-20.1`: Nếu `now - lastActivityAt > 14 ngày` (tham số `CFG-DEAL-02`), Deal tự động được gắn cờ Stale Deal. Thẻ Kanban hiển thị biểu tượng ngọn lửa tắt màu xám kèm số ngày bị bỏ quên.
- `BR-20.2`: Các hành vi được công nhận làm mới `lastActivityAt`: Ghi nhận cuộc gọi/họp/email, hoàn thành task liên kết, chuyển giai đoạn, nhận phản hồi từ khách hàng. Thao tác sửa đổi trường nội bộ của quản trị viên không được tính.

---

### FEAT-21 — Tự động Chạm Hoạt động Đánh thức Cơ hội (`touchActivity`) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi nhân viên ghi nhận 1 cuộc gọi, ghi chú hoặc hoàn tất 1 công việc liên quan, hệ thống tự động làm mới `lastActivityAt = now()`, lập tức xóa bỏ cờ Stale Deal và đưa cơ hội trở lại trạng thái hoạt động tích cực.

**Actor:** Tiến trình Hệ thống.

---

## F. QUẢN LÝ THẮNG/THUA & PHÂN TÍCH THẤT BẠI (WIN/LOSS)

### FEAT-22 — Quy trình Đóng Cơ hội Thành công (Closed Won) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi thương vụ chốt thành công, nhân viên chuyển Deal sang "Closed Won" và ghi nhận Lý do Thắng.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh.

**Quy tắc nghiệp vụ:**
- `BR-22.1`: Cập nhật `stageId` sang Won, `probability = 100%`, ghi nhận `wonAt = now()`.
- `BR-22.2`: Tự động nâng cấp giai đoạn vòng đời của Khách hàng cá nhân và Doanh nghiệp liên quan lên `Customer` (đồng bộ với [`contacts-srs.md`](./contacts-srs.md)).
- `BR-22.3`: Tự động ghi nhận giá trị thực tế vào bảng theo dõi Hạn ngạch doanh số (Sales Quotas) của người phụ trách.

---

### FEAT-23 — Bắt buộc Khai báo Lý do Thất bại khi Đóng Thua (Closed Lost) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi giao dịch thất bại, hệ thống bắt buộc hiển thị hộp thoại yêu cầu chọn Lý do Thất bại chuẩn hóa và Đối thủ cạnh tranh trước khi hoàn tất đóng deal.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh.

**Quy tắc nghiệp vụ:**
- `BR-23.1 (Bắt buộc khai báo)`: Bắt buộc chọn 1 Lý do thất bại từ danh mục chuẩn (`lostReasonId`), chọn Đối thủ cạnh tranh thắng cuộc (`competitorId` nếu có) và nhập giải thích chi tiết.
- `BR-23.2`: Cập nhật `stageId` sang Lost, `probability = 0%`, ghi nhận `lostAt = now()`. Deal đóng thua tự động bị loại khỏi Doanh thu dự báo.

---

### FEAT-24 — Quản lý Danh mục Lý do Thất bại & Đối thủ Cạnh tranh `[Đã triển khai]`

**Mô tả nghiệp vụ:** Quản trị viên cấu hình danh mục Lý do Thất bại (Giá quá cao, Thiếu tính năng cốt lõi, Chậm tiến độ phản hồi, Hết ngân sách năm) và danh mục Đối thủ Cạnh tranh trực tiếp trên thị trường.

**Actor:** Quản trị viên Workspace.

---

## G. VAI TRÒ LIÊN HỆ, PHÂN CHIA DOANH SỐ & NGUỒN GỐC

### FEAT-25 — Gắn Vai trò Nhân sự Mua hàng trong Cơ hội (Contact Roles) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Gắn nhiều nhân sự của khách hàng vào cùng một Deal với vai trò quyết định mua hàng cụ thể.

**Actor:** Nhân viên Kinh doanh.

**Quy tắc nghiệp vụ:**
- `BR-25.1`: Các vai trò chuẩn:
  - **Người ra quyết định (Decision Maker)** — Bắt buộc phải có tối thiểu 1 người trước khi vào giai đoạn Đàm phán.
  - **Người đánh giá kỹ thuật (Technical Evaluator)**
  - **Người bảo trợ nội bộ (Champion / Sponsor)**
  - **Người mua hàng / Kế toán (Purchaser / Billing Contact)**
  - **Người ảnh hưởng (Influencer)**
- `BR-25.2`: Mỗi Deal có duy nhất 1 Người liên hệ chính (`isPrimary = true`).

---

### FEAT-26 — Phân chia Doanh số & Hoa hồng Đồng phụ trách (Opportunity Splits) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép phân chia tỷ lệ % doanh số và hoa hồng khi có nhiều nhân viên kinh doanh cùng tham gia chăm sóc và chốt một hợp đồng lớn.

**Actor:** Quản lý Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-26.1`: Tổng tỷ lệ phân chia doanh số (`splitPercent`) giữa các nhân viên trong một Deal bắt buộc phải bằng chính xác **100%**.
- `BR-26.2`: Báo cáo doanh số và hạn ngạch (Quota) tự động tính toán doanh thu được ghi nhận cho từng nhân viên theo đúng tỷ lệ phân chia này.

---

### FEAT-27 — Theo dõi Nguồn gốc Tiếp thị & Chiến dịch (Campaign Attribution) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động kế thừa các tham số nguồn gốc tiếp thị (`utmSource`, `utmMedium`, `utmCampaign`, `campaignId`) từ khách hàng tiềm năng ban đầu sang bản ghi Deal để đo lường ROI chiến dịch marketing.

**Actor:** Tiến trình Hệ thống.

---

## H. DỰ BÁO DOANH THU & HẠN NGẠCH DOANH SỐ (FORECASTING & QUOTA)

### FEAT-28 — Động cơ Dự báo Doanh thu có Trọng số (Weighted Forecast) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Tự động tính toán doanh thu kỳ vọng theo thời gian thực dựa trên giá trị và xác suất thành công của từng Deal đang mở trong kỳ tài chính.

**Actor:** Quản lý Kinh doanh, Giám đốc Kinh doanh.

**Công thức tính toán:**
$$\text{Doanh thu Dự báo Kỳ vọng} = \sum_{i=1}^{n} \left( \text{Giá trị Deal}_i \times \text{Xác suất Giai đoạn}_i \right)$$

---

### FEAT-29 — Quản trị Nhóm Dự báo Doanh thu (Forecast Categories) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Phân loại độ tin cậy của các Deal trong kỳ tài chính thành 5 nhóm chuẩn quốc tế:
1. **`PIPELINE`:** Cơ hội mới ở giai đoạn đầu, xác suất thấp.
2. **`BEST_CASE`:** Cơ hội có triển vọng tốt, có thể chốt nếu mọi điều kiện thuận lợi.
3. **`COMMIT`:** Cơ hội cam kết chắc chắn chốt 100% trong kỳ tài chính hiện tại.
4. **`CLOSED`:** Cơ hội đã chốt thành công (`Closed Won`).
5. **`OMITTED`:** Cơ hội bị loại khỏi dự báo (Deal thất bại hoặc Deal tạm ngưng).

**Actor:** Nhân viên Kinh doanh (đề xuất), Quản lý Kinh doanh (thẩm định).

---

### FEAT-30 — Báo cáo Chuyển đổi Phễu & Quản lý Hạn ngạch Doanh số (Sales Quotas) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Thiết lập mục tiêu doanh số (Quota) theo Tháng/Quý cho từng nhân viên và phòng ban; theo dõi tỷ lệ hoàn thành chỉ tiêu (% Quota Attainment) theo thời gian thực.

**Actor:** Giám đốc Kinh doanh, Quản lý Kinh doanh.

---

## I. THAO TÁC HÀNG LOẠT & NHẬP/XUẤT DỮ LIỆU (BULK & IMPORT/EXPORT)

### FEAT-31 — Thao tác Cập nhật, Gắn thẻ & Xóa Hàng loạt `[Đã triển khai]`

**Mô tả nghiệp vụ:** Chọn nhiều Deal cùng lúc để chuyển giai đoạn, đổi người phụ trách, gắn thẻ phân loại hoặc xóa hàng loạt an toàn.

**Actor:** Quản lý Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-31.1`: Kiểm tra phân quyền trên từng ID. Các bản ghi không đủ quyền sẽ được đưa vào danh sách `skipped` kèm lý do thay vì làm gián đoạn toàn bộ yêu cầu.

---

### FEAT-32 — Nhập / Xuất Danh sách Cơ hội Dung lượng lớn 50MB `[Đã triển khai]`

**Mô tả nghiệp vụ:** Nhập danh sách cơ hội từ tệp Excel/CSV qua hàng đợi BullMQ có báo cáo lỗi chi tiết, hoặc xuất dữ liệu Deal ra tệp CSV bảo mật qua token tải về có thời hạn 24 giờ.

**Actor:** Quản trị viên Workspace, Người có quyền `export`/`import` trên Deals.

---

## J. DÒNG THỜI GIAN HOẠT ĐỘNG & TÍCH HỢP VÉ HỖ TRỢ

### FEAT-33 — Dòng thời gian Hợp nhất & Cảnh báo Vé Hỗ trợ Khẩn cấp `[Đã triển khai]`

**Mô tả nghiệp vụ:** Lưu trữ toàn bộ lịch sử tương tác trên Deal và hiển thị danh sách các Vé hỗ trợ kỹ thuật (`tickets`) liên quan đến khách hàng của Deal đó.

**Actor:** Nhân viên Kinh doanh, Nhân viên Hỗ trợ.

**Quy tắc nghiệp vụ:**
- `BR-33.1`: Hiển thị toàn bộ lịch sử cuộc gọi, ghi chú, email và thay đổi giai đoạn trên dòng thời gian 360 độ.
- `BR-33.2 (Cảnh báo Rủi ro Kỹ thuật trên Kanban) [Yêu cầu mới]`: Nếu một Deal có ít nhất một Vé hỗ trợ kỹ thuật đang mở ở mức độ ưu tiên `URGENT` hoặc `HIGH`, thẻ Deal trên bảng Kanban tự động hiển thị biểu tượng cờ cảnh báo màu vàng để nhắc nhở nhân viên kinh doanh phối hợp giải quyết rào cản kỹ thuật trước khi đàm phán chốt hợp đồng.

---

## K. KIỂM SOÁT QUY TRÌNH TIẾN ĐỘ BÁN HÀNG NÂNG CAO

### FEAT-34 — Rào cản Điều kiện Chuyển Giai đoạn (Stage-Gates / Entry Requirements) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép Quản trị viên cấu hình danh sách điều kiện bắt buộc phải thỏa mãn trước khi Deal được phép chuyển sang giai đoạn tiếp theo, đảm bảo tính trung thực của đường ống bán hàng.

**Actor:** Quản trị viên Workspace (cấu hình), Nhân viên Kinh doanh (tuân thủ), Quản lý Kinh doanh (ghi đè khẩn cấp).

**Quy tắc nghiệp vụ:**
- `BR-34.1 (Cấu hình điều kiện)`: Mỗi giai đoạn có thể cấu hình các điều kiện:
  - **Trường bắt buộc phải điền:** Ví dụ vào giai đoạn "Báo giá" bắt buộc phải có ít nhất 1 Line Item và Ngày đóng dự kiến.
  - **Tài liệu bắt buộc đính kèm:** Ví dụ vào giai đoạn "Closed Won" bắt buộc phải có file scan Hợp đồng đã ký.
  - **Vai trò bắt buộc khai báo:** Vào giai đoạn "Đàm phán" bắt buộc phải khai báo ít nhất 1 Contact Role là `Decision Maker`.
- `BR-34.2 (Chặn chuyển giai đoạn)`: Khi người dùng kéo thả hoặc chuyển bước không đủ điều kiện, hệ thống từ chối thao tác và hiển thị danh sách chi tiết các điều kiện còn thiếu.
- `BR-34.3 (Quyền Ghi đè Khẩn cấp - Stage-Gate Override)`: Chỉ Quản lý Kinh doanh và Quản trị viên mới có quyền "Ghi đè điều kiện" (Override) kèm bắt buộc nhập lý do giải trình; hành động ghi đè được lưu vĩnh viễn vào Nhật ký kiểm toán (Audit Log).

---

### FEAT-35 — Trạng thái Tạm ngưng Cơ hội (On Hold Deal Status) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép đặt Deal vào trạng thái "Tạm ngưng" khi khách hàng hoãn kế hoạch mua sắm ngoài ý muốn (hết ngân sách năm, thay đổi nhân sự phê duyệt), không làm méo mó dự báo doanh thu và không bị báo động giả về cơ hội nguội.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh.

**Quy tắc nghiệp vụ:**
- `BR-35.1 (Chuyển On Hold)`: Bắt buộc nhập: (a) Lý do tạm ngưng từ danh mục chuẩn (Chờ ngân sách năm mới, Đổi ban lãnh đạo, Tái cấu trúc nội bộ); (b) Ngày dự kiến tái kích hoạt (`expectedReactivationDate`).
- `BR-35.2 (Đóng băng Dự báo & Cảnh báo Nguội)`: Deal ở trạng thái `ON_HOLD` **tự động bị loại khỏi** Doanh thu Dự báo có Trọng số (Weighted Forecast) và **đóng băng** cơ chế cảnh báo Stale Deal trong suốt thời gian tạm ngưng.
- `BR-35.3 (Cột Tạm ngưng trên Kanban)`: Hiển thị trong một cột riêng biệt "Tạm ngưng" trên bảng Kanban.
- `BR-35.4 (Nhắc nhở Tái kích hoạt)`: Khi đến ngày `expectedReactivationDate`, hệ thống gửi thông báo nhắc nhở Người phụ trách và Quản lý để xem xét mở lại hoặc gia hạn tạm ngưng.
- `BR-35.5 (Tái kích hoạt)`: Khi kích hoạt lại, Deal quay trở về đúng giai đoạn phễu trước khi tạm ngưng và kích hoạt lại cơ chế đếm ngày hoạt động.

---

### FEAT-36 — Quy trình Phê duyệt Chiết khấu Đa cấp (Discount Approval Workflow) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Kiểm soát chặt chẽ biên lợi nhuận, bắt buộc trình cấp quản lý phê duyệt khi mức chiết khấu trên Deal vượt quá hạn mức cho phép.

**Actor:** Nhân viên Kinh doanh (trình duyệt), Quản lý Kinh doanh (duyệt cấp 1/2), Giám đốc Kinh doanh (duyệt cấp cao), Quản trị viên (cấu hình).

**Quy tắc nghiệp vụ:**
- `BR-36.1 (Phân cấp Ngưỡng Phê duyệt)`:
  - **Mức 1 (Chiết khấu $\le 10\%$):** Nhân viên kinh doanh tự quyết định.
  - **Mức 2 (Chiết khấu $10\% - 25\%$):** Bắt buộc phê duyệt của Quản lý Kinh doanh (Sales Manager).
  - **Mức 3 (Chiết khấu $> 25\%$):** Bắt buộc phê duyệt của Giám đốc Kinh doanh (VP of Sales).
- `BR-36.2 (Khóa bản ghi khi Chờ duyệt)`: Khi gửi yêu cầu duyệt chiết khấu, Deal chuyển sang trạng thái `PENDING_DISCOUNT_APPROVAL`. Trường Giá trị, Chiết khấu và Line Items bị khóa ở chế độ Chỉ đọc (Read-only) cho đến khi có quyết định phê duyệt.
- `BR-36.3 (Xử lý Kết quả Duyệt)`:
  - **Duyệt (Approved):** Deal mở khóa, áp dụng mức chiết khấu đã duyệt, cho phép nhân viên tiếp tục đàm phán và kết xuất Báo giá.
  - **Từ chối (Rejected):** Deal mở khóa, mức chiết khấu tự động hoàn về mức an toàn trước đó, gửi thông báo kèm lý do từ chối của người duyệt tới nhân viên.
- `BR-36.4 (Nhật ký Kiểm toán Chiết khấu)`: Toàn bộ lịch sử yêu cầu, thời điểm duyệt, người duyệt và lý do giải trình được lưu vĩnh viễn trong Audit Log.

---

## 4. Yêu cầu phi chức năng

### 4.1 Hiệu năng & Khả năng đáp ứng (Performance)
- **NFR-01 (Tốc độ Bảng Kanban):** API Board Summary và tải danh sách thẻ Kanban phản hồi dưới **200ms** (p95) trên phễu có 50,000 cơ hội bán hàng.
- **NFR-02 (Tối ưu hóa Phân trang Keyset):** Hiệu năng cuộn tải thẻ Kanban không bị suy giảm khi người dùng cuộn đến trang thứ 100.
- **NFR-03 (Tốc độ Báo cáo Dự báo Doanh số):** Báo cáo Weighted Pipeline và Sales Leaderboard phản hồi dưới **400ms** qua cơ chế tổng hợp chỉ mục cơ sở dữ liệu tối ưu.

### 4.2 Độ tin cậy & Toàn vẹn Dữ liệu (Reliability & ACID)
- **NFR-04 (An toàn Giao dịch Di chuyển Phễu):** Thao tác di chuyển hàng loạt cơ hội khi lưu trữ phễu thực thi trong 1 Database Transaction nguyên tử duy nhất.
- **NFR-05 (Tính Bất biến của Lịch sử Giai đoạn):** Bản ghi `stageHistory` và Nhật ký duyệt chiết khấu là bất biến (Append-Only), không thể bị chỉnh sửa hoặc xóa bỏ.

### 4.3 An toàn & Bảo mật (Security)
- **NFR-06 (Bảo mật Phân quyền ABAC & FLS):** Áp dụng nghiêm ngặt phạm vi dữ liệu và mặt nạ bảo vệ trường tài chính nhạy cảm. Nhân viên không có quyền không thể xem giá trị deal hoặc kéo thả deal ngoài phạm vi.
- **NFR-07 (Bảo mật Tệp Xuất Dữ liệu):** Đường dẫn tải file export được mã hóa bằng token dùng 1 lần, hết hạn sau 24 giờ.

---

## 5. Ma trận quyền truy cập tính năng

| Mã FEAT | Tên tính năng nghiệp vụ | Sales Rep | Account Mgr | Pre-sales/Collab | Sales Mgr | VP of Sales | Tenant Admin | Tenant Owner |
| --- | --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `FEAT-01` | Tạo & Quản lý Deal | Scope gán | Scope gán | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-02` | Hồ sơ Chi tiết Deal 360 | Scope gán | Scope gán | Scope deal gán | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-03` | Quản lý Line Items | Scope gán | Scope gán | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-04` | Đa Tiền tệ & Tỷ giá | Scope gán | Scope gán | Scope deal gán | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-05` | Xem Giá trị Nhạy cảm FLS | Có quyền* | Có quyền* | — | Có quyền* | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-06` | Thùng rác & Phục hồi Deal | — | — | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-07` | Xem & Quản trị Pipelines | Xem DS | Xem DS | Xem DS | Xem DS | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-08` | Cấu hình Giai đoạn & Xác suất | — | — | — | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-09` | Sắp xếp Thứ tự Giai đoạn | — | — | — | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-10` | Lưu trữ & Di chuyển Phễu | — | — | — | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-11` | Xóa Giai đoạn & Chuyển Deal | — | — | — | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-12` | Bảng Kanban Board Summary | Scope gán | Scope gán | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-13` | Tải thẻ Cột Kanban Column | Scope gán | Scope gán | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-14` | Kéo thả Chuyển Giai đoạn | Scope gán | Scope gán | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-15` | Bộ lọc Thông minh Kanban | **Cho phép** | **Cho phép** | — | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-16` | Ghi nhận Lịch sử Giai đoạn | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-17` | Báo cáo Vận tốc Bán hàng | — | — | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-18` | Đặt Lịch Hẹn Follow-up | Scope gán | Scope gán | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-19` | Nhận Thông báo Nhắc việc | **Cho phép** | **Cho phép** | — | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-20` | Nhận diện Cơ hội Nguội | **Cho phép** | **Cho phép** | — | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-21` | Đánh thức Deal (`touchActivity`)| Scope gán | Scope gán | Scope deal gán | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-22` | Đóng Deal Thắng (Won) | Scope gán | Scope gán | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-23` | Đóng Deal Thua (Lost) | Scope gán | Scope gán | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-24` | Cấu hình Lý do Thất bại | — | — | — | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-25` | Gắn Vai trò Mua hàng | Scope gán | Scope gán | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-26` | Phân chia Doanh số (Splits) | Scope gán | Scope gán | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-27` | Xem Nguồn gốc UTM | Scope gán | Scope gán | Scope deal gán | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-28` | Dự báo Doanh thu có Trọng số | — | — | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-29` | Quản lý Forecast Category | Scope gán | Scope gán | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-30` | Báo cáo Phễu & Hạn ngạch | Scope gán | Scope gán | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-31` | Thao tác Hàng loạt Bulk | — | — | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-32` | Nhập / Xuất Excel 50MB | — | — | — | Có quyền* | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-33` | Dòng thời gian & Cảnh báo Vé| Scope gán | Scope gán | Scope deal gán | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-34` | Cấu hình & Vượt Stage-Gates | Bị áp dụng | Bị áp dụng | — | Duyệt Override| Duyệt Override| Cấu hình | **Toàn quyền** |
| `FEAT-35` | Tạm ngưng Deal (On Hold) | Scope gán | Scope gán | — | Scope PB | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-36` | Phê duyệt Chiết khấu Đa cấp | Trình duyệt| Trình duyệt| — | Duyệt $\le 25\%$ | Duyệt $> 25\%$ | Cấu hình | **Toàn quyền** |

*(Ghi chú: Scope gán = Chỉ bản ghi được phân công trực tiếp; Scope PB = Toàn bộ bản ghi trong phòng ban quản lý; Scope deal gán = Chỉ xem bản ghi được thêm vào Đội ngũ cộng tác).*

---

## 6. Kịch bản chấp nhận tổng hợp (UAT)

### Kịch bản 1: Tạo mới Deal, Gắn Line Items & Kéo thả Kanban Chốt Thắng (Closed Won)
1. Nhân viên kinh doanh tạo Deal mới: "Hợp đồng Bản quyền CRM Enterprise", gắn Doanh nghiệp "Công ty Cổ phần Đại Phát", Ngày dự kiến đóng `30/09/2026`.
2. Đính kèm 2 Line Items: (a) Gói Enterprise 50 Users $\times$ 5,000,000 VND; (b) Gói Onboarding $\times$ 50,000,000 VND. Tổng giá trị Deal tự động tính: 300,000,000 VND.
3. Kéo thả Deal qua các giai đoạn trên Kanban: "Tiếp cận" $\rightarrow$ "Trình bày giải pháp" $\rightarrow$ "Closed Won".
4. **Kỳ vọng:** Trạng thái Deal chuyển sang Won, xác suất 100%, ghi nhận ngày thắng `wonAt = now()`, đồng bộ giai đoạn vòng đời khách hàng liên quan lên `Customer`, tỷ giá quy đổi được chốt cứng.

---

### Kịch bản 2: Bắt buộc Khai báo Lý do Thất bại & Đối thủ Cạnh tranh khi Closed Lost
1. Nhân viên kéo một Deal trị giá 200,000,000 VND vào cột "Closed Lost".
2. **Kỳ vọng giao diện:** Hiển thị hộp thoại bắt buộc: Chọn Lý do thất bại (chọn "Giá cao hơn ngân sách"), Chọn Đối thủ thắng cuộc (chọn "Đối thủ X") và nhập ghi chú giải trình.
3. Bấm "Xác nhận đóng thất bại".
4. **Kỳ vọng hệ thống:** Deal chuyển sang trạng thái Lost, xác suất về 0%, tự động loại khỏi Weighted Forecast và ghi nhận vào Báo cáo phân tích Thua thầu.

---

### Kịch bản 3: Đặt Lịch Chăm sóc & Nhận Thông báo Nhắc việc Tự động
1. Nhân viên thiết lập lịch chăm sóc tiếp theo: `nextFollowUpAt = 09:00 sáng mai`. Thẻ Kanban hiển thị dòng chữ màu xanh: "Lịch hẹn: 09:00 ngày mai".
2. Đến đúng 09:00 sáng hôm sau, tiến trình `DealFollowUpService` quét phát hiện lịch hẹn đến hạn.
3. **Kỳ vọng:** Hệ thống phát chuông thông báo In-App trên màn hình nhân viên, thẻ Kanban chuyển sang màu cam nhắc nhở.

---

### Kịch bản 4: Cảnh báo Cơ hội Nguội Lạnh & Đánh thức Hoạt động (`touchActivity`)
1. Một Deal nằm yên ở giai đoạn "Báo giá" suốt 16 ngày không có bất kỳ tương tác nào.
2. Thẻ Deal tự động hiển thị biểu tượng ngọn lửa xám kèm cảnh báo: "16 ngày không có hoạt động".
3. Nhân viên gọi điện cho khách hàng và bấm "Ghi nhận cuộc gọi" trên hồ sơ Deal, nhập nội dung trao đổi.
4. **Kỳ vọng:** `lastActivityAt` làm mới về thời điểm hiện tại, cờ cảnh báo nguội lập tức biến mất trên bảng Kanban.

---

### Kịch bản 5: Chặn Chuyển Giai đoạn khi Chưa Đạt Rào cản Stage-Gate
1. Quản trị viên cấu hình Stage-Gate cho giai đoạn "Đàm phán hợp đồng": Bắt buộc phải có ít nhất 1 Line Item và tối thiểu 1 Contact Role là `Decision Maker`.
2. Nhân viên kéo thẻ Deal (chưa gắn Decision Maker) từ "Báo giá" sang "Đàm phán".
3. **Kỳ vọng:** Hệ thống chặn thao tác kéo thả, giữ nguyên vị trí Deal ở cột cũ và hiển thị thông báo lỗi: *"Không thể chuyển giai đoạn: Chưa khai báo Người ra quyết định (Decision Maker)!"*.

---

### Kịch bản 6: Luồng Phê duyệt Chiết khấu Vượt Thẩm quyền (30% Discount)
1. Nhân viên kinh doanh nhập mức chiết khấu 30% cho hợp đồng trị giá 500 triệu (vượt ngưỡng thẩm quyền của Sales Rep và Sales Manager).
2. Hệ thống chuyển Deal sang trạng thái `PENDING_DISCOUNT_APPROVAL`, khóa trường Giá trị ở chế độ Chỉ đọc, tự động gửi thông báo duyệt tới Giám đốc Kinh doanh (VP of Sales).
3. VP of Sales mở hồ sơ Deal, xem giải trình và bấm "Phê duyệt chiết khấu".
4. **Kỳ vọng:** Deal mở khóa, áp dụng mức chiết khấu 30%, ghi nhận lịch sử duyệt vào Audit Log và gửi thông báo thành công tới nhân viên.

---

### Kịch bản 7: Tạm ngưng Cơ hội (On Hold) & Tái kích hoạt
1. Khách hàng thông báo hoãn dự án sang Quý 1 năm sau do chờ duyệt ngân sách. Nhân viên chuyển trạng thái Deal sang `ON_HOLD`, chọn lý do "Chờ ngân sách năm mới" và đặt ngày tái kích hoạt `15/01/2027`.
2. **Kỳ vọng:** Deal chuyển sang cột "Tạm ngưng" trên Kanban, không bị tính vào Dự báo doanh thu tháng này và không bị cảnh báo Stale Deal.
3. Đến ngày 15/01/2027, hệ thống bắn thông báo nhắc nhở; nhân viên bấm "Tái kích hoạt" -> Deal quay về đúng giai đoạn "Đàm phán" trước đó.

---

### Kịch bản 8: Phân chia Doanh số Đồng phụ trách (Opportunity Splits)
1. Deal trị giá 1 tỷ được chốt thành công bởi sự phối hợp giữa Nhân viên A (70% công sức) và Nhân viên B (30% hỗ trợ kỹ thuật chuyên sâu).
2. Quản lý thiết lập tỷ lệ phân chia: Nhân viên A = 70%, Nhân viên B = 30%.
3. **Kỳ vọng:** Báo cáo Hạn ngạch (Quota Attainment) ghi nhận 700 triệu doanh số cho Nhân viên A và 300 triệu cho Nhân viên B.

---

## 7. Giới hạn hiện tại & Vấn đề chính sách cần quyết định tiếp

1. **Tự động Đồng bộ Báo giá sang Hệ thống ERP / Kế toán (ERP Invoicing Sync):**
   - *Vấn đề:* Khi Deal chuyển sang `Closed Won`, có tự động đẩy dữ liệu sang SAP / Fast / MISA để tạo Đơn hàng / Hóa đơn tài chính không?
   - *Đề xuất PM:* Tích hợp qua phân hệ Webhook & Integration Gateway trong phiên bản 5.2.
2. **Chính sách Tự động Đóng Deal Quá hạn (Auto-Close Expired Deals Policy):**
   - *Vấn đề:* Có nên tự động chuyển Deal sang `Closed Lost` nếu quá ngày dự kiến đóng >60 ngày mà không có hoạt động?
   - *Quyết định chốt:* **Không tự động đóng** để tránh can thiệp ngoài ý muốn của sales; áp dụng cảnh báo Stale Deal để cấp Quản lý chủ động rà soát.

---

## 8. Nhật ký 10 Vòng Soát xét & Đối chiếu Chéo (10-Round Review Log)

| Vòng | Lăng kính Soát xét (Review Lens) | Phát hiện & Vấn đề xử lý | Kết quả Chuẩn hoá |
| :---: | :--- | --- | --- |
| **V1** | **Domain & Thuật ngữ Bán hàng** | Thiếu định nghĩa rõ ràng giữa Forecast Categories và Stages; thuật ngữ tiền tệ chưa chuẩn ISO. | Chuẩn hoá 5 Forecast Categories chuẩn quốc tế và mã ISO 4217. |
| **V2** | **Stage-Gates & Vận tốc Bán hàng** | Kéo thả Kanban không kiểm soát điều kiện dữ liệu dẫn đến rác phễu; thiếu đo lường thời gian lưu. | Bổ sung FEAT-34 (Stage-Gates) và tính toán `durationMs` tại FEAT-16. |
| **V3** | **Toàn vẹn Tài chính & CPQ** | Deal chỉ có 1 trường giá trị tổng thô, không theo dõi được cơ cấu sản phẩm và thuế/chiết khấu. | Bổ sung FEAT-03 (Line Items) và công thức tính toán tài chính tự động. |
| **V4** | **Dự báo Doanh thu & Đa tiền tệ** | Tỷ giá biến động làm sai lệch báo cáo doanh thu lịch sử của các Deal đã đóng từ năm trước. | Bổ sung BR-04.2 (Chốt cứng tỷ giá hối đoái tại thời điểm Closed Won/Lost). |
| **V5** | **Quản trị Rủi ro & Duyệt Chiết khấu**| Sales tự ý giảm giá sâu để chạy chỉ số mà không có sự kiểm soát của cấp quản lý. | Bổ sung FEAT-36 (Quy trình Phê duyệt Chiết khấu Đa cấp 3 tầng). |
| **V6** | **Cơ hội Nguội & Đóng băng Forecast**| Deal bị khách hoãn làm méo mó dự báo doanh số và liên tục bị báo động đỏ gây phiền toái. | Bổ sung FEAT-35 (Trạng thái Tạm ngưng On Hold và loại khỏi Forecast). |
| **V7** | **Tích hợp Chéo Liên Phân hệ** | Sales không biết khách hàng đang có khiếu nại gay gắt khi đàm phán chốt hợp đồng. | Bổ sung BR-33.2 (Cảnh báo cờ vàng trên Kanban khi có Ticket URGENT mở). |
| **V8** | **Phân quyền ABAC & Đội ngũ Deal** | Pre-sales và Pháp chế cần tham gia hỗ trợ deal nhưng bị chặn hoặc phải cấp quyền quá rộng. | Bổ sung vai trò Deal Collaborator (Xem/Ghi chú, không sửa giá/giai đoạn). |
| **V9** | **An toàn Di chuyển Phễu (Archival)**| Xóa phễu cũ làm mất dấu các deal đang mở hoặc dồn hết vào 1 giai đoạn không tương thích. | Bổ sung BR-10.1 (Ma trận Ánh xạ Giai đoạn khi di chuyển phễu). |
| **V10**| **Nghiệm thu Khách hàng & UAT** | Thiếu kịch bản UAT đa tiền tệ, Stage-Gate override và chia sẻ hoa hồng đồng phụ trách. | Hoàn thiện bộ 18 kịch bản UAT đầu-cuối và 36 tham số cấu hình tenant. |

---

## Phụ lục A: Danh mục Dữ liệu Chuẩn (Data Dictionary)

| Thực thể (Entity) | Trường dữ liệu chính | Kiểu dữ liệu | Ý nghĩa nghiệp vụ |
| --- | --- | --- | --- |
| **`Deal`** | `id`, `title`, `pipelineId`, `stageId`, `value`, `currency`, `closeDate`, `ownerId`, `orgUnitId`, `contactId`, `accountId`, `status` (`OPEN`, `WON`, `LOST`, `ON_HOLD`), `lostReasonId`, `wonAt`, `lostAt`, `expectedReactivationDate` | Object / Record | Bản ghi cơ hội bán hàng trung tâm. |
| **`DealLineItem`** | `id`, `dealId`, `sku`, `productName`, `quantity`, `unitPrice`, `discountPercent`, `taxPercent`, `lineTotal` | Array / Sub-document | Chi tiết từng sản phẩm/dịch vụ cấu thành giá trị deal. |
| **`StageHistory`** | `fromStageId`, `toStageId`, `changedAt`, `changedById`, `durationMs` | Array / Embedded | Lịch sử bất biến ghi nhận thời gian lưu tại từng giai đoạn. |
| **`ContactRole`** | `contactId`, `role` (`DECISION_MAKER`, `CHAMPION`, `TECHNICAL_EVALUATOR`, `PURCHASER`, `INFLUENCER`), `isPrimary` | Array / Embedded | Ma trận vai trò nhân sự mua hàng của khách hàng. |
| **`OpportunitySplit`** | `userId`, `splitPercent`, `splitAmount` | Array / Embedded | Tỷ lệ phân chia doanh số và hoa hồng giữa các nhân viên. |
| **`DiscountApproval`**| `id`, `dealId`, `requestedDiscountPercent`, `requestedById`, `approverId`, `status` (`PENDING`, `APPROVED`, `REJECTED`), `reason`, `actionAt` | Object / Record | Bản ghi nhật ký phê duyệt chiết khấu. |

---

## Phụ lục B: Danh mục Tham số Cấu hình theo Không gian làm việc

| Mã tham số | Tên tham số cấu hình | Kiểu dữ liệu | Giá trị mặc định | Ý nghĩa nghiệp vụ |
| --- | --- | :---: | :---: | --- |
| `CFG-DEAL-01` | Đồng tiền Cơ sở của Không gian làm việc | String (ISO) | `SAR` / `VND` | Tiền tệ chuẩn dùng hợp nhất báo cáo doanh số toàn công ty. |
| `CFG-DEAL-02` | Ngưỡng Số ngày Cảnh báo Cơ hội Nguội Lạnh | Number (Days) | `14` | Số ngày không hoạt động để gắn cờ Stale Deal. |
| `CFG-DEAL-03` | Chu kỳ Quét Lịch hẹn Chăm sóc Tiếp theo | Number (Mins) | `5` | Chu kỳ tiến trình quét gửi thông báo nhắc việc. |
| `CFG-DEAL-04` | Ngưỡng Chiết khấu Cần Quản lý Duyệt (Mức 1) | Number (%) | `10` | Mức chiết khấu bắt buộc trình Sales Manager duyệt. |
| `CFG-DEAL-05` | Ngưỡng Chiết khấu Cần Giám đốc Duyệt (Mức 2) | Number (%) | `25` | Mức chiết khấu bắt buộc trình VP of Sales duyệt. |
| `CFG-DEAL-06` | Bắt buộc Khai báo Line Items khi Chuyển Giai đoạn Báo giá | Boolean | `true` | Rào cản Stage-Gate kiểm soát giá trị hợp đồng. |
| `CFG-DEAL-07` | Bắt buộc Khai báo Decision Maker trước khi Đàm phán | Boolean | `true` | Rào cản Stage-Gate đảm bảo tiếp cận đúng người mua. |
| `CFG-DEAL-08` | Thời hạn Lưu trữ Cơ hội trong Thùng rác | Number (Days) | `30` | Số ngày trước khi xóa vĩnh viễn Deal trong Thùng rác. |
