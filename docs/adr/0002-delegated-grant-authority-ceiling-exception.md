---
status: accepted
---

# Quyền Ủy thác: cho phép gán Vai trò/Nhóm có sẵn vượt năng lực người gán

Toàn bộ hệ phân quyền dựa trên nguyên tắc "không ai được cấp quyền vượt quá năng lực bản thân" (BR-09.3, BR-17.2, và ràng buộc tương tự ở FEAT-25/FEAT-30) để chống leo thang đặc quyền. Trên thực tế, nhiều doanh nghiệp giao việc tạo tài khoản/gán vai trò cho bộ phận IT hoặc Hành chính-Nhân sự — những người này thường không có quyền nghiệp vụ (Sales, Cơ hội...) nên bị chặn không gán được đúng vai trò cần thiết cho nhân viên mới, buộc Owner phải tự tay xử lý mọi thao tác onboarding. Chúng tôi bổ sung một quyền hệ thống độc lập — **Quyền Ủy thác** — do Owner/Admin chủ động cấp riêng (không tự động đi kèm quyền "Tạo người dùng"), cho phép người giữ nó gán bất kỳ Vai trò/Nhóm **đã tồn tại sẵn** trong workspace cho người khác mà không bị chặn bởi ceiling.

Đây là một ngoại lệ có chủ đích với nguyên tắc chống leo thang — xương sống bảo mật của toàn module. Chúng tôi chấp nhận đánh đổi này vì phạm vi ngoại lệ được khoanh vùng chặt: chỉ áp dụng khi gán một Vai trò/Nhóm đã tồn tại (tức đã được Owner duyệt trước khi được tạo ra), tuyệt đối không áp dụng khi tạo Vai trò/Nhóm mới (FEAT-17, FEAT-25/26), không áp dụng cho Cấp quyền tạm thời (FEAT-30), và không bao giờ chạm tới trục Cấp bậc thành viên (nâng lên Admin/Owner vẫn theo đúng BR-09.2, không đổi). Nhờ vậy, người giữ Quyền Ủy thác không thể "phát minh" ra một năng lực quyền hạn mới chưa từng tồn tại — chỉ có thể phân phối lại cái đã được duyệt sẵn.

## Phương án đã cân nhắc

- **Giữ nguyên ceiling, giải quyết bài toán IT/HR bằng vai trò trung gian** (Owner tự tạo sẵn một Vai trò "IT Onboarding" đúng quyền cần thiết rồi giao riêng cho IT/HR) — bị loại vì Owner vẫn phải can thiệp thủ công mỗi khi danh mục vai trò cần gán thay đổi, không giải quyết triệt để vấn đề vận hành.
- **Gắn quyền "vượt ceiling" mặc định vào quyền "Tạo người dùng" sẵn có** (đúng như đề xuất ban đầu từ phía business) — bị loại vì biến một quyền rất phổ biến thành cửa hậu leo thang toàn hệ thống một cách âm thầm, không cho Owner kiểm soát chọn lọc ai thực sự có năng lực này.

## Hệ quả

- Cần rào chắn cứng ở tầng API: mọi endpoint gán Vai trò/Nhóm phải phân biệt rõ 2 luồng kiểm tra (ceiling thường vs miễn trừ qua Quyền Ủy thác), trong khi các endpoint tạo Vai trò/Nhóm mới hoặc yêu cầu Cấp quyền tạm thời phải tuyệt đối không đọc cờ Quyền Ủy thác này.
- Owner cần một nơi riêng để cấp/thu hồi Quyền Ủy thác cho từng thành viên, tách biệt hoàn toàn khỏi việc cấp quyền "Tạo người dùng" (xem BR-09.5, FEAT-09, FEAT-18).
