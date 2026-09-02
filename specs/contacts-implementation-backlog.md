# Backlog Triển khai — Phân hệ Khách hàng & Danh bạ Doanh nghiệp (Contacts & Accounts)

| | |
| --- | --- |
| **Nguồn yêu cầu** | [`srs/contacts-srs.md`](../srs/contacts-srs.md) v6.4 (nội dung đã hội tụ, 0 lỗi Nặng qua hai vòng soát độc lập) |
| **Ngày lập** | 2026-09-02 |
| **Phạm vi** | 36 tính năng nghiệp vụ, 141 quy tắc, 36 tham số cấu hình, 22 kịch bản UAT |
| **Trạng thái** | **Đã tạo issue** — 30 issue `#176`–`#205` tại `crmsaassaudi/product-management`, tạo ngày 2026-09-02 theo đúng thứ tự triển khai nên **số issue tăng dần khớp thứ tự làm**. Tất cả đang để **assignee trống** = chưa ai claim. |

## Cách dùng tài liệu này

Mã `T-xx` là **số tạm dùng khi lập backlog**; id thật là số issue GitHub ở cột *Issue*, dùng làm `id` feature (`feat-<số>`) và tên branch (`feat/<số>-<slug>`) theo [`PROCESS.md`](../PROCESS.md). Mã `T-xx` chỉ còn dùng để tra ngược tài liệu này.

**Claim một ticket** = tự gán issue tương ứng cho mình (`gh issue edit <N> --add-assignee @me`). Điểm tra duy nhất xem ticket còn trống hay không là assignee của issue ở `product-management`, không phải `feature_list.json` của repo triển khai.

Tiêu chí hoàn thành của mỗi issue **chỉ ghi phần đặc thù**: danh sách mã `BR` phải phủ và kịch bản UAT phải chạy được. Phần baseline (unit test pass, không phá tính năng khác, verification đã chạy) đã nằm trong `AGENTS.md` của repo triển khai — không lặp lại.

Ký hiệu `KB n` = Kịch bản UAT số n tại mục 6 của SRS.

## Bốn nguyên tắc xếp thứ tự

1. **Cơ chế dùng chung đi trước tính năng dùng nó.** 30/36 quy tắc có nhánh cấu hình trỏ về Phụ lục B; làm tính năng trước thì phải cắm cứng giá trị rồi tháo ra sửa lại.
2. **Sàn pháp lý và sàn chống mất dữ liệu đi trước tính năng tăng trưởng.** Gắn ràng buộc đồng thuận sau khi dữ liệu đã tích luỹ thì phải rà soát hồi tố toàn bộ danh bạ — đắt hơn nhiều so với làm đúng từ đầu.
3. **Việc bị chặn bởi quyết định ngoài kỹ thuật được tách riêng và đứng sau.** Không xếp một ticket vào sớm rồi để nó nằm chờ Pháp chế hoặc chờ chốt hợp đồng liên tài liệu.
4. **Vòng đời khách hàng là gốc phụ thuộc của nhóm D.** Chuyển đổi Lead, phân bổ và chấm điểm đều đọc/ghi giai đoạn vòng đời; ma trận chuyển đổi phải đúng trước.

---

## Đợt 0 — Nền tảng dùng chung (chặn phần lớn phần còn lại)

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- |
| **T-01** | [#176](https://github.com/crmsaassaudi/product-management/issues/176) | `crm-settings: cơ chế tham số cấu hình theo tenant có sàn bắt buộc không nới được` | Phụ lục B (36 tham số, 29 có sàn) | crm-api + crm-web | KB 22 | L |
| **T-02** | [#177](https://github.com/crmsaassaudi/product-management/issues/177) | `Contacts: danh mục dữ liệu chuẩn Phụ lục A (19 danh mục) — seeding và quản trị giá trị` | Phụ lục A | crm-api + crm-web | KB 12, 17 | M |
| **T-03** | [#178](https://github.com/crmsaassaudi/product-management/issues/178) | `audit-log: sổ sự kiện nhật ký theo danh mục đóng NFR-07` | NFR-07 | crm-api | KB 6, 15 | M |
| **T-04** | [#179](https://github.com/crmsaassaudi/product-management/issues/179) | `data-visibility: trao quyền theo quan hệ với bản ghi (chính người dùng / người phụ trách / người đang xử lý vé)` | Mục 5, BR-34.6, BR-35.4 | crm-api | KB 15 | L |

**T-01** đứng đầu vì hai lý do độc lập nhau: nó là chỗ 30 quy tắc khác trỏ về, và sàn bắt buộc phải chặn **ở tầng ghi giá trị** chứ không chỉ ẩn ô trên giao diện — một tenant gọi API đổi cấu hình phải bị từ chối. Đăng ký sẵn tham số của các tính năng đã có (`CFG-01-xx`, `04-xx`, `05-01`, `12-xx`, `15-xx`, `17-01`, `19-01`, `20-01`, `22-02`, `25-xx`, `30-xx`); tham số của tính năng chưa làm thì ticket của tính năng đó tự đăng ký, tránh dựng khung rỗng.

**T-02** phải xong trước Đợt 1 vì có quy tắc **bắt buộc chọn giá trị từ danh mục** mà danh mục chưa tồn tại thì quy tắc không chạy được: A.1 (hai nhóm lý do — 5 thương mại + 3 gian lận, dùng bởi BR-12.4/12.4b/12.8), A.17 (lý do mở lại), A.18 (lý do tái nhập phễu, BR-16.5). Cần phân biệt rõ danh mục **đóng** (tenant không sửa) và danh mục **mở rộng được**.

**T-04** là cơ chế mà quá trình soát SRS phát hiện là nguồn của sáu lỗi Nặng liên tiếp: quy tắc trao quyền theo **quan hệ với bản ghi**, còn ma trận phân quyền tổ chức theo **vai trò hệ thống** — không có ô nào diễn đạt được một quan hệ. Làm trước thì FEAT-34/35/36 chỉ khai báo quan hệ, không tự dựng lại logic. Trục *thành viên Đội ngũ phụ trách* nằm ở **T-26** vì nó cần Đội ngũ tồn tại trước.

---

## Đợt 1 — Vòng đời khách hàng (gốc phụ thuộc nhóm D)

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- |
| **T-05** | [#180](https://github.com/crmsaassaudi/product-management/issues/180) | `Contacts: ma trận chuyển đổi 10 giai đoạn vòng đời — 9 quy tắc mới` | FEAT-12 (BR-12.3, 12.4b, 12.5b, 12.6→12.10, 16.5) | crm-api + crm-web | KB 12, 16 | XL |
| **T-06** | [#181](https://github.com/crmsaassaudi/product-management/issues/181) | `Contacts: lịch sử chuyển giai đoạn và lý do bắt buộc khi chuyển ngược` | FEAT-13 (BR-13.3) | crm-api + crm-web | KB 12 | S |

**T-05** là ticket lớn nhất của loạt (9 quy tắc mới trên 13). Nên tách nhỏ khi vào sprint, nhưng **không tách theo từng quy tắc** — tách theo nhánh chuyển đổi, vì các quy tắc chia nhau cùng một bảng trạng thái và tách lẻ sẽ để lại trạng thái trung gian không hợp lệ. Riêng BR-12.6 (bảo vệ giai đoạn đang trả tiền) và BR-12.8 (phát hiện gian lận) phải cùng một lượt merge với BR-12.3.

---

## Đợt 2 — Chất lượng dữ liệu, trùng lặp và gộp bản ghi

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- |
| **T-07** | [#182](https://github.com/crmsaassaudi/product-management/issues/182) | `Contacts: quy tắc bắt buộc và chuẩn hoá dữ liệu khi tạo/sửa hồ sơ` | FEAT-01 (BR-01.1b, 01.1c, 01.5, 01.5b, 01.6) | crm-api + crm-web | KB 1 | M |
| **T-08** | [#183](https://github.com/crmsaassaudi/product-management/issues/183) | `Contacts: nhận diện trùng lặp — ngưỡng cấu hình và chặn tạo trùng` | FEAT-17 (BR-17.2b, 17.2c, 17.3, 17.4) | crm-api + crm-web | KB 3 | M |
| **T-09** | [#184](https://github.com/crmsaassaudi/product-management/issues/184) | `Contacts: gộp bản ghi — 7 quy tắc mới, gồm sàn "OPT_OUT thắng khi gộp"` | FEAT-19 (BR-19.4→19.10) | crm-api + crm-web | KB 3, 10, 16 | L |
| **T-10** | [#185](https://github.com/crmsaassaudi/product-management/issues/185) | `Contacts: sổ cái hoàn tác gộp — thời hạn hoàn tác cấu hình được` | FEAT-20 (BR-20.3) | crm-api | KB 3 | S |
| **T-11** | [#186](https://github.com/crmsaassaudi/product-management/issues/186) | `Contacts/Accounts: thùng rác, ảnh hưởng thực thể con và chốt an toàn trước dọn vĩnh viễn` | FEAT-05 (BR-05.5, 05.6), FEAT-09 (BR-09.2) | crm-api + crm-web | KB 11, 20 | M |
| **T-12** | [#187](https://github.com/crmsaassaudi/product-management/issues/187) | `Accounts: cây doanh nghiệp mẹ-con — chống vòng lặp và ảnh hưởng khi xoá nhánh` | FEAT-07 (BR-07.4, 07.5) | crm-api + crm-web | KB 5 | M |
| **T-13** | [#188](https://github.com/crmsaassaudi/product-management/issues/188) | `Contacts: quan hệ đa doanh nghiệp và trạng thái liên hệ OBSOLETE` | FEAT-10 (BR-10.4, 10.4a) | crm-api + crm-web | KB 5 | M |

**T-09** mang một sàn pháp lý: BR-19.6 buộc bản ghi sau khi gộp nhận trạng thái đồng thuận **nghiêm ngặt hơn**, không cho ghi đè thủ công. Đây là lý do T-09 đứng trước toàn bộ nhóm tăng trưởng ở Đợt 4 — gộp bản ghi mà làm mất `OPT_OUT` thì mỗi lượt gộp là một lần gửi tin cho người đã từ chối.

---

## Đợt 3 — Tuân thủ dữ liệu cá nhân (phần không bị chặn)

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- |
| **T-14** | [#189](https://github.com/crmsaassaudi/product-management/issues/189) | `Contacts: bằng chứng đồng thuận và cơ sở đồng thuận khi nhập khẩu` | FEAT-30 (BR-30.3, 30.4, 30.7→30.10) | crm-api + crm-web | KB 10 | L |
| **T-15** | [#190](https://github.com/crmsaassaudi/product-management/issues/190) | `Contacts: hạn mức mở mặt nạ dữ liệu nhạy cảm và cảnh báo truy cập bất thường` | FEAT-04 (BR-04.5, 04.5b) | crm-api + crm-web | KB 6 | M |
| **T-16** | [#191](https://github.com/crmsaassaudi/product-management/issues/191) | `Contacts: nhật ký đọc hồ sơ ngoài phạm vi gán` | FEAT-02 (BR-02.2), CFG-05-02 | crm-api | KB 6, 15 | S |
| **T-17** | [#192](https://github.com/crmsaassaudi/product-management/issues/192) | `Contacts: xuất dữ liệu — giới hạn mục đích và định danh KYC` | FEAT-25 (BR-25.3, 25.4, 25.5) | crm-api + crm-web | KB 6 | M |

---

## Đợt 4 — Tăng trưởng: chuyển đổi, phân bổ, chấm điểm

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | Phụ thuộc | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **T-18** | [#193](https://github.com/crmsaassaudi/product-management/issues/193) | `Contacts: chuyển đổi Khách hàng Tiềm năng 1-click và hoàn tác trong 24 giờ` | FEAT-14 | crm-api + crm-web | T-05 | KB 2, 7 | L |
| **T-19** | [#194](https://github.com/crmsaassaudi/product-management/issues/194) | `Contacts: theo dõi nguồn gốc khách hàng tiềm năng theo UTM` | FEAT-32 | crm-api + crm-web | T-05 | KB 9 | M |
| **T-20** | [#195](https://github.com/crmsaassaudi/product-management/issues/195) | `Contacts: động cơ phân bổ Khách hàng Tiềm năng tự động` | FEAT-31 (10 BR) | crm-api + crm-web | T-01, T-05, T-18 | KB 9 | XL |
| **T-21** | [#196](https://github.com/crmsaassaudi/product-management/issues/196) | `Contacts: chấm điểm tiềm năng — ngưỡng MQL/SQL và chuyển giao Marketing→Sales` | FEAT-15 (BR-15.3→15.7) | crm-api + crm-web | T-01, T-05 | KB 13 | L |
| **T-22** | [#197](https://github.com/crmsaassaudi/product-management/issues/197) | `Contacts: suy giảm điểm tiềm năng theo thời gian` | FEAT-16 | crm-api | T-21 | KB 8 | M |

**T-20** phụ thuộc T-18 chứ không ngược lại: phân bổ phải biết Lead ở giai đoạn nào và ai đang phụ trách, mà cả hai do luồng chuyển đổi sinh ra. BR-31.6 (chuyển yêu cầu chờ xử lý cho người xử lý thay) còn cần trạng thái *không khả dụng* của BR-34.6 — nếu **T-24** chưa xong thì T-20 phải tự khai báo phần trạng thái này và ghi rõ trong issue để T-24 không làm trùng.

---

## Đợt 5 — Nhóm K: quyền sở hữu và cộng tác

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- |
| **T-23** | [#198](https://github.com/crmsaassaudi/product-management/issues/198) | `Contacts: chốt hợp đồng nghiệp vụ chia sẻ bản ghi với iam-tenant-authorization (ADR)` | BR-35.5, mục 7 vấn đề #8 | product-management | — | S |
| **T-24** | [#199](https://github.com/crmsaassaudi/product-management/issues/199) | `Contacts: chuyển giao quyền phụ trách và bàn giao khi nhân viên rời tổ chức` | FEAT-34 (9 BR) | crm-api + crm-web | KB 14 | L |
| **T-25** | [#200](https://github.com/crmsaassaudi/product-management/issues/200) | `Contacts: ghi chú nội bộ và bản ghi hoạt động khách hàng` | FEAT-36 (7 BR) | crm-api + crm-web | KB 18 | L |
| **T-26** | [#201](https://github.com/crmsaassaudi/product-management/issues/201) | `Contacts: chia sẻ bản ghi có thời hạn và Đội ngũ Phụ trách Khách hàng` | FEAT-35 (7 BR) | crm-api + crm-web | KB 15, 19 | XL |

**T-23 mở cổng cho cả Đợt 5** — vấn đề #8 của SRS đặt thời hạn *"trước khi mở phạm vi phát triển Nhóm K"*, không chỉ riêng FEAT-35. Nó là ticket tài liệu, cỡ nhỏ, nên cho chạy song song từ Đợt 3 để không thành đường găng.

**T-26** là phần duy nhất của loạt hoàn toàn chưa có gì trong mã nguồn (xem khảo sát bên dưới). Nó cũng bổ sung trục *thành viên Đội ngũ* cho cơ chế T-04, và mang BR-35.4a — ngoại lệ ghi duy nhất của tuyến hỗ trợ (gắn `RESTRICTED` và hạ đồng thuận về `OPT_OUT` trong lúc vé còn mở).

---

## Đợt 6 — Nhập liệu thông minh

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- |
| **T-27** | [#202](https://github.com/crmsaassaudi/product-management/issues/202) | `Contacts: trợ lý tự động ánh xạ cột khi nhập khẩu Excel/CSV` | FEAT-23 | crm-api + crm-web | KB 4 | M |

Xếp sau Đợt 3 có chủ đích: BR-30.4 buộc mỗi lượt nhập khẩu khai báo **Cơ sở đồng thuận**, nên trợ lý ánh xạ cột phải ánh xạ được cả trường đó — làm trước T-14 thì phải sửa lại wizard.

---

## Đợt 7 — Quyền chủ thể dữ liệu (bị chặn một phần)

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- |
| **T-28** | [#203](https://github.com/crmsaassaudi/product-management/issues/203) | `Contacts: hợp đồng xoá dữ liệu theo mã khách hàng với omnichat và tickets` | BR-33.8 (h), (i), mục 7 vấn đề #12 | product-management | — | S |
| **T-29** | [#204](https://github.com/crmsaassaudi/product-management/issues/204) | `Contacts: cơ chế tiếp nhận và xử lý yêu cầu quyền chủ thể dữ liệu` | FEAT-33 (8 BR) | crm-api + crm-web | KB 17, 21 | XL |

**T-29 làm được ngay mà không cần chờ Pháp chế**, với một điều kiện: mọi mốc thời hạn phải là tham số `CFG-33-01`/`CFG-33-03` chứ không phải số cắm cứng, và giá trị mặc định giữ nguyên cho tới khi vấn đề #9 có xác nhận. Đúng ranh giới đã ghi ở mục 7 của SRS: **chưa** đưa các mốc ở bảng FEAT-33 vào hợp đồng, điều khoản dịch vụ hay chính sách công bố ra ngoài. Xây cơ chế thì được; công bố cam kết thì chưa.

---

## Đợt 8 — Nghiệm thu

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- |
| **T-30** | [#205](https://github.com/crmsaassaudi/product-management/issues/205) | `crm-test: tự động hoá 22 kịch bản UAT của phân hệ Khách hàng, gồm kiểm chứng 29 sàn bắt buộc` | Mục 6 toàn bộ | crm-test | 22/22 | L |

Kịch bản 22 phải chạy trên **môi trường mang cấu hình sản xuất** — ngoại lệ có chủ đích với quy ước nới lỏng ở môi trường phi sản xuất, vì nó kiểm chính cơ chế sàn.

---

## Việc bị chặn bởi quyết định ngoài kỹ thuật

| Vấn đề mục 7 | Chặn ticket | Ảnh hưởng nếu chưa chốt |
| --- | --- | --- |
| #2 — xung đột trường đa trị khi gộp | T-09 | Chỉ chặn **lên môi trường thật**, không chặn phát triển |
| #3 — thời hạn dọn thùng rác 30/90 ngày | T-11 | Không chặn: đã là tham số `CFG-05-01`, chỉ cần chốt giá trị mặc định theo gói |
| #4 — ngưỡng điểm MQL/SQL | T-21 | Không chặn: bộ 40/70/85 là mặc định cho 90 ngày đầu |
| #7 — khai báo nhóm mục đích gửi tin | T-14 (một phần) | Phần BR-30.5 cần phân hệ gửi tin khai báo nhóm; lượt gửi không khai báo mặc định coi là Tiếp thị |
| #8 — hợp đồng với tài liệu IAM | **cả Đợt 5** | Chặn **mở phạm vi phát triển** nhóm K → giải bằng T-23 |
| #9 — thời hạn xử lý yêu cầu chủ thể dữ liệu | T-29 (giá trị) | Không chặn cơ chế; chặn việc công bố cam kết ra ngoài |
| #12 — sàn liên tài liệu omnichat/tickets | T-29 | Chặn lên môi trường thật → giải bằng T-28 |

---

## Khảo sát hiện trạng mã nguồn (sơ bộ, cần xác minh trong từng ticket)

Đây là **tín hiệu để khảo sát**, không phải kết luận về mức độ tuân thủ quy tắc — số lượt khớp từ khoá trong `crm-api/src`:

| Hạng mục | Tín hiệu | Hệ quả cho ước lượng |
| --- | --- | --- |
| Cơ chế cấu hình tenant | có module `crm-settings` | T-01 là **mở rộng**, không phải dựng mới |
| Nhật ký | có `audit-log`, `activity-log` | T-03 là bổ sung danh mục sự kiện |
| Ghi chú | có module `notes` | T-25 có thể nhỏ hơn ước lượng ban đầu |
| Suy giảm điểm | có `lead-scoring-decay.worker.ts` | T-22 có thể chỉ là hiệu chỉnh theo BR-16.1→16.4 |
| Phân bổ | có module `assignment` | T-20 cần khảo sát trước khi ước lượng lại |
| Phạm vi dữ liệu | có `data-visibility`, `role-hierarchy` | T-04 mở rộng trục quan hệ trên nền có sẵn |
| Chuyển đổi Lead | 4 tệp khớp từ khoá | T-18 có thể đã có phần khung |
| Đội ngũ phụ trách / chia sẻ bản ghi | **0 tệp khớp** | T-26 là phần greenfield thật sự |

---

## Bước kế tiếp

1. **Bắt đầu được ngay, không phụ thuộc gì:** [#176](https://github.com/crmsaassaudi/product-management/issues/176) (cơ chế cấu hình), [#178](https://github.com/crmsaassaudi/product-management/issues/178) (sổ sự kiện nhật ký), [#187](https://github.com/crmsaassaudi/product-management/issues/187) (cây doanh nghiệp), [#198](https://github.com/crmsaassaudi/product-management/issues/198) và [#203](https://github.com/crmsaassaudi/product-management/issues/203) (hai ticket tài liệu), [#205](https://github.com/crmsaassaudi/product-management/issues/205) (bộ UAT).
2. **Chạy [#198](https://github.com/crmsaassaudi/product-management/issues/198) và [#203](https://github.com/crmsaassaudi/product-management/issues/203) sớm** dù chúng nằm ở Đợt 5 và Đợt 7: mỗi cái là ticket tài liệu cỡ nhỏ nhưng mở cổng cho cả một đợt. Để muộn thì chúng thành đường găng.
3. **Khảo sát hiện trạng trước khi cam kết ước lượng** cho [#193](https://github.com/crmsaassaudi/product-management/issues/193), [#195](https://github.com/crmsaassaudi/product-management/issues/195), [#197](https://github.com/crmsaassaudi/product-management/issues/197) và [#200](https://github.com/crmsaassaudi/product-management/issues/200) — khảo sát mã nguồn cho thấy bốn phần này có thể đã có khung sẵn.
4. **Hai ticket cỡ XL** — [#180](https://github.com/crmsaassaudi/product-management/issues/180) (ma trận vòng đời) và [#195](https://github.com/crmsaassaudi/product-management/issues/195) (phân bổ Lead) — giữ nguyên 1 issue để không mất mạch phụ thuộc, tách nhỏ ở bước sprint planning.
