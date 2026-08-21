# SRS — Quản lý Người dùng, Nhóm, Đơn vị Tổ chức & Phân quyền Workspace

## 0. Thông tin tài liệu

| | |
| --- | --- |
| Hệ thống | CRM — phân hệ Quản trị Workspace (Identity & Access Management) |
| Phạm vi | Khởi tạo Workspace, Người dùng, Nhóm, Đơn vị tổ chức, Vai trò & Quyền, Cấp quyền tạm thời, Phạm vi hiển thị dữ liệu, Chính sách truy cập nâng cao, Phân quyền theo bản ghi, Che dữ liệu nhạy cảm, Nhật ký thay đổi quyền |
| Tính chất | **SRS hồi tố** — các tính năng dưới đây đã được xây dựng và đang chạy thật; tài liệu này được biên soạn lại theo đúng hành vi hệ thống đang có, để lấp khoảng trống "làm trước, viết SRS sau". Đây KHÔNG phải tài liệu đề xuất thiết kế mới. |
| Ngày biên soạn | 2026-08-21 (v1) |
| Người biên soạn | BA (tổng hợp qua khảo sát hệ thống đang vận hành) |
| Phiên bản | **v2 — 2026-08-21**, sau phiên rà soát/grilling cùng Product Owner. v1 chỉ mô tả hành vi hệ thống tại thời điểm khảo sát; v2 bổ sung các quyết định đã chốt (tính năng mới, thay đổi quy tắc, mã lỗi phải chuẩn hoá) và tách phần "đã quyết định nhưng CHƯA triển khai" ra [Phụ lục B — Lộ trình triển khai](#phụ-lục-b--lộ-trình-triển-khai--implementation-roadmap). Cập nhật cuối phiên (2026-08-21): cả 10 mục ở Phụ lục B đã có GitHub issue tương ứng (#4–#13 tại `crmsaassaudi/product-management`), tạo trực tiếp bởi AI theo yêu cầu tường minh của Product Owner. |

### 0.1 Quy ước đọc tài liệu

- Mỗi **Chức năng (F-xx)** mô tả một nhóm nghiệp vụ hoàn chỉnh (thường gồm nhiều thao tác: tạo/xem/sửa/xoá/hành động đặc thù).
- Mỗi chức năng có: **Mô tả**, **Actor**, **Điều kiện tiên quyết**, **Luồng chính** (từng bước theo góc nhìn người dùng), **Luồng ngoại lệ**, **Quy tắc nghiệp vụ (BR)**, **Kết quả đầu ra**.
- Mục **"Vấn đề tồn đọng / cần quyết định"** ở cuối mỗi nhóm lớn ghi lại các khoảng trống hoặc rủi ro nghiệp vụ **chưa có quyết định** — cần Product Owner quyết định hướng xử lý ở một phiên rà soát tiếp theo.
- 🔧 **[Đã quyết định — chờ triển khai]** đánh dấu một quy tắc/tính năng đã được chốt phương án trong phiên rà soát 2026-08-21 nhưng **code hiện tại chưa phản ánh đúng quy tắc này** — chi tiết công việc cần làm nằm ở [Phụ lục B](#phụ-lục-b--lộ-trình-triển-khai--implementation-roadmap).

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả đầy đủ nghiệp vụ cho toàn bộ chuỗi: một khách hàng đăng ký sử dụng dịch vụ → workspace của họ được khởi tạo → chủ workspace mời đồng đội, tổ chức đội nhóm, dựng sơ đồ tổ chức, và cấp đúng quyền hạn cho từng người — để mọi thành viên chỉ thấy và làm được đúng phần việc được giao.

### 1.2 Phạm vi tài liệu

11 nhóm chức năng: (A) Khởi tạo Workspace, (B) Người dùng, (C) Nhóm, (D) Đơn vị tổ chức, (E) Vai trò & Quyền, (F) Cấp quyền tạm thời, (G) Phạm vi hiển thị dữ liệu, (H) Chính sách truy cập nâng cao, (I) Phân quyền theo bản ghi, (J) Che dữ liệu nhạy cảm, (K) Nhật ký thay đổi quyền.

Ngoài phạm vi: đăng nhập/xác thực (SSO, mật khẩu, phiên đăng nhập), các module nghiệp vụ tiêu thụ dữ liệu (Khách hàng, Cơ hội, Ticket…) trừ phần chúng bị ảnh hưởng bởi phạm vi hiển thị dữ liệu.

### 1.3 Thuật ngữ nghiệp vụ

| Thuật ngữ | Ý nghĩa |
| --- | --- |
| Workspace | Không gian làm việc riêng của một khách hàng/tổ chức, có tên miền phụ riêng, dữ liệu tách biệt hoàn toàn với workspace khác. Tên gọi kỹ thuật tương đương (dùng khi trao đổi với Dev) là **Tenant**. |
| Cấp bậc thành viên (Membership Tier) | Trục xác định mức độ đặc quyền của một người **trong một workspace cụ thể**: **Chủ sở hữu (Owner)** / **Quản trị viên (Admin)** / **Thành viên (Member)**. Owner và Admin có toàn quyền; đây là trục hoàn toàn khác với Vai trò (Role) bên dưới — một người vẫn cần Vai trò để xác định chi tiết mình làm được gì nếu chỉ ở cấp Member. |
| Chủ sở hữu (Owner) | Người tạo/sở hữu workspace, có toàn quyền, không thể bị hạ quyền qua thao tác thông thường — chỉ mất qua chức năng Chuyển nhượng quyền sở hữu (xem A9). |
| Quản trị viên (Admin) | Cấp bậc thành viên cao nhất có thể trao/thu hồi được, cũng có toàn quyền như Owner. |
| Thành viên (Member) | Người dùng thông thường, quyền hạn phụ thuộc vai trò/nhóm được gán. |
| Vai trò nền tảng (Platform Role) | Trục **độc lập với workspace** — nhân sự vận hành nền tảng (Super Admin) hoặc người dùng thông thường (User). Không liên quan gì tới Cấp bậc thành viên hay Vai trò trong một workspace cụ thể. |
| Vai trò (Role) | Một tập hợp quyền hạn có thể gán cho thành viên hoặc nhóm. Có vai trò "dựng sẵn" (hệ thống tạo mặc định) và vai trò "tự tạo" (tenant tự định nghĩa). |
| Nhóm (Group) | Tập hợp thành viên dùng để cấp quyền/điều phối công việc theo tập thể (không phải sơ đồ tổ chức). |
| Đơn vị tổ chức (Org Unit) | Nút trong sơ đồ tổ chức (phòng/ban/nhóm...), dùng để xác định "ai thuộc bộ phận nào" và từ đó suy ra phạm vi dữ liệu được xem. |
| Phạm vi hiển thị dữ liệu (Data Scope) | Mức độ rộng của dữ liệu một vai trò được thấy: Chỉ của mình → Của mình + cấp dưới/đơn vị → Của mình + cả nhánh đơn vị → Toàn workspace. |
| Quyền tạm thời (Role Assignment) | Việc cấp thêm quyền có thời hạn, cần người khác phê duyệt, tự động hết hạn — dùng cho các tình huống cần quyền cao hơn bình thường trong một khoảng thời gian ngắn. |
| Chính sách truy cập (Access Policy / ABAC) | Luật bổ sung dựa trên điều kiện (vd. "chỉ được sửa khi đang ở trạng thái nháp") để thu hẹp hoặc mở thêm quyền đã có, áp dụng cho từng nghiệp vụ cụ thể. |
| Quyền trên bản ghi (Object ACL) | Cấp/chặn quyền cho một bản ghi cụ thể (một khách hàng cụ thể, một hợp đồng cụ thể...), thay vì cả loại dữ liệu. |
| Che dữ liệu nhạy cảm | Ẩn bớt một phần thông tin (email, số điện thoại, giá trị hợp đồng...) khỏi người không có quyền xem đầy đủ. |

### 1.4 Actor

| Actor | Mô tả |
| --- | --- |
| Nhân sự vận hành nền tảng (Super Admin) | Đội ngũ nội bộ, có thể truy cập mọi workspace để hỗ trợ/vận hành. |
| Chủ sở hữu Workspace (Owner) | Toàn quyền trong workspace của mình. |
| Quản trị viên (Admin) | Toàn quyền trong workspace, do Owner/Admin khác cấp. |
| Thành viên (Member) | Quyền theo vai trò/nhóm được gán. |
| Người phê duyệt | Bất kỳ thành viên có quyền phê duyệt cấp quyền tạm thời (thường là quản lý cấp trên). |
| Hệ thống | Các bước tự động thực hiện khi có sự kiện (workspace khởi tạo xong, thành viên bị gỡ...). |

---

## 2. Danh sách chức năng

| Nhóm | Mã | Chức năng |
| --- | --- | --- |
| A. Khởi tạo Workspace | A1, A3–A9 | Đăng ký, khởi tạo nội bộ, theo dõi tiến trình, khởi tạo dữ liệu mặc định, kiểm tra sức khỏe cấu hình, cấu hình ngôn ngữ/khu vực, bật/tắt tính năng mở rộng, **chuyển nhượng quyền sở hữu (A9, mới)** — A2 đã gỡ bỏ (xem ghi chú tại A2) |
| B. Người dùng | B1–B8 | Mời/thêm thành viên, tra cứu, cập nhật & phân quyền, đổi vai trò quản trị, gỡ/xoá, đặt lại mật khẩu & trạng thái, xem quyền hiệu lực, tuỳ chỉnh cá nhân |
| C. Nhóm | C1–C4 | Tạo & cấu hình nhóm (kể cả phân cấp), quản lý thành viên nhóm, xem trước quyền nhóm, xoá nhóm |
| D. Đơn vị tổ chức | D1–D3 | Xây dựng & quản lý cây tổ chức, di chuyển đơn vị, xoá đơn vị |
| E. Vai trò & Quyền | E1–E6 | Xem danh mục quyền, tạo/sao chép vai trò, cập nhật vai trò, lịch sử & khôi phục, xoá vai trò, vai trò dựng sẵn |
| F. Cấp quyền tạm thời | F1–F4 | Yêu cầu, phê duyệt/từ chối, thu hồi, báo cáo kiểm định |
| G. Phạm vi hiển thị dữ liệu | G1–G2 | Cấu hình phạm vi, quy tắc phân giải theo cấp quản lý/đơn vị tổ chức |
| H. Chính sách truy cập nâng cao | H1–H3 | Tạo & quản lý chính sách, mô phỏng/kiểm thử, lịch sử & khôi phục |
| I. Phân quyền theo bản ghi | I1 | Cấp/thu hồi/xem quyền trên 1 bản ghi |
| J. Che dữ liệu nhạy cảm | J1 | Ẩn/hiện dữ liệu nhạy cảm theo quyền |
| K. Nhật ký thay đổi quyền | K1 | Tra cứu lịch sử thay đổi quyền/vai trò |

---

## A. KHỞI TẠO WORKSPACE

### A1. Đăng ký & khởi tạo workspace mới (tự đăng ký)

🔧 **[Đã quyết định — chờ triển khai]** Nội dung dưới đây là **luồng đích đã chốt** (quyết định 2026-08-21, xem [Phụ lục B](#phụ-lục-b--lộ-trình-triển-khai--implementation-roadmap) cho công việc cần làm). Trạng thái hiện tại: hệ thống có **2 luồng song song** cho cùng mục đích — một form đăng ký 1 bước cũ (cho phép tự chọn tên miền phụ) và luồng wizard nhiều bước mới bên dưới — cho ra 2 kết quả không hoàn toàn giống nhau (luồng cũ thiếu tích hợp dịch vụ hỗ trợ tự động, thiếu bước hỏi quy mô/mục đích sử dụng nên không tạo được phòng ban gợi ý/dữ liệu mẫu đúng ngữ cảnh). Quyết định: **gộp về đúng 1 luồng** — form cũ sẽ được thay thế hoàn toàn bằng wizard này.

**Mô tả:** Một người chưa có tài khoản tự đăng ký để tạo workspace của riêng mình.

**Actor:** Khách vãng lai (chưa đăng nhập).

**Điều kiện tiên quyết:** Không có — đây là điểm vào công khai.

**Luồng chính:**

1. Người dùng nhập email, họ tên, mật khẩu → hệ thống tạo tài khoản.
2. Hệ thống hỏi thêm thông tin công ty: tên công ty, quy mô đội ngũ, mục đích sử dụng (có thể trả lời dần qua nhiều màn hình, không bắt buộc trả lời hết một lần) — **bắt buộc phải hỏi đủ các bước này**, không có đường tắt bỏ qua để giữ nguyên trải nghiệm 1-bước như form cũ.
3. Người dùng xác nhận tạo workspace → hệ thống **tự sinh** tên miền phụ (subdomain) duy nhất từ tên công ty (không cho người dùng tự chọn tên miền phụ ở bất kỳ bước nào), đưa yêu cầu khởi tạo vào hàng chờ xử lý.
4. Hệ thống trả về một mã theo dõi tiến trình để màn hình chờ tự động cập nhật trạng thái (xem A4).
5. Khi khởi tạo xong, người dùng được chuyển tới trang đăng nhập của workspace vừa tạo, với vai trò **Chủ sở hữu (Owner)**.

**Luồng ngoại lệ:**

- Mật khẩu không đủ mạnh (thiếu hoa/thường/số/ký tự đặc biệt, dưới 8 ký tự) → yêu cầu nhập lại.
- Tên miền phụ theo tên công ty đã trùng → hệ thống tự thêm hậu tố để tạo tên khác, không cho người dùng chọn tay.
- Quá trình khởi tạo thất bại (lỗi hệ thống) → workspace được đánh dấu thất bại, dữ liệu tạm được dọn sạch, người dùng có thể thử lại.
- Người dùng bỏ dở sau bước 1 (không hoàn tất tạo workspace) quá 24 giờ → tài khoản tạm được hệ thống tự động dọn dẹp định kỳ (không tính là workspace đã tồn tại).

**Quy tắc nghiệp vụ:**

- BR-1: Người tự đăng ký luôn trở thành **Owner** của workspace mới — không có lựa chọn vai trò nào khác ở bước này.
- BR-2: Một email chỉ gắn với một tài khoản người dùng duy nhất trong toàn hệ thống (không phân biệt theo workspace).
- BR-3: Tên miền phụ phải là duy nhất toàn hệ thống, luôn do hệ thống sinh ra, không có đường nhập tay.
- BR-4: **Đây là điểm vào duy nhất** để một người hoàn toàn mới tạo workspace — không tồn tại một luồng rút gọn/thay thế nào khác cho cùng mục đích.

**Kết quả đầu ra:** Một workspace mới ở trạng thái sẵn sàng sử dụng, với Owner là người vừa đăng ký, cùng bộ dữ liệu/cấu hình mặc định (xem A5).

---

### A2. ~~Tạo thêm workspace cho tài khoản đã có sẵn~~ — ĐÃ GỠ BỎ

**Trạng thái:** Đã xoá khỏi hệ thống ngày 2026-08-21. Chức năng này (một người dùng đã đăng nhập tạo thêm 1 workspace mới cho chính mình, không qua wizard) tồn tại ở tầng API nhưng **không có màn hình nào trong sản phẩm gọi tới** — xác nhận qua rà soát mã nguồn không tìm thấy bất kỳ lời gọi nào từ giao diện người dùng. Nếu nhu cầu "một người sở hữu nhiều workspace" phát sinh trở lại trong tương lai, nên đi qua đúng wizard ở A1 (đăng xuất, đăng ký lại bằng cùng email) thay vì khôi phục lại đường tắt này.

---

### A3. Khởi tạo workspace theo yêu cầu nội bộ (đội ngũ vận hành tạo hộ khách hàng)

**Mô tả:** Đội ngũ Sales/CS nội bộ khởi tạo workspace thay cho khách hàng doanh nghiệp (mô hình bán hàng trực tiếp), sau đó mời người quản trị phía khách hàng vào.

**Actor:** Nhân sự vận hành nền tảng (qua hệ thống nội bộ, không phải giao diện khách hàng).

**Điều kiện tiên quyết:** Có thông tin công ty khách hàng, email người quản trị phía khách hàng, gói dịch vụ đã chốt.

**Luồng chính:**

1. Nhân sự nhập tên công ty, email/họ tên người quản trị, gói dịch vụ → gửi yêu cầu khởi tạo.
2. Hệ thống ghi nhận yêu cầu, đưa vào hàng chờ xử lý (có cơ chế chống gửi trùng nếu gửi lại yêu cầu giống hệt).
3. Workspace được khởi tạo tương tự A1 (tên miền phụ tự sinh, dữ liệu mặc định được tạo).
4. Nhân sự gửi lời mời tới người quản trị phía khách hàng với vai trò mong muốn (Chủ sở hữu/Quản trị/Thành viên) — người này nhận email đặt mật khẩu để bắt đầu sử dụng.

**Luồng ngoại lệ:**

- Gửi trùng yêu cầu khởi tạo cho cùng một công ty → hệ thống nhận diện và không tạo workspace thứ hai.

**Quy tắc nghiệp vụ:**

- BR-1: Đường này dành riêng cho nội bộ, không mở công khai.

**Kết quả đầu ra:** Workspace được tạo sẵn, người quản trị phía khách hàng nhận được lời mời kích hoạt.

---

### A4. Theo dõi & xử lý sự cố khởi tạo workspace

**Mô tả:** Cho phép người dùng (hoặc hệ thống) theo dõi trạng thái một yêu cầu khởi tạo workspace đang xử lý, và thử lại nếu thất bại.

**Actor:** Người dùng đang chờ workspace được tạo (A1/A3).

**Luồng chính:**

1. Màn hình chờ liên tục hỏi trạng thái theo mã theo dõi được cấp ở bước tạo.
2. Trạng thái lần lượt: Đang chờ xử lý → Đang khởi tạo → Sẵn sàng (hoặc Thất bại).
3. Khi "Sẵn sàng", hệ thống trả về đường dẫn đăng nhập vào workspace mới.

**Luồng ngoại lệ:**

- Nếu "Thất bại" → người dùng có thể yêu cầu thử lại; hệ thống tiếp tục từ trạng thái dở dang thay vì làm lại từ đầu, trừ lần thử cuối cùng (sau 3 lần thất bại liên tiếp) — khi đó mọi phần đã tạo dở (tài khoản đăng nhập ngoài, workspace, v.v.) sẽ được dọn sạch để tránh để lại dữ liệu rác.

**Quy tắc nghiệp vụ:**

- BR-1: Tối đa 3 lần thử tự động; sau đó cần thử lại thủ công qua yêu cầu người dùng.
- BR-2: Việc thử lại thủ công dựa vào chính mã theo dõi được cấp ban đầu — **không** có bước xác minh thêm rằng người gọi có đúng là chủ của yêu cầu ban đầu hay không (xem "Vấn đề tồn đọng").

**Kết quả đầu ra:** Trạng thái khởi tạo rõ ràng cho người dùng; workspace hỏng được dọn sạch thay vì để lại nửa vời.

---

### A5. Khởi tạo dữ liệu & cấu hình mặc định khi workspace sẵn sàng

**Mô tả:** Ngay khi một workspace được tạo xong, hệ thống **tự động** chuẩn bị sẵn một bộ khung để Owner có thể bắt đầu làm việc ngay mà không cần cấu hình từ số 0.

**Actor:** Hệ thống (tự động, không cần thao tác người dùng).

**Luồng chính (mỗi bước độc lập, một bước lỗi không chặn các bước còn lại):**

1. Tạo cấu hình CRM mặc định (pipeline cơ hội, quy trình xử lý ticket, quy tắc phân công mặc định).
2. Tạo sẵn các **vai trò dựng sẵn** phù hợp cho mọi workspace (xem E6).
3. Dựng **sơ đồ tổ chức khởi điểm**: một đơn vị gốc "Trụ sở chính", đặt Owner vào đơn vị đó; nếu quy mô đội ngũ khai báo đủ lớn, tự tạo thêm vài phòng ban gợi ý theo mục đích sử dụng đã khai báo.
4. Tạo một **nhóm mặc định** chứa Owner, chưa gắn quyền gì đặc biệt — làm điểm khởi đầu để Owner tự tổ chức thêm.
5. Nếu là workspace có chủ sở hữu (không phải khởi tạo nội bộ chưa gán người), sinh thêm **dữ liệu mẫu** (vài khách hàng/cơ hội mẫu phù hợp mục đích sử dụng đã khai báo) để Owner hình dung ngay giao diện có dữ liệu thay vì trống trơn.

**Quy tắc nghiệp vụ:**

- BR-1: Toàn bộ bước này **không** có màn hình riêng cho người dùng theo dõi — chạy ngầm ngay sau khi workspace chuyển trạng thái sẵn sàng.
- BR-2: Nếu workspace đã có dữ liệu khách hàng thật (vd. do đã dùng thử trước đó) → **không** sinh dữ liệu mẫu chồng lên.

**Kết quả đầu ra:** Owner đăng nhập vào một workspace đã có sẵn cấu hình cơ bản, sơ đồ tổ chức khởi điểm, và dữ liệu mẫu để tham khảo — sẵn sàng mời đồng đội ngay.

---

### A6. Kiểm tra sức khỏe cấu hình workspace

**Mô tả:** Cho phép Owner/Admin nhanh chóng biết workspace của mình còn thiếu cấu hình gì trước khi vận hành chính thức.

**Actor:** Owner/Admin.

**Luồng chính:**

1. Người dùng mở màn hình "Sức khỏe workspace".
2. Hệ thống liệt kê các cảnh báo, gồm 2 mức:
   - **Cảnh báo nghiêm trọng**: có thành viên chưa được gán đơn vị tổ chức; có thành viên chưa có vai trò nào.
   - **Cảnh báo nhẹ**: có vai trò tự tạo nhưng chưa khai báo phạm vi hiển thị dữ liệu; sơ đồ tổ chức mới chỉ có đúng 1 đơn vị gốc (chưa thực sự được xây dựng).

**Quy tắc nghiệp vụ:**

- BR-1: Đây là công cụ **chỉ mang tính tham khảo** — không chặn bất kỳ thao tác nào, không tự động sửa gì.

**Kết quả đầu ra:** Danh sách việc cần làm để hoàn thiện cấu hình workspace.

---

### A7. Cấu hình ngôn ngữ/khu vực mặc định của workspace

**Mô tả:** Cho phép chỉnh ngôn ngữ, múi giờ, định dạng ngày, đơn vị tiền tệ mặc định áp dụng cho toàn workspace (từng thành viên vẫn có thể tự override ngôn ngữ/múi giờ riêng — xem B8).

**Actor:** Owner/Admin.

**Kết quả đầu ra:** Cấu hình mặc định mới áp dụng cho mọi thành viên chưa tự tuỳ chỉnh riêng.

---

### A8. Bật/tắt tính năng mở rộng cho workspace (nội bộ)

**Mô tả:** Một số nhóm quyền (ví dụ: xuất/nhập dữ liệu hàng loạt, xem toàn bộ dữ liệu bất kể phân quyền, thư viện nội dung mạng xã hội...) không bật mặc định cho mọi workspace mà cần đội ngũ vận hành bật riêng theo gói dịch vụ đã đăng ký.

**Actor:** Nhân sự vận hành nền tảng (qua công cụ nội bộ).

**Quy tắc nghiệp vụ:**

- BR-1: Việc bật/tắt có tác động ngay lập tức tới mọi vai trò/thành viên đang có liên quan trong workspace đó.

---

### A9. Chuyển nhượng quyền sở hữu Workspace 🆕

🔧 **[Đã quyết định — chờ triển khai]** Tính năng hoàn toàn mới, chưa tồn tại trong hệ thống — chốt phương án trong phiên rà soát 2026-08-21 để lấp khoảng trống: nhiều nơi trong hệ thống từ chối cấp quyền Owner với lý do "hãy chuyển nhượng quyền sở hữu", nhưng chức năng đó chưa từng được xây.

**Mô tả:** Cho phép Chủ sở hữu (Owner) hiện tại chuyển giao quyền sở hữu workspace cho một thành viên khác, thông qua cơ chế xác nhận hai chiều (không đổi ngang một phía) để tránh workspace bị "cướp" nếu Owner bấm nhầm.

**Actor:** Chủ sở hữu (Owner) hiện tại (khởi tạo yêu cầu); Thành viên được chọn nhận chuyển nhượng (xác nhận).

**Điều kiện tiên quyết:** Người khởi tạo đang là Owner của workspace; người được chọn nhận đã là thành viên của chính workspace đó.

**Luồng chính:**

1. Owner chọn 1 thành viên trong workspace để chuyển nhượng quyền sở hữu, và chọn **vai trò bản thân sẽ giữ sau khi chuyển nhượng xong**: Quản trị viên (ở lại hỗ trợ) hoặc Thành viên (rút lui hoàn toàn).
2. Hệ thống gửi yêu cầu xác nhận tới người được chọn (email + hiển thị banner trong workspace); yêu cầu ở trạng thái **"Đang chờ xác nhận"** — chưa có tác dụng gì.
3. Người nhận bấm xác nhận → quyền sở hữu chuyển ngay lập tức; người chuyển nhượng chuyển sang cấp bậc đã chọn ở bước 1.
4. Cả 2 bên đều thấy banner trạng thái trong lúc yêu cầu đang chờ xử lý.

**Luồng ngoại lệ:**

- Yêu cầu **tự động hết hạn sau 72 giờ** nếu người nhận không phản hồi → hệ thống gửi email thông báo hết hạn cho cả 2 bên để khép lại vòng lặp giao tiếp.
- Owner có thể **huỷ yêu cầu thủ công bất kỳ lúc nào** trước khi người nhận xác nhận (kể cả trước khi hết hạn 72h) — ví dụ lỡ chọn nhầm người.
- **Chỉ 1 yêu cầu được tồn tại tại một thời điểm** cho một workspace — muốn gửi yêu cầu mới (cho người khác) phải huỷ yêu cầu cũ trước.
- Nếu người được chọn nhận bị gỡ khỏi workspace trong lúc yêu cầu đang chờ (do người khác xử lý song song) → yêu cầu **tự động huỷ ngay lập tức**, không chờ hết hạn 72h.
- Người nhận không thể là chính người khởi tạo (không tự chuyển nhượng cho bản thân).

**Quy tắc nghiệp vụ:**

- BR-1: Quyền sở hữu chỉ thực sự đổi tay khi **cả 2 bên đồng thuận** (Owner khởi tạo + người nhận xác nhận) — không có đường "ép chuyển" một phía.
- BR-2: Vai trò của Owner cũ sau khi chuyển nhượng **do chính họ chọn tại thời điểm khởi tạo yêu cầu**, không phải suy đoán tự động — vì "bàn giao nhưng ở lại hỗ trợ" và "rút lui hoàn toàn" là 2 nhu cầu khác nhau, hệ thống không đoán thay người dùng.
- BR-3: Đây là thao tác được ghi lại đầy đủ trong nhật ký thay đổi quyền (nhóm K) — chuyển quyền sở hữu là thay đổi cao nhất trong toàn bộ hệ phân quyền của một workspace.

**Kết quả đầu ra:** Workspace có Owner mới; Owner cũ ở đúng cấp bậc đã chọn trước; cả 2 bên nhận được xác nhận qua email.

---

### Vấn đề tồn đọng / cần quyết định — Nhóm A

- **Việc "thử lại" một yêu cầu khởi tạo đang thất bại (A4) không xác minh người gọi có đúng là người tạo yêu cầu ban đầu** — chỉ dựa vào mã theo dõi. Cần đánh giá rủi ro lộ mã theo dõi.
- **Không có cơ chế dọn dẹp cho workspace bị "kẹt" ở trạng thái thất bại** nếu bước dọn dẹp cuối cùng (sau 3 lần thử) chỉ dọn được một phần — hiện chỉ có việc dọn tài khoản người dùng bỏ dở (không phải workspace bỏ dở).

---

## B. NGƯỜI DÙNG

### B1. Mời / thêm thành viên vào workspace

**Mô tả:** Cho phép Owner/Admin/người có quyền thêm người đưa một người (đã có tài khoản hoặc chưa) vào workspace hiện tại.

**Actor:** Người có quyền "Tạo người dùng".

**Điều kiện tiên quyết:** Đang thao tác trong 1 workspace cụ thể.

**Luồng chính:**

1. Người thực hiện nhập: email người được mời; vai trò workspace (Quản trị/Thành viên); (tuỳ chọn) một hoặc nhiều vai trò quyền hạn; (tuỳ chọn) đơn vị tổ chức; (tuỳ chọn) người quản lý trực tiếp; (tuỳ chọn) các nhóm sẽ tham gia ngay.
2. Trước khi gửi, người thực hiện có thể **xem trước** người này sẽ thấy/làm được gì với lựa chọn hiện tại (không lưu gì, chỉ để tham khảo).
3. Hệ thống kiểm tra email đã tồn tại tài khoản trong hệ thống (không phân biệt workspace) hay chưa:
   - **Đã có tài khoản, chưa ở workspace này** → thêm thẳng vào workspace với các lựa chọn đã chọn.
   - **Chưa từng có tài khoản** → tạo tài khoản mới, gửi email hướng dẫn đặt mật khẩu, rồi thêm vào workspace.
4. Nếu có chọn nhóm ở bước 1 → thành viên mới được thêm vào (các) nhóm đó ngay.
5. Thành viên mới xuất hiện trong danh sách workspace.

**Luồng ngoại lệ:**

- Email đã là thành viên của **chính workspace này** → báo lỗi "đã có trong workspace", không tạo trùng.
- Không chọn vai trò quyền hạn nào → hệ thống tự gán vai trò "Chỉ xem" mặc định (an toàn nhất — tránh trường hợp người mới đăng nhập vào thấy trống trơn không làm được gì).
- Không chọn đơn vị tổ chức/người quản lý → mặc định lấy theo đúng vị trí của **người mời** (giả định hợp lý: người ta thường mời vào đúng đội của mình).
- Có sự cố khi tạo tài khoản (trường hợp tài khoản hoàn toàn mới) → toàn bộ thao tác được huỷ, không để lại tài khoản "treo" nửa vời.

**Quy tắc nghiệp vụ:**

- BR-1: **Không thể mời ai đó làm "Chủ sở hữu"** qua chức năng này — chỉ được chọn Quản trị viên hoặc Thành viên.
- BR-2: **Chỉ được cấp vai trò "Quản trị viên" nếu bản thân người mời đã có toàn quyền** (là Owner/Admin) — người có quyền "Tạo người dùng" nhưng không phải Owner/Admin thì không thể tự nâng người khác (hoặc gián tiếp chính mình) lên Quản trị viên.
- BR-3: **Người mời chỉ được gán cho người khác những quyền mà bản thân đang có** — không thể cấp vượt quá năng lực quyền hạn của chính mình, dù có quyền "Tạo người dùng".
- BR-4: Vị trí (đơn vị tổ chức, người quản lý) khai báo phải hợp lệ trong workspace hiện tại, không được trỏ sang workspace khác.

**Kết quả đầu ra:** Thành viên mới có mặt trong workspace với đúng vai trò/quyền/vị trí tổ chức đã chọn (hoặc mặc định an toàn nếu bỏ trống).

---

### B2. Xem & tra cứu thành viên

**Mô tả:** Xem danh sách thành viên workspace, tìm kiếm theo tên/email, xem chi tiết một thành viên, xem các nhóm mà thành viên đó tham gia.

**Actor:** Người có quyền "Xem người dùng".

**Luồng chính:**

1. Xem danh sách thành viên (có phân trang, tìm kiếm theo tên/email).
2. Chọn một thành viên để xem chi tiết hồ sơ.
3. Xem danh sách nhóm mà thành viên đó đang tham gia trong workspace hiện tại.

**Kết quả đầu ra:** Thông tin thành viên hiển thị đầy đủ cho người có quyền xem.

---

### B3. Cập nhật thông tin & phân quyền thành viên

**Mô tả:** Chỉnh sửa hồ sơ, vai trò quyền hạn, đơn vị tổ chức, người quản lý, kỹ năng... của một thành viên đã có trong workspace.

**Actor:** Người có quyền "Sửa người dùng".

**Luồng chính:**

1. Mở hồ sơ thành viên, chỉnh các trường muốn sửa.
2. Nếu có thay đổi vai trò quyền hạn (roleIds) hoặc thu hồi một quyền cụ thể (deny) → hệ thống kiểm tra các quy tắc bên dưới trước khi lưu.
3. Lưu thay đổi.

**Luồng ngoại lệ:**

- Người thực hiện cố **tự sửa chính hồ sơ quyền hạn của mình** → bị từ chối tuyệt đối, kể cả khi có quyền "Sửa người dùng" — phải nhờ một quản trị viên khác thực hiện, để đảm bảo có người thứ hai chịu trách nhiệm cho thay đổi này.
- Vai trò quyền hạn mới cấp cho người khác vượt quá năng lực quyền hạn hiện có của người thực hiện → bị từ chối.
- Muốn **cấp thêm** một quyền đơn lẻ ngoài vai trò (không qua vai trò) → **không hỗ trợ** — chỉ được phép **thu hồi bớt** một quyền đơn lẻ (deny) khỏi những gì vai trò/nhóm đã cấp; muốn mở rộng quyền phải làm qua vai trò hoặc qua đường cấp quyền tạm thời (nhóm F).
- Đơn vị tổ chức mới gán không thuộc workspace hiện tại → từ chối.
- Chỉ được sửa vị trí/vai trò quyền hạn của thành viên trong **workspace đang thao tác** — không thể vô tình chỉnh sang workspace khác mà người đó cũng là thành viên.

**Quy tắc nghiệp vụ:**

- BR-1: Trường hồ sơ cấp cao (vai trò nền tảng SUPER_ADMIN, trạng thái kích hoạt/khoá tài khoản) **chỉ nhân sự vận hành nền tảng** mới sửa được, dù người thực hiện có quyền "Sửa người dùng" ở mức workspace.
- BR-2: Nếu thay đổi khiến thành viên bị khoá tài khoản, hoặc bị hạ từ vai trò vận hành nền tảng xuống người dùng thường → mọi phiên đăng nhập hiện tại của người đó bị đăng xuất ngay lập tức (không chờ hết hạn tự nhiên).

**Kết quả đầu ra:** Hồ sơ/quyền hạn thành viên được cập nhật; có ghi lại lịch sử ai đã thay đổi gì (xem nhóm K).

---

### B4. Đổi vai trò Quản trị viên ⇄ Thành viên

**Mô tả:** Nâng một Thành viên lên Quản trị viên, hoặc hạ một Quản trị viên xuống Thành viên — tách riêng khỏi chức năng B3 vì đây là thay đổi quyền hạn ở mức cao nhất (toàn quyền workspace).

**Actor:** Người có quyền "Quản lý vai trò".

**Luồng ngoại lệ:**

- Không thể tự đổi vai trò của chính mình.
- Không áp dụng được cho Chủ sở hữu — vai trò của Owner luôn là toàn quyền tuyệt đối, không phụ thuộc cờ Quản trị/Thành viên, nên thao tác này với Owner sẽ bị từ chối (không có tác dụng gì, tránh gây hiểu nhầm đã "hạ quyền" được Owner).

**Kết quả đầu ra:** Vai trò workspace của thành viên được cập nhật, ghi log, phiên đăng nhập không bị ảnh hưởng trừ khi kèm khoá tài khoản.

---

### B5. Gỡ / Xoá thành viên

**Mô tả:** Loại một người khỏi workspace hiện tại (vẫn còn tài khoản, có thể ở workspace khác), hoặc xoá hẳn tài khoản khỏi hệ thống.

**Actor:** Người có quyền "Xoá người dùng" (gỡ khỏi workspace); nhân sự vận hành nền tảng (xoá hẳn tài khoản).

**Luồng chính (Gỡ khỏi workspace):**

1. Chọn thành viên → gỡ khỏi workspace hiện tại.
2. Hệ thống tự động loại người này khỏi mọi nhóm trong workspace đó trước khi gỡ membership.
3. Phiên đăng nhập hiện tại của người đó bị đăng xuất ngay.

**Luồng ngoại lệ:**

- Không thể gỡ **Chủ sở hữu** khỏi workspace — phải chuyển nhượng quyền sở hữu trước (xem A9).
- Không thể xoá hẳn tài khoản của người đang là Chủ sở hữu của **bất kỳ** workspace nào — phải xử lý workspace đó trước.

**Kết quả đầu ra:** Người bị gỡ mất quyền truy cập workspace ngay lập tức.

---

### B6. Đặt lại mật khẩu & quản lý trạng thái tài khoản

**Mô tả:** Gửi email đặt lại mật khẩu cho một thành viên; khoá/mở khoá tài khoản.

**Actor:** Người có quyền "Quản lý vai trò" (đặt lại mật khẩu); nhân sự vận hành nền tảng (khoá/mở khoá).

**Luồng ngoại lệ:**

- Tài khoản không đăng nhập bằng email/mật khẩu (đăng nhập qua mạng xã hội) → không hỗ trợ đặt lại mật khẩu qua đường này.
- Khoá tài khoản → mọi phiên đăng nhập hiện tại bị đăng xuất ngay.

---

### B7. Xem quyền hiệu lực & xem trước thay đổi quyền

**Mô tả:** Cho phép quản trị viên xem "người này đang thực sự làm được gì" (kèm giải thích quyền đến từ đâu: vai trò trực tiếp, nhóm, hay bị thu hồi riêng), và xem trước "nếu đổi thế này thì họ sẽ làm được gì" **trước khi lưu**.

**Actor:** Người có quyền "Xem người dùng".

**Kết quả đầu ra:** Bảng quyền hiệu lực rõ ràng, giúp quản trị viên ra quyết định phân quyền chính xác mà không cần thử-sai trên tài khoản thật.

---

### B8. Tuỳ chỉnh cá nhân (ngôn ngữ, múi giờ)

**Mô tả:** Mỗi thành viên tự đặt ngôn ngữ/múi giờ riêng cho mình, ưu tiên hơn cấu hình mặc định của workspace (A7). Đặt lại về "theo mặc định workspace" bất kỳ lúc nào.

**Actor:** Chính thành viên đó (tự phục vụ, không cần ai cấp quyền).

---

### Vấn đề tồn đọng / cần quyết định — Nhóm B

- Một số thông báo lỗi hệ thống trả về dạng **mã kỹ thuật** thay vì câu tiếng Việt/tiếng Anh dễ hiểu cho người dùng cuối — nên rà soát lại để đồng bộ trải nghiệm thông báo lỗi.
- "Đặt lại mật khẩu"/"khoá-mở khoá tài khoản" **phụ thuộc hoàn toàn vào loại tài khoản** (email/mật khẩu) — tài khoản đăng nhập qua mạng xã hội sẽ luôn báo lỗi ở 2 chức năng này. Cần xác nhận đây là giới hạn chấp nhận được hay cần bổ sung phương án khác.

---

## C. NHÓM

### C1. Tạo & cấu hình nhóm (bao gồm phân cấp nhóm)

**Mô tả:** Tạo một nhóm cộng tác, có thể tổ chức nhóm theo dạng cha-con (một nhóm lớn chứa các nhóm nhỏ bên trong), và gán vai trò quyền hạn cho cả nhóm — mọi thành viên trong nhóm (và trong các nhóm con) sẽ được thừa hưởng quyền đó.

**Actor:** Người có quyền "Tạo nhóm"/"Sửa nhóm".

**Điều kiện tiên quyết:** Đang thao tác trong 1 workspace.

**Luồng chính:**

1. Nhập tên nhóm (duy nhất trong workspace), mô tả, màu hiển thị, chọn nhóm cha (nếu muốn xếp vào một nhóm lớn hơn), chọn vai trò quyền hạn sẽ gán cho nhóm, chọn sẵn thành viên ban đầu (nếu có).
2. Hệ thống kiểm tra: tên không trùng, nhóm cha (nếu có) tồn tại và không tạo ra vòng lặp phân cấp (một nhóm không thể là "con của chính con cháu nó").
3. Hệ thống kiểm tra người tạo có đủ thẩm quyền cấp những gì nhóm này (và toàn bộ nhóm cha phía trên) sẽ mang lại hay không (xem BR-2).
4. Lưu nhóm.

**Luồng ngoại lệ:**

- Tên nhóm trùng trong workspace → báo lỗi.
- Chọn nhóm cha không tồn tại, hoặc tạo vòng lặp phân cấp (nhóm A là cha của B, giờ lại chọn B làm cha của A) → từ chối, báo rõ lý do.
- Cách cấp quyền trực tiếp kiểu cũ (gõ tay từng khoá quyền cho nhóm) **không còn được hỗ trợ để tạo mới** — chỉ có thể gán quyền cho nhóm thông qua việc chọn (các) vai trò đã được định nghĩa sẵn (xem nhóm E). Các nhóm cũ đã từng dùng cách gõ tay từ trước vẫn được giữ nguyên, không bị ép chuyển đổi ngay.

**Quy tắc nghiệp vụ:**

- BR-1: Nhóm là công cụ **cộng tác/tổ chức công việc**, hoàn toàn tách biệt với sơ đồ tổ chức (nhóm D) — một người có thể ở nhiều nhóm cùng lúc, không liên quan gì tới việc họ thuộc đơn vị tổ chức nào.
- BR-2: Người tạo/sửa nhóm **chỉ được gán cho nhóm (và qua đó, cho mọi thành viên nhóm) những quyền mà chính người đó đang có** — tính gộp cả quyền của toàn bộ chuỗi nhóm cha phía trên, không chỉ riêng nhóm đang thao tác. Việc **đổi tên/mô tả/màu sắc** (không liên quan tới quyền/thành viên/phân cấp) thì **không** bị kiểm tra ràng buộc này.
- BR-3: Trước khi lưu, có thể **xem trước** quyền một cấu hình nhóm (kể cả với nhóm cha giả định, chưa lưu) sẽ mang lại — không bị ràng buộc bởi BR-2 vì xem trước không cấp gì thật.

**Kết quả đầu ra:** Nhóm được tạo/cập nhật; mọi thành viên trong nhóm (và nhóm con) được cập nhật quyền hiệu lực ngay.

---

### C2. Quản lý thành viên nhóm

**Mô tả:** Thêm/gỡ thành viên khỏi nhóm; xem danh sách thành viên nhóm.

**Actor:** Người có quyền "Quản lý thành viên nhóm".

**Luồng chính (Thêm thành viên):**

1. Chọn nhóm, chọn thành viên (phải đã là thành viên workspace).
2. Hệ thống kiểm tra người thực hiện có đủ thẩm quyền cấp những gì nhóm (và chuỗi nhóm cha) mang lại — vì thêm ai đó vào nhóm tức là cấp cho họ toàn bộ quyền của nhóm đó.
3. Thêm thành công (thêm lại một người đã có sẵn trong nhóm không gây lỗi, cũng không tạo trùng).

**Luồng chính (Gỡ thành viên):** Gỡ ngay lập tức, **không cần kiểm tra thẩm quyền** — vì gỡ khỏi nhóm chỉ có thể làm giảm quyền, không bao giờ là hành vi cần kiểm soát chống lạm quyền.

**Luồng ngoại lệ:**

- Người được thêm chưa phải thành viên workspace → từ chối, yêu cầu mời vào workspace trước.

**Kết quả đầu ra:** Danh sách thành viên nhóm cập nhật; quyền hiệu lực của người bị ảnh hưởng cập nhật gần như ngay lập tức.

---

### C3. Xem trước quyền của nhóm

Đã mô tả ở C1/BR-3 — liệt kê lại thành mục riêng vì đây là một thao tác độc lập, không cần lưu nhóm: người dùng có thể nhập thử vai trò/nhóm cha bất kỳ và xem ngay kết quả quyền sẽ ra sao.

---

### C4. Xoá nhóm

**Mô tả:** Xoá hẳn một nhóm khỏi workspace.

**Actor:** Người có quyền "Xoá nhóm".

🔧 **[Đã quyết định — chờ triển khai]** Luồng ngoại lệ đầu tiên dưới đây là quy tắc **đích đã chốt** (2026-08-21) — hệ thống hiện tại chưa chặn, xem [Phụ lục B](#phụ-lục-b--lộ-trình-triển-khai--implementation-roadmap).

**Luồng ngoại lệ:**

- Nhóm đang có nhóm con bên dưới → **từ chối xoá**, yêu cầu xoá/di chuyển nhóm con trước — đồng bộ với quy tắc đơn vị tổ chức đang áp dụng (D3), vì cascade quyền hạn từ nhóm cha xuống nhóm con khiến việc để "mồ côi" rủi ro cao hơn.
- Nhóm đang có **thành viên trực tiếp** → **KHÔNG bị chặn xoá** (khác với đơn vị tổ chức) — xoá được bình thường, thành viên chỉ đơn giản mất quyền do nhóm đó mang lại. Quyết định có chủ đích: Nhóm được thiết kế để linh hoạt (vd. nhóm xử lý 1 chiến dịch, xong việc thì xoá), bắt gỡ từng thành viên trước mới cho xoá sẽ tạo trải nghiệm nặng nề không cần thiết.

**Quy tắc nghiệp vụ:**

- BR-1: Quyền của mọi thành viên trong nhóm (và toàn bộ nhóm con bên dưới) được tính toán lại ngay sau khi xoá.

**Kết quả đầu ra:** Nhóm bị xoá (chỉ khi không còn nhóm con); các thành viên liên quan mất quyền do nhóm đó mang lại.

---

### Vấn đề tồn đọng / cần quyết định — Nhóm C

- **Sửa danh sách thành viên nhóm bằng cách sửa trực tiếp cả nhóm (thay vì dùng đúng chức năng "thêm thành viên")** sẽ bỏ qua một số bước kiểm tra (như xác nhận người đó còn thuộc workspace) mà chức năng "thêm thành viên" chuẩn có làm — cần rà soát để đảm bảo hai đường đi đến cùng kết quả an toàn như nhau.
- Chưa rõ khi xoá một nhóm, các quyền tạm thời (nhóm F) đang cấp riêng cho nhóm đó (nếu có) có được dọn theo hay không — cần xác nhận thêm.

---

## D. ĐƠN VỊ TỔ CHỨC

### D1. Xây dựng & quản lý cây tổ chức

**Mô tả:** Tạo và duy trì sơ đồ tổ chức (phòng ban, đội nhóm...) dạng cây, dùng làm căn cứ xác định "ai thuộc bộ phận nào" — từ đó hệ thống suy ra phạm vi dữ liệu mỗi người được thấy (nhóm G).

**Actor:** Người có quyền "Xem/Tạo/Sửa đơn vị tổ chức" — đây là quyền **riêng biệt, không dùng chung với cấu hình thông thường**, kể cả việc **xem** sơ đồ tổ chức cũng cần quyền riêng (sơ đồ tổ chức không phải thông tin công khai cho mọi thành viên trong workspace).

**Luồng chính:**

1. Xem cây tổ chức dạng cây lồng nhau (kèm số người thuộc mỗi đơn vị) hoặc dạng danh sách phẳng.
2. Tạo đơn vị mới: tên (duy nhất trong workspace), mã đơn vị (tuỳ chọn, duy nhất nếu có), đơn vị cha (bỏ trống = đơn vị gốc), người phụ trách chính, danh sách đồng phụ trách (nếu có).
3. Sửa thông tin đơn vị.

**Luồng ngoại lệ:**

- Tên trùng trong workspace → từ chối.
- Mã đơn vị trùng (khi có khai báo mã) → từ chối rõ ràng.
- Người phụ trách/đồng phụ trách phải là thành viên của **chính workspace này** → nếu không, từ chối (tránh sau này người ngoài workspace bị hiểu nhầm là có quyền quản lý một bộ phận).
- Cây tổ chức bị giới hạn **tối đa 10 cấp** — thao tác khiến vượt quá bị từ chối.

**Kết quả đầu ra:** Sơ đồ tổ chức phản ánh đúng cấu trúc công ty, sẵn sàng làm căn cứ phân quyền dữ liệu.

---

### D2. Di chuyển đơn vị tổ chức trong cây (đổi đơn vị cha)

**Mô tả:** Chuyển một đơn vị (cùng toàn bộ đơn vị con bên dưới nó) sang làm con của một đơn vị khác.

**Actor:** Người có quyền "Sửa đơn vị tổ chức".

**Luồng ngoại lệ:**

- Không thể tự làm cha của chính mình.
- Đơn vị cha đích không tồn tại → từ chối.
- **Không thể chuyển một đơn vị vào bên dưới chính hậu duệ của nó** (tránh tạo vòng lặp vô hạn trong cây) → từ chối rõ ràng.
- Việc di chuyển tính luôn cả "chiều cao" của toàn bộ nhánh đang di chuyển — nếu khiến cây vượt quá 10 cấp ở vị trí mới thì từ chối, dù bản thân đơn vị đó không sâu.

**Quy tắc nghiệp vụ:**

- BR-1: Chỉ khi đơn vị cha **thực sự thay đổi** thì hệ thống mới coi là "di chuyển" và tính lại toàn bộ nhánh bên dưới — đổi tên/mô tả mà giữ nguyên vị trí không kích hoạt việc này.

**Kết quả đầu ra:** Đơn vị (và mọi đơn vị con) chuyển sang vị trí mới trong cây; phạm vi dữ liệu liên quan tới đơn vị đó được tính lại.

---

### D3. Xoá đơn vị tổ chức

**Mô tả:** Xoá một đơn vị khỏi sơ đồ tổ chức.

**Actor:** Người có quyền "Xoá đơn vị tổ chức".

**Luồng ngoại lệ:**

- Đơn vị đang có đơn vị con bên dưới → **từ chối xoá**, yêu cầu di chuyển/xoá đơn vị con trước.
- Đơn vị đang có thành viên được gán trực tiếp → **từ chối xoá**, yêu cầu chuyển thành viên sang đơn vị khác trước.

**Quy tắc nghiệp vụ:**

- BR-1: Chủ đích **không cho xoá cưỡng bức kèm cascade** — buộc người quản trị phải xử lý tường minh từng trường hợp, để tránh vô tình làm mất quyền xem dữ liệu của một loạt người hoặc để lại bản ghi tham chiếu tới đơn vị không còn tồn tại.
- BR-2: Nếu (bằng cách nào đó, ví dụ dữ liệu cũ) một đơn vị có đơn vị cha đã không còn tồn tại, hệ thống hiển thị đơn vị đó như một **đơn vị gốc** thay vì ẩn đi — để người quản trị nhận biết và xử lý, thay vì âm thầm mất dữ liệu khỏi tầm nhìn.

**Kết quả đầu ra:** Đơn vị bị xoá, chỉ khi không còn ràng buộc con/thành viên.

---

### Vấn đề tồn đọng / cần quyết định — Nhóm D

- **Thay đổi sơ đồ tổ chức (tạo/sửa/di chuyển/xoá) có thể mất tới khoảng 1 phút để phản ánh đầy đủ vào phạm vi dữ liệu mà người dùng khác đang thấy**, do cơ chế lưu tạm để tăng tốc độ tải trang. Trong hầu hết trường hợp không đáng kể, nhưng cần lưu ý nếu có kịch bản nghiệp vụ yêu cầu thay đổi phải có hiệu lực tức thời (ví dụ: cách ly khẩn cấp một nhân sự khỏi 1 bộ phận).
- Trùng **tên** đơn vị hiện được hệ thống chặn nhưng thông báo lỗi trả về **chưa thân thiện bằng** trường hợp trùng **mã đơn vị** (vốn đã có thông báo rõ ràng) — nên đồng bộ trải nghiệm.
- Người phụ trách một đơn vị, nếu sau này rời khỏi workspace, **không tự động được gỡ khỏi vai trò phụ trách** — cần xác nhận đây có gây ảnh hưởng gì tới quyền xem dữ liệu hay không và có cần dọn tự động không.

---

## E. VAI TRÒ & QUYỀN

### E1. Xem danh mục/ma trận quyền của workspace

**Mô tả:** Xem toàn bộ danh mục quyền có thể gán trong workspace (nhóm theo từng loại nghiệp vụ: Khách hàng, Cơ hội, Ticket, Tự động hoá...), cùng với "trần quyền" hiện tại của workspace (những quyền mà gói dịch vụ đang dùng cho phép sử dụng).

**Actor:** Người có quyền "Quản lý cấu hình hệ thống" của workspace.

**Kết quả đầu ra:** Danh sách quyền rõ ràng để làm căn cứ xây dựng vai trò tuỳ chỉnh.

---

### E2. Tạo & sao chép vai trò

**Mô tả:** Tạo một vai trò tuỳ chỉnh mới (đặt tên, mô tả, chọn danh sách quyền, chọn phạm vi hiển thị dữ liệu mặc định đi kèm vai trò — xem nhóm G); hoặc sao chép nhanh một vai trò có sẵn (kể cả vai trò dựng sẵn của hệ thống) thành bản có thể tuỳ biến riêng.

**Actor:** Người có quyền "Quản lý cấu hình hệ thống".

**Luồng ngoại lệ:**

- Danh sách quyền chọn cho vai trò mới phải nằm trong quyền hiện có của chính người tạo — **không thể tạo ra một vai trò mạnh hơn năng lực của người tạo nó.**
- Phạm vi hiển thị dữ liệu chọn cho vai trò cũng bị ràng buộc tương tự: chỉ được chọn phạm vi rộng bằng hoặc hẹp hơn phạm vi mà chính người tạo đang có.
- **Sao chép** một vai trò có sẵn **không bị ràng buộc bởi 2 quy tắc trên** — vì bản sao chỉ lặp lại đúng những gì vai trò gốc (đã tồn tại hợp lệ trong workspace) đang có, không tạo ra năng lực mới.

**Kết quả đầu ra:** Vai trò mới sẵn sàng để gán cho thành viên/nhóm.

---

### E3. Cập nhật vai trò

**Mô tả:** Sửa tên/mô tả/danh sách quyền/phạm vi dữ liệu của một vai trò tự tạo.

**Actor:** Người có quyền "Quản lý cấu hình hệ thống".

**Luồng ngoại lệ:**

- **Vai trò dựng sẵn của hệ thống không sửa được trực tiếp** — muốn tuỳ biến phải sao chép ra bản riêng trước (xem E2).
- Chỉ **quyền/phạm vi mới thêm vào** mới bị kiểm tra ràng buộc năng lực người sửa (giống E2); việc **bớt đi** quyền/thu hẹp phạm vi luôn được phép tự do, không kiểm tra gì (vì đây là hành vi giảm quyền, không phải leo thang).

**Kết quả đầu ra:** Vai trò cập nhật; mọi thành viên/nhóm đang gán vai trò này được tính lại quyền ngay.

---

### E4. Lịch sử phiên bản & khôi phục vai trò

**Mô tả:** Mỗi lần sửa vai trò, hệ thống lưu lại một bản ghi lịch sử (không thể xoá/sửa lịch sử này). Có thể xem lại các phiên bản trước và khôi phục về một phiên bản cũ.

**Actor:** Người có quyền "Quản lý cấu hình hệ thống".

**Quy tắc nghiệp vụ:**

- BR-1: "Khôi phục" **không xoá lịch sử hiện tại** — nó tạo ra một bản ghi lịch sử **mới**, ghi rõ là khôi phục từ phiên bản nào, để toàn bộ dòng thời gian thay đổi luôn được giữ nguyên vẹn, có thể tra soát về sau.

**Kết quả đầu ra:** Có thể truy vết và hoàn tác thay đổi vai trò một cách an toàn, không mất dấu vết.

---

### E5. Xoá vai trò

🔧 **[Đã quyết định — chờ triển khai]** Toàn bộ luồng dưới đây là quy tắc **đích đã chốt** (2026-08-21). Hệ thống hiện tại **cho xoá tự do, không cảnh báo, không cascade** — xem [Phụ lục B](#phụ-lục-b--lộ-trình-triển-khai--implementation-roadmap).

**Mô tả:** Xoá một vai trò tự tạo khỏi workspace.

**Actor:** Người có quyền "Quản lý cấu hình hệ thống".

**Luồng chính:**

1. Người dùng bấm xoá một vai trò.
2. Hệ thống đếm số người dùng/nhóm đang gán vai trò này → nếu > 0, hiển thị cảnh báo mềm kèm con số cụ thể ("Vai trò này đang gán cho N người dùng, M nhóm — vẫn xoá?").
3. Người dùng xác nhận vẫn muốn xoá → thao tác xoá thực sự diễn ra.

**Luồng ngoại lệ:**

- Vai trò dựng sẵn của hệ thống **không xoá được**.
- Nếu vai trò đang được một hoặc nhiều **quyền tạm thời** (nhóm F) tham chiếu → toàn bộ các quyền tạm thời đó **tự động bị thu hồi ngay khi xoá** (không để lại quyền "treo" trỏ tới vai trò không còn tồn tại).
- Bất kỳ ai đang có phiên đăng nhập dựa trên quyền vừa bị thu hồi theo cách này → **bị đăng xuất ngay lập tức** (áp dụng đúng nguyên tắc đã có sẵn cho việc khoá tài khoản/hạ quyền trực tiếp — xem B3/BR-2): khoảng trống giữa "quyền đã mất trên hệ thống" và "phiên đăng nhập tưởng vẫn còn quyền" không được để tồn tại, kể cả với quyền vốn chỉ mang tính tạm thời.

**Quy tắc nghiệp vụ:**

- BR-1: Cảnh báo chỉ là **bước xác nhận thêm**, không phải rào cản cứng — không cần gõ lại tên vai trò hay bất kỳ xác nhận nào nặng nề hơn checkbox.
- BR-2: Xoá vai trò **không được để lại quyền tạm thời mồ côi** — nguyên tắc chung: nếu gốc (vai trò) bị xoá, mọi thứ mọc ra từ gốc đó (quyền tạm thời tham chiếu tới nó) phải bị dọn ngay, không chờ báo cáo kiểm định (F4) phát hiện sau.

**Kết quả đầu ra:** Vai trò bị xoá; mọi quyền tạm thời tham chiếu bị thu hồi; phiên đăng nhập bị ảnh hưởng bị đăng xuất ngay.

---

### E6. Vai trò dựng sẵn của hệ thống

**Mô tả:** Mỗi workspace mới đều tự động có sẵn một bộ vai trò mẫu phổ biến, không cần Owner tự cấu hình từ đầu:

| Vai trò | Phạm vi dữ liệu đi kèm | Mô tả ngắn |
| --- | --- | --- |
| Quản lý (Manager) | Đơn vị + toàn bộ nhánh bên dưới | Toàn quyền thao tác trên hầu hết nghiệp vụ |
| Nhân viên Kinh doanh | Đơn vị của mình | Quản lý khách hàng/cơ hội, không xoá được |
| Nhân viên Hỗ trợ | Đơn vị của mình | Xử lý ticket, trả lời hội thoại đa kênh |
| Chỉ xem (Read Only) | Đơn vị của mình | Xem hầu hết mọi thứ, không sửa/xoá — **là vai trò mặc định khi mời thành viên không chọn vai trò nào** |
| Kiểm toán (Auditor) | Toàn workspace | Chỉ xem, không có quyền ghi nào — cần bật tính năng mở rộng tương ứng |
| Marketing | Đơn vị của mình | Quản lý chiến dịch, thư viện nội dung — cần bật tính năng mở rộng tương ứng |

**Quy tắc nghiệp vụ:**

- BR-1: Các vai trò này được đồng bộ tự động theo thời gian — nếu sau này hệ thống bổ sung quyền mới phù hợp với một vai trò dựng sẵn, workspace sẽ tự nhận cập nhật đó mà không cần thao tác gì.
- BR-2: **Tên hiển thị của vai trò dựng sẵn không bao giờ bị hệ thống tự đổi lại**, kể cả khi đồng bộ cập nhật quyền — vì quản trị viên có thể đã quen gọi theo tên đó trong nội bộ.
- BR-3: "Quản trị viên" (Owner/Admin) **không phải** một vai trò trong danh sách này — đó là một cờ đặc biệt (toàn quyền tuyệt đối), luôn hiển thị cho người dùng biết nhưng không tồn tại như một bản ghi vai trò có thể sửa/xoá.

---

### Vấn đề tồn đọng / cần quyết định — Nhóm E

- Có 4 loại báo cáo (nhóm E1–E6) dùng chung một khoá quyền cấu hình hệ thống khá rộng ("Quản lý cấu hình hệ thống") — bao gồm cả việc quản lý vai trò, chính sách truy cập nâng cao (nhóm H), quyền trên bản ghi (nhóm I), cấp quyền tạm thời (nhóm F). Cần đánh giá có nên tách nhỏ quyền hơn để phân công rõ ràng ai được làm phần nào.

---

## F. CẤP QUYỀN TẠM THỜI CÓ PHÊ DUYỆT

### F1. Yêu cầu cấp quyền tạm thời

**Mô tả:** Cho phép cấp thêm một vai trò cho một thành viên (hoặc cả một nhóm) trong **một khoảng thời gian giới hạn**, dùng cho các tình huống cần quyền cao hơn bình thường tạm thời (xử lý sự cố, hỗ trợ đột xuất...) mà không muốn cấp vĩnh viễn.

**Actor:** Người có quyền cấp yêu cầu quyền tạm thời.

**Luồng chính:**

1. Chọn người/nhóm nhận quyền, chọn vai trò sẽ cấp thêm, nhập lý do, chọn thời hạn hiệu lực.
2. Yêu cầu được ghi nhận ở trạng thái **"Chờ phê duyệt"** — **chưa có tác dụng gì** cho tới khi được duyệt đủ.

**Luồng ngoại lệ:**

- **Không thể tự yêu cầu cấp quyền cho chính mình.**
- Người/nhóm nhận phải thuộc chính workspace đang thao tác.
- Vai trò yêu cầu cấp phải nằm trong năng lực quyền hạn hiện có của người yêu cầu (không thể nhờ cấp hộ quyền mà bản thân không có).
- **Bắt buộc phải có thời hạn** — tối đa 90 ngày; không có lựa chọn "cấp vĩnh viễn" qua đường này.
- **Bắt buộc phải nhập lý do.**

**Quy tắc nghiệp vụ:**

- BR-1: Mọi cấp quyền tạm thời đều phải có hạn và có lý do — đây là cơ chế "cấp có kiểm soát", không phải đường tắt để cấp quyền vĩnh viễn nhanh hơn quy trình thường.

**Kết quả đầu ra:** Yêu cầu được ghi nhận, chờ phê duyệt.

---

### F2. Phê duyệt / từ chối yêu cầu

**Mô tả:** Một hoặc nhiều người có thẩm quyền xem xét và quyết định yêu cầu cấp quyền tạm thời.

**Actor:** Người có quyền phê duyệt.

**Luồng chính:**

1. Xem yêu cầu đang chờ.
2. Phê duyệt → yêu cầu quyền hiệu lực ngay sau khi có **đủ 2 lượt phê duyệt độc lập** (không phải cùng một người duyệt 2 lần).
3. Hoặc từ chối → yêu cầu kết thúc, không có hiệu lực.

**Luồng ngoại lệ:**

- Người phê duyệt **không được** là người đã tạo yêu cầu, và **không được** là chính người/thuộc nhóm sẽ nhận quyền — tránh tự phê duyệt cho chính mình dưới mọi hình thức.
- Một người không được phê duyệt 2 lần cho cùng một yêu cầu.
- Tại thời điểm phê duyệt, hệ thống **kiểm tra lại** người phê duyệt có còn đủ năng lực quyền hạn để "bảo chứng" cho vai trò sắp cấp hay không — nếu người phê duyệt đã bị mất quyền đó từ sau lúc yêu cầu được tạo, việc phê duyệt của họ bị từ chối.
- Người tạo yêu cầu ban đầu không được tự từ chối yêu cầu của chính mình (việc từ chối phải đến từ người khác, để quyết định luôn có ít nhất 2 người liên quan).

**Quy tắc nghiệp vụ:**

- BR-1: **Cần đủ 2 phê duyệt độc lập** thì quyền mới thực sự có hiệu lực — 1 phê duyệt chưa đủ, yêu cầu vẫn ở trạng thái chờ.

**Kết quả đầu ra:** Quyền tạm thời có hiệu lực ngay khi đủ phê duyệt, tự động hết hạn theo thời gian đã đặt.

---

### F3. Thu hồi quyền đã cấp

**Mô tả:** Chấm dứt sớm một quyền tạm thời đang có hiệu lực, trước khi tới hạn tự nhiên.

**Actor:** Người có quyền phê duyệt/quản trị liên quan.

🔧 **[Đã quyết định — chờ triển khai]** Xem [Phụ lục B](#phụ-lục-b--lộ-trình-triển-khai--implementation-roadmap).

**Quy tắc nghiệp vụ:**

- BR-1: Người vừa bị thu hồi quyền theo cách này **bị đăng xuất phiên đăng nhập hiện tại ngay lập tức**, không chờ hết hạn tự nhiên — cùng nguyên tắc real-time revocation áp dụng nhất quán ở mọi nơi trong hệ thống (B3, E5): khoảng cách giữa "quyền thực tế đã mất" và "phiên đăng nhập tưởng vẫn còn quyền" là cửa sổ rủi ro không được chấp nhận, kể cả khi quyền bị thu hồi vốn chỉ là quyền tạm thời.

**Kết quả đầu ra:** Quyền mất hiệu lực ngay; bản ghi được đánh dấu đã thu hồi (không xoá khỏi lịch sử, phục vụ tra soát về sau); phiên đăng nhập liên quan bị đăng xuất ngay.

---

### F4. Báo cáo kiểm định quyền

**Mô tả:** Báo cáo định kỳ giúp quản trị viên rà soát toàn bộ tình trạng cấp quyền tạm thời trong workspace: những yêu cầu đang chờ phê duyệt quá lâu, những quyền đã hết hạn nhưng chưa được đánh dấu thu hồi, những quyền "vĩnh viễn kiểu cũ" còn sót lại từ trước khi có quy trình phê duyệt này, và những trường hợp vai trò được cấp đã bị xoá (không còn tồn tại) mà bản ghi cấp quyền vẫn còn.

**Actor:** Người có quyền quản trị liên quan.

**Kết quả đầu ra:** Danh sách các trường hợp cần rà soát/dọn dẹp thủ công.

---

### Vấn đề tồn đọng / cần quyết định — Nhóm F

- Việc yêu cầu quyền tạm thời hiện **không tự động thông báo** cho người phê duyệt phù hợp — cần xác nhận có kênh thông báo (email/trong ứng dụng) đi kèm hay quy trình đang phụ thuộc vào việc người phê duyệt tự vào kiểm tra danh sách chờ.
- **Có tồn tại một số bản ghi cấp quyền "vĩnh viễn kiểu cũ"** (không hạn, không qua quy trình phê duyệt) từ giai đoạn trước khi cơ chế phê duyệt này được áp dụng — báo cáo kiểm định (F4) chỉ **liệt kê**, không tự động xử lý. Cần một đợt rà soát thủ công để xác nhận từng trường hợp còn cần thiết hay nên thu hồi.

---

## G. PHẠM VI HIỂN THỊ DỮ LIỆU

### G1. Cấu hình phạm vi hiển thị dữ liệu

**Mô tả:** Owner/Admin cấu hình, cho toàn workspace hoặc riêng theo từng loại dữ liệu (Khách hàng, Công ty, Cơ hội, Ticket, Công việc, Hội thoại), mức độ dữ liệu mặc định mỗi vai trò được thấy.

**Actor:** Owner/Admin.

**Các mức phạm vi (từ hẹp đến rộng):**

1. **Chỉ của mình** — chỉ thấy bản ghi do chính mình phụ trách.
2. **Của mình + cấp dưới / đơn vị của mình** — thấy thêm bản ghi của những người báo cáo trực tiếp/gián tiếp cho mình, hoặc của người cùng đơn vị tổ chức.
3. **Cả nhánh đơn vị** — thấy thêm bản ghi của mọi đơn vị con bên dưới đơn vị của mình.
4. **Toàn workspace** — thấy tất cả, không giới hạn.

**Quy tắc nghiệp vụ:**

- BR-1: Cấu hình mặc định của workspace chỉ là **"lưới an toàn dự phòng"** — nếu vai trò của một người đã khai báo rõ phạm vi riêng, phạm vi đó luôn được ưu tiên áp dụng, cấu hình mặc định workspace không được phép **thu hẹp lại** phạm vi mà vai trò đã khai báo rõ ràng, mà cũng không âm thầm mở rộng thêm.
- BR-2: Có thể cấu hình riêng theo từng bộ phận dữ liệu — ví dụ để Ticket là "toàn workspace" (ai cũng xem được để hỗ trợ nhau) trong khi Cơ hội kinh doanh vẫn giữ "chỉ của mình + cấp dưới" (bảo mật thông tin kinh doanh).
- BR-3: Có thể bật thêm lựa chọn "người phụ trách một đơn vị tổ chức được xem toàn bộ dữ liệu thuộc đơn vị đó và mọi đơn vị con", **độc lập** với phạm vi theo vai trò — đây là quyền lợi đi kèm chức vụ, không phải quyền theo vai trò.

**Kết quả đầu ra:** Phạm vi dữ liệu áp dụng ngay cho các lượt xem danh sách tiếp theo (có độ trễ tối đa khoảng 1 phút do cơ chế lưu tạm, xem lưu ý ở nhóm D).

---

### G2. Quy tắc phân giải phạm vi theo cấp quản lý & đơn vị tổ chức (tự động)

**Mô tả:** Đây không phải một thao tác người dùng bấm, mà là **quy tắc nền tảng** hệ thống luôn áp dụng mỗi khi ai đó mở một danh sách dữ liệu, để quyết định họ thực sự thấy những bản ghi nào.

**Quy tắc nghiệp vụ (áp dụng tự động):**

- BR-1: Giữ đồng thời nhiều vai trò/nhóm khác nhau **không bao giờ làm thu hẹp** phạm vi nhìn thấy so với chỉ giữ vai trò rộng nhất trong số đó — luôn lấy phạm vi **rộng nhất**. Muốn hạn chế thêm cho một trường hợp cụ thể phải dùng Chính sách truy cập nâng cao (nhóm H) để chặn riêng, không phải bằng cách phối hợp vai trò.
- BR-2: Bất kỳ phạm vi nào **rộng hơn "chỉ của mình"** đều tự động bao gồm toàn bộ chuỗi cấp dưới trực tiếp và gián tiếp (không chỉ báo cáo trực tiếp) — vì một người quản lý bình thường cũng có cấp dưới trực tiếp, nếu bỏ sót sẽ khiến phạm vi "rộng hơn" lại vô lý hiển thị **ít hơn** phạm vi hẹp hơn.
- BR-3: Phạm vi "cả nhánh đơn vị" chỉ bao gồm đơn vị của mình và **các đơn vị con bên dưới** — **không bao gồm** đơn vị anh em ngang hàng hay đơn vị cha phía trên.
- BR-4: Một thành viên **chưa được gán đơn vị tổ chức nào** sẽ không được xem thêm bất kỳ dữ liệu nào theo trục đơn vị tổ chức, ở bất kỳ mức phạm vi nào — không "rơi xuống" chế độ thấy tất cả.
- BR-5: Nếu một đơn vị tổ chức bị xoá ngay trong lúc đang tính toán phạm vi cho một người, hệ thống sẽ **thu hẹp** (bỏ đơn vị đó ra), không bao giờ mở rộng bất ngờ.
- BR-6: Bất kỳ lỗi bất thường nào trong quá trình tính toán phạm vi đều khiến hệ thống **từ chối hiển thị** (đóng hết, an toàn trước) thay vì lỡ hiển thị nhầm dữ liệu không thuộc phạm vi.

---

### Vấn đề tồn đọng / cần quyết định — Nhóm G

- Xem ghi chú ở nhóm D: thay đổi sơ đồ tổ chức có độ trễ tối đa ~1 phút mới phản ánh vào phạm vi hiển thị — với nhóm G thì cấu hình phạm vi/gán vai trò cho người dùng **được phản ánh gần như ngay lập tức** (không có độ trễ này), chỉ riêng thay đổi ở sơ đồ tổ chức (nhóm D) mới có độ trễ. Cần quyết định độ trễ này có chấp nhận được cho mọi kịch bản nghiệp vụ hay không.
- Trục "phạm vi theo cấp dưới" hiện tính bằng cách tải toàn bộ danh sách nhân sự của workspace mỗi lần — về nghiệp vụ không ảnh hưởng, nhưng cần lưu ý nếu workspace phát triển tới quy mô rất lớn (hàng chục nghìn người) có thể cần tối ưu thêm.

---

## H. CHÍNH SÁCH TRUY CẬP NÂNG CAO

### H1. Tạo & quản lý chính sách truy cập theo điều kiện

**Mô tả:** Bổ sung các luật chi tiết hơn những gì vai trò/nhóm/phạm vi dữ liệu có thể diễn đạt được — ví dụ: "chỉ được sửa hợp đồng khi hợp đồng đang ở trạng thái nháp", "chỉ được xuất báo cáo trong giờ hành chính". Áp dụng cho một loại nghiệp vụ + một hành động cụ thể.

**Actor:** Người có quyền "Quản lý cấu hình hệ thống".

**Luồng chính:**

1. Chọn loại dữ liệu + hành động chính sách này áp dụng (hoặc chọn "áp dụng cho mọi loại/mọi hành động").
2. Chọn hiệu lực: **Cho phép** hoặc **Từ chối**.
3. Thêm một hoặc nhiều điều kiện (so sánh một thuộc tính của người dùng, của bản ghi, hoặc của thời điểm hiện tại với một giá trị) — tất cả điều kiện trong cùng 1 chính sách phải **đồng thời đúng** thì chính sách mới áp dụng.
4. Đặt thứ tự ưu tiên (ít quan trọng vì luật "Từ chối" luôn thắng, thứ tự chủ yếu để dễ quản lý hiển thị).

**Luồng ngoại lệ:**

- Một điều kiện không thể tự đánh giá được (do dữ liệu không hợp lệ hoặc thiếu) → nếu chính sách đó là loại **Từ chối**, hệ thống coi như **vẫn áp dụng** (an toàn, giữ nguyên việc chặn); nếu là loại **Cho phép**, hệ thống coi như **không áp dụng** (không lỡ mở quyền khi không chắc chắn).

**Quy tắc nghiệp vụ:**

- BR-1: Chính sách loại **"Từ chối" luôn thắng** nếu có nhiều chính sách cùng áp dụng, bất kể có bao nhiêu chính sách "Cho phép" khác.
- BR-2: Chính sách chỉ có thể **thu hẹp thêm hoặc mở thêm trong phạm vi đã có** từ vai trò/nhóm/phạm vi dữ liệu — **không thể** dùng chính sách để tự cấp một quyền hoàn toàn không có trong vai trò.
- BR-3 — **Quyết định (2026-08-21), giữ nguyên giới hạn có chủ đích:** một điều kiện dạng "chứa đoạn văn bản" (ví dụ: email chứa "@congty.com") chỉ có tác dụng đầy đủ khi xem **chi tiết từng bản ghi**; ở **màn hình danh sách/tổng hợp**, loại điều kiện này **không lọc được**. 🔧 **[Đã quyết định — chờ triển khai]**: màn hình tạo/sửa chính sách **phải hiển thị cảnh báo tường minh** ngay khi người dùng chọn toán tử này ("điều kiện này chỉ bảo vệ trang chi tiết, không lọc được màn hình danh sách") — xem [Phụ lục B](#phụ-lục-b--lộ-trình-triển-khai--implementation-roadmap). Quyết định không đầu tư làm cho toán tử này lọc được cả danh sách (chi phí kỹ thuật không tương xứng ở giai đoạn hiện tại).
- BR-4 — 🔧 **[Đã quyết định — chờ triển khai]**: một điều kiện dạng "so sánh 2 thuộc tính của bản ghi" (ví dụ so sánh 2 trường trên cùng 1 bản ghi) **chỉ được phép lưu** nếu chính sách đó áp dụng cho một hành động **không có màn hình danh sách** (theo quy ước cố định: `view`, `export`, `import` = có màn hình danh sách; `create`, `edit`, `delete`, `resolve`, `reply`, `assign`, `manage_members`, `manage_roles`... = chỉ có thao tác/chi tiết đơn lẻ). Lưu chính sách loại này cho một hành động "có danh sách" phải bị **từ chối ngay lúc lưu** (không phải lúc chính sách được áp dụng thật) — quy ước action nào thuộc nhóm nào cần được định nghĩa tường minh và duy trì nhất quán khi có resource/action mới.

**Kết quả đầu ra:** Chính sách có hiệu lực gần như ngay lập tức cho mọi request liên quan.

---

### H2. Mô phỏng & kiểm thử chính sách

**Mô tả:** Cho phép thử một chính sách (kể cả chưa lưu) với một tình huống giả định (một người dùng giả định, một bản ghi giả định) để xem chính sách có áp dụng đúng như mong đợi không, trước khi đưa vào sử dụng thật.

**Actor:** Người có quyền "Quản lý cấu hình hệ thống".

**Kết quả đầu ra:** Kết quả mô phỏng (áp dụng hay không, hiệu lực gì) — không ảnh hưởng gì tới dữ liệu thật.

---

### H3. Lịch sử phiên bản & khôi phục chính sách

**Mô tả:** Tương tự E4 — mọi thay đổi chính sách được lưu vết, có thể khôi phục về phiên bản cũ (dưới dạng phiên bản mới, giữ nguyên lịch sử).

---

### Vấn đề tồn đọng / cần quyết định — Nhóm H

*(Không còn mục nào — 2 vấn đề trước đó đã được chốt phương án, xem BR-3/BR-4 ở H1 và [Phụ lục B](#phụ-lục-b--lộ-trình-triển-khai--implementation-roadmap).)*

---

## I. PHÂN QUYỀN THEO BẢN GHI

### I1. Cấp / thu hồi / xem quyền trên 1 bản ghi cụ thể

**Mô tả:** Cấp hoặc chặn quyền thao tác (xem/sửa...) cho **một bản ghi cụ thể** (một khách hàng cụ thể, một hợp đồng cụ thể) tới một người hoặc một nhóm cụ thể — dùng cho các trường hợp đặc biệt mà quy tắc chung theo vai trò/phạm vi không đáp ứng đủ (ví dụ: khách hàng VIP chỉ 2 người được động vào, bất kể họ thuộc vai trò/đơn vị nào).

**Actor:** Người có quyền "Quản lý cấu hình hệ thống".

**Luồng chính:**

1. Mở 1 bản ghi cụ thể → thêm một quyền cấp/chặn cho 1 người hoặc 1 nhóm.
2. Xem danh sách các quyền đã cấp/chặn trên bản ghi đó.
3. Gỡ một quyền đã cấp, hoặc gỡ toàn bộ.

**Quy tắc nghiệp vụ:**

- BR-1: Quyền **chặn tường minh trên bản ghi luôn thắng tuyệt đối** — dù vai trò/phạm vi dữ liệu thông thường có cho phép, bản ghi này vẫn bị chặn với người/nhóm bị chặn riêng.
- BR-2: Nếu không có cấp/chặn riêng nào cho bản ghi → áp dụng đúng theo quy tắc chung (vai trò/phạm vi/chính sách) như bình thường, không bị ảnh hưởng gì.
- BR-3: **Cấp/chặn riêng cho 1 bản ghi chỉ chắc chắn có tác dụng khi xem đúng bản ghi đó** — việc áp dụng ở màn hình danh sách/xuất báo cáo cần được từng nơi chủ động xử lý riêng, chưa phải là hành vi mặc định áp dụng ở mọi màn hình danh sách trong hệ thống (xem "Vấn đề tồn đọng").

**Kết quả đầu ra:** Bản ghi được bảo vệ/mở thêm đúng như cấu hình, độc lập với vai trò chung.

---

### Vấn đề tồn đọng / cần quyết định — Nhóm I

- Cần rà soát **từng màn hình danh sách/xuất báo cáo** trong hệ thống để xác nhận có thực sự loại trừ đúng những bản ghi đã bị chặn riêng (I1/BR-3) hay không — hiện chưa có khẳng định điều này đã được áp dụng đồng bộ ở mọi nơi.
- Không có lịch sử phiên bản cho quyền trên bản ghi (khác với vai trò/chính sách ở nhóm E/H) — muốn biết "trước đây bản ghi này từng cấp/chặn cho ai" chỉ tra được qua nhật ký thay đổi quyền (nhóm K), không có màn hình lịch sử riêng, trực quan như E4/H3.

---

## J. CHE DỮ LIỆU NHẠY CẢM

### J1. Ẩn/hiện dữ liệu nhạy cảm theo quyền

**Mô tả:** Một số trường thông tin nhạy cảm mặc định được **che bớt** trên giao diện, chỉ hiện đầy đủ cho người có quyền "xem đầy đủ" riêng cho từng loại dữ liệu đó. Có **2 cơ chế song song**, dùng cho 2 loại field khác nhau:

- **System PII** — field nhạy cảm do hệ thống định nghĩa sẵn (không tenant nào tự thêm/bớt được): email, số điện thoại, địa chỉ, ngày sinh của Khách hàng; giá trị hợp đồng và xác suất chốt của Cơ hội kinh doanh; email/số điện thoại khách hàng trong Hội thoại đa kênh; email, số điện thoại, mã số thuế, địa chỉ giao/nhận hàng của Công ty (Account) 🆕. Che theo một quyền "xem đầy đủ" riêng cho từng loại dữ liệu — cơ chế bảo mật chặt (Hard-coded Core Security).
- **Custom PII** — field nhạy cảm nằm trên **Trường tuỳ chỉnh (Custom Field) do chính workspace tự khai báo** (ví dụ: tự thêm 1 trường kiểu "Email" trên Khách hàng). Che theo cấu hình layout/nhóm người dùng do Admin của workspace tự chỉnh (Soft Security) — **không có quyền "xem đầy đủ" chuyên biệt** như System PII.

**Actor:** Mọi người dùng xem dữ liệu (bị ảnh hưởng); người có quyền "xem đầy đủ" tương ứng (không bị che, chỉ áp dụng cho System PII).

**Cách che áp dụng theo loại dữ liệu (System PII):**

- Email → giữ ký tự đầu và tên miền, che phần còn lại.
- Số điện thoại → giữ 4 số cuối.
- Giá trị hợp đồng/xác suất chốt, mã số thuế → ẩn hoàn toàn (không hiển thị số/giá trị).
- Các trường còn lại → thay toàn bộ bằng dấu che.

**Quy tắc nghiệp vụ:**

- BR-1: **Tác nhân là "trợ lý AI" luôn bị che tuyệt đối, không có ngoại lệ, không có quyền "xem đầy đủ" nào áp dụng được cho tác nhân AI** — đây là giới hạn cứng, chủ đích, không cấu hình được. 🔧 **[Đã quyết định — chờ triển khai]**: quy tắc này hiện chỉ được áp dụng cho System PII — quyết định (2026-08-21) bắt buộc **áp dụng thêm cho cả Custom PII**, dù cơ chế Custom PII nói chung được chấp nhận ở mức bảo mật thấp hơn (xem BR-3). Xem [Phụ lục B](#phụ-lục-b--lộ-trình-triển-khai--implementation-roadmap).
- BR-2: Việc che chỉ áp dụng trên **những gì hiển thị ra**, không thay đổi dữ liệu gốc được lưu trữ.
- BR-3 — **Quyết định (2026-08-21):** mức bảo vệ thấp hơn của Custom PII (không quyền "xem đầy đủ" riêng, chỉ dựa vào cấu hình layout) được **chấp nhận là đủ dùng**, vì đây là dữ liệu do chính workspace tự định nghĩa/tự chịu trách nhiệm cấu hình — khác với System PII là dữ liệu lõi hệ thống bảo vệ sẵn cho mọi workspace. Không đầu tư thiết kế lại Custom PII để đạt cùng mức đảm bảo như System PII ở giai đoạn này.
- BR-4: Doanh thu hàng năm và số lượng nhân sự của Công ty (Account) **không** thuộc diện che — đây là dữ liệu phân loại/đánh giá khách hàng (không phải thông tin định danh cá nhân), Sales cần thấy để ưu tiên xử lý.

**Kết quả đầu ra:** Người không có quyền "xem đầy đủ" chỉ thấy System PII đã được che một phần; Custom PII được che theo cấu hình layout của workspace.

---

### Vấn đề tồn đọng / cần quyết định — Nhóm J

- Ngoài Khách hàng, Cơ hội kinh doanh, Hội thoại đa kênh, Công ty (4 nhóm đã có System PII), còn loại dữ liệu nhạy cảm nào khác (ví dụ danh sách khách hàng tiềm năng — **đã xác nhận dùng chung cơ chế với Khách hàng, không cần bổ sung riêng**) cần rà soát thêm hay không.
- Cơ chế System PII từng bị lỗi "âm thầm mất tác dụng" trong quá khứ (do khai báo cấu hình không khớp đúng tên trường dữ liệu thực tế) — hiện chỉ có 1/4 nhóm dữ liệu (Khách hàng) có cơ chế kiểm tra tự động để chống lặp lại lỗi này. Nên nhân rộng cơ chế kiểm tra cho các nhóm còn lại, đặc biệt là Công ty (Account) vừa được bổ sung.

---

## K. NHẬT KÝ THAY ĐỔI QUYỀN

### K1. Tra cứu lịch sử thay đổi quyền/vai trò

**Mô tả:** Xem lại toàn bộ lịch sử các thay đổi liên quan tới quyền hạn trong workspace: ai đổi vai trò/quyền của ai, khi nào, giá trị trước/sau; ai tạo/sửa/xoá vai trò, nhóm, chính sách truy cập, quyền trên bản ghi; lịch sử yêu cầu-phê duyệt-thu hồi quyền tạm thời; và mọi quyết định cho phép/từ chối truy cập mà hệ thống đã đưa ra.

**Actor:** Người có quyền "Xem nhật ký quyền" (quyền riêng, tách biệt với quyền quản lý cấu hình khác).

**Luồng chính:**

1. Lọc theo loại thay đổi, loại đối tượng bị ảnh hưởng, khoảng thời gian.
2. Xem chi tiết từng bản ghi: ai thực hiện, thực hiện gì, giá trị trước/sau.

**Quy tắc nghiệp vụ:**

- BR-1: Đây là **nhật ký chỉ ghi, không sửa/xoá được** — kể cả quản trị viên cấp cao nhất cũng không chỉnh sửa lại lịch sử này.
- BR-2: Việc ghi nhật ký **không bao giờ được làm gián đoạn hay chặn** thao tác nghiệp vụ đang thực hiện — nếu việc ghi nhật ký gặp sự cố, thao tác chính vẫn diễn ra bình thường, chỉ có dòng nhật ký đó bị bỏ lỡ.
- BR-3: Đây là **nhật ký quyền hạn**, khác với nhật ký hoạt động nghiệp vụ thông thường (ví dụ: "khách hàng X vừa được tạo", "ticket Y vừa đóng") — hai loại nhật ký này tách biệt nhau, phục vụ mục đích khác nhau (nhật ký quyền phục vụ kiểm soát bảo mật/tuân thủ; nhật ký hoạt động phục vụ theo dõi công việc). Cùng một hành động (ví dụ mời một thành viên mới) có thể xuất hiện ở **cả hai** nơi.
- BR-4 — **Chính sách lưu trữ (Quyết định 2026-08-21)** 🔧 **[Đã quyết định — chờ triển khai]**: nhật ký được phân theo 2 nhóm lưu trữ khác nhau (xem [Phụ lục B](#phụ-lục-b--lộ-trình-triển-khai--implementation-roadmap)):
  - **Nhật ký quyết định cho phép/từ chối truy cập** (khối lượng lớn nhất, phát sinh theo mọi request) — lưu **60 ngày**, tự động xoá sau đó.
  - **Nhật ký thay đổi cấu hình quyền** (đổi vai trò/quyền/nhóm/chính sách/cấp quyền tạm thời/quyền trên bản ghi/vai trò nền tảng) — lưu **cố định 2 năm cho mọi gói dịch vụ** (chưa phân biệt theo gói — xem lưu ý ở Phụ lục B về việc không xây tính năng cấu hình theo gói ở giai đoạn này).

**Kết quả đầu ra:** Có thể tra soát đầy đủ "ai đã thay đổi quyền hạn gì, khi nào" phục vụ kiểm toán bảo mật, trong đúng thời hạn lưu trữ đã quy định.

---

### Vấn đề tồn đọng / cần quyết định — Nhóm K

- Việc ghi "quyết định cho phép/từ chối truy cập" là **tuỳ theo cấu hình triển khai**, về lý thuyết có thể bị tắt mà không có cảnh báo — nếu có yêu cầu bắt buộc phải ghi loại nhật ký này (ví dụ vì lý do tuân thủ), cần một cơ chế xác nhận việc ghi nhật ký đang thực sự hoạt động, không chỉ giả định là luôn bật.
- Bản ghi nhật ký có thể bị **bỏ sót** trong một số trường hợp hiếm (ví dụ hệ thống không xác định được workspace đang thao tác tại thời điểm ghi) — không có cơ chế bù đắp lại các bản ghi bị bỏ sót này.

---

## L. Yêu cầu phi chức năng chung

| # | Yêu cầu |
| --- | --- |
| NFR-1 | Khi hệ thống gặp lỗi không lường trước trong lúc tính toán phạm vi dữ liệu người dùng được xem, hệ thống PHẢI từ chối hiển thị (an toàn trước) thay vì lỡ hiển thị dữ liệu ngoài phạm vi. |
| NFR-2 | Sự cố ở các cơ chế lưu tạm (cache) tăng tốc độ tải trang KHÔNG được phép ảnh hưởng tới độ chính xác của quyết định phân quyền cuối cùng — chỉ được phép làm chậm đi (phải tính lại từ dữ liệu gốc), không bao giờ được phép làm sai. |
| NFR-3 | Việc ghi nhật ký thay đổi quyền không bao giờ được làm gián đoạn/chặn nghiệp vụ chính đang thực hiện. |
| NFR-4 | Thay đổi vai trò/quyền/cấu hình nhóm phải phản ánh gần như ngay lập tức tới người bị ảnh hưởng; riêng thay đổi sơ đồ tổ chức có thể có độ trễ tối đa khoảng 1 phút (đã nêu ở nhóm D/G). |
| NFR-5 | Không tồn tại cách "tự cấp thêm một quyền đứng riêng lẻ" ngoài vai trò — mọi việc mở rộng quyền hạn phải đi qua vai trò (có kiểm soát không-vượt-năng-lực người cấp) hoặc qua quy trình cấp quyền tạm thời có phê duyệt (nhóm F). |
| NFR-6 | Cấp quyền tạm thời (nhóm F) luôn có hạn tối đa 90 ngày — không có đường cấp "vĩnh viễn" chính thức. |
| NFR-7 🆕 | **Thu hồi quyền (revocation) phải luôn có hiệu lực gần như tức thời (real-time)** — bất kỳ thao tác nào làm mất quyền của một người (khoá tài khoản, hạ quyền trực tiếp, xoá vai trò kéo theo thu hồi quyền tạm thời, thu hồi quyền tạm thời thủ công) đều phải đăng xuất ngay phiên đăng nhập hiện tại của người đó, không chờ hết hạn tự nhiên. Khoảng cách giữa "quyền đã mất trên hệ thống" và "phiên đăng nhập tưởng vẫn còn quyền" là cửa sổ rủi ro bảo mật không được chấp nhận ở bất kỳ mức độ quyền nào (kể cả quyền tạm thời). |
| NFR-8 🆕 | Nhật ký quyết định cho phép/từ chối truy cập lưu tối đa **60 ngày**; nhật ký thay đổi cấu hình quyền (vai trò/thành viên/nhóm/chính sách/quyền tạm thời/quyền trên bản ghi) lưu **cố định 2 năm** cho mọi gói dịch vụ. |

---

## Phụ lục A — Tổng hợp Vấn đề tồn đọng còn mở (chưa có quyết định)

*Cập nhật v2: phần lớn các mục ở bản v1 đã được chốt phương án trong phiên rà soát 2026-08-21 — xem [Phụ lục B](#phụ-lục-b--lộ-trình-triển-khai--implementation-roadmap) cho các mục đã quyết định nhưng chờ triển khai. Danh sách dưới đây chỉ còn các mục **thực sự chưa có quyết định**, cần một phiên rà soát tiếp theo.*

**Trung bình**

1. Sửa danh sách thành viên nhóm bằng cách sửa trực tiếp cả nhóm (bỏ qua bước kiểm tra của chức năng "thêm thành viên" chuẩn) (nhóm C).
2. Chưa rõ khi xoá một nhóm, các quyền tạm thời đang cấp riêng cho nhóm đó có được dọn theo không (nhóm C).
3. Thay đổi sơ đồ tổ chức có độ trễ tối đa ~1 phút phản ánh vào phạm vi hiển thị dữ liệu (nhóm D/G) — chưa có quyết định về việc có cần rút ngắn cho các kịch bản cần hiệu lực tức thời (vd. cách ly khẩn cấp nhân sự) hay không.
4. 4 nhóm chức năng (Vai trò, Chính sách truy cập nâng cao, Quyền trên bản ghi, Cấp quyền tạm thời) dùng chung một khoá quyền cấu hình hệ thống khá rộng — chưa quyết định có cần tách nhỏ quyền hơn không (nhóm E).
5. Việc yêu cầu quyền tạm thời không tự động thông báo cho người phê duyệt phù hợp — chưa xác nhận có kênh thông báo đi kèm hay không (nhóm F).
6. Còn tồn tại một số bản ghi cấp quyền tạm thời "vĩnh viễn kiểu cũ" từ trước khi có quy trình phê duyệt — báo cáo kiểm định chỉ liệt kê, chưa có đợt rà soát thủ công dứt điểm (nhóm F).
7. Trục "phạm vi theo cấp quản lý" tải toàn bộ nhân sự workspace mỗi lần tính — có thể cần tối ưu khi workspace rất lớn (nhóm G).
8. Cần rà soát từng màn hình danh sách/xuất báo cáo để xác nhận có thực sự loại trừ đúng các bản ghi đã bị chặn quyền riêng hay không (nhóm I).
9. Người phụ trách một đơn vị tổ chức, nếu rời khỏi workspace, không tự động được gỡ khỏi vai trò phụ trách — chưa xác nhận có ảnh hưởng gì tới quyền xem dữ liệu (nhóm D).
10. Cơ chế kiểm tra tự động (chống lỗi "âm thầm mất tác dụng") cho che dữ liệu nhạy cảm mới có ở 1/4 nhóm dữ liệu (Khách hàng) — chưa quyết định có nhân rộng cho Công ty/Cơ hội/Hội thoại hay không (nhóm J).

**Thấp**

11. Thông báo lỗi một số nơi ở nhóm Người dùng còn dùng mã kỹ thuật thay vì câu rõ nghĩa.
12. Trùng tên đơn vị tổ chức có thông báo lỗi kém rõ ràng hơn trùng mã đơn vị.
13. Quyền trên bản ghi (nhóm I) chưa có màn hình lịch sử phiên bản riêng như vai trò/chính sách.
14. Việc ghi nhật ký quyết định cho phép/từ chối truy cập có thể bị tắt âm thầm tuỳ cấu hình triển khai — chưa có cơ chế xác nhận đang thực sự hoạt động (nhóm K).
15. Bản ghi nhật ký quyền có thể bị bỏ sót trong một số trường hợp hiếm — không có cơ chế bù đắp (nhóm K).

---

## Phụ lục B — Lộ trình triển khai (Implementation Roadmap)

Toàn bộ mục dưới đây **đã được Product Owner chốt phương án** trong phiên rà soát 2026-08-21 (xem lịch sử hội thoại grilling) — không cần bàn lại, chỉ cần lên kế hoạch thi công. Xếp theo mức ưu tiên gợi ý.

**Cập nhật 2026-08-21 (cuối phiên):** theo yêu cầu trực tiếp của Product Owner ("bắt đầu tạo các Issue/Ticket tương ứng cho tôi"), AI đã tự tạo cả 10 GitHub issue bên dưới tại `crmsaassaudi/product-management` — đây là một **ngoại lệ được phê duyệt tường minh** cho lần này, khác với quy ước mặc định đã thống nhất trước đó trong phiên (con người/PM-Tech Lead trực tiếp click tạo Issue, AI chỉ soạn block copy-paste). Mục D.4 dưới đây giữ lại nguyên văn nội dung đã dùng để tạo issue #2, nay chuyển thành bản ghi lưu vết (đã tạo thành issue #5), không còn là "chờ người click".

### D.1 Ưu tiên cao

| # | Việc cần làm | Thuộc mục | Issue | Ghi chú |
| --- | --- | --- | --- | --- |
| 1 | Xây API **Chuyển nhượng quyền sở hữu workspace** (handshake 2 chiều, hạn 72h, huỷ thủ công, auto-huỷ khi người nhận rời workspace, email thông báo hết hạn) | A9 | [#4](https://github.com/crmsaassaudi/product-management/issues/4) | Tính năng hoàn toàn mới — chạm cả `crm-api` + `crm-web` |
| 2 | Hợp nhất luồng đăng ký `/register` (form 1 bước) vào đúng wizard `/onboarding` — xoá luồng saga đồng bộ cũ, alias luôn tự sinh | A1 | [#5](https://github.com/crmsaassaudi/product-management/issues/5) | **Chạm cả `crm-api` và `crm-web`** — xem block issue mẫu ở D.4 |
| 3 | Chặn xoá Vai trò không cảnh báo: thêm bước đếm số người/nhóm dùng + xác nhận (soft warning), cascade thu hồi Role Assignment tham chiếu, đăng xuất phiên bị ảnh hưởng | E5 | [#6](https://github.com/crmsaassaudi/product-management/issues/6) | Chạm cả `crm-api` + `crm-web` |
| 4 | Áp dụng đăng xuất phiên ngay lập tức khi thu hồi quyền tạm thời (thủ công hoặc do cascade từ #3) | F3 | [#7](https://github.com/crmsaassaudi/product-management/issues/7) | Tái sử dụng cơ chế force-logout đã có sẵn cho khoá tài khoản |
| 5 | Chặn lưu (fail-fast) chính sách ABAC dùng điều kiện so sánh 2 thuộc tính bản ghi khi áp dụng cho action "có màn hình danh sách" | H1/BR-4 | [#8](https://github.com/crmsaassaudi/product-management/issues/8) | Cần định nghĩa `ACTION_BEHAVIORS` (quy ước action nào có list view) |
| 6 | Thêm cảnh báo UI tường minh khi tạo chính sách ABAC dùng điều kiện "chứa đoạn văn bản" | H1/BR-3 | [#9](https://github.com/crmsaassaudi/product-management/issues/9) | Không cần sửa engine, chỉ cần UI |
| 7 | Áp policy TTL cho nhật ký quyền: 60 ngày cho nhóm "quyết định truy cập", 2 năm cố định cho các nhóm còn lại | K1/BR-4 | [#10](https://github.com/crmsaassaudi/product-management/issues/10) | |

### D.2 Ưu tiên trung bình

| # | Việc cần làm | Thuộc mục | Issue | Ghi chú |
| --- | --- | --- | --- | --- |
| 8 | Chặn xoá Nhóm khi còn Nhóm con (đồng bộ quy tắc với Đơn vị tổ chức); **không** chặn khi còn thành viên trực tiếp | C4 | [#11](https://github.com/crmsaassaudi/product-management/issues/11) | |
| 9 | Mở rộng che dữ liệu nhạy cảm (System PII) cho Công ty/Account: `emails`, `phones`, `taxId`/`taxIdKey`, `billingAddress`, `shippingAddress` | J1 | [#12](https://github.com/crmsaassaudi/product-management/issues/12) | Không che `annualRevenue`/`numberOfEmployees` |
| 10 | Bắt buộc khoá tuyệt đối tác nhân AI cũng áp dụng cho cơ chế Custom PII (field tuỳ chỉnh do tenant tự khai báo), không chỉ System PII | J1/BR-1 | [#13](https://github.com/crmsaassaudi/product-management/issues/13) | Chi phí thấp — chặn ở tầng output cho tác nhân AI |

### D.3 Dọn dẹp (đã thực hiện xong trong phiên này)

✅ Các mục sau **đã được xử lý và xác nhận qua test** ngay trong phiên rà soát 2026-08-21, không cần đưa vào roadmap:
- Gỡ bỏ chức năng "Tạo thêm workspace cho tài khoản đã có sẵn" (A2) — xác nhận không có nơi nào gọi tới.
- Gỡ bỏ dòng truy vấn thừa từng bị nghi là rò rỉ dữ liệu liên hệ qua Ticket — xác nhận qua rà soát code đây **không phải** lỗ hổng thật (dữ liệu được truy vấn về nhưng bị loại bỏ trước khi tới phản hồi), chỉ là truy vấn thừa gây tốn tài nguyên.
- Gỡ bỏ 2 DTO và 1 guard không còn được sử dụng ở bất kỳ đâu trong hệ thống.

### D.4 Nội dung issue đã tạo (mục #2 → [issue #5](https://github.com/crmsaassaudi/product-management/issues/5), chạm cả `crm-api` và `crm-web`)

Theo quy trình issue xuyên project của tổ chức (`product-management/PROCESS.md`, mục "Feature Chạm Nhiều Project"): 1 issue duy nhất tại `crmsaassaudi/product-management`, gắn nhãn `repo:crm-api` + `repo:crm-web`. Nội dung dưới đây được giữ lại nguyên văn làm bản ghi lưu vết (đã dùng để tạo issue #5 ở trên) — không còn ở trạng thái "chờ copy-paste".

```text
Title: Hợp nhất luồng đăng ký workspace (/register) vào pipeline onboarding bất đồng bộ

Labels: repo:crm-api, repo:crm-web

## Bối cảnh
Hệ thống hiện có 2 luồng tạo workspace mới song song cho cùng một mục đích
(người hoàn toàn mới tự đăng ký):
- `/register` — form 1 bước, saga đồng bộ, cho phép tự chọn tên miền phụ.
- `/onboarding` — wizard 3 bước (start/context/complete), pipeline bất đồng bộ
  qua hàng chờ, tự sinh tên miền phụ.

Cả 2 đang được người dùng thật sử dụng đồng thời (`crm-web` route `/register`
và `/onboarding` cùng tồn tại), nhưng cho ra kết quả khác nhau: workspace tạo
qua `/register` thiếu tích hợp dịch vụ hỗ trợ tự động (crm-bot workspace),
không hỏi quy mô đội ngũ/mục đích sử dụng nên không tạo được phòng ban gợi ý/
dữ liệu mẫu đúng ngữ cảnh, và `provisioningStatus` mặc định "sẵn sàng" ngay
mà không qua các bước xử lý trung gian như luồng kia.

Quyết định (phiên rà soát SRS Identity & Access Management, 2026-08-21):
hợp nhất về đúng 1 luồng — thay `/register` bằng chính trải nghiệm wizard
của `/onboarding`, bỏ hẳn saga đồng bộ cũ.

## Phạm vi
- `crm-api`: gỡ bỏ luồng saga đồng bộ `TenantsService.register()` /
  `POST /api/v1/auth/register` sau khi frontend đã chuyển hẳn sang gọi
  `/onboarding/*`. Tên miền phụ luôn tự sinh (không nhận tham số alias tự chọn).
- `crm-web`: `RegisterPage.tsx` (route `/register`) chuyển sang tái sử dụng
  luồng/API của `/onboarding` — có thể giữ nguyên route `/register` như một
  điểm vào (redirect hoặc render lại) tới đúng wizard 3 bước, hoặc gỡ hẳn route
  `/register` và trỏ mọi liên kết sang `/onboarding`.
- Chấp nhận đánh đổi UX: người dùng phải trả lời thêm 2 câu hỏi (quy mô đội
  ngũ, mục đích sử dụng) và chờ thêm ~2-3 giây (màn hình "Đang khởi tạo
  workspace...") so với form 1 bước cũ.

## Tiêu chí hoàn thành
- [ ] Không còn 2 đường tạo tenant cho ra 2 kết quả khác nhau — mọi workspace
      mới (qua bất kỳ điểm vào UI nào) đều đi qua đúng 1 pipeline, có đầy đủ:
      tích hợp crm-bot workspace, phòng ban gợi ý theo quy mô/mục đích, dữ
      liệu mẫu.
- [ ] Tên miền phụ luôn do hệ thống tự sinh, không còn tham số cho phép
      client tự chọn.
- [ ] Saga đồng bộ cũ (`TenantsService.register`, endpoint
      `POST /api/v1/auth/register`) được gỡ bỏ khỏi `crm-api` sau khi xác
      nhận không còn nơi nào gọi tới.
- [ ] Test suite hiện có của cả 2 repo vẫn xanh; bổ sung test cho route/luồng
      mới nếu route `/register` được giữ lại dưới dạng redirect.

## Tham chiếu
- SRS: `product-management/srs/iam-tenant-authorization.md`, mục A1/A2.
```

---

*Hết tài liệu — v2, 2026-08-21.*
