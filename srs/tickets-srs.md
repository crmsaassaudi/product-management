# SRS — Phân hệ Quản lý Vé Hỗ trợ Khách hàng, Dịch vụ CSKH & Cam kết Chất lượng SLA (Tickets & Customer Support Management)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA (Version 7.2) |
| **Module** | CRM — Phân hệ Quản lý Vé Hỗ trợ Khách hàng, Dịch vụ CSKH & Cam kết Chất lượng SLA (Tickets & Customer Support Management) |
| **Ngày cập nhật** | 2026-09-13 |
| **Phiên bản** | v7.2 (Đồng bộ kịch bản nghiệm thu với quy tắc đã sửa, thống nhất thẩm quyền thao tác rủi ro cao — Thay thế v7.1) |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`contacts-srs.md`](./contacts-srs.md), [`omnichat-srs.md`](./omnichat-srs.md), [`deals-pipeline-srs.md`](./deals-pipeline-srs.md), [`tasks-srs.md`](./tasks-srs.md), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`object-manager-srs.md`](./object-manager-srs.md) |

## Quy ước sử dụng tài liệu

Tài liệu này đặc tả **nghiệp vụ chuẩn cần đạt được** cho phân hệ Tickets, không phải bản ghi lại những gì hệ thống hiện đang làm. Mỗi quy tắc nghiệp vụ (BR) là yêu cầu đội phát triển phải hiện thực đúng theo; đội phát triển làm theo tài liệu này, tài liệu không được viết lại theo những gì đội phát triển đã làm.

**Quy ước nhãn trạng thái** (gắn cho từng FEAT):
- **`[Đã triển khai]`** — Đang vận hành thực tế, khách hàng dùng được ngay, đáp ứng đầy đủ các quy tắc nghiệp vụ mô tả trong FEAT đó.
- **`[Đã triển khai một phần]`** — Có trong hệ thống nhưng chưa đáp ứng đủ toàn bộ quy tắc nghiệp vụ của FEAT; phần còn thiếu được nêu rõ trong mục `Khoảng cách cần bổ sung` ngay trong FEAT đó.
- **`[Yêu cầu mới]`** — Chưa có trong hệ thống, cần lên kế hoạch phát triển từ đầu.

Nhãn nói về **phần việc thuộc bản thân FEAT đó**. Một số FEAT đã hoàn chỉnh phần việc của mình nhưng chỉ phát huy đầy đủ tác dụng khi một FEAT khác được hoàn thành — những FEAT này giữ nhãn `[Đã triển khai]` và có thêm dòng `Phụ thuộc chưa sẵn sàng` nêu rõ đang chờ gì và hệ quả nghiệp vụ trong thời gian chờ. **Khi nghiệm thu, phải đọc dòng này cùng với nhãn**: nhãn `[Đã triển khai]` không đồng nghĩa với việc toàn bộ quy tắc nghiệp vụ của FEAT đã phát huy tác dụng trên thực tế.

**Ghi chú phiên bản:**

- **v7.0** bổ sung quy tắc nghiệp vụ và tiêu chí nghiệm thu cho toàn bộ tính năng, đồng thời đặc tả thêm các nhóm chức năng trước đây còn thiếu nhưng bắt buộc phải có để vận hành một phòng Dịch vụ Khách hàng nhiều nhân sự: điều phối theo ca trực và hàng đợi chung, thông báo cho tư vấn viên, báo cáo và giám sát hiệu suất, nhật ký truy vết, và tuân thủ bảo vệ dữ liệu cá nhân theo Nghị định 13/2023/NĐ-CP.
- **v7.1** xử lý các vấn đề phát hiện qua thẩm định độc lập ở ba lăng kính (khách hàng chuẩn bị ký hợp đồng, nhóm kiểm thử nghiệm thu, kiến trúc nghiệp vụ): giải quyết xung đột giữa các nhóm tính năng khi vận hành đồng thời (khảo sát hài lòng với tự động đóng vé và gộp vé; ẩn danh hóa với Thùng rác và nhật ký truy vết; ca trực với cam kết phục vụ liên tục; vé con với hạn mức năng lực); bổ sung quy tắc vòng đời dữ liệu khi doanh nghiệp đổi cấu hình giữa chừng (trạng thái vé, ngày nghỉ lễ); bổ sung mục Giả định & Phụ thuộc; bổ sung quy tắc xử lý thao tác đồng thời; thống nhất thẩm quyền các thao tác khó hoàn tác theo hướng cấu hình được cho từng doanh nghiệp; bổ sung KPI cho ba vấn đề nghiệp vụ trước đây chưa có chỉ số đo.
- **v7.2** rà soát ngược toàn bộ mục 6 theo các quy tắc đã sửa ở v7.1 — sửa hai kịch bản nghiệm thu đã trở nên nói ngược quy tắc mới (khảo sát sau khi mở lại vé; cảnh báo sớm xét theo từng mốc cam kết), bổ sung 5 kịch bản cho các quy tắc mới trước đó chưa có cách nghiệm thu; thống nhất thẩm quyền các thao tác rủi ro cao (gộp vé cấu hình được theo từng doanh nghiệp; xóa vĩnh viễn, phục hồi và xuất toàn bộ dữ liệu giữ ở cấp Trưởng phòng), tách hai cấp phạm vi xuất dữ liệu; chốt thời điểm ghi nhận cam kết phản hồi đầu tiên khi cập nhật lan truyền từ vé cha để độ trễ kỹ thuật không tạo vi phạm giả. Hoàn thiện lần cuối: lượng hóa cửa sổ cảnh báo ghi đè cùng một trường, bổ sung tham số số lần thử lại khi gửi thông báo, đưa thực thể Vé Khiếu nại vào từ điển dữ liệu và ma trận quyền, thống nhất thuật ngữ phạm vi xuất dữ liệu.

**Tình trạng thẩm định:** Tài liệu đã qua bốn vòng thẩm định độc lập với các lăng kính khác nhau (khách hàng chuẩn bị ký hợp đồng, nhóm kiểm thử nghiệm thu, kiến trúc nghiệp vụ, và rà soát tổng thể). Vòng cuối kết luận **đạt chuẩn nghiệp vụ, đủ điều kiện phát hành** cho cả ba mục đích: làm căn cứ hợp đồng với khách hàng, làm đầu vào cho đội phát triển, và làm cơ sở viết bộ kịch bản nghiệm thu.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả nghiệp vụ quản trị Vé hỗ trợ khách hàng (Tickets), Cam kết chất lượng dịch vụ (SLA) và Đánh giá sự hài lòng (CSAT) trong hệ thống CRM B2B SaaS:

1. **Tạo & Quản lý Vé Hỗ trợ:** Tư vấn viên hoặc khách hàng (qua các kênh trò chuyện đã kết nối) tạo vé để theo dõi một yêu cầu hỗ trợ từ lúc tiếp nhận tới lúc xử lý xong.
2. **Cấp Mã Định danh Vé Duy nhất:** Mỗi vé được cấp một mã số duy nhất, tuần tự theo từng doanh nghiệp sử dụng hệ thống (ví dụ `TKT-00421`), dùng để tra cứu và đối chiếu xuyên suốt vòng đời xử lý.
3. **Cam kết Chất lượng Dịch vụ (SLA):** Kiểm soát các mốc thời hạn cam kết — thời hạn phản hồi đầu tiên và thời hạn xử lý dứt điểm — theo lịch làm việc cấu hình riêng cho từng doanh nghiệp.
4. **Tạm dừng Đồng hồ SLA Công bằng:** Doanh nghiệp có thể định nghĩa các trạng thái vé "chờ bên khác phản hồi" (ví dụ chờ khách hàng, chờ đối tác) khiến đồng hồ SLA tạm ngưng đếm, tránh phạt oan tư vấn viên khi việc chậm trễ không do họ.
5. **Phân công & Điều phối:** Vé được gán cho tư vấn viên (thủ công hoặc theo quy tắc điều phối tự động của doanh nghiệp) và có thể bàn giao lại giữa các tư vấn viên.
6. **Không gian Làm việc của Tư vấn viên:** Trao đổi qua lại với khách hàng, ghi chú nội bộ giữa các nhân viên, đính kèm tài liệu minh chứng.
7. **Cảnh báo & Leo thang Vi phạm SLA:** Tự động cảnh báo trước khi sắp hết hạn và leo thang lên cấp quản lý khi đã vi phạm.
8. **Khảo sát Sự Hài lòng Khách hàng (CSAT):** Sau khi vé được xử lý xong, hệ thống chuẩn bị sẵn một đường dẫn khảo sát 1-5 sao để gửi tới khách hàng.
9. **Gộp Vé, Tách Vé & Quản lý Sự cố Diện rộng:** Gộp các vé trùng lặp vào một vé duy nhất; tách một yêu cầu chứa nhiều vấn đề thành các vé riêng; gom nhiều vé của một sự cố lớn dưới một vé cha để cập nhật và xử lý đồng loạt.
10. **Liên kết & Cảnh báo Rủi ro tới Bộ phận Bán hàng:** Liên kết vé hỗ trợ với cơ hội bán hàng đang đàm phán và cảnh báo nhân viên kinh doanh khi khách hàng của họ đang có sự cố nghiêm trọng chưa được giải quyết.
11. **Báo cáo & Giám sát Hiệu suất:** Bảng điều khiển hàng đợi thời gian thực và bộ báo cáo đo lường mức độ tuân thủ cam kết, hiệu suất tư vấn viên và khối lượng công việc — cơ sở để đánh giá chất lượng dịch vụ và báo cáo cho khách hàng theo hợp đồng.
12. **Truy vết & Tuân thủ Bảo vệ Dữ liệu Cá nhân:** Ghi nhận nhật ký thay đổi bất biến phục vụ đối soát tranh chấp, và đáp ứng yêu cầu về vòng đời dữ liệu, ẩn danh hóa theo Nghị định 13/2023/NĐ-CP.

### 1.2 Phạm vi

Tài liệu bao gồm 12 nhóm chức năng cốt lõi:
- **Nhóm A: Quản trị Vé Hỗ trợ Cơ bản (Tickets CRUD):** Tạo mới, chỉnh sửa, mã định danh duy nhất, liên kết khách hàng/doanh nghiệp, phân loại nhiều cấp.
- **Nhóm B: Phân loại & Trường Dữ liệu Tùy biến:** Danh mục phân loại nhiều cấp, trường tùy biến theo cấu hình đối tượng Ticket.
- **Nhóm C: Cam kết Chất lượng Dịch vụ (SLA & Business Hours):** Đồng hồ SLA theo mốc phản hồi đầu tiên và xử lý dứt điểm, lịch làm việc cấu hình theo tenant, tạm dừng đồng hồ SLA, chính sách SLA theo hạng khách hàng.
- **Nhóm D: Điều phối & Phân công:** Hàng đợi chung, trạng thái sẵn sàng và ca trực của tư vấn viên, phân bổ xoay vòng có hạn mức năng lực, phân bổ theo kỹ năng chuyên môn, bàn giao và chuyển vé hàng loạt.
- **Nhóm E: Tác nghiệp Xử lý & Phản hồi Khách hàng:** Dòng hội thoại, ghi chú nội bộ, đính kèm tài liệu, thông báo cho tư vấn viên, cảnh báo trùng thao tác.
- **Nhóm F: Leo thang & Cảnh báo Vi phạm SLA:** Cảnh báo sớm trước khi hết hạn, leo thang tự động và leo thang thủ công tới cấp quản lý, tự động chuyển người phụ trách.
- **Nhóm G: Đóng Vé & Khảo sát Hài lòng:** Ghi nhận nguyên nhân xử lý, mở lại vé, khảo sát CSAT.
- **Nhóm H: Thao tác Hàng loạt, Nhập/Xuất & Thùng rác:** Gắn nhãn hàng loạt, nhập/xuất dữ liệu, xóa mềm và phục hồi.
- **Nhóm I: Quản trị Quy trình Nâng cao & Rủi ro Khách hàng:** Gộp vé trùng lặp, tách vé, quản lý sự cố diện rộng theo mô hình vé cha - vé con, liên kết và cảnh báo rủi ro sang Cơ hội bán hàng, chuyển giải pháp thành bài viết tri thức, giám sát hướng dẫn nhân viên, theo dõi giờ hỗ trợ tính phí.
- **Nhóm J: Báo cáo & Giám sát Hiệu suất:** Bảng điều khiển hàng đợi thời gian thực, báo cáo tuân thủ SLA, báo cáo hiệu suất tư vấn viên, báo cáo khối lượng theo thời gian và theo danh mục.
- **Nhóm K: Nhật ký Thao tác & Truy vết:** Ghi nhận lịch sử thay đổi các trường quan trọng trên vé phục vụ đối soát và giải quyết tranh chấp.
- **Nhóm L: Vòng đời Dữ liệu & Tuân thủ Bảo vệ Dữ liệu Cá nhân:** Lưu trữ dài hạn, ẩn danh hóa, xử lý yêu cầu xóa dữ liệu cá nhân theo Nghị định 13/2023/NĐ-CP.

**Ngoài phạm vi (thuộc về các tài liệu SRS chuyên biệt khác):**
- **Nghiệp vụ Trực chat Trực tuyến & Tiếp nhận Đa kênh Tự động:** Thuộc về [`omnichat-srs.md`](./omnichat-srs.md). Việc chuyển một cuộc trò chuyện đa kênh thành vé hỗ trợ hiện là thao tác thủ công của tư vấn viên, không tự động.
- **Nghiệp vụ Quản trị Hồ sơ Khách hàng:** Thuộc về [`contacts-srs.md`](./contacts-srs.md).
- **Nghiệp vụ Quản lý Cơ hội & Phễu Bán hàng:** Thuộc về [`deals-pipeline-srs.md`](./deals-pipeline-srs.md).
- **Nghiệp vụ Cơ sở Tri thức:** Việc soạn, duyệt, phân loại và xuất bản bài viết tri thức nằm ngoài phạm vi. FEAT-31 chỉ đặc tả việc chuyển nội dung giải pháp từ vé sang dạng bản nháp bài viết và bàn giao cho quy trình đó.
- **Nghiệp vụ Hợp đồng & Xuất hóa đơn:** Việc quản lý hợp đồng dịch vụ và lập hóa đơn nằm ngoài phạm vi. FEAT-33 và FEAT-40 chỉ tiêu thụ thông tin hợp đồng/hạng khách hàng và cung cấp lại số giờ tính phí.
- **Nghiệp vụ Quản lý Nhân sự:** Quy trình xin và duyệt nghỉ phép nằm ngoài phạm vi. FEAT-35 chỉ tiếp nhận kết quả kỳ nghỉ đã duyệt để cơ chế phân bổ tránh giao việc cho người vắng mặt.

### 1.3 Giả định & Phụ thuộc

Các tính năng trong tài liệu này chỉ vận hành đúng khi những điều kiện dưới đây được đáp ứng. Đây là căn cứ để hai bên xác định trách nhiệm khi một tính năng không hoạt động vì nguyên nhân nằm ngoài phân hệ Tickets.

| Phụ thuộc | Tính năng cần tới | Hệ quả nếu chưa sẵn sàng |
| --- | --- | --- |
| Hệ thống gửi thư điện tử hoạt động | BR-20.2 (gửi khảo sát), BR-22.3 (nhắc trước khi đóng), BR-27.3 (báo khách hàng vé bị gộp), BR-37.2, BR-43.8 | Khách hàng không nhận được khảo sát và thông báo; KPI-03 không thu được số liệu. Cách xử lý khi gửi thất bại quy định tại BR-37.5 |
| Kênh tiếp nhận đa kênh còn kết nối (thư điện tử, chat, Zalo OA) | BR-27.3, BR-22.3, BR-41.4 | Không gửi được thông báo về đúng kênh khách hàng đã dùng; phải chuyển sang kênh dự phòng theo BR-37.5 |
| Danh mục ngày nghỉ lễ được khai báo cho năm đang xét | BR-08.4, BR-08.6 | Hạn chót cam kết tính nhầm ngày lễ thành ngày làm việc; hệ thống cảnh báo Quản trị viên khi năm chưa được khai báo |
| Sơ đồ tổ chức khai báo cấp quản lý trực tiếp | BR-18.2, BR-18.4 | Không xác định được báo lên ai khi leo thang; phải rơi về người nhận mặc định do doanh nghiệp chỉ định |
| Kênh gửi tin nhắn hoặc gọi tự động | BR-37.4 | Cảnh báo ngoài giờ không đánh thức được người trực; cam kết phục vụ liên tục cả ngày đêm trở thành không thực hiện được |
| Hồ sơ khách hàng và hạng khách hàng (phân hệ Contacts) | BR-03.1, BR-03.2, BR-40.1 | Không áp dụng được cam kết riêng theo hạng khách hàng |
| Thông tin hợp đồng dịch vụ | BR-40.1, BR-43.7 | Không áp dụng được cam kết riêng theo hợp đồng và không đối soát được giờ tính phí |
| Kỳ nghỉ phép đã duyệt từ quy trình nhân sự | BR-35.4 | Vé vẫn được giao cho người đang nghỉ |
| Tiến trình nền chạy đúng lịch | NFR-04, NFR-07, FEAT-17, FEAT-18, FEAT-22 | Cảnh báo, leo thang và đóng vé tự động không kích hoạt đúng thời điểm |

### 1.4 Đối tượng đọc

- **Product Owner / Business Analyst:** Căn cứ quản lý backlog, tiêu chí nghiệm thu và thiết kế quy trình Helpdesk.
- **Đội ngũ Kỹ sư Phát triển (Frontend / Backend):** Căn cứ thiết kế API, schemas dữ liệu, động cơ tính toán SLA theo lịch làm việc.
- **Đội ngũ Đảm bảo Chất lượng (QA/QC):** Căn cứ thiết kế ma trận kiểm thử đồng hồ SLA, kiểm thử leo thang vi phạm và kiểm thử gộp vé.
- **Trưởng phòng Dịch vụ Khách hàng & Giám đốc Vận hành:** Căn cứ thiết lập chính sách cam kết dịch vụ, giám sát hiệu suất và nâng cao chỉ số hài lòng khách hàng.

### 1.5 Thuật ngữ & Viết tắt

| Thuật ngữ | Định nghĩa nghiệp vụ |
| --- | --- |
| **Vé Hỗ trợ (Ticket)** | Bản ghi yêu cầu hỗ trợ, phản ánh sự cố hoặc câu hỏi của khách hàng cần được tiếp nhận và xử lý theo quy trình. |
| **SLA (Service Level Agreement)** | Cam kết chất lượng dịch vụ giữa doanh nghiệp và khách hàng về thời gian phản hồi và thời gian xử lý sự cố. |
| **SLA Phản hồi Đầu tiên (First Response SLA)** | Thời gian tối đa từ lúc vé được tạo đến khi tư vấn viên gửi phản hồi công khai đầu tiên tới khách hàng. |
| **SLA Xử lý Dứt điểm (Resolution SLA)** | Thời gian tối đa từ lúc vé được tạo đến khi vé chuyển sang một trạng thái kết thúc dạng "đã xử lý xong". |
| **Tạm dừng SLA (SLA Clock Pause)** | Trạng thái đóng băng đồng hồ đếm ngược SLA khi vé đang ở một trạng thái mà doanh nghiệp đánh dấu là "không tính giờ cam kết" (ví dụ đang chờ khách hàng phản hồi). |
| **Ghi chú Nội bộ (Internal Note)** | Trao đổi nghiệp vụ giữa các nhân viên trong nội bộ công ty trên vé hỗ trợ, khách hàng hoàn toàn không nhìn thấy. |
| **Quyền Xử lý Kết thúc Vé (Resolve)** | Quyền được phép chuyển một vé sang trạng thái kết thúc dạng "đã xử lý xong", hoặc mở lại một vé đã ở trạng thái đó. Đây là quyền riêng, tách biệt với quyền "sửa" (edit) thông thường, vì hành động này thay đổi tình trạng cam kết SLA của vé. |
| **Khảo sát Sự Hài lòng (CSAT Survey)** | Đường dẫn đánh giá mức độ hài lòng (1-5 sao) được chuẩn bị sẵn khi vé chuyển sang trạng thái kết thúc, để gửi tới khách hàng. |
| **Vé Cha - Vé Con (Parent-Child Tickets)** | Mô hình quản lý sự cố diện rộng: gom nhiều vé của các khách hàng khác nhau vào dưới một vé cha đại diện cho sự cố, để cập nhật tiến độ và xử lý đồng loạt từ một đầu mối duy nhất (xem FEAT-28). |
| **Gộp Vé (Ticket Merging)** | Hợp nhất một vé trùng lặp (vé phụ) vào một vé chính, giữ lại toàn bộ nội dung trao đổi và đóng vé phụ lại. |
| **Tách Vé (Ticket Splitting)** | Tách một phần nội dung của vé thành một vé mới khi một yêu cầu chứa nhiều vấn đề cần xử lý riêng biệt. |
| **Hàng đợi Chung (Queue)** | Nơi chứa các vé đã xác định nhóm phụ trách nhưng chưa có người cụ thể nhận xử lý; mọi thành viên trong nhóm đều nhìn thấy và có thể tự nhận việc. |
| **Trạng thái Sẵn sàng (Availability)** | Tình trạng tư vấn viên tự khai báo trong ca trực, cho biết họ có đang nhận việc mới được hay không. |
| **Ẩn danh hóa (Anonymization)** | Thay thế các thông tin định danh cá nhân trên vé bằng dấu hiệu đã ẩn danh, đồng thời giữ lại số liệu thống kê phi định danh — cách đáp ứng yêu cầu xóa dữ liệu cá nhân mà không phá vỡ tính toàn vẹn của báo cáo lịch sử. |
| **Nhật ký Thay đổi (Audit Trail)** | Bản ghi bất biến về việc ai đã thay đổi thông tin gì trên vé và vào lúc nào, phục vụ đối soát khi có tranh chấp. |

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

1. **Yêu cầu Hỗ trợ Thiếu Đầu mối Theo dõi:** Nếu không có một bản ghi vé tập trung, nhân viên dễ bỏ sót hoặc mất dấu tiến độ xử lý một yêu cầu của khách hàng.
2. **Vi phạm Cam kết Dịch vụ Không Được Phát hiện Sớm:** Khách hàng quan trọng gặp sự cố nghiêm trọng nhưng vé bị trôi, không có cảnh báo trước khi hết hạn dẫn đến vi phạm SLA.
3. **Đồng hồ SLA Bất công Khi Chờ Khách hàng:** Nhân viên đã gửi câu hỏi hướng dẫn nhưng khách hàng bận không trả lời; nếu đồng hồ vẫn chạy, nhân viên bị đánh giá vi phạm SLA oan.
4. **Phân bổ Vé Không Đồng đều:** Một nhân viên bị dồn quá nhiều vé trong khi người khác trống việc, nếu không có cơ chế giới hạn số vé mở tối đa và điều phối tự động.
5. **Nhiều Khách hàng Báo Cùng Một Sự cố:** Nhiều vé trùng lặp của cùng một sự việc gây khó theo dõi nếu không thể gộp lại hoặc nhóm chung dưới một đầu mối.
6. **Không Thu thập Được Đánh giá Thực tế từ Khách hàng:** Doanh nghiệp cần một cơ chế khảo sát mức độ hài lòng sau mỗi lần phục vụ để cải tiến chất lượng.
7. **Bất đồng Bộ giữa Bộ phận Hỗ trợ và Bộ phận Bán hàng:** Nhân viên kinh doanh cần biết khách hàng của mình có đang gặp sự cố hỗ trợ hay không trước khi tiếp tục đàm phán hợp đồng.
8. **Không Đo lường Được Chất lượng Dịch vụ:** Không có số liệu tin cậy về mức độ tuân thủ cam kết và hiệu suất từng tư vấn viên thì không thể đánh giá nhân sự, không xếp được lịch trực hợp lý, và không có căn cứ báo cáo cho khách hàng theo điều khoản hợp đồng.
9. **Gián đoạn khi Nhân sự Vắng mặt:** Nhân viên nghỉ ốm, nghỉ phép hoặc nghỉ việc để lại nhiều vé đang xử lý dở; nếu không chuyển giao nhanh, toàn bộ các vé đó trôi qua hạn cam kết trong khi không ai theo dõi.
10. **Rủi ro Pháp lý về Dữ liệu Cá nhân:** Vé hỗ trợ chứa nhiều dữ liệu cá nhân của khách hàng; doanh nghiệp cần chứng minh được khả năng đáp ứng yêu cầu xóa dữ liệu và kiểm soát việc ai đã truy xuất, xuất dữ liệu khách hàng ra ngoài.
11. **Lặp lại Cùng Một Loại Yêu cầu & Đào tạo Nhân sự Mới Chậm:** Cùng một câu hỏi được hỏi đi hỏi lại nhưng giải pháp chỉ nằm trong vé cũ, không ai tra được; nhân viên mới mất nhiều tháng mới đạt chuẩn phục vụ vì không có công cụ hướng dẫn tại chỗ.
12. **Không Đo được Chi phí Phục vụ từng Khách hàng:** Với hợp đồng bảo trì tính phí theo giờ, doanh nghiệp không có số liệu thời gian thực tế đã bỏ ra để xuất hóa đơn và để biết khách hàng nào đang tiêu tốn nguồn lực vượt giá trị hợp đồng.

### 2.2 Vai trò người dùng (Actor)

Quyền thao tác vé hỗ trợ (xem/tạo/sửa/xóa/gán/xử lý/nhập/xuất) được cấp theo vai trò và theo phạm vi (bản ghi được gán cho mình, bản ghi trong phòng ban, hoặc toàn bộ không gian làm việc), tùy cấu hình phân quyền của từng doanh nghiệp. Do một số thao tác khó hoặc không thể hoàn tác (xóa vĩnh viễn, gộp vé), vai trò quản lý được tách thành hai cấp để doanh nghiệp đặt các thao tác đó ở cấp phù hợp với mô hình tổ chức của mình:

| Actor | Mô tả vai trò và quyền hạn nghiệp vụ liên quan tới Tickets |
| --- | --- |
| **Khách hàng (Customer / End-user)** | Trao đổi với tư vấn viên qua các kênh đã kết nối; nhận phản hồi và chấm điểm khảo sát CSAT khi được gửi khảo sát. |
| **Support Agent** | Nhân viên hỗ trợ/CSKH tuyến đầu: tiếp nhận, xử lý, phản hồi, ghi chú nội bộ và cập nhật vé trong phạm vi được gán cho mình. |
| **Team Lead (Trưởng nhóm Hỗ trợ)** | Cấp quản lý vận hành hàng ngày trong phạm vi phòng ban/ca trực: giám sát hàng đợi vé, phân công lại vé quá tải, xử lý các vé được leo thang. Xuất được danh sách vé trong phạm vi nhóm mình (BR-25.5). Không có quyền xóa vĩnh viễn, phục hồi từ Thùng rác hay xuất toàn bộ dữ liệu không gian làm việc — các quyền này thuộc Support Manager và không hạ xuống cấp này trong bất kỳ cấu hình nào. Riêng quyền gộp vé mặc định không có nhưng doanh nghiệp mở rộng xuống được nếu mô hình vận hành cần (xem BR-27.7). |
| **Support Manager (Trưởng phòng Dịch vụ Khách hàng)** | Cấp quản lý chính sách và dữ liệu, phạm vi toàn bộ không gian làm việc: thực hiện và phê duyệt các thao tác khó hoàn tác (gộp vé, xóa vĩnh viễn, phục hồi từ Thùng rác), xuất báo cáo, thiết lập chính sách SLA/leo thang, xem báo cáo hiệu suất. Giữ quyền hoàn tác gộp vé trong mọi cấu hình. |
| **Sales Rep** | Xem vé hỗ trợ liên quan tới khách hàng/cơ hội bán hàng mình phụ trách để phối hợp chăm sóc. |
| **Administrator** | Toàn quyền cấu hình danh mục phân loại, chính sách SLA, quy tắc điều phối và toàn quyền truy cập dữ liệu vé trong không gian làm việc. |
| **Tiến trình Hệ thống (System Engine)** | Không phải người dùng — là hành vi tự động của hệ thống: đếm ngược đồng hồ SLA, tạm dừng/kích hoạt lại đồng hồ khi đổi trạng thái, phát hiện cảnh báo/vi phạm và chuẩn bị khảo sát CSAT. Xuất hiện trong tài liệu để phân biệt rõ hành động nào là tự động, hành động nào cần một actor con người thực hiện. |

*Ghi chú:* "Team Lead" và "Support Manager" là hai cấp quyền hạn nghiệp vụ bắt buộc phải **phân biệt được** khi cấu hình phân quyền — hệ thống phải cho phép cấp một quyền cho cấp này mà không cấp cho cấp kia. Bản thân việc đặt thao tác nào ở cấp nào là quyết định của từng doanh nghiệp; tài liệu này chỉ quy định giá trị mặc định và các giới hạn không được hạ thấp. Chi tiết phân quyền của từng tính năng nêu tại mục 5.

### 2.3 Bảng tổng hợp tính năng nghiệp vụ

| Nhóm | Mã FEAT | Tên tính năng nghiệp vụ | Trạng thái |
| --- | --- | --- | --- |
| **A. Quản trị Vé Cơ bản** | `FEAT-01` | Tạo mới & Quản lý Vé Hỗ trợ Toàn diện (Ticket CRUD) | `[Đã triển khai]` |
| | `FEAT-02` | Cấp Mã Định danh Vé Duy nhất theo Doanh nghiệp (Ticket Number) | `[Đã triển khai]` |
| | `FEAT-03` | Liên kết Khách hàng Cá nhân & Doanh nghiệp (Contact & Account Linking) | `[Đã triển khai]` |
| **B. Phân loại & Tùy biến** | `FEAT-04` | Danh mục Phân loại Nhiều Cấp (Category Path, tối đa 5 cấp) | `[Đã triển khai]` |
| | `FEAT-05` | Trường Dữ liệu Tùy biến cho Vé Hỗ trợ (Custom Fields) | `[Đã triển khai]` |
| | `FEAT-06` | Ma trận Đánh giá Mức độ Tác động & Nghiêm trọng (Impact & Severity Matrix) | `[Đã triển khai một phần]` |
| **C. Cam kết Chất lượng SLA** | `FEAT-07` | Động cơ Đo lường Đồng hồ SLA (First Response & Resolution SLA) | `[Đã triển khai]` |
| | `FEAT-08` | Lịch Làm việc Doanh nghiệp Cấu hình được (Business Hours theo tenant) | `[Đã triển khai]` |
| | `FEAT-09` | Tự động Tạm dừng Đồng hồ SLA theo Trạng thái Vé (SLA Clock Pause) | `[Đã triển khai]` |
| | `FEAT-40` | Chính sách SLA theo Hạng Khách hàng & Hợp đồng Dịch vụ (Tiered SLA) | `[Đã triển khai một phần]` |
| **D. Phân công & Điều phối** | `FEAT-10` | Phân bổ Tự động Xoay vòng theo Hạn mức Năng lực (Round-Robin Capacity) | `[Đã triển khai]` |
| | `FEAT-11` | Phân bổ Thông minh theo Kỹ năng Chuyên môn (Skill-Based Routing) | `[Đã triển khai]` |
| | `FEAT-12` | Bàn giao & Phân công Lại Vé (Reassignment) | `[Đã triển khai]` |
| | `FEAT-34` | Hàng đợi Chung & Giám sát Vé Chưa có Người Xử lý (Unassigned Queue) | `[Đã triển khai]` |
| | `FEAT-35` | Trạng thái Sẵn sàng & Ca trực của Tư vấn viên (Agent Availability & Shift) | `[Đã triển khai]` |
| | `FEAT-36` | Chuyển Vé Hàng loạt khi Nhân sự Vắng mặt hoặc Nghỉ việc (Bulk Reassignment) | `[Đã triển khai]` |
| **E. Tác nghiệp Xử lý & Phản hồi** | `FEAT-13` | Dòng Trao đổi Vé Hỗ trợ (Conversation Thread) | `[Đã triển khai]` |
| | `FEAT-14` | Ghi chú Nội bộ Bảo mật giữa Nhân viên (Internal Notes) | `[Đã triển khai]` |
| | `FEAT-15` | Đính kèm Tài liệu & Hình ảnh (Attachments) | `[Đã triển khai]` |
| | `FEAT-16` | Thư viện Câu trả lời Mẫu Soạn sẵn (Canned Responses / Macros) | `[Đã triển khai]` |
| | `FEAT-37` | Thông báo cho Tư vấn viên về Vé được Gán & Khách hàng Phản hồi | `[Đã triển khai]` |
| | `FEAT-38` | Cảnh báo Trùng Thao tác khi Nhiều Người cùng Xử lý Một Vé (Collision Detection) | `[Đã triển khai]` |
| **F. Leo thang & Cảnh báo Vi phạm** | `FEAT-17` | Cảnh báo Sớm Nguy cơ Vi phạm SLA trước khi hết hạn (SLA Warning) | `[Đã triển khai]` |
| | `FEAT-18` | Tự động Leo thang khi Vi phạm SLA (Escalation: Cảnh báo cấp Quản lý / Tự động Chuyển việc) | `[Đã triển khai]` |
| | `FEAT-39` | Leo thang Thủ công & Xử lý Khiếu nại về Chất lượng Phục vụ (Manual Escalation) | `[Đã triển khai]` |
| **G. Đóng Vé & Khảo sát Hài lòng** | `FEAT-19` | Quy trình Giải quyết & Ghi nhận Nguyên nhân Xử lý (Resolution Code) | `[Đã triển khai]` |
| | `FEAT-20` | Khảo sát Đánh giá Sự Hài lòng 1-5 Sao (CSAT Survey) | `[Đã triển khai]` |
| | `FEAT-21` | Quy định Mở lại Vé đã Giải quyết (Ticket Reopening) | `[Đã triển khai]` |
| | `FEAT-22` | Tự động Đóng Vé sau Thời gian Không Phản hồi (Auto-Close) | `[Đã triển khai]` |
| **H. Hàng loạt, Nhập/Xuất & Thùng rác** | `FEAT-23` | Gắn Nhãn Hàng loạt cho Nhiều Vé (Bulk Tagging) | `[Đã triển khai]` |
| | `FEAT-24` | Nhập Dữ liệu Vé Hỗ trợ từ Tệp (Ticket Import) | `[Đã triển khai]` |
| | `FEAT-25` | Xuất Báo cáo Vé Hỗ trợ Bảo mật qua Đường dẫn Tải có Thời hạn (Secure Export) | `[Đã triển khai]` |
| | `FEAT-26` | Thùng rác Vé Hỗ trợ & Phục hồi Bản ghi (Recycle Bin) | `[Đã triển khai]` |
| **I. Quản trị Quy trình Nâng cao** | `FEAT-27` | Gộp Vé Trùng lặp (Ticket Merging) | `[Đã triển khai một phần]` |
| | `FEAT-41` | Tách Vé khi Một Yêu cầu Chứa Nhiều Vấn đề (Ticket Splitting) | `[Đã triển khai]` |
| | `FEAT-28` | Quản lý Sự cố Diện rộng theo Mô hình Vé Cha - Vé Con (Major Incident Management) | `[Đã triển khai]` |
| | `FEAT-29` | Liên kết Thủ công Vé Hỗ trợ với Cơ hội Bán hàng (Deal Linking) | `[Đã triển khai]` |
| | `FEAT-30` | Bắn Cờ Cảnh báo Rủi ro Kỹ thuật Tự động sang Bảng Kanban Deals | `[Đã triển khai]` |
| | `FEAT-31` | Chuyển đổi Giải pháp Xử lý thành Bài viết Tri thức (Draft to KB) | `[Đã triển khai]` |
| | `FEAT-32` | Giám sát Trực tiếp & Nhắc nhở Hậu trường (Whisper Coaching) | `[Đã triển khai]` |
| | `FEAT-33` | Theo dõi Thời lượng Hỗ trợ Thực tế & Giờ Tính phí (Support Time Tracking) | `[Đã triển khai]` |
| **J. Báo cáo & Giám sát Hiệu suất** | `FEAT-42` | Bảng điều khiển Hàng đợi Thời gian thực (Real-time Queue Dashboard) | `[Đã triển khai một phần]` |
| | `FEAT-43` | Báo cáo Tuân thủ SLA, Hiệu suất Tư vấn viên & Khối lượng Công việc | `[Đã triển khai]` |
| **K. Nhật ký Thao tác** | `FEAT-44` | Nhật ký Thay đổi & Truy vết Thao tác trên Vé (Audit Trail) | `[Đã triển khai]` |
| **L. Vòng đời Dữ liệu & Tuân thủ** | `FEAT-45` | Lưu trữ, Ẩn danh hóa & Xử lý Yêu cầu Xóa Dữ liệu Cá nhân (Nghị định 13) | `[Đã triển khai]` |

---

### 2.4 Mục tiêu kinh doanh & Chỉ số thành công (Business Objectives & KPIs)

| Mã | Vấn đề nghiệp vụ (mục 2.1) | Chỉ số đo lường (KPI Metric) | Giá trị mục tiêu | Tính năng đóng góp |
| --- | --- | --- | --- | --- |
| `KPI-01` | Tốc độ phản hồi ban đầu (2.1.1, 2.1.2) | Tỷ lệ tuân thủ cam kết Phản hồi Đầu tiên (%) | Thống nhất theo Phụ lục Hợp đồng SLA (khuyến nghị ≥ 95%) | FEAT-07, 08, 10, 17, 34, 35 |
| `KPI-02` | Tốc độ xử lý dứt điểm sự cố (2.1.2) | Tỷ lệ tuân thủ cam kết Xử lý Dứt điểm (%) | Thống nhất theo Phụ lục Hợp đồng SLA (khuyến nghị ≥ 90%) | FEAT-07, 08, 09, 18, 40 |
| `KPI-03` | Mức độ hài lòng của khách hàng (2.1.6) | Điểm đánh giá sự hài lòng trung bình (1-5) | Thống nhất theo Phụ lục Hợp đồng SLA (khuyến nghị ≥ 4.5/5.0) | FEAT-13, 16, 20 |
| `KPI-04` | Chất lượng xử lý lần đầu (2.1.1) | Tỷ lệ vé bị mở lại sau khi đã giải quyết (%) | Thống nhất theo Phụ lục Hợp đồng SLA (khuyến nghị < 6%) | FEAT-19, 21 |
| `KPI-05` | Phân bổ vé không đồng đều (2.1.4) | Chênh lệch khối lượng vé giữa các tư vấn viên trong cùng nhóm và ca trực | Thống nhất khi triển khai (khuyến nghị chênh lệch ≤ 20% so với mức trung bình nhóm) | FEAT-10, 11, 34, 35, 36 |
| `KPI-06` | Vé tồn đọng không ai xử lý (2.1.1) | Số vé nằm trong hàng đợi chung quá ngưỡng cảnh báo mà chưa có người nhận | Thống nhất khi triển khai (khuyến nghị = 0 tại mọi thời điểm trong giờ làm việc) | FEAT-34, 35, 42 |
| `KPI-07` | Năng suất xử lý sự cố diện rộng (2.1.5) | Thời gian trung bình để phản hồi toàn bộ khách hàng trong một sự cố diện rộng | Thống nhất khi triển khai | FEAT-27, 28, 41 |
| `KPI-08` | Bất đồng bộ Hỗ trợ - Bán hàng (2.1.7) | Tỷ lệ cơ hội bán hàng được cảnh báo kịp thời khi khách hàng đang có vé nghiêm trọng chưa xử lý xong | Thống nhất khi triển khai (khuyến nghị ≥ 95%) | FEAT-29, 30 |
| `KPI-09` | Yêu cầu lặp lại & đào tạo nhân sự (2.1.11) | Số bài viết tri thức mới tạo từ giải pháp xử lý vé trong kỳ, và thời gian trung bình để nhân viên mới đạt chuẩn phục vụ | Thống nhất khi triển khai | FEAT-31, 32 |
| `KPI-10` | Chi phí phục vụ theo khách hàng (2.1.12) | Tổng số giờ hỗ trợ thực tế và số giờ tính phí theo từng khách hàng trong kỳ | Thống nhất khi triển khai | FEAT-33 |
| `KPI-11` | Lạm dụng tạm dừng cam kết (2.1.3) | Tỷ lệ thời gian vé nằm ở trạng thái tạm dừng so với tổng thời gian xử lý, và số vé bị tạm dừng vượt ngưỡng cảnh báo | Thống nhất khi triển khai (khuyến nghị tỷ lệ tạm dừng ≤ 30%) | FEAT-09, 42, 43 |
| `KPI-12` | Gián đoạn khi nhân sự vắng mặt (2.1.9) | Số vé bị vi phạm cam kết trong các kỳ nghỉ phép và thời gian trung bình để chuyển giao hết vé của người vắng mặt | Thống nhất khi triển khai | FEAT-35, 36 |
| `KPI-13` | Tuân thủ bảo vệ dữ liệu cá nhân (2.1.10) | Tỷ lệ yêu cầu về dữ liệu cá nhân được xử lý đúng hạn 72 giờ (%) | 100% — đây là nghĩa vụ pháp lý, không có ngưỡng chấp nhận thấp hơn | FEAT-43, 44, 45 |

*Ghi chú về giá trị mục tiêu:* Cột "Giá trị mục tiêu" là khung để Trưởng phòng Dịch vụ Khách hàng và khách hàng thống nhất con số cụ thể theo cam kết hợp đồng của từng doanh nghiệp khi triển khai — các mức khuyến nghị trong ngoặc là điểm khởi đầu tham khảo theo chuẩn ngành Helpdesk B2B, không phải giá trị mặc định cứng của hệ thống.

*Ghi chú về cách đo:* Toàn bộ các chỉ số trên được tính và hiển thị trong nhóm chức năng Báo cáo & Giám sát Hiệu suất (FEAT-42, FEAT-43). Quy tắc tính tỷ lệ tuân thủ cam kết — bao gồm những loại vé nào bị loại khỏi mẫu số — được quy định tại `BR-43.2` và phải được in kèm trên báo cáo để hai bên đối soát khi có tranh chấp.

---

### 2.5 Luồng nghiệp vụ đầu–cuối (End-to-End Ticket Lifecycle)

```
[GĐ 1: Tiếp nhận & Cấp Mã Định danh]
    ├── Tư vấn viên tạo vé thủ công (trực tiếp, hoặc từ một cuộc trò chuyện đa kênh đang xử lý)
    ├── Hệ thống tự động sinh mã số duy nhất theo doanh nghiệp & gắn khách hàng/doanh nghiệp liên quan
    └── Kích hoạt Đồng hồ SLA Phản hồi Đầu tiên
         │
[GĐ 2: Phân loại & Điều phối]
    ├── Xác định Danh mục phân loại (nhiều cấp) và Mức độ ưu tiên
    ├── Vé được gán cho tư vấn viên đang trực và sẵn sàng: thủ công, hoặc theo quy tắc điều phối tự động (xoay vòng có hạn mức / theo kỹ năng)
    ├── NẾU không còn ai sẵn sàng hoặc mọi người đã đầy hạn mức → vé vào Hàng đợi chung & cảnh báo Trưởng nhóm
    └── NẾU vé nằm trong hàng đợi quá ngưỡng mà chưa ai nhận → cảnh báo tồn đọng (trước khi kịp vi phạm cam kết)
         │
[GĐ 3: Tác nghiệp Xử lý & Phản hồi Khách hàng]
    ├── Tư vấn viên gửi phản hồi công khai đầu tiên (hoàn tất cam kết Phản hồi Đầu tiên → còn lại cam kết Xử lý Dứt điểm)
    ├── Trao đổi qua lại với khách hàng, ghi chú nội bộ giữa các nhân viên
    ├── NẾU một yêu cầu chứa nhiều vấn đề → Tách vé để xử lý riêng
    ├── NẾU nhiều vé trùng lặp của cùng khách hàng → Gộp vé & báo cho khách hàng vé phụ
    └── NẾU chuyển sang trạng thái tạm dừng cam kết (ví dụ chờ khách hàng) → đồng hồ Xử lý Dứt điểm đóng băng
         │
[GĐ 4: Giám sát Hạn chót & Leo thang]
    ├── Tiến trình ngầm đếm ngược thời hạn cam kết theo lịch làm việc của doanh nghiệp
    ├── NẾU đã trôi qua tỷ lệ thời hạn cấu hình được → cảnh báo sớm tới người phụ trách
    ├── NẾU vi phạm cam kết → đánh dấu vé vi phạm & thực hiện hành động leo thang đã cấu hình
    └── NẾU khách hàng bức xúc hoặc khiếu nại chất lượng phục vụ → Tư vấn viên leo thang thủ công lên quản lý
         │
[GĐ 5: Giải quyết Sự cố & Khảo sát Hài lòng]
    ├── Bắt buộc ghi nhận nguyên nhân xử lý & tóm tắt giải pháp khi chuyển sang "đã xử lý xong"
    └── Hệ thống tự động gửi khảo sát hài lòng 1-5 sao tới khách hàng
         │
[GĐ 6: Mở lại hoặc Đóng Vé]
    ├── NẾU khách hàng phản hồi lại trong hạn cho phép → Người có quyền resolve mở lại vé (yêu cầu xác nhận rõ ràng); vé quay lại trạng thái đang xử lý và tiếp tục đếm số lần đã từng mở lại
    ├── NẾU khách hàng phản hồi lại sau khi đã quá hạn cho phép → Hệ thống tạo vé mới, liên kết tham chiếu tới vé cũ thay vì mở lại
    └── NẾU khách hàng không phản hồi thêm trong thời hạn cấu hình → Hệ thống nhắc trước rồi tự động đóng hoàn tất vé
         │
[GĐ 7: Đo lường, Truy vết & Vòng đời Dữ liệu]
    ├── Chỉ số tuân thủ cam kết, hiệu suất tư vấn viên và khối lượng công việc được tổng hợp lên báo cáo & bảng điều khiển
    ├── Mọi thay đổi quan trọng trên vé được ghi vào nhật ký bất biến phục vụ đối soát
    └── Hết thời hạn lưu trữ → vé chuyển sang lưu trữ dài hạn; khi khách hàng yêu cầu xóa dữ liệu cá nhân → ẩn danh hóa, giữ lại số liệu thống kê phi định danh
```

---

## 3. Đặc tả yêu cầu chức năng

## A. QUẢN TRỊ VÉ HỖ TRỢ CƠ BẢN (TICKETS MANAGEMENT)

### FEAT-01 — Tạo mới & Quản lý Vé Hỗ trợ Toàn diện (Ticket CRUD) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép tư vấn viên tiếp nhận và tạo vé thủ công, cập nhật tiêu đề, mô tả sự cố, kênh tiếp nhận và theo dõi tiến độ xử lý. Một vé cũng có thể được tạo từ một cuộc trò chuyện đa kênh đang xử lý để giữ lại bối cảnh trao đổi trước đó.

**Actor:** Support Agent, Team Lead, Support Manager.

**Quy tắc nghiệp vụ:**
- `BR-01.1 (Thông tin bắt buộc)`: Tiêu đề vé, Kênh tiếp nhận, Mức độ ưu tiên, Người yêu cầu.
- `BR-01.2 (Trạng thái vé cấu hình được theo doanh nghiệp)`: Không có một bộ trạng thái cố định áp dụng chung cho mọi doanh nghiệp. Mỗi doanh nghiệp tự định nghĩa danh sách trạng thái vé của mình (tên hiển thị, màu sắc, thứ tự) và đánh dấu cho từng trạng thái:
  - Có phải trạng thái mặc định khi tạo vé mới hay không.
  - Có phải trạng thái kết thúc hay không, và nếu có thì thuộc dạng "đã xử lý xong" hay "đã đóng hoàn tất" (phân biệt hai dạng này để tách rõ thời điểm xử lý xong và thời điểm đóng hẳn hồ sơ).
  - Có tạm dừng đồng hồ SLA khi vé ở trạng thái đó hay không (xem FEAT-09).
- `BR-01.3 (Khóa vé đã gộp)`: Vé đã bị gộp vào một vé khác (xem FEAT-27) bị khóa, không thể chỉnh sửa thêm.
- `BR-01.4 (Bảo vệ trạng thái đang được sử dụng)`: Trạng thái đang có vé sử dụng **không xóa được**, chỉ đánh dấu **ngừng sử dụng** — trạng thái đó biến mất khỏi danh sách chọn cho vé mới nhưng vé cũ vẫn giữ nguyên, để không phát sinh vé mất trạng thái và báo cáo lịch sử không bị hụt dữ liệu. Nguyên tắc này giống cách bảo vệ danh mục phân loại tại BR-04.2. Doanh nghiệp phải luôn có ít nhất một trạng thái mặc định và một trạng thái kết thúc dạng "đã xử lý xong"; hệ thống từ chối thao tác khiến vi phạm điều kiện này, vì thiếu chúng thì không tạo được vé mới và không đo được cam kết xử lý dứt điểm (BR-07.4).
- `BR-01.5 (Đổi dấu hiệu nghiệp vụ của trạng thái)`: Khi doanh nghiệp đổi dấu hiệu của một trạng thái đang được sử dụng (đánh dấu hoặc bỏ đánh dấu "tạm dừng SLA", đổi dạng kết thúc), thay đổi **chỉ áp dụng cho vé phát sinh sau thời điểm sửa**; vé đang mở giữ nguyên cách tính đã áp dụng cho tới khi kết thúc. Lý do: nếu áp dụng ngay cho vé đang mở, hàng trăm vé đang dừng đồng hồ sẽ đột ngột chạy lại và một số lập tức thành vi phạm — doanh nghiệp bị mất cam kết vì một thao tác cấu hình chứ không phải vì chất lượng phục vụ. Hệ thống cảnh báo trước số vé đang chịu ảnh hưởng để người cấu hình biết quy mô trước khi xác nhận. Nguyên tắc này thống nhất với BR-08.5 (đổi lịch làm việc) và BR-40.2 (đổi chính sách cam kết).


**Tiêu chí chấp nhận:** Xem Kịch bản 1, Kịch bản 30 và Kịch bản 43 (mục 6).

---

### FEAT-02 — Cấp Mã Định danh Vé Duy nhất theo Doanh nghiệp (Ticket Number) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Mỗi vé khi tạo được hệ thống tự động cấp một mã số duy nhất, tăng dần tuần tự riêng cho từng doanh nghiệp (ví dụ `TKT-00421`), dùng để tra cứu nhanh và trao đổi với khách hàng qua điện thoại hoặc email.

**Actor:** Tiến trình Hệ thống (cấp mã tự động); mọi vai trò có quyền xem vé đều dùng mã này để tra cứu.

**Quy tắc nghiệp vụ:**
- `BR-02.1 (Duy nhất và không tái sử dụng)`: Mã số không bao giờ bị cấp trùng hoặc dùng lại, kể cả khi nhiều người cùng tạo vé hoặc khi nhập dữ liệu hàng loạt xảy ra đồng thời. Vé bị xóa cũng không trả mã số về để cấp lại cho vé khác, vì khách hàng có thể vẫn đang giữ mã cũ trong email trao đổi.
- `BR-02.2 (Không đổi theo vòng đời vé)`: Mã số vé giữ nguyên suốt vòng đời, kể cả khi vé bị chuyển người phụ trách, đổi phân loại, gộp hoặc tách.


**Tiêu chí chấp nhận:** Xem Kịch bản 1 (mục 6).

---

### FEAT-03 — Liên kết Khách hàng Cá nhân & Doanh nghiệp `[Đã triển khai]`

**Mô tả nghiệp vụ:** Liên kết vé với hồ sơ Khách hàng cá nhân (người trực tiếp gửi yêu cầu) và Doanh nghiệp mà họ thuộc về, giúp tư vấn viên nắm được bối cảnh khách hàng khi xử lý: lịch sử các vé trước đó, hạng dịch vụ đang áp dụng, và các cơ hội bán hàng đang diễn ra.

**Actor:** Support Agent, Team Lead, Support Manager.

**Quy tắc nghiệp vụ:**
- `BR-03.1 (Một người yêu cầu cho mỗi vé)`: Mỗi vé gắn với đúng một khách hàng cá nhân là người yêu cầu chính. Trường hợp nhiều người cùng quan tâm tới một vé được xử lý bằng danh sách người theo dõi, không phải bằng nhiều người yêu cầu.
- `BR-03.2 (Doanh nghiệp suy ra từ khách hàng)`: Doanh nghiệp liên quan được tự động điền theo hồ sơ của khách hàng cá nhân. Tư vấn viên sửa lại được trong trường hợp một người làm việc cho nhiều doanh nghiệp.
- `BR-03.3 (Hiển thị bối cảnh khách hàng)`: Màn hình xử lý vé hiển thị tóm tắt bối cảnh: hạng khách hàng, số vé đang mở và đã đóng trước đó, điểm hài lòng trung bình đã nhận, cơ hội bán hàng đang mở (nếu có).
- `BR-03.4 (Không mất dữ liệu khi hồ sơ khách hàng thay đổi)`: Nếu hồ sơ khách hàng bị gộp hoặc xóa, các vé liên quan vẫn giữ được lịch sử và được trỏ sang hồ sơ còn lại sau khi gộp; vé không bao giờ bị xóa theo hồ sơ khách hàng.


**Tiêu chí chấp nhận:** Xem Kịch bản 30 (mục 6).

---

## B. PHÂN LOẠI & TÙY BIẾN

### FEAT-04 — Danh mục Phân loại Nhiều Cấp (Category Path) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cấu trúc phân loại nhiều tầng (tối đa 5 cấp) giúp gom nhóm sự cố chính xác theo mức độ chi tiết doanh nghiệp mong muốn (ví dụ: *"Lỗi Kỹ thuật"* → *"Phần mềm"* → *"Không Đăng Nhập Được"*). Phân loại chính xác là cơ sở để tìm nguyên nhân gốc rễ của các sự cố lặp lại (BR-43.4) và để điều phối vé tới đúng nhóm chuyên môn.

**Actor:** Support Agent, Team Lead (chọn phân loại khi xử lý); Administrator (thiết lập cây danh mục).

**Quy tắc nghiệp vụ:**
- `BR-04.1 (Chọn tới cấp cuối)`: Khi phân loại, người dùng phải chọn tới nút cuối của nhánh đã chọn. Chọn dừng ở cấp giữa bị từ chối, vì phân loại không đủ chi tiết sẽ làm báo cáo nguyên nhân mất giá trị.
- `BR-04.2 (Ngừng sử dụng danh mục cũ)`: Danh mục không còn dùng được đánh dấu ngừng sử dụng thay vì xóa. Danh mục đã ngừng không xuất hiện khi phân loại vé mới, nhưng các vé cũ vẫn giữ nguyên phân loại để báo cáo lịch sử không bị sai lệch.
- `BR-04.3 (Đổi tên không ảnh hưởng vé cũ)`: Đổi tên hiển thị của một danh mục áp dụng cho cả vé cũ lẫn vé mới, vì đây là cùng một khái niệm nghiệp vụ được diễn đạt lại.


**Tiêu chí chấp nhận:** Xem Kịch bản 30 (mục 6).

---

### FEAT-05 — Trường Dữ liệu Tùy biến cho Vé Hỗ trợ `[Đã triển khai]`

**Mô tả nghiệp vụ:** Doanh nghiệp có thể tự định nghĩa thêm các trường thông tin đặc thù cần thu thập trên vé hỗ trợ (ví dụ "Mã Giao dịch Ngân hàng" cho các vé liên quan thanh toán), áp dụng chung cho toàn bộ vé hỗ trợ của doanh nghiệp.

**Actor:** Administrator (định nghĩa trường); Support Agent, Team Lead, Support Manager (nhập dữ liệu).

**Quy tắc nghiệp vụ:**
- `BR-05.1 (Trường bắt buộc)`: Administrator đánh dấu được trường nào bắt buộc nhập. Trường bắt buộc phải có giá trị trước khi tư vấn viên chuyển vé sang trạng thái kết thúc dạng **"đã xử lý xong"**, không chặn ở bước tạo vé — vì lúc tiếp nhận ban đầu tư vấn viên thường chưa có đủ thông tin. Ràng buộc này **không áp dụng cho việc hệ thống tự động chuyển vé sang "đã đóng hoàn tất"** theo FEAT-22, vì lúc đó thông tin đã được kiểm tra ở bước trước và tiến trình tự động không có ai để yêu cầu nhập liệu.
- `BR-05.2 (Không mất dữ liệu khi ngừng dùng trường)`: Khi một trường tùy biến bị ngừng sử dụng, dữ liệu đã nhập trên các vé cũ vẫn được giữ và tra cứu được.
- `BR-05.3 (Đưa vào báo cáo và xuất dữ liệu)`: Các trường tùy biến phải lọc được trong danh sách vé và có mặt trong tệp xuất dữ liệu, nếu không thì việc thu thập thông tin đặc thù trở nên vô nghĩa.

*Lưu ý phạm vi:* Hiện tại các trường tùy biến không tự động thay đổi theo từng nhánh phân loại cụ thể (ví dụ chọn danh mục "Lỗi Thanh toán" không tự động hiện thêm trường riêng cho danh mục đó) — xem mục 7.3 về kế hoạch bổ sung.


**Tiêu chí chấp nhận:** Xem Kịch bản 31 (mục 6).

---

### FEAT-06 — Ma trận Đánh giá Mức độ Tác động & Nghiêm trọng `[Đã triển khai một phần]`

**Mô tả nghiệp vụ:** Mức độ ưu tiên do tư vấn viên tự chọn thường thiếu nhất quán giữa các người khác nhau. Tính năng này giúp chuẩn hóa: tư vấn viên chỉ cần đánh giá hai yếu tố khách quan, hệ thống tự suy ra mức ưu tiên theo ma trận doanh nghiệp đã thống nhất.
- **Mức độ Tác động (Impact):** Cá nhân / Phòng ban / Toàn công ty.
- **Mức độ Khẩn cấp (Urgency):** Chặn hoàn toàn công việc / Có phương án thay thế tạm thời / Không khẩn cấp.

**Actor:** Support Agent (đánh giá hai yếu tố); Administrator, Support Manager (cấu hình ma trận).

**Quy tắc nghiệp vụ:**
- `BR-06.1 (Ma trận cấu hình được)`: Doanh nghiệp tự định nghĩa mức ưu tiên tương ứng với từng tổ hợp Tác động × Khẩn cấp.
- `BR-06.2 (Cho phép ghi đè có lý do)`: Trưởng nhóm ghi đè được mức ưu tiên hệ thống đề xuất, nhưng phải nhập lý do và việc ghi đè được ghi vào nhật ký thay đổi — vì thay đổi mức ưu tiên làm thay đổi cam kết dịch vụ với khách hàng.
- `BR-06.3 (Áp dụng lại cam kết khi đổi ưu tiên)`: Khi mức ưu tiên thay đổi, hạn chót cam kết được tính lại theo chính sách tương ứng, tính từ thời điểm tạo vé ban đầu chứ không phải từ lúc đổi ưu tiên.


**Trạng thái triển khai:** Đã triển khai `BR-06.2` và `BR-06.3` theo GitHub Issue #223 (`crmsaassaudi/product-management#223`).
- `BR-06.2`: ghi đè bắt buộc nhập lý do, lưu người và thời điểm ghi đè.
- `BR-06.3`: đổi mức ưu tiên tính lại hạn chót từ thời điểm tạo vé (`rebaseFromStart`), theo lịch làm việc hiện hành; vé đã quá hạn vẫn giữ nguyên trạng thái vi phạm.

**Khoảng cách cần bổ sung:** `BR-06.1` **chưa cấu hình được theo từng doanh nghiệp**. Ma trận 3×3 hiện là hằng số trong mã (`PRIORITY_MATRIX`), không nhận tham số `tenantId` và không có màn hình cấu hình — mọi doanh nghiệp dùng chung một bộ giá trị, trái với câu chữ của quy tắc và tiêu chí hoàn thành của Issue #223. Cần đưa ma trận về danh mục cấu hình theo từng doanh nghiệp trước khi coi FEAT-06 là hoàn tất.

**Tiêu chí chấp nhận:** Xem Kịch bản 28 (mục 6).

---

## C. CAM KẾT CHẤT LƯỢNG DỊCH VỤ (SLA & BUSINESS HOURS)

### FEAT-07 — Động cơ Đo lường Đồng hồ SLA (Dual SLA Engine) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Kiểm soát độc lập 2 mốc thời hạn cam kết với khách hàng:
1. **Cam kết Phản hồi Đầu tiên:** Đếm từ khi tạo vé đến khi tư vấn viên gửi phản hồi công khai đầu tiên tới khách hàng.
2. **Cam kết Xử lý Dứt điểm:** Đếm từ khi tạo vé đến khi vé chuyển sang trạng thái kết thúc dạng "đã xử lý xong".

Hai mốc này chạy song song từ cùng thời điểm, không nối tiếp nhau — việc đã phản hồi khách hàng không làm dời hạn chót xử lý dứt điểm.

**Actor:** Support Manager, Administrator (cấu hình chính sách); Tiến trình Hệ thống (tính toán và theo dõi).

**Quy tắc nghiệp vụ:**
- `BR-07.1 (Chính sách theo mức ưu tiên)`: Mỗi mức ưu tiên có thời hạn cam kết riêng cho từng mốc. Trường hợp doanh nghiệp cần cam kết khác nhau theo hạng khách hàng, áp dụng thêm FEAT-40 theo thứ tự ưu tiên tại BR-40.1.
- `BR-07.2 (Mốc bắt đầu đếm)`: Cả hai đồng hồ bắt đầu đếm từ thời điểm vé được tạo trong hệ thống. Với vé được tạo từ một cuộc trò chuyện đa kênh đã có trao đổi trước đó, mốc bắt đầu vẫn là thời điểm tạo vé — các trao đổi trước khi vé tồn tại không thuộc phạm vi cam kết của module này.
- `BR-07.3 (Điều kiện hoàn tất mốc phản hồi đầu tiên)`: Mốc này chỉ được tính là hoàn tất khi có một **phản hồi công khai** thực sự gửi tới khách hàng. Ghi chú nội bộ, thông báo hệ thống tự động sinh, và thao tác đổi trạng thái đều không tính (xem BR-14.3).
- `BR-07.4 (Điều kiện hoàn tất mốc xử lý dứt điểm)`: Mốc này được tính là hoàn tất tại thời điểm vé lần đầu chuyển sang trạng thái kết thúc dạng "đã xử lý xong". Nếu vé sau đó bị mở lại, kết quả tuân thủ đã ghi nhận không bị xóa; lần xử lý xong tiếp theo được ghi nhận riêng để theo dõi chất lượng (KPI-04).
- `BR-07.5 (Tính theo lịch làm việc)`: Mọi phép tính thời hạn và thời gian đã trôi qua đều dựa trên lịch làm việc đang áp dụng (FEAT-08), không dùng thời gian thực tế liên tục.
- `BR-07.6 (Hiển thị thời gian còn lại)`: Màn hình xử lý vé và danh sách vé hiển thị thời gian còn lại tới từng hạn chót, để tư vấn viên tự sắp xếp thứ tự công việc mà không phải nhẩm tính.

**Tiêu chí chấp nhận:** Xem Kịch bản 1 và Kịch bản 24 (mục 6).

---

### FEAT-08 — Lịch Làm việc Doanh nghiệp Cấu hình được (Business Hours) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Mỗi doanh nghiệp tự cấu hình lịch làm việc riêng của mình (ngày làm việc trong tuần, khung giờ theo từng ngày, múi giờ, ngày nghỉ lễ) để tính toán thời hạn cam kết cho chính xác. Vé phát sinh ngoài giờ làm việc hoặc ngày nghỉ không bị tính thời gian chờ vào đồng hồ cam kết — đây là nguyên tắc công bằng cơ bản, vì doanh nghiệp không cam kết phục vụ ngoài giờ trừ khi có thỏa thuận riêng.

**Actor:** Support Manager, Administrator (cấu hình lịch); Tiến trình Hệ thống (áp dụng khi tính hạn chót).

**Quy tắc nghiệp vụ:**
- `BR-08.1 (Chỉ đếm trong giờ làm việc)`: Thời gian ngoài khung giờ làm việc, ngày nghỉ cuối tuần và ngày nghỉ lễ đã khai báo không được tính vào đồng hồ cam kết.
- `BR-08.2 (Nhiều lịch làm việc song song)`: Doanh nghiệp tạo được nhiều lịch làm việc khác nhau và gán cho từng chính sách cam kết — ví dụ lịch phục vụ liên tục cả ngày đêm cho khách hàng hạng cao cấp, lịch giờ hành chính cho khách hàng phổ thông.
- `BR-08.3 (Múi giờ áp dụng)`: Mỗi lịch làm việc gắn với một múi giờ cụ thể; hạn chót cam kết được tính theo múi giờ của lịch đang áp dụng, không phụ thuộc múi giờ của người đang xem.
- `BR-08.4 (Cập nhật ngày nghỉ lễ hàng năm)`: Danh sách ngày nghỉ lễ được khai báo theo năm; doanh nghiệp phải cập nhật được trước mỗi năm mới. Nếu một năm chưa khai báo ngày nghỉ, hệ thống cảnh báo Quản trị viên thay vì âm thầm tính cả ngày lễ thành ngày làm việc.
- `BR-08.5 (Thay đổi lịch không ảnh hưởng vé đang mở)`: Việc sửa **khung giờ hoặc ngày làm việc trong tuần** chỉ áp dụng cho vé tạo mới sau đó; các vé đang mở giữ nguyên hạn chót đã tính, theo cùng nguyên tắc tại BR-40.2. Lý do: đây là thay đổi chính sách của doanh nghiệp, không nên làm dời cam kết đã hứa với khách hàng.
- `BR-08.6 (Bổ sung ngày nghỉ lễ áp dụng cho cả vé đang mở)`: Riêng việc **khai báo thêm ngày nghỉ lễ** được áp dụng cho cả vé đang mở: hạn chót của những vé có khoảng thời gian còn lại đi qua ngày lễ mới khai báo sẽ được tính lại để không tính ngày lễ đó thành ngày làm việc. Đây là ngoại lệ có chủ đích của BR-08.5, vì hai việc khác bản chất: khai báo ngày lễ không phải đổi chính sách mà là bổ sung một sự thật về lịch mà doanh nghiệp chưa kịp nhập. Không có ngoại lệ này, vé tạo cuối tháng 12 có hạn chót rơi vào Tết năm sau sẽ vĩnh viễn bị tính Tết thành ngày làm việc — đúng điều BR-08.4 muốn tránh. Hệ thống báo số vé bị ảnh hưởng và hạn chót mới trước khi người cấu hình xác nhận.
- `BR-08.7 (Ngày lễ áp dụng cho cả lịch phục vụ liên tục)`: Với lịch phục vụ liên tục cả ngày đêm, doanh nghiệp chọn được ngày nghỉ lễ có áp dụng hay không (mặc định **không** áp dụng — phục vụ liên tục nghĩa là phục vụ cả ngày lễ). Cần nêu rõ vì đây là điều khoản khách hàng cao cấp sẽ hỏi khi ký cam kết.

**Tiêu chí chấp nhận:** Xem Kịch bản 24 (mục 6).

---

### FEAT-09 — Tự động Tạm dừng Đồng hồ SLA theo Trạng thái Vé `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi vé chuyển sang một trạng thái mà doanh nghiệp đã đánh dấu là "tạm dừng SLA" (ví dụ trạng thái tự định nghĩa "Chờ khách hàng phản hồi"), đồng hồ Resolution SLA lập tức đóng băng. Khi vé chuyển khỏi trạng thái đó, đồng hồ tự động chạy tiếp từ số phút còn lại, đảm bảo tính công bằng cho tư vấn viên khi việc chậm trễ không do họ.

**Actor:** Support Agent, Team Lead, Support Manager (người đổi trạng thái vé); Tiến trình Hệ thống (thực hiện tạm dừng/chạy tiếp).

**Quy tắc nghiệp vụ:**
- `BR-09.1 (Phạm vi tạm dừng)`: Việc tạm dừng chỉ áp dụng cho đồng hồ SLA Xử lý Dứt điểm.
- `BR-09.2 (Không tạm dừng SLA Phản hồi Đầu tiên)`: Đồng hồ SLA Phản hồi Đầu tiên **không bao giờ được tạm dừng** trước khi tư vấn viên gửi phản hồi công khai đầu tiên, kể cả khi vé được chuyển sang trạng thái tạm dừng SLA. Lý do: trách nhiệm phản hồi lần đầu luôn thuộc về doanh nghiệp — nếu cho phép tạm dừng, tư vấn viên có thể chuyển vé sang "Chờ khách hàng" ngay khi tiếp nhận để dừng đồng hồ mà chưa thực sự trả lời khách, làm chỉ số KPI-01 mất ý nghĩa.
- `BR-09.3 (Ghi nhận lịch sử tạm dừng)`: Mỗi lần tạm dừng và chạy tiếp đều được ghi vào lịch sử vé (thời điểm, người thực hiện, tổng thời gian đã tạm dừng), để Trưởng phòng đối chiếu khi có tranh chấp về tuân thủ SLA với khách hàng.
- `BR-09.4 (Cảnh báo lạm dụng tạm dừng)`: Nếu một vé bị tạm dừng quá số lần cho phép cấu hình được (mặc định 3 lần), hệ thống thông báo cho Team Lead để rà soát, tránh việc dùng trạng thái tạm dừng để né vi phạm SLA.

**Tiêu chí chấp nhận:** Xem Kịch bản 2 (mục 6).

---

### FEAT-40 — Chính sách SLA theo Hạng Khách hàng & Hợp đồng Dịch vụ `[Đã triển khai một phần]`

**Mô tả nghiệp vụ:** Trong kinh doanh B2B, cam kết dịch vụ khác nhau theo từng hợp đồng: khách hàng gói cao cấp được cam kết phản hồi trong 1 giờ, khách hàng gói phổ thông là 8 giờ — kể cả khi hai vé có cùng mức độ ưu tiên. Doanh nghiệp cần gán chính sách SLA theo hạng khách hàng hoặc theo từng hợp đồng cụ thể, không chỉ theo mức ưu tiên của vé.

**Actor:** Support Manager, Administrator (thiết lập chính sách); Tiến trình Hệ thống (áp dụng khi tạo vé).

**Quy tắc nghiệp vụ:**
- `BR-40.1 (Thứ tự ưu tiên áp dụng chính sách)`: Khi tạo vé, hệ thống chọn chính sách SLA theo thứ tự: (1) chính sách riêng của hợp đồng khách hàng đó nếu có, (2) chính sách theo hạng khách hàng, (3) chính sách chung theo mức ưu tiên của vé. Chính sách cụ thể hơn luôn thắng chính sách chung hơn.
- `BR-40.2 (Ghi nhận chính sách đã áp dụng)`: Vé lưu lại chính sách SLA đã áp dụng tại thời điểm tạo. Nếu sau đó doanh nghiệp sửa chính sách, các vé đang mở vẫn giữ cam kết ban đầu — tránh việc thay đổi chính sách làm vé đang chạy đột ngột chuyển thành vi phạm.
- `BR-40.3 (Hiển thị cam kết cho tư vấn viên)`: Màn hình xử lý vé hiển thị rõ hạng khách hàng và thời hạn cam kết đang áp dụng, để tư vấn viên biết mức độ ưu tiên thực tế khi sắp xếp công việc.

**Trạng thái triển khai:** Đã triển khai tầng 2 và tầng 3 của BR-40.1 theo GitHub Issue #222 (`crmsaassaudi/product-management#222`).
- `BR-40.1` tầng 2 (hạng khách hàng) và tầng 3 (mức ưu tiên): phân giải theo phân khúc `VIP:<mức ưu tiên>` → `VIP` → `<mức ưu tiên>`; tài khoản doanh nghiệp VIP thắng liên hệ cá nhân VIP.
- `BR-40.2`: sửa chính sách không đụng vé đang mở; đổi hạng khách hàng tính lại cam kết cho vé đang mở.
- `BR-40.3`: màn hình vé hiển thị hạng khách hàng và tầng cam kết đang áp dụng.

**Khoảng cách cần bổ sung:** `BR-40.1` **tầng 1 — chính sách riêng theo hợp đồng dịch vụ — chưa xây dựng**, vì hai phụ thuộc ngoài phân hệ chưa tồn tại: chưa có thực thể hợp đồng dịch vụ, và chưa có trường gán chính sách SLA cho từng khách hàng. Trường `ticket.slaPolicyId` **không dùng được cho mục đích này**: đó là đầu ra do engine SLA ghi lại chính sách đã phân giải, không phải đầu vào theo hợp đồng. Khi hai phụ thuộc trên sẵn sàng, tầng 1 cần một trường riêng và kiểm thử riêng theo yêu cầu của Issue #222.

**Tiêu chí chấp nhận:** Xem Kịch bản 13 (mục 6).

---

## D. PHÂN CÔNG & ĐIỀU PHỐI TỰ ĐỘNG (ROUTING & CAPACITY)

### FEAT-10 — Phân bổ Tự động Xoay vòng theo Hạn mức Năng lực `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động phân bổ vé cho nhân viên đang trực ca theo cơ chế xoay vòng, đảm bảo khối lượng công việc được chia đều và không ai bị dồn quá tải trong khi người khác trống việc.

**Actor:** Administrator, Support Manager (cấu hình quy tắc); Tiến trình Hệ thống (thực hiện phân bổ).

**Quy tắc nghiệp vụ:**
- `BR-10.1 (Hạn mức năng lực)`: Nếu một nhân viên đang giữ số vé chưa kết thúc bằng hoặc vượt hạn mức cho phép (mặc định 10 vé, cấu hình được), hệ thống bỏ qua người đó và phân bổ cho người tiếp theo còn chỗ trống.
- `BR-10.2 (Cách tính số vé đang giữ)`: Số vé đang giữ được tính là các vé chưa ở trạng thái kết thúc và **không** ở trạng thái tạm dừng cam kết (ví dụ đang chờ khách hàng phản hồi). Lý do: vé đang chờ khách hàng không chiếm thời gian làm việc thực tế của tư vấn viên, nên không nên chặn họ nhận việc mới. Vé con của sự cố diện rộng cũng không được tính, theo BR-28.6. Việc tư vấn viên lạm dụng trạng thái tạm dừng để né hạn mức được giám sát qua BR-43.9.
- `BR-10.3 (Chỉ xét người đang sẵn sàng)`: Chỉ phân bổ cho tư vấn viên đang trong ca trực và ở trạng thái sẵn sàng, theo FEAT-35.
- `BR-10.4 (Khi mọi người đều đầy hạn mức)`: Nếu tất cả tư vấn viên trong nhóm đều đã đạt hạn mức, vé được đưa vào hàng đợi chung (FEAT-34) và Trưởng nhóm được cảnh báo ngay, thay vì gán ép cho người đã quá tải hoặc để vé rơi vào trạng thái không xác định.
- `BR-10.5 (Duy trì thứ tự xoay vòng)`: Thứ tự xoay vòng được duy trì liên tục, không bị thiết lập lại mỗi ngày hay mỗi lần hệ thống khởi động lại, để đảm bảo công bằng dài hạn giữa các tư vấn viên.

**Cập nhật theo Issue #226:** `BR-10.1` nay hỗ trợ **hạn mức riêng cho từng tư vấn viên** (`user.ticketMaxCapacity`), thay vì một con số chung cho cả doanh nghiệp.
- Phân giải ba cấp: hạn mức riêng của người → `assignment_settings.defaultMaxCapacity` → 10.
- Để trống nghĩa là **kế thừa động**: đổi mặc định doanh nghiệp áp dụng ngay cho người đang kế thừa, không phải giá trị sao chép cứng lúc tạo tài khoản. Tài khoản đang chạy không cần migration.
- Hạn mức áp **ở engine phân công** chứ không chỉ ở tầng đọc; `BR-11.3` giữ nguyên thứ tự — lọc theo kỹ năng trước, hạn mức sau.
- Bảng điều khiển hàng đợi (`BR-42.1`) hiển thị mẫu số riêng của từng người.

**Phụ thuộc chưa sẵn sàng:** BR-10.3 chờ FEAT-35 và BR-10.4 chờ FEAT-34. Trong thời gian chờ, việc phân bổ **không biết ai đang thực sự trong ca trực** — vé vẫn có thể được gán cho người đã hết ca hoặc đang nghỉ phép, và khi mọi người đều đầy hạn mức thì chưa có hàng đợi chung để vé nằm chờ an toàn. Đây là hạn chế lớn nhất với doanh nghiệp vận hành nhiều ca.

**Tiêu chí chấp nhận:** Xem Kịch bản 23 (mục 6).

---

### FEAT-11 — Phân bổ Thông minh theo Kỹ năng Chuyên môn (Skill-Based) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Doanh nghiệp có thể bật cơ chế điều phối theo kỹ năng chuyên môn để tự động điều hướng vé tới tư vấn viên có đúng năng lực xử lý — ví dụ vé về tích hợp kỹ thuật tới người có kỹ năng kỹ thuật, vé tiếng Anh tới người xử lý được tiếng Anh — thay vì chia đều cho mọi người rồi phải chuyển tay nhiều lần.

**Actor:** Administrator, Support Manager (khai báo kỹ năng và quy tắc); Team Lead (gán kỹ năng cho thành viên nhóm); Tiến trình Hệ thống (thực hiện điều phối).

**Quy tắc nghiệp vụ:**
- `BR-11.1 (Khai báo kỹ năng)`: Doanh nghiệp định nghĩa danh mục kỹ năng của riêng mình và gán cho từng tư vấn viên. Một người có nhiều kỹ năng, một kỹ năng thuộc về nhiều người.
- `BR-11.2 (Xác định kỹ năng vé cần)`: Kỹ năng yêu cầu của một vé được suy ra từ danh mục phân loại và/hoặc kênh tiếp nhận, theo quy tắc ánh xạ do doanh nghiệp cấu hình. Trưởng nhóm điều chỉnh lại được cho từng vé cụ thể khi cần.
- `BR-11.3 (Quan hệ với phân bổ xoay vòng)`: Điều phối theo kỹ năng **lọc trước**, xoay vòng theo hạn mức (FEAT-10) **chọn sau**. Nghĩa là hệ thống xác định tập tư vấn viên có đủ kỹ năng và đang sẵn sàng trước, rồi trong tập đó mới áp dụng thứ tự xoay vòng và hạn mức năng lực. Hai cơ chế bổ sung cho nhau, không loại trừ nhau.
- `BR-11.4 (Xử lý khi không có ai đủ kỹ năng)`: Doanh nghiệp chọn một trong ba cách ứng xử: (a) **Chờ** — giữ vé trong hàng đợi chung tới khi có người đủ kỹ năng sẵn sàng, đồng thời cảnh báo Trưởng nhóm ngay; (b) **Hạ tiêu chuẩn** — phân bổ cho tư vấn viên có nhiều kỹ năng khớp nhất trong số những người đang sẵn sàng, và nếu số kỹ năng khớp bằng nhau thì theo thứ tự xoay vòng. Trường hợp **không ai khớp kỹ năng nào**, vé vẫn được gán theo thứ tự xoay vòng thông thường và hệ thống ghi chú trên vé rằng người phụ trách chưa có kỹ năng yêu cầu, đồng thời cảnh báo Trưởng nhóm — vì để vé không người phụ trách trong khi đồng hồ cam kết vẫn chạy là kết cục xấu hơn; (c) **Chờ rồi hạ tiêu chuẩn** — chờ trong khoảng thời gian cấu hình được (mặc định 15 phút), hết thời gian đó thì áp dụng cách (b).
- `BR-11.5 (Đồng hồ cam kết vẫn chạy khi chờ)`: Trong thời gian chờ tìm người đủ kỹ năng, đồng hồ cam kết vẫn tiếp tục đếm, theo đúng nguyên tắc tại BR-34.4.

**Phụ thuộc chưa sẵn sàng:** Cách ứng xử (a) và (c) của BR-11.4, cùng BR-11.5, chờ FEAT-34 (hàng đợi chung). Trong thời gian chờ, doanh nghiệp chỉ dùng được cách (b) — hạ tiêu chuẩn ngay; vé cần kỹ năng đặc thù **không có chỗ nằm chờ đúng người**, nên hoặc phải giao cho người chưa đủ năng lực, hoặc rơi vào tình trạng không xác định.

**Tiêu chí chấp nhận:** Xem Kịch bản 25 (mục 6).

---

### FEAT-12 — Bàn giao & Phân công Lại Vé `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép chuyển giao vé đang xử lý sang cho tư vấn viên khác khi cần — vì xử lý sai người, vì cần chuyên môn cao hơn, hoặc vì người đang giữ vé không thể tiếp tục.

**Actor:** Support Agent (chuyển vé mình đang giữ), Team Lead, Support Manager (chuyển vé của bất kỳ ai trong phạm vi quản lý).

**Quy tắc nghiệp vụ:**
- `BR-12.1 (Ghi nhận lý do bàn giao)`: Mỗi lần chuyển vé phải kèm ghi chú bàn giao nêu lý do và tóm tắt những việc đã làm, lưu vào dòng trao đổi dưới dạng ghi chú nội bộ. Ghi chú này giúp người nhận không phải đọc lại toàn bộ lịch sử và giúp Trưởng nhóm đối soát khi vé bị chuyển lòng vòng.
- `BR-12.2 (SLA không được thiết lập lại)`: Việc chuyển người phụ trách **không** làm thiết lập lại đồng hồ SLA. Mốc phản hồi đầu tiên nếu đã hoàn tất thì giữ nguyên; hạn chót xử lý dứt điểm giữ nguyên theo vé, không tính lại từ thời điểm bàn giao. Lý do: cam kết dịch vụ là với khách hàng, không phụ thuộc việc nội bộ đổi người.
- `BR-12.3 (Thông báo người nhận)`: Người được nhận vé phải nhận được thông báo ngay (xem FEAT-37), kèm tên người bàn giao và lý do.
- `BR-12.4 (Từ chối nhận vé)`: Người nhận có thể từ chối vé kèm lý do trong một khoảng thời gian cấu hình được (mặc định 30 phút). Khi bị từ chối, vé quay về hàng đợi chung (xem FEAT-34) và Trưởng nhóm được thông báo để điều phối lại.
- `BR-12.5 (Giới hạn chuyển lòng vòng)`: Nếu một vé bị chuyển quá số lần cấu hình được (mặc định 3 lần) mà chưa được giải quyết, hệ thống cảnh báo Trưởng nhóm để can thiệp trực tiếp, tránh tình trạng vé bị đùn đẩy giữa các tư vấn viên trong khi đồng hồ SLA vẫn chạy.

**Luồng ngoại lệ:**
- Người nhận không còn hoạt động trong hệ thống (nghỉ việc, bị khóa tài khoản): hệ thống từ chối thao tác chuyển và yêu cầu chọn người khác.
- Người nhận đã đạt hạn mức năng lực tối đa: hệ thống cảnh báo nhưng vẫn cho phép Trưởng nhóm chuyển nếu xác nhận, vì bàn giao thủ công là quyết định có chủ đích của quản lý.

**Tiêu chí chấp nhận:** Xem Kịch bản 9 (mục 6).

---

### FEAT-34 — Hàng đợi Chung & Giám sát Vé Chưa có Người Xử lý `[Đã triển khai]`

**Mô tả nghiệp vụ:** Không phải vé nào cũng được gán ngay cho một tư vấn viên cụ thể. Những vé mới tiếp nhận chưa qua điều phối, vé bị từ chối, hoặc vé của người đã vắng mặt sẽ nằm ở **hàng đợi chung** — nơi cả nhóm cùng nhìn thấy và có thể nhận việc. Trưởng nhóm cần biết ngay khi có vé nằm quá lâu trong hàng đợi mà chưa ai nhận, vì đây là nguyên nhân phổ biến nhất khiến vi phạm cam kết phản hồi lần đầu.

**Actor:** Support Agent (xem và nhận vé từ hàng đợi), Team Lead, Support Manager (giám sát và điều phối).

**Quy tắc nghiệp vụ:**
- `BR-34.1 (Hàng đợi theo nhóm)`: Mỗi nhóm hỗ trợ có một hàng đợi riêng. Vé chưa có người phụ trách nằm trong hàng đợi của nhóm được phân công xử lý.
- `BR-34.2 (Tự nhận việc)`: Tư vấn viên có thể tự nhận một vé từ hàng đợi của nhóm mình. Khi một người đã nhận, vé lập tức biến mất khỏi hàng đợi của những người khác để tránh hai người cùng xử lý một vé.
- `BR-34.3 (Cảnh báo vé tồn đọng trong hàng đợi)`: Nếu một vé nằm trong hàng đợi chung quá khoảng thời gian cấu hình được (mặc định 15 phút) mà chưa ai nhận, hệ thống cảnh báo Trưởng nhóm. Ngưỡng này độc lập với hạn chót SLA — mục đích là phát hiện sớm **trước khi** vé kịp vi phạm, chứ không phải báo sau khi đã vi phạm.
- `BR-34.4 (Đồng hồ SLA vẫn chạy)`: Thời gian vé nằm trong hàng đợi chung vẫn được tính vào đồng hồ SLA. Việc chưa ai nhận vé là vấn đề nội bộ của doanh nghiệp, không phải lý do để tạm dừng cam kết với khách hàng.
- `BR-34.5 (Hai người cùng nhận một vé)`: Khi hai người bấm nhận cùng một vé gần như đồng thời, **chỉ người thao tác trước được nhận**; người sau nhận được thông báo vé đã có người nhận kèm tên người đó, và vé biến mất khỏi hàng đợi của họ. Hệ thống không bao giờ gán một vé cho hai người, kể cả khi hai thao tác đến cùng thời điểm. Đây là tình huống xảy ra thường xuyên ở hàng đợi đông người vào giờ cao điểm, nên phải xử lý dứt khoát thay vì để người sau tưởng mình đang giữ vé.

**Tiêu chí chấp nhận:** Xem Kịch bản 10 và Kịch bản 42 (mục 6).

---

### FEAT-35 — Trạng thái Sẵn sàng & Ca trực của Tư vấn viên `[Đã triển khai]`

**Mô tả nghiệp vụ:** Với đội hỗ trợ nhiều người làm theo ca, hệ thống cần biết ai đang thực sự trực để phân bổ vé đúng người. Tư vấn viên tự đặt trạng thái sẵn sàng của mình trong ca (đang trực, tạm rời chỗ, bận họp), và doanh nghiệp khai báo lịch trực cùng các ngày nghỉ phép đã duyệt.

**Actor:** Support Agent (tự đặt trạng thái sẵn sàng), Team Lead (xếp lịch trực, duyệt nghỉ phép), Support Manager.

**Quy tắc nghiệp vụ:**
- `BR-35.1 (Chỉ phân bổ cho người đang sẵn sàng)`: Cơ chế phân bổ tự động (FEAT-10, FEAT-11) chỉ xét những tư vấn viên đang trong ca trực và ở trạng thái sẵn sàng nhận việc. Người ngoài ca, đang nghỉ phép, hoặc đang ở trạng thái tạm rời chỗ sẽ bị bỏ qua.
- `BR-35.2 (Không còn ai sẵn sàng)`: Nếu tại thời điểm phân bổ không còn tư vấn viên nào sẵn sàng trong nhóm, vé được đưa vào hàng đợi chung (FEAT-34) và Trưởng nhóm được thông báo ngay, thay vì gán bừa cho người đang nghỉ.
- `BR-35.3 (Vé đang giữ khi hết ca)`: Khi một tư vấn viên kết thúc ca trực mà còn vé chưa xử lý xong, hệ thống hiển thị danh sách vé đó để họ bàn giao cho ca sau hoặc trả về hàng đợi chung. Không tự động chuyển ngầm, vì người tiếp nhận cần ghi chú bàn giao (BR-12.1).
- `BR-35.4 (Nghỉ phép đã duyệt)`: Khi một kỳ nghỉ phép được duyệt, hệ thống cảnh báo trước cho Trưởng nhóm về số vé đang mở của người sắp nghỉ, để lên kế hoạch chuyển giao (xem FEAT-36). Việc duyệt nghỉ phép thuộc quy trình nhân sự của doanh nghiệp; ở phạm vi tài liệu này chỉ yêu cầu ghi nhận được kỳ nghỉ đã duyệt để cơ chế phân bổ biết mà tránh.
- `BR-35.5 (Lịch trực phải phủ được lịch cam kết)`: Hệ thống đối chiếu lịch trực với lịch làm việc của các chính sách cam kết đang áp dụng và **cảnh báo Quản trị viên khi có khung giờ nằm trong cam kết nhưng không ai trực**. Lý do: doanh nghiệp bán cam kết phục vụ liên tục cả ngày đêm (BR-08.2) nhưng xếp ca trực chỉ tới 18:00 thì vé phát sinh lúc 2 giờ sáng vẫn chạy đồng hồ mà không người nào nhận — cam kết đã ký trở thành không thực hiện được, và lỗi chỉ lộ ra khi đã vi phạm với khách hàng.
- `BR-35.6 (Người trực ngoài giờ)`: Với khung giờ nằm ngoài ca trực thông thường nhưng vẫn thuộc phạm vi cam kết, doanh nghiệp chỉ định được người trực ngoài giờ cho từng khung. Vé phát sinh trong khung đó được gửi cảnh báo tới người này qua kênh có khả năng đánh thức (xem BR-37.4), không chỉ thông báo trong ứng dụng — vì thông báo trong ứng dụng lúc 2 giờ sáng không có ai đọc.

**Tiêu chí chấp nhận:** Xem Kịch bản 11 và Kịch bản 45 (mục 6).

---

### FEAT-36 — Chuyển Vé Hàng loạt khi Nhân sự Vắng mặt hoặc Nghỉ việc `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi một tư vấn viên nghỉ ốm đột xuất, nghỉ phép dài ngày hoặc nghỉ việc, toàn bộ vé đang mở của họ phải được chuyển cho người khác nhanh chóng. Nếu phải mở từng vé để chuyển thủ công, đồng hồ SLA của tất cả các vé đó vẫn chạy trong lúc Trưởng nhóm thao tác — đây là tình huống xảy ra thường xuyên và trực tiếp gây vi phạm cam kết.

**Actor:** Team Lead, Support Manager.

**Quy tắc nghiệp vụ:**
- `BR-36.1 (Chuyển toàn bộ vé của một người)`: Trưởng nhóm chọn một tư vấn viên, xem danh sách toàn bộ vé đang mở của họ, và chuyển tất cả (hoặc chọn lọc một phần) sang một hoặc nhiều người khác trong một thao tác duy nhất.
- `BR-36.2 (Phân bổ lại theo năng lực)`: Khi chuyển cho nhiều người, hệ thống đề xuất phân bổ tự động theo hạn mức năng lực còn trống của từng người (FEAT-10), Trưởng nhóm có thể điều chỉnh trước khi xác nhận.
- `BR-36.3 (Ghi chú bàn giao chung)`: Trưởng nhóm nhập một ghi chú bàn giao chung áp dụng cho toàn bộ vé được chuyển (ví dụ "Chuyển do anh A nghỉ ốm đột xuất"), ghi vào từng vé dưới dạng ghi chú nội bộ.
- `BR-36.4 (Xử lý khi nhân sự nghỉ việc)`: Khi một tài khoản tư vấn viên bị vô hiệu hóa, hệ thống **bắt buộc** người thực hiện phải chuyển giao hết vé đang mở của người đó trước khi hoàn tất vô hiệu hóa, tránh để lại vé không ai phụ trách.
- `BR-36.5 (Giữ nguyên SLA)`: Việc chuyển hàng loạt không thiết lập lại đồng hồ SLA của các vé liên quan, theo đúng nguyên tắc tại BR-12.2.

**Tiêu chí chấp nhận:** Xem Kịch bản 12 (mục 6).

---

## E. TÁC NGHIỆP XỬ LÝ & PHẢN HỒI KHÁCH HÀNG (AGENT WORKSPACE)

### FEAT-13 — Dòng Trao đổi Vé Hỗ trợ `[Đã triển khai]`

**Mô tả nghiệp vụ:** Màn hình hội thoại hợp nhất toàn bộ tin nhắn qua lại giữa khách hàng và tư vấn viên theo thứ tự thời gian, phân biệt rõ ba loại nội dung: phản hồi công khai gửi khách hàng, ghi chú nội bộ chỉ nhân viên nhìn thấy, và thông báo hệ thống tự động sinh ra (ví dụ khi đổi trạng thái, đổi người phụ trách).

**Actor:** Khách hàng (gửi và xem phần công khai), Support Agent, Team Lead, Support Manager (xem toàn bộ và gửi cả hai loại nội dung).

**Quy tắc nghiệp vụ:**
- `BR-13.1 (Bất biến sau khi gửi)`: Nội dung trao đổi và ghi chú trên vé không chỉnh sửa hoặc xóa được sau khi gửi, đảm bảo tính minh bạch của lịch sử xử lý. Muốn đính chính, phải gửi một nội dung mới.
- `BR-13.2 (Phân biệt ba loại nội dung)`: Giao diện phân biệt rõ ba loại nội dung bằng dấu hiệu trực quan, tránh tư vấn viên nhầm lẫn gửi nội dung nội bộ ra ngoài cho khách hàng (xem BR-14.1).
- `BR-13.3 (Thứ tự thời gian thống nhất)`: Toàn bộ nội dung hiển thị theo thứ tự thời gian phát sinh, không tách riêng theo loại, để người đọc nắm được diễn biến xử lý đúng như đã thực sự diễn ra.


**Tiêu chí chấp nhận:** Xem Kịch bản 38 (mục 6).

---

### FEAT-14 — Ghi chú Nội bộ Bảo mật giữa Nhân viên `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép viết ghi chú nội bộ để trao đổi giải pháp giữa các nhân viên; khách hàng không bao giờ nhìn thấy nội dung này ở bất kỳ giao diện nào tiếp xúc với khách hàng. Đây là nơi ghi nhận các thông tin không phù hợp để gửi khách (phân tích nguyên nhân chưa chắc chắn, trao đổi với bộ phận kỹ thuật, ghi chú bàn giao).

**Actor:** Support Agent, Team Lead, Support Manager.

**Quy tắc nghiệp vụ:**
- `BR-14.1 (Phân biệt rõ trên giao diện)`: Ghi chú nội bộ phải được hiển thị khác biệt rõ ràng so với phản hồi công khai, để tư vấn viên không nhầm lẫn gửi nhầm nội dung nội bộ cho khách hàng. Đây là lỗi có hậu quả nghiêm trọng và không thể thu hồi.
- `BR-14.2 (Nhắc tên đồng nghiệp)`: Trong ghi chú nội bộ, tư vấn viên nhắc tên được đồng nghiệp để xin hỗ trợ; người được nhắc tên nhận thông báo (xem FEAT-37) kèm đường dẫn tới vé.
- `BR-14.3 (Không tính vào cam kết phản hồi)`: Ghi chú nội bộ không được tính là phản hồi đầu tiên tới khách hàng — đồng hồ cam kết phản hồi chỉ dừng khi có phản hồi công khai thực sự gửi đi.

**Tiêu chí chấp nhận:** Xem Kịch bản 32 (mục 6).

---

### FEAT-15 — Đính kèm Tài liệu & Hình ảnh `[Đã triển khai]`

**Mô tả nghiệp vụ:** Đính kèm hình ảnh chụp màn hình lỗi, tệp nhật ký hoặc tài liệu minh chứng trực tiếp vào khung phản hồi hoặc ghi chú, giúp tư vấn viên và khách hàng trao đổi chính xác về sự cố.

**Actor:** Khách hàng (gửi kèm tệp qua kênh trao đổi), Support Agent, Team Lead, Support Manager.

**Quy tắc nghiệp vụ:**
- `BR-15.1 (Giới hạn số lượng và dung lượng)`: Tối đa 20 tệp cho mỗi lượt gửi; dung lượng tối đa mỗi tệp do doanh nghiệp cấu hình (mặc định 25MB).
- `BR-15.2 (Kiểm soát định dạng)`: Doanh nghiệp cấu hình danh sách định dạng tệp được phép nhận. Các định dạng có nguy cơ chứa mã độc (tệp thực thi) bị từ chối mặc định.
- `BR-15.3 (Quyền xem tệp theo quyền xem vé)`: Chỉ người có quyền xem vé mới tải được tệp đính kèm của vé đó. Tệp đính kèm trong ghi chú nội bộ không bao giờ hiển thị cho khách hàng, theo đúng NFR-09.
- `BR-15.4 (Lưu trữ theo vòng đời vé)`: Tệp đính kèm được lưu trữ và xóa theo cùng vòng đời của vé chứa nó, bao gồm cả khi vé bị ẩn danh hóa theo yêu cầu bảo vệ dữ liệu cá nhân (xem FEAT-45).

**Phụ thuộc chưa sẵn sàng:** BR-15.4 chờ FEAT-45. Trong thời gian chờ, khi khách hàng yêu cầu xóa dữ liệu cá nhân, **tệp đính kèm chưa được xử lý tự động** — ảnh chụp màn hình và tài liệu khách gửi có thể chứa thông tin định danh vẫn nằm lại trong hệ thống. Đây là rủi ro tuân thủ Nghị định 13, không chỉ là thiếu tiện ích.

**Tiêu chí chấp nhận:** Xem Kịch bản 32 (mục 6).

---

### FEAT-16 — Thư viện Câu trả lời Mẫu Soạn sẵn (Canned Responses / Macros) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Chèn nhanh các câu trả lời chuẩn từ thư viện mẫu có sẵn để tăng tốc độ phản hồi các câu hỏi thường gặp và đảm bảo tính nhất quán trong cách trả lời khách hàng giữa các tư vấn viên.

**Actor:** Support Agent (sử dụng mẫu), Team Lead, Support Manager (tạo và duyệt mẫu dùng chung).

**Quy tắc nghiệp vụ:**
- `BR-16.1 (Mẫu dùng chung và mẫu cá nhân)`: Doanh nghiệp có thư viện mẫu dùng chung do quản lý duyệt; mỗi tư vấn viên cũng có thể tạo mẫu riêng cho mình. Mẫu dùng chung đảm bảo tính nhất quán; mẫu cá nhân giúp người dùng tối ưu cách làm việc riêng.
- `BR-16.2 (Điền tự động thông tin vé)`: Mẫu hỗ trợ các trường thông tin tự động điền từ vé và hồ sơ khách hàng (tên khách hàng, mã số vé, tên tư vấn viên), tránh lỗi xưng hô sai khi sao chép thủ công.
- `BR-16.3 (Nội dung vẫn chỉnh sửa được trước khi gửi)`: Sau khi chèn mẫu, tư vấn viên vẫn phải chỉnh sửa được nội dung trước khi gửi — mẫu là điểm khởi đầu, không phải câu trả lời bắt buộc gửi nguyên văn.

**Trạng thái triển khai:** Đã triển khai đầy đủ theo GitHub Issue #223 (`crmsaassaudi/product-management#223`).
- `BR-16.1`: mẫu dùng chung và mẫu cá nhân, phân biệt phạm vi, hỗ trợ phím tắt.
- `BR-16.2`: tự động điền biến từ ngữ cảnh vé; biến không phân giải được được giữ nguyên dấu ngoặc và cảnh báo cho tư vấn viên trước khi gửi, thay vì bị thay bằng chuỗi rỗng.
- `BR-16.3`: chỉ OWNER/ADMIN/SUPPORT_MANAGER/TEAM_LEAD được quản lý mẫu dùng chung; vai trò nghiệp vụ phân giải qua `withSystemRoleKeys`, không đọc từ CLS.

**Tiêu chí chấp nhận:** Xem Kịch bản 14 (mục 6).

---

### FEAT-37 — Thông báo cho Tư vấn viên về Vé được Gán & Khách hàng Phản hồi `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tư vấn viên cần được báo ngay khi có vé mới được gán cho mình, khi khách hàng phản hồi trên vé họ đang giữ, hoặc khi đồng nghiệp nhắc tên họ trong ghi chú nội bộ. Nếu phải tự mở danh sách vé để kiểm tra thủ công, vé sẽ bị bỏ sót và cam kết phản hồi bị vi phạm.

**Actor:** Support Agent, Team Lead, Support Manager (người nhận thông báo); Tiến trình Hệ thống (phát thông báo).

**Quy tắc nghiệp vụ:**
- `BR-37.1 (Các sự kiện phát sinh thông báo)`: Tối thiểu các sự kiện sau phải sinh thông báo cho người liên quan: được gán một vé mới, khách hàng gửi phản hồi mới trên vé đang giữ, được nhắc tên trong ghi chú nội bộ, vé của mình sắp tới hạn hoặc đã vi phạm cam kết, vé của mình bị leo thang.
- `BR-37.2 (Kênh thông báo)`: Thông báo hiển thị trong ứng dụng; doanh nghiệp có thể bật thêm gửi qua email cho các sự kiện quan trọng. Mỗi người dùng tự cấu hình được sự kiện nào muốn nhận qua kênh nào, tránh nhiễu thông báo dẫn tới bỏ qua cả những cảnh báo thật sự cần thiết.
- `BR-37.3 (Không tự thông báo cho chính mình)`: Hành động do chính người đó thực hiện không sinh thông báo ngược lại cho họ.
- `BR-37.4 (Kênh đánh thức cho cảnh báo ngoài giờ)`: Với vé phát sinh hoặc vi phạm trong khung giờ ngoài ca trực thông thường mà vẫn thuộc phạm vi cam kết (BR-35.6), thông báo phải gửi qua kênh có khả năng đánh thức người nhận — tin nhắn điện thoại hoặc cuộc gọi tự động. Doanh nghiệp cấu hình kênh cụ thể và danh sách sự kiện được phép dùng kênh này. Không giới hạn danh sách sẽ dẫn tới việc nhân viên bị gọi vì những việc không khẩn cấp và sẽ tắt thông báo, làm hỏng chính cơ chế này.
- `BR-37.5 (Thông báo không gửi được)`: Khi một thông báo bắt buộc không gửi được (kênh mất kết nối, địa chỉ không hợp lệ), hệ thống thử lại theo số lần cấu hình được; nếu vẫn thất bại thì ghi nhận trên vé và báo Trưởng nhóm. Áp dụng cho cả thông báo gửi tới khách hàng ở BR-27.3 và BR-22.3 — đặc biệt quan trọng vì BR-27.3 là điều kiện bắt buộc để việc gộp vé được coi là hoàn chỉnh: gộp xong mà khách hàng không nhận được thông báo thì họ mất dấu yêu cầu của mình.

**Tiêu chí chấp nhận:** Xem Kịch bản 15 (mục 6).

---

### FEAT-38 — Cảnh báo Trùng Thao tác khi Nhiều Người cùng Xử lý Một Vé `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi hai tư vấn viên cùng mở một vé và cùng soạn phản hồi, khách hàng sẽ nhận hai câu trả lời khác nhau cho cùng một câu hỏi — tình huống gây mất uy tín và hay xảy ra ở các đội đông người dùng chung hàng đợi.

**Actor:** Support Agent, Team Lead, Support Manager.

**Quy tắc nghiệp vụ:**
- `BR-38.1 (Hiển thị người đang xem)`: Khi một người mở vé, những người khác cùng mở vé đó thấy cảnh báo nêu rõ ai đang xem cùng.
- `BR-38.2 (Cảnh báo khi đang soạn phản hồi)`: Khi một người bắt đầu soạn nội dung phản hồi công khai, những người khác đang mở cùng vé nhìn thấy cảnh báo nêu rõ tên người đang soạn. Cảnh báo này tự mất khi người đó gửi nội dung, đóng vé, hoặc không có thao tác nhập liệu nào trong 2 phút — mốc 2 phút áp dụng cho cả trường hợp mất kết nối, vì với người còn lại thì người mất kết nối và người bỏ đi là như nhau.
- `BR-38.3 (Cảnh báo trước khi gửi chồng)`: Nếu vé đã thay đổi trong lúc một người đang soạn — có phản hồi mới được gửi, hoặc có thay đổi ở trạng thái, mức ưu tiên, người phụ trách — hệ thống cảnh báo và yêu cầu người đó xem lại thay đổi mới trước khi gửi. Không chỉ xét phản hồi mới, vì vé có thể đã được người khác đóng hoặc chuyển đi trong lúc này và nội dung đang soạn không còn phù hợp.
- `BR-38.4 (Hai người cùng sửa một trường)`: Khi hai người cùng thay đổi một trường của vé cách nhau **không quá 60 giây** (cấu hình được), thay đổi đến sau được ghi nhận và ghi đè, nhưng hệ thống báo cho người thao tác sau biết giá trị vừa bị họ thay thế là do ai đặt và lúc nào, để họ tự quyết định có giữ thay đổi của mình hay không. Ngoài cửa sổ đó, thay đổi được ghi nhận bình thường không kèm cảnh báo — vì hai thao tác cách nhau đủ xa thì người sau đã nhìn thấy giá trị hiện hành trên màn hình, không phải tình huống ghi đè ngoài ý muốn. Cả hai lần thay đổi đều nằm trong nhật ký theo BR-44.1, nên không có thao tác nào biến mất khỏi hồ sơ truy vết.

**Trạng thái triển khai:** Đã triển khai đầy đủ theo GitHub Issue #224 (`crmsaassaudi/product-management#224`).
- `BR-38.1`: hiển thị người đang xem cùng vé theo thời gian thực (heartbeat 25 giây, Redis có dự phòng bộ nhớ).
- `BR-38.2`: cảnh báo người đang soạn phản hồi, tự gỡ khi gửi hoặc sau thời gian không thao tác theo `CFG-38-02`; mốc hết hạn lưu trên bản ghi nên áp dụng cả khi mất kết nối.
- `BR-38.3`: cảnh báo trước khi gửi chồng, đối chiếu `updatedAt` nên bắt được cả đổi trạng thái, mức ưu tiên và người phụ trách, không chỉ phản hồi mới.
- `BR-38.4`: ghi đè cùng một trường trong cửa sổ theo `CFG-38-01` thì người sau được báo ai đặt giá trị cũ, lúc nào và giá trị đó là gì. Cả hai lần thay đổi đều vào nhật ký theo BR-44.1 qua đường cập nhật chuẩn.

**Cấu hình liên quan (Phụ lục B):** `CFG-38-01` cửa sổ cảnh báo ghi đè (mặc định 60 giây, 10–600), `CFG-38-02` thời gian không thao tác trước khi gỡ cảnh báo đang soạn (mặc định 120 giây, 30–600). Cả hai được đọc theo từng doanh nghiệp lúc chạy.

**Tiêu chí chấp nhận:** Xem Kịch bản 16 và Kịch bản 42 (mục 6).

---

## F. LEO THANG & CẢNH BÁO VI PHẠM SLA (ESCALATION)

### FEAT-17 — Cảnh báo Sớm Nguy cơ Vi phạm SLA `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi một vé sắp tới hạn cam kết, hệ thống cảnh báo sớm tới tư vấn viên đang phụ trách để họ kịp ưu tiên xử lý trước khi vi phạm. Đây là công cụ phòng ngừa — giá trị nằm ở chỗ cảnh báo đủ sớm để còn kịp hành động.

**Actor:** Support Agent (nhận cảnh báo), Team Lead (nhận cảnh báo với vé trong nhóm), Support Manager, Administrator (cấu hình ngưỡng).

**Quy tắc nghiệp vụ:**
- `BR-17.1 (Ngưỡng cảnh báo theo tỷ lệ thời hạn)`: Ngưỡng cảnh báo được cấu hình theo **tỷ lệ phần trăm thời hạn cam kết đã trôi qua** (mặc định 75%), để cảnh báo luôn tương xứng với độ dài cam kết. Doanh nghiệp có thể đặt thêm một ngưỡng tuyệt đối tối thiểu tính bằng phút cho các cam kết rất ngắn.
- `BR-17.2 (Cảnh báo cho cả hai mốc cam kết)`: Cảnh báo áp dụng độc lập cho cả cam kết phản hồi đầu tiên và cam kết xử lý dứt điểm.
- `BR-17.3 (Không cảnh báo khi đồng hồ đang tạm dừng)`: Cảnh báo được xét **theo từng mốc cam kết**, không xét theo vé. Mốc nào có đồng hồ đang tạm dừng thì không phát cảnh báo cho mốc đó, vì trách nhiệm lúc đó không thuộc về tư vấn viên. Ngược lại, mốc nào vẫn đang chạy thì vẫn phát cảnh báo bình thường — kể cả khi vé đang ở trạng thái tạm dừng. Trường hợp điển hình: vé chưa có phản hồi công khai nào bị chuyển sang "Chờ khách hàng"; theo BR-09.2 đồng hồ Phản hồi Đầu tiên vẫn chạy, nên cảnh báo cho mốc này **vẫn phải phát** dù đồng hồ Xử lý Dứt điểm đã dừng. Nếu không, tư vấn viên sẽ vi phạm cam kết phản hồi mà không hề được báo trước, đúng tình huống BR-09.2 muốn ngăn chặn.
- `BR-17.4 (Hiển thị trực quan)`: Vé sắp tới hạn được đánh dấu nổi bật trong danh sách làm việc và trên bảng điều khiển hàng đợi (FEAT-42), không chỉ gửi thông báo một lần rồi thôi.

**Phụ thuộc chưa sẵn sàng:** Phần hiển thị trên bảng điều khiển hàng đợi tại BR-17.4 chờ FEAT-42. Trong thời gian chờ, Trưởng nhóm **không có màn hình nào nhìn được toàn cảnh các vé sắp tới hạn** để điều phối trước — việc phòng ngừa vi phạm phụ thuộc vào việc từng tư vấn viên có để ý cảnh báo của riêng mình hay không.

**Tiêu chí chấp nhận:** Xem Kịch bản 3 (mục 6).

---

### FEAT-18 — Tự động Leo thang khi Vi phạm SLA `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi một vé đã vi phạm cam kết mà vẫn chưa được xử lý, hệ thống chủ động đưa vấn đề lên cấp quản lý theo chính sách doanh nghiệp đã định. Đây là tuyến phòng thủ sau cùng, xảy ra **sau** khi cảnh báo sớm của FEAT-17 đã không đủ để ngăn vi phạm.

**Ranh giới với FEAT-17:** FEAT-17 chỉ nhắc người đang phụ trách **trước** khi vi phạm và không chuyển việc cho ai. FEAT-18 chỉ kích hoạt **từ thời điểm vi phạm trở đi**, và có quyền chuyển việc cũng như báo lên cấp trên. Hai tính năng không cấu hình chung một mốc: ngưỡng nhắc trước hạn thuộc FEAT-17 (BR-17.1), các mốc sau khi vi phạm thuộc FEAT-18 (BR-18.1).

**Actor:** Support Manager, Administrator (cấu hình chính sách leo thang); Tiến trình Hệ thống (thực thi); Team Lead, Support Manager (nhận cảnh báo).

**Quy tắc nghiệp vụ:**
- `BR-18.1 (Nhiều mốc leo thang sau vi phạm)`: Doanh nghiệp cấu hình nhiều mốc leo thang, mỗi mốc tính bằng khoảng thời gian làm việc trôi qua **kể từ thời điểm vi phạm cam kết** (ví dụ: ngay khi vi phạm, sau 1 giờ, sau 4 giờ). Mốc trước thời điểm vi phạm không thuộc phạm vi tính năng này.
- `BR-18.2 (Các hành động có thể cấu hình)`: Với mỗi mốc, doanh nghiệp chọn một hoặc nhiều hành động: đánh dấu vé vi phạm ở mức độ tương ứng; thông báo tới một người cụ thể; tự động báo lên cấp quản lý trực tiếp của người phụ trách theo số cấp cấu hình được; hoặc tự động chuyển vé sang người phụ trách khác được chỉ định.
- `BR-18.3 (Không leo thang trùng lặp)`: Mỗi mốc chỉ kích hoạt đúng một lần cho mỗi vé, tránh gửi cảnh báo lặp lại gây nhiễu cho cấp quản lý.
- `BR-18.4 (Không có cấp quản lý để leo thang)`: Nếu người phụ trách không có cấp quản lý được khai báo trong sơ đồ tổ chức, hệ thống gửi cảnh báo tới Trưởng phòng Dịch vụ Khách hàng làm phương án dự phòng, thay vì bỏ qua âm thầm.
- `BR-18.5 (Dừng leo thang khi vé được xử lý)`: Khi vé chuyển sang trạng thái kết thúc, các mốc leo thang chưa kích hoạt sẽ bị hủy.
- `BR-18.6 (Ghi nhận lịch sử leo thang)`: Mọi lần leo thang được ghi vào lịch sử vé (thời điểm, mốc kích hoạt, hành động đã thực thi, người nhận cảnh báo) phục vụ đối soát sau này.

**Tiêu chí chấp nhận:** Xem Kịch bản 3 (mục 6).

---

### FEAT-39 — Leo thang Thủ công & Xử lý Khiếu nại về Chất lượng Phục vụ `[Đã triển khai]`

**Hiện trạng triển khai:** Đã triển khai đầy đủ BR-39.1, BR-39.2, BR-39.3 và BR-39.4 (Refs crmsaassaudi/product-management#220).

**Mô tả nghiệp vụ:** Không phải mọi tình huống cần quản lý can thiệp đều gắn với vi phạm thời hạn. Khách hàng có thể bức xúc, đe dọa chấm dứt hợp đồng, hoặc khiếu nại về chính thái độ của tư vấn viên đang phục vụ họ — những trường hợp này cần chuyển lên quản lý ngay, không chờ đồng hồ SLA chạy hết.

**Actor:** Support Agent (chủ động leo thang vé mình giữ), Team Lead, Support Manager (tiếp nhận và xử lý).

**Quy tắc nghiệp vụ:**
- `BR-39.1 (Leo thang chủ động)`: Tư vấn viên có thể leo thang một vé lên cấp quản lý bất kỳ lúc nào, kèm lý do bắt buộc nhập. Vé được đánh dấu đang leo thang và quản lý nhận thông báo ngay.
- `BR-39.2 (Đánh dấu khách hàng bức xúc)`: Tư vấn viên có thể gắn cờ "khách hàng không hài lòng" lên vé. Vé mang cờ này được ưu tiên hiển thị trên bảng điều khiển của Trưởng nhóm (FEAT-42) để theo dõi sát.
- `BR-39.3 (Khiếu nại về chính người đang phục vụ)`: Khi khiếu nại nhắm vào tư vấn viên đang giữ vé, Trưởng nhóm phải chuyển vé sang người khác trước khi xử lý. Việc rà soát khiếu nại được ghi trên **một vé khiếu nại riêng**, liên kết với vé gốc, chứ không ghi lẫn vào dòng trao đổi của vé gốc. Tư vấn viên bị khiếu nại mất quyền xem vé khiếu nại này nhưng vẫn xem được vé gốc như bình thường. Chọn cách tách vé thay vì che bớt một phần dòng thời gian vì hai lý do: dòng trao đổi phải hiển thị liền mạch theo BR-13.3, và một dòng thời gian bị khuyết đoạn giữa sẽ tự nó tiết lộ rằng có nội dung đang bị giấu — chính điều mà quy tắc này muốn tránh.
- `BR-39.4 (Không thay đổi cam kết SLA)`: Leo thang thủ công không làm thay đổi hạn chót cam kết đã áp dụng cho vé — đây là công cụ điều phối nội bộ, không phải cơ chế gia hạn với khách hàng.

**Tiêu chí chấp nhận:** Xem Kịch bản 17 (mục 6).

---

## G. ĐÓNG VÉ & KHẢO SÁT HÀI LÒNG KHÁCH HÀNG (CSAT)

### FEAT-19 — Quy trình Giải quyết & Ghi nhận Nguyên nhân Xử lý `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi chuyển vé sang trạng thái kết thúc dạng "đã xử lý xong", người có quyền resolve ghi nhận nguyên nhân xử lý (chọn từ danh mục nguyên nhân do doanh nghiệp cấu hình) và tóm tắt giải pháp. Dữ liệu này là đầu vào để tìm nguyên nhân gốc rễ của các sự cố lặp lại và để xây dựng cơ sở tri thức.

**Actor:** Người có quyền resolve (xem mục 1.5).

**Quy tắc nghiệp vụ:**
- `BR-19.1 (Thông tin bắt buộc khi kết thúc)`: Danh mục trạng thái vé có thuộc tính cấu hình `requiresResolutionReason` (cho phép doanh nghiệp quyết định trạng thái nào bắt buộc ghi nhận nguyên nhân xử lý, ví dụ "Đã giải quyết", trong khi trạng thái "Đóng hoàn tất" hoặc "Đóng do spam" có thể không bắt buộc). Khi chuyển vé sang một trạng thái có `requiresResolutionReason = true`, hệ thống bắt buộc phải có nguyên nhân xử lý (`resolutionCodeId`). Hệ thống ưu tiên lấy `resolutionCodeId` từ dữ liệu gửi lên trong yêu cầu chuyển trạng thái; nếu dữ liệu gửi lên không kèm trường này, hệ thống kiểm tra giá trị `resolutionCodeId` hiện có sẵn trên vé. Nếu cả hai đều không có, thao tác chuyển trạng thái bị từ chối bằng lỗi `BadRequestException`. Vé đã có sẵn nguyên nhân xử lý được phép chuyển trạng thái thành công mà không cần gửi lại. Ràng buộc này không chặn các thao tác chuyển sang trạng thái không đánh dấu bắt buộc và không chặn tiến trình tự động chuyển vé sang "đã đóng hoàn tất" theo FEAT-22.
- `BR-19.2 (Phải có phản hồi công khai trước khi kết thúc)`: Không được chuyển vé sang trạng thái kết thúc nếu chưa từng có phản hồi công khai nào gửi tới khách hàng. Lý do: đóng vé mà chưa hề trả lời khách hàng là tình huống phục vụ không chấp nhận được, dù sự cố có thể đã tự hết.
- `BR-19.3 (Danh mục nguyên nhân cấu hình được)`: Doanh nghiệp tự định nghĩa danh mục nguyên nhân xử lý theo đặc thù dịch vụ của mình.
- `BR-19.4 (Ghi nhận thời điểm và người xử lý)`: Hệ thống ghi lại thời điểm kết thúc và người thực hiện, làm căn cứ tính chỉ số tuân thủ cam kết và hiệu suất cá nhân.

**Hiện trạng triển khai:** Đã triển khai đầy đủ BR-19.1 (với thuộc tính `requiresResolutionReason` trên trạng thái vé), BR-19.2, BR-19.3, BR-19.4 (Refs `crmsaassaudi/product-management#230`).

**Tiêu chí chấp nhận:** Xem Kịch bản 4 (mục 6).

---

### FEAT-20 — Khảo sát Đánh giá Sự Hài lòng 1-5 Sao (CSAT) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi vé kết thúc, hệ thống tự động chuẩn bị và gửi tới khách hàng một đường dẫn khảo sát dành riêng cho vé đó. Khách hàng chấm điểm 1-5 sao kèm nhận xét qua đường dẫn này. Kết quả được ghi nhận vào vé và vào hồ sơ hiệu suất của tư vấn viên đã xử lý.

**Actor:** Khách hàng (chấm điểm); Tiến trình Hệ thống (gửi khảo sát tự động); Support Manager (cấu hình mẫu khảo sát và kênh gửi).

**Quy tắc nghiệp vụ:**
- `BR-20.1 (Thời hạn khảo sát)`: Đường dẫn khảo sát có hiệu lực trong 7 ngày kể từ khi được gửi. Quá hạn, đường dẫn không còn dùng được và vé được ghi nhận là "khách hàng không phản hồi khảo sát".
- `BR-20.2 (Gửi tự động, không phụ thuộc tư vấn viên)`: Việc gửi khảo sát phải do hệ thống tự động thực hiện theo cấu hình của doanh nghiệp, **không để tư vấn viên tự quyết định gửi cho vé nào**. Lý do: nếu tư vấn viên được chọn, họ sẽ chỉ gửi khảo sát cho các vé mình xử lý tốt, khiến điểm CSAT (KPI-03) bị thiên lệch và mất giá trị đánh giá.
- `BR-20.3 (Mỗi lần kết thúc chỉ một khảo sát)`: Tại mỗi thời điểm, một vé chỉ có tối đa một đường dẫn khảo sát còn hiệu lực, và mỗi vé chỉ đóng góp tối đa một điểm đánh giá vào KPI-03. Vé bị mở lại rồi kết thúc lại không làm phát sinh khảo sát chồng lấn — xem BR-20.6 về cách xử lý. Mục đích: tránh làm phiền khách hàng và tránh một vé bị đếm nhiều lần trong dữ liệu đo lường.
- `BR-20.4 (Loại trừ khảo sát)`: Hệ thống **bắt buộc** không gửi khảo sát cho vé đã bị gộp vào vé khác (vé phụ theo FEAT-27) và vé bị đánh dấu spam — khách hàng của vé phụ sẽ chấm điểm qua vé chính, vì nội dung của họ đã được chuyển sang đó. Quy tắc này đồng bộ với BR-27.6 (loại vé phụ khỏi thống kê) để một lần liên hệ chỉ sinh một điểm đánh giá. Ngoài hai trường hợp bắt buộc trên, doanh nghiệp cấu hình thêm các trường hợp loại trừ khác nếu muốn (ví dụ vé do nội bộ tự tạo, vé thử nghiệm).
- `BR-20.5 (Mốc gửi khảo sát trong vòng đời hai trạng thái kết thúc)`: Khảo sát được gửi tại thời điểm vé chuyển sang trạng thái kết thúc dạng **"đã xử lý xong"** — tức ngay sau khi tư vấn viên hoàn tất công việc, khi trải nghiệm phục vụ còn mới trong trí nhớ khách hàng. Không chờ tới khi vé chuyển sang "đã đóng hoàn tất" theo FEAT-22, vì mốc đó cách thời điểm phục vụ nhiều ngày làm việc và tỷ lệ khách hàng phản hồi sẽ sụt mạnh. Nếu vé đi thẳng sang "đã đóng hoàn tất" mà không qua "đã xử lý xong", khảo sát được gửi tại mốc đó.
- `BR-20.6 (Vé mở lại trong khi khảo sát còn hiệu lực)`: Khi vé được mở lại (FEAT-21) trong lúc đường dẫn khảo sát chưa hết hạn, đường dẫn đó bị vô hiệu hóa ngay và điểm đã chấm (nếu có) được gỡ khỏi cách tính KPI-03. Lý do: khách hàng chấm điểm cho một lần phục vụ chưa thực sự giải quyết xong vấn đề thì điểm đó không phản ánh đúng chất lượng. Khi vé kết thúc lần kế tiếp, hệ thống gửi **một** khảo sát mới — đây là ngoại lệ duy nhất của BR-20.3, và ngoại lệ này chỉ áp dụng khi khảo sát trước đã bị vô hiệu hóa, nên khách hàng không bao giờ nhận hai khảo sát còn hiệu lực cùng lúc.

**Hiện trạng triển khai:** Đã triển khai đầy đủ BR-20.1, BR-20.2, BR-20.3, BR-20.4, BR-20.5 và BR-20.6 (Refs `crmsaassaudi/product-management#219`).

**Tiêu chí chấp nhận:** Xem Kịch bản 4 (mục 6).

---

### FEAT-21 — Quy định Mở lại Vé đã Giải quyết (Ticket Reopening) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Nếu khách hàng phản hồi lại sau khi vé đã ở trạng thái kết thúc, người có quyền resolve có thể mở lại vé, đưa về trạng thái đang xử lý bình thường. Vì một vé được coi là "đã xử lý xong" đã được tính vào các chỉ số tuân thủ SLA, việc mở lại cần có giới hạn thời gian hợp lý để không làm sai lệch ý nghĩa của các chỉ số đó và không để một vé bị "treo" mở-đóng nhiều lần trong thời gian dài.

**Actor:** Người có quyền resolve (xem mục 1.5).

**Quy tắc nghiệp vụ:**
- `BR-21.1 (Xác nhận mở lại)`: Thao tác mở lại vé yêu cầu xác nhận rõ ràng, tránh mở lại nhầm do thao tác vội.
- `BR-21.2 (Đếm số lần mở lại)`: Hệ thống ghi nhận số lần vé đã từng được mở lại và thời điểm mở lại gần nhất, phục vụ theo dõi chất lượng xử lý.
- `BR-21.3 (Giới hạn thời gian mở lại)`: Doanh nghiệp cấu hình khoảng thời gian tối đa được phép mở lại kể từ khi vé chuyển sang trạng thái kết thúc (mặc định 7 ngày theo `CFG-21-01`). Trong khoảng thời gian đó, khách hàng phản hồi lại sẽ mở lại đúng vé cũ. Quá khoảng thời gian đó, hệ thống chặn mở lại vé cũ (ném `TICKET_REOPEN_WINDOW_EXPIRED`), thay vào đó tạo một vé mới liên kết tham chiếu (`precedingTicketId` / `relatedTo`) và nhận SLA mới từ đầu.

**Hiện trạng triển khai:** Đã triển khai đầy đủ BR-21.1, BR-21.2 và BR-21.3 (Refs `crmsaassaudi/product-management#218`).

**Tiêu chí chấp nhận:** Xem Kịch bản 8 và Kịch bản 8b (mục 6).

---

### FEAT-22 — Tự động Đóng Vé sau Thời gian Không Phản hồi `[Đã triển khai]`

**Mô tả nghiệp vụ:** Sau khi vé được xử lý xong, nếu khách hàng không phản hồi thêm trong một khoảng thời gian do doanh nghiệp cấu hình, hệ thống tự động chuyển vé sang trạng thái đóng hoàn tất. Không có cơ chế này, các vé đã xử lý xong sẽ tồn đọng vô thời hạn, làm sai lệch số liệu vé đang mở và khiến Trưởng nhóm không nhìn đúng khối lượng công việc thực tế.

**Actor:** Support Manager, Administrator (cấu hình thời hạn); Tiến trình Hệ thống (thực hiện đóng tự động).

**Quy tắc nghiệp vụ:**
- `BR-22.1 (Thời hạn chờ)`: Doanh nghiệp cấu hình số giờ chờ kể từ khi vé chuyển sang "đã xử lý xong" (mặc định 48 giờ làm việc theo `CFG-22-01`).
- `BR-22.2 (Khách hàng phản hồi làm dừng đếm)`: Nếu khách hàng phản hồi trong thời gian chờ, vé không bị đóng tự động mà quay lại luồng xử lý bình thường (xem FEAT-21).
- `BR-22.3 (Thông báo trước khi đóng)`: Trước khi đóng tự động (mặc định 12 giờ làm việc trước mốc đóng theo `CFG-22-02`), hệ thống gửi thông báo nhắc khách hàng rằng vé sắp được đóng và họ có thể phản hồi nếu vấn đề chưa được giải quyết triệt để.
- `BR-22.4 (Đóng tự động không ảnh hưởng chỉ số tuân thủ)`: Thời điểm tính tuân thủ cam kết xử lý dứt điểm là lúc vé chuyển sang "đã xử lý xong", không phải lúc đóng tự động.

**Hiện trạng triển khai:** Đã triển khai đầy đủ qua BullMQ `ticket-auto-close` queue, tính thời gian làm việc qua `BusinessHoursService`, phát sự kiện nhắc `ticket.auto_close_reminder` và chuyển vé sang `closed` bảo toàn `resolvedAt` (Refs `crmsaassaudi/product-management#218`).

**Tiêu chí chấp nhận:** Xem Kịch bản 29 (mục 6).

---

## H. THAO TÁC HÀNG LOẠT, NHẬP/XUẤT & THÙNG RÁC

### FEAT-23 — Gắn Nhãn Hàng loạt cho Nhiều Vé `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tích chọn nhiều vé cùng lúc để gắn nhãn phân loại hàng loạt, phục vụ việc nhóm các vé liên quan tới cùng một chiến dịch, một đợt sự cố hoặc một chủ đề cần theo dõi riêng.

**Actor:** Team Lead, Support Manager.

**Quy tắc nghiệp vụ:**
- `BR-23.1 (Giới hạn mỗi lượt)`: Tối đa 500 vé cho mỗi lượt thao tác, để thao tác hoàn tất trong thời gian chấp nhận được và người dùng kiểm soát được phạm vi ảnh hưởng.
- `BR-23.2 (Xác nhận phạm vi trước khi thực hiện)`: Hệ thống hiển thị rõ số lượng vé sẽ bị ảnh hưởng và yêu cầu xác nhận trước khi thực hiện.
- `BR-23.3 (Báo cáo kết quả)`: Sau khi hoàn tất, hệ thống báo số vé thành công và liệt kê các vé không xử lý được kèm lý do (ví dụ vé đã bị khóa do gộp).
- `BR-23.4 (Ghi vết thao tác hàng loạt)`: Thao tác hàng loạt được ghi vào nhật ký thay đổi của từng vé bị ảnh hưởng theo FEAT-44.

*Lưu ý phạm vi:* Hiện tại thao tác hàng loạt chỉ hỗ trợ gắn nhãn. Cập nhật trạng thái hàng loạt chưa có — xem mục 7.3. Riêng việc chuyển người phụ trách hàng loạt được đặc tả thành một tính năng riêng do tính chất nghiệp vụ khác biệt (xem FEAT-36).

**Phụ thuộc chưa sẵn sàng:** BR-23.4 chờ FEAT-44. Trong thời gian chờ, một thao tác sửa cùng lúc tới 500 vé **không để lại dấu vết ai đã làm và đã đổi gì** — khi phát hiện sai sót, không có căn cứ để truy ngược và khôi phục đúng trạng thái cũ.

**Tiêu chí chấp nhận:** Xem Kịch bản 37 (mục 6).

---

### FEAT-24 — Nhập Dữ liệu Vé Hỗ trợ từ Tệp `[Đã triển khai]`

**Mô tả nghiệp vụ:** Nhập danh sách vé hỗ trợ từ tệp dữ liệu, phục vụ chuyển đổi dữ liệu từ hệ thống cũ khi doanh nghiệp mới bắt đầu sử dụng, hoặc nhập bổ sung các vé được tiếp nhận ngoài hệ thống.

**Actor:** Support Manager, Administrator.

**Quy tắc nghiệp vụ:**
- `BR-24.1 (Giới hạn tệp nhập)`: Dung lượng tối đa mỗi tệp là 50MB.
- `BR-24.2 (Đối chiếu cột dữ liệu)`: Trước khi nhập, người thực hiện đối chiếu các cột trong tệp với các trường thông tin của vé, và xem trước kết quả trên một số dòng mẫu để phát hiện sai lệch sớm.
- `BR-24.3 (Xử lý dòng lỗi)`: Các dòng hợp lệ vẫn được nhập thành công; các dòng lỗi bị bỏ qua và được liệt kê trong báo cáo kết quả kèm lý do cụ thể của từng dòng. Hệ thống **không** hủy toàn bộ lần nhập chỉ vì một số dòng lỗi, vì với tệp hàng chục nghìn dòng thì việc đó khiến quá trình chuyển đổi dữ liệu không bao giờ hoàn tất.
- `BR-24.4 (Chống nhập trùng)`: Nếu tệp chứa mã tham chiếu từ hệ thống cũ, hệ thống dùng mã đó để nhận diện và bỏ qua các bản ghi đã nhập ở lần trước, cho phép nhập lại an toàn sau khi sửa lỗi.
- `BR-24.5 (Vé nhập khẩu không tính vào chỉ số)`: Vé được nhập từ hệ thống cũ bị loại khỏi thống kê tuân thủ cam kết (BR-43.2), vì chúng không phản ánh chất lượng phục vụ trên hệ thống hiện tại.
- `BR-24.6 (Theo dõi tiến độ)`: Người thực hiện theo dõi được tiến độ xử lý và tải về báo cáo kết quả sau khi hoàn tất.


**Tiêu chí chấp nhận:** Xem Kịch bản 33 (mục 6).

---

### FEAT-25 — Xuất Báo cáo Vé Hỗ trợ Bảo mật qua Đường dẫn Tải có Thời hạn `[Đã triển khai]`

**Mô tả nghiệp vụ:** Xuất danh sách vé ra tệp để phân tích ngoài hệ thống hoặc gửi kèm báo cáo cho khách hàng. Vì tệp xuất chứa dữ liệu cá nhân của khách hàng, việc xuất phải được kiểm soát và ghi vết.

**Actor:** Support Manager, Administrator.

**Quy tắc nghiệp vụ:**
- `BR-25.1 (Đường dẫn tải có thời hạn)`: Tệp được tải về qua đường dẫn bảo mật có thời hạn 24 giờ; hết hạn phải yêu cầu xuất lại.
- `BR-25.2 (Xuất theo phạm vi quyền)`: Người dùng chỉ xuất được dữ liệu trong phạm vi quyền xem của mình — không thể dùng chức năng xuất để lấy dữ liệu ngoài phạm vi được phép.
- `BR-25.3 (Che trường nhạy cảm)`: Doanh nghiệp cấu hình được việc che bớt các trường chứa dữ liệu cá nhân đối với những vai trò không cần xem đầy đủ (xem BR-45.5).
- `BR-25.4 (Ghi vết mọi lần xuất)`: Mỗi lần xuất được ghi vào nhật ký thao tác theo BR-44.4, phục vụ kiểm soát rủi ro rò rỉ dữ liệu khách hàng.
- `BR-25.5 (Hai cấp phạm vi xuất dữ liệu)`: Phân biệt rõ hai mức: **xuất trong phạm vi phòng ban** — tương ứng ký hiệu "Scope PB" tại ma trận mục 5, tức toàn bộ vé thuộc phòng ban mà Trưởng nhóm phụ trách, giao với ca trực đang hoạt động nếu doanh nghiệp có bật ca trực — và **xuất toàn bộ không gian làm việc**, chỉ thuộc Trưởng phòng và Quản trị viên. Không gộp hai mức thành một quyền, vì việc lấy toàn bộ dữ liệu khách hàng của doanh nghiệp ra khỏi hệ thống có mức rủi ro khác hẳn việc xuất danh sách vé trong phạm vi quản lý hàng ngày.


**Tiêu chí chấp nhận:** Xem Kịch bản 34 (mục 6).

---

### FEAT-26 — Thùng rác Vé Hỗ trợ & Phục hồi Bản ghi `[Đã triển khai]`

**Mô tả nghiệp vụ:** Vé bị xóa không mất ngay mà được chuyển vào Thùng rác, cho phép khôi phục khi xóa nhầm. Đây là lớp bảo vệ chống mất dữ liệu do thao tác sai — tình huống phổ biến khi nhiều người cùng làm việc trên hệ thống.

**Actor:** Support Manager, Administrator (xóa và phục hồi); Tiến trình Hệ thống (xóa vĩnh viễn khi hết hạn lưu trữ).

**Quy tắc nghiệp vụ:**
- `BR-26.1 (Thời hạn lưu trong Thùng rác)`: Vé đã xóa được lưu 30 ngày, sau đó hệ thống tự động xóa vĩnh viễn. *(Khoảng cách ghi nhận: thời hạn lưu trữ Thùng rác hiện là biến môi trường của toàn hệ thống `TICKET_RECYCLE_BIN_RETENTION_DAYS`, chưa phải tham số cấu hình riêng theo từng doanh nghiệp).*
- `BR-26.2 (Phục hồi nguyên trạng)`: Khi phục hồi, vé trở lại đúng trạng thái, người phụ trách và toàn bộ lịch sử trao đổi như trước khi xóa. Ngoại lệ: vé đã bị ẩn danh hóa trong lúc nằm trong Thùng rác (BR-45.6) được phục hồi ở dạng đã ẩn danh — dữ liệu cá nhân đã gỡ bỏ không được khôi phục lại trong bất kỳ trường hợp nào.
- `BR-26.3 (Quyền phục hồi ngang quyền xóa)`: Chỉ người có quyền xóa mới có quyền phục hồi, và chỉ phục hồi được những vé mà họ vốn có quyền nhìn thấy — tránh việc dùng Thùng rác như đường vòng để tiếp cận dữ liệu ngoài phạm vi.
- `BR-26.4 (Không xóa vé đang là vé cha)`: Vé đang có vé con trực thuộc không xóa được cho tới khi gỡ hết quan hệ cha - con, tránh để lại các vé con mồ côi không truy vết được bối cảnh.
- `BR-26.5 (Vé đã xóa không tính vào chỉ số)`: Vé trong Thùng rác bị loại khỏi mọi báo cáo và thống kê tuân thủ cam kết.


**Tiêu chí chấp nhận:** Xem Kịch bản 35 và Kịch bản 44 (mục 6).

---

## I. QUẢN TRỊ QUY TRÌNH NÂNG CAO & RỦI RO KHÁCH HÀNG

### FEAT-27 — Gộp Vé Trùng lặp (Ticket Merging) `[Đã triển khai một phần]`

**Mô tả nghiệp vụ:** Cho phép gộp một vé trùng lặp (vé phụ) vào một vé chính, đồng thời đảm bảo khách hàng đã tạo vé phụ không bị "mất dấu" yêu cầu của mình.

**Actor:** Vai trò được phép gộp vé do từng doanh nghiệp cấu hình (xem BR-27.7); mặc định là Support Manager, có thể mở rộng xuống Team Lead. Support Manager luôn giữ quyền hoàn tác theo BR-27.5.

**Quy tắc nghiệp vụ:**
- `BR-27.1 (Hợp nhất lịch sử)`: Toàn bộ nội dung trao đổi của vé phụ được nối tiếp vào vé chính; vé phụ bị đóng và khóa lại (không thể chỉnh sửa thêm), nhưng lịch sử của riêng vé phụ vẫn được giữ nguyên để tra cứu khi cần.
- `BR-27.2 (Toàn vẹn giao dịch)`: Thao tác gộp vé được thực hiện trong một giao dịch dữ liệu duy nhất, đảm bảo không xảy ra trạng thái gộp dở dang nếu có lỗi giữa chừng.
- `BR-27.3 (Thông báo cho khách hàng của vé phụ)`: Ngay khi gộp, khách hàng đã tạo vé phụ phải nhận được thông báo qua kênh họ đã dùng để tạo vé, nêu rõ yêu cầu của họ đã được nhập vào vé chính (kèm mã số vé chính) và họ sẽ tiếp tục nhận cập nhật tại đó. Đây là điều kiện bắt buộc để tính năng được coi là hoàn chỉnh, tránh khách hàng cảm giác yêu cầu của mình bị "biến mất" không lời giải thích.

- `BR-27.4 (Chỉ gộp vé cùng một khách hàng)`: Chỉ được gộp các vé thuộc cùng một khách hàng liên hệ. Gộp vé của hai khách hàng khác nhau bị từ chối, vì sẽ khiến khách hàng này nhìn thấy nội dung trao đổi của khách hàng kia — vi phạm bảo mật dữ liệu. Trường hợp nhiều khách hàng cùng gặp một sự cố phải dùng mô hình vé cha - vé con (FEAT-28), không phải gộp vé.
- `BR-27.5 (Hoàn tác gộp nhầm)`: Trong khoảng thời gian cấu hình được sau khi gộp (mặc định 24 giờ), Trưởng phòng có thể hoàn tác thao tác gộp, khôi phục vé phụ về trạng thái trước đó. Quá thời hạn này, thao tác gộp là vĩnh viễn. Lý do: gộp nhầm là lỗi thao tác phổ biến, và việc phục hồi thủ công gần như không khả thi nếu không có cơ chế hoàn tác.
- `BR-27.6 (Tính SLA của vé phụ)`: Vé phụ đã gộp không được tính vào thống kê tuân thủ SLA, vì nó không còn được xử lý độc lập. Vé chính giữ nguyên cam kết ban đầu của mình.
- `BR-27.7 (Cấp quyền gộp vé do doanh nghiệp quyết định)`: Cấp thấp nhất được phép gộp vé là một tham số cấu hình của từng doanh nghiệp, vì mô hình tổ chức khác nhau: doanh nghiệp có đội nhỏ, vé trùng lặp nhiều thường cần Trưởng nhóm gộp ngay để không ùn tắc; doanh nghiệp có dữ liệu nhạy cảm lại muốn giữ thao tác này ở cấp Trưởng phòng. **Mặc định là Support Manager** — chọn mức chặt làm mặc định vì gộp nhầm chỉ sửa được trong thời hạn hoàn tác tại BR-27.5, sau đó là vĩnh viễn. Riêng quyền **hoàn tác** gộp không hạ xuống dưới Support Manager trong mọi cấu hình, để người gộp nhầm không tự xóa dấu vết thao tác của mình.

**Luồng ngoại lệ:**
- Vé phụ đang là vé cha của các vé khác: hệ thống từ chối gộp và yêu cầu gỡ quan hệ cha - con trước.
- Vé được chọn làm vé chính đang ở trạng thái kết thúc: hệ thống cảnh báo và yêu cầu chọn vé chính khác hoặc mở lại vé đó trước.

**Khoảng cách cần bổ sung:** Hệ thống hiện thực hiện đúng BR-27.1 và BR-27.2 (hợp nhất lịch sử, giao dịch nguyên tử). Các quy tắc **chưa được xây dựng**: BR-27.3 (thông báo cho khách hàng vé phụ), BR-27.4 (chặn gộp vé khác khách hàng — đây là rủi ro lộ dữ liệu giữa hai khách hàng, cần ưu tiên cao nhất), BR-27.5 (hoàn tác gộp nhầm) và BR-27.6 (loại vé đã gộp khỏi thống kê SLA).

**Tiêu chí chấp nhận:** Xem Kịch bản 5, Kịch bản 26 và Kịch bản 46 (mục 6).

---

### FEAT-41 — Tách Vé khi Một Yêu cầu Chứa Nhiều Vấn đề `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khách hàng thường gửi một yêu cầu duy nhất nhưng bên trong chứa nhiều vấn đề khác nhau (ví dụ vừa báo lỗi kỹ thuật, vừa thắc mắc về hóa đơn). Mỗi vấn đề cần được xử lý bởi người khác nhau, có thời hạn khác nhau và kết thúc ở thời điểm khác nhau. Nếu buộc phải xử lý chung trong một vé, chỉ số đo lường bị sai lệch và một vấn đề đã xong vẫn phải chờ vấn đề còn lại.

**Actor:** Support Agent, Team Lead, Support Manager.

**Quy tắc nghiệp vụ:**
- `BR-41.1 (Tách thành vé mới)`: Tư vấn viên chọn một hoặc nhiều nội dung trao đổi trong vé gốc để tách sang một vé mới, kèm tiêu đề và phân loại riêng cho vé mới.
- `BR-41.2 (Giữ liên kết hai chiều)`: Vé mới và vé gốc được liên kết tham chiếu với nhau để tra cứu bối cảnh đầy đủ. Nội dung đã tách vẫn hiển thị trên vé gốc (không bị xóa khỏi lịch sử), kèm ghi chú rằng nội dung này đã được chuyển sang vé nào xử lý.
- `BR-41.3 (Cam kết SLA của vé mới)`: Vé mới nhận cam kết SLA tính từ thời điểm tách, không kế thừa hạn chót của vé gốc — vì đây là một vấn đề mới được nhận diện, không phải vấn đề đã tồn tại từ đầu.
- `BR-41.4 (Thông báo khách hàng)`: Khách hàng nhận được thông báo về vé mới được tạo kèm mã số, để họ biết vấn đề thứ hai đang được xử lý riêng và theo dõi đúng chỗ.

**Trạng thái triển khai:** Đã triển khai đầy đủ theo GitHub Issue #224 (`crmsaassaudi/product-management#224`).
- `BR-41.1`: tách nội dung sang vé mới có mã riêng, liên kết hai chiều qua `splitFromTicketId` và `splitTicketCount`.
- `BR-41.2`: nội dung đã tách **vẫn ở lại vé gốc** kèm `splitToTicketNumber` và `splitAt`; không xóa khỏi lịch sử.
- `BR-41.3`: vé mới nhận cam kết tính từ thời điểm tách.
- `BR-41.4`: khách hàng được báo mã vé mới trên đúng kênh họ gửi yêu cầu, qua `OmniEvents.TICKET_SPLIT_NOTICE` → `SystemReplyListener` (thừa hưởng cơ chế thử lại BR-37.5). Thao tác tách ghi vết theo BR-44.1 trên cả hai vé.

**Tiêu chí chấp nhận:** Xem Kịch bản 18 (mục 6).

---

### FEAT-28 — Quản lý Sự cố Diện rộng theo Mô hình Vé Cha - Vé Con (Major Incident Management) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi một sự cố duy nhất (ví dụ lỗi hệ thống thanh toán, sập dịch vụ) khiến nhiều khách hàng cùng tạo vé báo lỗi, Team Lead cần một cách xử lý tập trung: tạo một Vé Sự cố (vé cha) đại diện cho sự việc, gán các vé của từng khách hàng làm vé con, và khi sự cố được khắc phục, cập nhật một lần duy nhất trên vé cha để toàn bộ vé con liên quan được xử lý đồng loạt — thay vì phải vào từng vé một để trả lời hàng trăm khách hàng giống hệt nhau.

**Actor:** Team Lead, Support Manager.

**Quy tắc nghiệp vụ:**
- `BR-28.1 (Gán vé con)`: Team Lead có thể gán một hoặc nhiều vé đang mở làm vé con của một vé cha đại diện cho sự cố.
- `BR-28.2 (Cập nhật tiến độ hàng loạt)`: Khi Team Lead thêm một cập nhật tiến độ công khai trên vé cha (ví dụ "Đội kỹ thuật đang khắc phục, dự kiến xong lúc..."), nội dung này phải tự động xuất hiện trên dòng trao đổi của toàn bộ vé con, để khách hàng của từng vé con nhận được thông tin kịp thời mà tư vấn viên không phải sao chép thủ công. Cập nhật lan truyền này **được tính là phản hồi công khai** của từng vé con: nó thỏa mãn BR-19.2 và làm hoàn tất mốc cam kết Phản hồi Đầu tiên theo BR-07.3. Lý do: đây là nội dung do con người soạn và thực sự gửi tới khách hàng, khác hẳn thông báo hệ thống tự sinh khi đổi trạng thái. Không có quy định này, 150 vé con của một sự cố diện rộng sẽ không bao giờ đóng được vì thiếu phản hồi riêng lẻ, và toàn bộ giá trị của mô hình vé cha - vé con bị vô hiệu. Mốc cam kết Phản hồi Đầu tiên của từng vé con được ghi nhận hoàn tất tại **thời điểm Trưởng nhóm đăng cập nhật trên vé cha**, không phải thời điểm nội dung lan truyền xong tới vé con đó — vì độ trễ lan truyền là vấn đề kỹ thuật nội bộ (NFR-08 cho phép tới 5 phút) và không được phép biến thành vi phạm cam kết với khách hàng.
- `BR-28.3 (Xử lý hàng loạt khi giải quyết xong)`: Khi Team Lead chuyển vé cha sang trạng thái kết thúc dạng "đã xử lý xong", hệ thống hiển thị danh sách vé con đang mở để Team Lead xác nhận chuyển đồng loạt sang cùng trạng thái trong một thao tác duy nhất. Mỗi khách hàng nhận khảo sát CSAT riêng của vé mình (không gộp chung một khảo sát cho tất cả).
- `BR-28.4 (Loại trừ vé con cần xử lý riêng)`: Trong danh sách xác nhận ở BR-28.3, Team Lead có thể bỏ chọn những vé con cần tiếp tục xử lý riêng (ví dụ khách hàng đó còn khiếu nại thêm ngoài phạm vi sự cố chung). Ngoài ra, một vé con đã được tư vấn viên đánh dấu "có vấn đề riêng ngoài sự cố chung" sẽ mặc định không bị chuyển trạng thái theo vé cha, mà phải được xử lý và đóng độc lập. Quy tắc này đảm bảo việc xử lý hàng loạt không vô tình đóng vé của khách hàng đang có khiếu nại chưa giải quyết.
- `BR-28.5 (Thông tin kết thúc của vé con lấy từ vé cha)`: Khi đóng hàng loạt theo BR-28.3, nguyên nhân xử lý, tóm tắt giải pháp và các trường bắt buộc của từng vé con được lấy từ vé cha, thay vì bắt Team Lead nhập lại 150 lần. Như vậy BR-19.1 và BR-05.1 vẫn được đáp ứng — vé con vẫn có đủ thông tin kết thúc, chỉ khác ở chỗ thông tin đó nhập một lần trên vé cha. Vé con bị bỏ chọn theo BR-28.4 vẫn phải nhập riêng như vé thông thường.
- `BR-28.6 (Vé con và hạn mức năng lực)`: Vé con của một sự cố diện rộng **không tính vào hạn mức năng lực** của người phụ trách theo BR-10.1, vì chúng được xử lý tập trung qua vé cha chứ không tiêu tốn thời gian riêng. Vé cha vẫn được tính bình thường. Không có quy tắc này, một sự cố 150 vé sẽ chiếm hết năng lực của 15 tư vấn viên và đẩy mọi vé mới không liên quan vào hàng đợi chung — đúng lúc doanh nghiệp cần năng lực xử lý nhất.

**Trạng thái triển khai:** Đã triển khai đầy đủ theo GitHub Issue #221 (`crmsaassaudi/product-management#221`).
- `BR-28.1`: Gán vé con, liên kết quan hệ phân cấp vé.
- `BR-28.2 & NFR-08`: Lan truyền cập nhật tiến độ công khai từ vé cha xuống toàn bộ vé con **đang mở** trong vòng 5 phút (hỗ trợ quy mô 500 vé con); tính là phản hồi công khai của từng vé con (BR-19.2) và hoàn tất mốc SLA Phản hồi Đầu tiên (BR-07.3) ghi nhận tại thời điểm đăng trên vé cha. Vé con đã đóng không nhận lan truyền, để số liệu tuân thủ đã chốt của vé đó không bị ghi đè.
- `BR-28.3`: Đóng hàng loạt vé con khi vé cha giải quyết xong; mỗi khách hàng nhận khảo sát CSAT riêng của vé mình.
- `BR-28.4`: Xem trước danh sách vé con mở, loại trừ vé con cần xử lý riêng; vé con có `hasStandaloneIssue: true` mặc định không được chọn và **bị máy chủ từ chối đóng kể cả khi mã vé được gửi lên** — quy tắc kiểm ở tầng dữ liệu, không phụ thuộc ô tích trên giao diện.
- `BR-28.5`: Thông tin kết thúc của vé con (`resolutionCodeId`, `resolutionNotes`) kế thừa từ vé cha.
- `BR-28.6`: Vé con không tính vào hạn mức năng lực (`BR-10.1`, `LOAD_SOURCES.Ticket`) của tư vấn viên.


**Tiêu chí chấp nhận:** Xem Kịch bản 6 (mục 6).

---

### FEAT-29 — Liên kết Thủ công Vé Hỗ trợ với Cơ hội Bán hàng `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tư vấn viên hoặc nhân viên kinh doanh có thể liên kết một vé hỗ trợ với một Cơ hội bán hàng (Deal) đang đàm phán, để cả hai bên tra cứu được bối cảnh của nhau — tránh tình huống nhân viên kinh doanh thúc ép ký hợp đồng trong khi khách hàng đang bức xúc vì một sự cố chưa được giải quyết.

**Actor:** Support Agent, Team Lead, Support Manager, Sales Rep.

**Quy tắc nghiệp vụ:**
- `BR-29.1 (Liên kết hai chiều)`: Vé đã liên kết hiển thị được từ màn hình Cơ hội bán hàng, và ngược lại Cơ hội bán hàng hiển thị được từ màn hình vé.
- `BR-29.2 (Tôn trọng quyền xem của từng bên)`: Người dùng chỉ nhìn thấy thông tin liên kết nếu họ có quyền xem bản ghi ở phía bên kia. Nhân viên kinh doanh không có quyền xem vé hỗ trợ sẽ chỉ thấy có liên kết tồn tại mà không thấy nội dung trao đổi.
- `BR-29.3 (Gỡ liên kết)`: Liên kết gỡ được khi liên kết nhầm; việc gỡ được ghi vào nhật ký thay đổi của cả hai bản ghi.

*Lưu ý phạm vi:* Đây là liên kết thủ công. Việc tự động cảnh báo trên bảng Kanban Deals khi khách hàng có vé khẩn cấp chưa xử lý xong chưa tồn tại — xem FEAT-30.


**Tiêu chí chấp nhận:** Xem Kịch bản 7 (mục 6).

---

### FEAT-30 — Bắn Cờ Cảnh báo Rủi ro Kỹ thuật Tự động sang Bảng Kanban Deals `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi một khách hàng có vé hỗ trợ mức ưu tiên cao đang mở chưa xử lý xong, hệ thống tự động hiển thị cảnh báo trên thẻ Cơ hội bán hàng tương ứng, nhắc nhân viên kinh doanh phối hợp giải quyết sự cố trước khi tiếp tục đàm phán. Đây là cơ chế chủ động, khắc phục hạn chế của liên kết thủ công ở FEAT-29 (phụ thuộc việc nhân viên có nhớ tra cứu hay không).

**Actor:** Sales Rep (nhận cảnh báo); Administrator, Support Manager (cấu hình điều kiện bắn cờ); Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-30.1 (Điều kiện bắn cảnh báo)`: Doanh nghiệp cấu hình điều kiện hiển thị cảnh báo — tối thiểu theo mức ưu tiên của vé đang mở và theo việc vé có đang vi phạm cam kết hay không.
- `BR-30.2 (Tự động tắt khi hết điều kiện)`: Cảnh báo tự động biến mất khi không còn vé nào thỏa điều kiện, không cần thao tác thủ công.
- `BR-30.3 (Xem nhanh bối cảnh)`: Nhân viên kinh doanh xem được danh sách vé gây ra cảnh báo (mã số, mức ưu tiên, tình trạng) trong phạm vi quyền của mình, để biết mức độ nghiêm trọng trước khi liên hệ khách hàng. Nếu người xem không có quyền xem vé gây cảnh báo, **cảnh báo vẫn hiển thị nhưng không kèm chi tiết vé** — bản thân việc khách hàng đang có sự cố là thông tin người bán cần biết, còn nội dung khiếu nại thì không.

**Ranh giới với phân hệ Cơ hội bán hàng:** Phân hệ Vé hỗ trợ sở hữu **điều kiện phát cảnh báo** và các tham số cấu hình đi kèm. Việc **hiển thị cảnh báo trên thẻ Kanban** thuộc [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) (FEAT-31).


**Trạng thái triển khai:** Đã triển khai đầy đủ theo GitHub Issue #225 (`crmsaassaudi/product-management#225`).
- `BR-30.1`: điều kiện bắn cờ do doanh nghiệp cấu hình — `CFG-30-03` mức ưu tiên tối thiểu (mặc định HIGH) và `CFG-30-04` chỉ bắn khi vé vi phạm cam kết (mặc định tắt). Tình trạng vi phạm đọc từ `isSlaBreached`.
- `BR-30.2`: cờ hiển thị trên thẻ cơ hội bán hàng kèm số vé vi phạm và vé ưu tiên cao đang mở.
- `BR-30.3`: cờ tự gỡ khi không còn vé nào thỏa điều kiện.

**Tiêu chí chấp nhận:** Xem Kịch bản 36 (mục 6).

---

### FEAT-31 — Chuyển đổi Giải pháp Xử lý thành Bài viết Tri thức `[Đã triển khai]`

**Mô tả nghiệp vụ:** Những giải pháp hay khi xử lý vé thường chỉ nằm lại trong vé đó và mất đi. Tính năng này cho phép chuyển nội dung câu hỏi và giải pháp vừa xử lý thành bản nháp bài viết tri thức, để tái sử dụng cho các trường hợp tương tự và giảm dần khối lượng vé lặp lại.

**Actor:** Support Agent (đề xuất bài viết), Team Lead, Support Manager (duyệt và xuất bản).

**Quy tắc nghiệp vụ:**
- `BR-31.1 (Tạo bản nháp từ vé)`: Từ một vé đã xử lý xong, tư vấn viên tạo được bản nháp bài viết với nội dung câu hỏi và giải pháp được điền sẵn từ vé.
- `BR-31.2 (Làm sạch dữ liệu cá nhân)`: Trước khi xuất bản, người duyệt phải rà soát và loại bỏ mọi thông tin định danh khách hàng khỏi nội dung bài viết — bài viết tri thức là tài liệu dùng chung, không được chứa dữ liệu cá nhân của khách hàng cụ thể.
- `BR-31.3 (Duyệt trước khi xuất bản)`: Bản nháp phải được quản lý duyệt trước khi trở thành bài viết chính thức.
- `BR-31.4 (Giữ liên kết nguồn)`: Bài viết lưu tham chiếu tới vé gốc để tra cứu bối cảnh đầy đủ khi cần cập nhật nội dung.


**Trạng thái triển khai:** Đã triển khai đầy đủ theo GitHub Issue #225 (`crmsaassaudi/product-management#225`).
- `BR-31.1`: tạo bản nháp từ vé đã xử lý, điền sẵn tiêu đề, mô tả vấn đề và giải pháp.
- `BR-31.2`: **không có đường nào xuất bản được** khi người duyệt chưa xác nhận đã rà soát và loại bỏ thông tin định danh khách hàng.
- `BR-31.3`: vòng đời `draft` → `in_review` → `published`, lưu tham chiếu ngược tới vé nguồn.
- `BR-31.4`: **chỉ quản lý hoặc trưởng nhóm** mới xuất bản được, nên người soạn không tự duyệt bài của mình.

**Tiêu chí chấp nhận:** Xem Kịch bản 39 (mục 6).

---

### FEAT-32 — Giám sát Trực tiếp & Nhắc nhở Hậu trường (Whisper Coaching) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép quản lý theo dõi cách nhân viên mới đang xử lý vé và gửi chỉ dẫn riêng cho họ ngay trong lúc làm việc, mà khách hàng không nhìn thấy. Công cụ đào tạo tại chỗ này giúp rút ngắn thời gian nhân viên mới đạt chuẩn phục vụ, đồng thời tránh sai sót với khách hàng.

**Actor:** Team Lead, Support Manager (giám sát và hướng dẫn); Support Agent (được hướng dẫn).

**Quy tắc nghiệp vụ:**
- `BR-32.1 (Chỉ dẫn không lộ ra ngoài)`: Nội dung chỉ dẫn của quản lý chỉ hiển thị cho tư vấn viên được hướng dẫn, không bao giờ xuất hiện ở bất kỳ giao diện nào khách hàng nhìn thấy.
- `BR-32.2 (Minh bạch với người bị giám sát)`: Tư vấn viên phải biết khi nào mình đang được giám sát — giám sát ngầm không thông báo là không chấp nhận được về mặt quan hệ lao động.
- `BR-32.3 (Ghi nhận phục vụ đào tạo)`: Các phiên hướng dẫn được ghi nhận để quản lý theo dõi tiến bộ của nhân viên qua thời gian.


**Trạng thái triển khai:** Đã triển khai đầy đủ theo GitHub Issue #225 (`crmsaassaudi/product-management#225`).
- `BR-32.1`: **chỉ quản lý hoặc trưởng nhóm** mở được phiên giám sát; toàn bộ tính năng bật/tắt theo `CFG-32-01` để doanh nghiệp áp chính sách nội bộ của mình.
- `BR-32.2`: tư vấn viên thấy chỉ báo đang được giám sát **trong suốt phiên** (giao diện hỏi trạng thái mỗi 10 giây), không chỉ lúc bắt đầu.
- `BR-32.3`: ghi nhận phiên hướng dẫn kèm đánh giá và ghi chú đào tạo. Nhắc bài hậu trường lưu dưới dạng ghi chú nội bộ (`kind: note`) nên khách hàng không nhìn thấy.

**Tiêu chí chấp nhận:** Xem Kịch bản 40 (mục 6).

---

### FEAT-33 — Theo dõi Thời lượng Hỗ trợ Thực tế & Giờ Tính phí `[Đã triển khai]`

**Mô tả nghiệp vụ:** Ghi nhận thời gian tư vấn viên bỏ ra để xử lý vé và đánh dấu giờ tính phí dịch vụ đối với các hợp đồng bảo trì có tính phí, làm căn cứ xuất hóa đơn dịch vụ và đánh giá chi phí phục vụ từng khách hàng.

**Actor:** Support Agent (ghi nhận thời gian), Team Lead, Support Manager (duyệt giờ trước khi chuyển sang tính phí).

**Quy tắc nghiệp vụ:**
- `BR-33.1 (Ghi nhận thời gian)`: Tư vấn viên bấm giờ trực tiếp khi bắt đầu và kết thúc xử lý, hoặc nhập tay số phút đã bỏ ra kèm mô tả công việc.
- `BR-33.2 (Phân biệt giờ tính phí)`: Mỗi khoản thời gian được đánh dấu là có tính phí hay không, theo điều khoản hợp đồng dịch vụ của khách hàng đó.
- `BR-33.3 (Duyệt trước khi tính phí)`: Giờ tính phí phải được quản lý duyệt trước khi chuyển sang bộ phận xuất hóa đơn, tránh sai lệch khi đối soát với khách hàng.


**Trạng thái triển khai:** Đã triển khai tới bước xuất tệp bàn giao theo GitHub Issue #225 (`crmsaassaudi/product-management#225`).
- `BR-33.1`, `BR-33.2`: bấm giờ và nhập tay kèm mô tả công việc; đánh dấu giờ tính phí kèm đơn giá tại thời điểm ghi nhận.
- `BR-33.3`: **chỉ quản lý** mới duyệt được giờ tính phí, và chỉ giờ đã duyệt mới vào tệp bàn giao cho khâu xuất hóa đơn.

*Lưu ý phạm vi:* khâu xuất hóa đơn (Billing) nằm ngoài phạm vi tài liệu này, nên tính năng dừng ở bước xuất tệp đối soát.

**Tiêu chí chấp nhận:** Xem Kịch bản 41 (mục 6).

---

## J. BÁO CÁO & GIÁM SÁT HIỆU SUẤT

### FEAT-42 — Bảng điều khiển Hàng đợi Thời gian thực `[Đã triển khai một phần]`

**Mô tả nghiệp vụ:** Trưởng nhóm cần một màn hình duy nhất cho biết tình hình hàng đợi ngay lúc này để điều phối kịp thời: còn bao nhiêu vé chưa ai nhận, vé nào sắp tới hạn, ai đang quá tải, vé nào có khách hàng bức xúc. Nếu chỉ có danh sách vé thô, Trưởng nhóm phải tự lọc và ước lượng bằng mắt, dẫn tới phát hiện vấn đề quá muộn.

**Actor:** Team Lead, Support Manager, Administrator.

**Hiện trạng triển khai:** Đã triển khai bảng điều khiển với đầy đủ các chỉ số hàng đợi, cập nhật theo chu kỳ 30 giây (polling).

**Khoảng cách cần bổ sung:** Cơ chế đẩy dữ liệu tức thời (WebSocket / Server-Sent Events) thay vì cập nhật theo chu kỳ 30 giây.

**Quy tắc nghiệp vụ:**
- `BR-42.1 (Các chỉ số hiển thị tối thiểu)`: Bảng điều khiển hiển thị theo thời gian thực: số vé chưa có người xử lý trong hàng đợi chung, số vé đang mở theo từng trạng thái, số vé đã qua ngưỡng cảnh báo sớm nhưng chưa vi phạm (theo BR-17.1), số vé đã vi phạm cam kết, số vé đang mang cờ khách hàng bức xúc, và số vé đang mở của từng tư vấn viên so với hạn mức năng lực.
- `BR-42.2 (Phạm vi dữ liệu theo quyền)`: Trưởng nhóm nhìn thấy dữ liệu trong phạm vi nhóm mình phụ trách; Trưởng phòng nhìn toàn bộ không gian làm việc.
- `BR-42.3 (Điều phối trực tiếp từ bảng điều khiển)`: Từ bảng điều khiển, Trưởng nhóm gán được vé cho người cụ thể hoặc chuyển vé hàng loạt mà không phải chuyển sang màn hình khác.
- `BR-42.4 (Bộ lọc lưu lại được)`: Người dùng tạo và lưu được các bộ lọc riêng (ví dụ "Vé quá hạn của nhóm tôi", "Vé khách hàng hạng cao đang mở") để mở lại nhanh ở các lần sau.

**Tiêu chí chấp nhận:** Xem Kịch bản 19 (mục 6).

---

### FEAT-43 — Báo cáo Tuân thủ SLA, Hiệu suất Tư vấn viên & Khối lượng Công việc `[Đã triển khai]`

**Mô tả nghiệp vụ:** Trưởng phòng cần bộ báo cáo định kỳ để đánh giá chất lượng dịch vụ, làm căn cứ đánh giá nhân sự, xếp lịch trực và báo cáo mức độ tuân thủ cam kết cho khách hàng theo hợp đồng. Đây là nơi toàn bộ các chỉ số thành công ở mục 2.4 được đo lường thực tế.

**Actor:** Team Lead (phạm vi nhóm), Support Manager, Administrator (toàn bộ).

**Hiện trạng triển khai:** Đã triển khai đầy đủ các chỉ số vé, đo lường tuân thủ SLA và phân tích khối lượng công việc.

**Quy tắc nghiệp vụ:**
- `BR-43.1 (Báo cáo tuân thủ cam kết dịch vụ)`: Báo cáo tỷ lệ tuân thủ cam kết phản hồi đầu tiên và cam kết xử lý dứt điểm, lọc được theo khoảng thời gian, theo nhóm, theo tư vấn viên, theo hạng khách hàng và theo từng khách hàng cụ thể (để gửi kèm báo cáo hợp đồng).
- `BR-43.2 (Quy tắc tính tỷ lệ tuân thủ)`: Tỷ lệ tuân thủ được tính bằng số vé hoàn tất đúng hạn chia cho tổng số vé đến hạn trong kỳ. Các vé sau bị **loại khỏi mẫu số**: vé đã gộp vào vé khác (BR-27.6), vé đang nằm trong Thùng rác (BR-26.5), vé được nhập khẩu từ hệ thống cũ (BR-24.5), và vé được gắn nhãn loại trừ do doanh nghiệp cấu hình — dùng cho các vé nội bộ tự tạo để thử nghiệm hoặc đào tạo. Thời gian đồng hồ bị tạm dừng hợp lệ (FEAT-09) không tính vào thời gian xử lý. Quy tắc này phải được in kèm trên báo cáo để hai bên đối soát khi có tranh chấp.
- `BR-43.3 (Báo cáo hiệu suất tư vấn viên)`: Theo từng tư vấn viên: số vé đã xử lý xong, thời gian xử lý trung bình, tỷ lệ tuân thủ cam kết, điểm hài lòng trung bình nhận được, số vé bị mở lại, số vé bị leo thang.
- `BR-43.4 (Báo cáo khối lượng công việc)`: Phân bố số vé theo khung giờ trong ngày và theo ngày trong tuần, phục vụ xếp lịch trực; phân bố theo danh mục phân loại, phục vụ tìm nguyên nhân gốc rễ của các sự cố lặp lại.
- `BR-43.5 (Báo cáo sự cố diện rộng)`: Với mỗi sự cố diện rộng đã xử lý (FEAT-28), báo cáo nêu số vé con liên quan, thời gian từ lúc tạo vé cha tới lúc toàn bộ khách hàng nhận được phản hồi đầu tiên, và thời gian tới lúc toàn bộ vé con được xử lý xong — đây là cách đo KPI-07.
- `BR-43.6 (Báo cáo phối hợp với bộ phận bán hàng)`: Báo cáo số cơ hội bán hàng đã được cảnh báo rủi ro (FEAT-30) trong kỳ, và trong số đó bao nhiêu trường hợp vé hỗ trợ liên quan được xử lý xong trước khi cơ hội bán hàng chốt — đây là cách đo KPI-08.
- `BR-43.7 (Báo cáo giờ hỗ trợ và giờ tính phí)`: Tổng hợp số giờ hỗ trợ thực tế và số giờ tính phí đã được duyệt (FEAT-33) theo từng khách hàng, từng hợp đồng và từng tư vấn viên trong kỳ — đây là cách đo KPI-10, đồng thời là dữ liệu đầu vào để bộ phận kế toán xuất hóa đơn dịch vụ.
- `BR-43.8 (Xuất và gửi định kỳ)`: Mọi báo cáo xuất được ra tệp và có thể đặt lịch gửi tự động định kỳ tới người nhận được chỉ định (ví dụ báo cáo tuần gửi Trưởng phòng sáng thứ Hai).
- `BR-43.9 (Báo cáo sử dụng cơ chế tạm dừng cam kết)`: Theo từng tư vấn viên và từng nhóm: tỷ lệ thời gian vé nằm ở trạng thái tạm dừng so với tổng thời gian xử lý, số vé bị tạm dừng vượt ngưỡng cảnh báo (BR-09.4), và **số vé đang tạm dừng tại thời điểm xem** — đây là cách đo KPI-11. Cần chỉ số cuối vì BR-09.4 chỉ bắt được việc tạm dừng nhiều lần trên cùng một vé, không bắt được người tạm dừng mỗi vé một lần trên nhiều vé để né hạn mức năng lực theo BR-10.2.
- `BR-43.10 (Báo cáo gián đoạn do nhân sự vắng mặt)`: Với mỗi kỳ nghỉ phép hoặc vắng mặt đã ghi nhận: số vé đang mở tại thời điểm bắt đầu vắng, thời gian từ lúc đó tới khi chuyển giao xong toàn bộ, và số vé bị vi phạm cam kết trong khoảng đó — đây là cách đo KPI-12.
- `BR-43.11 (Báo cáo tuân thủ bảo vệ dữ liệu cá nhân)`: Số yêu cầu về dữ liệu cá nhân đã tiếp nhận trong kỳ, số đã xử lý đúng hạn 72 giờ, số quá hạn kèm lý do, và phạm vi vé bị ảnh hưởng của từng yêu cầu — đây là cách đo KPI-13 và là hồ sơ xuất trình khi cơ quan quản lý kiểm tra.

**Tiêu chí chấp nhận:** Xem Kịch bản 20 (mục 6).

---

## K. NHẬT KÝ THAO TÁC & TRUY VẾT

### FEAT-44 — Nhật ký Thay đổi & Truy vết Thao tác trên Vé `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi có tranh chấp với khách hàng về việc "ai đã hứa gì, khi nào" hoặc khi cần rà soát nội bộ vì một vé bị xử lý sai, doanh nghiệp cần biết chính xác ai đã thay đổi gì trên vé và vào lúc nào. Nội dung trao đổi đã bất biến (BR-13.1), nhưng các trường quan trọng như mức ưu tiên, người phụ trách, trạng thái, hạn chót cam kết thì vẫn sửa được và cần được ghi vết.

**Actor:** Support Manager, Administrator (xem nhật ký); Tiến trình Hệ thống (ghi nhật ký).

**Hiện trạng triển khai:** Đã triển khai phân hệ nhật ký kiểm toán dùng chung, bất biến, áp dụng cho vé theo NFR-14.

**Quy tắc nghiệp vụ:**
- `BR-44.1 (Các thay đổi phải ghi vết)`: Tối thiểu ghi lại thay đổi của: trạng thái, mức ưu tiên, người phụ trách, nhóm phụ trách, danh mục phân loại, chính sách cam kết áp dụng, hạn chót cam kết, và các thao tác gộp/tách/gán vé cha - con.
- `BR-44.2 (Nội dung mỗi bản ghi)`: Mỗi bản ghi nêu rõ thời điểm, người thực hiện, giá trị trước và giá trị sau.
- `BR-44.3 (Nhật ký không sửa được)`: Nhật ký thay đổi là bất biến, kể cả Quản trị viên cũng không xóa hay sửa được — nếu sửa được thì nhật ký mất giá trị làm bằng chứng đối soát.
- `BR-44.4 (Ghi vết thao tác xuất dữ liệu)`: Mỗi lần xuất dữ liệu vé ra tệp đều được ghi lại (ai xuất, lúc nào, phạm vi dữ liệu nào), phục vụ kiểm soát rủi ro rò rỉ thông tin khách hàng.

**Quyền đọc nhật ký:** `NFR-14` (phân hệ Contacts, mục 4.3) là **sàn bắt buộc** và thắng mọi mô tả quyền khác trong tài liệu này. Theo đó chỉ Chủ sở hữu Workspace và Người phụ trách Bảo vệ Dữ liệu đọc được toàn bộ nhật ký; Quản trị viên chỉ đọc nhật ký của đúng bản ghi đang có yêu cầu chủ thể dữ liệu hoặc thao tác gộp/khôi phục đang mở; Trưởng phòng, Trưởng nhóm và Tư vấn viên **không** được tra cứu nhật ký toàn hệ thống. Họ vẫn xem được nhật ký thay đổi của từng vé mà mình có quyền trên màn hình chi tiết vé — đủ cho Kịch bản 21, vốn là đối soát một vé cụ thể với khách hàng, không phải truy vấn toàn kho. Ghi chú này tồn tại vì bảng phân quyền trước đây cấp "Toàn quyền" cho Trưởng phòng, mâu thuẫn trực tiếp với `NFR-14`.

**Tiêu chí chấp nhận:** Xem Kịch bản 21 (mục 6).

---

## L. VÒNG ĐỜI DỮ LIỆU & TUÂN THỦ BẢO VỆ DỮ LIỆU CÁ NHÂN

### FEAT-45 — Lưu trữ, Ẩn danh hóa & Xử lý Yêu cầu Xóa Dữ liệu Cá nhân `[Đã triển khai]`

**Mô tả nghiệp vụ:** Vé hỗ trợ chứa nhiều dữ liệu cá nhân của khách hàng (họ tên, số điện thoại, địa chỉ email, nội dung trao đổi, tệp đính kèm). Theo Nghị định 13/2023/NĐ-CP về bảo vệ dữ liệu cá nhân, doanh nghiệp phải có khả năng đáp ứng yêu cầu xóa dữ liệu của chủ thể dữ liệu và không được lưu trữ dữ liệu cá nhân lâu hơn mức cần thiết. Yêu cầu này mâu thuẫn bề mặt với nguyên tắc bất biến của lịch sử vé, nên cần một quy tắc xử lý rõ ràng.

**Actor:** Support Manager, Administrator (tiếp nhận và thực thi yêu cầu); Tiến trình Hệ thống (thực thi chính sách lưu trữ tự động).

**Hiện trạng triển khai:** Đã triển khai quy trình tiếp nhận yêu cầu xóa dữ liệu cá nhân, ẩn danh hóa dữ liệu có xem trước, lưu trữ và xóa định kỳ theo Nghị định 13.

**Quy tắc nghiệp vụ:**
- `BR-45.1 (Thời hạn lưu trữ vé đã đóng)`: Doanh nghiệp cấu hình thời hạn lưu trữ vé đã đóng (mặc định 36 tháng). Hết thời hạn, vé được chuyển sang trạng thái lưu trữ dài hạn: không còn hiển thị trong danh sách làm việc hàng ngày nhưng vẫn tra cứu được khi cần đối soát.
- `BR-45.2 (Ẩn danh hóa thay vì xóa)`: Khi khách hàng yêu cầu xóa dữ liệu cá nhân, hệ thống thực hiện **ẩn danh hóa**: thay thế các thông tin định danh cá nhân (họ tên, số điện thoại, email, nội dung trao đổi có chứa thông tin cá nhân, tệp đính kèm) bằng dấu hiệu đã ẩn danh, đồng thời **giữ lại các dữ liệu thống kê phi định danh** (thời gian xử lý, danh mục, kết quả tuân thủ cam kết). Cách này vừa đáp ứng quyền của chủ thể dữ liệu, vừa không phá vỡ tính toàn vẹn của báo cáo lịch sử và không làm sai lệch các chỉ số đã công bố. Với **toàn bộ nội dung trao đổi** của các vé thuộc phạm vi, hệ thống thay thế trọn vẹn phần nội dung do người viết nhập — không cố gắng dò tìm và chỉ xóa một phần câu chữ, vì việc nhận diện tự động thông tin cá nhân lẫn trong văn xuôi không bao giờ chắc chắn và một lần sót là một lần không đáp ứng. Siêu dữ liệu của từng tin nhắn (ai gửi là nhân viên nào, thời điểm, loại nội dung) được giữ lại để dòng thời gian xử lý vẫn đọc được.
- `BR-45.3 (Ghi vết việc ẩn danh hóa)`: Mỗi lần ẩn danh hóa được ghi vào nhật ký (ai yêu cầu, ai thực hiện, thời điểm, phạm vi vé bị ảnh hưởng) làm bằng chứng tuân thủ khi cơ quan quản lý kiểm tra.
- `BR-45.4 (Thời hạn đáp ứng)`: Yêu cầu xóa dữ liệu cá nhân phải được xử lý trong vòng **72 giờ** kể từ khi tiếp nhận, theo thời hạn quy định tại Nghị định 13/2023/NĐ-CP. Hệ thống theo dõi hạn đáp ứng của từng yêu cầu và cảnh báo người phụ trách khi còn 24 giờ, sau đó cảnh báo lại khi còn 6 giờ.
- `BR-45.5 (Bảo vệ dữ liệu trong tệp xuất)`: Tệp dữ liệu xuất ra chứa dữ liệu cá nhân phải được ghi vết người xuất (BR-44.4), và doanh nghiệp cấu hình được việc che bớt các trường nhạy cảm đối với những vai trò không cần xem đầy đủ.
- `BR-45.6 (Phạm vi ẩn danh hóa)`: Việc ẩn danh hóa phải bao trùm **mọi nơi dữ liệu cá nhân của khách hàng đó còn tồn tại**: vé đang hoạt động, vé đã đóng, vé nằm trong Thùng rác chưa bị xóa vĩnh viễn (FEAT-26), vé đã chuyển sang lưu trữ dài hạn (BR-45.1), và tệp đính kèm của các vé đó (BR-15.4). Nếu bỏ sót Thùng rác hoặc kho lưu trữ, dữ liệu cá nhân vẫn tồn tại tới 30 ngày (hoặc 36 tháng) sau khi doanh nghiệp đã xác nhận với chủ thể dữ liệu là đã xóa — tức là không đáp ứng yêu cầu dù trên hồ sơ ghi là đã xử lý xong. Hệ quả kèm theo: một vé đã ẩn danh hóa nếu được phục hồi từ Thùng rác sẽ trở lại **ở dạng đã ẩn danh**, đây là ngoại lệ có chủ đích của BR-26.2 về phục hồi nguyên trạng.
- `BR-45.7 (Phân biệt dữ liệu cá nhân và dữ liệu pháp nhân)`: Ẩn danh hóa chỉ áp dụng cho dữ liệu cá nhân của người liên hệ (họ tên, số điện thoại, email, nội dung trao đổi mang thông tin định danh). Liên kết giữa vé và **doanh nghiệp khách hàng** được giữ nguyên, vì Nghị định 13 điều chỉnh dữ liệu cá nhân chứ không điều chỉnh dữ liệu pháp nhân. Nhờ vậy báo cáo theo hợp đồng của khách hàng doanh nghiệp (BR-43.1) vẫn đủ số liệu sau khi một người liên hệ yêu cầu xóa dữ liệu của mình.
- `BR-45.8 (Nhật ký truy vết sau khi ẩn danh hóa)`: Nhật ký thay đổi (FEAT-44) lưu giá trị trước và sau của mỗi lần sửa, nên có thể chứa dữ liệu cá nhân đã được ẩn danh ở vé. Với các bản ghi nhật ký thuộc phạm vi ẩn danh hóa, hệ thống thay thế giá trị cá nhân trong nhật ký bằng dấu hiệu đã ẩn danh nhưng **giữ nguyên dòng nhật ký** (ai làm, lúc nào, thao tác gì). Đây là ngoại lệ có chủ đích và duy nhất của NFR-12, và bản thân việc thay thế đó cũng được ghi thành một dòng nhật ký mới theo BR-45.3 — nghĩa là lịch sử thao tác không mất, chỉ nội dung cá nhân được gỡ bỏ.

**Tiêu chí chấp nhận:** Xem Kịch bản 22 và Kịch bản 44 (mục 6).

---

## 4. Yêu cầu phi chức năng

### 4.1 Độ tin cậy & Toàn vẹn Dữ liệu
- **NFR-01 (Bảo toàn Nhật ký Trao đổi):** Lịch sử tin nhắn và ghi chú trên vé là bất biến, không thể bị chỉnh sửa hoặc xóa bỏ. Ngoại lệ duy nhất là ẩn danh hóa theo yêu cầu bảo vệ dữ liệu cá nhân (FEAT-45), và việc ẩn danh hóa đó phải được ghi vết. *Cách nghiệm thu:* không tồn tại thao tác sửa/xóa nội dung trao đổi trên giao diện lẫn qua lời gọi trực tiếp tới hệ thống; kiểm thử phải thử cả hai đường, vì chặn ở giao diện mà để hở đường gọi trực tiếp thì quy tắc này không có hiệu lực thực tế.
- **NFR-02 (An toàn Giao dịch Gộp Vé):** Thao tác Gộp Vé thực thi trọn vẹn hoặc không thực thi gì cả — không để lại trạng thái gộp dở dang nếu có lỗi giữa chừng.
- **NFR-03 (Chống trùng lặp mã số vé):** Mã số vé không bị cấp trùng ngay cả khi nhiều thao tác tạo hoặc nhập vé xảy ra đồng thời.
- **NFR-04 (Không mất cam kết khi hệ thống gián đoạn):** Sau khi hệ thống khởi động lại hoặc gián đoạn tạm thời, các đồng hồ cam kết, hàng đợi cảnh báo và tác vụ leo thang phải được khôi phục đúng trạng thái, không bỏ sót vé nào đến hạn trong thời gian gián đoạn.

### 4.2 Hiệu năng & Khả năng đáp ứng
- **NFR-05 (Quy mô sử dụng đồng thời):** Hệ thống đáp ứng tối thiểu 100 tư vấn viên thao tác đồng thời trên cùng một không gian làm việc mà không suy giảm trải nghiệm.
- **NFR-06 (Tốc độ tải danh sách và bảng điều khiển):** Danh sách vé và bảng điều khiển hàng đợi tải xong trong vòng 3 giây với khối lượng dữ liệu tương đương 100.000 vé.
- **NFR-07 (Độ trễ phát hiện vi phạm cam kết):** Cảnh báo sắp tới hạn và đánh dấu vi phạm cam kết phát sinh chậm nhất 1 phút so với thời điểm thực tế đến ngưỡng — độ trễ lớn hơn sẽ khiến cảnh báo mất tác dụng phòng ngừa.
- **NFR-08 (Thời gian lan truyền cập nhật sự cố diện rộng):** Cập nhật từ vé cha lan truyền xuống toàn bộ vé con (BR-28.2) hoàn tất trong vòng 5 phút với quy mô 500 vé con.

### 4.3 An toàn & Bảo mật
- **NFR-09 (Bảo mật Tuyệt đối Ghi chú Nội bộ):** Ghi chú nội bộ được lọc bỏ triệt để ở tầng hệ thống, không bao giờ để lộ sang giao diện dành cho khách hàng.
- **NFR-10 (Đường dẫn tải báo cáo có thời hạn):** Đường dẫn tải báo cáo xuất dữ liệu chỉ có hiệu lực trong thời gian giới hạn, hết hạn sẽ không truy cập được nữa.
- **NFR-11 (Cách ly dữ liệu giữa các doanh nghiệp):** Dữ liệu vé của mỗi doanh nghiệp sử dụng hệ thống được cách ly hoàn toàn; không tồn tại thao tác nào cho phép người dùng của doanh nghiệp này tiếp cận dữ liệu của doanh nghiệp khác.
- **NFR-12 (Nhật ký thao tác bất biến):** Nhật ký thay đổi và nhật ký xuất dữ liệu không sửa hoặc xóa được bởi bất kỳ vai trò nào, kể cả Quản trị viên. Ngoại lệ duy nhất là việc gỡ bỏ nội dung cá nhân khỏi nhật ký khi ẩn danh hóa theo BR-45.8 — ngoại lệ này do tiến trình hệ thống thực hiện, không mở ra thao tác sửa/xóa nhật ký cho bất kỳ vai trò người dùng nào, và dòng nhật ký vẫn được giữ lại.

---

## 5. Ma trận quyền truy cập tính năng

Bảng dưới quy định phân quyền mục tiêu cho toàn bộ tính năng trong tài liệu, bao gồm cả các tính năng chưa xây dựng — đây là yêu cầu đội phát triển phải đáp ứng khi hiện thực, không phải mô tả hiện trạng.

**Cách đọc bảng này khi kiểm thử:** Các giá trị trong bảng là **cấu hình mặc định khi khởi tạo một doanh nghiệp mới**, và đây chính là trạng thái dùng làm chuẩn nghiệm thu. Doanh nghiệp điều chỉnh lại được sau đó theo nhu cầu riêng, trừ các giới hạn ghi rõ là không hạ thấp được (ví dụ BR-27.7 về quyền hoàn tác gộp vé). Yêu cầu bắt buộc với đội phát triển gồm hai phần: (a) doanh nghiệp mới phải khởi tạo đúng bộ mặc định này; (b) mỗi ô trong bảng phải cấu hình được độc lập — không được gộp nhiều quyền vào một công tắc khiến doanh nghiệp buộc phải cấp thừa quyền.

Cột "Hệ thống" không phải một actor người dùng — đây là ký hiệu cho hành vi tự động của Tiến trình Hệ thống, đặt cạnh các actor con người để dễ đối chiếu ai được thông báo hoặc nhận kết quả từ hành vi tự động đó.

| Mã FEAT | Tên tính năng nghiệp vụ | Khách hàng | Support Agent | Team Lead | Support Manager | Sales Rep | Administrator | Hệ thống |
| --- | --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `FEAT-01` | Tạo & Quản lý Ticket | — | Scope gán | Scope PB | **Toàn quyền** | Xem liên quan | **Toàn quyền** | — |
| `FEAT-02` | Cấp Mã Ticket Number | — | — | — | — | — | — | ✔ |
| `FEAT-03` | Liên kết Contact/Account | — | Scope gán | Scope PB | **Toàn quyền** | Xem liên quan | **Toàn quyền** | — |
| `FEAT-04` | Phân loại Category Path | — | Scope gán | Scope PB | **Toàn quyền** | — | Cấu hình | — |
| `FEAT-05` | Trường Tùy biến | — | Scope gán | Scope PB | **Toàn quyền** | — | Cấu hình | — |
| `FEAT-07` | Đo lường SLA | — | — | — | Cấu hình | — | Cấu hình | ✔ |
| `FEAT-08` | Cấu hình Giờ Làm việc | — | — | — | Cấu hình | — | Cấu hình | — |
| `FEAT-09` | Tạm dừng Đồng hồ SLA | — | Scope gán | Scope PB | **Toàn quyền** | — | **Toàn quyền** | ✔ (tự động theo trạng thái) |
| `FEAT-10` | Phân bổ Round-Robin | — | — | Cấu hình | Cấu hình | — | Cấu hình | ✔ |
| `FEAT-11` | Định tuyến Skill-Based | — | — | Cấu hình | Cấu hình | — | Cấu hình | ✔ |
| `FEAT-12` | Bàn giao & Phân công Lại | — | Chuyển cấp | **Toàn quyền** | **Toàn quyền** | — | **Toàn quyền** | — |
| `FEAT-13` | Trao đổi Thread | — | Scope gán | Scope PB | **Toàn quyền** | Xem liên quan | **Toàn quyền** | — |
| `FEAT-14` | Ghi chú Nội bộ | — | Scope gán | Scope PB | **Toàn quyền** | — | **Toàn quyền** | — |
| `FEAT-15` | Đính kèm File | — | Scope gán | Scope PB | **Toàn quyền** | Xem | **Toàn quyền** | — |
| `FEAT-17` | Cảnh báo Sớm SLA | — | Nhận cảnh báo | Nhận cảnh báo | Cấu hình ngưỡng | — | **Toàn quyền** | ✔ |
| `FEAT-18` | Leo thang Vi phạm | — | — | Nhận cảnh báo | Cấu hình mốc leo thang | — | **Toàn quyền** | ✔ |
| `FEAT-19` | Giải quyết & Resolution Code | — | Scope gán (quyền resolve) | Scope PB (quyền resolve) | **Toàn quyền** | — | **Toàn quyền** | — |
| `FEAT-20` | Khảo sát CSAT | Chấm điểm | — | — | Cấu hình | — | Cấu hình | ✔ |
| `FEAT-21` | Mở lại Vé | — | Scope gán (quyền resolve) | Scope PB (quyền resolve) | **Toàn quyền** | — | **Toàn quyền** | — |
| `FEAT-23` | Gắn Nhãn Hàng loạt | — | — | **Toàn quyền** | **Toàn quyền** | — | **Toàn quyền** | — |
| `FEAT-24` | Nhập Dữ liệu | — | — | — | Scope PB / **Toàn quyền** | — | **Toàn quyền** | — |
| `FEAT-25` | Xuất Báo cáo | — | — | Scope PB | **Toàn quyền** | — | **Toàn quyền** | — |
| `FEAT-26` | Thùng rác & Phục hồi | — | — | — | **Toàn quyền** (phê duyệt) | — | **Toàn quyền** | — |
| `FEAT-27` | Gộp Vé | Nhận thông báo | — | Theo cấu hình (mặc định: không) | **Toàn quyền** (mặc định; giữ quyền hoàn tác) | — | **Toàn quyền** | Gửi thông báo tự động |
| `FEAT-28` | Sự cố Diện rộng (Vé Cha-Con) | Nhận cập nhật | Scope gán | **Toàn quyền** | **Toàn quyền** | — | **Toàn quyền** | Lan truyền cập nhật/trạng thái xuống vé con |
| `FEAT-29` | Liên kết Deal | — | Scope gán | Scope PB | **Toàn quyền** | Liên kết | **Toàn quyền** | — |
| `FEAT-06` | Ma trận Tác động & Nghiêm trọng | — | Đánh giá | Ghi đè có lý do | Cấu hình ma trận | — | Cấu hình | ✔ (suy ra mức ưu tiên) |
| `FEAT-16` | Câu trả lời Mẫu | — | Sử dụng + mẫu cá nhân | Quản lý mẫu nhóm | **Toàn quyền** | — | Cấu hình | — |
| `FEAT-22` | Tự động Đóng Vé | Nhận nhắc trước khi đóng | — | — | Cấu hình thời hạn | — | Cấu hình | ✔ |
| `FEAT-30` | Cảnh báo Rủi ro sang Deal | — | — | — | Cấu hình điều kiện | Nhận cảnh báo | Cấu hình | ✔ |
| `FEAT-31` | Tạo Bài viết Tri thức | — | Đề xuất bản nháp | Duyệt & xuất bản | **Toàn quyền** | — | **Toàn quyền** | — |
| `FEAT-32` | Giám sát & Hướng dẫn | — | Được hướng dẫn | Giám sát | Giám sát | — | **Toàn quyền** | — |
| `FEAT-33` | Theo dõi Giờ Hỗ trợ | — | Ghi nhận giờ | Duyệt giờ | Duyệt giờ | — | **Toàn quyền** | — |
| `FEAT-34` | Hàng đợi Chung | — | Xem & tự nhận việc | **Toàn quyền** | **Toàn quyền** | — | **Toàn quyền** | ✔ (cảnh báo tồn đọng) |
| `FEAT-35` | Ca trực & Trạng thái Sẵn sàng | — | Tự đặt trạng thái | Xếp lịch trực, duyệt nghỉ phép | **Toàn quyền** | — | **Toàn quyền** | ✔ (loại người không trực khỏi phân bổ) |
| `FEAT-36` | Chuyển Vé Hàng loạt | — | — | **Toàn quyền** | **Toàn quyền** | — | **Toàn quyền** | — |
| `FEAT-37` | Thông báo cho Tư vấn viên | — | Nhận & tự cấu hình | Nhận & tự cấu hình | Nhận & tự cấu hình | — | Cấu hình mặc định | ✔ |
| `FEAT-38` | Cảnh báo Trùng Thao tác | — | Nhận cảnh báo | Nhận cảnh báo | Nhận cảnh báo | — | **Toàn quyền** | ✔ |
| `FEAT-39` | Leo thang Thủ công | — | Leo thang vé mình giữ; **không xem được vé khiếu nại về chính mình** (BR-39.3) | Tiếp nhận & xử lý | Tiếp nhận & xử lý | — | **Toàn quyền** | — |
| `FEAT-40` | Cam kết theo Hạng Khách hàng | — | Xem cam kết áp dụng | Xem cam kết áp dụng | Cấu hình chính sách | — | Cấu hình | ✔ (chọn chính sách khi tạo vé) |
| `FEAT-41` | Tách Vé | Nhận thông báo vé mới | Scope gán | Scope PB | **Toàn quyền** | — | **Toàn quyền** | — |
| `FEAT-42` | Bảng điều khiển Hàng đợi | — | — | Scope PB | **Toàn quyền** | — | **Toàn quyền** | — |
| `FEAT-43` | Báo cáo Hiệu suất & Tuân thủ | — | Xem chỉ số của chính mình | Scope PB | **Toàn quyền** | — | **Toàn quyền** | ✔ (gửi báo cáo định kỳ) |
| `FEAT-44` | Nhật ký Thay đổi | — | — | Nhật ký của từng vé đang xử lý | Nhật ký của từng vé đang xử lý | — | Toàn bộ nhật ký (Chủ sở hữu/DPO); theo từng bản ghi (Quản trị viên) | ✔ (ghi nhật ký) |
| `FEAT-45` | Vòng đời Dữ liệu & Ẩn danh hóa | — | — | — | **Toàn quyền** | — | **Toàn quyền** | ✔ (thực thi chính sách lưu trữ) |

*Chú giải "quyền resolve":* Là quyền được phép chuyển vé sang trạng thái kết thúc dạng "đã xử lý xong" hoặc mở lại một vé đã kết thúc (FEAT-19, FEAT-21) — tách biệt với quyền "sửa" (edit) thông thường, vì đây là thao tác thay đổi tình trạng cam kết SLA của vé chứ không chỉ chỉnh sửa nội dung.

*Ghi chú phạm vi:* "Scope gán" = chỉ áp dụng cho bản ghi được gán cho chính actor đó; "Scope PB" = áp dụng cho toàn bộ bản ghi trong phòng ban mà actor đó phụ trách — nếu doanh nghiệp có bật ca trực (FEAT-35) thì phạm vi này là giao của phòng ban và ca trực đang hoạt động; "Toàn quyền" = áp dụng cho toàn bộ không gian làm việc.

*Thao tác khó hoàn tác:* **Gộp vé (FEAT-27)** mặc định đặt ở mức Support Manager và doanh nghiệp hạ xuống Team Lead được nếu mô hình vận hành cần, trừ quyền hoàn tác — theo BR-27.7. **Xóa vĩnh viễn, phục hồi từ Thùng rác (FEAT-26) và xuất dữ liệu ra ngoài (FEAT-25)** thì không hạ xuống Team Lead trong bất kỳ cấu hình nào: hai nhóm này hoặc phá hủy dữ liệu vĩnh viễn, hoặc đưa dữ liệu khách hàng ra khỏi hệ thống, nên phải giữ ở cấp chịu trách nhiệm về dữ liệu. Riêng FEAT-25, Trưởng nhóm xuất được danh sách vé trong phạm vi nhóm mình phụ trách (xem BR-25.5), khác với việc xuất toàn bộ dữ liệu không gian làm việc.

---

## 6. Kịch bản chấp nhận (UAT)

### Kịch bản 1: Tạo Vé, Cấp Mã Số & Đo lường Cam kết
1. Tư vấn viên tạo vé thủ công: "Lỗi không in được hóa đơn GTGT", mức ưu tiên cao.
2. **Kỳ vọng (BR-02.1, BR-07.1, BR-07.2):** Hệ thống cấp mã số vé mới duy nhất (ví dụ `TKT-01042`); cả hai đồng hồ cam kết (phản hồi đầu tiên và xử lý dứt điểm) bắt đầu đếm từ thời điểm tạo vé, theo chính sách của mức ưu tiên cao.
3. Tư vấn viên viết một ghi chú nội bộ trước, chưa gửi gì cho khách hàng.
4. **Kỳ vọng (BR-07.3, BR-14.3):** Mốc cam kết phản hồi đầu tiên vẫn chưa được tính là hoàn tất.
5. Tư vấn viên gửi phản hồi công khai đầu tiên trước khi hết hạn.
6. **Kỳ vọng (BR-07.3, BR-07.6):** Mốc phản hồi đầu tiên hoàn tất đúng hạn; đồng hồ xử lý dứt điểm vẫn tiếp tục chạy với thời gian còn lại hiển thị rõ trên màn hình vé.

---

### Kịch bản 2: Tạm dừng Đồng hồ Cam kết khi Chờ Khách hàng
1. Tư vấn viên gửi hướng dẫn kiểm tra và chuyển vé sang trạng thái "Chờ khách hàng phản hồi" (trạng thái này đã được doanh nghiệp đánh dấu tạm dừng cam kết).
2. Khách hàng 2 ngày sau mới trả lời lại.
3. **Kỳ vọng (BR-09.1, BR-09.3):** Trong suốt 2 ngày đó, đồng hồ xử lý dứt điểm bị đóng băng; khi khách phản hồi, vé chuyển về trạng thái đang xử lý và đồng hồ chạy tiếp từ số phút còn lại; tổng thời gian tạm dừng được ghi vào lịch sử vé.
4. Với một vé khác chưa từng có phản hồi công khai nào, tư vấn viên chuyển ngay sang trạng thái tạm dừng cam kết.
5. **Kỳ vọng (BR-09.2):** Đồng hồ cam kết phản hồi đầu tiên **vẫn tiếp tục chạy**, không bị đóng băng.
6. Một vé bị tạm dừng tới lần thứ tư.
7. **Kỳ vọng (BR-09.4):** Trưởng nhóm nhận thông báo để rà soát khả năng lạm dụng trạng thái tạm dừng.

---

### Kịch bản 3: Cảnh báo Sớm & Leo thang Vi phạm
1. Một vé có cam kết xử lý dứt điểm trong 2 giờ làm việc, ngưỡng cảnh báo sớm đặt ở 75% thời hạn.
2. Sau 1 giờ 30 phút làm việc (75% thời hạn) mà vé vẫn chưa xử lý xong.
3. **Kỳ vọng (BR-17.1, BR-17.4):** Tư vấn viên đang phụ trách nhận cảnh báo sớm; vé được đánh dấu nổi bật trong danh sách làm việc và trên bảng điều khiển hàng đợi.
4. Vé vượt quá thời hạn 2 giờ mà vẫn chưa xử lý xong.
5. **Kỳ vọng (BR-18.1, BR-18.3):** Vé được đánh dấu vi phạm cam kết; hành động leo thang đã cấu hình (thông báo tới quản lý trực tiếp) được thực thi đúng một lần; lần leo thang được ghi vào lịch sử vé.
6. Một vé khác **đã được tư vấn viên gửi phản hồi công khai đầu tiên**, sau đó chuyển sang trạng thái chờ khách hàng phản hồi; vé này đi qua mốc 75% của cam kết xử lý dứt điểm.
7. **Kỳ vọng (BR-17.3):** Không phát cảnh báo cho mốc xử lý dứt điểm, vì đồng hồ của mốc đó đang tạm dừng và trách nhiệm không thuộc về tư vấn viên. Mốc phản hồi đầu tiên đã hoàn tất nên cũng không còn cảnh báo.
8. Một vé thứ ba **chưa từng có phản hồi công khai nào** bị chuyển sang trạng thái chờ khách hàng phản hồi ngay khi vừa tiếp nhận, rồi đi qua mốc 75% của cam kết phản hồi đầu tiên.
9. **Kỳ vọng (BR-17.3, BR-09.2):** Hệ thống **vẫn phát cảnh báo** cho mốc phản hồi đầu tiên, vì đồng hồ của mốc này không bao giờ được tạm dừng trước khi có phản hồi thật. Đây là tình huống chống việc dừng đồng hồ để né chỉ số mà chưa thực sự trả lời khách hàng — cảnh báo phải đến trước khi vi phạm, nếu không quy tắc BR-09.2 chỉ phát hiện được sau khi đã muộn.

---

### Kịch bản 4: Giải quyết Vé & Khảo sát Hài lòng
1. Tư vấn viên xử lý xong sự cố, nhập nguyên nhân xử lý và tóm tắt giải pháp, chuyển vé sang trạng thái "đã xử lý xong".
2. **Kỳ vọng (BR-19.1, BR-19.4):** Hệ thống chỉ cho chuyển trạng thái khi đã có đủ nguyên nhân và tóm tắt; thời điểm kết thúc và người thực hiện được ghi nhận.
3. **Kỳ vọng (BR-20.2):** Hệ thống tự động gửi khảo sát tới khách hàng mà không cần tư vấn viên thao tác.
4. Khách hàng chấm 5 sao và để lại nhận xét "Hỗ trợ rất nhanh và nhiệt tình".
5. **Kỳ vọng:** Điểm số được ghi nhận vào vé và vào hồ sơ hiệu suất của tư vấn viên đã xử lý.
6. Vé sau đó bị mở lại trong lúc đường dẫn khảo sát vẫn còn trong thời hạn 7 ngày.
7. **Kỳ vọng (BR-20.6):** Đường dẫn khảo sát bị vô hiệu hóa ngay; điểm 5 sao đã chấm ở bước 4 được gỡ khỏi cách tính KPI-03, vì lần phục vụ đó hóa ra chưa giải quyết dứt điểm vấn đề.
8. Vé được xử lý xong lần thứ hai.
9. **Kỳ vọng (BR-20.6, BR-20.3):** Hệ thống gửi **một** khảo sát mới cho lần kết thúc này; tại mọi thời điểm chỉ có tối đa một đường dẫn khảo sát còn hiệu lực, và vé vẫn chỉ đóng góp tối đa một điểm đánh giá vào KPI-03.

---

### Kịch bản 5: Gộp Vé Trùng lặp (Ticket Merging)
1. Một khách hàng gửi 3 yêu cầu liên tiếp về cùng một lỗi thanh toán, tạo thành 3 vé riêng biệt.
2. Người có quyền gộp vé theo cấu hình của doanh nghiệp (mặc định là Trưởng phòng — xem BR-27.7) chọn vé đầu tiên làm vé chính, gộp 2 vé còn lại vào. Trước đó, kiểm tra một Trưởng nhóm chưa được cấp quyền này thao tác gộp: hệ thống phải từ chối.
3. **Kỳ vọng:** Toàn bộ nội dung của 2 vé phụ được nối tiếp vào dòng thời gian của vé chính; 2 vé phụ bị khóa lại và không thể chỉnh sửa thêm; khách hàng của 2 vé phụ nhận được thông báo yêu cầu của họ đã chuyển sang vé chính kèm mã số để tiếp tục theo dõi (BR-27.3).

---

### Kịch bản 6: Quản lý Sự cố Diện rộng (Major Incident)
1. Sự cố sập hệ thống thanh toán khiến 150 khách hàng cùng tạo vé báo lỗi trong vòng 10 phút.
2. Team Lead tạo một Vé Sự cố làm vé cha, gán 150 vé của khách hàng làm vé con.
3. Team Lead đăng một cập nhật công khai lên vé cha: "Đội kỹ thuật đã xác định nguyên nhân, dự kiến khắc phục trong 30 phút."
4. **Kỳ vọng (BR-28.2):** Cập nhật này tự động xuất hiện trên dòng trao đổi của toàn bộ 150 vé con, khách hàng của từng vé đều nhận được mà tư vấn viên không phải trả lời thủ công từng vé.
5. Sự cố được khắc phục, Team Lead chuyển vé cha sang trạng thái "đã xử lý xong".
6. **Kỳ vọng (BR-28.3):** Toàn bộ 150 vé con tự động chuyển sang "đã xử lý xong", mỗi khách hàng nhận khảo sát CSAT riêng của vé mình.

---

### Kịch bản 7: Liên kết Vé Hỗ trợ với Cơ hội Bán hàng
1. Khách hàng "Công ty Đại Phát" đang có một cơ hội bán hàng ở giai đoạn "Đàm phán" và đồng thời tạo một vé hỗ trợ vì gặp lỗi nghiêm trọng.
2. Nhân viên kinh doanh liên kết thủ công vé hỗ trợ này với cơ hội bán hàng đang phụ trách.
3. **Kỳ vọng (BR-29.1):** Vé hỗ trợ tra cứu được từ màn hình cơ hội bán hàng và ngược lại.
4. Một nhân viên kinh doanh khác không có quyền xem vé hỗ trợ mở cơ hội bán hàng này.
5. **Kỳ vọng (BR-29.2):** Người đó thấy có liên kết tồn tại nhưng không xem được nội dung trao đổi bên trong vé.

---

### Kịch bản 8: Mở lại Vé sau khi Đã Xử lý Xong (trong hạn cho phép)
1. Vé đã ở trạng thái "đã xử lý xong" được 3 ngày, trong hạn mở lại cho phép (ví dụ 7 ngày, BR-21.3).
2. Khách hàng phản hồi lại: "Lỗi này lại vừa tái diễn sáng nay".
3. Người có quyền resolve xác nhận thao tác mở lại.
4. **Kỳ vọng:** Vé chuyển về trạng thái đang xử lý, số lần mở lại của vé tăng thêm 1, thời điểm mở lại gần nhất được ghi nhận.

---

### Kịch bản 8b: Khách hàng Phản hồi Lại sau khi Quá Hạn Mở lại
1. Vé đã ở trạng thái "đã xử lý xong" được 20 ngày, đã quá hạn mở lại cho phép (7 ngày, BR-21.3).
2. Khách hàng phản hồi lại về cùng sự cố.
3. **Kỳ vọng:** Hệ thống không mở lại vé cũ; thay vào đó tạo một vé mới, tự động liên kết tham chiếu tới vé cũ để tư vấn viên tra cứu lịch sử xử lý trước đó.

---

### Kịch bản 9: Bàn giao Vé & Từ chối Nhận
1. Tư vấn viên A đang giữ một vé đã phản hồi khách hàng lần đầu, còn 4 giờ tới hạn xử lý dứt điểm.
2. A bàn giao vé cho tư vấn viên B kèm ghi chú "Cần chuyên môn về tích hợp thanh toán".
3. **Kỳ vọng (BR-12.1, BR-12.2, BR-12.3):** Ghi chú bàn giao xuất hiện dưới dạng ghi chú nội bộ; hạn chót xử lý dứt điểm vẫn là 4 giờ tới (không tính lại từ đầu); mốc phản hồi đầu tiên vẫn giữ trạng thái đã hoàn tất; B nhận được thông báo kèm lý do.
4. B từ chối vé trong vòng 30 phút với lý do "Đang xử lý sự cố khẩn cấp khác".
5. **Kỳ vọng (BR-12.4):** Vé quay về hàng đợi chung và Trưởng nhóm nhận được thông báo để điều phối lại.

---

### Kịch bản 10: Vé Tồn đọng trong Hàng đợi Chung
1. Một vé mới vào hàng đợi chung của nhóm Hỗ trợ Kỹ thuật lúc 09:00, chưa ai nhận.
2. Đến 09:15 (ngưỡng cảnh báo mặc định) vẫn chưa có ai nhận vé.
3. **Kỳ vọng (BR-34.3):** Trưởng nhóm nhận cảnh báo vé tồn đọng, dù vé chưa vi phạm cam kết phản hồi.
4. Tư vấn viên C nhận vé từ hàng đợi.
5. **Kỳ vọng (BR-34.2, BR-34.4):** Vé biến mất khỏi hàng đợi của những người khác; thời gian 15 phút nằm chờ vẫn được tính vào đồng hồ cam kết phản hồi đầu tiên.

---

### Kịch bản 11: Phân bổ Tự động Bỏ qua Người Không Trực
1. Nhóm có 3 tư vấn viên: A đang trực và sẵn sàng, B đang nghỉ phép đã duyệt, C đã hết ca trực.
2. Một vé mới cần phân bổ tự động.
3. **Kỳ vọng (BR-35.1):** Vé được gán cho A; B và C bị bỏ qua.
4. A chuyển sang trạng thái tạm rời chỗ, một vé mới nữa phát sinh.
5. **Kỳ vọng (BR-35.2):** Không còn ai sẵn sàng, vé được đưa vào hàng đợi chung và Trưởng nhóm được thông báo ngay.

---

### Kịch bản 12: Nhân viên Nghỉ Ốm Đột xuất
1. Tư vấn viên A báo nghỉ ốm, đang giữ 10 vé chưa xử lý xong.
2. Trưởng nhóm mở danh sách vé của A và chọn chuyển toàn bộ cho hai người còn chỗ trống là B và C, kèm ghi chú chung "Chuyển do anh A nghỉ ốm đột xuất".
3. **Kỳ vọng (BR-36.1, BR-36.2, BR-36.3, BR-36.5):** Toàn bộ 10 vé được chuyển trong một thao tác; hệ thống đề xuất chia theo hạn mức còn trống của B và C; ghi chú bàn giao xuất hiện trên từng vé; hạn chót cam kết của cả 10 vé giữ nguyên không bị tính lại.

---

### Kịch bản 13: Cam kết Dịch vụ theo Hạng Khách hàng
1. Khách hàng X thuộc hạng cao cấp (cam kết phản hồi 1 giờ), khách hàng Y thuộc hạng phổ thông (cam kết phản hồi 8 giờ).
2. Cả hai cùng gửi một yêu cầu ở mức ưu tiên trung bình.
3. **Kỳ vọng (BR-40.1, BR-40.3):** Vé của X có hạn chót phản hồi sau 1 giờ, vé của Y sau 8 giờ; màn hình xử lý vé hiển thị rõ hạng khách hàng và thời hạn cam kết đang áp dụng cho từng vé.
4. Trưởng phòng sửa chính sách cam kết của hạng cao cấp thành 30 phút.
5. **Kỳ vọng (BR-40.2):** Vé của X đang mở vẫn giữ hạn chót 1 giờ theo cam kết tại thời điểm tạo; chỉ các vé tạo mới sau đó mới áp dụng mức 30 phút.

---

### Kịch bản 14: Sử dụng Câu trả lời Mẫu
1. Tư vấn viên mở một vé hỏi về cách đổi mật khẩu và chọn mẫu trả lời có sẵn trong thư viện dùng chung.
2. **Kỳ vọng (BR-16.2):** Nội dung mẫu được chèn vào khung soạn thảo với tên khách hàng và mã số vé đã được điền tự động đúng theo vé đang mở.
3. Tư vấn viên sửa thêm một câu cho phù hợp ngữ cảnh rồi gửi.
4. **Kỳ vọng (BR-16.3):** Nội dung gửi đi là nội dung đã chỉnh sửa, không phải nguyên văn mẫu.

---

### Kịch bản 15: Thông báo cho Tư vấn viên
1. Trưởng nhóm gán một vé cho tư vấn viên A.
2. **Kỳ vọng (BR-37.1):** A nhận được thông báo trong ứng dụng về vé mới được gán.
3. Khách hàng phản hồi trên vé đó.
4. **Kỳ vọng:** A nhận thông báo về phản hồi mới của khách hàng.
5. A tự ghi chú nội bộ trên chính vé của mình.
6. **Kỳ vọng (BR-37.3):** A không nhận thông báo về hành động của chính mình.

---

### Kịch bản 16: Hai Người cùng Xử lý Một Vé
1. Tư vấn viên A và B cùng mở một vé trong hàng đợi.
2. **Kỳ vọng (BR-38.1):** Cả hai nhìn thấy cảnh báo có người khác đang xem cùng vé.
3. A bắt đầu soạn phản hồi.
4. **Kỳ vọng (BR-38.2):** B nhìn thấy cảnh báo A đang soạn trả lời.
5. A gửi phản hồi trong lúc B cũng đang soạn dở một nội dung khác.
6. **Kỳ vọng (BR-38.3):** B được cảnh báo vé đã có phản hồi mới và phải xem lại trước khi gửi nội dung của mình.

---

### Kịch bản 17: Khiếu nại về Chất lượng Phục vụ
1. Khách hàng phản hồi trên vé, bày tỏ không hài lòng về thái độ của tư vấn viên A đang phục vụ họ.
2. A gắn cờ "khách hàng không hài lòng" và leo thang vé lên Trưởng nhóm kèm lý do.
3. **Kỳ vọng (BR-39.1, BR-39.2):** Trưởng nhóm nhận thông báo ngay; vé được ưu tiên hiển thị trên bảng điều khiển.
4. Trưởng nhóm xác định khiếu nại nhắm vào chính A và chuyển vé sang tư vấn viên D để xử lý.
5. **Kỳ vọng (BR-39.3, BR-39.4):** Nội dung rà soát khiếu nại nằm trên một vé khiếu nại riêng liên kết với vé gốc; A không mở được vé khiếu nại đó nhưng vẫn xem được vé gốc đầy đủ, liền mạch theo thứ tự thời gian; hạn chót cam kết của vé gốc không thay đổi.

---

### Kịch bản 18: Tách Vé Chứa Hai Vấn đề
1. Khách hàng gửi một yêu cầu vừa báo lỗi không đăng nhập được, vừa thắc mắc về hóa đơn tháng trước.
2. Tư vấn viên tách phần nội dung về hóa đơn sang một vé mới, phân loại vào nhánh "Kế toán - Hóa đơn".
3. **Kỳ vọng (BR-41.1, BR-41.2, BR-41.3, BR-41.4):** Vé mới được tạo với mã số riêng, liên kết hai chiều với vé gốc; nội dung đã tách vẫn hiển thị trên vé gốc kèm ghi chú đã chuyển sang vé nào; vé mới nhận cam kết tính từ thời điểm tách; khách hàng nhận thông báo về vé mới kèm mã số.

---

### Kịch bản 19: Giám sát Hàng đợi Thời gian thực
1. Trưởng nhóm mở bảng điều khiển vào giờ cao điểm.
2. **Kỳ vọng (BR-42.1):** Bảng hiển thị số vé chưa có người xử lý, số vé sắp tới hạn, số vé đã vi phạm, số vé mang cờ khách hàng bức xúc, và số vé đang mở của từng tư vấn viên so với hạn mức.
3. Trưởng nhóm thấy tư vấn viên A đang giữ 10/10 vé trong khi B chỉ giữ 3 vé, liền chuyển bớt vé từ A sang B ngay trên bảng điều khiển.
4. **Kỳ vọng (BR-42.3):** Thao tác chuyển vé thực hiện được trực tiếp mà không cần rời bảng điều khiển.
5. Trưởng nhóm lưu bộ lọc "Vé quá hạn của nhóm tôi" để dùng lại.
6. **Kỳ vọng (BR-42.4):** Bộ lọc được lưu và mở lại được ở phiên làm việc sau.

---

### Kịch bản 20: Báo cáo Tuân thủ Cam kết cho Khách hàng
1. Trưởng phòng cần gửi báo cáo tháng cho khách hàng doanh nghiệp X theo điều khoản hợp đồng.
2. Trưởng phòng lọc báo cáo tuân thủ cam kết theo khách hàng X trong kỳ tháng trước.
3. **Kỳ vọng (BR-43.1, BR-43.2):** Báo cáo hiển thị tỷ lệ tuân thủ cam kết phản hồi và xử lý dứt điểm của riêng khách hàng X; các vé đã gộp và vé nhập khẩu bị loại khỏi mẫu số; thời gian đồng hồ tạm dừng hợp lệ không bị tính vào thời gian xử lý; quy tắc tính được in kèm trên báo cáo.
4. Trưởng phòng đặt lịch gửi tự động báo cáo này vào ngày 1 hàng tháng.
5. **Kỳ vọng (BR-43.8):** Báo cáo được xuất ra tệp và gửi tự động theo lịch đã đặt.

---

### Kịch bản 21: Truy vết Tranh chấp về Thay đổi Cam kết
1. Khách hàng khiếu nại rằng vé của họ ban đầu được cam kết xử lý trong 2 giờ nhưng sau đó bị kéo dài.
2. Trưởng phòng mở nhật ký thay đổi của vé.
3. **Kỳ vọng (BR-44.1, BR-44.2):** Nhật ký cho thấy rõ mức ưu tiên đã bị hạ từ "Cao" xuống "Trung bình" lúc nào, do ai thực hiện, kèm giá trị trước và sau — đủ căn cứ để đối chiếu với khách hàng.
4. Quản trị viên thử xóa bản ghi nhật ký này.
5. **Kỳ vọng (BR-44.3, NFR-12):** Hệ thống từ chối thao tác xóa.

---

### Kịch bản 22: Yêu cầu Xóa Dữ liệu Cá nhân
1. Một khách hàng cá nhân gửi yêu cầu xóa dữ liệu cá nhân của họ theo Nghị định 13.
2. Trưởng phòng tiếp nhận và thực hiện ẩn danh hóa toàn bộ vé của khách hàng đó.
3. **Kỳ vọng (BR-45.2):** Họ tên, số điện thoại, email, nội dung trao đổi chứa thông tin cá nhân và tệp đính kèm được thay bằng dấu hiệu đã ẩn danh; các số liệu thống kê phi định danh của những vé đó (thời gian xử lý, danh mục, kết quả tuân thủ cam kết) vẫn được giữ nguyên.
4. Trưởng phòng mở lại báo cáo tuân thủ cam kết của kỳ trước đó.
5. **Kỳ vọng:** Các chỉ số đã công bố trước đây không bị thay đổi sau khi ẩn danh hóa.
6. **Kỳ vọng (BR-45.3):** Việc ẩn danh hóa được ghi vào nhật ký kèm người yêu cầu, người thực hiện, thời điểm và phạm vi vé bị ảnh hưởng.

---

### Kịch bản 23: Phân bổ Xoay vòng theo Hạn mức
1. Hạn mức năng lực đặt ở mức mặc định 10 vé. Nhóm có 3 tư vấn viên đang trực: A được gán 10 vé chưa kết thúc, B được gán 4 vé, C được gán 4 vé.
2. Trong số 10 vé của A có 3 vé đang ở trạng thái chờ khách hàng phản hồi.
3. **Kỳ vọng (BR-10.2):** A được tính là đang giữ 7 vé, vẫn còn chỗ trống và vẫn nằm trong danh sách được phân bổ.
4. Ba vé mới lần lượt phát sinh.
5. **Kỳ vọng (BR-10.1, BR-10.5):** Ba vé được chia lần lượt cho các tư vấn viên theo thứ tự xoay vòng, không dồn hết cho một người.
6. Giả định cả ba đều đã đạt hạn mức và một vé mới phát sinh.
7. **Kỳ vọng (BR-10.4):** Vé được đưa vào hàng đợi chung và Trưởng nhóm nhận cảnh báo, thay vì bị gán ép cho người đã quá tải.

---

### Kịch bản 24: Tính Hạn chót Cam kết qua Ranh giới Ngoài Giờ Làm việc
1. Doanh nghiệp cấu hình lịch làm việc 08:00-17:30 từ Thứ Hai đến Thứ Sáu, múi giờ Việt Nam, có khai báo ngày nghỉ lễ.
2. Một vé phát sinh lúc 17:00 ngày Thứ Sáu với cam kết xử lý dứt điểm trong 4 giờ làm việc.
3. **Kỳ vọng (BR-08.1):** Hạn chót rơi vào 11:30 sáng Thứ Hai tuần sau (30 phút còn lại của Thứ Sáu, từ 17:00 tới 17:30, cộng 3 giờ 30 phút của Thứ Hai tính từ 08:00), không phải 21:00 tối Thứ Sáu.
4. Giả định Thứ Hai đó là ngày nghỉ lễ đã khai báo.
5. **Kỳ vọng (BR-08.1):** Hạn chót dời sang 11:30 sáng Thứ Ba, giữ nguyên cách tính ở bước 3.
6. Trưởng phòng sửa khung giờ làm việc thành 08:00-18:00 trong lúc vé trên vẫn đang mở.
7. **Kỳ vọng (BR-08.5):** Hạn chót của vé đang mở giữ nguyên như đã tính; chỉ vé tạo mới sau thời điểm sửa mới áp dụng khung giờ mới.
8. Quản trị viên khai báo bổ sung một ngày nghỉ lễ rơi vào Thứ Ba nói trên, trong lúc vé vẫn đang mở.
9. **Kỳ vọng (BR-08.6):** Khác với bước 7, lần này hạn chót của vé đang mở **được tính lại** và dời sang 11:30 sáng Thứ Tư; hệ thống báo trước số vé bị ảnh hưởng và hạn chót mới để người cấu hình xác nhận.

---

### Kịch bản 25: Điều phối theo Kỹ năng và Xử lý khi Không có Người Phù hợp
1. Doanh nghiệp bật điều phối theo kỹ năng, chọn cách ứng xử "chờ rồi hạ tiêu chuẩn" với thời gian chờ 15 phút.
2. Một vé thuộc danh mục "Lỗi tích hợp kỹ thuật" yêu cầu hai kỹ năng: "tích hợp kỹ thuật" và "tiếng Anh". Trong nhóm có hai người đủ cả hai kỹ năng nhưng đang đầy hạn mức; ba người khác đang rảnh, trong đó D có kỹ năng "tiếng Anh", E có kỹ năng "tiếng Anh", F không có kỹ năng nào trong hai kỹ năng trên.
3. **Kỳ vọng (BR-11.3, BR-11.4):** Vé không bị gán ngay cho ba người rảnh; vé nằm chờ trong hàng đợi chung và Trưởng nhóm nhận cảnh báo ngay lập tức.
4. Sau 15 phút vẫn chưa có người đủ kỹ năng nào trống chỗ.
5. **Kỳ vọng (BR-11.4, cách ứng xử (c)):** Hệ thống gán vé cho D hoặc E — hai người khớp 1 kỹ năng, nhiều hơn F khớp 0 kỹ năng. Vì D và E khớp bằng nhau, người được chọn xác định theo thứ tự xoay vòng. F không được chọn chừng nào còn người khớp nhiều kỹ năng hơn đang rảnh.
6. **Kỳ vọng (BR-11.5):** Toàn bộ 15 phút chờ vẫn được tính vào đồng hồ cam kết phản hồi đầu tiên.

---

### Kịch bản 26: Chặn Gộp Vé của Hai Khách hàng Khác nhau
1. Tư vấn viên chọn hai vé để gộp: một vé của khách hàng A, một vé của khách hàng B, cả hai cùng báo về một lỗi giống nhau.
2. **Kỳ vọng (BR-27.4):** Hệ thống từ chối thao tác gộp và giải thích rằng chỉ gộp được các vé của cùng một khách hàng; hệ thống gợi ý dùng mô hình vé cha - vé con (FEAT-28) cho trường hợp nhiều khách hàng cùng gặp một sự cố.
3. **Kỳ vọng:** Không có nội dung trao đổi nào của khách hàng A bị hiển thị trên vé của khách hàng B và ngược lại.

---

### Kịch bản 27: Không được Đóng Vé khi Chưa Phản hồi Khách hàng
1. Một vé mới được tạo, tư vấn viên xem xét và thấy sự cố đã tự hết, chưa từng gửi phản hồi công khai nào cho khách hàng.
2. Tư vấn viên thử chuyển vé sang trạng thái "đã xử lý xong".
3. **Kỳ vọng (BR-19.2):** Hệ thống từ chối và yêu cầu gửi phản hồi cho khách hàng trước khi kết thúc vé.
4. Tư vấn viên gửi phản hồi thông báo sự cố đã được khắc phục, nhập nguyên nhân xử lý và tóm tắt giải pháp, rồi kết thúc vé.
5. **Kỳ vọng (BR-19.1, BR-07.3):** Vé chuyển trạng thái thành công; mốc cam kết phản hồi đầu tiên được ghi nhận hoàn tất tại thời điểm gửi phản hồi đó.

---

### Kịch bản 28: Xác định Mức Ưu tiên theo Ma trận Tác động
1. Doanh nghiệp cấu hình ma trận: Tác động "Toàn công ty" × Khẩn cấp "Chặn hoàn toàn công việc" → mức ưu tiên cao nhất.
2. Tư vấn viên tiếp nhận một vé, đánh giá tác động là "Toàn công ty" và mức khẩn cấp là "Chặn hoàn toàn công việc".
3. **Kỳ vọng (BR-06.1):** Hệ thống tự xác định mức ưu tiên cao nhất và áp dụng chính sách cam kết tương ứng.
4. Trưởng nhóm cho rằng mức này chưa đúng và hạ xuống mức thấp hơn.
5. **Kỳ vọng (BR-06.2, BR-06.3):** Hệ thống yêu cầu nhập lý do, ghi việc ghi đè vào nhật ký thay đổi, và tính lại hạn chót cam kết theo mức ưu tiên mới nhưng vẫn tính từ thời điểm tạo vé ban đầu.

---

### Kịch bản 29: Tự động Đóng Vé sau Thời gian Không Phản hồi
1. Doanh nghiệp cấu hình thời hạn chờ 48 giờ làm việc. Một vé chuyển sang "đã xử lý xong" lúc 10:00 Thứ Hai.
2. Trước khi hết thời hạn, hệ thống gửi thông báo nhắc khách hàng.
3. **Kỳ vọng (BR-22.3):** Khách hàng nhận được nhắc nhở rằng vé sắp được đóng và có thể phản hồi nếu chưa hài lòng.
4. Khách hàng không phản hồi cho tới khi hết 48 giờ làm việc.
5. **Kỳ vọng (BR-22.1, BR-22.4):** Vé tự động chuyển sang trạng thái đóng hoàn tất; kết quả tuân thủ cam kết xử lý dứt điểm vẫn được tính theo mốc 10:00 Thứ Hai, không phải thời điểm đóng tự động.
6. Với một vé khác, khách hàng phản hồi trong thời gian chờ.
7. **Kỳ vọng (BR-22.2):** Vé đó không bị đóng tự động mà quay lại luồng xử lý theo FEAT-21.

---

### Kịch bản 30: Tạo Vé, Bắt buộc Thông tin & Phân loại Nhiều Cấp
1. Tư vấn viên tạo vé mới nhưng bỏ trống người yêu cầu.
2. **Kỳ vọng (BR-01.1):** Hệ thống từ chối lưu và chỉ rõ thông tin còn thiếu.
3. Tư vấn viên điền đủ thông tin bắt buộc, chọn khách hàng cá nhân là người yêu cầu.
4. **Kỳ vọng (BR-03.2, BR-03.3):** Doanh nghiệp liên quan được tự động điền theo hồ sơ khách hàng; màn hình hiển thị hạng khách hàng, số vé trước đó và điểm hài lòng trung bình.
5. Tư vấn viên chọn phân loại nhưng dừng ở cấp giữa của cây danh mục.
6. **Kỳ vọng (BR-04.1):** Hệ thống yêu cầu chọn tiếp tới cấp cuối của nhánh.
7. Quản trị viên đánh dấu ngừng sử dụng một danh mục đang được dùng trên các vé cũ.
8. **Kỳ vọng (BR-04.2):** Danh mục không còn xuất hiện khi phân loại vé mới, nhưng các vé cũ vẫn giữ nguyên phân loại và báo cáo lịch sử không thay đổi.

---

### Kịch bản 31: Trường Tùy biến & Ràng buộc khi Kết thúc Vé
1. Quản trị viên định nghĩa trường tùy biến "Mã Giao dịch Ngân hàng" và đánh dấu là bắt buộc.
2. Tư vấn viên tạo vé mới mà chưa có thông tin này.
3. **Kỳ vọng (BR-05.1):** Hệ thống vẫn cho tạo vé, vì thời điểm tiếp nhận ban đầu chưa chắc có đủ thông tin.
4. Tư vấn viên thử kết thúc vé khi trường này vẫn trống.
5. **Kỳ vọng (BR-05.1):** Hệ thống từ chối và yêu cầu điền trường bắt buộc trước khi kết thúc.
6. Trưởng phòng lọc danh sách vé theo giá trị của trường tùy biến này và xuất ra tệp.
7. **Kỳ vọng (BR-05.3):** Trường tùy biến lọc được và có mặt trong tệp xuất.

---

### Kịch bản 32: Ghi chú Nội bộ & Đính kèm Tệp
1. Tư vấn viên viết một ghi chú nội bộ nhắc tên đồng nghiệp để xin hỗ trợ, đính kèm ảnh chụp màn hình lỗi.
2. **Kỳ vọng (BR-14.1, BR-14.2):** Ghi chú hiển thị khác biệt rõ với phản hồi công khai; đồng nghiệp được nhắc tên nhận thông báo kèm đường dẫn tới vé.
3. **Kỳ vọng (BR-14.3):** Ghi chú nội bộ này không làm hoàn tất mốc cam kết phản hồi đầu tiên.
4. Khách hàng mở vé của mình trên giao diện dành cho khách hàng.
5. **Kỳ vọng (NFR-09, BR-15.3):** Khách hàng không nhìn thấy ghi chú nội bộ và không tải được tệp đính kèm trong ghi chú đó.
6. Tư vấn viên thử đính kèm 25 tệp trong một lượt gửi.
7. **Kỳ vọng (BR-15.1):** Hệ thống từ chối và nêu rõ giới hạn 20 tệp mỗi lượt.

---

### Kịch bản 33: Nhập Dữ liệu từ Hệ thống Cũ
1. Trưởng phòng nhập tệp dữ liệu 5.000 vé từ hệ thống cũ, trong đó 30 dòng có lỗi định dạng ngày tháng.
2. **Kỳ vọng (BR-24.3):** 4.970 dòng hợp lệ được nhập thành công; 30 dòng lỗi được liệt kê trong báo cáo kết quả kèm lý do từng dòng; hệ thống không hủy toàn bộ lần nhập.
3. Trưởng phòng sửa 30 dòng lỗi và nhập lại chính tệp đó.
4. **Kỳ vọng (BR-24.4):** Hệ thống bỏ qua 4.970 bản ghi đã nhập lần trước, chỉ nhập thêm 30 bản ghi đã sửa; không phát sinh dữ liệu trùng.
5. Trưởng phòng xem báo cáo tuân thủ cam kết của kỳ hiện tại.
6. **Kỳ vọng (BR-24.5, BR-43.2):** Toàn bộ vé nhập khẩu bị loại khỏi mẫu số tính tỷ lệ tuân thủ.

---

### Kịch bản 34: Xuất Dữ liệu có Kiểm soát
1. Trưởng nhóm thực hiện xuất danh sách vé trong phạm vi nhóm mình.
2. **Kỳ vọng (BR-25.2, BR-25.5):** Tệp xuất chỉ chứa dữ liệu trong phạm vi quyền của người đó, không bao gồm vé của nhóm khác.
2b. Cũng Trưởng nhóm đó thử xuất toàn bộ dữ liệu của không gian làm việc.
2c. **Kỳ vọng (BR-25.5):** Hệ thống từ chối — mức xuất toàn bộ chỉ thuộc Trưởng phòng và Quản trị viên, không hạ xuống cấp Trưởng nhóm trong bất kỳ cấu hình nào.
3. **Kỳ vọng (BR-25.4, BR-44.4):** Lần xuất được ghi vào nhật ký kèm người xuất, thời điểm và phạm vi dữ liệu.
4. Người dùng lưu lại đường dẫn tải và thử truy cập lại sau 25 giờ.
5. **Kỳ vọng (BR-25.1, NFR-10):** Đường dẫn đã hết hiệu lực, phải yêu cầu xuất lại.

---

### Kịch bản 35: Xóa Nhầm và Phục hồi Vé
1. Trưởng phòng xóa nhầm một vé đang xử lý dở.
2. **Kỳ vọng (BR-26.5):** Vé lập tức biến mất khỏi mọi báo cáo và thống kê tuân thủ cam kết.
3. Trưởng phòng phát hiện và phục hồi vé từ Thùng rác trong cùng ngày.
4. **Kỳ vọng (BR-26.2):** Vé trở lại đúng trạng thái, người phụ trách và toàn bộ lịch sử trao đổi như trước khi xóa.
5. Trưởng phòng thử xóa một vé đang là vé cha của nhiều vé con.
6. **Kỳ vọng (BR-26.4):** Hệ thống từ chối và yêu cầu gỡ quan hệ cha - con trước.

---

### Kịch bản 36: Cảnh báo Rủi ro Tự động sang Cơ hội Bán hàng
1. Khách hàng doanh nghiệp đang có một cơ hội bán hàng ở giai đoạn đàm phán và đồng thời tạo một vé hỗ trợ ở mức ưu tiên cao.
2. **Kỳ vọng (BR-30.1, BR-30.3):** Thẻ cơ hội bán hàng tự động hiển thị cảnh báo rủi ro; nhân viên kinh doanh xem được danh sách vé gây ra cảnh báo trong phạm vi quyền của mình.
3. Vé hỗ trợ được xử lý xong.
4. **Kỳ vọng (BR-30.2):** Cảnh báo tự động biến mất mà không cần thao tác thủ công.

---

### Kịch bản 37: Gắn Nhãn Hàng loạt
1. Trưởng nhóm chọn 300 vé liên quan tới một đợt sự cố và gắn nhãn chung.
2. **Kỳ vọng (BR-23.2):** Hệ thống hiển thị rõ số vé sẽ bị ảnh hưởng và yêu cầu xác nhận trước khi thực hiện.
3. Trong số đó có 5 vé đã bị khóa do đã gộp vào vé khác.
4. **Kỳ vọng (BR-23.3):** Hệ thống báo 295 vé thành công và liệt kê 5 vé không xử lý được kèm lý do.
5. **Kỳ vọng (BR-23.4):** Thao tác được ghi vào nhật ký thay đổi của từng vé bị ảnh hưởng.

---

### Kịch bản 38: Dòng Trao đổi Hợp nhất & Tính Bất biến
1. Trên một vé đã qua nhiều bước xử lý, tư vấn viên mở dòng trao đổi.
2. **Kỳ vọng (BR-13.1):** Toàn bộ nội dung hiển thị theo thứ tự thời gian, phân biệt rõ ba loại: phản hồi công khai gửi khách hàng, ghi chú nội bộ, và thông báo hệ thống tự sinh khi đổi trạng thái hoặc đổi người phụ trách.
3. Tư vấn viên thử sửa lại một phản hồi đã gửi nhầm cho khách hàng.
4. **Kỳ vọng (BR-13.1, NFR-01):** Hệ thống không cho sửa cũng không cho xóa; tư vấn viên phải gửi một phản hồi mới để đính chính.

---

### Kịch bản 39: Chuyển Giải pháp thành Bài viết Tri thức
1. Sau khi xử lý xong một vé có giải pháp hữu ích, tư vấn viên tạo bản nháp bài viết tri thức từ vé đó.
2. **Kỳ vọng (BR-31.1, BR-31.4):** Nội dung câu hỏi và giải pháp được điền sẵn từ vé; bản nháp lưu tham chiếu tới vé gốc.
3. Trưởng nhóm rà soát và phát hiện nội dung còn chứa tên và số điện thoại khách hàng.
4. **Kỳ vọng (BR-31.2, BR-31.3):** Hệ thống không cho xuất bản cho tới khi thông tin định danh khách hàng được loại bỏ; bài viết chỉ chính thức sau khi quản lý duyệt.

---

### Kịch bản 40: Giám sát & Hướng dẫn Nhân viên Mới
1. Trưởng nhóm bật chế độ giám sát một tư vấn viên mới đang xử lý vé.
2. **Kỳ vọng (BR-32.2):** Tư vấn viên đó nhìn thấy thông báo mình đang được giám sát.
3. Trưởng nhóm gửi một chỉ dẫn nhắc tư vấn viên bổ sung thông tin còn thiếu trước khi trả lời khách.
4. **Kỳ vọng (BR-32.1):** Chỉ dẫn chỉ hiển thị cho tư vấn viên; khách hàng không thấy nội dung này ở bất kỳ đâu.
5. **Kỳ vọng (BR-32.3):** Phiên hướng dẫn được ghi nhận để theo dõi tiến bộ của nhân viên qua thời gian.

---

### Kịch bản 41: Ghi nhận Giờ Hỗ trợ Tính phí
1. Tư vấn viên bấm giờ khi bắt đầu xử lý một vé thuộc hợp đồng bảo trì tính phí, kết thúc sau 90 phút.
2. **Kỳ vọng (BR-33.1, BR-33.2):** 90 phút được ghi nhận kèm mô tả công việc và được đánh dấu là giờ tính phí theo điều khoản hợp đồng của khách hàng đó.
3. Trưởng phòng xuất dữ liệu giờ tính phí của khách hàng này để bàn giao cho khâu xuất hóa đơn.
4. **Kỳ vọng (BR-33.3):** Chỉ những khoản giờ đã được quản lý duyệt mới xuất hiện trong dữ liệu bàn giao.
5. **Kỳ vọng (BR-43.7, KPI-10):** Báo cáo tổng hợp được tổng số giờ hỗ trợ thực tế và số giờ tính phí theo từng khách hàng, từng hợp đồng và từng tư vấn viên trong kỳ.

---

### Kịch bản 42: Hai Người Cùng Nhận và Cùng Sửa Một Vé
1. Một vé nằm trong hàng đợi chung của nhóm. Hai tư vấn viên A và B cùng nhìn thấy và bấm nhận gần như đồng thời.
2. **Kỳ vọng (BR-34.5):** Chỉ một người nhận được vé; người còn lại nhận thông báo vé đã có người nhận kèm tên người đó, và vé biến mất khỏi hàng đợi của họ. Không có trường hợp vé được gán cho cả hai.
3. Trưởng nhóm và người đang phụ trách cùng đổi mức ưu tiên của một vé, hai thao tác cách nhau 20 giây (nằm trong cửa sổ 60 giây của BR-38.4).
4. **Kỳ vọng (BR-38.4):** Thay đổi đến sau được ghi nhận; người thao tác sau được báo giá trị vừa bị thay thế là do ai đặt và lúc nào. Cả hai lần thay đổi đều có mặt trong nhật ký theo BR-44.1.
5. Một tư vấn viên đang soạn phản hồi thì người khác đổi trạng thái vé sang kết thúc.
6. **Kỳ vọng (BR-38.3):** Người đang soạn nhận cảnh báo về thay đổi này trước khi gửi, không chỉ khi có phản hồi mới — vì nội dung đang soạn có thể không còn phù hợp.

---

### Kịch bản 43: Đổi Cấu hình Trạng thái Vé khi Đang có Vé Sử dụng
1. Doanh nghiệp đang có 200 vé ở trạng thái tự định nghĩa "Chờ khách hàng" — trạng thái này được đánh dấu tạm dừng cam kết.
2. Quản trị viên thử xóa hẳn trạng thái này.
3. **Kỳ vọng (BR-01.4):** Hệ thống từ chối xóa, chỉ cho đánh dấu ngừng sử dụng; trạng thái biến mất khỏi danh sách chọn cho vé mới nhưng 200 vé cũ vẫn giữ nguyên trạng thái của mình.
4. Quản trị viên thử bỏ đánh dấu "tạm dừng cam kết" khỏi trạng thái này.
5. **Kỳ vọng (BR-01.5):** Hệ thống báo trước số vé đang chịu ảnh hưởng; sau khi xác nhận, 200 vé đang mở **giữ nguyên** cách tính cũ cho tới khi kết thúc, chỉ vé phát sinh sau thời điểm sửa mới áp dụng dấu hiệu mới. Không vé nào đột ngột chuyển thành vi phạm vì thao tác cấu hình này.
6. Quản trị viên thử đánh dấu ngừng sử dụng trạng thái kết thúc dạng "đã xử lý xong" duy nhất đang có.
7. **Kỳ vọng (BR-01.4):** Hệ thống từ chối, vì thiếu trạng thái này thì không đo được cam kết xử lý dứt điểm theo BR-07.4.

---

### Kịch bản 44: Ẩn danh hóa Trên Toàn bộ Nơi Lưu Dữ liệu
1. Một khách hàng cá nhân thuộc doanh nghiệp khách hàng X gửi yêu cầu xóa dữ liệu cá nhân. Người này có 12 vé: 8 vé đã đóng, 2 vé đang mở, 1 vé nằm trong Thùng rác được 10 ngày, 1 vé đã chuyển sang lưu trữ dài hạn.
2. Trưởng phòng thực hiện ẩn danh hóa theo yêu cầu.
3. **Kỳ vọng (BR-45.6):** Cả 12 vé đều được ẩn danh, bao gồm vé trong Thùng rác và vé lưu trữ dài hạn; tệp đính kèm của các vé đó cũng được xử lý theo BR-15.4.
4. **Kỳ vọng (BR-45.2):** Toàn bộ nội dung trao đổi do người viết nhập được thay thế trọn vẹn, không phải chỉ dò xóa một phần câu chữ; siêu dữ liệu của từng tin nhắn được giữ lại nên dòng thời gian xử lý vẫn đọc được.
5. Quản trị viên phục hồi vé trong Thùng rác.
6. **Kỳ vọng (BR-26.2, BR-45.6):** Vé trở lại ở **dạng đã ẩn danh**; dữ liệu cá nhân không được khôi phục.
7. Mở nhật ký thay đổi của một vé đã ẩn danh, xem các dòng ghi giá trị trước và sau.
8. **Kỳ vọng (BR-45.8, NFR-12):** Dòng nhật ký vẫn còn đủ (ai làm, lúc nào, thao tác gì) nhưng nội dung cá nhân trong đó đã được thay bằng dấu hiệu ẩn danh; việc thay thế này cũng được ghi thành một dòng nhật ký mới.
9. Xuất báo cáo hợp đồng của doanh nghiệp khách hàng X.
10. **Kỳ vọng (BR-45.7, BR-43.1):** Số vé của doanh nghiệp X trong báo cáo **không bị hụt**, vì liên kết giữa vé và pháp nhân khách hàng được giữ nguyên — chỉ dữ liệu cá nhân của người liên hệ bị gỡ.
11. **Kỳ vọng (BR-45.4, KPI-13):** Toàn bộ quá trình hoàn tất trong 72 giờ thực kể từ khi tiếp nhận; hệ thống có cảnh báo người phụ trách ở mốc còn 24 giờ và còn 6 giờ.

---

### Kịch bản 45: Lịch Trực Không Phủ Hết Lịch Cam kết
1. Doanh nghiệp gán cho một khách hàng cao cấp chính sách cam kết dùng lịch phục vụ liên tục cả ngày đêm, trong khi lịch trực của đội chỉ xếp người từ 08:00 tới 18:00.
2. **Kỳ vọng (BR-35.5):** Hệ thống cảnh báo Quản trị viên rằng có khung giờ nằm trong cam kết nhưng không ai trực, nêu rõ khung giờ nào.
3. Quản trị viên chỉ định người trực ngoài giờ cho khung 18:00–08:00.
4. Một vé của khách hàng này phát sinh lúc 02:00 sáng.
5. **Kỳ vọng (BR-35.6, BR-37.4):** Cảnh báo được gửi tới người trực ngoài giờ qua kênh có khả năng đánh thức (tin nhắn hoặc cuộc gọi tự động), không chỉ thông báo trong ứng dụng.
6. **Kỳ vọng (BR-08.7):** Nếu hôm đó là ngày nghỉ lễ, cam kết vẫn được tính bình thường theo cấu hình mặc định của lịch phục vụ liên tục.
7. Giả định kênh gửi tin nhắn gặp sự cố và lần gửi đầu thất bại.
8. **Kỳ vọng (BR-37.5):** Hệ thống thử lại theo số lần đã cấu hình; nếu vẫn thất bại thì ghi nhận trên vé và báo Trưởng nhóm, không để cảnh báo âm thầm biến mất.

---

### Kịch bản 46: Thẩm quyền Gộp Vé theo Cấu hình Doanh nghiệp
1. Doanh nghiệp giữ nguyên cấu hình mặc định về quyền gộp vé.
2. Một Trưởng nhóm thử gộp hai vé trùng lặp của cùng một khách hàng.
3. **Kỳ vọng (BR-27.7):** Hệ thống từ chối, vì mặc định quyền này ở mức Trưởng phòng.
4. Quản trị viên mở rộng quyền gộp vé xuống cấp Trưởng nhóm.
5. **Kỳ vọng (BR-27.7):** Cùng Trưởng nhóm đó nay gộp được; thao tác vẫn được ghi vết đầy đủ theo BR-44.1.
6. Trưởng nhóm này thử hoàn tác chính thao tác gộp mình vừa làm, trong thời hạn 24 giờ.
7. **Kỳ vọng (BR-27.7):** Hệ thống từ chối — quyền hoàn tác không hạ xuống dưới cấp Trưởng phòng trong bất kỳ cấu hình nào, để người gộp nhầm không tự xóa dấu vết thao tác của mình.
8. Trưởng phòng thực hiện hoàn tác thao tác đó.
9. **Kỳ vọng (BR-27.5):** Vé phụ được khôi phục về trạng thái trước khi gộp.

---

## 7. Khoảng cách Triển khai & Đề xuất Bổ sung

Mục này tổng hợp lại các khoảng cách kỹ thuật và nghiệp vụ thực tế còn lại sau đợt rà soát và kiểm chứng mã nguồn (16/09/2026), xếp theo mức độ ảnh hưởng, làm căn cứ cho các giai đoạn phát triển tiếp theo.

> **Ghi chú về các hạng mục đã hoàn thành:** Đợt rà soát thực tế mã nguồn cho thấy phần lớn các tính năng từng liệt kê ở phiên bản trước nay đã được triển khai hoàn chỉnh và có kiểm thử bảo vệ: Thư viện câu trả lời mẫu (`FEAT-16`), Tách vé (`FEAT-41`), Cảnh báo trùng thao tác (`FEAT-38`), Leo thang thủ công (`FEAT-39`), Chuyển vé hàng loạt (`FEAT-36`), Thông báo cho tư vấn viên (`FEAT-37`), Cảnh báo rủi ro sang Deals (`FEAT-30`), Chuyển giải pháp sang bài viết tri thức (`FEAT-31`), Giám sát hướng dẫn whisper (`FEAT-32`), Theo dõi giờ tính phí (`FEAT-33`), Gửi khảo sát CSAT tự động (`FEAT-20`), Tự động đóng vé (`FEAT-22`), Giới hạn thời gian mở lại vé (`FEAT-21`), Chặn gộp vé khác khách hàng (`BR-27.4`), Hàng đợi chung (`FEAT-34`), Ca trực & sẵn sàng (`FEAT-35`), Báo cáo tuân thủ & hiệu suất (`FEAT-43`), Nhật ký thay đổi & truy vết (`FEAT-44`), Vòng đời dữ liệu cá nhân Nghị định 13 (`FEAT-45`).

### 7.1 Khoảng cách nghiệp vụ & cấu hình còn lại

1. **Cơ chế đẩy tức thời cho Bảng điều khiển Hàng đợi (FEAT-42):** Bảng điều khiển hàng đợi thời gian thực hiện đang hoạt động theo cơ chế thăm dò định kỳ (polling 30 giây/lần). Khoảng cách còn lại là xây dựng hạ tầng kết nối thời gian thực (WebSocket hoặc Server-Sent Events) để đẩy các biến động hàng đợi ngay lập tức khi phát sinh.
2. **Cấu hình thời hạn lưu Thùng rác theo doanh nghiệp (FEAT-26, BR-26.1):** Thời hạn lưu trữ vé trong Thùng rác trước khi bị xóa vĩnh viễn hiện được thiết lập qua biến môi trường toàn hệ thống (`TICKET_RECYCLE_BIN_RETENTION_DAYS`, mặc định 30 ngày), chưa hỗ trợ cấu hình động riêng cho từng doanh nghiệp qua giao diện cài đặt.
3. **Chính sách SLA theo Hợp đồng Dịch vụ (FEAT-40, BR-40.1 tầng 1):** Phân cấp chính sách cam kết dịch vụ hiện đã hỗ trợ tầng 2 (Hạng khách hàng VIP) và tầng 3 (Mức ưu tiên mặc định). Tầng 1 (áp dụng chính sách riêng theo hợp đồng dịch vụ cụ thể) phụ thuộc vào thực thể Hợp đồng thuộc phân hệ Hợp đồng & Thanh toán (ngoài phân hệ Vé).
4. **Ma trận Mức độ Ưu tiên Cấu hình được (FEAT-06, BR-06.1 — GAP-04):** Ma trận suy ra mức độ ưu tiên từ tổ hợp Tác động × Khẩn cấp hiện đang dùng bảng giá trị cố định chuẩn trong mã nguồn, chưa có màn hình quản trị để doanh nghiệp tự tùy biến ma trận riêng.
5. **Chính sách bồi thường khi vi phạm cam kết:** Tự động tính khoản bù trừ khi mức vi phạm vượt ngưỡng cam kết SLA trong hợp đồng — thuộc phân hệ Billing & Contract Management, nằm ngoài phân hệ Vé Hỗ trợ nhưng cần đồng bộ khi triển khai gói dịch vụ cao cấp.

### 7.2 Đề xuất cải tiến giai đoạn sau

1. **Tự động trả lời và gợi ý giải pháp bằng AI / Trợ lý ảo:** Tích hợp với phân hệ AI Service Bot để tự động phân loại, gợi ý câu trả lời mẫu hoặc giải pháp dựa trên cơ sở tri thức đã xây dựng từ các vé đã giải quyết.
2. **Kênh đánh thức chuyên biệt ngoài giờ (FEAT-37, BR-37.4):** Bổ sung tích hợp cuộc gọi tự động (Automated Voice Call) hoặc SMS cảnh báo khẩn cấp cho nhân sự trực ca đêm khi phát sinh vé cam kết 24/7.

---

## Phụ lục A: Danh mục Dữ liệu Chuẩn (Data Dictionary)

Phụ lục này mô tả các thực thể nghiệp vụ và thông tin chúng nắm giữ. Cột "Bắt buộc" cho biết thông tin đó có phải luôn có giá trị hay không, ở thời điểm nào.

### A.1 Vé Hỗ trợ (Ticket)

| Thông tin | Kiểu giá trị | Bắt buộc | Ý nghĩa nghiệp vụ |
| --- | --- | :---: | --- |
| Mã số vé | Chuỗi, duy nhất theo doanh nghiệp | Có, hệ thống tự cấp | Định danh trao đổi với khách hàng (FEAT-02) |
| Tiêu đề | Văn bản ngắn | Có | Tóm tắt yêu cầu |
| Mô tả | Văn bản dài | Không | Nội dung chi tiết ban đầu |
| Trạng thái | Tham chiếu tới Trạng thái Vé | Có | Tình trạng xử lý hiện tại (BR-01.2) |
| Mức ưu tiên | Tham chiếu tới danh mục mức ưu tiên do doanh nghiệp cấu hình | Có | Cơ sở áp dụng cam kết dịch vụ |
| Kênh tiếp nhận | Tham chiếu tới Nguồn Tiếp nhận | Có | Nơi yêu cầu đến từ |
| Khách hàng yêu cầu | Tham chiếu tới hồ sơ khách hàng cá nhân | Có | Người gửi yêu cầu (BR-03.1) |
| Doanh nghiệp liên quan | Tham chiếu tới hồ sơ doanh nghiệp | Không | Tổ chức khách hàng thuộc về (BR-03.2) |
| Danh mục phân loại | Đường dẫn danh mục nhiều cấp | Có, khi chuyển sang "đã xử lý xong" | Phân loại tới cấp cuối (BR-04.1) |
| Nhóm phụ trách | Tham chiếu tới nhóm hỗ trợ | Có | Nhóm chịu trách nhiệm xử lý |
| Người phụ trách | Tham chiếu tới tài khoản người dùng | Không | Trống khi vé nằm ở hàng đợi chung (FEAT-34) |
| Chính sách cam kết áp dụng | Tham chiếu tới Chính sách SLA | Có | Ghi lại tại thời điểm tạo vé (BR-40.2) |
| Hạn chót phản hồi đầu tiên | Thời điểm | Có | Tính theo lịch làm việc (FEAT-08) |
| Hạn chót xử lý dứt điểm | Thời điểm | Có | Tính theo lịch làm việc |
| Thời điểm phản hồi đầu tiên | Thời điểm | Không | Trống tới khi có phản hồi công khai đầu tiên |
| Thời điểm xử lý xong | Thời điểm | Không | Căn cứ tính tuân thủ cam kết xử lý |
| Thời điểm đóng hoàn tất | Thời điểm | Không | Kết thúc vòng đời vé |
| Tổng thời gian tạm dừng cam kết | Số phút | Có, mặc định 0 | Trừ khỏi thời gian xử lý khi tính tuân thủ (BR-09.3) |
| Số lần tạm dừng cam kết | Số nguyên | Có, mặc định 0 | Phát hiện lạm dụng tạm dừng (BR-09.4) |
| Tình trạng vi phạm cam kết | Có/Không cho từng mốc cam kết | Có | Kết quả đối chiếu hạn chót |
| Nguyên nhân xử lý | Tham chiếu tới Nguyên nhân Xử lý | Có, khi chuyển sang "đã xử lý xong" | Đầu vào báo cáo nguyên nhân (BR-19.1) |
| Tóm tắt giải pháp | Văn bản dài | Có, khi chuyển sang "đã xử lý xong" | Nội dung tái sử dụng cho bài viết tri thức |
| Số lần mở lại | Số nguyên | Có, mặc định 0 | Theo dõi chất lượng xử lý (BR-21.2) |
| Vé cha | Tham chiếu tới vé khác | Không | Quan hệ sự cố diện rộng (FEAT-28) |
| Vé chính đã gộp vào | Tham chiếu tới vé khác | Không | Có giá trị khi vé này đã bị gộp (FEAT-27) |
| Vé gốc đã tách ra | Tham chiếu tới vé khác | Không | Có giá trị khi vé này được tách ra (FEAT-41) |
| Cơ hội bán hàng liên kết | Tham chiếu tới Cơ hội bán hàng | Không | Liên kết thủ công (FEAT-29) |
| Nhãn | Danh sách nhãn | Không | Gom nhóm linh hoạt (FEAT-23) |
| Trường tùy biến | Tập giá trị theo định nghĩa của doanh nghiệp | Tùy cấu hình | Thông tin đặc thù (FEAT-05) |
| Cờ khách hàng không hài lòng | Có/Không | Có, mặc định Không | Ưu tiên theo dõi (BR-39.2) |
| Điểm hài lòng & nhận xét | Số 1-5 và văn bản | Không | Kết quả khảo sát (FEAT-20) |

### A.2 Các thực thể liên quan

| Thực thể | Thông tin chính | Ý nghĩa nghiệp vụ |
| --- | --- | --- |
| **Trao đổi trên Vé** | Loại nội dung (phản hồi công khai / ghi chú nội bộ / thông báo hệ thống), người gửi, nội dung, danh sách tệp đính kèm, thời điểm | Từng lượt trao đổi hoặc ghi chú, bất biến sau khi tạo (NFR-01) |
| **Tệp đính kèm** | Tên tệp, định dạng, dung lượng, người tải lên, thời điểm, thuộc lượt trao đổi nào | Minh chứng sự cố, tuân theo BR-15.1 đến BR-15.4 |
| **Chính sách SLA** | Tên, phạm vi áp dụng (theo mức ưu tiên / theo hạng khách hàng / theo hợp đồng cụ thể), thời hạn phản hồi đầu tiên, thời hạn xử lý dứt điểm, lịch làm việc áp dụng | Cam kết chất lượng dịch vụ (FEAT-07, FEAT-40) |
| **Lịch Làm việc** | Tên, múi giờ, khung giờ làm việc theo từng ngày trong tuần, danh sách ngày nghỉ lễ | Cơ sở tính hạn chót cam kết (FEAT-08) |
| **Chính sách Leo thang** | Loại mốc áp dụng (sắp tới hạn / đã vi phạm), thời điểm kích hoạt, danh sách hành động thực thi | Phản ứng khi vé sắp hoặc đã vi phạm (FEAT-18) |
| **Nhóm Hỗ trợ** | Tên nhóm, danh sách thành viên, hàng đợi chung của nhóm | Đơn vị điều phối công việc (FEAT-34) |
| **Kỹ năng & Năng lực Tư vấn viên** | Danh sách kỹ năng của từng tư vấn viên, hạn mức số vé tối đa | Cơ sở điều phối theo kỹ năng và hạn mức (FEAT-10, FEAT-11) |
| **Ca trực & Trạng thái Sẵn sàng** | Lịch trực theo người và khung giờ, trạng thái sẵn sàng hiện tại, kỳ nghỉ phép đã duyệt | Xác định ai đủ điều kiện nhận vé (FEAT-35) |
| **Khảo sát Hài lòng** | Điểm 1-5, nhận xét, thời điểm gửi, thời điểm khách hàng phản hồi, tình trạng hết hạn | Đo lường chất lượng phục vụ (FEAT-20) |
| **Câu trả lời Mẫu** | Tiêu đề, nội dung, phạm vi dùng chung hay cá nhân, người tạo | Chuẩn hóa và tăng tốc phản hồi (FEAT-16) |
| **Nhật ký Thay đổi** | Vé liên quan, trường bị thay đổi, giá trị trước, giá trị sau, người thực hiện, thời điểm | Truy vết và đối soát tranh chấp (FEAT-44) |
| **Nhật ký Xuất dữ liệu** | Người xuất, thời điểm, phạm vi dữ liệu, số bản ghi | Kiểm soát rủi ro rò rỉ dữ liệu (BR-44.4) |
| **Phiên Nhập & Xuất Dữ liệu** | Người thực hiện, thời điểm, tình trạng, số dòng thành công, danh sách dòng lỗi kèm lý do | Theo dõi tiến độ chuyển đổi dữ liệu (FEAT-24, FEAT-25) |
| **Yêu cầu Xóa Dữ liệu Cá nhân** | Chủ thể dữ liệu, thời điểm tiếp nhận, hạn đáp ứng, tình trạng, phạm vi vé bị ảnh hưởng, người thực hiện | Bằng chứng tuân thủ Nghị định 13 (FEAT-45) |
| **Vé Khiếu nại** | Vé gốc liên quan, tư vấn viên bị khiếu nại, nội dung khiếu nại, người rà soát, kết luận, thời điểm | Hồ sơ rà soát khiếu nại về chất lượng phục vụ, tách khỏi vé gốc để tư vấn viên bị khiếu nại không xem được (BR-39.3) |
| **Danh mục cấu hình** — Trạng thái Vé, Loại Vé, Nguồn Tiếp nhận, Nguyên nhân Xử lý, Danh mục Phân loại, Mức ưu tiên | Tên hiển thị, thứ tự, màu sắc, tình trạng còn sử dụng, các dấu hiệu nghiệp vụ đi kèm (ví dụ trạng thái nào là kết thúc, trạng thái nào tạm dừng cam kết) | Cho phép mỗi doanh nghiệp tự chuẩn hóa theo quy trình riêng |

---

## Phụ lục B: Tham số Cấu hình theo Doanh nghiệp — Giá trị Mặc định Tham khảo

| Tên tham số cấu hình | Giá trị mặc định | Ý nghĩa nghiệp vụ |
| --- | :---: | --- |
| Hạn mức số vé đang giữ tối đa / tư vấn viên | 10 vé | Ngưỡng dừng phân bổ thêm cho một người (BR-10.1) |
| Hạn mức số vé tối đa riêng cho từng tư vấn viên | Không đặt (kế thừa hạn mức doanh nghiệp) | Cho phép ghi đè hạn mức tối đa cho từng tư vấn viên theo năng lực (BR-10.1, Issue #226) |
| Ngưỡng cảnh báo sắp tới hạn cam kết | 75% thời hạn đã trôi qua | Mốc phát cảnh báo sớm (BR-17.1) |
| Ngưỡng cảnh báo vé tồn đọng trong hàng đợi chung | 15 phút | Thời gian không ai nhận vé thì cảnh báo Trưởng nhóm (BR-34.3) |
| Số lần tạm dừng cam kết tối đa trước khi cảnh báo | 3 lần | Phát hiện lạm dụng tạm dừng (BR-09.4) |
| Thời gian cho phép từ chối vé được bàn giao | 30 phút | Khoảng thời gian người nhận được quyền từ chối (BR-12.4) |
| Số lần chuyển vé tối đa trước khi cảnh báo | 3 lần | Phát hiện vé bị đùn đẩy (BR-12.5) |
| Thời hạn hiệu lực đường dẫn khảo sát hài lòng | 7 ngày | Thời gian khách hàng còn chấm điểm được (BR-20.1) |
| Thời hạn chờ trước khi tự động đóng vé | 48 giờ làm việc | Thời gian chờ khách phản hồi sau khi xử lý xong (BR-22.1) |
| Thời hạn cho phép mở lại vé sau khi xử lý xong | 7 ngày | Quá hạn thì tạo vé mới liên kết (BR-21.3) |
| Thời hạn hoàn tác thao tác gộp vé | 24 giờ | Khoảng thời gian sửa được lỗi gộp nhầm (BR-27.5) |
| Thời hạn hiệu lực đường dẫn tải tệp xuất | 24 giờ | Thời gian tệp báo cáo còn tải được (BR-25.1) |
| Thời hạn lưu vé trong Thùng rác | 30 ngày | Thời gian còn phục hồi được trước khi xóa vĩnh viễn (BR-26.1) |
| Thời hạn lưu trữ vé đã đóng | 36 tháng | Sau đó chuyển sang lưu trữ dài hạn (BR-45.1) |
| Số cấp tối đa của danh mục phân loại | 5 cấp | Độ sâu tối đa khi phân loại nhiều tầng |
| Dung lượng tối đa mỗi tệp đính kèm | 25 MB | Giới hạn tệp khách hàng và tư vấn viên gửi kèm (BR-15.1) |
| Số tệp đính kèm tối đa mỗi lượt gửi | 20 tệp | Giới hạn số tệp trong một lượt trao đổi (BR-15.1) |
| Dung lượng tối đa tệp nhập dữ liệu | 50 MB | Giới hạn tệp chuyển đổi dữ liệu (BR-24.1) |
| Số vé tối đa mỗi lượt thao tác hàng loạt | 500 vé | Giới hạn phạm vi một lần thao tác (BR-23.1) |
| Cấp thấp nhất được phép gộp vé | Support Manager | Doanh nghiệp mở rộng xuống Team Lead được; quyền hoàn tác không hạ thấp (BR-27.7) |
| Thời gian chờ người đủ kỹ năng trước khi hạ tiêu chuẩn | 15 phút | Áp dụng cho cách ứng xử (c) của BR-11.4 — khác với ngưỡng cảnh báo tồn đọng của BR-34.3 |
| Ngưỡng cảnh báo tuyệt đối tối thiểu | Không đặt | Tùy chọn, dùng kèm ngưỡng 75% cho cam kết rất ngắn; khi đặt cả hai, mốc nào đến trước thì cảnh báo (BR-17.1) |
| Các mốc leo thang sau vi phạm | Ngay khi vi phạm, sau 1 giờ, sau 4 giờ | Chuỗi mốc kích hoạt hành động leo thang (BR-18.1) |
| Số cấp quản lý báo lên khi leo thang | 1 cấp | Số cấp tính từ người phụ trách theo sơ đồ tổ chức (BR-18.2) |
| Thời gian không thao tác trước khi gỡ cảnh báo trùng | 2 phút | Sau khoảng này coi như người đó đã rời khỏi vé (BR-38.2) |
| Cửa sổ cảnh báo ghi đè cùng một trường | 60 giây | Hai thao tác cách nhau trong khoảng này thì báo cho người sau (BR-38.4) |
| Số lần thử lại khi gửi thông báo thất bại | 3 lần | Hết số lần này thì ghi nhận trên vé và báo Trưởng nhóm (BR-37.5) |
| Thời hạn xử lý yêu cầu về dữ liệu cá nhân | 72 giờ thực | Tính theo giờ thực, không theo giờ làm việc, vì là nghĩa vụ pháp lý (BR-45.4) |
| Mốc cảnh báo trước hạn xử lý yêu cầu dữ liệu cá nhân | Còn 24 giờ và còn 6 giờ | Hai lần nhắc người phụ trách (BR-45.4) |
| Thời gian nhắc khách hàng trước khi tự động đóng vé | 12 giờ làm việc | Khoảng cách trước mốc đóng tự động để gửi nhắc (BR-22.3) |

*Ghi chú:* Đây là các giá trị mặc định khuyến nghị. Mỗi doanh nghiệp điều chỉnh lại theo cam kết dịch vụ và quy mô vận hành của mình khi triển khai. Các tham số thuộc những tính năng chưa xây dựng (xem mục 7) sẽ có hiệu lực khi tính năng tương ứng hoàn thành.
