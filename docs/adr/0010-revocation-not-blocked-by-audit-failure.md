---
status: accepted
---

# Thao tác thu hẹp quyền không bị chặn bởi sự cố nhật ký

**Bối cảnh:** [ADR-0003](./0003-permission-config-audit-log-fail-closed.md) quy định nhật ký thay đổi cấu hình quyền theo nguyên tắc Fail-closed: không ghi được nhật ký thì huỷ thao tác. Nguyên tắc này áp cho mọi thao tác thay đổi ai-được-làm-gì, kể cả gỡ thành viên, tạm ngưng, hạ cấp bậc và thu hồi quyền tạm thời. Hệ quả: đúng lúc doanh nghiệp cần cắt quyền khẩn cấp (nhân viên bị buộc thôi việc, tài khoản bị nghi chiếm), một sự cố của nhật ký giữ nguyên quyền truy cập.

## Quyết định

Tách thao tác thuộc nhóm nhật ký thay đổi cấu hình quyền thành hai loại:

1. **Nới rộng hoặc trung tính** (cấp vai trò, nâng cấp bậc, thêm vào nhóm, tạo chính sách Cho phép, chuyển nhượng quyền sở hữu, đổi tham số…): giữ Fail-closed như ADR-0003.
2. **Thu hẹp** (gỡ thành viên, tạm ngưng, hạ cấp bậc, thu hồi hoặc hết hạn quyền tạm thời, dọn quyền theo dây chuyền, gỡ khỏi nhóm, thu hẹp ô): luôn thực hiện. Hệ thống giữ sự kiện để **ghi bù** ngay khi nhật ký phục hồi, gắn cờ "ghi bù" kèm thời điểm thao tác thật, và cảnh báo Người có toàn quyền nếu ghi bù chưa xong sau 15 phút.

Thao tác vừa thu hẹp vừa nới rộng được coi là nới rộng.

## Lý do

ADR-0003 bảo vệ khả năng chứng minh **ai đã trao quyền gì**. Với thao tác thu hẹp, rủi ro của việc chặn (quyền vẫn mở) lớn hơn rủi ro của việc nhật ký đến muộn vài phút. Ghi bù giữ đủ vết, chỉ chậm hơn.

## Hệ quả

- `iam-tenant-authorization.md` `BR-41.5` (Fail-closed cho nới rộng và trung tính) và `BR-41.6` (thu hẹp không bị chặn, ghi bù).
- Cơ chế ghi bù phải bền: sự kiện chưa ghi không được mất khi hệ thống khởi động lại.
- ADR-0003 vẫn có hiệu lực cho nhóm thao tác nới rộng và trung tính.
