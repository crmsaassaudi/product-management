---
status: accepted
---

# Đơn vị công việc của Omnichat: Tách bạch Phiên trên kênh (Channel Session) và Vụ việc của khách hàng (Customer Case)

**Bối cảnh:** Hiện tại một hội thoại gắn với đúng một kênh (Facebook Messenger, WhatsApp, Zalo, Live Chat, Email...). Khi cùng một khách hàng nhắn tin qua Facebook lúc 9h và WhatsApp lúc 9h05, hệ thống đối mặt với câu hỏi: đây là một hay hai đơn vị công việc? Nếu xem là hai việc riêng rẽ, hệ thống sinh ra hai đồng hồ cam kết SLA, phân cho hai Agent khác nhau, gửi hai lần chấm CSAT, và đếm gấp đôi khối lượng công việc trong báo cáo. Nếu gộp cứng làm một stream, hệ thống không thể xử lý các ràng buộc kỹ thuật đặc thù của từng kênh (cửa sổ 24h của WhatsApp/Facebook, tiêu đề CC/BCC của Email, kết nối socket tức thời của Live Chat).

**Quyết định:** Tách bạch hai tầng khái niệm:
1. **Phiên trên kênh (Channel Session / Conversation):** Là đơn vị vận hành kỹ thuật gắn với một kênh cụ thể. Chịu trách nhiệm về: kết nối adapter, Cửa sổ phản hồi (24h/Messaging Window), giới hạn nền tảng (tin nhắn mẫu, tệp tin), và chuỗi tin nhắn thô.
2. **Vụ việc của khách hàng (Customer Case / Omni Work Item):** Là đơn vị nghiệp vụ trung tâm gắn với một định danh khách hàng duy nhất. Chịu trách nhiệm về: cam kết thời gian phản hồi (SLA), phân công Agent phụ trách chính, thu thập khảo sát hài lòng (CSAT), đếm số vụ việc và báo cáo hiệu suất tổng thể.

Các phiên trên kênh của cùng một khách hàng trong một chu kỳ tương tác được liên kết (link) vào cùng một Vụ việc, đảm bảo một người phụ trách chính và không bị đếm trùng lặp SLA/CSAT.

**Phương án đã cân nhắc:**
- *Phương án 1 (Giữ nguyên 1 hội thoại = 1 kênh làm đơn vị công việc duy nhất):* Bị từ chối vì làm mù thông tin giữa các Agent cùng chăm sóc 1 khách, gây trải nghiệm rời rạc và làm sai lệch chỉ số năng suất (khối lượng đếm ảo, CSAT spam khách hàng).
- *Phương án 2 (Gộp cứng mọi kênh vào một stream duy nhất):* Bị từ chối vì xóa nhòa ranh giới kỹ thuật (mỗi kênh có cơ chế webhook, token, và cửa sổ giao tiếp hoàn toàn khác nhau).
- *Phương án 3 (Mô hình 2 tầng: Tách Channel Session và Customer Case):* Được chọn vì giữ trọn tính linh hoạt kỹ thuật của từng kênh ở tầng dưới, đồng thời nhất quán nghiệp vụ phân công, đo lường SLA và CSAT ở tầng trên.

**Hệ quả:**
- Công cụ đo SLA và CSAT đo trên đơn vị Vụ việc (Customer Case) để phản ánh đúng trải nghiệm của khách hàng.
- Báo cáo vận hành phân định rõ hai góc nhìn: Báo cáo kỹ thuật theo Kênh (lưu lượng kênh, AHT theo kênh) và Báo cáo hiệu suất theo Vụ việc (tỷ lệ giải quyết vụ việc, SLA vụ việc, điểm CSAT khách hàng).
- Agent tại màn hình xử lý nhìn thấy toàn cảnh lịch sử các kênh của khách hàng (BR-02.9) và tương tác phản hồi qua kênh phù hợp.

**Xác nhận:** 2026-08-25, giải quyết Mục 7.2 câu 1 trong SRS Omnichat (Issue crmsaassaudi/product-management#90).
