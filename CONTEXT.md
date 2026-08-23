# CRM Product Glossary

Thuật ngữ nghiệp vụ dùng chung cho các SRS/spec trong `product-management`, được chốt trong quá trình viết và review các tài liệu đó. Đây là nguồn canonical — các SRS chỉ trích lại phần liên quan trong mục "Thuật ngữ" của mình, không định nghĩa lại khác đi.

## Object Manager

**Nhóm quyền (Group)**:
Một nhóm người dùng do quản trị viên tenant tạo ra để gán cấu hình chung (ví dụ phân quyền trường, danh sách hiển thị). Một người dùng có thể thuộc nhiều nhóm cùng lúc.
_Avoid_: Role, permission group (khác Vai trò/Role hệ thống — nhóm ở đây là đối tượng do tenant admin tự tạo, không phải vai trò cấp hệ thống).

**Phân quyền trường (Field-Level Security – FLS)**:
Cơ chế quyết định một người được làm gì với một trường — áp dụng theo Nhóm quyền, độc lập với quyền truy cập cả bản ghi. Theo đề xuất đang chờ duyệt, gồm **hai chiều độc lập**: Mức truy cập (Xem & Sửa / Chỉ xem / Ẩn) và Mức hiển thị giá trị (Hiện đầy đủ / Che một phần / Che hoàn toàn) — xem ADR-0001, mục Bổ sung 2026-08-23.
_Avoid_: Permission, ACL (ACL kiểm soát bản ghi; FLS kiểm soát trường bên trong bản ghi — hai khái niệm khác nhau).

**Chính sách phân giải xung đột nhóm quyền (Group Policy Conflict Resolution)**:
Quy tắc quyết định cấu hình nào thắng khi một người dùng thuộc nhiều nhóm có cấu hình khác nhau trên cùng một trường. Chiến lược đang áp dụng là **hạn chế thắng (deny-override)** cho thuộc tính bảo mật, áp dụng độc lập trên từng chiều của FLS; và **cộng gộp (additive)** cho thuộc tính chất lượng dữ liệu (bắt buộc nhập) — nhưng cộng gộp **không** áp dụng khi người dùng không có quyền nhập chính trường đó, khi ấy ràng buộc bắt buộc được miễn trừ và bản ghi bị gắn cờ thiếu dữ liệu. Ngoài ra, **vắng mặt cấu hình không phải là sự cho phép**, và quy tắc này không áp cho tác vụ tự động (được miễn trừ FLS) lẫn quản trị viên tenant (không bị FLS giới hạn). Xem [ADR-0001](./docs/adr/0001-group-policy-conflict-resolution.md) — lưu ý: phần gốc đã duyệt, riêng các điều khoản bổ sung 2026-08-23 (mô hình hai chiều, miễn trừ ràng buộc bắt buộc, vắng mặt cấu hình, phạm vi chủ thể) đang ở trạng thái đề xuất, chưa duyệt.
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

**Quyền Ủy thác (Delegated Grant Authority)**:
Một quyền hệ thống riêng biệt, do Owner/Admin chủ động cấp, cho phép người giữ nó gán các Vai trò/Nhóm đã tồn tại sẵn trong workspace cho thành viên khác mà không bị giới hạn bởi năng lực quyền hạn của chính bản thân (miễn trừ nguyên tắc "không vượt ceiling"). Không áp dụng cho việc tạo Vai trò/Nhóm mới, không áp dụng cho Cấp quyền tạm thời, và không cho phép thay đổi Cấp bậc thành viên (Owner/Admin/Member).
_Avoid_: "Quyền tạm thời"/"Role Assignment" (tên khác của một tính năng đã có sẵn — cấp có thời hạn, cần phê duyệt; Quyền Ủy thác là cấp vĩnh viễn, không cần phê duyệt, chỉ miễn trừ ràng buộc ceiling). Xem [ADR-0002](./docs/adr/0002-delegated-grant-authority-ceiling-exception.md).

**Cấp bậc thành viên (Membership Tier)** vs **Vai trò (Role)**:
Hai trục hoàn toàn độc lập trong một workspace — Cấp bậc thành viên (Owner/Admin/Member) quyết định có toàn quyền hay không; Vai trò là tập hợp quyền hạn chi tiết chỉ có ý nghĩa ở cấp Member. Một thay đổi tác động tới trục này không mặc nhiên tác động tới trục kia.
_Avoid_: Dùng lẫn "vai trò" để chỉ cả hai trục trong văn bản nghiệp vụ (ví dụ khi liệt kê phạm vi một nhật ký/audit log) — luôn nêu rõ đang nói tới Cấp bậc thành viên hay Vai trò.

**Nguyên tắc đóng cho Nhật ký cấu hình quyền (Closure Rule)**:
Nguyên tắc xác định phạm vi "Nhật ký thay đổi cấu hình quyền" (audit log Fail-closed, lưu 2 năm): bất kỳ thao tác nào làm thay đổi ai-được-làm-gì hoặc ai-thấy-gì trong workspace đều mặc định thuộc diện này, trừ thao tác xem trước/mô phỏng và thao tác chỉ đọc. Dùng nguyên tắc này thay vì liệt kê tĩnh để tránh bỏ sót tính năng phân quyền mới phát sinh sau này.
_Avoid_: Coi danh sách ví dụ minh hoạ (Vai trò, Nhóm, Đơn vị tổ chức...) là danh sách đóng kín — đó chỉ là ví dụ, nguyên tắc mới là điều khoản ràng buộc thật. Xem [ADR-0003](./docs/adr/0003-permission-config-audit-log-fail-closed.md) cho quyết định Fail-closed đi kèm.

## Omnichat

**Hồ sơ khách hàng tạm (Provisional Customer Record)**:
Một hồ sơ khách hàng do hệ thống tự tạo ngay khi nhận tin nhắn từ một người gửi chưa xác định được danh tính CRM thật (chưa khớp email/số điện thoại/liên kết thủ công nào). Được thay thế bằng liên kết tới hồ sơ khách hàng thật khi có đủ căn cứ (tự động với kênh có định danh xác thực mạnh như WhatsApp, hoặc do Agent tự xác nhận với các kênh còn lại).
_Avoid_: "Shadow contact", "khách vãng lai" — dùng đúng tên này để nhất quán giữa các SRS omnichannel.

**Ngưỡng an toàn đóng hàng loạt (Bulk-Close Safety Threshold)**:
Cơ chế giới hạn số lượng hội thoại được tự động đóng trong một khoảng thời gian ngắn của một tenant/kênh, để một lỗi cấu hình hoặc sự cố kênh không âm thầm đóng hàng loạt hội thoại đang cần xử lý. Khi chạm ngưỡng, việc đóng bị hoãn lại và được thử lại sau, không hủy bỏ.
_Avoid_: "Circuit breaker", "rate limit" — đây là khái niệm nghiệp vụ (bảo vệ khách hàng khỏi bị đóng hội thoại oan), không phải thuật ngữ hạ tầng.

**Lời mời nhận hội thoại (Conversation Offer)**:
Một đề nghị có thời hạn gửi tới một Agent cụ thể để nhận xử lý một hội thoại đang chờ. Agent phải phản hồi (nhận/từ chối) trong thời hạn; hết hạn hoặc từ chối thì hội thoại được mời tới Agent phù hợp tiếp theo.
_Avoid_: "Offer/lease", "work item" — dùng "Lời mời nhận hội thoại" trong mọi tài liệu nghiệp vụ.

**Ưu tiên người phụ trách trước đó (Previous-Assignee Priority / Sticky Routing)**:
Quy tắc ưu tiên định tuyến hội thoại mới của một khách hàng trở lại đúng Agent đã từng phụ trách khách hàng đó gần đây, nếu Agent đó còn khả năng nhận thêm việc — nhằm giữ mạch tương tác quen thuộc cho khách hàng.
_Avoid_: "Sticky assignee/sticky routing" trong văn bản nghiệp vụ — chỉ dùng trong tài liệu kỹ thuật.

**Cửa sổ phản hồi (Reply Window)**:
Khoảng thời gian, tính từ tin nhắn gần nhất của khách hàng, mà Agent còn được phép chủ động gửi tin nhắn tự do cho khách trên một kênh nhắn tin. Sau khi hết cửa sổ, một số kênh (ví dụ WhatsApp) chỉ cho gửi tin dạng mẫu đã được phê duyệt trước; một số kênh khác (Email, Live Chat, Telegram) không có giới hạn này.
_Avoid_: "Reply window" tiếng Anh trong văn bản nghiệp vụ tiếng Việt — dùng "Cửa sổ phản hồi".
