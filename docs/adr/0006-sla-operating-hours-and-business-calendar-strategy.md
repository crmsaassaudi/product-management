---
status: accepted
---

# Chiến lược Tính toán Cam kết Chất lượng Dịch vụ (SLA) theo Lịch làm việc (24/7 vs Giờ hành chính 8x5)

**Bối cảnh:** Hiện tại hệ thống đếm ngược SLA cho Vé Hỗ trợ (Tickets) tính toán hạn chót bằng phép cộng thời gian thuần túy (`firstResponseDueAt = createdAt + SLA_First_Response`). Trong thực tế vận hành doanh nghiệp B2B SaaS, đội ngũ hỗ trợ kỹ thuật hoạt động theo khung giờ hành chính (ví dụ: 08:00 - 17:30 Thứ 2 đến Thứ 6), ngoại trừ các khách hàng VIP hoặc sự cố khẩn cấp (`URGENT`) được phục vụ 24/7. Nếu áp dụng đồng hồ đếm liên tục 24/7 cho toàn bộ các mức độ, một yêu cầu hỗ trợ mức Trung bình (SLA 24h) gửi lúc 16:00 chiều Thứ Sáu sẽ bị tính là Vi phạm SLA lúc 16:00 chiều Thứ Bảy, dẫn đến đánh giá sai lệch năng lực của nhân viên hỗ trợ và làm hỏng báo cáo KPI dịch vụ.

**Quyết định:** Áp dụng mô hình tính toán SLA 2 tầng linh hoạt theo chính sách Lịch làm việc (Operating Hours & Business Calendar):
1. **Chế độ Hỗ trợ Liên tục 24/7 (Continuous 24/7 SLA):**
   - Áp dụng cho: Mức độ ưu tiên Khẩn cấp (`URGENT`), hoặc các vé phát sinh từ Hợp đồng dịch vụ cao cấp (Enterprise SLA / VIP Customer).
   - Quy tắc tính: Đồng hồ đếm ngược liên tục từng phút xuyên suốt mọi ngày trong năm (kể cả ban đêm, cuối tuần và các ngày lễ Tết).
2. **Chế độ Giờ Hành chính 8x5 (Business Hours Calendar SLA):**
   - Áp dụng cho: Các mức độ ưu tiên thông thường (`HIGH`, `MEDIUM`, `LOW`).
   - Khung giờ chuẩn: Mặc định `08:00 - 17:30` từ Thứ Hai đến Thứ Sáu theo Múi giờ của Doanh nghiệp.
   - Cơ chế đóng băng: Đồng hồ SLA tự động đóng băng (Pause) ngoài khung giờ làm việc, vào các ngày Thứ Bảy, Chủ Nhật và danh mục Ngày nghỉ lễ quốc gia được thiết lập trong cài đặt Workspace. Khi bắt đầu ngày làm việc tiếp theo lúc 08:00 sáng, đồng hồ tự động kích hoạt tiếp tục.

**Phương án đã cân nhắc:**
- *Phương án 1 (Đếm liên tục 24/7 cho toàn bộ vé):* Bị từ chối vì áp đặt bất công cho nhân viên hỗ trợ thông thường ngoài giờ làm việc, gây tỷ lệ vi phạm SLA ảo.
- *Phương án 2 (Đếm cố định theo giờ hành chính cho mọi trường hợp):* Bị từ chối vì không đáp ứng được cam kết hỗ trợ sự cố khẩn cấp (Major Outage) và các cam kết hợp đồng dịch vụ SLA 24/7 với khách hàng Enterprise.
- *Phương án 3 (Mô hình 2 tầng kết hợp Lịch làm việc có thể cấu hình):* Được chọn vì đảm bảo tính công bằng cho nhân sự nội bộ, đồng thời đáp ứng linh hoạt các cam kết thương mại dịch vụ cao cấp.

**Hệ quả:**
- Động cơ tính toán SLA (`ticket-sla.projector`) tính hạn chót dựa trên lịch làm việc của Tenant và mức độ ưu tiên của vé.
- Báo cáo SLA phân định rõ ràng giữa các sự cố 24/7 và các yêu cầu xử lý trong giờ hành chính.

**Xác nhận:** 2026-08-29, giải quyết lỗ hổng P0 trong Cross-Review SRS Tickets & Customer Service.
