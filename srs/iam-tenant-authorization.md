# SRS — Quản lý Người dùng, Nhóm, Đơn vị Tổ chức & Phân quyền Workspace

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA |
| **Module** | CRM — Phân hệ Quản trị Workspace (Identity & Access Management) |
| **Ngày cập nhật** | 2026-10-02 |
| **Phiên bản** | v5.0 (Chuẩn hóa Nghiệp vụ Thuần túy — thay thế v4) |
| **Neo mã nguồn** | Chưa xác định — tài liệu đặc tả trạng thái nghiệp vụ mục tiêu, không neo vào một phiên bản triển khai cụ thể |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`onboarding-srs.md`](./onboarding-srs.md), [`billing-subscription-srs.md`](./billing-subscription-srs.md), [`contacts-srs.md`](./contacts-srs.md), [`deals-pipeline-srs.md`](./deals-pipeline-srs.md), [`object-manager-srs.md`](./object-manager-srs.md), [`campaigns-srs.md`](./campaigns-srs.md), [ADR-0001](../docs/adr/0001-group-policy-conflict-resolution.md), [ADR-0002](../docs/adr/0002-delegated-grant-authority-ceiling-exception.md), [ADR-0003](../docs/adr/0003-permission-config-audit-log-fail-closed.md), [ADR-0007](../docs/adr/0007-record-sharing-and-permission-precedence-contract.md), [ADR-0009](../docs/adr/0009-access-level-per-action-and-record-type.md), [ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md) |

## Ghi chú về phiên bản v5.0

Phiên bản này viết lại toàn bộ tài liệu theo đúng vai trò của một SRS nghiệp vụ. Nội dung nghiệp vụ đã chốt của v4 được giữ về bản chất. Thay đổi nằm ở bốn điểm:

1. **Tài liệu là chuẩn, không phải bản ghi chép hiện trạng.** Tài liệu đặc tả trạng thái nghiệp vụ mục tiêu (To-Be). Mọi quy tắc trong Mục 3 đều là yêu cầu bắt buộc như nhau. Nơi nào hệ thống làm khác tài liệu, hệ thống phải được sửa theo tài liệu. Tài liệu không dùng nhãn trạng thái triển khai; nhu cầu chưa chốt được phương án gom tại Mục 7.
2. **Ranh giới với [`onboarding-srs.md`](./onboarding-srs.md) được làm rõ.** Các bước đăng ký, tên miền phụ, chuỗi khởi tạo, dữ liệu mẫu và dùng thử thuộc tài liệu đó. Tài liệu này chỉ giữ các quy tắc về Chủ sở hữu, danh tính và bất biến phân quyền. Vì vậy Nhóm A được tổ chức lại; mã `FEAT-09` trở đi giữ nguyên để các tài liệu khác dẫn chiếu không bị lệch.
3. **Bổ sung vòng đời nhân sự và kiểm soát tuân thủ:** tạm ngưng thành viên, rời workspace có bàn giao, thao tác hàng loạt, Quyền Ủy thác, tách biệt nhiệm vụ, chức danh trách nhiệm, truy cập hỗ trợ của nhà cung cấp, khôi phục quyền sở hữu, rà soát quyền định kỳ, báo cáo quyền cho kiểm toán, liên kết mời và làm việc trên nhiều workspace (`FEAT-07`, `FEAT-08`, `FEAT-42` – `FEAT-51`). Đồng thời bổ sung các quy tắc giúp doanh nghiệp nhỏ thiết lập đội ngũ nhanh: chế độ Cơ bản của vai trò, vai trò gợi ý theo đơn vị, mời nhiều email một lần.
4. **Mọi giá trị doanh nghiệp có thể muốn khác nhau là tham số cấu hình** tại Phụ lục B; mỗi tính năng có bảng Tiêu chí Chấp nhận; các mâu thuẫn đã giải quyết ghi tại Phụ lục C.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả nghiệp vụ cho toàn bộ việc quản trị con người và quyền hạn trong một workspace: ai là Chủ sở hữu, ai được vào workspace, mỗi người thuộc đội nhóm và đơn vị nào, mỗi người được làm gì trên loại dữ liệu nào và ở phạm vi rộng tới đâu, quyền được cấp – thu hồi – rà soát ra sao, và mọi thay đổi quyền được lưu vết thế nào — để mọi thành viên chỉ thấy và làm được đúng phần việc được giao.

### 1.2 Phạm vi

**Trong phạm vi — 11 nhóm chức năng:**

| Nhóm | Nội dung |
| --- | --- |
| A. Chủ sở hữu & Cấu hình nền của Workspace | Xác lập Chủ sở hữu, bất biến phân quyền khi workspace sẵn sàng, sức khỏe cấu hình, cấu hình chung, trần quyền theo gói, chuyển nhượng và khôi phục quyền sở hữu, truy cập hỗ trợ của nhà cung cấp |
| B. Người dùng | Mời và vòng đời lời mời, liên kết mời và tự gia nhập, tra cứu, cập nhật, đổi cấp bậc, tạm ngưng, rời workspace có bàn giao, thao tác hàng loạt, xem quyền hiệu lực, tuỳ chỉnh cá nhân, làm việc trên nhiều workspace |
| C. Nhóm | Tạo và cấu hình nhóm, thành viên nhóm, xem trước quyền nhóm, xoá nhóm |
| D. Đơn vị tổ chức | Cây tổ chức, di chuyển, xoá đơn vị |
| E. Vai trò & Quyền | Danh mục quyền, tạo – sửa – lịch sử – xoá vai trò, vai trò dựng sẵn, Quyền Ủy thác, tách biệt nhiệm vụ, chức danh trách nhiệm |
| F. Cấp quyền tạm thời | Yêu cầu, phê duyệt, thu hồi, báo cáo kiểm định |
| G. Phạm vi hiển thị dữ liệu | Mức nền của workspace, quy tắc phân giải phạm vi |
| H. Chính sách truy cập nâng cao | Chính sách theo điều kiện, mô phỏng, lịch sử |
| I. Phân quyền theo bản ghi | Cấp, chặn, xem quyền trên một bản ghi |
| J. Che dữ liệu nhạy cảm | Khung quy tắc che dữ liệu dùng chung cho mọi phân hệ |
| K. Nhật ký, Rà soát & Báo cáo quyền | Nhật ký thay đổi quyền, rà soát quyền định kỳ, báo cáo quyền cho kiểm toán |

**Ngoài phạm vi:**

- Đăng nhập và xác thực (chính sách mật khẩu, xác thực nhiều lớp, đăng nhập một lần, quản lý phiên đăng nhập nói chung). Tài liệu này chỉ quy định **khi nào** một phiên làm việc trong workspace phải bị chấm dứt vì quyền thay đổi.
- Các bước đăng ký tự phục vụ, tên miền phụ, chuỗi khởi tạo kỹ thuật, dữ liệu mẫu, dùng thử và cổng khởi tạo nội bộ — thuộc [`onboarding-srs.md`](./onboarding-srs.md).
- Gói dịch vụ, giới hạn số người dùng theo gói, thanh toán và Người phụ trách thanh toán — thuộc [`billing-subscription-srs.md`](./billing-subscription-srs.md). Tài liệu này chỉ quy định điểm chạm (chặn mời khi hết hạn mức người dùng, không gỡ Người phụ trách thanh toán khi chưa có người thay).
- Bàn giao bản ghi nghiệp vụ (khách hàng, cơ hội, vé hỗ trợ) khi nhân sự thay đổi — quy tắc bàn giao của từng loại dữ liệu thuộc phân hệ sở hữu nó (ví dụ [`contacts-srs.md`](./contacts-srs.md) `FEAT-34`). Tài liệu này quy định quy trình rời workspace bắt buộc gọi tới các bước bàn giao đó.
- Phân quyền trường (ai xem/sửa được trường nào) — thuộc [`object-manager-srs.md`](./object-manager-srs.md) và ADR-0001. Tài liệu này quy định vị trí của nó trong thứ tự hợp nhất quyền (`BR-39.6`) và khung che dữ liệu (`FEAT-40`).
- Nhật ký vận hành nội bộ của nhà cung cấp không gắn với một workspace cụ thể. Mọi thao tác của nhà cung cấp **tác động lên một workspace** thì thuộc phạm vi và được ghi vào nhật ký của chính workspace đó (`FEAT-08`, `BR-05.3`).

### 1.3 Đối tượng đọc

- Business Analyst / Product Owner: nguồn chuẩn về nghiệp vụ phân quyền trước khi đề xuất thay đổi.
- QA: căn cứ viết test case từ các bảng Tiêu chí Chấp nhận và Mục 6.
- Đội triển khai (Customer Success/Support): hiểu rõ khả năng cấu hình và giới hạn khi tư vấn khách hàng.
- Kỹ sư phát triển: hiểu ý định nghiệp vụ; chi tiết triển khai kỹ thuật không nằm trong tài liệu này.
- Kiểm toán nội bộ / Bảo mật thông tin của khách hàng: căn cứ đánh giá năng lực kiểm soát truy cập.

### 1.4 Thuật ngữ nghiệp vụ

| Thuật ngữ | Ý nghĩa |
| --- | --- |
| **Workspace** | Không gian làm việc riêng của một khách hàng doanh nghiệp, dữ liệu tách biệt hoàn toàn với workspace khác. Tên gọi tương đương khi trao đổi với đội phát triển là **Tenant**. |
| **Tài khoản người dùng** | Danh tính của một con người trên toàn hệ thống, gắn với đúng một địa chỉ email. Một tài khoản có thể là thành viên của nhiều workspace. |
| **Thành viên** | Quan hệ giữa một tài khoản và một workspace. Mọi thuộc tính phân quyền (cấp bậc, vai trò, nhóm, đơn vị, quản lý trực tiếp, trạng thái) gắn với thành viên, không gắn với tài khoản. |
| **Trạng thái thành viên** | **Đang chờ chấp nhận** (đã được mời, chưa chấp nhận; gồm lời mời đang Chờ gửi, `BR-09.11`) / **Chờ duyệt gia nhập** (tự xin vào qua liên kết hoặc tên miền, chờ người có thẩm quyền duyệt) / **Đang hoạt động** / **Tạm ngưng** (giữ nguyên cấu hình, không truy cập được) / **Đã rời** (không còn là thành viên). |
| **Cấp bậc thành viên** | Trục xác định mức đặc quyền của một thành viên: **Chủ sở hữu** / **Quản trị viên** / **Thành viên**. Độc lập với Vai trò. |
| **Chủ sở hữu (Owner)** | Thành viên duy nhất chịu trách nhiệm cao nhất về workspace. Mỗi workspace luôn có đúng một Chủ sở hữu. Toàn quyền, trừ các Sàn bắt buộc và yêu cầu người duyệt thứ hai. Chỉ thay đổi qua Chuyển nhượng (`FEAT-06`) hoặc Khôi phục quyền sở hữu (`FEAT-07`). |
| **Quản trị viên (Admin)** | Cấp bậc toàn quyền có thể trao và thu hồi, cùng phạm vi quyền như Chủ sở hữu, trừ các thao tác dành riêng cho Chủ sở hữu được nêu rõ trong tài liệu. |
| **Người có toàn quyền** | Cách gọi chung cho Chủ sở hữu và Quản trị viên. |
| **Vai trò (Role)** | Tập hợp quyền gán được cho thành viên hoặc nhóm, gồm **ma trận quyền trên dữ liệu** và **các quyền quản trị** (dạng có/không). Có vai trò dựng sẵn và vai trò tự tạo. |
| **Ma trận quyền** | Bảng **loại dữ liệu × thao tác** của một vai trò. Mỗi ô là một cặp, ví dụ (Khách hàng, Sửa), mang một Mức truy cập. |
| **Mức truy cập** | Giá trị của một ô, từ hẹp đến rộng: **Không có** / **Chỉ của mình** / **Đơn vị của mình** / **Đơn vị và các đơn vị con** / **Toàn workspace**. Riêng thao tác Tạo chỉ có hai giá trị **Có** / **Không có**. Định nghĩa chính xác từng mức tại `FEAT-34`. |
| **Quyền quản trị** | Quyền dạng có/không, không gắn với bản ghi, ví dụ Quản lý vai trò, Xem nhật ký quyền. Danh mục tại `FEAT-24`. |
| **Năng lực quyền hạn của một người** | Toàn bộ quyền hiệu lực của người đó: mức hiệu lực ở từng ô và các quyền quản trị đang có. Dùng làm **trần** khi người đó cấp quyền cho người khác. Khi làm trần cho một lượt cấp **vĩnh viễn** (vai trò, nhóm, lời mời, liên kết mời, lượt cấp trên bản ghi), năng lực **không tính** quyền tạm thời đang giữ. |
| **Trần quyền của workspace** | Tập quyền mà gói dịch vụ và các tính năng mở rộng đang bật cho phép workspace sử dụng (`FEAT-05`). |
| **Sàn bắt buộc** | Ràng buộc không cấu hình vượt được qua bất kỳ vai trò, nhóm, lượt cấp hay điều chỉnh nào — **kể cả với Người có toàn quyền** khi sàn đó nêu rõ. Sàn do phân hệ nghiệp vụ khai báo trong SRS của mình, hoặc do doanh nghiệp tự đặt qua tham số có mức "Có sàn bắt buộc". Hệ thống thực thi sàn nhất quán; hệ thống không tự duy trì quy định pháp lý của bất kỳ quốc gia nào. |
| **Quản lý trực tiếp** | Người được khai báo là cấp trên trực tiếp của một thành viên trong cùng workspace. Chuỗi quản lý trực tiếp xác định **cấp dưới** (trực tiếp và gián tiếp) của một người. |
| **Người phụ trách đơn vị** | Người được khai báo là phụ trách chính hoặc đồng phụ trách của một đơn vị tổ chức. Khác với Quản lý trực tiếp: một người có thể phụ trách một đơn vị mà không là quản lý trực tiếp của từng thành viên trong đó. |
| **Đơn vị chính / Đơn vị kiêm nhiệm** | Mỗi thành viên có tối đa một Đơn vị chính và có thể có thêm Đơn vị kiêm nhiệm (có hoặc không có ngày kết thúc). |
| **Nhóm** | Tập hợp thành viên do doanh nghiệp tạo để cấp vai trò theo tập thể và để gán cấu hình chung của [`object-manager-srs.md`](./object-manager-srs.md) (phân quyền trường, danh sách hiển thị). Nhóm trong tài liệu này và "Nhóm quyền" trong Object Manager là **cùng một thực thể**. Nhóm không phải sơ đồ tổ chức. |
| **Đơn vị tổ chức** | Nút trong sơ đồ tổ chức dạng cây (phòng, ban, chi nhánh, đội). |
| **Bản ghi của mình** | Bản ghi mà người đó đang là Người phụ trách. |
| **Bản ghi thuộc một đơn vị** | Mặc định, bản ghi thuộc đồng thời Đơn vị chính và mọi Đơn vị kiêm nhiệm mức Đầy đủ của Người phụ trách hiện tại (kiêm nhiệm mức Chỉ xem không làm bản ghi của người đó thuộc đơn vị kiêm nhiệm), và chuyển đơn vị theo ngay khi đổi Người phụ trách. Ngoại lệ theo `BR-35.10`, `BR-35.11`: **bản ghi công việc** của hàng đợi (ví dụ vé hỗ trợ) thuộc đơn vị tiếp nhận suốt vòng đời; **bản ghi chờ phân công** (ví dụ khách hàng tiềm năng từ biểu mẫu) thuộc đơn vị tiếp nhận cho tới khi có Người phụ trách; bản ghi chưa có người phụ trách không qua hàng đợi thuộc Đơn vị chính của người tạo; bản ghi giữ chỗ cho người chưa chấp nhận lời mời thuộc Đơn vị chính ghi trong lời mời; bản ghi công việc đổi đơn vị khi được chuyển hàng đợi (`BR-35.14`). |
| **Mức nền của workspace** | Mức dùng cho một ô khi vai trò không khai báo ô đó, và chế độ "công khai đọc" theo loại dữ liệu (`FEAT-34`). |
| **Quyền tạm thời** | Một vai trò được cấp thêm có thời hạn, có lý do và phải được phê duyệt (Nhóm F). |
| **Quyền Ủy thác** | Quyền quản trị đặc biệt do Người có toàn quyền cấp, kèm danh sách vai trò được giao, cho phép người giữ gán các vai trò đó (và thêm vào các nhóm mang chúng) cho người khác mà không bị giới hạn bởi năng lực của chính mình (`FEAT-45`). Cấp vĩnh viễn, không cần phê duyệt; khác hẳn Quyền tạm thời. |
| **Chức danh trách nhiệm** | Trách nhiệm nghiệp vụ được chỉ định cho một thành viên, không phải vai trò và không tự cấp quyền: Người phụ trách Bảo vệ Dữ liệu, Người phụ trách thanh toán, Người phụ trách bảo mật (`FEAT-47`). |
| **Người duyệt thứ hai** | Người thứ hai, khác người thực hiện, phải xác nhận một thao tác nhạy cảm trước khi thao tác có hiệu lực (`BR-47.3`). |
| **Chính sách truy cập** | Luật theo điều kiện thuộc tính (của người, của bản ghi, của thời điểm) để chặn thêm hoặc nới phạm vi trên một thao tác (Nhóm H). |
| **Quyền trên bản ghi** | Lượt cấp hoặc lượt chặn cho một bản ghi cụ thể tới một người hoặc một nhóm (Nhóm I). |
| **Thu hẹp quyền / Nới rộng quyền** | Thu hẹp: thao tác chỉ làm giảm điều ai đó được làm hoặc được thấy. Nới rộng: thao tác làm tăng. Thao tác vừa thu hẹp vừa nới rộng được coi là nới rộng. Gỡ một hạn chế (gỡ lượt chặn, tắt hoặc xoá chính sách Từ chối, gỡ khỏi nhóm đang mang hạn chế) là nới rộng. **Trung tính:** thay đổi cấu hình quyền không làm tăng hay giảm điều ai được làm tại thời điểm thay đổi, ví dụ đổi thời hạn mặc định của lời mời hay đổi chế độ duyệt. |
| **Phiên hỗ trợ của nhà cung cấp** | Khoảng thời gian có giới hạn trong đó nhân sự vận hành nền tảng được phép vào một workspace (`FEAT-08`). **Phiên triển khai** là loại phiên riêng sau kích hoạt, chỉ để cấu hình (`BR-08.7`). |

### 1.5 Tài liệu tham khảo

- [`CONTEXT.md`](../CONTEXT.md) — glossary dùng chung, mục "IAM & Phân quyền Workspace".
- [ADR-0001](../docs/adr/0001-group-policy-conflict-resolution.md) — phân giải xung đột phân quyền trường giữa các nhóm.
- [ADR-0002](../docs/adr/0002-delegated-grant-authority-ceiling-exception.md) — Quyền Ủy thác.
- [ADR-0003](../docs/adr/0003-permission-config-audit-log-fail-closed.md) và [ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md) — nhật ký thay đổi cấu hình quyền đóng khi lỗi và ngoại lệ cho thao tác thu hồi.
- [ADR-0007](../docs/adr/0007-record-sharing-and-permission-precedence-contract.md) — chia sẻ bản ghi và thứ tự hợp nhất quyền.
- [ADR-0009](../docs/adr/0009-access-level-per-action-and-record-type.md) — mức truy cập theo từng thao tác trên từng loại dữ liệu.

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà phân hệ giải quyết

1. **Lộ dữ liệu chéo phòng ban:** không có phạm vi dữ liệu rõ ràng, nhân viên kinh doanh thấy khách hàng của đội khác, hỗ trợ viên sửa được cơ hội kinh doanh.
2. **Leo thang quyền:** người được giao việc tạo tài khoản tự cấp cho mình hoặc người thân quen nhiều quyền hơn mức được phép.
3. **Quyền tồn dư sau thay đổi nhân sự:** người nghỉ việc, chuyển phòng, nghỉ dài hạn vẫn giữ quyền; bản ghi họ phụ trách bị bỏ ngỏ.
4. **Không chứng minh được tuân thủ:** khi kiểm toán hỏi "ai có quyền gì, ai đã đổi quyền của ai, đã rà soát quyền chưa", doanh nghiệp không có bằng chứng.
5. **Cấu hình quyền cứng nhắc:** mỗi doanh nghiệp có cơ cấu, quy mô và chính sách khác nhau; quy tắc cố định buộc họ chọn giữa cấp thừa quyền và không làm được việc.

### 2.2 Vai trò người dùng

**Vai trò thao tác (là các cột của Ma trận tại Mục 5):**

| Vai trò | Trách nhiệm nghiệp vụ |
| --- | --- |
| **Nhân sự vận hành nền tảng** | Đội ngũ của nhà cung cấp. Bật/tắt tính năng mở rộng, xử lý khôi phục quyền sở hữu, khoá tài khoản trên toàn hệ thống, vào workspace **chỉ** qua Phiên hỗ trợ (`FEAT-08`). Không có quyền nghiệp vụ trong workspace ngoài phiên đó. |
| **Chủ sở hữu** | Chịu trách nhiệm cao nhất về workspace; toàn quyền; thực hiện các thao tác dành riêng: chuyển nhượng quyền sở hữu, chọn chế độ truy cập hỗ trợ của nhà cung cấp (`CFG-08-01`), đặt các tham số dành riêng cho Chủ sở hữu tại Phụ lục B. |
| **Quản trị viên** | Toàn quyền quản trị người dùng, nhóm, đơn vị, vai trò, chính sách và nhật ký, cấp và chấm dứt phiên hỗ trợ; trừ các thao tác dành riêng cho Chủ sở hữu. |
| **Thành viên được cấp quyền quản trị** | Thành viên thường được giao một hoặc vài quyền quản trị cụ thể (ví dụ nhân sự HR được Mời người dùng và giữ Quyền Ủy thác; trưởng phòng được Phê duyệt quyền tạm thời). Chỉ làm được đúng phần quyền đó, luôn trong trần năng lực của mình. |
| **Người phê duyệt** | Thành viên giữ quyền Phê duyệt quyền tạm thời, hoặc người được giao rà soát trong một đợt rà soát quyền (`FEAT-48`). |
| **Người quản lý / Người phụ trách đơn vị** | Nhận nhiệm vụ rà soát quyền của cấp dưới hoặc của đơn vị, nhận bàn giao khi nhân sự rời đi, nhận thông báo liên quan tới cấp dưới. |
| **Thành viên** | Người dùng thông thường; tự đặt tuỳ chỉnh cá nhân, xem quyền hiệu lực của chính mình, gửi yêu cầu quyền tạm thời nếu được phép. |
| **Hệ thống** | Thực hiện tự động: hết hạn lời mời, hết hạn quyền tạm thời, tự tạm ngưng theo cấu hình, nhắc rà soát, dọn quyền mồ côi, chấm dứt phiên khi mất quyền. |

**Chủ thể chịu tác động (không có cột riêng trong Ma trận):**

| Chủ thể | Ghi chú |
| --- | --- |
| **Tác nhân AI** | Trợ lý dùng mô hình AI để đọc, tóm tắt, gợi ý hay truy vấn dữ liệu thay cho người dùng. Không bao giờ được xem dữ liệu nhạy cảm ở dạng đầy đủ (`BR-40.1`). Bot hội thoại trả lời theo kịch bản cố định, không đưa dữ liệu cho mô hình AI, không phải Tác nhân AI; bot có bước dùng mô hình AI là Tác nhân AI ở bước đó. |
| **Tiến trình chạy thay người dùng** | Gửi chiến dịch, quy tắc tự động hóa, nhập/xuất hàng loạt. Chỉ chạm tới dữ liệu trong quyền hiện có của người khởi chạy (`BR-35.8`). |

### 2.3 Quy ước thời gian nghiệp vụ

Mọi mốc thời gian nghiệp vụ trong tài liệu này — hết hạn lời mời, hết hạn quyền tạm thời, hạn chờ phê duyệt, hạn chuyển nhượng quyền sở hữu, ngày tự kích hoạt lại, điều kiện thời gian của chính sách truy cập (ví dụ "trong giờ hành chính"), hạn của đợt rà soát quyền, thời hạn lưu nhật ký — được xác định theo **múi giờ và lịch làm việc của workspace** (`FEAT-04`), không theo múi giờ máy chủ, không theo múi giờ cá nhân của từng người.

- Thời hạn tính bằng **giờ** trôi liên tục kể từ thời điểm sự kiện.
- Thời hạn tính bằng **ngày** kết thúc lúc 23:59:59 của ngày cuối cùng theo múi giờ workspace. Ví dụ quyền tạm thời 7 ngày cấp lúc 10:00 ngày 01 hết hiệu lực lúc 23:59:59 ngày 08.
- Thời hạn tính bằng **tháng** hoặc **năm** quy đổi 1 tháng = 30 ngày, 1 năm = 365 ngày, để không phát sinh ca 29/02 hay tháng ngắn.
- "Giờ hành chính" và "ngày làm việc" lấy theo lịch làm việc mặc định của workspace.
- Mọi thời điểm hiển thị cho người dùng theo múi giờ cá nhân của họ (`FEAT-16`), nhưng thời điểm hết hạn được tính theo múi giờ workspace.

**Lý do nghiệp vụ:** một quyền tạm thời phải hết hạn cùng một lúc với mọi người liên quan — người được cấp, người phê duyệt và kiểm toán viên. Nếu tính theo múi giờ cá nhân, cùng một quyền sẽ "hết hạn" ở ba thời điểm khác nhau và không nghiệm thu được.

### 2.4 Nguyên tắc nghiệp vụ nền tảng

**Nguyên tắc 1 — Không ai cấp được thứ mình không có.** Mọi thao tác làm tăng tập bản ghi hoặc tập thao tác của bất kỳ ai — qua vai trò, nhóm, lời mời, liên kết mời, quyền tạm thời, khôi phục phiên bản, mức nền, chính sách Cho phép, gỡ hạn chế, người phụ trách đơn vị, quản lý trực tiếp, lượt cấp trên bản ghi, bàn giao — chỉ được thực hiện trong năng lực quyền hạn của người thực hiện, xét trên đúng ô và tập bản ghi bị ảnh hưởng; năng lực dùng làm trần cho lượt cấp vĩnh viễn không tính quyền tạm thời (Mục 1.4). Thao tác nới rộng mà không xét được trên tập bản ghi (mức nền, công khai đọc, phụ trách đơn vị xem toàn nhánh) chỉ Người có toàn quyền thực hiện. Ngoại lệ duy nhất là Quyền Ủy thác, giới hạn trong danh sách vai trò được giao (`FEAT-45`).

**Nguyên tắc 2 — Không ai tự cấp quyền cho chính mình.** Mọi thao tác mà kết quả làm nới rộng quyền của chính người thực hiện — trực tiếp hay gián tiếp (qua nhóm mình thuộc, đơn vị mình phụ trách, cấp dưới mình nhận) — bị từ chối, bất kể người đó giữ quyền gì. Thu hẹp quyền của chính mình thì được phép. Chủ sở hữu vốn toàn quyền nên nguyên tắc này không có tác dụng với họ. Ngoại lệ tường minh duy nhất: **nhận việc từ hàng đợi** (`BR-35.11`).

**Nguyên tắc 3 — Thu hồi luôn đi nhanh hơn cấp.** Mọi thu hẹp quyền có hiệu lực ngay từ thao tác kế tiếp của người bị ảnh hưởng. Các sự kiện làm mất quyền truy cập nghiêm trọng (danh sách tại `BR-11.3`) chấm dứt ngay phiên làm việc trong workspace. Thao tác thu hồi không bao giờ bị chặn bởi sự cố của một cơ chế phụ trợ như nhật ký (`BR-41.6`).

**Nguyên tắc 4 — Quyền là cấu hình chung, không gắn cứng cho một vai trò có tên.** Mọi năng lực đều là giá trị của một ô hoặc một quyền quản trị mà vai trò bất kỳ đặt được. Vai trò dựng sẵn chỉ mang giá trị mặc định.

**Nguyên tắc 5 — Mọi thay đổi ai-được-làm-gì và ai-thấy-gì đều có vết.** Mỗi thay đổi ghi lại ai làm, lúc nào, giá trị trước và sau, lý do nếu có (`FEAT-41`).

**Nguyên tắc 6 — Không có quyền mồ côi.** Khi gốc của một quyền biến mất (vai trò bị xoá, nhóm bị xoá, thành viên rời đi, người tạo liên kết mời mất năng lực, người phê duyệt bị chặn trước khi yêu cầu đủ phê duyệt), mọi quyền mọc ra từ gốc đó được dọn ngay, không chờ rà soát phát hiện.

**Nguyên tắc 7 — Khi không chắc, đóng.** Lỗi khi tính quyền, thao tác không xác định được thuộc ô nào, điều kiện không đánh giá được của một luật cho phép — đều dẫn tới kết quả hẹp hơn, không bao giờ rộng hơn.

**Nguyên tắc 8 — Đơn giản mặc định, nâng cao khi cần.** Doanh nghiệp nhỏ phải thiết lập được đội ngũ chỉ với thành viên, đơn vị, nhóm và vai trò dựng sẵn ở chế độ Cơ bản; các công cụ nâng cao luôn sẵn có nhưng không chắn đường (`BR-24.3`). Mọi lựa chọn sẵn đều hiển thị và đổi được, không có mặc định ẩn.

### 2.5 Bảng tổng hợp tính năng nghiệp vụ

| Nhóm | Mã | Tên tính năng nghiệp vụ |
| --- | --- | --- |
| **A. Chủ sở hữu & Cấu hình nền** | `FEAT-01` | Xác lập Chủ sở hữu & danh tính khi workspace được tạo |
| | `FEAT-02` | Bất biến phân quyền khi workspace sẵn sàng |
| | `FEAT-03` | Kiểm tra sức khỏe cấu hình phân quyền |
| | `FEAT-04` | Cấu hình chung của workspace (ngôn ngữ, múi giờ, lịch làm việc) |
| | `FEAT-05` | Trần quyền theo gói dịch vụ & tính năng mở rộng |
| | `FEAT-06` | Chuyển nhượng quyền sở hữu |
| | `FEAT-07` | Khôi phục quyền sở hữu khi Chủ sở hữu không còn khả dụng |
| | `FEAT-08` | Truy cập hỗ trợ có kiểm soát của nhà cung cấp |
| **B. Người dùng** | `FEAT-09` | Mời thành viên & vòng đời lời mời |
| | `FEAT-10` | Xem & tra cứu thành viên |
| | `FEAT-11` | Cập nhật thông tin, vị trí & vai trò của thành viên |
| | `FEAT-12` | Đổi cấp bậc Quản trị viên ⇄ Thành viên |
| | `FEAT-13` | Gỡ thành viên khỏi workspace & xoá tài khoản |
| | `FEAT-14` | Đặt lại mật khẩu & khoá tài khoản toàn hệ thống |
| | `FEAT-15` | Xem quyền hiệu lực & xem trước thay đổi quyền |
| | `FEAT-16` | Tuỳ chỉnh cá nhân |
| | `FEAT-42` | Tạm ngưng & kích hoạt lại thành viên |
| | `FEAT-43` | Rời workspace có bàn giao (quy trình nghỉ việc) |
| | `FEAT-44` | Thao tác hàng loạt trên thành viên |
| | `FEAT-50` | Liên kết mời & tự gia nhập theo tên miền email |
| | `FEAT-51` | Làm việc trên nhiều workspace & cách ly phiên theo workspace |
| **C. Nhóm** | `FEAT-17` | Tạo & cấu hình nhóm (gồm phân cấp nhóm) |
| | `FEAT-18` | Quản lý thành viên nhóm |
| | `FEAT-19` | Xem trước quyền của nhóm |
| | `FEAT-20` | Xoá nhóm |
| **D. Đơn vị tổ chức** | `FEAT-21` | Xây dựng & quản lý cây tổ chức |
| | `FEAT-22` | Di chuyển đơn vị trong cây |
| | `FEAT-23` | Xoá đơn vị tổ chức |
| **E. Vai trò & Quyền** | `FEAT-24` | Danh mục quyền của workspace |
| | `FEAT-25` | Tạo & sao chép vai trò |
| | `FEAT-26` | Cập nhật vai trò |
| | `FEAT-27` | Lịch sử phiên bản & khôi phục vai trò |
| | `FEAT-28` | Xoá vai trò |
| | `FEAT-29` | Vai trò dựng sẵn & điều chỉnh ô của vai trò dựng sẵn |
| | `FEAT-45` | Cấp & thu hồi Quyền Ủy thác |
| | `FEAT-46` | Quy tắc tách biệt nhiệm vụ |
| | `FEAT-47` | Chức danh trách nhiệm & người duyệt thứ hai |
| **F. Cấp quyền tạm thời** | `FEAT-30` | Yêu cầu cấp quyền tạm thời |
| | `FEAT-31` | Phê duyệt / từ chối yêu cầu |
| | `FEAT-32` | Thu hồi, hết hạn & dọn quyền tạm thời |
| | `FEAT-33` | Báo cáo kiểm định quyền tạm thời |
| **G. Phạm vi hiển thị dữ liệu** | `FEAT-34` | Mức truy cập & mức nền của workspace |
| | `FEAT-35` | Quy tắc phân giải phạm vi |
| **H. Chính sách truy cập nâng cao** | `FEAT-36` | Tạo & quản lý chính sách truy cập theo điều kiện |
| | `FEAT-37` | Mô phỏng & kiểm thử chính sách |
| | `FEAT-38` | Lịch sử phiên bản & khôi phục chính sách |
| **I. Phân quyền theo bản ghi** | `FEAT-39` | Cấp / chặn / xem quyền trên một bản ghi |
| **J. Che dữ liệu nhạy cảm** | `FEAT-40` | Khung quy tắc che dữ liệu nhạy cảm |
| **K. Nhật ký, Rà soát & Báo cáo** | `FEAT-41` | Nhật ký thay đổi quyền & quyết định truy cập |
| | `FEAT-48` | Rà soát quyền định kỳ |
| | `FEAT-49` | Báo cáo quyền phục vụ kiểm toán |

**Tổng kết phạm vi:** 51 tính năng, tất cả là yêu cầu bắt buộc. Mã `FEAT-09` – `FEAT-41` giữ ổn định để các tài liệu khác dẫn chiếu không lệch; các tính năng bổ sung mang mã `FEAT-42` – `FEAT-51` và được trình bày trong nhóm theo nội dung nghiệp vụ, không theo thứ tự mã.

### 2.6 Mục tiêu kinh doanh & Chỉ số thành công

Đo tại mốc 90 ngày kể từ khi đưa vào vận hành trên từng workspace; Product Owner của phân hệ tổng hợp hằng tháng.

| Mã | Vấn đề (Mục 2.1) | Chỉ số | Mục tiêu | Tính năng đóng góp |
| --- | --- | --- | --- | --- |
| `KPI-01` | 1 | Tỷ lệ thành viên Đang hoạt động có Đơn vị chính và ít nhất một vai trò | ≥ 98% | `FEAT-03`, `FEAT-09`, `FEAT-11` |
| `KPI-02` | 3 | Tỷ lệ lượt rời workspace được hoàn tất đủ các bước bàn giao trước khi gỡ | 100% | `FEAT-43` |
| `KPI-03` | 3 | Số quyền tạm thời còn hiệu lực sau thời hạn | = 0 | `FEAT-32`, `FEAT-33` |
| `KPI-04` | 4 | Tỷ lệ mục rà soát được quyết định đúng hạn (workspace đã bật rà soát định kỳ) | ≥ 95% | `FEAT-48` |
| `KPI-05` | 2 | Số lượt nới rộng quyền vượt trần năng lực của người thực hiện được ghi nhận trong nhật ký | = 0 | `FEAT-09`, `FEAT-11`, `FEAT-17`, `FEAT-25`, `FEAT-45` |
| `KPI-06` | 5 | Tỷ lệ workspace phải tạo vai trò tự tạo trong 14 ngày đầu vì vai trò dựng sẵn không đáp ứng (càng thấp càng tốt) | ≤ 15% | `FEAT-25`, `FEAT-29` |
| `KPI-07` | 5 | Trung vị thời gian từ khi workspace Sẵn sàng tới lời mời đầu tiên được gửi | ≤ 10 phút | `FEAT-09`, `FEAT-21`, `FEAT-50` |
| `KPI-08` | 5 | Tỷ lệ lời mời được chấp nhận trong 72 giờ | ≥ 60% | `FEAT-09`, `FEAT-50` |
| `KPI-09` | 1 | Tỷ lệ thành viên mới nhận vai trò chỉ do lựa chọn sẵn mà người mời không đổi, rồi được đổi vai trò trong 7 ngày đầu (dấu hiệu lựa chọn sẵn sai) | ≤ 10% | `BR-09.6`, `BR-21.4` |

### 2.7 Luồng nghiệp vụ đầu – cuối

1. **Khởi tạo:** workspace được tạo qua [`onboarding-srs.md`](./onboarding-srs.md) → Chủ sở hữu được xác lập (`FEAT-01`) → vai trò dựng sẵn, đơn vị gốc và nhóm mặc định có sẵn (`FEAT-02`).
2. **Dựng khung:** Chủ sở hữu cấu hình múi giờ, lịch làm việc (`FEAT-04`) → dựng cây tổ chức (`FEAT-21`) → điều chỉnh vai trò dựng sẵn hoặc tạo vai trò riêng (`FEAT-25`, `FEAT-29`) → đặt mức nền (`FEAT-34`) → kiểm tra sức khỏe cấu hình (`FEAT-03`).
3. **Đưa người vào:** mời nhiều email một lần, mời từ tệp, liên kết mời hoặc tự gia nhập theo tên miền (`FEAT-09`, `FEAT-44`, `FEAT-50`), có thể giao cho bộ phận nhân sự qua Quyền Ủy thác (`FEAT-45`) → người được mời chấp nhận lời mời → làm việc trong đúng phạm vi (`FEAT-35`).
4. **Vận hành:** chuyển phòng, kiêm nhiệm (`FEAT-11`), nhu cầu quyền cao hơn ngắn hạn (`FEAT-30` – `FEAT-32`), ngoại lệ trên bản ghi cụ thể (`FEAT-39`), luật theo điều kiện (`FEAT-36`), nhà cung cấp hỗ trợ có kiểm soát (`FEAT-08`).
5. **Thay đổi nhân sự:** nghỉ dài hạn → tạm ngưng (`FEAT-42`); nghỉ việc → rời workspace có bàn giao (`FEAT-43`); Chủ sở hữu rời công ty → chuyển nhượng (`FEAT-06`) hoặc khôi phục quyền sở hữu (`FEAT-07`).
6. **Kiểm soát & tuân thủ:** nhật ký thay đổi quyền (`FEAT-41`) → rà soát quyền định kỳ (`FEAT-48`) → báo cáo cho kiểm toán (`FEAT-49`) → kiểm định quyền tạm thời (`FEAT-33`).

---

## 3. Đặc tả yêu cầu chức năng

## A. CHỦ SỞ HỮU & CẤU HÌNH NỀN CỦA WORKSPACE

### FEAT-01 — Xác lập Chủ sở hữu & danh tính khi workspace được tạo

**Mô tả nghiệp vụ:** Workspace được tạo qua một trong hai kênh của [`onboarding-srs.md`](./onboarding-srs.md): khách hàng tự đăng ký, hoặc đội ngũ của nhà cung cấp tạo hộ khách hàng doanh nghiệp. Tính năng này quy định ai trở thành Chủ sở hữu ở mỗi kênh và các quy tắc danh tính đi kèm. Các bước đăng ký, tên miền phụ và chuỗi khởi tạo không thuộc tài liệu này.

**Vai trò sử dụng chính:** Hệ thống; Nhân sự vận hành nền tảng (kênh tạo hộ).

**Điều kiện tiên quyết:** Yêu cầu tạo workspace đã được chấp nhận theo [`onboarding-srs.md`](./onboarding-srs.md).

**Luồng chính — kênh tự đăng ký:**

1. Người đăng ký hoàn tất các bước đăng ký.
2. Khi workspace chuyển sang Sẵn sàng, tài khoản của người đăng ký trở thành thành viên Đang hoạt động với cấp bậc Chủ sở hữu.

**Luồng chính — kênh tạo hộ:**

1. Nhân sự vận hành nền tảng khai báo email của người sẽ là Chủ sở hữu phía khách hàng.
2. Khi workspace Sẵn sàng, người này là thành viên với cấp bậc Chủ sở hữu, trạng thái **Đang chờ chấp nhận**, và nhận lời mời kích hoạt.
3. Người này chấp nhận lời mời → trạng thái chuyển sang Đang hoạt động.

**Luồng ngoại lệ:**

- Lời mời kích hoạt Chủ sở hữu hết hạn → Nhân sự vận hành nền tảng gửi lại lời mời cho cùng email, hoặc đổi sang email khác theo yêu cầu bằng văn bản của khách hàng. Đổi email ghi nhật ký (`FEAT-41`).
- Email khai báo đã có tài khoản → dùng chính tài khoản đó, không tạo tài khoản thứ hai.

**Quy tắc nghiệp vụ:**

- **`BR-01.1` (Luôn đúng một Chủ sở hữu):** Từ thời điểm Sẵn sàng, mỗi workspace có đúng một Chủ sở hữu, kể cả khi người đó chưa chấp nhận lời mời. Không có trạng thái "chưa có Chủ sở hữu", không có hai Chủ sở hữu.

  **Lý do nghiệp vụ:** nhiều quy tắc cần một người chịu trách nhiệm cuối cùng — chuyển nhượng, khôi phục, chọn chế độ truy cập hỗ trợ, phê duyệt tham số dành riêng. Workspace không có Chủ sở hữu thì các quy tắc đó không có người thực hiện.

- **`BR-01.2` (Một email, một tài khoản):** Một địa chỉ email gắn với đúng một tài khoản trên toàn hệ thống, không phân biệt workspace. Một tài khoản có thể là thành viên của nhiều workspace, mỗi nơi với cấp bậc, vai trò và vị trí riêng.

  **Lý do nghiệp vụ:** một người làm việc cho nhiều công ty (ví dụ tư vấn viên, đại lý) chỉ dùng một danh tính; mỗi workspace vẫn quản trị quyền của họ độc lập.

- **`BR-01.3` (Chủ sở hữu kênh tạo hộ không do nhà cung cấp tự chọn):** Email Chủ sở hữu ở kênh tạo hộ phải do khách hàng cung cấp bằng văn bản. Nhân sự vận hành nền tảng không tự đặt mình hay người của nhà cung cấp làm Chủ sở hữu.

  **Lý do nghiệp vụ:** Chủ sở hữu là người đại diện khách hàng; để nhà cung cấp làm Chủ sở hữu thì nhà cung cấp có toàn quyền vĩnh viễn trên dữ liệu khách hàng, vượt ngoài cơ chế Phiên hỗ trợ (`FEAT-08`).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-01.1.1` | Khách hàng tự đăng ký xong | Workspace chuyển Sẵn sàng | Danh sách thành viên có đúng một người cấp bậc Chủ sở hữu, là người đăng ký |
| `AC-01.1.2` | Workspace tạo hộ, Chủ sở hữu chưa chấp nhận lời mời | Mở danh sách thành viên | Thấy Chủ sở hữu ở trạng thái Đang chờ chấp nhận; không có thành viên nào khác mang cấp bậc Chủ sở hữu |
| `AC-01.2.1` | Email a@x.com đã là thành viên workspace W1 | Được mời vào workspace W2 | Người dùng đăng nhập bằng cùng tài khoản, thấy cả W1 và W2, quyền ở mỗi nơi độc lập |
| `AC-01.3.1` | Nhân sự vận hành nền tảng tạo hộ | Nhập email thuộc tên miền của nhà cung cấp làm Chủ sở hữu | Hệ thống yêu cầu xác nhận đây là email do khách hàng cung cấp và ghi nhận tài liệu đính kèm; thiếu tài liệu thì không tạo được |

---

### FEAT-02 — Bất biến phân quyền khi workspace sẵn sàng

**Mô tả nghiệp vụ:** Một workspace chỉ được coi là Sẵn sàng khi bộ khung phân quyền tối thiểu đã đầy đủ, để Chủ sở hữu mời người ngay mà không gặp vai trò thiếu hay cây tổ chức trống. Cấu hình nghiệp vụ khác (phễu bán hàng, quy trình hỗ trợ, dữ liệu mẫu) thuộc [`onboarding-srs.md`](./onboarding-srs.md) và không chặn trạng thái Sẵn sàng.

**Vai trò sử dụng chính:** Hệ thống.

**Điều kiện tiên quyết:** Chuỗi khởi tạo đã tạo được workspace.

**Luồng chính:**

1. Tạo đủ bộ vai trò dựng sẵn (`FEAT-29`).
2. Tạo đơn vị gốc mang tên của workspace; đặt Chủ sở hữu vào làm Đơn vị chính và làm người phụ trách chính của đơn vị gốc.
3. Tạo nhóm mặc định "Toàn bộ thành viên" chứa Chủ sở hữu, chưa mang vai trò nào.
4. Đặt mức nền của workspace theo giá trị mặc định (`FEAT-34`).
5. Khi cả bốn bước thành công → workspace Sẵn sàng.

**Luồng ngoại lệ:**

- Một trong bốn bước thất bại → workspace không chuyển Sẵn sàng; xử lý thử lại và dọn dẹp theo [`onboarding-srs.md`](./onboarding-srs.md).

**Quy tắc nghiệp vụ:**

- **`BR-02.1` (Khung phân quyền là điều kiện của Sẵn sàng):** Bốn thành phần trên cùng thành công hoặc workspace chưa Sẵn sàng.

  **Lý do nghiệp vụ:** lời mời đầu tiên cần vai trò để chọn (`BR-09.6`); thành viên đầu tiên cần đơn vị để phạm vi dữ liệu có nghĩa (`BR-35.4`). Một workspace "sẵn sàng" mà thiếu các thành phần này sẽ cấp sai quyền ngay từ người đầu tiên.

- **`BR-02.2` (Đơn vị gốc không bao giờ trống):** Workspace luôn có ít nhất một đơn vị gốc. Đơn vị gốc cuối cùng không xoá được (`BR-23.4`).

  **Lý do nghiệp vụ:** không có đơn vị gốc thì không đặt được ai vào cây, mọi phạm vi theo đơn vị tê liệt.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-02.1.1` | Workspace vừa Sẵn sàng | Chủ sở hữu mở màn hình Vai trò | Thấy đủ các vai trò dựng sẵn tại `FEAT-29` |
| `AC-02.1.2` | Workspace vừa Sẵn sàng | Chủ sở hữu mở cây tổ chức | Có một đơn vị gốc mang tên workspace; Chủ sở hữu là người phụ trách chính và có Đơn vị chính là đơn vị này |
| `AC-02.1.3` | Bước tạo vai trò dựng sẵn thất bại | Theo dõi tiến trình khởi tạo | Workspace không hiển thị Sẵn sàng; không ai đăng nhập được vào workspace dở dang |
| `AC-02.2.1` | Workspace chỉ có một đơn vị gốc | Thử xoá đơn vị gốc | Từ chối, nêu rõ workspace phải luôn có ít nhất một đơn vị gốc |

---

### FEAT-03 — Kiểm tra sức khỏe cấu hình phân quyền

**Mô tả nghiệp vụ:** Cho người quản trị biết cấu hình phân quyền còn thiếu sót gì trước và trong khi vận hành.

**Vai trò sử dụng chính:** Người có toàn quyền; người có quyền Quản lý cấu hình workspace.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Mở màn hình "Sức khỏe phân quyền".
2. Hệ thống liệt kê cảnh báo theo hai mức, mỗi cảnh báo kèm số lượng, danh sách đối tượng và đường dẫn tới màn hình xử lý:
   - **Nghiêm trọng:** thành viên Đang hoạt động hoặc Đang chờ chấp nhận chưa có Đơn vị chính; thành viên chưa có vai trò nào; thành viên có quản lý trực tiếp đã Tạm ngưng hoặc Đã rời mà chưa có quản lý tạm thời; đơn vị có người phụ trách đã Tạm ngưng hoặc Đã rời; quyền tạm thời đã quá hạn mà vẫn hiệu lực; người giữ Quyền Ủy thác hoặc cấp bậc Quản trị viên không đăng nhập quá `CFG-03-01`; vi phạm quy tắc tách biệt nhiệm vụ đang tồn tại (`FEAT-46`); thay đổi vai trò dựng sẵn đang chờ duyệt (`BR-29.1`); hàng đợi chưa khai báo đơn vị tiếp nhận (`BR-35.11`); thay đổi, lời mời và lô nhập soạn sẵn của Phiên triển khai đang chờ xác nhận (`BR-08.7`); đơn vị tiếp nhận không có thành viên nào có ô Gán khác Không có (`BR-35.11`); thành viên Tạm ngưng mà chưa ai chọn cách xử lý bản ghi đang mở (chọn "giữ nguyên" cũng là đã xử lý, `BR-42.4`; không tính người đang trong quy trình rời workspace, `FEAT-43` — các cảnh báo về quản lý trực tiếp và người phụ trách đơn vị của người đó cũng được miễn tới khi quy trình xong); chức danh trách nhiệm đang trống (`FEAT-47`); đơn vị có đơn vị cha không còn tồn tại (`BR-23.2`); tài khoản Chủ sở hữu đang bị khoá trong khi Chủ sở hữu giữ chức danh Người phụ trách thanh toán hoặc có quy trình rời workspace đang chờ Chủ sở hữu (gợi ý `FEAT-07`).
   - **Nhẹ:** đơn vị chưa có người phụ trách (là Nghiêm trọng khi `CFG-34-02` bật, hoặc đơn vị là đơn vị tiếp nhận của một nguồn, là mặc định trong `CFG-35-01`, hoặc đang giữ bản ghi chờ phân công); lời mời đang tạm treo do người mời bị tạm ngưng (`BR-42.6`); vai trò tự tạo có ô để trống (đang dùng mức nền); có từ hai Người có toàn quyền mà `CFG-12-01` tắt (`BR-12.3`); Người phụ trách thanh toán đang tạm về Chủ sở hữu mà Chủ sở hữu chưa xác nhận (`FEAT-42`); cây tổ chức chỉ có đơn vị gốc; vai trò không còn ai giữ; nhóm không có thành viên.

**Quy tắc nghiệp vụ:**

- **`BR-03.1` (Chỉ tham khảo):** Kiểm tra sức khỏe không chặn thao tác nào và không tự sửa cấu hình.

  **Lý do nghiệp vụ:** nhiều cảnh báo là trạng thái tạm thời hợp lệ (ví dụ người mới chưa xếp đơn vị trong tuần đầu); tự sửa sẽ cấp hoặc gỡ quyền mà không ai quyết định.

- **`BR-03.2` (Cảnh báo dẫn tới hành động):** Mỗi cảnh báo phải mở được đúng danh sách đối tượng bị ảnh hưởng và màn hình xử lý tương ứng.

  **Lý do nghiệp vụ:** một con số không kèm danh sách buộc người quản trị tự tìm lại, và cảnh báo bị bỏ qua.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-03.1.1` | 3 thành viên chưa có Đơn vị chính | Mở Sức khỏe phân quyền | Cảnh báo Nghiêm trọng "3 thành viên chưa có Đơn vị chính"; mọi thao tác khác trong workspace vẫn làm được bình thường |
| `AC-03.1.2` | Quản lý trực tiếp của 5 người vừa bị Tạm ngưng | Mở Sức khỏe phân quyền | Cảnh báo Nghiêm trọng liệt kê 5 thành viên có quản lý trực tiếp không còn hoạt động |
| `AC-03.2.1` | Có cảnh báo "2 thành viên chưa có vai trò" | Bấm vào cảnh báo | Mở danh sách đúng 2 thành viên đó, có lối tắt sang màn hình gán vai trò |

---

### FEAT-04 — Cấu hình chung của workspace (ngôn ngữ, múi giờ, lịch làm việc)

**Mô tả nghiệp vụ:** Cấu hình ngôn ngữ, múi giờ, định dạng ngày, đơn vị tiền tệ và **lịch làm việc mặc định** áp dụng cho toàn workspace. Đây là nguồn duy nhất về múi giờ và lịch làm việc mặc định mà Mục 2.3 của tài liệu này và các phân hệ khác dẫn chiếu. Lịch làm việc chuyên biệt của từng phân hệ (ví dụ lịch theo đội hỗ trợ) do phân hệ đó quy định, mặc định kế thừa lịch này.

**Vai trò sử dụng chính:** Người có toàn quyền; người có quyền Quản lý cấu hình workspace.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Mở Cấu hình chung.
2. Chỉnh ngôn ngữ, múi giờ, định dạng ngày, tiền tệ, lịch làm việc (ngày làm việc trong tuần, giờ bắt đầu – kết thúc, ngày nghỉ lễ).
3. Lưu; hệ thống hiển thị cảnh báo tác động trước khi lưu nếu đổi múi giờ.

**Luồng ngoại lệ:**

- Lịch làm việc không có ngày làm việc nào, hoặc giờ kết thúc không sau giờ bắt đầu → từ chối lưu, nêu rõ lỗi.

**Quy tắc nghiệp vụ:**

- **`BR-04.1` (Mặc định cho người chưa tự chỉnh):** Ngôn ngữ và múi giờ hiển thị của workspace áp dụng cho thành viên chưa tự đặt riêng (`FEAT-16`).

  **Lý do nghiệp vụ:** người mới vào thấy giao diện đúng ngôn ngữ của công ty ngay, không phải tự cấu hình.

- **`BR-04.2` (Đổi múi giờ không đổi mốc đã chốt):** Đổi múi giờ workspace không dời các mốc hết hạn đã được tính cho các quyền tạm thời, lời mời và yêu cầu đang hiệu lực; chỉ áp dụng cho mốc tính sau khi đổi. Trước khi lưu, màn hình nêu rõ điều này.

  **Lý do nghiệp vụ:** dời mốc hết hạn của một quyền tạm thời đã được phê duyệt là thay đổi quyền mà người phê duyệt không đồng ý.

- **`BR-04.3` (Lịch làm việc hợp lệ):** Lịch làm việc phải có ít nhất một ngày làm việc và giờ kết thúc sau giờ bắt đầu.

  **Lý do nghiệp vụ:** lịch không có ngày làm việc làm mọi điều kiện "trong giờ hành chính" của chính sách truy cập (`FEAT-36`) không bao giờ đúng, và mọi thời hạn tính theo ngày làm việc không bao giờ đến hạn.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-04.1.1` | Workspace đặt tiếng Việt; thành viên mới chưa tự chỉnh | Thành viên đăng nhập lần đầu | Giao diện hiển thị tiếng Việt |
| `AC-04.2.1` | Có quyền tạm thời hết hạn 23:59:59 ngày 10 theo múi giờ cũ | Đổi múi giờ workspace và lưu | Màn hình cảnh báo trước khi lưu; mốc hết hạn của quyền đó giữ nguyên thời điểm tuyệt đối đã chốt |
| `AC-04.3.1` | Màn hình lịch làm việc | Bỏ chọn tất cả ngày trong tuần | Nút lưu bị vô hiệu, hiển thị "Lịch phải có ít nhất một ngày làm việc" trước khi bấm lưu |

---

### FEAT-05 — Trần quyền theo gói dịch vụ & tính năng mở rộng

**Mô tả nghiệp vụ:** Gói dịch vụ ([`billing-subscription-srs.md`](./billing-subscription-srs.md)) quyết định những loại dữ liệu, thao tác và quyền quản trị nào workspace được dùng. Một số tính năng mở rộng (ví dụ xuất/nhập hàng loạt, vai trò Kiểm toán, thư viện nội dung mạng xã hội) chỉ được Nhân sự vận hành nền tảng bật riêng theo hợp đồng. Tổng hợp của hai nguồn này là **trần quyền của workspace**.

**Vai trò sử dụng chính:** Nhân sự vận hành nền tảng (bật/tắt tính năng mở rộng); Hệ thống (áp trần theo gói).

**Điều kiện tiên quyết:** Có căn cứ hợp đồng hoặc gói dịch vụ tương ứng.

**Luồng chính:**

1. Nhân sự vận hành nền tảng bật hoặc tắt một tính năng mở rộng cho workspace, ghi kèm căn cứ.
2. Hệ thống cập nhật trần quyền; các ô và quyền quản trị thuộc tính năng đó chuyển sang khả dụng hoặc không khả dụng.
3. Hệ thống thông báo cho Chủ sở hữu và mọi Quản trị viên, và ghi vào nhật ký của chính workspace.

**Luồng ngoại lệ:**

- Tính năng bị tắt (do hạ gói, hết dùng thử hoặc nhà cung cấp tắt) trong khi vai trò đang có ô thuộc tính năng đó → ô vẫn được lưu nguyên giá trị nhưng **không có hiệu lực**, hiển thị nhãn "Không khả dụng theo gói". Khi tính năng được bật lại, ô có hiệu lực trở lại với đúng giá trị cũ.

**Quy tắc nghiệp vụ:**

- **`BR-05.1` (Có hiệu lực ngay):** Thay đổi trần quyền có hiệu lực ngay với mọi thành viên liên quan, theo `NFR-04`.

  **Lý do nghiệp vụ:** trần quyền là ranh giới hợp đồng; quyền vượt trần dù chỉ vài giờ là dùng dịch vụ chưa được mua.

- **`BR-05.2` (Tắt không xoá cấu hình):** Hạ trần không xoá giá trị của ô hay quyền quản trị trong vai trò; chỉ làm chúng mất hiệu lực.

  **Lý do nghiệp vụ:** doanh nghiệp hạ gói tạm thời rồi nâng lại không phải cấu hình lại toàn bộ vai trò.

- **`BR-05.3` (Doanh nghiệp luôn biết):** Mọi lần bật/tắt tính năng mở rộng phải thông báo cho Chủ sở hữu và Quản trị viên, và ghi vào nhật ký thay đổi cấu hình quyền của workspace với người thực hiện được gắn nhãn "Nhà cung cấp".

  **Lý do nghiệp vụ:** một tính năng như "xem toàn bộ dữ liệu bất kể phân quyền" thay đổi ai thấy gì trong workspace; doanh nghiệp phải biết và chứng minh được với kiểm toán ai đã bật nó.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-05.1.1` | Workspace chưa bật tính năng Xuất/nhập hàng loạt | Người quản trị mở ma trận vai trò | Cột Xuất và Nhập hiển thị "Không khả dụng theo gói", không chọn được mức |
| `AC-05.2.1` | Vai trò X có (Khách hàng, Xuất) = Đơn vị của mình; tính năng bị tắt | Người giữ vai trò X thử xuất | Bị từ chối; mở vai trò X thấy ô vẫn ghi Đơn vị của mình kèm nhãn "Không khả dụng theo gói" |
| `AC-05.2.2` | Tình huống AC-05.2.1, tính năng được bật lại | Người giữ vai trò X thử xuất | Xuất được trong phạm vi Đơn vị của mình |
| `AC-05.3.1` | Nhân sự vận hành nền tảng bật tính năng Kiểm toán | Chủ sở hữu mở nhật ký thay đổi quyền | Có bản ghi "Bật tính năng Kiểm toán", người thực hiện gắn nhãn Nhà cung cấp, kèm căn cứ; Chủ sở hữu đã nhận thông báo |

---

### FEAT-06 — Chuyển nhượng quyền sở hữu

**Mô tả nghiệp vụ:** Chủ sở hữu hiện tại chuyển giao quyền sở hữu cho một thành viên khác qua xác nhận hai chiều. Đây là nguồn duy nhất về chuyển nhượng quyền sở hữu; [`onboarding-srs.md`](./onboarding-srs.md) dẫn chiếu tới đây.

**Vai trò sử dụng chính:** Chủ sở hữu (khởi tạo); thành viên được chọn (xác nhận).

**Điều kiện tiên quyết:** Người nhận là thành viên Đang hoạt động của workspace và không phải chính Chủ sở hữu.

**Luồng chính:**

1. Chủ sở hữu chọn người nhận và chọn cấp bậc bản thân sẽ giữ sau khi chuyển: Quản trị viên hoặc Thành viên (kèm vai trò nếu chọn Thành viên).
2. Hệ thống tạo yêu cầu ở trạng thái **Đang chờ xác nhận**, gửi email và hiển thị thông báo cho người nhận; cả hai bên thấy trạng thái yêu cầu trong workspace.
3. Người nhận xác nhận → quyền sở hữu chuyển ngay; người chuyển nhượng nhận cấp bậc đã chọn ở bước 1; mọi Quản trị viên được thông báo.

**Luồng ngoại lệ:**

- Người nhận từ chối → yêu cầu kết thúc, không thay đổi gì, Chủ sở hữu được thông báo.
- Hết thời hạn chờ (`CFG-06-01`) → yêu cầu tự hết hạn, cả hai bên nhận thông báo.
- Chủ sở hữu huỷ yêu cầu bất kỳ lúc nào trước khi người nhận xác nhận.
- Đã có một yêu cầu đang chờ → không tạo được yêu cầu thứ hai; phải huỷ yêu cầu cũ trước.
- Người nhận bị gỡ, bị tạm ngưng hoặc bị khoá tài khoản trong lúc chờ → yêu cầu tự huỷ ngay, Chủ sở hữu được thông báo.
- Chủ sở hữu bị khoá tài khoản trong lúc chờ → yêu cầu tự huỷ ngay; việc thay Chủ sở hữu lúc này đi qua `FEAT-07`.

**Quy tắc nghiệp vụ:**

- **`BR-06.1` (Đồng thuận hai bên):** Quyền sở hữu chỉ đổi khi Chủ sở hữu khởi tạo và người nhận xác nhận. Không ai — kể cả Quản trị viên hay Nhân sự vận hành nền tảng — ép chuyển một phía qua tính năng này.

  **Lý do nghiệp vụ:** chuyển quyền sở hữu là thay đổi cao nhất của workspace; một cú bấm nhầm hoặc một tài khoản Quản trị viên bị chiếm không được phép đổi chủ workspace.

- **`BR-06.2` (Người chuyển tự chọn vị trí sau chuyển):** Cấp bậc của Chủ sở hữu cũ sau khi chuyển do chính họ chọn lúc khởi tạo.

  **Lý do nghiệp vụ:** "bàn giao nhưng ở lại hỗ trợ" và "rút lui hoàn toàn" là hai nhu cầu khác nhau; hệ thống không đoán thay.

- **`BR-06.3` (Một yêu cầu tại một thời điểm):** Mỗi workspace có tối đa một yêu cầu chuyển nhượng Đang chờ xác nhận.

  **Lý do nghiệp vụ:** hai yêu cầu song song có thể được hai người cùng xác nhận, sinh ra hai Chủ sở hữu, trái `BR-01.1`.

- **`BR-06.4` (Không ảnh hưởng Người phụ trách thanh toán):** Chuyển nhượng quyền sở hữu không tự đổi Người phụ trách thanh toán; việc đổi người này theo [`billing-subscription-srs.md`](./billing-subscription-srs.md).

  **Lý do nghiệp vụ:** người ký và trả tiền hợp đồng không nhất thiết là người quản trị workspace; tự đổi có thể làm hoá đơn gửi sai người.

- **`BR-06.5` (Ghi nhật ký đóng khi lỗi):** Chuyển nhượng thuộc nhóm nhật ký thay đổi cấu hình quyền (`BR-41.4`); không ghi được nhật ký thì không chuyển.

  **Lý do nghiệp vụ:** đây là thao tác nới rộng quyền lớn nhất; mất vết là mất khả năng chứng minh ai đã trao quyền sở hữu cho ai.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-06.1.1` | Chủ sở hữu A tạo yêu cầu chuyển cho B, chọn ở lại làm Quản trị viên | B xác nhận | B là Chủ sở hữu; A là Quản trị viên; mọi Quản trị viên nhận thông báo |
| `AC-06.2.1` | A khởi tạo chuyển nhượng, chọn rút lui làm Thành viên với vai trò Chỉ xem bản ghi được giao | B xác nhận | A là Thành viên với đúng vai trò Chỉ xem bản ghi được giao; không có lựa chọn nào do hệ thống tự suy |
| `AC-06.4.1` | A là Chủ sở hữu và Người phụ trách thanh toán | Chuyển nhượng cho B hoàn tất | A vẫn là Người phụ trách thanh toán cho tới khi đổi theo quy trình thanh toán |
| `AC-06.1.2` | Quản trị viên C | Mở hồ sơ một thành viên | Không có thao tác chuyển quyền sở hữu nào hiển thị cho C |
| `AC-06.3.1` | Đã có yêu cầu A → B đang chờ | A thử tạo yêu cầu A → D | Từ chối, nêu rõ phải huỷ yêu cầu đang chờ trước |
| `AC-06.1.3` | Yêu cầu A → B đang chờ, `CFG-06-01` = 72 giờ | 72 giờ trôi qua, B không phản hồi | Yêu cầu hết hạn; A và B nhận email; A vẫn là Chủ sở hữu |
| `AC-06.1.4` | Yêu cầu A → B đang chờ | B bị Tạm ngưng | Yêu cầu tự huỷ ngay; A nhận thông báo kèm lý do |
| `AC-06.5.1` | Nhật ký thay đổi cấu hình quyền gặp sự cố | B bấm xác nhận | Báo lỗi, quyền sở hữu không đổi, yêu cầu vẫn Đang chờ xác nhận |

---

### FEAT-07 — Khôi phục quyền sở hữu khi Chủ sở hữu không còn khả dụng

**Mô tả nghiệp vụ:** Khi Chủ sở hữu rời công ty mà không chuyển nhượng, qua đời, hoặc mất quyền kiểm soát tài khoản, doanh nghiệp cần một đường hợp lệ để đặt Chủ sở hữu mới mà không cần chính người đó tham gia.

**Vai trò sử dụng chính:** Quản trị viên của workspace hoặc người đại diện theo pháp luật của doanh nghiệp (gửi yêu cầu); Nhân sự vận hành nền tảng (thẩm định và thực hiện).

**Điều kiện tiên quyết:** Người được đề xuất làm Chủ sở hữu mới là thành viên Đang hoạt động của workspace và không phải người gửi đề nghị.

**Luồng chính:**

1. Người yêu cầu gửi đề nghị khôi phục, nêu người đề xuất làm Chủ sở hữu mới và lý do, kèm văn bản xác nhận của doanh nghiệp.
2. Nhân sự vận hành nền tảng thẩm định căn cứ, ghi kết quả thẩm định.
3. Hệ thống mở **thời gian chờ phản đối** (`BR-07.2`) và thông báo qua mọi kênh có được cho Chủ sở hữu hiện tại và cho mọi Quản trị viên.
4. Hết thời gian chờ mà Chủ sở hữu hiện tại không phản đối → quyền sở hữu chuyển cho người được đề xuất; Chủ sở hữu cũ chuyển thành Thành viên ở trạng thái Tạm ngưng.

**Luồng ngoại lệ:**

- Chủ sở hữu hiện tại, một Người có toàn quyền khác người gửi, hoặc người mang chức danh Người phụ trách bảo mật phản đối trong thời gian chờ → đề nghị bị huỷ, mọi bên được thông báo.
- Thẩm định không đạt → đề nghị bị từ chối kèm lý do.
- Người được đề xuất bị gỡ hoặc tạm ngưng trong thời gian chờ → đề nghị tự huỷ.

**Quy tắc nghiệp vụ:**

- **`BR-07.1` (Căn cứ bằng văn bản):** Đề nghị khôi phục chỉ được thẩm định khi có văn bản xác nhận của doanh nghiệp. Kết quả thẩm định và văn bản được lưu cùng nhật ký.

  **Lý do nghiệp vụ:** đây là đường đổi chủ workspace không cần chủ hiện tại; thiếu căn cứ thì đó là con đường chiếm workspace.

- **`BR-07.2` (Thời gian chờ phản đối cố định):** Thời gian chờ phản đối là 7 ngày, không cấu hình được.

  **Lý do nghiệp vụ:** đây là hằng số chống chiếm quyền: nếu doanh nghiệp tự rút ngắn được thì một Quản trị viên xấu cũng rút ngắn được ngay trước khi gửi đề nghị.

- **`BR-07.3` (Chủ sở hữu cũ không còn toàn quyền):** Sau khôi phục, Chủ sở hữu cũ bị chuyển thành Thành viên Tạm ngưng; Chủ sở hữu mới quyết định gỡ hay kích hoạt lại.

  **Lý do nghiệp vụ:** lý do khôi phục là người đó không còn khả dụng hoặc mất kiểm soát tài khoản; giữ toàn quyền cho tài khoản đó là giữ rủi ro.

- **`BR-07.4` (Không tự đề cử, nhiều người phản đối được):** Người gửi đề nghị không được tự đề cử mình. Ngoài Chủ sở hữu hiện tại, mọi Người có toàn quyền khác và người mang chức danh Người phụ trách bảo mật đều phản đối được.

  **Lý do nghiệp vụ:** Nguyên tắc 2; và khi Chủ sở hữu đã thật sự không khả dụng thì phải còn người khác ngăn được một đề nghị không chính đáng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-07.1.1` | Quản trị viên gửi đề nghị không kèm văn bản | Gửi | Không gửi được, nêu rõ cần văn bản xác nhận của doanh nghiệp |
| `AC-07.2.1` | Đề nghị đã thẩm định đạt, đang trong 7 ngày chờ | Chủ sở hữu hiện tại đăng nhập và bấm phản đối | Đề nghị huỷ; mọi Quản trị viên và người yêu cầu nhận thông báo; Chủ sở hữu không đổi |
| `AC-07.2.2` | Đề nghị đã thẩm định đạt | Hết 7 ngày, không có phản đối | Người được đề xuất thành Chủ sở hữu; Chủ sở hữu cũ là Thành viên Tạm ngưng; nhật ký có đủ văn bản và kết quả thẩm định |
| `AC-07.4.1` | Quản trị viên A gửi đề nghị | Chọn chính A làm người được đề xuất | A không có trong danh sách chọn, kèm giải thích |
| `AC-07.4.2` | Đề nghị của A đề xuất B đang trong thời gian chờ | Quản trị viên C phản đối | Đề nghị huỷ; A, B, Chủ sở hữu hiện tại được thông báo |
| `AC-07.1.2` | Đề nghị đang trong thời gian chờ | Người được đề xuất bị gỡ khỏi workspace | Đề nghị tự huỷ; người gửi được thông báo |
| `AC-07.3.1` | Khôi phục vừa hoàn tất | Chủ sở hữu cũ thử đăng nhập | Thấy thông báo đang tạm ngưng; Chủ sở hữu mới thấy người này ở trạng thái Tạm ngưng với cấp bậc Thành viên |

---

### FEAT-08 — Truy cập hỗ trợ có kiểm soát của nhà cung cấp

**Mô tả nghiệp vụ:** Nhân sự vận hành nền tảng chỉ vào được một workspace qua **Phiên hỗ trợ**: có thời hạn, có lý do, được doanh nghiệp biết và được ghi đầy đủ vào nhật ký của chính workspace.

**Vai trò sử dụng chính:** Chủ sở hữu hoặc Quản trị viên (cấp phiên); Nhân sự vận hành nền tảng (dùng phiên).

**Điều kiện tiên quyết:** Có mã yêu cầu hỗ trợ.

**Luồng chính:**

1. Nhân sự vận hành nền tảng gửi đề nghị phiên hỗ trợ cho workspace, kèm mã yêu cầu hỗ trợ, lý do và thời lượng đề nghị.
2. Theo chế độ `CFG-08-01`:
   - **Chỉ khi doanh nghiệp cấp phiên (mặc định):** Chủ sở hữu hoặc Quản trị viên phê duyệt đề nghị, chọn mức của phiên (**Chỉ xem** hoặc **Xem và sửa dữ liệu nghiệp vụ**) và có thể rút ngắn thời lượng.
   - **Cho phép kèm thông báo:** phiên mở ngay ở mức Chỉ xem khi đề nghị được gửi; Chủ sở hữu và Quản trị viên nhận thông báo tức thì và nâng được lên Xem và sửa dữ liệu nghiệp vụ.
3. Trong phiên, mọi thao tác của nhân sự vận hành được ghi vào nhật ký của workspace, gắn nhãn "Nhà cung cấp".
4. Phiên kết thúc khi hết thời lượng, khi nhân sự vận hành tự đóng, hoặc khi Chủ sở hữu/Quản trị viên chấm dứt; doanh nghiệp nhận thông báo kết thúc kèm tóm tắt thao tác.

**Luồng ngoại lệ:**

- Sự cố an ninh hoặc yêu cầu của cơ quan có thẩm quyền mà không thể chờ phê duyệt → Nhân sự vận hành nền tảng mở **phiên khẩn cấp** kèm số hồ sơ sự cố; Chủ sở hữu được thông báo ngay; phiên khẩn cấp luôn Chỉ xem và tối đa 4 giờ.
- Đề nghị không được phê duyệt trong `CFG-08-03` → tự hết hạn; nhân sự vận hành được thông báo.

**Quy tắc nghiệp vụ:**

- **`BR-08.1` (Không có truy cập ngầm):** Ngoài Phiên hỗ trợ và giai đoạn triển khai trước kích hoạt của kênh tạo hộ ([`onboarding-srs.md`](./onboarding-srs.md) `BR-16.4`), nhân sự vận hành nền tảng không có cách nào xem hay sửa dữ liệu nghiệp vụ của workspace.

  **Lý do nghiệp vụ:** dữ liệu là của doanh nghiệp; nhà cung cấp truy cập không có dấu vết làm doanh nghiệp không đạt được yêu cầu quản lý nhà cung cấp trong đánh giá an toàn thông tin.

- **`BR-08.2` (Phiên có giới hạn):** Thời lượng một phiên không vượt `CFG-08-02`. Muốn tiếp tục phải xin phiên mới.

  **Lý do nghiệp vụ:** một phiên không giới hạn là quyền truy cập vĩnh viễn trá hình.

- **`BR-08.3` (Mọi thao tác của nhà cung cấp vào nhật ký riêng, giữ đủ lâu):** Mọi thao tác trong Phiên hỗ trợ, phiên khẩn cấp, Phiên triển khai và giai đoạn triển khai trước kích hoạt, kể cả chỉ xem bản ghi, được ghi vào **nhật ký truy cập của nhà cung cấp** (`BR-41.4`): đóng khi lỗi (không ghi được thì thao tác bị từ chối) và lưu theo `CFG-41-02`; người thực hiện luôn gắn nhãn "Nhà cung cấp" kèm tên nhân sự và mã yêu cầu hỗ trợ hoặc mã phiên.

  **Lý do nghiệp vụ:** doanh nghiệp phải trả lời được "nhà cung cấp đã xem và làm gì của chúng tôi" ở kỳ kiểm toán năm; nhật ký quyết định truy cập thông thường được phép bỏ sót và lưu ngắn hơn, không đủ cho câu hỏi này.

- **`BR-08.4` (Phiên khẩn cấp là ngoại lệ có hậu kiểm):** Mỗi phiên khẩn cấp phải gắn số hồ sơ sự cố, luôn Chỉ xem, tối đa 4 giờ, và được liệt kê riêng trong báo cáo của `FEAT-49`. Các giới hạn này cố định.

  **Lý do nghiệp vụ:** phiên khẩn cấp bỏ qua sự đồng ý của doanh nghiệp, nên phải hẹp, ngắn và hiển nhiên để không thành đường tắt thường ngày.

- **`BR-08.5` (Nhà cung cấp không đổi cấu hình quyền trong Phiên hỗ trợ):** Trong Phiên hỗ trợ (thường hay khẩn cấp), nhân sự vận hành nền tảng không được: mời, tạm ngưng hay gỡ thành viên; đổi cấp bậc, vai trò, nhóm, đơn vị, chức danh; tạo hay sửa chính sách, quyền trên bản ghi, quyền tạm thời, mức nền; đổi tham số tại Phụ lục B; chuyển nhượng hay khôi phục quyền sở hữu; xuất hay nhập dữ liệu. Mọi mức phiên luôn chịu che dữ liệu nhạy cảm (`FEAT-40`). Mức "Xem và sửa dữ liệu nghiệp vụ" chỉ cho sửa bản ghi nghiệp vụ, không gồm xoá. Mọi mức phiên được xem (chỉ đọc) cấu hình quyền và quyền hiệu lực của thành viên để chẩn đoán sự cố "không thấy dữ liệu".

  **Lý do nghiệp vụ:** hỗ trợ cần xem và đôi khi sửa dữ liệu để khắc phục sự cố; nhưng nếu nhà cung cấp đổi được ai có quyền gì hay mang dữ liệu ra ngoài, một phiên hỗ trợ thành cửa hậu thường trực.

- **`BR-08.6` (Số liệu vận hành không cần phiên):** Nhân sự vận hành nền tảng xem được, không cần phiên hỗ trợ, các số liệu vận hành ở dạng tổng hợp: số thành viên theo trạng thái, tiến độ lộ trình thiết lập, số cảnh báo sức khỏe cấu hình theo loại. Không có tên người, nội dung bản ghi hay cấu hình chi tiết.

  **Lý do nghiệp vụ:** đội chăm sóc khách hàng của nhà cung cấp cần biết khách nào đang gặp khó để chủ động hỗ trợ, mà không phải xin quyền vào dữ liệu.

- **`BR-08.7` (Phiên triển khai sau kích hoạt):** Chủ sở hữu, hoặc một Quản trị viên kèm người duyệt thứ hai theo `BR-47.3` (workspace chỉ có Chủ sở hữu thì Chủ sở hữu tự duyệt), duyệt được một **Phiên triển khai** cho nhân sự triển khai của nhà cung cấp, thời hạn tối đa `CFG-08-04`; Người có toàn quyền chấm dứt được bất kỳ lúc nào. Trong phiên, nhân sự triển khai cấu hình đơn vị, vai trò tự tạo, ô vai trò dựng sẵn, nhóm, chính sách, mức nền và đơn vị tiếp nhận; không được xem, sửa, xuất dữ liệu nghiệp vụ hay chạy lô nhập; không được đổi cấp bậc, Quyền Ủy thác, chức danh, chuyển nhượng hay tham số dành riêng cho Chủ sở hữu. Thay đổi **thu hẹp** quyền và thay đổi trên đối tượng mới chưa ai dùng có hiệu lực ngay. Thay đổi **nới rộng** quyền của bất kỳ thành viên đang có (sửa vai trò, nhóm, ô, mức nền, chính sách, người phụ trách đơn vị) và mọi lời mời chỉ ở trạng thái **soạn sẵn**, có hiệu lực khi một Người có toàn quyền xác nhận sau khi xem khác biệt quyền và số người bị ảnh hưởng; Nhân sự triển khai chuẩn bị được **lô nhập dữ liệu soạn sẵn** từ tệp do khách hàng cung cấp: ánh xạ cột (gồm cột Người phụ trách, `BR-35.10`) và kiểm tra lỗi **định dạng của chính tệp** theo SRS phân hệ (ví dụ [`contacts-srs.md`](./contacts-srs.md) `FEAT-22` – `FEAT-24`); việc đối chiếu trùng với dữ liệu đang có và chọn cách xử lý trùng chỉ diễn ra khi một Người có toàn quyền mở lô để xác nhận, và kết quả không hiển thị cho nhân sự triển khai. Lô chạy dưới danh nghĩa người xác nhận, Người phụ trách theo ánh xạ (chịu ô Gán của người xác nhận); lô có dòng trỏ tới người trong danh sách mời soạn sẵn chỉ chạy được sau khi các lời mời đó đã được xác nhận gửi hoặc bị huỷ (dòng trỏ tới lời mời bị huỷ thành dòng lỗi); nhân sự triển khai không xem được bản ghi sau khi nhập. Tệp gốc chỉ nhân sự đã tải lên và Người có toàn quyền tải về được, và bị xoá ngay khi lô chạy xong hoặc bị huỷ; trước khi xoá, các dòng lỗi được giữ thành một tệp dòng lỗi riêng để Người có toàn quyền tải về và nhập lại, theo cùng thời hạn `CFG-08-05`. Khi phiên hết hạn hoặc bị chấm dứt, thay đổi, lời mời và lô nhập soạn sẵn được giữ `CFG-08-05` để Người có toàn quyền xác nhận hoặc huỷ, sau đó tự huỷ. Đây là ngoại lệ tường minh của `BR-08.5` và chỉ trong giới hạn này.

  **Lý do nghiệp vụ:** triển khai cho doanh nghiệp lớn kéo dài nhiều tuần sau kích hoạt; không có kênh hợp lệ thì khách hàng sẽ cấp tài khoản thành viên cho tư vấn viên — không ai kiểm soát. Nhưng nhà cung cấp tự nới quyền của 300 người đang làm việc mà không ai xác nhận từng thay đổi thì là cửa hậu mà `BR-08.5` muốn chặn.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-08.1.1` | `CFG-08-01` = Chỉ khi doanh nghiệp cấp phiên; chưa có phiên | Nhân sự vận hành thử mở dữ liệu khách hàng của workspace | Bị từ chối; chỉ có lựa chọn gửi đề nghị phiên hỗ trợ |
| `AC-08.2.1` | Quản trị viên duyệt phiên 2 giờ, Chỉ xem | Nhân sự vận hành mở một khách hàng trong phiên | Mọi thao tác sửa bị vô hiệu; lượt xem được ghi nhật ký |
| `AC-08.2.2` | Phiên 2 giờ đã mở | Hết 2 giờ | Nhân sự vận hành mất truy cập ngay; Chủ sở hữu nhận tóm tắt thao tác của phiên |
| `AC-08.3.1` | Trong phiên, nhân sự vận hành mở 3 hồ sơ khách hàng | Chủ sở hữu tra nhật ký truy cập của nhà cung cấp | Thấy đúng 3 lượt xem, tên nhân sự, mã yêu cầu hỗ trợ; một năm sau vẫn tra được |
| `AC-08.4.1` | Phiên khẩn cấp mở kèm số hồ sơ sự cố | Chủ sở hữu kiểm tra hộp thư | Đã nhận thông báo ngay khi phiên mở; báo cáo `FEAT-49` liệt kê phiên này ở mục phiên khẩn cấp |
| `AC-08.5.1` | Phiên hỗ trợ mức Xem và sửa dữ liệu nghiệp vụ | Nhân sự vận hành mở màn hình mời thành viên hoặc sửa vai trò | Các thao tác bị vô hiệu kèm giải thích "Không khả dụng trong phiên hỗ trợ" |
| `AC-08.6.1` | Không có phiên hỗ trợ | Nhân sự vận hành mở bảng số liệu vận hành của workspace | Thấy số thành viên theo trạng thái, tiến độ lộ trình, số cảnh báo theo loại; không thấy tên người hay bản ghi |
| `AC-08.5.2` | Phiên hỗ trợ mức Chỉ xem | Nhân sự vận hành mở quyền hiệu lực của một thành viên | Xem được ma trận và nguồn quyền, không có thao tác sửa |
| `AC-08.5.3` | Phiên hỗ trợ bất kỳ | Nhân sự vận hành tìm thao tác xuất danh sách khách hàng | Không có; trường nhạy cảm hiển thị đã che |
| `AC-08.3.2` | Nhật ký gặp sự cố trong lúc có phiên hỗ trợ | Nhân sự vận hành mở một khách hàng | Bị từ chối kèm thông báo lỗi; không có lượt xem nào không được ghi |
| `AC-08.7.1` | Phiên triển khai đang mở | Nhân sự triển khai tạo 3 vai trò tự tạo chưa ai giữ và soạn 40 lời mời | 3 vai trò có hiệu lực ngay; 40 lời mời ở trạng thái soạn sẵn, chưa ai nhận thư cho tới khi Người có toàn quyền xác nhận |
| `AC-08.7.2` | Phiên triển khai đang mở | Nhân sự triển khai mở danh sách khách hàng | Không có quyền xem dữ liệu nghiệp vụ |
| `AC-08.7.3` | Phiên triển khai đang mở | Nhân sự triển khai nâng ô (Khách hàng, Xem) của Nhân viên Kinh doanh lên Toàn workspace | Thay đổi ở trạng thái soạn sẵn; Quản trị viên thấy khác biệt và "120 người bị ảnh hưởng" trước khi xác nhận |
| `AC-08.7.4` | Phiên triển khai đang mở | Nhân sự triển khai tìm thao tác nâng ai đó lên Quản trị viên | Không có |
| `AC-08.7.5` | Workspace có Chủ sở hữu và 2 Quản trị viên | Quản trị viên A duyệt Phiên triển khai | Phiên chỉ mở sau khi Quản trị viên B hoặc Chủ sở hữu duyệt thứ hai |
| `AC-08.7.6` | Phiên triển khai hết hạn còn 5 thay đổi soạn sẵn, `CFG-08-05` = 30 ngày | Quản trị viên mở danh sách sau 10 ngày | Vẫn xác nhận hoặc huỷ được; sau 30 ngày các thay đổi tự huỷ |
| `AC-08.7.7` | Nhân sự triển khai chuẩn bị lô nhập 8.000 khách hàng có cột Người phụ trách | Quản trị viên mở lô, chọn cách xử lý trùng, xác nhận | Lô chạy dưới tên Quản trị viên; mỗi khách hàng có Người phụ trách theo cột; nhân sự triển khai không thấy kết quả đối chiếu trùng, không mở được các khách hàng vừa nhập; tệp gốc bị xoá |
| `AC-08.7.8` | Lô nhập soạn sẵn quá `CFG-08-05` chưa được xác nhận | — | Lô và tệp gốc bị huỷ; Người có toàn quyền nhận thông báo |
| `AC-08.2.3` | Đề nghị phiên chưa được duyệt sau `CFG-08-03` | — | Đề nghị hiển thị Hết hạn ở cả hai phía; nhân sự vận hành nhận thông báo |

---

## B. NGƯỜI DÙNG

### FEAT-09 — Mời thành viên & vòng đời lời mời

**Mô tả nghiệp vụ:** Đưa một người (đã có hoặc chưa có tài khoản) vào workspace, với cấp bậc, vai trò, vị trí tổ chức và nhóm được chọn sẵn. Người được mời phải chấp nhận lời mời trước khi truy cập được.

**Vai trò sử dụng chính:** Người có quyền Mời người dùng.

**Điều kiện tiên quyết:** Workspace còn hạn mức người dùng theo gói ([`billing-subscription-srs.md`](./billing-subscription-srs.md)).

**Luồng chính:**

1. Người mời nhập **một hoặc nhiều email** (dán danh sách, tối đa theo `NFR-09`; mọi người trong lượt nhận cùng các lựa chọn bên dưới); chọn cấp bậc (Quản trị viên hoặc Thành viên); chọn một hoặc nhiều vai trò (đã chọn sẵn theo `BR-09.6`); chọn Đơn vị chính, các Đơn vị kiêm nhiệm, quản lý trực tiếp và các nhóm (đều tuỳ chọn).
2. Người mời có thể **xem trước** người này sẽ làm được gì với lựa chọn hiện tại (không lưu).
3. Gửi → hệ thống kiểm tra các quy tắc dưới đây, tạo thành viên ở trạng thái **Đang chờ chấp nhận** và gửi email mời. Người chưa có tài khoản được tạo tài khoản và đặt mật khẩu ngay trong bước chấp nhận.
4. Người được mời chấp nhận → thành viên chuyển Đang hoạt động; vai trò, vị trí và nhóm đã chọn có hiệu lực.

**Luồng ngoại lệ:**

- Email đã là thành viên (ở bất kỳ trạng thái nào trừ Đã rời) của workspace → báo "đã có trong workspace", không tạo trùng. Nếu đang Tạm ngưng, gợi ý kích hoạt lại (`FEAT-42`).
- Email không thuộc danh sách tên miền được phép (`CFG-09-03`) → báo ngay tại ô email, nêu rõ chính sách của workspace (`BR-09.9`).
- Workspace đang dùng thử vượt giới hạn số lời mời mỗi giờ của nền tảng → phần vượt được xếp hàng gửi dần theo [`onboarding-srs.md`](./onboarding-srs.md) `BR-14.3`.
- Vượt hạn mức người dùng của gói → xử lý theo `BR-09.8`.
- Lời mời hết hạn (`CFG-09-01`) → thành viên chuyển Đã rời; người mời được thông báo; có thể mời lại. Lời mời đang giữ chỗ bản ghi (`BR-35.10`) được nhắc trước khi hết hạn theo `CFG-09-04` (thời hạn lời mời không lớn hơn mốc nhắc thì nhắc ngay khi gửi), kèm số bản ghi sẽ được thả về hàng đợi: nhắc người được mời nếu lời mời không tạm treo, nhắc người mời nếu đang hoạt động, và luôn nhắc người phụ trách đơn vị giữ chỗ tại thời điểm nhắc. Gửi lại tính lại hạn nên lời nhắc được đặt lại theo hạn mới.
- Người mời **gửi lại** lời mời → vẫn là cùng lời mời: đường dẫn cũ mất hiệu lực, đường dẫn mới được gửi, thời hạn tính lại từ lúc gửi lại, bản ghi giữ chỗ không đổi (không coi là thu hồi). Người gửi lại phải còn năng lực bao phủ các lựa chọn trong lời mời (`BR-09.3`).
- Người có quyền Mời người dùng **sửa lời mời** đang chờ (vai trò, vị trí, nhóm) → theo `BR-09.12`.
- Người mời **thu hồi** lời mời trước khi được chấp nhận → thành viên chuyển Đã rời, đường dẫn trong email mất hiệu lực.
- Người được mời từ chối → thành viên chuyển Đã rời, người mời được thông báo.
- Sự cố khi tạo tài khoản mới → toàn bộ lời mời bị huỷ, không để lại tài khoản dở dang.

**Quy tắc nghiệp vụ:**

- **`BR-09.1` (Không mời Chủ sở hữu):** Lời mời chỉ có cấp bậc Quản trị viên hoặc Thành viên.

  **Lý do nghiệp vụ:** workspace luôn có đúng một Chủ sở hữu (`BR-01.1`); đổi Chủ sở hữu chỉ qua `FEAT-06`, `FEAT-07`.

- **`BR-09.2` (Chỉ Người có toàn quyền trao cấp bậc Quản trị viên):** Chỉ Chủ sở hữu hoặc Quản trị viên mới mời được người với cấp bậc Quản trị viên, và chịu thêm `BR-12.3`, `BR-12.4`.

  **Lý do nghiệp vụ:** Quản trị viên vượt mọi trần; người không có toàn quyền mà trao được toàn quyền thì mọi kiểm soát khác vô nghĩa.

- **`BR-09.3` (Không cấp vượt năng lực):** Vai trò và nhóm chọn cho người được mời không được mang lại quyền vượt năng lực quyền hạn của người mời, xét theo từng ô và từng quyền quản trị. Ngoại lệ duy nhất: Quyền Ủy thác (`BR-45.2`).

  **Lý do nghiệp vụ:** Nguyên tắc 1; nếu không, quyền Mời người dùng trở thành đường cấp quyền không giới hạn.

- **`BR-09.4` (Vị trí thuộc cùng workspace):** Đơn vị, quản lý trực tiếp và nhóm chọn cho người được mời phải thuộc workspace hiện tại; quản lý trực tiếp phải Đang hoạt động, hoặc Đang chờ chấp nhận trong cùng lượt mời — khi đó quan hệ quản lý chỉ có hiệu lực về phạm vi dữ liệu khi cả hai đã Đang hoạt động.

  **Lý do nghiệp vụ:** trỏ sang workspace khác hoặc sang người đã rời làm chuỗi quản lý và phạm vi dữ liệu sai từ đầu; còn trưởng nhóm và thành viên thường được mời cùng lúc ([`onboarding-srs.md`](./onboarding-srs.md) `FEAT-12`), bắt chờ từng người chấp nhận là thêm việc thủ công.

- **`BR-09.5` (Không tự suy vị trí):** Bỏ trống Đơn vị chính thì người được mời ở trạng thái "Chưa có đơn vị" và xuất hiện trong cảnh báo Nghiêm trọng của `FEAT-03`; bỏ trống quản lý trực tiếp thì để trống. Hệ thống không tự gán theo vị trí của người mời.

  **Lý do nghiệp vụ:** người mời thường là bộ phận nhân sự hoặc công nghệ thông tin, không cùng đội với người mới; tự gán theo người mời làm sai phạm vi dữ liệu và chuỗi quản lý mà không ai nhận ra.

- **`BR-09.6` (Vai trò mặc định hiển thị rõ):** Màn hình mời chọn sẵn vai trò theo thứ tự: vai trò gợi ý của Đơn vị chính đã chọn (`BR-21.4`); nếu đơn vị không khai báo thì vai trò theo `CFG-09-02`. Lựa chọn sẵn luôn hiển thị rõ cho người mời; người mời đổi hoặc bỏ được. Không có vai trò ẩn nào được gán thêm sau khi gửi. Nếu `CFG-09-02` đặt "Không chọn sẵn", người mời bắt buộc chọn ít nhất một vai trò.

  **Lý do nghiệp vụ:** gán ngầm một vai trò mặc định khiến người mới thấy dữ liệu mà người mời không hề quyết định; nhưng để người mới không có vai trò nào thì họ đăng nhập vào màn hình trống.

- **`BR-09.7` (Chấp nhận là bắt buộc):** Kể cả người đã có tài khoản, thành viên chỉ Đang hoạt động sau khi chính họ chấp nhận lời mời.

  **Lý do nghiệp vụ:** mời nhầm email ra ngoài công ty mà người đó vào ngay thì lộ dữ liệu trước khi kịp thu hồi; chấp nhận cũng xác nhận người dùng biết mình đang vào workspace nào.

- **`BR-09.8` (Hạn mức người dùng):** Khi lời mời làm vượt hạn mức người dùng của gói, hệ thống áp đúng chính sách chạm trần của gói theo [`billing-subscription-srs.md`](./billing-subscription-srs.md) `BR-11.6`, `BR-11.7` — chặn và nêu rõ giới hạn cùng cách xử lý, hoặc cho mời và báo trước phần phí vượt. Cách tính hạn mức (gồm việc thành viên Đang chờ chấp nhận và Tạm ngưng có được tính hay không) theo tài liệu đó.

  **Lý do nghiệp vụ:** hạn mức là cam kết hợp đồng; báo lỗi ngay lúc mời tốt hơn để người mới chấp nhận rồi mới bị chặn.

- **`BR-09.9` (Tên miền được phép):** Khi `CFG-09-03` có danh sách tên miền, mọi đường đưa người vào workspace — lời mời, mời hàng loạt, liên kết mời, tự gia nhập — chỉ nhận email thuộc danh sách.

  **Lý do nghiệp vụ:** doanh nghiệp cấm đưa email cá nhân vào hệ thống thì quy tắc phải áp ở mọi cửa, không chỉ ở một màn hình.

- **`BR-09.10` (Mời nhiều email một lượt):** Một lượt mời nhận tới số email tại `NFR-09`; mỗi email được kiểm tra riêng theo mọi quy tắc trên; email lỗi được báo ngay khi dán, các email khác vẫn gửi được. Một lượt có thể chứa nhiều tổ hợp lựa chọn khi được gửi từ thiết lập đội ngũ nhanh ([`onboarding-srs.md`](./onboarding-srs.md) `FEAT-12`).

  **Lý do nghiệp vụ:** mời từng người một là rào cản lớn nhất của việc đưa cả đội vào workspace.

- **`BR-09.11` (Lời mời Chờ gửi):** Lời mời xếp hàng do giới hạn gửi của workspace dùng thử ([`onboarding-srs.md`](./onboarding-srs.md) `BR-14.3`) ở trạng thái **Chờ gửi**: được tính vào hạn mức người dùng như lời mời Đang chờ chấp nhận (`BR-09.8`); được gửi dần theo giới hạn, và đứng yên khi hàng đợi tạm dừng; người mời thu hồi được; gửi ngay khi workspace chuyển sang trả phí; bị huỷ khi hết dùng thử mà chưa chuyển đổi (người mời được báo); bị nhân sự vận hành nền tảng huỷ thì xử lý như thu hồi (gồm cả nhánh trưởng nhóm của `BR-21.2`). Hạn lời mời tính từ lúc thư thực sự được gửi.

  **Lý do nghiệp vụ:** lời mời chờ gửi là cam kết đã đưa ra với người sẽ được mời; nó phải có vòng đời rõ như mọi lời mời khác.

- **`BR-09.12` (Sửa lời mời đang chờ):** Lời mời Đang chờ chấp nhận, Chờ gửi hoặc tạm treo sửa được cấp bậc, vai trò, Đơn vị chính, Đơn vị kiêm nhiệm, quản lý trực tiếp và nhóm bởi người có quyền Mời người dùng, khi năng lực của người sửa bao phủ **cả giá trị cũ lẫn giá trị mới** (`BR-09.2`, `BR-09.3`). Lời mời Soạn sẵn (`BR-08.7`) sửa được bởi nhân sự đã soạn trong cùng phiên hay giai đoạn triển khai, và mọi lần sửa vẫn chờ xác nhận như lúc soạn. Khi lời mời đang giữ chỗ bản ghi (`BR-35.10`): đổi Đơn vị chính hoặc quản lý trực tiếp hiện số bản ghi bị ảnh hưởng và người sẽ thấy/không còn thấy chúng; đổi Đơn vị chính còn đòi người sửa có ô Gán của từng loại bản ghi đang giữ chỗ bao phủ cả đơn vị cũ và đơn vị mới, nếu không thì bị từ chối kèm số bản ghi; không bỏ trống được Đơn vị chính; màn hình nêu số bản ghi sẽ chuyển theo. `FEAT-11` và `BR-11.5` không áp cho thành viên chưa chấp nhận. Mọi lần sửa ghi nhật ký với giá trị trước và sau; sửa làm rộng quyền ghi như thao tác nới rộng (`BR-41.5`).

  **Lý do nghiệp vụ:** sửa lời mời là cách nhanh để chữa một lựa chọn sai mà không phải thu hồi và thả bản ghi giữ chỗ ra hàng đợi; nhưng nếu chỉ xét giá trị mới, một người phụ trách đơn vị có thể kéo lời mời và bản ghi giữ chỗ của đơn vị khác về đơn vị mình để xem.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-09.1.1` | Màn hình mời | Mở danh sách cấp bậc | Chỉ có Quản trị viên và Thành viên; không có Chủ sở hữu |
| `AC-09.2.1` | Người mời là Thành viên có quyền Mời người dùng | Mở danh sách cấp bậc | Lựa chọn Quản trị viên bị vô hiệu kèm giải thích "Chỉ Chủ sở hữu hoặc Quản trị viên" |
| `AC-09.3.1` | Người mời có (Cơ hội, Xem) = Đơn vị của mình, không giữ Quyền Ủy thác | Chọn vai trò có (Cơ hội, Xem) = Toàn workspace | Vai trò đó hiển thị là không chọn được kèm ô vượt trần trước khi gửi; nếu vẫn gửi bằng đường khác thì bị từ chối |
| `AC-09.5.1` | Người mời thuộc phòng Nhân sự | Mời người mới, bỏ trống Đơn vị chính | Người mới ở trạng thái "Chưa có đơn vị", không bị xếp vào phòng Nhân sự; `FEAT-03` hiện cảnh báo |
| `AC-09.6.1` | `CFG-09-02` = Chỉ xem bản ghi được giao | Mở màn hình mời | Vai trò Chỉ xem bản ghi được giao được chọn sẵn và hiển thị; người mời bỏ chọn được |
| `AC-09.6.3` | Đơn vị "Kinh doanh" có vai trò gợi ý Nhân viên Kinh doanh | Mời người mới, chọn Đơn vị chính "Kinh doanh" | Vai trò Nhân viên Kinh doanh được chọn sẵn thay cho vai trò của `CFG-09-02` |
| `AC-09.10.1` | Dán 12 email, chọn Đơn vị "Hỗ trợ" | Gửi | 12 lời mời được tạo với cùng lựa chọn; email đã là thành viên được báo riêng, không chặn các email còn lại |
| `AC-09.11.1` | 10 lời mời đang Chờ gửi | Workspace chuyển sang trả phí | 10 thư được gửi ngay; hạn lời mời tính từ lúc gửi |
| `AC-09.6.2` | `CFG-09-02` = Không chọn sẵn | Gửi lời mời không chọn vai trò | Nút gửi bị vô hiệu, hiển thị "Chọn ít nhất một vai trò" |
| `AC-09.7.1` | Lời mời gửi tới email đã có tài khoản | Người đó đăng nhập trước khi chấp nhận | Không thấy workspace trong danh sách; thấy lời mời đang chờ với nút Chấp nhận / Từ chối |
| `AC-09.7.2` | Lời mời đã gửi, `CFG-09-01` = 7 ngày, đang giữ chỗ 30 khách hàng | Người mời bấm Gửi lại ở ngày 5 | Đường dẫn cũ báo hết hiệu lực; đường dẫn mới hiệu lực thêm 7 ngày; 30 bản ghi vẫn giữ chỗ, không về hàng đợi |
| `AC-09.7.3` | Lời mời đang chờ | Người mời thu hồi | Đường dẫn trong email báo "Lời mời đã bị thu hồi"; thành viên không còn trong danh sách đang chờ |
| `AC-09.8.1` | Workspace đã dùng hết hạn mức người dùng; chính sách chạm trần của gói là chặn | Gửi lời mời mới | Từ chối, nêu rõ hạn mức hiện tại và cách xử lý (nâng gói, mua thêm chỗ, tạm ngưng người khác) |
| `AC-09.8.2` | Cùng tình huống; chính sách chạm trần là cho thêm và tính phí vượt | Gửi lời mời mới | Trước khi gửi, màn hình báo lời mời này phát sinh phí vượt; xác nhận thì gửi |
| `AC-09.9.1` | Email không thuộc tên miền trong `CFG-09-03` | Dán email vào ô mời | Từ chối ngay tại ô email trước khi gửi, nêu rõ các tên miền được phép |
| `AC-09.4.1` | Mời trưởng nhóm T và thành viên M trong cùng lượt, chọn T làm quản lý trực tiếp của M | Gửi | Gửi được; khi M đã chấp nhận mà T chưa, M chưa là cấp dưới của T trong phạm vi dữ liệu; khi cả hai chấp nhận, T thấy bản ghi của M |
| `AC-09.12.1` | Lời mời của N ghi Đơn vị chính "Kinh doanh – Đà Nẵng", giữ chỗ 1.200 khách hàng; P là người phụ trách "Kinh doanh – Hà Nội", có quyền Mời người dùng và ô Gán chỉ bao phủ đơn vị mình | P sửa Đơn vị chính của lời mời thành "Kinh doanh – Hà Nội" | Từ chối, nêu 1.200 bản ghi và lý do không bao phủ đơn vị cũ; lời mời và bản ghi không đổi |
| `AC-09.12.2` | Như trên; Quản trị viên thực hiện | Sửa Đơn vị chính thành "Kinh doanh – Hà Nội" | Màn hình báo 1.200 bản ghi sẽ chuyển theo; xác nhận thì bản ghi giữ chỗ thuộc "Kinh doanh – Hà Nội"; nhật ký có giá trị trước và sau |
| `AC-09.12.3` | Lời mời đang giữ chỗ bản ghi | Xoá trống Đơn vị chính | Không lưu được, kèm giải thích |
| `AC-09.12.4` | Lời mời giữ chỗ 30 bản ghi, `CFG-09-04` = 2 ngày, lời mời do người đã bị tạm ngưng gửi | Còn 2 ngày tới hạn | Người phụ trách đơn vị giữ chỗ nhận nhắc kèm số 30; người mời và người được mời (lời mời đang tạm treo) không nhận |
| `AC-09.12.5` | Lời mời giữ chỗ 200 khách hàng ở "Kinh doanh – Đà Nẵng"; Q có quyền Mời người dùng và năng lực vai trò bao phủ cả hai đơn vị Đà Nẵng và Hà Nội, nhưng (Khách hàng, Gán) = Đơn vị của mình ở Hà Nội | Q đổi Đơn vị chính thành "Kinh doanh – Hà Nội" | Từ chối theo ô Gán, nêu 200 bản ghi; lời mời không đổi |
| `AC-09.12.6` | Lời mời giữ chỗ 30 bản ghi, `CFG-09-01` = 7 ngày, `CFG-09-04` = 2 ngày; người mời đang hoạt động | Tới ngày 5 | Người được mời, người mời và người phụ trách đơn vị giữ chỗ đều nhận nhắc kèm số 30 |
| `AC-09.12.7` | `CFG-09-01` = 1 ngày, `CFG-09-04` = 2 ngày; lời mời có giữ chỗ | Gửi lời mời | Lời nhắc gửi ngay khi gửi lời mời |
| `AC-09.12.8` | Lời mời giữ chỗ; người mời bấm Gửi lại ở ngày 6 (`CFG-09-01` = 7 ngày) | Tới 2 ngày trước hạn mới | Lời nhắc được gửi lại theo hạn mới |
| `AC-09.12.9` | Lời mời giữ chỗ 30 bản ghi ghi quản lý trực tiếp M | Quản trị viên đổi quản lý trực tiếp thành M2 | Màn hình nêu 30 bản ghi, M không còn và M2 có thể thấy (theo `BR-39.6`); lưu thì nhật ký có giá trị trước và sau |

---

### FEAT-10 — Xem & tra cứu thành viên

**Mô tả nghiệp vụ:** Xem danh sách thành viên, tìm kiếm, lọc, xem hồ sơ và các nhóm một thành viên tham gia.

**Vai trò sử dụng chính:** Người có quyền Xem người dùng.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Xem danh sách thành viên có phân trang; tìm theo tên hoặc email; lọc theo trạng thái, cấp bậc, vai trò, đơn vị, nhóm.
2. Mở hồ sơ một thành viên: thông tin cá nhân, cấp bậc, vai trò, Đơn vị chính và kiêm nhiệm, quản lý trực tiếp, nhóm, chức danh trách nhiệm, trạng thái và lần đăng nhập gần nhất.

**Quy tắc nghiệp vụ:**

- **`BR-10.1` (Danh bạ tối thiểu cho mọi thành viên):** Mọi thành viên Đang hoạt động, kể cả không có quyền Xem người dùng, được xem tên, ảnh đại diện, email công việc và đơn vị của thành viên khác để giao việc và nhắc tên. Các thông tin còn lại của hồ sơ (vai trò, quyền, trạng thái, lịch sử đăng nhập) chỉ dành cho người có quyền Xem người dùng.

  **Lý do nghiệp vụ:** chọn người phụ trách, nhắc tên trong ghi chú là việc hằng ngày của mọi người; nhưng biết ai có quyền gì là thông tin bảo mật, giúp kẻ tấn công chọn mục tiêu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-10.1.1` | Thành viên không có quyền Xem người dùng | Chọn người phụ trách cho một khách hàng | Thấy danh sách tên, ảnh, email, đơn vị của thành viên Đang hoạt động |
| `AC-10.1.2` | Cùng thành viên đó | Thử mở trang quản trị hồ sơ của người khác | Không thấy vai trò, quyền, trạng thái hay lịch sử đăng nhập |
| `AC-10.1.3` | Người có quyền Xem người dùng | Lọc trạng thái Tạm ngưng | Chỉ hiện thành viên Tạm ngưng |

---

### FEAT-11 — Cập nhật thông tin, vị trí & vai trò của thành viên

**Mô tả nghiệp vụ:** Chỉnh hồ sơ, vai trò, Đơn vị chính và kiêm nhiệm, quản lý trực tiếp, kỹ năng và lượt thu hồi quyền riêng lẻ của một thành viên.

**Vai trò sử dụng chính:** Người có quyền Sửa người dùng.

**Điều kiện tiên quyết:** Thành viên không ở trạng thái Đã rời.

**Luồng chính:**

1. Mở hồ sơ thành viên, sửa các trường.
2. Khi đổi vai trò, nhóm hoặc vị trí, màn hình hiển thị trước sự khác biệt về quyền hiệu lực (thêm gì, mất gì).
3. Khi đổi Đơn vị chính, người sửa chọn cách xử lý bản ghi người đó đang phụ trách: **đi theo người** (bản ghi chuyển sang đơn vị mới; bản ghi công việc của hàng đợi không đi theo, `BR-35.11`), **bàn giao lại** cho người được chọn ở đơn vị cũ, hoặc với bản ghi công việc và bản ghi chờ phân công chưa chốt, **trả về hàng đợi** (`BR-35.13`), theo quy tắc bàn giao của từng phân hệ.
4. Khi đổi Đơn vị chính, màn hình đề xuất gỡ các vai trò là vai trò gợi ý của đơn vị cũ và thêm vai trò gợi ý của đơn vị mới, hiển thị khác biệt quyền; người sửa xác nhận hoặc bỏ từng đề xuất (`BR-11.9`).
5. Lưu; có thể đặt **ngày hiệu lực** trong tương lai cho thay đổi vị trí — mọi lựa chọn (cách xử lý bản ghi, vai trò) được chốt lúc lưu và thực hiện vào ngày hiệu lực.

**Luồng ngoại lệ:**

- Người sửa tự nới rộng quyền của chính mình (thêm vai trò, thêm nhóm, đổi vị trí làm phạm vi rộng hơn, gỡ một lượt thu hồi riêng lẻ) → bị từ chối, kể cả khi có quyền Sửa người dùng; phải nhờ người khác thực hiện. Tự thu hẹp quyền của mình thì được phép.
- Vai trò mới vượt năng lực của người sửa → bị từ chối (trừ Quyền Ủy thác, `BR-45.2`).
- Thêm một quyền đơn lẻ ngoài vai trò → không hỗ trợ; chỉ được **thu hồi riêng lẻ** một ô hoặc một quyền quản trị khỏi những gì vai trò và nhóm mang lại.
- Đơn vị hoặc quản lý trực tiếp không thuộc workspace, hoặc đã rời → từ chối.
- Chọn quản lý trực tiếp tạo thành vòng (A quản lý B, B lại quản lý A, trực tiếp hoặc gián tiếp) → từ chối (`BR-11.8`).
- Người sửa chọn chính mình làm quản lý trực tiếp của người khác → từ chối (`BR-11.8`).
- Thay đổi vi phạm quy tắc tách biệt nhiệm vụ (`FEAT-46`) → chặn hoặc cảnh báo theo `CFG-46-01`.

**Quy tắc nghiệp vụ:**

- **`BR-11.1` (Trường của tài khoản toàn hệ thống):** Thông tin thuộc tài khoản dùng chung cho mọi workspace (email đăng nhập, khoá tài khoản toàn hệ thống, vai trò của nhân sự vận hành nền tảng) chỉ Nhân sự vận hành nền tảng sửa được. Doanh nghiệp quản lý quyền truy cập workspace của mình qua Tạm ngưng (`FEAT-42`) và Gỡ (`FEAT-13`).

  **Lý do nghiệp vụ:** tài khoản dùng chung cho nhiều workspace; một doanh nghiệp không được khoá người đó khỏi workspace của doanh nghiệp khác.

- **`BR-11.2` (Không tự nới rộng quyền của mình):** Mọi thay đổi nới rộng quyền của chính người thực hiện bị từ chối, bất kể người đó giữ quyền gì, kể cả Quản trị viên. Riêng Chủ sở hữu vốn đã toàn quyền nên quy tắc này không có tác dụng với họ.

  **Lý do nghiệp vụ:** Nguyên tắc 2; mỗi lượt nới rộng quyền phải có người thứ hai chịu trách nhiệm.

- **`BR-11.3` (Các sự kiện chấm dứt phiên ngay):** Các sự kiện sau chấm dứt ngay mọi phiên làm việc của người bị ảnh hưởng **trong workspace đó**: gỡ khỏi workspace; tạm ngưng; khoá tài khoản toàn hệ thống (chấm dứt phiên ở mọi workspace); hạ cấp bậc từ Quản trị viên hoặc Chủ sở hữu xuống Thành viên; xoá một vai trò hoặc nhóm mà người đó đang giữ; thu hồi quyền tạm thời thủ công hoặc theo dây chuyền. Mọi thu hẹp quyền khác có hiệu lực từ thao tác kế tiếp, không cần đăng nhập lại.

  **Lý do nghiệp vụ:** Nguyên tắc 3. Với các sự kiện này, phần giao diện đã mở có thể vẫn hiển thị dữ liệu và thao tác mà người đó không còn quyền; chấm dứt phiên xoá khoảng trống đó.

- **`BR-11.4` (Kiêm nhiệm):** Một thành viên có tối đa một Đơn vị chính và không giới hạn Đơn vị kiêm nhiệm; mỗi Đơn vị kiêm nhiệm có thể có ngày kết thúc, tới ngày đó tự gỡ. Mỗi Đơn vị kiêm nhiệm chọn một trong hai mức: **Chỉ xem** (mặc định — đơn vị đó chỉ được tính vào phạm vi của thao tác Xem, và vào việc nhận việc từ hàng đợi của đơn vị đó theo `BR-35.11`) hoặc **Đầy đủ** (tính vào phạm vi của mọi thao tác). Chuỗi quản lý và báo cáo theo đơn vị dùng Đơn vị chính.

  **Lý do nghiệp vụ:** giám đốc vùng kiêm hai chi nhánh hoặc đội hỗ trợ dùng chung nhiều chi nhánh là thực tế phổ biến; không có kiêm nhiệm thì buộc phải cấp Toàn workspace, tức cấp thừa quyền. Mặc định Chỉ xem vì kiêm nhiệm thường để theo dõi, không để xoá hay xuất dữ liệu của chi nhánh khác.

- **`BR-11.5` (Đổi đơn vị phải chọn số phận bản ghi):** Đổi Đơn vị chính bắt buộc chọn, theo từng loại dữ liệu: "đi theo người", "bàn giao lại", hoặc — với bản ghi công việc và bản ghi chờ phân công chưa chốt — "trả về hàng đợi". Bản ghi công việc không bao giờ đi theo người (`BR-35.11`); với bản ghi công việc có thêm lựa chọn "giữ Người phụ trách" chỉ khi người đó vẫn là thành viên của đơn vị tiếp nhận sau khi đổi (qua Đơn vị kiêm nhiệm), bản ghi vẫn ở đơn vị tiếp nhận. Không có lựa chọn mặc định ngầm.

  **Lý do nghiệp vụ:** nhân viên chuyển chi nhánh mà khách hàng cũ đi theo thì đơn vị cũ mất khách; ở lại mà không có người nhận thì khách vô chủ. Chỉ người quản lý biết lựa chọn nào đúng.

- **`BR-11.6` (Thu hồi riêng lẻ chỉ thu hẹp):** Lượt thu hồi riêng lẻ chỉ thu hẹp một ô (về mức hẹp hơn) hoặc bỏ một quyền quản trị; nó thắng mọi vai trò và nhóm của người đó, nhưng không thắng quyền tạm thời đang hiệu lực đã được phê duyệt.

  **Lý do nghiệp vụ:** thu hồi riêng lẻ phục vụ ngoại lệ hẹp ("người này không được xuất dữ liệu"); quyền tạm thời đã qua hai người phê duyệt là quyết định có chủ đích mạnh hơn.

- **`BR-11.7` (Mọi thay đổi vị trí nới phạm vi đều chịu trần):** Thêm Đơn vị kiêm nhiệm, đổi Đơn vị chính, đặt quản lý trực tiếp làm rộng phạm vi của người được sửa hoặc của người quản lý; các thay đổi này chịu Nguyên tắc 1 và Nguyên tắc 2.

  **Lý do nghiệp vụ:** vị trí trong cây và chuỗi quản lý là nguồn phạm vi dữ liệu; nếu chỉ vai trò bị kiểm tra, đổi vị trí là đường vòng.

- **`BR-11.8` (Quản lý trực tiếp hợp lệ):** Quản lý trực tiếp không tạo vòng và không phải chính người thực hiện, trừ khi người thực hiện là Người có toàn quyền.

  **Lý do nghiệp vụ:** đặt mình làm quản lý của người khác là tự nới phạm vi qua chuỗi cấp dưới; vòng quản lý làm "cấp dưới" không xác định.

- **`BR-11.9` (Chuyển phòng có danh sách bước):** Khi đổi Đơn vị chính, hệ thống lập danh sách bước như `FEAT-43`, gồm: đổi vai trò (đề xuất chọn sẵn: gỡ vai trò gợi ý của đơn vị cũ, thêm của đơn vị mới, kèm khác biệt quyền); chọn quản lý trực tiếp mới; xử lý cấp dưới trực tiếp (giữ hay chuyển quản lý khác — không chọn sẵn); vị trí phụ trách đơn vị cũ; nhóm và Đơn vị kiêm nhiệm đang có; chức danh trách nhiệm; yêu cầu quyền tạm thời đang chờ người đó phê duyệt và đợt rà soát đang giao cho người đó; tiến trình đang chạy thay người đó (người khởi chạy được báo nếu phạm vi của tiến trình sẽ thu hẹp theo `BR-35.8`); cách xử lý bản ghi (`BR-11.5`, không chọn sẵn). Khi thay đổi có ngày hiệu lực, tới ngày đó hệ thống kiểm tra lại người nhận bàn giao và quản lý mới còn Đang hoạt động, người nhận bàn giao còn ô Sửa; không đạt thì **toàn bộ** thay đổi được giữ lại chưa thực hiện và người đã lập được báo để sửa. Lựa chọn "đi theo người" (`BR-11.5`) bị chặn với loại dữ liệu mà sau thay đổi người đó không còn ô Sửa khác Không có.

  **Lý do nghiệp vụ:** trưởng nhóm Kinh doanh chuyển sang Hỗ trợ mà vẫn là quản lý của 15 nhân viên Kinh doanh thì vẫn thấy toàn bộ dữ liệu của họ qua chuỗi cấp dưới — đúng loại quyền tồn dư mà kiểm toán an toàn thông tin hay nêu; còn bản ghi đi theo người không sửa được chúng thì bị đóng băng.

- **`BR-11.10` (Kiêm nhiệm hết hạn):** Khi một Đơn vị kiêm nhiệm hết hạn hoặc bị gỡ, bản ghi công việc người đó đã nhận từ hàng đợi của đơn vị ấy mà còn mở được đề xuất trả về hàng đợi cho quản lý trực tiếp và người phụ trách đơn vị ấy; riêng loại bản ghi mà SRS phân hệ khai báo cần phản hồi tức thời (như ở `BR-42.4`) được Hệ thống trả về hàng đợi ngay.

  **Lý do nghiệp vụ:** hết kiêm nhiệm thì người đó thôi làm việc cho đội kia; vé còn đứng tên họ sẽ không ai xử lý.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-11.2.1` | Quản trị viên A mở hồ sơ của chính mình | Thêm một vai trò | Thao tác bị vô hiệu kèm giải thích "Không tự nới rộng quyền của chính mình" |
| `AC-11.2.2` | Thành viên có quyền Sửa người dùng mở hồ sơ của chính mình | Bỏ bớt một vai trò | Lưu thành công |
| `AC-11.3.1` | B đang mở danh sách khách hàng | Quản trị viên hạ B từ Quản trị viên xuống Thành viên | Phiên của B trong workspace kết thúc ngay; B đăng nhập lại chỉ thấy theo vai trò |
| `AC-11.3.2` | C có (Khách hàng, Sửa) = Đơn vị của mình | Quản trị viên thu hẹp ô đó về Chỉ của mình | C không bị đăng xuất; lượt sửa kế tiếp trên khách hàng của đồng nghiệp bị từ chối |
| `AC-11.4.1` | D có Đơn vị chính "Hà Nội", kiêm nhiệm "Hải Phòng" tới ngày 30 | Ngày 31 | D không còn kiêm nhiệm Hải Phòng; không còn thấy khách hàng chỉ thuộc Hải Phòng |
| `AC-11.5.1` | Đổi Đơn vị chính của E | Bấm lưu mà chưa chọn cách xử lý bản ghi | Không lưu được, yêu cầu chọn cho từng loại dữ liệu: "đi theo người", "bàn giao lại", hoặc "trả về hàng đợi" (với loại có hàng đợi) |
| `AC-11.8.1` | Sửa hồ sơ của B | Chọn làm quản lý trực tiếp một người đang là cấp dưới gián tiếp của B | Người đó không chọn được, kèm giải thích tạo vòng quản lý |
| `AC-11.8.2` | Thành viên có quyền Sửa người dùng mở hồ sơ của C | Chọn chính mình làm quản lý trực tiếp của C | Lựa chọn bị vô hiệu kèm giải thích |
| `AC-11.1.2` | Quản trị viên mở hồ sơ một thành viên | Tìm trường email đăng nhập, khoá tài khoản toàn hệ thống | Các trường hiển thị chỉ đọc, kèm ghi chú do nhà cung cấp quản lý; có thao tác Tạm ngưng trong workspace |
| `AC-11.7.1` | A có (Khách hàng, Xem) = Đơn vị của mình ở "Hà Nội", không có toàn quyền | A thêm cho chính mình Đơn vị kiêm nhiệm "Hải Phòng" | Từ chối, nêu lý do không tự nới rộng |
| `AC-11.7.2` | B (không có toàn quyền) có mức Xem Khách hàng = Đơn vị của mình ở "Hà Nội" | Thêm cho C Đơn vị kiêm nhiệm "Miền Nam" mức Đầy đủ | Từ chối, nêu thay đổi làm rộng phạm vi của C ra ngoài năng lực của B |
| `AC-11.9.1` | D thuộc "Kinh doanh" (vai trò gợi ý Nhân viên Kinh doanh), chuyển sang "Hỗ trợ" | Đổi Đơn vị chính | Màn hình đề xuất gỡ Nhân viên Kinh doanh, thêm Nhân viên Hỗ trợ, đã chọn sẵn, kèm khác biệt quyền |
| `AC-11.9.2` | E là quản lý trực tiếp của 15 người ở "Kinh doanh", chuyển sang "Hỗ trợ" | Đổi Đơn vị chính | Danh sách bước có "15 cấp dưới" với lựa chọn chuyển quản lý khác; chưa xử lý thì không lưu được |
| `AC-11.9.3` | Vai trò mới của E không có Sửa trên Cơ hội | Chọn "đi theo người" cho 40 cơ hội | Lựa chọn bị vô hiệu cho Cơ hội, kèm giải thích; chỉ chọn được "bàn giao lại" |
| `AC-11.9.4` | Chuyển phòng hẹn ngày 15, người nhận bàn giao bị tạm ngưng ngày 12 | Ngày 15 | Không có thay đổi nào được thực hiện; người đã lập nhận thông báo nêu lý do |
| `AC-11.4.3` | D có Đơn vị chính "Hà Nội", kiêm nhiệm "Hải Phòng" mức Chỉ xem | Nhân viên ở "Hải Phòng" có mức Đơn vị của mình xem danh sách khách hàng | Không thấy khách hàng do D phụ trách |
| `AC-11.10.1` | D kiêm nhiệm "Hỗ trợ" tới ngày 30, đang giữ 4 vé mở của hàng đợi Hỗ trợ | Ngày 31 | Quản lý trực tiếp của D và người phụ trách "Hỗ trợ" nhận đề xuất trả 4 vé về hàng đợi |
| `AC-11.4.2` | E kiêm nhiệm "Hải Phòng" mức Chỉ xem, vai trò có Xoá Khách hàng = Đơn vị của mình | E thử xoá một khách hàng chỉ thuộc Hải Phòng | Thao tác xoá không có trên bản ghi đó; E xem được bản ghi |
| `AC-11.6.1` | F có vai trò cho Xuất Khách hàng; có lượt thu hồi riêng lẻ Xuất | F thử xuất | Bị từ chối; màn hình quyền hiệu lực ghi rõ nguồn là "Thu hồi riêng lẻ" |

---

### FEAT-12 — Đổi cấp bậc Quản trị viên ⇄ Thành viên

**Mô tả nghiệp vụ:** Nâng một Thành viên lên Quản trị viên hoặc hạ một Quản trị viên xuống Thành viên.

**Vai trò sử dụng chính:** Chủ sở hữu; Quản trị viên.

**Điều kiện tiên quyết:** Thành viên Đang hoạt động.

**Luồng chính:**

1. Chọn thành viên, chọn cấp bậc mới; khi hạ xuống Thành viên, chọn vai trò người đó sẽ giữ.
2. Nếu `CFG-12-01` bật cho thao tác nâng: yêu cầu chờ người duyệt thứ hai (`BR-47.3`).
3. Thay đổi có hiệu lực; mọi Người có toàn quyền nhận thông báo.

**Luồng ngoại lệ:**

- Tự đổi cấp bậc của chính mình → từ chối (riêng tự hạ: xem `BR-12.2`).
- Áp lên Chủ sở hữu → từ chối, nêu rõ dùng `FEAT-06`.
- Nâng khi đã đạt số Quản trị viên tối đa (`CFG-12-02`) → từ chối.
- Nâng vi phạm quy tắc tách biệt nhiệm vụ → theo `CFG-46-01`.

**Quy tắc nghiệp vụ:**

- **`BR-12.1` (Chỉ Người có toàn quyền):** Chỉ Chủ sở hữu hoặc Quản trị viên đổi được cấp bậc. Không quyền quản trị nào khác, kể cả Quyền Ủy thác, làm được việc này.

  **Lý do nghiệp vụ:** đồng bộ với `BR-09.2`; cấp bậc là trục toàn quyền, không thể giao qua quyền chi tiết.

- **`BR-12.2` (Không tự đổi, trừ tự rút):** Không tự nâng cấp bậc. Một Quản trị viên được tự hạ mình xuống Thành viên nếu workspace vẫn còn ít nhất một Người có toàn quyền khác Đang hoạt động.

  **Lý do nghiệp vụ:** tự rút quyền là thu hẹp, hợp lệ; nhưng không được để workspace chỉ còn Chủ sở hữu không khả dụng.

- **`BR-12.3` (Người duyệt thứ hai khi nâng):** Khi `CFG-12-01` bật, nâng lên Quản trị viên cần người duyệt thứ hai theo `BR-47.3`; khi workspace chỉ có một Người có toàn quyền Đang hoạt động, lượt nâng không cần người duyệt thứ hai và mọi Người có toàn quyền mới đều được thông báo. `FEAT-03` cảnh báo nhẹ khi workspace có từ hai Người có toàn quyền mà `CFG-12-01` tắt.

  **Lý do nghiệp vụ:** cấp quyền tạm thời vài tuần cần nhiều người duyệt mà trao toàn quyền vĩnh viễn chỉ cần một người là không nhất quán; doanh nghiệp tự quyết theo quy mô. Khi chỉ có một Người có toàn quyền, người duyệt thứ hai không thể tồn tại.

- **`BR-12.4` (Mọi lần nâng đều được báo):** Mỗi lần nâng hay hạ cấp bậc đều thông báo ngay cho mọi Người có toàn quyền.

  **Lý do nghiệp vụ:** một tài khoản Quản trị viên bị chiếm thường tạo thêm Quản trị viên để giữ chỗ; thông báo giúp phát hiện sớm.

- **`BR-12.5` (Hạ cấp bậc chấm dứt phiên):** Hạ xuống Thành viên chấm dứt phiên ngay theo `BR-11.3`.

  **Lý do nghiệp vụ:** người vừa bị hạ không được giữ toàn quyền thêm phút nào.

- **`BR-12.6` (Số Quản trị viên tối đa):** Lượt nâng làm vượt `CFG-12-02` bị từ chối.

  **Lý do nghiệp vụ:** mỗi Quản trị viên là một tài khoản vượt mọi trần; doanh nghiệp muốn giới hạn bề mặt rủi ro này.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-12.1.1` | Thành viên có quyền Sửa người dùng và Quyền Ủy thác | Mở hồ sơ người khác | Không có thao tác đổi cấp bậc |
| `AC-12.2.1` | Workspace chỉ có Chủ sở hữu và Quản trị viên A | A tự hạ xuống Thành viên | Thành công vì Chủ sở hữu vẫn Đang hoạt động |
| `AC-12.2.2` | Tài khoản Chủ sở hữu bị khoá toàn hệ thống; A là Người có toàn quyền duy nhất Đang hoạt động | A tự hạ | Từ chối, nêu rõ workspace phải còn ít nhất một Người có toàn quyền Đang hoạt động |
| `AC-12.3.1` | `CFG-12-01` bật | Quản trị viên A nâng B | Yêu cầu chờ người duyệt thứ hai; B chưa có toàn quyền cho tới khi được duyệt |
| `AC-12.4.1` | Quản trị viên A nâng B | Thao tác hoàn tất | Chủ sở hữu và mọi Quản trị viên khác nhận thông báo |
| `AC-12.5.1` | B là Quản trị viên đang đăng nhập | Chủ sở hữu hạ B | Phiên của B kết thúc ngay |
| `AC-12.6.1` | Đã có 5 Quản trị viên, `CFG-12-02` = 5 | Mở hồ sơ một Thành viên | Thao tác nâng lên Quản trị viên bị vô hiệu kèm giải thích giới hạn |
| `AC-12.3.2` | `CFG-12-01` bật, workspace chỉ có Chủ sở hữu | Chủ sở hữu nâng A lên Quản trị viên | Thành công ngay, không chờ người duyệt thứ hai |

---

### FEAT-13 — Gỡ thành viên khỏi workspace & xoá tài khoản

**Mô tả nghiệp vụ:** Gỡ một người khỏi workspace (tài khoản vẫn còn, có thể là thành viên workspace khác), hoặc xoá hẳn tài khoản khỏi hệ thống.

**Vai trò sử dụng chính:** Người có quyền Gỡ người dùng (gỡ khỏi workspace); Nhân sự vận hành nền tảng (xoá tài khoản).

**Điều kiện tiên quyết:** Với thành viên Đang hoạt động hoặc Tạm ngưng: quy trình rời workspace đã hoàn tất các bước bắt buộc (`FEAT-43`). Với thành viên Đang chờ chấp nhận: không có điều kiện.

**Luồng chính (Gỡ khỏi workspace):**

1. Chọn thành viên → bấm Gỡ.
2. Hệ thống kiểm tra các bước bắt buộc của `FEAT-43` đã hoàn tất.
3. Thành viên chuyển Đã rời; bị loại khỏi mọi nhóm; mọi quyền tạm thời, lượt cấp trên bản ghi, Quyền Ủy thác và chức danh trách nhiệm của người đó bị thu hồi; phiên trong workspace chấm dứt ngay. Gỡ thành viên Đang chờ chấp nhận thì bản ghi giữ chỗ cho người đó thành bản ghi chờ phân công theo `BR-35.10`.

**Luồng ngoại lệ:**

- Gỡ Chủ sở hữu → từ chối, phải chuyển nhượng trước (`FEAT-06`).
- Các bước bắt buộc của `FEAT-43` chưa hoàn tất → từ chối, liệt kê bước còn thiếu và lối dẫn tới từng bước.
- Gỡ một Người có toàn quyền khi người thực hiện không có toàn quyền → từ chối.
- Người muốn tự rời workspace gửi **yêu cầu rời**; yêu cầu tới quản lý trực tiếp và người có quyền Gỡ người dùng, người đó thực hiện `FEAT-43`.
- Xoá tài khoản của người đang là Chủ sở hữu của bất kỳ workspace nào → từ chối.

**Quy tắc nghiệp vụ:**

- **`BR-13.1` (Không có quyền mồ côi):** Gỡ thành viên dọn sạch mọi quyền mọc ra từ thành viên đó trong workspace, như liệt kê ở bước 3, gồm cả lời mời Đang chờ chấp nhận và Chờ gửi do người đó gửi chưa được xử lý ở `FEAT-43`.

  **Lý do nghiệp vụ:** Nguyên tắc 6; một lượt cấp trên bản ghi hay một lời mời còn sót của người đã rời là một cửa sau nếu không ai còn chịu trách nhiệm cho nó.

- **`BR-13.2` (Lịch sử giữ nguyên):** Gỡ thành viên không xoá nhật ký, không xoá tên người đó khỏi các bản ghi lịch sử (người tạo, người sửa); tên hiển thị kèm ghi chú "đã rời".

  **Lý do nghiệp vụ:** kiểm toán cần biết ai đã làm gì, kể cả người đã nghỉ.

- **`BR-13.3` (Mời lại là bắt đầu mới):** Người Đã rời được mời lại nhận lại từ đầu, không tự khôi phục vai trò, nhóm hay vị trí cũ.

  **Lý do nghiệp vụ:** khôi phục tự động trả lại quyền mà người mời lần này không quyết định.

- **`BR-13.4` (Xoá tài khoản chỉ khi không còn thuộc workspace nào):** Nhân sự vận hành nền tảng chỉ xoá được tài khoản khi tài khoản không còn là thành viên Đang hoạt động, Tạm ngưng hay Chờ duyệt gia nhập ở bất kỳ workspace nào; mỗi workspace phải gỡ người đó qua `FEAT-43` trước.

  **Lý do nghiệp vụ:** xoá tài khoản là đường vòng bỏ qua quy trình bàn giao của mọi workspace người đó đang làm việc.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-13.1.1` | A đã hoàn tất quy trình rời workspace, đang giữ 1 quyền tạm thời và 2 lượt cấp trên bản ghi | Gỡ A | A Đã rời; quyền tạm thời và 2 lượt cấp đều hiện "Đã thu hồi — thành viên rời workspace"; phiên của A chấm dứt |
| `AC-13.1.2` | A chưa bàn giao bản ghi | Gỡ A | Từ chối, liệt kê "Bàn giao bản ghi" là bước còn thiếu |
| `AC-13.2.1` | A đã rời | Mở một khách hàng A từng tạo | Trường người tạo hiển thị tên A kèm "đã rời" |
| `AC-13.3.1` | A đã rời, từng có vai trò Quản lý | Mời lại A | Màn hình mời trống, không chọn sẵn vai trò Quản lý |
| `AC-13.1.3` | Người được chọn là Chủ sở hữu | Bấm Gỡ | Từ chối, gợi ý chuyển nhượng quyền sở hữu |
| `AC-13.1.6` | Người rời đi còn 2 lời mời Chờ gửi chưa được xử lý ở quy trình rời | Gỡ | 2 lời mời bị thu hồi; người nhận không bao giờ nhận thư |
| `AC-13.4.1` | Tài khoản A còn là thành viên Tạm ngưng của W1 | Nhân sự vận hành nền tảng xoá tài khoản A | Từ chối, nêu W1 phải hoàn tất rời workspace trước |
| `AC-13.1.4` | Thành viên có quyền Gỡ người dùng, không có toàn quyền | Mở hồ sơ một Quản trị viên | Thao tác gỡ bị vô hiệu kèm giải thích |
| `AC-13.1.5` | B muốn tự rời | Bấm "Rời workspace" | Yêu cầu gửi tới quản lý trực tiếp và người có quyền Gỡ người dùng; B vẫn truy cập cho tới khi quy trình hoàn tất |

---

### FEAT-14 — Đặt lại mật khẩu & khoá tài khoản toàn hệ thống

**Mô tả nghiệp vụ:** Người quản trị workspace gửi yêu cầu đặt lại mật khẩu cho một thành viên. Nhân sự vận hành nền tảng khoá hoặc mở khoá tài khoản trên toàn hệ thống. Chính sách mật khẩu và cách đặt lại thuộc phần xác thực, ngoài phạm vi.

**Vai trò sử dụng chính:** Người có quyền Sửa người dùng (gửi yêu cầu đặt lại mật khẩu); Nhân sự vận hành nền tảng (khoá/mở khoá).

**Điều kiện tiên quyết:** Thành viên Đang hoạt động.

**Luồng chính:**

1. Gửi yêu cầu đặt lại mật khẩu → thành viên nhận email đặt lại.
2. Khoá tài khoản (chỉ Nhân sự vận hành nền tảng, kèm lý do) → mọi phiên ở mọi workspace chấm dứt ngay; ở workspace nào người này là Người phụ trách thanh toán, chức danh tạm về Chủ sở hữu như `FEAT-42`.

**Luồng ngoại lệ:**

- Tài khoản không đăng nhập bằng mật khẩu (đăng nhập qua nhà cung cấp danh tính bên ngoài) → không có thao tác đặt lại, hiển thị lý do.
- Gửi yêu cầu đặt lại mật khẩu cho Người có toàn quyền khi người gửi không có toàn quyền → từ chối.

**Quy tắc nghiệp vụ:**

- **`BR-14.1` (Không đặt lại mật khẩu của người quyền cao hơn):** Chỉ Người có toàn quyền mới gửi được yêu cầu đặt lại mật khẩu cho một Người có toàn quyền khác.

  **Lý do nghiệp vụ:** yêu cầu đặt lại mật khẩu là một bước trong chuỗi chiếm tài khoản; người quyền thấp không được kích hoạt chuỗi đó lên người quyền cao.

- **`BR-14.2` (Khoá là thao tác toàn hệ thống):** Khoá tài khoản tác động mọi workspace và chỉ Nhân sự vận hành nền tảng thực hiện, kèm lý do; mọi workspace có người này là thành viên được thông báo.

  **Lý do nghiệp vụ:** dùng cho sự cố tài khoản bị chiếm; doanh nghiệp muốn chặn người đó trong workspace của mình thì dùng Tạm ngưng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-14.1.1` | Thành viên có quyền Sửa người dùng | Gửi yêu cầu đặt lại mật khẩu cho một Quản trị viên | Thao tác bị vô hiệu kèm lý do |
| `AC-14.2.1` | Tài khoản A là thành viên W1, W2 | Nhân sự vận hành nền tảng khoá A | A bị đăng xuất khỏi cả W1, W2; Chủ sở hữu W1, W2 nhận thông báo kèm lý do |
| `AC-14.1.2` | Tài khoản đăng nhập qua nhà cung cấp danh tính bên ngoài | Mở hồ sơ | Không có nút đặt lại mật khẩu; có giải thích |

---

### FEAT-15 — Xem quyền hiệu lực & xem trước thay đổi quyền

**Mô tả nghiệp vụ:** Cho biết một người đang thực sự làm được gì, vì sao, và nếu đổi cấu hình thì sẽ thế nào.

**Vai trò sử dụng chính:** Người có quyền Xem người dùng (xem của người khác); mọi thành viên (xem của chính mình).

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Mở "Quyền hiệu lực" của một thành viên.
2. Hệ thống hiển thị ma trận hiệu lực (mức ở từng ô) và danh sách quyền quản trị; mỗi giá trị kèm **nguồn**: vai trò nào, nhóm nào (và nhóm cha nào), quyền tạm thời nào (kèm hạn), mức nền của workspace, lượt thu hồi riêng lẻ, trần theo gói.
3. Hiển thị riêng các **nguồn nới phạm vi ngoài vai trò**: phụ trách đơn vị (`BR-34.3`), Đơn vị kiêm nhiệm, thành viên đơn vị tiếp nhận của hàng đợi (`BR-35.11`), lượt cấp trên bản ghi (ai cấp, hết hạn khi nào, nguồn sinh), chính sách Cho phép đang áp dụng.
4. Xem trước: chọn thêm/bớt vai trò, nhóm, vị trí giả định → xem kết quả, không lưu.
5. Tra cứu ngược: chọn một bản ghi cụ thể → hệ thống trả lời người này có xem/sửa được bản ghi đó không và vì sao.

**Quy tắc nghiệp vụ:**

- **`BR-15.1` (Mọi quyền phải truy được nguồn):** Mọi mức và mọi quyền quản trị hiển thị đều kèm ít nhất một nguồn. Một quyền không truy được nguồn là lỗi phải sửa.

  **Lý do nghiệp vụ:** người quản trị không thu hồi được quyền nếu không biết nó đến từ đâu.

- **`BR-15.2` (Thành viên xem được quyền của chính mình):** Mọi thành viên xem được quyền hiệu lực của chính mình, kèm nguồn.

  **Lý do nghiệp vụ:** người dùng biết vì sao mình không làm được việc, hỏi đúng người, thay vì báo lỗi hệ thống.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-15.1.1` | A có (Khách hàng, Xem) = Toàn workspace từ vai trò Marketing và = Đơn vị của mình từ vai trò Kinh doanh | Mở quyền hiệu lực của A | Ô hiển thị Toàn workspace, nguồn ghi cả hai vai trò và nêu vai trò quyết định mức |
| `AC-15.1.2` | A là người phụ trách đơn vị "Miền Bắc", `CFG-34-02` bật | Mở quyền hiệu lực | Mục "nguồn ngoài vai trò" liệt kê "Phụ trách đơn vị Miền Bắc — xem toàn nhánh" |
| `AC-15.1.3` | A được cấp riêng quyền xem khách hàng K tới ngày 15 | Tra cứu ngược bản ghi K | Kết quả "Xem được — lượt cấp trên bản ghi do B cấp, hết hạn ngày 15" |
| `AC-15.2.1` | Thành viên thường | Mở "Quyền của tôi" | Thấy ma trận hiệu lực của chính mình kèm nguồn |
| `AC-15.1.4` | Xem trước bỏ vai trò Kinh doanh khỏi A | Bấm xem trước | Hiển thị các ô bị thu hẹp; không có gì được lưu |

---

### FEAT-16 — Tuỳ chỉnh cá nhân

**Mô tả nghiệp vụ:** Mỗi thành viên tự đặt ngôn ngữ và múi giờ hiển thị riêng, ưu tiên hơn mặc định của workspace (`FEAT-04`); đặt lại "theo workspace" bất kỳ lúc nào.

**Vai trò sử dụng chính:** Chính thành viên đó.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Mở Tuỳ chỉnh cá nhân, chọn ngôn ngữ và múi giờ, lưu.

**Quy tắc nghiệp vụ:**

- **`BR-16.1` (Chỉ đổi cách hiển thị):** Múi giờ cá nhân chỉ đổi cách hiển thị thời điểm; mọi mốc hết hạn và điều kiện thời gian vẫn tính theo múi giờ workspace (Mục 2.3).

  **Lý do nghiệp vụ:** cùng một quyền phải hết hạn cùng một lúc với mọi người.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-16.1.1` | Workspace múi giờ GMT+7; A chọn GMT+3 | A xem một quyền tạm thời hết hạn 23:59:59 ngày 10 (GMT+7) | Hiển thị 19:59:59 ngày 10 theo GMT+3; quyền hết hạn đúng thời điểm đó |
| `AC-16.1.2` | A đã tự đặt tiếng Anh | Chọn "Theo workspace" | Giao diện trở về ngôn ngữ của workspace |

---

### FEAT-42 — Tạm ngưng & kích hoạt lại thành viên

**Mô tả nghiệp vụ:** Chặn tạm thời một thành viên khỏi workspace (nghỉ dài hạn, điều tra nội bộ, chờ thôi việc) mà vẫn giữ nguyên vai trò, nhóm, vị trí, để kích hoạt lại không phải cấu hình từ đầu.

**Vai trò sử dụng chính:** Người có quyền Tạm ngưng người dùng; Hệ thống (tự tạm ngưng, tự kích hoạt lại).

**Điều kiện tiên quyết:** Thành viên Đang hoạt động.

**Luồng chính:**

1. Chọn thành viên → Tạm ngưng, nhập lý do, tuỳ chọn ngày tự kích hoạt lại.
2. Thành viên chuyển Tạm ngưng; phiên chấm dứt ngay (`BR-11.3`).
3. Ngay sau đó (trừ khi tạm ngưng là một bước của quy trình rời workspace, khi đó việc xử lý đi theo `FEAT-43`), màn hình đề nghị — như các thao tác riêng (`BR-42.4`) — xử lý bản ghi đang mở (giữ nguyên, chuyển tạm cho người xử lý thay, hoặc trả bản ghi của hàng đợi về hàng đợi) và chỉ định quản lý tạm thời cho cấp dưới.
4. Kích hoạt lại thủ công hoặc tới ngày đã hẹn → thành viên Đang hoạt động, quyền như trước khi tạm ngưng.

**Luồng ngoại lệ:**

- Tạm ngưng Chủ sở hữu → từ chối (Chủ sở hữu chỉ thay qua `FEAT-06`, `FEAT-07`).
- Tạm ngưng một Quản trị viên khi người thực hiện không có toàn quyền → từ chối.
- Tạm ngưng chính mình → từ chối (`BR-42.5`).
- Thành viên không đăng nhập liên tục quá số ngày tại `CFG-42-01` → Hệ thống tự tạm ngưng, lý do "không hoạt động"; người đó và quản lý trực tiếp được thông báo trước `CFG-42-02`. Không tự tạm ngưng nếu việc đó làm workspace không còn Người có toàn quyền Đang hoạt động; khi đó Người có toàn quyền nhận cảnh báo. Cảnh báo trước khi tự tạm ngưng gửi quản lý trực tiếp kèm danh sách bản ghi đang mở và lối chuyển tạm.
- Kích hoạt lại một Người có toàn quyền khi người thực hiện không có toàn quyền → từ chối.
- Người bị tạm ngưng là Người phụ trách thanh toán → tạm ngưng thực hiện ngay và không bị chặn; chức danh tạm về Chủ sở hữu theo [`billing-subscription-srs.md`](./billing-subscription-srs.md) `BR-03.5`, `BR-03.6`, Chủ sở hữu được thông báo để xác nhận hoặc chỉ định người khác. Nếu người thực hiện có thẩm quyền chỉ định chức danh này, màn hình cho chọn người thay ngay trong cùng lượt; việc chỉ định thất bại (kể cả do sự cố nhật ký) không làm hỏng việc tạm ngưng. Tài khoản Chủ sở hữu đang bị khoá thì `FEAT-03` cảnh báo Nghiêm trọng. Áp như nhau cho tự tạm ngưng vì không hoạt động (`CFG-42-01`).

**Quy tắc nghiệp vụ:**

- **`BR-42.1` (Giữ cấu hình, mất truy cập):** Thành viên Tạm ngưng không truy cập được workspace, không nhận thông báo nghiệp vụ, không được chọn làm người phụ trách mới, người phê duyệt hay người nhận bàn giao; vai trò, nhóm, vị trí và bản ghi đang phụ trách giữ nguyên.

  **Lý do nghiệp vụ:** người nghỉ thai sản sáu tháng quay lại làm việc ngay; nhưng trong thời gian nghỉ, không ai được giao việc mới cho họ.

- **`BR-42.2` (Quyền tạm thời không kéo dài):** Quyền tạm thời của người bị tạm ngưng vẫn trôi thời hạn; kích hoạt lại không gia hạn chúng.

  **Lý do nghiệp vụ:** quyền tạm thời được duyệt cho một khoảng thời gian cụ thể, không cho số ngày làm việc.

- **`BR-42.3` (Yêu cầu đang chờ của người bị tạm ngưng):** Yêu cầu quyền tạm thời do người bị tạm ngưng gửi bị huỷ; lượt phê duyệt họ đã đưa ra cho yêu cầu chưa đủ phê duyệt bị bỏ; bản ghi đang chờ họ xử lý theo quy tắc của phân hệ sở hữu bản ghi.

  **Lý do nghiệp vụ:** người đang bị chặn không được tiếp tục ảnh hưởng tới quyết định cấp quyền.

- **`BR-42.4` (Người xử lý thay & quản lý tạm thời khi nghỉ dài):** Tạm ngưng luôn thực hiện ngay và không bị chặn (Nguyên tắc 3). Sau đó, như một thao tác riêng (nới rộng, chịu `BR-41.5`), người thực hiện chọn, theo từng loại dữ liệu, giữ nguyên, trả về hàng đợi (`BR-35.13`) — trừ loại bản ghi mà SRS phân hệ khai báo là cần phản hồi tức thời (ví dụ hội thoại đang mở theo [`omnichat-srs.md`](./omnichat-srs.md)), được Hệ thống trả về hàng đợi ngay khi tạm ngưng và không có lựa chọn giữ nguyên — hoặc chuyển tạm bản ghi đang mở — bản ghi chưa ở trạng thái kết thúc theo phân hệ (vé chưa đóng, cơ hội chưa Thắng hay Thua, khách hàng đang được người đó phụ trách) — cho một người xử lý thay đạt `BR-43.2` và `BR-43.4`, chỉ định quản lý tạm thời cho các cấp dưới của người bị tạm ngưng, chịu `BR-11.7`, `BR-11.8`, và chỉ định người phụ trách đơn vị tạm thời nếu người bị tạm ngưng đang phụ trách một đơn vị, chịu `BR-21.5`, `BR-21.6`. Khi kích hoạt lại (thủ công hay tới ngày hẹn), quan hệ quản lý tạm thời tự kết thúc, cấp dưới trở về quản lý cũ, và đề xuất trả các bản ghi còn mở mà người xử lý thay vẫn đang phụ trách (không gồm bản ghi đã trả về hàng đợi hay đã được người khác nhận từ hàng đợi) về người cũ được gửi tới người xử lý thay và quản lý trực tiếp. Chức danh trách nhiệm của người bị tạm ngưng chuyển trống theo `FEAT-47` (Người phụ trách thanh toán tạm về Chủ sở hữu; chỉ định người khác là tuỳ chọn) và không tự trả lại khi kích hoạt lại.

  **Lý do nghiệp vụ:** cắt truy cập phải nhanh và không phụ thuộc việc tìm người thay; nhưng người nghỉ dài vẫn đứng tên vé đang mở thì cam kết phản hồi bị vi phạm, và quản lý nghỉ mà không có người thay thì đội không có ai duyệt, phân việc. Người xử lý thay và quản lý tạm thời là lượt nới quyền nên chịu cùng ràng buộc như bàn giao khi nghỉ việc.

- **`BR-42.5` (Không tự tạm ngưng):** Không ai tự tạm ngưng chính mình; người muốn nghỉ dài gửi yêu cầu tới quản lý trực tiếp.

  **Lý do nghiệp vụ:** tạm ngưng chấm dứt phiên và cần người khác kích hoạt lại; tự tạm ngưng làm workspace mất một người mà quản lý không biết, và bản ghi đang mở không có người xử lý thay.

- **`BR-42.6` (Lời mời của người bị tạm ngưng):** Lời mời Đang chờ chấp nhận và Chờ gửi do người bị tạm ngưng gửi bị **tạm treo** — không chấp nhận được, không gửi — cho tới khi người đó được kích hoạt lại hoặc lời mời được người khác có quyền Mời người dùng gửi lại dưới tên mình — vẫn là cùng lời mời, chỉ đổi người mời, nên bản ghi giữ chỗ không đổi; đường dẫn mới được gửi và thời hạn tính lại như gửi lại ở `FEAT-09`; người gửi lại phải có năng lực bao phủ vai trò và Đơn vị chính trong lời mời, nếu không thì không gửi lại được — nhất quán với liên kết mời (`BR-50.6`). Thời hạn lời mời vẫn trôi trong lúc tạm treo, và lời mời tạm treo vẫn tính vào hạn mức người dùng (`BR-09.8`).

  **Lý do nghiệp vụ:** người đang bị chặn vì nghi vấn không được tiếp tục đưa người vào workspace qua những lời mời đã gửi trước đó.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-42.1.1` | A Đang hoạt động, có 2 vai trò, 3 nhóm | Tạm ngưng A | A bị đăng xuất; đăng nhập lại thấy "Tài khoản của bạn đang tạm ngưng trong workspace này" |
| `AC-42.1.2` | A Tạm ngưng | Quản lý chọn người phụ trách cho một khách hàng | A không có trong danh sách chọn |
| `AC-42.1.3` | A Tạm ngưng, hẹn kích hoạt lại ngày 01/03 | Ngày 01/03 | A Đang hoạt động với đúng 2 vai trò, 3 nhóm như trước |
| `AC-42.2.1` | A có quyền tạm thời hết hạn ngày 10; bị tạm ngưng ngày 05 | Kích hoạt lại ngày 12 | Quyền tạm thời đã hết hạn, không được khôi phục |
| `AC-42.1.4` | `CFG-42-01` = 90 ngày | A không đăng nhập 83 ngày | A và quản lý trực tiếp nhận cảnh báo; tới ngày 90 A tự Tạm ngưng với lý do "không hoạt động" |
| `AC-42.3.1` | A đã duyệt 1 trong 2 lượt cho yêu cầu Y | A bị tạm ngưng | Yêu cầu Y trở về 0 lượt phê duyệt |
| `AC-42.4.1` | A có 12 vé đang mở | Tạm ngưng A, chọn chuyển tạm cho B tới ngày kích hoạt lại | 12 vé chuyển cho B; khi kích hoạt lại A, màn hình đề xuất trả các vé còn mở về A |
| `AC-42.5.1` | A mở hồ sơ của chính mình | Tìm thao tác Tạm ngưng | Không có; có lối "Gửi yêu cầu nghỉ dài" tới quản lý trực tiếp |
| `AC-42.6.1` | T gửi lời mời cho U rồi bị tạm ngưng | U bấm Chấp nhận | Báo lời mời đang tạm treo; khi T được kích hoạt lại, U chấp nhận được |
| `AC-42.4.2` | M (có 6 cấp dưới) bị tạm ngưng không có ngày hẹn | Chỉ định N làm quản lý tạm thời; sau đó kích hoạt lại M thủ công | 6 người là cấp dưới của N trong thời gian M nghỉ; khi M được kích hoạt lại, họ trở về là cấp dưới của M |
| `AC-42.4.3` | Người có quyền Tạm ngưng người dùng, (Khách hàng, Sửa) = Chỉ của mình | Tạm ngưng P rồi chọn chính mình làm người xử lý thay cho khách hàng của P | Tạm ngưng thực hiện; chính mình không có trong danh sách người xử lý thay |
| `AC-42.4.4` | P bị tạm ngưng, người thực hiện chọn "giữ nguyên" cho 80 khách hàng | Mở Sức khỏe phân quyền | Không có cảnh báo về bản ghi đang mở của P |
| `AC-42.1.8` | Q là Người phụ trách thanh toán; người thực hiện có quyền Tạm ngưng người dùng nhưng không có thẩm quyền chỉ định chức danh | Tạm ngưng Q | Q bị tạm ngưng ngay; Chủ sở hữu trở thành Người phụ trách thanh toán và nhận thông báo xác nhận |
| `AC-42.1.9` | Q là Người phụ trách thanh toán, `CFG-42-01` bật, Q không đăng nhập quá ngưỡng | Tới hạn | Q bị tự tạm ngưng; Chủ sở hữu trở thành Người phụ trách thanh toán và nhận thông báo |
| `AC-42.1.10` | Chủ sở hữu tạm ngưng Q (Người phụ trách thanh toán) và chọn V làm người thay trong cùng lượt | Xác nhận | Q bị tạm ngưng; V là Người phụ trách thanh toán |
| `AC-42.1.5` | Tài khoản Chủ sở hữu bị khoá toàn hệ thống; A là Người có toàn quyền duy nhất còn lại và sắp chạm `CFG-42-01` | Tới hạn | A không bị tự tạm ngưng; A nhận cảnh báo |
| `AC-42.1.6` | Quản trị viên C đang Tạm ngưng | Thành viên có quyền Tạm ngưng người dùng mở hồ sơ C | Thao tác kích hoạt lại bị vô hiệu kèm giải thích |
| `AC-42.1.7` | D đang Tạm ngưng | Người có quyền bấm Kích hoạt lại | D Đang hoạt động ngay, đăng nhập được với vai trò như trước |

---

### FEAT-43 — Rời workspace có bàn giao (quy trình nghỉ việc)

**Mô tả nghiệp vụ:** Quy trình bắt buộc trước khi gỡ một thành viên Đang hoạt động hoặc Tạm ngưng, để không bản ghi, cấp dưới, đơn vị hay quy trình nào bị bỏ ngỏ.

**Vai trò sử dụng chính:** Người có quyền Gỡ người dùng; Quản lý trực tiếp của người rời đi (thực hiện bàn giao).

**Điều kiện tiên quyết:** Thành viên không phải Chủ sở hữu.

**Luồng chính:**

1. Mở "Rời workspace" cho thành viên; lựa chọn "Tạm ngưng ngay" được chọn sẵn để chặn truy cập trong lúc bàn giao.
2. Hệ thống lập danh sách bước, mỗi bước kèm số lượng đối tượng:
   - **Bản ghi đang phụ trách** theo từng loại dữ liệu → chọn người nhận (một người cho tất cả, hoặc theo từng loại); với bản ghi công việc (vé, hội thoại) và bản ghi chờ phân công chưa chốt (khách hàng tiềm năng), chọn được "trả về hàng đợi" của đơn vị tiếp nhận; việc bàn giao thực hiện theo quy tắc của phân hệ sở hữu (ví dụ [`contacts-srs.md`](./contacts-srs.md) `FEAT-34`).
   - **Cấp dưới trực tiếp** (gồm cả người được mời đang chờ ghi người này là quản lý) → chọn quản lý trực tiếp mới; có hiệu lực ngay khi bước được xác nhận, không chờ tới lúc Gỡ.
   - **Vị trí phụ trách đơn vị** → chọn người phụ trách thay thế hoặc để trống (đơn vị khi đó xuất hiện trong cảnh báo `FEAT-03`).
   - **Chức danh trách nhiệm** người này đang giữ khi bắt đầu quy trình — kể cả chức danh đã chuyển trống hay tạm về Chủ sở hữu do lượt "Tạm ngưng ngay" của chính quy trình này → chọn người nhận thay hoặc, với chức danh khác Người phụ trách thanh toán, chọn **để trống** (`FEAT-47` cho phép, `FEAT-03` cảnh báo) — một quyết định tường minh; giữ nguyên người đang tạm giữ cũng phải được xác nhận. Riêng Người phụ trách thanh toán: bước này chuyển tới Chủ sở hữu để chỉ định theo [`billing-subscription-srs.md`](./billing-subscription-srs.md) `BR-03.5`, `BR-03.6`; chưa xong thì chưa Gỡ được; tài khoản Chủ sở hữu đang bị khoá thì `FEAT-03` cảnh báo và gợi ý khôi phục quyền sở hữu (`FEAT-07`).
   - **Yêu cầu quyền tạm thời đang chờ người này phê duyệt**, **đợt rà soát đang giao cho người này** → chuyển cho người khác.
   - **Lời mời đích danh và liên kết mời do người này tạo** còn hiệu lực → thu hồi, hoặc người thực hiện gửi lại dưới tên mình (chịu năng lực của chính mình).
   - **Tiến trình đang chạy thay người này** (chiến dịch, quy tắc tự động hoá) → chuyển người khởi chạy hoặc dừng.
3. Khi mọi bước bắt buộc hoàn tất → nút Gỡ khả dụng (`FEAT-13`).
4. Hệ thống lưu **biên bản rời workspace**: các bước, người nhận, thời điểm, người thực hiện.

**Luồng ngoại lệ:**

- Người nhận được chọn không Đang hoạt động hoặc không đủ quyền nhận loại bản ghi đó → từ chối người nhận đó, nêu rõ lý do.
- Bước nào không có đối tượng → tự đánh dấu hoàn tất. Đối tượng của mỗi bước được xác định tại thời điểm bắt đầu quy trình, không bị lượt tạm ngưng trong cùng quy trình làm mất.

**Quy tắc nghiệp vụ:**

- **`BR-43.1` (Không gỡ khi còn việc dở):** Chỉ được gỡ khi mọi bước bắt buộc đã hoàn tất. Bản ghi đang phụ trách, cấp dưới trực tiếp, chức danh trách nhiệm và tiến trình đang chạy là bắt buộc; vị trí phụ trách đơn vị được phép để trống.

  **Lý do nghiệp vụ:** gỡ trước, bàn giao sau thì khách hàng vô chủ, cấp dưới mất chuỗi quản lý làm phạm vi dữ liệu của quản lý cấp trên sai, và chiến dịch tiếp tục chạy dưới tên người đã nghỉ.

- **`BR-43.2` (Người nhận phải nhận được):** Người nhận bản ghi phải Đang hoạt động và có ô Sửa trên loại dữ liệu đó khác Không có.

  **Lý do nghiệp vụ:** giao bản ghi cho người không sửa được thì bản ghi bị đóng băng.

- **`BR-43.3` (Biên bản không sửa được):** Biên bản rời workspace là bản ghi chỉ ghi, xuất được cho kiểm toán.

  **Lý do nghiệp vụ:** khi tranh chấp về khách hàng sau khi nhân viên nghỉ, doanh nghiệp cần bằng chứng ai đã nhận bàn giao gì.

- **`BR-43.4` (Không tự nhận bàn giao vượt quyền):** Người thực hiện chỉ chọn chính mình làm người nhận bản ghi khi, trên mọi ô bị ảnh hưởng của loại dữ liệu đó (Sửa, Xoá, Xuất, Gán và thao tác đặc thù), mức của chính mình đã bao phủ các bản ghi đó trước khi bàn giao; không tự nhận chức danh, và chỉ tự nhận cấp dưới khi họ vốn đã là cấp dưới gián tiếp của mình (không nới gì) — trường hợp khác do người khác thực hiện. Chủ sở hữu được miễn quy tắc này, như Nguyên tắc 2.

  **Lý do nghiệp vụ:** Nguyên tắc 2; nhận bản ghi về mình làm mọi ô mức Chỉ của mình phủ thêm chúng, nên quy trình nghỉ việc không được là cách nhận về mình quyền xoá hay xuất 120 khách hàng mà mình vốn không có.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-43.1.1` | A phụ trách 120 khách hàng, 15 cơ hội, có 4 cấp dưới | Mở Rời workspace | Thấy bước "120 khách hàng", "15 cơ hội", "4 cấp dưới" kèm trạng thái Chưa xong; nút Gỡ bị vô hiệu |
| `AC-43.1.2` | Đã bàn giao bản ghi và cấp dưới; A đang phụ trách đơn vị "Miền Nam" | Chọn để trống người phụ trách | Bước đánh dấu hoàn tất; nút Gỡ khả dụng; `FEAT-03` cảnh báo "Miền Nam" chưa có người phụ trách |
| `AC-43.2.1` | Bước bàn giao khách hàng | Chọn người nhận có (Khách hàng, Sửa) = Không có | Người đó không chọn được, kèm lý do |
| `AC-43.1.3` | A là người khởi chạy một chiến dịch đang gửi | Mở Rời workspace | Có bước "1 tiến trình đang chạy" bắt buộc chuyển người khởi chạy hoặc dừng |
| `AC-43.3.1` | Gỡ A xong | Người có quyền Xem báo cáo quyền mở biên bản | Thấy đủ các bước, người nhận, thời điểm; không có thao tác sửa |
| `AC-43.4.1` | F (có quyền Gỡ người dùng) có (Khách hàng, Sửa) = Chỉ của mình | Chọn chính F làm người nhận 120 khách hàng của người rời đi | F không có trong danh sách người nhận, kèm giải thích |
| `AC-43.1.4` | Người rời đi có 30 vé đang mở | Chọn "trả về hàng đợi" | 30 vé trở thành chưa có người phụ trách trong hàng đợi của đơn vị tiếp nhận |
| `AC-43.1.5` | Người rời đi còn 4 lời mời đang chờ | Mở Rời workspace | Có bước "4 lời mời đang chờ" với lựa chọn thu hồi hoặc gửi lại dưới tên người thực hiện |
| `AC-43.1.6` | R giữ chức danh Người phụ trách Bảo vệ Dữ liệu và Người phụ trách thanh toán; mở Rời workspace với "Tạm ngưng ngay" | Xem danh sách bước | Có bước chức danh cho cả hai; bước Người phụ trách thanh toán chờ Chủ sở hữu chỉ định; nút Gỡ bị vô hiệu cho tới khi cả hai xong |
| `AC-43.1.7` | Workspace chỉ có Chủ sở hữu và R; R giữ chức danh Người phụ trách Bảo vệ Dữ liệu | Chủ sở hữu mở Rời workspace cho R | Chọn được "để trống" hoặc tự nhận chức danh; Gỡ được sau khi chọn |

---

### FEAT-44 — Thao tác hàng loạt trên thành viên

**Mô tả nghiệp vụ:** Mời nhiều người từ tệp; tạo cây đơn vị từ tệp (tên, mã, đơn vị cha, người phụ trách); gán hoặc gỡ vai trò, nhóm, đơn vị, quản lý trực tiếp cho nhiều thành viên trong một lần. Đổi Đơn vị chính hàng loạt áp danh sách bước của `BR-11.9` cho từng dòng, với các lựa chọn chọn một lần cho cả lô (có thể chọn riêng theo loại dữ liệu); dòng nào không thỏa thì lỗi riêng dòng đó.

**Vai trò sử dụng chính:** Người có quyền tương ứng của từng thao tác đơn lẻ (Mời người dùng, Sửa người dùng, Quản lý thành viên nhóm).

**Điều kiện tiên quyết:** Mời từ tệp luôn khả dụng; các thao tác hàng loạt khác cần tính năng thao tác hàng loạt trong trần quyền (`FEAT-05`).

**Luồng chính:**

1. Chọn thao tác; tải tệp mẫu (đối với mời) hoặc chọn danh sách thành viên.
2. Hệ thống **kiểm tra từng dòng** theo đúng các quy tắc của thao tác đơn lẻ tương ứng và hiển thị bảng xem trước: dòng hợp lệ, dòng lỗi kèm lý do.
3. Người thực hiện xác nhận → các dòng hợp lệ được thực hiện; các dòng lỗi không thực hiện.
4. Hệ thống trả kết quả theo từng dòng và gắn cho cả lô một **mã lô** để tra trong nhật ký.

**Luồng ngoại lệ:**

- Một dòng vi phạm quy tắc (vượt năng lực, tự nới quyền, hết hạn mức người dùng, tách biệt nhiệm vụ) → chỉ dòng đó lỗi, các dòng khác vẫn thực hiện.
- Lô vượt giới hạn kích thước (`NFR-09`) → từ chối cả lô trước khi kiểm tra, đề nghị chia nhỏ.

**Quy tắc nghiệp vụ:**

- **`BR-44.1` (Không có đường tắt hàng loạt):** Mọi quy tắc của thao tác đơn lẻ áp nguyên văn cho từng dòng.

  **Lý do nghiệp vụ:** nếu thao tác hàng loạt bỏ qua kiểm tra trần năng lực, nó trở thành đường leo thang quyền dễ nhất.

- **`BR-44.2` (Từng dòng độc lập):** Kết quả từng dòng độc lập; lô không bị huỷ toàn bộ vì vài dòng lỗi.

  **Lý do nghiệp vụ:** đợt tuyển 50 người có 2 email gõ sai không nên chặn 48 người còn lại.

- **`BR-44.3` (Một mã lô trong nhật ký):** Mọi thay đổi trong một lô ghi nhật ký riêng từng dòng và cùng một mã lô.

  **Lý do nghiệp vụ:** kiểm toán cần cả chi tiết từng người lẫn khả năng xem lại "lô tái cơ cấu ngày 15" như một quyết định.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-44.1.1` | Tệp 50 dòng, 2 dòng có vai trò vượt năng lực người mời | Tải lên | Xem trước: 48 hợp lệ, 2 lỗi nêu rõ ô vượt trần |
| `AC-44.2.1` | Tình huống AC-44.1.1 | Xác nhận | 48 lời mời được gửi; 2 dòng lỗi không gửi; tệp kết quả liệt kê từng dòng |
| `AC-44.1.2` | Chọn 20 người, trong đó có chính người thực hiện | Gán thêm một vai trò | Dòng của chính người thực hiện lỗi "Không tự nới rộng quyền"; 19 dòng còn lại thực hiện |
| `AC-44.3.1` | Lô vừa thực hiện | Lọc nhật ký theo mã lô | Thấy đúng các dòng đã thực hiện, mỗi dòng một bản ghi |

---

### FEAT-50 — Liên kết mời & tự gia nhập theo tên miền email

**Mô tả nghiệp vụ:** Hai cách đưa nhiều người vào workspace nhanh mà không phải nhập từng email: một **liên kết mời** dùng chung, và cho phép người có email thuộc **tên miền công ty đã xác minh** tự xin gia nhập. Cả hai mặc định tắt.

**Vai trò sử dụng chính:** Người có quyền Mời người dùng (tạo liên kết); Chủ sở hữu hoặc Quản trị viên (bật tự gia nhập); người muốn gia nhập.

**Điều kiện tiên quyết:** Với tự gia nhập: tên miền email đã được xác minh theo [`onboarding-srs.md`](./onboarding-srs.md) `FEAT-20`.

**Luồng chính — liên kết mời:**

1. Tạo liên kết: chọn Đơn vị chính, vai trò (chọn sẵn theo `BR-09.6`), nhóm; đặt thời hạn và số lượt dùng tối đa (mặc định theo `CFG-50-01`); chọn cần duyệt (mặc định) hay không (`BR-50.5`).
2. Chia sẻ liên kết. Người mở liên kết đăng nhập hoặc tạo tài khoản (email đã xác minh), xác nhận gia nhập.
3. Không cần duyệt → thành viên Đang hoạt động ngay. Cần duyệt → thành viên **Chờ duyệt gia nhập** cho tới khi một người có quyền Mời người dùng và có năng lực bao phủ vai trò, nhóm của liên kết duyệt hoặc từ chối.

**Luồng chính — tự gia nhập theo tên miền:**

1. Bật tự gia nhập cho một hoặc nhiều tên miền đã xác minh ([`onboarding-srs.md`](./onboarding-srs.md) `FEAT-20`); chọn đơn vị, vai trò mặc định; chọn có cần duyệt hay không.
2. Người có email thuộc tên miền đó đăng nhập vào hệ thống → thấy workspace trong danh sách "Workspace bạn có thể tham gia" → bấm Tham gia.
3. Xử lý như liên kết mời ở bước 3.

**Luồng ngoại lệ:**

- Liên kết hết hạn, hết số lượt hoặc bị thu hồi → báo rõ, không cho gia nhập.
- Email chưa xác minh → yêu cầu xác minh trước.
- Vượt hạn mức người dùng → theo `BR-09.8`; khi chính sách là chặn, người tạo liên kết được thông báo.
- Email không thuộc `CFG-09-03` → không gia nhập được (`BR-09.9`).
- Người tạo liên kết bị tạm ngưng, rời workspace, hoặc không còn năng lực bao phủ vai trò của liên kết → liên kết mất hiệu lực; Người có toàn quyền được thông báo.

**Quy tắc nghiệp vụ:**

- **`BR-50.1` (Trần năng lực xét theo người tạo):** Vai trò và nhóm gắn với liên kết hoặc với tự gia nhập chịu trần năng lực của người tạo hoặc người bật, như một lời mời (`BR-09.3`). Liên kết và tự gia nhập không bao giờ trao cấp bậc Quản trị viên.

  **Lý do nghiệp vụ:** liên kết là lời mời gửi cho người chưa biết trước; nó không được thành đường cấp quyền cao hơn lời mời thông thường.

- **`BR-50.2` (Liên kết luôn có giới hạn):** Mỗi liên kết bắt buộc có thời hạn và số lượt dùng tối đa (mặc định `CFG-50-01`, không vượt trần hệ thống 30 ngày và 500 lượt); thu hồi được bất kỳ lúc nào.

  **Lý do nghiệp vụ:** liên kết bị chuyển tiếp ra ngoài là chuyện thường; giới hạn chặn thiệt hại khi điều đó xảy ra.

- **`BR-50.3` (Chỉ tên miền đã xác minh):** Tự gia nhập chỉ áp cho tên miền doanh nghiệp đã chứng minh sở hữu và email người gia nhập đã xác minh.

  **Lý do nghiệp vụ:** không xác minh thì bất kỳ ai tự khai một email thuộc tên miền công ty là vào được workspace.

- **`BR-50.4` (Người tạo và Người có toàn quyền đều được báo):** Mỗi lượt gia nhập qua liên kết hoặc tên miền thông báo ngay cho người tạo liên kết (hoặc người bật tự gia nhập), có trong bản tổng hợp hằng ngày gửi Người có toàn quyền, và ghi nhật ký thay đổi cấu hình quyền.

  **Lý do nghiệp vụ:** người quản trị phải biết ai vừa vào workspace mà không qua lời mời đích danh.

- **`BR-50.5` (Mặc định cần duyệt):** Liên kết mời mặc định cần duyệt; chỉ bỏ được duyệt khi `CFG-09-03` có danh sách tên miền. Tự gia nhập theo tên miền đã xác minh bỏ được duyệt. Liên kết chịu `BR-09.9`.

  **Lý do nghiệp vụ:** liên kết rò ra ngoài là chuyện thường; không duyệt và không giới hạn tên miền thì người lạ vào thẳng với vai trò đã gắn.

- **`BR-50.6` (Liên kết và tự gia nhập đi theo người tạo):** Liên kết mất hiệu lực khi người tạo bị tạm ngưng, rời workspace, mất quyền Mời người dùng, hoặc không còn năng lực bao phủ vai trò và nhóm của liên kết (gồm cả khi mất Quyền Ủy thác, `BR-45.4`). Cấu hình tự gia nhập theo tên miền bị **tạm tắt** khi người bật nó bị tạm ngưng, rời workspace hoặc không còn là Người có toàn quyền, và Người có toàn quyền được mời xác nhận lại; người xác nhận trở thành người bật mới.

  **Lý do nghiệp vụ:** Nguyên tắc 6; liên kết và cấu hình tự gia nhập là lời mời chưa trao xong của một người cụ thể.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-50.1.1` | Người có quyền Mời người dùng tạo liên kết | Mở danh sách cấp bậc | Không có lựa chọn Quản trị viên |
| `AC-50.2.1` | Liên kết 7 ngày, tối đa 20 lượt, đã dùng 20 | Người thứ 21 mở liên kết | Báo "Liên kết đã hết lượt"; không gia nhập được |
| `AC-50.2.2` | Liên kết đang hiệu lực | Thu hồi, rồi một người mở liên kết | Báo "Liên kết đã bị thu hồi" |
| `AC-50.3.1` | Tự gia nhập bật cho congty.vn đã xác minh | Người có email chưa xác minh @congty.vn đăng nhập | Không thấy workspace trong danh sách có thể tham gia cho tới khi xác minh email |
| `AC-50.3.2` | Thử bật tự gia nhập cho gmail.com | Lưu | Từ chối, nêu lý do tên miền email miễn phí |
| `AC-50.4.1` | Liên kết không cần duyệt | Một người gia nhập | Người tạo liên kết nhận thông báo; nhật ký có bản ghi kèm tên liên kết |
| `AC-50.5.1` | `CFG-09-03` không giới hạn | Tạo liên kết, tìm lựa chọn "Không cần duyệt" | Lựa chọn bị vô hiệu kèm giải thích cần khai báo tên miền được phép |
| `AC-50.5.2` | Liên kết cần duyệt; H gia nhập | Người có quyền Mời người dùng mở danh sách chờ duyệt, bấm Duyệt | H Đang hoạt động với đơn vị và vai trò của liên kết; từ chối thì H nhận thông báo và không có trong workspace |
| `AC-50.6.1` | A tạo liên kết rồi bị tạm ngưng | Một người mở liên kết | Báo liên kết không còn hiệu lực |
| `AC-50.6.2` | B bật tự gia nhập cho congty.vn rồi bị hạ từ Quản trị viên xuống Thành viên | Người có email @congty.vn đăng nhập | Không thấy workspace trong danh sách có thể tham gia; Người có toàn quyền nhận lời mời xác nhận lại cấu hình |
| `AC-50.6.3` | Nhân sự HR tạo liên kết mời rồi bị thu hồi quyền Mời người dùng | Một người mở liên kết | Báo liên kết không còn hiệu lực |
| `AC-50.5.3` | Người duyệt có năng lực không bao phủ vai trò của liên kết | Mở danh sách chờ duyệt | Nút Duyệt bị vô hiệu, nêu lý do |

---

### FEAT-51 — Làm việc trên nhiều workspace & cách ly phiên theo workspace

**Mô tả nghiệp vụ:** Một tài khoản có thể thuộc nhiều workspace. Người dùng chọn và chuyển workspace dễ dàng; mọi quyền, phiên và dữ liệu luôn gắn với đúng workspace đang dùng.

**Vai trò sử dụng chính:** Mọi người dùng; Hệ thống.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Người dùng đăng nhập ở trang đăng nhập chung hoặc ở địa chỉ riêng của một workspace.
2. Nếu thuộc nhiều workspace và đăng nhập ở trang chung → thấy danh sách workspace Đang hoạt động của mình (gần nhất lên đầu), kèm lời mời đang chờ và workspace có thể tham gia (`FEAT-50`).
3. Chọn workspace → vào làm việc; tên và biểu tượng workspace luôn hiển thị ở đầu màn hình.
4. Chuyển workspace từ bộ chọn ở đầu màn hình mà không cần đăng nhập lại.

**Luồng ngoại lệ:**

- Người dùng mở địa chỉ của một workspace mà mình không là thành viên Đang hoạt động → không thấy bất kỳ dữ liệu nào, kể cả tên thành viên; chỉ thấy thông báo không có quyền truy cập và lối về danh sách workspace của mình.
- Thành viên bị tạm ngưng hoặc gỡ ở workspace A trong khi đang dùng workspace B → chỉ phiên ở A chấm dứt, phiên ở B không ảnh hưởng.

**Quy tắc nghiệp vụ:**

- **`BR-51.1` (Quyền xét lại theo workspace ở mọi thao tác):** Mọi thao tác được xét trên tư cách thành viên và quyền của người dùng **trong workspace của thao tác đó**. Quyền ở workspace này không bao giờ có hiệu lực ở workspace khác, kể cả khi cùng tài khoản.

  **Lý do nghiệp vụ:** cách ly dữ liệu giữa các khách hàng là cam kết cốt lõi của dịch vụ; một tư vấn viên là Quản trị viên ở khách hàng A không được thấy gì của khách hàng B.

- **`BR-51.2` (Chấm dứt phiên theo đúng workspace):** Các sự kiện ở `BR-11.3` chấm dứt phiên trong đúng workspace phát sinh sự kiện, trừ khoá tài khoản toàn hệ thống.

  **Lý do nghiệp vụ:** doanh nghiệp A gỡ một người không được làm người đó mất việc ở doanh nghiệp B.

- **`BR-51.3` (Không lộ thông tin chéo workspace):** Mọi danh sách chọn người, chọn đơn vị, chọn nhóm, tìm kiếm và thông báo lỗi chỉ trả về dữ liệu của workspace hiện tại; thông báo lỗi không xác nhận sự tồn tại của bản ghi hay người dùng ở workspace khác.

  **Lý do nghiệp vụ:** chỉ một câu "email này đã thuộc workspace X" cũng làm lộ quan hệ khách hàng của người khác.

- **`BR-51.4` (Nhà cung cấp cũng bị cách ly):** Nhân sự vận hành nền tảng chỉ thấy dữ liệu nghiệp vụ của workspace đang có Phiên hỗ trợ hiệu lực (`FEAT-08`), từng workspace một.

  **Lý do nghiệp vụ:** một phiên hỗ trợ cho khách hàng A không được thành quyền xem đồng thời dữ liệu của khách hàng B.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-51.1.1` | A là Quản trị viên ở W1, Thành viên với vai trò Chỉ xem bản ghi được giao ở W2 | A chuyển sang W2, thử sửa một khách hàng | Bị từ chối; quyền ở W1 không có tác dụng |
| `AC-51.1.2` | A thuộc W1, W2 | Đăng nhập ở trang chung | Thấy W1, W2 và lời mời đang chờ; chọn W2 vào thẳng không đăng nhập lại |
| `AC-51.2.1` | A đang làm việc ở W1 và W2 trên hai thẻ trình duyệt | W1 gỡ A | Thẻ W1 chấm dứt phiên; thẻ W2 vẫn làm việc bình thường |
| `AC-51.3.1` | A không thuộc W3 | Mở địa chỉ của W3 | Chỉ thấy thông báo không có quyền truy cập và lối về danh sách workspace; không thấy tên thành viên hay dữ liệu nào |
| `AC-51.3.2` | Mời email đang thuộc workspace khác | Gửi lời mời | Lời mời được gửi bình thường; người mời không nhận bất kỳ thông tin nào về workspace khác |
| `AC-51.4.1` | Phiên hỗ trợ đang mở cho W1 | Nhân sự vận hành thử mở dữ liệu của W2 | Bị từ chối; chỉ có lựa chọn gửi đề nghị phiên hỗ trợ cho W2 |

---

## C. NHÓM

### FEAT-17 — Tạo & cấu hình nhóm (gồm phân cấp nhóm)

**Mô tả nghiệp vụ:** Tạo nhóm để cấp vai trò theo tập thể và gán cấu hình chung của Object Manager. Nhóm có thể lồng nhau: thành viên của nhóm con thừa hưởng vai trò của mọi nhóm cha.

**Vai trò sử dụng chính:** Người có quyền Quản lý nhóm.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Nhập tên (duy nhất trong workspace), mô tả, màu hiển thị; chọn nhóm cha (tuỳ chọn); chọn vai trò gán cho nhóm; chọn thành viên ban đầu (tuỳ chọn).
2. Hệ thống kiểm tra tên không trùng, nhóm cha tồn tại và không tạo vòng.
3. Hệ thống kiểm tra trần năng lực theo `BR-17.2`; màn hình hiển thị trước tổng quyền mà nhóm mang lại cho mỗi thành viên.
4. Lưu.

**Luồng ngoại lệ:**

- Tên trùng → từ chối.
- Nhóm cha không tồn tại hoặc tạo vòng (nhóm A là cha của B, giờ chọn B làm cha của A) → từ chối, nêu rõ chuỗi gây vòng.
- Độ sâu phân cấp nhóm vượt 5 cấp → từ chối.
- Gán quyền cho nhóm bằng cách chọn từng ô hoặc từng quyền quản trị trực tiếp → không hỗ trợ; nhóm chỉ mang vai trò.

**Quy tắc nghiệp vụ:**

- **`BR-17.1` (Nhóm khác sơ đồ tổ chức):** Nhóm là công cụ cộng tác và cấp quyền tập thể, độc lập với đơn vị tổ chức. Một người ở được nhiều nhóm; việc thuộc nhóm không ảnh hưởng tới đơn vị hay chuỗi quản lý.

  **Lý do nghiệp vụ:** "đội dự án ra mắt sản phẩm" gồm người của nhiều phòng; ép nhóm theo cây tổ chức sẽ không diễn đạt được.

- **`BR-17.2` (Trần năng lực khi cấu hình nhóm):** Người tạo hoặc sửa vai trò, nhóm cha hay thành viên của nhóm chỉ làm được nếu tổng quyền nhóm mang lại (gồm cả chuỗi nhóm cha) không vượt năng lực của chính họ. Đổi tên, mô tả, màu không bị kiểm tra. Quyền Ủy thác không miễn trừ ở đây (`BR-45.3`).

  **Lý do nghiệp vụ:** Nguyên tắc 1; gắn một nhóm con vào dưới một nhóm cha mạnh là cách cấp quyền gián tiếp.

- **`BR-17.3` (Nhóm chỉ mang vai trò):** Quyền của nhóm chỉ đến từ vai trò được gán.

  **Lý do nghiệp vụ:** một nơi duy nhất định nghĩa quyền (vai trò) thì một nơi duy nhất cần rà soát; quyền rải rác trên từng nhóm không rà soát nổi.

- **`BR-17.4` (Giới hạn độ sâu nhóm):** Nhóm lồng tối đa 5 cấp. Hằng số hệ thống.

  **Lý do nghiệp vụ:** phân cấp sâu hơn làm người quản trị không còn đoán được ai thừa hưởng gì, và quyền hiệu lực khó giải thích (`BR-15.1`).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-17.1.1` | Nhóm "Dự án X" | Thêm người của ba đơn vị khác nhau | Thành công; đơn vị của từng người không đổi |
| `AC-17.2.1` | Người tạo có (Cơ hội, Xem) = Đơn vị của mình | Gắn nhóm mới vào dưới nhóm cha có vai trò (Cơ hội, Xem) = Toàn workspace | Từ chối, nêu ô vượt trần và nhóm cha gây ra |
| `AC-17.2.2` | Cùng người tạo | Chỉ đổi màu nhóm cha đó | Lưu thành công |
| `AC-17.1.2` | A là cha của B | Chọn B làm cha của A | Từ chối, hiển thị chuỗi A → B → A |
| `AC-17.4.1` | Chuỗi nhóm đã sâu 5 cấp | Thêm nhóm con thứ 6 | Từ chối, nêu giới hạn 5 cấp |
| `AC-17.3.1` | Màn hình cấu hình nhóm | Tìm cách thêm từng ô hoặc quyền quản trị cho nhóm | Chỉ có lựa chọn vai trò; không có ô hay quyền quản trị rời |

---

### FEAT-18 — Quản lý thành viên nhóm

**Mô tả nghiệp vụ:** Thêm, gỡ và xem thành viên của nhóm.

**Vai trò sử dụng chính:** Người có quyền Quản lý thành viên nhóm.

**Điều kiện tiên quyết:** Người được thêm là thành viên Đang hoạt động hoặc Đang chờ chấp nhận của workspace.

**Luồng chính (thêm):**

1. Chọn nhóm, chọn một hoặc nhiều thành viên.
2. Hệ thống kiểm tra trần năng lực theo `BR-18.1`.
3. Thêm thành công; thêm lại người đã có trong nhóm không gây lỗi và không tạo trùng.

**Luồng chính (gỡ):** Gỡ ngay, không kiểm tra trần năng lực, trừ khi nhóm đang mang hạn chế (`BR-18.3`).

**Luồng ngoại lệ:**

- Người được thêm không thuộc workspace → từ chối, gợi ý mời trước.
- Tự thêm chính mình vào nhóm → từ chối (`BR-11.2`).
- Thêm vi phạm quy tắc tách biệt nhiệm vụ → theo `CFG-46-01`.

**Quy tắc nghiệp vụ:**

- **`BR-18.1` (Thêm vào nhóm là cấp quyền):** Thêm ai đó vào nhóm cấp cho họ toàn bộ quyền của nhóm và chuỗi nhóm cha, nên chịu trần năng lực như gán vai trò. Ngoại lệ: người giữ Quyền Ủy thác thêm được vào nhóm có vai trò nằm trong danh sách được giao của mình (`BR-45.2`), khi thao tác chỉ là thêm thành viên vào nhóm đã tồn tại.

  **Lý do nghiệp vụ:** thêm vào nhóm có sẵn mang rủi ro giống hệt gán một vai trò có sẵn.

- **`BR-18.2` (Gỡ luôn tự do):** Gỡ khỏi nhóm là thu hẹp quyền, không kiểm tra trần năng lực, có hiệu lực từ thao tác kế tiếp của người bị gỡ.

  **Lý do nghiệp vụ:** Nguyên tắc 3.

- **`BR-18.3` (Gỡ khỏi nhóm mang hạn chế là nới rộng):** Khi nhóm đang mang hạn chế — phân quyền trường hạn chế hơn, lượt chặn trên bản ghi, chính sách Từ chối nhắm vào nhóm — gỡ một người khỏi nhóm là nới rộng quyền của người đó: không tự gỡ được mình, và màn hình nêu rõ hạn chế nào sẽ không còn áp dụng.

  **Lý do nghiệp vụ:** nhóm không chỉ cấp quyền mà còn mang hạn chế; nếu gỡ luôn tự do, tự rời một nhóm bị hạn chế là cách lách hạn chế đó.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-18.1.1` | Nhóm "Sales Miền Bắc" mang vai trò vượt năng lực người thao tác, người đó không có Quyền Ủy thác | Thêm thành viên | Từ chối, nêu ô vượt trần |
| `AC-18.1.2` | Người giữ Quyền Ủy thác với danh sách gồm vai trò của nhóm | Thêm thành viên vào nhóm | Thành công |
| `AC-18.2.1` | A đang trong nhóm | Gỡ A | Thành công; lượt sửa kế tiếp của A trên dữ liệu mà chỉ nhóm cấp bị từ chối |
| `AC-18.3.1` | Nhóm "Hạn chế dữ liệu VIP" mang lượt chặn trên 10 khách hàng; A thuộc nhóm và có quyền Quản lý thành viên nhóm | A tự gỡ mình khỏi nhóm | Thao tác bị vô hiệu kèm giải thích; người khác gỡ A thì màn hình nêu 10 lượt chặn sẽ không còn áp cho A |
| `AC-18.1.3` | A đã trong nhóm | Thêm A lần nữa | Không lỗi, danh sách không trùng |

---

### FEAT-19 — Xem trước quyền của nhóm

**Mô tả nghiệp vụ:** Thử một cấu hình nhóm giả định (vai trò, nhóm cha) và xem ngay mỗi thành viên sẽ có quyền gì, không lưu.

**Vai trò sử dụng chính:** Người có quyền Quản lý nhóm.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Trên màn hình tạo hoặc sửa nhóm, chọn vai trò và nhóm cha giả định.
2. Hệ thống hiển thị ma trận quyền nhóm mang lại và danh sách thành viên sẽ được nới rộng quyền.

**Quy tắc nghiệp vụ:**

- **`BR-19.1` (Xem trước không cấp gì):** Xem trước không bị kiểm tra trần năng lực và không thay đổi dữ liệu.

  **Lý do nghiệp vụ:** người quản trị cần thấy hệ quả của cấu hình trước khi xin người có đủ quyền lưu giúp.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-19.1.1` | Người quản trị chọn nhóm cha giả định vượt năng lực của mình | Bấm xem trước | Thấy kết quả, có cảnh báo "Bạn không đủ quyền lưu cấu hình này"; không có gì được lưu |

---

### FEAT-20 — Xoá nhóm

**Mô tả nghiệp vụ:** Xoá hẳn một nhóm.

**Vai trò sử dụng chính:** Người có quyền Quản lý nhóm.

**Điều kiện tiên quyết:** Nhóm không có nhóm con.

**Luồng chính:**

1. Bấm xoá; hệ thống hiển thị số thành viên sẽ mất quyền do nhóm mang lại, các hạn chế nhóm đang mang sẽ không còn áp dụng (`BR-18.3` — xoá nhóm mang hạn chế là nới rộng, không làm được nếu người thực hiện thuộc nhóm), số quyền tạm thời đang cấp cho nhóm, số lượt cấp trên bản ghi dành cho nhóm, và các cấu hình Object Manager đang gán cho nhóm.
2. Xác nhận (gõ lại tên nhóm nếu số thành viên bị ảnh hưởng đạt `CFG-28-01`) → nhóm bị xoá.

**Luồng ngoại lệ:**

- Còn nhóm con → từ chối, yêu cầu xoá hoặc di chuyển nhóm con trước.
- Nhóm đang có thành viên trực tiếp → không chặn.

**Quy tắc nghiệp vụ:**

- **`BR-20.1` (Tính lại quyền ngay):** Quyền của mọi thành viên được tính lại ngay sau khi xoá.

  **Lý do nghiệp vụ:** Nguyên tắc 3.

- **`BR-20.2` (Chặn khi còn nhóm con):** Không xoá được nhóm còn nhóm con.

  **Lý do nghiệp vụ:** nhóm con thừa hưởng quyền từ nhóm cha; xoá nhóm cha làm quyền của cả nhánh đổi đột ngột, và nhóm con thành mồ côi mà người quản trị không chủ định.

- **`BR-20.3` (Không chặn khi còn thành viên):** Nhóm còn thành viên vẫn xoá được.

  **Lý do nghiệp vụ:** nhóm thường được lập cho một việc ngắn hạn (một chiến dịch, một dự án); buộc gỡ từng người trước khi xoá là gánh nặng không cần thiết, và màn hình xác nhận đã cho thấy số người bị ảnh hưởng.

- **`BR-20.4` (Dọn quyền mọc từ nhóm):** Xoá nhóm thu hồi ngay mọi quyền tạm thời cấp cho nhóm và mọi lượt cấp hoặc chặn trên bản ghi dành cho nhóm, và gỡ các cấu hình Object Manager gán cho nhóm.

  **Lý do nghiệp vụ:** Nguyên tắc 6.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-20.2.1` | Nhóm có 1 nhóm con | Xoá | Từ chối, nêu tên nhóm con |
| `AC-20.3.1` | Nhóm có 8 thành viên, `CFG-28-01` = 10 | Xoá | Màn hình xác nhận ghi "8 thành viên sẽ mất quyền do nhóm này mang lại"; chỉ cần đánh dấu xác nhận |
| `AC-20.3.2` | Nhóm có 25 thành viên, `CFG-28-01` = 10 | Xoá | Phải gõ lại đúng tên nhóm mới xoá được |
| `AC-20.4.1` | Nhóm đang nhận 1 quyền tạm thời | Xoá nhóm | Quyền tạm thời hiện "Đã thu hồi — nhóm bị xoá"; thành viên mất quyền đó ngay |
| `AC-20.1.1` | Nhóm có 8 thành viên, mang vai trò Xuất Khách hàng | Xoá nhóm, một thành viên ngay sau đó thử xuất | Bị từ chối (trừ khi có nguồn khác), trong thời hạn `NFR-04` |

---

## D. ĐƠN VỊ TỔ CHỨC

### FEAT-21 — Xây dựng & quản lý cây tổ chức

**Mô tả nghiệp vụ:** Tạo và duy trì sơ đồ tổ chức dạng cây, làm căn cứ cho phạm vi dữ liệu theo đơn vị (`FEAT-34`, `FEAT-35`).

**Vai trò sử dụng chính:** Người có quyền Xem sơ đồ tổ chức (xem); người có quyền Quản lý đơn vị tổ chức (tạo, sửa, di chuyển, xoá).

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Xem cây lồng nhau kèm số thành viên mỗi đơn vị, hoặc danh sách phẳng.
2. Tạo đơn vị: tên (duy nhất), mã đơn vị (tuỳ chọn, duy nhất nếu có), đơn vị cha (bỏ trống = đơn vị gốc), người phụ trách chính, các đồng phụ trách, vai trò gợi ý (tuỳ chọn, `BR-21.4`).
3. Sửa thông tin đơn vị.

**Luồng ngoại lệ:**

- Tên hoặc mã trùng → từ chối, thông báo nêu rõ trùng tên hay trùng mã và đơn vị đang dùng.
- Người phụ trách không phải thành viên Đang hoạt động của workspace → từ chối.
- Thao tác làm cây vượt 10 cấp → từ chối.

**Quy tắc nghiệp vụ:**

- **`BR-21.1` (Sơ đồ tổ chức không công khai mặc định):** Xem toàn bộ sơ đồ tổ chức cần quyền Xem sơ đồ tổ chức. Mọi thành viên vẫn thấy đơn vị của chính mình và của người khác ở mức danh bạ (`BR-10.1`).

  **Lý do nghiệp vụ:** cơ cấu tổ chức, quy mô từng phòng là thông tin nội bộ nhạy cảm (ví dụ trong giai đoạn tái cơ cấu).

- **`BR-21.2` (Người phụ trách phải là thành viên đang hoạt động):** Người phụ trách và đồng phụ trách phải là thành viên Đang hoạt động, hoặc Đang chờ chấp nhận trong cùng lượt mời (`BR-09.10`) — khi đó vị trí phụ trách chỉ có hiệu lực khi người đó Đang hoạt động; người đó từ chối hoặc lời mời hết hạn thì vị trí trở về trống và Chủ sở hữu được thông báo. Khi người phụ trách bị tạm ngưng hoặc rời workspace, vị trí được xử lý theo `FEAT-43` hoặc hiện cảnh báo `FEAT-03`.

  **Lý do nghiệp vụ:** người phụ trách có thể nhận quyền xem toàn nhánh (`BR-34.3`); để người đã rời giữ vị trí là để quyền đó tồn dư.

- **`BR-21.3` (Giới hạn 10 cấp):** Cây tổ chức tối đa 10 cấp. Hằng số hệ thống.

  **Lý do nghiệp vụ:** doanh nghiệp thực tế hiếm khi vượt 6 – 7 cấp; giới hạn bảo vệ thời gian tính phạm vi (`NFR-05`) và khả năng đọc hiểu sơ đồ.

- **`BR-21.4` (Vai trò gợi ý của đơn vị):** Mỗi đơn vị có thể khai báo một hoặc vài vai trò gợi ý; khi mời hoặc chuyển người vào đơn vị làm Đơn vị chính, các vai trò này được chọn sẵn (`BR-09.6`). Vai trò gợi ý chỉ là lựa chọn sẵn hiển thị cho người thao tác, không tự cấp quyền cho thành viên đang ở trong đơn vị.

  **Lý do nghiệp vụ:** với doanh nghiệp nhỏ, "vào đội Kinh doanh" gần như luôn đi kèm "làm Nhân viên Kinh doanh"; chọn sẵn theo đơn vị bỏ được một quyết định lặp lại ở mỗi lời mời, mà người mời vẫn thấy và đổi được.

- **`BR-21.5` (Đặt người phụ trách và di chuyển đơn vị không tự nới phạm vi):** Người thực hiện không được đặt chính mình làm người phụ trách hay đồng phụ trách; trừ Người có toàn quyền. Di chuyển đơn vị theo `BR-21.6`.

  **Lý do nghiệp vụ:** Nguyên tắc 2; người phụ trách có thể nhận quyền xem toàn nhánh (`BR-34.3`), và đơn vị con mở rộng phạm vi của mọi người ở đơn vị cha.

- **`BR-21.6` (Đặt người phụ trách và di chuyển đơn vị cho người khác):** Khi `CFG-34-02` bật, chỉ Người có toàn quyền đặt hay đổi người phụ trách và đồng phụ trách (kể cả khi tạo đơn vị từ tệp, `FEAT-44`). Di chuyển một đơn vị (`FEAT-22`) chỉ Người có toàn quyền thực hiện.

  **Lý do nghiệp vụ:** Nguyên tắc 1; đặt người khác phụ trách một đơn vị lớn hay chuyển một nhánh về dưới đơn vị khác làm rộng phạm vi của nhiều người trên mọi thao tác mà không xét được trên tập bản ghi — hai người thông đồng sẽ lách được trần năng lực. Tái cơ cấu cây là quyết định của người có toàn quyền.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-21.4.1` | Đơn vị "Hỗ trợ" đang có 5 thành viên với vai trò khác | Khai báo vai trò gợi ý Nhân viên Hỗ trợ và lưu | Vai trò của 5 thành viên không đổi; người được mời mới vào "Hỗ trợ" có Nhân viên Hỗ trợ chọn sẵn |
| `AC-21.5.1` | Thành viên có quyền Quản lý đơn vị tổ chức | Đặt chính mình làm người phụ trách đơn vị "Miền Nam" | Lựa chọn bị vô hiệu kèm giải thích |
| `AC-21.6.1` | `CFG-34-02` bật; B có quyền Quản lý đơn vị tổ chức, không có toàn quyền | Đặt C làm người phụ trách đơn vị gốc | Lựa chọn người phụ trách bị vô hiệu kèm giải thích |
| `AC-21.6.2` | D có quyền Quản lý đơn vị tổ chức và vai trò Kiểm toán | Tìm thao tác di chuyển đơn vị | Thao tác bị vô hiệu kèm giải thích |
| `AC-21.2.2` | Trưởng nhóm được đặt làm người phụ trách khi còn Đang chờ chấp nhận | Trưởng nhóm từ chối lời mời | Vị trí phụ trách trở về trống; Chủ sở hữu nhận thông báo; `FEAT-03` hiện cảnh báo |
| `AC-21.1.1` | Thành viên không có quyền Xem sơ đồ tổ chức | Mở sơ đồ tổ chức | Không thấy cây; hồ sơ của chính mình vẫn hiện tên đơn vị |
| `AC-21.2.1` | Tạo đơn vị | Chọn người phụ trách đang Tạm ngưng | Người đó không có trong danh sách chọn |
| `AC-21.3.1` | Cây đã sâu 10 cấp | Tạo đơn vị con ở cấp 11 | Từ chối, nêu giới hạn 10 cấp |
| `AC-21.1.2` | Đã có đơn vị "Kinh doanh" | Tạo đơn vị mới cùng tên | Từ chối, thông báo "Tên đơn vị đã được dùng bởi: Kinh doanh (mã KD)" |

---

### FEAT-22 — Di chuyển đơn vị trong cây

**Mô tả nghiệp vụ:** Chuyển một đơn vị (cùng toàn bộ đơn vị con) sang làm con của đơn vị khác.

**Vai trò sử dụng chính:** Người có toàn quyền (`BR-21.6`).

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Chọn đơn vị, chọn đơn vị cha mới.
2. Hệ thống hiển thị trước số thành viên có phạm vi dữ liệu thay đổi.
3. Xác nhận → di chuyển; phạm vi dữ liệu liên quan được tính lại theo `NFR-04`.

**Luồng ngoại lệ:**

- Chọn chính nó hoặc một đơn vị con cháu của nó làm cha → từ chối.
- Đơn vị cha đích không tồn tại → từ chối.
- Vị trí mới làm cây vượt 10 cấp (tính cả chiều sâu của nhánh đang di chuyển) → từ chối.

**Quy tắc nghiệp vụ:**

- **`BR-22.1` (Chỉ đổi cha mới là di chuyển):** Chỉ khi đơn vị cha thực sự đổi mới tính lại phạm vi; đổi tên, mô tả không kích hoạt việc này.

  **Lý do nghiệp vụ:** tránh tính lại phạm vi của hàng trăm người chỉ vì sửa một lỗi chính tả.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-22.1.1` | Chuyển "Chi nhánh Đà Nẵng" sang dưới "Miền Trung" | Xác nhận | Người phụ trách "Miền Trung" có phạm vi Đơn vị và các đơn vị con thấy được khách hàng của Đà Nẵng trong thời hạn `NFR-04` |
| `AC-22.1.2` | "A" là cha của "B" | Chọn "B" làm cha của "A" | Từ chối, nêu lý do tạo vòng |
| `AC-22.1.3` | Nhánh đang chuyển sâu 4 cấp, đích ở cấp 8 | Xác nhận | Từ chối vì vượt 10 cấp |
| `AC-21.5.2` | A (không có toàn quyền) có quyền Quản lý đơn vị tổ chức | Mở cây và tìm thao tác di chuyển đơn vị | Thao tác bị vô hiệu kèm giải thích chỉ Người có toàn quyền tái cơ cấu cây |

---

### FEAT-23 — Xoá đơn vị tổ chức

**Mô tả nghiệp vụ:** Xoá một đơn vị khỏi sơ đồ.

**Vai trò sử dụng chính:** Người có quyền Quản lý đơn vị tổ chức.

**Điều kiện tiên quyết:** Đơn vị không còn đơn vị con, không còn thành viên (chính hoặc kiêm nhiệm), không là đơn vị tiếp nhận của hàng đợi nào, không là đơn vị tiếp nhận mặc định (`CFG-35-01`), không còn bản ghi công việc chưa đóng hay bản ghi chờ phân công chưa gán (`BR-35.11`), và không là Đơn vị chính ghi trong lời mời nào chưa kết thúc (soạn sẵn, Chờ gửi, Đang chờ chấp nhận, tạm treo). Bản ghi công việc đã đóng của đơn vị bị xoá giữ tên đơn vị (`BR-23.3`) và chỉ người có mức Toàn workspace thấy.

**Luồng chính:**

1. Bấm xoá → hệ thống kiểm tra điều kiện → xoá.

**Luồng ngoại lệ:**

- Còn đơn vị con → từ chối, yêu cầu di chuyển hoặc xoá đơn vị con trước.
- Còn thành viên → từ chối, liệt kê thành viên cần chuyển.
- Đang là đơn vị tiếp nhận mặc định (`CFG-35-01`, ở cấp workspace hay ghi đè) → từ chối, nêu loại nguồn cần đổi mặc định.
- Còn bản ghi chờ phân công chưa gán, hoặc là Đơn vị chính trong lời mời đang chờ → từ chối, nêu số bản ghi và số lời mời.
- Đang là đơn vị tiếp nhận của hàng đợi hoặc còn bản ghi công việc chưa đóng → từ chối, nêu các hàng đợi cần đổi đơn vị tiếp nhận (`BR-35.12`, chỉ Người có toàn quyền) và số bản ghi.
- Là đơn vị gốc cuối cùng → từ chối (`BR-23.4`).

**Quy tắc nghiệp vụ:**

- **`BR-23.1` (Không xoá cưỡng bức):** Không có thao tác xoá kèm dọn dây chuyền.

  **Lý do nghiệp vụ:** xoá đơn vị làm mất phạm vi dữ liệu của mọi người trong đó; người quản trị phải xử lý tường minh từng người.

- **`BR-23.2` (Đơn vị mồ côi hiện như gốc):** Nếu một đơn vị có đơn vị cha không còn tồn tại, hệ thống hiển thị nó như đơn vị gốc và cảnh báo trong `FEAT-03`, thay vì ẩn đi.

  **Lý do nghiệp vụ:** ẩn đi là âm thầm làm mất cả một nhánh khỏi tầm nhìn của người quản trị.

- **`BR-23.3` (Bản ghi lịch sử giữ tên đơn vị):** Bản ghi nghiệp vụ và báo cáo đã chốt giữ tên đơn vị tại thời điểm phát sinh, kèm ghi chú "đã xoá".

  **Lý do nghiệp vụ:** báo cáo doanh số quý trước không được đổi chỉ vì đơn vị đã giải thể.

- **`BR-23.4` (Luôn còn đơn vị gốc):** Đơn vị gốc cuối cùng không xoá được.

  **Lý do nghiệp vụ:** `BR-02.2`.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-23.1.1` | Đơn vị có 3 thành viên kiêm nhiệm, không có thành viên chính | Xoá | Từ chối, liệt kê 3 người |
| `AC-23.1.2` | "Hỗ trợ Miền Trung" không còn thành viên nhưng là đơn vị tiếp nhận của một hộp thư | Xoá | Từ chối, nêu hộp thư cần đổi đơn vị tiếp nhận |
| `AC-23.1.3` | Đơn vị "Kinh doanh – Huế" là mặc định ghi đè của biểu mẫu cho chi nhánh Huế | Xoá | Từ chối, nêu mặc định cần đổi |
| `AC-23.1.4` | Đơn vị còn 40 khách hàng tiềm năng giữ chỗ cho một người chưa chấp nhận lời mời | Xoá | Từ chối, nêu 40 bản ghi và 1 lời mời |
| `AC-23.2.1` | Một đơn vị có đơn vị cha không còn tồn tại | Mở cây | Đơn vị hiển thị ở cấp gốc; `FEAT-03` có cảnh báo |
| `AC-23.4.1` | Workspace chỉ có một đơn vị gốc, không con, không thành viên | Xoá | Từ chối |
| `AC-23.3.1` | Đơn vị "Chi nhánh Huế" đã xoá; báo cáo doanh số quý trước đã chốt | Mở báo cáo | Vẫn hiển thị "Chi nhánh Huế (đã xoá)" ở các dòng cũ |

---

## E. VAI TRÒ & QUYỀN

### FEAT-24 — Danh mục quyền của workspace

**Mô tả nghiệp vụ:** Xem toàn bộ danh mục quyền có thể đưa vào vai trò: ma trận loại dữ liệu × thao tác và danh sách quyền quản trị, kèm trạng thái khả dụng theo trần quyền (`FEAT-05`).

**Vai trò sử dụng chính:** Người có quyền Quản lý vai trò.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Mở Danh mục quyền.
2. Xem các dòng loại dữ liệu (Khách hàng, Công ty, Cơ hội, Vé hỗ trợ, Công việc, Hội thoại, Chiến dịch và các loại khác do phân hệ khai báo) và các cột thao tác: Xem, Tạo, Sửa, Xoá, Xuất, Nhập, Gán người phụ trách, cùng thao tác đặc thù của từng loại do SRS phân hệ khai báo (ví dụ Phát sóng chiến dịch theo [`campaigns-srs.md`](./campaigns-srs.md)).
3. Xem danh sách quyền quản trị, mỗi quyền kèm mô tả nghiệp vụ.

**Danh mục quyền quản trị:**

| Nhóm | Quyền quản trị |
| --- | --- |
| Người dùng | Xem người dùng; Mời người dùng; Sửa người dùng; Tạm ngưng người dùng; Gỡ người dùng |
| Nhóm | Quản lý nhóm; Quản lý thành viên nhóm |
| Đơn vị tổ chức | Xem sơ đồ tổ chức; Quản lý đơn vị tổ chức |
| Vai trò | Quản lý vai trò (gồm quy tắc tách biệt nhiệm vụ) |
| Quyền tạm thời | Yêu cầu quyền tạm thời; Phê duyệt quyền tạm thời; Thu hồi quyền tạm thời |
| Chính sách & bản ghi | Quản lý chính sách truy cập; Quản lý quyền trên bản ghi |
| Workspace | Quản lý cấu hình workspace (cấu hình chung, mức nền, sức khỏe cấu hình) |
| Kiểm soát | Xem nhật ký quyền; Quản lý rà soát quyền; Xem báo cáo quyền |

Các quyền quản trị của phân hệ nghiệp vụ (ví dụ cấu hình chấm điểm tiềm năng) do SRS phân hệ khai báo và cùng xuất hiện trong danh mục.

**Quy tắc nghiệp vụ:**

- **`BR-24.3` (Cơ bản trước, Nâng cao khi cần):** Khu vực quản trị phân quyền trình bày mặc định các thao tác cơ bản: mời và quản lý thành viên, đơn vị, nhóm, vai trò ở chế độ Cơ bản (`BR-25.4`). Chính sách truy cập, quyền trên bản ghi, quyền tạm thời, tách biệt nhiệm vụ, rà soát quyền và chế độ Chi tiết của ma trận được gom trong khu "Nâng cao", luôn truy cập được bởi người có quyền tương ứng, không cần bật thêm.

  **Lý do nghiệp vụ:** doanh nghiệp 25 người cần mời đội và chia vai trò trong vài phút; đặt mười khái niệm phân quyền ngang hàng trên cùng một màn hình làm họ dừng lại hoặc cấu hình sai. Doanh nghiệp lớn vẫn có đủ công cụ mà không phải bật cờ ẩn.

- **`BR-24.1` (Quyền Ủy thác không nằm trong vai trò):** Quyền Ủy thác không có trong danh mục và không đưa vào vai trò được; chỉ cấp trực tiếp cho từng người qua `FEAT-45`.

  **Lý do nghiệp vụ:** Quyền Ủy thác miễn trần năng lực; nếu nằm trong vai trò, mọi người được gán vai trò đó đều có nó, và Người có toàn quyền mất kiểm soát ai đang giữ.

- **`BR-24.2` (Thao tác mới mặc định Không có):** Khi một phân hệ bổ sung loại dữ liệu hoặc thao tác mới, ô mới ở mọi vai trò tự tạo mặc định là Không có; vai trò dựng sẵn nhận giá trị theo `BR-29.1`.

  **Lý do nghiệp vụ:** Nguyên tắc 7; một bản phát hành không được tự mở quyền trong vai trò doanh nghiệp đã tự thiết kế.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-24.1.1` | Màn hình sửa vai trò | Tìm Quyền Ủy thác | Không có trong danh sách quyền quản trị |
| `AC-24.2.1` | Bản phát hành bổ sung thao tác "Chuyển giai đoạn" cho Cơ hội | Mở một vai trò tự tạo | Ô (Cơ hội, Chuyển giai đoạn) = Không có |
| `AC-24.3.1` | Người có quyền Quản lý vai trò lần đầu mở khu quản trị phân quyền | Mở | Thấy Thành viên, Đơn vị, Nhóm, Vai trò (chế độ Cơ bản); các tính năng nâng cao nằm trong mục "Nâng cao" và mở được ngay |
| `AC-24.1.2` | Gói không có tính năng Chiến dịch | Mở Danh mục quyền | Dòng Chiến dịch hiển thị "Không khả dụng theo gói" |

---

### FEAT-25 — Tạo & sao chép vai trò

**Mô tả nghiệp vụ:** Tạo vai trò tự tạo: tên, mô tả, mức ở từng ô của ma trận, các quyền quản trị. Hoặc sao chép một vai trò có sẵn (kể cả vai trò dựng sẵn) thành bản tuỳ biến được.

**Vai trò sử dụng chính:** Người có quyền Quản lý vai trò.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Chọn tạo mới hoặc sao chép.
2. Chọn chế độ hiển thị: **Cơ bản** (mỗi loại dữ liệu chọn một mức đặt sẵn — `BR-25.4`) hoặc **Chi tiết** (từng ô).
3. Đặt mức, chọn quyền quản trị; màn hình đánh dấu ngay ô vi phạm `BR-25.1`, ô vượt trần năng lực và ô không khả dụng theo gói.
4. Lưu.

**Luồng ngoại lệ:**

- Một ô hoặc quyền quản trị vượt năng lực của người tạo → từ chối, nêu ô. Áp cho cả tạo mới **và sao chép**.
- Vi phạm `BR-25.1` hoặc `BR-25.3` → từ chối, nêu ô.
- Tên trùng một vai trò đã có → từ chối.

**Quy tắc nghiệp vụ:**

- **`BR-25.1` (Không sửa được thứ mình không thấy):** Trong cùng một loại dữ liệu, mức của mọi thao tác khác (Sửa, Xoá, Xuất, Gán, thao tác đặc thù) không được rộng hơn mức Xem. Thao tác Tạo chỉ có Có / Không có, vì bản ghi mới mặc định do người tạo phụ trách (trừ bản ghi vào hàng đợi, `BR-35.11`).

  **Lý do nghiệp vụ:** mức Sửa rộng hơn Xem hoặc không có tác dụng, hoặc cho sửa mù những bản ghi người đó không được thấy — cả hai đều là cấu hình sai.

- **`BR-25.2` (Gán nhanh):** Ở chế độ Chi tiết, đặt được một mức cho cả dòng hoặc cả cột rồi chỉnh riêng từng ô. Ô bị trần quyền chặn hiển thị rõ là không khả dụng, không bị ẩn.

  **Lý do nghiệp vụ:** ma trận có hàng trăm ô; đặt từng ô là không khả thi với người quản trị không chuyên.

- **`BR-25.3` (Sàn bắt buộc):** Ô, quyền quản trị hay nhóm trường mang Sàn bắt buộc (khai báo tại SRS phân hệ, ví dụ [`contacts-srs.md`](./contacts-srs.md) `BR-01.5b` về nhóm Định danh KYC và `NFR-14` về quyền đọc nhật ký kiểm toán) không đặt vượt sàn được, dù trên vai trò tự tạo hay qua điều chỉnh vai trò dựng sẵn (`BR-29.4`). Sàn áp như nhau cho mọi vai trò, không gắn với tên vai trò; sàn nào áp cả lên Người có toàn quyền thì SRS phân hệ nêu rõ.

  **Lý do nghiệp vụ:** sàn là cam kết với khách hàng cuối của doanh nghiệp hoặc chính sách doanh nghiệp đã tự đặt; nó không được phụ thuộc vào cách đặt tên hay phối vai trò.

- **`BR-25.4` (Chế độ Cơ bản):** Chế độ Cơ bản cho mỗi loại dữ liệu chọn một trong các mức đặt sẵn, mỗi mức đặt sẵn là một tổ hợp ô xác định:

  | Mức đặt sẵn | Xem | Tạo | Sửa, Gán | Xoá, Xuất, Nhập |
  | --- | --- | --- | --- | --- |
  | Không truy cập | Không có | Không có | Không có | Không có |
  | Chỉ xem của mình | Chỉ của mình | Không có | Không có | Không có |
  | Chỉ xem đơn vị | Đơn vị của mình | Không có | Không có | Không có |
  | Chỉ xem toàn workspace | Toàn workspace | Không có | Không có | Không có |
  | Chỉ dữ liệu của mình | Chỉ của mình | Có | Chỉ của mình | Không có |
  | Xem đơn vị, sửa của mình | Đơn vị của mình | Có | Chỉ của mình | Không có |
  | Quản lý dữ liệu đơn vị | Đơn vị và các đơn vị con | Có | Đơn vị và các đơn vị con | Xoá = Đơn vị của mình; Xuất, Nhập = Không có |
  | Toàn bộ dữ liệu | Toàn workspace | Có | Toàn workspace | Toàn workspace |

  Chuyển sang Chi tiết giữ nguyên các ô; vai trò có ô không khớp mức đặt sẵn nào hiển thị "Tuỳ chỉnh" ở chế độ Cơ bản. Thao tác đặc thù của từng loại (ví dụ Phát sóng chiến dịch) không thuộc mức đặt sẵn nào, kể cả "Toàn bộ dữ liệu": ở chế độ Cơ bản mỗi thao tác đặc thù hiển thị thành **một dòng riêng** ngay dưới loại dữ liệu, với mức hiện tại, đặt được trực tiếp; chọn mức đặt sẵn không nới các dòng này; nếu mức đặt sẵn mới làm Xem hẹp hơn mức của một dòng thao tác đặc thù, dòng đó được thu hẹp theo bằng mức Xem mới (`BR-25.1`) và được đánh dấu nổi trước khi lưu.

  **Lý do nghiệp vụ:** đa số doanh nghiệp nhỏ chỉ cần vài kiểu quyền quen thuộc; buộc họ đọc ma trận làm chậm thiết lập và dễ cấu hình sai. Chế độ Cơ bản dùng đúng mô hình ô, nên không tạo ra cơ chế thứ hai.

- **`BR-25.5` (Sao chép cũng chịu trần):** Bản sao là vai trò mới, chịu trần năng lực của người sao chép như tạo mới.

  **Lý do nghiệp vụ:** nếu sao chép được miễn trần, người giữ quyền Quản lý vai trò sao chép một vai trò mạnh rồi gán qua Quyền Ủy thác, vượt trần mà không ai duyệt.

- **`BR-25.6` (Ý nghĩa ô Gán):** Ô Gán ở mức M cho phép đổi Người phụ trách của bản ghi nằm trong mức M, và chỉ sang người mà sau khi đổi, bản ghi vẫn nằm trong mức M của chính người gán. Hệ quả: mức Chỉ của mình chỉ cho **nhận việc về mình** (từ hàng đợi theo `BR-35.11` hay khi được mời nhận), không cho giao bản ghi của mình sang người khác; mức Đơn vị của mình cho giao trong đơn vị; giao ra ngoài đơn vị cần mức rộng hơn bao phủ đơn vị đích. Người nhận phải ở trạng thái Đang hoạt động (hoặc Đang chờ chấp nhận khi giữ chỗ theo `BR-35.10`) và có ô Xem của loại đó khác Không có; phân hệ có thể đòi thêm điều kiện (ví dụ người nhận là thành viên đơn vị tiếp nhận). Bàn giao khi rời đi hay tạm ngưng theo `FEAT-42`, `FEAT-43`. Hai đường luôn có cho **Người phụ trách hiện tại**, kể cả khi ô Gán của họ chỉ là Chỉ của mình: (a) **trả về hàng đợi** mà bản ghi đang thuộc (`BR-35.13`, chỉ khi xác định được hàng đợi); (b) **đề nghị chuyển** cho một người cụ thể — đề nghị chỉ có hiệu lực khi người nhận chấp nhận, và lúc chấp nhận người nhận phải tự đạt điều kiện nhận việc: Đang hoạt động, ô Gán của loại đó khác Không có, đã Xem được bản ghi theo `BR-39.6` (gồm nguồn nới của hàng đợi) và không bị nguồn chặn nào; đề nghị hết hạn theo `CFG-25-01` hoặc khi Người phụ trách đổi. Chuyển sang hàng đợi đơn vị khác theo `BR-35.14` là ngoại lệ tường minh của quy tắc đích ở trên, vì danh sách đích đã do Người có toàn quyền khai báo.

  **Lý do nghiệp vụ:** nếu giao được bản ghi của mình cho bất kỳ ai, một nhân viên có thể đẩy khách hàng sang đơn vị khác hay sang người ngoài phạm vi quản lý mà không ai có thẩm quyền duyệt; gắn đích với chính mức của người gán giữ cho Gán là một ô duy nhất, dễ hiểu, mà không phải tách thành hai ô "nhận" và "giao". Đề nghị chuyển giữ được thói quen bàn giao hằng ngày giữa đồng nghiệp mà không mở thêm dữ liệu: người nhận chỉ nhận được bản ghi mà chính họ đã thấy và được phép nhận.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-25.1.1` | Đặt (Khách hàng, Xem) = Chỉ của mình | Đặt (Khách hàng, Sửa) = Đơn vị của mình | Ô Sửa được đánh dấu lỗi ngay; nút lưu bị vô hiệu kèm "Sửa không được rộng hơn Xem" |
| `AC-25.2.1` | Chế độ Chi tiết | Đặt cả dòng Cơ hội = Đơn vị của mình, rồi đổi ô Xoá = Không có | Mọi ô Cơ hội là Đơn vị của mình trừ Xoá; Tạo = Có |
| `AC-25.3.1` | Phân hệ Khách hàng khai báo sàn cho nhóm Định danh KYC | Đặt quyền đọc KYC cho vai trò tự tạo vượt sàn | Lựa chọn bị vô hiệu kèm giải thích sàn và nguồn quy định |
| `AC-25.4.1` | Chế độ Cơ bản | Chọn "Xem đơn vị, sửa của mình" cho Khách hàng, chuyển sang Chi tiết | Ô Khách hàng hiển thị đúng tổ hợp ở bảng `BR-25.4` |
| `AC-25.4.2` | Vai trò có ô không khớp mức đặt sẵn nào | Mở ở chế độ Cơ bản | Loại dữ liệu đó hiển thị "Tuỳ chỉnh" |
| `AC-25.4.3` | Vai trò có (Chiến dịch, Phát sóng) = Đơn vị của mình | Mở ở chế độ Cơ bản | Dòng "Phát sóng" hiển thị riêng với mức Đơn vị của mình |
| `AC-25.4.4` | (Chiến dịch, Phát sóng) = Không có | Chọn mức đặt sẵn "Toàn bộ dữ liệu" cho Chiến dịch | Phát sóng vẫn là Không có |
| `AC-25.4.5` | (Chiến dịch, Phát sóng) = Đơn vị của mình | Chọn mức đặt sẵn "Chỉ xem của mình" cho Chiến dịch | Dòng Phát sóng tự thu về Chỉ của mình, được đánh dấu nổi trước khi lưu |
| `AC-25.5.1` | Người có (Cơ hội, Xem) = Đơn vị của mình | Mở danh sách sao chép, chọn vai trò dựng sẵn Quản lý (Cơ hội, Xem = Đơn vị và các đơn vị con) | Lựa chọn bị đánh dấu không sao chép được, nêu ô vượt trần |
| `AC-25.6.1` | Nhân viên Kinh doanh S có (Cơ hội, Gán) = Chỉ của mình, đang phụ trách cơ hội O | Giao O cho đồng nghiệp K cùng đơn vị | Không có K trong danh sách người nhận; giải thích cần mức Gán Đơn vị của mình |
| `AC-25.6.2` | Như trên | Nhận một khách hàng tiềm năng chưa gán từ hàng đợi của đơn vị mình | Thành công; S là Người phụ trách |
| `AC-25.6.3` | Quản lý Q có (Cơ hội, Gán) = Đơn vị của mình ở "Kinh doanh – Hà Nội" | Giao cơ hội của đơn vị cho người thuộc "Kinh doanh – Đà Nẵng" | Người thuộc Đà Nẵng không có trong danh sách người nhận |
| `AC-25.6.4` | Q giao cơ hội cho K đang Tạm ngưng | Mở danh sách người nhận | K không có trong danh sách |
| `AC-25.6.5` | Nhân viên Hỗ trợ H (Gán = Chỉ của mình) phụ trách vé V của hàng đợi "Hỗ trợ"; K cùng đơn vị, Gán = Chỉ của mình | H đề nghị chuyển V cho K; K chấp nhận | V thuộc K; nhật ký ghi người đề nghị và người chấp nhận |
| `AC-25.6.6` | S (Gán = Chỉ của mình) phụ trách khách hàng C; M ở đơn vị khác không Xem được C | S đề nghị chuyển C cho M | M không có trong danh sách người nhận được đề nghị |
| `AC-25.6.7` | Đề nghị chuyển từ H sang K chưa được chấp nhận; quá `CFG-25-01` | Hết hạn | Đề nghị huỷ, V vẫn thuộc H, H được báo |

---

### FEAT-26 — Cập nhật vai trò

**Mô tả nghiệp vụ:** Sửa tên, mô tả, mức ở từng ô và quyền quản trị của một vai trò tự tạo. `BR-25.1` → `BR-25.4` áp nguyên văn.

**Vai trò sử dụng chính:** Người có quyền Quản lý vai trò.

**Điều kiện tiên quyết:** Vai trò là vai trò tự tạo.

**Luồng chính:**

1. Mở vai trò, sửa.
2. Màn hình hiển thị trước số thành viên và nhóm bị ảnh hưởng, các ô bị nới rộng và bị thu hẹp.
3. Lưu → mọi thành viên và nhóm đang giữ vai trò được tính lại quyền theo `NFR-04`; một phiên bản lịch sử mới được tạo (`FEAT-27`).

**Luồng ngoại lệ:**

- Vai trò dựng sẵn → không sửa trực tiếp; điều chỉnh ô theo `BR-29.4` hoặc sao chép (`FEAT-25`).
- Ô bị nới rộng vượt năng lực người sửa → từ chối, nêu ô.
- Người sửa đang giữ chính vai trò này và thay đổi nới rộng nó → từ chối (`BR-11.2`).
- Thay đổi tạo vi phạm tách biệt nhiệm vụ cho người đang giữ vai trò → theo `CFG-46-01`.

**Quy tắc nghiệp vụ:**

- **`BR-26.1` (Chỉ ô nới rộng mới bị kiểm tra trần):** Thu hẹp một ô luôn được phép, trừ khi thu hẹp mức Xem làm nó hẹp hơn thao tác khác cùng dòng (`BR-25.1`).

  **Lý do nghiệp vụ:** thu hẹp không phải leo thang quyền; chặn nó chỉ làm người quản trị không dọn được quyền thừa.

- **`BR-26.2` (Sửa vai trò mình đang giữ):** Người sửa không nới rộng được vai trò mà chính họ đang giữ (trực tiếp hoặc qua nhóm).

  **Lý do nghiệp vụ:** Nguyên tắc 2; sửa vai trò của chính mình là tự cấp quyền gián tiếp.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-26.1.1` | Vai trò đang gán cho 40 người | Thu hẹp (Cơ hội, Xoá) về Không có | Màn hình xác nhận ghi 40 người bị ảnh hưởng; lưu thành công; lượt xoá kế tiếp của họ bị từ chối |
| `AC-26.2.1` | A giữ vai trò X qua nhóm | A nới rộng một ô của X | Từ chối, nêu lý do không tự nới rộng |
| `AC-26.1.2` | Mở vai trò dựng sẵn | Tìm nút Sửa | Không có; có lối tắt "Điều chỉnh ô" và "Sao chép" |

---

### FEAT-27 — Lịch sử phiên bản & khôi phục vai trò

**Mô tả nghiệp vụ:** Mỗi lần lưu vai trò tạo một phiên bản lịch sử không sửa, không xoá được. Xem lại và khôi phục về phiên bản cũ.

**Vai trò sử dụng chính:** Người có quyền Quản lý vai trò.

**Điều kiện tiên quyết:** Vai trò còn tồn tại; lịch sử của vai trò đã xoá vẫn xem được qua nhật ký (`FEAT-41`).

**Luồng chính:**

1. Mở lịch sử, chọn hai phiên bản để so sánh khác biệt theo từng ô.
2. Chọn khôi phục một phiên bản → hệ thống kiểm tra như một lần cập nhật → lưu thành phiên bản mới ghi rõ "khôi phục từ phiên bản N".

**Quy tắc nghiệp vụ:**

- **`BR-27.1` (Khôi phục là một lần sửa mới):** Khôi phục không xoá lịch sử; tạo phiên bản mới ghi rõ nguồn.

  **Lý do nghiệp vụ:** dòng thời gian thay đổi phải nguyên vẹn để tra soát.

- **`BR-27.2` (Khôi phục chịu đủ kiểm tra):** Khôi phục chịu trần năng lực của người khôi phục, `BR-25.1`, `BR-25.3`, `BR-26.2` và trần quyền hiện tại của workspace.

  **Lý do nghiệp vụ:** phiên bản cũ có thể được tạo bởi người quyền cao hơn hoặc dưới gói cao hơn; khôi phục không kiểm tra là đường vòng để lấy lại quyền mà người khôi phục không cấp được.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-27.1.1` | Vai trò có 5 phiên bản | Khôi phục phiên bản 3 | Có phiên bản 6 ghi "khôi phục từ phiên bản 3"; phiên bản 1 – 5 vẫn còn |
| `AC-27.2.1` | Phiên bản 2 có ô vượt năng lực người khôi phục | Mở phiên bản 2 | Nút khôi phục bị vô hiệu, nêu ô vượt trần |
| `AC-27.1.2` | Chọn phiên bản 2 và 5 | So sánh | Hiển thị đúng các ô khác nhau, giá trị cũ và mới |

---

### FEAT-28 — Xoá vai trò

**Mô tả nghiệp vụ:** Xoá một vai trò tự tạo, có cảnh báo tác động và dọn sạch quyền mọc ra từ nó.

**Vai trò sử dụng chính:** Người có quyền Quản lý vai trò.

**Điều kiện tiên quyết:** Vai trò là vai trò tự tạo.

**Luồng chính:**

1. Bấm xoá → hệ thống hiển thị số thành viên và nhóm đang giữ, số quyền tạm thời tham chiếu, số thành viên sẽ không còn vai trò nào sau khi xoá.
2. Xác nhận: đánh dấu xác nhận; nếu số thành viên bị ảnh hưởng đạt `CFG-28-01` thì phải gõ lại tên vai trò.
3. Vai trò bị xoá; quyền tạm thời tham chiếu bị thu hồi; phiên của người mất quyền tạm thời đó chấm dứt (`BR-11.3`).

**Luồng ngoại lệ:**

- Vai trò dựng sẵn → không xoá được.
- Vai trò đang nằm trong danh sách được giao của một Quyền Ủy thác → được xoá; vai trò bị gỡ khỏi danh sách đó.

**Quy tắc nghiệp vụ:**

- **`BR-28.1` (Xác nhận theo mức tác động):** Dưới ngưỡng `CFG-28-01`: chỉ cần đánh dấu xác nhận. Từ ngưỡng trở lên: phải gõ lại tên vai trò.

  **Lý do nghiệp vụ:** xoá vai trò của 3 người không cần nghi thức nặng; xoá vai trò của 80 người bằng một cú bấm là sự cố vận hành.

- **`BR-28.2` (Không để quyền tạm thời mồ côi):** Xoá vai trò thu hồi ngay mọi quyền tạm thời tham chiếu nó, kể cả yêu cầu đang chờ phê duyệt.

  **Lý do nghiệp vụ:** Nguyên tắc 6.

- **`BR-28.3` (Không tự gán vai trò thay thế):** Thành viên không còn vai trò nào sau khi xoá thì không được tự gán vai trò khác; họ chỉ còn quyền từ mức nền (nếu có) và xuất hiện trong cảnh báo Nghiêm trọng của `FEAT-03`.

  **Lý do nghiệp vụ:** tự gán một vai trò mặc định là cấp quyền không ai quyết định.

- **`BR-28.4` (Dọn mọi nơi trỏ tới vai trò):** Xoá vai trò gỡ nó khỏi vai trò gợi ý của mọi đơn vị, khỏi danh sách được giao của Quyền Ủy thác, khỏi liên kết mời (liên kết không còn vai trò nào thì mất hiệu lực), khỏi quy tắc tách biệt nhiệm vụ (quy tắc còn một vế thì tắt và báo người tạo); `CFG-09-02` đang trỏ tới nó trở về "Không chọn sẵn". Màn hình xác nhận liệt kê các nơi này.

  **Lý do nghiệp vụ:** Nguyên tắc 6; một liên kết mời trỏ tới vai trò không còn tồn tại sẽ đưa người mới vào mà không có quyền nào, hoặc tệ hơn, với một mặc định không ai chọn.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-28.1.1` | Vai trò gán cho 3 người, `CFG-28-01` = 10 | Xoá | Màn hình ghi "3 người, 0 nhóm"; đánh dấu xác nhận là xoá được |
| `AC-28.1.2` | Vai trò gán cho 80 người | Xoá | Phải gõ đúng tên vai trò |
| `AC-28.2.1` | Vai trò đang được 2 quyền tạm thời tham chiếu, người nhận đang đăng nhập | Xoá | 2 quyền tạm thời hiện "Đã thu hồi — vai trò bị xoá"; phiên của 2 người nhận chấm dứt |
| `AC-28.3.1` | 5 người chỉ có đúng vai trò này | Xoá | 5 người không có vai trò nào; `FEAT-03` cảnh báo Nghiêm trọng liệt kê 5 người |
| `AC-28.4.1` | Vai trò là vai trò gợi ý của 2 đơn vị và gắn với 1 liên kết mời | Xoá vai trò | Màn hình xác nhận liệt kê 2 đơn vị và 1 liên kết; sau khi xoá, 2 đơn vị không còn vai trò gợi ý đó, liên kết mất hiệu lực |

---

### FEAT-29 — Vai trò dựng sẵn & điều chỉnh ô của vai trò dựng sẵn

**Mô tả nghiệp vụ:** Mỗi workspace có sẵn một bộ vai trò mẫu. Đây là **nguồn duy nhất** về danh sách vai trò dựng sẵn; [`onboarding-srs.md`](./onboarding-srs.md) và các SRS phân hệ dẫn chiếu tới đây. Mỗi vai trò mẫu là một ma trận mặc định; ma trận chi tiết theo từng loại dữ liệu do SRS phân hệ khai báo (ví dụ [`contacts-srs.md`](./contacts-srs.md) Mục 5, [`campaigns-srs.md`](./campaigns-srs.md) Mục 5), bảng dưới đây nêu mức mặc định chung theo mức đặt sẵn của `BR-25.4`.

| Vai trò dựng sẵn | Mức mặc định chung | Ghi chú |
| --- | --- | --- |
| Quản lý | Quản lý dữ liệu đơn vị (Xoá = Đơn vị của mình; Xuất, Nhập = Không có) | Gồm quyền quản trị Phê duyệt quyền tạm thời |
| Nhân viên Kinh doanh | Xem đơn vị, sửa của mình trên Khách hàng, Công ty, Cơ hội, Công việc; Chỉ xem đơn vị trên Vé hỗ trợ | Không có Xoá; Gán = Chỉ của mình, đủ để nhận khách hàng tiềm năng từ hàng đợi (`BR-35.11`) |
| Nhân viên Hỗ trợ | Xem đơn vị, sửa của mình trên Vé hỗ trợ, Hội thoại, Công việc; Chỉ xem đơn vị trên Khách hàng, Công ty | Xem khách hàng của đơn vị khác qua quyền đọc tự động khi có vé hoặc hội thoại đang mở (`BR-39.5`) |
| Quản lý Marketing | Như Marketing; trên Chiến dịch: Xem, Sửa, Xoá = Đơn vị của mình; (Chiến dịch, Phát sóng) = Đơn vị của mình; trên Khách hàng: Xem = Toàn workspace, Tạo = Có, Nhập, Sửa, Gán = Đơn vị của mình | Vai trò trưởng nhóm của đội Marketing; khớp vai trò Quản lý Marketing của [`campaigns-srs.md`](./campaigns-srs.md) (Phát sóng gồm phê duyệt kép theo tài liệu đó); cần tính năng mở rộng tương ứng |
| Marketing | Xem đơn vị, sửa của mình trên Chiến dịch; ô (Khách hàng, Xem) = Toàn workspace, các thao tác khác trên Khách hàng = Không có | Ô (Chiến dịch, Phát sóng) = Không có theo [`campaigns-srs.md`](./campaigns-srs.md) `BR-17.1`; cần tính năng mở rộng tương ứng |
| Chỉ xem bản ghi được giao | Xem = Chỉ của mình trên mọi loại dữ liệu nghiệp vụ; không có thao tác ghi nào, trừ ô phân hệ khai báo lệch có lý do theo `BR-29.6` (ví dụ Sửa = Chỉ của mình trên Công việc để hoàn thành việc được giao, [`tasks-srs.md`](./tasks-srs.md) Mục 5) | Giá trị mặc định của `CFG-09-02`; người mới chỉ thấy bản ghi được giao cho mình |
| Kiểm toán | Xem = Toàn workspace trên mọi loại dữ liệu; không có ô ghi nào; quyền quản trị Xem nhật ký quyền và Xem báo cáo quyền | Cần tính năng mở rộng tương ứng |
| Kiểm toán quyền | Không truy cập mọi loại dữ liệu nghiệp vụ; quyền quản trị Xem nhật ký quyền, Xem báo cáo quyền | Cho kiểm toán viên an toàn thông tin cần bằng chứng về quyền mà không cần xem dữ liệu khách hàng |

**Vai trò sử dụng chính:** Hệ thống (tạo và đồng bộ); người có quyền Quản lý vai trò (điều chỉnh ô, duyệt thay đổi).

**Điều kiện tiên quyết:** Không có.

**Luồng chính (điều chỉnh ô):**

1. Mở vai trò dựng sẵn → "Điều chỉnh ô".
2. Đổi mức của ô theo đúng mô hình `FEAT-25`.
3. Lưu → ô được đánh dấu "đã điều chỉnh"; có thể "Đặt lại về mặc định" từng ô hoặc cả vai trò.

**Luồng chính (đồng bộ theo bản phát hành):**

1. Bản phát hành đổi ma trận mặc định của một vai trò dựng sẵn.
2. Ô doanh nghiệp đã điều chỉnh: giữ nguyên.
3. Ô chưa điều chỉnh bị **thu hẹp**: áp ngay.
4. Ô chưa điều chỉnh bị **nới rộng** hoặc ô mới: áp theo `CFG-29-01` — chờ Người có toàn quyền duyệt (mặc định), hoặc áp ngay.
5. Mọi thay đổi ghi vào nhật ký thay đổi cấu hình quyền, người thực hiện "Hệ thống — bản phát hành", và thông báo cho Người có toàn quyền kèm tóm tắt.

**Quy tắc nghiệp vụ:**

- **`BR-29.1` (Đồng bộ có kiểm soát):** Bản phát hành chỉ tự áp ngay các thay đổi thu hẹp; thay đổi nới rộng theo `CFG-29-01`.

  **Lý do nghiệp vụ:** bản phát hành tự mở rộng quyền của mọi người dùng là thay đổi quyền ngoài quy trình quản lý thay đổi của doanh nghiệp.

- **`BR-29.2` (Không đổi tên hiển thị):** Đồng bộ không đổi tên hiển thị của vai trò dựng sẵn.

  **Lý do nghiệp vụ:** người quản trị đã quen gọi theo tên đó trong nội bộ.

- **`BR-29.3` (Cấp bậc không phải vai trò):** Chủ sở hữu và Quản trị viên là cấp bậc, không phải vai trò trong danh sách này, và không sửa hay xoá được như một vai trò.

  **Lý do nghiệp vụ:** toàn quyền là trục riêng (`BR-12.1`); để nó thành một vai trò sửa được sẽ phá trần năng lực.

- **`BR-29.4` (Điều chỉnh ô của vai trò dựng sẵn):** Doanh nghiệp điều chỉnh từng ô của vai trò dựng sẵn qua `CFG-29-02`, theo đúng mô hình ô của `FEAT-25`, chịu `BR-25.1`, `BR-25.3`, `BR-26.2` và trần năng lực của người điều chỉnh; "Đặt lại về mặc định" là một lần điều chỉnh và chịu cùng kiểm tra. Ô đã điều chỉnh được giữ nguyên khi đồng bộ. Vai trò dựng sẵn vẫn không sửa trực tiếp được tên, mô tả hay bị xoá.

  **Lý do nghiệp vụ:** doanh nghiệp cần chỉnh nhỏ (ví dụ thu hẹp ô Khách hàng × Xem của Marketing về Đơn vị của mình) mà không phải sao chép vai trò rồi gán lại cho mọi người; một bản phát hành không được lặng lẽ xoá quyết định doanh nghiệp đã đưa ra.

- **`BR-29.5` (Một danh sách vai trò dựng sẵn):** Bảng vai trò dựng sẵn ở trên là danh sách duy nhất; mọi workspace mới có đủ các vai trò này, và SRS phân hệ dẫn chiếu vai trò dựng sẵn bằng đúng tên trong bảng.

  **Lý do nghiệp vụ:** khi mỗi tài liệu tự liệt kê vai trò mẫu riêng, ma trận mặc định không biết thuộc vai trò nào và điều chỉnh ô (`BR-29.4`) không áp được nhất quán.

- **`BR-29.6` (Ma trận chi tiết do phân hệ khai báo):** Bảng trên nêu mức mặc định chung. Với loại dữ liệu của mình, SRS phân hệ khai báo ma trận mặc định chi tiết của từng vai trò dựng sẵn — gồm thao tác đặc thù, quyền quản trị của phân hệ và các dòng loại dữ liệu mà bảng trên không nêu (ví dụ Công việc của Marketing) — và ma trận đó là giá trị mặc định có hiệu lực. Phân hệ được lệch khỏi mức mặc định chung khi nêu lệch đó cùng lý do nghiệp vụ trong SRS của mình (ví dụ thao tác đặc thù Gắn thẻ phân loại của Marketing, quyền Xuất khách hàng của mình cho Nhân viên Kinh doanh); "các thao tác khác = Không có" ở bảng trên chỉ áp cho thao tác chuẩn chưa được phân hệ khai báo. Ma trận chi tiết vẫn chịu Sàn bắt buộc (`BR-25.3`), trần của workspace và `BR-25.1`. `BR-29.4` (`CFG-29-02`) điều chỉnh được mọi ô và mọi quyền quản trị của phân hệ trên vai trò dựng sẵn, không chỉ ô chuẩn.

  **Lý do nghiệp vụ:** chỉ phân hệ hiểu thao tác đặc thù của mình đủ để đặt mặc định đúng; giữ một bảng chung ngắn ở đây và ma trận chi tiết ở phân hệ tránh hai bảng nói khác nhau về cùng một ô, còn yêu cầu nêu lý do khi lệch giữ cho mức mặc định chung vẫn là chuẩn.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-29.4.1` | Vai trò Marketing mặc định (Khách hàng, Xem) = Toàn workspace | Điều chỉnh về Đơn vị của mình | Lưu thành công; ô có nhãn "đã điều chỉnh"; người giữ Marketing chỉ còn thấy khách hàng đơn vị mình |
| `AC-29.4.2` | Tình huống AC-29.4.1, bản phát hành đổi mặc định của ô đó | Đồng bộ | Ô vẫn là Đơn vị của mình |
| `AC-29.4.3` | A giữ vai trò Nhân viên Kinh doanh | A điều chỉnh rộng một ô của Nhân viên Kinh doanh | Từ chối, nêu lý do không tự nới rộng |
| `AC-29.4.4` | Doanh nghiệp đã thu hẹp một ô; người điều chỉnh có năng lực hẹp hơn mặc định của ô đó | Bấm "Đặt lại về mặc định" | Thao tác bị vô hiệu cho ô đó, nêu ô vượt trần |
| `AC-29.1.1` | `CFG-29-01` = Chờ duyệt; bản phát hành nới rộng một ô chưa điều chỉnh | Đồng bộ | Ô chưa đổi; Người có toàn quyền nhận thông báo; `FEAT-03` hiện "1 thay đổi vai trò dựng sẵn đang chờ duyệt" |
| `AC-29.1.2` | Bản phát hành thu hẹp một ô chưa điều chỉnh | Đồng bộ | Ô thu hẹp ngay; nhật ký ghi người thực hiện "Hệ thống — bản phát hành" |
| `AC-29.3.1` | Danh sách vai trò | Tìm "Quản trị viên" | Không có trong danh sách vai trò; có ghi chú cấp bậc Quản trị viên được quản lý ở hồ sơ thành viên |
| `AC-29.5.1` | Workspace mới Sẵn sàng, gói có tính năng chiến dịch | Mở vai trò Quản lý Marketing | Có (Chiến dịch, Phát sóng) = Đơn vị của mình; vai trò Quản lý của các đội khác không có Phát sóng |
| `AC-29.2.1` | Doanh nghiệp quen gọi vai trò dựng sẵn "Nhân viên Hỗ trợ" | Bản phát hành đồng bộ vai trò | Tên hiển thị không đổi |
| `AC-29.6.1` | SRS phân hệ Khách hàng khai báo Marketing có thao tác đặc thù Gắn thẻ phân loại = Toàn workspace | Workspace mới được tạo | Vai trò Marketing có ô đó ở Toàn workspace; các thao tác chuẩn khác trên Khách hàng = Không có |
| `AC-29.6.2` | Vai trò Quản lý dựng sẵn có quyền quản trị phân hệ "Gộp vé" theo SRS phân hệ Vé hỗ trợ | Quản trị viên điều chỉnh qua `CFG-29-02` thành Không có | Lưu thành công, ô đánh dấu "đã điều chỉnh", giữ nguyên khi đồng bộ bản phát hành |

---

### FEAT-45 — Cấp & thu hồi Quyền Ủy thác

**Mô tả nghiệp vụ:** Cho phép bộ phận được giao (thường là Nhân sự hoặc Công nghệ thông tin) gán đúng những vai trò có sẵn được chỉ định cho nhân viên, dù bản thân họ không có quyền nghiệp vụ tương ứng. Xem phân tích đánh đổi tại ADR-0002.

**Vai trò sử dụng chính:** Chủ sở hữu, Quản trị viên (cấp và thu hồi); người giữ Quyền Ủy thác (sử dụng).

**Điều kiện tiên quyết:** Người nhận là thành viên Đang hoạt động.

**Luồng chính:**

1. Người có toàn quyền mở hồ sơ thành viên → Cấp Quyền Ủy thác.
2. Chọn **danh sách vai trò được giao** (một hoặc nhiều vai trò có sẵn).
3. Lưu → mọi Người có toàn quyền nhận thông báo.
4. Người giữ quyền gán các vai trò trong danh sách (và thêm vào nhóm chỉ mang vai trò trong danh sách) cho người khác.
5. Thu hồi bất kỳ lúc nào.

**Luồng ngoại lệ:**

- Người không có toàn quyền thử cấp → không có thao tác.
- Người giữ Quyền Ủy thác thử cấp tiếp Quyền Ủy thác cho người khác → không có thao tác.
- Vai trò trong danh sách bị xoá → tự gỡ khỏi danh sách.

**Quy tắc nghiệp vụ:**

- **`BR-45.1` (Chỉ Người có toàn quyền cấp, không truyền tiếp):** Chỉ Chủ sở hữu hoặc Quản trị viên cấp và thu hồi Quyền Ủy thác. Người giữ không cấp tiếp được.

  **Lý do nghiệp vụ:** quyền miễn trần chỉ được cấp bởi người vốn không có trần; truyền tiếp được thì không ai biết ai đang giữ.

- **`BR-45.2` (Phạm vi miễn trừ):** Người giữ được gán cho người khác bất kỳ vai trò nào trong danh sách được giao, và thêm người khác vào nhóm mà mọi vai trò của nhóm (gồm chuỗi nhóm cha) đều trong danh sách, kể cả vượt năng lực của chính mình.

  **Lý do nghiệp vụ:** giải quyết nghịch lý nhân sự được giao tạo tài khoản cho nhân viên kinh doanh mà bản thân không có quyền kinh doanh; danh sách được giao giữ cho Người có toàn quyền biết chính xác giới hạn của miễn trừ.

- **`BR-45.3` (Những gì không được miễn):** Miễn trừ không áp cho: cấp bậc Quản trị viên (`BR-12.1`); gán cho chính mình (`BR-11.2`); tạo, sửa, sao chép, khôi phục vai trò; tạo hoặc sửa cấu hình quyền của nhóm (`BR-17.2`); yêu cầu quyền tạm thời (`BR-30.3`); vai trò ngoài danh sách được giao.

  **Lý do nghiệp vụ:** miễn trừ chỉ dành cho việc phân phối vai trò đã được người khác thiết kế và chấp thuận; mọi đường tạo ra năng lực mới vẫn phải qua trần năng lực.

- **`BR-45.4` (Thu hồi Quyền Ủy thác kiểm tra lại những gì đang treo):** Khi Quyền Ủy thác bị thu hồi hoặc danh sách được giao bị thu hẹp, lời mời đang chờ và liên kết mời do người đó tạo được kiểm tra lại theo năng lực hiện tại của họ; phần vượt bị thu hồi, người đó và Người có toàn quyền được thông báo. Vai trò đã gán xong trước đó giữ nguyên.

  **Lý do nghiệp vụ:** một lời mời đang chờ hay một liên kết còn hiệu lực là quyền chưa trao xong; nó không được tiếp tục mang miễn trừ mà người tạo không còn giữ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-45.2.1` | A (phòng Nhân sự) giữ Quyền Ủy thác, danh sách {Nhân viên Kinh doanh, Nhân viên Hỗ trợ}; A không có quyền trên Cơ hội | A mời người mới với vai trò Nhân viên Kinh doanh | Thành công |
| `AC-45.3.1` | Cùng A | Gán vai trò Quản lý (ngoài danh sách) | Từ chối, nêu vai trò không trong danh sách được giao |
| `AC-45.3.2` | Cùng A | Nâng người mới lên Quản trị viên | Không có thao tác |
| `AC-45.3.3` | Cùng A | Tự gán Nhân viên Kinh doanh cho mình | Từ chối |
| `AC-45.1.1` | Cùng A | Mở hồ sơ người khác tìm "Cấp Quyền Ủy thác" | Không có thao tác |
| `AC-45.1.2` | Quản trị viên cấp Quyền Ủy thác cho A | Lưu | Chủ sở hữu và mọi Quản trị viên khác nhận thông báo |
| `AC-45.4.1` | A giữ Quyền Ủy thác, đã gửi 3 lời mời vai trò Nhân viên Kinh doanh còn đang chờ | Quản trị viên thu hồi Quyền Ủy thác của A | 3 lời mời bị thu hồi vì vượt năng lực hiện tại của A; A và Quản trị viên nhận thông báo |

---

### FEAT-46 — Quy tắc tách biệt nhiệm vụ

**Mô tả nghiệp vụ:** Doanh nghiệp khai báo các cặp quyền hoặc vai trò không được cùng nằm trong tay một người (ví dụ "tạo chiết khấu" và "duyệt chiết khấu"), và hệ thống ngăn hoặc cảnh báo khi một thay đổi tạo ra vi phạm.

**Vai trò sử dụng chính:** Người có quyền Quản lý vai trò.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Tạo quy tắc: tên, hai vế (mỗi vế là một vai trò, một quyền quản trị hoặc một ô ở mức khác Không có), lý do.
2. Hệ thống liệt kê ngay những người đang vi phạm quy tắc mới.
3. Từ đó, mọi thay đổi (gán vai trò, thêm vào nhóm, sửa vai trò, đổi cấp bậc, quyền tạm thời, thao tác hàng loạt) tạo vi phạm bị chặn hoặc cảnh báo theo `CFG-46-01`.

**Quy tắc nghiệp vụ:**

- **`BR-46.1` (Xét trên quyền hiệu lực):** Vi phạm xét trên quyền hiệu lực của người đó từ mọi nguồn (vai trò, nhóm, quyền tạm thời), không chỉ vai trò gán trực tiếp.

  **Lý do nghiệp vụ:** nếu chỉ xét vai trò trực tiếp, nhận vế thứ hai qua một nhóm là lách được quy tắc.

- **`BR-46.2` (Chặn hoặc cảnh báo):** Ở chế độ Chặn, thay đổi tạo vi phạm bị từ chối. Ở chế độ Cảnh báo, thay đổi chỉ thực hiện được khi người thực hiện nhập lý do; vi phạm được liệt kê trong `FEAT-03`, `FEAT-48`, `FEAT-49`.

  **Lý do nghiệp vụ:** doanh nghiệp nhỏ đôi khi bắt buộc một người kiêm hai việc; cảnh báo kèm lý do giữ được vết cho kiểm toán mà không làm tê liệt vận hành.

- **`BR-46.3` (Vi phạm có sẵn không bị gỡ tự động):** Tạo quy tắc mới không tự gỡ quyền của người đang vi phạm.

  **Lý do nghiệp vụ:** tự gỡ có thể chặn đứng công việc đang chạy; người quản trị quyết định cách xử lý từng người.

- **`BR-46.4` (Người có toàn quyền nằm ngoài quy tắc):** Quy tắc tách biệt nhiệm vụ không áp lên cấp bậc Chủ sở hữu và Quản trị viên; danh sách Người có toàn quyền được liệt kê riêng trong `FEAT-48` và `FEAT-49`. Vế của quy tắc là vai trò, quyền quản trị hoặc ô; nguồn được xét là vai trò, nhóm và quyền tạm thời — chính sách Cho phép và lượt cấp trên bản ghi chỉ nới phạm vi, không tạo vế mới.

  **Lý do nghiệp vụ:** Người có toàn quyền mang mọi quyền theo định nghĩa; áp quy tắc lên họ làm không nâng được ai lên Quản trị viên khi chế độ là Chặn. Kiểm soát với họ là số lượng tối đa, người duyệt thứ hai và rà soát.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-46.1.1` | Quy tắc: vai trò "Tạo chiết khấu" ⊥ "Duyệt chiết khấu", chế độ Chặn; A có "Tạo chiết khấu" | Thêm A vào nhóm mang "Duyệt chiết khấu" | Từ chối, nêu quy tắc bị vi phạm |
| `AC-46.2.1` | Cùng quy tắc, chế độ Cảnh báo | Thêm A vào nhóm | Yêu cầu nhập lý do; sau khi nhập, thành công; `FEAT-03` liệt kê vi phạm |
| `AC-46.3.1` | 3 người đang giữ cả hai vế | Tạo quy tắc | Màn hình liệt kê 3 người; quyền của họ không đổi |
| `AC-46.1.2` | Quy tắc đang có, chế độ Chặn | Duyệt quyền tạm thời mang vế thứ hai cho A | Từ chối ngay ở bước phê duyệt |
| `AC-46.4.1` | Quy tắc tách biệt nhiệm vụ đang ở chế độ Chặn | Chủ sở hữu nâng A lên Quản trị viên | Thành công; báo cáo `FEAT-49` liệt kê A trong danh sách Người có toàn quyền |

---

### FEAT-47 — Chức danh trách nhiệm & người duyệt thứ hai

**Mô tả nghiệp vụ:** Chỉ định các trách nhiệm nghiệp vụ không phải vai trò (Người phụ trách Bảo vệ Dữ liệu, Người phụ trách thanh toán, Người phụ trách bảo mật) và quy định thống nhất cách chọn người duyệt thứ hai cho mọi thao tác cần hai người.

**Vai trò sử dụng chính:** Chủ sở hữu, Quản trị viên.

**Điều kiện tiên quyết:** Người được chỉ định là thành viên Đang hoạt động.

**Luồng chính:**

1. Mở "Chức danh trách nhiệm", chọn chức danh, chỉ định một hoặc nhiều người.
2. Lưu → người được chỉ định nhận thông báo; thay đổi ghi nhật ký thay đổi cấu hình quyền.

**Luồng ngoại lệ:**

- Người được chỉ định bị tạm ngưng hoặc rời workspace → chức danh chuyển trống (hoặc chuyển người theo `FEAT-43`) và hiện cảnh báo `FEAT-03`.
- Người phụ trách thanh toán: chỉ định, thay thế và ràng buộc theo [`billing-subscription-srs.md`](./billing-subscription-srs.md).

**Quy tắc nghiệp vụ:**

- **`BR-47.1` (Chức danh không tự cấp quyền):** Chức danh không thêm ô hay quyền quản trị nào. Nó chỉ xác định ai nhận thông báo, ai được chọn làm người duyệt thứ hai theo quy tắc của phân hệ.

  **Lý do nghiệp vụ:** trộn trách nhiệm với quyền làm rà soát quyền mất chính xác — một người phụ trách bảo vệ dữ liệu không nhất thiết được xem mọi dữ liệu.

- **`BR-47.2` (Chức danh do Người có toàn quyền chỉ định):** Chỉ Chủ sở hữu hoặc Quản trị viên chỉ định và gỡ chức danh; riêng Người phụ trách thanh toán theo thẩm quyền tại [`billing-subscription-srs.md`](./billing-subscription-srs.md) `BR-03.5`.

  **Lý do nghiệp vụ:** người duyệt thứ hai chỉ có giá trị khi chính họ không tự chọn được mình.

- **`BR-47.3` (Quy tắc chọn người duyệt thứ hai):** Khi một thao tác cần người duyệt thứ hai, người duyệt phải: khác người thực hiện; Đang hoạt động; mang chức danh mà quy tắc của thao tác yêu cầu, nếu có. Thiếu người mang chức danh đó hoặc người đó trùng người thực hiện thì người duyệt thứ hai là **một Người có toàn quyền khác** người thực hiện. Workspace chỉ có một Người có toàn quyền Đang hoạt động và không có ai mang chức danh yêu cầu thì thao tác không thực hiện được; màn hình nêu rõ cần chỉ định thêm người.

  **Lý do nghiệp vụ:** yêu cầu bất biến là luôn có hai người khác nhau đứng tên, không phải hai chức danh cụ thể; nếu không có quy tắc thay thế, doanh nghiệp chưa chỉ định chức danh sẽ không làm được thao tác hợp lệ nào.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-47.1.1` | A được chỉ định Người phụ trách Bảo vệ Dữ liệu | Mở quyền hiệu lực của A | Không có ô hay quyền quản trị mới nào |
| `AC-47.3.1` | Thao tác cần "Người phụ trách Bảo vệ Dữ liệu" duyệt; chưa ai mang chức danh; có 2 Quản trị viên | Quản trị viên A thực hiện | Yêu cầu gửi tới Quản trị viên B hoặc Chủ sở hữu, không gửi lại cho A |
| `AC-47.3.2` | Chỉ có Chủ sở hữu là Người có toàn quyền, chưa ai mang chức danh | Chủ sở hữu thực hiện thao tác đó | Từ chối, hướng dẫn chỉ định chức danh hoặc thêm Quản trị viên |
| `AC-47.1.2` | B mang chức danh, bị Tạm ngưng | Mở Sức khỏe phân quyền | Cảnh báo chức danh đang trống |
| `AC-47.2.1` | Thành viên có quyền Sửa người dùng | Mở Chức danh trách nhiệm | Không có thao tác chỉ định hay gỡ |

---

## F. CẤP QUYỀN TẠM THỜI CÓ PHÊ DUYỆT

### FEAT-30 — Yêu cầu cấp quyền tạm thời

**Mô tả nghiệp vụ:** Cấp thêm một vai trò cho một thành viên hoặc một nhóm trong khoảng thời gian giới hạn, cho tình huống cần quyền cao hơn bình thường ngắn hạn (xử lý sự cố, hỗ trợ đột xuất, thay người đi vắng).

**Vai trò sử dụng chính:** Người có quyền Yêu cầu quyền tạm thời.

**Điều kiện tiên quyết:** Workspace có đủ người phê duyệt hợp lệ theo `BR-31.2` và `CFG-31-01`.

**Luồng chính:**

1. Chọn người hoặc nhóm nhận, chọn vai trò, nhập lý do, chọn thời điểm bắt đầu (mặc định ngay khi được duyệt đủ) và thời hạn.
2. Gửi → yêu cầu ở trạng thái **Chờ phê duyệt**, chưa có tác dụng; mọi người phê duyệt hợp lệ nhận thông báo.
3. Người yêu cầu có thể **rút yêu cầu** bất kỳ lúc nào trước khi đủ phê duyệt.

**Luồng ngoại lệ:**

- Yêu cầu cho chính mình, hoặc cho nhóm mà mình là thành viên → từ chối.
- Người hoặc nhóm nhận không thuộc workspace → từ chối.
- Vai trò vượt năng lực của người yêu cầu → từ chối.
- Thời hạn vượt `CFG-30-01` hoặc thiếu lý do → không gửi được, báo ngay trên màn hình.
- Không đủ người phê duyệt hợp lệ → không gửi được, nêu rõ cần bao nhiêu người phê duyệt và gợi ý cấp quyền Phê duyệt quyền tạm thời.
- Không đủ phê duyệt trong `CFG-30-02` → yêu cầu tự hết hạn; người yêu cầu được thông báo.

**Quy tắc nghiệp vụ:**

- **`BR-30.1` (Luôn có hạn và lý do):** Mọi quyền tạm thời bắt buộc có thời hạn không vượt `CFG-30-01` và có lý do. Không có lựa chọn vĩnh viễn.

  **Lý do nghiệp vụ:** đây là cơ chế cấp có kiểm soát, không phải đường tắt để cấp quyền vĩnh viễn nhanh hơn quy trình thường.

- **`BR-30.2` (Không tự xin cho mình):** Người yêu cầu không phải người nhận và không thuộc nhóm nhận.

  **Lý do nghiệp vụ:** Nguyên tắc 2.

- **`BR-30.3` (Trần năng lực, không có Quyền Ủy thác):** Vai trò yêu cầu phải nằm trong năng lực của người yêu cầu; Quyền Ủy thác không miễn trừ ở đây.

  **Lý do nghiệp vụ:** nhờ người khác duyệt hộ một quyền mà bản thân không có là leo thang quyền qua trung gian.

- **`BR-30.4` (Gia hạn là yêu cầu mới):** Gia hạn một quyền tạm thời là một yêu cầu mới, qua đủ phê duyệt. Người nhận được nhắc trước khi hết hạn theo `CFG-30-03`.

  **Lý do nghiệp vụ:** tự gia hạn biến quyền tạm thời thành vĩnh viễn trá hình; báo cáo `FEAT-33` liệt kê người được gia hạn liên tục.

- **`BR-30.5` (Nhóm nhận là nhóm động):** Khi người nhận là nhóm, thành viên vào nhóm trong thời gian hiệu lực cũng nhận quyền; thành viên rời nhóm mất quyền ngay.

  **Lý do nghiệp vụ:** "đội trực sự cố tuần này" thay người liên tục; người quản trị đã duyệt cho đội, không cho danh sách tên cố định. Việc thêm vào nhóm vẫn chịu `BR-18.1`.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-30.1.1` | `CFG-30-01` = 90 ngày | Chọn thời hạn 120 ngày | Ô thời hạn báo lỗi trước khi gửi, nêu tối đa 90 ngày |
| `AC-30.2.1` | A thuộc nhóm "Trực sự cố" | A mở danh sách người hoặc nhóm nhận | Nhóm "Trực sự cố" và chính A không chọn được, kèm giải thích |
| `AC-30.3.1` | A giữ Quyền Ủy thác với vai trò Quản lý trong danh sách, A không có năng lực vai trò Quản lý | Yêu cầu quyền tạm thời vai trò Quản lý cho B | Từ chối, nêu Quyền Ủy thác không áp cho quyền tạm thời |
| `AC-30.1.2` | `CFG-30-02` = 7 ngày | Yêu cầu chỉ có 1/2 phê duyệt sau 7 ngày | Yêu cầu hết hạn; người yêu cầu nhận thông báo |
| `AC-30.4.1` | `CFG-30-03` = 3 ngày | 3 ngày trước khi hết hạn | Người nhận và người yêu cầu nhận nhắc, kèm lối tắt gửi yêu cầu gia hạn |
| `AC-30.5.1` | Quyền tạm thời đang cấp cho nhóm X | Thêm C vào X | C có quyền ngay; gỡ C khỏi X thì C mất quyền ngay |
| `AC-30.1.3` | Workspace có 1 người phê duyệt hợp lệ, `CFG-31-01` = 2 | Gửi yêu cầu | Không gửi được, nêu cần 2 người phê duyệt hợp lệ |

---

### FEAT-31 — Phê duyệt / từ chối yêu cầu

**Mô tả nghiệp vụ:** Người phê duyệt hợp lệ xem xét yêu cầu quyền tạm thời và quyết định.

**Vai trò sử dụng chính:** Người có quyền Phê duyệt quyền tạm thời.

**Điều kiện tiên quyết:** Yêu cầu ở trạng thái Chờ phê duyệt.

**Luồng chính:**

1. Mở danh sách yêu cầu đang chờ mình; xem người nhận, vai trò, quyền sẽ được nới rộng, lý do, thời hạn.
2. Phê duyệt (kèm ghi chú tuỳ chọn) hoặc từ chối (bắt buộc lý do).
3. Đủ số lượt phê duyệt theo `CFG-31-01` → quyền có hiệu lực tại thời điểm bắt đầu đã chọn; người nhận và người yêu cầu được thông báo.
4. Một lượt từ chối → yêu cầu kết thúc ngay.

**Luồng ngoại lệ:**

- Người phê duyệt là người yêu cầu, là người nhận, hoặc thuộc nhóm nhận → không có thao tác phê duyệt.
- Người phê duyệt đã duyệt yêu cầu này → không duyệt lần hai.
- Năng lực của người phê duyệt không còn bao phủ vai trò được yêu cầu tại thời điểm duyệt → từ chối lượt duyệt đó.
- Vi phạm quy tắc tách biệt nhiệm vụ của người nhận → theo `CFG-46-01`.

**Quy tắc nghiệp vụ:**

- **`BR-31.1` (Đủ số lượt phê duyệt độc lập):** Quyền chỉ có hiệu lực khi đủ số lượt phê duyệt của những người khác nhau theo `CFG-31-01`.

  **Lý do nghiệp vụ:** số người cần duyệt tỷ lệ với rủi ro doanh nghiệp chấp nhận; doanh nghiệp nhỏ có thể chỉ có một người đủ thẩm quyền.

- **`BR-31.2` (Người phê duyệt hợp lệ):** Người phê duyệt phải Đang hoạt động, có quyền Phê duyệt quyền tạm thời, có năng lực bao phủ vai trò được yêu cầu, và không phải người yêu cầu, người nhận hay thành viên của nhóm nhận.

  **Lý do nghiệp vụ:** người phê duyệt "bảo chứng" cho quyền được cấp; không có năng lực đó thì họ đang bảo chứng cho thứ họ không được phép cấp.

- **`BR-31.3` (Lượt duyệt giữ giá trị):** Lượt phê duyệt đã đưa ra giữ giá trị dù sau đó người phê duyệt mất quyền, trừ khi họ bị tạm ngưng hoặc rời workspace trước khi yêu cầu đủ phê duyệt (`BR-42.3`).

  **Lý do nghiệp vụ:** quyết định được đưa ra khi người đó có thẩm quyền; nhưng nếu họ bị chặn vì nghi vấn, lượt duyệt chưa hoàn tất của họ không được tiếp tục có hiệu lực.

- **`BR-31.4` (Rút khác từ chối):** Người yêu cầu chỉ rút được yêu cầu của mình, không "từ chối" nó; từ chối luôn đến từ người phê duyệt.

  **Lý do nghiệp vụ:** báo cáo kiểm định phân biệt "người yêu cầu đổi ý" và "người phê duyệt không chấp thuận" — hai tín hiệu khác nhau về rủi ro.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-31.1.1` | `CFG-31-01` = 2; yêu cầu có 1 lượt duyệt | Người nhận thử dùng quyền | Chưa có quyền; yêu cầu vẫn Chờ phê duyệt |
| `AC-31.1.2` | `CFG-31-01` = 1 | Một người phê duyệt hợp lệ duyệt | Quyền có hiệu lực ngay |
| `AC-31.2.1` | B thuộc nhóm nhận | B mở yêu cầu | Không có nút Phê duyệt, có giải thích |
| `AC-31.2.2` | D có quyền Phê duyệt nhưng năng lực không bao phủ vai trò | D mở yêu cầu | Nút Phê duyệt bị vô hiệu, nêu lý do; yêu cầu không xuất hiện trong danh sách chờ của D |
| `AC-31.4.1` | A là người yêu cầu | A mở yêu cầu của mình | Chỉ có nút Rút yêu cầu, không có Từ chối |
| `AC-31.3.1` | E đã duyệt 1/2, sau đó bị Tạm ngưng | Mở yêu cầu | Yêu cầu trở về 0/2 lượt duyệt |

---

### FEAT-32 — Thu hồi, hết hạn & dọn quyền tạm thời

**Mô tả nghiệp vụ:** Chấm dứt quyền tạm thời trước hạn, khi hết hạn, hoặc khi gốc của nó biến mất.

**Vai trò sử dụng chính:** Người có quyền Thu hồi quyền tạm thời; người đã phê duyệt yêu cầu đó; Hệ thống.

**Điều kiện tiên quyết:** Quyền tạm thời đang hiệu lực hoặc đang chờ.

**Luồng chính:**

1. **Thu hồi thủ công:** chọn quyền, nhập lý do, thu hồi.
2. **Hết hạn:** tới thời điểm hết hạn, Hệ thống chấm dứt quyền.
3. **Dọn theo dây chuyền:** Hệ thống thu hồi khi vai trò bị xoá (`BR-28.2`), nhóm nhận bị xoá (`BR-20.4`), người nhận bị gỡ khỏi workspace (`BR-13.1`), hoặc tính năng của vai trò bị tắt theo trần quyền (`BR-05.2`, quyền mất hiệu lực cùng ô).
4. Bản ghi được đánh dấu Đã thu hồi hoặc Đã hết hạn kèm nguyên nhân; không bị xoá khỏi lịch sử.

**Quy tắc nghiệp vụ:**

- **`BR-32.1` (Thu hồi chấm dứt phiên):** Thu hồi thủ công và dọn theo dây chuyền chấm dứt ngay phiên của người bị ảnh hưởng (`BR-11.3`). Hết hạn tự nhiên có hiệu lực từ thao tác kế tiếp và người nhận được thông báo, không bị đăng xuất.

  **Lý do nghiệp vụ:** thu hồi trước hạn thường do sự cố hoặc nghi vấn, cần cắt ngay; hết hạn là sự kiện đã biết trước, người dùng đã được nhắc (`BR-30.4`).

- **`BR-32.2` (Thu hồi không bị chặn):** Thu hồi không bao giờ bị chặn bởi sự cố của nhật ký (`BR-41.6`).

  **Lý do nghiệp vụ:** Nguyên tắc 3.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-32.1.1` | B có quyền tạm thời và đang đăng nhập | Thu hồi thủ công | Phiên của B trong workspace chấm dứt ngay; bản ghi Đã thu hồi kèm lý do |
| `AC-32.1.2` | Quyền tạm thời hết hạn 23:59:59 ngày 10 | 00:00:00 ngày 11, B thao tác | Thao tác cần quyền đó bị từ chối; B không bị đăng xuất; B đã nhận thông báo hết hạn |
| `AC-32.2.1` | Nhật ký thay đổi cấu hình quyền gặp sự cố | Thu hồi thủ công | Thu hồi thành công; nhật ký được ghi bù theo `BR-41.6` |

---

### FEAT-33 — Báo cáo kiểm định quyền tạm thời

**Mô tả nghiệp vụ:** Báo cáo giúp rà soát toàn bộ tình trạng quyền tạm thời.

**Vai trò sử dụng chính:** Người có quyền Xem báo cáo quyền.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Mở báo cáo, chọn khoảng thời gian.
2. Hệ thống liệt kê: yêu cầu đang chờ lâu nhất; yêu cầu hết hạn vì không đủ phê duyệt; quyền đang hiệu lực sắp hết hạn; người nhận quyền tạm thời liên tiếp cùng vai trò từ ngưỡng `CFG-33-01` trở lên; quyền bị thu hồi trước hạn và lý do; quyền còn hiệu lực sau thời hạn (phải bằng 0, `KPI-03`).
3. Xuất báo cáo.

**Quy tắc nghiệp vụ:**

- **`BR-33.1` (Bất thường nổi lên trước):** Báo cáo xếp các bất thường (quyền còn hiệu lực sau hạn, gia hạn liên tục) lên đầu.

  **Lý do nghiệp vụ:** người rà soát có ít thời gian; bất thường lẫn trong danh sách dài sẽ bị bỏ qua.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-33.1.1` | `CFG-33-01` = 3 lần trong 12 tháng; B nhận quyền tạm thời vai trò Quản lý 4 lần trong 12 tháng | Mở báo cáo | B xuất hiện ở mục "gia hạn liên tục", đầu báo cáo |
| `AC-33.1.2` | Báo cáo đang mở | Xuất | Tệp xuất có đủ các mục như trên màn hình; lượt xuất được ghi nhật ký |

---

## G. PHẠM VI HIỂN THỊ DỮ LIỆU

### FEAT-34 — Mức truy cập & mức nền của workspace

**Mô tả nghiệp vụ:** Định nghĩa chính xác các mức truy cập, và cho phép cấu hình **mức nền** của workspace theo từng loại dữ liệu (Khách hàng, Công ty, Cơ hội, Vé hỗ trợ, Công việc, Hội thoại, Chiến dịch và loại khác do phân hệ khai báo): mức dùng cho một ô khi vai trò không khai báo ô đó, và chế độ "công khai đọc".

**Vai trò sử dụng chính:** Người có quyền Quản lý cấu hình workspace (thu hẹp); Người có toàn quyền (nới rộng).

**Điều kiện tiên quyết:** Không có.

**Định nghĩa các mức (từ hẹp đến rộng):**

| Mức | Tập bản ghi |
| --- | --- |
| **Không có** | Không bản ghi nào |
| **Chỉ của mình** | Bản ghi người đó đang phụ trách |
| **Đơn vị của mình** | Chỉ của mình + bản ghi của mọi cấp dưới (trực tiếp và gián tiếp) + bản ghi thuộc Đơn vị chính và các Đơn vị kiêm nhiệm của người đó (kiêm nhiệm mức Chỉ xem chỉ tính cho thao tác Xem, `BR-11.4`) |
| **Đơn vị và các đơn vị con** | Đơn vị của mình + bản ghi thuộc mọi đơn vị con cháu của các đơn vị đó |
| **Toàn workspace** | Mọi bản ghi của loại dữ liệu đó trong workspace |

**Luồng chính:**

1. Mở "Mức nền dữ liệu"; với mỗi loại dữ liệu chọn mức nền cho từng thao tác và bật/tắt "công khai đọc".
2. Bật/tắt "người phụ trách đơn vị xem toàn nhánh" (`CFG-34-02`).
3. Màn hình hiển thị trước số thành viên có phạm vi thay đổi; lưu.

**Quy tắc nghiệp vụ:**

- **`BR-34.1` (Mức nền chỉ lấp ô trống):** Mức nền chỉ áp cho ô mà không vai trò nào của người đó khai báo. Ô đã khai báo trong ít nhất một vai trò dùng giá trị khai báo; mức nền không thu hẹp nó.

  **Lý do nghiệp vụ:** mức nền là lưới an toàn cho vai trò tự tạo chưa hoàn thiện, không phải cách sửa đè quyết định đã có trong vai trò.

- **`BR-34.2` (Theo từng loại dữ liệu):** Mức nền và công khai đọc đặt riêng cho từng loại dữ liệu, ví dụ Vé hỗ trợ công khai đọc trong khi Cơ hội không.

  **Lý do nghiệp vụ:** "ai cũng xem vé để hỗ trợ nhau" và "bảo mật thông tin kinh doanh" cùng tồn tại trong một doanh nghiệp.

- **`BR-34.3` (Người phụ trách đơn vị xem toàn nhánh):** Khi `CFG-34-02` bật, người phụ trách chính và đồng phụ trách của một đơn vị có mức Xem Đơn vị và các đơn vị con trên đơn vị đó, cho mọi loại dữ liệu mà ít nhất một vai trò của họ có ô Xem khác Không có. Đây là nguồn nới phạm vi đi kèm chức vụ, hiển thị trong quyền hiệu lực (`BR-15.1`) và báo cáo `FEAT-49`. Nó chỉ nới mức Xem, không nới thao tác khác.

  **Lý do nghiệp vụ:** trưởng chi nhánh cần thấy toàn bộ chi nhánh dù vai trò chung của họ hẹp hơn; nhưng không được thành quyền sửa ngầm.

- **`BR-34.4` (Công khai đọc chỉ mở thao tác Xem):** Công khai đọc của một loại dữ liệu là một **nguồn nới phạm vi** cho thao tác Xem: mọi thành viên có ô Xem của loại đó khác Không có được xem Toàn workspace. Nó không nâng thao tác nào khác và không tạo quyền cho người có ô Xem = Không có. Nó là ngoại lệ tường minh của `BR-34.1`, hiển thị trong quyền hiệu lực.

  **Lý do nghiệp vụ:** "ai cũng xem được để hỗ trợ nhau" không có nghĩa là ai cũng sửa, xoá hay xuất được dữ liệu của người khác; và người được chủ động đặt Không có không được mở lại qua cấu hình chung.

- **`BR-34.5` (Nới rộng mức nền chỉ do Người có toàn quyền):** Nâng mức nền, bật công khai đọc và bật `CFG-34-02` là thao tác nới rộng cho toàn workspace, chỉ Người có toàn quyền thực hiện. Người có quyền Quản lý cấu hình workspace chỉ thu hẹp được.

  **Lý do nghiệp vụ:** mức nền áp cho mọi người, kể cả người thay đổi nó; không có cách xét trần năng lực trên một thay đổi rộng như vậy (Nguyên tắc 1).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-34.1.1` | Vai trò X khai báo (Cơ hội, Xem) = Chỉ của mình; mức nền Cơ hội Xem = Đơn vị của mình | Người giữ X xem danh sách cơ hội | Chỉ thấy cơ hội của mình |
| `AC-34.1.2` | Vai trò Y không khai báo (Công việc, Xem); mức nền = Đơn vị của mình | Người giữ Y xem danh sách công việc | Thấy công việc của đơn vị mình; quyền hiệu lực ghi nguồn "Mức nền" |
| `AC-34.4.1` | Vé hỗ trợ bật công khai đọc; A có (Vé, Xem) = Chỉ của mình, (Vé, Sửa) = Chỉ của mình | A mở vé của đồng nghiệp ở chi nhánh khác | Xem được, không sửa được |
| `AC-34.4.2` | Vé bật công khai đọc; B có (Vé, Xem) = Không có | B mở danh sách vé | Không thấy vé nào |
| `AC-34.3.1` | `CFG-34-02` bật; C phụ trách "Miền Bắc", vai trò của C (Khách hàng, Xem) = Chỉ của mình | C xem danh sách khách hàng | Thấy khách hàng của mọi đơn vị thuộc nhánh "Miền Bắc"; không sửa được khách hàng của người khác |
| `AC-34.3.2` | Đổi `CFG-34-02` | Bấm lưu | Màn hình hiển thị trước số người có phạm vi thay đổi trước khi xác nhận |
| `AC-34.5.1` | Thành viên có quyền Quản lý cấu hình workspace | Mở cấu hình công khai đọc của Vé hỗ trợ đang tắt | Công tắc bị vô hiệu kèm giải thích "Chỉ Chủ sở hữu hoặc Quản trị viên được nới rộng"; tắt một công khai đọc đang bật thì làm được |
| `AC-34.2.1` | Vé hỗ trợ bật công khai đọc, Cơ hội tắt | Thành viên có (Vé, Xem) và (Cơ hội, Xem) = Chỉ của mình mở hai danh sách | Thấy vé của mọi người, chỉ thấy cơ hội của mình |

---

### FEAT-35 — Quy tắc phân giải phạm vi

**Mô tả nghiệp vụ:** Quy tắc nền hệ thống luôn áp mỗi khi ai đó thao tác trên dữ liệu, để quyết định tập bản ghi họ chạm tới.

**Vai trò sử dụng chính:** Hệ thống.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Xác định thao tác và loại dữ liệu → lấy mức hiệu lực của đúng ô đó (`BR-35.7`).
2. Áp các nguồn nới phạm vi và các nguồn chặn theo thứ tự `BR-39.6`.
3. Trả về tập bản ghi.

**Quy tắc nghiệp vụ:**

- **`BR-35.1` (Hợp nhất theo từng ô, lấy mức rộng nhất):** Người giữ nhiều vai trò hoặc nhóm nhận ở mỗi ô mức rộng nhất trong số các nguồn. Hợp nhất làm theo từng ô: mức rộng của một ô không làm ô khác rộng theo. Người giữ cả Marketing (Khách hàng × Xem: Toàn workspace; Khách hàng × Sửa: Không có) và Nhân viên Kinh doanh (Khách hàng × Xem và Sửa: Đơn vị của mình) xem được toàn workspace nhưng chỉ sửa trong đơn vị của mình. Muốn chặn riêng một trường hợp, dùng chính sách Từ chối (Nhóm H) hoặc thu hồi riêng lẻ (`BR-11.6`).

  **Lý do nghiệp vụ:** giữ thêm một vai trò không bao giờ được làm người dùng mất quyền đang có; còn hợp nhất theo ô ngăn quyền sửa rộng "lây" từ quyền xem rộng.

- **`BR-35.2` (Cấp dưới gồm cả gián tiếp):** Mọi mức từ Đơn vị của mình trở lên gồm toàn bộ chuỗi cấp dưới trực tiếp và gián tiếp.

  **Lý do nghiệp vụ:** bỏ sót cấp dưới gián tiếp làm mức "rộng hơn" thấy ít hơn mức hẹp hơn với giám đốc nhiều tầng.

- **`BR-35.3` (Nhánh chỉ đi xuống):** Mức Đơn vị và các đơn vị con chỉ gồm đơn vị của mình và con cháu, không gồm đơn vị ngang hàng hay cấp trên; với kiêm nhiệm, tính cho từng đơn vị của người đó.

  **Lý do nghiệp vụ:** trưởng chi nhánh không được thấy chi nhánh bên cạnh chỉ vì cùng cấp.

- **`BR-35.4` (Không có đơn vị thì không có phạm vi theo đơn vị):** Thành viên chưa có đơn vị nào chỉ nhận phần "của mình" và "cấp dưới" của các mức theo đơn vị; không bao giờ rơi xuống Toàn workspace.

  **Lý do nghiệp vụ:** Nguyên tắc 7.

- **`BR-35.5` (Thay đổi cây chỉ thu hẹp khi đang tính):** Nếu cây tổ chức thay đổi đúng lúc đang tính phạm vi, phần chưa chắc chắn được loại ra, không bao giờ thêm vào.

  **Lý do nghiệp vụ:** Nguyên tắc 7.

- **`BR-35.6` (Lỗi thì đóng):** Lỗi bất thường khi tính phạm vi dẫn tới từ chối hiển thị, kèm thông báo lỗi có mã tra cứu để người dùng báo hỗ trợ.

  **Lý do nghiệp vụ:** hiển thị nhầm dữ liệu ngoài phạm vi là sự cố bảo mật; không hiển thị là sự cố vận hành, phục hồi được.

- **`BR-35.7` (Mỗi thao tác dùng mức của chính nó):** Tập bản ghi người dùng chạm tới khi thực hiện một thao tác được tính theo mức của đúng ô (loại dữ liệu, thao tác đó): danh sách và chi tiết theo mức Xem; sửa theo mức Sửa — kể cả bước tìm bản ghi cần sửa; xuất theo mức Xuất. Bản ghi thuộc loại dữ liệu khác hiển thị kèm trong thao tác (ví dụ khách hàng liên kết khi mở một cơ hội) theo mức Xem của loại dữ liệu kia, cộng các lượt cấp do phân hệ sinh ra (ví dụ quyền đọc tự động khách hàng của vé hoặc hội thoại đang mở, `BR-39.5`). Thao tác không xác định được thuộc ô nào tính theo mức hẹp nhất.

  **Lý do nghiệp vụ:** nếu mọi thao tác dùng chung một phạm vi, vai trò "xem rộng, sửa hẹp" hoặc bị buộc thành "xem hẹp", hoặc vô tình thành "sửa rộng".

- **`BR-35.8` (Tiến trình chạy thay người dùng):** Tiến trình tự động chạy thay một người (gửi chiến dịch, quy tắc tự động hoá, xuất hàng loạt) ghi nhận thao tác và mức tại lúc khởi chạy, và trong suốt quá trình chạy chỉ chạm tới bản ghi nằm trong **phần giao** của mức lúc khởi chạy và mức hiện tại của người khởi chạy. Khi người khởi chạy bị tạm ngưng hoặc rời workspace, tiến trình tạm dừng và người quản lý trực tiếp cùng Người có toàn quyền được thông báo để chuyển người khởi chạy hoặc dừng hẳn (`FEAT-43`). Kích hoạt lại người khởi chạy không tự chạy tiếp tiến trình; người khởi chạy hoặc Người có toàn quyền chọn chạy tiếp. Khi chuyển người khởi chạy, tiến trình từ đó dùng phần giao của mức lúc khởi chạy và mức hiện tại của người mới. **Ngoại lệ — nguồn tạo bản ghi vào hàng đợi:** một nguồn do Người có toàn quyền cấu hình mà chỉ tạo bản ghi vào hàng đợi của đơn vị tiếp nhận (ví dụ biểu mẫu website, công việc liên hệ lại từ chiến dịch theo [`tasks-srs.md`](./tasks-srs.md) `BR-13.6`) không tạm dừng khi người khởi chạy bị tạm ngưng hay rời đi, và không thuộc bước "Tiến trình đang chạy thay" của `FEAT-43`; khi người khởi chạy không còn Đang hoạt động, người tạo được ghi là "Hệ thống — nguồn <tên nguồn>", và mọi giới hạn lấy theo mức của người khởi chạy (ví dụ trần Gán) không còn áp dụng — bản ghi chỉ vào hàng đợi hoặc theo ngoại lệ giao thẳng mà SRS phân hệ khai báo.

  **Lý do nghiệp vụ:** tiến trình không được là đường vòng để làm điều người khởi chạy không tự làm được — kể cả sau khi quyền của họ đã bị thu hẹp.

- **`BR-35.9` (Đọc ngoài phạm vi phụ trách):** Một lượt xem bản ghi được phép **chỉ nhờ** mức Xem rộng hơn mức Sửa của chính người đó trên loại dữ liệu ấy là "đọc ngoài phạm vi phụ trách". Phân hệ nghiệp vụ ghi nhật ký sự kiện này theo quy tắc của mình (ví dụ [`contacts-srs.md`](./contacts-srs.md) `NFR-07` mục 13), áp như nhau cho mọi vai trò "xem rộng, sửa hẹp". Người có toàn quyền nằm ngoài sự kiện này vì mức Xem và Sửa của họ luôn bằng nhau.

  **Lý do nghiệp vụ:** xem rộng là cần thiết cho nhiều vị trí, nhưng doanh nghiệp cần biết ai đã xem gì ngoài phần việc của mình để phát hiện thu thập dữ liệu bất thường.

- **`BR-35.10` (Phân hệ khai báo hàng đợi và đơn vị tiếp nhận):** Phân hệ có hàng đợi khai báo trong SRS của mình: (a) **đơn vị tiếp nhận** cho từng hàng đợi, kênh hoặc nguồn (ví dụ hộp thư hỗ trợ, biểu mẫu website); (b) loại bản ghi là **bản ghi công việc** (thuộc đơn vị tiếp nhận suốt vòng đời — ví dụ vé hỗ trợ, hội thoại) hay **bản ghi chờ phân công** (chỉ thuộc đơn vị tiếp nhận khi chưa có Người phụ trách — ví dụ khách hàng tiềm năng từ biểu mẫu). Người cấu hình chọn được "gồm các đơn vị con" để một hàng đợi dùng chung cho cả nhánh; khi đó bản ghi của hàng đợi được tính là thuộc mọi đơn vị trong nhánh. Bản ghi nhập từ tệp không qua hàng đợi: Người phụ trách theo cột Người phụ trách của tệp nếu có ánh xạ (mỗi giá trị chịu ô Gán của người nhập); giá trị vượt quyền, trỏ tới người Tạm ngưng, Đã rời hay không tồn tại là dòng lỗi; giá trị trỏ tới thành viên Đang chờ chấp nhận (gồm lời mời Chờ gửi và lời mời đang tạm treo) thì bản ghi được **giữ chỗ** cho người đó: ô Người phụ trách để trống kèm nhãn "giữ chỗ cho" người được mời (nên không ai thấy bản ghi qua mức "của mình" hay chuỗi cấp dưới), thuộc Đơn vị chính ghi trong lời mời (với bản ghi công việc: thuộc đơn vị tiếp nhận của dòng hay của lô, và lời mời phải ghi đơn vị đó là Đơn vị chính hoặc kiêm nhiệm, nếu không thì dòng đó là dòng lỗi), không ai nhận việc được, và bị chặn với mọi người trừ nhóm ngoại lệ ở `BR-39.6` bước 3; khi Đơn vị chính trong lời mời được sửa, bản ghi giữ chỗ không phải bản ghi công việc chuyển theo, còn bản ghi công việc giữ chỗ luôn ở lại đơn vị tiếp nhận — nếu lời mời sau khi sửa không còn ghi đơn vị tiếp nhận đó (Đơn vị chính hay kiêm nhiệm), giữ chỗ chấm dứt, bản ghi thành bản ghi chưa gán của hàng đợi và người phụ trách đơn vị được báo; khi người đó chấp nhận, bản ghi tự gán cho họ nếu vai trò của họ có ô Sửa của loại đó khác Không có, nếu không thì bản ghi thành bản ghi chờ phân công của hàng đợi đơn vị đó và người phụ trách đơn vị được báo; người phụ trách hoặc đồng phụ trách đơn vị giữ chỗ hay đơn vị cấp trên trong nhánh (có ô Gán bao phủ) hoặc Người có toàn quyền gán lại được bản ghi giữ chỗ, việc đó chấm dứt giữ chỗ; khi lời mời bị từ chối, hết hạn, bị thu hồi hay thành viên đang chờ bị gỡ (`FEAT-13`), bản ghi thành bản ghi chờ phân công bình thường của hàng đợi đơn vị đó. Lời mời không ghi Đơn vị chính thì dòng đó là dòng lỗi. Ô Gán của người nhập được xét trên đơn vị mà bản ghi giữ chỗ sẽ thuộc (Đơn vị chính ghi trong lời mời, hoặc đơn vị tiếp nhận với bản ghi công việc). Tệp có thể ánh xạ cột Đơn vị tiếp nhận thay cho cột Người phụ trách để đưa bản ghi vào hàng đợi của một đơn vị (chịu `BR-35.12`). Cả hai cột trống thì người nhập là Người phụ trách; riêng với bản ghi công việc, người nhập chọn một đơn vị tiếp nhận cho cả lô (chịu `BR-35.12`); các dòng không ghi gì có Người phụ trách là người nhập nếu người nhập là thành viên đơn vị đó, nếu không là bản ghi chưa gán của hàng đợi đơn vị đó; dòng chỉ có cột Đơn vị tiếp nhận luôn là bản ghi chưa gán.

  **Lý do nghiệp vụ:** hàng đợi chỉ vận hành được khi đội nhận việc thấy được việc chưa ai nhận; nhưng một khách hàng sống nhiều năm không được mắc kẹt ở đơn vị tiếp nhận ban đầu sau khi đã có người phụ trách, còn một vé thì cần ở lại với đội để cả đội theo dõi.

- **`BR-35.11` (Bản ghi của hàng đợi & nhận việc):** Bản ghi công việc thuộc đơn vị tiếp nhận suốt vòng đời, không đổi theo Người phụ trách (trừ khi được chuyển hàng đợi theo `BR-35.14`). Bản ghi chờ phân công thuộc đơn vị tiếp nhận cho tới khi có Người phụ trách, sau đó theo Người phụ trách như mọi bản ghi. Bản ghi chưa có Người phụ trách không qua hàng đợi thuộc Đơn vị chính của người tạo; người tạo không có Đơn vị chính thì chỉ người tạo và người có mức Toàn workspace thấy; bản ghi do Hệ thống tạo ngoài mọi nguồn có đơn vị tiếp nhận (ví dụ từ một tích hợp chưa khai báo hàng đợi) thì chỉ người có mức Toàn workspace thấy, và `FEAT-03` cảnh báo. Mọi thành viên của đơn vị tiếp nhận — Đơn vị chính hoặc kiêm nhiệm ở bất kỳ mức nào — có ô Xem của loại đó khác Không có đều thấy các bản ghi chưa có người phụ trách của hàng đợi, bất kể mức Xem (một **nguồn nới phạm vi**, `BR-39.6` bước 2); và **nhận việc** được — tự gán mình làm Người phụ trách — khi ô Gán của họ khác Không có và không có nguồn chặn nào áp lên bản ghi. Đây là ngoại lệ tường minh của Nguyên tắc 2 và của `BR-11.4` (kiêm nhiệm Chỉ xem); sau khi nhận, mọi ô mức Chỉ của mình phủ bản ghi đã nhận.

  **Lý do nghiệp vụ:** mọi mức truy cập tính theo Người phụ trách; không có quy tắc này, vé mới chưa gán không ai thấy và đội hỗ trợ không tự nhận việc được. Bản ghi công việc ở lại đơn vị tiếp nhận để đội vẫn theo dõi được sau khi một người đã nhận, và để người ở hai đội nhận việc mà không làm dữ liệu riêng của mình lộ sang đội kia.

- **`BR-35.12` (Đặt và đổi đơn vị tiếp nhận):** Workspace có **đơn vị tiếp nhận mặc định theo loại nguồn** (`CFG-35-01`, ví dụ biểu mẫu website → Kinh doanh, kênh hội thoại → Hỗ trợ), do Người có toàn quyền đặt cho cả workspace và có thể **ghi đè cho từng đơn vị** (ví dụ chi nhánh Đà Nẵng: biểu mẫu → Kinh doanh – Đà Nẵng). Khi tạo một nguồn mới, mặc định được tìm từ Đơn vị chính của người tạo đi lên tới đơn vị gốc, lấy giá trị gần nhất; không có thì dùng mặc định của workspace. Người tạo dùng mặc định tìm được; chọn đơn vị khác cần Người có toàn quyền. (Người tạo không tự chọn được Đơn vị chính của mình: làm vậy là tự cho đơn vị mình thấy hàng đợi bất kể mức Xem, trái Nguyên tắc 2.) Một nguồn chưa có đơn vị tiếp nhận không kích hoạt, công khai hay kết nối được cho tới khi Người có toàn quyền đặt đơn vị tiếp nhận. Chỉ Người có toàn quyền đổi đơn vị tiếp nhận của một nguồn đang có, và bật hay tắt "gồm các đơn vị con". Khi đổi, người thực hiện chọn: bản ghi công việc chưa đóng **ở lại** đơn vị cũ (bản ghi chưa gán trong số đó vẫn hiện trong hàng đợi của đơn vị cũ cho tới khi được nhận) hay **chuyển** sang đơn vị mới; bản ghi chờ phân công chưa gán luôn chuyển sang đơn vị mới. Màn hình hiển thị trước số bản ghi và số người có phạm vi thay đổi, và nêu rõ khi bật "gồm các đơn vị con" thì các đơn vị trong nhánh thấy bản ghi của nhau theo mức của mình. Thao tác thuộc nhật ký thay đổi cấu hình quyền và là nới rộng (`BR-41.5`); trong Phiên triển khai, đặt đơn vị tiếp nhận cho nguồn mới chưa có bản ghi có hiệu lực ngay, đổi nguồn đang có chỉ ở dạng soạn sẵn (`BR-08.7`).

  **Lý do nghiệp vụ:** đơn vị tiếp nhận quyết định ai thấy hàng đợi bất kể mức Xem; đổi nó là nới phạm vi cho cả một đội mà không xét được trên tập bản ghi (Nguyên tắc 1). Mặc định theo loại nguồn để người tạo biểu mẫu không phải chọn, và không vô tình đẩy khách hàng tiềm năng vào hàng đợi Hỗ trợ.

- **`BR-35.13` (Trả về hàng đợi):** "Trả về hàng đợi" (khi chuyển phòng, tạm ngưng, rời workspace) bỏ Người phụ trách. Bản ghi công việc về hàng đợi của **đơn vị tiếp nhận mà nó đang thuộc** (không đổi đơn vị — `BR-35.12`). Bản ghi chờ phân công về hàng đợi **hiện tại** của nguồn đã sinh ra nó; nguồn không còn thì về đơn vị tiếp nhận mặc định của loại nguồn đó, tìm từ đơn vị mà bản ghi đang thuộc đi lên gốc (`CFG-35-01`, `BR-35.12`); bản ghi chờ phân công nhập từ tệp vào hàng đợi một đơn vị (`BR-35.10`) thì về hàng đợi của đơn vị đó; không xác định được hàng đợi nào thì lựa chọn này không hiển thị cho bản ghi đó. Áp cho bản ghi công việc chưa đóng và cho bản ghi chờ phân công ở giai đoạn mà phân hệ khai báo là **chưa chốt** (ví dụ khách hàng tiềm năng chưa chuyển đổi theo [`contacts-srs.md`](./contacts-srs.md) `BR-31.9`).

  **Lý do nghiệp vụ:** việc chưa xong của người rời đi nên quay lại nơi cả đội nhìn thấy, thay vì dồn hết cho một người nhận; nhưng chỉ khi biết chắc nơi đó là đâu.

- **`BR-35.14` (Chuyển một bản ghi công việc sang hàng đợi khác):** Người có ô Gán bao phủ một bản ghi công việc chuyển được bản ghi đó sang hàng đợi của một đơn vị tiếp nhận khác mà phân hệ cho phép chuyển tới; bản ghi thuộc đơn vị mới từ lúc chuyển và mất Người phụ trách nếu người đó không là thành viên của đơn vị mới (Đơn vị chính hay kiêm nhiệm ở bất kỳ mức nào, hoặc thuộc đơn vị con khi hàng đợi bật "gồm các đơn vị con"). Phân hệ không khai báo được chuyển tới đâu thì không chuyển được. Quy tắc chuyển chi tiết (hàng đợi nào chuyển được tới đâu, lý do chuyển) do phân hệ quy định, ví dụ [`tickets-srs.md`](./tickets-srs.md). Lượt chuyển ghi nhật ký hoạt động của bản ghi.

  **Lý do nghiệp vụ:** vé đến nhầm chi nhánh hay cần chuyên môn của đội khác là việc hằng ngày; nếu chỉ đổi được ở cấp nguồn, vé đó không ai xử lý đúng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-35.1.1` | A giữ Marketing và Nhân viên Kinh doanh theo ví dụ `BR-35.1` | A sửa một khách hàng thuộc đơn vị khác | Bị từ chối như với bản ghi ngoài phạm vi; A vẫn xem được khách hàng đó |
| `AC-35.2.1` | B có mức Đơn vị của mình; C báo cáo cho D, D báo cáo cho B | B xem danh sách | Thấy bản ghi của C và D |
| `AC-35.4.1` | E chưa có đơn vị, mức Đơn vị của mình, không có cấp dưới | E xem danh sách | Chỉ thấy bản ghi của mình |
| `AC-35.3.1` | B có mức Đơn vị và các đơn vị con tại "Miền Bắc"; "Miền Nam" ngang hàng | B xem danh sách | Không thấy bản ghi của "Miền Nam" hay của đơn vị cha |
| `AC-35.5.1` | Một đơn vị con của B bị xoá trong khi B đang mở danh sách | B tải lại danh sách | Danh sách không chứa bản ghi nào ngoài phạm vi trước thay đổi; bản ghi của đơn vị đã xoá biến mất trong thời hạn `NFR-04` |
| `AC-35.6.1` | Lỗi bất thường khi tính phạm vi | E mở danh sách | Thấy thông báo lỗi kèm mã tra cứu; không thấy bản ghi nào |
| `AC-35.7.1` | F có (Khách hàng, Xem) = Toàn workspace, (Khách hàng, Xuất) = Chỉ của mình | F xuất từ danh sách đang hiển thị khách hàng của mọi đơn vị | Tệp xuất chỉ chứa khách hàng của F |
| `AC-35.8.1` | G khởi chạy chiến dịch với (Khách hàng, Xem) = Toàn workspace, sau đó bị thu hẹp về Đơn vị của mình | Chiến dịch tiếp tục gửi | Chỉ gửi tới khách hàng trong Đơn vị của G |
| `AC-35.8.2` | G bị Tạm ngưng khi chiến dịch đang gửi | Chiến dịch | Tạm dừng; quản lý trực tiếp của G và Người có toàn quyền nhận thông báo |
| `AC-35.9.1` | H có (Khách hàng, Xem) = Toàn workspace, (Khách hàng, Sửa) = Chỉ của mình | H mở khách hàng của người khác | Lượt xem được ghi là "đọc ngoài phạm vi phụ trách" |
| `AC-35.11.1` | Kênh hộp thư hỗ trợ khai báo đơn vị tiếp nhận "Hỗ trợ"; vé mới chưa gán | Nhân viên Hỗ trợ có (Vé, Xem) = Đơn vị của mình mở hàng đợi | Thấy vé mới; bấm Nhận việc thì vé được gán cho mình |
| `AC-35.11.2` | Hàng đợi chưa khai báo đơn vị tiếp nhận, vé do Hệ thống tạo | Mở Sức khỏe phân quyền | Có cảnh báo hàng đợi chưa khai báo đơn vị tiếp nhận |
| `AC-35.11.3` | Nhân viên tạo một khách hàng chưa gán người phụ trách, không qua hàng đợi | Thành viên cùng Đơn vị chính của người tạo có mức Đơn vị của mình xem danh sách | Thấy khách hàng đó |
| `AC-35.11.4` | Nhân viên Hỗ trợ có (Vé, Xem) = Chỉ của mình, thuộc đơn vị tiếp nhận | Mở hàng đợi | Thấy vé chưa gán của đơn vị mình; nhận việc được |
| `AC-35.11.5` | Thành viên đội Kinh doanh, không thuộc đơn vị tiếp nhận "Hỗ trợ" | Thử nhận một vé chưa gán của "Hỗ trợ" | Không thấy vé đó trong hàng đợi của mình |
| `AC-35.11.6` | E kiêm nhiệm "Hỗ trợ" mức Chỉ xem, vai trò Nhân viên Hỗ trợ (Gán = Chỉ của mình) | Nhận một vé chưa gán của "Hỗ trợ" | Thành công; vé vẫn thuộc đơn vị "Hỗ trợ", trưởng nhóm Hỗ trợ vẫn thấy |
| `AC-35.11.7` | F thuộc "Hỗ trợ", ô (Vé, Gán) = Không có | Mở hàng đợi | Thấy vé chưa gán; nút Nhận việc bị vô hiệu kèm giải thích |
| `AC-35.11.8` | Khách hàng tiềm năng từ biểu mẫu vào hàng đợi của "Kinh doanh", nhân viên H nhận | H chuyển sang "Miền Nam" và chọn đi theo người | Khách hàng tiềm năng chuyển sang "Miền Nam" cùng H; đội "Kinh doanh" cũ không còn thấy |
| `AC-35.11.9` | G có vai trò Quản lý, Đơn vị chính là chi nhánh Đà Nẵng; vé chưa gán trong hàng đợi "Hỗ trợ – Đà Nẵng" (đơn vị con) | G gán vé cho một nhân viên | Thành công, vì Gán của Quản lý là Đơn vị và các đơn vị con |
| `AC-35.12.1` | Đổi đơn vị tiếp nhận của hộp thư hỗ trợ từ "Hỗ trợ" sang "CSKH Miền Bắc" | Quản trị viên chọn chuyển bản ghi công việc chưa đóng | Màn hình hiển thị trước số vé và số người bị ảnh hưởng; sau khi xác nhận, vé chưa đóng thuộc "CSKH Miền Bắc" |
| `AC-35.12.3` | Đổi đơn vị tiếp nhận, chọn "ở lại"; còn 6 vé chưa gán | Thành viên đơn vị cũ mở hàng đợi | Vẫn thấy và nhận được 6 vé đó; vé mới vào hàng đợi của đơn vị mới |
| `AC-35.12.4` | `CFG-35-01` biểu mẫu website = "Kinh doanh"; nhân viên Marketing tạo biểu mẫu mới | Chọn đơn vị tiếp nhận | "Kinh doanh" được chọn sẵn; các đơn vị khác, kể cả Đơn vị chính của người tạo, bị vô hiệu kèm giải thích |
| `AC-35.12.5` | Phiên triển khai đang mở | Nhân sự triển khai đặt đơn vị tiếp nhận cho hộp thư mới chưa có vé | Có hiệu lực ngay; đổi đơn vị tiếp nhận của hộp thư đang có vé thì ở trạng thái soạn sẵn |
| `AC-35.12.6` | Biểu mẫu website: workspace mặc định "Kinh doanh – Hà Nội", chi nhánh Đà Nẵng ghi đè "Kinh doanh – Đà Nẵng" | Giám đốc chi nhánh Đà Nẵng (Đơn vị chính: chi nhánh Đà Nẵng) tạo biểu mẫu | "Kinh doanh – Đà Nẵng" được chọn sẵn |
| `AC-35.12.7` | Không có mặc định nào, người tạo không có Đơn vị chính | Tạo biểu mẫu website và bấm công khai | Nút công khai bị vô hiệu kèm giải thích cần Người có toàn quyền đặt đơn vị tiếp nhận |
| `AC-35.13.1` | Nhân viên Kinh doanh rời đi, có 15 khách hàng tiềm năng chưa chuyển đổi đến từ biểu mẫu website | Chọn "trả về hàng đợi" cho khách hàng tiềm năng | 15 bản ghi không còn Người phụ trách, hiện trong hàng đợi hiện tại của biểu mẫu đó |
| `AC-35.13.2` | Khách hàng tiềm năng do chính nhân viên nhập từ tệp | Mở lựa chọn xử lý bản ghi | Không có "trả về hàng đợi" cho các bản ghi này |
| `AC-35.13.3` | Hộp thư đổi đơn vị tiếp nhận từ "Hỗ trợ A" sang "Hỗ trợ B", chọn "ở lại"; S ở "Hỗ trợ A" đang giữ vé cũ | S rời workspace, chọn trả về hàng đợi | Vé về hàng đợi của "Hỗ trợ A", không sang "Hỗ trợ B" |
| `AC-35.13.4` | Biểu mẫu sinh khách hàng tiềm năng đã bị xoá; mặc định của loại nguồn là "Kinh doanh" | Trả về hàng đợi khách hàng tiềm năng đó | Về hàng đợi "Kinh doanh" |
| `AC-35.13.5` | Khách hàng tiềm năng của chi nhánh Đà Nẵng; biểu mẫu gốc đã bị xoá; Đà Nẵng ghi đè mặc định "Kinh doanh – Đà Nẵng" | Trả về hàng đợi | Về hàng đợi "Kinh doanh – Đà Nẵng", không về mặc định của workspace |
| `AC-35.14.1` | Vé của "CSKH trung tâm"; nhân viên có (Vé, Gán) bao phủ vé | Chuyển sang hàng đợi "Hỗ trợ – Đà Nẵng" | Vé thuộc "Hỗ trợ – Đà Nẵng", hiện trong hàng đợi chưa gán của đội đó; nhật ký vé ghi lượt chuyển |
| `AC-35.14.2` | Phân hệ không khai báo cho phép chuyển từ hàng đợi A sang B | Người có ô Gán thử chuyển vé | Hàng đợi B không có trong danh sách chuyển tới |
| `AC-35.10.3` | Nhân viên Kinh doanh nhập 200 khách hàng từ tệp | Nhập xong | Nhân viên đó là Người phụ trách của 200 khách hàng; không có bản ghi nào vào hàng đợi |
| `AC-35.10.4` | Tệp có cột Người phụ trách; 3 dòng trỏ tới người Tạm ngưng | Nhập | 3 dòng báo lỗi nêu lý do; các dòng khác nhập bình thường |
| `AC-35.10.5` | Quản trị viên xác nhận lô nhập sau khi đã gửi lời mời; 1.200 dòng gán cho nhân viên N còn Đang chờ chấp nhận | Lô chạy; đồng nghiệp cùng đội mở hàng đợi; sau đó N chấp nhận | Đồng nghiệp không thấy và không nhận được 1.200 bản ghi; khi N chấp nhận, bản ghi tự gán cho N |
| `AC-35.10.7` | Như trên nhưng N từ chối lời mời | — | 1.200 bản ghi thành bản ghi chờ phân công trong hàng đợi của Đơn vị chính ghi trong lời mời của N; đội nhận việc được |
| `AC-35.10.8` | Bản ghi giữ chỗ cho N trong "Kinh doanh – Đà Nẵng"; H có vai trò Kiểm toán (Xem Toàn workspace) | H mở danh sách khách hàng | Không thấy các bản ghi giữ chỗ |
| `AC-35.10.9` | Vai trò trong lời mời của N không có Sửa Khách hàng | N chấp nhận | Bản ghi không tự gán cho N; thành bản ghi chờ phân công của đơn vị; người phụ trách đơn vị được báo |
| `AC-35.10.10` | Bản ghi giữ chỗ cho N trong "Kinh doanh – Đà Nẵng"; G là người phụ trách chi nhánh Đà Nẵng (đơn vị cấp trên), vai trò Quản lý có Xem = Đơn vị và các đơn vị con | G mở danh sách khách hàng | Thấy các bản ghi giữ chỗ, nhãn "giữ chỗ cho N"; không có nút nhận việc |
| `AC-35.10.11` | Như trên nhưng vai trò của G có Xem = Đơn vị của mình và `CFG-34-02` tắt | G mở danh sách | Không thấy bản ghi giữ chỗ; giải thích quyền nêu mức ô Xem không bao phủ |
| `AC-35.10.12` | Bản ghi giữ chỗ cho N; người phụ trách "Kinh doanh – Đà Nẵng" có ô Gán bao phủ | Gán lại 10 bản ghi cho K | 10 bản ghi thuộc K, hết giữ chỗ; khi N chấp nhận, chỉ các bản ghi còn lại tự gán cho N |
| `AC-35.10.13` | Bản ghi giữ chỗ cho N; Quản trị viên gỡ N khỏi workspace trước khi N chấp nhận | Hoàn tất gỡ | Bản ghi thành bản ghi chờ phân công của hàng đợi "Kinh doanh – Đà Nẵng" |
| `AC-35.10.14` | Bản ghi giữ chỗ cho N; lời mời ghi quản lý trực tiếp M thuộc đơn vị khác, vai trò của M có Xem = Đơn vị của mình (gồm chuỗi cấp dưới) | M mở danh sách khách hàng | Không thấy bản ghi giữ chỗ, vì ô Người phụ trách đang trống |
| `AC-35.10.15` | Vé giữ chỗ cho N thuộc hàng đợi "CSKH"; lời mời ghi Đơn vị chính "Kỹ thuật", kiêm nhiệm "CSKH"; M là quản lý trực tiếp ghi trong lời mời, thuộc "Kỹ thuật", Xem vé = Đơn vị của mình | M mở danh sách vé | Không thấy vé giữ chỗ (vé thuộc "CSKH") |
| `AC-35.10.16` | Như trên | Quản trị viên sửa lời mời, bỏ kiêm nhiệm "CSKH" | Giữ chỗ chấm dứt; vé thành vé chưa gán của hàng đợi "CSKH"; người phụ trách "CSKH" được báo |
| `AC-35.10.6` | Người nhập có (Khách hàng, Gán) = Chỉ của mình | Nhập tệp có cột Người phụ trách trỏ tới đồng nghiệp | Các dòng đó báo lỗi vượt quyền |
| `AC-35.10.2` | Hàng đợi khai báo đơn vị tiếp nhận "Miền Bắc", chọn gồm các đơn vị con | Nhân viên Hỗ trợ ở "Chi nhánh Hà Nội" (con của Miền Bắc) mở hàng đợi | Thấy và nhận được vé chưa gán |
| `AC-35.12.2` | Kênh hộp thư hỗ trợ có đơn vị tiếp nhận "Hỗ trợ" | Thành viên có quyền Quản lý cấu hình workspace, không có toàn quyền, mở cấu hình kênh | Thấy đơn vị tiếp nhận ở dạng chỉ đọc; Quản trị viên đổi được |
| `AC-35.8.3` | Tiến trình đang tạm dừng vì người khởi chạy bị tạm ngưng | Kích hoạt lại người khởi chạy | Tiến trình vẫn tạm dừng; người khởi chạy thấy lựa chọn Chạy tiếp |
| `AC-35.8.4` | Chiến dịch do M khởi chạy có nguồn liên hệ lại; M đã rời workspace | Khách hàng trả lời chiến dịch | Công việc liên hệ lại vẫn được tạo vào hàng đợi đơn vị tiếp nhận, người tạo ghi "Hệ thống — nguồn <tên tài khoản gửi>" (ví dụ "Hệ thống — nguồn ShopABC"); bước rời workspace của M không liệt kê nguồn này |

---

## H. CHÍNH SÁCH TRUY CẬP NÂNG CAO

### FEAT-36 — Tạo & quản lý chính sách truy cập theo điều kiện

**Mô tả nghiệp vụ:** Luật theo điều kiện cho những điều vai trò và phạm vi không diễn đạt được, ví dụ "chỉ được sửa hợp đồng khi đang ở trạng thái Nháp", "chỉ được xuất dữ liệu trong giờ hành chính", "được sửa cơ hội của cả đơn vị khi cơ hội đang ở giai đoạn Đàm phán".

**Vai trò sử dụng chính:** Người có quyền Quản lý chính sách truy cập.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Chọn loại dữ liệu và thao tác áp dụng (hoặc mọi loại, mọi thao tác).
2. Chọn hiệu lực: **Từ chối** hoặc **Cho phép**; với Cho phép, chọn mức phạm vi được nới tới.
3. Thêm một hoặc nhiều điều kiện, mỗi điều kiện so sánh một thuộc tính của người dùng, của bản ghi hoặc của thời điểm (theo Mục 2.3) với một giá trị; mọi điều kiện của một chính sách phải cùng đúng.
4. Chọn đối tượng áp dụng: mọi thành viên, hoặc một số vai trò, nhóm, đơn vị.
5. Đặt thứ tự hiển thị; mô phỏng (`FEAT-37`); lưu và bật.

**Luồng ngoại lệ:**

- Điều kiện không đánh giá được (thiếu dữ liệu, dữ liệu không hợp lệ): chính sách Từ chối coi như áp dụng; chính sách Cho phép coi như không áp dụng.
- Chính sách có điều kiện thuộc loại `BR-36.4` gắn với thao tác có danh sách → từ chối lưu.

**Quy tắc nghiệp vụ:**

- **`BR-36.1` (Từ chối luôn thắng):** Nhiều chính sách cùng áp dụng thì một chính sách Từ chối thắng mọi chính sách Cho phép.

  **Lý do nghiệp vụ:** luật chặn thường là luật tuân thủ; để luật mở thắng là để một chính sách mới vô tình vô hiệu hoá luật tuân thủ cũ.

- **`BR-36.2` (Cho phép chỉ nới phạm vi, không cấp năng lực):** Chính sách Cho phép chỉ nới **phạm vi** của một thao tác mà người đó đã có (ô khác Không có), tới mức được chọn; không bao giờ tạo quyền trên ô Không có, và không nới phân quyền trường.

  **Lý do nghiệp vụ:** chính sách là công cụ tinh chỉnh; nếu nó cấp được năng lực mới, mọi kiểm soát trần năng lực ở vai trò đều bị lách qua chính sách.

- **`BR-36.3` (Điều kiện "chứa đoạn văn bản"):** Điều kiện dạng "chứa đoạn văn bản" (ví dụ email chứa "@congty.com") chỉ có tác dụng khi xem chi tiết từng bản ghi và khi sửa, không lọc được danh sách và kết quả xuất. Màn hình cảnh báo tường minh ngay khi người dùng chọn toán tử này: "điều kiện này chỉ bảo vệ trang chi tiết, không lọc màn hình danh sách". Với chính sách Từ chối trên thao tác Xem hoặc Xuất, hệ thống đề nghị dùng toán tử khác.

  **Lý do nghiệp vụ:** người quản trị phải biết trước giới hạn để không tin nhầm rằng danh sách đã được bảo vệ.

- **`BR-36.4` (So sánh hai thuộc tính của bản ghi):** Điều kiện so sánh hai thuộc tính của cùng bản ghi chỉ được lưu cho thao tác trên một bản ghi đơn lẻ (Tạo, Sửa, Xoá, Gán và thao tác đặc thù trên một bản ghi); không được lưu cho Xem, Xuất, Nhập. Từ chối ngay lúc lưu.

  **Lý do nghiệp vụ:** điều kiện loại này không lọc được danh sách; cho lưu trên thao tác có danh sách là tạo ra một luật không có tác dụng ở nơi người quản trị tin nó có.

- **`BR-36.5` (Không áp lên Người có toàn quyền):** Chính sách truy cập không áp lên Chủ sở hữu và Quản trị viên.

  **Lý do nghiệp vụ:** một chính sách Từ chối "mọi loại, mọi thao tác" cấu hình nhầm không được khoá chính người có quyền sửa nó; ràng buộc cần áp lên Người có toàn quyền được khai báo bằng Sàn bắt buộc.

- **`BR-36.6` (Chính sách chịu trần năng lực):** Người **tạo, sửa, bật hay khôi phục** một chính sách Cho phép không được đặt mức nới vượt mức hiệu lực của chính mình ở đúng ô đó, và không được làm việc này với chính sách có đối tượng chứa chính mình. Tắt, xoá, hoặc sửa để một chính sách Từ chối áp cho ít trường hợp hơn là nới rộng: chỉ làm được khi chính sách không áp lên người thực hiện, và ở các ô chính sách áp dụng, mức hiệu lực của người thực hiện không hẹp hơn mức của bất kỳ ai chính sách đang áp lên. Khôi phục phiên bản chịu cùng kiểm tra (`BR-38.1`).

  **Lý do nghiệp vụ:** Nguyên tắc 1 và 2; nếu chỉ người tạo bị ràng buộc, người khác sửa một chính sách có sẵn đang nhắm vào mình là tự cấp phạm vi rộng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-36.1.1` | Chính sách Cho phép "sửa cơ hội của đơn vị khi ở giai đoạn Đàm phán" và chính sách Từ chối "không sửa cơ hội đã Thắng" | A sửa cơ hội đã Thắng của đồng nghiệp | Bị từ chối |
| `AC-36.2.1` | A có (Cơ hội, Sửa) = Chỉ của mình; chính sách Cho phép nới tới Đơn vị của mình khi giai đoạn = Đàm phán | A sửa cơ hội Đàm phán của đồng nghiệp cùng đơn vị | Thành công |
| `AC-36.2.2` | B có (Cơ hội, Sửa) = Không có; cùng chính sách | B sửa cơ hội Đàm phán | Bị từ chối |
| `AC-36.3.1` | Màn hình tạo chính sách | Chọn toán tử "chứa đoạn văn bản" | Cảnh báo hiển thị ngay, trước khi lưu |
| `AC-36.4.1` | Chính sách cho thao tác Xem | Thêm điều kiện so sánh hai thuộc tính của bản ghi | Lưu bị từ chối, nêu lý do |
| `AC-36.5.1` | Chính sách Từ chối "mọi loại, mọi thao tác" bật nhầm | Quản trị viên mở màn hình chính sách | Quản trị viên vẫn thao tác được và tắt được chính sách |
| `AC-36.1.2` | Chính sách Từ chối có điều kiện thiếu dữ liệu trên bản ghi | Thao tác trên bản ghi đó | Bị từ chối |
| `AC-36.6.1` | A có (Cơ hội, Sửa) = Đơn vị của mình | Tạo chính sách Cho phép nới (Cơ hội, Sửa) tới Toàn workspace | Mức Toàn workspace không chọn được, nêu ô vượt trần |
| `AC-36.6.2` | Chính sách Từ chối "không xuất ngoài giờ" đang áp lên A | A tắt chính sách | Công tắc bị vô hiệu kèm giải thích |
| `AC-36.6.3` | Chính sách Cho phép do B tạo đang nhắm vào nhóm của A | A nâng mức nới của chính sách | Thao tác sửa mức nới bị vô hiệu kèm giải thích |
| `AC-36.6.4` | G có (Khách hàng, Xuất) = Chỉ của mình; chính sách Từ chối "không xuất ngoài giờ" áp lên đội có Xuất = Toàn workspace | G tắt chính sách | Công tắc bị vô hiệu kèm giải thích ô vượt trần |

---

### FEAT-37 — Mô phỏng & kiểm thử chính sách

**Mô tả nghiệp vụ:** Thử một chính sách (kể cả chưa lưu) với người dùng và bản ghi giả định hoặc có thật để xem kết quả trước khi bật.

**Vai trò sử dụng chính:** Người có quyền Quản lý chính sách truy cập.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Chọn người dùng (có thật hoặc giả định), bản ghi (có thật hoặc giả định), thao tác, thời điểm.
2. Hệ thống trả về: được hay không; những chính sách nào khớp, chính sách nào quyết định; kết quả nếu không có chính sách đang thử.

**Quy tắc nghiệp vụ:**

- **`BR-37.1` (Mô phỏng không thay đổi gì):** Mô phỏng không cấp quyền, không ghi dữ liệu nghiệp vụ; khi dùng bản ghi có thật, kết quả chỉ nêu được/không được và lý do, không hiển thị nội dung bản ghi vượt quyền của người mô phỏng.

  **Lý do nghiệp vụ:** công cụ kiểm thử không được thành công cụ xem dữ liệu ngoài phạm vi.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-37.1.1` | Chính sách chưa lưu | Mô phỏng với người dùng A, cơ hội có thật ngoài phạm vi của người mô phỏng | Hiển thị kết quả và chính sách quyết định; không hiển thị nội dung cơ hội |
| `AC-37.1.2` | Mô phỏng xong | Kiểm tra danh sách chính sách | Chính sách vẫn ở trạng thái chưa lưu; quyền của A không đổi |

---

### FEAT-38 — Lịch sử phiên bản & khôi phục chính sách

**Mô tả nghiệp vụ:** Mọi thay đổi chính sách tạo phiên bản lịch sử; khôi phục về phiên bản cũ dưới dạng phiên bản mới.

**Vai trò sử dụng chính:** Người có quyền Quản lý chính sách truy cập.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Mở lịch sử, so sánh hai phiên bản.
2. Khôi phục → phiên bản mới ghi "khôi phục từ phiên bản N".

**Quy tắc nghiệp vụ:**

- **`BR-38.1` (Khôi phục chịu đủ kiểm tra):** Khôi phục chịu `BR-36.4`, `BR-36.6` và mọi quy tắc hiện hành như một lần sửa.

  **Lý do nghiệp vụ:** phiên bản cũ có thể được lưu trước khi quy tắc hiện hành có hiệu lực.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-38.1.1` | Phiên bản 2 có điều kiện vi phạm `BR-36.4` | Khôi phục phiên bản 2 | Từ chối, nêu điều kiện vi phạm |
| `AC-38.1.2` | Chính sách có 3 phiên bản | Khôi phục phiên bản 1 | Có phiên bản 4 ghi "khôi phục từ phiên bản 1" |

---

## I. PHÂN QUYỀN THEO BẢN GHI

### FEAT-39 — Cấp / chặn / xem quyền trên một bản ghi

**Mô tả nghiệp vụ:** Cấp hoặc chặn quyền trên một bản ghi cụ thể cho một người hoặc một nhóm, cho trường hợp đặc biệt mà quy tắc chung không đáp ứng (ví dụ khách hàng VIP chỉ hai người được động vào).

**Vai trò sử dụng chính:** Người có quyền Quản lý quyền trên bản ghi; phân hệ nghiệp vụ (lượt cấp tự động theo `BR-39.5`).

**Điều kiện tiên quyết:** Người thao tác có mức Xem bao phủ bản ghi; với lượt cấp Chỉnh sửa, có cả mức Sửa bao phủ bản ghi.

**Luồng chính:**

1. Mở bản ghi → Quyền trên bản ghi → thêm lượt cấp (mức trần: Chỉ đọc hoặc Chỉnh sửa; thời hạn tuỳ chọn) hoặc lượt chặn, cho một người hoặc một nhóm, kèm lý do.
2. Xem danh sách lượt cấp/chặn, nguồn sinh, người tạo, hạn.
3. Gỡ một lượt hoặc gỡ tất cả lượt đặt tay.

**Quy tắc nghiệp vụ:**

- **`BR-39.1` (Chặn thắng tuyệt đối):** Lượt chặn thắng mọi vai trò, mức nền, chính sách Cho phép và mọi lượt cấp, kể cả lượt cấp do phân hệ sinh ra (`BR-39.5`). Khi một lượt chặn vô hiệu một lượt cấp, người tạo lượt cấp được báo rằng lượt đó không có hiệu lực, ở mức không lộ nội dung bản ghi. Lượt chặn không áp lên Người có toàn quyền (`BR-36.5`, cùng lý do).

  **Lý do nghiệp vụ:** lượt chặn là quyết định hiếm, có chủ đích, thường mang nghĩa pháp lý hoặc hợp đồng; nếu một lượt chia sẻ thường ngày gỡ được nó thì nó vô giá trị. Không báo người chia sẻ thì công việc dừng ở chỗ không ai biết là đang dừng.

- **`BR-39.2` (Không có lượt riêng thì theo quy tắc chung):** Bản ghi không có lượt cấp/chặn nào theo đúng vai trò, phạm vi và chính sách.

  **Lý do nghiệp vụ:** quyền trên bản ghi là ngoại lệ, không phải lớp bắt buộc.

- **`BR-39.3` (Đồng bộ ở mọi nơi bản ghi xuất hiện):** Lượt cấp và lượt chặn có hiệu lực ở mọi nơi bản ghi xuất hiện: màn hình danh sách, tìm kiếm, kết quả xuất, số liệu tổng hợp trên bảng điều khiển, và danh sách bản ghi liên quan bên trong bản ghi khác. Bản ghi bị chặn biến mất khỏi mọi nơi đó; bản ghi được cấp xuất hiện ở đúng những nơi đó, dù vai trò thông thường không cho phép.

  **Lý do nghiệp vụ:** chặn chỉ ở trang chi tiết là rò rỉ dữ liệu thật; cấp mà không tìm thấy trong danh sách làm người được cấp tưởng hệ thống lỗi.

- **`BR-39.4` (Liên kết tới bản ghi bị chặn):** Khi bản ghi bị chặn xuất hiện gián tiếp qua liên kết trong bản ghi khác, hệ thống hiển thị nhãn trung lập "Bị hạn chế truy cập" thay vì ẩn liên kết, và không lộ thông tin nào của bản ghi bị chặn.

  **Lý do nghiệp vụ:** ẩn hẳn làm người dùng tưởng mất dữ liệu; hiển thị tên là lộ thông tin.

- **`BR-39.5` (Lượt cấp do phân hệ sinh ra):** Cơ chế này nhận cả lượt cấp do phân hệ nghiệp vụ sinh ra, ví dụ Đội ngũ phụ trách bản ghi và quyền đọc tự động của tuyến hỗ trợ tại [`contacts-srs.md`](./contacts-srs.md) `FEAT-35`. Lượt cấp loại này mang **mức trần** (Chỉ đọc hoặc Chỉnh sửa), **thời hạn** (tự thu hồi khi hết hạn hoặc khi điều kiện sinh ra nó kết thúc) và **nguồn sinh**. Hai ràng buộc: (a) chỉ nới phạm vi, không cấp năng lực — quyền hiệu lực trên bản ghi là phần giao của năng lực theo vai trò (ô khác Không có) và mức trần của lượt cấp; (b) `BR-39.3` áp nguyên văn.

  **Lý do nghiệp vụ:** không có (a), mỗi người phụ trách bản ghi thành người cấp quyền không kiểm soát, mở lại lỗ hổng mà `FEAT-11` đã đóng; không có (b), bản ghi được chia sẻ chỉ mở được qua đường dẫn trực tiếp.

- **`BR-39.6` (Thứ tự hợp nhất quyền):** Quyền hiệu lực của một người trên một bản ghi được quyết định theo đúng thứ tự:
  1. **Mức theo ô** — lấy mức rộng nhất từ vai trò, nhóm và mức nền, trừ thu hồi riêng lẻ; hợp với quyền tạm thời đang hiệu lực (thu hồi riêng lẻ không thắng quyền tạm thời, `BR-11.6`); rồi áp trần quyền của workspace (`BR-35.1`, `BR-34.1`, `FEAT-05`). Ô = Không có thì dừng: không có quyền, không nguồn nào ở bước 2 mở lại được.
  2. **Nguồn nới phạm vi** — công khai đọc (`BR-34.4`), phụ trách đơn vị (`BR-34.3`), thành viên đơn vị tiếp nhận của hàng đợi (`BR-35.11`), chính sách Cho phép (`BR-36.2`), lượt cấp trên bản ghi (`BR-39.5`). Các nguồn này chỉ mở rộng **tập bản ghi**, không bao giờ mở thao tác mà bước 1 đã là Không có, và lượt cấp không vượt mức trần của chính nó.
  3. **Nguồn chặn** — chính sách Từ chối (`BR-36.1`), lượt chặn trên bản ghi (`BR-39.1`), Sàn bắt buộc (`BR-25.3`), **bản ghi giữ chỗ** cho người chưa chấp nhận lời mời (`BR-35.10`: chặn mọi người trừ **nhóm ngoại lệ** — người phụ trách và đồng phụ trách đơn vị giữ chỗ và các đơn vị cấp trên trong nhánh, quản lý trực tiếp ghi trong lời mời, Người có toàn quyền; người có mức Toàn workspace không thuộc nhóm ngoại lệ cũng không thấy. Nhóm ngoại lệ chỉ là **không bị chặn**, không phải nguồn nới: người trong nhóm thấy và thao tác bản ghi giữ chỗ khi và chỉ khi bước 1 và 2 cho họ quyền đó trên bản ghi thuộc đơn vị mà bản ghi giữ chỗ đang thuộc (theo `BR-35.10`), trừ Người có toàn quyền; không ai nhận việc được, và gán lại chỉ theo `BR-35.10`. Giải thích quyền (`FEAT-49`) nêu nguồn này khi nó chặn). Nguồn chặn thắng mọi kết quả của bước 1 và 2.
  4. **Phân quyền trường và che dữ liệu** — quyết định thấy gì khi đã vào được bản ghi, theo nguyên tắc hạn chế thắng của ADR-0001 và khung `FEAT-40`. Không nguồn nào ở bước 2 nới lỏng được bước 4.

  **Lý do nghiệp vụ:** một thứ tự duy nhất làm mọi quyết định truy cập giải thích được (`BR-15.1`) và loại bỏ khả năng một nguồn nới rộng vô tình thắng một nguồn chặn. Hệ quả cố ý: một lượt chia sẻ mở cửa vào bản ghi nhưng không nới bất kỳ mức che trường nào.

- **`BR-39.7` (Lượt đặt tay chịu trần):** Mức trần của lượt cấp đặt tay không vượt mức hiệu lực của người cấp trên chính bản ghi đó (cấp Chỉnh sửa cần người cấp sửa được bản ghi); không cấp cho chính mình hay nhóm mình thuộc; không gỡ lượt chặn đang áp lên chính mình hay nhóm mình thuộc.

  **Lý do nghiệp vụ:** Nguyên tắc 1 và 2; người chỉ có quyền xem không được trao quyền sửa cho người khác, và lượt chặn không được để chính người bị chặn tự gỡ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-39.1.1` | A bị chặn trên khách hàng K; Đội ngũ phụ trách của K vừa thêm A | A mở K | Không mở được; người thêm A nhận thông báo lượt cấp không có hiệu lực, không kèm nội dung K |
| `AC-39.3.1` | A bị chặn trên K | A mở danh sách khách hàng | K không xuất hiện |
| `AC-39.3.3` | A bị chặn trên K | A tìm theo tên K | Không có kết quả là K |
| `AC-39.3.4` | A bị chặn trên K | A xuất danh sách khách hàng | Tệp xuất không có K |
| `AC-39.3.5` | A bị chặn trên K; K có 2 cơ hội đang mở | A xem bảng điều khiển số khách hàng và cơ hội | Số liệu không tính K và 2 cơ hội gắn với K mà A chỉ thấy được qua K |
| `AC-39.3.2` | B được cấp riêng Chỉ đọc trên cơ hội C ngoài phạm vi | B xem danh sách cơ hội | C xuất hiện trong danh sách |
| `AC-39.2.1` | Bản ghi không có lượt cấp hay chặn nào | Người có (Khách hàng, Xem) = Chỉ của mình mở danh sách | Bản ghi hiển thị đúng theo quy tắc chung |
| `AC-39.4.1` | A mở một cơ hội liên kết tới K | Xem mục khách hàng liên kết | Hiển thị "Bị hạn chế truy cập", không có tên K |
| `AC-39.5.1` | D có (Khách hàng, Sửa) = Không có; được cấp mức trần Chỉnh sửa trên K | D sửa K | Bị từ chối; D xem được K |
| `AC-39.6.1` | E được cấp Chỉ đọc trên K; nhóm của E che trường Định danh của Khách hàng | E mở K | Thấy K, trường Định danh vẫn bị che |
| `AC-39.6.2` | Chính sách Cho phép nới (Cơ hội, Sửa); F có (Cơ hội, Sửa) = Không có | F sửa cơ hội thoả điều kiện | Bị từ chối |
| `AC-39.7.1` | B có (Khách hàng, Xem) bao phủ K nhưng (Khách hàng, Sửa) không bao phủ K | Thêm lượt cấp cho C trên K | Chỉ chọn được mức trần Chỉ đọc |
| `AC-39.7.2` | Nhóm của D đang bị chặn trên K; D có quyền Quản lý quyền trên bản ghi | D mở danh sách lượt chặn của K | Thao tác gỡ lượt chặn của nhóm D bị vô hiệu kèm giải thích |
| `AC-39.6.3` | E có thu hồi riêng lẻ (Khách hàng, Xuất) và quyền tạm thời mang (Khách hàng, Xuất) = Đơn vị của mình | E xuất | Xuất được trong phạm vi Đơn vị của mình |

---

## J. CHE DỮ LIỆU NHẠY CẢM

### FEAT-40 — Khung quy tắc che dữ liệu nhạy cảm

**Mô tả nghiệp vụ:** Khung quy tắc chung để che bớt dữ liệu nhạy cảm khi hiển thị. Có hai nguồn trường nhạy cảm:

- **Trường nhạy cảm của hệ thống** — do phân hệ sở hữu loại dữ liệu khai báo trong SRS của mình, kèm mẫu che và quyền xem đầy đủ. Ví dụ Khách hàng và Công ty theo [`contacts-srs.md`](./contacts-srs.md) `FEAT-04`. Phân hệ chưa khai báo mẫu che riêng dùng mẫu che mặc định dưới đây.
- **Trường nhạy cảm do doanh nghiệp tự khai báo** trên trường tuỳ chỉnh — che theo phân quyền trường của [`object-manager-srs.md`](./object-manager-srs.md).

**Mẫu che mặc định:**

| Loại giá trị | Cách che |
| --- | --- |
| Email | Giữ ký tự đầu và tên miền |
| Số điện thoại | Giữ 4 số cuối |
| Giá trị tiền, tỷ lệ, mã số thuế | Ẩn hoàn toàn |
| Còn lại | Thay toàn bộ bằng ký hiệu che |

**Vai trò sử dụng chính:** Mọi người xem dữ liệu (bị ảnh hưởng); người có quyền xem đầy đủ theo phân hệ.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Khi hiển thị một bản ghi, hệ thống xác định các trường nhạy cảm và mức hiển thị của người xem theo mọi lớp áp dụng.
2. Áp mức hạn chế nhất.

**Quy tắc nghiệp vụ:**

- **`BR-40.1` (Tác nhân AI luôn bị che):** Tác nhân AI luôn nhận giá trị đã che của mọi trường nhạy cảm, cả của hệ thống lẫn do doanh nghiệp khai báo, không có quyền xem đầy đủ nào áp dụng được, bất kể người dùng mà nó phục vụ có quyền gì. Hằng số hệ thống.

  **Lý do nghiệp vụ:** đầu ra của AI có thể được sao chép, lưu hoặc gửi đi ngoài tầm kiểm soát của nhật ký truy cập; dữ liệu nhạy cảm đầy đủ không được đi qua kênh đó.

- **`BR-40.2` (Chỉ che khi hiển thị):** Che không thay đổi dữ liệu gốc.

  **Lý do nghiệp vụ:** người có quyền vẫn cần dữ liệu đầy đủ để làm việc; che là quyết định hiển thị, không phải xoá.

- **`BR-40.3` (Hạn chế thắng giữa các lớp):** Một trường chịu nhiều lớp che (của hệ thống, của phân hệ, của phân quyền trường) hiển thị theo lớp hạn chế nhất. Lượt cấp trên bản ghi không nới lỏng lớp che nào (`BR-39.6` bước 4).

  **Lý do nghiệp vụ:** mỗi lớp được đặt vì một lý do riêng; lớp thoáng hơn không được vô hiệu hoá lý do của lớp chặt hơn.

- **`BR-40.4` (Dữ liệu phân loại không che mặc định):** Doanh thu hằng năm và số nhân sự của Công ty không thuộc trường nhạy cảm của hệ thống; doanh nghiệp tự che nếu muốn bằng phân quyền trường.

  **Lý do nghiệp vụ:** đây là dữ liệu phân loại khách hàng, không phải định danh cá nhân; kinh doanh cần thấy để ưu tiên.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-40.1.1` | Người dùng có quyền xem đầy đủ số điện thoại khách hàng | Hỏi trợ lý AI số điện thoại của khách hàng K | AI trả số đã che theo mẫu của phân hệ |
| `AC-40.1.2` | Doanh nghiệp khai báo trường tuỳ chỉnh "Số căn cước" là nhạy cảm | AI truy vấn trường đó | Nhận giá trị đã che |
| `AC-40.3.1` | Phân hệ cho xem một phần, phân quyền trường của nhóm đặt Che hoàn toàn | Thành viên nhóm mở bản ghi | Trường bị che hoàn toàn |
| `AC-40.2.1` | Người không có quyền xem đầy đủ | Xuất bản ghi | Tệp xuất chứa giá trị đã che; dữ liệu gốc không đổi khi người có quyền mở |
| `AC-40.4.1` | Nhân viên kinh doanh không có quyền xem đầy đủ | Mở Công ty | Thấy doanh thu hằng năm và số nhân sự |

---

## K. NHẬT KÝ, RÀ SOÁT & BÁO CÁO QUYỀN

### FEAT-41 — Nhật ký thay đổi quyền & quyết định truy cập

**Mô tả nghiệp vụ:** Lưu và tra cứu toàn bộ lịch sử thay đổi liên quan tới quyền và các quyết định cho phép/từ chối truy cập.

**Vai trò sử dụng chính:** Người có quyền Xem nhật ký quyền; Hệ thống (ghi).

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Lọc theo loại thay đổi, người thực hiện (gồm nhãn Nhà cung cấp, Hệ thống — bản phát hành), đối tượng bị ảnh hưởng, mã lô, khoảng thời gian.
2. Xem chi tiết: ai, lúc nào, làm gì, giá trị trước/sau, lý do, người duyệt thứ hai nếu có.
3. Xuất kết quả lọc.

**Quy tắc nghiệp vụ:**

- **`BR-41.1` (Chỉ ghi):** Nhật ký không sửa, không xoá được bởi bất kỳ ai, kể cả Chủ sở hữu và Nhân sự vận hành nền tảng, trừ việc tự xoá khi hết thời hạn lưu.

  **Lý do nghiệp vụ:** nhật ký sửa được thì không chứng minh được gì.

- **`BR-41.2` (Nhật ký quyết định truy cập không chặn nghiệp vụ):** Ghi nhật ký quyết định cho phép/từ chối truy cập không bao giờ làm gián đoạn thao tác nghiệp vụ; nếu gặp sự cố, thao tác vẫn diễn ra, dòng nhật ký bị bỏ lỡ được đếm và cảnh báo theo `NFR-08`.

  **Lý do nghiệp vụ:** đây là nhật ký khối lượng lớn nhất, phát sinh ở mọi lượt đọc; chặn nghiệp vụ vì nó là đánh đổi tính sẵn sàng của toàn hệ thống lấy một dòng ghi chép thông thường.

- **`BR-41.3` (Khác nhật ký hoạt động):** Nhật ký quyền tách biệt với nhật ký hoạt động nghiệp vụ ("khách hàng X vừa được tạo"); cùng một hành động có thể xuất hiện ở cả hai.

  **Lý do nghiệp vụ:** hai nhật ký phục vụ hai mục đích và hai nhóm người đọc khác nhau (kiểm soát bảo mật vs theo dõi công việc).

- **`BR-41.4` (Ba nhóm nhật ký và nguyên tắc đóng):**
  - **Nhật ký quyết định truy cập** — lưu theo `CFG-41-01`; áp `BR-41.2`.
  - **Nhật ký truy cập của nhà cung cấp** — mọi thao tác của nhân sự vận hành nền tảng trong Phiên hỗ trợ, phiên khẩn cấp, Phiên triển khai và giai đoạn triển khai trước kích hoạt (`BR-08.3`); lưu theo `CFG-41-02`; đóng khi lỗi — ngoại lệ tường minh của `BR-41.2` — trừ thao tác thu hẹp quyền, vốn theo `BR-41.6` (ghi bù). Thao tác của nhà cung cấp ngoài phiên (bật tắt tính năng mở rộng, khoá tài khoản, huỷ lời mời Chờ gửi, thẩm định khôi phục quyền sở hữu) thuộc nhật ký thay đổi cấu hình quyền (`BR-05.3`).
  - **Nhật ký thay đổi cấu hình quyền** — lưu theo `CFG-41-02`; áp `BR-41.5`. **Nguyên tắc đóng:** mọi thao tác làm thay đổi **ai được làm gì** hoặc **ai thấy gì** trong workspace thuộc nhóm này, trừ thao tác chỉ xem trước, mô phỏng hoặc chỉ đọc. Danh sách sau là ví dụ, không phải danh sách đóng; tính năng phân quyền mới mặc định thuộc nhóm này: đổi cấp bậc, chuyển nhượng và khôi phục quyền sở hữu (`FEAT-06`, `FEAT-07`, `FEAT-12`); mời, chấp nhận, gia nhập qua liên kết, tạm ngưng, kích hoạt lại, gỡ (`FEAT-09`, `FEAT-13`, `FEAT-42`, `FEAT-43`, `FEAT-50`); vai trò, điều chỉnh ô vai trò dựng sẵn, Quyền Ủy thác, tách biệt nhiệm vụ, chức danh (`FEAT-25` – `FEAT-29`, `FEAT-45` – `FEAT-47`); nhóm, thành viên nhóm (Nhóm C); đơn vị, vai trò gợi ý, vị trí của thành viên (Nhóm D, `FEAT-11`); đơn vị tiếp nhận của hàng đợi (`BR-35.12`); mức nền (Nhóm G); chính sách (Nhóm H); quyền tạm thời (Nhóm F); quyền trên bản ghi (Nhóm I); trần quyền và phiên hỗ trợ (`FEAT-05`, `FEAT-08`); tham số tại Phụ lục B.

  **Lý do nghiệp vụ:** liệt kê tĩnh sẽ bỏ sót tính năng mới; nguyên tắc đóng làm mặc định là "có vết".

- **`BR-41.5` (Nhật ký cấu hình quyền đóng khi lỗi cho thao tác nới rộng):** Với thao tác nới rộng quyền hoặc trung tính (Mục 1.4) thuộc nhóm nhật ký thay đổi cấu hình quyền, nếu không ghi được nhật ký, thao tác bị huỷ và người dùng nhận thông báo lỗi. Xem ADR-0003.

  **Lý do nghiệp vụ:** mất dấu vết của một lượt nâng ai đó lên Quản trị viên là mất khả năng chứng minh tuân thủ cho đúng thao tác nhạy cảm nhất.

- **`BR-41.6` (Thao tác thu hẹp không bị chặn, nhật ký được ghi bù):** Với thao tác thu hẹp quyền (gỡ thành viên, tạm ngưng, hạ cấp bậc, thu hồi, hết hạn, dọn theo dây chuyền, gỡ khỏi nhóm không mang hạn chế, xoá nhóm không mang hạn chế, thu hẹp ô — gỡ hay xoá nhóm mang hạn chế là nới rộng, theo `BR-41.5`), sự cố nhật ký không chặn thao tác. Hệ thống giữ sự kiện để ghi bù ngay khi nhật ký phục hồi, đánh dấu "ghi bù" kèm thời điểm thao tác thật, và cảnh báo Người có toàn quyền nếu việc ghi bù chưa xong sau 15 phút. Xem ADR-0010.

  **Lý do nghiệp vụ:** chặn thu hồi vì nhật ký lỗi là giữ nguyên quyền truy cập đúng lúc doanh nghiệp cần cắt khẩn cấp — rủi ro lớn hơn rủi ro mà nhật ký bảo vệ. Ghi bù giữ được đầy đủ vết.

- **`BR-41.7` (Nhật ký không thành đường xem dữ liệu):** Người xem bất kỳ nhóm nhật ký nào (gồm cả lượt cấp, chặn trên bản ghi trong nhật ký thay đổi cấu hình quyền) chỉ thấy tên hay nội dung nhận diện của bản ghi khi chính họ có mức Xem bao phủ bản ghi đó; trường hợp khác chỉ thấy mã bản ghi và loại dữ liệu.

  **Lý do nghiệp vụ:** vai trò Kiểm toán quyền cần bằng chứng ai đã truy cập gì, không cần biết khách hàng đó là ai; nhật ký không được là đường vòng để xem dữ liệu ngoài phạm vi.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-41.1.1` | Chủ sở hữu mở một bản ghi nhật ký | Tìm thao tác sửa hoặc xoá | Không có |
| `AC-41.4.1` | Quản trị viên nâng A lên Quản trị viên | Lọc nhật ký theo A | Có bản ghi: người thực hiện, thời điểm, cấp bậc trước/sau |
| `AC-41.5.1` | Nhật ký cấu hình quyền gặp sự cố | Quản trị viên thêm vai trò Quản lý cho A | Báo lỗi; A không có vai trò Quản lý |
| `AC-41.6.3` | Nhật ký cấu hình quyền gặp sự cố | Quản trị viên xoá một vai trò | Xoá thành công; khi nhật ký phục hồi có bản ghi gắn nhãn "ghi bù" |
| `AC-41.5.2` | Nhật ký cấu hình quyền gặp sự cố | Quản trị viên đổi `CFG-09-01` từ 7 lên 14 ngày | Báo lỗi; tham số giữ 7 ngày |
| `AC-41.7.1` | Kiểm toán viên có vai trò Kiểm toán quyền | Lọc nhật ký quyết định truy cập | Mỗi dòng hiện người truy cập, thời điểm, loại dữ liệu và mã bản ghi; không có tên khách hàng |
| `AC-41.6.1` | Nhật ký cấu hình quyền gặp sự cố | Gỡ thành viên B | Gỡ thành công; khi nhật ký phục hồi có bản ghi gắn nhãn "ghi bù" với thời điểm gỡ thật |
| `AC-41.6.2` | Gỡ thành viên trong lúc nhật ký gặp sự cố; 15 phút sau nhật ký vẫn chưa phục hồi | Chủ sở hữu mở thông báo | Có cảnh báo "Có thao tác thu hồi chưa được ghi nhật ký" kèm danh sách thao tác |
| `AC-41.2.1` | Nhật ký quyết định truy cập gặp sự cố | Thành viên mở danh sách khách hàng | Danh sách hiển thị bình thường |
| `AC-41.3.1` | Quản trị viên mời một thành viên mới | Mở nhật ký quyền và dòng thời gian hoạt động | Thao tác có ở nhật ký quyền; nhật ký hoạt động hiển thị độc lập theo quy tắc của nó |
| `AC-41.4.2` | `CFG-41-01` = 60 ngày | Lọc nhật ký quyết định truy cập 61 ngày trước | Không còn bản ghi; lượt truy cập của nhà cung cấp cùng ngày vẫn còn trong nhật ký truy cập của nhà cung cấp |

---

### FEAT-48 — Rà soát quyền định kỳ

**Mô tả nghiệp vụ:** Định kỳ yêu cầu người quản lý xác nhận từng người dưới quyền còn cần quyền đang có hay không, và lưu kết quả làm bằng chứng cho kiểm toán.

**Vai trò sử dụng chính:** Người có quyền Quản lý rà soát quyền (khởi tạo, theo dõi); người được giao rà soát (quyết định); Hệ thống (tạo đợt theo chu kỳ, nhắc).

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Đợt rà soát được tạo theo chu kỳ `CFG-48-01`, hoặc khởi tạo thủ công với phạm vi chọn (toàn workspace, một số đơn vị, chỉ quyền nhạy cảm).
2. Hệ thống lập các mục rà soát, mỗi mục là một người kèm cấp bậc, vai trò, nhóm, Quyền Ủy thác, chức danh, nguồn nới phạm vi ngoài vai trò, lượt cấp trên bản ghi đặt tay, và vi phạm tách biệt nhiệm vụ.
3. Mỗi mục giao cho quản lý trực tiếp; không có thì cho người phụ trách Đơn vị chính; không có nữa thì cho người khởi tạo đợt. Không ai rà soát mục của chính mình.
4. Người rà soát chọn **Giữ** hoặc **Thu hồi** (toàn bộ hay từng phần), kèm ghi chú.
5. Thu hồi được thực hiện ngay khi người rà soát xác nhận, theo quy tắc của thao tác tương ứng.
6. Hết hạn `CFG-48-02`: các mục chưa quyết định được xử lý theo `CFG-48-03`.
7. Đóng đợt → biên bản rà soát không sửa được, xuất được.

**Luồng ngoại lệ:**

- Người rà soát bị tạm ngưng hoặc rời workspace → mục chuyển cho người kế tiếp theo thứ tự bước 3.
- Mục là Người có toàn quyền → giao cho Chủ sở hữu. Mục của Chủ sở hữu giao cho người mang chức danh Người phụ trách bảo mật nếu có; không có thì biên bản ghi "ngoại lệ — Chủ sở hữu" kèm lý do (Chủ sở hữu chỉ thay qua `FEAT-06`, `FEAT-07`).

**Quy tắc nghiệp vụ:**

- **`BR-48.1` (Không tự rà soát):** Không ai quyết định mục của chính mình.

  **Lý do nghiệp vụ:** rà soát là kiểm soát của người thứ hai; tự xác nhận không có giá trị bằng chứng.

- **`BR-48.2` (Thu hồi trong rà soát không vượt quyền người rà soát):** Người rà soát thu hồi trực tiếp được vai trò, nhóm không mang hạn chế, Đơn vị kiêm nhiệm và lượt cấp trên bản ghi đặt tay thuộc mục mình được giao, kể cả khi thường ngày họ không có quyền Sửa người dùng. Với cấp bậc Quản trị viên, Quyền Ủy thác, chức danh trách nhiệm và nhóm mang hạn chế (`BR-18.3`), người rà soát chỉ **đề xuất thu hồi**; Người có toàn quyền quyết định. Không ai cấp thêm được gì qua rà soát.

  **Lý do nghiệp vụ:** quản lý trực tiếp biết rõ nhất ai cần gì, nhưng rà soát chỉ là công cụ thu hẹp; gỡ một nhóm mang hạn chế lại là nới rộng, và các quyền đặc biệt chỉ Người có toàn quyền cấp thì chỉ họ thu hồi.

- **`BR-48.3` (Quá hạn xử lý theo cấu hình):** Mục quá hạn được xử lý theo `CFG-48-03`: chỉ nhắc và báo Chủ sở hữu (mặc định), hoặc tự thu hồi vai trò và nhóm không mang hạn chế, không phải vai trò gợi ý của Đơn vị chính. Tự thu hồi không bao giờ làm thành viên mất vai trò cuối cùng, không gỡ nhóm mang hạn chế, và không chạm cấp bậc, Quyền Ủy thác, chức danh; những mục đó chuyển cho Chủ sở hữu quyết định.

  **Lý do nghiệp vụ:** doanh nghiệp chịu kiểm toán chặt cần "không xác nhận là mất"; doanh nghiệp nhỏ không muốn công việc dừng vì một quản lý quên bấm; và tự động không được vô tình nới quyền hay làm ai mất hết vai trò.

- **`BR-48.4` (Biên bản là bằng chứng):** Biên bản ghi mọi mục, quyết định, người quyết định, thời điểm, ghi chú và cách xử lý mục quá hạn; không sửa được.

  **Lý do nghiệp vụ:** kiểm toán viên cần bằng chứng rà soát định kỳ, không phải lời khẳng định.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-48.1.1` | A là quản lý trực tiếp của chính A theo dữ liệu lỗi | Đợt rà soát tạo mục của A | Mục của A giao cho người kế tiếp theo thứ tự, không giao cho A |
| `AC-48.2.1` | B là quản lý trực tiếp, không có quyền Sửa người dùng | B chọn Thu hồi vai trò Quản lý của C | Vai trò bị thu hồi ngay; nhật ký ghi nguồn "Rà soát quyền đợt Q3" |
| `AC-48.3.1` | `CFG-48-03` = Chỉ nhắc; 3 mục quá hạn | Hết hạn | Quyền không đổi; Chủ sở hữu nhận danh sách 3 mục |
| `AC-48.3.2` | `CFG-48-03` = Tự thu hồi; D có vai trò gợi ý của đơn vị và thêm vai trò Kiểm toán, mục quá hạn | Hết hạn | D mất vai trò Kiểm toán, giữ vai trò gợi ý; D và quản lý nhận thông báo |
| `AC-48.4.1` | Đợt đã đóng | Xuất biên bản | Tệp có đủ mục, quyết định, người quyết định, thời điểm; không có thao tác sửa trên biên bản |
| `AC-48.2.2` | Mục của E có Quyền Ủy thác; người rà soát là quản lý trực tiếp của E | Chọn Thu hồi Quyền Ủy thác | Ghi nhận là đề xuất; Quyền Ủy thác giữ nguyên cho tới khi Người có toàn quyền quyết định |
| `AC-48.3.3` | `CFG-48-03` = Tự thu hồi; G chỉ có một vai trò, đơn vị không có vai trò gợi ý; mục quá hạn | Hết hạn | G giữ vai trò đó; mục chuyển cho Chủ sở hữu quyết định |
| `AC-48.3.4` | `CFG-48-03` = Tự thu hồi; H thuộc nhóm "Hạn chế dữ liệu VIP" mang lượt chặn; mục quá hạn | Hết hạn | H vẫn trong nhóm; mục chuyển cho Chủ sở hữu quyết định |

---

### FEAT-49 — Báo cáo quyền phục vụ kiểm toán

**Mô tả nghiệp vụ:** Bộ báo cáo xuất được trả lời các câu hỏi kiểm toán thường gặp về quyền truy cập.

**Vai trò sử dụng chính:** Người có quyền Xem báo cáo quyền.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Chọn báo cáo và thời điểm hoặc khoảng thời gian:
   - **Ma trận người dùng × quyền:** mỗi thành viên, cấp bậc, vai trò, mức hiệu lực theo từng loại dữ liệu, nguồn nới phạm vi ngoài vai trò.
   - **Ai truy cập được loại dữ liệu X ở mức Y:** danh sách người kèm nguồn.
   - **Người có quyền đặc biệt:** Người có toàn quyền, người giữ Quyền Ủy thác, chức danh trách nhiệm, người phụ trách đơn vị có quyền xem toàn nhánh.
   - **Thay đổi quyền trong kỳ:** tổng hợp từ `FEAT-41`.
   - **Truy cập của nhà cung cấp:** mọi phiên hỗ trợ, phiên khẩn cấp, Phiên triển khai và giai đoạn triển khai trước kích hoạt trong kỳ, theo từng nhân sự, kèm tóm tắt các thay đổi phân quyền họ đã soạn hoặc đã làm.
   - **Vi phạm tách biệt nhiệm vụ** đang tồn tại và lý do được chấp nhận.
   Bằng chứng ai đã xem dữ liệu nào trong thời gian dài nằm ở nhật ký đọc của từng phân hệ (`BR-35.9`) và nhật ký truy cập của nhà cung cấp; nhật ký quyết định truy cập là công cụ điều tra ngắn hạn (`CFG-41-01`).
2. Xem trên màn hình hoặc xuất; có thể lên lịch gửi định kỳ cho người nhận là thành viên Đang hoạt động có quyền Xem báo cáo quyền.

**Quy tắc nghiệp vụ:**

- **`BR-49.1` (Báo cáo tại một thời điểm):** Báo cáo ma trận và báo cáo người có quyền đặc biệt dựng được cho thời điểm hiện tại và cho một thời điểm trong quá khứ nằm trong thời hạn lưu `CFG-41-02`.

  **Lý do nghiệp vụ:** kiểm toán hỏi "ngày 31/12 ai có quyền gì", không hỏi "hôm nay".

- **`BR-49.2` (Tệp xuất có dấu kiểm tra toàn vẹn):** Mỗi tệp xuất mang thời điểm tạo, người tạo và một giá trị kiểm tra toàn vẹn để chứng minh không bị sửa sau khi xuất; mỗi lượt xuất ghi nhật ký.

  **Lý do nghiệp vụ:** báo cáo nộp cho kiểm toán phải chứng minh được là bản gốc.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-49.1.1` | Ngày 31/12 A là Quản trị viên, nay là Thành viên | Dựng báo cáo người có quyền đặc biệt tại 31/12 | A có trong danh sách Người có toàn quyền |
| `AC-49.2.1` | Xuất báo cáo ma trận | Mở tệp | Có thời điểm tạo, người tạo, giá trị kiểm tra toàn vẹn; nhật ký có bản ghi lượt xuất |
| `AC-49.1.2` | Trong kỳ có 2 phiên hỗ trợ, 1 phiên khẩn cấp, 1 Phiên triển khai | Mở báo cáo truy cập của nhà cung cấp | Thấy đủ 4 phiên theo từng nhân sự; phiên khẩn cấp có số hồ sơ sự cố; Phiên triển khai kèm danh sách thay đổi đã soạn và người xác nhận |
| `AC-49.1.3` | Lên lịch gửi hằng tháng cho người không có quyền Xem báo cáo quyền | Lưu lịch | Từ chối, người đó không chọn được |

---

## 4. Yêu cầu phi chức năng

### 4.1 An toàn & Toàn vẹn quyền hạn

- **`NFR-01` (Đóng khi lỗi):** Mọi lỗi không lường trước khi tính quyền hoặc phạm vi dẫn tới từ chối, không bao giờ cho phép (`BR-35.6`). Đo: 100% ca kiểm thử chèn lỗi vào bước tính phạm vi trả về từ chối.
- **`NFR-02` (Cơ chế lưu tạm không làm sai quyết định):** Sự cố ở cơ chế lưu tạm phục vụ tốc độ chỉ được làm chậm, không được làm sai quyết định phân quyền. Đo: khi cơ chế lưu tạm không khả dụng, 100% quyết định trùng với quyết định tính từ dữ liệu gốc.
- **`NFR-03` (Cách ly giữa các workspace):** Không thao tác nào của người dùng, tiến trình hay nhân sự vận hành nền tảng trả về dữ liệu của workspace khác workspace của thao tác đó (`BR-51.1` – `BR-51.4`). Đo: kiểm thử xâm nhập định kỳ mỗi bản phát hành lớn với tài khoản thuộc nhiều workspace, kết quả 0 trường hợp rò rỉ.

### 4.2 Hiệu lực & Hiệu năng

- **`NFR-04` (Thời gian có hiệu lực của thay đổi quyền):**
  - Các sự kiện chấm dứt phiên (`BR-11.3`): phiên chấm dứt và mọi thao tác kế tiếp bị từ chối trong vòng **5 giây** kể từ khi thao tác được lưu.
  - Thay đổi vai trò, nhóm, quyền tạm thời, quyền trên bản ghi, chính sách, mức nền, trần quyền: có hiệu lực với thao tác kế tiếp trong vòng **5 giây**.
  - Thay đổi cây tổ chức, vị trí của thành viên và quản lý trực tiếp: có hiệu lực trong vòng **60 giây**.
- **`NFR-05` (Thời gian kiểm tra quyền):** Việc kiểm tra quyền và tính phạm vi không làm thời gian mở một danh sách tăng quá 150 ms ở phân vị 95, với workspace ở quy mô `NFR-09`.
- **`NFR-06` (Xem quyền hiệu lực):** Màn hình quyền hiệu lực và tra cứu ngược một bản ghi (`FEAT-15`) trả kết quả trong 2 giây ở phân vị 95.
- **`NFR-07` (Tra cứu nhật ký):** Lọc nhật ký trong khoảng 30 ngày trả kết quả trang đầu trong 3 giây ở phân vị 95.

### 4.3 Độ tin cậy của nhật ký

- **`NFR-08` (Giám sát nhật ký quyết định truy cập):** Tỷ lệ dòng nhật ký quyết định truy cập bị bỏ lỡ (`BR-41.2`) được đếm liên tục; vượt 0,1% trong 1 giờ thì Người có toàn quyền và đội vận hành nền tảng nhận cảnh báo. Việc ghi nhật ký quyết định truy cập không tắt được bằng cấu hình.

### 4.4 Quy mô

- **`NFR-09` (Quy mô tối thiểu được hỗ trợ cho mỗi workspace):** 10.000 thành viên; 1.000 vai trò; 2.000 đơn vị tổ chức; 2.000 nhóm; 500 chính sách truy cập đang bật; 1.000.000 lượt cấp/chặn trên bản ghi. Một lượt mời dán danh sách tối đa 100 email; một lô thao tác hàng loạt tối đa 1.000 dòng.

### 4.5 Khả dụng & Đa ngôn ngữ

- **`NFR-10` (Khả dụng):** Các chức năng kiểm tra quyền khả dụng 99,9% theo tháng; khi kiểm tra quyền không khả dụng, mọi thao tác dữ liệu bị từ chối theo `NFR-01`.
- **`NFR-11` (Đa ngôn ngữ):** Mọi nhãn, thông báo, email mời, thông báo lỗi của phân hệ có đủ các ngôn ngữ mà nền tảng hỗ trợ, gồm ngôn ngữ viết từ phải sang trái.

---

## 5. Ma trận quyền truy cập tính năng

Các cột là vai trò thao tác tại Mục 2.2. Ký hiệu: **✅** = thực hiện được; **Q:** = thực hiện được khi có quyền quản trị được nêu; **—** = không; *(tự)* = chỉ trên chính mình.

| Tính năng | Nhân sự vận hành nền tảng | Chủ sở hữu | Quản trị viên | Thành viên được cấp quyền quản trị | Người phê duyệt | Người quản lý / phụ trách đơn vị | Thành viên | Hệ thống |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `FEAT-01` Xác lập Chủ sở hữu | ✅ (kênh tạo hộ) | — | — | — | — | — | — | ✅ |
| `FEAT-02` Bất biến khi sẵn sàng | — | — | — | — | — | — | — | ✅ |
| `FEAT-03` Sức khỏe cấu hình | — | ✅ | ✅ | Q: Quản lý cấu hình workspace | — | — | — | — |
| `FEAT-04` Cấu hình chung | — | ✅ | ✅ | Q: Quản lý cấu hình workspace | — | — | — | — |
| `FEAT-05` Trần quyền | ✅ (bật/tắt tính năng mở rộng) | Xem | Xem | — | — | — | — | ✅ (áp theo gói) |
| `FEAT-06` Chuyển nhượng | — | ✅ (khởi tạo) | Xác nhận khi được chọn | Xác nhận khi được chọn | — | — | Xác nhận khi được chọn | ✅ (hết hạn, tự huỷ) |
| `FEAT-07` Khôi phục quyền sở hữu | ✅ (thẩm định) | Phản đối | ✅ (gửi đề nghị, phản đối) | Phản đối khi mang chức danh Người phụ trách bảo mật | — | — | — | ✅ |
| `FEAT-08` Phiên hỗ trợ | ✅ (đề nghị, dùng; phiên triển khai) | ✅ (cấp, chấm dứt; duyệt phiên triển khai) | ✅ (cấp, chấm dứt; duyệt phiên triển khai kèm người duyệt thứ hai; xác nhận thay đổi soạn sẵn) | — | — | — | — | ✅ |
| `FEAT-09` Mời & lời mời | — | ✅ | ✅ | Q: Mời người dùng | — | — | Chấp nhận *(tự)* | ✅ (hết hạn) |
| `FEAT-10` Tra cứu thành viên | — | ✅ | ✅ | Q: Xem người dùng | — | — | Danh bạ tối thiểu | — |
| `FEAT-11` Cập nhật thành viên | ✅ (trường tài khoản toàn hệ thống) | ✅ | ✅ | Q: Sửa người dùng | — | — | — | ✅ (hết kiêm nhiệm) |
| `FEAT-12` Đổi cấp bậc | — | ✅ | ✅ | — | — | — | — | — |
| `FEAT-13` Gỡ / xoá tài khoản | ✅ (xoá tài khoản, `BR-13.4`) | ✅ | ✅ | Q: Gỡ người dùng (không áp lên Người có toàn quyền) | — | — | Gửi yêu cầu rời *(tự)* | — |
| `FEAT-14` Đặt lại mật khẩu / khoá | ✅ (khoá) | ✅ | ✅ | Q: Sửa người dùng | — | — | — | — |
| `FEAT-15` Quyền hiệu lực | — | ✅ | ✅ | Q: Xem người dùng | — | — | ✅ *(tự)* | — |
| `FEAT-16` Tuỳ chỉnh cá nhân | — | ✅ *(tự)* | ✅ *(tự)* | ✅ *(tự)* | ✅ *(tự)* | ✅ *(tự)* | ✅ *(tự)* | — |
| `FEAT-17` Tạo nhóm | — | ✅ | ✅ | Q: Quản lý nhóm | — | — | — | — |
| `FEAT-18` Thành viên nhóm | — | ✅ | ✅ | Q: Quản lý thành viên nhóm | — | — | — | — |
| `FEAT-19` Xem trước quyền nhóm | — | ✅ | ✅ | Q: Quản lý nhóm | — | — | — | — |
| `FEAT-20` Xoá nhóm | — | ✅ | ✅ | Q: Quản lý nhóm | — | — | — | — |
| `FEAT-21` Cây tổ chức | — | ✅ | ✅ | Q: Xem sơ đồ tổ chức / Quản lý đơn vị tổ chức | — | — | — | — |
| `FEAT-22` Di chuyển đơn vị | — | ✅ | ✅ | — (`BR-21.6`) | — | — | — | — |
| `FEAT-23` Xoá đơn vị | — | ✅ | ✅ | Q: Quản lý đơn vị tổ chức | — | — | — | — |
| `FEAT-24` Danh mục quyền | — | ✅ | ✅ | Q: Quản lý vai trò | — | — | — | — |
| `FEAT-25` – `FEAT-28` Vai trò | — | ✅ | ✅ | Q: Quản lý vai trò | — | — | — | — |
| `FEAT-29` Vai trò dựng sẵn | — | ✅ | ✅ (gồm duyệt thay đổi từ bản phát hành) | Q: Quản lý vai trò (điều chỉnh ô) | — | — | — | ✅ (đồng bộ) |
| `FEAT-30` Yêu cầu quyền tạm thời | — | ✅ | ✅ | Q: Yêu cầu quyền tạm thời | — | — | — | — |
| `FEAT-31` Phê duyệt | — | ✅ | ✅ | — | Q: Phê duyệt quyền tạm thời | — | — | — |
| `FEAT-32` Thu hồi quyền tạm thời | — | ✅ | ✅ | Q: Thu hồi quyền tạm thời | ✅ (yêu cầu mình đã duyệt) | — | — | ✅ (hết hạn, dây chuyền) |
| `FEAT-33` Báo cáo kiểm định | — | ✅ | ✅ | Q: Xem báo cáo quyền | — | — | — | — |
| `FEAT-34` Mức nền | — | ✅ | ✅ | Q: Quản lý cấu hình workspace (chỉ thu hẹp) | — | — | — | — |
| `FEAT-35` Phân giải phạm vi | — | — | — | — | — | — | — | ✅ |
| `FEAT-36` – `FEAT-38` Chính sách | — | ✅ | ✅ | Q: Quản lý chính sách truy cập | — | — | — | — |
| `FEAT-39` Quyền trên bản ghi | — | ✅ | ✅ | Q: Quản lý quyền trên bản ghi | — | — | — | ✅ (lượt cấp của phân hệ) |
| `FEAT-40` Che dữ liệu | — | Chịu che theo sàn | Chịu che theo sàn | Chịu che | Chịu che | Chịu che | Chịu che | ✅ (áp che) |
| `FEAT-41` Nhật ký | — | ✅ | ✅ | Q: Xem nhật ký quyền | — | — | — | ✅ (ghi) |
| `FEAT-42` Tạm ngưng | — | ✅ | ✅ | Q: Tạm ngưng người dùng (tạm ngưng và kích hoạt lại, không áp lên Người có toàn quyền) | — | — | — | ✅ (không hoạt động) |
| `FEAT-43` Rời workspace | — | ✅ | ✅ | Q: Gỡ người dùng | — | ✅ (thực hiện khi có quyền Gỡ người dùng; nhận bàn giao) | Gửi yêu cầu rời *(tự)* | — |
| `FEAT-44` Hàng loạt | — | ✅ | ✅ | Q: quyền của thao tác đơn lẻ tương ứng | — | — | — | — |
| `FEAT-45` Quyền Ủy thác | — | ✅ | ✅ | Sử dụng khi được cấp | — | — | — | — |
| `FEAT-46` Tách biệt nhiệm vụ | — | ✅ | ✅ | Q: Quản lý vai trò | — | — | — | — |
| `FEAT-47` Chức danh | — | ✅ | ✅ | — | — | — | — | — |
| `FEAT-48` Rà soát quyền | — | ✅ | ✅ | Q: Quản lý rà soát quyền | ✅ (mục được giao, `BR-48.2`) | ✅ (mục được giao, `BR-48.2`) | — | ✅ (tạo đợt, nhắc) |
| `FEAT-49` Báo cáo kiểm toán | — | ✅ | ✅ | Q: Xem báo cáo quyền | — | — | — | — |
| `FEAT-50` Liên kết mời / tự gia nhập | — | ✅ | ✅ | Q: Mời người dùng (liên kết) | — | — | Gia nhập *(tự)* | — |
| `FEAT-51` Nhiều workspace | Theo phiên hỗ trợ | ✅ *(tự)* | ✅ *(tự)* | ✅ *(tự)* | ✅ *(tự)* | ✅ *(tự)* | ✅ *(tự)* | ✅ (cách ly) |

**Ghi chú:** Mọi ô "Q:" vẫn chịu trần năng lực của người thực hiện (Nguyên tắc 1) và không tự nới rộng quyền của mình (Nguyên tắc 2). Các quyền quản trị là giá trị của vai trò, không gắn với tên vai trò nào; vai trò dựng sẵn chỉ mang giá trị mặc định (`FEAT-29`).

---

## 6. Kịch bản chấp nhận tổng hợp

### Kịch bản 1: Doanh nghiệp 25 người thiết lập đội ngũ trong 15 phút

1. Chủ sở hữu vừa tạo workspace mở khu quản trị phân quyền → thấy chế độ Cơ bản (`BR-24.3`).
2. Tạo đơn vị "Kinh doanh" với vai trò gợi ý Nhân viên Kinh doanh, "Hỗ trợ" với vai trò gợi ý Nhân viên Hỗ trợ (`BR-21.4`).
3. Dán 15 email, chọn Đơn vị chính "Kinh doanh" → vai trò Nhân viên Kinh doanh được chọn sẵn → gửi (`FEAT-09`, `BR-09.6`).
4. Dán 8 email cho "Hỗ trợ" → gửi.
5. **Kỳ vọng:** 23 lời mời Đang chờ chấp nhận; người được mời chấp nhận xong thấy đúng dữ liệu của mình theo vai trò; `FEAT-03` không có cảnh báo Nghiêm trọng về đơn vị và vai trò.

### Kịch bản 2: Thu hồi luôn đi nhanh hơn cấp

1. Quản trị viên lần lượt: tạm ngưng A; hạ B từ Quản trị viên xuống Thành viên; xoá vai trò mà C đang giữ; thu hồi thủ công quyền tạm thời của D.
2. **Kỳ vọng:** cả bốn người mất phiên trong workspace trong vòng 5 giây (`BR-11.3`, `NFR-04`); nếu cùng lúc nhật ký gặp sự cố, cả bốn thao tác vẫn thực hiện và được ghi bù (`BR-41.6`).

### Kịch bản 3: Không ai tự leo thang quyền

1. Thành viên có quyền Sửa người dùng thử tự thêm vai trò cho mình → từ chối (`BR-11.2`).
2. Người có quyền Quản lý vai trò thử sao chép một vai trò mạnh hơn mình → từ chối (`BR-25.5`).
3. Người có quyền Quản lý vai trò thử khôi phục phiên bản cũ của vai trò mạnh hơn mình → từ chối (`BR-27.2`).
4. Người giữ Quyền Ủy thác thử gán vai trò ngoài danh sách được giao → từ chối (`BR-45.3`).

### Kịch bản 4: Quyền Ủy thác cho bộ phận Nhân sự

1. Quản trị viên cấp cho A (phòng Nhân sự) Quyền Ủy thác với danh sách {Nhân viên Kinh doanh, Nhân viên Hỗ trợ} → mọi Người có toàn quyền nhận thông báo (`BR-45.1`).
2. A mời người mới với vai trò Nhân viên Kinh doanh → thành công dù A không có quyền trên Cơ hội (`BR-45.2`).
3. A thử nâng người mới lên Quản trị viên → không có thao tác (`BR-45.3`, `BR-12.1`).

### Kịch bản 5: Nhân viên nghỉ việc

1. Quản lý mở Rời workspace cho E, chọn tạm ngưng ngay → E mất phiên (`FEAT-43`, `BR-11.3`).
2. Bàn giao 120 khách hàng cho F, chỉ định quản lý mới cho 3 cấp dưới của E, chuyển chiến dịch E đang chạy cho F.
3. Gỡ E → quyền tạm thời và lượt cấp trên bản ghi của E bị thu hồi (`BR-13.1`).
4. **Kỳ vọng:** biên bản rời workspace có đủ các bước (`BR-43.3`); không còn bản ghi hay cấp dưới nào trỏ tới E.

### Kịch bản 6: Quyền tạm thời trong doanh nghiệp nhỏ

1. Workspace có Chủ sở hữu và 1 Quản trị viên; `CFG-31-01` = 1.
2. Quản trị viên gửi yêu cầu quyền tạm thời vai trò Quản lý cho G trong 7 ngày, kèm lý do.
3. Chủ sở hữu duyệt → quyền có hiệu lực (`BR-31.1`).
4. **Kỳ vọng:** 3 ngày trước khi hết hạn G nhận nhắc (`CFG-30-03`); hết hạn lúc 23:59:59 của ngày thứ 7 tính từ ngày hôm sau ngày cấp, theo múi giờ workspace (Mục 2.3); G không bị đăng xuất nhưng thao tác cần quyền đó bị từ chối (`BR-32.1`).

### Kịch bản 7: Xem rộng, sửa hẹp

1. Vai trò tự tạo "Chăm sóc khách hàng": Khách hàng × Xem = Toàn workspace, Khách hàng × Sửa = Chỉ của mình.
2. Người giữ vai trò mở hồ sơ mọi khách hàng → được; sửa khách hàng của người khác → bị từ chối (`BR-35.7`); mỗi lượt xem khách hàng của người khác được ghi là đọc ngoài phạm vi phụ trách (`BR-35.9`).
3. Thử lưu vai trò với Khách hàng × Sửa rộng hơn Khách hàng × Xem → bị từ chối, nêu ô vi phạm (`BR-25.1`).

### Kịch bản 8: Thứ tự hợp nhất quyền

1. H có (Khách hàng, Sửa) = Không có; Đội ngũ phụ trách của khách hàng K cấp cho H mức trần Chỉnh sửa → H xem được K, không sửa được (`BR-39.5`, `BR-39.6` bước 1).
2. Quản trị viên chặn H trên K → K biến mất khỏi danh sách, tìm kiếm, xuất, bảng điều khiển của H; người đã thêm H vào Đội ngũ phụ trách nhận thông báo lượt cấp không có hiệu lực (`BR-39.1`, `BR-39.3`).
3. Ở một cơ hội liên kết tới K, H thấy "Bị hạn chế truy cập" (`BR-39.4`).

### Kịch bản 9: Nhà cung cấp hỗ trợ có kiểm soát

1. Nhân sự vận hành nền tảng gửi đề nghị phiên hỗ trợ 2 giờ, chỉ xem, kèm mã yêu cầu hỗ trợ.
2. Quản trị viên duyệt; trong phiên nhân sự vận hành mở 3 khách hàng, thử sửa 1 → bị từ chối.
3. **Kỳ vọng:** hết 2 giờ mất truy cập; Chủ sở hữu nhận tóm tắt; nhật ký truy cập của nhà cung cấp có 3 lượt xem, kèm tên nhân sự; thao tác sửa không có trong phiên Chỉ xem (`BR-08.1` – `BR-08.3`); báo cáo `FEAT-49` liệt kê phiên.

### Kịch bản 10: Một người, hai workspace

1. I là Quản trị viên ở W1, Thành viên với vai trò Chỉ xem bản ghi được giao ở W2.
2. I đăng nhập ở trang chung, chọn W2, thử sửa một khách hàng → bị từ chối (`BR-51.1`).
3. W1 gỡ I → chỉ phiên ở W1 chấm dứt; I tiếp tục làm việc ở W2 (`BR-51.2`).

### Kịch bản 11: Chuyển nhượng và khôi phục quyền sở hữu

1. Chủ sở hữu A tạo yêu cầu chuyển cho B, chọn ở lại làm Quản trị viên; B bị tạm ngưng trước khi xác nhận → yêu cầu tự huỷ (`FEAT-06`).
2. Ở workspace khác, Chủ sở hữu rời công ty không bàn giao; Quản trị viên gửi đề nghị khôi phục kèm văn bản; hết 7 ngày không có phản đối → người được đề xuất thành Chủ sở hữu, Chủ sở hữu cũ là Thành viên Tạm ngưng (`FEAT-07`).

### Kịch bản 12: Rà soát quyền quý

1. `CFG-48-01` = Quý; đợt rà soát tạo mục cho mọi thành viên, giao cho quản lý trực tiếp.
2. Quản lý J thu hồi vai trò Kiểm toán của K → có hiệu lực ngay (`BR-48.2`).
3. Hết hạn, 2 mục chưa quyết định, `CFG-48-03` = Chỉ nhắc → Chủ sở hữu nhận danh sách (`BR-48.3`).
4. **Kỳ vọng:** biên bản xuất được, có đủ quyết định và người quyết định (`BR-48.4`).

### Kịch bản 13: Bản phát hành đổi vai trò dựng sẵn

1. Doanh nghiệp đã điều chỉnh ô Khách hàng × Xem của Marketing về Đơn vị của mình.
2. Bản phát hành nới rộng một ô chưa điều chỉnh của Marketing và thu hẹp một ô khác.
3. **Kỳ vọng:** ô đã điều chỉnh giữ nguyên; ô thu hẹp áp ngay; ô nới rộng chờ duyệt theo `CFG-29-01`; nhật ký ghi người thực hiện "Hệ thống — bản phát hành" (`BR-29.1`, `BR-29.4`).

### Kịch bản 14: Che dữ liệu với tác nhân AI

1. Người dùng có quyền xem đầy đủ số điện thoại khách hàng hỏi trợ lý AI thông tin liên hệ của khách hàng K.
2. **Kỳ vọng:** AI chỉ nhận số đã che, cả với trường tuỳ chỉnh do doanh nghiệp khai báo là nhạy cảm (`BR-40.1`).

---

## 7. Nhu cầu nghiệp vụ chưa chốt được phương án

Mục này chỉ liệt kê nhu cầu có thật mà chưa chốt được hướng xử lý đúng. Mọi điểm đã có quyết định nằm tại quy tắc tương ứng ở Mục 3 và Phụ lục C.

1. **Tài khoản kỹ thuật cho tích hợp.** Doanh nghiệp cần tài khoản không phải con người để kết nối hệ thống khác ([`billing-subscription-srs.md`](./billing-subscription-srs.md) có nhắc tới). Chưa chốt: tài khoản này có cấp bậc và vai trò như thành viên không, có tính vào hạn mức người dùng không, ai chịu trách nhiệm, và rà soát quyền định kỳ áp ra sao.
2. **Ủy quyền phê duyệt khi vắng mặt.** Người phê duyệt nghỉ phép có thể ủy quyền tạm thời việc duyệt cho người khác. Chưa chốt: người nhận ủy quyền có phải tự đủ năng lực theo `BR-31.2` không, hay ủy quyền chỉ chuyển trách nhiệm mà không chuyển năng lực.
3. **Tự thu hồi truy cập theo hệ thống danh tính của doanh nghiệp.** Khi doanh nghiệp tắt tài khoản công ty của nhân viên ở hệ thống danh tính của họ, quyền vào workspace nên tự tạm ngưng. Chưa chốt: đồng bộ theo chuẩn cấp phát tài khoản tự động hay theo đăng nhập một lần bắt buộc, tài khoản đặt mật khẩu riêng xử lý thế nào, và phạm vi này thuộc tài liệu này hay phần xác thực.
4. **Chuyển nhật ký liên tục sang hệ thống giám sát của doanh nghiệp.** Doanh nghiệp lớn muốn nhận nhật ký quyền liên tục vào hệ thống giám sát an ninh của mình. Chưa chốt: định dạng, nhật ký nào được chuyển, và việc chuyển có tính là "xuất dữ liệu" phải ghi nhật ký hay không.

---

## Phụ lục A — Danh mục Khái niệm Nghiệp vụ

> Phụ lục này mô tả **khái niệm nghiệp vụ**, không phải thiết kế dữ liệu, và không mang tính ràng buộc kỹ thuật. Cách tổ chức lưu trữ thuộc thẩm quyền đội phát triển.

| Khái niệm | Thông tin nghiệp vụ cốt lõi |
| --- | --- |
| A.1 Tài khoản người dùng | Email (duy nhất toàn hệ thống), họ tên, trạng thái khoá toàn hệ thống, ngôn ngữ và múi giờ cá nhân |
| A.2 Thành viên | Tài khoản, workspace, trạng thái (Đang chờ chấp nhận / Chờ duyệt gia nhập / Đang hoạt động / Tạm ngưng / Đã rời), cấp bậc, vai trò, Đơn vị chính, Đơn vị kiêm nhiệm (kèm mức Chỉ xem / Đầy đủ và ngày kết thúc), quản lý trực tiếp, lượt thu hồi riêng lẻ, lần đăng nhập gần nhất, ngày hẹn kích hoạt lại |
| A.3 Lời mời | Thành viên được mời, người mời, các lựa chọn đã chọn, thời điểm gửi, hạn, trạng thái (Soạn sẵn / Chờ gửi / Đang chờ / Tạm treo / Đã chấp nhận / Đã từ chối / Hết hạn / Đã thu hồi), số bản ghi đang giữ chỗ |
| A.4 Liên kết mời / Tự gia nhập | Người tạo, đơn vị, vai trò, nhóm, hạn, số lượt tối đa, số lượt đã dùng, có cần duyệt, tên miền (với tự gia nhập), trạng thái |
| A.5 Vai trò | Tên, mô tả, loại (dựng sẵn / tự tạo), ma trận ô, quyền quản trị, ô đã điều chỉnh (với vai trò dựng sẵn), các phiên bản lịch sử |
| A.6 Nhóm | Tên, mô tả, màu, nhóm cha, vai trò, thành viên, cấu hình Object Manager gán cho nhóm |
| A.7 Đơn vị tổ chức | Tên, mã, đơn vị cha, người phụ trách chính, đồng phụ trách, vai trò gợi ý |
| A.8 Mức nền | Theo loại dữ liệu và thao tác: mức nền; cờ công khai đọc |
| A.9 Quyền tạm thời | Người hoặc nhóm nhận, vai trò, lý do, người yêu cầu, các lượt phê duyệt, thời điểm bắt đầu, hạn, trạng thái, nguyên nhân kết thúc |
| A.10 Quyền Ủy thác | Người giữ, danh sách vai trò được giao, người cấp, thời điểm |
| A.11 Chính sách truy cập | Loại dữ liệu, thao tác, hiệu lực, mức nới (với Cho phép), điều kiện, đối tượng áp dụng, trạng thái, các phiên bản |
| A.12 Lượt cấp / chặn trên bản ghi | Bản ghi, người hoặc nhóm, loại (cấp / chặn), mức trần, hạn, nguồn sinh, người tạo, lý do |
| A.13 Quy tắc tách biệt nhiệm vụ | Tên, hai vế, lý do, các vi phạm được chấp nhận kèm lý do |
| A.14 Chức danh trách nhiệm | Loại chức danh, người được chỉ định |
| A.15 Yêu cầu chuyển nhượng / Đề nghị khôi phục quyền sở hữu | Người khởi tạo, người nhận hoặc được đề xuất, cấp bậc sau chuyển, căn cứ, kết quả thẩm định, hạn, trạng thái |
| A.16 Phiên của nhà cung cấp | Nhân sự vận hành, workspace, loại (Phiên hỗ trợ / phiên khẩn cấp / Phiên triển khai), mã yêu cầu hỗ trợ hoặc số hồ sơ sự cố, mức (Chỉ xem / Xem và sửa dữ liệu nghiệp vụ, với Phiên hỗ trợ), người duyệt, thời điểm bắt đầu và kết thúc, các thay đổi soạn sẵn và người xác nhận |
| A.17 Biên bản rời workspace | Thành viên rời đi, các bước, người nhận từng phần, người thực hiện, thời điểm |
| A.18 Đợt rà soát quyền | Phạm vi, chu kỳ, hạn, các mục, người rà soát, quyết định, cách xử lý mục quá hạn |
| A.19 Bản ghi nhật ký | Nhóm nhật ký, người thực hiện (kèm nhãn Nhà cung cấp / Hệ thống), thời điểm, đối tượng, giá trị trước/sau, lý do, người duyệt thứ hai, mã lô, cờ ghi bù |

---

## Phụ lục B — Danh mục Tham số Cấu hình theo Workspace

**Nguyên tắc:** mọi quy tắc có nhiều cách xử lý hợp lý tuỳ doanh nghiệp là một tham số cấp workspace, có giá trị mặc định chuẩn. Phụ lục này là nguồn duy nhất về giá trị mặc định và miền giá trị; quy tắc trong thân tài liệu nêu mặc định chỉ để dễ đọc. Mọi thay đổi tham số ghi nhật ký thay đổi cấu hình quyền (`BR-41.4`).

**Thẩm quyền:** cột "Thẩm quyền" nêu ai được đổi. "Người có toàn quyền" = Chủ sở hữu hoặc Quản trị viên. "+ Người duyệt thứ hai" = cần một người khác duyệt theo `BR-47.3`.

**Mức độ tự do:** **Tự do** — đặt bất kỳ giá trị nào trong miền. **Có sàn bắt buộc** — có giới hạn không nới được, kèm lý do.

| Mã | Quy tắc | Nội dung | Mặc định | Miền giá trị | Thẩm quyền | Mức độ tự do |
| --- | --- | --- | --- | --- | --- | --- |
| `CFG-03-01` | `FEAT-03` | Ngưỡng cảnh báo Người có toàn quyền hoặc người giữ Quyền Ủy thác không đăng nhập | 90 ngày | 30 – 365 ngày | Người có toàn quyền | Tự do |
| `CFG-06-01` | `FEAT-06` | Thời hạn chờ xác nhận chuyển nhượng quyền sở hữu | 72 giờ | 24 – 168 giờ | Chủ sở hữu | Tự do |
| `CFG-08-01` | `BR-08.1` | Chế độ truy cập hỗ trợ của nhà cung cấp | Chỉ khi doanh nghiệp cấp phiên | Chỉ khi doanh nghiệp cấp phiên / Cho phép kèm thông báo | Chủ sở hữu | Tự do |
| `CFG-08-02` | `BR-08.2` | Thời lượng tối đa của một phiên hỗ trợ | 24 giờ | 1 giờ – 7 ngày | Chủ sở hữu | Tự do |
| `CFG-08-03` | `FEAT-08` | Hạn chờ duyệt một đề nghị phiên hỗ trợ | 24 giờ | 1 – 72 giờ | Chủ sở hữu | Tự do |
| `CFG-08-04` | `BR-08.7` | Thời hạn tối đa của một Phiên triển khai | 14 ngày | 1 – 60 ngày | Chủ sở hữu | Tự do |
| `CFG-08-05` | `BR-08.7` | Thời gian giữ thay đổi, lời mời, lô nhập soạn sẵn sau khi phiên kết thúc | 30 ngày | 7 – 90 ngày | Người có toàn quyền | Tự do |
| `CFG-09-01` | `FEAT-09` | Hạn hiệu lực lời mời | 7 ngày | 1 – 30 ngày | Người có toàn quyền | Tự do |
| `CFG-09-02` | `BR-09.6` | Vai trò chọn sẵn khi mời (khi đơn vị không có vai trò gợi ý) | Chỉ xem bản ghi được giao | Một vai trò bất kỳ / Không chọn sẵn (bắt buộc người mời chọn) | Người có toàn quyền | Tự do |
| `CFG-09-03` | `FEAT-09` | Tên miền email được phép mời | Không giới hạn | Không giới hạn / danh sách tên miền | Người có toàn quyền | Tự do |
| `CFG-09-04` | `FEAT-09` | Mốc nhắc trước khi hết hạn lời mời đang giữ chỗ bản ghi | 2 ngày | 1 – 7 ngày | Người có toàn quyền | Tự do |
| `CFG-12-01` | `BR-12.3` | Cần người duyệt thứ hai khi nâng lên Quản trị viên | Tắt | Bật / Tắt | Chủ sở hữu | Tự do |
| `CFG-12-02` | `FEAT-12` | Số Quản trị viên tối đa | Không giới hạn | 1 – 50 / Không giới hạn | Chủ sở hữu | Tự do |
| `CFG-25-01` | `BR-25.6` | Hạn của đề nghị chuyển bản ghi chưa được chấp nhận; SRS phân hệ được khai báo mặc định ngắn hơn cho loại cần phản hồi tức thời (ví dụ hội thoại trực tiếp) | 3 ngày | 5 phút – 14 ngày | Người có toàn quyền | Tự do |
| `CFG-28-01` | `BR-28.1`, `FEAT-20` | Ngưỡng số người bị ảnh hưởng buộc gõ lại tên khi xoá vai trò hoặc nhóm | 10 | 1 – 1.000 | Người có toàn quyền | Tự do |
| `CFG-29-01` | `BR-29.1` | Áp dụng thay đổi nới rộng của vai trò dựng sẵn từ bản phát hành | Chờ Người có toàn quyền duyệt | Chờ duyệt / Áp ngay | Chủ sở hữu | Tự do |
| `CFG-29-02` | `BR-29.4` | Điều chỉnh ô của vai trò dựng sẵn (thay cho `CFG-05-02` trước đây tại [`contacts-srs.md`](./contacts-srs.md)) | Ma trận mặc định tại `FEAT-29` và SRS phân hệ | Mỗi ô một mức hợp lệ | Người có quyền Quản lý vai trò, chịu trần năng lực (`BR-29.4`); ô mang sàn của phân hệ cần thêm thẩm quyền do SRS phân hệ quy định | **Có sàn bắt buộc** — `BR-25.1`, `BR-25.3` |
| `CFG-30-01` | `BR-30.1` | Thời hạn tối đa của quyền tạm thời | 90 ngày | 1 – 180 ngày | Chủ sở hữu | Tự do |
| `CFG-30-02` | `FEAT-30` | Thời hạn chờ phê duyệt trước khi yêu cầu tự hết hạn | 7 ngày | 1 – 30 ngày | Người có toàn quyền | Tự do |
| `CFG-30-03` | `BR-30.4` | Nhắc trước khi quyền tạm thời hết hạn | 3 ngày | 0 – 14 ngày | Người có toàn quyền | Tự do |
| `CFG-31-01` | `BR-31.1` | Số lượt phê duyệt cần cho quyền tạm thời | 2 | 1 – 3 | Chủ sở hữu | Tự do |
| `CFG-33-01` | `FEAT-33` | Ngưỡng "gia hạn liên tục" trong báo cáo kiểm định | 3 lần cùng vai trò trong 12 tháng | 2 – 10 lần; 3 – 24 tháng | Người có toàn quyền | Tự do |
| `CFG-34-01` | `FEAT-34` | Mức nền và công khai đọc theo loại dữ liệu | Mức nền = Chỉ của mình cho mọi ô, riêng Tạo = Không có; công khai đọc tắt | Theo `FEAT-34` | Thu hẹp: Q: Quản lý cấu hình workspace; nới rộng: Người có toàn quyền (`BR-34.5`) | Tự do |
| `CFG-34-02` | `BR-34.3` | Người phụ trách đơn vị xem toàn nhánh | Tắt | Bật / Tắt | Bật: Người có toàn quyền; tắt: Q: Quản lý cấu hình workspace | Tự do |
| `CFG-35-01` | `BR-35.12` | Đơn vị tiếp nhận mặc định theo loại nguồn do SRS phân hệ khai báo (ví dụ biểu mẫu website, từng loại kênh hội thoại hay hộp thư, khách hàng tiềm năng sinh từ hội thoại, vé từ hội thoại, liên hệ lại từ chiến dịch, nhập từ tệp, quy trình tự động hoá, quy tắc phân công tự động, ma trận chuyển đổi), cho workspace và ghi đè theo đơn vị | Chưa đặt (nguồn mới phải chọn đơn vị) | Mỗi loại nguồn một đơn vị cho workspace; tuỳ chọn một đơn vị ghi đè cho mỗi đơn vị trong cây | Người có toàn quyền | Tự do |
| `CFG-41-01` | `BR-41.4` | Thời hạn lưu nhật ký quyết định truy cập | 180 ngày | 30 – 365 ngày, tối đa theo gói | Chủ sở hữu | **Có sàn bắt buộc** — tối thiểu 30 ngày để điều tra được sự cố gần nhất |
| `CFG-41-02` | `BR-41.4` | Thời hạn lưu nhật ký thay đổi cấu hình quyền và nhật ký truy cập của nhà cung cấp | 2 năm | 1 – 7 năm, tối đa theo gói | Chủ sở hữu + Người duyệt thứ hai | **Có sàn bắt buộc** — tối thiểu 1 năm, đủ cho một chu kỳ kiểm toán năm; giảm thời hạn có hiệu lực sau 30 ngày để kịp xuất |
| `CFG-42-01` | `FEAT-42` | Tự tạm ngưng thành viên không đăng nhập | Tắt | Tắt / 30 – 365 ngày | Người có toàn quyền | Tự do |
| `CFG-42-02` | `FEAT-42` | Báo trước khi tự tạm ngưng | 7 ngày | 1 – 30 ngày | Người có toàn quyền | Tự do |
| `CFG-46-01` | `BR-46.2` | Cách xử lý vi phạm tách biệt nhiệm vụ | Chặn | Chặn / Cảnh báo kèm lý do | Người có toàn quyền | Tự do |
| `CFG-48-01` | `FEAT-48` | Chu kỳ rà soát quyền | Tắt | Tắt / Quý / Nửa năm / Năm | Người có toàn quyền | Tự do |
| `CFG-48-02` | `FEAT-48` | Thời hạn phản hồi một đợt rà soát | 14 ngày | 7 – 60 ngày | Người có toàn quyền | Tự do |
| `CFG-48-03` | `BR-48.3` | Xử lý mục rà soát quá hạn | Chỉ nhắc và báo Chủ sở hữu | Chỉ nhắc / Tự thu hồi | Chủ sở hữu | Tự do |
| `CFG-50-01` | `BR-50.2` | Thời hạn và số lượt mặc định của liên kết mời | 7 ngày; 50 lượt | 1 – 30 ngày; 1 – 500 lượt | Người có toàn quyền | Tự do |

**Hằng số hệ thống (không cấu hình theo workspace), kèm lý do:**

| Hằng số | Giá trị | Lý do |
| --- | --- | --- |
| Thời gian chờ phản đối khi khôi phục quyền sở hữu (`BR-07.2`) | 7 ngày | Chống chiếm quyền; doanh nghiệp rút ngắn được thì kẻ chiếm quyền cũng rút ngắn được |
| Giới hạn phiên khẩn cấp của nhà cung cấp (`BR-08.4`) | Chỉ xem, tối đa 4 giờ | Phiên bỏ qua sự đồng ý của doanh nghiệp phải hẹp và ngắn |
| Độ sâu cây tổ chức (`BR-21.3`) | 10 cấp | Thời gian tính phạm vi và khả năng đọc hiểu |
| Độ sâu phân cấp nhóm (`BR-17.4`) | 5 cấp | Giải thích được quyền hiệu lực |
| Trần của liên kết mời (`BR-50.2`) | Tối đa 30 ngày, 500 lượt | Hạn chế thiệt hại khi liên kết bị chuyển tiếp; giá trị dùng hằng ngày là `CFG-50-01` |
| Tác nhân AI luôn bị che (`BR-40.1`) | Không ngoại lệ | Đầu ra AI ra ngoài tầm kiểm soát của nhật ký truy cập |
| Không truyền tiếp Quyền Ủy thác (`BR-45.1`) | Không | Giữ được danh sách người đang giữ quyền miễn trần |
| Ngưỡng cảnh báo ghi bù nhật ký chưa xong (`BR-41.6`) | 15 phút | Chuẩn vận hành, không phải lựa chọn nghiệp vụ |

---

## Phụ lục C — Nhật ký Mâu thuẫn & Quyết định đã chốt

| # | Mâu thuẫn / câu hỏi | Cách xử lý đã chốt | Nơi có hiệu lực |
| --- | --- | --- | --- |
| C.1 | Nhóm A trùng lặp gần hết với [`onboarding-srs.md`](./onboarding-srs.md) (đăng ký, tên miền phụ, khởi tạo, dữ liệu mẫu, sức khỏe cấu hình, chuyển nhượng) | Onboarding sở hữu các bước đăng ký, tên miền phụ, chuỗi khởi tạo, dữ liệu mẫu, dùng thử; tài liệu này sở hữu Chủ sở hữu, bất biến phân quyền, sức khỏe cấu hình, cấu hình chung, trần quyền, chuyển nhượng, vai trò dựng sẵn, lời mời. Nhóm A tổ chức lại; mã từ `FEAT-09` giữ nguyên | Nhóm A, `FEAT-09`, `FEAT-29` |
| C.2 | Hạ Quản trị viên xuống Thành viên mà "phiên không bị ảnh hưởng", trái yêu cầu thu hồi tức thời | Danh sách sự kiện chấm dứt phiên duy nhất tại `BR-11.3`, gồm hạ cấp bậc; mọi thu hẹp khác có hiệu lực ở thao tác kế tiếp | `BR-11.3`, `BR-12.5`, `NFR-04` |
| C.3 | Nhật ký cấu hình quyền đóng khi lỗi làm huỷ cả thao tác thu hồi khẩn cấp | Đóng khi lỗi chỉ cho thao tác nới rộng và trung tính; thao tác thu hẹp không bị chặn, nhật ký ghi bù | `BR-41.5`, `BR-41.6`, ADR-0010 |
| C.4 | Thứ tự hợp nhất quyền tự mâu thuẫn ("mỗi trục chỉ hẹp hơn trục trước" trong khi lượt cấp nới rộng); chính sách và phụ trách đơn vị không có vị trí | Bốn bước: mức theo ô → nguồn nới phạm vi (không mở ô Không có) → nguồn chặn → phân quyền trường và che | `BR-39.6` |
| C.5 | Sao chép vai trò được miễn trần năng lực, kết hợp Quyền Ủy thác thành đường leo thang | Sao chép và khôi phục phiên bản chịu đủ trần năng lực | `BR-25.5`, `BR-27.2` |
| C.6 | Quyền Ủy thác cho gán "bất kỳ vai trò nào" mà không ai biết giới hạn | Cấp kèm danh sách vai trò được giao; chỉ Người có toàn quyền cấp; không truyền tiếp; không nằm trong vai trò | `FEAT-45`, `BR-24.1` |
| C.7 | Nâng lên Quản trị viên: `FEAT-12` cho người có quyền Quản lý vai trò, `BR-09.2` chỉ cho Người có toàn quyền | Chỉ Người có toàn quyền; tuỳ chọn người duyệt thứ hai | `BR-12.1`, `CFG-12-01` |
| C.8 | Kênh tạo hộ "mời với vai trò Chủ sở hữu" và "workspace chưa gán người" trái nguyên tắc chỉ một Chủ sở hữu | Chủ sở hữu được chỉ định ngay khi tạo, ở trạng thái Đang chờ chấp nhận; không mời được Chủ sở hữu | `BR-01.1`, `BR-01.3`, `BR-09.1` |
| C.9 | Người đã có tài khoản bị "thêm thẳng" vào workspace | Mọi lời mời phải được chấp nhận; vòng đời lời mời đầy đủ | `BR-09.7`, `FEAT-09` |
| C.10 | Bỏ trống vị trí thì lấy theo người mời, xếp sai đơn vị | Để trống, hiện cảnh báo; vai trò chọn sẵn theo đơn vị hoặc tham số, luôn hiển thị | `BR-09.5`, `BR-09.6`, `BR-21.4` |
| C.11 | Doanh nghiệp không tự chặn được người của mình; chỉ nhà cung cấp khoá được tài khoản | Trạng thái Tạm ngưng cấp workspace; khoá tài khoản giữ cho sự cố toàn hệ thống | `FEAT-42`, `BR-11.1`, `BR-14.2` |
| C.12 | Gỡ thành viên để lại bản ghi vô chủ, cấp dưới mất quản lý, quyền mồ côi | Quy trình rời workspace bắt buộc trước khi gỡ; gỡ dọn sạch quyền mọc từ thành viên | `FEAT-43`, `BR-13.1` |
| C.13 | Nhân sự vận hành nền tảng truy cập mọi workspace không đồng ý, không vết | Chỉ qua Phiên hỗ trợ; mọi thao tác vào nhật ký workspace; phiên khẩn cấp có hậu kiểm | `FEAT-08`, `BR-05.3` |
| C.14 | Không có đường khi Chủ sở hữu biến mất | Khôi phục quyền sở hữu có văn bản, thẩm định và 7 ngày chờ phản đối | `FEAT-07` |
| C.15 | Xoá nhóm có làm sạch quyền tạm thời cấp cho nhóm không (Mục 7 cũ) | Có, theo Nguyên tắc 6 | `BR-20.4` |
| C.16 | Độ trễ thay đổi sơ đồ tổ chức "khoảng 1 phút" có cần rút ngắn cho cách ly khẩn cấp (Mục 7 cũ) | Cách ly khẩn cấp dùng Tạm ngưng (≤ 5 giây); thay đổi cây ≤ 60 giây | `NFR-04`, `FEAT-42` |
| C.17 | Một quyền cấu hình hệ thống dùng chung cho vai trò, chính sách, quyền trên bản ghi, quyền tạm thời (Mục 7 cũ) | Tách thành các quyền quản trị riêng | `FEAT-24` |
| C.18 | Yêu cầu quyền tạm thời không báo người phê duyệt (Mục 7 cũ); doanh nghiệp nhỏ không đủ 2 người duyệt | Thông báo mọi người phê duyệt hợp lệ; số lượt duyệt là tham số 1 – 3; chặn gửi khi không đủ người | `FEAT-30`, `BR-31.2`, `CFG-31-01` |
| C.19 | Hiệu năng khi workspace rất lớn (Mục 7 cũ) | Đặt yêu cầu đo được | `NFR-05`, `NFR-09` |
| C.20 | Người phụ trách đơn vị rời đi vẫn giữ vị trí và quyền xem toàn nhánh (Mục 7 cũ) | Xử lý trong quy trình rời workspace; cảnh báo sức khỏe cấu hình | `BR-21.2`, `FEAT-43` |
| C.21 | Nhật ký quyết định truy cập có thể bị tắt âm thầm; nhật ký bị bỏ sót (Mục 7 cũ) | Không tắt được; giám sát tỷ lệ bỏ lỡ và cảnh báo | `NFR-08` |
| C.22 | Quyền trên bản ghi chưa có lịch sử phiên bản riêng (Mục 7 cũ) | Không cần: mọi lượt cấp/chặn đã có vết đầy đủ trong nhật ký cấu hình quyền | `BR-41.4` |
| C.23 | Đổi tên miền phụ sau khi hoạt động (Mục 7 cũ) | Thuộc phạm vi [`onboarding-srs.md`](./onboarding-srs.md) | — |
| C.24 | Thời hạn lưu nhật ký cố định "cho mọi gói" | Tham số có sàn bắt buộc, trần theo gói | `CFG-41-01`, `CFG-41-02` |
| C.25 | "Sàn bắt buộc" chỉ định nghĩa trên ô, trong khi phân hệ khai báo sàn trên nhóm trường và quyền đọc nhật ký, và áp cả lên Người có toàn quyền | Định nghĩa chung tại Mục 1.4; áp trên ô, quyền quản trị và nhóm trường; sàn nào áp cả Người có toàn quyền thì SRS phân hệ nêu rõ | Mục 1.4, `BR-25.3` |
| C.26 | Ba mô hình che dữ liệu song song (tài liệu này, [`contacts-srs.md`](./contacts-srs.md) `FEAT-04`, Object Manager) | Tài liệu này giữ khung và mẫu che mặc định; phân hệ khai báo trường và mẫu che riêng; hạn chế thắng giữa các lớp | `FEAT-40`, `BR-40.3` |
| C.27 | "Nhóm" ở tài liệu này và "Nhóm quyền" ở Object Manager | Cùng một thực thể | Mục 1.4 |
| C.28 | "Quản lý trực tiếp" được các SRS khác dẫn chiếu mà chưa có định nghĩa; lẫn với người phụ trách đơn vị | Định nghĩa riêng hai khái niệm | Mục 1.4 |
| C.29 | Công khai đọc "nâng ô Xem của mọi vai trò" trái `BR-34.1` "không âm thầm mở rộng" | Công khai đọc là nguồn nới phạm vi tường minh, chỉ cho thao tác Xem, không áp cho ô Xem = Không có | `BR-34.4`, `BR-39.6` |
| C.30 | Chính sách Cho phép "mở thêm trong phạm vi đã có" không có nghĩa rõ | Cho phép nới phạm vi của thao tác đã có, không cấp năng lực | `BR-36.2` |
| C.31 | Chính sách Từ chối "mọi loại, mọi thao tác" có thể khoá cả Người có toàn quyền | Chính sách và lượt chặn trên bản ghi không áp lên Người có toàn quyền | `BR-36.5`, `BR-39.1` |
| C.32 | Tiến trình chạy thay người dùng giữ mức lúc khởi chạy kể cả khi người đó mất quyền | Dùng phần giao của mức lúc khởi chạy và mức hiện tại; tạm dừng khi người khởi chạy bị chặn | `BR-35.8` |
| C.33 | Bản phát hành tự mở rộng quyền của vai trò dựng sẵn không qua ai | Thu hẹp áp ngay; nới rộng theo tham số, mặc định chờ duyệt; ghi nhật ký | `BR-29.1`, `CFG-29-01` |
| C.34 | Mặc định vai trò "Chỉ xem" ở mức Đơn vị của mình làm người mới thấy toàn bộ dữ liệu của phòng | Vai trò dựng sẵn "Chỉ xem bản ghi được giao" ở mức Chỉ của mình; luôn hiển thị lựa chọn sẵn; mức đặt sẵn "Chỉ xem đơn vị" mang tên khác để không nhầm | `FEAT-29`, `BR-09.6` |
| C.35 | Thiết lập phân quyền quá phức tạp cho doanh nghiệp nhỏ | Chế độ Cơ bản với mức đặt sẵn; vai trò gợi ý theo đơn vị; khu Nâng cao tách riêng; mời nhiều email một lần, liên kết mời, tự gia nhập theo tên miền | `BR-24.3`, `BR-25.4`, `BR-21.4`, `FEAT-09`, `FEAT-50` |
| C.36 | Không có quy tắc nào cho người dùng thuộc nhiều workspace | Bộ chọn workspace, quyền xét theo workspace của thao tác, chấm dứt phiên theo đúng workspace, không lộ thông tin chéo | `FEAT-51` |
| C.37 | `CFG-05-02` (điều chỉnh ô vai trò dựng sẵn) nằm ở [`contacts-srs.md`](./contacts-srs.md) trong khi bản chất là tham số chung của mọi loại dữ liệu | Chuyển về tài liệu này thành `CFG-29-02`; `contacts-srs.md` chỉ giữ sàn của phân hệ (dòng `CFG-05-02` ở đó đã thay bằng dòng dẫn chiếu) | `CFG-29-02` |
| C.38 | Một tài khoản muốn tự tạo thêm workspace mà không đăng ký lại | Cho phép tự phục vụ, có giới hạn số workspace dùng thử theo tài khoản; luồng tại [`onboarding-srs.md`](./onboarding-srs.md) `FEAT-19` | `BR-01.2` |
| C.39 | Trưởng nhóm và thành viên được mời cùng lúc nhưng quản lý trực tiếp phải Đang hoạt động | Cho chọn quản lý Đang chờ chấp nhận trong cùng lượt mời; quan hệ có hiệu lực khi cả hai Đang hoạt động | `BR-09.4` |
| C.40 | Vượt hạn mức người dùng luôn bị chặn, trong khi gói có thể cho phép tính phí vượt | Áp đúng chính sách chạm trần của gói | `BR-09.8` |
| C.41 | Nhiều đường nới phạm vi không chịu trần năng lực: mức nền, chính sách Cho phép, gỡ hạn chế, người phụ trách đơn vị, quản lý trực tiếp, lượt cấp trên bản ghi, tự nhận bàn giao | Nguyên tắc 1 và 2 viết lại cho mọi thao tác làm tăng tập bản ghi hoặc tập thao tác; quy tắc riêng ở từng nơi | Mục 2.4, `BR-11.7`, `BR-11.8`, `BR-18.3`, `BR-21.5`, `BR-34.5`, `BR-36.6`, `BR-39.7`, `BR-43.4` |
| C.42 | Quyền tạm thời bị "rửa" thành quyền vĩnh viễn qua việc tạo vai trò hay mời người | Trần cho lượt cấp vĩnh viễn không tính quyền tạm thời | Mục 1.4 |
| C.43 | Nhà cung cấp làm được gì trong phiên hỗ trợ chưa rõ; phiên khẩn cấp có ngoại lệ ghi | Hai mức do doanh nghiệp chọn; không bao giờ đổi cấu hình quyền; phiên khẩn cấp luôn chỉ xem | `BR-08.5`, `BR-08.4` |
| C.44 | Thu hồi riêng lẻ thắng hay thua quyền tạm thời ở thứ tự hợp nhất | Thua; thứ tự tính ô viết lại | `BR-39.6`, `BR-11.6` |
| C.45 | Người gửi đề nghị khôi phục tự đề cử mình; chỉ Chủ sở hữu đã mất phản đối được | Cấm tự đề cử; Người có toàn quyền khác và Người phụ trách bảo mật phản đối được | `BR-07.4` |
| C.46 | Người rà soát thu hồi được Quyền Ủy thác và chức danh, trái thẩm quyền chỉ của Người có toàn quyền; tự thu hồi tước sạch vai trò | Chỉ đề xuất với các mục đó; tự thu hồi không lấy vai trò cuối cùng | `BR-48.2`, `BR-48.3` |
| C.47 | Quy tắc tách biệt nhiệm vụ chặn mọi lượt nâng Quản trị viên | Không áp lên Người có toàn quyền | `BR-46.4` |
| C.48 | Bật người duyệt thứ hai khi chỉ có Chủ sở hữu làm không tạo được Quản trị viên đầu tiên | Khi chỉ có một Người có toàn quyền Đang hoạt động, lượt nâng không cần người duyệt thứ hai | `BR-12.3` |
| C.49 | Bản ghi chưa có Người phụ trách không thuộc phạm vi của ai; hàng đợi hỗ trợ không vận hành | Đơn vị tiếp nhận do phân hệ khai báo; tự nhận là thao tác Gán | `BR-35.10`, `BR-35.11` |
| C.50 | Kiêm nhiệm mang theo mọi thao tác của vai trò; chuyển phòng giữ vai trò cũ; nghỉ dài không có người xử lý thay | Kiêm nhiệm mặc định Chỉ xem; chuyển phòng đề xuất đổi vai trò gợi ý; tạm ngưng có chuyển tạm | `BR-11.4`, `BR-11.9`, `BR-42.4` |
| C.51 | Liên kết mời không cần duyệt, không giới hạn tên miền, còn hiệu lực sau khi người tạo mất quyền | Mặc định cần duyệt; chịu tên miền được phép; đi theo người tạo; trạng thái Chờ duyệt gia nhập riêng | `BR-50.5`, `BR-50.6`, Mục 1.4 |
| C.52 | Mặc định vai trò Quản lý được xuất và nhập toàn nhánh ngay ngày đầu; tên mức đặt sẵn "Làm việc với dữ liệu của mình" gây hiểu nhầm | Quản lý: Xoá = Đơn vị của mình, Xuất và Nhập = Không có; đổi tên, thêm mức "Chỉ dữ liệu của mình" | `BR-25.4`, `FEAT-29` |
| C.53 | Nhật ký quyết định truy cập 60 ngày không đủ điều tra rò rỉ phát hiện muộn | Mặc định 180 ngày | `CFG-41-01` |
| C.54 | Xoá vai trò để lại vai trò gợi ý, liên kết mời, tham số và quy tắc trỏ tới vai trò không còn tồn tại | Dọn mọi nơi trỏ tới, liệt kê trước khi xác nhận | `BR-28.4` |
| C.55 | Nhận việc từ hàng đợi bị chặn với vai trò mặc định và là tự nới quyền không được khai báo | Ngoại lệ tường minh của Nguyên tắc 2, giới hạn trong đơn vị tiếp nhận; mọi thành viên đơn vị tiếp nhận thấy hàng đợi | `BR-35.11`, Mục 2.4 |
| C.56 | Đặt người khác làm người phụ trách đơn vị hoặc di chuyển đơn vị là đường nới phạm vi qua người trung gian | *(Phần di chuyển đơn vị đã được thay bởi C.71)* Chỉ Người có toàn quyền khi `CFG-34-02` bật; di chuyển đơn vị cần toàn quyền hoặc mức Xem Toàn workspace | `BR-21.6` |
| C.57 | Rà soát quyền gỡ nhóm mang hạn chế là nới rộng; tự gia nhập không đi theo người bật; chính sách Cho phép chỉ ràng buộc người tạo | Chỉ đề xuất với nhóm mang hạn chế; tự gia nhập tạm tắt; ràng buộc mọi người sửa, bật, khôi phục | `BR-48.2`, `BR-48.3`, `BR-50.6`, `BR-36.6` |
| C.58 | Kiêm nhiệm Chỉ xem làm bản ghi của người kiêm nhiệm lộ ra đơn vị kiêm nhiệm | Chỉ Đơn vị chính và kiêm nhiệm Đầy đủ quyết định bản ghi thuộc đơn vị nào | Mục 1.4, `FEAT-34` |
| C.59 | Chuyển phòng để lại quan hệ quản lý, vị trí phụ trách, nhóm; bản ghi đi theo người không sửa được | Danh sách bước như rời workspace; chặn "đi theo người" khi không còn ô Sửa | `BR-11.9` |
| C.60 | Bằng chứng truy cập của nhà cung cấp không đủ cho kiểm toán năm; mức phiên chưa nói về xuất dữ liệu và che; phiên Chỉ xem không chẩn đoán được quyền | Ghi đóng khi lỗi, lưu theo `CFG-41-02`; không xuất, nhập; luôn che; xem được cấu hình quyền | `BR-08.3`, `BR-08.5` |
| C.61 | Sau kích hoạt, đội triển khai của nhà cung cấp không có kênh cấu hình hợp lệ | *(Cách duyệt và trần đã được thay bởi C.65)* Phiên triển khai do Chủ sở hữu duyệt, chỉ cấu hình, chịu trần năng lực của Chủ sở hữu | `BR-08.7`, `CFG-08-04` |
| C.62 | Vai trò Kiểm toán quyền xem được tên bản ghi qua nhật ký quyết định truy cập | Chỉ thấy mã bản ghi khi không có mức Xem | `BR-41.7` |
| C.63 | Vai trò dựng sẵn chỉ xem hiện "Tuỳ chỉnh" ở chế độ Cơ bản | Thêm mức đặt sẵn Chỉ xem của mình và Chỉ xem toàn workspace | `BR-25.4` |
| C.64 | Cấm tự tạm ngưng không có lý do; nghỉ dài của quản lý không có người thay | `BR-42.5`; quản lý tạm thời cho cấp dưới | `BR-42.4`, `BR-42.5` |
| C.65 | Phiên triển khai cho nhà cung cấp nới quyền của thành viên đang làm việc mà không ai xác nhận | Thay đổi nới rộng và lời mời chỉ soạn sẵn, Người có toàn quyền xác nhận kèm khác biệt; người duyệt là Chủ sở hữu hoặc Quản trị viên kèm người duyệt thứ hai | `BR-08.7` |
| C.66 | Thao tác của nhà cung cấp ghi vào nhật ký quyết định truy cập vốn được bỏ sót và lưu ngắn | Nhóm nhật ký riêng "truy cập của nhà cung cấp", đóng khi lỗi, lưu theo `CFG-41-02`; có trong báo cáo kiểm toán | `BR-08.3`, `BR-41.4`, `FEAT-49` |
| C.67 | Di chuyển đơn vị chỉ cần mức Xem Toàn workspace; tắt chính sách Từ chối của người khác không chịu trần | *(Phần di chuyển đơn vị đã được thay bởi C.71)* Cần Toàn workspace ở mọi ô; tắt Từ chối chịu trần của mọi người bị áp | `BR-21.6`, `BR-36.6` |
| C.68 | Người kiêm nhiệm hai đội: kiêm nhiệm Đầy đủ lộ dữ liệu, kiêm nhiệm Chỉ xem không nhận việc được; vé đã nhận rời khỏi đơn vị tiếp nhận | *(Phạm vi "luôn thuộc" đã được thu hẹp bởi C.72)* Bản ghi hàng đợi luôn thuộc đơn vị tiếp nhận; kiêm nhiệm Chỉ xem được nhận việc từ hàng đợi; hàng đợi có thể gồm đơn vị con | `BR-35.10`, `BR-35.11`, `BR-11.4` |
| C.69 | Người xử lý thay tự nhận bản ghi; tạm ngưng bị chặn khi nhật ký lỗi hoặc khi là Người phụ trách thanh toán | *(Phần Người phụ trách thanh toán đã được thay bởi C.77)* Tạm ngưng tách khỏi chuyển tạm, không bao giờ bị chặn; người xử lý thay và quản lý tạm thời chịu ràng buộc bàn giao; chức danh thanh toán về Chủ sở hữu | `BR-42.4`, `FEAT-42` |
| C.70 | Mức đặt sẵn vô tình mở thao tác đặc thù; tên "Toàn quyền" nhầm với Người có toàn quyền | *(Cách thể hiện thao tác đặc thù đã được thay bởi C.75 và C.78)* Thao tác đặc thù Không có ở mọi mức đặt sẵn trừ "Toàn bộ dữ liệu"; đổi tên | `BR-25.4` |
| C.71 | Điều kiện "Toàn workspace ở mọi ô" để di chuyển đơn vị không bao giờ thỏa được | Chỉ Người có toàn quyền di chuyển đơn vị | `BR-21.6` |
| C.72 | Mọi bản ghi của hàng đợi thuộc đơn vị tiếp nhận suốt đời, kể cả khách hàng sống nhiều năm | Phân hệ khai báo bản ghi công việc (ở lại) và bản ghi chờ phân công (theo người phụ trách khi đã có); tệp nhập không qua hàng đợi | `BR-35.10`, `BR-35.11` |
| C.73 | Đổi đơn vị tiếp nhận không có ai quản | Chỉ Người có toàn quyền; chọn bản ghi ở lại hay chuyển; nới rộng, soạn sẵn trong Phiên triển khai | `BR-35.12` |
| C.74 | Tạm ngưng Người phụ trách thanh toán: IAM tự chuyển chức danh, billing đòi chỉ định người thay trước | *(Đã được thay bởi C.77)* Chỉ định người thay ngay trong thao tác tạm ngưng, Chủ sở hữu được chọn sẵn | `FEAT-42` |
| C.75 | Mức đặt sẵn che thao tác đặc thù hoặc âm thầm mở nó | Thao tác đặc thù là dòng riêng ở chế độ Cơ bản, không bị mức đặt sẵn đổi | `BR-25.4` |
| C.76 | Lời mời Chờ gửi được onboarding đặt ra ngoài vòng đời lời mời của IAM | Trạng thái và đường chuyển đặc tả tại IAM | `BR-09.11` |
| C.77 | Chỉ định người thay Người phụ trách thanh toán trong lượt tạm ngưng có thể chặn tạm ngưng (thiếu thẩm quyền, nhật ký lỗi) | Tạm ngưng không bao giờ bị chặn; chức danh tạm về Chủ sở hữu; chỉ định người khác là bước tuỳ chọn, thất bại không ảnh hưởng tạm ngưng. `billing-subscription-srs.md` `BR-03.6` được sửa cùng đợt: gỡ hoặc xoá vẫn bắt buộc có người thay, tạm ngưng hoặc khoá thì không | `FEAT-42`, `FEAT-14` |
| C.78 | Chọn mức đặt sẵn làm Xem hẹp hơn thao tác đặc thù, vi phạm `BR-25.1` | Thao tác đặc thù thu hẹp theo, đánh dấu trước khi lưu | `BR-25.4` |
| C.79 | Xoá đơn vị tiếp nhận lách quy tắc đổi đơn vị tiếp nhận | Chặn xoá khi là đơn vị tiếp nhận hoặc còn bản ghi công việc | `FEAT-23` |
| C.80 | *(Phần người tạo chọn Đơn vị chính của mình đã được thay bởi C.95)* Không quy định ai đặt đơn vị tiếp nhận lần đầu; biểu mẫu mới dễ vào nhầm hàng đợi Hỗ trợ | Đơn vị tiếp nhận mặc định theo loại nguồn do Người có toàn quyền đặt; người tạo nguồn chỉ chọn mặc định hoặc đơn vị của mình | `BR-35.12`, `CFG-35-01` |
| C.81 | Trưởng nhóm Marketing chỉ có vai trò Quản lý dùng chung, bật Phát sóng cho trưởng nhóm Marketing là bật cho mọi trưởng nhóm | Thêm vai trò dựng sẵn Quản lý Marketing | `FEAT-29` |
| C.82 | Nhà cung cấp không có đường hợp lệ để chuyển dữ liệu cũ cho khách hàng | Lô nhập soạn sẵn trong Phiên triển khai, chạy khi Người có toàn quyền xác nhận | `BR-08.7` |
| C.83 | "Trả về hàng đợi" cho khách hàng tiềm năng không có định nghĩa; nhập tệp luôn đứng tên người nhập làm hỏng lô chuyển dữ liệu | Định nghĩa trả về hàng đợi hiện tại của nguồn; tệp ánh xạ được cột Người phụ trách | `BR-35.13`, `BR-35.10` |
| C.84 | Lô nhập soạn sẵn để lộ dữ liệu hiện có qua đối chiếu trùng; không quy định lưu tệp gốc | Đối chiếu trùng chỉ khi Người có toàn quyền xác nhận; tệp gốc xoá sau khi chạy hoặc huỷ | `BR-08.7` |
| C.85 | Vai trò Quản lý Marketing lệch định nghĩa của campaigns | Phát sóng và Nhập, Sửa, Gán ở mức Đơn vị của mình | `FEAT-29` |
| C.86 | Lời mời đang chờ của người đã rời không có ai chịu trách nhiệm | Xử lý trong quy trình rời workspace; còn sót thì thu hồi khi gỡ | `BR-13.1`, `FEAT-43` |
| C.87 | "Tạm ngưng ngay" trong quy trình rời làm bước bàn giao chức danh tự hoàn tất, lách yêu cầu chỉ định người thay | Đối tượng của mỗi bước xác định lúc bắt đầu; chức danh phải được chỉ định tường minh; Người phụ trách thanh toán chuyển cho Chủ sở hữu | `FEAT-43` |
| C.88 | Trả về hàng đợi đưa bản ghi công việc sang đơn vị tiếp nhận mới, lách quy tắc "ở lại" (khác với chuyển hàng đợi có chủ đích theo `BR-35.14`) | Bản ghi công việc về hàng đợi của đơn vị nó đang thuộc | `BR-35.13` |
| C.89 | Nguồn chưa có đơn vị tiếp nhận: "chưa nhận bản ghi" trái với "chỉ người toàn workspace thấy" | Không công khai hay kết nối được cho tới khi có đơn vị tiếp nhận; quy tắc toàn workspace chỉ cho nguồn ngoài hàng đợi | `BR-35.11`, `BR-35.12` |
| C.90 | Mặc định chung theo loại nguồn dồn hàng đợi của mọi chi nhánh về một nơi | Ghi đè mặc định theo đơn vị, tìm từ Đơn vị chính của người tạo lên gốc | `BR-35.12`, `CFG-35-01` |
| C.91 | Lô nhập gán Người phụ trách cho người chưa chấp nhận lời mời; giá trị không hợp lệ không có quy tắc | Bản ghi chờ phân công tự gán khi chấp nhận; giá trị không hợp lệ là dòng lỗi; có cột Đơn vị tiếp nhận | `BR-35.10` |
| C.92 | Lời mời của người bị tạm ngưng vẫn chấp nhận được trong khi liên kết của họ mất hiệu lực | Lời mời tạm treo | `BR-42.6` |
| C.93 | Bản ghi gán cho người chưa chấp nhận lời mời bị đồng nghiệp giành và lộ cho cả đội | Giữ chỗ: không ai nhận được, chỉ người phụ trách đơn vị và quản lý thấy; lô chạy sau khi lời mời đã gửi | `BR-35.10`, `BR-08.7` |
| C.94 | Không chuyển được từng vé giữa các hàng đợi | Chuyển chịu ô Gán; chi tiết do phân hệ | `BR-35.14` |
| C.95 | Người tạo nguồn chọn Đơn vị chính của mình làm đơn vị tiếp nhận là tự nới phạm vi | Chỉ dùng mặc định tìm được; đơn vị khác cần Người có toàn quyền | `BR-35.12` |
| C.96 | Rời workspace bế tắc ở bước chức danh (Chủ sở hữu tự thực hiện, workspace hai người) | Chủ sở hữu miễn `BR-43.4`; chức danh khác Người phụ trách thanh toán được để trống | `BR-43.4`, `FEAT-43` |
| C.97 | Cảnh báo Nghiêm trọng "đơn vị chưa có người phụ trách" bật ngay sau thiết lập chuẩn | Hạ xuống Nhẹ trừ khi `CFG-34-02` bật hoặc là đơn vị tiếp nhận | `FEAT-03` |
| C.98 | Bản ghi giữ chỗ nằm ngoài thứ tự hợp nhất quyền, có thể lộ ra cho cả đội | Thêm vào bước Nguồn chặn của `BR-39.6` với danh sách ngoại lệ tường minh; gán lại chấm dứt giữ chỗ; gửi lại lời mời giữ nguyên giữ chỗ | `BR-39.6`, `BR-35.10`, `BR-42.6` |
| C.99 | Sửa Đơn vị chính của lời mời không có luồng và kéo được bản ghi giữ chỗ về đơn vị người sửa; "gửi lại" mâu thuẫn giữa `FEAT-09` và `BR-42.6`; nhóm ngoại lệ giữ chỗ không rõ là nguồn nới hay chỉ không bị chặn | `BR-09.12` xét năng lực trên cả giá trị cũ và mới; gửi lại luôn là cùng lời mời, tính lại hạn; nhóm ngoại lệ chỉ không bị chặn; mốc nhắc thành `CFG-09-04`; gỡ thành viên đang chờ thả bản ghi về hàng đợi | `BR-09.12`, `BR-35.10`, `BR-39.6`, `BR-42.6`, `CFG-09-04` |
| C.100 | Các SRS phân hệ đặt mặc định khác bảng vai trò dựng sẵn (Xuất của Nhân viên Kinh doanh, thao tác đặc thù của Marketing, Công việc của Marketing, Xuất vé của Quản lý); không rõ `CFG-29-02` có áp cho quyền quản trị của phân hệ | Ma trận chi tiết do phân hệ khai báo là mặc định có hiệu lực, lệch phải nêu lý do; `CFG-29-02` áp cho mọi ô và quyền quản trị của phân hệ | `BR-29.6`, `BR-29.4` |
| C.101 | Ô Gán mức Chỉ của mình vừa để nhận việc vừa có thể hiểu là giao bản ghi của mình cho bất kỳ ai | Đích phải nằm trong mức của chính người gán; Chỉ của mình chỉ nhận về mình | `BR-25.6` |
| C.102 | Loại nguồn của đơn vị tiếp nhận chỉ nêu biểu mẫu và kênh; nhập tệp bản ghi công việc để trống mọi cột gán cho người nhập | Loại nguồn do phân hệ khai báo; bản ghi công việc nhập vào hàng đợi đơn vị chọn cho lô | `CFG-35-01`, `BR-35.10` |
| C.103 | Hội thoại đang mở của người bị tạm ngưng có thể bị "giữ nguyên" trong khi khách đang chờ; bot theo kịch bản bị coi là Tác nhân AI | Phân hệ khai báo loại cần phản hồi tức thời, tự trả về hàng đợi; định nghĩa Tác nhân AI theo việc dùng mô hình AI | `BR-42.4`, Mục 1.4 |
| C.104 | `BR-25.6` chặn Người phụ trách có Gán = Chỉ của mình bàn giao vé, hội thoại, khách hàng của mình — thao tác hằng ngày của mọi phân hệ; chuyển hàng đợi với Gán = Chỉ của mình mâu thuẫn quy tắc đích | Hai đường luôn có: trả về hàng đợi và đề nghị chuyển cần người nhận chấp nhận bằng điều kiện nhận việc của chính họ; chuyển hàng đợi theo `BR-35.14` là ngoại lệ tường minh; hạn đề nghị `CFG-25-01` | `BR-25.6`, `BR-35.14`, `CFG-25-01` |
| C.105 | Khi kích hoạt lại, "không gồm bản ghi của hàng đợi" khiến vé đã chuyển cho người xử lý thay không bao giờ được đề xuất trả về | Chỉ loại bản ghi đã trả về hàng đợi hay đã được người khác nhận | `BR-42.4` |
| C.106 | Đổi đơn vị không có lựa chọn cho bản ghi công việc khi người phụ trách vẫn kiêm nhiệm đơn vị tiếp nhận; hạn đề nghị chuyển tối thiểu 1 giờ quá dài cho hội thoại trực tiếp | Thêm "giữ Người phụ trách" có điều kiện; hạn tối thiểu 5 phút, phân hệ khai báo mặc định riêng | `BR-11.5`, `CFG-25-01` |
| C.107 | Nguồn tạo công việc liên hệ lại "chạy thay" người khởi chạy chiến dịch nhưng không tạm dừng khi người đó nghỉ, trái quy tắc tiến trình chạy thay | Ngoại lệ tường minh cho nguồn tạo bản ghi vào hàng đợi; ghi người tạo là Hệ thống khi người khởi chạy không còn hoạt động; loại khỏi bước rời workspace | `BR-35.8`, `FEAT-43` |
| C.108 | Kiêm nhiệm hết hạn chỉ đề xuất trả về, để hội thoại trực tiếp treo dưới tên người không còn thuộc đội; vé giữ chỗ từ tệp vừa thuộc Đơn vị chính trong lời mời vừa thuộc hàng đợi của lô | Loại cần phản hồi tức thời tự trả về; bản ghi công việc giữ chỗ thuộc đơn vị tiếp nhận, lời mời phải ghi đơn vị đó | `BR-11.10`, `BR-35.10` |
| C.109 | Bản ghi công việc giữ chỗ vẫn bị xét quyền và chuyển theo Đơn vị chính trong lời mời, lộ vé sang đơn vị khác | Xét theo đơn vị mà bản ghi giữ chỗ đang thuộc; bản ghi công việc ở lại đơn vị tiếp nhận; lời mời bỏ đơn vị đó thì giữ chỗ chấm dứt | `BR-35.10`, `BR-39.6` |
