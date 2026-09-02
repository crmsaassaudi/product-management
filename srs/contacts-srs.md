# SRS — Phân hệ Quản lý Khách hàng & Danh bạ Doanh nghiệp (Contacts & Accounts Management)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA (Version 6.4) |
| **Module** | CRM — Phân hệ Quản lý Khách hàng & Danh bạ Doanh nghiệp (Contacts & Accounts Management) |
| **Ngày cập nhật** | 2026-09-02 |
| **Phiên bản** | v6.4 — **Nội dung đã hội tụ, phần soạn thảo hoàn tất** (xác nhận bởi hai vòng soát độc lập liên tiếp: vòng 16 và vòng 17 đều kết luận không còn lỗi mức Nặng, vòng 17 cũng không còn lỗi Trung bình). |
| **Trạng thái sử dụng** | **Giai đoạn hiện tại — căn cứ thiết kế & phát triển.** Hai nhóm việc được **hoãn có chủ đích** theo quyết định của chủ tài liệu ngày **2026-09-02** vì chưa cần ở giai đoạn này: (a) ba điều kiện chặn ban hành cần Pháp chế xác nhận (#9, #10, #11 — mục 7) và (b) toàn bộ vòng ký phê duyệt (mục 10). Đây là **quyết định về tiến độ, không phải thiếu sót của tài liệu**; ranh giới được phép và chưa được phép dùng tài liệu trong thời gian hoãn nêu tại mục 7 và mục 10. |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`object-manager-srs.md`](./object-manager-srs.md), [`omnichat-srs.md`](./omnichat-srs.md), [`onboarding-srs.md`](./onboarding-srs.md), [`deals-pipeline-srs.md`](./deals-pipeline-srs.md), [`tickets-srs.md`](./tickets-srs.md) |

## Ghi chú về nguồn gốc tài liệu

Tài liệu này được xây dựng thông qua quy trình chuẩn hoá 4 bước:
1. **Khảo sát toàn diện mã nguồn thực tế (As-Is):** Khảo sát toàn bộ hệ thống API, schemas, workers, merge services, scoring engines, import/export processors và logic phân quyền của `crm-api` (`src/contacts/`, `src/accounts/`) và `crm-web` (`src/features/contacts/`, `src/features/accounts/`).
2. **Rà soát & Đối chiếu Chuyên sâu:** Kiểm tra chéo từng quy tắc xử lý trùng lặp, sổ cái hoàn tác gộp (Unmerge Ledger), ma trận vòng đời khách hàng, quan hệ đa tổ chức (Multi-Affiliations) và bảo vệ dữ liệu nhạy cảm (Field Masking).
3. **Chuẩn hoá Nghiệp vụ Business First:** Đối chiếu với các chuẩn mực B2B SaaS CRM quốc tế (HubSpot Contacts, Salesforce Lead/Contact/Account Architecture, Pipedrive People/Organizations, Zoho CRM), loại bỏ các giới hạn kỹ thuật và bổ sung các quy trình chuyển đổi khách hàng tiềm năng (Lead Conversion) chuẩn mực.
4. **Đóng băng Đặc tả Mục tiêu:** Hoàn thiện bộ 36 tính năng nghiệp vụ cốt lõi, bộ chỉ số thành công đo lường được, ma trận chuyển đổi vòng đời khách hàng, ma trận phân quyền (7 cột vai trò hệ thống; 3 vai trò chức năng áp quyền theo vai trò gốc — Ghi chú 4 mục 5), 22 kịch bản UAT, danh mục dữ liệu chuẩn 19 mục (Phụ lục A) và danh mục 36 tham số cấu hình theo tenant (Phụ lục B).

**Nguyên tắc thiết kế nền tảng:** Mọi quy tắc nghiệp vụ có nhiều hướng xử lý hợp lý đều được triển khai thành **tham số cấu hình theo từng không gian làm việc**, với giá trị mặc định là hướng chuẩn hệ thống tại thời điểm thiết kế tài liệu này; tenant tự điều chỉnh cho phù hợp nghiệp vụ riêng. Các quy tắc liên quan nghĩa vụ pháp lý và toàn vẹn dữ liệu được đánh dấu **"sàn bắt buộc"** hoặc **"cố định"** — tenant không được nới lỏng. Toàn bộ danh mục tham số tại Phụ lục B.

**Ghi chú lịch sử soát xét:** Xem chi tiết tại mục 9. Từ vòng 3 trở đi mỗi vòng dùng hai bản review độc lập song song với hai lăng kính khác nhau (vận hành kinh doanh thực tế / nhất quán nội bộ, và từ vòng 4 thêm lăng kính mô phỏng vòng ký phê duyệt). Tài liệu đã qua **17 vòng review chéo PM/BA độc lập**. Số lỗi phát hiện theo vòng: 60 → 55 → 42 → 34 → 26 → 28 → 25 → 26 → 14 → 15 → 7 → 10 → 11 → 7 → 3 (vòng 3 → vòng 17). Số lỗi do **chính lượt sửa trước** tạo ra, theo đúng con số ghi ở mục 9 cho từng vòng — vòng 5: 18 · vòng 6: 9 · vòng 7: 8 · vòng 8: 6 · vòng 10: 4 · vòng 12: 4 · vòng 13: 4 · vòng 14: 4 · vòng 15: 4 · vòng 16: 1 · vòng 17: 0 (mục 9 không ghi con số này cho vòng 3, 9 và 11). Số lỗi **Nặng**: 6 → 5 → 5 → 9 → 5 → 4 → 5 → 5 → 3 → 3 → 2 → 2 → 2 → **0** → **0**. **Vòng 16 kết luận tài liệu đã hội tụ, và vòng 17 xác nhận lại: không còn lỗi mức Nặng, cũng không còn lỗi Trung bình.** Từ vòng 12, **năm nhóm kiểm cho kết quả sạch tuyệt đối**: ma trận vòng đời, nguồn chân lý về che mặt nạ, danh mục sự kiện kiểm toán, năm ngoại lệ tự động xóa, và nghĩa vụ dẫn chiếu mã quy tắc ở ma trận phân quyền. Từ vòng 13 Phụ lục B luôn sạch, và **từ vòng 16 toàn bộ bảy tuyên bố tự đặt tiêu chí cũng sạch**, và mọi lỗi Nặng còn lại dồn về đúng một nơi: **ô phân quyền tại mục 5** — hai vòng liền không còn lỗi Nặng nào ngoài mặt phẳng đó. Từ vòng 8 trở đi, **mọi lỗi Nặng đều sửa được cục bộ tại đúng một chỗ** — không còn lỗi cấu trúc nào phải viết lại cả cụm — và ma trận vòng đời được nhiều người soát độc lập dựng lại từ bốn nguyên tắc chi phối — từ vòng 8 trở đi, mọi lần dựng lại đều **trùng khít bảng thật, không ô thiếu không ô thừa**. Mỗi vòng được khắc phục ở đúng một phiên bản kế tiếp: v4.0 khắc phục vòng 3, v5.0 khắc phục vòng 4, v5.1 khắc phục vòng 5, v5.2 khắc phục vòng 6, v5.3 khắc phục vòng 7, v5.4 khắc phục vòng 8, v5.5 khắc phục vòng 9, v5.6 khắc phục vòng 10, v5.7 khắc phục vòng 11, v5.8 khắc phục vòng 12, v5.9 khắc phục vòng 13, v6.0 khắc phục vòng 14, v6.1 khắc phục vòng 15, v6.2 khắc phục vòng 16, **v6.3 khắc phục vòng 17** — tất cả theo phương pháp gộp về nguồn chân lý duy nhất thay vì điểm-sửa.

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
4. **Vòng đời Khách hàng & Chuyển đổi Tiềm năng (Lifecycle Stages & Lead Conversion):** Định vị mức độ trưởng thành của khách hàng qua 10 giai đoạn (7 giai đoạn phễu tuyến tính chính + 3 trạng thái đặc biệt: Nurturing, Churned, Disqualified) và quy trình thẩm định chuyển đổi Khách hàng tiềm năng (Lead) thành Liên hệ chính thức + Doanh nghiệp + Cơ hội bán hàng chỉ bằng 1 thao tác nguyên tử.
5. **Điểm Tiềm năng & Chấm điểm Tự động (Lead Scoring & Decay):** Đánh giá mức độ tiềm năng theo hồ sơ và tần suất tương tác để ưu tiên phân bổ cho đội ngũ kinh doanh.
6. **Chất lượng Dữ liệu & Xử lý Trùng lặp (Deduplication, Merge & Unmerge):** Tự động phát hiện trùng lặp, xem trước tác động gộp, gộp bản ghi an toàn kèm Sổ cái Hoàn tác Gộp (Unmerge Ledger).
7. **Nhập / Xuất Dữ liệu Thông minh (Smart Bulk Import/Export):** Trình trợ lý nhập khẩu Excel/CSV dung lượng lớn (mặc định 50MB, cấu hình được theo tenant) có tự động ánh xạ cột và xuất báo cáo lỗi chi tiết.
8. **Dòng thời gian Hoạt động Hợp nhất 360 độ (Unified Customer Timeline):** Bảng luồng thông tin trung tâm tập hợp mọi ghi chú, vé hỗ trợ, cơ hội bán hàng, nhiệm vụ và hội thoại đa kênh.
9. **Tuân thủ Dữ liệu Cá nhân (Data Privacy Compliance):** Quản lý đồng thuận nhận tin theo từng kênh kèm bằng chứng thu thập, phân loại mục đích gửi tin, xử lý các yêu cầu về quyền của chủ thể dữ liệu (yêu cầu bản sao, chỉnh sửa, xóa vĩnh viễn, hạn chế xử lý) và chính sách lưu trữ dữ liệu theo thời hạn.
10. **Quyền sở hữu, Cộng tác & Ghi nhận Hoạt động (Ownership, Collaboration & Activity):** Chuyển giao quyền phụ trách khách hàng và bàn giao khi nhân viên rời tổ chức hoặc nghỉ phép; chia sẻ bản ghi và Đội ngũ phụ trách để nhiều bộ phận cùng phục vụ một khách hàng; ghi chú nội bộ có phân loại phạm vi đọc và bản ghi hoạt động không thể tạo khống.

### 1.2 Phạm vi

Tài liệu bao gồm 11 nhóm chức năng cốt lõi:
- **Nhóm A: Quản trị Hồ sơ Khách hàng Cá nhân (Contacts):** Tạo mới, cập nhật, chi tiết 360 độ, phân loại, gắn nhãn (Tags), mặt nạ bảo vệ dữ liệu nhạy cảm (Field Masking) và Thùng rác phục hồi.
- **Nhóm B: Quản trị Hồ sơ Tổ chức & Doanh nghiệp (Accounts):** Tạo mới, cập nhật, cấu trúc Công ty Mẹ - Con, quản lý thuế/ngành nghề và danh sách nhân sự trực thuộc.
- **Nhóm C: Mạng lưới Quan hệ Đa chiều (Multi-Affiliations & Person Relations):** Quan hệ người - công ty đa năng và quan hệ người - người.
- **Nhóm D: Vòng đời Khách hàng & Chuyển đổi Tiềm năng (Lifecycle & Lead Conversion):** Chuẩn 10 giai đoạn vòng đời (7 giai đoạn phễu chính + 3 trạng thái đặc biệt), ma trận chuyển đổi, quy trình chuyển đổi Lead 1-click, phân bổ Lead tự động (Lead Routing) và theo dõi nguồn gốc khách hàng tiềm năng (UTM Source Tracking).
- **Nhóm E: Điểm Tiềm năng & Chấm điểm Tự động (Lead Scoring):** Chấm điểm tiềm năng theo hồ sơ và hành vi tương tác, cơ chế suy giảm điểm theo thời gian.
- **Nhóm F: Nhận diện Trùng lặp & Gộp Bản ghi An toàn (Deduplication, Merge & Unmerge):** Kiểm tra trùng lặp, Preview Merge, Gộp có sổ cái và Hoàn tác gộp bản ghi (Unmerge).
- **Nhóm G: Nhập Dữ liệu Thông minh qua Hàng đợi (Smart Bulk Import):** Upload Excel/CSV dung lượng lớn (mặc định 50MB, cấu hình được theo tenant), tự động ánh xạ cột, xử lý bất đồng bộ và báo cáo lỗi chi tiết.
- **Nhóm H: Xuất Dữ liệu & Danh sách Hiển thị Tùy chỉnh (Export & List Views):** Xuất dữ liệu luồng CSV bảo mật và danh sách hiển thị dùng chung (Shared List Views).
- **Nhóm I: Dòng thời gian Hoạt động Hợp nhất 360 độ (Unified Timeline & Customer Context):** Hợp nhất dòng thời gian và API ngữ cảnh khách hàng cho Hộp thư Omni-channel.
- **Nhóm J: Quản trị Định danh, Đồng thuận & Tuân thủ Dữ liệu Cá nhân (Identities, Consent & Data Privacy Compliance):** Quản lý kênh liên lạc chính, trạng thái Bounced/Verified, Đồng thuận nhận tin (Opt-in Consent), Định danh dùng chung (Shared Identifiers), Bằng chứng đồng thuận, Phân loại mục đích gửi tin và Quyền của Chủ thể Dữ liệu (Data Subject Rights).
- **Nhóm K: Quyền sở hữu, Cộng tác & Ghi nhận Hoạt động (Ownership, Collaboration & Activity):** Chuyển giao quyền phụ trách và bàn giao khi nhân viên rời tổ chức, chia sẻ bản ghi và Đội ngũ phụ trách khách hàng, ghi chú và bản ghi hoạt động.

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
| **Giai đoạn Vòng đời (Lifecycle Stage)** | Vị trí của khách hàng trong hành trình chuyển đổi, gồm 10 giai đoạn: 7 giai đoạn phễu tuyến tính chính (*Subscriber -> Lead -> MQL -> SQL -> Opportunity -> Customer -> Evangelist*) và 3 trạng thái đặc biệt nằm ngoài phễu tuyến tính (*Nurturing, Churned, Disqualified*) dùng để xử lý các nhánh rẽ/thoát phễu. Chi tiết xem FEAT-12. |
| **Chuyển đổi Tiềm năng (Lead Conversion)** | Thao tác thẩm định nâng cấp Lead thành Contact chính thức, liên kết/tạo Account và Deal tương ứng. |
| **Bản ghi Chính (Master Record)** | Bản ghi sống sót và giữ lại định danh sau khi thực hiện giao dịch gộp hai khách hàng hoặc hai doanh nghiệp. |
| **Sổ cái Hoàn tác Gộp (Unmerge Ledger)** | Bảng lưu vết lịch sử gộp cho phép khôi phục lại trạng thái ban đầu trước khi gộp nếu có sai sót. |
| **Quan hệ Đa tổ chức (Multi-Affiliations)** | Khả năng liên kết một cá nhân với nhiều công ty cùng lúc với các chức danh và vai trò khác nhau. |
| **Dòng thời gian 360 độ (Unified Timeline)** | Luồng hiển thị hợp nhất toàn bộ tương tác, ghi chú, vé, cơ hội và công việc của một khách hàng. |
| **Điểm Tiềm năng (Lead Score)** | Điểm số tự động đánh giá độ nóng của khách hàng dựa trên hồ sơ và mức độ tương tác thực tế. |
| **Đồng thuận Nhận tin (Opt-in Consent)** | Trạng thái đồng ý nhận thông tin tiếp thị/quảng bá của khách hàng theo chuẩn GDPR và chống thư rác. |
| **Ma trận Chuyển đổi Giai đoạn (Stage Transition Matrix)** | Bảng liệt kê các bước chuyển giai đoạn vòng đời được phép và điều kiện/quyền tương ứng, là hệ quả của 4 nguyên tắc chi phối nêu tại FEAT-12. Bước chuyển ngoài ma trận bị từ chối, trừ ngoại lệ của nguyên tắc "giai đoạn phải phản ánh thực tế bán hàng" (BR-12.6, BR-12.9). |
| **Ngưỡng điểm Thăng hạng (Scoring Threshold)** | Mốc điểm tiềm năng quy định thời điểm khách hàng tự động thăng hạng giai đoạn hoặc được đánh dấu sẵn sàng chuyển cho đội kinh doanh. Chi tiết tại BR-15.5. |
| **Bằng chứng Đồng thuận (Consent Evidence)** | Bộ dữ liệu chứng minh khách hàng đã đồng ý nhận tin: thời điểm, nguồn thu thập, nội dung điều khoản đã đồng ý và người ghi nhận. Được lưu vĩnh viễn để đối chiếu khi có khiếu nại. |
| **Quyền Chủ thể Dữ liệu (Data Subject Rights)** | Các quyền của khách hàng đối với dữ liệu cá nhân của chính họ: yêu cầu bản sao, chỉnh sửa, xóa vĩnh viễn, hạn chế xử lý và rút lại đồng thuận. Khác biệt hoàn toàn với Thùng rác nội bộ — quyền xóa của chủ thể dữ liệu là nghĩa vụ pháp lý và không thể phục hồi. |
| **Vai trò chức năng (Functional Designation)** | Trách nhiệm nghiệp vụ được gán cho một người (ví dụ Quản lý Khách hàng Hiện hữu, Quản trị Chất lượng Dữ liệu) nhưng không phải một vai trò phân quyền riêng trong hệ thống; quyền hạn áp dụng theo vai trò gốc được cấp. |

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
| **Quản lý Kinh doanh (Sales Manager)** | Phân công và chuyển giao khách hàng cho nhân viên (FEAT-34), phê duyệt các bước lùi giai đoạn và loại khách (BR-12.4, BR-12.7), thực hiện Hoàn tác Chuyển đổi (BR-14.2), xác nhận bằng chứng liên hệ ngoài hệ thống (BR-31.8), theo dõi điểm số và báo cáo danh bạ. |
| **Nhân viên Hỗ trợ Khách hàng (Support Agent)** | Tra cứu ngữ cảnh khách hàng 360 độ khi tiếp nhận hội thoại/vé hỗ trợ và ghi nhận ghi chú/hoạt động phục vụ khách. Lưu ý phạm vi: quyền đọc tự động theo BR-35.4 là **chỉ đọc**, trừ hai thao tác thu hẹp phạm vi xử lý dữ liệu nêu tại BR-35.4a (gắn `RESTRICTED`, hạ đồng thuận); việc cập nhật kênh liên lạc và ghi chú chỉ thực hiện được trên bản ghi thuộc phạm vi dữ liệu được gán hoặc khi được thêm vào Đội ngũ phụ trách với mức Chỉnh sửa (BR-35.1). Bao gồm cả Tư vấn viên Livechat — đây là **cùng một vai trò hệ thống**, chỉ khác kênh phục vụ. Do quyền phụ trách bản ghi thường thuộc đội kinh doanh (BR-01.3), vai trò này truy cập hồ sơ khách hàng chủ yếu qua cơ chế quyền đọc tự động khi có vé/hội thoại đang mở (BR-35.4). |
| **Nhân viên Marketing (Marketing Specialist)** | Xuất danh sách khách hàng phục vụ chiến dịch, **xem** cấu hình quy tắc chấm điểm tiềm năng (không được chỉnh sửa — xem BR-15.4), cấu hình trạng thái Opt-in/Opt-out từng kênh, gắn thẻ phân loại phục vụ phân khúc, xem báo cáo funnel chuyển đổi và báo cáo nguồn gốc khách hàng. **Không có quyền Xóa, Gộp hay Hoàn tác gộp bản ghi.** |
| **Quản lý Marketing (Marketing Manager)** | Toàn quyền chức năng Marketing: Cấu hình Lead Scoring, Lifecycle tự động, quy tắc Nurturing; phê duyệt xuất dữ liệu quy mô lớn; xem báo cáo UTM Source và Revenue Attribution. |
| **Quản trị viên Không gian làm việc (Tenant Admin)** | Quản lý cấu hình trường dữ liệu, thực thi gộp bản ghi trùng lặp, hoàn tác gộp, nhập/xuất dữ liệu hàng loạt và cấu hình quy tắc chấm điểm. |
| **Chủ sở hữu Không gian làm việc (Tenant Owner)** | Toàn quyền quản trị danh bạ, xem toàn bộ dữ liệu tổ chức và cấu hình chính sách bảo mật dữ liệu nhạy cảm. |
| **Quản lý Khách hàng Hiện hữu (Account Manager)** | **Vai trò chức năng (functional designation), không phải vai trò hệ thống riêng.** Là Nhân viên/Quản lý Kinh doanh được gán làm Người phụ trách của một khách hàng đã đạt giai đoạn `Customer` trở lên. Chịu trách nhiệm duy trì, gia hạn và mở rộng (Upsell/Cross-sell); là người nhận thông báo khi khách hàng chuyển sang `Churned` (BR-12.5). Quyền hạn hệ thống áp dụng theo vai trò gốc (Sales Rep hoặc Sales Manager) trong ma trận mục 5. |
| **Quản trị Chất lượng Dữ liệu (Data Steward)** | **Vai trò chức năng.** Là người được Chủ sở hữu/Quản trị viên chỉ định chịu trách nhiệm rà soát trùng lặp định kỳ, chuẩn hoá dữ liệu, xử lý hồ sơ tạm tồn dư và giám sát chỉ số chất lượng dữ liệu (mục 2.4). Trong tổ chức nhỏ, vai trò này do Quản trị viên Không gian làm việc kiêm nhiệm; quyền hạn hệ thống áp dụng theo vai trò gốc được cấp. |
| **Người phụ trách Bảo vệ Dữ liệu (Data Protection Officer)** | **Vai trò chức năng, bắt buộc chỉ định nếu tổ chức thuộc diện phải có theo pháp luật bảo vệ dữ liệu cá nhân.** Chịu trách nhiệm tiếp nhận và giám sát xử lý các yêu cầu về quyền chủ thể dữ liệu (FEAT-33), phê duyệt chính sách phạm vi dữ liệu và bằng chứng đồng thuận, ký phê duyệt phần tuân thủ dữ liệu cá nhân tại mục 10. Nếu tổ chức không chỉ định, trách nhiệm này thuộc Chủ sở hữu Không gian làm việc. Quyền hạn hệ thống áp dụng theo vai trò gốc được cấp (thường là Quản trị viên). |
| **Tiến trình Hệ thống (System Engine / Background Workers)** | Tự động tính toán điểm tiềm năng (Scoring), suy giảm điểm theo thời gian, xử lý tệp nhập khẩu/xuất khẩu bất đồng bộ theo hàng đợi và quét đồng bộ danh tính đa kênh. |

### 2.3 Bảng tổng hợp 36 tính năng nghiệp vụ

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
| **D. Vòng đời & Chuyển đổi Tiềm năng** | `FEAT-12` | Quản trị Giai đoạn Vòng đời Khách hàng (10 Lifecycle Stages) & Ma trận Chuyển đổi | `[Đã triển khai]` |
| | `FEAT-13` | Lịch sử Chuyển đổi Giai đoạn Vòng đời (Stage Transition History) | `[Đã triển khai]` |
| | `FEAT-14` | Quy trình Chuyển đổi Khách hàng Tiềm năng 1-Click (Lead Conversion) | `[Yêu cầu mới]` |
| | `FEAT-31` | Phân bổ Khách hàng Tiềm năng Tự động (Lead Routing Engine) | `[Yêu cầu mới]` |
| | `FEAT-32` | Theo dõi Nguồn gốc Khách hàng Tiềm năng (UTM Source Tracking) | `[Yêu cầu mới]` |
| **E. Điểm Tiềm năng & Chấm điểm** | `FEAT-15` | Động cơ Chấm điểm Tiềm năng Tự động (Lead Scoring Engine) | `[Đã triển khai]` |
| | `FEAT-16` | Cơ chế Suy giảm Điểm Tiềm năng theo Thời gian (Score Decay Engine) | `[Yêu cầu mới]` |
| **F. Xử lý Trùng lặp & Gộp Bản ghi** | `FEAT-17` | Tự động Nhận diện & Kiểm tra Trùng lặp Khách hàng (Duplicate Check) | `[Đã triển khai]` |
| | `FEAT-18` | Xem trước Tác động Gộp Bản ghi (Merge Preview & Impact Analysis) | `[Đã triển khai]` |
| | `FEAT-19` | Gộp Khách hàng Cá nhân & Doanh nghiệp An toàn (Contact/Account Merge) | `[Đã triển khai]` |
| | `FEAT-20` | Sổ cái Hoàn tác Gộp Bản ghi (Unmerge Contacts/Accounts Ledger) | `[Đã triển khai]` |
| | `FEAT-21` | Khôi phục Tự động Giao dịch Gộp Bị Lỗi (Recover Failed Merge) | `[Đã triển khai]` |
| **G. Nhập Dữ liệu Thông minh** | `FEAT-22` | Tải lên & Tiếp nhận Tệp Nhập khẩu Excel/CSV Dung lượng lớn (mặc định 50MB, `CFG-22-02`) | `[Đã triển khai]` |
| | `FEAT-23` | Trợ lý Tự động Ánh xạ Cột Dữ liệu (Auto Field Mapping Wizard) | `[Yêu cầu mới]` |
| | `FEAT-24` | Xử lý Nhập khẩu Hàng đợi & Xuất Báo cáo Lỗi Chi tiết (Import Error Report) | `[Đã triển khai]` |
| **H. Xuất Dữ liệu & Danh sách** | `FEAT-25` | Xuất Dữ liệu Khách hàng Dạng Luồng An toàn qua Token (Streaming Export) | `[Đã triển khai]` |
| | `FEAT-26` | Quản lý & Tích hợp Danh sách Hiển thị Dùng chung (Shared List Views) | `[Đã triển khai]` |
| **I. Dòng thời gian 360 & Ngữ cảnh** | `FEAT-27` | Dòng thời gian Hoạt động Hợp nhất 360 độ (Unified Customer Timeline) | `[Đã triển khai]` |
| | `FEAT-28` | Cung cấp Ngữ cảnh Khách hàng 1 Chạm cho Omni Inbox (Customer Context API) | `[Đã triển khai]` |
| **J. Định danh, Đồng thuận & Tuân thủ DLCN** | `FEAT-29` | Quản lý Danh tính Đa kênh & Trạng thái Tiếp cận (Identities & Deliverability)| `[Đã triển khai]` |
| | `FEAT-30` | Quản lý Trạng thái Đồng thuận Tiếp thị & Định danh Dùng chung (Consent) | `[Đã triển khai]` |
| | `FEAT-33` | Quyền Chủ thể Dữ liệu & Xử lý Yêu cầu Dữ liệu Cá nhân (Data Subject Rights) | `[Yêu cầu mới]` |
| **K. Quyền sở hữu, Cộng tác & Hoạt động** | `FEAT-34` | Chuyển giao Quyền phụ trách & Bàn giao khi Nhân viên rời tổ chức | `[Yêu cầu mới]` |
| | `FEAT-35` | Chia sẻ Bản ghi & Đội ngũ Phụ trách Khách hàng (Record Sharing & Teams) | `[Yêu cầu mới]` |
| | `FEAT-36` | Ghi chú & Ghi nhận Hoạt động Khách hàng (Notes & Activity Logging) | `[Yêu cầu mới]` |

### 2.4 Mục tiêu kinh doanh & Chỉ số thành công (Business Objectives & Success Metrics)

Mỗi vấn đề nêu tại mục 2.1 được gắn với **ít nhất một** chỉ số đo lường được để nghiệm thu hiệu quả nghiệp vụ sau khi phát hành (đo tại mốc 90 ngày kể từ ngày go-live trên từng không gian làm việc). Bảng có 8 chỉ số cho 5 vấn đề vì hai lý do: **(a)** hai chỉ số đo **điều kiện để các vấn đề kia được giải quyết bền vững** chứ không gắn trực tiếp một vấn đề — `KPI-07` (chất lượng dữ liệu đầu vào) và `KPI-08` (hiệu quả ngân sách Marketing); **(b)** vấn đề "Khách hàng tiềm năng bị bỏ quên và quy trình chuyển đổi rời rạc" được đo bằng **hai** chỉ số vì nó có hai mặt tách rời nhau trong vận hành — tốc độ phản hồi lần đầu (`KPI-03`) và tỷ lệ chuyển đổi qua các giai đoạn (`KPI-04`).

| Mã | Vấn đề nghiệp vụ hoặc điều kiện nền | Chỉ số đo lường (Metric) | Giá trị mục tiêu | Tính năng đóng góp |
| --- | --- | --- | --- | --- |
| `KPI-01` | Dữ liệu trùng lặp từ nhiều nguồn | Tỷ lệ bản ghi **dư thừa** do trùng lặp: số bản ghi cần bị gộp bỏ để không còn cặp trùng nào, chia cho tổng số bản ghi đang hoạt động (định nghĩa đầy đủ tại BR-17.4) | **< 2%** | FEAT-17, 18, 19, 23 |
| `KPI-02` | Nhân viên không nắm lịch sử tương tác của đồng nghiệp | Tỷ lệ hội thoại/vé hỗ trợ **được phản hồi lần đầu** mà trong đó nhân viên xử lý đã mở Ngữ cảnh Khách hàng hoặc Dòng thời gian của khách hàng đó **trước thời điểm phản hồi** và trong cùng phiên làm việc. Mẫu đo: mọi hội thoại/vé được phản hồi trong kỳ. Nguồn số liệu: **bộ đếm nghiệp vụ riêng** ghi nhận sự kiện "đã mở Ngữ cảnh/Dòng thời gian của khách hàng X" gắn với mã hội thoại/vé — bộ đếm này tách biệt khỏi nhật ký kiểm toán để Product Owner tổng hợp được hằng tháng mà không cần quyền đọc nhật ký theo NFR-14 | **≥ 80%** | FEAT-02, 27, 28, BR-35.4 |
| `KPI-03` | Khách hàng tiềm năng bị bỏ quên | Tỷ lệ Lead được liên hệ lần đầu **trong thời hạn cam kết tương ứng với mức ưu tiên của Lead đó** (1 / 4 / 24 giờ làm việc theo BR-31.7), tính trên bằng chứng liên hệ được công nhận tại BR-31.8, trong đó **bằng chứng nhóm 2 (liên hệ ngoài hệ thống có Quản lý xác nhận) được thống kê thành một cấu phần riêng** để nhìn được tỷ trọng. **Loại khỏi mẫu đo:** bản ghi đã được đánh dấu "Lead rác" theo BR-12.4b (đồng hồ cam kết bị đình chỉ từ thời điểm đánh dấu); bản ghi đang ở trạng thái `RESTRICTED` — BR-30.6 dừng phân bổ lại và thu hồi tự động, và **đồng hồ cam kết phản hồi lần đầu cũng dừng** từ thời điểm gắn trạng thái, vì hệ thống không được thúc nhân viên liên hệ một người vừa yêu cầu hạn chế xử lý | **≥ 90%** | FEAT-31 (BR-31.6, BR-31.7) |
| `KPI-04` | Chuyển đổi tiềm năng thủ công, rời rạc | Tỷ lệ Lead đủ điều kiện được chuyển đổi qua quy trình 1-Click (thay vì tạo tay rời rạc) | **≥ 95%** | FEAT-14 |
| `KPI-05` | Một cá nhân nhiều vai trò tại nhiều công ty | Tỷ lệ Contact có **Loại khách hàng = Doanh nghiệp (B2B)** theo BR-01.6 có ít nhất 1 liên kết công ty được khai báo (Contact loại B2C được loại khỏi mẫu đo) | **≥ 85%** | FEAT-10, 08, BR-01.6 |
| `KPI-06` | Rủi ro lộ lọt dữ liệu cá nhân | **Số lượt truy cập dữ liệu nhạy cảm vượt hạn mức hoặc bị đánh giá là bất thường mà chưa được rà soát và đóng kết luận trong 7 ngày.** Nguồn: báo cáo truy cập bất thường (BR-04.5), báo cáo phơi bày định kỳ (BR-04.5b), báo cáo nhóm Liên lạc 1-1 (BR-30.8) — **không** phải truy vấn trực tiếp nhật ký kiểm toán, vì NFR-14 giới hạn quyền đọc nhật ký | **= 0** | FEAT-04, NFR-06, NFR-07, NFR-14 |
| `KPI-07` | Chất lượng dữ liệu đầu vào | Tỷ lệ hồ sơ có đủ tối thiểu: 1 kênh liên lạc hợp lệ + Người phụ trách đang hoạt động + Giai đoạn vòng đời. **Loại khỏi mẫu đo:** Hồ sơ Khách hàng Tạm (BR-01.1b — chưa có kênh liên lạc và chưa gán giai đoạn theo thiết kế) | **≥ 95%** | FEAT-01, 12, 29, 34 |
| `KPI-08` | Hiệu quả ngân sách Marketing | Tỷ lệ Lead mới ghi nhận được nguồn gốc (không rơi vào nhóm "Không xác định") | **≥ 90%** | FEAT-32 |

**Quy ước theo dõi:** Chủ sở hữu chỉ số là Product Owner của phân hệ; số liệu được tổng hợp định kỳ hàng tháng. Chỉ số không đạt mục tiêu 2 kỳ liên tiếp sẽ được đưa vào danh mục vấn đề chính sách tại mục 7 để quyết định điều chỉnh nghiệp vụ.

---

### 2.5 Luồng nghiệp vụ đầu–cuối (End-to-End Business Flow)

Luồng vận hành xuyên suốt của phân hệ, thể hiện cách 36 tính năng phối hợp trong thực tế:

**Giai đoạn 1 — Thu nhận (Acquisition):**
Khách hàng để lại thông tin qua Website Form, Livechat, Quảng cáo, Sự kiện hoặc được nhập hàng loạt từ tệp danh bạ → Hệ thống ghi nhận nguồn gốc và tham số chiến dịch (FEAT-32) → Kiểm tra trùng lặp tức thì và áp dụng chính sách xử lý theo BR-17.2 (mặc định: trùng theo Tiêu chí chắc chắn thì chặn tạo mới và trả về bản ghi đã có; trùng theo Tiêu chí tham khảo thì chỉ cảnh báo mềm) → Nếu chưa có người tạo trực tiếp, hệ thống tự động phân bổ Người phụ trách (FEAT-31), ưu tiên trả về đúng người đang phụ trách nếu là khách đã tồn tại.

**Giai đoạn 2 — Thẩm định & Nuôi dưỡng (Qualification & Nurturing):**
Hệ thống chấm điểm tiềm năng theo hồ sơ và hành vi (FEAT-15) → Khi vượt ngưỡng điểm quy định, bản ghi tự động thăng hạng `Lead → MQL` (BR-15.5) → Đội kinh doanh phải phản hồi trong thời hạn cam kết, nếu quá hạn hệ thống thu hồi và chia lại (BR-31.7) → Sales thẩm định trực tiếp và chuyển `MQL → SQL`; nếu chưa sẵn sàng mua thì chuyển `Nurturing` kèm lý do; nếu không phù hợp thì `Disqualified` kèm lý do (BR-12.4) → Khách không tương tác lâu bị suy giảm điểm để phản ánh độ nguội (FEAT-16).

**Giai đoạn 3 — Chuyển đổi (Conversion):**
Sales kích hoạt Chuyển đổi 1-Click (FEAT-14): nâng cấp Liên hệ, liên kết hoặc tạo mới Doanh nghiệp, tạo Cơ hội bán hàng trong một giao dịch nguyên tử → Giai đoạn tự động lên `Opportunity` (BR-12.2) → Nếu chuyển đổi sai, Quản lý được hoàn tác trong 24 giờ (BR-14.2).

**Giai đoạn 4 — Phục vụ & Mở rộng (Serve & Expand):**
Nhiều bộ phận cùng phục vụ một khách hàng qua Đội ngũ phụ trách (FEAT-35) → Mọi tương tác hợp nhất về Dòng thời gian 360 độ (FEAT-27), trong đó ghi chú và bản ghi hoạt động là nguồn dữ liệu chính (FEAT-36) → Tư vấn viên tra cứu ngữ cảnh 1 chạm khi tiếp nhận hội thoại, truy cập được nhờ quyền đọc tự động khi vé/hội thoại đang mở (FEAT-28, BR-35.4) → Khi cơ hội thắng, giai đoạn tự lên `Customer`; người phụ trách trở thành Quản lý Khách hàng Hiện hữu → Quan hệ đa công ty và mạng lưới quan hệ cá nhân được khai thác cho bán hàng mở rộng (FEAT-10, 11) → Cấu trúc tập đoàn mẹ-con phục vụ bán hàng theo tập đoàn (FEAT-07).

**Giai đoạn 5 — Duy trì Chất lượng, Bàn giao & Tuân thủ (Data Hygiene, Handover & Compliance):**
Khi nhân viên đổi địa bàn, nghỉ phép hoặc rời tổ chức, danh bạ được bàn giao có kiểm soát để không bản ghi nào thành vô chủ (FEAT-34) → Quản trị Chất lượng Dữ liệu rà soát trùng lặp định kỳ, xem trước tác động và gộp bản ghi có sổ cái hoàn tác (FEAT-18, 19, 20) → Đồng thuận nhận tin được quản lý theo từng kênh kèm bằng chứng thu thập (FEAT-30) → Khách hàng thực hiện quyền của chủ thể dữ liệu (yêu cầu bản sao / yêu cầu xóa) qua quy trình chuẩn (FEAT-33) → Bản ghi hết giá trị được xóa mềm vào Thùng rác và dọn dẹp theo chính sách lưu trữ (FEAT-05, 09).

**Giai đoạn 6 — Rời bỏ & Tái tiếp cận (Churn & Win-Back):**
Khi khách hủy hợp đồng, chuyển `Churned`, tự động thông báo người phụ trách và dừng toàn bộ chiến dịch tự động (BR-12.5) → Chỉ được tái tiếp cận qua chiến dịch Win-Back được phê duyệt riêng.

---

## 3. Đặc tả yêu cầu chức năng

**Quy ước đọc mục này:** Dòng **Actor** trong mỗi tính năng liệt kê các vai trò **sử dụng chính** tính năng đó trong vận hành hàng ngày, phục vụ mục đích mô tả nghiệp vụ. **Ma trận Phân quyền tại mục 5 là nguồn chân lý duy nhất về việc một vai trò CÓ hay KHÔNG có quyền dùng một tính năng** — khi có khác biệt giữa dòng Actor và ma trận, ma trận có hiệu lực. **Quy tắc nghiệp vụ (BR) là nguồn chân lý duy nhất về ĐIỀU KIỆN, HẠN MỨC và NGOẠI LỆ bên trong quyền đó** — hạn mức số bản ghi, ngưỡng cần phê duyệt, nhóm trường bị loại trừ, thời hạn hiệu lực. Hai nguồn này **không được nói khác nhau về cùng một điều**: mỗi ô ma trận có điều kiện **vượt ra ngoài từ vựng chuẩn tại Ghi chú 8 mục 5** thì **bắt buộc dẫn chiếu mã BR** quy định điều kiện đó (các giá trị thuộc từ vựng chuẩn đã có nguồn chân lý riêng ở chính Ghi chú 8, không cần lặp lại ở từng ô). Nếu người kiểm thử phát hiện một ô ma trận và BR tương ứng mâu thuẫn về việc *có quyền hay không*, đây là **lỗi tài liệu phải sửa**, không phải tình huống áp dụng thứ tự ưu tiên — ghi nhận thành lỗi và gửi lại chủ sở hữu tài liệu, không tự chọn một bên để nghiệm thu.

**Quy ước thứ tự mã:** mã BR và NFR được cấp theo thứ tự **thời điểm bổ sung**, không theo thứ tự trình bày; vì vậy trong một tính năng có thể gặp `BR-xx.10` đứng trước `BR-xx.9`. Một quy tắc cũng có thể được **trình bày trong thân tính năng khác với tính năng cấp mã cho nó**, khi nội dung của nó thuộc về chỗ đó về mặt nghiệp vụ — ví dụ `BR-16.5` (đường quay lại phễu) mang mã của FEAT-16 nhưng trình bày cạnh ma trận vòng đời tại FEAT-12, nơi nó có hiệu lực. Mọi tham chiếu được giải theo **mã**, không theo vị trí — một quy tắc được phép viện dẫn quy tắc nằm phía dưới nó.

## A. QUẢN TRỊ KHÁCH HÀNG CÁ NHÂN (CONTACTS MANAGEMENT)

### FEAT-01 — Tạo mới & Quản lý Thông tin Khách hàng Cá nhân (Contact CRUD) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép người dùng tạo mới, tra cứu danh sách, xem chi tiết, cập nhật và xóa khách hàng cá nhân trong không gian làm việc.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-01.1 (Thông tin bắt buộc)`: Bắt buộc phải có ít nhất một phương thức liên lạc chính: `email` (đúng chuẩn RFC 5322) HOẶC `phone` (chuẩn hoá theo chuẩn quốc tế E.164). Họ và Tên (`fullName`) là trường **khuyến khích** nhưng không bắt buộc — nếu không có Tên, hệ thống tự động hiển thị tên fallback từ prefix email (ví dụ: `ceo@company.com` → tên hiển thị: `ceo`) hoặc số điện thoại.
- `BR-01.1b (Ngoại lệ Hồ sơ Khách hàng Tạm — Provisional Record) [Yêu cầu mới]`: Trường hợp tiếp nhận từ kênh Livechat/Khách vãng lai **chưa có** cả email lẫn phone tại thời điểm khởi tạo, hệ thống được phép tạo Hồ sơ Khách hàng Tạm (Provisional Record) như một ngoại lệ có kiểm soát của BR-01.1, với tên tạm `Khách vãng lai #{ID}` và định danh duy nhất thay thế là mã phiên hội thoại hoặc định danh thiết bị/kênh chat của khách truy cập. Hồ sơ Tạm bị loại khỏi các báo cáo Điểm tiềm năng và Phễu vòng đời cho đến khi được bổ sung email hoặc phone hợp lệ; tại thời điểm đó hồ sơ tự động chuyển sang Contact chính thức, được gán giai đoạn theo BR-12.10 và được đưa vào kiểm tra trùng lặp như bình thường.
- `BR-01.1c (Tái nhận diện Khách vãng lai quay lại) [Yêu cầu mới]`: Khi một khách vãng lai quay lại với **cùng định danh thiết bị/kênh chat**, hệ thống bắt buộc **tái sử dụng Hồ sơ Tạm đã có** thay vì tạo hồ sơ mới, để tư vấn viên thấy được toàn bộ lịch sử các phiên trước. Việc loại Hồ sơ Tạm khỏi kiểm tra trùng lặp tại BR-01.1b **chỉ áp dụng cho việc so khớp với Contact chính thức**, không áp dụng cho việc so khớp trong nội bộ nhóm Hồ sơ Tạm theo định danh thiết bị. Mục đích: tránh tái lập chính vấn đề "tư vấn viên không thấy lịch sử tương tác trước đó" nêu tại mục 2.1.

  **Cơ sở lưu và thông báo:** Định danh thiết bị/kênh chat là dữ liệu cá nhân. Việc lưu định danh này chỉ được thực hiện với mục đích duy trì liên tục hội thoại phục vụ khách, và cửa sổ chat **bắt buộc hiển thị thông báo ngắn** cho khách truy cập về việc hệ thống ghi nhận phiên để phục vụ hỗ trợ, kèm liên kết tới chính sách quyền riêng tư. Thời hạn lưu định danh chịu trần tuyệt đối tại BR-33.6.
- `BR-01.2 (Kiểm tra định dạng)`: Địa chỉ email phải đúng chuẩn RFC 5322; Số điện thoại được tự động chuẩn hoá theo chuẩn quốc tế E.164 (ví dụ: `+84901234567`, `+966501234567`).
- `BR-01.3 (Phân bổ quyền sở hữu)`: Khi tạo mới, người tạo tự động được gán làm Người phụ trách (`ownerId`), Đơn vị tổ chức được gán theo Đơn vị tổ chức của người tạo (`orgUnitId`), trừ khi được chỉ định khác bởi người có quyền.
- `BR-01.4 (Phân quyền truy cập theo ABAC)`: Người dùng chỉ được xem/sửa các liên hệ thuộc phạm vi dữ liệu được gán (Cá nhân / Phòng ban / Cây phòng ban / Toàn tổ chức).
- `BR-01.5 (Trường dữ liệu nhạy cảm tùy chọn) [Yêu cầu mới]`: Ngoài các trường liên lạc cơ bản, hồ sơ Contact hỗ trợ lưu trữ tùy chọn các trường định danh nhạy cảm phục vụ xác thực hợp đồng/KYC: Số CCCD/Căn cước công dân, Số Hộ chiếu, Ngày cấp, Nơi cấp. Các trường này áp dụng cơ chế Che giấu Mặt nạ theo FEAT-04 và không bắt buộc nhập khi tạo mới.
- `BR-01.5b (Mục đích, Điều kiện bật và Thời hạn lưu nhóm Định danh KYC) [Yêu cầu mới — sàn bắt buộc]`: Nhóm trường tại BR-01.5 chỉ được lưu khi có mục đích cụ thể và bị giới hạn thời gian:
  - **Mục đích duy nhất được phép:** xác thực danh tính phục vụ ký kết/thực hiện hợp đồng và nghĩa vụ định danh khách hàng theo quy định. **Không** được dùng cho tiếp thị, phân khúc, chấm điểm hay báo cáo.
  - **Điều kiện bật:** nhóm trường này **mặc định tắt** ở cấp tenant. Chỉ Chủ sở hữu Workspace cùng Người phụ trách Bảo vệ Dữ liệu bật được, và phải khai báo mục đích sử dụng khi bật (Phụ lục B, `CFG-01-02`).
  - **Khi nhóm trường ở trạng thái tắt:** các trường thuộc nhóm **không được lưu** và **không hiển thị với mọi vai trò** ở mọi cột của bảng BR-04.3 (mức "Ẩn trường"), kể cả người có quyền chuyên biệt trên nhóm KYC — vì không có dữ liệu để hiển thị.
  - **Thời hạn lưu:** tối đa **24 tháng** kể từ khi hợp đồng gần nhất của khách hàng kết thúc (Phụ lục B, `CFG-01-03`). Hết thời hạn, hệ thống **tự động khử vĩnh viễn phần định danh** (giữ hồ sơ khách hàng, chỉ xoá các trường KYC) — đây là **ngoại lệ có chủ đích** của nguyên tắc "hệ thống không tự động xóa" tại BR-33.5, vì nhóm trường này có mức thiệt hại cao nhất nếu rò rỉ và không có giá trị kinh doanh sau khi hết nghĩa vụ.
  - **Tài liệu xác minh:** bản chụp giấy tờ định danh thu theo BR-33.7 bị **xóa vĩnh viễn trong 30 ngày** sau khi yêu cầu tương ứng hoàn tất, không lưu vào hồ sơ khách hàng.
- `BR-01.6 (Loại Khách hàng — Doanh nghiệp / Cá nhân tiêu dùng) [Yêu cầu mới]`: Mỗi Contact bắt buộc có trường **Loại khách hàng** với hai giá trị: **Khách hàng Doanh nghiệp (B2B)** hoặc **Khách hàng Cá nhân tiêu dùng (B2C)**. Giá trị mặc định khi tạo mới là tham số cấu hình theo tenant (Phụ lục B, `CFG-01-01`) vì có tenant thuần B2B, có tenant thuần B2C. Trường này chi phối hành vi nghiệp vụ ở nhiều nơi:
  - Contact loại **Cá nhân tiêu dùng** không bị đưa vào danh sách "Liên hệ chưa gắn doanh nghiệp" (BR-09.1b) và bị **loại khỏi mẫu đo `KPI-05`** (chỉ số này chỉ đo khách hàng doanh nghiệp). `KPI-07` không đo liên kết doanh nghiệp nên áp dụng bình thường cho cả hai loại khách hàng.
  - Quy trình Chuyển đổi Tiềm năng (FEAT-14) có tùy chọn **"Không liên kết Doanh nghiệp (khách hàng cá nhân)"**, không bắt buộc tạo Doanh nghiệp.
  - Chấm điểm hồ sơ (BR-15.1) áp bộ tiêu chí riêng cho loại Cá nhân, không dùng tiêu chí "email doanh nghiệp" và "chức danh quản lý".
  - Kiểm tra trùng lặp theo Tiêu chí tham khảo (BR-17.1) dùng Họ tên kết hợp Ngày sinh hoặc Địa chỉ thay cho Tên công ty.

  Lý do bắt buộc có trường này: mục 2.1 tuyên bố phục vụ cả vận hành B2B và B2C, nhưng nếu không phân loại thì khách bán lẻ/dịch vụ cá nhân (rất phổ biến trên các kênh Zalo, Facebook mà hệ thống hỗ trợ) sẽ luôn bị coi là hồ sơ thiếu dữ liệu, bị chấm điểm sai, và buộc phải tạo Doanh nghiệp rác mang tên chính khách hàng — sau vài tháng danh bạ Doanh nghiệp đầy các "công ty" là tên người, làm hỏng báo cáo theo doanh nghiệp và cấu trúc mẹ-con (FEAT-07).

---

### FEAT-02 — Hồ sơ Chi tiết Khách hàng 360 độ (360-Degree Customer Profile) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Màn hình tổng hợp toàn diện mọi thông tin của khách hàng: Thông tin cá nhân, Doanh nghiệp trực thuộc, Giai đoạn vòng đời, Điểm tiềm năng, Các cơ hội bán hàng, Vé hỗ trợ, Công việc, Ghi chú và Lịch sử tương tác.

**Actor:** Mọi người dùng có quyền xem Contact.

**Quy tắc nghiệp vụ:**
- `BR-02.1`: Hiển thị bố cục chuẩn gồm: Panel tóm tắt bên trái (Thông tin chính, Điểm tiềm năng, Trạng thái liên lạc), Khu vực trung tâm (Dòng thời gian 360 độ, Tab Ghi chú, Tab Công việc, Tab Vé hỗ trợ, Tab Cơ hội) và Panel liên kết bên phải (Doanh nghiệp trực thuộc, Mối quan hệ cá nhân).
- `BR-02.2 (Hành động nhanh & Phân quyền)`: Cho phép thực hiện các hành động nhanh ngay trên hồ sơ: Gửi email, Tạo cuộc gọi, Tạo ghi chú nhanh, Tạo công việc mới, Tạo vé hỗ trợ mới. Hai hành động sau bị kiểm soát theo quyền: (a) "Tạo cơ hội mới" chỉ hiển thị khi người dùng sở hữu quyền tạo Cơ hội bán hàng — quyền này thuộc phạm vi đặc tả của [`deals-pipeline-srs.md`](./deals-pipeline-srs.md), Nhân viên Hỗ trợ không có quyền sẽ bị ẩn nút hoặc chuyển thành "Gợi ý Cơ hội" (Lead Referral); (b) "Chuyển giai đoạn vòng đời" chỉ hiển thị cho các vai trò được ma trận mục 5 dòng `FEAT-12` cấp quyền chuyển giai đoạn thủ công — cụ thể là **Nhân viên Kinh doanh, Quản lý Kinh doanh, Quản trị viên và Chủ sở hữu Workspace**. Theo **mặc định chuẩn hệ thống**, **Nhân viên Hỗ trợ, Nhân viên Marketing và Quản lý Marketing không có quyền này** nên nút bị ẩn với cả ba vai trò. Đây là mặc định, **không phải sàn bắt buộc**: tenant nới được qua `CFG-05-02` nếu chấp nhận rủi ro nêu dưới đây, khác với các ràng buộc mang nhãn `[sàn bắt buộc]` vốn không nới được ở bất kỳ mức nào. Lý do chọn mặc định này: tuyến Hỗ trợ tiếp xúc khách nhưng không thẩm định được mức độ sẵn sàng mua, còn Marketing có tầm nhìn toàn tổ chức ở dạng chỉ đọc (Ghi chú 2 mục 5) nên nếu cấp quyền chuyển giai đoạn thủ công thì một người có thể đổi giai đoạn của mọi bản ghi trong tổ chức. Giai đoạn của các bản ghi do Marketing nuôi dưỡng vẫn tiến lên được, nhưng **qua đường tự động** theo ngưỡng điểm (BR-15.5) chứ không qua thao tác tay.

---

### FEAT-03 — Quản lý Thẻ phân loại Hàng loạt (Bulk Tagging & Tag Management) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép gắn hoặc gỡ nhiều thẻ phân loại (Tags) cho một hoặc hàng loạt khách hàng cùng lúc để phục vụ việc lọc và phân khúc chiến dịch.

**Actor:** Nhân viên Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-03.1`: Hỗ trợ chọn nhiều khách hàng trên danh sách và thực hiện gắn/gỡ thẻ hàng loạt (`POST /api/v1/contacts/bulk-tag`).
- `BR-03.2`: Tên thẻ không phân biệt chữ hoa/thường, tự động cắt tỉa khoảng trắng và không vượt quá 50 ký tự mỗi thẻ.

---

### FEAT-04 — Bảo vệ Dữ liệu Nhạy cảm & Mở khóa Mặt nạ (Field Masking & Unmask) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Bảo vệ dữ liệu cá nhân nhạy cảm bằng cơ chế che mặt nạ có phân tầng theo **quan hệ của người xem với bản ghi**, thay vì che đồng loạt với mọi người. Nguyên tắc nền tảng: tách **"quyền sử dụng để liên lạc"** (nhân viên phục vụ khách hàng cần dùng số điện thoại hàng ngày) khỏi **"quyền xem giá trị thật"** (chỉ cần khi thực sự phải đọc, và luôn để lại dấu vết).

> **Mục này là nguồn chân lý duy nhất về che mặt nạ dữ liệu trong toàn tài liệu.** Mọi mục khác nói về mặt nạ (NFR-06, BR-35.4, BR-28.1, các kịch bản UAT) đều dẫn chiếu về đây và không được phát biểu lại chính sách theo cách riêng.

**Actor:** Mọi người dùng xem hồ sơ khách hàng (chịu chi phối của chính sách); riêng thao tác mở khóa yêu cầu quyền chuyên biệt `contacts:unmask`.

**Quy tắc nghiệp vụ:**
- `BR-04.1 (Ba nhóm trường nhạy cảm)`: Dữ liệu nhạy cảm được phân đúng 3 nhóm, không có nhóm nào khác:
  - **Nhóm 1 — Kênh liên lạc công việc:** email theo tên miền doanh nghiệp, số điện thoại di động và số điện thoại bàn dùng cho công việc.
  - **Nhóm 2 — Kênh liên lạc cá nhân:** email cá nhân (tên miền dịch vụ thư công cộng), số điện thoại được khách hàng khai báo là riêng tư.
  - **Nhóm 3 — Định danh KYC:** Số CCCD, Số Hộ chiếu, Ngày cấp, Nơi cấp (theo BR-01.5).
- `BR-04.2 (Bốn mức hiển thị)`: Hệ thống có đúng 4 mức hiển thị: **Đầy đủ** (thấy trọn giá trị) · **Che một phần** (ví dụ `090****567`, `m***@vinafoods.vn` — đủ để nhận diện và đối chiếu với khách, không đủ để sao chép sử dụng) · **Che hoàn toàn** (chỉ hiện dấu hiệu có dữ liệu, không hiện ký tự nào) · **Ẩn trường** (không hiển thị trường trên giao diện).
- `BR-04.3 (Chính sách hiển thị theo Quan hệ với Bản ghi)`: Chính sách mặc định chuẩn hệ thống như bảng dưới. Đây là tham số cấu hình theo tenant (Phụ lục B, `CFG-04-01`).

| Nhóm trường | (A) Người phụ trách & thành viên Đội ngũ phụ trách ở mức **Chỉnh sửa** (FEAT-35 — điều kiện tại BR-04.5b) | (B) Người trong phạm vi dữ liệu, **gồm thành viên Đội ngũ phụ trách ở mức Chỉ đọc** (BR-04.5b) | (C) Người có **quyền đọc tạm**: do đang xử lý vé/hội thoại (BR-35.4), hoặc do hệ thống tự cấp khi yêu cầu quyền truy cập quá hạn hai lần (BR-17.2c) | (D) Người ngoài phạm vi dữ liệu |
| --- | --- | --- | --- | --- |
| **1. Kênh liên lạc công việc** | **Đầy đủ** | Che một phần | **Che một phần** | Che hoàn toàn |
| **2. Kênh liên lạc cá nhân** | Che một phần | Che một phần | Che một phần | Che hoàn toàn |
| **3. Định danh KYC** | Che hoàn toàn | Che hoàn toàn | Che hoàn toàn | Ẩn trường |

  **Lý do nghiệp vụ của từng cột:** (A) Nếu che cả kênh liên lạc công việc với chính người phụ trách, tổ chức sẽ buộc phải cấp quyền `unmask` cho toàn bộ đội kinh doanh ngay tuần đầu để họ làm được việc — mặt nạ thành hình thức và `KPI-06` mất khả năng phát hiện bất thường. (C) Nhân viên Hỗ trợ đang xử lý vé/hội thoại của khách cần đủ thông tin để **xác minh đúng người** và gọi lại khi chat bị ngắt; nếu che hoàn toàn thì họ phải hỏi lại khách số điện thoại mà hệ thống đã có — trải nghiệm tệ và tổ chức lại buộc phải cấp `unmask` cho toàn tuyến Hỗ trợ, đúng thứ chính sách này muốn tránh. (D) Người không có quan hệ công việc nào với bản ghi không có nhu cầu nghiệp vụ để thấy dữ liệu liên lạc.
- `BR-04.4 (Mở khóa có kiểm toán — lối mở duy nhất)`: Người dùng có quyền `contacts:unmask` được nâng mức hiển thị lên **Đầy đủ** cho các trường thuộc Nhóm 1 và Nhóm 2 **trong phạm vi dữ liệu của mình**, và cho Nhóm 3 nếu được cấp thêm quyền chuyên biệt trên nhóm định danh KYC. Mỗi lượt mở khóa bắt buộc ghi nhật ký theo NFR-07. Quyền `unmask` **không** mở được dữ liệu ở cột (D) — người ngoài phạm vi dữ liệu phải xin quyền truy cập theo BR-17.3 trước, vì mở khóa không phải là con đường đi vòng qua phạm vi dữ liệu.
- `BR-04.5 (Hạn mức Mở khóa & Chống lấy dữ liệu hàng loạt) [Yêu cầu mới — sàn bắt buộc]`: Mỗi người dùng có hạn mức mở khóa mặc định **50 bản ghi/ngày**, cấu hình được trong khoảng **10–200** (Phụ lục B, `CFG-04-02`). **Không tồn tại lựa chọn "không giới hạn"** — đây là sàn bắt buộc, vì nếu tắt được hạn mức thì một nhân viên sắp rời tổ chức vẫn có thể mở mặt nạ hàng nghìn khách hàng trong một buổi và nhật ký chỉ ghi lại thụ động. Khi vượt hạn mức: tạm chặn thao tác mở khóa đến hết ngày, gửi cảnh báo tới Chủ sở hữu Workspace và Người phụ trách Bảo vệ Dữ liệu, ghi vào báo cáo truy cập bất thường.
- `BR-04.5b (Kiểm soát mức hiển thị Đầy đủ ở cột (A)) [Yêu cầu mới — sàn bắt buộc]`: Cột (A) là mức phơi bày cao nhất (thấy giá trị thật không cần mở khóa), nên phải có kiểm soát tương đương thao tác mở khóa, tránh việc thêm người vào Đội ngũ phụ trách trở thành đường vòng qua hạn mức tại BR-04.5:
  - **Chỉ thành viên Đội ngũ phụ trách ở mức quyền Chỉnh sửa** được hưởng cột (A). Thành viên ở mức **Chỉ đọc** và mọi thành viên mang vai trò **"Quan sát"** (A.13) áp cột (B) — che một phần.
  - **Mọi lượt thêm thành viên vào Đội ngũ phụ trách bắt buộc ghi nhật ký** theo NFR-07 (đã quy định tại BR-35.6), và số lượng bản ghi mà một người được thêm vào với mức Chỉnh sửa bị giới hạn **100 bản ghi/tháng** (Phụ lục B, `CFG-04-04`); vượt ngưỡng sẽ cảnh báo Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu.
  - **Báo cáo phơi bày định kỳ:** hằng tháng hệ thống báo cáo cho Người phụ trách Bảo vệ Dữ liệu số bản ghi mà mỗi người dùng đang có quyền xem ở mức Đầy đủ, để rà soát tích tụ quyền bất thường.
- `BR-04.6 (Liên lạc không cần Mở khóa)`: Các hành động liên lạc thực hiện **bên trong hệ thống** (bấm gọi, gửi email, gửi tin nhắn qua kênh đã tích hợp) thực hiện được ở **mọi mức hiển thị từ "Che một phần" trở lên**, không yêu cầu mở khóa và không tính vào hạn mức tại BR-04.5. Tuy nhiên các hành động này **bắt buộc được ghi nhật ký** theo NFR-07 và chịu hạn mức gửi hàng loạt riêng: mặc định **200 lượt liên lạc/người/ngày** (Phụ lục B, `CFG-04-03`), nhằm tránh việc dùng chính chức năng liên lạc để khai thác danh bạ mà không để lại dấu vết.

---

### FEAT-05 — Thùng rác Khách hàng & Phục hồi Bản ghi (Contact Recycle Bin & Restore) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi xóa một khách hàng, hệ thống thực hiện Xóa mềm (Soft Delete) và đưa vào Thùng rác trong 30 ngày. Cho phép người có thẩm quyền khôi phục lại nguyên vẹn bản ghi.

**Actor:** Quản trị viên Workspace, Người có quyền `delete` trên Contacts.

**Quy tắc nghiệp vụ:**
- `BR-05.1 (Xóa mềm)`: Đánh dấu `deletedAt = now()`, ẩn bản ghi khỏi toàn bộ danh sách tìm kiếm và báo cáo thông thường.
- `BR-05.2 (Danh sách Thùng rác)`: Cung cấp màn hình Thùng rác (`GET /api/v1/contacts/recycle-bin`) hiển thị danh sách các bản ghi đã xóa kèm ngày xóa và người thực hiện xóa.
- `BR-05.3 (Khôi phục bản ghi)`: Người có quyền `delete` được phép khôi phục bản ghi (`POST /api/v1/contacts/:id/restore`), xóa cờ `deletedAt` và phục hồi lại toàn bộ các liên kết dữ liệu cũ.
- `BR-05.4 (Dọn dẹp vĩnh viễn)`: Tiến trình hệ thống tự động xóa vĩnh viễn (Hard Delete) các bản ghi nằm trong Thùng rác quá **30 ngày** đối với gói tiêu chuẩn; thời hạn này là tham số cấu hình theo tenant (Phụ lục B, `CFG-05-01`), cho phép nâng lên tối đa 90 ngày ở gói Enterprise. Việc dọn dẹp chịu ràng buộc của các chốt an toàn tại BR-05.6.
- `BR-05.6 (Chốt An toàn trước khi Dọn dẹp Vĩnh viễn) [Yêu cầu mới]`: Hệ thống **không được** tự động xóa vĩnh viễn một bản ghi khi bản ghi đó còn thuộc bất kỳ trường hợp nào sau đây:
  - **(a)** Còn Cơ hội bán hàng hoặc Vé hỗ trợ ở trạng thái **đang mở** (theo BR-05.5, các thực thể này không bị xóa cùng Contact). Nếu vẫn xóa, Cơ hội trị giá lớn và Vé đang xử lý sẽ mất khách hàng và không thể phục hồi — đây là tình huống xảy ra thường xuyên vì nhân viên hay xóa nhầm rồi để đó.
  - **(b)** Là **bản ghi phụ do thao tác Gộp** tạo ra và vẫn còn trong thời hạn Hoàn tác gộp (BR-20.3).
  - **(c)** Còn nghĩa vụ hợp đồng, hóa đơn hoặc tranh chấp pháp lý đang xử lý (đồng bộ với BR-33.3).

  Các bản ghi thuộc diện trên được đưa vào danh sách **"Cần xử lý trước khi dọn dẹp"** kèm thông báo cho Quản trị viên. Việc xóa chỉ được thực hiện sau khi con người xử lý dứt điểm (đóng thực thể con, chuyển sang khách hàng khác, hoặc xác nhận xóa kèm lý do) — thống nhất với nguyên tắc "quyết định xóa dữ liệu luôn thuộc về con người" tại BR-33.5.
- `BR-05.5 (Xử lý Thực thể Con khi Xóa mềm) [Yêu cầu mới]`: Khi một Contact bị xóa mềm vào Thùng rác, các Vé hỗ trợ và Cơ hội bán hàng đang mở của khách hàng đó không bị xóa mà được gắn nhãn cảnh báo `[Khách hàng trong thùng rác]`; hệ thống tự động khóa tính năng gửi phản hồi công khai trên vé.

  **Lối ra khỏi khoá — tránh vòng khoá với BR-05.6:** Việc khoá phản hồi công khai được mở lại bằng **một trong ba** cách: (a) Contact được khôi phục từ Thùng rác; (b) Vé được **gán sang một Contact khác**; hoặc (c) Quản trị viên **đóng vé kèm lý do "Khách hàng đã bị xóa"**. Nếu chỉ có cách (a) thì sẽ hình thành vòng khoá: vé không phản hồi được nên khó đóng, mà vé chưa đóng thì bản ghi không bao giờ được dọn dẹp theo BR-05.6a.

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
- `BR-07.4 (Giới hạn Phạm vi Dữ liệu trong Báo cáo Hợp nhất) [Yêu cầu mới]`: Báo cáo hợp nhất tập đoàn áp dụng **phạm vi dữ liệu của người xem theo BR-01.4** làm tiêu chí nền, với **đúng một ngoại lệ theo vai trò** nêu tại mục (c) dưới đây; ngoài ngoại lệ đó, không dùng danh sách vai trò làm tiêu chí song song. Cụ thể:
  - **(a)** Số liệu hợp nhất **luôn** được tính trên đúng tập công ty nằm trong phạm vi dữ liệu của người xem, kèm ghi chú rõ khi tập đó nhỏ hơn toàn tập đoàn: "Số liệu hiển thị theo phạm vi dữ liệu của bạn — không phải toàn tập đoàn".
  - **(b)** Người xem thấy được số liệu hợp nhất **đầy đủ toàn tập đoàn** khi và chỉ khi phạm vi dữ liệu của họ bao trùm toàn bộ cây — trong ma trận mục 5 hiện tại là Quản trị viên và Chủ sở hữu, và Quản lý Kinh doanh khi cây tổ chức nằm trọn trong phạm vi phòng ban của họ.
  - **(c) Riêng vai trò Marketing:** dù có phạm vi đọc toàn tổ chức (Ghi chú 2 mục 5), Marketing **chỉ xem cấu trúc pháp nhân, không xem chỉ số tài chính hợp nhất** — vì nhu cầu nghiệp vụ của Marketing là phân khúc theo tập đoàn, không phải phân tích doanh thu. Đây là ngoại lệ duy nhất của nguyên tắc "theo phạm vi dữ liệu" và được thể hiện đúng trong ô ma trận dòng FEAT-07.
  - **(d)** Sơ đồ cây tổ chức hiển thị đầy đủ cấu trúc pháp nhân (tên công ty mẹ/con) vì đây là thông tin nhận diện, nhưng chỉ số tài chính của công ty ngoài phạm vi bị che.
- `BR-07.5 (Giới hạn số cấp phân cấp) [Yêu cầu mới]`: Cây tổ chức hỗ trợ tối đa **5 cấp** (Tập đoàn → Tổng công ty → Công ty thành viên → Chi nhánh → Đơn vị trực thuộc). Khi thiết lập vượt quá 5 cấp, hệ thống từ chối và đề nghị người dùng tổ chức lại cấu trúc.

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
- `BR-09.1 (Xử lý Liên hệ trực thuộc khi Xóa mềm Doanh nghiệp)`: Khi xóa doanh nghiệp, các liên hệ trực thuộc **không bị xóa**. Hệ thống xử lý theo mô hình Quan hệ Đa tổ chức (FEAT-10) như sau:
  - **(a) Liên kết bị vô hiệu hoá, không bị xóa:** Các bản ghi liên kết (Affiliation) giữa Contact và Doanh nghiệp bị xóa mềm được chuyển sang trạng thái `Tạm ngưng (Suspended)` và giữ nguyên toàn bộ dữ liệu chức danh, phòng ban, ngày bắt đầu/kết thúc, để có thể phục hồi nguyên vẹn nếu Doanh nghiệp được khôi phục từ Thùng rác.
  - **(b) Xác định lại Doanh nghiệp chính:** Nếu Doanh nghiệp bị xóa đang là Doanh nghiệp chính (`isPrimary`) của một Contact, hệ thống tự động đề cử liên kết đang hoạt động **còn lại có ngày bắt đầu muộn nhất** (phản ánh nơi công tác hiện tại, thống nhất với nguyên tắc tại BR-19.7) làm Doanh nghiệp chính mới và ghi nhận vào lịch sử hồ sơ. Nếu có nhiều liên kết cùng ngày bắt đầu, ưu tiên liên kết có vai trò `Chính`, sau đó là liên kết có tương tác gần nhất. Nếu Contact không còn liên kết hoạt động nào khác, Doanh nghiệp chính chuyển sang trạng thái rỗng và Contact được đưa vào danh sách "Liên hệ chưa gắn doanh nghiệp" để đội ngũ bổ sung.
  - **(c) Tùy chọn chuyển giao chủ động:** Tại bước xác nhận xóa, người thực hiện được phép chọn chuyển toàn bộ liên hệ trực thuộc sang một Doanh nghiệp khác (ví dụ trường hợp sáp nhập pháp nhân) thay vì để hệ thống xử lý theo (a) và (b).
- `BR-09.2 (Khôi phục Doanh nghiệp) [Yêu cầu mới]`: Khi Doanh nghiệp được khôi phục từ Thùng rác, toàn bộ liên kết ở trạng thái `Tạm ngưng` được phục hồi về trạng thái trước khi xóa. Nếu trong thời gian Doanh nghiệp nằm trong Thùng rác, một Contact đã được gán Doanh nghiệp chính mới, hệ thống **giữ nguyên** Doanh nghiệp chính mới và phục hồi liên kết cũ dưới dạng liên kết phụ, đồng thời thông báo cho người thực hiện khôi phục để rà soát.

---

## C. MẠNG LƯỚI QUAN HỆ ĐA CHIỀU (MULTI-AFFILIATIONS & PERSON RELATIONS)

### FEAT-10 — Quan hệ Đa Doanh nghiệp của Cá nhân (Multi-Company Affiliations) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Giải quyết bài toán một cá nhân làm việc cho nhiều công ty cùng lúc (ví dụ: Giám đốc tại Công ty A, đồng thời là Cố vấn tại Công ty B và Cổ đông tại Công ty C).

**Actor:** Nhân viên Kinh doanh, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-10.1 (Bản ghi liên kết Affiliation)`: Mỗi liên kết giữa 1 Contact và 1 Account lưu trữ: Chức danh (Job Title), Phòng ban công tác, **Vai trò** (chọn từ danh mục A.5), Ngày bắt đầu, Ngày kết thúc, **Trạng thái** (chọn từ danh mục A.5b: Đang công tác / Đã nghỉ việc / Tạm ngưng).
- `BR-10.4 (Xử lý khi Liên kết chuyển sang Đã nghỉ việc) [Yêu cầu mới]`: Khi một liên kết chuyển sang trạng thái "Đã nghỉ việc", hệ thống: **(a)** tự động gắn trạng thái khả năng tiếp cận **`OBSOLETE` (Không còn hiệu lực)** — giá trị mới tại danh mục A.10, **khác** với `BOUNCED` — cho email/số điện thoại công việc thuộc doanh nghiệp đó, và loại chúng khỏi các chiến dịch tự động. Lý do phải dùng giá trị riêng: `BOUNCED` là trạng thái kỹ thuật và theo BR-19.6 nó **luôn thắng** khi gộp bản ghi, nên nếu gán `BOUNCED` cho một lý do nghiệp vụ thì trạng thái sai này không thể đảo lại được sau khi gộp. `OBSOLETE` có thể được người dùng đảo lại khi khách quay lại công ty cũ; **(b)** nếu đó là Doanh nghiệp chính, đề cử Doanh nghiệp chính mới theo nguyên tắc tại BR-09.1b; **(c)** cảnh báo trên hồ sơ Doanh nghiệp nếu công ty đó không còn liên hệ hoạt động nào hoặc không còn Người liên hệ chính. Lý do: nếu không xử lý, danh sách khách hàng và mọi báo cáo tổng quan sẽ hiển thị khách hàng gắn với công ty họ đã rời — nhân viên gọi vào tổng đài công ty cũ, hợp đồng xuất sai pháp nhân, và chiến dịch email tiếp tục bắn vào địa chỉ đã bị hủy.
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

### FEAT-12 — Quản trị Giai đoạn Vòng đời Khách hàng (10 Lifecycle Stages) & Ma trận Chuyển đổi `[Đã triển khai]`

**Mô tả nghiệp vụ:** Phân loại khách hàng theo đúng vị trí trên hành trình trải nghiệm qua các giai đoạn chuẩn quốc tế. Gồm 2 nhóm: (a) **7 giai đoạn phễu tuyến tính chính**, phản ánh tiến trình thăng hạng thông thường từ nhận diện đến trung thành; và (b) **3 trạng thái đặc biệt** (Nurturing, Churned, Disqualified) nằm ngoài đường tuyến tính, dùng để xử lý các nhánh rẽ/thoát phễu và không được tính vào các báo cáo vận tốc phễu tuyến tính (xem BR-13.3).

**Actor:** Nhân viên Kinh doanh (chuyển giai đoạn tiến lên trong phạm vi được gán), **Quản lý Kinh doanh trở lên** (bước lùi và chuyển sang `Disqualified` — BR-12.4, BR-12.7), **Quản trị viên và Chủ sở hữu Workspace** (bao gồm ngoại lệ gian lận đối với `Customer`/`Evangelist`/`Churned` mà Quản lý Kinh doanh không có quyền — BR-12.8), Quản lý Marketing (cấu hình vòng đời tự động — BR-15.4), Nhân viên Marketing (chỉ xem cấu hình), Tiến trình Hệ thống (các bước chuyển tự sinh theo BR-12.9).

**Chi tiết các giai đoạn vòng đời:**

*Nhóm 7 giai đoạn phễu tuyến tính chính:*
1. **Người Đăng ký (Subscriber):** Khách mới đăng ký nhận bản tin/tài liệu, chưa có nhu cầu mua rõ ràng.
2. **Khách hàng Tiềm năng (Lead):** Đã để lại thông tin liên hệ và thể hiện sự quan tâm ban đầu.
3. **Tiềm năng Đủ điều kiện Tiếp thị (Marketing Qualified Lead — MQL):** Đã tương tác nhiều lần qua các chiến dịch tiếp thị và đạt điểm tiềm năng ban đầu.
4. **Tiềm năng Đủ điều kiện Bán hàng (Sales Qualified Lead — SQL):** Đã được đội ngũ kinh doanh thẩm định trực tiếp và sẵn sàng trao đổi cơ hội mua hàng.
5. **Cơ hội Kinh doanh (Opportunity):** Đang có ít nhất một Cơ hội bán hàng (Deal) đang mở trên phễu.
6. **Khách hàng Chính thức (Customer):** Đã ký hợp đồng hoặc phát sinh giao dịch mua hàng thành công.
7. **Khách hàng Trung thành / Đại sứ (Evangelist):** Khách hàng gắn bó lâu năm, sẵn sàng giới thiệu khách hàng mới.

*Nhóm 3 trạng thái đặc biệt (ngoài phễu tuyến tính):*
8. **Đang Nuôi dưỡng (Nurturing):** Lead chưa sẵn sàng mua ngay (hết ngân sách, chờ phê duyệt nội bộ) nhưng vẫn có tiềm năng. Tiếp tục được chăm sóc qua chiến dịch định kỳ.
9. **Đã Rời bỏ (Churned / Former Customer):** Khách hàng đã hủy dịch vụ hoặc chấm dứt hợp đồng. Không tiếp tục nhận Email Marketing tự động trừ khi có chiến dịch Win-Back được phê duyệt riêng.
10. **Đã Loại (Disqualified):** Lead không phù hợp với tập khách hàng mục tiêu (Sai ngành, Không đủ ngân sách, Lừa đảo/Spam). Bị loại khỏi mọi chiến dịch marketing.

**Ma trận Chuyển đổi Giai đoạn (Stage Transition Matrix):**

**Bốn nguyên tắc chi phối các bước chuyển trên phễu tuyến tính.** Bốn nguyên tắc dưới đây chi phối **các bước chuyển giữa 7 giai đoạn phễu tuyến tính**; các bước chuyển **ra/vào 3 trạng thái đặc biệt** (Nurturing, Churned, Disqualified) do các quy tắc riêng chi phối và cũng được liệt kê trong cùng một bảng để người đọc chỉ phải tra một chỗ:

- Sang `Nurturing`: BR-12.3 (toàn bộ Cơ hội `Closed Lost`, lý do từ A.2), BR-16.4 (điểm nguội ở bốn giai đoạn đầu phễu, lý do từ A.16), và BR-12.5b (nhánh Win-Back từ `Churned`).
- **Ra khỏi** `Nurturing` về `Lead`: BR-16.5 — đường quay lại phễu cho hồ sơ đã hoạt động trở lại nhưng chưa đủ Ngưỡng MQL.
- Sang `Churned`: BR-12.5 — chỉ từ `Customer`/`Evangelist`, tức chỉ khách đã từng trả tiền mới "rời bỏ" được; các dòng khác chỉ đến `Churned` qua đường gộp theo ngoại lệ (ii) tại BR-12.6.
- Sang `Disqualified`: BR-12.4 (các giai đoạn tiền bán hàng, quyền Quản lý Kinh doanh trở lên), BR-12.4b (loại nhanh Lead rác), BR-12.8 (ngoại lệ gian lận đối với `Customer`/`Evangelist`/`Churned`, chỉ Quản trị viên/Chủ sở hữu).
- Sang `Evangelist`: **chỉ từ `Customer`** — theo định nghĩa giai đoạn tại mục dưới, `Evangelist` là khách hàng đang trả tiền có hành vi giới thiệu, nên buộc phải đi qua `Customer` trước; đây là ngoại lệ có chủ đích của nguyên tắc 2 và là lý do duy nhất một bước tiến lên bị chặn.

Bốn nguyên tắc:

1. **Giai đoạn phải phản ánh thực tế bán hàng.** Khi thực tế đã phát sinh Cơ hội bán hàng hoặc Cơ hội đã thắng, giai đoạn buộc phải cập nhật theo — kể cả khi phải nhảy nhiều bậc. Đây là nguyên tắc mạnh nhất, ưu tiên cao hơn ba nguyên tắc còn lại.
2. **Tiến lên trên phễu là tự do có điều kiện chuyên môn.** Bước tiến lên không cần quyền đặc biệt, nhưng bước `→ SQL` bắt buộc có thẩm định của con người (BR-15.6) và bước `→ MQL` tự động theo ngưỡng điểm (BR-15.5).
3. **Lùi lại trên phễu là hành vi có kiểm soát.** Cần quyền Quản lý Kinh doanh trở lên và bắt buộc ghi lý do (BR-12.7), trừ trường hợp Hoàn tác Chuyển đổi đã có lý do riêng (BR-14.2). **Sàn của nguyên tắc này:** `Subscriber` **không** phải là đích lùi thủ công từ bất kỳ giai đoạn nào — `Subscriber` được định nghĩa là hồ sơ **chưa có tương tác nào**, nên hạ một hồ sơ đã có tương tác về đó là ghi sai lịch sử. Đường duy nhất trở lại `Subscriber` là **Hoàn tác Chuyển đổi** (BR-14.2), khi bản ghi vốn đã ở `Subscriber` trước lúc chuyển đổi. Vì vậy chỉ hai dòng `SQL` và `Opportunity` — hai giai đoạn có thể là kết quả của một lần chuyển đổi — mới có `Subscriber` trong danh sách đích.
4. **Trạng thái khách hàng đang trả tiền được bảo vệ tuyệt đối.** `Customer` và `Evangelist` không bao giờ bị hạ về giai đoạn tiền bán hàng; chỉ ra khỏi hai giai đoạn này qua `Churned` (rời bỏ) hoặc `Disqualified` (phát hiện gian lận, theo BR-12.8).

| Từ giai đoạn | Được phép chuyển đến | Điều kiện / Quyền yêu cầu |
| --- | --- | --- |
| **Subscriber** | Lead, MQL, SQL, Opportunity, **Customer**, Nurturing, Disqualified, *Churned (chỉ qua gộp)* | Lên `Lead`: **tự động khi có tương tác đầu tiên** — "tương tác đầu tiên" là lượt tương tác đầu tiên được ghi nhận thành bản ghi hoạt động theo BR-36.5 hoặc lượt tương tác đầu tiên được chấm Điểm Tương tác theo FEAT-15. Lên `MQL`: tự động khi đạt Ngưỡng MQL (BR-15.5). Lên `SQL`: qua thẩm định của Sales hoặc Chuyển đổi 1-Click không kèm Cơ hội (BR-15.6, FEAT-14). Lên `Opportunity` và `Customer`: **nguyên tắc 1**. Sang `Nurturing`: lý do từ A.16 (BR-16.4). Sang `Disqualified`: BR-12.4. Sang `Churned`: **chỉ qua đường gộp bản ghi** theo BR-19.8 (ngoại lệ (ii) tại BR-12.6), không có đường thủ công — khách chưa từng là khách hàng thì không thể "rời bỏ" |
| **Lead** | MQL, SQL, Opportunity, **Customer**, Nurturing, Disqualified, *Churned (chỉ qua gộp)* | Lên `MQL`: BR-15.5. Lên `SQL`: thẩm định của Sales hoặc Chuyển đổi 1-Click không kèm Cơ hội (BR-15.6, FEAT-14). Lên `Opportunity` và `Customer`: **nguyên tắc 1** (`→ Customer` khi có Cơ hội `Closed Won`, kể cả nhảy bậc). Sang `Nurturing`: lý do từ A.16 (BR-16.4). Sang `Disqualified`: BR-12.4. Sang `Churned`: **chỉ qua đường gộp bản ghi** theo BR-19.8 (ngoại lệ (ii) tại BR-12.6), không có đường thủ công — khách chưa từng là khách hàng thì không thể "rời bỏ" |
| **MQL** | SQL, Opportunity, **Customer**, Nurturing, Disqualified, *Lead (lùi)*, *Churned (chỉ qua gộp)* | Lên `SQL`: thẩm định của Sales (BR-15.6). Lên `Opportunity` và `Customer`: nguyên tắc 1. Lùi về `Lead`: nguyên tắc 3, lý do từ A.3. Sang `Nurturing`: lý do từ A.16 (BR-16.4). Sang `Disqualified`: BR-12.4. Sang `Churned`: **chỉ qua đường gộp bản ghi** theo BR-19.8 (ngoại lệ (ii) tại BR-12.6), không có đường thủ công — khách chưa từng là khách hàng thì không thể "rời bỏ" |
| **SQL** | Opportunity, **Customer**, Nurturing, Disqualified, *MQL, Lead, Subscriber (lùi)*, *Churned (chỉ qua gộp)* | Lên `Opportunity` và `Customer`: nguyên tắc 1. Sang `Nurturing`: lý do từ A.16 (BR-16.4). Lùi về `MQL`: nguyên tắc 3. Về `Lead`/`Subscriber`: **chỉ** qua Hoàn tác Chuyển đổi, trả về đúng giai đoạn trước khi chuyển đổi (BR-14.2). Sang `Disqualified`: BR-12.4. Sang `Churned`: **chỉ qua đường gộp bản ghi** theo BR-19.8 (ngoại lệ (ii) tại BR-12.6), không có đường thủ công — khách chưa từng là khách hàng thì không thể "rời bỏ" |
| **Opportunity** | Customer, Nurturing, Disqualified, *SQL, MQL, Lead, Subscriber (lùi)*, *Churned (chỉ qua gộp)* | Lên `Customer`: nguyên tắc 1, khi có Cơ hội `Closed Won`. Sang `Nurturing`: khi toàn bộ Cơ hội `Closed Lost`, bắt buộc Lý do không chuyển đổi từ A.2 (BR-12.3). Về `Lead`/`Subscriber`: **chỉ** qua Hoàn tác Chuyển đổi (BR-14.2). Lùi khác: nguyên tắc 3. Sang `Disqualified`: BR-12.4. Sang `Churned`: **chỉ qua đường gộp bản ghi** theo BR-19.8 (ngoại lệ (ii) tại BR-12.6), không có đường thủ công — khách chưa từng là khách hàng thì không thể "rời bỏ" |
| **Customer** | Evangelist, Churned, *Disqualified (ngoại lệ gian lận)* | **Nguyên tắc 4** — không được hạ về Subscriber/Lead/MQL/SQL/Opportunity/Nurturing. Sang `Disqualified` chỉ khi phát hiện gian lận và chỉ bởi Quản trị viên/Chủ sở hữu (BR-12.8) |
| **Evangelist** | Customer, Churned, *Disqualified (ngoại lệ gian lận)* | **Nguyên tắc 4.** Về `Customer`: nguyên tắc 3, lý do từ A.3. Sang `Disqualified`: chỉ theo BR-12.8 |
| **Nurturing** | MQL, SQL, Opportunity, Customer, Disqualified, *Lead (quay lại)*, *Churned (chỉ qua gộp)* | Về `Lead`: **BR-16.5** — dùng khi hồ sơ vào `Nurturing` từ `Lead` và nay có tương tác trở lại nhưng chưa đủ Ngưỡng MQL; cần quyền Quản lý Kinh doanh và lý do từ A.18, để hồ sơ quay lại đúng vị trí phễu thay vì mắc kẹt ngoài phễu và mất khỏi báo cáo vận tốc (BR-13.3). Lên `MQL`: tự động khi đạt lại Ngưỡng MQL (BR-15.5). Lên `SQL`: thẩm định của Sales. Lên `Opportunity` và `Customer`: **nguyên tắc 1** (khách đang nuôi dưỡng mở Cơ hội, hoặc chốt được đơn ngay). Sang `Disqualified`: BR-12.4. Sang `Churned`: **chỉ qua đường gộp bản ghi** theo BR-19.8 (ngoại lệ (ii) tại BR-12.6), không có đường thủ công — khách chưa từng là khách hàng thì không thể "rời bỏ" |
| **Churned** | Customer, Opportunity, Nurturing, *Disqualified (ngoại lệ gian lận)* | Về `Customer` và `Opportunity`: **nguyên tắc 1** — khách cũ quay lại, mở Cơ hội win-back hoặc ký lại hợp đồng. Về `Nurturing`: chỉ khi thuộc chiến dịch Win-Back đã được phê duyệt theo BR-12.5b. Sang `Disqualified`: vì `Churned` là khách **đã từng trả tiền**, áp đúng BR-12.8 — chỉ Quản trị viên/Chủ sở hữu, chỉ với lý do thuộc nhóm gian lận, không áp BR-12.4 |
| **Disqualified** | Lead, Nurturing, Opportunity, **Customer**, *Churned (chỉ qua gộp)* | Về `Lead`/`Nurturing`: chỉ Quản lý Kinh doanh trở lên khi có bằng chứng mới, bắt buộc chọn **Lý do mở lại** từ danh mục **A.17**. Lên `Opportunity` và `Customer`: **nguyên tắc 1** — nếu khách bị loại trước đây nay thực sự phát sinh Cơ hội hoặc đã ký hợp đồng, giai đoạn phải phản ánh thực tế đó và hệ thống cảnh báo cho Quản lý rà soát lại quyết định loại |

*Ghi chú đọc bảng: các giai đoạn in nghiêng kèm "(lùi)" là bước chuyển lùi trên phễu tuyến tính, chịu nguyên tắc 3. **Ma trận này điều chỉnh các bước chuyển do người dùng quyết định; giai đoạn do thao tác gộp bản ghi sinh ra được BR-19.8 tính và nằm ngoài phạm vi ma trận theo ngoại lệ (ii) tại BR-12.6.** Bước chuyển do hệ thống tự sinh theo nguyên tắc 1 được coi là hợp lệ theo thiết kế và không cần quyền đặc biệt (chi tiết tại BR-12.9). Ma trận là tham số cấu hình theo tenant (Phụ lục B, `CFG-12-01`); riêng nguyên tắc 4 (BR-12.3, BR-12.8) và ràng buộc quyền loại khách (BR-12.4) là sàn bắt buộc, tenant không được nới lỏng.*

**Quy tắc nghiệp vụ:**
- `BR-12.1 (Chính sách chuyển đổi giai đoạn)`: Tuân thủ quy tắc chuyển đổi có kiểm soát; khi chuyển giai đoạn, hệ thống ghi nhận thời điểm chuyển và người thực hiện.
- `BR-12.2 (Tự động nâng cấp)`: Khi một liên hệ được tạo mới một Deal, hệ thống tự động nâng cấp giai đoạn lên tối thiểu là `Opportunity`. Khi Deal chuyển sang `Closed Won`, hệ thống tự động nâng cấp lên `Customer`.
- `BR-12.3 (Quy tắc Đa Cơ hội & Xử lý khi Deal Thất bại) [Yêu cầu mới]`:
  - Khách hàng đã đạt giai đoạn `Customer` (do có ít nhất 1 Deal `Closed Won`) sẽ **không bị hạ hạng** khi có các Deal Upsell/Cross-sell tiếp theo bị `Closed Lost`.
  - Đối với Contact chưa từng là `Customer`: khi tất cả Deal đều `Closed Lost`, hệ thống **không tự động hạ cấp** mà chuyển sang giai đoạn `Nurturing` và yêu cầu Sales nhập "Lý do không chuyển đổi" để Marketing có kịch bản tái tiếp cận phù hợp.
- `BR-12.4 (Chuyển Disqualified)`: Chỉ người dùng có quyền `contacts:disqualify` (Quản lý Kinh doanh trở lên) mới được phép chuyển Contact sang trạng thái `Disqualified`. Bắt buộc nhập lý do loại từ danh mục chuẩn A.1.
- `BR-12.4b (Loại nhanh Lead rác bởi nhân viên) [Yêu cầu mới]`: **Ngoại lệ của BR-12.4 cho hai nhóm lý do hiển nhiên**: `Thông tin giả/Spam/Lừa đảo` và `Trùng lặp với bản ghi khác` (A.1). Nhân viên Kinh doanh được **đánh dấu "Lead rác"** với hai lý do này, và việc đánh dấu có hiệu lực **ngay lập tức** ở ba mặt: (a) **đình chỉ đồng hồ cam kết thời gian** tại BR-31.7; (b) **loại bản ghi khỏi mẫu đo `KPI-03`**; (c) **dừng cơ chế thu hồi và phân bổ lại** — Lead rác không được chia cho người khác. **Mọi lượt đánh dấu, dỡ dấu và duyệt "Lead rác" đều bắt buộc ghi nhật ký** theo NFR-07: ba hiệu lực trên tác động trực tiếp tới cam kết thời gian và tới `KPI-03` của cả đội, nên phải có dấu vết để rà soát khi chỉ số bất thường. **Phạm vi áp dụng:** chỉ các **giai đoạn tiền bán hàng**, định nghĩa thống nhất trong toàn tài liệu là **sáu giai đoạn** Subscriber, Lead, MQL, SQL, Opportunity và **Nurturing** — `Nurturing` thuộc nhóm này vì đó là hồ sơ chưa từng trả tiền, chỉ tạm ra khỏi đường tuyến tính. Hồ sơ ở `Customer`/`Evangelist` **không** thuộc phạm vi quy tắc này — nhóm lý do gian lận đối với hai giai đoạn đó thuộc thẩm quyền riêng của Quản trị viên/Chủ sở hữu theo BR-12.8, và `Churned` áp đúng BR-12.8 theo ma trận.

  **Xử lý sau khi đánh dấu:** Quản lý Kinh doanh duyệt theo lô. Việc chuyển bản ghi sang `Disqualified` **luôn cần một thao tác tường minh của Quản lý Kinh doanh trở lên** — **không có** cơ chế mặc-định-chấp-thuận cho bước chuyển giai đoạn, vì sàn bắt buộc tại `CFG-12-01` (quyền loại khách không được nới xuống dưới mức Quản lý Kinh doanh) và ô ma trận của Nhân viên Kinh doanh ("chỉ bước tiến lên") đều không cho phép. Nếu Quản lý không xử lý trong **5 ngày làm việc** (tính theo Lịch làm việc tại BR-31.7b), hệ thống **giữ nguyên** hiệu lực ba mặt của dấu "Lead rác" và **leo thang** hàng đợi chờ duyệt lên Quản trị viên Workspace kèm báo cáo tồn đọng; bản ghi **đứng nguyên giai đoạn hiện tại**. Nếu Quản lý từ chối, dấu "Lead rác" bị dỡ và đồng hồ cam kết chạy lại từ thời điểm từ chối.

  Lý do nghiệp vụ: spam và trùng lặp là hai lý do loại phổ biến nhất hằng ngày, trong khi Lead rác từ Form web/Chatbot vẫn được phân bổ tự động và vẫn tính vào cam kết thời gian. Nếu cả hai đều đòi quyền Quản lý, mỗi Lead rác sẽ đi qua 3 nhân viên (2 lần thu hồi) và làm bẩn chỉ số của cả ba, còn Quản lý trở thành thư ký bấm nút cho từng dòng — dẫn tới cách lách là gửi email rỗng cho mọi Lead mới để đóng dấu bằng chứng, đúng hành vi mà BR-31.8 muốn ngăn.
- `BR-12.5b (Chiến dịch Win-Back & đường về Nurturing của khách đã rời bỏ) [Yêu cầu mới]`: Một bản ghi ở `Churned` chỉ được đưa trở lại `Nurturing` khi thuộc một **Chiến dịch Win-Back đã được phê duyệt**. Chiến dịch Win-Back là chiến dịch tiếp thị nhắm vào tập khách đã rời bỏ, và vì nhóm này đã chủ động chấm dứt quan hệ nên việc tiếp cận lại có rủi ro pháp lý và rủi ro thương hiệu cao hơn chiến dịch thông thường. Ba ràng buộc: **(a) Người phê duyệt** là **Quản lý Marketing cùng Quản lý Kinh doanh phụ trách tập khách đó** — cần cả hai vì một bên chịu trách nhiệm nội dung tiếp cận, một bên chịu trách nhiệm quan hệ khách hàng; **(b)** phê duyệt được ghi nhận trên chính chiến dịch kèm phạm vi tập khách, thời hạn hiệu lực và người phê duyệt, và **ghi nhật ký** theo NFR-07; **(c)** phê duyệt chiến dịch Win-Back **không** ghi đè trạng thái đồng thuận — bản ghi đang ở `OPT_OUT` vẫn không nhận được thư nhóm Tiếp thị (BR-30.5, BR-30.10), nên trên thực tế chiến dịch chỉ chạm tới tập khách còn `OPT_IN`. Không có quy tắc này thì cụm từ "chiến dịch Win-Back được phê duyệt" được viện dẫn ba nơi trong tài liệu mà không ai biết ai phê duyệt và theo quy trình nào.
- `BR-16.5 (Đường quay lại phễu từ Nurturing) [Yêu cầu mới]`: Hồ sơ ở `Nurturing` có tương tác trở lại nhưng **chưa đạt Ngưỡng MQL** được chuyển về `Lead` bởi **Quản lý Kinh doanh trở lên**, bắt buộc chọn lý do từ danh mục **A.18**. Không dùng A.3 vì A.3 là danh mục **hạ hạng** trên phễu tuyến tính theo BR-12.7 ("thẩm định lại không đủ điều kiện", "sai sót nhập liệu"), trong khi bước chuyển này là **quay lại phễu** với căn cứ ngược hẳn — hồ sơ đã hoạt động trở lại. Nếu đạt lại Ngưỡng MQL thì hệ thống tự thăng lên `MQL` theo BR-15.5 và quy tắc này không áp dụng. Lý do phải có đường này: `Nurturing` là trạng thái ngoài phễu tuyến tính nên thời gian nằm ở đó **không** tính vào vận tốc phễu (BR-13.3); một hồ sơ đã hoạt động trở lại mà mắc kẹt ngoài phễu sẽ biến mất khỏi mọi báo cáo chuyển đổi và không ai theo dõi.
- `BR-12.5 (Chuyển Churned)`: Khi Contact chuyển sang `Churned`, tự động gửi thông báo nội bộ đến Quản lý Khách hàng Hiện hữu (Account Manager — vai trò chức năng theo mục 2.2) và Quản lý Kinh doanh phụ trách, đồng thời tắt toàn bộ chiến dịch Marketing tự động.
- `BR-12.6 (Hiệu lực Ma trận Chuyển đổi) [Yêu cầu mới]`: Ma trận Chuyển đổi Giai đoạn áp dụng cho mọi nguồn tác động: thao tác thủ công của người dùng, **thao tác gộp bản ghi** (BR-19.8), nhập khẩu hàng loạt, tích hợp qua giao diện lập trình, và chuyển đổi tự động do hệ thống. Bước chuyển nằm ngoài ma trận bị từ chối kèm thông báo nêu rõ giai đoạn hiện tại và các giai đoạn hợp lệ có thể chuyển đến.

  **Ngoại lệ duy nhất — nguyên tắc 1:** Bước chuyển do hệ thống tự sinh từ sự kiện của Cơ hội bán hàng (chi tiết tại BR-12.9) và bước chuyển do nguyên tắc giai đoạn tiến xa nhất khi gộp bản ghi (BR-19.8) **luôn hợp lệ theo thiết kế**, vì chúng chỉ làm giai đoạn phản ánh đúng một thực tế đã xảy ra. Điều khoản này khẳng định thứ tự ưu tiên trong hai tình huống: **(i)** tenant cấu hình lại ma trận theo `CFG-12-01` và vô tình tắt một bước chuyển thuộc nguyên tắc 1 — nguyên tắc 1 vẫn thắng; **(ii)** thao tác gộp sinh ra bước chuyển theo BR-19.8 — **mọi** bước chuyển thuộc nhóm này hợp lệ theo thiết kế, **bất kể ma trận có liệt kê hay không**.

  **Vì sao nhóm (ii) được đặt ngoài ma trận thay vì liệt kê từng ô:** giai đoạn sau gộp **không phải một quyết định của người dùng về giai đoạn** mà là hệ quả bắt buộc của nguyên tắc giai đoạn tiến xa nhất — người dùng chỉ quyết định gộp hai bản ghi nào, còn giai đoạn kết quả do BR-19.8 tính ra và **không thể ghi đè thủ công**. Nếu buộc liệt kê, ma trận phải chứa gần như mọi cặp giai đoạn (kể cả `Lead → Evangelist`, `Disqualified → SQL`, `Nurturing → Evangelist`), và khi đó ma trận mất luôn ý nghĩa kiểm soát đối với thao tác thủ công — vốn là mục đích duy nhất nó được dựng lên. Đổi lại, mỗi bước chuyển sinh từ gộp **bắt buộc** ghi nhật ký kèm thông báo cho Người phụ trách rà soát, và hoàn tác được trong 90 ngày theo BR-20.3.

  Các ô `→ Churned` mang nhãn "*chỉ qua gộp*" trong ma trận là **ví dụ minh hoạ** của nhóm (ii) được ghi tường minh vì hay gặp nhất trong vận hành, không phải danh sách đóng. Ngoài hai tình huống (i) và (ii), mọi bước chuyển đều phải nằm trong ma trận.

  Riêng nhập khẩu hàng loạt: dòng dữ liệu chứa bước chuyển không hợp lệ được ghi vào báo cáo lỗi (BR-24.2) thay vì làm gián đoạn toàn bộ tiến trình nhập.
- `BR-12.7 (Hạ hạng bắt buộc ghi lý do) [Yêu cầu mới]`: Mọi bước chuyển hạ hạng (về giai đoạn thấp hơn trên phễu tuyến tính) bắt buộc nhập lý do từ danh mục chuẩn tại Phụ lục A.3 và chỉ dành cho Quản lý Kinh doanh trở lên. Quy tắc này áp dụng cho cả bước `Evangelist → Customer`. Lịch sử hạ hạng được ghi nhận riêng để phục vụ phân tích chất lượng thẩm định của đội ngũ (FEAT-13). **Ngoại lệ duy nhất:** bước chuyển về `Lead` hoặc `Subscriber` do Hoàn tác Chuyển đổi (BR-14.2) không yêu cầu lý do hạ hạng vì đã có lý do hoàn tác riêng.
- `BR-12.8 (Ngoại lệ Gian lận đối với Khách hàng Chính thức) [Yêu cầu mới]`: Trường hợp phát hiện một Contact ở giai đoạn `Customer`, `Evangelist` **hoặc `Churned`** là gian lận (thông tin giả, mạo danh, lừa đảo), **chỉ Quản trị viên hoặc Chủ sở hữu Workspace** được phép chuyển sang `Disqualified` với lý do thuộc nhóm gian lận tại danh mục A.1. `Churned` thuộc phạm vi quy tắc này vì đó là khách **đã từng trả tiền** — việc loại họ vẫn tác động tới doanh thu đã ghi nhận, đúng rủi ro mà quy tắc nhắm tới. Hệ thống bắt buộc hiển thị cảnh báo về ảnh hưởng tới báo cáo doanh thu đã ghi nhận và yêu cầu xác nhận hai bước. Quản lý Kinh doanh **không** có quyền này (khác với BR-12.4 áp dụng cho các giai đoạn tiền bán hàng). Ba giai đoạn `Customer`/`Evangelist`/`Churned` (theo quy tắc này) và sáu giai đoạn tiền bán hàng (theo BR-12.4) hợp thành **đầy đủ chín giai đoạn có thể bị loại**. Giai đoạn thứ mười là chính `Disqualified` — một bản ghi đã bị loại thì không loại lại được, đường duy nhất ra khỏi nó là mở lại theo ma trận với lý do từ A.17.
- `BR-12.9 (Bước chuyển do Hệ thống sinh ra từ Sự kiện Cơ hội bán hàng) [Yêu cầu mới]`: Các bước chuyển do hệ thống tự sinh từ sự kiện của Cơ hội bán hàng — cụ thể là `→ Opportunity` khi Cơ hội mở đầu tiên được tạo, `→ Customer` khi có Cơ hội `Closed Won`, và `→ Lead` hoặc `→ Subscriber` khi Hoàn tác Chuyển đổi (trả về đúng giai đoạn trước khi chuyển đổi) — được coi là **hợp lệ theo thiết kế** và không bị chặn bởi ma trận, kể cả khi bước chuyển đó nhảy nhiều bậc (ví dụ `Subscriber → Opportunity`). Lý do nghiệp vụ: định nghĩa của giai đoạn `Opportunity` là "đang có ít nhất một Cơ hội bán hàng mở", nên khi thực tế đã có Cơ hội thì giai đoạn buộc phải phản ánh đúng thực tế đó. Các bước chuyển này vẫn được ghi vào lịch sử giai đoạn với người thực hiện là "Hệ thống" kèm sự kiện nguồn.
- `BR-12.10 (Giai đoạn mặc định khi tạo mới) [Yêu cầu mới]`: Giai đoạn vòng đời khi khởi tạo bản ghi được gán tự động theo nguồn tạo, là tham số cấu hình theo tenant (Phụ lục B, `CFG-12-02`) với giá trị mặc định chuẩn hệ thống:
  - Tạo thủ công bởi nhân viên, tạo từ Form web, tạo từ Chuyển đổi hội thoại, nhập khẩu từ tệp → `Lead`
  - Đăng ký nhận bản tin/tài liệu (không thể hiện nhu cầu mua) → `Subscriber`
  - Hồ sơ Khách hàng Tạm (BR-01.1b) → chưa gán giai đoạn cho tới khi trở thành Contact chính thức

---

### FEAT-13 — Lịch sử Chuyển đổi Giai đoạn Vòng đời (Stage Transition History) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Lưu vết toàn bộ lịch sử thăng hạng/hạ hạng giai đoạn vòng đời của khách hàng để phục vụ phân tích tỷ lệ chuyển đổi và vận tốc bán hàng (Sales Velocity).

**Actor:** Mọi người dùng có quyền xem Contact.

**Quy tắc nghiệp vụ:**
- `BR-13.1`: Ghi nhận: Giai đoạn trước, Giai đoạn sau, Thời gian ở giai đoạn cũ (thời lượng tính bằng ngày/giờ), Lý do chuyển đổi, Người thực hiện.
- `BR-13.2`: Cung cấp API tra cứu lịch sử `/api/v1/contacts/:id/stage-history`.
- `BR-13.3 (Phạm vi tính Vận tốc Phễu) [Yêu cầu mới]`: Báo cáo tỷ lệ chuyển đổi và vận tốc bán hàng (Sales Velocity) chỉ tính toán trên 7 giai đoạn phễu tuyến tính chính (Subscriber → Evangelist). Thời gian một bản ghi nằm ở 3 trạng thái đặc biệt (Nurturing, Churned, Disqualified) được báo cáo riêng dưới dạng "thời gian ngoài phễu" (off-funnel dwell time), không cộng dồn vào vận tốc phễu chính để tránh sai lệch số liệu.

---

### FEAT-14 — Quy trình Chuyển đổi Khách hàng Tiềm năng 1-Click (Lead Conversion) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khi một Khách hàng tiềm năng (Lead) được thẩm định đủ điều kiện mua hàng, cho phép nhân viên kinh doanh kích hoạt quy trình Chuyển đổi (Convert Lead) chỉ bằng 1 thao tác bấm nút.

**Actor:** Nhân viên Kinh doanh, Quản lý Kinh doanh.

**Luồng chính chuyển đổi:**
1. Người dùng bấm nút **"Chuyển đổi Tiềm năng" (Convert Lead)** trên hồ sơ Lead.
2. Hộp thoại chuyển đổi hiển thị với 3 tùy chọn liên kết:
   - **Liên hệ (Contact):** Nâng cấp bản ghi hiện tại thành Contact chính thức. Giai đoạn đích: `SQL` nếu không tạo Cơ hội bán hàng kèm theo; `Opportunity` nếu có tạo Cơ hội. Cả hai bước chuyển đều hợp lệ kể cả khi Contact đang ở `Subscriber`/`Lead`/`MQL`: nhánh `→ Opportunity` theo **BR-12.9** (bước chuyển tự sinh từ sự kiện Cơ hội bán hàng); nhánh `→ SQL` theo **BR-15.6** và đã được liệt kê tường minh trong ma trận FEAT-12 ở cả ba dòng đó.
   - **Doanh nghiệp (Account):** Chọn liên kết với một Doanh nghiệp đã có sẵn hoặc tự động tạo mới Doanh nghiệp từ tên công ty của Lead.
   - **Cơ hội Bán hàng (Deal):** Tùy chọn tạo ngay một Cơ hội bán hàng mới (nhập Tên Deal, Giá trị dự kiến, Phễu bán hàng và Giai đoạn khởi đầu).
3. Người dùng bấm "Xác nhận chuyển đổi".
4. **Hệ thống thực thi giao dịch nguyên tử (Atomic Transaction):** Cập nhật Contact, tạo/liên kết Account, tạo Deal, gán quyền sở hữu đồng nhất và chuyển hướng người dùng đến Cơ hội bán hàng vừa tạo.

**Quy tắc nghiệp vụ:**
- `BR-14.1 (Rollback toàn phần khi lỗi)`: Nếu bất kỳ bước nào trong giao dịch nguyên tử thất bại, toàn bộ giao dịch bị hủy (rollback). Không tạo Contact/Account/Deal ở trạng thái dang dở.
- `BR-14.2 (Hoàn tác Chuyển đổi — Undo Conversion) [Yêu cầu mới]`: Trong vòng **24 giờ** (tham số cấu hình theo tenant, Phụ lục B `CFG-14-01`) sau khi chuyển đổi thành công, Quản lý Kinh doanh trở lên được phép thực hiện **Hoàn tác Chuyển đổi (Undo Lead Conversion)** với điều kiện Cơ hội bán hàng vừa tạo chưa có bất kỳ hoạt động thực tế nào (chưa có ghi chú, chưa chuyển giai đoạn bán hàng, chưa đính kèm tài liệu). Khi hoàn tác: (a) Cơ hội vừa tạo bị xóa mềm; (b) Doanh nghiệp vừa tạo bị xóa mềm nếu chưa có Contact nào khác liên kết; (c) Contact trở về **đúng giai đoạn trước khi chuyển đổi** (thông thường là `Lead`) — bước chuyển này là **ngoại lệ được ma trận cho phép** theo BR-12.9 và **không** yêu cầu lý do hạ hạng theo BR-12.7, nhưng bắt buộc nhập **lý do hoàn tác**; (d) Điểm tiềm năng và nguồn gốc UTM được giữ nguyên; (e) Hệ thống ghi nhật ký kiểm toán đầy đủ theo NFR-07.

---

### FEAT-31 — Phân bổ Khách hàng Tiềm năng Tự động (Lead Routing Engine) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khi Lead được tạo tự động từ các kênh kỹ thuật số (Website Form, API, Chatbot, Quảng cáo) mà không có "người tạo" trực tiếp, hệ thống tự động phân bổ người phụ trách (Owner) theo bộ quy tắc định sẵn.

**Actor:** Tiến trình Hệ thống, Quản lý Kinh doanh (cấu hình quy tắc), Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-31.1 (Phân bổ Round-robin)`: Khi không có quy tắc đặc biệt nào khớp, hệ thống phân bổ Lead theo vòng lần lượt (Round-robin) đều nhau cho tất cả thành viên Sales đang hoạt động trong nhóm.
- `BR-31.2 (Phân bổ theo Vùng địa lý)`: Nếu Lead có thông tin Quốc gia hoặc Tỉnh/Thành, ưu tiên phân bổ cho nhân viên quản lý vùng địa lý tương ứng (Territory-based).
- `BR-31.3 (Phân bổ theo Ngành nghề)`: Nếu Lead có thông tin Ngành nghề, ưu tiên phân bổ cho nhân viên chuyên ngành tương ứng.
- `BR-31.3b (Thứ tự Ưu tiên giữa các Quy tắc) [Yêu cầu mới]`: Khi một Lead khớp đồng thời nhiều quy tắc, hệ thống áp dụng theo thứ tự ưu tiên giảm dần: **(1)** Người phụ trách hiện hữu (BR-31.6 — ưu tiên tuyệt đối); **(2)** Vùng địa lý (BR-31.2); **(3)** Ngành nghề (BR-31.3); **(4)** Phân bổ vòng lần lượt (BR-31.1); **(5)** Hàng đợi Unassigned (BR-31.4). Thứ tự này là tham số cấu hình theo tenant (Phụ lục B, `CFG-31-02`) vì có tổ chức phân đội theo ngành trước, có tổ chức phân theo vùng trước.
- `BR-31.4 (Fallback khi không khớp)`: Khi không có quy tắc nào khớp hoặc không có Sales khả dụng, Lead được đưa vào hàng đợi "Unassigned" và gửi thông báo cho Quản lý Kinh doanh phân công thủ công.
- `BR-31.5 (Quyền cấu hình)`: Quy tắc phân bổ chỉ được tạo, sửa và xóa bởi Quản lý Kinh doanh, Quản trị viên Workspace và Chủ sở hữu Workspace — khớp đúng ma trận mục 5 dòng FEAT-31.
- `BR-31.6 (Ưu tiên Tuyệt đối cho Người phụ trách Hiện hữu — Chống trùng chủ) [Yêu cầu mới]`: **Trước khi** áp dụng bất kỳ quy tắc phân bổ nào tại BR-31.1 đến BR-31.4, hệ thống bắt buộc chạy kiểm tra trùng lặp (FEAT-17). Nếu Lead mới trùng khớp với một bản ghi đã tồn tại và bản ghi đó **đã có Người phụ trách đang hoạt động**, hệ thống:
  - **Không tạo bản ghi mới** và **không phân bổ lại** cho người khác — tương tác mới được ghi nhận vào Dòng thời gian của bản ghi hiện hữu;
  - Gửi thông báo cho Người phụ trách hiện hữu: "Khách hàng bạn đang phụ trách vừa phát sinh yêu cầu mới từ kênh [tên kênh]";
  - Nếu Người phụ trách hiện hữu đã nghỉ việc/bị vô hiệu hoá, Lead được phân bổ lại theo quy tắc thông thường và ghi chú rõ lý do chuyển giao.

  Quy tắc này bảo đảm không xảy ra tình trạng hai nhân viên cùng liên hệ một khách hàng — vấn đề nghiệp vụ đã nêu tại mục 2.1.

  **Tuy nhiên, để tránh biến "chống trùng chủ" thành hố đen mất doanh thu**, yêu cầu mới trên bản ghi đã có chủ bắt buộc sinh ra một **"Yêu cầu chờ xử lý"** có cam kết thời gian riêng, áp cùng bộ thời hạn tại BR-31.7 theo mức ưu tiên của bản ghi. Khi quá hạn: hệ thống leo thang lên Quản lý Kinh doanh, và **Quản lý được phép chỉ định người xử lý thay** (không đổi Người phụ trách chính, có thể dùng cơ chế Đội ngũ phụ trách tại FEAT-35). Người phụ trách đang ở trạng thái nghỉ phép hoặc đã tắt hoạt động được coi là "không khả dụng" cho mục đích quy tắc này, và yêu cầu được chuyển ngay cho người xử lý thay. Lý do: đây là nguồn nhu cầu chất lượng cao nhất (khách cũ quay lại hỏi mua thêm) — nếu không có thời hạn và không ai được phép nhận thay, yêu cầu sẽ nằm im vô thời hạn trong dòng thời gian.
- `BR-31.7 (Cam kết Thời gian Phản hồi & Thu hồi Lead bị bỏ quên) [Yêu cầu mới]`: Sau khi được phân bổ, Lead phải được liên hệ lần đầu — bằng một trong các bằng chứng được công nhận tại BR-31.8 — trong thời hạn cam kết mặc định:

| Mức ưu tiên Lead | Thời hạn phản hồi lần đầu | Hành động khi quá hạn |
| --- | --- | --- |
| **Ưu tiên cao (điểm ≥ Ngưỡng Ưu tiên cao, mặc định 85 — `CFG-15-01`)** | **1 giờ làm việc** | Nhắc nhở người phụ trách + thông báo Quản lý Kinh doanh |
| **Thông thường (Ngưỡng MQL ≤ điểm < Ngưỡng Ưu tiên cao; mặc định 40–84 — `CFG-15-01`)** | **4 giờ làm việc** | Nhắc nhở người phụ trách |
| **Thấp (điểm < Ngưỡng MQL; mặc định < 40 — `CFG-15-01`)** | **24 giờ làm việc** | Ghi nhận vào báo cáo tồn đọng |

  Ba dải điểm **luôn được tính lại từ hai ngưỡng tại `CFG-15-01`**, không đóng cứng con số: dải Thông thường là khoảng giữa Ngưỡng MQL và Ngưỡng Ưu tiên cao, nên khi tenant hiệu chỉnh ngưỡng theo vấn đề #4 mục 7, ba dải vẫn kề nhau và **không chồng lấn**. Nếu đóng cứng, một tenant hạ Ngưỡng Ưu tiên cao xuống 70 sẽ có Lead 84 điểm vừa thuộc dải 1 giờ vừa thuộc dải 4 giờ, và `KPI-03` mất căn cứ đo.

  Bằng chứng "đã liên hệ lần đầu" được quy định tại **BR-31.8**.

  Nếu Lead vẫn không được phản hồi sau **2 lần thời hạn** nêu trên, hệ thống tự động **thu hồi và phân bổ lại** cho thành viên khác trong nhóm theo BR-31.1, ghi nhận lý do "Quá hạn phản hồi" vào lịch sử và gửi thông báo cho Quản lý Kinh doanh. **Giới hạn tối đa 2 lần thu hồi tự động** cho mỗi Lead; đến lần thứ 3, Lead được đưa vào hàng đợi "Unassigned" để Quản lý Kinh doanh phân công thủ công và chịu trách nhiệm — tránh tình trạng Lead bị quay vòng vô hạn giữa các nhân viên mà không ai chịu trách nhiệm. Thời hạn được tính theo Lịch làm việc của không gian làm việc theo BR-31.7b. Các mốc thời hạn và số lần thu hồi là tham số cấu hình theo tenant (Phụ lục B, `CFG-31-01`). Chỉ số tuân thủ được theo dõi qua `KPI-03` tại mục 2.4.
- `BR-31.7b (Lịch làm việc dùng để tính thời hạn) [Yêu cầu mới]`: Mọi thời hạn tính bằng "giờ làm việc" hoặc "ngày làm việc" trong tài liệu này (BR-12.4b, BR-17.2c, BR-31.6, BR-31.7, BR-34.1b, BR-35.3b) được tính theo **Lịch làm việc của không gian làm việc**, gồm: múi giờ, các ngày làm việc trong tuần, giờ bắt đầu và kết thúc mỗi ngày, và danh mục ngày lễ theo từng năm. Lịch do Chủ sở hữu Workspace khai báo (Phụ lục B, `CFG-31-03`); nếu chưa khai báo, hệ thống dùng mặc định Thứ Hai–Thứ Sáu 08:00–17:30 theo múi giờ của không gian làm việc và không có ngày lễ. Không có định nghĩa này thì hai người kiểm thử sẽ tính ra hai thời điểm quá hạn khác nhau và cam kết thời gian không nghiệm thu được.
- `BR-31.8 (Bằng chứng "đã liên hệ lần đầu") [Yêu cầu mới]`: Chỉ các bằng chứng sau được công nhận:
  - **Nhóm 1 — bằng chứng hệ thống tự sinh (mặc định):** cuộc gọi có bản ghi thời lượng, email đã gửi đi từ hệ thống, tin nhắn đã gửi trên kênh đã tích hợp, hoặc cuộc hẹn đã được tạo với khách hàng. Các bằng chứng này do FEAT-36 sinh tự động (BR-36.5) nên không tạo khống được.
  - **Nhóm 2 — liên hệ ngoài hệ thống, có xác nhận của Quản lý:** gặp trực tiếp tại văn phòng hoặc hiện trường, khách chỉ trả lời qua kênh cá nhân của nhân viên, hoặc nhân viên gọi bằng máy bàn/số cá nhân. Nhân viên khai báo và **Quản lý Kinh doanh xác nhận**; mỗi nhân viên dùng tối đa **10 lần/tháng** (Phụ lục B, `CFG-31-04`), có ghi nhật ký, và được **thống kê riêng** trong `KPI-03` để nhìn được tỷ trọng.
  - **Không được công nhận:** ghi chú thủ công đơn thuần không kèm bằng chứng nhóm 1 hoặc nhóm 2. Nếu tính, nhân viên chỉ cần gõ "đã gọi, không bắt máy" là đạt cam kết và `KPI-03` sẽ đạt 95% trên giấy trong khi khách chưa hề được liên hệ.
  - **Ngoại lệ tắt cơ chế thu hồi:** nếu tenant **chưa tích hợp bất kỳ kênh nào** sinh được bằng chứng nhóm 1, cơ chế **thu hồi tự động tại BR-31.7 không được kích hoạt** — hệ thống chỉ nhắc nhở và ghi vào báo cáo tồn đọng. Lý do: lấy Lead khỏi người đang thực sự làm việc chỉ vì hệ thống không có cách nhìn thấy công việc đó sẽ khiến khách nhận cuộc gọi thứ hai từ cùng công ty, và nhân viên sẽ lách bằng cách gửi email rỗng cho mọi Lead mới để đóng dấu bằng chứng.

---

### FEAT-32 — Theo dõi Nguồn gốc Khách hàng Tiềm năng (UTM Source Tracking) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Tự động ghi nhận nguồn gốc của mỗi Lead/Contact (kênh quảng cáo, chiến dịch marketing, từ khóa tìm kiếm) để đo lường hiệu quả Marketing ROI và tối ưu ngân sách.

**Actor:** Tiến trình Hệ thống (tự động ghi nhận), Nhân viên Marketing (xem báo cáo), Quản lý Marketing (phân tích ROI).

**Quy tắc nghiệp vụ:**
- `BR-32.1 (Trường nguồn gốc hệ thống)`: Mỗi Contact/Lead tự động ghi nhận: **Kênh nguồn gốc chính** (chọn từ danh mục chuẩn A.7, gồm cả giá trị "Không xác định" dùng cho `KPI-08`) và **Chi tiết kênh con**.
- `BR-32.2 (Lưu tham số UTM)`: Khi Lead được tạo qua form web hoặc API, hệ thống tự động ghi nhận và lưu trữ toàn bộ tham số UTM: `utm_source`, `utm_medium`, `utm_campaign`, `utm_content`, `utm_term`.
- `BR-32.3 (Không ghi đè nguồn gốc)`: Kênh nguồn gốc và các tham số UTM được ghi nhận **một lần** tại thời điểm tạo bản ghi và **không được phép ghi đè** sau đó (nguyên tắc First-touch Attribution). Người dùng nghiệp vụ chỉ xem, không sửa.
- `BR-32.3b (Sửa sai nguồn gốc do lỗi kỹ thuật) [Yêu cầu mới]`: Ngoại lệ duy nhất của BR-32.3: khi có **bằng chứng lỗi hệ thống** làm ghi nhận sai nguồn gốc trên diện rộng (form web cấu hình sai tham số, một lô nhập khẩu ánh xạ lệch cột nguồn, tích hợp lỗi khiến hàng loạt bản ghi rơi vào "Không xác định"), **Quản trị viên hoặc Chủ sở hữu Workspace** được phép sửa nguồn gốc theo lô, với đủ 4 điều kiện: **(a)** bắt buộc nhập lý do và mô tả bằng chứng lỗi; **(b)** ghi nhật ký kiểm toán theo NFR-07; **(c)** **giữ nguyên giá trị gốc trong lịch sử bản ghi**, không xoá dấu vết; **(d)** báo cáo phân tích nguồn gốc và Revenue Attribution phải nêu rõ **số bản ghi đã được sửa nguồn** trong kỳ. Không có ngoại lệ này thì cách duy nhất để chữa số liệu sai là xóa và tạo lại bản ghi, làm mất luôn dòng thời gian, điểm tiềm năng và lịch sử giai đoạn của khách hàng — thiệt hại lớn hơn nhiều so với việc sai nguồn.
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
- `BR-15.3 (Giới hạn Trần Điểm & Tần suất) [Yêu cầu mới]`: Tổng điểm tiềm năng được chuẩn hoá trong khoảng từ 0 đến 100 điểm (`0 <= Lead Score <= 100`), tính bằng tổng Điểm Hồ sơ + Điểm Tương tác (đã áp dụng suy giảm theo FEAT-16 nếu có) rồi chặn trần tại 100. Điểm cộng tương tác cho mỗi loại hành vi (như mở email, nhấp link) được giới hạn tối đa 1 lần cộng điểm / ngày / loại hành vi cho mỗi khách hàng để chống gian lận điểm số.
- `BR-15.4 (Cấu hình Quy tắc Chấm điểm) [Yêu cầu mới]`: Các mức điểm liệt kê tại BR-15.1/BR-15.2 là giá trị mặc định có thể cấu hình theo từng không gian làm việc. Quản lý Marketing, Quản trị viên Workspace và Chủ sở hữu Workspace được phép chỉnh sửa trọng số điểm, thêm/xóa tiêu chí Profile Fit và Engagement qua màn hình Cấu hình Quy tắc Chấm điểm — khớp đúng ma trận mục 5 dòng FEAT-15. Nhân viên Marketing chỉ được xem cấu hình hiện hành, không được chỉnh sửa. Mọi thay đổi quy tắc được ghi nhật ký kiểm toán và áp dụng cho lượt tính điểm kế tiếp (không hồi tố điểm đã tính trước đó).
- `BR-15.5 (Ngưỡng điểm Thăng hạng Vòng đời) [Yêu cầu mới]`: Điểm tiềm năng gắn trực tiếp với giai đoạn vòng đời qua bộ ngưỡng mặc định sau:

| Ngưỡng | Tổng điểm tiềm năng | Hành vi hệ thống |
| --- | --- | --- |
| **Ngưỡng MQL** | **≥ 40 điểm tổng VÀ ≥ 15 điểm tương tác** | Contact đang ở `Subscriber`/`Lead`/`Nurturing` tự động thăng hạng lên `MQL`, gửi thông báo cho Marketing. Điều kiện kép là bắt buộc — xem BR-15.7 |
| **Ngưỡng SQL (Sales-Ready)** | **≥ 70 điểm** | Contact ở `MQL` được đánh dấu "Sẵn sàng chuyển Sales" và đưa vào hàng đợi thẩm định; **không** tự động lên `SQL` — bắt buộc có thẩm định của Sales (BR-15.6) |
| **Ngưỡng Ưu tiên cao (Hot)** | **≥ 85 điểm** | Gắn nhãn "Khách hàng nóng" trên danh sách, ưu tiên hiển thị đầu hàng đợi phân bổ |
| **Dưới ngưỡng MQL** | **< 40 điểm** | Không tự động thăng hạng; tiếp tục nuôi dưỡng qua chiến dịch định kỳ |

  Bộ ngưỡng này là tham số cấu hình theo tenant (Phụ lục B, `CFG-15-01`), do Quản lý Marketing và Quản trị viên Workspace chỉnh sửa (cùng phạm vi quyền tại BR-15.4). Việc thăng hạng tự động tuân thủ Ma trận Chuyển đổi Giai đoạn tại FEAT-12 — ma trận đã cho phép `Subscriber → MQL` và `Nurturing → MQL`.
- `BR-15.6 (Nguyên tắc Chuyển giao Marketing → Sales) [Yêu cầu mới]`: Hệ thống chỉ tự động thăng hạng tối đa đến `MQL`. Bước chuyển `MQL → SQL` bắt buộc do con người thực hiện (Sales thẩm định), nhằm bảo đảm nguyên tắc "Sales chỉ nhận lead mình đã đồng ý nhận", tránh tranh chấp trách nhiệm giữa Marketing và Sales. Lead đạt Ngưỡng SQL nhưng chưa được Sales thẩm định trong thời hạn cam kết sẽ được xử lý theo BR-31.7. *(Ngoại lệ: bước `→ Opportunity` do sự kiện Cơ hội bán hàng theo BR-12.9 không thuộc phạm vi hạn chế này vì phản ánh thực tế đã có Cơ hội.)*
- `BR-15.7 (Chống Thăng hạng Giả từ Điểm Hồ sơ) [Yêu cầu mới]`: Điểm Hồ sơ (BR-15.1) có thể đạt tối đa 55 điểm mà không cần bất kỳ tương tác nào, nên **điểm tổng đơn thuần không được dùng làm căn cứ thăng hạng**. Điều kiện thăng hạng `MQL` bắt buộc kèm **điểm tương tác tối thiểu 15 điểm** (tương đương ít nhất một hành vi thực của khách hàng). Bổ sung các chốt an toàn:
  - **(a) Hoãn thăng hạng cho dữ liệu nhập khẩu:** Bản ghi tạo bằng nhập khẩu hàng loạt không được thăng hạng tự động trong **24 giờ** đầu và không tính vào cam kết thời gian phản hồi tại BR-31.7 cho tới khi phát sinh tương tác đầu tiên. Mục đích: một lô nhập 10.000 danh bạ hội thảo không được phép sinh ra hàng nghìn `MQL` giả rồi làm sập hàng đợi thẩm định của đội kinh doanh.
  - **(b) Chống thông báo lặp:** Mỗi bản ghi chỉ phát thông báo "đạt Ngưỡng MQL" hoặc "Khách hàng nóng" tối đa **1 lần trong 30 ngày**, tránh nhiễu khi điểm dao động quanh ngưỡng do cơ chế suy giảm điểm (FEAT-16) rồi được cộng lại.
  - **(c) Độ trễ hạ ngưỡng:** Bản ghi đã đạt `MQL` không bị đánh giá lại ngưỡng trong 7 ngày kể từ lần thăng hạng, để tránh trạng thái nhảy qua lại.

  Các giá trị tại (a), (b), (c) là tham số cấu hình theo tenant (Phụ lục B, `CFG-15-02`).

---

### FEAT-16 — Cơ chế Suy giảm Điểm Tiềm năng theo Thời gian (Score Decay Engine) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khách hàng không có tương tác trong một khoảng thời gian sẽ bị tự động giảm điểm tiềm năng để phản ánh độ nguội của cơ hội.

**Actor:** Tiến trình Hệ thống (Daily Cron).

**Quy tắc nghiệp vụ:**
- `BR-16.1`: Tiến trình hệ thống chạy lúc 02:00 hằng ngày kiểm tra: Nếu khách hàng không có bất kỳ tương tác nào trong vòng **14 ngày**, điểm tương tác bị giảm **10%** so với điểm tương tác hiện có (làm tròn xuống số nguyên). Mốc 14 ngày chỉ bị trừ **1 lần duy nhất** cho tới khi chạm mốc 30 ngày, không trừ lặp lại mỗi ngày.
- `BR-16.2`: Nếu không có tương tác trong vòng **30 ngày**, điểm tương tác bị giảm tiếp **25%** so với điểm hiện có (làm tròn xuống số nguyên). Các mốc ngày và tỷ lệ suy giảm tại BR-16.1 và BR-16.2 là tham số cấu hình theo tenant (Phụ lục B, `CFG-16-01`).
- `BR-16.3`: Điểm tiềm năng không bao giờ nhận giá trị âm (sàn là 0 điểm).
- `BR-16.4 (Ảnh hưởng đến Giai đoạn Vòng đời) [Yêu cầu mới]`: Điểm suy giảm **không tự động hạ giai đoạn vòng đời** ở bất kỳ giai đoạn nào — đây là lệnh cấm áp cho toàn bộ 10 giai đoạn. Phần còn lại của quy tắc quy định **cơ chế đề xuất chuyển `Nurturing`**, và chỉ riêng phần cơ chế đề xuất này mới có phạm vi hẹp hơn. Quy tắc này **chỉ áp dụng cho bốn giai đoạn đầu phễu** (Subscriber, Lead, MQL, SQL), với hai loại trừ tường minh: **(i)** hồ sơ ở `Customer`/`Evangelist` **không** bị đưa vào danh sách đề xuất chuyển `Nurturing` vì nguyên tắc 4 tại FEAT-12 cấm tuyệt đối bước chuyển đó — với hai giai đoạn này, điểm nguội chỉ sinh cảnh báo cho Quản lý Khách hàng Hiện hữu để chủ động chăm sóc; **(ii)** hồ sơ ở `Opportunity` cũng **không** bị đưa vào danh sách này, vì `Opportunity` theo định nghĩa đang có ít nhất một Cơ hội bán hàng **mở** — điểm tương tác nguội trong lúc đang thương lượng là chuyện thường và không phải căn cứ để rút hồ sơ khỏi phễu. Đường duy nhất để một hồ sơ `Opportunity` sang `Nurturing` là **khi toàn bộ Cơ hội đã `Closed Lost`**, dùng danh mục **A.2** theo BR-12.3 — không dùng A.16; điểm nguội ở giai đoạn này chỉ sinh cảnh báo cho Người phụ trách. Khi điểm rơi xuống dưới Ngưỡng MQL (BR-15.5), hệ thống gắn cảnh báo "Đã nguội" trên hồ sơ và đưa vào danh sách đề xuất chuyển sang `Nurturing`. Việc chuyển sang `Nurturing` phải do Quản lý Kinh doanh quyết định, **bắt buộc chọn lý do từ danh mục A.16** (Lý do chuyển sang Nuôi dưỡng). Lưu ý: `Nurturing` là trạng thái ngoài phễu tuyến tính nên bước chuyển này **không** thuộc phạm vi BR-12.7 (vốn chỉ điều chỉnh bước lùi trên phễu tuyến tính và dùng danh mục A.3). Nguyên tắc chung: điểm số là công cụ ưu tiên hóa, không phải cơ chế tự động loại khách hàng.

---

## F. XỬ LÝ TRÙNG LẶP & GỘP BẢN GHI AN TOÀN (DEDUPLICATION, MERGE & UNMERGE)

### FEAT-17 — Tự động Nhận diện & Kiểm tra Trùng lặp Khách hàng (Duplicate Check) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động cảnh báo trùng lặp theo thời gian thực khi người dùng đang nhập thông tin hoặc cung cấp công cụ quét trùng lặp toàn hệ thống.

**Actor:** Mọi người dùng tạo/sửa Contact, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-17.1 (Tiêu chí trùng lặp & Mức độ tin cậy)`: Hệ thống phân biệt hai mức tiêu chí có mức độ tin cậy khác nhau:
  - **Tiêu chí chắc chắn (Strong match):** Trùng khớp chính xác Địa chỉ Email, hoặc Số điện thoại đã chuẩn hoá theo chuẩn quốc tế.
  - **Tiêu chí tham khảo (Weak match):** Trùng khớp Họ tên kết hợp Tên công ty. Tiêu chí này có tỷ lệ nhận diện sai cao với dữ liệu tiếng Việt (hai người khác nhau cùng tên tại một doanh nghiệp lớn; hoặc "Cty CP ABC" và "ABC Corp" là cùng một công ty nhưng không khớp chuỗi), vì vậy **tuyệt đối không được dùng làm căn cứ gộp tự động** và không bao giờ dùng để chặn tạo bản ghi.
- `BR-17.2 (Chính sách Xử lý khi Phát hiện Trùng lặp)`: Chính sách xử lý là tham số cấu hình theo tenant (Phụ lục B, `CFG-17-01`), với giá trị mặc định chuẩn hệ thống:
  - **Trùng theo Tiêu chí chắc chắn → Chặn tạo bản ghi mới (mặc định).** Hệ thống không cho lưu bản ghi trùng, thay vào đó điều hướng người dùng tới bản ghi hiện hữu để bổ sung thông tin/ghi nhận tương tác mới. Tenant có thể đổi sang "Chỉ cảnh báo, cho phép lưu" nếu nghiệp vụ đặc thù yêu cầu; khi đó bắt buộc ghi nhật ký người bỏ qua cảnh báo.
  - **Trùng theo Tiêu chí tham khảo → Chỉ cảnh báo mềm.** Hiển thị cảnh báo kèm danh sách bản ghi nghi trùng, người dùng vẫn được phép lưu.
  - **Lối ra hợp lệ khi thực sự là hai người khác nhau:** Ngay trên màn hình cảnh báo, người tạo được chọn **"Đây là người khác dùng chung định danh này"**. Hệ thống cho tạo bản ghi mới, **tự động gắn nhãn Định danh dùng chung** cho định danh đó theo BR-30.2, và ghi nhật ký người xác nhận. Không có lối ra này thì các tình huống hằng ngày — hai vợ chồng dùng chung email, hai người cùng số tổng đài/lễ tân, khách cá nhân dùng số của người thân — sẽ không tạo được bản ghi thứ hai, và nhân viên sẽ lách bằng cách thêm dấu chấm vào email hoặc bỏ trống email, làm `KPI-01` tệ hơn chính cái mà quy tắc này muốn bảo vệ. Lối ra này **không** đòi đổi chính sách của cả tenant.
- `BR-17.2b (Các nguồn thực tế sinh ra bản ghi trùng) [Yêu cầu mới]`: Việc chặn tại giao diện tạo mới **không** loại bỏ được bản ghi trùng khỏi hệ thống, vì bản ghi trùng còn phát sinh từ các nguồn sau — đây chính là lý do các tính năng Gộp bản ghi (FEAT-18, 19, 20) vẫn cần thiết:
  - **Nhập khẩu hàng loạt:** theo chiến lược người dùng chọn tại BR-23.3.
  - **Tích hợp qua giao diện lập trình và các kênh tự động:** áp dụng BR-31.6 (trả về bản ghi hiện hữu) nhưng dữ liệu từ nguồn ngoài có thể lệch định dạng nên không luôn khớp được.
  - **Dữ liệu lịch sử tạo trước khi áp dụng chính sách này**, hoặc trong giai đoạn tenant từng cấu hình "chỉ cảnh báo".
  - **Trùng theo Tiêu chí tham khảo** (tên + công ty) — vốn không bao giờ bị chặn.
  - **Bản ghi từng được đánh dấu Định danh dùng chung** nhưng sau đó xác định lại là cùng một người.
- `BR-17.2c (Cam kết thời gian cho Yêu cầu quyền truy cập) [Yêu cầu mới]`: **Ba hành động** tại BR-17.3 dùng cùng bộ tham số thời hạn với BR-31.7 (mặc định **4 giờ làm việc**, Phụ lục B `CFG-31-01`), nhưng **hành vi khi quá hạn khác nhau theo từng loại**: **(i) Yêu cầu quyền truy cập** — quá hạn thì leo thang lên Quản lý Kinh doanh của Người phụ trách; nếu Quản lý cũng không xử lý trong thời hạn thứ hai, hệ thống **tự cấp quyền đọc tạm có ghi nhật ký** theo đúng cơ chế BR-35.4. Quyền tự cấp này **có thời hạn 7 ngày** kể từ lúc cấp, hoặc hết hiệu lực sớm hơn khi Người phụ trách/Quản lý xử lý bản ghi yêu cầu — tuỳ điều kiện nào đến trước; hết hạn mà yêu cầu vẫn chưa được xử lý thì bản ghi yêu cầu đóng với lý do "Không được xử lý" và người yêu cầu phải tạo yêu cầu mới. Không có mốc hết hiệu lực này thì một quyền cấp vì im lặng sẽ tồn tại vĩnh viễn, trong khi BR-35.4d luôn cho quyền đọc tạm một sự kiện đóng. **(ii) Đề nghị chuyển giao** — chỉ leo thang lên Quản lý Kinh doanh, **không có** hành vi tự động nào, vì chuyển giao quyền phụ trách luôn cần quyết định của con người theo FEAT-34. **(iii) Đề nghị gộp** — leo thang lên Quản trị Chất lượng Dữ liệu hoặc Quản trị viên, **không có** hành vi tự động nào, vì gộp bản ghi tác động không hoàn tác dễ dàng lên đồng thuận và giai đoạn vòng đời của cả hai bản ghi (BR-19.6, BR-19.8). Lý do: nếu yêu cầu này không có thời hạn và không ai buộc phải trả lời, nhân viên đang có khách trước mặt sẽ bỏ dùng và quay về cách lách nêu tại BR-17.2.
- `BR-17.3 (Hiển thị Bản ghi ngoài Phạm vi Dữ liệu) [Yêu cầu mới]`: Quy tắc này áp dụng cho **cả hai** tình huống: (i) bản ghi trùng được cảnh báo khi tạo/sửa nhưng nằm ngoài phạm vi dữ liệu của người dùng; và (ii) người dùng mở trực tiếp một bản ghi ngoài phạm vi dữ liệu của mình (kể cả khi vừa mất quyền đọc tạm theo BR-35.4d). Trong cả hai tình huống, hệ thống **không** được hiển thị "không có quyền truy cập" mà phải hiển thị **thông tin tối thiểu để nhận diện**: tên khách hàng dạng viết tắt, tên Người phụ trách, Đơn vị tổ chức phụ trách, và thời điểm tương tác gần nhất. Kèm theo là **ba hành động**: **"Yêu cầu quyền truy cập"**, **"Đề nghị chuyển giao"** và **"Đề nghị gộp"** — cả ba tạo ra một bản ghi yêu cầu có vòng đời và cam kết thời gian theo BR-35.3b, với **người xử lý khác nhau theo loại yêu cầu**: hai loại đầu gửi tới **Người phụ trách hiện hữu và Quản lý Kinh doanh của họ**; **"Đề nghị gộp"** gửi tới **Quản trị Chất lượng Dữ liệu hoặc Quản trị viên** — Người phụ trách hiện hữu chỉ nhận thông báo để biết, không phải để duyệt, vì gộp tác động lên đồng thuận và giai đoạn vòng đời của cả hai bản ghi nên vượt thẩm quyền của một người phụ trách.

  Hành động **"Đề nghị gộp"** là bắt buộc phải có vì người **duy nhất** nhìn thấy trùng lặp trong vận hành thực tế là nhân viên đang làm việc với khách, nhưng quyền thực thi gộp đòi quyền `delete` mà Nhân viên Kinh doanh không có (ma trận FEAT-18, FEAT-19). Đề nghị gộp được chuyển tới **Quản trị Chất lượng Dữ liệu hoặc Quản trị viên** để thực thi theo FEAT-18/FEAT-19. Không có hành động này, toàn bộ hàng đợi dọn trùng dồn về Quản trị viên trong khi người phát hiện không có cách báo, và `KPI-01` dưới 2% là không khả thi với khối dữ liệu lịch sử nêu tại BR-17.2b. Lý do nghiệp vụ: nếu nhân viên nhận cảnh báo trùng mà không biết là ai và không có đường xin quyền, cách lách phổ biến nhất là thêm dấu chấm vào email hoặc bỏ trống email để lưu cho xong — làm tỷ lệ trùng lặp tăng và `KPI-01` không thể đạt.
- `BR-17.4 (Định nghĩa đo lường Tỷ lệ trùng lặp) [Yêu cầu mới]`: Phục vụ nghiệm thu `KPI-01`, tỷ lệ trùng lặp được tính bằng **số bản ghi dư thừa** chia cho **tổng số bản ghi đang hoạt động** — hai đại lượng cùng đơn vị là *bản ghi*, không phải *cặp*. Cách xác định số bản ghi dư thừa: nhóm các bản ghi đang hoạt động (chưa xóa mềm) khớp nhau theo Tiêu chí chắc chắn thành từng cụm; mỗi cụm gồm *n* bản ghi đóng góp *n − 1* bản ghi dư thừa. Loại trừ khỏi phép đo: các bản ghi đã được đánh dấu **Định danh dùng chung** (BR-30.2) vì đây là các cá nhân khác nhau hợp lệ dùng chung định danh, và các Hồ sơ Khách hàng Tạm (BR-01.1b) vì chưa có định danh để so khớp.

---

### FEAT-18 — Xem trước Tác động Gộp Bản ghi (Merge Preview & Impact Analysis) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Trước khi thực hiện gộp hai bản ghi, hệ thống cung cấp màn hình Xem trước (Preview Merge) hiển thị rõ ràng giá trị nào sẽ được giữ lại, giá trị nào bị ghi đè và số lượng bản ghi con (Deals, Tickets, Tasks, Notes) sẽ được chuyển giao.

**Actor:** Quản trị viên Workspace, Người có quyền `delete` trên Contacts/Accounts.

**Quy tắc nghiệp vụ:**
- `BR-18.1`: Gọi API `/api/v1/contacts/:id/merge-preview?targetId=...` để tính toán chính xác bảng so sánh hai cột của Bản ghi A và Bản ghi B.
- `BR-18.2 (Quyền chọn trường và các trường bị cưỡng chế)`: Cho phép người dùng chủ động chọn từng trường dữ liệu muốn giữ lại từ bản ghi A hay bản ghi B trước khi bấm gộp — **trừ các trường sau, hệ thống tự quyết định và khoá lựa chọn thủ công**, hiển thị rõ lý do ngay tại màn hình Xem trước:

| Trường bị cưỡng chế | Quy tắc áp dụng | Quy tắc nguồn |
| --- | --- | --- |
| Trạng thái đồng thuận từng kênh | Trạng thái nghiêm ngặt nhất thắng (`OPT_OUT` thắng `OPT_IN`) | BR-19.6 |
| Trạng thái khả năng tiếp cận | Thứ tự thắng cho **cùng một địa chỉ**: `BOUNCED` > `OBSOLETE` > `UNVERIFIED` > `VERIFIED`. `BOUNCED` thắng tất cả (trạng thái kỹ thuật, không đảo được). `OBSOLETE` thắng `VERIFIED`/`UNVERIFIED` vì nó ghi nhận một sự kiện nghiệp vụ đã xảy ra (khách đã rời doanh nghiệp sở hữu địa chỉ) và **vẫn đảo lại được** thủ công theo BR-10.4a, nên giữ nó không gây mất mát như giữ `BOUNCED` sai | BR-19.6 (nhánh `BOUNCED`), BR-10.4 (nhánh `OBSOLETE`) |
| Nhãn Định danh dùng chung | Có ở bất kỳ bản ghi nào thì được giữ | BR-19.6 |
| Bằng chứng đồng thuận | Giữ đầy đủ của **cả hai** bản ghi, không ghi đè | BR-19.6 |
| Giai đoạn vòng đời | Giai đoạn tiến xa nhất thắng | BR-19.8 |
| Nguồn gốc và tham số UTM | Giữ của Bản ghi Chính (First-touch), lưu của bản ghi phụ vào sổ cái | BR-19.5 |
| Điểm tiềm năng | Lấy giá trị cao hơn, không cộng dồn | BR-19.10 |
| Thẻ phân loại | Hợp nhất toàn bộ | BR-19.10 |
| Vai trò liên hệ trên Cơ hội | Vai trò có thứ bậc ưu tiên cao nhất | BR-19.4 |

  Người thực hiện **vẫn được chỉ định lại** Doanh nghiệp chính (BR-19.7) và Người phụ trách (BR-19.9) tại bước Xem trước — hai trường này không bị khoá.

---

### FEAT-19 — Gộp Khách hàng Cá nhân & Doanh nghiệp An toàn (Merge Contacts/Accounts) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Thực thi giao dịch gộp hai bản ghi trùng lặp: Giữ lại Bản ghi Chính (Master Record), chuyển toàn bộ bản ghi con sang Bản ghi Chính, xóa mềm Bản ghi Phụ (Losing Record) và ghi nhận Sổ cái Hoàn tác.

**Actor:** Quản trị viên Workspace, Người có quyền `delete` trên Contacts/Accounts.

**Quy tắc nghiệp vụ:**
- `BR-19.1 (Quyền hạn bắt buộc)`: Thao tác Gộp bắt buộc yêu cầu quyền `delete` (bởi vì bản ghi phụ sẽ bị xóa sau khi gộp).
- `BR-19.2 (Chuyển giao toàn bộ dữ liệu liên quan)`: Toàn bộ Notes, Tasks, Tickets, Deals, Lịch sử Hội thoại và Mối quan hệ của bản ghi phụ được tự động tái liên kết (re-parented) sang Bản ghi Chính.
- `BR-19.3 (Ghi nhận Sổ cái Gộp)`: Tạo bản ghi sổ cái trong `contact_merges` ghi nhận chi tiết: `masterContactId`, `mergedContactId`, ảnh chụp dữ liệu gốc (snapshot) của bản ghi phụ và người thực hiện gộp.
- `BR-19.4 (Xử lý Xung đột Vai trò Liên hệ trên Deal) [Yêu cầu mới]`: Khi gộp hai Contact mà cả hai đều đang tham gia vào cùng một Deal với các vai trò mua hàng khác nhau (Contact Roles on Deals), hệ thống giữ lại vai trò có thứ bậc ưu tiên cao nhất theo chuẩn: `Decision Maker` > `Champion` > `Technical Evaluator` > `Influencer` > `Purchaser`.
- `BR-19.5 (Bảo toàn Nguồn gốc Attribution khi Gộp) [Yêu cầu mới]`: Trường `leadSource` và tham số UTM (theo BR-32.1/BR-32.2) của Bản ghi Chính (Master) luôn được giữ nguyên theo nguyên tắc First-touch Attribution, **không bị ghi đè** bởi dữ liệu của Bản ghi Phụ. Dữ liệu `leadSource`/UTM gốc của Bản ghi Phụ được lưu đầy đủ trong snapshot của Sổ cái Gộp (`contact_merges`, xem BR-19.3) để phục vụ đối chiếu báo cáo Revenue Attribution đa nguồn, dù không hiển thị trên hồ sơ 360 của Bản ghi Chính sau khi gộp.
- `BR-19.6 (Nguyên tắc Trạng thái Nghiêm ngặt nhất cho Đồng thuận Tiếp thị) [Yêu cầu mới — Bắt buộc tuân thủ pháp lý]`: Khi gộp hai bản ghi có trạng thái đồng thuận đối lập trên cùng một kênh (một bên `OPT_IN`, một bên `OPT_OUT`), Bản ghi Chính sau khi gộp **bắt buộc nhận trạng thái nghiêm ngặt hơn là `OPT_OUT`**, bất kể bản ghi nào được chọn làm Master và bất kể người dùng chọn giữ trường nào ở bước Xem trước Gộp (FEAT-18). Quy tắc này **không thể bị ghi đè thủ công** vì việc gửi tin cho người đã từ chối là vi phạm cam kết đồng thuận. Tương tự:
  - Trạng thái kênh liên lạc `BOUNCED` (FEAT-29) luôn thắng trạng thái `VERIFIED`/`UNVERIFIED` cho cùng một địa chỉ.
  - Nhãn "Định danh dùng chung" (BR-30.2) nếu tồn tại ở bất kỳ bản ghi nào thì được giữ lại sau khi gộp.
  - Bằng chứng đồng thuận (BR-30.3) của **cả hai** bản ghi được giữ lại đầy đủ, không ghi đè, để chứng minh cơ sở xử lý dữ liệu khi bị khiếu nại.
  - Nếu **bất kỳ bản ghi nào trong cặp gộp** — Bản ghi Chính hoặc bản ghi phụ — đang có yêu cầu thực hiện Quyền Chủ thể Dữ liệu chưa hoàn tất (FEAT-33), thao tác gộp bị **chặn** cho đến khi yêu cầu đó được xử lý xong.
- `BR-19.7 (Xử lý Xung đột Liên kết Doanh nghiệp khi Gộp) [Yêu cầu mới]`: Khi gộp hai Contact có liên kết doanh nghiệp:
  - **Trùng cùng một Doanh nghiệp với chức danh khác nhau:** Hệ thống giữ **một** liên kết duy nhất, ưu tiên chức danh của liên kết có ngày bắt đầu **muộn hơn** (phản ánh vị trí hiện tại), và lưu chức danh cũ vào lịch sử liên kết để không mất dữ liệu.
  - **Cả hai đều có Doanh nghiệp chính nhưng khác nhau:** Doanh nghiệp chính của **Bản ghi Chính (Master)** được giữ làm chính; liên kết của bản ghi phụ được chuyển thành liên kết phụ. Người thực hiện gộp được phép chỉ định lại Doanh nghiệp chính ngay tại bước Xem trước Gộp.
  - **Liên kết đã kết thúc (Đã nghỉ việc):** Được chuyển giao nguyên trạng, không tự động kích hoạt lại.
- `BR-19.8 (Nguyên tắc Giai đoạn Tiến xa nhất) [Yêu cầu mới — sàn bắt buộc]`: Sau khi gộp, Bản ghi Chính **luôn nhận giai đoạn vòng đời tiến xa nhất** của hai bản ghi, bất kể bản ghi nào được chọn làm Master và bất kể người dùng chọn giữ trường nào ở bước Xem trước Gộp (FEAT-18). Quy tắc này **không thể bị ghi đè thủ công**, cùng cơ chế cưỡng chế như BR-19.6, và thuộc ngoại lệ (ii) của ma trận nêu tại BR-12.6. Lý do: nếu Bản ghi Chính là hồ sơ `Lead` và bản ghi phụ là `Customer` (xem các nguồn sinh bản ghi trùng tại BR-17.2b), việc giữ `Lead` sẽ đẩy một khách hàng đang trả tiền trở lại chiến dịch marketing tiềm năng và làm sai báo cáo doanh thu.

  **Thứ tự so sánh khi có trạng thái ngoài phễu tuyến tính** (Nurturing, Churned, Disqualified — vốn không nằm trên trục tiến/lùi nên không so sánh trực tiếp được):
  - Nếu **một** bản ghi ở giai đoạn phễu tuyến tính và bản ghi kia ở trạng thái đặc biệt: lấy **giai đoạn phễu tuyến tính**, trừ khi trạng thái đặc biệt là `Churned` — khi đó lấy `Churned` và thông báo cho người phụ trách rà soát, vì `Churned` là thông tin có giá trị hơn về thực trạng quan hệ.
  - Nếu **cả hai** ở trạng thái đặc biệt: thứ tự ưu tiên `Churned` > `Nurturing` > `Disqualified`.
  - `Disqualified` **không bao giờ** được giữ nếu bản ghi kia ở bất kỳ giai đoạn phễu tuyến tính nào hoặc ở `Churned`/`Nurturing` — vì một bản ghi từng bị loại không được phép làm mất trạng thái đang hoạt động của bản ghi kia. Lý do loại được lưu vào lịch sử để rà soát.
- `BR-19.9 (Quyền sở hữu sau khi Gộp) [Yêu cầu mới]`: Khi hai bản ghi thuộc hai Người phụ trách khác nhau, Bản ghi Chính sau gộp mặc định giữ **Người phụ trách của bản ghi có tương tác gần nhất** (tham số cấu hình theo tenant, Phụ lục B `CFG-19-01`; các lựa chọn khác: giữ người phụ trách của Bản ghi Chính, hoặc bắt buộc chỉ định thủ công). Người thực hiện gộp được phép chỉ định lại ngay tại bước Xem trước. Hệ thống **bắt buộc gửi thông báo cho cả hai Người phụ trách** và ghi nhật ký kiểm toán, vì người mất bản ghi cũng mất luôn quyền truy cập theo BR-01.4 và điều này ảnh hưởng trực tiếp tới ghi nhận thành tích/hoa hồng.
- `BR-19.10 (Hợp nhất Điểm tiềm năng, Thẻ và Danh tính) [Yêu cầu mới]`: Điểm tiềm năng sau gộp lấy **giá trị cao hơn** của hai bản ghi (không cộng dồn, để tránh thổi điểm bằng cách tạo bản ghi trùng); Thẻ phân loại được **hợp nhất toàn bộ** (loại trùng); toàn bộ kênh danh tính hợp lệ của cả hai bản ghi được giữ lại thành danh sách định danh của Bản ghi Chính, người dùng chỉ định một định danh chính cho mỗi loại kênh.

  **Xử lý khi kết quả hợp nhất vượt hạn mức gói dịch vụ (NFR-11):** Thao tác gộp **không bị chặn** vì lý do vượt hạn mức — dữ liệu khách hàng không được phép mất chỉ vì giới hạn thương mại. Thay vào đó: bản ghi sau gộp được phép vượt hạn mức và gắn cờ "Vượt hạn mức do gộp"; hệ thống thông báo cho Chủ sở hữu Workspace kèm đề nghị rà soát hoặc nâng gói; và bản ghi này **không** được thêm mới thẻ/liên kết cho tới khi trở về trong hạn mức. Quy tắc này áp dụng cho cả hợp nhất liên kết doanh nghiệp tại BR-19.7.

---

### FEAT-20 — Sổ cái Hoàn tác Gộp Bản ghi (Unmerge Contacts/Accounts Ledger) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép khôi phục lại trạng thái ban đầu trước khi gộp nếu phát hiện thao tác gộp nhầm lẫn.

**Actor:** Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-20.1`: Người dùng truy cập Lịch sử gộp bản ghi (`GET /api/v1/contacts/:id/merge-history`), bấm nút **"Hoàn tác gộp" (Unmerge)** (`POST /api/v1/contacts/merges/:mergeId/unmerge`).
- `BR-20.2`: Hệ thống hồi sinh bản ghi phụ đã bị xóa mềm, trả lại các trường dữ liệu theo snapshot trong sổ cái và hoàn trả lại các bản ghi con nguyên thủy về đúng chủ sở hữu cũ.
- `BR-20.3 (Thời hạn Hoàn tác gộp & Bảo vệ khỏi Dọn dẹp) [Yêu cầu mới]`: Quyền Hoàn tác gộp có hiệu lực trong **90 ngày** kể từ thời điểm gộp (tham số cấu hình theo tenant, Phụ lục B `CFG-20-01`). Trong suốt thời hạn này, bản ghi phụ đã bị xóa mềm do gộp **được loại trừ khỏi tiến trình dọn dẹp vĩnh viễn** của BR-05.4 (xem BR-05.6b). Quá thời hạn, nút Hoàn tác gộp bị ẩn và bản ghi phụ mới được đưa vào diện dọn dẹp; sổ cái gộp vẫn được lưu vĩnh viễn theo NFR-05 để phục vụ tra soát lịch sử. Không có quy tắc này, một thao tác gộp sai phát hiện ở tháng thứ hai sẽ nhận về bản ghi rỗng, trong khi Hoàn tác gộp được thiết kế như cơ chế an toàn cốt lõi của phân hệ.

---

### FEAT-21 — Khôi phục Tự động Giao dịch Gộp Bị Lỗi (Recover Failed Merge) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Công cụ hỗ trợ xử lý sự cố khi một giao dịch gộp bị gián đoạn giữa chừng do mất kết nối mạng hoặc lỗi cơ sở dữ liệu.

**Actor:** Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-21.1`: Cung cấp API `/api/v1/contacts/merges/:mergeId/recover` cho phép hoàn tất nốt các bước chuyển giao bản ghi con còn dang dở hoặc tự động rollback về trạng thái an toàn.

---

## G. NHẬP DỮ LIỆU THÔNG MINH QUA HÀNG ĐỢI (SMART BULK IMPORT)

### FEAT-22 — Tải lên & Tiếp nhận Tệp Nhập khẩu Excel/CSV Dung lượng lớn (mặc định 50MB) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hỗ trợ tải lên tệp danh bạ Excel (.xlsx) hoặc CSV dung lượng tối đa **50 MB** để nhập khẩu hàng loạt vào hệ thống.

**Actor:** Quản trị viên Workspace, Người có quyền `import` trên Contacts/Accounts.

**Quy tắc nghiệp vụ:**
- `BR-22.1`: Tệp tải lên được tiếp nhận và xử lý bất đồng bộ qua hàng đợi tác vụ; người dùng không phải chờ trên giao diện và tiến trình nhập không làm ảnh hưởng tới hiệu năng chung của hệ thống.
- `BR-22.2`: Giới hạn dung lượng tệp tối đa **50MB** cho mỗi lần tải lên (tham số cấu hình theo tenant, Phụ lục B `CFG-22-02`). Tệp vượt giới hạn bị từ chối ngay tại bước tải lên kèm thông báo hướng dẫn chia nhỏ tệp.

---

### FEAT-23 — Trợ lý Tự động Ánh xạ Cột Dữ liệu (Auto Field Mapping Wizard) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Giao diện trực quan tự động quét các tiêu đề cột trong tệp Excel/CSV và đề xuất ánh xạ chính xác với các trường dữ liệu tương ứng trong CRM (Họ tên, SĐT, Email, Tên công ty, Chức danh, Địa chỉ).

**Actor:** Người dùng thực hiện Nhập dữ liệu.

**Quy tắc nghiệp vụ:**
- `BR-23.1`: Tự động nhận diện các tiêu đề cột phổ biến bằng cả tiếng Việt, tiếng Ả Rập và tiếng Anh (ví dụ: "Số điện thoại", "Phone", "Mobile", "Email", "Họ tên", "Full Name").
- `BR-23.2`: Cho phép người dùng chỉnh sửa ánh xạ thủ công hoặc bỏ qua các cột không cần nhập.
- `BR-23.3`: Cho phép chọn chiến lược xử lý trùng lặp cho từng lô nhập: **Bỏ qua bản ghi trùng** (mặc định) hoặc **Cập nhật đè dữ liệu mới vào bản ghi cũ**. Lựa chọn **"Tạo bản ghi mới dù trùng"** chỉ khả dụng khi người thực hiện xác nhận lô dữ liệu thuộc trường hợp Định danh dùng chung (theo lối ra tại BR-17.2) — khi đó các bản ghi tạo ra được tự động gắn nhãn Định danh dùng chung và ghi nhật ký. Quy tắc này bảo đảm kênh nhập khẩu **không** trở thành đường đi vòng qua chính sách chặn trùng tại BR-17.2.
- `BR-23.4 (Lookup Key bắt buộc khi Cập nhật) [Yêu cầu mới]`: Khi người dùng chọn chiến lược "Cập nhật đè dữ liệu cũ", hệ thống **bắt buộc** người dùng phải chọn trường định danh (Lookup Key) để tìm bản ghi cũ. Các tùy chọn Lookup Key: (a) **Contact ID nội bộ** (ưu tiên cao nhất, chính xác nhất); (b) **Địa chỉ Email**; (c) **Số điện thoại**. Nếu không có dòng nào khớp Lookup Key, hệ thống xử lý dòng đó như "Tạo mới" và đánh dấu cảnh báo trong báo cáo kết quả.

---

### FEAT-24 — Xử lý Nhập khẩu Hàng đợi & Xuất Báo cáo Lỗi Chi tiết (Import Error Report) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tiến trình worker xử lý tệp theo từng khối (chunking), kiểm tra tính hợp lệ từng dòng, nhập dữ liệu hợp lệ và xuất tệp báo cáo chi tiết các dòng bị lỗi để người dùng sửa đổi.

**Actor:** Tiến trình Hệ thống, Người dùng thực hiện Nhập dữ liệu.

**Quy tắc nghiệp vụ:**
- `BR-24.1 (Theo dõi tiến trình)`: Cung cấp API theo dõi trạng thái tiến trình thời gian thực (`GET /api/v1/contacts/import-status/:jobId`) hiển thị số dòng đã xử lý, số dòng thành công, số dòng lỗi.
- `BR-24.2 (Tệp báo cáo lỗi)`: Nếu có dòng bị lỗi, hệ thống tạo tệp báo cáo lỗi kèm cột nguyên nhân cụ thể và cấp mã tải về an toàn để người dùng sửa chữa. Các nguyên nhân lỗi hợp lệ gồm: sai định dạng email; sai định dạng số điện thoại không chuẩn hoá được; **thiếu cả email lẫn số điện thoại** (vi phạm BR-01.1 — lưu ý Họ tên **không** phải trường bắt buộc nên thiếu tên không bị coi là lỗi, hệ thống dùng tên fallback theo BR-01.1); bước chuyển giai đoạn không hợp lệ (BR-12.6); thiếu Lookup Key khi chọn chiến lược cập nhật (BR-23.4); trùng lặp bị chặn theo chính sách tại BR-17.2.

---

## H. XUẤT DỮ LIỆU & DANH SÁCH HIỂN THỊ TÙY CHỈNH (EXPORT & LIST VIEWS)

### FEAT-25 — Xuất Dữ liệu Khách hàng Dạng Luồng An toàn qua Token (Streaming Export) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép xuất danh sách khách hàng ra tệp CSV theo các bộ lọc tùy chỉnh với cơ chế truyền luồng (Streaming) chống nghẽn bộ nhớ và bảo vệ tải về bằng token an toàn dùng 1 lần.

**Actor:** Quản trị viên Workspace, Người có quyền `export` trên Contacts/Accounts.

**Quy tắc nghiệp vụ:**
- `BR-25.1`: Xuất dữ liệu qua hàng đợi bất đồng bộ, hỗ trợ xuất hàng chục nghìn bản ghi mà không làm chậm hệ thống.
- `BR-25.2`: Đường dẫn tải về được bảo vệ bằng mã token bảo mật có thời hạn **24 giờ**, chỉ dùng được một lần, và tên tệp được mã hoá an toàn để chống tấn công chèn tiêu đề. Cùng thời hạn 24 giờ áp dụng cho mã tải tệp báo cáo lỗi nhập khẩu tại BR-24.2.
- `BR-25.3 (Nhật ký Xuất dữ liệu) [Yêu cầu mới]`: Mỗi lần xuất dữ liệu bắt buộc ghi lại: người xuất, thời điểm, bộ lọc đã dùng, **danh sách trường được xuất** và **danh sách mã bản ghi được xuất**. Nhật ký này là căn cứ chính để trả lời câu hỏi "dữ liệu của khách hàng đã ra ngoài những đâu" khi thực thi quyền chủ thể dữ liệu (BR-33.8), và để rà soát khi có nghi vấn lấy dữ liệu hàng loạt. Nhật ký xuất dữ liệu chịu cùng chế độ kiểm soát truy cập như nhật ký kiểm toán (NFR-14).
- `BR-25.4 (Ngưỡng Xuất lớn cần phê duyệt) [Yêu cầu mới]`: Một lần xuất bắt buộc được phê duyệt **trước khi tệp được tạo** khi thoả bất kỳ điều kiện nào dưới đây. Điều kiện khác nhau theo vai trò, vì trần cứng của mỗi vai trò khác nhau:
  - **Với các vai trò có quyền `export` đầy đủ** (Quản lý Kinh doanh, Nhân viên và Quản lý Marketing, Quản trị viên, Chủ sở hữu): lần xuất **vượt 5.000 bản ghi** (Phụ lục B, `CFG-25-01`) cần phê duyệt trước.
  - **Trường thuộc nhóm Định danh KYC — giới hạn theo mục đích, không phải theo hạn mức:** chỉ **Quản trị viên và Chủ sở hữu Workspace** xuất được, và mỗi lần xuất bắt buộc có **Người phụ trách Bảo vệ Dữ liệu đồng phê duyệt** (hoặc quy tắc thay thế người thứ hai tại NFR-14) kèm khai báo mục đích đúng với mục đích đã đăng ký tại BR-01.5b. **Nhân viên Kinh doanh, Quản lý Kinh doanh, Nhân viên Marketing và Quản lý Marketing không xuất được nhóm trường này trong bất kỳ trường hợp nào** — không có đường phê duyệt nào mở nó ra. Lý do: BR-01.5b khoá mục đích của nhóm trường này và cấm tuyệt đối dùng cho tiếp thị, phân khúc, chấm điểm hay báo cáo; nếu mở đường phê duyệt cho chức năng Marketing thì chính giới hạn mục đích đó bị vô hiệu ngay tại điểm dữ liệu rời khỏi hệ thống.
  - **Với Nhân viên Kinh doanh:** ngưỡng phê duyệt là **giá trị nhỏ hơn** giữa `CFG-25-01` (ngưỡng xuất lớn) và `CFG-25-02` (hạn mức ngày) — phát biểu như vậy để kết luận không phụ thuộc vào việc tenant đặt hai tham số ở mức nào. Trường Định danh KYC thì bị BR-25.5 chặn cứng, không có đường phê duyệt nào mở được. Cụ thể, lần xuất **làm vượt ngưỡng nhỏ hơn trong hai giá trị đó**: Quản lý Kinh doanh phê duyệt từng lần, và một lần phê duyệt **không được nâng tổng lượng xuất trong ngày lên quá hai lần hạn mức**. Không có đường nào để Nhân viên Kinh doanh xuất trường Định danh KYC, kể cả qua phê duyệt.

  **Người phê duyệt là quản lý trực tiếp theo tuyến báo cáo của người xuất** — Quản lý Kinh doanh phê duyệt cho Nhân viên Kinh doanh, Quản lý Marketing phê duyệt cho Nhân viên Marketing, Chủ sở hữu phê duyệt cho Quản trị viên và các vai trò quản lý. **Với chính Chủ sở hữu Workspace** — vai trò không có quản lý cấp trên — người phê duyệt thứ hai là **Người phụ trách Bảo vệ Dữ liệu**; nếu tenant chưa chỉ định DPO thì áp đúng quy tắc thay thế người phê duyệt thứ hai tại NFR-14. Không có vai trò nào tự phê duyệt lần xuất của chính mình: một lần xuất chạm ngưỡng luôn có **hai người khác nhau** đứng tên, vì đây là thao tác đưa dữ liệu cá nhân ra khỏi hệ thống và là căn cứ duy nhất trả lời câu hỏi dữ liệu khách đã ra ngoài những đâu (BR-25.3, BR-33.8). Lý do quy định theo tuyến: buộc đội kinh doanh xin phê duyệt của bên Marketing là sai tuyến báo cáo và trong thực tế sẽ bị bỏ qua. Đây là con số cụ thể hoá cho quyền "duyệt xuất lớn" nêu trong ma trận mục 5, để QA có ngưỡng nghiệm thu.
- `BR-25.5 (Quyền xuất dữ liệu của Nhân viên Kinh doanh) [Yêu cầu mới]`: Nhân viên Kinh doanh được xuất dữ liệu **trong phạm vi dữ liệu được gán**, với ba ràng buộc: **(a)** hạn mức mặc định **2.000 bản ghi/người/ngày** (Phụ lục B, `CFG-25-02`) — chỉ vượt được khi có phê duyệt của Quản lý Kinh doanh theo BR-25.4 và không quá hai lần hạn mức trong một ngày; **(b)** **không** được xuất trường thuộc nhóm Định danh KYC ở bất kỳ trường hợp nào; **(c)** mọi lần xuất ghi nhật ký theo BR-25.3. Lý do bắt buộc có quyền này: nhu cầu hằng ngày (in danh sách khách để đi gặp, chuẩn bị danh sách mời hội thảo, đối chiếu danh bạ) nếu không có đường hợp lệ thì cách làm thật sẽ là bôi đen bảng dán vào bảng tính hoặc chụp màn hình — hoàn toàn không có nhật ký, phá luôn BR-25.3 vốn được BR-33.8 gọi là căn cứ duy nhất để trả lời dữ liệu khách đã ra ngoài những đâu.

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
- `BR-28.1`: Trả về đồng thời: Thông tin liên hệ, Doanh nghiệp trực thuộc, Giai đoạn vòng đời, 3 Cơ hội bán hàng gần nhất, 3 Vé hỗ trợ gần nhất và Ghi chú ghim đầu trang. **Mức hiển thị của phần Thông tin liên hệ** áp đúng chính sách che mặt nạ tại FEAT-04 theo quan hệ của người xem với bản ghi — với Nhân viên Hỗ trợ đang xử lý vé/hội thoại là **cột (C)** theo BR-35.4b; Ngữ cảnh này **không** có chính sách hiển thị riêng. **Phần Ghi chú ghim** được lọc theo phạm vi đọc của người xem theo BR-36.7.
- `BR-28.2 (Cam kết hiệu năng)`: Ngưỡng **nghiệm thu bắt buộc** là phản hồi dưới **150ms (p95)** theo NFR-02; mức **50ms (p50)** là mục tiêu tối ưu hoá mong đợi, không dùng làm tiêu chí đánh giá đạt/không đạt khi nghiệm thu.

---

## J. QUẢN TRỊ ĐỊNH DANH, ĐỒNG THUẬN & TUÂN THỦ DỮ LIỆU CÁ NHÂN

### FEAT-29 — Quản lý Danh tính Đa kênh & Trạng thái Tiếp cận (Identities & Deliverability) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Quản lý toàn bộ các kênh định danh có thể tiếp cận của khách hàng (Email cá nhân, Email công việc, SĐT di động, SĐT bàn, WhatsApp ID, Facebook PSID, Zalo ID) dưới dạng các bản ghi danh tính độc lập.

**Actor:** Mọi người dùng có quyền quản lý Contact.

**Quy tắc nghiệp vụ:**
- `BR-29.1`: Mỗi loại kênh có duy nhất 1 định danh chính (`isPrimary = true`).
- `BR-29.2`: Theo dõi **bốn** trạng thái khả năng tiếp cận theo danh mục A.10: ba trạng thái kỹ thuật — `VERIFIED` (Đã gửi nhận thành công), `BOUNCED` (Email hỏng / SĐT không tồn tại), `UNVERIFIED` (Chưa kiểm tra) — và một trạng thái nghiệp vụ `OBSOLETE` (Không còn hiệu lực) do BR-10.4 sinh ra khi khách rời doanh nghiệp sở hữu địa chỉ đó. Thứ tự thắng khi gộp bản ghi quy định tại BR-18.2.
- `BR-29.3`: Khi một địa chỉ bị đánh dấu `BOUNCED`, hệ thống tự động loại trừ địa chỉ đó khỏi các chiến dịch gửi email/tin nhắn tự động để bảo vệ uy tín tên miền (Domain Reputation).

---

### FEAT-30 — Quản lý Trạng thái Đồng thuận Tiếp thị & Định danh Dùng chung (Consent) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Quản lý trạng thái đồng ý nhận tin tiếp thị (Opt-in Consent) theo chuẩn quốc tế, phân loại mục đích gửi tin và gắn nhãn Định danh dùng chung (Shared Identifier).

**Actor:** Nhân viên Marketing và Quản lý Marketing (cấu hình đồng thuận từng kênh — nhiệm vụ chính theo mục 2.2), Nhân viên Kinh doanh (ghi nhận đồng thuận khi trao đổi trực tiếp với khách hàng), **Nhân viên Hỗ trợ** (hạ đồng thuận khi khách yêu cầu ngay trong vé/hội thoại đang mở — BR-35.4a), **Quản lý Kinh doanh** (trong phạm vi phòng ban), Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-30.1 (Đồng thuận & Hủy nhận tin theo từng Kênh)`: Lưu trữ trạng thái `OPT_IN` / `OPT_OUT` **độc lập cho từng kênh** truyền thông (Email, SMS, WhatsApp, Zalo, và các kênh khác được tích hợp). Khi khách hàng hủy nhận tin trên một kênh, hệ thống chỉ tác động lên kênh đó, không ảnh hưởng tới sự đồng thuận trên các kênh còn lại, trừ khi khách chọn "Hủy nhận tin trên toàn bộ mọi kênh". **Phạm vi tác động của `OPT_OUT` trên một kênh được quy định tại BR-30.5** — không phải chặn mọi loại thư trên kênh đó, mà chặn nhóm thư Tiếp thị trên kênh đó.
- `BR-30.2 (Định danh dùng chung - Shared Identifier)`: Cho phép đánh dấu một số điện thoại/email là "Định danh dùng chung" (ví dụ số tổng đài công ty, số lễ tân) để ngăn chặn việc hệ thống tự động gộp nhầm các khách hàng khác nhau dùng chung số điện thoại đó vào cùng một hồ sơ.
- `BR-30.3 (Bằng chứng Đồng thuận — Consent Evidence) [Yêu cầu mới]`: Mỗi lần trạng thái đồng thuận thay đổi, hệ thống bắt buộc lưu bộ bằng chứng không thể sửa đổi gồm: **(a)** Thời điểm ghi nhận; **(b)** Nguồn thu thập — **bắt buộc chọn từ danh mục A.8**, không nhập tự do; **(c)** Nội dung điều khoản mà khách hàng đã đồng ý (phiên bản văn bản đồng thuận tại thời điểm đó); **(d)** Người/hệ thống thực hiện ghi nhận. Bằng chứng đồng thuận được lưu **vĩnh viễn** ngay cả khi khách hàng đổi trạng thái nhiều lần, phục vụ chứng minh cơ sở xử lý dữ liệu khi có khiếu nại.
- `BR-30.4 (Đồng thuận thu thập qua Nhập khẩu hàng loạt) [Yêu cầu mới]`: Dữ liệu nhập khẩu từ tệp **không được** mặc định gán trạng thái `OPT_IN`. Người thực hiện nhập khẩu **bắt buộc chọn** Cơ sở đồng thuận cho lô dữ liệu từ danh mục chuẩn A.11 — giao diện không cho bỏ trống. Danh mục có sẵn giá trị **"Không có cơ sở đồng thuận"** để khai báo trung thực; khi chọn giá trị này (hoặc khi cơ sở được khai báo không đủ để chứng minh đồng thuận tiếp thị), toàn bộ lô được gán `OPT_OUT` cho nhóm thư Tiếp thị và chỉ được liên hệ theo nhóm thư Giao dịch & Dịch vụ (BR-30.5).
- `BR-30.5 (Phân loại Mục đích Gửi tin — phạm vi chi phối của OPT_OUT) [Yêu cầu mới — sàn bắt buộc]`: Mọi thư/tin nhắn gửi ra từ hệ thống bắt buộc được phân vào **một** trong ba nhóm mục đích sau. Trạng thái `OPT_OUT` (kể cả lựa chọn "Hủy nhận tin trên toàn bộ mọi kênh") **chỉ chi phối nhóm Tiếp thị**:

| Nhóm mục đích | Nội dung thuộc nhóm | Chịu chi phối `OPT_OUT`? |
| --- | --- | :---: |
| **Tiếp thị & Quảng bá** | Bản tin định kỳ, chiến dịch khuyến mãi, thư nuôi dưỡng tự động, mời sự kiện thương mại, thư Win-Back | **Có — chặn tuyệt đối** |
| **Giao dịch & Dịch vụ** | Phản hồi vé hỗ trợ, xác nhận đơn hàng, hóa đơn/nhắc thanh toán, thông báo bảo trì, cảnh báo bảo mật, thông báo pháp lý bắt buộc, **thư báo giá, thư hợp đồng, thư xác nhận cuộc hẹn, thư trả lời một yêu cầu do chính khách hàng đưa ra** | Không |
| **Liên lạc 1-1 do nhân viên chủ động** | Email/tin nhắn do nhân viên gửi trực tiếp trong quá trình phục vụ khách hàng, thoả **đồng thời cả 4 tiêu chí quan sát được** tại BR-30.7 | Không, nhưng **bắt buộc ghi nhật ký** |

  Lý do nghiệp vụ: nếu `OPT_OUT` chặn tất cả, khách hàng bấm "hủy nhận bản tin" hôm nay rồi mai gửi vé hỗ trợ sẽ không được trả lời — sự cố phục vụ khách hàng xảy ra ngay tuần đầu. Ngược lại, nếu không phân loại rõ thì hệ thống sẽ gửi thư tiếp thị cho người đã từ chối, vi phạm đúng cam kết mà BR-19.6 tuyên bố không thể ghi đè. Tenant **không được** cấu hình để nhóm Tiếp thị thoát khỏi chi phối của `OPT_OUT` (sàn pháp lý); tenant **được** cấu hình việc có chặn nhóm "Liên lạc 1-1" hay không (Phụ lục B, `CFG-30-01`).
- `BR-30.6 (Trạng thái Hạn chế Xử lý)`: Khi khách hàng yêu cầu hạn chế xử lý theo FEAT-33, bản ghi được gắn trạng thái `RESTRICTED`. Phạm vi dừng và phạm vi vẫn chạy được quy định dứt khoát để không xung đột với các quy tắc tự động khác:
  - **Dừng:** toàn bộ nhóm thư Tiếp thị; chấm điểm tiềm năng và suy giảm điểm (FEAT-15, FEAT-16); thăng hạng vòng đời **do ngưỡng điểm** (BR-15.5); phân bổ lại và thu hồi Lead tự động (BR-31.7); **đồng hồ cam kết phản hồi lần đầu** (BR-31.7) — vì hệ thống không được thúc nhân viên liên hệ một người vừa yêu cầu hạn chế xử lý, và bản ghi vì vậy bị loại khỏi mẫu đo `KPI-03`; đưa vào danh sách phân khúc chiến dịch.
  - **Dỡ trạng thái:** `RESTRICTED` được dỡ khi **chính chủ thể dữ liệu rút lại yêu cầu qua kênh đã xác minh theo BR-33.7**, hoặc theo nhánh dỡ sớm biện pháp phòng ngừa tại BR-33.7b. Thẩm quyền dỡ **khác nhau theo nhánh**: nhánh chủ thể rút lại yêu cầu do **Quản trị viên** thực hiện một mình, vì đây là làm đúng ý nguyện vừa được xác minh của chính chủ thể; nhánh dỡ sớm biện pháp phòng ngừa cần **Quản trị viên cùng Người phụ trách Bảo vệ Dữ liệu** theo đúng BR-33.7b, vì ở đó hệ thống đang dỡ một biện pháp bảo vệ mà chủ thể **chưa** xác minh được danh tính. Cả hai nhánh đều ghi nhật ký theo NFR-07 và làm đồng hồ cam kết chạy lại từ thời điểm dỡ. Không có đường dỡ này thì một khách đổi ý sẽ bị đóng băng vĩnh viễn.
  - **Vẫn chạy:** nhóm thư Giao dịch & Dịch vụ, để doanh nghiệp thực hiện được nghĩa vụ hợp đồng; và **các bước chuyển giai đoạn theo nguyên tắc 1** (BR-12.2, BR-12.9 — khi phát sinh Cơ hội bán hàng hoặc Cơ hội `Closed Won`). Lý do: nguyên tắc 1 chỉ ghi nhận một thực tế thương mại đã xảy ra giữa hai bên, không phải hoạt động xử lý dữ liệu cho mục đích tiếp thị, nên không thuộc phạm vi mà quyền hạn chế xử lý nhắm tới. Nếu dừng cả nhóm này thì hồ sơ của một khách đang ký hợp đồng sẽ đứng sai giai đoạn và làm sai báo cáo doanh thu.
- `BR-30.7 (Tiêu chí quan sát được của nhóm Liên lạc 1-1) [Yêu cầu mới — sàn bắt buộc]`: Một lượt gửi chỉ được xếp vào nhóm "Liên lạc 1-1" khi thoả **đồng thời cả 4** tiêu chí: **(a)** do một người dùng thật thực hiện, không do tiến trình tự động hay lịch gửi; **(b)** số người nhận **tối đa 5** trong một lượt gửi (Phụ lục B, `CFG-30-02`); **(c)** **không** dùng **mẫu chiến dịch** (mẫu do Marketing tạo và quản lý trong công cụ chiến dịch) — mẫu thư nghiệp vụ cá nhân do chính nhân viên hoặc đội kinh doanh soạn (thư theo dõi sau cuộc gọi, thư tự giới thiệu, thư hỏi lịch gặp) **vẫn được phép** vì đây là chuẩn nghề của đội kinh doanh, không phải tiếp thị. **Thư báo giá và thư xác nhận cuộc hẹn không được xét ở tiêu chí này**, vì bảng BR-30.5 đã xếp chúng vào nhóm **Giao dịch & Dịch vụ** — chúng nằm ngoài nhóm Liên lạc 1-1 nên không chịu trần số người nhận tại (b); **(d)** **không gửi theo lô cho toàn bộ một danh sách** — việc nhân viên mở một danh sách hiển thị rồi chọn thủ công vài khách hàng cụ thể **vẫn thoả tiêu chí này**, vì mọi nhân viên đều bắt đầu ngày làm việc từ một danh sách hiển thị (FEAT-26 có sẵn "Khách hàng của tôi", "Khách chưa có hoạt động 30 ngày"). Lượt gửi không thoả đủ 4 tiêu chí **bắt buộc bị xếp vào nhóm Tiếp thị** và chịu chi phối `OPT_OUT`.

  **Lưu ý phân loại quan trọng:** thư báo giá, thư hợp đồng, thư xác nhận cuộc hẹn và thư trả lời một yêu cầu do chính khách hàng đưa ra thuộc nhóm **"Giao dịch & Dịch vụ"** — đã được liệt kê tường minh trong bảng BR-30.5 để không còn hai đáp án cho cùng một loại thư — không thuộc nhóm Liên lạc 1-1 lẫn nhóm Tiếp thị — nên không chịu chi phối `OPT_OUT` và không bị giới hạn số người nhận. Nếu không phân loại như vậy, một khách đã hủy nhận bản tin nhưng đang thương lượng hợp đồng sẽ không nhận được báo giá, và nhân viên sẽ gửi từ hộp thư cá nhân — đưa nội dung thương lượng ra khỏi hệ thống, mất dòng thời gian và mất bằng chứng liên hệ nhóm 1 tại BR-31.8. Nếu không có bộ tiêu chí này, một chiến dịch tiếp thị chỉ cần gửi từ tài khoản nhân viên qua hành động nhanh là ra khỏi tầm chi phối của `OPT_OUT`, biến cam kết "không thể ghi đè" tại BR-19.6 thành hình thức.
- `BR-30.8 (Giám sát nhóm Liên lạc 1-1) [Yêu cầu mới]`: Hệ thống cung cấp báo cáo định kỳ hằng tháng cho Người phụ trách Bảo vệ Dữ liệu và Chủ sở hữu Workspace về khối lượng thư nhóm Liên lạc 1-1 đã gửi tới các bản ghi đang ở trạng thái `OPT_OUT`, chia theo người gửi. Vượt ngưỡng bất thường sẽ được cảnh báo để rà soát dấu hiệu lách quy tắc.
- `BR-30.10 (Cưỡng chế Đồng thuận trên MỌI nguồn tác động) [Yêu cầu mới — sàn bắt buộc]`: Bảng trường bị cưỡng chế tại BR-18.2 chỉ điều chỉnh **thao tác gộp**. Quy tắc này mở rộng cơ chế cưỡng chế ra mọi nguồn tác động còn lại, theo đúng mô hình mà BR-12.6 đã áp dụng cho ma trận vòng đời:
  - **Nguyên tắc bất biến:** Chỉ **hành vi của chính chủ thể dữ liệu** (khách tự đăng ký, tự bấm liên kết xác nhận, tự trả lời trên kênh của mình) hoặc **bằng chứng đồng thuận mới hợp lệ theo BR-30.3** mới **nâng** được mức đồng thuận từ `OPT_OUT` lên `OPT_IN`. Không nguồn nào khác làm được việc này.
  - **Nhập khẩu hàng loạt (BR-23.3, chiến lược "Cập nhật đè"):** **không bao giờ** được hạ mức nghiêm ngặt của bản ghi hiện hữu. Nếu bản ghi cũ đang `OPT_OUT` mà dòng dữ liệu nhập vào là `OPT_IN`, hệ thống **giữ `OPT_OUT`** và ghi dòng đó vào báo cáo kết quả nhập với ghi chú "Không nâng được mức đồng thuận — thiếu bằng chứng của chủ thể".
  - **Chỉnh sửa thủ công bởi bất kỳ vai trò nghiệp vụ nào có quyền ghi trên bản ghi** — Nhân viên và Quản lý Kinh doanh, Nhân viên và Quản lý Marketing, **và Nhân viên Hỗ trợ khi tiếp nhận yêu cầu của khách theo FEAT-33**: được phép **hạ** xuống `OPT_OUT` tự do (ghi nhận khách từ chối qua điện thoại, gặp trực tiếp), nhưng **nâng** lên `OPT_IN` bắt buộc kèm bằng chứng đồng thuận theo BR-30.3 với nguồn thu thập chọn từ A.8. Không có bằng chứng thì giao diện không cho lưu.
  - Mọi lượt nâng mức đồng thuận, từ bất kỳ nguồn nào, đều được ghi nhật ký theo NFR-07.

  Không có quy tắc này, cam kết "`OPT_OUT` không thể bị ghi đè" tại BR-19.6 và "có cơ chế cưỡng chế thật ngay từ ngày phát hành" tại BR-30.9 là tuyên bố tuân thủ sai sự thật, vì hai đường vào phổ biến hơn cả gộp vẫn hở.
- `BR-30.9 (Mặc định an toàn khi thiếu khai báo nhóm) [sàn bắt buộc]`: Mọi lượt gửi **không khai báo nhóm mục đích** bị hệ thống mặc định xếp vào **nhóm Tiếp thị** và chịu chi phối `OPT_OUT`. Đây là mặc định an toàn nhất về pháp lý và là điều kiện để cam kết tại BR-19.6 có cơ chế cưỡng chế thật ngay từ ngày phát hành, không phụ thuộc việc phân hệ gửi tin có khai báo nhóm đầy đủ hay chưa (xem mục 7, vấn đề #7).

---

### FEAT-33 — Quyền Chủ thể Dữ liệu & Xử lý Yêu cầu Dữ liệu Cá nhân (Data Subject Rights) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cung cấp quy trình chuẩn để tiếp nhận và xử lý các yêu cầu của khách hàng liên quan đến dữ liệu cá nhân của chính họ, đáp ứng nghĩa vụ của doanh nghiệp theo pháp luật bảo vệ dữ liệu cá nhân (bao gồm chuẩn GDPR và quy định về bảo vệ dữ liệu cá nhân tại Việt Nam). Đây là quy trình khác biệt hoàn toàn với Thùng rác nội bộ (FEAT-05): Thùng rác phục vụ nhu cầu vận hành nội bộ và có thể phục hồi, còn quyền xóa của chủ thể dữ liệu là nghĩa vụ pháp lý và **không thể phục hồi**.

**Actor:** Quản trị viên Workspace và Chủ sở hữu Workspace (xử lý và thực thi xóa vĩnh viễn); Người phụ trách Bảo vệ Dữ liệu (giám sát, phê duyệt); Nhân viên Hỗ trợ và Quản lý Kinh doanh (**tiếp nhận và ghi nhận** yêu cầu vào hệ thống theo dõi; **thực thi ngay** hai loại yêu cầu chỉ thu hẹp phạm vi xử lý và đảo lại được — gắn `RESTRICTED` và hạ đồng thuận xuống `OPT_OUT` — đúng cam kết "Tức thì" tại bảng loại yêu cầu; **không** thực thi **ba loại còn lại** — bản sao dữ liệu, chỉnh sửa, xóa vĩnh viễn — và không đóng được bản ghi yêu cầu).

**Các loại yêu cầu được hỗ trợ:**

| Loại yêu cầu | Nội dung nghiệp vụ | Thời hạn xử lý cam kết |
| --- | --- | --- |
| **Yêu cầu bản sao dữ liệu (Access/Portability)** | Xuất toàn bộ dữ liệu cá nhân của khách hàng đang lưu trong hệ thống ra tệp có cấu trúc đọc được | **30 ngày** kể từ ngày tiếp nhận |
| **Yêu cầu chỉnh sửa (Rectification)** | Cập nhật thông tin cá nhân không chính xác theo đề nghị của khách hàng | **15 ngày** |
| **Yêu cầu xóa vĩnh viễn (Erasure / Right to be Forgotten)** | Xóa vĩnh viễn dữ liệu cá nhân, không đưa vào Thùng rác | **30 ngày** |
| **Yêu cầu hạn chế xử lý (Restriction)** | Giữ dữ liệu nhưng dừng mọi hoạt động tiếp thị và tự động hóa trên bản ghi | **Tức thì** khi tiếp nhận — người tiếp nhận (Nhân viên Hỗ trợ, Quản lý Kinh doanh) **được** gắn trạng thái `RESTRICTED` ngay tại bước ghi nhận yêu cầu, vì đây là thao tác **chỉ thu hẹp** phạm vi xử lý và có thể đảo lại; các thao tác còn lại của FEAT-33 vẫn cần Quản trị viên (Ghi chú 5 mục 5) |
| **Yêu cầu rút lại đồng thuận (Withdraw Consent)** | Chuyển toàn bộ kênh sang `OPT_OUT` | **Tức thì** |

**Quy tắc nghiệp vụ:**
- `BR-33.1 (Tiếp nhận & Theo dõi)`: Mỗi yêu cầu được tạo thành một bản ghi theo dõi riêng, gắn với Contact liên quan, ghi nhận: loại yêu cầu, ngày tiếp nhận, hạn xử lý, người phụ trách xử lý, trạng thái (Đã tiếp nhận / Đang xử lý / Đã hoàn tất / Bị từ chối kèm lý do). Hệ thống cảnh báo khi yêu cầu sắp đến hạn.
- `BR-33.2 (Xóa vĩnh viễn có kiểm soát)`: Thao tác xóa vĩnh viễn theo yêu cầu chủ thể dữ liệu chỉ được thực hiện bởi Quản trị viên hoặc Chủ sở hữu, bắt buộc xác nhận hai bước và **không đi qua Thùng rác**. Sau khi hoàn tất, hệ thống giữ lại duy nhất một bản ghi tối thiểu để chứng minh đã thực hiện nghĩa vụ (mã bản ghi đã xóa, loại yêu cầu, thời điểm hoàn tất, người thực hiện) — không chứa dữ liệu cá nhân.
- `BR-33.3 (Ngoại lệ nghĩa vụ lưu trữ)`: Nếu khách hàng còn nghĩa vụ hợp đồng, hóa đơn hoặc tranh chấp pháp lý đang xử lý, yêu cầu xóa vĩnh viễn được **từ chối một phần** theo cơ sở "nghĩa vụ pháp lý phải lưu trữ": hệ thống xóa dữ liệu tiếp thị và dữ liệu liên hệ không cần thiết, giữ lại dữ liệu tối thiểu phục vụ nghĩa vụ pháp lý, và bắt buộc ghi rõ lý do từ chối một phần trong bản ghi theo dõi để phản hồi khách hàng.
- `BR-33.4 (Chặn xung đột với thao tác Gộp)`: Bản ghi đang có yêu cầu chủ thể dữ liệu chưa hoàn tất không được phép gộp (đồng bộ với BR-19.6).
- `BR-33.5 (Chính sách Lưu trữ Dữ liệu Không hoạt động)`: Contact không phát sinh bất kỳ tương tác nào trong **36 tháng** liên tục và không thuộc giai đoạn `Customer`/`Evangelist` được đưa vào danh sách đề xuất rà soát lưu trữ. Quản trị viên quyết định lưu trữ dài hạn (Archive) hoặc xóa. Hệ thống **không tự động xóa** dữ liệu khách hàng khi hết thời hạn này — quyết định luôn thuộc về con người, nhằm tránh mất dữ liệu kinh doanh ngoài ý muốn. Thời hạn rà soát là tham số cấu hình theo tenant (Phụ lục B, `CFG-33-02`); riêng hành vi "không tự động xóa" đối với **hồ sơ khách hàng đã định danh còn nằm ngoài Thùng rác** là **cố định**, không cấu hình được.

  **Năm ngoại lệ có chủ đích của nguyên tắc này**, mỗi ngoại lệ đều nhằm thu hẹp phạm vi dữ liệu cá nhân phải bảo vệ và đều chỉ xóa/khử phần định danh chứ không xóa giá trị kinh doanh: **(a)** tự động khử định danh nhóm Định danh KYC khi hết thời hạn lưu (BR-01.5b); **(b)** tự động xóa Hồ sơ Khách hàng Tạm chưa từng có nhân viên phản hồi (BR-33.6, nhánh thứ nhất); **(c)** tự động khử định danh Hồ sơ Khách hàng Tạm khi chạm trần lưu tuyệt đối (BR-33.6, trần 18 tháng); **(d)** **dọn dẹp Thùng rác** — tiến trình tự động xóa vĩnh viễn bản ghi đã nằm trong Thùng rác quá thời hạn (BR-05.4, `CFG-05-01`), vì đây là dữ liệu mà **con người đã ra quyết định xóa** và hệ thống chỉ thực thi quyết định đó sau một thời gian ân hạn; ngoại lệ này chịu toàn bộ chốt an toàn tại BR-05.6 nên không xóa được bản ghi còn Cơ hội/Vé mở, còn trong thời hạn hoàn tác gộp, hay còn nghĩa vụ hợp đồng. **(e)** tự động xóa **tệp nhập khẩu gốc** khi hết thời hạn lưu (BR-33.8, `CFG-22-01`) và **tài liệu xác minh danh tính** thu theo BR-33.7 sau 30 ngày kể từ khi yêu cầu hoàn tất (BR-01.5b) — cả hai là tệp đính kèm phục vụ một tiến trình đã kết thúc, không phải hồ sơ khách hàng.

  Ngoài **năm** ngoại lệ này, không tiến trình nào được tự động xóa dữ liệu khách hàng. Điểm chung của cả năm: hoặc chỉ khử phần định danh mà giữ giá trị kinh doanh, hoặc chỉ thực thi một quyết định xóa mà con người đã đưa ra trước đó.
- `BR-33.6 (Xử lý Hồ sơ Khách hàng Tạm tồn dư)`: Hồ sơ Khách hàng Tạm (BR-01.1b) không được bổ sung email hoặc số điện thoại trong **90 ngày** kể từ lần tương tác gần nhất (tham số cấu hình theo tenant, Phụ lục B `CFG-33-01`) được xử lý như sau:
  - **Hồ sơ chưa từng có nhân viên phản hồi** → tự động xóa vĩnh viễn cùng nội dung hội thoại vãng lai, nhằm giảm tồn dư dữ liệu rác và thu hẹp phạm vi dữ liệu cá nhân phải bảo vệ.
  - **Hồ sơ đã có tương tác của nhân viên** (đã được phản hồi, đã ghi nhận cam kết hoặc khiếu nại) → **không** tự động xóa ngay; chuyển vào danh sách rà soát thủ công của Quản trị Chất lượng Dữ liệu, thống nhất nguyên tắc "quyết định xóa luôn thuộc về con người" tại BR-33.5. Lý do: khiếu nại thực tế thường phát sinh sau vài tháng, nếu xóa ở ngày thứ 91 thì doanh nghiệp mất bằng chứng.
  - **Trần lưu tuyệt đối cho nhánh trên — sàn bắt buộc:** danh sách rà soát thủ công **không được tồn đọng vô hạn**. Sau **18 tháng** kể từ tương tác gần nhất (Phụ lục B, `CFG-33-03`), nếu vẫn chưa có ai quyết định, hệ thống **tự động khử định danh** hồ sơ tạm: xóa định danh thiết bị/kênh chat và mọi dữ liệu nhận diện, giữ lại nội dung hội thoại ở dạng vô danh phục vụ tra soát nghiệp vụ. Đây là lớp dữ liệu thu thập **không có hành vi đăng ký chủ động** của khách nên không được phép lưu định danh vô thời hạn.
  - **Sàn cho nội dung hội thoại:** nội dung hội thoại vãng lai được giữ theo chính sách lưu trữ của phân hệ Hộp thư Đa kênh, nhưng phân hệ đó **không được** lưu định danh của khách vãng lai lâu hơn trần 18 tháng nêu trên. Đây là ràng buộc tối thiểu mà tài liệu này đặt ra cho tài liệu kia, thay vì tham chiếu mở.
- `BR-33.7 (Xác minh Danh tính Chủ thể Dữ liệu — phân tầng theo mức rủi ro) [Yêu cầu mới — sàn bắt buộc]`: Mức xác minh yêu cầu **tương ứng với mức độ không thể phục hồi** của thao tác, không áp dụng một mức duy nhất cho mọi loại yêu cầu:

| Loại yêu cầu | Mức xác minh bắt buộc | Lý do |
| --- | --- | --- |
| **Rút lại đồng thuận**, **Hạn chế xử lý** | **Xác nhận trên chính kênh khách đang liên hệ** (trả lời đúng phiên hội thoại, bấm liên kết trong chính email/tin nhắn đã gửi tới khách, hoặc xác nhận trong phiên chat đang mở) | Hai thao tác này chỉ làm giảm mức xử lý dữ liệu, không mất dữ liệu, và luật cam kết xử lý tức thì. Đòi xác minh nặng ở đây là chặn quyền chính đáng của khách |
| **Yêu cầu bản sao dữ liệu**, **Chỉnh sửa** | Một trong: kênh liên lạc **đã xác thực** của hồ sơ · giấy tờ định danh đối chiếu KYC · xác nhận của người đại diện hợp đồng · **xác nhận hai yếu tố qua chính kênh chat/định danh thiết bị** đối với Hồ sơ Khách hàng Tạm | Có rủi ro tiết lộ dữ liệu cho người không phải chủ thể |
| **Xóa vĩnh viễn** | Như trên, **cộng thêm**: xác nhận hai bước trên giao diện (BR-33.2), và với khách ở `Customer`/`Evangelist` phải có **hai người khác nhau** (người xác minh và người phê duyệt xóa) | Thao tác không thể phục hồi. Thiếu bước này thì một email mạo danh là đủ để xoá sạch hồ sơ một khách hàng đang trả tiền |

  **Nguyên tắc khi không xác minh được (thay cho việc từ chối trắng):** Hệ thống **không** được từ chối và bỏ mặc. Thay vào đó áp dụng **biện pháp phòng ngừa tạm thời**: gán `OPT_OUT` toàn bộ kênh Tiếp thị và trạng thái `RESTRICTED` (BR-30.6), đồng thời gửi phản hồi nêu rõ cần bổ sung gì để thực hiện được yêu cầu.

  **Biện pháp phòng ngừa là có thời hạn và có đường dỡ — bốn ràng buộc bắt buộc:**
  - **(a) Thời hạn:** tối đa **30 ngày**, khớp thời hạn xử lý yêu cầu tại bảng FEAT-33 (Phụ lục B, `CFG-33-04`). Hết thời hạn mà khách không bổ sung xác minh, trạng thái tự động dỡ và yêu cầu được đóng với lý do "Không xác minh được danh tính".
  - **(b) Thẩm quyền dỡ sớm:** Quản trị viên **cùng** Người phụ trách Bảo vệ Dữ liệu (theo quy tắc thay thế người thứ hai tại NFR-14 nếu tenant không có DPO), khi xác định người yêu cầu không phải chủ thể dữ liệu hoặc khi khách xác nhận không có yêu cầu nào. Việc dỡ được ghi nhật ký.
  - **(c) Bắt buộc thông báo:** hệ thống **phải thông báo cho Người phụ trách** bản ghi khi áp trạng thái phòng ngừa, nêu rõ lý do và thời hạn. Không có thông báo này thì nhân viên chỉ thấy khách "biến mất" khỏi mọi danh sách mà không hiểu vì sao.
  - **(d) Ghi nhận đúng bản chất:** bằng chứng đồng thuận cho lượt hạ mức này dùng giá trị **"Yêu cầu chưa xác minh được danh tính"** tại A.8 — **không** được ghi là "Yêu cầu trực tiếp của khách hàng", vì đó là ghi nhận sai sự thật vào chính kho chứng cứ dùng để đối chiếu khi bị khiếu nại.

  Lý do phải có bốn ràng buộc: nếu trạng thái phòng ngừa là vĩnh viễn và không ai dỡ được, thì một email mạo danh (hoặc chỉ là khách gửi từ hộp thư khác) là đủ để rút một khách hàng đang trả tiền khỏi mọi chiến dịch và mọi tự động hóa mãi mãi — và ở quy mô, cơ chế này có thể bị dùng để đóng băng cả một tập khách hàng. Lý do: nếu ba phương thức xác minh đều bất khả (Hồ sơ Khách hàng Tạm không có kênh xác thực, không KYC, không hợp đồng; khách cá nhân chỉ có một số điện thoại chưa xác thực), quy tắc chống mạo danh sẽ biến thành quy tắc từ chối có hệ thống quyền của đúng nhóm dữ liệu rủi ro nhất.
- `BR-33.8 (Phạm vi Xóa & Ngoại lệ Sổ cái) [Yêu cầu mới]`: Thao tác xóa vĩnh viễn theo quyền chủ thể dữ liệu phải xử lý dứt điểm **mọi nơi lưu dữ liệu cá nhân**, tránh tình trạng doanh nghiệp trả lời khách "đã xóa xong" trong khi dữ liệu vẫn còn ở nơi khác:

| Nơi lưu dữ liệu | Xử lý bắt buộc |
| --- | --- |
| Hồ sơ Contact, các kênh danh tính, thẻ phân loại, liên kết doanh nghiệp | **Xóa vĩnh viễn** |
| Dòng thời gian, ghi chú, hoạt động gắn với khách hàng | **Xóa vĩnh viễn** phần nội dung chứa dữ liệu cá nhân |
| Ảnh chụp dữ liệu trong Sổ cái Gộp (BR-19.3) | **Khử định danh** — xóa dữ liệu cá nhân trong ảnh chụp, giữ lại cấu trúc sổ cái và mã bản ghi |
| Tệp xuất dữ liệu còn hiệu lực tải về (BR-25.2) | **Thu hồi token, hủy tệp** |
| Nhật ký kiểm toán (NFR-07, NFR-08) | **Giữ ở dạng đã khử định danh** — chỉ còn mã bản ghi và loại thao tác, vì đây là nghĩa vụ pháp lý phải lưu. Nhật ký vốn đã không lưu giá trị thật của trường nhạy cảm theo NFR-07 nên khối lượng phải khử là tối thiểu |
| Bằng chứng đồng thuận (BR-30.3) | **Giữ ở dạng tối thiểu** — thời điểm, nguồn thu thập, phiên bản điều khoản; xóa dữ liệu nhận diện cá nhân |
| **Bản sao lưu hệ thống (NFR-10)** | **Không phục hồi lại bản ghi đã xóa theo quyền chủ thể dữ liệu.** Bản sao lưu cuốn vòng trong **35 ngày** rồi tự hết hiệu lực; trong thời gian đó dữ liệu chỉ tồn tại ở dạng không truy cập được bằng nghiệp vụ. Nếu buộc phải phục hồi hệ thống từ bản sao lưu, quy trình phục hồi bắt buộc chạy lại danh sách yêu cầu xóa đã hoàn tất để xóa lại các bản ghi đó |
| **Tệp nhập khẩu gốc do người dùng tải lên (BR-22.1)** | **Xóa vĩnh viễn.** Ngoài ra tệp nhập khẩu gốc có thời hạn lưu tối đa **30 ngày** kể từ khi tiến trình nhập hoàn tất, sau đó tự động xóa bất kể có yêu cầu chủ thể dữ liệu hay không (Phụ lục B, `CFG-22-01`) |
| **Tệp báo cáo lỗi nhập khẩu (BR-24.2)** | **Xóa vĩnh viễn và thu hồi mã tải về.** Mã tải về tệp báo cáo lỗi có thời hạn **24 giờ**, thống nhất với tệp xuất dữ liệu tại BR-25.2 |
| **Nhật ký xuất dữ liệu** | Bắt buộc ghi lại **tập trường và tập bản ghi** của mỗi lần xuất (BR-25.3), để trả lời được câu hỏi "dữ liệu của khách đã ra ngoài những đâu" khi có yêu cầu xóa; bản thân nhật ký này giữ ở dạng đã khử định danh |
| **Nội dung hội thoại đa kênh** (Livechat, WhatsApp, Zalo, Facebook) — thuộc [`omnichat-srs.md`](./omnichat-srs.md) | **Khử định danh hoặc xóa** toàn bộ nội dung hội thoại gắn với khách hàng, gồm tệp đính kèm trong hội thoại. **Sàn tối thiểu mà tài liệu này đặt ra cho tài liệu đó:** phân hệ Hộp thư Đa kênh bắt buộc có cơ chế thực thi quyền xóa theo mã khách hàng, hoàn tất trong cùng thời hạn của yêu cầu, và trả về xác nhận để đưa vào Biên bản Hoàn tất Xử lý |
| **Nội dung Vé hỗ trợ và tệp đính kèm** — thuộc [`tickets-srs.md`](./tickets-srs.md) | **Khử định danh** nội dung vé (giữ dữ liệu thống kê vận hành như thời gian xử lý, phân loại) và **xóa tệp đính kèm** do khách gửi. **Sàn tối thiểu đặt cho tài liệu đó:** như hàng trên |

  Các tuyên bố **"lưu vĩnh viễn"** tại NFR-05, BR-30.3 và các tuyên bố **"không thể sửa đổi / không cho phép xóa"** tại NFR-07, NFR-08, BR-30.3 đều phải được đọc kèm ngoại lệ khử định danh của quy tắc này — đây là ngoại lệ duy nhất, do Quản trị viên cùng Người phụ trách Bảo vệ Dữ liệu thực hiện, và bản thân thao tác khử định danh được ghi lại một bản ghi nhật ký (NFR-08).

---

## K. QUYỀN SỞ HỮU, CỘNG TÁC & GHI NHẬN HOẠT ĐỘNG (OWNERSHIP, COLLABORATION & ACTIVITY)

### FEAT-34 — Chuyển giao Quyền phụ trách & Bàn giao khi Nhân viên rời tổ chức (Ownership Transfer & Offboarding) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép chuyển Người phụ trách của một hoặc hàng loạt khách hàng sang nhân viên khác, và bảo đảm không có bản ghi nào trở thành "vô chủ" khi một nhân viên rời tổ chức, chuyển bộ phận hoặc nghỉ dài hạn.

**Actor:** **Chính người dùng — bất kể vai trò** (tự khai báo nghỉ phép và người xử lý thay theo BR-34.6), **Người phụ trách hiện tại** (bàn giao ngang cho đồng nghiệp cùng nhóm theo BR-34.1b), Quản lý Kinh doanh (trong phạm vi phòng ban), Quản trị viên Workspace, Chủ sở hữu Workspace.

**Bối cảnh nghiệp vụ:** Nhân viên nghỉ việc là sự kiện xảy ra hàng tháng ở mọi đội kinh doanh. Vì phạm vi truy cập bị giới hạn theo BR-01.4, toàn bộ danh bạ của nhân viên rời đi sẽ trở thành vùng chết nếu không có công cụ bàn giao: đồng nghiệp không thấy, Quản lý chỉ thấy trong phạm vi phòng ban, và tổ chức buộc phải cấp quyền "Xem toàn bộ" cho tất cả để chữa cháy — phá vỡ toàn bộ mô hình phân quyền tại mục 5.

**Quy tắc nghiệp vụ:**
- `BR-34.1 (Chuyển giao đơn lẻ)`: Trên hồ sơ Contact/Account, người có quyền được đổi Người phụ trách. Hệ thống ghi nhận vào lịch sử bản ghi và gửi thông báo cho cả người giao và người nhận.
- `BR-34.1b (Bàn giao ngang giữa đồng nghiệp) [Yêu cầu mới]`: **Người phụ trách hiện tại** được phép tự khởi tạo "Đề nghị chuyển giao" cho một đồng nghiệp **trong cùng nhóm/đơn vị tổ chức**, không cần Quản lý thực hiện thay. Chuyển giao có hiệu lực khi **người nhận chấp nhận**; hệ thống thông báo cho Quản lý Kinh doanh và Quản lý được **thu hồi trong 3 ngày làm việc** nếu không đồng ý. Chuyển giao ra ngoài nhóm/đơn vị tổ chức vẫn phải do Quản lý trở lên thực hiện. Lý do: việc hoán đổi khách giữa hai nhân viên (đổi địa bàn, khách quen của đồng nghiệp, khách yêu cầu đổi người phụ trách) xảy ra liên tục và bình thường; nếu mọi lượt đều phải qua Quản lý thì Quản lý trở thành thư ký chuyển bản ghi, và trong lúc chờ thì đồng nghiệp không thấy được bản ghi theo BR-01.4 nên khách gọi vào không ai có ngữ cảnh — dẫn tới cách lách là chuyển thông tin khách qua kênh chat nội bộ, đưa dữ liệu ra khỏi hệ thống.
- `BR-34.2 (Chuyển giao hàng loạt)`: Cho phép chọn nhiều bản ghi theo bộ lọc (theo Người phụ trách, Đơn vị tổ chức, Giai đoạn vòng đời, Thẻ) và chuyển giao đồng thời. **Bắt buộc có bước xem trước** hiển thị: tổng số Contact, số Account, số Cơ hội bán hàng đang mở, số Vé hỗ trợ đang mở sẽ bị ảnh hưởng, trước khi người dùng xác nhận.
- `BR-34.3 (Phạm vi chuyển giao thực thể con)`: Người thực hiện chọn một trong ba mức: **(a)** chỉ chuyển Contact/Account; **(b)** chuyển kèm Cơ hội bán hàng và Vé hỗ trợ **đang mở**; **(c)** chuyển kèm toàn bộ, gồm cả thực thể đã đóng. Mặc định là **(b)** (tham số cấu hình theo tenant, Phụ lục B `CFG-34-01`).
- `BR-34.4 (Chốt an toàn khi Vô hiệu hoá người dùng) [sàn bắt buộc]`: Hệ thống **không cho phép** vô hiệu hoá một người dùng khi người đó còn là Người phụ trách của bất kỳ bản ghi nào, cho tới khi người thực hiện chỉ định người nhận bàn giao. Nếu tổ chức cần vô hiệu hoá gấp (ví dụ tình huống rủi ro bảo mật), hệ thống cho phép **bàn giao tạm về Quản lý trực tiếp** của người đó làm mặc định, và đưa toàn bộ bản ghi vào danh sách "Chờ bàn giao lại".
- `BR-34.5 (Báo cáo Bản ghi vô chủ)`: Cung cấp báo cáo thường trực "Bản ghi không có Người phụ trách hoạt động" (người phụ trách đã bị vô hiệu hoá, đã rời tổ chức, hoặc trường Người phụ trách rỗng), phục vụ Quản lý và Quản trị Chất lượng Dữ liệu rà soát định kỳ.
- `BR-34.6 (Nghỉ phép & Uỷ quyền tạm)`: **Chính người dùng — bất kể vai trò** — được tự khai báo khoảng thời gian nghỉ phép kèm người xử lý thay (Quản lý Kinh doanh cũng khai báo được thay cho thành viên trong nhóm). Trong khoảng đó, người dùng được coi là **"không khả dụng"**: Lead mới không phân bổ cho họ (BR-31.1), yêu cầu chờ xử lý chuyển ngay cho người xử lý thay (BR-31.6), nhưng **quyền phụ trách chính không thay đổi**. Trạng thái không khả dụng cũng được áp dụng **tự động** khi tài khoản bị vô hiệu hoá hoặc người dùng không đăng nhập quá **14 ngày liên tiếp**. Với nhánh tự động này — vốn không có ai được khai báo làm người xử lý thay — áp dụng ba quy tắc: **(a)** người xử lý thay mặc định là **Quản lý trực tiếp** của người đó, thống nhất với BR-34.4; **(b)** hệ thống **bắt buộc thông báo** cho chính người dùng và cho Quản lý khi trạng thái được bật; **(c)** trạng thái **tự hết hiệu lực ngay ở lần đăng nhập kế tiếp**. Không có ba quy tắc này thì câu "yêu cầu chờ xử lý chuyển ngay cho người xử lý thay" tại BR-31.6 không có đích, và một nhân viên đi công tác dài sẽ bị âm thầm loại khỏi vòng phân bổ trong khi yêu cầu của khách cũ họ phụ trách nằm im — đúng "hố đen mất doanh thu" mà BR-31.6 muốn tránh.
- `BR-34.8 (Ghi nhận người xử lý thay để tính thành tích) [Yêu cầu mới]`: Khi một người **không phải Người phụ trách chính** xử lý một Yêu cầu chờ xử lý (BR-31.6) hoặc một Cơ hội bán hàng phát sinh từ yêu cầu đó, hệ thống ghi nhận trường **"Người xử lý"** riêng biệt với trường Người phụ trách trên bản ghi yêu cầu. Báo cáo thành tích phải nhìn được cả hai vai. Lý do: đây là nguồn nhu cầu chất lượng cao nhất (khách cũ hỏi mua thêm) và cũng là nơi sinh tranh chấp nội bộ đầu tiên — chính BR-19.9 đã thừa nhận việc mất bản ghi ảnh hưởng trực tiếp tới ghi nhận thành tích và hoa hồng. Nếu không ghi nhận, hai người sẽ tranh nhau một đơn mà không có quy tắc phân xử, hoặc không ai nhận vì biết không được tính công.
- `BR-34.7 (Kiểm toán)`: Mọi thao tác chuyển giao (đơn lẻ và hàng loạt) được ghi nhật ký kiểm toán theo NFR-07, gồm: người thực hiện, người giao, người nhận, số lượng và danh sách bản ghi bị ảnh hưởng, thời điểm.

---

### FEAT-35 — Chia sẻ Bản ghi & Đội ngũ Phụ trách Khách hàng (Record Sharing & Account Teams) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép nhiều người cùng phục vụ một khách hàng với các mức quyền khác nhau, thay cho mô hình một Người phụ trách duy nhất.

**Actor:** Người phụ trách bản ghi (chia sẻ bản ghi mình phụ trách), **Nhân viên Hỗ trợ** (đối tượng chính nhận quyền đọc tự động theo BR-35.4), Quản lý Kinh doanh, Quản trị viên Workspace, Chủ sở hữu Workspace.

**Bối cảnh nghiệp vụ:** Trong thực tế, một khách hàng doanh nghiệp lớn được phục vụ đồng thời bởi nhân viên kinh doanh, Quản lý Khách hàng Hiện hữu, nhân viên hỗ trợ và kế toán. Mô hình một Người phụ trách duy nhất cộng phạm vi phòng ban không diễn tả được điều này. Đặc biệt, do Người phụ trách được gán cho người tạo (BR-01.3) — thực tế luôn là nhân viên kinh doanh — nên phạm vi "Scope gán" của **Nhân viên Hỗ trợ gần như là tập rỗng**, khiến tính năng Ngữ cảnh Khách hàng 1 chạm (FEAT-28) không dùng được cho đúng đối tượng mà nó được thiết kế cho, và `KPI-02` không thể đạt.

**Quy tắc nghiệp vụ:**
- `BR-35.1 (Đội ngũ Phụ trách bản ghi)`: Mỗi Contact/Account có thể có một Đội ngũ Phụ trách gồm nhiều thành viên, mỗi thành viên mang một vai trò tham gia từ danh mục A.13 (Kinh doanh chính, Hỗ trợ kỹ thuật, Quản lý khách hàng, Kế toán công nợ, Quan sát) và một mức quyền: **Chỉ đọc** hoặc **Chỉnh sửa**.

  **Vai trò hệ thống nào được thêm vào Đội ngũ:** mọi vai trò hệ thống đều được, nhưng **Nhân viên và Quản lý Marketing chỉ được thêm với vai trò tham gia "Quan sát"** — khi đó họ áp cột (B) của bảng che mặt nạ theo BR-04.5b như mọi thành viên mức Chỉ đọc, và **vẫn không đọc được** ghi chú phạm vi "Nội bộ đội bán hàng" — lệnh cấm này neo vào **vai trò hệ thống Marketing** tại BR-36.1, không neo vào vai trò tham gia "Quan sát"; một thành viên không thuộc Marketing mang vai trò tham gia "Quan sát" **vẫn đọc được** ghi chú nội bộ của bản ghi đó. Lý do giới hạn riêng cho Marketing: đây là hai vai trò có tầm nhìn toàn tổ chức (Ghi chú 2 mục 5), nên nếu tư cách thành viên đội ngũ mở thêm quyền đọc nội dung thương lượng thì lệnh cấm tại BR-36.1 và sàn của `CFG-36-03` bị vô hiệu chỉ bằng thao tác thêm thành viên. Các vai trò khác — kể cả Nhân viên Hỗ trợ — được thêm với bất kỳ vai trò tham gia nào, và khi đó đọc ghi chú "Nội bộ đội bán hàng" **của riêng bản ghi đó** theo BR-36.1.
- `BR-35.2 (Quyền chia sẻ)`: Người phụ trách bản ghi và Quản lý Kinh doanh được thêm/bớt thành viên trong phạm vi của mình. Quản trị viên và Chủ sở hữu được thao tác trên mọi bản ghi. Thành viên có mức "Chỉ đọc" **không** được chia sẻ tiếp cho người khác.
- `BR-35.3 (Chia sẻ có thời hạn)`: Mỗi lượt chia sẻ được phép đặt ngày hết hiệu lực. Khi hết hạn, quyền tự động thu hồi và ghi nhận vào lịch sử bản ghi.
- `BR-35.3b (Luồng Yêu cầu quyền truy cập, Đề nghị chuyển giao & Đề nghị gộp) [Yêu cầu mới]`: **Ba hành động** tại BR-17.3 tạo ra một **bản ghi yêu cầu** có vòng đời riêng, không phải chỉ là một thông báo. Ba hành động sinh **bốn loại yêu cầu** vì hành động "Yêu cầu quyền truy cập" cho người dùng chọn xin quyền đọc hay xin quyền sửa:
  - **Nội dung bản ghi:** người yêu cầu, bản ghi khách hàng liên quan, loại yêu cầu (xin quyền đọc / xin quyền sửa / đề nghị chuyển giao / **đề nghị gộp**), lý do, thời điểm tạo, hạn xử lý, người xử lý, trạng thái (Chờ xử lý / Đã chấp thuận / Đã từ chối kèm lý do / Tự động chấp thuận do quá hạn — trạng thái cuối **chỉ áp dụng cho loại xin quyền đọc**).
  - **Người xử lý:** với loại xin quyền đọc/sửa và đề nghị chuyển giao — Người phụ trách hiện hữu, hoặc Quản lý Kinh doanh của họ sau khi leo thang. Với loại **đề nghị gộp** — Quản trị Chất lượng Dữ liệu hoặc Quản trị viên (BR-17.3).
  - **Cam kết thời gian và hành vi khi quá hạn:** theo BR-17.2c, khác nhau theo từng loại yêu cầu — mặc định 4 giờ làm việc và leo thang cho cả ba loại; riêng loại xin quyền đọc mới có hành vi tự cấp **quyền đọc tạm có ghi nhật ký** theo cơ chế BR-35.4. Hệ thống **không** tự cấp quyền sửa, **không** tự chuyển giao quyền phụ trách và **không** tự gộp bản ghi trong bất kỳ trường hợp nào.
  - **Kiểm toán:** mọi thay đổi trạng thái của bản ghi yêu cầu được ghi nhật ký theo NFR-07.
- `BR-35.4 (Quyền đọc tự động cho tuyến Hỗ trợ) [Yêu cầu mới]`: Khi một Vé hỗ trợ hoặc Hội thoại đa kênh **đang mở** được gắn với một khách hàng, nhân viên đang xử lý vé/hội thoại đó **tự động có quyền đọc** hồ sơ 360, Dòng thời gian và Ngữ cảnh Khách hàng của khách hàng đó trong suốt thời gian vé/hội thoại còn mở, kể cả khi bản ghi nằm ngoài phạm vi dữ liệu thông thường của họ. Quyền này: **(a)** là quyền **đọc**, không cho sửa dữ liệu nghiệp vụ — **ngoại lệ duy nhất** là hai thao tác của FEAT-33 mà tuyến Hỗ trợ thực thi được ngay khi tiếp nhận yêu cầu của khách: **gắn trạng thái `RESTRICTED`** (BR-30.6) và **hạ đồng thuận xuống `OPT_OUT`** (BR-30.10). Hai thao tác này được mở vì chúng **chỉ thu hẹp** phạm vi xử lý dữ liệu, **đảo lại được**, và là điều kiện để cam kết "Tức thì" tại bảng loại yêu cầu FEAT-33 có người thực thi: khách nói "đừng gửi tin cho tôi nữa" ngay trong hội thoại đang mở, mà người đang nói chuyện với khách lại không làm được gì thì cam kết đó chỉ có trên giấy. Ngoại lệ **chỉ có hiệu lực trong thời gian vé/hội thoại còn mở** như mọi quyền khác tại quy tắc này, và mỗi lượt đều ghi nhật ký theo NFR-07; **(b)** áp dụng **cột (C)** của bảng chính sách che mặt nạ tại BR-04.3 — kênh liên lạc được che một phần, đủ để xác minh đúng người và bấm gọi/gửi trong hệ thống theo BR-04.6, còn định danh KYC vẫn che hoàn toàn; **(c)** **bắt buộc ghi nhật ký truy cập** để phát hiện lạm dụng; **(d)** tự động hết hiệu lực khi vé/hội thoại đóng. Đây là cơ chế giải quyết trực tiếp bế tắc của FEAT-28 nêu trên, thay cho việc phải cấp "Xem toàn bộ" cho toàn bộ nhân viên.
- `BR-35.5 (Phụ thuộc tài liệu)`: Cơ chế thực thi phạm vi dữ liệu và thứ tự ưu tiên giữa quyền theo vai trò, quyền theo phạm vi tổ chức và quyền chia sẻ bản ghi thuộc phạm vi đặc tả của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md). Tài liệu này chỉ quy định nhu cầu nghiệp vụ và các mức quyền cần có; nguyên tắc chung là **quyền chia sẻ chỉ nới rộng, không bao giờ thu hẹp** quyền mà người dùng đã có theo vai trò.
- `BR-35.6 (Kiểm toán)`: Mọi thao tác chia sẻ, thu hồi chia sẻ và mọi lượt truy cập theo quyền tự động tại BR-35.4 được ghi nhật ký kiểm toán theo NFR-07.

---

### FEAT-36 — Ghi chú & Ghi nhận Hoạt động Khách hàng (Notes & Activity Logging) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Quản lý các ghi chú nội bộ và bản ghi hoạt động (cuộc gọi, cuộc họp, email đã gửi) gắn với khách hàng — nguồn dữ liệu chính của Dòng thời gian 360 độ.

**Actor:** Mọi người dùng có quyền xem bản ghi tương ứng.

**Bối cảnh nghiệp vụ:** Ghi chú được viện dẫn ở nhiều nơi trong tài liệu (nguồn sự kiện của FEAT-27, hành động nhanh tại BR-02.2, ghi chú ghim tại BR-28.1, bằng chứng liên hệ tại BR-31.7) nhưng trước phiên bản này chưa có đặc tả nào. Trong vận hành thật, ghi chú chứa nội dung thương mại nhạy cảm ("khách sẵn sàng trả tới 800 triệu", "đang so sánh với đối thủ X"), nên nếu không quy định rõ ai đọc được và ai xóa được thì nhân viên sẽ xóa lịch sử trước khi nghỉ việc, hoặc ngừng ghi chú thật — làm rỗng đúng giá trị cốt lõi mà FEAT-27 và `KPI-02` hướng tới.

**Quy tắc nghiệp vụ:**
- `BR-36.1 (Phân loại phạm vi đọc)`: Mỗi ghi chú có một trong **ba** phạm vi:
  - **Nội bộ đội bán hàng (mặc định chuẩn hệ thống):** Người phụ trách, **Đội ngũ phụ trách (FEAT-35) — theo tư cách thành viên, bất kể vai trò hệ thống của người đó**, Quản lý Kinh doanh của họ, Quản trị viên và Chủ sở hữu đọc được. **Nhân viên và Quản lý Marketing không đọc được — kể cả khi được thêm vào Đội ngũ phụ trách**, vì hai vai trò này chỉ tham gia đội ngũ với vai trò "Quan sát" theo BR-35.1.
  - **Chung:** mọi người có quyền xem bản ghi đều đọc được, bao gồm Marketing và tuyến Hỗ trợ.
  - **Giới hạn:** chỉ người tạo, Người phụ trách bản ghi và Quản lý trở lên.

  Phạm vi mặc định là tham số cấu hình theo tenant (Phụ lục B, `CFG-36-01`). Lý do chọn mặc định "Nội bộ đội bán hàng" thay vì "Chung": ghi chú chứa nội dung thương lượng nhạy cảm nhất ("khách sẵn sàng trả tới 800 triệu", "đang so sánh với đối thủ X"). Nếu mặc định để toàn tổ chức đọc được — nhất là khi Marketing có tầm nhìn toàn tổ chức — nhân viên sẽ ngừng ghi chú thật hoặc ghi vào sổ riêng, làm rỗng đúng giá trị mà FEAT-27 và `KPI-02` hướng tới. Marketing cần trường phân khúc, không cần nội dung thương lượng giá.
- `BR-36.2 (Sửa ghi chú)`: Người tạo được sửa nội dung ghi chú trong **24 giờ** đầu (tham số cấu hình, `CFG-36-02`). Sau thời hạn đó chỉ được **bổ sung** nội dung mới, không sửa nội dung cũ, nhằm bảo toàn tính tin cậy của lịch sử trao đổi.
- `BR-36.3 (Không xóa cứng) [sàn bắt buộc]`: Ghi chú và bản ghi hoạt động **không được xóa vĩnh viễn** bởi người dùng thường. Thao tác "Xóa" chỉ **ẩn** ghi chú khỏi dòng thời gian, giữ nguyên nội dung và ghi nhật ký kiểm toán người ẩn (NFR-07). Quản trị viên xem được ghi chú đã ẩn. Ngoại lệ duy nhất được xóa vĩnh viễn là khi thực thi quyền chủ thể dữ liệu theo BR-33.8.
- `BR-36.4 (Ghi chú ghim)`: Người phụ trách bản ghi và Quản lý trở lên được ghim tối đa **3 ghi chú** lên đầu hồ sơ; các ghi chú ghim này là nội dung trả về trong Ngữ cảnh Khách hàng 1 chạm (BR-28.1), **sau khi lọc theo phạm vi đọc của người xem** theo BR-36.7.
- `BR-36.7 (Ghi chú trong Ngữ cảnh Khách hàng 1 chạm) [Yêu cầu mới]`: Ngữ cảnh Khách hàng 1 chạm là cơ chế truy cập chính của **Nhân viên Hỗ trợ** (FEAT-28), nhưng phạm vi mặc định của ghi chú là "Nội bộ đội bán hàng" mà tuyến Hỗ trợ **không** đọc được — nếu không xử lý, panel sẽ luôn rỗng phần ghi chú với đúng đối tượng nó phục vụ. Quy tắc:
  - Ngữ cảnh 1 chạm **lọc ghi chú theo phạm vi đọc của người xem** (BR-36.1), không trả về ghi chú ngoài phạm vi của họ.
  - Người ghim ghi chú được chọn **"Cho phép tuyến Hỗ trợ đọc"** trên từng ghi chú ghim — nhằm chia sẻ đúng thông tin cần thiết cho việc phục vụ (ví dụ "khách đang chờ xử lý khiếu nại lô hàng tháng 8") mà không mở toàn bộ nội dung thương lượng giá.
  - Phạm vi ghi chú mà tuyến Hỗ trợ đọc được là tham số cấu hình theo tenant (Phụ lục B, `CFG-36-03`), mặc định: phạm vi "Chung" cộng các ghi chú ghim đã được đánh dấu cho phép.
- `BR-36.5 (Bản ghi hoạt động tự động)`: Các hoạt động phát sinh trong hệ thống (cuộc gọi đã thực hiện kèm thời lượng, email đã gửi, tin nhắn đã gửi, cuộc hẹn đã tạo) được tự động ghi nhận thành bản ghi hoạt động và **không cho sửa nội dung**. Đây là nguồn sinh **bằng chứng liên hệ nhóm 1** theo BR-31.8; bằng chứng nhóm 2 (liên hệ ngoài hệ thống có Quản lý xác nhận) cũng được ghi nhận thành bản ghi hoạt động nhưng đánh dấu rõ là do người dùng khai báo, kèm người xác nhận.
- `BR-36.6 (Phạm vi dữ liệu cá nhân)`: Nội dung ghi chú và hoạt động thuộc phạm vi phải xử lý khi thực thi quyền xóa của chủ thể dữ liệu (BR-33.8).

---

## 4. Yêu cầu phi chức năng

### 4.1 Hiệu năng & Khả năng đáp ứng (Performance)
**Điều kiện đo chung cho toàn bộ nhóm NFR hiệu năng.** Mọi ngưỡng dưới đây được đo tại **biên dịch vụ phía máy chủ** (không tính thời gian dựng giao diện trên trình duyệt), trên **môi trường nghiệm thu có cấu hình tương đương môi trường sản xuất**, với **50 người dùng đồng thời**, bằng công cụ đo tải do Trưởng nhóm Kiểm thử chỉ định, và trên tập dữ liệu mẫu chuẩn: **1.000.000 Contact, 100.000 Account, và hồ sơ dùng để đo dòng thời gian có 500 sự kiện** (thêm một hồ sơ cực biên 10.000 sự kiện để đo riêng, báo cáo tách biệt). Mỗi ngưỡng phải đạt trong **3 lần đo liên tiếp**.

- **NFR-01 (Thời gian tìm kiếm & lọc danh bạ):** Tìm kiếm khách hàng theo tên, email, SĐT hoặc lọc theo danh sách phản hồi dưới **300ms** (p95).
- **NFR-02 (Thời gian tải Dòng thời gian 360 độ & Ngữ cảnh Khách hàng):** Dòng thời gian 360 độ và Ngữ cảnh Khách hàng phản hồi dưới **150ms (p95)** — đây là **ngưỡng nghiệm thu duy nhất** cho cả hai chức năng. Mức 50ms (p50) nêu tại BR-28.2 là mục tiêu tối ưu, không phải tiêu chí nghiệm thu.
- **NFR-03 (Tốc độ xử lý Nhập khẩu dữ liệu):** Tiến trình nhập khẩu xử lý tối thiểu **1,000 dòng/giây** đối với tệp dung lượng lớn.

### 4.2 Độ tin cậy & Toàn vẹn Dữ liệu (Reliability & Data Integrity)
- **NFR-04 (Giao dịch Gộp & Hoàn tác nguyên tử):** Thao tác Gộp (Merge) và Hoàn tác gộp (Unmerge) phải thực thi như **một giao dịch nguyên tử duy nhất**. Nếu có lỗi ở bất kỳ bước chuyển giao nào, toàn bộ giao dịch phải được hoàn tác 100%, không để lại trạng thái dang dở.
- **NFR-05 (Bảo toàn Sổ cái Gộp):** Bản ghi sổ cái gộp được lưu trữ vĩnh viễn và không bị xóa kể cả khi bản ghi chính bị xóa mềm.

### 4.3 An toàn & Bảo mật (Security)
- **NFR-06 (Bảo vệ dữ liệu nhạy cảm FLS):** Áp dụng nghiêm ngặt chính sách bảo mật cấp trường (Field-Level Security) **đúng theo chính sách che mặt nạ tại FEAT-04** — mức hiển thị của mỗi trường được quyết định bởi nhóm trường (BR-04.1) và quan hệ của người xem với bản ghi (BR-04.3), không phải bởi một quy tắc riêng ở mục này. Yêu cầu phi chức năng ở đây là: chính sách phải được thực thi **ở tầng dữ liệu trả về**, sao cho dữ liệu vượt mức hiển thị cho phép **không bao giờ rời khỏi hệ thống** kể cả khi giao diện bị can thiệp; và mọi lượt nâng mức hiển thị (BR-04.4) đều phải để lại dấu vết theo NFR-07.
- **NFR-07 (Nhật ký kiểm toán truy cập):** Các thao tác sau bắt buộc được ghi nhật ký kiểm toán: Mở khóa mặt nạ (BR-04.4), **Hành động liên lạc trong hệ thống** (BR-04.6), Xuất dữ liệu, Gộp bản ghi, Hoàn tác Gộp, Xóa bản ghi, Khôi phục từ Thùng rác, Hoàn tác Chuyển đổi Lead (BR-14.2), Thay đổi Quy tắc Chấm điểm (BR-15.4), **Chuyển giao Quyền phụ trách** (BR-34.7), **Chia sẻ bản ghi và truy cập theo quyền đọc tạm** (BR-35.6), **Đọc hồ sơ khách hàng nằm ngoài phạm vi dữ liệu được gán bởi vai trò có tầm nhìn toàn tổ chức** — hiện là Nhân viên và Quản lý Marketing theo Ghi chú 2 mục 5 (`CFG-05-02`); đây là sự kiện chống lưng cho cam kết "chỉ đọc **có ghi nhật ký**" tại vấn đề #5 mục 7. Sự kiện áp cho **các vai trò nghiệp vụ có tầm nhìn vượt phạm vi gán** — hiện là hai vai trò Marketing; **Quản trị viên và Chủ sở hữu không thuộc phạm vi sự kiện này** vì mọi thao tác của họ đã được phủ bởi các sự kiện khác trong danh mục và bởi NFR-14. Khối lượng nhật ký sinh ra ở đây **được chấp nhận có chủ đích**: đó là cái giá của việc cấp tầm nhìn toàn tổ chức cho một vai trò không phụ trách bản ghi nào, và là căn cứ duy nhất trả lời được câu hỏi ai đã đọc hồ sơ của một khách hàng khi có khiếu nại, **Ẩn ghi chú** (BR-36.3), **Nâng mức đồng thuận từ mọi nguồn tác động** (BR-30.10), **Sửa dữ liệu nguồn gốc theo lô** (BR-32.3b), **Đọc nhật ký kiểm toán ở cả hai mức có quyền** (NFR-14), **Mọi thay đổi trạng thái của bản ghi yêu cầu** xin quyền đọc/sửa, đề nghị chuyển giao, đề nghị gộp (BR-35.3b), **Bỏ qua cảnh báo trùng lặp** khi tenant cấu hình mức "chỉ cảnh báo" (BR-17.2), **Xác nhận "Đây là người khác dùng chung định danh này"** tại màn hình cảnh báo trùng (BR-17.2), **Gắn nhãn Định danh dùng chung cho lô nhập khẩu** chọn "Tạo bản ghi mới dù trùng" (BR-23.3), **Khai báo và xác nhận bằng chứng liên hệ nhóm 2** — liên hệ ngoài hệ thống có Quản lý xác nhận (BR-31.8), **Đánh dấu, dỡ dấu và duyệt "Lead rác"** (BR-12.4b), **Phê duyệt và thu hồi phê duyệt Chiến dịch Win-Back** (BR-12.5b), **Dỡ sớm biện pháp phòng ngừa** khi không xác minh được chủ thể dữ liệu (BR-33.7b), Thay đổi tham số cấu hình (Phụ lục B) và Xử lý Yêu cầu Chủ thể Dữ liệu (FEAT-33). Danh sách này là **nguồn chân lý duy nhất** để dựng danh mục sự kiện kiểm toán: một quy tắc viện dẫn NFR-07 mà thao tác của nó không có trong danh sách này là lỗi tài liệu.

  Mỗi bản ghi nhật ký lưu: người thực hiện, thời điểm, bản ghi bị tác động, loại thao tác, và **tên các trường bị tác động**.

  **Giới hạn nội dung — sàn bắt buộc:** Nhật ký **không được lưu giá trị thật của các trường nhạy cảm** thuộc 3 nhóm tại BR-04.1. Với thao tác mở khóa mặt nạ, nhật ký chỉ ghi *"đã mở khóa trường Số điện thoại của bản ghi X"*, **không** ghi chính số điện thoại đó. Lý do: nếu lưu giá trị thật, nhật ký trở thành kho dữ liệu cá nhân lớn nhất của phân hệ và biến thành đường đi vòng qua chính sách che mặt nạ — người xem được nhật ký sẽ đọc được giá trị mà chính họ không có quyền mở khóa. Với các thao tác thay đổi dữ liệu không nhạy cảm (giai đoạn vòng đời, người phụ trách, thẻ), nhật ký được lưu trạng thái trước/sau để phục vụ tra soát.
- **NFR-08 (Thời hạn lưu & Quyền đọc nhật ký kiểm toán):** Nhật ký được lưu tối thiểu **24 tháng** (gói tiêu chuẩn) và **60 tháng** (gói Enterprise). Trong thời hạn này nhật ký **không cho phép sửa hoặc xóa từng bản ghi** kể cả bởi Chủ sở hữu Workspace — ngoại lệ duy nhất là thao tác **khử định danh** khi thực thi quyền chủ thể dữ liệu theo BR-33.8, và ngoại lệ này phải do Quản trị viên thực hiện cùng Người phụ trách Bảo vệ Dữ liệu, để lại chính một bản ghi nhật ký về việc khử định danh đó.
- **NFR-14 (Kiểm soát truy cập Nhật ký kiểm toán) [Yêu cầu mới — sàn bắt buộc]:** Nhật ký kiểm toán có **ba mức truy cập**, không phải một:

| Mức | Ai được cấp | Phạm vi đọc |
| --- | --- | --- |
| **Toàn phần** | Chủ sở hữu Workspace và Người phụ trách Bảo vệ Dữ liệu | Toàn bộ nhật ký, truy vấn theo phạm vi thời gian và bản ghi |
| **Theo bản ghi đang xử lý (tối thiểu-cần-biết)** | Quản trị viên Workspace, **chỉ trong phạm vi một bản ghi đang có yêu cầu chủ thể dữ liệu hoặc thao tác gộp/khôi phục đang thực hiện** | Chỉ nhật ký của đúng bản ghi đó, chỉ trong thời gian yêu cầu còn mở. Đây là mức tối thiểu để thực hiện được nghĩa vụ khử định danh tại BR-33.8 và tra soát khi hoàn tác gộp |
| **Không truy cập** | Mọi vai trò nghiệp vụ khác (Quản lý Kinh doanh, Marketing, Nhân viên, Nhân viên Hỗ trợ) | — |

  **Mọi lượt đọc nhật ký, ở cả hai mức có quyền, đều phải được ghi nhật ký** (nhật ký của nhật ký), nhằm phát hiện việc dùng nhật ký để khai thác dữ liệu. Không hỗ trợ xuất toàn bộ nhật ký ra tệp trừ khi có phê duyệt kép theo quy tắc dưới.

  **Quy tắc phê duyệt kép khi tenant không chỉ định Người phụ trách Bảo vệ Dữ liệu:** Theo mục 2.2, khi không có DPO thì trách nhiệm thuộc Chủ sở hữu — nếu áp nguyên văn "Chủ sở hữu và DPO cùng phê duyệt" thì hai người sụp về một, làm mất kiểm soát kép. Trong trường hợp đó, người thứ hai là **một Quản trị viên Workspace khác, không phải người đang thực hiện thao tác**. Quy tắc thay thế này áp dụng cho mọi chỗ tài liệu yêu cầu "hai người khác nhau" hoặc "phê duyệt kép" (BR-33.7, BR-33.8, NFR-08, NFR-14).

### 4.4 Khả dụng, Sao lưu & Phục hồi (Availability & Disaster Recovery)
- **NFR-09 (Mức độ khả dụng):** Phân hệ Contacts & Accounts cam kết mức khả dụng tối thiểu **99,9% / tháng** (không tính thời gian bảo trì có thông báo trước tối thiểu 48 giờ).
- **NFR-10 (Sao lưu & Phục hồi thảm họa):** Dữ liệu khách hàng được sao lưu định kỳ với **mức mất dữ liệu tối đa cho phép (RPO) là 15 phút** và **thời gian phục hồi mục tiêu (RTO) là 4 giờ**. Do đây là dữ liệu tài sản kinh doanh cốt lõi, quy trình phục hồi phải được diễn tập kiểm chứng tối thiểu **2 lần/năm**. Bản sao lưu được lưu theo cơ chế **cuốn vòng trong 35 ngày** — đây là con số mà bảng phạm vi xóa tại BR-33.8 dựa vào để cam kết thời điểm dữ liệu đã xóa biến mất khỏi mọi bản sao lưu, nên hai nơi phải giữ cùng một giá trị.

### 4.5 Khả năng mở rộng & Giới hạn dung lượng (Scalability & Limits)
- **NFR-11 (Giới hạn theo gói dịch vụ):** Hệ thống áp dụng và hiển thị rõ các giới hạn sau, mọi con số đều có giá trị cụ thể để QA nghiệm thu được:

| Giới hạn | Gói tiêu chuẩn | Gói Enterprise |
| --- | --- | --- |
| Số Contact tối đa mỗi không gian làm việc | **500.000** | 5.000.000 |
| Số Account tối đa mỗi không gian làm việc | **50.000** | 500.000 |
| Số liên kết doanh nghiệp tối đa trên một Contact | **20** | 50 |
| Số thẻ phân loại tối đa trên một bản ghi | **50** | 100 |
| Số bản ghi tối đa mỗi lần xuất dữ liệu | **50.000** | 200.000 |
| Số thành viên tối đa trong một Đội ngũ phụ trách (BR-35.1) | **10** | 25 |

  Khi đạt **80%** giới hạn, hệ thống cảnh báo cho Chủ sở hữu Workspace để nâng gói. Trường hợp vượt giới hạn do thao tác gộp bản ghi được xử lý theo BR-19.10 (không chặn gộp).

### 4.6 Đa ngôn ngữ & Khả năng tiếp cận (Internationalization & Accessibility)
- **NFR-12 (Đa ngôn ngữ & hướng hiển thị):** Toàn bộ giao diện và thông báo nghiệp vụ của phân hệ hỗ trợ tối thiểu 3 ngôn ngữ: **Tiếng Việt, Tiếng Anh, Tiếng Ả Rập**; riêng Tiếng Ả Rập bắt buộc hỗ trợ bố cục hiển thị từ phải sang trái (Right-to-Left). Tên riêng của khách hàng phải hiển thị đúng dấu và đúng ký tự gốc, không bị chuyển tự tự động.
- **NFR-13 (Định dạng theo vùng):** Số điện thoại, ngày tháng, đơn vị tiền tệ và múi giờ được hiển thị theo thiết lập vùng của từng không gian làm việc; dữ liệu lưu trữ luôn dùng chuẩn quốc tế thống nhất để bảo đảm tính nhất quán khi báo cáo đa vùng.

---

## 5. Ma trận quyền truy cập tính năng

| Mã FEAT | Tên tính năng nghiệp vụ | Nhân viên (Sales Rep) | Nhân viên Hỗ trợ | Quản lý (Sales Mgr) | Nhân viên Marketing (MS) | Quản lý Marketing (MM) | Quản trị viên (Admin) | Chủ sở hữu (Owner) |
| --- | --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `FEAT-01` | Tạo & Quản lý Contact | Scope gán | Scope gán | Scope phòng ban | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** |
| `FEAT-02` | Xem Hồ sơ Chi tiết 360 | Scope gán | Scope gán + BR-35.4 | Scope phòng ban | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** |
| `FEAT-03` | Gắn nhãn Thẻ hàng loạt | Scope gán | Scope gán | Scope phòng ban | **Cho phép** | **Cho phép** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-04` | Mở khóa Mặt nạ Dữ liệu | Có quyền `contacts:unmask`* | Có quyền `contacts:unmask`* | Có quyền `contacts:unmask`* | Có quyền `contacts:unmask`* | Có quyền `contacts:unmask`* | **Toàn quyền** | **Toàn quyền** |
| `FEAT-05` | Thùng rác & Phục hồi Contact | — | — | Có quyền `delete` để khôi phục bản ghi (BR-05.3), scope phòng ban, chịu chốt an toàn BR-05.6 | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-06` | Tạo & Quản lý Account | Scope gán | Scope gán | Scope phòng ban | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** |
| `FEAT-07` | Cây Doanh nghiệp Mẹ - Con | Scope gán | Scope gán | Scope phòng ban | Xem cấu trúc, không xem chỉ số tài chính hợp nhất (BR-07.4c) | Xem cấu trúc, không xem chỉ số tài chính hợp nhất (BR-07.4c) | **Toàn quyền** | **Toàn quyền** |
| `FEAT-08` | Xem Chi tiết Doanh nghiệp | Scope gán | Scope gán | Scope phòng ban | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** |
| `FEAT-09` | Thùng rác & Phục hồi Account | — | — | Có quyền `delete`, scope phòng ban (BR-09.2) | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-10` | Quan hệ Đa Doanh nghiệp | Scope gán | Scope gán + BR-35.4 (panel liên kết của hồ sơ 360 theo BR-02.1) | Scope phòng ban | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** |
| `FEAT-11` | Quan hệ Giữa các Cá nhân | Scope gán | Scope gán + BR-35.4 (panel liên kết của hồ sơ 360 theo BR-02.1) | Scope phòng ban | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** |
| `FEAT-12` | Quản trị Vòng đời & Ma trận Chuyển đổi | Scope gán, chỉ bước tiến lên (nguyên tắc 2; `→ SQL` cần thẩm định theo BR-15.6); được **đánh dấu "Lead rác"** chờ Quản lý duyệt (BR-12.4b) | Chỉ xem giai đoạn hiện tại trong Ngữ cảnh Khách hàng (BR-28.1); không chuyển giai đoạn (BR-02.2) | Scope phòng ban, gồm bước lùi (BR-12.7), `Disqualified` (BR-12.4, BR-12.4b), **`→ Nurturing`** theo nhánh điểm nguội (BR-16.4 — nhánh toàn bộ Cơ hội `Closed Lost` do hệ thống tự chuyển theo BR-12.3, không cần thẩm quyền), **`Nurturing → Lead`** (BR-16.5), **mở lại bản ghi `Disqualified`** về `Lead`/`Nurturing` kèm lý do từ A.17 (ma trận FEAT-12), và **đồng phê duyệt Chiến dịch Win-Back** mở đường `Churned → Nurturing` (BR-12.5b) | **Không** có quyền chuyển giai đoạn thủ công (Ghi chú 2, BR-02.2) — chỉ xem trạng thái; giai đoạn vẫn tiến lên qua đường tự động theo ngưỡng điểm (BR-15.5) | Cấu hình vòng đời tự động (BR-15.4, BR-15.5); **không** chuyển giai đoạn thủ công (Ghi chú 2, BR-02.2), không có quyền bước lùi (BR-12.7) hay `Disqualified` (BR-12.4); có **đồng phê duyệt Chiến dịch Win-Back** (BR-12.5b) | **Toàn quyền** (gồm ngoại lệ gian lận BR-12.8) | **Toàn quyền** (gồm ngoại lệ gian lận BR-12.8) |
| `FEAT-13` | Xem Lịch sử Giai đoạn | Scope gán | Scope gán | Scope phòng ban | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** |
| `FEAT-14` | Chuyển đổi Lead 1-Click | Scope gán | — | Scope phòng ban | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-15` | Động cơ Chấm điểm Lead | Xem điểm, không sửa quy tắc (BR-15.4) | Xem điểm, không sửa quy tắc (BR-15.4) | Xem điểm, không sửa quy tắc (BR-15.4) | Xem cấu hình, không sửa (BR-15.4) | Cấu hình quy tắc (BR-15.4) | **Cấu hình quy tắc** (BR-15.4) | **Cấu hình quy tắc** (BR-15.4) |
| `FEAT-16` | Suy giảm Điểm Tiềm năng | *Hệ thống* (BR-16.1, Ghi chú 3) | *Hệ thống* (Ghi chú 3) | *Hệ thống* (Ghi chú 3) | *Hệ thống* (Ghi chú 3) | *Hệ thống* (Ghi chú 3) | *Hệ thống* (Ghi chú 3) | *Hệ thống* (Ghi chú 3) |
| `FEAT-17` | Kiểm tra Trùng lặp | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-18` | Xem trước Tác động Gộp | — | — | Có quyền `delete` | — | — | **Cho phép** | **Cho phép** |
| `FEAT-19` | Thực thi Gộp Bản ghi | — | — | Có quyền `delete` | — | — | **Cho phép** | **Cho phép** |
| `FEAT-20` | Hoàn tác Gộp (Unmerge) | — | — | — | — | — | **Cho phép** | **Cho phép** |
| `FEAT-21` | Khôi phục Gộp Lỗi | — | — | — | — | — | **Cho phép** | **Cho phép** |
| `FEAT-22` | Tải tệp Nhập khẩu Excel | — | — | Có quyền `import`| Có quyền `import` | Có quyền `import` | **Cho phép** | **Cho phép** |
| `FEAT-23` | Trợ lý Ánh xạ Cột | — | — | Có quyền `import`| Có quyền `import` | Có quyền `import` | **Cho phép** | **Cho phép** |
| `FEAT-24` | Nhập khẩu & Báo cáo Lỗi | — | — | Có quyền `import`| Có quyền `import` | Có quyền `import` | **Cho phép** | **Cho phép** |
| `FEAT-25` | Xuất Dữ liệu CSV qua Token | Scope gán, chịu hạn mức ngày và **không** xuất được Định danh KYC (BR-25.5) | — | Có quyền `export`, **không** xuất được Định danh KYC; duyệt lần xuất vượt hạn mức của Nhân viên Kinh doanh (BR-25.4) | Có quyền `export`, **không** xuất được Định danh KYC (BR-25.4) | Có quyền `export` + duyệt xuất lớn cho Nhân viên Marketing; **không** xuất được Định danh KYC (BR-25.4) | **Cho phép**, gồm xuất Định danh KYC khi có DPO đồng phê duyệt (BR-25.4) | **Cho phép**, gồm xuất Định danh KYC khi có DPO đồng phê duyệt; **duyệt xuất lớn cho Quản trị viên và các vai trò quản lý** (BR-25.4) |
| `FEAT-26` | Danh sách Hiển thị Dùng chung| **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Toàn quyền** | **Toàn quyền** |
| `FEAT-27` | Dòng thời gian 360 độ | Scope gán | Scope gán + BR-35.4 | Scope phòng ban | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** |
| `FEAT-28` | Customer Context cho Omni | Scope gán | **Scope gán + BR-35.4** (cơ chế truy cập chính) | Scope phòng ban | — | — | **Toàn quyền** | **Toàn quyền** |
| `FEAT-29` | Quản lý Danh tính Đa kênh | Scope gán | Scope gán + BR-35.4 | Scope phòng ban | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** |
| `FEAT-30` | Quản lý Đồng thuận & Shared | Scope gán | Scope gán, **cộng quyền hạ đồng thuận trên bản ghi đang có vé/hội thoại mở** (BR-35.4a) | Scope phòng ban | **Cho phép** | **Cho phép** trên toàn tổ chức; **không** đổi được `CFG-30-01`, `CFG-30-02` (thuộc Chủ sở hữu + DPO) | **Toàn quyền** | **Toàn quyền** |
| `FEAT-31` | Phân bổ Lead Tự động (Routing) | — | — | **Cho phép** (cấu hình quy tắc theo BR-31.5; thực thi là *Hệ thống* — Ghi chú 3) | — | — | **Cho phép** | **Cho phép** |
| `FEAT-32` | Theo dõi Nguồn gốc UTM | Xem trường trên hồ sơ (BR-32.1) | Xem trường trên hồ sơ (BR-32.1) | Xem trường trên hồ sơ (BR-32.1) | Xem báo cáo (BR-32.4) | Xem báo cáo + ROI (BR-32.4) | **Toàn quyền** | **Toàn quyền** |
| `FEAT-33` | Quyền Chủ thể Dữ liệu | — | Tiếp nhận, ghi nhận; **gắn được `RESTRICTED`** (BR-30.6) và **hạ được đồng thuận xuống `OPT_OUT`** (BR-30.10) trên bản ghi trong Scope gán **hoặc** đang có vé/hội thoại mở (ngoại lệ tại BR-35.4a); **không** thực thi ba loại yêu cầu còn lại (BR-33.2, BR-33.7 — Ghi chú 5) | Tiếp nhận, ghi nhận; **gắn được `RESTRICTED`** (BR-30.6) và **hạ được đồng thuận xuống `OPT_OUT`** (BR-30.10) trong Scope phòng ban; **không** thực thi ba loại yêu cầu còn lại (BR-33.2, BR-33.7 — Ghi chú 5) | — | — | **Cho phép** (xử lý & xóa vĩnh viễn) | **Cho phép** (xử lý & xóa vĩnh viễn) |
| `FEAT-34` | Chuyển giao Quyền phụ trách | Bàn giao ngang cho đồng nghiệp cùng nhóm trên bản ghi mình phụ trách, có hiệu lực khi người nhận chấp nhận (BR-34.1b); tự khai báo nghỉ phép và người xử lý thay (BR-34.6) | **Tự khai báo nghỉ phép** (BR-34.6); bàn giao ngang trên bản ghi mình phụ trách nếu có (BR-34.1b) | **Cho phép** (scope phòng ban; chốt bàn giao BR-34.4) | **Tự khai báo nghỉ phép** (BR-34.6); bàn giao ngang trên bản ghi mình phụ trách nếu có (BR-34.1b) | **Tự khai báo nghỉ phép** (BR-34.6); bàn giao ngang trên bản ghi mình phụ trách nếu có (BR-34.1b) | **Toàn quyền** | **Toàn quyền** |
| `FEAT-35` | Chia sẻ Bản ghi & Đội ngũ | Bản ghi mình phụ trách (BR-35.2) | Nhận quyền đọc tự động (BR-35.4); chia sẻ được bản ghi mình phụ trách nếu có (BR-35.2) | **Cho phép** (scope phòng ban) | Chia sẻ được bản ghi mình phụ trách nếu có (BR-35.2); được thêm vào Đội ngũ với vai trò "Quan sát" (BR-35.1) | Chia sẻ được bản ghi mình phụ trách nếu có (BR-35.2); được thêm vào Đội ngũ với vai trò "Quan sát" (BR-35.1) | **Toàn quyền** | **Toàn quyền** |
| `FEAT-36` | Ghi chú & Ghi nhận Hoạt động | Scope gán | Scope gán + BR-35.4; **theo vai trò chỉ đọc được ghi chú phạm vi "Chung" và ghi chú ghim đã mở cho tuyến Hỗ trợ** (BR-36.7); đọc thêm ghi chú "Nội bộ đội bán hàng" của riêng bản ghi mà họ là thành viên Đội ngũ phụ trách (BR-36.1) | Scope phòng ban | Chỉ ghi chú phạm vi "Chung" (BR-36.1) | Chỉ ghi chú phạm vi "Chung" (BR-36.1) | **Toàn quyền** | **Toàn quyền** |

*\*Ghi chú 1: Quyền `unmask` yêu cầu vai trò được cấp quyền chuyên biệt `contacts:unmask`, không mặc định theo vai trò.*
*Ghi chú 2: "Xem toàn bộ" (Marketing) khác với "Scope gán/Scope phòng ban" (Sales) — Marketing cần tầm nhìn toàn tổ chức để phân khúc chiến dịch, nhưng không có quyền chỉnh sửa trừ khi ghi rõ **Cho phép**/**Toàn quyền**.*
*Ghi chú 3 — ký hiệu "*Hệ thống*" và quyền cấu hình: `FEAT-16` (suy giảm điểm) và `FEAT-31` (phân bổ Lead) do **Tiến trình Hệ thống** thực thi tự động, **không vai trò nào thực thi bằng thao tác tay** — đó là nghĩa của ký hiệu "*Hệ thống*" trong ô. Với `FEAT-31`, cột trong ma trận chỉ thể hiện quyền **cấu hình quy tắc** phân bổ (Round-robin/Territory/Industry theo BR-31.5), không phải quyền thực thi. Với `FEAT-16`, quyền cấu hình các mốc và tỷ lệ suy giảm nằm ở `CFG-16-01` (Quản lý Marketing), không nằm trong ma trận này.*
*Ghi chú 4: Ba vai trò chức năng **Quản lý Khách hàng Hiện hữu (Account Manager)**, **Quản trị Chất lượng Dữ liệu (Data Steward)** và **Người phụ trách Bảo vệ Dữ liệu (DPO)** không có cột riêng trong ma trận vì không phải vai trò hệ thống độc lập — quyền hạn của họ áp dụng theo vai trò gốc được cấp (Sales Rep / Sales Manager / Admin) theo định nghĩa tại mục 2.2. Vì vậy Mục 2.2 định nghĩa **11 actor**: 7 vai trò hệ thống (có cột riêng trong ma trận), 3 vai trò chức năng (không có cột riêng, quyền theo vai trò gốc) và Tiến trình Hệ thống (thể hiện qua ô "*Hệ thống*" trong các dòng tự động).*
*Ghi chú 5: `FEAT-33` — Nhân viên Hỗ trợ và Quản lý Kinh doanh được **tiếp nhận, ghi nhận** yêu cầu của khách hàng vào hệ thống theo dõi, và được **gắn trạng thái `RESTRICTED`** ngay tại bước tiếp nhận đối với yêu cầu hạn chế xử lý. Ngoại lệ này có lý do dứt khoát: `RESTRICTED` là thao tác **chỉ thu hẹp** phạm vi xử lý dữ liệu và **đảo lại được**, trong khi bảng loại yêu cầu tại FEAT-33 cam kết thực thi "Tức thì" — nếu phải chờ Quản trị viên thì cam kết đó không có người thực thi. **Ngoại lệ thứ hai — rút lại đồng thuận:** hai vai trò này cũng **hạ được** trạng thái đồng thuận xuống `OPT_OUT` ngay khi tiếp nhận, đúng theo BR-30.10 vốn cho phép mọi vai trò nghiệp vụ có quyền ghi trên bản ghi hạ mức đồng thuận tự do — chỉ việc **nâng** mức mới bị cưỡng chế, vì hạ mức luôn là hướng an toàn hơn cho chủ thể dữ liệu. Đây là điều kiện để bảng loại yêu cầu tại FEAT-33 giữ được cam kết "Tức thì" cho loại yêu cầu này. Việc **đóng** bản ghi yêu cầu và phát hành phản hồi chính thức cho khách vẫn thuộc Quản trị viên. **Ba loại yêu cầu còn lại** trong bảng năm loại tại FEAT-33 — bản sao dữ liệu, chỉnh sửa, xóa vĩnh viễn — **chỉ** thuộc Quản trị viên và Chủ sở hữu, tuân thủ BR-33.2, BR-33.7 (xác minh danh tính + hai người khác nhau thực hiện). Việc gắn `RESTRICTED` được ghi nhật ký như mọi thao tác xử lý yêu cầu chủ thể dữ liệu (NFR-07).*
*Ghi chú 6: Ký hiệu **"+ BR-35.4"** nghĩa là ngoài phạm vi dữ liệu thông thường, vai trò đó còn nhận **quyền đọc tự động có ghi nhật ký** đối với khách hàng đang có Vé hỗ trợ hoặc Hội thoại mở mà mình đang xử lý. Đây là cơ chế truy cập chính của tuyến Hỗ trợ, vì quyền phụ trách bản ghi thường thuộc đội kinh doanh theo BR-01.3.*
*Ghi chú 7: Toàn bộ các ô trong ma trận là **giá trị mặc định chuẩn hệ thống**; tenant được cấu hình lại theo Phụ lục B (`CFG-05-02`), trừ các ràng buộc được đánh dấu "sàn bắt buộc" trong các quy tắc nghiệp vụ.*

*Ghi chú 8 — **từ vựng chuẩn của ma trận** (định nghĩa một lần, dùng cho mọi ô):*

| Giá trị trong ô | Nghĩa | Quy tắc nguồn |
| --- | --- | --- |
| **Scope gán** | Chỉ các bản ghi mà người dùng là Người phụ trách, cộng các bản ghi được chia sẻ tới họ | BR-01.4, BR-35.2 |
| **Scope phòng ban** | Toàn bộ bản ghi thuộc Đơn vị tổ chức của người dùng và các đơn vị cấp dưới | BR-01.4 |
| **Xem toàn bộ** | Đọc toàn tổ chức, **không** kèm quyền sửa (xem Ghi chú 2) | BR-01.4, `CFG-05-02` |
| **Có quyền `<tên quyền>`** | Chỉ dùng được khi vai trò được cấp đúng quyền chuyên biệt đó; không mặc định theo vai trò | Ghi chú 1 |
| **Cho phép** / **Toàn quyền** | Có quyền thực hiện; "Toàn quyền" gồm cả cấu hình và các ngoại lệ nêu trong ô | — |
| **—** | Không có quyền | — |

*Bốn giá trị đầu là **từ vựng chuẩn**: ô chỉ dùng từ vựng chuẩn thì **không** cần dẫn chiếu mã BR, vì bảng này đã là nguồn chân lý cho chúng. Nghĩa vụ dẫn chiếu mã BR tại quy ước đọc mục 3 áp dụng cho **phần điều kiện vượt ra ngoài từ vựng chuẩn** trong một ô — ví dụ "chỉ bước tiến lên", "gồm bước lùi", "+ duyệt xuất lớn", "không xem chỉ số tài chính hợp nhất".*

---

## 6. Kịch bản chấp nhận tổng hợp (UAT)

**Quy ước thứ tự kịch bản.** Số hiệu kịch bản được cấp theo **thời điểm bổ sung**, không theo thứ tự trình bày; kịch bản mới được đặt cạnh kịch bản có chủ đề gần nhất để người kiểm thử chạy theo cụm. Vì vậy có thể gặp Kịch bản 21 đứng giữa Kịch bản 14 và 15. Mọi tham chiếu tới kịch bản được giải theo **số hiệu**, không theo vị trí.

**Quy ước nghiệm thu các quy tắc theo mốc thời gian dài.** Nhiều quy tắc trong tài liệu có mốc tính bằng tuần, tháng hoặc năm (thùng rác 30 ngày, hoàn tác gộp 90 ngày, hồ sơ tạm 90 ngày và trần 18 tháng, định danh KYC 24 tháng, dữ liệu không hoạt động 36 tháng). Các mốc này **không thể chờ đủ thời gian thực** trong một chu kỳ phát hành, nên được nghiệm thu theo một trong hai cách, và bằng chứng của cả hai đều được coi là hợp lệ cho Điều kiện nghiệm thu #1 tại mục 10:

1. **Môi trường nghiệm thu được phép đặt tham số dưới sàn sản xuất — trừ Kịch bản 22.** Trên môi trường phi sản xuất, miền giá trị của các tham số **thời gian** tại Phụ lục B được mở rộng xuống mức phút/giờ để chạy được kịch bản mô phỏng thời gian trôi qua. **Ngoại lệ tuyệt đối:** quy ước nới này **không áp dụng cho Kịch bản 22**, vốn tồn tại để chứng minh chính việc sàn không thể bị vi phạm; Kịch bản 22 **bắt buộc chạy trên môi trường mang cấu hình sản xuất**, nơi toàn bộ miền giá trị và sàn của Phụ lục B có hiệu lực đầy đủ. Nếu không tách bạch, hai yêu cầu sẽ đánh nhau: một bên đòi môi trường nghiệm thu chấp nhận giá trị dưới sàn, bên kia đòi chứng minh không đặt được giá trị dưới sàn.
2. **Cơ chế tua thời gian của môi trường nghiệm thu**, nếu môi trường hỗ trợ.

Người kiểm thử ghi rõ trong biên bản đã dùng cách nào và giá trị tham số đã đặt.

### Kịch bản 1: Tạo mới Khách hàng Cá nhân & Tra cứu Hồ sơ 360 Độ
1. Nhân viên kinh doanh bấm "Thêm khách hàng", nhập Họ tên "Trần Thị Mai", Email `mai.tran@vinafoods.vn`, SĐT `0908123456`, Công ty "Công ty CP Thực phẩm Vina".
2. **Kỳ vọng:** Hệ thống lưu thành công, tự động chuẩn hoá SĐT thành `+84908123456`, gán giai đoạn mặc định `Lead` theo BR-12.10, gán Loại khách hàng theo mặc định của tenant (BR-01.6), gán Người phụ trách là nhân viên tạo và **gán Đơn vị tổ chức theo Đơn vị tổ chức của nhân viên tạo** (BR-01.3), rồi mở màn hình Hồ sơ 360 độ. Vì nhân viên tạo bản ghi là Người phụ trách, họ thấy **đầy đủ** SĐT và email công việc không cần mở mặt nạ — cột (A) của bảng BR-04.3.

---

### Kịch bản 2: Quy trình Chuyển đổi Khách hàng Tiềm năng 1-Click (Lead Conversion)
1. Nhân viên thẩm định Lead "Trần Thị Mai" đã sẵn sàng mua hàng, bấm nút "Chuyển đổi Tiềm năng".
2. Trong hộp thoại chuyển đổi:
   - Chọn tạo Doanh nghiệp mới "Công ty CP Thực phẩm Vina".
   - Chọn tạo Cơ hội mới: "Hợp đồng Cung ứng Nông sản Q3", Giá trị dự kiến 500,000,000 VND, giai đoạn "Đề xuất báo giá".
3. Bấm "Xác nhận chuyển đổi".
4. **Kỳ vọng:** Hệ thống thực thi giao dịch nguyên tử: Nâng cấp Contact từ `Lead` lên giai đoạn `Opportunity` (bước nhảy bậc này hợp lệ theo BR-12.9 vì đã có Cơ hội bán hàng mở), tạo Doanh nghiệp "Công ty CP Thực phẩm Vina", tạo Cơ hội trị giá 500 triệu và chuyển hướng nhân viên vào màn hình Cơ hội vừa tạo. Lịch sử giai đoạn ghi nhận bước chuyển với sự kiện nguồn là "Chuyển đổi Tiềm năng".
5. **Kịch bản phụ (tạo Cơ hội trực tiếp không qua Chuyển đổi):** Nhân viên mở một Contact đang ở giai đoạn `MQL` và tạo Cơ hội bán hàng trực tiếp từ hồ sơ 360.
6. **Kỳ vọng:** Hệ thống **không** chặn với lỗi "Bước chuyển giai đoạn không hợp lệ"; Contact tự động lên `Opportunity` theo BR-12.2 và BR-12.9.

---

### Kịch bản 3: Nhận diện Trùng lặp, Gộp Bản ghi & Hoàn tác Gộp (Unmerge)
1. Nhân viên tạo khách hàng mới với email `mai.tran@vinafoods.vn`. **Kỳ vọng:** Vì trùng theo Tiêu chí chắc chắn, hệ thống **chặn tạo bản ghi mới** theo chính sách mặc định BR-17.2, hiển thị bản ghi hiện hữu và điều hướng nhân viên sang bản ghi đó — **không** sinh ra bản ghi trùng.
1b. **Thiết lập tiền đề cho bước gộp:** Bản ghi trùng B tồn tại trong hệ thống từ một nguồn hợp lệ theo BR-17.2b — chọn một trong: (a) dữ liệu lịch sử nhập khẩu trước khi áp dụng chính sách; (b) một lô nhập khẩu chọn chiến lược "Tạo bản ghi mới dù trùng" theo BR-23.3; hoặc (c) bản ghi khớp chỉ theo Tiêu chí tham khảo (cùng họ tên + cùng công ty, khác email) nên không bị chặn.
2. Quản trị viên mở công cụ Gộp bản ghi giữa Bản ghi A (cũ) và Bản ghi B (trùng).
3. Xem trước (Preview Merge), chọn Bản ghi A làm Master Record và bấm "Xác nhận gộp".
4. **Kỳ vọng Gộp:** Bản ghi B bị xóa mềm, toàn bộ ghi chú và công việc của B chuyển sang A, Sổ cái `contact_merges` ghi nhận 1 dòng lịch sử.
5. Sau đó, Quản trị viên vào Lịch sử gộp, bấm nút "Hoàn tác gộp" (Unmerge).
6. **Kỳ vọng Hoàn tác:** Bản ghi B được khôi phục nguyên vẹn, các dữ liệu công việc cũ của B được trả về đúng vị trí ban đầu.

---

### Kịch bản 4: Nhập khẩu Danh bạ 10,000 dòng từ Excel có Tự động Ánh xạ & Báo cáo Lỗi
1. Quản trị viên tải lên tệp `Danh_sach_khach_hang_2026.xlsx` dung lượng 15MB chứa 10,000 dòng thu được từ một hội thảo.
2. Trợ lý ánh xạ tự động nhận diện các cột: "Họ và tên" → Họ tên, "Điện thoại" → Số điện thoại, "Email" → Email, "Công ty" → Tên doanh nghiệp (BR-23.1, BR-23.2).
3. **Ba bước khai báo bắt buộc trước khi chạy:** (a) chọn **chiến lược xử lý trùng lặp** — chọn "Bỏ qua bản ghi trùng" (BR-23.3); (b) vì không chọn chiến lược cập nhật nên không cần Lookup Key (BR-23.4); (c) chọn **Cơ sở đồng thuận** cho lô dữ liệu từ danh mục A.11 — chọn "Dữ liệu từ sự kiện có phiếu đồng ý" (BR-30.4). Cả ba là trường bắt buộc chọn: giao diện **không cho bỏ trống**, nhưng danh mục A.11 có sẵn giá trị "Không có cơ sở đồng thuận" để người dùng khai báo trung thực khi thực sự không có.
4. **Kỳ vọng nếu chọn "Không có cơ sở đồng thuận" ở bước (c):** Hệ thống vẫn cho nhập nhưng gán toàn bộ lô `OPT_OUT` cho nhóm thư Tiếp thị và ghi rõ cảnh báo này trong kết quả (BR-30.4).
4b. **Kỳ vọng về cưỡng chế đồng thuận (BR-30.10):** Nếu ở một lô khác người dùng chọn chiến lược "Cập nhật đè" và dòng dữ liệu mang trạng thái `OPT_IN` cho một bản ghi hiện hữu đang `OPT_OUT`, hệ thống **giữ nguyên `OPT_OUT`** và ghi dòng đó vào báo cáo kết quả với ghi chú "Không nâng được mức đồng thuận — thiếu bằng chứng của chủ thể".
5. Bấm "Bắt đầu nhập khẩu".
6. **Kỳ vọng:** Tiến trình xử lý ngầm, thanh tiến trình hiển thị 100%. Kết quả: 9,850 dòng thành công, 150 dòng lỗi.
7. Quản trị viên bấm tải "Tệp báo cáo lỗi".
8. **Kỳ vọng:** Tệp báo cáo nêu rõ từng dòng lỗi kèm nguyên nhân cụ thể thuộc danh mục nguyên nhân tại BR-24.2 (sai định dạng email; số điện thoại không chuẩn hoá được; thiếu cả email lẫn số điện thoại; bước chuyển giai đoạn không hợp lệ; trùng lặp bị chặn theo chính sách BR-17.2) — **không** gộp tất cả thành một nguyên nhân chung.
9. **Kỳ vọng bổ sung (BR-15.7a):** Toàn bộ 9,850 bản ghi mới **không** được thăng hạng `MQL` tự động trong 24 giờ đầu và **không** tính vào cam kết thời gian phản hồi tại BR-31.7 cho tới khi phát sinh tương tác đầu tiên — kể cả các bản ghi có đủ điểm hồ sơ.

---

### Kịch bản 5: Thiết lập Mối quan hệ Đa Doanh nghiệp (Multi-Affiliations)
1. Khách hàng "Nguyễn Văn Hùng" là Tổng giám đốc tại "Công ty Đầu tư Hùng Cường", đồng thời là Thành viên HĐQT tại "Ngân hàng Thương mại Á Châu".
2. Nhân viên vào Hồ sơ ông Hùng → Tab "Doanh nghiệp trực thuộc" → bấm "Thêm liên kết".
3. Chọn công ty "Ngân hàng Thương mại Á Châu", nhập chức danh "Thành viên HĐQT", vai trò **"Cố vấn (Advisor)"** chọn từ danh mục chuẩn A.5.
4. **Kỳ vọng:** Hồ sơ ông Hùng hiển thị đầy đủ 2 công ty công tác; đồng thời mở hồ sơ "Ngân hàng Thương mại Á Châu" thấy ông Hùng xuất hiện trong danh sách nhân sự cấp cao.

---

### Kịch bản 6: Bảo vệ Dữ liệu Nhạy cảm & Mở khóa Mặt nạ (Field Masking & Audit)
1. **Tiền đề cấu hình:** Chủ sở hữu Workspace cùng Người phụ trách Bảo vệ Dữ liệu đã **bật nhóm trường Định danh KYC** kèm khai báo mục đích (BR-01.5b, `CFG-01-02`) — nhóm này mặc định tắt nên phải bật trước khi kiểm thử được.
1b. **Quản lý Kinh doanh B** — phụ trách Đơn vị tổ chức chứa bản ghi nên bản ghi nằm trong phạm vi dữ liệu của B theo giá trị **"Scope phòng ban"** (Ghi chú 8 mục 5), nhưng B **không phải Người phụ trách** và **không** thuộc Đội ngũ phụ trách — mở hồ sơ khách hàng "Trần Thị Mai" (bản ghi do nhân viên A phụ trách). B **chưa được cấp** quyền `contacts:unmask`. *Chọn vai trò này làm tiền đề vì cột (B) đòi một người **trong** phạm vi dữ liệu, **không** phải Người phụ trách và **không** thuộc Đội ngũ phụ trách. Với một Nhân viên Kinh doanh, giá trị "Scope gán" tại Ghi chú 8 chỉ gồm bản ghi mình phụ trách **cộng** bản ghi được chia sẻ tới mình — mà bản ghi được chia sẻ lại đưa họ vào Đội ngũ phụ trách, tức rơi vào cột (A) hoặc (B) theo mức quyền chia sẻ chứ không phải ca cần kiểm ở đây. "Scope phòng ban" của Quản lý Kinh doanh là con đường duy nhất vào cột (B) mà không đi qua đường chia sẻ.*
2. **Kỳ vọng (cột B của bảng BR-04.3):** Số điện thoại hiển thị `090****567`, email công việc hiển thị `m***@vinafoods.vn`, trường Số CCCD **che hoàn toàn**.
2b. **Kỳ vọng đối chiếu khi nhóm KYC tắt:** Nếu tenant không bật nhóm Định danh KYC theo `CFG-01-02`, các trường thuộc nhóm này **không được lưu và không hiển thị trên giao diện với mọi vai trò** — mức "Ẩn trường" theo BR-04.2 áp cho cả bốn cột, đúng BR-01.5b (nhóm trường chỉ được lưu khi đã bật kèm khai báo mục đích). Đây là hệ quả của việc **tắt nhóm trường**, không phải một mức hiển thị riêng nằm ngoài bảng BR-04.3.
3. **Kỳ vọng đối chiếu (cột A):** Nhân viên A — Người phụ trách bản ghi — mở cùng hồ sơ và thấy **đầy đủ** số điện thoại và email công việc mà không cần mở khóa, đúng BR-04.3 và nhất quán với Kịch bản 1.
4. **Một Quản lý Kinh doanh khác, đã được cấp** quyền `contacts:unmask`, mở hồ sơ và bấm biểu tượng "Mắt".
5. **Kỳ vọng:** Số điện thoại hiển thị đầy đủ `0908123456`, hệ thống ghi một bản ghi vào Nhật ký kiểm toán ghi nhận Quản lý đã mở khóa **tên trường nào** của bản ghi nào lúc 14:30 — nhật ký **không** lưu giá trị thật của trường (BR-04.4, NFR-07).
6. **Kịch bản phụ (hạn mức — BR-04.5):** Cùng người dùng đó mở khóa liên tiếp tới bản ghi thứ 51 trong ngày.
7. **Kỳ vọng:** Thao tác mở khóa bị **tạm chặn đến hết ngày**, hệ thống gửi cảnh báo tới Chủ sở hữu Workspace và Người phụ trách Bảo vệ Dữ liệu, và ghi vào báo cáo truy cập bất thường. Giao diện cấu hình **không** cho phép đặt hạn mức thành "không giới hạn".
8. **Kịch bản phụ (liên lạc không cần mở khóa — BR-04.6):** Quản lý Kinh doanh B ở bước 1b bấm "Gọi" ngay trên hồ sơ dù số điện thoại đang che một phần.
9. **Kỳ vọng:** Cuộc gọi thực hiện được, **không** yêu cầu mở khóa, **không** tính vào hạn mức mở khóa, nhưng hành động liên lạc này **được ghi nhật ký** theo NFR-07.
10. **Kịch bản phụ (ba mức đọc nhật ký kiểm toán — NFR-14, sàn bắt buộc):** Lần lượt bốn người mở màn hình Nhật ký kiểm toán của bản ghi "Trần Thị Mai": Quản lý Kinh doanh của A, Quản trị viên Workspace khi **không** có yêu cầu nào đang mở trên bản ghi, Quản trị viên Workspace khi **đang** xử lý một yêu cầu chủ thể dữ liệu trên đúng bản ghi đó, và Người phụ trách Bảo vệ Dữ liệu.
11. **Kỳ vọng:** Quản lý Kinh doanh **không truy cập được** (mức "Không truy cập"). Quản trị viên ở trường hợp không có yêu cầu mở **không truy cập được**; ở trường hợp đang xử lý yêu cầu thì đọc được **chỉ nhật ký của đúng bản ghi đó** và chỉ trong thời gian yêu cầu còn mở. Người phụ trách Bảo vệ Dữ liệu đọc được toàn phần. Mọi lượt đọc nhật ký đều **để lại một bản ghi nhật ký mới**. Thử cấu hình để mở quyền đọc toàn phần cho Quản lý Kinh doanh — hệ thống **từ chối** thao tác và nêu rõ NFR-14 ở mức Cố định (đây là **phép thử C** theo phân loại tại Kịch bản 22: giao diện ma trận cho phép chỉnh ô nói chung, nhưng lượt lưu bị chặn vì vi phạm một quy tắc Cố định).
12. **Kịch bản phụ (quyền xuất dữ liệu của Nhân viên Kinh doanh — BR-25.5, BR-25.4):** Nhân viên A xuất danh sách khách hàng mình phụ trách; lần lượt thử ba việc: (i) chọn thêm trường **Số CCCD** vào tập trường xuất; (ii) xuất **2.001 bản ghi** trong một ngày; (iii) xin phê duyệt của Quản lý Kinh doanh rồi xuất tiếp.
13. **Kỳ vọng:** (i) trường Số CCCD **không xuất hiện** trong danh sách trường chọn được, và không có đường phê duyệt nào mở được nó. (ii) Lần xuất làm vượt hạn mức **2.000 bản ghi/ngày** (`CFG-25-02`) bị chặn và chuyển thành yêu cầu phê duyệt gửi **Quản lý Kinh doanh** — không phải Quản lý Marketing (BR-25.4). (iii) Sau phê duyệt, A xuất tiếp được nhưng tổng lượng trong ngày **không vượt quá hai lần hạn mức**. Cả ba lần thao tác đều được ghi nhật ký (BR-25.3).

---

### Kịch bản 7: Hoàn tác Chuyển đổi Lead trong 24 giờ (Undo Lead Conversion)
1. Lúc 09:00, nhân viên kinh doanh chuyển đổi nhầm Lead "Lê Văn Bình" thành Contact, tự động tạo Account "Công ty XYZ" (mới, chưa từng tồn tại) và Deal "Cơ hội ABC" — nhưng chưa thao tác gì thêm trên Deal (chưa ghi chú, chưa đổi stage, chưa đính kèm tài liệu).
2. Lúc 10:30 cùng ngày (trong vòng 24 giờ), Quản lý Kinh doanh phát hiện sai sót, mở hồ sơ Deal "Cơ hội ABC" và bấm "Hoàn tác Chuyển đổi" (Undo Lead Conversion).
3. **Kỳ vọng:** Hệ thống cho phép hoàn tác vì Cơ hội chưa có hoạt động thực tế nào. Cơ hội "Cơ hội ABC" bị xóa mềm; Doanh nghiệp "Công ty XYZ" bị xóa mềm do chưa có Contact nào khác liên kết; Contact "Lê Văn Bình" trở về đúng giai đoạn trước chuyển đổi là `Lead` — hệ thống **không** báo lỗi "Bước chuyển giai đoạn không hợp lệ" vì đây là ngoại lệ theo BR-12.9, và **không** đòi lý do hạ hạng theo BR-12.7 mà chỉ bắt buộc chọn **Lý do hoàn tác** từ danh mục A.15; nhật ký kiểm toán ghi nhận đầy đủ hành động và người thực hiện.
4. **Kịch bản phụ (từ chối hoàn tác):** Nếu tại bước 2, nhân viên đã ghi 1 ghi chú vào Deal trước khi Quản lý bấm "Hoàn tác", hệ thống phải từ chối thao tác và hiển thị thông báo "Không thể hoàn tác: Deal đã phát sinh hoạt động".
5. **Kịch bản phụ (hết hạn 24h):** Nếu thao tác "Hoàn tác" được thực hiện sau 24 giờ kể từ thời điểm chuyển đổi (ví dụ 09:05 ngày hôm sau), hệ thống ẩn nút "Hoàn tác Chuyển đổi" và không cho phép thực hiện.

---

### Kịch bản 8: Suy giảm Điểm Tiềm năng theo Thời gian (Score Decay)
1. **Tiền đề (bắt buộc tách rõ hai thành phần điểm):** Khách hàng "Phạm Thị Lan" có **Điểm Hồ sơ 20** và **Điểm Tương tác 40** (tổng 60, đang ở giai đoạn `MQL`). Lần tương tác gần nhất là **ngày D**. Bản ghi chưa từng bị áp suy giảm điểm lần nào.
2. Tiến trình hệ thống chạy vào 02:00 **ngày D+14** — đây là lần chạy đầu tiên mà mốc 14 ngày không tương tác được thoả mãn.
3. **Kỳ vọng:** Điểm tương tác giảm 10%, làm tròn xuống: 40 → **36**. Tổng điểm tiềm năng được tính lại = 20 + 36 = **56**, vẫn trên Ngưỡng MQL 40 nên không sinh cảnh báo.
4. Tiến trình chạy các ngày **D+15 đến D+29**.
5. **Kỳ vọng:** Điểm tương tác **giữ nguyên 36**, không bị trừ lặp lại, vì mốc 14 ngày chỉ áp dụng một lần cho tới khi chạm mốc 30 ngày (BR-16.1).
6. Tiến trình chạy vào 02:00 **ngày D+30**.
7. **Kỳ vọng:** Điểm tương tác giảm tiếp 25%, làm tròn xuống: 36 → **27** (BR-16.2). Tổng điểm = 20 + 27 = **47**, vẫn trên Ngưỡng MQL.
8. **Kỳ vọng bổ sung (BR-16.4 — không tự hạ giai đoạn):** Dù tổng điểm rơi xuống dưới Ngưỡng MQL ở bất kỳ chu kỳ nào, hệ thống **không bao giờ** tự hạ giai đoạn vòng đời vì lý do điểm số. **Riêng nhánh điểm nguội này**, bước chuyển sang `Nurturing` cần một thao tác tường minh của Quản lý Kinh doanh. *Lưu ý phạm vi:* đây **không** phải quy tắc chung cho mọi đường vào `Nurturing` — nhánh "toàn bộ Cơ hội `Closed Lost`" tại BR-12.3 do **hệ thống tự chuyển** và chỉ yêu cầu Sales nhập lý do từ A.2, được kiểm tại Kịch bản 12 bước 3.
9. **Kỳ vọng sàn điểm (BR-16.3):** Qua nhiều chu kỳ suy giảm liên tiếp, điểm tương tác tiến về 0 nhưng **không bao giờ** nhận giá trị âm.
10. **Kỳ vọng khi tổng điểm rơi dưới ngưỡng (BR-16.4):** Khi tổng điểm xuống dưới 40, hệ thống gắn cảnh báo "Đã nguội" và đưa vào danh sách đề xuất chuyển `Nurturing`; khi Quản lý Kinh doanh thực hiện chuyển, bắt buộc chọn lý do từ danh mục **A.16**. **Kỳ vọng đối chiếu — hai giai đoạn bị loại trừ (BR-16.4):** nếu bản ghi đang ở `Customer`/`Evangelist` thì **không** được đưa vào danh sách đề xuất này (nguyên tắc 4); nếu bản ghi đang ở `Opportunity` cũng **không** được đưa vào, và khi toàn bộ Cơ hội của nó `Closed Lost` thì lý do bắt buộc lấy từ **A.2** theo BR-12.3 chứ không phải A.16.

---

### Kịch bản 9: Phân bổ Lead Tự động theo Vùng địa lý (Lead Routing)
1. Một Lead mới được tạo tự động từ Website Form với `country = "Việt Nam"`, `province = "Đà Nẵng"`, không có người tạo trực tiếp.
2. Hệ thống áp dụng bộ quy tắc phân bổ theo thứ tự ưu tiên tại BR-31.3b: kiểm tra Người phụ trách hiện hữu trước (không khớp vì là khách mới), rồi tới quy tắc Vùng địa lý.
3. **Kỳ vọng (khớp quy tắc Vùng):** Có nhân viên Sales phụ trách khu vực Miền Trung đang hoạt động → Lead được gán trực tiếp cho nhân viên đó, không qua Round-robin.
4. **Kịch bản phụ (không khớp quy tắc nào):** Một Lead khác được tạo từ Chatbot không có `country`/`province`/`industry` khớp bất kỳ quy tắc nào, và không còn Sales nào đang hoạt động trong nhóm liên quan.
5. **Kỳ vọng:** Lead được đưa vào hàng đợi "Unassigned", hệ thống gửi thông báo cho Quản lý Kinh doanh để phân công thủ công (theo BR-31.4).
6. **Kịch bản phụ (chống trùng chủ — BR-31.6):** Một Lead mới từ Website Form có email trùng với khách hàng "Trần Thị Mai" đang do nhân viên A phụ trách.
7. **Kỳ vọng:** Hệ thống **không** tạo bản ghi mới và **không** chia lead này cho nhân viên khác; yêu cầu mới được ghi vào Dòng thời gian của bản ghi hiện hữu và nhân viên A nhận được thông báo về yêu cầu mới của khách hàng mình đang phụ trách.

---

### Kịch bản 10: Đồng thuận Tiếp thị theo Kênh & Nguyên tắc Nghiêm ngặt nhất khi Gộp
1. Khách hàng "Trần Thị Mai" (bản ghi A) đã đồng ý nhận tin qua **Email, SMS và Zalo**. Khách bấm liên kết "Hủy nhận tin" trong một email chiến dịch.
2. **Kỳ vọng:** Chỉ kênh Email chuyển sang `OPT_OUT`; kênh Zalo và SMS vẫn giữ `OPT_IN`. Hệ thống lưu bằng chứng đồng thuận gồm thời điểm, nguồn thu thập (liên kết hủy nhận tin trong email — giá trị thuộc danh mục A.8), phiên bản điều khoản và người/hệ thống ghi nhận (BR-30.3).
2b. Ngay sau đó khách gửi một Vé hỗ trợ qua email. **Kỳ vọng:** Nhân viên Hỗ trợ **vẫn gửi được** phản hồi vé qua email cho khách, vì thư phản hồi vé thuộc nhóm "Giao dịch & Dịch vụ" không chịu chi phối của `OPT_OUT` (BR-30.5).
2bb. **Kỳ vọng sàn bắt buộc — mặc định an toàn (BR-30.9) và trần người nhận (BR-30.7b):** Gửi một lượt thư **không khai báo nhóm mục đích** — hệ thống xếp vào **nhóm Tiếp thị** và **chặn** vì khách đang `OPT_OUT`. Gửi một lượt thư cá nhân cho **11 người nhận** — vượt trần `CFG-30-02`, hệ thống xếp vào nhóm Tiếp thị và loại khách `OPT_OUT` khỏi danh sách nhận. Mở cấu hình `CFG-30-02` và thử đặt **20** — hệ thống từ chối, miền chỉ nhận 1–10. Thử tìm cấu hình cho nhóm Tiếp thị thoát chi phối `OPT_OUT` (`CFG-30-01`) — **không tồn tại lựa chọn đó**.
2c. Cùng lúc đó Nhân viên Kinh doanh gửi **thư báo giá** cho chính khách này. **Kỳ vọng:** thư gửi được và **không** bị trần 5 người nhận của nhóm Liên lạc 1-1, vì thư báo giá thuộc nhóm "Giao dịch & Dịch vụ" theo bảng BR-30.5 (BR-30.7c). Đồng thời khách **không** nhận được email chiến dịch tiếp thị nào.
3. Sau đó phát hiện có bản ghi B trùng lặp của cùng khách hàng này (phát sinh từ nguồn hợp lệ theo BR-17.2b — ví dụ nhập khẩu danh bạ sự kiện bằng một email khác), trong đó kênh Email đang ở trạng thái `OPT_IN`.
4. Quản trị viên chọn bản ghi **B làm Bản ghi Chính** (Master) và thực hiện gộp; tại bước Xem trước, chủ động chọn giữ giá trị `OPT_IN` của bản ghi B.
5. **Kỳ vọng (bắt buộc):** Sau khi gộp, kênh Email của bản ghi chính vẫn là **`OPT_OUT`** — hệ thống ghi đè lựa chọn thủ công của người dùng theo BR-19.6 và hiển thị thông báo giải thích lý do (không được phép gửi tin cho người đã từ chối). Bằng chứng đồng thuận của **cả hai** bản ghi được giữ lại đầy đủ.
6. **Kịch bản phụ (chặn gộp):** Nếu bản ghi B đang có yêu cầu xóa dữ liệu theo quyền chủ thể dữ liệu chưa hoàn tất, thao tác gộp bị từ chối kèm thông báo phải xử lý xong yêu cầu trước (BR-19.6, BR-33.4).

---

### Kịch bản 11: Xóa mềm Khách hàng, Ảnh hưởng Thực thể Con & Phục hồi
1. Khách hàng "Lê Văn Bình" đang có 1 Vé hỗ trợ mở và 1 Cơ hội bán hàng mở. Quản trị viên xóa khách hàng này.
2. **Kỳ vọng:** Bản ghi vào Thùng rác, biến mất khỏi mọi danh sách và báo cáo thông thường. Vé hỗ trợ và Cơ hội **không bị xóa** mà được gắn nhãn `[Khách hàng trong thùng rác]`; chức năng gửi phản hồi công khai trên vé bị khóa (BR-05.5).
3. Quản trị viên mở màn hình Thùng rác, thấy bản ghi kèm ngày xóa và người thực hiện, bấm "Khôi phục".
4. **Kỳ vọng:** Bản ghi trở lại nguyên vẹn, nhãn cảnh báo trên vé/cơ hội được gỡ, chức năng phản hồi công khai được mở lại, và hệ thống ghi nhật ký kiểm toán thao tác khôi phục (NFR-07).
5. **Kịch bản phụ (xóa doanh nghiệp có đa liên kết — BR-09.1):** Xóa doanh nghiệp "Công ty A" đang là Doanh nghiệp chính của ông Bình, trong khi ông Bình còn liên kết hoạt động với "Công ty B".
6. **Kỳ vọng:** Liên kết với Công ty A chuyển trạng thái `Tạm ngưng` (không mất dữ liệu chức danh); "Công ty B" tự động trở thành Doanh nghiệp chính mới; ông Bình không bị xóa và không rơi vào danh sách "Liên hệ chưa gắn doanh nghiệp".

---

### Kịch bản 12: Ma trận Chuyển đổi Giai đoạn & Xử lý khi Mọi Cơ hội Thất bại
1. Khách hàng "Nguyễn Thị Hoa" đang ở giai đoạn `Opportunity` với 2 Cơ hội bán hàng đang mở, chưa từng là `Customer`.
2. Cả 2 Cơ hội đều bị chuyển sang `Closed Lost`.
3. **Kỳ vọng:** Hệ thống **không** hạ hạng về `Lead`/`MQL`; chuyển sang `Nurturing` và **bắt buộc** yêu cầu Sales chọn "Lý do không chuyển đổi" từ danh mục **A.2** trước khi lưu (BR-12.3) — **không** phải A.16, vì A.16 chỉ dùng cho nhánh điểm nguội tại BR-16.4.
4. Nhân viên kinh doanh thử chuyển khách hàng này từ `Nurturing` sang `Evangelist`.
5. **Kỳ vọng:** Hệ thống **từ chối** bước chuyển với thông báo "Bước chuyển giai đoạn không hợp lệ", nêu rõ các giai đoạn hợp lệ có thể chuyển đến từ `Nurturing` (Lead, MQL, SQL, Opportunity, Customer, Disqualified) — theo Ma trận Chuyển đổi Giai đoạn tại FEAT-12. `Evangelist` không có trong danh sách vì chỉ đến được từ `Customer`.
5b. **Kỳ vọng đối chiếu (nguyên tắc 1):** Nếu chính khách hàng này phát sinh một Cơ hội bán hàng mới, hệ thống **tự động** chuyển `Nurturing → Opportunity` mà không báo lỗi; và nếu Cơ hội đó `Closed Won`, tự động chuyển tiếp lên `Customer`. Hai bước này hợp lệ theo thiết kế (BR-12.9) dù nhảy bậc.
6. **Kịch bản phụ (khách hàng chính thức không bị hạ hạng):** Khách hàng "Trần Thị Mai" đã là `Customer` có thêm 1 Cơ hội Upsell bị `Closed Lost`.
7. **Kỳ vọng:** Giai đoạn vẫn giữ nguyên `Customer`, không bị hạ về `Nurturing` (BR-12.3).
8. **Kịch bản phụ (hạ hạng có kiểm soát):** Nhân viên kinh doanh (không phải Quản lý) thử hạ hạng một khách hàng từ `MQL` về `Lead`.
9. **Kỳ vọng:** Hệ thống từ chối vì thiếu quyền; khi Quản lý Kinh doanh thực hiện, hệ thống bắt buộc nhập lý do hạ hạng từ danh mục chuẩn và ghi vào lịch sử giai đoạn (BR-12.7).
10. **Kịch bản phụ (loại nhanh Lead rác — BR-12.4b):** Nhân viên Kinh doanh nhận một Lead mới có tên "asdf asdf", số điện thoại `0000000000`. Nhân viên bấm **"Lead rác"** và chọn lý do `Thông tin giả/Spam/Lừa đảo` (A.1).
11. **Kỳ vọng ngay lập tức:** Đồng hồ cam kết thời gian phản hồi **dừng** (BR-31.7); bản ghi **bị loại khỏi mẫu đo `KPI-03`**; cơ chế thu hồi và phân bổ lại **không** kích hoạt. Giai đoạn vòng đời **vẫn đứng nguyên**, chưa phải `Disqualified`.
12. **Kỳ vọng sau 5 ngày làm việc không ai duyệt:** Bản ghi **vẫn đứng nguyên giai đoạn**; hệ thống **không** tự chuyển sang `Disqualified`; hàng đợi chờ duyệt được leo thang lên Quản trị viên Workspace kèm báo cáo tồn đọng. Khi Quản lý Kinh doanh bấm duyệt, bản ghi mới chuyển `Disqualified`. Khi Quản lý từ chối, dấu "Lead rác" bị dỡ và đồng hồ cam kết chạy lại từ thời điểm từ chối.
13. **Kỳ vọng đối chiếu (phạm vi áp dụng):** Thử bấm "Lead rác" trên một hồ sơ đang ở giai đoạn `Customer` — hành động **không khả dụng**; nhóm lý do gian lận đối với `Customer`/`Evangelist` chỉ Quản trị viên/Chủ sở hữu thực hiện được theo BR-12.8.

---

### Kịch bản 13: Thăng hạng Tự động theo Ngưỡng điểm & Chuyển giao Marketing → Sales
1. **Tiền đề (bắt buộc tách rõ hai thành phần điểm):** Lead "Phạm Văn Nam" đang ở giai đoạn `Lead` với tổng 35 điểm, gồm **Điểm Hồ sơ 35** (email doanh nghiệp +10, SĐT di động +10, ngành nghề mục tiêu +15) và **Điểm Tương tác 0**.
2. Khách mở email chiến dịch (+5) rồi nhấp liên kết trong email (+10) theo BR-15.2 → tổng 50 điểm, trong đó Điểm Tương tác = 15.
3. **Kỳ vọng:** Thoả **cả hai** điều kiện của BR-15.5 (tổng ≥ 40 **VÀ** điểm tương tác ≥ 15), hệ thống **tự động** thăng hạng lên `MQL` và gửi thông báo cho Marketing. Bước chuyển được ghi vào lịch sử giai đoạn với người thực hiện là "Hệ thống".
3b. **Kỳ vọng đối chiếu điều kiện kép (BR-15.7):** Nếu khách chỉ mở email (+5, điểm tương tác = 5, tổng 40) thì **không** được thăng hạng dù tổng đã đạt 40 — vì điểm tương tác chưa đạt 15. Đây là ca kiểm thử bắt buộc để chứng minh điều kiện kép có hiệu lực thật.
4. Khách tiếp tục đặt lịch demo (+30 điểm), đạt tổng 80 điểm.
5. **Kỳ vọng:** Vì vượt Ngưỡng SQL (≥ 70 điểm), hệ thống đánh dấu "Sẵn sàng chuyển Sales" và đưa vào hàng đợi thẩm định, nhưng **không** tự động chuyển sang `SQL` — bắt buộc chờ Sales thẩm định (BR-15.6).
6. **Kịch bản phụ (chống gian lận điểm — BR-15.3):** Khách mở cùng một email 5 lần trong cùng ngày.
7. **Kỳ vọng:** Chỉ được cộng điểm **1 lần** cho loại hành vi "mở email" trong ngày đó; tổng điểm không vượt trần 100.
8. **Kịch bản phụ (chống MQL giả từ nhập khẩu — BR-15.7):** Quản trị viên nhập 10.000 danh bạ hội thảo, trong đó 3.000 bản ghi có email doanh nghiệp + SĐT di động + chức danh quản lý (đạt 40 điểm hồ sơ, 0 điểm tương tác).
9. **Kỳ vọng:** **Không** bản ghi nào trong số 3.000 được thăng hạng `MQL`, vì thiếu điều kiện điểm tương tác ≥ 15. Đồng thời toàn bộ lô không được thăng hạng tự động trong 24 giờ đầu và không tính vào cam kết thời gian phản hồi tại BR-31.7 cho tới khi có tương tác đầu tiên.

---

### Kịch bản 14: Nhân viên Nghỉ việc & Bàn giao Danh bạ (Ownership Transfer)
1. Nhân viên kinh doanh A nghỉ việc, đang phụ trách 450 Contact, 20 Doanh nghiệp, 8 Cơ hội bán hàng đang mở và 3 Vé hỗ trợ đang mở.
2. Quản trị viên thử vô hiệu hoá tài khoản của A ngay lập tức.
3. **Kỳ vọng:** Hệ thống **chặn** thao tác vô hiệu hoá và yêu cầu chỉ định người nhận bàn giao (BR-34.4); nếu cần vô hiệu hoá gấp, hệ thống cho phép bàn giao tạm về Quản lý trực tiếp của A và đưa toàn bộ bản ghi vào danh sách "Chờ bàn giao lại".
4. Quản lý Kinh doanh chọn bộ lọc "Người phụ trách = A", chọn chuyển giao cho nhân viên B, phạm vi "kèm Cơ hội và Vé đang mở".
5. **Kỳ vọng:** Trước khi xác nhận, hệ thống hiển thị bước xem trước đúng số lượng: 450 Contact, 20 Doanh nghiệp, 8 Cơ hội mở, 3 Vé mở (BR-34.2). Sau khi xác nhận, toàn bộ được chuyển sang B; B nhận thông báo; nhật ký kiểm toán ghi đầy đủ danh sách bản ghi bị ảnh hưởng (BR-34.7).
6. **Kỳ vọng bổ sung:** Báo cáo "Bản ghi không có Người phụ trách hoạt động" (BR-34.5) trả về 0 bản ghi thuộc A sau khi bàn giao xong.
6b. **Kịch bản phụ (tự khai báo nghỉ phép — BR-34.6):** Lần lượt **bốn** người tự khai báo nghỉ phép 5 ngày kèm người xử lý thay: một Nhân viên Kinh doanh, một Nhân viên Hỗ trợ, một Nhân viên Marketing và một Quản lý Marketing. **Kỳ vọng:** cả bốn đều mở được màn hình khai báo và lưu thành công — quyền này thuộc **chính người dùng, bất kể vai trò**; trong khoảng nghỉ, Lead mới không phân bổ cho họ (BR-31.1) và yêu cầu chờ xử lý chuyển cho người xử lý thay (BR-31.6), nhưng **quyền phụ trách chính không đổi**. **Kỳ vọng đối chiếu:** Quản lý Kinh doanh cũng khai báo được thay cho một thành viên trong nhóm mình; một Nhân viên Kinh doanh **không** khai báo được thay cho đồng nghiệp.
7. **Kịch bản phụ (không khả dụng tự động — BR-34.6):** Nhân viên kinh doanh E đi công tác dài và **không đăng nhập 14 ngày liên tiếp**, không khai báo nghỉ phép và không chỉ định người xử lý thay.
8. **Kỳ vọng:** Hệ thống bật trạng thái **"không khả dụng"** cho E; **người xử lý thay mặc định là Quản lý trực tiếp của E** (BR-34.6a, thống nhất BR-34.4); hệ thống **gửi thông báo cho cả E và Quản lý** khi bật trạng thái (BR-34.6b); Lead mới **không** phân bổ cho E (BR-31.1) và yêu cầu chờ xử lý của khách do E phụ trách **chuyển ngay** cho Quản lý (BR-31.6). **Quyền phụ trách chính của E không đổi** — không có bản ghi nào bị chuyển sang người khác.
9. **Kỳ vọng khi E quay lại:** Ngay ở **lần đăng nhập kế tiếp**, trạng thái không khả dụng **tự hết hiệu lực** (BR-34.6c) và E lại vào vòng phân bổ, không cần Quản trị viên can thiệp.

---

### Kịch bản 21: Vòng đời Hồ sơ Khách hàng Tạm & Các mốc Lưu trữ Dài hạn
*Toàn bộ kịch bản này nghiệm thu theo **Quy ước nghiệm thu các quy tắc theo mốc thời gian dài** ở đầu mục 6 (dịch mốc thời gian của tiến trình hoặc dựng dữ liệu có mốc quá khứ), không chờ thời gian thực.*

1. **Tiền đề:** Một khách truy cập ẩn danh nhắn tin qua Livechat và được tạo thành **Hồ sơ Khách hàng Tạm** (BR-01.1b) với định danh thiết bị/kênh chat, không có email và số điện thoại. Cửa sổ chat đã hiển thị thông báo ghi nhận phiên theo BR-01.1b.
2. **Kỳ vọng:** Hồ sơ được tạo **không gán giai đoạn vòng đời** (BR-12.10, `CFG-12-02`), **không** vào mẫu đo `KPI-01` (BR-17.4), và **không** được đưa vào bất kỳ danh sách phân khúc chiến dịch nào.
3. **Nhánh A — chưa từng có nhân viên phản hồi.** Dịch mốc tới **ngày thứ 91** kể từ tương tác gần nhất (`CFG-33-01` = 90 ngày).
4. **Kỳ vọng (BR-33.6, nhánh thứ nhất):** Hệ thống **tự động xóa** hồ sơ tạm này; đây là ngoại lệ (b) đã khai tại BR-33.5. Thử đặt `CFG-33-01` = **200 ngày** — hệ thống **từ chối** vì vượt miền **30–150 ngày**; thử đặt = **150 ngày** trong khi `CFG-33-03` đang ở **6 tháng** (180 ngày) — hệ thống **chấp nhận**, vì đây đúng là điểm cực biên hợp lệ: khoảng cách 180 − 150 = 30 ngày, bằng đúng khoảng cách tối thiểu mà ràng buộc chéo đòi hỏi. Đây là phép kiểm chứng rằng miền đã được thu đúng mức — không rộng đến mức sinh tổ hợp vi phạm, cũng không hẹp đến mức chặn nhầm một cấu hình hợp lệ.
5. **Nhánh B — đã có nhân viên phản hồi.** Một hồ sơ tạm khác có nhân viên đã trả lời. Dịch mốc tới **ngày thứ 91**.
6. **Kỳ vọng:** Hồ sơ **không** bị xóa, chỉ vào **danh sách rà soát thủ công** (BR-33.6).
7. Dịch mốc tiếp tới **tháng thứ 19** kể từ tương tác gần nhất mà không ai quyết định (`CFG-33-03` = 18 tháng).
8. **Kỳ vọng (BR-33.6 — trần lưu tuyệt đối, sàn bắt buộc):** Hệ thống **tự động khử định danh**: xóa định danh thiết bị/kênh chat và mọi dữ liệu nhận diện, **giữ** nội dung hội thoại ở dạng vô danh. Mở cấu hình `CFG-33-03` và thử đặt **36 tháng** hoặc "vô hạn" — hệ thống **từ chối cả hai**, miền chỉ nhận 6–18 tháng.
9. **Nhánh C — trần lưu nhóm Định danh KYC.** Một khách hàng ở giai đoạn `Customer` có nhóm Định danh KYC đã bật (`CFG-01-02`) và hợp đồng gần nhất kết thúc ở mốc T. Dịch mốc tới **tháng thứ 25** kể từ T (`CFG-01-03` = 24 tháng).
10. **Kỳ vọng (BR-01.5b — sàn bắt buộc):** Hệ thống **tự khử vĩnh viễn** các trường thuộc nhóm Định danh KYC, **giữ nguyên** hồ sơ khách hàng và toàn bộ dữ liệu kinh doanh. Thử đặt `CFG-01-03` = **60 tháng** hoặc "vô hạn" — hệ thống **từ chối cả hai**, miền chỉ nhận 6–24 tháng.
11. **Nhánh D — rà soát dữ liệu không hoạt động.** Một hồ sơ khách hàng đã định danh không có tương tác nào trong **37 tháng** (`CFG-33-02` = 36 tháng).
12. **Kỳ vọng (BR-33.5 — hành vi Cố định):** Hệ thống **không** tự xóa hồ sơ này, chỉ đưa vào **danh sách rà soát** để con người quyết định. Tìm trong toàn bộ giao diện cấu hình một lựa chọn bật "tự động xóa khi hết thời hạn rà soát" — **không tồn tại lựa chọn nào như vậy** ở bất kỳ mức phân quyền nào, kể cả Chủ sở hữu Workspace.
13. **Kỳ vọng đối chiếu năm ngoại lệ (BR-33.5):** Đối chiếu đúng **năm** ngoại lệ đã khai của nguyên tắc "hệ thống không tự động xóa" — **(a)** khử định danh nhóm Định danh KYC ở bước 10; **(b)** xóa Hồ sơ Khách hàng Tạm chưa có nhân viên phản hồi ở bước 4; **(c)** khử định danh Hồ sơ Khách hàng Tạm chạm trần lưu ở bước 8; **(d)** dọn Thùng rác quá hạn (BR-05.4, `CFG-05-01` — đã kiểm tại Kịch bản 20, gồm cả các chốt an toàn BR-05.6); **(e)** tự xóa tệp nhập khẩu gốc hết thời hạn (`CFG-22-01`) và tài liệu xác minh danh tính sau 30 ngày (BR-33.7) — tải một tệp nhập khẩu, dịch mốc tới ngày thứ 31 và xác nhận mã tải về bị từ chối. **Không** có tiến trình tự động xóa nào ngoài năm ngoại lệ này.

---

### Kịch bản 15: Tư vấn viên Livechat Truy cập Ngữ cảnh Khách hàng ngoài Phạm vi Dữ liệu
1. Khách hàng "Trần Thị Mai" (do nhân viên kinh doanh A phụ trách, thuộc phòng ban khác) gửi tin nhắn qua Livechat.
2. Tư vấn viên C tiếp nhận hội thoại và mở panel Ngữ cảnh Khách hàng.
3. **Kỳ vọng:** C **xem được** hồ sơ 360, Dòng thời gian và Ngữ cảnh Khách hàng của bà Mai dù bản ghi nằm ngoài phạm vi dữ liệu thông thường của C — nhờ quyền đọc tự động khi có hội thoại đang mở (BR-35.4). Panel phản hồi trong ngưỡng nghiệm thu 150ms (NFR-02).
4. **Kỳ vọng về bảo mật:** C **không sửa được dữ liệu nghiệp vụ** của hồ sơ (tên, kênh liên lạc, giai đoạn, người phụ trách, thẻ) — **ngoại lệ duy nhất** là hai thao tác thu hẹp phạm vi xử lý dữ liệu tại BR-35.4a, được kiểm ở bước 4b; SĐT và email công việc hiển thị ở mức **che một phần** theo **cột (C)** của bảng BR-04.3 — đủ để xác minh đúng người và bấm gọi/gửi trong hệ thống theo BR-04.6; trường Định danh KYC vẫn che hoàn toàn; lượt truy cập được ghi nhật ký (BR-35.4c).
4b. **Kỳ vọng về ngoại lệ ghi (BR-35.4a):** Ngay trong hội thoại, bà Mai nói "đừng gửi email tiếp thị cho tôi nữa". C bấm ghi nhận — hệ thống **cho phép** C hạ đồng thuận kênh Email xuống `OPT_OUT`, và gắn được `RESTRICTED` nếu khách yêu cầu hạn chế xử lý, dù bản ghi nằm ngoài phạm vi dữ liệu thông thường của C; mỗi lượt đều ghi nhật ký theo NFR-07. **Kỳ vọng đối chiếu:** C thử **nâng** lại lên `OPT_IN` — hệ thống **từ chối** (BR-30.10, chỉ hạ mức mới tự do).
5. Hội thoại được đóng.
6. **Kỳ vọng:** Quyền đọc của C tự động hết hiệu lực (BR-35.4d). **Kỳ vọng đối chiếu về ngoại lệ ghi:** C thử lại thao tác hạ đồng thuận trên chính bản ghi đó — hệ thống **từ chối**, vì ngoại lệ tại BR-35.4a chỉ có hiệu lực khi vé/hội thoại còn mở. Khi C mở lại hồ sơ bà Mai, hệ thống hiển thị **thông tin tối thiểu để nhận diện** — tên viết tắt, tên Người phụ trách, Đơn vị tổ chức phụ trách, thời điểm tương tác gần nhất — kèm **ba nút** "Yêu cầu quyền truy cập", "Đề nghị chuyển giao" và "Đề nghị gộp", **không** hiển thị thông báo "không có quyền truy cập" (BR-17.3).
6b. **Kỳ vọng phân luồng người xử lý (BR-17.3, BR-35.3b):** C bấm "Đề nghị gộp". Yêu cầu được gửi tới **Quản trị Chất lượng Dữ liệu hoặc Quản trị viên**, không gửi tới nhân viên A để duyệt; A chỉ nhận thông báo để biết. Quá hạn, yêu cầu **chỉ leo thang**, hệ thống **không** tự gộp bản ghi.
7. C bấm "Yêu cầu quyền truy cập" và không ai phản hồi.
8. **Kỳ vọng (BR-17.2c):** Quá 4 giờ làm việc, yêu cầu leo thang lên Quản lý Kinh doanh của nhân viên A; quá thời hạn thứ hai mà Quản lý cũng không xử lý, hệ thống **tự cấp quyền đọc tạm có ghi nhật ký** cho C theo cơ chế BR-35.4.

---

### Kịch bản 16: Gộp Khách hàng Chính thức với Lead trùng — Bảo vệ Giai đoạn và Đồng thuận
1. Khách hàng "Nguyễn Văn Hùng" đã là `Customer` (bản ghi A, do nhân viên A phụ trách, kênh Email `OPT_OUT`). Tồn tại bản ghi B trùng ở giai đoạn `Lead` (kênh Email `OPT_IN`, do nhân viên B phụ trách) — phát sinh từ một nguồn hợp lệ theo BR-17.2b: ông Hùng từng để lại thông tin ở một hội thảo và lô danh bạ đó được nhập khẩu bằng **email cá nhân khác** với email công việc trên bản ghi A, nên không bị chặn ở bước tạo; hai bản ghi chỉ được nhận diện là trùng sau đó qua số điện thoại đã chuẩn hoá.
2. Quản trị viên phát hiện trùng, thực hiện gộp và **chọn bản ghi B (Lead) làm Bản ghi Chính**, tại bước Xem trước chủ động chọn giữ giai đoạn `Lead` và trạng thái `OPT_IN`.
3. **Kỳ vọng (bắt buộc, không thể ghi đè):** Sau khi gộp, bản ghi chính có giai đoạn **`Customer`** (BR-19.8 — giai đoạn tiến xa nhất thắng) và kênh Email ở trạng thái **`OPT_OUT`** (BR-19.6 — trạng thái nghiêm ngặt nhất thắng). Hệ thống hiển thị thông báo giải thích hai lựa chọn thủ công của người dùng đã bị ghi đè kèm lý do.
4. **Kỳ vọng về quyền sở hữu:** Người phụ trách sau gộp là người của bản ghi có tương tác gần nhất (BR-19.9); **cả nhân viên A và B đều nhận được thông báo**; nhật ký kiểm toán ghi đầy đủ.
5. **Kỳ vọng về dữ liệu:** Điểm tiềm năng lấy giá trị cao hơn của hai bản ghi, thẻ phân loại được hợp nhất, nguồn gốc UTM của bản ghi chính giữ nguyên và UTM của bản ghi phụ được lưu trong sổ cái gộp (BR-19.5, BR-19.10).
6. Sau 45 ngày, Quản trị viên phát hiện gộp sai và bấm "Hoàn tác gộp".
7. **Kỳ vọng:** Thao tác **thành công** vì còn trong thời hạn 90 ngày và bản ghi phụ chưa bị dọn dẹp vĩnh viễn (BR-20.3, BR-05.6b).

---

### Kịch bản 17: Yêu cầu Xóa Dữ liệu Cá nhân của Chủ thể Dữ liệu
1. Một người tự nhận là khách hàng "Lê Thị Hồng" gửi email yêu cầu xóa toàn bộ dữ liệu cá nhân. Bà Hồng đang ở giai đoạn `Customer` và còn 1 hợp đồng hiệu lực.
2. Nhân viên Hỗ trợ tiếp nhận và ghi nhận yêu cầu vào hệ thống theo dõi.
3. **Kỳ vọng:** Nhân viên Hỗ trợ tạo được bản ghi theo dõi yêu cầu (loại "Xóa vĩnh viễn", hạn xử lý 30 ngày) nhưng **không** thực thi được thao tác xóa (Ghi chú 5, mục 5).
4. Quản trị viên mở yêu cầu và thử xóa ngay.
5. **Kỳ vọng:** Hệ thống **chặn** cho tới khi ghi nhận phương thức xác minh danh tính chủ thể dữ liệu (BR-33.7); vì bà Hồng là `Customer`, thao tác còn yêu cầu **hai người khác nhau** (người xác minh và người phê duyệt).
6. Sau khi xác minh qua email đã xác thực của chính hồ sơ, Quản trị viên tiếp tục xử lý.
7. **Kỳ vọng (từ chối một phần):** Vì còn hợp đồng hiệu lực, hệ thống thực hiện **từ chối một phần** theo BR-33.3: xóa dữ liệu tiếp thị và kênh liên lạc không cần thiết, giữ dữ liệu tối thiểu phục vụ nghĩa vụ hợp đồng, và bắt buộc ghi lý do từ chối một phần để phản hồi khách hàng.
8. **Kỳ vọng về phạm vi xóa — kiểm chứng theo đúng 12 hàng của bảng BR-33.8, mỗi hàng một quan sát cụ thể:** Sau khi thao tác hoàn tất, hệ thống hiển thị **Biên bản Hoàn tất Xử lý** liệt kê từng hàng của bảng BR-33.8 kèm trạng thái (Đã xóa / Đã khử định danh / Đã thu hồi / Được giữ theo nghĩa vụ pháp lý) và số lượng đối tượng đã xử lý ở mỗi hàng. Người kiểm thử kiểm chứng từng dòng biên bản: (a) mở lại hồ sơ → không tồn tại; (b) tra dòng thời gian và ghi chú → không còn nội dung chứa dữ liệu cá nhân; (c) mở ảnh chụp trong Sổ cái Gộp → còn cấu trúc, không còn dữ liệu cá nhân; (d) dùng lại mã tải **tệp xuất dữ liệu** → bị từ chối; (d2) dùng lại mã tải **tệp báo cáo lỗi nhập khẩu** → bị từ chối; (e) tra nhật ký kiểm toán → còn mã bản ghi và loại thao tác, không còn dữ liệu nhận diện; (f) tra bằng chứng đồng thuận → còn thời điểm/nguồn/phiên bản điều khoản; (g) tra nhật ký xuất dữ liệu → có ghi tập trường và tập bản ghi đã từng xuất; **(h)** tra nội dung hội thoại đa kênh gắn với khách → đã xóa hoặc khử định danh theo sàn mà BR-33.8 đặt cho `omnichat-srs.md`, và biên bản nêu rõ số hội thoại đã xử lý; **(i)** tra nội dung Vé hỗ trợ gắn với khách → đã xóa hoặc khử định danh theo sàn mà BR-33.8 đặt cho `tickets-srs.md`; **(j)** tra tệp nhập khẩu gốc đã tải lên → không còn tải về được và biên bản ghi rõ đã xóa (hoặc đã hết thời hạn `CFG-22-01` và tự xóa trước đó); **(k)** với **bản sao lưu** — kiểm chứng theo **quy ước nghiệm thu các mốc thời gian dài** tại đầu mục 6: xác nhận bản ghi khách hàng nằm trong **danh sách chờ khử định danh khi khôi phục** và mô phỏng một lần khôi phục để thấy bản ghi không quay trở lại. Biên bản **không** được tuyên bố bản sao lưu "đã xóa xong", vì bản sao lưu theo thiết kế là bất biến. Hai dòng (h) và (i) chỉ nghiệm thu được sau khi hai sàn liên tài liệu tại vấn đề **#12 mục 7** đã được chốt. Hệ thống **không** được phát biểu "đã xóa xong" mà chỉ phát hành biên bản này — mệnh đề "không còn dữ liệu ở bất kỳ đâu" không kiểm chứng được nên không được dùng làm cam kết.
9. **Kỳ vọng về xử lý nội dung văn bản tự do:** Với ghi chú và dòng thời gian, hệ thống liệt kê **danh sách hữu hạn** các mục có gắn với khách hàng và yêu cầu người xử lý xác nhận từng mục theo một trong hai hành động: **ẩn toàn bộ mục** hoặc **thay nội dung bằng ghi chú vô danh**. Hệ thống **không** tự động nhận diện "phần nào là dữ liệu cá nhân" trong văn bản tự do, để tránh hai lần chạy cùng một ca kiểm thử cho hai kết quả khác nhau.

---

### Kịch bản 18: Ghi chú & Bản ghi Hoạt động (Notes & Activity Logging)
1. Nhân viên kinh doanh A ghi một ghi chú trên hồ sơ khách hàng mình phụ trách: "Khách sẵn sàng trả tới 800 triệu, đang so sánh với đối thủ X".
2. **Kỳ vọng (BR-36.1):** Ghi chú nhận phạm vi mặc định **"Nội bộ đội bán hàng"**. Nhân viên Marketing mở cùng hồ sơ **không** đọc được nội dung ghi chú này; Quản lý Kinh doanh của A và Quản trị viên đọc được.
3. Sau 2 giờ, A sửa lại nội dung ghi chú. Sau 30 giờ, A thử sửa tiếp.
4. **Kỳ vọng (BR-36.2):** Lần sửa ở giờ thứ 2 thành công. Lần sửa ở giờ thứ 30 bị từ chối; hệ thống chỉ cho **bổ sung nội dung mới** vào ghi chú, không cho sửa nội dung cũ.
5. A bấm "Xóa" ghi chú.
6. **Kỳ vọng (BR-36.3 — sàn bắt buộc):** Ghi chú chỉ bị **ẩn** khỏi dòng thời gian, nội dung vẫn được lưu; nhật ký kiểm toán ghi ai đã ẩn và lúc nào; Quản trị viên vẫn xem được ghi chú đã ẩn. Giao diện **không** có bất kỳ đường nào cho người dùng thường xóa vĩnh viễn.
7. A bấm "Gọi" cho khách và cuộc gọi kéo dài 3 phút.
8. **Kỳ vọng (BR-36.5):** Hệ thống tự sinh một bản ghi hoạt động kèm thời lượng, **không cho sửa nội dung**, và bản ghi này được công nhận là bằng chứng liên hệ lần đầu nhóm 1 theo BR-31.8.
9. A thử ghim 4 ghi chú lên đầu hồ sơ.
10. **Kỳ vọng (BR-36.4):** Chỉ ghim được tối đa 3; các ghi chú ghim này là nội dung trả về trong Ngữ cảnh Khách hàng 1 chạm (BR-28.1), **sau khi lọc theo phạm vi đọc của người xem** theo BR-36.7.

---

### Kịch bản 19: Đội ngũ Phụ trách Khách hàng & Chia sẻ có thời hạn
1. Khách hàng doanh nghiệp lớn "Tập đoàn Đại Việt" do nhân viên kinh doanh A phụ trách. Thực tế còn có Quản lý Khách hàng Hiện hữu B, nhân viên hỗ trợ C và kế toán công nợ D cùng phục vụ.
2. A thêm B, C, D vào Đội ngũ Phụ trách với vai trò từ danh mục A.13 và mức quyền: B = Chỉnh sửa, C = Chỉ đọc, D = Chỉ đọc, trong đó quyền của D đặt hết hiệu lực sau 30 ngày.
3. **Kỳ vọng (BR-35.1, BR-35.2, BR-35.3):** Cả ba truy cập được hồ sơ dù nằm ngoài phạm vi dữ liệu thông thường — quyền chia sẻ đưa họ **vào phạm vi dữ liệu của bản ghi này** theo BR-35.2, nên với bảng BR-04.3 họ thuộc cột (A) hoặc (B) tuỳ mức quyền, **không** thuộc cột (D). Về mức quyền: B sửa được, C và D chỉ đọc; C **không** được chia sẻ tiếp cho người khác vì chỉ có mức Chỉ đọc.
4. **Kỳ vọng về mặt nạ (BR-04.3 + BR-04.5b):** **B** — thành viên ở mức **Chỉnh sửa** — thấy **đầy đủ** kênh liên lạc công việc theo **cột (A)**. **C và D** — mức **Chỉ đọc** — chỉ thấy **che một phần** theo **cột (B)**, dù cùng thuộc Đội ngũ phụ trách; riêng D mang vai trò "Kế toán công nợ" cũng không thay đổi kết luận này. Đây là chốt chống việc thêm người vào Đội ngũ phụ trách trở thành đường vòng qua hạn mức mở khóa tại BR-04.5.
4b. **Kỳ vọng về kiểm soát cột (A) — sàn bắt buộc (BR-04.5b):** Mọi lượt A thêm thành viên đều để lại nhật ký (BR-35.6). Hạn mức `CFG-04-04` đếm **theo người được thêm vào**, không phải theo người đi thêm — vì thứ cần kiểm soát là **mức phơi bày tích tụ của người nhận quyền**. Kiểm chứng bằng hai phép đo: **(i)** A thêm **B** vào 60 bản ghi rồi thêm **C** vào 60 bản ghi khác — **không** cảnh báo, vì mỗi người nhận mới ở mức 60/100 dù A đã thao tác 120 lượt; **(ii)** A thêm B vào 60 bản ghi và Quản lý Kinh doanh thêm **chính B** vào 50 bản ghi khác trong cùng tháng — **có** cảnh báo gửi Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu, vì B đã chạm 110 bản ghi ở mức Chỉnh sửa, vượt 100 dù không ai trong hai người chia sẻ tự mình vượt hạn mức. Mở màn hình cấu hình `CFG-04-04` và thử đặt "không giới hạn" — hệ thống **không** có lựa chọn đó. Cuối tháng, Người phụ trách Bảo vệ Dữ liệu nhận được **báo cáo phơi bày định kỳ** liệt kê số bản ghi mỗi người đang xem được ở mức Đầy đủ.
5. Sau 30 ngày, hệ thống chạy rà soát quyền hết hạn.
6. **Kỳ vọng (BR-35.3):** Quyền của D tự động thu hồi, ghi nhận vào lịch sử bản ghi và nhật ký kiểm toán (BR-35.6). Quyền của B và C không bị ảnh hưởng.

---

### Kịch bản 20: Chốt An toàn trước khi Dọn dẹp Vĩnh viễn Thùng rác
1. Contact "Vũ Minh Đức" bị xóa mềm vào Thùng rác, còn 1 Cơ hội bán hàng trị giá 800 triệu **đang mở** và 1 Vé hỗ trợ **đang mở**.
2. Đủ 30 ngày, tiến trình dọn dẹp vĩnh viễn chạy.
3. **Kỳ vọng (BR-05.6a):** Hệ thống **không** xóa vĩnh viễn bản ghi. Bản ghi được đưa vào danh sách **"Cần xử lý trước khi dọn dẹp"** kèm thông báo cho Quản trị viên nêu rõ đang vướng Cơ hội và Vé nào.
4. Quản trị viên đóng Vé và chuyển Cơ hội sang khách hàng khác, rồi xác nhận xóa kèm lý do.
5. **Kỳ vọng:** Bản ghi được xóa vĩnh viễn và ghi nhật ký kiểm toán theo NFR-07.
6. **Kịch bản phụ (BR-05.6b):** Một bản ghi phụ bị xóa mềm **do thao tác gộp** cách đây 40 ngày cũng đến hạn dọn dẹp.
7. **Kỳ vọng:** Bản ghi này **không** bị xóa vì vẫn trong thời hạn Hoàn tác gộp 90 ngày (BR-20.3); nút "Hoàn tác gộp" vẫn hoạt động. Sau ngày thứ 90, nút bị ẩn và bản ghi mới được đưa vào diện dọn dẹp, trong khi sổ cái gộp vẫn được giữ theo NFR-05.
8. **Ghi chú nghiệm thu:** Các mốc 30 ngày và 90 ngày trong kịch bản này được thực thi theo Quy ước nghiệm thu các quy tắc theo mốc thời gian dài nêu ở đầu mục 6.

---

---

### Kịch bản 22: Kiểm chứng Sàn bắt buộc của toàn bộ Tham số Cấu hình
*Kịch bản này tồn tại để thoả **Điều kiện nghiệm thu #4** tại mục 10 một cách đầy đủ và kiểm chứng được. Cách chạy: với **mỗi tham số**, đăng nhập bằng **vai trò thấp nhất** khai ở cột "Người được thay đổi" của Phụ lục B. Lý do dùng vai trò thấp nhất chứ không dùng một tài khoản Chủ sở hữu cho cả bảng: theo **Quy ước thẩm quyền** đầu Phụ lục B, Chủ sở hữu bao trùm mọi vai trò cấp dưới nên **luôn** đổi được — chạy bằng Chủ sở hữu sẽ không phát hiện được lỗi phân quyền, trong khi mục đích của kịch bản là chứng minh sàn không thể bị vi phạm **bởi bất kỳ ai**. Với **mọi dòng có dấu "+"**, chuẩn bị sẵn **cả hai tài khoản** vì dấu này luôn mang nghĩa "và" — kể cả khi hai vai trò **ngang cấp** và không xác định được vai trò nào thấp hơn (dòng `CFG-12-02` với "Quản lý Kinh doanh + Quản lý Marketing" là trường hợp duy nhất như vậy: chạy bằng cả hai, không chọn một). Nếu vai trò thứ hai không tồn tại trên tenant đang kiểm thử, dùng nhánh thay thế tại Quy ước thẩm quyền và ghi rõ vào biên bản, mở màn hình cấu hình của từng tham số, và với mỗi tham số thực hiện **ba phép thử** dưới đây. Tham số đã có bước kiểm chi tiết ở kịch bản khác được ghi rõ để không kiểm trùng.*

1. **Phép thử A — vượt miền:** nhập một giá trị **ngoài miền giá trị** khai tại Phụ lục B. **Kỳ vọng:** hệ thống từ chối ngay tại màn hình nhập, nêu rõ miền hợp lệ; giá trị cũ không đổi.
2. **Phép thử B — "không giới hạn":** tìm trong giao diện một lựa chọn "không giới hạn", "vô hạn", "tắt kiểm soát" hoặc tương đương. **Kỳ vọng:** với mọi tham số mang mức **Có sàn bắt buộc**, lựa chọn đó **không tồn tại** — không phải bị từ chối sau khi chọn, mà không hiển thị.
3. **Phép thử C — vi phạm nội dung sàn:** nhập giá trị **nằm trong miền** nhưng vi phạm điều kiện nêu ở cột "Mức độ tự do". **Kỳ vọng:** hệ thống từ chối và nêu đúng quy tắc bị vi phạm kèm mã BR.
4. **Bảng đối tượng kiểm — toàn bộ tham số mức "Có sàn bắt buộc":**

| Tham số | Nội dung sàn phải kiểm — phép thử C, hoặc phép thử A/B khi sàn của tham số được thực thi bằng chính miền giá trị | Ghi chú |
| --- | --- | --- |
| `CFG-01-02` | Bật nhóm Định danh KYC mà **bỏ trống khai báo mục đích** → từ chối | — |
| `CFG-01-03` | Đặt 60 tháng hoặc "vô hạn" → từ chối (miền 6–24 tháng) | Đã kiểm tại Kịch bản 21 bước 10 |
| `CFG-04-01` | Đặt Nhóm 3 (KYC) lên "Che một phần" ở bất kỳ cột nào → từ chối. Đặt cột **(B)**, **(C)** hoặc **(D)** lên "Đầy đủ" → từ chối. Đặt cột **(D)** lên "Che một phần" → cũng **từ chối**, vì trần của cột (D) là "Che hoàn toàn". Đặt cột **(A)** lên "Đầy đủ" cho **Nhóm 1 hoặc Nhóm 2** → **chấp nhận**, vì (A) là cột duy nhất được phép nhận mức này; nhưng đặt cột **(A)** lên "Đầy đủ" cho **Nhóm 3 (KYC)** → vẫn **từ chối** theo sàn (i), vốn khoá cả bốn cột | Sàn ba tầng, chưa kiểm ở kịch bản nào khác |
| `CFG-04-02` | Tìm lựa chọn "không giới hạn" → không tồn tại | Đã kiểm tại Kịch bản 6 bước 7 |
| `CFG-04-03` | Cấu hình để hành động liên lạc trong hệ thống **không ghi nhật ký** → không tồn tại lựa chọn đó (NFR-07) | Nội dung sàn là nghĩa vụ ghi nhật ký, không phải trần số lượng |
| `CFG-04-04` | Cấu hình để lượt thêm thành viên vào Đội ngũ phụ trách **không ghi nhật ký** → không tồn tại lựa chọn đó (BR-35.6, NFR-07) | Nội dung sàn là nghĩa vụ ghi nhật ký; phần hạn mức đã kiểm tại Kịch bản 19 bước 4b |
| `CFG-05-01` | Đặt **90 ngày** (đầu trên của miền) trong khi `CFG-20-01` đang ở **90 ngày** → **chấp nhận** (điều kiện là "không cao hơn", bằng nhau vẫn hợp lệ). Kiểm chứng miền đã đóng kín: không tồn tại giá trị nào trong miền 30–90 làm vi phạm ràng buộc chéo | Ràng buộc chéo **đã đóng bằng miền** — phép thử C ở đây là *chứng minh không thể vi phạm*, không phải chờ hệ thống từ chối |
| `CFG-05-02` | Chỉ các ràng buộc **thật sự mang nhãn `[sàn bắt buộc]`** mới bị chặn. Kiểm hai ô đại diện: cấp quyền xuất Định danh KYC cho Marketing (BR-25.4, neo vào sàn BR-01.5b) → **từ chối**; cấp quyền đọc nhật ký kiểm toán cho Quản lý Kinh doanh (NFR-14, mức Cố định) → **từ chối**. Kiểm một ô đối chứng: cấp quyền chuyển giai đoạn thủ công cho Marketing (BR-02.2, **không** mang nhãn sàn) → **chấp nhận**, vì Ghi chú 7 cho tenant sửa mọi ô không thuộc sàn | Tham số bao trùm toàn ma trận; hai ô bị chặn + một ô đối chứng được phép |
| `CFG-12-01` | Bật bước chuyển `Customer → Lead` → từ chối (BR-12.3). Hạ quyền loại khách xuống mức Nhân viên → từ chối (BR-12.4). Hạ quyền loại `Customer`/`Evangelist`/`Churned` xuống dưới Quản trị viên → từ chối (BR-12.8) | Ba sàn trong một tham số |
| `CFG-12-02` | Đặt giai đoạn khởi tạo là `Customer`, `Evangelist`, `Churned` hoặc `Disqualified` → từ chối | — |
| `CFG-15-01` | Đặt điều kiện điểm tương tác tối thiểu của MQL về **0** → từ chối (BR-15.7). Đặt ba ngưỡng không theo thứ tự tăng dần → từ chối | — |
| `CFG-15-02` | Đặt thời gian hoãn thăng hạng dữ liệu nhập khẩu dưới **12 giờ** → từ chối | — |
| `CFG-16-01` | Đặt mốc thứ hai **nhỏ hơn hoặc bằng** mốc thứ nhất → từ chối | Ràng buộc thứ tự |
| `CFG-17-01` | Bật dùng Tiêu chí tham khảo (tên + công ty) để chặn hoặc gộp tự động → không tồn tại lựa chọn đó (BR-17.1) | — |
| `CFG-20-01` | Đặt **90 ngày** (đầu dưới của miền) trong khi `CFG-05-01` đang ở **90 ngày** → **chấp nhận**. Kiểm chứng miền đã đóng kín: không tồn tại giá trị nào trong miền 90–365 nhỏ hơn giá trị nhỏ nhất của `CFG-05-01` | Ràng buộc chéo **đã đóng bằng miền** |
| `CFG-22-01` | Đặt 90 ngày → từ chối (miền 7–30 ngày; trần tuyệt đối do BR-33.8 đặt) | — |
| `CFG-25-01` | Đặt **4.999** → từ chối (miền bắt đầu từ 5.000). Đặt **5.000** trong khi `CFG-25-02` cũng ở 5.000 → **chấp nhận**, vì điều kiện là "không cao hơn" | Ràng buộc chéo **đã đóng bằng miền** |
| `CFG-25-02` | Tìm lựa chọn cho Nhân viên Kinh doanh xuất trường Định danh KYC → không tồn tại. Kiểm chứng ràng buộc chéo đã đóng: giá trị lớn nhất của miền (5.000) bằng giá trị nhỏ nhất của `CFG-25-01` nên không tổ hợp nào vi phạm | Phần KYC đã kiểm tại Kịch bản 6 bước 13 |
| `CFG-30-01` | Bật cho nhóm Tiếp thị thoát chi phối `OPT_OUT` → không tồn tại lựa chọn đó | Đã kiểm tại Kịch bản 10 bước 2bb |
| `CFG-30-02` | Đặt 20 → từ chối (miền 1–10) | Đã kiểm tại Kịch bản 10 bước 2bb |
| `CFG-31-01` | Cấu hình để ghi chú thủ công đơn thuần được tính là bằng chứng liên hệ → không tồn tại lựa chọn đó (BR-31.8) | — |
| `CFG-31-02` | Bỏ quy tắc chống trùng chủ khỏi thứ tự ưu tiên phân bổ → từ chối | — |
| `CFG-31-04` | Cấu hình để bằng chứng liên hệ nhóm 2 (liên hệ ngoài hệ thống) **không cần Quản lý xác nhận** → không tồn tại lựa chọn đó (BR-31.8) | Tham số là *số lần dùng bằng chứng nhóm 2 mỗi người mỗi tháng*, không phải hạn mức tiếp nhận Lead |
| `CFG-33-01` | Đặt **200 ngày** → từ chối (miền 30–150 ngày). Kiểm chứng ràng buộc chéo đã đóng bằng miền: đặt **150 ngày** (đầu trên) trong khi `CFG-33-03` ở **6 tháng = 180 ngày** (đầu dưới) → **chấp nhận**, vì khoảng cách đúng bằng 30 ngày tối thiểu | Ràng buộc chéo **đã đóng bằng miền**; điểm cực biên đã kiểm tại Kịch bản 21 bước 4 |
| `CFG-33-02` | Bật "tự động xóa khi hết thời hạn rà soát" → không tồn tại lựa chọn đó (BR-33.5) | Đã kiểm tại Kịch bản 21 bước 12 |
| `CFG-33-03` | Đặt 36 tháng hoặc "vô hạn" → từ chối (miền 6–18 tháng) | Đã kiểm tại Kịch bản 21 bước 8 |
| `CFG-33-04` | Tìm lựa chọn "vô hạn" → không tồn tại; đặt 60 ngày → từ chối (miền 7–30 ngày) | — |
| `CFG-36-02` | Cấu hình cho phép **xóa cứng** ghi chú sau khi hết thời hạn sửa → không tồn tại lựa chọn đó (BR-36.3) | Nội dung sàn là cấm xóa cứng, không phải độ dài thời hạn sửa |
| `CFG-36-03` | Tìm lựa chọn mở phạm vi "Nội bộ đội bán hàng" cho Marketing hoặc tuyến Hỗ trợ → không tồn tại (BR-36.1) | — |

5. **Kiểm các quy tắc mức "Cố định" (hàng cuối Phụ lục B):** với từng quy tắc ở hàng đó, tìm trong **toàn bộ** giao diện cấu hình của mọi vai trò — kể cả Chủ sở hữu Workspace — một tham số điều chỉnh được nội dung mà hàng đó khoá. **Kỳ vọng:** không tồn tại tham số nào. Lưu ý hai trường hợp có phần cấu hình được: BR-33.7 có `CFG-33-04` (thời hạn biện pháp phòng ngừa) và BR-33.8 có `CFG-22-01` (thời hạn lưu tệp nhập khẩu gốc) — hai tham số này **hợp lệ**, vì phần bị khoá là nghĩa vụ và phạm vi, không phải thời hạn.
6. **Kỳ vọng chung về nhật ký:** mọi lượt thay đổi tham số **thành công** ở bước 1–5 đều để lại bản ghi nhật ký kiểm toán theo sự kiện "Thay đổi tham số cấu hình" của NFR-07, ghi đủ người thực hiện, thời điểm, tham số bị tác động và giá trị trước/sau. Các lượt **bị từ chối** không sinh bản ghi nhật ký kiểm toán — chúng không phải một thay đổi đã xảy ra, và NFR-07 là danh mục đóng nên không có sự kiện nào cho thao tác bị chặn.

## 7. Giới hạn hiện tại & Vấn đề chính sách cần quyết định tiếp

Mỗi vấn đề dưới đây bắt buộc có **người ra quyết định** và **thời hạn chốt**. *Quy ước thứ tự: số hiệu vấn đề được cấp theo thời điểm bổ sung, không theo thứ tự trình bày — ba vấn đề mang nhãn **⛔ CHẶN BAN HÀNH** được xếp cuối bảng để dễ tra, nên có thể gặp #12 đứng trước #11. Mọi tham chiếu tới vấn đề được giải theo số hiệu.* Vấn đề chưa được chốt sau thời hạn sẽ mặc định áp dụng "Đề xuất PM" để không làm treo tiến độ phát hành, và được ghi nhận là quyết định tạm thời cần soát lại ở phiên bản sau.

> **Trạng thái ba vấn đề mang nhãn ⛔ CHẶN BAN HÀNH (#9, #10, #11) — HOÃN CÓ CHỦ ĐÍCH.** Chủ tài liệu quyết định ngày **2026-09-02**: ba vấn đề này **chưa cần xử lý ở giai đoạn hiện tại** và được giữ nguyên ở trạng thái **Chờ (pending)**, không có thời hạn chốt trong giai đoạn này.
>
> Nhãn "chặn ban hành" **vẫn giữ nguyên hiệu lực**: ba vấn đề này chặn việc **ban hành chính thức**, không chặn việc dùng tài liệu ở giai đoạn thiết kế và phát triển. Ranh giới sử dụng trong thời gian hoãn:
> - **Được phép:** mở phạm vi phát triển, thiết kế giao diện, lập kế hoạch và viết kịch bản kiểm thử, cấu hình tham số trên môi trường không phải môi trường thật.
> - **Chưa được phép:** dùng tài liệu làm căn cứ cam kết pháp lý với khách hàng; đưa các mốc thời hạn xử lý yêu cầu chủ thể dữ liệu tại bảng FEAT-33 vào hợp đồng, điều khoản dịch vụ hay chính sách quyền riêng tư công bố ra ngoài — cho tới khi vấn đề #9 có văn bản xác nhận của Pháp chế.
>
> Khi bước sang giai đoạn ban hành, ba vấn đề này được kích hoạt lại nguyên trạng. Chúng **không** áp dụng cơ chế "quá hạn thì mặc định theo Đề xuất PM" nêu ở đoạn trên, vì nhóm soạn tài liệu không có thẩm quyền chốt thay Pháp chế.

| # | Vấn đề chính sách | Đề xuất PM (mặc định nếu quá hạn) | Người ra quyết định | Thời hạn chốt |
| --- | --- | --- | --- | --- |
| 1 | **Tự động Làm giàu Dữ liệu Doanh nghiệp:** Có tích hợp nguồn dữ liệu bên thứ ba để tự động điền Tên công ty/Địa chỉ/Ngành nghề từ Mã số thuế hoặc Tên miền không? | Đưa vào lộ trình giai đoạn sau; trước mắt chỉ hỗ trợ nhập liệu thủ công và kiểm tra trùng lặp theo mã số thuế | Product Owner + người phê duyệt ngân sách của khối Kinh doanh (do liên quan chi phí thuê dữ liệu) — đây là vai trò tổ chức bên ngoài mô hình phân quyền hệ thống, không phải một actor tại mục 2.2 | Trước khi mở phạm vi phát triển giai đoạn kế tiếp |
| 2 | **Xung đột Trường Đa trị khi Gộp:** Khi gộp 2 khách hàng đều có nhiều email/SĐT, giữ tất cả hay chỉ giữ giá trị chính? | Giữ lại toàn bộ email/SĐT hợp lệ thành danh sách định danh phụ; người dùng chỉ định duy nhất 1 giá trị làm định danh chính | Product Owner + Quản trị Chất lượng Dữ liệu | Trước khi triển khai FEAT-19 lên môi trường thật |
| 3 | **Thời hạn Dọn dẹp Thùng rác:** 30 ngày hay 90 ngày? | 30 ngày cho gói tiêu chuẩn, cho phép tùy biến lên 90 ngày cho gói Enterprise | Product Owner + Chủ sở hữu Workspace (đại diện khách hàng lớn) | Trước khi ban hành chính sách gói dịch vụ |
| 4 | **Ngưỡng điểm MQL/SQL mặc định (BR-15.5):** Bộ ngưỡng 40/70/85 là giả định ban đầu, cần hiệu chỉnh theo dữ liệu thực tế | Áp dụng bộ ngưỡng mặc định 40/70/85 cho 90 ngày đầu, sau đó hiệu chỉnh theo tỷ lệ chuyển đổi thực tế | Quản lý Marketing + Quản lý Kinh doanh (thoả thuận chung Marketing–Sales) | Sau 90 ngày kể từ go-live |
| 5 | **~~Phạm vi dữ liệu của vai trò Marketing~~ — ĐÃ GIẢI QUYẾT ở v4.0** | Chuyển thành tham số cấu hình theo tenant `CFG-05-02` (Phụ lục B): mặc định Marketing xem toàn bộ ở dạng chỉ đọc, **mỗi lượt đọc bản ghi ngoài phạm vi gán đều ghi nhật ký** theo sự kiện tương ứng tại NFR-07; tenant siết lại theo phạm vi gán nếu chính sách nội bộ yêu cầu | — | Đã chốt |
| 6 | **~~Quyền nhập khẩu dữ liệu của Marketing~~ — ĐÃ GIẢI QUYẾT ở v4.0** | Chuyển thành tham số cấu hình `CFG-05-02`: mặc định cho phép khi được cấp quyền nhập khẩu, bắt buộc khai báo Cơ sở đồng thuận theo BR-30.4 và danh mục A.11 | — | Đã chốt |
| 7 | **Tích hợp kênh gửi tin để phân loại mục đích (BR-30.5):** Việc phân loại 3 nhóm mục đích gửi tin đòi hỏi phân hệ gửi email/tin nhắn phải khai báo nhóm cho mỗi lượt gửi. | Bổ sung yêu cầu khai báo nhóm mục đích vào giao diện tích hợp gửi tin; lượt gửi không khai báo nhóm bị mặc định coi là nhóm Tiếp thị (an toàn nhất về pháp lý) | Product Owner + Trưởng nhóm Kỹ thuật | Trước khi triển khai FEAT-30 lên môi trường thật |
| 8 | **Mức độ phụ thuộc vào tài liệu Phân quyền (BR-35.5):** Cơ chế thực thi chia sẻ bản ghi và thứ tự ưu tiên quyền thuộc `iam-tenant-authorization.md`. | Chốt hợp đồng nghiệp vụ giữa hai tài liệu trước khi mở phạm vi phát triển FEAT-35; nguyên tắc bất biến: quyền chia sẻ chỉ nới rộng, không thu hẹp quyền theo vai trò | Product Owner + Chủ sở hữu tài liệu IAM | Trước khi mở phạm vi phát triển Nhóm K |
| **9** | **⛔ CHẶN BAN HÀNH — Thời hạn xử lý yêu cầu chủ thể dữ liệu (bảng FEAT-33):** Tài liệu hiện đặt 30 ngày cho yêu cầu xóa và 15 ngày cho chỉnh sửa theo thông lệ GDPR. Pháp luật về bảo vệ dữ liệu cá nhân tại Việt Nam có mốc **ngắn hơn** cho một số loại yêu cầu. **Nhóm soạn tài liệu không có thẩm quyền chốt con số này.** | Pháp chế xác nhận thời hạn theo từng loại yêu cầu, lập bảng đối chiếu GDPR / pháp luật Việt Nam, và **lấy mốc ngắn hơn** làm cam kết hệ thống | **Pháp chế / Tư vấn pháp lý** + DPO | Trước khi ban hành tài liệu |
| **10** | **⛔ CHẶN BAN HÀNH — Không có quy trình xử lý sự cố rò rỉ dữ liệu cá nhân:** Toàn tài liệu không có quy tắc nào về phát hiện, phân loại, thông báo cho cơ quan quản lý và cho chủ thể dữ liệu khi xảy ra sự cố — dù BR-04.5 đã có cảnh báo truy cập bất thường và `KPI-06` đã đo số lượt truy cập bất thường chưa được đóng kết luận — hai thứ này phát hiện *dấu hiệu*, không thay được một quy trình xử lý sự cố. | Bổ sung một nhóm quy tắc nghiệp vụ về sự cố dữ liệu cá nhân: tiêu chí xác định sự cố, phân loại mức độ, mốc thời gian thông báo, vai trò chịu trách nhiệm, và mẫu nội dung thông báo | **Pháp chế** + DPO + Chủ sở hữu Workspace | Trước khi ban hành tài liệu |
| 12 | **Hai sàn liên tài liệu mới đặt cho `omnichat-srs.md` và `tickets-srs.md` (BR-33.8):** Tài liệu này bắt buộc hai phân hệ đó có cơ chế thực thi quyền xóa theo mã khách hàng, nhưng cả hai được tuyên bố **ngoài phạm vi** ở mục 1.2 và chủ sở hữu hai tài liệu không có trong vòng ký ở mục 10. | Chốt hợp đồng nghiệp vụ với hai tài liệu theo đúng cách đã làm với tài liệu IAM ở vấn đề #8; nếu chủ sở hữu hai tài liệu không cam kết được sàn này, hai dòng (h) và (i) của bảng BR-33.8 phải chuyển thành "chưa cam kết" và Biên bản Hoàn tác Xử lý phải nêu rõ phần chưa phủ | Product Owner + Chủ sở hữu `omnichat-srs.md` + Chủ sở hữu `tickets-srs.md` + DPO | Trước khi triển khai FEAT-33 lên môi trường thật |
| **11** | **⛔ CHẶN BAN HÀNH — Không có quy định nơi lưu và chuyển dữ liệu xuyên biên giới:** NFR-12/NFR-13 mở phạm vi vận hành đa vùng (gồm tiếng Ả Rập, hàm ý thị trường Trung Đông) nhưng không có dòng nào về nơi lưu dữ liệu khách hàng và điều kiện chuyển dữ liệu ra ngoài lãnh thổ. | Pháp chế xác định yêu cầu về nơi lưu dữ liệu theo từng thị trường mục tiêu; hoặc tuyên bố rõ nội dung này thuộc tài liệu nào khác và bổ sung vào danh mục Tài liệu liên quan | **Pháp chế** + Trưởng nhóm Kỹ thuật | Trước khi ban hành tài liệu |

---

## 8. Phụ lục A — Danh mục dữ liệu chuẩn (Reference Data)

Các danh mục dưới đây là giá trị chuẩn dùng chung, bắt buộc dùng dạng lựa chọn từ danh sách (không nhập tự do) để bảo đảm khả năng thống kê và báo cáo.

**A.1 Lý do Loại khách hàng (Disqualified Reason) — BR-12.4, BR-12.4b, BR-12.8:**
Danh mục chia thành **hai nhóm có nhãn**, vì thẩm quyền sử dụng khác nhau:

- **Nhóm Thương mại** — 5 giá trị: Sai ngành/không thuộc tập khách hàng mục tiêu · Không đủ ngân sách · Không có nhu cầu thực · Đã là khách hàng của đối thủ với hợp đồng dài hạn · Ngoài vùng phục vụ.
- **Nhóm Gian lận & Dữ liệu không hợp lệ** — 3 giá trị, gọi tắt là **"nhóm gian lận"** ở BR-12.8: Thông tin giả/Spam/Lừa đảo · Trùng lặp với bản ghi khác · Không thuộc đối tượng đủ điều kiện pháp lý.

  *Ai dùng nhóm nào:* **BR-12.4** (Quản lý Kinh doanh trở lên, các giai đoạn tiền bán hàng) dùng được **cả 8 giá trị**. **BR-12.4b** (Nhân viên Kinh doanh đánh dấu nhanh) chỉ dùng được **2 giá trị** "Thông tin giả/Spam/Lừa đảo" và "Trùng lặp với bản ghi khác" — đây là tập con của nhóm Gian lận, không phải cả nhóm, vì giá trị thứ ba đòi đánh giá pháp lý vượt thẩm quyền nhân viên. **BR-12.8** (Quản trị viên/Chủ sở hữu, với `Customer`/`Evangelist`/`Churned`) dùng được **cả 3 giá trị** của nhóm Gian lận.

*Nhóm Thương mại **không** dùng được cho `Customer`/`Evangelist`/`Churned`, vì một khách đã trả tiền không thể bị loại vì lý do "không đủ ngân sách".*

**A.2 Lý do Không chuyển đổi (Nurturing Reason) — BR-12.3:**
Hết ngân sách kỳ này · Chờ phê duyệt nội bộ · Chưa đúng thời điểm/hoãn sang kỳ sau · Thua đối thủ về giá · Thua đối thủ về tính năng · Thiếu người ra quyết định · Dự án bị tạm dừng · Không phản hồi sau nhiều lần liên hệ.

**A.3 Lý do Hạ hạng Giai đoạn (Stage Downgrade Reason) — BR-12.7:**
Thẩm định lại không đủ điều kiện · Thông tin ban đầu không chính xác · Khách hàng thay đổi nhu cầu · Điểm tiềm năng không phản ánh thực tế · Sai sót nhập liệu.

**A.4 Vai trò Liên hệ trên Cơ hội (Contact Role on Deal) — BR-19.4:**
Người ra quyết định (Decision Maker) · Người ủng hộ nội bộ (Champion) · Người thẩm định kỹ thuật (Technical Evaluator) · Người ảnh hưởng (Influencer) · Người thực hiện mua hàng (Purchaser). *Thứ tự ưu tiên khi gộp theo đúng trình tự liệt kê.*

**A.5 Vai trò Liên kết Doanh nghiệp (Affiliation Role) — BR-10.1:**
Chính (Primary) · Phụ (Secondary) · Cố vấn (Advisor) · Cổ đông (Shareholder) · Đại diện pháp luật (Legal Representative).
*Lưu ý: "Đã nghỉ việc" **không** phải vai trò mà là giá trị của trường Trạng thái liên kết — xem A.5b.*

**A.5b Trạng thái Liên kết Doanh nghiệp (Affiliation Status) — BR-10.1, BR-09.1:**
Đang công tác (Active) · Đã nghỉ việc (Former) · Tạm ngưng (Suspended — do Doanh nghiệp liên kết đang nằm trong Thùng rác theo BR-09.1a).

**A.6 Loại Quan hệ Cá nhân (Person Relation Type) — BR-11.1:**
Quản lý trực tiếp / Cấp dưới · Người giới thiệu / Được giới thiệu · Thành viên gia đình · Đối tác kinh doanh · Trợ lý / Người đại diện.

**A.7 Kênh Nguồn gốc Khách hàng (Lead Source) — BR-32.1:**
Website · Facebook Ads · Google Ads · Zalo · Giới thiệu (Referral) · Sự kiện/Hội thảo · Tiếp cận chủ động (Cold Outreach) · Nhập khẩu từ tệp · Đối tác · Không xác định.

**A.8 Nguồn thu thập Đồng thuận (Consent Source) — BR-30.3:**
Form đăng ký trên website · Hộp thoại đồng ý trên Livechat · Phiếu đồng ý tại sự kiện · Nhập khẩu từ tệp có khai báo cơ sở · Ghi nhận thủ công bởi nhân viên · Liên kết Hủy nhận tin trong email · Yêu cầu trực tiếp của khách hàng · **Yêu cầu chưa xác minh được danh tính** (dùng cho lượt hạ mức đồng thuận do biện pháp phòng ngừa tại BR-33.7d — ghi nhận đúng bản chất rằng lượt hạ mức này không xuất phát từ một yêu cầu đã xác minh).

**A.9 Giai đoạn Vòng đời (Lifecycle Stage) — FEAT-12:** Xem danh mục đầy đủ 10 giá trị và các bước chuyển hợp lệ tại Ma trận Chuyển đổi Giai đoạn, FEAT-12.

**A.10 Trạng thái Khả năng Tiếp cận (Deliverability Status) — BR-29.2, BR-10.4:**
Đã xác thực (Verified) · Không tiếp cận được (Bounced — trạng thái kỹ thuật: email hỏng, số không tồn tại) · Chưa kiểm tra (Unverified) · **Không còn hiệu lực (Obsolete — lý do nghiệp vụ: khách đã rời doanh nghiệp sở hữu địa chỉ đó, theo BR-10.4; có thể đảo lại, khác với `Bounced`)**.

**A.11 Cơ sở Đồng thuận cho lô Nhập khẩu (Consent Basis) — BR-30.4:**
Khách hàng đã đăng ký trực tiếp · Dữ liệu từ sự kiện có phiếu đồng ý · Quan hệ hợp đồng hiện hữu · Không có cơ sở đồng thuận (**không phải giá trị mặc định** — người nhập khẩu bắt buộc tự chọn theo BR-30.4; khi chọn giá trị này, toàn bộ lô nhận `OPT_OUT` cho nhóm thư Tiếp thị).

**A.12 Nhóm Mục đích Gửi tin (Message Purpose) — BR-30.5:**
Tiếp thị & Quảng bá (chịu chi phối `OPT_OUT`) · Giao dịch & Dịch vụ · Liên lạc 1-1 do nhân viên chủ động.

**A.13 Vai trò tham gia Đội ngũ Phụ trách (Account Team Role) — BR-35.1:**
Kinh doanh chính · Hỗ trợ kỹ thuật · Quản lý khách hàng · Kế toán công nợ · Quan sát.

**A.14 Loại Khách hàng (Customer Type) — BR-01.6:**
Khách hàng Doanh nghiệp (B2B) · Khách hàng Cá nhân tiêu dùng (B2C).

**A.15 Lý do Hoàn tác Chuyển đổi (Undo Conversion Reason) — BR-14.2:**
Chuyển đổi nhầm bản ghi · Khách hàng chưa thực sự đủ điều kiện · Thông tin doanh nghiệp sai · Trùng với Cơ hội đã có · Yêu cầu của Quản lý.

**A.16 Lý do chuyển sang Nuôi dưỡng (Nurturing Transition Reason) — BR-16.4, ma trận FEAT-12:**
Điểm tương tác nguội dưới ngưỡng · Khách hàng đề nghị liên hệ lại sau · Chưa đúng thời điểm ngân sách · Không phản hồi sau nhiều lần liên hệ · Chuyển sang chăm sóc bằng chiến dịch định kỳ.
*Phân biệt với A.2 (dùng khi toàn bộ Cơ hội `Closed Lost` theo BR-12.3) và A.3 (dùng cho bước lùi trên phễu tuyến tính theo BR-12.7).*

**A.18 Lý do Quay lại Phễu từ Nuôi dưỡng (Nurturing Re-entry Reason) — BR-16.5, ma trận FEAT-12:**
Khách hàng chủ động liên hệ trở lại · Có tương tác mới với nội dung tiếp thị · Đã qua thời điểm khách đề nghị liên hệ lại · Ngân sách của khách đã được duyệt · Người liên hệ mới tại doanh nghiệp cũ.
*Phân biệt với A.3 (hạ hạng trên phễu tuyến tính theo BR-12.7) và A.16 (đưa vào nuôi dưỡng theo BR-16.4) — A.18 là chiều ngược lại của A.16.*

**A.17 Lý do Mở lại bản ghi đã bị Loại (Disqualified Reopen Reason) — ma trận FEAT-12, dòng `Disqualified`:**
Thông tin liên lạc đã được xác minh lại · Khách hàng chủ động liên hệ trở lại · Đã xác định trước đây loại nhầm · Doanh nghiệp tái cấu trúc, người liên hệ nay hợp lệ · Có bằng chứng mới từ nguồn khác.
*Bắt buộc dùng khi đưa một bản ghi từ `Disqualified` trở lại `Lead`/`Nurturing`; phân biệt với A.1 (lý do loại) — A.17 ghi nhận căn cứ đảo ngược một quyết định loại, và là dữ liệu để rà soát chất lượng quyết định loại của từng Quản lý.*

---

## 8b. Phụ lục B — Danh mục Tham số Cấu hình theo Tenant (Tenant Configuration Catalog)

**Nguyên tắc thiết kế:** Mọi quy tắc nghiệp vụ có nhiều hướng xử lý hợp lý đều được triển khai thành **tham số cấu hình theo từng không gian làm việc**, kèm một **giá trị mặc định là hướng chuẩn hệ thống tại thời điểm thiết kế tài liệu này**. Tenant tự điều chỉnh cho phù hợp nghiệp vụ của mình mà không cần thay đổi mã nguồn hay chờ phát hành phiên bản mới.

**Quy ước thẩm quyền:** cột "Người được thay đổi" nêu **vai trò thấp nhất** được phép đổi tham số đó. **Đây là một trục quyền riêng, độc lập với ma trận mục 5:** ma trận quy định ai dùng được **tính năng** trên dữ liệu nghiệp vụ, còn cột này quy định ai đổi được **tham số cấu hình cấp không gian làm việc** của tính năng đó. Hai trục không suy ra nhau — một Quản lý Kinh doanh chỉ có "Scope phòng ban" trên dữ liệu vẫn có thể là người đặt các mốc thời hạn phản hồi Lead cho cả tenant, vì đó là quyết định nghiệp vụ thuộc chuyên môn của họ chứ không phải quyền chạm vào dữ liệu ngoài phạm vi. **Chủ sở hữu Workspace và Quản trị viên Workspace bao trùm mọi vai trò cấp dưới** — hai vai trò này đổi được mọi tham số mà một vai trò nghiệp vụ đổi được, nên không liệt kê lại ở từng dòng. Dấu **"+"** trong cột này luôn có nghĩa **"và"** — mọi vai trò được nối bằng "+" đều phải cùng phê duyệt một lượt thay đổi, không phải danh sách lựa chọn. Cụ thể: "+ DPO" và "+ Quản trị Chất lượng Dữ liệu" là **người phê duyệt thứ hai bắt buộc** — thiếu người này thì thao tác không thực hiện được kể cả bởi Chủ sở hữu. Riêng khi vai trò thứ hai là **Quản trị viên hoặc Chủ sở hữu**, dấu "+" chỉ mang tính liệt kê thừa vì hai vai trò đó vốn đã bao trùm; vai trò nghiệp vụ đứng trước dấu "+" **tự thực hiện được một mình**.

**Khi vai trò thứ hai không tồn tại hoặc trùng người:** áp đúng **quy tắc thay thế người phê duyệt thứ hai tại NFR-14** — người thứ hai là **một Quản trị viên Workspace khác** với người thực hiện. Quy tắc này áp cho **mọi** vai trò phê duyệt thứ hai xuất hiện ở cột này, không riêng Người phụ trách Bảo vệ Dữ liệu: khi tenant không chỉ định DPO, hoặc khi Quản trị Chất lượng Dữ liệu do chính Quản trị viên kiêm nhiệm theo mục 2.2, thao tác **vẫn thực hiện được** với hai người khác nhau. Yêu cầu bất biến là **luôn có hai người khác nhau đứng tên**, không phải là hai chức danh cụ thể — nếu không có nhánh này, một tenant chưa chỉ định DPO sẽ không đổi được 15 tham số có sàn pháp lý và Điều kiện nghiệm thu #4 không chạy được trên chính tenant đó.

**Ba mức độ tự do của tham số:**
- **Tự do** — tenant đặt giá trị bất kỳ trong miền cho phép.
- **Có sàn bắt buộc** — tenant điều chỉnh được nhưng không được nới lỏng dưới ngưỡng an toàn/pháp lý đã quy định.
- **Cố định** — không cấu hình được, vì liên quan nghĩa vụ pháp lý hoặc toàn vẹn dữ liệu.

| Mã tham số | Quy tắc liên quan | Nội dung cấu hình | Giá trị mặc định (chuẩn hệ thống) | Miền giá trị cho phép | Người được thay đổi | Mức độ tự do |
| --- | --- | --- | --- | --- | --- | --- |
| `CFG-01-01` | BR-01.6 | Loại khách hàng mặc định khi tạo mới | Khách hàng Doanh nghiệp (B2B) | B2B / B2C / Bắt buộc người dùng chọn | Chủ sở hữu | Tự do |
| `CFG-04-01` | BR-04.3 | Phạm vi che mặt nạ theo nhóm trường và quan hệ với bản ghi | Đúng bảng tại BR-04.3 | Ma trận **3 nhóm trường × 4 quan hệ người xem (A)(B)(C)(D)**, mỗi ô nhận một trong 4 mức hiển thị tại BR-04.2 | Chủ sở hữu + DPO | **Có sàn bắt buộc** — ba sàn không nới được: **(i)** Nhóm 3 (Định danh KYC) phải ở mức che hoàn toàn hoặc ẩn trường ở **cả bốn** cột, chỉ mở được bằng quyền chuyên biệt theo BR-04.4, có nhật ký và chịu hạn mức `CFG-04-02`; **(ii)** cột **(D)** — người ngoài phạm vi dữ liệu — **không được** đặt cao hơn mức "Che hoàn toàn" cho bất kỳ nhóm trường nào, vì nếu đặt cột (D) lên "Đầy đủ" thì không ai còn cần mở khóa và hai sàn BR-04.5, BR-04.5b trở thành vô hiệu; **(iii)** cột **(B)** — người trong phạm vi dữ liệu — và cột **(C)** — quyền đọc tạm của tuyến Hỗ trợ — **không được đặt cao hơn "Che một phần"**. Hệ quả: **cột (A) là cột duy nhất được phép nhận mức "Đầy đủ"**, và cột (A) đã chịu kiểm soát riêng tại BR-04.5b. Nếu bỏ trống trần của cột (B), một tenant đặt cột (B) lên "Đầy đủ" là toàn bộ người trong phạm vi dữ liệu — với vai trò Marketing là **toàn tổ chức** theo Ghi chú 2 mục 5 — đọc được giá trị thật mà không cần mở khóa: hạn mức tại BR-04.5 không còn gì để đếm, báo cáo phơi bày tại BR-04.5b rỗng, và `KPI-06` mất nguồn phát hiện bất thường |
| `CFG-01-02` | BR-01.5b | Bật/tắt nhóm trường Định danh KYC và mục đích sử dụng | **Tắt** | Bật (kèm khai báo mục đích) / Tắt | Chủ sở hữu + DPO | **Có sàn bắt buộc** — bật thì bắt buộc khai báo mục đích |
| `CFG-01-03` | BR-01.5b | Thời hạn lưu nhóm Định danh KYC sau khi hợp đồng kết thúc | 24 tháng | 6 – 24 tháng | Chủ sở hữu + DPO | **Có sàn bắt buộc** — hết hạn buộc phải tự khử định danh, không được đặt "vô hạn" |
| `CFG-04-02` | BR-04.5 | Hạn mức mở khóa mặt nạ mỗi người mỗi ngày | 50 bản ghi/ngày | **10 – 200** | Chủ sở hữu + DPO | **Có sàn bắt buộc** — **không tồn tại lựa chọn "Không giới hạn"**; trần tuyệt đối 200 áp cho mọi tenant |
| `CFG-04-03` | BR-04.6 | Hạn mức hành động liên lạc trong hệ thống mỗi người mỗi ngày | 200 lượt/ngày | 50 – 1.000 | Chủ sở hữu | **Có sàn bắt buộc** — mọi lượt liên lạc luôn phải ghi nhật ký (NFR-07) |
| `CFG-04-04` | BR-04.5b | Số bản ghi tối đa một người được thêm vào Đội ngũ phụ trách với mức Chỉnh sửa mỗi tháng | 100 bản ghi/tháng | 20 – 500 | Chủ sở hữu + DPO | **Có sàn bắt buộc** — mọi lượt thêm thành viên luôn phải ghi nhật ký (BR-35.6) |
| `CFG-05-01` | BR-05.4 | Thời hạn lưu bản ghi trong Thùng rác trước khi xóa vĩnh viễn | 30 ngày (gói tiêu chuẩn) | 30 – 90 ngày | Chủ sở hữu | **Có sàn bắt buộc** — không được đặt **cao hơn** `CFG-20-01` (thời hạn hoàn tác gộp); giới hạn trên còn phụ thuộc gói dịch vụ |
| `CFG-05-02` | Mục 5 | Ma trận phân quyền theo vai trò, gồm phạm vi dữ liệu và quyền nhập/xuất của vai trò Marketing | Theo đúng ma trận mục 5 | Từng ô điều chỉnh được | Chủ sở hữu + DPO | **Có sàn bắt buộc** — không được nới lỏng các ràng buộc đánh dấu "sàn bắt buộc" trong các BR |
| `CFG-12-01` | FEAT-12 | Ma trận Chuyển đổi Giai đoạn — các bước chuyển được phép | Theo đúng ma trận tại FEAT-12 | Từng bước chuyển bật/tắt được | Quản lý Kinh doanh (Chủ sở hữu và Quản trị viên bao trùm) | **Có sàn bắt buộc** — ba sàn không nới được: không được cho phép hạ `Customer`/`Evangelist` về giai đoạn tiền bán hàng (BR-12.3, nguyên tắc 4); không được nới quyền loại khách dưới mức Quản lý Kinh doanh (BR-12.4); không được nới quyền loại `Customer`/`Evangelist`/`Churned` với lý do gian lận xuống dưới mức Quản trị viên (BR-12.8) |
| `CFG-12-02` | BR-12.10 | Giai đoạn vòng đời mặc định theo từng nguồn tạo bản ghi | Thủ công/Form web/Nhập khẩu → `Lead`; Chuyển đổi hội thoại → `Lead`; Đăng ký bản tin → `Subscriber`; Hồ sơ Khách hàng Tạm → chưa gán giai đoạn | "Chưa gán giai đoạn" (chỉ dành cho nguồn Hồ sơ Khách hàng Tạm), hoặc một trong sáu giai đoạn **tiền bán hàng** (Subscriber, Lead, MQL, SQL, Opportunity, Nurturing) | Quản lý Kinh doanh + Quản lý Marketing | **Có sàn bắt buộc** — **không** được đặt giai đoạn khởi tạo là `Customer`, `Evangelist`, `Churned` hay `Disqualified`: một bản ghi vừa tạo chưa thể đã trả tiền hay đã rời bỏ, và nếu cho phép thì nguyên tắc 4 cùng toàn bộ báo cáo phễu bị vô hiệu ngay từ điểm nhập liệu |
| `CFG-14-01` | BR-14.2 | Thời hạn được Hoàn tác Chuyển đổi Tiềm năng | 24 giờ | 1 – 168 giờ | Quản lý Kinh doanh | Tự do |
| `CFG-15-01` | BR-15.5 | Bộ ngưỡng điểm MQL / SQL / Ưu tiên cao | 40 / 70 / 85 điểm, kèm điều kiện điểm tương tác ≥ 15 cho MQL | 0 – 100 mỗi ngưỡng, theo thứ tự tăng dần | Quản lý Marketing (tự thực hiện được; Quản trị viên và Chủ sở hữu bao trùm — xem Quy ước thẩm quyền đầu Phụ lục B) | **Có sàn bắt buộc** — điều kiện điểm tương tác tối thiểu không được đặt về 0 (BR-15.7) |
| `CFG-15-02` | BR-15.7 | Độ trễ thăng hạng cho dữ liệu nhập khẩu; chống thông báo lặp; độ trễ đánh giá lại ngưỡng | 24 giờ / 1 lần trong 30 ngày / 7 ngày | **12** – 168 giờ; 1 – 90 ngày; 0 – 30 ngày | Quản lý Marketing | **Có sàn bắt buộc** — độ trễ thăng hạng cho dữ liệu nhập khẩu không được đặt dưới 12 giờ, vì đây là chốt chống sinh MQL giả hàng loạt (BR-15.7a) |
| `CFG-16-01` | BR-16.1, BR-16.2 | Các mốc ngày không tương tác và tỷ lệ suy giảm điểm | 14 ngày −10%; 30 ngày −25% | 7 – 90 ngày mỗi mốc, **theo thứ tự tăng dần** (mốc thứ hai luôn lớn hơn mốc thứ nhất); 0 – 50% mỗi tỷ lệ | Quản lý Marketing | **Có sàn bắt buộc** — hai mốc không được bằng nhau hay đảo thứ tự, vì quy tắc "mốc thứ nhất chỉ trừ một lần cho tới khi chạm mốc thứ hai" tại BR-16.1 sẽ mất nghĩa |
| `CFG-17-01` | BR-17.2 | Chính sách xử lý khi phát hiện trùng lặp theo Tiêu chí chắc chắn | Chặn tạo bản ghi mới | Chặn cứng / Cảnh báo và cho phép **vẫn lưu bản ghi mới** có ghi nhật ký | Chủ sở hữu + Quản trị Chất lượng Dữ liệu | **Có sàn bắt buộc** — Tiêu chí tham khảo (tên + công ty) vĩnh viễn không được dùng để chặn hoặc gộp tự động (BR-17.1) |
| `CFG-19-01` | BR-19.9 | Quy tắc xác định Người phụ trách sau khi gộp | Giữ người phụ trách của bản ghi có tương tác gần nhất | Bản ghi có tương tác gần nhất / Bản ghi Chính / Bắt buộc chỉ định thủ công | Quản lý Kinh doanh | Tự do |
| `CFG-20-01` | BR-20.3 | Thời hạn được Hoàn tác gộp bản ghi | 90 ngày | 90 – 365 ngày | Chủ sở hữu | **Có sàn bắt buộc** — phải lớn hơn hoặc bằng `CFG-05-01`; miền bắt đầu từ 90 ngày để mọi tổ hợp hợp lệ với miền 30–90 của `CFG-05-01`, giao diện chặn ngay lúc lưu nếu hai giá trị vi phạm ràng buộc chéo |
| `CFG-30-01` | BR-30.5 | Nhóm "Liên lạc 1-1 do nhân viên chủ động" có chịu chi phối `OPT_OUT` hay không | Không chịu chi phối, nhưng bắt buộc ghi nhật ký | Có / Không | Chủ sở hữu + DPO | **Có sàn bắt buộc** — nhóm Tiếp thị vĩnh viễn chịu chi phối `OPT_OUT`, không cấu hình được |
| `CFG-31-01` | BR-31.7, BR-31.8, BR-17.2c, BR-35.3b | Các mốc thời hạn phản hồi Lead theo mức ưu tiên; số lần thu hồi tự động tối đa | 1 / 4 / 24 giờ làm việc; tối đa 2 lần thu hồi | 0,5 – 72 giờ; 0 – 5 lần | Quản lý Kinh doanh | **Có sàn bắt buộc** — ghi chú thủ công đơn thuần không bao giờ được tính là bằng chứng liên hệ (BR-31.8) |
| `CFG-31-02` | BR-31.3b | Thứ tự ưu tiên giữa các quy tắc phân bổ Lead | Người phụ trách hiện hữu → Vùng địa lý → Ngành nghề → Vòng lần lượt → Unassigned | Sắp xếp lại thứ tự các quy tắc từ (2) đến (4) | Quản lý Kinh doanh + Quản trị viên | **Có sàn bắt buộc** — quy tắc "Người phụ trách hiện hữu" luôn ở vị trí ưu tiên số 1 (BR-31.6) |
| `CFG-22-02` | BR-22.2 | Dung lượng tệp nhập khẩu tối đa mỗi lần tải lên | 50MB | 10 – 200MB (theo gói dịch vụ) | Chủ sở hữu | Tự do |
| `CFG-25-01` | BR-25.4 | Ngưỡng số bản ghi mỗi lần xuất cần phê duyệt trước | 5.000 bản ghi | 5.000 – 50.000 | Chủ sở hữu + DPO | **Có sàn bắt buộc** — lần xuất chứa trường Định danh KYC luôn cần phê duyệt bất kể số lượng |
| `CFG-25-02` | BR-25.5, BR-25.4 | Hạn mức xuất dữ liệu của Nhân viên Kinh doanh mỗi người mỗi ngày | 2.000 bản ghi/ngày | 200 – 5.000 (miền hai tham số không giao nhau ở phần vi phạm nên mọi tổ hợp đều hợp lệ) | Chủ sở hữu | **Có sàn bắt buộc** — vai này vĩnh viễn không xuất được trường Định danh KYC; mọi lần xuất luôn ghi nhật ký (BR-25.3); giá trị đặt ở đây **không được cao hơn** `CFG-25-01`, và khi tenant hạ `CFG-25-01` xuống dưới giá trị hiện tại thì hạn mức ngày tự động lấy theo giá trị nhỏ hơn (BR-25.4) |
| `CFG-33-04` | BR-33.7 | Thời hạn tối đa của biện pháp phòng ngừa khi không xác minh được chủ thể dữ liệu | 30 ngày | 7 – 30 ngày | Quản trị viên + DPO | **Có sàn bắt buộc** — không được đặt "vô hạn"; hết hạn buộc tự dỡ và bắt buộc thông báo Người phụ trách |
| `CFG-30-02` | BR-30.7 | Số người nhận tối đa của một lượt gửi thuộc nhóm Liên lạc 1-1 | 5 người nhận | 1 – 10 | Chủ sở hữu + DPO | **Có sàn bắt buộc** — trần tuyệt đối **10**; đây là ngưỡng mà một lượt gửi vẫn còn đọc được là liên lạc cá nhân, trên mức đó là gửi hàng loạt và **buộc xếp vào nhóm Tiếp thị** chịu chi phối `OPT_OUT` |
| `CFG-31-03` | BR-31.7b | Lịch làm việc: múi giờ, ngày làm việc, giờ bắt đầu/kết thúc, danh mục ngày lễ | Thứ Hai–Thứ Sáu 08:00–17:30, không ngày lễ | Tự khai báo | Chủ sở hữu | Tự do |
| `CFG-31-04` | BR-31.8 | Số lần dùng bằng chứng liên hệ nhóm 2 (ngoài hệ thống) mỗi người mỗi tháng | 10 lần/tháng | 0 – 50 | Quản lý Kinh doanh | **Có sàn bắt buộc** — luôn cần Quản lý xác nhận và luôn thống kê riêng trong `KPI-03` |
| `CFG-33-01` | BR-33.6 | Thời hạn dọn dẹp Hồ sơ Khách hàng Tạm không có tương tác của nhân viên | 90 ngày kể từ tương tác gần nhất | 30 – 150 ngày | Quản trị viên + DPO | **Có sàn bắt buộc** — phải **nhỏ hơn ít nhất 30 ngày** so với `CFG-33-03` (trần lưu tuyệt đối, tối thiểu 6 tháng = 180 ngày; quy ước quy đổi trong toàn tài liệu là **1 tháng = 30 ngày**); vì vậy miền dừng ở **150 ngày** để mọi tổ hợp đều hợp lệ kể cả ở điểm cực biên, nếu không thì trần khử định danh nổ trước khi hồ sơ kịp vào danh sách rà soát và nhánh thứ hai của BR-33.6 chết |
| `CFG-33-02` | BR-33.5 | Thời hạn rà soát dữ liệu khách hàng không hoạt động | 36 tháng | 12 – 84 tháng | Chủ sở hữu + DPO | **Có sàn bắt buộc** — thời hạn cấu hình được, nhưng hành vi "hệ thống không tự động xóa" là **cố định** (BR-33.5) |
| `CFG-33-03` | BR-33.6 | Trần lưu tuyệt đối cho Hồ sơ Khách hàng Tạm đã có tương tác của nhân viên | 18 tháng kể từ tương tác gần nhất | 6 – 18 tháng | Chủ sở hữu + DPO | **Có sàn bắt buộc** — hết trần buộc phải tự khử định danh, không được đặt "vô hạn" |
| `CFG-22-01` | BR-33.8 | Thời hạn lưu tệp nhập khẩu gốc sau khi tiến trình nhập hoàn tất | 30 ngày | 7 – 30 ngày | Quản trị viên + DPO | **Có sàn bắt buộc** — trần 30 ngày là trần tuyệt đối do bảng phạm vi xóa tại BR-33.8 đặt ra, không nới lên được; hết hạn buộc phải tự xóa |
| `CFG-34-01` | BR-34.3 | Phạm vi thực thể con mặc định khi chuyển giao quyền phụ trách | Kèm Cơ hội và Vé hỗ trợ đang mở | Chỉ bản ghi / Kèm thực thể đang mở / Kèm toàn bộ | Quản lý Kinh doanh | Tự do |
| `CFG-36-01` | BR-36.1 | Phạm vi đọc mặc định của ghi chú mới | **Nội bộ đội bán hàng** | Nội bộ đội bán hàng / Chung / Giới hạn | Chủ sở hữu | Tự do |
| `CFG-36-02` | BR-36.2 | Thời hạn người tạo được sửa nội dung ghi chú | 24 giờ | 0 – 168 giờ | Chủ sở hữu | **Có sàn bắt buộc** — hết thời hạn chỉ được bổ sung, vĩnh viễn không được xóa cứng (BR-36.3) |
| `CFG-36-03` | BR-36.1, BR-36.7 | Phạm vi đọc ghi chú của vai trò Marketing và của tuyến Hỗ trợ | Marketing: chỉ phạm vi "Chung". Tuyến Hỗ trợ: phạm vi "Chung" + ghi chú ghim **đã được đánh dấu cho phép tuyến Hỗ trợ đọc** (BR-36.7) | Chỉ "Chung" / "Chung" + ghi chú ghim (mặc định cho tuyến Hỗ trợ) | Chủ sở hữu + DPO | **Có sàn bắt buộc** — **không** có lựa chọn mở phạm vi "Nội bộ đội bán hàng" **theo vai trò** cho Marketing hay tuyến Hỗ trợ. Sàn này chặn theo **vai trò**, không chặn theo **tư cách thành viên**: một nhân viên hỗ trợ được thêm vào Đội ngũ phụ trách của một bản ghi (vai trò tham gia "Hỗ trợ kỹ thuật" tại A.13) **đọc được** ghi chú "Nội bộ đội bán hàng" **của riêng bản ghi đó** theo BR-36.1, vì khi ấy họ đọc với tư cách thành viên đội ngũ chứ không phải với tư cách tuyến Hỗ trợ — đây là quyền được cấp trên từng bản ghi, có nhật ký và có thể thu hồi, khác hẳn việc mở cho cả vai trò |
| — | BR-19.6, BR-19.8, BR-30.5 (nhóm Tiếp thị), BR-30.9, **BR-30.10**, BR-33.7 (nghĩa vụ xác minh danh tính và quy tắc hai người — **không** gồm thời hạn biện pháp phòng ngừa, vốn cấu hình được qua `CFG-33-04`), BR-33.8 (phạm vi xóa và các sàn liên tài liệu — **không** gồm thời hạn lưu tệp nhập khẩu gốc, vốn cấu hình được qua `CFG-22-01`), BR-34.4, BR-36.3, NFR-07 (giới hạn nội dung nhật ký), NFR-14 | Các quy tắc bảo vệ đồng thuận tiếp thị, **cưỡng chế đồng thuận trên mọi nguồn tác động**, giai đoạn khách hàng đang trả tiền, xác minh chủ thể dữ liệu, phạm vi xóa, chốt bàn giao, chống xóa cứng ghi chú, giới hạn nội dung và quyền đọc nhật ký kiểm toán | Theo đúng quy tắc | — | — | **Cố định** — không cấu hình được ở mọi mức |

*Ghi chú phân loại: nhãn **"[sàn bắt buộc]"** trong thân tài liệu có nghĩa duy nhất là **"quy tắc này có một phần không được nới lỏng"**. Nhãn đó **không** quyết định mức độ tự do ở bảng này — mức độ tự do phụ thuộc vào việc quy tắc có phần điều chỉnh được hay không: nếu có, quy tắc xuất hiện ở một dòng tham số với mức **Có sàn bắt buộc** (điều chỉnh được trong miền, không xuống dưới sàn); nếu **toàn bộ** quy tắc không có gì để điều chỉnh, nó xuất hiện ở hàng cuối bảng với mức **Cố định**. Vì vậy việc BR-19.8, BR-30.9, BR-30.10, BR-34.4, BR-36.3 và NFR-14 vừa mang nhãn `[sàn bắt buộc]` trong thân vừa nằm ở hàng Cố định là **nhất quán**: chúng là các quy tắc mà phần bắt buộc chính là toàn bộ nội dung. **Ba** quy tắc là trường hợp hỗn hợp — có mặt ở hàng Cố định cho phần không nới được, đồng thời có tham số riêng cho phần điều chỉnh được: **BR-33.7** (nghĩa vụ xác minh cố định · thời hạn biện pháp phòng ngừa qua `CFG-33-04`), **BR-33.8** (phạm vi xóa cố định · thời hạn lưu tệp nhập khẩu gốc qua `CFG-22-01`), và **BR-30.5** (nhóm Tiếp thị vĩnh viễn chịu chi phối `OPT_OUT` · việc nhóm Liên lạc 1-1 có chịu chi phối hay không thì cấu hình được qua `CFG-30-01`). Hai quy tắc BR-33.7 và BR-33.8 có phần cố định (nghĩa vụ, phạm vi) và phần cấu hình được (thời hạn) — hàng cuối chỉ khoá phần cố định, phần còn lại vẫn có tham số riêng ở các hàng trên. Điều kiện nghiệm thu #4 tại mục 10 áp dụng cho toàn bộ các tham số mang mức "Có sàn bắt buộc".*

**Quy tắc quản trị cấu hình:** Mọi thay đổi tham số được ghi nhật ký kiểm toán theo NFR-07 (ai đổi, đổi từ giá trị nào sang giá trị nào, thời điểm). Thay đổi chỉ áp dụng cho hành vi **từ thời điểm đổi trở đi**, không hồi tố dữ liệu đã xử lý. Các tham số đánh dấu "Có sàn bắt buộc" hiển thị rõ ngưỡng sàn trên giao diện cấu hình và hệ thống từ chối giá trị vi phạm sàn.

---

## 9. Lịch sử phiên bản (Version History)

| Phiên bản | Ngày | Người thực hiện | Nội dung thay đổi chính |
| --- | --- | --- | --- |
| v1.0 | — | Product Owner | Bản đặc tả As-Is đầu tiên dựa trên khảo sát hệ thống đang vận hành |
| v2.0 | 2026-08-28 | Product Owner / BA | Chuẩn hoá theo thông lệ B2B SaaS quốc tế; bổ sung nhóm tính năng Chuyển đổi Tiềm năng, Suy giảm điểm, Ánh xạ cột tự động; đóng băng bộ 30 tính năng và 6 kịch bản UAT |
| v2.1 | 2026-09-01 | BA (review chéo vòng 1) | Thống nhất số giai đoạn vòng đời; bổ sung FEAT-31/32 vào bảng tổng hợp và ma trận phân quyền; bổ sung 2 vai trò Marketing vào ma trận; thêm 3 kịch bản UAT (Score Decay, Lead Routing, Undo Conversion); làm rõ các mâu thuẫn nội bộ giữa các mục |
| v3.0 | 2026-09-01 | BA (review chéo vòng 2) | **Khắc phục toàn bộ điểm chặn ban hành vòng 2:** bổ sung Ma trận Chuyển đổi Giai đoạn (FEAT-12); bổ sung Ngưỡng điểm MQL/SQL và nguyên tắc chuyển giao Marketing–Sales (BR-15.5, BR-15.6); giải quyết xung đột cam kết hiệu năng (NFR-02, BR-28.2); định nghĩa 2 vai trò chức năng Account Manager và Data Steward; khép kín xử lý xóa doanh nghiệp trong mô hình đa liên kết (BR-09.1, BR-09.2); bổ sung nguyên tắc đồng thuận nghiêm ngặt nhất khi gộp (BR-19.6) và xung đột liên kết khi gộp (BR-19.7); bổ sung chống trùng chủ khi phân bổ Lead và cam kết thời gian phản hồi (BR-31.6, BR-31.7); thêm FEAT-33 Quyền Chủ thể Dữ liệu; thêm mục 2.4 Chỉ số thành công (8 KPI) và mục 2.5 Luồng nghiệp vụ đầu–cuối; bổ sung NFR-08 đến NFR-13 (khả dụng, sao lưu, giới hạn gói, đa ngôn ngữ); thêm 4 kịch bản UAT (tổng 13); thêm Phụ lục A Danh mục dữ liệu chuẩn; gắn người ra quyết định và thời hạn cho mục 7 |
| **v4.0** | **2026-09-01** | **BA (review chéo vòng 3 — 2 bản review độc lập song song)** | **Khắc phục 60 lỗi, gồm 6 lỗi mâu thuẫn nặng và 7 điểm chặn phát hành về nghiệp vụ vận hành.** *(a) Ma trận Chuyển đổi Giai đoạn:* bổ sung các bước chuyển tới `Opportunity` từ mọi giai đoạn tiền bán hàng và nguyên tắc bước chuyển do sự kiện Cơ hội bán hàng sinh ra là hợp lệ theo thiết kế (BR-12.9) — trước đó ma trận chặn chính tính năng Chuyển đổi 1-Click; bổ sung ngoại lệ Hoàn tác Chuyển đổi, ngoại lệ gian lận với khách hàng chính thức (BR-12.8), giai đoạn mặc định khi tạo mới (BR-12.10). *(b) Nghiệp vụ vận hành mới:* thêm Nhóm K với FEAT-34 Chuyển giao Quyền phụ trách & Bàn giao, FEAT-35 Chia sẻ Bản ghi & Đội ngũ Phụ trách (giải quyết bế tắc truy cập của tuyến Hỗ trợ), FEAT-36 Ghi chú & Ghi nhận Hoạt động. *(c) Chống mất dữ liệu:* chốt an toàn trước khi dọn thùng rác (BR-05.6), thời hạn hoàn tác gộp 90 ngày (BR-20.3), xác minh danh tính chủ thể dữ liệu và phạm vi xóa vs sổ cái (BR-33.7, BR-33.8). *(d) Bảo vệ dữ liệu khách hàng khi gộp:* nguyên tắc giai đoạn tiến xa nhất, quyền sở hữu, hợp nhất điểm/thẻ (BR-19.8 → BR-19.10). *(e) Tuân thủ:* phân loại 3 nhóm mục đích gửi tin để `OPT_OUT` không chặn thư dịch vụ (BR-30.5). *(f) Chống lead rác:* ngưỡng MQL yêu cầu điểm tương tác thật và hoãn thăng hạng cho dữ liệu nhập khẩu (BR-15.7). *(g) Bổ sung:* loại khách hàng B2B/B2C (BR-01.6), chính sách trùng lặp và mặt nạ dữ liệu theo quan hệ với bản ghi, vai trò DPO, **Phụ lục B Danh mục tham số cấu hình theo tenant**, 4 kịch bản UAT mới (tổng 17), sửa toàn bộ tham chiếu sai đích và danh mục lệch. |
| **v5.0** | **2026-09-01** | **BA (review chéo vòng 4 — soát đường khâu + mô phỏng vòng ký phê duyệt)** | **Khắc phục 55 lỗi của vòng 4 (5 Nặng) bằng phương pháp viết lại theo cụm** thay vì điểm-sửa, sau khi hai vòng trước cho thấy điểm-sửa tạo lỗi mới gần bằng tốc độ sửa. Mỗi cụm được gộp về **một nguồn chân lý duy nhất** và mọi nơi phụ thuộc trỏ về đó thay vì phát biểu lại. *(A) Che mặt nạ:* FEAT-04 viết lại thành nguồn chân lý duy nhất với 3 nhóm trường × 4 mức hiển thị × 4 quan hệ người xem; bổ sung cột riêng cho tuyến Hỗ trợ (trước đó BR-35.4 tự vô hiệu hoá mục đích của chính nó); NFR-06, BR-35.4, Kịch bản 1 và 6 trỏ về FEAT-04. *(B) Trùng lặp:* bổ sung lối khai báo Định danh dùng chung ngay tại màn hình cảnh báo, liệt kê 5 nguồn thực tế sinh bản ghi trùng (BR-17.2b) — trước đó chính sách chặn cứng đã xoá bỏ tiền đề của 3 kịch bản UAT và của BR-19.8; cam kết thời gian cho Yêu cầu quyền truy cập (BR-17.2c). *(C) Ma trận vòng đời:* tái cấu trúc thành 4 nguyên tắc chi phối + bảng liệt kê hệ quả; bổ sung 4 bước chuyển còn thiếu (`Evangelist→Disqualified`, `→SQL` nhánh không kèm Cơ hội, `Churned/Disqualified→Opportunity`, `→Subscriber` khi hoàn tác); bỏ 3 tuyên bố tuyệt đối đánh nhau với ngoại lệ. *(D) Đồng thuận:* 4 tiêu chí quan sát được cho nhóm Liên lạc 1-1 (BR-30.7) bịt kẽ hở lách `OPT_OUT`, mặc định an toàn khi thiếu khai báo nhóm (BR-30.9), giải quyết xung đột `RESTRICTED` với nguyên tắc 1. *(E) Nhật ký kiểm toán:* nhật ký không lưu giá trị thật của trường nhạy cảm, quyền đọc chỉ Chủ sở hữu + DPO và mọi lượt đọc đều ghi vết (NFR-14) — trước đó nhật ký là đường đi vòng qua toàn bộ chính sách mặt nạ. *(F) Quyền chủ thể dữ liệu:* xác minh phân tầng theo mức rủi ro (không còn từ chối trắng nhóm khách vãng lai), bổ sung 4 nơi lưu dữ liệu còn thiếu vào phạm vi xóa, mục đích và trần lưu 24 tháng cho định danh KYC, trần 18 tháng cho hồ sơ tạm. *(G) Cam kết thời gian Lead:* định nghĩa Lịch làm việc (BR-31.7b), 2 nhóm bằng chứng liên hệ gồm nhóm ngoài hệ thống có Quản lý xác nhận, và tắt thu hồi tự động khi tenant chưa tích hợp kênh. *(H) Gộp:* bảng 9 trường bị cưỡng chế tại BR-18.2, thứ tự so sánh khi có trạng thái ngoài phễu, xử lý vượt hạn mức. *(I) Nhóm K:* luồng yêu cầu–phê duyệt quyền (BR-35.3b), bàn giao ngang giữa đồng nghiệp (BR-34.1b), ghi nhận người xử lý thay (BR-34.8), ghi chú mặc định "Nội bộ đội bán hàng". *(J) Đo lường:* điều kiện đo chung cho nhóm NFR hiệu năng, điền đủ con số cho NFR-11, định nghĩa đo cho KPI-01/02/05/07, quy ước nghiệm thu các mốc thời gian dài. *(K)* Ba vấn đề cần pháp chế được ghi thành **điều kiện chặn ban hành** (#9, #10, #11) thay vì tự chốt. Thêm 3 kịch bản UAT (tổng 20), 10 tham số cấu hình (tổng 32), NFR-14, danh mục A.16 và giá trị `Obsolete` tại A.10. |
| **v5.1** | **2026-09-01** | **BA (review chéo vòng 5 — soát đường khâu + mô phỏng vòng ký)** | **Khắc phục 42 lỗi của vòng 5** (5 Nặng), trong đó 18 lỗi do chính lượt sửa v5.0 tạo ra theo cùng một khuôn: sửa nguồn chân lý nhưng bỏ sót nơi trỏ về nó. *Nội dung:* mở rộng cưỡng chế đồng thuận ra **mọi nguồn tác động** thay vì chỉ đường gộp (BR-30.10) — trước đó cam kết "`OPT_OUT` không thể ghi đè" vẫn hở ở nhập khẩu và chỉnh sửa thủ công; bổ sung nội dung hội thoại đa kênh và vé hỗ trợ vào phạm vi xóa dữ liệu kèm sàn đặt cho hai SRS liên quan (BR-33.8); kiểm soát mức hiển thị Đầy đủ ở cột (A) bằng nhật ký và hạn mức (BR-04.5b) — trước đó mức phơi bày cao nhất lại là mức duy nhất không có dấu vết; ba mức truy cập nhật ký kiểm toán và quy tắc thay thế người phê duyệt thứ hai khi tenant không có DPO (NFR-14); biện pháp phòng ngừa của BR-33.7 nay có thời hạn, thẩm quyền dỡ và bắt buộc thông báo. *Vận hành:* nới 2 tiêu chí của nhóm Liên lạc 1-1 để không chặn thư báo giá và thư theo dõi (BR-30.7); cho nhân viên loại nhanh Lead rác với hiệu lực đình chỉ đồng hồ cam kết (BR-12.4b); cấp quyền xuất dữ liệu theo phạm vi cho Nhân viên Kinh doanh (BR-25.5) và chuyển phê duyệt xuất lớn về đúng tuyến báo cáo; thêm hành động "Đề nghị gộp" (BR-17.3); chốt người xử lý thay và thông báo cho nhánh không khả dụng tự động (BR-34.6). *Nhất quán:* bổ sung `→ Customer` cho 5 dòng ma trận để bảng sinh ra đồng nhất từ nguyên tắc 1; giới hạn BR-16.4 khỏi `Customer`/`Evangelist`; liệt kê 3 ngoại lệ của BR-33.5; đồng bộ `CFG-04-01`, `CFG-36-01`, `CFG-36-03`; định nghĩa lại nguồn số liệu `KPI-02`, `KPI-06`; sửa tiền đề Kịch bản 4, 6, 8 và đánh số lại Kịch bản 8. Thêm 4 tham số cấu hình (tổng 36), BR-36.7, giá trị A.8 mới. |
| **v5.2** | **2026-09-01** | **BA (review chéo vòng 6 — kiểm tra hội tụ)** | **Khắc phục 34 lỗi của vòng 6** (9 Nặng), trong đó 9 lỗi do chính lượt sửa v5.1 tạo ra — tỷ lệ lỗi tự gây giảm từ 18 xuống 9 và toàn bộ lỗi Nặng dồn về đúng 4 cụm chứ không rải khắp tài liệu. *(A) Cụm quyền xuất dữ liệu:* chốt dứt điểm meta-quy tắc "ma trận thắng" bằng cách chia vai giữa ma trận (có/không có quyền) và BR (điều kiện, hạn mức, ngoại lệ), kèm nghĩa vụ mỗi ô ma trận có điều kiện phải dẫn chiếu mã BR; ô `FEAT-25` của Nhân viên Kinh doanh và Quản lý Kinh doanh viết lại theo BR-25.5; BR-25.4 chia ngưỡng phê duyệt theo vai trò để nhánh dành cho Nhân viên Kinh doanh không còn là quy tắc chết. *(B) Cụm mặt nạ cột (A):* tiêu đề cột (A) của bảng BR-04.3 mang luôn điều kiện "mức Chỉnh sửa"; Kịch bản 19 sửa lại kỳ vọng cho thành viên Chỉ đọc và bổ sung bước kiểm chốt chống đường vòng qua hạn mức. *(C) Cụm ma trận vòng đời:* bổ sung nhánh `→ Churned` "chỉ qua gộp" cho 7 dòng để BR-19.8 không còn sinh bước chuyển ngoài ma trận; BR-12.6 nêu đúng hai tình huống ưu tiên thay vì tuyên bố sai rằng mọi bước chuyển đã được liệt kê; thêm `Churned → Disqualified` theo BR-12.8; nguyên tắc 3 nêu sàn `Subscriber` để bảng sinh ra đồng nhất; BR-12.4b bỏ cơ chế mặc-định-chấp-thuận (vốn vượt sàn `CFG-12-01`) và giới hạn phạm vi giai đoạn để không chồng lên BR-12.8; BR-16.4 loại `Opportunity` khỏi đề xuất `Nurturing` để không đánh nhau với BR-12.3 về danh mục lý do. *(D) Cụm tuân thủ dữ liệu cá nhân:* thu miền `CFG-01-03` về 6–24 tháng, `CFG-33-03` về 6–18 tháng, `CFG-33-04` về 7–30 ngày — chấm dứt khuôn lỗi "sàn bắt buộc bị chính Phụ lục B nới lỏng" đã xuất hiện ba vòng liền; Kịch bản 17 kiểm chứng đủ 12 hàng của BR-33.8; cụm BR-17.3 / BR-17.2c / BR-35.3b viết lại một lượt cho ba hành động với người xử lý và hành vi quá hạn riêng theo từng loại; thư báo giá và thư xác nhận cuộc hẹn được liệt kê tường minh vào nhóm Giao dịch & Dịch vụ tại BR-30.5 nên không còn hai đáp án pháp lý cho cùng một loại thư. *Nhất quán:* NFR-07 thành nguồn chân lý duy nhất của danh mục sự kiện kiểm toán (thêm BR-30.10, BR-32.3b); BR-30.10 vào hàng Cố định của Phụ lục B; ô ma trận `FEAT-07` và `FEAT-36` đồng bộ với BR-07.4c và BR-36.7; `CFG-31-01`, `CFG-12-02`, `CFG-36-03` đồng bộ với quy tắc gốc; `KPI-03` có mục loại khỏi mẫu đo; BR-31.7b đủ danh sách quy tắc dùng giờ/ngày làm việc; quy ước thứ tự mã BR được ghi thành quy ước đọc. *Phủ sóng nghiệm thu:* bổ sung bước kiểm cho NFR-14, BR-25.5/BR-25.4, BR-12.4b và BR-34.6 vào Kịch bản 6, 12, 14 (giữ nguyên 20 kịch bản). Thêm vấn đề **#12 mục 7** về hai sàn liên tài liệu đặt cho `omnichat-srs.md` và `tickets-srs.md`. |
| **v5.3** | **2026-09-02** | **BA (review chéo vòng 7 — kiểm tra hội tụ)** | **Khắc phục 26 lỗi của vòng 7** (5 Nặng), trong đó 8 lỗi truy được về chính lượt sửa v5.2 — vòng này phát hiện một **loại lỗi tự gây kiểu mới**: tài liệu tự dựng tiêu chí ("nguồn chân lý duy nhất", "nghĩa vụ dẫn chiếu mã BR") rồi tự vi phạm ngay trong cùng phiên bản. *(A) Năm lỗi Nặng:* bổ sung giá trị "Yêu cầu chưa xác minh được danh tính" vào danh mục đóng A.8 — trước đó BR-33.7d bắt buộc dùng một giá trị **không tồn tại** và cấm đúng giá trị duy nhất còn lại, khiến DPO không ký được; BR-12.6 ngoại lệ (ii) mở lại thành **mọi** bước chuyển do gộp sinh ra thay vì chỉ nhánh `→ Churned` — lượt thu hẹp ở v5.2 đã biến 12 ô thiếu của ma trận từ chỗ mơ hồ thành mâu thuẫn tường minh; kèm lý do vì sao nhóm này đặt ngoài ma trận thay vì liệt kê từng ô; BR-25.4 chốt người phê duyệt cho **chính Chủ sở hữu Workspace** (DPO, hoặc quy tắc thay thế tại NFR-14) và nguyên tắc không ai tự phê duyệt lần xuất của mình — trước đó nhánh này là quy tắc chết chặn luôn nghĩa vụ xuất bản sao dữ liệu của FEAT-33; ngưỡng phê duyệt của Nhân viên Kinh doanh phát biểu lại thành **giá trị nhỏ hơn giữa `CFG-25-01` và `CFG-25-02`** nên không còn phụ thuộc vào cách tenant đặt tham số, kèm thu miền `CFG-25-02` về 200–5.000; thu miền `CFG-22-01` về 7–30 ngày. *(B) Ba sàn bị Phụ lục B nới lỏng — nay khoá:* cột (D) và cột (C) của `CFG-04-01` có trần hiển thị riêng (trước đó tenant đặt kênh liên lạc ở mức Đầy đủ cho người ngoài phạm vi dữ liệu là vô hiệu hoá cả BR-04.5 lẫn BR-04.5b); `CFG-30-02` thu về 1–10; ràng buộc chéo `CFG-05-01` ≤ `CFG-20-01` đưa vào miền giá trị thay vì chỉ nằm ở cột mô tả. *(C) Cụm ma trận vòng đời:* 4 nguyên tắc nay chỉ tuyên bố chi phối phễu tuyến tính, kèm bảng quy tắc riêng cho các bước ra/vào 3 trạng thái đặc biệt và lý do `→ Evangelist` chỉ đến từ `Customer`; bổ sung `Nurturing → Lead`; ô `FEAT-12` cột Marketing sửa lại theo Ghi chú 2 (lượt sửa này viện dẫn một mã BR không tồn tại và đã được v5.4 khắc phục). *(D) Nhất quán:* NFR-07 bổ sung đủ 6 sự kiện kiểm toán còn thiếu, trong đó có **nhật ký của nhật ký** do NFR-14 bắt buộc; bảng cưỡng chế khi gộp bổ sung thứ tự thắng cho giá trị `OBSOLETE`; BR-33.5 khai đủ **năm** ngoại lệ (bổ sung dọn Thùng rác theo BR-05.4 và tự xóa tệp đính kèm hết hạn); tiêu đề cột (B) bảng BR-04.3 và Kịch bản 19 bước 3 đồng bộ; `CFG-12-01` bổ sung sàn BR-12.8; sửa tham chiếu sai đích BR-28.2 → BR-28.1; đồng bộ tiêu đề Nhóm J; mục 2.4 giải thích vì sao có 8 chỉ số cho 5 vấn đề. *(E) Nghĩa vụ dẫn chiếu mã BR:* thay vì lặp mã BR ở hàng chục ô, bổ sung **Ghi chú 8 mục 5** định nghĩa một lần **từ vựng chuẩn của ma trận** (Scope gán / Scope phòng ban / Xem toàn bộ / Có quyền X) kèm quy tắc nguồn, và thu hẹp nghĩa vụ dẫn chiếu về đúng phần điều kiện vượt ra ngoài từ vựng chuẩn. *(F) Phủ sóng nghiệm thu:* thêm **Kịch bản 21** cho toàn bộ nhánh Hồ sơ Khách hàng Tạm và bốn mốc lưu trữ dài hạn chưa từng được kiểm (90 ngày, 18 tháng, 24 tháng, 36 tháng), mỗi mốc kèm bước thử đặt giá trị vi phạm sàn; Kịch bản 17 bổ sung quan sát cho hai hàng còn thiếu của BR-33.8. Tổng **21 kịch bản UAT**. |
| **v5.4** | **2026-09-02** | **BA (review chéo vòng 8 — kiểm tra hội tụ)** | **Khắc phục 28 lỗi của vòng 8** (4 Nặng), trong đó 6 lỗi truy được về chính lượt sửa v5.3. Vòng này ghi nhận **mốc đầu tiên**: ma trận vòng đời được dựng lại từ các nguyên tắc và **trùng khít bảng thật, không ô thiếu không ô thừa** sau 5 vòng sai liên tiếp; toàn bộ phép đếm tự khai đều đúng; cả 36 giá trị mặc định Phụ lục B đều khớp quy tắc gốc; NFR-07 thật sự là nguồn chân lý duy nhất. *(A) Bốn lỗi Nặng:* xoá mã `BR-02.2b` không tồn tại ở ô ma trận `FEAT-12` và **cắt tham chiếu vòng** giữa BR-02.2(b) với ma trận — BR-02.2(b) nay tự liệt kê ba vai trò không có quyền chuyển giai đoạn thủ công thay vì trỏ ngược về ma trận; `CFG-04-01` bổ sung trần cho **cột (B)** — lỗ hổng lớn hơn cái v5.3 vừa bịt cho (C)/(D), vì cột (B) với vai trò Marketing là **toàn tổ chức**, đặt lên "Đầy đủ" là vô hiệu hoá cả BR-04.5 lẫn BR-04.5b và làm `KPI-06` mất nguồn phát hiện; nay **chỉ cột (A) được nhận mức Đầy đủ**; Kịch bản 21 bước 13 sửa "bốn ngoại lệ" thành **năm**, khớp BR-33.5 và bổ sung quan sát cho ngoại lệ thứ năm; Kịch bản 8 bước 8 thu phạm vi về **nhánh điểm nguội**, hết đánh nhau với BR-12.3 và Kịch bản 12 về việc ai đưa bản ghi sang `Nurturing`. *(B) Hai quy tắc mới lấp chỗ trống:* **BR-12.5b** định nghĩa Chiến dịch Win-Back — cụm từ được viện dẫn ba nơi mà không nơi nào nói ai phê duyệt; **BR-16.5** định nghĩa đường quay lại `Lead` từ `Nurturing`, thay cho việc viện dẫn nguyên tắc 3 vốn chỉ chi phối phễu tuyến tính. Thêm danh mục **A.17** (Lý do Mở lại bản ghi đã bị Loại) — ma trận bắt buộc "ghi lý do mở lại" nhưng Phụ lục A chưa có tập giá trị nào. *(C) Ba ràng buộc chéo đưa vào miền giá trị:* `CFG-33-01` < `CFG-33-03` (nếu không, trần khử định danh nổ trước khi hồ sơ kịp vào danh sách rà soát, làm chết nhánh thứ hai của BR-33.6); `CFG-25-01` nâng sàn lên 5.000 để không giao với `CFG-25-02`; `CFG-16-01` buộc hai mốc suy giảm theo thứ tự tăng dần. *(D) Nhất quán:* BR-12.8 phủ thêm `Churned` để chín giai đoạn được chia trọn giữa BR-12.4 và BR-12.8; BR-28.1 và BR-29.2 bổ sung nội dung mà nơi khác đã giả định chúng có; BR-12.4b bắt buộc ghi nhật ký và vào danh mục NFR-07; BR-31.7 gỡ ngưỡng 85 đóng cứng, trỏ về `CFG-15-01`; bảng FEAT-33 chốt người tiếp nhận **được** gắn `RESTRICTED` ngay, để cam kết "Tức thì" có người thực thi; bốn ô ma trận và bốn dòng điều kiện `→ Nurturing` bổ sung dẫn chiếu mã BR; A.11 gỡ nhãn "mặc định" mâu thuẫn BR-30.4; đồng bộ tên `FEAT-12` ở ba nơi; thêm quy ước thứ tự kịch bản. *(E) Phủ sóng nghiệm thu:* Kịch bản 10 bổ sung bước kiểm hai sàn bắt buộc chưa từng được kiểm — mặc định an toàn khi thiếu khai báo nhóm mục đích (BR-30.9) và trần người nhận của nhóm Liên lạc 1-1 (BR-30.7b, `CFG-30-02`). Tổng **18 danh mục** Phụ lục A. |
| **v5.5** | **2026-09-02** | **BA (review chéo vòng 9 — kiểm tra hội tụ)** | **Khắc phục 25 lỗi của vòng 9** (5 Nặng), toàn bộ sửa được **cục bộ tại đúng một chỗ mỗi lỗi**, không phải viết lại cụm nào — vòng thứ hai liên tiếp ma trận vòng đời được dựng lại độc lập và **trùng khít bảng thật**. *(A) Năm lỗi Nặng:* NFR-07 bổ sung sự kiện **phê duyệt Chiến dịch Win-Back** mà BR-12.5b (quy tắc mới của v5.4) bắt buộc ghi nhật ký nhưng danh mục chưa có; Kịch bản 21 bước 4 sửa miền `CFG-33-01` cho khớp Phụ lục B — lượt thu miền ở v5.4 đã quên nơi nghiệm thu; **A.1 chia thành hai nhóm có nhãn** (Thương mại / Gian lận & Dữ liệu không hợp lệ) để cụm từ "nhóm gian lận" mà BR-12.8 bắt buộc dùng có tập giá trị thật — cùng khuôn lỗi mà A.17 vừa sửa ở v5.4 nhưng còn sót ở quy tắc liền kề; chốt dứt điểm **quyền gắn `RESTRICTED`**: người tiếp nhận được gắn ngay, ô ma trận và Ghi chú 5 sửa theo, để cam kết "Tức thì" có người thực thi; **cấm tuyệt đối chức năng Marketing xuất trường Định danh KYC** — BR-25.4 trước đó mở đường phê duyệt cho Marketing, vô hiệu hoá chính giới hạn mục đích của BR-01.5b ngay tại điểm dữ liệu rời khỏi hệ thống; nay chỉ Quản trị viên/Chủ sở hữu xuất được và bắt buộc có DPO đồng phê duyệt. *(B) Ba danh mục và quy tắc được làm sạch:* thêm **A.18** (Lý do Quay lại Phễu từ Nuôi dưỡng) vì BR-16.5 đang mượn A.3 — danh mục hạ hạng có nghĩa ngược hẳn; bảng mức ưu tiên Lead tại BR-31.7 gỡ nốt hai dải điểm đóng cứng, ba dải nay luôn sinh lại từ `CFG-15-01` nên không chồng lấn khi tenant hiệu chỉnh ngưỡng; định nghĩa thống nhất **sáu giai đoạn tiền bán hàng** (gồm `Nurturing`) để BR-12.4b và BR-12.8 không đếm hai tập khác nhau. *(C) Phụ lục B:* `CFG-12-02` từ mức Tự do thành có sàn — trước đó tenant đặt được giai đoạn khởi tạo là `Customer`, vô hiệu toàn bộ phễu ngay từ điểm nhập liệu; ràng buộc `CFG-33-01` < `CFG-33-03` nay quy đổi đơn vị tường minh (1 tháng = 30 ngày) và thu miền về 30–150 ngày để kín cả điểm cực biên; hàng "Cố định" tách rõ phần cố định và phần cấu hình được của BR-33.7, BR-33.8. *(D) Nhất quán:* Ghi chú 3 mục 5 viết lại để phủ cả `FEAT-16` và định nghĩa ký hiệu "*Hệ thống*"; 10 ô ma trận bổ sung dẫn chiếu; cột (C) bảng BR-04.3 phủ thêm quyền đọc tạm tự cấp theo BR-17.2c; NFR-10 nêu chu kỳ cuốn vòng 35 ngày mà BR-33.8 đang dựa vào; `KPI-03` nêu đúng hiệu lực dừng đồng hồ của `RESTRICTED`; sửa 4 tham chiếu trỏ sai đích; Điều kiện nghiệm thu #2 định nghĩa hai mức lỗi Nghiêm trọng/Cao; thêm quy ước thứ tự vấn đề ở mục 7. *(E) Phủ sóng nghiệm thu — thay đổi lớn nhất của vòng này:* thêm **Kịch bản 22** liệt kê **toàn bộ 29 tham số mức "Có sàn bắt buộc"** kèm ba phép thử chuẩn (vượt miền / tìm lựa chọn "không giới hạn" / vi phạm nội dung sàn) và nội dung sàn cụ thể phải kiểm cho từng tham số. Trước đó Điều kiện nghiệm thu #4 đòi kiểm mọi sàn nhưng bộ kịch bản chỉ phủ khoảng một phần ba, khiến QA Lead không ký được. Tổng **22 kịch bản UAT**, **19 danh mục** Phụ lục A. |
| **v5.6** | **2026-09-02** | **BA (review chéo vòng 10 — kiểm tra hội tụ)** | **Khắc phục 26 lỗi của vòng 10** (5 Nặng), trong đó **4 lỗi do chính lượt sửa v5.5 tạo ra và 3 lỗi nằm gọn trong Kịch bản 22 vừa thêm** — bài học của vòng này: một kịch bản nghiệm thu bao trùm 29 tham số là hạng mục dễ tự sinh mâu thuẫn nhất, vì nó phải phát biểu lại nội dung sàn của từng tham số. *(A) Năm lỗi Nặng:* Kịch bản 21 bước 4 sửa kỳ vọng ở **điểm cực biên** — lượt thu miền `CFG-33-01` ở v5.5 đặt trần 150 ngày với lập luận "mọi tổ hợp đều hợp lệ", nhưng kịch bản lại kỳ vọng hệ thống từ chối đúng cặp giá trị đó; tách bạch **quy ước nới sàn trên môi trường phi sản xuất** (đầu mục 6) khỏi **Kịch bản 22** — một bên đòi môi trường nghiệm thu chấp nhận giá trị dưới sàn, bên kia đòi chứng minh không đặt được giá trị dưới sàn, nên Kịch bản 22 nay bắt buộc chạy trên môi trường mang cấu hình sản xuất; dòng `CFG-05-02` của Kịch bản 22 bỏ việc coi BR-02.2 là ràng buộc `[sàn bắt buộc]` (nó không mang nhãn đó) và thêm **một ô đối chứng phải được chấp nhận**; `CFG-12-01` đổi người được thay đổi từ "Product Owner cấp tenant" — **vai trò không tồn tại trong bộ 11 actor** — sang Chủ sở hữu Workspace kèm Quản lý Kinh doanh (v5.8 rút gọn tiếp về "Quản lý Kinh doanh" khi định nghĩa dấu "+", vì Chủ sở hữu vốn đã bao trùm); Kịch bản 22 bước 6 bỏ yêu cầu ghi nhật ký cho **lượt bị từ chối**, vốn đòi một sự kiện ngoài danh mục đóng của NFR-07 và một trường dữ liệu không tồn tại trong bản ghi nhật ký. *(B) Danh mục và quy tắc:* A.1 nêu rõ **số lượng giá trị từng nhóm** và bảng ai dùng nhóm nào (BR-12.4 dùng cả 8, BR-12.4b dùng 2, BR-12.8 dùng 3) — trước đó chú thích khai "tập hai giá trị" nhưng liệt kê ba; BR-30.6 bổ sung **đồng hồ cam kết** vào danh sách Dừng để khớp `KPI-03`, và bổ sung **đường dỡ `RESTRICTED`** trong tình huống thông thường, trước đó chỉ có đường dỡ sớm cho biện pháp phòng ngừa nên một khách đổi ý bị đóng băng vĩnh viễn; BR-14.2 và BR-30.3 gọi đúng tên danh mục và tên giá trị. *(C) Ma trận mục 5:* ô `FEAT-12` cột Quản lý Kinh doanh bổ sung hai thẩm quyền mới mà v5.4–v5.5 vừa giao (`Nurturing → Lead` theo BR-16.5, đồng phê duyệt Win-Back theo BR-12.5b); ô cột Quản lý Marketing bổ sung dẫn chiếu; ba ô `FEAT-15` sửa dẫn chiếu trỏ sai đích. *(D) Kịch bản 22 làm lại 8 dòng:* bốn dòng ràng buộc chéo đổi từ "kỳ vọng từ chối" — phép thử không thực thi được vì miền đã đóng kín — sang **chứng minh miền đã đóng**; ba dòng nêu sai nội dung sàn (`CFG-04-03`, `CFG-31-04`, `CFG-36-02`) sửa về đúng nghĩa vụ mà Phụ lục B khai; tiền đề bỏ giả định một tài khoản chung cho cả bảng. *(E) Nhất quán khác:* Phụ lục B thêm **Quy ước thẩm quyền** (Chủ sở hữu bao trùm mọi vai trò cấp dưới) để cột "Người được thay đổi" không đánh nhau với ma trận; `CFG-12-02` mở miền cho giá trị "chưa gán giai đoạn"; quy ước thứ tự mã BR phủ thêm trường hợp quy tắc trình bày ở tính năng khác; sửa "10 vai trò" ở phần mở đầu; mục 7 gọi đúng nội dung `KPI-06` và không gọi tên một vai trò ngoài mô hình phân quyền. |
| **v5.7** | **2026-09-02** | **BA (review chéo vòng 11 — kiểm tra hội tụ)** | **Khắc phục 14 lỗi của vòng 11** (3 Nặng) — vòng đầu tiên số lỗi giảm mạnh (26 → 14) và số lỗi Nặng xuống dưới 4 (5 → 3). Ba nhóm kiểm cho kết quả **sạch tuyệt đối**: ma trận vòng đời (dựng lại độc lập, trùng khít lần thứ ba liên tiếp), nguồn chân lý về che mặt nạ, và năm ngoại lệ tự động xóa. *(A) Ba lỗi Nặng:* dòng `CFG-33-01` của Kịch bản 22 đổi từ "kỳ vọng từ chối" — phép thử không thực thi được vì miền đã đóng kín — sang **chứng minh miền đã đóng**, hết nói ngược Kịch bản 21 bước 4 (lượt sửa v5.6 đã đổi bốn dòng cùng loại nhưng bỏ sót dòng này); tiền đề Kịch bản 22 viết lại theo **Quy ước thẩm quyền**: chạy bằng **vai trò thấp nhất** chứ không phải Chủ sở hữu, vì Chủ sở hữu bao trùm nên luôn đổi được và sẽ không phát hiện được lỗi phân quyền — trước đó tiền đề phủ định chính quy ước vừa thêm ở cùng phiên bản; NFR-07 bổ sung sự kiện **đọc hồ sơ ngoài phạm vi gán bởi vai trò có tầm nhìn toàn tổ chức**, để cam kết "Marketing chỉ đọc **có ghi nhật ký**" tại vấn đề #5 mục 7 có sự kiện kiểm toán chống lưng — sự kiện chỉ ghi các lượt đọc ngoài phạm vi gán để nhật ký không phình theo thao tác thường ngày. *(B) Vận hành và phân quyền:* BR-30.6 tách **thẩm quyền dỡ `RESTRICTED` theo nhánh** — chủ thể rút yêu cầu thì một người, dỡ sớm biện pháp phòng ngừa thì hai người theo BR-33.7b; ô ma trận `FEAT-12` cột Nhân viên bổ sung thẩm quyền **đánh dấu "Lead rác"** vốn chưa xuất hiện ở ô nào; ô `FEAT-33` và dòng Actor của FEAT-12 sửa dẫn chiếu. *(C) Phụ lục B:* định nghĩa dứt khoát dấu **"+"** trong cột "Người được thay đổi" luôn mang nghĩa "và", kèm ngoại lệ khi vai trò thứ hai là Quản trị viên/Chủ sở hữu (vốn đã bao trùm) — trước đó bốn dòng dùng dấu này không có định nghĩa, và `CFG-15-01` đọc nguyên văn thì Quản lý Marketing không tự lưu được ngưỡng điểm, ngược BR-15.4. *(D) Kịch bản 22:* dòng `CFG-04-04` sửa về đúng nội dung sàn (nghĩa vụ ghi nhật ký, không phải trần số lượng); dòng `CFG-04-01` bổ sung ngoại trừ Nhóm 3 để không ngược sàn (i). *(E) Nhất quán:* BR-02.2 nêu rõ lệnh cấm ba vai trò chuyển giai đoạn thủ công là **mặc định chuẩn hệ thống, không phải sàn bắt buộc**, để không đánh nhau với ô đối chứng của Kịch bản 22; BR-12.8 sửa phép đếm chín giai đoạn và nói rõ vì sao `Disqualified` nằm ngoài; ghi chú lịch sử soát xét sửa lại chuỗi số lỗi cho khớp mục 9. |
| **v5.8** | **2026-09-02** | **BA (review chéo vòng 12 — kiểm tra hội tụ)** | **Khắc phục 15 lỗi của vòng 12** (3 Nặng), trong đó **4 lỗi do chính lượt sửa v5.7 tạo ra**. Vòng đầu tiên **năm nhóm kiểm sạch tuyệt đối**: ma trận vòng đời (dựng lại độc lập, trùng khít), FEAT-04 là nguồn chân lý duy nhất về che mặt nạ, NFR-07 phủ đủ 26 sự kiện không thao tác nào lọt ngoài, BR-33.5 đúng năm ngoại lệ, và nghĩa vụ dẫn chiếu mã BR ở toàn bộ 36 dòng × 7 cột ma trận. *(A) Ba lỗi Nặng:* Quy ước thẩm quyền Phụ lục B bổ sung **nhánh thay thế người phê duyệt thứ hai** theo NFR-14, áp cho mọi vai trò phê duyệt thứ hai chứ không riêng DPO — trước đó một tenant chưa chỉ định DPO, hoặc có Quản trị Chất lượng Dữ liệu do Quản trị viên kiêm nhiệm, sẽ không đổi được 15 tham số có sàn pháp lý và Điều kiện nghiệm thu #4 không chạy được; Kịch bản 19 bước 4b đo lại **đúng đại lượng** của hạn mức `CFG-04-04` — quy tắc đếm theo **người được thêm vào** (mức phơi bày tích tụ của người nhận quyền) còn kịch bản đang đếm theo người đi thêm, tức toàn bộ nghiệm thu của sàn chống đường vòng qua hạn mức mở khoá đo sai đại lượng; nay kiểm bằng hai phép đo đối chứng; tiền đề Kịch bản 6 bước 1b đổi vai trò từ Nhân viên Kinh doanh sang **Quản lý Kinh doanh cùng đơn vị tổ chức** — dưới định nghĩa "Scope gán" tại Ghi chú 8, tập "trong phạm vi dữ liệu nhưng không phụ trách" của một Nhân viên Kinh doanh là **rỗng**, nên cột (B) của bảng che mặt nạ không có ca kiểm thử thực thi được. *(B) Hai trục quyền được tách bạch:* Quy ước thẩm quyền nêu rõ cột "Người được thay đổi" là **trục quyền cấu hình cấp không gian làm việc, độc lập với ma trận mục 5** vốn là trục quyền trên dữ liệu nghiệp vụ — giải thích vì sao một Quản lý Kinh doanh chỉ có "Scope phòng ban" trên dữ liệu vẫn đặt được mốc thời hạn phản hồi Lead cho cả tenant. *(C) Ghi chú phân loại Phụ lục B viết lại:* nhãn `[sàn bắt buộc]` nay chỉ có nghĩa "quy tắc có một phần không nới lỏng được" và **không** quyết định mức độ tự do — sáu quy tắc vừa mang nhãn vừa nằm ở hàng Cố định là nhất quán, vì phần bắt buộc của chúng chính là toàn bộ nội dung. *(D) Vận hành:* Ghi chú 5 bổ sung ngoại lệ **rút lại đồng thuận** cho tuyến tiếp nhận, để cam kết "Tức thì" của loại yêu cầu này có người thực thi đúng như đã làm với `RESTRICTED` — trước đó Ghi chú 5 khoá cho riêng Quản trị viên trong khi BR-30.10 cho mọi vai trò nghiệp vụ hạ mức đồng thuận tự do; ô ma trận `FEAT-12` bổ sung thẩm quyền `→ Nurturing` mà BR-16.4 và BR-12.3 giao đích danh cho Quản lý Kinh doanh; BR-19.6 mở phạm vi chặn gộp sang **bất kỳ bản ghi nào trong cặp** cho khớp BR-33.4. *(E) Nghiệm thu và trình bày:* tiền đề Kịch bản 22 phủ trường hợp hai vai trò ngang cấp; Kịch bản 17 tách quan sát (d) thành hai để đủ 12 quan sát cho 12 hàng; tiêu đề cột bảng Kịch bản 22 nêu đúng loại phép thử; NFR-07 viết lại lý do phạm vi của sự kiện đọc hồ sơ; ghi chú lịch sử soát xét sửa chuỗi số lỗi tự gây cho khớp từng dòng mục 9. |
| **v5.9** | **2026-09-02** | **BA (review chéo vòng 13 — kiểm tra hội tụ)** | **Khắc phục 7 lỗi của vòng 13** (2 Nặng), trong đó **4 lỗi do chính lượt sửa v5.8 tạo ra** — số lỗi giảm còn **một nửa** vòng trước (15 → 7) và **mọi lỗi còn lại dồn về đúng một nơi: ô phân quyền tại mục 5**. **Tám** nhóm/tuyên bố cho kết quả **sạch tuyệt đối**: Phụ lục B (36/36 mặc định khớp, ba ràng buộc chéo đóng kín kể cả điểm cực biên), ma trận vòng đời (dựng lại độc lập, trùng khít), FEAT-04, NFR-07 (26 sự kiện, không thao tác nào lọt ngoài), BR-33.5, Ghi chú 8, Quy ước thẩm quyền và Ghi chú phân loại Phụ lục B. *(A) Hai lỗi Nặng — cả hai là ô ma trận nói ngược quy tắc trong chính tính năng đó:* ô `FEAT-33` của tuyến tiếp nhận nay ghi rõ **hai loại yêu cầu thực thi được ngay** (gắn `RESTRICTED` và hạ đồng thuận xuống `OPT_OUT`) — trước đó ô chỉ trừ ra một ngoại lệ, Ghi chú 5 trừ ra hai, và dòng Actor lại không trừ ngoại lệ nào, thành **ba đáp án** cho cùng một cam kết "Tức thì"; ô `FEAT-34` cột Nhân viên đổi từ **"—" (không có quyền)** sang thẩm quyền **bàn giao ngang cho đồng nghiệp cùng nhóm** (BR-34.1b) và **tự khai báo nghỉ phép** (BR-34.6) — trước đó ma trận phủ định đúng quyền mà quy tắc trong chính FEAT-34 trao cho Người phụ trách, và toàn bộ lập luận chống-lách của BR-34.1b không có ca kiểm thử nào. *(B) Ô `FEAT-12` cân lại hai chiều:* bỏ nhánh `→ Nurturing` khi toàn bộ Cơ hội `Closed Lost` — nhánh này do **hệ thống tự chuyển** theo BR-12.3, không phải thẩm quyền của ai; bổ sung thẩm quyền **mở lại bản ghi `Disqualified`** kèm lý do từ A.17, vốn được ma trận vòng đời giao đích danh cho Quản lý Kinh doanh trong khi danh mục A.17 tồn tại chỉ để phục vụ nó. *(C) Phạm vi vai trò của BR-30.10 mở đúng bằng thực tế:* quy tắc nay nêu **mọi vai trò nghiệp vụ có quyền ghi trên bản ghi**, gồm cả Nhân viên Hỗ trợ khi tiếp nhận yêu cầu — trước đó Ghi chú 5 viện dẫn BR-30.10 với phạm vi rộng hơn phạm vi mà chính quy tắc đó khai. *(D) Số liệu và trình bày:* dòng v5.8 bổ sung con số lỗi tự gây để chuỗi ở đầu tài liệu có nguồn; Kịch bản 6 bước 8 gọi đúng tên nhân vật đã đổi vai trò ở v5.8. |
| **v6.0** | **2026-09-02** | **BA (review chéo vòng 14 — soát từng ô ma trận phân quyền)** | **Khắc phục 10 lỗi của vòng 14** (2 Nặng), trong đó 4 lỗi do chính lượt sửa v5.9 tạo ra. Vòng này soát **từng ô của cả 36 dòng × 7 cột** ma trận mục 5 với ba câu hỏi cho mỗi ô: ô có nói ngược quy tắc trong thân tính năng không, có liệt kê thẩm quyền mà không quy tắc nào giao không, có bỏ sót thẩm quyền được giao đích danh không. **Tám** nhóm/tuyên bố tiếp tục sạch tuyệt đối, và **nhóm đường khâu BR/NFR cũng sạch lần đầu**. *(A) Hai lỗi Nặng — cả hai ở cột Nhân viên Hỗ trợ:* BR-35.4a mở **ngoại lệ ghi duy nhất** cho quyền đọc tự động của tuyến Hỗ trợ — gắn `RESTRICTED` và hạ đồng thuận xuống `OPT_OUT` — vì lượt sửa v5.9 trao hai thẩm quyền này qua ma trận mà không đụng tới quy tắc tuyên bố "chỉ đọc, không cho sửa", tạo hai đáp án cho đúng cam kết "Tức thì" mà nó muốn cứu; ngoại lệ có phạm vi (chỉ khi vé/hội thoại còn mở), có lý do (chỉ thu hẹp phạm vi xử lý, đảo lại được) và có nhật ký; ô `FEAT-34` mở thẩm quyền **tự khai báo nghỉ phép** cho ba cột còn ghi "—" (Nhân viên Hỗ trợ, Nhân viên và Quản lý Marketing) — BR-34.6 trao quyền đó cho "chính người dùng" không giới hạn vai trò, và lượt sửa v5.9 chỉ chạm đúng một cột. *(B) Bốn ô ma trận cân lại:* `FEAT-30` cột Quản lý Marketing bỏ "Toàn quyền" (vốn hàm ý gồm cấu hình) vì hai tham số của chính tính năng đó thuộc Chủ sở hữu + DPO; `FEAT-25` cột Chủ sở hữu bổ sung thẩm quyền duyệt xuất lớn cho Quản trị viên và các vai trò quản lý; `FEAT-12` cột Nhân viên Hỗ trợ đổi từ "—" sang quyền xem giai đoạn trong Ngữ cảnh Khách hàng (BR-28.1); ô `FEAT-33` đếm đúng **ba** loại yêu cầu còn lại thay vì bốn. *(C) Nhất quán:* Kịch bản 6 bước 1b viết lại lý do chọn vai trò cho khớp định nghĩa "Scope gán" tại Ghi chú 8; dòng v5.6 và v5.9 mục 9 bổ sung số liệu và mô tả đúng hiện trạng. |
| **v6.1** | **2026-09-02** | **BA (review chéo vòng 15 — soát từng ô ma trận, trọng tâm cột Nhân viên Hỗ trợ)** | **Khắc phục 11 lỗi của vòng 15** (2 Nặng), trong đó 4 lỗi do chính lượt sửa v6.0 tạo ra. **Nhóm đường khâu BR/NFR sạch tuyệt đối** (phạm vi vai trò, giai đoạn, bản ghi, thời gian ở mọi nơi viện dẫn đều khớp gốc), cùng Phụ lục B, ma trận vòng đời và sáu trong bảy tuyên bố tự đặt tiêu chí. *(A) Hai lỗi Nặng — cả hai ở tuyến Hỗ trợ:* Kịch bản 15 bước 4 bỏ tuyên bố tuyệt đối "không sửa được hồ sơ" và thêm **bước 4b** nghiệm thu đúng ngoại lệ ghi mà v6.0 vừa mở — trước đó kịch bản đòi hệ thống **chặn** đúng thao tác mà ma trận đòi hệ thống **cho phép**, hai kết quả nghiệm thu ngược nhau cho cùng một tình huống; `CFG-36-03` và BR-36.1 tách bạch **chặn theo vai trò** với **cấp theo tư cách thành viên** — một nhân viên hỗ trợ được thêm vào Đội ngũ phụ trách (vai trò "Hỗ trợ kỹ thuật" tại A.13, đúng như Kịch bản 19 dựng) đọc được ghi chú "Nội bộ đội bán hàng" **của riêng bản ghi đó**, vì đó là quyền cấp trên từng bản ghi, có nhật ký và thu hồi được, khác hẳn việc mở cho cả vai trò. *(B) Bảy ô ma trận cân lại:* `FEAT-30` và `FEAT-35` cột Nhân viên Hỗ trợ bổ sung hai thẩm quyền mà quy tắc giao theo **quan hệ với bản ghi** chứ không theo vai trò; `FEAT-05` và `FEAT-09` cột Quản lý Kinh doanh bổ sung điều kiện quyền `delete` cho khớp BR-05.3 và dòng Actor; ký hiệu "+ BR-35.4" chuyển từ `FEAT-07` (cây doanh nghiệp — quy tắc không cấp) sang `FEAT-10` và `FEAT-11` (panel liên kết của hồ sơ 360 — quy tắc có cấp). *(C) Phủ sóng nghiệm thu:* Kịch bản 14 thêm **bước 6b** cho thẩm quyền tự khai báo nghỉ phép của cả bốn vai trò vừa được mở, kèm hai ca đối chứng. *(D) Nhất quán:* dòng Actor của FEAT-33 và FEAT-34 cập nhật theo hai lượt sửa ma trận ở v5.9–v6.0; Ghi chú phân loại Phụ lục B đếm đúng **ba** quy tắc hỗn hợp (bổ sung BR-30.5); dòng v5.0 bổ sung số lỗi Nặng của vòng 4 để chuỗi ở đầu tài liệu truy được nguồn. |
| **v6.2** | **2026-09-02** | **BA (review chéo vòng 16 — soát khuôn "quyền theo quan hệ vs quyền theo vai trò")** | **Vòng 16 kết luận tài liệu ĐÃ HỘI TỤ: 0 lỗi mức Nặng**, sau khi soát trọng tâm đúng khuôn đã sinh ra sáu lỗi Nặng của ba vòng trước — các quy tắc trao quyền theo **quan hệ với bản ghi** ("chính người dùng", "Người phụ trách hiện tại", "thành viên Đội ngũ phụ trách", "người đang xử lý vé/hội thoại") trong khi ma trận phân quyền lại tổ chức theo **vai trò hệ thống**. Với từng quy tắc thuộc khuôn này, ba nơi được đối chiếu: ô ma trận, dòng Actor, và kịch bản UAT. Bốn nhóm **sạch tuyệt đối**: đường khâu BR/NFR, Phụ lục B, ma trận vòng đời, và toàn bộ 22 kịch bản UAT. *Khắc phục 7 lỗi Trung bình/Nhẹ còn lại (trong đó **1 lỗi do chính lượt sửa v6.1 tạo ra**):* **BR-35.1** nay quy định rõ vai trò hệ thống nào được thêm vào Đội ngũ phụ trách — mọi vai trò đều được, riêng Marketing chỉ với vai trò tham gia "Quan sát", vì hai vai trò này có tầm nhìn toàn tổ chức nên nếu tư cách thành viên mở thêm quyền đọc nội dung thương lượng thì lệnh cấm tại BR-36.1 và sàn `CFG-36-03` bị vô hiệu chỉ bằng thao tác thêm thành viên; BR-36.1 nêu tường minh hệ quả này. Bốn ô ma trận `FEAT-34`/`FEAT-35` cột Marketing bổ sung mệnh đề dự phòng "trên bản ghi mình phụ trách nếu có", thống nhất với cách diễn đạt đã dùng ở hai cột kia. Ba dòng Actor (FEAT-12, FEAT-30) và mô tả mặc định của `CFG-36-03` cập nhật theo các lượt sửa ma trận ở v6.0–v6.1; ô `FEAT-05` gọi đúng tên quy tắc; dòng v6.1 đếm đúng bảy ô. |
| **v6.3** | **2026-09-02** | **BA (review chéo vòng 17 — xác nhận hội tụ)** | **Vòng 17 xác nhận lại kết luận của vòng 16: tài liệu ĐÃ HỘI TỤ — 0 lỗi Nặng và 0 lỗi Trung bình**, chỉ còn 3 điểm Nhẹ về diễn đạt, và **lượt sửa v6.2 không phá vỡ hội tụ**. Vòng này truy ngược từng hạng mục của v6.2 rồi soát lại toàn bộ sáu nhóm. Năm nhóm **sạch tuyệt đối**: quy tắc trao quyền theo quan hệ với bản ghi (sáu quy tắc, đối chiếu đủ ba nơi — ô ma trận, dòng Actor, kịch bản UAT), bảy tuyên bố tự đặt tiêu chí, Phụ lục B, ma trận vòng đời, và 22 kịch bản UAT. *Khắc phục 3 lỗi Nhẹ:* **BR-35.1** viết lại mệnh đề về vai trò tham gia "Quan sát" để nêu rõ lệnh cấm đọc ghi chú nội bộ neo vào **vai trò hệ thống Marketing**, không neo vào vai trò tham gia — một thành viên không thuộc Marketing mang vai trò "Quan sát" vẫn đọc được; ghi chú lịch sử soát xét ở đầu tài liệu và dòng v6.2 đồng bộ lại hai con số tự khai. **Trạng thái tài liệu:** phần soạn thảo đã hoàn tất. Bốn việc còn lại thuộc về con người, không thuộc về soạn thảo — (1) văn bản xác nhận của Pháp chế cho ba điều kiện chặn ban hành #9, #10, #11; (2) quyết định chính thức hoặc xác nhận áp mặc định cho bảy vấn đề chính sách còn mở; (3) thu đủ 8 chữ ký; (4) chạy thực tế 22/22 kịch bản UAT, riêng Kịch bản 22 trên môi trường mang cấu hình sản xuất. |
| **v6.4** | **2026-09-02** | **BA (ghi nhận quyết định của chủ tài liệu)** | Ghi nhận quyết định **hoãn có chủ đích** hai nhóm việc thuộc về con người, do **chưa cần ở giai đoạn hiện tại**: (a) ba điều kiện chặn ban hành cần Pháp chế (#9, #10, #11 tại mục 7) và (b) toàn bộ vòng ký phê duyệt tại mục 10. **Không thay đổi bất kỳ quy tắc nghiệp vụ, tham số cấu hình, ma trận hay kịch bản UAT nào** — toàn bộ số liệu giữ nguyên: 36 tính năng, 141 quy tắc nghiệp vụ, 36 tham số cấu hình, 22 kịch bản UAT, 19 danh mục dữ liệu chuẩn, 14 NFR, 8 KPI. Bổ sung ba nội dung để trạng thái hoãn không bị hiểu nhầm thành "đã xong": **(1)** dòng **Trạng thái sử dụng** ở đầu tài liệu, nêu rõ giai đoạn hiện tại là căn cứ thiết kế & phát triển; **(2)** ranh giới **được phép / chưa được phép** dùng tài liệu trong thời gian hoãn (mục 7) — đặc biệt là chưa đưa các mốc thời hạn tại bảng FEAT-33 vào hợp đồng hay chính sách công bố ra ngoài cho tới khi #9 được Pháp chế xác nhận; **(3)** ghi rõ ba vấn đề chặn ban hành **không** áp cơ chế "quá hạn thì mặc định theo Đề xuất PM", vì nhóm soạn tài liệu không có thẩm quyền chốt thay Pháp chế. Hai điều kiện nghiệm thu số 5 và 6 được **đánh dấu hoãn, không bị xoá**. |

---

## 10. Phê duyệt & Ký ban hành (Approval & Sign-off)

Tài liệu chỉ có hiệu lực làm **căn cứ nghiệm thu chính thức** sau khi có đủ các phê duyệt dưới đây.

> **Trạng thái vòng ký — HOÃN CÓ CHỦ ĐÍCH.** Chủ tài liệu quyết định ngày **2026-09-02**: vòng ký **chưa cần thực hiện ở giai đoạn hiện tại**. Toàn bộ 8 dòng dưới đây giữ nguyên trạng thái **☐ Chờ ký** và được kích hoạt khi bước sang giai đoạn ban hành.
>
> Việc hoãn **không rút bớt dòng ký nào** — đủ 8 phê duyệt vẫn là điều kiện nghiệm thu số 5. Trong thời gian hoãn, tài liệu được dùng làm **căn cứ thiết kế và phát triển** theo đúng ranh giới nêu tại mục 7; mọi thay đổi nội dung vẫn phải được ghi vào bảng lịch sử phiên bản tại mục 9, để người ký sau này soát được chính xác bản mình đặt bút ký thay vì một bản đã trôi đi nhiều lượt sửa không dấu vết.

| Vai trò phê duyệt | Phạm vi chịu trách nhiệm xác nhận | Trạng thái | Ngày ký |
| --- | --- | :---: | --- |
| **Product Owner** | Toàn bộ phạm vi nghiệp vụ, bộ 36 tính năng, chỉ số thành công (mục 2.4), danh mục tham số cấu hình (Phụ lục B) | ☐ Chờ ký | |
| **Quản lý Kinh doanh** | Ma trận vòng đời (FEAT-12), cam kết thời gian phản hồi Lead (BR-31.7), quy tắc phân bổ (FEAT-31) | ☐ Chờ ký | |
| **Quản lý Marketing** | Ngưỡng điểm MQL/SQL (BR-15.5), nguyên tắc chuyển giao Marketing–Sales (BR-15.6), quy tắc đồng thuận (FEAT-30) | ☐ Chờ ký | |
| **Người phụ trách Bảo vệ Dữ liệu (DPO)** | Quyền chủ thể dữ liệu (FEAT-33), bằng chứng đồng thuận (BR-30.3), phân loại mục đích gửi tin (BR-30.5), phạm vi xóa vs sổ cái (BR-33.8), các tham số có sàn pháp lý tại Phụ lục B | ☐ Chờ ký | |
| **Trưởng nhóm Kiểm thử (QA Lead)** | Tính khả thi kiểm thử của 22 kịch bản UAT và các ngưỡng phi chức năng | ☐ Chờ ký | |
| **Trưởng nhóm Kỹ thuật** | Tính khả thi triển khai, các cam kết phi chức năng (mục 4) và khả năng cấu hình được của 36 tham số tại Phụ lục B | ☐ Chờ ký | |
| **Chủ sở hữu Không gian làm việc (đại diện khách hàng)** | Bộ giá trị mặc định của các tham số cấu hình (Phụ lục B), đặc biệt là phạm vi dữ liệu vai trò Marketing và chính sách che mặt nạ | ☐ Chờ ký | |
| **Chủ sở hữu tài liệu Phân quyền (IAM)** | Hợp đồng nghiệp vụ về chia sẻ bản ghi và thứ tự ưu tiên quyền (BR-35.5, mục 7 vấn đề #8) | ☐ Chờ ký | |

**Điều kiện nghiệm thu để phát hành (Exit Criteria):**
1. 100% kịch bản UAT (22/22) được thực thi, trong đó **toàn bộ** kịch bản liên quan tuân thủ dữ liệu cá nhân (Kịch bản 6, 10, 17, 21) và chống mất dữ liệu (Kịch bản 11, 14, 16, 20) phải đạt — không chấp nhận lỗi tồn đọng.
2. Không còn lỗi mức Nghiêm trọng (Critical) hoặc Cao (High) đang mở trên phân hệ. **Định nghĩa hai mức này để nghiệm thu**: *Nghiêm trọng* = lỗi làm mất dữ liệu khách hàng, làm lộ dữ liệu vượt mức hiển thị cho phép, vi phạm một quy tắc mang nhãn `[sàn bắt buộc]`, hoặc chặn hoàn toàn một tính năng `[Đã triển khai]` mà không có cách làm thay. *Cao* = lỗi làm sai số liệu của một `KPI` tại mục 2.4, làm một kịch bản UAT không thể hoàn thành, hoặc buộc người dùng thao tác ngoài hệ thống để hoàn thành công việc hằng ngày.
3. Toàn bộ vấn đề chính sách tại mục 7 chưa chốt (#1, #2, #3, #4, #7, #8, #12) đã có quyết định chính thức hoặc đã xác nhận áp dụng giá trị mặc định theo Đề xuất PM.
4. Toàn bộ 36 tham số tại Phụ lục B đã được cấu hình được trên môi trường thật, và các tham số "có sàn bắt buộc" đã được kiểm thử là **không thể** đặt giá trị vi phạm sàn — kiểm chứng bằng **Kịch bản 22**, vốn liệt kê từng tham số kèm phép thử tương ứng.
5. Đủ 8 phê duyệt trong bảng trên. *(Đang hoãn theo quyết định ngày 2026-09-02 — kích hoạt ở giai đoạn ban hành; điều kiện không bị xoá.)*
6. Ba vấn đề chặn ban hành cần pháp chế tại mục 7 (#9, #10, #11) đã có văn bản xác nhận của Pháp chế. *(Đang hoãn theo quyết định ngày 2026-09-02 — kích hoạt ở giai đoạn ban hành; điều kiện không bị xoá.)*
