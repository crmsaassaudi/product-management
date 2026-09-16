# Mini SRS — Khắc phục Khoảng cách Phát hiện qua Kiểm thử Phân hệ Vé Hỗ trợ

**Mã đợt:** `FIX-TICKETS-2026-09`
**Ngày lập:** 16/09/2026
**Tài liệu gốc:** `srs/tickets-srs.md` (v7.2)
**Phạm vi:** Phân hệ Vé Hỗ trợ (Tickets)

---

## 1. Bối cảnh

Toàn bộ phạm vi tính năng của `tickets-srs.md` đã được tuyên bố hoàn tất qua các Issue #218–#226. Đợt rà soát này không mở thêm tính năng mới, mà kiểm chứng lại chất lượng sẵn sàng phát hành bằng cách đối chiếu từng quy tắc nghiệp vụ với mã nguồn thực tế, thay vì dựa vào nhãn trạng thái trong tài liệu.

Kết quả kiểm thử tự động của phân hệ: **339/339 trường hợp kiểm thử đạt, 30 bộ kiểm thử**. Tuy vậy, số liệu này chỉ chứng minh mã nguồn làm đúng những gì bộ kiểm thử yêu cầu; nó không chứng minh bộ kiểm thử yêu cầu đúng những gì tài liệu đặc tả đòi hỏi. Các khoảng cách dưới đây đều **không bị bộ kiểm thử hiện tại phát hiện**.

Đợt rà soát cũng cho thấy mục 7 của tài liệu gốc (`Khoảng cách Triển khai`) đã lạc hậu: nhiều hạng mục còn liệt kê là chưa làm thì thực tế đã hoàn thành, và ngược lại một số tính năng mang nhãn `[Yêu cầu mới]` thì thực tế đã có đầy đủ trong hệ thống. Việc hiệu chỉnh lại tài liệu gốc được đưa vào phạm vi đợt này.

---

## 2. Nguyên tắc chung rút ra

Các khoảng cách phát hiện được đều quy về một nguyên nhân chung, cần được nêu thành nguyên tắc để tránh lặp lại:

> **Vai trò nghiệp vụ không tồn tại sẵn trong ngữ cảnh phiên làm việc.** Hệ thống lưu vai trò nghiệp vụ (Trưởng phòng Hỗ trợ, Trưởng nhóm...) dưới dạng vai trò phân quyền gắn với tài khoản, và chỉ phân giải được thông qua dịch vụ phân quyền chuyên trách. Mọi chốt kiểm tra quyền đọc thẳng vai trò từ ngữ cảnh phiên đều nhận về danh sách rỗng, và do đó **từ chối nhầm chính những người có thẩm quyền**.

Nguyên tắc này đã được áp dụng đúng ở nhiều nơi trong hệ thống (thư viện câu trả lời mẫu, duyệt giờ tính phí, xuất bản bài viết tri thức, giám sát hướng dẫn). Các khoảng cách dưới đây là những điểm còn sót lại.

---

## 3. Danh mục khoảng cách cần khắc phục

### GAP-01 — Quyền gộp vé từ chối nhầm người có thẩm quyền `[Chặn phát hành]`

**Quy tắc liên quan:** `BR-27.7` (Cấp quyền gộp vé do doanh nghiệp quyết định), `BR-27.5` (Hoàn tác gộp nhầm)

**Hiện trạng nghiệp vụ:** Chốt kiểm tra quyền của thao tác gộp vé đọc vai trò nghiệp vụ trực tiếp từ ngữ cảnh phiên làm việc, thay vì phân giải qua dịch vụ phân quyền. Hệ quả:

- Trưởng phòng Hỗ trợ được cấp quyền theo đúng cơ chế phân quyền chuẩn của hệ thống sẽ **bị từ chối** thao tác gộp vé, hoàn tác gộp vé, và cả màn hình xem trước kết quả gộp.
- Trên thực tế chỉ còn Chủ sở hữu và Quản trị viên thực hiện được, tức là quy tắc `BR-27.7` không phát huy tác dụng: tham số cấu hình `CFG-27-01` cho phép doanh nghiệp hạ cấp quyền xuống Trưởng nhóm trở nên vô nghĩa.
- Quy tắc `BR-27.5` bị ảnh hưởng dây chuyền: người có thẩm quyền hoàn tác trong thời hạn 24 giờ cũng bị chặn, trong khi quá thời hạn thì thao tác gộp là vĩnh viễn.

**Vì sao không bị phát hiện:** Toàn bộ kiểm thử hiện có đều dựng sẵn vai trò dưới dạng chuỗi ký tự thuần trong ngữ cảnh giả lập. Cách dựng này đi qua một nhánh dự phòng và luôn cho kết quả đạt. Không có trường hợp kiểm thử nào mô phỏng người dùng được cấp quyền theo cơ chế phân quyền chuẩn — tức tình huống phổ biến nhất trong vận hành thật.

**Mức độ ảnh hưởng:** Nghiệp vụ bị chặn. Doanh nghiệp có nhiều vé trùng lặp không xử lý được nếu không nâng quyền Quản trị viên cho Trưởng phòng — vốn là cách khắc phục sai, làm mở rộng quyền ngoài ý muốn.

**Yêu cầu khắc phục:**
1. Chốt kiểm tra quyền gộp vé, hoàn tác gộp vé và xem trước gộp vé phải phân giải vai trò nghiệp vụ qua dịch vụ phân quyền, đồng nhất với cách các tính năng khác trong phân hệ đang làm.
2. Giữ nguyên hành vi hiện có đối với Chủ sở hữu và Quản trị viên.
3. Giữ nguyên quy tắc quyền hoàn tác không hạ xuống dưới Trưởng phòng Hỗ trợ trong mọi cấu hình.

**Tiêu chí nghiệm thu:**
- Người dùng được cấp vai trò Trưởng phòng Hỗ trợ theo cơ chế phân quyền chuẩn thực hiện được thao tác gộp vé, hoàn tác và xem trước.
- Khi doanh nghiệp đặt `CFG-27-01` là Trưởng nhóm, người giữ vai trò Trưởng nhóm thực hiện được thao tác gộp vé.
- Tư vấn viên thường vẫn bị từ chối.
- Phải có kiểm thử mô phỏng người dùng được cấp quyền theo cơ chế phân quyền chuẩn, không chỉ bằng chuỗi ký tự thuần.

---

### GAP-02 — Vé phụ sau khi gộp không mang trạng thái kết thúc `[Rủi ro số liệu]`

**Quy tắc liên quan:** `BR-27.1` (Hợp nhất lịch sử — vé phụ bị đóng và khóa lại)

**Hiện trạng nghiệp vụ:** Thao tác gộp đánh dấu vé phụ là đã gộp và đưa vào trạng thái đã xóa mềm, nhưng **không chuyển trạng thái xử lý của vé phụ sang trạng thái kết thúc**. Trường trạng thái giữ nguyên giá trị trước khi gộp, thường là "Đang mở".

Việc vé phụ biến mất khỏi danh sách là nhờ mọi truy vấn thông thường đều loại bỏ bản ghi đã xóa mềm, chứ không phải nhờ một trạng thái được lưu lại. Nói cách khác, "đóng và khóa" hiện là hệ quả của bộ lọc, không phải một dữ kiện được ghi nhận.

**Hệ quả nghiệp vụ:** Bất kỳ báo cáo, bản xuất dữ liệu hay tiến trình di trú nào đọc trường trạng thái mà không kèm điều kiện loại bỏ bản ghi đã xóa mềm đều nhìn thấy vé phụ như một vé đang mở. Rủi ro là sai lệch số liệu báo cáo, không phải mất mát dữ liệu.

**Ghi nhận phần đang đúng:** Các báo cáo tuân thủ cam kết dịch vụ hiện đã loại trừ vé đã gộp đúng theo `BR-27.6`, và khảo sát hài lòng cũng không gửi cho vé đã gộp. Khoảng cách nằm ở trường trạng thái, không nằm ở thống kê cam kết.

**Yêu cầu khắc phục:**
1. Thao tác gộp phải chuyển vé phụ sang trạng thái kết thúc theo cấu hình của doanh nghiệp, song song với việc đánh dấu đã gộp.
2. Thao tác hoàn tác gộp phải khôi phục lại trạng thái trước khi gộp của vé phụ.

**Tiêu chí nghiệm thu:**
- Sau khi gộp, trường trạng thái của vé phụ mang giá trị kết thúc.
- Sau khi hoàn tác, vé phụ trở lại đúng trạng thái trước đó.
- Báo cáo phân tích theo trạng thái không còn đếm vé đã gộp là vé đang mở.

---

### GAP-03 — Sổ ghi nhận thao tác gộp nằm ngoài giao dịch dữ liệu `[Rủi ro toàn vẹn]`

**Quy tắc liên quan:** `BR-27.2` (Toàn vẹn giao dịch)

**Hiện trạng nghiệp vụ:** Phần lõi của thao tác gộp — cập nhật vé chính, chuyển lịch sử trao đổi, đóng vé phụ — được thực hiện trong một giao dịch dữ liệu duy nhất, đúng yêu cầu. Tuy nhiên **bản ghi sổ theo dõi thao tác gộp được tạo và cập nhật bên ngoài giao dịch đó**.

**Hệ quả nghiệp vụ:** Nếu giao dịch thất bại giữa chừng và được hoàn tác, bản ghi sổ vẫn tồn tại và mô tả một thao tác gộp chưa từng xảy ra. Bản ghi này là căn cứ cho chức năng hoàn tác, nên hệ quả là một mục hoàn tác trỏ vào thao tác không có thật. Tình huống tương tự cũng xảy ra ở chiều hoàn tác.

**Yêu cầu khắc phục:** Đưa toàn bộ thao tác ghi sổ theo dõi gộp vé vào trong cùng giao dịch dữ liệu với phần lõi, ở cả hai chiều gộp và hoàn tác.

**Tiêu chí nghiệm thu:**
- Khi giao dịch gộp thất bại, không tồn tại bản ghi sổ nào cho thao tác đó.
- Khi giao dịch hoàn tác thất bại, trạng thái sổ không đổi.

---

### GAP-04 — Ma trận mức độ ưu tiên chưa cấu hình được theo doanh nghiệp `[Đã biết, giữ nguyên ưu tiên]`

**Quy tắc liên quan:** `BR-06.1` (Ma trận cấu hình được)

**Hiện trạng nghiệp vụ:** Ma trận suy ra mức độ ưu tiên từ tổ hợp Tác động × Khẩn cấp hiện là một bảng giá trị cố định trong mã nguồn, không nhận tham số doanh nghiệp và không có màn hình cấu hình. Mọi doanh nghiệp dùng chung một bộ giá trị.

Khoảng cách này **đã được ghi nhận đúng** trong tài liệu gốc tại FEAT-06. Đưa vào đây để đợt khắc phục có danh mục đầy đủ, không phải phát hiện mới.

**Yêu cầu khắc phục:** Đưa ma trận về danh mục cấu hình theo từng doanh nghiệp, kèm màn hình quản trị, giữ bộ giá trị hiện tại làm mặc định để doanh nghiệp đang vận hành không đổi hành vi.

---

### GAP-05 — Thiếu tầng kiểm thử tích hợp cho phân hệ Vé Hỗ trợ `[Chất lượng phát hành]`

**Quy tắc liên quan:** `NFR` — chất lượng và độ tin cậy khi phát hành

**Hiện trạng:** Trước đợt này, phân hệ Vé Hỗ trợ **không có bất kỳ kiểm thử tích hợp hay kiểm thử đầu-cuối nào**. Toàn bộ 45 nhóm tính năng được nghiệm thu bằng kiểm thử đơn vị với đối tượng giả lập.

Đây là nguyên nhân hệ thống của các khoảng cách nêu trên, và cũng là nguyên nhân của loạt lỗi đã phát hiện trong các đợt trước: đối tượng giả lập cung cấp đúng những dữ kiện mà mã nguồn thật bỏ sót, nên lỗi không lộ ra.

**Đã thực hiện trong đợt này:** Bổ sung 4 bộ kiểm thử tích hợp (27 trường hợp) chạy trên cơ sở dữ liệu thật và ngữ cảnh phiên thật, phủ: phân giải quyền gộp vé, trạng thái sau khi gộp, vòng đời đồng hồ cam kết qua tạm dừng và tiếp tục, và phân giải hạn mức năng lực. Mỗi bộ đều được kiểm chứng là **phát hiện được lỗi** bằng cách cố ý làm hỏng mã nguồn và xác nhận kiểm thử báo đỏ.

**Yêu cầu khắc phục tiếp theo:**
1. Mở rộng kiểm thử tích hợp sang các luồng còn lại: tách vé, vé cha – vé con, tự động đóng vé, khảo sát hài lòng, nhập/xuất dữ liệu.
2. Đưa kiểm thử tích hợp vào quy trình tích hợp liên tục. Hiện quy trình phát hành **không chạy** kiểm thử tích hợp lẫn kiểm thử đầu-cuối; toàn bộ kiểm thử tích hợp sẵn có của hệ thống không có gì bắt buộc phải đạt.
3. Bổ sung kiểm thử đầu-cuối cho luồng vé. Hạ tầng đã sẵn sàng nhưng cần môi trường có xác thực thật và dữ liệu mẫu do quy trình tích hợp liên tục cung cấp.

---

### GAP-06 — Hiệu chỉnh nhãn trạng thái và mục Khoảng cách của tài liệu gốc `[Tài liệu]`

**Hiện trạng:** Đối chiếu mã nguồn cho thấy tài liệu gốc mô tả sai hiện trạng ở cả hai chiều:

**Đã hoàn thành nhưng còn bị liệt kê là khoảng cách** (mục 7): thư viện câu trả lời mẫu, tách vé, cảnh báo trùng thao tác, leo thang thủ công, chuyển vé hàng loạt, thông báo cho tư vấn viên, cảnh báo rủi ro sang Cơ hội bán hàng, bài viết tri thức, giám sát hướng dẫn, giờ tính phí, gửi khảo sát hài lòng tự động, tự động đóng vé, giới hạn thời gian mở lại vé, chặn gộp vé khác khách hàng.

**Mang nhãn `[Yêu cầu mới]` nhưng thực tế đã có đầy đủ trong hệ thống:**

| Tính năng | Hiện trạng thực tế |
|---|---|
| `FEAT-34` Hàng đợi chung & vé chưa ai nhận | Đã có hàng đợi, thao tác tự nhận vé có chống tranh chấp, cảnh báo vé tồn đọng theo ngưỡng cấu hình |
| `FEAT-35` Trạng thái sẵn sàng & ca trực | Đã có mô hình trạng thái sẵn sàng nhiều trục và lịch ca trực; cơ chế phân bổ tự động **có lọc theo ca trực và trạng thái sẵn sàng** |
| `FEAT-42` Bảng điều khiển hàng đợi thời gian thực | Đã có, cập nhật theo chu kỳ 30 giây (chưa phải đẩy tức thời) |
| `FEAT-43` Báo cáo tuân thủ & hiệu suất | Đã có khoảng 12 chỉ số vé và xếp hạng tư vấn viên đa yếu tố, đều có nguồn dữ liệu |
| `FEAT-44` Nhật ký thay đổi & truy vết | Đã có phân hệ nhật ký dùng chung, bất biến, áp dụng cho vé |
| `FEAT-45` Vòng đời dữ liệu & bảo vệ dữ liệu cá nhân | Đã có quy trình tiếp nhận yêu cầu, ẩn danh hóa có xem trước, lưu trữ và xóa theo chu kỳ |

**Yêu cầu khắc phục:** Cập nhật nhãn trạng thái của sáu tính năng trên, viết lại mục 7 theo hiện trạng đã kiểm chứng, và ghi rõ khoảng cách còn lại của `FEAT-42` là cơ chế đẩy tức thời.

---

## 4. Thứ tự ưu tiên đề xuất

| Thứ tự | Mã | Lý do |
|---|---|---|
| 1 | `GAP-01` | Chặn nghiệp vụ; cách khắc phục tạm thời tại hiện trường là nâng quyền sai nguyên tắc |
| 2 | `GAP-03` | Rủi ro toàn vẹn dữ liệu, phạm vi sửa nhỏ và khu trú |
| 3 | `GAP-02` | Sai lệch số liệu báo cáo, không mất dữ liệu |
| 4 | `GAP-05` | Ngăn ngừa tái diễn; nên làm song song với các mục trên |
| 5 | `GAP-06` | Tài liệu; cần hoàn thành trước khi nghiệm thu phát hành |
| 6 | `GAP-04` | Đã biết từ trước, không chặn vận hành |

---

## 5. Phạm vi không thuộc đợt này

- `BR-40.1` tầng 1 (chính sách cam kết theo hợp đồng dịch vụ): phụ thuộc hai thực thể ngoài phân hệ chưa tồn tại — hợp đồng dịch vụ và trường gán chính sách cho khách hàng. Không tự giải quyết được trong phạm vi Vé Hỗ trợ.
- Cơ chế đẩy tức thời cho bảng điều khiển hàng đợi (`FEAT-42`): ghi nhận là khoảng cách, đề xuất tách thành yêu cầu riêng vì chạm tới hạ tầng kết nối thời gian thực.
- Chính sách bồi thường khi vi phạm cam kết: thuộc phân hệ Hợp đồng & Thanh toán.
