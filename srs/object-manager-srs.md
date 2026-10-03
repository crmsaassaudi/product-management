# SRS — Object Manager: Cấu trúc Dữ liệu, Phân quyền Trường & Chính sách Dữ liệu

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA |
| **Module** | Object Manager — Cấu hình cấu trúc dữ liệu và chính sách dữ liệu cho Khách hàng (Liên hệ), Công ty (Tài khoản), Cơ hội, Vé hỗ trợ và Công việc |
| **Ngày cập nhật** | 2026-10-02 |
| **Phiên bản** | v5.0 (Chuẩn hóa Nghiệp vụ Thuần túy — đồng bộ IAM v5.0; thay thế v4.5) |
| **Neo mã nguồn** | Chưa xác định — tài liệu đặc tả trạng thái nghiệp vụ mục tiêu, không neo vào một phiên bản triển khai cụ thể |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`contacts-srs.md`](./contacts-srs.md), [`deals-pipeline-srs.md`](./deals-pipeline-srs.md), [`tickets-srs.md`](./tickets-srs.md), [`tasks-srs.md`](./tasks-srs.md), [ADR-0001](../docs/adr/0001-group-policy-conflict-resolution.md), [ADR-0003](../docs/adr/0003-permission-config-audit-log-fail-closed.md), [ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md) |

## Ghi chú về phiên bản v5.0

Phiên bản này viết lại tài liệu theo đúng vai trò của một SRS nghiệp vụ và đồng bộ với hợp đồng phân quyền của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) v5.0. Nội dung nghiệp vụ đã chốt của bản trước được giữ về bản chất; thay đổi nằm ở:

1. **Tài liệu là chuẩn, không phải bản ghi chép hiện trạng.** Tài liệu đặc tả trạng thái nghiệp vụ mục tiêu (To-Be); mọi quy tắc ở Mục 3 là yêu cầu bắt buộc như nhau; không dùng nhãn trạng thái triển khai, không phân kỳ theo đợt phát hành. Nhu cầu chưa chốt được phương án gom tại Mục 7.
2. **Quyền là cấu hình chung.** Quyền vào khu vực cấu hình là các quyền quản trị do tài liệu này khai báo trong danh mục quyền của IAM, không gắn cứng cho một tên vai trò. Mỗi loại dữ liệu là một dòng của ma trận quyền IAM.
3. **Thu hẹp quyền trên trường không bị chặn khi nhật ký gặp sự cố** (ghi bù theo [ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md)); nới rộng vẫn đóng khi lỗi.
4. **Trường nhạy cảm do doanh nghiệp khai báo** dùng khung che dữ liệu chung của IAM; **hàng đợi chưa phân công** dùng khung hàng đợi chung của IAM.
5. Mỗi tính năng có bảng Tiêu chí Chấp nhận; giá trị doanh nghiệp có thể muốn khác nhau là tham số tại Phụ lục B; quyết định đã chốt ghi tại Phụ lục C.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả yêu cầu chức năng và phi chức năng của **Object Manager** — khu vực cấu hình cho phép doanh nghiệp tự tùy biến cấu trúc dữ liệu, quy tắc toàn vẹn, phân quyền trên trường và chính sách dữ liệu của các loại dữ liệu nghiệp vụ, mà không cần đội phát triển can thiệp, đồng thời bảo đảm mọi cấu hình đó không bao giờ trở thành đường vòng vượt qua hợp đồng phân quyền của workspace.

### 1.2 Phạm vi

**Trong phạm vi — 10 tính năng, một nhóm chức năng:**

- Danh mục loại dữ liệu, năng lực khả dụng, vị trí của từng loại dữ liệu trong ma trận quyền và các quyền quản trị của Object Manager (`FEAT-01`).
- Trường tuỳ biến, kiểu dữ liệu và khai báo trường nhạy cảm (`FEAT-02`).
- Phân quyền trường theo Nhóm và bố cục biểu mẫu (`FEAT-03`).
- Quy tắc kiểm tra dữ liệu (`FEAT-04`).
- Giai đoạn vòng đời và ma trận chuyển đổi khách hàng tiềm năng (`FEAT-05`).
- Trạng thái và nguồn theo loại dữ liệu (`FEAT-06`).
- Quy trình bán hàng và giai đoạn Cơ hội (`FEAT-07`).
- Danh sách hiển thị dùng chung (`FEAT-08`).
- Cấu hình nâng cao: chống trùng, điều kiện đóng Cơ hội, phân công tự động và hàng đợi chưa phân công (`FEAT-09`).
- Nhật ký thay đổi cấu hình và hoàn tác phân quyền trường (`FEAT-10`).

**Ngoài phạm vi:**

- Nghiệp vụ vận hành chi tiết của người dùng trên từng loại dữ liệu (thao tác vé, chiến dịch, bàn giao bản ghi) — thuộc SRS phân hệ sở hữu loại dữ liệu đó.
- Vai trò, nhóm, đơn vị tổ chức, mức truy cập theo bản ghi, chính sách truy cập và vòng đời thành viên — thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md). Tài liệu này chỉ quy định phân quyền **trên trường** và vị trí của nó ở bước 4 của thứ tự hợp nhất quyền ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`).
- Bộ lọc/danh sách cá nhân người dùng tự lưu cho riêng mình.
- Chỉ số thành công và mục tiêu kinh doanh — thuộc tài liệu kế hoạch sản phẩm; tài liệu này chỉ bảo đảm dữ liệu đo tồn tại (`NFR-09`).
- Nhật ký thao tác ở cấp bản ghi (ai đã đọc/ghi giá trị nào trên một bản ghi) — năng lực của tầng lõi CRM; là điều kiện tiên quyết để truy vết tại `BR-03.4`.

### 1.3 Đối tượng đọc

- **Product Owner / Business Analyst:** nguồn chuẩn về nghiệp vụ cấu hình dữ liệu trước khi đề xuất thay đổi.
- **Kỹ sư phát triển:** hiểu ý định nghiệp vụ; chi tiết triển khai kỹ thuật không nằm trong tài liệu này.
- **QA:** căn cứ viết kịch bản kiểm thử từ các bảng Tiêu chí Chấp nhận và Mục 6.
- **Customer Success / Solution Consultant:** nắm năng lực và giới hạn cấu hình khi tư vấn khách hàng doanh nghiệp.

### 1.4 Thuật ngữ & viết tắt

Các thuật ngữ phân quyền chung (Mức truy cập, Ma trận quyền, Quyền quản trị, Người có toàn quyền, Sàn bắt buộc, Bản ghi của mình, Bản ghi thuộc một đơn vị, Thu hẹp/Nới rộng quyền, Nhóm) dùng đúng định nghĩa tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4, không định nghĩa lại.

| Thuật ngữ | Giải thích |
| --- | --- |
| **Loại dữ liệu (Đối tượng)** | Một loại bản ghi nghiệp vụ: Khách hàng (Liên hệ), Công ty (Tài khoản), Cơ hội, Vé hỗ trợ, Công việc. Mỗi loại dữ liệu là một dòng của ma trận quyền IAM (`BR-01.3`). |
| **Trường tuỳ biến** | Thuộc tính do doanh nghiệp tự định nghĩa thêm vào một loại dữ liệu. |
| **Nhóm** | Tập thành viên do doanh nghiệp tạo; là cùng một thực thể với Nhóm của IAM. Phân quyền trường, bố cục và danh sách hiển thị được gán theo Nhóm. |
| **Phân quyền trường** | Chính sách, theo từng Nhóm, quyết định một người được làm gì với một trường bên trong bản ghi mà họ đã được vào. Gồm hai chiều độc lập: **Mức quyền trên trường** (Xem & Sửa / Chỉ xem / Ẩn) và **Mức hiển thị giá trị** (Hiện đầy đủ / Che một phần / Che hoàn toàn) — `BR-03.1`. Tách tên với "Mức truy cập" của IAM, vốn là giá trị của một ô trong ma trận quyền. |
| **Trường nhạy cảm** | Trường mà giá trị phải được che khi hiển thị cho người không có quyền xem đầy đủ. Có trường nhạy cảm của hệ thống (do SRS phân hệ khai báo) và trường nhạy cảm do doanh nghiệp khai báo trên trường tuỳ biến (`BR-02.7`). |
| **Bố cục mặc định** | Bố cục và phân quyền trường dùng cho trường mà người dùng không thuộc Nhóm nào có cấu hình (`BR-03.2`). |
| **Quy tắc kiểm tra dữ liệu** | Ràng buộc mà giá trị của trường phải thỏa khi lưu bản ghi. |
| **Quy trình bán hàng** | Chuỗi giai đoạn để theo đuổi và chốt một Cơ hội. |
| **Giai đoạn Cơ hội** | Một bước bên trong một Quy trình bán hàng, gắn với tỷ lệ thành công kỳ vọng và thời gian lưu kỳ vọng. |
| **Điều kiện qua giai đoạn** | Tập trường phải có giá trị để Cơ hội vào một Giai đoạn cụ thể. |
| **Danh sách hiển thị dùng chung** | Bộ cột, điều kiện lọc và thứ tự sắp xếp do người có quyền quản trị tạo và gán cho Nhóm. |
| **Cờ thiếu dữ liệu do giới hạn quyền** | Dấu hiệu gắn lên bản ghi được lưu nhờ miễn trừ ràng buộc bắt buộc, vì người lưu không có quyền nhập trường đó (`BR-05.5`). |
| **Vai trò liên hệ trong Cơ hội** | Vai trò nghiệp vụ của một Liên hệ trong một Cơ hội (Người quyết định, Người phê duyệt, Người ảnh hưởng…). |
| **Hàng đợi chưa phân công** | Hàng đợi của một đơn vị tiếp nhận, chứa bản ghi mà quy tắc phân công tự động không tìm được người nhận; vận hành theo khung hàng đợi của IAM (`BR-09.5`). |
| **Tiến trình chạy thay người dùng** | Quy trình tự động hóa, nhập/xuất hàng loạt, tích hợp bên ngoài thực hiện đọc/ghi dữ liệu thay cho một người khởi chạy hoặc người chịu trách nhiệm. |
| **Nhật ký thay đổi cấu hình** | Lịch sử ai đã thay đổi cấu hình gì trong Object Manager, lúc nào, giá trị trước và sau (`FEAT-10`). |

### 1.5 Tài liệu tham khảo

- [`CONTEXT.md`](../CONTEXT.md) — glossary dùng chung, mục "Object Manager" và "IAM & Phân quyền Workspace".
- [ADR-0001](../docs/adr/0001-group-policy-conflict-resolution.md) — phân giải xung đột phân quyền trường giữa các Nhóm (gồm phần Bổ sung 2026-08-23).
- [ADR-0003](../docs/adr/0003-permission-config-audit-log-fail-closed.md) — nhật ký thay đổi cấu hình quyền đóng khi lỗi.
- [ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md) — thao tác thu hẹp quyền không bị chặn bởi sự cố nhật ký.

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

1. **Mỗi doanh nghiệp có cấu trúc dữ liệu khác nhau:** buộc mọi khách hàng dùng chung một bộ trường và giai đoạn khiến họ nhập dữ liệu vào chỗ sai hoặc bỏ CRM.
2. **Dữ liệu nhạy cảm lộ giữa các phòng ban:** không phân quyền được tới từng trường thì phải chọn giữa cho mọi người thấy hết hoặc không ai thấy bản ghi.
3. **Dữ liệu bẩn chảy vào báo cáo:** thiếu quy tắc kiểm tra thực thi đồng nhất trên mọi kênh nhập liệu.
4. **Cấu hình tự do làm tê liệt vận hành:** xoá một trường đang chặn giai đoạn, xoá giai đoạn đang có Cơ hội, đặt bắt buộc một trường người dùng không được thấy.
5. **Cấu hình thành đường vòng phân quyền:** bố cục, danh sách hiển thị, tác vụ tự động hay phân công tự động mở thêm điều mà ma trận quyền không cho.

### 2.2 Loại dữ liệu nghiệp vụ

| Loại dữ liệu | Mô tả nghiệp vụ | SRS sở hữu nghiệp vụ vận hành |
| --- | --- | --- |
| **Khách hàng (Liên hệ)** | Cá nhân khách hàng hoặc người liên hệ đại diện của doanh nghiệp | [`contacts-srs.md`](./contacts-srs.md) |
| **Công ty (Tài khoản)** | Pháp nhân là khách hàng hoặc đối tác | [`contacts-srs.md`](./contacts-srs.md) |
| **Cơ hội** | Thương vụ đang được theo đuổi qua các giai đoạn của Quy trình bán hàng | [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) |
| **Vé hỗ trợ** | Khiếu nại, thắc mắc, sự cố của khách hàng | [`tickets-srs.md`](./tickets-srs.md) |
| **Công việc** | Việc cần làm, có thể gắn với khách hàng, công ty, cơ hội hoặc vé | [`tasks-srs.md`](./tasks-srs.md) |

### 2.3 Vai trò người dùng

**Vai trò thao tác (là các cột của Ma trận tại Mục 5):**

| Vai trò | Trách nhiệm trong Object Manager |
| --- | --- |
| **Người có toàn quyền** (Chủ sở hữu, Quản trị viên) | Có mọi quyền quản trị của Object Manager; không bị phân quyền trường giới hạn, trừ Sàn bắt buộc nêu rõ áp cả lên họ (`BR-03.2b`). Là người duy nhất đặt và đổi đơn vị tiếp nhận của hàng đợi (`BR-09.5`). |
| **Thành viên giữ quyền quản trị của Object Manager** | Thành viên được cấp một hoặc vài quyền quản trị tại `BR-01.4` qua vai trò. Chỉ làm đúng phần quyền đó, luôn trong trần năng lực của chính mình (`BR-03.8`). |
| **Người dùng nghiệp vụ** | Mọi thành viên thao tác dữ liệu theo vai trò dựng sẵn ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`) hoặc vai trò tự tạo; chịu phân quyền trường, quy tắc kiểm tra và danh sách hiển thị. |
| **Kiểm toán viên** | Người giữ vai trò dựng sẵn Kiểm toán hoặc Kiểm toán quyền; tra cứu nhật ký thay đổi cấu hình (`BR-10.4`). |
| **Nhân sự vận hành nền tảng** | Đội ngũ của nhà cung cấp; chỉ vào cấu hình của workspace trong Phiên hỗ trợ và Phiên triển khai theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-08`, mọi thao tác được ghi vào nhật ký truy cập của nhà cung cấp. |
| **Hệ thống** | Tính trường công thức, kiểm tra quy tắc, phân công tự động, gắn và gỡ cờ thiếu dữ liệu, ghi nhật ký và ghi bù. |

**Chủ thể chịu tác động (không có cột riêng trong Ma trận):**

| Chủ thể | Ghi chú |
| --- | --- |
| **Tiến trình chạy thay người dùng** | Quy trình tự động hóa, nhập/xuất hàng loạt, tích hợp bên ngoài. Miễn trừ phân quyền trường có giới hạn theo `BR-03.4`; luôn chịu quy tắc kiểm tra dữ liệu và phạm vi bản ghi của người khởi chạy. |
| **Tác nhân AI** | Luôn nhận giá trị đã che của mọi trường nhạy cảm, kể cả trường do doanh nghiệp khai báo ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-40.1`). |

### 2.4 Nguyên tắc nghiệp vụ nền tảng

Object Manager áp nguyên văn tám nguyên tắc tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 2.4. Ba hệ quả riêng cho module:

**Nguyên tắc OM-1 — Cấu hình không bao giờ mở thêm quyền.** Bố cục, danh sách hiển thị, quy tắc phân công và cấu hình nâng cao chỉ sắp xếp, ràng buộc hoặc định tuyến trong phạm vi mà ma trận quyền và phân quyền trường đã cho; không cái nào là một nguồn nới quyền.

**Nguyên tắc OM-2 — Mức bảo vệ đi theo dữ liệu, không theo trường chứa nó.** Giá trị của trường được bảo vệ không được chuyển sang nơi có mức bảo vệ thấp hơn qua bất kỳ đường nào: tác vụ tự động, trường công thức, danh sách hiển thị, tệp xuất.

**Nguyên tắc OM-3 — Không cấu hình nào được tự làm tê liệt vận hành.** Thao tác cấu hình làm một bản ghi kẹt vĩnh viễn hoặc làm một điều kiện nghiệp vụ biến mất âm thầm bị chặn, kèm danh sách nơi đang bị ảnh hưởng.

### 2.5 Quy ước thời gian nghiệp vụ

Mọi mốc thời gian trong tài liệu (thời điểm thay đổi cấu hình, thời hạn lưu nhật ký, cảnh báo trễ giai đoạn tính theo ngày) theo **múi giờ và lịch làm việc của workspace** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-04`). Số ngày "Thời gian lưu kỳ vọng" của Giai đoạn Cơ hội kết thúc lúc 23:59:59 của ngày cuối theo múi giờ workspace.

### 2.6 Bảng tổng hợp tính năng

| Mã | Tên tính năng nghiệp vụ |
| --- | --- |
| `FEAT-01` | Danh mục loại dữ liệu, năng lực khả dụng & quyền quản trị của Object Manager |
| `FEAT-02` | Quản lý trường tuỳ biến & khai báo trường nhạy cảm |
| `FEAT-03` | Phân quyền trường & bố cục biểu mẫu theo Nhóm |
| `FEAT-04` | Quy tắc kiểm tra dữ liệu |
| `FEAT-05` | Giai đoạn vòng đời & ma trận chuyển đổi |
| `FEAT-06` | Trạng thái & nguồn theo loại dữ liệu |
| `FEAT-07` | Quy trình bán hàng & giai đoạn Cơ hội |
| `FEAT-08` | Danh sách hiển thị dùng chung |
| `FEAT-09` | Cấu hình nâng cao, phân công tự động & hàng đợi chưa phân công |
| `FEAT-10` | Nhật ký thay đổi cấu hình & hoàn tác phân quyền trường |

---

## 3. Đặc tả yêu cầu chức năng

*Cách đọc:* mỗi tính năng gồm Mô tả nghiệp vụ, Vai trò sử dụng chính (mô tả — quyền có/không thuộc Ma trận Mục 5), Điều kiện tiên quyết, Luồng chính, Quy tắc nghiệp vụ `BR-xx.n` kèm Lý do nghiệp vụ, và bảng Tiêu chí Chấp nhận. Mã của tài liệu khác luôn viết kèm tên tài liệu.

### FEAT-01 — Danh mục loại dữ liệu, năng lực khả dụng & quyền quản trị của Object Manager

**Mô tả nghiệp vụ:** Cung cấp bức tranh tổng quan về các loại dữ liệu, năng lực thao tác của từng loại, vị trí của chúng trong ma trận quyền của workspace, và các quyền quản trị dùng để vào từng khu vực cấu hình.

**Vai trò sử dụng chính:** Người giữ bất kỳ quyền quản trị nào của Object Manager; Người có toàn quyền.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Mở Object Manager → hệ thống chỉ hiển thị các khu vực mà người dùng có quyền quản trị tương ứng (`BR-01.4`).
2. Xem danh mục loại dữ liệu kèm năng lực khả dụng, số trường tuỳ biến đang dùng, và liên kết tới dòng tương ứng trong Danh mục quyền của IAM.

**Quy tắc nghiệp vụ:**

- **`BR-01.1` (Năng lực chuẩn của một loại dữ liệu):** Mỗi loại dữ liệu hướng tới tập năng lực chuẩn: Tạo/Sửa/Xoá, Cập nhật hàng loạt, Gán người phụ trách hàng loạt, Gắn thẻ hàng loạt, Nhập từ tệp, Xuất, Vòng đời/Giai đoạn, Gộp bản ghi trùng.

  **Lý do nghiệp vụ:** một danh mục năng lực chung cho phép doanh nghiệp so sánh và tư vấn viên trả lời nhất quán "loại dữ liệu này làm được gì", thay vì mỗi màn hình tự có một bộ nút.

- **`BR-01.2` (Năng lực khả dụng theo khai báo của phân hệ sở hữu):** Năng lực khả dụng của từng loại dữ liệu do SRS phân hệ sở hữu nó khai báo (Mục 2.2). Object Manager hiển thị đúng tập năng lực đó và màn hình danh sách bản ghi chỉ mở các nút hàng loạt, nhập, xuất, gộp khớp đúng khai báo. Một năng lực có thao tác tương ứng trong ma trận quyền thì người dùng còn phải có ô đó khác Không có mới thấy nút ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.7`).

  **Lý do nghiệp vụ:** khi hai tài liệu cùng liệt kê năng lực, chúng sẽ lệch nhau; nút hiện ra mà thao tác bị từ chối, hoặc năng lực đã có mà không ai tìm thấy.

- **`BR-01.3` (Mỗi loại dữ liệu là một dòng của ma trận quyền):** Năm loại dữ liệu mà Object Manager quản lý (Mục 2.2) là năm dòng trong Danh mục quyền và ma trận quyền của IAM ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-24`, `FEAT-25`), với các cột thao tác chuẩn (Xem, Tạo, Sửa, Xoá, Xuất, Nhập, Gán người phụ trách) cộng thao tác đặc thù do SRS phân hệ khai báo. Với mỗi dòng:
  - Ma trận mặc định của vai trò dựng sẵn trên dòng đó do SRS phân hệ sở hữu khai báo ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-29.6`); Object Manager không khai báo lại. Thao tác hay thao tác đặc thù mới bổ sung vào dòng có ô Không có ở mọi vai trò tự tạo ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-24.2`) và tới vai trò dựng sẵn theo đồng bộ có kiểm soát ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-29.1`).
  - Ô mà không vai trò nào của một người khai báo dùng **mức nền** của workspace cho loại dữ liệu đó ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-34.1`). Nâng mức nền hay bật công khai đọc chỉ Người có toàn quyền thực hiện ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-34.5`).
  - Cấu hình trong Object Manager (trường, phân quyền trường, danh sách hiển thị, phân công) không thêm, bớt hay đổi ô nào của dòng; quyền trên bản ghi chỉ do ma trận IAM quyết định.
  - Loại dữ liệu tuỳ biến do doanh nghiệp tự tạo chưa thuộc phạm vi đặc tả (Mục 7, điểm 1).

  **Lý do nghiệp vụ:** một loại dữ liệu nằm ngoài ma trận quyền là vùng không ai kiểm soát được ai thấy gì; nếu Object Manager khai báo lại mặc định của vai trò dựng sẵn, hai tài liệu sẽ lệch nhau và điều chỉnh ô không áp nhất quán.

- **`BR-01.4` (Quyền quản trị của Object Manager):** Object Manager khai báo các quyền quản trị sau vào Danh mục quyền của IAM ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-24`); mỗi khu vực cấu hình chỉ mở cho người có quyền tương ứng:

  | Quyền quản trị | Phạm vi | Mặc định trên vai trò dựng sẵn |
  | --- | --- | --- |
  | **Quản lý cấu hình đối tượng** | Trường tuỳ biến (`FEAT-02`, trừ khai báo nhạy cảm), quy tắc kiểm tra (`FEAT-04`), vòng đời & ma trận chuyển đổi (`FEAT-05`), trạng thái, nguồn & Nhóm công việc (`FEAT-06`), quy trình bán hàng (`FEAT-07`), cấu hình nâng cao và quy tắc phân công (`FEAT-09`, trừ đơn vị tiếp nhận) | Không vai trò dựng sẵn nào |
  | **Quản lý phân quyền trường & bố cục** | Phân quyền trường, bố cục biểu mẫu (`FEAT-03`), khai báo trường nhạy cảm (`BR-02.7`), hoàn tác phân quyền trường (`BR-10.5`), công cụ xem trước quyền thực tế (`BR-03.3`) | Không vai trò dựng sẵn nào |
  | **Quản lý danh sách hiển thị dùng chung** | `FEAT-08` | Không vai trò dựng sẵn nào |
  | **Xem nhật ký cấu hình đối tượng** | Tra cứu `FEAT-10` | Kiểm toán, Kiểm toán quyền |

  Người có toàn quyền có tất cả các quyền trên. Doanh nghiệp đưa các quyền này vào bất kỳ vai trò nào; không quyền nào gắn với một tên vai trò. Quyền khả dụng theo trần quyền của gói dịch vụ ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-05`). Đặt và đổi đơn vị tiếp nhận của hàng đợi không thuộc các quyền trên mà chỉ Người có toàn quyền thực hiện ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.12`).

  **Lý do nghiệp vụ:** doanh nghiệp cần giao việc cấu hình trường cho một chuyên viên vận hành mà không trao toàn quyền workspace; tách quyền phân quyền trường khỏi quyền cấu hình đối tượng vì chỉ quyền thứ nhất làm thay đổi ai thấy gì. Kiểm toán và Kiểm toán quyền mặc định có quyền xem nhật ký cấu hình vì việc của họ là đưa ra bằng chứng "ai đã đổi cấu hình dữ liệu và quyền trên trường, khi nào"; quyền này chỉ đọc nhật ký, không đọc được dữ liệu bản ghi ngoài mức Xem của họ (`BR-10.4`), nên không làm rộng thêm điều họ thấy về khách hàng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-01.1.1` | Người có quyền Quản lý cấu hình đối tượng | Mở danh mục loại dữ liệu | Thấy từng loại dữ liệu kèm năng lực khả dụng và số trường tuỳ biến đang dùng |
| `AC-01.2.1` | SRS phân hệ của một loại dữ liệu không khai báo năng lực Gộp | Mở danh sách bản ghi của loại đó | Không có nút Gộp |
| `AC-01.2.2` | Loại dữ liệu có năng lực Xuất; người dùng có ô (loại đó, Xuất) = Không có | Mở danh sách bản ghi | Không thấy nút Xuất |
| `AC-01.3.1` | Workspace mới | Mở Danh mục quyền của IAM | Có đủ năm dòng Khách hàng, Công ty, Cơ hội, Vé hỗ trợ, Công việc, mỗi dòng đủ cột thao tác chuẩn và thao tác đặc thù do SRS phân hệ khai báo |
| `AC-01.3.2` | Vai trò tự tạo X không khai báo dòng Vé hỗ trợ, mức nền chưa đổi | Người giữ X xem danh sách vé | Chỉ thấy vé mình phụ trách; quyền hiệu lực ghi nguồn "Mức nền" |
| `AC-01.3.3` | Thành viên có quyền Quản lý cấu hình workspace nhưng không có toàn quyền | Thử nâng mức nền Xem của Công ty lên Toàn workspace | Bị vô hiệu kèm giải thích chỉ Người có toàn quyền được nới rộng mức nền |
| `AC-01.3.4` | Người có quyền Quản lý cấu hình đối tượng | Tìm cách đổi ô của một vai trò trong Object Manager | Không có; chỉ có lối dẫn sang khu quản trị vai trò của IAM |
| `AC-01.4.1` | Thành viên không giữ quyền quản trị nào của Object Manager | Mở trực tiếp đường dẫn khu vực cấu hình trường | Bị từ chối; không thấy mục Object Manager trong trình đơn |
| `AC-01.4.2` | Thành viên chỉ giữ quyền Quản lý danh sách hiển thị dùng chung | Mở Object Manager | Chỉ thấy khu vực Danh sách hiển thị dùng chung |
| `AC-01.4.3` | Vai trò tự tạo được thêm quyền Quản lý phân quyền trường & bố cục | Người giữ vai trò mở Object Manager | Thấy khu vực phân quyền trường và bố cục; không thấy khu vực quy tắc kiểm tra |

---

### FEAT-02 — Quản lý trường tuỳ biến & khai báo trường nhạy cảm

**Mô tả nghiệp vụ:** Cho phép doanh nghiệp mở rộng mô hình dữ liệu của bất kỳ loại dữ liệu nào, và khai báo trường nào chứa dữ liệu nhạy cảm cần che.

**Vai trò sử dụng chính:** Người có quyền Quản lý cấu hình đối tượng (trường); người có quyền Quản lý phân quyền trường & bố cục (khai báo nhạy cảm).

**Điều kiện tiên quyết:** Loại dữ liệu đang hoạt động.

**Danh mục kiểu dữ liệu chuẩn:** Văn bản ngắn (tối đa 255 ký tự); Văn bản dài / định dạng phong phú; Số (cấu hình được số chữ số thập phân); Tiền tệ; Phần trăm (0 – 100%); Ngày; Ngày & giờ (theo múi giờ); Hộp kiểm (Có/Không); Danh sách chọn một; Danh sách chọn nhiều; Liên kết tới bản ghi của loại dữ liệu khác; Công thức (chỉ đọc, hệ thống tự tính từ các trường khác).

**Luồng chính:**

1. Chọn loại dữ liệu → Thêm trường → chọn kiểu dữ liệu, nhãn, mã định danh, thuộc tính của kiểu.
2. (Tuỳ chọn, người có quyền Quản lý phân quyền trường & bố cục) đánh dấu Trường nhạy cảm và chọn mẫu che.
3. Lưu → trường xuất hiện trên cấu hình liên quan và được thêm vào cuối phần bố cục do người cấu hình chỉ định (`BR-03.7`).

**Quy tắc nghiệp vụ:**

- **`BR-02.1` (Mã định danh duy nhất, không đổi):** Mỗi trường có một mã định danh không đổi dùng trong nhập/xuất và tích hợp; mã của trường tuỳ biến không trùng với trường chuẩn hay trường tuỳ biến khác của cùng loại dữ liệu.

  **Lý do nghiệp vụ:** tệp nhập và tích hợp tham chiếu trường bằng mã; mã trùng hay đổi làm dữ liệu đổ vào sai trường mà không ai thấy.

- **`BR-02.2` (Bảo toàn dữ liệu lịch sử khi sửa danh sách chọn):** Sửa nhãn hoặc vô hiệu hóa một lựa chọn: bản ghi cũ giữ nguyên giá trị đó ở chế độ chỉ đọc; lựa chọn bị vô hiệu không xuất hiện khi tạo mới hay chỉnh sửa tiếp.

  **Lý do nghiệp vụ:** báo cáo lịch sử phải giữ đúng giá trị tại thời điểm phát sinh; xoá lựa chọn làm báo cáo các kỳ trước đổi số.

- **`BR-02.3` (Xoá trường là vô hiệu hóa):** "Xoá" một trường tuỳ biến là vô hiệu hóa: trường không còn xuất hiện với ai, dữ liệu đã nhập không mất và vẫn tra cứu lịch sử được; mã định danh không được tái sử dụng. Trường đã vô hiệu hóa **không tính** vào hạn mức trường (`BR-02.6`).

  **Lý do nghiệp vụ:** một mã chỉ mang một ý nghĩa trong suốt lịch sử dữ liệu; tính trường đã vô hiệu vào hạn mức sẽ chặn một doanh nghiệp vận hành lâu năm tạo trường mới dù đang dùng rất ít trường.

- **`BR-02.4` (Trường công thức chỉ do hệ thống tính):** Không kênh nào — nhập tay, nhập tệp, tác vụ tự động, tích hợp — ghi đè được giá trị trường công thức.

  **Lý do nghiệp vụ:** giá trị công thức là kết quả suy ra; cho ghi đè thì cùng một trường vừa là số tính vừa là số nhập tay và báo cáo không còn đáng tin.

- **`BR-02.5` (Cấu hình phụ thuộc khi xoá trường):** Hệ thống phân xử theo hai nhóm tham chiếu:
  - **Tự động gỡ** (tham chiếu hiển thị/kiểm tra): phân quyền trường (`FEAT-03`), bố cục, quy tắc kiểm tra (`FEAT-04`), danh sách hiển thị dùng chung (`FEAT-08`).
  - **Chặn xoá** (tham chiếu là điều kiện để bản ghi tiến trình): trường bắt buộc theo giai đoạn vòng đời (`BR-05.2`), ma trận chuyển đổi (`BR-05.3`), điều kiện qua giai đoạn (`BR-07.3`), điều kiện đóng Cơ hội (`BR-09.2`), trường xác định khu vực (`BR-09.3`), và trường công thức đang tham chiếu trường đó. Hệ thống nêu đúng danh sách nơi đang tham chiếu để người cấu hình gỡ trước.

  **Lý do nghiệp vụ:** tự động gỡ một điều kiện chặn làm kỷ luật quy trình biến mất âm thầm; để tham chiếu treo làm bản ghi kẹt vĩnh viễn ở giai đoạn đó (Nguyên tắc OM-3).

- **`BR-02.6` (Hạn mức trường):** Mỗi loại dữ liệu có tối đa số trường tuỳ biến đang hoạt động theo `CFG-02-01`. Chạm ngưỡng thì hệ thống chặn tạo mới và cảnh báo ngay trên màn hình tạo trường, kèm số trường đang dùng.

  **Lý do nghiệp vụ:** cam kết hiệu năng của biểu mẫu và danh sách (`NFR-08`) chỉ kiểm chứng được khi có trần; báo trước khi người dùng nhập xong cấu hình tránh công sức bỏ phí.

- **`BR-02.7` (Khai báo trường nhạy cảm theo khung che dữ liệu chung):** Người có quyền Quản lý phân quyền trường & bố cục đánh dấu một trường tuỳ biến là **Trường nhạy cảm** và chọn **mẫu che**. Mẫu che mặc định theo kiểu giá trị của khung chung ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-40`): email giữ ký tự đầu và tên miền; số điện thoại giữ 4 số cuối; tiền, tỷ lệ, mã số thuế ẩn hoàn toàn; còn lại thay toàn bộ bằng ký hiệu che. Khi trường đã là nhạy cảm:
  - **Quyền xem đầy đủ** được trao qua chiều Mức hiển thị giá trị của phân quyền trường (`BR-03.1`) = Hiện đầy đủ cho Nhóm cụ thể. Nhóm chưa được trao, và Bố cục mặc định, hiển thị theo mẫu che (Che một phần nếu mẫu giữ lại một phần ký tự, Che hoàn toàn nếu không).
  - Tác nhân AI luôn nhận giá trị đã che ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-40.1`); tệp xuất của người không có quyền xem đầy đủ chứa giá trị đã che; dữ liệu gốc không đổi ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-40.2`).
  - Trường công thức tham chiếu một trường nhạy cảm tự động là trường nhạy cảm với mẫu che không lỏng hơn (Nguyên tắc OM-2); với từng người, trường công thức còn kế thừa mức hạn chế nhất của mọi trường nguồn theo `BR-03.5`.
  - Với người không có quyền xem đầy đủ, giá trị thật của trường không dùng được để tìm, lọc, sắp xếp hay nhóm ở bất kỳ kênh nào theo `BR-03.5`.
  - Trường nhạy cảm của hệ thống do SRS phân hệ khai báo (ví dụ [`contacts-srs.md`](./contacts-srs.md) `FEAT-04`) không bỏ đánh dấu được và không nới mẫu che dưới mức phân hệ đã đặt; phân quyền trường chỉ làm nó chặt hơn ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-40.3`). Trường mang Sàn bắt buộc của phân hệ không đặt vượt sàn được ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.3`).
  - Đánh dấu nhạy cảm là thu hẹp; bỏ đánh dấu hay đổi sang mẫu che lỏng hơn là nới rộng — theo `BR-10.3`.

  **Lý do nghiệp vụ:** số căn cước hay số tài khoản ngân hàng do doanh nghiệp tự thêm cần được che như dữ liệu nhạy cảm của hệ thống; dùng chung một khung che thì tác nhân AI, tệp xuất và màn hình hiển thị không lệch nhau, và một trường công thức không thành đường lộ giá trị gốc.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-02.1.1` | Loại Khách hàng đã có trường chuẩn mã "email" | Tạo trường tuỳ biến cùng mã "email" | Bị từ chối ngay tại ô mã, nêu trường đang dùng mã đó |
| `AC-02.2.1` | 120 bản ghi mang lựa chọn "Đối tác bạc" | Vô hiệu hóa lựa chọn này | 120 bản ghi vẫn hiện "Đối tác bạc" ở chế độ chỉ đọc; biểu mẫu tạo mới không còn lựa chọn đó |
| `AC-02.3.1` | Trường "Mã ưu đãi cũ" có dữ liệu trên 500 bản ghi | Xoá trường | Trường biến mất khỏi biểu mẫu và danh sách; tra cứu lịch sử vẫn thấy giá trị cũ; số trường đang dùng giảm 1 |
| `AC-02.3.2` | Trường "Mã ưu đãi cũ" đã vô hiệu hóa | Tạo trường mới cùng mã | Bị từ chối, nêu mã đã từng được dùng |
| `AC-02.4.1` | Trường công thức "Giá trị sau chiết khấu" | Nhập tệp có cột ghi giá trị cho trường này | Cột bị bỏ qua, báo cáo nhập nêu trường chỉ do hệ thống tính |
| `AC-02.5.1` | Trường "Ngân sách" đang là điều kiện qua giai đoạn "Báo giá" | Xoá trường | Bị chặn, kèm danh sách: Quy trình "B2B", Giai đoạn "Báo giá" |
| `AC-02.5.2` | Trường "Sở thích" chỉ có trong một danh sách hiển thị và một quy tắc kiểm tra | Xoá trường | Thành công; danh sách hiển thị và quy tắc không còn tham chiếu trường |
| `AC-02.6.1` | Loại Cơ hội đã đạt hạn mức `CFG-02-01` | Mở màn hình tạo trường | Nút tạo bị vô hiệu kèm số trường đang dùng và hạn mức, trước khi người dùng nhập gì |
| `AC-02.7.1` | Trường tuỳ biến "Số căn cước" vừa được đánh dấu nhạy cảm, mẫu "thay toàn bộ" | Nhân viên thuộc Nhóm chưa được trao Hiện đầy đủ mở bản ghi | Thấy trường có dữ liệu nhưng giá trị bị che hoàn toàn |
| `AC-02.7.2` | Tình huống `AC-02.7.1`; Nhóm "Kiểm soát rủi ro" được đặt Hiện đầy đủ | Thành viên Nhóm mở bản ghi | Thấy giá trị đầy đủ |
| `AC-02.7.3` | Tình huống `AC-02.7.2` | Thành viên Nhóm "Kiểm soát rủi ro" hỏi trợ lý AI số căn cước của khách hàng | AI trả giá trị đã che |
| `AC-02.7.4` | Trường công thức "4 số cuối căn cước" tham chiếu "Số căn cước" | Mở cấu hình trường công thức | Trường công thức hiển thị là nhạy cảm, mẫu che không lỏng hơn trường gốc |
| `AC-02.7.5` | Trường hệ thống "Số định danh cá nhân" do phân hệ Khách hàng khai báo nhạy cảm | Người có quyền Quản lý phân quyền trường & bố cục thử bỏ đánh dấu nhạy cảm | Lựa chọn bị vô hiệu kèm giải thích nguồn khai báo |
| `AC-02.7.6` | Người không có quyền xem đầy đủ "Số căn cước" | Xuất danh sách khách hàng | Cột "Số căn cước" trong tệp mang giá trị đã che |
| `AC-02.7.7` | Tình huống `AC-02.7.6` | Lọc danh sách theo "Số căn cước bắt đầu bằng 079" | Không chọn được trường làm điều kiện lọc theo giá trị |

---

### FEAT-03 — Phân quyền trường & bố cục biểu mẫu theo Nhóm

**Mô tả nghiệp vụ:** Kiểm soát, theo từng Nhóm, việc một người được xem, sửa, không thấy hay chỉ thấy giá trị đã che của từng trường, và tổ chức bố cục biểu mẫu nhập liệu.

**Vai trò sử dụng chính:** Người có quyền Quản lý phân quyền trường & bố cục (cấu hình); mọi người dùng nghiệp vụ và tiến trình chạy thay người dùng (chịu tác động).

**Điều kiện tiên quyết:** Có ít nhất một Nhóm hoặc dùng Bố cục mặc định.

**Luồng chính:**

1. Chọn loại dữ liệu → chọn Nhóm (hoặc Bố cục mặc định) → đặt Mức quyền trên trường và Mức hiển thị giá trị cho từng trường.
2. Màn hình hiển thị trước số người bị ảnh hưởng, các trường bị nới rộng và bị thu hẹp.
3. Lưu → có hiệu lực theo thời hạn của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `NFR-04`; ghi nhật ký theo `BR-10.3`.

**Quy tắc nghiệp vụ:**

- **`BR-03.1` (Hai chiều độc lập):** Chính sách của một trường đối với một Nhóm gồm hai chiều không gộp được:
  - **Mức quyền trên trường** (chọn đúng một): **Xem & Sửa** › **Chỉ xem** › **Ẩn**.
  - **Mức hiển thị giá trị** (khi không Ẩn): **Hiện đầy đủ** › **Che một phần** › **Che hoàn toàn**.

  **Lý do nghiệp vụ:** che là cách trình bày giá trị, không phải mức được làm gì; gộp chung thì không diễn đạt được "được sửa số thẻ nhưng không đọc được giá trị cũ" hay "chỉ xem nhưng đọc đầy đủ".

- **`BR-03.2` (Phân giải xung đột — hạn chế hơn luôn thắng):** Theo [ADR-0001](../docs/adr/0001-group-policy-conflict-resolution.md) và phần Bổ sung 2026-08-23:
  - Người thuộc nhiều Nhóm có cấu hình khác nhau trên cùng một trường nhận, **độc lập trên từng chiều**, giá trị hạn chế nhất (Ẩn › Chỉ xem › Xem & Sửa; Che hoàn toàn › Che một phần › Hiện đầy đủ).
  - "Bắt buộc nhập" cộng gộp: một Nhóm yêu cầu là bắt buộc với người đó.
  - **Quyền thắng ràng buộc nhập:** khi Mức quyền trên trường của người đó là Ẩn hoặc Chỉ xem mà một Nhóm khác đặt Bắt buộc, ràng buộc bắt buộc được miễn trừ cho riêng người đó và bản ghi gắn cờ theo `BR-05.5`.
  - **Vắng mặt cấu hình không phải sự cho phép:** chỉ Nhóm có cấu hình cho chính trường đó tham gia phân giải.
  - **Bố cục mặc định là phương án dự phòng xét theo từng trường:** với mỗi trường, người không thuộc Nhóm nào có cấu hình cho trường đó dùng Bố cục mặc định; có ít nhất một Nhóm cấu hình trường đó thì Bố cục mặc định không tham gia.
  - Muốn nâng quyền trên trường cho một người đang thuộc Nhóm hạn chế thì phải gỡ người đó khỏi Nhóm hạn chế hoặc tổ chức lại Nhóm; gỡ khỏi Nhóm mang hạn chế là nới rộng ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-18.3`).

  **Lý do nghiệp vụ:** khi có mâu thuẫn, phương án an toàn phải thắng phương án thuận tiện; nếu sự im lặng của một Nhóm được tính là cho phép, chỉ cần thêm một người vào một Nhóm trống là vô hiệu hạn chế; nếu Bố cục mặc định xét theo cả bố cục, cấu hình một trường bất kỳ gỡ luôn hạn chế trên mọi trường còn lại.

- **`BR-03.2b` (Người có toàn quyền và phân quyền trường):** Người có toàn quyền không bị phân quyền trường giới hạn, trừ hai trường hợp: Sàn bắt buộc mà SRS phân hệ nêu rõ áp cả lên Người có toàn quyền ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.3`), và che dữ liệu khi giá trị đi qua tác nhân AI ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-40.1`). Thành viên giữ quyền Quản lý phân quyền trường & bố cục **vẫn chịu** phân quyền trường như mọi người dùng. Hồ sơ bán hàng phải nêu: phân quyền trường là công cụ phân tách trách nhiệm giữa các phòng ban, không phải công cụ che dữ liệu khỏi Người có toàn quyền.

  **Lý do nghiệp vụ:** Người có toàn quyền tự sửa được cấu hình nên áp phân quyền trường lên họ chỉ tạo cảm giác an toàn giả; ngược lại, người chỉ được giao quyền cấu hình trường mà thoát phân quyền trường thì quyền đó thành cửa sau để đọc mọi dữ liệu.

- **`BR-03.3` (Xem trước quyền thực tế):** Người có quyền Quản lý phân quyền trường & bố cục chọn một thành viên và xem bảng quyền thực tế trên từng trường sau khi hợp nhất mọi Nhóm, gồm cả hai chiều, nguồn quyết định (Nhóm nào, Bố cục mặc định, Sàn bắt buộc, trường nhạy cảm) và các ràng buộc bắt buộc đã bị miễn trừ. Màn hình chỉ hiển thị cấu hình, không hiển thị dữ liệu bản ghi của người được xem trước.

  **Lý do nghiệp vụ:** quyền hợp nhất từ nhiều Nhóm không suy ra được bằng mắt; không có công cụ này người cấu hình không trả lời được "vì sao nhân viên X không thấy trường Y".

- **`BR-03.4` (Tiến trình chạy thay người dùng):** Theo [ADR-0001](../docs/adr/0001-group-policy-conflict-resolution.md) mục Bổ sung 4, quy trình tự động hóa và tích hợp bên ngoài được **miễn trừ phân quyền trường** để luồng đồng bộ không đứt vì chính sách hiển thị dành cho con người, với các giới hạn bắt buộc:
  - Chỉ chạm tới **bản ghi** trong phạm vi của người khởi chạy hoặc người chịu trách nhiệm tích hợp, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`; miễn trừ chỉ áp ở bước phân quyền trường, không mở bản ghi.
  - Luôn chịu đầy đủ quy tắc kiểm tra dữ liệu (`FEAT-04`).
  - **Cấm chuyển dữ liệu sang nơi bảo vệ thấp hơn:** không được sao giá trị của trường đang Ẩn/Che hoặc trường nhạy cảm sang trường có mức bảo vệ thấp hơn, sang nội dung gửi ra ngoài (email, thông báo, hệ thống bên thứ ba) mà người nhận không có quyền xem đầy đủ, hay sang tác nhân AI dưới dạng đầy đủ. Hệ thống chặn cấu hình như vậy ngay lúc lưu quy trình, nêu trường nguồn và trường đích.
  - **Truy vết:** mỗi lần đọc/ghi vào trường đang Ẩn/Che hoặc nhạy cảm truy được về người tạo/chịu trách nhiệm quy trình hay tích hợp (năng lực nhật ký cấp bản ghi, Mục 1.2).
  - Tính toán nội bộ của hệ thống (công thức, kiểm tra trùng, kiểm tra quy tắc) đọc được mọi trường cần thiết nhưng không bao giờ trả giá trị ra ngoài quyền của người nhận kết quả.

  **Lý do nghiệp vụ:** miễn trừ không giới hạn biến mỗi quy trình tự động thành đường vòng vô hiệu phân quyền trường — dữ liệu sau khi bị sao chép mang chính sách của nơi chứa mới và không thu hồi được (Nguyên tắc OM-2).

- **`BR-03.5` (Trường Ẩn vắng mặt ở mọi kênh):** Trường Ẩn với một người không xuất hiện ở bất kỳ kênh nào người đó dùng: biểu mẫu chi tiết, danh sách, tệp xuất, báo cáo, tìm kiếm toàn cục, xem trước phân đoạn khách hàng, kết quả của tác nhân AI phục vụ người đó.

  **Suy đoán qua tìm kiếm và lọc:** với trường bị Che một phần, Che hoàn toàn, hoặc trường nhạy cảm mà người đó chưa có quyền xem đầy đủ, **không kênh nào** cho người đó tìm, lọc, sắp xếp hay nhóm theo giá trị thật: tìm kiếm toàn cục không so khớp từ khoá với giá trị thật của trường đó (bản ghi không hiện ra chỉ vì khớp trường đó); bộ lọc tự do, bộ lọc của danh sách và của báo cáo không cho chọn trường đó làm điều kiện; báo cáo không nhóm, không đếm theo giá trị và không tính tổng hay trung bình trên trường đó; quy trình tự động người đó tạo không dùng trường đó làm điều kiện rẽ nhánh. Điều kiện "trường có dữ liệu / trống" vẫn dùng được vì không lộ giá trị. Áp cả cho các kênh do người khác cấu hình:
  - **Danh sách hiển thị dùng chung và phân đoạn khách hàng** có điều kiện lọc trên trường mà với người xem là Ẩn, bị che hoặc nhạy cảm chưa được xem đầy đủ: điều kiện đó **không được áp và không được tính** cho người xem — kết quả và số đếm của họ được tính như thể điều kiện ấy không có (vẫn trong mức Xem của họ), và màn hình ghi "một điều kiện không áp dụng với bạn" mà không nêu giá trị của điều kiện.
  - **Số đếm xem trước phân đoạn** theo cùng quy tắc.
  - **Cảnh báo trùng** (nhập tay, nhập tệp, tích hợp) không nêu tên và không so khớp trên trường mà với người thực hiện là Ẩn, bị che hoặc nhạy cảm chưa được xem đầy đủ (`BR-09.1`).
  - **Trường công thức**: với từng người, Mức quyền trên trường và Mức hiển thị giá trị của trường công thức là mức hạn chế nhất giữa cấu hình của chính nó và của **mọi trường nguồn** mà nó tham chiếu (Ẩn hoặc Che), không chỉ khi trường nguồn là nhạy cảm.

  **Lý do nghiệp vụ:** một kênh bỏ sót là đủ để lộ toàn bộ dữ liệu mà các kênh còn lại đang giữ kín; che giá trị trên màn hình là vô nghĩa nếu người dùng thử lần lượt từng giá trị qua ô tìm kiếm hay bộ lọc và suy ra giá trị thật.

- **`BR-03.6` (Bảo vệ giá trị bị che khi lưu):** Người dùng lưu biểu mẫu chứa chuỗi giá trị đã che thì hệ thống giữ nguyên giá trị gốc, không ghi chuỗi che lên dữ liệu thật. Người có Xem & Sửa nhưng chỉ thấy giá trị bị che vẫn nhập được giá trị mới thay hẳn giá trị cũ.

  **Lý do nghiệp vụ:** không có quy tắc này, mỗi lần một nhân viên bị che lưu biểu mẫu là một lần dữ liệu thật bị xoá trắng.

- **`BR-03.7` (Bố cục biểu mẫu theo Nhóm):** Người có quyền Quản lý phân quyền trường & bố cục cấu hình, trên màn hình quản trị, bố cục của từng loại dữ liệu: trường nào có mặt, phần có tiêu đề, thứ tự trường; gán bố cục riêng cho từng Nhóm, còn lại dùng Bố cục mặc định.
  - **Bố cục không bao giờ mở thêm quyền** (Nguyên tắc OM-1): trường có trên bố cục nhưng Ẩn với người dùng thì không hiển thị với họ.
  - Bắt buộc ở cấp bố cục cộng gộp với bắt buộc của phân quyền trường và chịu miễn trừ theo `BR-03.2`, `BR-05.5`.
  - Trường tuỳ biến mới không chèn vào giữa bố cục đang dùng; được thêm vào cuối một phần do người cấu hình chỉ định.
  - Gỡ trường khỏi bố cục chỉ ẩn khỏi biểu mẫu; trường vẫn có thể xuất hiện ở danh sách, báo cáo, tệp xuất nếu phân quyền trường cho phép.

  **Lý do nghiệp vụ:** nếu bố cục quyết định cả quyền, trình sửa bố cục thành đường vòng vô hiệu phân quyền trường; chèn trường mới vào giữa làm đảo trật tự nhập liệu mà nhân viên đã quen.

- **`BR-03.8` (Trần khi cấu hình phân quyền trường):** Người cấu hình không phải Người có toàn quyền:
  - không nới rộng một trường cho một Nhóm vượt quá quyền thực tế của chính mình trên trường đó (không đặt Xem & Sửa khi mình chỉ Chỉ xem; không đặt Hiện đầy đủ khi mình chỉ thấy giá trị bị che) — Nguyên tắc 1 của IAM;
  - không nới rộng phân quyền trường của Nhóm mà chính mình thuộc, hay của Bố cục mặc định trên trường đang áp lên chính mình — Nguyên tắc 2 của IAM;
  - không đặt vượt Sàn bắt buộc của phân hệ ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.3`);
  - bỏ đánh dấu trường nhạy cảm hoặc đổi sang mẫu che lỏng hơn là **nới rộng** cho mọi người chưa có quyền xem đầy đủ: chỉ được khi chính người thực hiện đang có quyền xem đầy đủ trường đó và không ai trong Nhóm mình thuộc được nới thêm nhờ thao tác đó; ghi nhật ký như nới rộng (`BR-10.3`);
  - luôn thu hẹp được, kể cả thu hẹp chính mình.

  **Lý do nghiệp vụ:** quyền cấu hình phân quyền trường mà không có trần là quyền tự mở mọi trường cho mình và người quen — đúng kiểu leo thang mà IAM chặn ở mọi nơi khác.

- **`BR-03.9` (Vị trí trong thứ tự hợp nhất quyền):** Phân quyền trường và che dữ liệu là bước 4 của thứ tự hợp nhất ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`): chỉ quyết định thấy gì bên trong bản ghi mà người đó đã được vào theo ô, nguồn nới phạm vi và nguồn chặn. Phân quyền trường không bao giờ cho vào một bản ghi; lượt cấp trên bản ghi, chia sẻ, công khai đọc hay chính sách Cho phép không nới lỏng phân quyền trường hay mẫu che.

  **Lý do nghiệp vụ:** một thứ tự duy nhất làm mọi quyết định truy cập giải thích được; một lượt chia sẻ bản ghi mà tự mở các trường bị che là rò rỉ không ai chủ ý.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-03.1.1` | Nhóm CSKH: "Số thẻ" = Xem & Sửa, Che hoàn toàn | Thành viên mở bản ghi | Sửa được ô số thẻ, không đọc được giá trị cũ |
| `AC-03.1.2` | Nhóm Kế toán: "Doanh thu dự kiến" = Chỉ xem, Hiện đầy đủ | Thành viên mở bản ghi | Đọc đầy đủ, không sửa được |
| `AC-03.2.1` | A thuộc Nhóm Sales (Chỉ xem, Hiện đầy đủ) và Nhóm Đối tác (Xem & Sửa, Che một phần) trên "Hạn mức" | A mở bản ghi | Chỉ xem, Che một phần |
| `AC-03.2.2` | "Hạn mức tín dụng" Ẩn với Nhóm Sales, Bắt buộc với Nhóm Kế toán; B thuộc cả hai | B lưu bản ghi | Lưu thành công; bản ghi gắn cờ thiếu dữ liệu; B không thấy trường |
| `AC-03.2.3` | C thuộc Nhóm X (Ẩn "Lương") và Nhóm Y (không cấu hình "Lương") | C mở bản ghi | "Lương" vẫn Ẩn |
| `AC-03.2.4` | Bố cục mặc định Ẩn "Lương" và "Thưởng"; D thuộc Nhóm Z chỉ cấu hình "Lương" = Chỉ xem | D mở bản ghi | Thấy "Lương" ở Chỉ xem; "Thưởng" vẫn Ẩn |
| `AC-03.2b.1` | Quản trị viên; "Lương" Ẩn với mọi Nhóm | Quản trị viên mở bản ghi | Thấy và sửa được "Lương" |
| `AC-03.2b.2` | Thành viên E giữ quyền Quản lý phân quyền trường & bố cục, thuộc Nhóm Ẩn "Lương" | E mở bản ghi | Không thấy "Lương" |
| `AC-03.3.1` | F thuộc Nhóm Sales và Support | Mở Xem trước quyền thực tế cho F | Bảng hiển thị từng trường với hai chiều, nguồn quyết định và ràng buộc bắt buộc bị miễn trừ; không hiển thị dữ liệu bản ghi nào |
| `AC-03.4.1` | Quy trình tự động của Quản lý G ghi "Điểm rủi ro" (Ẩn với Nhóm Sales) | Quy trình chạy trên khách hàng thuộc phạm vi của G | Ghi thành công; nhật ký cấp bản ghi ghi G là người chịu trách nhiệm |
| `AC-03.4.2` | Cấu hình quy trình sao "Số căn cước" (nhạy cảm) vào "Ghi chú" (ai cũng xem) | Lưu quy trình | Bị chặn, nêu trường nguồn và trường đích |
| `AC-03.4.3` | Quy trình của H, H có (Khách hàng, Sửa) = Đơn vị của mình | Quy trình chạy trên khách hàng đơn vị khác | Không chạm tới bản ghi đó |
| `AC-03.4.4` | Quy trình ghi giá trị sai định dạng vào trường có quy tắc kiểm tra | Quy trình chạy | Bản ghi bị từ chối, lỗi ghi vào nhật ký quy trình |
| `AC-03.5.1` | "Lương cơ bản" Ẩn với Nhóm Sales | Thành viên Sales tìm kiếm toàn cục bằng giá trị lương, xuất danh sách, mở báo cáo | Không kênh nào chứa trường hay giá trị đó; tìm kiếm không trả bản ghi theo từ khoá lương |
| `AC-03.5.2` | "Số căn cước" là nhạy cảm, người xem chưa có quyền xem đầy đủ | Gõ đúng số căn cước của một khách hàng vào tìm kiếm toàn cục | Không có kết quả nào hiện ra nhờ khớp trường đó |
| `AC-03.5.3` | "Thu nhập" Che hoàn toàn với Nhóm Sales | Thành viên Sales mở bộ lọc tự do và trình tạo báo cáo | Không chọn được "Thu nhập" làm điều kiện lọc, cột nhóm hay giá trị tính tổng; chọn được điều kiện "Thu nhập có dữ liệu" |
| `AC-03.5.4` | Tình huống `AC-03.5.3` | Thành viên Sales tạo quy trình tự động rẽ nhánh theo "Thu nhập > 50 triệu" | Bị chặn khi lưu, nêu trường bị che |
| `AC-03.5.5` | Phân đoạn "Khách hàng thu nhập > 50 triệu" do Marketing tạo; "Thu nhập" Che hoàn toàn với Nhóm Sales | Thành viên Sales mở xem trước phân đoạn | Số đếm và danh sách tính như không có điều kiện thu nhập, trong mức Xem của họ; màn hình ghi "một điều kiện không áp dụng với bạn", không nêu ngưỡng 50 triệu |
| `AC-03.5.6` | "Lương" (không nhạy cảm) Ẩn với Nhóm Sales; trường công thức "Tổng thu nhập" = Lương + Thưởng, không cấu hình riêng | Thành viên Sales mở bản ghi, danh sách, tệp xuất | "Tổng thu nhập" Ẩn ở mọi kênh |
| `AC-03.6.1` | Thành viên thấy "Số thẻ" dạng `****5678`, sửa trường khác rồi lưu | Lưu | Số thẻ gốc giữ nguyên |
| `AC-03.7.1` | Trường "Tình trạng điều tra" có trên bố cục của Nhóm Sales nhưng Ẩn với Sales | Thành viên Sales mở biểu mẫu | Không thấy trường |
| `AC-03.7.2` | Bố cục đang dùng có 3 phần | Tạo trường tuỳ biến mới, chỉ định phần "Thông tin thêm" | Trường nằm cuối phần "Thông tin thêm"; thứ tự các trường khác không đổi |
| `AC-03.7.3` | Người có quyền Quản lý phân quyền trường & bố cục | Mở màn hình quản trị bố cục | Kéo thả được trường giữa các phần, đặt bắt buộc ở cấp bố cục, gán bố cục cho Nhóm |
| `AC-03.8.1` | I giữ quyền Quản lý phân quyền trường & bố cục, có "Doanh thu" = Chỉ xem | Đặt "Doanh thu" = Xem & Sửa cho Nhóm Sales | Lựa chọn bị vô hiệu, nêu vượt quyền thực tế của I |
| `AC-03.8.2` | I thuộc Nhóm Sales; "Biên lợi nhuận" Ẩn với Sales | I đặt "Biên lợi nhuận" = Chỉ xem cho Sales | Từ chối, nêu lý do không tự nới rộng |
| `AC-03.8.3` | Tình huống `AC-03.8.2` | I đặt "Ghi chú nội bộ" = Ẩn cho Sales | Thành công |
| `AC-03.8.4` | I giữ quyền Quản lý phân quyền trường & bố cục nhưng chỉ thấy "Số căn cước" bị che | Bỏ đánh dấu nhạy cảm của trường | Từ chối, nêu vượt quyền thực tế của I |
| `AC-03.9.1` | Khách hàng K được chia sẻ cho J qua lượt cấp trên bản ghi; "Thu nhập" Che hoàn toàn với Nhóm của J | J mở K | Mở được K; "Thu nhập" vẫn che hoàn toàn |

---

### FEAT-04 — Quy tắc kiểm tra dữ liệu

**Mô tả nghiệp vụ:** Thiết lập ràng buộc để dữ liệu đúng và chuẩn hóa khi vào hệ thống.

**Vai trò sử dụng chính:** Người có quyền Quản lý cấu hình đối tượng.

**Điều kiện tiên quyết:** Trường cần kiểm tra đang hoạt động.

**Luồng chính:**

1. Chọn trường → chọn kiểu kiểm tra → nhập khuôn dạng hoặc khoảng giá trị và thông báo lỗi.
2. Hệ thống kiểm tra tính hợp lệ của chính quy tắc trước khi lưu (`BR-04.2`).
3. Lưu → áp cho mọi kênh từ lần lưu bản ghi kế tiếp, theo `BR-04.3`.

**Quy tắc nghiệp vụ:**

- **`BR-04.1` (Kiểu kiểm tra chuẩn):** Không được để trống; Đúng định dạng (email, số điện thoại quốc tế, mã số thuế, địa chỉ web hoặc khuôn dạng do người cấu hình định nghĩa); Nằm trong khoảng (tối thiểu và/hoặc tối đa).

  **Lý do nghiệp vụ:** ba kiểu này phủ phần lớn nhu cầu chuẩn hóa mà người cấu hình không chuyên vẫn tự đặt được; dữ liệu sai định dạng làm hỏng kiểm tra trùng và liên lạc với khách.

- **`BR-04.2` (Cấu hình sai bị chặn khi lưu; không đánh giá được thì từ chối bản ghi):**
  - Khuôn dạng và khoảng giá trị được kiểm tra ngay khi lưu cấu hình, chỉ rõ chỗ sai; quy tắc hệ thống không đánh giá được (viết sai, hoặc phức tạp tới mức rủi ro hiệu năng) bị từ chối lúc lưu.
  - Nếu tới lúc người dùng lưu bản ghi mà quy tắc vẫn không đánh giá được, hệ thống **từ chối bản ghi**, không âm thầm bỏ qua quy tắc.
  - **Đường phục hồi:** việc từ chối chỉ áp cho lệnh ghi bản ghi nghiệp vụ, không chặn việc đọc và sửa chính cấu hình quy tắc; thông báo cho người dùng phân biệt "dữ liệu chưa đúng quy tắc" với "hệ thống hiện không kiểm tra được quy tắc, liên hệ người quản trị"; số lần xảy ra là dữ liệu đo bắt buộc (`NFR-09`).

  **Lý do nghiệp vụ:** bỏ qua quy tắc tạo dữ liệu sai chảy vào báo cáo mà không ai biết — thiệt hại lớn hơn chặn một lần nhập; nhưng nếu chính màn hình cấu hình cũng bị chặn, doanh nghiệp không tự sửa được (Nguyên tắc OM-3).

- **`BR-04.3` (Chỉ kiểm tra khi giá trị của trường thay đổi):** Quy tắc chỉ được đánh giá khi bản ghi được tạo mới hoặc chính trường đó bị đổi trong lần lưu; sửa trường khác trên bản ghi cũ không bị chặn bởi giá trị chưa chuẩn của trường không đổi. Đánh đổi có chủ đích: dữ liệu không đạt chuẩn có thể tồn tại lâu nếu không ai sửa; nhu cầu báo cáo nợ dữ liệu ghi tại Mục 7, điểm 3.

  **Lý do nghiệp vụ:** một quy tắc ban hành hôm nay không được làm đình trệ toàn bộ công việc trên dữ liệu đã có từ trước.

- **`BR-04.4` (Định dạng độc lập với bắt buộc):** Quy tắc "Đúng định dạng" bỏ qua trường đang trống, trừ khi trường đồng thời bắt buộc.

  **Lý do nghiệp vụ:** gộp hai ý nghĩa làm mọi trường có định dạng thành bắt buộc ngầm, chặn lưu những bản ghi chưa có thông tin đó.

- **`BR-04.5` (Thực thi đồng nhất đa kênh):** Quy tắc áp bình đẳng cho nhập trên máy tính, trên điện thoại, nhập tệp, tác vụ tự động và tích hợp bên ngoài; không kênh nào có cửa sau.

  **Lý do nghiệp vụ:** một kênh không kiểm tra là đủ để dữ liệu bẩn quay lại mọi báo cáo.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-04.1.1` | Trường "Số lượng nhân sự", quy tắc Nằm trong khoảng 1 – 100.000 | Nhập 0 và lưu | Bị từ chối tại trường, hiện thông báo đã cấu hình |
| `AC-04.2.1` | Màn hình cấu hình quy tắc | Lưu khuôn dạng viết sai | Bị từ chối, chỉ rõ vị trí sai |
| `AC-04.2.2` | Quy tắc của loại Khách hàng hiện không đánh giá được | Nhân viên lưu một khách hàng | Bị từ chối với thông báo "hệ thống hiện không kiểm tra được quy tắc, liên hệ người quản trị", khác với thông báo dữ liệu sai |
| `AC-04.2.3` | Tình huống `AC-04.2.2` | Người có quyền Quản lý cấu hình đối tượng mở và gỡ quy tắc lỗi | Mở và lưu được; nhân viên lưu khách hàng thành công sau đó |
| `AC-04.3.1` | Quy tắc định dạng số điện thoại mới; khách hàng cũ có số sai định dạng | Nhân viên chỉ đổi người phụ trách, lưu | Lưu thành công |
| `AC-04.3.2` | Tình huống `AC-04.3.1` | Nhân viên sửa chính số điện thoại thành một số sai khác | Bị từ chối |
| `AC-04.4.1` | "Website" có quy tắc định dạng, không bắt buộc | Để trống, lưu | Lưu thành công |
| `AC-04.5.1` | Quy tắc định dạng số điện thoại | Nhập tệp có 1 dòng sai | Dòng đó bị từ chối kèm thông báo đã cấu hình; các dòng khác nhập bình thường |
| `AC-04.5.2` | Tình huống `AC-04.5.1` | Tích hợp bên ngoài ghi số sai định dạng | Bị từ chối, lỗi trả về cho tích hợp |

---

### FEAT-05 — Giai đoạn vòng đời & ma trận chuyển đổi

**Mô tả nghiệp vụ:** Định nghĩa giai đoạn vòng đời của Khách hàng và chuẩn hóa việc chuyển đổi khách hàng tiềm năng thành Cơ hội. Nghiệp vụ vận hành của vòng đời và chuyển đổi do [`contacts-srs.md`](./contacts-srs.md) quy định; tính năng này quy định phần cấu hình.

**Vai trò sử dụng chính:** Người có quyền Quản lý cấu hình đối tượng (cấu hình); người dùng nghiệp vụ (chịu ràng buộc).

**Điều kiện tiên quyết:** Có ít nhất một Quy trình bán hàng đang hoạt động khi bật tự động sinh Cơ hội.

**Luồng chính:**

1. Định nghĩa chuỗi giai đoạn, màu, thứ tự, trường bắt buộc theo giai đoạn.
2. Đánh dấu giai đoạn "Đã chuyển đổi" và cấu hình ma trận chuyển đổi.
3. Lưu → hệ thống cảnh báo xung đột Ẩn – Bắt buộc nếu có (`BR-05.4`).

**Quy tắc nghiệp vụ:**

- **`BR-05.1` (Chuỗi giai đoạn, cho phép đi ngược):** Thứ tự giai đoạn là tiến trình kỳ vọng, không phải đường một chiều: Khách hàng được quay về giai đoạn trước và rời "Rời bỏ" để vào lại vòng nuôi dưỡng; trường bắt buộc của giai đoạn đích vẫn áp; dữ liệu lần theo đuổi trước không bị xoá. Quyền thực hiện bước lùi theo [`contacts-srs.md`](./contacts-srs.md).

  **Lý do nghiệp vụ:** tái tiếp cận khách cũ là hoạt động cốt lõi của B2B; chặn đi ngược buộc nhân viên tạo bản ghi trùng.

- **`BR-05.2` (Trường bắt buộc theo giai đoạn):** Cấu hình được danh sách trường phải có dữ liệu khi Khách hàng chuyển tới một giai đoạn.

  **Lý do nghiệp vụ:** mỗi giai đoạn chỉ có ý nghĩa khi dữ liệu tối thiểu của nó tồn tại; không có ràng buộc thì báo cáo phễu đếm những khách chưa đủ điều kiện.

- **`BR-05.3` (Ma trận chuyển đổi):** Khi giai đoạn "Đã chuyển đổi" bật tự động sinh Cơ hội, ma trận gồm: (1) mẫu tên Cơ hội; (2) Quy trình bán hàng và Giai đoạn khởi đầu; (3) Người phụ trách Cơ hội — mặc định kế thừa Người phụ trách Khách hàng, hoặc người chỉ định, hoặc tường minh "Áp dụng phân công tự động"; khi phân công tự động đang bật, cấu hình của ma trận thắng. Cả ba lựa chọn chịu đúng điều kiện của `BR-09.3`: người chỉ định phải nằm trong ô (Cơ hội, Gán) của người cấu hình ma trận; chọn "kế thừa" hoặc "phân công tự động" giao Cơ hội cho những người mà lúc cấu hình chưa biết trước, nên chỉ người có ô (Cơ hội, Gán) = Toàn workspace hoặc Người có toàn quyền đặt được; người nhận thực tế (người chỉ định, Người phụ trách Khách hàng được kế thừa) phải Đang hoạt động ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-42.1`) và có ô (Cơ hội, Xem) khác Không có, nếu không Cơ hội vào hàng đợi chưa phân công của **đơn vị tiếp nhận của ma trận** — mỗi ma trận khai báo một đơn vị tiếp nhận theo `BR-09.5` (a), loại nguồn "Ma trận chuyển đổi"; đổi lựa chọn người phụ trách hay đơn vị tiếp nhận của ma trận ghi và phân loại như thay đổi quy tắc phân công (`BR-10.3`); Cơ hội thuộc đơn vị theo Người phụ trách ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4); (4) liên kết Công ty: có thì liên kết, chưa có thì tuỳ chọn tạo Công ty hoặc giữ độc lập; (5) chống trùng: Công ty đã có Cơ hội Đang mở trong cùng Quy trình đích thì không tự tạo, mà cảnh báo Người phụ trách Công ty và cho chọn gắn Khách hàng vào Cơ hội đó (kèm Vai trò liên hệ) hoặc tạo mới có chủ đích (lưu vết quyết định). Danh mục Vai trò liên hệ là danh mục dùng chung của workspace: chưa cấu hình thì không chặn; đã cấu hình thì vai trò ngoài danh mục bị từ chối kèm danh sách hợp lệ.

  **Lý do nghiệp vụ:** người đã nuôi dưỡng khách tới điểm chuyển đổi cần giữ quyền theo đuổi thương vụ; hai Cơ hội trùng trên một Công ty làm sai dự báo và gây tranh chấp hoa hồng; chặn Vai trò liên hệ khi danh mục còn trống làm mọi workspace mới không gắn được vai trò nào.

- **`BR-05.4` (Cảnh báo xung đột Ẩn – Bắt buộc khi cấu hình):** Một trường Ẩn với một Nhóm nhưng bắt buộc ở một giai đoạn thì màn hình cấu hình cảnh báo, kèm danh sách Nhóm bị ảnh hưởng.

  **Lý do nghiệp vụ:** người cấu hình cần biết ngay rằng một phần nhân viên sẽ lưu bản ghi thiếu trường đó và tạo cờ tồn đọng.

- **`BR-05.5` (Miễn trừ bắt buộc vì giới hạn quyền & cờ thiếu dữ liệu):** Ràng buộc bắt buộc (giai đoạn vòng đời, điều kiện qua giai đoạn, bố cục, phân quyền trường) không ép người dùng nhập trường mà với họ là Ẩn hoặc Chỉ xem; hệ thống miễn trừ cho riêng người đó, cho lưu và gắn cờ **"Thiếu dữ liệu bắt buộc do giới hạn quyền"**. Cờ dùng chung cho mọi loại dữ liệu. Quản trị cờ: (1) cảnh báo trên bản ghi nêu thiếu trường nào của giai đoạn nào, chỉ với người thấy được trường đó; (2) có điều kiện lọc hệ thống theo cờ để dùng trong danh sách hiển thị dùng chung, không bản ghi gắn cờ nào nằm ngoài bộ lọc; (3) khi gắn cờ, hệ thống thông báo tới thành viên các Nhóm có quyền nhập trường còn thiếu **và có mức Xem bao phủ bản ghi**; (4) cờ tự gỡ ngay khi trường có giá trị hợp lệ.

  **Lý do nghiệp vụ:** không miễn trừ thì nhân viên kẹt vĩnh viễn; miễn trừ mà không có người nhận trách nhiệm thì dữ liệu thiếu chảy đi không dấu vết; thông báo tới người không được xem bản ghi là rò rỉ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-05.1.1` | Khách hàng ở "Rời bỏ" | Chuyển về "Đang chăm sóc" | Thành công; lịch sử lần trước còn nguyên; trường bắt buộc của "Đang chăm sóc" được áp |
| `AC-05.2.1` | "Đủ điều kiện" bắt buộc "Ngân sách dự kiến" | Chuyển khách chưa có ngân sách sang giai đoạn này | Bị chặn, chỉ rõ trường cần bổ sung |
| `AC-05.3.1` | Ma trận: mẫu tên, Quy trình "B2B", Giai đoạn "Tiếp cận", kế thừa người phụ trách | Khách hàng đạt "Đã chuyển đổi" | Cơ hội tạo đúng mẫu tên, đúng Quy trình, Giai đoạn, người phụ trách; thuộc đơn vị của người phụ trách |
| `AC-05.3.2` | Ma trận chọn "kế thừa"; Người phụ trách Khách hàng đang Tạm ngưng | Khách hàng đạt "Đã chuyển đổi" | Cơ hội vào hàng đợi chưa phân công của đơn vị tiếp nhận của ma trận; người phụ trách đơn vị nhận cảnh báo |
| `AC-05.3.6` | Ma trận chọn "kế thừa"; Người phụ trách Khách hàng có ô (Cơ hội, Xem) = Không có | Khách hàng đạt "Đã chuyển đổi" | Cơ hội không giao cho người đó, vào hàng đợi của đơn vị tiếp nhận của ma trận |
| `AC-05.3.7` | Người cấu hình có ô (Cơ hội, Gán) = Đơn vị của mình | Chọn người chỉ định thuộc đơn vị khác; rồi thử chọn "kế thừa" | Cả hai bị từ chối, nêu vượt ô Gán |
| `AC-05.3.8` | Ma trận mới chưa có đơn vị tiếp nhận, không có mặc định | Bật tự động sinh Cơ hội | Bị vô hiệu kèm giải thích cần Người có toàn quyền đặt đơn vị tiếp nhận |
| `AC-05.3.3` | Hai Khách hàng cùng Công ty chuyển đổi cách nhau 1 phút | Lần chuyển thứ hai | Không tạo Cơ hội thứ hai; Người phụ trách Công ty nhận cảnh báo kèm lựa chọn gắn vào Cơ hội đang mở |
| `AC-05.3.4` | Danh mục Vai trò liên hệ còn trống | Gắn Khách hàng vào Cơ hội với vai trò "Người ảnh hưởng" | Thành công |
| `AC-05.3.5` | Danh mục đã cấu hình, không có "Cố vấn" | Gắn với vai trò "Cố vấn" | Bị từ chối kèm danh sách vai trò hợp lệ |
| `AC-05.4.1` | "Hạn mức tín dụng" Ẩn với Nhóm Sales | Đặt bắt buộc ở giai đoạn "Khách hàng chính thức" | Màn hình cảnh báo xung đột, liệt kê Nhóm Sales |
| `AC-05.5.1` | Tình huống `AC-05.4.1`; nhân viên Sales chuyển khách sang giai đoạn đó | Lưu | Lưu thành công; bản ghi gắn cờ; Sales không thấy cảnh báo về trường đó |
| `AC-05.5.2` | Tình huống `AC-05.5.1` | Thành viên Nhóm Kế toán có mức Xem bao phủ bản ghi | Nhận thông báo; lọc theo cờ thấy bản ghi; nhập trường thì cờ tự gỡ |
| `AC-05.5.3` | Thành viên Nhóm Kế toán không có mức Xem bao phủ bản ghi | Bản ghi bị gắn cờ | Không nhận thông báo |

---

### FEAT-06 — Trạng thái & nguồn theo loại dữ liệu

**Mô tả nghiệp vụ:** Quản lý danh mục trạng thái vận hành, Nhóm công việc và nguồn phát sinh dữ liệu cho từng loại dữ liệu.

**Vai trò sử dụng chính:** Người có quyền Quản lý cấu hình đối tượng.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Chọn loại dữ liệu → thêm/sửa/sắp xếp trạng thái hoặc nguồn.
2. Đặt trạng thái mặc định, cờ trạng thái đóng.
3. Lưu → danh sách chọn của loại dữ liệu cập nhật.

**Quy tắc nghiệp vụ:**

- **`BR-06.1` (Trạng thái phẳng):** Khách hàng, Công ty, Vé hỗ trợ, Công việc dùng danh sách trạng thái phẳng (tên, màu, thứ tự, cờ mặc định khi tạo mới, cờ trạng thái đóng); đúng một trạng thái mặc định mỗi loại. Cờ trạng thái đóng không kéo theo dữ liệu bắt buộc khi đóng; hệ quả nghiệp vụ khác do SRS phân hệ quy định — điều kiện đóng cấu hình được chỉ có ở Cơ hội (`BR-09.2`); ràng buộc riêng của từng loại (ví dụ nhánh Hoàn thành/Huỷ bỏ của Công việc) do SRS phân hệ quy định. Trạng thái đang được gán cho bản ghi chỉ vô hiệu hóa được, không xoá; trạng thái mặc định không vô hiệu hóa được trước khi chỉ định mặc định thay thế.

  **Lý do nghiệp vụ:** xoá trạng thái đang dùng làm sai báo cáo lịch sử; thiếu trạng thái mặc định thì không tạo được bản ghi mới (Nguyên tắc OM-3).

- **`BR-06.2` (Trạng thái vĩ mô của Cơ hội):** Cơ hội chỉ có ba trạng thái vĩ mô cố định: Đang mở, Thắng, Thua; giai đoạn chi tiết, tỷ lệ thành công và thời gian lưu thuộc từng Quy trình bán hàng (`FEAT-07`).

  **Lý do nghiệp vụ:** dự báo và báo cáo thắng/thua so sánh được giữa các Quy trình chỉ khi trạng thái vĩ mô là chung.

- **`BR-06.3` (Nguồn phát sinh):** Danh mục nguồn quản lý riêng cho từng loại dữ liệu; nguồn đang gán cho bản ghi chỉ vô hiệu hóa được, không xoá.

  **Lý do nghiệp vụ:** xoá nguồn làm báo cáo hiệu quả kênh của các kỳ trước mất gốc so sánh.

- **`BR-06.4` (Nhóm công việc):** Danh mục Nhóm công việc của loại Công việc (Gọi điện, Họp, Email…) được cấu hình tại đây; ý nghĩa nghiệp vụ và ràng buộc toàn vẹn của danh mục theo [`tasks-srs.md`](./tasks-srs.md) `FEAT-37`. Nhóm công việc đang được công việc sử dụng chỉ vô hiệu hóa được, không xoá; công việc đang mang nhóm đã vô hiệu hóa giữ nguyên nhóm đó.

  **Lý do nghiệp vụ:** Nhóm công việc quyết định loại bản ghi tương tác sinh ra khi hoàn thành công việc; xoá một nhóm đang dùng làm mất ý nghĩa của công việc và lịch sử tương tác đã có.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-06.1.1` | Vé hỗ trợ có trạng thái mặc định "Mới" | Đặt "Tiếp nhận" làm mặc định | "Tiếp nhận" là mặc định duy nhất; "Mới" mất cờ |
| `AC-06.1.2` | "Mới" đang là mặc định | Vô hiệu hóa "Mới" | Bị chặn, yêu cầu chỉ định mặc định thay thế trước |
| `AC-06.1.3` | 40 vé mang trạng thái "Chờ đối tác" | Vô hiệu hóa trạng thái | 40 vé giữ nguyên ở chế độ chỉ đọc; báo cáo lịch sử không đổi số; biểu mẫu mới không còn lựa chọn |
| `AC-06.1.4` | Trạng thái "Đã đóng" của Vé hỗ trợ có cờ đóng | Đóng một vé | Không bị đòi dữ liệu bắt buộc nào thêm |
| `AC-06.2.1` | Mở cấu hình trạng thái của Cơ hội | Thêm trạng thái vĩ mô thứ tư | Không có lựa chọn; chỉ dẫn sang cấu hình Giai đoạn của Quy trình |
| `AC-06.3.1` | Nguồn "Hội chợ 2025" gán cho 300 khách hàng | Xoá nguồn | Chỉ có lựa chọn vô hiệu hóa; báo cáo kênh các kỳ trước giữ nguyên |
| `AC-06.4.1` | Nhóm công việc "Gọi điện" đang được 300 công việc sử dụng | Xoá nhóm | Chỉ có lựa chọn vô hiệu hóa; 300 công việc giữ nguyên nhóm |

---

### FEAT-07 — Quy trình bán hàng & giai đoạn Cơ hội

**Mô tả nghiệp vụ:** Định nghĩa nhiều Quy trình bán hàng độc lập, mỗi Quy trình có tập Giai đoạn riêng. Nghiệp vụ vận hành Cơ hội do [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) quy định; tính năng này quy định phần cấu hình và các ràng buộc toàn vẹn.

**Vai trò sử dụng chính:** Người có quyền Quản lý cấu hình đối tượng (cấu hình); người dùng nghiệp vụ (chịu ràng buộc).

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Tạo Quy trình (tên, mô tả, màu, cờ mặc định, bắt buộc tuần tự theo `CFG-07-01`).
2. Thêm Giai đoạn: tên, trạng thái vĩ mô, tỷ lệ thành công, thời gian lưu kỳ vọng, điều kiện qua giai đoạn.
3. Lưu; thay đổi trên Giai đoạn đang có Cơ hội chịu `BR-07.6`.

**Quy tắc nghiệp vụ:**

- **`BR-07.1` (Nhiều Quy trình):** Tạo được nhiều Quy trình, mỗi Quy trình có tên, mô tả, màu, cờ mặc định, cờ đã lưu trữ.

  **Lý do nghiệp vụ:** bán dự án, bán gói nhỏ và gia hạn có nhịp và bước khác nhau; ép chung một quy trình làm sai tỷ lệ chuyển đổi của cả ba.

- **`BR-07.2` (Giai đoạn thuộc Quy trình):** Mỗi Quy trình sở hữu tập Giai đoạn độc lập; mỗi Giai đoạn có tên, trạng thái vĩ mô (Đang mở/Thắng/Thua), tỷ lệ thành công kỳ vọng 0 – 100% (dùng tính doanh số dự báo trọng số = giá trị × tỷ lệ), thời gian lưu kỳ vọng (quá thì cảnh báo Cơ hội trễ giai đoạn).

  **Lý do nghiệp vụ:** cùng tên "Báo giá" nhưng xác suất thắng ở hai Quy trình khác nhau; dùng chung giai đoạn làm dự báo sai.

- **`BR-07.3` (Điều kiện qua giai đoạn):** Cấu hình được trường phải có giá trị khi Cơ hội vào một Giai đoạn; chịu miễn trừ theo `BR-05.5`.

  **Lý do nghiệp vụ:** dự báo chỉ tin được khi Cơ hội ở "Báo giá" thật sự có giá trị và ngày dự kiến chốt.

- **`BR-07.4` (Bắt buộc tuần tự):** Khi `CFG-07-01` bật cho một Quy trình, Cơ hội chỉ tiến từng bước liền kề; chuyển sang Thắng/Thua hoặc lùi luôn được phép. Chuyển thẳng sang Thắng vẫn phải đủ điều kiện qua giai đoạn cộng dồn của các Giai đoạn bị bỏ qua (`BR-09.2`), và toàn bộ dữ liệu còn thiếu được yêu cầu trong **một lượt nhập** duy nhất.

  **Lý do nghiệp vụ:** ngoại lệ chốt nhanh không được thành cách né nghĩa vụ nhập dữ liệu; bắt quay lại từng giai đoạn để điền là trừng phạt người chốt nhanh.

- **`BR-07.5` (Chuyển Cơ hội giữa các Quy trình):** Người dùng bắt buộc chọn Giai đoạn đích trong Quy trình mới, không gán ngầm; Cơ hội Đang mở chỉ vào Giai đoạn Đang mở; dữ liệu điều kiện qua giai đoạn cũ được giữ; lượt chuyển ghi vào lịch sử Cơ hội (Quy trình và Giai đoạn cũ → mới, người thực hiện). Thao tác chịu ô (Cơ hội, Sửa) của người thực hiện.

  **Lý do nghiệp vụ:** gán ngầm vào Giai đoạn đầu làm doanh số dự báo nhảy mà không ai giải thích được.

- **`BR-07.6` (Vòng đời Quy trình & Giai đoạn khi đang có Cơ hội):** Xoá/vô hiệu hóa Giai đoạn đang có Cơ hội Đang mở bị chặn cho tới khi chỉ định Giai đoạn tiếp nhận; lưu trữ Quy trình đang có Cơ hội Đang mở phải chọn chuyển hết sang Quy trình khác (theo `BR-07.5`) hoặc để chúng ở chế độ chỉ đọc, không chuyển giai đoạn tiếp — không có trạng thái thứ ba; Quy trình đã lưu trữ không xuất hiện khi tạo Cơ hội hay cấu hình ma trận chuyển đổi nhưng lịch sử vẫn tham chiếu đúng tên; Quy trình mặc định không lưu trữ được trước khi chỉ định mặc định khác.

  **Lý do nghiệp vụ:** Cơ hội treo ở giai đoạn không còn tồn tại là dữ liệu mồ côi; báo cáo cũ mất tên Quy trình là mất khả năng đối chiếu (Nguyên tắc OM-3).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-07.1.1` | Workspace mới | Tạo Quy trình "B2B" (5 Giai đoạn) và "Gia hạn" (2 Giai đoạn) | Hai Quy trình độc lập, mỗi cái đúng tập Giai đoạn của mình |
| `AC-07.2.1` | Cơ hội 100 triệu ở Giai đoạn 60% | Xem dự báo trọng số | 60 triệu |
| `AC-07.3.1` | "Báo giá" bắt buộc "Giá trị" và "Ngày dự kiến chốt" | Chuyển Cơ hội thiếu "Giá trị" vào "Báo giá" | Bị chặn, chỉ rõ trường thiếu |
| `AC-07.4.1` | `CFG-07-01` bật cho "B2B" | Kéo Cơ hội từ Giai đoạn 1 sang Giai đoạn 3 | Bị chặn |
| `AC-07.4.2` | Tình huống `AC-07.4.1` | Chuyển thẳng Giai đoạn 1 sang Thắng | Một lượt nhập duy nhất yêu cầu điều kiện đóng và điều kiện qua giai đoạn của Giai đoạn 2 – 4 |
| `AC-07.4.3` | Tình huống `AC-07.4.1` | Chuyển thẳng sang Thua | Chỉ yêu cầu lý do thua và ghi chú |
| `AC-07.5.1` | Cơ hội Đang mở ở "Đàm phán" (80%) của "B2B" | Chuyển sang "Gia hạn" | Buộc chọn Giai đoạn đích thuộc nhóm Đang mở; dự báo theo tỷ lệ mới; lịch sử ghi người chuyển |
| `AC-07.6.1` | Giai đoạn "Demo" có 12 Cơ hội Đang mở | Xoá Giai đoạn | Bị chặn cho tới khi chỉ định Giai đoạn tiếp nhận |
| `AC-07.6.2` | "B2B" là Quy trình mặc định | Lưu trữ | Bị chặn, yêu cầu chỉ định mặc định khác |
| `AC-07.6.3` | Lưu trữ "Gia hạn", chọn để Cơ hội chỉ đọc | Mở một Cơ hội của "Gia hạn" | Xem được, không chuyển giai đoạn được; báo cáo cũ vẫn ghi đúng tên Quy trình |

---

### FEAT-08 — Danh sách hiển thị dùng chung

**Mô tả nghiệp vụ:** Thiết kế sẵn chế độ xem danh sách chuẩn (cột, bộ lọc, sắp xếp) và gán cho Nhóm.

**Vai trò sử dụng chính:** Người có quyền Quản lý danh sách hiển thị dùng chung (tạo, gán); người dùng nghiệp vụ (sử dụng).

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Tạo danh sách: chọn cột, thứ tự, độ rộng; điều kiện lọc mặc định; thứ tự sắp xếp mặc định.
2. Gán cho một hay nhiều Nhóm, loại trừ người cụ thể nếu cần; xếp ưu tiên giữa các danh sách.
3. Lưu → người dùng mở màn hình danh sách thấy danh sách mở sẵn theo `BR-08.1`.

**Quy tắc nghiệp vụ:**

- **`BR-08.1` (Gán theo Nhóm & danh sách mở sẵn):** Danh sách được gán cho Nhóm, loại trừ được người cụ thể. Người không thuộc Nhóm nào được gán (hoặc bị loại trừ khỏi tất cả) thấy danh sách hệ thống "Tất cả bản ghi". Người thuộc nhiều Nhóm được gán các danh sách khác nhau thấy mở sẵn danh sách có **ưu tiên cao nhất do người cấu hình xếp tường minh**; các danh sách còn lại vẫn chuyển sang được. Đây không phải xung đột quyền nên không áp nguyên tắc hạn chế thắng.

  **Lý do nghiệp vụ:** chọn ngẫu nhiên hay theo thứ tạo khiến hai người cùng vai trò thấy hai màn hình khác nhau mà không ai giải thích được; màn hình trắng khi không được gán là lỗi vận hành.

- **`BR-08.2` (Tôn trọng phân quyền trường):** Trường Ẩn với người dùng → cột bị loại bỏ hoàn toàn khỏi bảng của họ, không để cột trống. Trường bị che hoặc nhạy cảm chưa được xem đầy đủ → cột vẫn hiển thị giá trị đã che và **không dùng được làm điều kiện lọc hay sắp xếp** cho người đó. Điều kiện lọc mặc định của danh sách dùng chung trên trường như vậy không được áp và không được tính cho người đó (`BR-03.5`).

  **Lý do nghiệp vụ:** cột trống vẫn tiết lộ trường tồn tại; lọc hay sắp xếp theo trường bị che cho phép suy ra giá trị thật.

- **`BR-08.3` (Bảo vệ danh sách hệ thống):** Danh sách "Tất cả bản ghi" không xoá được.

  **Lý do nghiệp vụ:** đây là lối thoát cuối cùng khi mọi danh sách dùng chung bị cấu hình sai.

- **`BR-08.4` (Sao chép danh sách):** Sao chép một danh sách để tạo biến thể mà không đổi bản gốc.

  **Lý do nghiệp vụ:** sửa trực tiếp danh sách đang dùng để thử biến thể làm thay đổi màn hình của cả Nhóm đang làm việc.

- **`BR-08.5` (Không mở rộng phạm vi bản ghi):** Danh sách hiển thị chỉ chứa bản ghi trong mức Xem của chính người đang xem ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.7`, `BR-39.6`); điều kiện lọc chỉ thu hẹp tập đó. Điều kiện lọc theo người dùng diễn đạt bằng "Người phụ trách = người đang xem", "Đơn vị của người đang xem", không gắn cứng tên người.

  **Lý do nghiệp vụ:** danh sách dùng chung là cấu hình trình bày (Nguyên tắc OM-1); nếu nó mở được bản ghi ngoài phạm vi thì người có quyền quản lý danh sách trở thành người cấp quyền xem.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-08.1.1` | Nhóm Sales được gán "Cơ hội của tôi đang mở" | Nhân viên Sales mở màn hình Cơ hội | Mở sẵn danh sách đó, đúng cột, lọc "Người phụ trách = người đang xem", sắp theo ngày dự kiến chốt |
| `AC-08.1.2` | Người không thuộc Nhóm nào được gán | Mở màn hình danh sách | Thấy "Tất cả bản ghi", không màn hình trắng |
| `AC-08.1.3` | Hai người cùng thuộc Nhóm Sales và Nhóm Miền Bắc, mỗi Nhóm gán một danh sách | Cả hai mở màn hình | Cùng thấy danh sách có ưu tiên cao hơn; danh sách còn lại có trong lựa chọn |
| `AC-08.2.1` | Cột "Tình trạng điều tra" trong danh sách, Ẩn với Nhóm Sales | Thành viên Sales mở danh sách | Cột không có trong bảng |
| `AC-08.2.2` | Cột "Số căn cước" bị che với người xem | Mở bộ lọc và sắp xếp | Không chọn được cột đó để lọc hay sắp xếp; cột hiện giá trị đã che |
| `AC-08.2.3` | Danh sách dùng chung "Khách VIP" lọc theo "Hạn mức tín dụng > 1 tỷ"; trường bị che với nhân viên Sales | Nhân viên Sales mở danh sách | Thấy các khách trong mức Xem của mình như không có điều kiện hạn mức; số đếm cũng vậy; ghi chú "một điều kiện không áp dụng với bạn" |
| `AC-08.3.1` | Người có quyền Quản lý danh sách hiển thị dùng chung | Xoá "Tất cả bản ghi" | Không có lựa chọn xoá |
| `AC-08.4.1` | Danh sách "Cơ hội lớn" đang gán cho 3 Nhóm | Sao chép và sửa bản sao | Bản gốc và màn hình của 3 Nhóm không đổi |
| `AC-08.5.1` | Danh sách "Tất cả Cơ hội Đang mở" gán cho Nhóm Sales; nhân viên có (Cơ hội, Xem) = Đơn vị của mình | Mở danh sách | Chỉ thấy Cơ hội trong đơn vị của mình |

---

### FEAT-09 — Cấu hình nâng cao, phân công tự động & hàng đợi chưa phân công

**Mô tả nghiệp vụ:** Thiết lập chống trùng, điều kiện đóng Cơ hội, phân công tự động và hàng đợi cho bản ghi chưa phân công được.

**Vai trò sử dụng chính:** Người có quyền Quản lý cấu hình đối tượng; Người có toàn quyền (đơn vị tiếp nhận).

**Điều kiện tiên quyết:** Với phân công tự động: quy tắc đã có đơn vị tiếp nhận (`BR-09.5`).

**Luồng chính:**

1. Chọn loại dữ liệu → cấu hình chống trùng, điều kiện đóng (Cơ hội), quy tắc phân công.
2. Với phân công theo khu vực: chỉ định trường xác định khu vực, danh mục khu vực, đơn vị tiếp nhận và người nhận.
3. Lưu → hệ thống kiểm tra tính hợp lệ (giá trị trùng giữa khu vực, đơn vị tiếp nhận chưa đặt) trước khi cho bật.

**Quy tắc nghiệp vụ:**

- **`BR-09.1` (Chống trùng lặp):** Nơi SRS phân hệ sở hữu đã đặc tả tiêu chí và chính sách trùng (ví dụ [`contacts-srs.md`](./contacts-srs.md) `BR-17.2`, `CFG-17-01` cho Khách hàng và Công ty), quy định đó thắng và Object Manager là màn hình cấu hình của nó. Loại dữ liệu chưa có đặc tả riêng dùng `CFG-09-01` (tiêu chí) và `CFG-09-02` (hành động khi nhập tay). Bất kể cấu hình: nhập tệp và tích hợp bên ngoài từ chối bản ghi trùng; báo cáo nhập liệt kê dòng bị từ chối kèm lý do và mã bản ghi trùng; phản hồi cho tích hợp phân biệt lỗi trùng với lỗi khác và trả mã bản ghi gốc cùng tên trường gây trùng (chỉ khi bên gọi có quyền Xem bản ghi gốc); quy tắc kiểm tra dữ liệu đánh giá trước, kiểm tra trùng sau, và báo cáo nêu **toàn bộ** lý do từ chối của một dòng. **Trường bị che với người thực hiện:** tiêu chí trùng nằm trên trường mà với người thực hiện (người nhập tay, người khởi chạy nhập tệp, người chịu trách nhiệm tích hợp) là Ẩn, bị che hoặc nhạy cảm chưa được xem đầy đủ thì không được dùng để so khớp cho người đó và không được nêu trong cảnh báo hay báo cáo; bản ghi khớp các tiêu chí khác vẫn xử lý bình thường; bản ghi mới được đưa vào danh sách rà trùng để người có quyền xem đầy đủ trường đó kiểm tra lại.

  **Lý do nghiệp vụ:** hai tài liệu cùng định nghĩa chống trùng sẽ cho hai kết quả trên cùng một bản ghi; báo cáo dừng ở lỗi đầu tiên bắt người dùng nhập lại nhiều lượt cho cùng một dòng.

- **`BR-09.2` (Điều kiện đóng Cơ hội):** Đóng Thắng **luôn** yêu cầu Giá trị thực tế, Ngày chốt thực tế, Liên hệ chính, và điều kiện qua giai đoạn của mọi Giai đoạn bị bỏ qua trong Quy trình hiện tại; ba điều kiện đầu không tắt được. Đóng Thua yêu cầu Lý do thua và ghi chú; điều kiện qua giai đoạn trung gian không bắt buộc. Chỉ "Bắt buộc có Người phụ trách khi đóng" là tham số `CFG-09-03`.

  **Lý do nghiệp vụ:** ba điều kiện là đầu vào của doanh số thực thu và kỳ báo cáo; cho tắt là cho doanh nghiệp tự vô hiệu số liệu doanh thu và sai lệch chỉ lộ ra khi chốt sổ.

- **`BR-09.3` (Phân công tự động):** Cơ chế theo `CFG-09-04`: xoay vòng chia đều, theo tải hiện tại, hoặc theo khu vực. Nơi SRS phân hệ đã đặc tả phân bổ riêng (ví dụ [`contacts-srs.md`](./contacts-srs.md) `FEAT-31` cho khách hàng tiềm năng), quy định đó thắng. **Hợp đồng cấu hình theo khu vực:** (1) đúng một trường xác định khu vực cho mỗi loại dữ liệu, chưa chỉ định thì không bật được; (2) mỗi Khu vực gồm tên, tập giá trị nhận diện, **đơn vị tiếp nhận** và danh sách người nhận; (3) một giá trị chỉ thuộc một khu vực — trùng thì chặn ngay khi lưu, nêu giá trị và hai khu vực; (4) so khớp không phân biệt hoa/thường và khoảng trắng đầu cuối; (5) trong một khu vực, chia theo xoay vòng. Người nhận của mọi quy tắc phải là thành viên của đơn vị tiếp nhận — có đơn vị đó là Đơn vị chính hoặc Đơn vị kiêm nhiệm ở **bất kỳ mức nào**, hoặc thuộc đơn vị con khi hàng đợi bật "gồm các đơn vị con", đúng như [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.11`; hệ thống chỉ chọn người **Đang hoạt động** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-42.1`), không bật trạng thái vắng mặt, và **có ô Xem của loại dữ liệu đó khác Không có** tại thời điểm gán ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`) — người không thỏa bị bỏ qua, nếu không còn ai thỏa thì bản ghi vào hàng đợi (`BR-09.5`); bản ghi được gán thuộc đơn vị của người nhận ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4). **Quy tắc phân công quyết định ai phụ trách bản ghi nên chịu trần Gán:** người cấu hình không phải Người có toàn quyền chỉ thêm được người nhận mà chính mình gán được bản ghi của đơn vị tiếp nhận cho họ theo ô (loại dữ liệu, Gán) của mình ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`), và không tự thêm mình làm người nhận (Nguyên tắc 2 của IAM). **Kiểm tra lại trần:** khi ô Gán của người lưu quy tắc gần nhất bị thu hẹp, khi họ mất toàn quyền, bị tạm ngưng hoặc rời workspace, hệ thống kiểm tra lại người nhận theo trần mới (cùng tinh thần [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`); quy tắc có người nhận vượt trần thì **tạm dừng**, bản ghi mới vào hàng đợi chưa phân công, Người có toàn quyền và người giữ quyền Quản lý cấu hình đối tượng được thông báo; quy tắc chạy lại khi một người có trần bao phủ mọi người nhận lưu xác nhận. Thêm hay bớt người nhận, đổi cơ chế, đổi tập giá trị của khu vực thuộc nhật ký thay đổi cấu hình quyền và phân loại theo `BR-10.3` (mọi thay đổi quy tắc phân công là nới rộng). Phân công Công việc do [`tasks-srs.md`](./tasks-srs.md) `BR-13.6` quy định, không thuộc quy tắc này.

  **Lý do nghiệp vụ:** một giá trị thuộc hai khu vực làm hai bản ghi giống nhau về hai người; người nhận ngoài đơn vị tiếp nhận, hay người nhận mà người cấu hình không tự gán được, biến quy tắc phân công thành đường giao bản ghi vượt chính quyền Gán của người cấu hình; giao bản ghi cho người không có ô Xem tạo bản ghi mà người phụ trách không mở được.

- **`BR-09.4` (Phạm vi áp dụng):** Cấu hình nâng cao là cấu hình cấp workspace, áp đồng nhất cho mọi người dùng.

  **Lý do nghiệp vụ:** chống trùng hay điều kiện đóng khác nhau theo người dùng làm cùng một dữ liệu được chấp nhận hay bị từ chối tuỳ ai nhập.

- **`BR-09.5` (Hàng đợi chưa phân công theo khung hàng đợi của IAM):** Áp [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10`, `BR-35.11`, `BR-35.12`, `BR-35.13`, `BR-35.14`:
  - **(a) Đơn vị tiếp nhận:** mỗi quy tắc phân công tự động và mỗi Khu vực khai báo một đơn vị tiếp nhận; mặc định theo loại nguồn **"Quy tắc phân công tự động"** (ma trận chuyển đổi dùng loại nguồn **"Ma trận chuyển đổi"**; [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-35-01`), tìm từ Đơn vị chính của người tạo quy tắc lên gốc; quy tắc chưa có đơn vị tiếp nhận không bật được; chỉ Người có toàn quyền đặt khác mặc định, đổi, hoặc bật "gồm các đơn vị con".
  - **(b) Loại bản ghi:** Khách hàng, Công ty, Cơ hội là **bản ghi chờ phân công** (thuộc đơn vị tiếp nhận tới khi có Người phụ trách, sau đó theo Người phụ trách); Vé hỗ trợ là **bản ghi công việc** (ở lại đơn vị tiếp nhận suốt vòng đời) theo [`tickets-srs.md`](./tickets-srs.md). Hàng đợi của Công việc do [`tasks-srs.md`](./tasks-srs.md) `BR-13.6` quy định, không qua quy tắc phân công của Object Manager. Khai báo của SRS phân hệ sở hữu thắng nếu khác.
  - **(c) Vào hàng đợi:** bản ghi vào hàng đợi chưa phân công của đơn vị tiếp nhận khi không còn người nhận khả dụng, khi không xác định được khu vực, hoặc khi khu vực chưa có người nhận — không bao giờ bị bỏ qua âm thầm. Người phụ trách đơn vị tiếp nhận và Người có toàn quyền nhận cảnh báo.
  - **(d) Nhận việc:** thành viên đơn vị tiếp nhận có ô Xem của loại đó khác Không có thấy bản ghi chưa phân công; tự nhận là thao tác Gán, được khi ô Gán khác Không có và không có nguồn chặn.
  - **(e) Trả về hàng đợi** (khi người phụ trách chuyển phòng, tạm ngưng, rời workspace): bản ghi chờ phân công ở giai đoạn chưa chốt theo SRS phân hệ (ví dụ khách hàng tiềm năng chưa chuyển đổi, Cơ hội Đang mở) về hàng đợi hiện tại của quy tắc đã phân công nó; quy tắc không còn thì về đơn vị tiếp nhận mặc định của loại nguồn; không xác định được thì lựa chọn này không hiển thị.
  - **(f) Chuyển hàng đợi** chỉ áp cho bản ghi công việc theo quy định của [`tickets-srs.md`](./tickets-srs.md); Object Manager không mở chuyển hàng đợi cho bản ghi chờ phân công — muốn đưa sang đội khác thì gán người.

  **Lý do nghiệp vụ:** hàng đợi chỉ vận hành được khi đội nhận việc thấy việc chưa ai nhận; nhưng đơn vị tiếp nhận quyết định ai thấy hàng đợi bất kể mức Xem, nên đặt hay đổi nó là nới phạm vi cho cả một đội và chỉ Người có toàn quyền được quyết (Nguyên tắc 1 của IAM).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-09.1.1` | Cơ hội (chưa có đặc tả chống trùng riêng ở SRS phân hệ) dùng `CFG-09-01` = khớp tên Cơ hội và Công ty, `CFG-09-02` = Cảnh báo | Nhập tay Cơ hội trùng theo tiêu chí đã chọn | Cảnh báo, vẫn cho lưu nếu người dùng xác nhận |
| `AC-09.1.2` | Tệp nhập có dòng vừa sai định dạng vừa trùng | Nhập | Dòng bị từ chối; báo cáo nêu cả hai lý do và mã bản ghi trùng |
| `AC-09.1.3` | Tích hợp ghi bản ghi trùng | Gửi | Phản hồi nêu lỗi trùng, mã bản ghi gốc và tên trường gây trùng |
| `AC-09.1.4` | "Số căn cước" là tiêu chí trùng và bị che với nhân viên A | A nhập tay khách hàng có số căn cước trùng một khách hàng khác | Không có cảnh báo trùng theo số căn cước, không nêu trường đó; khách hàng mới vào danh sách rà trùng của người có quyền xem đầy đủ |
| `AC-09.1.5` | Như trên, A nhập tệp | Nhập | Dòng không bị từ chối vì số căn cước; báo cáo nhập không nhắc trường đó |
| `AC-09.2.1` | Cơ hội thiếu Liên hệ chính | Đóng Thắng | Bị chặn, bất kể cấu hình nào của workspace |
| `AC-09.2.2` | Nhân viên đóng Thua không chọn Lý do thua | Lưu | Bị chặn |
| `AC-09.2.3` | `CFG-09-03` bật; Cơ hội chưa có Người phụ trách | Đóng Thắng | Bị chặn, yêu cầu chọn Người phụ trách |
| `AC-09.3.1` | "Miền Bắc" và "Miền Trung" cùng khai "Thanh Hóa" | Lưu danh mục | Bị chặn, nêu "Thanh Hóa" và hai khu vực |
| `AC-09.3.2` | Khu vực "Miền Bắc" nhận "Hà Nội" | Hai bản ghi "hà nội " và "Hà Nội" | Cùng về nhóm người nhận của "Miền Bắc" |
| `AC-09.3.3` | Khai báo người nhận thuộc đơn vị ngoài đơn vị tiếp nhận của khu vực | Lưu | Bị chặn, nêu người nhận không thuộc đơn vị tiếp nhận |
| `AC-09.3.4` | Một người nhận của quy tắc đang Tạm ngưng | Bản ghi mới đến | Không được gán cho người đó |
| `AC-09.3.5` | Người nhận P của quy tắc phân công Cơ hội có ô (Cơ hội, Xem) = Không có | Cơ hội mới đến | Không gán cho P; nếu không còn ai thỏa thì Cơ hội vào hàng đợi chưa phân công |
| `AC-09.3.6` | Thành viên có quyền Quản lý cấu hình đối tượng, ô (Cơ hội, Gán) = Chỉ của mình | Thêm đồng nghiệp làm người nhận của quy tắc | Từ chối, nêu vượt ô Gán của người cấu hình |
| `AC-09.3.7` | Nhật ký thay đổi cấu hình quyền gặp sự cố | Người có toàn quyền thêm người nhận, bớt người nhận cuối cùng của khu vực "Miền Trung", đổi tập giá trị khu vực, đổi cơ chế sang theo tải | Cả bốn thao tác bị huỷ, thông báo lỗi; quy tắc giữ nguyên |
| `AC-09.3.8` | Quy tắc do Quản lý Q lưu gần nhất; ô (Cơ hội, Gán) của Q bị thu hẹp từ Đơn vị và các đơn vị con về Đơn vị của mình, hai người nhận ở đơn vị con | Cơ hội mới đến | Quy tắc tạm dừng; Cơ hội vào hàng đợi; Người có toàn quyền nhận thông báo |
| `AC-09.3.9` | Thành viên có đơn vị tiếp nhận là kiêm nhiệm mức Chỉ xem | Thêm làm người nhận | Được chấp nhận là thành viên đơn vị tiếp nhận |
| `AC-09.5.1` | Quy tắc phân công mới chưa có đơn vị tiếp nhận, không có mặc định | Bật quy tắc | Nút bật bị vô hiệu kèm giải thích cần Người có toàn quyền đặt đơn vị tiếp nhận |
| `AC-09.5.2` | Mọi người nhận của "Kinh doanh – Hà Nội" đang nghỉ phép | Khách hàng tiềm năng mới đến | Vào hàng đợi chưa phân công của "Kinh doanh – Hà Nội"; người phụ trách đơn vị và Người có toàn quyền nhận cảnh báo |
| `AC-09.5.3` | Bản ghi không có giá trị ở trường khu vực | Phân công chạy | Vào hàng đợi chưa phân công của đơn vị tiếp nhận mặc định của quy tắc, không bị bỏ qua |
| `AC-09.5.4` | Nhân viên thuộc đơn vị tiếp nhận, (Khách hàng, Xem) = Chỉ của mình, Gán = Chỉ của mình | Mở hàng đợi, bấm Nhận việc | Thấy bản ghi chưa phân công; nhận thành công |
| `AC-09.5.5` | Nhân viên đơn vị khác | Mở hàng đợi của "Kinh doanh – Hà Nội" | Không thấy bản ghi chưa phân công của đơn vị đó |
| `AC-09.5.6` | Thành viên có quyền Quản lý cấu hình đối tượng, không có toàn quyền | Đổi đơn vị tiếp nhận của quy tắc | Trường ở dạng chỉ đọc kèm giải thích |
| `AC-09.5.7` | Người phụ trách 8 Cơ hội Đang mở do quy tắc phân công gán bị tạm ngưng; người thực hiện chọn trả về hàng đợi | Xác nhận | 8 Cơ hội không còn Người phụ trách, hiện trong hàng đợi hiện tại của quy tắc |
| `AC-09.5.8` | Cơ hội trong hàng đợi chưa phân công | Tìm thao tác chuyển sang hàng đợi khác | Không có; chỉ có gán người |

---

### FEAT-10 — Nhật ký thay đổi cấu hình & hoàn tác phân quyền trường

**Mô tả nghiệp vụ:** Tự động ghi mọi thay đổi cấu hình trong Object Manager, phân loại thao tác theo tác động tới quyền để áp đúng chính sách khi nhật ký gặp sự cố, và cho hoàn tác một thay đổi phân quyền trường sai.

**Vai trò sử dụng chính:** Hệ thống (ghi); người có quyền Xem nhật ký cấu hình đối tượng (tra cứu); người có quyền Quản lý phân quyền trường & bố cục (hoàn tác).

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Mọi thao tác cấu hình sinh một dòng nhật ký; thao tác thuộc nhóm thay đổi ai thấy gì đồng thời vào nhật ký thay đổi cấu hình quyền của IAM.
2. Người có quyền tra cứu lọc theo thời gian, người thực hiện, loại dữ liệu, loại thao tác.
3. Chọn một dòng thay đổi phân quyền trường → Hoàn tác → hệ thống kiểm tra `BR-10.5` và trần `BR-03.8` → tạo thay đổi mới trỏ về dòng gốc.

**Quy tắc nghiệp vụ:**

- **`BR-10.1` (Ghi nhận toàn diện):** Mọi thao tác tạo, sửa, xoá cấu hình sinh đúng một dòng nhật ký: thời điểm, người thực hiện (kèm nhãn "Nhà cung cấp" và mã phiên nếu thực hiện trong Phiên hỗ trợ hoặc Phiên triển khai), loại thao tác, loại dữ liệu và mục cấu hình bị tác động. Nhật ký chỉ ghi, không ai sửa hay xoá được.

  **Lý do nghiệp vụ:** điều tra "vì sao cả phòng đột nhiên không thấy trường X" cần biết chính xác ai đổi gì lúc nào.

- **`BR-10.2` (Giá trị trước và sau):** Thay đổi phân quyền trường (hai chiều, bắt buộc), khai báo nhạy cảm, mẫu che, bố cục và đơn vị tiếp nhận lưu đầy đủ giá trị trước và sau; thay đổi khác lưu hành động và mục bị tác động.

  **Lý do nghiệp vụ:** không có giá trị trước thì không chứng minh được với khách hàng ai đã mở quyền xem dữ liệu nhạy cảm, và không hoàn tác được (`BR-10.5`).

- **`BR-10.3` (Hành vi khi không ghi được nhật ký):** Thao tác làm thay đổi ai thấy gì hoặc ai làm được gì thuộc nhóm **nhật ký thay đổi cấu hình quyền** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.4`) và được phân loại theo tác động ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4). **Phân loại theo kết quả, không theo chiều thao tác:** hệ thống so sánh quyền thực tế trên từng trường (cả hai chiều, sau phân giải `BR-03.2`) của **từng người bị ảnh hưởng** trước và sau thay đổi. Thao tác là thu hẹp chỉ khi không ai được thấy hay làm thêm điều gì; chỉ cần một người được nới thêm ở một trường thì cả thao tác là **nới rộng**. Ví dụ: tạo cấu hình cho một Nhóm trên một trường mà Bố cục mặc định đang chặt hơn (`BR-03.2`, `AC-03.2.4`) là nới rộng với thành viên Nhóm đó, dù chỉ là "thêm một cấu hình". Bảng dưới là các trường hợp điển hình:

  | Loại | Thao tác của Object Manager | Khi nhật ký gặp sự cố |
  | --- | --- | --- |
  | **Thu hẹp** (không ai được nới thêm) | Hạ Mức quyền trên trường (Xem & Sửa → Chỉ xem → Ẩn); tăng mức che; đánh dấu trường nhạy cảm; đổi sang mẫu che chặt hơn; vô hiệu hóa trường (`BR-02.3`) cùng việc tự động gỡ trường khỏi cấu hình kèm theo (`BR-02.5`); hoàn tác có tác dụng thu hẹp | **Không bị chặn.** Thao tác có hiệu lực ngay; hệ thống giữ sự kiện để **ghi bù** khi nhật ký phục hồi, đánh dấu "ghi bù" kèm thời điểm thao tác thật, cảnh báo Người có toàn quyền nếu chưa ghi bù xong sau 15 phút ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.6`; [ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md)) |
  | **Nới rộng** | Nâng Mức quyền trên trường; giảm mức che; gỡ cấu hình hạn chế của một Nhóm trên một trường (gỡ hạn chế là nới rộng); bỏ đánh dấu nhạy cảm hoặc đổi sang mẫu che lỏng hơn; hoàn tác có tác dụng nới rộng; tạo cấu hình cho một Nhóm trên trường mà Bố cục mặc định hoặc Nhóm khác đang chặt hơn với một số thành viên; đặt hoặc đổi đơn vị tiếp nhận (`BR-09.5`); mọi thay đổi quy tắc phân công và lựa chọn người phụ trách của ma trận chuyển đổi (`BR-09.3`, `BR-05.3`); thao tác vừa thu hẹp vừa nới rộng | **Đóng khi lỗi:** huỷ thao tác và báo lỗi cho người thực hiện ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.5`; [ADR-0003](../docs/adr/0003-permission-config-audit-log-fail-closed.md)) |
  | **Trung tính** | Bật/tắt ràng buộc bắt buộc ở phân quyền trường hoặc bố cục | Đóng khi lỗi, như nới rộng |

  Các thay đổi khác của Object Manager không thuộc nhóm trên, với lý do loại trừ tường minh: thay đổi trình bày và tổ chức (màu, thứ tự, nhãn, tên Quy trình, sắp xếp bố cục) vì không cấp hay thu hồi quyền của ai; bố cục vì không bao giờ mở quyền (`BR-03.7`); danh sách hiển thị dùng chung vì chỉ trình bày trong phạm vi đã có (`BR-08.5`); trường, quy tắc kiểm tra, trạng thái, nguồn, Quy trình bán hàng, chống trùng và điều kiện đóng Cơ hội vì là quy tắc dữ liệu chứ không phải quyền. **Các thay đổi trình bày và quy tắc dữ liệu đã loại trừ** ở trên vẫn ghi vào nhật ký cấu hình (`BR-10.1`); sự cố nhật ký không chặn chúng và sự kiện được ghi bù. Quy tắc phân công tự động và lựa chọn người phụ trách của ma trận chuyển đổi **không** thuộc loại trừ này: chúng quyết định ai phụ trách, nên ai thấy bản ghi tương lai. Phân loại theo tác dụng lên tập người thấy được bản ghi: thêm hay bớt người nhận, đổi cơ chế, đổi trường hay tập giá trị khu vực, tắt quy tắc đều chuyển bản ghi tương lai sang người khác hoặc vào hàng đợi mà mọi thành viên đơn vị tiếp nhận thấy (bớt người nhận cuối cùng của một khu vực là ví dụ rõ nhất), nên **mọi thay đổi quy tắc phân công được phân loại là nới rộng** và đóng khi lỗi (`AC-09.3.7`). Xem trước quyền thực tế (`BR-03.3`) không tạo thay đổi nên không thuộc nhóm nào. Năng lực cấu hình mới bổ sung sau này **mặc định thuộc nhóm nhật ký thay đổi cấu hình quyền** nếu tác động tới ai thấy gì hoặc ai làm được gì; muốn loại trừ phải nêu lý do tường minh trong tài liệu.

  **Lý do nghiệp vụ:** chặn một lượt ẩn trường nhạy cảm chỉ vì nhật ký lỗi là giữ nguyên quyền xem đúng lúc doanh nghiệp cần cắt khẩn cấp — rủi ro lớn hơn rủi ro nhật ký đến muộn vài phút; còn mất dấu vết của một lượt mở quyền xem là mất khả năng chứng minh tuân thủ cho đúng thao tác nhạy cảm nhất.

- **`BR-10.4` (Quyền tra cứu):** Người có quyền Xem nhật ký cấu hình đối tượng tra cứu toàn bộ nhật ký của `FEAT-10`; các dòng thuộc nhóm nhật ký thay đổi cấu hình quyền cũng xem được qua quyền Xem nhật ký quyền của IAM. Người xem chỉ thấy tên hay nội dung nhận diện của bản ghi khi chính họ có mức Xem bao phủ bản ghi đó ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.7`). Nhân sự vận hành nền tảng chỉ tra cứu trong Phiên hỗ trợ ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-08`), và lượt tra cứu đó vào nhật ký truy cập của nhà cung cấp. Thời hạn lưu theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-41-02`.

  **Lý do nghiệp vụ:** doanh nghiệp phải tự trả lời được câu hỏi kiểm toán "ai đã đổi quyền xem trường này" mà không phụ thuộc nhà cung cấp; nhật ký không được thành đường xem dữ liệu khách hàng.

- **`BR-10.5` (Hoàn tác thay đổi phân quyền trường):** Người có quyền Quản lý phân quyền trường & bố cục hoàn tác một thay đổi phân quyền trường về đúng giá trị trước dựa trên dòng nhật ký, thay vì dựng lại bằng tay:
  - Hoàn tác là một thay đổi mới, sinh dòng nhật ký riêng trỏ về dòng gốc, không ghi đè lịch sử; được phân loại theo **tác dụng** của chính nó tại `BR-10.3` (đưa trường từ Ẩn về Xem & Sửa là nới rộng; đưa về Ẩn là thu hẹp) và chịu trần `BR-03.8`.
  - Chỉ hoàn tác được thay đổi **mới nhất** của cùng mục tiêu (Nhóm, loại dữ liệu, trường); có thay đổi mới hơn thì từ chối, nêu thay đổi đó cùng người và thời điểm.
  - Hoàn tác một lần **tạo mới** cấu hình thì gỡ cấu hình đó (trường về mức theo `BR-03.2`); hoàn tác một lần **gỡ** thì khôi phục đúng cấu hình cũ.

  **Lý do nghiệp vụ:** ẩn nhầm 20 trường cuối ngày ảnh hưởng tức thời tới toàn bộ người dùng; hoàn tác một thay đổi cũ đè lên thay đổi mới hơn là mất dữ liệu âm thầm ngay trong lúc sửa sai.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-10.1.1` | Nhân sự vận hành nền tảng đổi màu một trạng thái trong Phiên hỗ trợ | Xem nhật ký | Dòng ghi nhãn "Nhà cung cấp", tên nhân sự và mã phiên |
| `AC-10.2.1` | Đổi "Lương" của Nhóm Sales từ Xem & Sửa sang Ẩn | Xem nhật ký | Dòng ghi giá trị trước "Xem & Sửa, Hiện đầy đủ" và sau "Ẩn" |
| `AC-10.3.1` | Nhật ký thay đổi cấu hình quyền đang gặp sự cố | Đặt "Lương" = Ẩn cho Nhóm Sales | Thành công ngay; lượt mở bản ghi kế tiếp của Sales không thấy "Lương"; khi nhật ký phục hồi, dòng được ghi bù, đánh dấu "ghi bù" kèm thời điểm thật |
| `AC-10.3.2` | Tình huống `AC-10.3.1`, ghi bù chưa xong sau 15 phút | — | Người có toàn quyền nhận cảnh báo |
| `AC-10.3.3` | Nhật ký gặp sự cố | Đánh dấu "Số căn cước" là nhạy cảm | Thành công, ghi bù sau |
| `AC-10.3.4` | Nhật ký gặp sự cố | Vô hiệu hóa trường "Mã ưu đãi cũ" | Thành công; trường biến mất với mọi người; ghi bù sau |
| `AC-10.3.5` | Nhật ký gặp sự cố | Đặt "Lương" = Xem & Sửa cho Nhóm Sales | Bị huỷ, thông báo lỗi; cấu hình không đổi |
| `AC-10.3.6` | Nhật ký gặp sự cố | Gỡ cấu hình Ẩn "Lương" của Nhóm Sales | Bị huỷ (gỡ hạn chế là nới rộng) |
| `AC-10.3.9` | Nhật ký gặp sự cố; Bố cục mặc định Ẩn "Thưởng"; D thuộc Nhóm Z, Nhóm Z chưa cấu hình "Thưởng" | Tạo cấu hình "Thưởng" = Chỉ xem cho Nhóm Z | Bị huỷ: D sẽ được thấy "Thưởng" nên là nới rộng; cấu hình không đổi |
| `AC-10.3.10` | Nhật ký gặp sự cố; Bố cục mặc định Ẩn "Thưởng" | Tạo cấu hình "Thưởng" = Ẩn cho Nhóm Z | Thành công, ghi bù sau — không ai thấy thêm |
| `AC-10.3.7` | Nhật ký gặp sự cố | Bật bắt buộc cho "Ngân sách" | Bị huỷ |
| `AC-10.3.8` | Nhật ký gặp sự cố | Đổi màu một Giai đoạn | Thành công; dòng nhật ký cấu hình được ghi bù |
| `AC-10.4.1` | Thành viên giữ vai trò Kiểm toán | Mở nhật ký cấu hình đối tượng | Tra cứu được; dòng trỏ tới bản ghi ngoài phạm vi Xem của họ chỉ hiện mã và loại dữ liệu |
| `AC-10.4.2` | Thành viên không có quyền Xem nhật ký cấu hình đối tượng | Mở nhật ký | Bị từ chối |
| `AC-10.5.1` | Một lượt ẩn nhầm 20 trường của Khách hàng | Hoàn tác | 20 trường về giá trị trước; có dòng hoàn tác trỏ về dòng gốc |
| `AC-10.5.2` | Sau lượt cần hoàn tác còn một thay đổi mới hơn trên cùng trường và Nhóm | Hoàn tác lượt cũ | Bị từ chối, nêu người và thời điểm của thay đổi mới hơn |
| `AC-10.5.3` | Lượt gốc là tạo mới cấu hình cho một trường | Hoàn tác | Cấu hình bị gỡ, không để lại mục rỗng |
| `AC-10.5.4` | Nhật ký gặp sự cố; hoàn tác sẽ đưa trường từ Ẩn về Xem & Sửa | Hoàn tác | Bị huỷ (nới rộng) |
| `AC-10.5.5` | Người hoàn tác thuộc Nhóm bị ảnh hưởng; hoàn tác sẽ nới rộng Nhóm đó | Hoàn tác | Từ chối, nêu lý do không tự nới rộng (`BR-03.8`) |

---

## 4. Yêu cầu phi chức năng

### 4.1 Bảo mật & phân quyền

- **`NFR-01` (Cách ly giữa các workspace):** Mọi cấu hình của Object Manager cô lập theo workspace; không đường nào để cấu hình hay dữ liệu của workspace này hiện ở workspace khác ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `NFR-03`).
- **`NFR-02` (Quyền vào khu vực cấu hình):** Chỉ người giữ quyền quản trị tương ứng tại `BR-01.4` (hoặc Người có toàn quyền) vào được từng khu vực cấu hình, kể cả bằng đường dẫn trực tiếp. Nhân sự vận hành nền tảng chỉ vào trong Phiên hỗ trợ hoặc Phiên triển khai ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-08`), trong phạm vi và thời gian của phiên, mọi thao tác ghi vào nhật ký truy cập của nhà cung cấp kèm định danh nhân sự.
- **`NFR-03` (Hạn chế hơn thắng):** Khi các Nhóm chồng lấn cho kết quả mâu thuẫn, hệ thống luôn chọn phương án hạn chế hơn (`BR-03.2`); lỗi khi tính phân quyền trường dẫn tới Ẩn, không bao giờ tới hiển thị ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `NFR-01`).

### 4.2 Toàn vẹn & nhất quán dữ liệu

- **`NFR-04` (Nhất quán đa kênh):** Phân quyền trường, che dữ liệu và quy tắc kiểm tra thực thi đồng nhất trên mọi kênh: máy tính, điện thoại, nhập tệp, tác vụ tự động, tích hợp, tác nhân AI.
- **`NFR-05` (Không để cấu hình lỗi xuống người dùng):** Mọi cấu hình dạng biểu thức (khuôn dạng, công thức) được xác nhận hợp lệ trước khi lưu.
- **`NFR-06` (Bảo vệ dữ liệu lịch sử):** Xoá trường, vô hiệu hóa lựa chọn, trạng thái, nguồn, Giai đoạn hay lưu trữ Quy trình không bao giờ làm sai lệch hoặc mất dữ liệu lịch sử.
- **`NFR-10` (Thay đổi cấu trúc của cấu hình đang vận hành):** Mọi thay đổi làm đổi cấu trúc dữ liệu cấu hình đang vận hành của doanh nghiệp (ví dụ chuyển danh sách Giai đoạn chung thành Giai đoạn thuộc từng Quy trình) phải: (1) có quy tắc ánh xạ được duyệt cho mọi giá trị đang tồn tại, kể cả giá trị chỉ còn được bản ghi lịch sử tham chiếu; (2) giữ nguyên trạng thái vĩ mô — Cơ hội đã chốt không trở lại Đang mở và ngược lại; (3) đối chiếu trước và sau tổng số bản ghi theo trạng thái, tổng giá trị và doanh số dự báo trọng số, sai lệch dự báo phải được giải thích và duyệt trước; (4) chặn ghi trong lúc chuyển đổi và **nghiệm thu bằng một thao tác ghi thật bị từ chối**; (5) có phương án khôi phục được diễn tập trước lần chạy thật trên môi trường mang đúng cấu hình thật của doanh nghiệp, nghiệm thu bằng **so khớp từng mục cấu hình theo từng thuộc tính** với bản chụp trước (toàn bộ thuộc tính, không chỉ phần chuyển đổi dùng tới), không chỉ bằng tổng số liệu; (6) thông báo trước cho Người có toàn quyền kèm bảng đối chiếu cũ → mới của chính workspace đó. *Lý do:* sai một đợt chuyển đổi làm sai dự báo của toàn workspace cùng lúc; tổng số khớp không chứng minh từng mục quay về nguyên trạng.
- **`NFR-11` (Diễn giải cấu hình phân quyền trường nhất quán và không nới):** Mọi điểm thi hành phân quyền trường (lưu, hiển thị, danh sách, tìm kiếm, báo cáo, tệp xuất, tác nhân AI) diễn giải một cấu hình **giống hệt** màn hình cấu hình đang hiển thị cho người quản trị; cấu hình được lưu theo cách diễn đạt cũ mà không đủ dữ kiện để xác định chắc một mức thì lấy mức hạn chế hơn. *Lý do:* người quản trị thấy một chính sách, hệ thống thi hành chính sách khác là lỗi lộ quyền không ai nhìn thấy.

### 4.3 Hiệu năng & vận hành

- **`NFR-07` (Thao tác song song):** Hai người cấu hình hai đơn vị cấu hình khác nhau cùng lúc không ghi đè nhau.
- **`NFR-07b` (Chống ghi đè trên cùng một đơn vị cấu hình):** Khi hai người mở và lưu cùng một đơn vị cấu hình — một bảng phân quyền trường (Nhóm × loại dữ liệu), một Quy trình, một danh sách hiển thị, một mục cấu hình nâng cao — người lưu sau được cảnh báo trước khi ghi, nêu **tên người đã thay đổi và thời điểm**, và phải xác nhận lại. Hai đơn vị cấu hình khác nhau không báo xung đột.
- **`NFR-08` (Giới hạn quy mô an toàn):** Biểu mẫu và danh sách tải dưới 1,5 giây ở điều kiện biên: loại dữ liệu đạt trần `CFG-02-01` **và** người dùng thuộc 20 Nhóm có cấu hình phân quyền trường — hạn mức số Nhóm mỗi người tham gia phân giải phân quyền trường. Vượt ngưỡng, hệ thống chặn thêm Nhóm và nêu lý do kèm gợi ý tổ chức lại. *Lý do:* quá 20 Nhóm, quyền thực tế của một người không ai giải thích được bằng lời và công cụ xem trước trở thành bảng không đọc nổi.
- **`NFR-09` (Dữ liệu đo):** Hệ thống ghi nhận tối thiểu: mỗi thay đổi cấu hình (đã có qua `FEAT-10`); số bản ghi mang cờ thiếu dữ liệu theo loại dữ liệu và trường; số lần thao tác cấu hình bị chặn vì xung đột (`BR-02.5`, `BR-06.1`, `BR-07.6`); số lần quy tắc không đánh giá được (`BR-04.2`); số lần cảnh báo ghi đè (`NFR-07b`); số sự kiện ghi bù và thời gian ghi bù (`BR-10.3`); số bản ghi vào hàng đợi chưa phân công (`BR-09.5`).

---

## 5. Ma trận quyền truy cập tính năng

Cột là các vai trò thao tác tại Mục 2.3. "Q: …" là quyền quản trị tại `BR-01.4`; "Ô" là ô của loại dữ liệu trong ma trận quyền IAM.

| Mã FEAT | Tính năng | Người có toàn quyền | Thành viên giữ quyền quản trị của Object Manager | Người dùng nghiệp vụ | Kiểm toán viên | Nhân sự vận hành nền tảng | Hệ thống |
| --- | --- | :---: | :---: | :---: | :---: | :---: | :---: |
| `FEAT-01` | Danh mục loại dữ liệu & quyền quản trị | **Toàn quyền** | Xem các khu vực theo quyền mình có | — | — | Chỉ trong Phiên hỗ trợ | — |
| `FEAT-02` | Trường tuỳ biến | **Toàn quyền** | Q: Quản lý cấu hình đối tượng | Nhập liệu theo phân quyền trường | — | Chỉ trong Phiên hỗ trợ/triển khai | Tính trường công thức |
| `BR-02.7` | Khai báo trường nhạy cảm | **Toàn quyền** (trừ trường hệ thống) | Q: Quản lý phân quyền trường & bố cục, chịu `BR-03.8` | Thấy giá trị theo mức hiển thị | — | Chỉ trong Phiên hỗ trợ/triển khai | Che khi hiển thị, che cho tác nhân AI |
| `FEAT-03` | Phân quyền trường & bố cục | **Toàn quyền**, không bị phân quyền trường giới hạn (`BR-03.2b`) | Q: Quản lý phân quyền trường & bố cục, chịu `BR-03.8` | Chịu phân quyền trường | — | Chỉ trong Phiên hỗ trợ/triển khai | — |
| `FEAT-04` | Quy tắc kiểm tra | **Toàn quyền** | Q: Quản lý cấu hình đối tượng | Chịu quy tắc khi lưu | — | Chỉ trong Phiên hỗ trợ/triển khai | Kiểm tra trên mọi kênh |
| `FEAT-05` | Vòng đời & ma trận chuyển đổi | **Toàn quyền** | Q: Quản lý cấu hình đối tượng | Chịu ràng buộc; thao tác theo ô của [`contacts-srs.md`](./contacts-srs.md) | — | Chỉ trong Phiên hỗ trợ/triển khai | Sinh Cơ hội, gắn/gỡ cờ |
| `FEAT-06` | Trạng thái & nguồn | **Toàn quyền** | Q: Quản lý cấu hình đối tượng | Chọn trạng thái/nguồn theo ô Sửa | — | Chỉ trong Phiên hỗ trợ/triển khai | — |
| `FEAT-07` | Quy trình & Giai đoạn | **Toàn quyền** | Q: Quản lý cấu hình đối tượng | Chuyển giai đoạn theo ô (Cơ hội, Sửa) | — | Chỉ trong Phiên hỗ trợ/triển khai | Cảnh báo trễ giai đoạn |
| `FEAT-08` | Danh sách hiển thị dùng chung | **Toàn quyền** | Q: Quản lý danh sách hiển thị dùng chung | Dùng danh sách được gán, trong mức Xem | — | Chỉ trong Phiên hỗ trợ/triển khai | — |
| `FEAT-09` | Cấu hình nâng cao & phân công | **Toàn quyền**, gồm đơn vị tiếp nhận | Q: Quản lý cấu hình đối tượng (trừ đơn vị tiếp nhận) | Nhận việc từ hàng đợi theo ô Xem và Gán | — | Chỉ trong Phiên hỗ trợ/triển khai | Phân công tự động, đưa vào hàng đợi |
| `FEAT-10` | Tra cứu nhật ký | **Toàn quyền** | Q: Xem nhật ký cấu hình đối tượng | — | **Có** (mặc định vai trò Kiểm toán, Kiểm toán quyền) | Chỉ trong Phiên hỗ trợ | Ghi và ghi bù |
| `BR-10.5` | Hoàn tác phân quyền trường | **Toàn quyền** | Q: Quản lý phân quyền trường & bố cục, chịu `BR-03.8` | — | — | Chỉ trong Phiên hỗ trợ | — |

**Ghi chú:**

1. Vai trò dựng sẵn và ma trận mặc định của chúng trên từng loại dữ liệu theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29` và SRS phân hệ sở hữu; tài liệu này chỉ khai báo mặc định cho quyền quản trị của mình (`BR-01.4`). Doanh nghiệp điều chỉnh qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02` hoặc vai trò tự tạo.
2. Tiến trình chạy thay người dùng không có cột riêng: miễn trừ phân quyền trường trong giới hạn `BR-03.4`, luôn chịu quy tắc kiểm tra và phạm vi bản ghi của người khởi chạy.

---

## 6. Kịch bản chấp nhận tổng hợp

### Kịch bản 1: Quy tắc kiểm tra trên mọi kênh *(FEAT-04)*

Người có quyền Quản lý cấu hình đối tượng đặt quy tắc số điện thoại quốc tế. Nhập sai trên giao diện bị báo ngay tại trường; tệp nhập có dòng sai bị từ chối đúng dòng đó; tích hợp ghi sai nhận lỗi; nhân viên sửa người phụ trách của một khách hàng cũ có số sai vẫn lưu được.

### Kịch bản 2: Hai Quy trình độc lập *(FEAT-07)*

Tạo "Bán phần mềm B2B" (5 Giai đoạn) và "Gia hạn dịch vụ" (2 Giai đoạn). Chuyển qua lại trên màn hình theo Quy trình, cột Giai đoạn và tỷ lệ hiển thị đúng từng Quy trình. Chuyển một Cơ hội từ "Đàm phán" (80%) sang "Gia hạn" buộc chọn Giai đoạn Đang mở; dự báo cập nhật theo tỷ lệ mới; lịch sử ghi người chuyển.

### Kịch bản 3: Danh sách hiển thị không mở thêm quyền *(FEAT-08)*

Nhóm Sales được gán "Cơ hội của tôi đang mở". Nhân viên Sales mở màn hình thấy ngay danh sách đó; cột "Biên lợi nhuận" (Ẩn với Sales) không có trong bảng; danh sách chỉ chứa Cơ hội trong mức Xem của chính người đó.

### Kịch bản 4: Trường bị Ẩn vắng mặt ở mọi kênh *(BR-03.5)*

Nhân viên không có quyền xem "Lương cơ bản" không thấy trường trên biểu mẫu, danh sách, tệp xuất, không tìm được bản ghi bằng từ khoá lương, và trợ lý AI phục vụ họ không trả trường đó.

### Kịch bản 5: Không ai bị bắt nhập trường mình không thấy *(BR-03.2, BR-05.5)*

"Hạn mức tín dụng" Ẩn với Sales, Bắt buộc với Kế toán. Nhân viên thuộc cả hai lưu được bản ghi; bản ghi gắn cờ; thành viên Kế toán có mức Xem bao phủ bản ghi được thông báo, lọc ra và bổ sung; cờ tự gỡ.

### Kịch bản 6: Trường nhạy cảm do doanh nghiệp khai báo *(BR-02.7)*

Doanh nghiệp tạo "Số căn cước", đánh dấu nhạy cảm. Mọi Nhóm thấy giá trị bị che; Nhóm "Kiểm soát rủi ro" được trao Hiện đầy đủ. Thành viên Nhóm đó hỏi trợ lý AI vẫn nhận giá trị đã che; nhân viên khác xuất danh sách nhận giá trị đã che; trường công thức "4 số cuối căn cước" tự động là nhạy cảm.

### Kịch bản 7: Thu hẹp không bị chặn khi nhật ký lỗi, nới rộng thì bị chặn *(BR-10.3)*

Nhật ký thay đổi cấu hình quyền gặp sự cố. Người có quyền ẩn khẩn cấp "Số tài khoản ngân hàng" với Nhóm Đối tác: có hiệu lực ngay, lượt mở kế tiếp của Đối tác không thấy trường. Cùng lúc, một người khác mở "Doanh thu" cho Nhóm Sales: bị huỷ, nhận thông báo lỗi. Khi nhật ký phục hồi, dòng ẩn trường được ghi bù với thời điểm thật.

### Kịch bản 8: Người cấu hình không tự mở quyền *(BR-03.8)*

Chuyên viên vận hành được giao quyền Quản lý phân quyền trường & bố cục, thuộc Nhóm Sales. Chuyên viên ẩn được "Ghi chú nội bộ" với Sales, nhưng không mở được "Biên lợi nhuận" cho Sales, và không đặt được "Doanh thu" = Xem & Sửa khi chính mình chỉ Chỉ xem.

### Kịch bản 9: Hai người cấu hình không âm thầm ghi đè nhau *(NFR-07b)*

A và B cùng mở bảng phân quyền trường của Nhóm Sales trên Khách hàng. A lưu trước; khi B lưu, hệ thống nêu A đã thay đổi lúc nào và buộc B xác nhận lại. Cùng lúc C sửa một Quy trình — không ai nhận cảnh báo xung đột với C.

### Kịch bản 10: Hoàn tác cấu hình sai *(BR-10.5)*

Người có quyền ẩn nhầm 20 trường của Khách hàng; người khác có quyền Quản lý phân quyền trường & bố cục hoàn tác từ nhật ký về đúng trạng thái trước; dòng hoàn tác trỏ về dòng gốc. Thử hoàn tác một lượt cũ đã bị lượt mới hơn đè lên thì bị từ chối kèm tên và thời điểm của lượt mới hơn.

### Kịch bản 11: Hàng đợi chưa phân công *(BR-09.3, BR-09.5)*

Quy tắc phân công theo khu vực, "Miền Bắc" có đơn vị tiếp nhận "Kinh doanh – Hà Nội". Mọi người nhận đang nghỉ phép: khách hàng tiềm năng mới vào hàng đợi của đơn vị, người phụ trách đơn vị nhận cảnh báo; nhân viên đơn vị đó nhận việc được; nhân viên đơn vị khác không thấy. Một người nhận bị tạm ngưng không được gán thêm; bản ghi đang mở của họ trả về hàng đợi của quy tắc khi người thực hiện tạm ngưng chọn như vậy.

---

## 7. Nhu cầu nghiệp vụ chưa chốt được phương án

| # | Nhu cầu | Điều cần quyết |
| --- | --- | --- |
| 1 | Doanh nghiệp tự tạo loại dữ liệu tuỳ biến | Có mở năng lực này không, giới hạn số loại theo gói, quan hệ với các loại cốt lõi. Khi chốt phải khai báo đủ: dòng mới trong ma trận quyền ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-24`) và ma trận mặc định của từng vai trò dựng sẵn trên dòng đó ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-29.6`); mức nền của dòng mới khi workspace đã thu hẹp mức nền mặc định ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-34-01`) — không được rộng hơn mức nền hẹp nhất đang dùng; đơn vị của bản ghi theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4; xử lý bản ghi khi người phụ trách tạm ngưng và rời workspace ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-42`, `FEAT-43`); loại bản ghi khi vào hàng đợi (`BR-09.5`). Cho tới khi chốt, không loại dữ liệu nào ngoài năm loại tại Mục 2.2 được tạo |
| 2 | Quy tắc kiểm tra liên trường do doanh nghiệp tự định nghĩa (ví dụ "nếu Loại khách hàng = Doanh nghiệp thì Mã số thuế bắt buộc") | Ngôn ngữ biểu đạt đủ cho người không chuyên và cách chặn biểu thức rủi ro hiệu năng. Ràng buộc điều kiện dựng sẵn theo ngữ cảnh (`BR-05.2`, `BR-07.3`, `BR-09.2`) không thuộc nhu cầu này |
| 3 | Báo cáo "Bản ghi chưa đạt quy tắc kiểm tra hiện hành" để chủ động làm sạch nợ dữ liệu do `BR-04.3` | Phạm vi báo cáo, ai được xem (theo mức Xem của từng loại dữ liệu) |
| 4 | Vòng đời độc lập cho Công ty | Giai đoạn của Công ty là độc lập hay suy ra từ Khách hàng và Cơ hội của nó |
| 5 | Khôi phục trường đã vô hiệu hóa và phân tích tác động chéo trước khi gỡ trường (tới quy trình tự động, mẫu email) | Thời hạn khôi phục. Mọi phương án khôi phục phải đưa trường về trạng thái Ẩn với mọi Nhóm cho tới khi được cấu hình lại, vì cấu hình phân quyền trường của trường đó đã bị gỡ (Nguyên tắc 7 của IAM) |
| 6 | Môi trường thử nghiệm cấu hình và triển khai theo từng Nhóm | Phạm vi cấu hình được thử, cách đưa sang thật; cho tới khi chốt, mọi thay đổi có hiệu lực ngay, rủi ro được giảm bằng xem trước quyền (`BR-03.3`) và hoàn tác (`BR-10.5`) |
| 7 | Điều kiện đóng cấu hình được cho Vé hỗ trợ và các loại ngoài Cơ hội | Thuộc Object Manager hay SRS phân hệ; quan hệ với cam kết chất lượng dịch vụ của [`tickets-srs.md`](./tickets-srs.md) |
| 8 | Bố cục thay đổi theo giai đoạn vòng đời | Trường hiện/ẩn theo giai đoạn có phải là quyền không; nếu có thì thuộc nhóm thay đổi ai thấy gì |

---

## Phụ lục A — Danh mục Khái niệm Nghiệp vụ

*Phụ lục này là mô tả nghiệp vụ, không phải thiết kế dữ liệu, và không mang tính ràng buộc. Ánh xạ sang thuật ngữ kỹ thuật thông dụng chỉ để đội phát triển và QA đối chiếu; khi khác với phần thân, phần thân thắng.*

| Khái niệm nghiệp vụ | Thuật ngữ kỹ thuật thường dùng |
| --- | --- |
| Mã định danh trường (`BR-02.1`) | Tên trường dùng cho lập trình |
| Xoá trường là vô hiệu hóa (`BR-02.3`) | Xoá mềm |
| Hai chiều Mức quyền trên trường / Mức hiển thị giá trị (`BR-03.1`) | Phân quyền mức trường và chính sách che dữ liệu, hai thuộc tính độc lập |
| Hạn chế hơn thắng; vắng mặt không phải cho phép (`BR-03.2`) | Từ chối thắng, áp riêng từng chiều |
| Quy tắc "Đúng định dạng" (`BR-04.1`) | Biểu thức chính quy |
| Từ chối khi không đánh giá được quy tắc (`BR-04.2`) | Đóng khi lỗi; kiểm tra rủi ro biểu thức khi lưu |
| Chỉ kiểm tra khi trường đổi (`BR-04.3`) | Điều kiện "đã thay đổi" |
| Số điện thoại chuẩn quốc tế (`BR-09.1`) | Chuẩn đánh số điện thoại quốc tế có mã quốc gia |
| Thu hẹp không bị chặn, ghi bù (`BR-10.3`) | Ghi nhật ký bất đồng bộ có hàng chờ bền |
| Cảnh báo ghi đè (`NFR-07b`) | Khóa lạc quan theo phiên bản |
| Thay đổi cấu trúc cấu hình đang vận hành (`NFR-10`) | Chuyển đổi dữ liệu và kế hoạch khôi phục |

---

## Phụ lục B — Danh mục Tham số Cấu hình theo Workspace

Phụ lục này là nguồn duy nhất về giá trị mặc định và miền giá trị của tham số do Object Manager khai báo. Tham số do IAM khai báo — mức nền, đơn vị tiếp nhận mặc định theo loại nguồn, điều chỉnh ô vai trò dựng sẵn, thời hạn lưu nhật ký — theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-34-01`, `CFG-35-01`, `CFG-29-02`, `CFG-41-02` (Phụ lục B của tài liệu đó). Mức tự do: **Tự do** — đặt bất kỳ trong miền; **Cố định** — không cấu hình được vì gắn với cam kết toàn vẹn hoặc hiệu năng.

| Mã | Quy tắc | Tham số | Mặc định | Miền giá trị | Thẩm quyền thay đổi | Mức tự do |
| --- | --- | --- | --- | --- | --- | --- |
| `CFG-02-01` | `BR-02.6` | Số trường tuỳ biến đang hoạt động tối đa mỗi loại dữ liệu | 300 | — | — | Cố định (điều kiện biên của `NFR-08`) |
| `CFG-07-01` | `BR-07.4` | Bắt buộc tuần tự, đặt riêng cho từng Quy trình bán hàng | Tắt | Bật / Tắt | Q: Quản lý cấu hình đối tượng | Tự do |
| `CFG-09-01` | `BR-09.1` | Tiêu chí nhận diện trùng cho loại dữ liệu chưa có đặc tả riêng ở SRS phân hệ | Khớp chính xác email | Một hoặc nhiều trường của loại dữ liệu, so khớp chính xác hoặc sau chuẩn hóa | Q: Quản lý cấu hình đối tượng | Tự do |
| `CFG-09-02` | `BR-09.1` | Hành động khi phát hiện trùng lúc nhập tay, cho loại dữ liệu chưa có đặc tả riêng | Cảnh báo, cho lưu khi xác nhận | Cảnh báo / Chặn | Q: Quản lý cấu hình đối tượng | Tự do (nhập tệp và tích hợp luôn từ chối) |
| `CFG-09-03` | `BR-09.2` | Bắt buộc có Người phụ trách khi đóng Cơ hội | Tắt | Bật / Tắt | Q: Quản lý cấu hình đối tượng | Tự do |
| `CFG-09-04` | `BR-09.3` | Cơ chế phân công tự động cho Khách hàng, Công ty, Cơ hội, Vé hỗ trợ (Công việc theo [`tasks-srs.md`](./tasks-srs.md)) | Tắt | Tắt / Xoay vòng / Theo tải / Theo khu vực | Q: Quản lý cấu hình đối tượng; đơn vị tiếp nhận do Người có toàn quyền đặt (`BR-09.5`) | Tự do |

Mọi thay đổi tham số ghi nhật ký theo `BR-10.1`.

---

## Phụ lục C — Nhật ký Mâu thuẫn & Quyết định đã chốt

Phụ lục ghi các mâu thuẫn đã giải quyết và quyết định đã chốt, để lần rà soát sau không lật lại. Nội dung chi tiết nằm tại quy tắc tương ứng.

| # | Mâu thuẫn / câu hỏi | Cách xử lý đã chốt | Nơi có hiệu lực |
| --- | --- | --- | --- |
| C.1 | Che dữ liệu là mức quyền hay cách trình bày | Hai chiều độc lập, hợp nhất riêng từng chiều | `BR-03.1`, `BR-03.2`; ADR-0001 |
| C.2 | Ẩn thắng nhưng Bắt buộc cộng gộp — người thuộc hai Nhóm bị kẹt | Quyền thắng ràng buộc nhập; miễn trừ và gắn cờ, có người nhận trách nhiệm và tự gỡ cờ | `BR-03.2`, `BR-05.5`; ADR-0001 |
| C.3 | Nhóm không cấu hình một trường có được tính là "không hạn chế" | Không; chỉ Nhóm có cấu hình cho chính trường đó tham gia; Bố cục mặc định xét theo từng trường | `BR-03.2` |
| C.4 | Người có toàn quyền có bị phân quyền trường giới hạn | Không, trừ Sàn bắt buộc nêu rõ và che cho tác nhân AI; người chỉ giữ quyền cấu hình phân quyền trường vẫn bị giới hạn | `BR-03.2b` |
| C.5 | Người giữ quyền cấu hình phân quyền trường tự mở trường cho mình | Trần theo quyền thực tế của chính mình, không tự nới rộng Nhóm mình thuộc, không vượt Sàn bắt buộc; thu hẹp luôn được | `BR-03.8`; Nguyên tắc 1, 2 của IAM |
| C.6 | Tác vụ tự động miễn trừ phân quyền trường trở thành đường vòng | Giữ miễn trừ ở bước phân quyền trường theo ADR-0001 nhưng giới hạn trong phạm vi bản ghi của người khởi chạy, cấm chuyển dữ liệu sang nơi bảo vệ thấp hơn (gồm nội dung gửi ra ngoài và tác nhân AI), bắt buộc truy vết | `BR-03.4`; [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`, `BR-40.1` |
| C.7 | Tên "Mức truy cập" của phân quyền trường trùng với "Mức truy cập" của ô trong ma trận IAM | Đổi thành "Mức quyền trên trường" trong tài liệu này | Mục 1.4, `BR-03.1` |
| C.8 | Quyền vào Object Manager gắn cứng cho "Quản trị viên tenant" | Bốn quyền quản trị khai báo vào danh mục quyền IAM, gán được cho bất kỳ vai trò nào; mặc định không vai trò dựng sẵn nào có, trừ quyền xem nhật ký cho Kiểm toán và Kiểm toán quyền | `BR-01.4`, Mục 5 |
| C.9 | Vị trí của loại dữ liệu trong ma trận quyền; loại dữ liệu tuỳ biến | Năm loại cốt lõi là năm dòng, mặc định vai trò dựng sẵn do SRS phân hệ khai báo; loại dữ liệu tuỳ biến chuyển hẳn sang Mục 7 kèm danh sách điều phải khai báo khi chốt | `BR-01.3`, Mục 7 điểm 1 |
| C.10 | Ma trận năng lực theo đối tượng lặp lại và lệch với SRS phân hệ | Năng lực do SRS phân hệ sở hữu khai báo; Object Manager hiển thị đúng khai báo đó | `BR-01.2` |
| C.11 | Trường tuỳ biến chứa dữ liệu nhạy cảm không có khung che | Khai báo nhạy cảm theo khung che chung của IAM; xem đầy đủ trao qua Mức hiển thị; công thức tham chiếu kế thừa che; trường hệ thống không bỏ đánh dấu được | `BR-02.7`; [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-40` |
| C.12 | Nhật ký lỗi chặn cả thao tác thu hẹp quyền trên trường | Phân loại theo tác động: thu hẹp không bị chặn, ghi bù; nới rộng và trung tính đóng khi lỗi; hoàn tác phân loại theo tác dụng của chính nó | `BR-10.3`, `BR-10.5`; ADR-0010, ADR-0003 |
| C.13 | Vô hiệu hóa trường kèm tự động gỡ cấu hình phân quyền trường là thu hẹp hay nới rộng | Thu hẹp: trường biến mất với mọi người nên không ai thấy thêm gì; khôi phục trường (nếu chốt) phải về trạng thái Ẩn | `BR-10.3`; Mục 7 điểm 5 |
| C.14 | Danh sách hiển thị có thuộc nhóm thay đổi ai thấy gì | Không, vì luôn trong mức Xem và tuân phân quyền trường; lý do loại trừ ghi tường minh | `BR-08.5`, `BR-10.3` |
| C.15 | Chỉ nhà cung cấp tra cứu và hoàn tác được cấu hình | Doanh nghiệp tự tra cứu qua quyền Xem nhật ký cấu hình đối tượng và tự hoàn tác qua quyền Quản lý phân quyền trường & bố cục; nhà cung cấp chỉ trong Phiên hỗ trợ | `BR-10.4`, `BR-10.5`, `NFR-02`; [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-08` |
| C.16 | Hàng đợi chưa phân công không có đơn vị, không rõ ai thấy | Áp khung hàng đợi IAM: đơn vị tiếp nhận do Người có toàn quyền đặt; loại bản ghi theo SRS phân hệ; nhận việc là thao tác Gán; người nhận phải thuộc đơn vị tiếp nhận và Đang hoạt động | `BR-09.3`, `BR-09.5`; [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10`, `BR-35.11`, `BR-35.12`, `BR-35.13`, `BR-35.14` |
| C.17 | Chống trùng và phân bổ khách hàng tiềm năng đặc tả ở cả Object Manager và phân hệ Khách hàng | Đặc tả của SRS phân hệ thắng; tham số của Object Manager chỉ cho loại dữ liệu chưa có đặc tả riêng | `BR-09.1`, `BR-09.3` |
| C.18 | Ba điều kiện đóng Thắng có tắt được không | Không; chỉ "bắt buộc có Người phụ trách khi đóng" là tham số, mặc định Tắt | `BR-09.2`, `CFG-09-03` |
| C.19 | Xoá trường đang là điều kiện chặn nghiệp vụ | Chặn xoá kèm danh sách nơi tham chiếu; tham chiếu hiển thị/kiểm tra tự gỡ | `BR-02.5` |
| C.20 | Quy tắc không đánh giá được: bỏ qua hay chặn, và có khoá chết không | Từ chối bản ghi; không chặn màn hình cấu hình; thông báo phân biệt hai tình huống | `BR-04.2` |
| C.21 | Hoàn tác thay đổi cũ khi đã có thay đổi mới hơn; hoàn tác khi giá trị trước là "không có" | Chỉ hoàn tác thay đổi mới nhất cùng mục tiêu; hoàn tác tạo mới là gỡ, hoàn tác gỡ là khôi phục | `BR-10.5` |
| C.22 | Danh sách mở sẵn khi thuộc nhiều Nhóm | Ưu tiên do người cấu hình xếp tường minh; không áp hạn chế thắng | `BR-08.1` |
| C.23 | Hạn mức Nhóm của một người | 20 Nhóm tham gia phân giải phân quyền trường, lý do giải thích được bằng lời | `NFR-08` |
| C.24 | Yêu cầu chuyển đổi dữ liệu và khả năng tương thích của cấu hình cũ gắn với một đợt thay đổi cụ thể | Chuyển thành yêu cầu phi chức năng chung cho mọi thay đổi cấu trúc cấu hình đang vận hành và cho việc diễn giải cấu hình phân quyền trường | `NFR-10`, `NFR-11` |
| C.25 | Hạn chế theo đợt phát hành và lộ trình trong thân tài liệu | Bỏ; điều đã chốt đặc tả tại Mục 3, điều chưa chốt gom ở Mục 7 | Mục 3, Mục 7 |
| C.26 | Phân loại thu hẹp/nới rộng theo chiều thao tác bỏ sót trường hợp tạo cấu hình Nhóm khi Bố cục mặc định chặt hơn | Phân loại theo quyền thực tế trước và sau của từng người bị ảnh hưởng; một người được nới là nới rộng | `BR-10.3` |
| C.27 | Người nhận của phân công tự động không có ô Xem | Bỏ qua người đó; không còn ai thì vào hàng đợi | `BR-09.3`; [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6` |
| C.28 | Công việc vừa nằm trong quy tắc phân công của Object Manager vừa có hàng đợi riêng ở phân hệ Công việc | Gỡ Công việc khỏi `CFG-09-04` và hàng đợi của quy tắc; theo [`tasks-srs.md`](./tasks-srs.md) `BR-13.6` | `BR-09.3`, `BR-09.5` |
| C.29 | Giá trị bị che suy ra được qua tìm kiếm, lọc, sắp xếp, nhóm, báo cáo | Không kênh nào dùng giá trị thật của trường bị che hoặc nhạy cảm chưa được xem đầy đủ; chỉ còn điều kiện "có dữ liệu / trống" | `BR-03.5`, `BR-02.7` |
| C.30 | Bỏ đánh dấu nhạy cảm không có trần | Là nới rộng, chịu trần `BR-03.8` và ghi như nới rộng | `BR-03.8`, `BR-10.3` |
| C.31 | Quy tắc phân công quyết định quyền sở hữu bản ghi nhưng không chịu trần nào | Người nhận phải nằm trong ô Gán của người cấu hình; thay đổi người nhận thuộc nhật ký thay đổi cấu hình quyền | `BR-09.3` |
| C.32 | Lý do Kiểm toán và Kiểm toán quyền mặc định xem nhật ký cấu hình | Nhiệm vụ đưa bằng chứng thay đổi; quyền chỉ đọc nhật ký, không mở dữ liệu bản ghi | `BR-01.4` |
| C.33 | Giá trị bị che suy ra qua điều kiện lọc của danh sách dùng chung, phân đoạn và số đếm xem trước do người khác cấu hình | Điều kiện trên trường bị che với người xem không được áp và không được tính cho họ; màn hình chỉ báo có điều kiện không áp dụng | `BR-03.5`, `BR-08.2` |
| C.34 | Cảnh báo trùng tiết lộ giá trị bị che | Tiêu chí trên trường bị che với người thực hiện không so khớp, không nêu tên; bản ghi vào danh sách rà trùng cho người xem đầy đủ | `BR-09.1` |
| C.35 | Trường công thức chỉ kế thừa che khi nguồn là nhạy cảm | Kế thừa mức hạn chế nhất của mọi trường nguồn, theo từng người | `BR-03.5`, `BR-02.7` |
| C.36 | Lựa chọn người phụ trách của ma trận chuyển đổi né điều kiện của phân công tự động | Cùng điều kiện `BR-09.3`; "kế thừa" và "phân công tự động" chỉ người có Gán Toàn workspace hoặc Người có toàn quyền đặt; ma trận có đơn vị tiếp nhận riêng | `BR-05.3` |
| C.37 | Bớt người nhận bị coi là thu hẹp dù đẩy bản ghi vào hàng đợi cả đơn vị thấy | Phân loại theo tập người thấy bản ghi: mọi thay đổi quy tắc phân công là nới rộng; loại trừ "cấu hình nâng cao" thu hẹp lại còn chống trùng và điều kiện đóng | `BR-10.3`, `BR-09.3` |
| C.38 | Thành viên đơn vị tiếp nhận chỉ tính kiêm nhiệm mức Đầy đủ, lệch IAM | Tính Đơn vị chính và kiêm nhiệm ở mọi mức như [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.11` | `BR-09.3` |
| C.39 | Trần Gán của người cấu hình quy tắc thay đổi sau khi lưu | Kiểm tra lại; vượt trần thì tạm dừng quy tắc, bản ghi vào hàng đợi, thông báo | `BR-09.3` |
| C.40 | Tên loại nguồn của quy tắc phân công trong [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-35-01` | "Quy tắc phân công tự động" và "Ma trận chuyển đổi" | `BR-09.5`, `BR-05.3` |
| C.41 | Câu "vẫn ghi nhật ký, không chặn, ghi bù" đặt sau quy tắc phân công nên đọc như áp cho cả quy tắc phân công | Chuyển lên ngay sau danh sách loại trừ, chủ ngữ là các thay đổi trình bày và quy tắc dữ liệu đã loại trừ; quy tắc phân công là nới rộng, đóng khi lỗi | `BR-10.3` |
| C.42 | Cờ trạng thái đóng "chỉ phục vụ lọc và thống kê" mâu thuẫn với hệ quả nghiệp vụ do phân hệ quy định (ví dụ nhánh Hoàn thành của Công việc) | Cờ không kéo theo dữ liệu bắt buộc; hệ quả khác do SRS phân hệ quy định | `BR-06.1` |
| C.43 | Danh mục Nhóm công việc không có nơi cấu hình trong Object Manager | Thêm vào phạm vi quyền Quản lý cấu hình đối tượng; đang dùng thì chỉ vô hiệu hóa | `BR-01.4`, `BR-06.4` |
