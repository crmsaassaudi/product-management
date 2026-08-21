# SRS — Object Manager

| | |
|---|---|
| **Loại tài liệu** | Software Requirements Specification — vừa mô tả hành vi hiện tại, vừa là chuẩn để phát triển tiếp theo (xem "Ghi chú về nguồn gốc tài liệu") |
| **Module** | Object Manager — cấu hình schema và chính sách dữ liệu cho Contact, Account, Deal, Ticket, Task |
| **Ngày viết** | 2026-08-21 (cập nhật sau vòng review BA lần 1) |
| **Tài liệu liên quan** | Báo cáo audit kỹ thuật của module (repo `crm-api`, thư mục `docs/audit`) · [`CONTEXT.md`](../CONTEXT.md) (glossary) · [`docs/adr/0001-group-policy-conflict-resolution.md`](../docs/adr/0001-group-policy-conflict-resolution.md) |

## Ghi chú về nguồn gốc tài liệu

Object Manager đã được xây dựng và đưa vào vận hành mà chưa từng có SRS. Bản gốc của tài liệu này được viết sau khi module đã chạy thật, bằng cách khảo sát hành vi hệ thống hiện tại (qua giao diện quản trị và các quy tắc nghiệp vụ đang áp dụng) và trình bày lại theo đúng format một SRS nên có: theo tính năng, theo use case, bằng ngôn ngữ nghiệp vụ. Đây không phải tài liệu thiết kế kỹ thuật — không mô tả code, tên hàm hay tên file.

Sau vòng review đầu tiên với BA, tài liệu chuyển vai trò: từ "chỉ mô tả hiện trạng" sang **vừa mô tả hiện trạng vừa là chuẩn bắt buộc cho phát triển tiếp theo** — các yêu cầu mới thêm ở vòng review này (ví dụ FEAT-10) chưa tồn tại trong hệ thống, và code sẽ phải được cập nhật để khớp với tài liệu, không phải ngược lại.

**Quy ước nhãn trạng thái:** mỗi tính năng (FEAT) được gắn nhãn ngay sau tiêu đề:
- **[Đã triển khai]** — hành vi đã được xác minh khớp với hệ thống đang chạy tại thời điểm soạn bản gốc (2026-08-21).
- **[Yêu cầu mới]** — chưa tồn tại trong hệ thống, được thêm ở vòng review này; đội phát triển cần lên kế hoạch xây dựng.

Trong một FEAT đã `[Đã triển khai]`, nếu có một quy tắc nghiệp vụ (BR) cụ thể mới được bổ sung/thay đổi ở vòng review này, BR đó được đánh dấu riêng `[Yêu cầu mới]` ngay sau mã số — các BR không có nhãn kế thừa trạng thái của FEAT chứa nó. Với thay đổi tính năng trong tương lai, tài liệu này cần được cập nhật song song, không để trôi khỏi thực tế vận hành.

---

## 1. Giới thiệu

### 1.1 Mục đích

Tài liệu đặc tả toàn bộ yêu cầu chức năng và phi chức năng của module **Object Manager** — khu vực cấu hình cho phép quản trị viên tenant tùy biến cấu trúc dữ liệu và chính sách truy cập dữ liệu cho 5 đối tượng nghiệp vụ cốt lõi của CRM, mà không cần yêu cầu đội kỹ thuật can thiệp code.

### 1.2 Phạm vi

Tài liệu bao trùm toàn bộ tính năng của Object Manager: quản lý trường tùy biến, phân quyền hiển thị/chỉnh sửa trường theo nhóm người dùng, quy tắc kiểm tra dữ liệu, giai đoạn vòng đời khách hàng, trạng thái/nguồn, pipeline bán hàng, danh sách hiển thị tùy biến, và các cấu hình nâng cao theo từng đối tượng.

**Ngoài phạm vi:** nghiệp vụ vận hành riêng của từng đối tượng (quy trình bán hàng, quy trình hỗ trợ khách hàng...) — các tài liệu đó mô tả *cách dùng* dữ liệu, còn tài liệu này mô tả *cách cấu hình* cấu trúc và chính sách của dữ liệu đó. Chiến dịch marketing (Campaign) hiện chưa nằm trong phạm vi Object Manager — đây là giới hạn phạm vi sản phẩm hiện tại, xem mục 8.

Cũng ngoài phạm vi: **bộ lọc/cột hiển thị cá nhân** mà một người dùng cuối có thể tự lưu cho riêng mình ở màn hình danh sách bản ghi (nếu sản phẩm hỗ trợ) — đây là khái niệm khác với "Danh sách hiển thị" ở FEAT-08, vốn là cấu hình *dùng chung* do quản trị viên tạo và gán cho nhóm qua Object Manager. Ranh giới này được nêu lại ở Mục 8.

### 1.3 Đối tượng đọc

- Business Analyst / Product Owner: hiểu đúng hành vi hiện tại trước khi đề xuất thay đổi.
- QA: làm căn cứ viết test case chấp nhận.
- Đội triển khai (Customer Success/Support): hiểu rõ giới hạn khi tư vấn cấu hình cho khách hàng.
- Kỹ sư phát triển: hiểu ý định nghiệp vụ trước khi đọc code — chi tiết triển khai kỹ thuật không nằm trong tài liệu này.

### 1.4 Thuật ngữ & viết tắt

| Thuật ngữ | Giải thích |
|---|---|
| **Đối tượng (Object)** | Một loại bản ghi nghiệp vụ cốt lõi có thể cấu hình được: Liên hệ (Contact), Tài khoản/Công ty (Account), Cơ hội (Deal), Yêu cầu hỗ trợ (Ticket), Công việc (Task). |
| **Trường (Field)** | Một thuộc tính dữ liệu trên một đối tượng — có thể là trường chuẩn (có sẵn hệ thống) hoặc trường tùy biến (tenant tự tạo). |
| **Trường tùy biến (Custom Field)** | Trường do quản trị viên tenant tự định nghĩa thêm vào một đối tượng. |
| **Phân quyền trường (Field-Level Security – FLS)** | Cơ chế quyết định một trường được hiển thị, ẩn, chỉ đọc, hay che (mask) giá trị, áp dụng riêng theo từng nhóm người dùng — độc lập với quyền xem/sửa cả bản ghi. |
| **Nhóm quyền (Group)** | Một nhóm người dùng được quản trị viên tạo ra (ví dụ: "Sales Rep miền Bắc", "Support Tier 2"); phân quyền trường được cấu hình theo nhóm, không theo từng người dùng riêng lẻ. |
| **Bố cục mặc định (Default Layout)** | Cấu hình phân quyền trường áp dụng cho người dùng không thuộc nhóm nào có cấu hình riêng. |
| **Quy tắc kiểm tra dữ liệu (Validation Rule)** | Điều kiện do quản trị viên định nghĩa mà giá trị một trường phải thỏa mãn khi lưu bản ghi. |
| **Giai đoạn vòng đời (Lifecycle Stage)** | Các bước tuần tự một bản ghi trải qua (hiện chỉ áp dụng cho Liên hệ), ví dụ: Khách tiềm năng → Đang chăm sóc → Khách hàng. |
| **Bắt buộc khi tạo mới (Required on Create)** | Trường bắt buộc phải có giá trị ngay lúc tạo bản ghi — khác với trường chỉ bắt buộc phải điền *sau khi* bản ghi đã tồn tại. |
| **Danh sách hiển thị dùng chung (Shared List View)** | Một cấu hình bảng danh sách bản ghi (cột hiển thị, thứ tự) do quản trị viên tạo và gán cho một hay nhiều Nhóm quyền — thuộc phạm vi Object Manager. |
| **Bộ lọc/view cá nhân (Personal View)** | Bộ lọc hoặc bộ cột hiển thị mà một người dùng cuối tự lưu cho riêng mình, độc lập với Danh sách hiển thị dùng chung. Nằm ngoài phạm vi Object Manager (xem Mục 1.2). |
| **Nhật ký kiểm toán cấu hình (Configuration Audit Trail)** | Bản ghi lưu vết ai đã thay đổi cấu hình gì trong Object Manager, vào lúc nào — xem FEAT-10. |

### 1.5 Tài liệu tham khảo

- Báo cáo audit kỹ thuật Object Manager (`docs/audit/OBJECT_MANAGER_AUDIT.md`, repo `crm-api`) — lịch sử rà soát và khắc phục các vấn đề kỹ thuật của module, không phải tài liệu yêu cầu.
- [`CONTEXT.md`](../CONTEXT.md) — glossary thuật ngữ nghiệp vụ dùng chung cho các SRS trong `product-management` (nguồn thuật ngữ canonical, tài liệu này chỉ trích lại phần liên quan).
- [`docs/adr/0001-group-policy-conflict-resolution.md`](../docs/adr/0001-group-policy-conflict-resolution.md) — quyết định kiến trúc/chính sách đằng sau BR-03.2.

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

Một CRM đa tenant phục vụ nhiều khách hàng doanh nghiệp khác nhau, mỗi khách hàng có nhu cầu dữ liệu và quy trình khác nhau. Nếu mọi thay đổi cấu trúc dữ liệu (thêm trường, đổi quy tắc bắt buộc, ẩn thông tin nhạy cảm khỏi một nhóm nhân viên...) đều cần đội kỹ thuật sửa code, hệ thống không thể phục vụ nhiều khách hàng với chi phí hợp lý và tốc độ triển khai nhanh.

Object Manager giải quyết vấn đề này bằng cách cung cấp cho **quản trị viên của từng tenant** một khu vực tự cấu hình, và đảm bảo cấu hình đó được **tôn trọng nhất quán** ở mọi nơi dữ liệu được nhập vào hoặc đọc ra — dù là qua giao diện người dùng, nhập liệu hàng loạt từ file Excel, tự động hóa quy trình (automation), hay báo cáo.

### 2.2 Năm đối tượng được quản lý

| Đối tượng | Mô tả nghiệp vụ |
|---|---|
| **Liên hệ (Contact)** | Cá nhân mà doanh nghiệp tương tác — khách hàng, người liên hệ tại một công ty đối tác. |
| **Tài khoản (Account)** | Công ty/tổ chức là khách hàng hoặc đối tác. |
| **Cơ hội (Deal)** | Một thương vụ bán hàng đang theo đuổi, đi qua các giai đoạn của một pipeline bán hàng. |
| **Yêu cầu hỗ trợ (Ticket)** | Một yêu cầu/khiếu nại của khách hàng cần đội hỗ trợ xử lý. |
| **Công việc (Task)** | Một việc cần làm, có thể gắn với Liên hệ/Tài khoản/Cơ hội/Ticket. |

Đây là danh sách cố định ở phiên bản hiện tại — quản trị viên không tự tạo thêm một loại đối tượng hoàn toàn mới (xem giới hạn ở mục 8).

### 2.3 Vai trò người dùng (Actor)

| Actor | Vai trò trong module |
|---|---|
| **Quản trị viên hệ thống (Tenant Admin)** | Người duy nhất truy cập được khu vực cấu hình Object Manager. Thực hiện mọi thao tác: tạo trường, cấu hình phân quyền, quy tắc kiểm tra, giai đoạn vòng đời, danh sách hiển thị. Yêu cầu quyền quản trị hệ thống cấp cao (không phải mọi người có quyền "xem cài đặt" đều vào được khu vực này). |
| **Người dùng cuối (Sales Rep, Support Agent, Manager...)** | Không truy cập màn hình cấu hình, nhưng là đối tượng chịu tác động trực tiếp của phân quyền trường: họ thấy/không thấy, sửa được/không sửa được, thấy giá trị thật/giá trị bị che của từng trường tùy theo nhóm họ thuộc về. |

### 2.4 Nhóm tính năng

1. Danh mục đối tượng & năng lực khả dụng
2. Quản lý trường tùy biến
3. Phân quyền & bố cục hiển thị trường theo nhóm
4. Quy tắc kiểm tra dữ liệu
5. Giai đoạn vòng đời (Liên hệ)
6. Trạng thái & Nguồn theo từng đối tượng
7. Quản lý Pipeline bán hàng (Cơ hội)
8. Danh sách hiển thị tùy biến
9. Cấu hình nâng cao theo từng đối tượng
10. Nhật ký kiểm toán thay đổi cấu hình

---

## 3. Đặc tả yêu cầu chức năng

### FEAT-01 — Danh mục đối tượng & năng lực khả dụng `[Đã triển khai]`

**Mô tả nghiệp vụ:** Điểm vào của khu vực Object Manager, giúp quản trị viên biết những đối tượng nào cấu hình được và mỗi đối tượng hỗ trợ những thao tác hàng loạt/nhập-xuất/gộp bản ghi nào.

**Actor:** Quản trị viên.

**Luồng chính:**
1. Quản trị viên vào khu vực Object Manager, thấy 5 đối tượng được nhóm theo danh mục nghiệp vụ (Con người: Liên hệ, Tài khoản; Bán hàng: Cơ hội; Dịch vụ: Ticket; Năng suất: Công việc).
2. Chọn một đối tượng để vào màn hình cấu hình chi tiết của đối tượng đó.

**Quy tắc nghiệp vụ:**
- BR-01.1: Mỗi đối tượng có một tập năng lực cố định theo hệ thống, không tự cấu hình được qua UI, gồm: cập nhật hàng loạt, gán chủ sở hữu hàng loạt, gắn thẻ hàng loạt, nhập liệu từ file, xuất dữ liệu, có giai đoạn vòng đời, và gộp bản ghi trùng.
- BR-01.2: Năng lực hiện tại của từng đối tượng:
  - **Liên hệ**: gán chủ sở hữu hàng loạt, gắn thẻ hàng loạt, nhập/xuất file, có giai đoạn vòng đời, gộp bản ghi trùng. *Không* hỗ trợ cập nhật hàng loạt theo trường tùy ý.
  - **Tài khoản**: gán chủ sở hữu hàng loạt, gắn thẻ hàng loạt, nhập/xuất file. *Không* có giai đoạn vòng đời, *không* gộp bản ghi trùng.
  - **Cơ hội**: cập nhật hàng loạt, gán chủ sở hữu hàng loạt, gắn thẻ hàng loạt, nhập/xuất file. *Không* dùng mô hình giai đoạn vòng đời chung (có mô hình Pipeline riêng, xem FEAT-07), *không* gộp bản ghi trùng.
  - **Ticket**: gán chủ sở hữu hàng loạt, gắn thẻ hàng loạt, nhập/xuất file, gộp bản ghi trùng. *Không* cập nhật hàng loạt, *không* có giai đoạn vòng đời.
  - **Công việc**: cập nhật hàng loạt, gán chủ sở hữu hàng loạt, xuất file. *Không* gắn thẻ hàng loạt, *không* nhập file, *không* giai đoạn vòng đời, *không* gộp bản ghi trùng.

**Tiêu chí chấp nhận:**
- Quản trị viên xem được danh sách 5 đối tượng, phân nhóm đúng danh mục.
- Với mỗi đối tượng, hệ thống chỉ hiển thị/cho phép các thao tác hàng loạt/nhập-xuất/gộp đúng theo bảng năng lực ở trên — không đối tượng nào thực hiện được thao tác mà nó không được khai báo hỗ trợ.

---

### FEAT-02 — Quản lý trường tùy biến (Custom Field) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép quản trị viên bổ sung trường dữ liệu riêng cho tenant của mình trên bất kỳ đối tượng nào trong 5 đối tượng, để lưu thông tin đặc thù mà hệ thống không có sẵn.

**Actor:** Quản trị viên.

**Điều kiện tiên quyết:** Đã chọn đối tượng cần thêm trường.

**Luồng chính — Tạo trường mới:**
1. Quản trị viên chọn "Thêm trường mới" tại màn hình Trường & Quan hệ của một đối tượng.
2. Nhập tên trường, chọn kiểu dữ liệu (văn bản, số, ngày tháng, danh sách chọn, tệp đính kèm, công thức tính toán...), và các thuộc tính đi kèm (ví dụ danh sách lựa chọn nếu là kiểu chọn).
3. Hệ thống lưu định nghĩa trường; trường xuất hiện ngay trên form nhập liệu, bảng danh sách, và các màn hình cấu hình khác của đối tượng đó.

**Luồng chính — Sửa trường:**
1. Quản trị viên chọn một trường tùy biến đã tạo, chỉnh thông tin hiển thị (nhãn, mô tả, danh sách lựa chọn...).
2. Tên kỹ thuật của trường, đối tượng gắn với trường, và kiểu dữ liệu **không thể thay đổi** sau khi đã tạo — muốn đổi kiểu dữ liệu, quản trị viên phải tạo trường mới.

**Luồng chính — Xóa (Vô hiệu hóa) trường:**
1. Quản trị viên chọn "Xóa" trên một trường tùy biến, xác nhận trong hộp thoại cảnh báo.
2. Trường biến mất khỏi form nhập liệu, bảng danh sách, và các màn hình cấu hình cho các thao tác mới — nhưng **dữ liệu đã lưu trên các bản ghi cũ không bị xóa**, chỉ không còn hiển thị/thao tác được nữa qua giao diện thông thường.
3. Mọi cấu hình phân quyền hoặc quy tắc kiểm tra dữ liệu đang tham chiếu tới trường này được tự động gỡ bỏ, để không còn cấu hình "treo" gây khó hiểu cho quản trị viên sau này.

**Quy tắc nghiệp vụ:**
- BR-02.1: Tên kỹ thuật của trường tùy biến không được trùng với tên của bất kỳ trường chuẩn nào sẵn có trên cùng đối tượng (kể cả tên cũ đã đổi của một trường chuẩn).
- BR-02.2: Mỗi đối tượng chỉ được tạo tối đa **300 trường tùy biến**.
- BR-02.3: Xóa trường tùy biến luôn là **thao tác mềm** (ẩn/vô hiệu hóa) — không bao giờ xóa cứng dữ liệu lịch sử đã lưu, và không cho phép một trường mới tái sử dụng đúng tên kỹ thuật của trường đã xóa để tránh nhầm lẫn dữ liệu cũ sang định nghĩa mới.
- BR-02.4: Trường kiểu "Công thức tính toán" (Formula) luôn do hệ thống tự tính, không ai — kể cả qua nhập liệu hàng loạt hay tự động hóa — được phép ghi đè giá trị trực tiếp.
- BR-02.5: Giá trị nhập vào một trường tùy biến phải đúng định dạng khai báo (ví dụ: số phải là số, email phải đúng định dạng email, giá trị chọn phải nằm trong danh sách đã cấu hình) — áp dụng bất kể dữ liệu được nhập qua form, nhập liệu hàng loạt, hay ghi tự động từ automation.
- BR-02.6 `[Yêu cầu mới]`: Khi quản trị viên cố tạo trường thứ 301 trở đi cho cùng một đối tượng, hệ thống PHẢI hiển thị thông báo rõ ràng là đã đạt hạn mức và chặn thao tác lưu ngay tại thời điểm đó — không tạo trường một phần rồi báo lỗi sau, không để quản trị viên tự đoán lý do thất bại.
- BR-02.7 `[Đã triển khai]`: Hạn mức 300 trường/đối tượng là **giới hạn kỹ thuật đồng nhất cho mọi tenant**, không phân biệt theo gói dịch vụ (subscription tier) ở phiên bản này — xem thêm Mục 8.

**Luồng ngoại lệ:**
- Nếu quản trị viên đặt tên kỹ thuật trùng với trường chuẩn hoặc vượt quá 300 trường/đối tượng, hệ thống từ chối tạo và báo lỗi rõ ràng.

**Tiêu chí chấp nhận:**
- Trường tùy biến mới tạo xuất hiện đồng thời trên: form tạo/sửa bản ghi, bảng danh sách (tùy chọn hiển thị), màn hình phân quyền trường, màn hình quy tắc kiểm tra dữ liệu.
- Xóa một trường đang được dùng trong quy tắc bắt buộc/phân quyền thì quy tắc/phân quyền đó tự dọn dẹp, không còn hiển thị "trường không xác định" ở bất kỳ đâu.

---

### FEAT-03 — Phân quyền & bố cục hiển thị trường theo nhóm (Field-Level Security) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép quản trị viên kiểm soát, theo từng nhóm người dùng, trường nào được nhìn thấy, trường nào chỉ xem không sửa được, trường nào bị ẩn hoàn toàn, và trường nào phải che bớt giá trị (ví dụ chỉ hiện 4 số cuối). Đây là công cụ bảo vệ dữ liệu nhạy cảm (lương, số thẻ, thông tin cá nhân) mà không cần tách hệ thống riêng cho từng phòng ban.

**Actor:** Quản trị viên (người cấu hình); toàn bộ người dùng cuối (người chịu tác động).

**Điều kiện tiên quyết:** Đã có ít nhất một Nhóm quyền được tạo trong hệ thống (hoặc dùng cấu hình mặc định áp dụng chung).

**Luồng chính:**
1. Quản trị viên chọn đối tượng cần cấu hình, chọn phạm vi áp dụng: "Mặc định" (áp dụng cho ai không thuộc nhóm nào có cấu hình riêng) hoặc một Nhóm quyền cụ thể.
2. Với từng trường, quản trị viên chọn mức truy cập: **Xem & Sửa**, **Chỉ xem**, hoặc **Ẩn**.
3. Nếu chọn Xem & Sửa, có thể thêm bật "Bắt buộc nhập" cho trường đó (nếu trường cho phép bắt buộc — xem BR-03.3).
4. Có thể chọn kiểu che dữ liệu cho trường: Không che / Che toàn bộ / Chỉ hiện 4 ký tự cuối (áp dụng cho trường dạng văn bản/số nhạy cảm).
5. Sắp xếp thứ tự hiển thị trường bằng kéo-thả, gom nhóm trường vào các "Phần" (Section) trên form để tổ chức giao diện hợp lý (ví dụ: "Thông tin liên hệ", "Thông tin công việc").
6. Có thể gắn một Phần chỉ hiển thị ở một số Giai đoạn vòng đời nhất định (xem FEAT-05).
7. Xem trước (Preview) form theo từng giai đoạn vòng đời với dữ liệu mẫu trước khi lưu.
8. Lưu cấu hình — áp dụng ngay cho toàn bộ người dùng thuộc nhóm được chọn.

**Quy tắc nghiệp vụ:**
- BR-03.1: Phân quyền được cấu hình theo **Nhóm quyền**, không theo từng người dùng riêng lẻ. Nếu một người dùng không thuộc nhóm nào có cấu hình riêng cho đối tượng đó, áp dụng cấu hình Mặc định.
- BR-03.2: Nếu một người dùng thuộc **nhiều nhóm** có cấu hình khác nhau trên cùng một trường, hệ thống luôn chọn phương án **an toàn/hạn chế hơn**: Ẩn thắng Hiển thị, Chỉ xem thắng Xem&Sửa, mức che dữ liệu mạnh hơn thắng mức che yếu hơn. Ngược lại, yêu cầu "Bắt buộc nhập" được cộng gộp — nếu bất kỳ nhóm nào yêu cầu bắt buộc thì trường đó bắt buộc. Đây là quyết định chủ đích, thiên về bảo mật dữ liệu — xem [ADR-0001](../docs/adr/0001-group-policy-conflict-resolution.md) cho lý do và các phương án đã cân nhắc.

  > **Hướng dẫn vận hành cho Admin (không phải ràng buộc hệ thống):** để nâng quyền truy cập một trường cho một người cụ thể (ví dụ khi thăng chức Sales Rep → Sales Manager), hãy **rút người đó khỏi nhóm đang hạn chế trường đó**, hoặc tổ chức lại nhóm cho rõ ràng — không nên chỉ *thêm* người đó vào một nhóm quyền mở rộng hơn trong khi vẫn giữ họ ở nhóm cũ, vì theo BR-03.2 cấu hình hạn chế hơn luôn thắng.
- BR-03.3: Một số trường do hệ thống tự quản lý (ví dụ: Ngày tạo, Người tạo, một số trường có giá trị mặc định do hệ thống tính sẵn) **không thể** được cấu hình là "Bắt buộc nhập" — vì bản thân người dùng không nhập tay các trường này. Cấu hình cố tình vi phạm sẽ bị hệ thống từ chối ngay khi lưu.
- BR-03.4: Một số trường mang nhãn/tên định danh chính của bản ghi (ví dụ tên Liên hệ, tiêu đề Cơ hội) **không thể** bị che dữ liệu — vì đây là thông tin cần thiết tối thiểu để người dùng nhận diện bản ghi trong danh sách.
- BR-03.5: Trường đang bị **Ẩn** thì không được phép xuất hiện ở **bất kỳ nơi nào khác** ngoài form/bảng chính — bao gồm: file xuất dữ liệu, báo cáo thống kê (kể cả gộp nhóm/đếm theo trường đó), kết quả tìm kiếm toàn hệ thống, xem trước khi gộp bản ghi trùng, xem trước danh sách phân đoạn khách hàng (segment).
- BR-03.6: Trường bị che dữ liệu (ví dụ hiện `****1234`) mà người dùng gửi lại nguyên giá trị bị che đó khi lưu form thì hệ thống phải **bỏ qua**, không được ghi đè giá trị thật bằng chuỗi che — tránh mất dữ liệu gốc.
- BR-03.7: Việc thay đổi cấu hình phân quyền cho một nhóm/đối tượng có hiệu lực ngay, không ảnh hưởng tới cấu hình của nhóm hoặc đối tượng khác đang được admin khác chỉnh sửa cùng lúc.

**Luồng ngoại lệ:**
- Quản trị viên cố đánh dấu bắt buộc một trường hệ thống quản lý → nút bắt buộc bị khóa ngay trên giao diện, không cho bật.
- Quản trị viên cố đặt chế độ che dữ liệu cho trường không hỗ trợ che → tùy chọn che bị khóa trên giao diện.

**Tiêu chí chấp nhận:**
- Người dùng thuộc nhóm bị ẩn một trường thì không thấy trường đó ở form, bảng danh sách, file xuất, báo cáo, tìm kiếm, và mọi màn hình xem trước liên quan.
- Người dùng thuộc nhiều nhóm với cấu hình khác nhau luôn nhận được trải nghiệm an toàn nhất trong số các cấu hình áp dụng.
- Gửi lại giá trị bị che không làm mất dữ liệu gốc.

---

### FEAT-04 — Quy tắc kiểm tra dữ liệu (Validation Rules) `[Đã triển khai]`

> **Giới hạn phạm vi:** các quy tắc dưới đây chỉ kiểm tra **một trường độc lập**. Quy tắc so sánh/phụ thuộc giữa nhiều trường (cross-field, ví dụ "nếu Trạng thái = Đã chốt thì Lý do chốt không được để trống") **chưa được hỗ trợ** — xem Mục 8, khoản 7.

**Mô tả nghiệp vụ:** Cho phép quản trị viên định nghĩa điều kiện mà giá trị một trường phải thỏa mãn khi lưu bản ghi, để đảm bảo chất lượng dữ liệu theo yêu cầu riêng của tenant (ví dụ: số điện thoại phải đúng định dạng, doanh thu phải nằm trong khoảng hợp lý).

**Actor:** Quản trị viên.

**Luồng chính:**
1. Quản trị viên vào màn hình Quy tắc kiểm tra dữ liệu của một đối tượng.
2. Chọn "Thêm quy tắc", chọn trường áp dụng, chọn kiểu kiểm tra: **Không được để trống**, **Đúng định dạng** (biểu thức chính quy), hoặc **Nằm trong khoảng** (giá trị số).
3. Nhập thông báo lỗi hiển thị cho người dùng khi vi phạm.
4. Có thể bật/tắt tạm thời một quy tắc mà không cần xóa.
5. Các thay đổi (thêm/sửa/tắt/xóa) chỉ có hiệu lực thật sự sau khi bấm "Lưu tất cả" — trước đó hệ thống cảnh báo còn thay đổi chưa lưu nếu người dùng cố rời trang.

**Quy tắc nghiệp vụ:**
- BR-04.1: Quy tắc "Đúng định dạng" bỏ qua kiểm tra nếu trường đang để trống — việc bắt buộc nhập là trách nhiệm của quy tắc "Không được để trống" hoặc cấu hình bắt buộc riêng, tránh vô tình biến một trường tùy chọn thành bắt buộc chỉ vì thêm một quy tắc định dạng.
- BR-04.2: Quy tắc "Nằm trong khoảng" cho phép để trống một đầu (chỉ giới hạn tối thiểu hoặc chỉ giới hạn tối đa) và hỗ trợ giá trị âm.
- BR-04.3: Nếu một quy tắc được cấu hình sai (biểu thức không hợp lệ, khoảng giá trị không hợp lệ, hoặc biểu thức có nguy cơ gây treo hệ thống), quy tắc đó **tạm thời không được áp dụng** thay vì chặn toàn bộ việc lưu dữ liệu của cả đối tượng — lỗi cấu hình của quản trị viên không được phép làm gián đoạn hoạt động của toàn bộ người dùng.
- BR-04.4: Quy tắc kiểm tra áp dụng nhất quán bất kể dữ liệu được nhập qua form, nhập liệu hàng loạt từ file, hay ghi tự động từ automation — không có kênh nhập liệu nào được phép bỏ qua quy tắc đã cấu hình.
- BR-04.5: Trường đang ở chế độ "Chỉ xem" (không cho sửa) thì không áp dụng quy tắc kiểm tra dữ liệu cho lần ghi đó, vì người dùng vốn không được phép thay đổi giá trị.

**Tiêu chí chấp nhận:**
- Lưu bản ghi vi phạm bất kỳ quy tắc active nào → bị từ chối, hiển thị đúng thông báo lỗi đã cấu hình cho trường đó.
- Nhập liệu hàng loạt vi phạm quy tắc → dòng dữ liệu đó bị báo lỗi riêng, không được âm thầm ghi vào hệ thống.
- Một quy tắc lỗi cấu hình không làm ảnh hưởng tới khả năng lưu dữ liệu bình thường của các trường khác.

---

### FEAT-05 — Giai đoạn vòng đời (Lifecycle Stages) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép quản trị viên định nghĩa các giai đoạn mà một bản ghi Liên hệ trải qua trong hành trình khách hàng, và những trường bắt buộc phải điền ở từng giai đoạn.

**Actor:** Quản trị viên.

**Phạm vi áp dụng:** Hiện tại chỉ **Liên hệ (Contact)** có mô hình giai đoạn vòng đời nhiều bước; các đối tượng khác dùng mô hình trạng thái đơn giản hơn (xem FEAT-06), riêng Cơ hội có mô hình Pipeline chuyên biệt (xem FEAT-07).

**Luồng chính:**
1. Quản trị viên vào màn hình Giai đoạn vòng đời của Liên hệ.
2. Thêm một giai đoạn mới: đặt tên hiển thị, thứ tự, màu sắc nhận diện.
3. Chọn những trường **bắt buộc phải có giá trị** khi một bản ghi ở giai đoạn này.
4. Đánh dấu một giai đoạn là **"Đã chuyển đổi"** (điểm kết thúc hành trình, ví dụ đã trở thành khách hàng chính thức) — có thể đồng thời bật tùy chọn **tự động tạo một Cơ hội bán hàng mới** khi Liên hệ đạt tới giai đoạn này.
5. Sắp xếp thứ tự các giai đoạn theo số thứ tự.

**Quy tắc nghiệp vụ:**
- BR-05.1: Trường được chọn "bắt buộc ở giai đoạn này" phải là trường mà người dùng thực sự có thể tự nhập (không chọn được các trường do hệ thống tự quản lý).
- BR-05.2: Nếu một trường được đánh dấu bắt buộc ở một giai đoạn, nhưng đồng thời bị cấu hình Ẩn ở đúng giai đoạn đó (qua phân quyền theo Phần — FEAT-03), hệ thống phải **cảnh báo xung đột** cho quản trị viên ngay trên màn hình cấu hình, vì người dùng sẽ không thể thỏa mãn yêu cầu bắt buộc với một trường họ không nhìn thấy.
- BR-05.3: Việc tự động tạo Cơ hội khi chuyển giai đoạn chỉ áp dụng cho các giai đoạn được đánh dấu "Đã chuyển đổi", không áp dụng ngầm cho giai đoạn khác.

**Tiêu chí chấp nhận:**
- Bản ghi Liên hệ chuyển sang một giai đoạn có trường bắt buộc mà chưa có giá trị → không cho phép lưu chuyển giai đoạn cho đến khi bổ sung.
- Xung đột "bắt buộc nhưng bị ẩn" luôn được cảnh báo ngay tại màn hình cấu hình, không đợi tới khi người dùng cuối gặp lỗi mới phát hiện.

---

### FEAT-06 — Trạng thái & Nguồn (Status & Sources) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép quản trị viên định nghĩa danh sách trạng thái vận hành (ví dụ trạng thái xử lý Ticket, trạng thái Công việc) và danh sách nguồn phát sinh bản ghi (ví dụ nguồn khách hàng: Quảng cáo, Giới thiệu, Website) cho từng đối tượng.

**Actor:** Quản trị viên.

**Luồng chính — Trạng thái:**
1. Quản trị viên chọn đối tượng, xem danh sách trạng thái hiện có.
2. Thêm trạng thái mới: tên hiển thị, thứ tự, màu sắc; đánh dấu **mặc định** (trạng thái khởi tạo khi tạo bản ghi mới) và/hoặc **trạng thái kết thúc** (đóng vòng đời xử lý, ví dụ "Đã giải quyết", "Đã hủy").
3. Riêng đối tượng Cơ hội, mỗi trạng thái còn có thêm: tỉ lệ thắng ước tính (%), số ngày dự kiến ở trạng thái đó, và cờ đánh dấu "Thắng" (Won).
4. Đặt một trạng thái làm mặc định sẽ tự động bỏ mặc định khỏi trạng thái khác cùng nhóm.

**Luồng chính — Nguồn:**
1. Quản trị viên thêm/sửa/xóa tên nguồn phát sinh bản ghi cho một đối tượng — đây là danh sách đơn giản chỉ gồm tên hiển thị.

**Quy tắc nghiệp vụ:**
- BR-06.1: Chỉ Liên hệ có nhiều tầng trạng thái theo giai đoạn vòng đời (FEAT-05); các đối tượng còn lại dùng một danh sách trạng thái phẳng, dùng chung cho toàn bộ bản ghi của đối tượng đó.
- BR-06.2: Danh sách trạng thái/nguồn hiển thị trên các trường chọn (dropdown) của form nhập liệu **phải luôn khớp với danh sách mà hệ thống chấp nhận khi lưu** — không được xảy ra tình trạng người dùng chọn một trạng thái hợp lệ trên form nhưng bị hệ thống từ chối khi lưu.
- BR-06.3: Xóa một trạng thái/nguồn đang được dùng cho các bản ghi hiện có không được làm ảnh hưởng tới dữ liệu lịch sử.

**Tiêu chí chấp nhận:**
- Trạng thái mới thêm xuất hiện ngay trên form tạo/sửa bản ghi và được chấp nhận khi lưu.
- Đổi trạng thái mặc định luôn đảm bảo chỉ có đúng một trạng thái mặc định trong cùng nhóm.

---

### FEAT-07 — Quản lý Pipeline bán hàng (Deal) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép quản trị viên định nghĩa một hoặc nhiều quy trình bán hàng (pipeline) mà Cơ hội đi qua, phù hợp với các dòng sản phẩm/kênh bán hàng khác nhau của doanh nghiệp.

**Actor:** Quản trị viên.

**Luồng chính:**
1. Quản trị viên xem danh sách Pipeline hiện có, mỗi pipeline có tên, mô tả, màu sắc nhận diện, và cờ "Mặc định"/"Đã lưu trữ".
2. Tạo pipeline mới: đặt tên, mô tả, màu sắc, và bật/tắt tùy chọn **"Bắt buộc tuần tự"** — nếu bật, Cơ hội trong pipeline này chỉ được tiến từng bước một qua các giai đoạn kế tiếp (không được nhảy cóc); đóng thương vụ (thắng/thua) và lùi giai đoạn luôn được phép bất kể tùy chọn này.
3. Đặt một pipeline làm mặc định.
4. Lưu trữ (archive) một pipeline không còn dùng — đây là thao tác mềm, không xóa dữ liệu các Cơ hội đã gắn với pipeline đó.

**Quy tắc nghiệp vụ:**
- BR-07.1: Không thể lưu trữ pipeline đang là mặc định — phải chuyển mặc định sang pipeline khác trước.
- BR-07.2: Các giai đoạn cụ thể bên trong một pipeline (ví dụ: Tiếp cận → Đề xuất → Đàm phán → Chốt) được quản lý ở màn hình Trạng thái & Nguồn của Cơ hội (FEAT-06), không phải ở màn hình Pipeline này — màn hình Pipeline chỉ quản lý thông tin cấp pipeline (tên, quy tắc tuần tự, mặc định/lưu trữ).

**Tiêu chí chấp nhận:**
- Cơ hội thuộc pipeline có bật "Bắt buộc tuần tự" không thể được chuyển nhảy cóc qua giai đoạn không liền kề (trừ khi là đóng thương vụ hoặc lùi giai đoạn).
- Lưu trữ pipeline không xóa lịch sử Cơ hội đã từng thuộc pipeline đó.

---

### FEAT-08 — Danh sách hiển thị tùy biến (List Views) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép quản trị viên tạo các cấu hình bảng danh sách khác nhau (cột nào hiển thị, thứ tự cột) và gán mỗi cấu hình cho một hoặc nhiều nhóm người dùng, để mỗi nhóm thấy thông tin phù hợp với công việc của họ khi xem danh sách bản ghi.

> **Ghi chú ranh giới:** đây là cấu hình **dùng chung** do Admin quản lý (Danh sách hiển thị dùng chung). Bộ lọc/cột hiển thị **cá nhân** mà một người dùng tự lưu riêng cho mình (nếu sản phẩm hỗ trợ) là khái niệm khác, nằm ngoài phạm vi Object Manager — xem Mục 1.2 và Mục 8.

**Actor:** Quản trị viên.

**Luồng chính:**
1. Quản trị viên tạo danh sách hiển thị mới cho một đối tượng: đặt tên, chọn các cột hiển thị (bao gồm cả trường tùy biến), sắp xếp thứ tự cột, ẩn/hiện từng cột, và tùy chỉnh độ rộng cột.
2. Gán danh sách hiển thị này làm mặc định cho một hoặc nhiều Nhóm quyền.
3. Trong phạm vi các nhóm đã gán, có thể loại trừ riêng một vài người dùng cụ thể khỏi việc dùng danh sách này.
4. Có thể **sao chép** một danh sách hiển thị có sẵn để tạo biến thể mới nhanh hơn.
5. Xóa một danh sách hiển thị — không áp dụng được cho danh sách được đánh dấu là "danh sách hệ thống mặc định".

**Quy tắc nghiệp vụ:**
- BR-08.1: Một cột chỉ được thêm vào danh sách hiển thị nếu tương ứng với một trường có thể hiển thị được trên bảng (bao gồm cột hiển thị giá trị suy ra từ một quan hệ, ví dụ cột "Chủ sở hữu" hiển thị tên người được gán).
- BR-08.2: Sao chép một danh sách hiển thị không kế thừa việc gán nhóm/loại trừ người dùng của bản gốc — quản trị viên phải gán lại.
- BR-08.3: Danh sách hiển thị hệ thống mặc định không thể bị xóa (có thể sao chép để tạo bản tùy biến riêng).
- BR-08.4: Cột tương ứng với trường đang bị Ẩn theo phân quyền (FEAT-03) không được hiển thị dữ liệu cho người dùng bị ẩn trường đó, kể cả khi cột này nằm trong một danh sách hiển thị đã cấu hình.

**Tiêu chí chấp nhận:**
- Người dùng thuộc nhóm được gán một danh sách hiển thị thấy đúng bộ cột, đúng thứ tự đã cấu hình.
- Người dùng bị loại trừ riêng không thấy danh sách hiển thị đó dù thuộc nhóm được gán.

---

### FEAT-09 — Cấu hình nâng cao theo từng đối tượng (Advanced Settings) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Mỗi đối tượng có thêm một số cấu hình đặc thù không thuộc các nhóm tính năng chung ở trên.

**Actor:** Quản trị viên.

**Nội dung theo từng đối tượng:**

- **Tài khoản**: bật/tắt mô hình phân cấp công ty mẹ-con và độ sâu phân cấp tối đa; danh mục loại tài khoản và ngành nghề; tự động phân công chủ sở hữu theo khu vực; hỗ trợ đa tiền tệ.
- **Liên hệ**: chính sách xử lý trùng lặp khi phát hiện email/số điện thoại giống nhau (xem xét thủ công hoặc ngăn tạo mới); mô hình quan hệ với Tài khoản (một Liên hệ thuộc một hay nhiều Tài khoản); theo dõi đồng ý nhận email/tuân thủ bảo vệ dữ liệu cá nhân (GDPR); danh mục vai trò liên hệ; quy tắc tự động phân công Liên hệ mới cho nhân viên (chia đều, theo trọng số, theo khu vực).
- **Cơ hội**: các điều kiện bắt buộc trước khi đánh dấu Thắng (bắt buộc có giá trị/chủ sở hữu/liên hệ/ngày đóng); cấu hình dự báo doanh thu (đơn vị tiền tệ, năm tài chính bắt đầu từ quý nào, có tính dự báo theo trọng số hay không); mục tiêu doanh số theo nhóm/cá nhân và chu kỳ (tháng/quý/năm).
- **Công việc**: danh mục loại công việc tùy biến; thời gian nhắc nhở mặc định trước hạn; bật quy tắc tự động hoàn tất công việc.
- **Ticket**: danh mục loại Ticket; cây phân loại (category) đa cấp; danh mục mã lý do đóng (resolution code).

**Quy tắc nghiệp vụ:**
- BR-09.1: Cấu hình nâng cao là cấu hình cấp tenant (áp dụng cho toàn bộ tenant), không cấu hình theo từng nhóm người dùng như FEAT-03.
- BR-09.2: Điều kiện bắt buộc khi đánh dấu Cơ hội Thắng chỉ kiểm tra tại thời điểm chuyển sang trạng thái Thắng, không chặn việc tạo/chỉnh sửa Cơ hội ở các giai đoạn khác.

**Tiêu chí chấp nhận:**
- Thay đổi một cấu hình nâng cao có hiệu lực ngay cho toàn tenant mà không cần thao tác thêm.
- Cố gắng đánh dấu Cơ hội Thắng khi thiếu điều kiện bắt buộc đã cấu hình → bị từ chối, nêu rõ điều kiện còn thiếu.

---

### FEAT-10 — Nhật ký kiểm toán thay đổi cấu hình (Configuration Audit Trail) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Object Manager cho phép quản trị viên thay đổi cấu trúc dữ liệu và chính sách truy cập ảnh hưởng tới toàn bộ người dùng của tenant. Khi một thay đổi gây sự cố (ví dụ: một nhóm nhân viên đột nhiên không còn thấy số điện thoại khách hàng), hệ thống phải cho phép trả lời được: ai đã đổi cấu hình gì, vào lúc nào — mà không cần truy vấn trực tiếp cơ sở dữ liệu ứng dụng.

**Actor:** Hệ thống tự động ghi nhận mỗi khi Quản trị viên thực hiện một thao tác cấu hình trong Object Manager. Không có actor nào chủ động "xem" nhật ký ở phiên bản này (xem BR-10.4) — đội vận hành/hỗ trợ tra cứu khi cần điều tra sự cố.

**Luồng chính:**
1. Quản trị viên thực hiện bất kỳ thao tác tạo/sửa/xóa cấu hình nào trong Object Manager (trường tùy biến, phân quyền/bố cục trường, quy tắc kiểm tra dữ liệu, giai đoạn vòng đời, trạng thái/nguồn, pipeline, danh sách hiển thị, cấu hình nâng cao).
2. Hệ thống tự động ghi một bản ghi nhật ký gồm: thời điểm, người thực hiện, loại hành động, đối tượng/mục cấu hình bị ảnh hưởng.
3. Nếu thao tác thuộc nhóm phân quyền trường (đổi mức truy cập, bật/tắt bắt buộc, đổi kiểu che dữ liệu), nhật ký còn lưu **giá trị trước và sau** của đúng thuộc tính bị đổi.
4. Việc ghi nhật ký diễn ra nền, không chặn hay làm chậm thao tác của quản trị viên.

**Quy tắc nghiệp vụ:**
- BR-10.1: Mọi hành động tạo/sửa/xóa cấu hình trong Object Manager PHẢI để lại đúng một bản ghi nhật ký tương ứng, không có ngoại lệ.
- BR-10.2: Với thay đổi thuộc nhóm phân quyền trường (mức truy cập, bắt buộc, che dữ liệu), nhật ký PHẢI lưu giá trị trước và sau của thuộc tính bị đổi. Với các hành động khác (CRUD trường/giai đoạn/trạng thái/nguồn/pipeline/danh sách hiển thị/cấu hình nâng cao), nhật ký chỉ cần ghi loại hành động và đối tượng/mục bị tác động, không cần snapshot đầy đủ.
- BR-10.3: Nhật ký được lưu trữ **không giới hạn thời gian** ở phiên bản này. Một chính sách lưu trữ theo thời hạn cụ thể (nếu cần vì lý do pháp lý hoặc chi phí hạ tầng) sẽ do chính sách chung của nền tảng quyết định sau, không đặt cứng trong tài liệu này.
- BR-10.4: Ở phiên bản này, nhật ký **không có màn hình tự tra cứu cho tenant admin** — chỉ đội vận hành/hỗ trợ tra cứu được qua công cụ nội bộ. Màn hình lịch sử thay đổi cho chính tenant admin tự xem là hướng mở rộng hợp lý, xem Mục 8.
- BR-10.5: Việc ghi nhật ký KHÔNG được phép là điều kiện quyết định một thao tác cấu hình thành công hay thất bại — đây là tác vụ nền, không phải một bước xác thực trong luồng chính.

**Tiêu chí chấp nhận:**
- Mọi thay đổi cấu hình trong Object Manager để lại đúng 1 bản ghi nhật ký tương ứng, không thiếu, không trùng.
- Với thay đổi phân quyền trường, nhật ký cho biết đủ để trả lời "trường Y của nhóm X bị đổi từ gì sang gì, ai đổi, lúc nào" mà không cần suy đoán.
- Đội vận hành tra cứu được nhật ký phục vụ điều tra sự cố mà không cần quyền truy cập trực tiếp cơ sở dữ liệu ứng dụng.

---

## 4. Yêu cầu phi chức năng

### 4.1 Bảo mật & Phân quyền

- **NFR-1 (Cách ly dữ liệu theo tenant):** Mọi cấu hình (trường tùy biến, phân quyền, quy tắc, giai đoạn, danh sách hiển thị...) chỉ nhìn thấy và áp dụng được trong phạm vi tenant đã tạo ra nó, không rò rỉ chéo giữa các tenant.
- **NFR-3 (Phân quyền truy cập khu vực cấu hình):** Chỉ vai trò quản trị hệ thống mới truy cập được toàn bộ khu vực Object Manager; người không đủ quyền phải thấy thông báo từ chối rõ ràng, không được âm thầm chuyển hướng hay hiển thị dữ liệu rút gọn.
- **NFR-7 (Ưu tiên an toàn khi có nhiều cấu hình chồng lấn):** Khi một người dùng chịu ảnh hưởng của nhiều cấu hình phân quyền cùng lúc (do thuộc nhiều nhóm), hệ thống luôn chọn phương án hạn chế/an toàn hơn cho việc hiển thị dữ liệu (xem BR-03.2, ADR-0001).

### 4.2 Toàn vẹn & Nhất quán dữ liệu

- **NFR-2 (Nhất quán xuyên kênh dữ liệu):** Một quy tắc đã cấu hình (bắt buộc, chỉ đọc, ẩn, định dạng...) phải được áp dụng **giống nhau** dù dữ liệu đi vào qua giao diện người dùng, nhập liệu hàng loạt từ file, hay ghi tự động bởi automation — không có kênh nào được phép "đi tắt" qua cấu hình đã thiết lập.
- **NFR-6 (Không để lỗi cấu hình làm gián đoạn toàn hệ thống):** Một quy tắc/cấu hình bị nhập sai bởi quản trị viên không được phép làm sập hoặc chặn hoàn toàn khả năng lưu dữ liệu của những người dùng khác đang thao tác bình thường (xem BR-04.3).
- **NFR-9 (Có thể kiểm thử hồi quy):** Các ràng buộc nền tảng (một trường hệ thống không thể bị đánh dấu bắt buộc, một trường không hỗ trợ che thì không cấu hình che được...) phải được kiểm tra tự động, không phụ thuộc vào việc quản trị viên/kỹ sư nhớ để rà soát thủ công mỗi lần thay đổi.

### 4.3 Hiệu năng & Vận hành

- **NFR-4 (An toàn khi nhiều quản trị viên thao tác song song):** Hai quản trị viên chỉnh sửa cấu hình của hai đối tượng/nhóm khác nhau cùng lúc không được ghi đè mất cấu hình của nhau.
- **NFR-5 (Giới hạn quy mô hợp lý):** Số trường tùy biến mỗi đối tượng, số dòng cấu hình trong một lần lưu, đều có giới hạn trên để đảm bảo hệ thống vận hành ổn định (chi tiết ngưỡng nêu tại BR-02.2, BR-02.6, BR-02.7).

### 4.4 Đa ngôn ngữ

- **NFR-8 (Đa ngôn ngữ):** Tên đối tượng và tên trường chuẩn phải hiển thị theo ngôn ngữ người dùng đang chọn; tên trường tùy biến hiển thị đúng theo tên quản trị viên đã đặt.

---

## 5. Ma trận quyền truy cập tính năng

| Tính năng | Quản trị viên hệ thống | Người dùng cuối |
|---|:---:|:---:|
| Xem danh mục đối tượng & năng lực | ✅ | — |
| Tạo/sửa/xóa trường tùy biến | ✅ | — |
| Cấu hình phân quyền trường theo nhóm | ✅ | — |
| Chịu tác động của phân quyền trường | — | ✅ (thụ động) |
| Cấu hình quy tắc kiểm tra dữ liệu | ✅ | — |
| Cấu hình giai đoạn vòng đời / trạng thái / nguồn / pipeline | ✅ | — |
| Tạo/gán danh sách hiển thị | ✅ | — |
| Sử dụng danh sách hiển thị đã gán | — | ✅ |
| Cấu hình nâng cao theo đối tượng | ✅ | — |
| Nhật ký kiểm toán cấu hình (ghi tự động, không thao tác) | — (tự động khi Admin cấu hình) | — |

---

## 6. Kịch bản chấp nhận tổng hợp (Acceptance Scenarios)

1. **Trường bắt buộc được tôn trọng ở mọi kênh nhập liệu:** Quản trị viên đánh dấu một trường tùy biến là bắt buộc khi tạo mới. Tạo bản ghi thiếu trường này qua giao diện bị từ chối; nhập liệu hàng loạt thiếu trường này bị báo lỗi ở đúng dòng vi phạm, không tạo bản ghi thiếu dữ liệu; automation ghi sai định dạng/giá trị không hợp lệ vào trường này bị từ chối, không báo "thành công" giả.
2. **Trường bị ẩn không lộ ra ở kênh phụ:** Người dùng thuộc nhóm bị ẩn một trường không nhìn thấy trường đó ở: form, bảng danh sách, file xuất, báo cáo (kể cả khi gộp nhóm/đếm theo trường đó), kết quả tìm kiếm, màn hình xem trước gộp bản ghi trùng hoặc xem trước phân đoạn khách hàng.
3. **Không thể tự cấu hình sai gây gián đoạn:** Quản trị viên thử đánh dấu bắt buộc một trường do hệ thống quản lý (ví dụ trạng thái mặc định khi tạo mới) → hệ thống chặn ngay tại bước lưu cấu hình, không đợi đến khi người dùng thật gặp lỗi mới phát hiện ra.
4. **Nhiều nhóm chồng lấn luôn an toàn:** Người dùng thuộc 2 nhóm, một nhóm ẩn trường X, nhóm còn lại cho xem-sửa trường X → người dùng vẫn không thấy trường X.
5. **Xóa trường tùy biến dọn sạch cấu hình liên quan:** Xóa một trường tùy biến đang được dùng trong quy tắc bắt buộc và phân quyền hiển thị → cả hai cấu hình tự động gỡ bỏ tham chiếu, không còn "quy tắc chết" gây khó hiểu cho quản trị viên sau này.
6. **Danh sách chọn luôn khớp giữa hiển thị và khi lưu:** Trạng thái/nguồn hiển thị trên form chọn được là chấp nhận được khi lưu — không có trường hợp người dùng chọn một giá trị hợp lệ trên form nhưng bị từ chối khi submit.
7. **Truy vết thay đổi cấu hình phân quyền (FEAT-10):** Quản trị viên đổi trường Số điện thoại của Liên hệ từ Xem&Sửa sang Ẩn cho nhóm Sales. Vài ngày sau, đội hỗ trợ nhận báo cáo "Sales không còn thấy Số điện thoại" — tra nhật ký kiểm toán cho biết đúng người đổi, thời điểm đổi, và giá trị trước/sau, giải quyết sự cố mà không cần đoán hay truy vấn database.

---

## 7. Giới hạn hiện tại của sản phẩm

Mục này nêu rõ ranh giới hiện tại để tránh kỳ vọng sai, không phải danh sách lỗi (lỗi/khiếm khuyết kỹ thuật được theo dõi ở tài liệu audit riêng, xem mục 1.5):

1. **Không phải nền tảng tạo đối tượng tùy ý.** Quản trị viên tùy biến được *trường* trên 5 đối tượng có sẵn, nhưng không tự tạo được một *loại đối tượng* hoàn toàn mới, cũng chưa có công cụ thiết kế quan hệ dữ liệu tùy ý giữa các đối tượng.
2. **Chiến dịch marketing (Campaign) chưa nằm trong phạm vi cấu hình của Object Manager** — không có trường tùy biến, không có phân quyền trường theo nhóm cho Campaign qua khu vực này.
3. **Một số kiểu trường nhạy cảm không hỗ trợ dùng để lọc/gộp nhóm trong báo cáo** (trường mã hóa, trường công thức, trường tệp đính kèm, trường dữ liệu dạng JSON) — đây là giới hạn có chủ đích để tránh việc dùng báo cáo như một cách gián tiếp dò ra giá trị thật của dữ liệu nhạy cảm, không phải thiếu sót cần bổ sung.
4. **Bảng danh sách trường tùy biến chưa tối ưu cho tenant có rất nhiều trường** (gần ngưỡng 300 trường/đối tượng) — hiện hiển thị theo từng đợt, chưa phải giải pháp phân trang hoàn chỉnh phía máy chủ.
5. **Một vài mục trong Cấu hình nâng cao (FEAT-09) hiện chỉ là giao diện minh họa, chưa có tác động thực tế** khi lưu (ví dụ một số nút "Thêm loại mới"/"Thêm vai trò mới" ở cấu hình Tài khoản/Liên hệ) — quản trị viên không nên dựa vào các mục này cho đến khi được xác nhận hoàn thiện.
6. **Một danh sách phân đoạn khách hàng (segment) đã lưu giữ nguyên định nghĩa gốc**, không tự tính lại theo phân quyền trường của từng người xem sau này — vì một phân đoạn được hiểu là một tập khách hàng cố định, không phải "mỗi người xem thấy một tập khác nhau".
7. **Validation liên trường (cross-field) chưa được hỗ trợ.** Quy tắc kiểm tra dữ liệu (FEAT-04) hiện chỉ áp dụng trên một trường độc lập (không để trống / đúng định dạng / nằm trong khoảng). Các điều kiện phụ thuộc giữa nhiều trường (ví dụ: "nếu Trạng thái = Đã chốt thì Lý do chốt không được để trống", hoặc "Ngày kết thúc phải sau Ngày bắt đầu") **chưa được hỗ trợ** ở phiên bản này. Đây là nhu cầu thật đã được ghi nhận qua review, nhưng cần một vòng thiết kế riêng (toán tử so sánh, cách cấu hình điều kiện, thứ tự áp dụng khi nhiều quy tắc phụ thuộc nhau) trước khi đưa vào — không đặc tả trong tài liệu này.
8. **"Danh sách hiển thị" (FEAT-08) là cấu hình dùng chung do Admin quản lý, không phải view cá nhân của từng người dùng.** Nếu người dùng cuối có thể tự lưu bộ lọc/cột hiển thị riêng cho mình, đó là một khả năng khác nằm ngoài khu vực Object Manager và ngoài phạm vi tài liệu này (xem Mục 1.2).
9. **Nhật ký kiểm toán cấu hình (FEAT-10) chưa có màn hình tự tra cứu cho tenant admin ở phiên bản này** — chỉ đội vận hành/hỗ trợ tra cứu được qua công cụ nội bộ. Màn hình lịch sử thay đổi cho chính tenant admin tự xem là hướng mở rộng hợp lý cho phiên bản sau, không phải cam kết của phiên bản này.
10. **Hạn mức 300 trường tùy biến/đối tượng là giới hạn kỹ thuật đồng nhất, chưa gắn với mô hình gói dịch vụ (subscription tier).** Việc phân biệt hạn mức theo gói (entitlement) là quyết định kinh doanh chưa được đưa vào phạm vi tài liệu này — xem BR-02.7.
