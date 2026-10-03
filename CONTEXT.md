# CRM Product Glossary

Thuật ngữ nghiệp vụ dùng chung cho các SRS/spec trong `product-management`, được chốt trong quá trình viết và review các tài liệu đó. Đây là nguồn canonical — các SRS chỉ trích lại phần liên quan trong mục "Thuật ngữ" của mình, không định nghĩa lại khác đi.

## Object Manager

**Nhóm quyền (Group)**:
Một nhóm người dùng do quản trị viên tenant tạo ra để gán cấu hình chung (ví dụ phân quyền trường, danh sách hiển thị). Một người dùng có thể thuộc nhiều nhóm cùng lúc.
_Avoid_: Role, permission group (khác Vai trò/Role hệ thống — nhóm ở đây là đối tượng do tenant admin tự tạo, không phải vai trò cấp hệ thống).

**Phân quyền trường (Field-Level Security – FLS)**:
Cơ chế quyết định một người được làm gì với một trường — áp dụng theo Nhóm quyền, độc lập với quyền truy cập cả bản ghi. Gồm **hai chiều độc lập**: Mức truy cập (Xem & Sửa / Chỉ xem / Ẩn) và Mức hiển thị giá trị (Hiện đầy đủ / Che một phần / Che hoàn toàn), hợp nhất độc lập trên từng chiều — xem [ADR-0001](./docs/adr/0001-group-policy-conflict-resolution.md), mục Bổ sung 2026-08-23 (đã duyệt 2026-08-24).
_Avoid_: Permission, ACL (ACL kiểm soát bản ghi; FLS kiểm soát trường bên trong bản ghi — hai khái niệm khác nhau).

**Chính sách phân giải xung đột nhóm quyền (Group Policy Conflict Resolution)**:
Quy tắc quyết định cấu hình nào thắng khi một người dùng thuộc nhiều nhóm có cấu hình khác nhau trên cùng một trường. Chiến lược đang áp dụng là **hạn chế thắng (deny-override)** cho thuộc tính bảo mật, áp dụng độc lập trên từng chiều của FLS; và **cộng gộp (additive)** cho thuộc tính chất lượng dữ liệu (bắt buộc nhập) — nhưng cộng gộp **không** áp dụng khi người dùng không có quyền nhập chính trường đó, khi ấy ràng buộc bắt buộc được miễn trừ và bản ghi bị gắn cờ thiếu dữ liệu. Ngoài ra, **vắng mặt cấu hình không phải là sự cho phép**, và quy tắc này không áp cho tác vụ tự động (được miễn trừ FLS) lẫn quản trị viên tenant (không bị FLS giới hạn). Xem [ADR-0001](./docs/adr/0001-group-policy-conflict-resolution.md) — cả phần gốc lẫn bốn điều khoản bổ sung 2026-08-23 (mô hình hai chiều, miễn trừ ràng buộc bắt buộc, vắng mặt cấu hình, phạm vi chủ thể) đều đã duyệt và có hiệu lực ngang nhau.
_Avoid_: "Additive permissions" dùng chung cho mọi thuộc tính — trong hệ thống này hai nhóm thuộc tính có chiến lược hợp nhất khác nhau, không nên gộp chung một tên.

**Danh sách hiển thị dùng chung (Shared List View)**:
Cấu hình bảng danh sách bản ghi (cột, thứ tự) do quản trị viên tạo và gán cho một hay nhiều Nhóm quyền, quản lý trong Object Manager.
_Avoid_: "List view" trống không kèm tính từ — dễ nhầm với Bộ lọc/view cá nhân.

**Bộ lọc/view cá nhân (Personal View)**:
Bộ lọc hoặc bộ cột hiển thị do một người dùng cuối tự lưu cho riêng mình, độc lập với Danh sách hiển thị dùng chung. Nằm ngoài phạm vi cấu hình của Object Manager.
_Avoid_: List view (dễ nhầm với Danh sách hiển thị dùng chung — luôn phân biệt rõ "dùng chung" vs "cá nhân").

**Nhật ký kiểm toán cấu hình (Configuration Audit Trail)**:
Bản ghi tự động lưu vết ai đã thay đổi cấu hình gì trong Object Manager và vào lúc nào, dùng để điều tra sự cố (ví dụ một nhóm người dùng đột nhiên mất quyền xem một trường).
_Avoid_: "Activity log", "history" chung chung — dùng đúng tên này để phân biệt với log hoạt động nghiệp vụ khác (ví dụ lịch sử thay đổi trên một bản ghi Deal).

**Bắt buộc khi tạo mới (Required on Create)**:
Trường bắt buộc phải có giá trị ngay lúc tạo bản ghi — tập con của "bắt buộc", loại trừ các trường chỉ có ý nghĩa/bắt buộc sau khi bản ghi đã tồn tại.
_Avoid_: "Required" dùng lẫn cho cả hai trường hợp mà không phân biệt thời điểm áp dụng.

## IAM & Phân quyền Workspace

Nguồn: [`srs/iam-tenant-authorization.md`](./srs/iam-tenant-authorization.md) v5 Mục 1.4.

**Thành viên / Trạng thái thành viên**:
Quan hệ giữa một tài khoản (duy nhất theo email toàn hệ thống) và một workspace; mọi thuộc tính phân quyền gắn với thành viên. Trạng thái: Đang chờ chấp nhận / Chờ duyệt gia nhập / Đang hoạt động / Tạm ngưng / Đã rời.
_Avoid_: "Vô hiệu hoá người dùng" — dùng Tạm ngưng (giữ cấu hình, chặn truy cập trong workspace) hoặc Gỡ (Đã rời). Khoá tài khoản là thao tác toàn hệ thống của nhà cung cấp.

**Cấp bậc thành viên (Membership Tier)** vs **Vai trò (Role)**:
Hai trục độc lập. Cấp bậc (Chủ sở hữu / Quản trị viên / Thành viên) quyết định có toàn quyền hay không; Vai trò là ma trận ô cộng các quyền quản trị, chỉ có ý nghĩa ở cấp Thành viên. **Người có toàn quyền** là cách gọi chung Chủ sở hữu và Quản trị viên.
_Avoid_: Dùng lẫn "vai trò" để chỉ cả hai trục; coi "Quản trị viên" là một vai trò.

**Mức truy cập (Access Level)**:
Giá trị của một ô (loại dữ liệu, thao tác) trong ma trận của Vai trò: Không có / Chỉ của mình / Đơn vị của mình (của mình + cấp dưới trực tiếp và gián tiếp + Đơn vị chính và kiêm nhiệm) / Đơn vị và các đơn vị con / Toàn workspace. Thao tác Tạo chỉ có Có / Không có. Mỗi thao tác dùng mức của chính ô đó; mức của thao tác khác không rộng hơn mức Xem. Xem `FEAT-34`, `BR-35.7`, [ADR-0009](./docs/adr/0009-access-level-per-action-and-record-type.md).
_Avoid_: "Phạm vi dữ liệu của vai trò" như một giá trị duy nhất; gắn cứng quyền cho một vai trò có tên.

**Mức nền & Công khai đọc**:
Mức workspace dùng cho ô mà không vai trò nào khai báo; công khai đọc là nguồn nới phạm vi chỉ cho thao tác Xem. Nới rộng chỉ Người có toàn quyền làm được. Xem `FEAT-34`.

**Năng lực quyền hạn (trần)**:
Toàn bộ quyền hiệu lực của một người, dùng làm trần khi người đó làm tăng quyền của bất kỳ ai. Khi làm trần cho lượt cấp vĩnh viễn thì không tính quyền tạm thời. Xem Nguyên tắc 1.
_Avoid_: "Ceiling" trong văn bản nghiệp vụ.

**Thu hẹp / Nới rộng / Trung tính**:
Phân loại thao tác thay đổi quyền. Gỡ một hạn chế là nới rộng. Thao tác thu hẹp có hiệu lực từ thao tác kế tiếp và không bao giờ bị chặn bởi sự cố nhật ký (ghi bù, [ADR-0010](./docs/adr/0010-revocation-not-blocked-by-audit-failure.md)).

**Thứ tự hợp nhất quyền**:
Mức theo ô → nguồn nới phạm vi (công khai đọc, phụ trách đơn vị, chính sách Cho phép, lượt cấp trên bản ghi; không mở ô Không có) → nguồn chặn (chính sách Từ chối, lượt chặn, Sàn bắt buộc) → phân quyền trường và che. Xem `BR-39.6`.

**Sàn bắt buộc**:
Ràng buộc không cấu hình vượt được qua vai trò, nhóm, lượt cấp hay điều chỉnh — kể cả với Người có toàn quyền khi sàn nêu rõ. Do SRS phân hệ khai báo hoặc doanh nghiệp tự đặt; hệ thống không tự duy trì quy định pháp lý của quốc gia nào.

**Quản lý trực tiếp** vs **Người phụ trách đơn vị**:
Quản lý trực tiếp là cấp trên được khai báo của một thành viên; chuỗi quản lý xác định cấp dưới. Người phụ trách đơn vị là người phụ trách chính hoặc đồng phụ trách một đơn vị, có thể được xem toàn nhánh khi `CFG-34-02` bật.
_Avoid_: Suy quản lý trực tiếp từ cây đơn vị.

**Đơn vị chính / Đơn vị kiêm nhiệm**:
Mỗi thành viên có tối đa một Đơn vị chính và nhiều Đơn vị kiêm nhiệm (mặc định Chỉ xem: chỉ nới thao tác Xem và nhận việc từ hàng đợi; Đầy đủ: mọi thao tác). Bản ghi thuộc đơn vị theo Người phụ trách hiện tại (qua Đơn vị chính hoặc kiêm nhiệm Đầy đủ). Ngoại lệ: **bản ghi công việc** của hàng đợi (vé, hội thoại) thuộc đơn vị tiếp nhận suốt vòng đời; **bản ghi chờ phân công** (khách hàng tiềm năng từ biểu mẫu) thuộc đơn vị tiếp nhận tới khi có người phụ trách; đơn vị tiếp nhận mặc định theo loại nguồn do Người có toàn quyền đặt (`BR-35.10` – `BR-35.12`).

**Vai trò gợi ý của đơn vị / Chế độ Cơ bản**:
Vai trò được chọn sẵn khi mời hoặc chuyển người vào đơn vị (`BR-21.4`). Chế độ Cơ bản cho mỗi loại dữ liệu chọn một mức đặt sẵn — Không truy cập / Chỉ xem đơn vị / Chỉ dữ liệu của mình / Xem đơn vị, sửa của mình / Quản lý dữ liệu đơn vị / Toàn quyền (`BR-25.4`).

**Quyền Ủy thác (Delegated Grant Authority)**:
Quyền do Người có toàn quyền cấp trực tiếp cho từng người, kèm danh sách vai trò được giao; người giữ gán được các vai trò trong danh sách (và thêm vào nhóm chỉ mang chúng) vượt năng lực của chính mình. Không truyền tiếp, không nằm trong vai trò, không áp cho cấp bậc, quyền tạm thời hay tạo vai trò. Xem `FEAT-45`, [ADR-0002](./docs/adr/0002-delegated-grant-authority-ceiling-exception.md).
_Avoid_: Nhầm với Quyền tạm thời (có hạn, cần phê duyệt).

**Chức danh trách nhiệm & Người duyệt thứ hai**:
Chức danh (Người phụ trách Bảo vệ Dữ liệu, Người phụ trách thanh toán, Người phụ trách bảo mật) không tự cấp quyền. Người duyệt thứ hai luôn khác người thực hiện; thiếu người mang chức danh thì là một Người có toàn quyền khác. Xem `FEAT-47`.

**Phiên hỗ trợ của nhà cung cấp**:
Cách duy nhất nhân sự vận hành nền tảng vào dữ liệu một workspace: có thời hạn, có mức do doanh nghiệp chọn, ghi vào nhật ký của workspace, không bao giờ đổi cấu hình quyền. Xem `FEAT-08`.

**Liên kết mời / Tự gia nhập theo tên miền**:
Hai cách đưa nhiều người vào nhanh; mặc định cần duyệt, chịu trần năng lực của người tạo, đi theo người tạo. Tự gia nhập chỉ với tên miền email đã xác minh. Xem `FEAT-50`.

**Tách biệt nhiệm vụ / Rà soát quyền định kỳ**:
Cặp quyền không được cùng nằm trong tay một người (không áp lên Người có toàn quyền); đợt rà soát giao cho quản lý xác nhận Giữ / Thu hồi, kết quả là biên bản không sửa được. Xem `FEAT-46`, `FEAT-48`.

**Đọc ngoài phạm vi phụ trách**:
Lượt xem được phép chỉ nhờ mức Xem rộng hơn mức Sửa của chính người đó; phân hệ ghi nhật ký sự kiện này. Xem `BR-35.9`.

**Nhóm**:
Tập thành viên do doanh nghiệp tạo để cấp vai trò tập thể và gán cấu hình Object Manager; cùng thực thể với "Nhóm quyền" của Object Manager. Không phải sơ đồ tổ chức.

**Nguyên tắc đóng cho Nhật ký cấu hình quyền (Closure Rule)**:
Mọi thao tác thay đổi ai-được-làm-gì hoặc ai-thấy-gì mặc định thuộc nhật ký thay đổi cấu hình quyền (thời hạn lưu `CFG-41-02`), trừ xem trước, mô phỏng và chỉ đọc. Đóng khi lỗi cho thao tác nới rộng và trung tính; ghi bù cho thao tác thu hẹp. Xem `BR-41.4` – `BR-41.6`, [ADR-0003](./docs/adr/0003-permission-config-audit-log-fail-closed.md), [ADR-0010](./docs/adr/0010-revocation-not-blocked-by-audit-failure.md).
_Avoid_: Coi danh sách ví dụ ở `BR-41.4` là danh sách đóng kín.

## Omnichat

**Hồ sơ khách hàng tạm (Provisional Customer Record)**:
Một hồ sơ khách hàng do hệ thống tự tạo ngay khi nhận tin nhắn từ một người gửi chưa xác định được danh tính CRM thật (chưa khớp email/số điện thoại/liên kết thủ công nào). Được thay thế bằng liên kết tới hồ sơ khách hàng thật khi có đủ căn cứ. Chuẩn rủi ro áp dụng thống nhất cho mọi kênh: chỉ được **tự động** gộp khi định danh người gửi do chính nền tảng kênh xác minh là của một cá nhân (hiện chỉ WhatsApp) **và** định danh đó chưa được đánh dấu là Định danh dùng chung; mọi trường hợp còn lại chỉ hiển thị gợi ý để Agent xác nhận. Mọi lần gộp đều phải gỡ lại được.
_Avoid_: "Shadow contact", "khách vãng lai" — dùng đúng tên này để nhất quán giữa các SRS omnichannel.

**Định danh dùng chung (Shared Identifier)**:
Một số điện thoại hoặc email được đánh dấu là không thuộc riêng một cá nhân — số tổng đài của đại lý, máy bàn văn phòng, email chung của một phòng ban. Không bao giờ được dùng làm căn cứ tự động gộp hồ sơ khách hàng, kể cả trên kênh có định danh xác thực mạnh.
_Avoid_: "Số chung", "số công ty" — dùng đúng tên này vì nó là một trạng thái do người dùng chủ động đánh dấu, không phải một suy đoán của hệ thống.

**Ngưỡng an toàn đóng hàng loạt (Bulk-Close Safety Threshold)**:
Cơ chế giới hạn số lượng hội thoại được tự động đóng trong một khoảng thời gian ngắn của một tenant/kênh, để một lỗi cấu hình hoặc sự cố kênh không âm thầm đóng hàng loạt hội thoại đang cần xử lý. Khi chạm ngưỡng, việc đóng bị hoãn lại và được thử lại sau, không hủy bỏ.
_Avoid_: "Circuit breaker", "rate limit" — đây là khái niệm nghiệp vụ (bảo vệ khách hàng khỏi bị đóng hội thoại oan), không phải thuật ngữ hạ tầng.

**Lời mời nhận hội thoại (Conversation Offer)**:
Một đề nghị có thời hạn gửi tới một Agent cụ thể để nhận xử lý một hội thoại đang chờ. Agent phải phản hồi (nhận/từ chối) trong thời hạn; hết hạn hoặc từ chối thì hội thoại được mời tới Agent phù hợp tiếp theo.
_Avoid_: "Offer/lease", "work item" — dùng "Lời mời nhận hội thoại" trong mọi tài liệu nghiệp vụ.

**Ưu tiên người phụ trách trước đó (Previous-Assignee Priority / Sticky Routing)**:
Quy tắc ưu tiên định tuyến hội thoại mới của một khách hàng trở lại đúng Agent đã từng phụ trách khách hàng đó gần đây, nếu Agent đó còn khả năng nhận thêm việc — nhằm giữ mạch tương tác quen thuộc cho khách hàng.
_Avoid_: "Sticky assignee/sticky routing" trong văn bản nghiệp vụ — chỉ dùng trong tài liệu kỹ thuật.

**Thuộc tính kênh (Channel Attributes)**:
Tập đặc điểm nghiệp vụ mà mỗi kênh khai báo khi được kết nối: trò chuyện đồng thời hay không đồng thời, có áp Cửa sổ phản hồi hay không, có tin nhắn mẫu được nền tảng phê duyệt trước hay không, định danh người gửi có được nền tảng xác minh là của một cá nhân hay không, và khả năng hiển thị tin nhắn dạng nút bấm. Mọi quy tắc nghiệp vụ trong SRS đều viện dẫn thuộc tính, không viện dẫn tên kênh — để thêm một kênh mới không phải sửa quy tắc.
_Avoid_: Liệt kê tên kênh ("hiện chỉ WhatsApp", "gộp chung Facebook, Instagram, Zalo...") bên trong một quy tắc nghiệp vụ — danh sách kênh thay đổi, quy tắc thì không nên.

**Lý do xử lý (Disposition)**:
Mã phân loại kết quả một hội thoại, do doanh nghiệp tự định nghĩa theo phân cấp và Agent chọn khi giải quyết. Là căn cứ duy nhất để trả lời câu hỏi "khách hàng liên hệ vì chuyện gì" trong báo cáo. Khác **Nhãn (Tag)** ở chỗ mỗi hội thoại chỉ có một lý do xử lý ở mỗi chu kỳ giải quyết, còn nhãn thì gắn được nhiều và gắn được bất cứ lúc nào.
_Avoid_: "Lý do đóng", "kết quả" chung chung — và không dùng lẫn với **lý do chuyển tiếp**, vốn là một danh mục riêng.

**Xử lý sau hội thoại (Wrap-up)**:
Khoảng thời gian một Agent hoàn tất phần việc còn lại của hội thoại vừa kết thúc (chọn lý do xử lý, ghi chú, cập nhật hồ sơ khách hàng) trước khi được phân công việc mới. Là thời gian làm việc thật, phải tính vào thời gian xử lý trung bình.
_Avoid_: Coi đây là thời gian nghỉ — nếu không tách thành một trạng thái riêng, việc mới sẽ ập tới ngay khi Agent vừa đóng hội thoại cũ và phần ghi nhận bị bỏ qua.

**Tỷ lệ khách bỏ cuộc (Abandonment Rate)**:
Tỷ lệ khách hàng chủ động rời đi trước khi có Agent nào tiếp nhận hội thoại của họ. Cùng với tỷ lệ đáp ứng cam kết thời gian phản hồi, đây là hai chỉ số cốt lõi đo năng lực phục vụ — vì tỷ lệ đáp ứng chỉ nói về những khách đã được phục vụ.
_Avoid_: Gộp khách bỏ cuộc vào nhóm "hội thoại không hoạt động rồi tự đóng" — như vậy một doanh nghiệp thiếu người vẫn nhìn thấy chỉ số đẹp.

**Tạm dừng xóa theo yêu cầu pháp lý (Legal Hold)**:
Trạng thái đặt lên dữ liệu của một hội thoại hoặc một khách hàng đang trong diện tranh chấp/điều tra, làm ngưng hiệu lực của cả thời hạn lưu trữ lẫn yêu cầu xóa dữ liệu cá nhân, cho tới khi được gỡ. Mỗi lần đặt/gỡ phải ghi rõ ai đặt và căn cứ gì.
_Avoid_: Coi đây là một ngoại lệ kỹ thuật của chính sách lưu trữ — đây là một quyết định có chủ thể chịu trách nhiệm, phải soi lại được.

**Cửa sổ phản hồi (Reply Window)**:
Khoảng thời gian, tính từ tin nhắn gần nhất của khách hàng, mà Agent còn được phép chủ động gửi tin nhắn tự do cho khách trên một kênh nhắn tin. Sau khi hết cửa sổ, một số kênh (ví dụ WhatsApp) chỉ cho gửi tin dạng mẫu đã được phê duyệt trước; một số kênh khác (Email, Live Chat, Telegram) không có giới hạn này.
_Avoid_: "Reply window" tiếng Anh trong văn bản nghiệp vụ tiếng Việt — dùng "Cửa sổ phản hồi".

## Billing & Subscription

**Hồ sơ thanh toán (Billing Account)**:
Danh tính thương mại của một doanh nghiệp — tên pháp lý, quốc gia, địa chỉ xuất hóa đơn, mã số thuế, tiền tệ, múi giờ cắt kỳ và người phụ trách thanh toán. Là cầu nối duy nhất giữa danh tính vận hành (workspace) và danh tính người mua. Mỗi doanh nghiệp có đúng một hồ sơ; tiền tệ và múi giờ cắt kỳ cố định trong suốt vòng đời đăng ký.
_Avoid_: Dùng lẫn "tenant"/"workspace"/"customer" khi nói về bên trả tiền — workspace là nơi làm việc, hồ sơ thanh toán là bên mua; hai thứ có vòng đời và quy tắc phân quyền khác nhau.

**Gói cước (Plan)** và **Phiên bản gói (Plan Version)**:
Gói cước là tập điều kiện thương mại được bán (phí thuê bao theo chu kỳ, hạn mức bao gồm theo từng loại tiêu dùng, đơn giá vượt). Phiên bản gói là một ảnh chụp bất biến của tập điều kiện đó — mỗi lần thay đổi giá hay hạn mức tạo ra một phiên bản mới, và doanh nghiệp đã đăng ký giữ nguyên phiên bản họ đã ký. Giá thương lượng riêng cho một doanh nghiệp cũng biểu diễn dưới dạng một phiên bản gói riêng, không sửa đè lên gói niêm yết.
_Avoid_: "Đổi giá gói" như một thao tác đơn lẻ — trong hệ thống này không có thao tác đó, chỉ có "phát hành phiên bản mới".

**Loại tiêu dùng tính phí (Billable Usage Type)**:
Một thứ đo được và tính tiền theo mức sử dụng, có mã định danh ổn định (ví dụ hội thoại được tạo, tin nhắn mẫu được gửi). Mỗi loại khai báo đơn vị đo, cách gộp trong kỳ và thời điểm tính phí. Mã đã phát hành không bao giờ đổi tên và không tái sử dụng; đổi bản chất đo lường bắt buộc tạo mã mới, vì mọi hóa đơn lịch sử đều tham chiếu tới mã cũ.
_Avoid_: "Metric", "usage code" trong văn bản nghiệp vụ tiếng Việt; và tránh gọi tên một loại cụ thể bên trong một quy tắc — quy tắc luôn viện dẫn thuộc tính của loại tiêu dùng, để thêm loại mới không phải sửa quy tắc.

**Sự kiện tính phí (Billing Event)**:
Bản ghi chỉ-thêm-mới do một module nghiệp vụ phát sinh khi một việc đáng tính phí xảy ra, gồm doanh nghiệp, loại tiêu dùng, mã tham chiếu tới đối tượng gốc, số lượng và thời điểm phát sinh. Là ranh giới duy nhất giữa nghiệp vụ CRM và lớp thương mại: module nghiệp vụ không biết gì về giá, hạn mức hay hóa đơn. Sự kiện đã ghi không sửa và không xóa.
_Avoid_: Coi đây là một bản ghi kỹ thuật nội bộ — nó là chứng cứ để trả lời khiếu nại hóa đơn, nên vòng đời và thời hạn lưu của nó là cam kết nghiệp vụ. Xem [ADR-0004](./docs/adr/0004-billing-engine-boundary.md).

**Thời điểm tính phí (Billable Moment)**:
Khoảnh khắc chính xác một việc trở thành đáng tính phí, định nghĩa riêng cho từng loại tiêu dùng, bằng ngôn ngữ nghiệp vụ và kiểm chứng được từ bên ngoài. Ví dụ: một tin nhắn mẫu được tính khi nền tảng kênh xác nhận đã tiếp nhận để gửi — không phải khi Agent bấm gửi. Một loại tiêu dùng chưa có định nghĩa này thì chưa được phát hành để bán.
_Avoid_: Mô tả mơ hồ kiểu "khi gửi tin nhắn" — mỗi từ mơ hồ ở đây là một khiếu nại hóa đơn về sau.

**Bút toán đảo (Reversal)**:
Cách duy nhất để hủy hiệu lực một sự kiện tính phí đã ghi nhận — tạo một bản ghi ngược tham chiếu tới sự kiện gốc, giữ nguyên lịch sử và thay đổi kết quả. Đảo trong kỳ chưa chốt làm giảm trực tiếp tiêu dùng của kỳ; đảo sau khi hóa đơn đã phát hành phải đi qua chứng từ ghi có. Căn cứ đảo là một danh sách đóng, không phải quyết định tùy tình huống.
_Avoid_: "Sửa lại"/"xóa sự kiện" — hai thao tác này không tồn tại trong hệ thống.

**Hạn mức bao gồm (Included Quota)** và **Chính sách chạm trần (Quota Policy)**:
Hạn mức bao gồm là lượng tiêu dùng đã nằm trong phí thuê bao của một kỳ, không cộng dồn sang kỳ sau trừ khi gói ghi rõ. Chính sách chạm trần là điều xảy ra khi dùng hết: chặn, cho vượt và tính phí, hoặc cho vượt tới trần cứng rồi chặn. Mỗi hạn mức gắn với đúng một chính sách — sản phẩm không có hành vi mặc định ngầm.
_Avoid_: Chặn theo chính sách này ở chiều tiếp nhận hoạt động do khách hàng của doanh nghiệp khởi xướng — chặn chỉ áp cho hành vi doanh nghiệp chủ động thực hiện, nếu không việc kiểm soát chi phí bị đổ lên đầu một bên thứ ba vô can.

**Trần chi phí vượt (Overage Ceiling)**:
Mức phí vượt tối đa của một doanh nghiệp trong một kỳ, mặc định tính theo bội số của phí thuê bao. Nó có hai cách vận hành tùy nguồn phát sinh tiêu dùng: với tiêu dùng do doanh nghiệp chủ động tạo ra, đây là **trần chặn** — chạm trần thì dừng và chờ doanh nghiệp xác nhận; với tiêu dùng do khách hàng của doanh nghiệp khởi xướng, chặn bị cấm nên đây là **trần tính tiền** — hoạt động vẫn tiếp nhận và vẫn ghi nhận, nhưng phần vượt trần không vào hóa đơn khi chưa có xác nhận và trở thành chi phí nhà cung cấp tự chịu, phải đo và báo cáo cùng giá vốn tiêu dùng.
_Avoid_: Nói "trần chi phí vượt" như một cơ chế duy nhất — nói vậy là ngầm hứa một mức chặn mà ở chiều tiếp nhận hệ thống không có quyền thực thi, và biến một khoản lỗ có thật của nhà cung cấp thành một con số không ai theo dõi. Xem BR-16.5 và BR-16.8 trong [`billing-subscription-srs.md`](./srs/billing-subscription-srs.md).

**Đình chỉ dịch vụ (Suspension)**:
Trạng thái hạn chế một doanh nghiệp sau khi chu trình nhắc nợ kết thúc mà chưa thu được tiền. Chỉ chặn các hành vi chủ động phát sinh chi phí mới (gửi tin đi, chạy chiến dịch, thêm người dùng); vẫn tiếp nhận và lưu hoạt động do khách hàng của doanh nghiệp khởi xướng, vẫn cho xuất dữ liệu, không xóa dữ liệu, và khôi phục tự động ngay khi thanh toán thành công.
_Avoid_: Coi đình chỉ là "khóa tài khoản" — giữ dữ liệu làm con tin không phải công cụ thu nợ, và nó biến tranh chấp về tiền thành tranh chấp pháp lý.

**Người phụ trách thanh toán (Billing Contact)**:
Vai trò trong một doanh nghiệp chịu trách nhiệm về hóa đơn, phương thức thanh toán và khiếu nại. Có thể là một người không tham gia vận hành CRM hằng ngày. Quyền xem dữ liệu tài chính của vai trò này **không** kéo theo quyền đọc nội dung nghiệp vụ: khi đối chiếu một dòng hóa đơn, họ thấy số lượng và định danh đối tượng nhưng không đọc được nội dung hội thoại.
_Avoid_: Gộp vai trò này vào Chủ workspace — chúng có thể là hai người, và hệ thống không bao giờ được ở trạng thái không có ai nhận thông báo về tiền.

## Onboarding & Khởi tạo Không gian làm việc

Nguồn: [`srs/onboarding-srs.md`](./srs/onboarding-srs.md) v3 Mục 1.4.

**Đăng ký tự phục vụ** vs **Tạo hộ**:
Hai kênh tạo workspace. Tự phục vụ: người đăng ký xác minh email rồi tạo workspace và trở thành Chủ sở hữu. Tạo hộ: nhà cung cấp tạo theo hợp đồng; Chủ sở hữu do khách hàng chỉ định bằng văn bản, ở trạng thái Đang chờ chấp nhận; có thể có giai đoạn triển khai trước kích hoạt (chỉ cấu hình, không dữ liệu, không gửi lời mời).

**Đăng ký dở dang**:
Tài khoản đã tạo nhưng chưa hoàn tất workspace. Được nhắc rồi dọn sau thời hạn nền tảng `PLT-02`; không dọn nếu đã là thành viên workspace khác. Tài khoản chưa xác minh không giữ chỗ email.
_Avoid_: "Tài khoản mồ côi".

**Tên miền phụ / Giữ chỗ tên miền phụ / Tên miền riêng / Tên miền email đã xác minh**:
Tên miền phụ: địa chỉ của workspace dưới tên miền nền tảng, duy nhất, được giữ chỗ trong lúc đăng ký. Tên miền riêng: tên miền của doanh nghiệp trỏ về workspace, phải chứng minh sở hữu, có trạng thái Mất xác minh. Tên miền email đã xác minh: tên miền email (congty.vn) mà workspace đã chứng minh sở hữu, mở tự gia nhập và nhận diện doanh nghiệp trong thư mời.
_Avoid_: Dùng lẫn tên miền riêng (để truy cập) với tên miền email (để nhận diện người).

**Khởi tạo / Sẵn sàng**:
Khởi tạo tạo workspace toàn vẹn; Sẵn sàng khi đủ Chủ sở hữu, khung phân quyền, trạng thái thương mại, tên miền phụ, dịch vụ đi kèm bắt buộc. Thất bại thì dọn sạch, giải phóng tên miền phụ. Cấu hình theo ngành và dữ liệu mẫu không thuộc điều kiện Sẵn sàng.

**Mẫu ngành / Mẫu đội ngũ**:
Mẫu ngành: phễu, quy trình vé và dữ liệu mẫu cho một ngành; áp về sau chỉ thêm, không sửa dữ liệu đang có. Mẫu đội ngũ: đội gợi ý theo quy mô và mục tiêu, mỗi đội là một đơn vị tổ chức kèm vai trò gợi ý.

**Dữ liệu mẫu**:
Bản ghi minh hoạ mang nhãn Mẫu, không tính vào báo cáo, hạn mức, tiêu dùng và không kích hoạt gửi ra ngoài; xoá được một lần, có lựa chọn giữ lại bản ghi mẫu đã sửa hoặc đã gắn với dữ liệu thật.

**Thiết lập đội ngũ nhanh**:
Tối đa ba màn hình ở lần đăng nhập đầu: đội và cách thấy dữ liệu → dán email theo đội, trưởng nhóm, đồng quản trị → xem lại và gửi. Là lối tắt dùng đúng quy tắc của IAM.

**Lộ trình thiết lập nhanh / Đạt kích hoạt sử dụng**:
Nhiệm vụ đầu tiên, đánh dấu theo kết quả nghiệp vụ, đội ngũ đứng đầu. Đạt kích hoạt sử dụng: ≥ 3 thành viên Đang hoạt động (gồm Chủ sở hữu), có kênh hoặc khách hàng thật, có cơ hội hoặc vé thật.
_Avoid_: Dùng "kích hoạt" trơn — luôn nói rõ kích hoạt Chủ sở hữu, kích hoạt tên miền hay Đạt kích hoạt sử dụng.

**Thông điệp hướng dẫn kích hoạt**:
Email hướng dẫn theo hành vi, mốc cuối tương đối với ngày hết dùng thử, gửi theo giờ và lịch làm việc của workspace, huỷ nhận được. Khác với thông báo bắt buộc trước khi hết dùng thử của billing.

**Tham số nền tảng (PLT) vs Tham số workspace (CFG)**:
PLT là chính sách của nhà cung cấp, áp chung, doanh nghiệp không đổi được; CFG do doanh nghiệp cấu hình.

**Dùng thử**:
Thời hạn, hạn mức và chính sách dùng thử thuộc [`srs/billing-subscription-srs.md`](./srs/billing-subscription-srs.md) `FEAT-05` (tham số theo chiến dịch); onboarding chỉ hiển thị trạng thái.
_Avoid_: "Dùng thử 14 ngày" như một hằng số.

## Quản lý Khách hàng & Danh bạ (Contacts & Accounts)

**Khách hàng Cá nhân (Contact)**:
Thực thể đại diện cho một con người cụ thể trong CRM (khách hàng tiềm năng, người liên hệ của doanh nghiệp, người mua lẻ, đối tác). Lưu trữ thông tin định danh, các kênh liên lạc có thể tiếp cận, lịch sử tương tác và mối quan hệ với các doanh nghiệp/cá nhân khác.

**Tổ chức / Doanh nghiệp (Account)**:
Thực thể đại diện cho một pháp nhân, công ty, tập đoàn hoặc cơ quan tổ chức mà doanh nghiệp đang có quan hệ kinh doanh. Quản lý thông tin mã số thuế, ngành nghề, quy mô, doanh thu, cây cấu trúc Công ty Mẹ - Công ty Con và danh sách các nhân sự liên hệ thuộc tổ chức.

**Giai đoạn Vòng đời Khách hàng (Lifecycle Stage)**:
Trạng thái định vị mức độ gắn kết và trưởng thành của khách hàng trong hành trình chuyển đổi doanh nghiệp (chuẩn 7 giai đoạn: *Subscriber -> Lead -> Marketing Qualified Lead / MQL -> Sales Qualified Lead / SQL -> Opportunity -> Customer -> Evangelist*).

**Chuyển đổi Khách hàng Tiềm năng (Lead Qualification & Conversion)**:
Quy trình nghiệp vụ thẩm định và nâng cấp một Khách hàng tiềm năng (Lead) đã đủ điều kiện kinh doanh thành Liên hệ chính thức (Contact), tự động liên kết hoặc tạo mới Doanh nghiệp (Account) và tạo Cơ hội bán hàng (Deal) tương ứng chỉ bằng một thao tác nguyên tử.

**Nhận diện & Gộp Trùng lặp (Duplicate Detection & Merge)**:
Cơ chế tự động phát hiện các bản ghi trùng nhau (theo Email, Số điện thoại, Mã số thuế) và cho phép người dùng xem trước (Preview Merge), lựa chọn bản ghi chính (Master Record), kế thừa dữ liệu và chuyển giao toàn bộ lịch sử tương tác/bản ghi con trước khi gộp.

**Sổ cái Hoàn tác Gộp (Unmerge Ledger)**:
Cơ chế lưu trữ vết lịch sử gộp bản ghi cho phép người dùng có thẩm quyền đảo ngược hoàn toàn một giao dịch gộp trước đó (Unmerge), khôi phục lại bản ghi đã mất và phân bổ lại đúng các mối liên kết gốc.

**Mối quan hệ Đa tổ chức (Multi-Affiliations)**:
Khả năng liên kết một cá nhân (Contact) với nhiều doanh nghiệp (Accounts) khác nhau cùng lúc với các chức danh, vai trò (Chính / Phụ / Cố vấn) và khoảng thời gian công tác riêng biệt.

**Quan hệ Giữa các Cá nhân (Person Relations)**:
Liên kết mạng lưới quan hệ trực tiếp giữa hai con người trong CRM (Quản lý trực tiếp / Reports-to, Người giới thiệu / Referred-by, Thành viên gia đình / Household, Đối tác kinh doanh / Partner).

**Dòng thời gian Hoạt động 360 độ (360-Degree Unified Timeline)**:
Bảng luồng thông tin hợp nhất hiển thị toàn bộ lịch sử tương tác của khách hàng (Email, Cuộc gọi, Ghi chú, Tin nhắn đa kênh, Vé hỗ trợ, Cơ hội bán hàng, Nhiệm vụ, Lịch sử đổi giai đoạn) theo thứ tự thời gian đảo ngược.

**Điểm Tiềm năng Khách hàng (Lead Score)**:
Điểm số định lượng tự động tính toán dựa trên mức độ phù hợp hồ sơ (Profile Fit) và mức độ tương tác thực tế (Engagement Activity), có cơ chế suy giảm điểm theo thời gian (Score Decay) để ưu tiên chăm sóc các cơ hội nóng.

**Khách hàng Đã rời bỏ (Churned Customer / Former Customer)**:
Trạng thái vòng đời của một khách hàng cá nhân hoặc doanh nghiệp đã từng mua hàng nhưng sau đó hủy hợp đồng, chấm dứt gói thuê bao hoặc không còn phát sinh bất kỳ giao dịch nào trong thời gian dài. Khách hàng ở trạng thái này bị loại khỏi các chiến dịch tiếp thị thông thường và chỉ được tiếp cận qua các chiến dịch giữ chân/tái kích hoạt (Win-Back Campaigns) được phê duyệt riêng.

**Lead Bị loại (Disqualified Lead)**:
Trạng thái vòng đời dành cho các khách hàng tiềm năng không phù hợp với tiêu chí khách hàng mục tiêu (sai ngành nghề, không đủ ngân sách, thông tin liên lạc giả mạo, spam). Lead bị loại được lưu trữ để phân tích chất lượng nguồn marketing nhưng bị loại bỏ khỏi danh sách phân bổ cho nhân viên kinh doanh.

**Phân bổ Lead Tự động (Lead Routing / Auto-Assignment)**:
Quy tắc tự động gán Người phụ trách (Owner) cho các khách hàng tiềm năng mới đổ về từ các kênh số (Website form, Chatbot, Facebook Ads, API) dựa trên thuật toán chia đều vòng (Round-robin), phân chia theo vùng địa lý (Territory) hoặc chuyên môn ngành nghề (Industry specialization).

**Tham số Nguồn gốc Tiếp thị (UTM Source Tracking)**:
Tập hợp các tham số theo dõi nguồn gốc (`utm_source`, `utm_medium`, `utm_campaign`, `utm_term`, `utm_content`) được hệ thống tự động ghi nhận tại thời điểm Lead đăng ký lần đầu để phục vụ phân tích hiệu quả kênh tiếp thị (Marketing Attribution) và tính toán tỷ suất sinh lời trên chi phí (ROI).

**Trợ lý Nhập Dữ liệu Thông minh (Smart Data Import Wizard)**:
Trình nhập khẩu dữ liệu từ tệp Excel (.xlsx) hoặc CSV dung lượng lớn (tới 50MB) qua hàng đợi bất đồng bộ, có tính năng tự động nhận diện cột (Auto Field Mapping), kiểm tra tính hợp lệ từng dòng và xuất tệp báo cáo lỗi chi tiết.

**Trạng thái Đồng thuận & Khả năng Tiếp cận (Consent & Deliverability)**:
Theo dõi tình trạng đồng thuận nhận tin quảng bá/tiếp thị (Opt-in Consent theo chuẩn GDPR/Anti-spam), đánh dấu kênh chính (Primary) và trạng thái kỹ thuật của địa chỉ liên lạc (Verified / Bounced / Inactive).

## Quản lý Cơ hội & Phễu Bán hàng (Deals & Pipelines)

**Cơ hội Bán hàng (Deal / Opportunity)**:
Thực thể đại diện cho một giao dịch kinh doanh tiềm năng giữa doanh nghiệp và khách hàng cá nhân hoặc tổ chức, có giá trị tiền tệ dự kiến, ngày dự kiến đóng và gắn liền với một giai đoạn cụ thể trên phễu bán hàng.

**Phễu Bán hàng (Sales Pipeline)**:
Quy trình trực quan hóa toàn bộ các bước từ khi tiếp cận cơ hội đến khi chốt hợp đồng thành công. Một không gian làm việc có thể sở hữu nhiều phễu bán hàng độc lập cho các dòng sản phẩm, dịch vụ hoặc thị trường khác nhau.

**Giai đoạn Phễu & Xác suất Thắng (Stage & Win Probability)**:
Các cột mốc tuần tự trên phễu bán hàng, mỗi giai đoạn được gán một tỷ lệ xác suất thắng từ 0% đến 100% để tính toán doanh thu dự báo có trọng số.

**Bảng Kanban Cơ hội (Deals Kanban Board)**:
Giao diện dạng bảng thẻ kéo thả phân chia theo các cột giai đoạn bán hàng, hiển thị số lượng và tổng giá trị cơ hội trên từng cột theo thời gian thực.

**Thời gian Lưu tại Giai đoạn (Time in Stage / Stage Duration)**:
Khoảng thời gian một cơ hội đã nằm yên tại một giai đoạn cụ thể trước khi chuyển sang giai đoạn khác, dùng để phát hiện điểm nghẽn quy trình và tính toán vận tốc bán hàng.

**Cơ hội Nguội Lạnh (Stale Deal)**:
Cơ hội bán hàng đang mở không có bất kỳ tương tác nào vượt quá ngưỡng thời gian quy định, được hệ thống tự động đánh dấu cảnh báo để người phụ trách kịp thời xử lý. Ngưỡng là tham số cấu hình theo Không gian làm việc. Xem `FEAT-17` tại [`deals-pipeline-srs.md`](./srs/deals-pipeline-srs.md).
_Avoid_: Tính mọi thao tác sửa bản ghi là "có tương tác" — chỉnh sửa trường nội bộ của quản trị viên không phải là chăm sóc khách hàng, và nếu tính vào thì cơ chế cảnh báo mất hoàn toàn ý nghĩa.

**Lịch Chăm sóc Tiếp theo (Follow-up Reminder)**:
Thời điểm cam kết tương tác tiếp theo với khách hàng do người phụ trách thiết lập; hệ thống chủ động quét và nhắc khi đến hạn, đồng thời tạo một Công việc mới giao cho người phụ trách (không chỉ là một thông báo thoáng qua).

**Lý do Thất bại (Loss Reason)**:
Nội dung bắt buộc phải khai báo khi chuyển cơ hội sang Đóng Thất bại, chọn từ một **danh mục chuẩn hóa do từng Không gian làm việc tự định nghĩa** (`FEAT-20`), có thể kèm ghi chú giải thích bổ sung. Một lý do đang được dùng trên bản ghi lịch sử chỉ được vô hiệu hóa, không xóa cứng.

**Doanh thu Dự báo có Trọng số (Weighted Pipeline Forecast)**:
Doanh thu kỳ vọng của một giai đoạn hoặc toàn phễu, bằng tổng của (giá trị từng cơ hội đang mở nhân với xác suất thắng của giai đoạn đang chứa nó). Báo cáo hiện **không quy đổi tiền tệ** khi một không gian làm việc có cơ hội thuộc nhiều loại tiền tệ khác nhau — chỉ cảnh báo trộn lẫn, không tự động chuẩn hóa về một đồng tiền cơ sở (xem `BR-02.2`).
_Avoid_: Viết công thức bằng ký hiệu toán học trong văn bản nghiệp vụ — phát biểu bằng lời như trên.

**Vai trò Liên hệ trong Cơ hội (Contact Roles on Deals)**:
Khả năng gắn nhiều nhân sự liên hệ vào cùng một Cơ hội bán hàng với vai trò cụ thể trong quá trình ra quyết định mua hàng. Danh mục vai trò **do từng không gian làm việc tự định nghĩa** (dùng chung với vai trò liên hệ trên hồ sơ Khách hàng), không phải một danh sách cố định của hệ thống.
_Avoid_: Liệt kê một bộ vai trò cố định (Decision Maker/Champion/...) như thể đó là giá trị enum đóng kín của hệ thống — đó chỉ là ví dụ minh họa, tenant có thể định nghĩa khác.

**Đóng Phễu An toàn & Di chuyển Cơ hội (Pipeline Archival & Migration)**:
Quy trình đóng một phễu bán hàng không còn dùng, bắt buộc chọn đúng một trong hai phương án loại trừ lẫn nhau cho các cơ hội đang mở: di chuyển theo ma trận ánh xạ giai đoạn sang phễu khác, hoặc đóng băng chỉ đọc để bảo toàn lịch sử. **Giới hạn đã biết:** hai bước "di chuyển dữ liệu" và "đánh dấu phễu đã đóng" hiện chạy tuần tự, chưa được bọc trong một giao dịch nguyên tử duy nhất — xem `BR-07.3`.
_Avoid_: Khẳng định thao tác này có đảm bảo toàn vẹn giao dịch tuyệt đối — đây là khoảng cách kỹ thuật thật đang tồn tại, không phải giả định an toàn.

**Rào cản Giai đoạn (Stage-Gate Rules / Stage Entry Requirements)**:
Bộ điều kiện dữ liệu bắt buộc phải thỏa mãn trước khi một cơ hội được coi là đủ điều kiện ở một giai đoạn. **Phạm vi hiện tại chỉ gồm trường dữ liệu bắt buộc** (kể cả trường tùy biến), tích lũy qua mọi giai đoạn bị bỏ qua khi nhảy cóc. Yêu cầu đính kèm tài liệu bắt buộc và yêu cầu khai báo vai trò liên hệ bắt buộc là nhu cầu nghiệp vụ đã ghi nhận nhưng **chưa triển khai** — xem `BR-13.4`.
_Avoid_: Gộp khái niệm này với một quy trình phê duyệt có người ký duyệt — rào cản giai đoạn chỉ kiểm tra dữ liệu, không phải cơ chế phê duyệt (xem Nguyên tắc 2, Mục 2.4 của SRS).

**Khóa Nhảy cóc Giai đoạn (Sequential Stage Enforcement)**:
Tham số cấu hình theo từng phễu (không phải theo toàn không gian làm việc), khi bật sẽ chặn một cơ hội tiến thẳng qua nhiều giai đoạn mà bỏ qua giai đoạn ở giữa; không áp dụng khi lùi giai đoạn hoặc khi đóng cơ hội (Thắng/Thua). Mặc định tắt.

**Đóng băng Ghi dữ liệu khi Di chuyển Dữ liệu nền (Migration Freeze)**:
Cờ theo không gian làm việc, khi bật sẽ chặn mọi yêu cầu sửa cơ hội của người dùng trong lúc một tiến trình di chuyển dữ liệu quy mô lớn đang chạy, để tránh xung đột ghi đè giữa người dùng và tiến trình nền.

**Chống Tạo Cơ hội Trùng khi Chuyển đổi (Deal Conversion Guard)**:
Cơ chế tự động phát hiện khi một doanh nghiệp đã có cơ hội đang mở trên cùng phễu lúc chuyển đổi từ khách hàng tiềm năng; mặc định gộp vào cơ hội có sẵn thay vì tạo mới, trừ khi người thực hiện chủ động xác nhận vẫn muốn tạo riêng.

**Tái phân loại Cơ hội Đã đóng (Deal Reclassification)**:
Nghiệp vụ mở lại một cơ hội đã đóng để sửa kết quả Thắng/Thua, tách biệt hoàn toàn khỏi quyền sửa hoặc chuyển giai đoạn cơ hội thông thường — đòi hỏi một quyền hạn riêng, để kết quả doanh số đã chốt không bị đảo ngược tùy tiện.

**Nhu cầu nghiệp vụ chưa triển khai (ghi nhận nhưng chưa đủ chín muồi để đặc tả):** Bảng giá & Chi tiết Dòng sản phẩm (CPQ), Quy trình Phê duyệt Chiết khấu, Trạng thái Tạm ngưng Cơ hội (On Hold), Phân chia Doanh số Đồng phụ trách, Nhóm Dự báo theo mức độ tin cậy (Forecast Categories), Hạn ngạch Doanh số (Sales Quota). Xem Mục 7 của [`deals-pipeline-srs.md`](./srs/deals-pipeline-srs.md) cho lý do hoãn từng nhu cầu.
_Avoid_: Gọi đây là một "vai trò" — Người theo dõi là quyền cấp trên **từng bản ghi cụ thể**, cộng thêm vào phạm vi dữ liệu của người đó, không phải một vai trò hệ thống và không thay thế phạm vi dữ liệu.

**Người Theo dõi Cơ hội (Deal Watcher)**:
Nhân sự từ phòng ban khác (tư vấn giải pháp, kỹ thuật, pháp chế, tài chính) được thêm vào **một cơ hội cụ thể** để theo dõi và phối hợp. Họ xem hồ sơ, thêm ghi chú và nhận thông báo về đúng cơ hội đó, kể cả khi nó nằm ngoài phạm vi dữ liệu thông thường của họ — nhưng không chuyển giai đoạn, không sửa giá trị, không đóng và không xóa. Việc có xem được số liệu tài chính hay không vẫn theo quyền xem đầy đủ riêng. Xem `FEAT-35` tại [`deals-pipeline-srs.md`](./srs/deals-pipeline-srs.md).
_Avoid_: Nhầm với Phân chia Doanh số (`FEAT-24`) — đó là bài toán chia hoa hồng giữa những người có công, khác hẳn với việc cho ai đó quyền xem.

**Tái mở Cơ hội đã Thua (Deal Re-open)**:
Khi một khách hàng từng thua quay lại có nhu cầu, nghiệp vụ đúng là **tạo một cơ hội mới có liên kết** tới cơ hội thua cũ, giữ nguyên kết quả và lý do thất bại của cơ hội cũ. Xem `FEAT-38`.
_Avoid_: Dùng đường Tái phân loại Cơ hội Đã đóng (`FEAT-21`) cho tình huống này — tái phân loại là để **sửa một kết quả ghi nhận sai**, và dùng nhầm sẽ xóa mất kết quả thua khỏi lịch sử, làm hỏng tỷ lệ thắng lẫn dữ liệu phân tích nguyên nhân thất bại.

## Quản lý Vé Hỗ trợ & Dịch vụ Khách hàng (Tickets & Customer Service)

**Vé Hỗ trợ (Ticket / Support Case)**:
Thực thể đại diện cho một yêu cầu trợ giúp, phản ánh sự cố kỹ thuật, thắc mắc hoặc khiếu nại của khách hàng gửi tới doanh nghiệp qua các kênh liên lạc (Email, Livechat, WhatsApp, Biểu mẫu, Điện thoại), có mã định danh duy nhất (ví dụ: `TK-10023`) và được theo dõi từ khi tiếp nhận đến khi xử lý hoàn tất.

**Cam kết Chất lượng Dịch vụ (SLA - Service Level Agreement)**:
Chính sách thỏa thuận về thời gian xử lý yêu cầu giữa doanh nghiệp và khách hàng, bao gồm hai chỉ số cốt lõi: **Thời hạn Phản hồi Đầu tiên (First Response Time Due)** và **Thời hạn Giải quyết Xong (Resolution Time Due)** tùy theo mức độ ưu tiên của vé.

**Vi phạm SLA (SLA Breach)**:
Trạng thái cảnh báo khi nhân viên hỗ trợ không phản hồi hoặc không giải quyết xong vé trong khoảng thời gian cam kết của chính sách SLA, dùng để kích hoạt quy trình leo thang quản lý (Escalation).

**Tạm dừng / Tiếp tục Tính giờ SLA (SLA Pause & Resume)**:
Cơ chế tự động đóng băng đồng hồ đếm ngược SLA khi vé chuyển sang trạng thái "Đang chờ khách hàng phản hồi" hoặc "Chờ bên thứ ba", và tiếp tục đếm giờ khi khách hàng phản hồi lại, đảm bảo tính công bằng khi đánh giá KPI nhân viên.

**Khảo sát Mức độ Hài lòng (CSAT - Customer Satisfaction Score)**:
Khảo sát đánh giá chất lượng dịch vụ (thang điểm 1-5 sao kèm nhận xét) được hệ thống tự động gửi tới khách hàng ngay sau khi vé hỗ trợ được đánh dấu Đã giải quyết (Resolved).

**Mã Phân loại Giải pháp (Resolution Code)**:
Danh mục nguyên nhân & giải pháp chuẩn hóa (ví dụ: *Đã hướng dẫn sử dụng, Đã sửa lỗi phần mềm, Lỗi do cấu hình người dùng, Hoàn tiền*) bắt buộc nhân viên hỗ trợ phải khai báo khi giải quyết vé để phục vụ phân tích chất lượng sản phẩm.

**Cấu trúc Vé Cha - Vé Con (Parent-Child Ticket Hierarchy)**:
Mô hình liên kết một sự cố lớn (Vé Cha / Major Incident) với nhiều yêu cầu khiếu nại của từng khách hàng riêng lẻ (Vé Con / Sub-tickets), cho phép cập nhật trạng thái và phản hồi hàng loạt tới tất cả các vé con khi sự cố cha được khắc phục.

**Gộp Vé Hỗ trợ (Ticket Merge)**:
Thao tác hợp nhất các vé hỗ trợ trùng lặp từ cùng một khách hàng về một vé duy nhất (Master Ticket), chuyển toàn bộ lịch sử trao đổi và đóng vé phụ để tránh trùng lặp công việc cho đội ngũ hỗ trợ.

**Ghi chú Nội bộ vs Phản hồi Công khai (Internal Note vs Public Reply)**:
Hai chế độ trao đổi trên vé hỗ trợ: *Ghi chú Nội bộ* chỉ hiển thị cho nhân viên trong công ty để phối hợp xử lý; *Phản hồi Công khai* sẽ gửi trực tiếp thông điệp tới khách hàng qua email/kênh chat.

**Lịch làm việc trong Cam kết SLA (SLA Operating Hours & Business Calendar)**:
Khung thời gian được tính vào đồng hồ đếm ngược SLA, hỗ trợ 2 chế độ: Hỗ trợ liên tục 24/7 (mọi ngày, kể cả ngày nghỉ/lễ) cho mức độ Khẩn cấp; và Giờ hành chính 8x5 (08:00 - 17:30 Thứ 2 - Thứ 6) cho các mức độ thông thường, tự động tạm dừng tính giờ vào ban đêm, cuối tuần và các ngày lễ quốc gia.

**Phân bổ Vé Tự động (Ticket Auto-Assignment)**:
Quy tắc tự động gán vé hỗ trợ mới tiếp nhận cho nhóm kỹ năng chuyên môn phù hợp và điều phối theo thuật toán chia đều (Round-robin) dựa trên khối lượng công việc hiện tại của nhân viên hỗ trợ.

**Trạng thái Chờ Bên thứ ba (Pending 3rd Party)**:
Trạng thái đặt lên vé hỗ trợ khi tiến độ giải quyết phụ thuộc vào phản hồi từ nhà cung cấp bên ngoài (Vendor, Nhà mạng viễn thông, Đối tác vận chuyển), cho phép tự động tạm dừng đồng hồ tính SLA để không phạt oan nhân viên hỗ trợ.

**Ma trận Leo thang SLA (SLA Escalation Matrix)**:
Bộ quy tắc phân cấp hành động tự động (gửi cảnh báo quản lý, tự động chuyển quyền sở hữu vé, gắn cờ ưu tiên khẩn cấp) khi một vé hỗ trợ tiếp cận hoặc vượt quá giới hạn thời gian cam kết chất lượng dịch vụ.

## Quản lý Công việc & Hoạt động (Tasks & Activities)

**Công việc / Tác vụ (Task / To-do)**:
Thực thể đại diện cho một hành động cần hoàn thành của nhân viên (gọi điện thoại, gửi báo giá, họp trực tuyến, demo sản phẩm, ký hợp đồng), có tiêu đề, mô tả, hạn chót (`dueDate`), mức độ ưu tiên và người chịu trách nhiệm thực thi (`ownerId`).

**Loại Hoạt động Bán hàng (Activity Type)**:
Phân loại chuẩn hóa các hình thức tương tác với khách hàng: **Cuộc gọi (Call)**, **Email**, **Cuộc họp (Meeting)**, **Trình diễn Sản phẩm (Demo)**, **Chăm sóc Tiếp theo (Follow-up)**, **Việc cần làm (To-do)**.

**Công việc Lặp lại Định kỳ (Recurring Task)**:
Quy tắc tự động sinh ra công việc mới theo chu kỳ định sẵn (Hằng ngày / Hằng tuần / Hằng tháng / Hằng năm) sau khi công việc kỳ trước được đánh dấu Hoàn tất hoặc theo lịch cố định (ví dụ: Chăm sóc khách hàng VIP định kỳ ngày 15 hằng tháng).

**Hạn chót & Nhắc nhở Tự động (Due Date & Task Reminder)**:
Mốc thời gian cam kết hoàn thành công việc kèm thời điểm phát chuông thông báo nhắc nhở (`reminderAt`) trước thời hạn (15 phút, 1 giờ, 1 ngày) để nhân viên không bao giờ bỏ sót công việc quan trọng.

**Trạng thái Công việc (Task Lifecycle Status)**:
Vòng đời thực thi của một nhiệm vụ: **Chờ thực hiện (`PENDING`)** -> **Đang thực hiện (`IN_PROGRESS`)** -> **Đã hoàn thành (`COMPLETED`)** hoặc **Đã hủy (`CANCELLED`)**.

**Liên kết Đa Thực thể (Multi-Entity Association)**:
Khả năng gắn một công việc vào đồng thời nhiều thực thể nghiệp vụ liên quan: vừa thuộc về một Khách hàng cá nhân (`contactId`), vừa thuộc Doanh nghiệp (`accountId`), vừa phục vụ Cơ hội bán hàng (`dealId`) hoặc xử lý Vé hỗ trợ (`ticketId`).

**Nhật ký Hoạt động (Activity Feed)**:
Luồng dữ liệu lưu vết chi tiết từng sự kiện tương tác phát sinh (Ai đã gọi cho ai lúc mấy giờ, kết quả cuộc gọi ra sao, email đã gửi với nội dung gì) để toàn bộ đội ngũ nắm bắt tiến độ công việc chung.

**Người theo dõi Công việc (Task Watcher / Collaborator)**:
Thành viên nội bộ được gắn vào công việc để theo dõi tiến độ và nhận thông báo khi công việc hoàn thành hoặc thay đổi hạn chót mà không phải là người trực tiếp chịu trách nhiệm thực thi.

**Danh sách Kiểm tra Công việc (Task Checklist)**:
Tập hợp các đầu mục việc con cần hoàn thành bên trong một nhiệm vụ chính, cho phép theo dõi tiến độ % và áp dụng ràng buộc bắt buộc hoàn thành tất cả các mục trước khi đóng nhiệm vụ.

## Quản lý Chiến dịch Tiếp thị & Truyền thông Đa kênh (Marketing Campaigns)

**Chiến dịch Tiếp thị (Marketing Campaign)**:
Một đợt gửi thông điệp tiếp thị tới một tập người nhận chọn theo tiêu chí, qua một kênh chính (Email, WhatsApp, Zalo ZNS, Zalo OA, SMS), với một phiên bản nội dung đã được phê duyệt. Mọi lượt gửi của chiến dịch thuộc nhóm mục đích **Tiếp thị & Quảng bá** theo `contacts-srs.md` `BR-30.5`.
_Avoid_: Dùng chiến dịch tiếp thị thường để gửi thông báo bảo trì hay thông báo dịch vụ hàng loạt — đó là loại **Thông báo dịch vụ** riêng (`campaigns-srs.md` `FEAT-45`).

**Thông báo dịch vụ (Service Notice)**:
Một loại chiến dịch thuộc nhóm Giao dịch & Dịch vụ, chỉ dùng cho danh mục mục đích đóng (gián đoạn dịch vụ, thu hồi sản phẩm hoặc cảnh báo an toàn, thay đổi điều khoản hoặc giá, thông báo pháp luật bắt buộc, thay đổi lịch phục vụ). Gửi được tới cả khách đã Từ chối nhận tin tiếp thị, nhưng cấm nội dung quảng bá, bắt buộc có phê duyệt của Người phụ trách Bảo vệ Dữ liệu và chịu trần tần suất riêng (`campaigns-srs.md` `FEAT-45`).
_Avoid_: Gọi tin tri ân, tin chúc mừng hay bản tin sản phẩm là thông báo dịch vụ — chúng là tiếp thị.

**Danh sách người nhận chốt**:
Danh sách người nhận cụ thể được xác định tại thời điểm chiến dịch bắt đầu gửi (không phải lúc phê duyệt); khách hàng khớp tiêu chí sau thời điểm đó không được thêm vào.

**Số người nhận khả dụng (Reachable Audience)**:
Số người khớp tiêu chí còn lại sau khi áp các lý do loại trừ (hạn chế xử lý, giai đoạn vòng đời, không có điểm đến, điểm đến Không tiếp cận được, Từ chối nhận tin, tập loại trừ, trùng điểm đến, thiếu dữ liệu cá nhân hóa, giới hạn tần suất, giới hạn pháp lý). Chỉ là ước tính: quyết định gửi cuối cùng được kiểm tra lại ngay trước từng tin.
_Avoid_: Coi con số xem trước là cam kết — người hủy nhận tin sau khi xem trước vẫn phải được tôn trọng.

**Điểm đến (Destination)**:
Địa chỉ cụ thể nhận tin trên một kênh: một email, một số điện thoại, một tài khoản Zalo hoặc WhatsApp. Mỗi điểm đến nhận tối đa một tin cho một lượt gửi, kể cả khi nhiều hồ sơ dùng chung.

**Sổ cái Người nhận (Send Ledger)**:
Bản ghi không sửa được theo từng người trong danh sách chốt: điểm đến, kênh thực tế, phiên bản nội dung đã nhận, trạng thái phân phát (Chờ gửi, Đã chuyển nhà cung cấp, Đã phân phát, Thất bại tạm thời, Thất bại vĩnh viễn, Chưa xác định, Bị loại trừ tại thời điểm gửi, Đã hủy trước khi gửi) và các sự kiện tương tác ghi thêm (mở, nhấp, trả lời, hủy nhận tin, khiếu nại).
_Avoid_: Để "Đã mở"/"Đã nhấp" thay thế "Đã phân phát" — sự kiện tương tác ghi thêm, không đổi trạng thái phân phát.

**Chưa xác định (Unknown Delivery)**:
Tin đã chuyển nhà cung cấp nhưng quá thời gian chờ không nhận được xác nhận phân phát hay thất bại. Không tự động gửi lại, không kích hoạt dự phòng — thà bỏ sót một tin còn hơn gửi trùng.

**Phê duyệt kép (Four-Eyes Principle)**:
Người phê duyệt phát sóng phải khác người gửi phê duyệt và khác mọi người đã sửa phiên bản đang duyệt. Mọi thay đổi sau phê duyệt tạo phiên bản mới cần duyệt lại.

**Quyền Phát sóng chiến dịch**:
Quyền hạn tách biệt khỏi quyền tạo/sửa chiến dịch, cần cho phê duyệt, phát sóng, hẹn giờ, tạm dừng, tiếp tục, hủy và gửi lại.

**Gửi thử (Test Send)**:
Gửi bản thật của nội dung tới tối đa vài địa chỉ **nội bộ đã đăng ký** để kiểm tra hiển thị. Không vào sổ cái, không tính vào số liệu, vẫn tính chi phí.
_Avoid_: Cho gửi thử tới địa chỉ bất kỳ — đó là đường vòng vượt phê duyệt và đồng thuận.

**Tự động tạm dừng bảo vệ (Protective Auto-Pause)**:
Hệ thống tự tạm dừng chiến dịch khi có dấu hiệu gây hại (khiếu nại hoặc điểm đến hỏng vượt ngưỡng, mất tài khoản gửi, mẫu bị khóa, hết hạn mức, đình chỉ dịch vụ, chạm ngân sách). Hệ thống được tự tạm dừng nhưng không bao giờ tự tiếp tục.

**Giới hạn Tần suất (Frequency Capping)**:
Số tin tiếp thị tối đa một người nhận trong một khoảng thời gian trượt tuyệt đối, tính trên toàn doanh nghiệp (mọi chiến dịch và chuỗi nuôi dưỡng, cả tin đang gửi dở), theo từng kênh hoặc gộp mọi kênh. Độc lập với giới hạn pháp lý số tin trong 24 giờ.

**Khung Giờ Yên lặng (Quiet Hours)**:
Khoảng giờ không gửi tin tiếp thị, tính theo **giờ địa phương của người nhận**; tin rơi vào khung giờ này được hoãn, không bị bỏ, và gửi lại theo nhịp khi hết giờ.

**Dự phòng Kênh (Channel Fallback)**:
Gửi qua kênh thay thế chỉ khi kênh trước đã **xác nhận thất bại vĩnh viễn** vì không tới được người nhận (ví dụ không có tài khoản Zalo). Không kích hoạt khi chưa chắc chắn, khi người nhận đã chặn doanh nghiệp, khi bị loại vì đồng thuận hay tần suất, hay khi người nhận chỉ chưa mở tin.

**Chiến dịch Tái tiếp cận (Win-Back Campaign)**:
Chiến dịch được phê duyệt riêng bởi Quản lý Marketing và Quản lý Kinh doanh phụ trách tập khách, kèm phạm vi tập khách và thời hạn hiệu lực, để gửi tới khách hàng ở giai đoạn Đã rời bỏ (`contacts-srs.md` `BR-12.5b`). Không ghi đè đồng thuận.

**Cơ hội có nguồn gốc từ chiến dịch / Cơ hội chịu ảnh hưởng của chiến dịch**:
Hai thước đo tách biệt. "Có nguồn gốc" dựa trên Nguồn gốc chính của cơ hội (điểm chạm đầu tiên, `deals-pipeline-srs.md` `BR-23.1`), cộng dồn được giữa các chiến dịch. "Chịu ảnh hưởng" là cơ hội được tạo trong cửa sổ ghi nhận sau khi một liên hệ tham gia đã nhấp hoặc trả lời chiến dịch; một cơ hội có thể chịu ảnh hưởng của nhiều chiến dịch nên không cộng dồn được.
_Avoid_: Gọi chung là "doanh thu từ chiến dịch" — hai con số trả lời hai câu hỏi khác nhau và sẽ mâu thuẫn nếu trộn lẫn.

**Zalo ZNS / Zalo OA**:
Hai kênh chiến dịch riêng. Zalo ZNS gửi tin theo số điện thoại qua mẫu đã được Zalo duyệt; Zalo OA gửi tin truyền thông của Tài khoản Chính thức, chỉ tới người đang quan tâm tài khoản đó. Khác nhau về cách xác định người nhận, loại nội dung được phép, hạn mức và cách tính phí.
_Avoid_: Gọi chung là "kênh Zalo" — quy tắc đúng cho kênh này thường sai cho kênh kia.

**Đồng ý nhận tin có bằng chứng (điều kiện gửi tiếp thị)**:
Chiến dịch chỉ gửi tới kênh mà khách hàng có Đồng ý nhận tin kèm bằng chứng theo `contacts-srs.md` `BR-30.3`. Kênh chưa từng có Đồng ý có bằng chứng (hồ sơ tạo tay, tạo từ hội thoại…) bị loại như kênh đã từ chối.
_Avoid_: Coi "chưa từ chối" là "được phép gửi" — pháp luật chống tin rác yêu cầu đồng ý trước.

**Giữ lại (chiến dịch)**:
Trạng thái của chiến dịch đã duyệt, tới lúc gửi nhưng chưa gửi được vì một điều kiện vận hành (hạn mức, tài khoản gửi, ngày không gửi, dừng khẩn cấp…). Phê duyệt giữ nguyên trong thời gian ân hạn; người có quyền Phát sóng chiến dịch quyết định gửi, hệ thống không tự gửi.

**Dấu vết chặn gửi (Suppression Trace)**:
Dạng không đọc ngược được của một điểm đến đã từ chối nhận tin, được giữ sau khi dữ liệu của người đó bị xóa theo yêu cầu, chỉ để chặn gửi tiếp thị nếu điểm đến đó được nhập lại.

**Dừng khẩn cấp (Emergency Stop)**:
Một thao tác dừng ngay mọi hoạt động gửi tiếp thị của Không gian làm việc (chiến dịch, chuỗi nuôi dưỡng, gửi lại, chiến dịch hẹn giờ). Gỡ dừng khẩn cấp không tự tiếp tục bất kỳ chiến dịch nào.
