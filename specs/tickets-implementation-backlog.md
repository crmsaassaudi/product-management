# Backlog Triển khai — Phân hệ Vé Hỗ trợ Khách hàng, Dịch vụ CSKH & Cam kết SLA (Tickets)

| | |
| --- | --- |
| **Nguồn yêu cầu** | [`srs/tickets-srs.md`](../srs/tickets-srs.md) v7.2 (đạt chuẩn nghiệp vụ qua bốn vòng thẩm định độc lập) |
| **Ngày lập** | 2026-09-13 |
| **Phạm vi** | 45 tính năng, 204 quy tắc, 21 tham số cấu hình, 46 kịch bản UAT |
| **Đã có trong hệ thống** | 22 tính năng `[Đã triển khai]` — không tạo issue |
| **Cần làm** | 23 hạng mục: 19 tính năng `[Yêu cầu mới]` + 4 tính năng `[Đã triển khai một phần]`, gộp thành **16 issue** |
| **Trạng thái** | **Đã tạo issue** — 16 issue `#210`–`#225` tại `crmsaassaudi/product-management`, tạo ngày 2026-09-13 theo đúng thứ tự triển khai nên **số issue tăng dần khớp thứ tự làm**. Tất cả đang để **assignee trống** = chưa ai claim. |

## Cách dùng tài liệu này

Mã `T-xx` là **số tạm dùng khi lập backlog**; id thật là số issue GitHub ở cột *Issue*, dùng làm `id` feature (`feat-<số>`) và tên branch (`feat/<số>-<slug>`) theo [`PROCESS.md`](../PROCESS.md).

**Claim một ticket** = tự gán issue tương ứng cho mình (`gh issue edit <N> --add-assignee @me`). Điểm tra duy nhất xem ticket còn trống hay không là assignee của issue ở `product-management`.

Tiêu chí hoàn thành của mỗi issue **chỉ ghi phần đặc thù**: danh sách mã `BR` phải phủ và kịch bản UAT phải chạy được. Phần baseline (unit test pass, không phá tính năng khác, verification đã chạy) đã nằm trong `AGENTS.md` của repo triển khai — không lặp lại.

Ký hiệu `KB n` = Kịch bản UAT số n tại mục 6 của SRS. Cỡ: S (≤3 quy tắc, một repo), M (4–6 quy tắc), L (7–10 quy tắc hoặc chạm nhiều repo), XL (>10 quy tắc hoặc là nền của nhiều tính năng khác).

## Năm nguyên tắc xếp thứ tự

1. **Đo lường đi trước tối ưu.** 13/13 KPI ở mục 2.4 SRS đều được tính bởi FEAT-42/FEAT-43. Chưa có hai tính năng này thì không có cách nào chứng minh các tính năng còn lại có cải thiện chất lượng dịch vụ hay không, và không có số liệu báo cáo khách hàng theo hợp đồng. Đây là lý do Đợt 1 bắt đầu bằng nhóm đo lường chứ không phải nhóm tính năng.

2. **Sàn pháp lý và sàn chống lộ dữ liệu đi trước tính năng tăng trưởng.** Hai hạng mục mang rủi ro trực tiếp: gộp vé của hai khách hàng khác nhau (`BR-27.4`) khiến khách hàng này đọc được nội dung trao đổi của khách hàng kia, và thiếu cơ chế đáp ứng Nghị định 13 (`FEAT-45`) là rủi ro pháp lý có thể chặn ký hợp đồng với doanh nghiệp lớn. Gắn ràng buộc sau khi dữ liệu đã tích lũy thì phải rà soát hồi tố toàn bộ kho vé.

3. **Cơ chế nền đi trước tính năng dùng nó.** Hàng đợi chung (`FEAT-34`), ca trực (`FEAT-35`) và thông báo (`FEAT-37`) là chỗ mà bảy quy tắc thuộc tính năng đã triển khai đang trỏ về nhưng chưa có đích (`BR-10.3`, `BR-10.4`, `BR-11.4`, `BR-11.5`, `BR-12.3`, `BR-12.4`, `BR-14.2`). Làm tính năng khác trước thì phải dựng đường tạm rồi tháo ra sửa lại.

4. **Việc bị chặn bởi phụ thuộc ngoài phân hệ được tách riêng và đứng sau.** Bài viết tri thức cần module Cơ sở Tri thức, giờ tính phí cần khâu xuất hóa đơn, cam kết theo hợp đồng cần thực thể hợp đồng — cả ba nằm ngoài phạm vi tài liệu này (mục 1.2 SRS). Không xếp sớm rồi để nằm chờ.

5. **Tính năng chặn vận hành đội nhiều người đi trước tiện ích cá nhân.** Doanh nghiệp 40 nhân sự ba ca không vận hành được nếu thiếu hàng đợi chung, ca trực và thông báo; trong khi thư viện câu trả lời mẫu hay giám sát hướng dẫn chỉ làm việc đã chạy được nhanh hơn.

---

## Bảng tổng hợp bảy đợt

| Đợt | Chủ đề | Issue | Lý do đứng ở vị trí này |
| --- | --- | --- | --- |
| **1** | Đo lường & truy vết | T-01 → T-03 | Không đo được thì không chứng minh được cải thiện |
| **2** | Sàn pháp lý & chống lộ dữ liệu | T-04 → T-05 | Rủi ro đang mở trên môi trường thật |
| **3** | Nền điều phối đội nhiều người | T-06 → T-08 | Gỡ phụ thuộc treo cho 7 tính năng đã có |
| **4** | Vòng đời vé & chất lượng phục vụ | T-09 → T-11 | Cần nền đo lường ở Đợt 1 để thấy tác dụng |
| **5** | Sự cố diện rộng & cam kết theo hạng khách hàng | T-12 → T-13 | Cần hàng đợi và thông báo ở Đợt 3 |
| **6** | Hiệu quả vận hành hàng ngày | T-14 → T-15 | Làm nhanh việc đã chạy được |
| **7** | Phụ thuộc ngoài phân hệ | T-16 | Chờ module ngoài, tách riêng để không chặn phần còn lại |

---

## Đợt 1 — Đo lường & Truy vết (nền của mọi cam kết chất lượng)

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- |
| **T-01** | [#210](https://github.com/crmsaassaudi/product-management/issues/210) | `Tickets: bảng điều khiển hàng đợi thời gian thực` | FEAT-42 (4 quy tắc) | crm-api + crm-web | KB 19 | M |
| **T-02** | [#211](https://github.com/crmsaassaudi/product-management/issues/211) | `Tickets: báo cáo tuân thủ SLA, hiệu suất và khối lượng công việc` | FEAT-43 (11 quy tắc) | crm-api + crm-web | KB 20, 41 | XL |
| **T-03** | [#212](https://github.com/crmsaassaudi/product-management/issues/212) | `Tickets: nhật ký thay đổi và truy vết thao tác trên vé` | FEAT-44 (4 quy tắc) | crm-api + crm-web | KB 21, 34 | M |

**T-01 và T-02 đứng đầu vì chúng là điều kiện để nghiệm thu mọi thứ còn lại.** Bảng KPI mục 2.4 SRS có 13 chỉ số, ghi chú dòng dưới bảng nói rõ toàn bộ được tính và hiển thị bởi FEAT-42/FEAT-43. Hiện không có màn hình nào trong hệ thống hiển thị các chỉ số này, nghĩa là Trưởng phòng không đo được chất lượng dịch vụ, không có số liệu gửi khách hàng theo hợp đồng, và không phát hiện được vé tồn đọng trước khi vi phạm.

**T-02 là ticket lớn nhất của loạt** (11 quy tắc, `BR-43.1`→`BR-43.11`). Nên tách khi vào sprint nhưng **không tách theo từng báo cáo** — tách theo nguồn dữ liệu, vì `BR-43.2` định nghĩa quy tắc tính mẫu số (vé nào bị loại khỏi thống kê) mà mọi báo cáo khác đều dùng chung. `BR-43.2` phải xong trước tiên và phải cùng một lượt merge với `BR-43.1`. Ba báo cáo `BR-43.9`/`43.10`/`43.11` phụ thuộc tính năng ở đợt sau nên làm phần khung trước, đấu nối sau.

**T-03 gỡ phụ thuộc treo cho `BR-23.4` và `BR-25.4`** — hai quy tắc thuộc tính năng đã chạy nhưng hiện không ghi được dấu vết. Hệ quả hiện tại: một thao tác sửa cùng lúc 500 vé không để lại dấu ai làm và đã đổi gì, và không ai biết ai đã xuất dữ liệu khách hàng ra ngoài. Quy tắc bất biến `NFR-12` phải được hiện thực ở tầng ghi, không chỉ ẩn nút trên giao diện.

---

## Đợt 2 — Sàn pháp lý & Chống lộ dữ liệu (rủi ro đang mở)

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- |
| **T-04** | [#213](https://github.com/crmsaassaudi/product-management/issues/213) | `Tickets: chặn gộp vé khác khách hàng, hoàn tác gộp nhầm và thông báo khách hàng vé phụ` | FEAT-27 phần còn thiếu (`BR-27.3`→`BR-27.7`) | crm-api + crm-web | KB 5, 26, 46 | L |
| **T-05** | [#214](https://github.com/crmsaassaudi/product-management/issues/214) | `Tickets: lưu trữ, ẩn danh hóa và xử lý yêu cầu xóa dữ liệu cá nhân theo Nghị định 13` | FEAT-45 (8 quy tắc) | crm-api + crm-web | KB 22, 44 | L |

**T-04 xử lý một lỗ hổng đang tồn tại trên môi trường thật.** Hệ thống hiện cho phép gộp vé của hai khách hàng khác nhau; sau khi gộp, khách hàng này nhìn thấy toàn bộ nội dung trao đổi của khách hàng kia. `BR-27.4` là phần phải làm sớm nhất trong ticket này, có thể tách merge riêng trước bốn quy tắc còn lại. `BR-27.7` (cấp quyền gộp vé cấu hình được theo từng doanh nghiệp) cần đăng ký tham số ở Phụ lục B với mặc định Support Manager — lưu ý quyền **hoàn tác** không hạ xuống dưới Support Manager trong bất kỳ cấu hình nào, để người gộp nhầm không tự xóa dấu vết.

**T-05 là điều kiện ký hợp đồng với khách hàng doanh nghiệp lớn.** Phạm vi ẩn danh hóa phải phủ cả bốn nơi dữ liệu cá nhân còn tồn tại (`BR-45.6`): vé đang hoạt động, vé trong Thùng rác, vé lưu trữ dài hạn, và tệp đính kèm. Bỏ sót Thùng rác nghĩa là dữ liệu vẫn còn tới 30 ngày sau khi đã xác nhận với chủ thể dữ liệu là đã xóa. `BR-45.8` là ngoại lệ có chủ đích duy nhất của `NFR-12` — cần T-03 xong trước để có nhật ký mà gỡ nội dung cá nhân ra. Thời hạn 72 giờ tại `BR-45.4` tính theo **giờ thực**, không theo giờ làm việc.

---

## Đợt 3 — Nền Điều phối cho Đội nhiều Người

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- |
| **T-06** | [#215](https://github.com/crmsaassaudi/product-management/issues/215) | `Tickets: hàng đợi chung, ca trực và trạng thái sẵn sàng của tư vấn viên` | FEAT-34 (5 quy tắc) + FEAT-35 (6 quy tắc) | crm-api + crm-web | KB 10, 11, 42, 45 | XL |
| **T-07** | [#216](https://github.com/crmsaassaudi/product-management/issues/216) | `Tickets: thông báo cho tư vấn viên, kênh đánh thức ngoài giờ và xử lý gửi thất bại` | FEAT-37 (5 quy tắc) | crm-api + crm-web | KB 15, 45 | M |
| **T-08** | [#217](https://github.com/crmsaassaudi/product-management/issues/217) | `Tickets: chuyển vé hàng loạt khi nhân sự vắng mặt hoặc nghỉ việc` | FEAT-36 (5 quy tắc) | crm-api + crm-web | KB 12 | M |

**T-06 gỡ phụ thuộc treo nặng nhất của cả loạt.** Bảy quy tắc thuộc tính năng đã chạy đang trỏ về hai tính năng chưa tồn tại: `BR-10.3`, `BR-10.4`, `BR-11.4`, `BR-11.5`, `BR-12.4` trỏ về hàng đợi chung và ca trực. Hệ quả hiện tại: cơ chế phân bổ tự động **không biết ai đang thực sự trực**, nên gán vé cho người đã hết ca hoặc đang nghỉ phép; và khi mọi người đều đầy hạn mức thì vé không có chỗ nằm chờ an toàn.

Gộp FEAT-34 và FEAT-35 vào một issue vì chúng chia nhau cùng một bài toán: xác định tập người đủ điều kiện nhận vé tại một thời điểm. Tách ra thì phải dựng đường tạm giữa hai lượt merge. `BR-34.5` (hai người cùng bấm nhận một vé) là quy tắc dễ bỏ sót nhất và là chỗ hay hỏng ở hàng đợi đông người giờ cao điểm.

`BR-35.5` cần đối chiếu chủ động lịch trực với lịch làm việc của các chính sách cam kết — không có ràng buộc này thì doanh nghiệp vẫn ký được cam kết phục vụ liên tục cả ngày đêm nhưng không thực hiện được.

**T-07 gỡ phụ thuộc treo cho `BR-12.3` và `BR-14.2`.** Hiện người nhận vé không được báo là mình vừa được giao việc — họ chỉ biết khi tự mở danh sách, làm chậm thời điểm bắt tay xử lý trong khi đồng hồ cam kết vẫn chạy. `BR-37.4` (kênh đánh thức) phải làm cùng T-06 mới có tác dụng: chỉ định được người trực ngoài giờ mà không đánh thức được họ thì cam kết ngoài giờ vẫn rỗng.

---

## Đợt 4 — Vòng đời Vé & Chất lượng Phục vụ

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- |
| **T-09** | [#218](https://github.com/crmsaassaudi/product-management/issues/218) | `Tickets: giới hạn thời gian mở lại vé và tự động đóng vé sau thời gian không phản hồi` | FEAT-21 phần còn thiếu (`BR-21.3`) + FEAT-22 (4 quy tắc) | crm-api + crm-web | KB 8, 8b, 29 | M |
| **T-10** | [#219](https://github.com/crmsaassaudi/product-management/issues/219) | `Tickets: gửi khảo sát hài lòng tự động và xử lý vé mở lại khi khảo sát còn hiệu lực` | FEAT-20 phần còn thiếu (`BR-20.2`, `BR-20.4`→`BR-20.6`) | crm-api + crm-web | KB 4 | M |
| **T-11** | [#220](https://github.com/crmsaassaudi/product-management/issues/220) | `Tickets: leo thang thủ công và xử lý khiếu nại về chất lượng phục vụ` | FEAT-39 (4 quy tắc) | crm-api + crm-web | KB 17 | M |

**T-09 và T-10 phải cùng đợt và T-09 đi trước**, vì `BR-20.5` chốt mốc gửi khảo sát là thời điểm vé chuyển sang "đã xử lý xong", còn `BR-22.1` quyết định khi nào vé rời khỏi mốc đó. Làm T-10 trước thì phải giả định một vòng đời chưa tồn tại.

**T-10 đóng một lỗ hổng làm sai lệch số liệu đánh giá.** Hiện việc gửi khảo sát còn phụ thuộc thao tác thủ công của tư vấn viên; chừng nào còn thế, họ chỉ gửi cho các vé mình xử lý tốt và điểm hài lòng thu được không đại diện cho khách hàng đã phục vụ — `KPI-03` mất giá trị đánh giá. `BR-20.6` (vé mở lại thì vô hiệu hóa khảo sát cũ và gỡ điểm đã chấm khỏi chỉ số) là ngoại lệ có chủ đích duy nhất của `BR-20.3`.

**T-11 cần T-01 xong trước** (`BR-39.2` hiển thị vé mang cờ khách hàng bức xúc trên bảng điều khiển). `BR-39.3` tạo một thực thể mới — vé khiếu nại riêng, liên kết với vé gốc — chứ không che bớt một phần dòng trao đổi của vé gốc; cần đăng ký thực thể này trong mô hình dữ liệu.

---

## Đợt 5 — Sự cố Diện rộng & Cam kết theo Hạng Khách hàng

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- |
| **T-12** | [#221](https://github.com/crmsaassaudi/product-management/issues/221) | `Tickets: xử lý hàng loạt sự cố diện rộng theo mô hình vé cha - vé con` | FEAT-28 phần còn thiếu (`BR-28.2`→`BR-28.6`) | crm-api + crm-web | KB 6 | L |
| **T-13** | [#222](https://github.com/crmsaassaudi/product-management/issues/222) | `Tickets: chính sách cam kết theo hạng khách hàng và hợp đồng dịch vụ` | FEAT-40 (3 quy tắc) | crm-api + crm-web | KB 13 | M |

**T-12 đúng lúc cần nhất thì hiện không dùng được.** Một sự cố hạ tầng có thể sinh vài trăm vé trong mười phút; hiện cập nhật hoặc đổi trạng thái trên vé cha không lan truyền xuống vé con, nên Trưởng nhóm vẫn phải xử lý thủ công từng vé. Hai điểm dễ làm sai: `BR-28.2` quy định cập nhật lan truyền **được tính** là phản hồi công khai của vé con (nếu không, vé con vĩnh viễn không đóng được vì thiếu phản hồi riêng lẻ theo `BR-19.2`), và mốc cam kết phản hồi đầu tiên được ghi nhận tại **thời điểm đăng trên vé cha**, không phải thời điểm lan truyền xong — độ trễ kỹ thuật cho phép tới 5 phút theo `NFR-08` không được biến thành vi phạm giả.

**T-13 mở khả năng bán gói dịch vụ cao cấp.** Hiện chỉ áp cam kết theo mức ưu tiên, nên không ký được hợp đồng có mức cam kết khác nhau theo hạng khách hàng. Phụ thuộc ngoài phân hệ: cần thực thể **hạng khách hàng** từ Contacts và **hợp đồng dịch vụ** — xác nhận hai thứ này có sẵn trước khi bắt đầu, nếu chưa thì T-13 chuyển sang Đợt 7. `BR-40.2` áp dụng nguyên tắc chung của tài liệu: đổi chính sách chỉ ảnh hưởng vé tạo mới, vé đang mở giữ nguyên hạn chót đã tính.

---

## Đợt 6 — Hiệu quả Vận hành Hàng ngày

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- |
| **T-14** | [#223](https://github.com/crmsaassaudi/product-management/issues/223) | `Tickets: thư viện câu trả lời mẫu và ma trận đánh giá mức độ ưu tiên` | FEAT-16 (3 quy tắc) + FEAT-06 (3 quy tắc) | crm-api + crm-web | KB 14, 28 | M |
| **T-15** | [#224](https://github.com/crmsaassaudi/product-management/issues/224) | `Tickets: tách vé nhiều vấn đề và cảnh báo trùng thao tác` | FEAT-41 (4 quy tắc) + FEAT-38 (4 quy tắc) | crm-api + crm-web | KB 16, 18, 42 | L |

**T-14 gộp hai tính năng cùng bản chất chuẩn hóa cách làm việc** giữa các tư vấn viên: câu trả lời mẫu chuẩn hóa nội dung, ma trận tác động–nghiêm trọng chuẩn hóa cách xác định mức ưu tiên. Cả hai đều là danh mục cấu hình theo tenant, dùng chung cơ chế quản trị giá trị.

**T-15 gộp hai tính năng cùng chạm vào một màn hình xử lý vé.** `BR-41.3` (vé tách nhận cam kết tính từ thời điểm tách) cần kèm cơ chế kiểm soát: số lần tách phải vào báo cáo hiệu suất `BR-43.3`, tránh việc tách vé sắp vi phạm để làm sạch đồng hồ. `BR-38.4` có cửa sổ cảnh báo ghi đè 60 giây, cần đăng ký tham số ở Phụ lục B.

---

## Đợt 7 — Phụ thuộc ngoài Phân hệ (tách riêng, không chặn phần còn lại)

| Mã tạm | Issue | Tiêu đề đề xuất | Phủ | Repo | UAT | Cỡ |
| --- | --- | --- | --- | --- | --- | --- |
| **T-16** | [#225](https://github.com/crmsaassaudi/product-management/issues/225) | `Tickets: giờ tính phí, bài viết tri thức, giám sát hướng dẫn và cảnh báo rủi ro sang Deals` | FEAT-33, FEAT-31, FEAT-32, FEAT-30 (13 quy tắc) | crm-api + crm-web | KB 36, 39, 40, 41 | L |

Bốn tính năng này gộp một issue vì chung một đặc điểm: **mỗi cái đều phụ thuộc một thứ nằm ngoài phạm vi tài liệu Tickets**, và khối lượng riêng từng cái nhỏ.

- **FEAT-33** (giờ tính phí) cần khâu xuất hóa đơn nhận dữ liệu — Billing nằm ngoài phạm vi theo mục 1.2 SRS. `BR-33.3` nói giờ đã duyệt được chuyển sang bộ phận xuất hóa đơn nhưng chưa chốt cơ chế chuyển. **Nếu doanh nghiệp có hợp đồng bảo trì tính phí theo giờ thì tính năng này lên Đợt 2** — mục 7.4 SRS đã ghi rõ điều kiện đảo ưu tiên này.
- **FEAT-31** (bài viết tri thức) cần module Cơ sở Tri thức tồn tại. Xác nhận trước khi bắt đầu.
- **FEAT-32** (giám sát hướng dẫn) là tính năng giám sát người lao động — cần thống nhất chính sách nội bộ trước khi bật, `BR-32.2` yêu cầu tư vấn viên phải biết khi nào mình đang được giám sát.
- **FEAT-30** (bắn cờ rủi ro sang Deals) cần đồng bộ với [`deals-pipeline-srs.md`](../srs/deals-pipeline-srs.md) vì đây là tính năng **ghi dữ liệu vào giao diện module Deals**, không chỉ đọc.

---

## Phụ thuộc giữa các ticket

```
T-03 (nhật ký)  ──────────────► T-05 (ẩn danh hóa cần nhật ký để gỡ nội dung cá nhân)
T-01 (bảng điều khiển) ───────► T-11 (hiển thị vé khách hàng bức xúc)
T-02 (báo cáo) ◄────────────── T-06, T-12, T-16 (đấu nối số liệu vào báo cáo tương ứng)
T-06 (hàng đợi + ca trực) ────► T-07 (kênh đánh thức cần biết ai trực ngoài giờ)
                          └───► T-08 (chuyển vé hàng loạt trả về hàng đợi chung)
T-09 (vòng đời đóng vé) ──────► T-10 (mốc gửi khảo sát bám vào trạng thái đã xử lý xong)
```

Không có phụ thuộc vòng. T-04 và T-13 độc lập với phần còn lại, claim được bất cứ lúc nào sau khi đợt trước xong.

## Tham số cấu hình cần đăng ký

Phụ lục B của SRS liệt kê 21 tham số. Các ticket dưới đây **phải đăng ký tham số của mình** thay vì cắm cứng giá trị:

| Ticket | Tham số |
| --- | --- |
| T-04 | Cấp thấp nhất được phép gộp vé (mặc định Support Manager); thời hạn hoàn tác gộp 24 giờ |
| T-05 | Thời hạn lưu trữ vé đã đóng 36 tháng; thời hạn xử lý yêu cầu dữ liệu cá nhân 72 giờ thực; mốc cảnh báo còn 24 giờ và còn 6 giờ |
| T-06 | Ngưỡng cảnh báo vé tồn đọng trong hàng đợi 15 phút |
| T-07 | Số lần thử lại khi gửi thông báo thất bại (mặc định 3) |
| T-09 | Thời hạn cho phép mở lại vé 7 ngày; thời hạn chờ trước khi tự đóng 48 giờ làm việc; thời gian nhắc trước khi đóng 12 giờ làm việc |
| T-10 | Thời hạn hiệu lực đường dẫn khảo sát 7 ngày |
| T-15 | Cửa sổ cảnh báo ghi đè cùng một trường 60 giây |

---

*Tài liệu này là bản đồ thứ tự triển khai. Nội dung nghiệp vụ chuẩn nằm ở [`srs/tickets-srs.md`](../srs/tickets-srs.md) — khi hai tài liệu khác nhau, SRS là căn cứ.*
