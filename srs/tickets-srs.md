# SRS — Phân hệ Quản lý Vé Hỗ trợ Khách hàng, Dịch vụ CSKH & Cam kết Chất lượng SLA (Tickets & Customer Support Management)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA |
| **Module** | CRM — Phân hệ Quản lý Vé Hỗ trợ Khách hàng, Dịch vụ CSKH & Cam kết Chất lượng SLA (Tickets & Customer Support Management) |
| **Ngày cập nhật** | 2026-10-02 |
| **Phiên bản** | v8.4 (Vé giữ chỗ luôn ở đơn vị tiếp nhận; tạm dừng xóa theo vé hoặc theo khách) — v8.3 (Người phụ trách của dòng nhập phải thuộc đơn vị tiếp nhận; nội dung chép từ hội thoại luôn ở dạng che hạn chế nhất; điều kiện người bị khiếu nại áp cho mọi đường nhận vé) — v8.2 (Lọc sẵn người nhận đề nghị chuyển; chuyển phân hệ của hộp thư là một thao tác; danh sách hàng đợi được chuyển tới mặc định trống; tách thẩm quyền bước xử lý vé trong quy trình chung) — v8.1 (Bàn giao bằng đề nghị chuyển cho người chỉ có quyền gán vé của mình; vé từ hội thoại theo đơn vị tiếp nhận hiện tại; mỗi hộp thư thuộc một phân hệ; đổi đơn vị chính là bước bắt buộc) — v8.0 (Chuẩn hóa Nghiệp vụ Thuần túy — Thay thế v7.2; đồng bộ mô hình phân quyền ô × mức của phân hệ Phân quyền, khai báo hàng đợi và đơn vị tiếp nhận, khai báo trường nhạy cảm của vé, thực thi quyền xóa dữ liệu cá nhân theo khách hàng) |
| **Neo mã nguồn** | Chưa xác định — tài liệu đặc tả trạng thái nghiệp vụ mục tiêu, không neo vào một phiên bản triển khai cụ thể |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`contacts-srs.md`](./contacts-srs.md), [`omnichat-srs.md`](./omnichat-srs.md), [`deals-pipeline-srs.md`](./deals-pipeline-srs.md), [`tasks-srs.md`](./tasks-srs.md), [`object-manager-srs.md`](./object-manager-srs.md), [ADR-0006](../docs/adr/0006-sla-operating-hours-and-business-calendar-strategy.md), [ADR-0008](../docs/adr/0008-data-subject-deletion-contract-omnichat-tickets.md), [ADR-0009](../docs/adr/0009-access-level-per-action-and-record-type.md), [ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md) |

## Ghi chú về phiên bản v8.0

Tài liệu này đặc tả **trạng thái nghiệp vụ mục tiêu** của phân hệ Vé hỗ trợ — điều doanh nghiệp cần hệ thống làm đúng, không phải điều hệ thống đang làm. Mọi quy tắc ở Mục 3 là yêu cầu bắt buộc như nhau; nơi nào hệ thống làm khác tài liệu, hệ thống phải được sửa theo tài liệu. Tài liệu không dùng nhãn trạng thái triển khai; những nhu cầu chưa chốt được phương án nghiệp vụ được gom tại Mục 7.

So với v7.2, phiên bản này thay đổi về bản chất ở bốn điểm:

1. **Quyền viết theo mô hình chung của phân hệ Phân quyền.** Quyền trên vé là các ô *loại dữ liệu × thao tác* mang một Mức truy cập, cộng các quyền quản trị của phân hệ; không năng lực nào gắn cứng cho một tên vai trò. Vai trò dựng sẵn và mức mặc định dẫn chiếu [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`.
2. **Vé là bản ghi công việc của hàng đợi.** Mỗi kênh, hộp thư, biểu mẫu và nguồn tạo vé khai báo đơn vị tiếp nhận; vé ở lại đơn vị tiếp nhận suốt vòng đời; nhận việc là thao tác Gán; chuyển hàng đợi có quy tắc riêng.
3. **Trường nhạy cảm của vé** được khai báo theo khung che dữ liệu chung, và việc xóa dữ liệu cá nhân trên vé được thực thi theo yêu cầu của khách hàng, trả kết quả về biên bản chung.
4. **Mỗi tính năng có bảng Tiêu chí Chấp nhận** và mỗi quy tắc có lý do nghiệp vụ; mọi giá trị doanh nghiệp có thể muốn khác là tham số tại Phụ lục B; mọi quyết định đã chốt ghi tại Phụ lục C.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả nghiệp vụ quản trị Vé hỗ trợ khách hàng, Cam kết chất lượng dịch vụ (SLA) và Đánh giá sự hài lòng (CSAT) trong hệ thống CRM B2B SaaS:

1. **Tạo & Quản lý Vé Hỗ trợ:** tư vấn viên tạo vé, hoặc vé được tạo từ các kênh đã kết nối, để theo dõi một yêu cầu hỗ trợ từ lúc tiếp nhận tới lúc xử lý xong.
2. **Cấp Mã Định danh Vé Duy nhất:** mỗi vé được cấp một mã số duy nhất, tuần tự theo từng doanh nghiệp (ví dụ `TKT-00421`).
3. **Cam kết Chất lượng Dịch vụ:** kiểm soát hai mốc thời hạn — phản hồi đầu tiên và xử lý dứt điểm — theo lịch làm việc riêng của từng doanh nghiệp.
4. **Tạm dừng Đồng hồ Cam kết Công bằng:** trạng thái "chờ bên khác" khiến đồng hồ xử lý dứt điểm tạm ngưng, tránh phạt oan tư vấn viên.
5. **Hàng đợi, Phân công & Điều phối:** vé vào hàng đợi của đơn vị tiếp nhận, được nhận việc, phân bổ tự động hoặc gán thủ công, bàn giao và chuyển hàng đợi.
6. **Không gian Làm việc của Tư vấn viên:** trao đổi với khách hàng, ghi chú nội bộ, đính kèm tài liệu.
7. **Cảnh báo & Leo thang:** cảnh báo trước khi hết hạn, leo thang khi đã vi phạm.
8. **Khảo sát Sự Hài lòng:** gửi khảo sát 1-5 sao sau khi vé được xử lý xong.
9. **Gộp Vé, Tách Vé & Sự cố Diện rộng.**
10. **Liên kết & Cảnh báo Rủi ro tới Bộ phận Bán hàng.**
11. **Báo cáo & Giám sát Hiệu suất.**
12. **Truy vết & Bảo vệ Dữ liệu Cá nhân:** nhật ký thay đổi bất biến; vòng đời dữ liệu, khử định danh và thực thi yêu cầu xóa dữ liệu cá nhân theo pháp luật áp dụng cho doanh nghiệp.

### 1.2 Phạm vi

**Trong phạm vi — 12 nhóm chức năng:**

| Nhóm | Nội dung |
| --- | --- |
| A. Quản trị Vé Cơ bản | Tạo mới, chỉnh sửa, trạng thái cấu hình được, mã định danh, liên kết khách hàng |
| B. Phân loại & Tùy biến | Danh mục phân loại nhiều cấp, trường tùy biến, ma trận tác động × khẩn cấp |
| C. Cam kết Chất lượng Dịch vụ | Đồng hồ hai mốc, lịch làm việc, tạm dừng đồng hồ, chính sách theo hạng khách hàng và hợp đồng |
| D. Hàng đợi, Phân công & Điều phối | Hàng đợi và đơn vị tiếp nhận, nhận việc, phân bổ xoay vòng có hạn mức, phân bổ theo kỹ năng, bàn giao, chuyển hàng đợi, ca trực, chuyển vé hàng loạt |
| E. Tác nghiệp Xử lý & Phản hồi | Dòng trao đổi, ghi chú nội bộ, đính kèm, câu trả lời mẫu, thông báo, cảnh báo trùng thao tác |
| F. Leo thang & Cảnh báo Vi phạm | Cảnh báo sớm, leo thang tự động, leo thang thủ công và khiếu nại |
| G. Đóng Vé & Khảo sát Hài lòng | Ghi nhận nguyên nhân xử lý, khảo sát, mở lại, tự động đóng |
| H. Hàng loạt, Nhập/Xuất & Thùng rác | Gắn nhãn hàng loạt, nhập, xuất, xóa mềm và phục hồi |
| I. Quy trình Nâng cao & Rủi ro Khách hàng | Gộp, tách, vé cha – vé con, liên kết và cảnh báo sang cơ hội bán hàng, bài viết tri thức, giám sát hướng dẫn, giờ tính phí |
| J. Báo cáo & Giám sát Hiệu suất | Bảng điều khiển hàng đợi, báo cáo tuân thủ, hiệu suất, khối lượng |
| K. Nhật ký Thao tác & Truy vết | Nhật ký thay đổi các trường quan trọng và nhật ký xuất dữ liệu |
| L. Vòng đời Dữ liệu & Bảo vệ Dữ liệu Cá nhân | Lưu trữ dài hạn, khử định danh, thực thi yêu cầu xóa theo khách hàng |

**Ngoài phạm vi (thuộc tài liệu khác):**

- Mô hình vai trò, mức truy cập, đơn vị tổ chức, hàng đợi ở mức chung, tạm ngưng và rời workspace, khung che dữ liệu — thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md). Tài liệu này chỉ khai báo phần thuộc loại dữ liệu Vé hỗ trợ.
- Trực chat và tiếp nhận đa kênh — thuộc [`omnichat-srs.md`](./omnichat-srs.md). Tài liệu này quy định vé được tạo từ một hội thoại ra sao.
- Hồ sơ khách hàng, mức hiển thị trường liên hệ và quy trình quyền chủ thể dữ liệu — thuộc [`contacts-srs.md`](./contacts-srs.md).
- Cơ hội và phễu bán hàng, hiển thị cảnh báo trên thẻ cơ hội — thuộc [`deals-pipeline-srs.md`](./deals-pipeline-srs.md).
- Soạn, duyệt nội dung và xuất bản cơ sở tri thức; quản lý hợp đồng và lập hóa đơn; quy trình xin và duyệt nghỉ phép. Tài liệu này chỉ đặc tả phần giao tiếp với các nghiệp vụ đó (`FEAT-31`, `FEAT-33`, `FEAT-35`, `FEAT-40`).

### 1.3 Đối tượng đọc

- **Product Owner / Business Analyst:** căn cứ quản lý backlog, tiêu chí nghiệm thu và thiết kế quy trình hỗ trợ khách hàng.
- **Đội phát triển:** căn cứ hiện thực hành vi nghiệp vụ; cách tổ chức dữ liệu thuộc thẩm quyền đội phát triển.
- **Đội kiểm thử:** căn cứ thiết kế kiểm thử đồng hồ cam kết, hàng đợi, phân quyền và gộp vé.
- **Trưởng phòng Dịch vụ Khách hàng & Giám đốc Vận hành:** căn cứ thiết lập chính sách cam kết, giám sát hiệu suất, báo cáo cho khách hàng.

### 1.4 Thuật ngữ & Viết tắt

Thuật ngữ về phân quyền — Mức truy cập (**Không có / Chỉ của mình / Đơn vị của mình / Đơn vị và các đơn vị con / Toàn workspace**), Bản ghi của mình, Bản ghi thuộc một đơn vị, Sàn bắt buộc, Người phụ trách đơn vị, Đơn vị chính, Đơn vị kiêm nhiệm, Người có toàn quyền, Thu hẹp và Nới rộng quyền — dùng đúng định nghĩa tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4; tài liệu này không định nghĩa lại.

| Thuật ngữ | Định nghĩa nghiệp vụ |
| --- | --- |
| **Vé Hỗ trợ (Ticket)** | Bản ghi một yêu cầu hỗ trợ, phản ánh sự cố hoặc câu hỏi của khách hàng cần được tiếp nhận và xử lý. Vé là **bản ghi công việc** của hàng đợi theo nghĩa của phân hệ Phân quyền. |
| **Hàng đợi vé** | Nơi chứa vé của một **đơn vị tiếp nhận**; mọi thành viên của đơn vị đó thấy các vé chưa có người phụ trách và nhận việc được. |
| **Đơn vị tiếp nhận** | Đơn vị tổ chức (thường là một đội hỗ trợ) mà vé thuộc về suốt vòng đời, xác định từ nguồn tạo vé hoặc từ lượt chuyển hàng đợi gần nhất. |
| **Nguồn tạo vé** | Nơi vé phát sinh: hộp thư hỗ trợ, kênh hội thoại, biểu mẫu hỗ trợ, tích hợp với hệ thống ngoài, tạo thủ công, nhập từ tệp, tách vé, tạo lại sau hạn mở lại. |
| **Nhận việc** | Tư vấn viên tự gán mình làm Người phụ trách của một vé chưa có người phụ trách trong hàng đợi. Đây là một lượt Gán người phụ trách. |
| **Trưởng nhóm** | Người phụ trách (chính hoặc đồng phụ trách) của đơn vị tiếp nhận mà vé đang thuộc. Là **người nhận thông báo và cảnh báo**, không phải một vai trò quyền. |
| **Trưởng phòng Dịch vụ Khách hàng** | Người phụ trách của đơn vị cấp trên gần nhất của đơn vị tiếp nhận. Là người nhận cảnh báo dự phòng, không phải một vai trò quyền. |
| **Tư vấn viên** | Thành viên của một đơn vị tiếp nhận làm việc trên vé. |
| **Cam kết Phản hồi Đầu tiên** | Thời hạn tối đa từ lúc tạo vé đến khi có phản hồi công khai đầu tiên gửi tới khách hàng. |
| **Cam kết Xử lý Dứt điểm** | Thời hạn tối đa từ lúc tạo vé đến khi vé lần đầu chuyển sang trạng thái kết thúc dạng "đã xử lý xong". |
| **Tạm dừng cam kết** | Đồng hồ xử lý dứt điểm đóng băng khi vé ở trạng thái doanh nghiệp đánh dấu "không tính giờ cam kết". |
| **Kết thúc vé** | Chuyển vé sang trạng thái kết thúc dạng "đã xử lý xong" hoặc mở lại một vé đang ở trạng thái đó. Là một thao tác riêng trên vé, tách khỏi thao tác Sửa, vì nó thay đổi kết quả cam kết của vé. |
| **Ghi chú Nội bộ** | Trao đổi giữa nhân viên trên vé; khách hàng không bao giờ thấy. |
| **Vé Cha – Vé Con** | Mô hình sự cố diện rộng: nhiều vé của các khách hàng khác nhau đặt dưới một vé cha đại diện cho sự cố (`FEAT-28`). |
| **Gộp Vé / Tách Vé** | Gộp: hợp nhất vé trùng lặp (vé phụ) vào vé chính. Tách: chuyển một phần nội dung sang vé mới. |
| **Vé Khiếu nại** | Hồ sơ rà soát khiếu nại về chất lượng phục vụ của một tư vấn viên, tách khỏi vé gốc (`BR-39.3`); là một loại dữ liệu riêng về quyền. |
| **Trạng thái Sẵn sàng** | Tình trạng tư vấn viên tự khai báo trong ca, cho biết có nhận việc mới hay không. |
| **Khử định danh** | Thay thông tin định danh cá nhân trên vé bằng dấu hiệu đã ẩn danh, giữ số liệu thống kê phi định danh. |
| **Nhật ký Thay đổi** | Bản ghi bất biến về ai đã đổi gì trên vé và lúc nào. |

### 1.5 Tài liệu tham khảo

- [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) — mức truy cập, vai trò dựng sẵn, hàng đợi và đơn vị tiếp nhận, thứ tự hợp nhất quyền, khung che dữ liệu, tạm ngưng và rời workspace.
- [`contacts-srs.md`](./contacts-srs.md) — hồ sơ khách hàng, nhóm trường nhạy cảm, quyền chủ thể dữ liệu và Biên bản Hoàn tất Xử lý.
- [ADR-0006](../docs/adr/0006-sla-operating-hours-and-business-calendar-strategy.md) — lịch làm việc dùng tính cam kết.
- [ADR-0008](../docs/adr/0008-data-subject-deletion-contract-omnichat-tickets.md) — hợp đồng thực thi quyền xóa theo khách hàng giữa Khách hàng, Hội thoại Đa kênh và Vé hỗ trợ.
- [ADR-0009](../docs/adr/0009-access-level-per-action-and-record-type.md) — mức truy cập theo từng thao tác trên từng loại dữ liệu.
- [ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md) — thao tác thu hẹp quyền không bị chặn khi nhật ký gặp sự cố.

### 1.6 Giả định & Phụ thuộc

Các tính năng chỉ vận hành đúng khi những điều kiện dưới đây được đáp ứng. Đây là căn cứ xác định trách nhiệm khi một tính năng không hoạt động vì nguyên nhân nằm ngoài phân hệ Vé hỗ trợ.

| Phụ thuộc | Quy tắc cần tới | Hệ quả nếu chưa sẵn sàng |
| --- | --- | --- |
| Gửi thư điện tử hoạt động | `BR-20.2`, `BR-22.3`, `BR-27.3`, `BR-37.2`, `BR-43.8` | Khách hàng không nhận khảo sát và thông báo; cách xử lý khi gửi thất bại theo `BR-37.5` |
| Kênh tiếp nhận đa kênh còn kết nối | `BR-22.3`, `BR-27.3`, `BR-41.4` | Không gửi được thông báo về đúng kênh khách hàng đã dùng; chuyển kênh dự phòng theo `BR-37.5` |
| Danh mục ngày nghỉ lễ được khai báo cho năm đang xét | `BR-08.4`, `BR-08.6` | Hệ thống cảnh báo người có quyền Cấu hình cam kết dịch vụ khi năm chưa khai báo |
| Sơ đồ tổ chức khai báo quản lý trực tiếp và người phụ trách đơn vị | `BR-18.2`, `BR-18.4`, `BR-34.3` | Không xác định được người nhận cảnh báo; rơi về người nhận dự phòng (`CFG-18-03`) |
| Kênh gửi tin nhắn hoặc gọi tự động | `BR-37.4` | Cảnh báo ngoài giờ không đánh thức được người trực; cam kết phục vụ liên tục không thực hiện được |
| Hồ sơ và hạng khách hàng (phân hệ Khách hàng) | `BR-03.1`, `BR-03.2`, `BR-40.1` | Không áp dụng được cam kết theo hạng khách hàng |
| Thông tin hợp đồng dịch vụ (phân hệ Hợp đồng) | `BR-40.1` tầng (1), `BR-33.2`, `BR-43.7` | Không áp dụng được cam kết riêng theo hợp đồng; vé rơi xuống tầng (2) hoặc (3) của `BR-40.1` |
| Kỳ nghỉ phép đã duyệt từ quy trình nhân sự | `BR-35.4` | Vé vẫn được giao cho người đang nghỉ |
| Quy trình quyền chủ thể dữ liệu của phân hệ Khách hàng | `BR-45.9`, `BR-45.10` | Yêu cầu xóa không được khởi động theo khách hàng; Biên bản Hoàn tất Xử lý phải nêu phần chưa phủ |
| Tiến trình nền chạy đúng lịch | `NFR-04`, `NFR-07`, `FEAT-17`, `FEAT-18`, `FEAT-22` | Cảnh báo, leo thang và đóng vé tự động không kích hoạt đúng thời điểm |

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà phân hệ giải quyết

1. **Yêu cầu hỗ trợ thiếu đầu mối theo dõi:** không có bản ghi vé tập trung, nhân viên bỏ sót hoặc mất dấu tiến độ xử lý.
2. **Vi phạm cam kết không được phát hiện sớm:** khách hàng quan trọng gặp sự cố nghiêm trọng nhưng vé bị trôi, không có cảnh báo trước khi hết hạn.
3. **Đồng hồ cam kết bất công khi chờ khách hàng:** khách không trả lời mà đồng hồ vẫn chạy, nhân viên bị đánh giá vi phạm oan.
4. **Phân bổ vé không đồng đều:** một người bị dồn quá nhiều vé trong khi người khác trống việc.
5. **Nhiều khách hàng báo cùng một sự cố:** vé trùng lặp khó theo dõi nếu không gộp hoặc nhóm dưới một đầu mối.
6. **Không thu được đánh giá thực tế từ khách hàng.**
7. **Hỗ trợ và bán hàng không đồng bộ:** nhân viên kinh doanh thúc ép ký hợp đồng trong khi khách đang có sự cố chưa giải quyết.
8. **Không đo lường được chất lượng dịch vụ:** không có số liệu tin cậy về tuân thủ cam kết và hiệu suất để đánh giá nhân sự, xếp lịch trực, báo cáo cho khách hàng.
9. **Gián đoạn khi nhân sự vắng mặt:** người nghỉ ốm, nghỉ phép, nghỉ việc để lại vé xử lý dở trôi qua hạn cam kết.
10. **Rủi ro pháp lý về dữ liệu cá nhân:** vé chứa nhiều dữ liệu cá nhân; doanh nghiệp phải đáp ứng yêu cầu xóa và kiểm soát việc đưa dữ liệu ra ngoài.
11. **Lặp lại cùng loại yêu cầu & đào tạo nhân sự mới chậm.**
12. **Không đo được chi phí phục vụ từng khách hàng** với hợp đồng tính phí theo giờ.
13. **Vé không ai thấy hoặc ai cũng thấy:** nếu không quy định vé thuộc đội nào, vé mới chưa gán không có ai nhìn thấy, hoặc ngược lại dữ liệu khách hàng của đội này lộ sang đội khác.

### 2.2 Vai trò người dùng

**Vai trò thao tác (là các cột của Ma trận tại Mục 5):**

| Vai trò | Trách nhiệm nghiệp vụ |
| --- | --- |
| **Khách hàng** | Người yêu cầu, ở ngoài workspace. Trao đổi qua các kênh đã kết nối, nhận thông báo, chấm điểm khảo sát. |
| **Nhân viên Hỗ trợ** | Vai trò dựng sẵn theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`, gán cho tư vấn viên tuyến đầu. Thấy vé của đơn vị mình, nhận việc từ hàng đợi, xử lý, phản hồi, ghi chú, bàn giao vé mình đang giữ. |
| **Quản lý** | Vai trò dựng sẵn theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`, gán cho trưởng nhóm hỗ trợ và trưởng phòng Dịch vụ Khách hàng. Phạm vi do đơn vị mà người đó thuộc quyết định: đặt ở đội thì quản lý vé của đội; đặt ở phòng là đơn vị cấp trên của các đội thì quản lý vé của mọi đội trong phòng. |
| **Nhân viên Kinh doanh** | Vai trò dựng sẵn; xem vé liên quan tới cơ hội mình phụ trách để phối hợp chăm sóc (`BR-29.4`), nhận cảnh báo rủi ro trên cơ hội (`FEAT-30`). |
| **Người có toàn quyền** | Chủ sở hữu và Quản trị viên — cấp bậc thành viên, không phải vai trò. Đặt đơn vị tiếp nhận của nguồn, danh sách hàng đợi được chuyển tới, và mọi cấu hình của phân hệ. |
| **Hệ thống** | Đếm ngược đồng hồ cam kết, tạm dừng và chạy tiếp đồng hồ, phân bổ tự động, phát hiện cảnh báo và vi phạm, gửi khảo sát, đóng vé tự động, ghi nhật ký. |

Các vai trò dựng sẵn khác (Quản lý Marketing, Marketing, Chỉ xem bản ghi được giao, Kiểm toán, Kiểm toán quyền) nhận mức mặc định trên loại dữ liệu Vé hỗ trợ tại Mục 5.1. Doanh nghiệp tự tạo vai trò (ví dụ một vai trò riêng cho trưởng phòng) hoặc điều chỉnh ô của vai trò dựng sẵn theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-25`, `CFG-29-02`.

**Thành viên được cấp quyền quản trị của phân hệ:** người giữ một hoặc vài quyền quản trị tại Mục 5.2 (ví dụ Cấu hình cam kết dịch vụ). Chỉ làm được đúng phần quyền đó, trong trần năng lực của mình.

**Chức danh trách nhiệm:** Người phụ trách Bảo vệ Dữ liệu theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-47` — giám sát việc thực thi yêu cầu xóa dữ liệu cá nhân trên vé (`FEAT-45`).

> **Ghi chú:** "Trưởng nhóm", "Trưởng phòng Dịch vụ Khách hàng" và "Tư vấn viên" trong tài liệu là **cách gọi theo vị trí** dùng để chỉ người nhận thông báo và để kể kịch bản (Mục 1.4). Điều một người làm được trên vé luôn do ô và quyền quản trị của họ quyết định (Mục 5), không do cách gọi.

### 2.3 Quy ước thời gian nghiệp vụ

- **Thời hạn cam kết** (phản hồi đầu tiên, xử lý dứt điểm, cảnh báo sớm, mốc leo thang, thời gian chờ trước khi tự động đóng, nhắc trước khi đóng) tính bằng **giờ làm việc** theo lịch làm việc gắn với chính sách cam kết của vé (`FEAT-08`), theo múi giờ của lịch đó (`BR-08.3`), không theo múi giờ máy chủ hay của người xem.
- **Các thời hạn khác** (hạn mở lại, hiệu lực khảo sát, thời gian lưu Thùng rác, thời hạn lưu trữ, hạn hoàn tác gộp, hiệu lực đường dẫn tải, hạn xử lý yêu cầu xóa dữ liệu cá nhân) tính bằng **thời gian thực liên tục**; thời hạn tính bằng ngày kết thúc lúc 23:59:59 của ngày cuối theo múi giờ workspace; 1 tháng = 30 ngày — theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 2.3.
- **Ngưỡng ngắn tính bằng phút** (tồn đọng hàng đợi, từ chối bàn giao, chờ người đủ kỹ năng, cửa sổ cảnh báo ghi đè) trôi liên tục.
- Mọi thời điểm hiển thị theo múi giờ cá nhân của người xem; thời điểm đến hạn tính theo quy ước trên.

**Lý do nghiệp vụ:** một vé phải quá hạn cùng một lúc với tư vấn viên, trưởng nhóm, khách hàng và người kiểm thử; nếu mỗi người tính theo múi giờ của mình, cam kết không nghiệm thu được và không đối soát được với khách hàng.

### 2.4 Nguyên tắc nghiệp vụ nền tảng

**Nguyên tắc 1 — Ma trận quyết định *có quyền hay không*; quy tắc quyết định *điều kiện bên trong quyền đó*.** Mục 5 là nguồn duy nhất về ô và quyền quản trị mỗi tính năng cần. Quy tắc nghiệp vụ không gắn năng lực cho một tên vai trò; dòng "Vai trò sử dụng chính" trong mỗi tính năng chỉ mang tính mô tả.

**Nguyên tắc 2 — Vé ở lại đơn vị tiếp nhận.** Vé thuộc đơn vị tiếp nhận suốt vòng đời, không đi theo người phụ trách; chỉ đổi đơn vị khi được chuyển hàng đợi (`FEAT-34`).

**Nguyên tắc 3 — Hệ thống không làm thay điều người nhận không được làm.** Phân bổ tự động, bàn giao, chuyển hàng loạt và leo thang tự động chỉ giao vé cho người mà ô của chính họ cho phép xử lý vé đó.

**Nguyên tắc 4 — Cam kết là với khách hàng.** Việc nội bộ đổi người, đổi đội, chờ trong hàng đợi hay chờ người đủ kỹ năng không làm dừng, không làm lại từ đầu đồng hồ cam kết; chỉ trạng thái "chờ bên khác" mà doanh nghiệp đã đánh dấu mới tạm dừng đồng hồ xử lý dứt điểm.

**Nguyên tắc 5 — Thu hồi quyền không chờ việc khác.** Tạm ngưng, rời workspace, thu hẹp quyền luôn thực hiện ngay, kể cả khi nhật ký gặp sự cố (ghi bù theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.6`, ADR-0010); việc xử lý vé đang mở của người đó là bước riêng sau đó.

**Nguyên tắc 6 — Mọi thay đổi quan trọng đều có vết** (`FEAT-44`).

### 2.5 Bảng tổng hợp tính năng nghiệp vụ

| Nhóm | Mã FEAT | Tên tính năng nghiệp vụ |
| --- | --- | --- |
| **A. Quản trị Vé Cơ bản** | `FEAT-01` | Tạo mới & Quản lý Vé Hỗ trợ |
| | `FEAT-02` | Cấp Mã Định danh Vé Duy nhất theo Doanh nghiệp |
| | `FEAT-03` | Liên kết Khách hàng Cá nhân & Doanh nghiệp |
| **B. Phân loại & Tùy biến** | `FEAT-04` | Danh mục Phân loại Nhiều Cấp |
| | `FEAT-05` | Trường Dữ liệu Tùy biến cho Vé Hỗ trợ |
| | `FEAT-06` | Ma trận Đánh giá Mức độ Tác động & Khẩn cấp |
| **C. Cam kết Chất lượng Dịch vụ** | `FEAT-07` | Đồng hồ Cam kết Hai Mốc |
| | `FEAT-08` | Lịch Làm việc Doanh nghiệp Cấu hình được |
| | `FEAT-09` | Tự động Tạm dừng Đồng hồ Cam kết theo Trạng thái Vé |
| | `FEAT-40` | Chính sách Cam kết theo Hạng Khách hàng & Hợp đồng Dịch vụ |
| **D. Hàng đợi, Phân công & Điều phối** | `FEAT-34` | Hàng đợi Vé, Đơn vị Tiếp nhận & Nhận việc |
| | `FEAT-10` | Phân bổ Tự động Xoay vòng theo Hạn mức Năng lực |
| | `FEAT-11` | Phân bổ theo Kỹ năng Chuyên môn |
| | `FEAT-12` | Bàn giao & Phân công Lại Vé |
| | `FEAT-35` | Trạng thái Sẵn sàng & Ca trực của Tư vấn viên |
| | `FEAT-36` | Chuyển Vé Hàng loạt khi Nhân sự Vắng mặt, Tạm ngưng hoặc Rời đi |
| **E. Tác nghiệp Xử lý & Phản hồi** | `FEAT-13` | Dòng Trao đổi Vé Hỗ trợ |
| | `FEAT-14` | Ghi chú Nội bộ giữa Nhân viên |
| | `FEAT-15` | Đính kèm Tài liệu & Hình ảnh |
| | `FEAT-16` | Thư viện Câu trả lời Mẫu |
| | `FEAT-37` | Thông báo cho Tư vấn viên |
| | `FEAT-38` | Cảnh báo Trùng Thao tác |
| **F. Leo thang & Cảnh báo Vi phạm** | `FEAT-17` | Cảnh báo Sớm Nguy cơ Vi phạm Cam kết |
| | `FEAT-18` | Tự động Leo thang khi Vi phạm Cam kết |
| | `FEAT-39` | Leo thang Thủ công & Xử lý Khiếu nại về Chất lượng Phục vụ |
| **G. Đóng Vé & Khảo sát Hài lòng** | `FEAT-19` | Kết thúc Vé & Ghi nhận Nguyên nhân Xử lý |
| | `FEAT-20` | Khảo sát Đánh giá Sự Hài lòng 1-5 Sao |
| | `FEAT-21` | Mở lại Vé đã Xử lý Xong |
| | `FEAT-22` | Tự động Đóng Vé sau Thời gian Không Phản hồi |
| **H. Hàng loạt, Nhập/Xuất & Thùng rác** | `FEAT-23` | Gắn Nhãn Hàng loạt |
| | `FEAT-24` | Nhập Dữ liệu Vé từ Tệp |
| | `FEAT-25` | Xuất Dữ liệu Vé có Kiểm soát |
| | `FEAT-26` | Thùng rác Vé & Phục hồi |
| **I. Quy trình Nâng cao & Rủi ro Khách hàng** | `FEAT-27` | Gộp Vé Trùng lặp |
| | `FEAT-41` | Tách Vé khi Một Yêu cầu Chứa Nhiều Vấn đề |
| | `FEAT-28` | Sự cố Diện rộng theo Mô hình Vé Cha – Vé Con |
| | `FEAT-29` | Liên kết Vé với Cơ hội Bán hàng |
| | `FEAT-30` | Cảnh báo Rủi ro Tự động sang Cơ hội Bán hàng |
| | `FEAT-31` | Chuyển Giải pháp thành Bản nháp Bài viết Tri thức |
| | `FEAT-32` | Giám sát Trực tiếp & Hướng dẫn Hậu trường |
| | `FEAT-33` | Theo dõi Thời lượng Hỗ trợ & Giờ Tính phí |
| **J. Báo cáo & Giám sát Hiệu suất** | `FEAT-42` | Bảng điều khiển Hàng đợi Thời gian thực |
| | `FEAT-43` | Báo cáo Tuân thủ Cam kết, Hiệu suất & Khối lượng |
| **K. Nhật ký & Truy vết** | `FEAT-44` | Nhật ký Thay đổi & Truy vết Thao tác trên Vé |
| **L. Vòng đời Dữ liệu & Bảo vệ Dữ liệu Cá nhân** | `FEAT-45` | Lưu trữ, Khử định danh & Thực thi Yêu cầu Xóa Dữ liệu Cá nhân |

### 2.6 Mục tiêu kinh doanh & Chỉ số thành công

| Mã | Vấn đề (Mục 2.1) | Chỉ số đo lường | Giá trị mục tiêu | Tính năng đóng góp |
| --- | --- | --- | --- | --- |
| `KPI-01` | 1, 2 | Tỷ lệ tuân thủ cam kết Phản hồi Đầu tiên (%) | Theo hợp đồng của doanh nghiệp (khuyến nghị ≥ 95%) | `FEAT-07`, `08`, `10`, `17`, `34`, `35` |
| `KPI-02` | 2 | Tỷ lệ tuân thủ cam kết Xử lý Dứt điểm (%) | Theo hợp đồng (khuyến nghị ≥ 90%) | `FEAT-07`, `08`, `09`, `18`, `40` |
| `KPI-03` | 6 | Điểm hài lòng trung bình (1-5) | Theo hợp đồng (khuyến nghị ≥ 4,5) | `FEAT-13`, `16`, `20` |
| `KPI-04` | 1 | Tỷ lệ vé bị mở lại sau khi đã xử lý xong (%) | Theo hợp đồng (khuyến nghị < 6%) | `FEAT-19`, `21` |
| `KPI-05` | 4 | Chênh lệch khối lượng vé giữa các tư vấn viên cùng đơn vị tiếp nhận và ca | Khuyến nghị ≤ 20% so với trung bình | `FEAT-10`, `11`, `34`, `35`, `36` |
| `KPI-06` | 1, 13 | Số vé nằm trong hàng đợi quá ngưỡng tồn đọng mà chưa có người nhận | Khuyến nghị = 0 trong giờ làm việc | `FEAT-34`, `35`, `42` |
| `KPI-07` | 5 | Thời gian trung bình để phản hồi toàn bộ khách hàng của một sự cố diện rộng | Thống nhất khi triển khai | `FEAT-27`, `28`, `41` |
| `KPI-08` | 7 | Tỷ lệ cơ hội bán hàng được cảnh báo kịp thời khi khách có vé nghiêm trọng chưa xử lý | Khuyến nghị ≥ 95% | `FEAT-29`, `30` |
| `KPI-09` | 11 | Số bài viết tri thức tạo từ vé trong kỳ; thời gian để nhân viên mới đạt chuẩn | Thống nhất khi triển khai | `FEAT-31`, `32` |
| `KPI-10` | 12 | Tổng giờ hỗ trợ thực tế và giờ tính phí theo khách hàng trong kỳ | Thống nhất khi triển khai | `FEAT-33` |
| `KPI-11` | 3 | Tỷ lệ thời gian vé ở trạng thái tạm dừng so với tổng thời gian xử lý | Khuyến nghị ≤ 30% | `FEAT-09`, `42`, `43` |
| `KPI-12` | 9 | Số vé vi phạm cam kết trong kỳ vắng mặt và thời gian chuyển giao hết vé của người vắng mặt | Thống nhất khi triển khai | `FEAT-35`, `36` |
| `KPI-13` | 10 | Tỷ lệ yêu cầu xóa dữ liệu cá nhân trên vé hoàn tất trong thời hạn của yêu cầu (%) | 100% | `FEAT-43`, `44`, `45` |

*Ghi chú:* các mức khuyến nghị là điểm khởi đầu tham khảo theo thông lệ ngành, không phải giá trị cứng của hệ thống. Quy tắc tính tỷ lệ tuân thủ — gồm những vé bị loại khỏi mẫu số — quy định tại `BR-43.2` và được in kèm trên báo cáo.

### 2.7 Luồng nghiệp vụ đầu – cuối

```
[GĐ 1: Tiếp nhận]
    ├── Vé phát sinh từ một nguồn (hộp thư, kênh hội thoại, biểu mẫu, tích hợp, tạo thủ công, nhập tệp)
    ├── Vé thuộc đơn vị tiếp nhận của nguồn đó, nằm trong hàng đợi của đơn vị; hệ thống cấp mã số
    └── Hai đồng hồ cam kết bắt đầu đếm
         │
[GĐ 2: Phân loại & Điều phối]
    ├── Phân loại nhiều cấp, xác định mức ưu tiên
    ├── Tư vấn viên nhận việc từ hàng đợi, hoặc hệ thống phân bổ (xoay vòng có hạn mức / theo kỹ năng)
    │   cho người đang trực, sẵn sàng và có ô cho phép xử lý vé
    ├── Không còn ai phù hợp → vé chờ trong hàng đợi, Trưởng nhóm được cảnh báo
    └── Vé đến nhầm đội → chuyển sang hàng đợi được phép
         │
[GĐ 3: Xử lý & Phản hồi]
    ├── Phản hồi công khai đầu tiên → hoàn tất mốc Phản hồi Đầu tiên
    ├── Trao đổi, ghi chú nội bộ; tách vé / gộp vé / gom dưới vé cha khi cần
    └── Trạng thái "chờ bên khác" → đồng hồ Xử lý Dứt điểm tạm dừng
         │
[GĐ 4: Giám sát hạn chót & Leo thang]
    ├── Qua ngưỡng cảnh báo → cảnh báo người phụ trách
    ├── Vi phạm → đánh dấu vi phạm, chạy các mốc leo thang
    └── Khách bức xúc, khiếu nại → leo thang thủ công
         │
[GĐ 5: Kết thúc & Khảo sát]
    ├── Kết thúc vé: nguyên nhân xử lý, tóm tắt giải pháp
    └── Hệ thống gửi khảo sát 1-5 sao
         │
[GĐ 6: Mở lại hoặc Đóng]
    ├── Khách phản hồi trong hạn mở lại → mở lại đúng vé
    ├── Khách phản hồi sau hạn → vé mới liên kết vé cũ
    └── Khách im lặng → nhắc trước, rồi tự động đóng hoàn tất
         │
[GĐ 7: Đo lường, Truy vết & Vòng đời dữ liệu]
    ├── Chỉ số lên bảng điều khiển và báo cáo
    ├── Thay đổi quan trọng vào nhật ký bất biến
    └── Hết hạn lưu → lưu trữ dài hạn; yêu cầu xóa của khách → khử định danh, trả kết quả về Biên bản Hoàn tất Xử lý
```

---

## 3. Đặc tả yêu cầu chức năng

## A. QUẢN TRỊ VÉ HỖ TRỢ CƠ BẢN

### FEAT-01 — Tạo mới & Quản lý Vé Hỗ trợ

**Mô tả nghiệp vụ:** Tiếp nhận và tạo vé, cập nhật tiêu đề, mô tả, kênh tiếp nhận, mức ưu tiên và trạng thái; theo dõi tiến độ xử lý. Vé có thể được tạo từ một hội thoại đa kênh đang xử lý để giữ bối cảnh trao đổi trước đó. Doanh nghiệp tự định nghĩa bộ trạng thái vé của mình.

**Vai trò sử dụng chính:** Nhân viên Hỗ trợ, Quản lý (tạo và cập nhật vé); người có quyền Cấu hình danh mục vé (định nghĩa trạng thái).

**Điều kiện tiên quyết:** Người tạo có ô (Vé hỗ trợ, Tạo) = Có; người sửa có ô (Vé hỗ trợ, Sửa) bao phủ vé.

**Luồng chính:**

1. Người dùng mở màn hình tạo vé (trực tiếp hoặc từ một hội thoại đang xử lý), nhập các thông tin bắt buộc; hàng đợi được đặt theo `BR-34.2` — vé tạo từ hội thoại lấy mặc định của loại nguồn "Vé từ hội thoại" tìm từ đơn vị tiếp nhận hiện tại của hội thoại. Nội dung sao chép từ hội thoại giữ nguyên các giá trị đã che (`BR-45.12`).
2. Hệ thống cấp mã số (`FEAT-02`), đặt trạng thái mặc định, khởi động hai đồng hồ cam kết (`FEAT-07`).
3. Trong quá trình xử lý, người có ô Sửa bao phủ vé cập nhật thông tin và đổi trạng thái.

**Luồng ngoại lệ:**

- Thiếu thông tin bắt buộc → không lưu được; màn hình chỉ rõ trường còn thiếu trước khi bấm lưu.
- Vé đã bị gộp vào vé khác → mọi thao tác sửa bị vô hiệu (`BR-01.3`).

**Quy tắc nghiệp vụ:**

- **`BR-01.1` (Thông tin bắt buộc):** Tiêu đề, Kênh tiếp nhận, Mức ưu tiên, Người yêu cầu.

  **Lý do nghiệp vụ:** thiếu một trong bốn thông tin này thì không biết vé hỏi gì, trả lời qua đâu, cam kết theo mức nào và phục vụ ai.

- **`BR-01.2` (Trạng thái cấu hình được theo doanh nghiệp):** Mỗi doanh nghiệp tự định nghĩa danh sách trạng thái vé (tên hiển thị, màu, thứ tự) và đánh dấu cho từng trạng thái: là trạng thái mặc định khi tạo vé hay không; là trạng thái kết thúc hay không, và nếu có thì thuộc dạng "đã xử lý xong" hay "đã đóng hoàn tất"; có tạm dừng đồng hồ cam kết hay không (`FEAT-09`); có bắt buộc nguyên nhân xử lý khi chuyển vào hay không (`BR-19.1`).

  **Lý do nghiệp vụ:** quy trình hỗ trợ của mỗi doanh nghiệp khác nhau; hai dạng kết thúc phải tách để phân biệt thời điểm xử lý xong (căn cứ cam kết) với thời điểm đóng hẳn hồ sơ.

- **`BR-01.3` (Khóa vé đã gộp):** Vé đã bị gộp vào vé khác (`FEAT-27`) bị khóa, không sửa thêm được, trừ khi thao tác gộp được hoàn tác (`BR-27.5`).

  **Lý do nghiệp vụ:** nội dung của vé phụ đã chuyển sang vé chính; sửa tiếp trên vé phụ tạo ra hai nơi ghi nhận cùng một yêu cầu.

- **`BR-01.4` (Bảo vệ trạng thái đang được sử dụng):** Trạng thái đang có vé sử dụng không xóa được, chỉ đánh dấu ngừng sử dụng: biến mất khỏi danh sách chọn cho vé mới, vé cũ giữ nguyên. Doanh nghiệp luôn phải có ít nhất một trạng thái mặc định và một trạng thái kết thúc dạng "đã xử lý xong"; thao tác vi phạm điều kiện này bị từ chối.

  **Lý do nghiệp vụ:** xóa trạng thái đang dùng làm vé mất trạng thái và báo cáo lịch sử hụt dữ liệu; thiếu trạng thái mặc định thì không tạo được vé, thiếu "đã xử lý xong" thì không đo được cam kết xử lý dứt điểm (`BR-07.4`).

- **`BR-01.5` (Đổi dấu hiệu của trạng thái đang dùng):** Đổi dấu hiệu "tạm dừng cam kết" hoặc dạng kết thúc của một trạng thái đang được dùng chỉ áp cho vé phát sinh sau thời điểm sửa; vé đang mở giữ nguyên cách tính đã áp dụng cho tới khi kết thúc. Màn hình báo trước số vé đang chịu ảnh hưởng.

  **Lý do nghiệp vụ:** áp ngay cho vé đang mở khiến hàng trăm vé đang dừng đồng hồ chạy lại và lập tức vi phạm — doanh nghiệp mất cam kết vì một thao tác cấu hình chứ không phải vì chất lượng phục vụ. Nguyên tắc này thống nhất với `BR-08.5` và `BR-40.2`.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-01.1.1` | Màn hình tạo vé, chưa chọn Người yêu cầu | Quan sát nút lưu | Trường Người yêu cầu được đánh dấu bắt buộc; nút lưu bị vô hiệu kèm giải thích |
| `AC-01.1.2` | Đủ bốn thông tin bắt buộc | Lưu | Vé được tạo với mã số mới và trạng thái mặc định |
| `AC-01.2.1` | Doanh nghiệp tạo trạng thái "Chờ đối tác", đánh dấu tạm dừng cam kết | Chuyển một vé sang trạng thái đó | Đồng hồ Xử lý Dứt điểm của vé tạm dừng |
| `AC-01.3.1` | Vé V2 đã bị gộp vào V1 | Mở V2 | Mọi trường ở dạng chỉ đọc, có ghi chú "Đã gộp vào V1" kèm đường dẫn |
| `AC-01.4.1` | 200 vé đang ở trạng thái "Chờ khách hàng" | Xóa trạng thái này | Từ chối; chỉ có lựa chọn ngừng sử dụng; 200 vé giữ nguyên trạng thái |
| `AC-01.4.2` | Doanh nghiệp chỉ có một trạng thái "đã xử lý xong" | Ngừng sử dụng trạng thái đó | Từ chối, nêu lý do không đo được cam kết xử lý dứt điểm |
| `AC-01.5.1` | 200 vé đang mở ở trạng thái có dấu hiệu tạm dừng | Bỏ dấu hiệu tạm dừng, xác nhận | Màn hình báo trước 200 vé bị ảnh hưởng; sau khi xác nhận, 200 vé giữ cách tính cũ; vé mới vào trạng thái này không còn tạm dừng |

---

### FEAT-02 — Cấp Mã Định danh Vé Duy nhất theo Doanh nghiệp

**Mô tả nghiệp vụ:** Mỗi vé khi tạo được cấp một mã số duy nhất, tăng dần riêng cho từng doanh nghiệp (ví dụ `TKT-00421`), dùng để tra cứu và trao đổi với khách hàng.

**Vai trò sử dụng chính:** Hệ thống (cấp mã); mọi người xem được vé (tra cứu).

**Điều kiện tiên quyết:** Vé được tạo thành công.

**Luồng chính:**

1. Khi vé được tạo bằng bất kỳ nguồn nào, hệ thống cấp mã số kế tiếp của doanh nghiệp.
2. Mã số hiển thị trên vé, trong mọi thông báo gửi khách hàng và tra cứu được trên ô tìm kiếm.

**Quy tắc nghiệp vụ:**

- **`BR-02.1` (Duy nhất và không tái sử dụng):** Mã số không bao giờ bị cấp trùng hoặc dùng lại, kể cả khi nhiều người tạo vé hay nhập hàng loạt đồng thời; vé bị xóa không trả mã về để cấp lại.

  **Lý do nghiệp vụ:** khách hàng có thể vẫn giữ mã cũ trong thư trao đổi; cấp lại mã làm họ tra ra vé của người khác.

- **`BR-02.2` (Không đổi theo vòng đời):** Mã số giữ nguyên suốt vòng đời, kể cả khi vé đổi người phụ trách, đổi hàng đợi, đổi phân loại, bị gộp hoặc được tách.

  **Lý do nghiệp vụ:** mã số là đầu mối duy nhất khách hàng và nhân viên dùng chung; đổi mã là mất dấu yêu cầu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-02.1.1` | Hai người tạo vé cùng một thời điểm, đồng thời một lô nhập 1.000 vé đang chạy | Quan sát mã số | Mọi vé có mã khác nhau, không có khoảng trống bị cấp hai lần |
| `AC-02.1.2` | Vé `TKT-00500` bị xóa vĩnh viễn | Tạo vé mới | Vé mới không nhận mã `TKT-00500` |
| `AC-02.2.1` | Vé `TKT-00421` được chuyển sang hàng đợi khác và đổi người phụ trách | Mở vé | Mã vẫn là `TKT-00421` |

---

### FEAT-03 — Liên kết Khách hàng Cá nhân & Doanh nghiệp

**Mô tả nghiệp vụ:** Liên kết vé với hồ sơ khách hàng cá nhân là người yêu cầu và doanh nghiệp họ thuộc về, để tư vấn viên nắm bối cảnh khi xử lý.

**Vai trò sử dụng chính:** Nhân viên Hỗ trợ, Quản lý.

**Điều kiện tiên quyết:** Người thao tác có ô (Vé hỗ trợ, Sửa) bao phủ vé.

**Luồng chính:**

1. Chọn người yêu cầu khi tạo vé, hoặc hệ thống tự nhận diện theo địa chỉ liên hệ của kênh.
2. Doanh nghiệp liên quan được tự điền theo hồ sơ người yêu cầu.
3. Màn hình xử lý vé hiển thị tóm tắt bối cảnh khách hàng.

**Quy tắc nghiệp vụ:**

- **`BR-03.1` (Một người yêu cầu cho mỗi vé):** Mỗi vé gắn với đúng một khách hàng cá nhân là người yêu cầu chính; người cùng quan tâm được đưa vào danh sách người theo dõi.

  **Lý do nghiệp vụ:** khảo sát, thông báo và quyền chủ thể dữ liệu đều cần biết chắc vé thuộc về ai.

- **`BR-03.2` (Doanh nghiệp suy ra từ khách hàng):** Doanh nghiệp liên quan được tự điền theo hồ sơ người yêu cầu; sửa lại được khi người đó làm việc cho nhiều doanh nghiệp.

  **Lý do nghiệp vụ:** cam kết theo hợp đồng (`FEAT-40`) và báo cáo cho khách hàng doanh nghiệp (`BR-43.1`) cần đúng doanh nghiệp.

- **`BR-03.3` (Hiển thị bối cảnh khách hàng):** Màn hình xử lý vé hiển thị hạng khách hàng, số vé đang mở và đã đóng trước đó, điểm hài lòng trung bình, cơ hội bán hàng đang mở. Mỗi thông tin chỉ hiển thị khi người xem có mức Xem bao phủ nguồn của nó, cộng lượt đọc tự động khách hàng của vé đang mở theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.5`; cơ hội ngoài phạm vi hiển thị dạng "Bị hạn chế truy cập".

  **Lý do nghiệp vụ:** tư vấn viên cần bối cảnh để xử lý đúng mức; nhưng màn hình vé không được thành đường xem dữ liệu kinh doanh ngoài phạm vi.

- **`BR-03.4` (Không mất dữ liệu khi hồ sơ khách hàng thay đổi):** Hồ sơ khách hàng bị gộp thì vé được trỏ sang hồ sơ còn lại; hồ sơ bị xóa thì vé không bị xóa theo, chịu `FEAT-45` nếu đó là xóa theo quyền chủ thể dữ liệu.

  **Lý do nghiệp vụ:** lịch sử phục vụ là dữ liệu vận hành và căn cứ đối soát cam kết, không được mất vì thao tác trên hồ sơ khách hàng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-03.1.1` | Vé có người yêu cầu A | Thêm người yêu cầu thứ hai B | Không có lựa chọn; B chỉ thêm được vào người theo dõi |
| `AC-03.2.1` | Chọn người yêu cầu thuộc Công ty Đại Phát | Quan sát trường Doanh nghiệp | Tự điền Công ty Đại Phát; sửa được |
| `AC-03.3.1` | Tư vấn viên không có mức Xem trên cơ hội của khách | Mở vé | Phần bối cảnh hiện "Bị hạn chế truy cập" ở mục cơ hội; các mục khác hiển thị bình thường |
| `AC-03.4.1` | Hồ sơ khách A được gộp vào hồ sơ A' | Mở vé cũ của A | Vé trỏ tới A', lịch sử vé nguyên vẹn |

---

## B. PHÂN LOẠI & TÙY BIẾN

### FEAT-04 — Danh mục Phân loại Nhiều Cấp

**Mô tả nghiệp vụ:** Cây phân loại nhiều tầng (tối đa theo `CFG-04-01`), ví dụ *"Lỗi Kỹ thuật" → "Phần mềm" → "Không Đăng Nhập Được"*. Phân loại chính xác là cơ sở tìm nguyên nhân gốc của sự cố lặp lại (`BR-43.4`) và điều phối theo kỹ năng (`BR-11.2`).

**Vai trò sử dụng chính:** Nhân viên Hỗ trợ, Quản lý (chọn phân loại); người có quyền Cấu hình danh mục vé (thiết lập cây).

**Điều kiện tiên quyết:** Doanh nghiệp đã có cây phân loại.

**Luồng chính:**

1. Người có quyền Cấu hình danh mục vé tạo, đổi tên, ngừng sử dụng nút của cây.
2. Khi xử lý, tư vấn viên chọn phân loại tới nút cuối của nhánh.

**Quy tắc nghiệp vụ:**

- **`BR-04.1` (Chọn tới cấp cuối):** Phân loại phải chọn tới nút cuối của nhánh; dừng ở cấp giữa bị từ chối. Phân loại bắt buộc có khi vé chuyển sang "đã xử lý xong", không bắt buộc khi tạo.

  **Lý do nghiệp vụ:** phân loại không đủ chi tiết làm báo cáo nguyên nhân mất giá trị; bắt chọn khi tiếp nhận thì tư vấn viên chọn bừa vì chưa biết sự cố là gì.

- **`BR-04.2` (Ngừng sử dụng thay vì xóa):** Nút không còn dùng được đánh dấu ngừng sử dụng: không xuất hiện khi phân loại vé mới, vé cũ giữ nguyên phân loại.

  **Lý do nghiệp vụ:** xóa nút đang dùng làm báo cáo lịch sử sai lệch.

- **`BR-04.3` (Đổi tên áp cho cả vé cũ):** Đổi tên hiển thị của một nút áp cho mọi vé.

  **Lý do nghiệp vụ:** đổi tên là diễn đạt lại cùng một khái niệm; muốn tách khái niệm thì tạo nút mới và ngừng nút cũ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-04.1.1` | Chọn "Lỗi Kỹ thuật → Phần mềm" (còn cấp con) | Chuyển vé sang "đã xử lý xong" | Ô phân loại được đánh dấu cần chọn tiếp trước khi xác nhận; không chuyển được |
| `AC-04.1.2` | Tạo vé mới chưa phân loại | Lưu | Lưu thành công |
| `AC-04.2.1` | Nút "Lỗi Fax" đang dùng trên 50 vé cũ | Ngừng sử dụng | Không còn trong danh sách chọn; 50 vé cũ vẫn hiển thị "Lỗi Fax"; báo cáo kỳ trước không đổi |
| `AC-04.3.1` | Đổi tên "Phần mềm" thành "Ứng dụng" | Mở vé cũ thuộc nút đó | Hiển thị "Ứng dụng" |

---

### FEAT-05 — Trường Dữ liệu Tùy biến cho Vé Hỗ trợ

**Mô tả nghiệp vụ:** Doanh nghiệp tự định nghĩa thêm trường thông tin cần thu thập trên vé (ví dụ "Mã Giao dịch Ngân hàng"), áp dụng chung cho mọi vé. Việc định nghĩa trường, đánh dấu nhạy cảm và phân quyền trường theo [`object-manager-srs.md`](./object-manager-srs.md).

**Vai trò sử dụng chính:** Người có quyền Cấu hình danh mục vé (định nghĩa); Nhân viên Hỗ trợ, Quản lý (nhập liệu).

**Điều kiện tiên quyết:** Trường đã được định nghĩa cho loại dữ liệu Vé hỗ trợ.

**Luồng chính:**

1. Định nghĩa trường, chọn bắt buộc hay không.
2. Tư vấn viên nhập giá trị trong lúc xử lý.
3. Trường xuất hiện trong bộ lọc danh sách và tệp xuất.

**Quy tắc nghiệp vụ:**

- **`BR-05.1` (Trường bắt buộc chặn ở bước kết thúc):** Trường bắt buộc phải có giá trị trước khi vé chuyển sang "đã xử lý xong", không chặn ở bước tạo. Ràng buộc không áp cho việc hệ thống tự chuyển vé sang "đã đóng hoàn tất" (`FEAT-22`).

  **Lý do nghiệp vụ:** lúc tiếp nhận tư vấn viên thường chưa đủ thông tin; còn tiến trình tự động không có ai để yêu cầu nhập và thông tin đã được kiểm ở bước kết thúc trước đó.

- **`BR-05.2` (Không mất dữ liệu khi ngừng dùng trường):** Trường bị ngừng sử dụng vẫn giữ và tra cứu được dữ liệu đã nhập trên vé cũ.

  **Lý do nghiệp vụ:** dữ liệu đã thu thập là căn cứ đối soát với khách hàng.

- **`BR-05.3` (Lọc và xuất được):** Trường tùy biến lọc được trong danh sách vé và có mặt trong tệp xuất, chịu phân quyền trường và che dữ liệu (`BR-25.3`).

  **Lý do nghiệp vụ:** thu thập mà không lọc, không xuất được thì việc thu thập vô nghĩa.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-05.1.1` | Trường "Mã Giao dịch" bắt buộc, đang trống | Tạo vé | Tạo được |
| `AC-05.1.2` | Như trên | Chuyển vé sang "đã xử lý xong" | Trường được đánh dấu cần nhập ngay trên hộp thoại kết thúc; không kết thúc được khi còn trống |
| `AC-05.1.3` | Vé "đã xử lý xong" có trường bắt buộc mới được thêm sau đó, đang trống | Hết thời gian chờ tự động đóng | Vé vẫn được tự động đóng hoàn tất |
| `AC-05.2.1` | Ngừng dùng trường "Mã Giao dịch" | Mở vé cũ | Vẫn thấy giá trị đã nhập |
| `AC-05.3.1` | Lọc theo "Mã Giao dịch" | Xuất kết quả | Tệp có cột "Mã Giao dịch" |

---

### FEAT-06 — Ma trận Đánh giá Mức độ Tác động & Khẩn cấp

**Mô tả nghiệp vụ:** Tư vấn viên đánh giá hai yếu tố khách quan, hệ thống suy ra mức ưu tiên theo ma trận doanh nghiệp đã thống nhất:

- **Tác động:** Cá nhân / Phòng ban / Toàn công ty.
- **Khẩn cấp:** Chặn hoàn toàn công việc / Có phương án tạm thời / Không khẩn cấp.

**Vai trò sử dụng chính:** Nhân viên Hỗ trợ (đánh giá); người có ô (Vé hỗ trợ, Ghi đè mức ưu tiên) (ghi đè); người có quyền Cấu hình danh mục vé (cấu hình ma trận).

**Điều kiện tiên quyết:** Ma trận đã cấu hình (`CFG-06-01`).

**Luồng chính:**

1. Tư vấn viên chọn Tác động và Khẩn cấp; hệ thống đặt mức ưu tiên theo ma trận và áp chính sách cam kết tương ứng.
2. Khi cần, người có ô Ghi đè mức ưu tiên bao phủ vé đổi mức ưu tiên kèm lý do.

**Quy tắc nghiệp vụ:**

- **`BR-06.1` (Ma trận cấu hình được theo doanh nghiệp):** Mỗi doanh nghiệp tự định nghĩa mức ưu tiên cho từng tổ hợp Tác động × Khẩn cấp (`CFG-06-01`); doanh nghiệp mới nhận ma trận mặc định tại Phụ lục B.

  **Lý do nghiệp vụ:** một vé "Toàn công ty × Có phương án tạm thời" là khẩn với doanh nghiệp này nhưng bình thường với doanh nghiệp khác; ma trận dùng chung cho mọi doanh nghiệp là áp đặt cam kết.

- **`BR-06.2` (Ghi đè có lý do):** Ghi đè mức ưu tiên hệ thống đề xuất cần ô (Vé hỗ trợ, Ghi đè mức ưu tiên) bao phủ vé, bắt buộc nhập lý do, và được ghi vào nhật ký thay đổi.

  **Lý do nghiệp vụ:** đổi mức ưu tiên là đổi cam kết với khách hàng; phải biết ai đổi và vì sao khi khách hàng khiếu nại.

- **`BR-06.3` (Tính lại cam kết từ thời điểm tạo vé):** Khi mức ưu tiên thay đổi, hạn chót được tính lại theo chính sách tương ứng, tính từ thời điểm tạo vé ban đầu; mốc đã vi phạm vẫn giữ trạng thái vi phạm.

  **Lý do nghiệp vụ:** tính từ lúc đổi sẽ cho phép kéo dài cam kết bằng cách hạ rồi nâng lại mức ưu tiên; xóa vi phạm đã xảy ra là làm đẹp số liệu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-06.1.1` | Ma trận: Toàn công ty × Chặn hoàn toàn → Khẩn cấp | Đánh giá vé theo tổ hợp đó | Mức ưu tiên Khẩn cấp được đặt và chính sách Khẩn cấp áp dụng |
| `AC-06.1.2` | Doanh nghiệp A sửa một ô ma trận | Doanh nghiệp B đánh giá cùng tổ hợp | Doanh nghiệp B vẫn theo ma trận của mình |
| `AC-06.2.1` | Người có ô Ghi đè mức ưu tiên bao phủ vé | Hạ mức ưu tiên, bỏ trống lý do | Nút xác nhận bị vô hiệu cho tới khi nhập lý do |
| `AC-06.2.2` | Nhân viên Hỗ trợ có ô Ghi đè mức ưu tiên = Không có | Mở vé | Mức ưu tiên ở dạng chỉ đọc kèm giải thích |
| `AC-06.3.1` | Vé tạo 09:00, đổi từ Cao sang Trung bình lúc 10:00 | Quan sát hạn chót | Hạn chót tính theo chính sách Trung bình từ 09:00; nhật ký ghi người đổi, lý do |
| `AC-06.3.2` | Mốc Phản hồi Đầu tiên đã vi phạm | Hạ mức ưu tiên làm hạn mới muộn hơn thời điểm hiện tại | Mốc vẫn ở trạng thái vi phạm |

---

## C. CAM KẾT CHẤT LƯỢNG DỊCH VỤ

### FEAT-07 — Đồng hồ Cam kết Hai Mốc

**Mô tả nghiệp vụ:** Kiểm soát độc lập hai mốc cam kết với khách hàng: **Phản hồi Đầu tiên** (tới phản hồi công khai đầu tiên) và **Xử lý Dứt điểm** (tới lần đầu chuyển sang "đã xử lý xong"). Hai mốc chạy song song từ cùng thời điểm; đã phản hồi không làm dời hạn xử lý dứt điểm.

**Vai trò sử dụng chính:** Người có quyền Cấu hình cam kết dịch vụ (chính sách); Hệ thống (tính toán, theo dõi).

**Điều kiện tiên quyết:** Có chính sách cam kết cho mức ưu tiên của vé.

**Luồng chính:**

1. Vé được tạo → hệ thống chọn chính sách (`BR-40.1`), tính hai hạn chót theo lịch làm việc.
2. Phản hồi công khai đầu tiên → mốc Phản hồi Đầu tiên hoàn tất.
3. Vé lần đầu chuyển sang "đã xử lý xong" → mốc Xử lý Dứt điểm hoàn tất.

**Quy tắc nghiệp vụ:**

- **`BR-07.1` (Chính sách theo mức ưu tiên):** Mỗi mức ưu tiên có thời hạn riêng cho từng mốc; cam kết theo hạng khách hàng hoặc hợp đồng áp thêm theo `BR-40.1`.

  **Lý do nghiệp vụ:** sự cố chặn toàn công ty không thể chung thời hạn với câu hỏi hướng dẫn.

- **`BR-07.2` (Mốc bắt đầu đếm):** Cả hai đồng hồ đếm từ thời điểm vé được tạo. Vé tạo từ hội thoại đã có trao đổi trước đó vẫn đếm từ lúc tạo vé.

  **Lý do nghiệp vụ:** cam kết của hội thoại thuộc [`omnichat-srs.md`](./omnichat-srs.md); tính hai lần cùng một khoảng thời gian làm một sự chậm trễ bị phạt hai lần.

- **`BR-07.3` (Điều kiện hoàn tất mốc phản hồi đầu tiên):** Chỉ một phản hồi công khai thực sự gửi tới khách hàng mới hoàn tất mốc này; ghi chú nội bộ, thông báo hệ thống tự sinh, đổi trạng thái đều không tính (`BR-14.3`). Cập nhật lan truyền từ vé cha được tính theo `BR-28.2`.

  **Lý do nghiệp vụ:** khách hàng chỉ nhận ra mình được phục vụ khi nhận được câu trả lời.

- **`BR-07.4` (Điều kiện hoàn tất mốc xử lý dứt điểm):** Mốc hoàn tất tại lần đầu vé chuyển sang "đã xử lý xong". Vé bị mở lại thì kết quả đã ghi nhận không bị xóa; lần xử lý xong sau ghi nhận riêng để theo dõi chất lượng (`KPI-04`).

  **Lý do nghiệp vụ:** mở lại là một sự kiện chất lượng riêng; xóa kết quả cũ làm số liệu tuân thủ dao động theo hành vi của khách.

- **`BR-07.5` (Tính theo lịch làm việc):** Mọi thời hạn và thời gian đã trôi tính theo lịch làm việc đang áp dụng (`FEAT-08`).

  **Lý do nghiệp vụ:** doanh nghiệp không cam kết phục vụ ngoài giờ trừ khi đã thỏa thuận.

- **`BR-07.6` (Hiển thị thời gian còn lại):** Màn hình vé và danh sách vé hiển thị thời gian còn lại tới từng hạn chót; mốc đang tạm dừng hiển thị "Tạm dừng".

  **Lý do nghiệp vụ:** tư vấn viên tự sắp thứ tự công việc mà không phải nhẩm tính.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-07.1.1` | Chính sách Cao: phản hồi 1 giờ, xử lý 8 giờ làm việc | Tạo vé mức Cao lúc 09:00 Thứ Ba | Hạn phản hồi 10:00, hạn xử lý 17:00 cùng ngày (lịch 08:00–17:30) |
| `AC-07.2.1` | Hội thoại bắt đầu 08:00, vé tạo từ hội thoại lúc 09:00 | Quan sát hạn chót | Hai đồng hồ đếm từ 09:00 |
| `AC-07.3.1` | Vé mới | Viết một ghi chú nội bộ | Mốc Phản hồi Đầu tiên vẫn đang chạy |
| `AC-07.3.2` | Như trên | Gửi phản hồi công khai | Mốc Phản hồi Đầu tiên hoàn tất tại thời điểm gửi |
| `AC-07.4.1` | Vé đã xử lý xong đúng hạn rồi bị mở lại | Xử lý xong lần hai sau hạn | Kết quả lần đầu vẫn là đúng hạn; lần hai được ghi nhận riêng trong số liệu mở lại |
| `AC-07.5.1` | Lịch 08:00–17:30 Thứ Hai – Thứ Sáu | Vé 4 giờ làm việc tạo 17:00 Thứ Sáu | Hạn là 11:30 Thứ Hai tuần sau |
| `AC-07.6.1` | Vé đang ở trạng thái tạm dừng, chưa có phản hồi đầu tiên | Mở danh sách vé | Mốc Phản hồi hiển thị thời gian còn lại; mốc Xử lý hiển thị "Tạm dừng" |

---

### FEAT-08 — Lịch Làm việc Doanh nghiệp Cấu hình được

**Mô tả nghiệp vụ:** Doanh nghiệp cấu hình lịch làm việc riêng (ngày làm việc trong tuần, khung giờ từng ngày, múi giờ, ngày nghỉ lễ) để tính cam kết; có thể có nhiều lịch song song cho các chính sách khác nhau. Cách tiếp cận theo ADR-0006.

**Vai trò sử dụng chính:** Người có quyền Cấu hình cam kết dịch vụ; Hệ thống (áp dụng).

**Điều kiện tiên quyết:** Không có; workspace mới dùng lịch mặc định của workspace theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-04`.

**Luồng chính:**

1. Tạo lịch, khai báo ngày, khung giờ, múi giờ, danh sách ngày lễ theo năm.
2. Gán lịch cho chính sách cam kết.
3. Hệ thống dùng lịch của chính sách để tính hạn chót của vé.

**Quy tắc nghiệp vụ:**

- **`BR-08.1` (Chỉ đếm trong giờ làm việc):** Thời gian ngoài khung giờ, ngày nghỉ cuối tuần và ngày lễ đã khai báo không tính vào đồng hồ cam kết.

  **Lý do nghiệp vụ:** công bằng cơ bản với tư vấn viên và đúng điều doanh nghiệp đã cam kết.

- **`BR-08.2` (Nhiều lịch song song):** Doanh nghiệp tạo nhiều lịch và gán cho từng chính sách, ví dụ lịch phục vụ liên tục cho khách hạng cao, lịch hành chính cho khách phổ thông.

  **Lý do nghiệp vụ:** các gói dịch vụ khác nhau bán các khung phục vụ khác nhau.

- **`BR-08.3` (Múi giờ của lịch):** Mỗi lịch gắn một múi giờ; hạn chót tính theo múi giờ của lịch, không theo người xem.

  **Lý do nghiệp vụ:** đội ở Hà Nội phục vụ khách ở Riyadh theo giờ đã cam kết với khách.

- **`BR-08.4` (Cảnh báo năm chưa khai báo ngày lễ):** Ngày lễ khai báo theo năm. Khi năm của một hạn chót chưa có ngày lễ nào được khai báo, hệ thống cảnh báo người có quyền Cấu hình cam kết dịch vụ thay vì âm thầm coi mọi ngày là ngày làm việc.

  **Lý do nghiệp vụ:** quên khai báo Tết là lỗi phổ biến và chỉ lộ ra khi đã vi phạm hàng loạt.

- **`BR-08.5` (Đổi khung giờ không ảnh hưởng vé đang mở):** Sửa khung giờ hoặc ngày làm việc trong tuần chỉ áp cho vé tạo sau thời điểm sửa.

  **Lý do nghiệp vụ:** đổi chính sách không được dời cam kết đã hứa với khách.

- **`BR-08.6` (Bổ sung ngày lễ áp cho cả vé đang mở):** Khai báo thêm ngày lễ áp cho cả vé đang mở có thời gian còn lại đi qua ngày đó; màn hình báo trước số vé và hạn chót mới để người cấu hình xác nhận.

  **Lý do nghiệp vụ:** đây là bổ sung một sự thật về lịch, không phải đổi chính sách; không có ngoại lệ này, vé tạo cuối năm có hạn rơi vào Tết năm sau sẽ tính Tết thành ngày làm việc.

- **`BR-08.7` (Ngày lễ với lịch phục vụ liên tục):** Với lịch phục vụ liên tục, doanh nghiệp chọn ngày lễ có áp dụng hay không (`CFG-08-01`, mặc định không áp dụng).

  **Lý do nghiệp vụ:** khách hàng cao cấp sẽ hỏi điều khoản này khi ký cam kết; phải nêu rõ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-08.1.1` | Lịch 08:00–17:30, Thứ Hai là ngày lễ | Vé 4 giờ làm việc tạo 17:00 Thứ Sáu | Hạn là 11:30 Thứ Ba |
| `AC-08.2.1` | Khách X dùng chính sách với lịch phục vụ liên tục | Vé tạo 02:00 sáng Chủ nhật, cam kết 1 giờ | Hạn 03:00 cùng ngày |
| `AC-08.3.1` | Lịch theo múi giờ Riyadh; người xem ở Hà Nội | Mở vé | Hạn hiển thị theo giờ Hà Nội nhưng là cùng một thời điểm với hạn theo lịch Riyadh |
| `AC-08.4.1` | Năm sau chưa khai báo ngày lễ | Một vé có hạn rơi vào năm sau | Người có quyền Cấu hình cam kết dịch vụ nhận cảnh báo kèm tên lịch |
| `AC-08.5.1` | Vé đang mở; sửa khung giờ thành 08:00–18:00 | Mở vé | Hạn chót không đổi; vé tạo sau thời điểm sửa theo khung mới |
| `AC-08.6.1` | Vé đang mở có hạn 11:30 Thứ Ba | Khai báo Thứ Ba là ngày lễ | Màn hình báo số vé bị ảnh hưởng và hạn mới; sau xác nhận, hạn dời sang 11:30 Thứ Tư |
| `AC-08.7.1` | Lịch phục vụ liên tục, `CFG-08-01` mặc định | Vé phát sinh ngày lễ | Đồng hồ vẫn chạy bình thường |

---

### FEAT-09 — Tự động Tạm dừng Đồng hồ Cam kết theo Trạng thái Vé

**Mô tả nghiệp vụ:** Khi vé chuyển sang trạng thái có dấu hiệu "tạm dừng cam kết" (ví dụ "Chờ khách hàng phản hồi"), đồng hồ Xử lý Dứt điểm đóng băng; khi rời trạng thái đó, đồng hồ chạy tiếp từ số phút còn lại.

**Vai trò sử dụng chính:** Người đổi trạng thái vé (ô Sửa bao phủ vé); Hệ thống.

**Điều kiện tiên quyết:** Doanh nghiệp đã đánh dấu ít nhất một trạng thái tạm dừng (`BR-01.2`).

**Luồng chính:**

1. Vé chuyển vào trạng thái tạm dừng → đồng hồ Xử lý Dứt điểm dừng, ghi lịch sử.
2. Vé rời trạng thái tạm dừng (do người hoặc do khách phản hồi) → đồng hồ chạy tiếp, ghi lịch sử.

**Quy tắc nghiệp vụ:**

- **`BR-09.1` (Chỉ tạm dừng mốc Xử lý Dứt điểm):** Tạm dừng chỉ áp cho đồng hồ Xử lý Dứt điểm.

  **Lý do nghiệp vụ:** chờ khách là lý do chính đáng để chưa xử lý xong, nhưng không phải lý do để chưa trả lời.

- **`BR-09.2` (Không bao giờ tạm dừng mốc Phản hồi Đầu tiên):** Đồng hồ Phản hồi Đầu tiên không tạm dừng trước khi có phản hồi công khai đầu tiên, kể cả khi vé ở trạng thái tạm dừng.

  **Lý do nghiệp vụ:** nếu cho phép, tư vấn viên chuyển vé sang "Chờ khách hàng" ngay khi tiếp nhận để dừng đồng hồ mà chưa trả lời khách, làm `KPI-01` mất ý nghĩa.

- **`BR-09.3` (Ghi nhận lịch sử tạm dừng):** Mỗi lần tạm dừng và chạy tiếp ghi vào lịch sử vé: thời điểm, người thực hiện hoặc sự kiện gây ra, tổng thời gian đã tạm dừng.

  **Lý do nghiệp vụ:** căn cứ đối chiếu khi tranh chấp về tuân thủ với khách hàng.

- **`BR-09.4` (Cảnh báo lạm dụng tạm dừng):** Vé bị tạm dừng quá số lần `CFG-09-01` thì Trưởng nhóm được thông báo để rà soát.

  **Lý do nghiệp vụ:** trạng thái tạm dừng có thể bị dùng để né vi phạm; kiểm soát theo từng người trên nhiều vé thuộc `BR-43.9`.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-09.1.1` | Vé đã có phản hồi đầu tiên, còn 3 giờ xử lý | Chuyển sang "Chờ khách hàng" 2 ngày, khách trả lời | Đồng hồ Xử lý chạy tiếp với 3 giờ còn lại |
| `AC-09.2.1` | Vé chưa có phản hồi công khai | Chuyển ngay sang "Chờ khách hàng" | Đồng hồ Phản hồi Đầu tiên vẫn chạy |
| `AC-09.3.1` | Vé đã tạm dừng hai lần | Mở lịch sử vé | Thấy hai lượt tạm dừng và chạy tiếp, người thực hiện, tổng thời gian tạm dừng |
| `AC-09.4.1` | `CFG-09-01` = 3 | Vé bị tạm dừng lần thứ tư | Trưởng nhóm nhận thông báo kèm mã vé và số lần tạm dừng |

---

### FEAT-40 — Chính sách Cam kết theo Hạng Khách hàng & Hợp đồng Dịch vụ

**Mô tả nghiệp vụ:** Trong kinh doanh B2B, cam kết khác nhau theo hợp đồng: khách gói cao cấp được phản hồi trong 1 giờ, khách phổ thông 8 giờ, kể cả khi hai vé cùng mức ưu tiên. Doanh nghiệp gán chính sách cam kết theo hạng khách hàng hoặc theo hợp đồng.

**Vai trò sử dụng chính:** Người có quyền Cấu hình cam kết dịch vụ; Hệ thống (chọn chính sách khi tạo vé).

**Điều kiện tiên quyết:** Hạng khách hàng có ở phân hệ Khách hàng; thông tin hợp đồng có ở phân hệ Hợp đồng (Mục 1.6).

**Luồng chính:**

1. Khai báo chính sách cho hạng khách hàng, cho hợp đồng cụ thể.
2. Khi tạo vé, hệ thống chọn chính sách theo `BR-40.1` và ghi lại chính sách đã áp dụng.

**Quy tắc nghiệp vụ:**

- **`BR-40.1` (Thứ tự áp dụng chính sách):** Hệ thống chọn theo thứ tự: (1) chính sách riêng của hợp đồng dịch vụ của khách; (2) chính sách theo hạng khách hàng — hạng của doanh nghiệp khách hàng thắng hạng của cá nhân; (3) chính sách chung theo mức ưu tiên. Ở mỗi tầng, chính sách khai báo riêng cho mức ưu tiên của vé thắng chính sách chung của tầng đó. Tầng không có dữ liệu thì bỏ qua sang tầng sau.

  **Lý do nghiệp vụ:** chính sách cụ thể hơn phản ánh đúng điều đã ký với khách; cam kết với doanh nghiệp khách hàng là cam kết hợp đồng, cao hơn đặc điểm của một cá nhân.

- **`BR-40.2` (Ghi nhận chính sách đã áp dụng):** Vé lưu chính sách đã áp dụng lúc tạo; doanh nghiệp sửa chính sách sau đó thì vé đang mở giữ cam kết ban đầu.

  **Lý do nghiệp vụ:** sửa chính sách không được làm vé đang chạy đột ngột thành vi phạm, cũng không được nới cam kết đã hứa.

- **`BR-40.3` (Hiển thị cam kết đang áp dụng):** Màn hình vé hiển thị hạng khách hàng, tầng chính sách đang áp dụng và thời hạn của từng mốc.

  **Lý do nghiệp vụ:** tư vấn viên biết mức độ ưu tiên thực tế khi sắp xếp công việc.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-40.1.1` | Khách X hạng cao cấp (phản hồi 1 giờ), khách Y phổ thông (8 giờ), cùng mức ưu tiên Trung bình | Cả hai gửi yêu cầu | Vé X hạn phản hồi sau 1 giờ, vé Y sau 8 giờ làm việc |
| `AC-40.1.2` | Khách X có hợp đồng riêng phản hồi 30 phút | X gửi yêu cầu | Áp chính sách hợp đồng 30 phút |
| `AC-40.1.3` | Cá nhân hạng phổ thông thuộc doanh nghiệp hạng cao cấp | Gửi yêu cầu | Áp chính sách hạng cao cấp |
| `AC-40.2.1` | Vé X đang mở với hạn 1 giờ | Sửa chính sách hạng cao cấp thành 30 phút | Vé X giữ hạn cũ; vé tạo sau đó theo 30 phút |
| `AC-40.3.1` | Mở vé của X | Quan sát | Hiển thị hạng, tầng "Hợp đồng" hoặc "Hạng khách hàng", thời hạn từng mốc |

## D. HÀNG ĐỢI, PHÂN CÔNG & ĐIỀU PHỐI

### FEAT-34 — Hàng đợi Vé, Đơn vị Tiếp nhận & Nhận việc

**Mô tả nghiệp vụ:** Vé là **bản ghi công việc**: thuộc **đơn vị tiếp nhận** của nguồn đã sinh ra nó và ở lại đơn vị đó suốt vòng đời, để cả đội theo dõi được kể cả sau khi một người đã nhận. Mỗi đơn vị tiếp nhận có một hàng đợi; thành viên của đơn vị thấy vé chưa có người phụ trách và nhận việc được. Tính năng này là phần khai báo của phân hệ Vé hỗ trợ theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10`, `BR-35.11`, `BR-35.12`, `BR-35.13`, `BR-35.14`.

**Vai trò sử dụng chính:** Nhân viên Hỗ trợ (xem hàng đợi, nhận việc, trả về hàng đợi); Quản lý (giám sát, chuyển hàng đợi); Người có toàn quyền (đặt đơn vị tiếp nhận của nguồn, "gồm các đơn vị con", danh sách hàng đợi được chuyển tới).

**Điều kiện tiên quyết:** Nguồn tạo vé đã có đơn vị tiếp nhận; nguồn chưa có đơn vị tiếp nhận không kích hoạt, công khai hay kết nối được.

**Luồng chính:**

1. Vé phát sinh từ một nguồn → thuộc đơn vị tiếp nhận của nguồn, xuất hiện trong hàng đợi của đơn vị đó ở trạng thái chưa có người phụ trách.
2. Tư vấn viên của đơn vị bấm Nhận việc → trở thành Người phụ trách; vé biến mất khỏi danh sách chưa gán của người khác nhưng vẫn thuộc đơn vị.
3. Vé đến nhầm đội → người có ô Gán bao phủ vé chuyển vé sang hàng đợi được phép, kèm lý do.
4. Người phụ trách không tiếp tục được → trả vé về hàng đợi.

**Luồng ngoại lệ:**

- Hai người bấm nhận gần như đồng thời → người trước nhận được (`BR-34.5`).
- Người có ô (Vé hỗ trợ, Gán) = Không có → thấy vé chưa gán nhưng nút Nhận việc bị vô hiệu kèm giải thích.
- Vé bị lượt chặn trên bản ghi đối với người đó → vé không hiển thị với họ.

**Quy tắc nghiệp vụ:**

- **`BR-34.1` (Vé là bản ghi công việc của đơn vị tiếp nhận):** Vé thuộc đơn vị tiếp nhận suốt vòng đời, không đổi đơn vị khi đổi Người phụ trách; chỉ đổi đơn vị khi được chuyển hàng đợi (`BR-34.8`). Phạm vi "Đơn vị của mình", "Đơn vị và các đơn vị con" trên vé tính theo đơn vị tiếp nhận này, cộng phần "Chỉ của mình" của Người phụ trách, theo định nghĩa tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4.

  **Lý do nghiệp vụ:** nếu vé đi theo người nhận, trưởng nhóm mất dấu vé ngay khi một người kiêm nhiệm từ đội khác nhận nó, và dữ liệu khách hàng của đội này lộ sang đội của người nhận.

- **`BR-34.2` (Đơn vị tiếp nhận theo từng nguồn tạo vé):** Mỗi nguồn khai báo đơn vị tiếp nhận như sau:

  | Nguồn tạo vé | Đơn vị tiếp nhận |
  | --- | --- |
  | Hộp thư hỗ trợ (thư điện tử) | Khai báo riêng cho từng hộp thư. Mỗi hộp thư là nguồn của đúng một phân hệ — Hội thoại Đa kênh hoặc Vé hỗ trợ — chọn khi kết nối (`BR-34.10`) |
  | Vé tạo từ một hội thoại (trò chuyện trên website, Zalo OA, mạng xã hội, hộp thư thuộc Hội thoại Đa kênh) | Loại nguồn **"Vé từ hội thoại"** trong đơn vị tiếp nhận mặc định theo loại nguồn của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-35-01`. Mặc định được tìm từ đơn vị tiếp nhận **hiện tại** của hội thoại theo [`omnichat-srs.md`](./omnichat-srs.md) (sau mọi lượt chuyển của hội thoại) đi lên tới đơn vị gốc, lấy giá trị gần nhất của loại nguồn này. Không tìm thấy thì thao tác tạo vé bị chặn kèm giải thích cần Người có toàn quyền khai báo. Người tạo chỉ chọn được hàng đợi khác nằm trong danh sách được chuyển tới (`CFG-34-02`) của hàng đợi mặc định đó |
  | Biểu mẫu hỗ trợ, cổng khách hàng | Khai báo riêng cho từng biểu mẫu |
  | Tích hợp với hệ thống ngoài | Khai báo riêng cho từng tích hợp |
  | Tạo thủ công | Người tạo chọn một hàng đợi trong số các đơn vị tiếp nhận mà mình là thành viên (Đơn vị chính hoặc kiêm nhiệm); người không là thành viên đơn vị tiếp nhận nào (ví dụ nhân viên kinh doanh tạo vé hộ khách) thì vé vào đơn vị tiếp nhận mặc định của loại nguồn "Tạo thủ công". **Người phụ trách:** người tạo là thành viên đơn vị tiếp nhận đã chọn và đạt điều kiện nhận việc (`BR-34.4`) thì lựa chọn "Giao cho tôi" được bật sẵn — giữ thì người tạo là Người phụ trách, bỏ thì vé chưa gán trong hàng đợi; người tạo không là thành viên đơn vị tiếp nhận thì vé luôn chưa gán trong hàng đợi |
  | Nhập từ tệp | Theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10`. Đơn vị tiếp nhận của dòng là cột Đơn vị tiếp nhận nếu có, nếu không là **hàng đợi người nhập chọn cho cả lô** (bắt buộc chọn trước khi chạy). Người phụ trách theo cột Người phụ trách (chịu ô Gán của người nhập) và phải là thành viên của đơn vị tiếp nhận của dòng (Đơn vị chính hoặc kiêm nhiệm ở bất kỳ mức nào), nếu không thì dòng là dòng lỗi. Dòng trỏ tới người Đang chờ chấp nhận thì vé được giữ chỗ theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10`: vé giữ chỗ **luôn thuộc đơn vị tiếp nhận của dòng hay của lô**, lời mời phải ghi đơn vị đó là Đơn vị chính hoặc kiêm nhiệm (nếu không thì dòng là dòng lỗi), nhóm ngoại lệ và ô Gán của người nhập được xét trên chính đơn vị mà vé giữ chỗ thuộc về; khi lời mời bị sửa không còn ghi đơn vị đó, giữ chỗ chấm dứt, vé thành vé chưa gán trong hàng đợi và người phụ trách đơn vị tiếp nhận được báo. Dòng có cột Đơn vị tiếp nhận nhưng không có cột Người phụ trách thì vé **chưa gán** trong hàng đợi đó — người nhập không tự thành Người phụ trách. Dòng không có cả hai cột thì người nhập là Người phụ trách nếu là thành viên đơn vị tiếp nhận của lô; nếu không, vé chưa gán trong hàng đợi của lô |
  | Tách vé (`FEAT-41`) | Hàng đợi hiện tại của vé gốc; người tách chọn được hàng đợi khác trong danh sách được chuyển tới (`BR-34.8`) |
  | Vé mới tạo lại khi khách phản hồi sau hạn mở lại (`BR-21.3`) | Hàng đợi hiện tại của vé cũ |
  | Vé cha của sự cố diện rộng (`FEAT-28`) | Như Tạo thủ công |

  Đơn vị tiếp nhận mặc định theo loại nguồn, ghi đè theo đơn vị, ai đặt và ai đổi theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.12`, `CFG-35-01`. Vé Khiếu nại không qua hàng đợi (`BR-39.3`).

  **Lý do nghiệp vụ:** đơn vị tiếp nhận quyết định ai thấy vé chưa gán bất kể mức Xem; mỗi nguồn phải có câu trả lời xác định, nếu không vé mới không ai thấy hoặc rơi vào nhầm đội.

- **`BR-34.3` (Hàng đợi gồm các đơn vị con):** Người có toàn quyền chọn được "gồm các đơn vị con" cho một hàng đợi; khi đó vé của hàng đợi thuộc mọi đơn vị trong nhánh, thành viên của mọi đơn vị trong nhánh thấy vé chưa gán và nhận việc được. "Trưởng nhóm" của vé là người phụ trách của đơn vị tiếp nhận gốc của hàng đợi.

  **Lý do nghiệp vụ:** một hàng đợi chung cho cả miền tránh việc khách miền Bắc phải chờ riêng đội Hà Nội trong khi đội Hải Phòng đang rảnh; nhưng việc mở rộng ai thấy vé là nới phạm vi cho cả nhánh nên chỉ Người có toàn quyền quyết.

- **`BR-34.4` (Ai thấy vé chưa gán và ai nhận việc được):** Thành viên của đơn vị tiếp nhận — Đơn vị chính hoặc kiêm nhiệm ở bất kỳ mức nào — có ô (Vé hỗ trợ, Xem) khác Không có thấy vé chưa có người phụ trách của hàng đợi; nhận việc được khi ô (Vé hỗ trợ, Gán) khác Không có, người đó Đang hoạt động, và không có nguồn chặn nào áp lên vé (lượt chặn trên bản ghi, chính sách Từ chối), và người đó không là tư vấn viên đang bị khiếu nại trên vé (`BR-12.8`) — theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.11`. Nhận việc là một lượt Gán, được ghi nhật ký thay đổi (`BR-44.1`) với người thực hiện là chính người nhận.

  **Lý do nghiệp vụ:** không có quy tắc này, vé mới chưa gán không ai thấy vì mọi mức truy cập tính theo Người phụ trách; coi nhận việc là Gán để doanh nghiệp tắt được khả năng tự nhận cho một vai trò (ví dụ thực tập sinh chỉ xử lý vé được giao) bằng chính ô Gán.

- **`BR-34.5` (Hai người cùng nhận một vé):** Khi hai người bấm nhận gần như đồng thời, chỉ người thao tác trước được nhận; người sau nhận thông báo vé đã có người nhận kèm tên người đó, và vé biến mất khỏi danh sách chưa gán của họ. Không bao giờ có vé được gán cho hai người.

  **Lý do nghiệp vụ:** tình huống thường xuyên ở hàng đợi đông người giờ cao điểm; người sau tưởng mình đang giữ vé thì khách nhận hai câu trả lời.

- **`BR-34.6` (Cảnh báo vé tồn đọng):** Vé nằm trong hàng đợi chưa có người nhận quá `CFG-34-01` thì Trưởng nhóm được cảnh báo. Ngưỡng này độc lập với hạn chót cam kết.

  **Lý do nghiệp vụ:** mục đích là phát hiện **trước khi** vé kịp vi phạm; vé không ai nhận là nguyên nhân phổ biến nhất của vi phạm cam kết phản hồi.

- **`BR-34.7` (Đồng hồ cam kết vẫn chạy trong hàng đợi):** Thời gian vé nằm trong hàng đợi, kể cả sau khi bị trả về hoặc chuyển hàng đợi, vẫn tính vào đồng hồ cam kết.

  **Lý do nghiệp vụ:** chưa ai nhận vé là vấn đề nội bộ của doanh nghiệp, không phải lý do tạm dừng cam kết với khách hàng (Nguyên tắc 4).

- **`BR-34.8` (Chuyển vé sang hàng đợi khác):** Người có ô (Vé hỗ trợ, Gán) bao phủ vé — gồm cả Người phụ trách hiện tại có ô Gán chỉ là Chỉ của mình — chuyển được vé sang hàng đợi của một đơn vị tiếp nhận khác. Đây là ngoại lệ tường minh của quy tắc đích của ô Gán, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.14`, `BR-25.6`. Điều kiện:
  - Hàng đợi đích nằm trong **danh sách hàng đợi được chuyển tới** của hàng đợi hiện tại (`CFG-34-02`). Danh sách do Người có toàn quyền đặt cho từng hàng đợi; **mặc định trống** — không chuyển hàng đợi được cho tới khi Người có toàn quyền khai báo. Trong Phiên triển khai, khai báo danh sách cho hàng đợi chưa có vé có hiệu lực ngay; đổi danh sách của hàng đợi đang có vé ở dạng soạn sẵn, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-08.7`. Thêm hàng đợi vào danh sách là nới rộng, bớt là thu hẹp; thay đổi thuộc nhật ký thay đổi cấu hình quyền theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.5`, `BR-41.6`.
  - Bắt buộc nhập lý do chuyển (chọn từ danh mục lý do do người có quyền Cấu hình điều phối khai báo, kèm ghi chú tùy chọn).
  - Từ lúc chuyển, vé thuộc đơn vị tiếp nhận mới. Người phụ trách được giữ khi đồng thời đạt các điều kiện sau của `BR-10.3`: là thành viên của đơn vị mới (Đơn vị chính, kiêm nhiệm ở bất kỳ mức nào, hoặc thuộc đơn vị con khi hàng đợi mới gồm các đơn vị con); Đang hoạt động; ô (Vé hỗ trợ, Sửa) và ô (Vé hỗ trợ, Gán) khác Không có; không bị nguồn chặn nào trên vé. Được **miễn** các điều kiện còn lại của `BR-10.3`: đang trong ca, trạng thái Sẵn sàng, và hạn mức năng lực (`BR-10.1`). Không đạt thì vé về trạng thái chưa gán trong hàng đợi mới.
  - Hạn chót cam kết, lịch sử trao đổi, mã số giữ nguyên. Trưởng nhóm của đơn vị mới được thông báo kèm lý do.
  - Màn hình xác nhận báo trước khi người chuyển sẽ không còn thấy vé sau khi chuyển.
  - Vé cha, vé con, vé đã gộp chuyển riêng từng vé; chuyển vé cha không kéo theo vé con.

  **Lý do nghiệp vụ:** vé đến nhầm chi nhánh hay cần chuyên môn của đội khác là việc hằng ngày; nhưng chuyển vé là đưa dữ liệu khách hàng sang một đội khác, nên doanh nghiệp phải khoanh được những đường chuyển hợp lệ, và người nhận vé phải là người của đội mới.

- **`BR-34.9` (Trả vé về hàng đợi):** "Trả về hàng đợi" bỏ Người phụ trách; vé về hàng đợi của **đơn vị tiếp nhận mà nó đang thuộc**, không đổi đơn vị, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.13`. Người phụ trách hiện tại luôn tự trả được vé của mình, kể cả khi ô Gán chỉ là Chỉ của mình, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6` (kèm ghi chú bàn giao, `BR-12.1`); người có ô Gán bao phủ vé trả được vé của người khác. Trả về hàng đợi cũng là kết quả của: từ chối nhận bàn giao (`BR-12.4`), hết ca (`BR-35.3`), tạm ngưng hoặc rời workspace (`BR-36.4`, `BR-36.5`), lựa chọn bắt buộc khi đổi Đơn vị chính (`BR-36.7`), đề xuất khi kiêm nhiệm hết hạn (`BR-36.8`). Chỉ áp cho vé chưa đóng hoàn tất. Đồng hồ cam kết không đổi.

  **Lý do nghiệp vụ:** việc chưa xong của người không làm tiếp được nên quay lại nơi cả đội nhìn thấy, thay vì dồn cho một người nhận không chọn trước.

- **`BR-34.10` (Một hộp thư thư điện tử thuộc đúng một phân hệ):** Mỗi hộp thư thư điện tử là nguồn của đúng một phân hệ — Vé hỗ trợ (mỗi thư mới tạo hoặc nối vào vé) hoặc Hội thoại Đa kênh (mỗi thư là hội thoại) theo [`omnichat-srs.md`](./omnichat-srs.md) — do Người có toàn quyền chọn khi kết nối; không hộp thư nào là nguồn của cả hai. Đổi phân hệ của một hộp thư đang kết nối là **một thao tác "chuyển phân hệ" duy nhất**, không phải ngắt kết nối rồi kết nối lại, và chỉ Người có toàn quyền thực hiện. Từ thời điểm xác nhận, thư mới đến đi vào phân hệ mới, không có khoảng trống nào mà thư không thuộc phân hệ nào; vé và hội thoại đã có giữ nguyên ở phân hệ cũ (thư trả lời vào một vé cũ vẫn nối vào vé đó cho tới khi vé đóng hoàn tất). Lượt chuyển thuộc nhật ký thay đổi cấu hình quyền theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.4` (người thực hiện, thời điểm, phân hệ trước và sau) và **đóng khi lỗi**: không ghi được nhật ký thì thao tác bị hủy, hộp thư giữ phân hệ cũ (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.5`); hộp thư chuyển sang Vé hỗ trợ phải có đơn vị tiếp nhận (`BR-34.2`) trước khi xác nhận.

  **Lý do nghiệp vụ:** một thư sinh ra cả hội thoại lẫn vé thì khách nhận hai câu trả lời, hai đồng hồ cam kết cùng chạy cho một yêu cầu, và yêu cầu xóa dữ liệu phải xử lý ở hai nơi.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-34.1.1` | Vé thuộc "Hỗ trợ – Hà Nội"; A ở "Hỗ trợ – Đà Nẵng" kiêm nhiệm "Hỗ trợ – Hà Nội" mức Chỉ xem nhận vé | Trưởng nhóm Hà Nội mở danh sách vé của đội | Vẫn thấy vé; vé không xuất hiện trong danh sách vé của đội Đà Nẵng |
| `AC-34.2.1` | Hộp thư hỗ trợ@ khai báo đơn vị tiếp nhận "CSKH" | Khách gửi thư mới | Vé mới thuộc "CSKH", hiện trong hàng đợi chưa gán của "CSKH" |
| `AC-34.2.2` | Hội thoại Zalo vào "Hỗ trợ", sau đó được chuyển sang "Hỗ trợ – Đà Nẵng"; chi nhánh Đà Nẵng (cha của "Hỗ trợ – Đà Nẵng") ghi đè mặc định "Vé từ hội thoại" = "Hỗ trợ – Đà Nẵng"; danh sách được chuyển tới của "Hỗ trợ – Đà Nẵng" gồm "Kỹ thuật" | Tư vấn viên tạo vé từ hội thoại đó | Hàng đợi chọn sẵn là "Hỗ trợ – Đà Nẵng"; đổi được sang "Kỹ thuật"; "Hỗ trợ" và các hàng đợi khác không có trong danh sách chọn |
| `AC-34.2.8` | Không đơn vị nào từ đơn vị hiện tại của hội thoại tới gốc, và workspace, khai báo mặc định "Vé từ hội thoại" | Tư vấn viên bấm tạo vé từ hội thoại | Thao tác bị chặn kèm giải thích cần Người có toàn quyền khai báo đơn vị tiếp nhận cho loại nguồn này |
| `AC-34.2.7` | Lô nhập có 50 dòng chỉ có cột Người phụ trách trỏ tới thành viên đội "Kỹ thuật" không thuộc "CSKH"; hàng đợi chọn cho cả lô là "CSKH" | Chạy | 50 dòng báo lỗi "Người phụ trách không thuộc đơn vị tiếp nhận của dòng"; không vé nào được tạo từ các dòng đó |
| `AC-34.2.9` | Dòng trỏ tới N đang chờ chấp nhận; lời mời của N ghi Đơn vị chính "Kỹ thuật", không ghi "CSKH"; hàng đợi của lô là "CSKH" | Chạy | Dòng báo lỗi; nếu lời mời ghi "CSKH" là kiêm nhiệm thì vé được giữ chỗ cho N và thuộc "CSKH" |
| `AC-34.2.11` | Vé giữ chỗ cho N thuộc "CSKH"; lời mời của N ghi kiêm nhiệm "CSKH" | Quản trị viên sửa lời mời, bỏ kiêm nhiệm "CSKH" | Giữ chỗ chấm dứt; vé thành vé chưa gán trong hàng đợi "CSKH"; người phụ trách đơn vị "CSKH" được báo |
| `AC-34.2.12` | Dòng nhập có cột Đơn vị tiếp nhận "Kỹ thuật", không có cột Người phụ trách; người nhập thuộc "Kỹ thuật" | Chạy | Vé chưa gán trong hàng đợi "Kỹ thuật"; người nhập không là Người phụ trách |
| `AC-34.2.13` | Người nhập không thuộc "CSKH"; lô chọn đơn vị tiếp nhận "CSKH"; 120 dòng trống cả cột Người phụ trách lẫn cột Đơn vị tiếp nhận | Chạy | 120 vé chưa gán trong hàng đợi "CSKH"; người nhập không là Người phụ trách |
| `AC-34.2.10` | Tư vấn viên của "Hỗ trợ" tạo vé thủ công vào "Hỗ trợ" | Giữ "Giao cho tôi" rồi lưu; tạo vé thứ hai và bỏ chọn | Vé thứ nhất do người tạo phụ trách; vé thứ hai chưa gán trong hàng đợi "Hỗ trợ" |
| `AC-34.2.3` | Nhân viên Kinh doanh không thuộc đơn vị tiếp nhận nào; mặc định loại nguồn "Tạo thủ công" là "CSKH" | Tạo vé hộ khách | Vé thuộc "CSKH"; ô chọn hàng đợi hiển thị "CSKH" ở dạng chỉ đọc |
| `AC-34.2.4` | Tư vấn viên là thành viên "Hỗ trợ" và kiêm nhiệm "Kỹ thuật" | Tạo vé thủ công | Chọn được "Hỗ trợ" hoặc "Kỹ thuật"; không chọn được hàng đợi khác |
| `AC-34.2.5` | Biểu mẫu hỗ trợ mới chưa có đơn vị tiếp nhận, không có mặc định | Bấm công khai biểu mẫu | Nút công khai bị vô hiệu kèm giải thích cần Người có toàn quyền đặt đơn vị tiếp nhận |
| `AC-34.2.6` | Lô nhập 500 vé, 120 dòng không có cột Đơn vị tiếp nhận và Người phụ trách | Bấm chạy khi chưa chọn hàng đợi cho cả lô | Nút chạy bị vô hiệu cho tới khi chọn; sau khi chọn, 120 vé thuộc hàng đợi đã chọn, Người phụ trách là người nhập |
| `AC-34.3.1` | Hàng đợi "Miền Bắc" bật gồm các đơn vị con | Tư vấn viên "Chi nhánh Hải Phòng" (con của Miền Bắc) mở hàng đợi | Thấy và nhận được vé chưa gán của "Miền Bắc" |
| `AC-34.3.2` | Thành viên có quyền Cấu hình điều phối, không có toàn quyền | Mở cấu hình hàng đợi | Công tắc "gồm các đơn vị con" ở dạng chỉ đọc kèm giải thích |
| `AC-34.4.1` | Nhân viên Hỗ trợ có (Vé, Xem) = Đơn vị của mình, thuộc "Hỗ trợ" | Mở hàng đợi, bấm Nhận việc | Vé được gán cho mình; nhật ký ghi lượt gán do chính người đó thực hiện |
| `AC-34.4.2` | Thành viên "Hỗ trợ" có ô (Vé, Gán) = Không có | Mở hàng đợi | Thấy vé chưa gán; nút Nhận việc bị vô hiệu kèm giải thích |
| `AC-34.4.3` | Thành viên đội Kinh doanh, không thuộc "Hỗ trợ" | Mở danh sách vé | Không thấy vé chưa gán của "Hỗ trợ" |
| `AC-34.4.4` | Vé chưa gán có lượt chặn đối với tư vấn viên B | B mở hàng đợi | Vé không xuất hiện với B |
| `AC-34.5.1` | A và B cùng bấm nhận một vé | Hai thao tác đến gần như đồng thời | Một người nhận được; người kia nhận thông báo "Vé đã được A nhận" và vé biến mất khỏi danh sách chưa gán của họ |
| `AC-34.6.1` | `CFG-34-01` = 15 phút; vé vào hàng đợi 09:00 | 09:15 chưa ai nhận | Trưởng nhóm nhận cảnh báo tồn đọng, dù vé chưa vi phạm |
| `AC-34.7.1` | Vé nằm hàng đợi 15 phút rồi được nhận | Quan sát đồng hồ Phản hồi Đầu tiên | 15 phút chờ đã được tính |
| `AC-34.8.1` | Vé của "CSKH trung tâm"; danh sách được chuyển tới của "CSKH trung tâm" gồm "Hỗ trợ – Đà Nẵng"; Quản lý có ô Gán bao phủ vé | Chuyển sang "Hỗ trợ – Đà Nẵng", chọn lý do "Sai chi nhánh" | Vé thuộc "Hỗ trợ – Đà Nẵng", chưa gán trong hàng đợi đó; hạn chót không đổi; Trưởng nhóm Đà Nẵng nhận thông báo kèm lý do; nhật ký ghi lượt chuyển |
| `AC-34.8.9` | Workspace mới, chưa ai khai báo danh sách được chuyển tới | Người có ô Gán mở thao tác chuyển hàng đợi | Thao tác bị vô hiệu kèm giải thích danh sách đang trống |
| `AC-34.8.2` | Danh sách được chuyển tới của "CSKH" không có "Kế toán" | Người có ô Gán mở hộp chuyển hàng đợi | "Kế toán" không có trong danh sách |
| `AC-34.8.3` | Người phụ trách A thuộc "Hỗ trợ" và kiêm nhiệm "Kỹ thuật" | Chuyển vé của A sang "Kỹ thuật" | Vé thuộc "Kỹ thuật", A vẫn là Người phụ trách |
| `AC-34.8.4` | Người phụ trách B không thuộc "Kỹ thuật" | Chuyển vé của B sang "Kỹ thuật" | Vé chưa gán trong hàng đợi "Kỹ thuật"; B không còn là Người phụ trách |
| `AC-34.8.5` | Nhân viên Hỗ trợ (Gán = Chỉ của mình) chuyển vé mình đang giữ sang đội khác (ngoại lệ theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.14`) | Mở hộp xác nhận | Chuyển được; màn hình báo trước sau khi chuyển sẽ không còn thấy vé |
| `AC-34.8.8` | Người phụ trách A thuộc đơn vị mới, đang ở trạng thái Bận họp và đã đầy hạn mức | Chuyển vé của A sang đơn vị đó | A vẫn là Người phụ trách |
| `AC-34.8.6` | Nhân viên Hỗ trợ (Gán = Chỉ của mình) | Thử chuyển một vé chưa gán trong hàng đợi | Thao tác chuyển bị vô hiệu kèm giải thích ô Gán không bao phủ vé |
| `AC-34.8.7` | Người có toàn quyền thêm "Kế toán" vào danh sách được chuyển tới khi nhật ký cấu hình quyền gặp sự cố | Lưu | Báo lỗi, danh sách không đổi; bớt một hàng đợi khỏi danh sách trong cùng lúc thì lưu được và nhật ký được ghi bù |
| `AC-34.9.1` | A đang giữ vé, chưa đóng | A chọn "Trả về hàng đợi" kèm ghi chú | Vé chưa gán trong hàng đợi của đơn vị đang thuộc; ghi chú bàn giao xuất hiện dạng ghi chú nội bộ; hạn chót không đổi |
| `AC-34.9.2` | Vé đã đóng hoàn tất | Mở thao tác | Không có lựa chọn "Trả về hàng đợi" |
| `AC-34.9.3` | Nhân viên Hỗ trợ (Gán = Chỉ của mình) đang giữ vé | Chọn "Trả về hàng đợi" | Thực hiện được |
| `AC-34.10.1` | Hộp thư hotro@ đang là nguồn của Vé hỗ trợ; thành viên có quyền Cấu hình điều phối, không có toàn quyền | Mở cấu hình hộp thư | Thao tác "Chuyển phân hệ" bị vô hiệu kèm giải thích; không có lối kết nối hotro@ lần thứ hai cho Hội thoại Đa kênh |
| `AC-34.10.2` | Quản trị viên chuyển phân hệ hotro@ từ Vé hỗ trợ sang Hội thoại Đa kênh, xác nhận lúc 10:00 | Thư mới đến lúc 10:00:05 | Thư thành hội thoại, không tạo vé; vé cũ giữ nguyên ở phân hệ Vé hỗ trợ; thư trả lời vào một vé cũ chưa đóng vẫn nối vào vé đó |
| `AC-34.10.4` | Nhật ký thay đổi cấu hình quyền gặp sự cố | Người có toàn quyền chuyển phân hệ hotro@ | Thao tác bị hủy kèm báo lỗi; hộp thư giữ phân hệ cũ; thư mới vẫn vào phân hệ cũ |
| `AC-34.10.3` | Sau lượt chuyển trên | Mở nhật ký thay đổi cấu hình quyền | Có dòng ghi người thực hiện, thời điểm, phân hệ trước và sau |

---

### FEAT-10 — Phân bổ Tự động Xoay vòng theo Hạn mức Năng lực

**Mô tả nghiệp vụ:** Tự động phân bổ vé mới của một hàng đợi cho tư vấn viên đang trực theo cơ chế xoay vòng, chia đều khối lượng, không dồn quá tải cho một người.

**Vai trò sử dụng chính:** Người có quyền Cấu hình điều phối (bật và cấu hình quy tắc cho hàng đợi); Hệ thống (thực hiện).

**Điều kiện tiên quyết:** Hàng đợi bật phân bổ tự động.

**Luồng chính:**

1. Vé mới vào hàng đợi có bật phân bổ tự động (người nhận tiềm năng chịu cả `BR-12.8`).
2. Hệ thống lập tập người nhận đạt điều kiện (`BR-10.3`), lọc theo kỹ năng nếu bật (`FEAT-11`), loại người đã đạt hạn mức.
3. Chọn người kế tiếp theo thứ tự xoay vòng, gán vé, thông báo người nhận (`FEAT-37`).

**Quy tắc nghiệp vụ:**

- **`BR-10.1` (Hạn mức năng lực):** Người đang giữ số vé bằng hoặc vượt hạn mức của mình bị bỏ qua. Hạn mức của một người là hạn mức riêng của người đó nếu có (`CFG-10-02`), nếu không thì hạn mức mặc định của workspace (`CFG-10-01`). Để trống hạn mức riêng nghĩa là kế thừa: đổi hạn mức mặc định áp ngay cho mọi người đang kế thừa. Hạn mức áp cho phân bổ tự động; bàn giao thủ công theo `BR-12.6`.

  **Lý do nghiệp vụ:** năng lực của một chuyên viên lâu năm khác người mới; một con số chung buộc doanh nghiệp chọn giữa làm quá tải người mới và để trống năng lực của người giỏi.

- **`BR-10.2` (Cách tính số vé đang giữ):** Đếm vé chưa ở trạng thái kết thúc và **không** ở trạng thái tạm dừng cam kết, trên mọi hàng đợi. Vé con của sự cố diện rộng không tính (`BR-28.6`).

  **Lý do nghiệp vụ:** vé đang chờ khách không chiếm thời gian làm việc thực tế; lạm dụng trạng thái tạm dừng để né hạn mức được giám sát qua `BR-43.9`.

- **`BR-10.3` (Điều kiện của người nhận phân bổ tự động):** Hệ thống chỉ phân bổ cho người đồng thời: là thành viên đơn vị tiếp nhận của vé (hoặc của đơn vị trong nhánh khi hàng đợi gồm các đơn vị con); Đang hoạt động (người Tạm ngưng bị loại theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-42.1`); đang trong ca và ở trạng thái sẵn sàng (`BR-35.1`); có ô (Vé hỗ trợ, Sửa) và ô (Vé hỗ trợ, Gán) khác Không có; không bị nguồn chặn nào trên vé; không là tư vấn viên đang bị khiếu nại trên vé (`BR-12.8`). Lượt phân bổ là một lượt Gán do Hệ thống thực hiện thay cho việc nhận việc, ghi nhật ký kèm tên quy tắc đã áp.

  **Lý do nghiệp vụ:** hệ thống không được làm thay điều người nhận không tự làm được (Nguyên tắc 3); giao vé cho người không sửa được vé thì vé bị đóng băng, giao cho người đội khác thì vé lộ ra ngoài đơn vị tiếp nhận.

- **`BR-10.4` (Khi không còn ai nhận được):** Nếu mọi người đạt điều kiện đều đã đầy hạn mức, hoặc không còn ai đạt điều kiện, vé ở lại hàng đợi chưa gán và Trưởng nhóm được cảnh báo ngay; hệ thống thử phân bổ lại khi có người trở nên đạt điều kiện.

  **Lý do nghiệp vụ:** gán ép cho người quá tải hoặc người không xử lý được chỉ che giấu tình trạng thiếu người.

- **`BR-10.5` (Duy trì thứ tự xoay vòng):** Thứ tự xoay vòng duy trì liên tục theo từng hàng đợi, không đặt lại mỗi ngày hay khi hệ thống khởi động lại.

  **Lý do nghiệp vụ:** đặt lại thứ tự làm người đứng đầu danh sách luôn nhận nhiều vé hơn.

- **`BR-10.6` (Ai cấu hình phân bổ cho một hàng đợi):** Người có quyền Cấu hình điều phối bật, tắt và cấu hình phân bổ tự động cho hàng đợi mà ô (Vé hỗ trợ, Gán) của chính họ bao phủ vé của hàng đợi đó.

  **Lý do nghiệp vụ:** quy tắc phân bổ là cách gán vé hàng loạt; người không được tự gán vé của một đội không được cấu hình cách hệ thống gán vé của đội đó.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-10.1.1` | `CFG-10-01` = 10; A đặt hạn mức riêng 15, đang giữ 12 vé | Vé mới vào hàng đợi | A vẫn nằm trong tập được phân bổ |
| `AC-10.1.2` | B để trống hạn mức riêng | Đổi `CFG-10-01` từ 10 xuống 8 khi B đang giữ 8 vé | B bị bỏ qua ở lượt phân bổ kế tiếp |
| `AC-10.2.1` | A giữ 10 vé, 3 vé đang chờ khách hàng | Vé mới vào | A được tính đang giữ 7 vé, vẫn được phân bổ |
| `AC-10.3.1` | C thuộc đơn vị tiếp nhận nhưng ô (Vé, Sửa) = Không có | Vé mới vào | C không bao giờ được phân bổ |
| `AC-10.3.2` | D đang Tạm ngưng | Vé mới vào | D không được phân bổ |
| `AC-10.3.3` | E thuộc đội khác, đang sẵn sàng và còn hạn mức | Vé mới vào hàng đợi "Hỗ trợ" | E không được phân bổ |
| `AC-10.3.4` | Vé được phân bổ cho A | Mở nhật ký thay đổi của vé | Có dòng gán người phụ trách do Hệ thống thực hiện kèm tên quy tắc |
| `AC-10.4.1` | Mọi người đạt điều kiện đều đầy hạn mức | Vé mới vào | Vé ở lại hàng đợi chưa gán; Trưởng nhóm nhận cảnh báo |
| `AC-10.4.2` | Tiếp nối, A kết thúc một vé và còn chỗ | Quan sát | Vé đang chờ được phân bổ cho A |
| `AC-10.5.1` | Ba người A, B, C đủ điều kiện | Ba vé lần lượt phát sinh, hệ thống khởi động lại giữa vé thứ hai và thứ ba | Mỗi người nhận một vé |
| `AC-10.6.1` | Người có quyền Cấu hình điều phối, ô (Vé, Gán) chỉ bao phủ "Hỗ trợ – Hà Nội" | Mở cấu hình phân bổ của "Hỗ trợ – Đà Nẵng" | Ở dạng chỉ đọc kèm giải thích |

---

### FEAT-11 — Phân bổ theo Kỹ năng Chuyên môn

**Mô tả nghiệp vụ:** Điều hướng vé tới tư vấn viên có đúng kỹ năng (ví dụ vé tích hợp kỹ thuật tới người có kỹ năng kỹ thuật, vé tiếng Anh tới người xử lý được tiếng Anh) thay vì chia đều rồi chuyển tay nhiều lần.

**Vai trò sử dụng chính:** Người có quyền Cấu hình điều phối (danh mục kỹ năng, ánh xạ kỹ năng theo phân loại hoặc kênh); người có quyền Quản lý ca trực & kỹ năng (gán kỹ năng cho thành viên); Hệ thống.

**Điều kiện tiên quyết:** Hàng đợi bật phân bổ tự động và bật điều phối theo kỹ năng.

**Luồng chính:**

1. Hệ thống xác định kỹ năng vé cần.
2. Lọc tập người đạt `BR-10.3` có đủ kỹ năng, áp hạn mức và xoay vòng.
3. Không có ai đủ kỹ năng → ứng xử theo `CFG-11-01`.

**Quy tắc nghiệp vụ:**

- **`BR-11.1` (Khai báo kỹ năng):** Doanh nghiệp định nghĩa danh mục kỹ năng và gán cho từng tư vấn viên; một người nhiều kỹ năng, một kỹ năng nhiều người. Người có quyền Quản lý ca trực & kỹ năng chỉ gán kỹ năng cho thành viên của đơn vị mà ô (Vé hỗ trợ, Gán) của họ bao phủ vé.

  **Lý do nghiệp vụ:** gán kỹ năng là quyết định ai nhận loại vé nào; người không điều phối được vé của một đội không được quyết điều đó cho đội ấy.

- **`BR-11.2` (Xác định kỹ năng vé cần):** Kỹ năng yêu cầu suy ra từ phân loại và/hoặc kênh tiếp nhận theo ánh xạ do doanh nghiệp cấu hình; người có ô (Vé hỗ trợ, Gán) bao phủ vé điều chỉnh được cho từng vé.

  **Lý do nghiệp vụ:** ánh xạ tự động đúng cho đa số vé; ngoại lệ cần người điều phối sửa ngay trên vé.

- **`BR-11.3` (Lọc kỹ năng trước, xoay vòng sau):** Hệ thống xác định tập người đạt `BR-10.3` và có đủ kỹ năng trước, rồi mới áp hạn mức và thứ tự xoay vòng trong tập đó.

  **Lý do nghiệp vụ:** hai cơ chế bổ sung cho nhau; áp hạn mức trước sẽ loại người duy nhất đủ kỹ năng.

- **`BR-11.4` (Khi không có ai đủ kỹ năng):** Doanh nghiệp chọn một cách ứng xử (`CFG-11-01`): (a) **Chờ** — giữ vé trong hàng đợi tới khi có người đủ kỹ năng, cảnh báo Trưởng nhóm ngay; (b) **Hạ tiêu chuẩn** — giao cho người khớp nhiều kỹ năng nhất trong tập đạt `BR-10.3`, bằng nhau thì theo xoay vòng; nếu không ai khớp kỹ năng nào thì giao theo xoay vòng thông thường, ghi chú trên vé rằng người phụ trách chưa có kỹ năng yêu cầu và cảnh báo Trưởng nhóm; (c) **Chờ rồi hạ tiêu chuẩn** — chờ trong `CFG-11-02`, hết thời gian thì áp (b).

  **Lý do nghiệp vụ:** để vé không người phụ trách trong khi đồng hồ vẫn chạy thường là kết cục xấu hơn giao cho người chưa đủ kỹ năng; nhưng doanh nghiệp có sản phẩm đặc thù có thể ưu tiên chờ đúng người.

- **`BR-11.5` (Đồng hồ vẫn chạy khi chờ):** Thời gian chờ người đủ kỹ năng vẫn tính vào đồng hồ cam kết (`BR-34.7`).

  **Lý do nghiệp vụ:** Nguyên tắc 4.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-11.1.1` | Quản lý đội Hà Nội có quyền Quản lý ca trực & kỹ năng | Gán kỹ năng cho thành viên đội Đà Nẵng | Thành viên đội Đà Nẵng không có trong danh sách chọn |
| `AC-11.2.1` | Phân loại "Lỗi tích hợp" ánh xạ kỹ năng "Tích hợp kỹ thuật" | Vé được phân loại như vậy | Vé yêu cầu kỹ năng "Tích hợp kỹ thuật"; người có ô Gán bao phủ vé sửa được |
| `AC-11.3.1` | Người duy nhất đủ kỹ năng có 9/10 vé, người khác 2/10 nhưng không đủ kỹ năng | Vé cần kỹ năng đó | Giao cho người đủ kỹ năng |
| `AC-11.4.1` | `CFG-11-01` = Chờ; hai người đủ kỹ năng đều đầy hạn mức | Vé vào | Vé ở lại hàng đợi; Trưởng nhóm nhận cảnh báo ngay |
| `AC-11.4.2` | `CFG-11-01` = Chờ rồi hạ tiêu chuẩn, `CFG-11-02` = 15 phút; vé cần 2 kỹ năng; D, E khớp 1, F khớp 0 | Hết 15 phút | Vé giao cho D hoặc E theo xoay vòng, không cho F |
| `AC-11.4.3` | Hạ tiêu chuẩn, không ai khớp kỹ năng nào | Vé vào | Giao theo xoay vòng; vé có ghi chú "Người phụ trách chưa có kỹ năng yêu cầu"; Trưởng nhóm được cảnh báo |
| `AC-11.5.1` | Vé chờ 15 phút theo cách (c) | Quan sát đồng hồ Phản hồi Đầu tiên | 15 phút chờ đã được tính |

---

### FEAT-12 — Bàn giao & Phân công Lại Vé

**Mô tả nghiệp vụ:** Chuyển giao vé đang xử lý sang tư vấn viên khác trong cùng đơn vị tiếp nhận — vì xử lý sai người, cần chuyên môn cao hơn, hoặc người đang giữ không tiếp tục được. Có hai đường, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`: **giao trực tiếp** (cần ô Gán từ mức Đơn vị của mình trở lên bao phủ vé, không cần người nhận chấp nhận) và **đề nghị chuyển** (Người phụ trách hiện tại, kể cả khi ô Gán chỉ là Chỉ của mình; chỉ có hiệu lực khi người nhận chấp nhận). Muốn giao cho người của đội khác thì chuyển hàng đợi (`BR-34.8`).

**Vai trò sử dụng chính:** Người phụ trách hiện tại (đề nghị chuyển vé mình giữ); Quản lý (giao trực tiếp vé trong phạm vi).

**Điều kiện tiên quyết:** Giao trực tiếp: ô (Vé hỗ trợ, Gán) của người thực hiện ở mức Đơn vị của mình trở lên và bao phủ vé. Đề nghị chuyển: người thực hiện là Người phụ trách hiện tại; danh sách người nhận đã lọc sẵn theo `BR-12.7`.

**Luồng chính:**

1. **Giao trực tiếp:** chọn người nhận trong danh sách người đạt `BR-12.6`, nhập ghi chú bàn giao → vé đổi Người phụ trách ngay; người nhận được thông báo, từ chối được trong thời hạn cho phép (`BR-12.4`).
2. **Đề nghị chuyển:** Người phụ trách hiện tại chọn một người cụ thể, nhập ghi chú bàn giao → người đó nhận đề nghị; vé vẫn thuộc người đề nghị cho tới khi người nhận chấp nhận (`BR-12.7`).

**Luồng ngoại lệ:**

- Người nhận đã đạt hạn mức → cảnh báo, vẫn giao được nếu người thực hiện xác nhận.
- Người nhận không Đang hoạt động, không thuộc đơn vị tiếp nhận, hoặc ô (Vé hỗ trợ, Sửa) = Không có → không có trong danh sách chọn.
- Người thực hiện có ô Gán chỉ là Chỉ của mình → không có thao tác giao trực tiếp; chỉ có Đề nghị chuyển, Trả về hàng đợi (`BR-34.9`) và chuyển hàng đợi (`BR-34.8`).
- Đề nghị chuyển quá hạn mà chưa được chấp nhận → đề nghị bị hủy, người đề nghị được báo.

**Quy tắc nghiệp vụ:**

- **`BR-12.1` (Ghi chú bàn giao):** Mỗi lần bàn giao bắt buộc kèm ghi chú nêu lý do và tóm tắt việc đã làm, lưu dạng ghi chú nội bộ trên vé.

  **Lý do nghiệp vụ:** người nhận không phải đọc lại toàn bộ lịch sử; Trưởng nhóm đối soát được khi vé bị chuyển lòng vòng.

- **`BR-12.2` (Cam kết không đặt lại):** Bàn giao không đặt lại đồng hồ cam kết; mốc đã hoàn tất giữ nguyên, hạn chót giữ nguyên.

  **Lý do nghiệp vụ:** cam kết là với khách hàng, không phụ thuộc việc nội bộ đổi người (Nguyên tắc 4).

- **`BR-12.3` (Thông báo người nhận):** Người nhận được thông báo ngay (`FEAT-37`), kèm tên người bàn giao và lý do.

  **Lý do nghiệp vụ:** vé được giao mà người nhận không biết thì đồng hồ chạy vô ích.

- **`BR-12.4` (Từ chối nhận vé được giao trực tiếp):** Người được giao trực tiếp từ chối được kèm lý do trong `CFG-12-01`; vé về hàng đợi (`BR-34.9`) và Trưởng nhóm được thông báo.

  **Lý do nghiệp vụ:** người nhận có thể đang xử lý sự cố khẩn khác; trả về hàng đợi để người khác nhận thay vì để vé nằm im với người không làm.

- **`BR-12.5` (Cảnh báo chuyển lòng vòng):** Vé bị chuyển quá `CFG-12-02` lần mà chưa xử lý xong thì Trưởng nhóm được cảnh báo.

  **Lý do nghiệp vụ:** vé bị đùn đẩy trong khi đồng hồ vẫn chạy cần quản lý can thiệp trực tiếp.

- **`BR-12.6` (Giao trực tiếp và người được nhận):** Giao trực tiếp cần ô (Vé hỗ trợ, Gán) của người thực hiện ở mức Đơn vị của mình trở lên bao phủ vé, và vé sau khi giao vẫn nằm trong mức Gán đó theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6` — luôn thỏa khi người nhận thuộc đơn vị tiếp nhận, vì vé không đổi đơn vị (`BR-34.1`). Ô Gán mức Chỉ của mình không giao trực tiếp được. Người nhận phải Đang hoạt động, là thành viên của đơn vị tiếp nhận của vé (hoặc của đơn vị trong nhánh khi hàng đợi gồm các đơn vị con), có ô (Vé hỗ trợ, Sửa) khác Không có, không bị nguồn chặn nào trên vé, và không là tư vấn viên đang bị khiếu nại trên vé (`BR-12.8`). Người thực hiện chỉ tự gán cho mình một vé đang có người phụ trách khác khi mức của chính mình trên mọi ô bị ảnh hưởng (Sửa, Xoá, Xuất, Gán, Kết thúc vé) đã bao phủ vé trước khi gán, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 2.4; nhận vé chưa gán từ hàng đợi là ngoại lệ theo `BR-34.4`.

  **Lý do nghiệp vụ:** giao cho người không sửa được thì vé bị đóng băng; tự lấy vé của đồng nghiệp về mình là đường để mở cho mình quyền mà vai trò không có; giao trực tiếp không cần người nhận đồng ý nên chỉ dành cho người điều phối được cả đơn vị.

- **`BR-12.7` (Đề nghị chuyển của Người phụ trách hiện tại):** Người phụ trách hiện tại, kể cả khi ô Gán chỉ là Chỉ của mình, đề nghị chuyển được vé của mình cho một người cụ thể kèm ghi chú bàn giao (`BR-12.1`). **Danh sách người nhận khi gửi đề nghị chỉ gồm người đã đạt mọi điều kiện sau**, và mọi điều kiện được **kiểm lại lúc chấp nhận**: Đang hoạt động; là thành viên đơn vị tiếp nhận của vé (`BR-12.6`); ô (Vé hỗ trợ, Sửa) và ô (Vé hỗ trợ, Gán) khác Không có; đã xem được vé theo thứ tự hợp nhất quyền tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`; không bị nguồn chặn nào trên vé; và không là tư vấn viên đang bị khiếu nại trên vé (`BR-12.8`). Đề nghị chỉ có hiệu lực khi người nhận chấp nhận; trong lúc chờ, vé vẫn thuộc người đề nghị và đồng hồ cam kết vẫn chạy. Người nhận khước từ được; đề nghị chưa được chấp nhận hết hạn theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-25-01`, khi đó đề nghị bị hủy và người đề nghị được báo. Mỗi vé có tối đa một đề nghị đang chờ. Đề nghị đang chờ **bị hủy** (người đề nghị và người nhận được báo) khi vé đổi Người phụ trách bằng bất kỳ đường nào — giao trực tiếp, trả về hàng đợi, chuyển hàng đợi, leo thang tự động chuyển người (`BR-18.2`), chuyển hàng loạt hay bước trong quy trình tạm ngưng, rời workspace, đổi Đơn vị chính (`FEAT-36`) — và khi vé chuyển sang trạng thái kết thúc, bị gộp vào vé khác hoặc bị xóa.

  **Lý do nghiệp vụ:** bàn giao vé của mình là việc hằng ngày của tư vấn viên; nhưng người chỉ có Gán = Chỉ của mình không được đẩy vé sang người khác mà người đó không tự nhận được — chấp nhận là lúc người nhận dùng chính quyền nhận việc của mình.

- **`BR-12.8` (Người đang bị khiếu nại không nhận lại vé):** Tư vấn viên là đối tượng của một vé khiếu nại đang mở liên kết với vé (`BR-39.3`) không được nhận vé đó bằng bất kỳ đường nào — giao trực tiếp (`BR-12.6`), đề nghị chuyển (`BR-12.7`), nhận việc từ hàng đợi, phân bổ tự động, leo thang tự động chuyển người (`BR-18.2`), chuyển hàng loạt, và bước bàn giao trong quy trình chung (`BR-36.7`, `BR-36.9`) — cho tới khi vé khiếu nại đóng. Đây là **điều kiện riêng của phân hệ Vé hỗ trợ**, không phải nguồn chặn trên vé gốc theo thứ tự hợp nhất quyền: người đó vẫn xem vé gốc bình thường.

  **Lý do nghiệp vụ:** khách khiếu nại về chính người phục vụ thì vé không được quay lại người đó; nhưng chặn quyền xem vé gốc sẽ làm dòng thời gian bị khuyết với chính họ và lộ ra rằng có khiếu nại.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-12.1.1` | A bàn giao vé cho B | Bỏ trống ghi chú | Nút xác nhận bị vô hiệu cho tới khi nhập ghi chú |
| `AC-12.2.1` | Vé đã có phản hồi đầu tiên, còn 4 giờ tới hạn xử lý | Bàn giao cho B | Mốc phản hồi vẫn hoàn tất; hạn xử lý không đổi |
| `AC-12.3.1` | A bàn giao cho B | — | B nhận thông báo kèm tên A và lý do |
| `AC-12.4.1` | `CFG-12-01` = 30 phút | B từ chối sau 10 phút kèm lý do | Vé chưa gán trong hàng đợi của đơn vị tiếp nhận; Trưởng nhóm nhận thông báo |
| `AC-12.4.2` | Đã quá 30 phút | B mở vé | Không còn lựa chọn từ chối; B dùng "Trả về hàng đợi" nếu cần |
| `AC-12.5.1` | `CFG-12-02` = 3 | Vé bị chuyển lần thứ tư | Trưởng nhóm nhận cảnh báo |
| `AC-12.6.1` | Danh sách người nhận | Mở hộp chọn | Không có người Tạm ngưng, người ngoài đơn vị tiếp nhận, người có (Vé, Sửa) = Không có |
| `AC-12.6.2` | Người nhận đã đầy hạn mức | Quản lý chọn người đó | Cảnh báo hạn mức; xác nhận thì giao được |
| `AC-12.6.3` | Nhân viên Hỗ trợ (Sửa = Chỉ của mình, Gán = Chỉ của mình) | Tìm thao tác tự gán vé đang do đồng nghiệp phụ trách | Không có; giải thích mức của mình không bao phủ vé |
| `AC-12.6.4` | Nhân viên Hỗ trợ A (Gán = Chỉ của mình) đang giữ vé | Mở thao tác bàn giao | Không có giao trực tiếp; có Đề nghị chuyển và Trả về hàng đợi |
| `AC-12.7.1` | A đề nghị chuyển vé cho B kèm ghi chú | B chưa chấp nhận | Vé vẫn thuộc A; B thấy đề nghị kèm ghi chú |
| `AC-12.7.2` | B có (Vé, Gán) = Chỉ của mình, thuộc đơn vị tiếp nhận | B chấp nhận | Vé thuộc B; ghi chú bàn giao nằm trên vé dạng ghi chú nội bộ; hạn chót không đổi |
| `AC-12.7.3` | C thuộc đơn vị tiếp nhận nhưng có (Vé, Gán) = Không có | A mở danh sách người nhận đề nghị | C không có trong danh sách |
| `AC-12.7.6` | Vé có vé khiếu nại đang mở về tư vấn viên D | A mở danh sách người nhận đề nghị | D không có trong danh sách |
| `AC-12.8.1` | Như trên | Trưởng nhóm mở danh sách giao trực tiếp; D mở hàng đợi sau khi vé được trả về | D không có trong danh sách giao; D thấy vé nhưng nút Nhận việc bị vô hiệu kèm giải thích; D vẫn xem được vé gốc |
| `AC-12.8.2` | Vé khiếu nại về D đã đóng | Trưởng nhóm giao trực tiếp vé cho D | Giao được |
| `AC-12.7.7` | Đề nghị gửi cho B; sau đó B bị gỡ khỏi đơn vị tiếp nhận | B bấm chấp nhận | Chấp nhận bị vô hiệu kèm giải thích; vé vẫn thuộc A |
| `AC-12.7.8` | Đề nghị gửi cho B đang chờ | Mốc leo thang tự động chuyển vé cho E, hoặc A kết thúc vé | Đề nghị bị hủy; A và B được báo |
| `AC-12.7.4` | Đề nghị gửi cho B; quá [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-25-01` | Hết hạn | Đề nghị bị hủy; vé vẫn thuộc A; A được báo |
| `AC-12.7.5` | B Tạm ngưng sau khi nhận đề nghị | Đề nghị đang chờ | B không chấp nhận được; vé vẫn thuộc A |

---

### FEAT-35 — Trạng thái Sẵn sàng & Ca trực của Tư vấn viên

**Mô tả nghiệp vụ:** Hệ thống cần biết ai đang thực sự trực để phân bổ đúng người. Tư vấn viên tự đặt trạng thái sẵn sàng; doanh nghiệp khai báo lịch trực, người trực ngoài giờ và ghi nhận kỳ nghỉ phép đã duyệt từ quy trình nhân sự.

**Vai trò sử dụng chính:** Tư vấn viên (tự đặt trạng thái); người có quyền Quản lý ca trực & kỹ năng (xếp lịch trực, người trực ngoài giờ, ghi nhận nghỉ phép).

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Xếp lịch trực theo người và khung giờ cho từng đơn vị tiếp nhận.
2. Tư vấn viên đặt trạng thái: Sẵn sàng / Tạm rời chỗ / Bận họp.
3. Hệ thống dùng lịch trực và trạng thái để lọc người nhận phân bổ (`BR-10.3`).

**Quy tắc nghiệp vụ:**

- **`BR-35.1` (Chỉ phân bổ cho người đang sẵn sàng):** Phân bổ tự động chỉ xét người đang trong ca và ở trạng thái Sẵn sàng; người ngoài ca, đang nghỉ phép đã ghi nhận, hoặc Tạm rời chỗ, Bận họp bị bỏ qua.

  **Lý do nghiệp vụ:** giao vé cho người không có mặt là để vé trôi qua hạn.

- **`BR-35.2` (Không còn ai sẵn sàng):** Không còn người sẵn sàng thì vé ở lại hàng đợi và Trưởng nhóm được thông báo ngay (`BR-10.4`).

  **Lý do nghiệp vụ:** gán bừa cho người đang nghỉ che giấu tình trạng thiếu người trực.

- **`BR-35.3` (Vé đang giữ khi hết ca):** Khi tư vấn viên kết thúc ca mà còn vé chưa xử lý xong, hệ thống hiển thị danh sách để họ đề nghị chuyển cho người của ca sau (`BR-12.7`), giao trực tiếp nếu ô Gán của họ cho phép (`BR-12.6`), hoặc trả về hàng đợi (`BR-34.9`). Không tự chuyển ngầm.

  **Lý do nghiệp vụ:** người tiếp nhận cần ghi chú bàn giao (`BR-12.1`); chuyển ngầm làm mất bối cảnh.

- **`BR-35.4` (Nghỉ phép đã duyệt):** Kỳ nghỉ phép đã duyệt (từ quy trình nhân sự) được ghi nhận để phân bổ tránh; khi kỳ nghỉ được ghi nhận, Trưởng nhóm được cảnh báo trước về số vé đang mở của người sắp nghỉ để lên kế hoạch chuyển giao (`FEAT-36`).

  **Lý do nghiệp vụ:** duyệt nghỉ thuộc quy trình nhân sự; phân hệ chỉ cần biết để không giao việc và để chuyển giao kịp.

- **`BR-35.5` (Lịch trực phải phủ lịch cam kết):** Hệ thống đối chiếu lịch trực với lịch làm việc của các chính sách cam kết đang dùng và cảnh báo người có quyền Quản lý ca trực & kỹ năng cùng người có quyền Cấu hình cam kết dịch vụ khi có khung giờ nằm trong cam kết mà không ai trực, nêu rõ khung giờ.

  **Lý do nghiệp vụ:** bán cam kết phục vụ liên tục mà chỉ xếp ca tới 18:00 thì vé lúc 2 giờ sáng chạy đồng hồ mà không ai nhận; lỗi chỉ lộ ra khi đã vi phạm với khách.

- **`BR-35.6` (Người trực ngoài giờ):** Với khung giờ ngoài ca thông thường nhưng thuộc cam kết, doanh nghiệp chỉ định người trực ngoài giờ cho từng hàng đợi; người được chỉ định phải đạt `BR-10.3` (trừ điều kiện trong ca). Vé phát sinh trong khung đó gửi cảnh báo tới người này qua kênh đánh thức (`BR-37.4`).

  **Lý do nghiệp vụ:** thông báo trong ứng dụng lúc 2 giờ sáng không ai đọc; người trực phải là người xử lý được vé.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-35.1.1` | A trực và Sẵn sàng, B nghỉ phép đã ghi nhận, C hết ca | Vé mới cần phân bổ | Vé giao cho A |
| `AC-35.1.2` | A chuyển Tạm rời chỗ | Vé mới | A bị bỏ qua |
| `AC-35.2.1` | Không còn ai Sẵn sàng | Vé mới | Vé ở lại hàng đợi; Trưởng nhóm nhận thông báo ngay |
| `AC-35.3.1` | A (Gán = Chỉ của mình) còn 3 vé mở khi hết ca | A bấm kết thúc ca | Danh sách 3 vé hiện ra với lựa chọn đề nghị chuyển hoặc trả về hàng đợi; không vé nào tự đổi người |
| `AC-35.4.1` | Kỳ nghỉ của B được ghi nhận, B có 12 vé mở | — | Trưởng nhóm nhận cảnh báo "B nghỉ từ ngày… đang có 12 vé mở" |
| `AC-35.5.1` | Chính sách phục vụ liên tục; lịch trực 08:00–18:00 | Lưu lịch trực | Cảnh báo nêu khung 18:00–08:00 không ai trực |
| `AC-35.6.1` | Chỉ định người trực ngoài giờ có (Vé, Sửa) = Không có | Lưu | Người đó không có trong danh sách chọn |
| `AC-35.6.2` | Người trực ngoài giờ D cho khung 18:00–08:00 | Vé phát sinh 02:00 | D nhận cảnh báo qua kênh đánh thức |

---

### FEAT-36 — Chuyển Vé Hàng loạt khi Nhân sự Vắng mặt, Tạm ngưng hoặc Rời đi

**Mô tả nghiệp vụ:** Khi một tư vấn viên nghỉ ốm đột xuất, nghỉ dài, bị tạm ngưng hoặc rời workspace, toàn bộ vé đang mở của họ phải có điểm đến nhanh. Tạm ngưng và rời workspace là quy trình chung của phân hệ Phân quyền ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-42`, `FEAT-43`); tính năng này quy định phần thuộc vé.

**Vai trò sử dụng chính:** Quản lý (chuyển hàng loạt); người có quyền Tạm ngưng người dùng hoặc Gỡ người dùng (trong quy trình tạm ngưng, rời workspace).

**Điều kiện tiên quyết:** Chuyển hàng loạt thủ công: người thực hiện có ô (Vé hỗ trợ, Gán) bao phủ các vé cần chuyển. Bước xử lý vé trong quy trình chung (tạm ngưng, rời workspace, đổi Đơn vị chính): theo `BR-36.9`, không cần ô (Vé hỗ trợ, Gán).

**Luồng chính:**

1. Chọn một tư vấn viên → xem danh sách vé đang mở của họ (vé chưa ở trạng thái đóng hoàn tất).
2. Chọn tất cả hoặc một phần; chọn một hay nhiều người nhận, hoặc trả về hàng đợi; nhập ghi chú bàn giao chung.
3. Hệ thống đề xuất chia theo hạn mức còn trống; người thực hiện điều chỉnh và xác nhận.

**Quy tắc nghiệp vụ:**

- **`BR-36.1` (Chuyển toàn bộ vé của một người):** Chuyển tất cả hoặc một phần vé đang mở của một người trong một thao tác. Vé mà ô Gán của người thực hiện không bao phủ được liệt kê riêng là "ngoài phạm vi", không bị bỏ qua âm thầm.

  **Lý do nghiệp vụ:** mở từng vé để chuyển trong khi đồng hồ vẫn chạy trực tiếp gây vi phạm; nhưng thao tác hàng loạt không được vượt quyền của người làm.

- **`BR-36.2` (Phân bổ lại theo năng lực):** Khi chuyển cho nhiều người, hệ thống đề xuất chia theo hạn mức còn trống (`BR-10.1`); mọi người nhận phải đạt `BR-12.6`.

  **Lý do nghiệp vụ:** chuyển hết cho một người chỉ dời tình trạng quá tải sang chỗ khác.

- **`BR-36.3` (Ghi chú bàn giao chung):** Một ghi chú chung được ghi vào từng vé dưới dạng ghi chú nội bộ.

  **Lý do nghiệp vụ:** đáp ứng `BR-12.1` mà không bắt nhập lại cho từng vé.

- **`BR-36.4` (Khi tư vấn viên bị tạm ngưng):** Tạm ngưng thực hiện ngay, không chờ xử lý vé và không bị chặn khi nhật ký gặp sự cố (ghi bù theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.6`). Sau đó, như một thao tác riêng, người thực hiện chọn cho vé đang mở: giữ nguyên, trả về hàng đợi (`BR-34.9`), hoặc chuyển tạm cho người xử lý thay đạt `BR-36.9` — theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-42.4`. Nếu chọn giữ nguyên, vé đứng tên người Tạm ngưng được đánh dấu "Người phụ trách đang tạm ngưng" trên danh sách và bảng điều khiển hàng đợi (`BR-42.1`), và Trưởng nhóm nhận thông báo kèm số vé. Khi người đó được kích hoạt lại, đề xuất trả về người đó các vé người xử lý thay vẫn đang phụ trách được gửi tới người xử lý thay và quản lý trực tiếp; vé đã được trả về hàng đợi hoặc đã được người khác nhận từ hàng đợi không thuộc đề xuất, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-42.4`.

  **Lý do nghiệp vụ:** cắt truy cập phải nhanh và không phụ thuộc việc tìm người thay; nhưng vé đứng tên người không vào được hệ thống thì đồng hồ chạy mà không ai làm, nên đội phải nhìn thấy những vé này.

- **`BR-36.5` (Khi tư vấn viên rời workspace):** Trong quy trình rời workspace ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-43`), vé đang mở của người đó là bước bắt buộc: chọn người nhận đạt `BR-36.9` hoặc trả về hàng đợi. Vé đã đóng hoàn tất giữ tên người xử lý làm dữ liệu lịch sử và báo cáo hiệu suất, không cần người nhận.

  **Lý do nghiệp vụ:** gỡ trước, bàn giao sau thì vé vô chủ; còn vé đã đóng là lịch sử, đổi tên người xử lý làm sai báo cáo hiệu suất.

- **`BR-36.6` (Giữ nguyên cam kết):** Chuyển hàng loạt không đặt lại đồng hồ cam kết của vé nào (`BR-12.2`).

  **Lý do nghiệp vụ:** Nguyên tắc 4.

- **`BR-36.7` (Đổi Đơn vị chính):** Khi tư vấn viên đổi Đơn vị chính, vé chưa đóng hoàn tất mà người đó đang giữ là một **bước bắt buộc** trong danh sách bước chuyển phòng theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-11.9`, `BR-11.5`. Lựa chọn theo từng vé hoặc cho cả nhóm vé: **bàn giao lại** cho người đạt `BR-36.9`, hoặc **trả về hàng đợi** (`BR-34.9`). Không có lựa chọn "đi theo người" (vé là bản ghi công việc, `BR-34.1`), và không lựa chọn nào được chọn sẵn; chưa chọn thì không hoàn tất được việc đổi Đơn vị chính. Vé mà người đó vẫn là thành viên đơn vị tiếp nhận sau khi đổi (ví dụ qua kiêm nhiệm) có thêm lựa chọn **giữ nguyên**, cũng không chọn sẵn.

  **Lý do nghiệp vụ:** chuyển phòng mà vé vẫn đứng tên người đã sang đội khác thì không ai xử lý; để hệ thống chọn sẵn thì không ai thực sự quyết số phận của vé.

- **`BR-36.8` (Kiêm nhiệm hết hạn hoặc bị gỡ):** Khi một Đơn vị kiêm nhiệm của tư vấn viên hết hạn hoặc bị gỡ, vé đang mở của hàng đợi đơn vị đó mà người đó đang giữ được **đề xuất** trả về hàng đợi cho quản lý trực tiếp và Trưởng nhóm của đơn vị đó, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-11.10`. Đây là đề xuất, không phải bước chặn, áp như nhau cho kiêm nhiệm hết hạn theo ngày và kiêm nhiệm bị người có quyền gỡ thủ công.

  **Lý do nghiệp vụ:** hết làm việc cho đội kia thì vé còn đứng tên họ sẽ không ai xử lý; vé ở lại đơn vị tiếp nhận (`BR-34.1`) nên điểm đến tự nhiên là hàng đợi của đơn vị đó. Kiêm nhiệm hết hạn tự xảy ra theo ngày, không có người thực hiện để đặt bước bắt buộc. Gỡ kiêm nhiệm thủ công thường là thao tác thu hẹp quyền cần hiệu lực ngay (ví dụ người đó không còn hỗ trợ đội kia, hoặc phải cắt quyền khẩn) — chặn nó bằng một bước xử lý vé là trái nguyên tắc thu hồi không chờ việc khác (Nguyên tắc 5); vé vẫn hiện với Trưởng nhóm qua đề xuất và bảng điều khiển.

- **`BR-36.9` (Bước xử lý vé trong quy trình chung):** Trong quy trình tạm ngưng, rời workspace và đổi Đơn vị chính của phân hệ Phân quyền, người thực hiện quy trình xử lý vé của người bị ảnh hưởng **không cần** ô (Vé hỗ trợ, Gán) bao phủ các vé đó. Bước xử lý vé là thao tác nới rộng chịu [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.5` (không ghi được nhật ký thì bước bị hủy, riêng việc tạm ngưng hay gỡ vẫn có hiệu lực). Người nhận vé phải đạt [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.2`, `BR-43.4`, đồng thời là thành viên đơn vị tiếp nhận của vé, không bị nguồn chặn nào trên vé và không là tư vấn viên đang bị khiếu nại trên vé (như `BR-12.6`, `BR-12.8`). Trả về hàng đợi luôn chọn được.

  **Lý do nghiệp vụ:** người xử lý nghỉ việc thường là nhân sự hoặc quản trị, không phải người điều phối vé; buộc họ có ô Gán trên vé thì quy trình chung không hoàn tất được. Ràng buộc được giữ ở người nhận, nơi quyền thật sự được trao.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-36.1.1` | A nghỉ ốm, giữ 10 vé; Quản lý có ô Gán bao phủ 9 vé, 1 vé thuộc đội khác | Chọn chuyển toàn bộ | 9 vé chuyển trong một thao tác; 1 vé được liệt kê "ngoài phạm vi" |
| `AC-36.2.1` | B còn 6 chỗ, C còn 3 chỗ | Chuyển 9 vé cho B và C | Đề xuất 6 cho B, 3 cho C; sửa được trước khi xác nhận |
| `AC-36.3.1` | Ghi chú chung "Chuyển do A nghỉ ốm" | Xác nhận | Ghi chú xuất hiện dạng ghi chú nội bộ trên từng vé |
| `AC-36.4.1` | A có 12 vé mở | Tạm ngưng A | A bị đăng xuất ngay; sau đó màn hình đề nghị xử lý 12 vé |
| `AC-36.4.2` | Tạm ngưng A, chọn giữ nguyên | Trưởng nhóm mở bảng điều khiển | 12 vé mang dấu "Người phụ trách đang tạm ngưng"; Trưởng nhóm đã nhận thông báo |
| `AC-36.4.3` | Nhật ký cấu hình quyền đang gặp sự cố | Tạm ngưng A | Tạm ngưng thực hiện; nhật ký được ghi bù khi phục hồi |
| `AC-36.4.4` | A được kích hoạt lại; trong thời gian tạm ngưng 5 vé đã chuyển tạm cho B và còn mở, 3 vé được trả về hàng đợi (1 trong số đó đã được C nhận) | — | B và quản lý trực tiếp của A nhận đề xuất trả 5 vé về A; 3 vé đã trả về hàng đợi, kể cả vé C đã nhận, không có trong đề xuất |
| `AC-36.5.1` | R rời workspace, có 30 vé chưa đóng và 400 vé đã đóng hoàn tất | Mở Rời workspace | Bước vé ghi "30 vé" bắt buộc; 400 vé đã đóng không thuộc bước này |
| `AC-36.5.2` | Như trên | Chọn trả về hàng đợi | 30 vé chưa gán trong hàng đợi của đơn vị tiếp nhận của từng vé |
| `AC-36.6.1` | Chuyển 10 vé | Mở từng vé | Hạn chót không đổi |
| `AC-36.7.1` | E chuyển Đơn vị chính từ "Hỗ trợ – Hà Nội" sang "Kỹ thuật", đang giữ 6 vé mở của "Hỗ trợ – Hà Nội" | Mở danh sách bước chuyển phòng | Có bước "6 vé" bắt buộc, không lựa chọn nào được chọn sẵn, không có "đi theo người"; nút hoàn tất bị vô hiệu cho tới khi chọn |
| `AC-36.7.2` | Tiếp nối, chọn trả về hàng đợi | Hoàn tất | 6 vé chưa gán trong hàng đợi "Hỗ trợ – Hà Nội" |
| `AC-36.7.3` | E chuyển Đơn vị chính sang "Kỹ thuật" nhưng vẫn kiêm nhiệm "Hỗ trợ – Hà Nội", đang giữ 6 vé của đơn vị đó | Mở bước vé | Có thêm lựa chọn "giữ nguyên", không chọn sẵn; chọn giữ nguyên thì 6 vé vẫn thuộc E và vẫn thuộc "Hỗ trợ – Hà Nội" |
| `AC-36.9.1` | Người có quyền Gỡ người dùng, ô (Vé hỗ trợ, Gán) = Không có, chạy Rời workspace cho R có 30 vé | Chọn người nhận F thuộc đơn vị tiếp nhận, có ô Sửa | Bước thực hiện được; F nhận 30 vé |
| `AC-36.9.4` | Rời workspace cho R; một vé của R có vé khiếu nại đang mở về F | Chọn người nhận cho vé đó | F không có trong danh sách chọn |
| `AC-36.9.2` | Như trên | Chọn người nhận không thuộc đơn vị tiếp nhận của vé | Người đó không có trong danh sách chọn |
| `AC-36.9.3` | Nhật ký cấu hình quyền gặp sự cố khi tạm ngưng A rồi chuyển tạm 12 vé cho B | Xác nhận | A vẫn bị tạm ngưng; bước chuyển 12 vé bị hủy kèm báo lỗi, vé giữ nguyên |
| `AC-36.8.1` | D kiêm nhiệm "Hỗ trợ" tới ngày 30, đang giữ 4 vé mở của "Hỗ trợ" | Ngày 31 | Quản lý trực tiếp của D và Trưởng nhóm "Hỗ trợ" nhận đề xuất trả 4 vé về hàng đợi; vé chưa tự đổi người |

## E. TÁC NGHIỆP XỬ LÝ & PHẢN HỒI KHÁCH HÀNG

### FEAT-13 — Dòng Trao đổi Vé Hỗ trợ

**Mô tả nghiệp vụ:** Màn hình hợp nhất toàn bộ nội dung trên vé theo thứ tự thời gian, phân biệt ba loại: phản hồi công khai gửi khách hàng, ghi chú nội bộ, thông báo hệ thống tự sinh (đổi trạng thái, đổi người phụ trách, chuyển hàng đợi).

**Vai trò sử dụng chính:** Khách hàng (phần công khai); người có ô (Vé hỗ trợ, Xem) bao phủ vé (toàn bộ); người có ô (Vé hỗ trợ, Sửa) bao phủ vé (gửi nội dung).

**Điều kiện tiên quyết:** Vé tồn tại.

**Luồng chính:**

1. Mở vé → dòng trao đổi hiển thị theo thời gian.
2. Người có ô Sửa bao phủ vé soạn phản hồi công khai hoặc ghi chú nội bộ và gửi.

**Quy tắc nghiệp vụ:**

- **`BR-13.1` (Bất biến sau khi gửi):** Nội dung trao đổi và ghi chú không sửa, không xóa được sau khi gửi; muốn đính chính thì gửi nội dung mới. Ngoại lệ duy nhất là khử định danh theo `FEAT-45`.

  **Lý do nghiệp vụ:** lịch sử xử lý là bằng chứng khi tranh chấp "ai đã hứa gì"; sửa được thì mất giá trị bằng chứng.

- **`BR-13.2` (Phân biệt ba loại nội dung):** Giao diện phân biệt rõ ba loại bằng dấu hiệu trực quan.

  **Lý do nghiệp vụ:** tránh gửi nhầm nội dung nội bộ cho khách hàng (`BR-14.1`).

- **`BR-13.3` (Một dòng thời gian thống nhất):** Mọi nội dung hiển thị theo thứ tự phát sinh, không tách theo loại.

  **Lý do nghiệp vụ:** người đọc nắm đúng diễn biến xử lý như đã xảy ra.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-13.1.1` | Phản hồi đã gửi nhầm | Tìm thao tác sửa, xóa | Không có; có lối "Gửi đính chính" |
| `AC-13.1.2` | Như trên | Gửi lời gọi sửa trực tiếp tới hệ thống, không qua giao diện | Bị từ chối |
| `AC-13.2.1` | Vé có cả ba loại nội dung | Mở vé | Mỗi loại có dấu hiệu riêng dễ phân biệt |
| `AC-13.3.1` | Ghi chú nội bộ viết giữa hai phản hồi | Mở vé | Ghi chú nằm đúng vị trí thời gian giữa hai phản hồi |

---

### FEAT-14 — Ghi chú Nội bộ giữa Nhân viên

**Mô tả nghiệp vụ:** Ghi chú nội bộ để trao đổi giải pháp giữa nhân viên; khách hàng không bao giờ thấy ở bất kỳ giao diện nào tiếp xúc với khách.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Sửa) bao phủ vé.

**Điều kiện tiên quyết:** Vé tồn tại.

**Luồng chính:**

1. Chọn chế độ Ghi chú nội bộ, soạn, nhắc tên đồng nghiệp nếu cần, gửi.
2. Người được nhắc tên nhận thông báo.

**Quy tắc nghiệp vụ:**

- **`BR-14.1` (Phân biệt rõ trên giao diện):** Khung soạn ghi chú nội bộ khác biệt rõ với khung phản hồi công khai, cả trước và sau khi gửi.

  **Lý do nghiệp vụ:** gửi nhầm nội dung nội bộ cho khách là lỗi nghiêm trọng và không thu hồi được.

- **`BR-14.2` (Nhắc tên đồng nghiệp):** Nhắc tên được đồng nghiệp; người được nhắc nhận thông báo (`FEAT-37`) kèm đường dẫn. Người được nhắc chỉ mở được vé nếu mức Xem của họ bao phủ vé; nếu không, người nhắc được báo ngay khi chọn tên.

  **Lý do nghiệp vụ:** nhắc tên để xin hỗ trợ, không phải để mở quyền xem vé cho người ngoài phạm vi.

- **`BR-14.3` (Không tính là phản hồi):** Ghi chú nội bộ không hoàn tất mốc Phản hồi Đầu tiên.

  **Lý do nghiệp vụ:** khách hàng không nhận được gì từ ghi chú nội bộ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-14.1.1` | Đang soạn ghi chú nội bộ | Quan sát khung soạn | Màu nền và nhãn "Nội bộ — khách hàng không thấy" khác hẳn khung phản hồi |
| `AC-14.1.2` | Vé có ghi chú nội bộ | Khách mở vé trên cổng khách hàng | Không thấy ghi chú |
| `AC-14.2.1` | Nhắc tên B có mức Xem bao phủ vé | Gửi | B nhận thông báo kèm đường dẫn mở được vé |
| `AC-14.2.2` | Nhắc tên C có mức Xem không bao phủ vé | Chọn tên C | Cảnh báo "C không xem được vé này" trước khi gửi |
| `AC-14.3.1` | Vé chưa có phản hồi công khai | Gửi ghi chú nội bộ | Mốc Phản hồi Đầu tiên vẫn chạy |

---

### FEAT-15 — Đính kèm Tài liệu & Hình ảnh

**Mô tả nghiệp vụ:** Đính kèm ảnh chụp lỗi, tệp nhật ký, tài liệu minh chứng vào phản hồi hoặc ghi chú.

**Vai trò sử dụng chính:** Khách hàng (gửi kèm qua kênh); người có ô (Vé hỗ trợ, Sửa) bao phủ vé.

**Điều kiện tiên quyết:** Vé tồn tại.

**Luồng chính:**

1. Chọn tệp khi soạn phản hồi hoặc ghi chú; hệ thống kiểm số lượng, dung lượng, định dạng trước khi gửi.
2. Tệp gắn với lượt trao đổi chứa nó.

**Quy tắc nghiệp vụ:**

- **`BR-15.1` (Giới hạn số lượng và dung lượng):** Tối đa `CFG-15-03` tệp mỗi lượt gửi; dung lượng mỗi tệp tối đa `CFG-15-01`.

  **Lý do nghiệp vụ:** giữ dung lượng lưu trữ và thời gian gửi trong mức chấp nhận được.

- **`BR-15.2` (Kiểm soát định dạng):** Doanh nghiệp cấu hình định dạng được nhận (`CFG-15-02`); tệp thực thi bị từ chối và không đưa được vào danh sách cho phép.

  **Lý do nghiệp vụ:** tệp thực thi là đường lây mã độc từ khách sang nhân viên.

- **`BR-15.3` (Quyền tải tệp theo quyền xem vé):** Chỉ người có mức Xem bao phủ vé tải được tệp của vé; tệp trong ghi chú nội bộ không bao giờ hiển thị cho khách hàng (`NFR-09`).

  **Lý do nghiệp vụ:** tệp đính kèm thường chứa dữ liệu nhạy cảm hơn chính nội dung trao đổi.

- **`BR-15.4` (Theo vòng đời vé):** Tệp được lưu, lưu trữ và xóa theo vòng đời của vé chứa nó, gồm cả khi khử định danh (`FEAT-45`).

  **Lý do nghiệp vụ:** ảnh chụp và tài liệu khách gửi có thể chứa thông tin định danh; bỏ sót tệp là không đáp ứng yêu cầu xóa.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-15.1.1` | `CFG-15-03` = 20 | Chọn 25 tệp | Màn hình báo giới hạn 20 tệp ngay khi chọn, trước khi gửi |
| `AC-15.1.2` | `CFG-15-01` = 25 MB | Chọn tệp 30 MB | Tệp bị đánh dấu vượt dung lượng, không gửi được |
| `AC-15.2.1` | Khách gửi tệp thực thi qua thư | Vé được tạo | Tệp bị từ chối; vé ghi nhận "1 tệp bị chặn do định dạng" |
| `AC-15.3.1` | Tệp trong ghi chú nội bộ | Khách mở vé | Không thấy, không tải được |
| `AC-15.4.1` | Vé được khử định danh | Mở vé | Tệp khách gửi đã bị xóa, có dấu hiệu "Tệp đã gỡ theo yêu cầu xóa dữ liệu" |

---

### FEAT-16 — Thư viện Câu trả lời Mẫu

**Mô tả nghiệp vụ:** Chèn nhanh câu trả lời chuẩn từ thư viện mẫu để tăng tốc và thống nhất cách trả lời.

**Vai trò sử dụng chính:** Tư vấn viên (dùng mẫu, tạo mẫu cá nhân); người có quyền Quản lý mẫu trả lời dùng chung.

**Điều kiện tiên quyết:** Có ít nhất một mẫu.

**Luồng chính:**

1. Khi soạn phản hồi, chọn mẫu (hoặc gõ phím tắt).
2. Hệ thống chèn nội dung, điền các biến từ vé.
3. Tư vấn viên chỉnh sửa và gửi.

**Quy tắc nghiệp vụ:**

- **`BR-16.1` (Mẫu dùng chung và mẫu cá nhân):** Thư viện dùng chung do người có quyền Quản lý mẫu trả lời dùng chung tạo, sửa, ngừng dùng; mỗi người tự tạo mẫu cá nhân chỉ mình thấy.

  **Lý do nghiệp vụ:** mẫu dùng chung đảm bảo nhất quán và cần kiểm soát nội dung; mẫu cá nhân giúp mỗi người tối ưu cách làm.

- **`BR-16.2` (Điền tự động thông tin vé):** Mẫu hỗ trợ biến tự điền (tên khách hàng, mã vé, tên tư vấn viên). Biến không điền được giá trị được giữ nguyên ký hiệu biến, tô nổi và tư vấn viên được cảnh báo trước khi gửi; không bao giờ bị thay bằng chuỗi rỗng.

  **Lý do nghiệp vụ:** xưng hô sai hoặc "Kính gửi ," là lỗi mất uy tín; để trống âm thầm thì không ai phát hiện.

- **`BR-16.3` (Sửa được trước khi gửi):** Nội dung đã chèn vẫn sửa được trước khi gửi.

  **Lý do nghiệp vụ:** mẫu là điểm khởi đầu, không phải câu trả lời bắt buộc.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-16.1.1` | Tư vấn viên không có quyền Quản lý mẫu trả lời dùng chung | Mở thư viện dùng chung | Dùng được; không có thao tác sửa |
| `AC-16.1.2` | A tạo mẫu cá nhân | B mở thư viện | B không thấy mẫu của A |
| `AC-16.2.1` | Mẫu có biến tên khách và mã vé | Chèn vào vé `TKT-01042` của chị Mai | Nội dung có "chị Mai" và "TKT-01042" |
| `AC-16.2.2` | Mẫu có biến số hợp đồng, vé không có hợp đồng | Chèn | Ký hiệu biến được giữ và tô nổi; bấm gửi thì có cảnh báo |
| `AC-16.3.1` | Chèn mẫu, sửa một câu | Gửi | Khách nhận nội dung đã sửa |

---

### FEAT-37 — Thông báo cho Tư vấn viên

**Mô tả nghiệp vụ:** Báo ngay cho người liên quan khi có vé được gán, khách phản hồi, được nhắc tên, vé sắp hoặc đã vi phạm, vé bị leo thang hay chuyển hàng đợi.

**Vai trò sử dụng chính:** Mọi người nhận thông báo; người có quyền Cấu hình cam kết dịch vụ (kênh mặc định, kênh đánh thức); Hệ thống.

**Điều kiện tiên quyết:** Người nhận Đang hoạt động.

**Luồng chính:**

1. Sự kiện phát sinh → hệ thống xác định người nhận, gửi theo kênh người đó chọn.
2. Gửi thất bại → thử lại, rồi xử lý theo `BR-37.5`.

**Quy tắc nghiệp vụ:**

- **`BR-37.1` (Sự kiện phát sinh thông báo):** Tối thiểu: được gán vé (kể cả do phân bổ tự động); khách phản hồi trên vé đang giữ; được nhắc tên; vé của mình sắp tới hạn hoặc đã vi phạm; vé của mình bị leo thang; vé được chuyển tới hàng đợi mình là Trưởng nhóm; vé tồn đọng. Người Tạm ngưng không nhận thông báo nghiệp vụ (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-42.1`); thông báo dành cho họ chuyển tới Trưởng nhóm.

  **Lý do nghiệp vụ:** phải tự mở danh sách kiểm tra thủ công thì vé bị bỏ sót; gửi cho người không vào được hệ thống là mất thông báo.

- **`BR-37.2` (Kênh thông báo):** Thông báo hiển thị trong ứng dụng; doanh nghiệp bật thêm thư điện tử cho sự kiện quan trọng; mỗi người tự chọn sự kiện nào nhận qua kênh nào, trừ thông báo bắt buộc (vi phạm, leo thang, cảnh báo ngoài giờ).

  **Lý do nghiệp vụ:** nhiễu thông báo làm người dùng bỏ qua cả cảnh báo thật; nhưng cảnh báo bắt buộc không được tắt.

- **`BR-37.3` (Không tự thông báo):** Hành động do chính người đó làm không sinh thông báo cho họ.

  **Lý do nghiệp vụ:** thông báo thừa làm giảm giá trị của thông báo thật.

- **`BR-37.4` (Kênh đánh thức cho cảnh báo ngoài giờ):** Vé phát sinh hoặc vi phạm trong khung ngoài ca nhưng thuộc cam kết (`BR-35.6`) gửi qua kênh có khả năng đánh thức (tin nhắn điện thoại hoặc cuộc gọi tự động). Doanh nghiệp cấu hình kênh và danh sách sự kiện được dùng kênh này (`CFG-37-02`).

  **Lý do nghiệp vụ:** không giới hạn danh sách thì nhân viên bị gọi vì việc không khẩn và sẽ tắt thông báo, làm hỏng chính cơ chế này.

- **`BR-37.5` (Thông báo không gửi được):** Thông báo bắt buộc gửi thất bại thì thử lại `CFG-37-01` lần; vẫn thất bại thì ghi nhận trên vé và báo Trưởng nhóm. Áp cả cho thông báo tới khách hàng ở `BR-22.3`, `BR-27.3`, `BR-41.4`.

  **Lý do nghiệp vụ:** `BR-27.3` là điều kiện để gộp vé được coi là hoàn chỉnh; khách không nhận được thông báo thì mất dấu yêu cầu của mình.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-37.1.1` | Quản lý gán vé cho A | — | A nhận thông báo trong ứng dụng |
| `AC-37.1.2` | Khách phản hồi trên vé A giữ | — | A nhận thông báo |
| `AC-37.1.3` | A đang Tạm ngưng, khách phản hồi trên vé A giữ | — | A không nhận; Trưởng nhóm nhận thông báo |
| `AC-37.2.1` | A tắt thư điện tử cho sự kiện "khách phản hồi" | Mở cấu hình thông báo | Không tắt được thông báo vi phạm và leo thang |
| `AC-37.3.1` | A tự ghi chú trên vé của mình | — | A không nhận thông báo |
| `AC-37.4.1` | Danh sách kênh đánh thức chỉ gồm "vi phạm vé hạng cao cấp" | Vé phổ thông phát sinh lúc 02:00 | Không gọi đánh thức; chỉ thông báo trong ứng dụng |
| `AC-37.5.1` | `CFG-37-01` = 3; kênh tin nhắn lỗi | Gửi cảnh báo ngoài giờ | Thử lại 3 lần; thất bại thì vé ghi "Không gửi được cảnh báo" và Trưởng nhóm được báo |

---

### FEAT-38 — Cảnh báo Trùng Thao tác

**Mô tả nghiệp vụ:** Hai người cùng mở và cùng soạn trả lời một vé thì khách nhận hai câu trả lời khác nhau. Tính năng cảnh báo để tránh tình huống này.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Xem) bao phủ vé.

**Điều kiện tiên quyết:** Hai người trở lên cùng mở một vé.

**Luồng chính:**

1. A mở vé → người khác đang mở thấy "A đang xem".
2. A bắt đầu soạn phản hồi → người khác thấy "A đang soạn trả lời".
3. Vé thay đổi trong lúc B soạn → B được cảnh báo trước khi gửi.

**Quy tắc nghiệp vụ:**

- **`BR-38.1` (Hiển thị người đang xem):** Người cùng mở một vé thấy ai đang xem cùng, cập nhật liên tục trong suốt thời gian mở.

  **Lý do nghiệp vụ:** biết có người khác đang ở vé là bước đầu để tránh làm trùng.

- **`BR-38.2` (Cảnh báo đang soạn):** Khi một người soạn phản hồi công khai, người khác đang mở vé thấy tên người đang soạn; cảnh báo tự mất khi người đó gửi, rời vé, hoặc không nhập liệu trong `CFG-38-02` — kể cả khi mất kết nối.

  **Lý do nghiệp vụ:** với người còn lại, người mất kết nối và người bỏ đi là như nhau.

- **`BR-38.3` (Cảnh báo trước khi gửi chồng):** Nếu vé đã thay đổi trong lúc một người đang soạn — có phản hồi mới, hoặc đổi trạng thái, mức ưu tiên, người phụ trách, hàng đợi — hệ thống cảnh báo và yêu cầu xem lại trước khi gửi.

  **Lý do nghiệp vụ:** vé có thể đã được người khác đóng hoặc chuyển đi; nội dung đang soạn không còn phù hợp.

- **`BR-38.4` (Hai người cùng sửa một trường):** Hai người đổi cùng một trường cách nhau không quá `CFG-38-01`: thay đổi sau được ghi nhận, người thao tác sau được báo giá trị vừa bị thay là gì, do ai đặt, lúc nào. Ngoài cửa sổ đó ghi nhận bình thường. Cả hai lần đều có trong nhật ký (`BR-44.1`).

  **Lý do nghiệp vụ:** hai thao tác cách xa nhau thì người sau đã thấy giá trị hiện hành; trong cửa sổ ngắn là ghi đè ngoài ý muốn cần được biết.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-38.1.1` | A và B cùng mở vé | Quan sát | Mỗi người thấy người kia đang xem |
| `AC-38.2.1` | A bắt đầu soạn | B quan sát | B thấy "A đang soạn trả lời" |
| `AC-38.2.2` | `CFG-38-02` = 120 giây; A mất kết nối khi đang soạn | Sau 120 giây | Cảnh báo trên màn hình B tự mất |
| `AC-38.3.1` | B đang soạn, A gửi phản hồi | B bấm gửi | B được cảnh báo có phản hồi mới, phải xem lại trước khi gửi |
| `AC-38.3.2` | B đang soạn, người khác chuyển vé sang trạng thái kết thúc | B bấm gửi | B được cảnh báo về thay đổi trạng thái |
| `AC-38.4.1` | `CFG-38-01` = 60 giây | Hai người đổi mức ưu tiên cách nhau 20 giây | Người sau được báo giá trị cũ, người đặt, thời điểm; nhật ký có cả hai lần |
| `AC-38.4.2` | Như trên | Hai lần cách nhau 5 phút | Không có cảnh báo; nhật ký có cả hai lần |

---

## F. LEO THANG & CẢNH BÁO VI PHẠM CAM KẾT

### FEAT-17 — Cảnh báo Sớm Nguy cơ Vi phạm Cam kết

**Mô tả nghiệp vụ:** Khi vé sắp tới hạn, hệ thống cảnh báo người phụ trách đủ sớm để còn kịp hành động. Đây là công cụ phòng ngừa, chỉ nhắc, không chuyển việc.

**Vai trò sử dụng chính:** Người phụ trách vé, Trưởng nhóm (nhận cảnh báo); người có quyền Cấu hình cam kết dịch vụ (ngưỡng).

**Điều kiện tiên quyết:** Vé có mốc cam kết đang chạy.

**Luồng chính:**

1. Mốc cam kết qua ngưỡng cảnh báo → người phụ trách nhận cảnh báo; vé chưa gán thì Trưởng nhóm nhận.
2. Vé được đánh dấu nổi trên danh sách và bảng điều khiển.

**Quy tắc nghiệp vụ:**

- **`BR-17.1` (Ngưỡng theo tỷ lệ thời hạn):** Ngưỡng là tỷ lệ phần trăm thời hạn đã trôi (`CFG-17-01`); doanh nghiệp đặt thêm được ngưỡng tuyệt đối tính bằng phút (`CFG-17-02`) — khi đặt cả hai, mốc nào đến trước thì cảnh báo.

  **Lý do nghiệp vụ:** tỷ lệ giúp cảnh báo tương xứng với độ dài cam kết; cam kết rất ngắn cần thêm mốc tuyệt đối để còn thời gian phản ứng.

- **`BR-17.2` (Cảnh báo cho cả hai mốc):** Áp độc lập cho mốc Phản hồi Đầu tiên và mốc Xử lý Dứt điểm.

  **Lý do nghiệp vụ:** hai mốc có hai hạn chót khác nhau.

- **`BR-17.3` (Xét theo từng mốc khi tạm dừng):** Mốc đang tạm dừng không phát cảnh báo; mốc đang chạy vẫn phát, kể cả khi vé ở trạng thái tạm dừng. Vé chưa có phản hồi công khai bị chuyển sang "Chờ khách hàng" vẫn phải nhận cảnh báo cho mốc Phản hồi Đầu tiên.

  **Lý do nghiệp vụ:** nếu không, tư vấn viên vi phạm cam kết phản hồi mà không được báo trước — đúng tình huống `BR-09.2` muốn ngăn.

- **`BR-17.4` (Hiển thị trực quan):** Vé sắp tới hạn được đánh dấu nổi trên danh sách làm việc và trên bảng điều khiển hàng đợi (`FEAT-42`) cho tới khi mốc hoàn tất hoặc vi phạm.

  **Lý do nghiệp vụ:** thông báo một lần rồi thôi dễ bị trôi; dấu hiệu thường trực giúp Trưởng nhóm điều phối trước.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-17.1.1` | Cam kết xử lý 2 giờ, `CFG-17-01` = 75% | Sau 1 giờ 30 phút làm việc vé chưa xong | Người phụ trách nhận cảnh báo |
| `AC-17.1.2` | Cam kết 20 phút, `CFG-17-02` = 10 phút | Sau 10 phút | Cảnh báo phát ở mốc 10 phút (đến trước mốc 15 phút của 75%) |
| `AC-17.2.1` | Mốc phản hồi và mốc xử lý cùng qua ngưỡng | — | Hai cảnh báo riêng, nêu rõ từng mốc |
| `AC-17.3.1` | Vé đã có phản hồi đầu tiên, đang "Chờ khách hàng" | Qua 75% thời hạn xử lý | Không có cảnh báo |
| `AC-17.3.2` | Vé chưa có phản hồi công khai, đang "Chờ khách hàng" | Qua 75% thời hạn phản hồi | Có cảnh báo cho mốc Phản hồi Đầu tiên |
| `AC-17.4.1` | Vé sắp tới hạn | Trưởng nhóm mở bảng điều khiển | Vé được đánh dấu nổi và tính vào số "sắp tới hạn" |

---

### FEAT-18 — Tự động Leo thang khi Vi phạm Cam kết

**Mô tả nghiệp vụ:** Khi vé đã vi phạm mà vẫn chưa xử lý, hệ thống đưa vấn đề lên cấp quản lý theo chính sách doanh nghiệp. Chỉ kích hoạt từ thời điểm vi phạm trở đi; mốc trước vi phạm thuộc `FEAT-17`.

**Vai trò sử dụng chính:** Người có quyền Cấu hình cam kết dịch vụ (chính sách leo thang); Hệ thống; người nhận cảnh báo.

**Điều kiện tiên quyết:** Vé có mốc cam kết đã vi phạm.

**Luồng chính:**

1. Mốc vi phạm → vé đánh dấu vi phạm; các mốc leo thang được lên lịch.
2. Tới mỗi mốc → hệ thống thực hiện các hành động đã cấu hình, ghi lịch sử.
3. Vé kết thúc → các mốc chưa kích hoạt bị hủy.

**Quy tắc nghiệp vụ:**

- **`BR-18.1` (Nhiều mốc sau vi phạm):** Doanh nghiệp cấu hình nhiều mốc, mỗi mốc là khoảng thời gian làm việc kể từ thời điểm vi phạm (`CFG-18-01`).

  **Lý do nghiệp vụ:** vi phạm 10 phút và vi phạm 4 giờ cần mức can thiệp khác nhau.

- **`BR-18.2` (Hành động cấu hình được):** Mỗi mốc gồm một hoặc nhiều hành động: đánh dấu mức độ vi phạm; thông báo một người cụ thể; báo lên quản lý trực tiếp của người phụ trách theo số cấp `CFG-18-02`; tự chuyển vé cho một người phụ trách khác được chỉ định, hoặc sang một hàng đợi trong danh sách được chuyển tới (`BR-34.8`). Người cấu hình chỉ chọn được người nhận hoặc hàng đợi đích mà ô (Vé hỗ trợ, Gán) của chính mình bao phủ, nhất quán với `BR-10.6`. Người được chỉ định nhận vé phải đạt `BR-12.6` tại thời điểm chuyển; nếu không đạt, hành động chuyển không thực hiện, vé giữ nguyên và người nhận dự phòng (`CFG-18-03`) được báo.

  **Lý do nghiệp vụ:** cấu hình leo thang được soạn từ trước; người được chỉ định có thể đã đổi đội hay bị tạm ngưng — hệ thống không được giao vé cho người không xử lý được (Nguyên tắc 3).

- **`BR-18.3` (Không leo thang trùng lặp):** Mỗi mốc kích hoạt đúng một lần cho mỗi vé.

  **Lý do nghiệp vụ:** cảnh báo lặp gây nhiễu cho cấp quản lý.

- **`BR-18.4` (Không có quản lý để báo lên):** Người phụ trách không có quản lý trực tiếp, hoặc vé chưa gán, thì cảnh báo gửi người nhận dự phòng (`CFG-18-03`, mặc định Trưởng nhóm, rồi Trưởng phòng Dịch vụ Khách hàng).

  **Lý do nghiệp vụ:** bỏ qua âm thầm là để vi phạm không ai biết.

- **`BR-18.5` (Dừng khi vé kết thúc):** Vé chuyển sang trạng thái kết thúc thì mốc chưa kích hoạt bị hủy; vé mở lại thì chỉ mốc của lần vi phạm mới được lên lịch.

  **Lý do nghiệp vụ:** leo thang cho vé đã xong là nhiễu; vé mở lại là một chu kỳ mới.

- **`BR-18.6` (Ghi lịch sử leo thang):** Mỗi lần leo thang ghi thời điểm, mốc, hành động đã thực hiện, người nhận, và hành động không thực hiện được kèm lý do.

  **Lý do nghiệp vụ:** căn cứ đối soát với khách hàng và rà soát nội bộ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-18.1.1` | `CFG-18-01` = ngay khi vi phạm, sau 1 giờ, sau 4 giờ | Vé vi phạm và vẫn chưa xong sau 4 giờ | Ba mốc kích hoạt đúng thời điểm |
| `AC-18.2.1` | Mốc 1 giờ: báo quản lý trực tiếp 1 cấp | Tới mốc | Quản lý trực tiếp của người phụ trách nhận cảnh báo |
| `AC-18.2.3` | Người có quyền Cấu hình cam kết dịch vụ, ô (Vé, Gán) chỉ bao phủ "Hỗ trợ – Hà Nội" | Cấu hình mốc tự chuyển vé sang hàng đợi "Kỹ thuật" | "Kỹ thuật" không có trong danh sách chọn |
| `AC-18.2.2` | Mốc 4 giờ: chuyển vé cho E; E vừa bị tạm ngưng | Tới mốc | Vé không chuyển; người nhận dự phòng được báo; lịch sử ghi "Không chuyển được — người nhận đang tạm ngưng" |
| `AC-18.3.1` | Mốc đã kích hoạt | Hệ thống khởi động lại | Mốc không kích hoạt lần hai |
| `AC-18.4.1` | Vé chưa gán vi phạm | Tới mốc báo quản lý | Trưởng nhóm nhận cảnh báo |
| `AC-18.5.1` | Vé kết thúc trước mốc 4 giờ | Tới thời điểm 4 giờ | Không có hành động |
| `AC-18.6.1` | Vé đã leo thang hai mốc | Mở lịch sử | Thấy hai mốc, hành động, người nhận |

---

### FEAT-39 — Leo thang Thủ công & Xử lý Khiếu nại về Chất lượng Phục vụ

**Mô tả nghiệp vụ:** Khách bức xúc, đe dọa chấm dứt hợp đồng, hoặc khiếu nại về chính tư vấn viên đang phục vụ — cần đưa lên quản lý ngay, không chờ hết hạn cam kết.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Sửa) bao phủ vé (leo thang, gắn cờ); Trưởng nhóm (tiếp nhận); người có ô (Vé khiếu nại, Tạo) (lập vé khiếu nại).

**Điều kiện tiên quyết:** Vé chưa đóng hoàn tất.

**Luồng chính:**

1. Tư vấn viên leo thang vé kèm lý do; Trưởng nhóm nhận thông báo.
2. Tư vấn viên gắn cờ "khách hàng không hài lòng" khi cần.
3. Khiếu nại nhắm vào tư vấn viên đang giữ vé → người tiếp nhận chuyển vé cho người khác và lập vé khiếu nại riêng.

**Quy tắc nghiệp vụ:**

- **`BR-39.1` (Leo thang chủ động):** Leo thang kèm lý do bắt buộc; vé được đánh dấu đang leo thang và Trưởng nhóm nhận thông báo ngay. Leo thang khi người leo thang chính là Trưởng nhóm thì thông báo tới Trưởng phòng Dịch vụ Khách hàng.

  **Lý do nghiệp vụ:** nhiều tình huống cần quản lý mà không gắn với vi phạm thời hạn.

- **`BR-39.2` (Cờ khách hàng không hài lòng):** Vé mang cờ được ưu tiên hiển thị trên bảng điều khiển (`FEAT-42`).

  **Lý do nghiệp vụ:** Trưởng nhóm theo dõi sát những khách có nguy cơ rời bỏ.

- **`BR-39.3` (Khiếu nại về chính người đang phục vụ):** Vé phải được chuyển cho người khác (`BR-12.6`) trước khi xử lý khiếu nại. Việc rà soát ghi trên một **vé khiếu nại** riêng, liên kết với vé gốc, không ghi vào dòng trao đổi của vé gốc. Vé khiếu nại là loại dữ liệu riêng với ô riêng (Mục 5.1), không qua hàng đợi; Người phụ trách là người rà soát. Hệ thống tự đặt lượt chặn trên vé khiếu nại đối với tư vấn viên bị khiếu nại theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.1`; người đó vẫn xem vé gốc như bình thường.

  **Lý do nghiệp vụ:** dòng trao đổi phải liền mạch (`BR-13.3`); một dòng thời gian bị khuyết giữa chừng tự tiết lộ có nội dung đang bị giấu. Loại dữ liệu riêng giúp đồng nghiệp cùng đội không thấy hồ sơ khiếu nại, còn lượt chặn bảo đảm người bị khiếu nại không thấy kể cả khi vai trò của họ có ô Xem.

- **`BR-39.4` (Không thay đổi cam kết):** Leo thang thủ công không đổi hạn chót đã áp dụng.

  **Lý do nghiệp vụ:** đây là công cụ điều phối nội bộ, không phải cơ chế gia hạn với khách.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-39.1.1` | A leo thang vé | Bỏ trống lý do | Nút xác nhận bị vô hiệu cho tới khi nhập lý do |
| `AC-39.1.2` | A nhập lý do, leo thang | — | Vé mang dấu "Đang leo thang"; Trưởng nhóm nhận thông báo |
| `AC-39.2.1` | A gắn cờ "khách hàng không hài lòng" | Trưởng nhóm mở bảng điều khiển | Vé nằm trong nhóm ưu tiên theo dõi |
| `AC-39.3.1` | Khách khiếu nại thái độ của A | Trưởng nhóm lập vé khiếu nại khi vé gốc vẫn do A giữ | Từ chối, yêu cầu chuyển vé gốc trước |
| `AC-39.3.2` | Vé gốc đã chuyển cho D; vé khiếu nại đã lập | A mở danh sách vé và tìm vé khiếu nại | A không thấy vé khiếu nại; vé gốc hiển thị liền mạch với A |
| `AC-39.3.3` | Đồng nghiệp B cùng đội, vai trò Nhân viên Hỗ trợ | B mở vé gốc | Liên kết tới vé khiếu nại hiển thị "Bị hạn chế truy cập" |
| `AC-39.4.1` | Vé leo thang thủ công | Quan sát hạn chót | Không đổi |

## G. ĐÓNG VÉ & KHẢO SÁT HÀI LÒNG KHÁCH HÀNG

### FEAT-19 — Kết thúc Vé & Ghi nhận Nguyên nhân Xử lý

**Mô tả nghiệp vụ:** Khi chuyển vé sang trạng thái kết thúc dạng "đã xử lý xong", người thực hiện ghi nhận nguyên nhân xử lý (từ danh mục doanh nghiệp cấu hình) và tóm tắt giải pháp — đầu vào cho tìm nguyên nhân gốc và cơ sở tri thức.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Kết thúc vé) bao phủ vé.

**Điều kiện tiên quyết:** Vé đã có ít nhất một phản hồi công khai (`BR-19.2`).

**Luồng chính:**

1. Chọn trạng thái kết thúc → hộp thoại hiển thị các thông tin bắt buộc của trạng thái đó.
2. Nhập nguyên nhân xử lý, tóm tắt giải pháp, trường bắt buộc (`BR-05.1`), phân loại tới cấp cuối (`BR-04.1`).
3. Xác nhận → hệ thống ghi thời điểm, người thực hiện; khảo sát được gửi (`FEAT-20`).

**Quy tắc nghiệp vụ:**

- **`BR-19.1` (Thông tin bắt buộc khi kết thúc):** Trạng thái kết thúc có dấu hiệu "bắt buộc nguyên nhân xử lý" (`BR-01.2`) đòi nguyên nhân xử lý và tóm tắt giải pháp; nguyên nhân đã có sẵn trên vé thì không phải nhập lại. Trạng thái không có dấu hiệu này (ví dụ "Đóng do thư rác") không đòi. Ràng buộc không chặn hệ thống tự đóng vé theo `FEAT-22`.

  **Lý do nghiệp vụ:** doanh nghiệp quyết định trạng thái nào cần căn cứ nguyên nhân; bắt nhập cho vé thư rác chỉ sinh dữ liệu rác.

- **`BR-19.2` (Phải có phản hồi công khai trước khi kết thúc):** Không chuyển được vé sang trạng thái kết thúc nếu chưa từng có phản hồi công khai nào tới khách hàng (cập nhật lan truyền từ vé cha được tính, `BR-28.2`). Trạng thái kết thúc dành cho vé không cần trả lời (thư rác, vé thử nghiệm) được doanh nghiệp đánh dấu miễn ràng buộc này khi cấu hình trạng thái.

  **Lý do nghiệp vụ:** đóng vé mà chưa hề trả lời khách là phục vụ không chấp nhận được, dù sự cố có thể đã tự hết; nhưng trả lời thư rác là vô lý.

- **`BR-19.3` (Danh mục nguyên nhân cấu hình được):** Người có quyền Cấu hình danh mục vé định nghĩa danh mục nguyên nhân xử lý; nguyên nhân đang dùng chỉ ngừng sử dụng, không xóa.

  **Lý do nghiệp vụ:** mỗi dịch vụ có các nhóm nguyên nhân khác nhau; xóa nguyên nhân đang dùng làm sai báo cáo lịch sử.

- **`BR-19.4` (Ghi nhận thời điểm và người thực hiện):** Ghi lại thời điểm kết thúc và người thực hiện.

  **Lý do nghiệp vụ:** căn cứ tính tuân thủ cam kết và hiệu suất cá nhân.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-19.1.1` | Trạng thái "Đã giải quyết" bắt buộc nguyên nhân; vé chưa có nguyên nhân | Mở hộp thoại kết thúc | Ô nguyên nhân và tóm tắt được đánh dấu bắt buộc; nút xác nhận bị vô hiệu tới khi điền |
| `AC-19.1.2` | Vé đã có nguyên nhân từ trước | Kết thúc | Kết thúc được mà không phải nhập lại nguyên nhân |
| `AC-19.1.3` | Trạng thái "Đóng do thư rác" không bắt buộc nguyên nhân | Chuyển vé sang trạng thái đó | Không đòi nguyên nhân |
| `AC-19.2.1` | Vé chưa có phản hồi công khai | Mở danh sách trạng thái kết thúc | Các trạng thái chịu ràng buộc bị vô hiệu kèm giải thích "Cần gửi phản hồi cho khách trước" |
| `AC-19.2.2` | Vé con nhận cập nhật lan truyền từ vé cha | Kết thúc vé con | Kết thúc được |
| `AC-19.3.1` | Nguyên nhân "Lỗi cấu hình" đang dùng | Xóa | Từ chối; chỉ ngừng sử dụng được |
| `AC-19.4.1` | Kết thúc vé | Mở lịch sử | Có thời điểm và người kết thúc |
| `AC-19.4.2` | Nhân viên Hỗ trợ có (Vé, Kết thúc vé) = Chỉ của mình | Mở vé của đồng nghiệp | Không có thao tác kết thúc |

---

### FEAT-20 — Khảo sát Đánh giá Sự Hài lòng 1-5 Sao

**Mô tả nghiệp vụ:** Khi vé kết thúc, hệ thống tự gửi khách một đường dẫn khảo sát riêng cho vé; khách chấm 1-5 sao kèm nhận xét; kết quả ghi vào vé và hồ sơ hiệu suất của người đã xử lý.

**Vai trò sử dụng chính:** Khách hàng (chấm điểm); Hệ thống (gửi); người có quyền Cấu hình cam kết dịch vụ (mẫu khảo sát, trường hợp loại trừ).

**Điều kiện tiên quyết:** Vé chuyển sang trạng thái kết thúc và không thuộc trường hợp loại trừ.

**Luồng chính:**

1. Vé chuyển sang "đã xử lý xong" → hệ thống gửi khảo sát qua kênh khách đã dùng.
2. Khách chấm điểm trong thời hạn hiệu lực.
3. Điểm được ghi nhận vào vé và chỉ số.

**Quy tắc nghiệp vụ:**

- **`BR-20.1` (Thời hạn khảo sát):** Đường dẫn có hiệu lực trong `CFG-20-01` kể từ khi gửi; quá hạn thì không dùng được và vé ghi nhận "khách không phản hồi khảo sát".

  **Lý do nghiệp vụ:** điểm chấm quá muộn không còn phản ánh trải nghiệm phục vụ.

- **`BR-20.2` (Gửi tự động, không phụ thuộc tư vấn viên):** Hệ thống gửi theo cấu hình; tư vấn viên không chọn được vé nào được gửi.

  **Lý do nghiệp vụ:** nếu được chọn, tư vấn viên chỉ gửi cho vé mình làm tốt, `KPI-03` bị thiên lệch.

- **`BR-20.3` (Một khảo sát còn hiệu lực, một điểm mỗi vé):** Mỗi thời điểm một vé có tối đa một đường dẫn còn hiệu lực, và mỗi vé đóng góp tối đa một điểm vào `KPI-03`.

  **Lý do nghiệp vụ:** tránh làm phiền khách và đếm một vé nhiều lần.

- **`BR-20.4` (Loại trừ khảo sát):** Bắt buộc không gửi cho vé phụ đã gộp (khách chấm qua vé chính) và vé đánh dấu thư rác; doanh nghiệp cấu hình thêm trường hợp loại trừ (`CFG-20-02`).

  **Lý do nghiệp vụ:** một lần liên hệ chỉ sinh một điểm (đồng bộ `BR-27.6`); gửi khảo sát cho thư rác là vô nghĩa.

- **`BR-20.5` (Mốc gửi):** Gửi khi vé chuyển sang "đã xử lý xong"; vé đi thẳng sang "đã đóng hoàn tất" thì gửi tại mốc đó.

  **Lý do nghiệp vụ:** chờ tới khi tự động đóng thì trải nghiệm đã cũ nhiều ngày, tỷ lệ phản hồi sụt mạnh.

- **`BR-20.6` (Vé mở lại khi khảo sát còn hiệu lực):** Vé mở lại trong lúc đường dẫn chưa hết hạn thì đường dẫn bị vô hiệu ngay và điểm đã chấm (nếu có) bị gỡ khỏi `KPI-03`; lần kết thúc sau gửi một khảo sát mới.

  **Lý do nghiệp vụ:** điểm chấm cho lần phục vụ chưa thực sự giải quyết xong không phản ánh đúng chất lượng; khảo sát cũ đã bị vô hiệu nên khách không bao giờ có hai khảo sát cùng hiệu lực.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-20.1.1` | `CFG-20-01` = 7 ngày | Khách mở đường dẫn ngày thứ 8 | Báo đường dẫn đã hết hạn; vé ghi "khách không phản hồi khảo sát" |
| `AC-20.2.1` | Tư vấn viên mở vé đã kết thúc | Tìm thao tác gửi hoặc không gửi khảo sát | Không có |
| `AC-20.3.1` | Vé đã có khảo sát còn hiệu lực | Vé được kết thúc lại mà không mở lại | Không phát sinh khảo sát thứ hai |
| `AC-20.4.1` | Vé phụ đã gộp | Vé chính kết thúc | Chỉ khách của vé chính nhận khảo sát của vé chính; vé phụ không có khảo sát riêng |
| `AC-20.5.1` | Vé chuyển "đã xử lý xong" 10:00 | — | Khảo sát gửi ngay, không chờ tự động đóng |
| `AC-20.6.1` | Khách đã chấm 5 sao, đường dẫn còn hạn | Vé mở lại | Đường dẫn bị vô hiệu; điểm 5 sao không còn trong `KPI-03` |
| `AC-20.6.2` | Tiếp nối, vé kết thúc lần hai | — | Đúng một khảo sát mới được gửi |

---

### FEAT-21 — Mở lại Vé đã Xử lý Xong

**Mô tả nghiệp vụ:** Khách phản hồi lại sau khi vé đã kết thúc thì vé được mở lại trong hạn cho phép; quá hạn thì tạo vé mới liên kết vé cũ, để số liệu tuân thủ đã chốt không bị treo mở-đóng vô hạn.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Kết thúc vé) bao phủ vé; Hệ thống (xử lý phản hồi của khách).

**Điều kiện tiên quyết:** Vé đang ở trạng thái kết thúc dạng "đã xử lý xong" hoặc "đã đóng hoàn tất".

**Luồng chính:**

1. Khách phản hồi trong hạn → vé tự quay về trạng thái đang xử lý; người phụ trách được thông báo.
2. Người có ô Kết thúc vé bao phủ vé mở lại thủ công khi cần, kèm xác nhận.
3. Khách phản hồi sau hạn → hệ thống tạo vé mới liên kết tới vé cũ.

**Quy tắc nghiệp vụ:**

- **`BR-21.1` (Xác nhận mở lại thủ công):** Mở lại thủ công yêu cầu xác nhận rõ ràng.

  **Lý do nghiệp vụ:** mở lại làm thay đổi số liệu chất lượng; tránh thao tác vội.

- **`BR-21.2` (Đếm số lần mở lại):** Ghi nhận số lần mở lại và thời điểm mở lại gần nhất.

  **Lý do nghiệp vụ:** căn cứ của `KPI-04`.

- **`BR-21.3` (Giới hạn thời gian mở lại):** Hạn mở lại là `CFG-21-01` kể từ khi vé kết thúc. Trong hạn, phản hồi của khách mở lại đúng vé cũ. Quá hạn, thao tác mở lại thủ công bị vô hiệu; phản hồi của khách tạo một vé mới liên kết tham chiếu tới vé cũ, vào hàng đợi hiện tại của vé cũ (`BR-34.2`), nhận cam kết mới từ đầu.

  **Lý do nghiệp vụ:** vé đã tính vào tuân thủ cam kết không được treo mở lại vô thời hạn; vé mới vẫn giữ được bối cảnh qua liên kết.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-21.1.1` | Vé đã xử lý xong | Bấm mở lại | Hộp xác nhận hiện ra; hủy thì vé không đổi |
| `AC-21.2.1` | Vé mở lại lần thứ hai | Mở vé | Số lần mở lại = 2, thời điểm mở lại gần nhất đúng |
| `AC-21.3.1` | `CFG-21-01` = 7 ngày; vé kết thúc 3 ngày trước | Khách phản hồi | Vé cũ chuyển về đang xử lý |
| `AC-21.3.2` | Vé kết thúc 20 ngày trước | Khách phản hồi | Vé mới được tạo, liên kết tới vé cũ, cùng hàng đợi; vé cũ không đổi |
| `AC-21.3.3` | Như trên | Người có ô Kết thúc vé mở vé cũ | Thao tác mở lại bị vô hiệu kèm giải thích đã quá hạn |

---

### FEAT-22 — Tự động Đóng Vé sau Thời gian Không Phản hồi

**Mô tả nghiệp vụ:** Sau khi vé xử lý xong, khách không phản hồi trong thời hạn cấu hình thì hệ thống tự chuyển vé sang "đã đóng hoàn tất", để số vé đang mở phản ánh đúng khối lượng thật.

**Vai trò sử dụng chính:** Người có quyền Cấu hình cam kết dịch vụ (thời hạn); Hệ thống.

**Điều kiện tiên quyết:** Vé ở trạng thái "đã xử lý xong".

**Luồng chính:**

1. Vé chuyển "đã xử lý xong" → bắt đầu đếm thời gian chờ.
2. Trước mốc đóng → gửi nhắc khách.
3. Hết thời gian chờ mà khách im lặng → vé đóng hoàn tất.

**Quy tắc nghiệp vụ:**

- **`BR-22.1` (Thời hạn chờ):** Thời gian chờ là `CFG-22-01` tính bằng giờ làm việc kể từ khi vé chuyển "đã xử lý xong".

  **Lý do nghiệp vụ:** cho khách đủ thời gian kiểm tra giải pháp trong giờ làm việc của họ.

- **`BR-22.2` (Khách phản hồi làm dừng đếm):** Khách phản hồi trong thời gian chờ thì vé không bị đóng và quay về xử lý theo `FEAT-21`.

  **Lý do nghiệp vụ:** khách phản hồi nghĩa là vấn đề chưa xong.

- **`BR-22.3` (Nhắc trước khi đóng):** Trước mốc đóng `CFG-22-02`, khách nhận nhắc rằng vé sắp được đóng và có thể phản hồi nếu chưa giải quyết triệt để.

  **Lý do nghiệp vụ:** đóng không báo trước làm khách cảm thấy bị bỏ rơi.

- **`BR-22.4` (Không ảnh hưởng tuân thủ):** Mốc tính tuân thủ xử lý dứt điểm là lúc vé chuyển "đã xử lý xong", không phải lúc tự động đóng.

  **Lý do nghiệp vụ:** thời gian chờ khách xác nhận không phải thời gian xử lý.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-22.1.1` | `CFG-22-01` = 48 giờ làm việc; vé xử lý xong 10:00 Thứ Hai (lịch 8 tiếng/ngày) | Khách im lặng | Vé đóng hoàn tất lúc 10:00 Thứ Hai tuần sau, không phải 10:00 Thứ Tư |
| `AC-22.2.1` | Vé đang chờ đóng | Khách phản hồi | Vé về đang xử lý, không bị đóng |
| `AC-22.3.1` | `CFG-22-02` = 12 giờ làm việc | Tới mốc nhắc | Khách nhận thư nhắc qua kênh đã dùng |
| `AC-22.4.1` | Vé tự động đóng | Xem báo cáo tuân thủ | Vé được tính theo mốc 10:00 Thứ Hai |

---

## H. THAO TÁC HÀNG LOẠT, NHẬP/XUẤT & THÙNG RÁC

### FEAT-23 — Gắn Nhãn Hàng loạt

**Mô tả nghiệp vụ:** Chọn nhiều vé để gắn nhãn chung — gom vé của một chiến dịch, một đợt sự cố, một chủ đề.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Sửa) bao phủ các vé cần gắn.

**Điều kiện tiên quyết:** Đã chọn ít nhất một vé.

**Luồng chính:**

1. Chọn vé trên danh sách, chọn nhãn.
2. Hệ thống hiển thị số vé sẽ bị ảnh hưởng, yêu cầu xác nhận.
3. Thực hiện, báo kết quả.

**Quy tắc nghiệp vụ:**

- **`BR-23.1` (Giới hạn mỗi lượt):** Tối đa `CFG-23-01` vé mỗi lượt.

  **Lý do nghiệp vụ:** thao tác hoàn tất trong thời gian chấp nhận được và người dùng kiểm soát được phạm vi ảnh hưởng.

- **`BR-23.2` (Xác nhận phạm vi):** Hiển thị số vé bị ảnh hưởng và yêu cầu xác nhận trước khi thực hiện.

  **Lý do nghiệp vụ:** tránh sửa nhầm hàng trăm vé vì chọn sai bộ lọc.

- **`BR-23.3` (Báo cáo kết quả):** Báo số vé thành công và liệt kê vé không xử lý được kèm lý do — vé đã khóa do gộp, vé ngoài mức Sửa của người thực hiện.

  **Lý do nghiệp vụ:** thao tác hàng loạt không được vượt quyền hay bỏ qua âm thầm.

- **`BR-23.4` (Ghi vết):** Ghi vào nhật ký thay đổi của từng vé bị ảnh hưởng (`FEAT-44`), kèm mã lô.

  **Lý do nghiệp vụ:** khi phát hiện sai sót, cần truy ngược ai đã sửa những vé nào để khôi phục.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-23.1.1` | `CFG-23-01` = 500 | Chọn 600 vé | Thao tác bị vô hiệu kèm giới hạn 500 |
| `AC-23.2.1` | Chọn 300 vé | Bấm gắn nhãn | Hộp xác nhận "300 vé sẽ được gắn nhãn" |
| `AC-23.3.1` | 300 vé, 5 vé đã gộp, 3 vé ngoài mức Sửa | Xác nhận | Báo 292 thành công, liệt kê 8 vé kèm lý do |
| `AC-23.4.1` | Sau khi gắn | Mở nhật ký một vé | Có dòng gắn nhãn kèm mã lô, người thực hiện |

---

### FEAT-24 — Nhập Dữ liệu Vé từ Tệp

**Mô tả nghiệp vụ:** Nhập vé từ tệp, phục vụ chuyển đổi từ hệ thống cũ hoặc bổ sung vé tiếp nhận ngoài hệ thống.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Nhập) khác Không có.

**Điều kiện tiên quyết:** Có tệp hợp lệ về định dạng.

**Luồng chính:**

1. Tải tệp, đối chiếu cột với trường của vé, xem trước một số dòng mẫu.
2. Chọn hàng đợi mặc định cho dòng không có cột Đơn vị tiếp nhận và Người phụ trách (`BR-34.2`).
3. Chạy; theo dõi tiến độ; tải báo cáo kết quả.

**Quy tắc nghiệp vụ:**

- **`BR-24.1` (Giới hạn tệp):** Dung lượng tối đa `CFG-24-01`.

  **Lý do nghiệp vụ:** tệp quá lớn cần tách lô để theo dõi và sửa lỗi được.

- **`BR-24.2` (Đối chiếu cột và xem trước):** Người nhập đối chiếu cột với trường và xem trước dòng mẫu trước khi chạy.

  **Lý do nghiệp vụ:** phát hiện sai lệch sớm, trước khi nhập hàng nghìn vé sai.

- **`BR-24.3` (Dòng lỗi không hủy cả lô):** Dòng hợp lệ được nhập; dòng lỗi bị bỏ qua và liệt kê kèm lý do. Dòng trỏ tới Người phụ trách vượt ô Gán của người nhập, người Tạm ngưng, Đã rời, không tồn tại, hoặc không là thành viên đơn vị tiếp nhận của dòng là dòng lỗi; người đang chờ chấp nhận lời mời được giữ chỗ khi lời mời ghi đơn vị tiếp nhận của dòng là Đơn vị chính hoặc kiêm nhiệm, nếu không cũng là dòng lỗi; vé giữ chỗ luôn thuộc đơn vị tiếp nhận đó, và giữ chỗ chấm dứt (vé thành chưa gán trong hàng đợi) khi lời mời bị sửa không còn ghi đơn vị đó — theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10`, và `BR-34.2`.

  **Lý do nghiệp vụ:** hủy cả lô vì vài dòng lỗi khiến chuyển đổi tệp hàng chục nghìn dòng không bao giờ hoàn tất; nhưng nhập không được là đường để gán vé vượt quyền.

- **`BR-24.4` (Chống nhập trùng):** Mã tham chiếu từ hệ thống cũ được dùng để bỏ qua bản ghi đã nhập ở lần trước.

  **Lý do nghiệp vụ:** cho phép nhập lại an toàn sau khi sửa lỗi.

- **`BR-24.5` (Vé nhập khẩu không tính vào chỉ số):** Vé nhập từ hệ thống cũ bị loại khỏi thống kê tuân thủ (`BR-43.2`) và không gửi khảo sát.

  **Lý do nghiệp vụ:** chúng không phản ánh chất lượng phục vụ trên hệ thống hiện tại.

- **`BR-24.6` (Theo dõi tiến độ):** Người nhập theo dõi tiến độ và tải báo cáo kết quả; trong suốt quá trình chạy, lô nhập chỉ chạm tới bản ghi nằm trong **phần giao** của mức lúc khởi chạy và mức hiện tại của người khởi chạy, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`.

  **Lý do nghiệp vụ:** lô lớn chạy lâu; quyền bị thu hẹp giữa chừng thì lô không được tiếp tục vượt quyền.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-24.1.1` | `CFG-24-01` = 50 MB | Tải tệp 60 MB | Từ chối, nêu giới hạn |
| `AC-24.2.1` | Tải tệp | Đối chiếu cột | Màn hình xem trước 10 dòng đầu theo ánh xạ đã chọn |
| `AC-24.3.1` | Tệp 5.000 dòng, 30 dòng sai định dạng ngày | Chạy | 4.970 vé được nhập; báo cáo liệt kê 30 dòng kèm lý do |
| `AC-24.3.3` | Lô đã tạo vé giữ chỗ cho N thuộc "CSKH" | Lời mời của N được sửa, không còn ghi "CSKH" | Vé chưa gán trong hàng đợi "CSKH"; báo cáo lô ghi giữ chỗ đã chấm dứt |
| `AC-24.3.2` | Người nhập có (Vé, Gán) = Chỉ của mình | Tệp có cột Người phụ trách trỏ tới đồng nghiệp | Các dòng đó báo lỗi vượt quyền |
| `AC-24.4.1` | Sửa 30 dòng lỗi, nhập lại cả tệp | Chạy | Chỉ 30 vé mới; 4.970 vé cũ bị bỏ qua |
| `AC-24.5.1` | Vé nhập khẩu | Xem báo cáo tuân thủ | Không có trong mẫu số |
| `AC-24.6.1` | Lô đang chạy | Người nhập mở màn hình tiến độ | Thấy số dòng đã xử lý, thành công, lỗi |

---

### FEAT-25 — Xuất Dữ liệu Vé có Kiểm soát

**Mô tả nghiệp vụ:** Xuất danh sách vé ra tệp để phân tích hoặc gửi báo cáo cho khách. Tệp chứa dữ liệu cá nhân nên được kiểm soát, che và ghi vết.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Xuất) khác Không có.

**Điều kiện tiên quyết:** Danh sách đang lọc có ít nhất một vé.

**Luồng chính:**

1. Lọc danh sách, bấm xuất.
2. Hệ thống tạo tệp chỉ gồm vé trong mức Xuất của người dùng, áp che dữ liệu, gửi đường dẫn tải có thời hạn.

**Quy tắc nghiệp vụ:**

- **`BR-25.1` (Đường dẫn có thời hạn):** Tệp tải qua đường dẫn có hiệu lực `CFG-25-02`; hết hạn phải xuất lại; chỉ người đã yêu cầu xuất mở được đường dẫn.

  **Lý do nghiệp vụ:** đường dẫn bị chuyển tiếp không được thành lối lấy dữ liệu vô thời hạn.

- **`BR-25.2` (Xuất theo mức của ô Xuất):** Tệp chỉ gồm vé nằm trong mức của ô (Vé hỗ trợ, Xuất), dù danh sách đang hiển thị rộng hơn theo mức Xem — theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.7`. Vé bị lượt chặn không có trong tệp.

  **Lý do nghiệp vụ:** xem rộng để hỗ trợ nhau không có nghĩa là được mang dữ liệu ra ngoài.

- **`BR-25.3` (Che trường nhạy cảm trong tệp):** Trường nhạy cảm của vé (`BR-45.11`) xuất theo đúng mức hiển thị của người xuất; người không có quyền xem đầy đủ nhận giá trị đã che.

  **Lý do nghiệp vụ:** tệp xuất là nơi dữ liệu rời hệ thống; che trên màn hình mà không che trong tệp là vô nghĩa.

- **`BR-25.4` (Ghi vết mọi lần xuất):** Mỗi lần xuất ghi nhật ký xuất dữ liệu (`BR-44.4`).

  **Lý do nghiệp vụ:** căn cứ trả lời "dữ liệu của khách đã ra ngoài những đâu".

- **`BR-25.5` (Phạm vi xuất là mức của ô, không là tên vai trò):** Xuất trong phạm vi đơn vị và xuất toàn workspace là hai mức khác nhau của cùng ô (Vé hỗ trợ, Xuất). Mặc định chỉ Người có toàn quyền có mức Toàn workspace; mức mặc định của các vai trò dựng sẵn tại Mục 5.1; doanh nghiệp đặt mức khác bằng điều chỉnh ô hoặc vai trò tự tạo, chịu trần năng lực của người cấp.

  **Lý do nghiệp vụ:** lấy toàn bộ dữ liệu khách hàng ra ngoài có rủi ro khác hẳn xuất danh sách vé của đội mình; mô hình ô × mức tách được hai rủi ro này mà không cần gắn cho một vai trò cụ thể.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-25.1.1` | `CFG-25-02` = 24 giờ | Mở đường dẫn sau 25 giờ | Hết hiệu lực, phải xuất lại |
| `AC-25.1.2` | Đường dẫn được chuyển cho người khác | Người đó mở | Không tải được |
| `AC-25.2.1` | F có (Vé, Xem) = Toàn workspace, (Vé, Xuất) = Đơn vị và các đơn vị con | Xuất danh sách đang hiện vé của mọi đội | Tệp chỉ gồm vé thuộc đơn vị và các đơn vị con của F |
| `AC-25.3.1` | Người xuất không có quyền xem đầy đủ số điện thoại người yêu cầu | Xuất | Cột số điện thoại trong tệp đã che theo mẫu |
| `AC-25.4.1` | Xuất xong | Mở nhật ký xuất | Có người xuất, thời điểm, bộ lọc, số vé |
| `AC-25.5.1` | Quản lý với mức mặc định, (Vé, Xuất) = Không có | Mở danh sách vé | Nút xuất bị vô hiệu kèm giải thích |
| `AC-25.5.2` | Doanh nghiệp điều chỉnh ô Xuất của Quản lý thành Đơn vị và các đơn vị con | Trưởng nhóm xuất | Tệp gồm vé của đội mình; tìm lựa chọn xuất toàn workspace thì không có |

---

### FEAT-26 — Thùng rác Vé & Phục hồi

**Mô tả nghiệp vụ:** Vé bị xóa vào Thùng rác, phục hồi được khi xóa nhầm; hết thời hạn lưu thì xóa vĩnh viễn.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Xoá) (xóa, phục hồi); người có ô (Vé hỗ trợ, Xoá vĩnh viễn) (xóa vĩnh viễn trước hạn); Hệ thống (xóa vĩnh viễn khi hết hạn).

**Điều kiện tiên quyết:** Người thực hiện có mức của ô tương ứng bao phủ vé.

**Luồng chính:**

1. Xóa vé → vé vào Thùng rác, biến mất khỏi danh sách và báo cáo.
2. Phục hồi trong thời hạn lưu → vé trở lại nguyên trạng.
3. Hết thời hạn → hệ thống xóa vĩnh viễn.

**Quy tắc nghiệp vụ:**

- **`BR-26.1` (Thời hạn lưu):** Vé đã xóa lưu `CFG-26-01` trong Thùng rác rồi tự động xóa vĩnh viễn; thời hạn đặt riêng cho từng doanh nghiệp.

  **Lý do nghiệp vụ:** đủ thời gian phát hiện xóa nhầm mà không giữ dữ liệu đã xóa vô thời hạn.

- **`BR-26.2` (Phục hồi nguyên trạng):** Vé trở lại đúng trạng thái, người phụ trách, hàng đợi và lịch sử. Ngoại lệ: vé đã khử định danh trong lúc nằm Thùng rác phục hồi ở dạng đã khử định danh (`BR-45.6`); người phụ trách cũ không còn đạt `BR-12.6` thì vé về hàng đợi chưa gán.

  **Lý do nghiệp vụ:** phục hồi phải hoàn tác đúng thao tác xóa, nhưng không được khôi phục dữ liệu cá nhân đã gỡ hay giao vé cho người không còn xử lý được.

- **`BR-26.3` (Quyền phục hồi theo ô Xoá):** Phục hồi cần ô (Vé hỗ trợ, Xoá) bao phủ vé — tính trên đơn vị tiếp nhận và người phụ trách vé có lúc bị xóa; xóa vĩnh viễn trước hạn cần ô (Vé hỗ trợ, Xoá vĩnh viễn) bao phủ vé.

  **Lý do nghiệp vụ:** Thùng rác không được thành đường vòng để tiếp cận vé ngoài phạm vi; xóa vĩnh viễn không hoàn tác được nên là ô riêng.

- **`BR-26.4` (Không xóa vé cha):** Vé đang có vé con không xóa được cho tới khi gỡ hết quan hệ cha – con.

  **Lý do nghiệp vụ:** tránh vé con mồ côi mất bối cảnh.

- **`BR-26.5` (Vé đã xóa không tính vào chỉ số):** Vé trong Thùng rác bị loại khỏi mọi báo cáo và thống kê tuân thủ.

  **Lý do nghiệp vụ:** vé xóa nhầm hay thư rác không phản ánh chất lượng phục vụ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-26.1.1` | `CFG-26-01` = 30 ngày | Vé nằm Thùng rác 31 ngày | Vé đã bị xóa vĩnh viễn |
| `AC-26.1.2` | Doanh nghiệp A đặt 60 ngày, B giữ 30 ngày | Vé của mỗi bên nằm 45 ngày | Vé của A còn, vé của B đã xóa vĩnh viễn |
| `AC-26.2.1` | Vé xóa nhầm cùng ngày | Phục hồi | Trạng thái, người phụ trách, hàng đợi, lịch sử như trước khi xóa |
| `AC-26.2.2` | Người phụ trách cũ đã rời workspace | Phục hồi | Vé chưa gán trong hàng đợi của đơn vị tiếp nhận |
| `AC-26.3.1` | Người có (Vé, Xoá) = Đơn vị của mình | Mở Thùng rác | Chỉ thấy và phục hồi được vé thuộc đơn vị mình |
| `AC-26.3.2` | Quản lý có (Vé, Xoá vĩnh viễn) = Không có | Mở Thùng rác | Không có thao tác xóa vĩnh viễn |
| `AC-26.4.1` | Vé cha có 3 vé con | Xóa vé cha | Từ chối, yêu cầu gỡ quan hệ cha – con |
| `AC-26.5.1` | Vé vừa bị xóa | Mở báo cáo tuân thủ | Vé không còn trong số liệu |

## I. QUY TRÌNH NÂNG CAO & RỦI RO KHÁCH HÀNG

### FEAT-27 — Gộp Vé Trùng lặp

**Mô tả nghiệp vụ:** Gộp một vé trùng lặp (vé phụ) vào một vé chính, bảo đảm khách hàng của vé phụ không mất dấu yêu cầu.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Gộp vé) bao phủ cả hai vé; người có ô (Vé hỗ trợ, Hoàn tác gộp vé) (hoàn tác).

**Điều kiện tiên quyết:** Hai vé cùng một khách hàng liên hệ; vé chính chưa ở trạng thái kết thúc.

**Luồng chính:**

1. Chọn vé chính và vé phụ → hệ thống kiểm điều kiện, hiển thị tóm tắt.
2. Xác nhận → nội dung vé phụ nối vào vé chính; vé phụ đóng và khóa; khách của vé phụ được thông báo.
3. Trong thời hạn hoàn tác, người có ô Hoàn tác gộp vé hoàn tác được.

**Luồng ngoại lệ:**

- Vé phụ đang là vé cha → từ chối, yêu cầu gỡ quan hệ cha – con trước.
- Vé chính đang ở trạng thái kết thúc → yêu cầu chọn vé chính khác hoặc mở lại vé đó trước.

**Quy tắc nghiệp vụ:**

- **`BR-27.1` (Hợp nhất lịch sử):** Nội dung trao đổi của vé phụ nối tiếp vào vé chính; vé phụ đóng và khóa (`BR-01.3`), lịch sử riêng vẫn tra cứu được.

  **Lý do nghiệp vụ:** một yêu cầu một đầu mối, không mất nội dung.

- **`BR-27.2` (Toàn vẹn):** Gộp thực hiện trọn vẹn hoặc không gì cả (`NFR-02`).

  **Lý do nghiệp vụ:** gộp dở dang để lại hai vé ở trạng thái không xác định.

- **`BR-27.3` (Thông báo khách của vé phụ):** Ngay khi gộp, khách của vé phụ nhận thông báo qua kênh đã dùng, nêu yêu cầu đã được nhập vào vé chính kèm mã vé chính. Đây là điều kiện để thao tác gộp được coi là hoàn chỉnh; gửi thất bại xử lý theo `BR-37.5`.

  **Lý do nghiệp vụ:** khách không được cảm thấy yêu cầu của mình biến mất.

- **`BR-27.4` (Chỉ gộp vé cùng một khách hàng):** Gộp vé của hai khách hàng liên hệ khác nhau bị từ chối; nhiều khách cùng một sự cố dùng vé cha – vé con (`FEAT-28`).

  **Lý do nghiệp vụ:** gộp khác khách làm khách này thấy nội dung trao đổi của khách kia.

- **`BR-27.5` (Hoàn tác gộp nhầm):** Trong `CFG-27-01` sau khi gộp, người có ô (Vé hỗ trợ, Hoàn tác gộp vé) bao phủ cả hai vé hoàn tác được, khôi phục vé phụ về trạng thái trước khi gộp; nội dung đã nối được gỡ khỏi vé chính. Quá thời hạn, gộp là vĩnh viễn. Mọi lượt hoàn tác ghi nhật ký (`BR-44.1`).

  **Lý do nghiệp vụ:** gộp nhầm là lỗi phổ biến và gần như không phục hồi thủ công được.

- **`BR-27.6` (Vé phụ không tính tuân thủ):** Vé phụ đã gộp bị loại khỏi thống kê tuân thủ; vé chính giữ cam kết ban đầu.

  **Lý do nghiệp vụ:** vé phụ không còn được xử lý độc lập.

- **`BR-27.7` (Gộp và hoàn tác là hai ô riêng):** Gộp vé và Hoàn tác gộp vé là hai thao tác đặc thù riêng của loại dữ liệu Vé hỗ trợ, đặt mức độc lập; mức mặc định tại Mục 5.1, doanh nghiệp điều chỉnh theo mô hình tổ chức. Nhật ký thay đổi giữ cả lượt gộp và lượt hoàn tác, không thao tác nào xóa được dấu vết của thao tác kia.

  **Lý do nghiệp vụ:** đội nhỏ, vé trùng nhiều cần trưởng nhóm gộp ngay; doanh nghiệp có dữ liệu nhạy cảm muốn giữ gộp ở cấp cao hơn; tách hai ô để doanh nghiệp cho người này gộp mà chỉ người khác hoàn tác được nếu muốn.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-27.1.1` | Ba vé cùng khách về một lỗi | Gộp hai vé vào vé đầu | Nội dung hai vé phụ nằm trên dòng thời gian vé chính; hai vé phụ khóa |
| `AC-27.2.1` | Sự cố hệ thống giữa chừng khi gộp | Mở hai vé | Cả hai ở trạng thái trước khi gộp |
| `AC-27.3.1` | Gộp xong | — | Khách của vé phụ nhận thông báo kèm mã vé chính |
| `AC-27.4.1` | Vé của khách A và vé của khách B | Chọn gộp | Từ chối, gợi ý dùng vé cha – vé con |
| `AC-27.5.1` | `CFG-27-01` = 24 giờ; người có ô Hoàn tác gộp vé | Hoàn tác sau 3 giờ | Vé phụ trở lại trạng thái cũ; nội dung gỡ khỏi vé chính; nhật ký có lượt hoàn tác |
| `AC-27.5.2` | Sau 25 giờ | Mở vé chính | Thao tác hoàn tác bị vô hiệu kèm giải thích đã quá hạn |
| `AC-27.6.1` | Vé phụ đã gộp | Xem báo cáo tuân thủ | Vé phụ không có trong mẫu số |
| `AC-27.7.1` | Vai trò X có (Vé, Gộp vé) = Đơn vị của mình, (Vé, Hoàn tác gộp vé) = Không có | Người giữ X gộp rồi thử hoàn tác | Gộp được; hoàn tác bị vô hiệu kèm giải thích |
| `AC-27.7.2` | Vai trò Y có (Vé, Gộp vé) = Không có | Người giữ Y chọn gộp | Thao tác gộp bị vô hiệu |

---

### FEAT-41 — Tách Vé khi Một Yêu cầu Chứa Nhiều Vấn đề

**Mô tả nghiệp vụ:** Một yêu cầu chứa nhiều vấn đề (vừa lỗi kỹ thuật vừa thắc mắc hóa đơn) được tách để mỗi vấn đề có người xử lý, thời hạn và thời điểm kết thúc riêng.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Sửa) bao phủ vé gốc và ô (Vé hỗ trợ, Tạo) = Có.

**Điều kiện tiên quyết:** Vé gốc chưa đóng hoàn tất.

**Luồng chính:**

1. Chọn nội dung cần tách, nhập tiêu đề và phân loại cho vé mới, chọn hàng đợi (`BR-34.2`).
2. Vé mới được tạo, liên kết hai chiều với vé gốc; khách được thông báo.

**Quy tắc nghiệp vụ:**

- **`BR-41.1` (Tách thành vé mới):** Chọn một hoặc nhiều nội dung trao đổi để tách sang vé mới có mã riêng, tiêu đề và phân loại riêng.

  **Lý do nghiệp vụ:** xử lý chung làm sai lệch chỉ số và bắt vấn đề đã xong chờ vấn đề còn lại.

- **`BR-41.2` (Liên kết hai chiều, không xóa khỏi vé gốc):** Hai vé liên kết với nhau; nội dung đã tách vẫn hiển thị trên vé gốc kèm ghi chú đã chuyển sang vé nào.

  **Lý do nghiệp vụ:** dòng trao đổi bất biến (`BR-13.1`); người đọc vé gốc vẫn thấy đủ bối cảnh.

- **`BR-41.3` (Cam kết của vé mới):** Vé mới nhận cam kết tính từ thời điểm tách.

  **Lý do nghiệp vụ:** đây là vấn đề mới được nhận diện; kế thừa hạn của vé gốc có thể làm vé mới vi phạm ngay khi sinh ra.

- **`BR-41.4` (Thông báo khách):** Khách nhận thông báo về vé mới kèm mã, trên đúng kênh họ đã gửi yêu cầu; gửi thất bại theo `BR-37.5`.

  **Lý do nghiệp vụ:** khách biết vấn đề thứ hai đang được xử lý riêng và theo dõi đúng chỗ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-41.1.1` | Vé có phần lỗi đăng nhập và phần hóa đơn | Tách phần hóa đơn, phân loại "Kế toán – Hóa đơn" | Vé mới có mã riêng, đúng phân loại |
| `AC-41.2.1` | Sau khi tách | Mở vé gốc | Nội dung đã tách vẫn còn, kèm "Đã chuyển sang TKT-…" |
| `AC-41.3.1` | Tách lúc 14:00 | Mở vé mới | Hạn chót tính từ 14:00 |
| `AC-41.4.1` | Khách gửi qua thư | Tách xong | Khách nhận thư kèm mã vé mới |
| `AC-41.1.2` | Người tách có (Vé, Tạo) = Không có | Mở vé gốc | Thao tác tách bị vô hiệu |

---

### FEAT-28 — Sự cố Diện rộng theo Mô hình Vé Cha – Vé Con

**Mô tả nghiệp vụ:** Một sự cố khiến nhiều khách cùng báo lỗi được quản lý tập trung: một vé cha đại diện cho sự cố, vé của từng khách là vé con; cập nhật một lần trên vé cha để mọi vé con nhận được, và kết thúc đồng loạt khi sự cố được khắc phục.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Sửa) bao phủ vé cha và các vé con (gán vé con, cập nhật); người có ô (Vé hỗ trợ, Kết thúc vé) bao phủ (kết thúc đồng loạt).

**Điều kiện tiên quyết:** Vé cha đã được tạo.

**Luồng chính:**

1. Tạo vé cha, gán các vé đang mở làm vé con.
2. Đăng cập nhật công khai trên vé cha → lan truyền tới mọi vé con đang mở.
3. Kết thúc vé cha → danh sách vé con đang mở hiện ra để xác nhận kết thúc đồng loạt.

**Quy tắc nghiệp vụ:**

- **`BR-28.1` (Gán vé con):** Gán một hoặc nhiều vé đang mở làm vé con; mỗi vé con phải nằm trong mức Sửa của người gán. Vé con giữ đơn vị tiếp nhận của mình.

  **Lý do nghiệp vụ:** gom dưới một sự cố không được thành đường kéo vé của đội khác về mình.

- **`BR-28.2` (Cập nhật lan truyền):** Cập nhật công khai trên vé cha tự xuất hiện trên dòng trao đổi của mọi vé con **đang mở**, được tính là phản hồi công khai của từng vé con: thỏa `BR-19.2` và hoàn tất mốc Phản hồi Đầu tiên tại **thời điểm đăng trên vé cha**, không phải thời điểm lan truyền xong. Vé con đã đóng không nhận lan truyền.

  **Lý do nghiệp vụ:** không tính là phản hồi thì 150 vé con không bao giờ đóng được; độ trễ lan truyền (`NFR-08`) là việc nội bộ, không được biến thành vi phạm; vé đã đóng không được bị ghi đè số liệu đã chốt.

- **`BR-28.3` (Kết thúc đồng loạt):** Khi vé cha chuyển "đã xử lý xong", hệ thống hiển thị vé con đang mở để xác nhận chuyển đồng loạt sang cùng trạng thái trong một thao tác; mỗi khách nhận khảo sát riêng của vé mình. Vé con ngoài mức Kết thúc vé của người thực hiện được liệt kê riêng, không bị kết thúc.

  **Lý do nghiệp vụ:** vào từng vé để trả lời hàng trăm khách giống nhau là lãng phí; nhưng thao tác đồng loạt không được vượt quyền.

- **`BR-28.4` (Loại trừ vé con cần xử lý riêng):** Bỏ chọn được vé con cần xử lý tiếp. Vé con đã đánh dấu "có vấn đề riêng ngoài sự cố chung" mặc định không được chọn và không bị kết thúc theo vé cha dưới bất kỳ cách gửi yêu cầu nào.

  **Lý do nghiệp vụ:** xử lý đồng loạt không được vô tình đóng vé của khách còn khiếu nại chưa giải quyết.

- **`BR-28.5` (Thông tin kết thúc lấy từ vé cha):** Khi kết thúc đồng loạt, nguyên nhân xử lý, tóm tắt giải pháp và trường bắt buộc của vé con lấy từ vé cha; vé con bị bỏ chọn nhập riêng.

  **Lý do nghiệp vụ:** đáp ứng `BR-19.1` và `BR-05.1` mà không bắt nhập lại 150 lần.

- **`BR-28.6` (Vé con không tính hạn mức):** Vé con không tính vào hạn mức năng lực của người phụ trách (`BR-10.2`); vé cha vẫn tính.

  **Lý do nghiệp vụ:** một sự cố 150 vé không được chiếm hết năng lực của 15 người và đẩy mọi vé không liên quan vào hàng đợi, đúng lúc doanh nghiệp cần năng lực nhất.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-28.1.1` | 150 vé của khách về sự cố thanh toán | Gán làm vé con của vé cha | 150 vé hiển thị quan hệ với vé cha, vẫn thuộc hàng đợi cũ |
| `AC-28.1.2` | Một vé thuộc đội ngoài mức Sửa của người gán | Gán | Vé đó báo lỗi vượt quyền; các vé khác được gán |
| `AC-28.2.1` | Đăng cập nhật trên vé cha lúc 10:00 | Lan truyền xong 10:04 | 150 vé con có cập nhật; mốc Phản hồi Đầu tiên ghi hoàn tất 10:00 |
| `AC-28.2.2` | Một vé con đã đóng | Đăng cập nhật | Vé con đó không nhận cập nhật |
| `AC-28.3.1` | Kết thúc vé cha | Xác nhận danh sách | Vé con được chọn chuyển "đã xử lý xong"; mỗi khách nhận khảo sát riêng |
| `AC-28.4.1` | Một vé con đánh dấu có vấn đề riêng | Kết thúc đồng loạt, kể cả khi yêu cầu kết thúc gửi kèm mã vé con đó | Vé con đó không bị kết thúc |
| `AC-28.5.1` | Vé cha có nguyên nhân và tóm tắt | Kết thúc đồng loạt | Vé con mang nguyên nhân và tóm tắt của vé cha |
| `AC-28.6.1` | A là người phụ trách 20 vé con và 5 vé thường | Phân bổ vé mới | A được tính đang giữ 5 vé |

---

### FEAT-29 — Liên kết Vé với Cơ hội Bán hàng

**Mô tả nghiệp vụ:** Liên kết vé hỗ trợ với một cơ hội bán hàng đang đàm phán để hai bên tra cứu bối cảnh của nhau — tránh nhân viên kinh doanh thúc ép ký trong khi khách đang bức xúc vì sự cố.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Sửa) bao phủ vé, hoặc ô Sửa trên cơ hội theo [`deals-pipeline-srs.md`](./deals-pipeline-srs.md).

**Điều kiện tiên quyết:** Người liên kết có mức Xem bao phủ cả vé và cơ hội.

**Luồng chính:**

1. Từ vé chọn cơ hội, hoặc từ cơ hội chọn vé.
2. Liên kết hiển thị ở cả hai phía; người phụ trách cơ hội nhận lượt đọc vé (`BR-29.4`).

**Quy tắc nghiệp vụ:**

- **`BR-29.1` (Liên kết hai chiều):** Liên kết hiển thị ở cả màn hình vé và màn hình cơ hội.

  **Lý do nghiệp vụ:** mỗi bên làm việc trên màn hình của mình.

- **`BR-29.2` (Tôn trọng quyền xem của từng bên):** Người xem chỉ thấy thông tin của bản ghi phía bên kia nếu mức Xem của họ bao phủ bản ghi đó (cộng `BR-29.4`); nếu không, chỉ thấy có liên kết với nhãn "Bị hạn chế truy cập".

  **Lý do nghiệp vụ:** liên kết không được thành đường xem dữ liệu ngoài phạm vi; ẩn hẳn liên kết làm người dùng tưởng mất dữ liệu.

- **`BR-29.3` (Gỡ liên kết):** Gỡ được khi liên kết nhầm; việc gỡ ghi nhật ký của cả hai bản ghi và thu hồi lượt đọc tại `BR-29.4`.

  **Lý do nghiệp vụ:** liên kết nhầm không được để lại quyền đọc tồn dư.

- **`BR-29.4` (Lượt đọc vé cho người phụ trách cơ hội liên kết):** Khi `CFG-29-01` bật, phân hệ Vé hỗ trợ sinh một lượt cấp mức trần Chỉ đọc trên vé cho Người phụ trách của cơ hội đã liên kết, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.5`: chỉ nới tập bản ghi, có hiệu lực khi người đó có ô (Vé hỗ trợ, Xem) khác Không có, chịu lượt chặn, tự thu hồi khi gỡ liên kết, khi đổi người phụ trách cơ hội, hoặc khi vé đóng hoàn tất. Lượt cấp không mở Vé khiếu nại liên kết với vé.

  **Lý do nghiệp vụ:** nhân viên kinh doanh cần thấy sự cố của khách mình đang đàm phán; vé thuộc đơn vị hỗ trợ nên mức "Đơn vị của mình" của họ không bao giờ phủ tới vé, cần một lượt cấp có điều kiện và có hạn thay vì mở rộng quyền xem vé của cả đội kinh doanh.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-29.1.1` | Liên kết vé với cơ hội "Đại Phát – Gia hạn" | Mở cơ hội | Thấy vé liên kết |
| `AC-29.2.1` | Nhân viên kinh doanh khác, không phụ trách cơ hội, mức Xem không bao phủ vé | Mở cơ hội | Thấy liên kết "Bị hạn chế truy cập", không thấy nội dung vé |
| `AC-29.3.1` | Gỡ liên kết | Mở nhật ký cả hai bản ghi | Có dòng gỡ liên kết; người phụ trách cơ hội không còn mở được vé |
| `AC-29.4.1` | `CFG-29-01` bật; người phụ trách cơ hội có (Vé, Xem) = Đơn vị của mình | Mở vé liên kết | Xem được, không sửa được; quyền hiệu lực ghi nguồn "Liên kết cơ hội" |
| `AC-29.4.2` | Người phụ trách cơ hội có (Vé, Xem) = Không có | Mở vé liên kết | Không mở được |
| `AC-29.4.3` | Cơ hội đổi người phụ trách từ S sang T | S mở vé | Không còn mở được; T mở được |
| `AC-29.4.4` | Vé có vé khiếu nại liên kết | Người phụ trách cơ hội mở vé | Liên kết tới vé khiếu nại hiển thị "Bị hạn chế truy cập" |

---

### FEAT-30 — Cảnh báo Rủi ro Tự động sang Cơ hội Bán hàng

**Mô tả nghiệp vụ:** Khi khách có vé ưu tiên cao đang mở, hệ thống tự hiển thị cảnh báo trên cơ hội bán hàng của khách đó, không phụ thuộc việc nhân viên có nhớ tra cứu. Phân hệ Vé hỗ trợ sở hữu điều kiện phát cảnh báo; việc hiển thị trên thẻ cơ hội thuộc [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `FEAT-31`.

**Vai trò sử dụng chính:** Nhân viên kinh doanh (nhận cảnh báo); người có quyền Cấu hình cam kết dịch vụ (điều kiện); Hệ thống.

**Điều kiện tiên quyết:** Khách hàng doanh nghiệp có cơ hội đang mở.

**Luồng chính:**

1. Vé của khách thỏa điều kiện → cờ rủi ro bật trên cơ hội đang mở của khách.
2. Không còn vé thỏa điều kiện → cờ tự tắt.

**Quy tắc nghiệp vụ:**

- **`BR-30.1` (Điều kiện phát cảnh báo):** Doanh nghiệp cấu hình mức ưu tiên tối thiểu của vé (`CFG-30-01`) và có chỉ phát khi vé đang vi phạm cam kết hay không (`CFG-30-02`).

  **Lý do nghiệp vụ:** mỗi doanh nghiệp có ngưỡng "nghiêm trọng" khác nhau; cảnh báo quá dày làm kinh doanh bỏ qua.

- **`BR-30.2` (Tự tắt khi hết điều kiện):** Cờ tự tắt khi không còn vé nào thỏa điều kiện.

  **Lý do nghiệp vụ:** cờ cũ còn treo làm kinh doanh mất tin vào cảnh báo.

- **`BR-30.3` (Xem nhanh bối cảnh trong phạm vi quyền):** Người xem cơ hội thấy danh sách vé gây cảnh báo (mã, mức ưu tiên, tình trạng) chỉ với vé nằm trong mức Xem của họ (cộng `BR-29.4`); nếu không, cờ vẫn hiển thị nhưng không kèm chi tiết vé.

  **Lý do nghiệp vụ:** việc khách đang có sự cố là điều người bán cần biết; nội dung khiếu nại thì không.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-30.1.1` | `CFG-30-01` = Cao, `CFG-30-02` = Tắt | Khách tạo vé mức Cao | Cờ rủi ro bật trên cơ hội đang mở của khách |
| `AC-30.1.2` | `CFG-30-02` = Bật | Vé mức Cao chưa vi phạm | Chưa có cờ; khi vé vi phạm thì cờ bật |
| `AC-30.2.1` | Vé gây cờ được xử lý xong | — | Cờ tự tắt |
| `AC-30.3.1` | Người xem không có mức Xem trên vé | Mở cơ hội | Thấy cờ, không thấy mã và nội dung vé |

---

### FEAT-31 — Chuyển Giải pháp thành Bản nháp Bài viết Tri thức

**Mô tả nghiệp vụ:** Chuyển nội dung câu hỏi và giải pháp từ vé đã xử lý thành bản nháp bài viết tri thức, để tái sử dụng và giảm vé lặp lại. Soạn, phân loại và xuất bản cơ sở tri thức nằm ngoài phạm vi; tính năng dừng ở bản nháp, việc duyệt và xuất bản theo quy trình của cơ sở tri thức.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Xem) bao phủ vé (đề xuất); người có quyền Duyệt bài viết tri thức (duyệt).

**Điều kiện tiên quyết:** Vé đã xử lý xong.

**Luồng chính:**

1. Từ vé chọn "Tạo bản nháp tri thức" → bản nháp điền sẵn câu hỏi và giải pháp.
2. Người duyệt rà soát, loại bỏ thông tin định danh, xác nhận, duyệt.

**Quy tắc nghiệp vụ:**

- **`BR-31.1` (Tạo bản nháp từ vé):** Bản nháp điền sẵn tiêu đề, mô tả vấn đề, giải pháp từ vé.

  **Lý do nghiệp vụ:** giải pháp tốt nếu không được chuyển thành tri thức sẽ mất trong vé cũ.

- **`BR-31.2` (Làm sạch dữ liệu cá nhân):** Không đường nào duyệt được bản nháp khi người duyệt chưa xác nhận đã rà soát và loại bỏ thông tin định danh khách hàng.

  **Lý do nghiệp vụ:** bài viết là tài liệu dùng chung, không được chứa dữ liệu cá nhân của khách cụ thể.

- **`BR-31.3` (Duyệt trước khi chuyển xuất bản):** Bản nháp chỉ chuyển sang quy trình xuất bản sau khi người có quyền Duyệt bài viết tri thức duyệt; người soạn không tự duyệt bản nháp của mình.

  **Lý do nghiệp vụ:** người viết khó tự thấy lỗi và thông tin định danh sót lại trong bài của mình.

- **`BR-31.4` (Giữ liên kết nguồn):** Bản nháp lưu tham chiếu tới vé gốc; người mở tham chiếu chỉ xem được vé khi mức Xem của họ bao phủ vé.

  **Lý do nghiệp vụ:** cần bối cảnh khi cập nhật bài; tham chiếu không được thành đường xem vé.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-31.1.1` | Vé đã xử lý xong | Tạo bản nháp | Câu hỏi và giải pháp được điền sẵn |
| `AC-31.2.1` | Bản nháp còn tên và số điện thoại khách | Người duyệt bấm duyệt khi chưa đánh dấu đã rà soát | Nút duyệt bị vô hiệu cho tới khi xác nhận |
| `AC-31.3.1` | A có quyền Duyệt bài viết tri thức, tự soạn bản nháp | A mở bản nháp của mình | Không có thao tác duyệt |
| `AC-31.4.1` | Người đọc bài không có mức Xem trên vé gốc | Bấm tham chiếu | Hiển thị "Bị hạn chế truy cập" |

---

### FEAT-32 — Giám sát Trực tiếp & Hướng dẫn Hậu trường

**Mô tả nghiệp vụ:** Người hướng dẫn theo dõi cách nhân viên mới xử lý vé và gửi chỉ dẫn riêng ngay trong lúc làm, khách không thấy.

**Vai trò sử dụng chính:** Người có quyền Giám sát & hướng dẫn tư vấn viên; tư vấn viên được hướng dẫn.

**Điều kiện tiên quyết:** `CFG-32-01` bật; người giám sát có mức Xem bao phủ vé đang được xử lý.

**Luồng chính:**

1. Bật giám sát một tư vấn viên → tư vấn viên thấy chỉ báo đang được giám sát.
2. Gửi chỉ dẫn → chỉ tư vấn viên thấy.
3. Kết thúc phiên, ghi đánh giá.

**Quy tắc nghiệp vụ:**

- **`BR-32.1` (Chỉ dẫn không lộ ra ngoài):** Chỉ dẫn chỉ hiển thị cho tư vấn viên được hướng dẫn, lưu dạng ghi chú nội bộ, không bao giờ xuất hiện ở giao diện khách hàng.

  **Lý do nghiệp vụ:** khách thấy chỉ dẫn nội bộ là mất uy tín và có thể lộ thông tin.

- **`BR-32.2` (Minh bạch với người bị giám sát):** Tư vấn viên thấy chỉ báo đang được giám sát suốt phiên, không chỉ lúc bắt đầu. Giám sát chỉ áp trên vé mà mức Xem của người giám sát bao phủ.

  **Lý do nghiệp vụ:** giám sát ngầm không chấp nhận được về quan hệ lao động; giám sát không được thành đường xem vé ngoài phạm vi.

- **`BR-32.3` (Ghi nhận phục vụ đào tạo):** Phiên hướng dẫn được ghi kèm đánh giá và ghi chú đào tạo.

  **Lý do nghiệp vụ:** theo dõi tiến bộ của nhân viên mới (`KPI-09`).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-32.1.1` | Gửi chỉ dẫn | Khách mở vé | Không thấy chỉ dẫn |
| `AC-32.2.1` | Phiên giám sát kéo dài 30 phút | Tư vấn viên quan sát | Chỉ báo hiển thị suốt 30 phút |
| `AC-32.2.2` | `CFG-32-01` tắt | Người có quyền mở giám sát | Thao tác không có |
| `AC-32.3.1` | Kết thúc phiên | Mở lịch sử đào tạo của tư vấn viên | Có phiên, đánh giá, ghi chú |

---

### FEAT-33 — Theo dõi Thời lượng Hỗ trợ & Giờ Tính phí

**Mô tả nghiệp vụ:** Ghi nhận thời gian xử lý vé và đánh dấu giờ tính phí với hợp đồng bảo trì có tính phí, làm căn cứ xuất hóa đơn (ngoài phạm vi) và đánh giá chi phí phục vụ.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Sửa) bao phủ vé (ghi giờ); người có quyền Duyệt giờ tính phí (duyệt).

**Điều kiện tiên quyết:** Vé tồn tại.

**Luồng chính:**

1. Bấm giờ khi bắt đầu và kết thúc, hoặc nhập tay số phút kèm mô tả.
2. Đánh dấu tính phí theo điều khoản hợp đồng.
3. Người có quyền duyệt; giờ đã duyệt vào tệp bàn giao cho khâu hóa đơn.

**Quy tắc nghiệp vụ:**

- **`BR-33.1` (Ghi nhận thời gian):** Bấm giờ hoặc nhập tay kèm mô tả công việc.

  **Lý do nghiệp vụ:** không có mô tả thì không đối soát được với khách.

- **`BR-33.2` (Phân biệt giờ tính phí):** Mỗi khoản đánh dấu có tính phí hay không theo hợp đồng của khách, kèm đơn giá tại thời điểm ghi nhận.

  **Lý do nghiệp vụ:** đơn giá đổi sau đó không được làm sai số tiền của giờ đã làm.

- **`BR-33.3` (Duyệt trước khi tính phí):** Chỉ giờ đã được người có quyền Duyệt giờ tính phí duyệt mới vào tệp bàn giao; người ghi giờ không tự duyệt giờ của mình.

  **Lý do nghiệp vụ:** tránh sai lệch khi đối soát với khách.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-33.1.1` | Bấm giờ 90 phút | Kết thúc | Khoản 90 phút được ghi, yêu cầu mô tả |
| `AC-33.2.1` | Khách có hợp đồng tính phí | Ghi giờ | Khoản giờ có dấu tính phí và đơn giá hiện hành |
| `AC-33.2.2` | Đơn giá đổi sau đó | Mở khoản giờ cũ | Đơn giá cũ giữ nguyên |
| `AC-33.3.1` | Có khoản đã duyệt và chưa duyệt | Xuất tệp bàn giao | Chỉ có khoản đã duyệt |
| `AC-33.3.2` | A có quyền Duyệt giờ tính phí | Mở khoản giờ do chính A ghi | Không có thao tác duyệt |

## J. BÁO CÁO & GIÁM SÁT HIỆU SUẤT

### FEAT-42 — Bảng điều khiển Hàng đợi Thời gian thực

**Mô tả nghiệp vụ:** Một màn hình cho biết tình hình hàng đợi ngay lúc này để điều phối kịp thời: bao nhiêu vé chưa ai nhận, vé nào sắp tới hạn, ai đang quá tải, vé nào có khách bức xúc.

**Vai trò sử dụng chính:** Người có quyền Xem báo cáo hỗ trợ (thường là Trưởng nhóm, Trưởng phòng Dịch vụ Khách hàng).

**Điều kiện tiên quyết:** Người xem có ô (Vé hỗ trợ, Xem) khác Không có.

**Luồng chính:**

1. Mở bảng điều khiển → chỉ số của các hàng đợi trong phạm vi.
2. Bấm vào một chỉ số → danh sách vé tương ứng; gán hoặc chuyển vé ngay tại đó.

**Quy tắc nghiệp vụ:**

- **`BR-42.1` (Chỉ số tối thiểu):** Theo từng hàng đợi trong phạm vi: số vé chưa gán (và vé quá ngưỡng tồn đọng), số vé đang mở theo trạng thái, số vé đã qua ngưỡng cảnh báo nhưng chưa vi phạm, số vé đã vi phạm, số vé mang cờ khách hàng không hài lòng, số vé có người phụ trách đang tạm ngưng (`BR-36.4`), và số vé đang giữ của từng tư vấn viên so với hạn mức của người đó (`BR-10.1`). Số liệu cập nhật theo `NFR-13`.

  **Lý do nghiệp vụ:** chỉ có danh sách vé thô thì trưởng nhóm phải tự lọc và ước lượng, phát hiện vấn đề quá muộn.

- **`BR-42.2` (Phạm vi theo quyền):** Chỉ số chỉ tính vé nằm trong mức (Vé hỗ trợ, Xem) của người xem và loại vé bị lượt chặn đối với họ, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.3`. Phần khối lượng từng tư vấn viên cần quyền Xem báo cáo hỗ trợ.

  **Lý do nghiệp vụ:** số đếm cũng là dữ liệu; đếm cả vé ngoài phạm vi là để lộ quy mô khiếu nại của đội khác. Khối lượng từng người là thông tin quản lý nhân sự.

- **`BR-42.3` (Điều phối trực tiếp):** Từ bảng điều khiển gán, chuyển hàng đợi hoặc chuyển hàng loạt được mà không đổi màn hình; mỗi thao tác chịu đúng quy tắc của nó (`BR-12.6`, `BR-34.8`, `FEAT-36`).

  **Lý do nghiệp vụ:** đổi màn hình trong giờ cao điểm làm chậm phản ứng; nhưng tiện lợi không được nới quyền.

- **`BR-42.4` (Bộ lọc lưu lại):** Người dùng lưu được bộ lọc riêng (ví dụ "Vé quá hạn của đội tôi") để mở lại nhanh; bộ lọc lưu điều kiện lọc, không lưu kết quả, nên luôn áp phạm vi hiện tại của người mở.

  **Lý do nghiệp vụ:** bộ lọc chia sẻ hay lưu lại không được thành đường xem vé mà người mở không còn quyền.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-42.1.1` | Giờ cao điểm | Trưởng nhóm mở bảng điều khiển | Thấy đủ các chỉ số tại `BR-42.1` cho hàng đợi của đội |
| `AC-42.1.2` | A giữ 10/10 vé, B giữ 3/15 | Quan sát | Hiển thị "A 10/10", "B 3/15" |
| `AC-42.1.3` | Một vé được tư vấn viên khác nhận | Quan sát bảng điều khiển đang mở | Số vé chưa gán giảm trong thời hạn `NFR-13` mà không cần tải lại |
| `AC-42.2.1` | Trưởng nhóm đội Hà Nội | Mở bảng điều khiển | Không có số liệu của đội Đà Nẵng |
| `AC-42.2.2` | Một vé bị chặn đối với người xem | Quan sát số vé vi phạm | Vé đó không được đếm |
| `AC-42.3.1` | Trên bảng điều khiển | Chuyển 3 vé từ A sang B | Thực hiện được tại chỗ; B phải đạt `BR-12.6` |
| `AC-42.4.1` | Lưu bộ lọc "Vé quá hạn của đội tôi"; sau đó bị chuyển sang đội khác | Mở lại bộ lọc | Kết quả theo phạm vi mới |

---

### FEAT-43 — Báo cáo Tuân thủ Cam kết, Hiệu suất & Khối lượng

**Mô tả nghiệp vụ:** Bộ báo cáo định kỳ để đánh giá chất lượng dịch vụ, đánh giá nhân sự, xếp lịch trực và báo cáo tuân thủ cho khách hàng theo hợp đồng — nơi các chỉ số Mục 2.6 được đo.

**Vai trò sử dụng chính:** Người có quyền Xem báo cáo hỗ trợ; mọi tư vấn viên (chỉ số của chính mình).

**Điều kiện tiên quyết:** Người xem có ô (Vé hỗ trợ, Xem) khác Không có.

**Luồng chính:**

1. Chọn báo cáo, kỳ, bộ lọc.
2. Xem, xuất hoặc đặt lịch gửi định kỳ.

**Quy tắc nghiệp vụ:**

- **`BR-43.1` (Báo cáo tuân thủ cam kết):** Tỷ lệ tuân thủ hai mốc, lọc theo kỳ, hàng đợi, tư vấn viên, hạng khách hàng, từng khách hàng doanh nghiệp. Số liệu chỉ tính vé trong mức Xem của người xem.

  **Lý do nghiệp vụ:** báo cáo theo hợp đồng cho từng khách là nghĩa vụ phổ biến trong B2B; báo cáo không được thành đường xem vé ngoài phạm vi.

- **`BR-43.2` (Quy tắc tính tỷ lệ tuân thủ):** Số vé hoàn tất đúng hạn chia tổng số vé đến hạn trong kỳ. Loại khỏi mẫu số: vé phụ đã gộp (`BR-27.6`), vé trong Thùng rác (`BR-26.5`), vé nhập khẩu (`BR-24.5`), vé mang nhãn loại trừ do doanh nghiệp cấu hình (`CFG-43-01`, ví dụ vé nội bộ thử nghiệm). Thời gian tạm dừng hợp lệ không tính vào thời gian xử lý. Quy tắc in kèm trên báo cáo.

  **Lý do nghiệp vụ:** hai bên phải đối soát được cùng một con số khi tranh chấp.

- **`BR-43.3` (Hiệu suất tư vấn viên):** Theo từng người: số vé đã xử lý xong, thời gian xử lý trung bình, tỷ lệ tuân thủ, điểm hài lòng trung bình, số vé bị mở lại, số vé bị leo thang. Mỗi người luôn xem được chỉ số của chính mình; xem của người khác cần quyền Xem báo cáo hỗ trợ và chỉ với người có vé nằm trong mức Xem của người xem.

  **Lý do nghiệp vụ:** tư vấn viên cần biết mình đang ở đâu; chỉ số của đồng nghiệp là thông tin quản lý nhân sự.

- **`BR-43.4` (Khối lượng công việc):** Phân bố vé theo khung giờ trong ngày và ngày trong tuần (xếp lịch trực); theo phân loại (tìm nguyên nhân gốc).

  **Lý do nghiệp vụ:** xếp ca theo cảm tính để thiếu người đúng giờ cao điểm.

- **`BR-43.5` (Sự cố diện rộng):** Với mỗi vé cha: số vé con, thời gian từ tạo vé cha tới khi mọi khách nhận phản hồi đầu tiên, thời gian tới khi mọi vé con xử lý xong — cách đo `KPI-07`.

  **Lý do nghiệp vụ:** đo năng lực phản ứng với sự cố lớn.

- **`BR-43.6` (Phối hợp với bán hàng):** Số cơ hội được cảnh báo rủi ro (`FEAT-30`) trong kỳ và bao nhiêu trường hợp vé liên quan xử lý xong trước khi cơ hội chốt — cách đo `KPI-08`.

  **Lý do nghiệp vụ:** đánh giá hiệu quả phối hợp hỗ trợ – bán hàng.

- **`BR-43.7` (Giờ hỗ trợ và giờ tính phí):** Tổng giờ thực tế và giờ tính phí đã duyệt (`FEAT-33`) theo khách hàng, hợp đồng, tư vấn viên — cách đo `KPI-10`; giá trị tiền hiển thị theo `BR-45.11`.

  **Lý do nghiệp vụ:** đầu vào cho khâu hóa đơn và để biết khách nào tiêu tốn nguồn lực vượt giá trị hợp đồng.

- **`BR-43.8` (Xuất và gửi định kỳ):** Mọi báo cáo xuất được và đặt lịch gửi định kỳ được. Báo cáo theo lịch tính theo phần giao của mức lúc đặt lịch và mức hiện tại của người đặt lịch ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`); người nhận trong workspace phải Đang hoạt động; thêm người nhận ngoài workspace (ví dụ khách hàng doanh nghiệp) cần ô (Vé hỗ trợ, Xuất) bao phủ dữ liệu báo cáo, và mỗi lần gửi ghi nhật ký xuất (`BR-44.4`).

  **Lý do nghiệp vụ:** gửi định kỳ là xuất dữ liệu lặp lại; nó không được tiếp tục sau khi người đặt lịch đã mất quyền.

- **`BR-43.9` (Sử dụng cơ chế tạm dừng):** Theo người và hàng đợi: tỷ lệ thời gian tạm dừng, số vé vượt ngưỡng `BR-09.4`, số vé đang tạm dừng tại thời điểm xem — cách đo `KPI-11`.

  **Lý do nghiệp vụ:** `BR-09.4` chỉ bắt tạm dừng nhiều lần trên một vé, không bắt người tạm dừng mỗi vé một lần trên nhiều vé để né hạn mức (`BR-10.2`).

- **`BR-43.10` (Gián đoạn do vắng mặt):** Với mỗi kỳ vắng mặt, tạm ngưng: số vé đang mở lúc bắt đầu, thời gian tới khi chuyển giao xong, số vé vi phạm trong khoảng đó — cách đo `KPI-12`.

  **Lý do nghiệp vụ:** đo được thì mới cải thiện được quy trình chuyển giao.

- **`BR-43.11` (Thực thi yêu cầu xóa dữ liệu cá nhân):** Số yêu cầu đã nhận trong kỳ, số hoàn tất đúng hạn, số quá hạn kèm lý do, số đang tạm dừng theo yêu cầu pháp lý, phạm vi vé của từng yêu cầu — cách đo `KPI-13`. Báo cáo này chỉ dành cho Người có toàn quyền và Người phụ trách Bảo vệ Dữ liệu.

  **Lý do nghiệp vụ:** hồ sơ xuất trình khi cơ quan quản lý kiểm tra; danh sách yêu cầu xóa là dữ liệu nhạy cảm của chính khách hàng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-43.1.1` | Lọc theo khách hàng doanh nghiệp X, tháng trước | Xem | Tỷ lệ tuân thủ hai mốc của riêng X |
| `AC-43.2.1` | Kỳ có vé phụ đã gộp, vé nhập khẩu, vé mang nhãn loại trừ | Xem báo cáo | Các vé đó không có trong mẫu số; quy tắc tính in kèm |
| `AC-43.3.1` | Nhân viên Hỗ trợ không có quyền Xem báo cáo hỗ trợ | Mở báo cáo hiệu suất | Chỉ thấy chỉ số của chính mình |
| `AC-43.4.1` | Báo cáo khối lượng tuần | Xem | Biểu đồ theo khung giờ và ngày trong tuần, theo phân loại |
| `AC-43.5.1` | Một sự cố 150 vé con đã xử lý | Xem báo cáo sự cố | Thấy số vé con và hai khoảng thời gian |
| `AC-43.6.1` | Kỳ có 20 cơ hội bị cảnh báo | Xem | Thấy 20 và số trường hợp vé xong trước khi chốt |
| `AC-43.7.1` | Người xem không có quyền Duyệt giờ tính phí | Xem báo cáo giờ | Số giờ hiển thị; giá trị tiền bị ẩn |
| `AC-43.8.1` | Đặt lịch gửi ngày 1 hằng tháng | Tới ngày 1 | Báo cáo gửi tới người nhận đã chỉ định |
| `AC-43.8.2` | Người đặt lịch bị thu hẹp mức Xem về đội mình | Lần gửi kế tiếp | Báo cáo chỉ gồm vé của đội đó |
| `AC-43.8.3` | Người đặt lịch có (Vé, Xuất) = Không có | Thêm địa chỉ thư của khách hàng làm người nhận | Không thêm được, kèm giải thích |
| `AC-43.9.1` | Tư vấn viên tạm dừng 30 vé mỗi vé một lần | Xem báo cáo tạm dừng | Thấy số vé đang tạm dừng cao bất thường của người đó |
| `AC-43.10.1` | Kỳ vắng mặt của A | Xem báo cáo | Số vé lúc bắt đầu, thời gian chuyển giao, số vé vi phạm |
| `AC-43.11.1` | Quản lý không có toàn quyền, không giữ chức danh Bảo vệ Dữ liệu | Tìm báo cáo yêu cầu xóa | Không có |

---

## K. NHẬT KÝ THAO TÁC & TRUY VẾT

### FEAT-44 — Nhật ký Thay đổi & Truy vết Thao tác trên Vé

**Mô tả nghiệp vụ:** Khi tranh chấp "ai đã hứa gì, khi nào" hoặc rà soát vé xử lý sai, doanh nghiệp cần biết chính xác ai đã đổi gì trên vé và lúc nào. Nội dung trao đổi đã bất biến (`BR-13.1`); các trường quan trọng vẫn sửa được nên cần ghi vết.

**Vai trò sử dụng chính:** Người có ô (Vé hỗ trợ, Xem nhật ký thay đổi) bao phủ vé; Hệ thống (ghi).

**Điều kiện tiên quyết:** Không có.

**Luồng chính:**

1. Mọi thay đổi thuộc `BR-44.1` được ghi tự động.
2. Người có quyền mở tab nhật ký trên vé, lọc theo trường, người, thời gian.

**Quy tắc nghiệp vụ:**

- **`BR-44.1` (Thay đổi phải ghi vết):** Tối thiểu: trạng thái, mức ưu tiên, người phụ trách (gồm nhận việc, phân bổ tự động kèm tên quy tắc, bàn giao, trả về hàng đợi), hàng đợi và đơn vị tiếp nhận (chuyển hàng đợi kèm lý do), phân loại, chính sách cam kết, hạn chót, gộp, hoàn tác gộp, tách, gán và gỡ vé cha – con, liên kết cơ hội, khử định danh.

  **Lý do nghiệp vụ:** đây là những trường làm thay đổi cam kết hoặc trách nhiệm với khách.

- **`BR-44.2` (Nội dung mỗi dòng):** Thời điểm, người thực hiện (hoặc "Hệ thống" kèm quy tắc), giá trị trước, giá trị sau. Với trường nhạy cảm (`BR-45.11`), nhật ký ghi "đã thay đổi" mà không lưu giá trị thật.

  **Lý do nghiệp vụ:** đủ căn cứ đối soát; nhật ký không được thành nơi sao lưu dữ liệu nhạy cảm.

- **`BR-44.3` (Bất biến):** Nhật ký không sửa, không xóa được bởi bất kỳ ai (`NFR-12`), trừ thay thế nội dung cá nhân khi khử định danh (`BR-45.8`).

  **Lý do nghiệp vụ:** nhật ký sửa được thì không chứng minh được gì.

- **`BR-44.4` (Ghi vết xuất dữ liệu):** Mỗi lần xuất vé ra tệp hoặc gửi báo cáo ra ngoài ghi ai, lúc nào, bộ lọc, số vé, người nhận.

  **Lý do nghiệp vụ:** căn cứ trả lời "dữ liệu của khách đã ra ngoài những đâu" (`BR-45.5`).

- **`BR-44.5` (Quyền đọc nhật ký):** Nhật ký của một vé hiển thị trên vé cho người có ô (Vé hỗ trợ, Xem nhật ký thay đổi) bao phủ vé. Tra cứu nhật ký trên toàn kho theo sàn bắt buộc về quyền đọc nhật ký kiểm toán tại [`contacts-srs.md`](./contacts-srs.md) `NFR-14`. Người xem nhật ký chỉ thấy tên và nội dung nhận diện của bản ghi khi mức Xem của họ bao phủ bản ghi đó, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.7`.

  **Lý do nghiệp vụ:** đối soát một vé với khách hàng là việc hằng ngày của quản lý; truy vấn toàn kho nhật ký là quyền kiểm soát, không được cấp theo vai trò vận hành.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-44.1.1` | Mức ưu tiên hạ từ Cao xuống Trung bình | Mở nhật ký vé | Có dòng ghi người đổi, thời điểm, giá trị trước và sau |
| `AC-44.1.2` | Vé được chuyển hàng đợi | Mở nhật ký | Có dòng hàng đợi cũ, mới, lý do |
| `AC-44.2.1` | Người yêu cầu đổi số điện thoại liên hệ trên vé | Mở nhật ký | Dòng ghi "Số điện thoại: đã thay đổi", không có số thật |
| `AC-44.3.1` | Quản trị viên mở một dòng nhật ký | Tìm thao tác sửa, xóa | Không có; gửi yêu cầu xóa trực tiếp tới hệ thống cũng bị từ chối |
| `AC-44.4.1` | Xuất 200 vé | Mở nhật ký xuất | Có người xuất, thời điểm, bộ lọc, 200 vé |
| `AC-44.5.1` | Nhân viên Hỗ trợ có (Vé, Xem nhật ký thay đổi) = Chỉ của mình | Mở vé của mình, rồi vé của đồng nghiệp | Thấy nhật ký vé của mình; tab nhật ký vé đồng nghiệp không có |
| `AC-44.5.2` | Quản lý không thuộc nhóm được đọc nhật ký toàn kho theo sàn | Tìm màn hình tra cứu nhật ký toàn kho | Không có |

---

## L. VÒNG ĐỜI DỮ LIỆU & BẢO VỆ DỮ LIỆU CÁ NHÂN

### FEAT-45 — Lưu trữ, Khử định danh & Thực thi Yêu cầu Xóa Dữ liệu Cá nhân

**Mô tả nghiệp vụ:** Vé chứa nhiều dữ liệu cá nhân (họ tên, số điện thoại, thư điện tử, nội dung trao đổi, tệp đính kèm). Pháp luật bảo vệ dữ liệu cá nhân áp dụng cho doanh nghiệp (ví dụ Nghị định 13/2023/NĐ-CP tại Việt Nam) đòi đáp ứng được yêu cầu xóa của chủ thể dữ liệu và không lưu lâu hơn cần thiết. Tính năng quy định vòng đời lưu trữ, trường nhạy cảm của vé, và cách phân hệ Vé hỗ trợ thực thi yêu cầu xóa khởi động từ quy trình quyền chủ thể dữ liệu của [`contacts-srs.md`](./contacts-srs.md) `FEAT-33`, theo hợp đồng tại ADR-0008. Hệ thống không tự duy trì quy định pháp lý của quốc gia nào; doanh nghiệp đặt tham số theo quy định áp dụng cho mình.

**Vai trò sử dụng chính:** Người có toàn quyền, Người phụ trách Bảo vệ Dữ liệu (theo dõi, tạm dừng theo yêu cầu pháp lý); Hệ thống (thực thi).

**Điều kiện tiên quyết:** Yêu cầu xóa đã được tiếp nhận và xác minh theo quy trình của phân hệ Khách hàng; hoặc vé đến hạn lưu trữ.

**Luồng chính:**

1. Phân hệ Khách hàng chuyển yêu cầu xóa theo mã khách hàng → phân hệ Vé hỗ trợ xác định mọi vé của khách ở mọi nơi lưu.
2. Hiển thị xem trước phạm vi cho người theo dõi; thực thi khử định danh và xóa tệp.
3. Trả kết quả về Biên bản Hoàn tất Xử lý (hàng Vé hỗ trợ).

**Quy tắc nghiệp vụ:**

- **`BR-45.1` (Thời hạn lưu trữ vé đã đóng):** Vé đã đóng hoàn tất quá `CFG-45-01` chuyển sang lưu trữ dài hạn: không còn trong danh sách làm việc, vẫn tra cứu được để đối soát.

  **Lý do nghiệp vụ:** giảm phơi bày dữ liệu cá nhân trong công việc hằng ngày mà vẫn giữ được căn cứ đối soát.

- **`BR-45.2` (Khử định danh thay vì xóa vé):** Thực thi yêu cầu xóa bằng khử định danh: thay họ tên, số điện thoại, thư điện tử, toàn bộ nội dung do khách và nhân viên nhập trong trao đổi của các vé thuộc phạm vi bằng dấu hiệu đã ẩn danh; xóa tệp đính kèm do khách gửi (`BR-15.4`); giữ dữ liệu thống kê phi định danh (thời gian xử lý, phân loại, kết quả tuân thủ) và siêu dữ liệu của từng lượt trao đổi (nhân viên nào, lúc nào, loại nội dung).

  **Lý do nghiệp vụ:** đáp ứng quyền của chủ thể dữ liệu mà không làm sai chỉ số đã công bố; thay trọn nội dung thay vì dò xóa một phần vì nhận diện tự động thông tin cá nhân trong văn xuôi không bao giờ chắc chắn — một lần sót là một lần không đáp ứng.

- **`BR-45.3` (Ghi vết khử định danh):** Mỗi lần khử định danh ghi ai yêu cầu, ai thực hiện hoặc khởi động, thời điểm, phạm vi vé.

  **Lý do nghiệp vụ:** bằng chứng tuân thủ khi cơ quan quản lý kiểm tra.

- **`BR-45.4` (Thời hạn đáp ứng):** Việc thực thi trên vé hoàn tất trong thời hạn của yêu cầu do phân hệ Khách hàng chuyển sang; khi yêu cầu không mang thời hạn, dùng `CFG-45-02`. Người phụ trách Bảo vệ Dữ liệu (hoặc Chủ sở hữu khi chưa chỉ định) được cảnh báo ở các mốc `CFG-45-03` trước hạn.

  **Lý do nghiệp vụ:** thời hạn là của cả yêu cầu, không riêng phân hệ Vé; thời hạn pháp lý khác nhau theo quốc gia nên là tham số doanh nghiệp tự đặt.

- **`BR-45.5` (Dữ liệu đã ra ngoài):** Kết quả trả về biên bản liệt kê các lượt xuất và gửi báo cáo đã chứa vé của khách (từ `BR-44.4`), để doanh nghiệp biết dữ liệu đã ra ngoài những đâu.

  **Lý do nghiệp vụ:** khử định danh trong hệ thống không thu hồi được tệp đã xuất; doanh nghiệp phải biết để xử lý ngoài hệ thống.

- **`BR-45.6` (Phạm vi khử định danh):** Bao trùm mọi nơi dữ liệu cá nhân của khách còn trên vé: vé đang mở, đã đóng, trong Thùng rác chưa xóa vĩnh viễn, trong lưu trữ dài hạn, tệp đính kèm, vé khiếu nại liên quan. Vé đã khử định danh phục hồi từ Thùng rác vẫn ở dạng đã khử định danh (`BR-26.2`).

  **Lý do nghiệp vụ:** bỏ sót Thùng rác hay kho lưu trữ là dữ liệu còn tồn tại nhiều tháng sau khi doanh nghiệp đã xác nhận với khách là đã xóa.

- **`BR-45.7` (Dữ liệu cá nhân và dữ liệu pháp nhân):** Khử định danh chỉ áp cho dữ liệu cá nhân của người liên hệ; liên kết giữa vé và doanh nghiệp khách hàng được giữ.

  **Lý do nghiệp vụ:** quy định bảo vệ dữ liệu cá nhân điều chỉnh dữ liệu cá nhân; báo cáo hợp đồng của khách hàng doanh nghiệp (`BR-43.1`) vẫn đủ số liệu sau khi một người liên hệ yêu cầu xóa.

- **`BR-45.8` (Nhật ký sau khử định danh):** Giá trị cá nhân trong nhật ký thay đổi thuộc phạm vi được thay bằng dấu hiệu đã ẩn danh, dòng nhật ký giữ nguyên (ai, lúc nào, thao tác gì); việc thay thế được ghi thành một dòng mới. Đây là ngoại lệ duy nhất của `NFR-12`, do Hệ thống thực hiện, không mở thao tác sửa nhật ký cho ai.

  **Lý do nghiệp vụ:** lịch sử thao tác không mất, chỉ nội dung cá nhân được gỡ.

- **`BR-45.9` (Thực thi theo mã khách hàng và trả xác nhận):** Phân hệ Vé hỗ trợ nhận yêu cầu xóa **theo mã khách hàng** từ quy trình quyền chủ thể dữ liệu, thực thi trên **toàn bộ vé của khách trong một lần**, và trả về kết quả cho hàng "Nội dung Vé hỗ trợ và tệp đính kèm" của Biên bản Hoàn tất Xử lý tại [`contacts-srs.md`](./contacts-srs.md) `BR-33.8`: trạng thái (Đã khử định danh / Đang tạm dừng theo yêu cầu pháp lý), số vé đã khử định danh, số tệp đã xóa, danh sách lượt xuất tại `BR-45.5`. Người khởi động không cần ô trên vé; thẩm quyền khởi động thuộc quy trình của phân hệ Khách hàng.

  **Lý do nghiệp vụ:** thỏa bốn vế của ADR-0008 Điều khoản 5; khách không được nhận câu trả lời "đã xóa xong" khi nội dung vé của họ còn nguyên.

- **`BR-45.10` (Tạm dừng xóa theo yêu cầu pháp lý):** Người có toàn quyền hoặc Người phụ trách Bảo vệ Dữ liệu đặt được tạm dừng xóa **theo từng vé** hoặc **theo khách hàng** (áp cho mọi vé hiện có và vé phát sinh sau của khách đó) khi đang trong diện tranh chấp, kèm căn cứ và ngày dự kiến gỡ. Trong thời gian tạm dừng, vé đó không bị khử định danh; kết quả trả về mang trạng thái "Đang tạm dừng theo yêu cầu pháp lý" kèm căn cứ, ngày dự kiến gỡ và số vé bị tạm dừng, nhất quán với [`omnichat-srs.md`](./omnichat-srs.md) `BR-23.7`, và yêu cầu xóa giữ ở trạng thái chưa hoàn tất cho phần đó. Gỡ tạm dừng thì phần còn lại được thực thi ngay và kết quả cập nhật vào biên bản.

  **Lý do nghiệp vụ:** nói "đã xóa" khi dữ liệu còn là sai sự thật; im lặng là vi phạm chính nguyên tắc của biên bản; đóng yêu cầu khi còn phần bị hoãn thì phần đó không bao giờ được xóa.

- **`BR-45.11` (Trường nhạy cảm của vé):** Phân hệ Vé hỗ trợ khai báo trường nhạy cảm của hệ thống theo khung [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-40`:

  | Trường nhạy cảm | Cách che | Ai xem đầy đủ |
  | --- | --- | --- |
  | Thư điện tử, số điện thoại của người yêu cầu đã liên kết hồ sơ khách hàng | Theo nhóm trường và mức hiển thị của hồ sơ khách hàng tại [`contacts-srs.md`](./contacts-srs.md) `FEAT-04`; vé không bao giờ hiển thị rộng hơn hồ sơ | Theo [`contacts-srs.md`](./contacts-srs.md) `BR-04.3`, `BR-04.4` |
  | Địa chỉ liên hệ của người gửi chưa liên kết hồ sơ (thư điện tử, số điện thoại từ kênh) | Mẫu che mặc định của khung: thư điện tử giữ ký tự đầu và tên miền; số điện thoại giữ 4 số cuối | Người có ô (Vé hỗ trợ, Sửa) bao phủ vé |
  | Đơn giá và thành tiền của giờ tính phí (`FEAT-33`) | Ẩn hoàn toàn | Người có quyền Duyệt giờ tính phí |
  | Trường tùy biến doanh nghiệp đánh dấu nhạy cảm | Theo phân quyền trường của [`object-manager-srs.md`](./object-manager-srs.md) | Theo phân quyền trường |

  Che áp ở mọi nơi trường xuất hiện: màn hình vé, danh sách, bảng điều khiển, thông báo, tệp xuất, báo cáo gửi định kỳ. Nội dung trao đổi và tệp đính kèm không che (cần để xử lý), được bảo vệ bằng quyền xem vé. Tác nhân AI luôn nhận giá trị đã che, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-40.1`. Lượt đọc do liên kết cơ hội (`BR-29.4`) không nới mức che nào.

  **Lý do nghiệp vụ:** người chỉ xem vé để nắm tình hình không cần số điện thoại của khách; người đang trả lời khách cần đủ địa chỉ để liên hệ. Vé là nơi thứ hai dữ liệu liên hệ xuất hiện ngoài hồ sơ khách hàng — nếu che lỏng hơn hồ sơ, vé thành đường vòng lấy dữ liệu.

- **`BR-45.12` (Dữ liệu nhạy cảm khách gõ vào và nội dung chép từ hội thoại):** Phát hiện và che dữ liệu nhạy cảm khách gõ trực tiếp vào nội dung (số thẻ thanh toán, mã định danh cá nhân, mã xác thực một lần) theo cùng danh mục và mẫu che của [`omnichat-srs.md`](./omnichat-srs.md) `BR-23.8`, `BR-23.9` áp cho vé đến từ hộp thư thư điện tử, biểu mẫu và tích hợp của phân hệ Vé hỗ trợ. Nội dung chép từ hội thoại sang vé (`BR-34.2`) dùng **dạng che hạn chế nhất** của [`omnichat-srs.md`](./omnichat-srs.md) `BR-23.9`, bất kể quyền của người tạo: số điện thoại, email, định danh kênh theo mẫu che mặc định; dữ liệu nhạy cảm khách gõ vào ở dạng ký hiệu che; bản gốc không bao giờ được chép và không mở lại được trên vé — nhất quán với [`omnichat-srs.md`](./omnichat-srs.md) `BR-21.12`. Địa chỉ trả lời người yêu cầu của vé lấy từ hồ sơ khách hàng hoặc định danh kênh gắn với hội thoại, không bao giờ lấy từ nội dung đã chép. Giá trị đã che không xuất hiện đầy đủ trên màn hình, tìm kiếm, tệp xuất, thông báo và không chuyển cho tác nhân AI.

  **Lý do nghiệp vụ:** khách gửi số thẻ qua thư cũng nguy hiểm như gửi qua chat; nếu vé là nơi duy nhất không che, sao chép hội thoại sang vé thành đường vòng lấy lại dữ liệu đã che.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-45.1.1` | `CFG-45-01` = 36 tháng | Vé đóng hoàn tất 37 tháng trước | Không có trong danh sách làm việc; tìm theo mã vẫn ra |
| `AC-45.2.1` | Khách có 12 vé | Thực thi yêu cầu xóa | Họ tên, liên hệ, toàn bộ nội dung trao đổi thay bằng dấu hiệu ẩn danh; tệp khách gửi bị xóa; thời gian xử lý, phân loại, kết quả tuân thủ giữ nguyên; dòng thời gian vẫn hiện nhân viên nào gửi lúc nào |
| `AC-45.2.2` | Báo cáo tuân thủ kỳ trước đã công bố | Mở lại sau khi khử định danh | Số liệu không đổi |
| `AC-45.3.1` | Khử định danh xong | Mở nhật ký | Có người yêu cầu, người khởi động, thời điểm, phạm vi vé |
| `AC-45.4.1` | Yêu cầu mang thời hạn 72 giờ, `CFG-45-03` = còn 24 giờ và còn 6 giờ | Chưa hoàn tất khi còn 24 giờ | Người phụ trách Bảo vệ Dữ liệu nhận cảnh báo; lại nhận khi còn 6 giờ |
| `AC-45.5.1` | Vé của khách từng có trong 2 lượt xuất | Hoàn tất | Kết quả trả về biên bản liệt kê 2 lượt xuất, người xuất, thời điểm |
| `AC-45.6.1` | 8 vé đã đóng, 2 đang mở, 1 trong Thùng rác, 1 trong lưu trữ dài hạn | Thực thi | Cả 12 vé được khử định danh |
| `AC-45.6.2` | Vé trong Thùng rác đã khử định danh | Phục hồi | Vé trở lại ở dạng đã khử định danh |
| `AC-45.7.1` | Người liên hệ thuộc doanh nghiệp X | Xuất báo cáo hợp đồng của X | Số vé của X không hụt |
| `AC-45.8.1` | Mở nhật ký vé đã khử định danh | Xem dòng ghi giá trị trước và sau | Dòng còn đủ ai, lúc nào, thao tác gì; giá trị cá nhân đã thay; có dòng mới ghi việc thay thế |
| `AC-45.9.1` | Phân hệ Khách hàng chuyển yêu cầu xóa của khách K | Hoàn tất | Hàng Vé hỗ trợ trong biên bản có trạng thái "Đã khử định danh", số vé và số tệp đã xử lý |
| `AC-45.10.1` | 2 trong 12 vé của K đang bị tạm dừng xóa theo từng vé | Thực thi | 10 vé được khử định danh; hàng Vé hỗ trợ mang trạng thái "Đang tạm dừng theo yêu cầu pháp lý" kèm căn cứ, ngày dự kiến gỡ, số vé bị tạm dừng; yêu cầu vẫn chưa hoàn tất |
| `AC-45.10.3` | Tạm dừng đặt theo khách hàng K | K có thêm vé mới, rồi yêu cầu xóa được thực thi | Mọi vé của K, kể cả vé mới, không bị khử định danh; kết quả trả về như `AC-45.10.1` với số vé là toàn bộ vé của K |
| `AC-45.10.2` | Tiếp nối, gỡ tạm dừng | — | 2 vé được khử định danh ngay; biên bản cập nhật; yêu cầu hoàn tất |
| `AC-45.11.1` | Người có (Vé, Xem) bao phủ vé nhưng (Vé, Sửa) = Không có; người gửi chưa liên kết hồ sơ | Mở vé | Thư điện tử người gửi hiển thị dạng "m***@vinafoods.vn" |
| `AC-45.11.2` | Người phụ trách vé | Mở vé | Thấy đầy đủ địa chỉ để trả lời |
| `AC-45.11.3` | Người không có quyền Duyệt giờ tính phí | Mở tab giờ hỗ trợ | Thấy số giờ; đơn giá và thành tiền bị ẩn |
| `AC-45.11.4` | Người dùng có quyền xem đầy đủ | Hỏi trợ lý AI số điện thoại người yêu cầu của vé | AI trả số đã che |
| `AC-45.12.1` | Khách gửi thư tới hộp thư của phân hệ Vé có số thẻ thanh toán | Tư vấn viên mở vé, tìm theo số thẻ | Nội dung hiển thị "[số thẻ đã che]"; tìm theo số thẻ không ra vé |
| `AC-45.12.3` | Người tạo vé có quyền xem đầy đủ số điện thoại và email của khách trong hội thoại | Tạo vé từ hội thoại | Nội dung chép sang vé chứa số điện thoại, email, định danh kênh theo mẫu che mặc định; không có lối hiển thị đầy đủ trên vé |
| `AC-45.12.4` | Vé tạo từ hội thoại Zalo có số điện thoại đã che trong nội dung chép | Tư vấn viên gửi phản hồi công khai | Phản hồi đi tới định danh Zalo gắn với hội thoại (hoặc liên hệ trong hồ sơ khách hàng), không dùng giá trị trong nội dung chép |
| `AC-45.12.2` | Hội thoại có số thẻ đã che | Tạo vé từ hội thoại | Nội dung sao chép sang vé vẫn ở dạng đã che; tệp xuất vé chứa giá trị đã che |
| `AC-45.11.5` | Người phụ trách cơ hội xem vé qua `BR-29.4`, hồ sơ khách cho họ mức Che một phần | Mở vé | Số điện thoại người yêu cầu ở dạng Che một phần |

---

## 4. Yêu cầu phi chức năng

### 4.1 Độ tin cậy & Toàn vẹn Dữ liệu

- **`NFR-01` (Bảo toàn dòng trao đổi):** Nội dung trao đổi và ghi chú bất biến; ngoại lệ duy nhất là khử định danh (`FEAT-45`), có ghi vết. *Cách nghiệm thu:* không có thao tác sửa, xóa trên giao diện lẫn qua lời gọi trực tiếp tới hệ thống; kiểm thử phải thử cả hai đường.
- **`NFR-02` (Toàn vẹn khi gộp vé):** Gộp và hoàn tác gộp thực hiện trọn vẹn hoặc không gì cả.
- **`NFR-03` (Không trùng mã số vé):** Mã số không bị cấp trùng khi nhiều thao tác tạo hoặc nhập vé đồng thời.
- **`NFR-04` (Không mất cam kết khi gián đoạn):** Sau khởi động lại hoặc gián đoạn, đồng hồ cam kết, cảnh báo, mốc leo thang, tự động đóng được khôi phục đúng trạng thái; vé đến hạn trong lúc gián đoạn được xử lý ngay khi hệ thống trở lại, không bỏ sót.

### 4.2 Hiệu năng & Khả năng đáp ứng

- **`NFR-05` (Sử dụng đồng thời):** Tối thiểu 100 tư vấn viên thao tác đồng thời trên một workspace mà không suy giảm trải nghiệm.
- **`NFR-06` (Tốc độ tải):** Danh sách vé và bảng điều khiển tải xong trong 3 giây với khối lượng tương đương 100.000 vé.
- **`NFR-07` (Độ trễ phát hiện):** Cảnh báo sớm và đánh dấu vi phạm phát sinh chậm nhất 1 phút sau thời điểm tới ngưỡng.
- **`NFR-08` (Lan truyền sự cố diện rộng):** Cập nhật từ vé cha lan truyền xong tới 500 vé con trong 5 phút.
- **`NFR-13` (Độ tươi của bảng điều khiển hàng đợi):** Một thay đổi trên vé (nhận việc, gán, chuyển hàng đợi, đổi trạng thái, vi phạm) phản ánh trên bảng điều khiển đang mở của người có quyền xem chậm nhất 5 giây, không cần tải lại trang.

### 4.3 An toàn & Bảo mật

- **`NFR-09` (Ghi chú nội bộ không bao giờ tới khách):** Ghi chú nội bộ và tệp trong ghi chú nội bộ bị loại ngay tại dữ liệu hệ thống trả cho mọi giao diện, kênh và thông báo dành cho khách hàng.
- **`NFR-10` (Đường dẫn tải có thời hạn):** Đường dẫn tải tệp xuất hết hiệu lực sau thời hạn (`CFG-25-02`) và chỉ mở được bởi người đã yêu cầu xuất.
- **`NFR-11` (Cách ly giữa các workspace):** Dữ liệu vé của mỗi workspace cách ly hoàn toàn; không thao tác nào cho người của workspace này tiếp cận vé của workspace khác.
- **`NFR-12` (Nhật ký bất biến):** Nhật ký thay đổi và nhật ký xuất không sửa, không xóa được bởi bất kỳ vai trò nào, kể cả Người có toàn quyền. Ngoại lệ duy nhất là thay nội dung cá nhân khi khử định danh (`BR-45.8`), do Hệ thống thực hiện.

---

## 5. Ma trận quyền truy cập tính năng

Mô hình quyền theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-24`, `FEAT-25`: mỗi vai trò mang một **ma trận ô** (loại dữ liệu × thao tác, mỗi ô một Mức truy cập) và các **quyền quản trị** dạng có/không. Phân hệ Vé hỗ trợ khai báo loại dữ liệu, thao tác đặc thù và quyền quản trị của mình tại 5.1, 5.2; mức mặc định của vai trò dựng sẵn ở đây là mức chi tiết cho loại dữ liệu Vé hỗ trợ, bổ sung cho bảng mức chung tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`. Doanh nghiệp điều chỉnh ô của vai trò dựng sẵn qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02` hoặc tạo vai trò riêng; mọi lượt cấp chịu trần năng lực của người cấp. Quyền hiệu lực trên một vé quyết định theo thứ tự bốn bước tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`: mức theo ô → nguồn nới phạm vi (thành viên đơn vị tiếp nhận của hàng đợi, lượt đọc do liên kết cơ hội `BR-29.4` (tài liệu này), công khai đọc nếu doanh nghiệp bật cho Vé hỗ trợ) → nguồn chặn (lượt chặn, chính sách Từ chối, vé khiếu nại `BR-39.3` (tài liệu này)) → che dữ liệu (`BR-45.11` (tài liệu này)).

*Quy ước mã trong Mục 5:* mã `BR`, `FEAT`, `CFG` không kèm tên tài liệu là mã **của tài liệu này** (`tickets-srs.md`); mã của phân hệ Phân quyền luôn đi kèm liên kết tới [`iam-tenant-authorization.md`](./iam-tenant-authorization.md). Các mã trùng số giữa hai tài liệu (ví dụ `BR-29.4`, `BR-39.3`, `BR-12.6`) được ghi rõ "(tài liệu này)".

### 5.1 Ô của loại dữ liệu và mức mặc định

**Loại dữ liệu Vé hỗ trợ** — thao tác chuẩn Xem, Tạo, Sửa, Xoá, Xuất, Nhập, Gán người phụ trách (gồm nhận việc, bàn giao, trả về hàng đợi của vé người khác, chuyển hàng đợi), và thao tác đặc thù: **Kết thúc vé** (chuyển sang "đã xử lý xong" và mở lại), **Ghi đè mức ưu tiên**, **Gộp vé**, **Hoàn tác gộp vé**, **Xoá vĩnh viễn**, **Xem nhật ký thay đổi**.

| Ô (Vé hỗ trợ, …) | Nhân viên Hỗ trợ | Quản lý | Nhân viên Kinh doanh | Chỉ xem bản ghi được giao | Kiểm toán | Quản lý Marketing, Marketing, Kiểm toán quyền | Người có toàn quyền |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Xem | Đơn vị của mình | Đơn vị và các đơn vị con | Đơn vị của mình | Chỉ của mình | Toàn workspace | Không có | Toàn workspace |
| Tạo | Có | Có | Có | Không có | Không có | Không có | Có |
| Sửa | Chỉ của mình | Đơn vị và các đơn vị con | Không có | Không có | Không có | Không có | Toàn workspace |
| Gán người phụ trách | Chỉ của mình | Đơn vị và các đơn vị con | Không có | Không có | Không có | Không có | Toàn workspace |
| Kết thúc vé | Chỉ của mình | Đơn vị và các đơn vị con | Không có | Không có | Không có | Không có | Toàn workspace |
| Ghi đè mức ưu tiên | Không có | Đơn vị và các đơn vị con | Không có | Không có | Không có | Không có | Toàn workspace |
| Xoá | Không có | Đơn vị của mình | Không có | Không có | Không có | Không có | Toàn workspace |
| Gộp vé | Không có | Đơn vị của mình | Không có | Không có | Không có | Không có | Toàn workspace |
| Hoàn tác gộp vé | Không có | Đơn vị của mình | Không có | Không có | Không có | Không có | Toàn workspace |
| Xoá vĩnh viễn | Không có | Không có | Không có | Không có | Không có | Không có | Toàn workspace |
| Xuất | Không có | Không có | Không có | Không có | Không có | Không có | Toàn workspace |
| Nhập | Không có | Không có | Không có | Không có | Không có | Không có | Toàn workspace |
| Xem nhật ký thay đổi | Chỉ của mình | Đơn vị và các đơn vị con | Không có | Không có | Toàn workspace | Không có | Toàn workspace |

**Loại dữ liệu Vé khiếu nại** (`BR-39.3` (tài liệu này)) — thao tác Xem, Tạo, Sửa, Gán người phụ trách.

| Ô (Vé khiếu nại, …) | Nhân viên Hỗ trợ | Quản lý | Kiểm toán | Các vai trò dựng sẵn khác | Người có toàn quyền |
| --- | --- | --- | --- | --- | --- |
| Xem | Không có | Đơn vị và các đơn vị con | Toàn workspace | Không có | Toàn workspace |
| Tạo | Không có | Có | Không có | Không có | Có |
| Sửa, Gán người phụ trách | Không có | Đơn vị và các đơn vị con | Không có | Không có | Toàn workspace |

Vé khiếu nại thuộc Đơn vị chính của Người phụ trách (người rà soát) theo quy tắc chung, không qua hàng đợi.

*Lệch khỏi mức mặc định chung:* ô (Vé hỗ trợ, Tạo) = Có của Nhân viên Kinh doanh là lệch có chủ đích khỏi mức "Chỉ xem đơn vị trên Vé hỗ trợ" tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`, khai báo theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-29.6`. **Lý do nghiệp vụ:** tạo vé hộ khách — khách báo sự cố với nhân viên kinh doanh đang chăm sóc mình, nhân viên đó ghi nhận ngay thay vì chuyển lời; vé vào hàng đợi chưa gán (`BR-34.2`), nhân viên kinh doanh không có quyền sửa hay xử lý vé.

*Cách đọc:* Nhân viên Hỗ trợ thấy mọi vé của đơn vị tiếp nhận mình thuộc, nhận việc và xử lý vé mình đang giữ. Một người giữ vai trò Quản lý đặt ở đơn vị của một đội quản lý vé của đội đó; đặt ở đơn vị cấp trên của nhiều đội (phòng Dịch vụ Khách hàng) quản lý vé của mọi đội trong phòng — cùng một vai trò, phạm vi do vị trí trong cây tổ chức quyết định. Mức Xem của Nhân viên Kinh doanh trên vé hầu như không phủ tới vé (vé thuộc đơn vị hỗ trợ); họ xem vé liên quan tới cơ hội mình phụ trách qua `BR-29.4` (tài liệu này).

### 5.2 Quyền quản trị của phân hệ

Mỗi quyền chỉ có hiệu lực trên phần dữ liệu mà các ô của chính người giữ quyền bao phủ, như nêu ở cột Giới hạn.

| Quyền quản trị | Nội dung | Giới hạn | Mặc định trong vai trò dựng sẵn |
| --- | --- | --- | --- |
| Cấu hình danh mục vé | Trạng thái vé, cây phân loại, nguyên nhân xử lý, mức ưu tiên, ma trận tác động × khẩn cấp, trường tùy biến của vé | Toàn workspace | Không vai trò nào |
| Cấu hình cam kết dịch vụ | Chính sách cam kết, lịch làm việc và ngày lễ, ngưỡng cảnh báo, chính sách leo thang, tự động đóng, khảo sát, kênh thông báo, điều kiện cảnh báo rủi ro sang cơ hội | Toàn workspace | Không vai trò nào |
| Cấu hình điều phối | Quy tắc phân bổ tự động, điều phối theo kỹ năng, danh mục kỹ năng và ánh xạ, danh mục lý do chuyển hàng đợi | Hàng đợi mà ô (Vé hỗ trợ, Gán) của người giữ bao phủ (`BR-10.6` (tài liệu này)) | Quản lý |
| Quản lý ca trực & kỹ năng | Lịch trực, người trực ngoài giờ, ghi nhận kỳ nghỉ, gán kỹ năng cho thành viên | Thành viên của đơn vị mà ô (Vé hỗ trợ, Gán) của người giữ bao phủ (`BR-11.1` (tài liệu này)) | Quản lý |
| Quản lý mẫu trả lời dùng chung | Tạo, sửa, ngừng dùng mẫu dùng chung | Toàn workspace | Quản lý |
| Duyệt bài viết tri thức | Duyệt bản nháp tri thức tạo từ vé | Bản nháp mà người giữ có mức Xem bao phủ vé nguồn; không tự duyệt bản của mình | Quản lý |
| Giám sát & hướng dẫn tư vấn viên | Mở phiên giám sát, gửi chỉ dẫn | Vé mà mức Xem của người giữ bao phủ (`BR-32.2` (tài liệu này)) | Quản lý |
| Duyệt giờ tính phí | Duyệt giờ tính phí; xem đầy đủ đơn giá và thành tiền | Vé mà mức Xem của người giữ bao phủ; không tự duyệt giờ của mình | Quản lý |
| Xem báo cáo hỗ trợ | Bảng điều khiển khối lượng từng người, báo cáo hiệu suất của người khác | Vé trong mức Xem của người giữ (`BR-42.2` (tài liệu này), `BR-43.3` (tài liệu này)) | Quản lý |

Đặt đơn vị tiếp nhận của nguồn, bật "gồm các đơn vị con", đặt danh sách hàng đợi được chuyển tới và tạm dừng xóa theo yêu cầu pháp lý không phải quyền quản trị có thể giao: chỉ Người có toàn quyền (và Người phụ trách Bảo vệ Dữ liệu với tạm dừng xóa) thực hiện, vì đó là nới phạm vi không xét được trên tập bản ghi.

### 5.3 Ma trận tính năng

Ký hiệu: **✅** = thực hiện được với mức mặc định tại 5.1, 5.2; **—** = không; *ô hoặc quyền* ghi ở cột cuối là điều kiện quyết định, áp như nhau cho mọi vai trò.

| Tính năng | Khách hàng | Nhân viên Hỗ trợ | Quản lý | Nhân viên Kinh doanh | Người có toàn quyền | Hệ thống | Ô / quyền quyết định |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `FEAT-01` Tạo & quản lý vé | Tạo qua kênh | ✅ (sửa vé của mình) | ✅ | Tạo hộ khách | ✅ | — | Tạo; Sửa; Cấu hình danh mục vé (trạng thái) |
| `FEAT-02` Mã số vé | — | — | — | — | — | ✅ | — |
| `FEAT-03` Liên kết khách hàng | — | ✅ (vé của mình) | ✅ | — | ✅ | ✅ (tự nhận diện) | Sửa |
| `FEAT-04` Phân loại | — | ✅ (chọn) | ✅ (chọn) | — | ✅ | — | Sửa; Cấu hình danh mục vé |
| `FEAT-05` Trường tùy biến | — | ✅ (nhập) | ✅ (nhập) | — | ✅ | — | Sửa; Cấu hình danh mục vé |
| `FEAT-06` Ma trận tác động | — | ✅ (đánh giá) | ✅ (ghi đè) | — | ✅ | ✅ (suy ra mức) | Sửa; Ghi đè mức ưu tiên; Cấu hình danh mục vé |
| `FEAT-07` Đồng hồ cam kết | — | — | — | — | ✅ (cấu hình) | ✅ | Cấu hình cam kết dịch vụ |
| `FEAT-08` Lịch làm việc | — | — | — | — | ✅ | — | Cấu hình cam kết dịch vụ |
| `FEAT-09` Tạm dừng đồng hồ | — | ✅ (đổi trạng thái vé của mình) | ✅ | — | ✅ | ✅ | Sửa |
| `FEAT-40` Cam kết theo hạng | — | Xem | Xem | — | ✅ | ✅ (chọn chính sách) | Cấu hình cam kết dịch vụ |
| `FEAT-34` Hàng đợi & nhận việc | — | ✅ (thấy, nhận, trả về vé của mình, chuyển vé của mình) | ✅ (chuyển, trả về) | — | ✅ (đơn vị tiếp nhận, danh sách chuyển tới) | ✅ (cảnh báo tồn đọng) | Xem; Gán; Người có toàn quyền cho cấu hình nguồn |
| `FEAT-10` Phân bổ xoay vòng | — | Được phân bổ | ✅ (cấu hình) | — | ✅ | ✅ | Cấu hình điều phối; người nhận: Sửa và Gán (`BR-10.3` (tài liệu này)) |
| `FEAT-11` Theo kỹ năng | — | Được phân bổ | ✅ (cấu hình, gán kỹ năng) | — | ✅ | ✅ | Cấu hình điều phối; Quản lý ca trực & kỹ năng |
| `FEAT-12` Bàn giao | — | Đề nghị chuyển vé của mình (`BR-12.7` (tài liệu này)) | ✅ (giao trực tiếp) | — | ✅ | — | Giao trực tiếp: Gán từ Đơn vị của mình trở lên (`BR-12.6` (tài liệu này)); đề nghị chuyển: Người phụ trách hiện tại, người nhận tự đạt điều kiện nhận việc khi chấp nhận |
| `FEAT-35` Ca trực | — | Tự đặt trạng thái | ✅ (xếp lịch) | — | ✅ | ✅ (loại người không trực) | Quản lý ca trực & kỹ năng |
| `FEAT-36` Chuyển hàng loạt | — | — | ✅ | — | ✅ | — | Gán (chuyển hàng loạt thủ công) hoặc Tạm ngưng người dùng, Gỡ người dùng, Sửa người dùng theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-24` (bước trong quy trình chung, `BR-36.9` (tài liệu này)) |
| `FEAT-13` Dòng trao đổi | Phần công khai | ✅ | ✅ | Đọc qua `BR-29.4` (tài liệu này) | ✅ | — | Xem; Sửa (gửi) |
| `FEAT-14` Ghi chú nội bộ | — | ✅ (vé của mình) | ✅ | — | ✅ | — | Sửa |
| `FEAT-15` Đính kèm | Gửi qua kênh | ✅ (vé của mình) | ✅ | Tải qua `BR-29.4` (tài liệu này) | ✅ | — | Sửa (gửi); Xem (tải) |
| `FEAT-16` Mẫu trả lời | — | Dùng, mẫu cá nhân | ✅ (mẫu dùng chung) | — | ✅ | — | Quản lý mẫu trả lời dùng chung |
| `FEAT-37` Thông báo | Nhận thông báo của vé | Nhận, tự cấu hình | Nhận, tự cấu hình | Nhận cảnh báo cơ hội | ✅ (kênh mặc định) | ✅ | Cấu hình cam kết dịch vụ (kênh mặc định, kênh đánh thức) |
| `FEAT-38` Cảnh báo trùng thao tác | — | Nhận | Nhận | — | Nhận | ✅ | Xem |
| `FEAT-17` Cảnh báo sớm | — | Nhận | Nhận | — | ✅ (ngưỡng) | ✅ | Cấu hình cam kết dịch vụ |
| `FEAT-18` Leo thang tự động | — | — | Nhận | — | ✅ (mốc) | ✅ | Cấu hình cam kết dịch vụ; người nhận vé: `BR-12.6` (tài liệu này) |
| `FEAT-39` Leo thang thủ công & khiếu nại | — | ✅ (vé của mình); không thấy vé khiếu nại về mình | ✅ (tiếp nhận, lập vé khiếu nại) | — | ✅ | ✅ (lượt chặn tự động) | Sửa; (Vé khiếu nại, Tạo / Xem) |
| `FEAT-19` Kết thúc vé | — | ✅ (vé của mình) | ✅ | — | ✅ | — | Kết thúc vé |
| `FEAT-20` Khảo sát | Chấm điểm | — | — | — | ✅ (cấu hình) | ✅ | Cấu hình cam kết dịch vụ |
| `FEAT-21` Mở lại vé | Phản hồi để mở lại | ✅ (vé của mình) | ✅ | — | ✅ | ✅ | Kết thúc vé |
| `FEAT-22` Tự động đóng | Nhận nhắc | — | — | — | ✅ (thời hạn) | ✅ | Cấu hình cam kết dịch vụ |
| `FEAT-23` Gắn nhãn hàng loạt | — | ✅ (vé của mình) | ✅ | — | ✅ | — | Sửa |
| `FEAT-24` Nhập | — | — | — | — | ✅ | — | Nhập |
| `FEAT-25` Xuất | — | — | — | — | ✅ | — | Xuất |
| `FEAT-26` Thùng rác | — | — | ✅ (xóa, phục hồi trong đơn vị) | — | ✅ (gồm xóa vĩnh viễn) | ✅ (xóa khi hết hạn) | Xoá; Xoá vĩnh viễn |
| `FEAT-27` Gộp vé | Nhận thông báo | — | ✅ (trong đơn vị) | — | ✅ | ✅ (thông báo) | Gộp vé; Hoàn tác gộp vé |
| `FEAT-41` Tách vé | Nhận thông báo | ✅ (vé của mình) | ✅ | — | ✅ | ✅ (thông báo) | Sửa; Tạo |
| `FEAT-28` Vé cha – vé con | Nhận cập nhật | ✅ (vé của mình) | ✅ | — | ✅ | ✅ (lan truyền) | Sửa; Kết thúc vé |
| `FEAT-29` Liên kết cơ hội | — | ✅ (vé của mình) | ✅ | ✅ (từ cơ hội của mình) | ✅ | ✅ (lượt đọc) | Sửa (vé hoặc cơ hội); `CFG-29-01` |
| `FEAT-30` Cảnh báo rủi ro | — | — | — | Nhận | ✅ (điều kiện) | ✅ | Cấu hình cam kết dịch vụ |
| `FEAT-31` Bản nháp tri thức | — | ✅ (đề xuất) | ✅ (duyệt) | — | ✅ | — | Xem; Duyệt bài viết tri thức |
| `FEAT-32` Giám sát hướng dẫn | — | Được hướng dẫn | ✅ | — | ✅ | — | Giám sát & hướng dẫn tư vấn viên; `CFG-32-01` |
| `FEAT-33` Giờ tính phí | — | ✅ (ghi giờ vé của mình) | ✅ (duyệt) | — | ✅ | — | Sửa; Duyệt giờ tính phí |
| `FEAT-42` Bảng điều khiển | — | — | ✅ | — | ✅ | — | Xem; Xem báo cáo hỗ trợ |
| `FEAT-43` Báo cáo | — | Chỉ số của chính mình | ✅ | — | ✅ | ✅ (gửi định kỳ) | Xem; Xem báo cáo hỗ trợ; Xuất (gửi ra ngoài) |
| `FEAT-44` Nhật ký | — | Nhật ký vé của mình | ✅ (vé trong phạm vi) | — | ✅ (toàn kho theo sàn [`contacts-srs.md`](./contacts-srs.md) `NFR-14`) | ✅ (ghi) | Xem nhật ký thay đổi |
| `FEAT-45` Vòng đời dữ liệu & xóa theo yêu cầu | — | — | — | — | ✅ (theo dõi, tạm dừng xóa) | ✅ (thực thi) | Quy trình quyền chủ thể dữ liệu của [`contacts-srs.md`](./contacts-srs.md) `FEAT-33`; Người phụ trách Bảo vệ Dữ liệu |

### 5.4 Sàn bắt buộc và hằng số của phân hệ

Áp như nhau cho mọi vai trò, không cấu hình vượt được qua vai trò, điều chỉnh ô hay lượt cấp nào ([`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.3`):

- Ghi chú nội bộ và tệp trong ghi chú nội bộ không bao giờ tới khách hàng (`NFR-09`).
- Tư vấn viên bị khiếu nại không xem được vé khiếu nại về mình (`BR-39.3` (tài liệu này)), trừ khi người đó là Người có toàn quyền; khi đó vé khiếu nại được ghi chú để Chủ sở hữu biết.
- Quyền đọc nhật ký toàn kho theo [`contacts-srs.md`](./contacts-srs.md) `NFR-14`.
- Nhật ký thay đổi và nhật ký xuất bất biến (`NFR-12`).

**Hằng số hệ thống (không phải Sàn bắt buộc):** tác nhân AI luôn nhận giá trị đã che của trường nhạy cảm của vé (`BR-45.11` (tài liệu này)), theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-40.1`.

Thao tác thu hẹp quyền liên quan tới vé (bớt hàng đợi khỏi danh sách chuyển tới, tắt "gồm các đơn vị con", thu hẹp ô, tạm ngưng, gỡ thành viên) không bị chặn khi nhật ký thay đổi cấu hình quyền gặp sự cố, nhật ký được ghi bù — theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.6`; thao tác nới rộng thì bị hủy khi không ghi được nhật ký, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.5`.

---

## 6. Kịch bản chấp nhận tổng hợp (UAT)

Các kịch bản dùng cách gọi theo vị trí (Mục 1.4, 2.2): "Tư vấn viên" giữ vai trò Nhân viên Hỗ trợ; "Trưởng nhóm" và "Trưởng phòng" giữ vai trò Quản lý, đặt ở đơn vị đội và đơn vị phòng; mọi ô ở mức mặc định tại Mục 5.1 trừ khi kịch bản nêu khác.

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
2. Trưởng nhóm (ô Gộp vé mặc định = Đơn vị của mình) chọn vé đầu tiên làm vé chính, gộp 2 vé còn lại vào. Trước đó, kiểm tra một tư vấn viên (ô Gộp vé = Không có) thao tác gộp: thao tác phải bị vô hiệu (BR-27.7).
3. **Kỳ vọng:** Toàn bộ nội dung của 2 vé phụ được nối tiếp vào dòng thời gian của vé chính; 2 vé phụ bị khóa lại và không thể chỉnh sửa thêm; khách hàng của 2 vé phụ nhận được thông báo yêu cầu của họ đã chuyển sang vé chính kèm mã số để tiếp tục theo dõi (BR-27.3).

---

### Kịch bản 6: Quản lý Sự cố Diện rộng (Major Incident)
1. Sự cố sập hệ thống thanh toán khiến 150 khách hàng cùng tạo vé báo lỗi trong vòng 10 phút.
2. Trưởng nhóm tạo một Vé Sự cố làm vé cha, gán 150 vé của khách hàng làm vé con.
3. Trưởng nhóm đăng một cập nhật công khai lên vé cha: "Đội kỹ thuật đã xác định nguyên nhân, dự kiến khắc phục trong 30 phút."
4. **Kỳ vọng (BR-28.2):** Cập nhật này tự động xuất hiện trên dòng trao đổi của toàn bộ 150 vé con, khách hàng của từng vé đều nhận được mà tư vấn viên không phải trả lời thủ công từng vé.
5. Sự cố được khắc phục, Trưởng nhóm chuyển vé cha sang trạng thái "đã xử lý xong".
6. **Kỳ vọng (BR-28.3, BR-28.5):** Hệ thống hiển thị danh sách 150 vé con đang mở để xác nhận; sau khi xác nhận, các vé được chọn chuyển sang "đã xử lý xong" với nguyên nhân và tóm tắt lấy từ vé cha, mỗi khách hàng nhận khảo sát riêng của vé mình.

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
2. A (ô Gán = Chỉ của mình) không có thao tác giao trực tiếp; A gửi đề nghị chuyển vé cho tư vấn viên B kèm ghi chú "Cần chuyên môn về tích hợp thanh toán".
3. **Kỳ vọng (BR-12.6, BR-12.7):** Trong lúc B chưa chấp nhận, vé vẫn thuộc A và đồng hồ vẫn chạy; B nhận được đề nghị kèm ghi chú.
4. B chấp nhận.
5. **Kỳ vọng (BR-12.1, BR-12.2, BR-12.3, BR-12.7):** Vé thuộc B; ghi chú bàn giao xuất hiện dưới dạng ghi chú nội bộ; hạn chót xử lý dứt điểm vẫn là 4 giờ tới; mốc phản hồi đầu tiên vẫn đã hoàn tất.
6. Sau đó Trưởng nhóm (ô Gán = Đơn vị và các đơn vị con) giao trực tiếp vé cho tư vấn viên E; E từ chối trong vòng 30 phút với lý do "Đang xử lý sự cố khẩn cấp khác".
7. **Kỳ vọng (BR-12.4):** Vé quay về hàng đợi của đơn vị tiếp nhận và Trưởng nhóm nhận được thông báo để điều phối lại.

---

### Kịch bản 10: Vé Tồn đọng trong Hàng đợi Chung
1. Một vé mới vào hàng đợi chung của nhóm Hỗ trợ Kỹ thuật lúc 09:00, chưa ai nhận.
2. Đến 09:15 (ngưỡng cảnh báo mặc định) vẫn chưa có ai nhận vé.
3. **Kỳ vọng (BR-34.6):** Trưởng nhóm nhận cảnh báo vé tồn đọng, dù vé chưa vi phạm cam kết phản hồi.
4. Tư vấn viên C nhận vé từ hàng đợi.
5. **Kỳ vọng (BR-34.4, BR-34.7, BR-34.1):** Vé biến mất khỏi danh sách chưa gán của những người khác nhưng vẫn thuộc đơn vị tiếp nhận; thời gian 15 phút nằm chờ vẫn được tính vào đồng hồ cam kết phản hồi đầu tiên.

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
3. **Kỳ vọng (BR-36.1, BR-36.2, BR-36.3, BR-36.6):** Toàn bộ 10 vé được chuyển trong một thao tác; hệ thống đề xuất chia theo hạn mức còn trống của B và C; ghi chú bàn giao xuất hiện trên từng vé; hạn chót cam kết của cả 10 vé giữ nguyên không bị tính lại.

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
4. Trưởng phòng đặt lịch gửi tự động báo cáo này tới chính mình vào ngày 1 hàng tháng; thử thêm địa chỉ thư của khách hàng X làm người nhận khi ô (Vé hỗ trợ, Xuất) của mình = Không có thì không thêm được (BR-43.8).
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
1. Một khách hàng cá nhân gửi yêu cầu xóa dữ liệu cá nhân của họ theo pháp luật bảo vệ dữ liệu cá nhân áp dụng cho doanh nghiệp.
2. Yêu cầu được tiếp nhận và xác minh theo quy trình của phân hệ Khách hàng, rồi chuyển sang phân hệ Vé hỗ trợ theo mã khách hàng (BR-45.9); Người phụ trách Bảo vệ Dữ liệu theo dõi.
3. **Kỳ vọng (BR-45.2):** Họ tên, số điện thoại, email, nội dung trao đổi chứa thông tin cá nhân và tệp đính kèm được thay bằng dấu hiệu đã ẩn danh; các số liệu thống kê phi định danh của những vé đó (thời gian xử lý, danh mục, kết quả tuân thủ cam kết) vẫn được giữ nguyên.
4. Trưởng phòng mở lại báo cáo tuân thủ cam kết của kỳ trước đó.
4b. **Kỳ vọng (BR-45.9):** Hàng "Nội dung Vé hỗ trợ và tệp đính kèm" của Biên bản Hoàn tất Xử lý có trạng thái "Đã khử định danh" kèm số vé và số tệp đã xử lý.
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
6. Quản trị viên lọc danh sách vé theo giá trị của trường tùy biến này và xuất ra tệp.
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
1. Quản trị viên (ô Nhập mặc định của Quản lý = Không có) nhập tệp dữ liệu 5.000 vé từ hệ thống cũ, trong đó 30 dòng có lỗi định dạng ngày tháng.
2. **Kỳ vọng (BR-24.3):** 4.970 dòng hợp lệ được nhập thành công; 30 dòng lỗi được liệt kê trong báo cáo kết quả kèm lý do từng dòng; hệ thống không hủy toàn bộ lần nhập.
3. Quản trị viên sửa 30 dòng lỗi và nhập lại chính tệp đó.
4. **Kỳ vọng (BR-24.4):** Hệ thống bỏ qua 4.970 bản ghi đã nhập lần trước, chỉ nhập thêm 30 bản ghi đã sửa; không phát sinh dữ liệu trùng.
5. Trưởng phòng xem báo cáo tuân thủ cam kết của kỳ hiện tại.
6. **Kỳ vọng (BR-24.5, BR-43.2):** Toàn bộ vé nhập khẩu bị loại khỏi mẫu số tính tỷ lệ tuân thủ.

---

### Kịch bản 34: Xuất Dữ liệu có Kiểm soát
1. Doanh nghiệp điều chỉnh ô (Vé hỗ trợ, Xuất) của vai trò Quản lý thành Đơn vị và các đơn vị con. Trưởng nhóm xuất danh sách vé của đội mình.
2. **Kỳ vọng (BR-25.2, BR-25.5):** Tệp xuất chỉ chứa dữ liệu trong phạm vi quyền của người đó, không bao gồm vé của nhóm khác.
2b. Cũng Trưởng nhóm đó mở danh sách vé toàn workspace (công khai đọc đang bật cho Vé hỗ trợ) và bấm xuất.
2c. **Kỳ vọng (BR-25.2, BR-25.5):** Tệp chỉ gồm vé thuộc đơn vị và các đơn vị con của Trưởng nhóm; không có lựa chọn xuất toàn workspace, vì đó là mức Toàn workspace của cùng ô Xuất mà Trưởng nhóm không có.
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
3. Trưởng phòng (có quyền Duyệt giờ tính phí) xuất tệp bàn giao giờ tính phí của khách hàng này cho khâu xuất hóa đơn.
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
2. Yêu cầu được chuyển từ phân hệ Khách hàng sang theo mã khách hàng; hệ thống thực thi khử định danh (BR-45.9).
3. **Kỳ vọng (BR-45.6):** Cả 12 vé đều được ẩn danh, bao gồm vé trong Thùng rác và vé lưu trữ dài hạn; tệp đính kèm của các vé đó cũng được xử lý theo BR-15.4.
4. **Kỳ vọng (BR-45.2):** Toàn bộ nội dung trao đổi do người viết nhập được thay thế trọn vẹn, không phải chỉ dò xóa một phần câu chữ; siêu dữ liệu của từng tin nhắn được giữ lại nên dòng thời gian xử lý vẫn đọc được.
5. Quản trị viên phục hồi vé trong Thùng rác.
6. **Kỳ vọng (BR-26.2, BR-45.6):** Vé trở lại ở **dạng đã ẩn danh**; dữ liệu cá nhân không được khôi phục.
7. Mở nhật ký thay đổi của một vé đã ẩn danh, xem các dòng ghi giá trị trước và sau.
8. **Kỳ vọng (BR-45.8, NFR-12):** Dòng nhật ký vẫn còn đủ (ai làm, lúc nào, thao tác gì) nhưng nội dung cá nhân trong đó đã được thay bằng dấu hiệu ẩn danh; việc thay thế này cũng được ghi thành một dòng nhật ký mới.
9. Xuất báo cáo hợp đồng của doanh nghiệp khách hàng X.
10. **Kỳ vọng (BR-45.7, BR-43.1):** Số vé của doanh nghiệp X trong báo cáo **không bị hụt**, vì liên kết giữa vé và pháp nhân khách hàng được giữ nguyên — chỉ dữ liệu cá nhân của người liên hệ bị gỡ.
11. **Kỳ vọng (BR-45.4, KPI-13):** Toàn bộ quá trình hoàn tất trong thời hạn của yêu cầu (ví dụ 72 giờ theo `CFG-45-02`); Người phụ trách Bảo vệ Dữ liệu được cảnh báo ở mốc còn 24 giờ và còn 6 giờ (`CFG-45-03`).

---

### Kịch bản 45: Lịch Trực Không Phủ Hết Lịch Cam kết
1. Doanh nghiệp gán cho một khách hàng cao cấp chính sách cam kết dùng lịch phục vụ liên tục cả ngày đêm, trong khi lịch trực của đội chỉ xếp người từ 08:00 tới 18:00.
2. **Kỳ vọng (BR-35.5):** Hệ thống cảnh báo người có quyền Quản lý ca trực & kỹ năng và người có quyền Cấu hình cam kết dịch vụ rằng có khung giờ nằm trong cam kết nhưng không ai trực, nêu rõ khung giờ nào.
3. Người có quyền Quản lý ca trực & kỹ năng chỉ định người trực ngoài giờ (đạt `BR-10.3`) cho khung 18:00–08:00.
4. Một vé của khách hàng này phát sinh lúc 02:00 sáng.
5. **Kỳ vọng (BR-35.6, BR-37.4):** Cảnh báo được gửi tới người trực ngoài giờ qua kênh có khả năng đánh thức (tin nhắn hoặc cuộc gọi tự động), không chỉ thông báo trong ứng dụng.
6. **Kỳ vọng (BR-08.7):** Nếu hôm đó là ngày nghỉ lễ, cam kết vẫn được tính bình thường theo cấu hình mặc định của lịch phục vụ liên tục.
7. Giả định kênh gửi tin nhắn gặp sự cố và lần gửi đầu thất bại.
8. **Kỳ vọng (BR-37.5):** Hệ thống thử lại theo số lần đã cấu hình; nếu vẫn thất bại thì ghi nhận trên vé và báo Trưởng nhóm, không để cảnh báo âm thầm biến mất.

---

### Kịch bản 46: Gộp Vé và Hoàn tác là Hai Ô Riêng
1. Doanh nghiệp tạo vai trò "Trưởng nhóm thực tập" bằng sao chép vai trò Quản lý, đặt (Vé hỗ trợ, Gộp vé) = Đơn vị của mình và (Vé hỗ trợ, Hoàn tác gộp vé) = Không có; gán cho Trưởng nhóm T.
2. T gộp hai vé trùng lặp của cùng một khách hàng trong đội mình.
3. **Kỳ vọng (BR-27.7, BR-44.1):** Gộp thành công; nhật ký ghi lượt gộp.
4. T thử hoàn tác trong thời hạn 24 giờ.
5. **Kỳ vọng (BR-27.7):** Thao tác hoàn tác bị vô hiệu kèm giải thích ô Hoàn tác gộp vé = Không có.
6. Trưởng phòng (vai trò Quản lý, ô Hoàn tác gộp vé bao phủ hai vé) thực hiện hoàn tác.
7. **Kỳ vọng (BR-27.5):** Vé phụ được khôi phục về trạng thái trước khi gộp; nhật ký giữ cả lượt gộp và lượt hoàn tác.
8. Một tư vấn viên (vai trò Nhân viên Hỗ trợ) chọn hai vé để gộp.
9. **Kỳ vọng (BR-27.7):** Thao tác gộp bị vô hiệu, vì ô Gộp vé mặc định của Nhân viên Hỗ trợ = Không có.

---

### Kịch bản 47: Vé Đến Nhầm Đội & Chuyển Hàng đợi
1. Hộp thư hỗ trợ@ có đơn vị tiếp nhận "CSKH trung tâm"; danh sách được chuyển tới của "CSKH trung tâm" gồm "Hỗ trợ – Đà Nẵng" và "Kỹ thuật", không gồm "Kế toán".
2. Khách ở Đà Nẵng gửi thư; vé mới xuất hiện chưa gán trong hàng đợi "CSKH trung tâm".
3. **Kỳ vọng (BR-34.2, BR-34.4):** Thành viên "CSKH trung tâm" thấy vé và nhận việc được; thành viên "Hỗ trợ – Đà Nẵng" chưa thấy vé.
4. Tư vấn viên A của "CSKH trung tâm" (ô Gán = Chỉ của mình) nhận việc, rồi chuyển vé sang "Hỗ trợ – Đà Nẵng" với lý do "Sai chi nhánh" — được phép vì chuyển hàng đợi là ngoại lệ theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.14`.
5. **Kỳ vọng (BR-34.8):** Màn hình xác nhận báo A sẽ không còn thấy vé; sau khi chuyển, vé thuộc "Hỗ trợ – Đà Nẵng", chưa gán (A không thuộc đơn vị đó); hạn chót không đổi; Trưởng nhóm Đà Nẵng nhận thông báo kèm lý do; "Kế toán" không có trong danh sách chuyển tới.
6. Phân bổ tự động của "Hỗ trợ – Đà Nẵng" đang bật; trong đội có B đang sẵn sàng nhưng ô (Vé hỗ trợ, Sửa) = Không có, và C đang sẵn sàng với mức mặc định.
7. **Kỳ vọng (BR-10.3):** Vé được phân bổ cho C, không cho B; nhật ký ghi lượt gán do Hệ thống kèm tên quy tắc.
8. C kiêm nhiệm "Kỹ thuật" mức Chỉ xem và nhận thêm một vé chưa gán của "Kỹ thuật".
9. **Kỳ vọng (BR-34.1, BR-34.4):** C nhận được; vé vẫn thuộc "Kỹ thuật", Trưởng nhóm "Kỹ thuật" vẫn thấy, đội Đà Nẵng không thấy vé đó.

---

### Kịch bản 48: Tư vấn viên Bị Tạm ngưng khi Đang Giữ Vé
1. Tư vấn viên D đang giữ 12 vé chưa đóng; nhật ký thay đổi cấu hình quyền đang gặp sự cố.
2. Người có quyền Tạm ngưng người dùng tạm ngưng D.
3. **Kỳ vọng (BR-36.4):** D bị đăng xuất ngay; tạm ngưng không bị chặn; nhật ký được ghi bù khi phục hồi.
4. Người thực hiện chọn "giữ nguyên" cho 12 vé.
5. **Kỳ vọng (BR-36.4, BR-42.1, BR-37.1):** 12 vé mang dấu "Người phụ trách đang tạm ngưng" trên bảng điều khiển; Trưởng nhóm nhận thông báo; khách phản hồi trên các vé đó thì thông báo tới Trưởng nhóm thay vì D; phân bổ tự động không chọn D.
6. Trưởng nhóm chọn 12 vé trên bảng điều khiển và trả về hàng đợi.
7. **Kỳ vọng (BR-34.9):** 12 vé chưa gán trong hàng đợi của đơn vị tiếp nhận mà mỗi vé đang thuộc; hạn chót không đổi.
8. D được kích hoạt lại.
9. **Kỳ vọng (BR-36.4):** Không có đề xuất trả về D các vé đã về hàng đợi.

---

### Kịch bản 49: Tạm dừng Xóa theo Yêu cầu Pháp lý
1. Khách K có 12 vé; 2 vé thuộc một vụ tranh chấp, Người phụ trách Bảo vệ Dữ liệu đặt tạm dừng xóa trên 2 vé đó kèm căn cứ.
2. K gửi yêu cầu xóa dữ liệu cá nhân; yêu cầu được chuyển sang phân hệ Vé hỗ trợ.
3. **Kỳ vọng (BR-45.9, BR-45.10):** 10 vé được khử định danh; hàng Vé hỗ trợ trong Biên bản Hoàn tất Xử lý mang trạng thái "Đang tạm dừng theo yêu cầu pháp lý" kèm căn cứ; yêu cầu xóa chưa hoàn tất.
4. Tạm dừng được gỡ.
5. **Kỳ vọng (BR-45.10):** 2 vé còn lại được khử định danh ngay; biên bản cập nhật trạng thái "Đã khử định danh"; yêu cầu hoàn tất.

---


## 7. Nhu cầu nghiệp vụ chưa chốt được phương án

Mục này chỉ gồm những nhu cầu có thật nhưng chưa quyết được hướng nghiệp vụ đúng. Mọi điểm đã chốt phương án được đặc tả tại Mục 3.

1. **Đổi hạng khách hàng khi vé đang mở.** `BR-40.2` chốt rằng sửa *chính sách* không ảnh hưởng vé đang mở. Chưa chốt: khi chính *khách hàng* được nâng hoặc hạ hạng trong lúc có vé đang mở, vé đó giữ cam kết cũ (nhất quán với `BR-40.2`) hay nhận cam kết mới (khách đã trả tiền cho gói cao hơn và kỳ vọng được phục vụ ngay). Cần chủ sản phẩm quyết cùng đội kinh doanh.
2. **Bồi hoàn khi vi phạm cam kết.** Tự động tính khoản bù trừ khi mức vi phạm vượt ngưỡng hợp đồng. Chưa chốt: phân hệ Vé hỗ trợ chỉ cung cấp số liệu vi phạm hay tự đề xuất khoản bồi hoàn; ranh giới với phân hệ Hợp đồng & Thanh toán.
3. **Trả lời và gợi ý giải pháp tự động.** Tự phân loại vé, gợi ý câu trả lời từ cơ sở tri thức. Chưa chốt: gợi ý chỉ hiển thị cho tư vấn viên hay được gửi thẳng cho khách; trách nhiệm khi câu trả lời tự động sai; cách tính mốc Phản hồi Đầu tiên với câu trả lời tự động.
4. **Trường tùy biến theo nhánh phân loại.** Chọn phân loại "Lỗi thanh toán" thì hiện thêm trường riêng. Chưa chốt: trường phụ thuộc phân loại có bắt buộc khi kết thúc như `BR-05.1` không, và xử lý ra sao khi vé đổi phân loại sau khi đã nhập.
5. **Cập nhật trạng thái hàng loạt.** Chưa chốt: cho đổi trạng thái hàng loạt tới mức nào — có gồm chuyển sang trạng thái kết thúc không, và khi đó các ràng buộc `BR-05.1`, `BR-19.1`, `BR-19.2` áp từng vé hay chặn cả lô.

---

## Phụ lục A: Danh mục Khái niệm Nghiệp vụ

> Phụ lục này mô tả **khái niệm nghiệp vụ** và thông tin chúng nắm giữ, không phải thiết kế dữ liệu, và không mang tính ràng buộc kỹ thuật. Cách tổ chức lưu trữ thuộc thẩm quyền đội phát triển. Cột "Bắt buộc" cho biết thông tin đó phải có giá trị hay không, ở thời điểm nào.

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
| Hàng đợi / Đơn vị tiếp nhận | Tham chiếu tới hàng đợi vé và đơn vị tổ chức tiếp nhận | Có | Vé thuộc đơn vị này suốt vòng đời, chỉ đổi khi chuyển hàng đợi (`BR-34.1`) |
| Nguồn tạo vé | Hộp thư, kênh, biểu mẫu, tích hợp, tạo thủ công, nhập tệp, tách vé, tạo lại | Có | Xác định đơn vị tiếp nhận ban đầu (`BR-34.2`) |
| Người phụ trách | Tham chiếu tới thành viên | Không | Trống khi vé chưa gán trong hàng đợi (`FEAT-34`) |
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
| Cờ có vấn đề riêng ngoài sự cố chung | Có/Không | Có, mặc định Không | Loại trừ khỏi kết thúc đồng loạt (BR-28.4) |
| Tạm dừng xóa theo yêu cầu pháp lý | Căn cứ, người đặt, ngày dự kiến gỡ | Không | Hoãn khử định danh (BR-45.10) |
| Điểm hài lòng & nhận xét | Số 1-5 và văn bản | Không | Kết quả khảo sát (FEAT-20) |

### A.2 Các thực thể liên quan

| Thực thể | Thông tin chính | Ý nghĩa nghiệp vụ |
| --- | --- | --- |
| **Trao đổi trên Vé** | Loại nội dung (phản hồi công khai / ghi chú nội bộ / thông báo hệ thống), người gửi, nội dung, danh sách tệp đính kèm, thời điểm | Từng lượt trao đổi hoặc ghi chú, bất biến sau khi tạo (NFR-01) |
| **Tệp đính kèm** | Tên tệp, định dạng, dung lượng, người tải lên, thời điểm, thuộc lượt trao đổi nào | Minh chứng sự cố, tuân theo BR-15.1 đến BR-15.4 |
| **Chính sách SLA** | Tên, phạm vi áp dụng (theo mức ưu tiên / theo hạng khách hàng / theo hợp đồng cụ thể), thời hạn phản hồi đầu tiên, thời hạn xử lý dứt điểm, lịch làm việc áp dụng | Cam kết chất lượng dịch vụ (FEAT-07, FEAT-40) |
| **Lịch Làm việc** | Tên, múi giờ, khung giờ làm việc theo từng ngày trong tuần, danh sách ngày nghỉ lễ | Cơ sở tính hạn chót cam kết (FEAT-08) |
| **Chính sách Leo thang** | Loại mốc áp dụng (sắp tới hạn / đã vi phạm), thời điểm kích hoạt, danh sách hành động thực thi | Phản ứng khi vé sắp hoặc đã vi phạm (FEAT-18) |
| **Hàng đợi vé** | Đơn vị tiếp nhận, gồm các đơn vị con hay không, danh sách hàng đợi được chuyển tới, quy tắc phân bổ | Nơi vé chờ người nhận; thành viên đơn vị tiếp nhận thấy và nhận việc (`FEAT-34`) |
| **Lượt chuyển hàng đợi** | Vé, hàng đợi cũ, hàng đợi mới, lý do, người chuyển, thời điểm | Truy vết vé đi qua các đội (`BR-34.8`) |
| **Lượt đọc do liên kết cơ hội** | Vé, cơ hội, người được cấp, mức trần Chỉ đọc, điều kiện thu hồi | Cho người phụ trách cơ hội xem vé liên quan (`BR-29.4`) |
| **Kỹ năng & Năng lực Tư vấn viên** | Danh sách kỹ năng của từng tư vấn viên, hạn mức số vé tối đa | Cơ sở điều phối theo kỹ năng và hạn mức (FEAT-10, FEAT-11) |
| **Ca trực & Trạng thái Sẵn sàng** | Lịch trực theo người và khung giờ, trạng thái sẵn sàng hiện tại, kỳ nghỉ phép đã duyệt | Xác định ai đủ điều kiện nhận vé (FEAT-35) |
| **Khảo sát Hài lòng** | Điểm 1-5, nhận xét, thời điểm gửi, thời điểm khách hàng phản hồi, tình trạng hết hạn | Đo lường chất lượng phục vụ (FEAT-20) |
| **Câu trả lời Mẫu** | Tiêu đề, nội dung, phạm vi dùng chung hay cá nhân, người tạo | Chuẩn hóa và tăng tốc phản hồi (FEAT-16) |
| **Nhật ký Thay đổi** | Vé liên quan, trường bị thay đổi, giá trị trước, giá trị sau, người thực hiện, thời điểm | Truy vết và đối soát tranh chấp (FEAT-44) |
| **Nhật ký Xuất dữ liệu** | Người xuất, thời điểm, phạm vi dữ liệu, số bản ghi | Kiểm soát rủi ro rò rỉ dữ liệu (BR-44.4) |
| **Phiên Nhập & Xuất Dữ liệu** | Người thực hiện, thời điểm, tình trạng, số dòng thành công, danh sách dòng lỗi kèm lý do | Theo dõi tiến độ chuyển đổi dữ liệu (FEAT-24, FEAT-25) |
| **Yêu cầu Xóa Dữ liệu Cá nhân (phần thuộc vé)** | Mã khách hàng, thời điểm nhận từ phân hệ Khách hàng, hạn đáp ứng, tình trạng (gồm Đang tạm dừng theo yêu cầu pháp lý), phạm vi vé, số tệp đã xóa, lượt xuất liên quan | Bằng chứng tuân thủ và kết quả trả về Biên bản Hoàn tất Xử lý (`FEAT-45`) |
| **Vé Khiếu nại** | Vé gốc liên quan, tư vấn viên bị khiếu nại, nội dung khiếu nại, người rà soát (Người phụ trách), kết luận, thời điểm | Loại dữ liệu riêng về quyền; tách khỏi vé gốc và tự chặn tư vấn viên bị khiếu nại (BR-39.3) |
| **Danh mục cấu hình** — Trạng thái Vé, Loại Vé, Nguồn Tiếp nhận, Nguyên nhân Xử lý, Danh mục Phân loại, Mức ưu tiên, Lý do chuyển hàng đợi | Tên hiển thị, thứ tự, màu sắc, tình trạng còn sử dụng, các dấu hiệu nghiệp vụ đi kèm (ví dụ trạng thái nào là kết thúc, trạng thái nào tạm dừng cam kết) | Cho phép mỗi doanh nghiệp tự chuẩn hóa theo quy trình riêng |


---

## Phụ lục B: Tham số Cấu hình theo Workspace

Mọi tham số dưới đây do từng doanh nghiệp đặt; giá trị mặc định là điểm khởi đầu khuyến nghị. Thay đổi tham số ghi nhật ký hoạt động của workspace; riêng `CFG-29-01` và `CFG-34-02` làm thay đổi ai thấy vé nên thuộc nhật ký thay đổi cấu hình quyền theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-41.4` (nới rộng đóng khi lỗi, thu hẹp ghi bù). Tham số áp cho vé đang mở theo nguyên tắc của quy tắc tương ứng (ví dụ `BR-01.5`, `BR-08.5`, `BR-40.2`).

| Mã | Quy tắc | Nội dung | Mặc định | Miền giá trị | Thẩm quyền |
| --- | --- | --- | --- | --- | --- |
| `CFG-04-01` | `FEAT-04` | Số cấp tối đa của cây phân loại | 5 cấp | 1 – 5 | Cấu hình danh mục vé |
| `CFG-06-01` | `BR-06.1` | Ma trận Tác động × Khẩn cấp → mức ưu tiên | Toàn công ty × Chặn hoàn toàn = Khẩn cấp; Phòng ban × Chặn hoàn toàn hoặc Toàn công ty × Có phương án tạm thời = Cao; Cá nhân × Không khẩn cấp = Thấp; các tổ hợp còn lại = Trung bình | Mỗi ô một mức ưu tiên của danh mục | Cấu hình danh mục vé |
| `CFG-08-01` | `BR-08.7` | Ngày lễ áp cho lịch phục vụ liên tục | Không áp dụng | Áp dụng / Không áp dụng (theo từng lịch) | Cấu hình cam kết dịch vụ |
| `CFG-09-01` | `BR-09.4` | Số lần tạm dừng tối đa trước khi cảnh báo | 3 lần | 1 – 20 | Cấu hình cam kết dịch vụ |
| `CFG-10-01` | `BR-10.1` | Hạn mức vé đang giữ mặc định của workspace | 10 vé | 1 – 200 | Cấu hình điều phối |
| `CFG-10-02` | `BR-10.1` | Hạn mức riêng của từng tư vấn viên | Trống (kế thừa `CFG-10-01`) | Trống hoặc 1 – 200 | Quản lý ca trực & kỹ năng |
| `CFG-11-01` | `BR-11.4` | Cách ứng xử khi không có ai đủ kỹ năng | Chờ rồi hạ tiêu chuẩn | Chờ / Hạ tiêu chuẩn / Chờ rồi hạ tiêu chuẩn (theo từng hàng đợi) | Cấu hình điều phối |
| `CFG-11-02` | `BR-11.4` | Thời gian chờ người đủ kỹ năng trước khi hạ tiêu chuẩn | 15 phút | 1 – 240 phút | Cấu hình điều phối |
| `CFG-12-01` | `BR-12.4` | Thời gian được từ chối vé bàn giao | 30 phút | 0 – 240 phút | Cấu hình điều phối |
| `CFG-12-02` | `BR-12.5` | Số lần chuyển tối đa trước khi cảnh báo | 3 lần | 1 – 20 | Cấu hình điều phối |
| `CFG-15-01` | `BR-15.1` | Dung lượng tối đa mỗi tệp đính kèm | 25 MB | 1 – 100 MB | Cấu hình danh mục vé |
| `CFG-15-02` | `BR-15.2` | Danh sách định dạng tệp được nhận | Ảnh, tài liệu văn phòng, PDF, văn bản, tệp nén | Không gồm được tệp thực thi | Cấu hình danh mục vé |
| `CFG-15-03` | `BR-15.1` | Số tệp tối đa mỗi lượt gửi | 20 tệp | 1 – 50 | Cấu hình danh mục vé |
| `CFG-17-01` | `BR-17.1` | Ngưỡng cảnh báo sớm theo tỷ lệ thời hạn | 75% | 50% – 95% | Cấu hình cam kết dịch vụ |
| `CFG-17-02` | `BR-17.1` | Ngưỡng cảnh báo tuyệt đối tối thiểu | Không đặt | Không đặt hoặc 5 – 240 phút | Cấu hình cam kết dịch vụ |
| `CFG-18-01` | `BR-18.1` | Các mốc leo thang sau vi phạm | Ngay khi vi phạm, sau 1 giờ, sau 4 giờ làm việc | 1 – 5 mốc | Cấu hình cam kết dịch vụ |
| `CFG-18-02` | `BR-18.2` | Số cấp quản lý báo lên | 1 cấp | 1 – 3 | Cấu hình cam kết dịch vụ |
| `CFG-18-03` | `BR-18.4` | Người nhận dự phòng khi không có quản lý trực tiếp hoặc vé chưa gán | Trưởng nhóm, rồi Trưởng phòng Dịch vụ Khách hàng | Theo thứ tự trên hoặc một thành viên chỉ định | Cấu hình cam kết dịch vụ |
| `CFG-20-01` | `BR-20.1` | Hiệu lực đường dẫn khảo sát | 7 ngày | 1 – 30 ngày | Cấu hình cam kết dịch vụ |
| `CFG-20-02` | `BR-20.4` | Trường hợp loại trừ khảo sát thêm | Không có | Theo nhãn, kênh, phân loại | Cấu hình cam kết dịch vụ |
| `CFG-21-01` | `BR-21.3` | Hạn mở lại vé sau khi kết thúc | 7 ngày | 1 – 90 ngày | Cấu hình cam kết dịch vụ |
| `CFG-22-01` | `BR-22.1` | Thời gian chờ trước khi tự động đóng | 48 giờ làm việc | 8 – 720 giờ làm việc | Cấu hình cam kết dịch vụ |
| `CFG-22-02` | `BR-22.3` | Thời gian nhắc khách trước khi tự động đóng | 12 giờ làm việc | Nhỏ hơn `CFG-22-01` | Cấu hình cam kết dịch vụ |
| `CFG-23-01` | `BR-23.1` | Số vé tối đa mỗi lượt thao tác hàng loạt | 500 vé | 1 – 1.000 | Cấu hình danh mục vé |
| `CFG-24-01` | `BR-24.1` | Dung lượng tối đa tệp nhập | 50 MB | 1 – 100 MB | Cấu hình danh mục vé |
| `CFG-25-02` | `BR-25.1` | Hiệu lực đường dẫn tải tệp xuất | 24 giờ | 1 – 72 giờ | Người có toàn quyền |
| `CFG-26-01` | `BR-26.1` | Thời gian lưu vé trong Thùng rác | 30 ngày | 7 – 90 ngày | Người có toàn quyền |
| `CFG-27-01` | `BR-27.5` | Thời hạn hoàn tác gộp vé | 24 giờ | 1 – 168 giờ | Cấu hình danh mục vé |
| `CFG-29-01` | `BR-29.4` | Lượt đọc vé cho người phụ trách cơ hội liên kết | Bật | Bật / Tắt | Người có toàn quyền (bật là nới rộng) |
| `CFG-30-01` | `BR-30.1` | Mức ưu tiên tối thiểu để cảnh báo rủi ro sang cơ hội | Cao | Theo danh mục mức ưu tiên | Cấu hình cam kết dịch vụ |
| `CFG-30-02` | `BR-30.1` | Chỉ cảnh báo khi vé đang vi phạm cam kết | Tắt | Bật / Tắt | Cấu hình cam kết dịch vụ |
| `CFG-32-01` | `FEAT-32` | Cho phép giám sát và hướng dẫn hậu trường | Bật | Bật / Tắt | Người có toàn quyền |
| `CFG-34-01` | `BR-34.6` | Ngưỡng cảnh báo vé tồn đọng trong hàng đợi | 15 phút | 1 – 240 phút (theo từng hàng đợi) | Cấu hình điều phối |
| `CFG-34-02` | `BR-34.8` | Danh sách hàng đợi được chuyển tới của từng hàng đợi | Trống (không chuyển được) | Tập con các hàng đợi vé | Người có toàn quyền; đặt được trong Phiên triển khai |
| `CFG-37-01` | `BR-37.5` | Số lần thử lại khi gửi thông báo thất bại | 3 lần | 1 – 10 | Cấu hình cam kết dịch vụ |
| `CFG-37-02` | `BR-37.4` | Kênh đánh thức và danh sách sự kiện được dùng | Tắt; khi bật, mặc định chỉ "vi phạm cam kết trong khung trực ngoài giờ" | Tin nhắn / Cuộc gọi tự động; danh sách sự kiện | Cấu hình cam kết dịch vụ |
| `CFG-38-01` | `BR-38.4` | Cửa sổ cảnh báo ghi đè cùng một trường | 60 giây | 10 – 600 giây | Cấu hình danh mục vé |
| `CFG-38-02` | `BR-38.2` | Thời gian không nhập liệu trước khi gỡ cảnh báo đang soạn | 120 giây | 30 – 600 giây | Cấu hình danh mục vé |
| `CFG-43-01` | `BR-43.2` | Nhãn loại trừ khỏi thống kê tuân thủ | Không có | Danh sách nhãn | Cấu hình cam kết dịch vụ |
| `CFG-45-01` | `BR-45.1` | Thời hạn trước khi vé đã đóng chuyển sang lưu trữ dài hạn | 36 tháng | 6 – 120 tháng | Chủ sở hữu, có xác nhận của Người phụ trách Bảo vệ Dữ liệu nếu đã chỉ định |
| `CFG-45-02` | `BR-45.4` | Thời hạn thực thi yêu cầu xóa trên vé khi yêu cầu không mang thời hạn | 72 giờ thực | 24 giờ – 30 ngày; doanh nghiệp đặt theo pháp luật áp dụng cho mình | Chủ sở hữu, có xác nhận của Người phụ trách Bảo vệ Dữ liệu nếu đã chỉ định |
| `CFG-45-03` | `BR-45.4` | Mốc cảnh báo trước hạn thực thi yêu cầu xóa | Còn 24 giờ và còn 6 giờ | 1 – 3 mốc | Người phụ trách Bảo vệ Dữ liệu hoặc Chủ sở hữu |

**Hằng số kèm lý do:** không nhận tệp thực thi (`BR-15.2` — đường lây mã độc); mốc Phản hồi Đầu tiên không bao giờ tạm dừng (`BR-09.2`); khử định danh thay trọn nội dung, không dò xóa một phần (`BR-45.2`); tác nhân AI luôn nhận giá trị đã che — hằng số hệ thống theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-40.1` (`BR-45.11`).

---

## Phụ lục C: Nhật ký Mâu thuẫn & Quyết định đã chốt

| # | Mâu thuẫn / câu hỏi | Cách xử lý đã chốt | Nơi có hiệu lực |
| --- | --- | --- | --- |
| C.1 | Vai trò "Support Agent / Team Lead / Support Manager / Sales Rep / Administrator" khác danh sách vai trò dựng sẵn của phân hệ Phân quyền; năng lực gắn cứng cho tên vai trò ("không hạ xuống Team Lead trong bất kỳ cấu hình nào") | Dùng vai trò dựng sẵn của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`; Quản trị viên là cấp bậc; mọi năng lực là ô hoặc quyền quản trị; Trưởng nhóm/Trưởng phòng là cách gọi theo vị trí, phạm vi do đơn vị quyết định | Mục 2.2, Mục 5 |
| C.2 | Phạm vi "Scope gán / Scope PB / Toàn quyền" và "giao với ca trực" tự định nghĩa khác mức truy cập chung | Thay bằng năm mức truy cập theo Mục 1.4 của tài liệu Phân quyền; ca trực chỉ ảnh hưởng phân bổ tự động, không ảnh hưởng phạm vi dữ liệu | Mục 1.4, `BR-35.1`, Mục 5.1 |
| C.3 | "Hàng đợi theo nhóm" không nói vé thuộc ai; vé đi theo người nhận làm mất dấu và lộ dữ liệu sang đội khác | Vé là bản ghi công việc thuộc đơn vị tiếp nhận suốt vòng đời; khai báo đơn vị tiếp nhận cho từng nguồn; tùy chọn gồm các đơn vị con | `BR-34.1` – `BR-34.3` |
| C.4 | Tự nhận việc không gắn với quyền nào | Nhận việc là thao tác Gán; cần ô Gán khác Không có | `BR-34.4` |
| C.5 | Không có cách chuyển vé sang đội khác; không rõ ai được chuyển và chuyển tới đâu | Chuyển hàng đợi cần ô Gán bao phủ vé, chỉ tới hàng đợi trong danh sách được chuyển tới (mặc định trống cho tới khi Người có toàn quyền khai báo; khai báo được trong Phiên triển khai), bắt buộc lý do; người phụ trách ngoài đơn vị mới mất vé | `BR-34.8`, `CFG-34-02` |
| C.6 | Phân bổ tự động và bàn giao có thể giao vé cho người không sửa được vé hoặc người đội khác | Người nhận phải là thành viên đơn vị tiếp nhận, Đang hoạt động, có ô Sửa (và Gán với phân bổ tự động), không bị chặn | `BR-10.3`, `BR-12.6`, `BR-18.2` |
| C.7 | Vô hiệu hóa tài khoản "bắt buộc chuyển giao trước khi hoàn tất" mâu thuẫn nguyên tắc tạm ngưng thực hiện ngay | Tạm ngưng thực hiện ngay, không bị chặn kể cả khi nhật ký lỗi (ghi bù, ADR-0010); xử lý vé là bước riêng; rời workspace có bàn giao bắt buộc theo quy trình chung | `BR-36.4`, `BR-36.5` |
| C.8 | Gộp vé mặc định "Support Manager", hoàn tác "không hạ dưới Support Manager" | Gộp vé và Hoàn tác gộp vé là hai ô riêng; mặc định của Quản lý = Đơn vị của mình (cùng mức với Xoá theo mức đặt sẵn của Phân quyền); nhật ký giữ cả hai lượt nên không ai xóa được dấu vết | `BR-27.7`, Mục 5.1 |
| C.9 | Xuất "Scope PB" mặc định cho Trưởng nhóm, khác mức mặc định Xuất = Không có của vai trò Quản lý trong tài liệu Phân quyền | Theo tài liệu Phân quyền: mặc định Không có; doanh nghiệp điều chỉnh ô; xuất đơn vị và xuất toàn workspace là hai mức của cùng ô | `BR-25.5`, Mục 5.1 |
| C.10 | Nhân viên kinh doanh "xem vé liên quan" nhưng vé thuộc đơn vị hỗ trợ nên mức Xem của họ không phủ tới | Lượt cấp Chỉ đọc do phân hệ sinh cho người phụ trách cơ hội đã liên kết, có điều kiện thu hồi | `BR-29.4`, `CFG-29-01` |
| C.11 | Vé khiếu nại "tư vấn viên mất quyền xem" không có cơ chế; đồng nghiệp cùng đội vẫn thấy | Vé khiếu nại là loại dữ liệu riêng với ô riêng; lượt chặn tự động với người bị khiếu nại | `BR-39.3`, Mục 5.1 |
| C.12 | Trường nhạy cảm của vé chưa được khai báo; "che trường nhạy cảm" ở hai nơi nói khác nhau | Khai báo theo khung che chung; vé không hiển thị rộng hơn hồ sơ khách hàng; một quy tắc dùng cho mọi nơi kể cả tệp xuất | `BR-45.11`, `BR-25.3` |
| C.13 | Phân hệ Vé chưa thỏa hợp đồng xóa theo mã khách hàng (ADR-0008 Điều khoản 5); không có trạng thái tạm dừng theo yêu cầu pháp lý | Thực thi theo mã khách hàng trong một lần, trả kết quả về Biên bản Hoàn tất Xử lý; tạm dừng xóa giữ yêu cầu chưa hoàn tất | `BR-45.9`, `BR-45.10` |
| C.14 | Thời hạn 72 giờ và thời hạn lưu khóa theo "Nghị định 13" như sàn pháp lý cố định | Thời hạn là của yêu cầu; khi thiếu thì theo tham số doanh nghiệp tự đặt theo pháp luật áp dụng; hệ thống không duy trì quy định của quốc gia nào | `BR-45.4`, `CFG-45-02` |
| C.15 | Quyền đọc nhật ký nêu bằng ghi chú "bảng phân quyền trước đây cấp Toàn quyền" | Ô Xem nhật ký thay đổi cho nhật ký từng vé; tra cứu toàn kho theo sàn của phân hệ Khách hàng | `BR-44.5` |
| C.16 | Nhãn trạng thái triển khai, ghi chú hiện trạng, tên trường và tên kỹ thuật trong thân tài liệu; Mục 7 "Khoảng cách Triển khai" nói về mã nguồn | Gỡ toàn bộ; các điểm đã chốt (ma trận cấu hình được, Thùng rác theo doanh nghiệp, kênh đánh thức, hạn mức riêng từng người, biến mẫu không điền được, vé con đã đóng không nhận lan truyền) đưa vào quy tắc; Mục 7 chỉ còn nhu cầu chưa chốt | Mục 3, Mục 7 |
| C.17 | `BR-19.2` chặn kết thúc mọi vé chưa phản hồi, kể cả thư rác | Trạng thái kết thúc được đánh dấu miễn ràng buộc này | `BR-19.2` |
| C.18 | Kết thúc đồng loạt vé con, gán vé con, gắn nhãn và chuyển hàng loạt có thể vượt quyền người làm | Mỗi vé chịu ô của người thực hiện; vé ngoài phạm vi được liệt kê riêng | `BR-23.3`, `BR-28.1`, `BR-28.3`, `BR-36.1` |
| C.19 | "Thời gian thực" của bảng điều khiển không đo được | Thay đổi phản ánh chậm nhất 5 giây | `NFR-13` |
| C.20 | Đổi hạng khách hàng khi vé đang mở chưa có quyết định | Đưa vào Mục 7, chưa chốt | Mục 7 |
| C.22 | Tư vấn viên (Gán = Chỉ của mình) bàn giao trực tiếp vé của mình không cần người nhận chấp nhận, trái quy tắc đích của ô Gán | Theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`: giao trực tiếp cần Gán từ Đơn vị của mình trở lên; Người phụ trách hiện tại luôn có trả về hàng đợi và đề nghị chuyển — đề nghị chỉ hiệu lực khi người nhận chấp nhận bằng điều kiện nhận việc của chính họ, hết hạn theo tham số của phân hệ Phân quyền | `BR-12.6`, `BR-12.7`, `BR-34.9`, `BR-35.3` |
| C.23 | Vé tạo từ hội thoại lấy đơn vị tiếp nhận của kênh, bỏ qua việc hội thoại đã được chuyển; người tạo chọn tùy ý | Mặc định đơn vị tiếp nhận hiện tại của hội thoại; chọn khác chỉ trong danh sách được chuyển tới | `BR-34.2` |
| C.24 | Nội dung sao chép từ hội thoại có thể mở lại giá trị đã che; dữ liệu nhạy cảm khách gõ vào thư không được che | Giữ giá trị đã che; áp phát hiện và che của Hội thoại Đa kênh cho vé từ thư, biểu mẫu, tích hợp | `BR-45.12` |
| C.25 | Một hộp thư có thể vừa sinh hội thoại vừa sinh vé | Mỗi hộp thư thuộc đúng một phân hệ, chọn khi kết nối | `BR-34.10` |
| C.26 | Đổi Đơn vị chính và kiêm nhiệm hết hạn gộp chung thành một đề xuất | Đổi Đơn vị chính là bước bắt buộc, không chọn sẵn, không có "đi theo người"; kiêm nhiệm hết hạn là đề xuất | `BR-36.7`, `BR-36.8` |
| C.27 | Chuyển hàng đợi với Gán = Chỉ của mình chưa dẫn căn cứ; điều kiện giữ Người phụ trách khi chuyển "trừ điều kiện trong ca" chưa rõ | Dẫn ngoại lệ [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.14`; liệt kê điều kiện giữ và điều kiện miễn (trong ca, Sẵn sàng, hạn mức) | `BR-34.8` |
| C.28 | Dòng nhập chỉ có cột Người phụ trách không rõ vé thuộc đơn vị nào | Thuộc hàng đợi chọn cho cả lô | `BR-34.2` |
| C.29 | Đề xuất trả vé khi kích hoạt lại loại cả vé đã chuyển tạm | Chỉ loại vé đã trả về hàng đợi hoặc đã được người khác nhận từ hàng đợi | `BR-36.4` |
| C.30 | Mã quy tắc trùng số với phân hệ Phân quyền trong Mục 5 dễ đọc nhầm | Quy ước mã và ghi "(tài liệu này)" | Mục 5 |
| C.31 | Danh sách người nhận đề nghị chuyển chưa lọc trước; đề nghị treo khi vé đổi người hay kết thúc | Lọc sẵn theo mọi điều kiện (gồm người bị khiếu nại), kiểm lại khi chấp nhận; hủy khi đổi Người phụ trách bằng bất kỳ đường nào, khi kết thúc, gộp, xóa | `BR-12.7` |
| C.32 | Đổi phân hệ của hộp thư bằng ngắt rồi kết nối lại gây khoảng trống thư (quyết định chung với Hội thoại Đa kênh) | Một thao tác chuyển phân hệ, chỉ Người có toàn quyền, thư mới vào phân hệ mới ngay, dữ liệu cũ ở lại, có nhật ký | `BR-34.10` |
| C.33 | Vé từ hội thoại lấy thẳng đơn vị của hội thoại, không theo cơ chế mặc định theo loại nguồn | Loại nguồn "Vé từ hội thoại" trong mặc định theo loại nguồn của phân hệ Phân quyền, tìm từ đơn vị hiện tại của hội thoại lên gốc; không có thì chặn | `BR-34.2` |
| C.34 | Bước xử lý vé trong tạm ngưng, rời đi, đổi đơn vị đòi ô Gán của người thực hiện | Không cần; bước chịu nhật ký đóng khi lỗi; ràng buộc ở người nhận | `BR-36.9` |
| C.35 | Danh sách hàng đợi được chuyển tới mặc định mở cho mọi hàng đợi (quyết định chung với Hội thoại Đa kênh) | Mặc định trống; đặt được trong Phiên triển khai. Thay quyết định mặc định tại C.5 | `BR-34.8`, `CFG-34-02` |
| C.36 | Mã tham số đường dẫn tải trùng số với tham số hạn đề nghị chuyển của phân hệ Phân quyền; tên phân hệ hội thoại không thống nhất | Đánh lại thành `CFG-25-02`; dùng tên "Hội thoại Đa kênh" | `BR-25.1`, toàn tài liệu |
| C.37 | Gỡ kiêm nhiệm thủ công chưa có lý do vì sao chỉ là đề xuất | Bổ sung lý do theo nguyên tắc thu hồi không chờ việc khác | `BR-36.8` |
| C.38 | Dòng nhập có Người phụ trách không thuộc đơn vị tiếp nhận của dòng vẫn được nhập; vé giữ chỗ chưa gắn với đơn vị tiếp nhận | Dòng lỗi; vé giữ chỗ thuộc đơn vị tiếp nhận của dòng hay lô, lời mời phải ghi đơn vị đó (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10`) | `BR-34.2`, `BR-24.3` |
| C.39 | Nội dung chép từ hội thoại có thể hiện đầy đủ liên hệ theo quyền người tạo (quyết định chung với Hội thoại Đa kênh) | Luôn dùng dạng che hạn chế nhất, bất kể quyền người tạo | `BR-45.12` |
| C.40 | Ô Tạo vé của Nhân viên Kinh doanh lệch mức mặc định chung không khai báo | Khai báo lệch kèm lý do tạo vé hộ khách | Mục 5.1 |
| C.41 | Vé tạo thủ công không rõ ai phụ trách | Thành viên đơn vị tiếp nhận: "Giao cho tôi" bật sẵn, bỏ được; người ngoài đơn vị: vé chưa gán | `BR-34.2` |
| C.42 | Chuyển phân hệ hộp thư ghi nhật ký thường, không đóng khi lỗi | Nhật ký thay đổi cấu hình quyền, đóng khi lỗi | `BR-34.10` |
| C.43 | "Đang bị khiếu nại" viết như nguồn chặn trên vé gốc, chỉ áp cho đề nghị chuyển | Điều kiện riêng của phân hệ, áp cho mọi đường nhận vé | `BR-12.8` |
| C.44 | Lô nhập chạy theo quyền hiện tại; mốc leo thang chuyển vé tới nơi người cấu hình không gán được | Phần giao của mức lúc khởi chạy và hiện tại; đích chịu ô Gán của người cấu hình | `BR-24.6`, `BR-18.2` |
| C.45 | Che cho tác nhân AI ghi là Sàn bắt buộc (quyết định chung) | Là hằng số hệ thống | Mục 5.4 |
| C.46 | Vé giữ chỗ khi lời mời bị sửa bỏ đơn vị tiếp nhận; dòng có đơn vị tiếp nhận nhưng không có Người phụ trách tự gán cho người nhập; bước bàn giao trong quy trình chung chưa loại người bị khiếu nại; tạm dừng xóa chỉ theo nhóm vé, không có ngày dự kiến gỡ trong kết quả; địa chỉ trả lời có thể lấy từ nội dung chép | Theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10` (C.109 của tài liệu đó): giữ chỗ luôn ở đơn vị tiếp nhận, chấm dứt khi lời mời bỏ đơn vị; dòng chỉ có đơn vị tiếp nhận thì vé chưa gán; `BR-12.8` áp cả bước bàn giao chung; tạm dừng theo vé hoặc theo khách, kết quả có ngày dự kiến gỡ; địa chỉ trả lời chỉ từ hồ sơ hoặc định danh kênh | `BR-34.2`, `BR-24.3`, `BR-12.8`, `BR-36.9`, `BR-45.10`, `BR-45.12` |
| C.21 | Hai tham số điều kiện cảnh báo rủi ro mang mã thứ tự 03, 04 không có trong danh mục tham số; nhiều tham số khác không có mã | Đánh lại thành `CFG-30-01`, `CFG-30-02`; mọi tham số có mã tại Phụ lục B | Phụ lục B |
