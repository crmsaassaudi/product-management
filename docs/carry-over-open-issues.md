# Phần chưa làm được chuyển tiếp từ các issue đã đóng

Tài liệu này ghi lại, theo từng issue, những hạng mục **chưa được triển khai** khi issue được đóng (đợt đóng toàn bộ backlog IAM v5 / 7 phân hệ). Issue được đóng theo yêu cầu của chủ sản phẩm; phần dưới đây là danh sách việc còn nợ — mở issue mới từ đây khi đưa vào kế hoạch. Mỗi mục lấy từ phần "Chưa làm" của comment tiến độ cuối cùng, hoặc từ nội dung issue nếu chưa có triển khai nào.

> Ghi chú: phần web của #565, #603, #605, #608 đã được giao trong crm-web#361 (trừ bộ lọc hàng đợi trên dashboard #608 và socket realtime); các mục "web" trong những issue đó bên dưới nếu trùng thì coi như đã xong.

## #565 — Tickets: Đơn giá & thành tiền giờ tính phí, ẩn tiền theo quyền Duyệt, tệp bàn giao theo phạm vi

Chưa có triển khai nào. Nội dung issue:

## Mô tả

Phát hiện khi đối chiếu #504 (`AC-45.11.3`): giờ tính phí của vé chưa có đơn giá và thành tiền, nên chưa có giá trị tiền nào để ẩn theo `BR-45.11` / `BR-43.7`. Tệp bàn giao giờ tính phí cũng chưa giới hạn theo phạm vi.

**Quy tắc nghiệp vụ:** [`tickets-srs.md`](https://github.com/crmsaassaudi/product-management/blob/main/srs/tickets-srs.md) — `FEAT-33` (`BR-33.2`, `BR-33.3`), `BR-43.7`, `BR-45.11` (dòng "Đơn giá và thành tiền của giờ tính phí").

**Hiện trạng (crm-api):**
- `ticket_time_entries` chỉ có `durationMinutes`, `isBillable`, trạng thái duyệt. Không có đơn giá tại thời điểm ghi nhận (`BR-33.2`) và không có thành tiền.
- `GET tickets/time-entries/export-billing` chỉ cần `tickets:view` và trả mọi khoản giờ đã duyệt của cả workspace, không theo phạm vi vé của người gọi.
- Báo cáo giờ hỗ trợ và giờ tính phí (`BR-43.7`) chưa có.

**Phạm vi:**
- Đơn giá chốt lúc ghi nhận theo hợp đồng của khách; thành tiền = giờ × đơn giá.
- Đơn giá và thành tiền **ẩn hoàn toàn** với người không có quyền Duyệt giờ tính phí, ở mọi nơi: tab giờ, tệp bàn giao, báo cáo `BR-43.7`, tệp xuất.
- Tệp bàn giao giờ tính phí chịu phạm vi vé của người gọi.

## Tiêu chí hoàn thành
- **Bước đầu tiên — đối chiếu code với SRS:** liệt kê Đúng / Lệch / Thiếu thành comment trước khi sửa code.
- `AC-45.11.3`, `AC-43.7.1` có test và pass.
- Mọi chuỗi UI tuân thủ 100% i18n.

**Liên quan:** #504, #225

## #566 — Object Manager: Chống suy đoán giá trị bị che — tìm kiếm toàn cục, cảnh báo trùng, quy trình (phần còn lại của #551)

Chưa có triển khai nào. Nội dung issue:

Tách từ #551 (phần chưa làm của BR-03.5 / BR-09.1):

1. **Tìm kiếm toàn cục theo giá trị trường tuỳ biến** nhạy cảm/bị che/Ẩn với người tìm: `search-keys.plugin` gom mọi giá trị `customFields` vào `searchKeys` (không phân biệt trường), nên tìm "079…" tìm ra khách hàng theo số căn cước dù đã che. Cần chỉ mục theo từng trường (hoặc loại trường nhạy cảm khỏi chỉ mục chung) + backfill khi đổi cờ nhạy cảm/mẫu che.
2. **Cảnh báo trùng (BR-09.1)**: tiêu chí trùng trên trường bị che với người thực hiện (nhập tay/nhập tệp/tích hợp) không so khớp và không được nêu; bản ghi vào danh sách rà trùng cho người xem đầy đủ. Hiện `checkDuplicate` của Khách hàng/Doanh nghiệp so khớp email/điện thoại/mã số thuế và nêu tên/website bất kể quyền mở che.
3. **Quy trình tự động**: người tạo không được rẽ nhánh/điều kiện theo trường mà với họ bị che/Ẩn (chỉ còn có dữ liệu/trống) — hiện chỉ `update_field` được kiểm (`assertNoDataLaundering`).
4. Báo cáo: nhóm/đếm/tổng/trung bình theo trường tuỳ biến bị che (danh mục hiện loại sẵn trường nhạy cảm; trường bị che theo bố cục từng nhóm cần kiểm tra thêm).

Quy tắc: `object-manager-srs.md` BR-03.5, BR-09.1, Phụ lục C C.29/C.33/C.34.

## #567 — Object Manager: Tiến trình chạy thay người dùng — phạm vi system, lọc theo người nhận khi render, truy vết (phần còn lại của #554)

Chưa làm: phạm vi bản ghi runAs=system (cần quyết định sản phẩm), executor messaging/http, hành động tác nhân AI, nhật ký từng lần đọc/ghi trường bảo vệ.

## #568 — Object Manager: Đơn vị tiếp nhận cho Khu vực, Ma trận chuyển đổi, Công ty; trả về hàng đợi (phần còn lại của #556)

Chưa có triển khai nào. Nội dung issue:

Tách từ #556 (phần chưa làm của BR-09.5 / BR-09.4):

1. **Khu vực** (`<object>_territories`) và **Ma trận chuyển đổi** khai báo đơn vị tiếp nhận (mặc định theo loại nguồn "Khu vực"/"Ma trận chuyển đổi", chưa có thì không bật được, chỉ toàn quyền đổi); Ma trận tự vô hiệu khi mất đơn vị (AC-05.3.8).
2. **Công ty (Account)**: khai báo bản ghi chờ phân công + trường `intakeUnitId` + hàng đợi (hiện chỉ Khách hàng và Cơ hội).
3. **Gỡ Công việc khỏi phân công tự động** theo `tasks-srs.md` BR-13.6 — cần làm rõ với người viết SRS phạm vi (`CFG-09-04` trong registry hiện là tham số khác); gỡ `Task` ở `record-auto-assignment.listener`, `assignment.types`, adapters, seeder.
4. **Trả về hàng đợi** (e): chuyển phòng / tạm ngưng / rời workspace với Cơ hội do quy tắc gán, Công ty; nhánh "hàng đợi hiện tại của quy tắc" trong `queue-return.ts`; `queueable` trong `offboarding-steps.ts`.
5. Tự nhận từ hàng đợi (d) cho Khách hàng do quy tắc đẩy vào (đường nhận Cơ hội đã có ở `deals.service`).

## #569 — Object Manager: Trần Gán khi chạy và hàng đợi của ma trận chuyển đổi (phần còn lại của #557)

Chưa có triển khai nào. Nội dung issue:

Tách từ #557 (phần chưa làm của BR-09.3 / BR-05.3):

1. **Trần Gán của người cấu hình được kiểm lại khi chạy**: lưu người tạo/sửa quy tắc (và ma trận), khi ô Gán của họ thu hẹp hoặc người nhận ra ngoài ô thì tạm dừng quy tắc, bản ghi vào hàng đợi, thông báo.
2. **Ma trận chuyển đổi**: người nhận thực tế không đạt (không hoạt động, ngoài ô Gán/Xem) → Cơ hội vào hàng đợi của đơn vị tiếp nhận của ma trận (phụ thuộc #568).
3. Người nhận theo nhóm trong quy tắc: kiểm "trong ô Gán của người cấu hình" cho từng thành viên nhóm lúc lưu (hiện chỉ kiểm người ghim đích danh).

## #571 — IAM: Tiến trình chạy thay — mức lúc khởi chạy, xuất/nhập, ngoại lệ nguồn hàng đợi (phần còn lại của #376)

Chưa có triển khai nào. Nội dung issue:

Tách từ #376 (phần chưa làm của BR-35.8):

1. **Mức lúc khởi chạy × mức hiện tại** cho quy trình tự động hoá (hiện chỉ có `runAs`; `system` chạy toàn tenant — sản phẩm cần quyết định cho `runAs=system`, xem #567) và cho xuất/nhập hàng loạt (khoá phạm vi lúc xếp hàng nhưng chưa giao với mức hiện tại).
2. **Xuất / nhập**: tạm dừng (không chỉ huỷ) khi người khởi chạy bị tạm ngưng/rời đi, báo quản lý trực tiếp + toàn quyền, Chạy tiếp, chuyển người khởi chạy; đưa vào bước "Tiến trình đang chạy thay" của `FEAT-43`.
3. **Quy trình tự động hoá trong bước "Tiến trình đang chạy thay"** của chuyển phòng/rời workspace (`unfinishedLaunches` mới có chiến dịch/chuỗi).
4. **Ngoại lệ nguồn tạo bản ghi vào hàng đợi** (AC-35.8.4): tác vụ liên hệ lại từ chiến dịch ghi người tạo "Hệ thống — nguồn <tên>" khi người khởi chạy không còn hoạt động và không chịu trần của người khởi chạy; loại khỏi bước "Tiến trình đang chạy thay"; nguồn biểu mẫu website (chưa có mô-đun).

## #572 — IAM: Giao diện điều chỉnh ô vai trò dựng sẵn và hàng chờ duyệt bản phát hành (phần còn lại của #386)

Chưa có triển khai nào. Nội dung issue:

Tách từ #386 (phần giao diện của BR-29.1/BR-29.4):

1. Nhãn "đã điều chỉnh" trên từng ô của vai trò dựng sẵn; "Đặt lại về mặc định" từng ô/cả vai trò, vô hiệu kèm ô vượt trần (AC-29.4.4).
2. Danh sách thay đổi vai trò dựng sẵn chờ duyệt (`GET /roles/release-proposals`) trên màn hình sức khoẻ phân quyền (#408), với nút Duyệt / Bỏ qua (`POST …/approve|skip`).
3. Khoá dịch thông báo `notifications.releaseRoleChangePending` (vi/en/ar).

## #573 — IAM: Giữ tên đơn vị đã xoá, bản ghi đóng của đơn vị đã xoá, cảnh báo mồ côi (phần còn lại của #391)

Chưa làm: bản ghi đóng của đơn vị đã xoá chỉ cho Xem Toàn workspace, nguồn 'bản ghi giữ chỗ' trong chặn xoá, dùng tên ở web.

## #575 — IAM: Giao diện rà soát quyền và chuyển mục khi người rà soát vắng (phần còn lại của #402)

Chưa làm: giao diện rà soát, thu hồi Quyền Ủy thác theo từng danh sách, khoá dịch web cho thông báo giao mục/nhắc hạn.

## #584 — IAM: lô nhập chuẩn bị — dòng trỏ tới lời mời nháp và file dòng lỗi (tiếp #417)

Chưa có triển khai nào. Nội dung issue:

Phần hoãn của #417: (1) dòng nhập trỏ tới lời mời đang nháp chỉ chạy sau khi lời mời được xác nhận/huỷ, huỷ → dòng lỗi; (2) file dòng lỗi giữ riêng theo CFG-08-05. Hiện lô chạy bình thường, chưa chờ lời mời.

## #586 — Onboarding: tên miền riêng — đăng nhập/phiên trên tên miền riêng, web theo Host, cấp chứng chỉ, khoá gói (tiếp #450)

Chưa có triển khai nào. Nội dung issue:

Phần hạ tầng chưa làm của #450: (1) cookie/phiên đăng nhập cho tên miền riêng (hiện cookie theo root domain; sanitizeReturnTo từ chối host ngoài root); (2) crm-web nhận diện workspace theo Host riêng (getTenantAliasFromHostname) và URL builder còn lại (email khác ngoài thư mời, đăng nhập); (3) cấp/gia hạn chứng chỉ tự động (cert-manager/gateway) — hiện chỉ kiểm tra chứng chỉ hợp lệ; (4) khoá tính năng 'tên miền riêng' trong plan catalog billing thay cho quy tắc tạm 'gói trả phí'; (5) crm-manager-api CustomDomain cũ cần bỏ cờ env/đồng bộ với mô hình mới.

## #588 — Đơn vị tiếp nhận của biểu mẫu website đầu tiên (AC-11.4.2, 11.4.5, tiếp #455)

Chưa có triển khai nào. Nội dung issue:

Chưa có đối tượng 'biểu mẫu website' trong crm-api/crm-web nên chưa làm được: chọn sẵn đơn vị tiếp nhận theo mặc định/đội Nhân viên Kinh doanh (IntakeUnitService.recommendedFor(..., 'sys.sales_rep') đã có), câu hỏi đặt mặc định chung, và vô hiệu nút công khai với người không toàn quyền. Làm khi có biểu mẫu website.

## #589 — Thiết lập đội ngũ nhanh: vai trò trưởng nhóm, kiêm nhiệm gộp vai trò, đồng quản trị, lối thay thế, cờ theo dòng (tiếp #456)

Chưa có triển khai nào. Nội dung issue:

Phần chưa làm của #456: (1) vai trò trưởng nhóm riêng (PLT-13 leadRole) — cần mời theo từng email có bộ vai trò riêng; (2) kiêm nhiệm: gộp vai trò gợi ý của cả hai đội; (3) dòng Đồng quản trị (chỉ toàn quyền); (4) lối thay thế ở màn hình 2: liên kết mời cho đội, mời từ tệp, tự gia nhập; (5) cờ theo dòng ở màn hình 3: vượt trần năng lực, CFG-34-02, đổi Đơn vị chính/kiêm nhiệm Đầy đủ; (6) cảnh báo sức khỏe cấu hình khi đơn vị không còn người đứng đầu (FEAT-03); (7) lời mời của trưởng nhóm/đồng quản trị gửi trước khi hàng đợi dùng thử.

## #591 — Tạo hộ: tính năng mở rộng & điều khoản gói trong biểu mẫu, thông báo đẩy cho nhân sự hợp đồng (tiếp #463)

Chưa có triển khai nào. Nội dung issue:

Phần chưa làm của #463: (1) tính năng mở rộng và điều khoản gói ngay trong biểu mẫu tạo hộ (hiện cấp bằng feature-permissions/grant sau khi tạo); (2) nhắc 'Chủ sở hữu chưa chấp nhận sau PLT-14' hiện chỉ là danh sách trong crm-manager-web — cần thông báo đẩy/email nội bộ cho nhân sự phụ trách hợp đồng; (3) crm-manager-web chưa có test runner nên màn hình mới chưa có test tự động.

## #592 — Giai đoạn triển khai trước kích hoạt: mặc định tiếp nhận, nhật ký có nhãn, màn hình soạn danh sách mời, nút mở ở console (tiếp #464)

Chưa làm: màn soạn danh sách mời + xác minh tên miền email, nút mở giai đoạn trong crm-manager-*, deep link nhiệm vụ lộ trình.

## #593 — Contacts: dọn phần còn lại của quyền quản trị phân hệ (CFG-05-02, đồng duyệt theo quyền)

Chưa có triển khai nào. Nội dung issue:

Phần còn lại của #471 sau khi merge (đối chiếu trong comment trên #471):

- Gỡ tham số CFG-05-02 của phân hệ Contacts; chuyển sang tham số điều chỉnh ô chung của IAM (CFG-29-02). CFG-05-02 hiện là hàng đăng ký duy nhất của khoá `role_permission_matrix_customization`, gắn vào 6 điểm của `crm-settings.service` + spec; cần hàng đăng ký IAM tương ứng trước.
- Đồng duyệt (`coApprovalRoles`) của CFG-12-02 và quyền "+ Quản trị viên" của CFG-31-02 vẫn so tên vai trò; chuyển sang quyền quản trị.
- Điểm thi hành của "Quét trùng lặp toàn workspace" (`BR-17.4`) và "Xác nhận liên hệ ngoài hệ thống" (`BR-31.8`): đã có khoá và mặc định, công cụ/quy trình thuộc #474/#475.
- Hậu triển khai: chạy `npm run migrate:contact-admin-cells -- --dry-run` rồi chạy thật, và `seed:system-roles` để nạp mặc định cho workspace hiện hữu.

## #594 — Contacts: nguồn lead còn lại có đơn vị tiếp nhận (biểu mẫu website, tích hợp, quảng cáo) và chống trùng vào hàng đợi

Chưa có triển khai nào. Nội dung issue:

Phần còn lại của #473 (đối chiếu trong comment trên #473):

- Biểu mẫu website, tích hợp, trợ lý trò chuyện, nguồn quảng cáo: mỗi nguồn có đúng một đơn vị tiếp nhận, chưa có đơn vị thì không kích hoạt/công khai được (`AC-31.9.1`, `AC-31.9.2`). Hiện crm-api không có mã/lược đồ cho các nguồn này (`website_forms` chỉ là tên trong danh mục loại nguồn `INTAKE_SOURCE_TYPES`); cần tạo nguồn rồi gắn `IntakeUnitService`.
- Khách trùng với bản ghi đã có Người phụ trách (Đang hoạt động hoặc Tạm ngưng) không vào hàng đợi — kiểm tra ở điểm tạo từ các nguồn trên.
- Màn hình hàng đợi khách hàng của đơn vị (danh sách chưa phân công theo đơn vị tiếp nhận) — hiện có `OwnerlessRecordsPage`; cần đối chiếu có gồm đủ bản ghi chờ phân công của lead.
- Hậu triển khai: `npm run backfill:lead-queue -- --dry-run` rồi `--confirm`.

## #595 — Contacts: phân bổ tự động — giao theo phạm vi người chịu trách nhiệm, chủ Tạm ngưng, thực thi thu hồi quá hạn

Chưa làm: phân bổ theo phạm vi người chịu trách nhiệm + ô Sửa (BR-43.2), quy tắc phân bổ trong bước tiến trình khi rời, nguồn bằng chứng hệ thống tự sinh.

## #596 — Contacts: nhập tệp — hai mức của ô, nhật ký giữ chỗ, resume cho module khác, hướng dẫn cột

Chưa làm: hai mức của ô, nhật ký bản ghi giữ chỗ, hướng dẫn cột trên trang nhập (web).

## #597 — Contacts: xuất dữ liệu — lọc theo mức Đơn vị của ô Xuất và luồng người phê duyệt trên UI

Chưa có triển khai nào. Nội dung issue:

Phần còn lại của #481 (đối chiếu trong comment trên #481):

- Mệnh đề lọc tệp xuất theo mức Đơn vị / Cây đơn vị của ô (Khách hàng, Xuất) (hiện chỉ ô Chỉ của mình ép lọc theo chủ sở hữu); tương tự (Doanh nghiệp, Xuất) cho module Doanh nghiệp.
- Trường người phê duyệt trong hộp thoại xuất cho lần xuất vượt ngưỡng/hạn mức (hiện chỉ có người đồng phê duyệt khi chọn KYC; ngoài ra 403 là tín hiệu duy nhất) và danh sách yêu cầu chờ duyệt cho quản lý trực tiếp.
- `CFG-25-01/02` backfill và test ≥ 2 giá trị đã có ở mức đơn vị; chưa có kiểm thử e2e cho đường xuất.

## #599 — Deals: xuất theo phạm vi hiện tại, tạm dừng/hủy theo người yêu cầu, bước chuyển tiến trình, UI tiếp tục nhập (tiếp #491)

Chưa làm: bước 'Tiến trình đang chạy' cho export/import trong offboarding, tạm dừng (thay vì huỷ) export, báo quản lý, log narrow/widen, UI resume batch nhập, mặc định intake cho dòng nhập không owner.

## #600 — Tickets: danh sách hàng đợi được chuyển tới khi tạo/tách vé, loại nguồn biểu mẫu/cổng/tích hợp, picker hàng đợi trên web (tiếp #494)

Chưa có triển khai nào. Nội dung issue:

Phần chưa làm của #494 (BR-34.2): (1) provider transfer-targets cho tickets (hiện chưa đăng ký) + hàm theo unitId để người tạo vé từ hội thoại/tách vé chọn hàng đợi khác trong danh sách được chuyển tới; (2) loại nguồn riêng cho biểu mẫu hỗ trợ / cổng khách hàng / tích hợp (hiện gộp ticket_source); (3) web: CreateTicketModal có picker hàng đợi (các đơn vị tiếp nhận mình là thành viên) + 'Giao cho tôi' (bật sẵn khi đạt điều kiện nhận việc, BR-34.4), SplitTicketDialog chọn hàng đợi; (4) điều kiện nhận việc cho 'Giao cho tôi' phía server (claimRefusal); (5) vé cha sự cố; (6) nhãn i18n web cho hai loại nguồn mới trong màn hình mặc định tiếp nhận. Backend đã có: ticket_manual / ticket_from_conversation, chặn khi thiếu mặc định, tách/tạo lại theo hàng đợi hiện tại.

## #601 — Tickets: giao diện khai báo danh sách chuyển tới & danh mục lý do, soạn sẵn trong Phiên triển khai, cảnh báo mất quyền thấy (tiếp #495)

Chưa có triển khai nào. Nội dung issue:

Phần chưa làm của #495 (BR-34.8, CFG-34-02, C.35): (1) màn hình admin khai báo danh sách đích theo hàng đợi + danh mục lý do (API đã có: ticket-settings/queue-transfer/*); (2) Phiên triển khai: thay đổi danh sách có hiệu lực ngay với hàng đợi chưa có vé, dạng soạn sẵn với hàng đợi đang có vé (classifyDeploymentRequest); (3) quyền 'Cấu hình điều phối' riêng cho danh mục lý do (đang dùng ticket_catalog:manage); (4) cảnh báo chính xác người chuyển có còn thấy vé sau chuyển không; (5) miễn điều kiện bị chặn khi giữ owner (record_block/deny_policy); (6) i18n web cho thông báo notifications.ticketQueueTransferred.

## #603 — Tickets: cấu hình mốc leo thang chịu ô Gán & danh sách chuyển tới, giao diện lịch sử leo thang, gán vé con hàng loạt (tiếp #501)

Chưa có triển khai nào. Nội dung issue:

Phần chưa làm của #501: (1) BR-18.2 lúc CẤU HÌNH: người tạo/sửa chính sách leo thang chỉ chọn được người nhận/hàng đợi đích mà ô (Vé hỗ trợ, Gán) của chính mình bao phủ (+ hành động chuyển hàng đợi trong danh sách được chuyển tới, hiện chưa có loại hành động queue); (2) số cấp notify_up 1–3 theo CFG-18-02 (đang 1–10); (3) hiển thị lịch sử leo thang (escalationHistory) trên chi tiết vé + timeline; (4) API gán nhiều vé con một lượt có danh sách từ chối kèm lý do, chỉ vé đang mở; (5) preview kết thúc sự cố đánh dấu vé con ngoài ô Kết thúc; cổng isSupervisor thay bằng ô; (6) giao diện web cho TIC-CFG-18-03 và hiển thị failed[]/outOfCell ở gắn nhãn/kết thúc hàng loạt; (7) vé đã resolved nhưng chưa closed vẫn bị leo thang.

## #604 — Tickets: nhập từ tệp — mặc định đơn vị 'Nhập từ tệp', người nhập kiêm nhiệm, mã lỗi riêng, gợi ý cột (tiếp #502)

Chưa có triển khai nào. Nội dung issue:

Phần chưa làm của #502: (1) override fileImportIntake cho vé (mặc định FILE_IMPORT_SOURCE của đơn vị người nhập, người không toàn quyền chỉ chọn đúng mặc định); (2) người nhập là thành viên KIÊM NHIỆM của đơn vị lô cũng được tính (hiện chỉ đơn vị chính → vé thành chưa gán); (3) mã lỗi riêng cho INTAKE_UNIT_NOT_IN_INVITATION (đang VALIDATION_FAILED); (4) test hết giữ chỗ khi lời mời bỏ đơn vị với vé + báo người phụ trách đơn vị; (5) web gợi ý tự cột Owner/Đơn vị tiếp nhận; (6) test processor cho launch ∩ current scope của vé.

## #605 — Tickets: hiển thị cơ hội liên kết theo mức Xem, cờ rủi ro theo từng vé, ngữ cảnh khách hàng, cảnh báo nhắc tên (tiếp #507)

Chưa có triển khai nào. Nội dung issue:

Phần chưa làm của #507: (1) BR-29.2: chi tiết vé trả cơ hội liên kết dạng {restricted:true} khi mức Xem không phủ (mở rộng RecordLinkRedactor cho dealId), findByDeal lọc/ẩn theo từng vé, web có thẻ 'cơ hội liên kết' + chip 'Bị hạn chế truy cập' và thao tác gắn/gỡ link; tab vé trên cơ hội dùng dealId thay relatedTo; (2) BR-30.3: getDealRisk lọc chi tiết theo TỪNG vé (hiện một lần kiểm tra cho cả lô); (3) BR-03.3: bối cảnh khách hàng trên màn hình vé theo mức Xem nguồn từng mục + lượt đọc tự động cho khách của vé đang mở (mẫu contacts/timeline/customer-context.service.ts); (4) BR-14.2: cảnh báo ngay cho người nhắc tên khi chọn người không có mức Xem bao phủ vé (endpoint candidates + notification không dẫn link); (5) test ≥2 giá trị CFG-29-01 ở mức e2e và test nhật ký lỗi cho nới rộng/thu hẹp; (6) backfill grant cho liên kết sẵn có khi deploy.

## #606 — Tickets: nhật ký — che tên/nội dung theo mức Xem, khử định danh chỉ thay nội dung cá nhân, bộ lọc, hoàn tác gộp (tiếp #508)

Chưa làm: che tên/nội dung theo mức Xem, khử định danh từng phần, quyền unmerge + diff, ghi chú bàn giao vào nhật ký.

## #607 — Tickets: tạo vé từ email của hộp thư thuộc Vé hỗ trợ + giao diện chuyển phân hệ (tiếp #509)

Chưa có triển khai nào. Nội dung issue:

Phần chưa làm của #509: (1) CONSUMER của sự kiện email.inbound.for_tickets: tạo vé / nối thư trả lời vào vé cũ theo Message-ID (đơn vị tiếp nhận = intakeUnitId hộp thư, nhận diện contact theo email); chỉ khi có consumer mới bật TICKET_EMAIL_INTAKE_ENABLED (hiện chặn chuyển sang Vé để không mất thư); (2) thư trả lời vào VÉ cũ vẫn nối vào vé tới khi đóng hoàn tất sau khi hộp thư chuyển sang Hội thoại; (3) web: chọn phân hệ khi kết nối hộp thư (full rights), nút 'Chuyển phân hệ' kèm xác nhận, danh sách hộp thư chưa chọn + cảnh báo (GET mailbox-modules/unchosen); (4) bắt buộc chọn phân hệ ở bước kết nối hộp thư mới (hiện mặc định Omnichat); (5) i18n.

## #608 — Tickets: bảng điều khiển — hiển thị theo hàng đợi, chuyển hàng đợi tại chỗ, báo cáo gián đoạn BR-43.10, test độ trễ (tiếp #510)

Chưa làm: sự kiện socket realtime cho dashboard, và phần web (bảng byQueue/bộ lọc, chuyển hàng đợi tại chỗ).

## #609 — Tickets: lịch trực theo đơn vị tiếp nhận, đối chiếu cam kết theo hàng đợi, nhà cung cấp SMS/gọi (tiếp #499)

Chưa làm: UI (ShiftFormDialog, nghỉ phép trong lịch), checkCoverage theo từng chính sách cam kết, đăng ký nhà cung cấp SMS/gọi thật, backfill CFG-37-02, nhận nghỉ phép từ quy trình duyệt.

## #610 — Tickets: thời hạn Thùng rác theo doanh nghiệp, kiểm tra mẫu trước khi gửi, mã CFG-06-01 va chạm, e2e ứng viên phân bổ (tiếp #511)

Chưa có triển khai nào. Nội dung issue:

Phần chưa làm của #511: (1) BR-26.1: TicketPurgeService vẫn dùng biến môi trường TICKET_PURGE_RETENTION_DAYS cho cả hệ thống — đọc TIC-CFG-26-01 theo từng tenant (đã đăng ký) khi purge/stamp purgeExpiresAt; (2) BR-16.2 UI: tô nổi biến chưa điền và cảnh báo trước khi gửi (API đã trả unresolved; interpolate chưa có chỗ gọi nào ngoài test); (3) mã CFG-06-01 trong registry đang là ownership_transfer_wait_hours — xin SRS đổi mã (issue tài liệu) hoặc đổi tham số đó; (4) UI cấu hình ma trận + cờ exemptFromPublicReply + tạo trạng thái 'Thư rác/Thử nghiệm' mặc định khi seed; (5) BR-19.2 cho tự đóng/đóng hàng loạt (câu hỏi mở SRS dòng 3304) và backfill; (6) ticket-complaints.e2e 'complained agent' trả pool rỗng — kiểm tra có phải do #498 (refusedRecipients/Assign) hay đã đỏ sẵn.

## #611 — Omnichat: chờ quá ngưỡng chỉ trong cùng đơn vị, hàng đợi dự phòng liên đơn vị (CFG-05-11), cấu hình leo thang chịu ô Gán (tiếp #515)

Chưa có triển khai nào. Nội dung issue:

Phần chưa làm của #515: (1) BR-05.6: queue-escalation.service overflow() chọn nhóm theo defaultGroupId/targetGroupId không kiểm tra nhóm thuộc đơn vị tiếp nhận của hội thoại và có thể mở audience cả tenant (TENANT_AUDIENCE) — chỉ cho nhóm cùng đơn vị; (2) chuyển sang đơn vị khác: hàng đợi dự phòng do toàn quyền khai báo trong danh sách đích hội thoại (cần #516) + OMN-CFG-05-11 (chưa có trong registry; nới rộng đóng khi nhật ký lỗi, backfill, test ≥2 giá trị); (3) BR-09.2 lúc cấu hình: người tạo chính sách leo thang chỉ chọn người nhận/hàng đợi trong mức Gán của mình (DTO chỉ validate ObjectId) + action chuyển hàng đợi; (4) thông báo người cấu hình qua thông báo bền thay vì kênh socket; (5) bot handoff nhóm: nhóm đích phải thuộc cùng đơn vị tiếp nhận (assertGroupEligible chỉ xét support pool kênh); fallback hiện là 'general' thay vì vào hàng đợi; (6) test cho claim-eligibility.service.

## #612 — Omnichat: giao diện chuyển hàng đợi hội thoại, SLA theo chính sách đích, báo khách, Phiên triển khai (tiếp #516)

Chưa có triển khai nào. Nội dung issue:

Phần chưa làm của #516: (1) web: nút/hộp thoại 'Chuyển hàng đợi' trên hội thoại (đích từ GET omni/conversations/:id/queue-transfer-targets, lý do từ danh mục chuyển tiếp) + màn hình admin khai báo danh sách đích CFG-05-08 (API đã có); (2) SLA: giữ mốc bắt đầu, tính lại theo chính sách của đơn vị đích nếu khác; (3) thông báo cho khách khi chuyển; (4) Phiên triển khai (BR-08.7): thay đổi danh sách có hiệu lực ngay với hàng đợi chưa có hội thoại, soạn sẵn với hàng đợi đã có; (5) chuyển tiếp tới nhóm/Hộp thư thuộc đơn vị khác phải đi qua chuyển hàng đợi (conversation-transfer.service hiện cho targetGroupId bất kỳ); (6) hiển thị lịch sử chuyển hàng đợi (CONVERSATION_QUEUE_TRANSFER) trên nhật ký hội thoại; (7) cùng đơn vị: tùy chọn bỏ người phụ trách; (8) i18n.

## #613 — Omnichat: Xin ý kiến — chỉ ghi chú nội bộ cho người được mời, cờ intervenedById, giao diện, backfill (tiếp #518)

Chưa làm: chặn trả lời/đổi trạng thái theo lượt consulting, BR-39.6, lọc mẫu chấm, backfill/UI cấu hình, web.

## #614 — Omnichat: hộp thoại kết ca chọn từng hội thoại, bỏ qua khẩn cấp theo phạm vi đơn vị (tiếp #520)

Chưa có triển khai nào. Nội dung issue:

Phần chưa làm của #520: (1) web EndShiftDialog: mỗi hội thoại có nút Đề nghị chuyển / Giao thẳng (chỉ khi Gán từ Đơn vị của mình) / Trả về hàng đợi + ô nhập ghi chú bàn giao bắt buộc (API POST omni/shifts/handoff/conversations/:id đã có); (2) bỏ qua khẩn cấp: thêm 'Người phụ trách đơn vị của agent' và kiểm tra phạm vi người bỏ qua trong đơn vị agent (hiện bất kỳ ai có omni_shifts:manage bỏ qua cho agent bất kỳ); (3) sửa nhãn quyền trong comment/test (manage_system vs omni_shifts:manage); (4) đề nghị ticket chưa chấp nhận cũng phải về hàng đợi khi bấm kết ca (hiện chỉ conversation); (5) giao thẳng conversation: lỗi 'Assign' dùng resource tickets ở mayHandOverDirectly — thêm omni_channel:assign cho hội thoại.

## #615 — Omnichat: gợi ý khớp theo phạm vi Xem, định danh dùng chung theo ô Sửa hội thoại, trả lead về hàng đợi khi chủ rời (tiếp #521)

Chưa làm: ô Sửa hội thoại cho cờ Định danh dùng chung, kiểm View của linkToContact, lead từ automation vào hàng đợi kênh, trả về hàng đợi khi chủ vắng, intakeIncludesSubunits, web nhãn hội thoại song song.

## #616 — Omnichat: tạo vé từ hội thoại — giao diện chọn hàng đợi, dòng thời gian hai chiều (tiếp #527)

Chưa có triển khai nào. Nội dung issue:

Phần chưa làm của #527: (1) web: nút 'Tạo vé' trên hội thoại hiển thị lý do chặn (TICKET_CONVERSATION_NO_INTAKE / TICKET_FROM_CONVERSATION_NOT_ALLOWED) và chọn hàng đợi khác trong danh sách được chuyển tới (cần endpoint liệt kê đích theo hàng đợi mặc định); (2) dòng thời gian hội thoại ghi 'đã tạo vé #…' và hiển thị linkedTicketIds trên hội thoại (mới lưu, chưa trả ra API); (3) test e2e: người chỉ có Sửa hội thoại / chỉ có Tạo vé bị chặn.

## #617 — Omnichat: Live Chat — tin cậy định danh gõ tay ở visitor:identify, tuỳ chọn privacyPolicyUrl, thu hồi visitorId (tiếp #528)

Chưa làm: kết thúc phiên phía server/thu hồi visitorId, test controller resume/ResumeCodeDelivery.

## #618 — Campaigns: hộp thư không thuộc Vé hỗ trợ, phạm vi tin gắn nhãn, danh sách tin theo quyền, các nơi còn gắn tên vai trò (tiếp #531)

Chưa có triển khai nào. Nội dung issue:

Phần chưa làm của #531: (1) BR-10.4: hộp thư nhận trả lời không được là hộp thư đã gán cho Vé hỗ trợ (channels.mailboxModule='tickets' từ #509) — campaign-reply-mailbox.service add/assertAllowed; xác nhận cảnh báo hộp thư bên ngoài của người duyệt kèm nhật ký (AC-10.4.4); thông báo lỗi vẫn ghi 'Quản trị viên'; (2) BR-26.3: listFlags chưa lọc theo tập khách hàng xem được của người xử lý (BR-05.4), chỉ DPO thấy phần còn lại; ghi nhận hàng loạt cho người có quyền Xử lý tin gắn nhãn; escalateOverdue báo theo quyền; (3) cột 'Thẩm quyền' các CFG-CAMP: mã ADMIN trong Phụ lục B đổi sang quyền cụ thể, điều kiện phụ (09 đặt 0 cần Chủ sở hữu, 10 >90 ngày cần DPO, 41 miễn trừ) và mã 53 'Người có toàn quyền'; ánh xạ sys.marketing→MARKETING_MANAGER trong tenant-config-authorizer; (4) còn tên vai trò ở: campaign-reply-opt-out.service (leo thang), campaign-reputation.service, campaign-launcher/sequence/reengagement/service-notice (tìm OWNER/ADMIN), marketing-policy/change-approval, test-recipient-directory (holdsCeilingRole) — thay bằng quyền; (5) BR-42.2: ghi bù nhật ký khi kích hoạt dừng khẩn cấp, người nhận thông báo theo ô Phát sóng; (6) UI Chủ sở hữu gán 7 quyền cho vai trò tuỳ ý + migrate:campaign-admin-cells đã có.

## #619 — Campaigns: bàn giao dãy, tạm chuyển khi chủ bị tạm ngưng, đề nghị chuyển (tiếp #535)

Chưa có triển khai nào. Nội dung issue:

Phần còn lại của #535: (1) dãy (sequence) là bước bàn giao khi rời, người nhận thành audienceSelector; (2) chủ bị tạm ngưng: chọn giữ hoặc tạm chuyển; (3) đề nghị chuyển (TRANSFER_PROPOSAL_RESOURCES) cho chiến dịch, hết hạn theo CFG-25-01; (4) xác nhận lại cỡ danh sách khi đóng băng.

## #620 — Campaigns: liên hệ lại — Zalo ZNS, UI đơn vị tiếp nhận, sinh nhật ký 'Hệ thống — nguồn' (tiếp #536)

Chưa có triển khai nào. Nội dung issue:

Phần còn lại của #536: đơn vị tiếp nhận cho Zalo ZNS (kênh channels); màn hình đặt/đổi đơn vị tiếp nhận; ghi người tạo 'Hệ thống — nguồn <tài khoản>'; backfill tường minh nếu cần; chuyển công việc chờ khi đổi đơn vị (phụ thuộc #543).

## #621 — Campaigns: gửi thử theo hạn chế gộp, xuất sổ cái qua ExportMasker, AI masker (tiếp #537)

Chưa có triển khai nào. Nội dung issue:

Phần còn lại của #537: BR-15.2 gửi thử — khách mẫu thuộc tập xem được của người gửi và mọi người nhận, mỗi trường áp mức hạn chế nhất (thay/che) và màn hình nêu trường nào bị thay vì ai; tệp xuất sổ cái dùng ExportMasker thay maskDestination cục bộ; AiDataMasker khai báo cho campaigns; mẫu che A/B/C/D của maskContact.

## #622 — Campaigns: nhật ký cho tạm dừng/huỷ/phát sóng trong vòng đời và ghi nhận từ chối (tiếp #538)

Chưa có triển khai nào. Nội dung issue:

Phần còn lại của #538: áp CampaignControlAuditService cho pause/cancel/cancel-schedule/approve/launch/resume trong campaign-lifecycle, auto-pause/loại trừ do mất quyền, ghi nhận từ chối nhận tin (narrowing, ghi bù cờ 'ghi bù'); nới rộng chuyển người khởi chạy đã dùng widen.

## #623 — Campaigns web: hiển thị cảnh báo vận hành CFG-CAMP-20/64 (tiếp #539)

Chưa có triển khai nào. Nội dung issue:

API (crm-api#508) trả advisories trong phản hồi PATCH và trong định nghĩa tham số (messageKey campaigns.config.ledgerRetentionShort / noticeCapHigh). Cần: màn hình cài đặt tham số hiển thị cảnh báo, i18n vi/en/ar, bỏ ràng buộc min 12 / max 4 cũ nếu có ở UI.

## #624 — Tasks: đặt/đổi đơn vị tiếp nhận của quy trình, backfill, UI hàng đợi (tiếp #543)

Chưa có triển khai nào. Nội dung issue:

Phần còn lại của #543: API đặt/đổi đơn vị tiếp nhận của quy trình sinh việc (chỉ toàn quyền, widen ghi nhật ký trước, kích hoạt quy trình bị chặn khi chưa có đơn vị); backfill việc chưa phân công cũ vào hàng đợi nguồn; return unit theo nguồn hiện tại (nguồn mất → mặc định loại nguồn); hiển thị nguồn (BR-13.2) giữ tên khi nguồn bị gỡ; UI web (danh sách hàng đợi, nút trả về, i18n vi/en/ar).

## #625 — Tasks: người khởi chạy quy trình — mức lúc bật, tự nhận, người hoàn thành, bước chuyển phòng (tiếp #544)

Chưa có triển khai nào. Nội dung issue:

Phần còn lại của #544: ghi mức (Tạo, Gán) lúc bật và dùng phần giao lúc bật ∩ hiện tại; người có quyền Quản lý quy trình sinh công việc tự nhận làm người khởi chạy; khi quy trình tự hoàn thành công việc thì người hoàn thành là người khởi chạy (BR-25.3, xét Xem theo phần giao); đổi Đơn vị chính xuất hiện trong danh sách bước chuyển phòng; nguồn liên hệ lại không áp.

## #626 — Tasks: đề nghị chuyển — huỷ khi đổi chủ, thông báo theo quyền xem, UI (tiếp #545)

Chưa có triển khai nào. Nội dung issue:

Phần còn lại của #545: huỷ đề nghị chủ động khi chủ công việc đổi bằng mọi đường (phát RECORD_OWNER_CHANGED_EVENT từ TasksService và lắng nghe để cancelForRecord); thông báo người nhận chỉ khi đã xem được công việc; UI web gửi/chấp nhận/từ chối/rút lại + i18n vi/en/ar; test CFG-25-01 hai giá trị với công việc.

## #627 — Tasks: tạm ngưng — danh sách cảnh báo quản lý, trần 12 kỳ CFG-TASK-14, UI (tiếp #546)

Chưa có triển khai nào. Nội dung issue:

Phần còn lại của #546: danh sách cảnh báo 'Cần phân công lại' dành cho quản lý trực tiếp và Người có toàn quyền; trần sinh bù tối đa 12 kỳ khi CFG-TASK-14 bật (hiện giới hạn theo ngày lưu Thùng rác); thông báo người xử lý thay + quản lý khi kích hoạt lại và có đề xuất trả; UI web nhãn và danh sách, i18n vi/en/ar.

## #628 — Tasks: bàn giao — migration đơn vị đã kết thúc, báo cáo, trả hàng đợi, biên bản rời (tiếp #547)

Chưa có triển khai nào. Nội dung issue:

Phần còn lại của #547: script chuyển đổi công việc đã kết thúc cũ sang đơn vị người phụ trách hiện tại + ghi closedOrgUnitId; báo cáo lịch sử dùng closedOrgUnitId; bỏ ngoại lệ hiển thị cho quản lý hai đơn vị; trả về hàng đợi trong bước rời; ghi vào biên bản rời workspace; phần ngoài phạm vi ô Bàn giao hiện riêng và thông báo toàn quyền; tự chọn mình (BR-38.9); UI.

## #629 — Tasks: tương tác — người khởi chạy làm actor, ghi chú qua NotesService, UI hạn chế (tiếp #548)

Chưa có triển khai nào. Nội dung issue:

Phần còn lại của #548: công việc do quy trình tự hoàn thành dùng người khởi chạy làm actor và phần giao các mức; ghi chú nội bộ Doanh nghiệp/Cơ hội và ghi chú tự sinh đi qua NotesService (contacts FEAT-36: phạm vi đọc, thời hạn sửa, ẩn thay xoá); chỉ chép trường tối thiểu; dòng thời gian chỉ hiện với người trong phạm vi đọc; UI 'Bị hạn chế truy cập' cho liên kết công việc (sourceTaskRestricted), i18n vi/en/ar.

