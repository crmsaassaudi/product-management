# Biên bản diễn tập phương án khôi phục — MIG-05

| | |
| --- | --- |
| **Loại tài liệu** | Biên bản kết quả diễn tập — bằng chứng nghiệm thu |
| **Phạm vi** | SRS Object Manager v4.5, Mục 7.3 — MIG-04, MIG-05 |
| **Issue** | [#54](https://github.com/crmsaassaudi/product-management/issues/54); lỗi phát hiện được ghi tại [#60](https://github.com/crmsaassaudi/product-management/issues/60) |
| **Ngày diễn tập** | 2026-08-24 |
| **Người thực hiện** | tuong.huynh@antbuddy.com |

## 1. Môi trường và nguồn bản sao

| | |
| --- | --- |
| **Môi trường** | Dev, vận hành như môi trường thật |
| **Nguồn** | Cơ sở dữ liệu `crm` của môi trường dev — **chỉ đọc, không ghi** |
| **Bản sao** | `crm_mig_rehearsal`, tạo mới toàn bộ ở mỗi lần chạy |
| **Công cụ** | `npm run rehearse:pipeline-stages` — gọi đúng công cụ chuyển đổi mà đợt chạy thật sẽ gọi, không phải một bản sao logic |

Bản sao mang **đúng cấu hình Quy trình bán hàng và Giai đoạn thật** của tenant trên dev: 11 Giai đoạn thuộc 2 Quy trình bán hàng, 30 bản ghi cấu hình.

**Cơ hội là dữ liệu tạo thêm, không phải dữ liệu thật:** môi trường dev không có Cơ hội nào. Đối chiếu MIG-03 trên không Cơ hội thì luôn khớp mà không chứng minh điều gì, nên bản sao được nạp 33 Cơ hội trải trên đúng 11 Giai đoạn thật, để các con số tổng phụ thuộc vào ánh xạ thật. **Điều này phải chạy lại trên dữ liệu Cơ hội thật trước đợt chuyển đổi đầu tiên của một tenant đang vận hành** — xem Mục 5.

## 2. Hai lỗi phát hiện được trong lần diễn tập đầu

Lần chạy đầu tiên **thất bại**, và đó là lý do MIG-05 tồn tại. Chi tiết đầy đủ tại [#60](https://github.com/crmsaassaudi/product-management/issues/60).

### 2.1 Lệnh chặn ghi (MIG-04) không có hiệu lực

Công cụ ghi lệnh chặn vào **một nơi không phải nơi hệ thống đọc**. Nhật ký in "đã đóng băng thao tác Cơ hội", rồi "đã nhả khóa" — nhìn hoàn toàn bình thường. Nhưng trong suốt cửa sổ chuyển đổi, mọi thao tác chuyển giai đoạn Cơ hội **vẫn được ghi**.

Đúng kịch bản MIG-04 sinh ra để ngăn: một Cơ hội ghi theo cấu trúc cũ, đọc theo cấu trúc mới, và phần đối chiếu số liệu sau đó **vẫn khớp** vì tổng số và tổng giá trị không đổi.

### 2.2 Khôi phục (MIG-05) làm mất thuộc tính của Giai đoạn

Khôi phục dựng lại Giai đoạn **chỉ từ những thuộc tính mà đợt chuyển đổi có dùng đến**. Kết quả: đủ 11 Giai đoạn quay về, đối chiếu MIG-03 báo **"khớp hoàn toàn"**, và bảy thuộc tính bị xóa vĩnh viễn:

| Thuộc tính | Số Giai đoạn mất |
| --- | --- |
| `createdAt`, `updatedAt` | 11/11 |
| `__v` | 6/11 |
| `legacyId`, `name`, `isTerminal`, `isActive` | 5/11 |

`isActive` là cờ nghiệp vụ đang sống: **một Giai đoạn đã ngừng sử dụng quay về ở trạng thái đang sử dụng** và xuất hiện lại trên bảng Kanban của khách hàng.

## 3. Đã xử lý

- Lệnh chặn ghi vào đúng nơi hệ thống đọc, và **tên nơi lưu chỉ còn khai báo một lần** để hai đầu không thể lệch nữa.
- Bản gốc của Giai đoạn được **lưu trữ nguyên trạng trước khi ghi bất cứ thứ gì**. Khôi phục đọc từ bản lưu trữ đó, nên trả về nguyên trạng bản ghi chứ không phải phần công cụ dùng đến.
- Công cụ **tự đối chiếu từng thuộc tính** sau khi khôi phục và **báo lỗi** khi có khác biệt, thay vì kết luận "khớp" dựa trên số liệu tổng.
- Khi chạy khôi phục cho một đợt chuyển đổi do bản công cụ cũ thực hiện (không có bản lưu trữ), công cụ **nói rõ kết quả là không đầy đủ** thay vì báo thành công.
- Kiểm thử đi qua **cả đường ghi lẫn đường đọc thật** trên cơ sở dữ liệu thật, không thay thế bằng bản giả — đây chính là chỗ hai lỗi trên đã lọt qua.

## 4. Kết quả lần chạy đạt — 2026-08-24

| Hạng mục | Kết quả |
| --- | --- |
| Giai đoạn trước / sau khôi phục | 11 / 11 |
| Đối chiếu từng thuộc tính | **Không có khác biệt nào** |
| Cơ hội Đang mở / Thắng / Thua (trước = sau) | 21 / 6 / 6 |
| Tổng giá trị Pipeline (trước = sau) | 108.021,00 |
| Dự báo trọng số (trước = sau) | 48.308,40 |
| MIG-04 — lệnh chặn ghi đúng nơi hệ thống đọc | Đạt |
| MIG-04 — lệnh chặn đã nhả sau khi chạy | Đạt |
| MIG-06 — bản đối chiếu theo từng tenant | In ra đủ 11 Giai đoạn |

Dự báo trọng số **không đổi**, đúng như MIG-03 yêu cầu: đợt chuyển đổi giữ nguyên danh tính Giai đoạn nên không có thay đổi xác suất nào, và do đó không có sai lệch nào cần duyệt.

Không phát hiện sai lệch nào cần xử lý ở lần chạy này.

## 5. Còn lại trước đợt chuyển đổi của một tenant đang vận hành

Diễn tập này nghiệm thu **cơ chế**. Nó chưa nghiệm thu **dữ liệu của một tenant cụ thể**, và hai việc đó khác nhau:

- [ ] Chạy `--verify` trên dữ liệu Cơ hội thật của tenant đó và duyệt mọi sai lệch dự báo trọng số (MIG-03).
- [ ] Chạy lại chính bộ diễn tập này trên bản sao dữ liệu **Cơ hội thật** của tenant đó — mục 1 nêu rõ Cơ hội trong lần này là dữ liệu tạo thêm.
- [ ] Gửi từng Tenant Admin bản đối chiếu Stage cũ → Stage mới của chính họ và nhận xác nhận (MIG-06).
- [ ] Chốt khung giờ chạy theo múi giờ của tenant và thời lượng cam kết để đưa vào `--reason`.
