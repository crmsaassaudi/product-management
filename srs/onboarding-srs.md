# SRS — Tiếp nhận Khách hàng & Khởi tạo Không gian làm việc (Onboarding & Workspace Provisioning)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA |
| **Module** | CRM — Phân hệ Tiếp nhận Khách hàng & Khởi tạo Không gian làm việc |
| **Ngày cập nhật** | 2026-10-02 |
| **Phiên bản** | v3.0 (Chuẩn hóa Nghiệp vụ Thuần túy — thay thế v2.0) |
| **Neo mã nguồn** | Chưa xác định — tài liệu đặc tả trạng thái nghiệp vụ mục tiêu, không neo vào một phiên bản triển khai cụ thể |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`billing-subscription-srs.md`](./billing-subscription-srs.md), [`contacts-srs.md`](./contacts-srs.md), [`deals-pipeline-srs.md`](./deals-pipeline-srs.md), [`tickets-srs.md`](./tickets-srs.md) |

## Ghi chú về phiên bản v3.0

Phiên bản này viết lại toàn bộ tài liệu theo đúng vai trò của một SRS nghiệp vụ, thay thế v2.0:

1. **Tài liệu là chuẩn, không phải bản ghi chép hiện trạng.** Đặc tả trạng thái nghiệp vụ mục tiêu; mọi quy tắc ở Mục 3 là yêu cầu bắt buộc như nhau; nơi nào hệ thống làm khác, hệ thống phải sửa theo tài liệu. Không dùng nhãn trạng thái triển khai.
2. **Ranh giới rõ với tài liệu khác.** Chủ sở hữu, vai trò dựng sẵn, lời mời, mức quyền tối thiểu, sức khỏe cấu hình, trần quyền và chuyển nhượng quyền sở hữu thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md). Gói dùng thử, hạn mức, hạ gói thuộc [`billing-subscription-srs.md`](./billing-subscription-srs.md). Tài liệu này dẫn chiếu tới đó.
3. **Tập trung vào thời gian tới giá trị:** đăng ký ngắn, xác minh danh tính, khởi tạo toàn vẹn, tự vào workspace không đăng nhập lại, thiết lập đội ngũ nhanh ngay lần đăng nhập đầu, thành viên mới chấp nhận lời mời và vào việc ngay.
4. Mã `FEAT` được đánh lại vì không tài liệu nào khác dẫn chiếu mã của v2.0; Phụ lục C ghi ánh xạ.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả nghiệp vụ cho hành trình từ khi một doanh nghiệp bắt đầu đăng ký (hoặc được nhà cung cấp tạo hộ) tới khi cả đội của họ đã vào workspace và làm việc được: đăng ký và xác minh, khai báo doanh nghiệp, đặt tên workspace, khởi tạo, cấu hình khởi điểm theo ngành, lần đăng nhập đầu, thiết lập đội ngũ, mời và chào mừng thành viên, theo dõi dùng thử và thông điệp hướng dẫn.

### 1.2 Phạm vi

**Trong phạm vi — 8 nhóm chức năng:**

| Nhóm | Nội dung |
| --- | --- |
| A. Đăng ký & Danh tính | Đăng ký tài khoản, xác minh email, đăng ký bằng tài khoản Google/Microsoft, tiếp tục đăng ký dở dang, dọn đăng ký bỏ dở |
| B. Hồ sơ doanh nghiệp | Thông tin doanh nghiệp, quy mô, ngành, mục tiêu; đề xuất ngôn ngữ, múi giờ, tiền tệ; tín hiệu khách hàng lớn cho đội kinh doanh của nhà cung cấp |
| C. Định danh workspace | Tên miền phụ; tên miền riêng của doanh nghiệp; xác minh tên miền email doanh nghiệp |
| D. Khởi tạo | Khởi tạo workspace toàn vẹn; cấu hình khởi điểm theo ngành; dữ liệu mẫu |
| E. Lần đăng nhập đầu & Thiết lập nhanh | Chào mừng và lộ trình thiết lập nhanh; thiết lập đội ngũ nhanh; hướng dẫn tương tác |
| F. Thành viên mới | Thư mời mang nhận diện doanh nghiệp; chấp nhận lời mời và chào mừng thành viên |
| G. Tạo hộ khách hàng doanh nghiệp | Cổng tạo hộ của nhà cung cấp và kích hoạt Chủ sở hữu |
| H. Dùng thử, Nuôi dưỡng & Nhiều workspace | Hiển thị trạng thái dùng thử; thông điệp hướng dẫn kích hoạt; tạo thêm workspace từ tài khoản đã có |

**Ngoài phạm vi (dẫn chiếu):**

- Chủ sở hữu, lời mời và vòng đời lời mời, vai trò, đơn vị, nhóm, sức khỏe cấu hình, trần quyền, chuyển nhượng và khôi phục quyền sở hữu, chuyển giữa nhiều workspace — [`iam-tenant-authorization.md`](./iam-tenant-authorization.md).
- Gói dùng thử, thời hạn và hạn mức dùng thử, thông báo bắt buộc trước khi hết dùng thử, chuyển đổi trả phí, hạ gói, hạn mức người dùng, chống dùng thử lặp lại, tính năng theo gói — [`billing-subscription-srs.md`](./billing-subscription-srs.md) `FEAT-01`, `FEAT-05`, `FEAT-06`, `FEAT-11`.
- Nhập danh bạ, cấu hình kênh, phễu bán hàng chi tiết — các SRS phân hệ tương ứng; tài liệu này chỉ dẫn người dùng tới đó.
- Chính sách mật khẩu, xác thực nhiều lớp, đăng nhập một lần doanh nghiệp — phần xác thực, ngoài phạm vi. Tài liệu này chỉ quy định danh tính phải được xác minh trước khi tạo workspace.

### 1.3 Đối tượng đọc

- Product Owner / Business Analyst: chuẩn nghiệp vụ và chỉ số kích hoạt.
- QA: căn cứ viết test case từ bảng Tiêu chí Chấp nhận và Mục 6.
- Customer Success / Kinh doanh của nhà cung cấp: hiểu hành trình khách hàng và các điểm can thiệp.
- Kỹ sư phát triển: hiểu ý định nghiệp vụ; chi tiết kỹ thuật không nằm trong tài liệu này.

### 1.4 Thuật ngữ nghiệp vụ

| Thuật ngữ | Ý nghĩa |
| --- | --- |
| **Workspace** | Không gian làm việc riêng của một doanh nghiệp, dữ liệu tách biệt hoàn toàn. Định nghĩa chuẩn tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4. |
| **Người đăng ký** | Người đang thực hiện đăng ký tự phục vụ, chưa có workspace. |
| **Đăng ký dở dang** | Tài khoản đã tạo ở bước đầu nhưng chưa hoàn tất tạo workspace. |
| **Tên miền phụ** | Địa chỉ riêng của workspace dưới tên miền chung của nền tảng, duy nhất toàn hệ thống. |
| **Tên miền riêng** | Tên miền thuộc sở hữu của doanh nghiệp, được trỏ về workspace. |
| **Giữ chỗ tên miền phụ** | Khoảng thời gian một tên miền phụ được giữ cho người đăng ký trong lúc chưa hoàn tất. |
| **Khởi tạo** | Toàn bộ việc tạo workspace: danh tính, workspace, Chủ sở hữu, khung phân quyền, cấu hình khởi điểm, kết nối các dịch vụ đi kèm (ví dụ không gian trợ lý hội thoại tự động). |
| **Sẵn sàng** | Trạng thái workspace dùng được, sau khi mọi điều kiện của `BR-08.1` thỏa. |
| **Mẫu ngành** | Bộ cấu hình khởi điểm (phễu bán hàng, quy trình vé hỗ trợ, dữ liệu mẫu, gợi ý đơn vị) cho một ngành. |
| **Dữ liệu mẫu** | Bản ghi minh hoạ được nạp để người dùng hình dung sản phẩm; luôn được đánh dấu là mẫu. |
| **Lộ trình thiết lập nhanh** | Danh sách nhiệm vụ đầu tiên giúp workspace đạt trạng thái kích hoạt. |
| **Đạt kích hoạt sử dụng** | Workspace đạt đồng thời: ít nhất 3 thành viên Đang hoạt động (tính cả Chủ sở hữu); ít nhất một kênh liên lạc đã kết nối hoặc ít nhất một khách hàng không phải dữ liệu mẫu; ít nhất một cơ hội hoặc vé hỗ trợ không phải dữ liệu mẫu. Khác với "kích hoạt Chủ sở hữu" (chấp nhận lời mời ở kênh tạo hộ). |
| **Tên miền email đã xác minh** | Tên miền email (ví dụ congty.vn) mà workspace đã chứng minh sở hữu theo `FEAT-20`; khác với tên miền riêng dùng để truy cập workspace (`FEAT-07`). |
| **Tạo hộ** | Kênh nhà cung cấp tạo workspace cho khách hàng doanh nghiệp theo hợp đồng. |
| **Người có toàn quyền** | Chủ sở hữu hoặc Quản trị viên của workspace ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4). |
| **Tham số nền tảng** | Giá trị thuộc chính sách thương mại hoặc vận hành của nhà cung cấp, áp chung cho mọi workspace, do nhà cung cấp cấu hình (Phụ lục B.2). Khác với tham số workspace do doanh nghiệp cấu hình (Phụ lục B.1). |

### 1.5 Tài liệu tham khảo

- [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) — `FEAT-01` (Chủ sở hữu), `FEAT-02` (bất biến phân quyền khi sẵn sàng), `FEAT-04` (cấu hình chung), `FEAT-08` (phiên hỗ trợ của nhà cung cấp), `FEAT-09` (lời mời), `FEAT-21` (đơn vị và vai trò gợi ý), `FEAT-29` (vai trò dựng sẵn), `FEAT-34` (mức nền), `FEAT-44` (thao tác hàng loạt), `FEAT-50` (liên kết mời), `FEAT-51` (nhiều workspace).
- [`billing-subscription-srs.md`](./billing-subscription-srs.md) — `FEAT-01` (gói và tính năng theo gói), `FEAT-03` (Người phụ trách thanh toán), `FEAT-05` (dùng thử và chuyển đổi), `FEAT-11` (tính phí theo người dùng).

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà phân hệ giải quyết

1. **Thời gian tới giá trị dài:** mỗi bước thừa, mỗi lần đăng nhập lại, mỗi màn hình trống làm khách hàng bỏ dở trước khi thấy giá trị.
2. **Workspace một người:** CRM chỉ có giá trị khi cả đội dùng; nếu việc mời và phân quyền khó, Chủ sở hữu dùng một mình rồi bỏ.
3. **Workspace hỏng nửa vời:** khởi tạo thất bại giữa chừng để lại tài khoản, tên miền phụ bị chiếm và người dùng không biết làm gì tiếp.
4. **Danh tính không đáng tin:** đăng ký bằng email không xác minh mở đường cho giả mạo, chiếm email của người khác và lạm dụng dùng thử.
5. **Cấu hình khởi điểm không hợp ngành:** phễu và dữ liệu chung chung buộc khách hàng cấu hình lại từ đầu.

### 2.2 Vai trò người dùng

**Vai trò thao tác (là các cột của Ma trận tại Mục 5):**

| Vai trò | Trách nhiệm nghiệp vụ |
| --- | --- |
| **Người đăng ký** | Tạo tài khoản, xác minh email, khai báo doanh nghiệp, đặt tên miền phụ, tạo workspace. Trở thành Chủ sở hữu khi workspace Sẵn sàng. |
| **Chủ sở hữu** | Hoàn tất thiết lập nhanh, thiết lập đội ngũ, quyết định dữ liệu mẫu, cấu hình tên miền riêng, theo dõi dùng thử. |
| **Người có quyền quản trị liên quan** | Quản trị viên, hoặc thành viên được cấp quyền quản trị tương ứng theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-24` (ví dụ Mời người dùng, Quản lý cấu hình workspace). |
| **Thành viên được mời** | Nhận thư mời, chấp nhận, vào làm việc. |
| **Người phụ trách thanh toán** | Nhận thông tin dùng thử và thực hiện nâng cấp, theo [`billing-subscription-srs.md`](./billing-subscription-srs.md). |
| **Nhân sự vận hành nền tảng** | Tạo hộ workspace, cấu hình tham số nền tảng, nhận tín hiệu khách hàng lớn, thẩm định khiếu nại tên miền email. Gồm cả **nhân sự triển khai** được giao cho một khách hàng tạo hộ (`BR-16.4`). |
| **Hệ thống** | Khởi tạo, thử lại và dọn dẹp, nạp cấu hình khởi điểm, gửi thông điệp hướng dẫn, dọn đăng ký bỏ dở. |

### 2.3 Quy ước thời gian nghiệp vụ

- **Trước khi workspace Sẵn sàng** (mã xác minh, giữ chỗ tên miền phụ, dọn đăng ký bỏ dở): thời hạn tính bằng phút hoặc giờ, trôi liên tục kể từ sự kiện, không phụ thuộc múi giờ.
- **Sau khi Sẵn sàng:** mọi mốc ngày (ngày còn lại của dùng thử, lịch thông điệp hướng dẫn) theo **múi giờ của workspace** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-04`). Múi giờ này được đặt ngay trong lúc đăng ký (`FEAT-04` của tài liệu này).
- **Mốc hết dùng thử** do [`billing-subscription-srs.md`](./billing-subscription-srs.md) quy định; tài liệu này chỉ hiển thị nó theo múi giờ workspace.
- **Thông điệp hướng dẫn** gửi vào giờ gửi của `PLT-09` (mặc định 09:00) theo múi giờ workspace, vào ngày làm việc theo lịch làm việc của workspace (lịch mặc định theo quốc gia, `PLT-06`). Mốc "N ngày trước khi hết dùng thử" tính tương đối với mốc hết dùng thử. Mốc rơi vào ngày nghỉ được dời về **ngày làm việc liền trước**; mốc rơi sau ngày hết dùng thử hoặc trước ngày Sẵn sàng bị bỏ; hai mốc rơi cùng ngày được gộp thành một thư. Thông điệp của ngày Sẵn sàng gửi ngay khi workspace Sẵn sàng, không chờ giờ gửi.

**Lý do nghiệp vụ:** khách hàng ở Riyadh và Hà Nội phải nhận thông điệp vào giờ làm việc của họ, và thông điệp "còn 2 ngày" phải đúng dù độ dài dùng thử thay đổi theo chiến dịch.

### 2.4 Nguyên tắc nghiệp vụ nền tảng

**Nguyên tắc 1 — Mỗi bước phải xứng đáng.** Chỉ hỏi điều không suy ra được và cần ngay; mọi trường khác có mặc định hợp lý và sửa được về sau.

**Nguyên tắc 2 — Danh tính trước, workspace sau.** Không workspace nào được tạo từ một email chưa xác minh.

**Nguyên tắc 3 — Hoặc Sẵn sàng đầy đủ, hoặc không để lại dấu vết.** Khởi tạo thất bại phải dọn mọi phần đã tạo, trả tên miền phụ về sau thời gian giữ chỗ, và nói rõ cho người dùng điều gì đã xảy ra.

**Nguyên tắc 4 — Dữ liệu mẫu không bao giờ lẫn vào dữ liệu thật.** Dữ liệu mẫu luôn được đánh dấu, không tính vào báo cáo, chỉ số, hạn mức và tiêu dùng tính phí, và xoá được mà không động tới dữ liệu thật.

**Nguyên tắc 5 — Đội ngũ là mốc kích hoạt chính.** Lộ trình thiết lập đặt việc đưa đội ngũ vào workspace lên đầu; việc mời phải xong được trong vài phút với mặc định đúng.

**Nguyên tắc 6 — Mọi quyền theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md).** Tài liệu này không tự định nghĩa vai trò, mức quyền hay quy tắc cấp quyền nào; mọi thao tác trên người và quyền dùng đúng quy tắc của tài liệu đó.

### 2.5 Bảng tổng hợp tính năng nghiệp vụ

| Nhóm | Mã | Tên tính năng nghiệp vụ |
| --- | --- | --- |
| **A. Đăng ký & Danh tính** | `FEAT-01` | Đăng ký tài khoản & xác minh danh tính |
| | `FEAT-02` | Tiếp tục đăng ký dở dang & dọn đăng ký bỏ dở |
| **B. Hồ sơ doanh nghiệp** | `FEAT-03` | Khai báo doanh nghiệp, quy mô, ngành & mục tiêu |
| | `FEAT-04` | Đề xuất ngôn ngữ, múi giờ & tiền tệ |
| | `FEAT-05` | Tín hiệu khách hàng lớn cho đội kinh doanh của nhà cung cấp |
| **C. Định danh workspace** | `FEAT-06` | Tên miền phụ của workspace |
| | `FEAT-07` | Tên miền riêng của doanh nghiệp |
| | `FEAT-20` | Xác minh tên miền email doanh nghiệp |
| **D. Khởi tạo** | `FEAT-08` | Khởi tạo workspace toàn vẹn |
| | `FEAT-09` | Cấu hình nghiệp vụ khởi điểm theo ngành |
| | `FEAT-10` | Dữ liệu mẫu & xoá dữ liệu mẫu |
| **E. Lần đăng nhập đầu & Thiết lập nhanh** | `FEAT-11` | Chào mừng & lộ trình thiết lập nhanh |
| | `FEAT-12` | Thiết lập đội ngũ nhanh |
| | `FEAT-13` | Hướng dẫn tương tác theo màn hình |
| **F. Thành viên mới** | `FEAT-14` | Thư mời mang nhận diện doanh nghiệp |
| | `FEAT-15` | Chấp nhận lời mời & chào mừng thành viên mới |
| **G. Tạo hộ** | `FEAT-16` | Tạo hộ workspace cho khách hàng doanh nghiệp |
| **H. Dùng thử, Nuôi dưỡng & Nhiều workspace** | `FEAT-17` | Hiển thị trạng thái dùng thử & lối nâng cấp |
| | `FEAT-18` | Thông điệp hướng dẫn kích hoạt |
| | `FEAT-19` | Tạo thêm workspace từ tài khoản đã có |

**Tổng kết phạm vi:** 20 tính năng, tất cả là yêu cầu bắt buộc. `FEAT-20` được trình bày trong Nhóm C theo nội dung nghiệp vụ.

### 2.6 Mục tiêu kinh doanh & Chỉ số thành công

Đo hằng tháng trên các workspace tạo trong kỳ; chủ sở hữu chỉ số là Product Owner của phân hệ. Mỗi chỉ số cần sự kiện đo tương ứng được ghi nhận (`NFR-08`).

| Mã | Vấn đề (Mục 2.1) | Chỉ số | Mục tiêu | Tính năng đóng góp |
| --- | --- | --- | --- | --- |
| `KPI-01` | 1 | Trung vị thời gian từ lúc bắt đầu đăng ký tới khi người đăng ký ở bên trong workspace Sẵn sàng | ≤ 3 phút | `FEAT-01`, `FEAT-03`, `FEAT-06`, `FEAT-08` |
| `KPI-02` | 1 | Tỷ lệ người bắt đầu đăng ký tới được workspace Sẵn sàng | ≥ 70% | Nhóm A – D |
| `KPI-03` | 3 | Tỷ lệ khởi tạo thành công ở lần đầu | ≥ 99,5% | `FEAT-08` |
| `KPI-04` | 2 | Tỷ lệ workspace tự đăng ký có ít nhất 3 thành viên Đang hoạt động trong 7 ngày | ≥ 40% | `FEAT-11`, `FEAT-12`, `FEAT-14`, `FEAT-15` |
| `KPI-05` | 2 | Trung vị thời gian từ khi Sẵn sàng tới lời mời đầu tiên (cùng định nghĩa với [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `KPI-07`) | ≤ 10 phút | `FEAT-12` |
| `KPI-06` | 1, 5 | Tỷ lệ workspace Đạt kích hoạt sử dụng trong 14 ngày | ≥ 35% | Nhóm D, E |
| `KPI-07` | 4 | Tỷ lệ workspace bị phát hiện đăng ký lạm dụng sau khi đã Sẵn sàng | ≤ 0,5% | `FEAT-01`, `FEAT-19` |

### 2.7 Luồng nghiệp vụ đầu – cuối

1. **Đăng ký:** nhập email hoặc dùng tài khoản Google/Microsoft → xác minh email (`FEAT-01`).
2. **Khai báo:** tên doanh nghiệp, quy mô, ngành, mục tiêu trên một màn hình; ngôn ngữ, múi giờ, tiền tệ được đề xuất sẵn; tên miền phụ được gợi ý sẵn (`FEAT-03`, `FEAT-04`, `FEAT-06`).
3. **Khởi tạo:** người dùng xác nhận → khởi tạo toàn vẹn → tự vào workspace, không đăng nhập lại (`FEAT-08`) → cấu hình theo ngành và dữ liệu mẫu có sẵn (`FEAT-09`, `FEAT-10`).
4. **Thiết lập nhanh:** chào mừng → thiết lập đội ngũ: chọn đội gợi ý, dán email theo đội, gửi (`FEAT-11`, `FEAT-12`).
5. **Thành viên vào việc:** thư mời mang tên doanh nghiệp → chấp nhận → vào thẳng màn hình làm việc với đúng vai trò (`FEAT-14`, `FEAT-15`).
6. **Dùng thử và nâng cấp:** trạng thái dùng thử hiển thị cho đúng người; thông điệp hướng dẫn theo hành vi (`FEAT-17`, `FEAT-18`).
7. **Kênh tạo hộ:** nhà cung cấp tạo workspace, Chủ sở hữu kích hoạt, tiếp tục từ bước 4 (`FEAT-16`).

---

## 3. Đặc tả yêu cầu chức năng

## A. ĐĂNG KÝ & DANH TÍNH

### FEAT-01 — Đăng ký tài khoản & xác minh danh tính

**Mô tả nghiệp vụ:** Người đăng ký tạo tài khoản bằng email và mật khẩu, hoặc bằng tài khoản Google/Microsoft, và xác minh quyền sở hữu email trước khi được tạo workspace.

**Vai trò sử dụng chính:** Người đăng ký.

**Điều kiện tiên quyết:** Không có.

**Luồng chính — email và mật khẩu:**

1. Nhập họ tên, email, mật khẩu.
2. Hệ thống gửi **mã xác minh** tới email; người đăng ký tiếp tục sang bước khai báo doanh nghiệp ngay, không phải chờ.
3. Nhập mã trước khi xác nhận tạo workspace (`FEAT-08`).

**Luồng chính — tài khoản Google/Microsoft:**

1. Chọn đăng ký bằng Google hoặc Microsoft, cho phép chia sẻ họ tên, email, ảnh đại diện.
2. Email được coi là đã xác minh khi thỏa `BR-01.5`; người đăng ký sang thẳng bước khai báo doanh nghiệp.

**Luồng ngoại lệ:**

- Email đã có tài khoản **đã xác minh** → người đăng ký đi tiếp sang màn hình khai báo giống hệt email mới; khác biệt duy nhất nằm trong thư gửi tới địa chỉ đó: thay vì mã xác minh, thư chứa lối đăng nhập và lối tạo workspace mới theo `FEAT-19` (`BR-01.6`).
- Email đã có tài khoản **chưa xác minh** → không chặn; người chứng minh được email (nhập đúng mã, hoặc qua nhà cung cấp danh tính thỏa `BR-01.5`) được chọn **Tiếp tục đăng ký đang dở** hoặc **Bắt đầu lại**; chỉ khi bắt đầu lại thì thông tin đã khai trước đó bị huỷ (`BR-01.7`).
- Người đăng ký gõ sai email → sửa được email ngay trên màn hình chờ mã trước khi xác minh; mã cũ mất hiệu lực.
- Không nhận được mã → gửi lại được sau khoảng chờ `PLT-11`, tối đa theo ngưỡng `PLT-11`.
- Mật khẩu không đạt chính sách độ mạnh của nền tảng → báo ngay trên ô nhập, nêu rõ điều chưa đạt.
- Mã xác minh sai → báo sai, cho nhập lại; lần sai thứ 5 → mã bị huỷ, gửi được mã mới.
- Mã hết hạn (`PLT-01`) → gửi lại mã mới; mã cũ mất hiệu lực.
- Nhà cung cấp danh tính không bảo đảm email đã xác minh theo `BR-01.5` → yêu cầu xác minh bằng mã như luồng email.
- Doanh nghiệp có dấu hiệu dùng thử lặp lại ([`billing-subscription-srs.md`](./billing-subscription-srs.md) `BR-05.7`) → workspace vẫn được tạo theo `BR-08.6`; người dùng thấy thông báo rõ ràng.
- Email đăng ký bằng Google/Microsoft trùng với một tài khoản **đã xác minh** có mật khẩu → không tạo tài khoản mới; yêu cầu đăng nhập bằng mật khẩu một lần để liên kết hai cách đăng nhập. Trùng với tài khoản **chưa xác minh** → xử lý theo `BR-01.7`, không đòi mật khẩu.
- Số lượt đăng ký từ cùng một nguồn vượt ngưỡng chống lạm dụng của nền tảng → yêu cầu bước kiểm tra người thật trước khi tiếp tục.

**Quy tắc nghiệp vụ:**

- **`BR-01.1` (Một email, một tài khoản):** Theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-01.2`.

  **Lý do nghiệp vụ:** một danh tính duy nhất cho phép một người làm việc với nhiều workspace mà không tạo trùng.

- **`BR-01.2` (Xác minh trước khi tạo workspace):** Không workspace nào được tạo khi email của người đăng ký chưa xác minh.

  **Lý do nghiệp vụ:** không xác minh thì bất kỳ ai cũng tạo được workspace bằng email của người khác, chiếm email đó, gửi thư mời mang danh doanh nghiệp, và lạm dụng dùng thử.

- **`BR-01.3` (Xác minh không chặn việc khai báo):** Người đăng ký điền tiếp các bước trong lúc chờ mã; mã chỉ cần trước bước xác nhận tạo workspace.

  **Lý do nghiệp vụ:** chờ email về là khoảng chết; tận dụng nó rút ngắn thời gian đăng ký (`KPI-01`).

- **`BR-01.4` (Chính sách mật khẩu thuộc phần xác thực):** Độ mạnh mật khẩu theo chính sách xác thực của nền tảng, đánh giá theo độ mạnh thực tế (độ dài, mức phổ biến) thay vì bắt buộc đủ từng loại ký tự.

  **Lý do nghiệp vụ:** ép đủ loại ký tự làm người dùng chọn mật khẩu dễ đoán kiểu "Abc@1234" và tăng bỏ dở; độ dài và kiểm tra mật khẩu phổ biến bảo vệ tốt hơn.

- **`BR-01.5` (Tin email từ nhà cung cấp danh tính có điều kiện):** Email nhận từ nhà cung cấp danh tính chỉ được coi là đã xác minh khi nhà cung cấp đó bảo đảm quyền sở hữu email — tài khoản email do chính nhà cung cấp cấp (ví dụ địa chỉ Gmail), hoặc tên miền email đã được tổ chức sở hữu xác minh với nhà cung cấp. Trường hợp còn lại phải nhập mã.

  **Lý do nghiệp vụ:** ở một số nhà cung cấp danh tính doanh nghiệp, quản trị viên tự đặt được trường email cho người dùng của mình; tin tuyệt đối thì kẻ gian tự đặt email x@congty.vn rồi vào đúng workspace của congty.vn.

- **`BR-01.6` (Không lộ ai đã có tài khoản):** Màn hình đăng ký hành xử giống hệt nhau cho email đã có và chưa có tài khoản; thông tin khác biệt chỉ gửi tới chính hộp thư đó.

  **Lý do nghiệp vụ:** câu "email đã tồn tại", hay một luồng màn hình khác đi, đều cho người ngoài dò được ai đang dùng nền tảng — bước đầu của tấn công nhắm vào tài khoản.

- **`BR-01.7` (Tài khoản chưa xác minh không giữ chỗ email):** Tài khoản chưa xác minh không ngăn người khác đăng ký cùng email. Khi một người xác minh được email, **mọi cách đăng nhập đã đặt trước đó cho email này (mật khẩu, phiên đang mở, liên kết nhà cung cấp danh tính) mất hiệu lực**; người xác minh đặt mật khẩu mới ngay lúc xác minh (đường mã) hoặc dùng nhà cung cấp danh tính đã dùng để xác minh. Sau đó họ chọn tiếp tục thông tin doanh nghiệp đã khai trước đó hoặc bắt đầu lại.

  **Lý do nghiệp vụ:** nếu không, kẻ gian đăng ký trước bằng email của nạn nhân là khoá được nạn nhân khỏi nền tảng, hoặc tệ hơn, giữ lại mật khẩu của mình để vào tài khoản sau khi nạn nhân xác minh; còn chính chủ bắt đầu trên điện thoại rồi xác minh trên máy tính không được mất công đã khai.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-01.1.1` | Email a@congty.vn đã có tài khoản đã xác minh | Đăng ký với email đó | Chuyển sang màn hình khai báo như mọi email; hộp thư a@congty.vn nhận thư có lối đăng nhập và lối tạo workspace mới thay cho mã; không tạo tài khoản mới |
| `AC-01.2.1` | Người đăng ký chưa nhập mã | Bấm xác nhận tạo workspace | Nút bị vô hiệu, hiển thị yêu cầu nhập mã xác minh |
| `AC-01.2.2` | Đăng ký bằng Google với email đã xác minh | Hoàn tất | Không bị hỏi mã xác minh |
| `AC-01.3.1` | Vừa nhập email và mật khẩu | Chuyển sang màn hình khai báo doanh nghiệp | Được chuyển ngay; thông báo nhỏ "Đã gửi mã tới a@congty.vn" hiển thị |
| `AC-01.2.3` | Đã nhập sai mã 4 lần | Nhập sai lần thứ 5 | Mã bị huỷ; có nút gửi mã mới |
| `AC-01.4.1` | Nhập mật khẩu dài nhưng không có ký tự đặc biệt | Rời ô mật khẩu | Được chấp nhận nếu đạt độ mạnh; không bị từ chối chỉ vì thiếu một loại ký tự |
| `AC-01.1.2` | Tài khoản có mật khẩu, nay đăng ký bằng Google cùng email | Hoàn tất bước Google | Được yêu cầu đăng nhập bằng mật khẩu một lần để liên kết; không phát sinh tài khoản thứ hai |
| `AC-01.5.1` | Đăng ký qua nhà cung cấp danh tính doanh nghiệp với email thuộc tên miền mà tổ chức chưa xác minh với nhà cung cấp | Hoàn tất bước đăng nhập | Được yêu cầu nhập mã gửi tới email |
| `AC-01.6.1` | Người ngoài nhập lần lượt một email đã có và một email chưa có tài khoản | Quan sát màn hình sau khi bấm tiếp | Hai lần hiển thị giống hệt nhau |
| `AC-01.7.1` | X đăng ký trước bằng email b@congty.vn nhưng không xác minh | Chủ thật của b@congty.vn đăng ký, nhập đúng mã, chọn Bắt đầu lại | Đăng ký thành công; không còn gì X đã khai |
| `AC-01.7.2` | Chính chủ khai dở trên điện thoại, chưa xác minh | Trên máy tính đăng ký lại cùng email, nhập đúng mã | Thấy lựa chọn Tiếp tục đăng ký đang dở; chọn thì thấy lại thông tin đã khai |
| `AC-01.7.3` | X tạo tài khoản mật khẩu chưa xác minh bằng email của B | B đăng ký bằng Google với email đó | B không bị đòi mật khẩu; mật khẩu và phiên của X mất hiệu lực; B thấy lựa chọn tiếp tục thông tin đã khai hoặc bắt đầu lại |
| `AC-01.7.4` | X đặt mật khẩu cho email của B khi chưa xác minh; B xác minh bằng mã, đặt mật khẩu mới, chọn Tiếp tục | X đăng nhập bằng mật khẩu cũ | Không đăng nhập được; mọi phiên của X đã bị chấm dứt |
| `AC-01.3.2` | Đang ở màn hình chờ mã, phát hiện gõ sai email | Sửa email | Mã gửi tới email mới; mã cũ mất hiệu lực |

---

### FEAT-02 — Tiếp tục đăng ký dở dang & dọn đăng ký bỏ dở

**Mô tả nghiệp vụ:** Người đăng ký tải lại trang, đổi thiết bị hay quay lại sau đều tiếp tục đúng chỗ đã dừng; đăng ký bỏ dở quá lâu được dọn để giải phóng email và tên miền phụ.

**Vai trò sử dụng chính:** Người đăng ký; Hệ thống.

**Điều kiện tiên quyết:** Tài khoản đã tạo ở `FEAT-01`.

**Luồng chính:**

1. Người đăng ký tải lại trang hoặc đăng nhập lại → các bước đã điền được khôi phục, kể cả lựa chọn chưa xác nhận.
2. Đăng ký dở dang không hoạt động quá thời hạn dọn của nền tảng (`PLT-02`) → Hệ thống gửi email nhắc trước khi dọn; tới hạn thì xoá tài khoản dở dang.

**Luồng ngoại lệ:**

- Quay lại sau khi giữ chỗ tên miền phụ đã hết (`PLT-03`) → nếu tên còn trống thì giữ lại; nếu đã bị người khác lấy thì gợi ý tên mới theo `FEAT-06`, không báo lỗi khó hiểu.
- Tài khoản dở dang đã là thành viên của workspace khác (được mời trước đó) → không bị dọn; chỉ phần đăng ký workspace dở dang bị huỷ.

**Quy tắc nghiệp vụ:**

- **`BR-02.1` (Không mất công đã khai):** Mọi thông tin đã khai được lưu theo tài khoản, không theo thiết bị.

  **Lý do nghiệp vụ:** người đăng ký thường bắt đầu trên điện thoại, hoàn tất trên máy tính; bắt khai lại là một lý do bỏ dở.

- **`BR-02.2` (Dọn bỏ dở có báo trước):** Tài khoản dở dang chỉ bị dọn khi không hoạt động quá `PLT-02` và không phải thành viên của workspace nào; người đăng ký nhận email nhắc trước khi dọn.

  **Lý do nghiệp vụ:** giữ mãi tài khoản dở dang làm email bị khoá vô cớ; dọn mà không báo thì người đang định quay lại mất công.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-02.1.1` | Đã khai tên doanh nghiệp, quy mô trên điện thoại | Đăng nhập trên máy tính | Thấy lại đúng các giá trị đã khai, đứng ở bước kế tiếp |
| `AC-02.2.1` | Tài khoản dở dang không hoạt động gần tới `PLT-02` | — | Người đăng ký nhận email nhắc kèm lối tiếp tục |
| `AC-02.2.2` | Tài khoản dở dang đã quá `PLT-02` | Tiến trình dọn chạy | Tài khoản bị xoá; email đăng ký lại được từ đầu |
| `AC-02.2.3` | Tài khoản dở dang đồng thời là thành viên của workspace khác | Quá `PLT-02` | Tài khoản không bị xoá; chỉ phần đăng ký dở dang bị huỷ |
| `AC-02.1.2` | Quay lại sau khi tên miền phụ đã bị người khác lấy | Mở bước tên miền phụ | Thấy gợi ý tên mới kèm giải thích ngắn |

---

## B. HỒ SƠ DOANH NGHIỆP

### FEAT-03 — Khai báo doanh nghiệp, quy mô, ngành & mục tiêu

**Mô tả nghiệp vụ:** Thu thập trên **một màn hình** những thông tin cần để cá nhân hoá workspace: tên doanh nghiệp, quy mô, ngành, mục tiêu sử dụng.

**Vai trò sử dụng chính:** Người đăng ký.

**Điều kiện tiên quyết:** Đã có tài khoản (`FEAT-01`).

**Luồng chính:**

1. Nhập tên doanh nghiệp (bắt buộc).
2. Chọn quy mô: 1 – 10, 11 – 50, 51 – 200, trên 200 (bắt buộc).
3. Chọn ngành từ danh mục mẫu ngành (`PLT-05`), có lựa chọn "Khác" (mặc định "Khác" nếu bỏ qua).
4. Chọn một hoặc nhiều mục tiêu sử dụng: bán hàng, chăm sóc khách hàng, tiếp thị (tuỳ chọn).
5. Sang bước kế tiếp.

**Luồng ngoại lệ:**

- Tên doanh nghiệp dưới 2 hoặc trên 150 ký tự → báo ngay trên ô nhập.

**Quy tắc nghiệp vụ:**

- **`BR-03.1` (Chỉ hai trường bắt buộc):** Chỉ tên doanh nghiệp và quy mô là bắt buộc; ngành và mục tiêu có mặc định và sửa được về sau.

  **Lý do nghiệp vụ:** Nguyên tắc 1; quy mô quyết định gợi ý đội ngũ (`FEAT-12`), tên quyết định tên miền phụ; các trường khác chỉ tinh chỉnh. Giới hạn 2 – 150 ký tự của tên doanh nghiệp đủ cho tên giao dịch đầy đủ và giữ tên hiển thị được trong thư mời, tiêu đề màn hình.

- **`BR-03.2` (Lựa chọn quyết định cấu hình khởi điểm):** Ngành chọn mẫu ngành (`FEAT-09`); quy mô chọn mẫu đội ngũ gợi ý (`FEAT-12`); mục tiêu quyết định nhiệm vụ nào lên đầu lộ trình thiết lập (`FEAT-11`).

  **Lý do nghiệp vụ:** câu hỏi nào không làm thay đổi gì cho người dùng thì không nên hỏi.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-03.1.1` | Màn hình khai báo | Nhập tên, chọn quy mô, bỏ qua ngành và mục tiêu | Sang được bước kế tiếp; ngành ghi "Khác" |
| `AC-03.1.2` | Màn hình khai báo | Nhập tên 1 ký tự | Ô báo lỗi trước khi bấm tiếp |
| `AC-03.2.1` | Chọn ngành Bất động sản, quy mô 11 – 50 | Workspace Sẵn sàng | Phễu theo mẫu Bất động sản có sẵn; thiết lập đội ngũ gợi ý mẫu 11 – 50 |

---

### FEAT-04 — Đề xuất ngôn ngữ, múi giờ & tiền tệ

**Mô tả nghiệp vụ:** Đề xuất ngôn ngữ, múi giờ, định dạng ngày và tiền tệ phù hợp với quốc gia của người đăng ký, hiển thị ngay trên màn hình khai báo để sửa được trong một bước. Giá trị chốt trở thành cấu hình chung của workspace ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-04`).

**Vai trò sử dụng chính:** Người đăng ký; Hệ thống.

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Hệ thống xác định quốc gia từ ngôn ngữ trình duyệt và vị trí truy cập.
2. Đề xuất theo bảng đề xuất theo quốc gia (`PLT-06`); quốc gia không có trong bảng dùng giá trị quốc tế.
3. Người đăng ký giữ hoặc sửa từng giá trị trên cùng màn hình khai báo doanh nghiệp.

**Quy tắc nghiệp vụ:**

- **`BR-04.1` (Đề xuất, không áp đặt):** Mọi giá trị đề xuất hiển thị rõ và sửa được trước khi tạo workspace, và sửa lại được bất kỳ lúc nào sau đó.

  **Lý do nghiệp vụ:** người ở Việt Nam có thể lập workspace cho chi nhánh tại Ả Rập Xê Út; đoán sai mà không cho sửa là áp đặt.

- **`BR-04.2` (Múi giờ có trước mốc đầu tiên):** Múi giờ chọn lúc đăng ký có hiệu lực ngay từ khi workspace Sẵn sàng; về sau mọi mốc ngày theo múi giờ hiện hành của workspace (Mục 2.3).

  **Lý do nghiệp vụ:** ngày còn lại của dùng thử và lịch thông điệp phải đúng ngay từ ngày đầu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-04.1.1` | Truy cập từ Việt Nam, trình duyệt tiếng Việt | Mở màn hình khai báo | Đề xuất tiếng Việt, múi giờ Việt Nam, tiền tệ Đồng; sửa được ngay tại chỗ |
| `AC-04.1.2` | Truy cập từ Ả Rập Xê Út | Đổi tiền tệ đề xuất sang Đô la Mỹ | Workspace tạo ra dùng Đô la Mỹ |
| `AC-04.2.1` | Chốt múi giờ Riyadh | Workspace Sẵn sàng, xem số ngày dùng thử còn lại | Số ngày tính theo múi giờ Riyadh |
| `AC-04.1.3` | Quốc gia không có trong bảng đề xuất | Mở màn hình khai báo | Đề xuất giá trị quốc tế |

---

### FEAT-05 — Tín hiệu khách hàng lớn cho đội kinh doanh của nhà cung cấp

**Mô tả nghiệp vụ:** Khi một doanh nghiệp đăng ký có quy mô đạt ngưỡng (`PLT-07`), đội kinh doanh của nhà cung cấp nhận tín hiệu để chủ động hỗ trợ.

**Vai trò sử dụng chính:** Hệ thống; Nhân sự vận hành nền tảng (nhận).

**Điều kiện tiên quyết:** Workspace Sẵn sàng.

**Luồng chính:**

1. Workspace Sẵn sàng với quy mô đạt ngưỡng → tạo tín hiệu trong công cụ nội bộ của nhà cung cấp, kèm tên doanh nghiệp, quy mô, ngành, ngày đăng ký và lối mở hồ sơ trong công cụ nội bộ.
2. Nhân sự nhận tín hiệu ghi kết quả liên hệ; kết quả "đã xác nhận khách hàng thật" là căn cứ nới giới hạn lời mời (`BR-14.3`).

**Quy tắc nghiệp vụ:**

- **`BR-05.1` (Tối thiểu dữ liệu cá nhân trong kênh nội bộ):** Tín hiệu gửi qua kênh trao đổi nội bộ chung (nhóm chat, email nhóm) không chứa email, số điện thoại hay tên người đăng ký; thông tin liên hệ chỉ xem được trong công cụ nội bộ có phân quyền.

  **Lý do nghiệp vụ:** kênh trao đổi chung có nhiều người đọc và lưu lâu; dữ liệu liên hệ của khách hàng không được rải ra đó.

- **`BR-05.2` (Không ảnh hưởng trải nghiệm khách hàng):** Tín hiệu không làm chậm hay đổi luồng đăng ký của khách hàng.

  **Lý do nghiệp vụ:** khách hàng lớn cần trải nghiệm tự phục vụ tốt như mọi khách hàng khác; hỗ trợ của đội kinh doanh là thêm vào, không thay thế.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-05.1.1` | Doanh nghiệp quy mô trên 200 vừa Sẵn sàng | Xem tin nhắn trong kênh nội bộ chung | Có tên doanh nghiệp, quy mô, ngành; không có email hay số điện thoại |
| `AC-05.2.1` | Cùng doanh nghiệp | Theo dõi luồng đăng ký | Không có bước hay thời gian chờ nào khác so với doanh nghiệp nhỏ |

---

## C. ĐỊNH DANH WORKSPACE

### FEAT-06 — Tên miền phụ của workspace

**Mô tả nghiệp vụ:** Gợi ý sẵn một tên miền phụ đẹp từ tên doanh nghiệp, cho sửa, kiểm tra trùng ngay khi gõ và giữ chỗ trong lúc người đăng ký hoàn tất.

**Vai trò sử dụng chính:** Người đăng ký; Hệ thống; Nhân sự vận hành nền tảng (kênh tạo hộ).

**Điều kiện tiên quyết:** Đã có tên doanh nghiệp.

**Luồng chính:**

1. Hệ thống sinh gợi ý từ tên doanh nghiệp: bỏ dấu tiếng Việt (gồm Đ → D), chữ thường, khoảng trắng và ký tự đặc biệt thành gạch nối, độ dài 3 – 50 ký tự.
2. Gợi ý bị trùng hoặc thuộc danh mục bảo lưu → hệ thống tự thử biến thể thêm hậu tố ngắn có nghĩa (ví dụ tên thành phố, năm) cho tới khi có tên trống.
3. Người đăng ký giữ hoặc sửa; mỗi lần sửa, hệ thống báo ngay tên có dùng được không.
4. Tên được giữ chỗ cho người đăng ký trong `PLT-03`; khi workspace Sẵn sàng, tên thuộc về workspace vĩnh viễn.

**Luồng ngoại lệ:**

- Người đăng ký **tự sửa** thành tên đã có người dùng hoặc thuộc danh mục bảo lưu → báo không dùng được và gợi ý vài tên gần giống; không tự gắn hậu tố vào tên người dùng tự chọn.
- Tên tự sửa sai định dạng (ký tự không hợp lệ, gạch nối ở đầu hay cuối, ngắn hoặc dài quá) → báo ngay, nêu quy tắc.
- Hệ thống không tìm được biến thể trống → yêu cầu người đăng ký tự nhập.

**Quy tắc nghiệp vụ:**

- **`BR-06.1` (Duy nhất toàn hệ thống):** Tên miền phụ duy nhất trên toàn nền tảng và không trùng danh mục tên bảo lưu (`PLT-04`).

  **Lý do nghiệp vụ:** tên miền phụ là địa chỉ đăng nhập của doanh nghiệp; trùng là không phân biệt được workspace.

- **`BR-06.2` (Không đổi tên người dùng đã tự chọn):** Hệ thống chỉ tự thêm hậu tố vào tên do chính hệ thống gợi ý.

  **Lý do nghiệp vụ:** tên tự chọn thường là thương hiệu; tự ý đổi làm biến dạng thương hiệu mà người dùng không đồng ý.

- **`BR-06.3` (Kiểm tra trùng không lộ khách hàng khác):** Thông báo "tên đã được dùng" không kèm bất kỳ thông tin nào về workspace đang dùng tên đó; số lượt kiểm tra từ một người đăng ký bị giới hạn theo ngưỡng chống lạm dụng của nền tảng.

  **Lý do nghiệp vụ:** cho dò tên tự do là cho người ngoài liệt kê ai đang là khách hàng của nền tảng.

- **`BR-06.4` (Danh mục tên bảo lưu):** Danh mục tên bảo lưu gồm tên dùng cho dịch vụ của nền tảng (đăng nhập, quản trị, hỗ trợ, trạng thái, thanh toán, tài liệu…), tên thương hiệu của nhà cung cấp và các biến thể dễ gây nhầm; do nhà cung cấp duy trì (`PLT-04`).

  **Lý do nghiệp vụ:** một workspace mang tên "login" hay tên thương hiệu nhà cung cấp là công cụ lừa đảo sẵn có.

- **`BR-06.5` (Giữ chỗ có hạn):** Tên được giữ cho người đăng ký trong `PLT-03`; hết hạn mà chưa tạo workspace thì tên được giải phóng.

  **Lý do nghiệp vụ:** không giữ chỗ thì tên có thể bị lấy mất giữa chừng; giữ mãi thì tên bị khoá bởi người đã bỏ đi.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-06.1.1` | Tên doanh nghiệp "Công ty Đại Dương" | Sang bước tên miền phụ | Gợi ý "cong-ty-dai-duong" hoặc biến thể ngắn hơn nếu trùng |
| `AC-06.2.1` | Người dùng tự gõ "daiduong", tên đã có người dùng | Rời ô nhập | Báo không dùng được, gợi ý vài tên gần giống; ô vẫn giữ "daiduong" |
| `AC-06.3.1` | Tên đã có người dùng | Xem thông báo | Không có tên doanh nghiệp hay thông tin nào của workspace đang dùng tên đó |
| `AC-06.4.1` | Người dùng gõ "login" | Rời ô nhập | Báo tên được bảo lưu |
| `AC-06.5.1` | Đang giữ chỗ, quá `PLT-03` chưa tạo workspace | Người khác thử tên đó | Người khác dùng được |
| `AC-06.1.2` | Người dùng gõ "-abc" | Rời ô nhập | Báo không được bắt đầu bằng gạch nối |

---

### FEAT-07 — Tên miền riêng của doanh nghiệp

**Mô tả nghiệp vụ:** Doanh nghiệp dùng tên miền của mình (ví dụ crm.congty.vn) để truy cập workspace, sau khi chứng minh sở hữu tên miền.

**Vai trò sử dụng chính:** Người có quyền Quản lý cấu hình workspace.

**Điều kiện tiên quyết:** Gói dịch vụ có tính năng tên miền riêng ([`billing-subscription-srs.md`](./billing-subscription-srs.md) `FEAT-01`).

**Luồng chính:**

1. Nhập tên miền mong muốn.
2. Hệ thống hiển thị hai bản ghi cần khai ở nhà cung cấp tên miền: một bản ghi chứng minh sở hữu và một bản ghi trỏ địa chỉ về nền tảng.
3. Bấm kiểm tra → hệ thống xác minh cả hai, cấp chứng chỉ bảo mật, kích hoạt tên miền.
4. Từ đó thư mời, liên kết trong email và trang đăng nhập dùng tên miền riêng; tên miền phụ vẫn hoạt động.

**Luồng ngoại lệ:**

- Chưa thấy bản ghi → báo chưa xác minh, kèm hướng dẫn và cho kiểm tra lại; hệ thống tự kiểm tra lại định kỳ trong 72 giờ.
- Tên miền đã được một workspace khác xác minh → từ chối, không nêu workspace đó là ai.
- Sau khi kích hoạt, bản ghi chứng minh sở hữu hoặc bản ghi trỏ bị gỡ, hoặc chứng chỉ không gia hạn được → tên miền chuyển "Mất xác minh", truy cập tự chuyển về tên miền phụ, Chủ sở hữu và người cấu hình nhận cảnh báo; quá `PLT-19` không khắc phục thì tên miền bị gỡ khỏi workspace.

**Quy tắc nghiệp vụ:**

- **`BR-07.1` (Chứng minh sở hữu là bắt buộc):** Chỉ kích hoạt khi bản ghi chứng minh sở hữu đúng giá trị do hệ thống cấp cho workspace này.

  **Lý do nghiệp vụ:** chỉ kiểm tra bản ghi trỏ địa chỉ thì ai trỏ được tên miền là chiếm được nó, kể cả tên miền đã bỏ của người khác.

- **`BR-07.2` (Một tên miền, một workspace):** Một tên miền riêng chỉ gắn với một workspace.

  **Lý do nghiệp vụ:** cùng địa chỉ dẫn tới hai workspace là không xác định được người dùng đang vào đâu.

- **`BR-07.3` (Mất xác minh thì quay về tên miền phụ):** Khi mất xác minh, mọi truy cập và liên kết mới dùng tên miền phụ; liên kết cũ theo tên miền riêng được chuyển hướng an toàn khi còn khả năng.

  **Lý do nghiệp vụ:** tên miền hết hạn rồi bị người khác mua lại không được trở thành cổng đăng nhập giả của workspace.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-07.1.1` | Chỉ khai bản ghi trỏ địa chỉ, chưa khai bản ghi chứng minh sở hữu | Bấm kiểm tra | Báo chưa xác minh sở hữu; tên miền chưa hoạt động |
| `AC-07.1.2` | Khai đủ hai bản ghi | Bấm kiểm tra | Tên miền hoạt động có chứng chỉ bảo mật; thư mời mới dùng tên miền riêng |
| `AC-07.2.1` | Tên miền đã gắn với workspace khác | Nhập tên miền đó | Từ chối, không nêu workspace kia |
| `AC-07.3.1` | Tên miền đang hoạt động, bản ghi chứng minh bị gỡ | Hệ thống kiểm tra định kỳ | Trạng thái "Mất xác minh"; truy cập chuyển về tên miền phụ; Chủ sở hữu nhận cảnh báo |
| `AC-07.3.2` | "Mất xác minh" quá `PLT-19` | — | Tên miền bị gỡ khỏi workspace; Chủ sở hữu được thông báo |

---

### FEAT-20 — Xác minh tên miền email doanh nghiệp

**Mô tả nghiệp vụ:** Workspace chứng minh sở hữu tên miền email của doanh nghiệp (ví dụ congty.vn) để mở các khả năng dựa trên tên miền: tự gia nhập theo tên miền ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-50`), nhận diện doanh nghiệp trong thư mời (`BR-14.2`), nới giới hạn lời mời (`BR-14.3`), và gợi ý người cùng công ty tham gia đúng workspace.

**Vai trò sử dụng chính:** Người có toàn quyền; Nhân sự vận hành nền tảng (thẩm định khiếu nại); Hệ thống (kiểm tra định kỳ).

**Điều kiện tiên quyết:** Tên miền không thuộc danh mục nhà cung cấp email miễn phí (`PLT-16`).

**Luồng chính:**

1. Nhập tên miền email.
2. Hệ thống cấp một giá trị chứng minh sở hữu riêng cho workspace; người dùng khai giá trị đó ở nhà cung cấp tên miền.
3. Bấm kiểm tra → tên miền chuyển Đã xác minh.
4. Hệ thống kiểm tra lại định kỳ; giá trị chứng minh bị gỡ → tên miền chuyển Mất xác minh.

**Luồng ngoại lệ:**

- Tên miền thuộc danh mục email miễn phí → từ chối ngay tại ô nhập.
- Tên miền đã được workspace khác xác minh → không xác minh được; người dùng có thể gửi **khiếu nại** kèm bằng chứng (ví dụ văn bản của doanh nghiệp sở hữu tên miền); một hồ sơ khiếu nại được mở — tên miền vẫn Đã xác minh cho bên đang giữ và mọi khả năng của họ vẫn hoạt động trong lúc thẩm định; Nhân sự vận hành nền tảng thẩm định, thông báo cho Người có toàn quyền của workspace đang giữ tên miền và chờ `PLT-17` để họ phản hồi kèm bằng chứng. Kết quả (`BR-20.5`):
  - **Khiếu nại được chấp thuận** (kể cả khi bên đang giữ không phản hồi trong `PLT-17`): bên đang giữ chuyển Mất xác minh; thành viên đã vào bên đó qua tự gia nhập giữ nguyên tư cách thành viên, Người có toàn quyền bên đó được thông báo để rà soát; bên khiếu nại xác minh được.
  - **Khiếu nại bị bác:** bên đang giữ giữ nguyên; bên khiếu nại được thông báo kèm lý do.
- Mất xác minh → tự gia nhập theo tên miền tắt, thư mời trở về mẫu chuẩn, giới hạn lời mời trở về mức thường; Người có toàn quyền nhận cảnh báo; xác minh lại thì các khả năng tự bật lại theo cấu hình cũ.

**Quy tắc nghiệp vụ:**

- **`BR-20.1` (Một tên miền email, một workspace):** Một tên miền email chỉ được một workspace xác minh tại một thời điểm.

  **Lý do nghiệp vụ:** hai workspace cùng nhận một tên miền thì không biết người có email thuộc tên miền đó nên được gợi ý vào đâu, và tự gia nhập thành cửa vào cho workspace sai.

- **`BR-20.2` (Không lộ workspace đang giữ tên miền):** Khi tên miền đã thuộc workspace khác, thông báo không nêu workspace đó là ai.

  **Lý do nghiệp vụ:** thông tin khách hàng nào đang dùng nền tảng là thông tin của khách hàng đó.

- **`BR-20.3` (Gợi ý đúng workspace của công ty):** Chỉ sau khi email đã xác minh, nếu email thuộc tên miền đã xác minh của một workspace đang bật tự gia nhập, hệ thống gợi ý "Tham gia workspace của công ty". Dù workspace đó có bật tự gia nhập hay không, nếu người đó tạo workspace mới, màn hình báo trước rằng Người có toàn quyền của workspace sở hữu tên miền sẽ được thông báo (tên workspace mới và email người tạo); người đó có thể quay lại dùng một email khác. Đây là ngoại lệ tường minh của `NFR-07` và [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-51.3`.

  **Lý do nghiệp vụ:** chi nhánh tự lập workspace riêng làm dữ liệu khách hàng của công ty phân tán ngoài tầm quản trị; công ty sở hữu tên miền có quyền được biết, còn người tạo có quyền được biết trước khi điều đó xảy ra. Gợi ý trước khi xác minh thì ai gõ một email cũng biết công ty nào đang là khách hàng.

- **`BR-20.4` (Mất xác minh thì tắt các khả năng dựa trên tên miền):** Mọi khả năng mở ra nhờ tên miền chỉ có hiệu lực khi tên miền đang Đã xác minh.

  **Lý do nghiệp vụ:** tên miền hết hạn rồi bị người khác mua lại không được tiếp tục là vé vào workspace.

- **`BR-20.5` (Khiếu nại có kết quả xác định):** Mỗi khiếu nại kết thúc ở chấp thuận hoặc bác, có lý do ghi nhận; bên đang giữ không phản hồi trong `PLT-17` thì kết quả theo bằng chứng của bên khiếu nại. Chấp thuận không tự gỡ thành viên nào khỏi workspace cũ.

  **Lý do nghiệp vụ:** tên miền đổi chủ (sáp nhập, bán công ty) là có thật; không có kết quả xác định thì tên miền bị khoá mãi. Tự gỡ thành viên thì người đang làm việc mất quyền truy cập dữ liệu công việc mà không ai quyết định.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-20.1.1` | Khai đúng giá trị chứng minh cho congty.vn | Bấm kiểm tra | Trạng thái Đã xác minh; lựa chọn tự gia nhập theo congty.vn mở được |
| `AC-20.1.2` | congty.vn đã thuộc workspace khác | Nhập congty.vn | Báo không xác minh được, có lối gửi khiếu nại |
| `AC-20.2.1` | Tình huống AC-20.1.2 | Đọc thông báo | Không có tên hay thông tin nào của workspace đang giữ tên miền |
| `AC-20.3.1` | congty.vn Đã xác minh ở W1, W1 bật tự gia nhập | Người có email @congty.vn đăng ký mới | Thấy gợi ý tham gia W1 trước khi tạo workspace mới |
| `AC-20.3.2` | Người đó vẫn tạo workspace W2 | W2 Sẵn sàng | Người có toàn quyền của W1 nhận thông báo kèm tên W2 và email người tạo |
| `AC-20.3.3` | Người đăng ký nhập x@congty.vn, chưa nhập mã | Sang màn hình khai báo | Không có gợi ý nào về workspace của congty.vn |
| `AC-20.3.4` | Người có email @congty.vn đã xác minh chọn vẫn tạo workspace mới | Màn hình trước khi tạo | Hiển thị cảnh báo sẽ thông báo cho công ty sở hữu tên miền, có lối quay lại |
| `AC-20.3.5` | congty.vn Đã xác minh ở W1, W1 không bật tự gia nhập | Người có email @congty.vn đã xác minh tạo workspace W2 | Không có gợi ý tham gia; vẫn có cảnh báo trước; Người có toàn quyền của W1 nhận thông báo |
| `AC-20.5.1` | Khiếu nại về congty.vn, bên đang giữ không phản hồi trong `PLT-17` | Nhân sự vận hành thẩm định đạt | Bên đang giữ chuyển Mất xác minh; thành viên của họ vẫn còn; bên khiếu nại xác minh được |
| `AC-20.5.2` | Khiếu nại thiếu bằng chứng | Nhân sự vận hành bác | Bên khiếu nại nhận lý do; bên đang giữ không bị gián đoạn lúc nào trong suốt quá trình |
| `AC-20.5.3` | Hồ sơ khiếu nại đang được thẩm định | Người có email @congty.vn đăng nhập | Vẫn thấy workspace của bên đang giữ trong danh sách có thể tham gia |
| `AC-20.4.1` | congty.vn đang Đã xác minh, tự gia nhập bật | Giá trị chứng minh bị gỡ, hệ thống kiểm tra định kỳ | Trạng thái Mất xác minh; người có email @congty.vn không còn thấy workspace trong danh sách có thể tham gia; Người có toàn quyền nhận cảnh báo |
| `AC-20.1.3` | Nhập gmail.com | Rời ô nhập | Báo tên miền email miễn phí không xác minh được |

---

## D. KHỞI TẠO

### FEAT-08 — Khởi tạo workspace toàn vẹn

**Mô tả nghiệp vụ:** Sau khi người đăng ký xác nhận, hệ thống tạo toàn bộ workspace. Kết quả chỉ có hai khả năng: Sẵn sàng đầy đủ và người dùng ở ngay bên trong, hoặc không để lại dấu vết và người dùng biết rõ phải làm gì.

**Vai trò sử dụng chính:** Người đăng ký (xác nhận); Hệ thống.

**Điều kiện tiên quyết:** Email đã xác minh (`BR-01.2`); tên miền phụ đang giữ chỗ hoặc đã chọn hợp lệ.

**Luồng chính:**

1. Người đăng ký xem tóm tắt (tên doanh nghiệp, tên miền phụ, ngôn ngữ, múi giờ, ngành, có nạp dữ liệu mẫu hay không) và bấm Tạo workspace.
2. Màn hình chờ hiển thị tiến độ theo các giai đoạn thật đã hoàn thành (không theo phần trăm giả định), với lời mô tả bằng ngôn ngữ người dùng.
3. Hệ thống tạo workspace, xác lập Chủ sở hữu, tạo khung phân quyền ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-01`, `FEAT-02`), kích hoạt dùng thử theo [`billing-subscription-srs.md`](./billing-subscription-srs.md) `FEAT-05`, và kết nối các dịch vụ đi kèm của workspace.
4. Workspace Sẵn sàng → người đăng ký được đưa thẳng vào workspace đã đăng nhập sẵn, không phải đăng nhập lại.
5. Cấu hình theo ngành (`FEAT-09`) và dữ liệu mẫu (`FEAT-10`) được nạp; nếu chưa xong khi người dùng vào, màn hình liên quan hiển thị "đang chuẩn bị" thay vì trống.

**Luồng ngoại lệ:**

- Một bước thất bại tạm thời → hệ thống tự thử lại tối đa theo `PLT-08`, người dùng chỉ thấy màn hình chờ tiếp tục.
- Thất bại sau mọi lần thử → hệ thống dọn mọi phần đã tạo, giữ chỗ tên miền phụ thêm `PLT-03` cho người đăng ký rồi mới giải phóng, giữ tài khoản và thông tin đã khai; người dùng thấy thông báo rõ ràng kèm nút "Thử lại" và lối liên hệ hỗ trợ có mã tra cứu; nếu người dùng không còn ở màn hình chờ, họ nhận email báo thất bại kèm lối thử lại.
- Bấm Thử lại khi tên miền phụ cũ đã có người khác lấy → hệ thống yêu cầu chọn tên mới theo `FEAT-06` trước khi chạy lại.
- Thất bại ở kênh tạo hộ → nhân sự vận hành nền tảng tạo yêu cầu được thông báo; Chủ sở hữu được chỉ định không nhận email nào.
- Người dùng đóng trình duyệt trong lúc chờ → khởi tạo vẫn tiếp tục; lần đăng nhập sau đưa thẳng vào workspace nếu đã Sẵn sàng, hoặc vào màn hình chờ.

**Quy tắc nghiệp vụ:**

- **`BR-08.1` (Điều kiện Sẵn sàng):** Workspace chỉ Sẵn sàng khi đủ: Chủ sở hữu được xác lập, khung phân quyền đầy đủ (`BR-02.1` của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md)), trạng thái thương mại đã được ghi nhận theo [`billing-subscription-srs.md`](./billing-subscription-srs.md) `BR-04.1`, hoặc đang chờ xử lý thủ công theo `BR-08.6`, tên miền phụ được xác nhận, các dịch vụ đi kèm bắt buộc đã kết nối. Cấu hình theo ngành và dữ liệu mẫu không thuộc điều kiện này.

  **Lý do nghiệp vụ:** thiếu một trong các thành phần bắt buộc thì người dùng gặp lỗi ngay ở thao tác đầu tiên; còn cấu hình theo ngành hỏng thì vẫn dùng được với cấu hình mặc định và sửa được.

- **`BR-08.2` (Thất bại không để lại dấu vết):** Khởi tạo thất bại phải dọn mọi phần đã tạo ở mọi dịch vụ; tên miền phụ được giữ cho người đăng ký thêm `PLT-03` để thử lại, sau đó mới giải phóng; tài khoản và thông tin đã khai của người đăng ký được giữ.

  **Lý do nghiệp vụ:** Nguyên tắc 3; phần dở dang ở một dịch vụ có thể gây lỗi khó hiểu ở lần thử sau; còn giải phóng tên miền phụ ngay trong lúc người dùng đang nhìn nút Thử lại thì họ có thể mất đúng cái tên vừa chọn.

- **`BR-08.3` (Thử lại chỉ bởi chính người đăng ký):** Thử lại một khởi tạo thất bại chỉ được thực hiện trong phiên đăng nhập của chính người đăng ký đó (hoặc bởi nhân sự vận hành nền tảng ở kênh tạo hộ); mã tra cứu chỉ dùng để tra cứu, không dùng để thao tác.

  **Lý do nghiệp vụ:** mã tra cứu hiển thị trên màn hình và được chia sẻ khi liên hệ hỗ trợ; nếu nó đủ để thao tác thì người khác có thể khởi tạo lại workspace của người khác.

- **`BR-08.4` (Không đăng nhập lại):** Khi Sẵn sàng, người đăng ký vào thẳng workspace bằng chính phiên đã có.

  **Lý do nghiệp vụ:** bắt đăng nhập lại ngay sau khi đăng ký là ma sát không có giá trị và là điểm bỏ dở đã biết.

- **`BR-08.5` (Tiến độ phản ánh thật):** Màn hình chờ chỉ báo một giai đoạn hoàn thành khi giai đoạn đó thật sự hoàn thành.

  **Lý do nghiệp vụ:** thanh tiến độ giả tạo kỳ vọng sai; khi thất bại ở "80%", người dùng không tin thông báo lỗi.

- **`BR-08.6` (Dùng thử chờ xử lý thủ công):** Khi có dấu hiệu dùng thử lặp lại ở kênh tự đăng ký, workspace vẫn Sẵn sàng để người dùng đăng nhập, xem cấu hình và chuẩn bị; trạng thái thương mại và thời điểm bắt đầu dùng thử theo quyết định xử lý thủ công của [`billing-subscription-srs.md`](./billing-subscription-srs.md) `BR-05.7`. Cho tới khi có quyết định, workspace chưa gửi được lời mời (kể cả mời từ tệp), chưa tạo được liên kết mời hay bật tự gia nhập, chưa kết nối được kênh hay tạo biểu mẫu website; thanh trạng thái hiển thị "Dùng thử đang chờ xác nhận" thay cho số ngày còn lại; lộ trình thiết lập và thông điệp hướng dẫn tạm dừng các nhiệm vụ bị chặn và nêu lý do. Người phụ trách thanh toán luôn có lối **trả phí ngay** để bỏ qua việc chờ. Không áp cho kênh tạo hộ.

  **Lý do nghiệp vụ:** người dùng thật không bị chặn ở cửa và có cách tự gỡ ngay bằng cách trả phí; kẻ lạm dụng thì không dùng được workspace để gửi thư hay tiếp nhận khách hàng. Chính sách thương mại (khi nào tính ngày dùng thử, kết quả khi bác) thuộc billing.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-08.1.1` | Khởi tạo bình thường | Bấm Tạo workspace | Trong thời gian `NFR-02`, người dùng ở bên trong workspace, thấy tên doanh nghiệp, không qua màn hình đăng nhập |
| `AC-08.1.2` | Cấu hình theo ngành chưa nạp xong khi người dùng vào | Mở màn hình Cơ hội | Thấy "đang chuẩn bị phễu theo ngành", không thấy màn hình trống hay lỗi |
| `AC-08.2.1` | Kết nối dịch vụ đi kèm thất bại sau mọi lần thử | Theo dõi màn hình chờ | Thông báo rõ kèm nút Thử lại và mã tra cứu; tên miền phụ vẫn được giữ cho người đăng ký trong `PLT-03`; thông tin đã khai còn nguyên |
| `AC-08.2.2` | Tình huống AC-08.2.1 | Bấm Thử lại | Khởi tạo chạy lại từ đầu; thành công thì vào workspace |
| `AC-08.2.3` | Khởi tạo thất bại, tên miền phụ cũ đã bị người khác lấy | Bấm Thử lại | Được yêu cầu chọn tên miền phụ mới trước khi chạy lại |
| `AC-08.2.4` | Người dùng đã đóng trình duyệt; khởi tạo thất bại | — | Người dùng nhận email báo thất bại kèm lối thử lại |
| `AC-08.6.1` | Doanh nghiệp có dấu hiệu dùng thử lặp lại | Khởi tạo hoàn tất, người dùng mở thiết lập đội ngũ | Vào được workspace; thao tác gửi lời mời bị vô hiệu kèm lý do và lối liên hệ |
| `AC-08.6.2` | Workspace đang chờ xử lý dùng thử | Mở màn hình kết nối kênh, tạo liên kết mời | Cả hai thao tác bị vô hiệu kèm cùng lý do; lộ trình thiết lập ghi "đang chờ xác nhận dùng thử" |
| `AC-08.6.3` | Workspace đang chờ xử lý dùng thử | Chủ sở hữu mở ứng dụng | Thanh trạng thái hiển thị "Dùng thử đang chờ xác nhận", không có số ngày; có lối trả phí ngay |
| `AC-08.6.4` | Workspace đang chờ xử lý dùng thử | Người phụ trách thanh toán trả phí ngay | Các thao tác bị chặn mở lại ngay |
| `AC-08.6.5` | Workspace đang chờ xác nhận dùng thử | Thử mời từ tệp, bật tự gia nhập, tạo biểu mẫu | Cả ba bị vô hiệu kèm cùng lý do |
| `AC-08.3.1` | Người khác có mã tra cứu | Thử dùng mã để khởi tạo lại | Không có thao tác nào; mã chỉ hiển thị trạng thái ở mức chung |
| `AC-08.4.1` | Người dùng đóng trình duyệt lúc chờ | Đăng nhập lại sau khi workspace đã Sẵn sàng | Vào thẳng workspace |
| `AC-08.5.1` | Giai đoạn "Thiết lập phân quyền" chưa xong | Xem màn hình chờ | Giai đoạn đó chưa được đánh dấu hoàn thành |

---

### FEAT-09 — Cấu hình nghiệp vụ khởi điểm theo ngành

**Mô tả nghiệp vụ:** Workspace mới có sẵn phễu bán hàng và quy trình vé hỗ trợ phù hợp với ngành đã chọn, để khách hàng làm việc ngay rồi tinh chỉnh dần.

**Vai trò sử dụng chính:** Hệ thống.

**Điều kiện tiên quyết:** Workspace Sẵn sàng.

**Luồng chính:**

1. Lấy mẫu ngành tương ứng trong danh mục mẫu ngành (`PLT-05`); ngành "Khác" dùng mẫu chung.
2. Tạo phễu bán hàng, quy trình vé hỗ trợ và các cấu hình khởi điểm khác mà mẫu khai báo.
3. Mọi cấu hình được tạo là cấu hình thường của workspace, sửa được theo SRS phân hệ tương ứng.

**Luồng ngoại lệ:**

- Nạp mẫu ngành thất bại → dùng mẫu chung; người có quyền Quản lý cấu hình workspace thấy thông báo nhỏ và lối "Áp dụng mẫu ngành" để thử lại.
- Áp dụng mẫu ngành về sau → **thêm** phễu và quy trình của mẫu như cấu hình mới; không sửa, không xoá phễu, quy trình và bản ghi đang có (`BR-09.3`).

**Quy tắc nghiệp vụ:**

- **`BR-09.1` (Mọi ngành trong danh mục đều có mẫu đầy đủ):** Mỗi ngành trong danh mục mẫu ngành phải có đủ phễu bán hàng và quy trình vé hỗ trợ; một ngành chưa có mẫu đầy đủ không được hiển thị để chọn.

  **Lý do nghiệp vụ:** chọn một ngành rồi nhận cấu hình chung chung là lời hứa không được giữ.

- **`BR-09.2` (Mẫu ngành là điểm khởi đầu):** Cấu hình từ mẫu ngành không khác gì cấu hình doanh nghiệp tự tạo; không có phần nào bị khoá.

  **Lý do nghiệp vụ:** mỗi doanh nghiệp trong cùng ngành vẫn bán hàng khác nhau.

- **`BR-09.3` (Áp mẫu sau không động tới dữ liệu đang có):** Áp dụng mẫu ngành sau khi workspace đã vận hành chỉ thêm cấu hình mới; cơ hội và vé đang có ở lại phễu, quy trình cũ cho tới khi người dùng tự chuyển.

  **Lý do nghiệp vụ:** thay phễu đang có cơ hội thật làm sai giai đoạn của các thương vụ đang chạy.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-09.1.1` | Ngành Bất động sản | Workspace Sẵn sàng, mở Cơ hội | Có phễu theo mẫu Bất động sản; mở Vé hỗ trợ có quy trình theo mẫu |
| `AC-09.1.2` | Một ngành chưa có đủ mẫu | Mở danh sách ngành khi đăng ký | Ngành đó không có trong danh sách |
| `AC-09.2.1` | Phễu tạo từ mẫu | Đổi tên một giai đoạn | Lưu được như phễu tự tạo |
| `AC-09.1.3` | Nạp mẫu ngành thất bại | Chủ sở hữu mở Cơ hội | Có phễu chung, kèm thông báo và nút "Áp dụng mẫu ngành" |
| `AC-09.3.1` | Phễu chung đã có 10 cơ hội thật | Áp dụng mẫu ngành Bất động sản | Có thêm phễu Bất động sản; 10 cơ hội vẫn ở phễu chung với nguyên giai đoạn |

---

### FEAT-10 — Dữ liệu mẫu & xoá dữ liệu mẫu

**Mô tả nghiệp vụ:** Nạp một ít khách hàng, công ty, cơ hội mẫu theo ngành và quốc gia để người dùng hình dung sản phẩm; xoá toàn bộ bằng một thao tác khi sẵn sàng dùng dữ liệu thật.

**Vai trò sử dụng chính:** Hệ thống (nạp); người có quyền Quản lý cấu hình workspace (xoá).

**Điều kiện tiên quyết:** Người đăng ký giữ lựa chọn "Nạp dữ liệu mẫu" ở bước tóm tắt (mặc định bật cho kênh tự đăng ký, tắt cho kênh tạo hộ).

**Luồng chính:**

1. Nạp dữ liệu mẫu theo mẫu ngành, tên và tiền tệ theo quốc gia, với Người phụ trách là Chủ sở hữu; mỗi bản ghi mang nhãn "Mẫu" hiển thị trên mọi màn hình.
2. Khi còn dữ liệu mẫu, một thanh thông báo nhắc có thể xoá dữ liệu mẫu bất kỳ lúc nào.
3. Bấm "Xoá dữ liệu mẫu" → hệ thống liệt kê số bản ghi mẫu theo loại, và riêng các bản ghi mẫu **đã bị người dùng sửa hoặc đã gắn với bản ghi thật**.
4. Với nhóm đã sửa hoặc đã gắn, người dùng chọn: xoá cùng, hoặc **giữ lại thành bản ghi thật** (bỏ nhãn Mẫu).
5. Xác nhận → xoá; cấu hình (phễu, quy trình, vai trò, đơn vị) giữ nguyên; thanh thông báo ẩn.

**Luồng ngoại lệ:**

- Xoá gặp lỗi giữa chừng → phần chưa xoá vẫn mang nhãn Mẫu; người dùng thấy số còn lại và có thể xoá tiếp; không bản ghi thật nào bị ảnh hưởng.
- Người dùng tự xoá hết bản ghi mẫu bằng tay → thanh thông báo tự ẩn.

**Quy tắc nghiệp vụ:**

- **`BR-10.1` (Mẫu luôn nhận diện được):** Bản ghi mẫu luôn mang nhãn Mẫu hiển thị, không thể bỏ nhãn trừ qua bước giữ lại ở luồng chính bước 4.

  **Lý do nghiệp vụ:** Nguyên tắc 4; người dùng phải luôn phân biệt được khách hàng thật với khách hàng minh hoạ.

- **`BR-10.2` (Mẫu không tính vào số liệu):** Bản ghi mẫu không tính vào báo cáo, chỉ số, hạn mức của gói, tiêu dùng tính phí, không nhận thông điệp tiếp thị, và không kích hoạt quy tắc tự động hoá gửi ra bên ngoài.

  **Lý do nghiệp vụ:** báo cáo doanh số tháng đầu có cơ hội mẫu là báo cáo sai; tự động hoá gửi email tới địa chỉ mẫu là gửi tới người thật có thể tồn tại.

- **`BR-10.3` (Xoá mẫu không động tới dữ liệu thật):** Thao tác xoá dữ liệu mẫu chỉ xoá bản ghi còn mang nhãn Mẫu; hoạt động, ghi chú, vé hay cơ hội thật gắn với bản ghi mẫu được giữ lại theo lựa chọn ở bước 4, hoặc được gỡ liên kết nếu bản ghi mẫu bị xoá.

  **Lý do nghiệp vụ:** người dùng thường thử nghiệm bằng cách gắn việc thật vào khách hàng mẫu; xoá mẫu mà mất việc thật là mất dữ liệu.

- **`BR-10.4` (Xoá là vĩnh viễn và báo trước):** Xoá dữ liệu mẫu không qua Thùng rác; màn hình xác nhận nêu rõ điều này.

  **Lý do nghiệp vụ:** dữ liệu mẫu không có giá trị khôi phục; đưa vào Thùng rác chỉ làm Thùng rác lẫn dữ liệu thật.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-10.1.1` | Workspace có dữ liệu mẫu | Mở danh sách khách hàng | Bản ghi mẫu mang nhãn "Mẫu" |
| `AC-10.2.1` | Có 5 cơ hội mẫu | Mở báo cáo doanh số | Không tính 5 cơ hội đó |
| `AC-10.2.2` | Chủ sở hữu tạo quy tắc tự động gửi email khi khách hàng chuyển giai đoạn | Chuyển giai đoạn một khách hàng mẫu | Quy tắc không gửi email nào cho bản ghi mẫu |
| `AC-10.3.1` | Người dùng đã gắn một vé hỗ trợ thật vào khách hàng mẫu K | Bấm Xoá dữ liệu mẫu | K xuất hiện trong nhóm "đã gắn với dữ liệu thật" cho chọn giữ hoặc xoá; chọn xoá thì vé vẫn còn và được gỡ liên kết |
| `AC-10.3.2` | Chọn giữ K | Xác nhận | K thành khách hàng thật, không còn nhãn Mẫu; các bản ghi mẫu khác bị xoá |
| `AC-10.4.1` | Màn hình xác nhận xoá | Đọc | Ghi rõ "xoá vĩnh viễn, không vào Thùng rác"; nêu số bản ghi theo loại |
| `AC-10.1.2` | Kênh tạo hộ | Workspace Sẵn sàng | Không có dữ liệu mẫu |

---

## E. LẦN ĐĂNG NHẬP ĐẦU & THIẾT LẬP NHANH

### FEAT-11 — Chào mừng & lộ trình thiết lập nhanh

**Mô tả nghiệp vụ:** Lần đầu vào workspace, Chủ sở hữu được chào mừng và thấy một lộ trình ngắn dẫn tới Đạt kích hoạt sử dụng, với việc đưa đội ngũ vào đứng đầu.

**Vai trò sử dụng chính:** Chủ sở hữu; Quản trị viên (theo `CFG-11-01`).

**Điều kiện tiên quyết:** Workspace Sẵn sàng.

**Luồng chính:**

1. Lần đầu vào, màn hình chào mừng nêu tên người dùng và tên doanh nghiệp, với hai lựa chọn: **Thiết lập đội ngũ ngay** (mở `FEAT-12`) hoặc **Tự khám phá**.
2. Lộ trình thiết lập nhanh hiển thị ở màn hình chính với các nhiệm vụ, sắp theo mục tiêu đã chọn (`BR-03.2`), nhiệm vụ đội ngũ luôn đứng đầu:
   1. **Đưa đội ngũ vào** — hoàn thành khi có ít nhất hai thành viên Đang hoạt động ngoài Chủ sở hữu, vào qua bất kỳ đường nào (lời mời, tệp, liên kết, tự gia nhập) — khớp ngưỡng Đạt kích hoạt sử dụng.
   2. **Kết nối kênh liên lạc đầu tiên** — hoàn thành khi một kênh kết nối thành công.
   3. **Thêm khách hàng thật** — hoàn thành khi có ít nhất một khách hàng không phải dữ liệu mẫu (tạo tay hoặc nhập tệp).
   4. **Tạo cơ hội hoặc vé hỗ trợ đầu tiên** — hoàn thành khi có ít nhất một bản ghi thật.
   5. **Dùng ứng dụng trên điện thoại** — hoàn thành khi Chủ sở hữu đăng nhập lần đầu bằng ứng dụng di động.
3. Mỗi nhiệm vụ mở thẳng màn hình thực hiện; hoàn thành thì được đánh dấu tự động.
4. Người dùng ẩn được lộ trình bất kỳ lúc nào và mở lại từ menu trợ giúp.

**Luồng ngoại lệ:**

- Người dùng hoàn tác sau khi nhiệm vụ đã hoàn thành (ví dụ gỡ người đã mời) → nhiệm vụ vẫn giữ trạng thái hoàn thành.
- Workspace tạo hộ có danh sách người sẽ mời dựng sẵn (`BR-16.4`) → nhiệm vụ "Đưa đội ngũ vào" mở thẳng bước xác nhận gửi danh sách đó; vẫn chỉ hoàn thành theo `BR-11.1`.
- Kết nối kênh liên lạc hoặc tạo biểu mẫu website đầu tiên → đơn vị tiếp nhận theo `BR-11.4`.

**Quy tắc nghiệp vụ:**

- **`BR-11.1` (Hoàn thành theo kết quả, không theo cú bấm):** Nhiệm vụ chỉ được đánh dấu khi kết quả nghiệp vụ đã xảy ra (người được mời đã chấp nhận, kênh đã kết nối), không khi người dùng mới mở màn hình hay mới gửi lời mời.

  **Lý do nghiệp vụ:** lộ trình là thước đo kích hoạt (`KPI-06`); đánh dấu theo cú bấm cho số liệu đẹp mà workspace vẫn chỉ có một người.

- **`BR-11.2` (Mốc đạt được thì giữ):** Nhiệm vụ đã hoàn thành không bị bỏ đánh dấu khi người dùng hoàn tác.

  **Lý do nghiệp vụ:** lộ trình ghi nhận mốc đã đạt; bỏ đánh dấu làm người dùng tưởng mình làm sai.

- **`BR-11.3` (Phần thưởng theo chính sách nhà cung cấp):** Nếu nhà cung cấp có chính sách thưởng khi hoàn thành lộ trình (`PLT-12`), phần thưởng liên quan tới dùng thử được áp theo [`billing-subscription-srs.md`](./billing-subscription-srs.md); không có chính sách thì không hiển thị lời hứa thưởng.

  **Lý do nghiệp vụ:** hứa thưởng mà không có chính sách thương mại đứng sau là lời hứa không giữ được.

- **`BR-11.4` (Đơn vị tiếp nhận của nguồn đầu tiên):** Khi kết nối kênh hay tạo biểu mẫu, đơn vị tiếp nhận được chọn sẵn theo mặc định tìm được theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.12` (mặc định ghi đè của đơn vị người tạo, nếu không thì mặc định workspace mà `FEAT-12` đã đặt). Chưa có mặc định nào: Người có toàn quyền được chọn sẵn đội có vai trò gợi ý tương ứng (Nhân viên Kinh doanh cho biểu mẫu, Nhân viên Hỗ trợ cho kênh) thuộc Đơn vị chính của chính họ hoặc gần nhất bên dưới nó; có nhiều đội ngang hàng thỏa thì không chọn sẵn mà yêu cầu chọn; không có đội nào thì đơn vị gốc; người khác không công khai hay kết nối được nguồn cho tới khi Người có toàn quyền đặt đơn vị tiếp nhận. Khi Người có toàn quyền chọn cho nguồn đầu tiên của một loại mà workspace chưa có mặc định ở cấp workspace và chưa có mặc định ghi đè theo đơn vị nào, màn hình hỏi "Đặt làm đơn vị tiếp nhận mặc định cho mọi [loại nguồn] mới?" (chọn sẵn Có); workspace đã có mặc định ghi đè theo đơn vị thì không hỏi.

  **Lý do nghiệp vụ:** nguồn đầu tiên là lúc dễ đặt sai nhất — khách hàng tiềm năng vào nhầm hàng đợi Hỗ trợ, hay một chi nhánh trở thành mặc định của cả công ty; mặc định đã chốt ở thiết lập đội ngũ phải được dùng lại, và quyết định đặt mặc định chung phải tường minh.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-11.1.1` | Chủ sở hữu vừa gửi 5 lời mời, 1 người đã chấp nhận | Xem lộ trình | Nhiệm vụ "Đưa đội ngũ vào" chưa hoàn thành, ghi "1/2 thành viên đã tham gia, 4 lời mời đang chờ" |
| `AC-11.1.2` | Người thứ hai chấp nhận | Xem lộ trình | Nhiệm vụ được đánh dấu hoàn thành |
| `AC-11.1.5` | 2 người vào qua liên kết mời, không có lời mời đích danh | Xem lộ trình | Nhiệm vụ "Đưa đội ngũ vào" hoàn thành |
| `AC-11.4.1` | Chưa có mặc định nào; đội "Hỗ trợ" có vai trò gợi ý Nhân viên Hỗ trợ | Chủ sở hữu kết nối kênh hộp thư đầu tiên | "Hỗ trợ" được chọn sẵn; màn hình hỏi có đặt làm mặc định cho mọi hộp thư mới không |
| `AC-11.4.2` | `FEAT-12` đã chọn Kinh doanh nhận khách hàng tiềm năng; có cả đội Hỗ trợ | Tạo biểu mẫu website đầu tiên | "Kinh doanh" được chọn sẵn làm đơn vị tiếp nhận, không phải "Hỗ trợ" |
| `AC-11.4.3` | Workspace chưa có mặc định nào cho biểu mẫu | Chủ sở hữu tạo biểu mẫu đầu tiên, chọn "Kinh doanh" | Màn hình hỏi có đặt làm mặc định không; chọn Có thì biểu mẫu sau chọn sẵn "Kinh doanh" |
| `AC-11.4.4` | Workspace có mặc định ghi đè theo chi nhánh | Quản trị viên tạo biểu mẫu đầu tiên cho Hà Nội | Không có câu hỏi đặt mặc định chung; biểu mẫu của Đà Nẵng vẫn chọn sẵn "Kinh doanh – Đà Nẵng" |
| `AC-11.4.5` | Chưa có mặc định nào; thành viên có quyền cấu hình kênh, không có toàn quyền | Tạo biểu mẫu và bấm công khai | Nút công khai bị vô hiệu kèm giải thích cần Người có toàn quyền đặt đơn vị tiếp nhận |
| `AC-11.4.6` | Chưa có mặc định nào; thành viên có quyền cấu hình kênh, không có toàn quyền | Kết nối hộp thư | Thao tác kết nối bị vô hiệu kèm giải thích cần Người có toàn quyền đặt đơn vị tiếp nhận |
| `AC-11.2.1` | Nhiệm vụ "Thêm khách hàng thật" đã hoàn thành | Xoá khách hàng thật duy nhất | Nhiệm vụ vẫn hoàn thành |
| `AC-11.1.3` | Lộ trình đã ẩn | Mở menu trợ giúp → Lộ trình thiết lập | Lộ trình hiển thị lại với đúng trạng thái |
| `AC-11.1.4` | Workspace chỉ có khách hàng mẫu, chưa có khách hàng nào khác | Xem lộ trình | Nhiệm vụ "Thêm khách hàng thật" chưa hoàn thành |
| `AC-11.3.1` | `PLT-12` không có chính sách thưởng | Xem lộ trình | Không có câu nào hứa phần thưởng |

---

### FEAT-12 — Thiết lập đội ngũ nhanh

**Mô tả nghiệp vụ:** Trong tối đa ba màn hình, Chủ sở hữu dựng các đội, mời người vào đúng đội với đúng vai trò, và chọn trưởng nhóm — không cần hiểu ma trận phân quyền. Mọi thao tác dùng đúng quy tắc của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md).

**Vai trò sử dụng chính:** Chủ sở hữu; người có quyền Mời người dùng và Quản lý đơn vị tổ chức.

**Điều kiện tiên quyết:** Workspace Sẵn sàng.

**Luồng chính:**

1. **Màn hình 1 — Đội ngũ của bạn:** hệ thống đề xuất các đội theo mẫu đội ngũ của quy mô và mục tiêu đã khai (`PLT-13`), mỗi đội là một đơn vị tổ chức dưới đơn vị gốc, kèm vai trò gợi ý ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-21.4`) và vai trò của trưởng nhóm. Người dùng đổi tên, bỏ, thêm đội, đổi các vai trò từ danh sách vai trò dựng sẵn. Đội trùng tên với đơn vị đã có được nhận diện là đơn vị đó, không tạo mới. Có thêm dòng **Đồng quản trị**: email của người sẽ được mời với cấp bậc Quản trị viên (chỉ khi người thực hiện là Người có toàn quyền).
   Các câu hỏi cách làm việc, đã chọn sẵn theo `PLT-13`:
   - "Nhân viên trong cùng đội có thấy dữ liệu của nhau không?" — áp lên ô Xem của vai trò gợi ý trên các loại dữ liệu chính của đội (Kinh doanh: Khách hàng, Công ty, Cơ hội; Hỗ trợ: Vé hỗ trợ, Hội thoại). Có → Đơn vị của mình; Không → Chỉ của mình. Không áp lên vai trò Marketing và không ảnh hưởng hàng đợi việc chưa gán ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.11`).
   - "Các đội có thấy vé hỗ trợ của nhau không?" — Có → bật công khai đọc cho Vé hỗ trợ. Khi chọn Không, câu hỏi nêu ngay hệ quả: nhân viên Kinh doanh không thấy vé của khách hàng mình phụ trách nếu vé thuộc đội khác.
   - Khi có đội Kinh doanh: "Khách hàng tiềm năng mới từ biểu mẫu website vào đội nào?" — chọn sẵn Kinh doanh; đội được chọn trở thành đơn vị tiếp nhận mặc định của biểu mẫu website ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-35-01`, `BR-35.12`), khách hàng tiềm năng là bản ghi chờ phân công (`BR-35.10` của tài liệu đó) mà nhân viên Kinh doanh nhận được (ô Gán = Chỉ của mình). Mẫu có chi nhánh đặt **mặc định ghi đè cho từng chi nhánh** (`CFG-35-01`, `BR-35.12` của tài liệu đó): mọi biểu mẫu do người thuộc chi nhánh X (kể cả giám đốc chi nhánh và Marketing của chi nhánh) tạo được chọn sẵn "Kinh doanh – X"; biểu mẫu dùng chung cho cả công ty do Người có toàn quyền chọn đơn vị và phân bổ theo khu vực theo [`contacts-srs.md`](./contacts-srs.md) `FEAT-31`. Cùng đội được đặt làm đơn vị tiếp nhận mặc định của loại nguồn "Liên hệ lại từ chiến dịch" ([`campaigns-srs.md`](./campaigns-srs.md) `BR-35.5`, [`tasks-srs.md`](./tasks-srs.md) `BR-13.6`), để tài khoản gửi do người không có toàn quyền kết nối không phải chờ; mẫu có chi nhánh đặt mặc định ghi đè "Kinh doanh – X" cho từng chi nhánh như với biểu mẫu. Mẫu không có đội Kinh doanh (kể cả quy mô 1 – 10) đặt mặc định của nguồn này là đơn vị gốc. Danh sách nhập từ tệp không qua câu hỏi này: Người phụ trách theo cột Người phụ trách của tệp hoặc là người nhập (`BR-35.10` của tài liệu đó), và việc chuyển khách hàng tiềm năng từ Marketing sang Kinh doanh theo quy tắc phân bổ của [`contacts-srs.md`](./contacts-srs.md) `FEAT-31`.
   - Khi có đội Hỗ trợ: đội có vai trò gợi ý Nhân viên Hỗ trợ trở thành đơn vị tiếp nhận mặc định của kênh hội thoại và hộp thư hỗ trợ (không hỏi thêm; mẫu có chi nhánh thì đặt mặc định ghi đè "Hỗ trợ – X" cho từng chi nhánh như trên). Giá trị hiển thị ở màn hình 3.
   - Mẫu có chi nhánh: mỗi chi nhánh có dòng nhập email giám đốc (tuỳ chọn). Người này được mời với vai trò Quản lý, Đơn vị chính là đơn vị chi nhánh, là người phụ trách đơn vị chi nhánh và là quản lý trực tiếp của các trưởng nhóm trong chi nhánh — nhờ đó thấy và phân việc được trong cả chi nhánh qua vai trò Quản lý (Đơn vị và các đơn vị con) và chuỗi cấp dưới, không cần bật tham số áp cho toàn workspace. Lời mời của giám đốc chi nhánh gửi trước cùng trưởng nhóm và đồng quản trị.
   - Mẫu có chi nhánh có đơn vị trung tâm: hai lựa chọn riêng "Biểu mẫu do đơn vị trung tâm tạo vào đội nào?" và "Kênh do đơn vị trung tâm kết nối vào đội nào?" — chọn trong các đội đã đề xuất (không chọn sẵn với biểu mẫu, để một chi nhánh không vô tình thành nơi nhận của cả công ty; kênh chọn sẵn Chăm sóc khách hàng trung tâm nếu có, nếu không thì cũng không chọn sẵn; bỏ trống thì nguồn của đơn vị trung tâm theo `BR-11.4`); câu trả lời là mặc định cấp workspace, chỉ áp cho đơn vị không thuộc chi nhánh nào vì mỗi chi nhánh có mặc định ghi đè riêng.
   - Khi có đội Marketing, hai câu riêng, mỗi câu chỉ hiện khi gói có tính năng tương ứng (không có thì hiển thị vô hiệu kèm lý do): "Nhân viên Marketing được nhập danh sách khách hàng không?" (Có → vai trò dựng sẵn Marketing: (Khách hàng, Tạo) = Có, (Khách hàng, Nhập), (Khách hàng, Sửa) và (Khách hàng, Gán) = Chỉ của mình — để sửa lỗi danh sách tự nhập và gán khách hàng tiềm năng cho Kinh doanh; Quản lý Marketing đã có các ô này ở mức Đơn vị của mình) và "Nhân viên Marketing được phát sóng chiến dịch của mình không?" (Có → (Chiến dịch, Phát sóng) = Chỉ của mình cho vai trò Marketing; vai trò Quản lý của các đội khác không đổi; đối tượng nhận nằm trong phần giao mức Xem Khách hàng của người chọn đối tượng và của người khởi chạy theo [`campaigns-srs.md`](./campaigns-srs.md) `BR-05.4`; mọi phê duyệt và sàn của [`campaigns-srs.md`](./campaigns-srs.md) vẫn áp). Mặc định cả hai là Không; vai trò Quản lý Marketing đã có sẵn Phát sóng trong đơn vị theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`. Màn hình ghi rõ phạm vi xem khách hàng **hiện tại** của Marketing và Quản lý Marketing (mặc định "xem được toàn bộ khách hàng của workspace và chọn đối tượng chiến dịch từ đó"), kèm lựa chọn **Thu về khách hàng của đơn vị mình** — áp cho cả hai vai trò — và hệ quả của nó ("đối tượng chiến dịch chỉ còn khách hàng thuộc đội Marketing, cộng phần công khai đọc, chính sách Cho phép hay đơn vị mà người đó phụ trách nếu có, nên có thể gần như rỗng nếu Marketing không tự nhập danh sách").
   Khi chạy lại thiết lập mà đổi câu trả lời, màn hình hiển thị trước số người đang giữ vai trò bị ảnh hưởng trước khi áp. Câu trả lời được áp bằng điều chỉnh ô vai trò dựng sẵn, mức nền và đơn vị tiếp nhận theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-29.4`, `FEAT-34`, `BR-35.12`, chỉ khi người thực hiện là Người có toàn quyền; người khác thấy các câu hỏi ở dạng chỉ đọc kèm giải thích "Chỉ Chủ sở hữu hoặc Quản trị viên thay đổi được".
2. **Màn hình 2 — Mời người:** với mỗi đội, dán danh sách email; tuỳ chọn đánh dấu một người là **trưởng nhóm**. Email bị lỗi (sai định dạng, ngoài tên miền được phép, đã là thành viên) được báo ngay khi dán. Một email xuất hiện ở hai đội: đội đứng trên trong danh sách ở màn hình 2 là Đơn vị chính, đội kia là Đơn vị kiêm nhiệm mức **Chỉ xem** kèm vai trò gợi ý của cả hai đội — đủ để nhận việc từ hàng đợi của đội kia ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.11`); màn hình 3 cho đổi Đơn vị chính giữa hai đội và — chỉ Người có toàn quyền — đổi kiêm nhiệm sang Đầy đủ, kèm cảnh báo "Các bản ghi người này phụ trách sẽ hiện cho đội X và người quản lý đội X sửa được". Có lựa chọn thay thế: tạo liên kết mời cho đội ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-50`), mời từ tệp (`FEAT-44` của tài liệu đó, luôn khả dụng trừ khi `BR-08.6` đang chặn lời mời), hoặc bật tự gia nhập khi đã có tên miền email đã xác minh (`FEAT-20`, chỉ Người có toàn quyền).
3. **Màn hình 3 — Xem lại và gửi:** bảng tóm tắt: đội, số người, vai trò, trưởng nhóm, đồng quản trị, câu trả lời các câu hỏi cách làm việc (gồm Marketing xem được bao nhiêu khách hàng); với người ở hai đội, đổi được Đơn vị chính và (Người có toàn quyền) mức kiêm nhiệm; các dòng vượt trần năng lực của người thực hiện hoặc vượt hạn mức người dùng được đánh dấu kèm lý do; với workspace tự đăng ký đang dùng thử, số lời mời gửi ngay và số được xếp hàng gửi dần (`BR-14.3`), kèm gợi ý xác minh tên miền email (`FEAT-20`) để nới giới hạn khi số người vượt giới hạn 24 giờ — lời mời của giám đốc chi nhánh, trưởng nhóm và đồng quản trị luôn gửi trước; danh sách lớn được tự chia thành nhiều lượt mời theo giới hạn IAM `NFR-09` ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md)); bấm Gửi.
4. Hệ thống tạo các đơn vị, gửi lời mời với Đơn vị chính và vai trò gợi ý của đội; với trưởng nhóm: vai trò trưởng nhóm đã chọn, là người phụ trách chính của đơn vị và là quản lý trực tiếp của các thành viên trong đội — hai quan hệ này có hiệu lực khi trưởng nhóm đã chấp nhận ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-09.4`, `BR-21.2`).

**Luồng ngoại lệ:**

- Bỏ qua thiết lập → không tạo gì; nhiệm vụ "Đưa đội ngũ vào" vẫn mở trên lộ trình.
- Vượt hạn mức người dùng của gói → theo chính sách chạm trần của gói ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-09.8`): chặn các dòng vượt kèm lối nâng gói, hoặc báo trước phần phí vượt; các dòng khác vẫn gửi được.
- Dòng vượt trần năng lực của người thực hiện (ví dụ người chỉ có quyền Mời người dùng chọn vai trò trưởng nhóm mạnh hơn mình), hoặc dòng đặt trưởng nhóm làm người phụ trách đơn vị khi tham số `CFG-34-02` của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) bật mà người thực hiện không có toàn quyền ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-21.6`) → dòng đó không gửi được, các dòng khác vẫn gửi.
- Trưởng nhóm hoặc giám đốc chi nhánh từ chối, lời mời hết hạn, hoặc lời mời Chờ gửi bị huỷ ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-09.11`) → vị trí người phụ trách đơn vị trở về trống, quan hệ quản lý trực tiếp bị gỡ, Chủ sở hữu được thông báo và `FEAT-03` của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) hiện cảnh báo.
- Mẫu quy mô 1 – 10: không đề xuất đội; mọi người được mời vào đơn vị gốc với vai trò chung theo `PLT-13` (mặc định Nhân viên Kinh doanh), đổi được; câu hỏi "thấy dữ liệu của nhau" mặc định Có.
- Người thực hiện không có quyền Quản lý đơn vị tổ chức → màn hình 1 chỉ cho chọn đơn vị đã có.

**Quy tắc nghiệp vụ:**

- **`BR-12.1` (Thiết lập nhanh là lối tắt, không là quy tắc riêng):** Mọi đơn vị, lời mời, vai trò, trưởng nhóm tạo ra ở đây là đối tượng thường của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), chịu đủ quy tắc của nó (trần năng lực, chấp nhận lời mời, hạn mức người dùng); sửa được về sau ở khu quản trị phân quyền.

  **Lý do nghiệp vụ:** Nguyên tắc 6; một đường tắt với quy tắc riêng là một lỗ hổng phân quyền.

- **`BR-12.2` (Đội là đơn vị tổ chức):** Mỗi đội trong thiết lập nhanh là một đơn vị tổ chức, không phải một nhóm.

  **Lý do nghiệp vụ:** phạm vi dữ liệu theo đơn vị (`FEAT-34` của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md)); tạo "đội" bằng nhóm làm mọi người cùng đơn vị gốc và thấy dữ liệu của nhau, trái kỳ vọng "đội Kinh doanh không thấy việc của đội khác".

- **`BR-12.3` (Quan hệ trưởng nhóm có hiệu lực khi cả hai đã vào):** Quan hệ quản lý trực tiếp giữa trưởng nhóm và thành viên được ghi ngay nhưng chỉ có hiệu lực về phạm vi dữ liệu khi cả hai đã chấp nhận lời mời.

  **Lý do nghiệp vụ:** trưởng nhóm và thành viên thường được mời cùng lúc; chờ từng người chấp nhận rồi mới gán quản lý là thêm một vòng việc thủ công.

- **`BR-12.4` (Không quá ba màn hình):** Thiết lập nhanh không có quá ba màn hình và không có trường bắt buộc nào ngoài email.

  **Lý do nghiệp vụ:** mục tiêu là lời mời đầu tiên trong 10 phút (`KPI-05`); mỗi màn hình thêm là một điểm bỏ dở.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-12.1.1` | Quy mô 11 – 50, mục tiêu bán hàng và chăm sóc khách hàng | Mở thiết lập đội ngũ | Đề xuất đội "Kinh doanh" (Nhân viên Kinh doanh) và "Hỗ trợ" (Nhân viên Hỗ trợ) |
| `AC-12.1.2` | Dán 15 email vào Kinh doanh, 8 vào Hỗ trợ, đánh dấu 1 trưởng nhóm mỗi đội | Gửi | 2 đơn vị được tạo; 23 lời mời Đang chờ chấp nhận; trưởng nhóm được ghi là người phụ trách đơn vị, có hiệu lực khi họ chấp nhận |
| `AC-12.2.1` | Tình huống AC-12.1.2, mọi người đã chấp nhận, câu hỏi "các đội thấy vé của nhau" = Có | Một nhân viên Kinh doanh mở danh sách cơ hội và danh sách vé | Không thấy cơ hội của nhân viên Hỗ trợ; thấy vé của đội Hỗ trợ ở chế độ chỉ xem |
| `AC-12.3.1` | Trưởng nhóm chưa chấp nhận, một thành viên đã chấp nhận | Trưởng nhóm chấp nhận | Từ lúc đó trưởng nhóm là người phụ trách đơn vị và thấy dữ liệu của thành viên theo mức của vai trò trưởng nhóm |
| `AC-12.1.3` | Workspace sẽ vượt hạn mức người dùng ở 5 email cuối; chính sách chạm trần là chặn | Xem màn hình 3 | 5 dòng báo vượt hạn mức kèm lối nâng gói; các dòng còn lại gửi được |
| `AC-12.1.5` | Cùng tình huống; chính sách là cho thêm và tính phí vượt | Xem màn hình 3 | 5 dòng được đánh dấu phát sinh phí vượt, gửi được sau khi xác nhận |
| `AC-12.4.1` | Đếm số màn hình | Đi từ đầu tới khi gửi | Không quá 3 màn hình; không trường bắt buộc nào ngoài email |
| `AC-12.1.4` | Quy mô 1 – 10 | Mở thiết lập đội ngũ | Không đề xuất đội; có ô dán email, vai trò chung Nhân viên Kinh doanh chọn sẵn, câu hỏi "thấy dữ liệu của nhau" = Có |
| `AC-12.1.6` | Một email được dán vào đội Kinh doanh (đứng trên) và đội Hỗ trợ | Gửi | Người đó có Đơn vị chính Kinh doanh, kiêm nhiệm Hỗ trợ mức Chỉ xem, vai trò Nhân viên Kinh doanh và Nhân viên Hỗ trợ; nhận đúng một lời mời; nhận được vé từ hàng đợi Hỗ trợ; khách hàng của người đó không hiện cho đội Hỗ trợ |
| `AC-12.1.7` | Đã chạy thiết lập một lần, có đơn vị "Kinh doanh" | Mở lại thiết lập đội ngũ | Đội "Kinh doanh" được nhận diện là đơn vị đã có; không tạo đơn vị trùng |
| `AC-12.1.8` | Người thực hiện chỉ có quyền Mời người dùng | Chọn vai trò trưởng nhóm Quản lý, mạnh hơn năng lực của mình | Màn hình 3 đánh dấu dòng trưởng nhóm không gửi được, nêu ô vượt trần |
| `AC-12.3.2` | Trưởng nhóm để lời mời hết hạn | — | Đơn vị không còn người phụ trách; Chủ sở hữu nhận thông báo |
| `AC-12.1.9` | Workspace tự đăng ký đang dùng thử, dán 80 email, giới hạn `PLT-11` là 50 mỗi giờ | Xem màn hình 3 | Ghi "50 gửi ngay, 30 được xếp hàng gửi dần trong giờ tới" |
| `AC-12.1.10` | Người thực hiện là thành viên có quyền Mời người dùng, không có toàn quyền | Mở màn hình 1 | Mọi câu hỏi cách làm việc và dòng Đồng quản trị hiển thị chỉ đọc kèm giải thích |
| `AC-12.1.11` | Có đội Kinh doanh và Marketing, gói có cả hai tính năng | Trả lời Có cho cả hai câu Marketing, giữ Kinh doanh nhận khách hàng tiềm năng từ biểu mẫu | Vai trò Marketing có Tạo, Nhập, Sửa, Gán và Phát sóng mức Chỉ của mình; Quản lý Marketing có các ô đó ở mức Đơn vị của mình; khách hàng tiềm năng từ biểu mẫu vào hàng đợi của Kinh doanh; danh sách Marketing tự nhập do người nhập phụ trách |
| `AC-12.1.12` | Gói có nhập hàng loạt nhưng chưa có phát sóng | Mở màn hình 1 | Câu về nhập trả lời được; câu về phát sóng bị vô hiệu kèm lý do |
| `AC-12.1.13` | Chạy lại thiết lập, đổi "thấy dữ liệu của nhau" từ Có sang Không | Bấm áp | Màn hình hiển thị "18 người đang giữ Nhân viên Kinh doanh bị ảnh hưởng" trước khi xác nhận |
| `AC-12.1.14` | Mẫu 11 – 200, không chọn mục tiêu tiếp thị | Mở màn hình 1 | Vẫn có câu "Khách hàng tiềm năng mới từ biểu mẫu website vào đội nào?", chọn sẵn Kinh doanh |
| `AC-12.1.15` | Có đội Marketing | Kiểm tra vai trò sau khi áp | Quản lý Marketing có Phát sóng Đơn vị của mình; vai trò Quản lý của trưởng nhóm Kinh doanh và Hỗ trợ không có Phát sóng |
| `AC-12.1.16` | Khách hàng tiềm năng mới vào hàng đợi Kinh doanh từ biểu mẫu | Nhân viên Kinh doanh mở hàng đợi, bấm Nhận việc | Nhận được; khách hàng tiềm năng chuyển theo người nhận (bản ghi chờ phân công) |
| `AC-12.1.17` | Người thực hiện là thành viên có quyền Mời người dùng | Ở màn hình 3, thử đổi kiêm nhiệm của một người sang Đầy đủ | Lựa chọn bị vô hiệu kèm giải thích; đổi Đơn vị chính giữa hai đội thì làm được |
| `AC-12.1.18` | Có đội Marketing | Xem màn hình 1 và 3 | Có dòng "Nhân viên Marketing xem được toàn bộ khách hàng" kèm lối thu về đơn vị mình |
| `AC-12.1.19` | Quy mô trên 200, khai 3 chi nhánh Hà Nội, Đà Nẵng, TP.HCM, không chọn đơn vị trung tâm | Mở màn hình 1 | Đề xuất 3 đơn vị chi nhánh, dưới mỗi chi nhánh các đội "Kinh doanh – Hà Nội", "Hỗ trợ – Hà Nội"…; không có hai đơn vị trùng tên; có dòng email giám đốc mỗi chi nhánh; mỗi chi nhánh có mặc định ghi đè riêng, không có mặc định cấp workspace |
| `AC-12.1.20` | Mẫu chi nhánh đã áp; một người có quyền cấu hình kênh, Đơn vị chính "Hỗ trợ – Đà Nẵng", tạo hộp thư hỗ trợ | Chọn đơn vị tiếp nhận | "Hỗ trợ – Đà Nẵng" được chọn sẵn; vé của hộp thư này không hiện cho "Hỗ trợ – Hà Nội" |
| `AC-12.1.21` | Có đội Marketing, đã chọn Thu về khách hàng của đơn vị mình | Xem màn hình 3 | Dòng phạm vi hiển thị "chỉ khách hàng do đội Marketing phụ trách" kèm cảnh báo đối tượng có thể gần như rỗng; cả Marketing và Quản lý Marketing có (Khách hàng, Xem) = Đơn vị của mình |
| `AC-12.1.22` | Mẫu chi nhánh; nhập email giám đốc chi nhánh Đà Nẵng | Gửi, giám đốc và các nhân viên chấp nhận | Giám đốc có vai trò Quản lý, là người phụ trách đơn vị chi nhánh Đà Nẵng và quản lý trực tiếp của các trưởng nhóm; thấy dữ liệu và gán được việc chưa gán của mọi đội dưới chi nhánh; tham số người phụ trách đơn vị xem toàn nhánh không bị bật |
| `AC-12.1.23` | Mẫu chi nhánh; giám đốc chi nhánh Đà Nẵng tạo biểu mẫu website | Chọn đơn vị tiếp nhận | "Kinh doanh – Đà Nẵng" được chọn sẵn; nhân viên "Kinh doanh – Đà Nẵng" thấy và nhận được khách hàng tiềm năng |
| `AC-12.1.24` | Mẫu chi nhánh có "Marketing trung tâm"; chọn biểu mẫu do đơn vị trung tâm tạo vào "Kinh doanh – Hà Nội" | Nhân viên Marketing trung tâm tạo biểu mẫu | "Kinh doanh – Hà Nội" được chọn sẵn; biểu mẫu do chi nhánh Đà Nẵng tạo vẫn chọn sẵn "Kinh doanh – Đà Nẵng" |
| `AC-12.1.25` | Workspace đã trả phí, hạn mức người dùng còn đủ 250 người; dán 250 email cho mẫu trên 200 người | Gửi | Hệ thống tự chia thành 3 lượt mời; cả 250 người nhận lời mời ngay |
| `AC-12.1.26` | Workspace tự đăng ký đang dùng thử, 320 người được mời, chưa xác minh tên miền | Xem màn hình 3 | Thấy riêng số gửi ngay, số xếp hàng gửi dần và số sẽ tạm dừng chờ nhân sự vận hành xem xét theo `BR-14.3`, kèm gợi ý xác minh tên miền email để nới giới hạn |
| `AC-12.1.27` | Mẫu chi nhánh có "Marketing trung tâm" và "Chăm sóc khách hàng trung tâm" | Mở câu hỏi đội nhận nguồn của đơn vị trung tâm | Lựa chọn cho biểu mẫu để trống; lựa chọn cho kênh chọn sẵn "Chăm sóc khách hàng trung tâm" |
| `AC-12.1.28` | Có đội Kinh doanh và Marketing, chọn Kinh doanh nhận khách hàng tiềm năng từ biểu mẫu; một thành viên có quyền Quản lý kênh gửi tiếp thị, không có toàn quyền | Thành viên đó kết nối tài khoản gửi chiến dịch đầu tiên | Đơn vị tiếp nhận của công việc liên hệ lại được chọn sẵn "Kinh doanh"; không phải chờ Người có toàn quyền |
| `AC-12.1.29` | Quy mô 1 – 10, không chia đội | Hoàn tất thiết lập đội ngũ | Đơn vị tiếp nhận mặc định của nguồn "Liên hệ lại từ chiến dịch" là đơn vị gốc |

---

### FEAT-13 — Hướng dẫn tương tác theo màn hình

**Mô tả nghiệp vụ:** Lần đầu mở một khu vực chính (hộp thư đa kênh, bảng cơ hội, báo cáo), người dùng được giới thiệu ngắn các điểm cần biết.

**Vai trò sử dụng chính:** Mọi thành viên.

**Điều kiện tiên quyết:** `CFG-13-01` bật.

**Luồng chính:**

1. Lần đầu mở một khu vực chính → hướng dẫn tối đa 3 bước (hằng số, Phụ lục B), có nút Bỏ qua ở mọi bước.
2. Đã xem hoặc đã bỏ qua → không hiển thị lại ở khu vực đó; xem lại được từ menu trợ giúp.

**Quy tắc nghiệp vụ:**

- **`BR-13.1` (Ghi nhớ theo tài khoản):** Trạng thái đã xem lưu theo tài khoản trong workspace, không theo thiết bị.

  **Lý do nghiệp vụ:** hướng dẫn lặp lại trên mỗi thiết bị mới là phiền toái và làm người dùng tắt hết mọi hướng dẫn.

- **`BR-13.2` (Chỉ hướng dẫn điều người dùng làm được):** Hướng dẫn chỉ giới thiệu thao tác mà người dùng có quyền thực hiện.

  **Lý do nghiệp vụ:** chỉ cho người dùng một nút họ bấm vào thì bị từ chối là tạo cảm giác hệ thống lỗi.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-13.1.1` | A đã xem hướng dẫn bảng cơ hội trên máy tính | Mở bảng cơ hội trên điện thoại | Không hiển thị lại |
| `AC-13.2.1` | A không có quyền Xoá cơ hội | Xem hướng dẫn bảng cơ hội | Không có bước giới thiệu thao tác xoá |
| `AC-13.1.2` | Đang ở bước 1 | Bấm Bỏ qua | Hướng dẫn đóng; không hiển thị lại ở khu vực đó |

---

## F. THÀNH VIÊN MỚI

### FEAT-14 — Thư mời mang nhận diện doanh nghiệp

**Mô tả nghiệp vụ:** Thư mời cho người được mời biết rõ ai mời, vào workspace nào, vào đội nào với vai trò gì — và không thể bị dùng để giả mạo.

**Vai trò sử dụng chính:** Hệ thống.

**Điều kiện tiên quyết:** Có lời mời theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-09`.

**Luồng chính:**

1. Gửi thư với tiêu đề nêu tên người mời và tên workspace; nội dung nêu đơn vị, vai trò, hạn của lời mời, địa chỉ của workspace (tên miền phụ hoặc tên miền riêng) và nút Chấp nhận.
2. Logo doanh nghiệp hiển thị trong thư khi `CFG-14-01` bật và workspace đã đạt điều kiện `BR-14.2`.
3. Thư gửi từ địa chỉ của nền tảng, tên người gửi dạng "[Tên người mời] qua [Tên nền tảng]".

**Quy tắc nghiệp vụ:**

- **`BR-14.1` (Thông tin đủ để quyết định):** Thư nêu tên người mời, tên workspace, đơn vị, vai trò và hạn của lời mời.

  **Lý do nghiệp vụ:** người được mời cần biết đây là lời mời thật từ người quen trước khi bấm.

- **`BR-14.2` (Nhận diện doanh nghiệp chỉ sau khi xác minh):** Logo và màu sắc tuỳ chỉnh chỉ xuất hiện trong thư khi workspace đã có tên miền email đã xác minh (`FEAT-20`) hoặc đã chuyển sang trả phí; trước đó thư dùng mẫu chuẩn của nền tảng. Tên workspace và tên người mời luôn hiển thị kèm địa chỉ của workspace để người nhận đối chiếu được.

  **Lý do nghiệp vụ:** cho mọi workspace dùng thử tự đặt logo là cho kẻ gian tạo thư mời mang logo ngân hàng, gửi qua địa chỉ uy tín của nền tảng.

- **`BR-14.3` (Hạn chế gửi cho workspace dùng thử):** Workspace tự đăng ký đang dùng thử (không áp cho workspace tạo hộ theo hợp đồng) chịu giới hạn số lời mời trong 1 giờ và trong **24 giờ trượt** (`PLT-11`). Phần vượt giới hạn giờ được **xếp hàng gửi dần**; lời mời xếp hàng ở trạng thái Chờ gửi theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-09.11`. Vượt giới hạn 24 giờ thì hàng đợi tạm dừng; trong `PLT-20` nhân sự vận hành nền tảng chọn **tiếp tục** (có thể nới giới hạn cho riêng hàng đợi đó tới khi hết, ghi lý do — điều kiện nới thứ ba bên cạnh hai điều kiện dưới đây) hoặc **huỷ** các lời mời Chờ gửi và báo Chủ sở hữu kèm lý do; không xử lý trong hạn thì hàng đợi tiếp tục ở giới hạn thường; mỗi lần chạm lại giới hạn 24 giờ, hàng đợi lại tạm dừng chờ xem xét. Ngoài lượt nới của nhân sự vận hành cho riêng một hàng đợi nói trên, giới hạn chỉ được nới (lên mức thứ hai của `PLT-11`) khi workspace có tên miền email đã xác minh (`FEAT-20`) hoặc khi nhân sự kinh doanh đã xác nhận khách hàng thật ở `FEAT-05` — không nới chỉ vì quy mô tự khai.

  **Lý do nghiệp vụ:** chặn dùng tính năng mời làm kênh gửi thư rác qua địa chỉ uy tín của nền tảng, mà không làm doanh nghiệp thật bị từ chối hay bị treo vô thời hạn khi mời cả đội. Rủi ro còn lại — tối đa giới hạn 24 giờ thư mỗi ngày nếu không ai xử lý — được chấp nhận có chủ đích; tính theo 24 giờ trượt để không phụ thuộc múi giờ do chính người đăng ký đặt.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-14.1.1` | A mời B vào đơn vị "Kinh doanh", vai trò Nhân viên Kinh doanh | B mở thư | Thấy tên A, tên workspace, "Kinh doanh", "Nhân viên Kinh doanh", hạn lời mời, nút Chấp nhận |
| `AC-14.2.1` | Workspace dùng thử, chưa xác minh tên miền, đã tải logo | Gửi lời mời | Thư dùng mẫu chuẩn, không có logo |
| `AC-14.2.2` | Workspace đã xác minh tên miền | Gửi lời mời | Thư có logo doanh nghiệp |
| `AC-14.3.1` | Workspace dùng thử đã gửi tới giới hạn giờ của `PLT-11` | Gửi thêm 10 lời mời | 10 lời mời hiển thị Chờ gửi kèm thời điểm dự kiến; người nhận chưa có thư cho tới lúc đó |
| `AC-14.3.2` | Workspace dùng thử chọn quy mô "trên 200", chưa xác minh tên miền, chưa được kinh doanh xác nhận | Gửi 200 lời mời | Giới hạn giờ vẫn ở mức thường; phần vượt xếp hàng |
| `AC-14.3.3` | Workspace dùng thử chạm giới hạn ngày | Hàng đợi | Tạm dừng; Chủ sở hữu thấy thông báo hàng đợi đang được xem xét |
| `AC-14.3.4` | Hàng đợi tạm dừng, nhân sự vận hành chọn huỷ | — | Các lời mời Chờ gửi bị huỷ; Chủ sở hữu nhận thông báo kèm lý do |
| `AC-14.3.5` | Hàng đợi tạm dừng, quá `PLT-20` không ai xử lý | Ngày hôm sau | Hàng đợi tự chạy tiếp ở giới hạn thường |
| `AC-14.3.6` | Hàng đợi tạm dừng, nhân sự vận hành chọn tiếp tục có nới | — | Các lời mời Chờ gửi của hàng đợi đó được gửi; lượt nới không áp cho lượt mời sau; nhật ký ghi lý do |
| `AC-14.3.7` | Workspace tạo hộ, Chủ sở hữu xác nhận gửi 300 lời mời dựng sẵn | Gửi | Cả 300 lời mời được gửi ngay, không xếp hàng |
| `AC-14.2.3` | Workspace dùng thử đặt tên giống tên một ngân hàng | Người nhận mở thư mời | Thư dùng mẫu chuẩn, hiển thị địa chỉ thật của workspace bên cạnh tên |

---

### FEAT-15 — Chấp nhận lời mời & chào mừng thành viên mới

**Mô tả nghiệp vụ:** Người được mời chấp nhận lời mời trong một bước — dù chưa có tài khoản, đã có tài khoản hay dùng tài khoản Google/Microsoft — rồi vào thẳng màn hình làm việc với đúng đơn vị và vai trò.

**Vai trò sử dụng chính:** Thành viên được mời.

**Điều kiện tiên quyết:** Lời mời Đang chờ chấp nhận và còn hạn.

**Luồng chính:**

1. Bấm Chấp nhận trong thư.
2. Theo trường hợp:
   - **Chưa có tài khoản:** dùng Google/Microsoft với đúng email được mời (thỏa `BR-01.5`), hoặc đặt mật khẩu và nhập mã xác minh hệ thống vừa gửi tới email được mời (`BR-15.4`).
   - **Đã có tài khoản:** đăng nhập (nếu chưa), thấy màn hình "Tham gia [Tên workspace]" với nút Chấp nhận / Từ chối.
3. Chấp nhận → thẻ chào mừng: tên workspace, đơn vị, quản lý trực tiếp (nếu có), vai trò, và một câu "bạn làm được gì" theo vai trò.
4. Vào thẳng màn hình làm việc chính của vai trò; hướng dẫn tương tác (`FEAT-13`) bắt đầu.

**Luồng ngoại lệ:**

- Đang đăng nhập bằng tài khoản khác email được mời → yêu cầu chuyển sang đúng tài khoản; không chấp nhận được bằng tài khoản khác.
- Lời mời hết hạn hoặc bị thu hồi → báo rõ, gợi ý liên hệ người mời; người mời được thông báo có người bấm lời mời đã hết hạn.
- Người được mời từ chối → theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-09`.

**Quy tắc nghiệp vụ:**

- **`BR-15.1` (Chấp nhận bằng đúng danh tính được mời):** Lời mời chỉ được chấp nhận bởi tài khoản mang đúng email được mời.

  **Lý do nghiệp vụ:** thư mời bị chuyển tiếp không được thành vé vào workspace cho người khác; muốn mời người khác thì mời đích danh (hoặc dùng liên kết mời có giới hạn).

- **`BR-15.2` (Không màn hình trống):** Thành viên mới vào màn hình chính phù hợp với vai trò; nếu chưa có bản ghi nào trong phạm vi, màn hình nêu bước đầu tiên nên làm thay vì một danh sách trống.

  **Lý do nghiệp vụ:** màn hình trống ngày đầu làm người dùng nghĩ mình chưa được cấp quyền và báo lỗi.

- **`BR-15.3` (Một bước cho người đã có tài khoản):** Người đã có tài khoản và đang đăng nhập đúng tài khoản chỉ cần một lần bấm để tham gia.

  **Lý do nghiệp vụ:** tư vấn viên, đối tác làm việc với nhiều doanh nghiệp; mỗi lần tham gia không được là một lần đăng ký.

- **`BR-15.4` (Thư mời chuyển tiếp không tạo được tài khoản):** Nút Chấp nhận trong thư dùng được nhiều lần cho tới khi lời mời được chấp nhận; mở liên kết không có tác dụng phụ nào. Người chưa có tài khoản phải chứng minh sở hữu email được mời ngay lúc chấp nhận (mã mới gửi tới hộp thư đó, hoặc nhà cung cấp danh tính thỏa `BR-01.5`).

  **Lý do nghiệp vụ:** có được thư không chứng minh là chủ hộp thư, nên mã mới là điều kiện; còn bộ quét liên kết của hệ thống thư doanh nghiệp tự mở liên kết, nếu liên kết bị tiêu thụ khi mở thì cả công ty nhận lời mời hỏng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-15.1.1` | Lời mời gửi b@congty.vn; trình duyệt đang đăng nhập c@khac.vn | Bấm Chấp nhận | Yêu cầu đăng nhập bằng b@congty.vn; không chấp nhận được bằng c@khac.vn |
| `AC-15.3.1` | b@congty.vn đã có tài khoản, đang đăng nhập | Bấm Chấp nhận trong thư | Một màn hình xác nhận; bấm Chấp nhận là vào workspace |
| `AC-15.2.1` | Nhân viên Kinh doanh mới, chưa có khách hàng nào | Vào workspace | Thấy thẻ chào mừng và gợi ý bước đầu tiên, không thấy danh sách trống không giải thích |
| `AC-15.1.2` | Lời mời đã hết hạn | Bấm Chấp nhận | Báo lời mời hết hạn, gợi ý liên hệ người mời; người mời nhận thông báo |
| `AC-15.4.1` | Chưa có tài khoản | Chọn dùng Google với đúng email được mời | Vào workspace không cần mã xác minh |
| `AC-15.4.2` | Thư mời của b@congty.vn bị chuyển tiếp cho người khác | Người khác bấm Chấp nhận, chọn đặt mật khẩu | Được yêu cầu mã gửi tới b@congty.vn; không nhập được thì không tạo được tài khoản |
| `AC-15.4.3` | Bộ quét thư đã tự mở liên kết; người nhận bấm Chấp nhận sau đó | Hoàn tất chấp nhận | Thành công; liên kết chỉ hết hiệu lực sau khi chấp nhận hoàn tất |

---

## G. TẠO HỘ KHÁCH HÀNG DOANH NGHIỆP

### FEAT-16 — Tạo hộ workspace cho khách hàng doanh nghiệp

**Mô tả nghiệp vụ:** Nhân sự vận hành nền tảng tạo workspace cho khách hàng theo hợp đồng: gói, tên miền phụ, cấu hình khởi điểm và Chủ sở hữu do khách hàng chỉ định; Chủ sở hữu nhận lời mời kích hoạt.

**Vai trò sử dụng chính:** Nhân sự vận hành nền tảng.

**Điều kiện tiên quyết:** Có hợp đồng hoặc đơn đặt hàng; có email Chủ sở hữu do khách hàng cung cấp bằng văn bản ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-01.3`).

**Luồng chính:**

1. Nhập tên doanh nghiệp, mã số thuế, quy mô, ngành, ngôn ngữ, múi giờ, tiền tệ, tên miền phụ (theo `FEAT-06`), gói và điều khoản theo [`billing-subscription-srs.md`](./billing-subscription-srs.md), các tính năng mở rộng ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-05`), email Chủ sở hữu kèm căn cứ.
2. Hệ thống kiểm tra trùng: doanh nghiệp đã có workspace (cùng mã số thuế, cùng tên miền email, hoặc cùng email Chủ sở hữu) → cảnh báo; chỉ tạo tiếp khi nhân sự nhập lý do.
3. Khởi tạo theo `FEAT-08`, không nạp dữ liệu mẫu theo mặc định.
4. Tuỳ chọn **giai đoạn triển khai trước kích hoạt** (`BR-16.4`): nhân sự triển khai của nhà cung cấp dựng sẵn cây đơn vị, vai trò, nhóm, mẫu ngành và danh sách người sẽ mời theo yêu cầu bằng văn bản của khách hàng.
5. Chủ sở hữu nhận lời mời kích hoạt; chấp nhận theo `FEAT-15`, xem tóm tắt những gì đã được dựng sẵn — nổi bật các vai trò tự tạo có ô mức Toàn workspace, các ô vai trò dựng sẵn đã điều chỉnh, mức nền và công khai đọc, đơn vị tiếp nhận của các nguồn, việc bật người phụ trách đơn vị xem toàn nhánh, dòng cấp bậc Quản trị viên và email ngoài tên miền công ty; lô nhập soạn sẵn; Chủ sở hữu xác nhận hoặc bác **từng mục soạn sẵn** — mức nền, công khai đọc, người phụ trách đơn vị xem toàn nhánh, chính sách Cho phép, từng dòng của danh sách mời, từng lô nhập (lô chạy dưới tên Chủ sở hữu, sau khi lời mời liên quan đã gửi) — kèm khác biệt quyền và số người bị ảnh hưởng; các cấu hình đã có hiệu lực (đơn vị, vai trò tự tạo, điều chỉnh ô vai trò dựng sẵn, nhóm, mẫu ngành, đơn vị tiếp nhận) được liệt kê để xem và sửa sau, không bác ở bước này; dòng nhập trỏ tới người có lời mời bị bác trở thành dòng lỗi, và xác nhận đã xem toàn bộ cấu hình (ghi nhật ký) — rồi vào lộ trình thiết lập (`FEAT-11`). Danh sách người sẽ mời chỉ được gửi lời mời khi Chủ sở hữu xác nhận.

**Luồng ngoại lệ:**

- Gửi lại cùng một yêu cầu tạo (do lỗi mạng hay bấm hai lần) → không tạo workspace thứ hai, trả về workspace đã tạo.
- Lời mời kích hoạt hết hạn → nhân sự gửi lại; đổi email Chủ sở hữu theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-01`.
- Chủ sở hữu chưa chấp nhận lời mời kích hoạt sau `PLT-14` → nhân sự phụ trách hợp đồng nhận nhắc.

**Quy tắc nghiệp vụ:**

- **`BR-16.1` (Cùng quy tắc với tự đăng ký):** Workspace tạo hộ chịu đúng các quy tắc khởi tạo, tên miền phụ và phân quyền như kênh tự đăng ký; chỉ khác phần ai nhập thông tin.

  **Lý do nghiệp vụ:** hai kênh với hai bộ quy tắc sinh ra hai loại workspace hành xử khác nhau, và lỗi chỉ lộ ra ở khách hàng lớn nhất.

- **`BR-16.2` (Chống tạo trùng):** Cùng một yêu cầu gửi lại không bao giờ tạo workspace thứ hai; doanh nghiệp đã có workspace chỉ được tạo thêm khi có lý do ghi nhận.

  **Lý do nghiệp vụ:** hai workspace cho cùng khách hàng chia đôi dữ liệu và hoá đơn.

- **`BR-16.3` (Nhà cung cấp không giữ quyền sau khi tạo):** Sau khi giai đoạn triển khai kết thúc, nhân sự vận hành nền tảng không có quyền trong workspace ngoài Phiên hỗ trợ ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-08`).

  **Lý do nghiệp vụ:** người tạo hộ không được trở thành người có quyền thường trực trên dữ liệu của khách hàng.

- **`BR-16.4` (Giai đoạn triển khai trước kích hoạt):** Từ khi tạo tới khi Chủ sở hữu chấp nhận lời mời kích hoạt (tối đa `PLT-15`), nhân sự triển khai được giao trong yêu cầu tạo hộ được dựng cấu hình — đơn vị, vai trò tự tạo, điều chỉnh ô vai trò dựng sẵn, nhóm, mẫu ngành, đơn vị tiếp nhận mặc định theo loại nguồn (kể cả ghi đè theo chi nhánh), chuẩn bị xác minh tên miền email, danh sách người sẽ mời, lô nhập soạn sẵn theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-08.7` — nhưng không xem hay sửa dữ liệu nghiệp vụ, không chạy lô nhập và không gửi lời mời. Mức nền, công khai đọc, người phụ trách đơn vị xem toàn nhánh và chính sách Cho phép chỉ được soạn sẵn và có hiệu lực khi Chủ sở hữu xác nhận ở bước 5. Mọi thao tác ghi nhật ký của workspace với tên từng nhân sự, gắn nhãn "Nhà cung cấp — triển khai". Đây là ngoại lệ tường minh của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-08.5`. Giai đoạn kết thúc khi Chủ sở hữu chấp nhận; hết `PLT-15` mà chưa chấp nhận thì giai đoạn đóng, cấu hình và danh sách được giữ, nhân sự phụ trách hợp đồng được thông báo; tệp nhập khách hàng đã tải lên bị xoá khi giai đoạn đóng, và lô nhập tương ứng ở bước 5 hiện "cần tải lại tệp", không xác nhận được cho tới khi tải lại. Sau kích hoạt, cấu hình tiếp dùng Phiên triển khai (`BR-08.7` của tài liệu đó).

  **Lý do nghiệp vụ:** khách hàng doanh nghiệp trả tiền để nhận một workspace đã dựng sẵn; nhưng chưa có Chủ sở hữu để duyệt phiên hỗ trợ, và nhà cung cấp không được động tới dữ liệu khách hàng hay tự mời người thay khách hàng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-16.1.1` | Nhân sự nhập tên miền phụ thuộc danh mục bảo lưu | Gửi | Từ chối như kênh tự đăng ký |
| `AC-16.2.1` | Yêu cầu tạo đã gửi | Gửi lại đúng yêu cầu đó | Không có workspace thứ hai; trả về workspace đã tạo |
| `AC-16.2.2` | Mã số thuế đã có workspace | Gửi yêu cầu mới | Cảnh báo; chỉ tạo khi nhập lý do; lý do được lưu |
| `AC-16.3.1` | Workspace vừa tạo xong | Nhân sự thử mở dữ liệu khách hàng của workspace | Bị từ chối; chỉ có lối gửi đề nghị phiên hỗ trợ |
| `AC-16.1.2` | Chủ sở hữu chấp nhận lời mời kích hoạt | Vào workspace | Thấy lộ trình thiết lập; không có dữ liệu mẫu |
| `AC-16.4.1` | Giai đoạn triển khai đang mở | Nhân sự triển khai tạo 5 đơn vị và 2 vai trò tự tạo | Thành công; nhật ký ghi nhãn "Nhà cung cấp — triển khai" |
| `AC-16.4.2` | Giai đoạn triển khai đang mở | Nhân sự triển khai thử nhập danh sách khách hàng hoặc gửi lời mời | Thao tác không có |
| `AC-16.4.3` | Chủ sở hữu chấp nhận lời mời kích hoạt | Nhân sự triển khai thử sửa một đơn vị | Bị từ chối; chỉ có lối đề nghị phiên hỗ trợ |
| `AC-16.4.4` | Danh sách dựng sẵn có 2 email ngoài tên miền công ty và 1 dòng Quản trị viên | Chủ sở hữu xem tóm tắt | Ba dòng đó được đánh dấu nổi trước nút xác nhận |
| `AC-16.4.5` | Nhân sự triển khai soạn sẵn bật công khai đọc cho Vé hỗ trợ trước kích hoạt | Chủ sở hữu kích hoạt, bác mục đó, xác nhận các mục khác | Công khai đọc vẫn tắt; các mục khác có hiệu lực |
| `AC-16.4.6` | Lô nhập soạn sẵn có 50 dòng trỏ tới người mà Chủ sở hữu vừa bác lời mời | Chủ sở hữu xác nhận lô | 50 dòng đó là dòng lỗi, có trong tệp dòng lỗi; các dòng khác nhập bình thường |
| `AC-16.4.7` | Giai đoạn đã đóng vì hết `PLT-15`; nhân sự gửi lại lời mời kích hoạt; Chủ sở hữu chấp nhận | Mở bước 5 | Cấu hình và danh sách mời còn; lô nhập hiện "cần tải lại tệp" và không xác nhận được |
| `AC-16.1.3` | Danh sách 120 người sẽ mời đã được dựng sẵn | Chủ sở hữu vào workspace lần đầu | Thấy danh sách kèm nút xác nhận gửi lời mời; chưa ai nhận thư trước khi Chủ sở hữu xác nhận |

---

## H. DÙNG THỬ, NUÔI DƯỠNG & NHIỀU WORKSPACE

### FEAT-17 — Hiển thị trạng thái dùng thử & lối nâng cấp

**Mô tả nghiệp vụ:** Những người cần biết thấy rõ workspace đang dùng thử, còn bao nhiêu ngày, điều gì xảy ra khi hết hạn, và lối nâng cấp. Chính sách dùng thử thuộc [`billing-subscription-srs.md`](./billing-subscription-srs.md) `FEAT-05`.

**Vai trò sử dụng chính:** Chủ sở hữu, Quản trị viên, Người phụ trách thanh toán ([`billing-subscription-srs.md`](./billing-subscription-srs.md) `BR-03.5`; chưa chỉ định thì Chủ sở hữu giữ chức danh này).

**Điều kiện tiên quyết:** Workspace đang dùng thử, dùng thử đang chờ xác nhận (`BR-08.6`), hoặc đã hết dùng thử chưa chuyển đổi.

**Luồng chính:**

1. Thanh trạng thái hiển thị "Dùng thử: còn N ngày" (tính theo Mục 2.3) — hoặc "Dùng thử đang chờ xác nhận" theo `BR-08.6` — và nút Nâng cấp cho Người phụ trách thanh toán.
2. Hết dùng thử chưa chuyển đổi → thanh trạng thái nêu workspace đang bị hạn chế những gì theo [`billing-subscription-srs.md`](./billing-subscription-srs.md) `BR-05.4`, lối xuất dữ liệu và lối nâng cấp.
3. Trong khoảng nhắc cuối do [`billing-subscription-srs.md`](./billing-subscription-srs.md) quy định, thanh trạng thái chuyển sang dạng nổi bật và nêu rõ ngày hết hạn cùng điều sẽ xảy ra.
4. Chuyển đổi sang trả phí → thanh trạng thái ẩn.

**Quy tắc nghiệp vụ:**

- **`BR-17.1` (Hiển thị cho đúng người):** Thanh trạng thái chỉ hiển thị cho Người có toàn quyền và Người phụ trách thanh toán; nút Nâng cấp chỉ hiển thị cho Người phụ trách thanh toán. Thành viên khác không thấy trong lúc dùng thử; khi hết dùng thử và họ gặp một thao tác bị hạn chế, họ thấy thông báo trung tính kèm tên Người phụ trách thanh toán.

  **Lý do nghiệp vụ:** nhân viên kinh doanh không quyết định mua; hiển thị cho họ chỉ gây nhiễu và nút bấm vô dụng.

- **`BR-17.2` (Hết dùng thử không mất dữ liệu):** Trạng thái và quyền sau khi hết dùng thử theo [`billing-subscription-srs.md`](./billing-subscription-srs.md) `BR-05.4`; tài liệu này chỉ hiển thị đúng trạng thái đó.

  **Lý do nghiệp vụ:** một nguồn duy nhất cho chính sách thương mại.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-17.1.1` | Workspace dùng thử | Nhân viên Kinh doanh mở ứng dụng | Không thấy thanh trạng thái dùng thử |
| `AC-17.1.2` | Quản trị viên không phải Người phụ trách thanh toán | Mở ứng dụng | Thấy số ngày còn lại; không có nút Nâng cấp, có ghi chú tên Người phụ trách thanh toán |
| `AC-17.2.2` | Hết dùng thử chưa chuyển đổi | Chủ sở hữu mở ứng dụng | Thanh trạng thái nêu các hạn chế đang áp dụng, lối xuất dữ liệu và lối nâng cấp |
| `AC-17.1.4` | Hết dùng thử chưa chuyển đổi | Nhân viên Kinh doanh thử thao tác bị hạn chế | Thấy thông báo trung tính "tính năng tạm hạn chế, liên hệ [tên Người phụ trách thanh toán]" |
| `AC-17.1.5` | Workspace đang chờ xác nhận dùng thử | Người phụ trách thanh toán mở ứng dụng | Thanh trạng thái hiển thị "Dùng thử đang chờ xác nhận" và nút Nâng cấp |
| `AC-17.2.1` | Đã chuyển đổi trả phí | Chủ sở hữu mở ứng dụng | Thanh trạng thái không còn |
| `AC-17.1.3` | Trong khoảng nhắc cuối | Chủ sở hữu mở ứng dụng | Thanh nổi bật, nêu ngày hết hạn và điều xảy ra sau đó |

---

### FEAT-18 — Thông điệp hướng dẫn kích hoạt

**Mô tả nghiệp vụ:** Chuỗi email hướng dẫn gửi cho người quản trị workspace mới, theo hành vi thực tế, giúp workspace Đạt kích hoạt sử dụng trước khi hết dùng thử.

**Vai trò sử dụng chính:** Hệ thống; người nhận theo `CFG-18-01`.

**Điều kiện tiên quyết:** Workspace tự đăng ký, trong thời gian `PLT-18` kể từ khi Sẵn sàng.

**Luồng chính:**

1. Hệ thống lập lịch theo lịch thông điệp của nền tảng (`PLT-09`), với mốc tương đối với ngày Sẵn sàng hoặc với ngày hết dùng thử. Thông báo bắt buộc trước khi hết dùng thử thuộc [`billing-subscription-srs.md`](./billing-subscription-srs.md) `BR-05.3`, không nằm trong chuỗi này và không chịu `CFG-18-01` hay việc huỷ nhận.
2. Trước mỗi lần gửi, hệ thống bỏ qua thông điệp về nhiệm vụ đã hoàn thành trên lộ trình (`FEAT-11`).
3. Gửi vào giờ gửi của `PLT-09` theo múi giờ workspace, vào ngày làm việc, theo đúng các quy tắc dời, bỏ và gộp mốc ở Mục 2.3.

**Luồng ngoại lệ:**

- Workspace đang chờ xác nhận dùng thử (`BR-08.6`) → tạm dừng thông điệp về các nhiệm vụ đang bị chặn (mời đội ngũ, kết nối kênh) và mọi mốc tính theo ngày hết dùng thử cho tới khi có quyết định; mốc đã qua trong thời gian chờ bị bỏ.
- Workspace chuyển đổi trả phí → dừng các thông điệp về dùng thử, giữ thông điệp hướng dẫn chưa hoàn thành.
- Người nhận huỷ đăng ký nhận → không gửi cho người đó nữa; các người nhận khác không bị ảnh hưởng.
- Độ dài dùng thử thay đổi (gia hạn theo [`billing-subscription-srs.md`](./billing-subscription-srs.md)) → các mốc theo ngày hết dùng thử tự dời theo.

**Quy tắc nghiệp vụ:**

- **`BR-18.1` (Theo hành vi, không theo lịch cứng):** Không gửi thông điệp hướng dẫn một việc người dùng đã làm xong.

  **Lý do nghiệp vụ:** email nhắc việc đã làm làm người dùng huỷ đăng ký nhận cả các thông điệp có giá trị.

- **`BR-18.2` (Mốc "sắp hết hạn" theo ngày hết hạn thật):** Thông điệp "còn N ngày" tính theo ngày hết dùng thử thật của workspace.

  **Lý do nghiệp vụ:** dùng thử có độ dài khác nhau theo chiến dịch; mốc cố định theo ngày đăng ký sẽ báo sai.

- **`BR-18.3` (Luôn huỷ được):** Mỗi thông điệp hướng dẫn có lối huỷ nhận một bước, áp cho riêng người đó; việc huỷ không ảnh hưởng thông báo nghiệp vụ bắt buộc của billing.

  **Lý do nghiệp vụ:** đây là thông điệp của nhà cung cấp, không phải thông báo nghiệp vụ bắt buộc; người nhận phải dừng được.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-18.1.1` | Nhiệm vụ "Kết nối kênh" đã hoàn thành | Tới mốc thông điệp về kết nối kênh | Không gửi |
| `AC-18.2.1` | Dùng thử được gia hạn thêm 7 ngày | Tới mốc "còn 2 ngày" cũ | Không gửi; thông điệp dời theo ngày hết hạn mới |
| `AC-18.3.1` | Chủ sở hữu huỷ nhận | Tới mốc kế tiếp | Chủ sở hữu không nhận; Quản trị viên vẫn nhận nếu `CFG-18-01` gồm Quản trị viên |
| `AC-18.1.2` | Workspace múi giờ Riyadh | Tới ngày gửi | Thư đến lúc 09:00 giờ Riyadh |
| `AC-18.2.2` | Workspace chuyển đổi trả phí | Tới mốc thông điệp dùng thử | Không gửi |
| `AC-18.3.2` | Chủ sở hữu đã huỷ nhận thông điệp hướng dẫn | Tới mốc thông báo bắt buộc trước khi hết dùng thử | Chủ sở hữu vẫn nhận thông báo của billing |
| `AC-18.1.3` | Workspace múi giờ Riyadh, mốc rơi vào thứ Sáu | Tới mốc | Thư gửi vào thứ Năm liền trước lúc 09:00 giờ Riyadh |
| `AC-18.1.4` | Workspace Sẵn sàng lúc 15:00 | — | Thông điệp của ngày Sẵn sàng tới ngay, không chờ 09:00 hôm sau |
| `AC-18.2.3` | Độ dài dùng thử 5 ngày, lịch có mốc +6 ngày | — | Mốc +6 bị bỏ |
| `AC-18.2.4` | Workspace đang chờ xác nhận dùng thử | Tới mốc +2 ngày về mời đội ngũ | Không gửi; khi được duyệt, các mốc tính lại theo ngày hết dùng thử mới |

---

### FEAT-19 — Tạo thêm workspace từ tài khoản đã có

**Mô tả nghiệp vụ:** Người đã có tài khoản (ví dụ đang là thành viên của một workspace khác) tự tạo thêm workspace mới cho doanh nghiệp của mình mà không phải đăng ký lại.

**Vai trò sử dụng chính:** Người dùng đã đăng nhập.

**Điều kiện tiên quyết:** Tài khoản đã xác minh email.

**Luồng chính:**

1. Từ danh sách workspace ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-51`) chọn "Tạo workspace mới". Nếu email của người dùng thuộc tên miền đã xác minh của một workspace khác: khi workspace đó bật tự gia nhập, hệ thống gợi ý "Tham gia workspace của công ty" trước; trong mọi trường hợp, màn hình báo trước rằng công ty sở hữu tên miền sẽ được thông báo (`BR-20.3`).
2. Đi qua `FEAT-03`, `FEAT-04`, `FEAT-06`, `FEAT-08`; không có bước tạo tài khoản và xác minh.
3. Người dùng là Chủ sở hữu của workspace mới; workspace cũ không bị ảnh hưởng.

**Luồng ngoại lệ:**

- Tài khoản đang là Chủ sở hữu của số workspace dùng thử tối đa cho một tài khoản (`PLT-10`) → từ chối, nêu giới hạn và lối liên hệ kinh doanh.
- Hệ thống chống dùng thử lặp lại ([`billing-subscription-srs.md`](./billing-subscription-srs.md) `BR-05.7`) phát hiện dấu hiệu lạm dụng → workspace vẫn tạo được và xử lý theo `BR-08.6` (tạo thêm workspace tính là kênh tự đăng ký).

**Quy tắc nghiệp vụ:**

- **`BR-19.1` (Workspace mới độc lập):** Workspace mới hoàn toàn độc lập với các workspace người dùng đang thuộc; không sao chép dữ liệu, vai trò hay thành viên nào.

  **Lý do nghiệp vụ:** cách ly giữa doanh nghiệp là cam kết cốt lõi; tư vấn viên tạo workspace riêng không được mang theo dữ liệu của khách hàng cũ.

- **`BR-19.2` (Giới hạn dùng thử theo tài khoản):** Số workspace đang dùng thử mà một tài khoản là Chủ sở hữu không vượt `PLT-10`.

  **Lý do nghiệp vụ:** không giới hạn thì một tài khoản tạo liên tục workspace mới để dùng thử mãi.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-19.1.1` | A là Thành viên của W1 | Tạo workspace mới W2 | A là Chủ sở hữu W2; W2 không có thành viên, vai trò tự tạo hay dữ liệu nào của W1; A vẫn là Thành viên W1 |
| `AC-19.2.1` | A đã là Chủ sở hữu của `PLT-10` workspace đang dùng thử | Tạo thêm | Từ chối, nêu giới hạn |
| `AC-19.1.2` | A tạo W2 | Đi qua luồng | Không có bước nhập email, mật khẩu hay mã xác minh |
| `AC-19.1.3` | Email của A thuộc tên miền đã xác minh của W1 | A tạo workspace mới | Thấy cảnh báo công ty sở hữu tên miền sẽ được thông báo trước khi tạo (`BR-20.3`) |

---

## 4. Yêu cầu phi chức năng

### 4.1 Hiệu năng

- **`NFR-01` (Phản hồi các bước đăng ký):** Mỗi bước của luồng đăng ký phản hồi trong 400 ms ở phân vị 95; kiểm tra tên miền phụ khi gõ phản hồi trong 300 ms ở phân vị 95.
- **`NFR-02` (Thời gian khởi tạo):** Từ khi bấm Tạo workspace tới khi người dùng ở bên trong workspace: phân vị 50 không quá 10 giây, phân vị 95 không quá 30 giây, đo trên mọi lượt khởi tạo trong tháng.
- **`NFR-03` (Khôi phục khi tải lại):** Tải lại trang ở bất kỳ bước nào khôi phục đủ thông tin đã khai trong 1 giây.

### 4.2 Độ tin cậy

- **`NFR-04` (Tỷ lệ khởi tạo thành công):** Ít nhất 99,5% lượt khởi tạo thành công ở lần đầu (không cần người dùng bấm thử lại); 100% lượt thất bại được dọn sạch theo `BR-08.2`, kiểm chứng bằng đối soát định kỳ không còn phần dở dang nào quá 1 giờ (tên miền phụ đang giữ chỗ hợp lệ theo `PLT-03` không tính là phần dở dang).
- **`NFR-05` (Chạy lại an toàn):** Mọi bước khởi tạo và nạp cấu hình chạy lại nhiều lần cho cùng một workspace không sinh bản ghi trùng.

### 4.3 An toàn

- **`NFR-06` (Chống lạm dụng đăng ký):** Đăng ký, kiểm tra tên miền phụ, gửi mã xác minh và gửi lời mời đều chịu ngưỡng tần suất theo nguồn và theo tài khoản (`PLT-11`); vượt ngưỡng thì yêu cầu kiểm tra người thật hoặc tạm chặn, không báo lỗi khó hiểu.
- **`NFR-07` (Không lộ thông tin khách hàng khác):** Không thông báo nào trong luồng đăng ký, kiểm tra tên miền phụ, tên miền riêng, tên miền email hay lời mời tiết lộ gì về workspace khác ngoài việc một tên không khả dụng (`BR-06.3`, `BR-07.2`, `BR-20.2`). Ngoại lệ tường minh duy nhất: thông báo cho công ty sở hữu tên miền theo `BR-20.3`, có báo trước cho người bị ảnh hưởng. Đo: kiểm thử xâm nhập mỗi bản phát hành lớn, 0 trường hợp.

### 4.4 Đo lường

- **`NFR-08` (Sự kiện đo kích hoạt):** Hệ thống ghi nhận các sự kiện cần cho Mục 2.6: bắt đầu đăng ký, hoàn thành từng bước, xác minh email, bắt đầu và kết thúc khởi tạo, vào workspace lần đầu, gửi lời mời, chấp nhận lời mời, hoàn thành từng nhiệm vụ lộ trình. Sự kiện đo không chứa nội dung dữ liệu nghiệp vụ của khách hàng.

### 4.5 Khả dụng & Đa ngôn ngữ

- **`NFR-09` (Khả dụng của luồng đăng ký):** 99,9% theo tháng.
- **`NFR-10` (Đa ngôn ngữ và khả năng tiếp cận):** Mọi màn hình, thư và thông điệp của phân hệ có đủ các ngôn ngữ nền tảng hỗ trợ, gồm tiếng Ả Rập với bố cục từ phải sang trái; các màn hình đăng ký đạt mức tiếp cận AA của chuẩn tiếp cận nội dung web.

---

## 5. Ma trận quyền truy cập tính năng

Các cột là vai trò thao tác tại Mục 2.2. Ký hiệu: **✅** = thực hiện được; **Q:** = cần quyền quản trị nêu theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-24`; **—** = không.

| Tính năng | Người đăng ký | Chủ sở hữu | Người có quyền quản trị liên quan | Thành viên được mời | Người phụ trách thanh toán | Nhân sự vận hành nền tảng | Hệ thống |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `FEAT-01` Đăng ký & xác minh | ✅ | — | — | — | — | — | ✅ (gửi mã) |
| `FEAT-02` Đăng ký dở dang | ✅ | — | — | — | — | — | ✅ (dọn) |
| `FEAT-03` Khai báo doanh nghiệp | ✅ | — | — | — | — | ✅ (kênh tạo hộ) | — |
| `FEAT-04` Bản địa hoá | ✅ | — | — | — | — | ✅ (kênh tạo hộ) | ✅ (đề xuất) |
| `FEAT-05` Tín hiệu khách hàng lớn | — | — | — | — | — | ✅ (nhận) | ✅ |
| `FEAT-06` Tên miền phụ | ✅ | — | — | — | — | ✅ (kênh tạo hộ) | ✅ (gợi ý, giữ chỗ) |
| `FEAT-07` Tên miền riêng | — | ✅ | Q: Quản lý cấu hình workspace | — | — | — | ✅ (kiểm tra định kỳ) |
| `FEAT-20` Tên miền email | — | ✅ | Người có toàn quyền | — | — | ✅ (thẩm định khiếu nại) | ✅ (kiểm tra định kỳ) |
| `FEAT-08` Khởi tạo | ✅ (xác nhận, thử lại) | — | — | — | — | ✅ (kênh tạo hộ) | ✅ |
| `FEAT-09` Cấu hình theo ngành | — | ✅ (áp thêm mẫu) | Q: Quản lý cấu hình workspace | — | — | — | ✅ |
| `FEAT-10` Dữ liệu mẫu | ✅ (chọn nạp) | ✅ (xoá) | Q: Quản lý cấu hình workspace | — | — | — | ✅ (nạp) |
| `FEAT-11` Lộ trình thiết lập | — | ✅ | Quản trị viên theo `CFG-11-01` | — | — | — | ✅ (đánh dấu) |
| `FEAT-12` Thiết lập đội ngũ | — | ✅ | Q: Mời người dùng (+ Quản lý đơn vị tổ chức để tạo đội); đồng quản trị, các câu hỏi cách làm việc và kiêm nhiệm Đầy đủ chỉ Người có toàn quyền | — | — | — | — |
| `FEAT-13` Hướng dẫn tương tác | — | ✅ | ✅ | ✅ | ✅ | — | — |
| `FEAT-14` Thư mời | — | — | — | Nhận | — | ✅ (xem xét hàng đợi lời mời tạm dừng, `BR-14.3`) | ✅ (gửi) |
| `FEAT-15` Chấp nhận lời mời | — | — | — | ✅ | — | — | — |
| `FEAT-16` Tạo hộ | — | Chấp nhận lời mời kích hoạt; xác nhận danh sách mời | — | — | — | ✅ (gồm giai đoạn triển khai) | ✅ |
| `FEAT-17` Trạng thái dùng thử | — | ✅ (xem; nâng cấp khi là Người phụ trách thanh toán) | Quản trị viên: xem | — | ✅ (xem, nâng cấp) | — | — |
| `FEAT-18` Thông điệp hướng dẫn | — | Nhận, huỷ nhận | Quản trị viên: nhận theo `CFG-18-01` | — | — | — | ✅ (gửi) |
| `FEAT-19` Tạo thêm workspace | — | — | — | — | — | — | — |

**Ghi chú:** `FEAT-19` thực hiện bởi bất kỳ người dùng đã đăng nhập có email đã xác minh, không phụ thuộc vai trò ở workspace nào; người đó trở thành Chủ sở hữu của workspace mới. Mọi ô "Q:" chịu trần năng lực của người thực hiện theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Nguyên tắc 1.

---

## 6. Kịch bản chấp nhận tổng hợp

### Kịch bản 1: Doanh nghiệp bất động sản ở Riyadh tự đăng ký

1. Người đăng ký truy cập từ Ả Rập Xê Út, chọn đăng ký bằng Google với email đã xác minh (`FEAT-01`).
2. Một màn hình: tên "Riyadh Realty", quy mô 11 – 50, ngành Bất động sản; tiếng Ả Rập, múi giờ Riyadh, tiền tệ Riyal được đề xuất sẵn (`FEAT-03`, `FEAT-04`).
3. Tên miền phụ "riyadh-realty" được gợi ý và còn trống (`FEAT-06`); bấm Tạo workspace.
4. **Kỳ vọng:** trong thời gian `NFR-02` người dùng ở bên trong workspace, không qua màn hình đăng nhập (`BR-08.4`); phễu Bất động sản có sẵn (`FEAT-09`); bản ghi mẫu mang nhãn Mẫu (`FEAT-10`); thanh dùng thử hiển thị số ngày theo giờ Riyadh (`FEAT-17`); bố cục từ phải sang trái (`NFR-10`).

### Kịch bản 2: Mời cả đội trong 10 phút

1. Ở màn hình chào mừng, Chủ sở hữu chọn Thiết lập đội ngũ ngay (`FEAT-11`).
2. Giữ hai đội gợi ý "Kinh doanh", "Hỗ trợ"; dán 15 và 8 email; đánh dấu một trưởng nhóm mỗi đội; gửi (`FEAT-12`).
3. **Kỳ vọng:** 23 lời mời được gửi (trưởng nhóm gửi trước), mỗi thư nêu đúng đội và vai trò (`FEAT-14`); nhiệm vụ "Đưa đội ngũ vào" hoàn thành khi có hai thành viên đã tham gia (`BR-11.1`).

### Kịch bản 3: Thành viên mới đã có tài khoản

1. Một tư vấn viên đã là thành viên workspace khác nhận lời mời, đang đăng nhập đúng tài khoản.
2. Bấm Chấp nhận → một màn hình xác nhận → vào workspace (`BR-15.3`).
3. **Kỳ vọng:** thẻ chào mừng nêu đơn vị, quản lý, vai trò; màn hình chính không trống (`BR-15.2`); người này chuyển qua lại hai workspace bằng bộ chọn ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-51`).

### Kịch bản 4: Khởi tạo thất bại

1. Kết nối dịch vụ đi kèm thất bại sau mọi lần tự thử lại.
2. **Kỳ vọng:** thông báo rõ ràng, nút Thử lại và mã tra cứu; tên miền phụ được giữ trong `PLT-03` rồi mới giải phóng; thông tin đã khai còn nguyên (`BR-08.2`); người khác có mã tra cứu không thao tác được (`BR-08.3`).

### Kịch bản 5: Xoá dữ liệu mẫu khi đã dùng thật một phần

1. Chủ sở hữu đã gắn một vé hỗ trợ thật vào khách hàng mẫu K và sửa tên một công ty mẫu.
2. Bấm Xoá dữ liệu mẫu → thấy K và công ty đó trong nhóm "đã sửa hoặc đã gắn với dữ liệu thật"; giữ lại công ty, xoá K.
3. **Kỳ vọng:** công ty thành bản ghi thật; vé vẫn còn và được gỡ liên kết với K; báo cáo không còn số liệu mẫu (`BR-10.2`, `BR-10.3`).

### Kịch bản 6: Tên miền riêng mất xác minh

1. Doanh nghiệp đã kích hoạt crm.congty.vn; sau đó bản ghi chứng minh sở hữu bị gỡ.
2. **Kỳ vọng:** trạng thái "Mất xác minh", truy cập chuyển về tên miền phụ, Chủ sở hữu nhận cảnh báo; sau `PLT-19` tên miền bị gỡ khỏi workspace (`FEAT-07`).

### Kịch bản 7: Tạo hộ cho khách hàng doanh nghiệp

1. Nhân sự vận hành nền tảng tạo workspace theo hợp đồng, email Chủ sở hữu kèm văn bản của khách hàng; bấm gửi hai lần do mạng chậm.
2. **Kỳ vọng:** chỉ có một workspace (`BR-16.2`); không có dữ liệu mẫu; Chủ sở hữu nhận lời mời kích hoạt, chấp nhận và vào lộ trình thiết lập; nhân sự vận hành không xem được dữ liệu của workspace ngoài phiên hỗ trợ (`BR-16.3`).

### Kịch bản 8: Thư mời không thể dùng để giả mạo

1. Một workspace dùng thử mới tải logo của một ngân hàng và gửi lời mời hàng loạt.
2. **Kỳ vọng:** thư dùng mẫu chuẩn, không có logo, hiển thị địa chỉ thật của workspace (`BR-14.2`); vượt giới hạn giờ thì xếp hàng, chạm giới hạn ngày thì hàng đợi tạm dừng để nhân sự vận hành xem xét (`BR-14.3`).

### Kịch bản 9: Tư vấn viên tạo workspace thứ hai

1. Tư vấn viên đang là thành viên W1 tạo W2 từ bộ chọn workspace (`FEAT-19`).
2. **Kỳ vọng:** không phải đăng ký lại; W2 không mang dữ liệu hay vai trò nào của W1 (`BR-19.1`); vượt `PLT-10` thì bị từ chối (`BR-19.2`).

### Kịch bản 10: Bỏ dở rồi quay lại

1. Người đăng ký khai tên doanh nghiệp trên điện thoại rồi bỏ đi; quay lại trên máy tính sau khi giữ chỗ tên miền phụ đã hết, tên đã bị người khác lấy.
2. **Kỳ vọng:** thông tin đã khai còn nguyên; thấy gợi ý tên mới thay vì lỗi (`FEAT-02`); nếu không quay lại trong `PLT-02`, nhận email nhắc rồi tài khoản dở dang bị dọn.

---

## 7. Nhu cầu nghiệp vụ chưa chốt được phương án

1. **Mua tên miền ngay trong sản phẩm.** Khách hàng chưa có tên miền có thể muốn mua trực tiếp. Chưa chốt: có làm hay không, và trách nhiệm gia hạn tên miền thuộc ai.
2. **Trạng thái thương mại khi dùng thử đang chờ xử lý thủ công.** `BR-08.6` cần [`billing-subscription-srs.md`](./billing-subscription-srs.md) chốt: thời hạn dùng thử bắt đầu tính từ lúc tạo hay lúc duyệt, và workspace chuyển trạng thái nào khi bác. Tài liệu này đã chốt phần thuộc onboarding (chặn gì, hiển thị gì, lối trả phí ngay).
3. **Đổi tên miền phụ sau khi workspace đã hoạt động.** Chưa chốt: có cho phép không; nếu cho, tên cũ được chuyển hướng bao lâu, và thư mời, liên kết đã gửi xử lý ra sao.
4. **Nhập đội ngũ từ danh bạ Google Workspace hoặc Microsoft 365.** Chưa chốt: có đồng bộ danh bạ công ty để mời không, phạm vi quyền đọc danh bạ cần xin, và cách xử lý khi danh bạ thay đổi sau đó.

Các câu hỏi về gia hạn dùng thử và thời gian ân hạn dữ liệu sau khi hết dùng thử thuộc [`billing-subscription-srs.md`](./billing-subscription-srs.md).

---

## Phụ lục A — Danh mục Khái niệm Nghiệp vụ

> Phụ lục này mô tả **khái niệm nghiệp vụ**, không phải thiết kế dữ liệu, và không mang tính ràng buộc kỹ thuật. Cách tổ chức lưu trữ thuộc thẩm quyền đội phát triển.

| Khái niệm | Thông tin nghiệp vụ cốt lõi |
| --- | --- |
| A.1 Đăng ký | Tài khoản, các bước đã hoàn thành, thông tin đã khai, trạng thái xác minh email, thời điểm hoạt động gần nhất |
| A.2 Hồ sơ doanh nghiệp | Tên, quy mô, ngành, mục tiêu, quốc gia, ngôn ngữ, múi giờ, tiền tệ |
| A.3 Tên miền phụ | Giá trị, trạng thái (đang giữ chỗ / thuộc workspace), hạn giữ chỗ |
| A.4 Tên miền riêng | Giá trị, trạng thái (chờ xác minh / hoạt động / mất xác minh), thời điểm xác minh gần nhất |
| A.5 Lượt khởi tạo | Kênh (tự đăng ký / tạo hộ), các giai đoạn và kết quả, số lần thử, mã tra cứu, kết quả cuối |
| A.6 Mẫu ngành | Ngành, phễu bán hàng, quy trình vé hỗ trợ, bộ dữ liệu mẫu theo quốc gia |
| A.7 Mẫu đội ngũ | Quy mô, mục tiêu, các đội gợi ý và vai trò gợi ý |
| A.8 Bản ghi mẫu | Nhãn Mẫu trên bản ghi nghiệp vụ; cờ đã bị sửa hoặc đã gắn với dữ liệu thật |
| A.9 Lộ trình thiết lập | Các nhiệm vụ, trạng thái hoàn thành, thời điểm hoàn thành, đã ẩn hay chưa |
| A.10 Lượt thông điệp hướng dẫn | Người nhận, mốc, đã gửi hay bỏ qua và lý do, trạng thái huỷ nhận |
| A.11 Yêu cầu tạo hộ | Nhân sự thực hiện, căn cứ hợp đồng, mã số thuế, email Chủ sở hữu và văn bản, nhân sự triển khai được giao, lý do khi tạo trùng |
| A.12 Tên miền email đã xác minh | Tên miền, workspace, trạng thái (chờ xác minh / đã xác minh / mất xác minh), thời điểm xác minh gần nhất; hồ sơ khiếu nại (bên khiếu nại, bằng chứng, trạng thái hồ sơ, kết quả, lý do) |

---

## Phụ lục B — Danh mục Tham số

### B.1 Tham số theo workspace

Thay đổi `CFG-14-01` ghi nhật ký thay đổi cấu hình quyền theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.4`; các tham số còn lại ghi nhật ký hoạt động của workspace. "Người có toàn quyền" = Chủ sở hữu hoặc Quản trị viên.

| Mã | Quy tắc | Nội dung | Mặc định | Miền giá trị | Thẩm quyền | Mức độ tự do |
| --- | --- | --- | --- | --- | --- | --- |
| `CFG-11-01` | `FEAT-11` | Hiển thị lộ trình thiết lập cho Quản trị viên | Bật | Bật / Tắt | Chủ sở hữu | Tự do |
| `CFG-13-01` | `FEAT-13` | Hướng dẫn tương tác cho thành viên | Bật | Bật / Tắt | Người có toàn quyền | Tự do |
| `CFG-14-01` | `BR-14.2` | Dùng logo doanh nghiệp trong thư mời | Bật | Bật / Tắt | Người có toàn quyền | **Có sàn bắt buộc** — chỉ có hiệu lực khi đạt điều kiện `BR-14.2` |
| `CFG-18-01` | `FEAT-18` | Người nhận thông điệp hướng dẫn | Chủ sở hữu và Quản trị viên | Chỉ Chủ sở hữu / Chủ sở hữu và Quản trị viên / Không ai | Chủ sở hữu | Tự do |

### B.2 Tham số nền tảng (chính sách của nhà cung cấp)

Áp chung cho mọi workspace; do nhà cung cấp quyết định và Nhân sự vận hành nền tảng cấu hình; mọi thay đổi ghi nhật ký vận hành nội bộ của nhà cung cấp. Doanh nghiệp không đổi được.

| Mã | Quy tắc | Nội dung | Mặc định | Ghi chú |
| --- | --- | --- | --- | --- |
| `PLT-01` | `FEAT-01` | Hiệu lực của mã xác minh email | 15 phút | |
| `PLT-02` | `BR-02.2` | Thời gian không hoạt động trước khi dọn đăng ký dở dang; nhắc trước khi dọn | 72 giờ; nhắc trước 24 giờ | |
| `PLT-03` | `BR-06.5` | Thời gian giữ chỗ tên miền phụ | 30 phút, gia hạn khi người đăng ký còn hoạt động | |
| `PLT-04` | `BR-06.4` | Danh mục tên miền phụ bảo lưu | Theo `BR-06.4` | Duy trì liên tục |
| `PLT-05` | `FEAT-03`, `FEAT-09` | Danh mục ngành và mẫu ngành | Bất động sản; Dịch vụ doanh nghiệp & phần mềm; Bán lẻ & thương mại; Tài chính, bảo hiểm & tư vấn; Khác | Mỗi ngành phải có mẫu đầy đủ (`BR-09.1`) |
| `PLT-06` | `FEAT-04` | Bảng đề xuất ngôn ngữ, múi giờ, định dạng, tiền tệ và lịch làm việc mặc định theo quốc gia | Ả Rập Xê Út (làm việc Chủ nhật – thứ Năm); Việt Nam (thứ Hai – thứ Sáu); giá trị quốc tế (thứ Hai – thứ Sáu) | |
| `PLT-07` | `FEAT-05` | Ngưỡng quy mô gửi tín hiệu khách hàng lớn | Trên 200 | |
| `PLT-08` | `FEAT-08` | Số lần tự thử lại một bước khởi tạo | 3 | |
| `PLT-09` | `FEAT-18` | Lịch và giờ gửi thông điệp hướng dẫn | Ngày Sẵn sàng; +2 ngày; +6 ngày; 4 ngày trước khi hết dùng thử; giờ gửi 09:00 | Mốc cuối tương đối với ngày hết dùng thử; không trùng thông báo bắt buộc của billing |
| `PLT-10` | `BR-19.2` | Số workspace đang dùng thử tối đa một tài khoản làm Chủ sở hữu | 1 | |
| `PLT-11` | `NFR-06`, `BR-14.3` | Ngưỡng chống lạm dụng | Đăng ký: 5 mỗi giờ mỗi nguồn; kiểm tra tên miền phụ: 60 mỗi phút mỗi người đăng ký; gửi lại mã: chờ 60 giây giữa hai lần, tối đa 5 mỗi giờ mỗi email; lời mời của workspace dùng thử: 50 mỗi giờ và 300 trong 24 giờ trượt, nâng lên 500 mỗi giờ và 3.000 trong 24 giờ trượt khi có tên miền email đã xác minh hoặc kinh doanh đã xác nhận khách hàng thật | Vượt ngưỡng: kiểm tra người thật hoặc xếp hàng, không báo lỗi khó hiểu |
| `PLT-12` | `BR-11.3` | Phần thưởng khi hoàn thành lộ trình | Không có | |
| `PLT-13` | `FEAT-12` | Mẫu đội ngũ và câu trả lời mặc định theo quy mô và mục tiêu | **1 – 10:** không chia đội; vai trò chung Nhân viên Kinh doanh; thấy dữ liệu của nhau = Có; các đội thấy vé của nhau = Có; biểu mẫu website và kênh vào đơn vị gốc. **11 – 200:** Kinh doanh (Nhân viên Kinh doanh) và Hỗ trợ (Nhân viên Hỗ trợ) — luôn có cả hai khi chưa chọn mục tiêu; thêm Marketing (Marketing, trưởng nhóm Quản lý Marketing) khi có mục tiêu tiếp thị; trưởng nhóm các đội khác Quản lý; thấy dữ liệu của nhau trong đội = Có; các đội thấy vé của nhau = Có; biểu mẫu website → Kinh doanh; kênh hội thoại và hộp thư → Hỗ trợ; Marketing nhập danh sách = Không; Marketing phát sóng = Không. **Trên 200:** hỏi số và tên chi nhánh; mỗi chi nhánh là một đơn vị dưới đơn vị gốc (vai trò gợi ý Quản lý cho giám đốc chi nhánh), dưới mỗi chi nhánh các đội Kinh doanh và Hỗ trợ (và Marketing nếu có mục tiêu tiếp thị) mang tên "<Đội> – <Chi nhánh>"; tuỳ chọn các đơn vị trung tâm dưới đơn vị gốc (Ban giám đốc, Marketing trung tâm, Chăm sóc khách hàng trung tâm); đặt mặc định ghi đè cho từng chi nhánh; giám đốc chi nhánh là quản lý trực tiếp của các trưởng nhóm; nếu có đơn vị trung tâm thì hỏi đội nhận nguồn của đơn vị trung tâm; gợi ý bật rà soát quyền định kỳ theo quý ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-48-01`); thấy dữ liệu của nhau trong đội = Có; các đội thấy vé của nhau = Không; hai câu Marketing = Không; gợi ý mời từ tệp | Vai trò lấy từ danh sách vai trò dựng sẵn của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29` |
| `PLT-14` | `FEAT-16` | Nhắc nhân sự phụ trách khi Chủ sở hữu kênh tạo hộ chưa chấp nhận lời mời | 7 ngày | |
| `PLT-15` | `BR-16.4` | Thời hạn tối đa của giai đoạn triển khai trước kích hoạt | 30 ngày | |
| `PLT-16` | `FEAT-20` | Danh mục nhà cung cấp email miễn phí không xác minh được làm tên miền doanh nghiệp | Danh mục do nhà cung cấp duy trì | |
| `PLT-17` | `FEAT-20` | Thời gian chờ bên đang giữ tên miền phản hồi khiếu nại | 7 ngày | |
| `PLT-18` | `FEAT-18` | Khoảng thời gian gửi thông điệp hướng dẫn kể từ khi Sẵn sàng | 60 ngày | |
| `PLT-19` | `FEAT-07` | Thời gian ân hạn trước khi gỡ tên miền riêng Mất xác minh | 14 ngày | |
| `PLT-20` | `BR-14.3` | Thời hạn nhân sự vận hành xử lý hàng đợi lời mời đang tạm dừng | 24 giờ | |

**Hằng số kèm lý do:** độ dài tên miền phụ 3 – 50 ký tự và tập ký tự cho phép (giới hạn của hệ thống tên miền); 5 lần nhập sai mã xác minh (chống dò mã); 72 giờ tự kiểm tra lại tên miền riêng chưa xác minh (thời gian lan truyền bản ghi tên miền); tối đa 3 bước cho mỗi hướng dẫn tương tác (giữ hướng dẫn ngắn hơn ngưỡng người dùng bỏ qua).

---

## Phụ lục C — Nhật ký Mâu thuẫn & Quyết định đã chốt

| # | Mâu thuẫn / câu hỏi | Cách xử lý đã chốt | Nơi có hiệu lực |
| --- | --- | --- | --- |
| C.1 | Trùng lặp với [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) (vai trò dựng sẵn, vai trò tối thiểu, sức khỏe cấu hình, trần quyền, chuyển nhượng) và với [`billing-subscription-srs.md`](./billing-subscription-srs.md) (dùng thử, hạn mức, hạ gói) | Gỡ khỏi tài liệu này, chỉ dẫn chiếu. Ánh xạ v2.0 → v3.0: `FEAT-01`, `03` → `FEAT-01`; `04`, `05` → `FEAT-03`; `06` → `FEAT-04`; `07` → `FEAT-05`; `08` – `10` → `FEAT-06`; `11` → `FEAT-07`; `12` – `15` → `FEAT-08`; `16` → `FEAT-09`; `18`, `19` → `FEAT-10`; `20`, `21` → `FEAT-11`; `22` → `FEAT-13`; `23` → `FEAT-14`; `24` → `FEAT-15`; `26`, `27` → `FEAT-16`; `29` → `FEAT-17`; `30` → `FEAT-18`; `02` → billing `FEAT-05`; `17`, `25`, `28`, `31`, `32` → IAM `FEAT-02`, `FEAT-09`, `FEAT-05`, `FEAT-03`, `FEAT-06` | Mục 1.2 |
| C.2 | Danh sách vai trò dựng sẵn khác với IAM, có "Quản trị viên" như một vai trò | Theo IAM `FEAT-29`; Quản trị viên là cấp bậc | `PLT-13`, `FEAT-12` |
| C.3 | Không xác minh email, ai cũng đăng ký được bằng email người khác | Xác minh bắt buộc trước khi tạo workspace, không chặn việc khai báo | `BR-01.2`, `BR-01.3` |
| C.4 | Phải đăng nhập lại sau khi tạo workspace | Vào thẳng bằng phiên đã có | `BR-08.4` |
| C.5 | Tự khởi tạo ngay khi chọn ngành, không có bước xác nhận và không có chỗ sửa tên miền phụ, bản địa hoá | Một màn hình khai báo gồm đề xuất bản địa hoá; bước tóm tắt và xác nhận trước khi khởi tạo | `FEAT-03`, `FEAT-04`, `FEAT-08` |
| C.6 | Thanh tiến độ theo phần trăm cố định | Theo giai đoạn thật đã hoàn thành | `BR-08.5` |
| C.7 | Thử lại khởi tạo chỉ bằng mã theo dõi, không kiểm tra người gọi | Chỉ trong phiên của người đăng ký; mã chỉ để tra cứu | `BR-08.3` |
| C.8 | "Giao dịch duy nhất" với "các bước độc lập, lỗi không chặn" khi nạp cấu hình | Điều kiện Sẵn sàng chỉ gồm thành phần bắt buộc; cấu hình theo ngành và dữ liệu mẫu ngoài điều kiện, có đường thử lại | `BR-08.1`, `FEAT-09` |
| C.9 | Hai ngành trong danh mục không có phễu; phễu Bán lẻ lệch mô tả | Ngành chỉ hiển thị khi có mẫu đầy đủ; danh mục là tham số nền tảng | `BR-09.1`, `PLT-05` |
| C.10 | Xoá dữ liệu mẫu khi bản ghi mẫu đã bị sửa hoặc gắn với dữ liệu thật | Liệt kê riêng, cho chọn giữ lại hoặc xoá; không động tới dữ liệu thật | `BR-10.3` |
| C.11 | Dữ liệu mẫu tính vào báo cáo và kích hoạt tự động hoá | Không tính, không kích hoạt gửi ra ngoài | `BR-10.2` |
| C.12 | Nhiệm vụ "mời đồng nghiệp" hoàn thành khi mới gửi; nhiệm vụ tải ứng dụng không quan sát được | Hoàn thành theo kết quả; ứng dụng di động tính khi đăng nhập lần đầu bằng ứng dụng | `BR-11.1`, `FEAT-11` |
| C.13 | Không có cách thiết lập đội ngũ nhanh; "đội" dễ bị tạo bằng nhóm làm lộ dữ liệu giữa các đội | Thiết lập đội ngũ ba màn hình; đội là đơn vị tổ chức với vai trò gợi ý | `FEAT-12`, `BR-12.2` |
| C.14 | Mặc định vị trí và quản lý theo người mời | Theo IAM: không tự suy; trưởng nhóm và đội chọn tường minh | IAM `BR-09.5`, `FEAT-12` |
| C.15 | Thư mời có logo tự khai dùng được để giả mạo | Logo chỉ sau khi xác minh tên miền hoặc trả phí; giới hạn lời mời cho workspace dùng thử | `BR-14.2`, `BR-14.3` |
| C.16 | Người đã có tài khoản không có luồng chấp nhận; thư chuyển tiếp dùng được bởi người khác | Chấp nhận một bước cho người đã có tài khoản; chỉ đúng email được mời | `FEAT-15` |
| C.17 | Tên miền riêng chỉ kiểm tra bản ghi trỏ địa chỉ; không xử lý khi bản ghi bị gỡ | Bắt buộc chứng minh sở hữu; trạng thái Mất xác minh và quay về tên miền phụ | `FEAT-07` |
| C.18 | Danh mục tên bảo lưu quá ngắn; kiểm tra trùng lộ khách hàng khác | Danh mục là tham số nền tảng đầy đủ; thông báo không lộ workspace khác; giới hạn tần suất | `BR-06.3`, `BR-06.4`, `PLT-04` |
| C.19 | Một người sở hữu nhiều workspace không có đường đi (đăng ký lại bị chặn vì email đã tồn tại) | Tạo thêm workspace từ tài khoản đã có, có giới hạn dùng thử theo tài khoản | `FEAT-19` |
| C.20 | Thông điệp hướng dẫn theo lịch cứng ngày 1/3/7/12, sai khi độ dài dùng thử đổi | Theo hành vi, mốc cuối tương đối với ngày hết dùng thử, giờ gửi theo múi giờ workspace | `FEAT-18`, Mục 2.3 |
| C.21 | Tín hiệu khách hàng lớn đẩy email khách hàng vào kênh trao đổi nội bộ chung | Không chứa thông tin liên hệ trong kênh chung | `BR-05.1` |
| C.22 | Thời hạn dọn đăng ký bỏ dở 24 giờ không báo trước | 72 giờ, có email nhắc trước 24 giờ; là tham số nền tảng | `BR-02.2`, `PLT-02` |
| C.23 | Mật khẩu bắt buộc đủ bốn loại ký tự | Đánh giá theo độ mạnh thực tế, theo chính sách xác thực của nền tảng | `BR-01.4` |
| C.24 | Mục 7 cũ chứa câu hỏi về gia hạn dùng thử và ân hạn dữ liệu | Thuộc [`billing-subscription-srs.md`](./billing-subscription-srs.md) | Mục 7 |
| C.25 | Xác minh tên miền email bị dẫn chiếu vòng giữa tài liệu này và IAM | Đặc tả tại `FEAT-20`; IAM `FEAT-50` và `BR-14.2` dẫn về đây | `FEAT-20` |
| C.26 | Thư mời chuyển tiếp đủ để tạo tài khoản ("email coi như đã xác minh vì đã nhận được thư") | *(Phần "nút dùng một lần" đã được thay bởi C.44)* Nút dùng một lần; người chưa có tài khoản phải chứng minh sở hữu email lúc chấp nhận | `BR-15.4` |
| C.27 | Email từ nhà cung cấp danh tính doanh nghiệp được tin tuyệt đối | Tin có điều kiện | `BR-01.5` |
| C.28 | Tài khoản chưa xác minh giữ chỗ email của người khác; màn hình đăng ký lộ email nào đã có tài khoản | Không giữ chỗ; thông điệp trung tính | `BR-01.6`, `BR-01.7` |
| C.29 | Trưởng nhóm Đang chờ chấp nhận được đặt làm người phụ trách đơn vị, trái IAM; vai trò trưởng nhóm gắn cứng | IAM cho phép trong cùng lượt mời, hiệu lực khi đã chấp nhận; vai trò trưởng nhóm là tham số chọn lại được | `FEAT-12`, IAM `BR-21.2`, `PLT-13` |
| C.30 | *(Câu hỏi cách thấy dữ liệu nay là nhóm "các câu hỏi cách làm việc", C.53, C.56)* Mẫu 1 – 10 rơi về vai trò chỉ xem, cả đội thấy màn hình trống; nhân viên trong đội có thấy dữ liệu của nhau hay không chưa được hỏi | Vai trò chung Nhân viên Kinh doanh; hai câu hỏi về cách thấy dữ liệu ở màn hình 1 | `PLT-13`, `FEAT-12` |
| C.31 | Giới hạn lời mời mỗi giờ từ chối một phần thiết lập đội ngũ | Xếp hàng gửi dần; nới khi có tên miền đã xác minh | `BR-14.3`, `PLT-11` |
| C.32 | Người nhận huỷ được cả thông báo bắt buộc trước khi hết dùng thử | Thông báo bắt buộc thuộc billing, ngoài chuỗi hướng dẫn | `FEAT-18` |
| C.33 | Thông điệp gửi "ngày làm việc" nhưng không có lịch làm việc theo quốc gia; mốc rơi ngày nghỉ hoặc sau ngày hết hạn | Lịch mặc định theo quốc gia; dời về ngày làm việc liền trước; mốc sau hạn bị bỏ | Mục 2.3, `PLT-06` |
| C.34 | Workspace có dấu hiệu dùng thử lặp lại không bao giờ Sẵn sàng | *(Đã được thay bởi C.41 và C.48)* Sẵn sàng, áp cho mọi kênh | `BR-08.1`, `FEAT-01` |
| C.35 | Kênh tạo hộ không dựng sẵn được cấu hình vì chưa có Chủ sở hữu duyệt phiên hỗ trợ | Giai đoạn triển khai trước kích hoạt, chỉ cấu hình, không dữ liệu, không gửi lời mời | `BR-16.4` |
| C.36 | Áp mẫu ngành về sau không rõ làm gì với phễu đang có | Chỉ thêm, không động tới dữ liệu đang có | `BR-09.3` |
| C.37 | Nhiệm vụ "Đưa đội ngũ vào" hoàn thành với 1 người, lệch ngưỡng 3 thành viên của Đạt kích hoạt sử dụng | Hoàn thành khi 2 người đã chấp nhận | `FEAT-11` |
| C.38 | Chi nhánh tự đăng ký workspace riêng ngoài tầm quản trị của công ty | Gợi ý tham gia workspace công ty; báo Người có toàn quyền của workspace sở hữu tên miền | `BR-20.3` |
| C.39 | Gợi ý workspace công ty trước khi xác minh email và thông báo cho công ty sở hữu tên miền làm lộ thông tin chéo workspace | Chỉ gợi ý sau xác minh; báo trước cho người tạo; ngoại lệ tường minh của `NFR-07` và IAM `BR-51.3` | `BR-20.3`, `NFR-07` |
| C.40 | Thông điệp trung tính nhưng luồng màn hình khác nhau vẫn dò được email; liên kết Google đòi mật khẩu của tài khoản chưa xác minh; thay thế tài khoản chưa xác minh làm chính chủ mất công đã khai | Luồng giống hệt nhau; chỉ liên kết với tài khoản đã xác minh; cho chọn tiếp tục hoặc bắt đầu lại | `BR-01.6`, `BR-01.7`, `FEAT-01` |
| C.41 | Trạng thái "chờ xác nhận dùng thử" không có trong billing | *(Đã được thay bởi C.54)* Workspace ở trạng thái đang dùng thử của billing, chưa gửi lời mời và kết nối kênh cho tới khi xử lý thủ công | `BR-08.6` |
| C.42 | Quy mô tự khai đủ để nới giới hạn lời mời; xếp hàng không có trần ngày | Chỉ nới khi tên miền đã xác minh hoặc kinh doanh xác nhận; thêm trần ngày và tạm dừng hàng đợi | `BR-14.3`, `PLT-11` |
| C.43 | Nhiệm vụ đội ngũ không tính người vào qua liên kết, tệp hay tự gia nhập; ngoại lệ tạo hộ không còn xảy ra | Tính theo thành viên Đang hoạt động qua mọi đường; tạo hộ mở thẳng bước xác nhận danh sách | `FEAT-11` |
| C.44 | Nút Chấp nhận dùng một lần bị bộ quét thư tiêu thụ | Dùng nhiều lần tới khi chấp nhận; chống chuyển tiếp bằng mã | `BR-15.4` |
| C.45 | Khiếu nại tên miền email không có kết quả | Chấp thuận hoặc bác; không phản hồi thì theo bằng chứng; không tự gỡ thành viên | `BR-20.5`, `PLT-17` |
| C.46 | *(Phần kiêm nhiệm Đầy đủ đã được thay bởi C.52; phần Marketing bởi C.53)* Người ở hai đội không làm được việc của đội thứ hai; kênh đầu tiên không có đơn vị tiếp nhận; Marketing không nhập và phát sóng được | Kiêm nhiệm Đầy đủ kèm vai trò của cả hai đội; chọn đơn vị tiếp nhận khi kết nối kênh; câu hỏi riêng cho đội Marketing | `FEAT-12`, `FEAT-11` |
| C.47 | Sau kích hoạt đội triển khai không có kênh cấu hình; hết thời hạn giai đoạn triển khai không rõ xử lý | Phiên triển khai của IAM `BR-08.7`; hết `PLT-15` giữ cấu hình, báo nhân sự hợp đồng | `BR-16.4` |
| C.48 | Workspace chờ xử lý dùng thử mất ngày dùng thử trong lúc chờ; kết quả bác không rõ; phạm vi chặn chưa đủ | *(Phần chính sách thương mại đã được thay bởi C.54)* Thời hạn dùng thử tính từ lúc duyệt; bác → đã hết dùng thử chưa chuyển đổi; chặn lời mời, liên kết, tự gia nhập, kết nối kênh | `BR-08.6` |
| C.49 | Chọn "tiếp tục đăng ký đang dở" giữ lại mật khẩu kẻ gian đã đặt | Xác minh email vô hiệu mọi cách đăng nhập đặt trước đó | `BR-01.7` |
| C.50 | Hồ sơ khiếu nại tắt khả năng của bên đang giữ tên miền | Khiếu nại là trạng thái của hồ sơ; bên đang giữ không bị gián đoạn | `FEAT-20` |
| C.51 | Hàng đợi lời mời tạm dừng không có kết quả | Tiếp tục hoặc huỷ trong `PLT-20`; quá hạn tự chạy tiếp | `BR-14.3` |
| C.52 | Người ở hai đội mặc định kiêm nhiệm Đầy đủ làm dữ liệu riêng lộ sang đội kia; khách hàng tiềm năng của Marketing không tới được Kinh doanh | Kiêm nhiệm Chỉ xem vẫn nhận việc hàng đợi; câu hỏi đội nhận khách hàng tiềm năng; câu hỏi Marketing nêu từng ô | `FEAT-12` |
| C.53 | Câu hỏi gộp biểu mẫu và tệp nhập làm danh sách Marketing đổ vào hàng đợi Kinh doanh; câu hỏi đội nhận khách hàng tiềm năng chỉ hiện khi có Marketing; một tính năng thiếu vô hiệu cả câu | Biểu mẫu qua đơn vị tiếp nhận, hỏi khi có đội Kinh doanh; tệp do người nhập phụ trách, chuyển giao theo contacts; hai câu Marketing riêng; trưởng nhóm Marketing phát sóng trong đơn vị | `FEAT-12` |
| C.54 | Onboarding tự đặt chính sách thương mại cho workspace chờ xử lý dùng thử | Chính sách thuộc billing (Mục 7.2); onboarding chỉ chặn, hiển thị trạng thái chờ, cho trả phí ngay; không áp kênh tạo hộ | `BR-08.6` |
| C.55 | Giới hạn "mỗi ngày" phụ thuộc múi giờ tự đặt; hàng đợi tự chạy lại làm việc tạm dừng mất tác dụng | 24 giờ trượt; rủi ro còn lại được chấp nhận có chủ đích | `BR-14.3` |
| C.56 | Biểu mẫu website được chọn sẵn đơn vị tiếp nhận khác nhau ở `FEAT-11` và `FEAT-12`; câu trả lời `FEAT-12` không có chỗ lưu | Lưu vào đơn vị tiếp nhận mặc định theo loại nguồn của IAM; `FEAT-11` dùng giá trị đó | `FEAT-11`, `FEAT-12`, IAM `CFG-35-01` |
| C.57 | Bật Phát sóng cho trưởng nhóm Marketing sửa vai trò Quản lý dùng chung; Marketing nhập được nhưng không sửa, không gán được; Marketing thấy toàn bộ khách hàng mà người thiết lập không được báo | Vai trò Quản lý Marketing riêng; nhập đi kèm Sửa và Gán của mình; hiển thị rõ phạm vi xem của Marketing, cho thu về đơn vị | `FEAT-12`, IAM `FEAT-29` |
| C.58 | Mẫu trên 200 người không dùng được với tên đơn vị duy nhất | Hỏi chi nhánh; đặt tên đội theo chi nhánh | `PLT-13` |
| C.59 | Chi nhánh tự lập workspace chỉ được báo khi công ty bật tự gia nhập | Báo mọi khi tên miền đã xác minh | `BR-20.3` |
| C.60 | Mẫu chi nhánh dồn mọi hàng đợi về chi nhánh đầu tiên hoặc làm mọi chi nhánh thấy vé của nhau | Mặc định ghi đè cho từng chi nhánh; mỗi nguồn nhận đội của chi nhánh tạo ra nó (câu hỏi giám đốc chi nhánh đã bỏ theo C.66; đơn vị trung tâm có mặc định cấp workspace theo C.67) | `PLT-13`, `FEAT-12` |
| C.61 | Danh sách được dựng trước kích hoạt lệch với bảng tóm tắt; nhà cung cấp có thể tự nới quyền toàn workspace trước khi có Chủ sở hữu | Danh sách thống nhất; các nới rộng toàn workspace chỉ soạn sẵn, hiệu lực khi Chủ sở hữu xác nhận; vai trò tự tạo có hiệu lực ngay nhưng chỉ tới tay người dùng qua dòng mời Chủ sở hữu xác nhận, dòng mời mang vai trò có ô Toàn workspace được đánh dấu và bác được | `BR-16.4` |
| C.62 | Lưu lựa chọn đầu tiên làm mặc định chung phá mẫu chi nhánh; nguồn do giám đốc chi nhánh hay Marketing tạo vào nhầm hàng đợi | Mặc định ghi đè theo chi nhánh; câu hỏi đặt mặc định chung tường minh và không áp cho mẫu chi nhánh | `FEAT-11`, `FEAT-12`, `PLT-13` |
| C.63 | Giám đốc chi nhánh chỉ có dòng email, không vai trò, không là quản lý của trưởng nhóm | *(Câu hỏi bật xem toàn nhánh đã được bỏ theo C.66)* Vai trò Quản lý, Đơn vị chính chi nhánh, người phụ trách và quản lý trực tiếp của trưởng nhóm | `FEAT-12` |
| C.64 | Thu về chỉ áp cho Marketing trong khi Quản lý Marketing là người phát sóng | Áp cho cả hai vai trò; nêu hệ quả đối tượng có thể rỗng | `FEAT-12` |
| C.65 | Chủ sở hữu không bác được từng mục soạn sẵn trước kích hoạt | Xác nhận hoặc bác từng mục kèm khác biệt | `FEAT-16`, `BR-16.4` |
| C.66 | Câu "giám đốc chi nhánh thấy toàn chi nhánh" trả lời Không vẫn ra Có, và tham số kèm theo áp cho toàn workspace | Bỏ câu hỏi; giám đốc thấy và phân việc qua vai trò Quản lý và chuỗi cấp dưới | `FEAT-12`, `PLT-13` |
| C.67 | Đơn vị trung tâm trong mẫu chi nhánh không có đơn vị tiếp nhận mặc định | Hỏi đội nhận nguồn của đơn vị trung tâm, lưu làm mặc định cấp workspace | `FEAT-12` |
| C.68 | Phần chọn đơn vị tiếp nhận ở `FEAT-11` mang mã AC của `BR-11.1` | Tách thành `BR-11.4` | `BR-11.4` |
| C.69 | Workspace tạo hộ có hợp đồng vẫn chịu giới hạn lời mời của dùng thử | Không áp cho tạo hộ | `BR-14.3` |
| C.70 | Loại nguồn "Liên hệ lại từ chiến dịch" không có đơn vị tiếp nhận mặc định, tài khoản gửi phải chờ Người có toàn quyền; hệ quả của "Thu về" bỏ qua nguồn nới | Dùng đội nhận khách hàng tiềm năng từ biểu mẫu; nêu nguồn nới trong câu hệ quả | `FEAT-12` |
