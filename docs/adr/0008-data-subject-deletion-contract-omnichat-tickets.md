---
status: accepted
---

# Hợp đồng nghiệp vụ giữa Contacts, Omnichat và Tickets: thực thi quyền xoá theo mã khách hàng

**Bối cảnh:** `contacts-srs.md` BR-33.8 đặt **sàn liên tài liệu** cho `omnichat-srs.md` (dòng h) và `tickets-srs.md` (dòng i): hai phân hệ đó phải có cơ chế thực thi quyền xoá theo mã khách hàng, hoàn tất trong cùng thời hạn của yêu cầu, và trả về xác nhận để đưa vào Biên bản Hoàn tất Xử lý. Nhưng cả hai tài liệu được tuyên bố **ngoài phạm vi** ở mục 1.2 và **không có vòng ký**, nên sàn này chưa có ai cam kết. Vấn đề #12 mục 7 giao chốt theo đúng cách đã làm với tài liệu IAM ở vấn đề #8.

Khảo sát hai tài liệu cho một kết quả **bất đối xứng** mà chính vấn đề #12 không lường trước: hai dòng (h) và (i) **không ở cùng một trạng thái**, nên xử lý chúng như nhau sẽ sai ở cả hai chiều.

## Ba phát hiện khi khảo sát

**Phát hiện 1 — dòng (h) đã được phủ gần như nguyên văn, từ trước.**

`omnichat-srs.md` đã có **BR-23.3**:

> *"Khi một khách hàng yêu cầu xóa dữ liệu cá nhân của họ, doanh nghiệp PHẢI thực hiện được yêu cầu đó trên toàn bộ hội thoại của khách hàng đó ở mọi kênh **trong một lần thao tác**, và PHẢI **nhận được xác nhận việc xóa đã hoàn tất** để trả lời lại khách hàng."*

Đối chiếu từng vế với sàn BR-33.8 đặt ra:

| Sàn BR-33.8 (dòng h) yêu cầu | `omnichat-srs.md` BR-23.3 |
| --- | --- |
| Cơ chế thực thi quyền xoá **theo mã khách hàng** | "trên toàn bộ hội thoại của khách hàng đó ở mọi kênh" |
| **Trong một lần thao tác** | "trong một lần thao tác" |
| **Trả về xác nhận** để đưa vào Biên bản | "nhận được xác nhận việc xóa đã hoàn tất" |
| Gồm **tệp đính kèm** | BR-23.1 đặt nội dung và tệp đính kèm cùng một chế độ |

Ghi dòng (h) là "chưa cam kết" sẽ là **một phát biểu sai về hiện trạng tài liệu**.

**Phát hiện 2 — dòng (i) thật sự chưa có gì.**

`tickets-srs.md` **không có** quy tắc nào về quyền chủ thể dữ liệu. Thứ gần nhất là `CFG-TCK-08` — số ngày giữ Vé trong Thùng rác trước khi xoá vĩnh viễn. Đó là **chính sách dọn rác theo thời gian**, không phải **thực thi một yêu cầu của khách hàng theo mã khách hàng**: nó không khởi động được từ một yêu cầu, không chạy theo mã khách, không có thời hạn gắn với yêu cầu, và không trả về xác nhận.

Ghi dòng (i) là "đã cam kết" sẽ sai đúng bằng chiều ngược lại.

**Phát hiện 3 — mã BR trùng số giữa hai tài liệu.**

`omnichat-srs.md` **đã có một `BR-33.8` của riêng nó**, và nó nói về **ghi nhật ký thay đổi quy tắc tự động hoá** — không liên quan gì tới quyền chủ thể dữ liệu. Không gian đánh số BR là **độc lập theo từng tài liệu**.

Hệ quả bắt buộc: mọi tham chiếu liên tài liệu phải viết **đủ tên tài liệu** (`omnichat-srs.md § BR-23.3`), **không bao giờ** viết mã trần. Một câu như "theo BR-33.8" trong ngữ cảnh liên tài liệu là **mơ hồ có thật**, không phải rủi ro lý thuyết.

## Xung đột thật: Tạm dừng xoá theo yêu cầu pháp lý

`omnichat-srs.md` **BR-23.7** cho phép đặt **Tạm dừng xoá theo yêu cầu pháp lý (Legal Hold)** lên dữ liệu của một khách hàng đang trong diện tranh chấp, và trong thời gian đó **BR-23.3 KHÔNG được thi hành**.

Điều này **mâu thuẫn trực tiếp** với kỳ vọng của Biên bản Hoàn tất Xử lý, vốn chờ một xác nhận "đã xử lý xong" cho dòng (h). Khi một hội thoại đang bị tạm dừng:

- Nói **"đã xoá"** là **sai sự thật** — dữ liệu vẫn còn.
- **Im lặng** là vi phạm chính nguyên tắc mở đầu BR-33.8: không để doanh nghiệp trả lời khách "đã xoá xong" trong khi dữ liệu còn ở nơi khác.

Bảng 12 hàng hiện tại **không có từ vựng** cho tình huống này — bốn trạng thái đang có là *Đã xoá / Đã khử định danh / Đã thu hồi / Được giữ theo nghĩa vụ pháp lý*. Trạng thái thứ tư gần nhất nhưng mô tả một **nghĩa vụ lưu trữ vĩnh viễn** (nhật ký kiểm toán), còn tạm dừng pháp lý là **hoãn có thời hạn, sẽ thi hành lại khi gỡ**. Đánh đồng hai thứ sẽ khiến một lần hoãn trông như một lần từ chối vĩnh viễn.

## Quyết định

**Điều khoản 1 — Dòng (h) `omnichat-srs.md`: ĐÃ CAM KẾT.**
Sàn được coi là đã thoả bởi `omnichat-srs.md § BR-23.3`, với tệp đính kèm thuộc cùng chế độ theo `§ BR-23.1`. Không cần thêm quy tắc mới ở tài liệu đó cho phần này.

**Điều khoản 2 — Dòng (i) `tickets-srs.md`: CHƯA CAM KẾT.**
Tài liệu đó chưa có cơ chế nào thoả sàn. `CFG-TCK-08` **không** được tính là đã thoả, vì nó là dọn rác theo thời gian chứ không phải thực thi yêu cầu theo mã khách hàng.

Cho tới khi chủ sở hữu `tickets-srs.md` bổ sung quy tắc tương đương và xác nhận, **Biên bản Hoàn tất Xử lý bắt buộc nêu rõ phần chưa phủ** ở dòng (i), bằng chính từ ngữ nói rằng nội dung Vé hỗ trợ của khách hàng **chưa nằm trong phạm vi được bảo đảm**. Hệ thống **không** được phát hành một biên bản ngụ ý đã phủ hết.

**Điều khoản 3 — Trạng thái thứ năm cho Biên bản: "Đang tạm dừng theo yêu cầu pháp lý".**
Bảng trạng thái của Biên bản Hoàn tất Xử lý bổ sung trạng thái này, kèm **căn cứ** và **thời điểm dự kiến gỡ** khi có. Nó khác "Được giữ theo nghĩa vụ pháp lý" ở chỗ: trạng thái kia là **vĩnh viễn theo thiết kế**, trạng thái này là **hoãn có điều kiện** và yêu cầu sẽ được thi hành lại khi tạm dừng được gỡ.

Kèm theo: khi một phần dữ liệu rơi vào trạng thái này, hệ thống phải **giữ lại yêu cầu xoá ở trạng thái chưa hoàn tất** cho phần đó, để lần gỡ tạm dừng có thứ để thi hành. Một yêu cầu bị đóng lại vì "đã xử lý xong các phần còn lại" sẽ khiến phần bị hoãn **không bao giờ được xoá**.

**Điều khoản 4 — Tham chiếu liên tài liệu viết đủ tên tài liệu.**
Vì không gian mã BR độc lập theo tài liệu và đã có va chạm thật (`BR-33.8` tồn tại ở cả hai tài liệu với hai nghĩa khác nhau), mọi tham chiếu chéo giữa ba tài liệu này viết dạng `<tên tệp> § <mã>`. Mã trần chỉ dùng cho tham chiếu **trong cùng một tài liệu**.

**Điều khoản 5 — Điều kiện để dòng (i) chuyển sang "đã cam kết".**
Chủ sở hữu `tickets-srs.md` bổ sung một quy tắc thoả **cả bốn** vế: (a) khởi động được **theo mã khách hàng**; (b) **khử định danh** nội dung vé, giữ số liệu vận hành (thời gian xử lý, phân loại) theo đúng dòng (i) của bảng BR-33.8; (c) **xoá tệp đính kèm** do khách gửi; (d) **trả về xác nhận** để đưa vào Biên bản. Khi đó ADR này được cập nhật và điều khoản 2 hết hiệu lực.

## Hệ quả

- **Nghiệm thu Kịch bản 17** bước (h) nghiệm thu được ngay theo `omnichat-srs.md § BR-23.3`; bước (i) **chưa nghiệm thu được**, và đó là kết quả đúng chứ không phải lỗi kịch bản.
- **FEAT-33 triển khai được lên môi trường thật** với điều kiện Biên bản nêu rõ phần chưa phủ ở dòng (i). Đây là điều mà chính vấn đề #12 đã dự liệu: *"không để sàn treo không ai chịu"*.
- **Rủi ro còn lại, ghi rõ:** một khách hàng thực thi quyền xoá vẫn còn dữ liệu cá nhân trong nội dung Vé hỗ trợ. Rủi ro này **không được che giấu bằng một biên bản im lặng** — nó hiện trên biên bản, để doanh nghiệp biết mình đang cam kết tới đâu với khách.

## Phương án đã cân nhắc và loại

**Ghi cả hai dòng là "chưa cam kết" cho gọn.** Loại: sai sự thật với `omnichat-srs.md`, và sẽ khiến đội phát triển dựng lại một cơ chế đã được đặc tả — đúng loại lãng phí mà việc khảo sát trước khi ước lượng nhằm tránh.

**Chờ cả hai chủ sở hữu ký rồi mới chốt.** Loại: hai tài liệu **không có vòng ký** để chờ. Chờ một chữ ký không tồn tại là cách chắc chắn nhất để sàn treo vô thời hạn — đúng kết cục vấn đề #12 yêu cầu tránh.

**Tự bổ sung quy tắc vào `tickets-srs.md`.** Loại: tài liệu đó ngoài phạm vi của tài liệu này, và viết quy tắc thay chủ sở hữu là lặp lại chính vấn đề — một cam kết không ai nhận.

---

**Xác nhận:** 2026-09-12. Cùng lý do như [ADR-0007](./0007-record-sharing-and-permission-precedence-contract.md): `omnichat-srs.md` và `tickets-srs.md` **đều không có vòng ký** để chờ, và chính vấn đề #12 mục 7 yêu cầu không để sàn treo không ai chịu.

Hợp đồng đã được thi hành ở FEAT-33 ([#204](https://github.com/crmsaassaudi/product-management/issues/204)): điều khoản 2 hiện thành cờ `covered: false` trên dòng Vé hỗ trợ của Biên bản Hoàn tất Xử lý, và điều khoản 3 thành trạng thái thứ năm `suspendedByLegalHold` giữ yêu cầu ở trạng thái chưa hoàn tất. Cả hai có test khoá trong `completion-record.spec.ts`.

**Điều khoản 2 vẫn là một cam kết còn mở về phía `tickets-srs.md`** — trạng thái `accepted` ở đây nghĩa là *hợp đồng đã chốt*, không phải *sàn đã được phủ*. Khi chủ sở hữu tài liệu đó bổ sung quy tắc thoả bốn vế tại điều khoản 5, ADR này được cập nhật và điều khoản 2 hết hiệu lực.
