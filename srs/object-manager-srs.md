# SRS — Object Manager

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Chuẩn nghiệp vụ và đặc tả chức năng |
| **Module** | Object Manager — Cấu hình cấu trúc dữ liệu và chính sách dữ liệu cho Contact, Account, Deal, Ticket, Task |
| **Ngày cập nhật** | 2026-08-24 |
| **Phiên bản** | **v4.4** · bản chốt (baseline) từ v4.0 · xem *Lịch sử phiên bản* bên dưới |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (Glossary) · [ADR-0001](../docs/adr/0001-group-policy-conflict-resolution.md) — xung đột nhóm quyền · [ADR-0003](../docs/adr/0003-permission-config-audit-log-fail-closed.md) và [SRS IAM](./iam-tenant-authorization.md) BR-41.4/41.5 — phân loại nhật ký kiểm toán |

## Ghi chú về nguồn gốc & Nguyên tắc tài liệu

Tài liệu này được tái cấu trúc sau vòng review chiến lược giữa Product Owner, Lead Business Analyst và Solution Architect. Tài liệu tuân thủ các nguyên tắc sau:

1. **Chuẩn nghiệp vụ độc lập (Business-First):** Các quy tắc nghiệp vụ (BR) mô tả logic kinh doanh đúng đắn và chuẩn mực của một nền tảng B2B CRM, hoàn toàn độc lập với cách hiện thực hóa. Tài liệu đặc tả **hệ thống phải hành xử như thế nào để đúng về mặt nghiệp vụ**, không đặc tả hệ thống được xây dựng bằng cách nào.
2. **Tách bạch nghiệp vụ khỏi kỹ thuật:** Mọi quy tắc trong Mục 3 và Mục 4 được viết bằng ngôn ngữ nghiệp vụ mà Product Owner, Customer Success và khách hàng doanh nghiệp đều đọc hiểu và phản biện được. Các thuật ngữ kỹ thuật, tên cơ chế hoặc chuẩn công nghệ tương ứng — nếu cần cho đội phát triển — được tập trung tại **Phụ lục A (Ghi chú kỹ thuật tham chiếu)** và mang tính **không ràng buộc (non-normative)**: khi Phụ lục A và phần thân tài liệu có khác biệt, **phần thân tài liệu là căn cứ duy nhất để nghiệm thu**.
3. **Phân định ranh giới giữa Chuẩn nghiệp vụ và Hạn chế Phase 1:** Các năng lực chuẩn mà phiên bản Phase 1 chưa đáp ứng (ví dụ: chưa gộp Account, chưa sửa hàng loạt Contact) được đưa vào mục **"Hạn chế hệ thống Phase 1" (Mục 7)** để làm căn cứ nghiệm thu cho QA, tuyệt đối không bị đồng hóa thành "quy tắc nghiệp vụ vĩnh viễn".
4. **Quy ước nhãn trạng thái:** Mỗi tính năng (FEAT) hoặc quy tắc (BR) được gắn nhãn để phục vụ theo dõi tiến độ bàn giao:
   - **[Đã triển khai]** — Đã có trong hệ thống và khớp với đặc tả chuẩn.
   - **[Cần chuẩn hóa]** — Năng lực đã tồn tại nhưng hành vi hiện tại chưa khớp chuẩn nghiệp vụ, cần điều chỉnh để khớp (ví dụ: Giai đoạn Cơ hội chưa thuộc về từng Quy trình bán hàng tại FEAT-07; Danh sách hiển thị chưa có Bộ lọc tại FEAT-08).
   - **[Yêu cầu mới]** — Năng lực chưa tồn tại, bổ sung vào phạm vi phát triển.
5. **Trạng thái bản chốt (từ v4.0):** Tài liệu đã qua bốn vòng phản biện chéo nghiệp vụ và được chốt làm **căn cứ phân rã công việc**. Không còn câu hỏi nghiệp vụ nào để ngỏ trong phần đặc tả: những điểm chưa quyết được đã chuyển thành issue có người chịu trách nhiệm, và những giới hạn chưa làm được nằm ở Mục 7.1. Mỗi thay đổi nội dung sau v4.0 phải nêu lý do nghiệp vụ và ghi vào Lịch sử phiên bản, để người đọc sau luôn phản biện được *lập luận* chứ không chỉ thấy *kết luận*.

## Lịch sử phiên bản

| Phiên bản | Nội dung thay đổi chính |
| --- | --- |
| **v2.0** | Tái cấu trúc sau vòng review chiến lược PO / Lead BA / Solution Architect: chuẩn hóa kiến trúc Pipeline–Stage, tách bạch Chuẩn nghiệp vụ khỏi Hạn chế Phase 1, phân kỳ lộ trình. |
| **v2.1** | Vá các lỗ hổng phát hiện qua phản biện chéo nghiệp vụ vòng 1: deadlock FLS vs Bắt buộc theo giai đoạn (BR-05.5), chống né Stage Gating khi chốt Thắng (BR-07.4 & BR-09.2), chống trùng Cơ hội khi chuyển đổi (BR-05.3), truy vết trách nhiệm Automation Bypass FLS (BR-03.4), các fallback vận hành (BR-08.1, BR-09.1, BR-09.3). |
| **v2.2** | Vá các lỗ hổng phát hiện qua phản biện chéo vòng 2: tie-break Ẩn vs Bắt buộc trong cùng tầng FLS (BR-03.2), quy tắc chuyển Deal giữa các Pipeline (BR-07.5), vòng đời Stage & Pipeline khi đang có Deal (BR-07.6), giải quyết tranh chấp chủ sở hữu Deal tự sinh (BR-05.3), quản trị bản ghi bị gắn cờ thiếu dữ liệu (BR-05.5), hoàn tác cấu hình FLS sai (BR-10.5), chống ghi đè khi hai Admin sửa cùng một cấu hình (NFR-07b). |
| **v2.3** | Vá các lỗ hổng phát hiện qua phản biện chéo vòng 3: bổ sung **Yêu cầu chuyển đổi dữ liệu khi Refactor (Mục 7.3)** — rủi ro nghiệp vụ lớn nhất của lộ trình, trước đó không được đặc tả ở bất kỳ đâu; chặn xóa trường đang là điều kiện chặn nghiệp vụ (BR-02.5); chặn luân chuyển dữ liệu nhạy cảm sang trường bảo vệ thấp hơn qua Automation (BR-03.4); chống tồn đọng cờ thiếu dữ liệu (BR-05.5); làm rõ ranh giới hạn chế Cross-field Validation so với các ràng buộc điều kiện có sẵn (Mục 7.1); ràng buộc hiệu năng theo số Nhóm quyền (NFR-08); yêu cầu dữ liệu đo (NFR-09); phân kỳ lộ trình lại theo quan hệ phụ thuộc thay vì theo chủ đề (Mục 7.2). |
| **v3.0** | **Chuẩn hóa tài liệu về thuần nghiệp vụ và tách bạch khỏi kỹ thuật.** Loại bỏ thuật ngữ kỹ thuật khỏi toàn bộ quy tắc nghiệp vụ và chuyển sang **Phụ lục A** (không ràng buộc); giải quyết các xung đột nghiệp vụ còn lại: tách hai chiều Mức truy cập vs Mức hiển thị (BR-03.1), làm rõ Tenant Admin không bị FLS giới hạn và hệ quả với cam kết bán hàng (BR-03.2b), ngoại lệ có kiểm soát cho Đội Vận hành nội bộ (NFR-02), trường đã vô hiệu hóa không chiếm hạn mức (BR-02.3), cho phép Liên hệ đi ngược giai đoạn và tái tiếp cận khách cũ (BR-05.1), nhật ký kiểm toán không được mất (BR-10.3). |
| **v3.1** | Soạn phần bổ sung cho ADR-0001 (mô hình hai chiều, thứ tự ưu tiên với ràng buộc bắt buộc, nguyên tắc vắng mặt cấu hình, phạm vi chủ thể) — **đang chờ PO và Solution Architect thông qua**. Sửa xung đột giữa BR-10.3 và nguyên tắc đóng tại SRS IAM BR-41.4/41.5: phân loại lại nhóm fail-closed theo mặc định-thuộc-nhóm, đưa việc vô hiệu hóa trường và hoàn tác FLS vào nhóm fail-closed, nêu lý do loại trừ tường minh cho FEAT-08 (BR-10.3, BR-10.5). |
| **v3.2** | Kiểm toán lại toàn văn theo chuẩn nghiệp vụ: lấp khoảng trống đặc tả **Bố cục form nhập liệu (BR-03.7)** — trước đó được hứa trong Phạm vi, Thuật ngữ và tên FEAT-03 nhưng không có quy tắc nào; ghi nhận yêu cầu đối chiếu hiện trạng (Mục 7.1 điểm 9); làm sạch các thuật ngữ kỹ thuật còn sót (mã nguồn, cơ sở dữ liệu, Boolean, API Integration). |
| **v3.3** | Rà soát sẵn sàng phát hành: khử các chỗ hệ thống có thể làm hai cách khác nhau — cột ẩn/che trong danh sách hiển thị (BR-08.2), danh sách mặc định khi người dùng thuộc nhiều nhóm (BR-08.1), ý nghĩa cờ trạng thái đóng ngoài Cơ hội (BR-06.1); bảo toàn Nguồn đang dùng (BR-06.3); fallback khi không xác định được khu vực (BR-09.3); bổ sung tiêu chí nghiệm thu cho FEAT-06/08/09 và ghi nhận thiếu điều kiện đóng Ticket (Mục 7.1 điểm 10). |
| **v4.4** | Kết thúc đối chiếu hiện trạng **Bố cục form nhập liệu (BR-03.7)** (issue [#30](https://github.com/crmsaassaudi/product-management/issues/30)) và đóng lại điểm 9 của Mục 7.1 đúng như quy trình đối chiếu yêu cầu: kết luận **(b) đã có nhưng khác chuẩn**, nên **giữ Bố cục form trong Phạm vi Mục 1.2**. Toàn bộ chuẩn nghiệp vụ của quy tắc đã hiện thực hóa và nghiệm thu được ở phía máy chủ — riêng quy tắc **bố cục không mở thêm quyền** nay có kiểm chứng riêng ở cả hai đầu (phân giải và phản hồi), vì đây là chỗ mà nếu sai thì trình sửa bố cục trở thành đường vòng vô hiệu hóa phân quyền trường. Phần còn lệch — **giao diện quản trị Bố cục cho Tenant Admin** — được tách thành issue [#57](https://github.com/crmsaassaudi/product-management/issues/57) và đưa vào Mục 7.2 Sprint R3, thay vì để trong Mục 7.1 dưới dạng một câu hỏi đã có lời đáp. Nhãn FEAT-03 được ghi đúng theo từng nửa: phần FLS đã triển khai, phần giao diện Bố cục cần chuẩn hóa — gắn một nhãn chung cho cả hai sẽ báo sai một trong hai nửa. Ghi nhận **bố cục theo giai đoạn vòng đời** nằm ngoài phạm vi nghiệm thu BR-03.7 vì chưa có quy tắc nghiệp vụ nào đặc tả nó. |
| **v4.3** | **Thông qua phần Bổ sung 2026-08-23 của ADR-0001** (issue [#29](https://github.com/crmsaassaudi/product-management/issues/29)) và đưa trọn bốn điều khoản vào phần thân đặc tả, thay vì để một nửa nằm ở Phụ lục A không ràng buộc: bổ sung **nguyên tắc vắng mặt cấu hình không phải là sự cho phép** và **vị trí của Bố cục mặc định trong phân giải, xét theo từng trường** (BR-03.2) — trước đó chỉ có ở ADR và Phụ lục A, nên phần duy nhất dùng để nghiệm thu lại không nói gì; gỡ ghi chú "chờ thông qua" ở BR-03.2 và Mục 7.2. Bổ sung **MIG-07** trả lời câu hỏi đường di trú của cấu hình phân quyền đang tồn tại sang mô hình hai chiều (Mục 7.3). Sửa siêu dữ liệu đầu tài liệu vốn còn ghi v4.0 trong khi đã có v4.1 và v4.2. |
| **v4.2** | Chốt hai khoản còn để ngỏ của hoàn tác phân quyền trường trước khi hiện thực hóa (BR-10.5): chỉ hoàn tác được thay đổi mới nhất của cùng một mục tiêu, và ngữ nghĩa khi giá trị trước đó là sự vắng mặt. Cả hai đều là chỗ hệ thống có thể làm hai cách khác nhau mà đặc tả không nói. |
| **v4.1** | Đối chiếu đặc tả với hiện trạng sau đợt chuẩn hóa Sprint R1–R3 và **sửa đặc tả trước, không để đặc tả lạc hậu so với hệ thống**: chốt ba điều kiện đóng Thắng là bắt buộc vô điều kiện, không còn là tùy chọn của tenant (BR-09.2); bổ sung đường phục hồi khi hệ thống không đánh giá được quy tắc kiểm tra (BR-04.2); đặc tả **hợp đồng cấu hình của phân bổ theo Khu vực** — trước đây chỉ có một câu nêu tên, không đủ để hiện thực hóa, và trên thực tế năng lực này chưa tồn tại (BR-09.3, kèm hạ nhãn FEAT-09); làm rõ ý nghĩa danh mục Vai trò liên hệ khi còn trống (BR-05.3); chốt phạm vi năm đối tượng của cờ thiếu dữ liệu (BR-05.5); chốt nội dung bắt buộc của cảnh báo ghi đè và đơn vị phiên bản của cấu hình (NFR-07b). |
| **v4.0** | **Bản chốt làm căn cứ phân rã công việc.** Chốt hạn mức 20 Nhóm quyền cho một người dùng kèm lý do nghiệp vụ (NFR-08) — tham số cuối cùng còn để trống. Các câu hỏi còn treo được chuyển thành issue có người chịu trách nhiệm thay vì tiếp tục nằm trong đặc tả: thông qua phần Bổ sung ADR-0001, và đối chiếu hiện trạng Bố cục form nhập liệu (Mục 7.1 điểm 9). Từ phiên bản này, mọi thay đổi nội dung phải đi kèm lý do nghiệp vụ và cập nhật Lịch sử phiên bản. |

---

## 1. Giới thiệu

### 1.1 Mục đích

Tài liệu đặc tả toàn bộ yêu cầu chức năng và phi chức năng của module **Object Manager** — khu vực cấu hình cho phép quản trị viên tenant (Tenant Admin) tùy biến cấu trúc dữ liệu, quy tắc toàn vẹn và chính sách truy cập cho 5 đối tượng nghiệp vụ cốt lõi của CRM, mà không cần đội phát triển can thiệp.

### 1.2 Phạm vi

Tài liệu bao trùm toàn bộ tính năng quản trị cấu hình dữ liệu:

- Danh mục đối tượng và ma trận năng lực khả dụng.
- Quản lý định nghĩa trường tùy biến và thuộc tính kiểu dữ liệu.
- Phân quyền hiển thị/chỉnh sửa trường (FLS) theo Nhóm quyền và cấu hình Bố cục form (Layout).
- Quy tắc kiểm tra dữ liệu (Validation Rules) và cơ chế kích hoạt khi thay đổi dữ liệu.
- Giai đoạn vòng đời (Lifecycle Stages) và Ma trận chuyển đổi khách hàng tiềm năng.
- Quản lý Trạng thái & Nguồn theo đối tượng.
- Quản lý Quy trình bán hàng (Multi-Pipeline) và Giai đoạn cơ hội (Deal Stages).
- Danh sách hiển thị dùng chung (Shared List Views: Cột + Bộ lọc + Sắp xếp).
- Cấu hình nâng cao theo từng đối tượng (Chống trùng, Phân bổ, Điều kiện đóng thương vụ).
- Nhật ký kiểm toán thay đổi cấu hình (Configuration Audit Trail).

**Ngoài phạm vi:**

- Nghiệp vụ vận hành chi tiết của người dùng cuối (ví dụ: kịch bản telesales, thao tác xử lý ticket, chiến dịch email marketing).
- Bộ lọc/view cá nhân (Personal View) mà người dùng cuối tự lưu riêng cho bản thân ở màn hình danh sách (nằm ngoài phạm vi quản trị dùng chung của Object Manager).
- **Chỉ số thành công và mục tiêu kinh doanh (Success Metrics / KPI):** SRS đặc tả *hệ thống phải làm gì và phải đúng như thế nào*, không đặt mục tiêu kinh doanh. Ngưỡng KPI, baseline và mục tiêu theo quý thuộc tài liệu kế hoạch sản phẩm (PRD/Product Plan) — nếu đưa vào SRS, chúng sẽ lạc hậu ngay sau một quý trong khi phần đặc tả vẫn còn hiệu lực nhiều năm. Phần SRS chịu trách nhiệm là **đảm bảo các chỉ số đó đo được**, đặc tả tại NFR-09.
- Nhật ký thao tác ở cấp bản ghi — tức việc lưu vết ai đã đọc hoặc ghi giá trị cụ thể nào trên một bản ghi. Đây là năng lực của tầng lõi CRM, không thuộc phạm vi cấu hình của Object Manager, nhưng là **điều kiện tiên quyết bắt buộc** để việc miễn trừ FLS cho tác vụ tự động (BR-03.4) đáp ứng chuẩn bảo mật khi bán cho khách hàng Enterprise.

### 1.3 Đối tượng đọc

- **Product Owner / Business Analyst:** Định hướng tầm nhìn sản phẩm, lập kế hoạch sprint và backlog.
- **Kỹ sư phát triển (Developers):** Hiểu rõ bản chất nghiệp vụ để thiết kế kiến trúc và triển khai chính xác.
- **QA / Software Testers:** Căn cứ viết kịch bản kiểm thử chấp nhận (UAT) theo cả chuẩn nghiệp vụ và hạn chế Phase 1.
- **Customer Success / Solution Consultant:** Nắm vững năng lực cấu hình khi triển khai giải pháp cho khách hàng doanh nghiệp.

### 1.4 Thuật ngữ & Viết tắt

| Thuật ngữ | Giải thích |
| --- | --- |
| **Đối tượng (Object)** | Một loại thực thể dữ liệu nghiệp vụ cốt lõi: Liên hệ (Contact), Tài khoản (Account), Cơ hội (Deal), Yêu cầu hỗ trợ (Ticket), Công việc (Task). |
| **Trường tùy biến (Custom Field)** | Thuộc tính dữ liệu do quản trị viên tenant tự định nghĩa thêm vào một đối tượng. |
| **Phân quyền trường (Field-Level Security – FLS)** | Cơ chế kiểm soát, theo từng Nhóm quyền, việc một người được làm gì với một trường. Gồm hai chiều độc lập: **Mức truy cập** (Xem & Sửa / Chỉ xem / Ẩn) và **Mức hiển thị giá trị** (Hiện đầy đủ / Che một phần / Che hoàn toàn) — xem BR-03.1. |
| **Bố cục mặc định (Default Layout)** | Cấu hình bố cục và FLS áp dụng cho người dùng không thuộc nhóm quyền chuyên biệt nào. |
| **Quy tắc kiểm tra dữ liệu (Validation Rule)** | Ràng buộc nghiệp vụ mà giá trị của trường phải thỏa mãn khi lưu bản ghi. |
| **Quy trình bán hàng (Pipeline)** | Chuỗi các giai đoạn tuần tự để theo đuổi và chốt một thương vụ bán hàng. |
| **Giai đoạn Cơ hội (Deal Stage)** | Một bước cụ thể bên trong một Pipeline, gắn liền với tỷ lệ thành công (%) và thời gian kỳ vọng. |
| **Danh sách hiển thị dùng chung (Shared List View)** | Cấu hình bảng danh sách bản ghi gồm Tập cột hiển thị + Điều kiện lọc + Sắp xếp mặc định do Admin tạo và phân quyền cho Nhóm. |
| **Nhật ký kiểm toán cấu hình (Configuration Audit Trail)** | Lịch sử lưu vết thời gian, người thực hiện và chi tiết các thay đổi cấu hình trong Object Manager. |
| **Điều kiện qua giai đoạn (Stage Gating)** | Tập trường bắt buộc phải có giá trị để một Cơ hội được chuyển vào một Giai đoạn cụ thể của Pipeline. |
| **Cờ thiếu dữ liệu do giới hạn quyền** | Dấu hiệu hệ thống gắn lên bản ghi được lưu thành công nhờ miễn trừ ràng buộc bắt buộc, vì người lưu không có quyền nhìn/nhập trường đó. Là căn cứ để người có thẩm quyền bổ sung dữ liệu về sau. |
| **Vai trò liên hệ trong Cơ hội (Contact Role)** | Vai trò nghiệp vụ của một Liên hệ trong một Cơ hội (Người quyết định, Người phê duyệt, Người ảnh hưởng, Người dùng cuối...), phản ánh sơ đồ ảnh hưởng trong tài khoản doanh nghiệp. |
| **Hàng đợi chưa phân công (Unassigned Queue)** | Nơi tiếp nhận các bản ghi mà quy tắc phân công tự động không tìm được người phụ trách khả dụng, kèm cảnh báo cho cấp quản lý. |
| **Tài khoản hệ thống / Dịch vụ (Service Account)** | Danh tính của các tác vụ tự động hóa (Automation) hoặc API tích hợp. |

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

Trong mô hình CRM SaaS phục vụ đa dạng khách hàng B2B, mỗi doanh nghiệp có quy trình bán hàng, cấu trúc dữ liệu và chính sách bảo mật nội bộ hoàn toàn khác nhau. Object Manager giải quyết bài toán này bằng cách trao cho **Quản trị viên Tenant** năng lực tự cấu hình toàn bộ mô hình dữ liệu và chính sách truy cập trong vài phút, đồng thời đảm bảo các cấu hình này được thực thi nhất quán trên mọi kênh mà dữ liệu đi vào hoặc đi ra khỏi hệ thống — nhập trên máy tính, nhập trên điện thoại, nhập từ file, tác vụ tự động và tích hợp bên ngoài.

### 2.2 Năm đối tượng nghiệp vụ cốt lõi

| Đối tượng | Mô tả nghiệp vụ |
| --- | --- |
| **Liên hệ (Contact)** | Cá nhân khách hàng hoặc người liên hệ đại diện của một đối tác/doanh nghiệp. |
| **Tài khoản (Account)** | Công ty, tổ chức, pháp nhân là khách hàng doanh nghiệp hoặc đối tác kinh doanh. |
| **Cơ hội (Deal)** | Một thương vụ bán hàng đang được theo đuổi qua các giai đoạn của Pipeline. |
| **Yêu cầu hỗ trợ (Ticket)** | Một khiếu nại, thắc mắc hoặc sự cố của khách hàng cần đội ngũ CSKH xử lý. |
| **Công việc (Task)** | Một tác vụ hoặc hoạt động cần thực hiện gắn liền với Contact, Account, Deal hoặc Ticket. |

### 2.3 Vai trò người dùng (Actors)

| Actor | Quyền hạn & Trách nhiệm trong Object Manager |
| --- | --- |
| **Quản trị viên Tenant (Tenant Admin)** | Người có toàn quyền truy cập khu vực Object Manager để quản lý trường, phân quyền FLS, quy tắc kiểm tra, pipeline, danh sách hiển thị và cấu hình nâng cao. |
| **Người dùng cuối (Sales, Support, Manager...)** | Không truy cập màn hình cấu hình. Là đối tượng thụ hưởng bố cục hiển thị và chịu sự ràng buộc trực tiếp của FLS, Validation Rules và Shared List Views khi thao tác dữ liệu. |
| **Tác vụ Tự động & Tích hợp (Service Account / API)** | Các luồng Automation nội bộ hoặc kết nối API bên ngoài thực hiện ghi/đọc dữ liệu. |
| **Đội Vận hành / Hỗ trợ nội bộ (Nhà cung cấp SaaS)** | Nhân sự của bên vận hành nền tảng, **không thuộc tenant khách hàng**. Trong Phase 1, đây là actor duy nhất tra cứu được Nhật ký kiểm toán cấu hình (BR-10.4) và thực hiện hoàn tác cấu hình FLS sai (BR-10.5) khi khách hàng báo sự cố. Mọi truy cập của actor này phải tuân thủ chính sách kiểm soát truy cập nội bộ của nhà cung cấp và bản thân cũng phải được lưu vết. |

---

## 3. Đặc tả yêu cầu chức năng

### FEAT-01 — Danh mục đối tượng & Năng lực khả dụng `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp bức tranh tổng quan về 5 đối tượng dữ liệu và các năng lực thao tác dữ liệu chuẩn mà hệ thống hỗ trợ.

**Actor:** Quản trị viên Tenant.

**Quy tắc nghiệp vụ:**

- **BR-01.1 (Năng lực chuẩn của CRM B2B):** Mỗi đối tượng trong CRM hướng tới tập năng lực chuẩn bao gồm: Tạo/Sửa/Xóa, Cập nhật hàng loạt (Bulk Update), Gán chủ sở hữu hàng loạt (Bulk Assign), Gắn thẻ hàng loạt (Bulk Tag), Nhập dữ liệu từ file (Import), Xuất dữ liệu (Export), Vòng đời/Giai đoạn (Lifecycle/Pipeline), và Gộp bản ghi trùng lặp (Deduplication & Merge).
- **BR-01.2 (Năng lực khả dụng trong Phase 1):** Theo kế hoạch phân kỳ triển khai, năng lực khả dụng hiện tại của từng đối tượng được giới hạn như sau (đối chiếu đầy đủ với căn cứ nghiệm thu QA tại **Mục 7.1**):
  - **Liên hệ (Contact):** Gán chủ sở hữu hàng loạt, gắn thẻ hàng loạt, nhập/xuất file, giai đoạn vòng đời, gộp trùng lặp. *(Hạn chế Phase 1: Chưa hỗ trợ Bulk Update theo trường tùy ý — xem Mục 7)*.
  - **Tài khoản (Account):** Gán chủ sở hữu hàng loạt, gắn thẻ hàng loạt, nhập/xuất file. *(Hạn chế Phase 1: Chưa hỗ trợ Merge và chưa có Lifecycle độc lập — xem Mục 7)*.
  - **Cơ hội (Deal):** Cập nhật hàng loạt, gán chủ sở hữu hàng loạt, gắn thẻ hàng loạt, nhập/xuất file, quản lý đa Pipeline.
  - **Ticket:** Gán chủ sở hữu hàng loạt, gắn thẻ hàng loạt, nhập/xuất file, gộp trùng lặp. *(Hạn chế Phase 1: Chưa hỗ trợ Bulk Update)*.
  - **Công việc (Task):** Cập nhật hàng loạt, gán chủ sở hữu hàng loạt, xuất file. *(Hạn chế Phase 1: Chưa hỗ trợ Import file và Bulk Tag)*.

**Tiêu chí chấp nhận:**

- Admin xem được danh mục 5 đối tượng kèm trạng thái cấu hình và số lượng trường tùy biến đang sử dụng.
- Hệ thống chỉ mở các nút thao tác hàng loạt/nhập/xuất trên màn hình danh sách bản ghi khớp đúng với ma trận năng lực khả dụng của Phase 1.

---

### FEAT-02 — Quản lý trường tùy biến (Custom Fields) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép quản trị viên mở rộng mô hình dữ liệu của bất kỳ đối tượng nào để đáp ứng nhu cầu lưu trữ thông tin đặc thù của doanh nghiệp.

**Actor:** Quản trị viên Tenant.

**Danh mục kiểu dữ liệu chuẩn (Field Data Types):**

1. **Văn bản ngắn (Single-line Text):** Lưu chuỗi văn bản tối đa 255 ký tự.
2. **Văn bản dài / Định dạng phong phú (Multi-line / Rich Text):** Lưu mô tả, ghi chú chi tiết.
3. **Số (Number):** Số nguyên hoặc số thập phân, cho phép cấu hình độ chính xác thập phân.
4. **Tiền tệ (Currency):** Giá trị số gắn liền với ký hiệu/mã tiền tệ của tenant.
5. **Phần trăm (Percentage):** Giá trị phần trăm (0 - 100%).
6. **Ngày (Date):** Chỉ lưu ngày tháng năm.
7. **Ngày & Giờ (DateTime):** Lưu mốc thời gian chi tiết theo múi giờ.
8. **Hộp kiểm (Checkbox):** Giá trị Có / Không.
9. **Danh sách chọn đơn (Single-Select Dropdown):** Chọn 1 giá trị trong danh sách định sẵn.
10. **Danh sách chọn nhiều (Multi-Select Dropdown):** Chọn 1 hoặc nhiều giá trị trong danh sách.
11. **Liên kết / Tham chiếu (Lookup / Relation):** Trỏ tới một bản ghi thuộc đối tượng khác (ví dụ: Liên hệ trỏ tới Người giới thiệu).
12. **Công thức (Formula):** Giá trị chỉ đọc do hệ thống tự tính toán dựa trên biểu thức số học hoặc logic giữa các trường khác.

**Quy tắc nghiệp vụ:**

- **BR-02.1 (Định danh duy nhất):** Mỗi trường có một **mã định danh** không đổi, dùng để tham chiếu trường đó trong nhập/xuất dữ liệu và tích hợp. Mã định danh của một trường tùy biến không được trùng với mã định danh của trường chuẩn hoặc trường tùy biến khác trên cùng đối tượng.
- **BR-02.2 (Bảo toàn dữ liệu lịch sử khi sửa Dropdown):** Khi Admin sửa nhãn hoặc vô hiệu hóa một lựa chọn (Option) trong Dropdown:
  - Các bản ghi cũ đã lưu giá trị đó vẫn **giữ nguyên giá trị cũ ở chế độ chỉ đọc** để bảo toàn tính toàn vẹn của báo cáo lịch sử.
  - Lựa chọn bị vô hiệu hóa sẽ **không xuất hiện** trên form nhập liệu cho các thao tác tạo mới hoặc chỉnh sửa tiếp theo.
- **BR-02.3 (Vòng đời xóa trường):** "Xóa" một trường tùy biến trên giao diện quản trị thực chất là **vô hiệu hóa**: trường không còn xuất hiện với người dùng, nhưng dữ liệu đã nhập trên các bản ghi cũ **không bị mất vĩnh viễn** và vẫn phục vụ được nhu cầu tra cứu lịch sử. Mã định danh của trường đã vô hiệu hóa không được phép tái sử dụng cho trường mới, để một mã định danh luôn chỉ tương ứng với duy nhất một ý nghĩa nghiệp vụ trong suốt lịch sử dữ liệu của tenant.
  - **Hạn mức không bị chiếm dụng bởi trường đã vô hiệu hóa (`[Yêu cầu mới]`):** Trường đã vô hiệu hóa **không được tính vào hạn mức 300 trường** tại BR-02.6. Nếu tính vào, một tenant vận hành nhiều năm và điều chỉnh mô hình dữ liệu nhiều lần sẽ bị chặn tạo trường mới dù thực tế đang dùng rất ít trường — đây là kết cục không thể giải thích được với khách hàng.
- **BR-02.4 (Tính toàn vẹn của trường Công thức):** Giá trị trường Công thức luôn do hệ thống tự tính; không một kênh nào — người dùng nhập tay, nhập liệu từ file, tác vụ tự động hay tích hợp — được phép ghi đè giá trị này.
- **BR-02.5 (Xử lý cấu hình phụ thuộc khi xóa trường):** Một trường tùy biến có thể đang được tham chiếu bởi nhiều cấu hình khác nhau trong Object Manager. Hệ thống phân xử theo hai nhóm:
  - **Nhóm tự động dọn dẹp (tham chiếu mang tính hiển thị/kiểm tra):** Hệ thống tự động gỡ trường khỏi Phân quyền FLS (FEAT-03), Quy tắc kiểm tra dữ liệu (FEAT-04) và Danh sách hiển thị dùng chung (FEAT-08).
  - **Nhóm chặn xóa (tham chiếu mang tính điều kiện chặn nghiệp vụ) `[Yêu cầu mới]`:** Nếu trường đang được dùng làm **điều kiện bắt buộc để bản ghi tiến trình**, hệ thống **chặn thao tác xóa** và yêu cầu Admin gỡ trường khỏi các cấu hình đó trước, kèm danh sách cụ thể nơi đang tham chiếu. Các cấu hình thuộc nhóm này gồm: Trường bắt buộc theo giai đoạn vòng đời (BR-05.2), Ma trận chuyển đổi (BR-05.3), Điều kiện qua giai đoạn — Stage Gating (BR-07.3), và Điều kiện đóng thương vụ (BR-09.2).
  - **Lý do phân biệt:** Nếu tự động gỡ trường khỏi nhóm thứ hai, một điều kiện chặn nghiệp vụ sẽ **âm thầm biến mất** — Deal/Contact đột nhiên đi qua được giai đoạn mà lẽ ra phải bị chặn, làm mất kỷ luật quy trình mà không ai hay biết. Ngược lại, nếu để tham chiếu treo thì bản ghi bị kẹt vĩnh viễn ở giai đoạn đó. Cả hai kết cục đều không chấp nhận được, nên thao tác xóa phải bị chặn để Admin ra quyết định tường minh.
  - *(Lưu ý: Công cụ phân tích tác động chéo tới Automation/Email Template nằm ở Backlog tương lai — xem Mục 7)*.
- **BR-02.6 (Hạn mức cấu hình):** Mỗi đối tượng hỗ trợ tối đa **300 trường tùy biến**. Khi chạm ngưỡng 300, hệ thống chặn tạo mới và hiển thị cảnh báo rõ ràng ngay lập tức.

**Tiêu chí chấp nhận:**

- Tạo trường mới với đầy đủ thuộc tính xuất hiện ngay lập tức trên các giao diện nhập liệu và cấu hình liên quan.
- Vô hiệu hóa một lựa chọn Dropdown không làm mất giá trị đó trên các bản ghi lịch sử.
- Thao tác xóa trường dọn dẹp sạch sẽ các rule kiểm tra và phân quyền liên quan.
- Xóa một trường đang được dùng làm điều kiện Stage Gating hoặc điều kiện đóng thương vụ bị chặn lại, kèm danh sách chính xác nơi đang tham chiếu — không có tham chiếu treo và cũng không có điều kiện chặn nào biến mất âm thầm.

---

### FEAT-03 — Phân quyền trường (FLS) & Bố cục hiển thị theo nhóm `[Đã triển khai — riêng giao diện quản trị Bố cục form: Cần chuẩn hóa, xem BR-03.7]`

**Mô tả nghiệp vụ:** Cho phép quản trị viên kiểm soát chi tiết quyền xem, sửa, ẩn hoặc che dữ liệu nhạy cảm của từng trường theo từng Nhóm quyền, đồng thời tổ chức bố cục form nhập liệu hợp lý.

**Actor:** Quản trị viên Tenant (cấu hình); Người dùng cuối & Service Account (thụ hưởng/chịu tác động).

**Quy tắc nghiệp vụ:**

- **BR-03.1 (Hai chiều độc lập: Mức truy cập và Mức hiển thị):** Chính sách của một trường đối với một Nhóm quyền gồm **hai chiều tách biệt**, không được gộp thành một thang duy nhất:
  - **Chiều 1 — Mức truy cập (loại trừ nhau, chọn đúng một):** **Xem & Sửa** › **Chỉ xem** › **Ẩn** (hoàn toàn không thấy trường).
  - **Chiều 2 — Mức hiển thị giá trị (áp dụng khi mức truy cập không phải Ẩn):** **Hiện đầy đủ** › **Che một phần** (ví dụ chỉ hiện 4 ký tự cuối) › **Che hoàn toàn** (biết trường có dữ liệu nhưng không đọc được giá trị).
  - **Lý do tách hai chiều (`[Yêu cầu mới]`):** Che dữ liệu là *cách trình bày giá trị*, không phải *mức được phép làm gì với trường*. Nếu gộp chung, hai tổ hợp nghiệp vụ có thật sẽ không diễn đạt được: *(a)* nhân viên CSKH **được sửa** số thẻ khách hàng nhưng **không được đọc** giá trị cũ; *(b)* nhân viên kế toán **chỉ xem** nhưng được đọc **đầy đủ**. Việc gộp cũng làm nguyên tắc so sánh "Che mạnh hơn thắng Che yếu hơn" trở nên vô nghĩa vì không còn thang bậc để so.
- **BR-03.2 (Nguyên tắc giải quyết xung đột — Cấu hình hạn chế hơn luôn thắng):** Theo quyết định kiến trúc đã duyệt tại [ADR-0001](../docs/adr/0001-group-policy-conflict-resolution.md), hệ thống luôn chọn phương án an toàn nhất khi có mâu thuẫn, thay vì chọn phương án thuận tiện nhất cho người dùng:
  - Khi một người dùng thuộc nhiều Nhóm quyền có cấu hình khác nhau trên cùng một trường, **cấu hình hạn chế hơn luôn thắng, và hai chiều tại BR-03.1 được xét độc lập**: chiều Mức truy cập lấy giá trị hạn chế nhất trong các nhóm (*Ẩn thắng Chỉ xem, Chỉ xem thắng Xem & Sửa*), chiều Mức hiển thị cũng lấy giá trị hạn chế nhất (*Che hoàn toàn thắng Che một phần, Che một phần thắng Hiện đầy đủ*).
  - Ràng buộc "Bắt buộc nhập" được áp dụng theo cơ chế **cộng gộp**: nếu bất kỳ nhóm nào yêu cầu bắt buộc thì người dùng đó phải điền trường đó khi lưu.
  - **Thứ tự ưu tiên khi hai nguyên tắc trên xung đột — Quyền truy cập thắng Ràng buộc nhập (`[Yêu cầu mới]`):** Trường hợp một người dùng thuộc Nhóm A (cấu hình trường ở mức **Ẩn** hoặc **Chỉ xem**) và đồng thời thuộc Nhóm B (cấu hình trường đó là **Bắt buộc nhập**), hai nguyên tắc trên cho ra kết quả trái ngược nhau. Nguyên tắc xử lý: **mức truy cập hạn chế hơn luôn được áp dụng trước, và ràng buộc Bắt buộc nhập được miễn trừ đối với riêng người dùng đó** — hệ thống không bao giờ được yêu cầu người dùng nhập một trường mà chính họ không có quyền nhìn thấy hoặc không có quyền sửa. Bản ghi được gắn cờ thiếu dữ liệu theo cơ chế thống nhất tại BR-05.5. *(Điều khoản này được đặc tả tại [ADR-0001](../docs/adr/0001-group-policy-conflict-resolution.md), mục Bổ sung 2026-08-23 — mục 2, **đã được thông qua ngày 2026-08-24** theo issue [#29](https://github.com/crmsaassaudi/product-management/issues/29).)*
  - **Vắng mặt cấu hình không phải là sự cho phép (`[Yêu cầu mới]`):** Khi một người dùng thuộc nhiều Nhóm quyền nhưng chỉ **một số** nhóm có cấu hình cho trường đang xét, sự im lặng của các nhóm còn lại **không bao giờ được tính là "không hạn chế"**. Chỉ những nhóm thực sự có cấu hình cho **chính trường đó** mới tham gia phân giải. Nếu hiểu ngược lại, chỉ cần thêm một người vào một nhóm không cấu hình gì là vô hiệu hóa được hạn chế của nhóm chính họ — đúng lỗ hổng mà nguyên tắc hạn chế thắng sinh ra để bịt.
  - **Vị trí của Bố cục mặc định trong phân giải (`[Yêu cầu mới]`):** **Bố cục mặc định** là *phương án dự phòng*, không phải một chính sách ngang hàng: nó không được cộng vào cùng các nhóm khác, nhưng cũng không được bỏ qua chỉ vì người dùng có một nhóm nào đó có cấu hình. Phạm vi xét là **từng trường**: với mỗi trường, nếu người dùng không thuộc bất kỳ nhóm nào có cấu hình cho trường đó thì Bố cục mặc định quyết định trường đó; nếu có ít nhất một nhóm cấu hình trường đó thì Bố cục mặc định không tham gia. *Lý do nêu rõ phạm vi là từng trường:* nếu xét theo cả bố cục, một nhóm chỉ cần cấu hình **một** trường bất kỳ là hạn chế của Bố cục mặc định trên **mọi trường còn lại** bị gỡ bỏ cùng lúc — một sự mở rộng quyền không ai chủ ý và không nhìn thấy được trên màn hình cấu hình.
- **BR-03.2b (Phạm vi áp dụng FLS đối với Quản trị viên Tenant `[Yêu cầu mới]`):** Tenant Admin là người cấu hình FLS, nên phải trả lời rõ ràng câu hỏi: *chính Admin có bị FLS của mình ràng buộc không?* Nguyên tắc: **Tenant Admin không bị giới hạn bởi FLS** — vì họ có quyền tự sửa cấu hình để mở lại bất kỳ trường nào, việc áp FLS lên Admin chỉ tạo cảm giác an toàn giả mà không ngăn được gì. Hệ quả nghiệp vụ phải được nêu minh bạch trong hồ sơ bán hàng và triển khai: **FLS là công cụ phân tách trách nhiệm giữa các phòng ban, không phải công cụ che dữ liệu khỏi quản trị viên.** Khách hàng có yêu cầu giới hạn cả quản trị viên (thường thuộc ngành tài chính, y tế) cần được tư vấn giải pháp khác, không được cam kết bằng FLS.
- **BR-03.3 (Xem trước quyền thực tế — Effective Permissions Preview `[Yêu cầu mới]`):** Hệ thống cung cấp công cụ cho Admin nhập tên một người dùng cụ thể để xem trước bảng phân quyền trường thực tế mà người dùng đó đang nhận được sau khi hệ thống hợp nhất chính sách của tất cả các nhóm họ tham gia.
- **BR-03.4 (Ranh giới FLS đối với Tác vụ Tự động và Tích hợp bên ngoài `[Yêu cầu mới]`):**
  - Các tác vụ tự động chạy ngầm và các luồng tích hợp bên ngoài được **miễn trừ FLS** (đọc/ghi được mọi trường) nhằm đảm bảo quy trình đồng bộ dữ liệu không bị đứt đoạn chỉ vì chính sách hiển thị dành cho con người.
  - Các tác vụ này **vẫn phải tuân thủ nghiêm ngặt các Quy tắc kiểm tra dữ liệu (FEAT-04)**.
  - **Cấm luân chuyển dữ liệu sang trường có mức bảo vệ thấp hơn (`[Yêu cầu mới]`):** Quyền miễn trừ FLS **không được dùng để sao chép giá trị của một trường đang bị Ẩn/Che sang một trường khác có mức bảo vệ thấp hơn** (ví dụ tác vụ tự động đọc trường *Số CMND* đang bị che rồi ghi vào trường *Ghi chú* mà mọi người dùng đều xem được). Đây là đường vòng vô hiệu hóa toàn bộ FLS: dữ liệu nhạy cảm sau khi được sao chép sẽ mang chính sách truy cập của trường đích và **không thể thu hồi**. Hệ thống phải phát hiện và chặn cấu hình dạng này ngay tại thời điểm người dùng lưu quy trình tự động, đồng thời nêu rõ trường nguồn và trường đích gây vi phạm. Nguyên tắc nghiệp vụ: **mức bảo vệ của dữ liệu đi theo dữ liệu, không đi theo trường chứa nó.**
  - **Truy vết trách nhiệm (`[Yêu cầu mới]`):** Mỗi lần một tác vụ được miễn trừ FLS đọc/ghi vào trường đang bị Ẩn/Che, hệ thống phải truy vết được **danh tính người đã tạo/sở hữu quy trình tự động đó** — không chỉ ghi chung là "tác vụ hệ thống". Mục đích là quy được trách nhiệm khi dữ liệu nhạy cảm vô tình lộ ra ngoài phạm vi FLS qua một kênh khác (email nội bộ, thông báo ra hệ thống ngoài, tích hợp bên thứ ba). Năng lực ghi nhận này nằm ở nhật ký thao tác cấp bản ghi (xem Mục 1.2 — Ngoài phạm vi) và là điều kiện bắt buộc trước khi cam kết chuẩn bảo mật này với khách hàng Enterprise.
- **BR-03.5 (Phạm vi che chắn của trường Ẩn):** Trường bị Ẩn đối với một người dùng sẽ không được phép xuất hiện ở bất kỳ kênh nào người đó truy cập: Form chi tiết, Bảng danh sách, File xuất Excel/CSV, Báo cáo thống kê, Kết quả tìm kiếm toàn cầu, Xem trước phân đoạn khách hàng (Segment).
- **BR-03.6 (Bảo vệ giá trị bị che):** Khi người dùng lưu form có chứa chuỗi giá trị bị che (ví dụ: `****5678`), hệ thống bỏ qua và giữ nguyên giá trị gốc đã lưu, không ghi đè chuỗi che lên dữ liệu thật.
- **BR-03.7 (Bố cục form nhập liệu theo Nhóm quyền) `[Cần chuẩn hóa]`:** *(Quy tắc này bổ sung để lấp khoảng trống đặc tả: Bố cục form được nêu trong Phạm vi — Mục 1.2, trong Thuật ngữ — Mục 1.4 và trong tên của FEAT-03, nhưng trước đây không có quy tắc nghiệp vụ nào. **Đối chiếu hiện trạng hoàn tất ngày 2026-08-24 theo issue [#30](https://github.com/crmsaassaudi/product-management/issues/30): kết luận (b) — đã có nhưng khác chuẩn, nên giữ Bố cục form trong Phạm vi Mục 1.2.** Phần chuẩn nghiệp vụ đã hiện thực hóa và nghiệm thu được ở phía máy chủ; phần còn lệch là giao diện quản trị dành cho Tenant Admin — Mục 7.1 điểm 9 và issue [#57](https://github.com/crmsaassaudi/product-management/issues/57).)*
  - Admin cấu hình bố cục form nhập liệu cho từng đối tượng: chọn trường nào xuất hiện trên form, nhóm các trường thành **phần có tiêu đề**, và sắp thứ tự trường trong từng phần.
  - Có thể gán bố cục riêng cho từng Nhóm quyền. Người dùng không thuộc nhóm nào được gán bố cục riêng thì dùng **Bố cục mặc định** (Mục 1.4).
  - **Ranh giới với phân quyền trường — bố cục không bao giờ mở thêm quyền:** Bố cục quyết định *cách trình bày* (trường nằm ở phần nào, thứ tự nào); FLS quyết định *quyền* (được thấy, được sửa hay không). Khi hai thứ mâu thuẫn, **FLS luôn thắng**: một trường có mặt trên bố cục nhưng bị Ẩn với người dùng thì không hiển thị với người đó. Việc đưa một trường lên bố cục không bao giờ được hiểu là cấp quyền xem trường đó.
  - **Ràng buộc bắt buộc ở cấp bố cục:** Admin có thể đặt một trường là bắt buộc trên bố cục. Ràng buộc này **cộng gộp** với ràng buộc bắt buộc từ FLS, và cũng chịu nguyên tắc miễn trừ theo quyền truy cập tại BR-03.2 và BR-05.5 — không có ngoại lệ riêng cho bố cục.
  - **Trường mới được tạo:** Trường tùy biến mới **không tự động chèn vào giữa bố cục đang dùng**; hệ thống thêm vào cuối một phần do Admin chỉ định, để không làm thay đổi trật tự nhập liệu mà nhân viên đã quen mà không ai chủ ý.
  - **Gỡ trường khỏi bố cục không phải là xóa trường:** Thao tác này chỉ ẩn trường khỏi form nhập liệu; định nghĩa trường và dữ liệu đã lưu không bị ảnh hưởng, và trường vẫn có thể xuất hiện ở danh sách, báo cáo hay file xuất nếu FLS cho phép.

**Tiêu chí chấp nhận:**

- Người dùng thuộc nhóm bị ẩn trường không thể thấy hoặc truy vấn trường đó qua bất kỳ giao diện nào.
- Admin sử dụng công cụ Effective Permissions Preview kiểm tra được chính xác quyền của nhân viên thuộc nhiều nhóm.
- Một trường có mặt trên bố cục nhưng bị Ẩn với người dùng thì không hiển thị với người đó — bố cục không mở thêm quyền (BR-03.7).
- Tạo trường tùy biến mới không làm xáo trộn thứ tự trường trên bố cục đang dùng.
- Tác vụ Automation chạy ngầm ghi nhận dữ liệu vào trường bị ẩn với người dùng cuối một cách bình thường.
- Mọi hành động ghi dữ liệu vào trường bị Ẩn/Che thông qua Automation/API đều truy được về danh tính người tạo cấu hình Automation đó.
- Không thể lưu một cấu hình Automation sao chép giá trị từ trường bị Ẩn/Che sang trường có mức bảo vệ thấp hơn; hệ thống chặn và nêu rõ trường nguồn, trường đích vi phạm.

---

### FEAT-04 — Quy tắc kiểm tra dữ liệu (Validation Rules) `[Cần chuẩn hóa]`

**Mô tả nghiệp vụ:** Cho phép quản trị viên thiết lập các ràng buộc logic để đảm bảo tính đúng đắn và chuẩn hóa của dữ liệu khi nhập vào hệ thống.

**Actor:** Quản trị viên Tenant.

**Quy tắc nghiệp vụ:**

- **BR-04.1 (Các kiểu kiểm tra chuẩn):** Hệ thống hỗ trợ:
  - **Không được để trống:** Bắt buộc phải có giá trị.
  - **Đúng định dạng:** Giá trị phải khớp một khuôn dạng do Admin định nghĩa (Email, Số điện thoại quốc tế, Mã số thuế, địa chỉ web...).
  - **Nằm trong khoảng:** Giới hạn giá trị số tối thiểu và/hoặc tối đa.
- **BR-04.2 (Cấu hình sai phải bị chặn ngay khi lưu `[Cần chuẩn hóa]`):**
  - Hệ thống **bắt buộc kiểm tra tính hợp lệ của khuôn dạng và khoảng giá trị ngay tại thời điểm Admin lưu cấu hình**, và chỉ ra cụ thể chỗ sai để Admin sửa.
  - Một quy tắc mà hệ thống không thể đánh giá được (khuôn dạng viết sai, hoặc phức tạp tới mức gây rủi ro cho hiệu năng hệ thống) **phải bị từ chối ngay lúc lưu**.
  - **Nguyên tắc nghiệp vụ khi không đánh giá được quy tắc:** Nếu tới thời điểm người dùng cuối lưu bản ghi mà hệ thống vẫn không đánh giá được một quy tắc, hệ thống phải **từ chối bản ghi** chứ không được **âm thầm bỏ qua quy tắc và cho lưu**. Lý do: bỏ qua quy tắc tạo ra dữ liệu sai mà không ai biết — thiệt hại lớn hơn nhiều so với việc chặn một lần nhập liệu, vì dữ liệu sai sẽ chảy tiếp vào báo cáo và quyết định kinh doanh.
  - **Đường phục hồi bắt buộc (`[Yêu cầu mới]`):** Hai khoản trên đặt cạnh nhau tạo ra một trạng thái khóa chết nếu không nói rõ: nếu tập quy tắc của một đối tượng không đọc/không đánh giá được, mọi lệnh ghi lên đối tượng đó bị từ chối — và nếu chính màn hình cấu hình cũng bị chặn theo, tenant không còn cách nào tự sửa. Do đó:
    1. **Việc từ chối chỉ áp dụng cho lệnh ghi bản ghi nghiệp vụ**, không áp dụng cho việc đọc và sửa chính cấu hình quy tắc kiểm tra — Admin luôn vào được để sửa hoặc gỡ quy tắc gây lỗi.
    2. **Thông báo cho người dùng cuối phải phân biệt được** "dữ liệu bạn nhập chưa đúng quy tắc" với "hệ thống hiện không kiểm tra được quy tắc" kèm hướng dẫn liên hệ Admin — hai tình huống này đòi hai hành động hoàn toàn khác nhau, gộp chung thành một thông báo lỗi sẽ khiến người dùng sửa mãi một dữ liệu vốn không sai.
    3. Số lần phát sinh tình huống này là **dữ liệu đo bắt buộc** theo NFR-09 — một tenant liên tục rơi vào trạng thái này là dấu hiệu cấu hình sai chưa ai xử lý, không phải sự cố nhất thời.
- **BR-04.3 (Chỉ kiểm tra khi giá trị của trường thay đổi `[Yêu cầu mới]`):**
  - Một quy tắc kiểm tra chỉ được đánh giá khi: (1) bản ghi được **tạo mới**, hoặc (2) **giá trị của chính trường đó bị thay đổi** trong lần lưu này.
  - Nếu người dùng chỉnh sửa các trường khác trên một bản ghi cũ mà không động tới trường đang có dữ liệu chưa chuẩn hóa, hệ thống cho phép lưu bình thường. Lý do nghiệp vụ: một quy tắc mới ban hành hôm nay không được phép làm đình trệ toàn bộ công việc trên dữ liệu đã tồn tại từ trước.
  - **Đánh đổi có chủ đích (`[Ghi nhận rủi ro]`):** Cơ chế này chấp nhận rủi ro dữ liệu không đạt chuẩn tồn tại vô thời hạn nếu không ai chủ động sửa trường đó — đây là đánh đổi có chủ đích giữa tính liên tục của vận hành và tốc độ làm sạch dữ liệu, không phải khiếm khuyết. Để nợ dữ liệu không tồn đọng mãi, Admin cần có báo cáo "Bản ghi chưa đạt quy tắc kiểm tra hiện hành" để chủ động rà soát và khắc phục (xem Mục 7.2 — Phase 2).
- **BR-04.4 (Độc lập giữa Định dạng và Bắt buộc):** Quy tắc "Đúng định dạng" tự động bỏ qua nếu trường đang để trống (trừ khi trường đó đồng thời được cấu hình Bắt buộc nhập).
- **BR-04.5 (Thực thi nhất quán đa kênh):** Quy tắc kiểm tra áp dụng bình đẳng cho mọi kênh dữ liệu đi vào hệ thống: nhập trên máy tính, nhập trên điện thoại, nhập từ file, tác vụ tự động và tích hợp bên ngoài. Không kênh nào được có "cửa sau".

**Tiêu chí chấp nhận:**

- Admin không thể lưu một quy tắc có khuôn dạng viết sai; hệ thống chỉ rõ chỗ sai.
- Người dùng sửa thông tin người phụ trách trên một Contact cũ không bị chặn bởi quy tắc định dạng số điện thoại mới ban hành nếu họ không sửa trường số điện thoại đó.
- Nhập file Excel chứa dòng sai định dạng bị từ chối chính xác ở dòng đó với thông báo lỗi đã cấu hình.

---

### FEAT-05 — Giai đoạn vòng đời (Lifecycle Stages) & Chuyển đổi `[Đã triển khai]`

**Mô tả nghiệp vụ:** Định nghĩa các giai đoạn phát triển của khách hàng (hiện áp dụng cho Liên hệ) và chuẩn hóa quy trình chuyển đổi khách hàng tiềm năng thành Cơ hội bán hàng chính thức.

**Actor:** Quản trị viên Tenant (cấu hình); Người dùng cuối & Quản lý nhóm (chịu ràng buộc khi chuyển giai đoạn, xử lý bản ghi bị gắn cờ thiếu dữ liệu).

**Quy tắc nghiệp vụ:**

- **BR-05.1 (Chuỗi giai đoạn vòng đời):** Admin định nghĩa chuỗi giai đoạn vòng đời (ví dụ: *Khách tiềm năng → Đang chăm sóc → Đủ điều kiện (MQL) → Khách hàng chính thức → Rời bỏ*), kèm màu sắc nhận diện và thứ tự.
  - **Cho phép đi ngược và tái tiếp cận (`[Yêu cầu mới]`):** Thứ tự giai đoạn thể hiện **tiến trình kỳ vọng**, không phải đường một chiều. Liên hệ được phép **quay về giai đoạn trước** (khách hàng tạm dừng, nhu cầu chưa chín) và được phép **rời khỏi trạng thái Rời bỏ để vào lại vòng nuôi dưỡng** — tái tiếp cận khách hàng cũ là hoạt động kinh doanh cốt lõi trong B2B, hệ thống không được chặn. Khi Liên hệ quay lại, các trường bắt buộc của giai đoạn đích vẫn được áp dụng đầy đủ, và dữ liệu lịch sử của lần theo đuổi trước không bị xóa. *(Quy tắc này làm cho vòng đời Liên hệ nhất quán với quy tắc chuyển lùi giai đoạn của Cơ hội tại BR-07.4.)*
- **BR-05.2 (Trường bắt buộc theo giai đoạn):** Cho phép cấu hình danh sách trường bắt buộc phải có dữ liệu khi một Liên hệ chuyển tới một giai đoạn nhất định.
- **BR-05.3 (Ma trận Chuyển đổi Khách hàng tiềm năng — Conversion Matrix `[Cần chuẩn hóa]`):**
  - Khi một giai đoạn được đánh dấu là "Đã chuyển đổi" (Converted) và bật tùy chọn tự động sinh Cơ hội (Deal), Admin cấu hình trước ma trận chuyển đổi:
    1. **Mẫu tên Cơ hội mặc định:** Định dạng tự động, ví dụ: `{Contact_Name} - Cơ hội mới {Date}`.
    2. **Quy trình bán hàng đích:** Chọn Pipeline và Giai đoạn khởi đầu (Initial Stage) của Cơ hội được tạo.
    3. **Người phụ trách Cơ hội (Deal Owner):** Mặc định kế thừa từ Người phụ trách Liên hệ (Contact Owner) hoặc gán cho một người dùng/nhóm chỉ định. **Thứ tự ưu tiên khi tranh chấp với Phân công tự động (`[Yêu cầu mới]`):** Nếu đối tượng Cơ hội đồng thời có quy tắc Phân công tự động đang bật (BR-09.3), **cấu hình Deal Owner trong Ma trận chuyển đổi luôn thắng** — Cơ hội sinh ra từ chuyển đổi không được đưa vào vòng phân bổ Round Robin. Lý do nghiệp vụ: người đã nuôi dưỡng Liên hệ tới điểm chuyển đổi cần giữ được quyền theo đuổi thương vụ, tránh tranh chấp hoa hồng và mất ngữ cảnh khách hàng. Trường hợp Admin cố ý muốn Cơ hội chuyển đổi đi qua Round Robin, phải chọn tường minh tùy chọn *"Áp dụng quy tắc phân công tự động"* thay cho việc kế thừa chủ sở hữu.
    4. **Liên kết Tài khoản doanh nghiệp (Account):** Nếu Liên hệ đã gắn với một Account, Cơ hội tự động liên kết với Account đó; nếu chưa có Account, hệ thống cho phép tùy chọn tự động tạo Account doanh nghiệp tương ứng hoặc giữ độc lập.
    5. **Chống trùng lặp Cơ hội trên cùng Tài khoản (`[Yêu cầu mới]`):** Trước khi tự động sinh Cơ hội mới, hệ thống kiểm tra Tài khoản liên kết đã có Cơ hội nào đang **Đang mở (Open)** trong cùng Quy trình bán hàng đích hay chưa — nếu **có**, hệ thống không tự tạo Cơ hội trùng lặp mà cảnh báo cho Chủ sở hữu Tài khoản/Admin và cho phép chọn gắn Liên hệ mới vào Cơ hội đang mở đó, hoặc vẫn tạo mới có chủ đích (lưu vết quyết định); nếu **không có**, hệ thống tạo Cơ hội mới như bình thường.
       - **Vai trò liên hệ trong Cơ hội (Contact Role):** Khi gắn Liên hệ thứ hai trở đi vào một Cơ hội đang mở, quan hệ này phải mang **vai trò nghiệp vụ** (ví dụ: *Người quyết định, Người phê duyệt, Người ảnh hưởng, Người dùng cuối, Liên hệ phụ*) thay vì chỉ là một danh sách phẳng — giúp đội bán hàng nắm được sơ đồ ảnh hưởng bên trong tài khoản doanh nghiệp. *Ranh giới phạm vi: Object Manager chịu trách nhiệm cho phép Admin cấu hình danh mục vai trò này (như một danh mục dùng chung của tenant); còn việc lưu và hiển thị quan hệ Liên hệ–Cơ hội thuộc tầng lõi CRM, ngoài phạm vi tài liệu này (Mục 1.2).*
       - **Khi danh mục vai trò còn trống (`[Yêu cầu mới]`):** Danh mục này là quy tắc **chất lượng dữ liệu**, không phải quy tắc an toàn — nó tồn tại để báo cáo sơ đồ ảnh hưởng không bị phân mảnh bởi các cách gọi tên khác nhau. Vì vậy: tenant **chưa cấu hình** danh mục thì hệ thống **không chặn** việc gắn vai trò (chưa có chuẩn thì chưa có gì để đối chiếu); tenant **đã cấu hình** danh mục thì mọi vai trò nằm ngoài danh mục bị từ chối kèm danh sách vai trò hợp lệ. Không được làm ngược lại — chặn khi danh mục trống sẽ khiến mọi tenant mới không gắn được vai trò nào cho tới khi có người nhớ ra phải cấu hình danh mục trước.
- **BR-05.4 (Cảnh báo xung đột Ẩn vs Bắt buộc — Config-time):** Nếu một trường bị ẩn đối với một Nhóm quyền nhưng lại bị cấu hình bắt buộc ở một giai đoạn vòng đời, hệ thống hiển thị cảnh báo xung đột ngay tại màn hình cấu hình cho Admin, kèm danh sách Nhóm quyền bị ảnh hưởng.
- **BR-05.5 (Chống Deadlock giữa Ẩn và Bắt buộc — Runtime `[Yêu cầu mới]`):** Ràng buộc bắt buộc theo giai đoạn vòng đời (BR-05.2) không được phép ép người dùng nhập một trường mà FLS đang Ẩn hoặc Chỉ xem với họ (BR-03.1). Khi xảy ra xung đột này, hệ thống **miễn trừ ràng buộc bắt buộc đối với riêng người dùng đó** và cho phép lưu bản ghi bình thường; bản ghi được gắn cờ **"Thiếu dữ liệu bắt buộc do giới hạn quyền"**. Ràng buộc bắt buộc vẫn được áp dụng đầy đủ với bất kỳ người dùng nào có quyền nhập trường đó.
  - **Phạm vi áp dụng thống nhất:** Cờ này là cơ chế dùng chung cho mọi trường hợp miễn trừ vì lý do quyền truy cập — bao gồm cả xung đột nội tại của tầng FLS giữa nhiều Nhóm quyền (BR-03.2) và xung đột giữa FLS với Stage Gating của Cơ hội (BR-07.3). Cờ được mang trên **cả năm đối tượng nghiệp vụ cốt lõi** (Liên hệ, Tài khoản, Cơ hội, Ticket, Công việc — Mục 2.2), không riêng đối tượng có vòng đời: miễn trừ vì lý do quyền có thể xảy ra ở bất kỳ đối tượng nào có ràng buộc bắt buộc, và một đối tượng nằm ngoài phạm vi cờ chính là chỗ dữ liệu thiếu chảy đi không dấu vết.
  - **Quản trị bản ghi bị gắn cờ (`[Yêu cầu mới]`):** Cờ này chỉ có giá trị nghiệp vụ nếu có người chịu trách nhiệm xử lý. Do đó hệ thống phải đảm bảo: (1) bản ghi hiển thị cảnh báo trực quan nêu rõ **đang thiếu trường bắt buộc của giai đoạn nào**, chỉ đối với người dùng có quyền nhìn thấy trường đó; (2) tồn tại điều kiện lọc hệ thống theo trạng thái *"Thiếu dữ liệu bắt buộc do giới hạn quyền"* để dùng trong Shared List View (FEAT-08), cho phép Trưởng nhóm/Admin lọc ra toàn bộ bản ghi tồn đọng và giao người có thẩm quyền bổ sung; (3) không bản ghi nào bị gắn cờ mà không xuất hiện trong bộ lọc này.
  - **Chống tồn đọng vô thời hạn (`[Yêu cầu mới]`):** Một cờ không có người nhận trách nhiệm cụ thể sẽ tồn đọng vĩnh viễn — đúng bệnh lý đã được ghi nhận ở BR-04.3. Do đó: (1) khi bản ghi bị gắn cờ, hệ thống **gửi thông báo tới Nhóm quyền có quyền nhập trường còn thiếu** (cơ chế tương đương cảnh báo của Hàng đợi chưa phân công tại BR-09.3), không chỉ chờ ai đó tự vào lọc; (2) cờ được **tự động xóa ngay khi trường còn thiếu có giá trị hợp lệ**, không cần thao tác thủ công — tránh tình trạng dữ liệu đã đủ nhưng cờ vẫn treo làm sai lệch báo cáo tồn đọng.

**Tiêu chí chấp nhận:**

- Liên hệ chuyển sang giai đoạn có trường bắt buộc mà thiếu thông tin sẽ bị chặn lưu và chỉ rõ trường cần bổ sung.
- Khi Liên hệ đạt trạng thái "Đã chuyển đổi", một Cơ hội mới được tạo chính xác theo mẫu tên, đúng Pipeline, đúng giai đoạn và đúng người phụ trách đã cấu hình.
- Người dùng không có quyền xem một trường đang bắt buộc bởi giai đoạn vòng đời vẫn lưu được bản ghi bình thường (miễn trừ theo BR-05.5), không bị kẹt vĩnh viễn.
- Hai Liên hệ cùng một Tài khoản chuyển đổi gần nhau không sinh ra hai Cơ hội trùng lặp trong cùng Pipeline.

---

### FEAT-06 — Trạng thái & Nguồn theo đối tượng `[Cần chuẩn hóa]`

**Mô tả nghiệp vụ:** Quản lý danh mục trạng thái vận hành và nguồn gốc phát sinh dữ liệu cho từng đối tượng.

**Actor:** Quản trị viên Tenant.

**Quy tắc nghiệp vụ:**

- **BR-06.1 (Trạng thái phẳng cho Contact, Account, Ticket, Task):**
  - Các đối tượng Contact, Account, Ticket, Task sử dụng danh sách trạng thái phẳng. Mỗi trạng thái gồm: Tên hiển thị, Màu sắc, Thứ tự, Cờ đánh dấu "Mặc định khi tạo mới", và Cờ "Trạng thái đóng/kết thúc".
  - Chỉ có đúng 1 trạng thái mặc định cho mỗi đối tượng.
  - **Ý nghĩa của cờ "Trạng thái đóng/kết thúc" trong Phase 1 (`[Yêu cầu mới]`):** Cờ này **chỉ** phục vụ lọc danh sách và thống kê — phân biệt việc đang mở với việc đã xong; nó **không kéo theo bất kỳ ràng buộc dữ liệu bắt buộc nào** khi bản ghi chuyển sang trạng thái đó. Chỉ duy nhất đối tượng **Cơ hội** có điều kiện đóng cấu hình được (BR-09.2). Nêu rõ điều này để tránh hiểu nhầm rằng đóng một Ticket cũng bắt buộc nhập lý do như đóng một Cơ hội — năng lực đó hiện chưa có, xem Mục 7.1 điểm 10.
  - **Bảo toàn khi vô hiệu hóa trạng thái đang được sử dụng (`[Yêu cầu mới]`):** Trạng thái đang được gán cho bản ghi hiện hữu chỉ được **vô hiệu hóa** (không còn xuất hiện khi tạo mới/chỉnh sửa), không được xóa vĩnh viễn; các bản ghi cũ giữ nguyên trạng thái đó ở chế độ chỉ đọc để không làm sai lệch báo cáo lịch sử — áp dụng cùng nguyên tắc với BR-02.2. Trạng thái đang mang cờ Mặc định không được vô hiệu hóa trước khi Admin chỉ định trạng thái mặc định thay thế.
- **BR-06.2 (Trạng thái vĩ mô của Cơ hội — Deal System Status `[Cần chuẩn hóa]`):**
  - Riêng đối tượng **Cơ hội (Deal)**, trạng thái cấp hệ thống (System Status) chỉ gồm 3 nhóm trạng thái vĩ mô cố định:
    1. **Đang mở (Open):** Thương vụ đang trong quá trình tiếp cận/đàm phán.
    2. **Thắng (Closed Won):** Thương vụ thành công, chốt hợp đồng.
    3. **Thua (Closed Lost):** Thương vụ thất bại/hủy bỏ.
  - *Các giai đoạn chi tiết (Stages), tỷ lệ % thành công và thời gian xử lý của Deal được chuyển toàn bộ sang quản lý bên trong từng Pipeline tại FEAT-07*.
- **BR-06.3 (Nguồn phát sinh dữ liệu - Source):** Danh mục nguồn (Website, Hotline, Quảng cáo, Giới thiệu...) được quản lý độc lập cho từng đối tượng để phục vụ phân tích hiệu quả kênh. **Nguồn đang được gán cho bản ghi hiện hữu chỉ được vô hiệu hóa, không được xóa vĩnh viễn** — cùng nguyên tắc bảo toàn lịch sử như trạng thái (BR-06.1) và lựa chọn danh sách (BR-02.2). Nếu xóa hẳn, toàn bộ báo cáo hiệu quả kênh của các kỳ trước mất gốc so sánh.

**Tiêu chí chấp nhận:**

- Trạng thái và Nguồn mới tạo hiển thị đồng bộ trên các dropdown của đối tượng tương ứng.
- Đổi trạng thái mặc định tự động giải phóng cờ mặc định khỏi trạng thái cũ.
- Không thể vô hiệu hóa một trạng thái đang mang cờ Mặc định trước khi chỉ định trạng thái thay thế; bản ghi cũ giữ nguyên trạng thái đã vô hiệu hóa và báo cáo lịch sử không đổi số.
- Đóng một Ticket không bị hệ thống đòi thêm dữ liệu bắt buộc nào — cờ đóng chỉ tác động tới việc lọc và thống kê (BR-06.1).

---

### FEAT-07 — Quản lý Quy trình bán hàng & Giai đoạn Cơ hội (Deal Pipelines & Stages) `[Cần chuẩn hóa]`

**Mô tả nghiệp vụ:** Cho phép quản trị viên định nghĩa nhiều Quy trình bán hàng (Multi-Pipeline) độc lập cho các dòng sản phẩm hoặc kênh bán hàng khác nhau, và quản lý các Giai đoạn (Stages) chi tiết thuộc về từng Pipeline.

**Actor:** Quản trị viên Tenant (cấu hình Pipeline/Stage); Người dùng cuối (chịu ràng buộc Stage Gating, tiến trình tuần tự và quy tắc chuyển Pipeline khi vận hành Cơ hội).

**Quy tắc nghiệp vụ:**

- **BR-07.1 (Đa quy trình bán hàng - Multi-Pipeline):** Admin có thể tạo nhiều Pipeline (ví dụ: *Bán hàng Doanh nghiệp B2B, Bán hàng SMB, Gia hạn Hợp đồng/Upsell*). Mỗi Pipeline có tên, mô tả, màu sắc nhận diện, cờ "Mặc định", và cờ "Đã lưu trữ" (Archived).
- **BR-07.2 (Giai đoạn thuộc Pipeline — Pipeline Stages Architecture `[Cần chuẩn hóa]`):**
  - Mỗi Pipeline sở hữu một tập hợp các **Giai đoạn (Stages)** hoàn toàn độc lập.
  - Mỗi Stage trong một Pipeline được cấu hình các thuộc tính riêng biệt:
    1. **Tên giai đoạn:** (Ví dụ: *Tiếp cận, Demo giải pháp, Báo giá, Đàm phán*).
    2. **Thuộc nhóm Trạng thái vĩ mô:** Liên kết với *Đang mở (Open)*, *Thắng (Won)* hoặc *Thua (Lost)*.
    3. **Tỷ lệ thành công kỳ vọng (Probability %):** Từ 0% đến 100% (dùng để tính Doanh số dự báo trọng số = Giá trị Deal × Tỷ lệ %).
    4. **Thời gian lưu kỳ vọng (Expected Days / SLA):** Số ngày tối đa một deal nên ở giai đoạn này trước khi bị đánh dấu cảnh báo trễ hạn (Stale Deal).
- **BR-07.3 (Điều kiện qua giai đoạn — Stage Gating `[Yêu cầu mới]`):**
  - Cho phép Admin cấu hình các trường **bắt buộc phải có giá trị khi Deal chuyển vào một Stage cụ thể** (ví dụ: chuyển sang Stage "Báo giá" bắt buộc có *Giá trị cơ hội* và *Ngày dự kiến chốt*).
- **BR-07.4 (Quy tắc tiến trình tuần tự — Sequential Enforcement):**
  - Tùy chọn "Bắt buộc tuần tự" trên Pipeline: Nếu bật, Deal chỉ được chuyển tiến từng bước qua các giai đoạn liền kề, không được nhảy cóc.
  - Ngoại lệ: Hành động chuyển Deal sang trạng thái đóng (*Closed Won / Closed Lost*) hoặc chuyển lùi về giai đoạn trước luôn được phép thực hiện bất kể tùy chọn này. **Riêng khi chuyển thẳng sang Closed Won, điều kiện Stage Gating của các Stage bị bỏ qua vẫn được cộng dồn và kiểm tra theo BR-09.2 — ngoại lệ này chỉ miễn trừ ràng buộc tuần tự, không miễn trừ nghĩa vụ nhập dữ liệu.**
  - **Nhập liệu gộp một lần khi chốt nhanh (`[Yêu cầu mới]`):** Toàn bộ dữ liệu còn thiếu khi chốt Thắng nhanh (điều kiện đóng deal + Stage Gating cộng dồn của các giai đoạn bị bỏ qua) phải được yêu cầu **trong một lượt nhập liệu duy nhất**, không được buộc người dùng quay lại từng giai đoạn để điền rồi mới chốt được. Yêu cầu nghiệp vụ ở đây là *số lượt tương tác*, không phải hình thức giao diện cụ thể — thiết kế UI do đội Product Design quyết định.
- **BR-07.5 (Chuyển Cơ hội giữa các Quy trình bán hàng — Pipeline Switching `[Yêu cầu mới]`):** Do mỗi Pipeline có tập Stage hoàn toàn độc lập (BR-07.2), khi một Deal được chuyển từ Pipeline này sang Pipeline khác, Stage hiện tại của nó không còn tồn tại ở Pipeline đích. Quy tắc xử lý:
  - Hệ thống **bắt buộc yêu cầu người dùng chọn Stage đích** trong Pipeline mới, không được tự động gán ngầm (ví dụ tự đưa về Stage đầu tiên), vì thao tác gán ngầm sẽ làm sai lệch Doanh số dự báo trọng số do tỷ lệ % thành công của hai Stage khác nhau.
  - Nhóm Trạng thái vĩ mô (Open/Won/Lost) của Deal phải được bảo toàn: Deal đang mở chỉ được chuyển vào Stage thuộc nhóm *Đang mở* của Pipeline đích.
  - Dữ liệu đã nhập ở các trường Stage Gating của Pipeline cũ **không bị xóa**; nếu Pipeline đích không dùng những trường đó thì dữ liệu được giữ nguyên trên bản ghi để bảo toàn lịch sử (nguyên tắc NFR-06).
  - Việc chuyển Pipeline được ghi vào lịch sử của Deal (Pipeline cũ → Pipeline mới, Stage cũ → Stage mới, người thực hiện) để giải trình các biến động bất thường trong báo cáo dự báo doanh số.
- **BR-07.6 (Vòng đời của Pipeline & Stage khi đang có dữ liệu vận hành `[Yêu cầu mới]`):** Áp dụng nguyên tắc bảo toàn dữ liệu lịch sử tương đương BR-02.2/BR-02.3 cho Pipeline và Stage:
  - **Xóa/Vô hiệu hóa một Stage** đang có Deal ở trạng thái *Đang mở*: hệ thống **chặn thao tác** và yêu cầu Admin chỉ định Stage tiếp nhận để chuyển các Deal đó sang, nhằm tránh Deal bị treo ở một giai đoạn không còn tồn tại.
  - **Lưu trữ (Archive) một Pipeline** đang có Deal *Đang mở*: hệ thống cảnh báo rõ số lượng Deal bị ảnh hưởng và yêu cầu Admin chọn một trong hai hướng — chuyển toàn bộ Deal đang mở sang Pipeline khác (theo BR-07.5), hoặc chấp nhận để chúng ở chế độ **chỉ đọc, không thể chuyển giai đoạn tiếp**. Không được để tồn tại trạng thái mơ hồ thứ ba.
  - Pipeline đã lưu trữ **không xuất hiện** trong danh sách lựa chọn khi tạo Deal mới hoặc cấu hình Ma trận chuyển đổi (BR-05.3), nhưng Deal lịch sử và báo cáo cũ vẫn tham chiếu đúng tên Pipeline/Stage tại thời điểm phát sinh.
  - Pipeline đang mang cờ **"Mặc định"** không được phép lưu trữ trước khi Admin chỉ định một Pipeline mặc định khác.

**Tiêu chí chấp nhận:**

- Admin tạo được 2 Pipeline khác nhau với danh sách các Stage, tỷ lệ % và số ngày SLA hoàn toàn khác nhau.
- Deal chuyển qua Stage có yêu cầu bắt buộc trường sẽ bị chặn nếu chưa điền đủ dữ liệu.
- Lưu trữ (Archive) một Pipeline không làm mất dữ liệu lịch sử của các Deal đã từng thuộc Pipeline đó.
- Deal nhảy thẳng từ Stage đầu tiên sang Closed Won vẫn bị yêu cầu điền đủ các trường Stage Gating của những Stage đã bỏ qua trước khi lưu thành công; nhảy thẳng sang Closed Lost thì không. Toàn bộ các trường còn thiếu này được yêu cầu trong một lượt nhập duy nhất.
- Chuyển một Deal đang mở sang Pipeline khác luôn buộc người dùng chọn Stage đích; Doanh số dự báo trọng số sau khi chuyển khớp đúng tỷ lệ % của Stage mới.
- Không thể xóa một Stage đang có Deal mở nếu chưa chỉ định Stage tiếp nhận; không thể lưu trữ Pipeline đang mang cờ Mặc định.

---

### FEAT-08 — Danh sách hiển thị dùng chung (Shared List Views) `[Cần chuẩn hóa]`

**Mô tả nghiệp vụ:** Cho phép quản trị viên thiết kế sẵn các chế độ xem danh sách bản ghi chuẩn hóa (gồm bộ cột hiển thị, bộ lọc dữ liệu và thứ tự sắp xếp) và phân quyền áp dụng cho từng Nhóm quyền.

**Actor:** Quản trị viên Tenant (tạo & phân quyền); Người dùng cuối (sử dụng).

**Cấu phần của một Shared List View:**

1. **Bộ cột hiển thị (Columns):** Chọn các trường hiển thị, thứ tự cột kéo thả và độ rộng cột.
2. **Bộ lọc dữ liệu mặc định (Default Filter Criteria `[Cần chuẩn hóa]`):** Định nghĩa các điều kiện lọc sẵn (ví dụ: `[Chủ sở hữu = Người dùng hiện tại]`, `[Trạng thái = Đang mở]`, `[Thành phố = Hà Nội]`).
3. **Thứ tự sắp xếp mặc định (Default Sorting `[Cần chuẩn hóa]`):** Cấu hình trường sắp xếp và chiều sắp xếp (ví dụ: `Ngày tạo - Giảm dần` hoặc `Giá trị Deal - Giảm dần`).

**Quy tắc nghiệp vụ:**

- **BR-08.1 (Gán theo Nhóm quyền):** Danh sách hiển thị được gán làm mặc định cho một hoặc nhiều Nhóm quyền. Có thể chọn loại trừ một số người dùng cụ thể khỏi danh sách được gán. **Người dùng không thuộc bất kỳ Nhóm quyền nào được gán Shared List View (hoặc bị loại trừ khỏi tất cả) sẽ mặc định thấy danh sách hệ thống "All Records" (BR-08.3), không gặp màn hình trắng `[Yêu cầu mới]`.**
  - **Khi một người thuộc nhiều nhóm được gán các danh sách mặc định khác nhau (`[Yêu cầu mới]`):** Đây không phải xung đột quyền, nên **không** áp dụng nguyên tắc hạn chế hơn thắng của ADR-0001 — không có danh sách nào "an toàn hơn" danh sách nào. Nguyên tắc xử lý: **Admin phải xếp ưu tiên tường minh** giữa các danh sách được gán; hệ thống mở sẵn danh sách có ưu tiên cao nhất trong các nhóm mà người đó tham gia, **các danh sách còn lại vẫn khả dụng để người dùng tự chuyển sang**. Tuyệt đối không được chọn ngầu nhiên hoặc chọn theo thứ tự tạo — khi đó hai nhân viên cùng vai trò sẽ thấy hai màn hình khác nhau mà không ai giải thích được.
- **BR-08.2 (Tôn trọng phân quyền trường):** Danh sách hiển thị không bao giờ được mở thêm quyền. Hành vi được xác định dứt khoát theo từng chiều của BR-03.1, không để tùy chọn cách hiện thực:
  - Trường ở mức **Ẩn** với người dùng → **cột bị loại bỏ hoàn toàn** khỏi bảng của người đó, không được để cột trống. Lý do: theo BR-03.5 trường bị Ẩn không được xuất hiện ở bất kỳ kênh nào — một cột trống vẫn tiết lộ rằng trường đó tồn tại, bản thân điều đó đã là rò rỉ thông tin (ví dụ cột *Đang điều tra gian lận*).
  - Trường ở mức **Che một phần / Che hoàn toàn** → **cột vẫn hiển thị**, giá trị được che đúng mức đã cấu hình, và **không được dùng làm điều kiện lọc hay sắp xếp** cho người dùng đó — vì kết quả lọc/sắp xếp cho phép suy ra giá trị thật đang bị che.
- **BR-08.3 (Bảo vệ Danh sách hệ thống):** Danh sách hiển thị mặc định của hệ thống (All Records) không thể bị xóa.
- **BR-08.4 (Sao chép danh sách):** Cho phép sao chép một Shared List View có sẵn để tạo biến thể mới mà không làm thay đổi bản gốc.

**Tiêu chí chấp nhận:**

- Người dùng thuộc nhóm kinh doanh mở màn hình Cơ hội thấy ngay danh sách hiển thị được cấu hình riêng cho nhóm mình với đúng bộ lọc "Deal của tôi", đúng thứ tự cột và sắp xếp theo ngày chốt gần nhất.
- Cột chứa dữ liệu nhạy cảm tự động biến mất đối với nhân viên không có quyền FLS.
- Người dùng không thuộc nhóm nào được gán Shared List View vẫn thấy được danh sách "All Records" mặc định thay vì màn hình trắng.
- Cột ứng với trường bị Ẩn **biến mất hoàn toàn** khỏi bảng, không còn lại cột trống; cột ứng với trường bị che vẫn hiển thị nhưng không lọc/sắp xếp được theo cột đó (BR-08.2).
- Hai nhân viên thuộc cùng tập Nhóm quyền luôn thấy cùng một danh sách mở sẵn, xác định theo thứ tự ưu tiên Admin đã xếp (BR-08.1).

---

### FEAT-09 — Cấu hình nâng cao theo từng đối tượng (Advanced Settings) `[Cần chuẩn hóa]`

**Mô tả nghiệp vụ:** Cung cấp các thiết lập nghiệp vụ chuyên sâu cấp tenant cho từng đối tượng cốt lõi.

**Actor:** Quản trị viên Tenant.

**Quy tắc nghiệp vụ chi tiết:**

- **BR-09.1 (Chính sách chống trùng lặp — Deduplication):**
  - Cho phép cấu hình tiêu chí nhận diện trùng lặp: Khớp chính xác Email, Khớp số điện thoại sau khi đã chuẩn hóa về định dạng quốc tế, hoặc Khớp Mã số thuế/Tên công ty.
  - Hành động xử lý khi phát hiện trùng: Cảnh báo người dùng khi nhập tay trên biểu mẫu, hoặc từ chối tạo bản ghi khi nhập file hàng loạt và khi nhận từ tích hợp bên ngoài.
  - **Xử lý dòng bị từ chối (`[Yêu cầu mới]`):** Đối với Import file, các dòng bị từ chối do trùng lặp phải được liệt kê trong báo cáo kết quả Import (kèm lý do và ID bản ghi trùng) để Admin tải về đối chiếu, không được âm thầm bỏ qua. Đối với API, phản hồi phải **phân biệt được lỗi trùng lặp với các lỗi nghiệp vụ khác** và trả kèm **ID bản ghi gốc** cùng **tên trường gây trùng**, để hệ thống tích hợp có thể tự động chuyển sang luồng cập nhật bản ghi hiện có thay vì báo lỗi chung chung. *(Mã lỗi/HTTP status cụ thể thuộc tài liệu đặc tả API, không quy định trong SRS nghiệp vụ.)*
  - **Thứ tự thực thi so với Quy tắc kiểm tra dữ liệu (`[Yêu cầu mới]`):** Một dòng dữ liệu có thể đồng thời sai định dạng (FEAT-04) và trùng lặp. Hệ thống **kiểm tra Validation Rules trước, kiểm tra trùng lặp sau**, và báo cáo kết quả phải nêu **toàn bộ** lý do bị từ chối của dòng đó, không dừng ở lỗi đầu tiên — tránh việc người dùng phải sửa và import lại nhiều lượt cho cùng một dòng.
- **BR-09.2 (Điều kiện đóng thương vụ Cơ hội — Deal Close Requirements):**
  - **Khi đóng Thắng (Closed Won):** Bắt buộc phải có *Giá trị thực tế*, *Ngày chốt thực tế*, *Liên hệ chính*, **và toàn bộ trường Stage Gating (BR-07.3) của mọi Stage mà Deal đã bỏ qua trong Pipeline hiện tại `[Yêu cầu mới]`** — đảm bảo việc chuyển thẳng sang Closed Won (ngoại lệ tại BR-07.4) không trở thành cách né tránh nghĩa vụ nhập dữ liệu của quy trình bán hàng.
  - **Khi đóng Thua (Closed Lost):** Bắt buộc phải chọn *Lý do thất bại (Loss Reason)* và ghi chú nguyên nhân. Trường Stage Gating của các Stage trung gian bị bỏ qua **không bắt buộc** trong trường hợp này.
  - **Ba điều kiện đóng Thắng là bắt buộc vô điều kiện, không phải tùy chọn của tenant (`[Yêu cầu mới]`):** *Giá trị thực tế*, *Ngày chốt thực tế* và *Liên hệ chính* **luôn** được yêu cầu khi đóng Thắng; tenant **không** có công tắc bật/tắt từng điều kiện này. Lý do nghiệp vụ: cả ba là đầu vào của Doanh số thực thu, kỳ báo cáo và sơ đồ quan hệ khách hàng — cho phép tắt tức là cho phép một tenant tự vô hiệu hóa chính số liệu doanh thu của mình, và sai lệch chỉ lộ ra ở kỳ chốt sổ khi không còn sửa được. *(Đặc tả trước đây không nói rõ ba điều kiện này có cấu hình được hay không; hệ thống từng cho tenant tắt riêng từng cái. Khoản này chốt lại một cách làm duy nhất.)*
  - **Điều kiện có thể cấu hình:** Chỉ *"bắt buộc có Người phụ trách khi đóng"* là tùy chọn cấp tenant — đây là quy ước vận hành nội bộ về trách nhiệm, không phải dữ liệu cấu thành số liệu doanh thu.
- **BR-09.3 (Quy tắc phân công tự động — Auto-Assignment):**
  - Hỗ trợ cơ chế phân bổ xoay vòng chia đều (Round Robin), phân bổ theo tải hiện tại của từng người, và **phân bổ theo Khu vực địa lý (Territory)**.
  - **Hợp đồng cấu hình của phân bổ theo Khu vực (`[Yêu cầu mới]`):** Đặc tả trước đây chỉ nêu tên cơ chế này trong một câu, không nói khu vực của một bản ghi được xác định bằng gì và ai nhận bản ghi của khu vực nào — nên không thể hiện thực hóa và trên thực tế **năng lực này chưa tồn tại trong hệ thống** — issue [#51](https://github.com/crmsaassaudi/product-management/issues/51). Hợp đồng cấu hình được chốt như sau:
    1. **Trường xác định khu vực:** Với mỗi đối tượng áp dụng, Admin chỉ định **đúng một trường** trên bản ghi làm căn cứ xác định khu vực (ví dụ *Tỉnh/Thành*, *Quốc gia*, hoặc một trường tùy biến do tenant tự tạo). Không chỉ định trường này thì quy tắc phân bổ theo khu vực không được phép bật.
    2. **Danh mục Khu vực:** Mỗi Khu vực gồm tên khu vực, **tập giá trị nhận diện** của trường trên (ví dụ khu vực *Miền Bắc* nhận các giá trị *Hà Nội, Bắc Ninh, Hải Phòng…*), và **nhóm người tiếp nhận** bản ghi thuộc khu vực đó.
    3. **Một giá trị chỉ thuộc một khu vực:** Hệ thống **chặn ngay khi lưu** danh mục có một giá trị xuất hiện ở hai khu vực khác nhau, kèm chỉ rõ giá trị và hai khu vực đang tranh chấp — cùng nguyên tắc "cấu hình sai phải bị chặn ngay khi lưu" của BR-04.2. Lý do: một giá trị thuộc hai khu vực làm kết quả phân công phụ thuộc thứ tự đọc cấu hình, tức là hai bản ghi giống nhau có thể về hai người khác nhau mà không ai giải thích được.
    4. **So khớp không phân biệt chữ hoa/thường và khoảng trắng đầu cuối**, để dữ liệu nhập tay và dữ liệu nhập từ file cho cùng kết quả.
    5. **Chia việc bên trong một khu vực:** Sau khi xác định được khu vực, bản ghi được chia cho các thành viên khả dụng của nhóm tiếp nhận theo cùng cơ chế xoay vòng chia đều — hai cơ chế **bổ trợ nhau chứ không loại trừ nhau**: Khu vực quyết định *nhóm nào*, xoay vòng quyết định *ai trong nhóm đó*.
  - **Khi không xác định được khu vực (`[Yêu cầu mới]`):** Với phân bổ theo khu vực, bản ghi không có dữ liệu khu vực hoặc có khu vực chưa gán cho ai **không được âm thầm bỏ qua**: bản ghi được đưa vào Hàng đợi chưa phân công kèm cảnh báo, giống trường hợp hết người khả dụng bên dưới. Đây là nguyên nhân mất khách phổ biến nhất khi triển khai phân bổ theo khu vực.
  - Hệ thống chỉ phân công cho các thành viên trong nhóm đang ở trạng thái **Đang hoạt động (Active)** và không bật chế độ vắng mặt/nghỉ phép.
  - **Xử lý khi không còn thành viên khả dụng (`[Yêu cầu mới]`):** Nếu toàn bộ thành viên trong quy tắc phân bổ đều không khả dụng (nghỉ phép/không hoạt động), bản ghi được đưa vào **Hàng đợi chưa phân công (Unassigned Queue)** và gửi cảnh báo cho Trưởng nhóm/Admin, thay vì bị bỏ sót không ai xử lý.
- **BR-09.4 (Phạm vi áp dụng):** Các cấu hình nâng cao là cấu hình cấp toàn tenant, áp dụng đồng nhất cho mọi người dùng trong workspace.

**Tiêu chí chấp nhận:**

- Nhân viên cố tình đánh dấu Deal là "Closed Lost" mà không chọn Lý do thất bại sẽ bị hệ thống chặn lại và yêu cầu điền đầy đủ.
- Nhập liệu trùng số điện thoại đã tồn tại sẽ kích hoạt đúng cảnh báo chống trùng theo cấu hình.
- File Import chứa dòng trùng số điện thoại bị từ chối sẽ xuất hiện đầy đủ trong báo cáo kết quả Import kèm lý do, không bị bỏ sót âm thầm.
- Khi toàn bộ thành viên trong quy tắc Auto-Assignment đều nghỉ phép, bản ghi được đưa vào Hàng đợi chưa phân công và Trưởng nhóm nhận được cảnh báo.
- Bản ghi không xác định được khu vực cũng vào Hàng đợi chưa phân công, không bị bỏ qua không dấu vết (BR-09.3).
- Admin cấu hình phân bổ theo Khu vực: chỉ định trường xác định khu vực, khai báo tập giá trị cho từng khu vực và nhóm tiếp nhận; hệ thống chặn ngay khi lưu nếu một giá trị bị khai ở hai khu vực (BR-09.3).
- Hai bản ghi cùng khu vực nhưng khác cách viết hoa/khoảng trắng ở trường xác định khu vực vẫn về cùng nhóm tiếp nhận (BR-09.3).
- Đóng Thắng một Cơ hội thiếu Giá trị thực tế, Ngày chốt thực tế hoặc Liên hệ chính luôn bị chặn, không phụ thuộc cấu hình nào của tenant (BR-09.2).

---

### FEAT-10 — Nhật ký kiểm toán thay đổi cấu hình (Configuration Audit Trail) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động ghi lại lịch sử mọi thay đổi cấu hình trong Object Manager để phục vụ điều tra sự cố vận hành và bảo vệ tính minh bạch dữ liệu.

**Actor:** Hệ thống ghi tự động khi Tenant Admin thao tác. Đội hỗ trợ/Vận hành tra cứu qua công cụ nội bộ trong Phase 1.

**Quy tắc nghiệp vụ:**

- **BR-10.1 (Ghi nhận toàn diện):** Mọi hành động Tạo, Sửa, Xóa cấu hình trong Object Manager đều phải sinh ra đúng một bản ghi kiểm toán gồm: Thời gian thực hiện, Người thực hiện (Admin ID & Email), Loại hành động, và Đối tượng/Mục cấu hình bị tác động.
- **BR-10.2 (Lưu vết giá trị trước và sau đối với phân quyền trường):**
  - Riêng đối với các thay đổi thuộc nhóm **Phân quyền trường FLS** (đổi mức truy cập Xem/Sửa/Ẩn, bật/tắt Bắt buộc, đổi chế độ Che dữ liệu), nhật ký **bắt buộc phải lưu giá trị trước và sau (Old Value → New Value)** của thuộc tính bị đổi.
  - Đối với các cấu hình khác (tạo trường, đổi thứ tự, đổi màu sắc...), nhật ký lưu thông tin hành động và đối tượng bị tác động để tối ưu dung lượng lưu trữ.
- **BR-10.3 (Hành vi khi không ghi được nhật ký):** Object Manager áp dụng **nguyên tắc đóng đã chốt tại SRS IAM BR-41.4/BR-41.5** và [ADR-0003](../docs/adr/0003-permission-config-audit-log-fail-closed.md): *mọi thao tác làm thay đổi "ai được làm gì" hoặc "ai thấy gì" đều thuộc nhóm nhật ký thay đổi cấu hình quyền, mặc định là **thuộc nhóm này** trừ khi có lý do loại trừ tường minh.* Vì phân quyền trường (FLS) đúng nghĩa là *"ai thấy gì"*, các thao tác dưới đây thuộc nhóm **buộc phải có dấu vết**:
  - **Nhóm buộc phải có dấu vết — nếu không ghi được nhật ký thì hủy thao tác và báo lỗi cho Admin:** thay đổi mức truy cập hoặc mức hiển thị của trường theo Nhóm quyền (FEAT-03); bật/tắt ràng buộc bắt buộc; **vô hiệu hóa một trường (BR-02.3) và việc tự động gỡ trường khỏi cấu hình FLS kèm theo (BR-02.5)**; hoàn tác thay đổi phân quyền (BR-10.5). Lý do: một khoảng trống trong dấu vết *"ai đã mở/khóa quyền xem trường dữ liệu nhạy cảm nào, khi nào"* làm mất khả năng chứng minh trước khách hàng và kiểm toán viên. Đây là thao tác tần suất thấp, không nằm trên đường công việc của người dùng cuối, nên chi phí của việc chặn là chấp nhận được.
  - **Được loại trừ tường minh — ghi nhật ký không được chặn hay làm chậm Admin:** các thay đổi **chỉ mang tính trình bày và tổ chức**, không cấp thêm và không thu hồi quyền của bất kỳ ai: đổi màu, đổi thứ tự, đổi nhãn hiển thị, đặt tên quy trình bán hàng. Riêng **Danh sách hiển thị dùng chung (FEAT-08)** cũng được loại trừ, với lý do tường minh: theo BR-08.2 danh sách hiển thị luôn tuân thủ FLS, nó chỉ **sắp xếp lại thứ tự trình bày** những gì người dùng vốn đã được phép thấy, không mở thêm quyền — nên không thuộc phạm vi *"ai thấy gì"* theo nghĩa phân quyền.
  - **Xem trước không thuộc phạm vi:** Công cụ Xem trước quyền thực tế (BR-03.3) không tạo ra thay đổi có hiệu lực, nên không thuộc nhóm buộc phải có dấu vết — thống nhất với ngoại lệ dành cho thao tác mô phỏng tại BR-41.4.
  - **Nguyên tắc cho tính năng phát sinh sau này:** Mọi năng lực cấu hình mới bổ sung vào Object Manager **mặc định thuộc nhóm buộc phải có dấu vết** nếu nó tác động tới việc ai thấy gì hoặc ai làm được gì; muốn loại trừ thì phải nêu lý do tường minh ngay trong tài liệu, không được loại trừ bằng im lặng.
- **BR-10.4 (Quyền tra cứu trong Phase 1):** Trong Phase 1, nhật ký kiểm toán được lưu trữ tập trung phục vụ đội CSKH/Vận hành nội bộ điều tra khi khách hàng báo sự cố. *(Giao diện tự tra cứu dành riêng cho Tenant Admin được hoạch định cho gói Enterprise trong Phase tiếp theo — xem Mục 7)*.
- **BR-10.5 (Hoàn tác thay đổi phân quyền FLS — Revert `[Yêu cầu mới]`):** Một thao tác cấu hình FLS sai có phạm vi ảnh hưởng tức thời tới toàn bộ người dùng của tenant (ví dụ Admin ẩn nhầm 20 trường của Contact vào cuối ngày làm việc). Vì nhật ký đã lưu đầy đủ giá trị trước/sau (BR-10.2), hệ thống phải cho phép **hoàn tác một thay đổi phân quyền FLS về đúng giá trị trước đó** dựa trên bản ghi kiểm toán tương ứng, thay vì buộc Admin nhớ và dựng lại cấu hình cũ bằng tay.
  - Thao tác hoàn tác bản chất là một thay đổi phân quyền mới, do đó **cũng sinh bản ghi kiểm toán riêng** (ghi rõ là hành động hoàn tác và trỏ tới bản ghi gốc), không được ghi đè hay xóa lịch sử — và cũng thuộc nhóm buộc phải có dấu vết tại BR-10.3: nếu không ghi được nhật ký cho chính hành động hoàn tác thì việc hoàn tác bị hủy.
  - **Chỉ hoàn tác được thay đổi mới nhất của cùng một mục tiêu (`[Yêu cầu mới]`):** Nếu sau bản ghi được chọn còn có thay đổi phân quyền khác trên **cùng cặp (Nhóm quyền, Đối tượng, Trường)**, hệ thống **từ chối hoàn tác** và nêu rõ thay đổi mới hơn đó cùng người thực hiện. Lý do: hoàn tác một thay đổi cũ sẽ âm thầm xóa bỏ thay đổi mới hơn — đúng kiểu mất dữ liệu không ai biết mà NFR-07b tồn tại để ngăn, chỉ khác là xảy ra trong lúc đang sửa sai. Muốn quay về xa hơn thì hoàn tác lần lượt từ mới nhất trở về trước, mỗi bước đều để lại dấu vết riêng.
  - **Ngữ nghĩa khi giá trị trước là "không có" (`[Yêu cầu mới]`):** Hoàn tác một thay đổi **tạo mới** cấu hình cho một trường thì **gỡ bỏ** cấu hình đó, trả trường về mức mặc định của hệ thống; hoàn tác một thay đổi **gỡ bỏ** thì **khôi phục** lại đúng cấu hình cũ. Nêu rõ vì "về giá trị trước đó" khi giá trị trước đó là sự vắng mặt là chỗ dễ hiểu thành hai cách khác nhau.
  - Trong Phase 1, năng lực này được thực hiện bởi đội Vận hành nội bộ qua công cụ quản trị platform (nhất quán với BR-10.4).

**Tiêu chí chấp nhận:**

- Mỗi lần Admin đổi quyền một trường từ "Xem & Sửa" sang "Ẩn", hệ thống lưu lại đầy đủ bản ghi kiểm toán kèm giá trị cũ và mới.
- Đội hỗ trợ có thể trích xuất chính xác ai đã thực hiện thay đổi cấu hình vào thời điểm nào mà không cần tác động trực tiếp vào dữ liệu vận hành của khách hàng.
- Một thay đổi phân quyền FLS sai có thể được hoàn tác về đúng trạng thái trước đó dựa trên nhật ký, và bản thân hành động hoàn tác cũng được lưu vết.
- Hoàn tác một thay đổi đã bị một thay đổi mới hơn đè lên bị từ chối, kèm tên người và thời điểm của thay đổi mới hơn đó (BR-10.5).
- Hoàn tác một thay đổi vốn tạo mới cấu hình sẽ gỡ bỏ cấu hình đó chứ không để lại một mục rỗng (BR-10.5).

---

## 4. Yêu cầu phi chức năng (NFR)

### 4.1 Bảo mật & Phân quyền

- **NFR-01 (Cách ly dữ liệu giữa các khách hàng):** Mọi cấu hình trường tùy biến, phân quyền trường, quy tắc kiểm tra, quy trình bán hàng và danh sách hiển thị hoàn toàn cô lập theo từng tenant. Không tồn tại bất kỳ đường nào để cấu hình hoặc dữ liệu của một khách hàng bị nhìn thấy từ tenant khác.
- **NFR-02 (Quyền truy cập khu vực cấu hình):** Trong phạm vi một tenant, chỉ người mang vai trò Quản trị viên tenant mới truy cập được khu vực cấu hình Object Manager. Người dùng cuối không có đường nào tiếp cận, kể cả truy cập trực tiếp.
  - **Ngoại lệ duy nhất — Đội Vận hành nội bộ của nhà cung cấp (`[Yêu cầu mới]`):** Actor này (Mục 2.3) cần truy cập cấu hình của tenant để tra cứu nhật ký kiểm toán (BR-10.4) và hoàn tác cấu hình sai (BR-10.5) khi khách hàng báo sự cố. Đây là ngoại lệ **có kiểm soát**, không phải quyền mặc định: chỉ được cấp trong phạm vi và thời gian xử lý sự cố, và **mọi thao tác của actor này trên dữ liệu khách hàng đều phải được lưu vết** như một hành động cấu hình bình thường (BR-10.1), kèm định danh nhân sự thực hiện. Đây là câu hỏi bắt buộc phải trả lời được trong mọi vòng đánh giá bảo mật của khách hàng Enterprise: *"nhân sự của nhà cung cấp có xem được dữ liệu của chúng tôi không, và ai kiểm soát việc đó?"*
- **NFR-03 (Ưu tiên an toàn khi chồng lấn nhóm quyền):** Khi các Nhóm quyền chồng lấn và cho kết quả mâu thuẫn, hệ thống luôn chọn phương án hạn chế hơn thay vì phương án thuận tiện hơn, theo ADR-0001 và BR-03.2.

### 4.2 Toàn vẹn & Nhất quán dữ liệu

- **NFR-04 (Nhất quán đa kênh):** Phân quyền trường và quy tắc kiểm tra dữ liệu phải được thực thi đồng nhất trên mọi kênh dữ liệu đi vào hệ thống — nhập trên máy tính, nhập trên điện thoại, nhập từ file, tác vụ tự động và tích hợp bên ngoài.
- **NFR-05 (Không để cấu hình lỗi lọt xuống người dùng cuối):** Mọi cấu hình có dạng biểu thức (khuôn dạng kiểm tra, công thức tính) phải được xác nhận hợp lệ trước khi lưu. Cấu hình mà hệ thống không đánh giá được thì phải bị từ chối ngay tại màn hình quản trị, không được để phát tác thành lỗi cho người dùng cuối.
- **NFR-06 (Bảo vệ dữ liệu lịch sử):** Xóa trường hoặc vô hiệu hóa lựa chọn dropdown không bao giờ được phép làm sai lệch hoặc mất mát dữ liệu lịch sử đã lưu trên các bản ghi cũ.

### 4.3 Hiệu năng & Vận hành

- **NFR-07 (Tác vụ song song):** Hai Admin thao tác cấu hình trên hai đối tượng hoặc hai nhóm quyền khác nhau cùng lúc không được ghi đè hay làm mất cấu hình của nhau.
- **NFR-07b (Chống ghi đè khi sửa cùng một cấu hình `[Yêu cầu mới]`):** Trường hợp hai Admin mở và lưu **cùng một** cấu hình (cùng bảng FLS của một nhóm quyền, cùng một Pipeline, cùng một Shared List View), hệ thống **không được áp dụng cơ chế người lưu sau ghi đè toàn bộ người lưu trước** — thay đổi của người lưu trước sẽ biến mất âm thầm mà không ai biết. Hệ thống phải phát hiện cấu hình đã bị người khác thay đổi kể từ lúc mở và **cảnh báo cho người lưu sau trước khi ghi**, cho họ biết ai đã thay đổi và buộc xác nhận lại. Đây là rủi ro thực tế cao ở các tenant lớn có nhiều Admin cùng vận hành.
  - **Nội dung bắt buộc của cảnh báo (`[Yêu cầu mới]`):** Cảnh báo phải nêu **tên người đã thay đổi** và **thời điểm thay đổi** — issue [#53](https://github.com/crmsaassaudi/product-management/issues/53). Một cảnh báo chỉ nói "cấu hình đã bị thay đổi, hãy tải lại" không đủ dùng: Admin không biết nên hỏi ai, và trong tenant có nhiều Admin cùng vận hành thì việc tải lại rồi lưu đè lại chính là kết cục mà quy tắc này muốn ngăn.
  - **Đơn vị phiên bản của cấu hình (`[Yêu cầu mới]`):** Việc phát hiện xung đột được tính trên **từng đơn vị cấu hình độc lập**: một bảng FLS là một cặp *(Nhóm quyền × Đối tượng)*, một Pipeline, một Shared List View, một mục cấu hình nâng cao. Hai Admin sửa hai đơn vị khác nhau **không được** báo xung đột — nếu tính xung đột trên cả tenant, mọi Admin sẽ chặn nhau và tất cả sẽ học cách bỏ qua cảnh báo, đúng trạng thái mà NFR-07 cấm.
- **NFR-08 (Giới hạn quy mô an toàn):** Hệ thống đảm bảo thời gian tải trang danh sách và form nhập liệu dưới 1.5 giây đối với các đối tượng có tối đa 300 trường tùy biến.
  - **Bổ sung chiều Nhóm quyền (`[Yêu cầu mới]`):** Cam kết trên chỉ có ý nghĩa nếu ràng buộc được cả chiều thứ hai. Chi phí tính toán quyền thực tế của một người dùng tăng theo **số trường × số Nhóm quyền người đó tham gia** (do phải hợp nhất chính sách theo BR-03.2), chưa kể chi phí cộng dồn Stage Gating (BR-09.2). Vì vậy hệ thống công bố hạn mức: **một người dùng thuộc tối đa 20 Nhóm quyền** (tương tự hạn mức 300 trường tại BR-02.6). Cam kết 1.5 giây được đo ở điều kiện biên: đối tượng đạt trần 300 trường tùy biến **và** người dùng đạt trần 20 nhóm. Không có hạn mức này, cam kết hiệu năng không kiểm chứng được.
  - **Lý do chọn 20 là lý do nghiệp vụ, không phải giới hạn kỹ thuật:** Vượt ngưỡng này, quyền thực tế của một người trở thành thứ **không ai giải thích nổi bằng lời** — Admin không còn tự suy ra được vì sao nhân viên X không thấy trường Y, và công cụ Xem trước quyền thực tế (BR-03.3) trở thành bảng dữ liệu quá dài để đọc. Một người thuộc hơn 20 nhóm là dấu hiệu mô hình phân quyền của tenant cần tổ chức lại, không phải dấu hiệu hệ thống cần nâng trần. Khi chạm hạn mức, hệ thống chặn thêm nhóm và nêu rõ lý do kèm gợi ý tổ chức lại — cùng cách xử lý với BR-02.6.
- **NFR-09 (Khả năng đo lường — Instrumentation `[Yêu cầu mới]`):** Các chỉ số thành công của module (xem ranh giới tại Mục 1.2) chỉ tính toán được nếu hệ thống phát sinh sẵn dữ liệu đo. Do đó hệ thống phải ghi nhận được, ở mức tối thiểu: (1) mỗi thay đổi cấu hình kèm thời điểm và người thực hiện (đã có qua FEAT-10); (2) số lượng bản ghi đang mang cờ *"Thiếu dữ liệu bắt buộc do giới hạn quyền"* theo từng đối tượng và từng trường (BR-05.5); (3) số lần thao tác cấu hình bị hệ thống chặn vì xung đột (BR-02.5, BR-06.1, BR-07.6) — đây là tín hiệu cho thấy Admin đang gặp khó khi tự cấu hình; (4) số lần cảnh báo ghi đè cấu hình đồng thời được kích hoạt (NFR-07b). Yêu cầu ở đây là **dữ liệu đo phải tồn tại**; việc chọn ngưỡng mục tiêu và cách diễn giải thuộc tài liệu kế hoạch sản phẩm, không thuộc SRS.

---

## 5. Ma trận quyền truy cập tính năng

| Nhóm tính năng | Quản trị viên Tenant | Người dùng cuối | Tác vụ Tự động / API (Service Account) | Đội Vận hành nội bộ (SaaS) |
| --- | :---: | :---: | :---: | :---: |
| Xem & Cấu hình Đối tượng / Năng lực | ✅ | — | — | — |
| Tạo / Sửa / Xóa Trường tùy biến | ✅ | — | — | — |
| Cấu hình Phân quyền FLS & Layout | ✅ | — | — | — |
| Thao tác nhập liệu trên bản ghi | ✅ (không bị FLS giới hạn — BR-03.2b) | ✅ (tuân thủ FLS) | ✅ (được miễn trừ FLS) | — |
| Cấu hình Quy tắc kiểm tra (Validation) | ✅ | — | — | — |
| Kiểm tra dữ liệu khi lưu bản ghi | ✅ (tuân thủ Rule) | ✅ (tuân thủ Rule) | ✅ (tuân thủ Rule) | — |
| Cấu hình Pipeline & Deal Stages | ✅ | — | — | — |
| Cấu hình Shared List Views | ✅ | — | — | — |
| Sử dụng Shared List Views đã gán | — | ✅ | — | — |
| Cấu hình nâng cao (Trùng lặp, Phân bổ) | ✅ | — | — | — |
| Tra cứu Nhật ký kiểm toán cấu hình | — *(Phase 2: gói Enterprise)* | — | — (Hệ thống ghi) | ✅ (Phase 1) |
| Hoàn tác thay đổi phân quyền FLS (BR-10.5) | — | — | — | ✅ (Phase 1) |

---

## 6. Kịch bản chấp nhận tổng hợp (Acceptance Scenarios)

1. **Quy tắc kiểm tra được tôn trọng trên mọi kênh:** Admin đặt quy tắc Số điện thoại phải đúng định dạng quốc tế. Người dùng nhập sai trên giao diện bị báo lỗi ngay tại trường đó; file nhập liệu có dòng sai bị từ chối đúng ở dòng đó; tác vụ tự động ghi sai định dạng bị từ chối và lưu lại dấu vết lỗi để đối chiếu.
2. **Quy tắc mới không làm đình trệ công việc trên dữ liệu cũ:** Admin mới ban hành quy tắc bắt buộc nhập Mã số thuế cho Tài khoản. Khi nhân viên mở một Tài khoản cũ (chưa có MST) để cập nhật trường "Ghi chú", hệ thống cho phép lưu thành công mà không bắt buộc phải điền ngay MST.
3. **Phân tách Pipeline và Stages độc lập cho Deal:** Admin tạo Pipeline "Bán phần mềm B2B" (5 giai đoạn) và Pipeline "Gia hạn dịch vụ" (2 giai đoạn). Khi nhân viên chuyển qua lại giữa 2 Pipeline trên màn hình Kanban, các cột giai đoạn hiển thị chính xác theo từng quy trình riêng biệt kèm đúng tỷ lệ % thành công.
4. **Shared List View hiển thị chuẩn theo Nhóm:** Nhân viên kinh doanh mở danh sách Deal thấy view mặc định "Deal của tôi đang mở" với dữ liệu đã được lọc sẵn theo Chủ sở hữu và sắp xếp theo ngày đóng gần nhất.
5. **Bảo vệ dữ liệu nhạy cảm bị Ẩn:** Nhân viên không có quyền xem trường "Lương cơ bản" sẽ không thể thấy trường này trên Form, không thấy cột trên Danh sách, không thấy trong File xuất Excel và không thể tìm kiếm bản ghi bằng từ khóa lương.
6. **Xem trước quyền thực tế (Effective Preview):** Admin mở công cụ Preview cho nhân viên Nguyễn Văn A (thuộc cả nhóm Sales và nhóm Support), hệ thống hiển thị bảng tổng hợp chính xác các trường A được xem, bị ẩn hoặc bị che theo luật an toàn nhất.
7. **Không ai bị yêu cầu nhập trường mình không được thấy:** Trường "Hạn mức tín dụng" bị Ẩn với nhóm Sales nhưng được nhóm Kế toán cấu hình Bắt buộc. Nhân viên thuộc cả hai nhóm vẫn lưu được bản ghi (miễn trừ theo BR-03.2/BR-05.5); bản ghi bị gắn cờ thiếu dữ liệu và xuất hiện trong bộ lọc quản trị để Kế toán bổ sung — không ai bị kẹt, cũng không có dữ liệu nào bị bỏ quên.
8. **Chuyển Pipeline không làm méo dự báo doanh số:** Một Deal đang ở Stage "Đàm phán" (80%) của Pipeline B2B được chuyển sang Pipeline "Gia hạn dịch vụ". Hệ thống buộc chọn Stage đích thuộc nhóm Đang mở, dự báo trọng số cập nhật theo đúng tỷ lệ % của Stage mới, dữ liệu Stage Gating cũ vẫn còn trên bản ghi, và lịch sử ghi rõ ai đã chuyển.
9. **Hai Admin không âm thầm ghi đè nhau:** Admin A và Admin B cùng mở bảng FLS của nhóm Sales. A lưu trước; khi B bấm lưu, hệ thống cảnh báo cấu hình đã bị A thay đổi và buộc B xác nhận lại thay vì xóa trắng thay đổi của A.
10. **Hoàn tác cấu hình sai không cần dựng lại bằng tay:** Admin ẩn nhầm hàng loạt trường của Contact; đội Vận hành dùng nhật ký kiểm toán hoàn tác về đúng trạng thái trước đó, và hành động hoàn tác này cũng được lưu vết đầy đủ.

---

## 7. Hạn chế hệ thống Phase 1 & Phân kỳ lộ trình (Roadmap)

### 7.1 Hạn chế hệ thống trong Phase 1 (Căn cứ nghiệm thu QA)

Các giới hạn dưới đây là hiện trạng kỹ thuật của phiên bản Phase 1. Đội ngũ QA và Kỹ thuật căn cứ vào danh sách này để kiểm thử nghiệm thu:

1. **Chưa hỗ trợ Gộp trùng (Merge) cho Tài khoản (Account):** Hiện tại chỉ hỗ trợ gộp trùng lặp trên Liên hệ (Contact) và Ticket.
2. **Chưa hỗ trợ Cập nhật hàng loạt (Bulk Update) cho Contact và Ticket:** Thao tác sửa hàng loạt theo trường tùy ý hiện chỉ khả dụng trên Deal và Task.
3. **Chưa hỗ trợ Nhập file (Import) và Gắn thẻ hàng loạt (Bulk Tag) cho Task.**
4. **Chưa hỗ trợ Quy tắc kiểm tra liên trường do Admin tự định nghĩa (Cross-field Validation):** Trong **Quy tắc kiểm tra dữ liệu (FEAT-04)**, Admin chỉ cấu hình được ràng buộc trên từng trường độc lập; chưa thể tự viết điều kiện phụ thuộc giữa nhiều trường (ví dụ *"nếu Loại khách hàng = Doanh nghiệp thì Mã số thuế bắt buộc"*).
   - **Lưu ý phân biệt cho QA:** Hạn chế này **không áp dụng** cho các ràng buộc điều kiện đã được hệ thống xây dựng sẵn theo ngữ cảnh nghiệp vụ, vốn nằm trong phạm vi Phase 1 và phải hoạt động đầy đủ: Trường bắt buộc theo giai đoạn vòng đời (BR-05.2), Điều kiện qua giai đoạn — Stage Gating (BR-07.3), Điều kiện đóng thương vụ kèm cộng dồn Stage Gating (BR-09.2). Ranh giới ở đây là **ai định nghĩa điều kiện**: hệ thống định nghĩa sẵn theo ngữ cảnh (có trong Phase 1) so với Admin tự do định nghĩa biểu thức logic giữa các trường (chưa có trong Phase 1).
5. **Chưa có Vòng đời (Lifecycle Stages) độc lập cho Account:** Vòng đời khách hàng hiện chỉ áp dụng trên đối tượng Contact.
6. **Chưa mở Giao diện tra cứu Audit Trail cho Tenant Admin:** Nhật ký kiểm toán được lưu trữ phục vụ đội ngũ kỹ thuật/vận hành nội bộ tra cứu qua công cụ quản trị platform.
7. **Chưa có Thùng rác (Recycle Bin) và Engine phân tích tác động chéo khi xóa trường.**
8. **Chưa có môi trường thử nghiệm cấu hình (Sandbox / Staged Rollout):** Mọi thay đổi cấu hình của Tenant Admin có hiệu lực tức thời trên môi trường vận hành thật với toàn bộ người dùng, không có bước thử nghiệm hay triển khai theo từng nhóm. Rủi ro này được giảm nhẹ một phần nhờ Effective Permissions Preview (BR-03.3) và khả năng hoàn tác FLS (BR-10.5), nhưng chưa được loại bỏ hoàn toàn — cần lưu ý khi cam kết với khách hàng Enterprise có quy trình quản lý thay đổi (Change Management) nội bộ.
9. **Bố cục form nhập liệu chưa có màn hình quản trị (BR-03.7):** Toàn bộ chuẩn nghiệp vụ của BR-03.7 đã được hiện thực hóa và nghiệm thu được ở phía máy chủ — kể cả quy tắc quan trọng nhất, **bố cục không mở thêm quyền**, đã được kiểm chứng riêng ở cả hai đầu: bộ phân giải vẫn trả về Ẩn dù bố cục mô tả trường đầy đủ đến đâu, và phản hồi không mang trường đó ra ngoài. Thứ chưa có là **giao diện**: Tenant Admin hiện chỉ cấu hình bố cục và phần có tiêu đề qua giao diện tích hợp, nên năng lực này chưa tới tay người mà quy tắc trao nó cho — issue [#57](https://github.com/crmsaassaudi/product-management/issues/57), Mục 7.2 Sprint R3.
   - **Bố cục theo giai đoạn vòng đời** nằm ngoài phạm vi nghiệm thu BR-03.7: trường `visibleAtStages` lưu được nhưng **chưa có quy tắc nghiệp vụ nào đặc tả**, nên phải bổ sung đặc tả trước, không phải hiện thực hóa trước.

10. **Chưa có điều kiện đóng cấu hình được cho Ticket và các đối tượng ngoài Cơ hội:** Chỉ Cơ hội có điều kiện bắt buộc khi đóng (BR-09.2). Đóng một Ticket không bắt buộc nhập lý do hay kết quả xử lý, dù đây là yêu cầu phổ biến của đội CSKH và là căn cứ đo chất lượng hỗ trợ. Cần lưu ý khi tư vấn khách hàng có cam kết SLA hỗ trợ — để ngỏ sẽ dẫn tới Ticket đóng hàng loạt mà không ai biết đã xử lý thực sự hay chưa.

### 7.2 Phân kỳ lộ trình phát triển (Roadmap)

**Nguyên tắc phân kỳ:** Các hạng mục dưới đây được xếp theo **quan hệ phụ thuộc giữa các hạng mục**, không theo chủ đề nghiệp vụ. Lý do: gộp toàn bộ khối chuẩn hóa vào một sprint sẽ đặt một đợt chuyển đổi dữ liệu (Mục 7.3) cạnh nhiều luồng thay đổi độc lập khác — khi có sự cố sẽ không thể khoanh vùng nguyên nhân, và QA không có mốc nào để nghiệm thu từng phần.

- **Sprint R1 — Nền tảng dữ liệu Pipeline (điều kiện tiên quyết cho mọi hạng mục Deal):**
  - Hiện thực hóa mô hình hai chiều của phân quyền trường và thứ tự ưu tiên so với ràng buộc bắt buộc — issue [#29](https://github.com/crmsaassaudi/product-management/issues/29), theo [ADR-0001](../docs/adr/0001-group-policy-conflict-resolution.md) mục Bổ sung 2026-08-23. **Điều kiện tiên quyết này đã hoàn tất: phần Bổ sung được thông qua ngày 2026-08-24** (ADR-0001, `amendment_status: accepted`), và bốn điều khoản của nó đã được đưa vào phần thân đặc tả — mô hình hai chiều tại BR-03.1, thứ tự ưu tiên và nguyên tắc vắng mặt cấu hình tại BR-03.2, phạm vi chủ thể tại BR-03.2b và BR-03.4, cơ chế gắn cờ tại BR-05.5. Đây là nền tảng chung của **BR-05.5, BR-07.3 và BR-09.2** — cả ba cùng phụ thuộc một trục logic.
  - Tách hoàn toàn Deal Stages vào bên trong từng Pipeline (FEAT-06 & FEAT-07) **kèm đợt chuyển đổi dữ liệu theo Mục 7.3** — issue [#31](https://github.com/crmsaassaudi/product-management/issues/31).
  - Vòng đời Pipeline & Stage khi đang có Deal mở (BR-07.6) — issue [#32](https://github.com/crmsaassaudi/product-management/issues/32); quy tắc chuyển Deal giữa các Pipeline (BR-07.5) — issue [#33](https://github.com/crmsaassaudi/product-management/issues/33).
  - *Ghi chú phụ thuộc:* BR-07.5 và BR-07.6 phải ra cùng đợt với việc tách Stage. Nếu tách Stage mà chưa có BR-07.6, hệ thống ở trạng thái cho phép xóa Stage đang có Deal mở — tức là tự tạo ra dữ liệu mồ côi ngay trong sprint refactor.
- **Sprint R2 — Toàn vẹn dữ liệu & Kỷ luật quy trình:**
  - Chặn cấu hình kiểm tra sai ngay khi lưu và từ chối bản ghi khi không đánh giá được quy tắc (BR-04.2) — issue [#34](https://github.com/crmsaassaudi/product-management/issues/34); chỉ kiểm tra khi giá trị trường thay đổi (BR-04.3) — issue [#35](https://github.com/crmsaassaudi/product-management/issues/35).
  - Cộng dồn điều kiện Stage Gating khi chốt Thắng nhanh, kèm yêu cầu nhập gộp một lượt (BR-07.4 & BR-09.2) — issue [#36](https://github.com/crmsaassaudi/product-management/issues/36).
  - Chặn xóa trường đang được dùng làm điều kiện chặn nghiệp vụ (BR-02.5) — issue [#37](https://github.com/crmsaassaudi/product-management/issues/37); bảo toàn trạng thái/nguồn đang được sử dụng (BR-06.1, BR-06.3) — issue [#38](https://github.com/crmsaassaudi/product-management/issues/38).
  - Bổ sung Bộ lọc dữ liệu và Sắp xếp vào Shared List Views, kèm fallback "All Records" và quy tắc ưu tiên khi thuộc nhiều nhóm (FEAT-08) — issue [#39](https://github.com/crmsaassaudi/product-management/issues/39).
  - *Ghi chú phụ thuộc:* Bộ lọc của FEAT-08 phải có **trước** cờ thiếu dữ liệu ở Sprint R3 — cơ chế quản trị bản ghi bị gắn cờ (BR-05.5) được đặc tả dựa trên năng lực lọc của Shared List View.
- **Sprint R3 — An toàn vận hành & Vùng đệm nghiệp vụ:**
  - Công cụ Effective Permissions Preview (BR-03.3) — issue [#40](https://github.com/crmsaassaudi/product-management/issues/40).
  - Miễn trừ ràng buộc bắt buộc theo quyền truy cập khi người dùng lưu bản ghi, cờ thiếu dữ liệu kèm thông báo và tự động xóa cờ (BR-03.2, BR-05.5) — issue [#41](https://github.com/crmsaassaudi/product-management/issues/41).
  - Truy vết danh tính tác vụ tự động và chặn luân chuyển dữ liệu sang trường bảo vệ thấp hơn (BR-03.4) — issue [#42](https://github.com/crmsaassaudi/product-management/issues/42); điều kiện tiên quyết trước khi cam kết chuẩn bảo mật với khách hàng Enterprise.
  - Chống ghi đè khi hai Admin sửa cùng một cấu hình (NFR-07b) — issue [#43](https://github.com/crmsaassaudi/product-management/issues/43); hoàn tác phân quyền trường qua nhật ký (BR-10.5) — issue [#44](https://github.com/crmsaassaudi/product-management/issues/44).
  - Chống trùng lặp Cơ hội khi chuyển đổi, thứ tự ưu tiên chủ sở hữu, danh mục Vai trò liên hệ (BR-05.3) — issue [#45](https://github.com/crmsaassaudi/product-management/issues/45).
  - Hàng đợi chưa phân công, gồm cả trường hợp không xác định được khu vực (BR-09.3) — issue [#46](https://github.com/crmsaassaudi/product-management/issues/46); báo cáo Import nêu đầy đủ mọi lý do từ chối của một dòng (BR-09.1) — issue [#47](https://github.com/crmsaassaudi/product-management/issues/47).
  - Nhóm thao tác buộc phải có dấu vết cho nhật ký cấu hình (BR-10.3) — issue [#48](https://github.com/crmsaassaudi/product-management/issues/48).
  - Dữ liệu đo phục vụ theo dõi hiệu quả module (NFR-09) — issue [#49](https://github.com/crmsaassaudi/product-management/issues/49).
  - Hạn mức 20 Nhóm quyền cho một người dùng và cam kết hiệu năng ở điều kiện biên (NFR-08) — issue [#50](https://github.com/crmsaassaudi/product-management/issues/50).
  - Phân bổ theo Khu vực địa lý kèm hợp đồng cấu hình (BR-09.3) — issue [#51](https://github.com/crmsaassaudi/product-management/issues/51); tách từ #46 vì #46 chỉ giải quyết nửa hàng đợi, nửa định tuyến theo khu vực chưa tồn tại.
  - Áp bộ lọc & sắp xếp của Shared List View cho Cơ hội, Tài khoản, Ticket, Công việc (FEAT-08) — issue [#52](https://github.com/crmsaassaudi/product-management/issues/52); tách từ #39 vì #39 chỉ đấu dây đối tượng Liên hệ.
  - Cảnh báo xung đột cấu hình nêu ai đã thay đổi và khi nào (NFR-07b) — issue [#53](https://github.com/crmsaassaudi/product-management/issues/53); tách từ #43 vì #43 chỉ làm phần phát hiện xung đột.
  - Vận hành đợt chuyển đổi Stage vào Pipeline: MIG-04, MIG-05, MIG-06 — issue [#54](https://github.com/crmsaassaudi/product-management/issues/54); tách từ #31 vì đây là điều kiện nghiệm thu vận hành, không phải mã nguồn.
  - Giao diện quản trị Bố cục form và phần có tiêu đề cho Tenant Admin (BR-03.7) — issue [#57](https://github.com/crmsaassaudi/product-management/issues/57); tách từ #30 sau khi đối chiếu kết luận **(b) đã có nhưng khác chuẩn**: máy chủ đã đúng chuẩn, phần còn lệch là năng lực này chưa tới tay người mà quy tắc trao nó cho.
- **Phase 2 (Mở rộng năng lực dữ liệu):**
  - Bổ sung năng lực Merge cho Account và Bulk Update cho Contact/Ticket (FEAT-01).
  - Bổ sung Quy tắc kiểm tra liên trường (Cross-field / Conditional Validation).
  - Mở màn hình tra cứu Configuration Audit Trail trên UI dành riêng cho các gói dịch vụ Enterprise.
  - Báo cáo "Bản ghi chưa đạt quy tắc kiểm tra hiện hành" để Admin chủ động rà soát và khắc phục nợ dữ liệu tồn đọng theo BR-04.3.
- **Phase 3 (Nâng cao & Tối ưu hóa):**
  - Xây dựng Lifecycle Stages độc lập cho Account trong mô hình Account-Based Marketing (ABM).
  - Thùng rác phục hồi trường tùy biến đã vô hiệu hóa, và công cụ cảnh báo tác động chéo trước khi Admin gỡ bỏ một định nghĩa trường.
  - Môi trường thử nghiệm cấu hình và triển khai theo từng nhóm (Sandbox / Staged Rollout) phục vụ khách hàng Enterprise có quy trình Change Management nội bộ.

### 7.3 Yêu cầu chuyển đổi dữ liệu khi Refactor (Data Migration) `[Yêu cầu mới]`

Các hạng mục mang nhãn `[Yêu cầu chuẩn hóa / Refactor]` không phải là tính năng mới trên dữ liệu trắng — chúng thay đổi cấu trúc của dữ liệu **đang vận hành thật của khách hàng**. Đặc biệt việc tách Deal Stages vào bên trong từng Pipeline (FEAT-06 & FEAT-07) buộc mọi Cơ hội hiện hữu phải được ánh xạ lại từ danh sách Stage phẳng cũ sang cặp *(Pipeline, Stage)* mới. Một đợt chuyển đổi sai sẽ làm sai lệch Pipeline và báo cáo dự báo doanh số của **toàn bộ tenant cùng lúc** — đây là rủi ro nghiệp vụ lớn nhất của cả lộ trình, lớn hơn bất kỳ lỗi tính năng đơn lẻ nào. Do đó các yêu cầu sau là điều kiện nghiệm thu bắt buộc:

- **MIG-01 (Quy tắc ánh xạ tường minh):** Phải có quy tắc ánh xạ được duyệt trước cho *mọi* Stage đang tồn tại của từng tenant sang cặp (Pipeline, Stage) mới, bao gồm cả các Stage đã không còn sử dụng nhưng vẫn được Deal lịch sử tham chiếu. Không Deal nào được kết thúc đợt chuyển đổi ở trạng thái không xác định Pipeline.
- **MIG-02 (Bảo toàn nhóm Trạng thái vĩ mô):** Deal đang *Đang mở* phải nằm ở Stage thuộc nhóm Đang mở của Pipeline đích; Deal đã *Thắng/Thua* phải giữ nguyên kết quả — đợt chuyển đổi tuyệt đối không được làm một Deal đã chốt trở lại trạng thái đang mở, hoặc ngược lại.
- **MIG-03 (Đối chiếu trước và sau):** Trước khi công bố hoàn tất, phải đối chiếu và khớp đúng: tổng số Deal theo từng nhóm trạng thái, tổng giá trị Pipeline, và tổng Doanh số dự báo trọng số. Sai lệch ở chỉ số dự báo trọng số là **được phép** khi tỷ lệ % của Stage mới khác Stage cũ, nhưng phải được giải thích và duyệt trước, không được phát hiện sau khi khách hàng phản ánh.
- **MIG-04 (Hành vi trong thời gian chuyển đổi):** Phải xác định rõ trạng thái hệ thống trong đợt chuyển đổi — người dùng cuối bị chặn thao tác trên Cơ hội, hay hệ thống vẫn cho ghi. Không được để tồn tại khoảng thời gian mà người dùng chuyển giai đoạn Deal trong khi dữ liệu đang được ánh xạ.
- **MIG-05 (Khả năng khôi phục):** Phải có phương án đưa dữ liệu về đúng trạng thái trước đợt chuyển đổi nếu phát hiện sai sót, và phương án này phải được diễn tập trước trên bản sao dữ liệu thật, không chỉ tồn tại trên giấy.
- **MIG-06 (Thông báo cho khách hàng):** Tenant Admin phải được thông báo trước về thay đổi cấu trúc Pipeline và được cung cấp bản đối chiếu Stage cũ → Stage mới của chính tenant mình, vì đây là thay đổi họ sẽ thấy ngay trên màn hình Kanban và báo cáo.

- **MIG-07 (Tương thích của cấu hình phân quyền trường đang tồn tại với mô hình hai chiều — BR-03.1 `[Yêu cầu mới]`):** Mô hình hai chiều tại BR-03.1 là cách **diễn đạt lại** chính sách đã có, không phải một cấu trúc mới thay thế nó: chiều *Mức hiển thị giá trị* vốn đã được lưu tách bạch, còn chiều *Mức truy cập* ở các cấu hình lưu trước khi chốt mô hình được diễn đạt bằng hai dấu hiệu riêng lẻ (*trường có hiện trên form hay không* và *trường có bị khóa sửa hay không*) thay vì một mức duy nhất. Vì vậy **không yêu cầu ghi lại dữ liệu cấu hình của tenant** — nhưng kèm hai điều kiện nghiệm thu bắt buộc:
  - **Đọc hiểu được cách diễn đạt cũ ở mọi nơi có hiệu lực:** Mọi điểm hệ thống *thi hành* phân quyền trường (lưu bản ghi, trả dữ liệu ra, danh sách, tìm kiếm, báo cáo, file xuất) phải hiểu cách diễn đạt cũ **giống hệt** màn hình cấu hình đang hiển thị cho Admin. Một cấu hình cũ được màn hình đọc là *Chỉ xem* nhưng nơi thi hành đọc thành *Xem & Sửa* là lỗi lộ quyền: Admin nhìn thấy một chính sách, hệ thống thi hành một chính sách khác, và không có gì trên màn hình cho thấy sự khác biệt đó.
  - **Chuyển đổi cách hiểu không bao giờ được nới quyền:** Kết quả đọc một cấu hình lưu theo cách cũ phải **hạn chế bằng hoặc hơn** điều Admin đã thấy khi lưu nó. Khi cách diễn đạt cũ không đủ dữ kiện để xác định chắc chắn một mức, hệ thống chọn mức hạn chế hơn.

*Ghi chú phạm vi: Mục này đặc tả **yêu cầu nghiệm thu nghiệp vụ** đối với đợt chuyển đổi. Cách thức thực thi, thời điểm và phân công thuộc kế hoạch triển khai của đội Kỹ thuật — theo dõi tại issue [#54](https://github.com/crmsaassaudi/product-management/issues/54).*

---

## Phụ lục A — Ghi chú kỹ thuật tham chiếu (không ràng buộc)

Phụ lục này **không phải là đặc tả** và **không dùng làm căn cứ nghiệm thu**. Mục đích duy nhất là giúp đội phát triển và QA đối chiếu các quy tắc nghiệp vụ ở phần thân tài liệu với thuật ngữ kỹ thuật thông dụng trong ngành. Khi Phụ lục A và phần thân tài liệu khác nhau, **phần thân tài liệu thắng** (Nguyên tắc 2).

Lý do tách ra: phần thân tài liệu cần đọc được và phản biện được bởi Product Owner, Customer Success và chính khách hàng doanh nghiệp — những người quyết định *thế nào là đúng nghiệp vụ*. Nếu trộn thuật ngữ kỹ thuật vào quy tắc nghiệp vụ, tài liệu sẽ mất đúng nhóm người đọc quan trọng nhất, và tệ hơn: một lựa chọn kỹ thuật sẽ được mặc nhiên coi là yêu cầu nghiệp vụ mà không ai còn chất vấn được nữa.

| Quy tắc nghiệp vụ | Thuật ngữ / cơ chế kỹ thuật thường dùng tương ứng |
| --- | --- |
| BR-02.1 — Mã định danh trường | API Name / field key |
| BR-02.3 — "Xóa" trường là vô hiệu hóa, dữ liệu lịch sử không mất | Soft delete |
| BR-03.1 — Hai chiều Mức truy cập và Mức hiển thị | Field-Level Security + data masking policy (hai thuộc tính độc lập) |
| BR-03.2 — Hạn chế hơn thắng; vắng mặt cấu hình không phải sự cho phép | Deny-override áp dụng độc lập trên từng chiều; xem ADR-0001 mục Bổ sung 2026-08-23 |
| BR-03.4 — Miễn trừ FLS cho tác vụ tự động | Service account với quyền bypass FLS |
| BR-04.1 — Kiểm tra "Đúng định dạng" | Regular expression (regex) |
| BR-04.2 — Từ chối bản ghi khi không đánh giá được quy tắc | Fail-closed; kiểm tra rủi ro biểu thức (ReDoS) tại thời điểm lưu |
| BR-04.3 — Chỉ kiểm tra khi giá trị trường thay đổi | Điều kiện dạng `ISCHANGED(field)` |
| BR-09.1 — Chuẩn hóa số điện thoại về định dạng quốc tế | E.164 |
| BR-10.2 — Lưu giá trị trước và sau | Snapshot diff / before-after audit record |
| BR-10.3 — Nhóm "buộc phải có dấu vết" vs nhóm không chặn | Fail-closed vs fail-open audit logging; xem ADR-0003 và SRS IAM BR-41.4/41.5 |
| NFR-07b — Cảnh báo khi cấu hình đã bị người khác thay đổi | Optimistic locking / version check |
| Mục 7.3 — Yêu cầu chuyển đổi dữ liệu | Data migration; MIG-05 tương ứng rollback plan |
