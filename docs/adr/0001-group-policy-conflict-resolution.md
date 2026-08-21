---
status: accepted
---

# Xung đột phân quyền trường khi user thuộc nhiều Nhóm quyền: hạn chế thắng, không cộng gộp

Trong Object Manager, phân quyền trường (FLS) được cấu hình theo Nhóm quyền, và một người dùng có thể thuộc nhiều nhóm cùng lúc. Khi các nhóm đó có cấu hình khác nhau trên cùng một trường, hệ thống phải chọn cấu hình nào áp dụng. Chúng tôi chọn **hạn chế thắng (deny-override)** cho các thuộc tính bảo mật — ẩn thắng hiển thị, chỉ đọc thắng xem&sửa, mức che dữ liệu mạnh hơn thắng mức yếu hơn — thay vì cộng gộp mở rộng (permissive-override, quyền cao nhất trong các nhóm thắng).

Lý do: một người dùng có thể bị thêm vào một nhóm khác vì lý do không liên quan (dự án chéo phòng ban, nhóm tạm thời) mà không có chủ đích cấp thêm quyền xem dữ liệu nhạy cảm; cộng gộp mở rộng sẽ khiến việc thêm nhóm này vô tình mở khóa dữ liệu PII/tài chính mà nhóm chính của họ đang hạn chế. Với CRM xử lý dữ liệu khách hàng và tài chính, rủi ro lộ dữ liệu do cộng dồn quyền không kiểm soát được coi là nghiêm trọng hơn chi phí vận hành (admin phải rút user khỏi nhóm hạn chế để nâng quyền thay vì chỉ thêm nhóm mới).

Yêu cầu "bắt buộc nhập" (không phải thuộc tính bảo mật mà là ràng buộc chất lượng dữ liệu) vẫn hợp nhất theo **cộng gộp** — nếu bất kỳ nhóm nào yêu cầu bắt buộc thì trường đó bắt buộc với người dùng đó.

## Phương án đã cân nhắc

- **Cộng gộp mở rộng (Additive/Permissive-override)** cho cả thuộc tính bảo mật — bị loại vì rủi ro lộ dữ liệu nêu trên; đây cũng là mô hình bị nhiều audit bảo mật CRM/ERP gắn cờ khi phát hiện.
- **Nhóm có độ ưu tiên tường minh** (admin xếp hạng nhóm nào thắng khi chồng lấn) — linh hoạt nhất nhưng là cơ chế mới, thêm một trường cấu hình và logic phân giải phức tạp hơn; hoãn lại, không loại trừ cho tương lai nếu nhu cầu vận hành thực tế đòi hỏi.

## Hệ quả

Để nâng quyền truy cập một trường cho một người dùng cụ thể (ví dụ thăng chức), quản trị viên phải rút người đó khỏi nhóm đang hạn chế trường đó hoặc tổ chức lại nhóm — không thể chỉ thêm họ vào một nhóm quyền mở rộng hơn trong khi vẫn giữ ở nhóm cũ. Hướng dẫn này được ghi trong SRS Object Manager (BR-03.2).
