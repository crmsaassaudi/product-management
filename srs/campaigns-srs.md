# SRS — Phân hệ Quản lý Chiến dịch Tiếp thị & Truyền thông Đa kênh (Marketing Campaigns & Mass Messaging)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA (Version 6.6) |
| **Module** | CRM — Phân hệ Quản lý Chiến dịch Tiếp thị & Truyền thông Đa kênh (Marketing Campaigns & Mass Messaging) |
| **Ngày cập nhật** | 2026-10-02 |
| **Phiên bản** | v6.6 (Chuẩn hóa Nghiệp vụ Thuần túy — v6.6: công việc liên hệ lại tạo thay người khởi chạy, hai đường chuyển của người phụ trách, phạm vi danh sách tin gắn nhãn, quyền quản trị cụ thể thay cho Quản trị viên; v6.5: đối tượng theo tập khách hàng xem được của phân quyền nền tảng, ma trận mặc định chi tiết của mọi vai trò dựng sẵn, công việc liên hệ lại theo quy tắc hàng đợi của phân hệ Công việc, cá nhân hóa và gửi thử theo quyền xem trường; v6.4: đồng bộ phân quyền nền tảng v5.0: năm mức truy cập, vai trò dựng sẵn và điều chỉnh ô theo tài liệu phân quyền; Phát sóng là thao tác đặc thù hiển thị riêng; đối tượng giới hạn theo mức Xem Khách hàng của người chọn đối tượng và của người khởi chạy; tiến trình gửi chạy thay người khởi chạy; tạm ngưng, rời workspace và bàn giao theo quy trình chung; khai báo hàng đợi công việc liên hệ lại; thao tác dừng không bị chặn khi nhật ký gặp sự cố) |
| **Neo mã nguồn** | Chưa xác định — tài liệu đặc tả trạng thái nghiệp vụ mục tiêu, không neo vào một phiên bản triển khai cụ thể |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`contacts-srs.md`](./contacts-srs.md), [`omnichat-srs.md`](./omnichat-srs.md), [`deals-pipeline-srs.md`](./deals-pipeline-srs.md), [`billing-subscription-srs.md`](./billing-subscription-srs.md), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`object-manager-srs.md`](./object-manager-srs.md), [`tasks-srs.md`](./tasks-srs.md) |

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả toàn bộ nghiệp vụ lập kế hoạch, chọn đối tượng, soạn nội dung, kiểm duyệt, phát sóng, theo dõi và đo lường các chiến dịch tiếp thị gửi hàng loạt qua năm kênh **Email**, **WhatsApp**, **Zalo ZNS**, **Zalo OA** và **SMS**, bảo đảm ba yêu cầu không thể thỏa hiệp:

1. **Tuân thủ đồng thuận và chính sách gửi tiếp thị của doanh nghiệp:** không một thông điệp tiếp thị nào tới được người đã từ chối nhận tin, kể cả khi họ từ chối giữa lúc chiến dịch đang chạy.
2. **Kiểm soát rủi ro phát sóng diện rộng:** một sai sót nội dung không được phép tới tay hàng chục nghìn khách hàng chỉ vì một người thao tác nhầm.
3. **Đo lường trung thực:** mọi con số báo cáo có định nghĩa và mẫu số thống nhất, đối soát được tới từng người nhận.

### 1.2 Phạm vi

Tài liệu bao gồm 13 nhóm chức năng:

- **Nhóm A — Quản trị Chiến dịch:** tạo, sửa, vòng đời trạng thái, mã định danh, nhân bản, xóa và lưu trữ.
- **Nhóm B — Đối tượng Nhận tin:** tiêu chí chọn đối tượng, xem trước quy mô, xem mẫu, quy tắc loại trừ, khử trùng lặp, giới hạn tần suất.
- **Nhóm C — Kênh & Tài khoản Gửi:** năm kênh phát sóng, tài khoản gửi và định danh người gửi.
- **Nhóm D — Nội dung & Cá nhân hóa:** soạn nội dung theo kênh, trộn thông tin cá nhân hóa, mẫu tin đã được nền tảng phê duyệt, liên kết theo dõi và tham số nguồn gốc.
- **Nhóm E — Kiểm tra Trước Phát sóng:** gửi thử, danh mục kiểm tra trước phát sóng.
- **Nhóm F — Phê duyệt & Điều phối Phát sóng:** phê duyệt kép, phát sóng ngay, hẹn giờ, tạm dừng, tiếp tục, hủy, tự động tạm dừng bảo vệ.
- **Nhóm G — Sổ cái Người nhận & Xử lý Lỗi:** sổ cái từng người nhận, phân loại lỗi, thử lại.
- **Nhóm H — Tuân thủ & Bảo vệ Danh tiếng Gửi:** hủy nhận tin, khiếu nại thư rác, địa chỉ hỏng, khung giờ yên lặng, chiến dịch tái tiếp cận, quyền của chủ thể dữ liệu trên sổ cái.
- **Nhóm I — Đo lường & Ghi nhận Hiệu quả:** báo cáo phân phát và tương tác, ghi nhận cơ hội và doanh thu chịu ảnh hưởng.
- **Nhóm J — Phản hồi & Hành động sau Tương tác:** chuyển tin trả lời về Hộp thư Hội thoại, gắn thẻ tự động.
- **Nhóm K — Tối ưu & Chiến dịch Nâng cao:** dự phòng kênh, thử nghiệm phiên bản, chuỗi nuôi dưỡng nhiều bước, hạn ngạch ngày, ngân sách chiến dịch.
- **Nhóm L — Kiểm soát Toàn Không gian làm việc:** dừng khẩn cấp mọi hoạt động gửi tiếp thị, lịch ngày không gửi, cổng kiểm soát gửi tiếp thị dùng chung cho mọi phân hệ.
- **Nhóm M — Thông báo Dịch vụ:** gửi hàng loạt thông báo không mang tính quảng bá thuộc nhóm Giao dịch & Dịch vụ, có phê duyệt của Người phụ trách Bảo vệ Dữ liệu.

**Ngoài phạm vi (thuộc tài liệu khác):**

- Trạng thái đồng thuận, bằng chứng đồng thuận, trạng thái tiếp cận của kênh liên lạc, nhóm mục đích gửi tin, gộp hồ sơ và quyền chủ thể dữ liệu trên hồ sơ khách hàng — thuộc [`contacts-srs.md`](./contacts-srs.md). Tài liệu này **tuân theo và viện dẫn**, không định nghĩa lại.
- Kết nối tài khoản kênh WhatsApp/Zalo, xử lý hội thoại và phân công tư vấn viên — thuộc [`omnichat-srs.md`](./omnichat-srs.md).
- Tính phí, hạn mức theo gói dịch vụ, đình chỉ dịch vụ — thuộc [`billing-subscription-srs.md`](./billing-subscription-srs.md).
- Nguồn gốc chính của cơ hội bán hàng — thuộc [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) (`FEAT-23`).
- Thư hướng dẫn tự động mà **nền tảng** gửi cho doanh nghiệp mới đăng ký (thuật ngữ "Chiến dịch Email Nuôi dưỡng Tự động" trong `CONTEXT.md`) — đó là liên lạc giữa nhà cung cấp nền tảng với doanh nghiệp, không phải chiến dịch do doanh nghiệp gửi cho khách hàng của mình.
- Thông điệp Giao dịch & Dịch vụ gửi cho từng khách theo sự kiện (xác nhận đơn hàng, hóa đơn, nhắc lịch hẹn) — thuộc phân hệ phát sinh sự kiện; phân hệ này chỉ đặc tả Thông báo dịch vụ gửi hàng loạt (`FEAT-45`).

### 1.3 Đối tượng đọc

- **Product Owner / Business Analyst:** căn cứ lập backlog, tiêu chí nghiệm thu và quy trình vận hành tiếp thị.
- **Đội phát triển:** căn cứ thiết kế giải pháp; cách tổ chức kỹ thuật thuộc thẩm quyền đội phát triển.
- **Đội kiểm thử (QA):** căn cứ viết kịch bản kiểm thử, đặc biệt các quy tắc tuân thủ và ca biên thời gian.
- **Bộ phận Pháp chế / Người phụ trách Bảo vệ Dữ liệu:** căn cứ đánh giá mức độ tuân thủ đồng thuận và pháp luật chống tin rác.
- **Khách hàng doanh nghiệp:** căn cứ thỏa thuận phạm vi dịch vụ.

### 1.4 Thuật ngữ nghiệp vụ

| Thuật ngữ | Định nghĩa |
| --- | --- |
| **Chiến dịch** | Một đợt gửi thông điệp tiếp thị tới một tập người nhận được chọn theo tiêu chí, qua một kênh chính, với một nội dung đã được phê duyệt. Mọi lượt gửi của chiến dịch thuộc nhóm mục đích **Tiếp thị & Quảng bá** (`BR-01.5`). |
| **Người phụ trách chiến dịch** | Người chịu trách nhiệm về chiến dịch; mặc định là người tạo. Chiến dịch là "bản ghi của mình" của người phụ trách và thuộc đơn vị theo định nghĩa "Bản ghi thuộc một đơn vị" của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4. Người phụ trách **không** quyết định tập người nhận (`BR-05.4`). |
| **Mức truy cập** | Năm mức **Không có** / **Chỉ của mình** / **Đơn vị của mình** / **Đơn vị và các đơn vị con** / **Toàn workspace** (thao tác Tạo: **Có** / **Không có**), định nghĩa duy nhất tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4 và [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-34`. Tài liệu này không định nghĩa lại các mức. |
| **Người chọn đối tượng** | Người lưu phần Đối tượng của phiên bản chiến dịch gần nhất. Tập khách hàng mà chiến dịch có thể nhắm tới không vượt mức Xem trên Khách hàng của người này (`BR-05.4`). |
| **Người khởi chạy** | Người thực hiện thao tác làm một lượt gửi bắt đầu hoặc được lên lịch: phát sóng ngay, chốt hoặc dời giờ hẹn, phát sóng hoặc xác nhận quy mô từ Giữ lại, bắt đầu chuỗi nuôi dưỡng, gửi lại thủ công (cho riêng lượt gửi lại đó), hoặc người được chuyển làm người khởi chạy (`BR-17.11`). Tiến trình gửi là **tiến trình chạy thay người dùng** theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`: chỉ chạm tới khách hàng trong quyền của người khởi chạy. |
| **Hội thoại đang có tư vấn viên xử lý** | Hội thoại đang mở trong Hộp thư Hội thoại, đã được phân công cho một tư vấn viên, và tư vấn viên đó đã gửi tin trong hội thoại trong khoảng thời gian `CFG-CAMP-59`. Dùng để phân biệt lời khách nói với tư vấn viên với tin trả lời chiến dịch (`BR-26.3`). |
| **Tiêu chí đối tượng** | Tập điều kiện chọn khách hàng nhận tin, dựa trên thông tin hồ sơ khách hàng và hành vi tương tác. |
| **Danh sách người nhận chốt** | Danh sách người nhận cụ thể được xác định **tại thời điểm chiến dịch bắt đầu gửi** từ tiêu chí đối tượng; sau thời điểm này khách hàng mới khớp tiêu chí không được thêm vào (`BR-06.3`). |
| **Điểm đến** | Địa chỉ cụ thể mà tin được gửi tới trên một kênh: một địa chỉ email, một số điện thoại, hoặc một người quan tâm Tài khoản Chính thức Zalo. |
| **Số người nhận khả dụng** | Số người trong tập khớp tiêu chí còn lại sau khi áp các quy tắc loại trừ (`FEAT-08`). |
| **Lý do loại trừ** | Nguyên nhân duy nhất khiến một người khớp tiêu chí không được gửi tin, chọn theo thứ tự ưu tiên cố định (`BR-08.2`). |
| **Sổ cái người nhận** | Bản ghi theo từng người nhận của một chiến dịch: điểm đến, kênh, trạng thái phân phát, các sự kiện tương tác, lý do loại trừ hoặc lỗi. |
| **Trạng thái phân phát** | Tiến trình đưa tin tới người nhận: Chờ gửi → Đã chuyển nhà cung cấp → Đã phân phát, hoặc Hoãn / Thất bại tạm thời / Thất bại vĩnh viễn / Chưa xác định / Bị loại trừ tại thời điểm gửi / Đã hủy trước khi gửi (`BR-23.2`). |
| **Sự kiện tương tác** | Mở, nhấp liên kết, trả lời, hủy nhận tin, khiếu nại thư rác. Sự kiện tương tác được **ghi thêm**, không thay thế trạng thái phân phát. |
| **Gửi thử** | Gửi bản xem thực tế của nội dung tới một số ít địa chỉ nội bộ để kiểm tra hiển thị; không thuộc sổ cái và không tính vào số liệu chiến dịch (`FEAT-15`). |
| **Phê duyệt kép** | Người phê duyệt phát sóng phải khác người đã soạn phiên bản nội dung được phê duyệt (`BR-17.2`). |
| **Phiên bản đã duyệt** | Toàn bộ nội dung, đối tượng, tài khoản gửi và lịch gửi tại thời điểm được phê duyệt. Bất kỳ thay đổi nào sau đó tạo phiên bản mới cần duyệt lại. |
| **Giới hạn tần suất** | Số tin tiếp thị tối đa một người nhận được nhận trong một khoảng thời gian trượt (`FEAT-08`, `CFG-CAMP-02`, `CFG-CAMP-03`). |
| **Khung giờ yên lặng** | Khoảng thời gian trong ngày không được gửi tin tiếp thị, tính theo giờ địa phương của người nhận (`FEAT-29`). |
| **Dự phòng kênh** | Gửi cho người nhận qua kênh thay thế khi kênh chính đã **xác nhận không tới được** người đó (`FEAT-37`). |
| **Tự động tạm dừng bảo vệ** | Hệ thống tự tạm dừng chiến dịch khi phát hiện dấu hiệu gây hại (khiếu nại, địa chỉ hỏng tăng vọt, mất tài khoản gửi, hết hạn mức…) và chờ con người quyết định (`FEAT-20`). |
| **Cơ hội chịu ảnh hưởng** | Cơ hội bán hàng được tạo trong cửa sổ ghi nhận sau khi một liên hệ tham gia cơ hội đã tương tác với chiến dịch (`FEAT-34`). Khác với **Nguồn gốc chính** của cơ hội theo `deals-pipeline-srs.md`. |
| **Chiến dịch Tái tiếp cận** | Chiến dịch được phê duyệt riêng để gửi tới khách hàng ở giai đoạn Đã rời bỏ, theo `BR-12.5b` của `contacts-srs.md` (`FEAT-31`). |
| **Giữ lại** | Trạng thái của chiến dịch đã duyệt, tới lúc bắt đầu gửi nhưng chưa gửi được vì một điều kiện vận hành không làm thay đổi phiên bản đã duyệt (hạn mức, tài khoản gửi, trang đích tạm thời không truy cập được…); phê duyệt được giữ trong thời gian ân hạn (`BR-18.3`). |
| **Chuỗi nuôi dưỡng** | Chuỗi các bước gửi tự động theo thời gian và hành vi, có vòng đời riêng khác chiến dịch gửi một lần (`FEAT-39`). |
| **Danh sách không quảng cáo** | Danh sách điểm đến không nhận tin quảng cáo mà doanh nghiệp cấu hình để đối chiếu — do doanh nghiệp nạp hoặc từ một nguồn bên ngoài doanh nghiệp kết nối (`BR-30.5`). |
| **Tin nhắn mẫu đã phê duyệt** | Mẫu tin đã được nền tảng nhắn tin (WhatsApp, Zalo) duyệt trước; bắt buộc với các kênh có yêu cầu này (`FEAT-13`). |

### 1.5 Tài liệu tham khảo

- Các SRS liên quan liệt kê ở đầu tài liệu; thuật ngữ dùng chung tại [`CONTEXT.md`](../CONTEXT.md).
- Yêu cầu đối với người gửi thư hàng loạt của các nhà cung cấp hộp thư lớn (xác thực tên miền gửi, hủy nhận bằng một thao tác, ngưỡng khiếu nại).
- Chính sách tin nhắn doanh nghiệp của WhatsApp và Zalo (loại mẫu tin, điều kiện gửi, hạn mức, điểm chất lượng).
- Quy định của nhà mạng về tin nhắn thương hiệu quảng cáo (đăng ký tên thương hiệu, duyệt nội dung).

### 1.6 Giả định & Phụ thuộc

1. **Đồng thuận do phân hệ Khách hàng quản lý.** Mọi quyết định "được phép gửi tiếp thị tới điểm đến này hay không" đọc từ trạng thái đồng thuận và trạng thái tiếp cận theo `contacts-srs.md` (`FEAT-29`, `FEAT-30`, `FEAT-33`). Phân hệ Chiến dịch **ghi ngược** các sự kiện hủy nhận tin, khiếu nại thư rác và địa chỉ hỏng về đúng các trạng thái đó, kèm bằng chứng theo `BR-30.3` của `contacts-srs.md`.
2. **Tài khoản kênh WhatsApp và Zalo OA dùng chung với Hộp thư Hội thoại.** Chiến dịch gửi qua đúng các tài khoản kênh đã kết nối tại `omnichat-srs.md` (`FEAT-01`), để tin khách hàng trả lời quay về đúng hộp thư. Không có danh mục tài khoản WhatsApp/Zalo OA thứ hai riêng cho chiến dịch. Tài khoản Zalo ZNS, địa chỉ gửi email và tên thương hiệu SMS (kèm đầu số nhận từ chối) do phân hệ này quản lý. Việc tiếp nhận tin trả lời qua Zalo ZNS và qua đầu số SMS hai chiều vào Hộp thư Hội thoại phụ thuộc `omnichat-srs.md` định nghĩa hai kênh tiếp nhận này; với kênh không có tiếp nhận hội thoại, tin trả lời được xử lý theo `BR-35.1`.
3. **Tuân thủ pháp luật là trách nhiệm của doanh nghiệp.** Tài liệu không đặc tả bộ quy tắc pháp lý của quốc gia nào. Các quy tắc thường chịu quy định pháp luật — yêu cầu đồng ý trước, nhãn quảng cáo, khung giờ gửi, số tin tối đa trong ngày, Danh sách không quảng cáo, căn cứ theo dõi hành vi — là tham số của Không gian làm việc (`FEAT-30`). Giá trị mặc định là giá trị gợi ý thận trọng, không phải cam kết tuân thủ; doanh nghiệp tự cấu hình theo pháp luật và chính sách áp dụng cho mình và chịu trách nhiệm về cấu hình đó.
4. **Phạm vi dữ liệu và quyền.** Chiến dịch là một loại dữ liệu chịu mô hình phân quyền chung của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) v5.0: mỗi vai trò có một mức truy cập (định nghĩa tại Mục 1.4 của tài liệu phân quyền) cho từng ô (Chiến dịch, Xem / Tạo / Sửa / Xoá / Xuất / Gán người phụ trách) và cho thao tác đặc thù **(Chiến dịch, Phát sóng)** do tài liệu này khai báo (`BR-17.1`). Trong ma trận quyền, Phát sóng không thuộc mức đặt sẵn nào và luôn hiển thị thành một dòng riêng ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-24`, `BR-25.4`). Danh sách vai trò dựng sẵn và ma trận mặc định — gồm Marketing và Quản lý Marketing — chỉ có một nguồn là [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`; tài liệu này dẫn chiếu, không chép lại. Doanh nghiệp điều chỉnh từng ô của vai trò dựng sẵn qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02`, chịu các sàn của phân hệ này (Mục 5, ghi chú 3). Khi khởi tạo Không gian làm việc, [`onboarding-srs.md`](./onboarding-srs.md) `FEAT-12` có thể đặt (Chiến dịch, Phát sóng) = Chỉ của mình cho vai trò Marketing, và lựa chọn **"Thu về khách hàng của đơn vị mình"** thu ô (Khách hàng, Xem) của cả Marketing lẫn Quản lý Marketing về Đơn vị của mình — khi đó, nếu không có nguồn nới khác (công khai đọc, chính sách Cho phép, phụ trách đơn vị), đối tượng chiến dịch chỉ còn khách hàng thuộc đơn vị Marketing (`BR-05.4`). Quyền quản trị riêng của phân hệ được khai báo tại Mục 5, ghi chú 2.
5. **Nhà cung cấp dịch vụ gửi tin** (nhà mạng, nền tảng nhắn tin, dịch vụ gửi thư) là bên thứ ba. Mọi trạng thái "đã phân phát", "đã mở", "thất bại" đều là **thông tin do bên thứ ba báo về**, có thể trễ hoặc không bao giờ tới; tài liệu quy định cách xử lý khi thông tin đó không tới (`BR-23.3`).
6. **Ba trạng thái đồng thuận.** Theo (`contacts-srs.md`, `BR-30.1`), mỗi kênh của mỗi khách hàng ở một trong ba trạng thái **Đồng ý nhận tin** (có bằng chứng), **Từ chối nhận tin**, **Chưa có đồng thuận** (mặc định của mọi hồ sơ tạo ra không kèm bằng chứng). Trên các kênh doanh nghiệp yêu cầu Đồng ý trước (`CFG-CAMP-74`, mặc định cả năm kênh), chỉ trạng thái thứ nhất được nhận tiếp thị (`BR-08.1`); trên kênh không yêu cầu, Chưa có đồng thuận cũng được nhận, Từ chối nhận tin thì không bao giờ. Kênh đồng thuận tương ứng với từng kênh chiến dịch theo `BR-09.1`.
7. **Danh mục dùng chung với phân hệ Khách hàng.** Các nguồn ghi nhận Từ chối nhận tin phát sinh từ chiến dịch (`BR-26.4`) có trong danh mục nguồn thu thập A.8 của `contacts-srs.md`; sổ cái chiến dịch và dấu vết chặn gửi (`FEAT-32`) có trong bảng nơi lưu dữ liệu tại (`contacts-srs.md`, `BR-33.8`).

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà phân hệ giải quyết

1. **Gửi nhầm đối tượng, gửi tới người đã từ chối.** Gửi tiếp thị tới người đã hủy nhận tin vừa vi phạm pháp luật, vừa làm tăng khiếu nại thư rác khiến nhà mạng hoặc nhà cung cấp khóa tài khoản gửi — tổn thất kéo dài cho mọi chiến dịch sau.
2. **Khách hàng bị làm phiền quá mức.** Nhiều nhóm tiếp thị cùng gửi cho một người trong cùng tuần vì không ai nhìn thấy tổng số tin người đó đã nhận.
3. **Sai sót nội dung phát tán diện rộng.** Mã giảm giá sai, liên kết hỏng, lỗi chính tả, thiếu cá nhân hóa ("Kính gửi {tên}") tới hàng chục nghìn người trước khi ai kịp phát hiện, và không có cách dừng kịp thời.
4. **Thất thoát thông điệp khi kênh chính không tới được người nhận.** Người nhận không dùng Zalo, số không đăng ký WhatsApp — thông điệp bị bỏ qua dù khách hàng vẫn liên lạc được qua kênh khác đã đồng ý.
5. **Đứt gãy khi khách hàng phản hồi.** Khách hàng trả lời tin tiếp thị để hỏi mua nhưng tin trả lời không tới được nhân viên nào.
6. **Không chứng minh được hiệu quả.** Ban giám đốc không biết chiến dịch mang về bao nhiêu cơ hội và doanh thu; các con số mở/nhấp mỗi báo cáo tính một kiểu.
7. **Chi phí gửi tin mất kiểm soát.** Một chiến dịch SMS lớn hoặc cấu hình dự phòng sai có thể tiêu hết hạn mức của cả doanh nghiệp trong một ngày.

### 2.2 Vai trò người dùng

| Vai trò | Quyền hạn và trách nhiệm nghiệp vụ |
| --- | --- |
| **Nhân viên Marketing** | Người giữ vai trò dựng sẵn **Marketing** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`). Tạo và soạn chiến dịch, chọn đối tượng, gửi thử, gửi phê duyệt, theo dõi kết quả — mỗi việc trong đúng mức của ô (Chiến dịch, thao tác) tương ứng. Theo mặc định không có (Chiến dịch, Phát sóng) (`BR-17.1`); doanh nghiệp có thể đặt ô này = Chỉ của mình khi khởi tạo ([`onboarding-srs.md`](./onboarding-srs.md) `FEAT-12`) hoặc qua điều chỉnh ô. |
| **Quản lý Marketing** | Người giữ vai trò dựng sẵn **Quản lý Marketing** (cùng nguồn). Theo mặc định có các ô trên Chiến dịch ở mức Đơn vị của mình, gồm (Chiến dịch, Phát sóng) — phê duyệt, phát sóng, hẹn giờ, tạm dừng, tiếp tục, hủy, gửi lại, đặt ngân sách, kích hoạt dừng khẩn cấp (`BR-17.1`, `BR-42.2`) — và các quyền quản trị Cấu hình nhịp độ tiếp thị, Xử lý tin gắn nhãn từ chối (Mục 5, ghi chú 2 và 6). |
| **Quản lý Kinh doanh** | Người phụ trách quan hệ với tập khách hàng theo định nghĩa của `contacts-srs.md` `BR-12.5b`; trên nền tảng thường giữ vai trò dựng sẵn **Quản lý** và là Người phụ trách đơn vị kinh doanh. Đồng phê duyệt Chiến dịch Tái tiếp cận nhắm tới khách hàng thuộc phạm vi mình (`FEAT-31`); xem báo cáo cơ hội chịu ảnh hưởng trong mức Xem của mình. |
| **Người phụ trách Bảo vệ Dữ liệu** | Vai trò chức năng theo `contacts-srs.md`: xem nhật ký tuân thủ của chiến dịch, đồng phê duyệt các thay đổi tham số tuân thủ (Phụ lục B), xử lý yêu cầu của chủ thể dữ liệu trên sổ cái (`FEAT-32`); là người duy nhất gỡ tạm chặn chờ xác nhận ngoài hội thoại và nhận cảnh báo leo thang khi tin gắn nhãn chưa được xử lý (`BR-26.3`). Nếu doanh nghiệp không chỉ định, Chủ sở hữu đảm nhận; khi đó mọi chỗ yêu cầu hai người (Chủ sở hữu cùng Người phụ trách Bảo vệ Dữ liệu, hay Quản trị viên cùng Người phụ trách Bảo vệ Dữ liệu) áp **quy tắc thay thế người thứ hai** của (`contacts-srs.md`, `NFR-14`): người thứ hai là một Quản trị viên khác, không phải người đề xuất. |
| **Tư vấn viên** | Người xử lý hội thoại trong Hộp thư Hội thoại (thường giữ vai trò dựng sẵn **Nhân viên Hỗ trợ** hoặc **Nhân viên Kinh doanh**). Tiếp nhận và xử lý tin khách hàng trả lời chiến dịch (`FEAT-35`); ghi nhận lời từ chối của khách trên bất kỳ tin nào, và đánh dấu "Không phải lời từ chối" cho tin gắn nhãn, trong hội thoại đang mở mà họ xử lý, không cần quyền sửa hồ sơ (`BR-26.3`); theo mặc định không có ô nào trên Chiến dịch. Trong Ma trận quyền, cột này đại diện cho mọi người dùng chỉ có quyền xem hồ sơ khách hàng. |
| **Quản trị viên Không gian làm việc** | Cấp bậc thành viên, cùng Chủ sở hữu là **Người có toàn quyền** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4): có mọi ô ở mức Toàn workspace, gồm (Chiến dịch, Phát sóng), và mọi quyền quản trị của phân hệ (Mục 5, ghi chú 2), trừ các thao tác dành riêng cho Chủ sở hữu và các sàn của phân hệ áp cả lên Người có toàn quyền (Mục 5, ghi chú 3). |
| **Người phụ trách thanh toán** | Vai trò theo `billing-subscription-srs.md`: xác nhận cho một lô gửi (kể cả đợt dự phòng) vượt hạn mức gói dịch vụ trên một con số cụ thể (`BR-17.8`, `BR-37.6`) và xem báo cáo đối soát chi phí (`BR-41.4`). Không có quyền nào khác trên chiến dịch. |
| **Chủ sở hữu Không gian làm việc** | Người có toàn quyền; là thẩm quyền duy nhất cho các tham số tắt cơ chế kiểm soát (ví dụ tắt phê duyệt kép, Phụ lục B). |
| **Người nhận** | Khách hàng nhận tin: đọc, nhấp liên kết, trả lời, hủy nhận tin, khiếu nại thư rác. Không đăng nhập hệ thống. |
| **Tiến trình Hệ thống** | Chốt danh sách người nhận, gửi theo nhịp, áp loại trừ tại thời điểm gửi, thực hiện dự phòng kênh, ghi nhận trạng thái do nhà cung cấp báo về, tự động tạm dừng bảo vệ. Mọi lượt gửi là **tiến trình chạy thay người khởi chạy** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`): chỉ gửi tới khách hàng trong quyền của người khởi chạy tại thời điểm gửi (`BR-17.10`). |

> **Ghi chú:** Đây là **vai trò nghiệp vụ** dùng để diễn đạt yêu cầu; cột "Quyền hạn" mô tả **cấu hình mặc định**, không phải năng lực gắn cứng cho tên vai trò. Theo Nguyên tắc 4 của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), mọi năng lực trong tài liệu này là giá trị của một ô (Chiến dịch, thao tác) hoặc một quyền quản trị của phân hệ (Mục 5, ghi chú 2) mà vai trò bất kỳ đặt được; khi tài liệu viết "người có quyền Phát sóng chiến dịch" nghĩa là người có ô (Chiến dịch, Phát sóng) bao phủ chiến dịch đó. Danh sách vai trò dựng sẵn và mặc định của chúng chỉ có tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`. "Quản trị viên" và "Chủ sở hữu" là **cấp bậc thành viên**, không phải vai trò; Người phụ trách Bảo vệ Dữ liệu và Người phụ trách thanh toán là **chức danh trách nhiệm** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-47`), không tự cấp quyền. Người có toàn quyền luôn có quyền Phát sóng chiến dịch, nên trong đội có ít người, một Người có toàn quyền không tham gia soạn phiên bản luôn là người duyệt hợp lệ theo `BR-17.2`.

### 2.3 Quy ước thời gian nghiệp vụ

Tài liệu dùng ba loại khái niệm thời gian, xử lý khác nhau:

- **Mốc thời điểm cụ thể** (giờ hẹn gửi, thời điểm gửi, thời điểm mở/nhấp, thời điểm hủy nhận tin): là **một thời điểm tuyệt đối** trên trục thời gian. Người dùng nhập và xem theo múi giờ cá nhân của mình; màn hình hẹn giờ **luôn hiển thị kèm tên múi giờ** đang dùng để nhập.
- **Khái niệm tính theo "ngày"** (hạn ngạch ngày `FEAT-40`, báo cáo theo ngày, thời hạn lưu trữ): xác định theo **múi giờ cấu hình của Không gian làm việc** — ngày mới bắt đầu lúc 00:00 theo múi giờ đó, giống cách `contacts-srs.md` và `tasks-srs.md` xử lý.
- **Khái niệm gắn với trải nghiệm của người nhận** (khung giờ yên lặng `FEAT-29`): xác định theo **giờ địa phương của người nhận**. Người nhận không có thông tin múi giờ trên hồ sơ thì dùng múi giờ của Không gian làm việc. Khi địa phương của người nhận chuyển giờ mùa hè/mùa đông, khung giờ yên lặng theo **giờ đồng hồ treo tường** tại địa phương đó (22:00 luôn là 22:00 tại chỗ người nhận).
- **Khoảng thời gian trượt** (cửa sổ giới hạn tần suất, cửa sổ ghi nhận ảnh hưởng, thời gian chờ dự phòng): đo bằng **thời lượng tuyệt đối** tính từ một mốc thời điểm, không phụ thuộc ranh giới ngày. Ví dụ "7 ngày" là đúng 7 × 24 giờ tính ngược từ thời điểm đang xét.

- **Khoảng thời gian tính theo tháng** (thời hạn lưu sổ cái, khoảng hẹn giờ tối đa): cộng theo tháng dương lịch; nếu tháng đích không có ngày tương ứng (ngày 29/02 ở năm không nhuận, ngày 31 ở tháng 30 ngày) thì lấy **ngày cuối cùng của tháng đích**.
- **Giờ hẹn rơi vào khoảng giờ không tồn tại hoặc lặp lại** khi địa phương chuyển giờ mùa hè/mùa đông: giờ không tồn tại được chuyển sang phút hợp lệ đầu tiên sau đó; giờ lặp lại được hiểu là **lần xuất hiện đầu tiên**. Màn hình hẹn giờ cảnh báo khi giờ chọn rơi vào hai trường hợp này.
- **Chỉ số theo tháng** (`KPI-03`): tháng dương lịch theo múi giờ Không gian làm việc. **Tỷ lệ khiếu nại của Không gian làm việc** (`KPI-02`, `BR-27.3`) dùng khoảng trượt tại Phụ lục B.2, theo cách các nhà cung cấp hộp thư đánh giá danh tiếng người gửi.
- **Xét thêm theo giờ của Không gian làm việc:** khi `CFG-CAMP-71` bật, khung giờ yên lặng còn được xét theo **giờ của Không gian làm việc**, **đồng thời** với giờ địa phương của người nhận; tin chỉ được gửi khi nằm ngoài khung theo **cả hai** giờ (`BR-29.1`).

**Lý do nghiệp vụ:** Khung giờ yên lặng tồn tại để không đánh thức người nhận lúc nửa đêm — nếu tính theo múi giờ của doanh nghiệp, một doanh nghiệp đặt tại Việt Nam gửi cho khách ở Trung Đông lúc 21:00 sẽ tới tay khách lúc 17:00 (vô hại), nhưng ngược lại một doanh nghiệp đặt ở múi giờ muộn hơn sẽ gửi đúng lúc khách đang ngủ. Ngược lại, hạn ngạch ngày là công cụ kiểm soát chi phí của doanh nghiệp nên phải neo vào một ranh giới ngày duy nhất của doanh nghiệp. Giới hạn tần suất dùng khoảng trượt tuyệt đối để một người nhận không thể nhận 2 tin lúc 23:59 và 2 tin lúc 00:01 chỉ vì vừa qua ranh giới ngày.

### 2.4 Nguyên tắc nghiệp vụ nền tảng

**Nguyên tắc 1 — Đồng thuận được kiểm tra tại thời điểm gửi từng tin, không chỉ tại thời điểm chọn đối tượng.**
Số người nhận khả dụng khi xem trước chỉ là **ước tính**. Quyết định cuối cùng "có gửi cho người này không" được đưa ra ngay trước khi gửi tin tới người đó, dựa trên trạng thái đồng thuận, trạng thái tiếp cận, hạn chế xử lý và giới hạn tần suất **tại đúng thời điểm đó** (`BR-08.4`). Một người hủy nhận tin lúc 9:05 không nhận tin của chiến dịch bắt đầu gửi lúc 9:00 nếu lượt gửi tới họ chưa diễn ra.

**Nguyên tắc 2 — Không lượt gửi nào ra khỏi hệ thống mà không qua phiên bản đã duyệt.**
Nội dung, đối tượng, tài khoản gửi và lịch gửi được gửi tới khách hàng phải đúng là phiên bản đã được phê duyệt. Mọi thay đổi — kể cả sửa một liên kết trong lúc tạm dừng — tạo phiên bản mới cần phê duyệt lại (`BR-17.4`); trong lúc chờ duyệt, phiên bản đã duyệt trước đó vẫn là phiên bản duy nhất được phép gửi. Gửi thử chỉ được tới địa chỉ nội bộ đã đăng ký (`BR-15.1`), không phải đường vòng để gửi cho khách hàng.

**Nguyên tắc 3 — Mỗi người nhận nhận tối đa một thông điệp của một lượt gửi.**
Một người khớp tiêu chí qua nhiều cách, có nhiều hồ sơ cùng một điểm đến, hay được gửi dự phòng qua kênh thứ hai, vẫn chỉ nhận **một** thông điệp cho mỗi lượt gửi của chiến dịch (`BR-08.3`, `BR-37.2`). Hệ thống chấp nhận bỏ sót một tin trong tình huống không chắc chắn hơn là gửi trùng (`BR-23.3`).

**Nguyên tắc 4 — Sự kiện xấu về điểm đến được ghi ngược về hồ sơ khách hàng.**
Hủy nhận tin, khiếu nại thư rác và địa chỉ hỏng phát hiện qua chiến dịch không chỉ nằm trong báo cáo chiến dịch mà được ghi về trạng thái đồng thuận và trạng thái tiếp cận của hồ sơ khách hàng, để **mọi** chiến dịch sau và mọi phân hệ khác đều thấy (`FEAT-26`, `FEAT-27`, `FEAT-28`).

**Nguyên tắc 5 — Hệ thống được tự tạm dừng, không được tự tiếp tục.**
Khi phát hiện dấu hiệu gây hại, hệ thống tự tạm dừng chiến dịch để ngăn thiệt hại lan rộng. Việc tiếp tục luôn cần một người có quyền Phát sóng chiến dịch quyết định sau khi đã xem lý do (`FEAT-20`).

**Nguyên tắc 6 — Chiến dịch không phải đường vòng vượt quyền xem khách hàng.**
Tập người nhận không bao giờ vượt mức Xem trên Khách hàng của người chọn đối tượng (`BR-05.4`), và tiến trình gửi chạy thay người khởi chạy chỉ gửi tới khách hàng nằm trong quyền của người đó **tại thời điểm gửi** — phần giao của mức lúc khởi chạy và mức hiện tại ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`; `BR-17.10`). Khi người khởi chạy bị tạm ngưng hoặc rời workspace, lượt gửi dừng lại chờ người có thẩm quyền quyết định (`BR-17.11`).

**Nguyên tắc 7 — Thao tác dừng và thu hẹp không bị chặn bởi sự cố nhật ký.**
Tạm dừng, hủy, hủy lịch, kích hoạt dừng khẩn cấp, thu hồi phê duyệt Tái tiếp cận, ghi nhận từ chối nhận tin, và mọi lượt tự động tạm dừng hay loại trừ do mất quyền luôn có hiệu lực ngay cả khi nhật ký kiểm toán đang gặp sự cố; sự kiện được giữ lại để **ghi bù** ngay khi nhật ký phục hồi, gắn cờ "ghi bù" kèm thời điểm thao tác thật (`NFR-08`; [ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md); [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.6`). Thao tác làm tăng phạm vi gửi (phê duyệt, phát sóng, tiếp tục, gỡ dừng khẩn cấp) không thực hiện được khi không ghi được nhật ký.

### 2.5 Vòng đời trạng thái chiến dịch

| Trạng thái | Ý nghĩa | Chuyển được sang |
| --- | --- | --- |
| **Nháp** | Đang soạn; sửa tự do. | Chờ duyệt; Đã duyệt *(chỉ khi tắt phê duyệt kép, với chiến dịch của chính mình, và người thao tác có quyền Phát sóng chiến dịch — `BR-17.9`)*; Thùng rác |
| **Chờ duyệt** | Đã gửi phê duyệt, chờ người có thẩm quyền xem xét. | Đã duyệt *(phê duyệt, hoặc tự xác nhận theo `BR-17.9` khi tắt phê duyệt kép với Chiến dịch Tái tiếp cận)*; Nháp *(bị từ chối; người gửi rút lại; có người sửa — `BR-01.6`; quá giờ hẹn mà chưa được duyệt — `BR-18.3`)*; Thùng rác |
| **Đã duyệt** | Phiên bản đã duyệt, chưa gửi; có thể có giờ hẹn. | Đang gửi *(phát ngay hoặc tới giờ hẹn, khi mọi kiểm tra của `BR-17.8` đạt)*; Giữ lại *(tới giờ hẹn nhưng một điều kiện vận hành chưa đạt — `BR-18.3`)*; Nháp *(sửa bất kỳ phần nào của phiên bản, hủy lịch, hoặc một kiểm tra làm mất hiệu lực phiên bản đã duyệt không đạt — `BR-18.3`)* |
| **Giữ lại** | Đã tới lúc gửi nhưng chưa gửi được vì điều kiện vận hành; phê duyệt vẫn hiệu lực trong thời gian ân hạn `CFG-CAMP-32` (thời gian dừng khẩn cấp, ngày không gửi và đình chỉ dịch vụ không tính vào ân hạn — `BR-18.3`). | Đang gửi *(người có quyền Phát sóng chiến dịch bấm phát sóng và mọi kiểm tra đạt; hoặc hệ thống tự phát sóng khi điều kiện vận hành được khắc phục, trong các trường hợp và giới hạn của `BR-18.3`, `BR-40.2`)*; Đã duyệt với giờ hẹn mới *(dời giờ muộn hơn trong thời gian ân hạn — `BR-18.2`)*; Nháp *(hủy lịch; hết thời gian ân hạn; lỗi chặn nhóm (a) được biết — `BR-16.4`; có thay đổi phiên bản; khi bấm phát sóng, một kiểm tra làm mất hiệu lực phiên bản không đạt; tin nhắn mẫu bị vô hiệu, bị từ chối hoặc đổi loại — `BR-13.2`; người dùng đổi người phụ trách theo `BR-01.3`)* |
| **Đang gửi** | Đang gửi tới danh sách người nhận chốt, kể cả khi đang chờ sang ngày mới theo dàn trải hạn ngạch (`BR-40.3`), hoặc đang có tin bị hoãn vì khung giờ yên lặng, ngày không gửi (`FEAT-43`), giới hạn 24 giờ theo điểm đến (`BR-30.3`) hay hiệu lực duyệt SMS (`BR-13.4`). | Tạm dừng; Hoàn tất; Đã hủy |
| **Tạm dừng** | Ngừng gửi tin mới; do người dùng, do hệ thống tự động tạm dừng bảo vệ, do dừng khẩn cấp, hoặc do người khởi chạy bị tạm ngưng, rời workspace hay mất quyền Phát sóng chiến dịch (`BR-17.10`, `BR-17.11`). | Đang gửi *(tiếp tục)*; Tạm dừng — chờ duyệt phiên bản *(có người sửa nội dung, đổi tài khoản gửi hoặc tăng nhịp khi phê duyệt kép bật — `BR-19.3`)*; Đã hủy *(người dùng hủy hoặc quá thời hạn tạm dừng — `BR-19.4`)* |
| **Tạm dừng — chờ duyệt phiên bản** | Đang tạm dừng, có phiên bản nội dung mới chờ duyệt; phiên bản đã duyệt trước đó vẫn là phiên bản hiệu lực. | Tạm dừng *(phiên bản mới được duyệt — trở thành phiên bản hiệu lực; hoặc bị từ chối / rút lại — giữ phiên bản cũ)*; Đã hủy |
| **Hoàn tất** | Mọi người nhận trong danh sách chốt (gồm cả các lượt dự phòng, lượt bị hoãn và phần còn lại sau thử nghiệm A/B) đều đã có kết quả cuối cùng theo `BR-23.2`. Gửi lại thủ công (`BR-25.2`) diễn ra khi chiến dịch vẫn ở Hoàn tất. | Lưu trữ *(khi không có lượt gửi lại đang chạy)* |
| **Đã hủy** | Dừng vĩnh viễn trước khi gửi hết; không tiếp tục được. | Lưu trữ |
| **Lưu trữ** | Ẩn khỏi danh sách mặc định; chỉ sửa được tên, mô tả nội bộ và người phụ trách (`BR-01.6`); báo cáo và sổ cái giữ nguyên. | Trạng thái trước khi lưu trữ *(bỏ lưu trữ)* |
| **Thùng rác** | Chiến dịch chưa từng gửi đã bị xóa, chờ xóa vĩnh viễn sau `CFG-CAMP-21`. | Nháp *(khôi phục)* |

Không có đường chuyển nào khác ngoài bảng trên. Đặc biệt: không có đường từ **Đang gửi / Tạm dừng / Hoàn tất / Đã hủy** về **Nháp** — một chiến dịch đã gửi ít nhất một tin không bao giờ trở lại thành bản nháp, vì sổ cái của nó là bằng chứng tuân thủ và phải gắn với đúng nội dung đã gửi. Muốn gửi lại, dùng Nhân bản (`FEAT-02`). Thao tác "Phát sóng ngay" không đạt một kiểm tra vận hành (gồm lỗi chặn nhóm (b) và chênh lệch đối tượng chưa được xác nhận) thì chiến dịch **giữ nguyên Đã duyệt** (không có gì thay đổi); không đạt một kiểm tra làm mất hiệu lực phiên bản (lỗi chặn nhóm (a)) thì về Nháp. Trạng thái Đã duyệt không có thời gian ân hạn; ân hạn `CFG-CAMP-32` chỉ tính từ lúc chiến dịch vào Giữ lại. Chuỗi nuôi dưỡng có vòng đời riêng tại `BR-39.1`.

### 2.6 Bảng tổng hợp tính năng nghiệp vụ

| Nhóm | Mã | Tên tính năng nghiệp vụ |
| --- | --- | --- |
| **A. Quản trị Chiến dịch** | `FEAT-01` | Tạo mới & Quản lý Chiến dịch |
| | `FEAT-02` | Nhân bản Chiến dịch |
| | `FEAT-03` | Mã Định danh Chiến dịch |
| | `FEAT-04` | Xóa, Thùng rác & Lưu trữ Chiến dịch |
| **B. Đối tượng Nhận tin** | `FEAT-05` | Tiêu chí Chọn Đối tượng |
| | `FEAT-06` | Xem trước Quy mô & Chốt Danh sách Người nhận |
| | `FEAT-07` | Xem Mẫu Người nhận |
| | `FEAT-08` | Loại trừ, Khử trùng lặp & Giới hạn Tần suất |
| **C. Kênh & Tài khoản Gửi** | `FEAT-09` | Năm Kênh Phát sóng |
| | `FEAT-10` | Tài khoản Gửi & Định danh Người gửi |
| **D. Nội dung & Cá nhân hóa** | `FEAT-11` | Soạn Nội dung theo Kênh |
| | `FEAT-12` | Trộn Thông tin Cá nhân hóa |
| | `FEAT-13` | Tin nhắn Mẫu đã Phê duyệt |
| | `FEAT-14` | Liên kết Theo dõi & Tham số Nguồn gốc |
| **E. Kiểm tra Trước Phát sóng** | `FEAT-15` | Gửi Thử |
| | `FEAT-16` | Danh mục Kiểm tra Trước Phát sóng |
| **F. Phê duyệt & Điều phối Phát sóng** | `FEAT-17` | Phê duyệt Kép & Phát sóng |
| | `FEAT-18` | Hẹn giờ Phát sóng |
| | `FEAT-19` | Tạm dừng, Sửa trong lúc Tạm dừng & Tiếp tục |
| | `FEAT-20` | Tự động Tạm dừng Bảo vệ |
| | `FEAT-21` | Hủy Chiến dịch |
| | `FEAT-22` | Nhịp Gửi & Ưu tiên Tin Trả lời Khách hàng |
| **G. Sổ cái Người nhận & Xử lý Lỗi** | `FEAT-23` | Sổ cái Người nhận |
| | `FEAT-24` | Phân loại Lỗi Gửi |
| | `FEAT-25` | Thử lại Tin Thất bại |
| **H. Tuân thủ & Bảo vệ Danh tiếng Gửi** | `FEAT-26` | Hủy Nhận tin trên Mọi Kênh |
| | `FEAT-27` | Khiếu nại Thư rác |
| | `FEAT-28` | Xử lý Điểm đến Hỏng |
| | `FEAT-29` | Khung Giờ Yên lặng |
| | `FEAT-30` | Chính sách Gửi Tiếp thị theo Cấu hình Doanh nghiệp |
| | `FEAT-31` | Chiến dịch Tái tiếp cận |
| | `FEAT-32` | Quyền Chủ thể Dữ liệu & Thời hạn Lưu Sổ cái |
| **I. Đo lường & Ghi nhận Hiệu quả** | `FEAT-33` | Báo cáo Phân phát & Tương tác |
| | `FEAT-34` | Ghi nhận Cơ hội & Doanh thu Chịu ảnh hưởng |
| **J. Phản hồi & Hành động sau Tương tác** | `FEAT-35` | Chuyển Tin Trả lời về Hộp thư Hội thoại |
| | `FEAT-36` | Gắn Thẻ Tự động sau Tương tác |
| **K. Tối ưu & Chiến dịch Nâng cao** | `FEAT-37` | Dự phòng Kênh |
| | `FEAT-38` | Thử nghiệm Phiên bản (A/B) |
| | `FEAT-39` | Chuỗi Nuôi dưỡng Nhiều Bước |
| | `FEAT-40` | Hạn ngạch Gửi Hằng ngày |
| | `FEAT-41` | Ngân sách & Chi phí Chiến dịch |
| **L. Kiểm soát Toàn Không gian làm việc** | `FEAT-42` | Dừng Khẩn cấp Toàn bộ Hoạt động Gửi Tiếp thị |
| | `FEAT-43` | Lịch Ngày Không Gửi |
| | `FEAT-44` | Cổng Kiểm soát Gửi Tiếp thị Dùng chung |
| **M. Thông báo Dịch vụ** | `FEAT-45` | Thông báo Dịch vụ Hàng loạt |

**Tổng kết phạm vi:** 45 tính năng, **tất cả là yêu cầu bắt buộc**. Các nhu cầu chưa chốt phương án gom tại Mục 7 và không mang mã `FEAT`.

### 2.7 Mục tiêu kinh doanh & Chỉ số thành công

Mọi tỷ lệ dưới đây dùng đúng định nghĩa tại `BR-33.2`; không báo cáo nào được tính theo mẫu số khác.

| Mã | Vấn đề (Mục 2.1) | Chỉ số | Mục tiêu | Tính năng đóng góp |
| --- | --- | --- | --- | --- |
| `KPI-01` | 1 | Số lượt gửi tiếp thị tới điểm đến mà tại thời điểm gửi: không có Đồng ý nhận tin có bằng chứng (với kênh thuộc `CFG-CAMP-74` và mọi kênh ngoài năm kênh), đang Từ chối nhận tin, Không tiếp cận được hoặc Không còn hiệu lực, Hạn chế xử lý, hoặc thuộc Danh sách không quảng cáo (khi `CFG-CAMP-70` bật) | **= 0 (tuyệt đối)** | FEAT-08, 26, 28, 30, 32 |
| `KPI-02` | 1 | Tỷ lệ khiếu nại thư rác (`BR-33.2`), tính trên toàn Không gian làm việc theo khoảng trượt tại Phụ lục B.2 | **< 0,1%** | FEAT-08, 27, 29, 30 |
| `KPI-03` | 1 | Tỷ lệ điểm đến hỏng vĩnh viễn (`BR-33.2`), theo tháng | **< 2%** | FEAT-08, 28 |
| `KPI-04` | 2 | Số người nhận bị gửi vượt giới hạn tần suất bởi chiến dịch **không** được miễn trừ; số lượt miễn trừ được thống kê riêng (`BR-08.6`) | **= 0 (tuyệt đối)** | FEAT-08 |
| `KPI-05` | 3 | Số chiến dịch được phát sóng mà không qua phê duyệt kép, trong khi phê duyệt kép đang bật | **= 0 (tuyệt đối)** | FEAT-17, 19 |
| `KPI-06` | 3 | Thời gian từ khi bấm Tạm dừng tới khi không còn tin mới nào được chuyển nhà cung cấp | **≤ 1 phút** | FEAT-19, 20 |
| `KPI-07` | 4 | Tỷ lệ người nhận đủ điều kiện dự phòng (`BR-37.2`, `BR-37.3`) và có hạn mức cho đợt dự phòng (`BR-37.6`) được chuyển nhà cung cấp ở kênh dự phòng trong vòng thời gian chờ `CFG-CAMP-08` cộng 5 phút, tính từ thời điểm muộn nhất giữa lúc kênh trước xác nhận thất bại và lúc hết hoãn hợp lệ (khung giờ yên lặng, ngày không gửi) | **100%** | FEAT-37 |
| `KPI-08` | 5 | Tỷ lệ tin trả lời chiến dịch qua kênh có tiếp nhận hội thoại có mặt trong Hộp thư Hội thoại kèm đúng ngữ cảnh chiến dịch; với điểm đến gắn với đúng một hồ sơ thì gắn đúng hồ sơ đó (điểm đến dùng chung xử lý theo `omnichat-srs.md`, không tự gán hồ sơ) | **100%** | FEAT-35 |
| `KPI-09` | 6 | Tỷ lệ chiến dịch hoàn tất có báo cáo cơ hội và doanh thu chịu ảnh hưởng | **100%** | FEAT-34 |
| `KPI-10` | 7 | Số lần chi phí đã cam kết của một chiến dịch vượt ngân sách đã đặt quá mức sai số cho phép tại `BR-41.3` | **= 0** | FEAT-40, 41 |

*Ghi chú:* `KPI-02` tính theo khoảng trượt tại Phụ lục B.2, `KPI-03` theo tháng dương lịch (Mục 2.3). Tỷ lệ mở và tỷ lệ nhấp phụ thuộc chủ yếu vào chất lượng nội dung và tệp khách hàng của từng doanh nghiệp, không phải cam kết của hệ thống, nên không được đặt làm chỉ số thành công của phân hệ; hệ thống chỉ cam kết **đo đúng** chúng.

### 2.8 Luồng nghiệp vụ đầu–cuối

```
[1. Lập kế hoạch]  Tạo chiến dịch (Nháp) → chọn kênh, tài khoản gửi → đặt tiêu chí đối tượng
                   → xem trước quy mô và phân tích lý do loại trừ → xem mẫu người nhận
        │
[2. Soạn & kiểm tra]  Soạn nội dung theo kênh (và nội dung cho kênh dự phòng nếu có)
                   → cá nhân hóa, giá trị thay thế → gửi thử tới địa chỉ nội bộ
                   → danh mục kiểm tra trước phát sóng: lỗi chặn phải sửa, cảnh báo phải xác nhận
        │
[3. Phê duyệt]     Gửi phê duyệt (Chờ duyệt) → người có quyền Phát sóng chiến dịch, khác người soạn, xem phiên bản
                   → Phê duyệt (Đã duyệt) hoặc Từ chối kèm lý do (về Nháp)
        │
[4. Phát sóng]     Phát ngay hoặc chờ tới giờ hẹn → đối chiếu hạn mức toàn lô, hạn ngạch ngày, ngân sách
                   → chốt danh sách người nhận → gửi theo nhịp; ngay trước mỗi tin: kiểm tra lại đồng thuận,
                     tiếp cận, tần suất, khung giờ yên lặng
                   → kênh chính xác nhận không tới được → chờ → gửi dự phòng (nếu cấu hình)
        │
[5. Theo dõi]      Trạng thái phân phát và tương tác ghi vào sổ cái → hủy nhận tin / khiếu nại / hỏng ghi ngược
                     về hồ sơ khách hàng → dấu hiệu gây hại → tự động tạm dừng bảo vệ
                   → khách trả lời → Hộp thư Hội thoại
        │
[6. Đánh giá]      Hoàn tất → báo cáo phân phát, tương tác, chi phí → cơ hội và doanh thu chịu ảnh hưởng
                     trong cửa sổ ghi nhận → thử lại tin thất bại tạm thời (trong thời hạn cho phép)
```

---

## 3. Đặc tả yêu cầu chức năng

**Quy ước viện dẫn:** Mã `BR-xx.n`, `FEAT-xx`, `CFG-CAMP-xx` không kèm tên tài liệu là của chính tài liệu này. Mã thuộc tài liệu khác luôn ghi kèm tên tài liệu, ví dụ (`contacts-srs.md`, `BR-30.5`).

### Nhóm A — Quản trị Chiến dịch

#### FEAT-01 — Tạo mới & Quản lý Chiến dịch

**Mô tả nghiệp vụ:** Nhân viên Marketing tạo chiến dịch ở trạng thái Nháp, soạn dần các phần (kênh, tài khoản gửi, đối tượng, nội dung), lưu lại nhiều lần và theo dõi danh sách chiến dịch của mình.

**Quy tắc nghiệp vụ:**

- **`BR-01.1` (Thông tin tối thiểu để lưu nháp):** Chỉ bắt buộc **Tên chiến dịch** và **Kênh chính**. Kênh chính mặc định theo `CFG-CAMP-01`. Tài khoản gửi, tiêu chí đối tượng và nội dung được phép để trống khi lưu nháp.

  **Lý do nghiệp vụ:** Một chiến dịch thường được soạn trong nhiều ngày, qua nhiều người góp ý. Bắt buộc đủ mọi thông tin ngay khi tạo khiến người dùng điền tạm giá trị giả để lưu được — chính những giá trị giả đó là nguồn sai sót khi phát sóng.

- **`BR-01.2` (Điều kiện đầy đủ để gửi phê duyệt):** Chỉ gửi phê duyệt được khi có đủ: tài khoản gửi đang hoạt động (`FEAT-10`), tiêu chí đối tượng cho ra ít nhất một người nhận khả dụng (`FEAT-08`) — với Chiến dịch Tái tiếp cận, tính cả khách Đã rời bỏ trong phạm vi tập khách **sẽ được miễn nếu phần Tái tiếp cận được duyệt**, hiển thị riêng thành một dòng — nội dung đầy đủ cho kênh chính và cho mọi kênh dự phòng đã cấu hình (`FEAT-11`, `FEAT-37`), và danh mục kiểm tra trước phát sóng không còn lỗi chặn (`FEAT-16`).

  **Lý do nghiệp vụ:** Đưa một chiến dịch thiếu phần tới người duyệt làm lãng phí thời gian của cấp quản lý và tạo thói quen "duyệt đại cho xong".

- **`BR-01.3` (Người phụ trách & đơn vị tổ chức):** Người tạo mặc định là người phụ trách chiến dịch. Chiến dịch thuộc đơn vị theo định nghĩa "Bản ghi thuộc một đơn vị" của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4 — đơn vị đi theo **người phụ trách hiện tại**; phân hệ này không có hàng đợi chiến dịch nên không có ngoại lệ bản ghi công việc hay bản ghi chờ phân công cho chiến dịch. Đổi người phụ trách là thao tác **(Chiến dịch, Gán người phụ trách)**, chịu mức của ô đó, và được ghi nhật ký. Người phụ trách không quyết định tập người nhận (`BR-05.4`) và không là một phần của phiên bản duyệt, nên đổi người phụ trách ở **mọi** trạng thái không đưa chiến dịch về Nháp, không gỡ giờ hẹn và không đổi danh sách đã chốt; người phụ trách mới nhận các thông báo của chiến dịch từ thời điểm đổi.

  **Lý do nghiệp vụ:** Cùng nguyên tắc với cơ hội bán hàng (`deals-pipeline-srs.md`, Nguyên tắc 4): quản lý phải nhìn thấy chiến dịch mà nhân viên của mình đang chịu trách nhiệm, bất kể ai tạo ra nó.

- **`BR-01.4` (Phạm vi dữ liệu trên chiến dịch):** Mỗi thao tác trên chiến dịch dùng mức của đúng ô (Chiến dịch, thao tác đó) theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.7`: danh sách và chi tiết theo mức Xem; soạn, sửa và gửi phê duyệt theo mức Sửa; xóa, lưu trữ theo mức Xoá; xuất sổ cái và báo cáo theo mức Xuất; đổi người phụ trách theo mức Gán; tạo chiến dịch khi ô Tạo = Có; các thao tác của quyền Phát sóng chiến dịch theo mức của ô (Chiến dịch, Phát sóng) (`BR-17.1`). Các mức truy cập và cách xác định "bản ghi của mình", "bản ghi thuộc một đơn vị" theo đúng Mục 1.4 và [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-34`, `FEAT-35`; tài liệu này không định nghĩa lại. Người dùng mở đường dẫn tới một chiến dịch ngoài mức Xem nhận thông báo từ chối chung, không tiết lộ sự tồn tại của chiến dịch.

  **Lý do nghiệp vụ:** Chiến dịch chứa tiêu chí đối tượng, nội dung chưa công bố và số liệu hiệu quả của từng đơn vị; không giới hạn theo phạm vi thì một nhân viên nhìn được toàn bộ kế hoạch tiếp thị và danh sách khách hàng mục tiêu của đơn vị khác.

- **`BR-01.5` (Nhóm mục đích gửi cố định là Tiếp thị):** Trừ Thông báo dịch vụ (`FEAT-45`), mọi lượt gửi phát ra từ phân hệ Chiến dịch — gồm lượt gửi chính, lượt gửi dự phòng, lượt gửi của chuỗi nuôi dưỡng và lượt thử lại — đều được khai báo thuộc nhóm mục đích **Tiếp thị & Quảng bá** theo (`contacts-srs.md`, `BR-30.5`). Không có tùy chọn nào trong phân hệ này để khai báo nhóm mục đích khác. Riêng lượt **gửi thử** tới địa chỉ nội bộ (`FEAT-15`) không phải thông điệp gửi tới khách hàng và không thuộc phạm vi quy tắc này.

  **Lý do nghiệp vụ:** Lượt gửi hàng loạt dùng nội dung do bộ phận Marketing soạn trong công cụ chiến dịch không bao giờ thỏa tiêu chí Liên lạc 1-1 (`contacts-srs.md`, `BR-30.7`), và nhóm Tiếp thị là nhóm duy nhất bị chặn tuyệt đối bởi Từ chối nhận tin. Nếu cho phép người soạn tự chọn "Giao dịch & Dịch vụ", một bản tin khuyến mãi chỉ cần đổi nhãn là vượt qua được toàn bộ cơ chế đồng thuận. Nhu cầu gửi thông báo dịch vụ hàng loạt được đáp ứng bằng một loại chiến dịch riêng có kiểm soát (`FEAT-45`), không bằng việc cho người soạn tự chọn nhóm mục đích.

- **`BR-01.6` (Quyền sửa theo trạng thái):** Chỉ được sửa nội dung, đối tượng, kênh, tài khoản gửi, cấu hình dự phòng và lịch gửi khi chiến dịch ở **Nháp**. Thao tác sửa trên chiến dịch **Chờ duyệt** hoặc **Đã duyệt** được phép nhưng đưa chiến dịch về **Nháp** và hủy yêu cầu duyệt/kết quả duyệt trước đó (người dùng được cảnh báo trước khi lưu). Chiến dịch **Giữ lại** sửa được như Đã duyệt (đưa về Nháp). Chiến dịch **Tạm dừng** hoặc **Tạm dừng — chờ duyệt phiên bản** chỉ sửa được theo `BR-19.3`, cộng tên, mô tả nội bộ và người phụ trách. Chiến dịch **Đang gửi**, **Hoàn tất**, **Đã hủy**, **Lưu trữ** không sửa được các phần trên; chỉ sửa được tên hiển thị nội bộ, mô tả nội bộ và người phụ trách. Đổi người phụ trách ở mọi trạng thái tuân theo `BR-01.3` và không đưa chiến dịch về Nháp. **Ngân sách chiến dịch** (`BR-41.2`) không thuộc phiên bản duyệt: người có quyền Phát sóng chiến dịch sửa được ở mọi trạng thái trước Hoàn tất / Đã hủy, kể cả Đang gửi và Tạm dừng, và ở Hoàn tất trong thời hạn gửi lại thủ công `CFG-CAMP-16` (`BR-25.2`); thay đổi được ghi nhật ký, không tạo phiên bản mới, không đổi trạng thái. Được **tăng** tự do; được **giảm** nhưng không thấp hơn chi phí đã cam kết — hệ quả của mức mới áp theo `BR-41.2` (không bắt đầu gửi, hoặc tự động tạm dừng bảo vệ khi chạm).

  **Lý do nghiệp vụ:** Bảo đảm Nguyên tắc 2 (Mục 2.4): thứ tới tay khách hàng luôn là đúng thứ đã được duyệt. Tên và mô tả nội bộ không tới tay khách hàng nên không cần khóa.

- **`BR-01.7` (Phát hiện xung đột khi hai người cùng sửa):** Chiến dịch được chia thành ba phần sửa độc lập: **Nội dung** (mọi kênh, mọi phiên bản A/B), **Đối tượng** (tiêu chí, tập loại trừ, Tái tiếp cận) và **Cấu hình gửi** (tài khoản gửi, dự phòng, nhịp, lịch, ngân sách, cách xử lý hạn ngạch). Mỗi lần lưu một phần mang theo phiên bản của phần đó đã đọc. Nếu trong lúc đó đã có người khác lưu **cùng phần**, hệ thống từ chối lưu, báo ai đã sửa gần nhất và yêu cầu tải lại; hai người sửa hai phần khác nhau không chặn nhau. Thao tác phê duyệt cũng tuân theo quy tắc này: phê duyệt dựa trên một phiên bản đã cũ bị từ chối (`BR-17.3`).

  **Lý do nghiệp vụ:** Không có quy tắc này, người A đang sửa liên kết trong khi người B bấm gửi phê duyệt với nội dung cũ — người duyệt duyệt một phiên bản không còn tồn tại. Chia theo phần để người viết nội dung và người chọn đối tượng — hai người thường làm song song — không liên tục bị từ chối lưu của nhau.

- **`BR-01.8` (Người phụ trách bị tạm ngưng hoặc rời workspace):** Chiến dịch chưa Hoàn tất, chưa Đã hủy và chưa Lưu trữ là **bản ghi đang mở** của người phụ trách theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-42`, `FEAT-43`; chuỗi nuôi dưỡng chưa Kết thúc cũng vậy. Hệ thống **không** tự chuyển chiến dịch cho ai.
  - **Tạm ngưng** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-42`): tạm ngưng có hiệu lực ngay, không chờ xử lý chiến dịch. Sau đó, như một thao tác riêng, người thực hiện chọn **giữ nguyên** hoặc **chuyển tạm** các chiến dịch đang mở cho một người xử lý thay đạt [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.2`, `BR-43.4` (Đang hoạt động, ô (Chiến dịch, Sửa) khác Không có); chiến dịch không có lựa chọn "trả về hàng đợi". Việc người phụ trách bị tạm ngưng không đổi trạng thái chiến dịch; riêng lượt gửi do chính người đó khởi chạy xử lý theo `BR-17.11`.
  - **Rời workspace** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-43`): các chiến dịch và chuỗi đang mở của người rời đi là một bước bắt buộc của danh sách bàn giao — chọn người nhận (một người cho tất cả hoặc từng chiến dịch), người nhận phải đạt [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.2`, `BR-43.4`; chưa xong bước này thì chưa gỡ được thành viên. Lượt gửi do người rời đi khởi chạy là bước "tiến trình đang chạy" riêng (`BR-17.11`).
  - **Hai đường của người phụ trách hiện tại** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`), không phụ thuộc trạng thái thành viên và ô (Chiến dịch, Gán) của họ: (1) **trả về hàng đợi** không áp dụng cho chiến dịch vì phân hệ không có hàng đợi chiến dịch (`BR-35.5` (f)); (2) **đề nghị chuyển** chiến dịch cho một người cụ thể — chỉ có hiệu lực khi người nhận chấp nhận, và tại lúc chấp nhận người nhận phải Đang hoạt động, có ô (Chiến dịch, Gán) khác Không có, xem được chiến dịch và không bị nguồn chặn nào; đề nghị hết hạn theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-25-01` hoặc khi người phụ trách đổi. Đổi người phụ trách theo đường này tuân `BR-01.3`.
  - Đổi người phụ trách theo hai đường trên tuân đúng `BR-01.3`: không đổi trạng thái, không đổi phiên bản duyệt. Nếu người rời đi đồng thời là **người chọn đối tượng** của một chiến dịch chưa chốt danh sách, người nhận bàn giao chiến dịch đó trở thành người chọn đối tượng và phạm vi đối tượng tính lại theo mức Xem trên Khách hàng của họ (`BR-05.4`); chênh lệch quy mô được xét tại lúc chốt theo `BR-06.4`, và với chiến dịch Chờ duyệt, người duyệt thấy con số mới trên màn hình duyệt.

  **Lý do nghiệp vụ:** Tự chuyển chiến dịch cho quản lý trực tiếp ngay khi một người nghỉ vừa bỏ qua quy trình bàn giao có biên bản của nền tảng, vừa có thể giao chiến dịch cho người không sửa được nó. Cắt truy cập phải nhanh và không phụ thuộc việc tìm người thay; nhưng gỡ một người khi chiến dịch của họ chưa có ai nhận thì chiến dịch hẹn giờ sẽ chạy dưới tên người đã nghỉ mà không ai chịu trách nhiệm.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-01.1.1` | Màn hình tạo chiến dịch | Nhập tên, giữ kênh mặc định, lưu | Chiến dịch được tạo ở trạng thái Nháp, kênh chính đúng theo `CFG-CAMP-01` |
| `AC-01.1.2` | Màn hình tạo chiến dịch | Bỏ trống tên, lưu | Từ chối, báo rõ trường Tên chiến dịch bắt buộc |
| `AC-01.2.1` | Chiến dịch Nháp chưa chọn tài khoản gửi | Bấm "Gửi phê duyệt" | Nút bị vô hiệu hoặc từ chối, giao diện liệt kê rõ phần còn thiếu ("Chưa chọn tài khoản gửi") trước khi người dùng thao tác |
| `AC-01.2.2` | Chiến dịch Nháp có tiêu chí đối tượng cho ra 0 người nhận khả dụng | Bấm "Gửi phê duyệt" | Từ chối, báo "Không có người nhận khả dụng" kèm phân tích lý do loại trừ (`BR-06.2`) |
| `AC-01.2.3` | Chiến dịch có cấu hình dự phòng SMS nhưng chưa soạn nội dung SMS | Bấm "Gửi phê duyệt" | Từ chối, báo thiếu nội dung cho kênh dự phòng SMS |
| `AC-01.2.4` | Chiến dịch Tái tiếp cận chỉ nhắm 800 khách Đã rời bỏ trong phạm vi tập khách, chưa có phê duyệt Tái tiếp cận | Bấm "Gửi phê duyệt" | Gửi phê duyệt được; màn hình hiển thị "0 khả dụng hiện tại · 800 khả dụng nếu phần Tái tiếp cận được duyệt" — con số thứ hai không gồm khách chưa có người duyệt Tái tiếp cận (`BR-31.2`) |
| `AC-01.3.1` | Nhân viên phòng A tạo chiến dịch; người có (Chiến dịch, Gán) bao phủ chiến dịch đổi người phụ trách sang nhân viên phòng B | Người phụ trách đơn vị B (mức Xem Đơn vị của mình) mở danh sách chiến dịch | Thấy chiến dịch này; người phụ trách đơn vị A (mức Xem Đơn vị của mình) không còn thấy |
| `AC-01.3.2` | Chiến dịch Đã duyệt, hẹn giờ | Đổi người phụ trách sang người thuộc đơn vị khác | Chiến dịch vẫn Đã duyệt, giờ hẹn giữ nguyên; người phụ trách mới nhận thông báo; nhật ký ghi lần đổi |
| `AC-01.3.3` | Người dùng có (Chiến dịch, Sửa) = Đơn vị của mình nhưng (Chiến dịch, Gán) = Không có | Mở chiến dịch của đơn vị mình | Thao tác đổi người phụ trách không hiển thị; các phần soạn thảo vẫn sửa được |
| `AC-01.3.4` | Chiến dịch Đang gửi dàn trải nhiều ngày | Đổi người phụ trách | Chiến dịch vẫn Đang gửi, danh sách chốt không đổi; người khởi chạy không đổi |
| `AC-01.8.1` | Người phụ trách P của 3 chiến dịch Nháp và 1 chiến dịch Đã duyệt hẹn giờ bị tạm ngưng | Người thực hiện tạm ngưng P | P bị tạm ngưng ngay; màn hình đề nghị giữ nguyên hoặc chuyển tạm 4 chiến dịch; không có lựa chọn trả về hàng đợi; trạng thái 4 chiến dịch không đổi |
| `AC-01.8.2` | Tiếp nối AC-01.8.1; người thực hiện chọn chuyển tạm cho R có (Chiến dịch, Sửa) = Không có | Chọn R | R không chọn được, kèm lý do |
| `AC-01.8.3` | Người phụ trách P của 5 chiến dịch đang mở bắt đầu quy trình rời workspace | Mở Rời workspace | Có bước "5 chiến dịch" bắt buộc chọn người nhận; nút Gỡ bị vô hiệu tới khi bước hoàn tất; không chiến dịch nào tự chuyển cho quản lý trực tiếp |
| `AC-01.8.5` | Nhân viên Marketing A (Gán = Không có) phụ trách chiến dịch Nháp | A đề nghị chuyển cho Quản lý Marketing M; M chấp nhận | M là người phụ trách; nhật ký ghi đề nghị và lượt chấp nhận |
| `AC-01.8.6` | A đề nghị chuyển chiến dịch cho đồng nghiệp B có (Chiến dịch, Gán) = Không có | B mở đề nghị | B không chấp nhận được, kèm lý do; A vẫn là người phụ trách |
| `AC-01.8.7` | A đề nghị chuyển cho M, [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-25-01` = 3 ngày; M không phản hồi | Hết 3 ngày | Đề nghị hết hạn; A vẫn là người phụ trách và được thông báo |
| `AC-01.8.4` | Chiến dịch Đã duyệt hẹn 7 ngày sau do P chọn đối tượng; P rời đi, chiến dịch giao cho Q có mức Xem Khách hàng hẹp hơn làm số người nhận khả dụng giảm 25% | Hoàn tất bàn giao, rồi tới giờ hẹn | Lúc bàn giao: chiến dịch vẫn Đã duyệt, Q là người phụ trách và người chọn đối tượng; tới giờ hẹn: chiến dịch Giữ lại chờ xác nhận quy mô theo `BR-06.4` |
| `AC-01.4.1` | A có (Chiến dịch, Xem) = Chỉ của mình | Mở đường dẫn tới chiến dịch của đồng nghiệp | Từ chối truy cập, thông báo không tiết lộ chiến dịch có tồn tại hay không |
| `AC-01.4.2` | A giữ vai trò dựng sẵn Marketing mặc định: (Chiến dịch, Xem) = Đơn vị của mình, (Chiến dịch, Sửa) = Chỉ của mình | Mở chiến dịch của đồng nghiệp cùng đơn vị | Xem được chiến dịch ở chế độ chỉ đọc; không có thao tác lưu hay gửi phê duyệt |
| `AC-01.4.3` | Vai trò tự tạo có (Chiến dịch, Xem) = Đơn vị và các đơn vị con tại "Marketing miền Bắc" | Mở danh sách chiến dịch | Thấy chiến dịch của "Marketing miền Bắc" và các đơn vị con; không thấy chiến dịch của đơn vị ngang hàng hay đơn vị cha |
| `AC-01.5.1` | Màn hình soạn chiến dịch | Tìm tùy chọn chọn nhóm mục đích gửi | Không có tùy chọn; màn hình hiển thị cố định "Nhóm mục đích: Tiếp thị & Quảng bá" |
| `AC-01.6.1` | Chiến dịch Đã duyệt, có giờ hẹn | Sửa tiêu đề email, lưu | Hệ thống cảnh báo trước "Chiến dịch sẽ về Nháp và phải phê duyệt lại"; sau khi xác nhận, chiến dịch về Nháp, giờ hẹn bị gỡ |
| `AC-01.6.2` | Chiến dịch Đang gửi | Mở màn hình soạn | Nội dung, đối tượng, tài khoản gửi ở chế độ chỉ đọc; tên và mô tả nội bộ vẫn sửa được |
| `AC-01.6.3` | Chiến dịch tự tạm dừng bảo vệ vì chi phí đã cam kết chạm ngân sách 10.000.000 đ | Quản lý Marketing tăng ngân sách lên 15.000.000 đ | Lưu được, ghi nhật ký, chiến dịch vẫn Tạm dừng và không tạo phiên bản mới; sau đó tiếp tục được nếu chi phí ước tính phần còn lại trong 5.000.000 đ còn lại |
| `AC-01.6.4` | Chiến dịch Đang gửi, chi phí đã cam kết 6.000.000 đ | Giảm ngân sách xuống 5.000.000 đ | Không cho phép; giao diện nêu mức thấp nhất là chi phí đã cam kết |
| `AC-01.6.5` | Chiến dịch Hoàn tất 10 giờ trước, `CFG-CAMP-16` = 72 giờ; gửi lại nhóm Thất bại tạm thời bị chặn vì chi phí ước tính vượt ngân sách còn lại | Quản lý Marketing tăng ngân sách rồi gửi lại | Tăng được, ghi nhật ký; gửi lại qua đối chiếu ngân sách |
| `AC-01.7.1` | A và B cùng mở một chiến dịch Nháp; A lưu trước | B lưu thay đổi của mình | Từ chối, báo A vừa sửa, yêu cầu B tải lại |
| `AC-01.7.2` | A đang sửa phần Nội dung, B đang sửa phần Đối tượng của cùng chiến dịch | Cả hai lưu | Cả hai lưu thành công |

---

#### FEAT-02 — Nhân bản Chiến dịch

**Mô tả nghiệp vụ:** Tạo nhanh chiến dịch mới từ một chiến dịch có sẵn (ở bất kỳ trạng thái nào) để tái sử dụng cấu hình cho đợt gửi tiếp theo.

**Quy tắc nghiệp vụ:**

- **`BR-02.1` (Phần được sao chép):** Bản sao nhận: kênh chính, tài khoản gửi, tiêu chí đối tượng, nội dung mọi kênh (gồm các phiên bản thử nghiệm A/B), cấu hình dự phòng, nhịp gửi, ngân sách. Tên bản sao là tên gốc kèm hậu tố "(Bản sao)"; người phụ trách là người thực hiện nhân bản.

  **Lý do nghiệp vụ:** Nhân bản để tái dùng công sức soạn một chiến dịch đã chạy tốt; người nhân bản là người phụ trách vì chính họ sẽ chịu trách nhiệm về đợt gửi mới, không phải người soạn bản gốc.

- **`BR-02.2` (Phần không được sao chép):** Bản sao **không** nhận: mã định danh (được cấp mã mới theo `FEAT-03`), giờ hẹn, lịch sử và kết quả phê duyệt, danh sách người nhận chốt, sổ cái, số liệu báo cáo, kết quả thử nghiệm A/B, và **phê duyệt Chiến dịch Tái tiếp cận** (`FEAT-31`). Bản sao luôn ở trạng thái Nháp.

  **Lý do nghiệp vụ:** Phê duyệt gắn với một phiên bản và một thời điểm cụ thể; sao chép phê duyệt sang chiến dịch mới đồng nghĩa với việc một chiến dịch được phát sóng mà chưa ai duyệt nó. Riêng phê duyệt Tái tiếp cận có phạm vi tập khách và thời hạn hiệu lực riêng theo (`contacts-srs.md`, `BR-12.5b`) nên càng không được kế thừa.

- **`BR-02.3` (Cảnh báo cấu hình đã lỗi thời):** Nếu tài khoản gửi, tin nhắn mẫu đã phê duyệt hoặc trường thông tin cá nhân hóa trong bản gốc không còn dùng được tại thời điểm nhân bản, bản sao vẫn được tạo nhưng phần đó được đánh dấu cần chọn lại, và hiện trong danh mục kiểm tra trước phát sóng như một lỗi chặn.

  **Lý do nghiệp vụ:** Chặn tạo bản sao làm mất công sức soạn; im lặng sao chép một mẫu đã bị khóa thì lỗi chỉ lộ ra khi gửi.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-02.1.1` | Chiến dịch "Khuyến mãi Tháng 9" đã Hoàn tất | Nhân bản | Có chiến dịch mới "Khuyến mãi Tháng 9 (Bản sao)" ở Nháp, cùng nội dung, đối tượng, tài khoản gửi; người phụ trách là người vừa nhân bản |
| `AC-02.2.1` | Tiếp nối AC-02.1.1 | Mở bản sao | Mã định danh khác bản gốc; không có giờ hẹn, không có lịch sử phê duyệt, báo cáo trống |
| `AC-02.2.2` | Bản gốc là Chiến dịch Tái tiếp cận đã được phê duyệt đầy đủ | Nhân bản | Bản sao vẫn được đánh dấu là Chiến dịch Tái tiếp cận nhưng phê duyệt Tái tiếp cận ở trạng thái "Chưa phê duyệt" |
| `AC-02.3.1` | Tin nhắn mẫu dùng trong bản gốc đã bị nền tảng nhắn tin tạm khóa | Nhân bản | Bản sao được tạo; phần nội dung báo "Mẫu không còn dùng được, chọn mẫu khác"; danh mục kiểm tra hiện lỗi chặn |

---

#### FEAT-03 — Mã Định danh Chiến dịch

**Mô tả nghiệp vụ:** Mỗi chiến dịch được cấp tự động một mã ngắn, dễ đọc, duy nhất, dùng để tra cứu, đối soát chi phí với nhà cung cấp và làm giá trị tên chiến dịch trong tham số nguồn gốc của liên kết (`FEAT-14`).

**Quy tắc nghiệp vụ:**

- **`BR-03.1` (Duy nhất & bất biến):** Mã duy nhất trong phạm vi Không gian làm việc, được cấp khi tạo chiến dịch, không sửa được, và **không bao giờ được cấp lại** cho chiến dịch khác kể cả sau khi chiến dịch gốc bị xóa vĩnh viễn. Mã có dạng tiền tố cố định, năm tạo (theo múi giờ Không gian làm việc) và một chuỗi định danh, ví dụ `CMP-2026-X89Q`.

  **Lý do nghiệp vụ:** Mã được gắn vào liên kết đã gửi tới khách hàng và vào dữ liệu nguồn gốc của khách hàng tiềm năng. Nếu một mã được tái sử dụng, lượt nhấp từ một email cũ khách hàng mở lại sau nhiều tháng sẽ bị ghi nhận nhầm cho chiến dịch mới.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-03.1.1` | Tạo hai chiến dịch liên tiếp | Xem mã của hai chiến dịch | Hai mã khác nhau, đúng định dạng |
| `AC-03.1.2` | Chiến dịch có mã X bị xóa vĩnh viễn | Tạo nhiều chiến dịch mới | Không chiến dịch nào nhận lại mã X |
| `AC-03.1.3` | Màn hình chi tiết chiến dịch | Tìm cách sửa mã | Mã chỉ đọc |

---

#### FEAT-04 — Xóa, Thùng rác & Lưu trữ Chiến dịch

**Mô tả nghiệp vụ:** Dọn các chiến dịch không còn dùng mà không làm mất bằng chứng về những gì đã gửi tới khách hàng.

**Quy tắc nghiệp vụ:**

- **`BR-04.1` (Chỉ xóa được chiến dịch chưa từng gửi):** Chỉ chiến dịch ở **Nháp** hoặc **Chờ duyệt** và **chưa từng gửi tin nào tới khách hàng** mới xóa được. Chiến dịch đã gửi ít nhất một tin (kể cả đã hủy giữa chừng) không xóa được, chỉ **lưu trữ** được.

  **Lý do nghiệp vụ:** Sổ cái của chiến dịch đã gửi là bằng chứng rằng doanh nghiệp đã tôn trọng đồng thuận của từng người nhận — thứ doanh nghiệp phải xuất trình khi có khiếu nại hoặc thanh tra. Cho xóa chiến dịch đã gửi là cho xóa bằng chứng tuân thủ. Việc xóa dữ liệu cá nhân của một người cụ thể trong sổ cái đi theo đường riêng tại `FEAT-32`.

- **`BR-04.2` (Thùng rác):** Chiến dịch bị xóa vào Thùng rác, khôi phục được về đúng trạng thái Nháp trong thời hạn `CFG-CAMP-21`; hết thời hạn, hệ thống xóa vĩnh viễn. Chiến dịch Chờ duyệt bị xóa thì yêu cầu duyệt bị hủy, người duyệt được thông báo.

  **Lý do nghiệp vụ:** Xóa nhầm một chiến dịch đang soạn dở làm mất nhiều ngày công; thùng rác cho khoảng thời gian để phát hiện và khôi phục mà không cần hỗ trợ kỹ thuật.

- **`BR-04.3` (Lưu trữ):** Chiến dịch Hoàn tất hoặc Đã hủy lưu trữ được để ẩn khỏi danh sách mặc định. Chiến dịch lưu trữ chỉ sửa được tên, mô tả nội bộ và người phụ trách (`BR-01.6`); báo cáo, sổ cái và số liệu ghi nhận ảnh hưởng (`FEAT-34`) giữ nguyên và **tiếp tục được cập nhật** cho tới hết cửa sổ ghi nhận. Bỏ lưu trữ đưa chiến dịch về đúng trạng thái trước khi lưu trữ. Không lưu trữ được chiến dịch Đang gửi, Tạm dừng, hoặc Hoàn tất mà đang có lượt gửi lại chạy (`BR-25.2`).

  **Lý do nghiệp vụ:** Người dùng thường lưu trữ ngay sau khi gửi xong, trong khi khách hàng vẫn còn mở email và cơ hội bán hàng vẫn còn phát sinh trong nhiều tuần — nếu lưu trữ làm ngừng cập nhật, báo cáo hiệu quả bị cắt cụt một cách âm thầm.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-04.1.1` | Chiến dịch Nháp chưa từng gửi | Xóa | Chiến dịch vào Thùng rác |
| `AC-04.1.2` | Chiến dịch Đã hủy sau khi đã gửi 300 tin | Tìm thao tác Xóa | Không có thao tác Xóa, chỉ có Lưu trữ; giao diện giải thích lý do |
| `AC-04.2.1` | Chiến dịch trong Thùng rác, còn trong thời hạn `CFG-CAMP-21` | Khôi phục | Chiến dịch về Nháp, nguyên nội dung |
| `AC-04.2.2` | Chiến dịch trong Thùng rác đã quá thời hạn `CFG-CAMP-21` (dùng dịch chuyển đồng hồ `NFR-11`) | Tìm trong Thùng rác | Không còn; không khôi phục được |
| `AC-04.2.3` | Chiến dịch Chờ duyệt | Người soạn xóa chiến dịch | Chiến dịch vào Thùng rác; người đã được thông báo duyệt nhận thông báo yêu cầu duyệt đã bị hủy |
| `AC-04.3.1` | Chiến dịch Hoàn tất đã lưu trữ; 5 ngày sau một người nhận nhấp liên kết | Mở báo cáo chiến dịch | Lượt nhấp mới được ghi nhận |
| `AC-04.3.2` | Chiến dịch Tạm dừng | Tìm thao tác Lưu trữ | Không có; giao diện báo phải hủy hoặc hoàn tất trước |

---

### Nhóm B — Đối tượng Nhận tin

#### FEAT-05 — Tiêu chí Chọn Đối tượng

**Mô tả nghiệp vụ:** Nhân viên Marketing xác định ai nhận chiến dịch bằng tổ hợp điều kiện trên hồ sơ khách hàng, không phải bằng danh sách tải lên từ bên ngoài.

**Quy tắc nghiệp vụ:**

- **`BR-05.1` (Nguồn tiêu chí):** Tiêu chí chọn được dựa trên: thẻ phân loại, giai đoạn vòng đời, điểm tiềm năng, trường tùy biến của hồ sơ khách hàng và doanh nghiệp, khu vực địa lý, thời điểm tạo hồ sơ, người phụ trách và đơn vị phụ trách khách hàng, **Danh sách Hiển thị Dùng chung** (`contacts-srs.md`, `FEAT-26`) — chỉ khi bộ lọc của danh sách không dùng trường mà người soạn không được xem, và không dùng trường nhạy cảm trừ khi đi đúng đường ngoại lệ của `BR-05.5`. Khi chọn, **bộ lọc hiện tại của danh sách được sao vào tiêu chí của chiến dịch** và từ đó là một phần của phiên bản duyệt (`BR-17.4`); chủ danh sách sửa danh sách sau đó không làm đổi đối tượng của chiến dịch — người soạn muốn theo bộ lọc mới thì chọn lại danh sách, tạo phiên bản mới cần duyệt lại. Khi danh sách gốc đổi bộ lọc, người phụ trách của mọi chiến dịch chưa chốt danh sách và mọi chuỗi chưa Kết thúc đang dùng bản sao được thông báo; tiêu chí hiển thị dấu "khác với danh sách hiện tại" kèm thao tác "Cập nhật theo danh sách" (tạo phiên bản mới cần duyệt). Điều kiện tương đối trong bộ lọc đã sao (ví dụ "khách hàng của tôi", "tạo trong 7 ngày qua") được hiểu theo **người chọn đối tượng** và theo **thời điểm chốt danh sách** (với chuỗi: thời điểm xét một người vào chuỗi); danh sách có điều kiện dùng nguồn nằm ngoài các nguồn của quy tắc này thì không chọn được làm tiêu chí.
  Nguồn tiêu chí cuối cùng là **hành vi với các chiến dịch trước** (đã nhận / đã mở / đã nhấp / chưa mở một chiến dịch cụ thể). Người không có căn cứ theo dõi hành vi (`BR-14.4`) **không khớp** bất kỳ điều kiện hành vi mở/nhấp nào — cả "đã" lẫn "chưa" — để hệ thống không suy diễn hành vi mà nó không được phép đo. Chiến dịch đã hoàn tất quá `CFG-CAMP-45` (dữ liệu mở/nhấp gắn danh tính đã được khử) không chọn được làm điều kiện "đã mở / đã nhấp / chưa mở"; chỉ còn dùng được điều kiện "đã nhận". Các điều kiện kết hợp bằng "và", "hoặc", và nhóm lồng nhau.

  **Lý do nghiệp vụ:** Tiêu chí dựa trên dữ liệu đã có trong hồ sơ để mọi người nhận đều mang theo trạng thái đồng thuận và phạm vi dữ liệu của chính hồ sơ đó; không có nguồn tiêu chí nào đi vòng qua hồ sơ khách hàng.

- **`BR-05.2` (Tập loại trừ chủ động):** Ngoài tiêu chí chọn, người soạn được thêm một hoặc nhiều **tập loại trừ** (ví dụ: "khách đang có vé hỗ trợ mở", "đã nhận chiến dịch X"). Một người khớp cả tiêu chí chọn và tập loại trừ thì bị loại, với lý do "Thuộc tập loại trừ của chiến dịch". Người soạn đánh dấu được từng tập loại trừ là **"xét lại tại thời điểm gửi"** (ví dụ "khách đang có vé khiếu nại mở"); tập được đánh dấu được kiểm tra lại trước mỗi tin (`BR-08.4`).

  **Lý do nghiệp vụ:** Khách vừa mở một vé khiếu nại lỗi sản phẩm sau lúc chốt danh sách mà vẫn nhận khuyến mãi là trải nghiệm tệ; nhưng xét lại mọi tập loại trừ trước mỗi tin là tốn kém không cần thiết với các tập ổn định.

- **`BR-05.3` (Chỉ khách hàng có hồ sơ trong hệ thống):** Người nhận chỉ có thể là khách hàng có hồ sơ trong phân hệ Khách hàng. Không có đường tải danh sách điểm đến trực tiếp vào chiến dịch. Muốn gửi cho một danh sách bên ngoài, doanh nghiệp phải nhập danh sách đó vào phân hệ Khách hàng trước, nơi bắt buộc khai báo Cơ sở đồng thuận (`contacts-srs.md`, `BR-30.4`).

  **Lý do nghiệp vụ:** Đồng thuận, trạng thái tiếp cận và bằng chứng đồng thuận chỉ tồn tại trên hồ sơ khách hàng. Một danh sách tải thẳng vào chiến dịch là danh sách không ai kiểm được ai đã đồng ý — đúng con đường mà các vụ phạt tin rác thường bắt nguồn.

- **`BR-05.4` (Phạm vi dữ liệu giới hạn đối tượng):** Chọn khách hàng vào đối tượng là một lượt **xem** khách hàng. **Tập khách hàng xem được** của một người cho mục đích chiến dịch được xác định theo bước 1–3 của thứ tự hợp nhất quyền tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`, với một **thu hẹp có chủ đích so với [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`** ở bước 2 (một số nguồn nới không được tính):
  1. **Mức theo ô (Khách hàng, Xem)** từ vai trò, nhóm và mức nền, sau thu hồi riêng lẻ và trần quyền của workspace; **không** tính quyền tạm thời. Mức Sửa, Xuất hay Gán trên Khách hàng không giới hạn và không nới đối tượng ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.7`).
  2. **Nguồn nới phạm vi được tính:** chỉ công khai đọc, phụ trách đơn vị và chính sách Cho phép ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-34.4`, `BR-34.3`, `BR-36.2`). **Không tính:** mọi lượt cấp trên bản ghi — lượt đặt tay cũng như lượt do phân hệ sinh ra như quyền đọc tự động của tuyến hỗ trợ khi có vé hay hội thoại đang mở và đội ngũ phụ trách ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.5`) — và quyền thấy bản ghi chưa phân công của hàng đợi nhờ là thành viên đơn vị tiếp nhận ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.11`) — quyền này chỉ để nhận việc: khách hàng tiềm năng đang chờ phân công chưa có ai chịu trách nhiệm quan hệ, và một nhân viên Marketing kiêm nhiệm đơn vị tiếp nhận không được đưa họ vào chiến dịch chỉ vì thấy được hàng đợi.
  - **Thời điểm chốt nguồn nới:** nguồn nới ở bước 2 được chốt tại lúc chốt danh sách (chiến dịch một lần) hoặc lúc phiên bản chuỗi nuôi dưỡng đang chạy được duyệt; nguồn nới phát sinh sau đó không mở rộng đối tượng, còn nguồn nới bị gỡ sau đó thì thu hẹp theo (phần giao của lúc chốt và hiện tại). Nguồn chặn luôn tính tại thời điểm xét.
  3. **Nguồn chặn luôn áp dụng:** chính sách Từ chối, lượt chặn trên bản ghi, Sàn bắt buộc ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-36.1`, `BR-39.1`, `BR-25.3`); **bản ghi giữ chỗ** cho người chưa chấp nhận lời mời ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10`) không bao giờ thuộc đối tượng chiến dịch, kể cả với nhóm ngoại lệ của bản ghi giữ chỗ và Người có toàn quyền.

  Bước 4 (phân quyền trường và che dữ liệu) quyết định trường nào dùng được làm tiêu chí và cá nhân hóa (`BR-05.5`, `BR-12.1`). Cách áp:
  - (a) **Khi chọn:** tiêu chí, xem trước quy mô, phân tích loại trừ và mẫu người nhận chỉ tính trên **tập khách hàng xem được** của **người chọn đối tượng** (Mục 1.4) tại thời điểm tính. Người khác mở phần Đối tượng thấy con số tính theo tập xem được của chính họ; lưu phần Đối tượng thì họ trở thành người chọn đối tượng và phiên bản mới cần duyệt lại (`BR-17.4`).
  - (b) **Khi duyệt và phát sóng:** màn hình duyệt và hộp xác nhận phát sóng hiển thị số người nhận khả dụng trên **phần giao** tập xem được của người chọn đối tượng và của người đang thao tác — đúng tập sẽ được gửi nếu người đó khởi chạy (`BR-17.10`).
  - (c) **Khi chốt danh sách:** danh sách chốt chỉ gồm khách hàng khớp tiêu chí nằm trong phần giao tập xem được của người chọn đối tượng và của người khởi chạy (tính theo `BR-17.10`). Khách hàng ngoài phần giao không được tính, không được đếm, không xuất hiện trong bất kỳ con số nào của chiến dịch. Người chọn đối tượng không Đang hoạt động tại lúc chốt (tạm ngưng, đang trong quy trình rời workspace) thì chiến dịch không bắt đầu gửi mà chuyển Giữ lại với lý do "Người chọn đối tượng không còn hoạt động", cho tới khi vai trò được bàn giao (`BR-01.8`) hoặc một người có ô (Chiến dịch, Sửa) bao phủ chiến dịch lưu lại phần Đối tượng (phiên bản mới cần duyệt).
  - (d) **Sau khi chốt:** đổi người phụ trách hay người chọn đối tượng không đổi danh sách đã chốt; mỗi tin vẫn chịu kiểm tra quyền của người khởi chạy tại thời điểm gửi (`BR-08.1` lý do 18).
  - (e) **Chuỗi nuôi dưỡng:** mỗi lần xét một người vào chuỗi, người đó phải thuộc tập xem được của người chọn đối tượng theo **phần giao** của mức lúc chuỗi được bắt đầu (hoặc lúc người đó trở thành người chọn đối tượng) và mức hiện tại — cùng cách tính với người khởi chạy (`BR-17.10`) — và thuộc tập xem được của người khởi chạy chuỗi. Khi người chọn đối tượng của một chuỗi chưa Kết thúc bị tạm ngưng hoặc bắt đầu quy trình rời workspace, chuỗi **tạm không nhận người mới** (vẫn ở trạng thái hiện tại; người đang trong chuỗi đi tiếp các bước) với lý do hiển thị "Chờ bàn giao người chọn đối tượng", và người phụ trách chuỗi, quản lý trực tiếp của người chọn đối tượng và Người có toàn quyền được thông báo. Chuỗi nhận người mới trở lại khi vai trò người chọn đối tượng được bàn giao (`BR-01.8`) hoặc một người có ô (Chiến dịch, Sửa) bao phủ chuỗi lưu lại phần Đối tượng và phiên bản mới được duyệt (`BR-39.5`); người chọn đối tượng được kích hoạt lại thì một người có quyền Phát sóng chiến dịch chọn cho chuỗi nhận người mới trở lại. Người đủ điều kiện vào trong thời gian chờ không được thêm hồi tố.

  Khi doanh nghiệp chọn **"Thu về khách hàng của đơn vị mình"** cho vai trò Marketing và Quản lý Marketing ([`onboarding-srs.md`](./onboarding-srs.md) `FEAT-12`) hoặc thu hẹp ô (Khách hàng, Xem) qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02`, đối tượng của người giữ các vai trò đó thu theo ngay — khi không có nguồn nới khác (công khai đọc, chính sách Cho phép, phụ trách đơn vị); màn hình chọn đối tượng nêu rõ mức Xem đang áp dụng (ví dụ "Chỉ khách hàng thuộc đơn vị của bạn") để người chọn hiểu vì sao tập khớp nhỏ.

  **Lý do nghiệp vụ:** Lượt cấp trên bản ghi và quyền tạm thời được trao để xử lý một việc cụ thể hoặc trong một thời hạn ngắn; tính chúng vào đối tượng biến một lượt chia sẻ để hỗ trợ khách thành giấy phép gửi tiếp thị hàng loạt. Nguồn chặn luôn thắng vì doanh nghiệp đặt chính sách Từ chối hay lượt chặn chính để không ai chạm tới nhóm khách đó, và bản ghi giữ chỗ chưa có người chịu trách nhiệm quan hệ. Nếu chiến dịch gửi được tới khách hàng mà người chọn đối tượng không có quyền xem, chiến dịch trở thành đường vòng để tiếp cận khách hàng của đơn vị khác — và để suy ra số lượng khách hàng của đơn vị khác qua con số xem trước. Lấy phần giao với người khởi chạy vì việc gửi là tiến trình chạy thay người đó: một người không được gửi tới khách hàng mà mình không được xem chỉ vì người khác đã chọn đối tượng. Không gắn đối tượng với người phụ trách vì người phụ trách đổi theo bàn giao và nghỉ phép, không phải người đã đưa ra lựa chọn tiếp thị.

- **`BR-05.5` (Tiêu chí dùng trường thông tin bị hạn chế):** Người soạn chỉ dùng được làm tiêu chí những trường thông tin mà chính họ được phép xem theo cấu hình bảo mật trường (`object-manager-srs.md`). Trường được cấu hình bảo mật trường đánh dấu là **nhạy cảm** (ví dụ sức khỏe, tôn giáo, số định danh) **không** dùng được làm tiêu chí đối tượng chiến dịch, bất kể người soạn có quyền xem, trừ khi có **đồng thời**: (a) đồng ý rõ ràng của **từng người** cho việc dùng đúng loại dữ liệu đó vào mục đích tiếp thị, được kiểm tra tại thời điểm gửi — người không có đồng ý này bị loại khỏi tập khớp tiêu chí; và (b) Người phụ trách Bảo vệ Dữ liệu duyệt cho đúng chiến dịch đó, ghi nhật ký. Toàn bộ tiêu chí — kể cả bộ lọc đã sao từ Danh sách Hiển thị Dùng chung (`BR-05.1`) và điều kiện vào của chuỗi nuôi dưỡng — được đối chiếu lại với **phân loại trường hiện hành** tại lúc duyệt, lúc chốt danh sách và, với chuỗi, mỗi lần xét một người vào chuỗi. Nếu một trường dùng trong tiêu chí đã được đánh dấu nhạy cảm sau khi duyệt mà chưa có (a) và (b): chiến dịch một lần gặp lỗi chặn nhóm (a) (`BR-16.1`); chuỗi bị tự động tạm dừng bảo vệ (`BR-20.1` tình huống 11).

  **Lý do nghiệp vụ:** Việc phân loại một trường là nhạy cảm có hiệu lực ngay với mọi cách dùng trường đó — một chiến dịch đã duyệt hay một chuỗi chạy nhiều tháng không được tiếp tục chọn người theo dữ liệu nhạy cảm chỉ vì đã được duyệt trước khi trường được phân loại. Nếu lọc được theo một trường mình không được xem, người soạn có thể dò giá trị của trường đó cho từng khách hàng bằng cách thử lọc liên tiếp và nhìn con số xem trước thay đổi.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-05.1.1` | Có khách hàng mang thẻ "VIP" ở giai đoạn Khách hàng và khách mang thẻ "VIP" ở giai đoạn Khách hàng tiềm năng | Đặt tiêu chí "thẻ chứa VIP **và** giai đoạn là Khách hàng" | Chỉ nhóm thứ nhất khớp |
| `AC-05.1.2` | Chiến dịch X đã hoàn tất | Đặt tiêu chí "đã nhận chiến dịch X **và** chưa mở" | Chỉ người đã được phân phát chiến dịch X nhưng chưa có sự kiện mở khớp |
| `AC-05.1.3` | K nhận chiến dịch X nhưng không có căn cứ theo dõi hành vi | Đặt tiêu chí "đã nhận chiến dịch X **và** chưa mở" | K không khớp |
| `AC-05.1.4` | Một Danh sách Hiển thị Dùng chung lọc theo trường nhạy cảm "Tình trạng sức khỏe" | Nhân viên Marketing chọn danh sách đó làm tiêu chí | Không chọn được; giao diện nêu danh sách dùng trường nhạy cảm |
| `AC-05.1.5` | Chiến dịch Đã duyệt dùng một danh sách hợp lệ; sau khi duyệt, chủ danh sách thêm điều kiện theo trường nhạy cảm và đổi điều kiện khu vực | Tới lúc chốt danh sách | Chiến dịch vẫn dùng bộ lọc đã sao lúc chọn danh sách; đối tượng không đổi, không có trường nhạy cảm nào được dùng |
| `AC-05.1.6` | Chuỗi Đang chạy dùng bản sao bộ lọc của danh sách "Khách VIP"; chủ danh sách đổi ngưỡng chi tiêu | Hệ thống xử lý | Người phụ trách chuỗi được thông báo; tiêu chí hiển thị "khác với danh sách hiện tại"; chuỗi vẫn chạy theo bộ lọc cũ tới khi phiên bản mới được duyệt |
| `AC-05.1.7` | Danh sách Hiển thị Dùng chung lọc theo trường "Hạn mức tín dụng" mà Nhân viên Marketing N không được xem | N chọn danh sách đó làm tiêu chí | Không chọn được; giao diện nêu danh sách dùng trường N không được xem, không hiển thị giá trị của điều kiện |
| `AC-05.2.1` | Khách hàng H khớp tiêu chí chọn và đang có vé hỗ trợ mở; tập loại trừ là "có vé hỗ trợ mở" | Xem trước quy mô | H bị loại với lý do "Thuộc tập loại trừ của chiến dịch" |
| `AC-05.2.2` | Tập loại trừ "có vé khiếu nại mở" được đánh dấu xét lại tại thời điểm gửi; K mở vé khiếu nại sau khi danh sách đã chốt | Tới lượt K | K Bị loại trừ tại thời điểm gửi — "Thuộc tập loại trừ của chiến dịch" |
| `AC-05.3.1` | Màn hình chọn đối tượng | Tìm chức năng tải tệp danh sách điểm đến | Không có; giao diện hướng dẫn nhập qua phân hệ Khách hàng |
| `AC-05.4.1` | Người chọn đối tượng có (Khách hàng, Xem) = Đơn vị của mình (phòng A); tiêu chí khớp 800 khách phòng A và 500 khách phòng B | Xem trước quy mô | Tổng khớp tiêu chí là 800; không có con số hay gợi ý nào về 500 khách phòng B; màn hình nêu mức Xem đang áp dụng |
| `AC-05.4.2` | Tiếp nối AC-05.4.1; người khởi chạy có (Khách hàng, Xem) = Toàn workspace | Phát sóng, rồi mở mẫu người nhận, sổ cái và báo cáo chiến dịch | Danh sách chốt là 800 khách phòng A; không màn hình nào chứa khách của phòng B |
| `AC-05.4.3` | Người chọn đối tượng có (Khách hàng, Xem) = Toàn workspace, tập khớp 1.300; người khởi chạy có (Khách hàng, Xem) = Đơn vị của mình (phòng A, 800 khách) | Người khởi chạy mở hộp xác nhận phát sóng | Hộp xác nhận hiển thị 800 người nhận khả dụng; sau khi phát sóng, danh sách chốt chỉ gồm khách phòng A |
| `AC-05.4.4` | Người chọn đối tượng có (Khách hàng, Xem) = Toàn workspace, (Khách hàng, Sửa) = Không có | Xem trước quy mô | Tập khớp gồm khách của mọi đơn vị; mức Sửa không làm thu hẹp đối tượng |
| `AC-05.4.5` | Doanh nghiệp đã chọn "Thu về khách hàng của đơn vị mình" cho Marketing và Quản lý Marketing, không có nguồn nới khác (công khai đọc, chính sách Cho phép, phụ trách đơn vị); đội Marketing chỉ phụ trách 40 khách | Nhân viên Marketing đặt tiêu chí khớp 5.000 khách toàn workspace | Tổng khớp tiêu chí là 40; màn hình nêu "Chỉ khách hàng thuộc đơn vị của bạn" |
| `AC-05.4.7` | Người chọn đối tượng có (Khách hàng, Xem) = Toàn workspace; chính sách Từ chối chặn mọi người đọc khách hàng thuộc nhóm "VIP – Ban giám đốc" (120 khách), trừ đơn vị Chăm sóc VIP | Xem trước quy mô và phát sóng | Tổng khớp tiêu chí không gồm 120 khách đó; danh sách chốt không có họ |
| `AC-05.4.8` | Khách hàng K là bản ghi giữ chỗ cho người được mời chưa chấp nhận; người chọn đối tượng là Người có toàn quyền | Xem trước quy mô | K không thuộc tập khớp tiêu chí |
| `AC-05.4.9` | Nhân viên Hỗ trợ H có (Khách hàng, Xem) = Đơn vị của mình và đang xử lý vé mở của khách L thuộc đơn vị khác (quyền đọc tự động); H có quyền tạm thời (Khách hàng, Xem) = Toàn workspace | H chọn đối tượng cho một chiến dịch | Tập khớp chỉ gồm khách thuộc đơn vị của H; L và khách ngoài đơn vị không được tính |
| `AC-05.4.10` | Khách hàng X được chia sẻ trực tiếp cho người chọn đối tượng qua lượt cấp trên bản ghi | Xem trước quy mô | X không thuộc tập khớp nếu nằm ngoài mức theo ô và nguồn nới được tính |
| `AC-05.4.11` | Chiến dịch Đã duyệt hẹn giờ do A chọn đối tượng; A bị tạm ngưng | Tới giờ hẹn | Chiến dịch Giữ lại với lý do "Người chọn đối tượng không còn hoạt động" |
| `AC-05.4.12` | Chuỗi Đang chạy do A chọn đối tượng; A bị tạm ngưng; người phụ trách chuỗi được thông báo | Khách mới đủ điều kiện vào | Không ai vào chuỗi; chuỗi hiển thị "Chờ bàn giao người chọn đối tượng"; người đang trong chuỗi vẫn nhận bước tiếp theo |
| `AC-05.4.13` | Tiếp nối AC-05.4.12; quy trình rời workspace của A bàn giao chuỗi cho B | Hoàn tất bàn giao | B là người chọn đối tượng; chuỗi nhận người mới theo tập xem được của B; khách đủ điều kiện trong thời gian chờ không được thêm hồi tố |
| `AC-05.4.14` | Chuỗi Đang chạy; người chọn đối tượng A lúc bắt đầu chuỗi có (Khách hàng, Xem) = Đơn vị của mình, sau đó được nới thành Toàn workspace | Khách đơn vị khác đủ điều kiện vào | Không vào chuỗi (phần giao với mức lúc bắt đầu) |
| `AC-05.4.15` | Chiến dịch Đang gửi dàn trải; sau lúc chốt danh sách, doanh nghiệp tạo chính sách Cho phép mở cho người khởi chạy xem thêm nhóm khách "Đại lý" | Tới lượt gửi tiếp theo | Danh sách không thêm khách "Đại lý"; nếu chính sách Cho phép đã có từ trước bị gỡ, khách chỉ thấy nhờ chính sách đó bị loại "Ngoài quyền của người khởi chạy" |
| `AC-05.4.16` | Người chọn đối tượng là thành viên kiêm nhiệm của đơn vị tiếp nhận "Kinh doanh"; hàng đợi có 200 khách hàng tiềm năng chưa phân công nằm ngoài mức Xem của họ | Xem trước quy mô | 200 khách đó không được tính |
| `AC-05.4.6` | Chiến dịch Nháp do A (mức Xem Đơn vị của mình) chọn đối tượng, 800 khả dụng; B (mức Xem Toàn workspace) mở phần Đối tượng | B xem trước rồi lưu phần Đối tượng | Khi xem: B thấy con số theo mức Xem của B; sau khi lưu: B là người chọn đối tượng, tập khớp theo mức Xem của B, phiên bản mới cần duyệt |
| `AC-05.5.1` | Trường "Thu nhập" bị ẩn với Nhân viên Marketing | Nhân viên Marketing mở danh sách trường để lọc | Trường "Thu nhập" không có trong danh sách |
| `AC-05.5.2` | Trường "Tình trạng sức khỏe" được đánh dấu nhạy cảm; Quản lý Marketing có quyền xem trường | Tìm trường này trong danh sách tiêu chí | Không có, trừ khi Người phụ trách Bảo vệ Dữ liệu đã duyệt cho chiến dịch; kể cả khi đã duyệt, chỉ những khách có đồng ý rõ ràng cho mục đích này mới khớp tiêu chí |
| `AC-05.5.3` | Chiến dịch được duyệt dùng trường nhạy cảm; K rút đồng ý dùng dữ liệu đó sau khi danh sách đã chốt | Tới lượt K | K Bị loại trừ tại thời điểm gửi — "Không còn đồng ý dùng dữ liệu nhạy cảm" |
| `AC-05.5.4` | Chiến dịch Đã duyệt lọc theo trường "Nhóm bệnh lý quan tâm"; sau khi duyệt, Quản trị viên đánh dấu trường này là nhạy cảm | Tới lúc chốt danh sách | Lỗi chặn nhóm (a); chiến dịch về Nháp, người phụ trách được báo lý do |
| `AC-05.5.5` | Chuỗi Đang chạy có điều kiện vào theo trường "Nhóm bệnh lý quan tâm"; Quản trị viên đánh dấu trường này là nhạy cảm | Người mới đủ điều kiện vào | Không ai vào chuỗi; chuỗi tự động tạm dừng bảo vệ với lý do tiêu chí dùng trường nhạy cảm; Người phụ trách Bảo vệ Dữ liệu được thông báo |

---

#### FEAT-06 — Xem trước Quy mô & Chốt Danh sách Người nhận

**Mô tả nghiệp vụ:** Trước khi gửi phê duyệt, người soạn thấy ngay chiến dịch sẽ tới khoảng bao nhiêu người và vì sao phần còn lại bị loại; khi chiến dịch bắt đầu gửi, danh sách người nhận được chốt cố định.

**Quy tắc nghiệp vụ:**

- **`BR-06.1` (Hai con số bắt buộc):** Màn hình xem trước luôn hiển thị **Tổng khớp tiêu chí** và **Số người nhận khả dụng**, cùng thời điểm tính.

  **Lý do nghiệp vụ:** Chỉ có một con số thì người soạn không biết tiêu chí của mình thực sự nhắm tới bao nhiêu người và bao nhiêu người trong đó bị loại; hai con số cùng thời điểm mới so sánh được với nhau.

- **`BR-06.2` (Phân tích lý do loại trừ cộng khớp):** Hiệu giữa hai con số được tách theo từng lý do loại trừ (`BR-08.2`), mỗi người chỉ tính vào **đúng một** lý do, sao cho: Tổng khớp tiêu chí = Số người nhận khả dụng + tổng các nhóm bị loại. Riêng giới hạn tần suất và giới hạn 24 giờ theo điểm đến được hiển thị là **ước tính tại thời điểm xem**, vì chúng phụ thuộc thời điểm gửi thật; khung giờ yên lặng không phải lý do loại trừ mà chỉ làm hoãn, được hiển thị riêng là số người ước tính bị hoãn. Khi chiến dịch bật kênh thay thế, người sẽ đi kênh thay thế được tính vào số khả dụng và hiển thị riêng một dòng "qua kênh thay thế".

  **Lý do nghiệp vụ:** Con số không cộng khớp khiến người duyệt không tin báo cáo, và che mất tín hiệu quan trọng — ví dụ 30% tệp bị loại vì Không tiếp cận được là dấu hiệu dữ liệu khách hàng đang xuống cấp.

- **`BR-06.3` (Chốt danh sách tại thời điểm bắt đầu gửi):** Danh sách người nhận chốt được xác định **tại thời điểm chiến dịch chuyển sang Đang gửi** (phát ngay hoặc tới giờ hẹn), không phải tại thời điểm phê duyệt. Khách hàng khớp tiêu chí sau thời điểm này không được thêm vào. Khách hàng trong danh sách chốt nhưng sau đó không còn khớp tiêu chí (ví dụ bị gỡ thẻ) vẫn nằm trong danh sách, nhưng vẫn phải qua kiểm tra tại thời điểm gửi (`BR-08.4`).

  **Lý do nghiệp vụ:** Chốt tại thời điểm phê duyệt khiến chiến dịch hẹn gửi một tuần sau bỏ sót toàn bộ khách mới trong tuần đó; không chốt gì cả khiến chiến dịch kéo dài nhiều giờ liên tục "mọc thêm" người nhận, không ai biết khi nào là xong và báo cáo không có mẫu số cố định. Không gỡ người đã chốt khi họ ngừng khớp tiêu chí vì tiêu chí là lựa chọn tiếp thị, còn quyền không nhận tin đã được bảo vệ bằng kiểm tra tại thời điểm gửi.

- **`BR-06.4` (Chênh lệch lớn so với lúc duyệt):** Tại thời điểm phê duyệt, hệ thống lưu **số người nhận khả dụng chỉ xét các lý do loại trừ ổn định giữa lúc duyệt và lúc chốt** (lý do 1–10 tại `BR-08.1`); màn hình duyệt hiển thị cả con số này lẫn con số sau khi trừ ước tính tần suất và giới hạn 24 giờ theo điểm đến. Nếu tại thời điểm chốt, con số tính **theo cùng cơ sở** (lý do 1–10) lệch so với con số đã lưu vượt quá `CFG-CAMP-22`: chiến dịch không bắt đầu gửi mà chuyển **Giữ lại** để xác nhận lại quy mô. **Xác nhận quy mô** là thao tác phát sóng từ Giữ lại với lý do này, cập nhật con số đã lưu thành con số hiện tại, không cần duyệt lại nội dung: nếu số người nhận **giảm**, bất kỳ người có quyền Phát sóng chiến dịch nào xác nhận được; nếu **tăng**, người xác nhận phải thỏa điều kiện phê duyệt kép của `BR-17.2` (khác người soạn phiên bản), vì chi phí và rủi ro đã lớn hơn cái được duyệt; khi `CFG-CAMP-05` tắt, người có quyền Phát sóng chiến dịch tự xác nhận được theo `BR-17.9`, ghi nhật ký và thông báo Người phụ trách Bảo vệ Dữ liệu. Phiên bản duyệt có thể khai báo trước một **mức giảm** và một **mức tăng quy mô chấp nhận được** (tỷ lệ do người soạn đề xuất, người duyệt chấp thuận — người duyệt là người thứ hai theo `BR-17.2`); nếu quy mô thay đổi không quá mức đã khai báo — và với mức tăng, chi phí ước tính theo quy mô mới vẫn trong ngân sách còn lại — chiến dịch bắt đầu gửi mà không cần xác nhận. Mức này là một phần của phiên bản duyệt (`BR-17.4`). Với **phát sóng ngay**, chênh lệch hiển thị trên hộp xác nhận phát sóng kèm hai con số và cùng điều kiện người xác nhận; chiến dịch vẫn Đã duyệt nếu người thao tác không đủ điều kiện xác nhận. Số người nhận khả dụng lúc chốt bằng 0 thì không phát sóng được: chiến dịch hẹn giờ ở Giữ lại tới hết ân hạn `CFG-CAMP-32` rồi về Nháp; phát sóng ngay thì chiến dịch vẫn Đã duyệt. Người phụ trách và người duyệt được thông báo kèm hai con số.

  **Lý do nghiệp vụ:** Giới hạn tần suất và giới hạn 24 giờ theo điểm đến thay đổi liên tục trong ngày theo các chiến dịch khác nên không dùng để so. Tăng quá ngưỡng là đổi chi phí và rủi ro so với cái đã duyệt nên cần người thứ hai xác nhận; nhưng xóa hẳn phê duyệt của một chiến dịch hẹn nửa đêm chỉ vì tệp khách tăng tự nhiên sau vài ngày nhập dữ liệu là làm hỏng vận hành mà không bảo vệ thêm điều gì. Người duyệt đã chấp thuận gửi cho "khoảng 5.000 người". Nếu tới giờ hẹn con số thành 50.000 (do một đợt nhập dữ liệu lớn vừa chạy), đó không còn là chiến dịch họ đã duyệt, cả về chi phí lẫn rủi ro.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-06.1.1` | Tiêu chí khớp 5.200 khách; 120 khách không có email, 30 khách Từ chối nhận tin email | Xem trước | Hiển thị "Khớp tiêu chí: 5.200 · Khả dụng: 5.050"; phân tích "Không có điểm đến: 120 · Từ chối nhận tin: 30" |
| `AC-06.2.1` | Một khách vừa không có email vừa đang Hạn chế xử lý | Xem trước | Khách được tính đúng một lần, vào lý do đứng trước theo thứ tự `BR-08.2` |
| `AC-06.2.2` | Bất kỳ tiêu chí nào | Cộng số khả dụng với các nhóm bị loại | Bằng đúng Tổng khớp tiêu chí |
| `AC-06.3.1` | Chiến dịch hẹn 9:00; khách K được tạo lúc 9:30 và khớp tiêu chí | Xem sổ cái | K không có trong sổ cái |
| `AC-06.3.2` | Chiến dịch Đã duyệt lúc thứ Hai, hẹn gửi thứ Sáu; khách M được tạo thứ Tư và khớp tiêu chí | Tới giờ hẹn | M có trong danh sách chốt |
| `AC-06.4.1` | Lúc duyệt, số khả dụng theo lý do 1–10 là 5.000; tới giờ hẹn, theo cùng cơ sở là 9.000; `CFG-CAMP-22` = 20% | Tới giờ hẹn | Chiến dịch không gửi, chuyển Giữ lại, phê duyệt còn nguyên; thông báo nêu "5.000 lúc duyệt → 9.000 lúc chốt" |
| `AC-06.4.2` | Lúc duyệt, số khả dụng theo lý do 1–10 là 6.500 (sau khi trừ ước tính tần suất là 5.000); lúc chốt theo lý do 1–10 là 6.550 | Tới giờ hẹn | Chiến dịch bắt đầu gửi (chênh lệch tần suất không được tính) |
| `AC-06.4.3` | Lúc duyệt, số khả dụng theo lý do 1–10 là 5.000; lúc chốt còn 3.500 (một đợt hủy nhận tin lớn) | Tới giờ hẹn, rồi người có quyền Phát sóng chiến dịch bấm phát sóng | Tới giờ: chiến dịch Giữ lại, phê duyệt còn nguyên; bấm phát sóng: con số đã lưu cập nhật thành 3.500, chiến dịch bắt đầu gửi, không quay lại Giữ lại |
| `AC-06.4.4` | Tiếp nối AC-06.4.1; người soạn phiên bản có quyền Phát sóng chiến dịch | Người soạn bấm phát sóng | Không cho phép (tăng quy mô cần người khác người soạn); một người có quyền khác xác nhận thì chiến dịch bắt đầu gửi cho 9.000 người |
| `AC-06.4.5` | Phiên bản duyệt khai báo mức giảm chấp nhận 40%; lúc chốt quy mô giảm 30% | Tới giờ hẹn | Chiến dịch bắt đầu gửi, không Giữ lại |
| `AC-06.4.6` | `CFG-CAMP-05` tắt; quy mô tăng quá ngưỡng | Người soạn (có quyền Phát sóng) xác nhận quy mô | Được; nhật ký ghi tự xác nhận; Người phụ trách Bảo vệ Dữ liệu nhận thông báo |
| `AC-06.4.7` | Phiên bản duyệt khai báo mức tăng chấp nhận 30%; lúc duyệt 5.000, lúc chốt 6.100 (+22%), chi phí ước tính vẫn trong ngân sách | Tới giờ hẹn 00:00 | Chiến dịch bắt đầu gửi, không Giữ lại |

---

#### FEAT-07 — Xem Mẫu Người nhận

**Mô tả nghiệp vụ:** Người soạn xem một số khách hàng cụ thể khớp tiêu chí để tự kiểm tra tiêu chí đúng ý đồ.

**Quy tắc nghiệp vụ:**

- **`BR-07.1` (Mẫu có cả người khả dụng và người bị loại):** Màn hình mẫu cho phép xem một số khách hàng giới hạn (`CFG-CAMP-56`) trong nhóm khả dụng và trong **từng** nhóm lý do loại trừ, lấy ngẫu nhiên, có nút lấy mẫu khác — **trừ** nhóm lý do 1 (Hạn chế xử lý, biện pháp phòng ngừa): nhóm này chỉ hiển thị số đếm tổng hợp, không có mẫu từng người, không xuất hiện trong tệp xuất, theo (`contacts-srs.md`, `BR-30.6`).

  **Lý do nghiệp vụ:** Mẫu chỉ gồm người khả dụng không giúp phát hiện tiêu chí sai theo chiều ngược lại — ví dụ cả nhóm khách VIP bị loại vì hồ sơ chưa có email. Lấy ngẫu nhiên thay vì "20 người đầu tiên" để mẫu không luôn là những hồ sơ cũ nhất.

- **`BR-07.2` (Mẫu tuân theo quyền xem):** Thông tin hiển thị trong mẫu chịu đúng quy tắc che dữ liệu và bảo mật trường của người đang xem như khi họ mở hồ sơ khách hàng.

  **Lý do nghiệp vụ:** Màn hình mẫu không được trở thành đường xem thông tin khách hàng mà chính hồ sơ đã che.

- **`BR-07.3` (Khai báo trường nhạy cảm của phân hệ):** Theo khung che dữ liệu của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-40`, phân hệ khai báo: (a) loại dữ liệu Chiến dịch **không có trường nhạy cảm của hệ thống riêng**; (b) điểm đến (địa chỉ email, số điện thoại, định danh người quan tâm Zalo OA) và mọi thông tin hồ sơ khách hàng hiển thị trong mẫu người nhận, sổ cái, tệp xuất sổ cái, danh sách tin gắn nhãn và báo cáo là trường của Khách hàng, che theo đúng mẫu che và quyền xem đầy đủ của `contacts-srs.md` `FEAT-04`, hoặc theo mẫu che mặc định của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-40` khi phân hệ Khách hàng không khai báo mẫu riêng; (c) giá trị cơ hội và doanh thu theo `BR-34.4`; (d) tác nhân AI luôn nhận giá trị đã che ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-40.1`); (e) ngân sách chiến dịch, đơn giá kênh, chi phí ước tính và chi phí đã cam kết **không** là trường nhạy cảm và không bị che: ai xem được chiến dịch theo ô (Chiến dịch, Xem) đều thấy, vì đây là số liệu vận hành nội bộ không chứa dữ liệu khách hàng, và người soạn, người duyệt cần thấy chúng để kiểm soát chi phí trước khi phát sóng (`BR-17.3`, `BR-41.2`); doanh nghiệp muốn hạn chế thì thu hẹp ô Xem trên Chiến dịch hoặc dùng phân quyền trường. Che chỉ áp khi hiển thị: tiến trình gửi dùng điểm đến đầy đủ để gửi, không phụ thuộc quyền xem đầy đủ của người khởi chạy.

  **Lý do nghiệp vụ:** Khai báo rõ để không phát sinh một lớp che thứ hai khác với hồ sơ khách hàng cho cùng một số điện thoại; tách che khỏi việc gửi để người không được xem đầy đủ số điện thoại vẫn phát sóng được chiến dịch SMS mà không thấy số.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-07.1.1` | Tiêu chí có 3 nhóm lý do loại trừ; `CFG-CAMP-56` = 20 | Mở mẫu | Có danh sách mẫu tối đa 20 người cho nhóm khả dụng và cho từng nhóm trong 3 nhóm loại trừ |
| `AC-07.1.2` | Đang xem mẫu của một nhóm có 500 người | Bấm lấy mẫu khác | Danh sách mới khác danh sách trước ít nhất một người |
| `AC-07.2.1` | Người xem bị che số điện thoại theo cấu hình bảo mật trường | Mở mẫu của chiến dịch SMS | Số điện thoại hiển thị ở dạng đã che |
| `AC-07.3.1` | Người xem không có quyền xem đầy đủ email của Khách hàng | Mở sổ cái chiến dịch Email và xuất sổ cái | Email hiển thị giữ ký tự đầu và tên miền, ở cả màn hình và tệp xuất |
| `AC-07.3.2` | Người khởi chạy không có quyền xem đầy đủ số điện thoại | Phát sóng chiến dịch SMS | Tin được gửi tới đúng số đầy đủ; mọi màn hình của người khởi chạy vẫn hiển thị số đã che |
| `AC-07.3.4` | Nhân viên Marketing có (Chiến dịch, Xem) = Đơn vị của mình mở chiến dịch của đồng nghiệp | Xem phần ngân sách và chi phí | Thấy ngân sách, chi phí ước tính và đã cam kết ở dạng đầy đủ |
| `AC-07.3.3` | Trợ lý AI được hỏi danh sách người nhận của một chiến dịch | Trợ lý trả lời | Mọi điểm đến ở dạng đã che, bất kể quyền của người hỏi |

---

#### FEAT-08 — Loại trừ, Khử trùng lặp & Giới hạn Tần suất

**Mô tả nghiệp vụ:** Hệ thống tự động loại khỏi chiến dịch những người không được phép hoặc không nên nhận tin, và bảo đảm mỗi người chỉ nhận một tin.

**Quy tắc nghiệp vụ:**

- **`BR-08.1` (Danh mục lý do loại trừ):** Một người khớp tiêu chí bị loại nếu rơi vào bất kỳ lý do nào sau đây:
  1. **Đang Hạn chế xử lý** hoặc thuộc biện pháp phòng ngừa của yêu cầu chủ thể dữ liệu (`contacts-srs.md`, `BR-30.6`, `BR-33.7`).
  2. **Giai đoạn vòng đời không được nhận tiếp thị:** Bị loại (Disqualified); Đã rời bỏ (Churned) — trừ khi chiến dịch là Chiến dịch Tái tiếp cận có hiệu lực cho người đó (`FEAT-31`); khách về Nurturing do một tương tác sau đó bị nhận diện là tự động và đang chờ người phụ trách quyết định (`BR-14.3`); hồ sơ khách hàng tạm.
  3. **Không có điểm đến** cho kênh chính (không có email; không có số điện thoại; với Zalo OA — chưa quan tâm Tài khoản Chính thức của doanh nghiệp).
  4. **Điểm đến Không tiếp cận được hoặc Không còn hiệu lực** (`contacts-srs.md`, `BR-29.2`).
  5. **Từ chối nhận tin** trên kênh chính, hoặc đã chọn "Hủy nhận tin trên toàn bộ mọi kênh" (`contacts-srs.md`, `BR-30.1`), hoặc điểm đến khớp dấu vết chặn gửi (`BR-32.4`), hoặc điểm đến đang **tạm chặn chờ xác nhận lời từ chối** (`BR-26.3`, ghi kèm chú thích này).
  6. **Chưa có Đồng ý nhận tin có bằng chứng** trên kênh chính, với kênh doanh nghiệp yêu cầu tại `CFG-CAMP-74` và mọi kênh ngoài năm kênh của phân hệ này (luôn yêu cầu, theo `contacts-srs.md` `BR-30.1`) (Mục 1.6, giả định 6). Lý do này bao gồm cả các Đồng ý **không được coi là căn cứ**: (i) Đồng ý tự khôi phục khi dỡ biện pháp phòng ngừa vì hết hạn, chưa được chính chủ xác nhận lại (`BR-32.1`); (ii) khi `CFG-CAMP-72` bật, Đồng ý thu qua biểu mẫu chưa được xác nhận qua chính điểm đến (`BR-09.3`). Phân tích loại trừ ghi chú riêng từng loại (ví dụ "Đồng ý tự khôi phục, cần chính chủ xác nhận lại") để người phụ trách biết gửi yêu cầu xác nhận lại. Định nghĩa này áp cho mọi đường gửi, kể cả qua cổng dùng chung (`FEAT-44`).
  7. **Thuộc Danh sách không quảng cáo** (`BR-30.5`).
  8. **Thuộc tập loại trừ của chiến dịch** (`BR-05.2`).
  9. **Trùng điểm đến** với một người khác trong cùng chiến dịch (`BR-08.3`).
  10. **Thiếu dữ liệu cá nhân hóa bắt buộc** (`BR-12.2`).
  11. **Nội dung chưa được nhà mạng của điểm đến duyệt, hoặc ngoài hiệu lực duyệt** (`BR-13.3`, `BR-13.4`).
  12. **Chạm giới hạn tần suất** (`BR-08.5`).
  13. **Chạm giới hạn 24 giờ theo điểm đến** (`BR-30.3`).
  14. **Quá hạn chót gửi** của chiến dịch (`BR-18.4`).
  15. **Không còn đồng ý dùng dữ liệu nhạy cảm** cho mục đích tiếp thị, với chiến dịch có tiêu chí dựa trên trường nhạy cảm (`BR-05.5`).
  16. **Chạm giới hạn Thông báo dịch vụ** (`BR-45.5`) — chỉ áp cho Thông báo dịch vụ.
  17. **Từ chối Thông báo dịch vụ không thiết yếu** (`BR-45.8`) — chỉ áp cho Thông báo dịch vụ mục đích (1), (3), (5).
  18. **Ngoài quyền của người khởi chạy** — khách hàng không còn nằm trong mức Xem trên Khách hàng của người khởi chạy tính theo `BR-17.10` (ví dụ người khởi chạy bị thu hẹp quyền giữa lúc chiến dịch đang gửi). Chỉ phát sinh tại thời điểm gửi; lúc chốt danh sách, khách hàng ngoài quyền không thuộc tập khớp tiêu chí (`BR-05.4`).

  **Lý do nghiệp vụ:** Lý do 6 là điều kiện "phải có đồng ý trước" mà doanh nghiệp bật cho từng kênh: chỉ chặn người đã từ chối là chưa đủ, vì phần lớn hồ sơ tạo tay hoặc tạo từ hội thoại chưa bao giờ đồng ý nhận tiếp thị. Lý do 18 bảo đảm thu hẹp quyền có hiệu lực ngay từ tin kế tiếp, kể cả với chiến dịch đang gửi dở.

- **`BR-08.2` (Thứ tự ưu tiên khi nhiều lý do cùng đúng):** Khi một người rơi vào nhiều lý do, lý do ghi nhận là lý do có **số thứ tự nhỏ nhất** trong danh mục `BR-08.1`. Ngoại lệ duy nhất: khi lý do 18 đúng, dòng đó chỉ ghi lý do 18 và các lý do khác không được xét, vì tiến trình gửi không còn quyền xem hồ sơ đó. Thứ tự này cố định, không cấu hình.

  **Lý do nghiệp vụ:** Thứ tự đi từ các lý do về quyền của chủ thể dữ liệu và vòng đời (hạn chế xử lý, người đã rời bỏ), qua các lý do khiến việc gửi không thể thực hiện (không có điểm đến, điểm đến hỏng), tới các lý do về đồng thuận và chính sách gửi tiếp thị, rồi tới lý do thuần vận hành (tần suất). Không có điểm đến đứng trước Từ chối nhận tin vì khi không có điểm đến thì không tồn tại đồng thuận trên điểm đến đó để xét. Ghi lý do nghiêm trọng nhất giúp báo cáo tuân thủ phản ánh đúng rủi ro thật, và giữ cho phân tích loại trừ (`BR-06.2`) cộng khớp.

- **`BR-08.3` (Khử trùng lặp theo người và theo điểm đến):** Mỗi hồ sơ khách hàng xuất hiện tối đa một lần trong danh sách chốt, dù khớp tiêu chí qua nhiều điều kiện; các hồ sơ đã được gộp vào cùng một Bản ghi Chính được coi là một người (`BR-08.4`). Thêm vào đó, mỗi **điểm đến** chỉ nhận tối đa một tin cho một lượt gửi, kể cả khi nhiều hồ sơ dùng chung điểm đến đó (một email chung của gia đình, một số tổng đài doanh nghiệp). Với các hồ sơ dùng chung một điểm đến, cách xử lý theo `CFG-CAMP-23`:
  - **Mặc định — Gửi một lần, chặt nhất thắng:** nếu **bất kỳ** hồ sơ nào dùng điểm đến đó rơi vào lý do 1, 2, 4, 5, 6 hoặc 7 (với Thông báo dịch vụ: lý do 1 theo mục đích, 4 và 17; lý do 2 chỉ xét trên hồ sơ được chọn, `BR-45.4`), thì điểm đến đó không nhận tin; ngược lại, gửi một tin duy nhất, nội dung cá nhân hóa dùng **giá trị thay thế** thay vì thông tin của một hồ sơ cụ thể.
  - **Tùy chọn — Loại hẳn điểm đến dùng chung:** điểm đến mang nhãn Định danh dùng chung (`contacts-srs.md`, `BR-30.2`) không nhận tin tiếp thị. Tùy chọn này không áp cho Thông báo dịch vụ (`FEAT-45`): điểm đến dùng chung nhận thông báo theo cách mặc định "gửi một lần, chặt nhất thắng" với tập lý do của `BR-45.4`.

  Các hồ sơ không được chọn gửi được ghi lý do "Trùng điểm đến". Khi cả điểm đến bị chặn vì lý do của một hồ sơ dùng chung, hồ sơ đó ghi đúng lý do của mình, còn các hồ sơ khác cùng điểm đến ghi lý do đó kèm chú thích "từ hồ sơ dùng chung điểm đến" — tại thời điểm chốt và tại thời điểm gửi như nhau.

  **Lý do nghiệp vụ:** Hai hồ sơ cùng một số điện thoại nghĩa là cùng một chiếc điện thoại nhận hai tin giống hệt nhau — trải nghiệm tệ và tốn chi phí gấp đôi. "Chặt nhất thắng" vì người đang cầm điện thoại có thể chính là người đã từ chối. Dùng giá trị thay thế vì không biết ai trong số các hồ sơ đang đọc tin — gọi sai tên người khác còn tệ hơn lời chào chung.

- **`BR-08.4` (Kiểm tra lại tại thời điểm gửi):** Ngay trước khi gửi tin tới từng người trong danh sách chốt, hệ thống kiểm tra lại các lý do 1–7, 10–18 và lý do 8 với các tập loại trừ được đánh dấu "xét lại tại thời điểm gửi" (`BR-05.2`), theo dữ liệu **tại đúng thời điểm đó**, với hai quy tắc bổ sung:
  - (a) Kiểm tra xét **mọi hồ sơ đang giữ điểm đến** tại thời điểm gửi, không chỉ hồ sơ được chọn khi chốt danh sách — áp nguyên tắc chặt nhất thắng của `BR-08.3`.
  - (b) Nếu hồ sơ trong danh sách chốt đã bị gộp vào hồ sơ khác, kiểm tra dựa trên **Bản ghi Chính hiện hành** (đồng thuận của Bản ghi Chính theo `contacts-srs.md`, `BR-19.6`); nếu Bản ghi Chính đã được gửi hoặc đang có dòng khác trong cùng danh sách chốt, dòng này ghi "Trùng điểm đến" và không gửi. Khi một lần gộp được hoàn tác theo phân hệ Khách hàng, dòng sổ cái trở về gắn với hồ sơ gốc, và đồng thuận của cả hai hồ sơ sau khi tách tuân theo đúng (`contacts-srs.md`, `BR-20.2`).

  Người rơi vào một lý do tại thời điểm này được ghi trạng thái **Bị loại trừ tại thời điểm gửi** kèm lý do. Quy tắc này áp dụng cho mọi lượt gửi: lượt gửi chính, dự phòng, gửi lại, bước của chuỗi nuôi dưỡng, phiên bản thắng của thử nghiệm A/B.

  **Lý do nghiệp vụ:** Đây là cách duy nhất bảo đảm `KPI-01` bằng 0. Hoàn tác gộp không được làm "sống lại" một Đồng ý mà khách đã rút trong thời gian hai hồ sơ là một. Một chiến dịch 200.000 người có thể gửi trong nhiều giờ, một chiến dịch dàn trải có thể kéo dài nhiều ngày; trong khoảng đó luôn có người hủy nhận tin, hồ sơ bị gộp, hay người cầm chiếc điện thoại dùng chung nhắn từ chối — và họ có quyền được tôn trọng ngay.

- **`BR-08.5` (Giới hạn tần suất):** Một người không nhận quá `CFG-CAMP-02` tin tiếp thị trong khoảng thời gian trượt `CFG-CAMP-03` tính ngược từ thời điểm gửi. Phạm vi đếm theo `CFG-CAMP-04` (**theo từng kênh** hoặc **gộp mọi kênh**). Cách đếm:
  - Đếm mọi tin tiếp thị tới người đó từ mọi chiến dịch và mọi chuỗi nuôi dưỡng đang ở trạng thái **Đã phân phát**, **Đã chuyển nhà cung cấp** hoặc **Chưa xác định**, **cộng cả những tin đang được gửi dở** của chiến dịch khác tại cùng thời điểm, để hai chiến dịch chạy song song không cùng lọt qua giới hạn. Tin Thất bại vĩnh viễn không được đếm.
  - Một thông điệp được gửi qua kênh dự phòng thay cho kênh chính tính là **một** tin, cho kênh thực sự đã phân phát.
  - Gửi thử (`FEAT-15`) không tính.
  - Một lượt gửi lại (`FEAT-25`) cho cùng dòng sổ cái **thay thế** tin gốc trong phép đếm tần suất, không cộng thêm.
  - Khi hai chiến dịch cùng cạnh tranh suất cuối cùng của một người, chiến dịch **gửi tới người đó trước** được dùng suất; chiến dịch kia ghi người đó là "Chạm giới hạn tần suất".
  - Với chiến dịch gửi một lần, người chạm giới hạn bị loại; với chuỗi nuôi dưỡng, bước chạm giới hạn được hoãn theo `BR-39.4`.
  - Nếu phiên bản điều khoản đồng thuận mà người nhận đã đồng ý có nêu tần suất nhận tin (ví dụ "tối đa 1 tin mỗi tuần"), giới hạn áp cho người đó là **mức chặt hơn** giữa tần suất trong điều khoản và `CFG-CAMP-02`/`CFG-CAMP-03`.

  **Lý do nghiệp vụ:** Giới hạn tần suất chỉ có nghĩa khi tính trên toàn doanh nghiệp — mỗi nhóm tiếp thị tự giới hạn riêng thì khách hàng vẫn nhận 6 tin một tuần từ 3 nhóm. Đếm cả tin đang gửi dở vì nếu chỉ đếm tin đã gửi xong, hai chiến dịch bắt đầu cùng lúc sẽ cùng thấy người nhận "chưa nhận tin nào". Không đếm tin thất bại vĩnh viễn vì người nhận chưa hề bị làm phiền.

- **`BR-08.6` (Miễn trừ giới hạn tần suất có kiểm soát):** Người soạn đề xuất miễn trừ giới hạn tần suất cho chiến dịch kèm lý do bắt buộc; miễn trừ là một phần của phiên bản gửi phê duyệt và chỉ có hiệu lực khi phiên bản đó được một người có quyền Phát sóng chiến dịch duyệt theo `BR-17.2`. Chiến dịch miễn trừ vẫn được **đếm** vào tần suất của các chiến dịch khác. Miễn trừ chỉ bỏ qua giới hạn chung `CFG-CAMP-02`/`CFG-CAMP-03`; **không** áp dụng cho tần suất nêu trong điều khoản đồng thuận mà người nhận đã đồng ý (`BR-08.5` — người đó vẫn bị loại theo lý do 12), giới hạn 24 giờ theo điểm đến (`BR-30.3`), khung giờ yên lặng hay bất kỳ lý do loại trừ nào khác. Mọi miễn trừ được ghi nhật ký và thống kê trong báo cáo tuân thủ.

  **Lý do nghiệp vụ:** Có thông điệp tiếp thị quan trọng hơn giới hạn thông thường (ví dụ thông báo thu hồi một chương trình khuyến mãi bị lỗi giá). Không có đường miễn trừ chính thức, người dùng sẽ tạm nâng tham số cho toàn doanh nghiệp rồi quên hạ lại. Miễn trừ phải qua phê duyệt kép như mọi phần khác của phiên bản, để người duyệt không tự miễn trừ cho chính quyết định của mình. Tần suất ghi trong điều khoản là cam kết với chính người nhận — phạm vi mà họ đã đồng ý — nên doanh nghiệp không tự miễn trừ được.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-08.1.1` | Khách ở giai đoạn Đã rời bỏ khớp tiêu chí chiến dịch thường | Xem trước | Bị loại, lý do "Giai đoạn vòng đời không được nhận tiếp thị" |
| `AC-08.1.2` | Khách có email ở trạng thái Không tiếp cận được | Xem trước chiến dịch Email | Bị loại, lý do "Điểm đến Không tiếp cận được" |
| `AC-08.1.3` | Khách Từ chối nhận tin email nhưng Đồng ý nhận tin SMS | Xem trước chiến dịch SMS | Khách khả dụng |
| `AC-08.1.4` | Khách Đang Hạn chế xử lý | Xem trước bất kỳ chiến dịch nào và mở mẫu | Bị loại, lý do "Đang Hạn chế xử lý"; không xuất hiện trong bất kỳ mẫu nào — nhóm lý do này chỉ có số đếm |
| `AC-08.1.5` | `CFG-CAMP-74` mặc định; khách K được nhân viên tạo tay từ danh thiếp, kênh SMS chưa từng có Đồng ý có bằng chứng | Xem trước chiến dịch SMS | K bị loại "Chưa có Đồng ý nhận tin có bằng chứng" |
| `AC-08.1.6` | Chiến dịch Zalo OA; khách L có số điện thoại nhưng chưa quan tâm Tài khoản Chính thức | Xem trước | L bị loại "Không có điểm đến" |
| `AC-08.2.1` | Khách K Đang Hạn chế xử lý và đồng thời Từ chối nhận tin email | Xem trước chiến dịch Email | K được tính đúng một lần với lý do "Đang Hạn chế xử lý" (thứ tự 1), không tính vào "Từ chối nhận tin" |
| `AC-08.3.1` | Hai hồ sơ H1 (Đồng ý) và H2 (Từ chối nhận tin SMS) cùng một số điện thoại; `CFG-CAMP-23` mặc định | Gửi chiến dịch SMS | Số đó không nhận tin; H2 ghi "Từ chối nhận tin", H1 ghi "Từ chối nhận tin — từ hồ sơ dùng chung điểm đến" |
| `AC-08.3.2` | Hai hồ sơ H1, H2 cùng Đồng ý, cùng một email | Gửi chiến dịch Email có lời chào "Chào {tên}" | Email đó nhận đúng một thư, lời chào dùng giá trị thay thế ("Chào Quý khách") |
| `AC-08.3.3` | Khách K khớp tiêu chí qua hai nhóm điều kiện "hoặc" | Chốt danh sách | K xuất hiện một lần |
| `AC-08.4.1` | Chiến dịch 50.000 người bắt đầu 9:00; khách K ở vị trí gửi lúc khoảng 9:40; K hủy nhận tin email lúc 9:10 từ một thư cũ | Chiến dịch gửi tới vị trí của K | K không nhận thư; sổ cái ghi "Bị loại trừ tại thời điểm gửi — Từ chối nhận tin" |
| `AC-08.4.2` | H1 được chọn gửi cho số điện thoại dùng chung với H2; sau khi chốt danh sách, người cầm máy nhắn từ khóa từ chối và lời từ chối được ghi cho H2 | Tới lượt gửi của H1 | Số đó không nhận tin; dòng H1 ghi "Bị loại trừ tại thời điểm gửi — Từ chối nhận tin — từ hồ sơ dùng chung điểm đến" |
| `AC-08.4.3` | Hồ sơ B (Đồng ý) trong danh sách chốt được gộp vào Bản ghi Chính A (Từ chối) trước lượt gửi của B | Tới lượt gửi của B | Không gửi; lý do "Từ chối nhận tin" theo đồng thuận của A |
| `AC-08.4.4` | A và B cùng trong danh sách chốt với hai email khác nhau, sau đó B được gộp vào A; A đã được gửi | Tới lượt gửi của B | Không gửi; dòng B ghi "Trùng điểm đến" |
| `AC-08.4.5` | Hồ sơ B (Đồng ý SMS) được gộp vào A; khách nhắn "TC", lời từ chối ghi trên A; 45 ngày sau lần gộp được hoàn tác | Chiến dịch SMS gửi tới B | B bị loại "Từ chối nhận tin" |
| `AC-08.4.6` | Sau khi chốt danh sách, nhân viên CSKH gỡ số điện thoại khỏi hồ sơ K vì số thuộc người khác | Tới lượt gửi SMS của K | Không gửi; K Bị loại trừ tại thời điểm gửi — "Không có điểm đến" |
| `AC-08.4.7` | Chiến dịch Đang gửi do G khởi chạy với (Khách hàng, Xem) = Toàn workspace; G bị thu hẹp về Đơn vị của mình | Tới lượt một khách thuộc đơn vị khác | Không gửi; dòng ghi Bị loại trừ tại thời điểm gửi — "Ngoài quyền của người khởi chạy"; khách trong đơn vị của G vẫn được gửi |
| `AC-08.2.2` | Tiếp nối AC-08.4.7; khách đó đồng thời đã Từ chối nhận tin | Xem sổ cái | Dòng chỉ ghi "Ngoài quyền của người khởi chạy" |
| `AC-08.5.1` | Các AC của `BR-08.5` giả định `CFG-CAMP-69` tắt, để tách khỏi giới hạn 24 giờ theo điểm đến. `CFG-CAMP-02` = 2, `CFG-CAMP-03` = 7 ngày, theo từng kênh; K đã nhận 2 email tiếp thị lúc 10:00 ngày 1 và ngày 4; hôm nay ngày 6 | Chiến dịch Email mới gửi tới K | K bị loại "Chạm giới hạn tần suất" |
| `AC-08.5.2` | Tiếp nối AC-08.5.1 | Chiến dịch SMS gửi tới K cùng ngày | K nhận SMS (đếm theo từng kênh) |
| `AC-08.5.3` | Như AC-08.5.1 nhưng `CFG-CAMP-04` = gộp mọi kênh | Chiến dịch SMS gửi tới K | K bị loại "Chạm giới hạn tần suất" |
| `AC-08.5.4` | K đã nhận 1 email tiếp thị 3 ngày trước; chiến dịch Email A và B cùng bắt đầu, cùng có K; giới hạn 2 tin / 7 ngày | Cả hai chạy | Đúng một chiến dịch gửi được cho K; chiến dịch còn lại ghi K "Chạm giới hạn tần suất" |
| `AC-08.5.5` | K nhận tin thứ nhất lúc 10:00 ngày 1 và tin thứ hai lúc 10:00 ngày 3; giới hạn 2 tin / 7 ngày | Chiến dịch mới gửi tới K lúc 09:59 ngày 8 và lúc 10:01 ngày 8 | Lúc 09:59 ngày 8 bị loại; lúc 10:01 ngày 8 được gửi (khoảng trượt tuyệt đối, dùng dịch chuyển đồng hồ `NFR-11`) |
| `AC-08.5.6` | K có 1 tin email Thất bại vĩnh viễn và 1 tin Đã phân phát trong 7 ngày; giới hạn 2 | Chiến dịch Email mới gửi tới K | K được gửi (tin thất bại vĩnh viễn không được đếm) |
| `AC-08.5.7` | `CFG-CAMP-02` = 2 tin / 7 ngày; điều khoản K đã đồng ý ghi "tối đa 1 tin mỗi tuần"; K đã nhận 1 tin 3 ngày trước | Chiến dịch mới gửi tới K | K bị loại "Chạm giới hạn tần suất" |
| `AC-08.5.8` | Giới hạn 2 tin / 7 ngày; K đã nhận 1 tin khác và có 1 tin Chưa xác định của chiến dịch C | Gửi lại thủ công tin của C cho K | K được gửi lại; tần suất của K vẫn tính là 2 |
| `AC-08.6.1` | Người soạn đề xuất miễn trừ tần suất kèm lý do; M2 duyệt phiên bản; K đã nhận 2 tin trong tuần | Gửi | K nhận tin; báo cáo tuân thủ ghi một lượt miễn trừ kèm lý do, người đề xuất và người duyệt |
| `AC-08.6.2` | Tiếp nối AC-08.6.1; một chiến dịch thường khác gửi tới K ngày hôm sau | Gửi | K bị loại "Chạm giới hạn tần suất" (tin miễn trừ vẫn được đếm) |
| `AC-08.6.3` | Chiến dịch Đã duyệt | Người có quyền Phát sóng chiến dịch bật miễn trừ tần suất | Chiến dịch về Nháp; miễn trừ chỉ hiệu lực khi phiên bản mới được một người khác duyệt |
| `AC-08.6.4` | Chiến dịch được duyệt miễn trừ tần suất; K đã đồng ý theo điều khoản nêu "tối đa 1 tin mỗi tuần" và đã nhận 1 tin tuần này | Chốt danh sách | K bị loại với lý do "Chạm giới hạn tần suất"; người không có tần suất trong điều khoản được miễn giới hạn chung |

---

### Nhóm C — Kênh & Tài khoản Gửi

#### FEAT-09 — Năm Kênh Phát sóng

**Mô tả nghiệp vụ:** Chiến dịch gửi qua một kênh chính trong năm kênh: **Email**, **WhatsApp**, **Zalo ZNS** (tin thông báo gửi theo số điện thoại qua mẫu), **Zalo OA** (tin truyền thông của Tài khoản Chính thức, chỉ tới người đang quan tâm tài khoản đó), **SMS** (tin nhắn thương hiệu). Zalo ZNS và Zalo OA là hai kênh riêng vì khác nhau về cách xác định người nhận, loại nội dung nền tảng cho phép, hạn mức và cách tính phí. Các quy tắc trong tài liệu viện dẫn **thuộc tính của kênh** thay vì tên kênh, để thêm kênh mới không phải viết lại quy tắc.

**Quy tắc nghiệp vụ:**

- **`BR-09.1` (Thuộc tính kênh):** Mỗi kênh được mô tả bằng các thuộc tính nghiệp vụ sau; mọi quy tắc liên quan dựa vào thuộc tính chứ không dựa vào tên kênh:

  | Thuộc tính | Email | WhatsApp | Zalo ZNS | Zalo OA | SMS |
  | --- | :---: | :---: | :---: | :---: | :---: |
  | Điểm đến | Địa chỉ email | Số điện thoại | Số điện thoại | Người quan tâm Tài khoản Chính thức | Số điện thoại |
  | Kênh đồng thuận tương ứng (`contacts-srs.md`, `BR-30.1`) | Email | WhatsApp | Zalo ZNS | Zalo OA | SMS |
  | Bắt buộc dùng mẫu đã được nền tảng duyệt (`FEAT-13`) | Không | Có | Có | Không | Không |
  | Nội dung phải được nhà mạng hoặc nền tảng duyệt trước khi gửi (`BR-13.3`, `BR-13.5`) | Không | Có *(mẫu)* | Có *(mẫu)* | Theo chính sách nền tảng | Có |
  | Nền tảng giới hạn loại nội dung dùng cho tiếp thị (`BR-13.1`) | Không | Có | Có | Có | Không |
  | Nền tảng áp hạn mức hoặc điểm chất lượng riêng lên tài khoản gửi (`BR-22.2`) | Theo nhà cung cấp | Có | Có | Có | Theo nhà mạng |
  | Đo được sự kiện mở | Có *(gần đúng, `BR-33.3`)* | Có *(đã đọc)* | Tùy mẫu | Có *(đã đọc)* | Không |
  | Đo được sự kiện nhấp liên kết | Có | Có | Có | Có | Có *(qua liên kết rút gọn)* |
  | Người nhận trả lời được | Có | Có | Tùy mẫu | Có | Chỉ qua đầu số hai chiều (`BR-26.1`) |
  | Chi phí tính theo | Tin chuyển đi | Tin phân phát, theo loại mẫu | Tin phân phát, theo loại mẫu | Theo nền tảng | Đơn vị tin chuyển đi, theo nhà mạng (`BR-11.3`) |

  Quy tắc riêng về cửa sổ phản hồi và tin nhắn mẫu của từng nền tảng nhắn tin theo `omnichat-srs.md` (`BR-01.8`, `BR-12.2`). Cột "Chi phí tính theo" là căn cứ ước tính và đối soát chi phí (`FEAT-41`); giá trị cụ thể do Quản trị viên khai báo theo hợp đồng với từng nhà cung cấp.

  **Lý do nghiệp vụ:** Mô tả kênh bằng thuộc tính buộc mọi quy tắc phải nói rõ vì sao nó áp cho kênh này mà không áp cho kênh kia; khi nền tảng đổi chính sách (ví dụ cho phép một loại mẫu mới), chỉ cần cập nhật thuộc tính.

- **`BR-09.2` (Một chiến dịch — một kênh chính):** Mỗi chiến dịch có đúng một kênh chính. Gửi cùng thông điệp qua nhiều kênh cùng lúc cho cùng một người không được hỗ trợ; nhu cầu "tới được người nhận bằng kênh khác khi kênh chính không tới" dùng Dự phòng kênh (`FEAT-37`).

  **Lý do nghiệp vụ:** Gửi song song nhiều kênh làm một người nhận cùng một thông điệp hai, ba lần — đúng vấn đề làm phiền mà giới hạn tần suất tồn tại để ngăn.

- **`BR-09.3` (Thu đồng thuận trên kênh ứng dụng):** Đồng ý nhận tin tiếp thị qua Zalo OA, Zalo ZNS và WhatsApp được thu ngay trên nền tảng qua một thao tác xác nhận chủ động của người nhận (nút hoặc biểu mẫu đồng ý trong Tài khoản Chính thức, trong tin nhắn), hoặc — với kênh gửi theo số điện thoại (Zalo ZNS, WhatsApp) — qua biểu mẫu của doanh nghiệp nêu rõ tên kênh sẽ nhận tin. Với mọi Đồng ý thu qua biểu mẫu (cho bất kỳ kênh nào), phân hệ Chiến dịch chỉ coi là **có bằng chứng** khi người điền đã **xác nhận qua mã hoặc liên kết gửi tới chính điểm đến đó** (khi `CFG-CAMP-72` bật — mặc định bật); Đồng ý chưa qua bước này xếp lý do 6, và được ghi bằng chứng theo (`contacts-srs.md`, `BR-30.3`) với nguồn tương ứng trong A.8. **Chỉ việc quan tâm** Tài khoản Chính thức hay nhắn tin cho doanh nghiệp không phải là Đồng ý nhận tiếp thị.

  **Lý do nghiệp vụ:** Không có đường thu đồng thuận ngay trên kênh, phần lớn người quan tâm Tài khoản Chính thức sẽ mãi ở trạng thái Chưa có đồng thuận và kênh Zalo OA gần như không dùng được; nhưng coi việc quan tâm là đồng ý là tự tạo căn cứ không có thật.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-09.1.1` | Chiến dịch SMS đã hoàn tất | Mở báo cáo | Chỉ số Tỷ lệ mở hiển thị "Không áp dụng cho kênh này", không hiển thị 0% |
| `AC-09.1.2` | Chiến dịch WhatsApp | Soạn nội dung tự do không dùng mẫu | Không cho phép; chỉ chọn được tin nhắn mẫu đã phê duyệt |
| `AC-09.2.1` | Màn hình tạo chiến dịch | Chọn hai kênh chính | Không cho phép; chỉ chọn được một kênh chính |
| `AC-09.3.1` | Người dùng Zalo bấm nút "Đồng ý nhận ưu đãi" trong Tài khoản Chính thức của doanh nghiệp | Hệ thống xử lý | Kênh Zalo OA của hồ sơ chuyển Đồng ý nhận tin, có bằng chứng với nguồn đồng ý trên Tài khoản Chính thức |
| `AC-09.3.2` | `CFG-CAMP-74` mặc định; người dùng chỉ quan tâm Tài khoản Chính thức, không bấm đồng ý | Xem trước chiến dịch Zalo OA | Người đó bị loại "Chưa có Đồng ý nhận tin có bằng chứng" |
| `AC-09.3.3` | `CFG-CAMP-74`, `CFG-CAMP-72` mặc định; một người điền biểu mẫu với số điện thoại của K nhưng không nhập mã xác nhận gửi tới số đó | Xem trước chiến dịch SMS | K bị loại "Chưa có Đồng ý nhận tin có bằng chứng" |

---

#### FEAT-10 — Tài khoản Gửi & Định danh Người gửi

**Mô tả nghiệp vụ:** Chiến dịch gửi đi dưới danh nghĩa một tài khoản gửi cụ thể của doanh nghiệp (một địa chỉ email gửi, một tài khoản WhatsApp doanh nghiệp, một Tài khoản Chính thức Zalo, một tên thương hiệu SMS).

**Quy tắc nghiệp vụ:**

- **`BR-10.1` (Nguồn tài khoản gửi):** Tài khoản WhatsApp và Zalo OA là **đúng các tài khoản kênh** đã kết nối tại `omnichat-srs.md` (`FEAT-01`). Tài khoản Zalo ZNS, địa chỉ gửi email và tên thương hiệu SMS (kèm đầu số nhận từ chối) do người có quyền quản trị **Quản lý kênh gửi tiếp thị** khai báo trong phân hệ này. Thông tin xác thực của mọi tài khoản không hiển thị lại sau khi lưu (cùng nguyên tắc `omnichat-srs.md`, `BR-01.7`).

  **Lý do nghiệp vụ:** Dùng chung tài khoản kênh với Hộp thư Hội thoại để khách hàng trả lời tin chiến dịch vào đúng nơi tư vấn viên đang làm việc; một danh mục tài khoản thứ hai sẽ sinh ra tin trả lời không ai đọc.

- **`BR-10.2` (Tên miền gửi email phải được xác thực):** Một địa chỉ email gửi chỉ được dùng cho chiến dịch khi tên miền của nó đã hoàn tất **xác thực người gửi** theo yêu cầu của các nhà cung cấp hộp thư lớn: khai báo máy chủ được phép gửi, chữ ký tên miền, và chính sách xác thực tên miền khớp với địa chỉ gửi hiển thị. Tên miền chưa xác thực hoặc mất xác thực thì không chọn được; chiến dịch đang dùng nó bị tự động tạm dừng bảo vệ (`FEAT-20`).

  **Lý do nghiệp vụ:** Các nhà cung cấp hộp thư lớn từ chối hoặc đẩy vào thư rác thư gửi hàng loạt từ tên miền chưa xác thực. Gửi 50.000 thư từ tên miền chưa xác thực không chỉ thất bại mà còn làm hỏng danh tiếng của tên miền đó cho cả thư giao dịch của doanh nghiệp.

- **`BR-10.3` (Tài khoản ngừng hoạt động giữa chừng):** Tài khoản gửi bị ngắt kết nối, bị nền tảng khóa hoặc bị người có quyền Quản lý kênh gửi tiếp thị vô hiệu hóa thì mọi chiến dịch Đang gửi dùng tài khoản đó bị tự động tạm dừng bảo vệ; chiến dịch Đã duyệt dùng tài khoản đó chuyển sang Giữ lại khi tới giờ hẹn (`BR-18.3`). Tài khoản gửi đã từng được dùng cho một chiến dịch không xóa được, chỉ vô hiệu hóa được, để sổ cái luôn chỉ ra được tin đã gửi từ tài khoản nào.

  **Lý do nghiệp vụ:** Gửi qua một tài khoản đã bị nền tảng khóa hoặc ngắt kết nối thì toàn bộ tin thất bại mà vẫn phát sinh chi phí; tiếp tục thử còn có thể làm nền tảng hạ điểm chất lượng của doanh nghiệp.

- **`BR-10.4` (Thông tin người gửi hiển thị):** Với Email: tên người gửi hiển thị và địa chỉ nhận thư trả lời được đặt theo từng chiến dịch; địa chỉ nhận thư trả lời mặc định trỏ về hộp thư email đã kết nối với Hộp thư Hội thoại (nếu có) để thư trả lời được xử lý theo `FEAT-35`; đổi sang địa chỉ khác thì địa chỉ đó phải thuộc danh sách hộp thư nhận trả lời do người có quyền quản trị **Quản lý kênh gửi tiếp thị** khai báo kèm người chịu trách nhiệm ghi nhận lời từ chối gửi tới hộp thư đó. Chỉ chọn được hộp thư đã kết nối với Hộp thư Hội thoại hoặc hộp thư bên ngoài hệ thống; hộp thư đã gán cho phân hệ Vé hỗ trợ không chọn được, vì mỗi hộp thư chỉ thuộc một phân hệ tiếp nhận — thư trả lời chiến dịch đổ vào đó sẽ thành vé hỗ trợ. Với hộp thư bên ngoài, danh mục kiểm tra hiển thị cảnh báo mà người duyệt phải xác nhận rằng lời từ chối gửi qua thư trả lời sẽ không được hệ thống tự nhận biết. Với SMS: chỉ dùng tên thương hiệu đã đăng ký, không nhập tự do.

  **Lý do nghiệp vụ:** Thư trả lời gửi về một địa chỉ không ai đọc là khách hàng tiềm năng bị bỏ rơi; tên thương hiệu SMS chưa đăng ký bị nhà mạng chặn toàn bộ.

- **`BR-10.5` (Tên thương hiệu SMS quảng cáo tách khỏi chăm sóc khách hàng):** Mỗi tên thương hiệu SMS được khai báo loại **Quảng cáo** hoặc **Chăm sóc khách hàng** theo đăng ký với nhà mạng. Chiến dịch chỉ chọn được tên thương hiệu loại Quảng cáo.

  **Lý do nghiệp vụ:** Nhà mạng quản lý và tính phí hai loại khác nhau; gửi nội dung quảng cáo qua tên thương hiệu chăm sóc khách hàng là vi phạm quy định của nhà mạng và có thể làm khóa luôn kênh gửi tin xác nhận, tin giao dịch của doanh nghiệp.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-10.1.1` | Tài khoản Chính thức Zalo "CSKH Miền Nam" đã kết nối ở Hộp thư Hội thoại | Tạo chiến dịch Zalo OA | "CSKH Miền Nam" có trong danh sách tài khoản gửi, không phải khai báo lại |
| `AC-10.2.1` | Tên miền `khuyenmai.congty.vn` chưa xác thực | Chọn địa chỉ gửi thuộc tên miền này | Không chọn được; giao diện hướng dẫn Quản trị viên hoàn tất xác thực |
| `AC-10.2.2` | Chiến dịch Đang gửi; tên miền gửi mất xác thực | Hệ thống phát hiện | Chiến dịch chuyển Tạm dừng, lý do "Tên miền gửi mất xác thực"; người phụ trách và người duyệt nhận thông báo |
| `AC-10.3.1` | Chiến dịch Đã duyệt hẹn 9:00; tài khoản WhatsApp bị ngắt kết nối lúc 8:00 | Tới 9:00 | Chiến dịch không gửi, chuyển Giữ lại, phê duyệt còn nguyên; thông báo nêu lý do |
| `AC-10.3.2` | Tài khoản gửi đã từng dùng cho chiến dịch | Quản trị viên tìm thao tác xóa | Chỉ có thao tác vô hiệu hóa |
| `AC-10.4.1` | Chiến dịch SMS | Soạn tên người gửi | Chỉ chọn được trong danh sách tên thương hiệu đã đăng ký |
| `AC-10.4.3` | Hộp thư "hotro@" đã gán cho phân hệ Vé hỗ trợ | Người có quyền Quản lý kênh gửi tiếp thị thêm hộp thư này vào danh sách hộp thư nhận trả lời | Không cho phép, nêu hộp thư đã thuộc phân hệ Vé hỗ trợ |
| `AC-10.4.4` | Chiến dịch Email đặt địa chỉ nhận thư trả lời là hộp thư bên ngoài | Người duyệt bấm phê duyệt khi chưa xác nhận cảnh báo "lời từ chối gửi qua thư trả lời sẽ không được hệ thống tự nhận biết" | Không phê duyệt được cho tới khi xác nhận cảnh báo; nhật ký ghi người xác nhận |
| `AC-10.4.5` | Người có quyền Quản lý kênh gửi tiếp thị thêm một hộp thư bên ngoài vào danh sách hộp thư nhận trả lời mà không khai báo người chịu trách nhiệm ghi nhận lời từ chối | Lưu | Không cho phép; trường người chịu trách nhiệm là bắt buộc |
| `AC-10.4.2` | Không gian làm việc có hộp thư email đã kết nối với Hộp thư Hội thoại | Tạo chiến dịch Email | Địa chỉ nhận thư trả lời mặc định là hộp thư đã kết nối; người soạn đổi được |
| `AC-10.5.1` | Có hai tên thương hiệu: "CONGTY" (Quảng cáo), "CONGTY-CSKH" (Chăm sóc khách hàng) | Chọn tên thương hiệu cho chiến dịch SMS | Chỉ chọn được "CONGTY"; "CONGTY-CSKH" hiển thị kèm lý do không chọn được |

---

### Nhóm D — Nội dung & Cá nhân hóa

#### FEAT-11 — Soạn Nội dung theo Kênh

**Mô tả nghiệp vụ:** Người soạn tạo nội dung phù hợp đặc điểm từng kênh: thư email trình bày phong phú, tin nhắn ngắn cho SMS, tin nhắn mẫu cho WhatsApp/Zalo.

**Quy tắc nghiệp vụ:**

- **`BR-11.1` (Email):** Nội dung email gồm tiêu đề, dòng xem trước, thân thư soạn bằng trình soạn kéo thả hoặc chỉnh trực tiếp mã trình bày, và **bản văn bản thuần** đi kèm. Bản văn bản thuần được sinh tự động từ thân thư và cho phép chỉnh tay; bản văn bản thuần cũng bắt buộc chứa liên kết hủy nhận tin. Chân thư bắt buộc chứa thông tin nhận diện doanh nghiệp gửi (tên, địa chỉ bưu chính thực tế) và liên kết hủy nhận tin (`BR-26.1`); người soạn không xóa hay ẩn được phần bắt buộc này.

  **Lý do nghiệp vụ:** Một số hộp thư và thiết bị chỉ hiển thị văn bản thuần; thư thiếu bản văn bản thuần dễ bị bộ lọc thư rác đánh giá thấp. Thông tin nhận diện người gửi là yêu cầu của các nhà cung cấp hộp thư với người gửi thư hàng loạt.

- **`BR-11.2` (Tin nhắn mẫu cho WhatsApp/Zalo):** Với kênh có thuộc tính "bắt buộc dùng tin nhắn mẫu" (`BR-09.1`), người soạn chỉ chọn một tin nhắn mẫu đã phê duyệt (`FEAT-13`) và điền giá trị cho các vị trí tham số của mẫu (cố định hoặc bằng thông tin cá nhân hóa).

  **Lý do nghiệp vụ:** Nền tảng từ chối tin không khớp đúng mẫu đã duyệt; cho soạn tự do trên các kênh này chỉ tạo ra một chiến dịch thất bại toàn bộ.

- **`BR-11.3` (SMS — độ dài và chi phí):** Khi soạn SMS, giao diện hiển thị liên tục: số ký tự, số đơn vị tin tính phí, và cảnh báo khi nội dung dùng ký tự có dấu hoặc ký tự đặc biệt làm giảm số ký tự mỗi đơn vị tin, hoặc khi nhà mạng của một phần người nhận chỉ chấp nhận tin không dấu. Với nội dung có cá nhân hóa, số đơn vị tin được ước tính theo **giá trị dài nhất** có thể xuất hiện trong danh sách người nhận khả dụng. Nhãn quảng cáo bắt buộc (`FEAT-30`) và hướng dẫn từ chối nhận tin (`BR-26.1`) được tính vào độ dài.

  **Lý do nghiệp vụ:** Một ký tự tiếng Việt có dấu có thể làm một tin 1 đơn vị thành 2–3 đơn vị, nhân đôi hoặc nhân ba chi phí của cả chiến dịch mà người soạn không hề hay biết.

- **`BR-11.4` (Nội dung cho kênh dự phòng):** Nếu chiến dịch có cấu hình dự phòng (`FEAT-37`), người soạn phải soạn nội dung riêng cho **từng** kênh dự phòng, tuân theo đúng các quy tắc của kênh đó. Không có cơ chế tự động chuyển nội dung email thành SMS.

  **Lý do nghiệp vụ:** Nội dung email 800 chữ kèm hình ảnh cắt ngắn tự động thành SMS gần như luôn ra một tin vô nghĩa hoặc sai ý; người duyệt phải thấy được chính xác cái mà khách hàng sẽ nhận trên mỗi kênh.

- **`BR-11.5` (Thư viện nội dung):** Người soạn lưu nội dung thành mẫu nội dung nội bộ để dùng lại, và tạo chiến dịch từ mẫu nội dung. Sửa mẫu nội dung không làm thay đổi nội dung của chiến dịch đã tạo từ mẫu đó.

  **Lý do nghiệp vụ:** Chiến dịch phải gửi đúng phiên bản đã duyệt (Nguyên tắc 2); nếu sửa mẫu lan sang chiến dịch đã tạo, một thay đổi ở thư viện sẽ đổi nội dung của chiến dịch đã được duyệt mà không ai duyệt lại.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-11.1.1` | Trình soạn email | Xóa khối chân thư | Không xóa được phần thông tin nhận diện và liên kết hủy nhận tin; phần còn lại của chân thư sửa được |
| `AC-11.1.2` | Thân thư vừa sửa | Mở bản văn bản thuần | Bản văn bản thuần đã được cập nhật theo thân thư; sửa tay được |
| `AC-11.2.1` | Chiến dịch WhatsApp, mẫu có hai vị trí tham số | Để trống một vị trí rồi gửi phê duyệt | Danh mục kiểm tra có lỗi chặn yêu cầu điền giá trị hoặc chọn thông tin cá nhân hóa cho vị trí đó |
| `AC-11.3.1` | Soạn SMS 150 ký tự không dấu | Thêm một ký tự có dấu | Số đơn vị tin tính phí tăng và cảnh báo hiển thị ngay |
| `AC-11.3.2` | SMS có "{tên}"; tên dài nhất trong danh sách khả dụng là 28 ký tự | Xem ước tính | Số đơn vị tin tính theo tên dài 28 ký tự |
| `AC-11.4.1` | Chiến dịch Zalo ZNS có dự phòng SMS | Mở phần soạn | Có hai khu vực soạn riêng: tin nhắn mẫu Zalo ZNS và nội dung SMS |
| `AC-11.5.1` | Chiến dịch C tạo từ mẫu nội dung M; sau đó M được sửa | Mở C | Nội dung C không đổi |

---

#### FEAT-12 — Trộn Thông tin Cá nhân hóa

**Mô tả nghiệp vụ:** Chèn thông tin của từng người nhận vào nội dung (tên, công ty, chức danh, người phụ trách…).

**Quy tắc nghiệp vụ:**

- **`BR-12.1` (Nguồn thông tin cá nhân hóa):** Dùng được thông tin từ hồ sơ khách hàng, doanh nghiệp liên kết chính và người phụ trách khách hàng (tên, email, số điện thoại công việc), **chỉ** với những trường mà người soạn nội dung xem được ở **dạng đầy đủ, không che** theo phân quyền trường và khung che dữ liệu (bước 4 của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`; `BR-07.3`). Trường người soạn bị ẩn hoặc chỉ thấy ở dạng che không có trong danh sách thông tin cá nhân hóa; nội dung đã chứa một trường như vậy (do người khác soạn trước, hoặc quyền của người soạn bị thu hẹp sau đó) là lỗi chặn nhóm (a) khi người đó lưu, gửi phê duyệt hay duyệt (`BR-16.1`). **Không** dùng được các trường mà cấu hình bảo mật trường đánh dấu là nhạy cảm (`object-manager-srs.md`), bất kể người soạn có quyền xem trường đó hay không.

  **Lý do nghiệp vụ:** Nội dung chiến dịch rời khỏi hệ thống, đi qua nhà cung cấp bên thứ ba và có thể bị người khác đọc (điện thoại dùng chung, thư bị chuyển tiếp). Trường nhạy cảm được bảo vệ bên trong hệ thống không được rò ra ngoài qua một lời chào. Người soạn không được đưa vào nội dung một trường mình không được xem đầy đủ, vì bản gửi thử và bản xem trước sẽ cho họ thấy chính giá trị đó.

- **`BR-12.2` (Giá trị thay thế hoặc bắt buộc có dữ liệu):** Với mỗi thông tin cá nhân hóa dùng trong nội dung, người soạn chọn một trong hai: **giá trị thay thế** khi người nhận không có dữ liệu (ví dụ "Quý khách"), hoặc **bắt buộc có dữ liệu** — người nhận không có dữ liệu bị loại với lý do "Thiếu dữ liệu cá nhân hóa bắt buộc". Không có lựa chọn thứ ba để trống: một thông tin cá nhân hóa chưa chọn cách xử lý là lỗi chặn trong danh mục kiểm tra (`FEAT-16`). Vị trí tham số của tin nhắn mẫu mà nền tảng nhắn tin không cho phép để trống luôn thuộc diện bắt buộc có dữ liệu hoặc phải có giá trị thay thế.

  **Lý do nghiệp vụ:** "Kính gửi {tên}," hay "Kính gửi ," tới hàng nghìn khách hàng là sai sót tiếp thị phổ biến nhất và làm mất uy tín ngay lập tức. Buộc chọn rõ ràng để không ai được phép "quên".

- **`BR-12.3` (Giá trị tại thời điểm gửi):** Thông tin cá nhân hóa được lấy theo dữ liệu hồ sơ **tại thời điểm gửi** tin tới người đó, không theo thời điểm chốt danh sách.

  **Lý do nghiệp vụ:** Khách sửa tên sai chính tả hôm qua thì hôm nay phải được gọi đúng tên; chốt giá trị từ lúc chốt danh sách là gửi đi dữ liệu mà chính khách vừa sửa.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-12.1.1` | Trường "Số căn cước" được đánh dấu nhạy cảm | Người soạn có quyền xem trường đó mở danh sách thông tin cá nhân hóa | Trường không có trong danh sách |
| `AC-12.1.2` | Trường "Hạn mức tín dụng" bị ẩn với người soạn theo phân quyền trường | Mở danh sách thông tin cá nhân hóa | Trường không có trong danh sách |
| `AC-12.1.3` | Người soạn chỉ thấy số điện thoại của khách hàng ở dạng che | Mở danh sách thông tin cá nhân hóa | "Số điện thoại khách hàng" không có trong danh sách |
| `AC-12.1.4` | Nội dung do A soạn có "{hạn mức}"; người duyệt M không được xem trường này | M mở màn hình duyệt | Lỗi chặn nêu trường M không được xem; không có nút phê duyệt cho M |
| `AC-12.2.1` | Nội dung dùng "{công ty}" chưa chọn cách xử lý | Mở danh mục kiểm tra | Lỗi chặn "Chưa chọn cách xử lý khi thiếu dữ liệu cho {công ty}" |
| `AC-12.2.2` | "{tên}" có giá trị thay thế "Quý khách"; khách K không có tên | Gửi | K nhận "Chào Quý khách" |
| `AC-12.2.3` | "{công ty}" chọn bắt buộc có dữ liệu; 40 người không có công ty | Xem trước | 40 người bị loại "Thiếu dữ liệu cá nhân hóa bắt buộc" |
| `AC-12.3.1` | K đổi tên trên hồ sơ sau khi danh sách đã chốt nhưng trước khi tới lượt K | Gửi tới K | Tin dùng tên mới |

---

#### FEAT-13 — Tin nhắn Mẫu đã Phê duyệt

**Mô tả nghiệp vụ:** Với các nền tảng nhắn tin yêu cầu duyệt trước nội dung tin do doanh nghiệp chủ động gửi, chiến dịch chỉ dùng được mẫu đã được nền tảng duyệt.

**Quy tắc nghiệp vụ:**

- **`BR-13.1` (Danh mục mẫu, loại mẫu và điều kiện dùng cho tiếp thị):** Hệ thống hiển thị danh mục mẫu của từng tài khoản kênh cùng trạng thái do nền tảng báo về (Đã duyệt / Chờ duyệt / Bị từ chối / Tạm khóa / Đã vô hiệu), **loại mẫu** do nền tảng phân loại, và **điểm chất lượng** nếu nền tảng có. Chỉ mẫu **Đã duyệt** và thuộc loại mà nền tảng cho phép dùng cho mục đích quảng bá mới chọn được trong chiến dịch. Cụ thể: với WhatsApp, chỉ mẫu loại tiếp thị; với Zalo ZNS, chỉ loại mẫu mà chính sách Zalo cho phép chứa nội dung quảng bá — nếu tài khoản không có mẫu nào như vậy thì kênh Zalo ZNS không dùng được cho chiến dịch của tài khoản đó; với Zalo OA, nội dung chịu quy định về tin truyền thông và hạn mức gửi tới người quan tâm của nền tảng.

  **Lý do nghiệp vụ:** Chiến dịch luôn thuộc nhóm mục đích Tiếp thị (`BR-01.5`). Dùng mẫu được duyệt cho mục đích chăm sóc/giao dịch để gửi nội dung quảng bá là vi phạm chính sách của nền tảng, có thể dẫn tới khóa tài khoản kênh — và tài khoản kênh đó cũng đang được dùng cho Hộp thư Hội thoại.

- **`BR-13.2` (Mẫu đổi trạng thái giữa chừng):** Nếu một mẫu đang được chiến dịch Đang gửi sử dụng chuyển sang Tạm khóa, Bị từ chối, Đã vô hiệu, hoặc bị nền tảng **đổi sang loại không được dùng cho tiếp thị**, chiến dịch bị tự động tạm dừng bảo vệ (`FEAT-20`). Với chiến dịch Đã duyệt hoặc Giữ lại: mẫu **Tạm khóa** là lỗi chặn vận hành (chiến dịch chuyển Giữ lại khi tới giờ); mẫu Bị từ chối, Đã vô hiệu hoặc bị đổi loại là lỗi làm mất hiệu lực phiên bản (chiến dịch về Nháp). Điểm chất lượng của mẫu giảm xuống mức nền tảng cảnh báo thì người phụ trách được thông báo.

  **Lý do nghiệp vụ:** Nền tảng có thể đổi trạng thái hoặc loại mẫu bất kỳ lúc nào, kể cả giữa lúc gửi; tạm khóa thường chỉ kéo dài vài giờ nên không đáng xóa phê duyệt, còn mẫu đã bị vô hiệu thì phiên bản đã duyệt không thể gửi được nữa; tiếp tục gửi khi mẫu đã bị khóa chỉ tạo ra hàng loạt tin thất bại và làm giảm thêm điểm chất lượng của tài khoản.

- **`BR-13.3` (Nội dung SMS quảng cáo phải được nhà mạng duyệt):** Nội dung SMS quảng cáo phải được đăng ký và duyệt với nhà mạng (qua đối tác cung cấp dịch vụ) trước khi gửi. Hệ thống hiển thị trạng thái duyệt của nội dung **theo từng nhà mạng**. Nhà mạng của từng điểm đến được xác định theo **tra cứu nhà mạng thực tế** do đối tác cung cấp (tính cả các số đã chuyển mạng giữ số), không suy ra từ đầu số. Tin bị nhà mạng từ chối vì nội dung chưa được duyệt được xếp nhóm lỗi "Nội dung bị từ chối" (`BR-24.1`), không đánh dấu điểm đến hỏng. Chiến dịch không có nội dung được duyệt với nhà mạng nào là lỗi chặn (`BR-16.1`); nội dung được duyệt với một số nhà mạng thì người nhận thuộc nhà mạng chưa duyệt bị loại với lý do "Nội dung chưa được nhà mạng của điểm đến duyệt" (`BR-08.1`). Mọi thay đổi nội dung SMS, hoặc đổi sang tên thương hiệu khác, sau khi được duyệt phải đăng ký lại. Bản nộp duyệt là **bản cuối cùng sẽ gửi đi**, gồm cả nhãn quảng cáo và hướng dẫn từ chối do hệ thống tự thêm (`BR-30.2`, `BR-26.1`); thông tin cá nhân hóa chỉ được đặt ở các vị trí tham số mà mẫu đã được nhà mạng duyệt cho phép, đặt ở vị trí khác là lỗi chặn nhóm (a).

  **Lý do nghiệp vụ:** Nhà mạng từ chối cả lô tin quảng cáo có nội dung chưa đăng ký; một chiến dịch được duyệt trong hệ thống nhưng bị nhà mạng chặn ở bước cuối là thất bại hoàn toàn mà người duyệt không lường trước được. Bản nộp duyệt phải là bản cuối cùng vì nhà mạng đối chiếu nguyên văn tin gửi đi.

- **`BR-13.4` (Hiệu lực duyệt và khung giờ của đối tác):** Khi nhà mạng hoặc đối tác duyệt nội dung SMS quảng cáo kèm **hiệu lực** (khoảng thời gian được gửi, sản lượng tối đa) hoặc **khung giờ gửi được phép** hẹp hơn khung giờ yên lặng của doanh nghiệp, hệ thống lưu các giới hạn đó theo nội dung và theo nhà mạng. Giờ hẹn hoặc lịch dàn trải dự kiến nằm ngoài hiệu lực là lỗi chặn khi duyệt; tại thời điểm gửi, tin rơi ngoài khung giờ được phép thì được **hoãn** tới đầu khung kế tiếp còn trong hiệu lực, tin mà hiệu lực đã hết hoặc sản lượng đã dùng hết thì bị loại (lý do 11). Quy tắc áp cho mọi lượt gửi SMS: lượt chính, dự phòng, gửi lại, bước chuỗi nuôi dưỡng.

  **Lý do nghiệp vụ:** Nhà mạng từ chối cả loạt tin quảng cáo gửi ngoài đợt đã đăng ký; các tin phát ra tự động (dự phòng lẻ tẻ, dàn trải sang ngày khác, bước chuỗi) là nơi vi phạm hay xảy ra nhất vì không ai nhìn thấy chúng lúc duyệt.

- **`BR-13.5` (Nội dung cần nền tảng kiểm duyệt trước mỗi lượt gửi):** Với kênh mà nền tảng kiểm duyệt nội dung trước khi phát (theo chính sách hiện hành của nền tảng — ví dụ tin truyền thông của Tài khoản Chính thức Zalo), hệ thống gửi nội dung đi kiểm duyệt ngay khi phiên bản được **gửi phê duyệt nội bộ** hoặc **tự xác nhận** theo `BR-17.9` (gửi lại nếu phiên bản thay đổi) và hiển thị trạng thái do nền tảng báo về. Nội dung **bị nền tảng từ chối** là lỗi chặn nhóm (a); nội dung **đang chờ nền tảng kiểm duyệt** không cản việc duyệt nội bộ, nhưng tại lúc bắt đầu gửi là lỗi chặn nhóm (b) (chiến dịch hẹn giờ chuyển Giữ lại). Hạn mức gửi tới từng người quan tâm do nền tảng áp được xử lý theo nhóm lỗi "Nền tảng giới hạn theo người nhận" (`BR-24.1`) và được đối chiếu trước ở `BR-22.2`.

  **Lý do nghiệp vụ:** Chiến dịch hẹn giờ có thể bị nền tảng giữ hoặc từ chối đúng lúc gửi; chỉ khi trạng thái kiểm duyệt là một phần của chuỗi kiểm tra thì người duyệt mới thấy trước được rủi ro này.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-13.1.1` | Tài khoản WhatsApp có một mẫu Đã duyệt loại tiếp thị, một mẫu Đã duyệt loại giao dịch, một mẫu Chờ duyệt | Chọn mẫu cho chiến dịch | Chỉ chọn được mẫu Đã duyệt loại tiếp thị; hai mẫu còn lại hiển thị kèm lý do không chọn được |
| `AC-13.1.2` | Tài khoản Zalo ZNS không có mẫu nào thuộc loại được dùng cho nội dung quảng bá | Tạo chiến dịch Zalo ZNS | Không chọn được mẫu nào; giao diện giải thích kênh này không dùng được cho chiến dịch của tài khoản |
| `AC-13.2.1` | Chiến dịch Zalo ZNS Đang gửi; nền tảng báo mẫu bị Tạm khóa | Hệ thống nhận thông báo | Chiến dịch chuyển Tạm dừng, lý do "Tin nhắn mẫu bị nền tảng tạm khóa" |
| `AC-13.2.2` | Chiến dịch Đã duyệt hẹn giờ dùng mẫu WhatsApp; nền tảng đổi mẫu từ loại tiếp thị sang loại khác | Hệ thống nhận thông báo | Chiến dịch về Nháp; người phụ trách và người duyệt được thông báo |
| `AC-13.2.3` | Chiến dịch Đã duyệt hẹn 09:00; lúc 08:00 mẫu bị nền tảng Tạm khóa | Tới 09:00 | Chiến dịch Giữ lại, phê duyệt còn nguyên; nếu mẫu được mở lại trong thời gian ân hạn thì phát sóng được không cần duyệt lại |
| `AC-13.3.1` | Nội dung SMS được duyệt với hai nhà mạng, chưa duyệt với nhà mạng thứ ba | Xem trước | Người nhận thuộc nhà mạng thứ ba bị loại "Nội dung chưa được nhà mạng của điểm đến duyệt" |
| `AC-13.3.2` | Nội dung SMS đã được duyệt | Người soạn sửa một từ | Trạng thái duyệt về "Chưa duyệt" với mọi nhà mạng |
| `AC-13.3.3` | Số của K có đầu số của nhà mạng X nhưng đã chuyển sang nhà mạng Y; nội dung chỉ được Y duyệt | Xem trước | K khả dụng (nhà mạng tra cứu thực tế là Y) |
| `AC-13.3.4` | Nội dung SMS nộp duyệt chưa có nhãn "QC" và hướng dẫn từ chối | Mở màn hình nộp duyệt | Bản nộp duyệt hiển thị đúng bản cuối cùng, đã gồm nhãn và hướng dẫn |
| `AC-13.4.1` | Nội dung SMS được duyệt với hiệu lực từ ngày 1 đến ngày 3, khung giờ 08:00–20:00; chiến dịch dàn trải dự kiến kết thúc ngày 5 | Mở danh mục kiểm tra | Lỗi chặn: lịch dàn trải vượt hiệu lực duyệt |
| `AC-13.4.2` | Như trên nhưng lịch nằm trong hiệu lực; một SMS dự phòng tới lượt lúc 21:00 | Hệ thống xử lý | Tin bị hoãn tới 08:00 hôm sau (nếu còn trong hiệu lực) |
| `AC-13.5.1` | Chiến dịch Zalo OA hẹn 20:00; nền tảng chưa kiểm duyệt xong nội dung lúc 20:00 | Tới giờ | Chiến dịch Giữ lại, lý do nội dung đang chờ nền tảng kiểm duyệt |
| `AC-13.5.2` | Nền tảng từ chối nội dung Zalo OA của chiến dịch Đã duyệt | Hệ thống nhận thông báo | Chiến dịch về Nháp |

---

#### FEAT-14 — Liên kết Theo dõi & Tham số Nguồn gốc

**Mô tả nghiệp vụ:** Liên kết trong nội dung được theo dõi để đo lượt nhấp, và được gắn tham số nguồn gốc để khách hàng tiềm năng phát sinh từ chiến dịch được ghi nhận đúng nguồn.

**Quy tắc nghiệp vụ:**

- **`BR-14.1` (Theo dõi nhấp):** Mọi liên kết trong nội dung (trừ liên kết hủy nhận tin) được chuyển thành liên kết theo dõi — riêng cho từng người nhận nếu người đó có căn cứ theo dõi hành vi (`BR-14.4`) **và** kênh cho phép nội dung khác nhau giữa các người nhận ở vị trí liên kết, hoặc liên kết chung không gắn danh tính nếu không. Với SMS, liên kết theo dõi riêng chỉ dùng khi nội dung đã được nhà mạng duyệt có vị trí tham số cho liên kết và tên miền rút gọn đã đăng ký với nhà mạng (`BR-13.3`); nếu không, dùng một liên kết chung cho mọi người nhận — chuyển tiếp người nhận tới đúng địa chỉ gốc. Liên kết theo dõi phải tiếp tục hoạt động (chuyển tiếp đúng) **vô thời hạn**, kể cả sau khi chiến dịch lưu trữ hoặc hết thời hạn lưu chi tiết sổ cái (`CFG-CAMP-20`) — khi đó vẫn chuyển tiếp nhưng không còn ghi sự kiện cho từng người. Lượt nhấp mới trên thư cũ chỉ được ghi gắn danh tính khi người đó còn căn cứ theo dõi (`BR-14.4`) và thời điểm nhấp còn trong thời hạn `CFG-CAMP-45` tính từ lúc gửi; ngoài thời hạn đó chỉ ghi tổng hợp.

  **Lý do nghiệp vụ:** Khách hàng mở lại một email cũ nhiều tháng sau và bấm vào — một liên kết chết khiến doanh nghiệp mất khách ngay ở bước cuối.

- **`BR-14.2` (Tham số nguồn gốc tự động):** Khi `CFG-CAMP-19` bật, mỗi liên kết trỏ tới **tên miền thuộc doanh nghiệp** (danh sách do người có quyền Quản lý kênh gửi tiếp thị khai báo) được tự động gắn bộ năm tham số nguồn gốc theo `contacts-srs.md` (`BR-32.2`): nguồn là kênh gửi, phương tiện là "chiến dịch tiếp thị", tên chiến dịch là **mã định danh chiến dịch** (`FEAT-03`), nội dung là phiên bản A/B nếu có, từ khóa để trống. Liên kết đã có sẵn tham số nguồn gốc do người soạn tự đặt thì **giữ nguyên**, không ghi đè. Liên kết tới tên miền bên ngoài không bị gắn tham số.

  **Lý do nghiệp vụ:** Tham số nguồn gốc là cơ sở để phân hệ Khách hàng ghi nhận nguồn gốc lần đầu của khách tiềm năng (`contacts-srs.md`, `BR-32.3`) và từ đó thành Nguồn gốc chính của cơ hội (`deals-pipeline-srs.md`, `BR-23.1`). Dùng mã chiến dịch thay vì tên để đổi tên chiến dịch không làm gãy ghi nhận. Không gắn cho tên miền bên ngoài để không làm lộ thông tin chiến dịch cho bên thứ ba.

- **`BR-14.3` (Loại lượt nhấp tự động):** Lượt nhấp nhận diện được là do phần mềm quét bảo mật của hộp thư thực hiện (không phải người nhận), theo bộ tiêu chí nhận diện tại Phụ lục B.2, được ghi riêng, **không** tính vào số liệu nhấp, **không** kích hoạt gắn thẻ tự động (`FEAT-36`), **không** cộng điểm tương tác (`contacts-srs.md`, `BR-15.2`) và **không** tính là tương tác để ghi nhận ảnh hưởng (`FEAT-34`).

  Khi một lượt nhấp đã được tính **sau đó** mới được nhận diện là lượt nhấp tự động, mọi hệ quả được đảo lại: số liệu báo cáo được tính lại, thẻ gắn từ lượt nhấp đó được gỡ (`BR-36.4`), cơ hội không còn đủ điều kiện chịu ảnh hưởng thì không còn được tính (`BR-34.3`), phân hệ Khách hàng được thông báo để trừ điểm tương tác đã cộng, và nếu đó là tương tác duy nhất đã đưa một khách Đã rời bỏ về Nurturing (`BR-31.4`) thì người phụ trách khách hàng được thông báo để quyết định giai đoạn phù hợp theo ma trận chuyển giai đoạn của (`contacts-srs.md`, `BR-12.6`) — phân hệ Chiến dịch không tự đổi giai đoạn, vì ma trận đó không có bước tự động từ Nurturing về Đã rời bỏ. Cho tới khi người phụ trách quyết định, khách đó bị loại khỏi mọi chiến dịch không phải Tái tiếp cận với lý do "Giai đoạn vòng đời" (`BR-08.1` mục 2). Người phụ trách quyết định bằng một trong hai thao tác trên hồ sơ: **"Xác nhận giai đoạn hiện tại"** (gỡ việc loại, khách tiếp tục ở Nurturing) hoặc chuyển khách sang một giai đoạn hợp lệ theo ma trận (việc loại tự gỡ khi giai đoạn đổi); người phụ trách được nhắc lại mỗi `CFG-CAMP-51` cho tới khi quyết định.

  **Lý do nghiệp vụ:** Hệ thống bảo mật của nhiều doanh nghiệp tự động "bấm" mọi liên kết trong thư đến để kiểm tra mã độc, khiến cả tệp khách hàng doanh nghiệp trông như đã nhấp 100% — làm sai tỷ lệ nhấp, gắn thẻ "quan tâm" sai hàng loạt và đẩy khách hàng lên ngưỡng tiềm năng một cách giả tạo.

- **`BR-14.4` (Căn cứ để theo dõi hành vi từng người):** Khi `CFG-CAMP-73` bật (mặc định), theo dõi mở và nhấp **gắn với danh tính người nhận** chỉ được thực hiện khi phiên bản điều khoản đồng thuận mà người nhận đã đồng ý (bằng chứng theo `contacts-srs.md`, `BR-30.3` (c)) có nêu mục đích đo lường tương tác với thông điệp tiếp thị. Nếu không, tin vẫn được gửi nhưng hệ thống chỉ ghi nhận **tổng hợp không gắn danh tính** (số lượt nhấp theo liên kết), không đặt cơ chế đo mở cho người đó, và các tính năng dựa trên hành vi từng người — tiêu chí đối tượng theo hành vi (`BR-05.1`), gắn thẻ tự động (`FEAT-36`), nhánh điều kiện của chuỗi (`BR-39.1`), ghi nhận cơ hội chịu ảnh hưởng (`FEAT-34`), cộng điểm tương tác — không áp dụng cho người đó. Khi `CFG-CAMP-73` tắt, người có Đồng ý nhận tin được theo dõi gắn danh tính mà không cần điều khoản nêu mục đích đo lường. Người nhận tin trên kênh không yêu cầu Đồng ý trước mà ở trạng thái Chưa có đồng thuận, hoặc chỉ có Đồng ý không được coi là căn cứ (Đồng ý tự khôi phục, Đồng ý biểu mẫu chưa xác nhận — lý do 6 của `BR-08.1`), **không** có căn cứ theo dõi gắn danh tính, dù `CFG-CAMP-73` bật hay tắt — chỉ được ghi nhận tổng hợp. Người đang Hạn chế xử lý luôn chỉ được ghi nhận tổng hợp.

  **Lý do nghiệp vụ:** Đồng ý nhận tin không đồng nghĩa với đồng ý bị theo dõi và lập hồ sơ hành vi; đây là một mục đích xử lý dữ liệu riêng. Giữ con số tổng hợp để doanh nghiệp vẫn đo được hiệu quả nội dung mà không xử lý dữ liệu cá nhân ngoài căn cứ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-14.1.1` | Chiến dịch đã lưu trữ và quá thời hạn lưu chi tiết sổ cái | Người nhận bấm liên kết trong thư cũ | Được chuyển tới đúng trang đích |
| `AC-14.1.2` | Nội dung SMS được nhà mạng duyệt nguyên văn, liên kết không phải vị trí tham số | Gửi chiến dịch SMS | Mọi người nhận nhận cùng một liên kết; báo cáo chỉ có số lượt nhấp tổng hợp |
| `AC-14.2.1` | `CFG-CAMP-19` bật; liên kết tới trang của doanh nghiệp không có tham số | Gửi thử và bấm liên kết | Trang đích nhận đủ tham số nguồn gốc, tên chiến dịch là mã định danh chiến dịch |
| `AC-14.2.2` | Liên kết đã có tham số nguồn gốc do người soạn đặt | Gửi thử và bấm | Tham số của người soạn giữ nguyên |
| `AC-14.2.3` | Liên kết tới trang báo điện tử bên ngoài | Gửi thử và bấm | Không có tham số nguồn gốc được thêm |
| `AC-14.3.1` | Một thư được "nhấp" mọi liên kết trong vòng vài giây sau khi phân phát, nhận diện được là phần mềm quét | Xem báo cáo và hồ sơ khách hàng | Không tính lượt nhấp, không gắn thẻ tự động, không cộng điểm; sổ cái ghi "Lượt nhấp tự động bị loại" |
| `AC-14.3.2` | Lượt nhấp của K đã được tính và đã gắn thẻ; sau đó được nhận diện lại là lượt nhấp tự động | Hệ thống xử lý | Báo cáo giảm một lượt nhấp; thẻ gắn từ lượt nhấp đó bị gỡ; phân hệ Khách hàng nhận yêu cầu trừ điểm |
| `AC-14.4.1` | K Đồng ý nhận email theo phiên bản điều khoản không nêu mục đích đo lường tương tác | K mở thư và nhấp liên kết | Báo cáo tăng tổng số lượt nhấp của liên kết; sổ cái của K không có sự kiện mở hay nhấp; không gắn thẻ, không cộng điểm |
| `AC-14.4.2` | K đang Hạn chế xử lý nhấp một liên kết trong thư cũ | Hệ thống xử lý | Chỉ tăng số lượt nhấp tổng hợp; K không bị gắn thẻ, không bị chuyển giai đoạn, không có sự kiện gắn danh tính |
| `AC-14.4.3` | `CFG-CAMP-74` không gồm SMS; K chỉ có Đồng ý tự khôi phục chưa xác nhận lại trên SMS | K nhấp liên kết trong SMS tiếp thị | Chỉ tăng số lượt nhấp tổng hợp; không có sự kiện gắn danh tính K |

---

### Nhóm E — Kiểm tra Trước Phát sóng

#### FEAT-15 — Gửi Thử

**Mô tả nghiệp vụ:** Gửi bản thật của nội dung tới một vài địa chỉ nội bộ để kiểm tra hiển thị trên thiết bị thật trước khi gửi phê duyệt hoặc phát sóng.

**Quy tắc nghiệp vụ:**

- **`BR-15.1` (Chỉ gửi tới địa chỉ nội bộ đã đăng ký):** Người nhận gửi thử chỉ được là: (a) địa chỉ email / số điện thoại công việc của **thành viên Không gian làm việc** đã được xác minh chủ sở hữu (thành viên bấm liên kết hoặc nhập mã gửi tới chính địa chỉ đó; địa chỉ thuộc tên miền nội bộ đã khai báo được coi là đã xác minh), hoặc (b) địa chỉ thuộc **Danh sách người nhận gửi thử**. Một địa chỉ chỉ vào được Danh sách người nhận gửi thử khi: thuộc tên miền nội bộ đã khai báo của doanh nghiệp (danh sách tên miền nội bộ do người có quyền quản trị **Quản lý danh sách người nhận gửi thử** khai báo, Người phụ trách Bảo vệ Dữ liệu cùng chấp thuận; không được khai tên miền của dịch vụ thư công cộng), **hoặc** chính chủ địa chỉ đã bấm liên kết xác nhận gửi tới địa chỉ đó; **và** địa chỉ đó không trùng với điểm đến của bất kỳ hồ sơ khách hàng nào — trừ khi hồ sơ trùng chính là hồ sơ khách hàng của thành viên đó (nhân viên đồng thời là khách mua hàng) và Người phụ trách Bảo vệ Dữ liệu chấp thuận ngoại lệ cho đúng địa chỉ đó. Thay đổi Danh sách người nhận gửi thử cần người có quyền Quản lý danh sách người nhận gửi thử cùng Người phụ trách Bảo vệ Dữ liệu chấp thuận. Mỗi lượt gửi thử tối đa `CFG-CAMP-18` người nhận; mỗi chiến dịch tối đa `CFG-CAMP-42` lượt gửi thử mỗi ngày, và mỗi người nhận gửi thử nhận tối đa `CFG-CAMP-52` tin gửi thử mỗi ngày trên toàn Không gian làm việc. Mọi lượt gửi thử được ghi nhật ký (`NFR-08`). **Tại mỗi lượt gửi thử**, mọi người nhận thử — kể cả địa chỉ công việc của thành viên — được đối chiếu lại với điểm đến của hồ sơ khách hàng, dấu vết chặn gửi (`BR-32.4`) và Danh sách không quảng cáo; trùng thì người nhận đó bị loại khỏi lượt gửi thử — trừ địa chỉ đã được chấp thuận theo ngoại lệ nhân viên đồng thời là khách hàng nêu dưới đây, chỉ được miễn phần trùng với chính hồ sơ khách hàng của thành viên đó (vẫn bị loại nếu khớp dấu vết chặn gửi hay Danh sách không quảng cáo).

  **Lý do nghiệp vụ:** Nếu gửi thử tới được địa chỉ bất kỳ, một người không có quyền Phát sóng chiến dịch có thể "gửi thử" liên tục tới khách hàng thật, vượt qua cả phê duyệt kép lẫn kiểm tra đồng thuận.

- **`BR-15.2` (Nội dung gửi thử):** Bản gửi thử dùng **đúng** tài khoản gửi, nội dung và định dạng thật; tiêu đề (email) hoặc đầu tin (các kênh khác, trong giới hạn nền tảng cho phép) có tiền tố "[Gửi thử]". Thông tin cá nhân hóa lấy theo **một khách hàng mẫu** do người gửi thử chọn trong danh sách người nhận khả dụng — chỉ chọn được khách hàng thuộc tập khách hàng xem được của người gửi thử theo `BR-05.4`, và chỉ khi khách hàng đó thuộc tập xem được của **mọi** người nhận gửi thử là thành viên; nếu không, dùng giá trị thay thế. Với từng thông tin cá nhân hóa, bản gửi thử áp **mức hạn chế nhất** về phân quyền trường và che dữ liệu trong số người gửi thử và mọi người nhận gửi thử: chỉ cần một người không được xem trường đó thì dùng giá trị thay thế; chỉ cần một người chỉ thấy dạng che thì giá trị hiển thị ở dạng che (`BR-07.3`). Màn hình gửi thử nêu rõ trường nào bị thay thế hoặc che và vì người nhận nào.

  **Lý do nghiệp vụ:** Tiền tố giúp người nhận thử không nhầm bản thử với thư thật. Giới hạn dữ liệu khách hàng mẫu theo quyền của người nhận để gửi thử không trở thành đường chuyển dữ liệu khách hàng ra ngoài phạm vi được xem.

- **`BR-15.3` (Gửi thử không phải là lượt gửi chiến dịch):** Gửi thử không vào sổ cái, không tính vào số liệu báo cáo, không tính vào giới hạn tần suất, không kích hoạt dự phòng kênh, không gắn thẻ tự động, không ghi nhận vào hồ sơ khách hàng mẫu. Gửi thử **vẫn** phát sinh chi phí gửi tin, **vẫn** được tính vào hạn mức theo `billing-subscription-srs.md`, và bị chặn khi Không gian làm việc bị đình chỉ dịch vụ (`billing-subscription-srs.md`, `BR-22.2`).

  **Lý do nghiệp vụ:** Tính gửi thử vào số liệu chiến dịch sẽ làm sai tỷ lệ; nhưng gửi thử vẫn là tin thật đi qua nhà cung cấp nên chi phí và hạn mức phải được tính trung thực.

- **`BR-15.4` (Gửi thử qua kênh cần tin nhắn mẫu):** Với kênh bắt buộc tin nhắn mẫu, người nhận gửi thử phải đáp ứng điều kiện nhận tin của nền tảng (ví dụ số điện thoại có tài khoản trên nền tảng đó); nếu không, gửi thử thất bại với thông báo rõ nguyên nhân.

  **Lý do nghiệp vụ:** Báo rõ nguyên nhân để người soạn không nhầm một lỗi của địa chỉ thử với lỗi của nội dung.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-15.1.1` | Màn hình gửi thử | Mở ô chọn người nhận gửi thử | Ô chỉ cho chọn từ thành viên và Danh sách người nhận gửi thử, không cho nhập tự do; giao diện nêu rõ giới hạn `CFG-CAMP-18` |
| `AC-15.1.2` | `CFG-CAMP-18` = 5 | Chọn người nhận thứ 6 | Không chọn thêm được; giao diện hiển thị đã đạt số người nhận tối đa |
| `AC-15.1.3` | Người có quyền Quản lý danh sách người nhận gửi thử thêm vào Danh sách người nhận gửi thử một email trùng email của một hồ sơ khách hàng | Lưu | Từ chối, báo địa chỉ trùng điểm đến của khách hàng |
| `AC-15.1.4` | Người có quyền Quản lý danh sách người nhận gửi thử thêm một email ngoài tên miền nội bộ | Lưu | Địa chỉ ở trạng thái chờ chủ địa chỉ xác nhận và chờ Người phụ trách Bảo vệ Dữ liệu chấp thuận; chưa dùng được |
| `AC-15.1.5` | Chiến dịch đã gửi thử đủ `CFG-CAMP-42` lượt trong ngày | Gửi thử thêm | Từ chối, báo đã đạt số lượt gửi thử trong ngày |
| `AC-15.1.6` | Thành viên T sửa số điện thoại công việc của mình thành số của một khách hàng | T gửi thử chiến dịch SMS tới chính mình | Lượt gửi thử bỏ qua số đó, báo trùng điểm đến của khách hàng |
| `AC-15.1.7` | Thành viên T đổi số điện thoại công việc sang một số mới chưa xác minh | T gửi thử tới chính mình | Lượt gửi thử bỏ qua số đó cho tới khi T nhập đúng mã xác nhận gửi tới số mới |
| `AC-15.1.8` | Người có quyền Quản lý danh sách người nhận gửi thử khai tên miền của một dịch vụ thư công cộng làm tên miền nội bộ | Lưu | Từ chối |
| `AC-15.1.9` | Nhân viên T đồng thời là khách hàng; ngoại lệ cho số của T đã được Người phụ trách Bảo vệ Dữ liệu chấp thuận | T gửi thử chiến dịch SMS tới chính mình | T nhận bản gửi thử |
| `AC-15.2.1` | Chọn khách hàng mẫu "Nguyễn An" | Gửi thử email | Hộp thư nội bộ nhận thư tiêu đề bắt đầu "[Gửi thử]", lời chào "Chào Nguyễn An" |
| `AC-15.2.2` | Người nhận gửi thử gồm một thành viên không được xem khách hàng "Nguyễn An" | Chọn khách hàng mẫu "Nguyễn An" | Không cho phép; thông tin cá nhân hóa dùng giá trị thay thế |
| `AC-15.2.3` | Nội dung có "{điện thoại}"; một trong ba người nhận gửi thử chỉ thấy số điện thoại khách hàng ở dạng che | Gửi thử với khách hàng mẫu "Nguyễn An" | Cả ba bản gửi thử hiển thị số ở dạng che; màn hình nêu người nhận gây ra việc che |
| `AC-15.2.4` | Nội dung có "{ngày sinh}"; một người nhận gửi thử bị ẩn trường ngày sinh | Gửi thử | Mọi bản gửi thử dùng giá trị thay thế cho ngày sinh; các trường khác dùng giá trị của khách hàng mẫu |
| `AC-15.3.1` | Đã gửi thử 3 lần | Xem báo cáo chiến dịch và sổ cái | Không có bản ghi nào của lượt gửi thử; hạn mức gói dịch vụ đã trừ tương ứng |
| `AC-15.3.2` | Không gian làm việc bị đình chỉ dịch vụ | Gửi thử | Bị chặn, báo lý do đình chỉ |
| `AC-15.4.1` | Gửi thử chiến dịch Zalo ZNS tới số điện thoại nội bộ không có tài khoản Zalo | Gửi thử | Gửi thử thất bại, thông báo "Số này không có tài khoản Zalo" |

---

#### FEAT-16 — Danh mục Kiểm tra Trước Phát sóng

**Mô tả nghiệp vụ:** Trước khi gửi phê duyệt và một lần nữa ngay trước khi bắt đầu gửi, hệ thống tự kiểm tra chiến dịch và chia kết quả thành **lỗi chặn** (phải sửa) và **cảnh báo** (phải xác nhận đã xem).

**Quy tắc nghiệp vụ:**

- **`BR-16.1` (Lỗi chặn — hai nhóm):** Lỗi chặn không cho gửi phê duyệt, không cho phê duyệt và không cho bắt đầu gửi — trừ hai ngoại lệ: thiếu phê duyệt phần Tái tiếp cận (`BR-31.2`) **không chặn gửi phê duyệt**, chỉ chặn phê duyệt phát sóng và bắt đầu gửi; nội dung đang chờ nền tảng kiểm duyệt (`BR-13.5`) **không chặn gửi phê duyệt và không chặn phê duyệt**, chỉ chặn bắt đầu gửi (lỗi nhóm (b)). Lỗi chặn chia hai nhóm, xử lý khác nhau khi xuất hiện **sau** khi đã duyệt (`BR-16.4`, `BR-17.8`):
  - **(a) Lỗi chặn làm mất hiệu lực phiên bản** — lỗi nằm trong chính phiên bản đã duyệt, phải sửa phiên bản: chưa đặt tiêu chí đối tượng; chưa chọn tài khoản gửi; thiếu nội dung cho kênh chính hoặc cho một kênh dự phòng đã cấu hình; thông tin cá nhân hóa chưa chọn cách xử lý (`BR-12.2`); email thiếu tiêu đề; tin nhắn mẫu **Đã vô hiệu**, **Bị từ chối** hoặc bị nền tảng đổi sang loại không dùng được cho tiếp thị (`BR-13.2`); nội dung SMS chưa được nhà mạng nào duyệt (`BR-13.3`); lịch gửi SMS vượt hiệu lực duyệt **xét tại lúc soạn và lúc duyệt**, hoặc hiệu lực duyệt SMS đã hết trước khi chiến dịch bắt đầu gửi (kể cả khi đang Giữ lại) — phải đăng ký lại nội dung (`BR-13.4`); nội dung bị nền tảng từ chối kiểm duyệt (`BR-13.5`); mẫu tiếp thị WhatsApp/Zalo ZNS thiếu nhãn quảng cáo khi kênh đó bật nhãn (`BR-30.2`) hoặc thiếu cơ chế từ chối (`BR-26.1`); nội dung SMS hoặc Zalo OA đã được nhà mạng hay nền tảng duyệt nhưng không có nhãn quảng cáo mà chính sách đang yêu cầu — phải sửa phiên bản và đăng ký lại hoặc gửi kiểm duyệt lại (`BR-30.1`, `BR-13.3`, `BR-13.5`); liên kết trong nội dung **chắc chắn hỏng tại lúc soạn hoặc lúc duyệt** (trang đích báo không tồn tại, hoặc tên miền không tồn tại); Tái tiếp cận thiếu phê duyệt của Quản lý Marketing, không có phê duyệt của Quản lý Kinh doanh nào, hoặc đã hết hiệu lực trước khi chiến dịch bắt đầu gửi (`FEAT-31`); hạn chót gửi không còn để gửi được cho người nhận nào, xét trước khi chiến dịch bắt đầu gửi (`BR-18.4`); tiêu chí dùng một trường đã được đánh dấu nhạy cảm sau khi duyệt mà chưa đi đúng đường ngoại lệ (`BR-05.5`). **Sau khi chiến dịch đã bắt đầu gửi**, hết hiệu lực Tái tiếp cận và qua hạn chót gửi không còn là lỗi chặn mà được xử lý theo từng người nhận (lý do 2 và lý do 14 tại `BR-08.1`); nếu không còn người nào gửi được, chiến dịch chuyển Hoàn tất.
  - **(b) Lỗi chặn vận hành** — lỗi nằm ở điều kiện bên ngoài phiên bản, khắc phục được mà không sửa phiên bản: tài khoản gửi ngừng hoạt động (`BR-10.3`); tên miền gửi chưa xác thực hoặc mất xác thực (`BR-10.2`); tin nhắn mẫu đang **Tạm khóa** (`BR-13.2`); nội dung đang chờ nền tảng kiểm duyệt (`BR-13.5`); lịch dàn trải SMS bị đẩy vượt hiệu lực duyệt sau khi đã duyệt, do hạn ngạch hay lịch làm nóng thay đổi, xét trước khi chiến dịch bắt đầu gửi (`BR-13.4`) — sau khi đã bắt đầu gửi, đây không còn là lỗi chặn: các tin rơi ngoài hiệu lực được loại theo từng người nhận với lý do 11 (`BR-08.1`); ngân sách đã đặt nhưng bảng đơn giá thiếu đơn giá cho kênh đang dùng (`BR-41.1`); chi phí ước tính vượt ngân sách còn lại (`BR-41.2`); công cụ theo dõi danh tiếng của nhà cung cấp hộp thư báo tỷ lệ thư rác của tên miền gửi vượt ngưỡng (`BR-27.4`); đầu số nhận từ chối của tên thương hiệu SMS bị gỡ hoặc ngừng nhận tin (`BR-26.1`, trừ ngoại lệ cho Thông báo dịch vụ); liên kết đã kiểm tra được lúc duyệt nhưng trang đích báo lỗi tại lúc chuẩn bị gửi (phiên bản không đổi, trang đích do bên ngoài quản lý); cách xử lý hạn ngạch là "Chờ" trong khi phần khối lượng của **một ngày gửi dự kiến** vượt trần thực của một chiến dịch (`BR-40.4` — giá trị nhỏ hơn giữa `CFG-CAMP-54` và `CFG-CAMP-55` của hạn ngạch một ngày của kênh) (`BR-40.2` — khắc phục bằng nâng hạn ngạch, nâng `CFG-CAMP-54` hoặc `CFG-CAMP-55`, hoặc sửa phiên bản sang Dàn trải).

  Các điều kiện "tài khoản gửi đang hoạt động" và "có ít nhất một người nhận khả dụng" của `BR-01.2` chỉ là điều kiện **để gửi phê duyệt**; sau khi đã duyệt, tài khoản ngừng hoạt động là lỗi nhóm (b), còn số người nhận thay đổi (kể cả về 0) xử lý theo `BR-06.4`.

  **Lý do nghiệp vụ:** Đây là những lỗi chắc chắn làm chiến dịch thất bại hoặc vi phạm quy định; để người duyệt tự phát hiện là đặt toàn bộ rủi ro vào sự cẩn thận của một người vào cuối ngày làm việc.

- **`BR-16.2` (Cảnh báo):** Tối thiểu gồm: nội dung email có dấu hiệu dễ bị bộ lọc thư rác đánh giá thấp theo bộ tiêu chí chấm điểm thư rác tại Phụ lục B.2; liên kết **không kiểm tra được** (trang đích từ chối truy cập của bộ kiểm tra, quá thời gian phản hồi); liên kết được người soạn đánh dấu **"trang mở theo giờ" kèm giờ mở** — trước giờ mở mọi kết quả kiểm tra chỉ là cảnh báo; SMS vượt `CFG-CAMP-34` đơn vị tin; chưa từng gửi thử phiên bản hiện tại; tỷ lệ người bị loại vì Không tiếp cận được vượt `CFG-CAMP-35` tổng khớp tiêu chí; giờ hẹn, thời điểm phát sóng ngay hoặc khoảng gửi dự kiến theo nhịp gửi rơi vào khung giờ yên lặng của bất kỳ người nhận nào (kèm số người ước tính bị hoãn, `BR-29.4`); chi phí ước tính vượt `CFG-CAMP-36` ngân sách còn lại nhưng chưa vượt ngân sách (`FEAT-41`); chiến dịch "Chờ" không đủ chỗ hạn ngạch tại giờ hẹn vì chiến dịch khác đã giữ chỗ (`BR-40.2`); ngày dự kiến hoàn tất theo dàn trải hạn ngạch hoặc lịch làm nóng muộn hơn ngày bắt đầu (`BR-40.2`, `BR-22.5`); cách xử lý hạn ngạch là Dàn trải mà chưa đặt hạn chót gửi (`BR-18.4`); chưa khai báo đơn giá cho kênh đang dùng (khi không đặt ngân sách); tỷ lệ khiếu nại của Không gian làm việc đang vượt ngưỡng (`BR-27.3`); kênh không trả lời được mà nội dung không có thông tin liên hệ (`BR-35.4`); tệp thử nghiệm A/B nhỏ hơn cỡ mẫu khuyến nghị (`BR-38.2`).

  **Lý do nghiệp vụ:** Các dấu hiệu này chưa chắc là lỗi nên không chặn phát sóng, nhưng mỗi dấu hiệu là nguyên nhân thường gặp của thư vào mục thư rác, liên kết chết hay tin tới người nhận lúc nửa đêm; người soạn và người duyệt phải được nhìn thấy chúng trước khi quyết định.

- **`BR-16.3` (Xác nhận cảnh báo):** Người gửi phê duyệt phải đánh dấu đã xem từng cảnh báo. Người duyệt nhìn thấy danh sách cảnh báo đã được xác nhận và ai đã xác nhận.

  **Lý do nghiệp vụ:** Cảnh báo không chặn nhưng phải có người chịu trách nhiệm đã nhìn thấy nó; không có bước xác nhận, cảnh báo nhanh chóng trở thành nhiễu mà không ai đọc.

- **`BR-16.4` (Kiểm tra lại ngay trước khi gửi):** Toàn bộ lỗi chặn được kiểm tra lại tại thời điểm chiến dịch chuẩn bị chuyển sang Đang gửi. Lỗi nhóm (a) mà hệ thống biết được **trước** lúc gửi (ví dụ nền tảng báo mẫu bị vô hiệu) đưa chiến dịch Đã duyệt hoặc Giữ lại về Nháp **ngay khi biết**, kèm thông báo; kiểm tra tại lúc chuẩn bị gửi bắt các lỗi còn lại. Xuất hiện lỗi chặn **nhóm (a)** tại lúc này thì chiến dịch không gửi và về Nháp; xuất hiện lỗi chặn **nhóm (b)** thì xử lý như một kiểm tra vận hành không đạt theo `BR-17.8` (giữ Đã duyệt hoặc chuyển Giữ lại); cả hai trường hợp đều kèm thông báo. Liên kết trở thành không kiểm tra được (không phải chắc chắn hỏng) tại thời điểm này **không** chặn việc gửi; người phụ trách được thông báo. Liên kết "trang mở theo giờ" có giờ mở đã qua được kiểm tra như liên kết thường: trang đích báo không tồn tại thì là lỗi chặn nhóm (b).

  **Lý do nghiệp vụ:** Giữa lúc duyệt và lúc gửi có thể có nhiều ngày; trang đích có thể bị gỡ, mẫu có thể bị khóa. Gửi 50.000 tin trỏ tới một trang lỗi là thiệt hại không thu hồi được.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-16.1.1` | Nội dung có một liên kết tới trang không tồn tại | Mở danh mục kiểm tra | Lỗi chặn nêu đúng liên kết; nút gửi phê duyệt bị vô hiệu |
| `AC-16.1.2` | Trang đích của chương trình bán hàng chớp nhoáng trả "không tồn tại" trước 20:00; người soạn đánh dấu "trang mở theo giờ", giờ mở 20:00 | Mở danh mục kiểm tra lúc 15:00 | Chỉ là cảnh báo; gửi phê duyệt được sau khi xác nhận |
| `AC-16.1.3` | Chiến dịch có đặt ngân sách; bảng đơn giá chưa có đơn giá SMS | Mở danh mục kiểm tra chiến dịch SMS | Lỗi chặn "Thiếu đơn giá cho kênh SMS" |
| `AC-16.1.4` | Tiếp nối AC-16.1.2; chiến dịch hẹn 20:05 | Tới 20:05 trang vẫn trả "không tồn tại" | Lỗi chặn nhóm (b); chiến dịch Giữ lại, phê duyệt còn nguyên; khi trang lên, người có quyền bấm phát sóng |
| `AC-16.2.1` | Tiêu đề email viết hoa toàn bộ | Mở danh mục kiểm tra | Có cảnh báo; vẫn gửi phê duyệt được sau khi đánh dấu đã xem |
| `AC-16.3.1` | Người soạn đã xác nhận 2 cảnh báo | Người duyệt mở chiến dịch | Thấy 2 cảnh báo, người xác nhận và thời điểm xác nhận |
| `AC-16.4.1` | Chiến dịch Đã duyệt hẹn thứ Sáu; trang đích bị gỡ vào thứ Năm | Tới giờ hẹn | Chiến dịch không gửi, chuyển Giữ lại, thông báo nêu liên kết lỗi; nếu cần đổi liên kết thì phải sửa phiên bản và duyệt lại |

---

### Nhóm F — Phê duyệt & Điều phối Phát sóng

#### FEAT-17 — Phê duyệt Kép & Phát sóng

**Mô tả nghiệp vụ:** Tách người soạn khỏi người quyết định phát sóng, để mọi chiến dịch được ít nhất hai người nhìn thấy trước khi tới tay khách hàng.

**Quy tắc nghiệp vụ:**

- **`BR-17.1` (Quyền Phát sóng chiến dịch):** Phân hệ khai báo **(Chiến dịch, Phát sóng)** là thao tác đặc thù của loại dữ liệu Chiến dịch trong ma trận quyền của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) (`FEAT-24`). Ô này mang một mức truy cập như mọi ô khác (Không có / Chỉ của mình / Đơn vị của mình / Đơn vị và các đơn vị con / Toàn workspace — Mục 1.4 của tài liệu phân quyền) và áp trên chính chiến dịch: người có ô này bao phủ chiến dịch mới được phê duyệt, phát sóng ngay, chốt hoặc dời giờ hẹn, phát sóng hoặc xác nhận quy mô từ Giữ lại, tạm dừng, tiếp tục, hủy, gửi lại thủ công, đặt ngân sách, duyệt miễn trừ tần suất, bắt đầu và dừng chuỗi nuôi dưỡng. Các ô Tạo, Sửa trên Chiến dịch không bao hàm ô này. Ở chế độ Cơ bản của ma trận quyền, (Chiến dịch, Phát sóng) **luôn hiển thị thành một dòng riêng** dưới loại Chiến dịch, không thuộc mức đặt sẵn nào và không bị chọn mức đặt sẵn nới theo ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.4`). Ô mới mặc định Không có ở mọi vai trò tự tạo ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-24.2`). Giá trị mặc định cho vai trò dựng sẵn chỉ có tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`: **Quản lý Marketing** = Đơn vị của mình; **Marketing** = Không có — trừ khi doanh nghiệp đặt = Chỉ của mình qua [`onboarding-srs.md`](./onboarding-srs.md) `FEAT-12` hoặc qua điều chỉnh ô ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02`); các vai trò dựng sẵn khác = Không có. Người có toàn quyền có ô này ở mức Toàn workspace. Mức Xem của ô (Chiến dịch, Xem) hẹp hơn mức Phát sóng thì Phát sóng thu theo mức Xem ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.1`).

  **Lý do nghiệp vụ:** Soạn một chiến dịch là việc hằng ngày của nhiều người; quyết định đưa thông điệp tới hàng chục nghìn khách hàng — và chịu trách nhiệm về chi phí, danh tiếng — là việc của một số ít người được giao trách nhiệm.

- **`BR-17.2` (Phê duyệt kép):** Khi `CFG-CAMP-05` bật, người phê duyệt phải khác **người gửi phê duyệt** và khác **mọi người đã tạo ra bất kỳ thay đổi nào của phiên bản đang được duyệt** thuộc danh sách các phần của phiên bản tại `BR-17.4`. Nếu người có quyền Phát sóng chiến dịch tự soạn chiến dịch, họ vẫn phải gửi phê duyệt để một người khác duyệt. Khi `CFG-CAMP-05` tắt, người có quyền Phát sóng chiến dịch được phát sóng chiến dịch do chính mình soạn, và thao tác này được ghi nhật ký rõ là "phát sóng không qua phê duyệt kép".

  **Lý do nghiệp vụ:** Chỉ yêu cầu "người duyệt khác người tạo" là chưa đủ: người duyệt có thể tự sửa nội dung rồi tự duyệt chính phần mình vừa sửa. Doanh nghiệp nhỏ chỉ có một người làm tiếp thị không thể có hai người, nên được tắt — nhưng việc tắt phải là quyết định có ý thức của Chủ sở hữu (Phụ lục B).

- **`BR-17.3` (Duyệt đúng phiên bản):** Người duyệt thấy phiên bản đầy đủ: nội dung từng kênh (gồm kênh dự phòng và mọi phiên bản A/B), bản xem trên máy tính và điện thoại, tiêu chí đối tượng, số người nhận khả dụng và phân tích loại trừ tại thời điểm xem, tài khoản gửi, giờ hẹn, cách xử lý khi vượt hạn ngạch ngày (`BR-40.2`), miễn trừ tần suất nếu có, chi phí ước tính của kênh chính và chi phí dự phòng tối đa (`BR-41.3`), cảnh báo đã xác nhận; với Thông báo dịch vụ, thêm mục đích, văn bản pháp luật dẫn chiếu, lý do chọn tập đối tượng và xác nhận gửi khẩn cấp (`FEAT-45`). Phê duyệt gắn với đúng phiên bản đó; nếu phiên bản đã thay đổi trong lúc người duyệt đang xem, thao tác phê duyệt bị từ chối.

  **Lý do nghiệp vụ:** Người duyệt chỉ chịu trách nhiệm được về cái mình đã thấy; duyệt một bản tóm tắt, hay một phiên bản đã bị sửa sau lưng, là duyệt hình thức.

- **`BR-17.4` (Mọi thay đổi sau phê duyệt đều phải duyệt lại):** Từ khi danh sách đã chốt (Đang gửi trở về sau), đổi người phụ trách chỉ được ghi nhật ký, không tạo phiên bản mới (`BR-01.6`); các phần khác chỉ sửa được theo `BR-19.3`. Các phần của phiên bản duyệt gồm: nội dung (mọi kênh, mọi phiên bản A/B), đối tượng (tiêu chí, tập loại trừ và đánh dấu "xét lại tại thời điểm gửi", Tái tiếp cận, người chọn đối tượng), tài khoản gửi, cấu hình dự phòng và kênh thay thế, cách xử lý hạn ngạch, giờ hẹn, hạn chót gửi, nhịp gửi (khi tăng), lựa chọn tự phát sóng khi điều kiện vận hành được khắc phục, miễn trừ tần suất, phương án khi thử nghiệm A/B hòa, mức giảm và mức tăng quy mô chấp nhận được; với Thông báo dịch vụ, thêm mục đích, văn bản pháp luật dẫn chiếu, lý do chọn tập đối tượng và xác nhận gửi khẩn cấp (`FEAT-45`). Bất kỳ thay đổi nào tới một phần trong danh sách này khi chiến dịch ở **Chờ duyệt, Đã duyệt hoặc Giữ lại** tạo phiên bản mới và đưa chiến dịch về Nháp (`BR-01.6`). Các ngoại lệ: sửa trong lúc tạm dừng theo `BR-19.3`; dời giờ hẹn muộn hơn trong cửa sổ `CFG-CAMP-31`, hoặc một lần từ Giữ lại, theo `BR-18.2`; **giảm** nhịp gửi theo `BR-22.1`; người chọn đối tượng đổi do bàn giao khi rời workspace (`BR-01.8`) — quy mô được xác nhận lại tại lúc chốt theo `BR-06.4`. Người phụ trách và người khởi chạy không thuộc phiên bản duyệt: đổi họ không tạo phiên bản mới (`BR-01.3`, `BR-17.11`).

  **Lý do nghiệp vụ:** Nguyên tắc 2 (Mục 2.4). Nếu có một loại thay đổi nhỏ được miễn duyệt lại, người dùng sẽ học cách luồn thay đổi lớn qua đúng lối đó.

- **`BR-17.5` (Từ chối phê duyệt):** Người duyệt từ chối phải nhập lý do (không để trống). Chiến dịch về Nháp; người gửi phê duyệt được thông báo kèm lý do. Người gửi phê duyệt có thể **rút lại** yêu cầu khi đang Chờ duyệt.

  **Lý do nghiệp vụ:** Lý do từ chối là thông tin duy nhất giúp người soạn sửa đúng chỗ; từ chối không lý do tạo vòng gửi – từ chối lặp lại vô ích.

- **`BR-17.6` (Lịch sử phê duyệt):** Mọi lượt gửi phê duyệt, phê duyệt, từ chối, rút lại được ghi vào lịch sử phê duyệt của chiến dịch: ai, lúc nào, phiên bản nào, lý do. Lịch sử này không sửa, không xóa được, và vẫn giữ khi chiến dịch được lưu trữ.

  **Lý do nghiệp vụ:** Khi một chiến dịch gây sự cố, câu hỏi đầu tiên luôn là "ai đã duyệt phiên bản nào"; lịch sử sửa được thì không trả lời được câu hỏi đó.

- **`BR-17.7` (Thông báo & chỉ định người duyệt):** Người gửi phê duyệt có thể chỉ định một hoặc nhiều người duyệt; nếu chỉ định, chỉ những người đó nhận thông báo, nhưng bất kỳ người có quyền hợp lệ nào vẫn duyệt được. Nếu không chỉ định, mọi người có quyền Phát sóng chiến dịch trên chiến dịch đó (trừ những người bị loại theo `BR-17.2`) nhận thông báo kèm liên kết mở màn hình duyệt. Với Chiến dịch Tái tiếp cận, Quản lý Marketing và các Quản lý Kinh doanh phụ trách tập khách cũng nhận thông báo cho phần Tái tiếp cận (`BR-31.2`). Phê duyệt hoặc từ chối thì người gửi phê duyệt và người phụ trách nhận thông báo. Người soạn và người duyệt để lại được bình luận trên từng phiên bản; bình luận lưu cùng lịch sử phê duyệt.

  **Lý do nghiệp vụ:** Thông báo cho mọi người có quyền trong một đội lớn khiến không ai thấy mình là người phải làm; chỉ định người duyệt giao trách nhiệm rõ ràng mà không tạo điểm nghẽn khi người đó vắng mặt.

- **`BR-17.8` (Phát sóng ngay và chuỗi kiểm tra trước khi bắt đầu):** Người có quyền Phát sóng chiến dịch bấm "Phát sóng ngay" trên chiến dịch Đã duyệt không có giờ hẹn. Trước khi chuyển sang Đang gửi, hệ thống thực hiện theo thứ tự:
  1. Lỗi chặn nhóm (a) (`BR-16.1`) — **kiểm tra làm mất hiệu lực phiên bản**.
  2. Chênh lệch đối tượng quá ngưỡng chưa được xác nhận (`BR-06.4`); lỗi chặn nhóm (b) (`BR-16.1`); Không gian làm việc không đang dừng khẩn cấp (`FEAT-42`), thời điểm gửi không thuộc ngày không gửi (`FEAT-43`), Không gian làm việc không bị đình chỉ dịch vụ (`billing-subscription-srs.md`, `BR-22.2`); người khởi chạy Đang hoạt động và còn ô (Chiến dịch, Phát sóng) bao phủ chiến dịch (`BR-17.10`).
  3. Đối chiếu hạn mức của gói dịch vụ cho **toàn bộ khối lượng dự kiến của lô** theo (`billing-subscription-srs.md`, `BR-16.9`). Không đủ thì lô không bắt đầu; Chủ sở hữu hoặc Người phụ trách thanh toán có thể **nâng trần lên một mức mới cụ thể** theo quy trình của (`billing-subscription-srs.md`, `BR-16.5b`), sau đó phát sóng lại được. Không tra được hạn mức thì hoãn, không gửi "mù" (`billing-subscription-srs.md`, `BR-32.3`).
  4. Đối chiếu hạn ngạch ngày theo cách xử lý đã duyệt (`BR-40.2`) và ngân sách chiến dịch: chi phí ước tính của kênh chính vượt ngân sách còn lại thì không bắt đầu (`BR-41.2`); chi phí dự phòng tối đa không chặn bắt đầu — con số này hiển thị trên màn hình duyệt và hộp xác nhận phát sóng; khi gửi, nếu các lượt dự phòng làm chi phí đã cam kết chạm ngân sách, chiến dịch tự động tạm dừng bảo vệ (`BR-20.1` tình huống 6) và có thể chỉ gửi được một phần — người đặt ngân sách muốn tránh điều này thì đặt ngân sách bao gồm cả chi phí dự phòng tối đa.
  5. Chốt danh sách người nhận (`BR-06.3`).

  Bước 1 không đạt thì chiến dịch về Nháp. Bước 2–4 không đạt là **kiểm tra vận hành**: chiến dịch giữ nguyên Đã duyệt (phát sóng ngay) hoặc chuyển Giữ lại (tới giờ hẹn, `BR-18.3`), và người thao tác được báo rõ bước nào không đạt, ai xử lý được.

  **Lý do nghiệp vụ:** Hạn mức được đối chiếu cho cả lô trước khi bắt đầu, vì một chiến dịch dừng giữa chừng do hết hạn mức khiến một nửa khách hàng nhận ưu đãi còn nửa kia không — tệ hơn là không gửi. Tách kiểm tra làm mất hiệu lực phiên bản khỏi kiểm tra vận hành để một trục trặc hạn mức lúc nửa đêm không xóa mất phê duyệt của một chiến dịch hoàn toàn đúng.

- **`BR-17.9` (Tự duyệt khi tắt phê duyệt kép):** Khi `CFG-CAMP-05` tắt, người có quyền Phát sóng chiến dịch dùng thao tác **"Xác nhận phiên bản"** để chuyển chiến dịch Nháp của chính mình sang Đã duyệt — hoặc, với Chiến dịch Tái tiếp cận, chuyển từ Chờ duyệt sang Đã duyệt sau khi phần Tái tiếp cận đạt điều kiện tối thiểu của `BR-31.2` (có phê duyệt của Quản lý Marketing và của ít nhất một Quản lý Kinh doanh); thao tác này chỉ thực hiện được khi đủ điều kiện `BR-01.2` và danh mục kiểm tra không còn lỗi chặn, người thao tác vẫn phải xác nhận từng cảnh báo (`BR-16.3`), và được ghi vào lịch sử phê duyệt là "tự xác nhận — phê duyệt kép đang tắt"; Người phụ trách Bảo vệ Dữ liệu nhận thông báo mỗi lần phê duyệt kép bị tắt và mỗi lần một miễn trừ tần suất được tự xác nhận. Cùng thao tác này áp cho phiên bản mới khi sửa trong lúc tạm dừng (`BR-19.3`) và cho phiên bản mới của chuỗi nuôi dưỡng (`BR-39.5`).

  **Lý do nghiệp vụ:** Tắt phê duyệt kép bỏ người thứ hai, không bỏ các kiểm tra; nếu không có thao tác tường minh, chiến dịch sẽ kẹt ở trạng thái chờ duyệt mà không ai duyệt được.

- **`BR-17.10` (Người khởi chạy và tiến trình gửi chạy thay):** Mỗi lượt gửi — lượt gửi chính, lượt gửi lại thủ công, chuỗi nuôi dưỡng, phiên bản thắng của thử nghiệm A/B, lượt dự phòng — là **tiến trình chạy thay người khởi chạy** (Mục 1.4) theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`. Người khởi chạy là người thực hiện thao tác bắt đầu hoặc lên lịch lượt gửi: với chiến dịch hẹn giờ là người chốt hoặc dời giờ hẹn gần nhất; với lượt gửi từ Giữ lại là người phát sóng hoặc xác nhận quy mô; lượt dự phòng, phiên bản thắng và lượt thử lại tự động thuộc người khởi chạy của lượt gửi chính. Hệ thống ghi nhận người khởi chạy, thời điểm và mức của họ lúc khởi chạy trên hai ô (Khách hàng, Xem) và (Chiến dịch, Phát sóng). Trong suốt lượt gửi:
  - (a) Mỗi tin chỉ được gửi tới khách hàng thuộc **tập khách hàng xem được** của người khởi chạy theo `BR-05.4` (bước 1–3 của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`, không tính lượt cấp trên bản ghi và quyền tạm thời), với mức theo ô là **phần giao** của mức (Khách hàng, Xem) lúc khởi chạy và mức hiện tại, nguồn nới chốt tại lúc chốt danh sách (với chuỗi: lúc phiên bản đang chạy được duyệt) và không mở rộng sau đó, còn nguồn chặn tính tại thời điểm gửi (`BR-05.4`); khách hàng ngoài tập này bị loại tại thời điểm gửi với lý do 18 (`BR-08.1`). Với chuỗi nuôi dưỡng, người mới vào chuỗi còn phải thuộc tập xem được của người chọn đối tượng theo `BR-05.4` (e).
  - (b) Nếu người khởi chạy không còn ô (Chiến dịch, Phát sóng) bao phủ chiến dịch (vai trò bị thu hẹp, chiến dịch đổi sang đơn vị ngoài mức của họ), lượt gửi chuyển Tạm dừng với lý do "Người khởi chạy không còn quyền Phát sóng chiến dịch này"; chiến dịch Đã duyệt có giờ hẹn thì tới giờ chuyển Giữ lại (`BR-17.8` bước 2). Người phụ trách chiến dịch, người khởi chạy, quản lý trực tiếp của người khởi chạy và Người có toàn quyền được thông báo ngay, kèm lựa chọn chuyển người khởi chạy hoặc dừng hẳn (`BR-17.11` (c), (d)). Màn hình đổi người phụ trách cảnh báo trước khi lưu nếu lần đổi gây ra tình huống này.
  - (c) Tiến trình không bao giờ dùng quyền của người khác — kể cả của người phụ trách, người duyệt hay Người có toàn quyền — để gửi tới khách hàng ngoài quyền của người khởi chạy.

  **Lý do nghiệp vụ:** Một chiến dịch dàn trải nhiều ngày hay một chuỗi chạy nhiều tháng không được là đường vòng để gửi tới khách hàng mà người khởi chạy đã bị thu quyền xem — ví dụ sau khi họ chuyển phòng hoặc doanh nghiệp chọn thu Marketing về khách hàng của đơn vị mình. Lấy phần giao với mức lúc khởi chạy để việc được nới quyền giữa chừng cũng không tự mở rộng một lượt gửi đã được duyệt cho tập nhỏ hơn.

- **`BR-17.11` (Người khởi chạy bị tạm ngưng hoặc rời workspace):** Áp [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`, `FEAT-42`, `FEAT-43`:
  - (a) **Tạm ngưng:** ngay khi người khởi chạy bị tạm ngưng — kể cả tự tạm ngưng vì không hoạt động và lượt "Tạm ngưng ngay" của quy trình rời workspace — mọi chiến dịch Đang gửi, lượt gửi lại và chuỗi nuôi dưỡng chưa Kết thúc do họ khởi chạy chuyển Tạm dừng với lý do "Người khởi chạy bị tạm ngưng"; chiến dịch Đã duyệt có giờ hẹn do họ chốt giữ nguyên trạng thái nhưng tới giờ chuyển Giữ lại (`BR-17.8` bước 2). Người quản lý trực tiếp của người khởi chạy, Người có toàn quyền và người phụ trách chiến dịch được thông báo ngay, kèm hai lựa chọn: **chuyển người khởi chạy** hoặc **dừng hẳn**. Việc tạm ngưng thành viên không chờ và không bị chặn bởi bước này.
  - (b) **Kích hoạt lại** người khởi chạy **không** tự chạy tiếp: chính người đó hoặc một Người có toàn quyền chọn tiếp tục, qua đủ kiểm tra của `BR-19.2`.
  - (c) **Chuyển người khởi chạy:** do Người có toàn quyền thực hiện, hoặc do một người có ô (Chiến dịch, Phát sóng) bao phủ chiến dịch tự nhận làm người khởi chạy. Quản lý trực tiếp của người khởi chạy luôn được thông báo (a); nếu họ không có ô Phát sóng bao phủ chiến dịch thì không tự nhận được, và việc chuyển do Người có toàn quyền hoặc một người có ô Phát sóng bao phủ chiến dịch thực hiện; người được chuyển tới phải Đang hoạt động và có ô (Chiến dịch, Phát sóng) bao phủ chiến dịch. Từ thời điểm chuyển, `BR-17.10` (a) tính theo phần giao mức lúc nhận và mức hiện tại của người mới; tin đã gửi không bị ảnh hưởng, danh sách đã chốt không thêm người. Chuyển người khởi chạy không tạo phiên bản mới và không tự tiếp tục lượt gửi đang tạm dừng — tiếp tục là thao tác riêng theo `BR-19.2`. Lượt chuyển được ghi nhật ký (`NFR-08`).
  - (d) **Dừng hẳn:** chiến dịch Đang gửi hoặc Tạm dừng thì hủy theo `BR-21.1`; chiến dịch Đã duyệt có giờ hẹn hoặc Giữ lại thì hủy lịch về Nháp (`BR-18.2`); chuỗi nuôi dưỡng thì hủy chuỗi (`BR-39.1`); lượt gửi lại thì hủy lượt đó.
  - (e) **Rời workspace:** ngay khi quy trình rời workspace được mở cho người khởi chạy — dù lựa chọn "Tạm ngưng ngay" có được giữ hay không — mọi lượt gửi đang chạy của họ chuyển Tạm dừng và lịch hẹn của họ tới giờ chuyển Giữ lại như (a). Mỗi lượt gửi do người rời đi khởi chạy và chưa kết thúc — gồm chiến dịch Đã duyệt có giờ hẹn, Giữ lại, Đang gửi, Tạm dừng, lượt gửi lại, chuỗi chưa Kết thúc — là một mục của bước bắt buộc **"tiến trình đang chạy"** trong danh sách bàn giao của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-43`; mỗi mục phải được chuyển người khởi chạy theo (c) hoặc dừng hẳn theo (d) trước khi gỡ được thành viên.
  - (f) Tạm dừng, hủy, hủy lịch theo quy tắc này là thao tác dừng, không bị chặn khi nhật ký gặp sự cố (Nguyên tắc 7); chuyển người khởi chạy và tiếp tục là thao tác làm tiếp tục việc gửi, chỉ thực hiện được khi ghi được nhật ký.

  **Lý do nghiệp vụ:** Chiến dịch tiếp tục gửi dưới tên một người đang bị điều tra hoặc đã nghỉ việc là gửi thông điệp mà không ai đang chịu trách nhiệm; nhưng tự hủy thì làm hỏng một chiến dịch hoàn toàn đúng chỉ vì người bấm nút nghỉ phép. Dừng lại và giao cho người có thẩm quyền quyết định giữ được cả hai.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-17.1.1` | Nhân viên Marketing không có quyền Phát sóng chiến dịch | Mở chiến dịch Đã duyệt | Không có nút phát sóng, tạm dừng, hủy |
| `AC-17.2.1` | `CFG-CAMP-05` bật; Quản lý Marketing M tự soạn chiến dịch | M tìm nút phê duyệt trên chiến dịch của mình | Không có; M chỉ gửi phê duyệt được |
| `AC-17.2.2` | A gửi phê duyệt; M sửa một câu (chiến dịch về Nháp) rồi gửi phê duyệt lại | M mở màn hình duyệt | Không có nút phê duyệt cho M — M đã sửa phiên bản này; M2 duyệt được |
| `AC-17.2.3` | `CFG-CAMP-05` tắt; M tự soạn | M phát sóng ngay | Chiến dịch gửi đi; nhật ký ghi "phát sóng không qua phê duyệt kép" |
| `AC-17.2.4` | A gửi phê duyệt; M bật miễn trừ tần suất (chiến dịch về Nháp) rồi gửi phê duyệt lại | M mở màn hình duyệt | Không có nút phê duyệt cho M |
| `AC-17.3.1` | Người duyệt đang mở màn hình duyệt; người soạn sửa và lưu nội dung | Người duyệt bấm phê duyệt | Từ chối, báo phiên bản đã thay đổi, yêu cầu tải lại |
| `AC-17.4.1` | Chiến dịch Đã duyệt | Người soạn bỏ cấu hình dự phòng SMS và lưu | Chiến dịch về Nháp; phải duyệt lại |
| `AC-17.4.2` | Chiến dịch Đã duyệt | Người duyệt bật miễn trừ tần suất sau khi đã duyệt | Chiến dịch về Nháp; miễn trừ chỉ có hiệu lực khi phiên bản mới được duyệt |
| `AC-17.4.3` | Chiến dịch Đã duyệt | Người có quyền nới hạn chót gửi hoặc bật tự phát sóng | Chiến dịch về Nháp; cần duyệt lại |
| `AC-17.4.4` | Chiến dịch Đang gửi dàn trải 6 ngày; người phụ trách A nghỉ phép | Quản lý đổi người phụ trách sang B | Chiến dịch vẫn Đang gửi; B nhận các thông báo từ đó; nhật ký ghi lần đổi |
| `AC-17.4.5` | Chiến dịch Lưu trữ | Đổi người phụ trách | Chiến dịch vẫn Lưu trữ; nhật ký ghi lần đổi |
| `AC-17.5.1` | Chiến dịch Chờ duyệt | Người duyệt bấm Từ chối không nhập lý do | Không cho phép; lý do bắt buộc |
| `AC-17.5.2` | Người duyệt từ chối với lý do "Sai mã giảm giá" | Người soạn mở thông báo | Chiến dịch ở Nháp, thấy lý do "Sai mã giảm giá" |
| `AC-17.6.1` | Chiến dịch bị từ chối một lần, duyệt lần hai | Mở lịch sử phê duyệt | Có đủ 4 bản ghi (gửi duyệt, từ chối, gửi duyệt, phê duyệt) với người, thời điểm, phiên bản |
| `AC-17.7.1` | A gửi phê duyệt; M và M2 có quyền Phát sóng chiến dịch, M đã sửa phiên bản này | Kiểm tra thông báo | M2 nhận thông báo kèm liên kết mở màn hình duyệt; M không nhận |
| `AC-17.7.2` | M2 phê duyệt | Kiểm tra thông báo | A và người phụ trách nhận thông báo |
| `AC-17.7.3` | A gửi phê duyệt, chỉ định M2 | Kiểm tra thông báo | Chỉ M2 nhận thông báo; một người có quyền hợp lệ khác vẫn duyệt được nếu mở chiến dịch |
| `AC-17.7.4` | M2 để lại bình luận "Đổi ảnh bìa" trên phiên bản 1 | A mở lịch sử phê duyệt | Thấy bình luận gắn với phiên bản 1 |
| `AC-17.8.1` | Hạn mức gói còn 4.000 tin; chiến dịch có 10.000 người khả dụng | Phát sóng ngay | Không bắt đầu gửi tin nào; thông báo nêu hạn mức đang chạm và người có thẩm quyền xử lý |
| `AC-17.8.2` | Không gian làm việc đang bị đình chỉ dịch vụ | Phát sóng ngay | Không cho phép, báo lý do đình chỉ |
| `AC-17.8.3` | Tiếp nối AC-17.8.1; Người phụ trách thanh toán nâng trần hạn mức lên một mức mới cụ thể đủ cho lô theo quy trình tính phí | M phát sóng lại | Chiến dịch bắt đầu gửi |
| `AC-17.8.4` | Chiến dịch đặt ngân sách 5.000.000 đồng, chi phí ước tính 8.000.000 đồng | Phát sóng ngay | Không bắt đầu; chiến dịch vẫn Đã duyệt; thông báo nêu ngân sách không đủ |
| `AC-17.9.1` | `CFG-CAMP-05` tắt; M soạn chiến dịch còn một lỗi chặn | M bấm "Xác nhận phiên bản" | Không cho phép cho tới khi hết lỗi chặn |
| `AC-17.9.2` | `CFG-CAMP-05` tắt; chiến dịch của M không còn lỗi chặn, có 2 cảnh báo | M xác nhận 2 cảnh báo và bấm "Xác nhận phiên bản" | Chiến dịch Đã duyệt; lịch sử phê duyệt ghi "tự xác nhận — phê duyệt kép đang tắt" |
| `AC-17.1.2` | Người có quyền Quản lý vai trò mở vai trò tự tạo ở chế độ Cơ bản và chọn mức đặt sẵn "Toàn bộ dữ liệu" cho Chiến dịch | Lưu | Dòng "Phát sóng" hiển thị riêng, vẫn là Không có |
| `AC-17.1.3` | Doanh nghiệp trả lời Có cho câu "Nhân viên Marketing được phát sóng chiến dịch của mình không?" khi khởi tạo | Nhân viên Marketing mở chiến dịch Đã duyệt của mình, rồi của đồng nghiệp cùng đơn vị | Có nút phát sóng trên chiến dịch của mình; không có trên chiến dịch của đồng nghiệp |
| `AC-17.1.4` | Vai trò có (Chiến dịch, Phát sóng) = Đơn vị của mình, (Chiến dịch, Xem) bị điều chỉnh về Chỉ của mình | Mở vai trò | Dòng Phát sóng đã thu về Chỉ của mình |
| `AC-17.10.1` | G khởi chạy chiến dịch với (Khách hàng, Xem) = Toàn workspace; giữa lúc gửi G bị thu hẹp về Đơn vị của mình | Chiến dịch tiếp tục gửi | Chỉ gửi tới khách hàng trong đơn vị của G; khách đơn vị khác ghi "Ngoài quyền của người khởi chạy" |
| `AC-17.10.2` | Chiến dịch Đang gửi do M (Phát sóng = Đơn vị của mình, đơn vị A) khởi chạy | Người có quyền Gán đổi người phụ trách sang người thuộc đơn vị B | Trước khi lưu: cảnh báo chiến dịch sẽ tạm dừng; sau khi lưu: chiến dịch Tạm dừng với lý do "Người khởi chạy không còn quyền Phát sóng chiến dịch này" |
| `AC-17.10.3` | M chốt giờ hẹn 20:00 khi có (Khách hàng, Xem) = Đơn vị của mình; 19:00 M được nới thành Toàn workspace | Tới 20:00 | Danh sách chốt vẫn chỉ trong đơn vị của M (phần giao với mức lúc khởi chạy) |
| `AC-17.10.4` | M2 (có quyền Phát sóng) phát sóng ngay một chiến dịch do M duyệt | Kiểm tra nhật ký | Người khởi chạy ghi là M2 kèm mức (Khách hàng, Xem) và (Chiến dịch, Phát sóng) của M2 tại lúc phát sóng |
| `AC-17.11.1` | Chiến dịch Đang gửi và một chuỗi Đang chạy do G khởi chạy | G bị tạm ngưng | Cả hai chuyển Tạm dừng, lý do "Người khởi chạy bị tạm ngưng"; quản lý trực tiếp của G, Người có toàn quyền và người phụ trách nhận thông báo kèm lựa chọn chuyển người khởi chạy hoặc dừng hẳn |
| `AC-17.11.2` | Chiến dịch Đã duyệt hẹn 20:00 do G chốt giờ; G bị tạm ngưng lúc 15:00 | Tới 20:00 | Chiến dịch không gửi, chuyển Giữ lại với lý do người khởi chạy không Đang hoạt động; thông báo đã gửi từ 15:00 |
| `AC-17.11.3` | Tiếp nối AC-17.11.1; G được kích hoạt lại | Chờ | Chiến dịch và chuỗi vẫn Tạm dừng; G hoặc Người có toàn quyền tiếp tục được |
| `AC-17.11.4` | Tiếp nối AC-17.11.1; M2 có (Chiến dịch, Phát sóng) bao phủ chiến dịch | M2 chọn "Nhận làm người khởi chạy", rồi Tiếp tục | Người khởi chạy là M2, nhật ký ghi lượt chuyển; chiến dịch tiếp tục, mỗi tin xét theo quyền của M2; không tạo phiên bản mới |
| `AC-17.11.5` | Tiếp nối AC-17.11.1; người được chọn làm người khởi chạy mới có (Chiến dịch, Phát sóng) = Không có | Người có toàn quyền chọn người đó | Không chọn được, kèm lý do |
| `AC-17.11.6` | G là người khởi chạy của 1 chiến dịch Đang gửi và 1 chiến dịch hẹn giờ | Mở Rời workspace cho G | Có bước "2 tiến trình đang chạy" bắt buộc chuyển người khởi chạy hoặc dừng hẳn; nút Gỡ bị vô hiệu tới khi hoàn tất |
| `AC-17.11.7` | Nhật ký kiểm toán đang gặp sự cố | G bị tạm ngưng khi đang là người khởi chạy của một chiến dịch Đang gửi | Chiến dịch vẫn Tạm dừng ngay; sự kiện được ghi bù khi nhật ký phục hồi, gắn cờ "ghi bù" |
| `AC-17.11.9` | Quy trình rời workspace được mở cho G, người thực hiện bỏ chọn "Tạm ngưng ngay"; G là người khởi chạy của một chiến dịch Đang gửi | Mở quy trình | Chiến dịch chuyển Tạm dừng ngay; G vẫn đăng nhập được trong lúc bàn giao |
| `AC-17.10.5` | Chiến dịch Đang gửi do M khởi chạy; M mất ô (Chiến dịch, Phát sóng) | Hệ thống xử lý | Chiến dịch Tạm dừng; người phụ trách, M, quản lý trực tiếp của M và Người có toàn quyền nhận thông báo kèm lựa chọn chuyển người khởi chạy hoặc dừng hẳn |
| `AC-17.10.6` | Chiến dịch Đang gửi do M khởi chạy; sau khi khởi chạy, doanh nghiệp tạo chính sách Từ chối chặn M đọc nhóm khách "Đang tranh chấp" | Tới lượt một khách thuộc nhóm đó | Không gửi; lý do "Ngoài quyền của người khởi chạy" |
| `AC-17.11.8` | Tiếp nối AC-17.11.6; chọn Dừng hẳn cho chiến dịch hẹn giờ | Xác nhận | Chiến dịch về Nháp, giờ hẹn bị gỡ; bước đánh dấu hoàn tất cho mục đó |

---

#### FEAT-18 — Hẹn giờ Phát sóng

**Mô tả nghiệp vụ:** Đặt chiến dịch tự động bắt đầu gửi tại một thời điểm trong tương lai.

**Quy tắc nghiệp vụ:**

- **`BR-18.1` (Đặt giờ hẹn):** Giờ hẹn là một mốc thời điểm cụ thể (Mục 2.3), phải cách thời điểm đặt ít nhất `CFG-CAMP-29` và không xa hơn `CFG-CAMP-30`. Màn hình hiển thị giờ hẹn kèm tên múi giờ, đồng thời hiển thị giờ đó quy đổi theo múi giờ Không gian làm việc nếu khác múi giờ người đặt. Bộ chọn giờ không cho chọn thời điểm ngoài khoảng hợp lệ.

  **Lý do nghiệp vụ:** Khoảng tối thiểu để có thời gian chạy toàn bộ kiểm tra trước khi gửi; hiển thị múi giờ vì "9 giờ sáng" của người soạn ở chi nhánh khác có thể là 13 giờ của Không gian làm việc.

- **`BR-18.2` (Giờ hẹn là một phần của phiên bản duyệt):** Người soạn **đề xuất** giờ hẹn trước khi gửi phê duyệt; người duyệt chốt giờ hẹn khi phê duyệt: giữ nguyên, hoặc dời **muộn hơn** giờ đề xuất (phiên bản vẫn do người duyệt chịu trách nhiệm, không cần duyệt lại); muốn gửi **sớm hơn** giờ đề xuất thì người duyệt từ chối để người soạn đề xuất lại. Sau khi đã duyệt, người có quyền Phát sóng chiến dịch được **dời giờ hẹn muộn hơn** tối đa `CFG-CAMP-31` so với giờ đã duyệt mà không cần duyệt lại, với điều kiện giờ mới không vượt thời hạn hiệu lực Tái tiếp cận (nếu có) và hiệu lực duyệt nội dung SMS của nhà mạng (`BR-13.4`, nếu có); mọi lần dời được ghi nhật ký. Chiến dịch đang **Giữ lại** cũng được dời giờ hẹn muộn hơn **một lần**, tới một giờ mới không muộn hơn thời điểm hết ân hạn `CFG-CAMP-32` tính từ lần vào Giữ lại đầu tiên, trước hạn chót gửi và trong thời hạn hiệu lực Tái tiếp cận (nếu có) (không bị giới hạn bởi `CFG-CAMP-31`), không cần duyệt lại; chiến dịch trở về Đã duyệt với giờ hẹn mới. Nếu tới giờ mới lại vào Giữ lại, ân hạn không được tính lại từ đầu. Với chiến dịch Đã duyệt, được dời muộn nhiều lần miễn tổng mức dời so với giờ đã duyệt không quá `CFG-CAMP-31`. Dời sớm hơn, dời quá cửa sổ này, hoặc dời thêm sau khi đã dùng lượt dời một lần từ Giữ lại là thay đổi cần duyệt lại (`BR-17.4`). **Hủy lịch** đưa chiến dịch về Nháp.

  **Lý do nghiệp vụ:** Thời điểm gửi là một phần của quyết định tiếp thị (ví dụ không gửi khuyến mãi trước giờ mở bán); dời sớm có thể đưa ưu đãi tới trước khi hệ thống bán hàng sẵn sàng. Dời muộn một chút (kho chưa kịp, trang đích chưa lên) là tình huống vận hành thường gặp, rủi ro thấp; bắt duyệt lại từ đầu cho tình huống đó sẽ đẩy người dùng tìm cách lách quy trình.

- **`BR-18.3` (Khi tới giờ hẹn):** Tới giờ hẹn, hệ thống thực hiện đúng chuỗi kiểm tra của `BR-17.8`:
  - Kiểm tra làm mất hiệu lực phiên bản không đạt → chiến dịch về **Nháp**, người phụ trách và người duyệt được thông báo.
  - Kiểm tra vận hành không đạt → chiến dịch chuyển **Giữ lại**, giữ nguyên phê duyệt; người phụ trách, người duyệt và mọi người có quyền Phát sóng chiến dịch trên chiến dịch được thông báo ngay kèm lý do. Trong thời gian ân hạn `CFG-CAMP-32`, người có quyền Phát sóng chiến dịch bấm phát sóng khi điều kiện đã được khắc phục (chạy lại toàn bộ chuỗi kiểm tra). Trước khi hết ân hạn một khoảng `CFG-CAMP-60` — hoặc ở giữa thời gian ân hạn nếu ân hạn ngắn hơn hai lần khoảng đó — những người trên được nhắc lại kèm lý do còn tồn tại. Thời gian Không gian làm việc đang dừng khẩn cấp, đang trong ngày không gửi hoặc đang bị đình chỉ dịch vụ không được tính vào ân hạn. Mốc hết ân hạn **thuộc** ân hạn: các thay đổi có hiệu lực đúng tại mốc đó (ví dụ hạn ngạch ngày mới lúc 00:00) được xét trước khi chiến dịch về Nháp. Hết thời gian ân hạn mà chưa phát sóng thì chiến dịch về Nháp. Hệ thống **không tự động** phát sóng một chiến dịch đang Giữ lại, **trừ khi** phiên bản đã duyệt có bật **"Tự phát sóng khi điều kiện vận hành được khắc phục"** kèm một khoảng tối đa (không quá thời gian ân hạn và không quá hạn chót gửi): khi đó, nếu trong khoảng này một lỗi chặn nhóm (b) hoặc điều kiện vận hành của bước 2–4 `BR-17.8` tự hết (tài khoản gửi kết nối lại, mẫu hết tạm khóa, trang đích lên, hạn mức được nâng), hệ thống chạy lại toàn bộ chuỗi kiểm tra và tự bắt đầu gửi. Lựa chọn này không áp cho chênh lệch quy mô (`BR-06.4`), dừng khẩn cấp, ngày không gửi và đình chỉ dịch vụ — các trường hợp đó luôn cần người quyết định. Cách xử lý hạn ngạch "Chờ" tự bắt đầu khi đủ hạn ngạch theo `BR-40.2` mà không cần bật lựa chọn này.
  - Chiến dịch ở Chờ duyệt khi tới giờ hẹn thì không gửi, về Nháp với lý do "Quá giờ hẹn mà chưa được phê duyệt".

  **Lý do nghiệp vụ:** Tự động gửi muộn hơn giờ hẹn có thể đưa thông điệp tới sai thời điểm (ưu đãi đã hết hạn, sự kiện đã diễn ra), nên việc gửi muộn luôn cần người quyết định. Nhưng xóa luôn phê duyệt chỉ vì hạn mức chưa kịp nâng hay tài khoản gửi bị ngắt vài phút sẽ biến mọi chiến dịch hẹn giờ ban đêm thành việc phải duyệt lại từ đầu vào sáng hôm sau.

- **`BR-18.4` (Hạn chót gửi):** Người soạn đặt được một **hạn chót gửi** — thời điểm mà sau đó thông điệp không còn giá trị (ưu đãi hết hạn, sự kiện đã diễn ra). Hạn chót là một phần của phiên bản duyệt. Mọi tin — kể cả tin bị hoãn vì khung giờ yên lặng, giới hạn 24 giờ theo điểm đến, dàn trải hạn ngạch, ngày không gửi, dự phòng, gửi lại, phần còn lại sau A/B — tới lượt **sau** hạn chót bị loại với lý do "Quá hạn chót gửi" (`BR-08.1` mục 14). Với nền tảng hỗ trợ thời hạn sống của tin, hệ thống truyền hạn chót sang nhà cung cấp để tin chưa phân phát được không bị phân phát muộn. Nếu hạn chót sớm hơn thời điểm sớm nhất có thể gửi cho **mọi** người nhận (ví dụ cả khoảng từ giờ hẹn tới hạn chót nằm trong khung giờ yên lặng), đó là lỗi chặn nhóm (a) (`BR-16.1`); nếu chỉ một phần người nhận bị ảnh hưởng, danh mục kiểm tra cảnh báo kèm số người ước tính bị loại.

  **Lý do nghiệp vụ:** Mọi cơ chế hoãn trong tài liệu này đều bảo vệ người nhận khỏi bị làm phiền, nhưng một ưu đãi hết hạn tới tay khách lúc 08:00 còn tệ hơn không gửi — khách vào mua và thấy ưu đãi không còn. Chỉ người soạn biết thông điệp hết giá trị lúc nào.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-18.1.1` | Người đặt dùng múi giờ GMT+3, Không gian làm việc GMT+7 | Đặt 09:00 | Màn hình hiển thị "09:00 (GMT+3) — tức 13:00 theo giờ Không gian làm việc" |
| `AC-18.1.2` | Bây giờ là 10:00; `CFG-CAMP-29` = 15 phút | Mở bộ chọn giờ | Các mốc trước 10:15 không chọn được; giao diện ghi rõ giới hạn |
| `AC-18.1.3` | `CFG-CAMP-30` = 12 tháng | Chọn một ngày sau 13 tháng | Không chọn được |
| `AC-18.2.1` | Chiến dịch Đã duyệt hẹn 09:00 thứ Sáu; `CFG-CAMP-31` = 2 giờ | Người có quyền Phát sóng chiến dịch dời thành 14:00 | Chiến dịch về Nháp, cần phê duyệt lại |
| `AC-18.2.2` | Như trên | Dời thành 10:30 | Chiến dịch vẫn Đã duyệt với giờ hẹn 10:30; nhật ký ghi lần dời |
| `AC-18.2.3` | Như trên | Dời thành 08:00 | Chiến dịch về Nháp (dời sớm hơn) |
| `AC-18.2.4` | Người soạn đề xuất 09:00; người duyệt đổi thành 10:00 khi phê duyệt | Phê duyệt | Chiến dịch Đã duyệt với giờ hẹn 10:00, không cần duyệt lại |
| `AC-18.2.5` | Người soạn đề xuất 10:00 | Người duyệt tìm cách đổi thành 09:00 khi phê duyệt | Không cho phép; chỉ dời muộn hơn được |
| `AC-18.2.6` | Chiến dịch hẹn 00:00 Giữ lại vì hạn mức; 07:00 hạn mức được nâng; còn trong ân hạn | M dời giờ hẹn sang 20:00 cùng ngày (trước hạn chót) | Chiến dịch về Đã duyệt với giờ hẹn 20:00, không cần duyệt lại |
| `AC-18.2.7` | Chiến dịch Đã duyệt hẹn 09:00; `CFG-CAMP-31` = 2 giờ | Dời 09:00 → 09:30, rồi 09:30 → 10:00 | Cả hai lần giữ Đã duyệt (tổng dời 1 giờ); dời tiếp lên 11:30 thì về Nháp |
| `AC-18.3.1` | Chiến dịch Chờ duyệt, giờ hẹn 09:00 đã tới | Không ai duyệt | Không gửi; chiến dịch về Nháp, lý do "Quá giờ hẹn mà chưa được phê duyệt" |
| `AC-18.3.2` | Chiến dịch Đã duyệt hẹn 00:00, cách xử lý hạn ngạch là "Chờ"; lúc 00:00 hạn mức gói không đủ | Tới giờ | Không gửi; chiến dịch Giữ lại, phê duyệt còn nguyên; người có quyền được thông báo ngay |
| `AC-18.3.3` | Tiếp nối AC-18.3.2; 07:00 hạn mức đã được nâng, trong thời gian ân hạn | M bấm phát sóng | Chiến dịch Đang gửi, không cần duyệt lại |
| `AC-18.3.4` | Tiếp nối AC-18.3.2; hết thời gian ân hạn `CFG-CAMP-32` mà không ai phát sóng | Chờ | Chiến dịch về Nháp |
| `AC-18.3.5` | Chiến dịch Đã duyệt hẹn giờ; lúc tới giờ tin nhắn mẫu đã bị nền tảng vô hiệu | Tới giờ | Chiến dịch về Nháp (lỗi chặn nhóm (a)) |
| `AC-18.3.6` | Chiến dịch hẹn 00:00 có bật tự phát sóng khi khắc phục, khoảng tối đa 60 phút; lúc 00:00 tài khoản gửi ngắt, 00:20 tự kết nối lại | Hệ thống xử lý | Chiến dịch tự chạy lại chuỗi kiểm tra lúc 00:20 và bắt đầu gửi, không cần người bấm |
| `AC-18.3.7` | Như trên nhưng tài khoản kết nối lại lúc 01:30 | Hệ thống xử lý | Chiến dịch vẫn Giữ lại; cần người có quyền bấm phát sóng |
| `AC-18.3.8` | Chiến dịch Giữ lại từ 00:00, `CFG-CAMP-32` = 24 giờ, `CFG-CAMP-60` = 2 giờ; lý do vẫn còn | Tới 22:00 | Người phụ trách, người duyệt và người có quyền Phát sóng nhận nhắc lại kèm lý do; 00:00 hôm sau chiến dịch về Nháp nếu vẫn chưa phát sóng |
| `AC-18.4.1` | Chiến dịch SMS flash sale hẹn 00:00, hạn chót 02:00; khung giờ yên lặng 22:00–08:00 áp cho mọi người nhận | Mở danh mục kiểm tra | Lỗi chặn: không người nhận nào gửi được trước hạn chót |
| `AC-18.4.2` | Email thuộc `CFG-CAMP-07`; chiến dịch Email hẹn 21:00, hạn chót 23:00, nhịp đủ gửi hết trong 1 giờ; 30% người nhận ở múi giờ đang trong khung giờ yên lặng | Mở danh mục kiểm tra, rồi phát sóng | Cảnh báo ước tính 30% bị loại; sau 23:00 các tin còn hoãn bị loại "Quá hạn chót gửi" |

---

#### FEAT-19 — Tạm dừng, Sửa trong lúc Tạm dừng & Tiếp tục

**Mô tả nghiệp vụ:** Khi phát hiện sự cố trong lúc gửi, người có quyền dừng ngay việc gửi, sửa lỗi, và gửi tiếp phần còn lại.

**Quy tắc nghiệp vụ:**

- **`BR-19.1` (Tạm dừng có hiệu lực nhanh):** Sau khi bấm Tạm dừng, không tin mới nào được chuyển nhà cung cấp quá thời hạn mục tiêu tại `KPI-06`. Tin đã chuyển nhà cung cấp trước thời điểm đó không thu hồi được; giao diện nói rõ điều này khi xác nhận tạm dừng và hiển thị số tin đã chuyển đi.

  **Lý do nghiệp vụ:** Mỗi phút chậm trễ khi dừng một chiến dịch có lỗi là thêm hàng nghìn khách hàng nhận thông tin sai.

- **`BR-19.2` (Tiếp tục gửi phần còn lại):** Tiếp tục gửi đúng những người trong danh sách chốt chưa được gửi; không gửi lại cho người đã được chuyển nhà cung cấp. Mọi kiểm tra tại thời điểm gửi (`BR-08.4`) và bước 1–4 của `BR-17.8` (trừ chênh lệch đối tượng, vì danh sách đã chốt, và trừ điều kiện ngày không gửi — trong ngày không gửi, chiến dịch vẫn tiếp tục được, tin tự hoãn theo `BR-43.1`) được thực hiện lại khi tiếp tục, cho khối lượng còn lại; không đạt thì chiến dịch vẫn Tạm dừng và người thao tác được báo lý do — với lỗi nhóm (a), phải sửa phiên bản theo `BR-19.3` rồi mới tiếp tục được. Riêng chiến dịch "Chờ" chỉ thiếu chỗ hạn ngạch: người thao tác chọn được **"Báo khi đủ chỗ"** — khi phần còn có thể giữ chỗ đủ cho mọi ngày dự kiến, người phụ trách và người đã yêu cầu được thông báo để bấm tiếp tục (chạy lại toàn bộ kiểm tra của quy tắc này); thông báo nêu rõ chỗ chưa được giữ và có thể bị chiến dịch khác lấy trước khi bấm. Hệ thống **không** tự tiếp tục chiến dịch đang Tạm dừng (Nguyên tắc 5, `BR-20.2`); yêu cầu thông báo hết hiệu lực khi chiến dịch rời trạng thái Tạm dừng (tiếp tục, bị hủy hay tự hủy theo `BR-19.4`), khi qua hạn chót gửi, hoặc khi người có quyền Phát sóng hủy yêu cầu.

  **Lý do nghiệp vụ:** Trong thời gian tạm dừng, hạn mức có thể đã bị chiến dịch khác dùng hết và người nhận có thể đã từ chối; tiếp tục mà không kiểm tra lại là tiếp tục dựa trên dữ liệu cũ.

- **`BR-19.3` (Sửa trong lúc tạm dừng):** Khi chiến dịch Tạm dừng, người có quyền được sửa **nội dung** (kể cả liên kết), **nhịp gửi** và **tài khoản gửi sang một tài khoản khác cùng kênh**; không sửa được đối tượng và kênh. Đổi tài khoản gửi là thay đổi phiên bản cần duyệt như sửa nội dung. Mọi thay đổi nội dung tạo phiên bản mới. Khi `CFG-CAMP-05` bật, chiến dịch chuyển **Tạm dừng — chờ duyệt phiên bản** và phiên bản mới phải được phê duyệt theo `BR-17.2` trước khi tiếp tục; khi `CFG-CAMP-05` tắt, người có quyền Phát sóng chiến dịch xác nhận phiên bản mới theo `BR-17.9` và chiến dịch ở lại Tạm dừng. Nhịp gửi được giảm trực tiếp, không tạo phiên bản mới; tăng nhịp là thay đổi cần duyệt như sửa nội dung. Được duyệt thì phiên bản mới trở thành phiên bản hiệu lực và chiến dịch về Tạm dừng, sẵn sàng tiếp tục; bị từ chối hoặc người gửi rút lại thì chiến dịch về Tạm dừng với **phiên bản đã duyệt trước đó**. Không tiếp tục được khi đang chờ duyệt. Sổ cái ghi **phiên bản nội dung** mà từng người nhận đã nhận.

  **Lý do nghiệp vụ:** Tình huống điển hình là đang gửi thì phát hiện liên kết ưu đãi bị lỗi. Nếu không sửa được trong lúc tạm dừng, lựa chọn duy nhất là hủy và gửi lại — khi đó người đã nhận bản lỗi lại nhận thêm bản mới, còn nếu loại họ ra thì phải làm thủ công. Không cho đổi đối tượng hay kênh vì đó là chiến dịch khác, không phải sửa lỗi; đổi sang tài khoản khác cùng kênh được phép vì tài khoản bị khóa giữa chừng là sự cố vận hành, không làm đổi thông điệp.

- **`BR-19.4` (Tạm dừng quá lâu):** Chiến dịch Tạm dừng (kể cả Tạm dừng — chờ duyệt phiên bản) liên tục quá `CFG-CAMP-17` (tính bằng khoảng trượt tuyệt đối) bị tự động hủy (`FEAT-21`). Thời gian Không gian làm việc đang dừng khẩn cấp (`FEAT-42`) hoặc bị đình chỉ dịch vụ không được tính vào khoảng này. Người phụ trách và người duyệt được nhắc trước `CFG-CAMP-33`.

  **Lý do nghiệp vụ:** Thông điệp tiếp thị có tính thời điểm; tiếp tục một chiến dịch sau nhiều tuần gần như chắc chắn gửi nội dung đã lỗi thời. Không tính thời gian dừng khẩn cấp và đình chỉ vì đó là tạm dừng không do chiến dịch; tự hủy hàng loạt chiến dịch chỉ vì một đợt đình chỉ kéo dài là hậu quả không ai muốn và không đảo ngược được. Đồng thời chiến dịch treo lâu làm rối báo cáo và làm người dùng tưởng thông điệp vẫn còn hiệu lực.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-19.1.1` | Chiến dịch Đang gửi, đã chuyển 1.000/5.000 tin | Bấm Tạm dừng | Trong vòng 1 phút không còn tin mới được chuyển; chiến dịch Tạm dừng; màn hình hiện số đã chuyển thực tế |
| `AC-19.2.1` | Tiếp nối AC-19.1.1, không sửa gì | Bấm Tiếp tục | Chỉ gửi cho những người chưa được chuyển; không ai nhận hai tin |
| `AC-19.2.2` | Trong lúc tạm dừng, 20 người trong phần còn lại hủy nhận tin | Tiếp tục | 20 người đó bị loại tại thời điểm gửi |
| `AC-19.2.3` | Trong lúc tạm dừng, chiến dịch khác dùng hết hạn mức gói | Bấm Tiếp tục | Chiến dịch vẫn Tạm dừng; thông báo nêu hạn mức không đủ cho khối lượng còn lại |
| `AC-19.2.4` | Trong lúc tạm dừng, nền tảng vô hiệu tin nhắn mẫu của chiến dịch | Bấm Tiếp tục | Không tiếp tục được; thông báo yêu cầu sửa phiên bản sang mẫu khác theo `BR-19.3` |
| `AC-19.2.5` | Chiến dịch có hạn chót gửi 18:00, tạm dừng lúc 16:00 với 2.000 người chưa gửi | Tiếp tục lúc 19:00 | Chiến dịch không kẹt: 2.000 người ghi "Quá hạn chót gửi", chiến dịch chuyển Hoàn tất |
| `AC-19.2.6` | Chiến dịch "Chờ" Tạm dừng; tiếp tục bị từ chối vì thiếu 3.000 chỗ hạn ngạch hôm nay | Quản lý Marketing chọn "Báo khi đủ chỗ"; 2 giờ sau một chiến dịch khác hoàn tất và trả chỗ | Người phụ trách và Quản lý Marketing được báo; chiến dịch vẫn Tạm dừng tới khi có người bấm tiếp tục và chuỗi kiểm tra đạt |
| `AC-19.3.1` | Tạm dừng, sửa liên kết; `CFG-CAMP-05` bật | Bấm Tiếp tục | Không cho tiếp tục; phiên bản mới cần phê duyệt; sau khi được duyệt thì tiếp tục được |
| `AC-19.3.2` | Tiếp nối AC-19.3.1 sau khi tiếp tục và hoàn tất | Xem sổ cái | 1.000 người đầu ghi phiên bản 1, phần còn lại ghi phiên bản 2 |
| `AC-19.3.3` | Chiến dịch Tạm dừng | Thử đổi tiêu chí đối tượng | Không cho phép |
| `AC-19.3.4` | Chiến dịch Tạm dừng — chờ duyệt phiên bản 2 | Người duyệt từ chối phiên bản 2 | Chiến dịch về Tạm dừng; tiếp tục thì gửi phiên bản 1 |
| `AC-19.3.5` | `CFG-CAMP-05` tắt; chiến dịch Tạm dừng; M sửa liên kết và xác nhận phiên bản | Bấm Tiếp tục | Tiếp tục được với phiên bản mới; lịch sử ghi "tự xác nhận" |
| `AC-19.3.6` | Chiến dịch WhatsApp Tạm dừng vì số gửi bị nền tảng khóa | M đổi sang một số WhatsApp khác đã kết nối, gửi duyệt; M2 duyệt | Tiếp tục được với tài khoản mới; không đổi được sang kênh khác |
| `AC-19.4.1` | `CFG-CAMP-17` = 14 ngày, `CFG-CAMP-33` = 24 giờ; chiến dịch Tạm dừng 13 ngày (dịch chuyển đồng hồ `NFR-11`) | Chờ | Người phụ trách nhận nhắc "sẽ tự hủy sau 24 giờ"; khi đủ 14 × 24 giờ chiến dịch Đã hủy |
| `AC-19.4.2` | Chiến dịch Tạm dừng 10 ngày, trong đó 7 ngày Không gian làm việc bị đình chỉ dịch vụ; `CFG-CAMP-17` = 14 ngày | Chờ | Chiến dịch chưa bị tự hủy (chỉ tính 3 ngày) |

---

#### FEAT-20 — Tự động Tạm dừng Bảo vệ

**Mô tả nghiệp vụ:** Hệ thống tự tạm dừng chiến dịch khi có dấu hiệu chiến dịch đang gây hại cho khách hàng, cho danh tiếng gửi hoặc cho chi phí của doanh nghiệp.

**Quy tắc nghiệp vụ:**

- **`BR-20.1` (Các tình huống bắt buộc tự động tạm dừng):**
  1. Tỷ lệ khiếu nại thư rác của chiến dịch vượt `CFG-CAMP-12` sau khi đã có ít nhất `CFG-CAMP-14` tin được phân phát.
  2. Tỷ lệ điểm đến hỏng vĩnh viễn của chiến dịch vượt `CFG-CAMP-13` sau khi đã có ít nhất `CFG-CAMP-14` tin được chuyển nhà cung cấp.
  3. Tài khoản gửi ngừng hoạt động, tên miền gửi mất xác thực (`BR-10.2`, `BR-10.3`), hoặc tin nhắn mẫu bị tạm khóa, bị từ chối, bị vô hiệu hay bị đổi sang loại không dùng được cho mục đích của chiến dịch — tiếp thị, hoặc giao dịch với Thông báo dịch vụ (`BR-13.2`, `BR-45.2`); hoặc mẫu (WhatsApp, Zalo ZNS) hay nội dung SMS, Zalo OA đã duyệt không còn chứa nhãn quảng cáo mà chính sách vừa yêu cầu (`BR-30.1`).
  4. Không đủ hạn mức gói dịch vụ, không tra được hạn mức, hoặc Không gian làm việc bị đình chỉ dịch vụ giữa chừng (`billing-subscription-srs.md`, `BR-22.2`, `BR-32.3`).
  5. Hết hạn ngạch ngày của kênh chính mà chiến dịch không được cấu hình dàn trải (`BR-40.2`, `BR-40.3`) — không áp cho chuỗi nuôi dưỡng (`BR-39.1`).
  6. Chi phí đã cam kết chạm ngân sách chiến dịch (`BR-41.2`).
  7. Nhà cung cấp báo đang giới hạn hoặc từ chối hàng loạt tin của tài khoản gửi, hoặc tin bị từ chối vì danh tiếng người gửi (`BR-24.1`), vượt dấu hiệu tại Phụ lục B.2.
  8. Dừng khẩn cấp toàn Không gian làm việc (`FEAT-42`) — không áp cho Thông báo dịch vụ (`FEAT-45`).
  9. Công cụ theo dõi danh tiếng của nhà cung cấp hộp thư báo tỷ lệ thư rác của tên miền gửi vượt `CFG-CAMP-12` (`BR-27.4`) — áp cho chiến dịch Email.
  10. Đầu số nhận từ chối của tên thương hiệu SMS đang dùng bị gỡ hoặc ngừng nhận tin (`BR-26.1`) — phát hiện theo báo cáo của đối tác hoặc tin kiểm tra định kỳ gửi tới đầu số; với Thông báo dịch vụ, chỉ áp cho mục đích (1), (3), (5) gửi qua SMS (`BR-45.7`).
  11. (Chỉ chuỗi nuôi dưỡng) Điều kiện vào chuỗi dùng một trường đã được đánh dấu nhạy cảm sau khi duyệt mà chưa đi đúng đường ngoại lệ (`BR-05.5`).

  Ngày không gửi (`FEAT-43`) **không** là tình huống tạm dừng bảo vệ: tin chỉ bị hoãn và tự gửi tiếp khi hết ngày không gửi. Mọi tình huống trên áp cho cả **chuỗi nuôi dưỡng** ở mọi trạng thái còn gửi (Đang chạy, Ngừng nhận người mới), tính trên số liệu của chuỗi trong khoảng trượt tại Phụ lục B.2; chuỗi bị tạm dừng bảo vệ theo cùng quy tắc `BR-20.2`, `BR-20.3`.

  **Lý do nghiệp vụ:** Mỗi tình huống trên là dấu hiệu tiếp tục gửi sẽ gây thiệt hại lớn hơn dừng lại: khiếu nại và địa chỉ hỏng tăng vọt làm nhà cung cấp khóa tài khoản gửi của cả doanh nghiệp; gửi khi hết hạn mức hay ngân sách là tiêu tiền không được phép.

- **`BR-20.2` (Tạm dừng bảo vệ không tự tiếp tục):** Chiến dịch bị tạm dừng bảo vệ hiển thị rõ **lý do và số liệu kích hoạt**. Chỉ người có quyền Phát sóng chiến dịch mới tiếp tục được, sau khi đánh dấu đã xem lý do. Với tình huống 1 và 2, người tiếp tục phải nhập ghi chú giải trình; sau khi tiếp tục, ngưỡng của tình huống đó được xét trên **phần tin gửi sau thời điểm tiếp tục** (cỡ mẫu tối thiểu `CFG-CAMP-14` tính lại từ đầu), không trên số liệu tích lũy từ trước — số liệu toàn chiến dịch vẫn hiển thị để theo dõi. Hệ thống **không bao giờ** tự tiếp tục, kể cả khi nguyên nhân đã hết (ví dụ tài khoản đã kết nối lại, hạn mức đã được nâng, Không gian làm việc đã hết bị đình chỉ).

  **Lý do nghiệp vụ:** Nguyên tắc 5 (Mục 2.4). Tự động tiếp tục sau khi hết đình chỉ có thể gửi một khuyến mãi đã hết hạn tới hàng chục nghìn người; tự động tiếp tục sau khiếu nại tăng vọt là tiếp tục gây hại.

- **`BR-20.3` (Thông báo):** Người phụ trách, người đã duyệt phiên bản đang gửi, và với tình huống 1, 2 và 11 thêm Người phụ trách Bảo vệ Dữ liệu, được thông báo ngay.

  **Lý do nghiệp vụ:** Tạm dừng tự động mà không ai biết thì chiến dịch treo tới khi bị tự hủy; người có thể tiếp tục phải là người được báo đầu tiên.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-20.1.1` | `CFG-CAMP-12` = 0,3%, `CFG-CAMP-14` = 500; chiến dịch đã phân phát 2.000 tin, có 7 khiếu nại (0,35%) | Khiếu nại thứ 7 tới | Chiến dịch Tạm dừng, lý do "Tỷ lệ khiếu nại 0,35% vượt ngưỡng 0,3%" |
| `AC-20.1.2` | Chiến dịch mới phân phát 100 tin, có 2 khiếu nại (2%) | Khiếu nại tới | Chưa tạm dừng (chưa đủ cỡ mẫu `CFG-CAMP-14`) |
| `AC-20.1.3` | Chiến dịch Đang gửi; Không gian làm việc bị đình chỉ | Đình chỉ có hiệu lực | Chiến dịch Tạm dừng, lý do "Đình chỉ dịch vụ" |
| `AC-20.1.4` | `CFG-CAMP-13` = 5%, `CFG-CAMP-14` = 500; chiến dịch đã chuyển 1.000 tin, đã có 50 tin điểm đến không tồn tại (5%) | Tin hỏng thứ 51 được báo về (5,1%) | Chiến dịch Tạm dừng, lý do "Tỷ lệ hỏng vĩnh viễn vượt ngưỡng" |
| `AC-20.1.5` | Nhà cung cấp từ chối hàng loạt tin với lý do danh tiếng người gửi, vượt dấu hiệu tại Phụ lục B.2 (nhà cung cấp giả lập `NFR-12`) | Hệ thống xử lý | Chiến dịch Tạm dừng, lý do "Nhà cung cấp từ chối hàng loạt"; các điểm đến không bị đánh dấu Không tiếp cận được |
| `AC-20.1.6` | Chuỗi nuôi dưỡng ở trạng thái Ngừng nhận người mới có tỷ lệ khiếu nại 30 ngày vượt `CFG-CAMP-12`, đủ cỡ mẫu | Khiếu nại tiếp theo tới | Chuỗi chuyển Tạm dừng, lý do tỷ lệ khiếu nại; không bước nào được gửi tới khi có người tiếp tục |
| `AC-20.1.7` | Chiến dịch SMS Đang gửi; đối tác báo đầu số nhận từ chối ngừng nhận tin | Hệ thống xử lý | Chiến dịch Tạm dừng, lý do đầu số nhận từ chối ngừng hoạt động |
| `AC-20.2.1` | Tiếp nối AC-20.1.3; doanh nghiệp đã thanh toán, hết đình chỉ | Chờ | Chiến dịch vẫn Tạm dừng; người phụ trách nhận thông báo "có thể tiếp tục" |
| `AC-20.2.2` | Tiếp nối AC-20.1.1 | Quản lý Marketing bấm Tiếp tục không nhập ghi chú | Không cho phép; ghi chú giải trình bắt buộc |
| `AC-20.2.3` | Chiến dịch tự tạm dừng khi tỷ lệ khiếu nại 0,4% trên 1.000 thư; `CFG-CAMP-12` = 0,3%, `CFG-CAMP-14` = 1.000 | Quản lý Marketing tiếp tục có ghi chú; 1 khiếu nại mới đến trong 200 thư kế tiếp | Chiến dịch không tạm dừng lại (phần sau khi tiếp tục chưa đủ 1.000 tin); tạm dừng lại nếu phần gửi sau khi tiếp tục vượt 0,3% khi đã đủ 1.000 tin |
| `AC-20.3.1` | Tiếp nối AC-20.1.1 | Kiểm tra thông báo | Người phụ trách, người duyệt, Người phụ trách Bảo vệ Dữ liệu đều nhận |

---

#### FEAT-21 — Hủy Chiến dịch

**Mô tả nghiệp vụ:** Dừng vĩnh viễn một chiến dịch đang gửi hoặc tạm dừng.

**Quy tắc nghiệp vụ:**

- **`BR-21.1` (Hệ quả của hủy):** Người có quyền Phát sóng chiến dịch hủy được chiến dịch Đang gửi hoặc Tạm dừng, bắt buộc nhập lý do. Mọi người trong danh sách chốt chưa được chuyển nhà cung cấp chuyển sang **Đã hủy trước khi gửi**; các lượt dự phòng đang chờ và các lần thử lại đang chờ cũng bị hủy. Tin đã chuyển nhà cung cấp giữ nguyên trạng thái và vẫn tiếp tục nhận cập nhật phân phát, tương tác. Chiến dịch Đã hủy không tiếp tục được; muốn gửi lại, nhân bản (`FEAT-02`).

  **Lý do nghiệp vụ:** Hủy là quyết định không thể đảo; lý do hủy là thông tin cần thiết khi đối soát chi phí và khi rà soát vì sao một chiến dịch đã được duyệt lại phải dừng.

- **`BR-21.2` (Phân biệt với hủy lịch):** Chiến dịch Đã duyệt có giờ hẹn nhưng chưa gửi tin nào không "hủy" theo quy tắc này, mà dùng **hủy lịch** (`BR-18.2`) để về Nháp.

  **Lý do nghiệp vụ:** Chiến dịch chưa gửi tin nào vẫn sửa được và gửi lại được; đưa nó vào trạng thái Đã hủy không đảo được là mất công soạn và duyệt mà không bảo vệ điều gì.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-21.1.1` | Chiến dịch Tạm dừng, đã chuyển 1.000/5.000 | Hủy, lý do "Sai giá" | Trạng thái Đã hủy; 4.000 người ghi "Đã hủy trước khi gửi"; không có nút Tiếp tục |
| `AC-21.1.2` | Tiếp nối AC-21.1.1; một người trong 1.000 người đã nhận mở thư ngày hôm sau | Xem báo cáo | Sự kiện mở được ghi nhận |
| `AC-21.2.1` | Chiến dịch Đã duyệt hẹn giờ, chưa gửi | Tìm thao tác | Có "Hủy lịch" (về Nháp), không có "Hủy chiến dịch" |

---

#### FEAT-22 — Nhịp Gửi & Ưu tiên Tin Trả lời Khách hàng

**Mô tả nghiệp vụ:** Điều tiết tốc độ gửi để không làm quá tải đội tiếp nhận phản hồi, không vượt ngưỡng an toàn của nhà cung cấp, và không chiếm chỗ của tin tư vấn viên đang trả lời khách hàng.

**Quy tắc nghiệp vụ:**

- **`BR-22.1` (Nhịp gửi theo chiến dịch):** Người soạn đặt được tốc độ gửi tối đa theo giờ cho chiến dịch; mặc định theo `CFG-CAMP-27`. Nhịp gửi là một phần của phiên bản duyệt; người có quyền Phát sóng chiến dịch **giảm** nhịp được bất kỳ lúc nào mà không cần duyệt lại, **tăng** nhịp là thay đổi cần duyệt lại (`BR-17.4`) — với chiến dịch Đang gửi, phải Tạm dừng trước rồi đi theo `BR-19.3`. Giao diện hiển thị thời gian dự kiến hoàn tất theo nhịp đã chọn.

  **Lý do nghiệp vụ:** Một chiến dịch WhatsApp 20.000 người gửi hết trong 10 phút có thể sinh ra hàng nghìn tin trả lời cùng lúc — đội tư vấn không kịp xử lý và khách hàng đang có ý định mua bị bỏ rơi (vấn đề 5, Mục 2.1).

- **`BR-22.2` (Ngưỡng an toàn của nhà cung cấp):** Ngoài nhịp do người soạn chọn, hệ thống luôn tự điều tiết để không vượt ngưỡng gửi an toàn mà từng nhà cung cấp và từng tài khoản gửi cho phép. Ô nhập nhịp gửi hiển thị ngưỡng này và không nhận giá trị vượt ngưỡng. Với nền tảng giới hạn theo **số người nhận duy nhất trong 24 giờ** (ví dụ WhatsApp — giới hạn có thể tính chung cho mọi số của cùng một tài khoản doanh nghiệp trên nền tảng), hệ thống đối chiếu cả giới hạn đó ở đúng cấp nền tảng áp dụng và hiển thị số ngày dự kiến nếu khối lượng vượt giới hạn một ngày.

  **Lý do nghiệp vụ:** Vượt ngưỡng của nhà cung cấp không làm gửi nhanh hơn mà làm tin bị từ chối hàng loạt và tài khoản bị hạ hạng.

- **`BR-22.3` (Ưu tiên tin trả lời khách hàng):** Trên tài khoản kênh dùng chung với Hộp thư Hội thoại, khi nền tảng giới hạn số tin, tin của tư vấn viên trả lời khách đang chờ **luôn được ưu tiên** trước tin chiến dịch, theo `omnichat-srs.md` (`BR-12.6`). Chiến dịch tự giảm nhịp để nhường chỗ, không bao giờ làm tin tư vấn viên bị chậm.

  **Lý do nghiệp vụ:** Một khách hàng đang chờ trả lời có giá trị lớn hơn nhiều so với một tin quảng cáo tới muộn vài phút.

- **`BR-22.4` (Nhiều chiến dịch trên cùng tài khoản gửi):** Khi nhiều chiến dịch cùng gửi qua một tài khoản, năng lực gửi của tài khoản được chia theo thứ tự chiến dịch bắt đầu gửi trước; không chiến dịch nào bị dừng hẳn vì chiến dịch khác.

  **Lý do nghiệp vụ:** Ưu tiên chiến dịch bắt đầu trước để thời gian hoàn tất dự kiến đã hiển thị cho người duyệt vẫn đúng; không để một chiến dịch lớn chặn hoàn toàn các chiến dịch nhỏ bắt đầu sau.

- **`BR-22.5` (Làm nóng tài khoản gửi mới):** Tài khoản gửi email hoặc tên miền gửi mới, và tài khoản gửi trên nền tảng có hạng mức theo lịch sử gửi, chịu **lịch tăng nhịp** `CFG-CAMP-41`: khối lượng tối đa mỗi ngày tăng dần theo lịch cho tới khi đạt ngưỡng an toàn. Chiến dịch vượt khối lượng của lịch được dàn trải theo lịch, hiển thị ngày dự kiến hoàn tất khi xem trước và khi duyệt. Tên miền hoặc tài khoản chuyển sang từ một hệ thống gửi khác mà có **bằng chứng danh tiếng** (lịch sử gửi ổn định, số liệu của công cụ theo dõi danh tiếng của nhà cung cấp hộp thư) được miễn lịch làm nóng khi Quản trị viên cùng Người phụ trách Bảo vệ Dữ liệu chấp thuận; miễn trừ được ghi nhật ký.

  **Lý do nghiệp vụ:** Nhà cung cấp hộp thư đánh giá người gửi mới rất khắt khe; gửi 50.000 thư ngay ngày đầu từ một tên miền mới gần như chắc chắn bị đưa vào danh sách chặn, làm hỏng tên miền đó trong nhiều tháng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-22.1.1` | Chiến dịch 12.000 người, nhịp 3.000 tin/giờ | Xem trước khi phát sóng | Hiển thị thời gian dự kiến hoàn tất khoảng 4 giờ |
| `AC-22.1.2` | Chiến dịch Đang gửi, nhịp đã duyệt 3.000 tin/giờ | M giảm nhịp xuống 1.000 tin/giờ | Áp dụng ngay, chiến dịch vẫn Đang gửi |
| `AC-22.1.3` | Chiến dịch Đã duyệt, nhịp 3.000 tin/giờ | M tăng nhịp lên 6.000 tin/giờ | Chiến dịch về Nháp, cần duyệt lại |
| `AC-22.2.1` | Ngưỡng an toàn của tài khoản là 1.000 tin/giờ | Người soạn nhập nhịp 5.000 tin/giờ | Ô nhập không nhận giá trị vượt 1.000; giao diện hiển thị ngưỡng và lý do |
| `AC-22.3.1` | Chiến dịch WhatsApp đang gửi trên tài khoản dùng chung; nền tảng đang giới hạn số tin; tư vấn viên gửi trả lời khách | Tư vấn viên gửi | Tin tư vấn viên được gửi trước các tin chiến dịch đang chờ |
| `AC-22.4.1` | Chiến dịch X bắt đầu 9:00, chiến dịch Y bắt đầu 9:30, cùng tài khoản gửi | Cả hai đang gửi | Cả hai đều tiếp tục có tin được gửi; X được ưu tiên phần năng lực trước |
| `AC-22.5.1` | Tên miền gửi mới; lịch làm nóng cho phép 1.000 thư ngày đầu, nhân đôi mỗi ngày | Xem trước chiến dịch 10.000 người | Hiển thị dự kiến hoàn tất sau 4 ngày; mỗi ngày gửi không quá khối lượng của lịch |
| `AC-22.5.2` | Tên miền gửi đã dùng ổn định 3 năm ở hệ thống cũ; Quản trị viên và Người phụ trách Bảo vệ Dữ liệu chấp thuận miễn trừ kèm bằng chứng | Xem trước chiến dịch 200.000 người | Không áp lịch làm nóng; thời gian dự kiến theo nhịp gửi thông thường |

---

### Nhóm G — Sổ cái Người nhận & Xử lý Lỗi

#### FEAT-23 — Sổ cái Người nhận

**Mô tả nghiệp vụ:** Mỗi chiến dịch có một sổ cái ghi đầy đủ chuyện gì đã xảy ra với từng người trong danh sách chốt — căn cứ đối soát với nhà cung cấp, trả lời khiếu nại của khách hàng và chứng minh tuân thủ.

**Quy tắc nghiệp vụ:**

- **`BR-23.1` (Nội dung một dòng sổ cái):** Mỗi người trong danh sách chốt có một dòng gồm: hồ sơ khách hàng, điểm đến tại thời điểm gửi, **căn cứ cho phép lượt gửi** — tham chiếu tới bằng chứng Đồng ý nhận tin (theo `contacts-srs.md`, `BR-30.3`; với Đồng ý được khôi phục khi dỡ biện pháp phòng ngừa, là bằng chứng Đồng ý gốc) khi người nhận có Đồng ý là căn cứ — kể cả trên kênh không yêu cầu; chỉ khi không có Đồng ý là căn cứ (Chưa có đồng thuận, hoặc Đồng ý không được coi là căn cứ theo lý do 6), với kênh doanh nghiệp không yêu cầu Đồng ý trước (`CFG-CAMP-74`), ghi "kênh không yêu cầu Đồng ý" kèm phiên bản chính sách gửi tiếp thị đang hiệu lực (`BR-30.1`) — kênh thực tế đã dùng, phiên bản nội dung đã nhận (và phiên bản A/B nếu có), trạng thái phân phát hiện tại, lịch sử các lần thử (kênh chính, dự phòng, thử lại) với thời điểm và kết quả từng lần, các sự kiện tương tác, lý do loại trừ hoặc lý do lỗi. Người nhận bị loại **trước khi chốt** (khi xem trước) không có dòng sổ cái; người bị loại **tại thời điểm gửi** có dòng sổ cái với trạng thái Bị loại trừ tại thời điểm gửi. Người không dùng được kênh chính nhưng được gửi qua **kênh thay thế** (`BR-37.2`) nằm trong danh sách chốt và có dòng sổ cái, ghi lý do ở kênh chính và kết quả ở kênh thay thế.

  **Lý do nghiệp vụ:** Khi khách hàng khiếu nại "tôi chưa bao giờ đồng ý nhận tin", doanh nghiệp phải chỉ ra được căn cứ cho đúng lượt gửi đó, không phải trạng thái đồng thuận hiện tại.

- **`BR-23.2` (Trạng thái phân phát):** Một dòng sổ cái ở đúng một trong các trạng thái:

  | Trạng thái | Ý nghĩa | Là kết quả cuối cùng? |
  | --- | --- | :---: |
  | **Chờ gửi** | Trong danh sách chốt, chưa tới lượt | Không |
  | **Hoãn** | Tới lượt nhưng đang trong khung giờ yên lặng, ngày không gửi, dàn trải hạn ngạch, giới hạn 24 giờ theo điểm đến (`BR-30.3`), ngoài khung giờ hoặc chờ hiệu lực duyệt SMS (`BR-13.4`), không tra được dữ liệu (`NFR-06`), chờ hạn mức cho đợt dự phòng (`BR-37.6`), hoặc — với chuỗi nuôi dưỡng — chờ hết giới hạn tần suất hay đang tạm chặn chờ xác nhận lời từ chối; kèm thời điểm dự kiến gửi nếu xác định được | Không |
  | **Đã chuyển nhà cung cấp** | Nhà cung cấp đã xác nhận tiếp nhận để gửi | Không |
  | **Đã phân phát** | Nhà cung cấp xác nhận đã tới điểm đến | Có |
  | **Thất bại tạm thời** | Không gửi được vì lý do có thể hết (mạng, nghẽn, hộp thư tạm đầy) | Không, cho tới khi hết lượt thử lại tự động (`BR-25.1`); sau đó tính là kết quả cuối cùng |
  | **Thất bại vĩnh viễn** | Không bao giờ tới được điểm đến này qua kênh này | Có |
  | **Chưa xác định** | Đã chuyển nhà cung cấp nhưng quá thời gian chờ mà không nhận được xác nhận phân phát hay thất bại | Có |
  | **Bị loại trừ tại thời điểm gửi** | Bị loại theo `BR-08.4` | Có |
  | **Đã hủy trước khi gửi** | Chiến dịch bị hủy trước khi tới lượt | Có |

  Các sự kiện mở, nhấp, trả lời, hủy nhận tin, khiếu nại được **ghi thêm** kèm thời điểm, không thay đổi trạng thái phân phát. Một thư có sự kiện mở nhưng nhà cung cấp chưa báo phân phát được coi là **Đã phân phát** từ thời điểm mở.

  Kết quả của từng lần thử dự phòng (ví dụ "Không gửi dự phòng — không đủ hạn mức", `BR-37.6`) được ghi trong lịch sử các lần thử của dòng, không phải một trạng thái riêng.

  **Lý do nghiệp vụ:** Nếu "Đã mở" ghi đè "Đã phân phát", báo cáo không còn phân biệt được tin chưa tới với tin đã tới nhưng chưa đọc, và mọi tỷ lệ tính trên số đã phân phát bị sai.

- **`BR-23.3` (Không chắc chắn thì không gửi lại):** Tin ở trạng thái **Chưa xác định** không được tự động thử lại và không kích hoạt dự phòng kênh. Người có quyền Phát sóng chiến dịch được chủ động gửi lại cho nhóm này qua `FEAT-25`, sau khi xác nhận cảnh báo "người nhận có thể nhận hai lần". Thời gian chờ xác nhận là hằng số hệ thống theo từng kênh (Phụ lục B).

  Đây là ngoại lệ có chủ đích so với quy tắc không gửi lại khi không chắc của (`omnichat-srs.md`, `BR-12.4`): quy tắc đó áp cho tin tự động trong hội thoại, còn ở đây người có quyền quyết định có cảnh báo rõ, và vẫn chịu giới hạn 24 giờ (`BR-30.3`).

  **Lý do nghiệp vụ:** Nguyên tắc 3 (Mục 2.4). Một tin quảng cáo tới hai lần làm khách hàng khó chịu và có thể khiếu nại; một tin quảng cáo không tới gây thiệt hại nhỏ hơn nhiều. Quyết định chấp nhận rủi ro trùng thuộc về con người, không thuộc về hệ thống.

- **`BR-23.4` (Sổ cái không sửa được):** Không người dùng nào sửa hay xóa được dòng sổ cái. Ngoại lệ duy nhất là khử định danh theo `FEAT-32`.

  **Lý do nghiệp vụ:** Sổ cái là bằng chứng tuân thủ và căn cứ đối soát chi phí với nhà cung cấp; một bằng chứng sửa được không còn là bằng chứng.

- **`BR-23.5` (Tra cứu & xuất):** Sổ cái lọc được theo trạng thái, lý do, kênh thực tế, phiên bản, và tìm theo khách hàng. Xuất sổ cái là một quyền hạn riêng; tệp xuất chịu đúng quy tắc che dữ liệu của người xuất và mỗi lượt xuất được ghi nhật ký (người xuất, chiến dịch, số dòng, bộ lọc).

  **Lý do nghiệp vụ:** Tệp xuất sổ cái là danh sách điểm đến kèm hành vi của từng khách hàng — thứ dễ bị đem ra ngoài dùng lại nhất; quyền xem báo cáo không được mặc nhiên kéo theo quyền mang dữ liệu đó ra khỏi hệ thống.

- **`BR-23.6` (Hồ sơ khách hàng thấy chiến dịch đã nhận):** Trên dòng thời gian của hồ sơ khách hàng, mỗi chiến dịch đã **chuyển nhà cung cấp** tới người đó hiển thị một mục (tên chiến dịch, kênh, thời điểm, trạng thái phân phát, các sự kiện tương tác), theo quy tắc hiển thị dòng thời gian hợp nhất của `tasks-srs.md`. Mọi người dùng có quyền xem hồ sơ khách hàng đều thấy mục này (không riêng Tư vấn viên); người không có quyền trên chiến dịch thì không mở được chiến dịch. Mục này ghi nhận **một sự việc xảy ra với khách hàng** nên thuộc quyền xem hồ sơ khách hàng, không phải một mục "ngoài phạm vi quyền" theo (`tasks-srs.md`, `BR-31.4`); nội dung chiến dịch vẫn thuộc quyền xem chiến dịch.

  **Lý do nghiệp vụ:** Nhân viên chăm sóc cần biết khách vừa nhận ưu đãi gì trước khi gọi điện; nhưng quyền xem khách hàng không kéo theo quyền xem kế hoạch tiếp thị.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-23.1.1` | Người nhận K thất bại ở Zalo ZNS, được gửi dự phòng SMS thành công | Mở dòng sổ cái của K | Thấy hai lần thử: Zalo ZNS — Thất bại vĩnh viễn — lý do; SMS — Đã phân phát; kênh thực tế là SMS |
| `AC-23.1.2` | `CFG-CAMP-74` không gồm SMS; SMS của K là Đồng ý tự khôi phục chưa xác nhận lại | Chiến dịch SMS gửi tới K | K nhận tin; dòng sổ cái ghi căn cứ "kênh không yêu cầu Đồng ý" kèm phiên bản chính sách, không tham chiếu bằng chứng Đồng ý gốc |
| `AC-23.1.3` | `CFG-CAMP-74` không gồm Email; K có Đồng ý email là căn cứ | Chiến dịch Email gửi tới K | K nhận thư; dòng sổ cái tham chiếu bằng chứng Đồng ý của K |
| `AC-23.2.1` | Thư của K đã phân phát, K mở rồi nhấp | Mở dòng sổ cái | Trạng thái vẫn là Đã phân phát; có hai sự kiện Mở và Nhấp kèm thời điểm |
| `AC-23.2.2` | Nhà cung cấp chưa báo phân phát nhưng K đã mở thư | Mở dòng sổ cái | Trạng thái Đã phân phát, tính từ thời điểm mở |
| `AC-23.3.1` | 30 tin quá thời gian chờ không có xác nhận | Chiến dịch hoàn tất | 30 dòng ở Chưa xác định; không được tự động gửi lại; không gửi dự phòng |
| `AC-23.3.2` | Tiếp nối AC-23.3.1, trong thời hạn gửi lại | M chọn gửi lại nhóm Chưa xác định | Hệ thống yêu cầu xác nhận cảnh báo "người nhận có thể nhận hai lần"; chỉ sau khi xác nhận mới gửi |
| `AC-23.4.1` | Quản trị viên | Tìm cách sửa trạng thái một dòng sổ cái | Không có thao tác sửa hay xóa |
| `AC-23.5.1` | Người dùng không có quyền xuất sổ cái | Mở sổ cái | Không có nút xuất |
| `AC-23.6.1` | K nhận chiến dịch "Khuyến mãi Tháng 9" | Tư vấn viên mở hồ sơ K | Dòng thời gian có mục chiến dịch; tư vấn viên không mở được chiến dịch |

---

#### FEAT-24 — Phân loại Lỗi Gửi

**Mô tả nghiệp vụ:** Chuẩn hóa các mã lỗi khác nhau của từng nhà cung cấp thành một danh mục lỗi nghiệp vụ thống nhất, để biết lỗi nào thử lại được, lỗi nào phải ghi ngược về hồ sơ khách hàng, lỗi nào kích hoạt dự phòng.

**Quy tắc nghiệp vụ:**

- **`BR-24.1` (Danh mục lỗi nghiệp vụ):** Mỗi lỗi nhà cung cấp báo về được quy về một nhóm, và mỗi nhóm có hệ quả xác định:

  | Nhóm lỗi | Ví dụ | Loại | Thử lại tự động | Kích hoạt dự phòng | Ghi ngược về hồ sơ khách hàng |
  | --- | --- | --- | :---: | :---: | --- |
  | **Điểm đến không tồn tại** | Email không tồn tại, số điện thoại không có thực | Vĩnh viễn | Không | Có | Điểm đến → Không tiếp cận được (`BR-28.1`) |
  | **Không có tài khoản trên nền tảng** | Số không đăng ký Zalo/WhatsApp | Vĩnh viễn | Không | Có | Không (số vẫn dùng được cho SMS) |
  | **Người nhận chặn doanh nghiệp** | Người nhận đã chặn tài khoản Zalo/WhatsApp của doanh nghiệp | Vĩnh viễn | Không | **Không** | Từ chối nhận tin trên kênh đó (`BR-26.4`) |
  | **Nội dung bị từ chối** | Mẫu sai tham số, nội dung vi phạm chính sách nền tảng | Vĩnh viễn | Không | Không | Không — tính vào dấu hiệu tự động tạm dừng |
  | **Hộp thư tạm thời không nhận** | Hộp thư đầy, máy chủ nhận tạm từ chối | Tạm thời | Có | Không | Không — chỉ đếm để tạm ngừng trong phân hệ Chiến dịch (`BR-28.2`) |
  | **Lỗi phía nhà cung cấp / mạng** | Quá thời gian, nhà cung cấp bận, giới hạn tốc độ | Tạm thời | Có | Không | Không |
  | **Bị từ chối do danh tiếng người gửi** | Máy chủ nhận từ chối vì chính sách hoặc vì người gửi nằm trong danh sách chặn | Vĩnh viễn cho lượt này | Không | Không | Không — lỗi phía doanh nghiệp, không phải của điểm đến; tính vào dấu hiệu tự động tạm dừng (`BR-20.1` mục 7) |
  | **Người nhận tắt tin tiếp thị trên nền tảng** | Người nhận đã dùng chức năng của nền tảng để ngừng nhận tin tiếp thị từ doanh nghiệp | Vĩnh viễn | Không | **Không** | Từ chối nhận tin trên kênh đó (`BR-26.4`) |
  | **Nền tảng giới hạn theo người nhận** | Nền tảng từ chối vì người nhận đã nhận đủ số tin tiếp thị mà nền tảng cho phép | Vĩnh viễn cho lượt này | Không | Không | Không |
  | **Không phân phát được — chưa rõ lý do** | Nền tảng báo không phân phát được bằng một mã gộp nhiều nguyên nhân (không có tài khoản, ứng dụng cũ, hoặc người nhận đã chặn) | Vĩnh viễn cho lượt này | Không | **Không** | Không |
  | **Không đủ hạn mức / bị đình chỉ** | Hạn mức gói, đình chỉ dịch vụ | Tạm thời | Không | Không | Không — kích hoạt tự động tạm dừng (`FEAT-20`) |

  **Lý do nghiệp vụ:** "Người nhận chặn doanh nghiệp" không kích hoạt dự phòng vì đó là tín hiệu người nhận không muốn nghe doanh nghiệp — chuyển sang SMS để "tới bằng được" là hành vi đúng kiểu khiến khách hàng khiếu nại. Ngược lại "không có tài khoản trên nền tảng" không nói gì về ý muốn của người nhận nên dự phòng là hợp lý. Khi nền tảng không phân biệt được hai trường hợp này (mã lỗi gộp), hệ thống xếp vào "chưa rõ lý do" và **không** dự phòng — vì có thể chính là người đã chặn doanh nghiệp. Hệ quả có chủ đích: với nền tảng thường trả mã gộp (như WhatsApp), dự phòng ít khi được kích hoạt; đây là giới hạn phải nói rõ với khách hàng khi bán tính năng dự phòng. Lỗi do danh tiếng người gửi không được ghi ngược về hồ sơ vì địa chỉ không hỏng; ghi ngược sẽ làm mất oan hàng loạt khách hàng khi tên miền bị chặn tạm thời.

- **`BR-24.2` (Mã lỗi gốc vẫn được giữ):** Dòng sổ cái lưu cả nhóm lỗi nghiệp vụ và nguyên văn mã/thông điệp lỗi của nhà cung cấp, để đối soát khi tranh chấp với nhà cung cấp.

  **Lý do nghiệp vụ:** Nhóm lỗi nghiệp vụ phục vụ quyết định thử lại hay ghi ngược; nguyên văn mã lỗi của nhà cung cấp là bằng chứng duy nhất khi đối soát cước hoặc tranh chấp trách nhiệm với nhà cung cấp.

- **`BR-24.3` (Lỗi chưa được phân loại):** Mã lỗi chưa có trong bảng ánh xạ được xếp vào nhóm "Lỗi phía nhà cung cấp / mạng" (tạm thời, không ghi ngược), và được thống kê riêng để đội vận hành bổ sung ánh xạ.

  **Lý do nghiệp vụ:** Xếp lỗi lạ vào nhóm vĩnh viễn có nguy cơ đánh dấu oan hàng loạt điểm đến là Không tiếp cận được — một sai lầm khó sửa vì điểm đến bị loại khỏi mọi chiến dịch sau.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-24.1.1` | Chiến dịch Zalo ZNS có dự phòng SMS; nền tảng báo K đã chặn tài khoản doanh nghiệp | Hệ thống xử lý | K không được gửi dự phòng; kênh Zalo ZNS của K chuyển Từ chối nhận tin với nguồn "Người nhận chặn doanh nghiệp" |
| `AC-24.1.2` | Nền tảng báo số của K không có tài khoản Zalo | Hệ thống xử lý | Dự phòng SMS được kích hoạt; số điện thoại của K không bị đánh dấu Không tiếp cận được |
| `AC-24.1.3` | Nền tảng WhatsApp báo không phân phát được bằng mã gộp nhiều nguyên nhân | Hệ thống xử lý | Dòng ghi "Không phân phát được — chưa rõ lý do"; không gửi dự phòng; điểm đến không bị đánh dấu |
| `AC-24.1.4` | Máy chủ nhận từ chối 300 thư vì danh tiếng người gửi | Hệ thống xử lý | 300 dòng Thất bại vĩnh viễn nhóm "Bị từ chối do danh tiếng người gửi"; không email nào bị chuyển Không tiếp cận được |
| `AC-24.1.5` | Nền tảng WhatsApp báo K đã tắt tin tiếp thị từ doanh nghiệp | Hệ thống xử lý | Kênh WhatsApp của K chuyển Từ chối nhận tin, nguồn "Người nhận tắt tin tiếp thị trên nền tảng"; không gửi dự phòng |
| `AC-24.2.1` | Một dòng Thất bại vĩnh viễn | Mở chi tiết | Thấy cả nhóm lỗi nghiệp vụ và mã lỗi gốc của nhà cung cấp |
| `AC-24.3.1` | Nhà cung cấp trả một mã lỗi chưa có trong ánh xạ | Hệ thống xử lý | Dòng ở Thất bại tạm thời; điểm đến không bị đánh dấu; mã lỗi xuất hiện trong thống kê "lỗi chưa phân loại" |

---

#### FEAT-25 — Thử lại Tin Thất bại

**Mô tả nghiệp vụ:** Tự động thử lại các lỗi tạm thời, và cho phép người có quyền chủ động gửi lại cho các tin chưa tới trong một thời hạn hợp lý.

**Quy tắc nghiệp vụ:**

- **`BR-25.1` (Thử lại tự động):** Tin Thất bại tạm thời được tự động thử lại tối đa `CFG-CAMP-15` lần, với khoảng cách giữa các lần theo Phụ lục B.2. Hết số lần mà vẫn thất bại, dòng giữ trạng thái Thất bại tạm thời và được đánh dấu "đã hết lượt thử lại tự động".

  **Lý do nghiệp vụ:** Phần lớn lỗi tạm thời tự hết trong vài phút tới vài giờ; thử lại dồn dập ngay lập tức làm nhà cung cấp coi là gửi rác, còn không thử lại thì mất tin một cách không cần thiết.

- **`BR-25.2` (Gửi lại thủ công):** Người có quyền Phát sóng chiến dịch được chọn gửi lại cho nhóm **Thất bại tạm thời** (đã hết lượt tự động), nhóm **Chưa xác định** (sau khi xác nhận cảnh báo tại `BR-23.3`), hoặc nhóm Thất bại vĩnh viễn **"Bị từ chối do danh tiếng người gửi"** (sau khi Quản trị viên xác nhận nguyên nhân phía người gửi đã được khắc phục — vì lỗi nằm ở doanh nghiệp, không ở điểm đến), trong vòng `CFG-CAMP-16` kể từ khi chiến dịch Hoàn tất (với chuỗi nuôi dưỡng: kể từ khi lượt gửi của bước đó có kết quả cuối cùng). Các nhóm Thất bại vĩnh viễn khác, Bị loại trừ, Đã hủy trước khi gửi **không** gửi lại được.

  **Lý do nghiệp vụ:** Thông điệp tiếp thị có tính thời điểm; gửi lại sau nhiều ngày gần như chắc chắn là gửi nội dung đã lỗi thời. Thất bại vĩnh viễn và bị loại trừ không gửi lại được vì nguyên nhân không thay đổi, hoặc chính là lý do không được phép gửi.

  Gửi lại thủ công không đưa chiến dịch ra khỏi trạng thái Hoàn tất: lượt gửi lại có tiến độ riêng hiển thị trên chiến dịch, và người có quyền Phát sóng chiến dịch tạm dừng hoặc hủy được riêng lượt gửi lại đó.

- **`BR-25.3` (Gửi lại vẫn qua mọi kiểm tra):** Mọi lần gửi lại (tự động hay thủ công) đều qua kiểm tra tại thời điểm gửi (`BR-08.4`), khung giờ yên lặng, ngày không gửi (`FEAT-43` — tin gửi lại tự hoãn, không chặn việc tạo lượt gửi lại), dừng khẩn cấp và đình chỉ dịch vụ (bước 2 `BR-17.8`); gửi lại thủ công thêm đối chiếu hạn mức và ngân sách cho khối lượng gửi lại (bước 3–4 `BR-17.8`). Gửi lại dùng **đúng phiên bản nội dung** người đó lẽ ra đã nhận, không dùng phiên bản mới hơn.

  **Lý do nghiệp vụ:** Giữa lần gửi đầu và lần gửi lại, người nhận có thể đã từ chối; và phiên bản mới hơn chưa từng được duyệt cho người đó.

- **`BR-25.4` (Không gửi hai lần):** Nếu trong lúc chờ gửi lại, nhà cung cấp báo muộn rằng tin trước đã phân phát, lần gửi lại bị hủy.

  **Lý do nghiệp vụ:** Nguyên tắc 3 (Mục 2.4).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-25.1.1` | `CFG-CAMP-15` = 3; nhà cung cấp nghẽn khiến 100 tin thất bại tạm thời | Chờ | Mỗi tin được thử lại tối đa 3 lần; tin nào thành công chuyển Đã phân phát |
| `AC-25.2.1` | Chiến dịch Hoàn tất 10 giờ trước, `CFG-CAMP-16` = 72 giờ; còn 40 tin Thất bại tạm thời | Quản lý bấm "Gửi lại tin thất bại" | 40 tin được đưa vào gửi lại |
| `AC-25.2.2` | Chiến dịch Hoàn tất 80 giờ trước | Tìm thao tác gửi lại | Không còn; giao diện báo đã quá thời hạn |
| `AC-25.2.3` | Có 15 tin Thất bại vĩnh viễn | Chọn gửi lại | 15 tin không được đưa vào |
| `AC-25.2.4` | Lượt gửi lại 40 tin đang chạy trên chiến dịch Hoàn tất | M tạm dừng rồi hủy lượt gửi lại | Lượt gửi lại dừng; chiến dịch vẫn Hoàn tất; các tin chưa gửi lại giữ trạng thái cũ |
| `AC-25.2.5` | 300 tin Thất bại vĩnh viễn nhóm "Bị từ chối do danh tiếng người gửi"; Quản trị viên xác nhận đã khắc phục | M chọn gửi lại nhóm này trong thời hạn | 300 tin được đưa vào gửi lại, qua đầy đủ kiểm tra tại thời điểm gửi |
| `AC-25.3.1` | Một trong 40 người hủy nhận tin trước khi gửi lại | Gửi lại | Người đó Bị loại trừ tại thời điểm gửi |
| `AC-25.4.1` | Tin của K ở Thất bại tạm thời, đang chờ lần thử lại; nhà cung cấp báo muộn là đã phân phát | Tới giờ thử lại | Không gửi lại; dòng chuyển Đã phân phát |

---

### Nhóm H — Tuân thủ & Bảo vệ Danh tiếng Gửi

#### FEAT-26 — Hủy Nhận tin trên Mọi Kênh

**Mô tả nghiệp vụ:** Người nhận luôn có cách từ chối nhận tin tiếp thị ngay trong tin họ nhận được, trên mọi kênh, và việc từ chối có hiệu lực ngay lập tức.

**Quy tắc nghiệp vụ:**

- **`BR-26.1` (Cơ chế từ chối bắt buộc trên từng kênh):**
  - **Email:** chân thư và bản văn bản thuần có liên kết hủy nhận tin (`BR-11.1`); thư đồng thời hỗ trợ cơ chế **hủy nhận bằng một thao tác từ giao diện của hộp thư** mà các nhà cung cấp hộp thư lớn yêu cầu với thư gửi hàng loạt.
  - **SMS:** nội dung có hướng dẫn từ chối bằng cách nhắn một từ khóa tới **đầu số nhận từ chối** của doanh nghiệp — một đầu số hai chiều do đối tác cung cấp dịch vụ tin nhắn cấp, khai báo cùng tên thương hiệu (`FEAT-10`). Tin nhắn tới đầu số này được đưa về hệ thống và xử lý theo `BR-26.3`. Hệ thống tự động thêm hướng dẫn, người soạn không xóa được. Tên thương hiệu chưa khai báo đầu số nhận từ chối thì không dùng được cho chiến dịch; đầu số nhận từ chối bị gỡ hoặc ngừng nhận tin sau khi đã duyệt là lỗi chặn nhóm (b), và chiến dịch đang gửi bị tự động tạm dừng bảo vệ. Với Thông báo dịch vụ (`FEAT-45`), hướng dẫn tự động thêm là hướng dẫn phản đối của `BR-45.8` cho mục đích (1), (3), (5); mục đích (2), (4) không có cơ chế phản đối nên **không** thêm hướng dẫn, không cần đầu số nhận từ chối và không bị chặn hay tạm dừng vì đầu số.
  - **WhatsApp / Zalo ZNS:** mẫu tiếp thị bắt buộc có nút hoặc hướng dẫn từ chối nhận tin; mẫu không có là lỗi chặn nhóm (a). **Zalo OA:** hệ thống tự thêm hướng dẫn từ chối vào nội dung, người soạn không xóa được. Với Thông báo dịch vụ, cách thêm hướng dẫn trên WhatsApp, Zalo ZNS và Zalo OA theo `BR-45.8`: có cho mục đích (1), (3), (5), không có cho mục đích (2), (4). Với cả ba kênh, ngoài ra hệ thống nhận diện từ khóa từ chối trong tin trả lời (`BR-26.3`) và sự kiện người nhận chặn doanh nghiệp do nền tảng báo về.

  **Lý do nghiệp vụ:** Pháp luật chống tin rác và các nhà cung cấp hộp thư đều yêu cầu người nhận từ chối được ngay trong chính tin đã nhận, không phải tìm cách liên hệ doanh nghiệp. Một tên thương hiệu SMS không có đầu số nhận được tin trả lời thì hướng dẫn "Soạn TC" là hướng dẫn không làm được.

- **`BR-26.2` (Trang hủy nhận tin của email):** Liên kết hủy nhận tin mở một trang **không cần đăng nhập**, không yêu cầu người nhận nhập lại email, hiển thị các lựa chọn theo (`contacts-srs.md`, `BR-30.1`): mặc định chỉ hủy nhận tin trên kênh email; tùy chọn "Hủy nhận tin trên toàn bộ mọi kênh". Liên kết hoạt động **vô thời hạn**, kể cả khi chiến dịch đã lưu trữ, hết thời hạn lưu chi tiết sổ cái, hay người nhận đã bị gộp hồ sơ (khi đó áp lên Bản ghi Chính). Ánh xạ từ liên kết hủy nhận tin (và cơ chế hủy một thao tác của hộp thư) tới **điểm đến** được lưu tách khỏi sổ cái và **không** bị khử định danh theo thời hạn lưu sổ cái (`BR-32.3`); khi điểm đến bị gỡ khỏi hồ sơ (bị sửa, bị xóa, hồ sơ bị xóa) — kể cả khi đang Đồng ý — ánh xạ **không mất** mà được giữ ở dạng biến đổi một chiều có khóa như dấu vết chặn gửi; một lượt bấm hủy nhận tin về sau trên thư cũ vẫn được trang ghi nhận thành công và **tạo dấu vết chặn gửi** cho điểm đến đó (`BR-32.4`), và nếu dòng sổ cái còn định danh thì lựa chọn "toàn bộ mọi kênh" áp cho hồ sơ đã nhận thư; nếu không còn xác định được hồ sơ, trang chỉ xác nhận "đã ngừng gửi tiếp thị tới địa chỉ này" và nêu cách liên hệ doanh nghiệp để ngừng các kênh khác — không báo đã hủy mọi kênh. Ánh xạ chỉ bị hủy khi yêu cầu xóa dữ liệu của chủ thể được thực hiện — khi đó dấu vết chặn gửi đã được tạo theo `BR-32.4` (1). Trang không hiển thị bất kỳ thông tin cá nhân nào của người nhận ngoài địa chỉ đang hủy ở dạng đã che một phần. Lựa chọn trên trang áp cho **điểm đến** (theo `BR-26.4`).

  **Lý do nghiệp vụ:** Bắt đăng nhập hay nhập lại email là cách làm khó người nhận mà các nhà cung cấp hộp thư coi là vi phạm, và dẫn người nhận tới nút "Báo cáo thư rác" — gây hại cho danh tiếng gửi hơn nhiều. Không hiển thị thông tin cá nhân vì liên kết có thể bị chuyển tiếp cho người khác.

- **`BR-26.3` (Từ khóa từ chối và lời từ chối trong tin trả lời):**
  - **Tin trả lời chiến dịch:** tin người nhận gửi tới đầu số nhận từ chối (SMS); hoặc tin trên WhatsApp, Zalo OA, hay thư trả lời email về hộp thư nhận trả lời đã khai báo (`BR-10.4`), đến trong thời gian `CFG-CAMP-58` kể từ lượt gửi chiến dịch gần nhất tới người đó **và** không thuộc một hội thoại đang có tư vấn viên xử lý (Mục 1.4, theo khoảng `CFG-CAMP-59`). Zalo ZNS chưa có kênh tiếp nhận tin trả lời (Mục 1.6, giả định 2). Định nghĩa này **chỉ** quyết định tin nào đủ điều kiện **tự động ghi nhận**; mọi tin khách gửi tới doanh nghiệp qua các kênh trên — bất kể thời điểm, bất kể đang có tư vấn viên xử lý hay không — đều được so khớp để xét luồng **cần người xác nhận** dưới đây.
  - **Ba danh mục do hệ thống duy trì** (Quản trị viên được **thêm**, không xóa mục mặc định): (1) **từ khóa từ chối** (ví dụ "TC", "HUY", "STOP", "KHONG NHAN"), trong đó mỗi từ khóa được đánh dấu **đa nghĩa** nếu trùng một từ thông dụng có nghĩa khác hoặc một tên riêng (ví dụ "HUY" — cũng là "hủy đơn", "hủy lịch", tên người) — "STOP", "TC", "KHONG NHAN" không đa nghĩa; (2) **cụm từ từ chối** (ví dụ "hủy nhận tin giúp tôi", "đừng gửi quảng cáo nữa", "unsubscribe me"); (3) **từ chỉ tiếp thị** đi kèm từ khóa đa nghĩa (tối thiểu "QC", "KM", "quảng cáo", "khuyến mãi", "tin nhắn", "nhận tin", "email", "đăng ký"; doanh nghiệp thêm theo `CFG-CAMP-68`). Quản trị viên thêm từ khóa phải đánh dấu đa nghĩa hay không — hệ thống đề xuất đa nghĩa nếu từ khóa trùng danh mục tên riêng hoặc là một phần của từ thông dụng. Đánh dấu của từ khóa mặc định bị khóa; đánh dấu của từ khóa do Quản trị viên thêm chỉ đổi được khi có Người phụ trách Bảo vệ Dữ liệu cùng chấp thuận, vì nó quyết định tin đi luồng tự động ghi nhận hay luồng cần người xác nhận. Từ khóa thêm vào không được trùng danh mục từ thông dụng tại Phụ lục B.2 (ví dụ "OK", "CÓ", "VÂNG") và phải đạt độ dài tối thiểu tại Phụ lục B.2. So khớp bỏ khoảng trắng thừa, dấu câu, không phân biệt hoa thường và dấu tiếng Việt, theo từ hoặc cụm từ đứng riêng.
  - **Phần được so khớp:** phần khách tự viết trong thân tin, và tiêu đề thư nếu khách đã sửa khác tiêu đề thư gốc. Phần trích dẫn được nhận biết bằng cách **đối chiếu với nội dung chính tin mà hệ thống đã gửi** tới người đó (ví dụ thư gốc mà ứng dụng thư tự chèn khi bấm Trả lời, kèm chân thư có liên kết hủy nhận tin) và bị bỏ qua, cùng chữ ký và chân thư của khách; nếu không tách chắc chắn được, hệ thống so khớp toàn bộ tin trừ các đoạn trùng đúng tin gốc. Áp cho mọi kênh có trích dẫn tin.
  - **Tự động ghi nhận:** tin trả lời chiến dịch qua SMS (đầu số nhận từ chối), WhatsApp, Zalo OA mà **toàn bộ phần được so khớp** trùng đúng một từ khóa từ chối được ghi nhận Từ chối nhận tin trên kênh đó — trừ từ khóa đa nghĩa trên kênh hội thoại (WhatsApp, Zalo OA), vốn đi luồng cần người xác nhận.
  - **Cần người xác nhận** — tin không được tự động ghi nhận nhưng thuộc một trong các loại sau:
    1. Tin tới **đầu số SMS nhận từ chối** có chứa bất kỳ từ khóa từ chối nào, kể cả từ khóa đa nghĩa nằm trong câu (ví dụ "HUY QC", "huy giup em", "hủy đơn giúp tôi") — đầu số này chỉ dùng để nhận từ chối.
    2. Tin trên các kênh khác có chứa một cụm từ từ chối; một từ khóa không đa nghĩa; hoặc một từ khóa đa nghĩa đi cùng một từ chỉ tiếp thị ("hủy nhận tin", "huy QC").
    3. Tin chỉ gồm đúng một từ khóa nhưng không phải tin trả lời chiến dịch (ví dụ "STOP" gửi sau cửa sổ thời gian, hoặc trong hội thoại đang có tư vấn viên xử lý), hoặc chỉ gồm đúng một từ khóa đa nghĩa trên kênh hội thoại.
    4. Thư trả lời email mà phần được so khớp chỉ gồm đúng một từ khóa, hoặc tiêu đề do khách sửa chứa từ khóa hay cụm từ từ chối (ví dụ tiêu đề "Unsubscribe", thân thư trống).

    **Không** gắn nhãn: từ khóa đa nghĩa nằm trong một câu trên kênh hội thoại hay email mà không đi cùng từ chỉ tiếp thị (ví dụ "cho mình hủy đơn hôm qua nhé"); thư trả lời tự động (thông báo vắng mặt, xác nhận đã nhận thư — nhận diện theo các dấu hiệu tiêu chuẩn của thư tự động); tin không chứa từ khóa hay cụm từ từ chối. Thư trả lời tự động không được chuyển vào Hộp thư Hội thoại như một hội thoại mới.
  - **Xử lý tin cần người xác nhận:** tin mang nhãn "Có thể là lời từ chối nhận tin" ở nơi nó được đưa tới theo `BR-35.1` (Hộp thư Hội thoại, hoặc công việc liên hệ lại với kênh không có tiếp nhận hội thoại). **Ngay khi tin được gắn nhãn**, **giá trị** điểm đến đó bị **tạm chặn** trên **mọi kênh dùng giá trị đó** (với số điện thoại: SMS, Zalo ZNS, WhatsApp; với tài khoản Zalo OA hay email: chính kênh đó) mọi lượt gửi tiếp thị (lượt gửi trong chiến dịch một lần bị loại theo lý do 5 kèm chú thích "tạm chặn chờ xác nhận"; bước chuỗi nuôi dưỡng được hoãn như giới hạn tần suất, `BR-39.4`); tạm chặn **giữ nguyên cho tới khi có người xử lý**; nếu sau `CFG-CAMP-44` chưa ai xử lý, hệ thống leo thang cảnh báo tới người có quyền quản trị **Xử lý tin gắn nhãn từ chối** (Mục 5, ghi chú 2) và Người phụ trách Bảo vệ Dữ liệu — hệ thống **không** tự tạo lời từ chối thay cho khách. Ngoại lệ duy nhất cho việc tạm chặn: tin **chỉ** gồm một từ khóa đa nghĩa là tên riêng, gửi trong hội thoại đang có tư vấn viên xử lý, được gắn nhãn nhưng không tạm chặn, vì tư vấn viên đang trực tiếp đọc tin.
  - **Hai thao tác trên tin gắn nhãn:** **Ghi nhận từ chối nhận tin** hoặc **Không phải lời từ chối**, cho từng tin hoặc **hàng loạt** trên danh sách tin đang gắn nhãn (mỗi tin vẫn ghi nhật ký riêng, `NFR-08`). "Không phải lời từ chối" chỉ gỡ tạm chặn, không nâng mức đồng thuận — trạng thái đồng thuận của khách chưa từng bị đổi; thao tác hàng loạt chỉ áp cho "Ghi nhận từ chối nhận tin"; "Không phải lời từ chối" luôn làm từng tin. Ai thực hiện được:
    - Người đang xử lý một hội thoại **đang mở** chứa tin đó: Ghi nhận từ chối nhận tin theo ngoại lệ thu hẹp của tuyến hỗ trợ (`contacts-srs.md`, `BR-35.4` (a), Mục 2 Nguyên tắc 3); và Không phải lời từ chối — thao tác này không thuộc ngoại lệ đó vì không đổi đồng thuận, chỉ gỡ tạm chặn do chính phân hệ Chiến dịch đặt, và được giao cho người đang đọc tin trong ngữ cảnh hội thoại. Cả hai không cần quyền sửa hồ sơ khách hàng. Hội thoại **không đóng được** khi còn tin gắn nhãn chưa xử lý; tương tự, công việc liên hệ lại không hoàn tất được khi còn tin gắn nhãn chưa xử lý.
    - **Phạm vi của danh sách tin đang gắn nhãn:** mỗi người có quyền Xử lý tin gắn nhãn từ chối chỉ thấy trong danh sách — và chỉ nhận cảnh báo leo thang cho — tin của khách hàng thuộc **tập khách hàng xem được** của chính họ theo `BR-05.4`, đúng tập dùng cho đối tượng chiến dịch. Tin của khách hàng ngoài tập đó không hiển thị với họ; Người phụ trách Bảo vệ Dữ liệu (hoặc Chủ sở hữu nếu doanh nghiệp không chỉ định) thấy toàn bộ danh sách, nhận cảnh báo leo thang cho mọi tin, và là người xử lý các tin không nằm trong tập xem được của bất kỳ người có quyền Xử lý tin gắn nhãn từ chối nào.
    - Ngoài trường hợp trên (công việc liên hệ lại của SMS, hoặc danh sách tin đang gắn nhãn): **Ghi nhận từ chối nhận tin** do người có quyền sửa hồ sơ khách hàng đó (`contacts-srs.md`, `BR-30.10`), người có quyền quản trị Xử lý tin gắn nhãn từ chối, Người phụ trách Bảo vệ Dữ liệu hoặc Chủ sở hữu thực hiện; **Không phải lời từ chối** chỉ do Người phụ trách Bảo vệ Dữ liệu thực hiện — hoặc Chủ sở hữu nếu doanh nghiệp không chỉ định Người phụ trách Bảo vệ Dữ liệu (`contacts-srs.md`, Mục 2.2) — từng tin một, kèm lý do bắt buộc. Người được giao công việc liên hệ lại mà không có quyền sửa hồ sơ thì chuyển công việc cho những người này.
    - Hội thoại được coi là **đang mở** khi ở trạng thái Đang mở, Tạm hoãn hoặc Chờ đóng theo (`omnichat-srs.md`, Mục vòng đời hội thoại) — tức mọi trạng thái trước Đã giải quyết, kể cả khi phân hệ Hộp thư Hội thoại không coi Chờ đóng là trạng thái riêng. Khi còn tin gắn nhãn chưa xử lý, hội thoại **không chuyển được** sang Đã giải quyết hay Đã đóng bằng bất kỳ đường nào — người dùng đánh dấu, đóng hàng loạt, ép đóng hay tự động đóng (`omnichat-srs.md`, `BR-10.5`, `BR-10.9`); việc tự động đóng được hoãn và hiển thị lý do "còn tin gắn nhãn chưa xử lý" (`omnichat-srs.md`, `BR-10.8`), và hệ thống không gửi tin hỏi khách trước khi đóng (`omnichat-srs.md`, `BR-10.3`) tới khách có tin gắn nhãn.
  - **Ghi nhận trên bất kỳ tin nào của hội thoại đang mở:** ngoài tin được gắn nhãn, người đang xử lý một hội thoại **đang mở** có thao tác **Ghi nhận từ chối nhận tin** trên bất kỳ tin nào của hội thoại đó, cho lời từ chối không khớp danh mục, cùng căn cứ `BR-35.4` (a); sau khi hội thoại đóng, lời từ chối được ghi nhận qua hồ sơ khách hàng theo (`contacts-srs.md`, `BR-30.10`).
  - **Báo cáo:** cả hai thao tác được thống kê theo từng người dùng (tách riêng thao tác hàng loạt, và tách riêng lượt "Không phải lời từ chối" do chính tư vấn viên đang xử lý hội thoại đó thực hiện), kèm số điểm đến đang tạm chặn chờ xác nhận quá `CFG-CAMP-44` và số tin chứa từ khóa đa nghĩa trong câu không được gắn nhãn theo từng kênh, trong báo cáo định kỳ gửi Người phụ trách Bảo vệ Dữ liệu, cùng cách với báo cáo Liên lạc 1-1 của (`contacts-srs.md`, `BR-30.8`), để hậu kiểm việc gỡ, ghi nhận sai hàng loạt hay bỏ sót lời từ chối.
  - Hệ thống gửi một tin xác nhận ngắn (thuộc nhóm Giao dịch & Dịch vụ, không chứa bất kỳ nội dung quảng bá nào, không tính cước người nhận) cho lượt ghi nhận tự động và lượt người dùng ghi nhận, nếu nền tảng cho phép. Mọi tin trả lời được xử lý theo `BR-35.1` (thư trả lời tự động không tạo hội thoại hay công việc); tin được ghi nhận tự động mang nhãn "Người nhận đã từ chối nhận tin".

  **Lý do nghiệp vụ:** Khớp đúng toàn bộ nội dung mới tự động ghi nhận, để một khách nhắn "cho mình hủy đơn hôm qua nhé" không mất quyền nhận tin mà họ vẫn muốn; từ đa nghĩa trong một câu không gây gắn nhãn trên kênh hội thoại để chăm sóc khách hàng hằng ngày không bị tạm chặn oan — nhưng trên đầu số SMS nhận từ chối thì mọi từ khóa đều được xét, vì khách nhắn tới đầu số đó chỉ để từ chối và thường viết "HUY QC", "huy giup em". Chỉ so khớp phần khách tự viết vì thư trả lời luôn kèm thư gốc có chữ "Hủy nhận tin" ở chân thư; nhận biết phần trích dẫn bằng cách đối chiếu với tin đã gửi, và khi không chắc thì so khớp nhiều hơn chứ không ít hơn. Tạm chặn tới khi có người xử lý đã đáp ứng nghĩa vụ ngừng gửi, nên hệ thống không cần — và không được — tự tạo một bằng chứng từ chối có thể sai; không gắn nhãn mọi thư trả lời email vì một bản tin gửi hàng chục nghìn địa chỉ luôn sinh ra hàng nghìn thư báo vắng mặt. Lời từ chối không khớp đúng mẫu, đến muộn hay nằm giữa một hội thoại vẫn không bị bỏ qua vì mọi tin của khách đều được so khớp, và người đang xử lý hội thoại ghi nhận được trên bất kỳ tin nào; cửa sổ thời gian chỉ giới hạn việc tự động ghi nhận, để một chữ "STOP" gửi giữa một cuộc trò chuyện về việc khác không tự thành bằng chứng từ chối. Không cho đóng hội thoại khi còn tin gắn nhãn để hai thao tác của tuyến hỗ trợ không mất hiệu lực giữa chừng. Trong hội thoại, người gỡ tạm chặn là người đang đọc lời khách nói trong ngữ cảnh, làm từng tin một và được thống kê theo người dùng; ngoài hội thoại không còn ngữ cảnh đó, nên chỉ Người phụ trách Bảo vệ Dữ liệu — người không chịu chỉ tiêu về lượng tin gửi — được gỡ. Không cho xóa từ khóa mặc định vì đó là các từ khóa mà hướng dẫn trong chính tin nhắn đã yêu cầu người nhận dùng.

- **`BR-26.4` (Hiệu lực tức thì, áp lên điểm đến, ghi bằng chứng):** Từ chối nhận tin có hiệu lực ngay khi hệ thống ghi nhận: mọi lượt gửi tiếp thị **bắt đầu sau thời điểm đó** tới điểm đến này đều bị chặn, kể cả các lượt còn lại của chính chiến dịch đang gửi (`BR-08.4`). Lời từ chối được ghi cho **mọi hồ sơ khách hàng đang giữ điểm đến đó** trên kênh tương ứng (hoặc mọi kênh nếu người nhận chọn toàn bộ mọi kênh). Mỗi lần ghi nhận có bằng chứng theo (`contacts-srs.md`, `BR-30.3`), nguồn chọn từ danh mục A.8 của tài liệu đó: "Liên kết hủy nhận tin trong chiến dịch", "Hủy nhận bằng một thao tác từ giao diện hộp thư", "Từ khóa từ chối trong tin trả lời", "Tư vấn viên ghi nhận từ chối", "Người nhận chặn doanh nghiệp trên nền tảng nhắn tin", "Người nhận tắt tin tiếp thị trên nền tảng", hoặc "Khiếu nại thư rác" (`FEAT-27`), kèm mã chiến dịch.

  **Lý do nghiệp vụ:** Người bấm hủy là người đang cầm hộp thư hay chiếc điện thoại đó; ghi lời từ chối chỉ cho một hồ sơ trong số các hồ sơ dùng chung điểm đến sẽ để các hồ sơ còn lại tiếp tục gửi tới đúng người vừa từ chối.

- **`BR-26.5` (Không có đường nâng đồng thuận từ chiến dịch):** Không thao tác nào trong phân hệ Chiến dịch — kể cả người nhận nhấp liên kết, trả lời tin, hay bấm lại liên kết hủy nhận tin — nâng trạng thái Từ chối lên Đồng ý. Người nhận muốn nhận tin lại phải đi qua đường đăng ký của phân hệ Khách hàng theo (`contacts-srs.md`, `BR-30.10`), trong đó lời từ chối phát sinh từ chiến dịch chỉ được nâng lại khi chính chủ xác nhận qua chính điểm đến đó.

  **Lý do nghiệp vụ:** Coi lượt nhấp hay trả lời là "đồng ý lại" là cách đồng thuận bị bào mòn âm thầm; và một nhân viên tự khai "khách đã đồng ý lại" không được mạnh hơn chính lời từ chối khách vừa bấm; (`contacts-srs.md`, `BR-30.10`) chỉ cho phép hành vi đăng ký chủ động của chính chủ thể.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-26.1.1` | Soạn SMS tiếp thị | Xem nội dung sẽ gửi | Có hướng dẫn từ chối tự động thêm; không xóa được; được tính vào độ dài |
| `AC-26.1.2` | Chiến dịch Email | Người nhận dùng nút hủy nhận của giao diện hộp thư | Kênh email của người đó Từ chối nhận tin; nguồn "Hủy nhận bằng một thao tác từ giao diện hộp thư" |
| `AC-26.1.3` | Tên thương hiệu SMS chưa khai báo đầu số nhận từ chối | Chọn tên thương hiệu cho chiến dịch | Không chọn được, kèm lý do |
| `AC-26.2.1` | K bấm liên kết hủy nhận tin, giữ lựa chọn mặc định | Xác nhận | Kênh email của K Từ chối nhận tin; kênh SMS, Zalo ZNS của K không đổi |
| `AC-26.2.2` | K chọn "Hủy nhận tin trên toàn bộ mọi kênh" | Xác nhận | Mọi kênh của K Từ chối nhận tin nhóm Tiếp thị |
| `AC-26.2.3` | Chiến dịch đã gửi 18 tháng trước, đã lưu trữ | K bấm liên kết hủy nhận tin trong thư cũ | Trang vẫn hoạt động và ghi nhận từ chối |
| `AC-26.2.4` | Liên kết hủy nhận tin được mở trên một máy khác | Xem trang | Không có tên, số điện thoại hay thông tin nào của K; email hiển thị dạng che một phần |
| `AC-26.2.5` | `CFG-CAMP-20` = 24 tháng; chiến dịch hoàn tất 25 tháng trước, sổ cái đã khử định danh; K vẫn Đồng ý nhận email (dịch chuyển đồng hồ `NFR-11`) | K bấm liên kết hủy nhận tin trong thư cũ | Lời từ chối được ghi cho email của K |
| `AC-26.2.6` | Nhân viên thay email cũ của K (đang Đồng ý) bằng email mới | K bấm liên kết hủy nhận tin trong một thư cũ gửi tới email cũ | Trang báo thành công; email cũ có dấu vết chặn gửi; nếu email cũ được nhập lại sau đó thì bị loại "Từ chối nhận tin" |
| `AC-26.3.1` | K trả lời SMS "TC" tới đầu số nhận từ chối (kênh SMS không có tiếp nhận hội thoại) | Hệ thống xử lý | Kênh SMS của K Từ chối nhận tin; K nhận tin xác nhận; sổ cái ghi sự kiện Trả lời; không tạo công việc liên hệ lại (`BR-35.1`) |
| `AC-26.3.2` | K nhắn "hủy đơn giúp tôi" tới đầu số SMS nhận từ chối (kênh SMS không có tiếp nhận hội thoại); người được giao công việc liên hệ lại có quyền sửa hồ sơ K | Hệ thống xử lý | Đồng thuận không đổi; điểm đến tạm chặn; công việc liên hệ lại của K mang nhãn "Có thể là lời từ chối nhận tin"; người xử lý bấm Ghi nhận từ chối ngay trên công việc thì kênh SMS của K Từ chối nhận tin |
| `AC-26.3.3` | Danh mục từ khóa từ chối | Quản trị viên xóa từ khóa mặc định "TC" | Không cho phép; chỉ xóa được từ khóa do Quản trị viên thêm |
| `AC-26.3.4` | K trả lời Zalo "đừng nhắn quảng cáo nữa nhé"; tin vào hộp thư kèm nhãn lúc 10:00; `CFG-CAMP-44` = 12 giờ; không ai xử lý | Một bước chuỗi nuôi dưỡng Zalo của K tới hạn lúc 14:00; tới 22:00 | Bước lúc 14:00 không được gửi (tạm chặn); lúc 22:00 người có quyền Xử lý tin gắn nhãn từ chối và Người phụ trách Bảo vệ Dữ liệu nhận cảnh báo leo thang; kênh Zalo của K vẫn tạm chặn, đồng thuận không bị tự đổi |
| `AC-26.3.5` | Danh mục từ khóa từ chối | Quản trị viên thêm từ khóa "OK" | Không cho phép; từ khóa trùng danh mục từ thông dụng |
| `AC-26.3.6` | Bản tin email gửi 50.000 địa chỉ; 1.500 thư báo vắng mặt tự động trả về | Hệ thống xử lý | Không thư nào được gắn nhãn "Có thể là lời từ chối"; không điểm đến nào bị tạm chặn; không có hội thoại mới trong Hộp thư Hội thoại |
| `AC-26.3.7` | K trả lời email "Cảm ơn, tôi sẽ ghé cửa hàng" | Hệ thống xử lý | Không gắn nhãn; thư vào Hộp thư Hội thoại như tin trả lời bình thường |
| `AC-26.3.8` | Tư vấn viên bấm "Không phải lời từ chối" cho tin "đừng nhắn quảng cáo nữa" | Người phụ trách Bảo vệ Dữ liệu mở báo cáo định kỳ | Thao tác có trong nhật ký và trong báo cáo, kèm người thực hiện và nội dung tin |
| `AC-26.3.9` | K trả lời Zalo OA "hủy đơn giúp tôi" (kênh có tiếp nhận hội thoại) | Hệ thống xử lý | Không gắn nhãn, không tạm chặn — "HUY" đa nghĩa nằm trong câu, không đi cùng từ chỉ tiếp thị; tin vào Hộp thư Hội thoại như tin trả lời bình thường |
| `AC-26.3.10` | K nhắn "hủy đơn giúp tôi" tới đầu số SMS nhận từ chối; tin đang chờ xác nhận | Một bước chuỗi Zalo ZNS gửi tới cùng số của K tới hạn | Bước bị hoãn (số điện thoại đang tạm chặn trên mọi kênh gửi theo số) |
| `AC-26.3.11` | Tư vấn viên đang trò chuyện với K trên Zalo OA và hỏi tên; K trả lời "Huy" | Hệ thống xử lý | Tin được gắn nhãn "Có thể là lời từ chối nhận tin"; không ghi nhận từ chối, không tạm chặn — tên riêng trong hội thoại đang có tư vấn viên xử lý |
| `AC-26.3.12` | K nhận tin chiến dịch WhatsApp 1 giờ trước, không có hội thoại đang xử lý; K trả lời "HUY" | Hệ thống xử lý | Chuyển sang luồng cần người xác nhận (từ khóa đa nghĩa trên kênh hội thoại); điểm đến tạm chặn |
| `AC-26.3.13` | K nhận tin chiến dịch WhatsApp 4 ngày trước; `CFG-CAMP-58` = 72 giờ | K nhắn "STOP" (toàn bộ tin) | Không ghi nhận tự động; tin được gắn nhãn "Có thể là lời từ chối nhận tin", số điện thoại của K bị tạm chặn cho tới khi có người xử lý |
| `AC-26.3.14` | Tư vấn viên đang hỗ trợ K về đơn hàng trên WhatsApp | K nhắn "à mà đừng gửi quảng cáo nữa nhé" | Tin được gắn nhãn và số điện thoại của K bị tạm chặn; leo thang theo `CFG-CAMP-44` nếu không ai xử lý |
| `AC-26.3.15` | Tư vấn viên chỉ có quyền xem hồ sơ; K nhắn "từ giờ khỏi gửi gì cho tôi" (không khớp danh mục, không gắn nhãn) | Tư vấn viên chọn Ghi nhận từ chối nhận tin trên tin đó | K chuyển Từ chối nhận tin trên kênh WhatsApp; nhật ký ghi tư vấn viên và tin làm bằng chứng |
| `AC-26.3.16` | K nhận bản tin email 1 ngày trước | K trả lời thư "please unsubscribe me, thanks" | Thư được gắn nhãn "Có thể là lời từ chối nhận tin"; email của K bị tạm chặn |
| `AC-26.3.17` | K bấm Trả lời bản tin email; ứng dụng thư tự chèn thư gốc có chân thư "Hủy nhận tin" | K viết "Cảm ơn, tôi sẽ ghé cửa hàng" | Không gắn nhãn, không tạm chặn — phần trích dẫn thư gốc không được so khớp |
| `AC-26.3.18` | K đang được hỗ trợ trên Zalo OA | K nhắn "cho mình hủy đơn hôm qua nhé" | Không gắn nhãn, không tạm chặn — "HUY" là từ khóa đa nghĩa, chỉ tính khi đứng một mình hoặc đi cùng từ chỉ tiếp thị |
| `AC-26.3.19` | K nhắn "HUY QC" tới đầu số SMS nhận từ chối | Hệ thống xử lý | Không tự động ghi nhận (không trùng đúng một từ khóa); tin được gắn nhãn, số điện thoại của K bị tạm chặn |
| `AC-26.3.20` | K trả lời bản tin email 1 ngày trước, đổi tiêu đề thành "Unsubscribe", thân thư trống ngoài phần trích dẫn thư gốc | Hệ thống xử lý | Thư được gắn nhãn "Có thể là lời từ chối nhận tin"; email của K bị tạm chặn |
| `AC-26.3.21` | K nhận tin chiến dịch WhatsApp 1 giờ trước, không có hội thoại đang xử lý | K trả lời có trích dẫn tin chiến dịch, phần tự gõ là "STOP" | Tự động ghi nhận Từ chối nhận tin trên WhatsApp — phần trích dẫn không được so khớp |
| `AC-26.3.22` | Hội thoại đang mở có một tin gắn nhãn chưa xử lý | Tư vấn viên bấm đóng hội thoại | Không cho đóng; giao diện yêu cầu xử lý tin gắn nhãn trước |
| `AC-26.3.23` | Công việc liên hệ lại của SMS có tin gắn nhãn; người được giao không có quyền sửa hồ sơ K | Người được giao mở công việc | Không có hai thao tác trên tin; có thao tác chuyển công việc cho người có quyền sửa hồ sơ K, người có quyền Xử lý tin gắn nhãn từ chối, Người phụ trách Bảo vệ Dữ liệu hoặc Chủ sở hữu |
| `AC-26.3.24` | Danh sách tin đang gắn nhãn có 40 tin tới đầu số SMS nhận từ chối (công việc liên hệ lại) | Quản lý Marketing (có quyền Xử lý tin gắn nhãn từ chối) chọn cả 40 tin và tìm thao tác "Không phải lời từ chối" | Không có; người đó chỉ Ghi nhận từ chối được (từng tin hoặc hàng loạt) |
| `AC-26.3.25` | Như trên | Người phụ trách Bảo vệ Dữ liệu chọn một tin, bấm "Không phải lời từ chối" không nhập lý do | Không cho phép; nhập lý do thì được, tạm chặn của điểm đến đó được gỡ, nhật ký ghi lý do |
| `AC-26.3.26` | Hội thoại Zalo OA có tin gắn nhãn chưa xử lý; chính sách tự động đóng sau 24 giờ không hoạt động | Hết 24 giờ | Hội thoại không bị đóng; hiển thị lý do "còn tin gắn nhãn chưa xử lý"; điểm đến vẫn tạm chặn |
| `AC-26.3.27` | Tháng qua có 120 tin Zalo OA và 30 thư email chứa "HUY" trong câu, không đi cùng từ chỉ tiếp thị | Người phụ trách Bảo vệ Dữ liệu mở báo cáo định kỳ | Báo cáo nêu 120 (Zalo OA) và 30 (Email) tin chứa từ khóa đa nghĩa trong câu không được gắn nhãn |
| `AC-26.3.28` | Giám sát viên (vai trò của `omnichat-srs.md`) chọn 50 hội thoại để đóng hàng loạt; 2 hội thoại còn tin gắn nhãn chưa xử lý | Xác nhận đóng hàng loạt | 48 hội thoại đóng; 2 hội thoại giữ nguyên, hiển thị lý do "còn tin gắn nhãn chưa xử lý" |
| `AC-26.3.29` | Hội thoại còn tin gắn nhãn chưa xử lý | Tư vấn viên chọn ép đóng ngay | Không cho phép; giao diện yêu cầu xử lý tin gắn nhãn trước |
| `AC-26.3.30` | Quản trị viên thêm từ khóa "DUNG" | Lưu từ khóa | Phải chọn đa nghĩa hay không; hệ thống đề xuất "đa nghĩa" vì trùng một phần từ thông dụng ("dừng", "dùng", "đúng"); lưu không được nếu chưa chọn |
| `AC-26.3.31` | Từ khóa mặc định "HUY" đang được đánh dấu đa nghĩa | Quản trị viên thử bỏ đánh dấu | Không cho phép; đánh dấu của từ khóa mặc định bị khóa |
| `AC-26.3.33` | Doanh nghiệp bật "Thu về khách hàng của đơn vị mình" và không có nguồn nới khác; Quản lý Marketing M có quyền Xử lý tin gắn nhãn từ chối; tin gắn nhãn của khách K thuộc đơn vị Kinh doanh và của khách L thuộc đơn vị Marketing | M mở danh sách tin đang gắn nhãn | M chỉ thấy tin của L; tin của K hiển thị với Người phụ trách Bảo vệ Dữ liệu, người nhận cảnh báo leo thang cho K khi quá `CFG-CAMP-44` |
| `AC-26.3.32` | Công việc liên hệ lại của SMS còn một tin gắn nhãn chưa xử lý | Người được giao bấm hoàn tất công việc | Không cho phép; giao diện yêu cầu xử lý tin gắn nhãn trước |
| `AC-26.4.1` | K nhận email của chiến dịch C lúc 9:00 và hủy nhận tin lúc 9:10; K cũng đang ở trong một chuỗi nuôi dưỡng email có bước tới hạn lúc 10:00 | Tới 10:00 | K không nhận bước của chuỗi; bằng chứng có nguồn "Liên kết hủy nhận tin trong chiến dịch" và mã chiến dịch C |
| `AC-26.4.2` | H1 và H2 cùng một email; người nhận bấm liên kết hủy nhận tin trong thư gửi cho H1 | Hệ thống xử lý | Kênh email của cả H1 và H2 Từ chối nhận tin |
| `AC-26.5.1` | K đã Từ chối nhận tin email; K nhấp một liên kết trong thư cũ | Kiểm tra đồng thuận của K | Vẫn Từ chối nhận tin |

---

#### FEAT-27 — Khiếu nại Thư rác

**Mô tả nghiệp vụ:** Khi người nhận đánh dấu tin là thư rác hoặc báo cáo tin nhắn với nền tảng, và nhà cung cấp báo lại cho doanh nghiệp, hệ thống xử lý như một lời từ chối mạnh.

**Quy tắc nghiệp vụ:**

- **`BR-27.1` (Khiếu nại là từ chối):** Mỗi khiếu nại nhà cung cấp báo về được ghi là sự kiện Khiếu nại trên dòng sổ cái và ghi Từ chối nhận tin trên kênh đó theo `BR-26.4` với nguồn "Khiếu nại thư rác". Khiếu nại không có chiều ngược: nhà cung cấp có rút lại hay không, lời từ chối vẫn giữ (`BR-26.5`).

  **Lý do nghiệp vụ:** Người đánh dấu thư rác là người rõ ràng không muốn nhận; gửi tiếp cho họ là cách nhanh nhất để bị nhà cung cấp chặn toàn bộ tên miền.
- **`BR-27.2` (Khiếu nại được tính vào ngưỡng bảo vệ):** Khiếu nại được tính vào tỷ lệ khiếu nại của chiến dịch (`BR-20.1`) và của Không gian làm việc (`KPI-02`). Khiếu nại và chặn doanh nghiệp **không** tính vào tỷ lệ hủy nhận tin (`BR-33.2`); mỗi loại có chỉ số riêng.

  **Lý do nghiệp vụ:** Gộp khiếu nại vào hủy nhận tin làm che mất tín hiệu nghiêm trọng hơn nhiều.
- **`BR-27.3` (Cảnh báo cấp Không gian làm việc):** Khi tỷ lệ khiếu nại của Không gian làm việc trong khoảng trượt tại Phụ lục B.2 (Mục 2.3) vượt `CFG-CAMP-39`, Quản trị viên, Quản lý Marketing và Người phụ trách Bảo vệ Dữ liệu nhận cảnh báo, và danh mục kiểm tra của **mọi** chiến dịch mới hiển thị cảnh báo này cho tới khi tỷ lệ về dưới ngưỡng.

  **Lý do nghiệp vụ:** Nhà cung cấp hộp thư và nhà mạng đánh giá danh tiếng theo toàn bộ lưu lượng của người gửi, không theo từng chiến dịch. Một tỷ lệ khiếu nại cao kéo dài là dấu hiệu tệp khách hàng hoặc thói quen gửi đang có vấn đề hệ thống, cần người quản lý nhìn thấy.

- **`BR-27.4` (Khiếu nại không đo được):** Một số nhà cung cấp hộp thư lớn không báo khiếu nại theo từng người nhận mà chỉ cung cấp số liệu tổng hợp. Vì vậy mẫu số của tỷ lệ khiếu nại (chiến dịch và Không gian làm việc) **chỉ gồm tin đã phân phát tới các nhà cung cấp có báo khiếu nại theo từng người**, và cỡ mẫu tối thiểu `CFG-CAMP-14` cho ngưỡng khiếu nại được tính trên chính mẫu số này; báo cáo hiển thị riêng tỷ lệ tin "không đo được khiếu nại". Khi Không gian làm việc kết nối công cụ theo dõi danh tiếng của nhà cung cấp hộp thư, số liệu tổng hợp của công cụ đó được hiển thị cạnh chỉ số của hệ thống và được dùng cho cảnh báo cấp Không gian làm việc (`BR-27.3`); khi tỷ lệ thư rác của tên miền gửi mà công cụ báo trong khoảng xét tại Phụ lục B.2 vượt `CFG-CAMP-12`, mọi chiến dịch Email mới dùng tên miền đó gặp lỗi chặn vận hành (`BR-16.1` nhóm (b)) và chiến dịch Email đang gửi bị tự động tạm dừng bảo vệ. Lỗi chặn hết khi số liệu mới của công cụ về dưới ngưỡng. Vì công cụ chỉ có số liệu khi có lượng gửi, Quản trị viên cùng Người phụ trách Bảo vệ Dữ liệu được duyệt một **đợt gửi phục hồi**: một chiến dịch Email giới hạn khối lượng và chỉ tới khách hàng đã có tương tác gần đây, theo giới hạn tại Phụ lục B.2; đợt phục hồi được ghi nhật ký.

  **Lý do nghiệp vụ:** Tính khiếu nại trên toàn bộ tin phân phát trong khi phần lớn nhà cung cấp không báo về sẽ cho ra một tỷ lệ thấp giả tạo — cơ chế tự động tạm dừng không bao giờ bật trong khi tên miền đang bị chặn thật.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-27.1.1` | Nhà cung cấp báo K đánh dấu thư là thư rác | Hệ thống xử lý | Dòng sổ cái của K có sự kiện Khiếu nại; kênh email của K Từ chối nhận tin, nguồn "Khiếu nại thư rác" |
| `AC-27.2.1` | Chiến dịch có 3 khiếu nại trên 1.000 phân phát | Mở báo cáo chiến dịch và báo cáo Không gian làm việc | Tỷ lệ khiếu nại chiến dịch 0,3%; 3 khiếu nại được cộng vào tỷ lệ khoảng trượt 30 ngày của Không gian làm việc |
| `AC-27.2.2` | Chiến dịch có 3 khiếu nại và 5 lượt hủy nhận tin trên 1.000 phân phát tới nhà cung cấp có báo khiếu nại | Mở báo cáo | Tỷ lệ khiếu nại 0,3%, tỷ lệ hủy nhận tin 0,5%, hai chỉ số tách riêng |
| `AC-27.3.1` | Tỷ lệ khiếu nại 30 ngày của Không gian làm việc là 0,15% | Nhân viên Marketing mở danh mục kiểm tra của một chiến dịch mới | Có cảnh báo về tỷ lệ khiếu nại của Không gian làm việc |
| `AC-27.4.1` | Chiến dịch 10.000 thư phân phát, trong đó 7.000 thư tới nhà cung cấp không báo khiếu nại theo từng người; có 6 khiếu nại | Mở báo cáo | Tỷ lệ khiếu nại tính trên 3.000 (0,2%); hiển thị "70% tin không đo được khiếu nại" |
| `AC-27.4.2` | Chiến dịch phân phát 500 thư, chỉ 50 thư tới nhà cung cấp có báo khiếu nại; 1 khiếu nại; `CFG-CAMP-14` = 500 | Khiếu nại tới | Không tạm dừng (cỡ mẫu đo được mới là 50) |
| `AC-27.4.3` | Công cụ theo dõi danh tiếng báo tỷ lệ thư rác của tên miền gửi là 0,4%; `CFG-CAMP-12` = 0,2% | Người soạn gửi phê duyệt một chiến dịch Email dùng tên miền đó | Lỗi chặn vận hành nêu số liệu của công cụ |
| `AC-27.4.4` | Tên miền đang bị chặn vì tỷ lệ thư rác 0,4%, không có số liệu mới vì đã ngừng gửi | Quản trị viên và Người phụ trách Bảo vệ Dữ liệu duyệt đợt gửi phục hồi | Một chiến dịch Email tới khách có tương tác gần đây, khối lượng trong giới hạn phục hồi, được gửi đi; chiến dịch vượt giới hạn phục hồi vẫn bị chặn |

---

#### FEAT-28 — Xử lý Điểm đến Hỏng

**Mô tả nghiệp vụ:** Phát hiện và ghi ngược về hồ sơ khách hàng những điểm đến không bao giờ nhận được tin, để doanh nghiệp không tiếp tục trả tiền và làm hỏng danh tiếng khi gửi vào đó.

**Quy tắc nghiệp vụ:**

- **`BR-28.1` (Hỏng vĩnh viễn):** Lỗi nhóm "Điểm đến không tồn tại" (`BR-24.1`) chuyển điểm đến đó sang **Không tiếp cận được** trên hồ sơ khách hàng (`contacts-srs.md`, `BR-29.2`) ngay khi nhận được, kèm nguồn là mã chiến dịch và lỗi gốc.

  **Lý do nghiệp vụ:** Tiếp tục gửi vào địa chỉ không tồn tại là dấu hiệu rõ nhất để nhà cung cấp coi doanh nghiệp là người gửi rác.

- **`BR-28.2` (Hỏng tạm thời lặp lại):** Một điểm đến gặp Thất bại tạm thời nhóm "Hộp thư tạm thời không nhận" ở `CFG-CAMP-11` **chiến dịch liên tiếp** (không có lần phân phát thành công nào xen giữa) bị **tạm ngừng gửi tiếp thị** trong phân hệ Chiến dịch trong `CFG-CAMP-47` (loại với lý do 4 kèm chú thích "tạm ngừng do lỗi lặp lại"); trạng thái tiếp cận trên hồ sơ khách hàng không đổi. Hết thời gian tạm ngừng, điểm đến được gửi lại bình thường; nếu lại gặp đủ `CFG-CAMP-11` lần liên tiếp thì tạm ngừng lần nữa. Một lần phân phát thành công bất kỳ (kể cả tin Giao dịch & Dịch vụ từ phân hệ khác) xóa trạng thái tạm ngừng.

  **Lý do nghiệp vụ:** Hộp thư "tạm đầy" suốt nhiều tháng trên thực tế thường là hộp thư đã bỏ, gửi tiếp làm giảm danh tiếng gửi. Nhưng "Không tiếp cận được" của phân hệ Khách hàng là trạng thái kỹ thuật không đảo được — dùng nó cho một lỗi tạm thời sẽ khóa vĩnh viễn một địa chỉ có thể vẫn đang được dùng. Tạm ngừng có thời hạn giữ được cả hai.

- **`BR-28.3` (Chiều ngược):** Phân hệ Chiến dịch **không bao giờ** tự đưa một điểm đến từ Không tiếp cận được về trạng thái khác; theo (`contacts-srs.md`, `BR-18.2`), đây là trạng thái kỹ thuật không đảo được. Khi khách hàng đổi sang một điểm đến mới, điểm đến mới có trạng thái riêng, không kế thừa trạng thái hỏng của điểm đến cũ.

  **Lý do nghiệp vụ:** Không tiếp cận được là kết luận rằng chính điểm đến đó không nhận được tin; đảo trạng thái — dù tự động hay bằng tay — sẽ đưa địa chỉ hỏng quay vòng vào mọi chiến dịch và làm hỏng danh tiếng người gửi. Khách muốn nhận lại tin thì cung cấp điểm đến mới.

- **`BR-28.4` (Số điện thoại bị cấp lại cho chủ mới):** Khi nhà mạng hoặc nền tảng báo một số điện thoại đã đổi chủ (thu hồi và cấp lại), hoặc nền tảng báo tài khoản gắn với số đó đã thay đổi, điểm đến chuyển sang **Không còn hiệu lực** (`contacts-srs.md`, `BR-29.2`) và mọi Đồng ý nhận tin trên các kênh dùng số đó của hồ sơ **không còn là căn cứ gửi tiếp thị** — hệ thống yêu cầu phân hệ Khách hàng hạ về trạng thái chưa có Đồng ý có bằng chứng. Nếu sau đó nhà mạng hoặc nền tảng xác nhận đã báo nhầm, điểm đến chỉ được khôi phục Đồng ý khi chính chủ xác nhận lại qua số đó (`BR-26.5`).

  **Lý do nghiệp vụ:** Đồng ý là của người chủ cũ; gửi tiếp thị tới chủ mới của số điện thoại là gửi cho người chưa bao giờ đồng ý.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-28.1.1` | Nhà cung cấp báo email của K không tồn tại | Hệ thống xử lý | Email của K chuyển Không tiếp cận được; chiến dịch Email tiếp theo loại K với lý do "Điểm đến Không tiếp cận được" |
| `AC-28.2.1` | `CFG-CAMP-11` = 3, `CFG-CAMP-47` = 90 ngày; email của K báo hộp thư đầy ở 2 chiến dịch liên tiếp | Chiến dịch thứ 3 cũng báo hộp thư đầy | Email của K bị tạm ngừng gửi tiếp thị 90 ngày; trạng thái tiếp cận trên hồ sơ không đổi; sau 90 ngày (dịch chuyển đồng hồ `NFR-11`) gửi lại bình thường |
| `AC-28.2.2` | Email của K báo hộp thư đầy ở chiến dịch 1, phân phát thành công ở chiến dịch 2, báo đầy ở chiến dịch 3 và 4 | Sau chiến dịch 4 | Chưa bị tạm ngừng (chuỗi liên tiếp bị ngắt) |
| `AC-28.3.1` | K cập nhật email mới trên hồ sơ | Chiến dịch Email mới | K khả dụng với email mới |
| `AC-28.4.1` | Nền tảng báo số điện thoại của K đã được cấp cho chủ mới | Hệ thống xử lý | Số đó chuyển Không còn hiệu lực; chiến dịch SMS tiếp theo loại K |

---

#### FEAT-29 — Khung Giờ Yên lặng

**Mô tả nghiệp vụ:** Không gửi tin tiếp thị vào khung giờ người nhận đang nghỉ ngơi.

**Quy tắc nghiệp vụ:**

- **`BR-29.1` (Khung giờ và kênh áp dụng):** Không gian làm việc có một khung giờ yên lặng (`CFG-CAMP-06`) áp dụng cho các kênh tại `CFG-CAMP-07`, tính theo giờ địa phương của từng người nhận (Mục 2.3). Khi `CFG-CAMP-71` bật, khung giờ yên lặng còn được xét **đồng thời** theo giờ của Không gian làm việc; tin chỉ được gửi khi nằm ngoài khung theo cả hai giờ. Doanh nghiệp tự đặt khung giờ và các kênh áp dụng theo chính sách và pháp luật áp dụng cho mình (`BR-30.1`).

  **Lý do nghiệp vụ:** Khung giờ gửi vừa là lựa chọn trải nghiệm vừa có thể là nghĩa vụ của doanh nghiệp ở thị trường của họ, nên thuộc cấu hình của doanh nghiệp. Tùy chọn xét thêm theo giờ của Không gian làm việc để một thông tin múi giờ sai trên hồ sơ không thành đường gửi quảng cáo lúc nửa đêm tới khách ở cùng thị trường với doanh nghiệp.

- **`BR-29.2` (Hoãn, không bỏ):** Khi tới lượt gửi cho một người mà giờ địa phương của người đó đang trong khung giờ yên lặng, tin của người đó được **hoãn** tới thời điểm kết thúc khung giờ yên lặng gần nhất của người đó (không muộn hơn hạn chót gửi, `BR-18.4`), và qua lại đầy đủ kiểm tra tại thời điểm gửi khi tới giờ. Chiến dịch chỉ Hoàn tất khi mọi tin bị hoãn đã có kết quả cuối cùng.

  **Lý do nghiệp vụ:** Bỏ hẳn tin rơi vào giờ yên lặng làm một phần người nhận không bao giờ nhận được thông điệp chỉ vì múi giờ của họ; kiểm tra lại khi tới giờ vì trong đêm người nhận có thể đã từ chối.

- **`BR-29.3` (Không dồn cục khi hết giờ yên lặng):** Các tin được hoãn không được phát ra cùng lúc ngay khi hết giờ yên lặng mà đi theo nhịp gửi của chiến dịch (`FEAT-22`).

  **Lý do nghiệp vụ:** Phát hàng chục nghìn tin đúng 08:00 vừa vượt ngưỡng an toàn của nhà cung cấp, vừa đổ dồn tin trả lời vào đội tư vấn ngay đầu ca.

- **`BR-29.4` (Cảnh báo khi hẹn giờ và phát sóng ngay):** Nếu giờ hẹn hoặc thời điểm phát sóng ngay — hoặc bất kỳ thời điểm nào trong khoảng gửi dự kiến tính theo nhịp gửi — rơi vào khung giờ yên lặng của người nhận, danh mục kiểm tra hiển thị cảnh báo (`BR-16.2`) kèm ước tính số người bị hoãn, thời điểm họ sẽ nhận, và gợi ý đặt hạn chót gửi (`BR-18.4`) nếu chưa đặt. Với phát sóng ngay, cảnh báo này hiển thị trong hộp xác nhận phát sóng và người bấm phát sóng phải xác nhận.

  **Lý do nghiệp vụ:** Người soạn thường không nghĩ tới người nhận ở múi giờ khác; con số ước tính giúp họ tự chọn lại giờ gửi.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-29.1.1` | Khung giờ yên lặng đang là 22:00–08:00 | Chủ sở hữu đặt 23:00–06:00, Người phụ trách Bảo vệ Dữ liệu cùng chấp thuận | Lưu được; không có giá trị nào bị khóa; thay đổi ghi nhật ký |
| `AC-29.1.2` | SMS thuộc `CFG-CAMP-07` | Chủ sở hữu bỏ SMS khỏi `CFG-CAMP-07`, Người phụ trách Bảo vệ Dữ liệu cùng chấp thuận | Lưu được; SMS gửi sau đó không còn bị hoãn theo khung giờ yên lặng |
| `AC-29.1.3` | Không gian làm việc ở GMT+7, `CFG-CAMP-71` bật; khung giờ yên lặng 22:00–08:00; hồ sơ người nhận ghi múi giờ GMT+3; chiến dịch SMS gửi lúc 23:00 giờ Không gian làm việc (19:00 tại GMT+3) | Tới lượt người đó | Tin không được gửi lúc 23:00 (trong khung theo giờ Không gian làm việc); 08:00 giờ Không gian làm việc là 04:00 tại GMT+3, vẫn trong khung theo giờ người nhận; tin được gửi lúc 08:00 GMT+3, tức 12:00 giờ Không gian làm việc |
| `AC-29.2.1` | Khung giờ 22:00–08:00; chiến dịch SMS hẹn 22:30; người nhận ở cùng múi giờ Không gian làm việc | Tới giờ hẹn | Không tin nào gửi đi trước 08:00; từ 08:00 bắt đầu gửi theo nhịp |
| `AC-29.2.2` | Không gian làm việc ở GMT+7, `CFG-CAMP-71` bật; khung giờ yên lặng 22:00–08:00; chiến dịch Zalo ZNS gửi 20:00 giờ Không gian làm việc; hồ sơ K ghi múi giờ GMT+9 (22:00 tại chỗ K) | Tới lượt K | Tin của K bị hoãn tới 08:00 giờ GMT+9, tức 06:00 giờ Không gian làm việc — vẫn trong khung theo giờ Không gian làm việc, nên tiếp tục hoãn tới 08:00 giờ Không gian làm việc (10:00 GMT+9) |
| `AC-29.2.3` | Khách K trong nhóm bị hoãn hủy nhận tin lúc 02:00 | 08:00 tới lượt K | K Bị loại trừ tại thời điểm gửi |
| `AC-29.2.4` | Email không thuộc `CFG-CAMP-07` | Chiến dịch Email gửi lúc 23:00 | Thư được gửi ngay |
| `AC-29.2.5` | Khung giờ yên lặng 22:00–08:00; người nhận K ở GMT+7 | Tin tới lượt lúc 07:30 | Hoãn tới 08:00 |
| `AC-29.2.6` | Doanh nghiệp thêm Email vào `CFG-CAMP-07`, khung giờ yên lặng 22:00–08:00; chiến dịch Email tới người nhận ở GMT+7 | Gửi lúc 23:00 | Thư bị hoãn tới 08:00 |
| `AC-29.3.1` | 30.000 tin bị hoãn, nhịp chiến dịch 5.000 tin/giờ | 08:00 | Tin được phát theo nhịp 5.000/giờ, không phát cùng lúc |
| `AC-29.4.1` | Chiến dịch SMS 10.000 người, hẹn 23:00 | Mở danh mục kiểm tra | Có cảnh báo giờ hẹn rơi vào khung giờ yên lặng, ước tính khoảng 10.000 người bị hoãn tới 08:00 |
| `AC-29.4.2` | Chiến dịch SMS 30.000 người hẹn 21:00, nhịp 5.000 tin/giờ, chưa đặt hạn chót; khung giờ yên lặng 22:00–08:00 | Mở danh mục kiểm tra | Cảnh báo ước tính 25.000 người sẽ nhận từ 08:00 tới khoảng 13:00 hôm sau, gợi ý đặt hạn chót gửi |
| `AC-29.4.3` | Chiến dịch SMS 10.000 người Đã duyệt, không hẹn giờ; khung giờ yên lặng 22:00–08:00, nhịp 5.000 tin/giờ | Quản lý Marketing bấm Phát sóng ngay lúc 21:30 | Hộp xác nhận cảnh báo ước tính 7.500 người bị hoãn tới 08:00; chỉ phát sóng sau khi người bấm xác nhận cảnh báo |

---

#### FEAT-30 — Chính sách Gửi Tiếp thị theo Cấu hình Doanh nghiệp

**Mô tả nghiệp vụ:** Các quy tắc về yêu cầu đồng ý trước, nhãn quảng cáo, số tin tối đa tới một điểm đến trong ngày, Danh sách không quảng cáo, khung giờ gửi và căn cứ theo dõi hành vi khác nhau theo thị trường, theo ngành và theo chính sách riêng của từng doanh nghiệp. Hệ thống không duy trì bộ quy tắc pháp lý của quốc gia nào: mỗi quy tắc là một tham số của Không gian làm việc, có giá trị mặc định; doanh nghiệp tự cấu hình cho phù hợp với pháp luật và chính sách áp dụng cho mình, và tự chịu trách nhiệm về cấu hình đó.

**Quy tắc nghiệp vụ:**

- **`BR-30.1` (Chính sách do doanh nghiệp cấu hình và chịu trách nhiệm):** Chính sách gửi tiếp thị của Không gian làm việc gồm các tham số: kênh yêu cầu Đồng ý nhận tin có bằng chứng (`CFG-CAMP-74`, lý do 6 của `BR-08.1`); nhãn quảng cáo (`CFG-CAMP-28`, `BR-30.2`); giới hạn 24 giờ theo điểm đến (`CFG-CAMP-69`, `BR-30.3`); Danh sách không quảng cáo (`CFG-CAMP-70`, `BR-30.5`); khung giờ yên lặng và việc xét thêm theo giờ của Không gian làm việc (`CFG-CAMP-06`, `CFG-CAMP-07`, `CFG-CAMP-71`, `BR-29.1`); xác nhận qua chính điểm đến cho Đồng ý thu qua biểu mẫu (`CFG-CAMP-72`, `BR-09.3`); căn cứ theo dõi hành vi từng người (`CFG-CAMP-73`, `BR-14.4`). Không tham số nào bị hệ thống khóa theo quốc gia. Mỗi thay đổi cần Chủ sở hữu cùng Người phụ trách Bảo vệ Dữ liệu chấp thuận (quy tắc thay thế người thứ hai tại Mục 2.2 khi không chỉ định), người đề xuất ghi lý do, và màn hình xác nhận nêu rõ doanh nghiệp chịu trách nhiệm về việc cấu hình phù hợp với pháp luật áp dụng cho mình; thay đổi được ghi nhật ký (`NFR-08`), tạo một **phiên bản chính sách** mới. Thay đổi **chặt hơn** (thêm kênh yêu cầu Đồng ý, bật Danh sách không quảng cáo, hạ giới hạn, mở rộng khung giờ yên lặng) có hiệu lực ngay với mọi tin chưa gửi, kể cả của chiến dịch và chuỗi đang chạy, vì được xét lại tại thời điểm gửi (`BR-08.4`); thay đổi **lỏng hơn** chỉ áp cho các lượt gửi bắt đầu sau thời điểm đó. Bật nhãn quảng cáo cho một kênh (`CFG-CAMP-28`), bật xác nhận qua điểm đến (`CFG-CAMP-72`) hay bật căn cứ theo dõi (`CFG-CAMP-73`) là thay đổi chặt hơn: tin chưa gửi được thêm nhãn khi gửi (với WhatsApp, Zalo ZNS, chiến dịch dùng mẫu thiếu nhãn bị tự động tạm dừng bảo vệ và phải sửa phiên bản; với SMS và Zalo OA, vì nội dung gửi đi phải đúng bản đã được nhà mạng hoặc nền tảng duyệt (`BR-13.3`, `BR-13.5`), chiến dịch đang gửi bị tự động tạm dừng bảo vệ và phải sửa phiên bản, đăng ký lại hoặc gửi kiểm duyệt lại nội dung có nhãn; chỉ Email được thêm nhãn khi gửi mà không dừng), Đồng ý biểu mẫu chưa xác nhận trở thành lý do 6, và tin chưa gửi đi không kèm cơ chế đo gắn danh tính với người không có căn cứ. Quản trị viên tạo được đề xuất thay đổi, chờ Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu chấp thuận; một trong hai từ chối thì đề xuất đóng lại kèm lý do, chính sách giữ nguyên.

  **Lý do nghiệp vụ:** Pháp luật về tin nhắn và thư quảng cáo khác nhau giữa các quốc gia và thay đổi theo thời gian; một bộ quy tắc khóa cứng trong hệ thống vừa không đúng cho doanh nghiệp ở thị trường khác, vừa chặn những việc mà pháp luật nơi doanh nghiệp hoạt động cho phép. Người hiểu nghĩa vụ của doanh nghiệp là chính doanh nghiệp; hệ thống cung cấp công cụ thực thi chính sách đó một cách nhất quán. Giữ thẩm quyền hai người vì các tham số này quyết định việc có gửi tới người chưa đồng ý hay không.

- **`BR-30.2` (Nhãn quảng cáo):** Theo `CFG-CAMP-28`, với mỗi kênh doanh nghiệp bật nhãn quảng cáo, hệ thống tự động thêm nhãn doanh nghiệp đã khai báo vào đầu nội dung SMS, đầu tiêu đề email, hoặc vào nội dung Zalo OA; người soạn không xóa hay sửa được nhãn trong chiến dịch; nhãn được tính vào độ dài. Với WhatsApp và Zalo ZNS (nội dung là mẫu đã được nền tảng duyệt, hệ thống không tự chèn được), khi kênh bật nhãn, mẫu tiếp thị phải chứa nhãn; mẫu thiếu nhãn là lỗi chặn nhóm (a) (`BR-16.1`).

  **Lý do nghiệp vụ:** Để người soạn tự thêm nhãn thì sớm muộn sẽ có chiến dịch quên; đặt ở cấp doanh nghiệp để mọi chiến dịch và mọi đường gửi nhất quán.

- **`BR-30.3` (Giới hạn 24 giờ theo điểm đến):** Khi `CFG-CAMP-69` bật, độc lập với giới hạn tần suất (`BR-08.5`), một điểm đến không nhận quá số tin tiếp thị doanh nghiệp đặt trong 24 giờ trượt, theo đơn vị đếm doanh nghiệp chọn (gộp các kênh gửi theo cùng số điện thoại, hoặc theo từng kênh). Cách đếm giống `BR-08.5`: đếm tin Đã phân phát, Đã chuyển nhà cung cấp, Chưa xác định và tin đang gửi dở của chiến dịch khác; không đếm tin Thất bại vĩnh viễn. Không được gửi lại thủ công nhóm Chưa xác định (`BR-25.2`) cho điểm đến chưa qua 24 giờ kể từ lần gửi trước. Với chiến dịch gửi một lần, người chạm giới hạn được **hoãn** tối đa `CFG-CAMP-43` tới khi hết chạm; quá thời gian đó thì bị loại (lý do 13). Miễn trừ tần suất (`BR-08.6`) không áp cho giới hạn này. Nếu giới hạn tần suất chặt hơn, giới hạn chặt hơn được áp dụng.

  **Lý do nghiệp vụ:** Giới hạn theo tuần không ngăn được việc dồn nhiều tin tới một người trong cùng một ngày qua nhiều kênh dùng chung số điện thoại; tách thành một tầng riêng để doanh nghiệp đặt được cả hai.

- **`BR-30.4` (Thời hạn xử lý từ chối):** Quy tắc `BR-26.4` (hiệu lực tức thì) áp cho mọi Không gian làm việc; không tham số nào nới hiệu lực tức thì thành một khoảng chờ.

  **Lý do nghiệp vụ:** Một khoảng chờ vẫn là khoảng thời gian người nhận tiếp tục nhận tin họ vừa từ chối — đúng lúc họ dễ khiếu nại nhất.

- **`BR-30.5` (Danh sách không quảng cáo):** Khi `CFG-CAMP-70` bật, điểm đến thuộc Danh sách không quảng cáo mà doanh nghiệp cấu hình — danh sách doanh nghiệp nạp, hoặc nguồn danh sách bên ngoài doanh nghiệp kết nối — không nhận tin tiếp thị qua các kênh doanh nghiệp chọn, kể cả khi khách hàng có Đồng ý nhận tin với doanh nghiệp; kiểm tra tại thời điểm gửi (`BR-08.4`, lý do 7). Bản danh sách đang dùng cũ hơn tuổi tối đa doanh nghiệp đặt tại `CFG-CAMP-70` thì áp nguyên tắc không chắc thì không gửi (`NFR-06`).

  **Lý do nghiệp vụ:** Đăng ký vào một danh sách không nhận quảng cáo là lời từ chối người nhận gửi tới cả thị trường; doanh nghiệp dùng danh sách nào, cho kênh nào là quyết định của doanh nghiệp, nhưng đã dùng thì phải được đối chiếu ở mọi lượt gửi.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-30.1.1` | Chủ sở hữu mở cấu hình chính sách gửi tiếp thị | Xem | Thấy mọi tham số của `BR-30.1` với giá trị hiện tại và giá trị mặc định; không tham số nào bị khóa |
| `AC-30.1.2` | Chủ sở hữu tắt yêu cầu Đồng ý có bằng chứng cho kênh Email (`CFG-CAMP-74`) | Lưu | Cần Người phụ trách Bảo vệ Dữ liệu cùng chấp thuận và lý do; màn hình xác nhận nêu doanh nghiệp chịu trách nhiệm tuân thủ; sau khi chấp thuận, lượt gửi Email bắt đầu sau đó không còn loại người Chưa có đồng thuận theo lý do 6; người Từ chối nhận tin vẫn bị loại |
| `AC-30.1.3` | Quản trị viên không phải Chủ sở hữu | Thử đổi `CFG-CAMP-69` | Không đổi trực tiếp được; tạo được đề xuất thay đổi chờ Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu chấp thuận |
| `AC-30.1.4` | Đề xuất tắt `CFG-CAMP-72` đang chờ | Người phụ trách Bảo vệ Dữ liệu từ chối kèm lý do | Đề xuất đóng lại; chính sách giữ nguyên; người đề xuất được báo lý do |
| `AC-30.1.5` | Doanh nghiệp không chỉ định Người phụ trách Bảo vệ Dữ liệu; Chủ sở hữu O đề xuất tắt `CFG-CAMP-71` | O tìm cách tự chấp thuận | Không được; cần một Quản trị viên khác O chấp thuận theo quy tắc thay thế người thứ hai |
| `AC-30.1.6` | `CFG-CAMP-74` không gồm Email; K ở trạng thái Chưa có đồng thuận trên email | Chiến dịch Email gửi tới K | K nhận thư; dòng sổ cái ghi căn cứ "kênh không yêu cầu Đồng ý" kèm phiên bản chính sách |
| `AC-30.1.7` | `CFG-CAMP-72` tắt; K điền biểu mẫu đồng ý nhận SMS nhưng chưa xác nhận qua mã gửi tới số | Chiến dịch SMS gửi tới K | K được coi là có Đồng ý có bằng chứng và nhận tin |
| `AC-30.1.8` | `CFG-CAMP-73` tắt; K có Đồng ý theo điều khoản không nêu mục đích đo lường | K mở và nhấp thư tiếp thị | Lượt mở và nhấp được ghi gắn danh tính K |
| `AC-30.1.9` | `CFG-CAMP-71` tắt; Không gian làm việc ở GMT+7, khung giờ yên lặng 22:00–08:00; hồ sơ người nhận ghi GMT+3 | Chiến dịch SMS gửi lúc 23:00 giờ Không gian làm việc (19:00 tại GMT+3) | Tin được gửi ngay (chỉ xét giờ người nhận) |
| `AC-30.1.10` | `CFG-CAMP-74` không gồm Email, `CFG-CAMP-73` tắt; K ở Chưa có đồng thuận trên email | K mở và nhấp thư tiếp thị | Chỉ ghi nhận tổng hợp, không có sự kiện mở hay nhấp gắn danh tính K |
| `AC-30.1.11` | Chiến dịch Email dàn trải đang gửi ngày thứ 2; `CFG-CAMP-74` chưa gồm Email; K ở Chưa có đồng thuận, chưa tới lượt | Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu thêm Email vào `CFG-CAMP-74` | Tới lượt K, K bị loại lý do 6; phần đã gửi không bị ảnh hưởng |
| `AC-30.1.12` | Chiến dịch WhatsApp đang gửi dùng mẫu không có nhãn; `CFG-CAMP-28` chưa bật nhãn cho WhatsApp | Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu bật nhãn cho WhatsApp | Chiến dịch tự động tạm dừng bảo vệ với lý do mẫu thiếu nhãn; tiếp tục được sau khi sửa phiên bản sang mẫu có nhãn |
| `AC-30.1.13` | Chiến dịch SMS đang gửi, nội dung đã duyệt không có nhãn; `CFG-CAMP-28` chưa bật nhãn cho SMS | Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu bật nhãn "QC" cho SMS | Chiến dịch tự động tạm dừng bảo vệ; tiếp tục được sau khi sửa phiên bản và nội dung có nhãn được nhà mạng duyệt |
| `AC-30.1.14` | Chiến dịch Email đang gửi; `CFG-CAMP-28` chưa bật nhãn cho Email | Bật nhãn "[QC]" cho Email | Chiến dịch tiếp tục; mọi thư chưa gửi có "[QC]" ở đầu tiêu đề |
| `AC-30.1.15` | Chiến dịch SMS Đã duyệt hẹn ngày mai, nội dung đã duyệt không có nhãn | Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu bật nhãn "QC" cho SMS | Chiến dịch về Nháp với lỗi chặn nhóm (a); người phụ trách được báo phải sửa phiên bản và đăng ký lại nội dung có nhãn |
| `AC-30.1.16` | Chiến dịch Zalo OA đang gửi, nội dung đã qua kiểm duyệt của nền tảng không có nhãn; `CFG-CAMP-28` chưa bật nhãn cho Zalo OA | Bật nhãn cho Zalo OA | Chiến dịch tự động tạm dừng bảo vệ; tiếp tục được sau khi sửa phiên bản và nội dung có nhãn được nền tảng kiểm duyệt |
| `AC-30.1.19` | Chiến dịch Zalo OA Đã duyệt hẹn ngày mai, nội dung đã qua kiểm duyệt của nền tảng không có nhãn | Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu bật nhãn cho Zalo OA | Chiến dịch về Nháp với lỗi chặn nhóm (a); người phụ trách được báo phải sửa phiên bản và gửi kiểm duyệt lại nội dung có nhãn |
| `AC-30.2.1` | `CFG-CAMP-28` bật nhãn SMS "QC"; soạn SMS "Ưu đãi 20%…" | Xem trước nội dung | Nội dung là "QC Ưu đãi 20%…"; không xóa được "QC" |
| `AC-30.2.2` | `CFG-CAMP-28` bật nhãn email "[QC]"; soạn email tiêu đề "Ưu đãi mùa thu" | Gửi thử | Tiêu đề nhận được là "[Gửi thử] [QC] Ưu đãi mùa thu" |
| `AC-30.2.3` | `CFG-CAMP-28` bật nhãn cho WhatsApp; mẫu tiếp thị không có nhãn quảng cáo | Mở danh mục kiểm tra | Lỗi chặn nhóm (a) yêu cầu dùng mẫu có nhãn quảng cáo |
| `AC-30.2.4` | Doanh nghiệp tắt nhãn cho SMS trong `CFG-CAMP-28` | Soạn và gửi SMS | Tin không có nhãn; thay đổi cấu hình có trong nhật ký kèm người đề xuất và người chấp thuận |
| `AC-30.3.1` | `CFG-CAMP-69` = 1 tin / 24 giờ, gộp theo số điện thoại; giới hạn tần suất 10 tin / 7 ngày, `CFG-CAMP-43` = 0; K đã nhận 1 SMS tiếp thị lúc 10:00 hôm qua | Chiến dịch SMS khác gửi tới K lúc 09:00 hôm nay | K bị loại "Chạm giới hạn 24 giờ theo điểm đến"; lúc 10:01 hôm nay thì gửi được |
| `AC-30.3.2` | Như trên; chiến dịch được miễn trừ giới hạn tần suất; K đã chạm giới hạn 24 giờ | Gửi tới K | K vẫn bị loại "Chạm giới hạn 24 giờ theo điểm đến" |
| `AC-30.3.3` | `CFG-CAMP-69` = 1 tin / 24 giờ, `CFG-CAMP-43` = 2 giờ; L nhận SMS tiếp thị lúc 10:40 hôm qua; chiến dịch SMS mới tới lượt L lúc 10:20 hôm nay | Hệ thống xử lý | Tin của L hoãn tới 10:41 rồi gửi |
| `AC-30.3.4` | `CFG-CAMP-69` = 1 tin / 24 giờ, gộp theo số điện thoại; K nhận 1 SMS tiếp thị lúc 10:00 hôm qua | Chiến dịch Zalo ZNS gửi tới cùng số lúc 09:00 hôm nay, `CFG-CAMP-43` = 0 | K bị loại "Chạm giới hạn 24 giờ theo điểm đến" (đếm gộp theo số điện thoại) |
| `AC-30.3.5` | `CFG-CAMP-69` tắt; giới hạn tần suất 5 tin / 7 ngày; K đã nhận 1 SMS tiếp thị sáng nay | Chiến dịch SMS khác gửi tới K chiều nay | K nhận tin |
| `AC-30.4.1` | Quản trị viên tìm cách đặt độ trễ trước khi lời từ chối có hiệu lực | Mở cấu hình | Không có tham số nào như vậy |
| `AC-30.5.1` | `CFG-CAMP-70` bật cho SMS; số điện thoại của K thuộc Danh sách không quảng cáo; K có Đồng ý nhận SMS có bằng chứng | Chiến dịch SMS gửi tới K | K bị loại "Thuộc Danh sách không quảng cáo" |
| `AC-30.5.2` | Như trên; K được thêm vào danh sách sau khi danh sách chốt, trước lượt gửi của K | Tới lượt K | K bị loại tại thời điểm gửi — "Thuộc Danh sách không quảng cáo" |
| `AC-30.5.3` | `CFG-CAMP-70` tắt; số của K có trong danh sách doanh nghiệp đã nạp | Chiến dịch SMS gửi tới K | K nhận tin |
| `AC-30.5.4` | `CFG-CAMP-70` bật, tuổi tối đa 48 giờ; lần cập nhật danh sách thành công gần nhất cách đây 50 giờ | Chiến dịch SMS tới lượt gửi | Tin tới các điểm đến thuộc kênh áp dụng được hoãn và thử tra lại (`NFR-06`); quá thời gian tại Phụ lục B.2 thì chiến dịch tự động tạm dừng bảo vệ |

---

#### FEAT-31 — Chiến dịch Tái tiếp cận

**Mô tả nghiệp vụ:** Cho phép gửi tới khách hàng ở giai đoạn Đã rời bỏ khi — và chỉ khi — chiến dịch được phê duyệt đúng theo quy định của phân hệ Khách hàng.

**Quy tắc nghiệp vụ:**

- **`BR-31.1` (Đánh dấu Chiến dịch Tái tiếp cận):** Người soạn đánh dấu chiến dịch là Chiến dịch Tái tiếp cận và khai báo **phạm vi tập khách** (tiêu chí xác định những khách Đã rời bỏ nào được tiếp cận) và **thời hạn hiệu lực**. Chỉ khách hàng ở giai đoạn Đã rời bỏ **nằm trong phạm vi tập khách đã khai báo** và thuộc phần đã được duyệt (`BR-31.2`) mới được miễn lý do loại trừ "Giai đoạn vòng đời" (`BR-08.1` mục 2). Các lý do loại trừ khác vẫn áp dụng đầy đủ; đồng thuận không bị ghi đè (`contacts-srs.md`, `BR-12.5b` (c)).

  **Lý do nghiệp vụ:** Khách đã rời bỏ đã chủ động chấm dứt quan hệ; phạm vi và thời hạn buộc người soạn nói rõ đang tiếp cận ai, trong bao lâu, thay vì mở toàn bộ nhóm khách đã rời bỏ cho mọi chiến dịch.

- **`BR-31.2` (Phê duyệt phần Tái tiếp cận):** Phần Tái tiếp cận được duyệt **trong cùng giai đoạn Chờ duyệt** với phê duyệt phát sóng, bởi đúng hai bên theo (`contacts-srs.md`, `BR-12.5b` (a)): **Quản lý Marketing** và **Quản lý Kinh doanh phụ trách tập khách**. Khi chiến dịch được gửi phê duyệt, các Quản lý Kinh doanh phụ trách khách trong phạm vi tập khách được thông báo (`BR-17.7`), mỗi người duyệt phần thuộc phạm vi của mình. Phê duyệt được ghi trên chính chiến dịch kèm phạm vi tập khách, thời hạn hiệu lực và người phê duyệt (`contacts-srs.md`, `BR-12.5b` (b)). Với khách Đã rời bỏ không có người phụ trách, "Quản lý Kinh doanh phụ trách tập khách" là quản lý kinh doanh của đơn vị tổ chức mà hồ sơ khách thuộc về; khách không thuộc đơn vị nào thì là Quản lý Kinh doanh được Quản trị viên chỉ định sẵn làm người duyệt Tái tiếp cận cho khách không có người phụ trách. Nếu chưa có người được chỉ định (hoặc đơn vị không có quản lý kinh doanh), danh mục kiểm tra hiển thị **cảnh báo** ngay từ lúc soạn, nêu số khách không có người duyệt; những khách này không được miễn, và Quản trị viên được thông báo để chỉ định. Quản lý Marketing là người soạn chiến dịch vẫn được duyệt phần Marketing của Tái tiếp cận, vì bên đối trọng của phần này là Quản lý Kinh doanh; việc duyệt phát sóng vẫn tuân theo `BR-17.2`. Quản trị viên và Chủ sở hữu không thay được hai bên này. Quản lý Marketing đã duyệt phần Tái tiếp cận vẫn được là người duyệt phát sóng nếu thỏa `BR-17.2`. Khi `CFG-CAMP-05` tắt, Chiến dịch Tái tiếp cận vẫn phải qua trạng thái Chờ duyệt để hai bên duyệt phần Tái tiếp cận; sau khi đạt điều kiện tối thiểu (có phê duyệt của Quản lý Marketing và của ít nhất một Quản lý Kinh doanh), người có quyền Phát sóng chiến dịch (kể cả người soạn) xác nhận phát sóng theo `BR-17.9`.
  - Thiếu phê duyệt của Quản lý Marketing, hoặc chưa có phê duyệt của **bất kỳ** Quản lý Kinh doanh nào, là lỗi chặn khi phê duyệt phát sóng và khi bắt đầu gửi (`BR-16.1`).
  - Đã có phê duyệt của một số Quản lý Kinh doanh: chiến dịch phát sóng được; khách thuộc phạm vi của Quản lý Kinh doanh **chưa** duyệt không được miễn và bị loại với lý do "Giai đoạn vòng đời". Số khách này hiển thị riêng khi xem trước và khi duyệt.

  **Lý do nghiệp vụ:** Một bên chịu trách nhiệm nội dung tiếp cận, một bên chịu trách nhiệm quan hệ khách hàng. Không cho phần được duyệt một phần chặn cả chiến dịch để một Quản lý Kinh doanh vắng mặt không làm hỏng chiến dịch của các đơn vị khác; nhưng khách của họ không được tiếp cận khi họ chưa đồng ý.

- **`BR-31.3` (Hết hiệu lực):** Sau thời hạn hiệu lực, miễn trừ chấm dứt: các lượt gửi bắt đầu sau thời điểm đó (kể cả tin bị hoãn vì khung giờ yên lặng, gửi lại, bước tiếp theo của chuỗi nuôi dưỡng) loại khách Đã rời bỏ như chiến dịch thường. Mọi thay đổi phạm vi tập khách hoặc thời hạn hiệu lực cần duyệt lại cả hai phần.

  **Lý do nghiệp vụ:** Phê duyệt tái tiếp cận là cho một đợt cụ thể; nếu không hết hiệu lực, nó trở thành một giấy phép vĩnh viễn để gửi cho khách đã rời bỏ.

- **`BR-31.4` (Đường về Nurturing):** Khi một khách Đã rời bỏ thuộc phần được miễn **nhấp liên kết** (không tính lượt nhấp tự động) hoặc **trả lời** tin của Chiến dịch Tái tiếp cận trong thời hạn hiệu lực, hệ thống yêu cầu phân hệ Khách hàng chuyển giai đoạn của khách về Nurturing theo (`contacts-srs.md`, `BR-12.5b`, `BR-12.6`), với nguồn là mã chiến dịch. Chỉ nhận tin mà không tương tác thì giai đoạn không đổi. Tương tác không được ghi nhận gắn danh tính theo `BR-14.4` (không có căn cứ theo dõi, hoặc đang Hạn chế xử lý) không kích hoạt chuyển giai đoạn.

  **Lý do nghiệp vụ:** Khách chủ động phản hồi là tín hiệu quan hệ được mở lại; chỉ vì đã được gửi tin thì không phải.

- **`BR-31.5` (Thu hồi phê duyệt Tái tiếp cận):** Người đã duyệt phần Tái tiếp cận (Quản lý Marketing hoặc một Quản lý Kinh doanh) được thu hồi phê duyệt của chính mình bất kỳ lúc nào trước khi chiến dịch Hoàn tất hoặc Đã hủy, kèm lý do; thu hồi được ghi nhật ký (`contacts-srs.md`, `BR-12.5b`, danh mục nhật ký kiểm toán mục 24) và thông báo cho người phụ trách chiến dịch, người duyệt phát sóng. Hệ quả:
  - Thu hồi **trước khi chiến dịch bắt đầu gửi**: xét lại như thiếu phê duyệt — nếu mất phê duyệt của Quản lý Marketing hoặc không còn phê duyệt của Quản lý Kinh doanh nào thì là lỗi chặn nhóm (a) (`BR-16.1`), chiến dịch về Nháp; nếu còn phê duyệt của Quản lý Kinh doanh khác thì khách thuộc phạm vi bị thu hồi không còn được miễn, xử lý như `BR-31.2`.
  - Thu hồi **sau khi chiến dịch đã bắt đầu gửi**: như hết hiệu lực (`BR-31.3`) cho phạm vi bị thu hồi — mọi lượt gửi bắt đầu sau thời điểm thu hồi loại khách Đã rời bỏ trong phạm vi đó với lý do "Giai đoạn vòng đời" (`BR-08.1` mục 2); thu hồi phần của Quản lý Marketing chấm dứt miễn trừ cho toàn bộ tập khách.
  - Khách đã được chuyển về Nurturing theo `BR-31.4` **giữ nguyên** giai đoạn, vì việc chuyển dựa trên tương tác thật của khách, không dựa trên phê duyệt.

  **Lý do nghiệp vụ:** Người duyệt phải dừng được một đợt tiếp cận khi phát hiện rủi ro quan hệ khách hàng (ví dụ khách đang khiếu nại); nhưng không được xóa ngược phản hồi thật của khách đã xảy ra.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-31.1.1` | Chiến dịch Tái tiếp cận, phạm vi "Đã rời bỏ trong 12 tháng qua"; khách K rời bỏ 18 tháng trước | Xem trước | K bị loại "Giai đoạn vòng đời không được nhận tiếp thị" |
| `AC-31.1.2` | Khách L Đã rời bỏ, trong phạm vi, nhưng Từ chối nhận tin email | Xem trước chiến dịch Email Tái tiếp cận | L bị loại "Từ chối nhận tin" |
| `AC-31.2.1` | Chiến dịch Tái tiếp cận Chờ duyệt; chỉ Quản lý Marketing đã duyệt phần Tái tiếp cận | Người có quyền Phát sóng chiến dịch mở màn hình duyệt | Lỗi chặn "Chưa có phê duyệt của Quản lý Kinh doanh"; không phê duyệt phát sóng được |
| `AC-31.2.2` | Tập khách thuộc phạm vi của hai Quản lý Kinh doanh; Quản lý Marketing và một Quản lý Kinh doanh đã duyệt | Phê duyệt phát sóng và gửi | Chiến dịch gửi được; chỉ khách thuộc phạm vi người đã duyệt được miễn; khách còn lại bị loại "Giai đoạn vòng đời" và hiển thị riêng khi duyệt |
| `AC-31.2.3` | Chiến dịch Tái tiếp cận vừa được gửi phê duyệt | Kiểm tra thông báo | Quản lý Marketing và mọi Quản lý Kinh doanh phụ trách khách trong phạm vi tập khách nhận thông báo |
| `AC-31.2.4` | Quản trị viên mở phần duyệt Tái tiếp cận | Tìm thao tác duyệt | Không có |
| `AC-31.2.5` | Chiến dịch Tái tiếp cận Nháp, chưa có phê duyệt phần Tái tiếp cận, không còn lỗi chặn nào khác | Người soạn bấm "Gửi phê duyệt" | Chiến dịch chuyển Chờ duyệt; Quản lý Marketing và Quản lý Kinh doanh nhận yêu cầu duyệt |
| `AC-31.2.6` | `CFG-CAMP-05` tắt; chiến dịch Tái tiếp cận của M | M bấm "Xác nhận phiên bản" khi chưa gửi phê duyệt | Không cho phép; M phải gửi phê duyệt để hai bên duyệt phần Tái tiếp cận trước |
| `AC-31.2.7` | Tập khách Tái tiếp cận gồm 5.000 khách lẻ không có người phụ trách, không thuộc đơn vị nào; Quản trị viên đã chỉ định Quản lý Kinh doanh S2 cho nhóm này | Gửi phê duyệt | S2 nhận yêu cầu duyệt cho phần 5.000 khách đó |
| `AC-31.2.8` | Tập khách Tái tiếp cận có 800 khách không có người phụ trách, không thuộc đơn vị nào; chưa ai được chỉ định duyệt nhóm này | Mở danh mục kiểm tra khi soạn | Cảnh báo "800 khách chưa có người duyệt Tái tiếp cận"; Quản trị viên được thông báo; 800 khách này không được miễn khi phát sóng |
| `AC-31.2.9` | Quản lý Marketing M soạn chiến dịch Tái tiếp cận; phê duyệt kép bật | M duyệt phần Tái tiếp cận, rồi thử duyệt phát sóng | Duyệt phần Tái tiếp cận được; không duyệt phát sóng được (`BR-17.2`) |
| `AC-31.3.1` | Thời hạn hiệu lực hết lúc 17:00; tin của K bị hoãn vì khung giờ yên lặng tới 08:00 hôm sau | Tới 08:00 | K Bị loại trừ tại thời điểm gửi — "Giai đoạn vòng đời" |
| `AC-31.3.2` | Chiến dịch Tái tiếp cận Đã duyệt | Người soạn mở rộng phạm vi tập khách | Chiến dịch về Nháp; cả phê duyệt phần Tái tiếp cận lẫn phê duyệt phát sóng phải làm lại |
| `AC-31.4.1` | Khách L Đã rời bỏ trong phần được miễn nhấp liên kết của Chiến dịch Tái tiếp cận trong thời hạn hiệu lực | Hệ thống xử lý | Giai đoạn của L chuyển về Nurturing, nguồn là mã chiến dịch |
| `AC-31.4.2` | Khách N Đã rời bỏ nhận tin nhưng không tương tác | Hết thời hạn hiệu lực | Giai đoạn của N vẫn là Đã rời bỏ |
| `AC-31.4.3` | L được chuyển về Nurturing do lượt nhấp duy nhất; sau đó lượt nhấp đó được nhận diện lại là nhấp tự động | Hệ thống xử lý | Người phụ trách của L được thông báo; giai đoạn không tự đổi; trong lúc chờ, L bị loại khỏi chiến dịch thường với lý do "Giai đoạn vòng đời" |
| `AC-31.4.4` | Tiếp nối AC-31.4.3 | Người phụ trách bấm "Xác nhận giai đoạn hiện tại" | L không còn bị loại khỏi chiến dịch thường |
| `AC-31.5.1` | Chiến dịch Tái tiếp cận Đã duyệt, chưa gửi; chỉ một Quản lý Kinh doanh S đã duyệt | S thu hồi phê duyệt | Chiến dịch về Nháp (lỗi chặn nhóm (a)); người phụ trách được thông báo |
| `AC-31.5.2` | Chiến dịch Tái tiếp cận đang gửi; khách L trong phạm vi của S đã nhấp và đã về Nurturing; khách K trong phạm vi của S đang bị hoãn vì khung giờ yên lặng | S thu hồi phê duyệt | K bị loại với lý do "Giai đoạn vòng đời" khi tới giờ gửi; L giữ Nurturing |

---

#### FEAT-32 — Quyền Chủ thể Dữ liệu & Thời hạn Lưu Sổ cái

**Mô tả nghiệp vụ:** Sổ cái chứa dữ liệu cá nhân (điểm đến, hành vi mở/nhấp) nên phải tuân theo quyền của chủ thể dữ liệu và có thời hạn lưu, đồng thời vẫn giữ được con số tổng hợp và bằng chứng tuân thủ.

**Quy tắc nghiệp vụ:**

- **`BR-32.1` (Hạn chế xử lý):** Khi một khách hàng chuyển sang Hạn chế xử lý hoặc thuộc biện pháp phòng ngừa theo (`contacts-srs.md`, `BR-30.6`, `BR-33.7`), mọi lượt gửi tiếp thị bắt đầu sau thời điểm đó tới người đó bị chặn (`BR-08.4`), gồm cả chuỗi nuôi dưỡng đang chạy. Trong thời gian Hạn chế xử lý, không có gắn thẻ tự động, chuyển giai đoạn hay sự kiện tương tác gắn danh tính nào được ghi cho người đó (`BR-14.4`). Khi Hạn chế xử lý được dỡ, phân hệ Chiến dịch chỉ gửi lại khi đồng thuận của người đó **lúc đó** qua được lý do 5 và 6 của `BR-08.1` — trên kênh doanh nghiệp yêu cầu Đồng ý trước (`CFG-CAMP-74`) là Đồng ý có bằng chứng; việc dỡ Hạn chế xử lý tự nó không bao giờ tạo ra Đồng ý. Riêng biện pháp phòng ngừa do yêu cầu chưa xác minh được danh tính (`contacts-srs.md`, `BR-33.7`): khi dỡ **sớm** vì đã xác định người yêu cầu không phải chủ thể hoặc chủ thể xác nhận không có yêu cầu, hoặc khi khách **xác minh thành công** (với các kênh mà yêu cầu đã xác minh không tác động tới), Đồng ý gốc được khôi phục **là căn cứ** — trừ khi Đồng ý gốc vốn đã không là căn cứ (lý do 6); khi dỡ vì **hết hạn** mà chưa xác minh được, Đồng ý tự khôi phục **không** là căn cứ cho tới khi chính chủ xác nhận lại qua liên kết hoặc mã gửi tới chính điểm đến (`contacts-srs.md`, `BR-30.10`).

  **Lý do nghiệp vụ:** Hạn chế xử lý là yêu cầu của chủ thể dữ liệu, có hiệu lực tức thì; khôi phục gửi tin chỉ vì biện pháp phòng ngừa hết hạn là tự nâng đồng thuận thay cho người nhận.

- **`BR-32.2` (Yêu cầu xóa dữ liệu):** Khi yêu cầu xóa dữ liệu của một khách hàng được chấp thuận — toàn phần, **hoặc từ chối một phần** mà phân hệ Khách hàng vẫn yêu cầu xóa dữ liệu tiếp thị (`contacts-srs.md`, `BR-33.3`); trong trường hợp từ chối một phần mà điểm đến vẫn còn trên hồ sơ, hệ thống ghi Từ chối nhận tin trên mọi kênh (nguồn "Yêu cầu trực tiếp của khách hàng") và giữ ánh xạ liên kết hủy nhận tin theo `BR-26.2` — trong cùng thời hạn xử lý mà phân hệ Khách hàng áp dụng, phân hệ Chiến dịch **khử định danh** mọi dữ liệu của người đó trong phạm vi của mình:
  - Dòng sổ cái của mọi chiến dịch: xóa điểm đến, liên kết tới hồ sơ, thông điệp lỗi gốc của nhà cung cấp (thường chứa địa chỉ) và mọi thông tin nhận diện; giữ trạng thái phân phát, sự kiện tương tác (không kèm danh tính) và thời điểm để số liệu tổng hợp không đổi.
  - Vị trí của người đó trong mọi chuỗi nuôi dưỡng: người đó thoát khỏi chuỗi.
  - Liên kết theo dõi và liên kết hủy nhận tin của người đó: vẫn chuyển tiếp tới trang đích nhưng không còn ánh xạ được về người đó.
  - Nhật ký kiểm toán của phân hệ Chiến dịch (`NFR-08`): giữ loại thao tác và thời điểm, khử phần nhận diện người đó.
  - Tệp xuất sổ cái còn hiệu lực tải về chứa người đó: thu hồi đường tải.
  - Bản ghi tạm chặn chờ xác nhận lời từ chối (`BR-26.3`) và bản ghi tạm ngừng do lỗi hộp thư lặp lại (`BR-28.2`) của các điểm đến của người đó: chuyển sang dấu vết chặn gửi, xóa giá trị đọc được.

  Thao tác khử định danh do Quản trị viên (hoặc Chủ sở hữu) cùng Người phụ trách Bảo vệ Dữ liệu thực hiện theo quy trình của phân hệ Khách hàng. Phân hệ Chiến dịch báo hoàn tất phần việc của mình để phân hệ Khách hàng ghi vào Biên bản Hoàn tất Xử lý. Với yêu cầu **bản sao dữ liệu**, phân hệ Chiến dịch cung cấp danh sách chiến dịch đã gửi tới người đó cùng trạng thái phân phát và sự kiện tương tác.

  **Lý do nghiệp vụ:** Xóa hẳn dòng sổ cái khiến báo cáo của mọi chiến dịch cũ tự thay đổi mỗi khi có người yêu cầu xóa — ban giám đốc thấy số liệu quý trước "tự nhảy". Khử định danh xóa được con người khỏi dữ liệu mà vẫn giữ được con số.

- **`BR-32.3` (Thời hạn lưu chi tiết sổ cái):** Sự kiện mở/nhấp gắn danh tính được lưu `CFG-CAMP-45` — áp cho cả sổ cái, các sự kiện tương tác hiển thị trên dòng thời gian hồ sơ khách hàng (`BR-23.6`, mục nhận tin vẫn giữ) và thẻ gắn tự động từ tương tác (`BR-36.4`) — không có ngoại lệ; nếu người dùng muốn giữ một phân loại, họ gắn thẻ thủ công theo quy tắc của phân hệ Khách hàng, và thẻ đó là thẻ thủ công, không còn là dữ liệu suy ra từ chiến dịch; đây là ngoại lệ tự động khử định danh (f) tại (`contacts-srs.md`, `BR-33.5`); phần còn lại của chi tiết sổ cái theo từng người (điểm đến, trạng thái phân phát, tham chiếu bằng chứng đồng ý) được lưu `CFG-CAMP-20` kể từ khi chiến dịch Hoàn tất hoặc Đã hủy (với chuỗi nuôi dưỡng: kể từ khi lượt gửi của từng bước có kết quả cuối cùng), sau đó được khử định danh theo cách của `BR-32.2`. Thời hạn này có sàn bắt buộc (Phụ lục B). Bằng chứng đồng thuận đã ghi về hồ sơ khách hàng (`BR-26.4`) **không** chịu thời hạn này mà theo quy tắc lưu của (`contacts-srs.md`, `BR-30.3`).

  **Lý do nghiệp vụ:** Giữ vĩnh viễn hành vi mở/nhấp của từng người vượt quá mục đích thu thập; nhưng xóa quá sớm làm doanh nghiệp không còn bằng chứng khi có khiếu nại về một chiến dịch trong năm tài chính trước. Chuỗi nuôi dưỡng không bao giờ "hoàn tất" nên phải tính theo từng lượt gửi, nếu không dữ liệu của chuỗi bị giữ vô hạn.

- **`BR-32.4` (Dấu vết chặn gửi sau khi xóa):** Hệ thống giữ dấu vết chặn gửi trong hai trường hợp: (1) **mọi** điểm đến bị xóa theo yêu cầu xóa dữ liệu của chủ thể — yêu cầu xóa được coi là rút đồng thuận tiếp thị; (2) điểm đến bị xóa vĩnh viễn theo đường khác — hồ sơ bị xóa vĩnh viễn khỏi Thùng rác, kênh liên lạc bị xóa khỏi hồ sơ, hoặc giá trị điểm đến trên hồ sơ bị thay bằng giá trị khác — mà tại thời điểm đó điểm đến đang Từ chối nhận tin (trừ Từ chối do biện pháp phòng ngừa có nguồn "Yêu cầu chưa xác minh được danh tính", `contacts-srs.md` `BR-33.7`), có khiếu nại thư rác, hoặc người nhận đã chặn hay tắt tin tiếp thị của doanh nghiệp, hoặc điểm đến đang tạm chặn chờ xác nhận lời từ chối (`BR-26.3`). Trước khi biến đổi, điểm đến được **chuẩn hóa** (số điện thoại về định dạng quốc tế đầy đủ; email về chữ thường, bỏ khoảng trắng), và mọi lượt so khớp cũng chuẩn hóa như vậy. Dấu vết là một **dấu vết chặn gửi** của điểm đến: dạng biến đổi một chiều **có dùng khóa bí mật của Không gian làm việc** (để không thể dò ngược bằng cách thử mọi số điện thoại), chỉ kèm loại điểm đến (email / số điện thoại / người quan tâm Tài khoản Chính thức) và thời điểm tạo, không gắn bất kỳ thông tin nào khác về người đó. Dấu vết chặn **mọi kênh dùng điểm đến đó** (với số điện thoại: SMS, Zalo ZNS, WhatsApp). Mọi lượt gửi tiếp thị tới một điểm đến khớp dấu vết bị loại với lý do "Từ chối nhận tin" (`BR-08.1` mục 5). Dấu vết chỉ bị xóa khi **chính chủ điểm đến** tự đăng ký nhận tin lại **và** xác nhận qua một liên kết hoặc mã gửi tới chính điểm đến đó; nhân viên ghi nhận thủ công hay nhập khẩu không xóa được dấu vết. Khóa bí mật được bảo vệ và có bản dự phòng; mọi lần xoay vòng khóa phải chuyển đổi toàn bộ dấu vết hiện có sang khóa mới — mất khóa là mất toàn bộ danh sách chặn.

  **Lý do nghiệp vụ:** Sau khi dữ liệu bị xóa, hệ thống không còn biết người đó đã từ chối; nếu doanh nghiệp nhập lại một danh sách cũ, người đã từ chối — thậm chí đã yêu cầu xóa dữ liệu vì bị làm phiền — sẽ lại nhận tin quảng cáo. Giữ một dấu vết tối thiểu, không đọc ngược được, chỉ để thực hiện chính lời từ chối của họ là cách tôn trọng cả quyền được xóa lẫn quyền không bị làm phiền. Việc gỡ dấu vết chỉ dựa trên hành vi xác nhận của chính chủ, vì mọi nguồn khác đều có thể chính là nguồn đã sinh ra danh sách cũ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-32.1.1` | K đang ở bước 2 của chuỗi nuôi dưỡng; K chuyển sang Hạn chế xử lý | Tới bước 3 | K không nhận; lý do "Đang Hạn chế xử lý" |
| `AC-32.1.2` | K từng Từ chối nhận tin email rồi bị Hạn chế xử lý; Hạn chế xử lý được dỡ | Chiến dịch Email gửi tới K | K bị loại "Từ chối nhận tin" (dỡ Hạn chế xử lý không tạo Đồng ý) |
| `AC-32.1.3` | `CFG-CAMP-74` không gồm Email; K ở Chưa có đồng thuận trên email, vừa được dỡ Hạn chế xử lý | Chiến dịch Email bắt đầu sau khi dỡ | K nhận thư |
| `AC-32.1.4` | K có Đồng ý SMS; biện pháp phòng ngừa được áp rồi dỡ vì hết hạn 30 ngày | Chiến dịch SMS bắt đầu sau khi dỡ, `CFG-CAMP-74` mặc định | K bị loại lý do 6 (Đồng ý tự khôi phục chưa được xác nhận lại) |
| `AC-32.1.5` | Như trên; K bấm liên kết xác nhận lại gửi tới số của mình | Chiến dịch SMS tiếp theo | K nhận tin |
| `AC-32.1.6` | K có Đồng ý SMS; biện pháp phòng ngừa được dỡ sớm vì xác định người yêu cầu không phải K | Chiến dịch SMS bắt đầu sau khi dỡ | K nhận tin (Đồng ý gốc là căn cứ) |
| `AC-32.1.7` | K có Đồng ý SMS là căn cứ; biện pháp phòng ngừa kết thúc vì K bổ sung xác minh thành công, yêu cầu của K chỉ là xin bản sao dữ liệu | Chiến dịch SMS bắt đầu sau đó | K nhận tin (Đồng ý gốc là căn cứ) |
| `AC-32.2.1` | Chiến dịch có 5.000 người phân phát, 1.200 người mở; yêu cầu xóa của K (người đã mở) được chấp thuận | Sau khi xử lý | Sổ cái không còn tìm được K; báo cáo vẫn 5.000 phân phát, 1.200 mở |
| `AC-32.2.2` | K đang ở bước 2 của một chuỗi nuôi dưỡng; yêu cầu xóa dữ liệu của K được chấp thuận | Sau khi xử lý | K không còn trong chuỗi; không bước nào tiếp theo được gửi |
| `AC-32.2.3` | K yêu cầu bản sao dữ liệu | Phân hệ Khách hàng tổng hợp | Bản sao có danh sách chiến dịch đã gửi tới K kèm trạng thái và sự kiện tương tác |
| `AC-32.3.1` | `CFG-CAMP-20` = 24 tháng; chiến dịch hoàn tất 24 tháng trước (dịch chuyển đồng hồ `NFR-11`) | Mở sổ cái | Các dòng đã khử định danh; số liệu tổng hợp không đổi; bằng chứng từ chối nhận tin trên hồ sơ khách hàng vẫn còn |
| `AC-32.4.1` | K đã Từ chối nhận tin email rồi yêu cầu xóa dữ liệu; 3 tháng sau email của K được nhập lại trong một lô với cơ sở đồng thuận "Khách hàng đã đăng ký trực tiếp" | Chiến dịch Email gửi tới hồ sơ mới | Hồ sơ mới bị loại "Từ chối nhận tin" do khớp dấu vết chặn gửi |
| `AC-32.4.2` | Tiếp nối AC-32.4.1 | Tìm cách đọc ra email từ dấu vết chặn gửi | Không có cách nào; dấu vết chỉ dùng được để so khớp |
| `AC-32.4.3` | Nhân viên xóa hồ sơ H đang Từ chối nhận tin SMS; 30 ngày sau Thùng rác xóa vĩnh viễn H; tháng sau số của H được nhập lại | Chiến dịch SMS gửi tới hồ sơ mới | Bị loại "Từ chối nhận tin" do khớp dấu vết chặn gửi |
| `AC-32.4.4` | Tiếp nối AC-32.4.3 | Nhân viên sửa tay hồ sơ mới lên Đồng ý kèm bằng chứng tự khai | Dấu vết vẫn còn; hồ sơ mới vẫn bị loại |
| `AC-32.4.5` | Tiếp nối AC-32.4.3 | Chủ số tự đăng ký trên biểu mẫu và nhập đúng mã xác nhận gửi tới số đó | Dấu vết bị xóa; hồ sơ nhận được tiếp thị |
| `AC-32.4.6` | K đang Đồng ý nhận SMS thì yêu cầu xóa dữ liệu; yêu cầu được chấp thuận; tháng sau số của K được nhập lại | Chiến dịch SMS gửi tới hồ sơ mới | Bị loại "Từ chối nhận tin" do khớp dấu vết chặn gửi |
| `AC-32.4.7` | Nhân viên sửa số điện thoại của hồ sơ H (đang Từ chối nhận tin SMS) thành một số khác | Số cũ được nhập lại vào hồ sơ khác | Hồ sơ đó bị loại khi gửi SMS tới số cũ |
| `AC-32.4.8` | Số "0912 345 678" của H (đang Từ chối) bị xóa; số được nhập lại dạng "+84912345678" | Chiến dịch SMS gửi tới hồ sơ mới | Bị loại "Từ chối nhận tin" — hai cách viết khớp cùng một dấu vết |

---

### Nhóm I — Đo lường & Ghi nhận Hiệu quả

#### FEAT-33 — Báo cáo Phân phát & Tương tác

**Mô tả nghiệp vụ:** Báo cáo của từng chiến dịch và báo cáo tổng hợp nhiều chiến dịch, với định nghĩa chỉ số thống nhất tuyệt đối.

**Quy tắc nghiệp vụ:**

- **`BR-33.1` (Các con số gốc):** Mọi báo cáo xây dựng từ cùng một tập con số gốc, lấy từ sổ cái:
  - **Khớp tiêu chí** và **Bị loại trước khi chốt** (theo lý do) — từ lần chốt danh sách.
  - **Đã chuyển nhà cung cấp** — số dòng đã từng đạt trạng thái này (gồm cả những dòng sau đó phân phát hay thất bại).
  - **Đã phân phát**, **Thất bại vĩnh viễn**, **Thất bại tạm thời còn lại**, **Chưa xác định**, **Bị loại trừ tại thời điểm gửi**, **Đã hủy trước khi gửi**.
  - **Người mở (duy nhất)**, **Người nhấp (duy nhất)**, **Tổng lượt nhấp**, **Hủy nhận tin**, **Khiếu nại**, **Trả lời**.
  - Mỗi người được tính **theo kênh thực tế đã nhận** (kênh chính hoặc kênh dự phòng).

  **Lý do nghiệp vụ:** Mỗi báo cáo tự đếm theo cách riêng là nguồn gốc của các con số không khớp nhau giữa màn hình chiến dịch, báo cáo tổng hợp và tệp đối soát; dùng chung một tập con số gốc từ sổ cái thì mọi con số đều truy được tới từng người nhận.

- **`BR-33.2` (Định nghĩa tỷ lệ — dùng thống nhất ở mọi nơi):**

  | Chỉ số | Tử số | Mẫu số |
  | --- | --- | --- |
  | Tỷ lệ phân phát | Đã phân phát | Đã chuyển nhà cung cấp |
  | Tỷ lệ hỏng vĩnh viễn | Thất bại vĩnh viễn nhóm "Điểm đến không tồn tại" | Đã chuyển nhà cung cấp |
  | Tỷ lệ mở | Người mở (duy nhất) | Đã phân phát tới người có căn cứ theo dõi hành vi (`BR-14.4`), chỉ trên các kênh đo được sự kiện mở |
  | Tỷ lệ nhấp | Người nhấp (duy nhất) | Đã phân phát tới người có căn cứ theo dõi hành vi (`BR-14.4`) |
  | Tỷ lệ nhấp trên mở | Người nhấp (duy nhất) | Người mở (duy nhất), chỉ trên các kênh đo được sự kiện mở |
  | Tỷ lệ hủy nhận tin | Người hủy nhận tin từ chiến dịch này qua liên kết, thao tác của hộp thư, từ khóa hoặc tư vấn viên ghi nhận — **không** gồm khiếu nại và chặn doanh nghiệp | Đã phân phát |
  | Tỷ lệ chặn doanh nghiệp | Người chặn tài khoản gửi của doanh nghiệp | Đã phân phát, chỉ trên các kênh nền tảng báo sự kiện này |
  | Tỷ lệ khiếu nại | Người khiếu nại | Đã phân phát tới các nhà cung cấp có báo khiếu nại theo từng người (`BR-27.4`) |
  | Tỷ lệ trả lời | Người trả lời (duy nhất) | Đã phân phát, chỉ trên các kênh người nhận trả lời được |

  Người bị loại (trước hay tại thời điểm gửi) **không bao giờ** nằm trong mẫu số. Báo cáo hiển thị riêng tỷ lệ tin "không đo được hành vi từng người" và tổng lượt nhấp theo liên kết của cả phần không đo được. Lượt nhấp tự động bị loại (`BR-14.3`) không nằm trong tử số. Kênh không đo được một chỉ số hiển thị "Không áp dụng", không hiển thị 0% — trên mọi màn hình: báo cáo chiến dịch, báo cáo tổng hợp, tệp xuất báo cáo.

  **Lý do nghiệp vụ:** Hai tỷ lệ dùng hai mẫu số khác nhau thì không so sánh được với nhau và không so sánh được với chuẩn ngành. Một định nghĩa duy nhất là điều kiện để doanh nghiệp so sánh chiến dịch này với chiến dịch khác. Khiếu nại và chặn doanh nghiệp được tách khỏi hủy nhận tin để một tín hiệu nghiêm trọng không bị pha loãng.

- **`BR-33.3` (Tỷ lệ mở là số gần đúng):** Báo cáo ghi chú rõ rằng sự kiện mở email là **gần đúng**: một số hộp thư tự tải trước nội dung (làm tăng số mở), một số chặn hình ảnh (làm giảm). Sự kiện mở nhận diện được là do hộp thư tự tải trước nội dung được đánh dấu riêng: vẫn hiển thị trong sổ cái nhưng **không** tính vào Người mở, **không** được chuyển sang phân hệ Khách hàng để cộng điểm tương tác, và **không** tính là tương tác cho nhánh điều kiện của chuỗi nuôi dưỡng. Tỷ lệ mở **không** được dùng làm tiêu chí chọn phiên bản thắng mặc định (`CFG-CAMP-26`); chọn "đã mở" làm điều kiện rẽ nhánh trong chuỗi nuôi dưỡng thì giao diện cảnh báo người cấu hình.

  **Lý do nghiệp vụ:** Quyết định dựa trên một con số có sai số lớn mà không biết là có sai số, là quyết định sai một cách có hệ thống.

- **`BR-33.4` (Chi tiết theo liên kết, theo phiên bản, theo kênh):** Để người soạn biết phần nào của nội dung hiệu quả, báo cáo chiến dịch có: số lượt nhấp theo từng liên kết; so sánh các phiên bản A/B; tách số liệu theo kênh chính và từng kênh dự phòng; phân bố theo thời gian (theo giờ, theo ngày tính theo múi giờ Không gian làm việc).

  **Lý do nghiệp vụ:** Chỉ có con số tổng thì người soạn biết chiến dịch tốt hay dở nhưng không biết sửa phần nào — liên kết nào không ai nhấp, phiên bản nào kém, kênh dự phòng có đáng chi phí không.

- **`BR-33.5` (Cập nhật & chốt số):** Số liệu được cập nhật liên tục khi có sự kiện mới, kể cả sau khi chiến dịch Hoàn tất hay lưu trữ. Báo cáo hiển thị thời điểm cập nhật gần nhất.

  **Lý do nghiệp vụ:** Khách hàng tiếp tục mở thư nhiều ngày sau khi gửi; chốt số sớm làm báo cáo luôn thấp hơn thực tế.

- **`BR-33.6` (Báo cáo tổng hợp):** Báo cáo nhiều chiến dịch (theo kỳ, theo kênh, theo người phụ trách, theo đơn vị) cộng dồn các **con số gốc** rồi mới tính tỷ lệ theo `BR-33.2`, không lấy trung bình các tỷ lệ của từng chiến dịch. Báo cáo chỉ gồm chiến dịch trong phạm vi dữ liệu của người xem.

  **Lý do nghiệp vụ:** Trung bình tỷ lệ của một chiến dịch 100 người và một chiến dịch 100.000 người cho ra một con số vô nghĩa.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-33.1.1` | Chiến dịch có 1.000 người trong danh sách chốt, đang gửi | Cộng số dòng theo **trạng thái hiện tại** trong báo cáo | Tổng các nhóm Chờ gửi, Hoãn, Đã chuyển nhà cung cấp (chưa có kết quả), Đã phân phát, Thất bại tạm thời, Thất bại vĩnh viễn, Chưa xác định, Bị loại trừ tại thời điểm gửi, Đã hủy trước khi gửi bằng 1.000; số lũy kế "Đã chuyển nhà cung cấp" hiển thị riêng, không cộng vào tổng |
| `AC-33.2.1` | Chiến dịch Email: chuyển 10.000, phân phát 9.800, 2.450 người mở, 490 người nhấp | Mở báo cáo | Tỷ lệ phân phát 98%; tỷ lệ mở 25%; tỷ lệ nhấp 5%; tỷ lệ nhấp trên mở 20% |
| `AC-33.2.2` | Chiến dịch có 500 người Bị loại trừ tại thời điểm gửi | Mở báo cáo | 500 người không nằm trong mẫu số của bất kỳ tỷ lệ nào; hiển thị riêng theo lý do |
| `AC-33.2.3` | Chiến dịch Zalo ZNS, 1.000 người thất bại ở Zalo ZNS được gửi SMS | Mở báo cáo | Số liệu tách theo Zalo ZNS và SMS; tỷ lệ mở chỉ tính trên phần Zalo ZNS |
| `AC-33.2.4` | 10.000 tin phân phát, trong đó 6.000 người có căn cứ theo dõi, 600 trong số đó nhấp; phần còn lại có 900 lượt nhấp tổng hợp | Mở báo cáo | Tỷ lệ nhấp 10% (600/6.000); hiển thị "40% không đo được hành vi từng người" và 900 lượt nhấp tổng hợp |
| `AC-33.3.1` | Báo cáo chiến dịch Email | Xem chỉ số Tỷ lệ mở | Có ghi chú "số gần đúng" kèm giải thích |
| `AC-33.3.2` | Hộp thư của K tự tải trước nội dung thư ngay khi nhận, nhận diện được | Mở sổ cái và báo cáo | Sổ cái ghi sự kiện mở tự động; báo cáo không tính K là người mở; không có yêu cầu cộng điểm gửi sang phân hệ Khách hàng |
| `AC-33.4.1` | Chiến dịch Email có 3 liên kết và 2 phiên bản A/B | Mở báo cáo | Có số lượt nhấp theo từng liên kết, bảng so sánh A/B, biểu đồ theo giờ |
| `AC-33.5.1` | Chiến dịch Hoàn tất 10 ngày trước | Một người nhận mở thư | Báo cáo tăng số người mở; thời điểm cập nhật thay đổi |
| `AC-33.6.1` | Chiến dịch A: 100 phân phát, 50 mở; chiến dịch B: 10.000 phân phát, 1.000 mở | Mở báo cáo tổng hợp A + B | Tỷ lệ mở tổng hợp là 1.050 / 10.100 ≈ 10,4% (không phải trung bình 30%) |
| `AC-33.6.2` | Báo cáo tổng hợp gồm một chiến dịch Email và một chiến dịch SMS | Xem và xuất báo cáo | Tỷ lệ mở tính trên phần Email; dòng SMS hiển thị "Không áp dụng" ở cả màn hình và tệp xuất |

---

#### FEAT-34 — Ghi nhận Cơ hội & Doanh thu Chịu ảnh hưởng

**Mô tả nghiệp vụ:** Trả lời câu hỏi "chiến dịch này đã góp phần tạo ra bao nhiêu cơ hội bán hàng và bao nhiêu doanh thu", nhất quán với cách phân hệ Cơ hội Bán hàng ghi nhận nguồn gốc.

**Quy tắc nghiệp vụ:**

- **`BR-34.1` (Hai thước đo, không trộn lẫn):** Báo cáo chiến dịch có hai thước đo tách biệt:
  - **Cơ hội có nguồn gốc từ chiến dịch:** cơ hội có Nguồn gốc chính (`deals-pipeline-srs.md`, `BR-23.1`) mang tên chiến dịch trùng với mã định danh của chiến dịch. Thước đo này **cộng dồn được** giữa các chiến dịch, vì mỗi cơ hội có đúng một Nguồn gốc chính.
  - **Cơ hội chịu ảnh hưởng của chiến dịch:** cơ hội được **tạo** trong vòng `CFG-CAMP-10` ngày sau lần gần nhất một liên hệ tham gia cơ hội đó (`deals-pipeline-srs.md`, `BR-01.3`) có tương tác được tính với chiến dịch — nhấp liên kết (không tính lượt nhấp tự động) hoặc trả lời. Một cơ hội có thể chịu ảnh hưởng của nhiều chiến dịch; thước đo này **không cộng dồn được** giữa các chiến dịch, và báo cáo tổng hợp nhiều chiến dịch phải hiển thị số cơ hội **duy nhất** kèm ghi chú.

  **Lý do nghiệp vụ:** Phân hệ Cơ hội Bán hàng đã chốt nguyên tắc ghi nhận điểm chạm đầu tiên cho Nguồn gốc chính. Nếu phân hệ Chiến dịch đưa ra một con số "doanh thu từ chiến dịch" theo cách đếm khác mà không gọi tên khác, hai báo cáo sẽ mâu thuẫn nhau và ban giám đốc không biết tin con số nào. Thước đo "chịu ảnh hưởng" vẫn cần vì phần lớn chiến dịch nhắm tới khách hàng **đã có** trong hệ thống — Nguồn gốc chính của họ đã được ghi từ trước và không bao giờ đổi.

- **`BR-34.2` (Doanh thu):** Với mỗi thước đo, báo cáo hiển thị: số cơ hội, tổng giá trị cơ hội đang mở, số cơ hội Thắng và **doanh thu Thắng**. Giá trị lấy theo giá trị đã quy đổi về đồng tiền cơ sở tại thời điểm đóng (`deals-pipeline-srs.md`, `BR-02.3`, `BR-27.5`), dùng 100% giá trị cơ hội (không chia theo tỷ lệ chia doanh số), nhất quán với (`deals-pipeline-srs.md`, `BR-26.2`).

  **Lý do nghiệp vụ:** Báo cáo theo nguồn của phân hệ Cơ hội đã chọn tính 100% giá trị; tính khác ở đây sẽ làm hai báo cáo về cùng một cơ hội lệch nhau.

- **`BR-34.3` (Chiều ngược):** Cơ hội không còn ở kết quả Thắng (bị mở lại hoặc tái phân loại theo `deals-pipeline-srs.md`, `FEAT-21`), bị xóa, không còn đủ điều kiện chịu ảnh hưởng (lượt nhấp duy nhất bị nhận diện lại là tự động, `BR-14.3`), hoặc bị chuyển ra ngoài phạm vi của người xem thì không còn được tính (hoặc được tính lại) ngay trong báo cáo chiến dịch. Báo cáo của một chiến dịch không lưu con số doanh thu riêng mà luôn tính từ kết quả hiện hành của cơ hội. Báo cáo tổng hợp **theo kỳ** (tháng, quý) áp đúng quy tắc chọn ảnh chụp kết quả theo kỳ của (`deals-pipeline-srs.md`, `BR-21.3`), để doanh thu tiếp thị theo kỳ khớp với báo cáo doanh số theo kỳ.

  **Lý do nghiệp vụ:** Doanh thu tiếp thị phải khớp với doanh thu mà bộ phận bán hàng báo cáo; một con số lưu riêng sẽ lệch ngay lần đầu có cơ hội bị tái phân loại.

- **`BR-34.4` (Che số liệu tài chính):** Giá trị cơ hội và doanh thu trong báo cáo chiến dịch chịu đúng quy tắc che số liệu tài chính của (`deals-pipeline-srs.md`, `FEAT-03`) theo quyền của người xem. Người xem thấy số lượng cơ hội trong phạm vi của mình nhưng không thấy giá trị nếu không có quyền xem số liệu tài chính. Quy tắc áp như nhau trên báo cáo chiến dịch, báo cáo tổng hợp và tệp xuất.

  **Lý do nghiệp vụ:** Báo cáo tiếp thị không được trở thành đường vòng để xem giá trị hợp đồng mà phân hệ Cơ hội đã che.

- **`BR-34.5` (Ai xem được báo cáo ghi nhận hiệu quả):** Ngoài người có quyền xem chiến dịch, Quản lý Kinh doanh xem được báo cáo ghi nhận hiệu quả của mọi chiến dịch, **chỉ gồm các cơ hội thuộc phạm vi của mình**, và chỉ thấy tên, mã, kênh, thời điểm gửi của chiến dịch — không mở được nội dung, đối tượng hay sổ cái.

  **Lý do nghiệp vụ:** Quản lý Kinh doanh cần biết chiến dịch nào đang đem lại cơ hội cho đội mình để phối hợp chăm sóc, nhưng không cần quyền trên kế hoạch tiếp thị.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-34.1.1` | Khách tiềm năng mới đăng ký qua liên kết của chiến dịch C, rồi được chuyển đổi thành cơ hội D | Mở báo cáo C | D nằm trong "Cơ hội có nguồn gốc từ chiến dịch" |
| `AC-34.1.2` | Khách hàng cũ K (Nguồn gốc chính từ năm trước) nhấp chiến dịch C ngày 1; cơ hội D có K tham gia được tạo ngày 20; `CFG-CAMP-10` = 30 | Mở báo cáo C | D nằm trong "Cơ hội chịu ảnh hưởng", không nằm trong "có nguồn gốc" |
| `AC-34.1.3` | Như AC-34.1.2 nhưng D được tạo ngày 35 | Mở báo cáo C | D không được tính |
| `AC-34.1.4` | K nhấp cả chiến dịch C1 và C2 trước khi D được tạo | Mở báo cáo tổng hợp C1 + C2 | "Cơ hội chịu ảnh hưởng" hiển thị 1 cơ hội duy nhất, kèm ghi chú không cộng dồn |
| `AC-34.1.5` | Lượt nhấp duy nhất của K với chiến dịch C là lượt nhấp tự động bị loại | Cơ hội có K được tạo sau đó | Không được tính là chịu ảnh hưởng của C |
| `AC-34.2.1` | Cơ hội D Thắng, giá trị 10.000 USD, đồng tiền cơ sở VND, tỷ giá lúc đóng 25.000 | Mở báo cáo chiến dịch | Doanh thu Thắng hiển thị 250.000.000 VND, 100% giá trị dù D có chia doanh số cho hai người |
| `AC-34.3.1` | D đang được tính doanh thu Thắng 500 triệu; D được mở lại rồi đóng Thua theo quy trình tái phân loại của phân hệ Cơ hội | Mở lại báo cáo C | Doanh thu Thắng giảm 500 triệu |
| `AC-34.3.2` | D Thắng trong quý 3, tái phân loại sang Thua trong quý 4 | Mở báo cáo tổng hợp theo quý | Quý 3 và quý 4 hiển thị theo đúng quy tắc chọn ảnh chụp theo kỳ của phân hệ Cơ hội, khớp với báo cáo doanh số theo quý |
| `AC-34.4.1` | Người xem không có quyền xem số liệu tài chính | Mở báo cáo C | Thấy số cơ hội; giá trị và doanh thu bị che |
| `AC-34.4.2` | Tiếp nối AC-34.4.1 | Mở báo cáo tổng hợp nhiều chiến dịch và xuất tệp | Giá trị và doanh thu bị che ở cả màn hình và tệp xuất |
| `AC-34.5.1` | Quản lý Kinh doanh S không có quyền trên chiến dịch C | Mở báo cáo ghi nhận hiệu quả | Thấy tên và mã của C cùng các cơ hội thuộc phạm vi của S; không mở được nội dung hay sổ cái của C |

---

### Nhóm J — Phản hồi & Hành động sau Tương tác

#### FEAT-35 — Chuyển Tin Trả lời về Hộp thư Hội thoại

**Mô tả nghiệp vụ:** Khách hàng trả lời tin chiến dịch được tư vấn viên tiếp nhận ngay trong Hộp thư Hội thoại, kèm ngữ cảnh chiến dịch đã gửi.

**Quy tắc nghiệp vụ:**

- **`BR-35.1` (Tin trả lời đi theo luồng hội thoại chuẩn):** Tin trả lời qua WhatsApp, Zalo, SMS (đầu số hai chiều) và thư trả lời email (khi địa chỉ nhận thư trả lời trỏ về hộp thư đã kết nối, `BR-10.4`) được đưa vào Hộp thư Hội thoại và xử lý theo đúng quy tắc nhận diện hồ sơ, tạo hội thoại và phân công của `omnichat-srs.md` (`FEAT-02`, `FEAT-04`). Phân hệ Chiến dịch không có quy tắc phân công riêng. Với kênh không có tiếp nhận hội thoại trong Hộp thư Hội thoại (Mục 1.6, giả định 2), tin trả lời vẫn được ghi sự kiện Trả lời trên sổ cái, ghi vào dòng thời gian hồ sơ khách hàng, được xử lý từ khóa từ chối (`BR-26.3`), và tạo **một** công việc liên hệ lại — giao cho người phụ trách khách hàng hoặc vào hàng đợi của đơn vị tiếp nhận theo `CFG-CAMP-53` và `BR-35.5` (khách không có người phụ trách luôn vào hàng đợi) — cho mỗi khách trong mỗi chiến dịch — tin cần xác nhận lời từ chối được xác nhận ngay trên công việc này bằng cùng hai thao tác của `BR-26.3` (các tin trả lời sau được gộp vào công việc đó); không tạo công việc cho tin đã được ghi nhận tự động là lời từ chối.

  **Lý do nghiệp vụ:** Một cách phân công thứ hai cho cùng một hộp thư sẽ khiến cùng một khách được hai tư vấn viên trả lời, hoặc không ai.

- **`BR-35.2` (Ngữ cảnh chiến dịch):** Hội thoại phát sinh từ tin trả lời hiển thị cho tư vấn viên: tên chiến dịch, thời điểm gửi và nội dung tin mà khách hàng đã nhận (đúng phiên bản trong sổ cái). Sự kiện Trả lời được ghi vào dòng sổ cái tương ứng.

  **Lý do nghiệp vụ:** Khách trả lời "cho tôi 2 cái" mà tư vấn viên không biết khách đang nói về ưu đãi nào thì cuộc trò chuyện bắt đầu bằng một câu hỏi lại.

- **`BR-35.3` (Trả lời không phải đồng ý):** Tin trả lời không thay đổi đồng thuận (`BR-26.5`), trừ khi là lời từ chối theo `BR-26.3`.

  **Lý do nghiệp vụ:** Trả lời một tin không phải là đồng ý nhận tiếp thị trong tương lai.

- **`BR-35.4` (Tin không trả lời được):** Với kênh hoặc đầu số không nhận trả lời, nội dung tin nên có hướng dẫn liên hệ; danh mục kiểm tra hiển thị cảnh báo nếu chiến dịch dùng kênh hoặc mẫu tin không nhận trả lời (ví dụ mẫu Zalo ZNS không cho trả lời) mà nội dung không có thông tin liên hệ nào.

  **Lý do nghiệp vụ:** Khách muốn mua mà không biết liên hệ bằng cách nào là cơ hội mất đi ngay tại chiến dịch.

- **`BR-35.5` (Hàng đợi và đơn vị tiếp nhận):** Theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10`, `BR-35.11`, `BR-35.12`, `BR-35.13`, `BR-35.14` và quy tắc hàng đợi của loại dữ liệu Công việc tại [`tasks-srs.md`](./tasks-srs.md) `BR-13.6` (phân hệ Công việc sở hữu loại dữ liệu này), phân hệ khai báo:
  - (a) **Tin trả lời vào Hộp thư Hội thoại** đi vào hàng đợi và đơn vị tiếp nhận của tài khoản kênh theo `omnichat-srs.md`; phân hệ này không khai báo hàng đợi hay đơn vị tiếp nhận thứ hai cho cùng tài khoản kênh.
  - (b) **Nguồn "Liên hệ lại từ chiến dịch" và người khởi chạy:** công việc liên hệ lại (`BR-35.1`) do Hệ thống tạo **thay người khởi chạy của chiến dịch** đã gửi tin được trả lời ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`; người khởi chạy theo `BR-17.10`, cũng là người khởi chạy được nhắc tới tại [`tasks-srs.md`](./tasks-srs.md) `BR-13.7`) — không phải thay tài khoản gửi; tài khoản gửi chỉ xác định nguồn và hàng đợi theo [`tasks-srs.md`](./tasks-srs.md) `BR-13.6` (a). Việc giao công việc chịu trần ô (Công việc, Gán) của người khởi chạy, trừ một **ngoại lệ tường minh**: giao thẳng cho Người phụ trách của khách hàng (mặc định của `CFG-CAMP-53`) không chịu trần ô Gán của người khởi chạy, vì người nhận đã phụ trách chính khách hàng đó và công việc chỉ liên quan tới khách hàng đó. Người nhận vẫn phải đạt điều kiện người nhận của [`tasks-srs.md`](./tasks-srs.md) `BR-09.1` (gồm [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`); không đạt thì công việc vào hàng đợi của nguồn theo (c). Hai làm rõ: (i) công việc liên hệ lại là bản ghi sinh ra từ một nguồn vào hàng đợi, nguồn đó do Người có toàn quyền thiết lập, nên việc tạo **không** xét ô (Công việc, Tạo) của người khởi chạy — giống biểu mẫu tạo khách hàng tiềm năng; "tạo thay người khởi chạy" chỉ quyết định trần ô Gán khi giao và người được ghi là người tạo; (ii) khi khách trả lời mà người khởi chạy đang bị tạm ngưng hoặc đã rời workspace, công việc vẫn được tạo vào hàng đợi của nguồn, và việc giao thẳng cho Người phụ trách khách hàng vẫn áp dụng vì ngoại lệ đó không phụ thuộc người khởi chạy; quy tắc tạm dừng quy trình khi người khởi chạy bị tạm ngưng hay rời đi của [`tasks-srs.md`](./tasks-srs.md) `BR-13.7` (c) chỉ áp cho quy trình tự động hóa, không áp cho nguồn này. Mỗi tài khoản gửi không có tiếp nhận hội thoại (tên thương hiệu SMS kèm đầu số hai chiều, tài khoản Zalo ZNS) là **một nguồn** thuộc loại nguồn "Liên hệ lại từ chiến dịch", có **đơn vị tiếp nhận** khai báo khi kết nối tài khoản gửi; mặc định lấy theo đơn vị tiếp nhận mặc định của loại nguồn đó ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-35-01`, `BR-35.12`). Người kết nối dùng mặc định tìm được; chọn đơn vị khác, đổi đơn vị tiếp nhận sau này hay bật "gồm các đơn vị con" chỉ Người có toàn quyền thực hiện. Tài khoản gửi chưa có đơn vị tiếp nhận không được chọn cho chiến dịch.
  - (c) **Loại bản ghi:** công việc liên hệ lại chưa có người phụ trách là **bản ghi chờ phân công** — thuộc đơn vị tiếp nhận của nguồn cho tới khi có người phụ trách, sau đó thuộc đơn vị theo người phụ trách như mọi công việc ([`tasks-srs.md`](./tasks-srs.md) `BR-13.6` (b)).
  - (d) **Giao thẳng cho người phụ trách khách hàng** (mặc định của `CFG-CAMP-53`): công việc có người phụ trách ngay từ đầu, không vào hàng đợi, và thuộc đơn vị theo người phụ trách đó ([`tasks-srs.md`](./tasks-srs.md) `BR-13.6` (b)). Khách không có người phụ trách, hoặc người phụ trách không đạt điều kiện người nhận của [`tasks-srs.md`](./tasks-srs.md) `BR-09.1` (gồm [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`), thì công việc vào hàng đợi của nguồn theo (c).
  - (e) **Nhận việc, trả về, chuyển:** nhận việc là thao tác Gán theo [`tasks-srs.md`](./tasks-srs.md) `BR-13.6` (c); trả về hàng đợi theo [`tasks-srs.md`](./tasks-srs.md) `BR-13.6` (d) — công việc chưa kết thúc bỏ người phụ trách và về hàng đợi hiện tại của tài khoản gửi đã sinh ra nó; **chuyển hàng đợi không áp dụng** ([`tasks-srs.md`](./tasks-srs.md) `BR-13.6` (e)) — muốn chuyển cho đội khác thì giao việc theo quy tắc giao việc của phân hệ Công việc. **Người phụ trách hiện tại** của một công việc liên hệ lại luôn có hai đường của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`, kể cả khi ô (Công việc, Gán) của họ chỉ là Chỉ của mình, theo [`tasks-srs.md`](./tasks-srs.md) `BR-13.6`: (1) **trả về hàng đợi** của nguồn bất kỳ lúc nào, chỉ với công việc của hàng đợi (công việc đã vào hàng đợi rồi được nhận việc hay giao); công việc đã giao thẳng cho người phụ trách khách hàng không phải công việc của hàng đợi ([`tasks-srs.md`](./tasks-srs.md) `BR-13.6` (b), (d)) nên không có đường này — người phụ trách chỉ dùng đề nghị chuyển ở (2), hoặc người có ô Gán giao lại theo quy tắc giao việc của phân hệ Công việc; (2) **đề nghị chuyển** cho một người cụ thể — chỉ có hiệu lực khi người nhận chấp nhận và tại lúc chấp nhận đạt điều kiện người nhận của [`tasks-srs.md`](./tasks-srs.md) `BR-09.1` (gồm [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`); đề nghị hết hạn theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-25-01` hoặc khi người phụ trách đổi.
  - (f) Phân hệ không có hàng đợi nào khác: chiến dịch, chuỗi nuôi dưỡng và sổ cái không qua hàng đợi và thuộc đơn vị theo người phụ trách (`BR-01.3`).

  **Lý do nghiệp vụ:** Công việc liên hệ lại sinh ra từ khách hàng trả lời, không do một nhân viên tạo; nếu không khai báo đơn vị tiếp nhận, công việc của khách chưa có người phụ trách chỉ người có mức Toàn workspace nhìn thấy và khách muốn mua không được ai liên hệ. Dùng đúng quy tắc hàng đợi của phân hệ Công việc để cùng một loại dữ liệu không có hai cách thuộc đơn vị khác nhau.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-35.1.1` | K trả lời tin WhatsApp của chiến dịch C "Tôi muốn đặt 2 sản phẩm" | Hệ thống xử lý | Hội thoại mới trong Hộp thư Hội thoại, gắn hồ sơ K, được phân công theo quy tắc của Hộp thư Hội thoại |
| `AC-35.1.2` | Kênh SMS không có tiếp nhận hội thoại; K trả lời SMS qua đầu số hai chiều "Còn hàng không?" | Hệ thống xử lý | Sổ cái ghi sự kiện Trả lời; dòng thời gian của K có tin trả lời; người phụ trách của K có một công việc liên hệ lại |
| `AC-35.1.3` | Tiếp nối AC-35.1.2; K gửi thêm 2 tin trả lời; L trả lời "TC" | Hệ thống xử lý | K vẫn chỉ có một công việc, chứa cả 3 tin; không có công việc nào cho L |
| `AC-35.2.1` | Tiếp nối AC-35.1.1 | Tư vấn viên mở hội thoại | Thấy "Trả lời chiến dịch C", thời điểm gửi và nội dung tin K đã nhận |
| `AC-35.2.2` | Tiếp nối AC-35.1.1 | Mở sổ cái C | Dòng của K có sự kiện Trả lời |
| `AC-35.3.1` | K đã Từ chối nhận tin WhatsApp; K trả lời một tin cũ | Kiểm tra đồng thuận | Vẫn Từ chối nhận tin |
| `AC-35.5.1` | Tên thương hiệu SMS "ShopABC" khai báo đơn vị tiếp nhận "Chăm sóc khách hàng"; K không có người phụ trách trả lời SMS qua đầu số hai chiều | Hệ thống xử lý | Công việc liên hệ lại vào hàng đợi của "Chăm sóc khách hàng"; thành viên đơn vị đó có ô (Công việc, Xem) khác Không có thấy công việc |
| `AC-35.5.2` | Tiếp nối AC-35.5.1; nhân viên N có (Công việc, Gán) = Không có | N bấm Nhận việc | Không cho phép, kèm lý do |
| `AC-35.5.3` | Tiếp nối AC-35.5.1; nhân viên C nhận việc rồi bị tạm ngưng, người thực hiện chọn trả về hàng đợi | Hệ thống xử lý | Công việc bỏ người phụ trách, về hàng đợi của tài khoản "ShopABC" và thuộc "Chăm sóc khách hàng" cho tới khi có người nhận mới |
| `AC-35.5.4` | Người có quyền Quản lý kênh gửi tiếp thị kết nối tài khoản Zalo ZNS mới, [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-35-01` cho loại nguồn này là "Chăm sóc khách hàng" | Chọn đơn vị tiếp nhận | "Chăm sóc khách hàng" được chọn sẵn; đơn vị khác bị vô hiệu kèm giải thích nếu người kết nối không phải Người có toàn quyền |
| `AC-35.5.5` | Tên thương hiệu SMS kèm đầu số hai chiều chưa có đơn vị tiếp nhận | Người soạn chọn tên thương hiệu này cho chiến dịch | Không chọn được; giao diện nêu tài khoản chưa khai báo đơn vị tiếp nhận |
| `AC-35.5.18` | Chiến dịch Email đặt địa chỉ nhận thư trả lời là hộp thư bên ngoài (đã có trong danh sách) | Kết nối và chọn cho chiến dịch | Không yêu cầu đơn vị tiếp nhận; thư trả lời không về hệ thống nên không sinh công việc liên hệ lại |
| `AC-35.5.6` | Công việc liên hệ lại trong hàng đợi "Chăm sóc khách hàng" | Người có ô (Công việc, Gán) bao phủ tìm thao tác chuyển sang hàng đợi khác | Không có thao tác chuyển hàng đợi; chỉ có giao việc cho một người cụ thể |
| `AC-35.5.9` | C đang phụ trách một công việc liên hệ lại đã nhận từ hàng đợi, ô (Công việc, Gán) = Chỉ của mình | C chọn trả về hàng đợi | Công việc về hàng đợi của tài khoản gửi đã sinh ra nó |
| `AC-35.5.16` | Công việc liên hệ lại đã giao thẳng cho P là người phụ trách khách hàng K | P tìm thao tác trả về hàng đợi | Không có; P chỉ có đề nghị chuyển, hoặc người có ô (Công việc, Gán) bao phủ giao lại cho người khác |
| `AC-35.5.10` | C đề nghị chuyển công việc cho D; D đạt điều kiện người nhận của [`tasks-srs.md`](./tasks-srs.md) `BR-09.1` (gồm [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`) | D chấp nhận trong hạn [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-25-01` | D là người phụ trách; nếu D không chấp nhận trong hạn, đề nghị hết hạn và C vẫn là người phụ trách |
| `AC-35.5.17` | C đề nghị chuyển công việc cho D; D không đạt điều kiện người nhận của [`tasks-srs.md`](./tasks-srs.md) `BR-09.1` (gồm [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`) vì không xem được công việc (ngoài mức Xem, hoặc bị chính sách Từ chối) | D bấm chấp nhận | Không chấp nhận được, kèm lý do; C vẫn là người phụ trách |
| `AC-35.5.11` | Chiến dịch do M khởi chạy, M có (Công việc, Gán) = Chỉ của mình; khách K có người phụ trách P ở đơn vị khác, P đạt điều kiện người nhận của [`tasks-srs.md`](./tasks-srs.md) `BR-09.1` (gồm [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`); `CFG-CAMP-53` mặc định | K trả lời SMS | Công việc giao thẳng cho P dù vượt trần Gán của M; nhật ký ghi Hệ thống tạo thay M |
| `AC-35.5.12` | Như AC-35.5.11 nhưng P có (Công việc, Xem) = Không có | K trả lời SMS | Công việc không giao cho P mà vào hàng đợi của tài khoản gửi |
| `AC-35.5.13` | Chiến dịch do M khởi chạy, M có (Công việc, Tạo) = Không có; khách K không có người phụ trách trả lời SMS | Hệ thống xử lý | Công việc liên hệ lại vẫn được tạo vào hàng đợi của tài khoản gửi; người tạo ghi là Hệ thống thay M |
| `AC-35.5.14` | Người khởi chạy M đã bị tạm ngưng; khách L không có người phụ trách trả lời SMS | Hệ thống xử lý | Công việc được tạo vào hàng đợi của tài khoản gửi, không bị tạm dừng hay bỏ qua; người tạo ghi "Hệ thống — nguồn <tên tài khoản gửi>" ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`) |
| `AC-35.5.15` | Người khởi chạy M đã rời workspace; khách K có người phụ trách P đạt điều kiện người nhận của [`tasks-srs.md`](./tasks-srs.md) `BR-09.1` (gồm [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`); `CFG-CAMP-53` mặc định | K trả lời SMS | Công việc giao thẳng cho P; người tạo ghi "Hệ thống — nguồn <tên tài khoản gửi>" ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`) |
| `AC-35.5.19` | Như AC-35.5.11 nhưng P có (Công việc, Sửa) = Không có | K trả lời SMS | P không đạt điều kiện người nhận; công việc vào hàng đợi của tài khoản gửi |
| `AC-35.5.20` | C đề nghị chuyển công việc cho D; D Đang hoạt động, xem được công việc nhưng (Công việc, Sửa) = Không có | D bấm chấp nhận | Không chấp nhận được, kèm lý do; C vẫn là người phụ trách |
| `AC-35.5.21` | Như `AC-35.5.11` nhưng P đang Tạm ngưng | K trả lời SMS | Công việc không giao cho P mà vào hàng đợi liên hệ lại của tài khoản gửi |
| `AC-35.5.7` | `CFG-CAMP-53` = Người phụ trách khách hàng; K có người phụ trách P thuộc "Kinh doanh – Hà Nội" trả lời SMS | Hệ thống xử lý | Công việc giao thẳng cho P, không vào hàng đợi, thuộc "Kinh doanh – Hà Nội" |
| `AC-35.5.8` | Tiếp nối AC-35.5.1; C có Đơn vị chính "Kinh doanh" và là thành viên "Chăm sóc khách hàng" qua đơn vị kiêm nhiệm mức Chỉ xem; C nhận việc | Hệ thống xử lý | Công việc thuộc "Kinh doanh" theo người phụ trách C |
| `AC-35.4.1` | Chiến dịch Zalo ZNS dùng mẫu không cho trả lời, nội dung không có số điện thoại hay liên kết liên hệ | Mở danh mục kiểm tra | Có cảnh báo "Người nhận không trả lời được tin này và không có thông tin liên hệ" |

---

#### FEAT-36 — Gắn Thẻ Tự động sau Tương tác

**Mô tả nghiệp vụ:** Tự động gắn thẻ phân loại lên hồ sơ khách hàng khi họ tương tác theo một cách cụ thể với chiến dịch, làm cơ sở cho phân khúc và chăm sóc tiếp theo.

**Quy tắc nghiệp vụ:**

- **`BR-36.1` (Quy tắc gắn thẻ):** Người soạn cấu hình quy tắc cho từng chiến dịch: khi người nhận **nhấp một liên kết cụ thể**, **nhấp bất kỳ liên kết nào**, hoặc **trả lời**, thì gắn một hoặc nhiều thẻ. Thẻ phải tuân theo quy tắc thẻ của (`contacts-srs.md`, `BR-03.2`); nếu thẻ chưa tồn tại, chỉ người có quyền tạo thẻ mới được dùng thẻ mới trong quy tắc.

  **Lý do nghiệp vụ:** Quy tắc gắn thẻ tự động có thể tạo thẻ trên hàng chục nghìn hồ sơ; không kiểm soát quyền tạo thẻ thì danh mục thẻ của doanh nghiệp nhanh chóng hỗn loạn.

- **`BR-36.2` (Không kích hoạt bởi tương tác không thật):** Lượt nhấp tự động (`BR-14.3`), sự kiện mở, và tương tác từ lượt gửi thử không kích hoạt gắn thẻ.

  **Lý do nghiệp vụ:** Sự kiện mở là số gần đúng (`BR-33.3`); gắn thẻ "quan tâm" dựa trên sự kiện mở sẽ gắn sai cho một phần lớn tệp khách.

- **`BR-36.3` (Giới hạn số thẻ):** Nếu hồ sơ đã chạm số thẻ tối đa theo (`contacts-srs.md`, `NFR-11`), thẻ không được gắn; sự kiện được ghi vào nhật ký của quy tắc để người soạn biết số lượt bỏ qua.

  **Lý do nghiệp vụ:** Giới hạn số thẻ thuộc phân hệ Khách hàng; phân hệ Chiến dịch không được vượt qua nó, nhưng phải cho người soạn biết quy tắc của họ đang bị bỏ qua.

- **`BR-36.4` (Chiều ngược):** Thẻ đã gắn tự động không bị gỡ khi chiến dịch bị hủy hay lưu trữ, hay khi quy tắc bị xóa — thẻ ghi nhận một sự thật đã xảy ra; cũng không bị gỡ khi người nhận hủy nhận tin **trên một kênh** mà vẫn còn Đồng ý nhận tin có căn cứ theo dõi (`BR-14.4`) trên ít nhất một kênh khác. Thẻ gắn tự động được đánh dấu nguồn là mã chiến dịch trên nhật ký hồ sơ khách hàng để có thể lọc và gỡ hàng loạt thủ công nếu cần. Khi một lượt nhấp **sau đó** được nhận diện lại là lượt nhấp tự động, thẻ gắn từ lượt nhấp đó được gỡ. Thẻ gắn tự động cũng bị gỡ khi hết thời hạn `CFG-CAMP-45` (`BR-32.3`) và khi căn cứ theo dõi hành vi của người đó chấm dứt trên **mọi** kênh (`BR-14.4` — người đó không còn Đồng ý nhận tin trên kênh nào, chọn "Hủy nhận tin trên toàn bộ mọi kênh", chuyển sang Hạn chế xử lý, hoặc mọi điều khoản còn hiệu lực không còn nêu mục đích đo lường).

  **Lý do nghiệp vụ:** Thẻ là dữ liệu suy ra từ hành vi; nó được giữ khi căn cứ còn hiệu lực vì nó ghi nhận một sự thật đã xảy ra, nhưng không được sống lâu hơn căn cứ cho phép thu thập nó.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-36.1.1` | Quy tắc: nhấp liên kết "Giải pháp AI" → gắn thẻ "Quan-tâm-AI" | K nhấp liên kết đó | Hồ sơ K có thẻ "Quan-tâm-AI", nguồn là mã chiến dịch |
| `AC-36.1.2` | Người soạn không có quyền tạo thẻ | Nhập một thẻ chưa tồn tại vào quy tắc | Không cho phép; chỉ chọn được thẻ đã có |
| `AC-36.2.1` | Quy tắc như trên | K chỉ mở thư, không nhấp | Không gắn thẻ |
| `AC-36.3.1` | Hồ sơ K đã có số thẻ tối đa | K nhấp liên kết | Không gắn thẻ; nhật ký quy tắc ghi một lượt bỏ qua |
| `AC-36.4.1` | K đã được gắn thẻ; K hủy nhận email nhưng vẫn Đồng ý nhận SMS theo điều khoản có nêu mục đích đo lường | Kiểm tra hồ sơ | Thẻ vẫn còn |
| `AC-36.4.2` | Quy tắc gắn thẻ bị xóa sau khi đã gắn cho 500 hồ sơ | Lọc hồ sơ theo nguồn gắn thẻ là mã chiến dịch | Tìm được đủ 500 hồ sơ; gỡ hàng loạt thủ công được |
| `AC-36.4.3` | K đã được gắn thẻ; K chọn "Hủy nhận tin trên toàn bộ mọi kênh" | Kiểm tra hồ sơ | Thẻ gắn tự động từ tương tác chiến dịch đã bị gỡ |

---

### Nhóm K — Tối ưu & Chiến dịch Nâng cao

#### FEAT-37 — Dự phòng Kênh

**Mô tả nghiệp vụ:** Khi kênh chính xác nhận không tới được một người nhận, hệ thống gửi thông điệp cho người đó qua kênh dự phòng đã cấu hình, nếu người đó được phép nhận tiếp thị trên kênh dự phòng.

**Quy tắc nghiệp vụ:**

- **`BR-37.1` (Cấu hình chuỗi dự phòng):** Chiến dịch có một hoặc nhiều kênh dự phòng xếp thứ tự, số kênh tối đa theo `CFG-CAMP-48` (ví dụ Zalo ZNS → SMS → Email). Mỗi kênh dự phòng cần tài khoản gửi và nội dung riêng (`BR-11.4`). Cấu hình dự phòng là một phần của phiên bản được phê duyệt: người duyệt thấy chuỗi dự phòng, nội dung từng kênh dự phòng và chi phí dự phòng tối đa (`BR-41.3`).

  **Lý do nghiệp vụ:** Mỗi kênh dự phòng là một thông điệp khác tới khách hàng, bằng một kênh có chi phí khác; người duyệt phải thấy nó như thấy kênh chính.

- **`BR-37.2` (Chỉ dự phòng khi đã xác nhận không tới):** Dự phòng chỉ được kích hoạt khi dòng sổ cái ở kênh trước ở **Thất bại vĩnh viễn** thuộc các nhóm lỗi có "Kích hoạt dự phòng" tại `BR-24.1`. **Không** kích hoạt khi: Thất bại tạm thời, Chưa xác định, người nhận **bị loại trừ ở kênh chính** (vì bất kỳ lý do nào), người nhận chặn doanh nghiệp, hoặc tin đã được phân phát nhưng người nhận không mở. Khi đã vào chuỗi dự phòng, một kênh dự phòng mà người nhận bị loại trừ ở kênh đó (theo `BR-37.3`) thì được bỏ qua và chuyển sang kênh dự phòng kế tiếp. **Kênh thay thế:** nếu phiên bản đã duyệt bật "Gửi qua kênh thay thế cho người không dùng được kênh chính", người bị loại ở kênh chính **chỉ** vì lý do trung tính — lý do 3 (không có điểm đến; riêng Zalo OA, người đã **bỏ quan tâm** Tài khoản Chính thức không được coi là trung tính) hoặc lý do 6 (chưa có Đồng ý có bằng chứng trên kênh chính), và **không rơi vào bất kỳ lý do nào khác** của `BR-08.1` khi xét toàn bộ danh mục (không chỉ lý do được ghi theo thứ tự ưu tiên) — được gửi ngay qua kênh dự phòng đầu tiên mà người đó qua được kiểm tra đầy đủ của `BR-37.3`; người bị loại vì mọi lý do khác (từ chối, hạn chế xử lý, tần suất, giới hạn 24 giờ theo điểm đến…) không bao giờ được chuyển kênh.

  **Lý do nghiệp vụ:** Dự phòng khi chưa chắc chắn thì người nhận có thể nhận cùng thông điệp ở hai kênh. Kênh thay thế chỉ mở cho lý do trung tính vì "không có Zalo" hay "chưa đồng ý nhận qua Zalo" không nói gì về ý muốn nhận tin qua kênh thay thế — việc gửi ở kênh thay thế vẫn phải qua đúng lý do 5, 6 của kênh đó theo chính sách doanh nghiệp (`CFG-CAMP-74`). Dự phòng khi người nhận chưa mở biến tính năng thành "gửi nhắc lại qua kênh khác" — đó là một chiến dịch khác (chuỗi nuôi dưỡng, `FEAT-39`) và phải chịu giới hạn tần suất như một tin mới. Người bị loại vì từ chối, hạn chế xử lý hay giới hạn tần suất ở kênh chính không được "tới bằng đường khác".

- **`BR-37.3` (Kiểm tra đầy đủ trên kênh dự phòng):** Trước khi gửi dự phòng, người nhận phải qua **toàn bộ** lý do loại trừ 1–7 và 10–15 của `BR-08.1` (cộng lý do 8 với các tập loại trừ được đánh dấu "xét lại tại thời điểm gửi", và lý do 9 — điểm đến dự phòng không được trùng với điểm đến đã hoặc sẽ nhận tin của lượt gửi này, kể cả từ hồ sơ khác) và cả hai quy tắc (a), (b) của `BR-08.4`, xét trên **kênh dự phòng** — trong đó có: có điểm đến, điểm đến tiếp cận được, **qua được lý do 5 và 6** trên kênh dự phòng (Đồng ý có bằng chứng nếu kênh dự phòng thuộc `CFG-CAMP-74`), không thuộc Danh sách không quảng cáo, nội dung đã được nhà mạng duyệt, khung giờ yên lặng và giới hạn 24 giờ theo điểm đến của kênh dự phòng, và nguyên tắc chặt nhất thắng với mọi hồ sơ dùng chung điểm đến dự phòng.

  **Lý do nghiệp vụ:** Đồng ý trên Zalo không phải đồng ý trên SMS; dự phòng không được trở thành đường gửi tới một kênh mà người nhận đã từ chối, hoặc chưa đồng ý khi chính sách của doanh nghiệp yêu cầu đồng ý trên kênh đó.

- **`BR-37.4` (Thời gian chờ):** Lượt dự phòng được gửi sau thời gian chờ `CFG-CAMP-08` tính từ lúc kênh trước xác nhận thất bại vĩnh viễn.

  **Lý do nghiệp vụ:** Khoảng chờ để các báo cáo phân phát đến muộn của kênh trước kịp về, giảm rủi ro gửi trùng.

- **`BR-37.5` (Một thông điệp cho một người):** Người đã được phân phát ở một kênh trong chuỗi không được gửi ở kênh tiếp theo. Nếu nhà cung cấp của kênh trước báo phân phát muộn trong lúc lượt dự phòng đang chờ, lượt dự phòng bị hủy. Cả chuỗi tính là **một** tin cho giới hạn tần suất (`BR-08.5`).

  **Lý do nghiệp vụ:** Nguyên tắc 3 (Mục 2.4).

- **`BR-37.6` (Hạn mức cho lượt dự phòng):** Đối chiếu hạn mức trước khi bắt đầu (`BR-17.8`) chỉ tính khối lượng kênh chính. Mỗi đợt dự phòng được đối chiếu hạn mức theo khối lượng của chính đợt đó trước khi bắt đầu; không đủ thì đợt dự phòng được giữ chờ trong thời gian `CFG-CAMP-49`, người phụ trách, Chủ sở hữu và Người phụ trách thanh toán được thông báo; nếu trong thời gian đó trần được nâng lên một mức mới cụ thể theo (`billing-subscription-srs.md`, `BR-16.5b`), đợt dự phòng chạy (qua lại đầy đủ kiểm tra tại thời điểm gửi); quá hạn thì người nhận ghi "Không gửi dự phòng — không đủ hạn mức". Việc thiếu hạn mức cho dự phòng **không** tạm dừng phần gửi của kênh chính.

  **Lý do nghiệp vụ:** Khối lượng dự phòng không biết trước. Tính cả khối lượng dự phòng tối đa vào lúc bắt đầu sẽ khiến gần như mọi chiến dịch có dự phòng bị chặn vì "có thể" vượt hạn mức. Tách đợt dự phòng thành lô riêng vẫn giữ đúng nguyên tắc "không khởi động rồi dừng giữa chừng" của (`billing-subscription-srs.md`, `BR-16.9`) cho từng lô.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-37.1.1` | `CFG-CAMP-48` = 2; màn hình cấu hình dự phòng | Thêm kênh dự phòng thứ ba | Không cho phép; giao diện ghi rõ giới hạn |
| `AC-37.2.1` | Chiến dịch Zalo ZNS → SMS; 50 người không có tài khoản Zalo, đều Đồng ý nhận SMS | Chờ `CFG-CAMP-08` | 50 người được gửi SMS; sổ cái ghi "Zalo ZNS: Thất bại vĩnh viễn → SMS: Đã phân phát" |
| `AC-37.2.2` | 30 người ở Chưa xác định trên Zalo ZNS | Chiến dịch hoàn tất | 30 người không được gửi SMS |
| `AC-37.2.3` | K đã chặn tài khoản Zalo của doanh nghiệp | Hệ thống xử lý | K không được gửi SMS |
| `AC-37.2.4` | K bị loại ở Zalo ZNS vì Chạm giới hạn tần suất | Hệ thống xử lý | K không được gửi SMS |
| `AC-37.2.5` | `CFG-CAMP-74` mặc định; chiến dịch Zalo ZNS bật kênh thay thế, dự phòng SMS; K chưa có Đồng ý Zalo ZNS nhưng có Đồng ý SMS | Chốt danh sách và gửi | K được gửi SMS ngay; sổ cái ghi "Zalo ZNS: Bị loại — chưa có Đồng ý → SMS: Đã phân phát" |
| `AC-37.2.6` | Như trên; L Từ chối nhận tin Zalo ZNS, có Đồng ý SMS | Chốt danh sách và gửi | L không được gửi SMS (từ chối không phải lý do trung tính) |
| `AC-37.2.7` | Như AC-37.2.5; K chưa có Đồng ý Zalo ZNS và đồng thời thuộc tập loại trừ "có vé khiếu nại mở" | Chốt danh sách và gửi | K không được gửi SMS (có lý do khác ngoài lý do trung tính) |
| `AC-37.3.1` | K không có tài khoản Zalo, Từ chối nhận tin SMS, Đồng ý nhận email; chuỗi Zalo ZNS → SMS → Email | Hệ thống xử lý | SMS bỏ qua (Từ chối nhận tin); K được gửi email |
| `AC-37.3.2` | Thất bại Zalo ZNS xác nhận lúc 21:58; SMS thuộc khung giờ yên lặng 22:00–08:00; `CFG-CAMP-08` = 5 phút | Tới 22:03 | SMS của K bị hoãn tới 08:00 |
| `AC-37.3.3` | H1, H2 có số điện thoại khác nhau nhưng cùng một email; cả hai thất bại Zalo ZNS, dự phòng là Email | Hệ thống xử lý | Email đó nhận đúng một thư |
| `AC-37.3.4` | `CFG-CAMP-74` chỉ gồm Zalo ZNS; chiến dịch Zalo ZNS dự phòng SMS; K có Đồng ý Zalo ZNS, Chưa có đồng thuận trên SMS, không có tài khoản Zalo | Dự phòng tới lượt | K nhận SMS dự phòng (SMS không yêu cầu Đồng ý trước); dòng sổ cái ghi căn cứ "kênh không yêu cầu Đồng ý" kèm phiên bản chính sách |
| `AC-37.4.1` | `CFG-CAMP-08` = 30 phút; Zalo ZNS xác nhận thất bại lúc 10:00 | Chờ | SMS dự phòng được gửi không sớm hơn 10:30 |
| `AC-37.5.1` | Lượt SMS dự phòng của K đang chờ; nền tảng Zalo ZNS báo muộn đã phân phát | Tới giờ gửi SMS | Không gửi SMS; dòng chuyển Đã phân phát qua Zalo ZNS |
| `AC-37.6.1` | Hạn mức còn 20 tin SMS; đợt dự phòng cần 50; không ai nâng trần trong thời gian giữ chờ `CFG-CAMP-49` | Hết thời gian giữ chờ | 50 người ghi "Không gửi dự phòng — không đủ hạn mức"; kênh chính tiếp tục bình thường |
| `AC-37.6.2` | Như trên; Người phụ trách thanh toán nâng trần hạn mức lên một mức mới cụ thể đủ cho đợt dự phòng trong thời gian giữ chờ `CFG-CAMP-49` | Hệ thống xử lý | Đợt dự phòng 50 tin chạy |

---

#### FEAT-38 — Thử nghiệm Phiên bản (A/B)

**Mô tả nghiệp vụ:** Gửi nhiều phiên bản nội dung cho một phần nhỏ người nhận, chọn phiên bản tốt nhất, rồi gửi phiên bản đó cho phần còn lại.

**Quy tắc nghiệp vụ:**

- **`BR-38.1` (Cấu hình):** Người soạn tạo các phiên bản (số lượng theo Phụ lục B.2) khác nhau về tiêu đề, nội dung, tên người gửi hoặc tin nhắn mẫu. Tỷ lệ tệp thử nghiệm theo `CFG-CAMP-24` (chia đều cho các phiên bản), thời gian đánh giá theo `CFG-CAMP-25`, tiêu chí chọn phiên bản thắng theo `CFG-CAMP-26`. Người nhận được phân vào phiên bản **ngẫu nhiên**. Mọi phiên bản đều phải qua danh mục kiểm tra và được phê duyệt cùng lúc như một phiên bản duyệt duy nhất.

  **Lý do nghiệp vụ:** Phân ngẫu nhiên để hai nhóm so sánh được với nhau; duyệt cùng lúc vì phiên bản thắng sẽ tới phần lớn người nhận mà không qua thêm một bước duyệt nào.

- **`BR-38.2` (Cỡ mẫu tối thiểu):** Nếu tệp thử nghiệm cho mỗi phiên bản ít hơn `CFG-CAMP-37` người khả dụng, danh mục kiểm tra hiển thị cảnh báo rằng kết quả có thể không đủ tin cậy.

  **Lý do nghiệp vụ:** Thử nghiệm trên vài trăm người gần như luôn cho ra một "người thắng" ngẫu nhiên.

- **`BR-38.3` (Chọn phiên bản thắng):** Hết thời gian đánh giá, phiên bản có chỉ số theo tiêu chí cao nhất thắng. Nếu chênh lệch giữa phiên bản cao nhất và phiên bản thứ hai **không có ý nghĩa thống kê** ở mức tin cậy `CFG-CAMP-57`, hệ thống **không tự chọn phiên bản thắng** mà áp **phương án khi hòa** người soạn đã chọn trong phiên bản duyệt: **(i) Dùng A ngay** (mặc định) — gửi phiên bản A cho phần còn lại ngay khi hết thời gian đánh giá và thông báo kết quả hòa; hoặc **(ii) Chờ chọn thủ công** — thông báo người phụ trách và người duyệt để chọn; nếu sau `CFG-CAMP-38` không ai chọn thì dùng phiên bản A. Người có quyền Phát sóng chiến dịch luôn được chọn thủ công trước khi hết thời gian đánh giá.

  **Lý do nghiệp vụ:** Tuyên bố một phiên bản thắng chỉ vì 0,2% chênh lệch trên vài trăm người là ngẫu nhiên, và dạy đội tiếp thị những "bài học" sai.

- **`BR-38.4` (Gửi phần còn lại):** Phiên bản thắng được gửi cho phần còn lại của danh sách chốt, qua đầy đủ kiểm tra tại thời điểm gửi và khung giờ yên lặng. Phần còn lại không bao gồm người đã nhận một phiên bản thử nghiệm. Nếu chiến dịch bị tạm dừng trong thời gian đánh giá, thời gian đánh giá **không** dừng theo, nhưng việc gửi phần còn lại chỉ bắt đầu sau khi chiến dịch được tiếp tục.

  **Lý do nghiệp vụ:** Người đã nhận một phiên bản không được nhận thêm phiên bản khác; kết quả đánh giá dựa trên hành vi đã xảy ra nên không cần dừng theo lúc tạm dừng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-38.1.1` | 20.000 người khả dụng, 2 phiên bản, `CFG-CAMP-24` = 20% | Phát sóng | 2.000 người nhận A, 2.000 người nhận B, phân ngẫu nhiên |
| `AC-38.2.1` | 3.000 người khả dụng, 2 phiên bản, tỷ lệ tệp thử nghiệm 20% (300 người mỗi phiên bản) | Mở danh mục kiểm tra | Có cảnh báo kết quả thử nghiệm có thể không đủ tin cậy |
| `AC-38.3.1` | Tiêu chí tỷ lệ nhấp, `CFG-CAMP-57` = 95%; A 6,1%, B 3,0% trên 2.000 người mỗi phiên bản | Hết thời gian đánh giá | A thắng tự động; 16.000 người còn lại nhận A |
| `AC-38.3.2` | Phương án khi hòa là "Chờ chọn thủ công"; A 5,1%, B 5,0% trên 2.000 người mỗi phiên bản | Hết thời gian đánh giá | Không tự chọn; người phụ trách và người duyệt nhận thông báo chọn thủ công |
| `AC-38.3.3` | Tiếp nối AC-38.3.2; phương án khi hòa là "chờ chọn thủ công"; không ai chọn trong `CFG-CAMP-38` | Chờ | Phiên bản A được gửi cho phần còn lại |
| `AC-38.3.4` | Như AC-38.3.2; phương án khi hòa là mặc định | Hết thời gian đánh giá | Phiên bản A được gửi ngay cho phần còn lại, người phụ trách được thông báo kết quả hòa |
| `AC-38.4.1` | Tiếp nối AC-38.3.1 | Xem sổ cái | Không ai nhận cả A và B, hay nhận hai lần |
| `AC-38.4.2` | Chiến dịch bị tạm dừng giữa thời gian đánh giá; hết thời gian đánh giá khi vẫn tạm dừng | Tiếp tục chiến dịch | Phiên bản thắng được xác định theo kết quả đến hết thời gian đánh giá; phần còn lại chỉ bắt đầu gửi sau khi tiếp tục |

---

#### FEAT-39 — Chuỗi Nuôi dưỡng Nhiều Bước

**Mô tả nghiệp vụ:** Chuỗi các bước gửi tự động theo thời gian và theo hành vi của người nhận (ví dụ: gửi email chào mừng; sau 2 ngày nếu đã nhấp thì gửi WhatsApp giới thiệu sản phẩm; nếu chưa nhấp thì sau 3 ngày gửi lại email với tiêu đề khác).

**Quy tắc nghiệp vụ:**

- **`BR-39.1` (Cấu trúc và vòng đời của chuỗi):** Chuỗi nuôi dưỡng là một khái niệm riêng, không phải một chiến dịch gửi một lần. Điều kiện vào (kể cả điều kiện dựa trên Danh sách Hiển thị Dùng chung — bộ lọc được sao vào phiên bản chuỗi như `BR-05.1`, nên chủ danh sách sửa danh sách không làm đổi điều kiện vào của chuỗi đang chạy) và thao tác thêm thủ công chịu đúng `BR-05.3`, `BR-05.4`, `BR-05.5` như tiêu chí đối tượng của chiến dịch: chỉ khách hàng có hồ sơ, thuộc tập khách hàng xem được theo `BR-05.4` của người chọn đối tượng và của người khởi chạy chuỗi (`BR-17.10`), và trường nhạy cảm chỉ dùng được với đúng điều kiện của `BR-05.5` (kiểm tra lại trước mỗi bước theo lý do 15). Chuỗi gồm: **điều kiện vào** (khách hàng bắt đầu khớp một tiêu chí đối tượng, hoặc được thêm thủ công); các **bước** gửi (mỗi bước có kênh, nội dung, tài khoản gửi, thời gian chờ từ bước trước); **nhánh điều kiện** theo hành vi ở bước trước (đã nhấp / chưa nhấp / đã trả lời), kèm **nhánh mặc định** được khai báo và duyệt cho người không có căn cứ theo dõi hành vi (`BR-14.4`) — người này không bao giờ bị xếp vào "chưa nhấp"; **điều kiện thoát**. Vòng đời của chuỗi:

  | Trạng thái | Ý nghĩa | Chuyển được sang |
  | --- | --- | --- |
  | **Nháp** | Đang soạn | Chờ duyệt; Đã duyệt *(tự xác nhận khi tắt phê duyệt kép — `BR-17.9`)*; Thùng rác |
  | **Chờ duyệt** | Chờ duyệt toàn bộ các bước như một phiên bản | Đã duyệt; Nháp *(bị từ chối, rút lại, có người sửa)*; Thùng rác |
  | **Đã duyệt** | Đã duyệt, chưa bắt đầu | Đang chạy *(người có quyền Phát sóng chiến dịch bấm Bắt đầu và qua bước 1–2 của `BR-17.8`)*; Nháp *(có người sửa, hoặc lỗi chặn nhóm (a) xuất hiện)* |
  | **Đang chạy** | Nhận người mới và gửi các bước; tạm không nhận người mới khi chờ bàn giao người chọn đối tượng (`BR-05.4` (e)) | Tạm dừng; Ngừng nhận người mới; Kết thúc *(hủy)* |
  | **Tạm dừng** | Không gửi bước nào, không nhận người mới; do người dùng, tự động tạm dừng bảo vệ (`FEAT-20`) hoặc dừng khẩn cấp (`FEAT-42`) | Trạng thái trước khi tạm dừng *(tiếp tục — Đang chạy hoặc Ngừng nhận người mới; chuyển thẳng sang Ngừng nhận người mới cũng là một lần tiếp tục và chịu cùng điều kiện xác nhận của `BR-20.2`)*; Kết thúc *(hủy)* |
  | **Ngừng nhận người mới** | Không nhận người mới; người đang trong chuỗi đi tiếp tới hết | Kết thúc *(khi không còn ai trong chuỗi, hoặc hủy)*; Tạm dừng |
  | **Kết thúc** | Không còn hoạt động; mọi người còn trong chuỗi (nếu hủy) thoát với lý do "Chuỗi bị hủy" | Lưu trữ |
  | **Lưu trữ** | Ẩn khỏi danh sách mặc định; báo cáo giữ nguyên | Kết thúc *(bỏ lưu trữ)* |
  | **Thùng rác** | Chuỗi chưa từng gửi đã bị xóa (`BR-04.2`) | Nháp *(khôi phục)* |

  **Hủy** chuỗi cần quyền Phát sóng chiến dịch và lý do bắt buộc. Chuỗi **không** bị tự hủy khi tạm dừng lâu (`BR-19.4` không áp cho chuỗi); thay vào đó người phụ trách được nhắc mỗi `CFG-CAMP-50` khi chuỗi còn tạm dừng. Thời gian chờ giữa các bước là khoảng trượt tuyệt đối (Mục 2.3).

  Trong các trạng thái Đang chạy, Tạm dừng và Ngừng nhận người mới, người soạn được tạo **phiên bản mới** của chuỗi (ở Đã duyệt, sửa thì chuỗi về Nháp); phiên bản đang chạy tiếp tục chạy cho tới khi phiên bản mới được duyệt (`BR-39.5`).

  **Khác biệt có chủ đích so với chiến dịch gửi một lần:** mỗi bước có kênh riêng (ngoại lệ của `BR-09.2`, vì mỗi bước là một thông điệp riêng biệt theo thời gian, không phải cùng một thông điệp gửi song song); không có danh sách người nhận chốt — mỗi người được kiểm tra khi vào chuỗi và trước mỗi bước (ngoại lệ của `BR-06.3`, `BR-06.4`); chuỗi được sửa trong khi đang chạy qua cơ chế phiên bản (ngoại lệ của `BR-01.6`); thời hạn gửi lại và thời hạn lưu sổ cái tính theo từng lượt gửi (`BR-25.2`, `BR-32.3`); đối chiếu hạn mức gói (`BR-17.8` bước 3) thực hiện theo từng đợt gửi của từng bước, không theo toàn chuỗi; hạn ngạch ngày không có cách xử lý "Chờ" mà bước chạm hạn ngạch luôn được hoãn sang ngày có hạn ngạch, và chuỗi không bị tạm dừng bảo vệ vì hết hạn ngạch (`BR-20.1` mục 5).

  **Lý do nghiệp vụ:** Chuỗi chạy liên tục nhiều tháng; tự hủy chuỗi sau một kỳ nghỉ dài sẽ đẩy hàng nghìn người ra khỏi chuỗi không khôi phục được, còn tạm dừng mọi chuỗi chào mừng chỉ vì các chiến dịch một lần đã dùng hết hạn ngạch sáng nay là để chiến dịch một lần làm hỏng việc chăm sóc thường xuyên. Nếu bắt chuỗi theo vòng đời của chiến dịch một lần thì hoặc không bao giờ sửa được, hoặc phải dừng hàng nghìn người đang ở giữa chuỗi chỉ để đổi một câu chữ. "Ngừng nhận người mới" cho phép khép lại một chuỗi mà không cắt ngang những người đang được chăm sóc.

- **`BR-39.2` (Điều kiện thoát bắt buộc):** Ngoài điều kiện thoát do người cấu hình đặt, một người **luôn** thoát khỏi chuỗi khi: Từ chối nhận tin trên **mọi** kênh mà các bước còn lại sử dụng; chuyển sang Hạn chế xử lý; chuyển sang giai đoạn vòng đời không được nhận tiếp thị; hồ sơ bị xóa hoặc bị gộp vào hồ sơ khác (Bản ghi Chính không tự động được thêm vào chuỗi). Người Từ chối nhận tin chỉ trên kênh của một bước thì bước đó bị bỏ qua (ghi lý do) và người đó đi tiếp theo nhánh "chưa nhấp / chưa trả lời" — hoặc theo nhánh mặc định nếu người đó không có căn cứ theo dõi hành vi (`BR-14.4`). Người đang bị tạm loại chờ người phụ trách quyết định giai đoạn (`BR-14.3`) **không** thoát chuỗi — bước của họ được hoãn như giới hạn tần suất (`BR-39.4`).

  **Lý do nghiệp vụ:** Chuỗi chạy tự động không người trông; nếu không có điều kiện thoát bắt buộc, người đã từ chối hay đã bị hạn chế xử lý vẫn nằm trong chuỗi và chỉ được bảo vệ bởi kiểm tra từng bước — không đủ khi chuỗi có nhánh gửi sang kênh khác.

- **`BR-39.3` (Vào lại chuỗi):** Một người đã hoàn tất hoặc đã thoát khỏi chuỗi **không** được vào lại, trừ khi chuỗi được cấu hình cho phép vào lại sau một khoảng thời gian tối thiểu do người cấu hình đặt (là một phần của phiên bản duyệt).

  **Lý do nghiệp vụ:** Không có quy tắc này, một khách hàng bị gỡ thẻ rồi gắn lại (do nhập dữ liệu, do quy tắc tự động) sẽ nhận lại toàn bộ chuỗi chào mừng từ đầu.

- **`BR-39.4` (Mọi bước chịu đầy đủ kiểm soát):** Mỗi bước là một lượt gửi Tiếp thị chịu đầy đủ kiểm tra tại thời điểm gửi, giới hạn 24 giờ theo điểm đến, khung giờ yên lặng, ngày không gửi, hạn ngạch ngày (chỉ phần chưa ai giữ chỗ — luôn gồm ít nhất phần ngoài `CFG-CAMP-55` — `BR-40.4`) và hạn mức gói dịch vụ. Khi bước của một chuỗi bị hoãn vì hạn ngạch quá khoảng `CFG-CAMP-61`, người phụ trách chuỗi được thông báo kèm số bước đang hoãn và các chiến dịch đang giữ chỗ — một lần mỗi ngày cho tới khi hết bước bị hoãn vì hạn ngạch. Chuỗi **có thể** được đánh dấu **miễn trừ giới hạn tần suất** theo đúng điều kiện của `BR-08.6` (người soạn đề xuất kèm lý do, là một phần của phiên bản chuỗi được người khác duyệt; vẫn được đếm vào tần suất của chiến dịch khác; không miễn trừ tần suất trong điều khoản đồng thuận hay giới hạn 24 giờ theo điểm đến). Miễn trừ của chuỗi chỉ áp cho các bước tới hạn trong khoảng thời gian `CFG-CAMP-62` kể từ khi người đó vào chuỗi **lần đầu**; lần vào lại (`BR-39.3`) không được miễn trừ; bước sau khoảng đó chịu giới hạn tần suất như chuỗi không miễn trừ. Riêng giới hạn tần suất (với chuỗi không miễn trừ) và giới hạn 24 giờ theo điểm đến: bước chạm giới hạn được **hoãn** tới khi người đó hết chạm giới hạn, tối đa bằng thời gian chờ của bước tiếp theo (với bước cuối: bằng thời gian chờ của chính bước đó); quá thời hạn đó thì bước bị bỏ qua (ghi lý do) và người đó đi tiếp theo nhánh "chưa nhấp / chưa trả lời" — hoặc theo nhánh mặc định nếu người đó không có căn cứ theo dõi hành vi (`BR-14.4`). Nếu đó là bước cuối, người đó hoàn tất chuỗi.

  **Lý do nghiệp vụ:** Loại hẳn một bước chỉ vì tần suất sẽ làm chuỗi mất mạch; hoãn có giới hạn giữ được mạch mà không vượt giới hạn làm phiền.

- **`BR-39.5` (Sửa chuỗi đang chạy):** Phiên bản mới của chuỗi phải được phê duyệt (`FEAT-17`). Sau khi được duyệt: người đang chờ một bước **chưa tới** nhận phiên bản mới của bước đó; bước bị xóa thì bị bỏ qua; bước thêm mới **trước** vị trí hiện tại của một người thì không áp dụng cho người đó. Bị từ chối thì phiên bản đang chạy giữ nguyên. Tạm dừng chuỗi dừng mọi bước chưa gửi của mọi người; khách hàng bắt đầu khớp điều kiện vào trong lúc chuỗi Tạm dừng (không phải Ngừng nhận người mới) được **ghi danh bù** khi chuỗi tiếp tục, với các bước tính từ thời điểm tiếp tục — **trừ** khi lần tạm dừng do người khởi chạy bị tạm ngưng, rời workspace hay mất quyền (`BR-17.10`, `BR-17.11`) hoặc trùng với thời gian chuỗi tạm không nhận người mới vì chờ bàn giao người chọn đối tượng (`BR-05.4` (e)): khi đó không ghi danh bù cho khoảng thời gian đó, kể cả khi người chọn đối tượng đồng thời là người khởi chạy, vì quy tắc không ghi danh hồi tố của `BR-05.4` (e) thắng; tiếp tục thì các bước đã quá hạn trong lúc tạm dừng được gửi theo nhịp, không dồn cục.

  **Lý do nghiệp vụ:** Nguyên tắc 2 (Mục 2.4) — chỉ phiên bản đã duyệt được gửi — vẫn giữ nguyên trong khi chuỗi không phải dừng để sửa.

- **`BR-39.6` (Báo cáo chuỗi — quy tắc của tài liệu này, khác `BR-39.6` về thứ tự hợp nhất quyền của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md)):** Báo cáo theo từng bước (số người tới bước, phân phát, tương tác, hoãn và bỏ qua theo lý do), số người đang ở từng bước, số người thoát theo lý do.

  **Lý do nghiệp vụ:** Chuỗi chạy nhiều tháng qua nhiều bước; chỉ số tổng làm mất dấu bước nào đang khiến người nhận thoát chuỗi hay khiếu nại.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-39.1.1` | Chuỗi: B1 email; sau 2 ngày nếu nhấp → B2 WhatsApp; nếu không → B3 email tiêu đề khác sau 3 ngày | K vào chuỗi và nhấp B1 | 2 ngày sau K nhận B2, không nhận B3 |
| `AC-39.1.2` | Chuỗi Đang chạy | Chuyển sang Ngừng nhận người mới | Khách mới khớp điều kiện vào không được thêm; người đang trong chuỗi vẫn nhận các bước còn lại; khi người cuối cùng hoàn tất, chuỗi Kết thúc |
| `AC-39.1.3` | Chuỗi Đang chạy có 2.000 người đang ở giữa chuỗi | M hủy chuỗi kèm lý do | Chuỗi Kết thúc; 2.000 người thoát với lý do "Chuỗi bị hủy"; không bước nào được gửi thêm |
| `AC-39.1.4` | Chuỗi Đã duyệt; tài khoản gửi của một bước đang ngừng hoạt động | M bấm Bắt đầu | Chuỗi vẫn Đã duyệt; M được báo bước kiểm tra không đạt |
| `AC-39.1.5` | Chuỗi Tạm dừng 20 ngày qua Tết; `CFG-CAMP-17` = 14 ngày, `CFG-CAMP-50` = 7 ngày | Chờ | Chuỗi không bị hủy; người phụ trách nhận nhắc sau 7 và 14 ngày |
| `AC-39.1.6` | Chuỗi ở Ngừng nhận người mới bị tạm dừng bảo vệ | Người có quyền tiếp tục | Chuỗi về Ngừng nhận người mới, không mở lại việc nhận người mới |
| `AC-39.1.7` | Chuỗi Đang chạy có điều kiện vào là một Danh sách Hiển thị Dùng chung lọc "khu vực = Hà Nội"; chủ danh sách đổi thành "khu vực = Hà Nội hoặc tình trạng sức khỏe = …" | Khách K chỉ thỏa phần điều kiện mới (không ở Hà Nội) | K không vào chuỗi vì chuỗi dùng bộ lọc đã sao trong phiên bản; điều kiện vào không có trường nhạy cảm |
| `AC-39.2.1` | K đang chờ B3 (email); K Từ chối nhận tin email và mọi bước còn lại đều là email | Hệ thống xử lý | K thoát chuỗi, lý do "Từ chối nhận tin" |
| `AC-39.2.2` | K Từ chối nhận tin WhatsApp; bước tiếp theo là WhatsApp, bước sau nữa là email | Tới bước WhatsApp | Bước bị bỏ qua (ghi lý do); K đi tiếp tới bước email |
| `AC-39.3.1` | K đã hoàn tất chuỗi; K bị gỡ rồi gắn lại thẻ khớp điều kiện vào; chuỗi không cho vào lại | Hệ thống xử lý | K không vào lại chuỗi |
| `AC-39.3.2` | Chuỗi cho phép vào lại sau 90 ngày; K hoàn tất chuỗi 100 ngày trước và lại khớp điều kiện vào | Hệ thống xử lý | K vào lại chuỗi |
| `AC-39.4.1` | Tới B2 của K nhưng K đang chạm giới hạn tần suất, sẽ hết chạm sau 1 ngày; thời gian chờ bước sau là 3 ngày | Hệ thống xử lý | B2 hoãn 1 ngày rồi gửi |
| `AC-39.4.2` | Các chiến dịch đã giữ chỗ 90% hạn ngạch email của ngày, 10% còn lại đã bị lượt dự phòng và chuỗi khác dùng hết lúc 10:00; 300 người có bước email của chuỗi chào mừng tới hạn chiều nay | Hệ thống xử lý | Chuỗi không bị tạm dừng; 300 bước được hoãn sang ngày có hạn ngạch |
| `AC-39.4.3` | K không có căn cứ theo dõi hành vi; bước 2 của K bị bỏ qua vì K Từ chối nhận tin trên kênh của bước 2 | Hệ thống xử lý | K đi theo nhánh mặc định đã khai báo, không vào nhánh "chưa nhấp" |
| `AC-39.4.4` | Chuỗi chào mừng 4 bước trong 7 ngày được duyệt miễn trừ tần suất; giới hạn 2 tin / 7 ngày; điều khoản K đã đồng ý không nêu tần suất | K đi hết chuỗi | K nhận đủ 4 bước (mỗi bước vẫn chịu giới hạn 24 giờ theo điểm đến); các chiến dịch khác trong tuần tính 4 tin này vào tần suất của K |
| `AC-39.4.5` | Chuỗi được duyệt miễn trừ tần suất, `CFG-CAMP-62` = 14 ngày; bước 5 của K tới hạn vào ngày thứ 20 kể từ khi K vào chuỗi; K đã nhận 2 tin trong 7 ngày qua | Tới hạn bước 5 | Bước 5 được hoãn như chuỗi không miễn trừ (quá khoảng thời gian miễn trừ 14 ngày) |
| `AC-39.4.6` | Chuỗi được duyệt miễn trừ tần suất và cho vào lại sau 15 ngày; K vào lại chuỗi | Bước 1 của lần vào lại tới hạn; K đã nhận 2 tin trong 7 ngày qua | Bước 1 được hoãn như chuỗi không miễn trừ |
| `AC-39.4.7` | Hạn ngạch email 20.000/ngày, `CFG-CAMP-55` = 90%; các chiến dịch đã giữ 18.000 và 2.000 còn lại đã dùng hết cho các chuỗi khác | 300 bước email của chuỗi chào mừng bị hoãn quá `CFG-CAMP-61` (24 giờ) | Người phụ trách chuỗi nhận thông báo kèm số bước đang hoãn và tên các chiến dịch đang giữ chỗ |
| `AC-39.4.8` | Tiếp nối AC-39.4.7; các bước vẫn bị hoãn vì hạn ngạch trong 3 ngày tiếp theo | Hệ thống xử lý | Người phụ trách chuỗi nhận đúng một thông báo mỗi ngày; hết bước bị hoãn thì ngừng thông báo |
| `AC-39.5.1` | K đang chờ B3; chuỗi được sửa nội dung B3 và phê duyệt | Tới B3 của K | K nhận nội dung B3 mới |
| `AC-39.5.2` | Chuỗi Đang chạy; phiên bản mới đang chờ duyệt | Tới bước của một người | Người đó nhận nội dung của phiên bản đang chạy |
| `AC-39.5.3` | Chuỗi Tạm dừng 3 ngày; 2.000 người có bước tới hạn trong thời gian đó | Tiếp tục | 2.000 bước được gửi theo nhịp, không cùng lúc |
| `AC-39.5.5` | Chuỗi Đang chạy; A vừa là người chọn đối tượng vừa là người khởi chạy; A bị tạm ngưng 3 ngày, 50 khách khớp điều kiện vào trong thời gian đó; A được kích hoạt lại và tiếp tục chuỗi | Hệ thống xử lý | Chuỗi Tạm dừng trong 3 ngày; sau khi tiếp tục, 50 khách không được ghi danh bù; khách khớp sau thời điểm tiếp tục vào chuỗi bình thường |
| `AC-39.5.6` | Chuỗi bị người dùng tạm dừng 2 ngày, 30 khách khớp điều kiện vào trong thời gian đó | Tiếp tục chuỗi | 30 khách được ghi danh bù, bước tính từ thời điểm tiếp tục |
| `AC-39.5.4` | Chuỗi chào mừng tạm dừng 2 ngày; 300 khách đăng ký trong 2 ngày đó | Tiếp tục chuỗi | 300 khách được ghi danh bù, bước đầu tiên tính từ lúc tiếp tục |
| `AC-39.6.1` | Chuỗi 3 bước đang chạy | Mở báo cáo chuỗi | Thấy số người tới từng bước, phân phát, tương tác, bỏ qua theo lý do, số người đang ở từng bước và thoát theo lý do |

---

#### FEAT-40 — Hạn ngạch Gửi Hằng ngày

**Mô tả nghiệp vụ:** Doanh nghiệp tự đặt trần số tin tiếp thị gửi mỗi ngày để kiểm soát chi phí và rủi ro, **độc lập** với hạn mức của gói dịch vụ.

**Quy tắc nghiệp vụ:**

- **`BR-40.1` (Phạm vi của hạn ngạch):** Hạn ngạch ngày `CFG-CAMP-09` tính trên tổng số tin **chuyển nhà cung cấp** từ mọi chiến dịch và chuỗi nuôi dưỡng trong một ngày theo múi giờ Không gian làm việc (Mục 2.3), đặt được riêng cho từng kênh. Hạn ngạch **không** áp cho Thông báo dịch vụ (`FEAT-45`), cho thông điệp nhóm Giao dịch & Dịch vụ và Liên lạc 1-1 phát ra từ phân hệ khác, và không áp cho gửi thử. Hạn ngạch ngày không thay thế hạn mức gói dịch vụ; cả hai cùng được đối chiếu.

  **Lý do nghiệp vụ:** Hạn ngạch tồn tại để tiếp thị không tiêu hết ngân sách gửi tin; nếu nó chặn cả tin xác nhận đơn hàng hay phản hồi vé hỗ trợ, một chiến dịch lớn buổi sáng sẽ làm tê liệt dịch vụ khách hàng buổi chiều.

- **`BR-40.2` (Cách xử lý khi vượt hạn ngạch là một phần của phiên bản duyệt):** Khi soạn, người soạn chọn cách xử lý nếu khối lượng vượt phần hạn ngạch còn lại của ngày (mặc định theo `CFG-CAMP-40`): **(a) Dàn trải** — mỗi ngày gửi trong phần hạn ngạch còn lại cho tới khi xong, màn hình duyệt hiển thị ngày dự kiến hoàn tất; hoặc **(b) Chờ** — không bắt đầu cho tới khi đủ hạn ngạch; chiến dịch chọn Chờ ở Giữ lại **tự bắt đầu** ngay khi hạn ngạch đủ (ví dụ sang ngày mới) nếu vẫn còn trong thời gian ân hạn `CFG-CAMP-32` và trước hạn chót gửi, sau khi chạy lại toàn bộ chuỗi kiểm tra — lựa chọn này chính là một cho phép tự phát sóng đã được duyệt trước. Tại bước đối chiếu trước khi bắt đầu (`BR-17.8`), cách xử lý đã duyệt được áp dụng tự động, kể cả với chiến dịch hẹn giờ không có người thao tác; với (b), chiến dịch chuyển Giữ lại (`BR-18.3`) hoặc giữ nguyên Đã duyệt (phát sóng ngay). Việc đề nghị Quản trị viên nâng hạn ngạch là thao tác ngoài chiến dịch. Hệ thống không bao giờ tự cắt bớt người nhận. Chọn "Chờ" trong khi phần khối lượng của một ngày gửi dự kiến (tính theo `BR-40.4`) vượt trần thực của một chiến dịch (`BR-40.4`) là lỗi chặn nhóm (b) (`BR-16.1`), vì chiến dịch sẽ không bao giờ đủ điều kiện bắt đầu nếu không nâng hạn ngạch, nâng `CFG-CAMP-54` hoặc `CFG-CAMP-55`, hoặc đổi sang Dàn trải. Nếu phiên bản khai báo mức tăng quy mô chấp nhận được (`BR-06.4`) mà quy mô tối đa theo mức đó sẽ vượt trần này, màn hình duyệt hiển thị cảnh báo. Nếu theo phần giữ chỗ hiện có, phần hạn ngạch còn trống tại giờ hẹn không đủ cho chiến dịch "Chờ", danh mục kiểm tra và hộp xác nhận phát sóng hiển thị cảnh báo nêu tên các chiến dịch đang chiếm hạn ngạch những ngày đó, để người phụ trách chọn tạm dừng chiến dịch kia, dời giờ hẹn hoặc đề nghị nâng hạn ngạch.

  **Lý do nghiệp vụ:** Chiến dịch hẹn giờ nửa đêm không có ai để chọn cách xử lý; quyết định phải được đưa ra và duyệt từ trước, như mọi phần khác của phiên bản.

- **`BR-40.3` (Hạn ngạch hết giữa chừng):** Nhờ giữ chỗ (`BR-40.4`), lượt gửi chính của một chiến dịch chỉ hết hạn ngạch giữa chừng khi người có quyền quản trị **Quản lý hạn ngạch và chi phí gửi** hạ hạn ngạch, `CFG-CAMP-54` hoặc `CFG-CAMP-55` (xem `BR-40.4`); các lượt ngoài dự kiến (gửi lại, dự phòng) chỉ dùng phần chưa ai giữ chỗ. Nếu hạn ngạch ngày hết trong khi chiến dịch đang gửi, chiến dịch có chọn dàn trải thì tự dừng gửi tới hết ngày và tự gửi tiếp từ đầu ngày hôm sau (đây là nhịp gửi đã được duyệt, không phải tạm dừng bảo vệ) — trước khi gửi tiếp, hệ thống chạy lại bước 2–4 của `BR-17.8` trừ chênh lệch đối tượng (danh sách đã chốt, như `BR-19.2`) và trừ điều kiện ngày không gửi (ngày không gửi chỉ làm hoãn tin, `BR-43.1`), không đạt thì chiến dịch bị tự động tạm dừng bảo vệ; chiến dịch không chọn dàn trải thì bị tự động tạm dừng bảo vệ (`BR-20.1`). Hết hạn ngạch của kênh dự phòng, hoặc hết hạn ngạch chỉ ảnh hưởng tới các lượt dự phòng hay gửi lại, **không bao giờ** làm dừng chiến dịch: các lượt đó được hoãn sang ngày kế tiếp (`BR-40.4`), vẫn chịu hạn chót gửi, trong khi kênh chính tiếp tục (như `BR-37.6`).

  **Lý do nghiệp vụ:** Qua đêm, tài khoản gửi có thể bị ngắt, ngân sách có thể đã hết hay Không gian làm việc bị đình chỉ; tự gửi tiếp mà không kiểm tra lại là bỏ qua đúng các điều kiện đã được kiểm tra hôm trước.

- **`BR-40.4` (Giữ chỗ hạn ngạch):** Khi một chiến dịch không dàn trải (cách xử lý "Chờ") bắt đầu gửi, hệ thống **giữ chỗ** trong hạn ngạch ngày cho toàn bộ khối lượng của lô, phân theo từng ngày gửi dự kiến; phần giữ chỗ không dùng hết được trả lại khi chiến dịch hoàn tất hoặc bị hủy. Chiến dịch dàn trải, ngay khi bắt đầu gửi, cũng giữ chỗ phần khối lượng dự kiến của **từng ngày** tới khi hoàn tất. Giữ chỗ được xác lập **theo thứ tự bắt đầu gửi** và **không bao giờ bị lấy lại** bởi chiến dịch bắt đầu sau: tổng các phần giữ chỗ của mọi chiến dịch trong một ngày không vượt `CFG-CAMP-55` hạn ngạch ngày — phần còn lại luôn chưa ai giữ, để chuỗi nuôi dưỡng, lượt gửi lại và lượt dự phòng không bị đói hạn ngạch khi có bản tin lớn (nếu `CFG-CAMP-55` thấp hơn `CFG-CAMP-54`, trần của từng chiến dịch là `CFG-CAMP-55`) — và chiến dịch bắt đầu sau (dàn trải hay "Chờ") chỉ được phần còn lại — ngày hoàn tất dự kiến của nó được tính theo phần đó. Với **mọi** chiến dịch (Dàn trải hay Chờ), cả phần giữ chỗ lẫn lượng gửi thực trong một ngày không vượt `CFG-CAMP-54` hạn ngạch ngày của kênh — chiến dịch không lấy thêm phần ngoài trần, kể cả khi cuối ngày chưa ai dùng, để ngày hoàn tất hiển thị luôn dự đoán được. **Phần giữ chỗ chỉ dành cho lượt gửi chính của chính chiến dịch giữ nó.** Chuỗi nuôi dưỡng, lượt gửi lại và lượt dự phòng (của bất kỳ chiến dịch nào) chỉ dùng phần hạn ngạch chưa ai giữ chỗ, và lượt dự phòng, gửi lại của một chiến dịch cũng tính vào trần của chiến dịch đó; khi không còn phần chưa ai giữ chỗ hoặc đã chạm trần, các lượt này được hoãn sang ngày kế tiếp (vẫn chịu hạn chót gửi, `BR-18.4`) và không bao giờ làm chiến dịch khác phải dừng. Giữ chỗ được tính theo **ngày gửi dự kiến thực tế** (tính cả nhịp gửi, khung giờ yên lặng, ngày không gửi): chiến dịch "Chờ" mà khối lượng sẽ tràn sang ngày sau được giữ chỗ ở cả những ngày đó và chỉ bắt đầu khi đủ phần giữ chỗ cho mọi ngày dự kiến; màn hình duyệt hiển thị số ngày dự kiến và phần hạn ngạch chiến dịch chiếm mỗi ngày. Hai chiến dịch cùng đối chiếu tại một thời điểm được xét lần lượt theo thời điểm được duyệt (sớm hơn trước), chiến dịch sau chỉ thấy phần còn lại sau khi trừ phần đã giữ chỗ.
  - **Phần còn có thể giữ chỗ của một ngày** = giá trị nhỏ hơn giữa (`CFG-CAMP-55` × hạn ngạch − tổng phần đã giữ chỗ) và (hạn ngạch − tổng phần đã giữ chỗ − lượng đã gửi trong ngày bởi các lượt không giữ chỗ: chuỗi nuôi dưỡng, gửi lại, dự phòng). Trần thực của một chiến dịch là giá trị nhỏ hơn giữa `CFG-CAMP-54` và `CFG-CAMP-55`, nhân hạn ngạch ngày.
  - **Tạm dừng, tiếp tục, hủy:** khi chiến dịch chuyển Tạm dừng (do người dùng, tự động tạm dừng bảo vệ hay dừng khẩn cấp), phần giữ chỗ từ thời điểm dừng trở đi được **trả lại** ngay; hộp xác nhận tạm dừng nêu số tin mỗi ngày sẽ được trả lại và rằng khi tiếp tục chiến dịch có thể hoàn tất muộn hơn — với chiến dịch "Chờ", rằng chiến dịch có thể không tiếp tục được nếu chỗ bị chiến dịch khác giữ trong lúc dừng. Khi tiếp tục, chiến dịch giữ chỗ lại cho khối lượng còn lại theo nhịp gửi hiện hành (kể cả nhịp đã giảm trong lúc tạm dừng, `BR-19.3`), xếp như một chiến dịch vừa bắt đầu gửi (chỉ được phần còn trống, không giữ thứ tự ưu tiên cũ), và ngày hoàn tất dự kiến được tính lại; với chiến dịch "Chờ", tiếp tục chỉ thực hiện được khi đủ chỗ cho mọi ngày dự kiến (`BR-19.2`). Hủy và hoàn tất trả lại toàn bộ phần giữ chỗ còn lại của mọi chiến dịch, dàn trải hay "Chờ".
  - **Hạ hạn ngạch, `CFG-CAMP-54` hoặc `CFG-CAMP-55`:** trước khi Quản trị viên xác nhận, hệ thống hiển thị danh sách mọi chiến dịch sẽ bị cắt và mức cắt ở từng ngày. Trước hết, phần giữ chỗ của từng chiến dịch vượt trần thực mới của một chiến dịch (giá trị nhỏ hơn giữa `CFG-CAMP-54` và `CFG-CAMP-55` mới, nhân hạn ngạch mới) bị cắt về đúng trần đó; sau đó, nếu tổng phần giữ chỗ của một ngày (hôm nay hay tương lai) vẫn vượt mức tổng mới — giá trị nhỏ hơn giữa `CFG-CAMP-55` × hạn ngạch mới và (hạn ngạch mới − lượng đã gửi trong ngày bởi các lượt không giữ chỗ), như công thức phần còn có thể giữ chỗ ở trên — phần giữ chỗ bị cắt theo **thứ tự ngược với thứ tự bắt đầu** (chiến dịch bắt đầu sau bị cắt trước) cho tới khi vừa mức mới. Chiến dịch dàn trải bị cắt được tính lại ngày hoàn tất dự kiến; chiến dịch "Chờ" bị cắt mà thiếu chỗ ở ngày đang gửi thì xử lý như hết hạn ngạch giữa chừng (`BR-40.3`); bị cắt ở một ngày tương lai thì người phụ trách được báo ngay, và tới ngày đó chiến dịch chỉ gửi trong phần còn giữ chỗ — phần thiếu xử lý như hết hạn ngạch giữa chừng. Người phụ trách mọi chiến dịch bị ảnh hưởng được thông báo.
  - **Chiến dịch khẩn cấp:** giữ chỗ đã xác lập không bị lấy lại; đường xử lý chính thức khi một chiến dịch "Chờ" khẩn cấp không đủ chỗ là tạm dừng chiến dịch đang chiếm chỗ (trả chỗ theo trên) hoặc đề nghị Quản trị viên nâng hạn ngạch ngày. Phần hạn ngạch vừa được nâng hoặc vừa được trả lại là phần còn trống, được xét trước hết cho chiến dịch "Chờ" đang Giữ lại (đã có cho phép tự phát sóng theo `BR-40.2`), theo thứ tự vào Giữ lại; chiến dịch "Chờ" đang Tạm dừng chỉ được thông báo theo `BR-19.2`; chiến dịch dàn trải đang gửi không tự lấy thêm phần đó.

  **Lý do nghiệp vụ:** Không giữ chỗ thì hai chiến dịch cùng qua đối chiếu lúc 00:00 và một trong hai dừng lại khi mới gửi nửa tệp — đúng điều mà việc đối chiếu cả lô trước khi bắt đầu muốn tránh. Chiến dịch đang tạm dừng không gửi tin nào nên không được chiếm chỗ; nếu không trả lại, một chiến dịch bị tự động tạm dừng bảo vệ sẽ khóa phần lớn hạn ngạch của cả doanh nghiệp trong nhiều ngày.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-40.1.1` | Hạn ngạch SMS ngày đã hết | Phân hệ Vé hỗ trợ gửi SMS xác nhận cho khách | SMS xác nhận vẫn được gửi |
| `AC-40.2.1` | `CFG-CAMP-09` = 20.000 tin/ngày, `CFG-CAMP-54` = 70%, hôm nay chưa gửi tin nào; chiến dịch 50.000 người, cách xử lý đã duyệt là Dàn trải | Phát sóng ngay | Chiến dịch Đang gửi; mỗi ngày gửi tối đa 14.000; màn hình hiển thị dự kiến hoàn tất vào ngày thứ 4 (14.000 + 14.000 + 14.000 + 8.000) |
| `AC-40.2.2` | `CFG-CAMP-09` = 20.000 tin/ngày, hôm nay đã gửi 15.000; chiến dịch 10.000 người, cách xử lý đã duyệt là Chờ, hẹn 09:00 | Tới 09:00 | Chiến dịch Giữ lại, lý do phần hạn ngạch còn lại của ngày không đủ; phê duyệt còn nguyên |
| `AC-40.2.3` | Màn hình soạn | Mở phần Cấu hình gửi | Có lựa chọn cách xử lý khi vượt hạn ngạch, mặc định theo `CFG-CAMP-40` |
| `AC-40.2.4` | `CFG-CAMP-09` = 20.000 tin/ngày, `CFG-CAMP-54` = 70%; chiến dịch Email 50.000 người, cách xử lý "Chờ", hẹn 08:00, nhịp gửi không giới hạn (cả lô dự kiến gửi trong ngày) | Mở danh mục kiểm tra | Lỗi chặn: khối lượng của một ngày gửi dự kiến vượt trần, phải chọn Dàn trải, nâng hạn ngạch hoặc nâng `CFG-CAMP-54` / `CFG-CAMP-55` |
| `AC-40.3.1` | Chiến dịch "Chờ" đang gửi, bắt đầu sau cùng nên bị cắt giữ chỗ trước (`BR-40.4`); Quản trị viên hạ hạn ngạch ngày của kênh chính | Hạn ngạch ngày của kênh chính hết | Chiến dịch Tạm dừng, lý do "Hết hạn ngạch ngày" |
| `AC-40.3.2` | Chiến dịch Email có dàn trải, Email thuộc `CFG-CAMP-07`; hạn ngạch hết lúc 15:00 | Sang ngày mới theo múi giờ Không gian làm việc | Chiến dịch tự gửi tiếp từ khi hết khung giờ yên lặng của ngày mới, không cần người bấm tiếp tục |
| `AC-40.3.3` | Tiếp nối AC-40.3.2; trong đêm tài khoản gửi bị ngắt kết nối | Tới lúc gửi tiếp | Chiến dịch không gửi tiếp mà Tạm dừng, lý do tài khoản gửi ngừng hoạt động |
| `AC-40.3.4` | Chiến dịch Email "Chờ" có dự phòng SMS; hạn ngạch SMS hôm nay đã hết do chiến dịch khác | 300 người cần dự phòng SMS | Kênh email tiếp tục gửi; 300 tin dự phòng hoãn sang ngày kế tiếp, vẫn bị loại nếu quá hạn chót gửi; chiến dịch không tạm dừng |
| `AC-40.4.1` | Hạn ngạch 20.000 tin/ngày, `CFG-CAMP-54` = 70%, `CFG-CAMP-55` = 90%; lúc 00:00 hai chiến dịch "Chờ", mỗi chiến dịch 12.000 người, cùng tới giờ | Hệ thống đối chiếu | Chiến dịch được duyệt trước giữ chỗ 12.000 và gửi hết; chiến dịch còn lại chỉ còn 6.000 có thể giữ chỗ (18.000 − 12.000), không bắt đầu, chuyển Giữ lại chờ hạn ngạch ngày hôm sau |
| `AC-40.4.2` | Hạn ngạch 50.000 tin/ngày, `CFG-CAMP-54` = 70%, `CFG-CAMP-55` = 90%; bản tin 300.000 người dàn trải, không có chiến dịch nào khác | Bắt đầu gửi | Bản tin giữ chỗ 35.000 tin mỗi ngày trong 8 ngày đầu và 20.000 ở ngày thứ 9 (8 × 35.000 + 20.000); chiến dịch khác còn giữ chỗ được tối đa 10.000 mỗi ngày trong 8 ngày đầu (45.000 − 35.000) và 25.000 ở ngày thứ 9 (45.000 − 20.000); 5.000 mỗi ngày luôn chưa ai giữ cho chuỗi nuôi dưỡng, lượt gửi lại, lượt dự phòng |
| `AC-40.4.3` | Chiến dịch SMS "Chờ" 14.000 người bắt đầu 21:00, nhịp 5.000 tin/giờ, khung giờ yên lặng 22:00–08:00; hạn ngạch 20.000/ngày, `CFG-CAMP-54` = 70% | Hệ thống đối chiếu | Giữ chỗ 5.000 cho hôm nay và 9.000 cho ngày mai; chiến dịch chỉ bắt đầu nếu cả hai ngày đủ phần giữ chỗ |
| `AC-40.4.4` | Hạn ngạch 20.000/ngày, `CFG-CAMP-54` = 70%, `CFG-CAMP-55` = 90%; bản tin dàn trải A đang gửi, giữ chỗ 14.000 mỗi ngày trong 5 ngày tới | Soạn chiến dịch "Chờ" B 10.000 người hẹn ngày mai | Cảnh báo phần còn có thể giữ chỗ ngày mai là 4.000 (18.000 − 14.000), không đủ cho B, nêu tên A; bản tin dàn trải C bắt đầu sau A chỉ được tối đa 4.000 mỗi ngày; 2.000 mỗi ngày luôn chưa ai giữ |
| `AC-40.4.5` | Tiếp nối AC-40.4.3: chiến dịch "Chờ" X đã giữ chỗ 9.000 cho ngày mai; 23:00 bản tin SMS dàn trải D 100.000 người bắt đầu (hôm nay đang trong khung giờ yên lặng nên D không có ngày gửi hôm nay) | Hệ thống giữ chỗ cho D | Ngày mai X giữ nguyên 9.000 và không bị dừng; với `CFG-CAMP-55` = 90%, D chỉ được 9.000 cho ngày mai (18.000 − 9.000), từ ngày kia 14.000 mỗi ngày |
| `AC-40.4.6` | Hạn ngạch 20.000/ngày, `CFG-CAMP-54` = 70%, `CFG-CAMP-55` = 90%; bản tin dàn trải A giữ 14.000 mỗi ngày trong 10 ngày, hôm nay A chưa gửi tin nào; chiến dịch "Chờ" khẩn cấp B 12.000 người đang Giữ lại vì chỉ còn 4.000 có thể giữ chỗ | Quản lý Marketing tạm dừng A | Hộp xác nhận nêu A sẽ trả lại 14.000 tin/ngày; A tạm dừng, chỗ được trả lại; B tự bắt đầu (có 18.000 có thể giữ chỗ, B dùng 12.000 ≤ 14.000) |
| `AC-40.4.7` | Tiếp nối AC-40.4.6; B đã giữ 12.000 cho hôm nay | Quản lý Marketing tiếp tục A | A giữ chỗ lại cho khối lượng còn lại: hôm nay 6.000 (18.000 − 12.000), từ ngày mai 14.000 mỗi ngày; ngày hoàn tất dự kiến của A được tính lại và hiển thị |
| `AC-40.4.8` | `CFG-CAMP-55` = 90%, hôm nay chưa có lượt gửi nào ngoài giữ chỗ; chiến dịch "Chờ" C đang Giữ lại vì thiếu 5.000 chỗ; một chiến dịch dàn trải D đang gửi và chưa dùng hết trần 70% | Quản trị viên nâng hạn ngạch ngày thêm 10.000 | Phần có thể giữ chỗ tăng 9.000 (90% × 10.000); C được xét trước và tự bắt đầu; D không tự lấy thêm phần vừa nâng |
| `AC-40.4.9` | Hạn ngạch 20.000/ngày, `CFG-CAMP-54` = 100%, `CFG-CAMP-55` = 100%; ngày mai A (bắt đầu trước) giữ 14.000, B (bắt đầu sau, dàn trải) giữ 6.000 | Quản trị viên hạ hạn ngạch xuống 16.000 | Trước khi xác nhận, hệ thống liệt kê B bị ảnh hưởng; sau khi xác nhận, B còn 2.000 cho ngày mai và được tính lại ngày hoàn tất; A giữ nguyên 14.000; người phụ trách B được thông báo |
| `AC-40.4.10` | Hạn ngạch 20.000/ngày, `CFG-CAMP-54` = 70%, `CFG-CAMP-55` = 90%; ngày mai bản tin dàn trải A giữ 14.000 | Quản trị viên hạ hạn ngạch xuống 16.000 | Trước khi xác nhận, danh sách xem trước nêu A bị cắt 2.800 ngày mai; sau khi xác nhận, A bị cắt về 11.200 (70% × 16.000) và được tính lại ngày hoàn tất; người phụ trách A được thông báo |
| `AC-40.4.11` | Hạn ngạch 20.000/ngày, `CFG-CAMP-54` = 70%, `CFG-CAMP-55` = 90%; chưa ai giữ chỗ hôm nay, các chuỗi nuôi dưỡng đã gửi 10.000 tin | Chiến dịch "Chờ" 12.000 người bắt đầu | Không bắt đầu: phần còn có thể giữ chỗ là 10.000 (20.000 − 0 − 10.000, nhỏ hơn 18.000); chiến dịch Giữ lại |

---

#### FEAT-41 — Ngân sách & Chi phí Chiến dịch

**Mô tả nghiệp vụ:** Ước tính chi phí trước khi phát sóng, theo dõi chi phí thực tế, và dừng chiến dịch khi chạm ngân sách đã đặt.

**Quy tắc nghiệp vụ:**

- **`BR-41.1` (Đơn giá):** Chi phí được tính theo bảng đơn giá do người có quyền Quản lý hạn ngạch và chi phí gửi khai báo **theo kênh × nhà mạng hoặc nền tảng × loại tin** (ví dụ SMS quảng cáo theo từng nhà mạng; WhatsApp và Zalo ZNS theo loại mẫu), với căn cứ tính phí theo thuộc tính kênh (`BR-09.1`: tính trên tin chuyển đi hay tin phân phát), bằng đồng tiền cơ sở của Không gian làm việc (`deals-pipeline-srs.md`, `CFG-DEAL-08`). Đây là **chi phí quản trị nội bộ** cho mục đích tiếp thị; số tiền doanh nghiệp thực trả nền tảng do phân hệ Tính phí quyết định (`billing-subscription-srs.md`).

  **Lý do nghiệp vụ:** Giá gửi tin khác nhau theo nhà mạng và loại tin; một đơn giá chung cho cả kênh làm ước tính lệch xa hóa đơn thực tế của nhà cung cấp.

- **`BR-41.2` (Ngân sách chiến dịch):** Người có quyền Phát sóng chiến dịch được đặt ngân sách cho chiến dịch (không bắt buộc). Hệ thống theo dõi **chi phí đã cam kết** = chi phí của mọi tin đã chuyển nhà cung cấp (tính theo đơn giá, kể cả khi kênh tính phí theo tin phân phát mà chưa có báo phân phát) + chi phí tối đa của các lượt dự phòng và gửi lại đã xếp lịch; không gồm gửi thử. Chi phí đã cam kết chạm ngân sách thì chiến dịch bị tự động tạm dừng bảo vệ (`BR-20.1`) và các lượt đã xếp lịch không được gửi. Chi phí ước tính của phần chưa gửi vượt ngân sách còn lại thì chiến dịch không bắt đầu (`BR-17.8` bước 4) — không phải cảnh báo. Chi phí thực tế (theo báo phân phát) hiển thị riêng để đối soát. Tăng ngân sách là thay đổi được ghi nhật ký, không cần phê duyệt lại nội dung.

  **Lý do nghiệp vụ:** Ngân sách là giới hạn chi tiêu, không phải một phần của thông điệp; bắt duyệt lại nội dung chỉ vì nâng ngân sách là thủ tục không bảo vệ điều gì.

- **`BR-41.3` (Ước tính trước khi phát sóng):** Màn hình duyệt hiển thị chi phí ước tính = số người nhận khả dụng × đơn giá (với SMS, nhân thêm số đơn vị tin ước tính theo `BR-11.3`), kèm **chi phí dự phòng tối đa** nếu có cấu hình dự phòng. Chi phí đã cam kết của một chiến dịch không được vượt ngân sách quá **chi phí của các tin đã chuyển nhà cung cấp trong thời gian tạm dừng có hiệu lực** (`BR-19.1`).

  **Lý do nghiệp vụ:** Người duyệt quyết định dựa trên chi phí; không thấy chi phí dự phòng tối đa thì một chiến dịch Zalo ZNS rẻ có thể thành một chiến dịch SMS đắt.

- **`BR-41.4` (Đối soát với nhà cung cấp):** Hệ thống có báo cáo đối soát theo chiến dịch, theo nhà cung cấp/nhà mạng và theo tháng dương lịch: số tin theo căn cứ tính phí, đơn giá áp dụng, chi phí ước tính; xuất được thành tệp để đối chiếu với hóa đơn của nhà cung cấp.

  **Lý do nghiệp vụ:** Không đối soát được thì doanh nghiệp không phát hiện được nhà cung cấp tính sai, và không chứng minh được chi phí tiếp thị với bộ phận tài chính.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-41.1.1` | Đơn giá SMS 800 đồng/đơn vị tin; chiến dịch 10.000 người, mỗi tin 2 đơn vị | Mở màn hình duyệt | Chi phí ước tính 16.000.000 đồng |
| `AC-41.1.2` | Đơn giá SMS khác nhau cho ba nhà mạng | Mở màn hình duyệt chiến dịch SMS | Chi phí ước tính cộng theo số người nhận của từng nhà mạng × đơn giá tương ứng |
| `AC-41.2.1` | Ngân sách 10.000.000 đồng; chiến dịch WhatsApp; chi phí đã cam kết (gồm tin đã chuyển đi chưa có báo phân phát) chạm 10.000.000 | Hệ thống xử lý | Chiến dịch Tạm dừng, lý do "Chạm ngân sách"; các lượt dự phòng đã xếp lịch không được gửi |
| `AC-41.2.2` | Tiếp nối AC-41.2.1 | Quản lý tăng ngân sách lên 20.000.000 rồi tiếp tục | Không cần duyệt lại nội dung; nhật ký ghi thay đổi ngân sách |
| `AC-41.3.1` | Chiến dịch có dự phòng SMS | Mở màn hình duyệt | Hiển thị riêng chi phí kênh chính và chi phí dự phòng tối đa |
| `AC-41.3.2` | Ngân sách 10.000.000 đồng; chạm ngân sách khi đang gửi; trong thời gian tạm dừng có hiệu lực có thêm 200 tin được chuyển đi | Xem chi phí đã cam kết cuối cùng | Chi phí đã cam kết vượt ngân sách không quá chi phí của 200 tin đó |
| `AC-41.3.3` | Chiến dịch có dự phòng SMS, ngân sách còn lại đủ cho kênh chính nhưng không đủ cho chi phí dự phòng tối đa | Bấm Phát sóng ngay | Hộp xác nhận hiển thị chi phí kênh chính, chi phí dự phòng tối đa và lưu ý chiến dịch có thể tự tạm dừng khi dự phòng chạm ngân sách; phát sóng được sau khi xác nhận |
| `AC-41.4.1` | Chiến dịch SMS đã hoàn tất | Xuất báo cáo đối soát tháng | Tệp có số tin theo nhà mạng, đơn giá, chi phí, mã chiến dịch |

---

### Nhóm L — Kiểm soát Toàn Không gian làm việc

#### FEAT-42 — Dừng Khẩn cấp Toàn bộ Hoạt động Gửi Tiếp thị

**Mô tả nghiệp vụ:** Một thao tác duy nhất dừng ngay mọi hoạt động gửi tiếp thị của Không gian làm việc — khi có sự kiện quốc gia, khủng hoảng truyền thông, sự cố bảo mật hay phát hiện một lỗi nghiêm trọng dùng chung (ví dụ trang đích của mọi chiến dịch bị lỗi).

**Quy tắc nghiệp vụ:**

- **`BR-42.1` (Phạm vi dừng):** Khi dừng khẩn cấp được kích hoạt: mọi chiến dịch Đang gửi chuyển Tạm dừng với lý do "Dừng khẩn cấp"; mọi chuỗi nuôi dưỡng chưa Kết thúc (Đang chạy, Ngừng nhận người mới) chuyển Tạm dừng; mọi lượt gửi lại chuyển tạm dừng; mọi chiến dịch Đã duyệt tới giờ hẹn trong thời gian dừng chuyển Giữ lại; không phát sóng được chiến dịch nào. Hiệu lực trong cùng thời hạn của `KPI-06`. Dừng khẩn cấp **không** ảnh hưởng thông điệp nhóm Giao dịch & Dịch vụ — kể cả Thông báo dịch vụ của chính phân hệ này (`FEAT-45`) — và Liên lạc 1-1 của các phân hệ khác. Mọi phần giữ chỗ hạn ngạch được trả lại (`BR-40.4`); khi tiếp tục từng chiến dịch sau khi gỡ, thứ tự giữ chỗ xếp lại theo thứ tự tiếp tục — màn hình gỡ dừng khẩn cấp nêu rõ điều này.

  **Lý do nghiệp vụ:** Khi có quốc tang hay khủng hoảng, việc tạm dừng từng chiến dịch một mất quá nhiều thời gian và dễ bỏ sót chuỗi nuôi dưỡng đang chạy ngầm; trong khi đó mỗi tin quảng cáo gửi đi là một rủi ro thương hiệu.

- **`BR-42.2` (Ai được kích hoạt và gỡ):** Kích hoạt: Người có toàn quyền, hoặc bất kỳ người nào có ô (Chiến dịch, Phát sóng) khác Không có, không phụ thuộc tên vai trò; bắt buộc nhập lý do. Kích hoạt là thao tác dừng, không bị chặn khi nhật ký gặp sự cố (Nguyên tắc 7). Gỡ: người có quyền quản trị **Gỡ dừng khẩn cấp gửi tiếp thị** (Mục 5, ghi chú 2; mặc định chỉ Người có toàn quyền), và chỉ thực hiện được khi ghi được nhật ký. Gỡ dừng khẩn cấp **không** tự tiếp tục chiến dịch, chuỗi hay lượt gửi lại nào; mỗi chiến dịch và chuỗi phải được tiếp tục theo `BR-20.2` — người có quyền được **tiếp tục hàng loạt** các chiến dịch và chuỗi đã tạm dừng vì dừng khẩn cấp từ một danh sách, nhập một lý do chung; mỗi mục vẫn chạy lại chuỗi kiểm tra riêng của mình và được ghi nhật ký riêng. Kích hoạt và gỡ được ghi nhật ký và thông báo tới mọi người có quyền Phát sóng chiến dịch.

  **Lý do nghiệp vụ:** Người phát hiện sự cố phải dừng được ngay, nên quyền kích hoạt rộng hơn quyền gỡ. Không tự tiếp tục sau khi gỡ vì bối cảnh đã thay đổi — nội dung đang gửi dở có thể không còn phù hợp.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-42.1.1` | 3 chiến dịch Đang gửi, 1 chuỗi Đang chạy, 1 chuỗi Ngừng nhận người mới, 1 lượt gửi lại đang chạy, 1 chiến dịch hẹn giờ 30 phút sau | M kích hoạt dừng khẩn cấp với lý do | Trong vòng 1 phút không tin tiếp thị mới nào được chuyển đi; 3 chiến dịch, 2 chuỗi và lượt gửi lại đều tạm dừng; tới giờ hẹn chiến dịch hẹn giờ chuyển Giữ lại |
| `AC-42.1.2` | Đang dừng khẩn cấp | Phân hệ Vé hỗ trợ gửi thư phản hồi cho khách | Thư vẫn được gửi |
| `AC-42.2.1` | Đang dừng khẩn cấp; M có (Chiến dịch, Phát sóng) = Đơn vị của mình, không có quyền quản trị Gỡ dừng khẩn cấp gửi tiếp thị | M tìm thao tác gỡ | Không có; chỉ người có quyền Gỡ dừng khẩn cấp gửi tiếp thị gỡ được |
| `AC-42.2.4` | Nhân viên Marketing được doanh nghiệp đặt (Chiến dịch, Phát sóng) = Chỉ của mình | Kích hoạt dừng khẩn cấp với lý do | Dừng khẩn cấp có hiệu lực trên toàn Không gian làm việc |
| `AC-42.2.5` | Nhật ký kiểm toán đang gặp sự cố | M kích hoạt dừng khẩn cấp; sau đó Quản trị viên thử gỡ | Kích hoạt có hiệu lực ngay, sự kiện được ghi bù khi nhật ký phục hồi; thao tác gỡ bị từ chối kèm thông báo chưa ghi được nhật ký |
| `AC-42.2.2` | Quản trị viên gỡ dừng khẩn cấp | Chờ | Không chiến dịch hay chuỗi nào tự tiếp tục; mọi người có quyền Phát sóng chiến dịch nhận thông báo |
| `AC-42.2.3` | Sau khi gỡ dừng khẩn cấp, còn 12 chiến dịch và 5 chuỗi đang tạm dừng vì dừng khẩn cấp | Quản lý Marketing chọn tất cả và tiếp tục hàng loạt, nhập lý do | Mọi mục đạt kiểm tra được tiếp tục; mục không đạt vẫn tạm dừng kèm lý do; nhật ký có từng mục |

---

#### FEAT-43 — Lịch Ngày Không Gửi

**Mô tả nghiệp vụ:** Người có quyền quản trị **Quản lý lịch ngày không gửi** khai báo trước những ngày hoặc khoảng thời gian doanh nghiệp không gửi tin tiếp thị (quốc tang đã công bố, ngày lễ mà doanh nghiệp chọn không quảng cáo, thời gian chuyển đổi hệ thống).

**Quy tắc nghiệp vụ:**

- **`BR-43.1` (Hiệu lực của ngày không gửi):** Ngày không gửi được khai báo theo ngày của Không gian làm việc (Mục 2.3) hoặc theo một khoảng thời gian cụ thể, kèm lý do. Trong ngày không gửi: chiến dịch có giờ hẹn rơi vào ngày đó chuyển **Giữ lại** khi tới giờ; tin của chiến dịch Đang gửi và các bước của chuỗi nuôi dưỡng được **hoãn** tới khi hết ngày không gửi (và hết khung giờ yên lặng nếu có) rồi gửi theo nhịp, không dồn cục (như `BR-29.3`); không phát sóng ngay được. Khi khai báo, hệ thống liệt kê các chiến dịch và chuỗi bị ảnh hưởng và thông báo người phụ trách.

  **Lý do nghiệp vụ:** Những ngày này thường biết trước; khai báo một lần tốt hơn nhiều so với trông chờ từng người nhớ hoãn chiến dịch của mình, và tránh việc chiến dịch hẹn giờ tự bắn đi vào đúng ngày cần im lặng.

  Một khoảng không gửi liên tục dài quá `CFG-CAMP-46` cần Chủ sở hữu chấp thuận.

- **`BR-43.2` (Phạm vi):** Ngày không gửi chỉ áp cho nhóm mục đích Tiếp thị & Quảng bá; không áp cho gửi thử nội bộ, thông điệp Giao dịch & Dịch vụ và Liên lạc 1-1.

  **Lý do nghiệp vụ:** Ngày im lặng về quảng cáo không phải là ngày ngừng phục vụ khách hàng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-43.1.1` | Ngày 20 là ngày không gửi; chiến dịch Đã duyệt hẹn 09:00 ngày 20 | Tới giờ | Chiến dịch Giữ lại, lý do "Ngày không gửi" |
| `AC-43.1.2` | Chiến dịch dàn trải đang gửi; ngày 20 là ngày không gửi | Ngày 20 | Không tin nào được gửi trong ngày 20; ngày 21 gửi tiếp theo nhịp kể từ khi hết khung giờ yên lặng |
| `AC-43.1.3` | Quản trị viên khai báo ngày 20 là ngày không gửi | Lưu | Hệ thống liệt kê các chiến dịch và chuỗi bị ảnh hưởng; người phụ trách được thông báo |
| `AC-43.1.4` | `CFG-CAMP-46` = 7 ngày | Quản trị viên khai báo khoảng không gửi 10 ngày | Khoảng chưa có hiệu lực cho tới khi Chủ sở hữu chấp thuận |
| `AC-43.2.1` | Ngày không gửi | Gửi thử nội bộ | Gửi thử vẫn thực hiện được |

---

#### FEAT-44 — Cổng Kiểm soát Gửi Tiếp thị Dùng chung

**Mô tả nghiệp vụ:** Mọi thông điệp thuộc nhóm Tiếp thị & Quảng bá — không chỉ từ phân hệ Chiến dịch mà cả từ các đường gửi khác của nền tảng (ví dụ thư gửi cho một danh sách hay lịch gửi tự động của phân hệ Khách hàng, theo `contacts-srs.md`, `BR-30.5`, `BR-30.7`) — đi qua cùng một cổng kiểm soát tại thời điểm gửi.

**Quy tắc nghiệp vụ:**

- **`BR-44.1` (Một bộ kiểm tra cho mọi đường gửi tiếp thị):** Trước khi một tin nhóm Tiếp thị rời hệ thống, bất kể phân hệ phát ra, cổng áp: lý do loại trừ 1–7, 11, 13 và 15 của `BR-08.1` với quy tắc (a), (b) của `BR-08.4`; nhãn quảng cáo và cơ chế từ chối (`BR-30.2`, `BR-26.1`); khung giờ yên lặng (`BR-29.1`) — tin bị hoãn; giới hạn 24 giờ (`BR-30.3`, kể cả thời gian hoãn tối đa `CFG-CAMP-43`) và giới hạn tần suất (`BR-08.5`); dừng khẩn cấp và ngày không gửi (`FEAT-42`, `FEAT-43`); nguyên tắc không chắc thì không gửi (`NFR-06`); căn cứ theo dõi hành vi (`BR-14.4`) — với người không có căn cứ, tin đi với liên kết chung không gắn danh tính và không có cơ chế đo mở; cấm trường nhạy cảm trong cá nhân hóa (`BR-12.1`); thông tin nhận diện người gửi và liên kết hủy nhận tin trong email (`BR-11.1`); ghi ngược hủy nhận tin, khiếu nại thư rác, điểm đến hỏng, chặn và tắt tin tiếp thị trên nền tảng (`FEAT-26`–`FEAT-28`, `BR-24.1`) cho cả tin do phân hệ khác phát ra; tên thương hiệu SMS phải thuộc loại Quảng cáo (`BR-10.5`) và tin nhắn mẫu phải thuộc loại được dùng cho tiếp thị (`BR-13.1`). Tin đi qua cổng được **đếm chung** vào giới hạn 24 giờ và giới hạn tần suất với tin của chiến dịch.

  **Lý do nghiệp vụ:** Nếu các lớp bảo vệ chỉ nằm trong phân hệ Chiến dịch, bất kỳ đường gửi hàng loạt nào khác đều trở thành đường vòng: một nhân viên gửi "cho toàn bộ danh sách" lúc 23:00 tới người đã yêu cầu xóa dữ liệu mà không có nhãn quảng cáo — trái chính sách của chính doanh nghiệp dù phân hệ Chiến dịch làm đúng mọi thứ.

- **`BR-44.2` (Kết quả trả về cho phân hệ gửi):** Cổng trả về cho phân hệ gửi kết quả cho từng người nhận — đã gửi, đã hoãn (kèm thời điểm dự kiến), hoặc bị loại (kèm lý do theo danh mục `BR-08.1`) — để phân hệ đó hiển thị cho người thao tác. **Mọi** kết quả — gửi, hoãn, loại — được ghi vào **sổ cái của cổng** với cùng nội dung như sổ cái chiến dịch (`BR-23.1`), gồm căn cứ cho phép lượt gửi theo `BR-23.1`; sổ cái của cổng chịu cùng thời hạn lưu (`CFG-CAMP-20`, `CFG-CAMP-45`) và cùng quy tắc khử định danh khi có yêu cầu xóa (`BR-32.2`, `BR-32.3`).

  **Lý do nghiệp vụ:** Người gửi phải biết tin của mình không đi và vì sao; nếu không, họ sẽ tìm cách gửi lại qua đường khác. Lượt gửi thành công cũng phải có bằng chứng, vì khi khách khiếu nại "tôi chưa từng đồng ý", doanh nghiệp cần chỉ ra căn cứ cho đúng lượt gửi đó dù nó phát ra từ phân hệ nào.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-44.1.1` | Điểm đến của K khớp dấu vết chặn gửi | Nhân viên gửi thư tiếp thị cho một danh sách có K từ phân hệ Khách hàng | K không nhận thư; nhân viên thấy K bị loại lý do "Từ chối nhận tin" |
| `AC-44.1.2` | `CFG-CAMP-28` bật nhãn SMS "QC"; lịch gửi tự động của phân hệ Khách hàng gửi SMS tiếp thị lúc 23:00 giờ của người nhận | Tới giờ | Tin bị hoãn tới hết khung giờ yên lặng; nội dung có nhãn "QC" và hướng dẫn từ chối |
| `AC-44.1.3` | K đã nhận 1 SMS quảng cáo từ chiến dịch lúc 09:00; `CFG-CAMP-43` = 2 giờ | Nhân viên gửi SMS tiếp thị cho danh sách có K lúc 15:00 | K bị loại "Chạm giới hạn 24 giờ theo điểm đến" (còn 18 giờ mới hết 24 giờ, vượt thời gian hoãn tối đa) — đếm chung với tin của chiến dịch |
| `AC-44.1.4` | K Đồng ý theo phiên bản điều khoản không nêu mục đích đo lường tương tác | Phân hệ Khách hàng gửi thư tiếp thị cho danh sách có K | Thư tới K không có cơ chế đo mở và dùng liên kết chung; không có sự kiện mở/nhấp gắn danh tính K |
| `AC-44.1.5` | Lịch gửi SMS tiếp thị của phân hệ khác dùng tên thương hiệu loại Chăm sóc khách hàng | Tới giờ | Không gửi; kết quả nêu lỗi tên thương hiệu không thuộc loại Quảng cáo |
| `AC-44.1.6` | Thư tiếp thị gửi từ phân hệ Khách hàng qua cổng; nhà cung cấp báo K đánh dấu thư rác | Hệ thống xử lý | Kênh email của K chuyển Từ chối nhận tin, nguồn "Khiếu nại thư rác" |
| `AC-44.2.1` | Tiếp nối AC-44.1.1 | Nhân viên mở kết quả lượt gửi | Thấy từng người: đã gửi / đã hoãn / bị loại kèm lý do |
| `AC-44.2.2` | Tiếp nối AC-44.1.1, K2 trong danh sách được gửi thành công | Mở sổ cái của cổng | Dòng của K2 có tham chiếu tới bằng chứng Đồng ý |

---

### Nhóm M — Thông báo Dịch vụ

#### FEAT-45 — Thông báo Dịch vụ Hàng loạt

**Mô tả nghiệp vụ:** Doanh nghiệp gửi hàng loạt thông báo không mang tính quảng bá — gián đoạn dịch vụ, thu hồi sản phẩm, thay đổi điều khoản, thông báo pháp lý, thay đổi lịch phục vụ — tới cả những khách hàng đã từ chối nhận tiếp thị, vì đó là thông tin khách cần biết về sản phẩm, dịch vụ họ đang dùng. Thông báo dịch vụ là một **loại chiến dịch** riêng, thuộc nhóm mục đích Giao dịch & Dịch vụ (`contacts-srs.md`, `BR-30.5`), với kiểm soát chặt để một bản tin khuyến mãi không thể đổi nhãn đi qua đường này. Mọi quy tắc của chiến dịch thường (vòng đời, phê duyệt kép, hẹn giờ, tạm dừng, sổ cái, ngân sách, hạn mức gói) áp như nhau, trừ phần nêu dưới đây. Ở mọi quy tắc viện dẫn danh sách lý do loại trừ (`BR-06.4`, `BR-08.3`, `BR-08.4`), Thông báo dịch vụ dùng tập lý do tại `BR-45.4`.

**Quy tắc nghiệp vụ:**

- **`BR-45.1` (Loại và mục đích):** Loại "Thông báo dịch vụ" được chọn khi tạo chiến dịch và không đổi được sau đó (muốn đổi thì tạo chiến dịch mới). Người soạn bắt buộc khai báo **một** mục đích trong danh mục do hệ thống duy trì: (1) gián đoạn, bảo trì sản phẩm hay dịch vụ khách đang dùng; (2) thu hồi sản phẩm hoặc cảnh báo an toàn; (3) thay đổi điều khoản, chính sách hoặc giá của sản phẩm, dịch vụ khách đang dùng; (4) thông báo mà pháp luật bắt buộc doanh nghiệp gửi; (5) thay đổi nơi hoặc giờ khách **đang được phục vụ** (nghỉ lễ, giờ làm việc, chuyển điểm phục vụ) — không dùng để giới thiệu điểm kinh doanh mới. Với mục đích (4), người soạn bắt buộc dẫn chiếu văn bản pháp luật yêu cầu gửi. Doanh nghiệp tắt được từng mục đích (`CFG-CAMP-63`) nhưng không thêm được mục đích mới. Tắt một mục đích thì mọi Thông báo dịch vụ chưa bắt đầu gửi thuộc mục đích đó gặp lỗi chặn nhóm (a) và về Nháp; thông báo đang gửi không bị ảnh hưởng vì danh sách đã chốt. Lượt gửi của Thông báo dịch vụ được khai báo thuộc nhóm Giao dịch & Dịch vụ theo ngoại lệ của (`contacts-srs.md`, `BR-30.7`).

  **Lý do nghiệp vụ:** Danh mục đóng là ranh giới quan sát được giữa thông báo và quảng bá; nếu doanh nghiệp tự thêm mục đích, "tin tri ân khách hàng" sẽ sớm thành một mục đích. Không đổi loại sau khi tạo để một chiến dịch tiếp thị không thể mượn phê duyệt của thông báo.

- **`BR-45.2` (Nội dung):** Nội dung Thông báo dịch vụ **không** được chứa: ưu đãi, giá khuyến mãi, mã giảm giá, lời mời mua hay nâng cấp, liên kết tới trang bán hàng, tham số nguồn gốc, nhãn quảng cáo. Liên kết chỉ trỏ tới tên miền trong danh sách tên miền doanh nghiệp (`FEAT-14`) và không gắn cơ chế đo nhấp hay mở gắn danh tính. Cá nhân hóa chỉ dùng trường nhận diện (tên, mã khách hàng, mã đơn hàng hay hợp đồng, sản phẩm khách đang dùng). Kênh chỉ dùng tài khoản và mẫu loại giao dịch: tên thương hiệu SMS loại Chăm sóc khách hàng, mẫu WhatsApp loại tiện ích, mẫu Zalo ZNS loại giao dịch, tin Zalo OA loại giao dịch, email. Danh mục từ khuyến mãi (hệ thống duy trì, doanh nghiệp được thêm theo `CFG-CAMP-67`) chia hai mức: mức **chặn** (ví dụ "mã giảm giá", "voucher", "khuyến mãi", "giảm giá") là lỗi chặn nhóm (a) — nội dung phải sửa; mức **cảnh báo** (ví dụ "ưu đãi", "tri ân", "trả góp") hiện trên danh mục kiểm tra (`FEAT-16`) và người duyệt phải xác nhận từng cảnh báo.

  **Lý do nghiệp vụ:** Thông báo dịch vụ chỉ được đi qua đồng thuận tiếp thị vì nó không bán gì; một liên kết khuyến mãi hay một tham số đo chiến dịch là dấu hiệu nó đang bán.

- **`BR-45.3` (Phê duyệt của Người phụ trách Bảo vệ Dữ liệu):** Ngoài phê duyệt phát sóng (`BR-17.2`), mỗi phiên bản Thông báo dịch vụ bắt buộc có phê duyệt của Người phụ trách Bảo vệ Dữ liệu cho mục đích, nội dung và tiêu chí đối tượng — kể cả khi `CFG-CAMP-05` tắt. **Không vai trò nào thay được** phần duyệt này, kể cả Quản trị viên và Chủ sở hữu; chỉ khi doanh nghiệp không chỉ định Người phụ trách Bảo vệ Dữ liệu thì áp quy tắc thay thế tại Mục 2.2. Người duyệt phần bảo vệ dữ liệu phải khác người soạn, khác người duyệt phát sóng và khác mọi người đã sửa phiên bản. Khi Chủ sở hữu đảm nhận vai trò này (doanh nghiệp không chỉ định) mà chính Chủ sở hữu là người soạn, người sửa hay người duyệt phát sóng, phần duyệt bảo vệ dữ liệu do một Quản trị viên khác thực hiện theo quy tắc thay thế người thứ hai (Mục 2.2). Mọi thay đổi phiên bản sau phê duyệt cần duyệt lại cả hai (`BR-17.4`).

  **Lý do nghiệp vụ:** Người chịu trách nhiệm về đồng thuận là người phải quyết định một thông điệp có được đi qua đồng thuận hay không; người có chỉ tiêu tiếp thị không nên tự quyết điều đó.

- **`BR-45.4` (Đối tượng và loại trừ):** Tiêu chí đối tượng theo `FEAT-05`; người soạn ghi lý do tập đối tượng liên quan tới mục đích (ví dụ khách có dịch vụ tại khu vực bảo trì). Người nhận phải đang có quan hệ với doanh nghiệp: chỉ hồ sơ ở giai đoạn vòng đời được phép cho mục đích đó theo `CFG-CAMP-66` được chọn — mặc định Customer, Evangelist cho mục đích (1), (3), (5), thêm Churned cho mục đích (2), (4); không bao giờ gồm các giai đoạn tiền bán hàng (Subscriber, Lead, MQL, SQL, Opportunity, Nurturing), Disqualified hay hồ sơ khách hàng tạm. Hồ sơ ngoài tập này bị loại với lý do 2 (Giai đoạn vòng đời). Ngoài lý do 2 theo cách hiểu này, Thông báo dịch vụ áp các lý do 3, 4, 8, 9, 10, 11, 14, 15, 16, 17, 18 của `BR-08.1`, và lý do 1 (Hạn chế xử lý) cho mục đích (1), (3), (5). **Không** áp: lý do 1 cho mục đích (2) và (4) — theo (`contacts-srs.md`, `BR-30.6`), Giao dịch & Dịch vụ vẫn được xử lý khi đang Hạn chế xử lý, và đây là hai mục đích bảo vệ chính người nhận hoặc do luật bắt buộc; lý do 5 (Từ chối nhận tin, dấu vết chặn gửi, tạm chặn chờ xác nhận) — đều là rút đồng thuận tiếp thị; lý do 6, 7, 12, 13. Khách đã bị xóa dữ liệu không còn hồ sơ nên không thể là người nhận. Khách chặn doanh nghiệp trên nền tảng vẫn bị loại (`BR-24.1`).

  **Lý do nghiệp vụ:** Khách đã rời bỏ hay đã từ chối tiếp thị vẫn cần biết sản phẩm mình mua bị thu hồi; nhưng người chưa từng có quan hệ với doanh nghiệp không có gì để được "thông báo" — gửi cho họ là quảng cáo. Hạn chế xử lý chặn các thông báo không thiết yếu, nhưng không được chặn cảnh báo an toàn hay thông báo luật định.

- **`BR-45.5` (Tần suất và thời điểm):** Số Thông báo dịch vụ tới một người — gộp mọi kênh — không vượt số tin trong khoảng trượt đặt tại `CFG-CAMP-64`, trừ mục đích (2) và (4) — vẫn được đếm. Khung giờ yên lặng của doanh nghiệp (`FEAT-29`) áp như chiến dịch thường; riêng các mục đích doanh nghiệp chọn tại `CFG-CAMP-65` (trong (1), (2), (4)), khi Người phụ trách Bảo vệ Dữ liệu xác nhận gửi khẩn cấp tại phê duyệt, thông báo được gửi cả trong khung giờ yên lặng. Bỏ một mục đích khỏi `CFG-CAMP-65` sau khi đã xác nhận thì xác nhận hết hiệu lực: phần chưa gửi tuân khung giờ yên lặng từ thời điểm đó. Theo `BR-43.2`, ngày không gửi không áp cho Thông báo dịch vụ. Nhãn quảng cáo (`CFG-CAMP-28`) không áp vì đây không phải tin quảng cáo. Dừng khẩn cấp (`FEAT-42`) **không** tự tạm dừng Thông báo dịch vụ; người có quyền vẫn tạm dừng từng thông báo được. Hạn ngạch ngày tiếp thị (`FEAT-40`) không áp và Thông báo dịch vụ không dùng phần hạn ngạch đó (`BR-40.1`); hạn mức gói dịch vụ và ngân sách vẫn áp. Nguyên tắc không chắc thì không gửi (`NFR-06`) áp cho mọi thông tin thật sự quyết định việc gửi: trạng thái tiếp cận (mọi mục đích); Hạn chế xử lý, số thông báo đã gửi theo `CFG-CAMP-64` và lý do 17 (chỉ mục đích (1), (3), (5)). Với mục đích (2), (4), việc không tra được những thông tin không áp cho chúng không làm hoãn thông báo.

  **Lý do nghiệp vụ:** Không có trần tần suất, đường này thành kênh tiếp thị không cần đồng thuận; nhưng cảnh báo an toàn và thông báo luật định không được vì trần mà đến muộn. Dừng khẩn cấp dành cho tiếp thị trong khủng hoảng — đúng lúc thông báo an toàn cần đi nhất.

- **`BR-45.6` (Tính năng không áp dụng):** Thông báo dịch vụ không dùng được thử nghiệm A/B (`FEAT-38`), chuỗi nuôi dưỡng (`FEAT-39`), Tái tiếp cận (`FEAT-31`), miễn trừ tần suất (`BR-08.6`); kênh dự phòng chỉ dùng được sang kênh có mẫu loại giao dịch. Tương tác với Thông báo dịch vụ không gắn thẻ tự động (`FEAT-36`), không gửi sang phân hệ Khách hàng để cộng điểm tương tác, không ghi cơ hội chịu ảnh hưởng (`FEAT-34`). Tin trả lời vẫn vào Hộp thư Hội thoại theo `BR-35.1`, và lời từ chối nhận tiếp thị trong tin trả lời vẫn được xử lý theo `BR-26.3`.

  **Lý do nghiệp vụ:** Mọi cơ chế đo và tối ưu hiệu quả bán hàng đều biến thông báo thành công cụ bán hàng.

- **`BR-45.7` (Giám sát sau gửi):** Mọi tình huống tự động tạm dừng bảo vệ của `BR-20.1` áp cho Thông báo dịch vụ, trừ tình huống 5 (hạn ngạch tiếp thị không áp) và 8; tình huống 10 chỉ áp cho thông báo mục đích (1), (3), (5) gửi qua SMS, vì đầu số là cơ chế phản đối của chúng (`BR-45.8`); khi một Thông báo dịch vụ bị tự động tạm dừng, Người phụ trách Bảo vệ Dữ liệu được thông báo ngay. Khiếu nại thư rác được xử lý theo `FEAT-27`. Báo cáo định kỳ gửi Người phụ trách Bảo vệ Dữ liệu — theo cùng chu kỳ với báo cáo Liên lạc 1-1 của (`contacts-srs.md`, `BR-30.8`) — liệt kê mọi Thông báo dịch vụ đã gửi kèm mục đích, người soạn, người duyệt, số người nhận, số người nhận đang Từ chối nhận tin tiếp thị, tỷ lệ khiếu nại và số lượt Từ chối Thông báo dịch vụ không thiết yếu.

  **Lý do nghiệp vụ:** Khiếu nại trên một thông báo là tín hiệu người nhận coi nó là quảng cáo; hậu kiểm theo mục đích và theo người soạn là cách phát hiện việc lạm dụng đường này.

- **`BR-45.8` (Quyền phản đối thông báo không thiết yếu):** Thông báo dịch vụ mục đích (1), (3), (5) có cơ chế **"Không nhận thông báo dịch vụ không thiết yếu"** — nêu rõ áp cho cả ba mục đích: email có liên kết ở chân thư; SMS có hướng dẫn trả lời từ khóa tới đầu số nhận từ chối; WhatsApp, Zalo OA có hướng dẫn trả lời — trên ba kênh này, hướng dẫn nêu rõ trả lời như vậy là ngừng nhận **cả** thông báo dịch vụ không thiết yếu **lẫn** tin tiếp thị trên kênh đó; Zalo ZNS chỉ dùng được cho ba mục đích này với mẫu có nút hoặc liên kết phản đối, vì kênh này không nhận tin trả lời (Mục 1.6, giả định 2); mẫu WhatsApp cho ba mục đích này phải có sẵn nút hoặc hướng dẫn phản đối vì hệ thống không tự thêm được vào mẫu đã được nền tảng duyệt. Mẫu WhatsApp hay Zalo ZNS thiếu cơ chế này là lỗi chặn nhóm (a). Khách dùng cơ chế này thì điểm đến đó được ghi **Từ chối Thông báo dịch vụ không thiết yếu** trong sổ cái của phân hệ Chiến dịch và bị loại khỏi các thông báo mục đích (1), (3), (5) sau đó (lý do 17). Tin trả lời một Thông báo dịch vụ được xử lý theo `BR-26.3` như tin trả lời chiến dịch: khi được ghi nhận là lời từ chối (tự động hay do người xác nhận), hệ thống ghi **cả** Từ chối nhận tin tiếp thị trên kênh đó **và** Từ chối Thông báo dịch vụ không thiết yếu, vì khách nói "đừng nhắn nữa" không phân biệt loại tin. Mục đích (2) và (4) không có cơ chế này và không bị loại vì nó. Khách muốn nhận lại thì người có quyền sửa hồ sơ ghi nhận theo yêu cầu của chính khách, kèm tài liệu chứng minh yêu cầu và nguồn "Yêu cầu trực tiếp của khách hàng" (`contacts-srs.md`, Phụ lục A.8) — như nhánh nâng từ Chưa có đồng thuận của (`contacts-srs.md`, `BR-30.10`), không như nhánh nâng từ Từ chối nhận tin; việc khôi phục này **không** nâng Từ chối nhận tin tiếp thị. Bản ghi này chịu quyền chủ thể dữ liệu như sổ cái (`FEAT-32`). Thông báo dịch vụ không mang liên kết hủy nhận tin tiếp thị.

  **Lý do nghiệp vụ:** Người nhận có quyền phản đối việc xử lý dữ liệu cho mục đích không thiết yếu; cảnh báo an toàn và thông báo luật định thì doanh nghiệp buộc phải gửi.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-45.1.1` | Nhân viên Marketing tạo chiến dịch loại "Thông báo dịch vụ" | Lưu mà không chọn mục đích | Không lưu được; phải chọn một trong các mục đích đang bật |
| `AC-45.1.2` | Doanh nghiệp đã tắt mục đích (5) theo `CFG-CAMP-63` | Tạo Thông báo dịch vụ | Mục đích (5) không có trong danh sách chọn |
| `AC-45.1.3` | Thông báo dịch vụ đang Nháp | Tìm thao tác đổi sang chiến dịch tiếp thị | Không có; giao diện hướng dẫn tạo chiến dịch mới |
| `AC-45.1.4` | Thông báo dịch vụ mục đích (5) Đã duyệt, hẹn ngày mai | Quản trị viên tắt mục đích (5) cùng Người phụ trách Bảo vệ Dữ liệu | Thông báo về Nháp với lỗi chặn nhóm (a); người phụ trách được báo |
| `AC-45.1.5` | Soạn Thông báo dịch vụ mục đích (4) | Gửi phê duyệt không dẫn chiếu văn bản pháp luật | Không gửi phê duyệt được |
| `AC-45.2.1` | Nội dung email thông báo bảo trì chứa "giảm 20% cho khách hàng thân thiết" | Mở danh mục kiểm tra | Cảnh báo từ khuyến mãi; người duyệt phải xác nhận cảnh báo mới duyệt được |
| `AC-45.2.2` | Thông báo dịch vụ SMS | Chọn tên thương hiệu loại Quảng cáo | Không chọn được; chỉ tên thương hiệu loại Chăm sóc khách hàng |
| `AC-45.2.3` | Thông báo dịch vụ email có liên kết tới trang hướng dẫn trên tên miền doanh nghiệp | Gửi | Liên kết không có tham số nguồn gốc và không gắn đo nhấp theo danh tính |
| `AC-45.2.4` | Nội dung Thông báo dịch vụ chứa "nhập mã giảm giá TET24" | Mở danh mục kiểm tra | Lỗi chặn nhóm (a); không gửi phê duyệt được tới khi sửa |
| `AC-45.3.1` | `CFG-CAMP-05` tắt; Quản lý Marketing soạn Thông báo dịch vụ | Bấm "Xác nhận phiên bản" | Không chuyển được sang Đã duyệt khi chưa có phê duyệt của Người phụ trách Bảo vệ Dữ liệu |
| `AC-45.3.2` | Người phụ trách Bảo vệ Dữ liệu P soạn Thông báo dịch vụ | P mở phần duyệt của Người phụ trách Bảo vệ Dữ liệu | Không duyệt được thông báo do chính mình soạn |
| `AC-45.3.3` | Người phụ trách Bảo vệ Dữ liệu đã được chỉ định | Chủ sở hữu mở phần duyệt của Người phụ trách Bảo vệ Dữ liệu trên một Thông báo dịch vụ | Không có thao tác duyệt |
| `AC-45.3.4` | Người phụ trách Bảo vệ Dữ liệu P có quyền Phát sóng; P đã duyệt phần bảo vệ dữ liệu | P thử duyệt phát sóng cùng thông báo | Không cho phép; cần người duyệt phát sóng khác P |
| `AC-45.3.5` | Doanh nghiệp không chỉ định Người phụ trách Bảo vệ Dữ liệu; Chủ sở hữu O soạn Thông báo dịch vụ | O mở phần duyệt bảo vệ dữ liệu | O không duyệt được; một Quản trị viên khác O duyệt phần này theo quy tắc thay thế người thứ hai |
| `AC-45.4.1` | K đã Từ chối nhận tin SMS tiếp thị, giai đoạn Đã rời bỏ; sản phẩm K mua bị thu hồi | Thông báo dịch vụ mục đích (2) gửi SMS | K nhận thông báo |
| `AC-45.4.2` | Khách L đang Hạn chế xử lý | Thông báo dịch vụ mục đích (3) | L bị loại với lý do "Đang Hạn chế xử lý" |
| `AC-45.4.3` | M ở giai đoạn Customer; số của M thuộc Danh sách không quảng cáo, không có Đồng ý nhận tin | Thông báo dịch vụ mục đích (1) | M nhận thông báo; không có nhãn "QC" |
| `AC-45.4.4` | Hồ sơ N ở giai đoạn Lead | Thông báo dịch vụ mục đích (5) | N bị loại với lý do "Giai đoạn vòng đời"; không có cấu hình nào đưa Lead vào tập được phép |
| `AC-45.4.5` | Khách L đang Hạn chế xử lý, giai đoạn Customer | Thông báo dịch vụ mục đích (2) thu hồi sản phẩm | L nhận thông báo |
| `AC-45.4.6` | K ở giai đoạn Customer dùng chung số điện thoại gia đình với hồ sơ Lead N | Thông báo thu hồi (2) qua SMS | Số điện thoại nhận một tin cho K; giai đoạn Lead của N không làm chặn điểm đến |
| `AC-45.4.7` | `CFG-CAMP-23` là "Loại hẳn điểm đến dùng chung"; số gia đình của K (Customer) mang nhãn Định danh dùng chung | Thông báo thu hồi (2) | Số đó nhận một tin; chiến dịch tiếp thị cùng lúc thì loại số đó |
| `AC-45.5.1` | K đã nhận 4 Thông báo dịch vụ mục đích (1), (3) trong 30 ngày (`CFG-CAMP-64` = 4 / 30 ngày) | Thông báo mục đích (5) mới | K bị loại "Chạm giới hạn Thông báo dịch vụ"; một thông báo mục đích (2) vẫn gửi được tới K |
| `AC-45.5.2` | `CFG-CAMP-65` gồm mục đích (2); Thông báo thu hồi sản phẩm mục đích (2) được Người phụ trách Bảo vệ Dữ liệu xác nhận gửi ngay; lúc 23:00 | Phát sóng ngay | Gửi ngay, không hoãn theo khung giờ yên lặng |
| `AC-45.5.3` | `CFG-CAMP-65` không gồm mục đích (2); như trên | Phát sóng ngay lúc 23:00 | Tin hoãn tới hết khung giờ yên lặng |
| `AC-45.5.4` | Dừng khẩn cấp đang bật; hôm nay cũng là ngày không gửi | Một Thông báo dịch vụ đang gửi | Thông báo tiếp tục gửi; mọi chiến dịch tiếp thị tạm dừng |
| `AC-45.5.5` | `CFG-CAMP-64` = 4 / 30 ngày; trong 30 ngày K đã nhận 2 thông báo (1) và 2 thông báo thu hồi (2) | Thông báo mục đích (3) mới | K bị loại "Chạm giới hạn Thông báo dịch vụ" (4 thông báo đã đếm); thông báo (2) tiếp theo vẫn tới K |
| `AC-45.5.6` | Công ty điện lực; `CFG-CAMP-65` gồm mục đích (1); thông báo cắt điện khẩn được xác nhận gửi khẩn cấp | Phát sóng lúc 23:30 | Gửi ngay, không hoãn theo khung giờ yên lặng |
| `AC-45.5.7` | Thông báo cắt điện (1) được xác nhận gửi khẩn cấp, đang gửi lúc 23:00 | Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu bỏ mục đích (1) khỏi `CFG-CAMP-65` | Phần chưa gửi hoãn tới hết khung giờ yên lặng |
| `AC-45.5.8` | Không tra được trạng thái Hạn chế xử lý trong 20 phút | Thông báo thu hồi (2) và thông báo bảo trì (1) cùng tới giờ gửi | Thông báo (2) vẫn gửi; thông báo (1) bị hoãn theo `NFR-06` |
| `AC-45.6.1` | Thông báo dịch vụ | Tìm tùy chọn A/B, Tái tiếp cận, miễn trừ tần suất | Không có |
| `AC-45.6.2` | K trả lời Thông báo dịch vụ qua Zalo OA | Hệ thống xử lý | Tin vào Hộp thư Hội thoại; không gắn thẻ, không cộng điểm tương tác, không ghi cơ hội chịu ảnh hưởng |
| `AC-45.7.1` | Thông báo dịch vụ vượt ngưỡng tỷ lệ hỏng vĩnh viễn `CFG-CAMP-13` | Hệ thống xử lý | Thông báo tự động tạm dừng bảo vệ; Người phụ trách Bảo vệ Dữ liệu được thông báo ngay |
| `AC-45.7.2` | Kỳ báo cáo vừa qua có 3 Thông báo dịch vụ | Người phụ trách Bảo vệ Dữ liệu mở báo cáo định kỳ | Thấy đủ 3 thông báo kèm mục đích, người soạn, người duyệt, số người nhận, số người đang Từ chối nhận tin tiếp thị, tỷ lệ khiếu nại và số lượt Từ chối Thông báo dịch vụ không thiết yếu |
| `AC-45.7.3` | Thông báo thu hồi sản phẩm (2) qua SMS đang gửi; đầu số nhận từ chối của tên thương hiệu ngừng nhận tin | Hệ thống phát hiện | Thông báo tiếp tục gửi, không tạm dừng; một thông báo bảo trì (1) cùng tên thương hiệu thì bị tự động tạm dừng bảo vệ |
| `AC-45.8.1` | K là Customer, nhận thông báo đổi giờ làm việc (5) qua email | K bấm "Không nhận thông báo dịch vụ không thiết yếu" | Email của K bị loại khỏi thông báo (1), (3), (5) sau đó với lý do 17; đồng thuận tiếp thị của K không đổi; thông báo thu hồi (2) sau đó vẫn tới K |
| `AC-45.8.2` | K nhận thông báo bảo trì (1) qua SMS; hướng dẫn cuối tin nêu trả lời "TC" là ngừng cả thông báo không thiết yếu và tin tiếp thị | K trả lời "TC" tới đầu số nhận từ chối | Ghi Từ chối nhận tin SMS tiếp thị và Từ chối Thông báo dịch vụ không thiết yếu; thông báo thu hồi (2) sau đó vẫn tới K |
| `AC-45.8.3` | Soạn Thông báo dịch vụ mục đích (3) qua Zalo ZNS | Chọn mẫu không có nút hay liên kết phản đối | Lỗi chặn nhóm (a); chọn được mẫu đó cho mục đích (2) |
| `AC-45.8.4` | Email của K đang Từ chối Thông báo dịch vụ không thiết yếu; K gọi tổng đài xin nhận lại | Nhân viên có quyền sửa hồ sơ ghi nhận kèm bằng chứng cuộc gọi | K nhận lại thông báo (1), (3), (5); nhật ký ghi người thực hiện và bằng chứng |
| `AC-45.8.5` | Soạn Thông báo dịch vụ mục đích (1) qua WhatsApp | Chọn mẫu tiện ích không có nút hay hướng dẫn phản đối | Lỗi chặn nhóm (a); chọn được mẫu đó cho mục đích (4) |
| `AC-45.8.6` | Thông báo thu hồi (2) qua SMS, tên thương hiệu có đầu số nhận từ chối | Xem trước tin | Tin không có hướng dẫn từ chối |

---

## 4. Yêu cầu phi chức năng

### 4.1 Hiệu năng

- **NFR-01 (Xem trước quy mô):** Xem trước quy mô và phân tích lý do loại trừ trả kết quả trong tối đa 5 giây (95% số lần) với Không gian làm việc có 1.000.000 hồ sơ khách hàng. Nếu vượt thời gian, giao diện hiển thị trạng thái đang tính và cập nhật khi xong, không chặn người dùng tiếp tục soạn.
- **NFR-02 (Năng lực gửi):** Nền tảng gửi được tối thiểu 20.000 tin mỗi phút trên toàn bộ các Không gian làm việc mà không làm chậm các phân hệ khác, trong giới hạn ngưỡng an toàn của nhà cung cấp (`BR-22.2`).
- **NFR-03 (Độ trễ số liệu):** Sự kiện do nhà cung cấp báo về (phân phát, mở, nhấp, hủy nhận tin, khiếu nại) xuất hiện trong sổ cái và báo cáo trong vòng 1 phút sau khi hệ thống nhận được.

### 4.2 Độ tin cậy & Toàn vẹn Dữ liệu

- **NFR-04 (Không gửi trùng):** Mỗi người trong danh sách chốt không bao giờ được chuyển nhà cung cấp nhiều hơn một lần cho mỗi kênh trong chuỗi của một lượt gửi **ngoài** các lượt thử lại và gửi lại được phép tường minh — thử lại tự động tin Thất bại tạm thời (`BR-25.1`), gửi lại thủ công các nhóm tại `BR-25.2` (gồm nhóm Chưa xác định sau khi người có quyền đã xác nhận cảnh báo, `BR-23.3`) — kể cả khi hệ thống gặp sự cố hoặc khởi động lại giữa chừng.
- **NFR-05 (Từ chối nhận tin không bao giờ mất):** Trang hủy nhận tin chỉ hiển thị "thành công" sau khi lời từ chối đã được ghi nhận bền vững. Nếu không ghi nhận được, trang báo lỗi và cho người nhận thử lại; không bao giờ báo thành công giả.
- **NFR-06 (Đóng khi không chắc — Fail-closed):** Nếu tại thời điểm gửi không tra được trạng thái đồng thuận, trạng thái tiếp cận, trạng thái hạn chế xử lý, Danh sách không quảng cáo, dấu vết chặn gửi, hoặc số tin đã nhận để tính tần suất và giới hạn 24 giờ theo điểm đến của một người — hoặc bản Danh sách không quảng cáo đang dùng cũ hơn tuổi tối đa tại `CFG-CAMP-70` — tin tới người đó **không được gửi** mà được hoãn và thử tra lại; nếu tình trạng kéo dài quá thời gian tại Phụ lục B.2, chiến dịch bị tự động tạm dừng bảo vệ. Nguyên tắc này thống nhất với việc hoãn khi không tra được hạn mức (`billing-subscription-srs.md`, `BR-32.3`).
- **NFR-07 (Bất biến của sổ cái và lịch sử phê duyệt):** Sổ cái (`BR-23.4`) và lịch sử phê duyệt (`BR-17.6`) không sửa, không xóa được, trừ khử định danh theo `FEAT-32`.

### 4.3 An toàn & Bảo mật

- **NFR-08 (Nhật ký kiểm toán):** Các thao tác sau được ghi nhật ký bất biến, gồm người thực hiện (hoặc Tiến trình Hệ thống kèm người khởi phát nếu có), thời điểm, giá trị cũ và mới. Danh mục sự kiện: gửi phê duyệt, phê duyệt, từ chối, rút lại; phát sóng ngay, hẹn giờ, hủy lịch; tạm dừng, tiếp tục (kèm ghi chú giải trình), hủy; mọi lần tự động tạm dừng bảo vệ kèm lý do và số liệu; phát sóng không qua phê duyệt kép; miễn trừ giới hạn tần suất; phê duyệt Tái tiếp cận; đổi người phụ trách; xuất sổ cái; thay đổi tài khoản gửi, danh sách người nhận gửi thử, danh sách tên miền doanh nghiệp, từ khóa từ chối, bảng đơn giá, ngân sách, lịch ngày không gửi; kích hoạt và gỡ dừng khẩn cấp; dời giờ hẹn trong cửa sổ không cần duyệt lại; người khởi chạy của mỗi lượt gửi kèm mức của họ lúc khởi chạy, mọi lượt chuyển người khởi chạy và mọi lượt tạm dừng vì người khởi chạy bị tạm ngưng, rời đi hay mất quyền (`BR-17.10`, `BR-17.11`); đổi đơn vị tiếp nhận của tài khoản gửi (`BR-35.5`); mọi lần người dùng ghi nhận từ chối trên tin của khách — tư vấn viên, người có quyền sửa hồ sơ, người có quyền Xử lý tin gắn nhãn từ chối, Người phụ trách Bảo vệ Dữ liệu, Chủ sở hữu (nhật ký lưu tham chiếu tới tin của khách, không sao chép nội dung tin — nội dung theo thời hạn lưu của hội thoại); thay đổi bất kỳ tham số `CFG-CAMP-*` nào; mọi thao tác "Không phải lời từ chối", kèm lý do khi thực hiện ngoài hội thoại (`BR-26.3`); đánh dấu hay bỏ đánh dấu từ khóa đa nghĩa; khai báo mục đích, phê duyệt của Người phụ trách Bảo vệ Dữ liệu và xác nhận gửi khẩn cấp của Thông báo dịch vụ (`FEAT-45`). Khi nhật ký gặp sự cố, các thao tác dừng và thu hẹp tại Nguyên tắc 7 vẫn thực hiện và được **ghi bù** ngay khi nhật ký phục hồi, gắn cờ "ghi bù" kèm thời điểm thao tác thật; Người có toàn quyền được cảnh báo nếu ghi bù chưa xong sau 15 phút ([ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md)); các thao tác còn lại không thực hiện được cho tới khi ghi được nhật ký. Nhật ký kiểm toán không chịu thời hạn lưu sổ cái (`CFG-CAMP-20`) mà theo thời hạn lưu nhật ký kiểm toán của nền tảng (`contacts-srs.md`, `NFR-08`), và phần nhận diện người nhận trong nhật ký được khử định danh khi có yêu cầu xóa (`BR-32.2`).
- **NFR-09 (Liên kết không đoán được):** Liên kết theo dõi và liên kết hủy nhận tin của mỗi người nhận không đoán được và không suy ra được liên kết của người nhận khác; không ai dùng liên kết của mình để hủy nhận tin hộ người khác.
- **NFR-10 (Bảo vệ thông tin xác thực tài khoản gửi):** Thông tin xác thực của tài khoản gửi không hiển thị lại sau khi lưu và không xuất hiện trong bất kỳ tệp xuất, nhật ký hay thông báo nào (`BR-10.1`).

### 4.4 Khả năng Kiểm thử

- **NFR-11 (Đồng hồ nghiệp vụ điều khiển được ở môi trường không phải sản xuất):** Trên môi trường không phải sản xuất, hệ thống có cơ chế **dịch chuyển đồng hồ nghiệp vụ** áp dụng nhất quán cho mọi quy tắc phụ thuộc thời gian của tài liệu này: giờ hẹn, khung giờ yên lặng, cửa sổ giới hạn tần suất, giới hạn 24 giờ theo điểm đến, hạn ngạch ngày, thời gian chờ dự phòng, thời gian đánh giá A/B, thời gian chờ giữa các bước chuỗi nuôi dưỡng, thời hạn tạm dừng tối đa, thời hạn gửi lại, cửa sổ ghi nhận ảnh hưởng, thời hạn thùng rác, thời hạn lưu sổ cái, thời hạn hiệu lực Tái tiếp cận. Cơ chế này **bị cấm tuyệt đối trên môi trường sản xuất**.
- **NFR-12 (Nhà cung cấp giả lập):** Trên môi trường không phải sản xuất, có một nhà cung cấp gửi tin giả lập cho phép kiểm thử chủ động tạo mọi kết quả trong danh mục lỗi (`BR-24.1`), các sự kiện mở/nhấp/trả lời/khiếu nại, lượt nhấp tự động, và báo cáo phân phát đến muộn — không gửi tin thật ra ngoài.

  **Lý do nghiệp vụ:** Phần lớn quy tắc quan trọng nhất của tài liệu (dự phòng, không gửi trùng, tự động tạm dừng, ghi ngược về hồ sơ khách hàng) chỉ kích hoạt khi nhà cung cấp trả về lỗi hoặc sự kiện cụ thể. Không tạo được các tình huống đó một cách chủ động và lặp lại được thì các quy tắc này không bao giờ được kiểm chứng trước khi phát hành.

---

## 5. Ma trận quyền truy cập tính năng

| Mã FEAT | Tính năng | Nhân viên Marketing | Quản lý Marketing | Quản lý *(vai trò dựng sẵn; gồm Quản lý Kinh doanh)* | Người phụ trách BVDL | Người phụ trách thanh toán | Tư vấn viên | Quản trị viên | Chủ sở hữu | Tiến trình Hệ thống |
| --- | --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `FEAT-01` | Tạo & sửa chiến dịch; đổi người phụ trách theo ô Gán | Tạo: Có; Xem: Đơn vị của mình; Sửa: Chỉ của mình; Gán: Không có | Đơn vị của mình | Xem: Đơn vị và các đơn vị con; không tạo, sửa | — | — | — | **Toàn quyền** | **Toàn quyền** | — *(không tự chuyển chiến dịch khi người phụ trách tạm ngưng hay rời đi — `BR-01.8`)* |
| `FEAT-02` | Nhân bản | Chỉ của mình | Đơn vị của mình | — | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-03` | Mã định danh | Xem | Xem | — | — | — | — | Xem | Xem | Cấp tự động |
| `FEAT-04` | Xóa, lưu trữ | Chỉ của mình | Đơn vị của mình | — | — | — | — | **Toàn quyền** | **Toàn quyền** | Dọn thùng rác |
| `FEAT-05` | Tiêu chí đối tượng | Chỉ của mình | Đơn vị của mình | — | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-06` | Xem trước & chốt danh sách | Xem trước | Xem trước | — | — | — | — | Xem trước | Xem trước | Chốt danh sách |
| `FEAT-07` | Xem mẫu người nhận | Đơn vị của mình *(Xem)* | Đơn vị của mình | Đơn vị và các đơn vị con *(Xem)* | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-08` | Loại trừ, tần suất | Xem, đề xuất miễn trừ | Xem, đề xuất và duyệt miễn trừ *(quyền Phát sóng)* | — | Xem | — | — | Như Quản lý Marketing | Như Quản lý Marketing | Áp tự động |
| `FEAT-09` | Chọn kênh | Chỉ của mình | Đơn vị của mình | — | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-10` | Quản lý tài khoản gửi | Chọn | Chọn | — | — | — | — | **Toàn quyền** | **Toàn quyền** | Theo dõi trạng thái |
| `FEAT-11` | Soạn nội dung | Chỉ của mình | Đơn vị của mình | — | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-12` | Cá nhân hóa | Chỉ của mình | Đơn vị của mình | — | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-13` | Tin nhắn mẫu, duyệt nội dung SMS | Chọn | Chọn | — | — | — | — | **Toàn quyền** | **Toàn quyền** | Đồng bộ trạng thái |
| `FEAT-14` | Liên kết theo dõi, tên miền doanh nghiệp | Xem | Xem | — | — | — | — | Quản lý danh sách tên miền | **Toàn quyền** | Gắn tự động |
| `FEAT-15` | Gửi thử | Chỉ của mình | Đơn vị của mình | — | Cùng chấp thuận thay đổi danh sách người nhận gửi thử | — | — | Quản lý danh sách người nhận gửi thử *(cùng BVDL)* | **Toàn quyền** *(thay đổi danh sách vẫn cần BVDL cùng chấp thuận)* | — |
| `FEAT-16` | Danh mục kiểm tra | Xác nhận cảnh báo | Xác nhận cảnh báo | — | — | — | — | Xác nhận cảnh báo | Xác nhận cảnh báo | Kiểm tra tự động |
| `FEAT-17` | Gửi phê duyệt, bình luận | Chỉ của mình | Đơn vị của mình | — | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-17` | Phê duyệt & phát sóng, tự xác nhận khi tắt phê duyệt kép; nhận làm người khởi chạy | — *(Chỉ của mình nếu doanh nghiệp bật — `BR-17.1`)* | Đơn vị của mình *(quyền Phát sóng)* | — | — | Nâng trần hạn mức gói cho lô | — | **Toàn quyền** *(trừ nâng trần hạn mức gói — theo `billing-subscription-srs.md`)* | **Toàn quyền**, xác nhận lô vượt hạn mức gói | Tạm dừng lượt gửi khi người khởi chạy bị tạm ngưng, rời đi hoặc mất quyền (`BR-17.10`, `BR-17.11`) |
| `FEAT-18` | Hẹn giờ | Đề xuất giờ hẹn | Chốt, dời trong cửa sổ, hủy lịch *(quyền Phát sóng)* | — | — | — | — | **Toàn quyền** | **Toàn quyền** | Kích hoạt đúng giờ, chuyển Giữ lại |
| `FEAT-19` | Tạm dừng, tiếp tục | — | Đơn vị của mình *(quyền Phát sóng)* | — | — | — | — | **Toàn quyền** | **Toàn quyền** | Tự hủy khi quá hạn |
| `FEAT-19` | Sửa nội dung trong lúc tạm dừng | Chỉ của mình | Đơn vị của mình | — | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-20` | Tự động tạm dừng bảo vệ | Xem lý do | Xem lý do, tiếp tục *(quyền Phát sóng)* | — | Nhận thông báo | — | — | Xem lý do, tiếp tục | Xem lý do, tiếp tục | Tạm dừng tự động |
| `FEAT-21` | Hủy chiến dịch | — | Đơn vị của mình *(quyền Phát sóng)* | — | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-22` | Nhịp gửi | Chỉ của mình | Đơn vị của mình | — | — | — | — | **Toàn quyền** | **Toàn quyền** | Điều tiết, làm nóng, ưu tiên tin trả lời |
| `FEAT-23` | Xem sổ cái | Đơn vị của mình *(Xem)* | Đơn vị của mình | Đơn vị và các đơn vị con *(Xem)* | **Toàn quyền** *(đọc)* | — | Mục trên hồ sơ khách hàng | **Toàn quyền** | **Toàn quyền** | Ghi tự động |
| `FEAT-23` | Xuất sổ cái | — | Theo ô (Chiến dịch, Xuất); mặc định Không có | — | Theo ô (Chiến dịch, Xuất) | — | — | Có *(Người có toàn quyền)* | Có *(Người có toàn quyền)* | — |
| `FEAT-24` | Phân loại lỗi | Xem | Xem | — | — | — | — | Xem | Xem | Phân loại tự động |
| `FEAT-25` | Gửi lại thủ công | — | Đơn vị của mình *(quyền Phát sóng)* | — | — | — | — | **Toàn quyền** | **Toàn quyền** | Thử lại tự động |
| `FEAT-26` | Hủy nhận tin, từ khóa từ chối | Xem; Ghi nhận từ chối cho khách mình có quyền sửa hồ sơ | Xem; Ghi nhận từ chối từ danh sách tin đang gắn nhãn *(quyền Xử lý tin gắn nhãn từ chối)* | Ghi nhận từ chối cho khách mình có quyền sửa hồ sơ | Xem; từ danh sách tin đang gắn nhãn: Ghi nhận từ chối (từng tin hoặc hàng loạt) và Không phải lời từ chối *(từng tin, có lý do)* | — | Ghi nhận từ chối / Không phải lời từ chối *(trong hội thoại đang mở mà mình xử lý)* | Thêm từ khóa, đánh dấu từ khóa đa nghĩa; Ghi nhận từ chối cho khách mình có quyền sửa hồ sơ | **Toàn quyền** ngoài hội thoại, trừ Không phải lời từ chối *(trừ khi không chỉ định Người phụ trách Bảo vệ Dữ liệu)*; trong hội thoại chỉ khi là người đang xử lý hội thoại đó | Ghi nhận tự động |
| `FEAT-27` | Khiếu nại thư rác | Xem | Xem, nhận cảnh báo | — | Xem, nhận cảnh báo | — | — | Xem, nhận cảnh báo | Xem | Ghi nhận tự động |
| `FEAT-28` | Điểm đến hỏng | Xem | Xem | — | — | — | — | Xem | Xem | Ghi ngược tự động |
| `FEAT-29` | Khung giờ yên lặng | Xem | Xem | — | Cùng chấp thuận thay đổi | — | — | Xem, đề xuất thay đổi; chấp thuận thay khi không chỉ định BVDL | Thay đổi *(cùng BVDL)* | Hoãn tự động |
| `FEAT-30` | Chính sách gửi tiếp thị | Xem | Xem | — | Xem; cùng chấp thuận thay đổi | — | — | Xem, đề xuất thay đổi; chấp thuận thay khi không chỉ định BVDL | Thay đổi *(cùng BVDL)* | Áp tự động |
| `FEAT-31` | Duyệt phần Tái tiếp cận | — | Duyệt, thu hồi phê duyệt của mình | Duyệt phần trong phạm vi mình, thu hồi phê duyệt của mình | — | — | — | — | — | Chuyển về Nurturing khi khách tương tác |
| `FEAT-32` | Khử định danh sổ cái, dấu vết chặn gửi | — | — | — | Cùng Quản trị viên hoặc Chủ sở hữu thực hiện | — | — | Cùng BVDL thực hiện | Cùng BVDL thực hiện | Khử định danh, giữ dấu vết |
| `FEAT-33` | Báo cáo | Đơn vị của mình *(Xem)* | Đơn vị của mình | Đơn vị và các đơn vị con *(Xem)* | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-34` | Cơ hội & doanh thu chịu ảnh hưởng | Đơn vị của mình *(Xem)* | Đơn vị của mình | Cơ hội trong phạm vi mình (`BR-34.5`) | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-35` | Xử lý tin trả lời; công việc liên hệ lại: nhận việc, trả về hàng đợi, đề nghị chuyển | Công việc liên hệ lại: theo ô (Công việc, Gán) và hai đường của người phụ trách hiện tại (`BR-35.5` (e)) | Công việc liên hệ lại: theo ô (Công việc, Gán) và hai đường của người phụ trách hiện tại (`BR-35.5` (e)) | Công việc liên hệ lại: theo ô (Công việc, Gán) và hai đường của người phụ trách hiện tại (`BR-35.5` (e)) | — | — | Theo quy tắc Hộp thư Hội thoại; công việc liên hệ lại: nhận việc theo ô (Công việc, Gán) ([`tasks-srs.md`](./tasks-srs.md) `BR-13.6` (c)), trả về hàng đợi với công việc của hàng đợi ([`tasks-srs.md`](./tasks-srs.md) `BR-13.6` (d)), đề nghị chuyển khi là người phụ trách hiện tại ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`) | **Toàn quyền** | **Toàn quyền** | Chuyển tự động; tạo công việc liên hệ lại (`BR-35.5`) |
| `FEAT-36` | Quy tắc gắn thẻ | Chỉ của mình | Đơn vị của mình | — | — | — | — | **Toàn quyền** | **Toàn quyền** | Gắn tự động |
| `FEAT-37` | Cấu hình dự phòng | Chỉ của mình | Đơn vị của mình | — | — | Xác nhận đợt dự phòng vượt hạn mức gói | — | **Toàn quyền** | **Toàn quyền** | Thực thi dự phòng |
| `FEAT-38` | Thử nghiệm A/B | Chỉ của mình | Đơn vị của mình, chọn thủ công *(quyền Phát sóng)* | — | — | — | — | **Toàn quyền** | **Toàn quyền** | Chọn phiên bản thắng |
| `FEAT-39` | Chuỗi nuôi dưỡng | Chỉ của mình | Đơn vị của mình *(duyệt, bắt đầu, tạm dừng, ngừng nhận, hủy: quyền Phát sóng)* | — | — | — | — | **Toàn quyền** | **Toàn quyền** | Chạy các bước |
| `FEAT-40` | Hạn ngạch ngày | Chọn cách xử lý khi soạn | Chọn cách xử lý khi soạn | — | — | — | — | Cấu hình hạn ngạch | **Toàn quyền** | Đối chiếu, dàn trải |
| `FEAT-41` | Ngân sách, bảng đơn giá, đối soát | Xem | Đặt ngân sách *(quyền Phát sóng)*, xuất đối soát | — | — | Xem đối soát | — | Bảng đơn giá, ngân sách, đối soát | **Toàn quyền** | Theo dõi chi phí |
| `FEAT-42` | Dừng khẩn cấp | — | Kích hoạt *(quyền Phát sóng)* | — | — | — | — | Kích hoạt, gỡ | Kích hoạt, gỡ | Thực thi |
| `FEAT-43` | Lịch ngày không gửi | Xem | Xem | — | — | — | — | Quản lý *(khoảng dài quá `CFG-CAMP-46` cần Chủ sở hữu)* | **Toàn quyền** | Hoãn, chuyển Giữ lại |
| `FEAT-44` | Cổng kiểm soát gửi tiếp thị dùng chung | — | — | — | Xem nhật ký | — | — | Xem nhật ký | Xem nhật ký | Áp tự động cho mọi phân hệ |
| `FEAT-45` | Thông báo dịch vụ hàng loạt | Chỉ của mình (soạn) | Đơn vị của mình; phát sóng *(quyền Phát sóng)* | — | Duyệt mục đích, nội dung, đối tượng *(bắt buộc)*; xác nhận gửi khẩn cấp | — | — | **Toàn quyền**, trừ phần duyệt của BVDL; bật/tắt mục đích *(cùng BVDL)*; khôi phục Từ chối Thông báo dịch vụ không thiết yếu cho khách mình có quyền sửa hồ sơ | **Toàn quyền**, trừ phần duyệt của BVDL *(trừ khi không chỉ định BVDL)*; bật gửi khẩn cấp *(cùng BVDL)* | Kiểm tra từ khuyến mãi, thống kê báo cáo |

**Ghi chú về ma trận:**

1. **Ma trận là cấu hình mặc định, không phải năng lực gắn cứng.** Các cột vai trò ghi giá trị mặc định của vai trò dựng sẵn theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29` (cột Nhân viên Marketing = vai trò **Marketing**, cột Quản lý Marketing = vai trò **Quản lý Marketing**) và của cấp bậc thành viên; doanh nghiệp đổi được từng ô qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02` hoặc dùng vai trò tự tạo, trong giới hạn các sàn ở ghi chú 3. Giá trị mức trong ô ("Chỉ của mình", "Đơn vị của mình"…) là mức truy cập theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4, áp trên ô (Chiến dịch, thao tác) tương ứng với tính năng (`BR-01.4`); "*(Xem)*" nghĩa là mức của ô Xem. "*(quyền Phát sóng)*" nghĩa là cần ô (Chiến dịch, Phát sóng) bao phủ chiến dịch (`BR-17.1`). "**Toàn quyền**" là của Người có toàn quyền. Đối tượng nhận tin còn bị giới hạn thêm bởi mức Xem trên Khách hàng của người chọn đối tượng và của người khởi chạy (`BR-05.4`, `BR-17.10`). Cột Tư vấn viên đại diện cho mọi người dùng chỉ có quyền xem hồ sơ khách hàng (`BR-23.6`). Chức danh Người phụ trách Bảo vệ Dữ liệu và Người phụ trách thanh toán không tự cấp ô nào; các cột này chỉ ghi phần việc mà chức danh đó được giao trong phân hệ.

2. **Quyền quản trị của phân hệ** (dạng có/không, không gắn với chiến dịch cụ thể; khai báo vào danh mục quyền của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-24`). Người có toàn quyền luôn có; vai trò bất kỳ được cấp:

   | Quyền quản trị | Phạm vi | Mặc định |
   | --- | --- | --- |
   | **Quản lý kênh gửi tiếp thị** | Tài khoản gửi, định danh người gửi, đơn vị tiếp nhận của tài khoản gửi (`BR-35.5`, đổi đơn vị cần Người có toàn quyền), danh sách tên miền doanh nghiệp, từ khóa từ chối | Người có toàn quyền |
   | **Quản lý danh sách người nhận gửi thử** | `FEAT-15`: Danh sách người nhận gửi thử và danh sách tên miền nội bộ (`BR-15.1`); mọi thay đổi vẫn cần Người phụ trách Bảo vệ Dữ liệu cùng chấp thuận | Người có toàn quyền |
   | **Quản lý hạn ngạch và chi phí gửi** | Hạn ngạch ngày, bảng đơn giá kênh (`FEAT-40`, `FEAT-41`) | Người có toàn quyền |
   | **Quản lý lịch ngày không gửi** | `FEAT-43`; khoảng dài quá `CFG-CAMP-46` cần Chủ sở hữu | Người có toàn quyền |
   | **Cấu hình nhịp độ tiếp thị** | Các tham số nhịp độ và thói quen tiếp thị thường nhật tại Phụ lục B | Người có toàn quyền; vai trò dựng sẵn Quản lý Marketing (ghi chú 6) |
   | **Xử lý tin gắn nhãn từ chối** | Ghi nhận từ chối nhận tin từ danh sách tin đang gắn nhãn và công việc liên hệ lại, kể cả hàng loạt; nhận cảnh báo leo thang khi tin gắn nhãn quá `CFG-CAMP-44` chưa được xử lý (`BR-26.3`). Không gồm "Không phải lời từ chối" | Người có toàn quyền; vai trò dựng sẵn Quản lý Marketing (ghi chú 6) |
   | **Gỡ dừng khẩn cấp gửi tiếp thị** | `BR-42.2`; kích hoạt không cần quyền quản trị này | Người có toàn quyền |

3. **Sàn bắt buộc của phân hệ** ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.3`) — không vai trò, điều chỉnh ô hay quyền quản trị nào vượt được, áp cả lên Người có toàn quyền khi ghi rõ:
   - Phê duyệt kép khi `CFG-CAMP-05` bật: người duyệt khác người gửi duyệt và khác mọi người đã sửa phiên bản (`BR-17.2`) — áp cả lên Người có toàn quyền.
   - Phần Tái tiếp cận chỉ do hai bên theo `contacts-srs.md` `BR-12.5b` (a) duyệt; Người có toàn quyền không thay được (`BR-31.2`).
   - Không ai chọn được nhóm mục đích gửi khác Tiếp thị cho chiến dịch tiếp thị (`BR-01.5`); thay đổi danh sách người nhận gửi thử và phê duyệt của Thông báo dịch vụ luôn cần Người phụ trách Bảo vệ Dữ liệu (`BR-15.1`, `FEAT-45`).
   - Không vai trò nào nới được đối tượng vượt **tập khách hàng xem được** của người chọn đối tượng và người khởi chạy theo bước 1–3 của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`: không tính lượt cấp trên bản ghi (kể cả quyền đọc tự động, `BR-39.5`) và quyền tạm thời; chính sách Từ chối, lượt chặn và bản ghi giữ chỗ luôn áp dụng (`BR-05.4`, `BR-17.10`).

4. **Trường nhạy cảm và hàng đợi:** khai báo theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-40` tại `BR-07.3`; khai báo hàng đợi theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10`, `BR-35.11`, `BR-35.12`, `BR-35.13`, `BR-35.14` tại `BR-35.5`.

5. Quản trị viên và Chủ sở hữu **không** thay được Quản lý Marketing và Quản lý Kinh doanh trong phê duyệt phần Tái tiếp cận — đây là ràng buộc của (`contacts-srs.md`, `BR-12.5b` (a)), là ngoại lệ có chủ đích đối với nguyên tắc Người có toàn quyền. Thẩm quyền đổi tham số cấu hình là trục quyền riêng, xem Phụ lục B.

6. **Ma trận mặc định chi tiết của vai trò dựng sẵn trên loại dữ liệu Chiến dịch** (khai báo theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-29.6`; đây là giá trị mặc định có hiệu lực, điều chỉnh được qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02`). Loại Chiến dịch không có thao tác Nhập. Cột "Lệch" so với mức mặc định chung của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`:

   | Vai trò dựng sẵn | Xem | Tạo | Sửa | Xoá | Xuất | Gán | Phát sóng | Quyền quản trị của phân hệ | Lệch so với mức chung — lý do nghiệp vụ |
   | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
   | Quản lý | Đơn vị và các đơn vị con | Không có | Không có | Không có | Không có | Không có | Không có | Không có | Lệch "Quản lý dữ liệu đơn vị" ở Tạo, Sửa, Xoá, Gán. Vai trò Quản lý dùng chung cho trưởng nhóm Kinh doanh, Hỗ trợ và các đội khác; soạn, sửa, xóa hay giao chiến dịch là việc của đội Marketing, và một trưởng nhóm kinh doanh sửa được chiến dịch của đội mình là đường vòng qua phê duyệt của Marketing. Xem giữ theo mức chung để trưởng nhóm theo dõi chiến dịch và cơ hội chịu ảnh hưởng của đơn vị (`FEAT-34`); phê duyệt phần Tái tiếp cận không cần ô nào trên Chiến dịch (`BR-31.2`) |
   | Nhân viên Kinh doanh | Không có | Không có | Không có | Không có | Không có | Không có | Không có | Không có | Không lệch — mức chung không gồm Chiến dịch |
   | Nhân viên Hỗ trợ | Không có | Không có | Không có | Không có | Không có | Không có | Không có | Không có | Không lệch — mức chung không gồm Chiến dịch |
   | Quản lý Marketing | Đơn vị của mình | Có | Đơn vị của mình | Đơn vị của mình | Không có | Đơn vị của mình | Đơn vị của mình | Cấu hình nhịp độ tiếp thị; Xử lý tin gắn nhãn từ chối | Gán = Đơn vị của mình thay vì Chỉ của mình (mức "Như Marketing"): trưởng nhóm phân công chiến dịch trong đội, nhận bàn giao khi nhân viên nghỉ phép hay chuyển việc (`BR-01.8`). Hai quyền quản trị: nhịp độ tiếp thị và việc xác nhận lời từ chối hàng loạt là việc vận hành hằng ngày của trưởng nhóm Marketing; các tham số chạm tới tuân thủ vẫn cần Người phụ trách Bảo vệ Dữ liệu (Phụ lục B), và "Không phải lời từ chối" vẫn chỉ của Người phụ trách Bảo vệ Dữ liệu (`BR-26.3`) |
   | Marketing | Đơn vị của mình | Có | Chỉ của mình | Chỉ của mình | Không có | Không có | Không có *(Chỉ của mình nếu bật tại khởi tạo, `BR-17.1`)* | Không có | Xoá = Chỉ của mình thay vì Không có: chỉ chiến dịch chưa từng gửi tin nào mới xóa được (`BR-04.1`), nên xóa nháp của chính mình là việc dọn dẹp không làm mất bằng chứng gửi tin. Gán = Không có thay vì Chỉ của mình: giao chiến dịch cho người khác làm chiến dịch đổi đơn vị và đổi người chịu trách nhiệm, là quyết định của trưởng nhóm; nhân viên vẫn đề nghị chuyển chiến dịch mình phụ trách được theo đường (2) của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6` — người nhận phải chấp nhận và có ô Gán (`BR-01.8`) — và nhân viên nghỉ việc vẫn được bàn giao qua quy trình chung |
   | Chỉ xem bản ghi được giao | Chỉ của mình | Không có | Không có | Không có | Không có | Không có | Không có | Không có | Không lệch |
   | Kiểm toán | Toàn workspace | Không có | Không có | Không có | Không có | Không có | Không có | Không có | Không lệch |
   | Kiểm toán quyền | Không có | Không có | Không có | Không có | Không có | Không có | Không có | Không có | Không lệch |

   Các quyền quản trị khác của phân hệ (ghi chú 2) mặc định chỉ Người có toàn quyền có. Cột vai trò trong bảng tính năng ở trên là cách đọc của ma trận này theo từng tính năng; khi hai nơi khác nhau, ma trận này thắng.

---

## 6. Kịch bản chấp nhận tổng hợp (UAT)

**Quy ước chung cho toàn bộ mục này:**

- Mỗi kịch bản chạy trên một Không gian làm việc mới tạo riêng, múi giờ GMT+7, mọi tham số ở giá trị mặc định (Phụ lục B) trừ khi kịch bản nêu khác.
- Nhà cung cấp gửi tin là nhà cung cấp giả lập (`NFR-12`); mọi quy tắc phụ thuộc thời gian dùng dịch chuyển đồng hồ nghiệp vụ (`NFR-11`).
- Không gian làm việc có: Nhân viên Marketing **A**, Quản lý Marketing **M** (có quyền Phát sóng chiến dịch), Quản lý Marketing **M2** (có quyền Phát sóng chiến dịch), Quản lý Kinh doanh **S**, Người phụ trách Bảo vệ Dữ liệu **P**, Người phụ trách thanh toán **B**, Tư vấn viên **T**, Quản trị viên **Q**, cùng thuộc một đơn vị. Hạn mức gói dịch vụ đủ lớn, hạn ngạch ngày đủ lớn, tài khoản gửi đã qua giai đoạn làm nóng, trừ khi kịch bản nêu khác.
- Trừ khi kịch bản nêu khác, mọi khách hàng trong kịch bản đều có Đồng ý nhận tin có bằng chứng trên kênh được dùng theo phiên bản điều khoản **có nêu mục đích đo lường tương tác** (`BR-14.4`), và không thuộc Danh sách không quảng cáo.

### Kịch bản 1: Chiến dịch Email từ Nháp tới Hoàn tất với phê duyệt kép

1. A tạo chiến dịch Email "Khuyến mãi Mùa Thu", chọn tài khoản gửi đã xác thực tên miền, tiêu chí "thẻ VIP **và** giai đoạn Khách hàng".
2. Xem trước: 5.200 khớp; 120 không có email; 30 Từ chối nhận tin email; 40 chưa có Đồng ý có bằng chứng → khả dụng 5.010.
3. A soạn nội dung có "{tên}" với giá trị thay thế "Quý khách", gửi thử tới email công việc của mình, xác nhận cảnh báo, gửi phê duyệt.
4. M mở màn hình duyệt, thấy nội dung, 5.010 người khả dụng, phân tích loại trừ, chi phí ước tính; phê duyệt và phát sóng ngay.

**Kỳ vọng:** Tiêu đề thư khách hàng nhận có nhãn "[QC]"; chân thư và bản văn bản thuần có liên kết hủy nhận tin; sổ cái có 5.010 dòng, mỗi dòng có tham chiếu tới bằng chứng Đồng ý; khi tất cả có kết quả cuối cùng, chiến dịch Hoàn tất; báo cáo tính tỷ lệ đúng `BR-33.2`; lịch sử phê duyệt có hai bản ghi (gửi duyệt của A, phê duyệt của M). *(Kiểm chứng: BR-01.2, BR-06.2, BR-08.1, BR-12.2, BR-15.1, BR-17.2, BR-17.8, BR-23.1, BR-26.1, BR-30.2, BR-33.2)*

### Kịch bản 2: Người duyệt không được duyệt phần mình đã sửa

1. A gửi phê duyệt. M mở chiến dịch, sửa một câu, lưu (chiến dịch về Nháp), rồi gửi phê duyệt lại.
2. M tìm nút phê duyệt.

**Kỳ vọng:** M không phê duyệt được; M2 phê duyệt được. Nếu M2 đang mở màn hình duyệt mà A lưu một thay đổi, thao tác phê duyệt của M2 bị từ chối vì phiên bản đã đổi. *(BR-01.6, BR-17.2, BR-17.3)*

### Kịch bản 3: Từ chối phê duyệt và gửi lại

1. M từ chối chiến dịch của A với lý do "Sai mã giảm giá".
2. A sửa và gửi phê duyệt lại; M phê duyệt.

**Kỳ vọng:** Lần từ chối không nhập lý do bị chặn; A nhận thông báo kèm lý do; lịch sử phê duyệt có đủ 4 bản ghi. *(BR-17.5, BR-17.6, BR-17.7)*

### Kịch bản 4: Hủy nhận tin giữa lúc chiến dịch đang gửi

1. Chiến dịch Email 50.000 người, nhịp 10.000 tin/giờ, bắt đầu 9:00. Khách K ở cuối danh sách.
2. Lúc 9:10, K bấm liên kết hủy nhận tin trong một thư cũ, giữ lựa chọn mặc định.

**Kỳ vọng:** Trang xác nhận không yêu cầu đăng nhập; kênh email của K Từ chối nhận tin, các kênh khác không đổi; bằng chứng ghi nguồn và mã chiến dịch; K không nhận thư của chiến dịch đang gửi — sổ cái ghi "Bị loại trừ tại thời điểm gửi — Từ chối nhận tin"; chiến dịch Email tiếp theo loại K ngay từ bước xem trước. *(BR-08.4, BR-26.2, BR-26.4, NFR-05)*

### Kịch bản 5: Giới hạn tần suất giữa hai chiến dịch chạy song song

1. Giới hạn 2 tin / 7 ngày theo từng kênh. K đã nhận 1 email tiếp thị cách đây 3 ngày.
2. Chiến dịch Email X và Y cùng có K, cùng bắt đầu lúc 10:00.

**Kỳ vọng:** Đúng một trong hai chiến dịch gửi được cho K; chiến dịch còn lại ghi K "Chạm giới hạn tần suất". Chiến dịch SMS cùng ngày vẫn gửi được cho K. *(BR-08.5)*

### Kịch bản 6: Khung giờ yên lặng và nhịp gửi sau khi hết giờ

1. Chiến dịch SMS 30.000 người, hẹn 22:30, nhịp 5.000 tin/giờ.
2. Khách L trong danh sách hủy nhận tin SMS lúc 02:00.

**Kỳ vọng:** Không SMS nào gửi trước 08:00; từ 08:00 gửi theo nhịp 5.000 tin/giờ, không phát cùng lúc; L Bị loại trừ tại thời điểm gửi; chiến dịch Hoàn tất khi mọi tin bị hoãn đã có kết quả. *(BR-29.2, BR-29.3)*

### Kịch bản 7: Dự phòng Zalo ZNS → SMS

1. Chiến dịch Zalo ZNS 1.000 người, dự phòng SMS, nội dung SMS đã soạn riêng.
2. Nhà cung cấp giả lập: 50 người "không có tài khoản Zalo" (trong đó 5 người Từ chối nhận tin SMS), 10 người "đã chặn doanh nghiệp", 20 người Chưa xác định.

**Kỳ vọng:** Sau 5 phút, 45 người được gửi SMS; 5 người bị loại "Từ chối nhận tin" ở SMS; 10 người chặn doanh nghiệp không được gửi SMS và kênh Zalo ZNS của họ chuyển Từ chối nhận tin; 20 người Chưa xác định không được gửi SMS; báo cáo tách số liệu theo Zalo ZNS và SMS; mỗi người tính một tin cho giới hạn tần suất. *(BR-24.1, BR-37.2, BR-37.3, BR-37.5, BR-33.4)*

### Kịch bản 8: Tạm dừng, sửa liên kết, duyệt lại và tiếp tục

1. Chiến dịch Email 5.000 người đã gửi 1.000 thì M phát hiện liên kết ưu đãi lỗi, bấm Tạm dừng.
2. A sửa liên kết (phiên bản 2) — chiến dịch chuyển Tạm dừng — chờ duyệt phiên bản; M thử bấm Tiếp tục; M2 phê duyệt phiên bản 2; M bấm Tiếp tục.

**Kỳ vọng:** Trong vòng 1 phút sau khi tạm dừng không còn thư mới được chuyển đi; lần bấm Tiếp tục đầu tiên của M bị từ chối vì phiên bản 2 chưa được duyệt; sau khi M2 duyệt, chiến dịch về Tạm dừng với phiên bản 2 là phiên bản hiệu lực; sau khi tiếp tục, 4.000 người còn lại nhận phiên bản 2, không ai nhận hai thư; sổ cái ghi đúng phiên bản từng người đã nhận. *(BR-19.1, BR-19.2, BR-19.3, KPI-06)*

### Kịch bản 9: Tự động tạm dừng bảo vệ vì khiếu nại

1. Chiến dịch Email; nhà cung cấp giả lập tạo 0,4% khiếu nại sau 1.000 thư phân phát.

**Kỳ vọng:** Chiến dịch tự Tạm dừng với lý do và số liệu; người phụ trách, người duyệt và P nhận thông báo; mọi người khiếu nại chuyển Từ chối nhận tin email; M chỉ tiếp tục được khi nhập ghi chú giải trình; hệ thống không tự tiếp tục. *(BR-20.1, BR-20.2, BR-20.3, BR-27.1)*

### Kịch bản 10: Hạn mức gói không đủ và đình chỉ dịch vụ

1. Hạn mức gói còn 4.000 tin; M phát sóng chiến dịch 10.000 người.
2. B nâng trần hạn mức lên một mức mới cụ thể đủ cho lô theo quy trình tính phí; M phát sóng lại; giữa chừng Không gian làm việc bị đình chỉ dịch vụ, rồi thanh toán xong.

**Kỳ vọng:** Bước 1 không gửi tin nào, chiến dịch vẫn Đã duyệt, thông báo nêu hạn mức đang chạm và người xử lý được; bước 2 chiến dịch bắt đầu gửi sau xác nhận của B, tự Tạm dừng khi bị đình chỉ và **vẫn Tạm dừng** sau khi hết đình chỉ cho tới khi M bấm Tiếp tục. *(BR-17.8, BR-20.1, BR-20.2)*

### Kịch bản 11: Chiến dịch Tái tiếp cận

1. A tạo Chiến dịch Tái tiếp cận, phạm vi "Đã rời bỏ trong 12 tháng", thời hạn hiệu lực 7 ngày, gửi phê duyệt; M và S nhận thông báo; M phê duyệt phần Tái tiếp cận, S chưa duyệt.
2. S phê duyệt phần Tái tiếp cận; M phê duyệt phát sóng; phát sóng. Khách L trong phạm vi nhấp liên kết của chiến dịch.

**Kỳ vọng:** Bước 1 không phê duyệt phát sóng được (lỗi chặn "Chưa có phê duyệt của Quản lý Kinh doanh"); bước 2 chỉ khách Đã rời bỏ trong phạm vi và có Đồng ý có bằng chứng được gửi; khách rời bỏ 18 tháng bị loại; giai đoạn của L chuyển về Nurturing; sau 7 ngày, tin còn bị hoãn loại khách Đã rời bỏ như chiến dịch thường. *(BR-31.1, BR-31.2, BR-31.3, BR-31.4)*

### Kịch bản 12: Ghi nhận cơ hội chịu ảnh hưởng và chiều ngược

1. Khách cũ K nhấp liên kết chiến dịch C ngày 1. Ngày 10, S tạo cơ hội D có K tham gia, giá trị 500 triệu; ngày 20 D Thắng.
2. Ngày 40, D được mở lại và đóng Thua theo quy trình tái phân loại của phân hệ Cơ hội.

**Kỳ vọng:** Sau bước 1, báo cáo C có D trong "Cơ hội chịu ảnh hưởng" (không trong "có nguồn gốc") và doanh thu Thắng 500 triệu; sau bước 2 doanh thu Thắng giảm về 0; người không có quyền xem số liệu tài chính thấy số cơ hội nhưng không thấy giá trị. *(BR-34.1, BR-34.2, BR-34.3, BR-34.4)*

### Kịch bản 13: Tin trả lời về Hộp thư Hội thoại và từ khóa từ chối

1. Chiến dịch WhatsApp; K trả lời "Tôi muốn đặt 2 sản phẩm"; L trả lời "STOP".

**Kỳ vọng:** T thấy hội thoại của K kèm ngữ cảnh chiến dịch và nội dung K đã nhận; hội thoại được phân công theo quy tắc Hộp thư Hội thoại; kênh WhatsApp của L Từ chối nhận tin, L nhận tin xác nhận, T thấy tin "STOP" kèm nhãn; sổ cái ghi sự kiện Trả lời cho cả hai. *(BR-26.3, BR-35.1, BR-35.2)*

### Kịch bản 14: Yêu cầu xóa dữ liệu của người đã nhận nhiều chiến dịch

1. K đã nhận 3 chiến dịch (đã mở 2). Yêu cầu xóa dữ liệu của K được P chấp thuận ở phân hệ Khách hàng.

**Kỳ vọng:** Trong thời hạn xử lý của phân hệ Khách hàng, sổ cái của cả 3 chiến dịch không còn tìm được K; số liệu tổng hợp của 3 chiến dịch không đổi; phân hệ Chiến dịch báo hoàn tất phần việc của mình vào Biên bản Hoàn tất Xử lý. *(BR-32.2)*

### Kịch bản 15: Điểm đến dùng chung

1. Hồ sơ H1 (Đồng ý) và H2 (Từ chối nhận tin SMS) cùng một số điện thoại; hồ sơ H3, H4 cùng Đồng ý và cùng một email.
2. Gửi một chiến dịch SMS và một chiến dịch Email (lời chào "Chào {tên}") cùng tiêu chí.

**Kỳ vọng:** Số điện thoại của H1/H2 không nhận SMS; email của H3/H4 nhận đúng một thư với lời chào "Chào Quý khách". *(BR-08.3)*

### Kịch bản 16: Chuỗi nuôi dưỡng với thoát chuỗi và sửa chuỗi đang chạy

1. Chuỗi: B1 email → sau 2 ngày, nếu nhấp: B2 WhatsApp; nếu không: B3 email tiêu đề khác sau 3 ngày.
2. K nhấp B1; L không nhấp và Từ chối nhận tin email trước B3; trong lúc N đang chờ B3, A tạo phiên bản mới sửa nội dung B3; phiên bản này được M duyệt trước khi tới B3 của N.

**Kỳ vọng:** K nhận B2, không nhận B3; L thoát chuỗi (mọi bước còn lại là email); trong lúc phiên bản mới chờ duyệt chuỗi vẫn chạy phiên bản cũ; N nhận nội dung B3 mới. *(BR-39.1, BR-39.2, BR-39.5)*

### Kịch bản 17: Chiến dịch hẹn giờ bị Giữ lại, rồi dừng khẩn cấp giữa lúc gửi

1. Chiến dịch SMS 20.000 người, nhịp 5.000 tin/giờ, Đã duyệt hẹn 09:00; lúc 08:30 tài khoản gửi bị ngắt kết nối.
2. 10:00 tài khoản được kết nối lại; M bấm phát sóng. 10:30 Q kích hoạt dừng khẩn cấp vì một sự kiện truyền thông; 12:00 Q gỡ dừng khẩn cấp.

**Kỳ vọng:** Lúc 09:00 chiến dịch Giữ lại, phê duyệt còn nguyên, M và người duyệt nhận thông báo; 10:00 chiến dịch Đang gửi không cần duyệt lại; 10:30 chiến dịch Tạm dừng lý do "Dừng khẩn cấp" và không tin nào được chuyển đi quá 1 phút sau đó (khoảng 2.500 tin đã gửi); 12:00 sau khi gỡ, chiến dịch vẫn Tạm dừng cho tới khi M tiếp tục; thời gian dừng khẩn cấp không tính vào `CFG-CAMP-17`. *(BR-17.8, BR-18.3, BR-19.4, BR-42.1, BR-42.2)*

### Kịch bản 18: Chính sách gửi SMS theo cấu hình doanh nghiệp

1. Doanh nghiệp cấu hình: nhãn SMS "QC" (`CFG-CAMP-28`), giới hạn 1 tin / 24 giờ gộp theo số điện thoại (`CFG-CAMP-69`), Danh sách không quảng cáo bật cho SMS (`CFG-CAMP-70`), xét thêm theo giờ Không gian làm việc (`CFG-CAMP-71`), giới hạn tần suất 5 tin / 7 ngày (`CFG-CAMP-02`). Khách K có Đồng ý nhận SMS có bằng chứng; số của K thuộc Danh sách không quảng cáo. Khách L có Đồng ý, đã nhận 1 SMS tiếp thị của doanh nghiệp lúc 10:00 hôm qua. Khách N có Đồng ý, hồ sơ ghi múi giờ GMT+3.
2. Chiến dịch SMS gửi lúc 09:00 hôm nay tới K, L; một chiến dịch SMS khác gửi lúc 23:00 giờ Không gian làm việc tới N.
3. Chủ sở hữu tắt giới hạn 24 giờ, Người phụ trách Bảo vệ Dữ liệu cùng chấp thuận; một chiến dịch SMS mới gửi tới L lúc 11:00.

**Kỳ vọng:** K bị loại "Thuộc Danh sách không quảng cáo"; tin của L bị hoãn tới 10:01 rồi gửi (`CFG-CAMP-43` mặc định 2 giờ); tin của N không được gửi lúc 23:00 giờ Không gian làm việc dù giờ địa phương của N là 19:00, và được gửi lúc 12:00 giờ Không gian làm việc (08:00 GMT+3 — khi khung giờ yên lặng theo cả hai giờ đều đã hết); mọi tin có nhãn "QC" và hướng dẫn từ chối tới đầu số nhận từ chối; ở bước 3, L nhận tin lúc 11:00 và thay đổi cấu hình có trong nhật ký. *(BR-26.1, BR-29.1, BR-30.1, BR-30.2, BR-30.3, BR-30.5)*

### Kịch bản 19: Người khởi chạy bị thu hẹp quyền, mất quyền Phát sóng, bị tạm ngưng và rời workspace

1. Doanh nghiệp chọn "Thu về khách hàng của đơn vị mình" cho Marketing và Quản lý Marketing; A thuộc đơn vị Marketing — đơn vị Marketing phụ trách tổng 300 khách — chọn đối tượng khớp 5.000 khách toàn workspace và gửi phê duyệt.
2. G — vai trò tự tạo có (Khách hàng, Xem) = Toàn workspace và (Chiến dịch, Phát sóng) = Toàn workspace — duyệt và phát sóng ngay chiến dịch thứ hai do B (người chọn đối tượng có mức Xem Toàn workspace) soạn, 4.000 người nhận, nhịp 500 tin/giờ.
3. Giữa lúc gửi, ô (Khách hàng, Xem) của G bị điều chỉnh về Đơn vị của mình (1.000 khách); một giờ sau, ô (Chiến dịch, Phát sóng) của G bị đặt về Không có; sau đó G bị tạm ngưng.
4. M (có quyền Phát sóng chiến dịch) nhận làm người khởi chạy và tiếp tục; sau đó quy trình rời workspace được mở cho G (bỏ chọn "Tạm ngưng ngay" không áp dụng vì G đã tạm ngưng), trong khi M2 chốt giờ hẹn cho chiến dịch thứ ba do G phụ trách.

**Kỳ vọng:** Bước 1 tổng khớp tiêu chí là 300, màn hình nêu mức Xem đang áp dụng. Bước 3: sau lần điều chỉnh đầu, tin chưa gửi tới khách ngoài đơn vị của G ghi "Ngoài quyền của người khởi chạy", tin trong đơn vị vẫn gửi; khi G mất ô Phát sóng, chiến dịch **Tạm dừng ngay** với lý do "Người khởi chạy không còn quyền Phát sóng chiến dịch này", người phụ trách B, G, quản lý trực tiếp của G và Người có toàn quyền nhận thông báo; khi G bị tạm ngưng, chiến dịch vẫn Tạm dừng, không tự tiếp tục. Bước 4: từ lúc M nhận, mỗi tin xét theo tập khách hàng xem được của M; không tạo phiên bản mới. Quy trình rời workspace của G có bước bàn giao chiến dịch thứ ba (G là người phụ trách) nhưng không có bước tiến trình đang chạy cho chiến dịch đó (người khởi chạy là M2); không chiến dịch nào tự chuyển cho quản lý trực tiếp.

---

## 7. Giới hạn hiện tại & Vấn đề tồn đọng — Nhu cầu nghiệp vụ chưa chốt được phương án

Mục này chỉ chứa các nhu cầu **chưa quyết định được điều gì là đúng về mặt nghiệp vụ**. Mọi yêu cầu đã chốt đều nằm trong Mục 3.

1. **Gửi theo giờ địa phương hoặc giờ tối ưu của từng người nhận.** Chưa chốt cách tương tác với hạn ngạch ngày, với chốt danh sách và với thời gian đánh giá A/B.

2. **Ghi nhận doanh thu đa điểm chạm.** Thước đo "chịu ảnh hưởng" (`BR-34.1`) không cộng dồn được giữa các chiến dịch. Mô hình chia doanh thu cho nhiều chiến dịch chưa chốt, và mọi mô hình đều phải thống nhất với nguyên tắc điểm chạm đầu tiên của `deals-pipeline-srs.md`.

3. **Doanh thu từ đơn hàng (doanh nghiệp bán lẻ).** Doanh nghiệp bán lẻ ghi nhận doanh thu qua đơn hàng hoặc hệ thống bán hàng tại điểm bán, không qua cơ hội bán hàng. Chưa chốt nguồn dữ liệu đơn hàng và cách gắn đơn hàng với chiến dịch (qua tham số nguồn gốc, qua mã giảm giá, hay qua sự kiện gửi về từ hệ thống bán hàng).

4. **Nhóm đối chứng không nhận tin.** Để đo hiệu quả gia tăng thật, một phần tệp được giữ lại không gửi. Chưa chốt cách tính, cách báo cáo và cách tương tác với giới hạn tần suất.

5. **Phê duyệt nhiều cấp.** Một số ngành (dược, tài chính) cần thêm vòng duyệt pháp chế hoặc thương hiệu. Chưa chốt: số vòng cấu hình được, thứ tự, và quan hệ với phê duyệt kép hiện có.

6. **Trừ tiền trước từ số dư trả trước khi phát sóng.** Phụ thuộc `billing-subscription-srs.md`, nơi chưa có khái niệm số dư trả trước.

7. **Cộng điểm tương tác cho sự kiện mở.** (`contacts-srs.md`, `BR-15.2`) cộng điểm cho mỗi lần mở email, trong khi sự kiện mở là số gần đúng (`BR-33.3`). Phân hệ Chiến dịch đã không gửi các sự kiện mở tự động nhận diện được; việc có nên tiếp tục cộng điểm cho sự kiện mở hay không thuộc quyết định của chủ tài liệu `contacts-srs.md`.

8. **Đơn vị "người gửi" khi một doanh nghiệp có nhiều Không gian làm việc.** Giới hạn 24 giờ và giới hạn tần suất đang tính trong một Không gian làm việc; một doanh nghiệp vận hành nhiều Không gian làm việc có thể gửi nhiều tin tới cùng một số trong 24 giờ. Chưa chốt cách xác định cùng một pháp nhân gửi và cách đếm chung — cần chủ sản phẩm quyết định đơn vị "người gửi".

9. **Người nhận chưa thành niên.** Pháp luật bảo vệ dữ liệu cá nhân yêu cầu đồng ý của cha mẹ hoặc người giám hộ với trẻ em, và pháp luật quảng cáo hạn chế quảng cáo nhắm tới trẻ em. Chưa chốt cách phân hệ Khách hàng ghi nhận độ tuổi và tính hợp lệ của đồng ý, và lý do loại trừ tương ứng ở phân hệ này — cần chủ tài liệu `contacts-srs.md` quyết định.

10. **Doanh nghiệp không có người đóng vai Quản lý Marketing.** Phê duyệt phần Tái tiếp cận theo (`contacts-srs.md`, `BR-12.5b` (a)) cần đúng Quản lý Marketing, và Quản trị viên, Chủ sở hữu không thay được. Chưa chốt cách nhận diện "Quản lý Marketing" trong một doanh nghiệp tự dựng vai trò (ví dụ người có quyền Phát sóng chiến dịch trên đơn vị Marketing), và đường thay thế cho doanh nghiệp rất nhỏ — cần chủ tài liệu `contacts-srs.md` quyết định.

11. **Chính sách gửi tiếp thị theo thị trường của điểm đến.** Chính sách gửi tiếp thị (`BR-30.1`) hiện đặt một lần cho cả Không gian làm việc; doanh nghiệp gửi tới nhiều thị trường phải áp cấu hình chặt nhất cho mọi người nhận. Chưa chốt: đặt chính sách theo quốc gia hay vùng của điểm đến, nhãn quảng cáo theo ngôn ngữ, khung giờ yên lặng theo từng kênh, ngày không gửi lặp theo tuần — và cách xác định thị trường của một điểm đến khi hồ sơ không ghi rõ.


---

## Phụ lục A — Danh mục Khái niệm Nghiệp vụ

> Phụ lục này mô tả các khái niệm nghiệp vụ và thông tin cần lưu cho từng khái niệm, **không phải thiết kế dữ liệu và không mang tính ràng buộc**. Cách tổ chức lưu trữ thực tế thuộc thẩm quyền đội phát triển.

| Khái niệm nghiệp vụ | Thông tin nghiệp vụ cần có |
| --- | --- |
| **Chiến dịch** | Mã định danh, tên, mô tả nội bộ, kênh chính, trạng thái (Mục 2.5), người phụ trách, đơn vị tổ chức, tài khoản gửi, tiêu chí đối tượng và tập loại trừ, các phiên bản nội dung, cấu hình dự phòng, nhịp gửi, giờ hẹn, ngân sách, cờ miễn trừ tần suất kèm lý do, cờ Tái tiếp cận kèm phạm vi và thời hạn hiệu lực, thời điểm bắt đầu gửi, thời điểm hoàn tất/hủy, lý do hủy. |
| **Thông báo dịch vụ** | Chiến dịch loại Thông báo dịch vụ kèm mục đích (một trong năm mục đích của `BR-45.1`), văn bản pháp luật dẫn chiếu (mục đích (4)), lý do chọn tập đối tượng, phê duyệt bảo vệ dữ liệu, xác nhận gửi khẩn cấp. |
| **Danh mục từ chỉ tiếp thị** | Từ, nguồn (hệ thống hay doanh nghiệp thêm theo `CFG-CAMP-68`). |
| **Danh mục từ khuyến mãi** | Từ, mức (chặn hay cảnh báo), nguồn (hệ thống hay doanh nghiệp thêm). |
| **Từ chối Thông báo dịch vụ không thiết yếu** | Điểm đến, kênh, thời điểm, nguồn (cơ chế phản đối, tin trả lời, yêu cầu của khách), người ghi nhận; lịch sử khôi phục kèm bằng chứng. |
| **Phiên bản chính sách gửi tiếp thị** | Số phiên bản, giá trị các tham số của `BR-30.1`, người đề xuất, lý do, người chấp thuận, thời điểm hiệu lực. |
| **Phiên bản chiến dịch** | Số thứ tự phiên bản, nội dung từng kênh và từng phiên bản A/B, người soạn, thời điểm tạo; là đơn vị được phê duyệt. |
| **Lịch sử phê duyệt** | Chiến dịch, phiên bản, loại thao tác (gửi duyệt / phê duyệt / từ chối / rút lại / phê duyệt Tái tiếp cận), người thực hiện, thời điểm, lý do. |
| **Danh sách người nhận chốt & Sổ cái** | Theo `BR-23.1`: hồ sơ khách hàng, điểm đến, tham chiếu bằng chứng đồng ý, kênh thực tế, phiên bản đã nhận, trạng thái phân phát (`BR-23.2`), lịch sử các lần thử, sự kiện tương tác, lý do loại trừ hoặc nhóm lỗi và mã lỗi gốc. |
| **Tài khoản gửi** | Kênh, tên hiển thị, định danh người gửi, trạng thái hoạt động, trạng thái xác thực tên miền (email), ngưỡng an toàn, liên kết tới tài khoản kênh của Hộp thư Hội thoại (WhatsApp/Zalo). |
| **Tin nhắn mẫu** | Tài khoản kênh, tên mẫu, loại mẫu theo nền tảng, trạng thái theo nền tảng, các vị trí tham số. |
| **Chuỗi nuôi dưỡng** | Trạng thái (`BR-39.1`), các phiên bản, điều kiện vào, các bước và nhánh, điều kiện thoát, chính sách vào lại, vị trí hiện tại của từng người trong chuỗi. |
| **Danh sách người nhận gửi thử** | Địa chỉ nội bộ được phép nhận gửi thử, người thêm, thời điểm thêm. |
| **Danh sách tên miền doanh nghiệp** | Tên miền được gắn tham số nguồn gốc tự động (`BR-14.2`). |
| **Bảng đơn giá kênh** | Kênh, nhà mạng hoặc nền tảng, loại tin, căn cứ tính phí, đơn giá, đồng tiền, thời điểm hiệu lực. |
| **Danh mục từ khóa từ chối** | Từ khóa mặc định (không xóa được) và từ khóa do Quản trị viên thêm. |
| **Đầu số nhận từ chối** | Đầu số hai chiều gắn với tên thương hiệu SMS, nhận tin từ chối của người nhận. |
| **Tên thương hiệu SMS** | Tên, loại (Quảng cáo / Chăm sóc khách hàng), đầu số nhận từ chối, trạng thái đăng ký theo từng nhà mạng. |
| **Trạng thái duyệt nội dung SMS** | Nội dung, nhà mạng, trạng thái duyệt, thời điểm. |
| **Dấu vết chặn gửi** | Dạng biến đổi một chiều có khóa bí mật của một điểm đến, kèm loại kênh và thời điểm tạo; không gắn thông tin nào khác (`BR-32.4`). |
| **Danh sách không quảng cáo** | Điểm đến (số điện thoại, email hay tài khoản), nguồn (tệp doanh nghiệp nạp hoặc nguồn bên ngoài doanh nghiệp kết nối), thời điểm cập nhật (`BR-30.5`). |
| **Lịch ngày không gửi** | Khoảng thời gian, lý do, người khai báo (`FEAT-43`). |
| **Dừng khẩn cấp** | Trạng thái bật/tắt, người kích hoạt, lý do, thời điểm, người gỡ (`FEAT-42`). |

---

## Phụ lục B — Danh mục Tham số Cấu hình theo Không gian làm việc

### B.1 Tham số cấu hình

Dấu "+" trong cột Thẩm quyền nghĩa là **cần cả hai** người cùng chấp thuận, cùng quy ước với `contacts-srs.md`; khi người thứ hai vắng mặt, áp quy tắc người thay thế của (`contacts-srs.md`, `NFR-14`). Thay đổi một tham số có hiệu lực cho **mọi lượt gửi bắt đầu sau thời điểm thay đổi**, kể cả tin đang bị hoãn; riêng các tham số của chính sách gửi tiếp thị (`BR-30.1`), thay đổi chặt hơn có hiệu lực ngay với mọi tin chưa gửi của chiến dịch và chuỗi đang chạy, thay đổi lỏng hơn chỉ áp cho lượt gửi bắt đầu sau thời điểm thay đổi. Không hồi tố lượt gửi đã diễn ra.

| Mã tham số | Quy tắc liên quan | Nội dung cấu hình | Mặc định chuẩn hệ thống | Miền giá trị | Thẩm quyền thay đổi | Mức độ tự do |
| --- | --- | --- | --- | --- | --- | --- |
| `CFG-CAMP-01` | `BR-01.1` | Kênh chính mặc định khi tạo chiến dịch | Email | Email / WhatsApp / Zalo ZNS / Zalo OA / SMS | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-02` | `BR-08.5` | Số tin tiếp thị tối đa một người nhận trong cửa sổ tần suất | 2 | 1–20 | Quản trị viên | **Tự do** — giới hạn 24 giờ theo điểm đến (`BR-30.3`) áp dụng độc lập |
| `CFG-CAMP-03` | `BR-08.5` | Độ dài cửa sổ tần suất (khoảng trượt) | 7 ngày | 1–30 ngày | Quản trị viên | **Tự do** |
| `CFG-CAMP-04` | `BR-08.5` | Phạm vi đếm tần suất | Theo từng kênh | Theo từng kênh / Gộp mọi kênh | Quản trị viên | **Tự do** |
| `CFG-CAMP-05` | `BR-17.2` | Bắt buộc phê duyệt kép | Bật | Bật / Tắt | Chủ sở hữu | **Tự do** |
| `CFG-CAMP-06` | `BR-29.1` | Khung giờ yên lặng (bắt đầu – kết thúc, theo giờ địa phương người nhận) | 22:00 – 08:00 | Khoảng bất kỳ, tối đa 14 giờ | Chủ sở hữu + Người phụ trách Bảo vệ Dữ liệu | **Tự do** — doanh nghiệp chịu trách nhiệm (`BR-30.1`) |
| `CFG-CAMP-07` | `BR-29.1` | Kênh áp dụng khung giờ yên lặng | SMS, WhatsApp, Zalo ZNS, Zalo OA | Tập con bất kỳ của 5 kênh | Chủ sở hữu + Người phụ trách Bảo vệ Dữ liệu | **Tự do** — doanh nghiệp chịu trách nhiệm (`BR-30.1`) |
| `CFG-CAMP-08` | `BR-37.4` | Thời gian chờ trước khi gửi dự phòng | 5 phút | 0–1.440 phút | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-09` | `BR-40.1` | Hạn ngạch tin tiếp thị mỗi ngày, theo từng kênh | 50.000 tin / kênh / ngày | 0 (không giới hạn thêm) hoặc từ 100 trở lên | Quản trị viên; đặt về 0 (tắt hạn ngạch) cần Chủ sở hữu | **Tự do** |
| `CFG-CAMP-10` | `BR-34.1` | Cửa sổ ghi nhận cơ hội chịu ảnh hưởng | 30 ngày | 1–180 ngày | Người có quyền Cấu hình nhịp độ tiếp thị; đặt quá 90 ngày cần Người phụ trách Bảo vệ Dữ liệu cùng chấp thuận | **Tự do** |
| `CFG-CAMP-11` | `BR-28.2` | Số chiến dịch liên tiếp có lỗi hộp thư tạm thời trước khi tạm ngừng gửi tiếp thị tới điểm đến | 3 | 2–10 | Quản trị viên | **Tự do** |
| `CFG-CAMP-12` | `BR-20.1`, `BR-27.4` | Ngưỡng tỷ lệ khiếu nại tự động tạm dừng | 0,2% | 0,05% – 0,3% | Quản trị viên + Người phụ trách Bảo vệ Dữ liệu | **Có sàn bắt buộc** — không tắt được, không đặt cao hơn 0,3% |
| `CFG-CAMP-13` | `BR-20.1` | Ngưỡng tỷ lệ hỏng vĩnh viễn tự động tạm dừng | 5% | 1% – 10% | Quản trị viên + Người phụ trách Bảo vệ Dữ liệu | **Có sàn bắt buộc** — không tắt được, không đặt cao hơn 10% |
| `CFG-CAMP-14` | `BR-20.1` | Cỡ mẫu tối thiểu trước khi xét hai ngưỡng trên | 500 tin | 100 – 2.000 tin | Quản trị viên + Người phụ trách Bảo vệ Dữ liệu | **Có sàn bắt buộc** — không đặt cao hơn 2.000 |
| `CFG-CAMP-15` | `BR-25.1` | Số lần thử lại tự động cho lỗi tạm thời | 3 | 1–5 | Quản trị viên | **Tự do** |
| `CFG-CAMP-16` | `BR-25.2` | Thời hạn cho phép gửi lại thủ công | 72 giờ | 0–168 giờ | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-17` | `BR-19.4` | Thời gian tạm dừng liên tục tối đa trước khi tự hủy | 14 ngày | 1–30 ngày | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-18` | `BR-15.1` | Số người nhận tối đa mỗi lượt gửi thử | 5 | 1–10 | Quản trị viên | **Có sàn bắt buộc** — trần tuyệt đối 10 |
| `CFG-CAMP-19` | `BR-14.2` | Tự động gắn tham số nguồn gốc vào liên kết tới tên miền doanh nghiệp | Bật | Bật / Tắt | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-20` | `BR-32.3` | Thời hạn lưu chi tiết sổ cái theo từng người | 24 tháng | 1–60 tháng | Chủ sở hữu + Người phụ trách Bảo vệ Dữ liệu | **Tự do** — đặt dưới 12 tháng thì màn hình cảnh báo trước khi lưu |
| `CFG-CAMP-21` | `BR-04.2` | Thời hạn lưu chiến dịch trong Thùng rác | 30 ngày | 30–90 ngày | Chủ sở hữu | **Có sàn bắt buộc** — không dưới 30 ngày |
| `CFG-CAMP-22` | `BR-06.4` | Ngưỡng chênh lệch số người nhận khả dụng giữa lúc duyệt và lúc chốt | 20% | 5%–100% | Quản trị viên | **Tự do** |
| `CFG-CAMP-23` | `BR-08.3` | Cách xử lý điểm đến dùng chung | Gửi một lần, chặt nhất thắng | Gửi một lần, chặt nhất thắng / Loại hẳn điểm đến mang nhãn Định danh dùng chung | Quản trị viên | **Tự do** |
| `CFG-CAMP-24` | `BR-38.1` | Tỷ lệ tệp thử nghiệm A/B | 20% | 5%–50% | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-25` | `BR-38.1` | Thời gian đánh giá A/B | 4 giờ | 1–72 giờ | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-26` | `BR-38.1`, `BR-33.3` | Tiêu chí chọn phiên bản thắng mặc định | Tỷ lệ nhấp | Tỷ lệ nhấp / Tỷ lệ trả lời *(tỷ lệ mở không được dùng — `BR-33.3`)* | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-27` | `BR-22.1` | Nhịp gửi mặc định của chiến dịch mới | 5.000 tin/giờ | 100 tin/giờ tới ngưỡng an toàn của tài khoản gửi | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-28` | `BR-30.2` | Nhãn quảng cáo theo từng kênh (bật/tắt và nội dung nhãn) | SMS: "QC" đầu tin; Email: "[QC]" đầu tiêu đề; Zalo OA: bật; WhatsApp, Zalo ZNS: bật (mẫu phải chứa nhãn) | Bật/tắt từng kênh; nội dung nhãn tự do | Chủ sở hữu + Người phụ trách Bảo vệ Dữ liệu | **Tự do** — doanh nghiệp chịu trách nhiệm (`BR-30.1`) |
| `CFG-CAMP-29` | `BR-18.1` | Khoảng tối thiểu giữa lúc đặt và giờ hẹn | 15 phút | 15 phút – 24 giờ | Người có quyền Cấu hình nhịp độ tiếp thị | **Có sàn bắt buộc** — không dưới 15 phút |
| `CFG-CAMP-30` | `BR-18.1` | Khoảng hẹn giờ tối đa | 12 tháng | 1–24 tháng | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-31` | `BR-18.2` | Cửa sổ dời giờ hẹn muộn hơn không cần duyệt lại | 2 giờ | 0–24 giờ (0 nghĩa là mọi lần dời đều phải duyệt lại) | Quản trị viên | **Tự do** |
| `CFG-CAMP-32` | `BR-18.3`, `BR-06.4` | Thời gian ân hạn của trạng thái Giữ lại | 24 giờ | 1–72 giờ | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-33` | `BR-19.4` | Thời gian nhắc trước khi tự hủy chiến dịch tạm dừng quá lâu | 24 giờ | 1–72 giờ | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-34` | `BR-16.2` | Ngưỡng cảnh báo số đơn vị tin SMS | 3 | 1–10 | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-35` | `BR-16.2` | Ngưỡng cảnh báo tỷ lệ bị loại vì Không tiếp cận được | 10% | 1%–50% | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-36` | `BR-16.2` | Ngưỡng cảnh báo chi phí ước tính so với ngân sách còn lại | 80% | 50%–100% | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-37` | `BR-38.2` | Cỡ mẫu khuyến nghị cho mỗi phiên bản A/B | 1.000 người | 100–10.000 người | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-38` | `BR-38.3` | Thời gian chờ chọn thủ công phiên bản A/B trước khi dùng phiên bản A | 24 giờ | 1–72 giờ | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-39` | `BR-27.3` | Ngưỡng cảnh báo tỷ lệ khiếu nại của Không gian làm việc (tính trên khoảng trượt tại B.2) | 0,1% | 0,05% – 0,3% | Quản trị viên + Người phụ trách Bảo vệ Dữ liệu | **Có sàn bắt buộc** — không tắt được, không đặt cao hơn 0,3% |
| `CFG-CAMP-40` | `BR-40.2` | Cách xử lý mặc định khi vượt hạn ngạch ngày | Dàn trải | Dàn trải / Chờ | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-41` | `BR-22.5` | Lịch làm nóng tài khoản gửi mới | Ngày đầu 1.000 tin, nhân đôi mỗi ngày tới ngưỡng an toàn | Khối lượng ngày đầu 100–10.000 tin; hệ số tăng 1,2–3 lần mỗi ngày | Quản trị viên; miễn trừ cho một tài khoản cần Quản trị viên + Người phụ trách Bảo vệ Dữ liệu | **Có sàn bắt buộc** — không tắt được với tài khoản gửi email và tên miền gửi mới, trừ miễn trừ có bằng chứng theo `BR-22.5` |
| `CFG-CAMP-42` | `BR-15.1` | Số lượt gửi thử tối đa mỗi chiến dịch mỗi ngày | 20 | 1–50 | Quản trị viên | **Có sàn bắt buộc** — trần tuyệt đối 50 |
| `CFG-CAMP-43` | `BR-30.3` | Thời gian hoãn tối đa trong chiến dịch khi người nhận chạm giới hạn 24 giờ theo điểm đến | 2 giờ | 0–12 giờ (0 nghĩa là loại ngay) | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-44` | `BR-26.3` | Thời hạn tư vấn viên xử lý tin có thể là lời từ chối trước khi leo thang cảnh báo | 12 giờ | 1–24 giờ | Quản trị viên + Người phụ trách Bảo vệ Dữ liệu | **Có sàn bắt buộc** — không quá 24 giờ |
| `CFG-CAMP-45` | `BR-32.3`, `BR-14.1` | Thời hạn lưu sự kiện mở/nhấp gắn danh tính trong sổ cái (ngắn hơn phần bằng chứng gửi tin) | 12 tháng | 1–24 tháng, không dài hơn `CFG-CAMP-20` | Chủ sở hữu + Người phụ trách Bảo vệ Dữ liệu | **Có sàn bắt buộc** — trần 24 tháng |
| `CFG-CAMP-46` | `BR-43.1` | Độ dài tối đa của một khoảng không gửi liên tục trước khi cần Chủ sở hữu chấp thuận | 7 ngày | 1–30 ngày | Chủ sở hữu | **Tự do** |
| `CFG-CAMP-47` | `BR-28.2` | Thời gian tạm ngừng gửi tiếp thị tới điểm đến có lỗi hộp thư tạm thời lặp lại | 90 ngày | 30–365 ngày | Quản trị viên | **Tự do** |
| `CFG-CAMP-48` | `BR-37.1` | Số kênh dự phòng tối đa trong một chiến dịch | 2 | 1–3 | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-49` | `BR-37.6` | Thời gian giữ chờ đợt dự phòng thiếu hạn mức gói trước khi bỏ | 2 giờ | 15 phút – 24 giờ | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-50` | `BR-39.1` | Chu kỳ nhắc người phụ trách khi chuỗi nuôi dưỡng còn tạm dừng | 7 ngày | 1–30 ngày | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-51` | `BR-14.3` | Chu kỳ nhắc người phụ trách quyết định giai đoạn của khách bị tạm loại do tương tác bị nhận diện lại là tự động | 7 ngày | 1–30 ngày | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-52` | `BR-15.1` | Số tin gửi thử tối đa một người nhận gửi thử nhận mỗi ngày trên toàn Không gian làm việc | 50 | 5–200 | Quản trị viên | **Có sàn bắt buộc** — trần tuyệt đối 200 |
| `CFG-CAMP-53` | `BR-35.1`, `BR-35.5` | Nơi nhận công việc liên hệ lại từ tin trả lời qua kênh không có tiếp nhận hội thoại | Người phụ trách khách hàng | Người phụ trách khách hàng / Hàng đợi của đơn vị tiếp nhận | Người có toàn quyền *(đổi tham số làm đổi ai thấy công việc)* | **Tự do** |
| `CFG-CAMP-54` | `BR-40.4`, `BR-40.2`, `BR-16.1` | Tỷ lệ tối đa của hạn ngạch ngày mà một chiến dịch bất kỳ (Dàn trải hay Chờ) được giữ chỗ và gửi thực trong một ngày | 70% | 30%–100% | Quản trị viên | **Tự do** |
| `CFG-CAMP-55` | `BR-40.4`, `BR-40.2`, `BR-16.1`, `BR-39.4` | Tỷ lệ tối đa của hạn ngạch ngày mà tổng giữ chỗ của mọi chiến dịch được chiếm trong một ngày; đặt 100% nghĩa là không còn phần luôn dành cho chuỗi nuôi dưỡng, lượt gửi lại và dự phòng | 90% | 50%–100% | Quản trị viên | **Tự do** |
| `CFG-CAMP-56` | `BR-07.1` | Số người tối đa trong mỗi mẫu xem người nhận | 20 | 5–50 | Quản trị viên | **Có sàn bắt buộc** — trần 50, vì mẫu là cửa sổ xem dữ liệu cá nhân của từng người |
| `CFG-CAMP-57` | `BR-38.3` | Mức tin cậy tối thiểu để hệ thống tự chọn phiên bản thắng trong thử nghiệm A/B | 95% | 90%–99% | Người có quyền Cấu hình nhịp độ tiếp thị | **Có sàn bắt buộc** — không thấp hơn 90%, dưới mức này hệ thống tuyên bố kết luận sai quá thường xuyên |
| `CFG-CAMP-58` | `BR-26.3` | Thời gian kể từ lượt gửi chiến dịch gần nhất để một tin đến được coi là tin trả lời chiến dịch (chỉ quyết định việc tự động ghi nhận lời từ chối) | 72 giờ | 24–168 giờ | Quản trị viên + Người phụ trách Bảo vệ Dữ liệu | **Tự do** — tin ngoài khoảng này vẫn đi luồng cần người xác nhận |
| `CFG-CAMP-59` | `BR-26.3` | Khoảng hoạt động gần nhất của tư vấn viên để coi một hội thoại là "đang có tư vấn viên xử lý" | 4 giờ | 1–24 giờ | Quản trị viên + Người phụ trách Bảo vệ Dữ liệu | **Tự do** |
| `CFG-CAMP-60` | `BR-18.3` | Khoảng nhắc lại trước khi hết thời gian ân hạn Giữ lại | 2 giờ | 1–24 giờ | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-61` | `BR-39.4` | Khoảng hoãn vì hạn ngạch trước khi báo người phụ trách chuỗi nuôi dưỡng | 24 giờ | 1–72 giờ | Người có quyền Cấu hình nhịp độ tiếp thị | **Tự do** |
| `CFG-CAMP-62` | `BR-39.4` | Khoảng thời gian miễn trừ tần suất của chuỗi nuôi dưỡng áp cho một người, kể từ lần vào chuỗi đầu tiên | 14 ngày | 1–30 ngày | Quản trị viên | **Có sàn bắt buộc** — trần 30 ngày; chuỗi dài hơn phải tôn trọng tần suất chung |
| `CFG-CAMP-63` | `BR-45.1` | Các mục đích Thông báo dịch vụ được bật trong Không gian làm việc | Cả năm mục đích | Bật hoặc tắt từng mục đích trong danh mục; không thêm mục đích mới | Quản trị viên + Người phụ trách Bảo vệ Dữ liệu | **Có sàn bắt buộc** — không thêm mục đích ngoài danh mục |
| `CFG-CAMP-64` | `BR-45.5` | Số Thông báo dịch vụ tối đa tới một người, gộp mọi kênh, trong một khoảng trượt (thông báo mục đích (2), (4) được đếm nhưng không bao giờ bị chặn) | 4 thông báo / 30 ngày | 1–30 thông báo; khoảng 7–90 ngày | Quản trị viên + Người phụ trách Bảo vệ Dữ liệu | **Tự do** — vượt 4 thông báo trong 30 ngày thì màn hình cảnh báo trước khi lưu |
| `CFG-CAMP-65` | `BR-45.5` | Các mục đích Thông báo dịch vụ được phép gửi trong khung giờ yên lặng | Không mục đích nào | Chọn trong (1), (2), (4) | Chủ sở hữu + Người phụ trách Bảo vệ Dữ liệu | **Có sàn bắt buộc** — không gồm mục đích (3), (5) |
| `CFG-CAMP-66` | `BR-45.4` | Giai đoạn vòng đời được nhận Thông báo dịch vụ theo từng mục đích | (1), (3), (5): Customer, Evangelist; (2), (4): thêm Churned | Chọn trong Customer, Evangelist, Churned cho từng mục đích | Quản trị viên + Người phụ trách Bảo vệ Dữ liệu | **Có sàn bắt buộc** — chỉ gồm giai đoạn đã từng là khách hàng; không bao giờ gồm Subscriber, Lead, MQL, SQL, Opportunity, Nurturing, Disqualified, hồ sơ khách hàng tạm |
| `CFG-CAMP-67` | `BR-45.2` | Từ khuyến mãi doanh nghiệp thêm vào danh mục của hệ thống, kèm mức chặn hay cảnh báo | Trống | Thêm từ; không xóa hay hạ mức từ mặc định | Quản trị viên | **Có sàn bắt buộc** — chỉ thêm theo hướng chặt hơn |
| `CFG-CAMP-68` | `BR-26.3` | Từ chỉ tiếp thị doanh nghiệp thêm vào danh mục của hệ thống | Trống | Thêm từ; không xóa từ mặc định | Quản trị viên | **Có sàn bắt buộc** — chỉ thêm theo hướng chặt hơn |
| `CFG-CAMP-69` | `BR-30.3` | Giới hạn 24 giờ theo điểm đến: bật/tắt, số tin tối đa, đơn vị đếm | Bật; 1 tin / 24 giờ; gộp các kênh theo số điện thoại (Email, Zalo OA theo từng kênh) | Tắt, hoặc 1–10 tin; gộp theo số điện thoại hay theo từng kênh | Chủ sở hữu + Người phụ trách Bảo vệ Dữ liệu | **Tự do** — doanh nghiệp chịu trách nhiệm (`BR-30.1`) |
| `CFG-CAMP-70` | `BR-30.5` | Danh sách không quảng cáo: bật/tắt, nguồn danh sách, kênh áp dụng, tuổi tối đa của bản đang dùng | Tắt; khi bật mặc định áp cho mọi kênh gửi theo số điện thoại, tuổi tối đa 48 giờ | Nguồn: tệp doanh nghiệp nạp hoặc nguồn bên ngoài doanh nghiệp kết nối; kênh: tập con bất kỳ; tuổi tối đa 1–168 giờ | Chủ sở hữu + Người phụ trách Bảo vệ Dữ liệu | **Tự do** — doanh nghiệp chịu trách nhiệm (`BR-30.1`) |
| `CFG-CAMP-71` | `BR-29.1` | Xét thêm khung giờ yên lặng theo giờ của Không gian làm việc | Bật | Bật / Tắt | Chủ sở hữu + Người phụ trách Bảo vệ Dữ liệu | **Tự do** — doanh nghiệp chịu trách nhiệm (`BR-30.1`) |
| `CFG-CAMP-72` | `BR-09.3` | Yêu cầu xác nhận qua chính điểm đến cho Đồng ý thu qua biểu mẫu | Bật | Bật / Tắt | Chủ sở hữu + Người phụ trách Bảo vệ Dữ liệu | **Tự do** — doanh nghiệp chịu trách nhiệm (`BR-30.1`) |
| `CFG-CAMP-73` | `BR-14.4` | Yêu cầu điều khoản đồng thuận nêu mục đích đo lường trước khi theo dõi hành vi gắn danh tính | Bật | Bật / Tắt | Chủ sở hữu + Người phụ trách Bảo vệ Dữ liệu | **Tự do** — doanh nghiệp chịu trách nhiệm (`BR-30.1`) |
| `CFG-CAMP-74` | `BR-08.1` | Kênh yêu cầu Đồng ý nhận tin có bằng chứng trước khi gửi tiếp thị (lý do 6) | Cả 5 kênh | Tập con bất kỳ của 5 kênh | Chủ sở hữu + Người phụ trách Bảo vệ Dữ liệu | **Tự do** — doanh nghiệp chịu trách nhiệm (`BR-30.1`) |

*Ghi chú về thẩm quyền:* Thẩm quyền đổi tham số là **một trục quyền riêng**, không suy ra được từ các ô trên Chiến dịch tại Mục 5. "Quản trị viên" và "Chủ sở hữu" trong cột Thẩm quyền là cấp bậc thành viên (Quản trị viên nghĩa là Người có toàn quyền); không tham số nào gắn thẩm quyền với tên một vai trò. Tham số điều chỉnh **nhịp độ và thói quen tiếp thị thường nhật** (`01`, `08`, `10`, `16`, `17`, `19`, `24`–`27`, `29`, `30`, `32`–`38`, `40`) thuộc người có quyền quản trị **Cấu hình nhịp độ tiếp thị** (Mục 5, ghi chú 2). Tham số **ràng buộc quy trình và bảo vệ khách hàng** (`02`–`04`, `09`, `11`, `15`, `18`, `22`, `23`, `31`, `41`, `42`, `54`, `55`, `56`, `62`) thuộc Quản trị viên. `57`, `60`, `61` thuộc người có quyền quản trị **Cấu hình nhịp độ tiếp thị** (Mục 5, ghi chú 2). `67`, `68` thuộc Quản trị viên. `58`, `59`, `63`, `64`, `66` cần Quản trị viên và Người phụ trách Bảo vệ Dữ liệu cùng chấp thuận; `65` cần Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu. `43`, `48`–`51` thuộc người có quyền quản trị **Cấu hình nhịp độ tiếp thị** (Mục 5, ghi chú 2); `52`, `53` thuộc Quản trị viên (`53` đổi ai thấy công việc liên hệ lại nên thuộc Người có toàn quyền); `47` thuộc Quản trị viên. Tham số **tắt một cơ chế kiểm soát hoặc chạm tới khả năng mất dữ liệu** (`05`, `21`, `46`) thuộc Chủ sở hữu. Tham số **chạm tới tuân thủ và danh tiếng gửi** (`12`–`14`, `20`, `39`, `44`, `45`) cần Người phụ trách Bảo vệ Dữ liệu cùng chấp thuận. Khi doanh nghiệp không chỉ định Người phụ trách Bảo vệ Dữ liệu, mọi chỗ cần người này cùng chấp thuận áp quy tắc thay thế người thứ hai của (`contacts-srs.md`, `NFR-14`), để hai người không sụp về một.

*Ghi chú về `CFG-CAMP-05`:* Tắt phê duyệt kép là quyết định có ý thức của Chủ sở hữu, dành cho doanh nghiệp chỉ có một người làm tiếp thị. Mọi chiến dịch phát sóng khi tham số này tắt đều được ghi nhật ký là "phát sóng không qua phê duyệt kép" (`BR-17.2`).

*Ghi chú về `CFG-CAMP-12`–`14`, `39`:* Không cho tắt vì đây là cơ chế duy nhất ngăn một chiến dịch tiếp tục gây hại khi không ai đang theo dõi. Trần 0,3% vì đó là ngưỡng mà các nhà cung cấp hộp thư lớn bắt đầu chặn người gửi; trần cỡ mẫu để không thể vô hiệu hóa cơ chế một cách gián tiếp bằng cỡ mẫu quá lớn.

*Ghi chú về `CFG-CAMP-20`, `CFG-CAMP-64`:* Hai tham số là lựa chọn của doanh nghiệp, hệ thống không khóa giá trị theo quy định của quốc gia nào. `CFG-CAMP-20` dưới 12 tháng: cảnh báo doanh nghiệp có thể không còn sổ cái chi tiết để đối chiếu khi khách khiếu nại hay nhà cung cấp tranh chấp cước cho các chiến dịch trong năm gần nhất; trần 60 tháng để không giữ dữ liệu cá nhân quá mục đích đối soát. `CFG-CAMP-64` vượt 4 thông báo trong 30 ngày: cảnh báo thông báo dịch vụ dày đặc dễ bị người nhận coi là tin rác và báo khiếu nại, làm hỏng danh tiếng của tài khoản gửi dùng chung với chiến dịch tiếp thị. Ánh xạ liên kết hủy nhận tin không chịu thời hạn của `CFG-CAMP-20` (`BR-26.2`).

*Ghi chú về chính sách gửi tiếp thị:* `06`, `07`, `28`, `69`–`74` thuộc chính sách gửi tiếp thị của doanh nghiệp (`BR-30.1`): cần Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu cùng chấp thuận; hệ thống không khóa giá trị nào theo quốc gia; doanh nghiệp chịu trách nhiệm về việc cấu hình phù hợp pháp luật áp dụng cho mình.

### B.2 Hằng số vận hành cấp hệ thống

Các giá trị sau áp dụng thống nhất cho mọi Không gian làm việc và không đưa vào bảng tham số theo tenant, vì chúng là **giới hạn thiết kế của tính năng**, **chuẩn thống kê**, hoặc **tiêu chí kỹ thuật bảo vệ tài khoản gửi dùng chung** — không phải một lựa chọn nghiệp vụ khác nhau giữa các doanh nghiệp. Mọi hằng số đều **Cố định** với lý do nêu ở cột cuối.

| Hằng số | Giá trị | Quy tắc | Lý do không cấu hình |
| --- | --- | --- | --- |
| Số phiên bản A/B | 2–4 | `BR-38.1` | Nhiều hơn làm mỗi phiên bản quá ít người để có kết luận |
| Khoảng cách giữa các lần thử lại tự động | 5 phút, 30 phút, 2 giờ, 6 giờ, 12 giờ | `BR-25.1` | Theo khuyến nghị của nhà cung cấp; thử dồn dập bị coi là gửi rác |
| Tiêu chí nhận diện lượt nhấp tự động | Email: nhấp trong 10 giây đầu sau khi phân phát; hoặc nhấp từ 3 liên kết khác nhau trở lên trong cùng 2 giây; hoặc từ nguồn thuộc danh sách hệ thống quét đã biết. Kênh nhắn tin: chỉ lượt truy cập từ nguồn thuộc danh sách máy tạo bản xem trước liên kết của nền tảng — không dùng tiêu chí thời gian, vì người nhận thật thường bấm ngay khi thấy thông báo | `BR-14.3` | Tiêu chí kỹ thuật, cập nhật theo hành vi của các hệ thống quét |
| Bộ tiêu chí chấm điểm thư rác | Tiêu đề viết hoa toàn bộ; từ 3 dấu chấm than liên tiếp; cụm từ trong danh mục do hệ thống duy trì; hình ảnh chiếm trên 60% nội dung; hình ảnh thiếu mô tả thay thế | `BR-16.2` | Chỉ dùng để cảnh báo; danh mục cập nhật theo bộ lọc của nhà cung cấp hộp thư |
| Dấu hiệu nhà cung cấp từ chối hàng loạt | > 50% tin thất bại tạm thời hoặc bị từ chối do danh tiếng người gửi trong 10 phút gần nhất, tối thiểu 200 tin | `BR-20.1` mục 7 | Ngưỡng kỹ thuật bảo vệ tài khoản gửi dùng chung |
| Thời gian chờ xác nhận phân phát trước khi xếp Chưa xác định | Email 72 giờ; SMS 48 giờ; WhatsApp, Zalo ZNS, Zalo OA 24 giờ | `BR-23.3` | Theo thời hạn báo phân phát tối đa mà các nhà cung cấp cam kết; không phải lựa chọn nghiệp vụ của doanh nghiệp |
| Độ dài tối thiểu của từ khóa từ chối do Quản trị viên thêm | 2 ký tự | `BR-26.3` | Chặn từ khóa một ký tự gây hủy nhận tin hàng loạt do nhầm |
| Khoảng trượt tính tỷ lệ khiếu nại của Không gian làm việc và của chuỗi nuôi dưỡng | 30 ngày | `BR-20.1`, `BR-27.3`, `KPI-02` | Theo cách các nhà cung cấp hộp thư đánh giá danh tiếng người gửi; không phải lựa chọn của doanh nghiệp |
| Danh mục từ khóa đa nghĩa trong danh mục từ khóa từ chối | Do hệ thống duy trì (ví dụ "HUY"); "STOP", "TC", "KHONG NHAN" không đa nghĩa | `BR-26.3` | Chặn ghi nhận nhầm lời từ chối trong hội thoại chăm sóc khách hàng |
| Danh mục từ thông dụng không được dùng làm từ khóa từ chối | Do hệ thống duy trì (ví dụ "OK", "CÓ", "VÂNG", "YES") | `BR-26.3` | Bảo vệ khỏi việc hủy nhận tin hàng loạt do cấu hình nhầm |
| Khoảng xét tỷ lệ thư rác từ công cụ theo dõi danh tiếng | 7 ngày gần nhất có số liệu | `BR-27.4` | Theo cách công cụ của nhà cung cấp hộp thư công bố số liệu |
| Giới hạn đợt gửi phục hồi tên miền | Tối đa 5.000 thư, chỉ tới khách có mở hoặc nhấp được ghi hợp lệ theo `BR-14.4` (không tính mở tự động, nhấp tự động) trong 90 ngày gần nhất; đợt phục hồi vẫn chịu đủ kiểm tra của `BR-08.4` | `BR-27.4` | Khối lượng nhỏ, tệp chất lượng cao là cách chuẩn để nhà cung cấp hộp thư đánh giá lại tên miền |
| Thời gian không tra được thông tin quyết định việc gửi (đồng thuận, trạng thái tiếp cận, Hạn chế xử lý, dấu vết chặn gửi, Danh sách không quảng cáo, số tin đã nhận để tính tần suất và giới hạn 24 giờ) hoặc bản Danh sách không quảng cáo quá tuổi, trước khi tự động tạm dừng | 15 phút | `NFR-06` | Ngưỡng sự cố kỹ thuật, không phải lựa chọn nghiệp vụ |

---

## Phụ lục C — Nhật ký mâu thuẫn đã giải quyết

Các mâu thuẫn và lỗ hổng dưới đây tồn tại trong v5.1 và đã được chốt cách xử lý trong v6.0; từ dòng 84 là các quyết định của v6.1, từ dòng 89 của v6.2, từ dòng 98 của v6.4 (đồng bộ phân quyền nền tảng v5.0), từ dòng 110 của v6.5, từ dòng 121 của v6.6 (sau các vòng review độc lập). Các vòng review sau không lật lại các quyết định này nếu không có căn cứ nghiệp vụ mới.

| # | Mâu thuẫn / lỗ hổng | Cách xử lý (v6.0; từ dòng 84: v6.1; từ dòng 89: v6.2) |
| --- | --- | --- |
| 1 | Đồng thuận chỉ được loại trừ khi tính quy mô tệp; người hủy nhận tin giữa lúc chiến dịch đang gửi vẫn nhận tin | Nguyên tắc 1; `BR-08.4` kiểm tra lại ngay trước từng tin, áp cho mọi loại lượt gửi |
| 2 | Nội dung bị khóa hoàn toàn khi đang gửi, trong khi kịch bản UAT yêu cầu tạm dừng để sửa liên kết lỗi rồi tiếp tục | `BR-19.3`: sửa được nội dung trong lúc tạm dừng, bắt buộc duyệt lại, sổ cái ghi phiên bản từng người nhận |
| 3 | Tỷ lệ mở tính trên số đã phân phát, tỷ lệ nhấp tính trên số đã gửi — hai mẫu số khác nhau | `BR-33.2` một bảng định nghĩa duy nhất; báo cáo tổng hợp cộng con số gốc (`BR-33.6`) |
| 4 | Trạng thái "Đã mở", "Đã nhấp" thay thế trạng thái "Đã phân phát" | `BR-23.2`: tách trạng thái phân phát và sự kiện tương tác |
| 5 | Dự phòng kênh kích hoạt khi "thất bại" chung chung, có thể gửi trùng hai kênh hoặc gửi cho người đã từ chối, đã chặn doanh nghiệp | `BR-37.2`–`BR-37.5`: chỉ khi thất bại vĩnh viễn thuộc nhóm lỗi cho phép, kiểm tra đầy đủ trên kênh dự phòng, hủy nếu kênh trước báo phân phát muộn |
| 6 | Gửi thử tới địa chỉ bất kỳ — đường vòng vượt phê duyệt kép và đồng thuận | `BR-15.1`: chỉ địa chỉ nội bộ đã đăng ký, có trần số người nhận |
| 7 | Phê duyệt kép chỉ yêu cầu "khác người tạo" — người duyệt tự sửa rồi tự duyệt được | `BR-17.2`: khác người gửi duyệt và khác mọi người đã sửa phiên bản đang duyệt |
| 8 | Bắt buộc đủ mọi thông tin ngay khi tạo, không lưu nháp được | `BR-01.1` / `BR-01.2`: tối thiểu để lưu nháp tách khỏi điều kiện đủ để gửi duyệt |
| 9 | Loại trừ toàn bộ Định danh dùng chung, nhưng không xử lý hai hồ sơ cùng điểm đến có đồng thuận khác nhau | `BR-08.3`: khử trùng theo điểm đến, chặt nhất thắng, lời chào dùng giá trị thay thế; tùy chọn loại hẳn qua `CFG-CAMP-23` |
| 10 | Giới hạn tần suất không nói cách đếm khi hai chiến dịch chạy song song | `BR-08.5`: đếm cả tin đang gửi dở, chiến dịch gửi tới trước dùng suất |
| 11 | Khung giờ yên lặng: "chặn phát sóng" theo múi giờ khách hàng nhưng kịch bản gửi dồn lúc 08:01 | `BR-29.2`, `BR-29.3`: hoãn từng tin theo giờ địa phương người nhận, gửi lại theo nhịp |
| 12 | "Doanh thu từ chiến dịch" 30 ngày không nêu mô hình, mâu thuẫn tiềm tàng với Nguồn gốc chính điểm chạm đầu tiên của phân hệ Cơ hội | `BR-34.1`: hai thước đo tên khác nhau — "có nguồn gốc" (cộng dồn được, khớp `deals-pipeline-srs.md`) và "chịu ảnh hưởng" (không cộng dồn) |
| 13 | Nhóm mục đích gửi không được khai báo, trái (`contacts-srs.md`, `BR-30.5`, `BR-30.9`) | `BR-01.5`: cố định nhóm Tiếp thị & Quảng bá; nhu cầu thông báo dịch vụ đưa vào Mục 7 |
| 14 | Chiến dịch hẹn giờ không đối chiếu hạn mức, không xử lý đình chỉ dịch vụ | `BR-17.8`, `BR-18.3`, `BR-20.1`, `BR-20.2`: đối chiếu toàn lô trước khi gửi; tự tạm dừng khi đình chỉ, không tự tiếp tục |
| 15 | Chiến dịch Tái tiếp cận (`contacts-srs.md`, `BR-12.5b`) không được đặc tả | `FEAT-31` |
| 16 | Không có quy định với sổ cái khi có yêu cầu xóa dữ liệu | `FEAT-32`: khử định danh, giữ số liệu tổng hợp |
| 17 | Chỉ chặn người đã từ chối, không đòi hỏi đồng ý trước | Lý do loại trừ 6 "Chưa có Đồng ý nhận tin có bằng chứng" (`BR-08.1`); giả định 6 Mục 1.6 — nay áp theo kênh doanh nghiệp yêu cầu (xem dòng 89, 90) |
| 18 | Kênh "Zalo" gộp tin thông báo theo số điện thoại với tin truyền thông tới người quan tâm Tài khoản Chính thức | Tách Zalo ZNS và Zalo OA (`FEAT-09`), mỗi kênh có thuộc tính riêng |
| 19 | Sửa trong lúc tạm dừng cần duyệt lại nhưng vòng đời không có trạng thái chờ duyệt, từ chối lại "về Nháp" trái quy tắc không quay về Nháp | Trạng thái "Tạm dừng — chờ duyệt phiên bản"; bị từ chối thì giữ phiên bản cũ (`BR-19.3`, Mục 2.5) |
| 20 | Chiến dịch hẹn giờ gặp trục trặc vận hành lúc nửa đêm bị đưa về Nháp, mất phê duyệt | Tách kiểm tra làm mất hiệu lực phiên bản khỏi kiểm tra vận hành; trạng thái Giữ lại có thời gian ân hạn (`BR-17.8`, `BR-18.3`) |
| 21 | Đổi người phụ trách làm đổi tập người nhận mà không phải duyệt lại | `BR-01.3`: đổi người phụ trách ở Chờ duyệt / Đã duyệt / Giữ lại đưa về Nháp |
| 22 | Chuỗi nuôi dưỡng vi phạm hàng loạt quy tắc của chiến dịch một lần | Vòng đời riêng và danh sách ngoại lệ có chủ đích (`BR-39.1`) |
| 23 | Kiểm tra tại thời điểm gửi chỉ xét hồ sơ được chọn, bỏ sót hồ sơ khác cùng điểm đến và hồ sơ đã bị gộp | `BR-08.4` (a), (b); lời từ chối áp lên mọi hồ sơ giữ điểm đến (`BR-26.4`) |
| 24 | Giới hạn pháp lý 24 giờ ghi theo quy định cũ | Đã thay bằng dòng 89: giá trị và cách đếm là tham số của doanh nghiệp (`CFG-CAMP-69`) |
| 25 | Người đã xóa dữ liệu có thể nhận tiếp thị lại khi danh sách cũ được nhập lại | Dấu vết chặn gửi không đọc ngược được (`BR-32.4`) |
| 26 | Miễn trừ tần suất do chính người duyệt bật khi phê duyệt | Miễn trừ là phần của phiên bản gửi duyệt (`BR-08.6`) |
| 27 | Các ngưỡng cảnh báo và thời hạn bị đóng cứng dưới dạng hằng số | Chuyển thành `CFG-CAMP-29`–`42`; chỉ giữ ở B.2 (hằng số) các giới hạn thiết kế và chuẩn kỹ thuật có giải trình |
| 28 | Bộ quy tắc pháp lý gắn theo quốc gia đăng ký, bỏ lọt tin gửi tới số Việt Nam từ Không gian làm việc đăng ký ở nước khác | Đã thay bằng dòng 89: không còn bộ quy tắc theo quốc gia; chính sách do doanh nghiệp cấu hình (`BR-30.1`) |
| 29 | Hoàn tác gộp làm Đồng ý cũ "sống lại" | `BR-08.4` (b) theo quy tắc hoàn tác gộp của phân hệ Khách hàng (đã thay bởi dòng 96) |
| 30 | Dấu vết chặn gửi chỉ tạo khi xóa theo quyền chủ thể dữ liệu | `BR-32.4`: mọi lần xóa vĩnh viễn điểm đến đang từ chối; chỉ chính chủ xác nhận mới gỡ được |
| 31 | Lời từ chối chờ tư vấn viên xác nhận không có thời hạn | `BR-26.3`: tạm chặn ngay tới khi có người xử lý, quá `CFG-CAMP-44` thì leo thang; không tự tạo bằng chứng từ chối |
| 32 | Theo dõi mở/nhấp từng người không có căn cứ riêng | `BR-14.4` |
| 33 | Ngân sách: bước kiểm tra trước là cảnh báo ở một nơi, chặn ở nơi khác; trần vượt không đạt được với kênh tính phí theo tin phân phát | `BR-41.2`: kiểm tra trước là chặn; theo dõi chi phí đã cam kết |
| 34 | Ngày không gửi vừa là tạm dừng bảo vệ vừa là hoãn | `BR-20.1`: ngày không gửi chỉ hoãn |
| 35 | Dừng khẩn cấp và tự động tạm dừng bảo vệ bỏ sót chuỗi nuôi dưỡng | `BR-20.1`, `BR-42.1` áp cho mọi chuỗi chưa Kết thúc |
| 36 | Cùng một điều kiện vừa là lỗi làm mất hiệu lực phiên bản vừa là kiểm tra vận hành | `BR-16.1` chia lỗi chặn nhóm (a) và nhóm (b) |
| 37 | Mọi thư trả lời email, kể cả thư báo vắng mặt, bị coi là lời từ chối tiềm năng | `BR-26.3`: chỉ gắn nhãn khi có từ khóa hoặc cụm từ từ chối; loại thư tự động |
| 38 | Không có cách ngăn ưu đãi hết hạn tới tay khách vì các cơ chế hoãn | `BR-18.4` hạn chót gửi |
| 39 | Liên kết hủy nhận tin ngừng hoạt động khi sổ cái hết hạn lưu | `BR-26.2`: ánh xạ liên kết hủy lưu tách khỏi sổ cái |
| 40 | Giới hạn pháp lý 24 giờ đếm riêng từng kênh cho cùng một số điện thoại | `BR-30.3`: gộp các kênh gửi theo số điện thoại — nay là tùy chọn đơn vị đếm của `CFG-CAMP-69` (dòng 89) |
| 41 | Chủ thể đang Đồng ý yêu cầu xóa dữ liệu thì không để lại dấu vết chặn gửi | `BR-32.4`: yêu cầu xóa được coi là rút đồng thuận |
| 42 | Nhóm lỗi chặn (a) viện dẫn cả `BR-01.2` nên lỗi vận hành gián tiếp thành lỗi mất hiệu lực | `BR-16.1`: nhóm (a) liệt kê tường minh; điều kiện gửi phê duyệt tách khỏi phân loại |
| 43 | Chiều ngược trả khách về Đã rời bỏ bị ma trận chuyển giai đoạn của phân hệ Khách hàng cấm | `BR-14.3`: chỉ thông báo người phụ trách, tạm loại khỏi chiến dịch thường |
| 44 | Hỏng tạm thời lặp lại dùng trạng thái không đảo được của phân hệ Khách hàng | `BR-28.2`: tạm ngừng có thời hạn trong phân hệ Chiến dịch |
| 45 | Người phụ trách rời đi đưa chiến dịch vào trạng thái mà bảng vòng đời không cho phép | `BR-01.3`: không đổi trạng thái, xét quy mô tại lúc chốt |
| 46 | Số người nhận tăng quá ngưỡng xóa phê duyệt của chiến dịch hẹn giờ | `BR-06.4`: Giữ lại, người khác người soạn xác nhận quy mô |
| 47 | Khóa gửi Email theo công cụ danh tiếng không có đường gỡ | `BR-27.4`: khoảng xét cố định, đợt gửi phục hồi có hai người duyệt |
| 48 | Tin tiếp thị từ phân hệ khác đi vòng qua các lớp chặn của chiến dịch | `FEAT-44` cổng kiểm soát dùng chung; (`contacts-srs.md`, `BR-30.5`) |
| 49 | Chiến dịch tạm dừng qua hạn chót hoặc qua mốc hết hiệu lực Tái tiếp cận bị kẹt | `BR-16.1`: sau khi bắt đầu gửi, xử lý theo từng người nhận |
| 50 | Một tham số dùng cho nhiều mục đích | Tách `CFG-CAMP-49`, `CFG-CAMP-50` |
| 51 | Cổng dùng chung chưa áp căn cứ theo dõi hành vi, loại tên thương hiệu, loại mẫu; không có bằng chứng cho lượt gửi thành công | `BR-44.1`, `BR-44.2` |
| 52 | Chiến dịch Tái tiếp cận không gửi phê duyệt được vì thiếu chính phê duyệt chỉ có ở giai đoạn Chờ duyệt | `BR-16.1`: hai lỗi chỉ chặn phê duyệt phát sóng và bắt đầu gửi |
| 53 | Người duyệt tự duyệt được thay đổi của mình ngoài nội dung, đối tượng, tài khoản gửi | `BR-17.2`: loại mọi người đã tạo thay đổi thuộc danh sách `BR-17.4` |
| 54 | Thẻ gắn tự động vừa "không gỡ khi hủy nhận tin" vừa "gỡ khi rút đồng thuận" | `BR-36.4`: phân biệt hủy trên một kênh và mất căn cứ trên mọi kênh |
| 55 | Hai chiến dịch "Chờ" cùng qua đối chiếu hạn ngạch, một chiến dịch dừng giữa chừng | `BR-40.4` giữ chỗ hạn ngạch |
| 56 | Liên kết hủy nhận tin mất tác dụng khi điểm đến đang Đồng ý bị sửa hoặc xóa | `BR-26.2`: giữ ánh xạ dạng một chiều, lượt hủy về sau tạo dấu vết chặn gửi |
| 57 | Chiến dịch Tái tiếp cận không gửi phê duyệt được vì 0 người khả dụng trước khi phần Tái tiếp cận được duyệt | `BR-01.2`: tính cả khách sẽ được miễn nếu được duyệt |
| 58 | Nội dung chờ nền tảng kiểm duyệt chặn luôn phê duyệt nội bộ, khiến tình huống Giữ lại vì chờ kiểm duyệt không bao giờ xảy ra | `BR-16.1`, `BR-13.5`: chỉ chặn bắt đầu gửi |
| 59 | Đổi người phụ trách khi chiến dịch đang gửi vừa bị bắt về Nháp vừa bị vòng đời cấm về Nháp | `BR-17.4`: chỉ áp ở Chờ duyệt, Đã duyệt, Giữ lại |
| 60 | Danh sách Hiển thị Dùng chung làm tiêu chí lách quy tắc trường nhạy cảm; mẫu người nhận hiển thị khách đang Hạn chế xử lý | `BR-05.1`, `BR-07.1` |
| 61 | Trần hạn ngạch của một chiến dịch chỉ áp cho giữ chỗ dàn trải, khiến chiến dịch "Chờ" chiếm trọn hạn ngạch và ngày hoàn tất tính sai | `BR-40.4`: trần áp cho mọi chiến dịch, cả giữ chỗ lẫn gửi thực, giữ chỗ theo ngày gửi thực tế |
| 62 | Ngân sách không có trong danh sách phần sửa được theo trạng thái, khiến chiến dịch tạm dừng vì ngân sách không tiếp tục được, và gửi lại thủ công sau khi Hoàn tất bị chặn vĩnh viễn | `BR-01.6`: ngân sách sửa được ở mọi trạng thái trước Hoàn tất / Đã hủy và trong thời hạn gửi lại |
| 63 | Ngưỡng tự tạm dừng xét trên số liệu tích lũy nên kích hoạt lại ngay sau khi tiếp tục | `BR-20.2`: xét trên phần gửi sau thời điểm tiếp tục |
| 64 | Gửi tiếp sang ngày mới bị tạm dừng oan vì chênh lệch đối tượng dù danh sách đã chốt | `BR-40.3` loại trừ chênh lệch đối tượng như `BR-19.2` |
| 65 | Nhật ký của phân hệ Khách hàng có sự kiện thu hồi phê duyệt Tái tiếp cận nhưng phân hệ Chiến dịch không có quy trình thu hồi | `BR-31.5` |
| 66 | Định nghĩa "tin trả lời chiến dịch" loại tin ngoài cửa sổ thời gian và tin trong hội thoại có tư vấn viên khỏi mọi đường ghi nhận lời từ chối | `BR-26.3`: định nghĩa chỉ giới hạn tự động ghi nhận; mọi tin được so khớp để gắn nhãn; tư vấn viên ghi nhận được trên bất kỳ tin nào |
| 67 | Miễn trừ tần suất ghi đè tần suất trong điều khoản đồng thuận | `BR-08.6`: chỉ bỏ qua `CFG-CAMP-02`/`CFG-CAMP-03` |
| 68 | Lỗi "Chờ" vượt trần so tổng khối lượng trong khi giữ chỗ tính theo ngày | `BR-40.2`, `BR-16.1`: xét theo từng ngày gửi dự kiến |
| 69 | Danh sách Hiển thị Dùng chung làm tiêu chí cho phép chủ danh sách đổi đối tượng sau khi duyệt | `BR-05.1`, `BR-39.1`: bộ lọc được sao vào phiên bản |
| 70 | Trường được đánh dấu nhạy cảm sau khi duyệt vẫn được dùng làm tiêu chí cho chiến dịch đã duyệt và chuỗi đang chạy | `BR-05.5`: đối chiếu lại với phân loại trường hiện hành lúc duyệt, lúc chốt và mỗi lần xét người vào chuỗi |
| 71 | So khớp cả phần trích dẫn thư gốc và từ đa nghĩa trong câu, khiến gần như mọi thư trả lời và hội thoại "hủy đơn" bị tạm chặn oan | `BR-26.3`: chỉ so khớp phần khách tự viết; từ đa nghĩa chỉ tính khi đứng một mình hoặc đi cùng cụm từ về tiếp thị |
| 72 | Tổng phần giữ chỗ của các chiến dịch dàn trải có thể vượt hạn ngạch; chiến dịch "Chờ" kẹt mà không được báo trước | `BR-40.4`, `BR-40.2` |
| 73 | Miễn trừ tần suất 14 ngày của chuỗi được tính lại mỗi lần vào lại | `BR-39.4`: chỉ tính từ lần vào chuỗi đầu tiên |
| 74 | Ghi nhận từ chối trên bất kỳ hội thoại nào không khớp ngoại lệ hạ đồng thuận của tuyến hỗ trợ | `BR-26.3`: chỉ trong hội thoại đang mở, theo `contacts-srs.md` `BR-35.4` (a) |
| 75 | Tạm dừng không trả lại chỗ giữ hạn ngạch, nên cách gỡ "tạm dừng chiến dịch kia" không có tác dụng | `BR-40.4`: tạm dừng trả chỗ, tiếp tục giữ chỗ lại theo phần còn trống; hủy và hoàn tất trả chỗ cho mọi chiến dịch |
| 76 | Thu hẹp từ khóa đa nghĩa áp cả cho đầu số SMS nhận từ chối, bỏ sót "HUY QC"; AC cũ còn gắn nhãn "hủy đơn giúp tôi" trên kênh hội thoại; thao tác trên tin gắn nhãn mất hiệu lực khi hội thoại đóng | `BR-26.3` viết lại: ba danh mục, bốn loại tin cần xác nhận, phạm vi hai thao tác, không đóng hội thoại khi còn tin gắn nhãn |
| 77 | Lượt ngoài dự kiến và chuỗi nuôi dưỡng có thể dùng phần đã giữ chỗ; hạ hạn ngạch dưới tổng phần đã giữ chỗ không có quy tắc | `BR-40.4`: giữ chỗ chỉ cho lượt gửi chính; cắt theo thứ tự ngược thứ tự bắt đầu |
| 78 | Quản lý Marketing gỡ tạm chặn hàng loạt ngoài hội thoại; "không đóng được hội thoại" không khớp các đường đóng của Hộp thư Hội thoại | `BR-26.3`: ngoài hội thoại chỉ Người phụ trách Bảo vệ Dữ liệu gỡ, từng tin, có lý do; chặn mọi đường đóng, `omnichat-srs.md` `BR-10.9` |
| 79 | Tổng giữ chỗ chiếm hết hạn ngạch, chuỗi nuôi dưỡng bị hoãn nhiều ngày không ai biết | `CFG-CAMP-55`, `BR-39.4` thông báo khi hoãn quá 24 giờ |
| 80 | Hạ hạn ngạch chỉ so với tổng giữ chỗ, bỏ sót phần của từng chiến dịch vượt trần mới; yêu cầu tiếp tục chiến dịch "Chờ" khi đủ chỗ không có quy tắc; doanh nghiệp không có Người phụ trách Bảo vệ Dữ liệu không gỡ được tạm chặn | `BR-40.4`, `BR-19.2`, `BR-26.3` |
| 81 | Chiến dịch "Chờ" đang Tạm dừng được tự tiếp tục khi đủ chỗ, trái Nguyên tắc 5 | `BR-19.2`: chỉ thông báo, người bấm tiếp tục |
| 82 | "Phần còn có thể giữ chỗ" không trừ lượng đã gửi bởi lượt không giữ chỗ; trần thực của một chiến dịch khi `CFG-CAMP-55` thấp hơn `CFG-CAMP-54` | `BR-40.4`, `BR-40.2` |
| 83 | Chủ sở hữu thay Người phụ trách Bảo vệ Dữ liệu làm sụp kiểm soát hai người | Mục 2.2, ghi chú Phụ lục B.1: quy tắc thay thế người thứ hai của `contacts-srs.md` `NFR-14` |
| 84 | Thông báo dịch vụ hàng loạt để ở Mục 7 trong khi phân hệ Khách hàng đã xếp thông báo bảo trì, thông báo pháp lý vào nhóm Giao dịch & Dịch vụ | `FEAT-45`; ngoại lệ tại `contacts-srs.md` `BR-30.7` |
| 85 | Hằng số vận hành mà doanh nghiệp có nhu cầu đặt khác nhau nằm ở phụ lục hằng số (nay là B.2) | `CFG-CAMP-56`–`CFG-CAMP-62` |
| 86 | Thông báo dịch vụ mâu thuẫn với dừng khẩn cấp, ngày không gửi, hạn ngạch tiếp thị; không giới hạn người nhận là khách đang có quan hệ; Quản trị viên, Chủ sở hữu duyệt thay được Người phụ trách Bảo vệ Dữ liệu | `BR-42.1`, `BR-20.1`, `BR-45.3`–`BR-45.5`, `CFG-CAMP-66`, ma trận `FEAT-45` |
| 87 | Tham số giai đoạn nhận Thông báo dịch vụ cho chọn cả giai đoạn tiền bán hàng; quyền phản đối không thực hiện được trên kênh nhắn tin; phần duyệt bảo vệ dữ liệu có thể trùng người duyệt phát sóng; mục đích (2), (4) bị trần tần suất, hạn ngạch tiếp thị và đầu số nhận từ chối chặn | `CFG-CAMP-66`, `BR-45.8`, `BR-45.3`, `BR-45.5` (phạm vi `NFR-06`), `CFG-CAMP-64`, `CFG-CAMP-65` theo mục đích, `BR-40.1`, `BR-45.7`, `BR-26.1`, `contacts-srs.md` `BR-30.6` |
| 88 | Đầu số nhận từ chối SMS chặn thông báo thu hồi; "chặt nhất thắng" chặn thông báo vì giai đoạn của hồ sơ dùng chung; khôi phục phản đối trỏ sai nhánh | `BR-26.1`, `BR-08.3`, `BR-45.8` |
| 89 | Bộ quy tắc pháp lý Việt Nam khóa cứng trong hệ thống, trái nguyên tắc mọi quy tắc tùy doanh nghiệp là cấu hình; giá trị ghi theo trí nhớ còn lệch văn bản | `FEAT-30` viết lại thành chính sách gửi tiếp thị do doanh nghiệp cấu hình và chịu trách nhiệm; bỏ Phụ lục B.2 cũ; `CFG-CAMP-28`, `CFG-CAMP-69`–`74` |
| 90 | Tắt yêu cầu Đồng ý trước không có tác dụng vì phân hệ Khách hàng chặn cứng Chưa có đồng thuận; sổ cái không có căn cứ khi kênh không yêu cầu Đồng ý | Giả định 6, `BR-23.1`, phiên bản chính sách (`BR-30.1`); `contacts-srs.md` `BR-30.1` theo `CFG-CAMP-74` |
| 91 | Quy tắc dỡ Hạn chế xử lý và kênh dự phòng còn giả định Chưa có đồng thuận luôn bị chặn; chính sách siết giữa chừng không rõ hiệu lực; lô nhập khẩu "không có cơ sở đồng thuận" bị gắn Từ chối | `BR-32.1`, `BR-37.3`, `BR-14.4`, `BR-30.1`; `contacts-srs.md` `BR-30.4` |
| 92 | Phụ lục B nói mọi thay đổi tham số chỉ áp cho lượt gửi mới, ngược `BR-30.1`; biện pháp phòng ngừa biến Chưa có đồng thuận thành Từ chối vĩnh viễn | Ghi chú Phụ lục B.1; `BR-32.4`; `contacts-srs.md` `BR-33.7` (e) |
| 93 | Đồng ý tự khôi phục không bao giờ là căn cứ, kể cả khi đã xác định người yêu cầu là mạo danh; bật nhãn SMS giữa chừng gửi nội dung chưa được nhà mạng duyệt | `BR-32.1`: dỡ sớm khôi phục Đồng ý gốc là căn cứ, hết hạn thì cần chính chủ xác nhận lại; `BR-30.1`, `BR-20.1` tình huống 3 với SMS |
| 94 | Bật nhãn SMS khi chiến dịch đã duyệt chưa gửi chưa có quy tắc; khách xác minh thành công chưa được coi là căn cứ ở phân hệ này | `BR-16.1` nhóm (a), `BR-32.1`, `BR-23.1` |
| 95 | Bật nhãn Zalo OA giữa chừng gửi nội dung chưa được nền tảng kiểm duyệt; sổ cái coi Đồng ý tự khôi phục là căn cứ | `BR-30.1`, `BR-16.1` nhóm (a), `BR-23.1` |
| 96 | `BR-08.4` (b) diễn giải lại quy tắc hoàn tác gộp và lệch `contacts-srs.md` `BR-20.2` | `BR-08.4` (b) chỉ dẫn chiếu `contacts-srs.md` `BR-20.2`, không diễn giải lại |
| 97 | Vai trò có mức riêng cho từng thao tác (`iam-tenant-authorization.md` v4): "phạm vi của người phụ trách" trong `BR-05.4` chưa nói là mức nào | `BR-05.4`: đối tượng theo mức Xem trên Khách hàng của người phụ trách; mức Sửa không giới hạn đối tượng |
| 98 | Tài liệu dùng bốn tên mức phạm vi cũ ("Của mình + cấp dưới và đơn vị của mình", "Cả nhánh đơn vị", "Toàn Không gian làm việc") và tham chiếu tài liệu phân quyền bản cũ | Dùng năm mức truy cập của `iam-tenant-authorization.md` v5.0 và dẫn chiếu Mục 1.4 của tài liệu đó, không tự định nghĩa (Mục 1.4, `BR-01.4`) |
| 99 | `CFG-05-02` của `contacts-srs.md` được dùng làm nơi điều chỉnh mức Xem Khách hàng của Marketing, trong khi tham số đã chuyển về tài liệu phân quyền | Dẫn chiếu [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02`; danh sách và ma trận mặc định của vai trò dựng sẵn chỉ dẫn chiếu `FEAT-29` của tài liệu đó (Giả định 4, Mục 2.2, Mục 5 ghi chú 1) |
| 100 | Phát sóng chiến dịch được mô tả như "quyền hạn tách biệt" gắn với Quản lý Marketing, Quản trị viên | Khai báo (Chiến dịch, Phát sóng) là thao tác đặc thù có mức truy cập, hiển thị dòng riêng ở chế độ Cơ bản, mặc định theo `FEAT-29` của tài liệu phân quyền; Marketing = Không có trừ khi doanh nghiệp bật tại khởi tạo (`BR-17.1`) |
| 101 | Đối tượng theo mức Xem của người phụ trách chiến dịch (thay quyết định dòng 97): người phụ trách đổi theo bàn giao, nghỉ phép, không phải người đưa ra lựa chọn tiếp thị; tiến trình gửi lại không chịu quyền của người bấm phát sóng | Đối tượng giới hạn theo phần giao mức Xem Khách hàng của người chọn đối tượng và của người khởi chạy; mỗi tin xét quyền người khởi chạy tại thời điểm gửi, lý do loại trừ 18 (`BR-05.4`, `BR-17.10`, `BR-08.1`) |
| 102 | Đổi người phụ trách ở Chờ duyệt / Đã duyệt / Giữ lại đưa chiến dịch về Nháp (thay quyết định dòng 21, 59) | Người phụ trách không còn quyết định đối tượng nên không thuộc phiên bản duyệt; đổi người phụ trách là thao tác Gán, không đổi trạng thái (`BR-01.3`, `BR-17.4`) |
| 103 | Người phụ trách rời đi thì chiến dịch tự chuyển cho quản lý trực tiếp (thay quyết định dòng 45), bỏ qua quy trình bàn giao có biên bản của nền tảng | Tạm ngưng và rời workspace theo `FEAT-42`, `FEAT-43` của tài liệu phân quyền: giữ nguyên / chuyển tạm, hoặc bàn giao bắt buộc trước khi gỡ (`BR-01.8`) |
| 104 | Không có quy tắc khi người bấm phát sóng hoặc chốt giờ hẹn bị tạm ngưng, rời đi hay bị thu quyền giữa lúc gửi | Khái niệm người khởi chạy; tạm dừng và thông báo, kích hoạt lại không tự chạy tiếp, chuyển người khởi chạy hoặc dừng hẳn, bước bắt buộc khi rời workspace (`BR-17.10`, `BR-17.11`) |
| 105 | Công việc liên hệ lại vào "hàng đợi chung" không có đơn vị tiếp nhận, chỉ người xem toàn workspace thấy | Mỗi tài khoản gửi không có tiếp nhận hội thoại khai báo đơn vị tiếp nhận; công việc là bản ghi công việc; nhận việc, trả về, chuyển hàng đợi theo tài liệu phân quyền (`BR-35.5`) |
| 106 | Phân hệ chưa khai báo trường nhạy cảm theo khung che dữ liệu chung | Không có trường nhạy cảm riêng; điểm đến che theo hồ sơ khách hàng; che không cản việc gửi (`BR-07.3`) |
| 107 | Thẩm quyền tham số, kích hoạt và gỡ dừng khẩn cấp gắn với tên vai trò Quản lý Marketing, Quản trị viên | Quyền quản trị của phân hệ (Mục 5 ghi chú 2); kích hoạt dừng khẩn cấp theo ô Phát sóng; mặc định của Quản lý Marketing đưa về Mục 7 mục 12 (`BR-42.2`, Phụ lục B) |
| 108 | Thao tác dừng có thể bị chặn khi nhật ký kiểm toán gặp sự cố | Nguyên tắc 7: dừng và thu hẹp luôn thực hiện, ghi bù theo ADR-0010; thao tác làm tăng phạm vi gửi đóng khi lỗi (`NFR-08`) |
| 109 | Tám quy tắc thiếu lý do nghiệp vụ | Bổ sung lý do cho `BR-02.1`, `BR-06.1`, `BR-11.5`, `BR-16.2`, `BR-24.2`, `BR-33.1`, `BR-33.4`, `BR-39.6` |
| 110 | Đối tượng định nghĩa theo "mức Xem" chung chung, có thể tính cả quyền đọc tự động, lượt chia sẻ, quyền tạm thời và bỏ qua chính sách Từ chối, bản ghi giữ chỗ | Tập khách hàng xem được theo bước 1–3 của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`, liệt kê nguồn nới được tính và không tính; nguồn chặn và bản ghi giữ chỗ luôn áp dụng (`BR-05.4`, `BR-17.10`, Mục 5 ghi chú 3) |
| 111 | Mục 5 chưa khai báo ma trận chi tiết cho mọi vai trò dựng sẵn và lý do khi lệch mức chung | Mục 5 ghi chú 6 theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-29.6` |
| 112 | Mặc định của quyền Cấu hình nhịp độ tiếp thị để ngỏ ở Mục 7 | Phân hệ tự chốt theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-29.6`: Người có toàn quyền và Quản lý Marketing; bỏ Mục 7 mục 12 |
| 113 | `BR-35.5` coi công việc liên hệ lại là bản ghi công việc, có chuyển hàng đợi, lệch quy tắc của phân hệ sở hữu loại Công việc | Bản ghi chờ phân công, không chuyển hàng đợi, trả về theo [`tasks-srs.md`](./tasks-srs.md) `BR-13.6`; nguồn "Liên hệ lại từ chiến dịch"; giao thẳng thì theo đơn vị người phụ trách; `CFG-CAMP-53` thuộc Người có toàn quyền |
| 114 | Cá nhân hóa và gửi thử có thể đưa trường người soạn hay người nhận gửi thử không được xem đầy đủ ra ngoài | `BR-12.1`: chỉ trường người soạn xem đầy đủ; `BR-15.2`: mức hạn chế nhất trong số người nhận gửi thử |
| 115 | Chuỗi nuôi dưỡng không có quy tắc khi người chọn đối tượng mất quyền, bị tạm ngưng hay rời đi | `BR-05.4` (e): phần giao như người khởi chạy; tạm không nhận người mới tới khi bàn giao |
| 116 | Mở quy trình rời workspace mà bỏ "Tạm ngưng ngay" thì lượt gửi vẫn chạy | `BR-17.11` (e): tạm dừng ngay khi mở quy trình |
| 117 | `BR-26.3` gắn với tên Quản lý Marketing | Quyền quản trị Xử lý tin gắn nhãn từ chối (Mục 5 ghi chú 2, 6) |
| 118 | Ngân sách và chi phí chưa được khai báo về che dữ liệu | `BR-07.3` (e): không che, kèm lý do |
| 119 | `CFG-CAMP-20`, `CFG-CAMP-64` khóa sàn mang tính pháp lý | Tự do kèm cảnh báo, lý do vận hành (Phụ lục B) |
| 120 | Kịch bản 19 không cho thấy mất quyền Phát sóng làm tạm dừng trước và báo người phụ trách | Viết lại Kịch bản 19 |
| 121 | Công việc liên hệ lại được coi như tạo thay tài khoản gửi; giao thẳng cho người phụ trách khách hàng có thể vượt trần Gán của người khởi chạy mà không ghi nhận | Tạo thay người khởi chạy của chiến dịch ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`); giao thẳng là ngoại lệ tường minh của trần Gán, người nhận vẫn đạt điều kiện [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`, không đạt thì vào hàng đợi (`BR-35.5` (b)) |
| 122 | Danh sách tin gắn nhãn cho người có quyền Xử lý thấy tin của khách ngoài tập xem được | Chỉ tin trong tập xem được theo `BR-05.4`; phần còn lại thuộc Người phụ trách Bảo vệ Dữ liệu (`BR-26.3`) |
| 123 | Nhiều quy tắc còn giao việc cho "Quản trị viên" | Thay bằng quyền quản trị cụ thể của Mục 5 ghi chú 2; danh sách tên miền nội bộ thuộc quyền Quản lý danh sách người nhận gửi thử |
| 124 | Người phụ trách hiện tại không có đường trả về hay đề nghị chuyển theo phân quyền nền tảng | Hai đường của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6` cho công việc liên hệ lại (`BR-35.5` (e)) và chiến dịch (`BR-01.8`); chiến dịch không có hàng đợi |
| 125 | Ghi danh bù sau tạm dừng (`BR-39.5`) mâu thuẫn quy tắc không ghi danh hồi tố khi chờ bàn giao (`BR-05.4` (e)) khi người chọn đối tượng cũng là người khởi chạy | Không ghi danh bù cho khoảng tạm dừng do người khởi chạy hay chờ bàn giao người chọn đối tượng |
| 126 | Nguồn nới tính lúc gửi làm danh sách đã chốt có thể nở thêm | Nguồn nới chốt tại lúc chốt danh sách hoặc lúc duyệt chuỗi, chỉ thu hẹp về sau (`BR-05.4`, `BR-17.10`) |
| 127 | Cột "Quản lý Kinh doanh" ở Mục 5 không khớp vai trò dựng sẵn và ghi chú 6 | Đổi thành vai trò Quản lý, đồng bộ các dòng Xem |
| 128 | Chưa rõ việc tạo công việc liên hệ lại có xét ô (Công việc, Tạo) của người khởi chạy, và xử lý khi người khởi chạy bị tạm ngưng hay đã rời đi | Không xét ô Tạo (nguồn do Người có toàn quyền thiết lập, như biểu mẫu); vẫn tạo vào hàng đợi và vẫn giao thẳng cho người phụ trách khách hàng khi người khởi chạy không còn hoạt động; [`tasks-srs.md`](./tasks-srs.md) `BR-13.7` (c) chỉ áp cho quy trình tự động hóa (`BR-35.5` (b)) |
| 129 | Trả về hàng đợi được cho cả công việc giao thẳng, trái quy tắc của phân hệ sở hữu loại Công việc; nguồn liệt kê cả địa chỉ email không nhận trả lời về hệ thống; hộp thư nhận trả lời có thể trùng hộp thư của Vé hỗ trợ | Trả về chỉ cho công việc của hàng đợi ([`tasks-srs.md`](./tasks-srs.md) `BR-13.6` (b), (d)), công việc giao thẳng chỉ đề nghị chuyển hoặc giao lại; bỏ email khỏi danh sách nguồn; `BR-10.4` chỉ cho hộp thư Hộp thư Hội thoại hoặc bên ngoài, do quyền Quản lý kênh gửi tiếp thị khai báo |
| 130 | Điều kiện chấp nhận đề nghị chuyển thiếu điều kiện xem được và không bị chặn; Kịch bản 19 nêu số khách của cá nhân A; Mục 5 thiếu thao tác trên công việc liên hệ lại | Bổ sung AC-35.5.10, 35.5.17; Kịch bản 19 nêu tổng của đơn vị Marketing; dòng `FEAT-35` ở Mục 5 |
| 131 | `BR-35.5` tự liệt kê điều kiện người nhận, thiếu (Công việc, Sửa) khác Không có; câu cảnh báo hộp thư bên ngoài ở `BR-10.4` sai ngữ pháp; người tạo công việc liên hệ lại chưa ghi nguồn | Dẫn chiếu điều kiện người nhận của [`tasks-srs.md`](./tasks-srs.md) `BR-09.1` (gồm [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`), thêm AC-35.5.19, 35.5.20; sửa `BR-10.4` kèm AC-10.4.4, 10.4.5; người tạo ghi "Hệ thống — nguồn <tên tài khoản gửi>" |
