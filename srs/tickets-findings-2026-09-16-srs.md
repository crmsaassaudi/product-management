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

Các khoảng cách phát hiện được quy về ba nguyên nhân, cần nêu thành nguyên tắc để tránh lặp lại:

**Nguyên tắc 1 — Vai trò nghiệp vụ không tồn tại sẵn trong ngữ cảnh phiên làm việc.** Hệ thống lưu vai trò nghiệp vụ (Trưởng phòng Hỗ trợ, Trưởng nhóm...) dưới dạng vai trò phân quyền gắn với tài khoản, và chỉ phân giải được thông qua dịch vụ phân quyền chuyên trách. Mọi chốt kiểm tra quyền đọc thẳng vai trò từ ngữ cảnh phiên đều nhận về danh sách rỗng, và do đó **từ chối nhầm chính những người có thẩm quyền**. Áp dụng cho `GAP-01`, `GAP-12`.

**Nguyên tắc 2 — Quyền thao tác dữ liệu không thay được cho quyền chức vụ.** Quyền chỉnh sửa vé là quyền **mọi tư vấn viên đều có**. Một quy tắc nghiệp vụ nói "chỉ cấp quản lý mới được làm" mà chỉ gác bằng quyền chỉnh sửa vé thì trên thực tế không gác gì cả. Áp dụng cho `GAP-08`, `GAP-10`; `GAP-09` là biến thể: chốt quyền đúng nhưng thiếu quy tắc tách bạch người thực hiện và người phê duyệt.

**Nguyên tắc 3 — Hệ thống không được bỏ qua yêu cầu mà nó không hiểu.** Khi một truy vấn nhận điều kiện lọc không hỗ trợ, bỏ qua im lặng sẽ trả về dữ liệu sai phạm vi trông y như dữ liệu đúng. Phải báo lỗi. Áp dụng cho `GAP-07`.

Nguyên tắc 1 và 2 đã được áp dụng **đúng** ở nhiều nơi trong hệ thống — thư viện câu trả lời mẫu, duyệt giờ tính phí, xuất bản bài viết tri thức, khởi tạo phiên giám sát đều phân giải vai trò qua dịch vụ phân quyền và kiểm tra vai trò quản lý. Các khoảng cách dưới đây là những điểm còn sót lại, không phải hiểu nhầm hệ thống.

**Về phương pháp phát hiện:** mọi khoảng cách trong tài liệu này đều được xác minh bằng cách đọc mã nguồn thực tế. Nhãn trạng thái trong tài liệu gốc và tên của các bộ kiểm thử **không được dùng làm căn cứ**, vì đợt rà soát cho thấy cả hai đều mô tả sai hiện trạng ở cả hai chiều.

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

### GAP-07 — Điều kiện lọc bị loại bỏ âm thầm khi truy vấn danh sách vé `[Chặn phát hành]`

**Quy tắc liên quan:** `BR-30.1`, `BR-30.2` (Cảnh báo rủi ro sang Cơ hội bán hàng), `BR-41.2` (Liên kết hai chiều khi tách vé)

**Hiện trạng nghiệp vụ:** Tầng truy vấn danh sách vé chỉ chấp nhận một **danh sách trắng cố định** các trường được phép lọc. Mọi điều kiện lọc nằm ngoài danh sách này bị **âm thầm bỏ qua** — không báo lỗi, không ghi cảnh báo, truy vấn vẫn trả về kết quả trông bình thường.

Hai nơi đang gửi điều kiện lọc không nằm trong danh sách trắng:

**1. Cảnh báo rủi ro sang Cơ hội bán hàng.** Chức năng này yêu cầu chỉ xét các vé **đang mở** của một cơ hội bán hàng. Hai điều kiện dùng để loại vé đã kết thúc đều bị bỏ qua. Nghiêm trọng hơn, cả hai điều kiện này tham chiếu tới những trường **không tồn tại** trong cấu trúc dữ liệu vé — nên kể cả khi danh sách trắng cho qua, chúng vẫn vô nghĩa.

Hệ quả nghiệp vụ:
- Số lượng vé đang mở của cơ hội bán hàng bị tính sai, gồm cả vé đã giải quyết và đã đóng từ lâu.
- Một cơ hội bán hàng mà toàn bộ vé đã đóng xong vẫn tiếp tục hiển thị cờ cảnh báo rủi ro.
- **`BR-30.2` không bao giờ phát huy tác dụng**: quy tắc yêu cầu cảnh báo tự biến mất khi không còn vé nào thỏa điều kiện, nhưng điều kiện dừng chỉ xảy ra khi cơ hội bán hàng chưa từng có vé nào trong lịch sử. Nhân viên kinh doanh nhìn thấy cảnh báo rủi ro vĩnh viễn và sẽ học cách bỏ qua nó.

**2. Danh sách vé đã tách.** Chức năng tra cứu các vé được tách ra từ một vé gốc lọc theo trường liên kết ngược. Trường này **có tồn tại** trong cấu trúc dữ liệu nhưng không nằm trong danh sách trắng, nên cũng bị bỏ qua. Kết quả trả về là một trang vé bất kỳ của doanh nghiệp thay vì các vé đã tách — sai lệch `BR-41.2` về liên kết hai chiều.

**Vì sao không bị phát hiện:** kiểm thử hiện tại kiểm tra hàm truy vấn *được gọi với* tham số đúng, chứ không kiểm tra *kết quả trả về* có đúng phạm vi hay không. Đối tượng giả lập trả về danh sách đã dựng sẵn, nên việc điều kiện lọc bị bỏ qua không bao giờ lộ ra.

**Yêu cầu khắc phục:**
1. Bổ sung trường liên kết vé đã tách vào danh sách trường được phép lọc.
2. Sửa điều kiện lọc vé đang mở của chức năng cảnh báo rủi ro sang dùng đúng các trường hiện có trong cấu trúc dữ liệu vé.
3. Tầng truy vấn phải **báo lỗi rõ ràng** khi nhận điều kiện lọc không được hỗ trợ, thay vì bỏ qua im lặng. Đây là yêu cầu quan trọng nhất: cơ chế bỏ qua im lặng là nguyên nhân gốc khiến hai lỗi trên tồn tại mà không ai biết.

**Tiêu chí nghiệm thu:**
- Cơ hội bán hàng có vé nhưng tất cả đã đóng: không hiển thị cờ cảnh báo rủi ro.
- Số lượng vé đang mở khớp với số vé thực sự chưa kết thúc.
- Danh sách vé đã tách chỉ trả về đúng các vé tách ra từ vé gốc được hỏi.
- Gửi một điều kiện lọc không được hỗ trợ: hệ thống báo lỗi thay vì trả kết quả sai.

---

### GAP-08 — Đóng hàng loạt vé sự cố diện rộng không có chốt quyền cấp quản lý `[Chặn phát hành]`

**Quy tắc liên quan:** `BR-28.3` (Trưởng nhóm xác nhận khi đóng hàng loạt), `BR-28.4` (Vé con có vấn đề riêng phải xử lý độc lập)

**Hiện trạng nghiệp vụ:** Thao tác giải quyết vé cha kèm toàn bộ vé con chỉ được gác bằng quyền **chỉnh sửa vé** — quyền mà **mọi tư vấn viên đều có**. Không có chốt kiểm tra vai trò cấp quản lý, khác hẳn cách các tính năng cùng nhóm đang làm (duyệt giờ tính phí, xuất bản bài viết tri thức, khởi tạo phiên giám sát đều kiểm tra vai trò quản lý).

Hệ quả: một tư vấn viên bất kỳ có thể đóng hàng loạt toàn bộ vé con của một sự cố diện rộng — thao tác có thể ảnh hưởng hàng trăm khách hàng cùng lúc, và mỗi vé con đóng lại sẽ kích hoạt khảo sát hài lòng gửi tới khách hàng.

**Lỗ hổng dây chuyền với `BR-28.4`:** thao tác đánh dấu vé con "có vấn đề riêng" cũng chỉ gác bằng quyền chỉnh sửa vé. Nghĩa là bảo đảm của `BR-28.4` có thể bị vô hiệu hóa qua hai bước: gỡ cờ "có vấn đề riêng" của vé con, rồi đóng hàng loạt. Quy tắc bảo vệ vé con cần xử lý độc lập trở thành hình thức.

**Ghi nhận phần đang đúng:** bản thân logic `BR-28.4` được thực thi **phía máy chủ** rất chắc — vé con mang cờ "có vấn đề riêng" bị từ chối kèm thông báo rõ ràng, không chỉ là mặc định trên giao diện.

**Yêu cầu khắc phục:**
1. Thao tác giải quyết vé cha kèm vé con phải kiểm tra vai trò cấp quản lý, phân giải qua dịch vụ phân quyền.
2. Thao tác đánh dấu hoặc gỡ cờ "có vấn đề riêng" của vé con phải chịu cùng cấp quyền.

**Tiêu chí nghiệm thu:**
- Tư vấn viên thường bị từ chối thao tác đóng hàng loạt vé sự cố.
- Trưởng nhóm và Trưởng phòng Hỗ trợ thực hiện được.
- Tư vấn viên thường không gỡ được cờ "có vấn đề riêng" của vé con.

---

### GAP-09 — Người duyệt giờ tính phí được duyệt giờ của chính mình `[Rủi ro tài chính]`

**Quy tắc liên quan:** `BR-33.3` (Chỉ giờ đã được quản lý duyệt mới chuyển sang đối soát thanh toán)

**Hiện trạng nghiệp vụ:** Chốt kiểm tra quyền duyệt giờ tính phí hoạt động **đúng** — chỉ vai trò cấp quản lý mới duyệt được, và việc phân giải vai trò đi qua dịch vụ phân quyền theo đúng nguyên tắc.

Tuy nhiên **không có phép kiểm tra tách bạch trách nhiệm**: hệ thống không so sánh người duyệt với người đã ghi nhận giờ. Một Trưởng nhóm hoặc Trưởng phòng Hỗ trợ tự ghi giờ làm việc của mình rồi tự duyệt chính khoản giờ đó, và số giờ này đi thẳng vào tệp bàn giao cho đối soát thanh toán.

Đây là đường đi tới hóa đơn khách hàng, nên bước duyệt tồn tại chính là để có người thứ hai xác nhận. Khi người ghi và người duyệt là một, bước duyệt không còn tác dụng kiểm soát.

**Yêu cầu khắc phục:** Từ chối thao tác duyệt khi người duyệt trùng với người đã ghi nhận khoản giờ đó, kèm thông báo nêu rõ lý do. Trường hợp doanh nghiệp nhỏ chỉ có một quản lý cần có đường xử lý riêng (ví dụ cho phép cấp cao hơn duyệt), thống nhất khi triển khai.

**Tiêu chí nghiệm thu:**
- Quản lý tự ghi giờ rồi tự duyệt: bị từ chối.
- Quản lý duyệt giờ của người khác: thành công như hiện tại.
- Giờ chưa duyệt hoặc bị từ chối không xuất hiện trong tệp bàn giao đối soát.

---

### GAP-10 — Nhắc hậu trường và kết thúc phiên giám sát không có chốt quyền `[Rủi ro quyền riêng tư]`

**Quy tắc liên quan:** `BR-32.x` (Giám sát trực tiếp & Nhắc nhở hậu trường)

**Hiện trạng nghiệp vụ:** Thao tác **khởi tạo** phiên giám sát được bảo vệ đúng: kiểm tra tham số bật/tắt tính năng của doanh nghiệp, rồi kiểm tra vai trò cấp quản lý.

Nhưng hai thao tác còn lại trong cùng luồng — **gửi lời nhắc hậu trường** cho tư vấn viên và **kết thúc phiên giám sát** — không có chốt kiểm tra nào ngoài quyền chỉnh sửa vé. Hệ quả:
- Một tư vấn viên bất kỳ gửi được lời nhắc hậu trường vào vé mà người khác đang xử lý.
- Một tư vấn viên bất kỳ kết thúc được phiên giám sát do quản lý mở.
- Chức năng tra cứu trạng thái giám sát để lộ việc một tư vấn viên đang bị giám sát cho bất kỳ ai xem vé.

Điều này mâu thuẫn với chính lập luận đã ghi trong mã nguồn ở thao tác khởi tạo, rằng giám sát là một hành vi thuộc thẩm quyền quản lý.

**Yêu cầu khắc phục:**
1. Gửi lời nhắc hậu trường và kết thúc phiên giám sát phải chịu cùng cấp quyền với khởi tạo phiên, và cùng chịu tham số bật/tắt tính năng.
2. Tra cứu trạng thái giám sát chỉ trả về thông tin cho người có thẩm quyền giám sát.

**Tiêu chí nghiệm thu:**
- Tư vấn viên thường không gửi được lời nhắc hậu trường.
- Tư vấn viên thường không kết thúc được phiên giám sát của quản lý.
- Khi doanh nghiệp tắt tính năng giám sát, mọi thao tác trong luồng đều bị từ chối.

---

### GAP-11 — Chưa có quy tắc bắt buộc nhập nguyên nhân xử lý khi giải quyết vé `[Rủi ro số liệu]`

**Quy tắc liên quan:** `FEAT-19` (Quy trình Giải quyết & Ghi nhận Nguyên nhân Xử lý)

**Hiện trạng nghiệp vụ:** Không tồn tại điểm kiểm tra nào buộc nhập nguyên nhân xử lý khi chuyển vé sang trạng thái đã giải quyết. Trường nguyên nhân xử lý là trường tùy chọn ở mọi tầng: mô tả dữ liệu đầu vào, cấu trúc lưu trữ, và nhánh xử lý chuyển trạng thái chỉ ghi mốc thời gian mà không hề xét tới nó.

Cơ chế "đánh dấu trường bắt buộc theo bố cục màn hình" **không phủ được tình huống này**: khi trường không xuất hiện trong dữ liệu gửi lên, phép kiểm tra bắt buộc bị bỏ qua — mà thao tác giải quyết vé đúng là loại thao tác chỉ gửi lên trường trạng thái. Danh mục trạng thái vé cũng không có thuộc tính nào để gắn quy tắc này vào.

Hành vi duy nhất hiện có liên quan tới nguyên nhân xử lý là **xóa** nó khi vé được mở lại.

**Hệ quả nghiệp vụ:** mọi vé đều có thể được giải quyết mà không ghi nguyên nhân. Báo cáo phân tích nguyên nhân sự cố mất giá trị vì tỷ lệ dữ liệu trống không kiểm soát được.

**Ghi chú về phạm vi:** tài liệu gốc mô tả `FEAT-19` là đã triển khai nhưng **không phát biểu tường minh** quy tắc bắt buộc nhập. Vì vậy đây vừa là khoảng cách triển khai, vừa là khoảng cách đặc tả. Cần chốt yêu cầu nghiệp vụ trước khi triển khai: bắt buộc cho mọi trạng thái kết thúc, hay chỉ cho trạng thái "đã giải quyết", và có cho phép doanh nghiệp tắt quy tắc này không.

**Yêu cầu khắc phục:**
1. Bổ sung phát biểu quy tắc nghiệp vụ tường minh vào `FEAT-19` của tài liệu gốc.
2. Bổ sung thuộc tính cấu hình trên danh mục trạng thái vé cho phép đánh dấu trạng thái nào đòi nguyên nhân xử lý.
3. Kiểm tra khi chuyển trạng thái, lấy giá trị hiện có trên vé làm căn cứ nếu dữ liệu gửi lên không kèm trường này.

**Tiêu chí nghiệm thu:**
- Chuyển vé sang trạng thái đã cấu hình là bắt buộc mà không có nguyên nhân xử lý: bị từ chối.
- Vé đã có sẵn nguyên nhân xử lý: chuyển trạng thái thành công mà không cần gửi lại.
- Trạng thái không được đánh dấu bắt buộc: giữ nguyên hành vi hiện tại.

---

### GAP-12 — Tự động mở lại vé khi khách hàng phản hồi bị lỗi khi chạy `[Chặn phát hành]`

**Quy tắc liên quan:** `BR-21.x` (Mở lại vé), `BR-22.2` (Hủy hẹn đóng tự động khi khách hàng phản hồi)

**Hiện trạng nghiệp vụ:** Khi khách hàng phản hồi vào một vé đã kết thúc, tiến trình nền phải mở lại vé đó. Tiến trình này chạy trong ngữ cảnh nền chỉ thiết lập **định danh doanh nghiệp**, không thiết lập **định danh người dùng**. Trong khi đó, chốt kiểm tra quyền của thao tác mở lại vé đòi phải có định danh người dùng và **ném lỗi** khi không có.

Lỗi phát sinh được bắt lại và chỉ ghi vào nhật ký, nên không có dấu hiệu nào lộ ra bên ngoài.

**Hệ quả nghiệp vụ:** phần hủy hẹn đóng tự động vẫn chạy đúng (nằm trước điểm lỗi), nhưng **vé không được mở lại**. Khách hàng phản hồi vào vé đã đóng và yêu cầu của họ không quay lại hàng đợi xử lý — không ai được giao, không đồng hồ cam kết nào chạy. Đây là tình huống bỏ sót yêu cầu khách hàng, không phải lỗ hổng bảo mật: hệ thống từ chối theo hướng an toàn, nhưng nghiệp vụ thì đứt.

**Vì sao không bị phát hiện:** kiểm thử của tiến trình nền này thay thế toàn bộ dịch vụ vé bằng đối tượng giả lập, nên chốt kiểm tra quyền thật không bao giờ được gọi. Đây là cùng một dạng lỗi với `GAP-01` — chốt quyền đọc thứ không tồn tại trong ngữ cảnh — chỉ khác ở chỗ thiếu định danh người dùng thay vì thiếu vai trò.

**Yêu cầu khắc phục:** Thao tác mở lại vé do hệ thống tự thực hiện cần một đường đi riêng cho tác nhân hệ thống, không đi qua chốt kiểm tra quyền dành cho người dùng, đồng thời vẫn ghi vết rõ ràng rằng thao tác do hệ thống thực hiện.

**Tiêu chí nghiệm thu:**
- Khách hàng phản hồi vào vé đã giải quyết trong thời hạn cho phép: vé được mở lại, số lần mở lại tăng đúng.
- Quá thời hạn cho phép: tạo vé tiếp nối như quy tắc hiện hành, không mở lại.
- Thao tác mở lại do hệ thống thực hiện được ghi vết với tác nhân là hệ thống.
- Phải có kiểm thử chạy trên ngữ cảnh nền thật, không thay thế dịch vụ vé bằng đối tượng giả lập.

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

| Thứ tự | Mã | Nhóm | Lý do |
|---|---|---|---|
| 1 | `GAP-07` | Dữ liệu sai | Trả về dữ liệu sai phạm vi ở hai chức năng; `BR-30.2` không bao giờ chạy được. Nguyên nhân gốc — bỏ qua điều kiện lọc im lặng — còn có thể đang ảnh hưởng chỗ khác chưa phát hiện |
| 2 | `GAP-01` | Quyền | Chặn nghiệp vụ; cách khắc phục tạm tại hiện trường là nâng quyền Quản trị viên, sai nguyên tắc |
| 3 | `GAP-08` | Quyền | Tư vấn viên bất kỳ đóng được hàng loạt vé sự cố diện rộng, ảnh hưởng hàng trăm khách hàng |
| 4 | `GAP-12` | Bỏ sót khách hàng | Khách hàng phản hồi vào vé đã đóng nhưng yêu cầu không quay lại hàng đợi |
| 5 | `GAP-09` | Tài chính | Đường đi tới hóa đơn khách hàng; bước duyệt mất tác dụng kiểm soát |
| 6 | `GAP-10` | Quyền riêng tư | Giám sát và nhắc hậu trường không đúng thẩm quyền |
| 7 | `GAP-03` | Toàn vẹn | Rủi ro toàn vẹn dữ liệu, phạm vi sửa nhỏ và khu trú |
| 8 | `GAP-02` | Số liệu | Sai lệch báo cáo theo trạng thái, không mất dữ liệu |
| 9 | `GAP-11` | Số liệu + đặc tả | Cần chốt yêu cầu nghiệp vụ trước khi triển khai |
| 10 | `GAP-05` | Chất lượng | Ngăn ngừa tái diễn; nên làm song song với các mục trên |
| 11 | `GAP-06` | Tài liệu | Cần hoàn thành trước khi nghiệm thu phát hành |
| 12 | `GAP-04` | Tính năng | Đã biết từ trước, không chặn vận hành |

**Nhóm theo bản chất, để chia việc:**

- **Chốt quyền sai hoặc thiếu** — `GAP-01`, `GAP-08`, `GAP-09`, `GAP-10`, `GAP-12`. Năm mục cùng một họ: chốt quyền đọc thứ không có trong ngữ cảnh, hoặc không có chốt nào. Nên làm cùng đợt để áp dụng nhất quán một cách phân giải vai trò.
- **Truy vấn trả sai phạm vi** — `GAP-07`, `GAP-02`. Cùng liên quan tới việc điều kiện lọc và trạng thái không phản ánh đúng thực tế dữ liệu.
- **Toàn vẹn giao dịch** — `GAP-03`.
- **Đặc tả và tài liệu** — `GAP-11`, `GAP-06`, `GAP-04`.
- **Hạ tầng kiểm thử** — `GAP-05`.

---

## 5. Phạm vi không thuộc đợt này

- `BR-40.1` tầng 1 (chính sách cam kết theo hợp đồng dịch vụ): phụ thuộc hai thực thể ngoài phân hệ chưa tồn tại — hợp đồng dịch vụ và trường gán chính sách cho khách hàng. Không tự giải quyết được trong phạm vi Vé Hỗ trợ.
- Cơ chế đẩy tức thời cho bảng điều khiển hàng đợi (`FEAT-42`): ghi nhận là khoảng cách, đề xuất tách thành yêu cầu riêng vì chạm tới hạ tầng kết nối thời gian thực.
- Chính sách bồi thường khi vi phạm cam kết: thuộc phân hệ Hợp đồng & Thanh toán.
