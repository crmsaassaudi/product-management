---
status: accepted
---

# Nhật ký thay đổi cấu hình quyền: Fail-closed thay vì Fail-open

Toàn bộ nhật ký nghiệp vụ trong hệ thống, kể cả nhật ký quyền ở phiên bản trước, áp dụng Fail-open — lỗi ghi log không được chặn thao tác chính, ưu tiên tính sẵn sàng. Với riêng **Nhật ký thay đổi cấu hình quyền** (đổi Vai trò, Cấp bậc thành viên, Nhóm, Đơn vị tổ chức, Phạm vi hiển thị dữ liệu, Chính sách truy cập, Cấp quyền tạm thời, Quyền trên bản ghi, chuyển nhượng quyền sở hữu Workspace — danh sách đầy đủ ở BR-41.4), chúng tôi đổi sang **Fail-closed**: nếu ghi log cho một trong các thao tác này thất bại, thao tác chính bị huỷ ngay lập tức và báo lỗi cho người dùng, thay vì âm thầm để thao tác diễn ra mà thiếu vết kiểm toán.

Lý do: đây là nhóm log phục vụ bằng chứng tuân thủ/kiểm toán bảo mật (ISO 27001, SOC2...). Một khoảng trống trong dấu vết "ai đã nâng quyền cho ai, khi nào" là mất khả năng chứng minh không thể chối cãi (non-repudiation) — hệ quả nghiêm trọng hơn nhiều so với việc một thao tác quản trị phân quyền (tần suất thấp, không nằm trên đường dẫn nghiệp vụ chính của người dùng cuối) bị chặn và phải thử lại. Ranh giới quan trọng: **Nhật ký quyết định cho phép/từ chối truy cập** — phát sinh theo mọi request đọc/ghi dữ liệu nghiệp vụ, tần suất rất cao — không nằm trong phạm vi Fail-closed này, vẫn giữ Fail-open (BR-41.2), vì đây không phải bằng chứng cho một thay đổi quyền cụ thể mà là log thống kê/điều tra khối lượng lớn; áp Fail-closed ở đây sẽ biến một sự cố hạ tầng logging thoáng qua thành gián đoạn toàn bộ nghiệp vụ đọc/ghi dữ liệu của khách hàng.

## Phương án đã cân nhắc

- **Fail-open cho toàn bộ** (giữ nguyên như trước) — bị loại vì để lộ khoảng trống kiểm toán ở đúng nhóm thao tác nhạy cảm nhất (nâng quyền Admin, chuyển nhượng quyền sở hữu...).
- **Fail-closed cho toàn bộ 2 nhóm log**, kể cả log quyết định truy cập theo mỗi request — bị loại vì log này phát sinh ở tần suất rất cao trên mọi thao tác đọc/ghi dữ liệu nghiệp vụ; một sự cố logging thoáng qua sẽ chặn đứng toàn bộ workspace thay vì chỉ chặn riêng thao tác quản trị phân quyền.
- **Fail-closed với cơ chế thử lại ngắn hạn** trước khi chặn hẳn — cân nhắc để giảm rủi ro chặn oan vì một lần trục trặc thoáng qua, nhưng bị loại theo quyết định của Product Owner: ưu tiên tuyệt đối tính toàn vẹn kiểm toán ngay từ lần lỗi đầu tiên, chấp nhận rủi ro gián đoạn ngắn hạn hơn là để lọt một giao dịch thiếu log.

## Hệ quả

- Ghi log cho nhóm cấu hình quyền phải nằm trong cùng luồng đồng bộ với thao tác chính (không thể xử lý bất đồng bộ/best-effort như log quyết định truy cập).
- Một sự cố ở hạ tầng lưu trữ log, kể cả tạm thời, sẽ khiến toàn bộ chức năng quản trị phân quyền của mọi workspace tạm thời không dùng được cho tới khi hạ tầng log phục hồi — đây là đánh đổi có chủ đích, không phải khiếm khuyết.
