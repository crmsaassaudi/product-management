---
status: accepted
amendment_status: accepted
amendment_date: 2026-08-24
amendment_approved_by: Nguyễn Phước Đại Toàn (PO + Solution Architect)
---

# Xung đột phân quyền trường khi user thuộc nhiều Nhóm quyền: hạn chế thắng, không cộng gộp

Trong Object Manager, phân quyền trường (FLS) được cấu hình theo Nhóm quyền, và một người dùng có thể thuộc nhiều nhóm cùng lúc. Khi các nhóm đó có cấu hình khác nhau trên cùng một trường, hệ thống phải chọn cấu hình nào áp dụng. Chúng tôi chọn **hạn chế thắng (deny-override)** cho các thuộc tính bảo mật — ẩn thắng hiển thị, chỉ đọc thắng xem&sửa, mức che dữ liệu mạnh hơn thắng mức yếu hơn — thay vì cộng gộp mở rộng (permissive-override, quyền cao nhất trong các nhóm thắng).

Lý do: một người dùng có thể bị thêm vào một nhóm khác vì lý do không liên quan (dự án chéo phòng ban, nhóm tạm thời) mà không có chủ đích cấp thêm quyền xem dữ liệu nhạy cảm; cộng gộp mở rộng sẽ khiến việc thêm nhóm này vô tình mở khóa dữ liệu PII/tài chính mà nhóm chính của họ đang hạn chế. Với CRM xử lý dữ liệu khách hàng và tài chính, rủi ro lộ dữ liệu do cộng dồn quyền không kiểm soát được coi là nghiêm trọng hơn chi phí vận hành (admin phải rút user khỏi nhóm hạn chế để nâng quyền thay vì chỉ thêm nhóm mới).

Yêu cầu "bắt buộc nhập" (không phải thuộc tính bảo mật mà là ràng buộc chất lượng dữ liệu) vẫn hợp nhất theo **cộng gộp** — nếu bất kỳ nhóm nào yêu cầu bắt buộc thì trường đó bắt buộc với người dùng đó. *(Quy tắc cộng gộp này được giới hạn lại bởi phần **Bổ sung 2026-08-23**, mục 2 — nó không áp dụng khi người dùng không có quyền nhập chính trường đó.)*

## Phương án đã cân nhắc

- **Cộng gộp mở rộng (Additive/Permissive-override)** cho cả thuộc tính bảo mật — bị loại vì rủi ro lộ dữ liệu nêu trên; đây cũng là mô hình bị nhiều audit bảo mật CRM/ERP gắn cờ khi phát hiện.
- **Nhóm có độ ưu tiên tường minh** (admin xếp hạng nhóm nào thắng khi chồng lấn) — linh hoạt nhất nhưng là cơ chế mới, thêm một trường cấu hình và logic phân giải phức tạp hơn; hoãn lại, không loại trừ cho tương lai nếu nhu cầu vận hành thực tế đòi hỏi.

## Hệ quả

Để nâng quyền truy cập một trường cho một người dùng cụ thể (ví dụ thăng chức), quản trị viên phải rút người đó khỏi nhóm đang hạn chế trường đó hoặc tổ chức lại nhóm — không thể chỉ thêm họ vào một nhóm quyền mở rộng hơn trong khi vẫn giữ ở nhóm cũ. Hướng dẫn này được ghi trong SRS Object Manager (BR-03.2).

---

## Bổ sung 2026-08-23 — Mô hình hai chiều, thứ tự ưu tiên với ràng buộc bắt buộc, và phạm vi áp dụng

> **TRẠNG THÁI: ĐÃ DUYỆT (accepted) ngày 2026-08-24.** Cả bốn điều khoản dưới đây được thông qua nguyên văn, không sửa đổi, và có hiệu lực ngang với phần gốc. Phần gốc phía trên không thay đổi.
>
> **Ý kiến Solution Architect về điều khoản 1 (điểm chặn trước khi duyệt):** điều khoản này quy định cấu hình phân quyền trường phải lưu **hai thuộc tính độc lập** cho mỗi cặp (trường, nhóm), thay vì một giá trị bốn trạng thái như cách hiểu ban đầu. Kết luận khi rà soát dữ liệu cấu hình đang tồn tại: **không cần ghi lại dữ liệu của tenant.** Chiều *Mức hiển thị giá trị* vốn đã được lưu tách bạch từ đầu; chiều *Mức truy cập* ở các cấu hình cũ được diễn đạt bằng hai dấu hiệu riêng lẻ (trường có hiện trên form hay không, và trường có bị khóa sửa hay không) thay vì một mức duy nhất. Cách hiểu bốn trạng thái là **cách hiểu ban đầu về mô hình, chưa từng là hình thức lưu trữ**. Đổi lại, việc đọc hiểu cách diễn đạt cũ trở thành điều kiện nghiệm thu bắt buộc — xem MIG-07 tại SRS Object Manager Mục 7.3.
>
> **Vì sao phải đóng sớm (lý do khi còn ở trạng thái đề xuất):** ba quy tắc BR-05.5, BR-07.3 và BR-09.2 của SRS Object Manager cùng phụ thuộc vào điều khoản 2. Nếu chưa chốt mà đã code, mỗi người sẽ tự suy diễn một cách hiểu khác nhau (xem lộ trình Sprint R1 tại SRS Object Manager Mục 7.2).

Quyết định gốc ở trên chỉ trả lời câu hỏi *"cấu hình nào thắng"* cho các thuộc tính bảo mật. Quá trình phản biện chéo SRS Object Manager (v2.2 → v3.0) phát hiện bốn tình huống mà quyết định gốc không xử lý được, trong đó có một tình huống **hai điều khoản của chính ADR này phủ định nhau**. Bốn bổ sung dưới đây là phần không thể tách rời của quyết định, có hiệu lực ngang với phần gốc.

### 1. Tách hai chiều độc lập: Mức truy cập và Mức hiển thị giá trị

Quyết định gốc gộp *che dữ liệu* vào cùng một thang với *ẩn / chỉ đọc / xem&sửa*. Cách gộp này sai về mô hình: che dữ liệu là **cách trình bày giá trị**, không phải **mức được phép làm gì với trường**. Hệ quả của việc gộp là câu "mức che mạnh hơn thắng mức yếu hơn" không có thang bậc để so, và hai tổ hợp nghiệp vụ có thật không diễn đạt được: nhân viên CSKH *được sửa* số thẻ khách hàng nhưng *không được đọc* giá trị cũ; nhân viên kế toán *chỉ xem* nhưng được đọc *đầy đủ*.

Chính sách của một trường đối với một Nhóm quyền vì vậy gồm **hai chiều tách biệt, hợp nhất độc lập với nhau**, mỗi chiều đều theo nguyên tắc hạn chế thắng:

- **Mức truy cập** (chọn đúng một): Xem & Sửa › Chỉ xem › Ẩn.
- **Mức hiển thị giá trị** (áp dụng khi mức truy cập không phải Ẩn): Hiện đầy đủ › Che một phần › Che hoàn toàn.

### 2. Quyền truy cập thắng ràng buộc bắt buộc nhập

Đây là điểm hai điều khoản của quyết định gốc phủ định nhau: đoạn thứ nhất nói *ẩn thắng hiển thị* (hạn chế thắng), đoạn thứ ba nói *bắt buộc nhập cộng gộp*. Khi Nhóm A ẩn một trường và Nhóm B đặt trường đó là bắt buộc, một người thuộc cả hai nhóm rơi vào thế bế tắc: hệ thống không cho họ thấy trường, nhưng cũng không cho họ lưu bản ghi. Trên thực tế nhân viên đó bị kẹt vĩnh viễn, chỉ quản trị viên mới gỡ được.

Quyết định: **chiều Mức truy cập được phân giải trước, và ràng buộc bắt buộc nhập được miễn trừ đối với riêng người dùng không có quyền nhập trường đó** (mức truy cập là Ẩn hoặc Chỉ xem). Hệ thống không bao giờ được yêu cầu một người nhập trường mà chính họ không được nhìn thấy hoặc không được sửa. Bản ghi khi đó vẫn lưu được nhưng bị gắn cờ *"Thiếu dữ liệu bắt buộc do giới hạn quyền"* để người có thẩm quyền bổ sung sau (SRS Object Manager BR-05.5).

Lý do chọn hướng này thay vì chặn lưu: ràng buộc bắt buộc nhập tồn tại để bảo đảm **chất lượng dữ liệu**, còn phân quyền trường tồn tại để bảo đảm **an toàn dữ liệu**. Khi hai mục tiêu xung đột, an toàn phải thắng — nhất quán với chính lý do của quyết định gốc. Đổi lại, ta chấp nhận rủi ro dữ liệu thiếu, và bù bằng cơ chế gắn cờ có thông báo và có người nhận trách nhiệm, chứ không bù bằng cách chặn đứng công việc của nhân viên tuyến đầu.

Ràng buộc bắt buộc vẫn cộng gộp đầy đủ như quyết định gốc đối với **mọi người dùng có quyền nhập trường** — bổ sung này chỉ khoanh một ngoại lệ hẹp, không đảo ngược nguyên tắc.

### 3. Không có cấu hình không đồng nghĩa với được phép

Quyết định gốc không nói gì về trường hợp một người thuộc nhiều nhóm nhưng chỉ **một** nhóm có cấu hình cho trường đang xét. Nếu coi sự im lặng của các nhóm còn lại là "không hạn chế", thì bất kỳ ai chỉ cần được thêm vào một nhóm không cấu hình gì là vô hiệu hóa được hạn chế của nhóm chính — đúng lỗ hổng mà quyết định gốc muốn bịt.

Quyết định: **sự vắng mặt cấu hình không bao giờ được tính là một sự cho phép.** Chỉ các nhóm thực sự có cấu hình cho trường đó tham gia phân giải. **Bố cục mặc định (Default Layout)** chỉ được áp dụng khi người dùng không thuộc *bất kỳ* nhóm nào có cấu hình cho trường đó; nó không tham gia phân giải cùng các nhóm khác như một chính sách ngang hàng.

### 4. Phạm vi áp dụng: chỉ người dùng, không áp cho tác vụ tự động và không áp cho quản trị viên

Quyết định gốc không khoanh phạm vi chủ thể, khiến người đọc chỉ đọc ADR này có thể áp nguyên tắc hạn chế thắng cho cả tác vụ tự động và làm đứt các luồng đồng bộ dữ liệu. Khoanh lại:

- **Tác vụ tự động và tích hợp bên ngoài** được **miễn trừ** phân quyền trường (SRS Object Manager BR-03.4) — chúng vẫn phải tuân thủ đầy đủ các quy tắc kiểm tra dữ liệu, và không được dùng quyền miễn trừ để sao chép giá trị từ trường bảo vệ cao sang trường bảo vệ thấp hơn.
- **Quản trị viên tenant** **không bị** phân quyền trường giới hạn (SRS Object Manager BR-03.2b), vì họ có quyền tự sửa cấu hình nên việc áp FLS lên họ chỉ tạo an toàn giả. Điều này **không** vi phạm nguyên tắc chống leo thang đặc quyền tại [ADR-0002](./0002-delegated-grant-authority-ceiling-exception.md): quản trị viên không "leo" lên năng lực mới, họ vốn đã ở cấp cao nhất trong tenant. Hệ quả thương mại phải được nêu minh bạch: **phân quyền trường là công cụ phân tách trách nhiệm giữa các phòng ban, không phải công cụ che dữ liệu khỏi quản trị viên.** Khách hàng cần giới hạn cả quản trị viên (thường thuộc tài chính, y tế) phải được tư vấn giải pháp khác, không được cam kết bằng FLS.

### Hệ quả bổ sung

- Mô hình dữ liệu cấu hình phân quyền trường phải lưu **hai thuộc tính độc lập** cho mỗi cặp (trường, nhóm), không phải một giá trị enum bốn trạng thái. Đây là thay đổi so với cách hiểu ban đầu và cần được xử lý trước khi hiện thực hóa BR-05.5, BR-07.3 và BR-09.2 — cả ba cùng phụ thuộc vào trục logic ở mục 2.
- Công cụ *Xem trước quyền thực tế* (BR-03.3) phải hiển thị được cả hai chiều và nêu rõ khi một ràng buộc bắt buộc đã bị miễn trừ theo mục 2, nếu không quản trị viên sẽ không hiểu vì sao dữ liệu vẫn thiếu dù đã đặt bắt buộc.
- Cơ chế gắn cờ ở mục 2 chỉ có giá trị nếu có người nhận trách nhiệm: cờ phải kèm thông báo tới nhóm có quyền nhập và phải tự động xóa khi dữ liệu được bổ sung, tránh tồn đọng vô thời hạn.

## Lịch sử quyết định

| Ngày | Trạng thái | Thay đổi |
| --- | --- | --- |
| 2026-08-21 | **accepted** | Quyết định gốc: hạn chế thắng cho thuộc tính bảo mật, cộng gộp cho ràng buộc bắt buộc nhập. |
| 2026-08-23 | **proposed** | Bổ sung bốn điều khoản: tách hai chiều truy cập/hiển thị; quyền truy cập thắng ràng buộc bắt buộc; vắng mặt cấu hình không phải sự cho phép; khoanh phạm vi chủ thể áp dụng. Phần gốc được giữ nguyên văn; điều khoản cộng gộp ở đoạn 3 được giới hạn lại bởi mục 2. |
| 2026-08-24 | **accepted** | **Thông qua cả bốn điều khoản, nguyên văn, không sửa đổi.** Người duyệt: Nguyễn Phước Đại Toàn (Product Owner kiêm Solution Architect — issue [#29](https://github.com/crmsaassaudi/product-management/issues/29)). Điểm chặn của điều khoản 1 được kết luận: không cần ghi lại dữ liệu cấu hình của tenant, đổi lại việc đọc hiểu cách diễn đạt cũ trở thành điều kiện nghiệm thu (MIG-07, SRS Object Manager Mục 7.3). Bốn điều khoản được đưa vào phần thân SRS Object Manager v4.3 (BR-03.1, BR-03.2, BR-03.2b, BR-03.4, BR-05.5) để phần dùng nghiệm thu không còn phải tra cứu sang ADR. |
