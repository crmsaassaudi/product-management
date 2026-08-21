# CRM Product Glossary

Thuật ngữ nghiệp vụ dùng chung cho các SRS/spec trong `product-management`, được chốt trong quá trình viết và review các tài liệu đó. Đây là nguồn canonical — các SRS chỉ trích lại phần liên quan trong mục "Thuật ngữ" của mình, không định nghĩa lại khác đi.

## Object Manager

**Nhóm quyền (Group)**:
Một nhóm người dùng do quản trị viên tenant tạo ra để gán cấu hình chung (ví dụ phân quyền trường, danh sách hiển thị). Một người dùng có thể thuộc nhiều nhóm cùng lúc.
_Avoid_: Role, permission group (khác Vai trò/Role hệ thống — nhóm ở đây là đối tượng do tenant admin tự tạo, không phải vai trò cấp hệ thống).

**Phân quyền trường (Field-Level Security – FLS)**:
Cơ chế quyết định một trường được hiển thị, ẩn, chỉ đọc, hay che giá trị — áp dụng theo Nhóm quyền, độc lập với quyền truy cập cả bản ghi.
_Avoid_: Permission, ACL (ACL kiểm soát bản ghi; FLS kiểm soát trường bên trong bản ghi — hai khái niệm khác nhau).

**Chính sách phân giải xung đột nhóm quyền (Group Policy Conflict Resolution)**:
Quy tắc quyết định cấu hình nào thắng khi một người dùng thuộc nhiều nhóm có cấu hình khác nhau trên cùng một trường. Chiến lược đang áp dụng là **hạn chế thắng (deny-override)** cho thuộc tính bảo mật (ẩn/chỉ đọc/che dữ liệu), và **cộng gộp (additive)** cho thuộc tính chất lượng dữ liệu (bắt buộc nhập). Xem [ADR-0001](./docs/adr/0001-group-policy-conflict-resolution.md).
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
