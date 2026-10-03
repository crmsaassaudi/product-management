# SRS — Phân hệ Quản lý Khách hàng & Danh bạ Doanh nghiệp (Contacts & Accounts Management)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA |
| **Module** | CRM — Phân hệ Quản lý Khách hàng & Danh bạ Doanh nghiệp (Contacts & Accounts Management) |
| **Ngày cập nhật** | 2026-10-02 |
| **Phiên bản** | v7.3 (Chuẩn hóa Nghiệp vụ Thuần túy — Thay thế v6.4; đồng bộ mô hình quyền với Phân quyền Workspace v5.0: năm mức truy cập, vai trò dựng sẵn, quyền quản trị của phân hệ, hàng đợi và đơn vị tiếp nhận khách hàng tiềm năng, khung che dữ liệu nhạy cảm, tạm ngưng và rời workspace của nhân sự) |
| **Neo mã nguồn** | Chưa xác định — tài liệu đặc tả trạng thái nghiệp vụ mục tiêu, không neo vào một phiên bản triển khai cụ thể |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`object-manager-srs.md`](./object-manager-srs.md), [`omnichat-srs.md`](./omnichat-srs.md), [`onboarding-srs.md`](./onboarding-srs.md), [`deals-pipeline-srs.md`](./deals-pipeline-srs.md), [`tickets-srs.md`](./tickets-srs.md), [`tasks-srs.md`](./tasks-srs.md), [`campaigns-srs.md`](./campaigns-srs.md), [ADR-0007](../docs/adr/0007-record-sharing-and-permission-precedence-contract.md), [ADR-0008](../docs/adr/0008-data-subject-deletion-contract-omnichat-tickets.md), [ADR-0009](../docs/adr/0009-access-level-per-action-and-record-type.md), [ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md) |

## Ghi chú về phiên bản v7.0

Phiên bản này **viết lại toàn bộ** tài liệu theo đúng vai trò của một SRS nghiệp vụ, thay thế v6.4. Toàn bộ nội dung nghiệp vụ của v6.4 — 36 tính năng, các quy tắc nghiệp vụ, danh mục dữ liệu chuẩn, danh mục tham số cấu hình, ma trận phân quyền, kịch bản chấp nhận và chỉ số thành công — được giữ nguyên về bản chất; thay đổi nằm ở văn phong, cấu trúc và tính kiểm chứng được.

**Nguyên tắc biên soạn — tài liệu là chuẩn, không phải bản ghi chép hiện trạng:**

Tài liệu này đặc tả **trạng thái nghiệp vụ mục tiêu (To-Be)** — điều doanh nghiệp cần hệ thống làm đúng, không phải điều hệ thống đang làm. Mọi quy tắc trong Mục 3 đều là **yêu cầu bắt buộc như nhau**, bất kể phần triển khai đã đáp ứng hay chưa. Nơi nào hệ thống làm khác tài liệu, **hệ thống phải được sửa theo tài liệu**.

Hệ quả trực tiếp:

- Tài liệu **không hạ chuẩn nghiệp vụ** để khớp với giới hạn kỹ thuật hiện có.
- Tài liệu **không mô tả hiện trạng triển khai** trong thân đặc tả. Việc đo khoảng cách giữa đặc tả và triển khai thuộc backlog kỹ thuật riêng.
- **Không dùng nhãn trạng thái triển khai** ở bất kỳ đâu. Những nhu cầu **chưa đủ chín muồi để chốt phương án nghiệp vụ** được gom riêng tại Mục 7 — đó là khác biệt duy nhất có ý nghĩa: *đã chốt được điều đúng là gì* hay *chưa chốt được*.

**Ba thay đổi về bản chất so với v6.4:**

1. **Loại bỏ ngôn ngữ kỹ thuật khỏi phần thân.** Tên trường dữ liệu, tên giá trị mã hóa, đường dẫn giao diện lập trình, tên bảng lưu trữ và tên chuẩn kỹ thuật được thay bằng ngôn ngữ người dùng nghiệp vụ.
2. **Mỗi tính năng có bảng Tiêu chí Chấp nhận kiểm chứng được**, phủ cả luồng thất bại, ca biên và từng màn hình mà quy tắc có hiệu lực.
3. **Tách bạch điều đã chốt và điều chưa chốt.** Các vấn đề đã có quyết định được đặc tả ngay tại quy tắc tương ứng và ghi vào nhật ký quyết định (Phụ lục C); Mục 7 chỉ còn những điểm thực sự chưa có quyết định nghiệp vụ.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả toàn bộ nghiệp vụ quản trị dữ liệu khách hàng cá nhân và tổ chức doanh nghiệp trong hệ thống CRM B2B SaaS:

1. **Quản trị Danh bạ Khách hàng Cá nhân:** Thu thập, lưu trữ, làm giàu thông tin và quản trị hồ sơ 360 độ của mọi cá nhân tương tác với doanh nghiệp.
2. **Quản trị Danh bạ Tổ chức Doanh nghiệp:** Quản lý hồ sơ công ty, mã số thuế, ngành nghề, cấu trúc tập đoàn Công ty Mẹ – Con và danh sách nhân sự liên hệ trực thuộc.
3. **Mạng lưới Quan hệ Đa chiều:** Quản lý việc một cá nhân làm việc cho nhiều công ty cùng lúc và mạng lưới quan hệ người – người (báo cáo trực tiếp, giới thiệu, đối tác).
4. **Vòng đời Khách hàng & Chuyển đổi Tiềm năng:** Định vị mức độ trưởng thành của khách hàng qua 10 giai đoạn (7 giai đoạn phễu tuyến tính và 3 trạng thái đặc biệt) và quy trình chuyển đổi Khách hàng tiềm năng thành Liên hệ chính thức, Doanh nghiệp và Cơ hội bán hàng trong một thao tác duy nhất.
5. **Điểm Tiềm năng & Chấm điểm Tự động:** Đánh giá mức độ tiềm năng theo hồ sơ và tần suất tương tác để ưu tiên phân bổ cho đội kinh doanh, có cơ chế suy giảm điểm khi khách nguội.
6. **Chất lượng Dữ liệu & Xử lý Trùng lặp:** Tự động phát hiện trùng lặp, xem trước tác động gộp, gộp bản ghi an toàn kèm Sổ cái Hoàn tác Gộp.
7. **Nhập / Xuất Dữ liệu Thông minh:** Trình trợ lý nhập tệp danh bạ dung lượng lớn có tự động ánh xạ cột và báo cáo lỗi chi tiết; xuất dữ liệu có kiểm soát và có nhật ký.
8. **Dòng thời gian Hoạt động Hợp nhất 360 độ:** Luồng thông tin trung tâm tập hợp mọi ghi chú, vé hỗ trợ, cơ hội bán hàng, công việc và hội thoại đa kênh của một khách hàng.
9. **Tuân thủ Dữ liệu Cá nhân:** Quản lý đồng thuận nhận tin theo từng kênh kèm bằng chứng thu thập, phân loại mục đích gửi tin, xử lý các yêu cầu về quyền của chủ thể dữ liệu (bản sao, chỉnh sửa, xóa vĩnh viễn, hạn chế xử lý) và chính sách lưu trữ theo thời hạn.
10. **Quyền phụ trách, Cộng tác & Ghi nhận Hoạt động:** Chuyển giao quyền phụ trách và bàn giao khi nhân viên rời tổ chức hoặc nghỉ phép; chia sẻ bản ghi và Đội ngũ phụ trách để nhiều bộ phận cùng phục vụ một khách hàng; ghi chú nội bộ có phân loại phạm vi đọc và bản ghi hoạt động không thể tạo khống.

### 1.2 Phạm vi

Tài liệu bao gồm 11 nhóm chức năng:

- **Nhóm A — Quản trị Hồ sơ Khách hàng Cá nhân:** Tạo mới, cập nhật, hồ sơ 360 độ, thẻ phân loại, che dữ liệu nhạy cảm và Thùng rác.
- **Nhóm B — Quản trị Hồ sơ Tổ chức & Doanh nghiệp:** Tạo mới, cập nhật, cấu trúc Công ty Mẹ – Con, hồ sơ doanh nghiệp và danh sách nhân sự trực thuộc, Thùng rác.
- **Nhóm C — Mạng lưới Quan hệ Đa chiều:** Quan hệ người – công ty đa liên kết và quan hệ người – người.
- **Nhóm D — Vòng đời Khách hàng & Chuyển đổi Tiềm năng:** 10 giai đoạn vòng đời, ma trận chuyển đổi, lịch sử giai đoạn, chuyển đổi tiềm năng một thao tác, phân bổ khách hàng tiềm năng tự động và theo dõi nguồn gốc tiếp thị.
- **Nhóm E — Điểm Tiềm năng & Chấm điểm Tự động:** Chấm điểm theo hồ sơ và hành vi, ngưỡng thăng hạng, suy giảm điểm theo thời gian.
- **Nhóm F — Nhận diện Trùng lặp & Gộp Bản ghi An toàn:** Kiểm tra trùng lặp, xem trước tác động gộp, gộp bản ghi, hoàn tác gộp và khôi phục giao dịch gộp bị gián đoạn.
- **Nhóm G — Nhập Dữ liệu Thông minh qua Hàng đợi:** Tải tệp dung lượng lớn, tự động ánh xạ cột, xử lý nền và báo cáo lỗi chi tiết.
- **Nhóm H — Xuất Dữ liệu & Danh sách Hiển thị:** Xuất dữ liệu có kiểm soát, phê duyệt xuất lớn, nhật ký xuất và danh sách hiển thị dùng chung.
- **Nhóm I — Dòng thời gian 360 độ & Ngữ cảnh Khách hàng:** Dòng thời gian hợp nhất và khung Ngữ cảnh Khách hàng một chạm cho Hộp thư Đa kênh.
- **Nhóm J — Định danh, Đồng thuận & Tuân thủ Dữ liệu Cá nhân:** Kênh liên lạc và trạng thái tiếp cận, đồng thuận nhận tin theo kênh, định danh dùng chung, bằng chứng đồng thuận, phân loại mục đích gửi tin và quyền của chủ thể dữ liệu.
- **Nhóm K — Quyền phụ trách, Cộng tác & Ghi nhận Hoạt động:** Chuyển giao và bàn giao, Đội ngũ phụ trách và chia sẻ bản ghi, ghi chú và bản ghi hoạt động.

**Ngoài phạm vi (thuộc về các tài liệu SRS chuyên biệt khác):**

- Quản trị giao tiếp đa kênh và Hộp thư chung — thuộc [`omnichat-srs.md`](./omnichat-srs.md).
- Cấu hình phễu bán hàng, vòng đời Cơ hội bán hàng và bảng Kanban — thuộc [`deals-pipeline-srs.md`](./deals-pipeline-srs.md). Phân hệ này chỉ đặc tả **tác động của sự kiện Cơ hội bán hàng lên giai đoạn vòng đời khách hàng**.
- Quản trị Vé hỗ trợ và cam kết chất lượng dịch vụ — thuộc [`tickets-srs.md`](./tickets-srs.md).
- Phân quyền trường chi tiết và tùy biến bố cục — thuộc [`object-manager-srs.md`](./object-manager-srs.md).
- Mô hình vai trò, danh sách vai trò dựng sẵn, mức truy cập, phạm vi dữ liệu theo cây tổ chức, hàng đợi và đơn vị tiếp nhận, khung che dữ liệu, tạm ngưng và rời workspace của thành viên, thứ tự hợp nhất quyền — thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md). Tài liệu này chỉ khai báo phần mà tài liệu đó giao cho phân hệ: ma trận ô mặc định và quyền quản trị của phân hệ (Mục 5), hàng đợi khách hàng tiềm năng (`BR-31.9`), trường nhạy cảm của hệ thống (`BR-04.7`), Sàn bắt buộc của phân hệ và quy tắc bàn giao bản ghi khách hàng (`FEAT-34`).
- Thiết kế và phát sóng chiến dịch tiếp thị — thuộc [`campaigns-srs.md`](./campaigns-srs.md). Phân hệ này chỉ đặc tả **điều kiện đồng thuận và mục đích gửi tin** mà mọi lượt gửi phải tuân theo.

### 1.3 Đối tượng đọc

- **Product Owner / Business Analyst:** Căn cứ chốt phạm vi và thẩm định quy trình nghiệp vụ quản lý dữ liệu khách hàng.
- **Đội ngũ Phát triển:** Căn cứ duy nhất để xây dựng và sửa chữa chức năng. Khi hệ thống khác tài liệu, hệ thống phải sửa theo tài liệu.
- **Đội ngũ Đảm bảo Chất lượng:** Căn cứ thiết kế kịch bản kiểm thử. Mỗi Tiêu chí Chấp nhận (AC) là một ca kiểm thử.
- **Đội ngũ Kinh doanh, Marketing & Hỗ trợ:** Nắm rõ quy trình quản lý danh bạ, luồng chuyển đổi tiềm năng, chất lượng dữ liệu và nghĩa vụ tuân thủ dữ liệu cá nhân.
- **Người phụ trách Bảo vệ Dữ liệu & Pháp chế:** Căn cứ đối chiếu nghĩa vụ bảo vệ dữ liệu cá nhân.

### 1.4 Thuật ngữ nghiệp vụ

| Thuật ngữ | Định nghĩa nghiệp vụ |
| --- | --- |
| **Khách hàng Cá nhân (Contact)** | Một con người cụ thể trong CRM kèm thông tin liên hệ và lịch sử tương tác. |
| **Tổ chức / Doanh nghiệp (Account)** | Một pháp nhân, công ty, tập đoàn hoặc cơ quan có quan hệ kinh doanh với doanh nghiệp. |
| **Hồ sơ Khách hàng Tạm** | Hồ sơ được phép tạo cho khách vãng lai chưa để lại email hay số điện thoại, nhận diện bằng định danh phiên chat/thiết bị (`BR-01.1b`). |
| **Giai đoạn Vòng đời** | Vị trí của khách hàng trên hành trình chuyển đổi: 7 giai đoạn phễu tuyến tính (**Subscriber → Lead → MQL → SQL → Opportunity → Customer → Evangelist**) và 3 trạng thái đặc biệt ngoài phễu (**Nurturing, Churned, Disqualified**). Định nghĩa từng giai đoạn tại `FEAT-12`. Tên giai đoạn là nhãn nghiệp vụ hiển thị cho người dùng. |
| **Giai đoạn tiền bán hàng** | Sáu giai đoạn mà khách chưa từng trả tiền: Subscriber, Lead, MQL, SQL, Opportunity và Nurturing. |
| **Chuyển đổi Tiềm năng** | Thao tác thẩm định nâng cấp một khách hàng tiềm năng thành Liên hệ chính thức, liên kết/tạo Doanh nghiệp và tạo hoặc gắn vào Cơ hội bán hàng tương ứng. |
| **Ma trận Chuyển đổi Giai đoạn** | Bảng các bước chuyển giai đoạn vòng đời được phép kèm điều kiện và quyền, là hệ quả của bốn nguyên tắc chi phối tại `FEAT-12`. |
| **Bản ghi Chính** | Bản ghi được giữ lại định danh sau khi gộp hai khách hàng hoặc hai doanh nghiệp. Bản ghi còn lại gọi là **bản ghi phụ**. |
| **Sổ cái Hoàn tác Gộp** | Nơi lưu vết mỗi lần gộp, kèm ảnh chụp dữ liệu gốc của bản ghi phụ, cho phép khôi phục trạng thái trước khi gộp. |
| **Quan hệ Đa tổ chức** | Khả năng liên kết một cá nhân với nhiều doanh nghiệp cùng lúc, mỗi liên kết có chức danh, vai trò và thời gian công tác riêng. |
| **Doanh nghiệp chính** | Doanh nghiệp duy nhất được dùng để hiển thị mặc định cho một cá nhân trên danh sách và báo cáo. |
| **Dòng thời gian 360 độ** | Luồng hiển thị hợp nhất mọi tương tác, ghi chú, vé, cơ hội và công việc của một khách hàng theo thứ tự thời gian mới nhất trước. |
| **Điểm Tiềm năng** | Điểm 0–100 đánh giá độ nóng của khách hàng, gồm **Điểm Hồ sơ** (mức phù hợp theo thuộc tính) và **Điểm Tương tác** (hành vi thực tế). |
| **Đồng thuận Nhận tin** | Trạng thái **Đồng ý nhận tin**, **Từ chối nhận tin** hoặc **Chưa có đồng thuận** của khách hàng đối với thư tiếp thị, ghi nhận độc lập cho từng kênh (`BR-30.1`). |
| **Bằng chứng Đồng thuận** | Bộ dữ liệu chứng minh một lần thay đổi đồng thuận: thời điểm, nguồn thu thập, nội dung điều khoản đã đồng ý và người ghi nhận. |
| **Định danh dùng chung** | Nhãn đặt lên một email hoặc số điện thoại mà nhiều người khác nhau hợp lệ cùng dùng (tổng đài, lễ tân, vợ chồng), để hệ thống không coi các bản ghi đó là trùng. |
| **Trạng thái Hạn chế xử lý** | Trạng thái đặt lên hồ sơ khi chủ thể dữ liệu yêu cầu hạn chế xử lý: dữ liệu được giữ nhưng dừng mọi hoạt động tiếp thị và tự động hóa (`BR-30.6`). |
| **Quyền Chủ thể Dữ liệu** | Các quyền của khách hàng đối với dữ liệu cá nhân của chính họ: yêu cầu bản sao, chỉnh sửa, xóa vĩnh viễn, hạn chế xử lý và rút lại đồng thuận. Khác hoàn toàn với Thùng rác nội bộ — xóa theo quyền chủ thể dữ liệu là nghĩa vụ pháp lý và không thể phục hồi. |
| **Khử định danh** | Xóa phần dữ liệu cho phép nhận ra một người cụ thể, giữ lại phần giá trị kinh doanh hoặc thống kê ở dạng vô danh. |
| **Mặt nạ dữ liệu** | Cơ chế che một phần hoặc toàn bộ giá trị trường nhạy cảm theo quan hệ của người xem với bản ghi (`FEAT-04`). **Mở khóa mặt nạ** là thao tác có kiểm soát để xem giá trị đầy đủ. |
| **Đội ngũ phụ trách** | Nhóm người cùng phục vụ một khách hàng bên cạnh Người phụ trách, mỗi người có vai trò tham gia và mức quyền Chỉ đọc hoặc Chỉnh sửa (`FEAT-35`). |
| **Người phụ trách** | Nhân viên chịu trách nhiệm chính đối với một khách hàng. Mỗi bản ghi có tối đa một Người phụ trách. |
| **Mức truy cập** | Giá trị của một ô (loại dữ liệu × thao tác) trong vai trò, từ hẹp đến rộng: **Không có** / **Chỉ của mình** / **Đơn vị của mình** / **Đơn vị và các đơn vị con** / **Toàn workspace**; thao tác Tạo chỉ có Có / Không có. Định nghĩa các mức, "Bản ghi của mình" và "Bản ghi thuộc một đơn vị" theo đúng [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4 và `FEAT-34`; tài liệu này không định nghĩa lại. |
| **Đơn vị tiếp nhận** | Đơn vị tổ chức được khai báo nhận khách hàng tiềm năng của một nguồn (biểu mẫu website, tích hợp, kênh hội thoại, lô nhập khẩu vào hàng đợi) — `BR-31.9`, theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10`. |
| **Bản ghi chờ phân công** | Khách hàng tiềm năng đi vào hàng đợi của đơn vị tiếp nhận: thuộc đơn vị tiếp nhận cho tới khi có Người phụ trách, sau đó theo Người phụ trách như mọi bản ghi (`BR-31.9`). |
| **Bản ghi giữ chỗ** | Bản ghi nhập từ tệp được gán cho một người còn đang chờ chấp nhận lời mời vào workspace: chưa có Người phụ trách, mang nhãn "giữ chỗ cho" người đó, thuộc Đơn vị chính ghi trong lời mời (`BR-23.5`). |
| **Sàn bắt buộc** | Ràng buộc không cấu hình vượt được qua bất kỳ vai trò, lượt cấp hay điều chỉnh nào, theo định nghĩa tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4. Các quy tắc gắn nhãn "sàn bắt buộc" trong tài liệu này là sàn do phân hệ khai báo; sàn nào áp cả lên Người có toàn quyền thì quy tắc nêu rõ. |
| **Người có toàn quyền** | Cách gọi chung cho Chủ sở hữu và Quản trị viên — hai cấp bậc thành viên, không phải vai trò (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4). |
| **Vai trò chức năng** | Trách nhiệm nghiệp vụ được giao cho một người (Quản lý Khách hàng Hiện hữu, Quản trị Chất lượng Dữ liệu) nhưng không phải một vai trò phân quyền riêng; quyền hạn theo vai trò gốc được cấp. Người phụ trách Bảo vệ Dữ liệu là **chức danh trách nhiệm** theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-47`, cũng không tự cấp quyền. |
| **Không gian làm việc** | Phạm vi dữ liệu của một doanh nghiệp khách hàng. Dữ liệu giữa các không gian làm việc tuyệt đối không nhìn thấy nhau. |

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà phân hệ giải quyết

Trong vận hành kinh doanh B2B và B2C, dữ liệu khách hàng thường bị phân tán, trùng lặp và thiếu nhất quán:

1. **Mất ngữ cảnh tương tác.** Nhân viên kinh doanh không nắm được lịch sử tương tác trước đó của đồng nghiệp hoặc bộ phận hỗ trợ với cùng khách hàng.
2. **Dữ liệu trùng lặp.** Dữ liệu nhập từ nhiều nguồn (website, trò chuyện trực tuyến, tệp danh bạ, mạng xã hội, sự kiện) sinh bản ghi trùng, lãng phí nguồn lực và khiến khách hàng bị liên hệ nhiều lần.
3. **Một người, nhiều công ty.** Một cá nhân đóng nhiều vai trò tại nhiều công ty, nhưng CRM truyền thống chỉ cho gán vào một công ty duy nhất.
4. **Khách hàng tiềm năng bị bỏ quên và chuyển đổi rời rạc.** Khách hàng tiềm năng không được liên hệ kịp thời, hoặc được chuyển đổi thủ công rời rạc làm mất liên kết giữa người liên hệ, công ty và cơ hội bán hàng.
5. **Rủi ro lộ lọt dữ liệu cá nhân.** Thông tin nhạy cảm bị phơi bày cho người không có nhu cầu công việc, và doanh nghiệp không chứng minh được đã xử lý dữ liệu cá nhân đúng nghĩa vụ.

Phân hệ giải quyết các bài toán trên bằng một nền tảng quản trị danh bạ 360 độ: tự động phát hiện trùng lặp, hỗ trợ quan hệ đa chiều, hợp nhất dòng thời gian hoạt động, và bảo vệ dữ liệu nhạy cảm theo quan hệ của người xem với bản ghi.

### 2.2 Vai trò người dùng

Danh sách vai trò dưới đây dùng **đúng tên vai trò dựng sẵn** tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29` — nguồn duy nhất về danh sách vai trò dựng sẵn; tài liệu này chỉ khai báo ma trận chi tiết của chúng trên dữ liệu khách hàng và doanh nghiệp (Mục 5).

| Vai trò | Tên gọi trong quy tắc và kịch bản | Quyền hạn và trách nhiệm nghiệp vụ |
| --- | --- | --- |
| **Nhân viên Kinh doanh** (vai trò dựng sẵn) | Nhân viên Kinh doanh, nhân viên | Tạo mới, chăm sóc khách hàng cá nhân và doanh nghiệp mình phụ trách, xem khách hàng của đơn vị mình, cập nhật giai đoạn vòng đời theo chiều tiến lên, đánh dấu Lead rác, chuyển đổi tiềm năng, nhận khách hàng tiềm năng từ hàng đợi của đơn vị, đề nghị chuyển khách mình phụ trách cho đồng nghiệp (`BR-34.1b`), theo dõi dòng thời gian tương tác. |
| **Nhân viên Hỗ trợ** (vai trò dựng sẵn) | Nhân viên Hỗ trợ, Tư vấn viên, tuyến Hỗ trợ | Tra cứu ngữ cảnh khách hàng khi tiếp nhận hội thoại hoặc vé hỗ trợ, ghi nhận ghi chú và hoạt động phục vụ khách. Bao gồm cả Tư vấn viên trò chuyện trực tuyến — cùng một vai trò, chỉ khác kênh phục vụ. Vai trò này chỉ xem khách hàng của đơn vị mình; với khách của đơn vị khác, truy cập hồ sơ qua quyền đọc tự động khi có vé/hội thoại đang mở (`BR-35.4`) — quyền đó là **chỉ đọc**, trừ hai thao tác thu hẹp phạm vi xử lý dữ liệu (gắn Hạn chế xử lý, hạ đồng thuận). |
| **Quản lý** (vai trò dựng sẵn) | Quản lý Kinh doanh, Quản lý — khi nói về người giữ vai trò Quản lý trong đơn vị kinh doanh | Phân công và chuyển giao khách hàng trong đơn vị và các đơn vị con (`FEAT-34`), duyệt các bước lùi giai đoạn và loại khách (`BR-12.4`, `BR-12.7`), duyệt Lead rác, hoàn tác chuyển đổi (`BR-14.2`), xác nhận bằng chứng liên hệ ngoài hệ thống (`BR-31.8`), cấu hình quy tắc phân bổ, theo dõi báo cáo danh bạ trong phạm vi đơn vị. Các năng lực này là quyền quản trị của phân hệ mặc định có ở vai trò Quản lý (Mục 5.2), không gắn với tên vai trò. |
| **Marketing** (vai trò dựng sẵn) | Nhân viên Marketing, Marketing | Xem toàn bộ khách hàng phục vụ phân khúc, **xem** (không sửa) cấu hình chấm điểm, ghi nhận đồng thuận nhận tin theo kênh, gắn thẻ phân loại phục vụ phân khúc, xem báo cáo chuyển đổi và báo cáo nguồn gốc. Mặc định không sửa, xóa, gộp, nhập hay xuất bản ghi khách hàng. |
| **Quản lý Marketing** (vai trò dựng sẵn) | Quản lý Marketing | Toàn bộ chức năng Marketing, cộng: tạo, nhập và sửa khách hàng trong đơn vị mình; cấu hình chấm điểm tiềm năng, ngưỡng thăng hạng và suy giảm điểm; đồng phê duyệt Chiến dịch Tái tiếp cận khách đã rời bỏ; xem báo cáo nguồn gốc và phân bổ doanh thu theo kênh. |
| **Chỉ xem bản ghi được giao** (vai trò dựng sẵn) | — | Chỉ xem khách hàng và doanh nghiệp mình đang phụ trách; không có thao tác ghi nào. Là vai trò mặc định của người mới (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`). |
| **Kiểm toán** (vai trò dựng sẵn) | — | Xem toàn bộ khách hàng và doanh nghiệp, không có thao tác ghi, không mở khóa mặt nạ, không xuất; mỗi lượt xem là đọc ngoài phạm vi phụ trách (`NFR-07` mục 13). Không đọc được nhật ký kiểm toán của phân hệ (`NFR-14`). |
| **Kiểm toán quyền** (vai trò dựng sẵn) | — | Không truy cập dữ liệu khách hàng và doanh nghiệp. |
| **Quản trị viên** (cấp bậc thành viên) | Quản trị viên | Quản trị cấu hình trường dữ liệu, thực thi gộp và hoàn tác gộp, khôi phục giao dịch gộp bị gián đoạn, nhập/xuất dữ liệu hàng loạt, xử lý yêu cầu quyền chủ thể dữ liệu, loại khách đã trả tiền khi phát hiện gian lận. |
| **Chủ sở hữu** (cấp bậc thành viên) | Chủ sở hữu | Toàn quyền quản trị danh bạ, xem toàn bộ dữ liệu tổ chức, cấu hình chính sách bảo mật dữ liệu nhạy cảm và khai báo lịch làm việc của không gian làm việc. |
| **Tiến trình Hệ thống** | Hệ thống | Tự động tính điểm tiềm năng và suy giảm điểm, sinh bước chuyển giai đoạn từ sự kiện Cơ hội bán hàng, phân bổ khách hàng tiềm năng, xử lý nhập/xuất theo hàng đợi, dọn dẹp Thùng rác quá hạn, khử định danh theo thời hạn lưu, cấp và thu hồi quyền đọc tự động. |

> **Ghi chú:** Năm vai trò dựng sẵn có thao tác trên dữ liệu khách hàng (Nhân viên Kinh doanh, Nhân viên Hỗ trợ, Quản lý, Marketing, Quản lý Marketing), hai cấp bậc Quản trị viên và Chủ sở hữu, cùng Tiến trình Hệ thống là các cột của Ma trận tính năng tại Mục 5.3; ba vai trò dựng sẵn còn lại được khai báo đủ ô tại Mục 5.1. Mô hình vai trò, cấp bậc thành viên và cách phân giải quyền thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md).

**Quy ước đọc tên vai trò trong quy tắc nghiệp vụ.** Quyền không gắn cứng với tên vai trò: mọi năng lực trong tài liệu là **một ô** (loại dữ liệu × thao tác, kể cả thao tác đặc thù của phân hệ — Mục 5.1) hoặc **một quyền quản trị của phân hệ** (Mục 5.2), áp như nhau cho vai trò dựng sẵn và vai trò doanh nghiệp tự tạo. Khi một quy tắc hay kịch bản nêu tên vai trò, tên đó chỉ người giữ quyền tương ứng **theo mặc định**:

- "Quản lý Kinh doanh" hay "Quản lý Kinh doanh trở lên" thực hiện một thao tác = người giữ quyền quản trị của phân hệ quy định cho thao tác đó tại Mục 5.2, trên bản ghi mà ô tương ứng của họ bao phủ.
- "Quản lý Kinh doanh của Người phụ trách", "Quản lý của nhân viên" = **quản lý trực tiếp** của người đó theo chuỗi quản lý tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4; nếu người đó không có quản lý trực tiếp thì là người phụ trách Đơn vị chính của họ.
- "Quản trị viên và Chủ sở hữu" = **Người có toàn quyền**.

Doanh nghiệp trao các năng lực này cho vai trò tự tạo, hoặc điều chỉnh từng ô của vai trò dựng sẵn qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02`, trong giới hạn các Sàn bắt buộc của tài liệu này.

**Vai trò chức năng (không có cột riêng trong Ma trận phân quyền):**

| Vai trò chức năng | Trách nhiệm | Quyền hạn áp dụng |
| --- | --- | --- |
| **Quản lý Khách hàng Hiện hữu** | Nhân viên hoặc Quản lý Kinh doanh được gán làm Người phụ trách của một khách hàng từ giai đoạn Customer trở lên. Chịu trách nhiệm duy trì, gia hạn và bán mở rộng; là người nhận thông báo khi khách chuyển sang Churned (`BR-12.5`). | Theo vai trò gốc (Nhân viên hoặc Quản lý Kinh doanh). |
| **Quản trị Chất lượng Dữ liệu** | Người được Chủ sở hữu hoặc Quản trị viên chỉ định rà soát trùng lặp định kỳ, chuẩn hóa dữ liệu, xử lý hồ sơ tạm tồn dư, xử lý Đề nghị gộp và giám sát chỉ số chất lượng dữ liệu (Mục 2.6). Trong tổ chức nhỏ, do Quản trị viên kiêm nhiệm. | Theo vai trò gốc được cấp. |
| **Người phụ trách Bảo vệ Dữ liệu** | Chức danh trách nhiệm theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-47`. **Doanh nghiệp tự xác định có phải chỉ định hay không theo quy định áp dụng cho mình và tự chịu trách nhiệm về quyết định đó.** Giám sát xử lý yêu cầu quyền chủ thể dữ liệu (`FEAT-33`), đồng phê duyệt các tham số mức "Có sàn bắt buộc" ghi "+ BVDL" tại Phụ lục B, nhận cảnh báo truy cập bất thường và báo cáo phơi bày dữ liệu. Nếu tổ chức không chỉ định, trách nhiệm thuộc Chủ sở hữu, và mọi nơi yêu cầu "hai người khác nhau" áp quy tắc thay thế tại `NFR-14`. | Theo vai trò gốc được cấp (thường là Quản trị viên). |

### 2.3 Quy ước thời gian nghiệp vụ

Toàn bộ mốc thời gian nghiệp vụ trong tài liệu này — *giờ làm việc và ngày làm việc*, *hết ngày* của các hạn mức theo ngày, *tháng* của các hạn mức theo tháng, *giờ chạy tiến trình nền* (ví dụ suy giảm điểm lúc 02:00), *số ngày không tương tác*, và *thời hạn lưu trữ* — được xác định theo **múi giờ của Không gian làm việc**, khai báo trong Lịch làm việc (`BR-31.7b`, Phụ lục B `CFG-31-03`), không theo múi giờ máy chủ và không theo múi giờ của từng người dùng. Quy ước này thống nhất với [`tasks-srs.md`](./tasks-srs.md#23-quy-ước-thời-gian-nghiệp-vụ) và [`deals-pipeline-srs.md`](./deals-pipeline-srs.md#23-quy-ước-thời-gian-nghiệp-vụ).

Ba quy ước đo thời gian áp dụng cho toàn tài liệu:

- **Hạn mức theo ngày** (mở khóa mặt nạ, liên lạc trong hệ thống, xuất dữ liệu) tính theo **ngày dương lịch**, bắt đầu lại lúc 00:00 theo múi giờ Không gian làm việc.
- **Hạn mức theo tháng** (thêm thành viên Đội ngũ phụ trách, bằng chứng liên hệ ngoài hệ thống) tính theo **tháng dương lịch**.
- **Thời hạn tính bằng tháng** (thời hạn lưu định danh, trần lưu hồ sơ tạm, thời hạn rà soát dữ liệu không hoạt động) được quy đổi **1 tháng = 30 ngày**, để các ràng buộc chéo giữa tham số tính bằng ngày và tính bằng tháng (ví dụ `CFG-33-01` với `CFG-33-03`) luôn so sánh được, không phụ thuộc tháng dài hay ngắn và không phát sinh ca 29/02.

Các thời hạn tính bằng **giờ làm việc** hoặc **ngày làm việc** chỉ trôi trong khung giờ làm việc của Lịch làm việc; các thời hạn tính bằng giờ hoặc ngày thông thường trôi liên tục.

**Lý do nghiệp vụ:** Một khách hàng tiềm năng được phân bổ lúc 16:00 phải quá hạn vào cùng một thời điểm với mọi thành viên trong đội và với người kiểm thử. Nếu tính theo múi giờ cá nhân hoặc múi giờ máy chủ, cam kết thời gian phản hồi (`BR-31.7`), hạn mức mở khóa (`BR-04.5`) và chỉ số `KPI-03` không nghiệm thu được một cách thống nhất.

### 2.4 Nguyên tắc nghiệp vụ nền tảng

**Nguyên tắc 1 — Ma trận phân quyền quyết định *có quyền hay không*; quy tắc nghiệp vụ quyết định *điều kiện bên trong quyền đó*.**
Ma trận tại Mục 5 là nguồn duy nhất về việc một vai trò có hay không có quyền dùng một tính năng. Quy tắc nghiệp vụ (`BR`) là nguồn duy nhất về điều kiện, hạn mức, ngưỡng phê duyệt và ngoại lệ bên trong quyền đó. Hai nguồn không được nói khác nhau về cùng một điều: mỗi ô ma trận có điều kiện vượt ra ngoài từ vựng chuẩn của Mục 5 bắt buộc dẫn chiếu mã `BR` quy định điều kiện đó. Nếu một ô ma trận và quy tắc tương ứng mâu thuẫn về việc có quyền hay không, đó là **lỗi tài liệu phải sửa**, không phải tình huống chọn một bên để nghiệm thu. Dòng "Vai trò sử dụng chính" trong mỗi tính năng chỉ mang tính mô tả.

**Nguyên tắc 2 — Quyền theo quan hệ với bản ghi cộng thêm vào quyền theo vai trò, không thay thế nó.**
Một số quyền được trao theo **quan hệ với một bản ghi cụ thể** — Người phụ trách, thành viên Đội ngũ phụ trách, người đang xử lý vé/hội thoại của khách, chính người dùng khai báo cho bản thân — chứ không theo vai trò. Các quyền này chỉ nới rộng **phạm vi dữ liệu** trên đúng bản ghi đó, không cấp thêm **năng lực** mà vai trò không có, và không nới lỏng bất kỳ mức che trường nào (`BR-35.5`).

**Nguyên tắc 3 — Hạ mức xử lý dữ liệu cá nhân luôn tự do; nâng mức luôn cần căn cứ.**
Mọi thao tác chỉ thu hẹp phạm vi xử lý dữ liệu cá nhân (từ chối nhận tin, hạn chế xử lý) được phép thực hiện ngay bởi người đang tiếp nhận yêu cầu của khách. Mọi thao tác nới rộng phạm vi (đồng ý nhận tin trở lại, dỡ hạn chế xử lý) bắt buộc có bằng chứng từ chính chủ thể dữ liệu hoặc thẩm quyền được quy định (`BR-30.6`, `BR-30.10`).

**Nguyên tắc 4 — Quyết định xóa dữ liệu khách hàng luôn thuộc về con người.**
Không tiến trình tự động nào được xóa hồ sơ khách hàng đã định danh, ngoài đúng sáu ngoại lệ có chủ đích liệt kê tại `BR-33.5` — mỗi ngoại lệ hoặc chỉ khử phần định danh mà giữ giá trị kinh doanh, hoặc chỉ thực thi một quyết định xóa mà con người đã đưa ra trước đó.

**Nguyên tắc 5 — Không một bản ghi nào được trở thành vô chủ.**
Mọi thay đổi nhân sự (nghỉ việc, chuyển bộ phận, nghỉ phép, vắng mặt dài) đều phải có điểm đến cho các khách hàng người đó đang phụ trách và cho các yêu cầu của khách đang chờ xử lý (`FEAT-34`).

### 2.5 Bảng tổng hợp tính năng nghiệp vụ

| Nhóm | Mã | Tên tính năng nghiệp vụ |
| --- | --- | --- |
| **A. Quản trị Khách hàng Cá nhân** | `FEAT-01` | Tạo mới & Quản lý Thông tin Khách hàng Cá nhân |
| | `FEAT-02` | Hồ sơ Chi tiết Khách hàng 360 độ |
| | `FEAT-03` | Quản lý Thẻ phân loại Hàng loạt |
| | `FEAT-04` | Bảo vệ Dữ liệu Nhạy cảm & Mở khóa Mặt nạ |
| | `FEAT-05` | Thùng rác Khách hàng & Phục hồi Bản ghi |
| **B. Quản trị Doanh nghiệp & Tổ chức** | `FEAT-06` | Tạo mới & Quản lý Thông tin Doanh nghiệp |
| | `FEAT-07` | Cấu trúc Cây Doanh nghiệp Công ty Mẹ – Con |
| | `FEAT-08` | Hồ sơ Chi tiết Doanh nghiệp & Danh sách Nhân sự Liên hệ |
| | `FEAT-09` | Thùng rác Doanh nghiệp & Phục hồi Bản ghi |
| **C. Mạng lưới Quan hệ Đa chiều** | `FEAT-10` | Quan hệ Đa Doanh nghiệp của Cá nhân |
| | `FEAT-11` | Mạng lưới Quan hệ Giữa các Cá nhân |
| **D. Vòng đời & Chuyển đổi Tiềm năng** | `FEAT-12` | Quản trị Giai đoạn Vòng đời Khách hàng & Ma trận Chuyển đổi |
| | `FEAT-13` | Lịch sử Chuyển đổi Giai đoạn Vòng đời |
| | `FEAT-14` | Chuyển đổi Khách hàng Tiềm năng Một thao tác |
| | `FEAT-31` | Phân bổ Khách hàng Tiềm năng Tự động |
| | `FEAT-32` | Theo dõi Nguồn gốc Khách hàng Tiềm năng |
| **E. Điểm Tiềm năng & Chấm điểm** | `FEAT-15` | Chấm điểm Tiềm năng Tự động |
| | `FEAT-16` | Suy giảm Điểm Tiềm năng theo Thời gian |
| **F. Xử lý Trùng lặp & Gộp Bản ghi** | `FEAT-17` | Nhận diện & Kiểm tra Trùng lặp Khách hàng |
| | `FEAT-18` | Xem trước Tác động Gộp Bản ghi |
| | `FEAT-19` | Gộp Khách hàng Cá nhân & Doanh nghiệp An toàn |
| | `FEAT-20` | Hoàn tác Gộp Bản ghi theo Sổ cái |
| | `FEAT-21` | Khôi phục Giao dịch Gộp bị Gián đoạn |
| **G. Nhập Dữ liệu Thông minh** | `FEAT-22` | Tải lên & Tiếp nhận Tệp Nhập khẩu Dung lượng lớn |
| | `FEAT-23` | Trợ lý Tự động Ánh xạ Cột Dữ liệu |
| | `FEAT-24` | Xử lý Nhập khẩu theo Hàng đợi & Báo cáo Lỗi Chi tiết |
| **H. Xuất Dữ liệu & Danh sách** | `FEAT-25` | Xuất Dữ liệu Khách hàng có Kiểm soát |
| | `FEAT-26` | Danh sách Hiển thị Dùng chung |
| **I. Dòng thời gian 360 & Ngữ cảnh** | `FEAT-27` | Dòng thời gian Hoạt động Hợp nhất 360 độ |
| | `FEAT-28` | Ngữ cảnh Khách hàng Một chạm cho Hộp thư Đa kênh |
| **J. Định danh, Đồng thuận & Tuân thủ** | `FEAT-29` | Quản lý Kênh liên lạc Đa kênh & Trạng thái Tiếp cận |
| | `FEAT-30` | Đồng thuận Nhận tin, Mục đích Gửi tin & Định danh Dùng chung |
| | `FEAT-33` | Quyền Chủ thể Dữ liệu & Xử lý Yêu cầu Dữ liệu Cá nhân |
| **K. Quyền phụ trách, Cộng tác & Hoạt động** | `FEAT-34` | Chuyển giao Quyền phụ trách & Bàn giao khi Thay đổi Nhân sự |
| | `FEAT-35` | Chia sẻ Bản ghi & Đội ngũ Phụ trách Khách hàng |
| | `FEAT-36` | Ghi chú & Ghi nhận Hoạt động Khách hàng |

**Tổng kết phạm vi:** 36 tính năng nghiệp vụ, **tất cả đều là yêu cầu bắt buộc** của đặc tả này. Mã `FEAT` được giữ ổn định để các tài liệu khác dẫn chiếu không bị lệch; vì vậy `FEAT-31`, `FEAT-32` được trình bày trong Nhóm D và `FEAT-33` trong Nhóm J theo nội dung nghiệp vụ, không theo thứ tự mã. Các nhu cầu chưa chốt được phương án nghiệp vụ được gom tại Mục 7 và không mang mã `FEAT`.

### 2.6 Mục tiêu kinh doanh & Chỉ số thành công

Mỗi vấn đề tại Mục 2.1 được gắn với ít nhất một chỉ số đo lường được để nghiệm thu hiệu quả nghiệp vụ, đo tại mốc **90 ngày kể từ ngày đưa vào vận hành** trên từng không gian làm việc. Bảng có 8 chỉ số cho 5 vấn đề vì: **(a)** `KPI-07` (chất lượng dữ liệu đầu vào) và `KPI-08` (hiệu quả ngân sách Marketing) đo **điều kiện để các vấn đề kia được giải quyết bền vững**, không gắn trực tiếp một vấn đề; **(b)** vấn đề số 4 có hai mặt tách rời trong vận hành — tốc độ phản hồi lần đầu (`KPI-03`) và tỷ lệ chuyển đổi đúng quy trình (`KPI-04`).

| Mã | Vấn đề nghiệp vụ hoặc điều kiện nền | Chỉ số đo lường | Giá trị mục tiêu | Tính năng đóng góp |
| --- | --- | --- | --- | --- |
| `KPI-01` | Dữ liệu trùng lặp từ nhiều nguồn | Tỷ lệ bản ghi **dư thừa** do trùng lặp: số bản ghi cần gộp bỏ để không còn cặp trùng nào, chia cho tổng số bản ghi đang hoạt động (định nghĩa đầy đủ tại `BR-17.4`) | **< 2%** | `FEAT-17`, `18`, `19`, `23` |
| `KPI-02` | Mất ngữ cảnh tương tác | Tỷ lệ hội thoại/vé hỗ trợ được phản hồi lần đầu mà nhân viên xử lý đã mở Ngữ cảnh Khách hàng hoặc Dòng thời gian của khách đó **trước thời điểm phản hồi** và trong cùng phiên làm việc. Mẫu đo: mọi hội thoại/vé được phản hồi trong kỳ. Nguồn số liệu: **bộ đếm nghiệp vụ riêng** ghi nhận sự kiện "đã mở Ngữ cảnh/Dòng thời gian của khách hàng X" gắn với hội thoại/vé — tách biệt khỏi nhật ký kiểm toán để Product Owner tổng hợp hằng tháng mà không cần quyền đọc nhật ký theo `NFR-14` | **≥ 80%** | `FEAT-02`, `27`, `28`, `BR-35.4` |
| `KPI-03` | Khách hàng tiềm năng bị bỏ quên | Tỷ lệ khách hàng tiềm năng được liên hệ lần đầu **trong thời hạn cam kết tương ứng với mức ưu tiên** của họ (`BR-31.7`), tính trên bằng chứng liên hệ được công nhận tại `BR-31.8`; bằng chứng nhóm 2 (liên hệ ngoài hệ thống có Quản lý xác nhận) được thống kê thành **một cấu phần riêng**. **Loại khỏi mẫu đo:** bản ghi đã đánh dấu Lead rác (`BR-12.4b`) kể từ thời điểm đánh dấu; bản ghi đang ở trạng thái Hạn chế xử lý kể từ thời điểm gắn (`BR-30.6`) | **≥ 90%** | `FEAT-31` (`BR-31.6`, `BR-31.7`) |
| `KPI-04` | Chuyển đổi tiềm năng rời rạc | Tỷ lệ khách hàng tiềm năng đủ điều kiện được chuyển đổi qua quy trình một thao tác (`FEAT-14`) thay vì tạo tay rời rạc | **≥ 95%** | `FEAT-14` |
| `KPI-05` | Một người, nhiều công ty | Tỷ lệ khách hàng cá nhân thuộc **Loại khách hàng Doanh nghiệp (B2B)** (`BR-01.6`) có ít nhất một liên kết doanh nghiệp được khai báo; khách thuộc loại Cá nhân tiêu dùng (B2C) được loại khỏi mẫu đo | **≥ 85%** | `FEAT-10`, `08`, `BR-01.6` |
| `KPI-06` | Rủi ro lộ lọt dữ liệu cá nhân | **Số lượt truy cập dữ liệu nhạy cảm vượt hạn mức hoặc bị đánh giá là bất thường mà chưa được rà soát và đóng kết luận trong 7 ngày.** Nguồn: báo cáo truy cập bất thường (`BR-04.5`), báo cáo phơi bày định kỳ (`BR-04.5b`), báo cáo nhóm Liên lạc 1-1 (`BR-30.8`) — **không** truy vấn trực tiếp nhật ký kiểm toán, vì `NFR-14` giới hạn quyền đọc nhật ký | **= 0** | `FEAT-04`, `NFR-06`, `NFR-07`, `NFR-14` |
| `KPI-07` | Chất lượng dữ liệu đầu vào | Tỷ lệ hồ sơ có đủ tối thiểu: một kênh liên lạc hợp lệ, Người phụ trách đang hoạt động và Giai đoạn vòng đời. **Loại khỏi mẫu đo:** Hồ sơ Khách hàng Tạm (`BR-01.1b`) — theo thiết kế chưa có kênh liên lạc và chưa gán giai đoạn | **≥ 95%** | `FEAT-01`, `12`, `29`, `34` |
| `KPI-08` | Hiệu quả ngân sách Marketing | Tỷ lệ khách hàng tiềm năng mới ghi nhận được nguồn gốc (không rơi vào giá trị "Không xác định") | **≥ 90%** | `FEAT-32` |

**Quy ước theo dõi:** Chủ sở hữu chỉ số là Product Owner của phân hệ; số liệu tổng hợp hằng tháng. Chỉ số không đạt mục tiêu hai kỳ liên tiếp được đưa ra xem xét điều chỉnh nghiệp vụ.

### 2.7 Luồng nghiệp vụ đầu – cuối

**Giai đoạn 1 — Thu nhận:**
Khách hàng để lại thông tin qua biểu mẫu website, trò chuyện trực tuyến, quảng cáo, sự kiện, hoặc được nhập hàng loạt từ tệp danh bạ → Hệ thống ghi nhận nguồn gốc và tham số chiến dịch (`FEAT-32`) → Kiểm tra trùng lặp tức thì và áp chính sách xử lý (`BR-17.2`: mặc định trùng theo Tiêu chí chắc chắn thì chặn tạo mới và dẫn về bản ghi đã có; trùng theo Tiêu chí tham khảo thì chỉ cảnh báo) → Nếu không có người tạo trực tiếp, hệ thống tự động phân bổ Người phụ trách (`FEAT-31`), ưu tiên trả về đúng người đang phụ trách nếu là khách đã tồn tại.

**Giai đoạn 2 — Thẩm định & Nuôi dưỡng:**
Hệ thống chấm điểm theo hồ sơ và hành vi (`FEAT-15`) → Vượt Ngưỡng MQL thì tự động thăng hạng Lead → MQL (`BR-15.5`) → Đội kinh doanh phải phản hồi trong thời hạn cam kết, quá hạn thì hệ thống thu hồi và chia lại (`BR-31.7`) → Nhân viên kinh doanh thẩm định và chuyển MQL → SQL; chưa sẵn sàng mua thì chuyển Nurturing kèm lý do; không phù hợp thì Disqualified kèm lý do (`BR-12.4`) → Khách không tương tác lâu bị suy giảm điểm để phản ánh độ nguội (`FEAT-16`).

**Giai đoạn 3 — Chuyển đổi:**
Nhân viên kích hoạt Chuyển đổi Tiềm năng (`FEAT-14`): nâng cấp Liên hệ, liên kết hoặc tạo Doanh nghiệp, tạo Cơ hội bán hàng mới **hoặc gắn vào Cơ hội đang mở sẵn có của cùng Doanh nghiệp trên cùng Phễu** (`BR-14.3`) — tất cả cùng thành công hoặc cùng thất bại → Giai đoạn tự động lên Opportunity (`BR-12.2`) → Nếu chuyển đổi sai, Quản lý được hoàn tác trong thời hạn cho phép (`BR-14.2`).

**Giai đoạn 4 — Phục vụ & Mở rộng:**
Nhiều bộ phận cùng phục vụ một khách hàng qua Đội ngũ phụ trách (`FEAT-35`) → Mọi tương tác hợp nhất về Dòng thời gian 360 độ (`FEAT-27`), với ghi chú và bản ghi hoạt động là nguồn dữ liệu chính (`FEAT-36`) → Tư vấn viên tra cứu Ngữ cảnh Khách hàng một chạm khi tiếp nhận hội thoại nhờ quyền đọc tự động (`FEAT-28`, `BR-35.4`) → Khi Cơ hội thắng, giai đoạn tự lên Customer; Người phụ trách trở thành Quản lý Khách hàng Hiện hữu → Quan hệ đa công ty và mạng lưới cá nhân phục vụ bán mở rộng (`FEAT-10`, `FEAT-11`); cấu trúc tập đoàn phục vụ bán hàng theo tập đoàn (`FEAT-07`).

**Giai đoạn 5 — Duy trì Chất lượng, Bàn giao & Tuân thủ:**
Khi nhân viên đổi địa bàn, nghỉ phép hoặc rời tổ chức, danh bạ được bàn giao có kiểm soát (`FEAT-34`) → Quản trị Chất lượng Dữ liệu rà soát trùng lặp, xem trước tác động và gộp bản ghi có sổ cái hoàn tác (`FEAT-18`, `19`, `20`) → Đồng thuận nhận tin được quản lý theo từng kênh kèm bằng chứng (`FEAT-30`) → Khách hàng thực hiện quyền chủ thể dữ liệu qua quy trình chuẩn (`FEAT-33`) → Bản ghi hết giá trị được xóa mềm vào Thùng rác và dọn dẹp theo chính sách lưu trữ (`FEAT-05`, `FEAT-09`).

**Giai đoạn 6 — Rời bỏ & Tái tiếp cận:**
Khách hủy hợp đồng thì chuyển Churned, hệ thống thông báo người phụ trách và dừng toàn bộ chiến dịch tiếp thị tự động (`BR-12.5`) → Chỉ được tái tiếp cận qua Chiến dịch Tái tiếp cận đã được phê duyệt (`BR-12.5b`).

---

## 3. Đặc tả yêu cầu chức năng

*Cách đọc mục này:* mỗi tính năng gồm Mô tả nghiệp vụ, Vai trò sử dụng chính (chỉ mang tính mô tả — quyền có/không thuộc Ma trận Mục 5, theo Nguyên tắc 1 tại Mục 2.4), các Quy tắc nghiệp vụ `BR-xx.n` kèm Lý do nghiệp vụ, và bảng Tiêu chí Chấp nhận. Khi một quy tắc nhắc tới một mục con của quy tắc khác, tài liệu viết dạng `BR-05.6 (b)` — nghĩa là mục (b) trong danh sách của `BR-05.6`; các mã có hậu tố chữ liền (`BR-01.1b`, `BR-04.5b`, `BR-17.2c`…) là quy tắc độc lập.

### Nhóm A — Quản trị Khách hàng Cá nhân

#### FEAT-01 — Tạo mới & Quản lý Thông tin Khách hàng Cá nhân

**Mô tả nghiệp vụ:** Người dùng tạo mới, tra cứu danh sách, xem chi tiết, cập nhật và xóa khách hàng cá nhân trong không gian làm việc.

**Vai trò sử dụng chính:** Nhân viên Kinh doanh, Quản lý Kinh doanh, Quản trị viên.

**Điều kiện tiên quyết:** Người dùng có ô (Khách hàng, Tạo) = Có để tạo mới; các thao tác khác theo mức của đúng ô tương ứng (Mục 5.1).

**Luồng chính:**

1. Người dùng mở "Thêm khách hàng", nhập ít nhất một kênh liên lạc chính và các thông tin khác.
2. Hệ thống kiểm tra định dạng (`BR-01.2`) và trùng lặp tức thì (`FEAT-17`) ngay khi rời từng ô.
3. Người dùng lưu; hệ thống gán Người phụ trách và đơn vị của bản ghi (`BR-01.3`).
4. Người dùng tra cứu, mở chi tiết, cập nhật hoặc xóa khách hàng trong phạm vi mức truy cập của mình (`BR-01.4`).

**Quy tắc nghiệp vụ:**

- **`BR-01.1` (Thông tin bắt buộc):** Mỗi khách hàng cá nhân bắt buộc có ít nhất một kênh liên lạc chính: **địa chỉ email** hoặc **số điện thoại**. Họ tên là trường **khuyến khích** nhưng không bắt buộc — khi không có họ tên, hệ thống hiển thị tên thay thế lấy từ phần trước ký tự "@" của email (ví dụ email "ceo@company.com" hiển thị là "ceo") hoặc từ số điện thoại.

  **Lý do nghiệp vụ:** Một hồ sơ không có kênh liên lạc nào thì không phục vụ được mục đích nào của CRM và không kiểm tra trùng lặp được. Ngược lại, nhiều khách để lại email hoặc số điện thoại trước khi cho biết tên; bắt buộc họ tên sẽ khiến nhân viên nhập tên giả, làm bẩn dữ liệu.

- **`BR-01.1b` (Ngoại lệ Hồ sơ Khách hàng Tạm):** Khi tiếp nhận khách vãng lai qua trò chuyện trực tuyến mà khách **chưa có** cả email lẫn số điện thoại, hệ thống được tạo **Hồ sơ Khách hàng Tạm** — một ngoại lệ có kiểm soát của `BR-01.1` — với tên tạm "Khách vãng lai #[số thứ tự]" và định danh thay thế là mã phiên hội thoại hoặc định danh thiết bị/kênh chat của khách. Hồ sơ Tạm bị loại khỏi báo cáo Điểm tiềm năng và phễu vòng đời cho đến khi được bổ sung email hoặc số điện thoại hợp lệ; tại thời điểm đó hồ sơ tự động trở thành khách hàng chính thức, được gán giai đoạn theo `BR-12.10` và được kiểm tra trùng lặp như một bản ghi mới.

  **Lý do nghiệp vụ:** Nếu không có ngoại lệ này, tư vấn viên không ghi nhận được lịch sử của khách vãng lai, và khi khách để lại số điện thoại ở lần sau thì mọi trao đổi trước đó đã mất.

- **`BR-01.1c` (Tái nhận diện khách vãng lai quay lại):** Khi một khách vãng lai quay lại với **cùng định danh thiết bị/kênh chat**, hệ thống bắt buộc **dùng lại Hồ sơ Tạm đã có** thay vì tạo hồ sơ mới. Việc loại Hồ sơ Tạm khỏi kiểm tra trùng lặp chỉ áp dụng cho việc so khớp với khách hàng chính thức, không áp dụng cho việc so khớp giữa các Hồ sơ Tạm với nhau theo định danh thiết bị.

  **Cơ sở lưu và thông báo:** Định danh thiết bị/kênh chat là dữ liệu cá nhân. Việc lưu chỉ phục vụ duy trì liên tục hội thoại với khách, và cửa sổ chat **bắt buộc hiển thị thông báo ngắn** cho khách về việc hệ thống ghi nhận phiên để phục vụ hỗ trợ, kèm liên kết tới chính sách quyền riêng tư. Thời hạn lưu định danh chịu trần tuyệt đối tại `BR-33.6`.

  **Lý do nghiệp vụ:** Tránh tái lập chính vấn đề "mất ngữ cảnh tương tác" nêu tại Mục 2.1 — mỗi lần khách quay lại lại là một hồ sơ trắng.

- **`BR-01.2` (Kiểm tra định dạng):** Địa chỉ email phải đúng định dạng địa chỉ email chuẩn quốc tế. Số điện thoại được tự động chuẩn hóa về **định dạng quốc tế có mã quốc gia** (ví dụ +84901234567, +966501234567); số nhập theo định dạng trong nước được chuẩn hóa theo quốc gia mặc định của không gian làm việc. Giá trị không chuẩn hóa được bị từ chối ngay tại ô nhập.

  **Lý do nghiệp vụ:** Cùng một số điện thoại viết theo nhiều cách ("0908 123 456", "+84908123456") sẽ không khớp nhau khi kiểm tra trùng lặp theo Tiêu chí chắc chắn (`BR-17.1`), làm `KPI-01` không đạt được.

- **`BR-01.3` (Người phụ trách & đơn vị của bản ghi):** Khi người dùng tạo mới, người tạo tự động được gán làm Người phụ trách, trừ khi người có ô (Khách hàng, Gán) bao phủ người được chọn chỉ định người phụ trách khác ngay từ đầu. Bản ghi thuộc đơn vị nào theo đúng định nghĩa "Bản ghi thuộc một đơn vị" tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4: thuộc đồng thời Đơn vị chính và mọi Đơn vị kiêm nhiệm mức Đầy đủ của **Người phụ trách hiện tại**, và chuyển theo ngay lập tức khi Người phụ trách đổi qua bàn giao (`FEAT-34`). Các ngoại lệ của định nghĩa đó áp cho phân hệ này như sau: khách hàng tiềm năng vào hàng đợi là **bản ghi chờ phân công**, thuộc đơn vị tiếp nhận của nguồn cho tới khi có Người phụ trách (`BR-31.9`); **bản ghi giữ chỗ** thuộc Đơn vị chính ghi trong lời mời (`BR-23.5`); bản ghi chưa có Người phụ trách không qua hàng đợi thuộc Đơn vị chính của người tạo. Nhất quán với quy tắc tương ứng của Cơ hội bán hàng tại [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) (Nguyên tắc 4 và `BR-01.2` của tài liệu đó).

  **Lý do nghiệp vụ:** Nếu đơn vị của bản ghi giữ nguyên theo người tạo ban đầu sau khi bàn giao, quản lý của người nhận sẽ không thấy khách hàng đó dù cấp dưới của họ đang là người thực sự xử lý. Khách hàng tiềm năng từ biểu mẫu chưa có ai phụ trách thì phải thuộc đơn vị tiếp nhận để đội nhận việc thấy được nó; nếu không, nó không thuộc phạm vi của ai và bị bỏ quên.

- **`BR-01.4` (Phạm vi dữ liệu theo từng thao tác):** Tập khách hàng một người chạm tới khi thực hiện một thao tác được tính theo mức của **đúng ô** (Khách hàng, thao tác đó) trong vai trò của họ: danh sách và hồ sơ theo mức Xem; sửa theo mức Sửa — kể cả bước tìm bản ghi cần sửa; xóa, xuất, nhập, gán và các thao tác đặc thù của phân hệ (Mục 5.1) theo mức của ô đó. Năm mức **Không có** / **Chỉ của mình** / **Đơn vị của mình** / **Đơn vị và các đơn vị con** / **Toàn workspace**, cách hợp nhất khi một người giữ nhiều vai trò, các nguồn nới phạm vi và nguồn chặn áp đúng [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4, `FEAT-34`, `FEAT-35` và thứ tự hợp nhất `BR-39.6` của tài liệu đó; tài liệu này không định nghĩa lại mức nào. Ô của Doanh nghiệp áp theo cùng cách. Khi người dùng mở một bản ghi ngoài phạm vi, hệ thống xử lý theo `BR-17.3`.

  **Lý do nghiệp vụ:** Một phạm vi chung cho mọi thao tác buộc doanh nghiệp chọn giữa "xem hẹp" làm Marketing không phân khúc được, hoặc "sửa rộng" làm một người sửa được khách của cả tổ chức. Định nghĩa mức phải là một nơi duy nhất cho mọi phân hệ, nếu không "Đơn vị của mình" ở danh bạ và ở cơ hội bán hàng sẽ ra hai tập bản ghi khác nhau cho cùng một người.

- **`BR-01.5` (Nhóm trường Định danh KYC tùy chọn):** Ngoài các kênh liên lạc, hồ sơ khách hàng hỗ trợ lưu **tùy chọn** nhóm trường định danh nhạy cảm phục vụ xác thực hợp đồng: Số Căn cước công dân, Số Hộ chiếu, Ngày cấp, Nơi cấp. Nhóm trường này áp dụng che mặt nạ theo `FEAT-04` và không bắt buộc nhập khi tạo mới.

  **Lý do nghiệp vụ:** Một số ngành phải xác thực danh tính khi ký hợp đồng; nếu hệ thống không có chỗ lưu có kiểm soát, nhân viên sẽ lưu số giấy tờ vào ghi chú tự do — nơi không che, không có thời hạn lưu và không có nhật ký.

- **`BR-01.5b` (Mục đích, điều kiện bật và thời hạn lưu nhóm Định danh KYC) — sàn bắt buộc:**
  - **Mục đích duy nhất được phép:** xác thực danh tính phục vụ ký kết, thực hiện hợp đồng và nghĩa vụ định danh khách hàng theo quy định. **Không** được dùng cho tiếp thị, phân khúc, chấm điểm hay báo cáo.
  - **Điều kiện bật:** nhóm trường **mặc định tắt**. Chỉ Chủ sở hữu cùng Người phụ trách Bảo vệ Dữ liệu bật được, và phải khai báo mục đích sử dụng khi bật (Phụ lục B, `CFG-01-02`).
  - **Khi nhóm trường tắt:** các trường thuộc nhóm **không được lưu** và **không hiển thị với mọi vai trò** — mức "Ẩn trường" áp cho cả bốn cột của bảng `BR-04.3`, kể cả người có quyền chuyên biệt trên nhóm KYC.
  - **Thời hạn lưu:** mặc định **24 tháng** kể từ khi hợp đồng gần nhất của khách hàng kết thúc (Phụ lục B, `CFG-01-03`). Hết thời hạn, hệ thống **tự động khử vĩnh viễn phần định danh** — giữ hồ sơ khách hàng, chỉ xóa các trường KYC. Đây là ngoại lệ có chủ đích (a) của nguyên tắc "hệ thống không tự động xóa" tại `BR-33.5`.
  - **Tài liệu xác minh:** bản chụp giấy tờ định danh thu theo `BR-33.7` bị **xóa vĩnh viễn trong 30 ngày** sau khi yêu cầu tương ứng hoàn tất, không lưu vào hồ sơ khách hàng.

  **Lý do nghiệp vụ:** Đây là nhóm dữ liệu có mức thiệt hại cao nhất nếu rò rỉ và không còn giá trị kinh doanh sau khi hết nghĩa vụ hợp đồng. Giới hạn theo mục đích và theo thời gian là cách duy nhất thu hẹp rủi ro mà vẫn phục vụ được nhu cầu xác thực hợp đồng.

- **`BR-01.6` (Loại Khách hàng — Doanh nghiệp / Cá nhân tiêu dùng):** Mỗi khách hàng cá nhân bắt buộc có **Loại khách hàng** với hai giá trị: **Khách hàng Doanh nghiệp (B2B)** hoặc **Khách hàng Cá nhân tiêu dùng (B2C)** (Phụ lục A, A.14). Giá trị mặc định khi tạo mới là tham số cấu hình (Phụ lục B, `CFG-01-01`). Loại khách hàng chi phối hành vi nghiệp vụ ở nhiều nơi:
  - Khách loại B2C không bị đưa vào danh sách "Liên hệ chưa gắn doanh nghiệp" (`BR-09.1` (b)) và bị loại khỏi mẫu đo `KPI-05`. `KPI-07` áp dụng cho cả hai loại.
  - Quy trình Chuyển đổi Tiềm năng (`FEAT-14`) có tùy chọn **"Không liên kết Doanh nghiệp (khách hàng cá nhân)"**.
  - Chấm điểm hồ sơ (`BR-15.1`) áp bộ tiêu chí riêng cho loại B2C, không dùng tiêu chí "email doanh nghiệp" và "chức danh quản lý".
  - Kiểm tra trùng lặp theo Tiêu chí tham khảo (`BR-17.1`) với loại B2C dùng họ tên kết hợp ngày sinh hoặc địa chỉ thay cho tên công ty.

  **Lý do nghiệp vụ:** Phân hệ phục vụ cả B2B và B2C. Nếu không phân loại, khách bán lẻ trên các kênh mạng xã hội luôn bị coi là hồ sơ thiếu dữ liệu, bị chấm điểm sai, và nhân viên buộc phải tạo "doanh nghiệp" mang tên chính khách hàng — sau vài tháng danh bạ Doanh nghiệp đầy các "công ty" là tên người, làm hỏng báo cáo theo doanh nghiệp và cấu trúc mẹ – con (`FEAT-07`).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-01.1.1` | Màn hình tạo khách hàng | Chỉ nhập email "ceo@company.com", không nhập họ tên, lưu | Tạo thành công; tên hiển thị trên danh sách là "ceo" |
| `AC-01.1.2` | Màn hình tạo khách hàng | Chỉ nhập số điện thoại, lưu | Tạo thành công; tên hiển thị là số điện thoại |
| `AC-01.1.3` | Màn hình tạo khách hàng, chưa nhập email và số điện thoại | Quan sát biểu mẫu trước khi bấm lưu | Biểu mẫu thể hiện rõ yêu cầu "cần ít nhất email hoặc số điện thoại"; nút lưu không cho hoàn tất khi cả hai trống |
| `AC-01.1b.1` | Khách vãng lai nhắn tin qua trò chuyện trực tuyến, không để lại email hay số điện thoại | Tư vấn viên mở hồ sơ khách | Hồ sơ Tạm "Khách vãng lai #…" tồn tại; hồ sơ không xuất hiện trong báo cáo Điểm tiềm năng và báo cáo phễu vòng đời |
| `AC-01.1b.2` | Hồ sơ Tạm đang tồn tại | Tư vấn viên bổ sung email hợp lệ của khách | Hồ sơ trở thành khách hàng chính thức, có giai đoạn theo `BR-12.10`, và được kiểm tra trùng lặp như một bản ghi mới |
| `AC-01.1c.1` | Khách vãng lai đã có Hồ sơ Tạm từ tuần trước | Khách quay lại từ cùng thiết bị và nhắn tin | Không có hồ sơ mới; tư vấn viên thấy lịch sử các phiên trước trên cùng hồ sơ |
| `AC-01.1c.2` | Khách truy cập mở cửa sổ chat lần đầu | Quan sát cửa sổ chat | Có thông báo ngắn về việc ghi nhận phiên để phục vụ hỗ trợ, kèm liên kết chính sách quyền riêng tư |
| `AC-01.2.1` | Không gian làm việc có quốc gia mặc định Việt Nam | Nhập số "0908123456", lưu | Số được lưu và hiển thị dạng +84908123456 |
| `AC-01.2.2` | Màn hình tạo khách hàng | Nhập email "mai.tran@@vinafoods" | Ô email báo sai định dạng ngay khi rời ô, trước khi bấm lưu; không lưu được |
| `AC-01.3.1` | Nhân viên A thuộc Phòng Kinh doanh 1 | Tạo khách hàng, không chỉ định người phụ trách | Người phụ trách là A; khách hàng thuộc Phòng Kinh doanh 1 |
| `AC-01.3.2` | A có Đơn vị chính "Kinh doanh 1", kiêm nhiệm "Dự án Xanh" mức Đầy đủ và "Hội chợ" mức Chỉ xem; A phụ trách khách K | Thành viên "Dự án Xanh" và thành viên "Hội chợ" (cùng có Xem = Đơn vị của mình) mở danh sách khách hàng | Thành viên "Dự án Xanh" thấy K; thành viên "Hội chợ" không thấy K |
| `AC-01.3.3` | Biểu mẫu website có đơn vị tiếp nhận "Kinh doanh – Đà Nẵng"; khách mới gửi biểu mẫu, chưa được phân bổ | Thành viên "Kinh doanh – Đà Nẵng" mở hàng đợi khách hàng tiềm năng | Thấy khách mới, chưa có Người phụ trách; thành viên đơn vị khác không thấy |
| `AC-01.3.4` | Khách K thuộc "Kinh doanh 1" do A phụ trách | Đổi Người phụ trách sang B thuộc "Kinh doanh 2" | K thuộc "Kinh doanh 2" ngay lập tức; quản lý "Kinh doanh 1" không còn thấy K qua mức Đơn vị của mình |
| `AC-01.4.1` | Nhân viên A có (Khách hàng, Xem) = Chỉ của mình, phụ trách 3 khách hàng, được chia sẻ 1 khách hàng; phòng có 200 khách hàng | A mở danh sách khách hàng | Chỉ thấy đúng 4 khách hàng |
| `AC-01.4.3` | Nhân viên Kinh doanh B theo mặc định: (Khách hàng, Xem) = Đơn vị của mình, (Khách hàng, Sửa) = Chỉ của mình | B mở hồ sơ một khách của đồng nghiệp cùng đơn vị | Xem được; các trường không sửa được kèm giải thích; lượt xem được ghi là đọc ngoài phạm vi phụ trách (`NFR-07` mục 13) |
| `AC-01.4.4` | C có (Khách hàng, Xem) = Toàn workspace, (Khách hàng, Xuất) = Chỉ của mình | C xuất từ danh sách đang hiển thị khách của mọi đơn vị | Tệp xuất chỉ chứa khách C phụ trách |
| `AC-01.4.2` | Tiếp nối AC-01.4.1 | A mở trực tiếp đường dẫn tới hồ sơ một khách hàng của đồng nghiệp | Không thấy hồ sơ đầy đủ; thấy thông tin tối thiểu để nhận diện và ba hành động theo `BR-17.3` |
| `AC-01.5.1` | Nhóm Định danh KYC đã bật | Người phụ trách nhập Số Căn cước công dân cho khách, lưu | Lưu thành công; trường hiển thị ở mức che theo `BR-04.3` |
| `AC-01.5b.1` | Nhóm Định danh KYC đang tắt | Bất kỳ vai trò nào, kể cả Chủ sở hữu, mở biểu mẫu tạo/sửa khách hàng | Không có trường nào thuộc nhóm KYC trên biểu mẫu |
| `AC-01.5b.2` | Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu mở cấu hình nhóm KYC | Bật nhóm nhưng để trống mục đích sử dụng, lưu | Từ chối, yêu cầu khai báo mục đích |
| `AC-01.5b.3` | Khách hàng có dữ liệu KYC, hợp đồng gần nhất kết thúc tại mốc T, thời hạn lưu là 24 tháng | Thời gian trôi tới T + 24 tháng + 1 ngày | Các trường KYC của khách bị xóa vĩnh viễn; hồ sơ, giai đoạn và dòng thời gian của khách còn nguyên |
| `AC-01.5b.4` | Tiếp nối AC-01.5b.3, thời gian mới tới T + 24 tháng − 1 ngày | Mở hồ sơ | Dữ liệu KYC vẫn còn |
| `AC-01.6.1` | Tham số `CFG-01-01` đặt "Bắt buộc người dùng chọn" | Tạo khách hàng mà không chọn Loại khách hàng | Biểu mẫu yêu cầu chọn trước khi lưu |
| `AC-01.6.2` | Khách hàng loại B2C không có liên kết doanh nghiệp | Mở danh sách "Liên hệ chưa gắn doanh nghiệp" | Khách này không có trong danh sách |
| `AC-01.6.3` | Khách hàng tiềm năng loại B2C | Mở hộp thoại Chuyển đổi Tiềm năng | Có tùy chọn "Không liên kết Doanh nghiệp (khách hàng cá nhân)" và chuyển đổi được mà không tạo doanh nghiệp |

---

#### FEAT-02 — Hồ sơ Chi tiết Khách hàng 360 độ

**Mô tả nghiệp vụ:** Màn hình tổng hợp toàn diện mọi thông tin của một khách hàng: thông tin cá nhân, doanh nghiệp trực thuộc, giai đoạn vòng đời, điểm tiềm năng, cơ hội bán hàng, vé hỗ trợ, công việc, ghi chú và lịch sử tương tác.

**Vai trò sử dụng chính:** Mọi người dùng có quyền xem khách hàng.

**Điều kiện tiên quyết:** Người xem có ô (Khách hàng, Xem) bao phủ bản ghi, hoặc có quyền đọc tự động theo `BR-35.4`.

**Luồng chính:**

1. Người dùng mở hồ sơ khách hàng từ danh sách, kết quả tìm kiếm, hội thoại hoặc vé.
2. Hệ thống hiển thị ba khu vực theo `BR-02.1`, áp mức che theo `FEAT-04`.
3. Người dùng dùng các hành động nhanh mà mình có quyền (`BR-02.2`).

**Quy tắc nghiệp vụ:**

- **`BR-02.1` (Bố cục chuẩn):** Hồ sơ gồm ba khu vực: **khung tóm tắt** (thông tin chính, điểm tiềm năng, trạng thái liên lạc), **khu vực trung tâm** (Dòng thời gian 360 độ, các thẻ Ghi chú, Công việc, Vé hỗ trợ, Cơ hội) và **khung liên kết** (doanh nghiệp trực thuộc, mối quan hệ cá nhân). Mọi giá trị hiển thị trên hồ sơ tuân theo chính sách che mặt nạ tại `FEAT-04`.

  **Lý do nghiệp vụ:** Người phục vụ khách cần thấy toàn cảnh trong một màn hình; nếu thông tin nằm rải ở nhiều màn hình, họ trả lời khách khi chưa biết khách vừa khiếu nại hay đang thương lượng một cơ hội lớn.

- **`BR-02.2` (Hành động nhanh & phân quyền):** Hồ sơ cho phép thực hiện nhanh: gửi email, gọi điện, tạo ghi chú, tạo công việc, tạo vé hỗ trợ. Hai hành động bị kiểm soát theo quyền:
  - **(a) "Tạo cơ hội mới"** chỉ hiển thị khi người dùng có quyền tạo Cơ hội bán hàng (quyền này thuộc [`deals-pipeline-srs.md`](./deals-pipeline-srs.md)). Người không có quyền — tiêu biểu là Nhân viên Hỗ trợ — thấy hành động **"Gợi ý Cơ hội"** thay thế, để chuyển nhu cầu mua cho đội kinh doanh.
  - **(b) "Chuyển giai đoạn vòng đời"** chỉ hiển thị khi ô (Khách hàng, Chuyển giai đoạn vòng đời) — thao tác đặc thù của phân hệ, Mục 5.1 — của người dùng bao phủ bản ghi. Theo **ma trận mặc định của vai trò dựng sẵn**, Nhân viên Kinh doanh, Quản lý và Người có toàn quyền có ô này; Nhân viên Hỗ trợ, Marketing và Quản lý Marketing **không** có. Đây là mặc định, **không phải sàn bắt buộc**: doanh nghiệp nới được bằng cách điều chỉnh ô qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02` hoặc đặt ô này trong vai trò tự tạo.

  **Lý do nghiệp vụ:** Tuyến Hỗ trợ tiếp xúc khách nhưng không thẩm định được mức độ sẵn sàng mua; Marketing có tầm nhìn toàn tổ chức ở dạng chỉ đọc, nên nếu được chuyển giai đoạn thủ công thì một người có thể đổi giai đoạn của mọi bản ghi trong tổ chức. Giai đoạn của các bản ghi do Marketing nuôi dưỡng vẫn tiến lên được **qua đường tự động** theo ngưỡng điểm (`BR-15.5`).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-02.1.1` | Khách hàng có 2 cơ hội, 1 vé hỗ trợ, 3 ghi chú, 1 liên kết doanh nghiệp | Người phụ trách mở hồ sơ | Thấy đủ ba khu vực; các thẻ Cơ hội, Vé hỗ trợ, Ghi chú hiển thị đúng số lượng; khung liên kết có doanh nghiệp trực thuộc |
| `AC-02.2.1` | Nhân viên Hỗ trợ không có quyền tạo Cơ hội bán hàng | Mở hồ sơ khách hàng | Không có "Tạo cơ hội mới"; có "Gợi ý Cơ hội" |
| `AC-02.2.2` | Nhân viên Marketing, cấu hình mặc định | Mở hồ sơ khách hàng | Không có hành động "Chuyển giai đoạn vòng đời" |
| `AC-02.2.3` | Nhân viên Kinh doanh là Người phụ trách | Mở hồ sơ khách hàng | Có hành động "Chuyển giai đoạn vòng đời" |
| `AC-02.2.4` | Doanh nghiệp điều chỉnh ô (Khách hàng, Chuyển giai đoạn vòng đời) của vai trò Marketing thành Toàn workspace qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02` | Nhân viên Marketing mở hồ sơ | Có hành động "Chuyển giai đoạn vòng đời" |
| `AC-02.2.5` | Vai trò tự tạo "Chăm sóc khách hàng" có ô (Khách hàng, Chuyển giai đoạn vòng đời) = Đơn vị của mình | Người giữ vai trò mở hồ sơ khách của đơn vị mình và của đơn vị khác | Có hành động trên hồ sơ của đơn vị mình; không có trên hồ sơ của đơn vị khác |

---

#### FEAT-03 — Quản lý Thẻ phân loại Hàng loạt

**Mô tả nghiệp vụ:** Gắn hoặc gỡ nhiều thẻ phân loại cho một hoặc hàng loạt khách hàng cùng lúc, phục vụ lọc và phân khúc chiến dịch.

**Vai trò sử dụng chính:** Nhân viên Kinh doanh, Nhân viên Marketing, Quản trị viên.

**Điều kiện tiên quyết:** Người dùng có ô (Khách hàng, Gắn thẻ phân loại) — thao tác đặc thù, Mục 5.1 — khác Không có.

**Luồng chính:**

1. Người dùng chọn một hoặc nhiều khách hàng trên danh sách.
2. Chọn "Gắn thẻ" hoặc "Gỡ thẻ", nhập hoặc chọn tên thẻ.
3. Hệ thống áp thao tác trên các bản ghi mà ô Gắn thẻ của người dùng bao phủ và báo số bản ghi bị bỏ qua.

**Quy tắc nghiệp vụ:**

- **`BR-03.1` (Gắn/gỡ hàng loạt):** Người dùng chọn nhiều khách hàng trên danh sách và gắn hoặc gỡ thẻ cho toàn bộ lựa chọn trong một thao tác. Thao tác chỉ áp dụng trên các bản ghi mà ô (Khách hàng, Gắn thẻ phân loại) của người thực hiện bao phủ; bản ghi nằm ngoài được bỏ qua và nêu số lượng.

  **Lý do nghiệp vụ:** Phân khúc chiến dịch cần gắn thẻ cho hàng trăm khách một lần; nhưng nếu thao tác hàng loạt bỏ qua phạm vi, chọn "tất cả" trên một danh sách lọc sẽ ghi lên cả những bản ghi người đó không được chạm tới.

- **`BR-03.2` (Chuẩn hóa tên thẻ):** Tên thẻ không phân biệt chữ hoa/thường, tự động loại bỏ khoảng trắng thừa ở hai đầu, dài tối đa 50 ký tự. Số thẻ tối đa trên một bản ghi theo gói dịch vụ tại `NFR-11`.

  **Lý do nghiệp vụ:** Nếu "VIP", "vip" và " VIP " là ba thẻ khác nhau, phân khúc chiến dịch theo thẻ sẽ bỏ sót khách và báo cáo theo thẻ bị chia nhỏ vô nghĩa.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-03.1.1` | Danh sách có 20 khách hàng được chọn | Gắn thẻ "Hội thảo Q3" | Cả 20 khách hàng mang thẻ "Hội thảo Q3" |
| `AC-03.1.2` | 20 khách hàng đang mang thẻ "Hội thảo Q3" | Chọn cả 20, gỡ thẻ | Không khách hàng nào còn thẻ đó |
| `AC-03.2.1` | Không gian làm việc đã có thẻ "VIP" | Gắn thẻ " vip " cho một khách hàng | Khách hàng được gắn đúng thẻ "VIP" đã có; không phát sinh thẻ mới |
| `AC-03.2.2` | Ô nhập tên thẻ | Nhập tên dài 51 ký tự | Ô nhập báo vượt giới hạn 50 ký tự trước khi lưu; không tạo được thẻ |
| `AC-03.2.3` | Gói tiêu chuẩn, khách hàng đã có 50 thẻ | Gắn thêm thẻ thứ 51 | Từ chối, nêu rõ giới hạn số thẻ của gói |
| `AC-03.1.3` | Nhân viên A có ô Gắn thẻ = Chỉ của mình; danh sách được chọn có 20 khách, 5 khách do người khác phụ trách | Gắn thẻ "Hội thảo Q3" | 15 khách mang thẻ; màn hình báo 5 bản ghi bị bỏ qua vì ngoài phạm vi |

---

#### FEAT-04 — Bảo vệ Dữ liệu Nhạy cảm & Mở khóa Mặt nạ

**Mô tả nghiệp vụ:** Bảo vệ dữ liệu cá nhân nhạy cảm bằng cơ chế che mặt nạ phân tầng theo **quan hệ của người xem với bản ghi**, thay vì che đồng loạt. Nguyên tắc nền tảng: tách **"quyền sử dụng để liên lạc"** (nhân viên phục vụ khách cần dùng số điện thoại hằng ngày) khỏi **"quyền xem giá trị thật"** (chỉ cần khi thực sự phải đọc, và luôn để lại dấu vết).

> **Tính năng này là nguồn duy nhất về chính sách che mặt nạ dữ liệu trong toàn tài liệu.** Mọi nơi khác nói về mặt nạ (`NFR-06`, `BR-35.4`, `BR-28.1`, các kịch bản UAT) dẫn chiếu về đây và không phát biểu lại chính sách theo cách riêng.

**Vai trò sử dụng chính:** Mọi người dùng xem hồ sơ khách hàng (chịu chi phối của chính sách); riêng thao tác mở khóa yêu cầu **quyền Mở khóa mặt nạ** được cấp riêng.

**Điều kiện tiên quyết:** Không có cho việc xem theo chính sách che; mở khóa cần ô (Khách hàng, Mở khóa mặt nạ) bao phủ bản ghi (`BR-04.4`).

**Luồng chính:**

1. Khi một giá trị nhạy cảm được hiển thị ở bất kỳ đâu, hệ thống xác định nhóm trường (`BR-04.1`) và quan hệ của người xem với bản ghi (`BR-04.3`), rồi áp mức hiển thị hạn chế nhất giữa các lớp che (`BR-04.7`).
2. Người có quyền bấm biểu tượng mở khóa trên một trường; hệ thống kiểm tra hạn mức (`BR-04.5`), hiển thị giá trị đầy đủ và ghi nhật ký (`NFR-07`).
3. Người dùng liên lạc với khách qua kênh trong hệ thống mà không cần mở khóa (`BR-04.6`).

**Quy tắc nghiệp vụ:**

- **`BR-04.1` (Ba nhóm trường nhạy cảm):** Dữ liệu nhạy cảm được phân đúng ba nhóm:
  - **Nhóm 1 — Kênh liên lạc công việc:** email theo tên miền doanh nghiệp, số điện thoại di động và số điện thoại bàn dùng cho công việc.
  - **Nhóm 2 — Kênh liên lạc cá nhân:** email cá nhân (tên miền dịch vụ thư công cộng), số điện thoại được khách hàng khai báo là riêng tư.
  - **Nhóm 3 — Định danh KYC:** Số Căn cước công dân, Số Hộ chiếu, Ngày cấp, Nơi cấp (`BR-01.5`).

  **Lý do nghiệp vụ:** Mỗi nhóm có mức thiệt hại khác nhau khi lộ và nhu cầu sử dụng khác nhau trong vận hành; gộp chung buộc chọn hoặc che quá chặt kênh liên lạc công việc mà nhân viên cần dùng hằng ngày, hoặc che quá lỏng giấy tờ định danh.

- **`BR-04.2` (Bốn mức hiển thị):** **Đầy đủ** (thấy trọn giá trị) · **Che một phần** (ví dụ "090****567", "m***@vinafoods.vn" — đủ để nhận diện và đối chiếu với khách, không đủ để sao chép sử dụng) · **Che hoàn toàn** (chỉ hiện dấu hiệu có dữ liệu, không hiện ký tự nào) · **Ẩn trường** (không hiển thị trường trên giao diện).

  **Lý do nghiệp vụ:** Phân biệt "đủ để nhận diện" với "đủ để sao chép" là điều cho phép nhân viên xác minh đúng khách mà không mang được danh bạ ra ngoài; chỉ có hai mức hiện/ẩn thì không có điểm cân bằng đó.

- **`BR-04.3` (Chính sách hiển thị theo quan hệ với bản ghi):** Chính sách mặc định chuẩn hệ thống như bảng dưới; tenant cấu hình được trong giới hạn sàn (Phụ lục B, `CFG-04-01`).

| Nhóm trường | (A) Người phụ trách & thành viên Đội ngũ phụ trách ở mức **Chỉnh sửa** (điều kiện tại `BR-04.5b`) | (B) Người trong phạm vi dữ liệu, **gồm thành viên Đội ngũ phụ trách ở mức Chỉ đọc** | (C) Người có **quyền đọc tạm**: đang xử lý vé/hội thoại của khách (`BR-35.4`), hoặc được hệ thống tự cấp khi yêu cầu quyền truy cập quá hạn hai lần (`BR-17.2c`) | (D) Người ngoài phạm vi dữ liệu |
| --- | --- | --- | --- | --- |
| **1. Kênh liên lạc công việc** | **Đầy đủ** | Che một phần | Che một phần | Che hoàn toàn |
| **2. Kênh liên lạc cá nhân** | Che một phần | Che một phần | Che một phần | Che hoàn toàn |
| **3. Định danh KYC** | Che hoàn toàn | Che hoàn toàn | Che hoàn toàn | Ẩn trường |

  **Lý do nghiệp vụ:** Theo từng cột — (A) Nếu che cả kênh liên lạc công việc với chính người phụ trách, tổ chức buộc phải cấp quyền Mở khóa mặt nạ cho toàn bộ đội kinh doanh ngay tuần đầu — mặt nạ thành hình thức và `KPI-06` mất khả năng phát hiện bất thường. (B) Người trong phạm vi nhưng không trực tiếp phục vụ khách chỉ cần nhận diện, không cần sao chép. (C) Nhân viên Hỗ trợ đang xử lý vé/hội thoại cần đủ thông tin để **xác minh đúng người** và gọi lại khi chat bị ngắt; nếu che hoàn toàn thì họ phải hỏi lại khách số điện thoại mà hệ thống đã có và tổ chức lại phải cấp quyền mở khóa cho toàn tuyến Hỗ trợ. (D) Người không có quan hệ công việc nào với bản ghi không có nhu cầu nghiệp vụ để thấy dữ liệu liên lạc.

  Chính sách áp dụng đồng nhất ở **mọi nơi giá trị xuất hiện**: hồ sơ 360 độ, danh sách khách hàng, danh sách nhân sự trên hồ sơ doanh nghiệp (`BR-08.1`), khung Ngữ cảnh Khách hàng (`BR-28.1`), kết quả tìm kiếm và tệp dữ liệu xuất (`FEAT-25`, `NFR-06`).

- **`BR-04.4` (Mở khóa có kiểm toán — lối mở duy nhất):** Người dùng có quyền Mở khóa mặt nạ được nâng mức hiển thị lên **Đầy đủ** cho các trường Nhóm 1 và Nhóm 2 **trong phạm vi dữ liệu của mình**, và cho Nhóm 3 nếu được cấp thêm quyền chuyên biệt trên nhóm Định danh KYC. Mỗi lượt mở khóa bắt buộc ghi nhật ký theo `NFR-07`. Quyền Mở khóa mặt nạ là thao tác đặc thù (Khách hàng, Mở khóa mặt nạ) mang mức truy cập như mọi ô (Mục 5.1), mặc định Không có ở mọi vai trò dựng sẵn và chỉ có khi được cấp; quyền mở khóa nhóm Định danh KYC là ô riêng (Khách hàng, Mở khóa Định danh KYC). Quyền Mở khóa mặt nạ **không** mở được dữ liệu ở cột (D) — người ngoài phạm vi phải xin quyền truy cập theo `BR-17.3` trước.

  **Lý do nghiệp vụ:** Mở khóa không được là con đường đi vòng qua phạm vi dữ liệu; nếu không, một quyền cấp để phục vụ khách của mình trở thành quyền đọc dữ liệu liên lạc của toàn tổ chức.

- **`BR-04.5` (Hạn mức mở khóa & chống lấy dữ liệu hàng loạt) — sàn bắt buộc:** Mỗi người dùng có hạn mức mở khóa mặc định **50 bản ghi/ngày**, cấu hình được trong khoảng **10–200** (Phụ lục B, `CFG-04-02`). **Không tồn tại lựa chọn "không giới hạn".** Khi vượt hạn mức: thao tác mở khóa bị tạm chặn **đến hết ngày** (Mục 2.3), hệ thống gửi cảnh báo tới Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu, và ghi vào báo cáo truy cập bất thường.

  **Lý do nghiệp vụ:** Nếu tắt được hạn mức, một nhân viên sắp rời tổ chức có thể mở mặt nạ hàng nghìn khách hàng trong một buổi và nhật ký chỉ ghi lại thụ động sau khi dữ liệu đã ra ngoài.

- **`BR-04.5b` (Kiểm soát mức hiển thị Đầy đủ ở cột (A)) — sàn bắt buộc:** Cột (A) là mức phơi bày cao nhất (thấy giá trị thật không cần mở khóa), nên phải có kiểm soát tương đương thao tác mở khóa:
  - **Chỉ thành viên Đội ngũ phụ trách ở mức Chỉnh sửa** được hưởng cột (A). Thành viên ở mức **Chỉ đọc** và mọi thành viên mang vai trò tham gia **"Quan sát"** (Phụ lục A, A.13) áp cột (B).
  - **Mọi lượt thêm thành viên vào Đội ngũ phụ trách bắt buộc ghi nhật ký** (`BR-35.6`), và số bản ghi mà **một người được thêm vào** với mức Chỉnh sửa bị giới hạn **100 bản ghi/tháng** (Phụ lục B, `CFG-04-04`), đếm theo người nhận quyền chứ không theo người đi thêm; vượt ngưỡng thì cảnh báo Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu.
  - **Báo cáo phơi bày định kỳ:** hằng tháng hệ thống báo cáo cho Người phụ trách Bảo vệ Dữ liệu số bản ghi mà mỗi người dùng đang xem được ở mức Đầy đủ, để rà soát tích tụ quyền bất thường.

  **Lý do nghiệp vụ:** Không có kiểm soát này, việc thêm người vào Đội ngũ phụ trách trở thành đường vòng qua hạn mức tại `BR-04.5` — thứ cần kiểm soát là **mức phơi bày tích tụ của người nhận quyền**, không phải số thao tác của người chia sẻ.

- **`BR-04.6` (Liên lạc không cần mở khóa):** Các hành động liên lạc **bên trong hệ thống** (bấm gọi, gửi email, gửi tin nhắn qua kênh đã tích hợp) thực hiện được ở **mọi mức hiển thị từ Che một phần trở lên**, không yêu cầu mở khóa và không tính vào hạn mức tại `BR-04.5`. Các hành động này **bắt buộc ghi nhật ký** theo `NFR-07` và chịu hạn mức riêng: mặc định **200 lượt liên lạc/người/ngày** (Phụ lục B, `CFG-04-03`); vượt hạn mức thì hành động liên lạc bị chặn tới hết ngày.

  **Lý do nghiệp vụ:** Nhân viên phải liên lạc được với khách mà không cần nhìn thấy số thật; nhưng nếu chính chức năng liên lạc không có nhật ký và hạn mức, nó trở thành cách khai thác danh bạ không để lại dấu vết.

- **`BR-04.7` (Khai báo trường nhạy cảm của hệ thống theo khung che dữ liệu dùng chung):** Phân hệ khai báo với khung che dữ liệu tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-40`:
  - **Trường nhạy cảm của hệ thống — Khách hàng:** ba nhóm tại `BR-04.1`. **Doanh nghiệp:** không có trường nhạy cảm của hệ thống — tên, mã số thuế, địa chỉ trụ sở, số tổng đài, doanh thu và quy mô nhân sự là thông tin pháp nhân và phân loại (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-40.4`); doanh nghiệp muốn che thì tự khai báo bằng phân quyền trường.
  - **Mẫu che riêng của phân hệ** (thay cho mẫu che mặc định của khung): số điện thoại giữ 3 số đầu và 3 số cuối ("090****567"); email giữ ký tự đầu và tên miền ("m***@vinafoods.vn"); nhóm Định danh KYC chỉ có mức Che hoàn toàn hoặc Ẩn trường, không có mẫu che một phần.
  - **Quyền xem đầy đủ:** cột (A) của `BR-04.3` cho Nhóm 1; ô (Khách hàng, Mở khóa mặt nạ) cho Nhóm 1 và Nhóm 2; ô (Khách hàng, Mở khóa Định danh KYC) cho Nhóm 3, chịu sàn tại `CFG-04-01`.
  - **Sàn của ô (Khách hàng, Mở khóa Định danh KYC) — sàn bắt buộc:** mức cao nhất đặt được qua bất kỳ vai trò nào là **Chỉ của mình** — mọi mức từ Đơn vị của mình trở lên vượt sàn và bị từ chối, trên vai trò tự tạo lẫn khi điều chỉnh vai trò dựng sẵn qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02`. Mỗi lượt đặt ô này khác Không có, ở bất kỳ vai trò nào, cần **thẩm quyền bổ sung**: Người phụ trách Bảo vệ Dữ liệu phê duyệt (hoặc người thứ hai theo `NFR-14`), ngoài trần năng lực của người điều chỉnh; thu hẹp ô về Không có không cần phê duyệt. **Sàn này áp cả lên Người có toàn quyền** (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.3`): họ không có mức Toàn workspace thường trực trên ô này và không xem được nhóm KYC ở mức Đầy đủ hàng loạt; họ chỉ mở khóa từng bản ghi theo `BR-04.4`, mỗi lượt có nhật ký và chịu hạn mức `CFG-04-02`.
  - **Hạn chế thắng giữa các lớp:** một trường đồng thời chịu chính sách của tính năng này và phân quyền trường do doanh nghiệp đặt hiển thị theo lớp hạn chế hơn; tư cách thành viên Đội ngũ phụ trách, quyền đọc tự động hay bất kỳ lượt cấp nào không nới lỏng lớp che nào (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-40.3` và bước 4 của `BR-39.6`).
  - **Tác nhân AI** luôn nhận giá trị đã che của mọi trường nhạy cảm, kể cả khi người dùng mà nó phục vụ đang ở cột (A) hoặc có quyền mở khóa (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-40.1`).

  **Lý do nghiệp vụ:** Giấy tờ định danh chỉ cần cho người ký hợp đồng với chính khách mình phụ trách; mở cho cả đơn vị là phơi bày nhóm dữ liệu rủi ro nhất cho người không có nhu cầu. Khung che dữ liệu dùng chung chỉ thực thi được khi mỗi phân hệ khai báo rõ trường nào nhạy cảm, che theo mẫu nào và ai được xem đầy đủ; nếu không, mỗi màn hình tự diễn giải và cùng một số điện thoại hiện ba kiểu khác nhau. Mẫu che riêng giữ đủ đầu và cuối số để nhân viên đối chiếu khi khách đọc số qua điện thoại, điều mà mẫu chỉ giữ 4 số cuối không làm được.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-04.1.1` | Khách hàng có email cá nhân thuộc dịch vụ thư công cộng | Người phụ trách mở hồ sơ | Email cá nhân hiển thị **che một phần** (Nhóm 2, cột A), trong khi email công việc hiển thị đầy đủ |
| `AC-04.3.1` | Nhân viên A là Người phụ trách | Mở hồ sơ | Số điện thoại và email công việc hiển thị **đầy đủ**, không cần mở khóa |
| `AC-04.3.2` | Quản lý Kinh doanh B có bản ghi trong phạm vi đơn vị, không phải Người phụ trách, không thuộc Đội ngũ phụ trách, chưa có quyền mở khóa | Mở hồ sơ | Số điện thoại hiện "090****567", email công việc hiện "m***@vinafoods.vn"; trường KYC che hoàn toàn |
| `AC-04.3.3` | Nhân viên Hỗ trợ C đang xử lý một hội thoại mở của khách, bản ghi ngoài phạm vi của C | Mở hồ sơ qua hội thoại | Kênh liên lạc công việc và cá nhân **che một phần**; KYC che hoàn toàn |
| `AC-04.3.4` | Người dùng D ngoài phạm vi, không có quyền đọc tạm | Mở đường dẫn tới hồ sơ | Chỉ thấy thông tin tối thiểu theo `BR-17.3`; không thấy ký tự nào của kênh liên lạc; trường KYC không xuất hiện |
| `AC-04.3.5` | Cùng bối cảnh AC-04.3.2 | B mở **danh sách khách hàng** của đơn vị | Cột số điện thoại và email trên danh sách che một phần, đúng như trên hồ sơ |
| `AC-04.3.6` | Cùng bối cảnh AC-04.3.2 | B mở **hồ sơ doanh nghiệp** mà khách hàng trực thuộc, xem danh sách nhân sự | Số điện thoại, email của khách trong danh sách nhân sự che một phần |
| `AC-04.3.7` | Cùng bối cảnh AC-04.3.3 | C xem **khung Ngữ cảnh Khách hàng** trong hộp thư | Kênh liên lạc che một phần, đúng cột (C) |
| `AC-04.3.8` | Cùng bối cảnh AC-04.3.2, B có quyền xuất dữ liệu | B xuất danh sách khách hàng của đơn vị ra tệp | Cột số điện thoại, email trong tệp ở đúng mức che một phần như B thấy trên màn hình |
| `AC-04.3.9` | Cùng bối cảnh AC-04.3.2 | B tìm kiếm theo tên khách hàng | Kết quả tìm kiếm hiển thị kênh liên lạc ở mức che một phần |
| `AC-04.4.1` | Quản lý Kinh doanh có quyền Mở khóa mặt nạ, bản ghi trong phạm vi | Bấm biểu tượng mở khóa trên số điện thoại | Số hiện đầy đủ; nhật ký ghi "đã mở khóa trường Số điện thoại của bản ghi X" kèm người và thời điểm, **không** chứa giá trị số |
| `AC-04.4.2` | Người dùng có quyền Mở khóa mặt nạ, bản ghi ngoài phạm vi | Mở hồ sơ | Không có biểu tượng mở khóa; chỉ có các hành động xin quyền theo `BR-17.3` |
| `AC-04.4.3` | Người dùng có quyền Mở khóa mặt nạ nhưng không có quyền chuyên biệt trên nhóm KYC | Tìm cách mở khóa Số Căn cước công dân | Không mở khóa được; trường vẫn che hoàn toàn |
| `AC-04.5.1` | Người dùng đã mở khóa 50 bản ghi trong ngày, hạn mức 50 | Mở khóa bản ghi thứ 51 | Bị chặn tới hết ngày; Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu nhận cảnh báo; lượt này có trong báo cáo truy cập bất thường |
| `AC-04.5.2` | Tiếp nối AC-04.5.1 | Ngày hôm sau, sau 00:00 theo múi giờ không gian làm việc, mở khóa lại | Mở khóa được bình thường |
| `AC-04.5.3` | Chủ sở hữu mở cấu hình hạn mức mở khóa | Tìm lựa chọn "không giới hạn"; nhập 250 | Không có lựa chọn "không giới hạn"; giá trị 250 bị từ chối, nêu miền 10–200 |
| `AC-04.5b.1` | Nhân viên D được thêm vào Đội ngũ phụ trách ở mức Chỉ đọc | D mở hồ sơ | Kênh liên lạc công việc **che một phần** (cột B) |
| `AC-04.5b.2` | Nhân viên B đã được thêm vào 100 bản ghi ở mức Chỉnh sửa trong tháng, bởi nhiều người khác nhau | Một người khác thêm B vào bản ghi thứ 101 | Lượt thêm được ghi nhật ký; Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu nhận cảnh báo vượt ngưỡng |
| `AC-04.5b.3` | Cuối tháng | Người phụ trách Bảo vệ Dữ liệu mở báo cáo phơi bày | Báo cáo liệt kê số bản ghi mỗi người đang xem được ở mức Đầy đủ |
| `AC-04.6.1` | Cùng bối cảnh AC-04.3.2 | B bấm "Gọi" trên số điện thoại đang che một phần | Cuộc gọi thực hiện được, không yêu cầu mở khóa, không trừ hạn mức mở khóa; hành động liên lạc có trong nhật ký |
| `AC-04.6.2` | Người dùng đã thực hiện 200 lượt liên lạc trong ngày, hạn mức 200 | Bấm gửi email lần thứ 201 | Bị chặn tới hết ngày, nêu rõ lý do hạn mức |
| `AC-04.6.3` | Cùng bối cảnh AC-04.3.4 (cột D, che hoàn toàn) | Tìm nút "Gọi" | Không có hành động liên lạc |
| `AC-04.7.1` | Nhân viên A là Người phụ trách, thấy số điện thoại công việc đầy đủ (cột A) | A hỏi trợ lý AI số điện thoại của khách | Trợ lý AI trả số đã che dạng "090****567" |
| `AC-04.7.2` | Doanh nghiệp đặt phân quyền trường "Che hoàn toàn" cho email cá nhân với nhóm "Thực tập sinh" | Một thực tập sinh là thành viên Đội ngũ phụ trách mức Chỉnh sửa mở hồ sơ | Email cá nhân che hoàn toàn (lớp hạn chế hơn thắng) |
| `AC-04.7.3` | Nhân viên Kinh doanh không có quyền mở khóa | Mở hồ sơ một doanh nghiệp | Thấy đầy đủ mã số thuế, số tổng đài, doanh thu và quy mô nhân sự |
| `AC-04.7.4` | Người có ô Mở khóa mặt nạ nhưng không có ô Mở khóa Định danh KYC | Mở hồ sơ có Số Căn cước công dân | Trường KYC che hoàn toàn, không có biểu tượng mở khóa trên trường đó |
| `AC-04.7.5` | Người có quyền Quản lý vai trò điều chỉnh vai trò Quản lý | Đặt (Khách hàng, Mở khóa Định danh KYC) = Đơn vị của mình | Lựa chọn bị vô hiệu kèm giải thích sàn: mức tối đa là Chỉ của mình |
| `AC-04.7.6` | Như trên | Đặt ô đó = Chỉ của mình, chưa có phê duyệt | Lượt điều chỉnh ở trạng thái chờ Người phụ trách Bảo vệ Dữ liệu phê duyệt; ô chưa có hiệu lực |
| `AC-04.7.7` | Tiếp nối AC-04.7.6 | Người phụ trách Bảo vệ Dữ liệu phê duyệt | Ô có hiệu lực; người giữ vai trò mở khóa được KYC của khách mình phụ trách, không mở được của khách đồng nghiệp |
| `AC-04.7.9` | Quản trị viên mở danh sách khách hàng có nhóm KYC | Quan sát cột Số Căn cước công dân | Che hoàn toàn trên danh sách; Quản trị viên chỉ mở khóa được từng bản ghi, mỗi lượt có nhật ký và trừ hạn mức |
| `AC-04.7.8` | Vai trò có ô Mở khóa Định danh KYC = Chỉ của mình | Thu hẹp về Không có | Lưu ngay, không cần phê duyệt |

---

#### FEAT-05 — Thùng rác Khách hàng & Phục hồi Bản ghi

**Mô tả nghiệp vụ:** Khi xóa một khách hàng, hệ thống xóa mềm và đưa vào Thùng rác trong một thời hạn lưu; người có thẩm quyền khôi phục được nguyên vẹn bản ghi.

**Vai trò sử dụng chính:** Người có **quyền Xóa** trên khách hàng, Quản trị viên.

**Điều kiện tiên quyết:** Người xóa hoặc khôi phục có ô (Khách hàng, Xoá) bao phủ bản ghi.

**Luồng chính:**

1. Người dùng xóa một khách hàng; hệ thống xóa mềm và đưa vào Thùng rác (`BR-05.1`).
2. Người có quyền mở Thùng rác, xem danh sách và khôi phục khi cần (`BR-05.2`, `BR-05.3`).
3. Tiến trình hệ thống dọn dẹp bản ghi quá thời hạn lưu, trừ bản ghi vướng chốt an toàn (`BR-05.4`, `BR-05.6`).

**Quy tắc nghiệp vụ:**

- **`BR-05.1` (Xóa mềm):** Bản ghi bị xóa được ẩn khỏi toàn bộ danh sách, tìm kiếm và báo cáo thông thường, ghi nhận thời điểm xóa và người xóa.

  **Lý do nghiệp vụ:** Xóa nhầm là sai sót hằng ngày; xóa cứng ngay lập tức thì một cú bấm nhầm làm mất vĩnh viễn lịch sử khách hàng mà không ai phục hồi được.

- **`BR-05.2` (Màn hình Thùng rác):** Màn hình Thùng rác liệt kê các bản ghi đã xóa kèm ngày xóa, người xóa và ngày dự kiến xóa vĩnh viễn.

  **Lý do nghiệp vụ:** Người khôi phục cần biết ai xóa, lúc nào và còn bao lâu trước khi mất hẳn để quyết định kịp thời; thiếu ngày dự kiến xóa vĩnh viễn, bản ghi biến mất trước khi có người nhận ra.

- **`BR-05.3` (Khôi phục):** Người có ô (Khách hàng, Xoá) bao phủ bản ghi khôi phục được bản ghi; bản ghi trở lại nguyên vẹn cùng toàn bộ liên kết dữ liệu cũ.

  **Lý do nghiệp vụ:** Khôi phục mà không kèm liên kết cũ (ghi chú, doanh nghiệp, cơ hội) trả về một hồ sơ rỗng, tức là vẫn mất dữ liệu. Quyền khôi phục đi cùng quyền xóa vì cả hai là quyết định về sự tồn tại của cùng một bản ghi.

- **`BR-05.4` (Dọn dẹp vĩnh viễn):** Tiến trình hệ thống tự động xóa vĩnh viễn bản ghi nằm trong Thùng rác quá thời hạn lưu — mặc định **30 ngày** ở gói tiêu chuẩn, nâng được tối đa 90 ngày ở gói Enterprise (Phụ lục B, `CFG-05-01`). Việc dọn dẹp chịu các chốt an toàn tại `BR-05.6`. Nhật ký kiểm toán về việc xóa được giữ lại sau khi bản ghi bị xóa vĩnh viễn.

  **Lý do nghiệp vụ:** Thùng rác không có thời hạn sẽ giữ vô thời hạn dữ liệu cá nhân mà doanh nghiệp đã quyết định không dùng nữa, trái nguyên tắc chỉ lưu dữ liệu khi còn mục đích; thời hạn quá ngắn thì không kịp phát hiện xóa nhầm.

- **`BR-05.5` (Thực thể con khi xóa mềm):** Khi một khách hàng bị xóa mềm, các Vé hỗ trợ và Cơ hội bán hàng đang mở của khách **không bị xóa** mà được gắn nhãn cảnh báo **"Khách hàng trong thùng rác"**; tính năng gửi phản hồi công khai trên vé bị khóa.

  **Lối ra khỏi khóa:** Khóa phản hồi công khai được mở lại bằng **một trong ba** cách: (a) khách hàng được khôi phục từ Thùng rác; (b) vé được **gán sang một khách hàng khác**; (c) Quản trị viên **đóng vé kèm lý do "Khách hàng đã bị xóa"**.

  **Lý do nghiệp vụ:** Gửi phản hồi công khai cho một khách hàng mà doanh nghiệp đã quyết định xóa là rủi ro liên lạc sai đối tượng. Nhưng nếu chỉ có cách (a), sẽ hình thành vòng khóa: vé không phản hồi được nên khó đóng, mà vé chưa đóng thì bản ghi không bao giờ được dọn dẹp theo `BR-05.6` (a).

- **`BR-05.6` (Chốt an toàn trước khi dọn dẹp vĩnh viễn):** Hệ thống **không được** tự động xóa vĩnh viễn một bản ghi khi bản ghi còn thuộc bất kỳ trường hợp nào sau:
  - **(a)** còn Cơ hội bán hàng hoặc Vé hỗ trợ **đang mở** (theo `BR-05.5`, các thực thể này không bị xóa cùng khách hàng);
  - **(b)** là **bản ghi phụ do thao tác gộp** và vẫn còn trong thời hạn hoàn tác gộp (`BR-20.3`);
  - **(c)** còn nghĩa vụ hợp đồng, hóa đơn hoặc tranh chấp pháp lý đang xử lý (thống nhất với `BR-33.3`).

  Các bản ghi này được đưa vào danh sách **"Cần xử lý trước khi dọn dẹp"** kèm thông báo cho Quản trị viên nêu rõ đang vướng điều gì. Việc xóa chỉ thực hiện sau khi con người xử lý dứt điểm (đóng thực thể con, chuyển sang khách hàng khác, hoặc xác nhận xóa kèm lý do) — thống nhất Nguyên tắc 4 tại Mục 2.4.

  **Lý do nghiệp vụ:** Nhân viên hay xóa nhầm rồi để đó. Nếu tự động xóa vĩnh viễn khi còn Cơ hội trị giá lớn hoặc Vé đang xử lý, các thực thể đó mất khách hàng và không thể phục hồi; nếu xóa bản ghi phụ còn trong hạn hoàn tác gộp, cơ chế hoàn tác gộp trả về một bản ghi rỗng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-05.1.1` | Khách hàng đang hoạt động | Người có quyền Xóa xóa khách hàng | Khách hàng biến mất khỏi danh sách, kết quả tìm kiếm và báo cáo thông thường |
| `AC-05.2.1` | Tiếp nối AC-05.1.1 | Mở Thùng rác | Thấy bản ghi kèm ngày xóa, người xóa và ngày dự kiến xóa vĩnh viễn |
| `AC-05.3.1` | Bản ghi trong Thùng rác, trước đó có 2 liên kết doanh nghiệp và 5 ghi chú | Người có quyền Xóa bấm "Khôi phục" | Bản ghi trở lại danh sách; 2 liên kết và 5 ghi chú còn nguyên |
| `AC-05.3.2` | Người dùng không có quyền Xóa | Mở Thùng rác | Không có hành động "Khôi phục" |
| `AC-05.4.1` | Bản ghi đã ở Thùng rác 29 ngày, không vướng chốt an toàn, thời hạn 30 ngày | Tiến trình dọn dẹp chạy | Bản ghi vẫn còn trong Thùng rác |
| `AC-05.4.2` | Bản ghi đã ở Thùng rác đủ 30 ngày, không vướng chốt an toàn | Tiến trình dọn dẹp chạy | Bản ghi bị xóa vĩnh viễn; nhật ký kiểm toán về việc xóa vẫn tra cứu được |
| `AC-05.5.1` | Khách hàng có 1 vé hỗ trợ đang mở | Xóa mềm khách hàng, mở vé | Vé vẫn tồn tại, mang nhãn "Khách hàng trong thùng rác"; nút gửi phản hồi công khai bị vô hiệu kèm giải thích |
| `AC-05.5.2` | Tiếp nối AC-05.5.1 | Gán vé sang một khách hàng khác | Nhãn cảnh báo được gỡ; gửi phản hồi công khai được |
| `AC-05.5.3` | Tiếp nối AC-05.5.1 | Quản trị viên đóng vé với lý do "Khách hàng đã bị xóa" | Vé đóng thành công dù chưa có phản hồi công khai |
| `AC-05.5.4` | Tiếp nối AC-05.5.1 | Khôi phục khách hàng | Nhãn cảnh báo được gỡ; gửi phản hồi công khai được |
| `AC-05.6.1` | Bản ghi đủ thời hạn dọn dẹp nhưng còn 1 Cơ hội đang mở | Tiến trình dọn dẹp chạy | Bản ghi không bị xóa; xuất hiện trong danh sách "Cần xử lý trước khi dọn dẹp"; Quản trị viên nhận thông báo nêu rõ Cơ hội đang vướng |
| `AC-05.6.2` | Bản ghi phụ do gộp cách đây 40 ngày, thời hạn hoàn tác gộp 90 ngày | Tiến trình dọn dẹp chạy | Bản ghi không bị xóa; hoàn tác gộp vẫn thực hiện được |
| `AC-05.6.3` | Bản ghi đủ thời hạn, khách còn một tranh chấp pháp lý đang xử lý | Tiến trình dọn dẹp chạy | Bản ghi không bị xóa; có trong danh sách "Cần xử lý trước khi dọn dẹp" |
| `AC-05.6.4` | Tiếp nối AC-05.6.1, Quản trị viên đã chuyển Cơ hội sang khách hàng khác | Quản trị viên xác nhận xóa kèm lý do | Bản ghi bị xóa vĩnh viễn; nhật ký ghi người xác nhận và lý do |

---

### Nhóm B — Quản trị Doanh nghiệp & Tổ chức

#### FEAT-06 — Tạo mới & Quản lý Thông tin Doanh nghiệp

**Mô tả nghiệp vụ:** Quản lý danh bạ các công ty, tổ chức đối tác kinh doanh với đầy đủ thông tin pháp nhân và thương mại.

**Vai trò sử dụng chính:** Nhân viên Kinh doanh, Quản lý Kinh doanh, Quản trị viên.

**Điều kiện tiên quyết:** Người dùng có ô (Doanh nghiệp, Tạo) = Có để tạo mới; sửa, xóa theo mức của ô tương ứng trên Doanh nghiệp.

**Luồng chính:**

1. Người dùng mở "Thêm doanh nghiệp", nhập thông tin theo `BR-06.1`.
2. Hệ thống kiểm tra trùng theo mã số thuế và tên miền website (`BR-06.2`).
3. Người dùng lưu; Người phụ trách và đơn vị của bản ghi xác định như với khách hàng cá nhân (`BR-01.3`).

**Quy tắc nghiệp vụ:**

- **`BR-06.1` (Thông tin doanh nghiệp):** Gồm Tên công ty (bắt buộc), Tên thương mại/viết tắt, Mã số thuế hoặc mã định danh doanh nghiệp, Ngành nghề kinh doanh, Quy mô nhân sự, Doanh thu hằng năm, Website, Địa chỉ trụ sở, Số điện thoại tổng đài.

  **Lý do nghiệp vụ:** Báo cáo theo ngành, theo quy mô và việc xuất hợp đồng đúng pháp nhân đều cần cùng một bộ thông tin chuẩn; chỉ bắt buộc tên công ty để nhân viên tạo được doanh nghiệp ngay khi mới biết tên, bổ sung dần sau.

- **`BR-06.2` (Căn cứ nhận diện trùng, hai mức độ tin cậy):** Áp dụng cùng mô hình hai mức độ tin cậy với `BR-17.1`:
  - **Tiêu chí chắc chắn:** trùng khớp chính xác **mã số thuế**. Một mã số thuế chỉ thuộc đúng một pháp nhân theo quy định pháp luật, nên trùng theo tiêu chí này **chặn tạo doanh nghiệp mới**, theo đúng chính sách xử lý tại `BR-17.2` (tenant đổi được sang "Chỉ cảnh báo" qua `CFG-17-01`).
  - **Tiêu chí tham khảo:** trùng khớp **tên miền website**. Tên miền thường dùng chung hợp lệ giữa công ty mẹ và các công ty con (`FEAT-07`), nên trùng theo tiêu chí này **chỉ cảnh báo mềm**, không bao giờ chặn tạo mới và không bao giờ dùng làm căn cứ gộp tự động — cùng nguyên tắc với Tiêu chí tham khảo của khách hàng cá nhân.

  **Lý do nghiệp vụ:** Tên công ty không phải căn cứ tin cậy ("Cty CP ABC" và "ABC Corp" là một), trong khi mã số thuế là định danh pháp lý duy nhất của pháp nhân, kể cả trong cùng một tập đoàn. Coi tên miền là căn cứ chắc chắn sẽ chặn đúng nghiệp vụ hợp lệ mà `FEAT-07` cần: tạo một công ty con dùng chung tên miền với công ty mẹ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-06.1.1` | Màn hình tạo doanh nghiệp | Bỏ trống Tên công ty, lưu | Từ chối, báo rõ trường bắt buộc ngay trên biểu mẫu |
| `AC-06.1.2` | Màn hình tạo doanh nghiệp | Nhập đủ các trường, lưu | Tạo thành công; hồ sơ hiển thị đủ thông tin đã nhập |
| `AC-06.2.1` | Đã có doanh nghiệp mang Mã số thuế 0101234567 | Tạo doanh nghiệp mới cùng Mã số thuế | Hiển thị cảnh báo trùng kèm doanh nghiệp đã có |
| `AC-06.2.2` | Đã có doanh nghiệp có website "vinafoods.vn" | Nhập khẩu một dòng doanh nghiệp có website "vinafoods.vn" | Dòng đó được nhận diện là trùng và xử lý theo chiến lược trùng lặp của lô nhập (`BR-23.3`) |

---

#### FEAT-07 — Cấu trúc Cây Doanh nghiệp Công ty Mẹ – Con

**Mô tả nghiệp vụ:** Thiết lập quan hệ phân cấp giữa Công ty Mẹ (tập đoàn) và các Công ty Con, chi nhánh thành viên.

**Vai trò sử dụng chính:** Nhân viên Kinh doanh, Quản trị viên.

**Điều kiện tiên quyết:** Người khai báo quan hệ mẹ – con có ô (Doanh nghiệp, Sửa) bao phủ doanh nghiệp được gắn làm con.

**Luồng chính:**

1. Trên hồ sơ doanh nghiệp, người dùng chọn Doanh nghiệp Mẹ.
2. Hệ thống kiểm tra một mẹ duy nhất, chống vòng lặp và giới hạn số cấp (`BR-07.1`, `BR-07.2`, `BR-07.5`).
3. Người dùng mở sơ đồ cây và báo cáo hợp nhất; số liệu hiển thị theo phạm vi của người xem (`BR-07.3`, `BR-07.4`).

**Quy tắc nghiệp vụ:**

- **`BR-07.1` (Một doanh nghiệp mẹ):** Mỗi doanh nghiệp khai báo được tối đa một Doanh nghiệp Mẹ.

  **Lý do nghiệp vụ:** Một công ty có hai mẹ làm báo cáo hợp nhất cộng doanh số hai lần và không xác định được tập đoàn nào chịu trách nhiệm về hợp đồng.

- **`BR-07.2` (Chống vòng lặp):** Hệ thống ngăn mọi quan hệ vòng tròn, trực tiếp hoặc gián tiếp: nếu A là mẹ của B thì B không thể là mẹ của A; nếu A là mẹ của B và B là mẹ của C thì C không thể là mẹ của A.

  **Lý do nghiệp vụ:** Một vòng lặp khiến sơ đồ cây và báo cáo hợp nhất không có điểm gốc — doanh số của tập đoàn bị cộng lặp vô hạn hoặc không tính được.

- **`BR-07.3` (Báo cáo hợp nhất):** Người dùng xem được sơ đồ cây tổ chức và báo cáo tổng doanh số, số lượng cơ hội hợp nhất của toàn bộ tập đoàn.

  **Lý do nghiệp vụ:** Bán hàng theo tập đoàn cần nhìn tổng giá trị quan hệ với cả tập đoàn để định giá và ưu tiên; xem từng công ty riêng lẻ thì bỏ sót cơ hội bán chéo giữa các công ty thành viên.

- **`BR-07.4` (Phạm vi dữ liệu trong báo cáo hợp nhất):** Báo cáo hợp nhất áp dụng **mức Xem của người xem** (`BR-01.4`) làm tiêu chí nền, theo đúng quy tắc của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.7`: mỗi loại dữ liệu hiển thị kèm được tính theo mức Xem của chính loại đó:
  - **(a)** Số liệu hợp nhất luôn tính trên đúng tập công ty nằm trong mức (Doanh nghiệp, Xem) của người xem, kèm ghi chú rõ khi tập đó nhỏ hơn toàn tập đoàn: "Số liệu hiển thị theo phạm vi dữ liệu của bạn — không phải toàn tập đoàn".
  - **(b)** Chỉ số tài chính hợp nhất (doanh số, số lượng và giá trị cơ hội) là dữ liệu của Cơ hội bán hàng, nên chỉ cộng các cơ hội nằm trong mức (Cơ hội, Xem) của người xem. Người xem thấy số liệu **đầy đủ toàn tập đoàn** khi và chỉ khi mức Xem Doanh nghiệp và mức Xem Cơ hội của họ cùng bao trùm toàn bộ cây — ví dụ Người có toàn quyền, hoặc người giữ vai trò Quản lý khi cây nằm trọn trong đơn vị và các đơn vị con của họ.
  - **(c)** Người có mức Xem Doanh nghiệp rộng nhưng ô (Cơ hội, Xem) = Không có — như vai trò dựng sẵn Marketing theo ma trận mặc định — **chỉ xem cấu trúc pháp nhân, không thấy chỉ số tài chính hợp nhất nào**. Quy tắc này áp theo ô, không theo tên vai trò: một vai trò tự tạo có cùng cấu hình ô nhận cùng kết quả.
  - **(d)** Sơ đồ cây hiển thị đầy đủ cấu trúc pháp nhân (tên công ty mẹ/con) vì đây là thông tin nhận diện, nhưng chỉ số tài chính của công ty ngoài phạm vi bị ẩn.

  **Lý do nghiệp vụ:** Báo cáo hợp nhất không được trở thành đường đọc doanh số của các đơn vị mà người xem không có quyền, hay của cơ hội bán hàng mà người xem vốn không được xem. Người phân khúc theo tập đoàn cần cấu trúc pháp nhân, không cần phân tích doanh thu.

- **`BR-07.5` (Giới hạn số cấp):** Cây tổ chức có tối đa **5 cấp** (Tập đoàn → Tổng công ty → Công ty thành viên → Chi nhánh → Đơn vị trực thuộc). Thiết lập vượt 5 cấp bị từ chối kèm đề nghị tổ chức lại cấu trúc.

  **Lý do nghiệp vụ:** Cây sâu vô hạn làm báo cáo hợp nhất chậm và khó đọc, và trong thực tế hầu như luôn là dấu hiệu nhập sai quan hệ; năm cấp đủ phủ cấu trúc tập đoàn thông thường.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-07.1.1` | Doanh nghiệp B chưa có doanh nghiệp mẹ | Khai báo A là Doanh nghiệp Mẹ của B | Lưu thành công; sơ đồ cây hiển thị A là mẹ của B |
| `AC-07.2.1` | A là mẹ của B | Khai báo B là mẹ của A | Từ chối, nêu rõ sẽ tạo quan hệ vòng tròn |
| `AC-07.2.2` | A là mẹ của B, B là mẹ của C | Khai báo C là mẹ của A | Từ chối, nêu rõ sẽ tạo quan hệ vòng tròn |
| `AC-07.3.1` | Tập đoàn có 3 công ty con, mỗi công ty có cơ hội đã thắng | Chủ sở hữu mở báo cáo hợp nhất | Tổng doanh số bằng tổng của cả tập đoàn |
| `AC-07.4.1` | Quản lý Kinh doanh chỉ có 2 trong 3 công ty con trong phạm vi | Mở báo cáo hợp nhất | Số liệu chỉ cộng 2 công ty; có ghi chú "Số liệu hiển thị theo phạm vi dữ liệu của bạn — không phải toàn tập đoàn" |
| `AC-07.4.2` | Tiếp nối AC-07.4.1 | Mở sơ đồ cây | Thấy tên cả 3 công ty con; chỉ số tài chính của công ty ngoài phạm vi bị ẩn |
| `AC-07.4.3` | Nhân viên Marketing theo ma trận mặc định (ô Cơ hội, Xem = Không có) | Mở sơ đồ cây và báo cáo hợp nhất của tập đoàn | Thấy cấu trúc pháp nhân; không thấy bất kỳ chỉ số tài chính hợp nhất nào |
| `AC-07.4.4` | Vai trò tự tạo "Phân tích thị trường" có (Doanh nghiệp, Xem) = Toàn workspace và (Cơ hội, Xem) = Đơn vị của mình | Mở báo cáo hợp nhất của một tập đoàn có cơ hội ở nhiều đơn vị | Thấy cấu trúc toàn cây; chỉ số tài chính chỉ cộng cơ hội của đơn vị mình, kèm ghi chú "không phải toàn tập đoàn" |
| `AC-07.5.1` | Cây đã có đủ 5 cấp | Gắn một doanh nghiệp làm con của đơn vị ở cấp 5 | Từ chối, nêu giới hạn 5 cấp |

---

#### FEAT-08 — Hồ sơ Chi tiết Doanh nghiệp & Danh sách Nhân sự Liên hệ

**Mô tả nghiệp vụ:** Màn hình 360 độ của doanh nghiệp hiển thị danh sách nhân sự liên hệ thuộc công ty, các Cơ hội bán hàng, Vé hỗ trợ và Dòng thời gian tương tác của toàn bộ nhân sự trực thuộc.

**Vai trò sử dụng chính:** Mọi người dùng có quyền xem doanh nghiệp.

**Điều kiện tiên quyết:** Người xem có ô (Doanh nghiệp, Xem) bao phủ doanh nghiệp.

**Luồng chính:**

1. Người dùng mở hồ sơ doanh nghiệp.
2. Hệ thống hiển thị danh sách nhân sự liên hệ với mức che theo quan hệ của người xem với từng khách hàng (`BR-08.1`), cùng dòng thời gian tổng hợp đã lọc theo phạm vi đọc của từng mục (`BR-08.2`).

**Quy tắc nghiệp vụ:**

- **`BR-08.1` (Danh sách nhân sự):** Hiển thị nhân sự liên hệ kèm chức danh, số điện thoại, email và đánh dấu ai là **Người liên hệ chính** của doanh nghiệp. Kênh liên lạc trong danh sách tuân theo chính sách che mặt nạ tại `BR-04.3` theo quan hệ của người xem với từng khách hàng.

  **Lý do nghiệp vụ:** Biết ai là người liên hệ chính giúp nhân viên gọi đúng người; nhưng nếu danh sách nhân sự không theo chính sách che của từng khách hàng, mở hồ sơ một doanh nghiệp lớn là cách đọc đầy đủ số điện thoại của hàng trăm người.

- **`BR-08.2` (Dòng thời gian doanh nghiệp):** Dòng thời gian của doanh nghiệp tự động tổng hợp dòng thời gian của các nhân sự liên hệ có liên kết với doanh nghiệp đó. Mỗi mục trong dòng thời gian tổng hợp vẫn tuân theo phạm vi đọc của chính mục đó (ví dụ phạm vi đọc ghi chú tại `BR-36.1`).

  **Lý do nghiệp vụ:** Nếu dòng thời gian doanh nghiệp không lọc theo phạm vi đọc của từng mục, nó trở thành đường đọc vòng các ghi chú nội bộ mà người xem không được đọc trên hồ sơ cá nhân.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-08.1.1` | Doanh nghiệp có 5 nhân sự liên hệ, 1 người là Người liên hệ chính | Mở hồ sơ doanh nghiệp | Thấy đủ 5 người kèm chức danh; Người liên hệ chính được đánh dấu |
| `AC-08.1.2` | Người xem không phải Người phụ trách của các nhân sự đó nhưng trong phạm vi | Xem danh sách nhân sự | Số điện thoại và email che một phần theo cột (B) |
| `AC-08.2.1` | Một nhân sự của doanh nghiệp có ghi chú phạm vi "Chung" mới tạo | Mở dòng thời gian doanh nghiệp | Ghi chú xuất hiện trên dòng thời gian doanh nghiệp |
| `AC-08.2.2` | Một nhân sự có ghi chú phạm vi "Nội bộ đội bán hàng" | Nhân viên Marketing mở dòng thời gian doanh nghiệp | Ghi chú đó không hiển thị nội dung với Marketing |

---

#### FEAT-09 — Thùng rác Doanh nghiệp & Phục hồi Bản ghi

**Mô tả nghiệp vụ:** Xóa mềm và khôi phục doanh nghiệp. Doanh nghiệp bị xóa tuân theo cùng quy tắc xóa mềm, Thùng rác, thời hạn dọn dẹp và chốt an toàn trước khi dọn dẹp như khách hàng cá nhân (`BR-05.1` đến `BR-05.6`); tính năng này đặc tả thêm cách xử lý các nhân sự liên hệ trực thuộc.

**Vai trò sử dụng chính:** Người có quyền Xóa trên doanh nghiệp, Quản trị viên.

**Điều kiện tiên quyết:** Người xóa hoặc khôi phục có ô (Doanh nghiệp, Xoá) bao phủ doanh nghiệp.

**Luồng chính:**

1. Người dùng xóa doanh nghiệp; tại bước xác nhận, chọn giữ nguyên các liên kết ở trạng thái Tạm ngưng hoặc chuyển toàn bộ nhân sự liên hệ sang doanh nghiệp khác (`BR-09.1`).
2. Hệ thống xác định lại Doanh nghiệp chính cho các khách hàng bị ảnh hưởng.
3. Khi doanh nghiệp được khôi phục, các liên kết trở lại theo `BR-09.2`.

**Quy tắc nghiệp vụ:**

- **`BR-09.1` (Xử lý nhân sự liên hệ khi xóa mềm doanh nghiệp):** Khi xóa doanh nghiệp, các nhân sự liên hệ trực thuộc **không bị xóa**. Hệ thống xử lý theo mô hình Quan hệ Đa tổ chức (`FEAT-10`):
  - **(a) Liên kết bị tạm ngưng, không bị xóa:** các liên kết giữa khách hàng và doanh nghiệp bị xóa chuyển sang trạng thái **Tạm ngưng** (Phụ lục A, A.5b), giữ nguyên chức danh, phòng ban, ngày bắt đầu/kết thúc, để phục hồi nguyên vẹn nếu doanh nghiệp được khôi phục.
  - **(b) Xác định lại Doanh nghiệp chính:** nếu doanh nghiệp bị xóa đang là Doanh nghiệp chính của một khách hàng, hệ thống tự động đề cử liên kết đang hoạt động **còn lại có ngày bắt đầu muộn nhất** (phản ánh nơi công tác hiện tại, thống nhất `BR-19.7`) làm Doanh nghiệp chính mới và ghi vào lịch sử hồ sơ. Nhiều liên kết cùng ngày bắt đầu thì ưu tiên liên kết có vai trò "Chính", sau đó là liên kết có tương tác gần nhất. Nếu không còn liên kết hoạt động nào, Doanh nghiệp chính để trống và khách hàng (loại B2B) được đưa vào danh sách **"Liên hệ chưa gắn doanh nghiệp"** để đội ngũ bổ sung.
  - **(c) Chuyển giao chủ động:** tại bước xác nhận xóa, người thực hiện được chọn chuyển toàn bộ nhân sự liên hệ sang một doanh nghiệp khác (ví dụ khi sáp nhập pháp nhân) thay cho cách xử lý (a) và (b).

  Khi doanh nghiệp bị xóa vĩnh viễn sau thời hạn lưu, các liên kết ở trạng thái Tạm ngưng bị gỡ theo; Doanh nghiệp chính của từng khách hàng đã được xác định lại tại (b) từ lúc xóa mềm.

  **Lý do nghiệp vụ:** Xóa một doanh nghiệp không có nghĩa là những con người từng làm việc ở đó biến mất; nếu xóa theo, doanh nghiệp mất cả lịch sử quan hệ với những khách hàng có thể đang làm việc ở công ty khác.

- **`BR-09.2` (Khôi phục doanh nghiệp):** Khi doanh nghiệp được khôi phục, toàn bộ liên kết Tạm ngưng trở về trạng thái trước khi xóa. Nếu trong thời gian doanh nghiệp nằm trong Thùng rác, một khách hàng đã được gán Doanh nghiệp chính mới, hệ thống **giữ nguyên** Doanh nghiệp chính mới và phục hồi liên kết cũ dưới dạng liên kết phụ, đồng thời thông báo cho người khôi phục để rà soát.

  **Lý do nghiệp vụ:** Doanh nghiệp chính mới là một quyết định có thể đã được con người xác nhận trong thời gian chờ; tự động đè lại sẽ xóa mất quyết định đó.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-09.1.1` | Doanh nghiệp A có 10 nhân sự liên hệ | Xóa mềm A | 10 khách hàng vẫn tồn tại; liên kết với A hiện trạng thái Tạm ngưng, chức danh còn nguyên |
| `AC-09.1.2` | Ông Bình có Doanh nghiệp chính là A, còn liên kết đang hoạt động với B (bắt đầu 2025) và C (bắt đầu 2023) | Xóa mềm A | B trở thành Doanh nghiệp chính của ông Bình; lịch sử hồ sơ ghi nhận thay đổi |
| `AC-09.1.3` | Ông Bình có liên kết với B và C cùng ngày bắt đầu, liên kết với C mang vai trò "Chính" | Xóa mềm Doanh nghiệp chính hiện tại | C trở thành Doanh nghiệp chính |
| `AC-09.1.4` | Khách hàng loại B2B chỉ có liên kết với A | Xóa mềm A | Doanh nghiệp chính để trống; khách hàng xuất hiện trong danh sách "Liên hệ chưa gắn doanh nghiệp" |
| `AC-09.1.5` | Doanh nghiệp A sáp nhập vào D | Xóa A, chọn chuyển toàn bộ nhân sự sang D | Toàn bộ nhân sự liên hệ của A có liên kết với D |
| `AC-09.2.1` | A đang trong Thùng rác, liên kết của các nhân sự đang Tạm ngưng | Khôi phục A | Các liên kết trở về trạng thái trước khi xóa |
| `AC-09.2.2` | Trong lúc A trong Thùng rác, ông Bình đã được gán Doanh nghiệp chính B | Khôi phục A | Doanh nghiệp chính của ông Bình vẫn là B; liên kết với A trở lại dưới dạng liên kết phụ; người khôi phục nhận thông báo rà soát |
| `AC-09.2.3` | Doanh nghiệp đủ thời hạn dọn dẹp nhưng còn 1 Cơ hội đang mở | Tiến trình dọn dẹp chạy | Doanh nghiệp không bị xóa vĩnh viễn; có trong danh sách "Cần xử lý trước khi dọn dẹp" (`BR-05.6`) |

---

### Nhóm C — Mạng lưới Quan hệ Đa chiều

#### FEAT-10 — Quan hệ Đa Doanh nghiệp của Cá nhân

**Mô tả nghiệp vụ:** Giải quyết bài toán một cá nhân làm việc cho nhiều công ty cùng lúc (ví dụ Giám đốc tại Công ty A, đồng thời Cố vấn tại Công ty B và Cổ đông tại Công ty C).

**Vai trò sử dụng chính:** Nhân viên Kinh doanh, Quản trị viên.

**Điều kiện tiên quyết:** Người thêm, sửa, gỡ liên kết có ô (Khách hàng, Sửa) bao phủ khách hàng.

**Luồng chính:**

1. Trên hồ sơ khách hàng, người dùng thêm liên kết với một doanh nghiệp, nhập chức danh, vai trò liên kết, ngày bắt đầu (`BR-10.1`).
2. Người dùng đặt một liên kết làm Doanh nghiệp chính (`BR-10.2`).
3. Khi khách rời doanh nghiệp, người dùng chuyển liên kết sang "Đã nghỉ việc"; hệ thống xử lý kênh liên lạc và Doanh nghiệp chính theo `BR-10.4`.

**Quy tắc nghiệp vụ:**

- **`BR-10.1` (Nội dung một liên kết):** Mỗi liên kết giữa một khách hàng cá nhân và một doanh nghiệp ghi nhận: Chức danh, Phòng ban công tác, **Vai trò liên kết** (Phụ lục A, A.5), Ngày bắt đầu, Ngày kết thúc và **Trạng thái liên kết** (Phụ lục A, A.5b: Đang công tác / Đã nghỉ việc / Tạm ngưng).

  **Lý do nghiệp vụ:** Một người có thể là giám đốc ở công ty này và cố vấn ở công ty khác; không ghi riêng chức danh và thời gian cho từng liên kết thì không biết nên liên hệ họ với tư cách nào và ở đâu.

- **`BR-10.2` (Doanh nghiệp chính):** Mỗi khách hàng có tối đa **một** Doanh nghiệp chính, dùng để hiển thị mặc định trên danh sách và báo cáo tổng quan. Đặt một liên kết khác làm chính thì liên kết cũ tự động mất trạng thái chính.

  **Lý do nghiệp vụ:** Danh sách và báo cáo cần một công ty duy nhất để hiển thị mặc định; nhiều Doanh nghiệp chính làm báo cáo theo doanh nghiệp đếm một người nhiều lần.

- **`BR-10.3` (Quản lý liên kết):** Người dùng thêm, sửa và gỡ liên kết doanh nghiệp ngay trên hồ sơ khách hàng. Số liên kết tối đa trên một khách hàng theo gói dịch vụ tại `NFR-11`. Liên kết hiển thị ở cả hai phía: trên hồ sơ khách hàng và trong danh sách nhân sự của doanh nghiệp.

  **Lý do nghiệp vụ:** Liên kết phải sửa được ngay nơi nhân viên làm việc, và hiển thị ở cả hai phía để người mở hồ sơ doanh nghiệp không bỏ sót một nhân sự đã được khai báo từ phía khách hàng.

- **`BR-10.4` (Xử lý khi liên kết chuyển sang Đã nghỉ việc):** Khi một liên kết chuyển sang "Đã nghỉ việc", hệ thống:
  - **(a)** tự động gắn trạng thái tiếp cận **"Không còn hiệu lực"** (Phụ lục A, A.10) cho email và số điện thoại công việc thuộc doanh nghiệp đó, và loại chúng khỏi các chiến dịch gửi tự động. Trạng thái này **khác** với "Không tiếp cận được" và **người dùng đảo lại được** khi khách quay lại công ty cũ;
  - **(b)** nếu đó là Doanh nghiệp chính, đề cử Doanh nghiệp chính mới theo nguyên tắc tại `BR-09.1` (b);
  - **(c)** cảnh báo trên hồ sơ doanh nghiệp nếu công ty không còn nhân sự liên hệ đang công tác hoặc không còn Người liên hệ chính.

  **Lý do nghiệp vụ:** Nếu không xử lý, danh sách và báo cáo hiển thị khách hàng gắn với công ty họ đã rời — nhân viên gọi vào tổng đài công ty cũ, hợp đồng xuất sai pháp nhân, chiến dịch email tiếp tục gửi vào địa chỉ đã bị hủy. Phải dùng một trạng thái riêng thay vì "Không tiếp cận được" vì trạng thái đó là trạng thái kỹ thuật **luôn thắng khi gộp** (`BR-19.6`) và không đảo lại được — gán nó cho một lý do nghiệp vụ sẽ khóa vĩnh viễn một địa chỉ có thể dùng lại.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-10.1.1` | Ông Hùng đã có liên kết với Công ty Hùng Cường | Thêm liên kết với Ngân hàng Á Châu, chức danh "Thành viên HĐQT", vai trò "Cố vấn" | Hồ sơ ông Hùng hiển thị hai công ty; hồ sơ Ngân hàng Á Châu có ông Hùng trong danh sách nhân sự |
| `AC-10.2.1` | Doanh nghiệp chính của ông Hùng là Hùng Cường | Đặt Ngân hàng Á Châu làm Doanh nghiệp chính | Ngân hàng Á Châu là chính; Hùng Cường tự động trở thành liên kết phụ |
| `AC-10.3.1` | Gói tiêu chuẩn, khách hàng đã có 20 liên kết | Thêm liên kết thứ 21 | Từ chối, nêu giới hạn của gói |
| `AC-10.4.1` | Chị Mai có liên kết Đang công tác với Vinafoods, email công việc thuộc tên miền Vinafoods | Chuyển liên kết sang "Đã nghỉ việc" | Email công việc đó mang trạng thái "Không còn hiệu lực" và không còn trong danh sách nhận của chiến dịch tự động |
| `AC-10.4.2` | Tiếp nối AC-10.4.1, Vinafoods là Doanh nghiệp chính của chị Mai, chị còn liên kết đang hoạt động với công ty khác | Quan sát hồ sơ sau khi chuyển trạng thái | Công ty còn lại được đề cử làm Doanh nghiệp chính |
| `AC-10.4.3` | Chị Mai là nhân sự liên hệ duy nhất và là Người liên hệ chính của Vinafoods | Chuyển liên kết sang "Đã nghỉ việc" | Hồ sơ Vinafoods hiển thị cảnh báo không còn nhân sự đang công tác và không còn Người liên hệ chính |
| `AC-10.4.4` | Chị Mai quay lại làm việc tại Vinafoods | Chuyển liên kết về "Đang công tác" và đảo trạng thái email về hợp lệ | Email công việc dùng lại được cho chiến dịch (nếu đồng thuận cho phép) |

---

#### FEAT-11 — Mạng lưới Quan hệ Giữa các Cá nhân

**Mô tả nghiệp vụ:** Thiết lập liên kết trực tiếp giữa hai con người trong CRM để phục vụ bán hàng dựa trên mạng lưới quan hệ.

**Vai trò sử dụng chính:** Nhân viên Kinh doanh, Quản trị viên.

**Điều kiện tiên quyết:** Người khai báo có ô (Khách hàng, Sửa) bao phủ ít nhất một trong hai khách hàng, và ô (Khách hàng, Xem) bao phủ khách hàng còn lại.

**Luồng chính:**

1. Trên hồ sơ khách hàng A, người dùng thêm quan hệ, chọn khách hàng B và loại quan hệ (`BR-11.1`).
2. Hệ thống ghi nhận cả hai chiều (`BR-11.2`).
3. Người dùng gỡ quan hệ ở một phía; hệ thống gỡ cả hai chiều.

**Quy tắc nghiệp vụ:**

- **`BR-11.1` (Loại quan hệ):** Chọn từ danh mục chuẩn (Phụ lục A, A.6): Quản lý trực tiếp / Cấp dưới · Người giới thiệu / Được giới thiệu · Thành viên gia đình · Đối tác kinh doanh · Trợ lý / Người đại diện.

  **Lý do nghiệp vụ:** Quan hệ nhập tự do không thống kê và không lọc được; danh mục chuẩn cho phép tìm "mọi người giới thiệu" hay "mọi quản lý trực tiếp" để bán hàng qua mạng lưới.

- **`BR-11.2` (Tính đối xứng):** Khi tạo quan hệ "A là Quản lý trực tiếp của B", hệ thống tự động ghi nhận chiều ngược lại "B là Cấp dưới của A"; các loại quan hệ hai chiều như nhau (Thành viên gia đình, Đối tác kinh doanh) hiển thị cùng một nhãn ở cả hai phía. Gỡ quan hệ ở một phía thì chiều ngược lại cũng được gỡ.

  **Lý do nghiệp vụ:** Nếu mỗi chiều phải khai báo riêng, hai hồ sơ sẽ sớm nói hai điều khác nhau về cùng một mối quan hệ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-11.1.1` | Hồ sơ khách hàng A | Thêm quan hệ, mở danh sách loại quan hệ | Chỉ có các loại thuộc danh mục A.6 |
| `AC-11.2.1` | A và B chưa có quan hệ | Trên hồ sơ A, khai báo "A là Quản lý trực tiếp của B" | Hồ sơ B tự động hiển thị "Cấp dưới của A" |
| `AC-11.2.2` | Tiếp nối AC-11.2.1 | Gỡ quan hệ trên hồ sơ B | Quan hệ biến mất trên cả hai hồ sơ |
| `AC-11.2.3` | A và C chưa có quan hệ | Khai báo A và C là "Thành viên gia đình" | Cả hai hồ sơ hiển thị cùng nhãn "Thành viên gia đình" |

---

### Nhóm D — Vòng đời Khách hàng & Chuyển đổi Tiềm năng

#### FEAT-12 — Quản trị Giai đoạn Vòng đời Khách hàng & Ma trận Chuyển đổi

**Mô tả nghiệp vụ:** Phân loại khách hàng theo đúng vị trí trên hành trình qua các giai đoạn chuẩn, gồm hai nhóm: **7 giai đoạn phễu tuyến tính**, phản ánh tiến trình thăng hạng thông thường từ nhận diện đến trung thành; và **3 trạng thái đặc biệt** nằm ngoài đường tuyến tính, dùng cho các nhánh rẽ hoặc thoát phễu và không tính vào báo cáo vận tốc phễu tuyến tính (`BR-13.3`).

**Vai trò sử dụng chính:** Nhân viên Kinh doanh (bước tiến lên trong phạm vi của mình), Quản lý Kinh doanh (bước lùi, loại khách, đưa về nuôi dưỡng), Quản trị viên và Chủ sở hữu (gồm ngoại lệ gian lận với khách đã trả tiền — `BR-12.8`), Quản lý Marketing (cấu hình thăng hạng tự động), Tiến trình Hệ thống (bước chuyển tự sinh — `BR-12.9`).

**Định nghĩa các giai đoạn vòng đời:**

*Nhóm 7 giai đoạn phễu tuyến tính:*

1. **Người Đăng ký (Subscriber):** Khách mới đăng ký nhận bản tin/tài liệu, **chưa có tương tác nào khác** và chưa có nhu cầu mua rõ ràng.
2. **Khách hàng Tiềm năng (Lead):** Đã để lại thông tin liên hệ và thể hiện sự quan tâm ban đầu.
3. **Tiềm năng Đủ điều kiện Tiếp thị (MQL):** Đã tương tác thực qua các chiến dịch và đạt Ngưỡng MQL (`BR-15.5`).
4. **Tiềm năng Đủ điều kiện Bán hàng (SQL):** Đã được đội kinh doanh thẩm định trực tiếp và sẵn sàng trao đổi cơ hội mua hàng.
5. **Cơ hội Kinh doanh (Opportunity):** Đang có ít nhất một Cơ hội bán hàng đang mở.
6. **Khách hàng Chính thức (Customer):** Đã ký hợp đồng hoặc có Cơ hội bán hàng đã thắng.
7. **Khách hàng Trung thành / Đại sứ (Evangelist):** Khách hàng đang trả tiền, gắn bó lâu năm, sẵn sàng giới thiệu khách hàng mới.

*Nhóm 3 trạng thái đặc biệt (ngoài phễu tuyến tính):*

8. **Đang Nuôi dưỡng (Nurturing):** Chưa sẵn sàng mua (hết ngân sách, chờ phê duyệt nội bộ) nhưng còn tiềm năng; tiếp tục được chăm sóc qua chiến dịch định kỳ.
9. **Đã Rời bỏ (Churned):** Khách hàng đã hủy dịch vụ hoặc chấm dứt hợp đồng. Không nhận thư tiếp thị tự động, trừ khi thuộc Chiến dịch Tái tiếp cận đã được phê duyệt (`BR-12.5b`).
10. **Đã Loại (Disqualified):** Không phù hợp với tập khách hàng mục tiêu (sai ngành, không đủ ngân sách, gian lận). Bị loại khỏi mọi chiến dịch tiếp thị.

**Bốn nguyên tắc chi phối các bước chuyển trên phễu tuyến tính:**

1. **Giai đoạn phải phản ánh thực tế bán hàng.** Khi thực tế đã phát sinh Cơ hội bán hàng hoặc Cơ hội đã thắng, giai đoạn buộc phải cập nhật theo — kể cả khi phải nhảy nhiều bậc. Đây là nguyên tắc mạnh nhất, ưu tiên cao hơn ba nguyên tắc còn lại.
2. **Tiến lên trên phễu là tự do có điều kiện chuyên môn.** Bước tiến lên không cần quyền đặc biệt, nhưng bước lên SQL bắt buộc có thẩm định của con người (`BR-15.6`) và bước lên MQL diễn ra tự động theo ngưỡng điểm (`BR-15.5`).
3. **Lùi lại trên phễu là hành vi có kiểm soát.** Cần Quản lý Kinh doanh trở lên và bắt buộc ghi lý do (`BR-12.7`), trừ Hoàn tác Chuyển đổi đã có lý do riêng (`BR-14.2`). **Sàn của nguyên tắc này:** Subscriber **không** phải đích lùi thủ công từ bất kỳ giai đoạn nào, vì Subscriber là hồ sơ chưa có tương tác — hạ một hồ sơ đã có tương tác về đó là ghi sai lịch sử. Đường duy nhất trở lại Subscriber là Hoàn tác Chuyển đổi khi bản ghi vốn ở Subscriber trước lúc chuyển đổi; vì vậy chỉ hai dòng SQL và Opportunity — hai giai đoạn có thể là kết quả của một lần chuyển đổi — có Subscriber trong danh sách đích.
4. **Trạng thái khách hàng đang trả tiền được bảo vệ tuyệt đối.** Customer và Evangelist không bao giờ bị hạ về giai đoạn tiền bán hàng; chỉ ra khỏi hai giai đoạn này qua Churned (rời bỏ) hoặc Disqualified (phát hiện gian lận, `BR-12.8`).

**Các bước chuyển ra/vào ba trạng thái đặc biệt** do quy tắc riêng chi phối và được liệt kê cùng bảng dưới để người đọc chỉ tra một chỗ:

- Vào Nurturing: `BR-12.3` (toàn bộ Cơ hội Thua, lý do từ A.2), `BR-16.4` (điểm nguội ở bốn giai đoạn đầu phễu, lý do từ A.16), `BR-12.5b` (Churned thuộc Chiến dịch Tái tiếp cận).
- Ra khỏi Nurturing về Lead: `BR-16.5` — đường quay lại phễu cho hồ sơ hoạt động trở lại nhưng chưa đủ Ngưỡng MQL.
- Vào Churned: `BR-12.5` — chỉ từ Customer/Evangelist; các giai đoạn khác chỉ tới Churned qua đường gộp bản ghi (`BR-12.6`, tình huống (ii)).
- Vào Disqualified: `BR-12.4` (giai đoạn tiền bán hàng, Quản lý Kinh doanh trở lên), `BR-12.4b` (đánh dấu nhanh Lead rác), `BR-12.8` (ngoại lệ gian lận với Customer/Evangelist/Churned, chỉ Quản trị viên/Chủ sở hữu).
- Vào Evangelist: **chỉ từ Customer** — Evangelist là khách đang trả tiền có hành vi giới thiệu, nên phải qua Customer trước; đây là ngoại lệ có chủ đích của nguyên tắc 2 và là trường hợp duy nhất một bước tiến lên bị chặn.

**Ma trận Chuyển đổi Giai đoạn:**

| Từ giai đoạn | Được phép chuyển đến | Điều kiện / Quyền yêu cầu |
| --- | --- | --- |
| **Subscriber** | Lead, MQL, SQL, Opportunity, **Customer**, Nurturing, Disqualified, *Churned (chỉ qua gộp)* | Lên Lead: **tự động khi có tương tác đầu tiên** — lượt tương tác đầu tiên được ghi nhận thành bản ghi hoạt động (`BR-36.5`) hoặc được chấm Điểm Tương tác (`FEAT-15`). Lên MQL: tự động khi đạt Ngưỡng MQL (`BR-15.5`). Lên SQL: thẩm định của nhân viên kinh doanh hoặc Chuyển đổi Tiềm năng không kèm Cơ hội (`BR-15.6`, `FEAT-14`). Lên Opportunity và Customer: **nguyên tắc 1**. Sang Nurturing: lý do từ A.16 (`BR-16.4`). Sang Disqualified: `BR-12.4`. Sang Churned: chỉ qua gộp bản ghi (`BR-19.8`) — khách chưa từng trả tiền thì không thể "rời bỏ" bằng thao tác tay |
| **Lead** | MQL, SQL, Opportunity, **Customer**, Nurturing, Disqualified, *Churned (chỉ qua gộp)* | Lên MQL: `BR-15.5`. Lên SQL: thẩm định hoặc Chuyển đổi Tiềm năng không kèm Cơ hội (`BR-15.6`, `FEAT-14`). Lên Opportunity và Customer: **nguyên tắc 1** (lên Customer khi có Cơ hội Thắng, kể cả nhảy bậc). Sang Nurturing: lý do từ A.16 (`BR-16.4`). Sang Disqualified: `BR-12.4`. Sang Churned: chỉ qua gộp bản ghi |
| **MQL** | SQL, Opportunity, **Customer**, Nurturing, Disqualified, *Lead (lùi)*, *Churned (chỉ qua gộp)* | Lên SQL: thẩm định (`BR-15.6`). Lên Opportunity và Customer: nguyên tắc 1. Lùi về Lead: nguyên tắc 3, lý do từ A.3. Sang Nurturing: lý do từ A.16 (`BR-16.4`). Sang Disqualified: `BR-12.4`. Sang Churned: chỉ qua gộp bản ghi |
| **SQL** | Opportunity, **Customer**, Nurturing, Disqualified, *MQL, Lead, Subscriber (lùi)*, *Churned (chỉ qua gộp)* | Lên Opportunity và Customer: nguyên tắc 1. Sang Nurturing: lý do từ A.16 (`BR-16.4`). Lùi về MQL: nguyên tắc 3. Về Lead/Subscriber: **chỉ** qua Hoàn tác Chuyển đổi, trả về đúng giai đoạn trước khi chuyển đổi (`BR-14.2`). Sang Disqualified: `BR-12.4`. Sang Churned: chỉ qua gộp bản ghi |
| **Opportunity** | Customer, Nurturing, Disqualified, *SQL, MQL, Lead, Subscriber (lùi)*, *Churned (chỉ qua gộp)* | Lên Customer: nguyên tắc 1, khi có Cơ hội Thắng. Sang Nurturing: khi toàn bộ Cơ hội Thua, lý do từ A.2 (`BR-12.3`). Về Lead/Subscriber: **chỉ** qua Hoàn tác Chuyển đổi (`BR-14.2`). Lùi khác: nguyên tắc 3. Sang Disqualified: `BR-12.4`. Sang Churned: chỉ qua gộp bản ghi |
| **Customer** | Evangelist, Churned, *Disqualified (ngoại lệ gian lận)* | **Nguyên tắc 4** — không được hạ về Subscriber/Lead/MQL/SQL/Opportunity/Nurturing. Sang Disqualified chỉ khi phát hiện gian lận và chỉ bởi Quản trị viên/Chủ sở hữu (`BR-12.8`) |
| **Evangelist** | Customer, Churned, *Disqualified (ngoại lệ gian lận)* | **Nguyên tắc 4.** Về Customer: nguyên tắc 3, lý do từ A.3. Sang Disqualified: chỉ theo `BR-12.8` |
| **Nurturing** | MQL, SQL, Opportunity, Customer, Disqualified, *Lead (quay lại)*, *Churned (chỉ qua gộp)* | Về Lead: `BR-16.5` — Quản lý Kinh doanh trở lên, lý do từ A.18. Lên MQL: tự động khi đạt lại Ngưỡng MQL (`BR-15.5`). Lên SQL: thẩm định. Lên Opportunity và Customer: **nguyên tắc 1**. Sang Disqualified: `BR-12.4`. Sang Churned: chỉ qua gộp bản ghi |
| **Churned** | Customer, Opportunity, Nurturing, *Disqualified (ngoại lệ gian lận)* | Về Customer và Opportunity: **nguyên tắc 1** — khách cũ quay lại, mở cơ hội mới hoặc ký lại hợp đồng. Về Nurturing: chỉ khi thuộc Chiến dịch Tái tiếp cận đã được phê duyệt (`BR-12.5b`). Sang Disqualified: vì Churned là khách **đã từng trả tiền**, áp `BR-12.8` — chỉ Quản trị viên/Chủ sở hữu, chỉ với lý do nhóm gian lận |
| **Disqualified** | Lead, Nurturing, Opportunity, **Customer**, *Churned (chỉ qua gộp)* | Về Lead/Nurturing: chỉ Quản lý Kinh doanh trở lên khi có bằng chứng mới, bắt buộc chọn **Lý do mở lại** từ A.17. Lên Opportunity và Customer: **nguyên tắc 1** — hệ thống đồng thời cảnh báo Quản lý Kinh doanh rà soát lại quyết định loại |

*Cách đọc bảng:* giai đoạn in nghiêng kèm "(lùi)" là bước lùi trên phễu tuyến tính, chịu nguyên tắc 3. Ma trận điều chỉnh các bước chuyển **do người dùng quyết định**; giai đoạn do thao tác gộp bản ghi sinh ra được `BR-19.8` tính và nằm ngoài ma trận theo `BR-12.6` (ii). Bước chuyển do hệ thống tự sinh theo nguyên tắc 1 hợp lệ theo thiết kế (`BR-12.9`). Ma trận là tham số cấu hình theo tenant (Phụ lục B, `CFG-12-01`); riêng nguyên tắc 4 (`BR-12.3`, `BR-12.8`) và ràng buộc quyền loại khách (`BR-12.4`) là sàn bắt buộc.

**Điều kiện tiên quyết:** Người chuyển giai đoạn thủ công có ô (Khách hàng, Chuyển giai đoạn vòng đời) bao phủ bản ghi; bước lùi, loại khách, mở lại và các bước có kiểm soát khác cần thêm quyền quản trị tương ứng tại Mục 5.2.

**Luồng chính:**

1. Người dùng mở hồ sơ, chọn "Chuyển giai đoạn vòng đời"; hệ thống chỉ liệt kê các giai đoạn đích hợp lệ theo Ma trận Chuyển đổi và quyền của người dùng.
2. Người dùng chọn giai đoạn đích và lý do khi quy tắc yêu cầu.
3. Hệ thống ghi bước chuyển vào Lịch sử giai đoạn (`BR-12.1`).
4. Song song, hệ thống tự sinh các bước chuyển theo nguyên tắc 1 từ sự kiện Cơ hội bán hàng (`BR-12.9`).

**Quy tắc nghiệp vụ:**

- **`BR-12.1` (Ghi nhận mỗi lần chuyển giai đoạn):** Mọi bước chuyển giai đoạn tuân thủ ma trận trên; mỗi lần chuyển, hệ thống ghi nhận thời điểm chuyển và người thực hiện (hoặc "Hệ thống" kèm sự kiện nguồn) vào Lịch sử giai đoạn (`FEAT-13`).

  **Lý do nghiệp vụ:** Không có dấu vết người và thời điểm của từng bước chuyển thì không phân tích được vận tốc phễu, không phân biệt được bước do người hay do hệ thống, và không truy được trách nhiệm khi một khách bị loại sai.

- **`BR-12.2` (Tự động nâng cấp từ sự kiện Cơ hội bán hàng):** Khi một khách hàng được tạo Cơ hội bán hàng mới, giai đoạn tự động nâng lên tối thiểu Opportunity. Khi Cơ hội chuyển sang Thắng, giai đoạn tự động nâng lên Customer. Sự kiện tạo Cơ hội và đóng thắng do phân hệ Cơ hội bán hàng phát ra — xem [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) (`BR-18.4` của tài liệu đó).

  **Lý do nghiệp vụ:** Giai đoạn phải phản ánh thực tế bán hàng (nguyên tắc 1); nếu nhân viên phải tự cập nhật sau khi tạo hoặc thắng cơ hội, phễu luôn trễ so với thực tế và báo cáo doanh thu theo giai đoạn sai.

- **`BR-12.3` (Nhiều cơ hội & xử lý khi mọi cơ hội thất bại):**
  - Khách hàng đã đạt Customer (có ít nhất một Cơ hội Thắng) **không bị hạ hạng** khi các cơ hội bán thêm tiếp theo bị Thua.
  - Khách hàng chưa từng là Customer: khi **toàn bộ** Cơ hội đều Thua, hệ thống **không tự hạ về Lead/MQL** mà tự chuyển sang Nurturing và yêu cầu nhân viên kinh doanh chọn **Lý do không chuyển đổi** từ A.2 để Marketing có kịch bản tái tiếp cận phù hợp.

  **Lý do nghiệp vụ:** Hạ một khách đã trả tiền về tiền bán hàng chỉ vì một đơn bán thêm thất bại sẽ đẩy họ vào chiến dịch săn khách mới và làm sai báo cáo doanh thu. Với khách chưa mua, thất bại của cơ hội không có nghĩa là khách hết tiềm năng — họ cần được nuôi dưỡng, không bị loại.

- **`BR-12.3b` (Cơ hội mở duy nhất biến mất mà không qua đóng Thua):** Định nghĩa giai đoạn Opportunity là "đang có ít nhất một Cơ hội mở gắn với khách hàng chưa từng là Customer". Khi Cơ hội mở duy nhất đó không còn gắn với khách hàng **mà không qua đóng Thua** — bị xóa mềm, được chuyển sang gắn với khách hàng khác, hoặc khách hàng bị gỡ khỏi Cơ hội — và khách hàng không còn Cơ hội mở nào khác, hệ thống áp **đúng cách xử lý của `BR-12.3`** cho khách chưa từng là Customer: tự động chuyển sang Nurturing, bắt buộc chọn Lý do không chuyển đổi từ A.2. **Không áp** khi Cơ hội bị xóa mềm hoặc gỡ liên kết do Hoàn tác Chuyển đổi: khi đó khách trở về đúng giai đoạn trước khi chuyển đổi theo `BR-14.2` (c), `BR-14.5`.

  **Lý do nghiệp vụ:** Hệ quả quan sát được giống hệt trường hợp toàn bộ Cơ hội Thua — khách không còn Cơ hội mở nào — nên phải nhận cùng một xử lý; nếu không, khách đứng mãi ở Opportunity dù không có Cơ hội nào đang vận động, làm sai báo cáo phễu và khiến khách này không bao giờ vào lại vòng nuôi dưỡng của Marketing.

- **`BR-12.3c` (Tái phân loại Cơ hội Thắng duy nhất thành Thua):** Khi [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) (`FEAT-21` của tài liệu đó) tái phân loại một Cơ hội đã Thắng thành Thua, và đó là Cơ hội Thắng **duy nhất** đã đưa khách hàng lên Customer, hệ thống **không tự động hạ giai đoạn** — khách hàng **giữ nguyên Customer** theo nguyên tắc 4, vì tái phân loại là sửa một sai sót ghi nhận quá khứ, không phải một sự kiện thương mại mới, và việc tự động hạ giai đoạn có thể diễn ra giữa lúc hợp đồng, hóa đơn hay nghĩa vụ pháp lý khác đã phát sinh thật ngoài hệ thống dựa trên trạng thái Customer đó. Hệ thống **bắt buộc cảnh báo** Quản lý Kinh doanh trở lên rà soát thủ công, cùng cơ chế cảnh báo đã dùng khi mở lại một bản ghi Disqualified (`BR-12.4`); Quản lý là người duy nhất quyết định có hạ giai đoạn theo `BR-12.7` (kèm lý do) hay giữ nguyên.

  **Lý do nghiệp vụ:** Nguyên tắc 1 (giai đoạn phản ánh thực tế) chi phối các sự kiện thương mại mới phát sinh, không chi phối việc sửa lại một sự kiện quá khứ đã ghi sai — áp dụng nguyên tắc 1 một cách máy móc vào tình huống này sẽ tự động hạ một khách hàng đang được nguyên tắc 4 bảo vệ tuyệt đối, chỉ vì một thao tác sửa dữ liệu ở một phân hệ khác, mà không ai trong tổ chức chủ động quyết định điều đó.

- **`BR-12.4` (Chuyển Disqualified):** Chỉ người giữ quyền quản trị **Duyệt vòng đời khách hàng** (Mục 5.2) trên bản ghi mà ô (Khách hàng, Chuyển giai đoạn vòng đời) của họ bao phủ — trong tài liệu gọi tắt là "Quản lý Kinh doanh trở lên" — mới được chuyển khách hàng ở giai đoạn tiền bán hàng sang Disqualified, bắt buộc chọn lý do loại từ A.1. Mở lại một bản ghi Disqualified về Lead hoặc Nurturing cũng chỉ Quản lý Kinh doanh trở lên thực hiện, bắt buộc chọn lý do từ A.17.

  **Lý do nghiệp vụ:** Loại khách là quyết định rút một bản ghi khỏi mọi chiến dịch và mọi phân bổ; nếu nhân viên tự loại được, khách khó chăm sóc sẽ bị loại để làm đẹp chỉ số cá nhân.

- **`BR-12.4b` (Đánh dấu nhanh Lead rác bởi nhân viên):** **Ngoại lệ của `BR-12.4` cho hai lý do hiển nhiên** trong A.1: "Thông tin giả/Spam/Lừa đảo" và "Trùng lặp với bản ghi khác". Nhân viên Kinh doanh được **đánh dấu "Lead rác"** với hai lý do này; việc đánh dấu có hiệu lực **ngay lập tức** ở ba mặt: (a) **đình chỉ đồng hồ cam kết thời gian phản hồi** (`BR-31.7`); (b) **loại bản ghi khỏi mẫu đo `KPI-03`**; (c) **dừng thu hồi và phân bổ lại**. Mọi lượt đánh dấu, dỡ dấu và duyệt đều ghi nhật ký (`NFR-07`).

  **Phạm vi áp dụng:** chỉ sáu giai đoạn tiền bán hàng. Hồ sơ ở Customer/Evangelist/Churned không thuộc phạm vi — nhóm lý do gian lận với các giai đoạn đó thuộc `BR-12.8`.

  **Xử lý sau khi đánh dấu:** Quản lý Kinh doanh duyệt theo lô. Việc chuyển bản ghi sang Disqualified **luôn cần thao tác tường minh của Quản lý Kinh doanh trở lên** — không có cơ chế mặc định chấp thuận. Nếu Quản lý không xử lý trong **5 ngày làm việc** (`BR-31.7b`), hệ thống **giữ nguyên** hiệu lực ba mặt của dấu "Lead rác", bản ghi **đứng nguyên giai đoạn hiện tại**, và hàng đợi chờ duyệt được **leo thang** lên Quản trị viên kèm báo cáo tồn đọng. Nếu Quản lý từ chối, dấu "Lead rác" bị gỡ và đồng hồ cam kết chạy lại từ thời điểm từ chối.

  **Lý do nghiệp vụ:** Spam và trùng lặp là hai lý do loại phổ biến nhất hằng ngày. Nếu cả hai đều đòi Quản lý duyệt trước khi có hiệu lực, mỗi Lead rác sẽ đi qua ba nhân viên (hai lần thu hồi) và làm bẩn chỉ số của cả ba, còn Quản lý thành người bấm nút cho từng dòng — dẫn tới cách lách là gửi email rỗng cho mọi Lead mới để có bằng chứng liên hệ, đúng hành vi `BR-31.8` muốn ngăn.

- **`BR-12.5` (Chuyển Churned):** Khi khách hàng chuyển sang Churned, hệ thống tự động thông báo Quản lý Khách hàng Hiện hữu và Quản lý Kinh doanh phụ trách, đồng thời dừng toàn bộ chiến dịch tiếp thị tự động đối với khách hàng đó.

  **Lý do nghiệp vụ:** Khách rời bỏ vẫn nhận thư khuyến mãi tự động là trải nghiệm tệ và rủi ro khiếu nại; người phụ trách quan hệ phải biết ngay để chủ động giữ chân hoặc ghi nhận nguyên nhân.

- **`BR-12.5b` (Chiến dịch Tái tiếp cận — đường về Nurturing của khách đã rời bỏ):** Một bản ghi Churned chỉ được đưa trở lại Nurturing khi thuộc một **Chiến dịch Tái tiếp cận (Win-Back) đã được phê duyệt**. Ba ràng buộc:
  - **(a) Người phê duyệt:** **Quản lý Marketing cùng Quản lý Kinh doanh phụ trách tập khách đó** — một bên chịu trách nhiệm nội dung tiếp cận, một bên chịu trách nhiệm quan hệ khách hàng.
  - **(b) Ghi nhận:** phê duyệt được ghi trên chính chiến dịch kèm phạm vi tập khách, thời hạn hiệu lực và người phê duyệt, và ghi nhật ký (`NFR-07`).
  - **(c) Không ghi đè đồng thuận:** phê duyệt chiến dịch không thay đổi trạng thái đồng thuận — khách đang Từ chối nhận tin vẫn không nhận thư nhóm Tiếp thị (`BR-30.5`, `BR-30.10`).

  **Lý do nghiệp vụ:** Nhóm khách này đã chủ động chấm dứt quan hệ, nên tiếp cận lại có rủi ro pháp lý và thương hiệu cao hơn chiến dịch thông thường. Không có quy tắc này thì cụm từ "chiến dịch đã được phê duyệt" không xác định được ai phê duyệt và theo quy trình nào.

- **`BR-12.6` (Hiệu lực của Ma trận Chuyển đổi):** Ma trận áp dụng cho mọi nguồn tác động: thao tác thủ công, gộp bản ghi (`BR-19.8`), nhập khẩu hàng loạt, tích hợp từ hệ thống ngoài và chuyển đổi tự động. Bước chuyển ngoài ma trận bị từ chối kèm thông báo nêu rõ giai đoạn hiện tại và các giai đoạn hợp lệ có thể chuyển đến. Với nhập khẩu hàng loạt, dòng chứa bước chuyển không hợp lệ được ghi vào báo cáo lỗi (`BR-24.2`) thay vì làm dừng cả lô.

  **Ngoại lệ duy nhất — nguyên tắc 1:** Bước chuyển do hệ thống tự sinh từ sự kiện Cơ hội bán hàng (`BR-12.9`) và bước chuyển do nguyên tắc giai đoạn tiến xa nhất khi gộp (`BR-19.8`) **luôn hợp lệ theo thiết kế**, trong hai tình huống: **(i)** tenant cấu hình lại ma trận theo `CFG-12-01` và vô tình tắt một bước chuyển thuộc nguyên tắc 1 — nguyên tắc 1 vẫn thắng; **(ii)** thao tác gộp sinh ra bước chuyển theo `BR-19.8` — mọi bước chuyển thuộc nhóm này hợp lệ, **bất kể ma trận có liệt kê hay không**. Các ô "chỉ qua gộp" trong ma trận là ví dụ tường minh của nhóm (ii), không phải danh sách đóng.

  **Lý do nghiệp vụ:** Nhóm (ii) nằm ngoài ma trận vì giai đoạn sau gộp không phải quyết định của người dùng về giai đoạn mà là hệ quả bắt buộc của `BR-19.8` — người dùng chỉ quyết định gộp hai bản ghi nào, giai đoạn kết quả **không thể ghi đè thủ công**. Nếu buộc liệt kê, ma trận phải chứa gần như mọi cặp giai đoạn và mất ý nghĩa kiểm soát đối với thao tác thủ công. Đổi lại, mỗi bước chuyển sinh từ gộp **bắt buộc** ghi nhật ký kèm thông báo cho Người phụ trách rà soát, và hoàn tác được trong thời hạn hoàn tác gộp (`BR-20.3`).

- **`BR-12.7` (Hạ hạng bắt buộc ghi lý do):** Mọi bước lùi về giai đoạn thấp hơn trên phễu tuyến tính, kể cả Evangelist → Customer, bắt buộc chọn lý do từ A.3 và chỉ dành cho Quản lý Kinh doanh trở lên. Lịch sử hạ hạng được ghi nhận riêng để phân tích chất lượng thẩm định của đội ngũ (`FEAT-13`). **Ngoại lệ duy nhất:** bước về Lead hoặc Subscriber do Hoàn tác Chuyển đổi (`BR-14.2`) dùng lý do hoàn tác riêng.

  **Lý do nghiệp vụ:** Bước lùi không có lý do làm mất khả năng phân biệt giữa thẩm định sai, khách đổi nhu cầu và nhập liệu nhầm — ba vấn đề cần ba cách khắc phục khác nhau.

- **`BR-12.8` (Ngoại lệ gian lận với khách đã trả tiền) — sàn bắt buộc:** Khi phát hiện một khách hàng ở Customer, Evangelist **hoặc Churned** là gian lận (thông tin giả, mạo danh, lừa đảo), **chỉ Người có toàn quyền** được chuyển sang Disqualified — năng lực này không đưa được vào bất kỳ vai trò nào, chỉ với lý do thuộc nhóm Gian lận của A.1. Hệ thống bắt buộc hiển thị cảnh báo về ảnh hưởng tới báo cáo doanh thu đã ghi nhận và yêu cầu xác nhận hai bước. Người giữ quyền Duyệt vòng đời khách hàng **không** có quyền này. Ba giai đoạn thuộc quy tắc này và sáu giai đoạn tiền bán hàng thuộc `BR-12.4` hợp thành đủ chín giai đoạn có thể bị loại; giai đoạn thứ mười là chính Disqualified.

  **Lý do nghiệp vụ:** Loại một khách đã từng trả tiền tác động tới doanh thu đã ghi nhận; đây phải là quyết định hiếm, của cấp quản trị, và chỉ với lý do gian lận — một khách đã trả tiền không thể bị loại vì "không đủ ngân sách".

- **`BR-12.9` (Bước chuyển do hệ thống sinh ra từ sự kiện Cơ hội bán hàng):** Các bước chuyển lên Opportunity khi Cơ hội mở đầu tiên được tạo, lên Customer khi có Cơ hội Thắng, và về Lead hoặc Subscriber khi Hoàn tác Chuyển đổi (trả về đúng giai đoạn trước khi chuyển đổi) được coi là **hợp lệ theo thiết kế** và không bị ma trận chặn, kể cả khi nhảy nhiều bậc (ví dụ Subscriber → Opportunity). Các bước chuyển này được ghi vào lịch sử giai đoạn với người thực hiện "Hệ thống" kèm sự kiện nguồn.

  **Lý do nghiệp vụ:** Opportunity được định nghĩa là "đang có ít nhất một Cơ hội bán hàng mở"; khi thực tế đã có Cơ hội thì giai đoạn buộc phải phản ánh đúng thực tế đó, nếu không chính tính năng Chuyển đổi Tiềm năng bị ma trận chặn.

- **`BR-12.10` (Giai đoạn mặc định khi tạo mới):** Giai đoạn khi khởi tạo bản ghi được gán tự động theo nguồn tạo (Phụ lục B, `CFG-12-02`), mặc định chuẩn hệ thống:
  - Tạo thủ công, tạo từ biểu mẫu website, tạo từ hội thoại, nhập khẩu từ tệp → **Lead**;
  - Đăng ký nhận bản tin/tài liệu (không thể hiện nhu cầu mua) → **Subscriber**;
  - Hồ sơ Khách hàng Tạm (`BR-01.1b`) → chưa gán giai đoạn cho tới khi trở thành khách hàng chính thức.

  **Lý do nghiệp vụ:** Giai đoạn khởi tạo phản ánh mức độ thể hiện nhu cầu của nguồn: người đăng ký bản tin chưa thể hiện ý định mua, nên đưa vào Lead sẽ đẩy họ vào hàng đợi kinh doanh quá sớm; Hồ sơ Tạm chưa định danh nên không thuộc phễu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-12.1.1` | Khách hàng ở Lead | Nhân viên chuyển lên SQL sau thẩm định | Lịch sử giai đoạn có dòng Lead → SQL kèm thời điểm và tên nhân viên |
| `AC-12.2.1` | Khách hàng ở MQL | Nhân viên tạo Cơ hội bán hàng trực tiếp từ hồ sơ | Không có lỗi "bước chuyển không hợp lệ"; giai đoạn tự động lên Opportunity |
| `AC-12.2.2` | Khách hàng ở Opportunity, có một Cơ hội đang mở | Cơ hội được đóng Thắng | Giai đoạn tự động lên Customer |
| `AC-12.3.1` | Khách hàng ở Customer, có thêm một Cơ hội bán thêm | Cơ hội bán thêm bị đóng Thua | Giai đoạn vẫn là Customer |
| `AC-12.3.2` | Khách hàng ở Opportunity, chưa từng là Customer, có 2 Cơ hội đang mở | Cả hai Cơ hội bị đóng Thua | Giai đoạn chuyển sang Nurturing; nhân viên được yêu cầu chọn Lý do không chuyển đổi từ A.2 trước khi lưu |
| `AC-12.3.3` | Khách hàng ở Opportunity có 2 Cơ hội đang mở | Một Cơ hội bị đóng Thua, Cơ hội còn lại vẫn mở | Giai đoạn vẫn là Opportunity |
| `AC-12.3b.1` | Khách hàng chưa từng là Customer, ở Opportunity nhờ đúng 1 Cơ hội đang mở | Cơ hội đó bị xóa mềm | Giai đoạn chuyển sang Nurturing; yêu cầu chọn Lý do không chuyển đổi từ A.2 |
| `AC-12.3b.2` | Khách hàng chưa từng là Customer, ở Opportunity nhờ đúng 1 Cơ hội đang mở | Cơ hội đó được sửa để gắn sang một khách hàng khác | Khách hàng ban đầu chuyển sang Nurturing kèm lý do; khách hàng mới được gắn Cơ hội không tự động lên Opportunity qua quy tắc này (áp dụng `BR-12.2` như một Cơ hội thông thường) |
| `AC-12.3b.3` | Khách hàng ở Customer (đã có Cơ hội Thắng trước đó), đồng thời có 1 Cơ hội bán thêm đang mở | Cơ hội bán thêm đó bị xóa mềm | Giai đoạn vẫn là Customer — `BR-12.3b` chỉ áp dụng cho khách chưa từng là Customer |
| `AC-12.3c.1` | Khách hàng ở Customer nhờ đúng 1 Cơ hội Thắng, không có Cơ hội Thắng nào khác | Cơ hội đó được tái phân loại thành Thua (`FEAT-21` của [`deals-pipeline-srs.md`](./deals-pipeline-srs.md)) | Giai đoạn vẫn là Customer, không tự động hạ; Quản lý Kinh doanh trở lên nhận cảnh báo rà soát |
| `AC-12.3c.2` | Tiếp nối AC-12.3c.1 | Quản lý Kinh doanh rà soát, xác nhận khách chưa từng thực sự mua, chủ động hạ giai đoạn theo `BR-12.7` | Giai đoạn hạ xuống theo lựa chọn của Quản lý, kèm lý do bắt buộc |
| `AC-12.3c.3` | Khách hàng ở Customer nhờ 2 Cơ hội Thắng | Một trong hai Cơ hội Thắng bị tái phân loại thành Thua | Giai đoạn vẫn là Customer, không cảnh báo theo `BR-12.3c` — khách vẫn còn ít nhất một Cơ hội Thắng khác xác nhận thực tế đã mua |
| `AC-12.4.1` | Nhân viên Kinh doanh xem hồ sơ khách ở Lead | Tìm hành động chuyển Disqualified | Không có hành động này (chỉ có "Lead rác" theo `BR-12.4b`) |
| `AC-12.4.2` | Quản lý Kinh doanh chuyển khách ở MQL sang Disqualified | Bỏ trống lý do, lưu | Từ chối; danh sách lý do chỉ gồm các giá trị A.1 |
| `AC-12.4.3` | Khách ở Disqualified, có bằng chứng mới | Quản lý Kinh doanh mở lại về Lead | Bắt buộc chọn lý do từ A.17; lịch sử ghi nhận lý do mở lại |
| `AC-12.4b.1` | Nhân viên nhận một Lead tên "asdf asdf", số "0000000000" | Bấm "Lead rác", chọn "Thông tin giả/Spam/Lừa đảo" | Ngay lập tức: đồng hồ cam kết dừng; bản ghi không còn trong mẫu đo `KPI-03`; không bị thu hồi/phân bổ lại; giai đoạn vẫn là Lead |
| `AC-12.4b.2` | Nhân viên bấm "Lead rác" | Mở danh sách lý do | Chỉ có "Thông tin giả/Spam/Lừa đảo" và "Trùng lặp với bản ghi khác" |
| `AC-12.4b.3` | Lead rác chờ duyệt 5 ngày làm việc, Quản lý chưa xử lý | Quá mốc 5 ngày làm việc | Bản ghi vẫn ở giai đoạn cũ, không tự sang Disqualified; Quản trị viên nhận leo thang kèm báo cáo tồn đọng |
| `AC-12.4b.4` | Lead rác chờ duyệt | Quản lý Kinh doanh từ chối | Dấu "Lead rác" bị gỡ; đồng hồ cam kết chạy lại từ thời điểm từ chối |
| `AC-12.4b.5` | Lead rác chờ duyệt | Quản lý Kinh doanh duyệt | Bản ghi chuyển sang Disqualified kèm lý do đã chọn |
| `AC-12.4b.6` | Hồ sơ ở Customer | Nhân viên tìm hành động "Lead rác" | Hành động không khả dụng |
| `AC-12.5.1` | Khách hàng ở Customer, đang trong một chuỗi thư nuôi dưỡng tự động | Chuyển sang Churned | Quản lý Khách hàng Hiện hữu và Quản lý Kinh doanh nhận thông báo; khách không còn nhận thư tiếp thị tự động |
| `AC-12.5.2` | Khách hàng ở Lead | Nhân viên tìm cách chuyển sang Churned bằng tay | Không có bước chuyển này trong danh sách giai đoạn đích |
| `AC-12.5b.1` | Khách ở Churned, không thuộc chiến dịch tái tiếp cận nào | Quản lý tìm cách chuyển sang Nurturing | Từ chối, nêu rõ cần Chiến dịch Tái tiếp cận đã được phê duyệt |
| `AC-12.5b.2` | Chiến dịch tái tiếp cận mới chỉ có Quản lý Marketing phê duyệt | Kích hoạt chiến dịch | Chiến dịch chưa có hiệu lực; khách Churned trong phạm vi chưa được chuyển Nurturing |
| `AC-12.5b.3` | Chiến dịch đã có đủ Quản lý Marketing và Quản lý Kinh doanh phê duyệt | Mở chi tiết chiến dịch | Thấy phạm vi tập khách, thời hạn hiệu lực và tên hai người phê duyệt; khách trong phạm vi chuyển được sang Nurturing |
| `AC-12.5b.4` | Tiếp nối AC-12.5b.3, một khách trong phạm vi đang Từ chối nhận tin qua email | Chiến dịch gửi thư qua email | Khách đó không nhận thư |
| `AC-12.6.1` | Khách hàng ở Nurturing | Nhân viên chọn chuyển sang Evangelist | Từ chối "bước chuyển giai đoạn không hợp lệ", kèm danh sách giai đoạn hợp lệ từ Nurturing |
| `AC-12.6.2` | Lô nhập khẩu có một dòng đổi giai đoạn khách từ Customer về Lead | Chạy nhập khẩu | Dòng đó có trong báo cáo lỗi với nguyên nhân bước chuyển không hợp lệ; các dòng khác vẫn được nhập |
| `AC-12.6.3` | Tenant đã tắt bước Lead → Opportunity trong ma trận cấu hình | Tạo Cơ hội bán hàng cho một khách ở Lead | Giai đoạn vẫn lên Opportunity (nguyên tắc 1 thắng) |
| `AC-12.6.4` | Gộp một bản ghi Lead (Bản ghi Chính) với một bản ghi Customer | Hoàn tất gộp | Bản ghi Chính ở Customer; lịch sử ghi bước chuyển sinh từ gộp; Người phụ trách nhận thông báo rà soát |
| `AC-12.7.1` | Khách hàng ở MQL | Nhân viên Kinh doanh tìm cách lùi về Lead | Không khả dụng với Nhân viên |
| `AC-12.7.2` | Khách hàng ở MQL | Quản lý Kinh doanh lùi về Lead | Bắt buộc chọn lý do từ A.3; lịch sử hạ hạng ghi nhận lý do |
| `AC-12.7.3` | Khách hàng ở Evangelist | Quản lý Kinh doanh chuyển về Customer | Bắt buộc chọn lý do từ A.3 |
| `AC-12.7.4` | Khách hàng ở MQL | Quản lý Kinh doanh mở danh sách giai đoạn đích | Không có Subscriber |
| `AC-12.8.1` | Khách hàng ở Customer | Quản lý Kinh doanh tìm cách chuyển Disqualified | Không khả dụng |
| `AC-12.8.2` | Khách hàng ở Churned, phát hiện mạo danh | Quản trị viên chuyển sang Disqualified | Chỉ các lý do nhóm Gian lận được chọn; hiển thị cảnh báo ảnh hưởng báo cáo doanh thu; yêu cầu xác nhận hai bước |
| `AC-12.9.1` | Khách hàng ở Subscriber | Một Cơ hội bán hàng được tạo cho khách | Giai đoạn lên thẳng Opportunity; lịch sử ghi người thực hiện "Hệ thống" kèm sự kiện "Tạo Cơ hội bán hàng" |
| `AC-12.9.2` | Khách hàng ở Disqualified | Một Cơ hội bán hàng được tạo cho khách | Giai đoạn lên Opportunity; Quản lý Kinh doanh nhận cảnh báo rà soát lại quyết định loại |
| `AC-12.10.1` | Cấu hình mặc định | Tạo khách bằng tay; khách tự đăng ký nhận bản tin; tạo Hồ sơ Tạm từ chat | Lần lượt nhận Lead; Subscriber; chưa gán giai đoạn |

---

#### FEAT-13 — Lịch sử Chuyển đổi Giai đoạn Vòng đời

**Mô tả nghiệp vụ:** Lưu vết toàn bộ lịch sử thăng hạng và hạ hạng giai đoạn vòng đời của khách hàng để phân tích tỷ lệ chuyển đổi và vận tốc chuyển đổi.

**Vai trò sử dụng chính:** Mọi người dùng có quyền xem khách hàng; Quản lý Kinh doanh và Quản lý Marketing (báo cáo).

**Điều kiện tiên quyết:** Người xem có ô (Khách hàng, Xem) bao phủ bản ghi; báo cáo tổng hợp tính trên các bản ghi trong mức Xem của người xem.

**Luồng chính:**

1. Mỗi lần giai đoạn đổi, hệ thống ghi một dòng lịch sử (`BR-13.1`).
2. Người dùng xem lịch sử trên hồ sơ (`BR-13.2`).
3. Người dùng mở báo cáo vận tốc phễu và chất lượng thẩm định (`BR-13.3`).

**Quy tắc nghiệp vụ:**

- **`BR-13.1` (Nội dung ghi nhận):** Mỗi lần chuyển giai đoạn ghi nhận: giai đoạn trước, giai đoạn sau, thời gian đã ở giai đoạn cũ (theo ngày và giờ), lý do chuyển (nếu có), người thực hiện hoặc "Hệ thống" kèm sự kiện nguồn.

  **Lý do nghiệp vụ:** Thời gian ở từng giai đoạn là cơ sở đo điểm nghẽn của quy trình bán hàng; lý do chuyển là cơ sở rà soát chất lượng thẩm định của từng người.

- **`BR-13.2` (Tra cứu lịch sử):** Người dùng xem lịch sử giai đoạn ngay trên hồ sơ khách hàng, theo thứ tự thời gian.

  **Lý do nghiệp vụ:** Nhân viên tiếp nhận khách cần biết ngay khách từng bị loại hay hạ hạng vì sao trước khi liên hệ lại, không phải đi tìm trong báo cáo.

- **`BR-13.3` (Phạm vi tính vận tốc phễu):** Báo cáo tỷ lệ chuyển đổi và vận tốc chuyển đổi chỉ tính trên 7 giai đoạn phễu tuyến tính. Thời gian một bản ghi nằm ở ba trạng thái đặc biệt được báo cáo riêng dưới dạng **"thời gian ngoài phễu"**, không cộng vào vận tốc phễu chính. Các bước hạ hạng (`BR-12.7`) được thống kê riêng trong báo cáo chất lượng thẩm định.

  **Lý do nghiệp vụ:** Một khách nằm ở Nurturing sáu tháng rồi quay lại sẽ làm vận tốc phễu trung bình phình ra gấp nhiều lần nếu cộng vào, che mất điểm nghẽn thật của quy trình bán hàng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-13.1.1` | Khách hàng ở MQL được 5 ngày | Quản lý lùi về Lead với lý do "Thẩm định lại không đủ điều kiện" | Lịch sử có dòng MQL → Lead, thời gian ở MQL là 5 ngày, lý do và tên Quản lý |
| `AC-13.2.1` | Khách hàng đã qua 4 lần chuyển giai đoạn | Mở hồ sơ, xem lịch sử giai đoạn | Thấy đủ 4 dòng theo thứ tự thời gian |
| `AC-13.3.1` | Khách đi Lead (3 ngày) → Nurturing (60 ngày) → MQL | Mở báo cáo vận tốc phễu | 60 ngày ở Nurturing không nằm trong vận tốc phễu; báo cáo "thời gian ngoài phễu" có 60 ngày này |
| `AC-13.3.2` | Trong kỳ có 3 bước hạ hạng | Quản lý Kinh doanh mở báo cáo chất lượng thẩm định | Thấy đủ 3 bước hạ hạng kèm lý do |

---

#### FEAT-14 — Chuyển đổi Khách hàng Tiềm năng Một thao tác

**Mô tả nghiệp vụ:** Khi một khách hàng tiềm năng được thẩm định đủ điều kiện mua hàng, nhân viên kinh doanh kích hoạt Chuyển đổi Tiềm năng trong một thao tác.

**Vai trò sử dụng chính:** Nhân viên Kinh doanh, Quản lý Kinh doanh.

**Điều kiện tiên quyết:** Khách hàng đã có Người phụ trách (`BR-14.6`); người chuyển đổi có ô (Khách hàng, Sửa) bao phủ khách hàng; tạo doanh nghiệp cần ô (Doanh nghiệp, Tạo) = Có; tạo Cơ hội cần quyền tạo Cơ hội theo [`deals-pipeline-srs.md`](./deals-pipeline-srs.md). Khách hàng chưa từng được chuyển đổi thành công.

**Luồng chính:**

1. Người dùng bấm **"Chuyển đổi Tiềm năng"** trên hồ sơ khách hàng tiềm năng.
2. Hộp thoại chuyển đổi hiển thị ba lựa chọn:
   - **Liên hệ:** nâng cấp bản ghi hiện tại thành Liên hệ chính thức. Giai đoạn đích: SQL nếu không tạo Cơ hội kèm theo; Opportunity nếu có Cơ hội. Cả hai bước chuyển đều hợp lệ kể cả khi khách đang ở Subscriber/Lead/MQL — nhánh Opportunity theo `BR-12.9`, nhánh SQL theo `BR-15.6` và đã có trong ma trận.
   - **Doanh nghiệp:** liên kết với một doanh nghiệp đã có, tạo mới doanh nghiệp từ tên công ty của khách, hoặc — với khách loại B2C — chọn "Không liên kết Doanh nghiệp" (`BR-01.6`).
   - **Cơ hội bán hàng:** tùy chọn tạo ngay một Cơ hội (tên, giá trị dự kiến, phễu, giai đoạn khởi đầu). Nếu doanh nghiệp được liên kết **đã có Cơ hội đang mở trên cùng phễu**, hệ thống hiển thị Cơ hội đó và mặc định gắn Liên hệ vào Cơ hội sẵn có (`BR-14.3`).
3. Người dùng bấm "Xác nhận chuyển đổi".
4. Hệ thống cập nhật Liên hệ, tạo/liên kết Doanh nghiệp, tạo Cơ hội mới **hoặc** gắn Liên hệ vào Cơ hội sẵn có, xác định Người phụ trách theo `BR-14.7`, rồi chuyển người dùng tới Cơ hội tương ứng.

**Quy tắc nghiệp vụ:**

- **`BR-14.1` (Toàn vẹn khi chuyển đổi):** Toàn bộ các bước tại luồng chính **cùng thành công hoặc cùng thất bại**. Nếu bất kỳ bước nào thất bại, không có Liên hệ, Doanh nghiệp hay Cơ hội nào ở trạng thái dang dở, và giai đoạn của khách giữ nguyên như trước khi chuyển đổi.

  **Lý do nghiệp vụ:** Một chuyển đổi dở dang (đã tạo doanh nghiệp nhưng chưa tạo cơ hội) để lại bản ghi mồ côi mà người dùng không biết phải dọn hay chuyển đổi lại, và lần chuyển đổi lại sẽ sinh trùng.

- **`BR-14.2` (Hoàn tác Chuyển đổi):** Trong vòng **24 giờ** sau khi chuyển đổi thành công (Phụ lục B, `CFG-14-01`), người giữ quyền quản trị **Hoàn tác Chuyển đổi Tiềm năng** (Mục 5.2) có ô (Khách hàng, Sửa) bao phủ khách hàng được **Hoàn tác Chuyển đổi**, với điều kiện Cơ hội vừa tạo **chưa có hoạt động thực tế nào** (chưa có ghi chú, chưa chuyển giai đoạn bán hàng, chưa đính kèm tài liệu). Khi hoàn tác:
  - (a) Cơ hội vừa tạo bị xóa mềm và **không khôi phục được** từ Thùng rác;
  - (b) Doanh nghiệp vừa tạo bị xóa mềm nếu chưa có khách hàng nào khác liên kết;
  - (c) Liên hệ trở về **đúng giai đoạn trước khi chuyển đổi** — bước chuyển hợp lệ theo `BR-12.9`, **không** yêu cầu lý do hạ hạng theo `BR-12.7`, nhưng bắt buộc chọn **Lý do hoàn tác** từ A.15;
  - (d) Điểm tiềm năng và nguồn gốc tiếp thị được giữ nguyên;
  - (e) Toàn bộ thao tác được ghi nhật ký (`NFR-07`).

  Điều kiện đầy đủ, quyền cần có và nhánh gắn vào Cơ hội sẵn có quy định tại `BR-14.5` — danh sách duy nhất mà [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `BR-04.5` dẫn chiếu.

  **Lý do nghiệp vụ:** Chuyển đổi nhầm là sai sót thường gặp; không có đường hoàn tác thì cách duy nhất là xóa tay từng bản ghi, làm hỏng lịch sử giai đoạn và nguồn gốc của khách. Giới hạn "chưa có hoạt động" bảo đảm hoàn tác không xóa mất công việc thật đã làm trên Cơ hội.

- **`BR-14.3` (Chống tạo trùng Cơ hội khi chuyển đổi):** Trước khi tạo Cơ hội mới, hệ thống bắt buộc kiểm tra doanh nghiệp được liên kết đã có **Cơ hội nào đang mở trên cùng phễu đích** hay chưa, **chỉ xét các Cơ hội nằm trong ô (Cơ hội, Sửa) của người thực hiện chuyển đổi** — thống nhất với [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `BR-33.1`, `BR-01.8`; Cơ hội ngoài phạm vi không được gợi ý và không bị tiết lộ.
  - **Nếu có đúng một:** mặc định gắn Liên hệ vào Cơ hội đó, không tạo Cơ hội mới; người thực hiện được thông báo rõ đang gắn vào Cơ hội nào.
  - **Nếu có nhiều hơn một:** hệ thống không tự chọn; người thực hiện bắt buộc chọn một Cơ hội trong danh sách hoặc chọn tạo Cơ hội mới.
  - **Người phụ trách của Cơ hội sẵn có không đổi** khi Liên hệ được gắn vào; Cơ hội tạo mới có Người phụ trách là người thực hiện chuyển đổi.
  - **Nếu người dùng vẫn muốn tạo riêng:** được chủ động chọn tạo Cơ hội mới, và quyết định này được ghi nhật ký để rà soát chất lượng dữ liệu.
  - Phạm vi kiểm tra là **theo từng phễu**, không phải toàn doanh nghiệp — một doanh nghiệp có thể có song song một Cơ hội bán mới và một Cơ hội gia hạn ở hai phễu khác nhau.

  **Lý do nghiệp vụ:** Không có quy tắc này, mỗi lần một nhân sự khác của cùng doanh nghiệp được chuyển đổi sẽ sinh thêm một Cơ hội trùng trên cùng thương vụ — dự báo doanh thu bị thổi phồng nhiều lần, và hai nhân viên có thể cùng đàm phán một hợp đồng mà không biết nhau. Quy tắc tương ứng phía Cơ hội bán hàng tại [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) (`FEAT-33` của tài liệu đó).

- **`BR-14.4` (Giai đoạn khi gắn vào Cơ hội sẵn có):** Khi Liên hệ được gắn vào một Cơ hội đang mở thay vì tạo Cơ hội mới, giai đoạn của Liên hệ vẫn được nâng lên Opportunity theo `BR-12.2`, vì thực tế thương mại (người này đang tham gia một thương vụ) là như nhau ở cả hai nhánh.

  **Lý do nghiệp vụ:** Thực tế thương mại — người này đang tham gia một thương vụ — là như nhau dù cơ hội mới tạo hay đã có; để hai nhánh cho hai giai đoạn khác nhau sẽ làm cùng một tình huống báo cáo khác nhau.

- **`BR-14.5` (Điều kiện Hoàn tác Chuyển đổi — danh sách duy nhất):** Mọi phân hệ dẫn chiếu danh sách này; [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `BR-04.5` chỉ quy định phía Cơ hội. Hoàn tác thực hiện được khi và chỉ khi thỏa **đồng thời**:
  - **(1) Thời hạn:** còn trong `CFG-14-01` kể từ lượt chuyển đổi.
  - **(2) Quyền:** người hoàn tác giữ quyền quản trị Hoàn tác Chuyển đổi Tiềm năng và có ô (Khách hàng, Sửa) bao phủ Liên hệ; ô (Cơ hội, Xoá) bao phủ Cơ hội vừa tạo; ô (Doanh nghiệp, Xoá) bao phủ Doanh nghiệp vừa tạo sẽ bị xóa mềm; ô (Cơ hội, Sửa) bao phủ Cơ hội sẵn có sẽ bị gỡ liên kết.
  - **(3) Chưa có hoạt động thực tế:** Cơ hội vừa tạo chưa có ghi chú, chưa chuyển giai đoạn bán hàng, chưa đính kèm tài liệu; ở nhánh gắn vào Cơ hội sẵn có, **Liên hệ chưa có hoạt động thực tế nào của riêng mình** trên Cơ hội đó (chưa được gắn Vai trò liên hệ khác với lúc gắn tự động, chưa có ghi chú hay hoạt động nhắc tới Liên hệ này gắn với Cơ hội đó).
  - **(4) Toàn bộ hoặc không:** thiếu bất kỳ điều kiện nào thì hành động hoàn tác bị vô hiệu kèm giải thích nêu đúng điều kiện còn thiếu — hoàn tác không bao giờ thực hiện một phần.

  Ở nhánh gắn vào Cơ hội sẵn có, các bước (a) và (b) của `BR-14.2` **không áp dụng**, vì Cơ hội và Doanh nghiệp liên quan đã tồn tại từ trước và có thể đang phục vụ một thương vụ khác. Thay vào đó:
  - **(a')** Hệ thống chỉ **gỡ liên kết** giữa Liên hệ và Cơ hội sẵn có, và **gỡ nguồn gốc bổ sung** mà lượt chuyển đổi đã ghi thêm vào Cơ hội đó;
  - **(b')** Cơ hội sẵn có, Người phụ trách của nó và Doanh nghiệp liên quan **giữ nguyên**, không bị xóa mềm.

  **Lý do nghiệp vụ:** Hai phân hệ cùng chạm một lượt hoàn tác; nếu mỗi bên giữ một danh sách điều kiện, hai danh sách sẽ lệch nhau và một lượt hoàn tác thành công ở bên này bị từ chối ở bên kia. Cơ hội bị xóa do hoàn tác không khôi phục được vì đó là thương vụ đã được xác định là ghi nhầm; khôi phục nó là sống lại một sai sót. Cơ hội sẵn có ở nhánh `BR-14.3` không phải sản phẩm của lần chuyển đổi đang được hoàn tác — nó có thể đang được đàm phán bởi một nhân viên khác từ trước. Áp nguyên bước (a)/(b) của `BR-14.2` vào nhánh này sẽ xóa mất một thương vụ có thật không liên quan gì tới sai sót đang được sửa.

- **`BR-14.6` (Chuyển đổi bản ghi chưa có Người phụ trách):** Khách hàng tiềm năng đang chờ phân công trong hàng đợi, hoặc bản ghi giữ chỗ, **không chuyển đổi được** cho tới khi có Người phụ trách: người muốn chuyển đổi phải nhận việc trước (`BR-31.9` (c)) hoặc được gán theo `BR-34.1`. Nút "Chuyển đổi Tiềm năng" trên bản ghi chưa có Người phụ trách bị vô hiệu kèm lối "Nhận việc" khi người dùng đủ điều kiện.

  **Lý do nghiệp vụ:** Chuyển đổi gán Người phụ trách cho Liên hệ và Cơ hội; làm việc đó trên một bản ghi chưa ai nhận là cách nhận việc đi vòng qua điều kiện của hàng đợi, và khiến khách rời đơn vị tiếp nhận mà không ai ghi nhận người chịu trách nhiệm.

- **`BR-14.7` (Người phụ trách sau chuyển đổi):** Chuyển đổi **không** là một thao tác Gán: Liên hệ **giữ Người phụ trách hiện tại**; Doanh nghiệp và Cơ hội **tạo mới** trong lượt chuyển đổi do người thực hiện chuyển đổi phụ trách (thống nhất [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `BR-33.1`); Doanh nghiệp và Cơ hội **sẵn có** giữ Người phụ trách của chúng. Mọi thay đổi Người phụ trách khác — kể cả giao Cơ hội vừa tạo cho chính Người phụ trách của Liên hệ — là thao tác riêng sau chuyển đổi, chịu ô Gán của loại dữ liệu đó theo `BR-34.1`.

  **Lý do nghiệp vụ:** Nếu chuyển đổi tự đổi người phụ trách, một người có quyền sửa khách hàng sẽ dùng nó để chiếm khách của đồng nghiệp, hoặc giao cơ hội cho người mà chính họ không được phép giao — đi vòng qua ô Gán. Người tạo cơ hội phụ trách nó để luôn có người chịu trách nhiệm ngay từ đầu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-14.1.1` | Lead "Trần Thị Mai", chưa có doanh nghiệp | Chuyển đổi, tạo doanh nghiệp mới và Cơ hội 500 triệu | Liên hệ ở Opportunity; doanh nghiệp và Cơ hội được tạo; người dùng được chuyển tới màn hình Cơ hội; lịch sử giai đoạn ghi sự kiện nguồn "Chuyển đổi Tiềm năng" |
| `AC-14.1.2` | Lead ở MQL | Chuyển đổi, không tạo Cơ hội | Liên hệ lên SQL, không báo lỗi bước chuyển |
| `AC-14.1.3` | Việc tạo Cơ hội thất bại giữa chừng khi chuyển đổi | Xác nhận chuyển đổi | Người dùng nhận thông báo thất bại; không có doanh nghiệp hay Cơ hội mới nào; giai đoạn của khách không đổi |
| `AC-14.2.1` | Chuyển đổi lúc 09:00, Cơ hội vừa tạo chưa có hoạt động, doanh nghiệp vừa tạo chưa có liên hệ khác | Quản lý Kinh doanh hoàn tác lúc 10:30 cùng ngày, chọn lý do từ A.15 | Cơ hội và doanh nghiệp bị xóa mềm; Liên hệ về đúng Lead; không bị hỏi lý do hạ hạng; điểm và nguồn gốc giữ nguyên; nhật ký ghi đầy đủ |
| `AC-14.2.2` | Tiếp nối AC-14.2.1 nhưng nhân viên đã thêm 1 ghi chú vào Cơ hội | Quản lý bấm hoàn tác | Từ chối: "Không thể hoàn tác: Cơ hội đã phát sinh hoạt động" |
| `AC-14.2.3` | Chuyển đổi lúc 09:00, thời hạn 24 giờ | 08:59 hôm sau mở hồ sơ; 09:05 hôm sau mở lại | Lúc 08:59 còn hành động hoàn tác; lúc 09:05 hành động không còn hiển thị |
| `AC-14.2.4` | Chuyển đổi đã gắn vào doanh nghiệp có sẵn 3 liên hệ khác | Hoàn tác | Doanh nghiệp không bị xóa |
| `AC-14.2.5` | Nhân viên Kinh doanh xem hồ sơ vừa chuyển đổi | Tìm hành động hoàn tác | Không khả dụng với Nhân viên |
| `AC-14.3.1` | Doanh nghiệp Vina có đúng một Cơ hội "Cung ứng Q3" đang mở trên Phễu A, do nhân viên K phụ trách, nằm trong ô (Cơ hội, Sửa) của người chuyển đổi | Chuyển đổi Lead "Nguyễn Văn Bình" của Vina, chọn Phễu A | Hộp thoại hiển thị Cơ hội "Cung ứng Q3" và mặc định gắn Bình vào đó; không phát sinh Cơ hội thứ hai; dự báo doanh thu không tăng; Người phụ trách Cơ hội vẫn là K |
| `AC-14.3.4` | Cơ hội đang mở duy nhất của Vina trên Phễu A nằm ngoài ô (Cơ hội, Sửa) của người chuyển đổi | Chuyển đổi Lead của Vina, chọn Phễu A | Không có gợi ý gắn, không lộ tên hay giá trị Cơ hội đó; Cơ hội mới được tạo |
| `AC-14.3.5` | Vina có hai Cơ hội đang mở trên Phễu A trong phạm vi người chuyển đổi | Chuyển đổi Lead của Vina, chọn Phễu A | Hộp thoại liệt kê hai Cơ hội và bắt buộc chọn một hoặc chọn tạo mới; không có lựa chọn mặc định |
| `AC-14.7.1` | Khách K do nhân viên E phụ trách; Quản lý Q (ô Sửa Khách hàng bao phủ K) chuyển đổi K, tạo doanh nghiệp mới và Cơ hội mới | Xác nhận chuyển đổi | Liên hệ K vẫn do E phụ trách; doanh nghiệp và Cơ hội mới do Q phụ trách; muốn giao Cơ hội cho E, Q dùng thao tác Gán riêng, chịu ô (Cơ hội, Gán người phụ trách) của Q |
| `AC-14.6.1` | Khách tiềm năng T nằm trong hàng đợi chưa phân công | Thành viên đơn vị tiếp nhận mở hồ sơ T | Nút "Chuyển đổi Tiềm năng" bị vô hiệu kèm lối "Nhận việc"; sau khi nhận việc, chuyển đổi được |
| `AC-14.5.3` | Chuyển đổi đã gắn Liên hệ vào Cơ hội sẵn có và ghi thêm nguồn gốc "Sự kiện" vào Cơ hội đó | Quản lý hoàn tác | Liên hệ bị gỡ khỏi Cơ hội; nguồn gốc "Sự kiện" bổ sung bị gỡ; Người phụ trách Cơ hội không đổi |
| `AC-14.5.4` | Cơ hội bị xóa mềm do hoàn tác chuyển đổi | Người có ô (Cơ hội, Xoá) mở Thùng rác | Cơ hội hiển thị nhãn "Xóa do hoàn tác chuyển đổi", không có hành động Khôi phục |
| `AC-14.2.6` | Quản lý có quyền Hoàn tác Chuyển đổi nhưng ô (Doanh nghiệp, Xoá) không bao phủ doanh nghiệp vừa tạo | Mở hành động hoàn tác | Hành động bị vô hiệu, nêu thiếu ô (Doanh nghiệp, Xoá); không bản ghi nào bị thay đổi |
| `AC-12.3b.4` | Khách ở Lead được chuyển đổi kèm Cơ hội mới, lên Opportunity | Quản lý hoàn tác chuyển đổi trong thời hạn | Khách về Lead; không bị chuyển sang Nurturing và không bị hỏi Lý do không chuyển đổi |
| `AC-14.3.2` | Tiếp nối AC-14.3.1 | Nhân viên chọn vẫn tạo Cơ hội riêng | Cơ hội thứ hai được tạo; nhật ký ghi quyết định kèm người thực hiện |
| `AC-14.3.3` | Doanh nghiệp Vina có Cơ hội đang mở trên Phễu A | Chuyển đổi một Lead của Vina, chọn Phễu B | Cơ hội mới được tạo trên Phễu B, không có gợi ý gắn vào Cơ hội ở Phễu A |
| `AC-14.4.1` | Tiếp nối AC-14.3.1 | Xem giai đoạn của Bình | Bình ở Opportunity |
| `AC-14.5.1` | Tiếp nối AC-14.3.1: Bình đã được gắn tự động vào Cơ hội "Cung ứng Q3", chưa có hoạt động riêng nào gắn với Bình trên Cơ hội đó | Quản lý Kinh doanh hoàn tác trong thời hạn cho phép, chọn lý do từ A.15 | Liên kết giữa Bình và Cơ hội "Cung ứng Q3" bị gỡ; Bình về đúng giai đoạn trước khi chuyển đổi; Cơ hội "Cung ứng Q3" và Doanh nghiệp Vina giữ nguyên, không bị xóa mềm |
| `AC-14.5.2` | Tiếp nối AC-14.5.1 nhưng nhân viên đã gắn thêm Vai trò liên hệ riêng cho Bình trên Cơ hội "Cung ứng Q3" | Quản lý bấm hoàn tác | Từ chối: "Không thể hoàn tác: đã phát sinh hoạt động của Liên hệ này trên Cơ hội" |

---

#### FEAT-31 — Phân bổ Khách hàng Tiềm năng Tự động

**Mô tả nghiệp vụ:** Khi khách hàng tiềm năng được tạo tự động từ các kênh số (biểu mẫu website, tích hợp từ hệ thống ngoài, trợ lý trò chuyện tự động, quảng cáo) mà không có người tạo trực tiếp, hệ thống tự động phân bổ Người phụ trách theo bộ quy tắc định sẵn.

**Vai trò sử dụng chính:** Tiến trình Hệ thống (thực thi), Quản lý Kinh doanh (cấu hình quy tắc), thành viên đơn vị tiếp nhận (nhận việc từ hàng đợi), Quản trị viên.

**Điều kiện tiên quyết:** Nguồn đã có đơn vị tiếp nhận (`BR-31.9`); quy tắc phân bổ do người giữ quyền Cấu hình phân bổ khách hàng tiềm năng thiết lập trong phạm vi ô Gán của mình (`BR-31.10`).

**Luồng chính:**

1. Một khách hàng tiềm năng đến từ nguồn không có người tạo trực tiếp; hệ thống kiểm tra trùng lặp và áp ưu tiên Người phụ trách hiện hữu (`BR-31.6`).
2. Không trùng thì khách vào hàng đợi của đơn vị tiếp nhận của nguồn dưới dạng bản ghi chờ phân công (`BR-31.9`).
3. Hệ thống áp các quy tắc phân bổ theo thứ tự ưu tiên (`BR-31.3b`) và gán Người phụ trách trong giới hạn `BR-31.10`.
4. Không khớp quy tắc nào thì khách nằm lại trong hàng đợi chưa phân công; thành viên đơn vị tiếp nhận nhận việc hoặc người có ô Gán phân công thủ công (`BR-31.4`).
5. Hệ thống theo dõi cam kết phản hồi lần đầu và thu hồi, phân bổ lại khi quá hạn (`BR-31.7`).

**Quy tắc nghiệp vụ:**

- **`BR-31.1` (Chia vòng lần lượt):** Khi không có quy tắc đặc biệt nào khớp, hệ thống chia khách hàng tiềm năng lần lượt đều nhau cho các thành viên kinh doanh **đang khả dụng** trong nhóm. Người đang ở trạng thái "không khả dụng" (`BR-34.6`) không nhận phân bổ mới.

  **Lý do nghiệp vụ:** Chia đều là cách công bằng và dễ hiểu nhất khi không có căn cứ chuyên môn nào; bỏ qua người không khả dụng để khách không rơi vào tay người đang nghỉ.

- **`BR-31.2` (Theo vùng địa lý):** Nếu khách hàng tiềm năng có thông tin quốc gia hoặc tỉnh/thành, ưu tiên phân bổ cho nhân viên phụ trách vùng địa lý tương ứng.

  **Lý do nghiệp vụ:** Khách được phục vụ bởi người hiểu thị trường địa phương và có thể gặp trực tiếp thì chuyển đổi tốt hơn; doanh nghiệp tổ chức đội theo vùng cần phân bổ đi theo cấu trúc đó.

- **`BR-31.3` (Theo ngành nghề):** Nếu khách hàng tiềm năng có thông tin ngành nghề, ưu tiên phân bổ cho nhân viên chuyên ngành tương ứng.

  **Lý do nghiệp vụ:** Ngành có quy trình mua và thuật ngữ riêng; nhân viên chuyên ngành tư vấn đúng ngay từ cuộc gọi đầu.

- **`BR-31.3b` (Thứ tự ưu tiên giữa các quy tắc):** Khi khớp nhiều quy tắc cùng lúc, hệ thống áp dụng theo thứ tự giảm dần: **(1)** Người phụ trách hiện hữu (`BR-31.6` — ưu tiên tuyệt đối); **(2)** Vùng địa lý; **(3)** Ngành nghề; **(4)** Chia vòng lần lượt; **(5)** Hàng đợi "Chưa phân công" (`BR-31.4`). Thứ tự từ (2) đến (4) là tham số cấu hình (Phụ lục B, `CFG-31-02`); vị trí (1) cố định.

  **Lý do nghiệp vụ:** Có tổ chức phân đội theo ngành trước, có tổ chức phân theo vùng trước; nhưng không tổ chức nào muốn một khách đang có người phụ trách lại bị chia cho người thứ hai.

- **`BR-31.4` (Khi không khớp quy tắc nào):** Khi không có quy tắc nào khớp hoặc không có nhân viên khả dụng, khách hàng tiềm năng nằm lại trong **hàng đợi "Chưa phân công" của đơn vị tiếp nhận** (`BR-31.9`); người phụ trách đơn vị tiếp nhận nhận thông báo. Thành viên đơn vị tiếp nhận tự nhận việc, hoặc người có ô (Khách hàng, Gán) bao phủ đơn vị tiếp nhận phân công thủ công cho người đạt điều kiện người nhận của `BR-34.1`.

  **Lý do nghiệp vụ:** Không có điểm đến tường minh thì khách không khớp quy tắc nào trở thành vô chủ (Nguyên tắc 5); hàng đợi thuộc đúng đơn vị tiếp nhận để chính đội đó thấy và nhận, thay vì dồn hết cho một người quản lý.

- **`BR-31.5` (Quyền cấu hình):** Quy tắc phân bổ chỉ được tạo, sửa, xóa bởi người giữ quyền quản trị **Cấu hình phân bổ khách hàng tiềm năng** (Mục 5.2; mặc định ở vai trò Quản lý) và Người có toàn quyền, trong giới hạn ô Gán tại `BR-31.10` (a) — khớp ma trận Mục 5 dòng `FEAT-31`.

  **Lý do nghiệp vụ:** Quy tắc phân bổ quyết định ai nhận khách nào, tức là quyết định thu nhập và khối lượng công việc của cả đội; người không chịu trách nhiệm về kết quả của đội không được tự đổi nó.

- **`BR-31.6` (Ưu tiên tuyệt đối cho Người phụ trách hiện hữu):** Trước khi áp bất kỳ quy tắc phân bổ nào, hệ thống bắt buộc kiểm tra trùng lặp (`FEAT-17`). Nếu khách hàng tiềm năng mới trùng với một bản ghi đã có **Người phụ trách** (Đang hoạt động hoặc đang Tạm ngưng), hệ thống:
  - **không tạo bản ghi mới** và **không phân bổ cho người khác** — tương tác mới được ghi vào Dòng thời gian của bản ghi hiện hữu;
  - thông báo cho Người phụ trách hiện hữu: "Khách hàng bạn đang phụ trách vừa phát sinh yêu cầu mới từ kênh [tên kênh]";
  - nếu Người phụ trách hiện hữu đang Tạm ngưng, bản ghi **không** được phân bổ lại: Người phụ trách giữ nguyên (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-42.1`), yêu cầu mới chuyển cho người xử lý thay đã chỉ định khi tạm ngưng, không có thì cho quản lý trực tiếp của người đó — hoặc quản lý tạm thời nếu đã được chỉ định (`BR-34.4` (b)); người đang Tạm ngưng không nhận thông báo nào. Người đã rời workspace không còn là Người phụ trách của bản ghi nào, vì gỡ khỏi workspace chỉ xảy ra sau khi mọi bản ghi đã có người nhận (`BR-34.4` (c)).

  Để "chống trùng chủ" không thành chỗ mất doanh thu, yêu cầu mới trên bản ghi đã có chủ bắt buộc sinh ra một **"Yêu cầu chờ xử lý"** có cam kết thời gian riêng, áp cùng bộ thời hạn tại `BR-31.7` theo mức ưu tiên của bản ghi. Quá hạn thì leo thang lên Quản lý Kinh doanh, và Quản lý được **chỉ định người xử lý thay** (không đổi Người phụ trách chính, có thể dùng Đội ngũ phụ trách tại `FEAT-35`). Người phụ trách đang nghỉ phép hoặc "không khả dụng" (`BR-34.6`) thì yêu cầu được chuyển ngay cho người xử lý thay.

  **Lý do nghiệp vụ:** Bảo đảm không có chuyện hai nhân viên cùng liên hệ một khách hàng (Mục 2.1, vấn đề 2). Nhưng khách cũ quay lại hỏi mua thêm là nguồn nhu cầu chất lượng cao nhất — nếu không có thời hạn và không ai được phép nhận thay, yêu cầu sẽ nằm im vô thời hạn trong dòng thời gian.

- **`BR-31.7` (Cam kết thời gian phản hồi & thu hồi khách bị bỏ quên):** Sau khi được phân bổ, khách hàng tiềm năng phải được liên hệ lần đầu — bằng một bằng chứng được công nhận tại `BR-31.8` — trong thời hạn cam kết mặc định (Phụ lục B, `CFG-31-01`):

| Mức ưu tiên | Dải điểm tiềm năng | Thời hạn phản hồi lần đầu | Hành động khi quá hạn |
| --- | --- | --- | --- |
| **Ưu tiên cao** | Từ Ngưỡng Ưu tiên cao trở lên (mặc định ≥ 85) | **1 giờ làm việc** | Nhắc người phụ trách và thông báo Quản lý Kinh doanh |
| **Thông thường** | Từ Ngưỡng MQL tới dưới Ngưỡng Ưu tiên cao (mặc định 40–84) | **4 giờ làm việc** | Nhắc người phụ trách |
| **Thấp** | Dưới Ngưỡng MQL (mặc định < 40) | **24 giờ làm việc** | Ghi vào báo cáo tồn đọng |

  Ba dải điểm **luôn được tính lại từ hai ngưỡng tại `CFG-15-01`**, không đóng cứng con số, nên khi tenant hiệu chỉnh ngưỡng thì ba dải vẫn kề nhau và không chồng lấn.

  Nếu khách vẫn không được phản hồi sau **hai lần thời hạn** nêu trên, hệ thống tự động **thu hồi và phân bổ lại** cho thành viên khác theo `BR-31.1`, ghi lý do "Quá hạn phản hồi" vào lịch sử và thông báo Quản lý Kinh doanh. Tối đa **2 lần thu hồi tự động** cho mỗi khách; đến lần thứ ba, khách được đưa vào hàng đợi "Chưa phân công" của đơn vị tiếp nhận (`BR-31.9`) để người có ô (Khách hàng, Gán) bao phủ đơn vị đó phân công thủ công và chịu trách nhiệm. Thu hồi và phân bổ lại chịu cùng giới hạn của `BR-31.10`. Đồng hồ cam kết **dừng** khi bản ghi bị đánh dấu Lead rác (`BR-12.4b`) hoặc bị gắn Hạn chế xử lý (`BR-30.6`), và không áp dụng cho bản ghi nhập khẩu trước khi có tương tác đầu tiên (`BR-15.7` (a)).

  **Lý do nghiệp vụ:** Tốc độ phản hồi lần đầu là yếu tố quyết định tỷ lệ chuyển đổi của khách hàng tiềm năng. Giới hạn số lần thu hồi để khách không bị quay vòng vô hạn giữa các nhân viên mà không ai chịu trách nhiệm.

- **`BR-31.7b` (Lịch làm việc dùng để tính thời hạn):** Mọi thời hạn tính bằng "giờ làm việc" hoặc "ngày làm việc" trong tài liệu (`BR-12.4b`, `BR-17.2c`, `BR-31.6`, `BR-31.7`, `BR-34.1b`, `BR-35.3b`) được tính theo **Lịch làm việc của không gian làm việc**: múi giờ, các ngày làm việc trong tuần, giờ bắt đầu và kết thúc mỗi ngày, và danh mục ngày lễ theo từng năm. Lịch do Chủ sở hữu khai báo (Phụ lục B, `CFG-31-03`); khi chưa khai báo, hệ thống dùng mặc định Thứ Hai – Thứ Sáu, 08:00 – 17:30 theo múi giờ của không gian làm việc, không có ngày lễ. Lịch hợp lệ phải có **ít nhất một ngày làm việc trong tuần** và giờ kết thúc **sau** giờ bắt đầu.

  **Lý do nghiệp vụ:** Không có định nghĩa này thì hai người kiểm thử tính ra hai thời điểm quá hạn khác nhau. Một lịch không có ngày làm việc nào sẽ khiến mọi thời hạn tính bằng giờ làm việc không bao giờ đến hạn, vô hiệu hóa toàn bộ cơ chế leo thang và thu hồi.

- **`BR-31.8` (Bằng chứng "đã liên hệ lần đầu"):** Chỉ các bằng chứng sau được công nhận:
  - **Nhóm 1 — bằng chứng hệ thống tự sinh:** cuộc gọi có ghi nhận thời lượng, email đã gửi từ hệ thống, tin nhắn đã gửi trên kênh đã tích hợp, hoặc cuộc hẹn đã được tạo với khách hàng. Các bằng chứng này do `FEAT-36` sinh tự động (`BR-36.5`) nên không tạo khống được.
  - **Nhóm 2 — liên hệ ngoài hệ thống có xác nhận của Quản lý:** gặp trực tiếp, khách chỉ trả lời qua kênh cá nhân của nhân viên, hoặc nhân viên gọi bằng số không tích hợp. Nhân viên khai báo và **người giữ quyền quản trị Xác nhận liên hệ ngoài hệ thống** (Mục 5.2; mặc định Quản lý) có ô (Khách hàng, Gán) bao phủ bản ghi xác nhận — người xác nhận phải khác người khai báo; mỗi nhân viên dùng tối đa **10 lần/tháng** (Phụ lục B, `CFG-31-04`), có ghi nhật ký, và được **thống kê riêng** trong `KPI-03`.
  - **Không được công nhận:** ghi chú thủ công đơn thuần không kèm bằng chứng nhóm 1 hoặc nhóm 2.
  - **Khi không gian làm việc chưa tích hợp kênh nào** sinh được bằng chứng nhóm 1, cơ chế **thu hồi tự động tại `BR-31.7` không được kích hoạt** — hệ thống chỉ nhắc nhở và ghi vào báo cáo tồn đọng.

  **Lý do nghiệp vụ:** Nếu ghi chú tay được tính, nhân viên chỉ cần gõ "đã gọi, không bắt máy" là đạt cam kết, và `KPI-03` đạt trên giấy trong khi khách chưa được liên hệ. Ngược lại, thu hồi khách khỏi người đang thực sự làm việc chỉ vì hệ thống không nhìn thấy công việc đó sẽ khiến khách nhận cuộc gọi thứ hai từ cùng công ty.

- **`BR-31.9` (Hàng đợi khách hàng tiềm năng và đơn vị tiếp nhận):** Phân hệ khai báo hàng đợi của mình theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10` – `BR-35.14`:
  - **(a) Nguồn và đơn vị tiếp nhận:** mỗi nguồn sinh khách hàng tiềm năng không có người tạo trực tiếp — từng biểu mẫu website, từng tích hợp từ hệ thống ngoài, từng trợ lý trò chuyện tự động, từng nguồn quảng cáo, từng kênh hội thoại sinh Hồ sơ Khách hàng Tạm — có đúng một **đơn vị tiếp nhận**, tùy chọn "gồm các đơn vị con". Nguồn mới nhận đơn vị tiếp nhận mặc định theo loại nguồn (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-35-01`, tìm từ Đơn vị chính của người tạo nguồn lên đơn vị gốc); chọn đơn vị khác, đổi đơn vị tiếp nhận của nguồn đang có, bật hay tắt "gồm các đơn vị con" chỉ do Người có toàn quyền thực hiện (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.12`). Nguồn chưa có đơn vị tiếp nhận không kích hoạt hay công khai được. Lô nhập khẩu vào hàng đợi khai báo đơn vị tiếp nhận theo `BR-23.5`.
  - **(b) Loại bản ghi:** mọi khách hàng tiềm năng đi vào hàng đợi là **bản ghi chờ phân công** — thuộc đơn vị tiếp nhận cho tới khi có Người phụ trách, sau đó thuộc đơn vị theo Người phụ trách (`BR-01.3`). Phân hệ không có bản ghi công việc. Khách trùng với một bản ghi đã có Người phụ trách Đang hoạt động hoặc Tạm ngưng không vào hàng đợi (`BR-31.6`).
  - **(c) Thấy và nhận việc:** mọi thành viên của đơn vị tiếp nhận — Đơn vị chính hoặc kiêm nhiệm ở bất kỳ mức nào, cả nhánh khi bật "gồm các đơn vị con" — có ô (Khách hàng, Xem) khác Không có đều thấy khách chưa có Người phụ trách trong hàng đợi, bất kể mức Xem. **Nhận việc** là thao tác Gán cho chính mình, chỉ khả dụng khi ô (Khách hàng, Gán) của người đó khác Không có (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.11`); người có ô Gán bao phủ đơn vị tiếp nhận gán được cho người khác, chỉ khi đạt `BR-34.1` — ô Gán mức Chỉ của mình chỉ cho nhận việc về mình.
  - **(d) Giai đoạn chưa chốt và trả về hàng đợi:** bản ghi chờ phân công được coi là **chưa chốt** khi chưa từng được Chuyển đổi Tiềm năng thành công (`FEAT-14`) và đang ở giai đoạn tiền bán hàng khác Opportunity. Chỉ bản ghi chưa chốt mới áp được "Trả về hàng đợi" khi người phụ trách chuyển phòng, bị tạm ngưng hay rời workspace (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.13`): bản ghi về hàng đợi hiện tại của nguồn đã sinh ra nó; nguồn không còn thì về đơn vị tiếp nhận mặc định của loại nguồn đó, tìm từ đơn vị bản ghi đang thuộc lên gốc; bản ghi nhập vào hàng đợi một đơn vị (`BR-23.5`) về hàng đợi đơn vị đó. Khách hàng do chính nhân viên tạo hay nhập không qua hàng đợi không có lựa chọn này.
  - **(e) Không chuyển hàng đợi:** phân hệ không khai báo chuyển hàng đợi, vì quy tắc chuyển hàng đợi chỉ dành cho bản ghi công việc (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.14`). Bản ghi chờ phân công đổi đơn vị khi được gán Người phụ trách, hoặc khi Người có toàn quyền đổi đơn vị tiếp nhận của nguồn — khi đó mọi bản ghi chờ phân công chưa gán của nguồn chuyển sang đơn vị mới.
  - **(f) Hồ sơ Khách hàng Tạm** là bản ghi chờ phân công của đơn vị tiếp nhận của kênh hội thoại sinh ra nó, và không vào phân bổ tự động cho tới khi trở thành khách hàng chính thức (`BR-01.1b`).

  **Lý do nghiệp vụ:** Mọi mức truy cập tính theo Người phụ trách; một khách hàng tiềm năng chưa ai phụ trách sẽ không thuộc phạm vi của ai nếu không có đơn vị tiếp nhận, và đội kinh doanh không thấy để nhận. Nhưng một khách hàng sống nhiều năm không được mắc kẹt ở đơn vị tiếp nhận ban đầu sau khi đã có người phụ trách, nên đây là bản ghi chờ phân công chứ không phải bản ghi công việc. Chỉ khách chưa chuyển đổi mới quay lại hàng đợi được, vì khách đã vào thương vụ cần một người nhận bàn giao cụ thể chứ không phải một hàng chờ.

- **`BR-31.10` (Phân bổ tự động là thao tác Gán):** Phân bổ tự động (`BR-31.1` – `BR-31.7`) là thao tác Gán do Hệ thống thực hiện thay **người chịu trách nhiệm quy tắc** — người tạo hoặc sửa quy tắc gần nhất:
  - **(a)** Một quy tắc gắn với hàng đợi của một đơn vị tiếp nhận. Người cấu hình chỉ tạo quy tắc cho hàng đợi mà ô (Khách hàng, Gán) của mình bao phủ đơn vị tiếp nhận, và chỉ chọn người nhận nằm trong phạm vi ô Gán đó — kể cả với quy tắc theo vùng địa lý (`BR-31.2`) và theo ngành nghề (`BR-31.3`).
  - **(b)** Mỗi lượt phân bổ chỉ giao cho người nhận nằm trong **phần giao** của phạm vi ô Gán lúc thiết lập và phạm vi hiện tại của người chịu trách nhiệm quy tắc (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`). Người nhận phải Đang hoạt động, khả dụng (`BR-34.6`) và có ô (Khách hàng, Sửa) khác Không có (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.2`); người không còn thỏa bị bỏ khỏi vòng chia, và quy tắc không còn người nhận nào thì khách nằm lại trong hàng đợi chưa phân công (`BR-31.4`).
  - **(c)** Khi người chịu trách nhiệm quy tắc bị Tạm ngưng hoặc rời workspace, quy tắc tạm dừng — khách mới nằm lại trong hàng đợi chưa phân công — và người phụ trách đơn vị tiếp nhận cùng Người có toàn quyền được thông báo để chuyển người chịu trách nhiệm hoặc tắt quy tắc; quy tắc là một tiến trình chạy thay người đó trong danh sách bước rời workspace (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-43`). Kích hoạt lại người đó không tự chạy lại quy tắc.
  - **(d)** Thu hồi và phân bổ lại khi quá hạn (`BR-31.7`) chịu cùng giới hạn.

  **Lý do nghiệp vụ:** Phân bổ là giao bản ghi cho người khác. Nếu không giới hạn theo ô Gán, người cấu hình quy tắc giao được khách cho những người, ở những đơn vị mà chính họ không được phép giao — tự nới phạm vi qua một tiến trình chạy ngầm. Khi người đặt quy tắc đã bị thu hẹp quyền hoặc nghỉ việc, quy tắc không được tiếp tục giao việc nhân danh họ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-31.1.1` | Nhóm có 3 nhân viên khả dụng, không có quy tắc vùng/ngành khớp | 3 khách hàng tiềm năng mới lần lượt đổ về | Mỗi nhân viên nhận đúng 1 khách |
| `AC-31.1.2` | Một trong 3 nhân viên đang nghỉ phép (không khả dụng) | 2 khách mới đổ về | Hai khách được chia cho 2 nhân viên còn lại; người nghỉ phép không nhận |
| `AC-31.2.1` | Biểu mẫu website có đơn vị tiếp nhận "Kinh doanh" bật "gồm các đơn vị con"; quy tắc vùng địa lý do Quản lý M chịu trách nhiệm, (Khách hàng, Gán) của M = Đơn vị và các đơn vị con tại "Kinh doanh", bao phủ "Kinh doanh – Miền Trung"; có nhân viên Miền Trung đang khả dụng | Khách mới từ biểu mẫu có tỉnh/thành Đà Nẵng | Khách được gán cho nhân viên Miền Trung, không qua chia vòng; khách thuộc Đơn vị chính của người nhận |
| `AC-31.3b.1` | Tenant đặt thứ tự Ngành nghề trước Vùng địa lý | Khách mới khớp cả quy tắc vùng và quy tắc ngành | Khách được gán theo quy tắc ngành |
| `AC-31.4.1` | Biểu mẫu có đơn vị tiếp nhận "Kinh doanh – Hà Nội"; khách mới không có thông tin khớp quy tắc nào và không còn nhân viên khả dụng | Khách đổ về | Khách nằm trong hàng đợi "Chưa phân công" của "Kinh doanh – Hà Nội"; người phụ trách đơn vị nhận thông báo |
| `AC-31.4.2` | Tiếp nối AC-31.4.1; nhân viên K thuộc "Kinh doanh – Hà Nội", (Khách hàng, Xem) = Chỉ của mình, (Khách hàng, Gán) = Chỉ của mình | K mở hàng đợi, bấm "Nhận việc" | K thấy khách dù mức Xem hẹp; sau khi nhận, K là Người phụ trách và khách thuộc Đơn vị chính của K |
| `AC-31.4.3` | Thành viên "Kinh doanh – Hà Nội" có (Khách hàng, Gán) = Không có | Mở hàng đợi | Thấy khách chưa phân công; nút "Nhận việc" bị vô hiệu kèm giải thích |
| `AC-31.5.1` | Nhân viên Kinh doanh | Mở màn hình quy tắc phân bổ | Không sửa được quy tắc |
| `AC-31.9.1` | Người tạo biểu mẫu mới có Đơn vị chính ở chi nhánh Đà Nẵng; mặc định loại nguồn "biểu mẫu website" ghi đè cho chi nhánh là "Kinh doanh – Đà Nẵng" | Tạo biểu mẫu | Đơn vị tiếp nhận "Kinh doanh – Đà Nẵng" được chọn sẵn; chọn đơn vị khác bị vô hiệu kèm giải thích cần Người có toàn quyền |
| `AC-31.9.2` | Biểu mẫu chưa có đơn vị tiếp nhận và không có mặc định nào | Bấm công khai biểu mẫu | Nút công khai bị vô hiệu kèm giải thích cần đặt đơn vị tiếp nhận |
| `AC-31.9.3` | Khách tiềm năng T từ biểu mẫu do H nhận việc, chưa chuyển đổi, đang ở MQL; H bị tạm ngưng | Người thực hiện chọn "Trả về hàng đợi" cho khách hàng của H | T không còn Người phụ trách và hiện trong hàng đợi hiện tại của biểu mẫu đó |
| `AC-31.9.4` | Khách K do H tự tạo bằng tay; H rời workspace | Mở bước bàn giao khách hàng của H | Không có lựa chọn "Trả về hàng đợi" cho K; K phải có người nhận |
| `AC-31.9.5` | Khách T đã được Chuyển đổi Tiềm năng, đang ở Opportunity, đến từ biểu mẫu | Người phụ trách T bị tạm ngưng; mở lựa chọn xử lý bản ghi | Không có "Trả về hàng đợi" cho T; chỉ giữ nguyên hoặc chuyển tạm cho người xử lý thay |
| `AC-31.9.6` | Người có ô Gán bao phủ khách chờ phân công T | Tìm thao tác "Chuyển sang hàng đợi khác" trên T | Không có thao tác này; chỉ có gán Người phụ trách |
| `AC-31.9.7` | Quản trị viên đổi đơn vị tiếp nhận của biểu mẫu từ "Kinh doanh – Hà Nội" sang "Kinh doanh – Miền Bắc"; còn 12 khách chưa gán | Xác nhận | 12 khách chuyển sang hàng đợi "Kinh doanh – Miền Bắc"; màn hình đã hiển thị trước số bản ghi và số người có phạm vi thay đổi |
| `AC-31.10.1` | Quản lý M có (Khách hàng, Gán) = Đơn vị và các đơn vị con tại "Miền Bắc" | M tạo quy tắc vùng địa lý và tìm chọn một nhân viên thuộc "Miền Nam" làm người nhận | Nhân viên "Miền Nam" không có trong danh sách chọn, kèm giải thích ngoài phạm vi ô Gán |
| `AC-31.10.2` | Quy tắc do M thiết lập; sau đó ô Gán của M bị thu hẹp về Đơn vị của mình, loại khỏi phạm vi người nhận N ở đơn vị con | Khách mới khớp quy tắc | Khách không được giao cho N; được giao cho người nhận còn trong phần giao, hoặc nằm lại hàng đợi chưa phân công |
| `AC-31.10.3` | M bị tạm ngưng | Khách mới khớp quy tắc của M | Quy tắc tạm dừng; khách nằm lại hàng đợi chưa phân công; người phụ trách đơn vị tiếp nhận và Người có toàn quyền nhận thông báo |
| `AC-31.10.4` | Người nhận P trong vòng chia có (Khách hàng, Sửa) = Không có | Khách mới đổ về | P không được giao khách nào |
| `AC-31.6.1` | Chị Mai do nhân viên A phụ trách | Một khách mới từ biểu mẫu website có email trùng chị Mai | Không có bản ghi mới; yêu cầu được ghi vào dòng thời gian của chị Mai; A nhận thông báo; một "Yêu cầu chờ xử lý" được tạo |
| `AC-31.6.2` | Tiếp nối AC-31.6.1, A không xử lý quá thời hạn | Quá hạn | Quản lý Kinh doanh nhận leo thang và chỉ định được người xử lý thay; Người phụ trách chính vẫn là A |
| `AC-31.6.3` | A đang khai báo nghỉ phép, người xử lý thay là B | Một yêu cầu mới của khách do A phụ trách đổ về | Yêu cầu chờ xử lý được chuyển ngay cho B |
| `AC-31.7.1` | Ngưỡng mặc định; khách 90 điểm được phân bổ lúc 09:00 Thứ Ba | Quan sát hạn phản hồi | Hạn là 10:00 cùng ngày (1 giờ làm việc) |
| `AC-31.7.2` | Tenant hạ Ngưỡng Ưu tiên cao xuống 70 | Khách 84 điểm được phân bổ | Khách thuộc mức Ưu tiên cao, hạn 1 giờ làm việc — không đồng thời thuộc mức 4 giờ |
| `AC-31.7.3` | Khách Thông thường (hạn 4 giờ) không được liên hệ | Quá 8 giờ làm việc (hai lần thời hạn) | Khách bị thu hồi, phân bổ cho người khác; lịch sử ghi "Quá hạn phản hồi"; Quản lý nhận thông báo |
| `AC-31.7.4` | Khách đã bị thu hồi tự động 2 lần | Lại quá hai lần thời hạn | Khách vào hàng đợi "Chưa phân công", không thu hồi lần thứ ba |
| `AC-31.7b.1` | Lịch mặc định 08:00–17:30, Thứ Hai – Thứ Sáu | Khách Ưu tiên cao được phân bổ lúc 17:00 Thứ Sáu | Hạn phản hồi là 08:30 Thứ Hai tuần sau |
| `AC-31.7b.2` | Chủ sở hữu khai báo Thứ Hai là ngày lễ | Khách Ưu tiên cao được phân bổ lúc 17:00 Thứ Sáu trước đó | Hạn phản hồi là 08:30 Thứ Ba |
| `AC-31.7b.3` | Chủ sở hữu mở cấu hình Lịch làm việc | Bỏ chọn toàn bộ ngày làm việc, hoặc đặt giờ kết thúc trước giờ bắt đầu, lưu | Từ chối, nêu rõ điều kiện hợp lệ của lịch |
| `AC-31.8.1` | Khách mới được phân bổ cho A | A chỉ ghi chú tay "đã gọi, không bắt máy" | Đồng hồ cam kết vẫn chạy; ghi chú không được tính là liên hệ lần đầu |
| `AC-31.8.2` | Khách mới được phân bổ cho A | A gọi qua hệ thống, cuộc gọi kéo dài 2 phút | Bản ghi hoạt động cuộc gọi tự sinh; khách được tính là đã liên hệ lần đầu |
| `AC-31.8.3` | A gặp khách trực tiếp tại sự kiện | A khai báo liên hệ ngoài hệ thống, Quản lý xác nhận | Được tính là liên hệ lần đầu; báo cáo `KPI-03` thống kê lượt này ở cấu phần riêng |
| `AC-31.8.4` | A đã dùng 10 lần bằng chứng nhóm 2 trong tháng | Khai báo lần thứ 11 | Không khai báo được; nêu rõ hạn mức tháng |
| `AC-31.8.5` | Không gian làm việc chưa tích hợp kênh gọi, email hay tin nhắn nào | Khách quá hai lần thời hạn | Không bị thu hồi tự động; người phụ trách được nhắc và khách có trong báo cáo tồn đọng |

---

#### FEAT-32 — Theo dõi Nguồn gốc Khách hàng Tiềm năng

**Mô tả nghiệp vụ:** Tự động ghi nhận nguồn gốc của mỗi khách hàng (kênh quảng cáo, chiến dịch tiếp thị, từ khóa tìm kiếm) để đo hiệu quả đầu tư tiếp thị và tối ưu ngân sách.

**Vai trò sử dụng chính:** Tiến trình Hệ thống (ghi nhận), Nhân viên Marketing (xem báo cáo), Quản lý Marketing (phân tích hiệu quả đầu tư).

**Điều kiện tiên quyết:** Không có cho việc ghi nhận; xem báo cáo cần quyền quản trị Xem báo cáo nguồn gốc (`BR-32.4`).

**Luồng chính:**

1. Khi bản ghi được tạo, hệ thống ghi kênh nguồn gốc và tham số chiến dịch một lần (`BR-32.1` – `BR-32.3`).
2. Người dùng xem trường nguồn gốc trên hồ sơ; người có quyền mở báo cáo phân tích nguồn gốc.
3. Khi có lỗi kỹ thuật diện rộng, Người có toàn quyền sửa nguồn gốc theo lô (`BR-32.3b`).

**Quy tắc nghiệp vụ:**

- **`BR-32.1` (Kênh nguồn gốc):** Mỗi khách hàng ghi nhận **Kênh nguồn gốc chính** (Phụ lục A, A.7, gồm cả giá trị "Không xác định" dùng cho `KPI-08`) và **Chi tiết kênh con**.

  **Lý do nghiệp vụ:** Không biết khách đến từ đâu thì không đo được kênh nào hiệu quả; giá trị "Không xác định" tách riêng để đo được mức thiếu dữ liệu (`KPI-08`) thay vì lẫn vào một kênh có thật.

- **`BR-32.2` (Tham số chiến dịch):** Khi khách được tạo qua biểu mẫu website hoặc tích hợp từ hệ thống ngoài, hệ thống tự động ghi nhận toàn bộ tham số chiến dịch (UTM) đi kèm: nguồn, phương tiện, tên chiến dịch, nội dung và từ khóa.

  **Lý do nghiệp vụ:** Tham số chiến dịch chỉ có giá trị khi được ghi tự động đúng lúc khách đến; nhập tay sau đó thì vừa sai vừa thiếu.

- **`BR-32.3` (Không ghi đè nguồn gốc):** Kênh nguồn gốc và tham số chiến dịch được ghi nhận **một lần** tại thời điểm tạo bản ghi và **không được ghi đè** sau đó bởi bất kỳ nguồn nào — kể cả biểu mẫu gửi lại, nhập khẩu theo chiến lược cập nhật hay gộp bản ghi (`BR-19.5`) — theo nguyên tắc **ghi nhận điểm chạm đầu tiên**. Người dùng nghiệp vụ chỉ xem, không sửa.

  **Lý do nghiệp vụ:** Nếu nguồn gốc bị ghi đè bởi lần tương tác sau, mọi khách hàng đều sẽ "đến từ" kênh cuối cùng họ chạm vào, và ngân sách tiếp thị bị phân bổ sai cho kênh thu hoạch thay vì kênh tạo nhu cầu.

- **`BR-32.3b` (Sửa sai nguồn gốc do lỗi kỹ thuật):** Ngoại lệ duy nhất của `BR-32.3`: khi có **bằng chứng lỗi hệ thống** làm ghi nhận sai nguồn gốc trên diện rộng (biểu mẫu cấu hình sai tham số, một lô nhập khẩu ánh xạ lệch cột nguồn, tích hợp lỗi khiến hàng loạt bản ghi rơi vào "Không xác định"), **Quản trị viên hoặc Chủ sở hữu** được sửa nguồn gốc theo lô, với đủ bốn điều kiện: **(a)** bắt buộc nhập lý do và mô tả bằng chứng lỗi; **(b)** ghi nhật ký (`NFR-07`); **(c)** **giữ nguyên giá trị gốc trong lịch sử bản ghi**; **(d)** báo cáo phân tích nguồn gốc và phân bổ doanh thu theo kênh nêu rõ **số bản ghi đã được sửa nguồn** trong kỳ.

  **Lý do nghiệp vụ:** Không có ngoại lệ này thì cách duy nhất để chữa số liệu sai là xóa và tạo lại bản ghi, làm mất dòng thời gian, điểm tiềm năng và lịch sử giai đoạn — thiệt hại lớn hơn nhiều so với việc sai nguồn.

- **`BR-32.4` (Quyền xem báo cáo nguồn gốc):** Chỉ người giữ quyền quản trị **Xem báo cáo nguồn gốc** (Mục 5.2; mặc định ở Marketing và Quản lý Marketing) và Người có toàn quyền được xem báo cáo phân tích nguồn gốc khách hàng và phân bổ doanh thu theo kênh; người khác chỉ xem trường nguồn gốc trên hồ sơ mà mình xem được.

  **Lý do nghiệp vụ:** Báo cáo phân bổ doanh thu theo kênh cộng gộp dữ liệu của toàn tổ chức, kể cả của các đơn vị người xem không được thấy từng bản ghi; nó chỉ dành cho người chịu trách nhiệm ngân sách tiếp thị.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-32.1.1` | Khách được nhân viên tạo tay, không có thông tin nguồn | Xem hồ sơ | Kênh nguồn gốc là "Không xác định" |
| `AC-32.2.1` | Biểu mẫu website nhận khách từ quảng cáo có tham số chiến dịch "Khuyến mãi Tết" | Khách gửi biểu mẫu | Hồ sơ ghi kênh nguồn gốc và đủ năm tham số chiến dịch |
| `AC-32.3.1` | Tiếp nối AC-32.2.1 | Một tháng sau, khách gửi lại biểu mẫu từ chiến dịch khác | Nguồn gốc và tham số chiến dịch giữ nguyên như lần đầu; lần gửi mới được ghi vào dòng thời gian |
| `AC-32.3.2` | Khách có nguồn gốc "Website" | Nhập khẩu theo chiến lược cập nhật với cột nguồn "Sự kiện" | Nguồn gốc vẫn là "Website" |
| `AC-32.3.3` | Nhân viên Kinh doanh mở hồ sơ | Tìm cách sửa trường nguồn gốc | Trường chỉ đọc |
| `AC-32.3b.1` | Một lô 500 bản ghi rơi vào "Không xác định" do biểu mẫu cấu hình sai | Quản trị viên sửa nguồn gốc theo lô, bỏ trống mô tả bằng chứng | Từ chối, yêu cầu lý do và mô tả bằng chứng |
| `AC-32.3b.2` | Tiếp nối AC-32.3b.1 | Quản trị viên nhập đủ lý do và bằng chứng, xác nhận | 500 bản ghi có nguồn mới; lịch sử mỗi bản ghi còn giá trị gốc; báo cáo nguồn gốc kỳ đó nêu "500 bản ghi đã được sửa nguồn" |
| `AC-32.4.1` | Nhân viên Kinh doanh | Tìm báo cáo phân tích nguồn gốc | Không truy cập được; vẫn xem được trường nguồn gốc trên hồ sơ |
| `AC-32.4.2` | Nhân viên Marketing | Mở báo cáo phân tích nguồn gốc | Xem được |

---

### Nhóm E — Điểm Tiềm năng & Chấm điểm Tự động

#### FEAT-15 — Chấm điểm Tiềm năng Tự động

**Mô tả nghiệp vụ:** Tự động tính điểm tiềm năng (0–100) cho khách hàng dựa trên thuộc tính hồ sơ (**Điểm Hồ sơ**) và hành vi tương tác thực tế (**Điểm Tương tác**).

**Vai trò sử dụng chính:** Tiến trình Hệ thống (tính điểm), Quản lý Marketing (cấu hình), Nhân viên Kinh doanh và Marketing (sử dụng điểm để ưu tiên).

**Điều kiện tiên quyết:** Không có cho việc tính điểm; sửa cấu hình cần quyền quản trị Cấu hình chấm điểm tiềm năng (`BR-15.4`).

**Luồng chính:**

1. Hệ thống tính Điểm Hồ sơ khi hồ sơ thay đổi và cộng Điểm Tương tác khi có hành vi (`BR-15.1` – `BR-15.3`).
2. Khi điểm vượt ngưỡng, hệ thống thăng hạng hoặc gắn nhãn theo `BR-15.5`, kèm các chốt an toàn `BR-15.7`.
3. Người có quyền chỉnh trọng số và ngưỡng; thay đổi áp từ lượt tính kế tiếp (`BR-15.4`).

**Quy tắc nghiệp vụ:**

- **`BR-15.1` (Điểm Hồ sơ):** Với khách loại B2B, mặc định: có email doanh nghiệp hợp lệ +10; có số điện thoại di động +10; có chức danh quản lý cấp cao (Giám đốc, Phó Chủ tịch, lãnh đạo cấp cao) +20; thuộc ngành nghề mục tiêu +15. Khách loại B2C áp bộ tiêu chí riêng, không dùng "email doanh nghiệp" và "chức danh quản lý" (`BR-01.6`).

  **Lý do nghiệp vụ:** Khách có hồ sơ phù hợp tập khách mục tiêu đáng được ưu tiên ngay cả trước khi tương tác; tiêu chí B2B không áp được cho khách cá nhân, nên dùng chung sẽ chấm sai cả một tập khách.

- **`BR-15.2` (Điểm Tương tác):** Mặc định: mở email chiến dịch +5 mỗi lần; nhấp liên kết trong email/tin nhắn +10 mỗi lần; gửi tin nhắn qua trò chuyện trực tuyến hoặc ứng dụng nhắn tin +15; đặt lịch hẹn hoặc tham gia buổi trình diễn sản phẩm +30.

  **Lý do nghiệp vụ:** Hành vi thật thể hiện mức quan tâm thật; mức điểm tăng theo độ cam kết của hành vi — đặt lịch hẹn nói nhiều hơn mở một email.

- **`BR-15.3` (Trần điểm & tần suất cộng điểm):** Điểm tiềm năng bằng Điểm Hồ sơ cộng Điểm Tương tác (sau khi đã áp suy giảm theo `FEAT-16`), chặn trong khoảng 0–100. Mỗi loại hành vi tương tác chỉ được cộng điểm **tối đa một lần mỗi ngày** cho mỗi khách hàng.

  **Lý do nghiệp vụ:** Không giới hạn tần suất thì một khách mở cùng một email mười lần (hoặc một công cụ tự động mở thư) sẽ thành "khách nóng" giả, chiếm chỗ ưu tiên của khách thật.

- **`BR-15.4` (Cấu hình quy tắc chấm điểm):** Các mức điểm tại `BR-15.1`, `BR-15.2` là giá trị mặc định, cấu hình được theo không gian làm việc. Người giữ quyền quản trị **Cấu hình chấm điểm tiềm năng** (Mục 5.2; mặc định ở Quản lý Marketing) và Người có toàn quyền được sửa trọng số, thêm/xóa tiêu chí qua màn hình Cấu hình Quy tắc Chấm điểm — khớp ma trận Mục 5 dòng `FEAT-15`. Người chỉ giữ quyền **Xem cấu hình chấm điểm** (mặc định ở Marketing) chỉ xem, không sửa. Mọi thay đổi được ghi nhật ký và **áp dụng từ lượt tính điểm kế tiếp**, không tính lại điểm đã có.

  **Lý do nghiệp vụ:** Tính lại toàn bộ điểm cũ sau mỗi lần chỉnh trọng số sẽ làm hàng loạt khách đột ngột vượt hoặc rơi khỏi ngưỡng, phát sinh thông báo và thăng hạng hàng loạt không phản ánh hành vi thật nào.

- **`BR-15.5` (Ngưỡng điểm thăng hạng vòng đời):** Điểm tiềm năng gắn với giai đoạn vòng đời qua bộ ngưỡng mặc định (Phụ lục B, `CFG-15-01`):

| Ngưỡng | Điều kiện điểm | Hành vi hệ thống |
| --- | --- | --- |
| **Ngưỡng MQL** | Tổng **≥ 40** **và** Điểm Tương tác **≥ 15** | Khách ở Subscriber, Lead hoặc Nurturing tự động thăng hạng lên MQL; Marketing nhận thông báo. Điều kiện kép là bắt buộc (`BR-15.7`) |
| **Ngưỡng SQL (sẵn sàng chuyển kinh doanh)** | Tổng **≥ 70** | Khách ở MQL được đánh dấu **"Sẵn sàng chuyển Sales"** và đưa vào hàng đợi thẩm định; **không** tự động lên SQL (`BR-15.6`) |
| **Ngưỡng Ưu tiên cao** | Tổng **≥ 85** | Gắn nhãn **"Khách hàng nóng"**, ưu tiên hiển thị đầu hàng đợi phân bổ |
| **Dưới Ngưỡng MQL** | Tổng **< 40** | Không tự động thăng hạng; tiếp tục nuôi dưỡng qua chiến dịch định kỳ |

  Việc thăng hạng tự động tuân thủ ma trận tại `FEAT-12` — ma trận đã cho phép Subscriber → MQL, Lead → MQL và Nurturing → MQL.

  **Lý do nghiệp vụ:** Ngưỡng gắn điểm với hành động cụ thể của đội ngũ: dưới ngưỡng thì Marketing tiếp tục nuôi dưỡng, đạt Ngưỡng SQL thì chuyển kinh doanh thẩm định; không có ngưỡng chung thì mỗi người tự đặt mốc và hai đội tranh cãi về chất lượng khách hàng tiềm năng.

- **`BR-15.6` (Nguyên tắc chuyển giao Marketing → Kinh doanh):** Hệ thống chỉ tự động thăng hạng tối đa đến MQL. Bước MQL → SQL bắt buộc do con người (nhân viên kinh doanh thẩm định) thực hiện. Khách đạt Ngưỡng SQL nhưng chưa được thẩm định trong thời hạn cam kết được xử lý theo `BR-31.7`. Bước lên Opportunity do sự kiện Cơ hội bán hàng (`BR-12.9`) không thuộc phạm vi hạn chế này.

  **Lý do nghiệp vụ:** Bảo đảm nguyên tắc "đội kinh doanh chỉ nhận khách hàng tiềm năng mình đã đồng ý nhận", tránh tranh chấp trách nhiệm giữa Marketing và Kinh doanh khi khách không chuyển đổi.

- **`BR-15.7` (Chống thăng hạng giả từ Điểm Hồ sơ):** Điểm Hồ sơ đạt tối đa 55 điểm mà không cần tương tác nào, nên **điểm tổng đơn thuần không được dùng làm căn cứ thăng hạng**: điều kiện lên MQL bắt buộc kèm **Điểm Tương tác tối thiểu 15** (tương đương ít nhất một hành vi thực). Bổ sung các chốt an toàn (Phụ lục B, `CFG-15-02`):
  - **(a) Hoãn thăng hạng cho dữ liệu nhập khẩu:** bản ghi tạo bằng nhập khẩu hàng loạt không được thăng hạng tự động trong **24 giờ** đầu, và không tính vào cam kết thời gian phản hồi tại `BR-31.7` cho tới khi phát sinh tương tác đầu tiên.
  - **(b) Chống thông báo lặp:** mỗi bản ghi chỉ phát thông báo "đạt Ngưỡng MQL" hoặc "Khách hàng nóng" tối đa **một lần trong 30 ngày**.
  - **(c) Độ trễ đánh giá lại:** bản ghi vừa đạt MQL không bị đánh giá lại ngưỡng trong **7 ngày** kể từ lần thăng hạng, tránh trạng thái nhảy qua lại.

  **Lý do nghiệp vụ:** Một lô nhập 10.000 danh bạ hội thảo không được phép sinh ra hàng nghìn MQL giả và làm tắc hàng đợi thẩm định của đội kinh doanh; điểm dao động quanh ngưỡng do suy giảm rồi cộng lại không được biến thành chuỗi thông báo nhiễu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-15.1.1` | Khách B2B có email doanh nghiệp, số di động, thuộc ngành mục tiêu, chức danh nhân viên | Hệ thống tính điểm | Điểm Hồ sơ là 35 |
| `AC-15.1.2` | Khách B2C có email thuộc dịch vụ thư công cộng | Hệ thống tính điểm | Không áp tiêu chí "email doanh nghiệp" hay "chức danh quản lý"; áp bộ tiêu chí B2C |
| `AC-15.2.1` | Khách có Điểm Tương tác 0 | Khách mở email chiến dịch rồi nhấp liên kết trong email | Điểm Tương tác là 15 |
| `AC-15.3.1` | Khách mở cùng một email 5 lần trong cùng ngày | Hệ thống tính điểm | Chỉ cộng +5 một lần cho loại hành vi "mở email" trong ngày |
| `AC-15.3.2` | Tiếp nối AC-15.3.1 | Hôm sau khách mở email lần nữa | Được cộng +5 |
| `AC-15.3.3` | Khách có tổng 95 điểm | Khách đặt lịch hẹn (+30) | Tổng điểm hiển thị 100, không vượt |
| `AC-15.4.1` | Nhân viên Marketing | Mở Cấu hình Quy tắc Chấm điểm | Xem được cấu hình; không có thao tác sửa |
| `AC-15.4.2` | Quản lý Marketing đổi điểm "mở email" từ +5 thành +3 | Lưu, rồi quan sát một khách đã có điểm và một lượt mở email mới | Điểm cũ của khách không bị tính lại; lượt mở email sau thời điểm đổi được cộng +3; nhật ký ghi thay đổi |
| `AC-15.5.1` | Khách ở Lead, Điểm Hồ sơ 35, Điểm Tương tác 0 | Khách mở email (+5) và nhấp liên kết (+10) | Tổng 50, Điểm Tương tác 15 → tự động lên MQL; Marketing nhận thông báo; lịch sử ghi người thực hiện "Hệ thống" |
| `AC-15.5.2` | Khách ở Lead, Điểm Hồ sơ 35 | Khách chỉ mở email (+5), tổng 40, Điểm Tương tác 5 | **Không** thăng hạng lên MQL |
| `AC-15.5.3` | Khách ở MQL | Khách đặt lịch trình diễn sản phẩm, tổng lên 80 | Khách mang nhãn "Sẵn sàng chuyển Sales", vào hàng đợi thẩm định; giai đoạn vẫn là MQL |
| `AC-15.5.4` | Khách đạt 86 điểm | Mở hàng đợi phân bổ | Khách mang nhãn "Khách hàng nóng" và hiển thị ở đầu hàng đợi |
| `AC-15.6.1` | Khách ở MQL mang nhãn "Sẵn sàng chuyển Sales" | Nhân viên kinh doanh thẩm định và chuyển lên SQL | Chuyển thành công; lịch sử ghi tên nhân viên |
| `AC-15.7.1` | Nhập 10.000 danh bạ hội thảo, 3.000 bản ghi có Điểm Hồ sơ 40, Điểm Tương tác 0 | Hoàn tất nhập | Không bản ghi nào lên MQL |
| `AC-15.7.2` | Bản ghi nhập khẩu lúc 09:00, khách nhấp liên kết email lúc 15:00 cùng ngày đủ điều kiện điểm | Quan sát giai đoạn lúc 15:00 và sau 09:00 hôm sau | Lúc 15:00 chưa thăng hạng; sau mốc 24 giờ, bản ghi được thăng hạng lên MQL |
| `AC-15.7.3` | Bản ghi nhập khẩu chưa có tương tác nào | Quan sát đồng hồ cam kết phản hồi | Bản ghi không có hạn phản hồi cho tới khi phát sinh tương tác đầu tiên |
| `AC-15.7.4` | Khách đã nhận thông báo "đạt Ngưỡng MQL" 10 ngày trước, điểm rơi xuống rồi vượt lại ngưỡng | Hệ thống tính điểm | Không phát thông báo "đạt Ngưỡng MQL" lần thứ hai |

---

#### FEAT-16 — Suy giảm Điểm Tiềm năng theo Thời gian

**Mô tả nghiệp vụ:** Khách hàng không tương tác trong một khoảng thời gian bị tự động giảm Điểm Tương tác để phản ánh độ nguội.

**Vai trò sử dụng chính:** Tiến trình Hệ thống (chạy hằng ngày), Quản lý Kinh doanh (xử lý danh sách đề xuất nuôi dưỡng), Quản lý Marketing (cấu hình mốc và tỷ lệ).

**Điều kiện tiên quyết:** Không có cho tiến trình suy giảm; chuyển khách sang Nurturing hay về Lead cần quyền quản trị Duyệt vòng đời khách hàng (`BR-16.4`, `BR-16.5`).

**Luồng chính:**

1. Tiến trình chạy hằng ngày, giảm Điểm Tương tác của khách không tương tác qua các mốc (`BR-16.1`, `BR-16.2`).
2. Khi tổng điểm rơi dưới Ngưỡng MQL, hệ thống gắn cảnh báo và đưa khách thuộc diện vào danh sách đề xuất (`BR-16.4`).
3. Người có quyền xử lý danh sách đề xuất; khách hoạt động trở lại được đưa về phễu (`BR-16.5`).

**Quy tắc nghiệp vụ:**

- **`BR-16.1` (Mốc suy giảm thứ nhất):** Tiến trình hệ thống chạy lúc **02:00 hằng ngày** theo múi giờ không gian làm việc (Mục 2.3). Nếu khách hàng không có tương tác nào trong **14 ngày**, Điểm Tương tác bị giảm **10%** so với điểm hiện có, làm tròn xuống số nguyên. Mốc 14 ngày chỉ trừ **một lần** cho tới khi chạm mốc thứ hai, không trừ lặp lại mỗi ngày.

  **Lý do nghiệp vụ:** Điểm không giảm theo thời gian thì một khách quan tâm từ năm ngoái vẫn đứng đầu hàng đợi; trừ một lần mỗi mốc để điểm không về 0 chỉ sau vài tuần im lặng.

- **`BR-16.2` (Mốc suy giảm thứ hai):** Nếu không có tương tác trong **30 ngày**, Điểm Tương tác bị giảm tiếp **25%** so với điểm hiện có, làm tròn xuống. Mỗi mốc áp dụng một lần trong mỗi khoảng không tương tác liên tục; khi khách có tương tác mới, việc đếm ngày không tương tác bắt đầu lại từ đầu. Các mốc và tỷ lệ là tham số cấu hình (Phụ lục B, `CFG-16-01`).

  **Lý do nghiệp vụ:** Im lặng một tháng là dấu hiệu nguội rõ hơn hai tuần nên mức giảm lớn hơn; đếm lại từ đầu khi khách tương tác để phản ánh đúng sự quay lại.

- **`BR-16.3` (Sàn điểm):** Điểm tiềm năng không bao giờ nhận giá trị âm; sàn là 0.

  **Lý do nghiệp vụ:** Điểm âm không có nghĩa nghiệp vụ và làm khách mất nhiều tương tác mới mới vượt lại ngưỡng, không phản ánh hành vi hiện tại.

- **`BR-16.4` (Ảnh hưởng đến giai đoạn vòng đời):** Điểm suy giảm **không tự động hạ giai đoạn vòng đời** ở bất kỳ giai đoạn nào. Khi tổng điểm rơi xuống dưới Ngưỡng MQL, hệ thống xử lý theo giai đoạn:
  - **Subscriber, Lead, MQL, SQL:** gắn cảnh báo **"Đã nguội"** trên hồ sơ và đưa vào **danh sách đề xuất chuyển Nurturing**. Việc chuyển sang Nurturing do Quản lý Kinh doanh quyết định, **bắt buộc chọn lý do từ A.16**. Bước chuyển này không thuộc `BR-12.7`, vì Nurturing nằm ngoài phễu tuyến tính.
  - **Customer, Evangelist:** **không** vào danh sách đề xuất (nguyên tắc 4 tại `FEAT-12` cấm bước chuyển đó); điểm nguội chỉ sinh cảnh báo cho Quản lý Khách hàng Hiện hữu để chủ động chăm sóc.
  - **Opportunity:** **không** vào danh sách đề xuất, vì khách đang có Cơ hội mở — điểm tương tác nguội trong lúc thương lượng là bình thường. Điểm nguội chỉ sinh cảnh báo cho Người phụ trách. Đường duy nhất từ Opportunity sang Nurturing là khi toàn bộ Cơ hội Thua, theo `BR-12.3` với lý do từ A.2.

  **Lý do nghiệp vụ:** Điểm số là công cụ ưu tiên hóa, không phải cơ chế tự động loại khách hàng. Một con số giảm vì khách bận vài tuần không được phép rút khách khỏi phễu mà không có quyết định của người hiểu khách.

- **`BR-16.5` (Đường quay lại phễu từ Nurturing):** Hồ sơ ở Nurturing có tương tác trở lại nhưng **chưa đạt Ngưỡng MQL** được Quản lý Kinh doanh trở lên chuyển về Lead, bắt buộc chọn lý do từ A.18. Nếu đạt lại Ngưỡng MQL thì hệ thống tự thăng lên MQL theo `BR-15.5` và quy tắc này không áp dụng.

  **Lý do nghiệp vụ:** Thời gian ở Nurturing không tính vào vận tốc phễu (`BR-13.3`); một hồ sơ đã hoạt động trở lại mà mắc kẹt ngoài phễu sẽ biến mất khỏi mọi báo cáo chuyển đổi. Không dùng A.3 vì A.3 là lý do hạ hạng, trong khi bước này là quay lại phễu với căn cứ ngược hẳn.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-16.1.1` | Khách có Điểm Hồ sơ 20, Điểm Tương tác 40, tương tác gần nhất ngày D | Tiến trình chạy lúc 02:00 ngày D+14 | Điểm Tương tác 36; tổng 56 |
| `AC-16.1.2` | Tiếp nối AC-16.1.1 | Tiến trình chạy các ngày D+15 tới D+29 | Điểm Tương tác giữ nguyên 36 |
| `AC-16.1.3` | Không gian làm việc có múi giờ khác múi giờ máy chủ | Quan sát thời điểm áp suy giảm | Suy giảm được áp lúc 02:00 theo múi giờ không gian làm việc |
| `AC-16.2.1` | Tiếp nối AC-16.1.2 | Tiến trình chạy lúc 02:00 ngày D+30 | Điểm Tương tác 27; tổng 47 |
| `AC-16.2.2` | Tiếp nối AC-16.2.1, khách nhấp liên kết email ngày D+35 | Quan sát các lần chạy tiếp theo | Việc đếm ngày không tương tác bắt đầu lại từ D+35; mốc 14 ngày tiếp theo là D+49 |
| `AC-16.3.1` | Khách có Điểm Tương tác 1 | Áp suy giảm nhiều lần qua các khoảng không tương tác liên tiếp | Điểm Tương tác không nhỏ hơn 0 |
| `AC-16.4.1` | Khách ở MQL, tổng điểm rơi xuống 38 | Tiến trình chạy | Giai đoạn vẫn là MQL; hồ sơ có cảnh báo "Đã nguội"; khách có trong danh sách đề xuất chuyển Nurturing |
| `AC-16.4.2` | Tiếp nối AC-16.4.1 | Quản lý Kinh doanh chuyển sang Nurturing | Bắt buộc chọn lý do từ A.16 |
| `AC-16.4.3` | Tiếp nối AC-16.4.1 | Nhân viên Kinh doanh tìm cách chuyển sang Nurturing từ danh sách đề xuất | Không khả dụng với Nhân viên |
| `AC-16.4.4` | Khách ở Customer, tổng điểm rơi dưới 40 | Tiến trình chạy | Khách không có trong danh sách đề xuất; Quản lý Khách hàng Hiện hữu nhận cảnh báo điểm nguội |
| `AC-16.4.5` | Khách ở Opportunity, tổng điểm rơi dưới 40 | Tiến trình chạy | Khách không có trong danh sách đề xuất; Người phụ trách nhận cảnh báo |
| `AC-16.5.1` | Khách ở Nurturing vừa trả lời email, tổng điểm 30 | Quản lý Kinh doanh chuyển về Lead | Bắt buộc chọn lý do từ A.18; lịch sử ghi nhận |
| `AC-16.5.2` | Khách ở Nurturing | Điểm đạt lại Ngưỡng MQL | Hệ thống tự thăng lên MQL, không cần thao tác của Quản lý |

---

### Nhóm F — Xử lý Trùng lặp & Gộp Bản ghi An toàn

#### FEAT-17 — Nhận diện & Kiểm tra Trùng lặp Khách hàng

**Mô tả nghiệp vụ:** Cảnh báo trùng lặp tức thì khi người dùng đang nhập thông tin, và cung cấp công cụ quét trùng lặp toàn không gian làm việc cho người giữ quyền quản trị Quét trùng lặp toàn workspace (mặc định Người có toàn quyền; doanh nghiệp cấp cho người làm Quản trị Chất lượng Dữ liệu).

**Vai trò sử dụng chính:** Mọi người dùng tạo hoặc sửa khách hàng, Quản trị viên, Quản trị Chất lượng Dữ liệu.

**Điều kiện tiên quyết:** Không có cho cảnh báo khi tạo/sửa; công cụ quét toàn không gian làm việc cần quyền quản trị Quét trùng lặp toàn workspace (Mục 5.2).

**Luồng chính:**

1. Khi người dùng nhập email hoặc số điện thoại, hệ thống so khớp theo hai mức tin cậy (`BR-17.1`) ngay khi rời ô.
2. Hệ thống chặn hoặc cảnh báo theo chính sách (`BR-17.2`), và với bản ghi ngoài phạm vi thì hiển thị thông tin tối thiểu kèm ba hành động (`BR-17.3`).
3. Người có quyền chạy công cụ quét, xem các cụm trùng và tỷ lệ trùng lặp (`BR-17.4`).

**Quy tắc nghiệp vụ:**

- **`BR-17.1` (Tiêu chí trùng lặp & mức độ tin cậy):**
  - **Tiêu chí chắc chắn:** trùng khớp chính xác địa chỉ email, hoặc số điện thoại đã chuẩn hóa (`BR-01.2`).
  - **Tiêu chí tham khảo:** trùng khớp họ tên kết hợp tên công ty (với khách B2C: họ tên kết hợp ngày sinh hoặc địa chỉ — `BR-01.6`). Tiêu chí này có tỷ lệ nhận diện sai cao với dữ liệu tiếng Việt (hai người khác nhau cùng tên tại một doanh nghiệp lớn; "Cty CP ABC" và "ABC Corp" là một công ty nhưng không khớp chữ), nên **tuyệt đối không dùng làm căn cứ gộp tự động** và **không bao giờ dùng để chặn tạo bản ghi**.

  **Lý do nghiệp vụ:** Email và số điện thoại đã chuẩn hóa là định danh gần như duy nhất của một người, nên đủ tin cậy để chặn; họ tên thì không, và dùng nó để chặn hay gộp tự động sẽ nhập hai người khác nhau làm một — sai sót khó đảo ngược nhất của phân hệ.

- **`BR-17.2` (Chính sách xử lý khi phát hiện trùng):** Tham số cấu hình (Phụ lục B, `CFG-17-01`), mặc định chuẩn hệ thống:
  - **Trùng theo Tiêu chí chắc chắn → chặn tạo bản ghi mới.** Hệ thống không cho lưu, thay vào đó dẫn người dùng tới bản ghi hiện hữu để bổ sung thông tin hoặc ghi nhận tương tác mới. Tenant đổi được sang "Chỉ cảnh báo, vẫn cho lưu"; khi đó mỗi lượt bỏ qua cảnh báo được ghi nhật ký.
  - **Trùng theo Tiêu chí tham khảo → chỉ cảnh báo mềm**, kèm danh sách bản ghi nghi trùng; người dùng vẫn lưu được.
  - **Lối ra khi thực sự là hai người khác nhau:** ngay trên màn hình cảnh báo, người tạo được chọn **"Đây là người khác dùng chung định danh này"**. Hệ thống cho tạo bản ghi mới, **tự động gắn nhãn Định danh dùng chung** cho email/số điện thoại đó (`BR-30.2`) và ghi nhật ký người xác nhận. Lối ra này không đòi đổi chính sách của cả tenant.

  **Lý do nghiệp vụ:** Chặn cứng không có lối ra thì các tình huống hằng ngày — hai vợ chồng dùng chung email, hai người cùng số tổng đài, khách cá nhân dùng số của người thân — sẽ không tạo được bản ghi thứ hai, và nhân viên lách bằng cách thêm dấu chấm vào email hoặc bỏ trống email, làm `KPI-01` tệ hơn chính điều quy tắc muốn bảo vệ.

- **`BR-17.2b` (Các nguồn thực tế sinh ra bản ghi trùng):** Chặn tại màn hình tạo mới **không** loại bỏ được bản ghi trùng khỏi hệ thống, vì bản ghi trùng còn đến từ: nhập khẩu hàng loạt theo chiến lược người dùng chọn (`BR-23.3`); tích hợp và các kênh tự động có dữ liệu lệch định dạng; dữ liệu lịch sử tạo trước khi áp chính sách hoặc trong thời gian tenant cấu hình "chỉ cảnh báo"; trùng theo Tiêu chí tham khảo (vốn không bao giờ bị chặn); bản ghi từng được đánh dấu Định danh dùng chung nhưng sau đó xác định lại là cùng một người. Đây là lý do các tính năng gộp bản ghi (`FEAT-18`, `19`, `20`) vẫn cần thiết.

  **Lý do nghiệp vụ:** Nếu coi chặn ở màn hình tạo mới là đủ, doanh nghiệp sẽ bỏ công cụ gộp và khối dữ liệu trùng từ nhập khẩu, tích hợp và lịch sử không bao giờ được dọn, làm `KPI-01` không đạt được.

- **`BR-17.2c` (Cam kết thời gian cho ba loại yêu cầu tại `BR-17.3`):** Ba hành động tại `BR-17.3` dùng chung thời hạn với `BR-31.7` — mặc định **4 giờ làm việc** (Phụ lục B, `CFG-31-01`) — nhưng hành vi khi quá hạn khác nhau:
  - **(i) Yêu cầu quyền truy cập:** quá hạn thì leo thang lên Quản lý Kinh doanh của Người phụ trách; nếu Quản lý cũng không xử lý trong thời hạn thứ hai, hệ thống **tự cấp quyền đọc tạm có ghi nhật ký** theo cơ chế `BR-35.4` (cột (C) của `BR-04.3`). Quyền tự cấp này có hiệu lực **7 ngày** hoặc hết sớm hơn khi Người phụ trách/Quản lý xử lý yêu cầu, tùy điều kiện nào đến trước; hết hạn mà yêu cầu chưa được xử lý thì yêu cầu đóng với lý do **"Không được xử lý"** và người yêu cầu phải tạo yêu cầu mới.
  - **(ii) Yêu cầu nhận bàn giao:** chỉ leo thang, **không** có hành vi tự động nào, vì chuyển giao quyền phụ trách luôn cần quyết định của con người (`FEAT-34`). Thứ tự: Người phụ trách → quản lý trực tiếp của Người phụ trách → người có ô (Khách hàng, Gán) bao phủ cả đơn vị của bản ghi lẫn đơn vị của người yêu cầu (gần nhất trong cây tổ chức) → Người có toàn quyền; ở mỗi bậc, người không đạt `BR-34.1` với người yêu cầu chỉ chuyển tiếp lên bậc kế. Yêu cầu không ai xử lý sau khi bậc cuối cũng quá thời hạn được đóng với lý do **"Không được xử lý"**; người yêu cầu được báo.
  - **(iii) Đề nghị gộp:** leo thang lên Quản trị Chất lượng Dữ liệu hoặc Quản trị viên, **không** có hành vi tự động nào, vì gộp tác động khó đảo ngược lên đồng thuận và giai đoạn của cả hai bản ghi (`BR-19.6`, `BR-19.8`).

  **Lý do nghiệp vụ:** Nếu yêu cầu không có thời hạn và không ai buộc phải trả lời, nhân viên đang có khách trước mặt sẽ bỏ dùng và quay về cách lách tại `BR-17.2`. Quyền tự cấp phải có hạn, nếu không một quyền cấp vì im lặng sẽ tồn tại vĩnh viễn.

- **`BR-17.3` (Hiển thị bản ghi ngoài phạm vi dữ liệu):** Áp dụng cho **cả hai** tình huống: (i) bản ghi trùng được cảnh báo khi tạo/sửa nhưng nằm ngoài phạm vi dữ liệu của người dùng; (ii) người dùng mở trực tiếp một bản ghi ngoài phạm vi (kể cả khi vừa mất quyền đọc tạm theo `BR-35.4` (d)). Hệ thống **không** hiển thị "không có quyền truy cập" mà hiển thị **thông tin tối thiểu để nhận diện**: tên khách hàng dạng viết tắt, tên Người phụ trách, Đơn vị tổ chức phụ trách và thời điểm tương tác gần nhất, kèm **ba hành động**:
  - **"Yêu cầu quyền truy cập"** (xin quyền đọc hoặc quyền sửa) — gửi tới Người phụ trách hiện hữu và quản lý trực tiếp của họ;
  - **"Yêu cầu nhận bàn giao"** — người dùng xin nhận khách về mình phụ trách; khác với đề nghị chuyển do chính Người phụ trách khởi tạo (`BR-34.1b`). Chỉ người có ô (Khách hàng, Gán) đạt `BR-34.1` với **người yêu cầu là người nhận** mới chấp thuận được; bất kỳ ai nhận yêu cầu mà không đạt `BR-34.1` với người yêu cầu — kể cả Người phụ trách hiện hữu chỉ có ô Gán mức Chỉ của mình — không chấp thuận được mà chỉ **chuyển tiếp** lên theo `BR-17.2c` (ii);
  - **"Đề nghị gộp"** — gửi tới **Quản trị Chất lượng Dữ liệu hoặc Quản trị viên**; Người phụ trách hiện hữu chỉ nhận thông báo để biết, không phải người duyệt.

  Mọi hành động tạo ra một bản ghi yêu cầu có vòng đời và cam kết thời gian theo `BR-35.3b`.

  **Bản ghi bị nguồn chặn:** khi bản ghi chịu một nguồn chặn — lượt chặn trên bản ghi, chính sách Từ chối, hoặc là bản ghi giữ chỗ (`BR-23.5`) — với người dùng, hệ thống chỉ hiển thị nhãn trung lập **"Bị hạn chế truy cập"**, không có thông tin nhận diện nào (không tên viết tắt, không Người phụ trách, không đơn vị, không thời điểm tương tác), và chỉ còn hành động **"Đề nghị gộp"** gửi tới Người có toàn quyền (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.4`, `BR-39.6` bước 3). Áp cả khi kiểm tra trùng lặp lúc tạo bản ghi mới khớp với một bản ghi như vậy.

  **Lý do nghiệp vụ:** Người **duy nhất** nhìn thấy trùng lặp trong vận hành là nhân viên đang làm việc với khách, nhưng quyền gộp đòi quyền Xóa mà Nhân viên Kinh doanh không có. Nếu nhân viên nhận cảnh báo trùng mà không biết là ai và không có đường báo, cách lách phổ biến nhất là thêm dấu chấm vào email để lưu cho xong — và `KPI-01` không thể đạt với khối dữ liệu lịch sử tại `BR-17.2b`. Gộp tác động lên đồng thuận và giai đoạn của cả hai bản ghi nên vượt thẩm quyền của một người phụ trách. Nhận bàn giao là một lượt Gán nên chỉ người có ô Gán đủ rộng mới chấp thuận được. Bản ghi bị chặn mà vẫn lộ người phụ trách hay đơn vị thì lượt chặn — vốn đặt vì lý do pháp lý hay hợp đồng — bị đọc vòng qua cảnh báo trùng.

- **`BR-17.4` (Định nghĩa đo Tỷ lệ trùng lặp):** Phục vụ `KPI-01`: tỷ lệ trùng lặp = **số bản ghi dư thừa** chia cho **tổng số bản ghi đang hoạt động** — cùng đơn vị là bản ghi, không phải cặp. Các bản ghi đang hoạt động (chưa xóa mềm) khớp nhau theo Tiêu chí chắc chắn được nhóm thành từng cụm; cụm gồm *n* bản ghi đóng góp *n − 1* bản ghi dư thừa. Loại khỏi phép đo: bản ghi mang nhãn **Định danh dùng chung** (`BR-30.2`) và **Hồ sơ Khách hàng Tạm** (`BR-01.1b`). Công cụ quét trùng lặp toàn không gian làm việc hiển thị kết quả theo đúng các cụm này.

  **Lý do nghiệp vụ:** Đếm theo cặp làm một cụm ba bản ghi trùng thành ba cặp và thổi phồng tỷ lệ; đếm số bản ghi dư thừa phản ánh đúng số bản ghi cần gộp bỏ. Loại định danh dùng chung và hồ sơ tạm vì chúng không phải trùng lặp cần xử lý.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-17.1.1` | Đã có khách hàng với email "mai.tran@vinafoods.vn" | Người dùng đang nhập email đó vào biểu mẫu tạo mới | Cảnh báo trùng xuất hiện ngay khi rời ô email, trước khi bấm lưu |
| `AC-17.1.2` | Đã có "Nguyễn Văn Bình — Vinafoods" | Tạo "Nguyễn Văn Bình — Vinafoods" với email và số khác | Chỉ cảnh báo mềm kèm bản ghi nghi trùng; lưu được |
| `AC-17.2.1` | Chính sách mặc định | Cố lưu bản ghi trùng email với bản ghi hiện hữu | Không lưu được; người dùng được dẫn tới bản ghi hiện hữu |
| `AC-17.2.2` | Tenant cấu hình "Chỉ cảnh báo, vẫn cho lưu" | Bỏ qua cảnh báo và lưu | Lưu được; nhật ký ghi người bỏ qua cảnh báo |
| `AC-17.2.3` | Chính sách mặc định, vợ chồng dùng chung email | Chọn "Đây là người khác dùng chung định danh này" | Bản ghi mới được tạo; email mang nhãn Định danh dùng chung trên cả hai hồ sơ; nhật ký ghi người xác nhận |
| `AC-17.2c.1` | Nhân viên C gửi Yêu cầu quyền truy cập, không ai phản hồi | Quá 4 giờ làm việc | Quản lý Kinh doanh của Người phụ trách nhận leo thang |
| `AC-17.2c.2` | Tiếp nối AC-17.2c.1 | Quá thêm 4 giờ làm việc mà Quản lý không xử lý | C được cấp quyền đọc tạm, thấy hồ sơ ở mức che cột (C); lượt cấp có trong nhật ký |
| `AC-17.2c.3` | Tiếp nối AC-17.2c.2, không ai xử lý yêu cầu | Qua 7 ngày kể từ lúc tự cấp | Quyền đọc tạm hết hiệu lực; yêu cầu đóng với lý do "Không được xử lý" |
| `AC-17.2c.4` | Yêu cầu nhận bàn giao không được phản hồi | Quá hai lần thời hạn | Chỉ có leo thang; Người phụ trách không đổi |
| `AC-17.2c.6` | E ("Kinh doanh 2") yêu cầu nhận khách của A ("Kinh doanh 1"); quản lý trực tiếp của A chỉ có Gán bao phủ "Kinh doanh 1"; Giám đốc G có Gán bao phủ cả "Khối Kinh doanh" | Quản lý của A mở yêu cầu; sau đó không ai xử lý ở bậc G và Người có toàn quyền tới hết thời hạn | Quản lý của A chỉ chuyển tiếp được, yêu cầu tới G; khi bậc cuối quá hạn, yêu cầu đóng với lý do "Không được xử lý" và E được báo; Người phụ trách vẫn là A |
| `AC-17.2c.5` | Đề nghị gộp không được phản hồi | Quá hai lần thời hạn | Chỉ có leo thang tới Quản trị Chất lượng Dữ liệu/Quản trị viên; hai bản ghi không bị gộp |
| `AC-17.3.1` | Bản ghi trùng thuộc phòng khác, ngoài phạm vi người tạo, không chịu nguồn chặn nào | Cảnh báo trùng xuất hiện | Thấy tên viết tắt, tên Người phụ trách, đơn vị phụ trách, thời điểm tương tác gần nhất và các hành động "Yêu cầu quyền truy cập", "Yêu cầu nhận bàn giao", "Đề nghị gộp"; không thấy "không có quyền truy cập" |
| `AC-17.3.3` | Đã có bản ghi giữ chỗ cho N với email x@ (`BR-23.5`) | Một nhân viên tạo khách mới với email x@ | Cảnh báo trùng chỉ hiển thị "Bị hạn chế truy cập", không tên viết tắt, Người phụ trách hay đơn vị; chỉ có "Đề nghị gộp", gửi tới Người có toàn quyền |
| `AC-17.3.4` | Quản trị viên đặt lượt chặn cho nhân viên E trên khách K | E mở đường dẫn tới K | Chỉ thấy nhãn "Bị hạn chế truy cập" và "Đề nghị gộp"; không có "Yêu cầu quyền truy cập" hay "Yêu cầu nhận bàn giao" |
| `AC-17.3.5` | E gửi "Yêu cầu nhận bàn giao" cho khách do A phụ trách; A có (Khách hàng, Gán) = Chỉ của mình | A mở yêu cầu | Không có nút chấp thuận; chỉ có "Chuyển tiếp lên" |
| `AC-17.3.6` | Tiếp nối AC-17.3.5; quản lý Q của A có (Khách hàng, Gán) = Đơn vị và các đơn vị con bao phủ đơn vị của E | Q chấp thuận | E là Người phụ trách; nếu ô Gán của Q không bao phủ đơn vị của E thì nút chấp thuận bị vô hiệu kèm giải thích |
| `AC-17.3.2` | Tiếp nối AC-17.3.1 | Bấm "Đề nghị gộp" | Yêu cầu được gửi tới Quản trị Chất lượng Dữ liệu hoặc Quản trị viên; Người phụ trách hiện hữu chỉ nhận thông báo |
| `AC-17.4.1` | Không gian làm việc có 1.000 bản ghi hoạt động thuộc diện đo, trong đó một cụm 3 bản ghi trùng email và một cụm 2 bản ghi trùng số điện thoại; ngoài ra có 2 bản ghi dùng chung một email đã gắn nhãn Định danh dùng chung (không thuộc diện đo) | Quản trị viên chạy công cụ quét trùng lặp | Kết quả hiển thị 2 cụm; tỷ lệ trùng lặp là 3/1.000 = 0,3% |

---

#### FEAT-18 — Xem trước Tác động Gộp Bản ghi

**Mô tả nghiệp vụ:** Trước khi gộp hai bản ghi, hệ thống hiển thị màn hình Xem trước nêu rõ giá trị nào được giữ, giá trị nào bị thay thế và số lượng bản ghi con (Cơ hội, Vé hỗ trợ, Công việc, Ghi chú) sẽ được chuyển sang Bản ghi Chính.

**Vai trò sử dụng chính:** Người có quyền Xóa trên khách hàng/doanh nghiệp, Quản trị viên, Quản trị Chất lượng Dữ liệu.

**Điều kiện tiên quyết:** Người thực hiện có ô (Khách hàng, Xoá) — hoặc (Doanh nghiệp, Xoá) — bao phủ cả hai bản ghi; không bản ghi nào đang có yêu cầu chủ thể dữ liệu chưa hoàn tất (`BR-19.6`).

**Luồng chính:**

1. Người dùng chọn hai bản ghi và mở Xem trước gộp.
2. Hệ thống hiển thị bảng so sánh, số bản ghi con sẽ chuyển, và các trường bị khóa kèm lý do (`BR-18.1`, `BR-18.2`).
3. Người dùng chọn giá trị cho các trường không bị khóa, chỉ định Doanh nghiệp chính và Người phụ trách, rồi chuyển sang thực thi gộp (`FEAT-19`).

**Quy tắc nghiệp vụ:**

- **`BR-18.1` (So sánh hai cột):** Màn hình Xem trước hiển thị bảng so sánh hai cột của hai bản ghi và số lượng bản ghi con của từng bản ghi sẽ được chuyển giao.

  **Lý do nghiệp vụ:** Gộp là thao tác khó đảo ngược; người thực hiện phải thấy trước quy mô tác động — một khách có 40 cơ hội và một khách có 0 cơ hội cần mức thận trọng khác nhau.

- **`BR-18.2` (Quyền chọn trường và các trường bị cưỡng chế):** Người thực hiện chủ động chọn từng trường muốn giữ từ bản ghi nào — **trừ các trường dưới đây, hệ thống tự quyết định và khóa lựa chọn thủ công**, hiển thị rõ lý do ngay tại màn hình:

| Trường bị cưỡng chế | Quy tắc áp dụng | Quy tắc nguồn |
| --- | --- | --- |
| Trạng thái đồng thuận từng kênh | Từ chối nhận tin luôn thắng; Đồng ý tự khôi phục thắng Đồng ý là căn cứ, trừ khi Đồng ý đó là xác nhận của chính chủ qua điểm đến ghi sau khi biện pháp phòng ngừa bắt đầu; Đồng ý thắng Chưa có đồng thuận, trừ khi Chưa có đồng thuận do một lượt hạ mức mới hơn trên cùng địa chỉ | `BR-19.6` |
| Trạng thái tiếp cận của cùng một địa chỉ | Thứ tự thắng: Không tiếp cận được > Không còn hiệu lực > Chưa kiểm tra > Đã xác thực. "Không tiếp cận được" thắng tất cả (trạng thái kỹ thuật, không đảo được). "Không còn hiệu lực" thắng "Đã xác thực" và "Chưa kiểm tra" vì nó ghi nhận một sự kiện nghiệp vụ đã xảy ra và vẫn đảo lại được thủ công (`BR-10.4` (a)) | `BR-19.6`, `BR-10.4` |
| Nhãn Định danh dùng chung | Có ở bất kỳ bản ghi nào thì được giữ | `BR-19.6` |
| Hạn chế xử lý | Có ở bất kỳ bản ghi nào thì Bản ghi Chính mang Hạn chế xử lý, kèm lượt ghi nhận và nguồn | `BR-19.6` |
| Bằng chứng đồng thuận | Giữ đầy đủ của **cả hai** bản ghi | `BR-19.6` |
| Giai đoạn vòng đời | Giai đoạn tiến xa nhất thắng | `BR-19.8` |
| Nguồn gốc và tham số chiến dịch | Giữ của Bản ghi Chính; của bản ghi phụ được lưu vào sổ cái gộp | `BR-19.5` |
| Điểm tiềm năng | Lấy giá trị cao hơn, không cộng dồn | `BR-19.10` |
| Thẻ phân loại | Hợp nhất toàn bộ | `BR-19.10` |
| Vai trò liên hệ trên Cơ hội | Vai trò đứng trước theo thứ tự tenant đã sắp xếp cho danh mục | `BR-19.4` |

  Người thực hiện **vẫn chỉ định lại được** Doanh nghiệp chính (`BR-19.7`) và Người phụ trách (`BR-19.9`) tại bước Xem trước — hai trường này không bị khóa.

  **Lý do nghiệp vụ:** Các trường bị khóa là những nơi một lựa chọn sai của con người gây vi phạm cam kết đồng thuận, đẩy khách đang trả tiền về chiến dịch săn khách mới, hoặc làm mất bằng chứng pháp lý — không lựa chọn nào trong số đó được phép phụ thuộc vào việc người gộp chọn bản ghi nào làm chính.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-18.1.1` | Bản ghi A có 3 ghi chú, 1 Cơ hội; bản ghi B có 2 công việc, 1 vé hỗ trợ | Mở Xem trước gộp A và B | Thấy bảng so sánh hai cột và số bản ghi con sẽ chuyển: 3 ghi chú, 1 Cơ hội, 2 công việc, 1 vé |
| `AC-18.2.1` | A Từ chối nhận tin qua email, B Đồng ý nhận tin qua email | Xem trường đồng thuận email tại Xem trước | Trường bị khóa, giá trị kết quả là Từ chối nhận tin, kèm lý do hiển thị |
| `AC-18.2.2` | A ở Lead, B ở Customer | Xem trường giai đoạn | Trường bị khóa, kết quả là Customer |
| `AC-18.2.3` | Hai bản ghi có Người phụ trách khác nhau | Chỉ định lại Người phụ trách tại Xem trước | Chỉ định được; kết quả gộp dùng người đã chỉ định |
| `AC-18.2.4` | Hai bản ghi có tên khác nhau | Chọn giữ tên của bản ghi phụ | Chọn được; kết quả gộp dùng tên đã chọn |

---

#### FEAT-19 — Gộp Khách hàng Cá nhân & Doanh nghiệp An toàn

**Mô tả nghiệp vụ:** Thực thi gộp hai bản ghi trùng: giữ Bản ghi Chính, chuyển toàn bộ bản ghi con sang Bản ghi Chính, xóa mềm bản ghi phụ và ghi vào Sổ cái Hoàn tác Gộp.

**Vai trò sử dụng chính:** Người có quyền Xóa trên khách hàng/doanh nghiệp, Quản trị viên, Quản trị Chất lượng Dữ liệu.

**Điều kiện tiên quyết:** Như `FEAT-18`; người thực hiện đã qua bước Xem trước.

**Luồng chính:**

1. Người dùng xác nhận gộp.
2. Hệ thống chuyển toàn bộ bản ghi con sang Bản ghi Chính, áp các trường bị cưỡng chế, xóa mềm bản ghi phụ và ghi Sổ cái Hoàn tác Gộp (`BR-19.2`, `BR-19.3`) — tất cả cùng thành công hoặc cùng thất bại (`NFR-04`).
3. Hệ thống thông báo cho các Người phụ trách liên quan (`BR-19.9`).

**Quy tắc nghiệp vụ:**

- **`BR-19.1` (Quyền hạn bắt buộc):** Thao tác gộp yêu cầu ô (Khách hàng, Xoá) — hoặc (Doanh nghiệp, Xoá) — **bao phủ cả hai bản ghi**, vì bản ghi phụ bị xóa mềm sau khi gộp và Bản ghi Chính bị thay đổi.

  **Lý do nghiệp vụ:** Gộp xóa đi một bản ghi; nếu chỉ cần quyền sửa, người không được xóa vẫn xóa được bất kỳ khách nào bằng cách gộp nó vào một bản ghi khác.

- **`BR-19.2` (Chuyển giao toàn bộ dữ liệu liên quan):** Toàn bộ ghi chú, công việc, vé hỗ trợ, cơ hội bán hàng, lịch sử hội thoại và mối quan hệ của bản ghi phụ được chuyển sang Bản ghi Chính.

  **Lý do nghiệp vụ:** Gộp mà để lại ghi chú, vé hay cơ hội ở bản ghi phụ đã xóa mềm thì các thực thể đó mất khách hàng và sẽ bị dọn dẹp cùng bản ghi phụ.

- **`BR-19.3` (Ghi sổ cái gộp):** Mỗi lần gộp tạo một mục trong Sổ cái Hoàn tác Gộp, ghi: Bản ghi Chính, bản ghi phụ, **ảnh chụp dữ liệu gốc** của bản ghi phụ tại thời điểm gộp, **ảnh chụp trạng thái đồng thuận, Hạn chế xử lý và trạng thái tiếp cận của Bản ghi Chính ngay trước khi gộp** (dùng khi hoàn tác, `BR-20.2`), người thực hiện và thời điểm.

  **Lý do nghiệp vụ:** Không có ảnh chụp dữ liệu gốc thì hoàn tác gộp không biết trả bản ghi phụ về trạng thái nào; không có ảnh chụp trạng thái đồng thuận của Bản ghi Chính thì hoàn tác có thể để lại một Đồng ý mượn của bản ghi kia.

- **`BR-19.4` (Xung đột vai trò liên hệ trên cùng Cơ hội):** Danh mục Vai trò Liên hệ trên Cơ hội (Phụ lục A.4) **do từng Không gian làm việc tự định nghĩa** — không phải danh sách cố định của hệ thống — và dùng chung giữa hồ sơ khách hàng và Cơ hội bán hàng, theo đúng quy định tại [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) (`BR-22.1` của tài liệu đó); tài liệu này không định nghĩa lại danh mục, chỉ đặc tả cách xử lý khi gộp. Khi hai bản ghi cùng tham gia một Cơ hội với vai trò khác nhau, hệ thống giữ vai trò đứng **trước** trong thứ tự mà Quản trị viên đã sắp xếp cho danh mục đó (thứ tự này là một thuộc tính của chính danh mục, không phải một danh sách ưu tiên riêng do phân hệ này định nghĩa).

  **Lý do nghiệp vụ:** Nếu danh mục do tenant tự định nghĩa nhưng thứ tự ưu tiên khi gộp lại cố định trong mã nguồn, một tenant thêm vai trò mới (ví dụ "Người gác cổng ngân sách") sẽ không có vị trí nào trong thứ tự đó — hệ thống buộc phải đoán hoặc bỏ qua vai trò mới thêm. Gắn thứ tự vào chính danh mục (do tenant sắp xếp khi tạo/sửa) giữ được cả hai mục tiêu: tenant tự do định nghĩa vai trò, và gộp bản ghi luôn có kết quả xác định.

- **`BR-19.5` (Bảo toàn nguồn gốc khi gộp):** Nguồn gốc và tham số chiến dịch (`BR-32.1`, `BR-32.2`) của Bản ghi Chính luôn được giữ, **không bị thay thế** bởi dữ liệu của bản ghi phụ. Nguồn gốc của bản ghi phụ được lưu đầy đủ trong ảnh chụp tại sổ cái gộp (`BR-19.3`) để đối chiếu báo cáo phân bổ doanh thu đa nguồn, dù không hiển thị trên hồ sơ Bản ghi Chính.

  **Lý do nghiệp vụ:** Nguồn gốc là điểm chạm đầu tiên (`BR-32.3`); để bản ghi phụ ghi đè sẽ làm báo cáo phân bổ ngân sách đổi theo lựa chọn ngẫu nhiên bản ghi nào làm chính.

- **`BR-19.6` (Trạng thái nghiêm ngặt nhất cho đồng thuận) — sàn bắt buộc, nghĩa vụ pháp lý:** Với mỗi kênh, trạng thái đồng thuận của Bản ghi Chính sau khi gộp được xác định như sau, bất kể bản ghi nào được chọn làm chính và bất kể người dùng chọn gì ở bước Xem trước; quy tắc **không thể ghi đè thủ công**:
  - **Từ chối nhận tin** ở bất kỳ bản ghi nào thì Bản ghi Chính nhận Từ chối nhận tin. Hai trạng thái được coi là **đối lập** khi một bên là Từ chối nhận tin.
  - Nếu không có Từ chối: một bên **Đồng ý tự khôi phục** (chưa là căn cứ, `BR-30.10`) và bên kia Đồng ý là căn cứ thì Bản ghi Chính mang Đồng ý tự khôi phục — trừ khi bằng chứng Đồng ý là căn cứ được ghi **sau** thời điểm biện pháp phòng ngừa bắt đầu **và** là xác nhận của chính chủ qua chính điểm đến (`BR-30.3` (e)), vì chỉ khi đó mới là hành vi mới của chính chủ; bằng chứng do nhân viên ghi hay do nhập khẩu không gỡ được dấu "tự khôi phục" qua gộp (`BR-30.10`).
  - Nếu không có Từ chối: một bên **Chưa có đồng thuận** và bên kia **Đồng ý** (là căn cứ hay tự khôi phục) thì Bản ghi Chính nhận **Đồng ý** kèm bằng chứng của bên đó, giữ nguyên dấu "tự khôi phục" nếu có — vì hai bản ghi là cùng một người và Chưa có đồng thuận chỉ nghĩa là chưa ai hỏi ý kiến; **trừ** khi Chưa có đồng thuận là kết quả của một lượt hạ mức (ví dụ số điện thoại đổi chủ, `BR-30.10`) trên **cùng địa chỉ** với bằng chứng Đồng ý kia và ghi sau bằng chứng đó, thì Bản ghi Chính nhận Chưa có đồng thuận; lượt hạ mức trên một địa chỉ khác chỉ áp cho địa chỉ đó.

  Tương tự:
  - Trạng thái tiếp cận của cùng một địa chỉ theo thứ tự thắng tại bảng của `BR-18.2`: Không tiếp cận được > Không còn hiệu lực > Chưa kiểm tra > Đã xác thực.
  - Nhãn Định danh dùng chung (`BR-30.2`) có ở bất kỳ bản ghi nào thì được giữ.
  - Hạn chế xử lý (`BR-30.6`) có ở bất kỳ bản ghi nào thì Bản ghi Chính mang Hạn chế xử lý, kèm lượt ghi nhận và nguồn gốc của nó.
  - Bằng chứng đồng thuận (`BR-30.3`) của **cả hai** bản ghi được giữ đầy đủ, không ghi đè. Việc gộp **không tạo** lượt ghi nhận đồng thuận mới: trạng thái sau gộp dẫn chiếu bằng chứng gốc của bản ghi quyết định trạng thái đó, nên không bị coi là một lượt "trong thời gian gộp" khi hoàn tác (`BR-20.2`).
  - Nếu **bất kỳ bản ghi nào trong cặp gộp** đang có yêu cầu quyền chủ thể dữ liệu chưa hoàn tất (`FEAT-33`), thao tác gộp **bị chặn** cho đến khi yêu cầu được xử lý xong.

  **Lý do nghiệp vụ:** Gửi tin cho người đã từ chối là vi phạm cam kết đồng thuận, và gộp là thao tác dễ vô tình "hồi sinh" một đồng ý cũ nhất. Bằng chứng của cả hai bản ghi là căn cứ chứng minh cơ sở xử lý dữ liệu khi bị khiếu nại. Gộp trong lúc đang xử lý yêu cầu chủ thể dữ liệu làm mất khả năng xác định yêu cầu áp dụng trên phần dữ liệu nào.

- **`BR-19.7` (Xung đột liên kết doanh nghiệp khi gộp):**
  - **Cùng một doanh nghiệp, chức danh khác nhau:** giữ **một** liên kết, ưu tiên chức danh của liên kết có ngày bắt đầu **muộn hơn** (phản ánh vị trí hiện tại); chức danh cũ lưu vào lịch sử liên kết.
  - **Hai Doanh nghiệp chính khác nhau:** Doanh nghiệp chính của Bản ghi Chính được giữ; liên kết của bản ghi phụ trở thành liên kết phụ. Người thực hiện chỉ định lại được tại bước Xem trước.
  - **Liên kết đã kết thúc (Đã nghỉ việc):** chuyển giao nguyên trạng, không tự kích hoạt lại.

  **Lý do nghiệp vụ:** Một người không thể có hai chức danh cùng lúc ở cùng một công ty; giữ chức danh mới nhất phản ánh vị trí hiện tại, còn chức danh cũ vẫn cần cho lịch sử quan hệ.

- **`BR-19.8` (Nguyên tắc giai đoạn tiến xa nhất) — sàn bắt buộc:** Sau khi gộp, Bản ghi Chính **luôn nhận giai đoạn tiến xa nhất** của hai bản ghi, bất kể bản ghi nào được chọn làm chính và bất kể lựa chọn ở bước Xem trước; không thể ghi đè thủ công, và thuộc `BR-12.6` (ii). Thứ tự so sánh khi có trạng thái ngoài phễu tuyến tính:
  - **Một** bản ghi ở giai đoạn tuyến tính, bản ghi kia ở trạng thái đặc biệt: lấy **giai đoạn tuyến tính**, trừ khi trạng thái đặc biệt là Churned — khi đó lấy Churned và thông báo Người phụ trách rà soát, vì Churned là thông tin giá trị hơn về thực trạng quan hệ.
  - **Cả hai** ở trạng thái đặc biệt: Churned > Nurturing > Disqualified.
  - Disqualified **không bao giờ** được giữ nếu bản ghi kia ở bất kỳ giai đoạn tuyến tính nào hoặc ở Churned/Nurturing; lý do loại được lưu vào lịch sử để rà soát.

  **Lý do nghiệp vụ:** Nếu Bản ghi Chính là Lead và bản ghi phụ là Customer, giữ Lead sẽ đẩy một khách đang trả tiền trở lại chiến dịch săn khách mới và làm sai báo cáo doanh thu. Một bản ghi từng bị loại không được làm mất trạng thái đang hoạt động của bản ghi kia.

- **`BR-19.9` (Người phụ trách sau khi gộp):** Khi hai bản ghi thuộc hai Người phụ trách khác nhau, Bản ghi Chính mặc định giữ **Người phụ trách của bản ghi có tương tác gần nhất** (Phụ lục B, `CFG-19-01`; các lựa chọn khác: giữ người phụ trách của Bản ghi Chính, hoặc bắt buộc chỉ định thủ công). Người thực hiện chỉ định lại được tại bước Xem trước. Hệ thống **bắt buộc thông báo cho cả hai Người phụ trách** và ghi nhật ký.

  **Lý do nghiệp vụ:** Người mất bản ghi cũng mất quyền truy cập theo `BR-01.4`, ảnh hưởng trực tiếp tới ghi nhận thành tích và hoa hồng; họ phải được biết để khiếu nại nếu cần.

- **`BR-19.10` (Hợp nhất điểm, thẻ và kênh liên lạc):** Điểm tiềm năng sau gộp lấy **giá trị cao hơn** (không cộng dồn); thẻ phân loại được **hợp nhất** (loại trùng); toàn bộ kênh liên lạc hợp lệ của cả hai bản ghi được giữ thành danh sách kênh của Bản ghi Chính, người dùng chỉ định một kênh chính cho mỗi loại.

  **Khi kết quả vượt giới hạn gói dịch vụ (`NFR-11`):** thao tác gộp **không bị chặn** — dữ liệu khách hàng không được mất vì giới hạn thương mại. Bản ghi sau gộp được phép vượt giới hạn và gắn cờ **"Vượt hạn mức do gộp"**; Chủ sở hữu nhận thông báo kèm đề nghị rà soát hoặc nâng gói; bản ghi **không** được thêm thẻ/liên kết mới cho tới khi trở về trong giới hạn. Áp dụng cả cho liên kết doanh nghiệp tại `BR-19.7`.

  **Lý do nghiệp vụ:** Cộng dồn điểm sẽ cho phép thổi điểm bằng cách tạo bản ghi trùng rồi gộp.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-19.1.1` | Nhân viên Kinh doanh không có quyền Xóa | Tìm hành động gộp | Không khả dụng |
| `AC-19.2.1` | Bản ghi phụ B có 2 ghi chú, 1 công việc, 1 hội thoại | Gộp B vào A | Cả 4 mục xuất hiện trên hồ sơ A; B vào Thùng rác |
| `AC-19.3.1` | Tiếp nối AC-19.2.1 | Mở lịch sử gộp của A | Có một mục sổ cái ghi A, B, người gộp, thời điểm |
| `AC-19.4.1` | A là "Người ảnh hưởng", B là "Người ra quyết định" trên cùng một Cơ hội | Gộp | Liên hệ sau gộp mang vai trò "Người ra quyết định" trên Cơ hội đó |
| `AC-19.5.1` | A có nguồn "Website", B có nguồn "Sự kiện", A là Bản ghi Chính | Gộp | Hồ sơ A giữ nguồn "Website"; nguồn "Sự kiện" có trong ảnh chụp sổ cái |
| `AC-19.6.1` | A Từ chối nhận tin qua email; B Đồng ý nhận tin qua email, B là Bản ghi Chính, người gộp chọn giữ giá trị của B | Gộp | Kênh email của bản ghi sau gộp là Từ chối nhận tin; thông báo giải thích lý do; bằng chứng đồng thuận của cả hai còn nguyên |
| `AC-19.6.2` | B đang có yêu cầu xóa dữ liệu theo quyền chủ thể dữ liệu chưa hoàn tất | Bấm gộp A và B | Từ chối, nêu rõ phải xử lý xong yêu cầu trước |
| `AC-19.6.3` | Bản ghi A có email Đồng ý tự khôi phục; bản ghi B có email Đồng ý là căn cứ với bằng chứng ghi trước khi biện pháp phòng ngừa của A bắt đầu; B được chọn làm Bản ghi Chính và người gộp chọn giá trị của B | Gộp | Email của Bản ghi Chính là Đồng ý tự khôi phục, cần chính chủ xác nhận lại |
| `AC-19.6.4` | Như trên, nhưng bằng chứng Đồng ý của B là xác nhận của chính chủ qua liên kết gửi tới đúng email đó, ghi sau khi biện pháp phòng ngừa của A bắt đầu | Gộp | Email của Bản ghi Chính là Đồng ý là căn cứ |
| `AC-19.6.5` | Bản ghi A (nhập khẩu) có email Chưa có đồng thuận; bản ghi B (đăng ký qua biểu mẫu) có email Đồng ý có bằng chứng | Gộp | Email của Bản ghi Chính là Đồng ý kèm bằng chứng của B |
| `AC-19.6.6` | Bản ghi A có SMS Đồng ý trên số 090x (bằng chứng tháng 1); bản ghi B có cùng số 090x ở Chưa có đồng thuận do nhà mạng báo số đổi chủ tháng 3 | Gộp | SMS trên số 090x của Bản ghi Chính là Chưa có đồng thuận |
| `AC-19.6.7` | A có email Đồng ý tự khôi phục; B là bản ghi nhập khẩu "Tạo bản ghi mới dù trùng" có email Đồng ý kèm phiếu, ghi sau khi biện pháp của A bắt đầu | Gộp | Email của Bản ghi Chính vẫn là Đồng ý tự khôi phục (bằng chứng của B không phải xác nhận của chính chủ qua điểm đến) |
| `AC-19.6.8` | A đang Hạn chế xử lý (yêu cầu đã hoàn tất); B không | Gộp, chọn B làm Bản ghi Chính | Bản ghi Chính mang Hạn chế xử lý với lượt ghi nhận và nguồn của A |
| `AC-19.6.9` | A có email Chưa có đồng thuận; B có email Đồng ý tự khôi phục | Gộp | Email của Bản ghi Chính là Đồng ý mang dấu "tự khôi phục" |
| `AC-19.6.10` | Bản ghi A có SMS Đồng ý trên số 091y; bản ghi B có số 090x ở Chưa có đồng thuận do đổi chủ ghi sau | Gộp | Số 091y giữ Đồng ý; lượt đổi chủ chỉ áp cho số 090x |
| `AC-19.6.11` | Email x@ của A ở Không tiếp cận được; cùng email x@ trên B ở Đã xác thực | Gộp | Email x@ của Bản ghi Chính ở Không tiếp cận được |
| `AC-19.6.12` | Số điện thoại của A mang nhãn Định danh dùng chung; B không | Gộp, chọn B làm Bản ghi Chính | Số đó trên Bản ghi Chính vẫn mang nhãn Định danh dùng chung |
| `AC-19.7.1` | A và B cùng liên kết với Vinafoods, chức danh "Trưởng phòng" (bắt đầu 2022) và "Giám đốc" (bắt đầu 2025) | Gộp | Còn một liên kết với Vinafoods, chức danh "Giám đốc"; "Trưởng phòng" có trong lịch sử liên kết |
| `AC-19.8.1` | A (Bản ghi Chính) ở Lead, B ở Customer, người gộp chọn giữ Lead | Gộp | Bản ghi sau gộp ở Customer; thông báo giải thích lựa chọn thủ công đã bị ghi đè |
| `AC-19.8.2` | A ở MQL, B ở Churned | Gộp | Bản ghi sau gộp ở Churned; Người phụ trách nhận thông báo rà soát |
| `AC-19.8.3` | A ở Nurturing, B ở Disqualified | Gộp | Bản ghi sau gộp ở Nurturing; lý do loại của B có trong lịch sử |
| `AC-19.9.1` | A do nhân viên X phụ trách (tương tác gần nhất 1 tháng trước), B do Y phụ trách (tương tác gần nhất hôm qua) | Gộp với cấu hình mặc định | Người phụ trách sau gộp là Y; cả X và Y nhận thông báo |
| `AC-19.10.1` | A có 60 điểm, B có 45 điểm; A có thẻ "VIP", B có thẻ "VIP" và "Hội thảo" | Gộp | Điểm là 60; thẻ gồm "VIP" và "Hội thảo" |
| `AC-19.10.2` | Gói tiêu chuẩn; A có 30 thẻ, B có 30 thẻ khác nhau | Gộp | Gộp thành công với 60 thẻ, bản ghi mang cờ "Vượt hạn mức do gộp"; Chủ sở hữu nhận thông báo; gắn thêm thẻ mới bị từ chối |

---

#### FEAT-20 — Hoàn tác Gộp Bản ghi theo Sổ cái

**Mô tả nghiệp vụ:** Khôi phục trạng thái trước khi gộp khi phát hiện gộp nhầm.

**Vai trò sử dụng chính:** Quản trị viên, Chủ sở hữu.

**Điều kiện tiên quyết:** Người thực hiện giữ quyền quản trị Hoàn tác gộp (Mục 5.2; mặc định Người có toàn quyền); mục sổ cái còn trong thời hạn hoàn tác và không bản ghi nào đang chịu biện pháp phòng ngừa (`BR-33.7` (f)).

**Luồng chính:**

1. Người dùng mở Lịch sử gộp trên hồ sơ Bản ghi Chính, chọn mục sổ cái và bấm "Hoàn tác gộp" (`BR-20.1`).
2. Hệ thống khôi phục bản ghi phụ, trả bản ghi con về đúng chủ cũ và tính lại trạng thái đồng thuận của cả hai bản ghi theo `BR-20.2`.
3. Hệ thống ghi nhật ký và thông báo cho các Người phụ trách liên quan.

**Quy tắc nghiệp vụ:**

- **`BR-20.1` (Điểm thao tác):** Người giữ quyền quản trị **Hoàn tác gộp** (Mục 5.2) mở **Lịch sử gộp** trên hồ sơ Bản ghi Chính và bấm **"Hoàn tác gộp"** trên mục sổ cái tương ứng.

  **Lý do nghiệp vụ:** Hoàn tác gộp tác động lên hai hồ sơ và trạng thái đồng thuận của cả hai; nó phải xuất phát từ đúng mục sổ cái để không hoàn tác nhầm lần gộp khác, và chỉ do người có thẩm quyền quản trị dữ liệu thực hiện.

- **`BR-20.2` (Nội dung khôi phục):** Hệ thống khôi phục bản ghi phụ đã bị xóa mềm, trả lại các trường dữ liệu theo ảnh chụp trong sổ cái và trả các bản ghi con về đúng bản ghi sở hữu ban đầu. **Ngoại lệ bắt buộc cho đồng thuận, hạn chế xử lý và trạng thái tiếp cận:** với mỗi kênh của bản ghi được khôi phục, hệ thống bắt đầu từ trạng thái trong ảnh chụp, rồi áp **theo thứ tự thời gian** mọi lượt ghi nhận trên Bản ghi Chính trong thời gian gộp — lượt đồng thuận ghi ở cấp kênh áp cho kênh đó của bản ghi được khôi phục; lượt gắn với một địa chỉ cụ thể (số điện thoại đổi chủ, trạng thái tiếp cận) chỉ áp cho đúng địa chỉ đó — lượt đồng thuận (kể cả lượt hạ về Chưa có đồng thuận do đổi chủ số và dấu "tự khôi phục"), Hạn chế xử lý, và trạng thái tiếp cận Không còn hiệu lực, Không tiếp cận được — nhưng chỉ nhận những lượt làm trạng thái **chặt hơn** theo thứ tự hoàn tác **Từ chối nhận tin > Chưa có đồng thuận > Đồng ý tự khôi phục > Đồng ý là căn cứ**, cộng với các lượt **nới mức là hành vi của chính chủ** qua đúng điểm đến (tự xác nhận Đồng ý, tự đăng ký lại, xác nhận lại để gỡ dấu "tự khôi phục" — `BR-30.3` (e), `BR-30.10`) được ghi trên đúng điểm đến vốn thuộc bản ghi đó; ngoài các lượt ấy, kết quả không bao giờ nâng mức so với ảnh chụp. Bỏ qua các lượt có nguồn "Yêu cầu chưa xác minh được danh tính" của một biện pháp phòng ngừa đã kết thúc (`BR-33.7`) — hạ mức và Hạn chế xử lý đó không phải ý muốn của chủ thể; mọi lượt "Tự khôi phục khi dỡ biện pháp phòng ngừa" cũng bị bỏ qua, trừ dấu "tự khôi phục" của biện pháp hết hạn — dấu này được áp; lượt ghi nhận mới tạo khi khách xác minh thành công (`BR-33.7` (e)) vẫn được áp. **Bản ghi Chính** cũng được tính lại theo cùng cách, bắt đầu từ ảnh chụp trạng thái của chính nó ngay trước khi gộp (`BR-19.3`): mọi Đồng ý mà nó có được chỉ nhờ bằng chứng của bản ghi phụ (`BR-19.6`) bị bỏ, còn các lượt làm chặt hơn và các lượt nới mức là hành vi của chính chủ nêu trên vẫn được áp. Các điểm đến vốn thuộc bản ghi phụ (đã chuyển sang Bản ghi Chính theo `BR-19.10`) rời Bản ghi Chính và trở về bản ghi phụ; mọi lượt ghi nhận trên chúng trong thời gian gộp đi theo làm lịch sử và bằng chứng, còn trạng thái vẫn tính theo đúng cách trên. Điểm đến được thêm mới trong thời gian gộp ở lại Bản ghi Chính; lượt nới mức của chính chủ trên điểm đến đó chỉ tính cho Bản ghi Chính. Hạn chế xử lý đã được dỡ trong thời gian gộp theo đúng một nhánh dỡ hợp lệ của `BR-30.6` (chính chủ rút yêu cầu qua kênh đã xác minh) được coi là lượt nới mức của chính chủ và chỉ được áp cho bản ghi mà kênh đã xác minh đó vốn thuộc về. Thứ tự chặt của trạng thái tiếp cận theo bảng của `BR-18.2`. Hoàn tác gộp không bao giờ khôi phục một Đồng ý mà khách đã rút trong thời gian hai bản ghi là một, và không bao giờ biến một Đồng ý chưa được chính chủ xác nhận lại thành căn cứ (`BR-30.10`).

  **Lý do nghiệp vụ:** Hoàn tác gộp phải trả lại đúng hai người như trước, nhưng không được xóa những gì khách đã nói trong thời gian hai bản ghi là một: lời từ chối đã đưa ra phải được giữ, và một đồng ý chỉ có nhờ bản ghi kia phải bị bỏ.

- **`BR-20.3` (Thời hạn hoàn tác & bảo vệ khỏi dọn dẹp):** Hoàn tác gộp có hiệu lực trong **90 ngày** kể từ thời điểm gộp (Phụ lục B, `CFG-20-01`), **cộng thêm** mọi khoảng thời gian hoàn tác bị chặn vì một trong hai bản ghi đang chịu biện pháp phòng ngừa (`BR-33.7` (f)). Trong thời hạn này, bản ghi phụ **được loại khỏi dọn dẹp vĩnh viễn** của `BR-05.4` (xem `BR-05.6` (b)). Quá thời hạn, hành động "Hoàn tác gộp" không còn hiển thị và bản ghi phụ mới vào diện dọn dẹp; sổ cái gộp vẫn được lưu vĩnh viễn (`NFR-05`).

  **Lý do nghiệp vụ:** Hoàn tác gộp là cơ chế an toàn cốt lõi của phân hệ. Không có quy tắc này, một lần gộp sai phát hiện ở tháng thứ hai sẽ khôi phục về một bản ghi rỗng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-20.1.1` | Quản lý Kinh doanh có quyền Xóa | Mở Lịch sử gộp | Thấy lịch sử; không có hành động "Hoàn tác gộp" |
| `AC-20.2.1` | B đã được gộp vào A cách đây 45 ngày; 2 công việc của B đã chuyển sang A | Quản trị viên bấm "Hoàn tác gộp" | B trở lại hoạt động với dữ liệu theo ảnh chụp; 2 công việc trở về B |
| `AC-20.2.2` | Trong thời gian gộp, Bản ghi Chính chịu một biện pháp phòng ngừa rồi biện pháp hết hạn; ảnh chụp của bản ghi phụ có email Đồng ý | Quản trị viên hoàn tác gộp | Không chép Từ chối hay Hạn chế xử lý của biện pháp đã kết thúc; email của bản ghi phụ được khôi phục là Đồng ý mang dấu "tự khôi phục" như trên Bản ghi Chính, chưa là căn cứ cho tới khi chính chủ xác nhận lại |
| `AC-20.2.3` | Trong thời gian gộp, khách xác minh thành công yêu cầu rút đồng thuận email và Hạn chế xử lý đưa ra khi chưa xác minh; ảnh chụp của bản ghi phụ có email Đồng ý | Quản trị viên hoàn tác gộp | Cả hai bản ghi giữ email Từ chối nhận tin và Hạn chế xử lý (nguồn "Yêu cầu trực tiếp của khách hàng") |
| `AC-20.2.4` | Bản ghi phụ có ảnh chụp email Chưa có đồng thuận; Bản ghi Chính hiện mang email Đồng ý tự khôi phục | Hoàn tác gộp | Bản ghi phụ giữ Chưa có đồng thuận (không bị nâng lên Đồng ý) |
| `AC-20.2.5` | Ảnh chụp của bản ghi phụ có SMS Đồng ý trên số 090x; trong thời gian gộp nhà mạng báo chính số 090x đổi chủ, Bản ghi Chính ghi SMS của số đó Chưa có đồng thuận và Không còn hiệu lực | Hoàn tác gộp | Bản ghi phụ có SMS Chưa có đồng thuận, trạng thái tiếp cận Không còn hiệu lực; không gửi tiếp thị tới chủ mới của số |
| `AC-20.2.6` | A (Bản ghi Chính) có email Chưa có đồng thuận; B có email Đồng ý có bằng chứng; sau gộp A mang Đồng ý kèm bằng chứng của B | Hoàn tác gộp vì gộp nhầm hai người | A về Chưa có đồng thuận; B giữ Đồng ý của mình |
| `AC-20.2.7` | Trong thời gian gộp, số 091y riêng của Bản ghi Chính đổi chủ; bản ghi phụ có số 090x khác | Hoàn tác gộp | Số 090x của bản ghi phụ giữ nguyên trạng thái trong ảnh chụp |
| `AC-20.2.8` | Trong thời gian gộp, biện pháp phòng ngừa trên Bản ghi Chính được dỡ sớm vì xác định mạo danh; ảnh chụp của bản ghi phụ có email Đồng ý là căn cứ | Hoàn tác gộp | Email của bản ghi phụ là Đồng ý là căn cứ, không mang dấu "tự khôi phục" |
| `AC-20.2.9` | A (Bản ghi Chính) có email Chưa có đồng thuận trước khi gộp; trong thời gian gộp, chính chủ bấm liên kết xác nhận gửi tới đúng email của A | Hoàn tác gộp | A giữ email Đồng ý là căn cứ theo lượt xác nhận của chính chủ |
| `AC-20.2.10` | Trong thời gian gộp, Nhân viên Hỗ trợ ghi khách từ chối kênh email trên Bản ghi Chính | Hoàn tác gộp | Kênh email của cả hai bản ghi là Từ chối nhận tin |
| `AC-20.2.11` | Bản ghi phụ B có số 090x ở trạng thái tiếp cận Không còn hiệu lực trong ảnh chụp; khi gộp, số 090x chuyển sang Bản ghi Chính A; trong thời gian gộp, nhân viên gỡ thủ công Không còn hiệu lực của số 090x | Hoàn tác gộp | Số 090x rời A và trở về B cùng lịch sử các lượt ghi nhận trên số đó; số 090x ở trạng thái Không còn hiệu lực (lượt nhân viên gỡ không được áp) |
| `AC-20.2.12` | Ảnh chụp của bản ghi phụ B có email Từ chối nhận tin; trong thời gian gộp, chính chủ đăng ký lại và xác nhận qua liên kết gửi tới đúng email vốn của B | Hoàn tác gộp | Email của B là Đồng ý là căn cứ |
| `AC-20.2.13` | Trong thời gian gộp, chính chủ xác nhận Đồng ý qua email vốn của Bản ghi Chính A; ảnh chụp của bản ghi phụ B có email Chưa có đồng thuận | Hoàn tác gộp | Email của B vẫn là Chưa có đồng thuận (lượt xác nhận ghi trên điểm đến của A) |
| `AC-20.2.14` | Ảnh chụp của bản ghi phụ có email Đồng ý tự khôi phục; trong thời gian gộp, chính chủ xác nhận lại qua email đó | Hoàn tác gộp | Email của bản ghi phụ là Đồng ý là căn cứ, không mang dấu "tự khôi phục" |
| `AC-20.2.15` | Ảnh chụp của bản ghi phụ có email Chưa có đồng thuận; trong thời gian gộp, nhân viên ghi Đồng ý kèm phiếu cho kênh email của Bản ghi Chính | Hoàn tác gộp | Email của bản ghi phụ vẫn là Chưa có đồng thuận; Bản ghi Chính trở về trạng thái trong ảnh chụp của nó, và kết quả hoàn tác nhắc nhân viên ghi lại phiếu đồng ý nếu phiếu thuộc về Bản ghi Chính |
| `AC-20.2.16` | Trong thời gian gộp, khách xác minh thành công yêu cầu chỉ rút đồng thuận SMS; ảnh chụp của bản ghi phụ có email Đồng ý là căn cứ | Hoàn tác gộp | Email của bản ghi phụ là Đồng ý là căn cứ; SMS là Từ chối nhận tin |
| `AC-20.2.17` | Ảnh chụp của bản ghi phụ có Hạn chế xử lý; trong thời gian gộp, chính chủ rút yêu cầu qua kênh đã xác minh và Quản trị viên dỡ Hạn chế | Hoàn tác gộp | Bản ghi phụ không mang Hạn chế xử lý |
| `AC-20.2.18` | Trong thời gian gộp, email mới được thêm vào A (Bản ghi Chính) và chính chủ xác nhận Đồng ý qua email đó | Hoàn tác gộp | Email mới ở lại A với Đồng ý là căn cứ; B không đổi |
| `AC-20.2.19` | A (Bản ghi Chính) có Hạn chế xử lý theo yêu cầu của người X; trong thời gian gộp, yêu cầu rút được xác minh qua kênh vốn của B và Hạn chế được dỡ | Hoàn tác gộp (gộp nhầm hai người) | A vẫn mang Hạn chế xử lý; B không mang |
| `AC-20.2.20` | Gộp A (email Từ chối) với B (email Đồng ý), B là Bản ghi Chính | Hoàn tác gộp ngay sau đó, không có lượt ghi nhận nào khác | B trở về email Đồng ý; A trở về email Từ chối (việc gộp không tạo lượt ghi nhận mới) |
| `AC-20.3.1` | Gộp cách đây 89 ngày | Mở Lịch sử gộp | Có hành động "Hoàn tác gộp" |
| `AC-20.3.2` | Gộp cách đây 91 ngày | Mở Lịch sử gộp | Không còn hành động "Hoàn tác gộp"; mục sổ cái vẫn xem được |
| `AC-20.3.3` | Gộp lúc 09:00 ngày 1 tháng 3; `CFG-20-01` = 90 ngày (hạn gốc 09:00 ngày 30 tháng 5); biện pháp phòng ngừa áp từ 09:00 ngày 20 tháng 5 tới 09:00 ngày 19 tháng 6 (30 ngày, tính theo khoảng thời gian thực) | 09:00 ngày 20 tháng 6, Quản trị viên mở Lịch sử gộp trên Bản ghi Chính | Vẫn còn "Hoàn tác gộp"; hạn mới là 09:00 ngày 29 tháng 6 (hạn gốc cộng 30 ngày bị chặn); bản ghi phụ chưa vào diện dọn dẹp |
| `AC-20.3.4` | Như `AC-20.3.3`; lúc 09:00 ngày 1 tháng 6 (hạn gốc đã qua, biện pháp đang áp) | Quản trị viên mở Lịch sử gộp trên Bản ghi Chính | "Hoàn tác gộp" hiển thị nhưng bị chặn, nêu lý do biện pháp phòng ngừa đang áp; bản ghi phụ không bị dọn dẹp |

---

#### FEAT-21 — Khôi phục Giao dịch Gộp bị Gián đoạn

**Mô tả nghiệp vụ:** Công cụ xử lý sự cố khi một giao dịch gộp bị gián đoạn giữa chừng (mất kết nối, sự cố hệ thống) trước khi hoàn tất.

**Vai trò sử dụng chính:** Quản trị viên.

**Điều kiện tiên quyết:** Có ít nhất một giao dịch gộp bị gián đoạn; người xử lý giữ quyền quản trị Hoàn tác gộp.

**Luồng chính:**

1. Người xử lý mở danh sách giao dịch gộp cần xử lý (`BR-21.2`).
2. Chọn một giao dịch và chọn hoàn tất nốt hoặc đưa về trạng thái trước khi gộp (`BR-21.1`).
3. Hệ thống thực hiện và ghi nhật ký.

**Quy tắc nghiệp vụ:**

- **`BR-21.1` (Đưa về một trong hai trạng thái toàn vẹn):** Người giữ quyền quản trị Hoàn tác gộp xử lý giao dịch gộp bị gián đoạn bằng một trong hai cách: **hoàn tất nốt** các bước chuyển giao bản ghi con còn dang dở, hoặc **đưa cả hai bản ghi về trạng thái trước khi gộp**. Kết quả cuối cùng luôn là một trong hai trạng thái toàn vẹn — đã gộp xong hoặc chưa gộp — đúng yêu cầu `NFR-04`; không bao giờ tồn tại trạng thái một phần bản ghi con đã chuyển, một phần chưa.

  **Lý do nghiệp vụ:** Trạng thái nửa vời khiến cùng một khách có dữ liệu ở hai nơi và không ai biết nơi nào đúng; chỉ hai trạng thái cuối hợp lệ thì người xử lý luôn biết mình đang đưa dữ liệu về đâu.

- **`BR-21.2` (Danh sách giao dịch cần xử lý):** Mọi giao dịch gộp bị gián đoạn được liệt kê cho Quản trị viên kèm hai bản ghi liên quan và thời điểm gián đoạn; mỗi lượt xử lý được ghi nhật ký (`NFR-07`).

  **Lý do nghiệp vụ:** Một giao dịch gộp dở dang để bản ghi con nằm rải ở hai nơi, người dùng không biết bản ghi nào là đúng. Không có danh sách thì Quản trị viên chỉ phát hiện khi người dùng báo lỗi.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-21.2.1` | Một giao dịch gộp A và B bị gián đoạn khi mới chuyển được một phần bản ghi con | Quản trị viên mở danh sách giao dịch gộp cần xử lý | Thấy giao dịch kèm A, B và thời điểm gián đoạn |
| `AC-21.1.1` | Tiếp nối AC-21.2.1 | Quản trị viên chọn hoàn tất nốt | Kết quả giống một lần gộp bình thường: mọi bản ghi con ở A, B vào Thùng rác, sổ cái có mục gộp |
| `AC-21.1.2` | Tiếp nối AC-21.2.1 | Quản trị viên chọn đưa về trạng thái trước khi gộp | A và B hoạt động như trước; mọi bản ghi con trở về đúng bản ghi ban đầu |

---

### Nhóm G — Nhập Dữ liệu Thông minh qua Hàng đợi

#### FEAT-22 — Tải lên & Tiếp nhận Tệp Nhập khẩu Dung lượng lớn

**Mô tả nghiệp vụ:** Tải lên tệp danh bạ dạng bảng tính (Excel hoặc CSV) dung lượng lớn để nhập khẩu hàng loạt.

**Vai trò sử dụng chính:** Người có ô (Khách hàng, Nhập) hoặc (Doanh nghiệp, Nhập) khác Không có, Quản trị viên.

**Điều kiện tiên quyết:** Người dùng có ô Nhập trên loại dữ liệu cần nhập khác Không có.

**Luồng chính:**

1. Người dùng chọn tệp; hệ thống kiểm tra dung lượng ngay khi chọn (`BR-22.2`).
2. Hệ thống tiếp nhận tệp và chuyển sang bước ánh xạ cột (`FEAT-23`).
3. Sau khi người dùng xác nhận, tiến trình nhập chạy nền (`BR-22.1`).

**Quy tắc nghiệp vụ:**

- **`BR-22.1` (Xử lý nền):** Tệp tải lên được tiếp nhận và xử lý nền theo hàng đợi; người dùng không phải chờ trên màn hình, và tiến trình nhập không làm chậm các thao tác khác của người dùng trong không gian làm việc. Tệp gốc được lưu trong thời hạn tại `BR-33.8` rồi tự động xóa. Lô nhập chạy nền là **tiến trình chạy thay người nhập** (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8`): mỗi dòng chỉ được tạo, cập nhật hay gán trong phần giao của mức lúc khởi chạy và mức hiện tại của người nhập trên các ô Nhập, Sửa, Gán; dòng ra ngoài phần giao là dòng lỗi (`BR-24.2`). Người nhập bị tạm ngưng hoặc rời workspace thì lô tạm dừng, quản lý trực tiếp và Người có toàn quyền được thông báo để chuyển người khởi chạy hoặc dừng hẳn; kích hoạt lại không tự chạy tiếp.

  **Lý do nghiệp vụ:** Tệp hàng chục nghìn dòng mất nhiều phút để xử lý; bắt người dùng chờ trên màn hình thì họ đóng trình duyệt giữa chừng và lô nhập dở dang.

- **`BR-22.2` (Giới hạn dung lượng):** Dung lượng tối đa mỗi lần tải lên mặc định **50 MB** (Phụ lục B, `CFG-22-02`). Tệp vượt giới hạn bị từ chối ngay tại bước tải lên, kèm hướng dẫn chia nhỏ tệp.

  **Lý do nghiệp vụ:** Báo lỗi dung lượng sau khi người dùng đã chờ tải lên và ánh xạ cột là lãng phí thời gian; giới hạn phải được thể hiện trước khi họ bắt đầu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-22.1.1` | Người có ô (Khách hàng, Nhập) khác Không có | Tải tệp 15 MB chứa 10.000 dòng, bắt đầu nhập, rồi chuyển sang màn hình khác | Tiến trình tiếp tục chạy nền; người dùng thao tác bình thường; theo dõi được tiến độ khi quay lại |
| `AC-22.2.1` | Giới hạn 50 MB | Chọn tệp 60 MB để tải lên | Bị từ chối ngay khi chọn tệp, kèm hướng dẫn chia nhỏ; không chuyển sang bước ánh xạ cột |
| `AC-22.2.2` | Màn hình tải tệp | Quan sát trước khi chọn tệp | Giới hạn dung lượng hiện hành được hiển thị |
| `AC-22.2.3` | Người dùng có ô (Khách hàng, Nhập) = Không có | Tìm chức năng nhập khẩu | Không khả dụng |
| `AC-22.1.2` | Lô 10.000 dòng đang chạy; ô (Khách hàng, Gán) của người nhập bị thu hẹp từ Đơn vị và các đơn vị con về Đơn vị của mình | Lô chạy tiếp | Các dòng còn lại gán cho người ngoài đơn vị của người nhập thành dòng lỗi vượt quyền |
| `AC-22.1.3` | Lô đang chạy, người nhập bị tạm ngưng | Quan sát lô | Lô tạm dừng; quản lý trực tiếp và Người có toàn quyền nhận thông báo; kích hoạt lại người nhập không tự chạy tiếp |

---

#### FEAT-23 — Trợ lý Tự động Ánh xạ Cột Dữ liệu

**Mô tả nghiệp vụ:** Trợ lý trực quan tự động đọc tiêu đề cột trong tệp và đề xuất ánh xạ với các trường tương ứng (Họ tên, Số điện thoại, Email, Tên công ty, Chức danh, Địa chỉ).

**Vai trò sử dụng chính:** Người dùng thực hiện nhập dữ liệu.

**Điều kiện tiên quyết:** Tệp đã được tiếp nhận (`FEAT-22`).

**Luồng chính:**

1. Trợ lý đọc tiêu đề cột và đề xuất trường đích (`BR-23.1`).
2. Người dùng chỉnh ánh xạ, chọn chiến lược trùng lặp và trường tra cứu khi cập nhật (`BR-23.2` – `BR-23.4`).
3. Người dùng ánh xạ cột Người phụ trách hoặc cột Đơn vị tiếp nhận nếu cần (`BR-23.5`), chọn cơ sở đồng thuận (`BR-30.4`) và bắt đầu nhập.

**Quy tắc nghiệp vụ:**

- **`BR-23.1` (Nhận diện tiêu đề cột):** Tự động nhận diện tiêu đề cột phổ biến bằng tiếng Việt, tiếng Ả Rập và tiếng Anh (ví dụ "Số điện thoại", "Phone", "Mobile", "Email", "Họ tên", "Full Name").

  **Lý do nghiệp vụ:** Tệp danh bạ đến từ nhiều nguồn với tiêu đề khác nhau; tự nhận diện các tiêu đề phổ biến giảm phần lớn thao tác ánh xạ tay và lỗi ánh xạ lệch cột.

- **`BR-23.2` (Chỉnh ánh xạ):** Người dùng sửa được ánh xạ thủ công hoặc bỏ qua các cột không cần nhập. Cột không ánh xạ được trường nào thuộc nhóm Định danh KYC khi nhóm này đang tắt (`BR-01.5b`).

  **Lý do nghiệp vụ:** Đề xuất tự động có thể sai; người nhập phải sửa được trước khi chạy. Không cho ánh xạ vào nhóm KYC khi nhóm đang tắt để nhập khẩu không thành đường lưu dữ liệu mà doanh nghiệp đã quyết định không thu.

- **`BR-23.3` (Chiến lược xử lý trùng lặp cho từng lô):** Người dùng chọn cho mỗi lô: **Bỏ qua bản ghi trùng** (mặc định) hoặc **Cập nhật dữ liệu mới vào bản ghi cũ**. Lựa chọn **"Tạo bản ghi mới dù trùng"** chỉ khả dụng khi người thực hiện xác nhận lô dữ liệu thuộc trường hợp Định danh dùng chung (lối ra tại `BR-17.2`) — khi đó các bản ghi tạo ra tự động mang nhãn Định danh dùng chung và lượt xác nhận được ghi nhật ký. Chiến lược cập nhật không thay đổi được nguồn gốc (`BR-32.3`), không nâng được mức đồng thuận (`BR-30.10`) và không tạo được bước chuyển giai đoạn ngoài ma trận (`BR-12.6`).

  **Lý do nghiệp vụ:** Kênh nhập khẩu không được trở thành đường đi vòng qua chính sách chặn trùng, cam kết đồng thuận và ma trận vòng đời.

- **`BR-23.4` (Trường tra cứu bắt buộc khi cập nhật):** Khi chọn chiến lược cập nhật, người dùng **bắt buộc** chọn trường dùng để tìm bản ghi cũ: **(a)** mã khách hàng nội bộ (chính xác nhất); **(b)** địa chỉ email; **(c)** số điện thoại. Dòng không khớp bản ghi nào được xử lý như tạo mới và được đánh dấu cảnh báo trong báo cáo kết quả.

  **Lý do nghiệp vụ:** Cập nhật mà không nói rõ tìm bản ghi cũ theo gì sẽ ghi đè nhầm dữ liệu của người khác khi tên trùng nhau.

- **`BR-23.5` (Người phụ trách, đơn vị tiếp nhận và bản ghi giữ chỗ của dòng nhập):** Nhập từ tệp không đi qua hàng đợi, trừ khi tệp chỉ định đơn vị tiếp nhận (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10`):
  - **(a) Cột Người phụ trách:** mỗi giá trị chịu ô (Khách hàng, Gán) của người nhập. Giá trị ngoài phạm vi ô Gán, trỏ tới người đang Tạm ngưng, Đã rời hoặc không tồn tại là **dòng lỗi** (`BR-24.2`).
  - **(b) Giữ chỗ cho người chưa chấp nhận lời mời:** giá trị trỏ tới thành viên **Đang chờ chấp nhận** (gồm lời mời Chờ gửi và lời mời đang tạm treo) tạo **bản ghi giữ chỗ**: ô Người phụ trách để trống kèm nhãn "giữ chỗ cho" người được mời — nên không ai thấy bản ghi qua mức "của mình" hay chuỗi cấp dưới; bản ghi thuộc Đơn vị chính ghi trong lời mời, và ô Gán của người nhập được xét trên đơn vị đó; lời mời không ghi Đơn vị chính thì dòng đó là dòng lỗi. Không ai nhận việc được trên bản ghi giữ chỗ, và bản ghi bị chặn với mọi người trừ nhóm ngoại lệ tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6` bước 3 — kể cả người có mức Toàn workspace như vai trò Kiểm toán cũng không thấy. Khi Đơn vị chính trong lời mời được sửa, bản ghi giữ chỗ chuyển theo.
  - **(c) Kết thúc giữ chỗ:** khi người được mời chấp nhận, bản ghi tự gán cho họ nếu vai trò của họ có ô (Khách hàng, Sửa) khác Không có; nếu không, bản ghi thành bản ghi chờ phân công của hàng đợi đơn vị đó và người phụ trách đơn vị được báo. Người phụ trách hoặc đồng phụ trách đơn vị giữ chỗ hay đơn vị cấp trên trong nhánh có ô Gán bao phủ, hoặc Người có toàn quyền, gán lại được bản ghi giữ chỗ — việc đó chấm dứt giữ chỗ. Lời mời bị từ chối, hết hạn, bị thu hồi, hoặc thành viên đang chờ bị gỡ thì bản ghi thành bản ghi chờ phân công bình thường của hàng đợi đơn vị đó.
  - **(d) Cột Đơn vị tiếp nhận:** tệp có thể ánh xạ cột này thay cho cột Người phụ trách để đưa bản ghi vào hàng đợi của một đơn vị dưới dạng bản ghi chờ phân công (`BR-31.9`). Giá trị chịu [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.12`: người nhập không có toàn quyền chỉ dùng được đơn vị tiếp nhận mặc định của loại nguồn "Nhập khẩu từ tệp" tìm được từ Đơn vị chính của mình; giá trị khác là dòng lỗi.
  - **(e)** Cả hai cột trống thì người nhập là Người phụ trách của mọi dòng.

  **Lý do nghiệp vụ:** Doanh nghiệp mới thường nhập danh bạ cũ cùng lúc mời đội ngũ; nếu bắt buộc mọi người nhận phải đã vào workspace, họ phải nhập hai lần hoặc gán tạm cho một người rồi bàn giao lại hàng nghìn bản ghi. Nhưng bản ghi giữ chỗ không được thành vùng xám ai cũng thấy hoặc không ai quản: nó thuộc đúng đơn vị người được mời sẽ vào, chỉ người phụ trách đơn vị đó quản lý, và tự về hàng đợi khi lời mời không thành. Giới hạn theo ô Gán để nhập khẩu không thành cách giao khách cho người mà người nhập không được phép giao.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-23.1.1` | Tệp có các cột "Họ và tên", "Điện thoại", "Email", "Công ty" | Tải lên | Trợ lý đề xuất đúng Họ tên, Số điện thoại, Email, Tên doanh nghiệp |
| `AC-23.1.2` | Tệp có tiêu đề cột tiếng Anh "Full Name", "Mobile" | Tải lên | Trợ lý đề xuất đúng Họ tên và Số điện thoại |
| `AC-23.2.1` | Trợ lý đề xuất cột "Ghi chú nội bộ" → trường Chức danh | Người dùng đổi thành "Bỏ qua cột này" | Cột không được nhập |
| `AC-23.2.2` | Nhóm Định danh KYC đang tắt, tệp có cột "Số CCCD" | Mở danh sách trường đích cho cột đó | Không có trường KYC nào để chọn |
| `AC-23.3.1` | Lô có 100 dòng trùng email với bản ghi hiện hữu, chiến lược mặc định | Chạy nhập | 100 dòng bị bỏ qua và được nêu trong báo cáo kết quả |
| `AC-23.3.2` | Người dùng mở danh sách chiến lược trùng lặp | Tìm "Tạo bản ghi mới dù trùng" | Lựa chọn chỉ khả dụng sau khi người dùng xác nhận lô thuộc trường hợp Định danh dùng chung; khi dùng, bản ghi tạo ra mang nhãn Định danh dùng chung |
| `AC-23.4.1` | Chọn chiến lược cập nhật | Bỏ trống trường tra cứu, bấm bắt đầu | Không bắt đầu được; yêu cầu chọn trường tra cứu |
| `AC-23.4.2` | Chiến lược cập nhật theo email; một dòng có email không khớp bản ghi nào | Chạy nhập | Dòng đó được tạo mới và có cảnh báo trong báo cáo kết quả |
| `AC-23.5.1` | Nhân viên Kinh doanh nhập 200 khách, không ánh xạ cột Người phụ trách hay Đơn vị tiếp nhận | Nhập xong | Nhân viên đó là Người phụ trách của 200 khách; không bản ghi nào vào hàng đợi |
| `AC-23.5.2` | Người nhập có (Khách hàng, Gán) = Chỉ của mình | Nhập tệp có cột Người phụ trách trỏ tới đồng nghiệp | Các dòng đó báo lỗi vượt quyền |
| `AC-23.5.3` | Tệp có cột Người phụ trách; 3 dòng trỏ tới người đang Tạm ngưng | Nhập | 3 dòng báo lỗi nêu lý do; các dòng khác nhập bình thường |
| `AC-23.5.4` | Quản trị viên nhập 1.200 dòng gán cho N đang chờ chấp nhận lời mời, lời mời ghi Đơn vị chính "Kinh doanh – Đà Nẵng" | Lô chạy; đồng nghiệp cùng đội và người giữ vai trò Kiểm toán mở danh sách | Không ai trong số đó thấy 1.200 bản ghi; người phụ trách "Kinh doanh – Đà Nẵng" (có Xem bao phủ) thấy bản ghi kèm nhãn "giữ chỗ cho N", không có nút nhận việc |
| `AC-23.5.5` | Tiếp nối AC-23.5.4 | N chấp nhận lời mời với vai trò Nhân viên Kinh doanh | 1.200 bản ghi tự gán cho N |
| `AC-23.5.6` | Như AC-23.5.4 nhưng vai trò trong lời mời của N có (Khách hàng, Sửa) = Không có | N chấp nhận | Bản ghi không tự gán cho N; thành bản ghi chờ phân công của "Kinh doanh – Đà Nẵng"; người phụ trách đơn vị được báo |
| `AC-23.5.7` | Như AC-23.5.4 | N từ chối lời mời | 1.200 bản ghi thành bản ghi chờ phân công trong hàng đợi "Kinh doanh – Đà Nẵng"; thành viên đơn vị nhận việc được |
| `AC-23.5.8` | Như AC-23.5.4; người phụ trách "Kinh doanh – Đà Nẵng" có ô Gán bao phủ | Gán lại 10 bản ghi giữ chỗ cho K | 10 bản ghi thuộc K, hết giữ chỗ; khi N chấp nhận, chỉ các bản ghi còn lại tự gán cho N |
| `AC-23.5.9` | Lời mời của N không ghi Đơn vị chính | Nhập dòng gán cho N | Dòng đó báo lỗi nêu thiếu Đơn vị chính trong lời mời |
| `AC-23.5.10` | Nhân viên Marketing có quyền nhập, không có toàn quyền; mặc định loại nguồn "Nhập khẩu từ tệp" tìm từ Đơn vị chính của họ là "Kinh doanh – Hà Nội" | Nhập tệp có cột Đơn vị tiếp nhận ghi "Kinh doanh – Hà Nội" cho 300 dòng và "Kinh doanh – Đà Nẵng" cho 20 dòng | 300 dòng vào hàng đợi "Kinh doanh – Hà Nội" dưới dạng bản ghi chờ phân công; 20 dòng báo lỗi đơn vị tiếp nhận không hợp lệ |

---

#### FEAT-24 — Xử lý Nhập khẩu theo Hàng đợi & Báo cáo Lỗi Chi tiết

**Mô tả nghiệp vụ:** Tiến trình nền xử lý tệp theo từng phần, kiểm tra tính hợp lệ từng dòng, nhập các dòng hợp lệ và xuất báo cáo chi tiết các dòng lỗi để người dùng sửa.

**Vai trò sử dụng chính:** Tiến trình Hệ thống, người dùng thực hiện nhập dữ liệu.

**Điều kiện tiên quyết:** Người dùng đã xác nhận bắt đầu nhập ở `FEAT-23`.

**Luồng chính:**

1. Tiến trình nền xử lý tệp theo từng phần, kiểm tra từng dòng và nhập dòng hợp lệ.
2. Người dùng theo dõi tiến độ (`BR-24.1`).
3. Khi xong, hệ thống tạo tệp báo cáo lỗi nếu có dòng lỗi và gửi thông báo kèm đường tải về (`BR-24.2`).

**Quy tắc nghiệp vụ:**

- **`BR-24.1` (Theo dõi tiến độ):** Người dùng theo dõi tiến độ gần như tức thời: số dòng đã xử lý, số dòng thành công, số dòng lỗi.

  **Lý do nghiệp vụ:** Người nhập cần biết lô có đang chạy, chạy đến đâu và có nhiều lỗi không để quyết định dừng sửa tệp sớm, thay vì chờ tới cuối mới phát hiện cả lô sai.

- **`BR-24.2` (Tệp báo cáo lỗi):** Nếu có dòng lỗi, hệ thống tạo tệp báo cáo lỗi, mỗi dòng lỗi kèm nguyên nhân cụ thể, và cấp đường tải về có hiệu lực **24 giờ** (thống nhất `BR-25.2`). Các nguyên nhân lỗi gồm: sai định dạng email; số điện thoại không chuẩn hóa được; **thiếu cả email lẫn số điện thoại** (vi phạm `BR-01.1` — thiếu họ tên **không** phải lỗi); bước chuyển giai đoạn không hợp lệ (`BR-12.6`); thiếu trường tra cứu khi chọn chiến lược cập nhật (`BR-23.4`); trùng lặp bị chặn theo chính sách (`BR-17.2`); dòng ra ngoài phần giao của mức người nhập lúc khởi chạy và hiện tại (`BR-22.1`); Người phụ trách ngoài phạm vi ô Gán của người nhập, đang Tạm ngưng, Đã rời hoặc không tồn tại; lời mời của người được giữ chỗ không ghi Đơn vị chính; đơn vị tiếp nhận không hợp lệ (`BR-23.5`).

  **Lý do nghiệp vụ:** Một nguyên nhân chung chung ("dòng không hợp lệ") buộc người dùng tự dò từng ô trong hàng trăm dòng; nguyên nhân cụ thể cho phép sửa đúng chỗ và nhập lại.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-24.1.1` | Lô 10.000 dòng đang chạy | Mở màn hình tiến độ | Thấy số dòng đã xử lý, thành công, lỗi tăng dần tới khi đạt 100% |
| `AC-24.2.1` | Lô hoàn tất với 9.850 dòng thành công, 150 dòng lỗi | Tải tệp báo cáo lỗi | Tệp có đúng 150 dòng, mỗi dòng mang một nguyên nhân cụ thể thuộc danh sách tại `BR-24.2` |
| `AC-24.2.2` | Một dòng có email hợp lệ nhưng không có họ tên | Chạy nhập | Dòng đó được nhập thành công với tên thay thế theo `BR-01.1`, không có trong báo cáo lỗi |
| `AC-24.2.3` | Một dòng không có cả email lẫn số điện thoại | Chạy nhập | Dòng đó có trong báo cáo lỗi với nguyên nhân "thiếu cả email lẫn số điện thoại" |
| `AC-24.2.4` | Báo cáo lỗi được tạo lúc 10:00 | Mở đường tải về lúc 10:05 hôm sau | Bị từ chối vì đã hết hiệu lực |

---

### Nhóm H — Xuất Dữ liệu & Danh sách Hiển thị

#### FEAT-25 — Xuất Dữ liệu Khách hàng có Kiểm soát

**Mô tả nghiệp vụ:** Xuất danh sách khách hàng ra tệp theo bộ lọc tùy chỉnh, có hạn mức, có phê duyệt cho lần xuất lớn, có nhật ký, và bảo vệ đường tải về.

**Vai trò sử dụng chính:** Người có ô (Khách hàng, Xuất) khác Không có — mặc định Nhân viên Kinh doanh trong giới hạn `BR-25.5` — và Người có toàn quyền.

**Điều kiện tiên quyết:** Người xuất có ô (Khách hàng, Xuất) — hoặc (Doanh nghiệp, Xuất) — khác Không có.

**Luồng chính:**

1. Người dùng chọn bộ lọc và các trường cần xuất; hệ thống chỉ liệt kê trường người đó được xuất.
2. Hệ thống kiểm tra hạn mức và ngưỡng phê duyệt (`BR-25.4`, `BR-25.5`); vượt ngưỡng thì tạo yêu cầu phê duyệt gửi đúng người duyệt trước khi tạo tệp.
3. Tệp được tạo nền với giá trị theo đúng mức hiển thị của người xuất, chỉ chứa bản ghi trong mức Xuất của họ (`BR-25.1`).
4. Người dùng tải tệp qua đường tải một lần (`BR-25.2`); lần xuất được ghi nhật ký (`BR-25.3`).

**Quy tắc nghiệp vụ:**

- **`BR-25.1` (Xuất nền, đúng mức hiển thị):** Việc xuất chạy nền theo hàng đợi, hỗ trợ hàng chục nghìn bản ghi mà không làm chậm hệ thống; số bản ghi tối đa mỗi lần xuất theo gói dịch vụ (`NFR-11`). Giá trị trong tệp xuất tuân theo **đúng mức hiển thị của người xuất** tại `BR-04.3` (`NFR-06`).

  **Lý do nghiệp vụ:** Xuất là đường dữ liệu rời khỏi hệ thống; nếu tệp chứa giá trị đầy đủ hơn màn hình, mọi chính sách che chỉ cần một nút xuất để vô hiệu.

- **`BR-25.2` (Bảo vệ đường tải về):** Đường tải về có hiệu lực **24 giờ**, **chỉ dùng được một lần**, và tên tệp được chuẩn hóa an toàn. Cùng thời hạn áp dụng cho đường tải tệp báo cáo lỗi nhập khẩu (`BR-24.2`).

  **Lý do nghiệp vụ:** Đường tải về bị chuyển tiếp qua email hay chat là đường rò rỉ phổ biến; giới hạn thời gian và một lần dùng làm đường tải mất giá trị khi bị lộ.

- **`BR-25.3` (Nhật ký xuất dữ liệu):** Mỗi lần xuất bắt buộc ghi: người xuất, thời điểm, bộ lọc đã dùng, **danh sách trường được xuất** và **danh sách bản ghi được xuất**. Nhật ký xuất chịu cùng chế độ kiểm soát truy cập như nhật ký kiểm toán (`NFR-14`).

  **Lý do nghiệp vụ:** Đây là căn cứ chính để trả lời câu hỏi "dữ liệu của khách hàng đã ra ngoài những đâu" khi thực thi quyền chủ thể dữ liệu (`BR-33.8`) và khi có nghi vấn lấy dữ liệu hàng loạt.

- **`BR-25.4` (Ngưỡng xuất lớn cần phê duyệt):** Một lần xuất bắt buộc được phê duyệt **trước khi tệp được tạo** khi thỏa bất kỳ điều kiện nào dưới đây:
  - **Với người có ô (Khách hàng, Xuất) rộng hơn Chỉ của mình:** lần xuất **vượt 5.000 bản ghi** (Phụ lục B, `CFG-25-01`).
  - **Với người có ô (Khách hàng, Xuất) = Chỉ của mình** (mặc định: Nhân viên Kinh doanh, `BR-25.5`): lần xuất làm vượt **giá trị nhỏ hơn** giữa ngưỡng xuất lớn (`CFG-25-01`) và hạn mức ngày (`CFG-25-02`) — cần phê duyệt từng lần, và một lần phê duyệt **không được nâng tổng lượng xuất trong ngày lên quá hai lần hạn mức ngày**.
  - **Trường thuộc nhóm Định danh KYC — sàn bắt buộc, giới hạn theo mục đích, không theo hạn mức:** chỉ **Người có toàn quyền** xuất được, mỗi lần bắt buộc có **Người phụ trách Bảo vệ Dữ liệu đồng phê duyệt** (hoặc người thứ hai theo quy tắc thay thế tại `NFR-14`) kèm khai báo mục đích đúng với mục đích đã đăng ký tại `BR-01.5b`. Năng lực xuất nhóm trường này **không đưa được vào bất kỳ vai trò nào** — dựng sẵn hay tự tạo — qua điều chỉnh ô hay vai trò tự tạo (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.3`).

  **Người phê duyệt là quản lý trực tiếp của người xuất** theo chuỗi quản lý tại [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4; người xuất không có quản lý trực tiếp, hoặc là Người có toàn quyền, thì người phê duyệt là Chủ sở hữu; với chính Chủ sở hữu, người phê duyệt là Người phụ trách Bảo vệ Dữ liệu (hoặc người thứ hai theo `NFR-14`). **Không ai tự phê duyệt lần xuất của chính mình.**

  **Lý do nghiệp vụ:** Xuất là thao tác đưa dữ liệu cá nhân ra khỏi hệ thống; một lần xuất chạm ngưỡng luôn phải có hai người khác nhau đứng tên. Phê duyệt theo tuyến báo cáo vì buộc một nhân viên xin phê duyệt của người ở đội khác là sai tuyến và sẽ bị bỏ qua trong thực tế. Nhóm KYC bị khóa theo mục đích ở cấp sàn: nếu năng lực xuất KYC cấp được qua vai trò, chỉ cần một vai trò tự tạo là vô hiệu chính giới hạn mục đích tại `BR-01.5b` đúng ở điểm dữ liệu rời khỏi hệ thống.

- **`BR-25.5` (Xuất dữ liệu ở mức Chỉ của mình):** Người có ô (Khách hàng, Xuất) = Chỉ của mình — theo ma trận mặc định là Nhân viên Kinh doanh — được xuất các khách hàng mình phụ trách, với ba ràng buộc: **(a)** hạn mức mặc định **2.000 bản ghi/người/ngày** (Phụ lục B, `CFG-25-02`), chỉ vượt được khi có phê duyệt theo `BR-25.4`; **(b)** **không** xuất được trường thuộc nhóm Định danh KYC; **(c)** mọi lần xuất ghi nhật ký theo `BR-25.3`.

  **Lý do nghiệp vụ:** Nhu cầu hằng ngày (in danh sách khách để đi gặp, chuẩn bị danh sách mời hội thảo) nếu không có đường hợp lệ thì cách làm thật sẽ là sao chép màn hình vào bảng tính hoặc chụp màn hình — hoàn toàn không có nhật ký, phá vỡ `BR-25.3`.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-25.1.1` | Quản trị viên xuất 30.000 bản ghi | Bấm xuất | Tệp được tạo nền; người dùng nhận thông báo khi sẵn sàng |
| `AC-25.1.2` | Người xuất ở cột (B) của `BR-04.3` | Mở tệp xuất | Số điện thoại, email ở mức che một phần như trên màn hình |
| `AC-25.2.1` | Tệp xuất sẵn sàng | Tải về lần thứ nhất, rồi dùng lại cùng đường tải | Lần thứ nhất thành công; lần thứ hai bị từ chối |
| `AC-25.2.2` | Tệp xuất sẵn sàng lúc 10:00, chưa tải | Mở đường tải lúc 10:05 hôm sau | Bị từ chối vì hết hiệu lực |
| `AC-25.3.1` | Nhân viên Marketing xuất 800 bản ghi với 5 trường | Người có quyền đọc nhật ký toàn phần mở nhật ký xuất | Thấy người xuất, thời điểm, bộ lọc, đủ 5 trường và 800 bản ghi |
| `AC-25.4.1` | Quản lý Kinh doanh được cấp (Khách hàng, Xuất) = Đơn vị và các đơn vị con, không có quản lý trực tiếp | Xuất 6.000 bản ghi | Tệp chưa được tạo; yêu cầu phê duyệt gửi Chủ sở hữu |
| `AC-25.4.2` | Nhân viên Marketing được cấp ô Xuất = Toàn workspace, quản lý trực tiếp là Quản lý Marketing Q | Xuất 6.000 bản ghi | Yêu cầu phê duyệt gửi Q |
| `AC-25.4.3` | Chủ sở hữu xuất 6.000 bản ghi | Bấm xuất | Yêu cầu phê duyệt gửi Người phụ trách Bảo vệ Dữ liệu; Chủ sở hữu không tự phê duyệt được |
| `AC-25.4.4` | Quản trị viên chọn trường Số Căn cước công dân khi xuất | Bấm xuất, không khai báo mục đích | Không tạo được yêu cầu; bắt buộc khai báo mục đích và cần Người phụ trách Bảo vệ Dữ liệu đồng phê duyệt |
| `AC-25.4.5` | Quản lý Marketing mở danh sách trường xuất | Tìm trường thuộc nhóm KYC | Không có trường KYC nào để chọn |
| `AC-25.4.6` | Người có quyền Quản lý vai trò tạo vai trò tự tạo và tìm cách cấp năng lực xuất nhóm KYC | Mở cấu hình ô | Lựa chọn bị vô hiệu kèm giải thích Sàn bắt buộc `BR-25.4` |
| `AC-25.5.1` | Nhân viên Kinh doanh A đã xuất 1.500 bản ghi hôm nay, hạn mức 2.000 | Xuất thêm 600 bản ghi | Lần xuất bị giữ lại và chuyển thành yêu cầu phê duyệt gửi quản lý trực tiếp của A (không gửi Quản lý Marketing) |
| `AC-25.5.2` | Tiếp nối AC-25.5.1, Quản lý đã phê duyệt | A xuất tiếp tới tổng 4.001 bản ghi trong ngày | Phần vượt 4.000 bản ghi (hai lần hạn mức) không xuất được |
| `AC-25.5.3` | A mở danh sách trường xuất | Tìm trường Số Căn cước công dân | Không có trong danh sách; không có đường phê duyệt nào mở được |

---

#### FEAT-26 — Danh sách Hiển thị Dùng chung

**Mô tả nghiệp vụ:** Hiển thị danh bạ theo các bộ lọc lưu sẵn, ví dụ "Khách hàng của tôi", "Khách hàng tiềm năng mới trong tuần", "Khách hàng VIP", "Khách hàng chưa có hoạt động trong 30 ngày", và chia sẻ bộ lọc cho đồng nghiệp.

**Vai trò sử dụng chính:** Mọi người dùng trong không gian làm việc.

**Điều kiện tiên quyết:** Người dùng có ô (Khách hàng, Xem) khác Không có.

**Luồng chính:**

1. Người dùng đặt điều kiện lọc, cột và thứ tự sắp xếp, lưu thành danh sách (`BR-26.1`).
2. Người dùng chia sẻ danh sách cho đồng nghiệp hoặc nhóm.
3. Người nhận mở danh sách và chỉ thấy bản ghi trong mức Xem của chính mình (`BR-26.2`).

**Quy tắc nghiệp vụ:**

- **`BR-26.1` (Nội dung một danh sách):** Một danh sách lưu cấu hình cột hiển thị, điều kiện lọc kết hợp (**và** / **hoặc**) và thứ tự sắp xếp.

  **Lý do nghiệp vụ:** Mỗi đội có các bộ lọc dùng hằng ngày; lưu lại giúp không phải dựng lại bộ lọc phức tạp mỗi lần, và chia sẻ giúp cả đội nhìn cùng một tập khách.

- **`BR-26.2` (Chia sẻ cấu hình, không chia sẻ dữ liệu):** Danh sách dùng chung chỉ chia sẻ **cấu hình bộ lọc**; mỗi người mở danh sách chỉ thấy các bản ghi thuộc phạm vi dữ liệu của chính mình, với mức che theo `BR-04.3`.

  **Lý do nghiệp vụ:** Nếu chia sẻ danh sách đồng nghĩa với chia sẻ dữ liệu, một thao tác chia sẻ bộ lọc sẽ vượt qua toàn bộ mô hình phạm vi dữ liệu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-26.1.1` | Người dùng lọc "Giai đoạn là MQL **và** (tỉnh/thành là Hà Nội **hoặc** Đà Nẵng)", chọn 5 cột, sắp xếp theo điểm giảm dần | Lưu thành danh sách, mở lại hôm sau | Danh sách giữ đúng điều kiện, cột và thứ tự sắp xếp |
| `AC-26.2.1` | Quản lý Kinh doanh chia sẻ danh sách "Khách VIP phòng 1" cho Nhân viên A có (Khách hàng, Xem) = Chỉ của mình | A mở danh sách | A chỉ thấy các khách VIP do A phụ trách hoặc được chia sẻ cho A |

---

### Nhóm I — Dòng thời gian 360 độ & Ngữ cảnh Khách hàng

#### FEAT-27 — Dòng thời gian Hoạt động Hợp nhất 360 độ

**Mô tả nghiệp vụ:** Một luồng duy nhất tập hợp mọi sự kiện liên quan đến khách hàng, theo thứ tự thời gian mới nhất trước.

**Vai trò sử dụng chính:** Mọi người dùng có quyền xem khách hàng.

**Điều kiện tiên quyết:** Người xem có ô (Khách hàng, Xem) bao phủ bản ghi, hoặc có quyền đọc tự động (`BR-35.4`).

**Luồng chính:**

1. Người dùng mở thẻ Dòng thời gian trên hồ sơ.
2. Hệ thống hợp nhất sự kiện từ các nguồn (`BR-27.1`), lọc theo phạm vi đọc riêng của từng mục và mức che (`BR-27.2`), hiển thị mới nhất trước.

**Quy tắc nghiệp vụ:**

- **`BR-27.1` (Các nguồn sự kiện hợp nhất):**
  - **Ghi chú:** trao đổi nội bộ của nhân viên (`FEAT-36`).
  - **Vé hỗ trợ:** yêu cầu hỗ trợ và khiếu nại của khách.
  - **Cơ hội bán hàng:** cơ hội đang mở và lịch sử chuyển giai đoạn bán hàng.
  - **Công việc & lịch hẹn:** cuộc gọi, cuộc họp, việc cần làm.
  - **Hội thoại đa kênh:** các phiên trò chuyện qua trò chuyện trực tuyến và các ứng dụng nhắn tin đã tích hợp.
  - **Thay đổi trạng thái:** đổi giai đoạn vòng đời, gộp bản ghi, thay đổi Người phụ trách.

  **Lý do nghiệp vụ:** Ngữ cảnh khách hàng nằm rải ở nhiều phân hệ; chỉ khi mọi nguồn hội tụ về một luồng thì người tiếp nhận mới biết đủ trước khi trả lời khách (vấn đề 1 tại Mục 2.1).

- **`BR-27.2` (Thứ tự và phạm vi đọc):** Sự kiện hiển thị theo thời gian mới nhất trước. Mỗi sự kiện vẫn tuân theo phạm vi đọc riêng của nó (ví dụ phạm vi đọc ghi chú tại `BR-36.1`) và chính sách che mặt nạ tại `FEAT-04`.

  **Lý do nghiệp vụ:** Dòng thời gian là nơi mọi dữ liệu hội tụ; nếu nó không tuân theo phạm vi đọc của từng mục, nó trở thành đường đọc vòng mọi giới hạn khác trong tài liệu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-27.1.1` | Khách vừa gửi một vé hỗ trợ, được tạo một công việc và đổi giai đoạn | Mở dòng thời gian | Thấy cả ba sự kiện |
| `AC-27.1.2` | Khách có một phiên trò chuyện trực tuyến hôm qua | Mở dòng thời gian | Thấy phiên trò chuyện |
| `AC-27.2.1` | Các sự kiện xảy ra lúc 09:00, 11:00, 15:00 | Mở dòng thời gian | Thứ tự hiển thị 15:00, 11:00, 09:00 |
| `AC-27.2.2` | Khách có ghi chú phạm vi "Nội bộ đội bán hàng" | Nhân viên Marketing mở dòng thời gian | Không đọc được nội dung ghi chú đó |

---

#### FEAT-28 — Ngữ cảnh Khách hàng Một chạm cho Hộp thư Đa kênh

**Mô tả nghiệp vụ:** Khung thông tin khách hàng hiển thị ngay cạnh hội thoại trong Hộp thư Đa kênh, giúp tư vấn viên nắm trọn thông tin khách trong một lần mở.

**Vai trò sử dụng chính:** Nhân viên Hỗ trợ (gồm Tư vấn viên trò chuyện trực tuyến).

**Điều kiện tiên quyết:** Người dùng đang xử lý một hội thoại gắn với khách hàng trong Hộp thư Đa kênh.

**Luồng chính:**

1. Tư vấn viên mở hội thoại; khung ngữ cảnh hiển thị ngay bên cạnh (`BR-28.1`), trong ngưỡng hiệu năng `BR-28.2`.
2. Quyền xem đi theo quan hệ với bản ghi: mức Xem thông thường hoặc quyền đọc tự động khi hội thoại còn mở (`BR-35.4`).
3. Tư vấn viên mở hồ sơ 360 độ từ khung khi cần.

**Quy tắc nghiệp vụ:**

- **`BR-28.1` (Nội dung khung ngữ cảnh):** Hiển thị đồng thời: thông tin liên hệ, doanh nghiệp trực thuộc, giai đoạn vòng đời, **3 Cơ hội bán hàng gần nhất**, **3 Vé hỗ trợ gần nhất** và các ghi chú ghim. Mức hiển thị của thông tin liên hệ áp đúng `FEAT-04` theo quan hệ của người xem với bản ghi — với Nhân viên Hỗ trợ đang xử lý vé/hội thoại là **cột (C)**; khung ngữ cảnh **không** có chính sách che riêng. Ghi chú ghim được lọc theo phạm vi đọc của người xem (`BR-36.7`).

  **Lý do nghiệp vụ:** Tư vấn viên cần đúng những gì giúp trả lời ngay — khách là ai, đang mua gì, vừa khiếu nại gì; ghi chú thương lượng nội bộ không cần cho việc đó nên được lọc theo phạm vi đọc.

- **`BR-28.2` (Cam kết hiệu năng):** Ngưỡng **nghiệm thu bắt buộc** là phản hồi dưới **150 mili giây với 95% lượt truy vấn** (`NFR-02`); mức **50 mili giây với 50% lượt truy vấn** là mục tiêu tối ưu mong đợi, không dùng làm tiêu chí đạt/không đạt.

  **Lý do nghiệp vụ:** Tư vấn viên mở khung này khi khách đang chờ trả lời; chậm vài giây là khách cảm nhận được và tư vấn viên sẽ bỏ qua khung, làm `KPI-02` không đạt.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-28.1.1` | Khách có 5 Cơ hội và 4 Vé hỗ trợ | Tư vấn viên đang xử lý hội thoại mở khung ngữ cảnh | Thấy thông tin liên hệ, doanh nghiệp, giai đoạn, đúng 3 Cơ hội và 3 Vé gần nhất |
| `AC-28.1.2` | Tư vấn viên C đang xử lý hội thoại, bản ghi ngoài phạm vi thông thường của C | Xem thông tin liên hệ trong khung | Che một phần theo cột (C) |
| `AC-28.1.3` | Khách có 3 ghi chú ghim, chỉ 1 ghi chú được đánh dấu "Cho phép tuyến Hỗ trợ đọc", 2 ghi chú phạm vi "Nội bộ đội bán hàng" | C mở khung ngữ cảnh | Chỉ thấy 1 ghi chú ghim được phép |
| `AC-28.2.1` | Môi trường nghiệm thu theo điều kiện đo tại Mục 4.1 | Đo thời gian phản hồi khung ngữ cảnh | 95% lượt truy vấn dưới 150 mili giây |

---

### Nhóm J — Định danh, Đồng thuận & Tuân thủ Dữ liệu Cá nhân

#### FEAT-29 — Quản lý Kênh liên lạc Đa kênh & Trạng thái Tiếp cận

**Mô tả nghiệp vụ:** Quản lý toàn bộ kênh liên lạc có thể tiếp cận của khách hàng — email cá nhân, email công việc, số di động, số bàn, định danh trên các ứng dụng nhắn tin đã tích hợp (WhatsApp, Facebook Messenger, Zalo) — như các mục kênh liên lạc độc lập trên hồ sơ.

**Vai trò sử dụng chính:** Mọi người dùng có quyền cập nhật khách hàng.

**Điều kiện tiên quyết:** Người thêm, sửa kênh liên lạc có ô (Khách hàng, Sửa) bao phủ bản ghi.

**Luồng chính:**

1. Người dùng thêm hoặc sửa kênh liên lạc, đặt kênh chính cho mỗi loại (`BR-29.1`).
2. Hệ thống cập nhật trạng thái tiếp cận từ kết quả gửi nhận và từ sự kiện nghiệp vụ (`BR-29.2`).
3. Kênh không tiếp cận được bị loại khỏi gửi tự động (`BR-29.3`).

**Quy tắc nghiệp vụ:**

- **`BR-29.1` (Một kênh chính mỗi loại):** Mỗi loại kênh có tối đa **một** kênh chính. Đặt một kênh khác làm chính thì kênh cũ tự động mất trạng thái chính.

  **Lý do nghiệp vụ:** Nhiều kênh chính cho cùng một loại làm hệ thống không biết gửi vào đâu và gửi lặp cho khách.

- **`BR-29.2` (Bốn trạng thái tiếp cận):** Theo danh mục A.10: ba trạng thái kỹ thuật — **Đã xác thực** (đã gửi nhận thành công), **Không tiếp cận được** (email hỏng hoặc số không tồn tại), **Chưa kiểm tra** — và một trạng thái nghiệp vụ **Không còn hiệu lực** — địa chỉ không còn thuộc về khách hàng: do `BR-10.4` sinh ra khi khách rời doanh nghiệp sở hữu địa chỉ, hoặc khi nhà mạng/nền tảng báo số điện thoại đã đổi chủ ([`campaigns-srs.md`](./campaigns-srs.md) `BR-28.4`); trong trường hợp đổi chủ, mọi Đồng ý nhận tin trên các kênh dùng số đó chuyển về Chưa có đồng thuận. Thứ tự thắng khi gộp quy định tại `BR-18.2`.

  **Lý do nghiệp vụ:** Phân biệt địa chỉ hỏng (không đảo được) với địa chỉ không còn thuộc về khách (đảo được khi khách quay lại) để vừa không gửi nhầm người, vừa không khóa vĩnh viễn một địa chỉ có thể dùng lại.

- **`BR-29.3` (Loại trừ khỏi gửi tự động):** Kênh ở trạng thái Không tiếp cận được bị tự động loại khỏi mọi chiến dịch gửi email/tin nhắn tự động.

  **Lý do nghiệp vụ:** Gửi liên tục vào địa chỉ hỏng làm các nhà cung cấp dịch vụ thư đánh giá thấp uy tín tên miền gửi thư của doanh nghiệp, kéo theo thư gửi cho khách thật cũng bị đưa vào thư rác.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-29.1.1` | Khách có 2 số di động, số thứ nhất là chính | Đặt số thứ hai làm chính | Số thứ hai là chính; số thứ nhất trở thành kênh phụ |
| `AC-29.2.1` | Một email vừa bị trả về vì không tồn tại | Mở hồ sơ | Email mang trạng thái Không tiếp cận được |
| `AC-29.3.1` | Tiếp nối AC-29.2.1, khách đang Đồng ý nhận tin | Một chiến dịch email tự động được gửi | Email đó không có trong danh sách nhận |

---

#### FEAT-30 — Đồng thuận Nhận tin, Mục đích Gửi tin & Định danh Dùng chung

**Mô tả nghiệp vụ:** Quản lý trạng thái đồng ý nhận tin tiếp thị theo từng kênh kèm bằng chứng, phân loại mục đích của mọi lượt gửi, và gắn nhãn Định danh dùng chung.

**Vai trò sử dụng chính:** Nhân viên và Quản lý Marketing (quản lý đồng thuận theo kênh), Nhân viên Kinh doanh (ghi nhận đồng thuận khi trao đổi trực tiếp), Nhân viên Hỗ trợ (hạ đồng thuận khi khách yêu cầu ngay trong vé/hội thoại đang mở — `BR-35.4` (a)), Quản lý Kinh doanh (trong phạm vi đơn vị), Quản trị viên.

**Điều kiện tiên quyết:** Hạ mức đồng thuận không cần quyền riêng ngoài việc tiếp cận được bản ghi (Nguyên tắc 3); nâng mức cần ô (Khách hàng, Ghi nhận đồng thuận) bao phủ bản ghi và bằng chứng theo `BR-30.10`.

**Luồng chính:**

1. Người dùng hoặc chính khách hàng thay đổi đồng thuận trên một kênh; hệ thống lưu bằng chứng không sửa được (`BR-30.3`).
2. Mọi lượt gửi khai báo nhóm mục đích; hệ thống áp chi phối của Từ chối nhận tin theo nhóm (`BR-30.5`, `BR-30.7`, `BR-30.9`).
3. Hệ thống cưỡng chế quy tắc nâng mức trên mọi nguồn tác động (`BR-30.10`).

**Quy tắc nghiệp vụ:**

- **`BR-30.1` (Đồng thuận theo từng kênh):** Trạng thái đồng thuận được lưu **độc lập cho từng kênh** (email, tin nhắn SMS, WhatsApp, Zalo ZNS — theo số điện thoại, Zalo OA — theo người quan tâm Tài khoản Chính thức, và các kênh tích hợp khác; kênh tích hợp khác luôn yêu cầu Đồng ý trước cho nhóm Tiếp thị cho tới khi được đưa vào chính sách gửi tiếp thị), nhận một trong ba giá trị: **Đồng ý nhận tin** (có bằng chứng theo `BR-30.3`), **Từ chối nhận tin**, **Chưa có đồng thuận**. **Chưa có đồng thuận** là giá trị mặc định của mọi kênh trên mọi đường tạo hồ sơ hoặc thêm kênh liên lạc (tạo tay, tạo từ hội thoại, từ biểu mẫu, từ tích hợp, nhập khẩu) khi không kèm bằng chứng Đồng ý; trên các kênh doanh nghiệp yêu cầu Đồng ý trước theo chính sách gửi tiếp thị ([`campaigns-srs.md`](./campaigns-srs.md) `BR-30.1`, `CFG-CAMP-74`, mặc định mọi kênh), nó chặn nhóm Tiếp thị giống Từ chối nhận tin; và được nâng lên Đồng ý nhận tin theo đúng quy tắc nâng mức của `BR-30.10`. Khách từ chối trên một kênh chỉ tác động lên kênh đó, trừ khi khách chọn **"Hủy nhận tin trên toàn bộ mọi kênh"**. Phạm vi tác động của Từ chối nhận tin quy định tại `BR-30.5` — chặn nhóm thư Tiếp thị, không chặn mọi loại thư.

  **Lý do nghiệp vụ:** Nhiều doanh nghiệp phải có sự đồng ý **trước** mới được gửi tiếp thị, và việc có yêu cầu hay không trên từng kênh là chính sách của doanh nghiệp; nếu chỉ có hai giá trị, mọi hồ sơ chưa ai hỏi ý kiến đều phải mang một trong hai nhãn sai — hoặc Đồng ý không có căn cứ, hoặc Từ chối trong khi khách chưa từng từ chối. Zalo ZNS và Zalo OA tách riêng vì người nhận có thể đồng ý nhận tin qua Tài khoản Chính thức mình đang quan tâm mà không đồng ý nhận tin theo số điện thoại, và ngược lại.

- **`BR-30.2` (Định danh dùng chung):** Một số điện thoại hoặc email được đánh dấu **Định danh dùng chung** (ví dụ số tổng đài, số lễ tân, email gia đình) để hệ thống không coi các khách hàng khác nhau dùng chung định danh đó là trùng lặp và không gợi ý gộp họ.

  **Lý do nghiệp vụ:** Gia đình, lễ tân và tổng đài dùng chung định danh là chuyện hằng ngày; không có nhãn này thì kiểm tra trùng lặp chặn sai và công cụ gộp gợi ý nhập hai người khác nhau làm một.

- **`BR-30.3` (Bằng chứng đồng thuận):** Mỗi lần trạng thái đồng thuận thay đổi, hệ thống bắt buộc lưu bộ bằng chứng **không sửa được** gồm: **(a)** thời điểm ghi nhận; **(b)** nguồn thu thập — **bắt buộc chọn từ A.8**, không nhập tự do; **(c)** nội dung điều khoản khách đã đồng ý (phiên bản văn bản đồng thuận tại thời điểm đó); **(d)** người hoặc tiến trình ghi nhận; **(e)** đồng ý đã được **xác nhận qua chính điểm đến** hay chưa (mã hoặc liên kết gửi tới đúng email/số điện thoại đó — [`campaigns-srs.md`](./campaigns-srs.md) `BR-09.3`). Bằng chứng được lưu **vĩnh viễn** kể cả khi khách đổi trạng thái nhiều lần, chỉ chịu ngoại lệ khử định danh tại `BR-33.8`.

  **Lý do nghiệp vụ:** Khi bị khiếu nại, doanh nghiệp phải chứng minh được cơ sở xử lý dữ liệu tại đúng thời điểm gửi tin; bằng chứng sửa được hoặc chỉ lưu trạng thái cuối cùng thì không chứng minh được gì.

- **`BR-30.4` (Đồng thuận qua nhập khẩu hàng loạt):** Dữ liệu nhập khẩu **không được** mặc định nhận trạng thái Đồng ý nhận tin. Lô chọn một cơ sở tạo Đồng ý nhận tin ("Khách hàng đã đăng ký trực tiếp", "Dữ liệu từ sự kiện có phiếu đồng ý") bắt buộc đính kèm tài liệu chứng minh nguồn gốc lô; lô vượt `CFG-30-03` bản ghi cần Người phụ trách Bảo vệ Dữ liệu chấp thuận trước khi Đồng ý có hiệu lực. Người nhập khẩu **bắt buộc chọn** Cơ sở đồng thuận cho lô từ A.11 — giao diện không cho bỏ trống. Danh mục có sẵn giá trị **"Không có cơ sở đồng thuận"** để khai báo trung thực; khi chọn giá trị này (hoặc cơ sở khai báo không đủ chứng minh đồng thuận tiếp thị), toàn bộ lô nhận **Chưa có đồng thuận** — lô chưa từng từ chối nên không được gắn Từ chối nhận tin; việc lô có nhận tiếp thị hay không do chính sách gửi tiếp thị của doanh nghiệp quyết định ([`campaigns-srs.md`](./campaigns-srs.md) `BR-30.1`, `CFG-CAMP-74`).

  **Lý do nghiệp vụ:** Danh bạ mua về hoặc thu thập không rõ nguồn là nguồn vi phạm đồng thuận phổ biến nhất; mặc định Đồng ý sẽ biến mỗi lần nhập khẩu thành một lần vi phạm hàng loạt.

- **`BR-30.5` (Phân loại mục đích gửi tin — phạm vi chi phối của Từ chối nhận tin) — sàn bắt buộc:** Mọi thư/tin nhắn gửi ra từ hệ thống bắt buộc thuộc **một** trong ba nhóm mục đích (A.12). Mọi phân hệ và kênh gửi tin phải khai báo nhóm mục đích cho từng lượt gửi. Trạng thái Từ chối nhận tin (kể cả "Hủy nhận tin trên toàn bộ mọi kênh") **chỉ chi phối nhóm Tiếp thị**:

| Nhóm mục đích | Nội dung thuộc nhóm | Chịu chi phối của Từ chối nhận tin? |
| --- | --- | :---: |
| **Tiếp thị & Quảng bá** | Bản tin định kỳ, chiến dịch khuyến mãi, thư nuôi dưỡng tự động, mời sự kiện thương mại, thư tái tiếp cận | **Có — chặn tuyệt đối** |
| **Giao dịch & Dịch vụ** | Phản hồi vé hỗ trợ, xác nhận đơn hàng, hóa đơn/nhắc thanh toán, thông báo bảo trì, cảnh báo bảo mật, thông báo pháp lý bắt buộc, Thông báo dịch vụ hàng loạt theo ([`campaigns-srs.md`](./campaigns-srs.md) `FEAT-45`), **thư báo giá, thư hợp đồng, thư xác nhận cuộc hẹn, thư trả lời một yêu cầu do chính khách hàng đưa ra** | Không |
| **Liên lạc 1-1 do nhân viên chủ động** | Email/tin nhắn nhân viên gửi trực tiếp trong quá trình phục vụ khách, thỏa **đồng thời cả bốn tiêu chí** tại `BR-30.7` | Không (mặc định), nhưng **bắt buộc ghi nhật ký** |

  **Cổng kiểm soát gửi Tiếp thị dùng chung (sàn bắt buộc):** mọi lượt gửi thuộc nhóm Tiếp thị & Quảng bá — dù phát ra từ phân hệ Chiến dịch, từ thao tác gửi thư cho danh sách hay lịch gửi tự động của phân hệ này, hay từ bất kỳ phân hệ nào khác — đều phải đi qua cùng một bộ kiểm tra tại thời điểm gửi đặc tả tại ([`campaigns-srs.md`](./campaigns-srs.md) `FEAT-44`): đồng thuận và bằng chứng, hạn chế xử lý, trạng thái tiếp cận, dấu vết chặn gửi, tạm chặn chờ xác nhận lời từ chối, Danh sách không quảng cáo, nhãn quảng cáo, khung giờ yên lặng, giới hạn 24 giờ và giới hạn tần suất, dừng khẩn cấp, căn cứ theo dõi hành vi, cấm trường nhạy cảm trong cá nhân hóa, loại tên thương hiệu SMS và loại mẫu tin, thông tin nhận diện người gửi và liên kết hủy nhận tin, ghi ngược hủy nhận tin, khiếu nại, điểm đến hỏng và chặn doanh nghiệp; và được đếm chung vào các giới hạn đó. Mọi kết quả được ghi vào sổ cái của cổng kèm căn cứ cho phép lượt gửi — bằng chứng Đồng ý, hoặc phiên bản chính sách khi kênh không yêu cầu Đồng ý trước ([`campaigns-srs.md`](./campaigns-srs.md) `BR-23.1`; hàng 13 của `BR-33.8`).

  Tenant **không được** cấu hình để nhóm Tiếp thị thoát khỏi chi phối của Từ chối nhận tin (sàn bắt buộc); tenant **được** cấu hình nhóm Liên lạc 1-1 có chịu chi phối hay không (Phụ lục B, `CFG-30-01`).

  **Lý do nghiệp vụ:** Nếu Từ chối nhận tin chặn tất cả, khách bấm "hủy nhận bản tin" hôm nay rồi mai gửi vé hỗ trợ sẽ không được trả lời — sự cố phục vụ khách xảy ra ngay tuần đầu; khách đang thương lượng hợp đồng không nhận được báo giá, và nhân viên sẽ gửi từ hộp thư cá nhân, đưa nội dung thương lượng ra khỏi hệ thống. Ngược lại, nếu không phân loại rõ, hệ thống sẽ gửi thư tiếp thị cho người đã từ chối.

- **`BR-30.6` (Trạng thái Hạn chế xử lý):** Khi khách yêu cầu hạn chế xử lý (`FEAT-33`), hồ sơ mang trạng thái **Hạn chế xử lý**, kèm lượt ghi nhận gồm thời điểm, người thực hiện và **nguồn** — "Yêu cầu trực tiếp của khách hàng" hay "Yêu cầu chưa xác minh được danh tính" (biện pháp phòng ngừa, `BR-33.7`), cùng danh mục nguồn với A.8:
  - **Dừng:** toàn bộ nhóm thư Tiếp thị; chấm điểm và suy giảm điểm (`FEAT-15`, `FEAT-16`); thăng hạng vòng đời **do ngưỡng điểm** (`BR-15.5`); thu hồi và phân bổ lại tự động (`BR-31.7`); **đồng hồ cam kết phản hồi lần đầu** (`BR-31.7`) — bản ghi vì vậy bị loại khỏi mẫu đo `KPI-03`; đưa vào danh sách phân khúc chiến dịch — trừ Thông báo dịch vụ mục đích thu hồi sản phẩm, cảnh báo an toàn và thông báo luật định theo ([`campaigns-srs.md`](./campaigns-srs.md) `FEAT-45`, `BR-45.4`).
  - **Vẫn chạy:** nhóm thư Giao dịch & Dịch vụ, để doanh nghiệp thực hiện nghĩa vụ hợp đồng — riêng Thông báo dịch vụ gửi hàng loạt, chỉ các mục đích thu hồi sản phẩm, cảnh báo an toàn và thông báo luật định ([`campaigns-srs.md`](./campaigns-srs.md) `BR-45.4`); và **các bước chuyển giai đoạn theo nguyên tắc 1** (`BR-12.2`, `BR-12.9`).
  - **Dỡ trạng thái:** khi **chính chủ thể dữ liệu rút lại yêu cầu qua kênh đã xác minh** theo `BR-33.7` — do **Quản trị viên** thực hiện một mình; hoặc theo nhánh dỡ sớm biện pháp phòng ngừa tại `BR-33.7` (b) — cần **Quản trị viên cùng Người phụ trách Bảo vệ Dữ liệu**; hoặc biện pháp phòng ngừa tự kết thúc khi hết hạn (`BR-33.7` (a), do hệ thống thực hiện) hay khi khách xác minh thành công (`BR-33.7` (e)) — trừ khi chính yêu cầu đã xác minh là yêu cầu Hạn chế xử lý, thì Hạn chế xử lý được giữ với lượt ghi nhận mới. Mọi nhánh ghi nhật ký (`NFR-07`) và làm đồng hồ cam kết chạy lại từ thời điểm dỡ.

  **Lý do nghiệp vụ:** Hệ thống không được thúc nhân viên liên hệ một người vừa yêu cầu hạn chế xử lý. Nguyên tắc 1 chỉ ghi nhận một thực tế thương mại đã xảy ra, không phải hoạt động xử lý dữ liệu cho mục đích tiếp thị — nếu dừng cả nhóm này, hồ sơ của một khách đang ký hợp đồng sẽ đứng sai giai đoạn. Thẩm quyền dỡ khác nhau theo nhánh vì nhánh thứ nhất làm đúng ý nguyện vừa được xác minh của chủ thể, còn nhánh thứ hai dỡ một biện pháp bảo vệ mà chủ thể chưa xác minh được danh tính. Không có đường dỡ thì một khách đổi ý bị đóng băng vĩnh viễn.

- **`BR-30.7` (Tiêu chí quan sát được của nhóm Liên lạc 1-1) — sàn bắt buộc:** Một lượt gửi chỉ thuộc nhóm Liên lạc 1-1 khi thỏa **đồng thời cả bốn** tiêu chí:
  - **(a)** do một người dùng thật thực hiện, không do tiến trình tự động hay lịch gửi;
  - **(b)** số người nhận **tối đa 5** trong một lượt gửi (Phụ lục B, `CFG-30-02`);
  - **(c)** **không** dùng **mẫu chiến dịch** do Marketing tạo trong công cụ chiến dịch — mẫu thư nghiệp vụ cá nhân do nhân viên hoặc đội kinh doanh soạn (thư theo dõi sau cuộc gọi, thư giới thiệu, thư hỏi lịch gặp) **vẫn được phép**;
  - **(d)** **không gửi theo lô cho toàn bộ một danh sách** — việc mở một danh sách hiển thị rồi chọn thủ công vài khách hàng cụ thể **vẫn thỏa** tiêu chí này.

  Lượt gửi không thỏa đủ bốn tiêu chí **bắt buộc thuộc nhóm Tiếp thị**, trừ nội dung Giao dịch & Dịch vụ nêu tại `BR-30.5`; trong đó, gửi **hàng loạt** một nội dung Giao dịch & Dịch vụ chỉ được qua Thông báo dịch vụ theo ([`campaigns-srs.md`](./campaigns-srs.md) `FEAT-45`) — danh mục mục đích đóng, cấm nội dung quảng bá, có phê duyệt của Người phụ trách Bảo vệ Dữ liệu. Thư báo giá, thư hợp đồng, thư xác nhận cuộc hẹn và thư trả lời yêu cầu của khách thuộc nhóm Giao dịch & Dịch vụ (`BR-30.5`), không xét theo bốn tiêu chí này và không chịu trần số người nhận.

  **Lý do nghiệp vụ:** Không có bộ tiêu chí quan sát được, một chiến dịch tiếp thị chỉ cần gửi từ tài khoản nhân viên là ra khỏi tầm chi phối của Từ chối nhận tin, biến cam kết "không thể ghi đè" tại `BR-19.6` thành hình thức.

- **`BR-30.8` (Giám sát nhóm Liên lạc 1-1):** Hằng tháng, hệ thống báo cáo cho Người phụ trách Bảo vệ Dữ liệu và Chủ sở hữu khối lượng thư nhóm Liên lạc 1-1 đã gửi tới các khách đang Từ chối nhận tin, chia theo người gửi; khối lượng vượt mức bất thường được cảnh báo để rà soát dấu hiệu lách quy tắc.

  **Lý do nghiệp vụ:** Tiêu chí của nhóm Liên lạc 1-1 quan sát được nhưng vẫn có thể bị lách bằng cách gửi nhiều lượt nhỏ; giám sát theo người gửi là cách phát hiện việc lách đó mà không chặn công việc thật.

- **`BR-30.9` (Mặc định an toàn khi thiếu khai báo nhóm) — sàn bắt buộc:** Mọi lượt gửi **không khai báo nhóm mục đích** được mặc định xếp vào **nhóm Tiếp thị**.

  **Lý do nghiệp vụ:** Đây là mặc định an toàn nhất về pháp lý, để cam kết tại `BR-19.6` có cơ chế cưỡng chế thật mà không phụ thuộc việc mọi kênh gửi tin đã khai báo nhóm đầy đủ hay chưa.

- **`BR-30.10` (Cưỡng chế đồng thuận trên mọi nguồn tác động) — sàn bắt buộc:** Bảng trường bị cưỡng chế tại `BR-18.2` chỉ điều chỉnh thao tác gộp; quy tắc này mở rộng cơ chế cưỡng chế ra mọi nguồn tác động còn lại:
  - **Nguyên tắc bất biến:** chỉ **hành vi của chính chủ thể dữ liệu** (tự đăng ký, tự bấm liên kết xác nhận, tự trả lời trên kênh của mình) hoặc **bằng chứng đồng thuận mới hợp lệ theo `BR-30.3`** mới **nâng** được từ Từ chối nhận tin hoặc Chưa có đồng thuận lên Đồng ý nhận tin. **Ngoại lệ duy nhất:** khi dỡ biện pháp phòng ngừa, hệ thống trả kênh về đúng trạng thái trước khi áp theo `BR-33.7` (e) — từ Từ chối về Đồng ý (dỡ sớm hoặc khách xác minh thành công: là căn cứ như trước khi áp; hết hạn: đánh dấu "tự khôi phục", chưa là căn cứ) hoặc về Chưa có đồng thuận — vì lời từ chối đó không phải của chủ thể.
  - **Nâng từ Từ chối nhận tin** — bất kể nguồn của lời từ chối, trừ ngoại lệ của biện pháp phòng ngừa nêu trên: chỉ được nâng lên Đồng ý khi **chính chủ** xác nhận qua liên kết hoặc mã gửi tới chính điểm đến đó; ghi nhận thủ công của nhân viên và nhập khẩu không nâng được ([`campaigns-srs.md`](./campaigns-srs.md) `BR-26.5`).
  - **Nhập khẩu theo chiến lược cập nhật (`BR-23.3`):** **không bao giờ** hạ mức nghiêm ngặt của bản ghi hiện hữu. Bản ghi đang Từ chối mà dòng nhập vào là Đồng ý thì giữ Từ chối, và dòng đó được ghi vào báo cáo kết quả với ghi chú "Không nâng được mức đồng thuận — thiếu bằng chứng của chủ thể".
  - **Chỉnh sửa thủ công** bởi người có ô (Khách hàng, Ghi nhận đồng thuận) bao phủ bản ghi — theo ma trận mặc định gồm Nhân viên Kinh doanh, Quản lý, Marketing, Quản lý Marketing — và bởi người đang xử lý vé/hội thoại theo `BR-35.4` (a) khi tiếp nhận yêu cầu của khách (chỉ hạ): được **hạ** xuống Từ chối nhận tin tự do; **nâng** từ **Chưa có đồng thuận** lên Đồng ý nhận tin bắt buộc kèm bằng chứng theo `BR-30.3` có **tài liệu chứng minh đính kèm** (phiếu đồng ý, bản ghi cuộc gọi, ảnh chụp biểu mẫu) với nguồn thu thập từ A.8 — không có bằng chứng thì giao diện không cho lưu.
  - **Hạ về Chưa có đồng thuận:** hệ thống hạ một kênh từ Đồng ý về Chưa có đồng thuận khi nhà mạng hoặc nền tảng báo số điện thoại đổi chủ ([`campaigns-srs.md`](./campaigns-srs.md) `BR-28.4`), với bằng chứng nguồn tương ứng trong A.8. Đồng ý được tự khôi phục khi dỡ biện pháp phòng ngừa vì hết hạn (`BR-33.7` (e)) được đánh dấu "tự khôi phục" để các phân hệ gửi tin nhận biết ([`campaigns-srs.md`](./campaigns-srs.md) `BR-32.1`); dấu này chỉ được gỡ khi **chính chủ** xác nhận lại qua liên kết hoặc mã gửi tới chính điểm đến — người có quyền sửa hồ sơ gửi được yêu cầu xác nhận lại đó, nhưng không tự gỡ dấu được.
  - Mọi lượt nâng mức đồng thuận, từ bất kỳ nguồn nào, đều được ghi nhật ký (`NFR-07`).

  **Lý do nghiệp vụ:** Không có quy tắc này, cam kết "Từ chối nhận tin không thể bị ghi đè" chỉ đúng với thao tác gộp, trong khi hai đường vào phổ biến hơn — nhập khẩu và sửa tay — vẫn hở.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-30.1.1` | Chị Mai Đồng ý nhận tin qua email, SMS và Zalo | Chị bấm liên kết "Hủy nhận tin" trong một email chiến dịch | Chỉ kênh email chuyển sang Từ chối nhận tin; SMS và Zalo vẫn Đồng ý |
| `AC-30.1.2` | Cùng bối cảnh | Chị chọn "Hủy nhận tin trên toàn bộ mọi kênh" | Cả ba kênh chuyển sang Từ chối nhận tin |
| `AC-30.1.3` | Nhân viên tạo tay hồ sơ từ danh thiếp, nhập email và số điện thoại, không kèm bằng chứng đồng ý | Mở phần đồng thuận của hồ sơ | Mọi kênh ở trạng thái Chưa có đồng thuận; với chính sách mặc định (mọi kênh yêu cầu Đồng ý trước), hồ sơ không nhận thư nhóm Tiếp thị |
| `AC-30.1.4` | Khách quan tâm Tài khoản Chính thức Zalo và Đồng ý nhận tin qua Zalo OA | Khách từ chối nhận tin trên Zalo OA | Chỉ kênh Zalo OA chuyển Từ chối nhận tin; kênh Zalo ZNS giữ nguyên trạng thái |
| `AC-30.2.1` | Hai khách hàng dùng chung số tổng đài đã được đánh dấu Định danh dùng chung | Quản trị viên chạy công cụ quét trùng lặp | Hai khách không bị xếp vào cụm trùng |
| `AC-30.3.1` | Tiếp nối AC-30.1.1 | Mở lịch sử đồng thuận của chị Mai | Có bằng chứng gồm thời điểm, nguồn "Liên kết Hủy nhận tin trong email", phiên bản điều khoản và tiến trình ghi nhận; không có thao tác sửa bằng chứng |
| `AC-30.3.2` | Chị Mai đổi trạng thái email ba lần trong năm | Mở lịch sử đồng thuận | Thấy đủ ba bộ bằng chứng |
| `AC-30.4.1` | Màn hình nhập khẩu | Bỏ trống Cơ sở đồng thuận, bấm bắt đầu | Không bắt đầu được; yêu cầu chọn từ A.11 |
| `AC-30.4.2` | Chọn "Không có cơ sở đồng thuận" | Chạy nhập | Toàn bộ lô nhận Chưa có đồng thuận (không gắn Từ chối nhận tin); báo cáo kết quả nêu rõ lô không nhận tiếp thị trên các kênh doanh nghiệp yêu cầu Đồng ý trước |
| `AC-30.5.1` | Chị Mai đang Từ chối nhận tin qua email | Chị gửi vé hỗ trợ và nhân viên phản hồi qua email | Thư phản hồi được gửi tới chị |
| `AC-30.5.2` | Cùng bối cảnh | Một bản tin định kỳ được gửi | Chị không nhận được |
| `AC-30.5.3` | Cùng bối cảnh | Nhân viên Kinh doanh gửi thư báo giá cho chị | Thư được gửi; không bị trần 5 người nhận |
| `AC-30.5.4` | Chủ sở hữu mở cấu hình mục đích gửi tin | Tìm lựa chọn cho nhóm Tiếp thị không chịu chi phối của Từ chối nhận tin | Không tồn tại lựa chọn này |
| `AC-30.6.1` | Khách đang ở Lead, được gắn Hạn chế xử lý | Khách mở email và nhấp liên kết; quan sát điểm, phân khúc, hạn phản hồi | Điểm không thay đổi; khách không thăng hạng; không xuất hiện trong danh sách phân khúc chiến dịch; không có hạn phản hồi đang chạy; bản ghi không thuộc mẫu đo `KPI-03` |
| `AC-30.6.2` | Tiếp nối AC-30.6.1 | Nhân viên gửi xác nhận đơn hàng cho khách | Thư được gửi |
| `AC-30.6.3` | Tiếp nối AC-30.6.1 | Một Cơ hội của khách được đóng Thắng | Giai đoạn vẫn lên Customer |
| `AC-30.6.4` | Tiếp nối AC-30.6.1 | Khách rút lại yêu cầu qua kênh đã xác minh; Quản trị viên dỡ trạng thái | Trạng thái được dỡ; nhật ký ghi nhận; đồng hồ cam kết chạy lại từ thời điểm dỡ |
| `AC-30.6.5` | Hạn chế xử lý đang áp do biện pháp phòng ngừa (chưa xác minh được danh tính) | Quản trị viên tìm cách dỡ một mình | Không dỡ được khi chưa có Người phụ trách Bảo vệ Dữ liệu cùng phê duyệt |
| `AC-30.6.6` | Biện pháp phòng ngừa áp cho yêu cầu Hạn chế xử lý chưa xác minh | Khách bổ sung xác minh thành công | Hạn chế xử lý được giữ, kèm lượt ghi nhận mới với nguồn "Yêu cầu trực tiếp của khách hàng"; biện pháp phòng ngừa kết thúc |
| `AC-30.7.1` | Nhân viên gửi thư theo dõi sau cuộc gọi, dùng mẫu thư cá nhân, cho 3 người nhận chọn tay | Gửi | Lượt gửi thuộc nhóm Liên lạc 1-1; có trong nhật ký |
| `AC-30.7.2` | Nhân viên gửi thư cá nhân cho 6 người nhận | Gửi | Lượt gửi thuộc nhóm Tiếp thị; người nhận đang Từ chối nhận tin bị loại khỏi danh sách nhận |
| `AC-30.7.3` | Nhân viên dùng một mẫu chiến dịch của Marketing gửi cho 1 khách | Gửi | Lượt gửi thuộc nhóm Tiếp thị |
| `AC-30.7.4` | Nhân viên mở danh sách "Khách chưa có hoạt động 30 ngày", chọn tay 3 khách | Gửi thư cá nhân | Lượt gửi thuộc nhóm Liên lạc 1-1 |
| `AC-30.7.5` | Nhân viên chọn "gửi cho toàn bộ danh sách" | Gửi | Lượt gửi thuộc nhóm Tiếp thị |
| `AC-30.7.6` | Một lịch gửi tự động được đặt sẵn | Lịch gửi chạy | Lượt gửi thuộc nhóm Tiếp thị |
| `AC-30.8.1` | Cuối tháng | Người phụ trách Bảo vệ Dữ liệu mở báo cáo Liên lạc 1-1 | Thấy khối lượng thư nhóm Liên lạc 1-1 tới khách đang Từ chối nhận tin, chia theo người gửi |
| `AC-30.9.1` | Một lượt gửi không khai báo nhóm mục đích, danh sách có khách đang Từ chối nhận tin | Gửi | Lượt gửi được xếp vào nhóm Tiếp thị; khách đang Từ chối không nhận được |
| `AC-30.10.1` | Khách đang Từ chối nhận tin qua email | Nhập khẩu theo chiến lược cập nhật với cột đồng thuận "Đồng ý" | Khách vẫn Từ chối; báo cáo kết quả có ghi chú "Không nâng được mức đồng thuận — thiếu bằng chứng của chủ thể" |
| `AC-30.10.2` | Cùng bối cảnh | Nhân viên Kinh doanh sửa tay lên Đồng ý nhận tin, không kèm bằng chứng | Không lưu được |
| `AC-30.10.3` | Cùng bối cảnh (khách đang Từ chối) | Nhân viên sửa lên Đồng ý nhận tin kèm bằng chứng, nguồn "Ghi nhận thủ công bởi nhân viên" | Không lưu được; giao diện đề nghị gửi yêu cầu xác nhận tới chính điểm đến của khách |
| `AC-30.10.5` | Kênh email của khách ở Chưa có đồng thuận | Nhân viên sửa lên Đồng ý kèm bằng chứng, nguồn "Ghi nhận thủ công bởi nhân viên", phiên bản điều khoản và ảnh chụp phiếu đồng ý đính kèm | Lưu được; nhật ký ghi lượt nâng mức |
| `AC-30.10.4` | Cùng bối cảnh | Khách tự bấm liên kết xác nhận đăng ký nhận tin | Kênh email chuyển sang Đồng ý nhận tin; bằng chứng được lưu |

---

#### FEAT-33 — Quyền Chủ thể Dữ liệu & Xử lý Yêu cầu Dữ liệu Cá nhân

**Mô tả nghiệp vụ:** Quy trình chuẩn để tiếp nhận và xử lý yêu cầu của khách hàng đối với dữ liệu cá nhân của chính họ, đáp ứng nghĩa vụ theo pháp luật bảo vệ dữ liệu cá nhân (bao gồm GDPR và quy định về bảo vệ dữ liệu cá nhân tại Việt Nam). Khác hoàn toàn với Thùng rác nội bộ (`FEAT-05`): Thùng rác phục vụ vận hành nội bộ và phục hồi được; xóa theo quyền chủ thể dữ liệu là nghĩa vụ pháp lý và **không thể phục hồi**.

**Vai trò sử dụng chính:** Quản trị viên và Chủ sở hữu (xử lý và thực thi xóa vĩnh viễn); Người phụ trách Bảo vệ Dữ liệu (giám sát, đồng phê duyệt); Nhân viên Hỗ trợ và Quản lý Kinh doanh (**tiếp nhận và ghi nhận** yêu cầu; **thực thi ngay** hai loại yêu cầu chỉ thu hẹp phạm vi xử lý và đảo lại được — gắn Hạn chế xử lý và hạ đồng thuận xuống Từ chối nhận tin; **không** thực thi ba loại còn lại và không đóng được yêu cầu).

**Các loại yêu cầu:**

| Loại yêu cầu | Nội dung nghiệp vụ | Thời hạn xử lý cam kết |
| --- | --- | --- |
| **Yêu cầu bản sao dữ liệu** | Xuất toàn bộ dữ liệu cá nhân của khách đang lưu trong hệ thống ra tệp có cấu trúc đọc được | **30 ngày** kể từ ngày tiếp nhận |
| **Yêu cầu chỉnh sửa** | Cập nhật thông tin cá nhân không chính xác theo đề nghị của khách | **15 ngày** |
| **Yêu cầu xóa vĩnh viễn** | Xóa vĩnh viễn dữ liệu cá nhân, không qua Thùng rác | **30 ngày** |
| **Yêu cầu hạn chế xử lý** | Giữ dữ liệu nhưng dừng mọi hoạt động tiếp thị và tự động hóa (`BR-30.6`) | **Tức thì** — người tiếp nhận (Nhân viên Hỗ trợ, Quản lý Kinh doanh) gắn trạng thái Hạn chế xử lý ngay tại bước ghi nhận |
| **Yêu cầu rút lại đồng thuận** | Chuyển toàn bộ kênh sang Từ chối nhận tin | **Tức thì** |

**Điều kiện tiên quyết:** Tiếp nhận cần quyền quản trị Tiếp nhận yêu cầu chủ thể dữ liệu (Mục 5.2) hoặc quyền đọc tự động trên bản ghi; thực thi bản sao, chỉnh sửa, xóa và đóng yêu cầu chỉ do Người có toàn quyền.

**Luồng chính:**

1. Người tiếp nhận ghi nhận yêu cầu thành bản ghi theo dõi (`BR-33.1`); với yêu cầu hạn chế xử lý và rút đồng thuận, thực thi ngay.
2. Người xử lý xác minh danh tính theo mức rủi ro (`BR-33.7`); không xác minh được thì hệ thống áp biện pháp phòng ngừa.
3. Người xử lý thực thi yêu cầu, áp ngoại lệ nghĩa vụ lưu trữ khi có (`BR-33.3`), và với yêu cầu xóa thì xử lý mọi nơi lưu (`BR-33.8`).
4. Hệ thống phát hành Biên bản Hoàn tất Xử lý; Người có toàn quyền đóng yêu cầu và phản hồi khách.

**Quy tắc nghiệp vụ:**

- **`BR-33.1` (Tiếp nhận & theo dõi):** Mỗi yêu cầu là một bản ghi theo dõi riêng, gắn với khách hàng liên quan, ghi: loại yêu cầu, ngày tiếp nhận, hạn xử lý, người xử lý, trạng thái (Đã tiếp nhận / Đang xử lý / Đã hoàn tất / Bị từ chối kèm lý do). Hệ thống cảnh báo khi yêu cầu sắp đến hạn. Chỉ Quản trị viên và Chủ sở hữu đóng được yêu cầu và phát hành phản hồi chính thức cho khách.

  **Lý do nghiệp vụ:** Nghĩa vụ có thời hạn chỉ thực hiện được khi mỗi yêu cầu có người chịu trách nhiệm, hạn và trạng thái rõ ràng; yêu cầu nằm trong hộp thư của một nhân viên thì không ai biết nó sắp quá hạn.

- **`BR-33.2` (Xóa vĩnh viễn có kiểm soát):** Chỉ Quản trị viên hoặc Chủ sở hữu thực hiện, bắt buộc xác nhận hai bước và **không qua Thùng rác**. Sau khi hoàn tất, hệ thống chỉ giữ một bản ghi tối thiểu chứng minh đã thực hiện nghĩa vụ (mã bản ghi đã xóa, loại yêu cầu, thời điểm hoàn tất, người thực hiện) — không chứa dữ liệu cá nhân.

  **Lý do nghiệp vụ:** Xóa theo quyền chủ thể không phục hồi được, nên phải do người có thẩm quyền cao nhất thực hiện với xác nhận hai bước; bản ghi tối thiểu là bằng chứng doanh nghiệp đã thực hiện nghĩa vụ mà không giữ lại chính dữ liệu đã xóa.

- **`BR-33.3` (Ngoại lệ nghĩa vụ lưu trữ):** Khách còn nghĩa vụ hợp đồng, hóa đơn hoặc tranh chấp pháp lý đang xử lý thì yêu cầu xóa bị **từ chối một phần** theo cơ sở "nghĩa vụ pháp lý phải lưu trữ": hệ thống xóa dữ liệu tiếp thị và dữ liệu liên hệ không cần thiết, giữ dữ liệu tối thiểu phục vụ nghĩa vụ pháp lý, và bắt buộc ghi rõ lý do từ chối một phần trong bản ghi theo dõi để phản hồi khách.

  **Lý do nghiệp vụ:** Quyền xóa của khách không xóa được nghĩa vụ lưu trữ hợp đồng và hóa đơn của doanh nghiệp; xóa tất cả làm doanh nghiệp vi phạm nghĩa vụ khác, còn từ chối tất cả làm doanh nghiệp vi phạm quyền của khách.

- **`BR-33.4` (Chặn xung đột với gộp):** Bản ghi đang có yêu cầu chủ thể dữ liệu chưa hoàn tất không được gộp (thống nhất `BR-19.6`).

  **Lý do nghiệp vụ:** Gộp trong lúc đang xử lý làm thay đổi tập dữ liệu mà yêu cầu nhắm tới; người xử lý không còn xác định được phần nào là của chủ thể đã yêu cầu.

- **`BR-33.5` (Chính sách lưu trữ dữ liệu không hoạt động):** Khách hàng không phát sinh tương tác nào trong **36 tháng** liên tục và không ở Customer/Evangelist được đưa vào danh sách đề xuất rà soát lưu trữ (Phụ lục B, `CFG-33-02`). Quản trị viên quyết định lưu trữ dài hạn hoặc xóa. Hệ thống **không tự động xóa** hồ sơ khách hàng đã định danh còn nằm ngoài Thùng rác — hành vi này **cố định**.

  **Sáu ngoại lệ có chủ đích**, mỗi ngoại lệ chỉ xóa hoặc khử phần định danh chứ không xóa giá trị kinh doanh, hoặc chỉ thực thi một quyết định xóa mà con người đã đưa ra:
  - **(a)** tự động khử định danh nhóm Định danh KYC khi hết thời hạn lưu (`BR-01.5b`);
  - **(b)** tự động xóa Hồ sơ Khách hàng Tạm chưa từng có nhân viên phản hồi (`BR-33.6`, nhánh thứ nhất);
  - **(c)** tự động khử định danh Hồ sơ Khách hàng Tạm khi chạm trần lưu tuyệt đối (`BR-33.6`);
  - **(d)** dọn dẹp Thùng rác quá thời hạn (`BR-05.4`) — dữ liệu mà con người đã quyết định xóa, chịu toàn bộ chốt an toàn tại `BR-05.6`;
  - **(e)** tự động xóa **tệp nhập khẩu gốc** khi hết thời hạn lưu (`BR-33.8`, `CFG-22-01`) và **tài liệu xác minh danh tính** thu theo `BR-33.7` sau 30 ngày kể từ khi yêu cầu hoàn tất (`BR-01.5b`) — tệp phục vụ một tiến trình đã kết thúc, không phải hồ sơ khách hàng.

  - **(f)** tự động khử định danh sổ cái người nhận chiến dịch, sự kiện tương tác gắn danh tính và thẻ gắn tự động từ tương tác khi hết thời hạn lưu của phân hệ Chiến dịch ([`campaigns-srs.md`](./campaigns-srs.md) `BR-32.3`) — dữ liệu hành vi phục vụ một đợt gửi đã kết thúc, không phải hồ sơ khách hàng.

  Ngoài sáu ngoại lệ này, không tiến trình nào được tự động xóa dữ liệu khách hàng.

  **Lý do nghiệp vụ:** Tự động xóa khách hàng "không hoạt động" theo một con số thời gian dễ xóa mất khách lớn có chu kỳ mua dài; quyết định xóa giá trị kinh doanh phải thuộc về con người (Mục 2.4, Nguyên tắc 4).

- **`BR-33.6` (Xử lý Hồ sơ Khách hàng Tạm tồn dư):** Hồ sơ Tạm không được bổ sung email hoặc số điện thoại trong **90 ngày** kể từ tương tác gần nhất (Phụ lục B, `CFG-33-01`):
  - **Chưa từng có nhân viên phản hồi** → tự động xóa vĩnh viễn cùng nội dung hội thoại vãng lai.
  - **Đã có tương tác của nhân viên** (đã được phản hồi, đã ghi nhận cam kết hoặc khiếu nại) → **không** tự động xóa; chuyển vào danh sách rà soát thủ công của Quản trị Chất lượng Dữ liệu.
  - **Trần lưu tuyệt đối — sàn bắt buộc:** danh sách rà soát không được tồn đọng vô hạn. Sau **18 tháng** kể từ tương tác gần nhất (Phụ lục B, `CFG-33-03`), nếu vẫn chưa có quyết định, hệ thống **tự động khử định danh**: xóa định danh thiết bị/kênh chat và mọi dữ liệu nhận diện, giữ nội dung hội thoại ở dạng vô danh.
  - **Sàn cho nội dung hội thoại:** nội dung hội thoại vãng lai được giữ theo chính sách lưu trữ của Hộp thư Đa kênh, nhưng phân hệ đó **không được** lưu định danh của khách vãng lai lâu hơn trần 18 tháng nêu trên.

  **Lý do nghiệp vụ:** Hồ sơ Tạm là dữ liệu thu thập không có hành vi đăng ký chủ động của khách, nên không được lưu định danh vô thời hạn. Nhưng khiếu nại thực tế thường phát sinh sau vài tháng; xóa ở ngày thứ 91 hồ sơ đã có trao đổi với nhân viên thì doanh nghiệp mất bằng chứng.

- **`BR-33.7` (Xác minh danh tính chủ thể dữ liệu — phân tầng theo rủi ro) — sàn bắt buộc:** Mức xác minh tương ứng với mức độ không thể phục hồi của thao tác:

| Loại yêu cầu | Mức xác minh bắt buộc | Lý do |
| --- | --- | --- |
| **Rút lại đồng thuận**, **Hạn chế xử lý** | Xác nhận trên chính kênh khách đang liên hệ (trả lời đúng phiên hội thoại, bấm liên kết trong chính email/tin nhắn đã gửi tới khách, hoặc xác nhận trong phiên chat đang mở) | Chỉ làm giảm mức xử lý dữ liệu, không mất dữ liệu, và pháp luật cam kết xử lý tức thì; đòi xác minh nặng là chặn quyền chính đáng của khách |
| **Bản sao dữ liệu**, **Chỉnh sửa** | Một trong: kênh liên lạc **đã xác thực** của hồ sơ · giấy tờ định danh đối chiếu KYC · xác nhận của người đại diện hợp đồng · xác nhận hai yếu tố qua chính kênh chat/định danh thiết bị đối với Hồ sơ Khách hàng Tạm | Có rủi ro tiết lộ dữ liệu cho người không phải chủ thể |
| **Xóa vĩnh viễn** | Như trên, **cộng thêm**: xác nhận hai bước trên giao diện (`BR-33.2`), và với khách ở Customer/Evangelist phải có **hai người khác nhau** (người xác minh và người phê duyệt xóa) | Không thể phục hồi; thiếu bước này thì một email mạo danh là đủ xóa sạch hồ sơ một khách đang trả tiền |

  **Khi không xác minh được — biện pháp phòng ngừa thay cho từ chối:** hệ thống **không** từ chối rồi bỏ mặc, mà áp **biện pháp phòng ngừa tạm thời**: chuyển các kênh chưa ở Từ chối nhận tin sang Từ chối nhận tin cho nhóm Tiếp thị (kênh đã Từ chối từ trước giữ nguyên bằng chứng và nguồn cũ) và gắn Hạn chế xử lý (`BR-30.6`), đồng thời phản hồi nêu rõ cần bổ sung gì để thực hiện được yêu cầu. Biện pháp phòng ngừa chịu sáu ràng buộc:
  - **(a) Thời hạn:** tối đa **30 ngày** (Phụ lục B, `CFG-33-04`). Hết thời hạn mà khách không bổ sung xác minh, biện pháp phòng ngừa tự động được dỡ và yêu cầu đóng với lý do "Không xác minh được danh tính".
  - **(b) Thẩm quyền dỡ sớm:** Quản trị viên **cùng** Người phụ trách Bảo vệ Dữ liệu (hoặc người thứ hai theo `NFR-14`), khi xác định người yêu cầu không phải chủ thể dữ liệu hoặc khi khách xác nhận không có yêu cầu nào. Việc dỡ được ghi nhật ký.
  - **(c) Bắt buộc thông báo:** Người phụ trách bản ghi được thông báo khi biện pháp phòng ngừa được áp, nêu rõ lý do và thời hạn.
  - **(d) Ghi nhận đúng bản chất:** bằng chứng đồng thuận cho lượt hạ mức này dùng nguồn **"Yêu cầu chưa xác minh được danh tính"** (A.8), **không** ghi là "Yêu cầu trực tiếp của khách hàng".
  - **(e) Trả về đúng trạng thái trước khi áp:** khi biện pháp kết thúc — hết hạn, dỡ sớm theo (b), hoặc khách bổ sung xác minh thành công — **chỉ** những kênh mà trạng thái hiện tại vẫn là lượt hạ mức của chính biện pháp này (nguồn "Yêu cầu chưa xác minh được danh tính") được trả về đúng trạng thái ngay trước khi áp: Đồng ý (dỡ sớm hoặc xác minh thành công: là căn cứ; hết hạn: đánh dấu "tự khôi phục", `BR-30.10`) hoặc Chưa có đồng thuận, với nguồn "Tự khôi phục khi dỡ biện pháp phòng ngừa" (A.8), tham chiếu tới bằng chứng gốc, và giữ nguyên mọi thuộc tính của trạng thái trước khi áp — phiên bản điều khoản (`BR-30.3` (c)), việc đã hay chưa xác nhận qua điểm đến (`BR-30.3` (e)), và dấu "tự khôi phục" nếu có; lần khôi phục không bao giờ biến một Đồng ý vốn chưa là căn cứ thành căn cứ. Kênh có bất kỳ thay đổi đồng thuận nào khác trong thời gian áp — chính chủ từ chối hay xác nhận Đồng ý mới, tư vấn viên ghi nhận từ chối, khiếu nại thư rác, người nhận chặn doanh nghiệp, số điện thoại đổi chủ ([`campaigns-srs.md`](./campaigns-srs.md) `BR-28.4`) — giữ trạng thái mới nhất đó. Khi khách xác minh thành công, yêu cầu được xử lý theo đúng nội dung của nó: hệ thống **tạo một lượt ghi nhận mới** cho mỗi lượt hạ mức và Hạn chế xử lý thuộc phạm vi yêu cầu, với nguồn "Yêu cầu trực tiếp của khách hàng" — lượt cũ mang nguồn "Yêu cầu chưa xác minh được danh tính" giữ nguyên, không bị sửa (`BR-30.3`) — và trạng thái hiện tại của các kênh đó từ nay xuất phát từ lượt mới; (e) chỉ áp cho các kênh yêu cầu đó không tác động tới.
  - **(f) Không hoàn tác gộp trong thời gian áp:** bản ghi đang chịu biện pháp phòng ngừa, hoặc đã gộp với một bản ghi đang chịu, không hoàn tác gộp được (`BR-20.1`) cho tới khi biện pháp kết thúc, để trạng thái do biện pháp đặt ra không bị chép sang một bản ghi không còn gắn với yêu cầu. Thời hạn hoàn tác và việc bảo vệ bản ghi phụ khỏi dọn dẹp vĩnh viễn được **kéo dài thêm** đúng bằng thời gian bị chặn (`BR-20.3`), để quyền hoàn tác không mất vì biện pháp này.

  **Lý do nghiệp vụ:** Khi mọi phương thức xác minh đều bất khả (Hồ sơ Tạm không có kênh xác thực; khách cá nhân chỉ có một số điện thoại chưa xác thực), từ chối trắng sẽ biến quy tắc chống mạo danh thành quy tắc từ chối có hệ thống quyền của đúng nhóm dữ liệu rủi ro nhất. Ngược lại, nếu biện pháp phòng ngừa là vĩnh viễn và không ai dỡ được, một email mạo danh là đủ để rút một khách đang trả tiền khỏi mọi chiến dịch mãi mãi — và ở quy mô lớn có thể bị dùng để đóng băng cả một tập khách hàng. Ghi sai nguồn bằng chứng là ghi sai sự thật vào chính kho chứng cứ dùng khi bị khiếu nại.

- **`BR-33.8` (Phạm vi xóa & ngoại lệ sổ cái):** Xóa vĩnh viễn theo quyền chủ thể dữ liệu phải xử lý dứt điểm **mọi nơi lưu dữ liệu cá nhân**:

| # | Nơi lưu dữ liệu | Xử lý bắt buộc |
| --- | --- | --- |
| 1 | Hồ sơ khách hàng, các kênh liên lạc, thẻ phân loại, liên kết doanh nghiệp | **Xóa vĩnh viễn** |
| 2 | Dòng thời gian, ghi chú, hoạt động gắn với khách hàng | **Xóa vĩnh viễn** phần nội dung chứa dữ liệu cá nhân |
| 3 | Ảnh chụp dữ liệu trong Sổ cái Gộp (`BR-19.3`) | **Khử định danh** — xóa dữ liệu cá nhân trong ảnh chụp, giữ cấu trúc sổ cái và mã bản ghi |
| 4 | Tệp xuất dữ liệu còn hiệu lực tải về (`BR-25.2`) | **Thu hồi đường tải, hủy tệp** |
| 5 | Nhật ký kiểm toán (`NFR-07`, `NFR-08`) | **Giữ ở dạng đã khử định danh** — chỉ còn mã bản ghi và loại thao tác (nghĩa vụ pháp lý phải lưu). Nhật ký vốn không lưu giá trị thật của trường nhạy cảm (`NFR-07`) nên phần phải khử là tối thiểu |
| 6 | Bằng chứng đồng thuận (`BR-30.3`) | **Giữ ở dạng tối thiểu** — thời điểm, nguồn thu thập, phiên bản điều khoản; xóa dữ liệu nhận diện cá nhân |
| 7 | Bản sao lưu hệ thống (`NFR-10`) | **Không phục hồi lại bản ghi đã xóa theo quyền chủ thể dữ liệu.** Bản sao lưu cuốn vòng trong **35 ngày** rồi tự hết hiệu lực; trong thời gian đó dữ liệu không truy cập được bằng nghiệp vụ. Nếu phải phục hồi hệ thống từ bản sao lưu, quy trình phục hồi bắt buộc chạy lại danh sách yêu cầu xóa đã hoàn tất |
| 8 | Tệp nhập khẩu gốc do người dùng tải lên (`BR-22.1`) | **Xóa vĩnh viễn.** Ngoài ra tệp nhập khẩu gốc có thời hạn lưu tối đa **30 ngày** kể từ khi tiến trình nhập hoàn tất, sau đó tự động xóa bất kể có yêu cầu hay không (Phụ lục B, `CFG-22-01`) |
| 9 | Tệp báo cáo lỗi nhập khẩu (`BR-24.2`) | **Xóa vĩnh viễn và thu hồi đường tải** |
| 10 | Nhật ký xuất dữ liệu (`BR-25.3`) | Giữ ở dạng đã khử định danh; là căn cứ liệt kê tập trường và tập bản ghi của khách từng được xuất ra ngoài |
| 11 | Nội dung hội thoại đa kênh — thuộc [`omnichat-srs.md`](./omnichat-srs.md) | **Khử định danh hoặc xóa** toàn bộ nội dung hội thoại gắn với khách, gồm tệp đính kèm. **Sàn tối thiểu đặt cho tài liệu đó:** Hộp thư Đa kênh thực thi được quyền xóa theo khách hàng trên mọi kênh trong một lần thao tác, hoàn tất trong cùng thời hạn của yêu cầu và trả về xác nhận để đưa vào Biên bản Hoàn tất Xử lý — sàn này được thỏa bởi `BR-23.3` và `BR-23.1` của `omnichat-srs.md`. Phần dữ liệu đang bị **Tạm dừng xóa theo yêu cầu pháp lý** (`BR-23.7` của `omnichat-srs.md`) mang trạng thái **Đang tạm dừng theo yêu cầu pháp lý** trong Biên bản, và yêu cầu xóa **giữ ở trạng thái chưa hoàn tất** cho tới khi tạm dừng được gỡ |
| 12 | Nội dung Vé hỗ trợ và tệp đính kèm — thuộc [`tickets-srs.md`](./tickets-srs.md) | **Khử định danh** nội dung vé (giữ dữ liệu thống kê vận hành như thời gian xử lý, phân loại) và **xóa tệp đính kèm** do khách gửi. **Sàn tối thiểu đặt cho tài liệu đó:** như hàng 11. Khi phân hệ Vé hỗ trợ chưa đặc tả quy tắc thực thi quyền xóa theo khách hàng thỏa sàn này, **Biên bản Hoàn tất Xử lý bắt buộc nêu rõ nội dung Vé hỗ trợ nằm ngoài phạm vi được bảo đảm** |
| 13 | Sổ cái người nhận chiến dịch, sổ cái của cổng kiểm soát gửi tiếp thị dùng chung, vị trí trong chuỗi nuôi dưỡng, bản ghi tạm chặn chờ xác nhận lời từ chối, bản ghi tạm ngừng do lỗi hộp thư lặp lại, tệp xuất sổ cái còn hiệu lực, nhật ký kiểm toán của phân hệ Chiến dịch, ánh xạ liên kết hủy nhận tin, bản ghi Từ chối Thông báo dịch vụ không thiết yếu — thuộc [`campaigns-srs.md`](./campaigns-srs.md) | **Khử định danh** dòng sổ cái (xóa điểm đến, liên kết tới hồ sơ, thông điệp lỗi gốc; giữ trạng thái và số liệu tổng hợp), đưa khách ra khỏi mọi chuỗi nuôi dưỡng, ngắt ánh xạ của liên kết theo dõi; xóa bản ghi Từ chối Thông báo dịch vụ không thiết yếu cùng hồ sơ — khách đã xóa không còn là người nhận được ([`campaigns-srs.md`](./campaigns-srs.md) `BR-32.2`, `BR-45.8`) |
| 14 | Dấu vết chặn gửi của mọi điểm đến của khách (yêu cầu xóa được coi là rút đồng thuận tiếp thị) — thuộc [`campaigns-srs.md`](./campaigns-srs.md) | **Giữ ở dạng không đọc ngược được** theo nghĩa vụ tôn trọng lời từ chối ([`campaigns-srs.md`](./campaigns-srs.md) `BR-32.4`); không gắn thông tin nào khác về khách |

  **Biên bản Hoàn tất Xử lý:** thay cho mọi tuyên bố "đã xóa xong", hệ thống phát hành một biên bản liệt kê **từng hàng** của bảng trên kèm trạng thái — **Đã xóa / Đã khử định danh / Đã thu hồi / Được giữ theo nghĩa vụ pháp lý / Đang tạm dừng theo yêu cầu pháp lý** — và số lượng đối tượng đã xử lý ở mỗi hàng. Trạng thái "Đang tạm dừng theo yêu cầu pháp lý" khác "Được giữ theo nghĩa vụ pháp lý" ở chỗ đây là **hoãn có điều kiện**: yêu cầu sẽ được thi hành lại khi tạm dừng được gỡ.

  **Nội dung văn bản tự do** (ghi chú, dòng thời gian): hệ thống liệt kê **danh sách hữu hạn** các mục gắn với khách hàng và người xử lý xác nhận từng mục theo một trong hai hành động — **ẩn toàn bộ mục** hoặc **thay nội dung bằng ghi chú vô danh**. Hệ thống **không** tự động nhận diện "phần nào là dữ liệu cá nhân" trong văn bản tự do.

  Các tuyên bố **"lưu vĩnh viễn"** (`NFR-05`, `BR-30.3`) và **"không sửa được / không được xóa"** (`NFR-07`, `NFR-08`, `BR-30.3`) đều được đọc kèm ngoại lệ khử định danh của quy tắc này — ngoại lệ duy nhất, do Quản trị viên cùng Người phụ trách Bảo vệ Dữ liệu thực hiện, và bản thân thao tác khử định danh được ghi một bản ghi nhật ký (`NFR-08`).

  **Lý do nghiệp vụ:** Doanh nghiệp không được trả lời khách "đã xóa xong" trong khi dữ liệu vẫn còn ở nơi khác. Mệnh đề "không còn dữ liệu ở bất kỳ đâu" không kiểm chứng được, nên cam kết đúng là một biên bản từng nơi lưu, nói rõ cả phần chưa được bảo đảm. Tự động nhận diện dữ liệu cá nhân trong văn bản tự do cho kết quả khác nhau giữa hai lần chạy, nên quyết định phải thuộc về con người.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-33.1.1` | Nhân viên Hỗ trợ nhận email yêu cầu xóa dữ liệu của khách | Ghi nhận yêu cầu | Bản ghi theo dõi được tạo với loại "Xóa vĩnh viễn", hạn 30 ngày; Nhân viên Hỗ trợ không thấy hành động thực thi xóa hay đóng yêu cầu |
| `AC-33.1.2` | Yêu cầu còn 3 ngày tới hạn | Quan sát | Người xử lý nhận cảnh báo sắp đến hạn |
| `AC-33.2.1` | Quản trị viên thực thi xóa vĩnh viễn một khách ở Lead đã được xác minh | Xác nhận hai bước | Hồ sơ không còn tồn tại, không xuất hiện trong Thùng rác; còn một bản ghi tối thiểu gồm mã bản ghi, loại yêu cầu, thời điểm, người thực hiện |
| `AC-33.3.1` | Khách ở Customer còn một hợp đồng hiệu lực yêu cầu xóa | Quản trị viên xử lý | Hệ thống từ chối một phần: xóa dữ liệu tiếp thị và kênh liên lạc không cần thiết, giữ dữ liệu tối thiểu phục vụ hợp đồng; bản ghi theo dõi có lý do từ chối một phần |
| `AC-33.4.1` | Khách có yêu cầu chỉnh sửa chưa hoàn tất | Quản trị viên tìm cách gộp khách với một bản ghi trùng | Bị chặn cho tới khi yêu cầu hoàn tất |
| `AC-33.5.1` | Khách ở Lead không có tương tác 37 tháng, thời hạn rà soát 36 tháng | Tiến trình chạy | Khách không bị xóa; có trong danh sách đề xuất rà soát lưu trữ |
| `AC-33.5.2` | Chủ sở hữu tìm trong toàn bộ cấu hình | Tìm lựa chọn "tự động xóa khi hết thời hạn rà soát" | Không tồn tại ở bất kỳ mức phân quyền nào |
| `AC-33.6.1` | Hồ sơ Tạm chưa có nhân viên phản hồi, 91 ngày không tương tác, thời hạn 90 ngày | Tiến trình chạy | Hồ sơ và nội dung hội thoại vãng lai bị xóa vĩnh viễn |
| `AC-33.6.2` | Hồ sơ Tạm đã có nhân viên phản hồi, 91 ngày không tương tác | Tiến trình chạy | Không bị xóa; có trong danh sách rà soát thủ công |
| `AC-33.6.3` | Tiếp nối AC-33.6.2, không ai quyết định, đã 18 tháng + 1 ngày kể từ tương tác gần nhất | Tiến trình chạy | Định danh thiết bị/kênh chat và dữ liệu nhận diện bị xóa; nội dung hội thoại còn ở dạng vô danh |
| `AC-33.7.1` | Khách trong phiên chat đang mở yêu cầu hạn chế xử lý | Nhân viên Hỗ trợ ghi nhận và khách xác nhận trong chính phiên chat | Hạn chế xử lý có hiệu lực ngay |
| `AC-33.7.2` | Một email từ địa chỉ không khớp hồ sơ yêu cầu xóa dữ liệu của một khách ở Customer | Quản trị viên cố thực thi xóa | Bị chặn vì chưa có phương thức xác minh hợp lệ; hệ thống áp biện pháp phòng ngừa: Từ chối nhận tin nhóm Tiếp thị và Hạn chế xử lý; Người phụ trách nhận thông báo kèm lý do và thời hạn; bằng chứng đồng thuận ghi nguồn "Yêu cầu chưa xác minh được danh tính" |
| `AC-33.7.3` | Khách ở Customer đã được xác minh qua email đã xác thực của hồ sơ | Cùng một Quản trị viên vừa xác minh vừa phê duyệt xóa | Không thực hiện được; cần một người thứ hai phê duyệt xóa |
| `AC-33.7.4` | Tiếp nối AC-33.7.2, khách không bổ sung xác minh | Qua 30 ngày | Biện pháp phòng ngừa tự động được dỡ; yêu cầu đóng với lý do "Không xác minh được danh tính" |
| `AC-33.7.5` | Tiếp nối AC-33.7.2, xác định người gửi không phải chủ thể | Quản trị viên và Người phụ trách Bảo vệ Dữ liệu cùng dỡ sớm | Biện pháp được dỡ; nhật ký ghi hai người thực hiện; kênh vốn Đồng ý trở về Đồng ý là căn cứ, không mang dấu "tự khôi phục" |
| `AC-33.7.6` | Trước khi áp biện pháp phòng ngừa, email của K ở Chưa có đồng thuận, SMS ở Đồng ý | Biện pháp hết hạn và được dỡ | Email về Chưa có đồng thuận, SMS về Đồng ý đánh dấu "tự khôi phục"; cả hai ghi nguồn "Tự khôi phục khi dỡ biện pháp phòng ngừa" |
| `AC-33.7.7` | Trong thời gian áp biện pháp, chính K bấm liên kết hủy nhận tin trên email | Biện pháp được dỡ | Email giữ Từ chối nhận tin; các kênh khác trở về trạng thái trước khi áp |
| `AC-33.7.8` | Trong thời gian áp biện pháp, nhà mạng báo số của K đổi chủ | Biện pháp hết hạn | SMS của K ở Chưa có đồng thuận (theo lượt đổi chủ), không trả về Đồng ý |
| `AC-33.7.9` | Trong thời gian áp biện pháp, chính K bấm liên kết xác nhận Đồng ý email mới | Biện pháp hết hạn | Email giữ Đồng ý mới là căn cứ, không bị trả về trạng thái cũ |
| `AC-33.7.10` | Email của K đã Từ chối từ trước khi áp biện pháp | Áp biện pháp, rồi hồ sơ K bị xóa vĩnh viễn khỏi Thùng rác trong thời gian áp | Email giữ nguồn từ chối cũ; dấu vết chặn gửi được tạo ([`campaigns-srs.md`](./campaigns-srs.md) `BR-32.4`) |
| `AC-33.7.11` | Bản ghi đang chịu biện pháp phòng ngừa | Quản trị viên thử hoàn tác gộp | Không cho phép cho tới khi biện pháp kết thúc |
| `AC-33.7.13` | Email của K mang dấu "tự khôi phục" sau khi biện pháp hết hạn | Nhân viên có quyền sửa hồ sơ mở phần đồng thuận | Kênh hiển thị "Đồng ý — tự khôi phục, cần chính chủ xác nhận lại"; có thao tác "Gửi yêu cầu xác nhận lại"; không có thao tác tự gỡ dấu |
| `AC-33.7.14` | Tiếp nối AC-33.7.13; nhân viên gửi yêu cầu xác nhận lại | K bấm liên kết trong email xác nhận | Dấu "tự khôi phục" được gỡ; nhật ký ghi hành vi của chủ thể |
| `AC-33.7.15` | Trước khi áp biện pháp, SMS của K có Đồng ý qua biểu mẫu chưa xác nhận qua điểm đến | Khách xác minh thành công, biện pháp kết thúc | SMS trở về Đồng ý vẫn ở trạng thái chưa xác nhận qua điểm đến |
| `AC-33.7.17` | K có Đồng ý SMS là căn cứ; biện pháp phòng ngừa áp vì yêu cầu xin bản sao chưa xác minh | K bổ sung xác minh thành công | Yêu cầu được xử lý; biện pháp kết thúc; SMS trở về Đồng ý là căn cứ, không mang dấu "tự khôi phục" |
| `AC-33.7.20` | Biện pháp phòng ngừa áp cho yêu cầu rút đồng thuận email chưa xác minh | Khách bổ sung xác minh thành công | Email có lượt ghi nhận mới Từ chối nhận tin nguồn "Yêu cầu trực tiếp của khách hàng"; lượt cũ nguồn "Yêu cầu chưa xác minh được danh tính" vẫn còn nguyên và xem được trong kho bằng chứng |
| `AC-33.8.1` | Hoàn tất xóa theo quyền chủ thể cho một khách có dữ liệu ở đủ 14 nơi lưu | Mở Biên bản Hoàn tất Xử lý | Biên bản có đủ 14 hàng, mỗi hàng có trạng thái và số lượng đối tượng đã xử lý; không có câu "đã xóa xong" |
| `AC-33.8.2` | Tiếp nối AC-33.8.1 | Dùng lại đường tải một tệp xuất và một tệp báo cáo lỗi nhập khẩu còn hạn của khách | Cả hai bị từ chối |
| `AC-33.8.3` | Tiếp nối AC-33.8.1 | Mở ảnh chụp trong sổ cái gộp liên quan tới khách | Còn cấu trúc và mã bản ghi, không còn dữ liệu cá nhân |
| `AC-33.8.4` | Một phần hội thoại của khách đang bị Tạm dừng xóa theo yêu cầu pháp lý | Hoàn tất các phần còn lại | Hàng hội thoại trong biên bản mang trạng thái "Đang tạm dừng theo yêu cầu pháp lý" kèm căn cứ; yêu cầu xóa vẫn ở trạng thái chưa hoàn tất |
| `AC-33.8.5` | Phân hệ Vé hỗ trợ chưa có quy tắc thực thi quyền xóa theo khách hàng | Mở biên bản | Hàng Vé hỗ trợ nêu rõ nội dung vé nằm ngoài phạm vi được bảo đảm; biên bản im lặng về hàng này là không đạt |
| `AC-33.8.6` | Khách có 7 ghi chú | Người xử lý xử lý phần văn bản tự do | Hệ thống liệt kê đúng 7 ghi chú; mỗi ghi chú phải được chọn "ẩn toàn bộ" hoặc "thay bằng ghi chú vô danh" |
| `AC-33.8.7` | Hệ thống phải phục hồi từ bản sao lưu chụp trước thời điểm xóa | Chạy quy trình phục hồi | Khách đã xóa không xuất hiện trở lại sau phục hồi |

**Tham chiếu:** [ADR-0008](../docs/adr/0008-data-subject-deletion-contract-omnichat-tickets.md) — hợp đồng xóa dữ liệu theo quyền chủ thể với Hộp thư Đa kênh và Vé hỗ trợ.

---

### Nhóm K — Quyền phụ trách, Cộng tác & Ghi nhận Hoạt động

#### FEAT-34 — Chuyển giao Quyền phụ trách & Bàn giao khi Thay đổi Nhân sự

**Mô tả nghiệp vụ:** Chuyển Người phụ trách của một hoặc hàng loạt khách hàng sang nhân viên khác, và bảo đảm không bản ghi nào trở thành vô chủ khi một nhân viên rời tổ chức, chuyển bộ phận, nghỉ phép hoặc vắng mặt dài.

**Vai trò sử dụng chính:** Chính người dùng — bất kể vai trò — (tự khai báo nghỉ phép và người xử lý thay), Người phụ trách hiện tại (đề nghị chuyển cho một người cụ thể, trả về hàng đợi), quản lý trực tiếp, người có ô Gán bao phủ bản ghi (chuyển giao), người thực hiện tạm ngưng hoặc quy trình rời workspace theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-42`, `FEAT-43`.

**Bối cảnh nghiệp vụ:** Nhân viên nghỉ việc là sự kiện hằng tháng ở mọi đội kinh doanh. Vì phạm vi truy cập bị giới hạn theo `BR-01.4`, danh bạ của nhân viên rời đi sẽ thành vùng chết nếu không có công cụ bàn giao: đồng nghiệp không thấy, Quản lý chỉ thấy trong phạm vi đơn vị, và tổ chức buộc phải cấp quyền xem toàn bộ cho tất cả để chữa cháy — phá vỡ mô hình phân quyền tại Mục 5.

**Điều kiện tiên quyết:** Người thực hiện và người nhận theo `BR-34.1`, riêng đường đề nghị theo `BR-34.1b`; quy trình tạm ngưng, rời workspace và chuyển phòng theo `BR-34.4`, `BR-34.9`.

**Luồng chính:**

1. Người có quyền chọn một hoặc nhiều bản ghi, chọn người nhận và phạm vi thực thể con, xem trước tác động rồi xác nhận (`BR-34.1` – `BR-34.3`).
2. Khi một thành viên bị tạm ngưng, lượt tạm ngưng có hiệu lực ngay; sau đó người thực hiện chọn giữ nguyên, chuyển tạm hoặc trả về hàng đợi cho khách hàng của người đó (`BR-34.4`).
3. Khi một thành viên rời workspace, bước "Bản ghi đang phụ trách" của quy trình rời workspace gọi tới chuyển giao theo tính năng này; chưa xong thì chưa gỡ được thành viên (`BR-34.4`).
4. Người dùng tự khai báo nghỉ phép kèm người xử lý thay; hệ thống tự áp trạng thái không khả dụng khi không đăng nhập quá lâu (`BR-34.6`).

**Quy tắc nghiệp vụ:**

- **`BR-34.1` (Chuyển giao đơn lẻ & điều kiện người nhận):** Trên hồ sơ khách hàng hoặc doanh nghiệp, người có ô (Khách hàng, Gán) — hoặc (Doanh nghiệp, Gán) — ở mức M bao phủ bản ghi đổi được Người phụ trách, **chỉ sang người mà sau khi đổi, bản ghi vẫn nằm trong mức M của chính người gán** (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`): mức Chỉ của mình chỉ cho **nhận việc về mình**, không cho giao bản ghi của mình sang người khác; mức Đơn vị của mình cho giao trong đơn vị; giao sang đơn vị khác cần mức rộng hơn bao phủ đơn vị đích. **Điều kiện người nhận** — mọi quy tắc khác của tài liệu dẫn chiếu tới đây: người nhận Đang hoạt động (hoặc Đang chờ chấp nhận khi giữ chỗ theo `BR-23.5`), có ô Xem và ô Sửa của loại dữ liệu đó khác Không có (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`, thống nhất [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `BR-01.2`); người thực hiện chỉ chọn chính mình làm người nhận khi mọi ô của mình trên loại dữ liệu đó đã bao phủ bản ghi trước khi chuyển (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.2`, `BR-43.4`). Khi lượt chuyển kèm thực thể con (`BR-34.3`), cùng quy tắc đích áp trên ô Gán **của từng loại dữ liệu** — (Cơ hội, Gán người phụ trách), (Vé hỗ trợ, Gán) — và người nhận phải có ô Xem, ô Sửa của loại đó khác Không có. Ngoài ra, **Người phụ trách hiện tại luôn có hai đường**, kể cả khi ô Gán của họ chỉ là Chỉ của mình: trả về hàng đợi mà bản ghi đang thuộc (chỉ với bản ghi chờ phân công chưa chốt, `BR-31.9` (d)), và đề nghị chuyển cho một người cụ thể (`BR-34.1b`). Hệ thống ghi vào lịch sử bản ghi và thông báo cho cả người giao và người nhận. Đơn vị tổ chức của bản ghi chuyển theo Người phụ trách mới ngay lập tức, theo `BR-01.3`.

  **Lý do nghiệp vụ:** Giao bản ghi cho người không sửa được thì bản ghi bị đóng băng; tự nhận về mình thì mọi ô mức Chỉ của mình của người nhận phủ thêm bản ghi đó, nên chuyển giao không được là cách nhận quyền xóa hay xuất khách hàng mà mình vốn không có. Giữ bản ghi trong mức của người gán để ô Gán không thành đường đẩy khách sang đơn vị mà người gán không có thẩm quyền.

- **`BR-34.1b` (Đề nghị chuyển cho đồng nghiệp):** **Người phụ trách hiện tại**, bất kể mức ô Gán, tự khởi tạo được "Đề nghị chuyển giao" cho một người cụ thể, không cần Quản lý thực hiện thay (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6` (b)). Đề nghị **chỉ chuyển bản ghi khách hàng hoặc doanh nghiệp đó**, không kèm thực thể con. Đề nghị chỉ có hiệu lực khi **người nhận chấp nhận**, và tại lúc chấp nhận người nhận phải tự đạt đúng điều kiện nhận việc của tài liệu đó: Đang hoạt động; ô (Khách hàng, Gán) — hoặc (Doanh nghiệp, Gán) — khác Không có; xem được bản ghi theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`, gồm cả qua lượt chia sẻ và Đội ngũ phụ trách; và không bị nguồn chặn nào; cộng một điều kiện của phân hệ mà [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6` cho phép: ô Sửa của loại dữ liệu đó khác Không có. Không có giới hạn cùng đơn vị. Đề nghị hết hạn theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-25-01` hoặc khi Người phụ trách đổi. Trong lúc chờ, bản ghi vẫn thuộc người đề nghị. Khi đề nghị được chấp nhận, quản lý trực tiếp của người đề nghị được thông báo; lượt chuyển có hiệu lực ngay, không có khoảng chờ để quản lý thu hồi — người có ô Gán đạt `BR-34.1` vẫn giao lại được nếu không đồng ý; cùng cách với [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `BR-01.2`.

  **Lý do nghiệp vụ:** Người nhận tự đạt điều kiện nhận việc nên đề nghị không cấp cho ai thứ họ không tự nhận được — không cần thêm giới hạn đơn vị hay quyền thu hồi riêng. Hoán đổi khách giữa hai nhân viên (đổi địa bàn, khách quen của đồng nghiệp, khách yêu cầu đổi người phụ trách) xảy ra liên tục; nếu mọi lượt đều qua Quản lý thì Quản lý thành người chuyển bản ghi hộ, và trong lúc chờ, đồng nghiệp không thấy bản ghi nên khách gọi vào không ai có ngữ cảnh — dẫn tới việc chuyển thông tin khách qua kênh chat nội bộ, đưa dữ liệu ra khỏi hệ thống.

- **`BR-34.2` (Chuyển giao hàng loạt):** Chọn nhiều bản ghi theo bộ lọc (Người phụ trách, Đơn vị tổ chức, Giai đoạn vòng đời, Thẻ) và chuyển giao đồng thời. **Bắt buộc có bước xem trước** hiển thị tổng số khách hàng, số doanh nghiệp, số Cơ hội đang mở và số Vé hỗ trợ đang mở bị ảnh hưởng trước khi xác nhận.

  **Lý do nghiệp vụ:** Nhân viên nghỉ việc thường phụ trách hàng trăm khách; chuyển từng bản ghi là không khả thi, nhưng chuyển hàng loạt mà không thấy trước quy mô thì dễ chuyển nhầm cả danh bạ của một đội.

- **`BR-34.3` (Phạm vi thực thể con):** Người thực hiện chọn một trong ba mức: **(a)** chỉ khách hàng/doanh nghiệp; **(b)** kèm Cơ hội và Vé hỗ trợ **đang mở**; **(c)** kèm Cơ hội đang mở và **mọi** Vé hỗ trợ, gồm vé đã đóng. Mặc định là **(b)** (Phụ lục B, `CFG-34-01`). **Cơ hội đã đóng (Thắng hoặc Thua) không bao giờ đổi Người phụ trách** ở bất kỳ mức nào (theo [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `BR-37.2`). Mỗi thực thể con chỉ đổi Người phụ trách khi ô Gán **của chính loại dữ liệu đó** — (Cơ hội, Gán người phụ trách), (Vé hỗ trợ, Gán) — của người thực hiện bao phủ nó, sau khi chuyển vẫn nằm trong mức Gán đó, và người nhận đạt điều kiện tương ứng theo `BR-34.1` và tài liệu sở hữu loại dữ liệu đó. Phần Cơ hội của lượt chuyển kèm thực thể con tuân đúng [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `BR-37.1`: bắt buộc ghi lý do; Người phụ trách đơn vị của đơn vị nhận được thông báo khi cơ hội sang đơn vị khác; Công việc nhắc chưa xử lý, tỷ lệ chia doanh số đang chờ và danh sách Người theo dõi đi theo cơ hội. Vé hỗ trợ là **bản ghi công việc**: đổi Người phụ trách không đổi đơn vị của vé — vé vẫn thuộc đơn vị tiếp nhận của hàng đợi (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.11`; quy tắc chi tiết tại [`tickets-srs.md`](./tickets-srs.md)). Thực thể người thực hiện không có quyền, hoặc người nhận không đạt điều kiện, **không chuyển** và được liệt kê trong **danh sách bỏ qua** ở bước xem trước và trong kết quả.

  **Lý do nghiệp vụ:** Cơ hội và vé đang mở cần người theo tiếp ngay, nên đi cùng khách hàng theo mặc định; thực thể đã đóng thuộc thành tích lịch sử của người cũ, chuyển theo sẽ làm sai báo cáo thành tích. Quyền Gán trên khách hàng không phải quyền Gán trên cơ hội hay vé; chuyển kèm mà không xét ô của từng loại là đường vòng giao cơ hội và vé mà người thực hiện không được giao.

- **`BR-34.4` (Không bản ghi vô chủ khi nhân sự tạm ngưng hoặc rời workspace) — sàn bắt buộc:**
  - **(a) Tạm ngưng không bị chặn:** tạm ngưng một thành viên luôn thực hiện ngay, không bị chặn vì người đó còn là Người phụ trách của bản ghi nào (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-42.4`), và không bị chặn khi nhật ký tạm thời không ghi được — lượt tạm ngưng có hiệu lực, bản ghi nhật ký được ghi bù ngay khi nhật ký phục hồi (ADR-0010).
  - **(b) Xử lý bản ghi sau tạm ngưng:** ngay sau đó, như một thao tác riêng, người thực hiện chọn cho khách hàng và doanh nghiệp người đó đang phụ trách một trong ba cách: **giữ nguyên**; **chuyển tạm** cho một người xử lý thay đạt điều kiện người nhận của `BR-34.1` — mặc định đề xuất là quản lý trực tiếp của người bị tạm ngưng; hoặc **trả về hàng đợi** — chỉ với bản ghi chờ phân công còn chưa chốt (`BR-31.9` (d)). Yêu cầu chờ xử lý (`BR-31.6`) và mọi thông báo nghiệp vụ về khách do người bị tạm ngưng phụ trách chuyển ngay cho người xử lý thay; khi chọn giữ nguyên, chuyển cho quản lý trực tiếp của người đó, hoặc quản lý tạm thời nếu đã được chỉ định (thống nhất [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `BR-16.6`); người bị tạm ngưng không nhận thông báo (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-42.1`). Bản ghi giữ nguyên xuất hiện trong báo cáo `BR-34.5`. Khi người đó được kích hoạt lại, đề xuất trả về người cũ chỉ gồm các bản ghi còn mở **mà người xử lý thay vẫn đang phụ trách** (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-42.4`) — bản ghi đã được trả về hàng đợi hay đã giao tiếp cho người khác không thuộc đề xuất — và được gửi tới người xử lý thay và quản lý trực tiếp.
  - **(c) Rời workspace bị chặn khi còn bản ghi:** thành viên chỉ được gỡ khỏi workspace khi mọi khách hàng và doanh nghiệp người đó đang phụ trách đã có người nhận theo `BR-34.1`, hoặc đã được trả về hàng đợi theo `BR-31.9` (d) — bước bắt buộc "Bản ghi đang phụ trách" của quy trình rời workspace (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-43`, `BR-43.1`). Đối tượng của bước này xác định tại lúc bắt đầu quy trình, không bị lượt "Tạm ngưng ngay" trong cùng quy trình làm mất. Trong quy trình rời workspace và xử lý bản ghi sau tạm ngưng, người thực hiện **không cần** ô Gán bao phủ bản ghi; chỉ áp điều kiện người nhận theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.2`, `BR-43.4` (thống nhất [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `BR-37.3`).

  **Lý do nghiệp vụ:** Cắt truy cập của người có nghi vấn phải nhanh và không phụ thuộc việc tìm được người nhận bàn giao, nên tạm ngưng không bao giờ bị chặn — kể cả bởi sự cố nhật ký, vì thu hẹp quyền chậm lại là rủi ro thật còn nhật ký ghi bù thì không mất dấu vết. Nhưng gỡ hẳn trước rồi bàn giao sau là cách chắc chắn nhất tạo ra hàng trăm bản ghi vô chủ mà không ai biết cho tới khi khách phàn nàn (Nguyên tắc 5).

- **`BR-34.5` (Báo cáo bản ghi vô chủ):** Báo cáo thường trực **"Bản ghi không có Người phụ trách hoạt động"** (người phụ trách đang Tạm ngưng, đã rời workspace, hoặc để trống — không gồm bản ghi chờ phân công đang nằm trong hàng đợi của đơn vị tiếp nhận và bản ghi giữ chỗ, vốn đã có nơi theo dõi), phục vụ Quản lý và Quản trị Chất lượng Dữ liệu rà soát định kỳ.

  **Lý do nghiệp vụ:** Bản ghi vô chủ không tự lộ ra; không có báo cáo thường trực thì chỉ khi khách phàn nàn doanh nghiệp mới biết một tập khách không ai chăm sóc.

- **`BR-34.6` (Nghỉ phép & ủy quyền tạm):** **Chính người dùng — bất kể vai trò** — tự khai báo được khoảng thời gian nghỉ phép kèm người xử lý thay; quản lý trực tiếp khai báo được thay cho cấp dưới của mình. Người xử lý thay phải thỏa điều kiện người nhận của `BR-34.1`. Trong khoảng đó, người dùng được coi là **"không khả dụng"**: không nhận khách hàng tiềm năng mới (`BR-31.1`), yêu cầu chờ xử lý chuyển ngay cho người xử lý thay (`BR-31.6`), nhưng **quyền phụ trách chính không thay đổi**. Trạng thái không khả dụng cũng được áp **tự động** khi người dùng không đăng nhập quá **14 ngày liên tiếp** (Phụ lục B, `CFG-34-02`). Trạng thái này khác Tạm ngưng: người dùng vẫn truy cập được và quyền không đổi; tự tạm ngưng vì không hoạt động là quy tắc riêng của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-42`. Với nhánh tự động — vốn không có ai được khai báo làm người xử lý thay:
  - **(a)** người xử lý thay mặc định là **Quản lý trực tiếp** của người đó, thống nhất `BR-34.4`;
  - **(b)** hệ thống **bắt buộc thông báo** cho chính người dùng và cho Quản lý khi trạng thái được bật;
  - **(c)** trạng thái **tự hết hiệu lực ngay ở lần đăng nhập kế tiếp**.

  **Lý do nghiệp vụ:** Không có ba quy tắc này thì yêu cầu chờ xử lý tại `BR-31.6` không có đích, và một nhân viên đi công tác dài sẽ bị âm thầm loại khỏi vòng phân bổ trong khi yêu cầu của khách cũ họ phụ trách nằm im.

- **`BR-34.7` (Kiểm toán):** Mọi thao tác chuyển giao (đơn lẻ và hàng loạt) được ghi nhật ký (`NFR-07`): người thực hiện, người giao, người nhận, số lượng và danh sách bản ghi bị ảnh hưởng, thời điểm.

  **Lý do nghiệp vụ:** Tranh chấp về khách hàng và hoa hồng sau khi nhân sự thay đổi chỉ phân xử được khi biết chắc ai đã giao khách nào cho ai, lúc nào.

- **`BR-34.8` (Ghi nhận người xử lý thay để tính thành tích):** Khi một người **không phải Người phụ trách chính** xử lý một Yêu cầu chờ xử lý (`BR-31.6`) hoặc một Cơ hội phát sinh từ yêu cầu đó, hệ thống ghi nhận **"Người xử lý"** riêng biệt với Người phụ trách trên bản ghi yêu cầu. Báo cáo thành tích thể hiện được cả hai vai.

  **Lý do nghiệp vụ:** Khách cũ hỏi mua thêm là nơi sinh tranh chấp nội bộ đầu tiên về thành tích và hoa hồng; không ghi nhận thì hai người tranh nhau một đơn mà không có căn cứ phân xử, hoặc không ai nhận vì biết không được tính công.

- **`BR-34.9` (Chuyển phòng — đổi Đơn vị chính của Người phụ trách):** Khi một thành viên đổi Đơn vị chính (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-11.5`, `BR-11.9`), bước "cách xử lý bản ghi" của danh sách bước bắt buộc người thực hiện chọn cho Khách hàng và cho Doanh nghiệp người đó đang phụ trách, **không có lựa chọn nào được chọn sẵn**:
  - **(a) Đi theo người** — bản ghi đổi đơn vị theo Người phụ trách (`BR-01.3`). Lựa chọn này **bị chặn** cho loại dữ liệu mà sau thay đổi người đó không còn ô Sửa khác Không có.
  - **(b) Bàn giao lại** — cho người nhận đạt `BR-34.1`, theo `BR-34.1` – `BR-34.3`.
  - **(c) Trả về hàng đợi** — chỉ với bản ghi chờ phân công chưa chốt (`BR-31.9` (d)).

  Lựa chọn có thể khác nhau giữa Khách hàng và Doanh nghiệp, và chọn được theo từng nhóm bản ghi. Phần Cơ hội theo [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `BR-37.3`. Như ở `BR-34.4` (c), người thực hiện không cần ô Gán; chỉ áp điều kiện người nhận.

  **Lý do nghiệp vụ:** Chuyển phòng là lúc khách hàng dễ lạc nhất: đi theo người thì đơn vị cũ mất khách đang chăm sóc, bàn giao lại thì khách đổi đầu mối giữa chừng. Đây là quyết định kinh doanh nên phải có người chọn tường minh; mặc định ngầm làm khách âm thầm đổi đơn vị mà không ai quyết. Không cho đi theo người khi người đó không còn sửa được, vì bản ghi sẽ đứng tên một người không làm việc được trên nó.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-34.1.1` | Quản lý Kinh doanh mở hồ sơ khách do A phụ trách | Đổi Người phụ trách sang B | B là Người phụ trách; A và B đều nhận thông báo; lịch sử bản ghi ghi nhận |
| `AC-34.1.2` | Khách hàng do A (thuộc Phòng Kinh doanh 1) phụ trách; Giám đốc Kinh doanh G có (Khách hàng, Gán) = Đơn vị và các đơn vị con tại "Khối Kinh doanh", bao phủ cả Phòng Kinh doanh 1 và 2; Trưởng Phòng Kinh doanh 2 chưa thấy khách này | G đổi Người phụ trách sang B thuộc Phòng Kinh doanh 2 | Khách hàng chuyển sang thuộc Phòng Kinh doanh 2 ngay lập tức; Trưởng Phòng Kinh doanh 2 thấy khách hàng này trong danh sách đơn vị mình |
| `AC-34.1.3` | Trưởng Phòng Kinh doanh 1 P có (Khách hàng, Gán) = Đơn vị và các đơn vị con tại Phòng Kinh doanh 1 | P tìm chọn B thuộc Phòng Kinh doanh 2 làm Người phụ trách mới cho khách của A | B không có trong danh sách người nhận, kèm giải thích bản ghi sẽ ra ngoài mức Gán của P |
| `AC-34.1.4` | Nhân viên A có (Khách hàng, Gán) = Chỉ của mình | A mở hồ sơ khách mình phụ trách, tìm đổi Người phụ trách trực tiếp sang đồng nghiệp | Không có thao tác đổi trực tiếp; chỉ có "Đề nghị chuyển giao" và — nếu là khách chờ phân công chưa chốt — "Trả về hàng đợi" |
| `AC-34.1b.1` | A và C cùng Đơn vị chính, C có (Khách hàng, Gán) = Chỉ của mình | A gửi Đề nghị chuyển giao một khách cho C | Khách vẫn do A phụ trách cho tới khi C chấp nhận; sau khi C chấp nhận, C là Người phụ trách; quản lý trực tiếp của A nhận thông báo; Cơ hội và vé của khách vẫn do người cũ phụ trách |
| `AC-34.1b.2` | Tiếp nối AC-34.1b.1 | Quản lý trực tiếp của A tìm hành động "Thu hồi chuyển giao" | Không có; quản lý giao lại được bằng thao tác Gán nếu ô Gán của họ đạt `BR-34.1` |
| `AC-34.1b.8` | A gửi Đề nghị cho C; C có ô Gán khác Không có nhưng (Khách hàng, Sửa) = Không có | C bấm chấp nhận | Từ chối, nêu thiếu ô Sửa; khách vẫn do A phụ trách |
| `AC-34.1b.5` | A gửi Đề nghị cho C; C có (Khách hàng, Gán) = Không có | C bấm chấp nhận | Từ chối, nêu C chưa đạt điều kiện nhận việc; khách vẫn do A phụ trách |
| `AC-34.1b.6` | A gửi Đề nghị cho C, `CFG-25-01` của tài liệu Phân quyền = 3 ngày | C không phản hồi trong 3 ngày | Đề nghị hết hạn; khách vẫn do A phụ trách; A được báo |
| `AC-34.1b.4` | A và D khác Đơn vị chính; D là thành viên Đội ngũ phụ trách của khách, có ô (Khách hàng, Gán) khác Không có | A gửi Đề nghị cho D, D chấp nhận | D là Người phụ trách; khách chuyển sang đơn vị của D |
| `AC-34.1b.7` | D khác đơn vị, không xem được khách theo bất kỳ nguồn nào | D bấm chấp nhận đề nghị | Từ chối, nêu D không xem được bản ghi |
| `AC-34.2.1` | A phụ trách 450 khách hàng, 20 doanh nghiệp, 8 Cơ hội mở, 3 Vé mở | Quản lý lọc "Người phụ trách là A", chọn chuyển cho B | Bước xem trước hiển thị đúng 450, 20, 8, 3 trước khi xác nhận |
| `AC-34.3.1` | Tiếp nối AC-34.2.1, dùng mức mặc định; người thực hiện có ô Gán bao phủ cả bốn loại dữ liệu | Xác nhận | Khách hàng, doanh nghiệp, 8 Cơ hội mở và 3 Vé mở chuyển sang B; 3 vé vẫn thuộc đơn vị tiếp nhận của hàng đợi; các Cơ hội đã đóng của A giữ nguyên |
| `AC-34.3.3` | A có 2 Cơ hội đã Thắng và 1 Cơ hội Thua; người thực hiện chọn mức (c) | Xác nhận chuyển cho B | 3 Cơ hội đã đóng vẫn do A phụ trách; báo cáo kết quả của chúng không đổi |
| `AC-34.3.4` | Chuyển kèm Cơ hội đang mở sang B thuộc đơn vị khác | Xác nhận mà bỏ trống lý do | Không xác nhận được; sau khi nhập lý do, người phụ trách đơn vị của B được báo; Công việc nhắc và Người theo dõi của các cơ hội đi theo |
| `AC-34.3.2` | Như AC-34.3.1 nhưng người thực hiện có (Vé hỗ trợ, Gán) = Không có | Xem trước rồi xác nhận | Bước xem trước nêu "3 vé sẽ bị bỏ qua — thiếu quyền Gán trên Vé hỗ trợ"; sau xác nhận, vé giữ Người phụ trách cũ và có trong danh sách bỏ qua của kết quả |
| `AC-34.4.1` | A còn phụ trách 450 khách hàng | Người có quyền Tạm ngưng người dùng tạm ngưng A | A bị tạm ngưng ngay; màn hình đề nghị xử lý 450 khách hàng: giữ nguyên, chuyển tạm, hoặc trả về hàng đợi cho các khách chờ phân công chưa chốt |
| `AC-34.4.2` | Tiếp nối AC-34.4.1 | Chọn chuyển tạm, chấp nhận người xử lý thay đề xuất là quản lý trực tiếp M của A | 450 khách chuyển tạm cho M; khi A được kích hoạt lại, M và quản lý trực tiếp nhận đề xuất trả các bản ghi còn mở về A |
| `AC-34.4.3` | Nhật ký tạm thời không ghi được | Tạm ngưng A | A bị tạm ngưng và đăng xuất ngay; khi nhật ký phục hồi, bản ghi nhật ký của lượt tạm ngưng xuất hiện với thời điểm thực |
| `AC-34.4.4` | A bị tạm ngưng, người thực hiện chọn giữ nguyên | Mở báo cáo "Bản ghi không có Người phụ trách hoạt động" | 450 khách của A có trong báo cáo; yêu cầu chờ xử lý mới của các khách này chuyển cho quản lý trực tiếp của A (hoặc quản lý tạm thời nếu có); A không nhận thông báo |
| `AC-34.4.7` | 450 khách chuyển tạm cho M; trong thời gian A tạm ngưng, M giao 50 khách cho K | A được kích hoạt lại | Đề xuất trả về A chỉ gồm 400 khách M vẫn đang phụ trách |
| `AC-34.4.5` | A còn 450 khách hàng, đã mở quy trình rời workspace | Tìm nút Gỡ | Nút Gỡ bị vô hiệu cho tới khi bước "450 khách hàng" hoàn tất |
| `AC-34.4.6` | Bước bàn giao khách hàng của A | Chọn người nhận có (Khách hàng, Sửa) = Không có | Người đó không chọn được, kèm lý do |
| `AC-34.5.1` | Sau khi bàn giao toàn bộ bản ghi của A | Mở báo cáo "Bản ghi không có Người phụ trách hoạt động" | Không còn bản ghi nào thuộc A |
| `AC-34.6.1` | Một Nhân viên Kinh doanh, một Nhân viên Hỗ trợ, một Nhân viên Marketing và một Quản lý Marketing | Mỗi người tự khai báo nghỉ phép 5 ngày kèm người xử lý thay | Cả bốn lưu thành công; trong khoảng nghỉ không ai nhận phân bổ mới; quyền phụ trách chính của họ không đổi |
| `AC-34.6.2` | Quản lý trực tiếp của nhân viên E | Khai báo nghỉ phép thay cho E | Lưu thành công |
| `AC-34.6.3` | Nhân viên Kinh doanh không phải quản lý trực tiếp của đồng nghiệp | Tìm cách khai báo nghỉ phép thay cho đồng nghiệp | Không khả dụng |
| `AC-34.6.4` | Nhân viên E không đăng nhập 14 ngày liên tiếp, không khai báo nghỉ phép | Qua mốc 14 ngày | E ở trạng thái không khả dụng; người xử lý thay là Quản lý trực tiếp; E và Quản lý nhận thông báo; không bản ghi nào đổi Người phụ trách |
| `AC-34.6.5` | Tiếp nối AC-34.6.4 | E đăng nhập lại | Trạng thái không khả dụng hết ngay; E trở lại vòng phân bổ mà không cần Quản trị viên can thiệp |
| `AC-34.7.1` | Sau chuyển giao hàng loạt tại AC-34.3.1 | Người có quyền đọc nhật ký mở nhật ký | Thấy người thực hiện, A, B, số lượng và danh sách bản ghi, thời điểm |
| `AC-34.9.1` | Nhân viên A (phụ trách 120 khách, 10 doanh nghiệp) chuyển từ "Kinh doanh 1" sang "Kinh doanh 2" | Người thực hiện mở bước xử lý bản ghi | Ba lựa chọn hiển thị cho Khách hàng và cho Doanh nghiệp, không lựa chọn nào được chọn sẵn; không xác nhận được khi chưa chọn |
| `AC-34.9.2` | Tiếp nối; chọn "Đi theo người" cho khách hàng | Xác nhận | 120 khách thuộc "Kinh doanh 2" ngay; quản lý "Kinh doanh 1" không còn thấy qua mức Đơn vị của mình |
| `AC-34.9.3` | Sau khi đổi, vai trò gợi ý của "Kinh doanh 2" làm (Khách hàng, Sửa) của A = Không có | Mở lựa chọn cho khách hàng | "Đi theo người" bị vô hiệu kèm giải thích; chỉ còn bàn giao lại hoặc trả về hàng đợi |
| `AC-34.9.4` | 15 trong 120 khách là khách chờ phân công chưa chốt từ biểu mẫu; 105 khách A tự tạo | Chọn "Trả về hàng đợi" | Lựa chọn chỉ áp cho 15 khách; 105 khách còn lại phải chọn đi theo người hoặc bàn giao lại |
| `AC-34.8.1` | A nghỉ phép, B xử lý một Yêu cầu chờ xử lý và tạo Cơ hội từ đó | Mở báo cáo thành tích | Cơ hội hiển thị A là Người phụ trách và B là Người xử lý |

---

#### FEAT-35 — Chia sẻ Bản ghi & Đội ngũ Phụ trách Khách hàng

**Mô tả nghiệp vụ:** Cho phép nhiều người cùng phục vụ một khách hàng với các mức quyền khác nhau, bổ sung cho mô hình một Người phụ trách.

**Vai trò sử dụng chính:** Người phụ trách bản ghi (chia sẻ bản ghi mình phụ trách), Nhân viên Hỗ trợ (đối tượng chính nhận quyền đọc tự động — `BR-35.4`), người có ô Gán bao phủ bản ghi, Người có toàn quyền.

**Bối cảnh nghiệp vụ:** Một khách hàng doanh nghiệp lớn được phục vụ đồng thời bởi nhân viên kinh doanh, Quản lý Khách hàng Hiện hữu, nhân viên hỗ trợ và kế toán. Do Người phụ trách được gán cho người tạo (`BR-01.3`) — thực tế luôn là nhân viên kinh doanh — phạm vi "của mình" của Nhân viên Hỗ trợ gần như rỗng, khiến Ngữ cảnh Khách hàng một chạm (`FEAT-28`) không dùng được cho đúng đối tượng nó được thiết kế, và `KPI-02` không thể đạt.

**Điều kiện tiên quyết:** Người chia sẻ là Người phụ trách, người có ô (Khách hàng, Gán) bao phủ bản ghi, hoặc Người có toàn quyền (`BR-35.2`).

**Luồng chính:**

1. Người chia sẻ thêm thành viên vào Đội ngũ phụ trách, chọn vai trò tham gia, mức quyền và ngày hết hiệu lực (`BR-35.1` – `BR-35.3`).
2. Hệ thống tính quyền hiệu lực của thành viên là phần giao của năng lực theo vai trò và mức chia sẻ, và báo rõ khi lượt chia sẻ không có hiệu lực (`BR-35.5`).
3. Khi một vé hoặc hội thoại đang mở được gắn với khách, hệ thống cấp quyền đọc tự động cho người đang xử lý và thu hồi khi vé/hội thoại đóng (`BR-35.4`).
4. Mọi lượt chia sẻ, thu hồi và truy cập theo quyền đọc tự động được ghi nhật ký (`BR-35.6`).

**Quy tắc nghiệp vụ:**

- **`BR-35.1` (Đội ngũ phụ trách):** Mỗi khách hàng/doanh nghiệp có thể có một Đội ngũ phụ trách gồm nhiều thành viên (số tối đa theo gói tại `NFR-11`), mỗi thành viên mang một **vai trò tham gia** từ A.13 (Kinh doanh chính, Hỗ trợ kỹ thuật, Quản lý khách hàng, Kế toán công nợ, Quan sát) và một **mức quyền**: **Chỉ đọc** hoặc **Chỉnh sửa**. Mức quyền là **trần trên của lượt chia sẻ, không phải một lượt cấp quyền**: thành viên không có năng lực sửa theo vai trò thì dù được thêm ở mức Chỉnh sửa vẫn không sửa được — quyền hiệu lực là **phần giao** của năng lực vai trò và mức chia sẻ. Màn hình phải nói rõ điều này khi xảy ra.

  **Ai được thêm vào đội ngũ:** mọi người có ô (Khách hàng, Xem) khác Không có đều được thêm. Người có ô (Khách hàng, Đọc ghi chú nội bộ) = Không có — theo ma trận mặc định là Marketing và Quản lý Marketing — **chỉ được thêm với vai trò tham gia "Quan sát"**, áp cột (B) của bảng che mặt nạ như mọi thành viên Chỉ đọc (`BR-04.5b`), và **vẫn không đọc được** ghi chú phạm vi "Nội bộ đội bán hàng" (`BR-36.1`), vì tư cách thành viên chỉ nới phạm vi chứ không cấp năng lực (`BR-35.5` (i)). Người có ô đó khác Không có — kể cả Nhân viên Hỗ trợ theo mặc định — được thêm với bất kỳ vai trò tham gia nào, và khi đó đọc được ghi chú "Nội bộ đội bán hàng" **của riêng bản ghi đó**.

  **Lý do nghiệp vụ:** Nếu người mời tin rằng mình đã cấp quyền sửa còn người được mời thấy hệ thống từ chối, cả hai sẽ coi đó là lỗi. Người được doanh nghiệp chủ động không cho đọc ghi chú nội bộ — thường là người có tầm nhìn toàn tổ chức như Marketing — mà được mở lại quyền đó chỉ bằng thao tác thêm thành viên thì quyết định của doanh nghiệp bị vô hiệu bởi một người phụ trách bản ghi.

- **`BR-35.2` (Quyền chia sẻ):** Người phụ trách bản ghi và người có ô (Khách hàng, Gán) bao phủ bản ghi thêm/bớt thành viên; Người có toàn quyền thao tác trên mọi bản ghi. Mức quyền của lượt thêm không vượt mức hiệu lực của chính người thêm trên bản ghi đó, và không ai tự thêm chính mình (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.7`). Thành viên mức Chỉ đọc **không** được chia sẻ tiếp. Được chia sẻ một bản ghi đưa người đó **vào phạm vi dữ liệu của bản ghi ấy**.

  **Lý do nghiệp vụ:** Người chịu trách nhiệm về khách mới biết ai cần cùng phục vụ; nhưng người chỉ được đọc mà chia sẻ tiếp được thì một lượt chia sẻ lan ra không kiểm soát, và người tự thêm mình thì tự nới phạm vi.

- **`BR-35.3` (Chia sẻ có thời hạn):** Mỗi lượt chia sẻ được đặt ngày hết hiệu lực; hết hạn thì quyền tự động thu hồi và ghi vào lịch sử bản ghi.

  **Lý do nghiệp vụ:** Nhu cầu cùng phục vụ thường gắn với một dự án hay một đợt hỗ trợ; chia sẻ không có hạn sẽ tích tụ thành quyền tồn dư sau khi việc đã xong.

- **`BR-35.3b` (Vòng đời của Yêu cầu quyền truy cập, Yêu cầu nhận bàn giao và Đề nghị gộp):** Ba hành động tại `BR-17.3` tạo ra một **bản ghi yêu cầu** có vòng đời riêng, không chỉ là một thông báo. Ba hành động sinh **bốn loại yêu cầu** vì "Yêu cầu quyền truy cập" cho chọn xin quyền đọc hoặc quyền sửa:
  - **Nội dung:** người yêu cầu, khách hàng liên quan, loại yêu cầu (xin quyền đọc / xin quyền sửa / yêu cầu nhận bàn giao / đề nghị gộp), lý do, thời điểm tạo, hạn xử lý, người xử lý, trạng thái (Chờ xử lý / Đã chấp thuận / Đã từ chối kèm lý do / Tự động cấp quyền đọc tạm do quá hạn — trạng thái cuối **chỉ áp dụng cho loại xin quyền đọc**).
  - **Người xử lý:** xin quyền đọc/sửa — Người phụ trách hiện hữu, hoặc quản lý trực tiếp của họ sau khi leo thang; yêu cầu nhận bàn giao — người có ô Gán đạt `BR-34.1` với người yêu cầu là người nhận; người không đạt chuyển tiếp lên theo thứ tự `BR-17.2c` (ii), và yêu cầu không ai xử lý đóng với lý do "Không được xử lý"; đề nghị gộp — Quản trị Chất lượng Dữ liệu hoặc Quản trị viên.
  - **Cam kết thời gian và hành vi khi quá hạn:** theo `BR-17.2c`. Hệ thống **không** tự cấp quyền sửa, **không** tự chuyển giao và **không** tự gộp trong bất kỳ trường hợp nào.
  - **Kiểm toán:** mọi thay đổi trạng thái của bản ghi yêu cầu được ghi nhật ký (`NFR-07`).

  **Lý do nghiệp vụ:** Yêu cầu chỉ là một thông báo thì dễ bị bỏ qua và không ai biết đã quá hạn; có vòng đời và người xử lý rõ ràng thì người yêu cầu biết chờ ai, và hệ thống leo thang đúng người.

- **`BR-35.4` (Quyền đọc tự động cho tuyến Hỗ trợ):** Khi một Vé hỗ trợ hoặc Hội thoại đa kênh **đang mở** được gắn với một khách hàng, nhân viên đang xử lý vé/hội thoại đó **tự động có quyền đọc** hồ sơ 360, Dòng thời gian và Ngữ cảnh Khách hàng của khách đó trong suốt thời gian vé/hội thoại còn mở, kể cả khi bản ghi nằm ngoài phạm vi dữ liệu thông thường của họ. Quyền này:
  - **(a)** là quyền **đọc**, không cho sửa dữ liệu nghiệp vụ — **ngoại lệ duy nhất** là hai thao tác của `FEAT-33` mà tuyến Hỗ trợ thực thi được ngay khi khách yêu cầu: **gắn Hạn chế xử lý** (`BR-30.6`) và **hạ đồng thuận xuống Từ chối nhận tin** (`BR-30.10`). Ngoại lệ chỉ có hiệu lực trong thời gian vé/hội thoại còn mở, và mỗi lượt đều ghi nhật ký;
  - **(b)** áp **cột (C)** của bảng `BR-04.3` — kênh liên lạc che một phần, đủ để xác minh đúng người và liên lạc trong hệ thống theo `BR-04.6`; định danh KYC vẫn che hoàn toàn;
  - **(c)** **bắt buộc ghi nhật ký truy cập** để phát hiện lạm dụng;
  - **(d)** tự động hết hiệu lực khi vé/hội thoại đóng.

  Quyền này là một **lượt cấp do phân hệ sinh ra** theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.5` — mức trần Chỉ đọc, nguồn sinh là vé hoặc hội thoại đang mở — nên chịu lượt chặn tường minh trên bản ghi và không nới lỏng lớp che nào (bước 3, bước 4 của `BR-39.6` tài liệu đó). Nó không bao giờ mở ghi chú phạm vi "Nội bộ đội bán hàng" (`BR-36.7`).

  **Lý do nghiệp vụ:** Đây là cơ chế giải quyết trực tiếp bế tắc của `FEAT-28`, thay cho việc cấp quyền xem toàn bộ cho mọi nhân viên hỗ trợ. Hai thao tác ghi được mở vì chúng chỉ thu hẹp phạm vi xử lý, đảo lại được, và là điều kiện để cam kết "Tức thì" tại `FEAT-33` có người thực thi — khách nói "đừng gửi tin cho tôi nữa" ngay trong hội thoại mà người đang nói chuyện với khách không làm được gì thì cam kết đó chỉ có trên giấy.

- **`BR-35.5` (Phụ thuộc tài liệu Phân quyền):** Cơ chế thực thi phạm vi dữ liệu và thứ tự ưu tiên giữa quyền theo vai trò, quyền theo phạm vi tổ chức và quyền chia sẻ bản ghi thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md). Tài liệu này ràng buộc ba điểm: **(i)** chia sẻ nới rộng **phạm vi dữ liệu**, không nới rộng **năng lực theo vai trò**; **(ii)** chia sẻ **không nới lỏng bất kỳ mức che trường nào** — chính sách tại `FEAT-04` áp nguyên cho thành viên Đội ngũ phụ trách; **(iii)** một lượt chia sẻ **không vượt được** lượt chặn tường minh trên bản ghi do quản trị viên đặt (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.1`) — chia sẻ không lấy đi quyền nào, nhưng cũng không gỡ được một lệnh chặn đặt vì lý do pháp lý hoặc hợp đồng.

  **Lý do nghiệp vụ:** Chia sẻ là cách mở cửa vào một bản ghi cho người cần phục vụ khách, không phải cách cấp năng lực hay gỡ một lệnh chặn; nếu chia sẻ làm được hai việc đó, người phụ trách bản ghi trở thành người cấp quyền không ai kiểm soát.

- **`BR-35.6` (Kiểm toán):** Mọi thao tác chia sẻ, thu hồi chia sẻ và mọi lượt truy cập theo quyền đọc tự động tại `BR-35.4` được ghi nhật ký (`NFR-07`). Thao tác chỉ thu hẹp — thu hồi chia sẻ, gỡ thành viên Đội ngũ phụ trách, hạ mức Chỉnh sửa về Chỉ đọc, hết hiệu lực quyền đọc tự động hay quyền đọc tạm — **không bị chặn** khi nhật ký tạm thời không ghi được: thao tác có hiệu lực ngay và bản ghi nhật ký được ghi bù khi nhật ký phục hồi (ADR-0010). Thao tác nới rộng — thêm thành viên, nâng mức — không thực hiện được khi chưa ghi được nhật ký.

  **Lý do nghiệp vụ:** Nhật ký chia sẻ là căn cứ duy nhất trả lời ai đã được mở cửa vào hồ sơ của một khách và vì sao. Thu hồi bị kẹt vì sự cố nhật ký nghĩa là một người lẽ ra đã mất quyền vẫn đọc được khách hàng — rủi ro thật hơn việc ghi nhật ký trễ vài phút; còn nới rộng không có dấu vết thì không bao giờ chấp nhận được.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-35.1.1` | Tập đoàn Đại Việt do A phụ trách | A thêm B (Quản lý khách hàng, Chỉnh sửa), C (Hỗ trợ kỹ thuật, Chỉ đọc), D (Kế toán công nợ, Chỉ đọc) | Cả ba truy cập được hồ sơ; B sửa được; C, D chỉ đọc |
| `AC-35.1.2` | Một thành viên có vai trò hệ thống không có năng lực sửa khách hàng được thêm ở mức Chỉnh sửa | Thành viên đó mở hồ sơ | Không sửa được; màn hình giải thích quyền hiệu lực là phần giao giữa năng lực vai trò và mức chia sẻ |
| `AC-35.1.3` | A thêm một Nhân viên Marketing (ô Đọc ghi chú nội bộ = Không có theo mặc định) vào đội ngũ | Mở danh sách vai trò tham gia | Chỉ chọn được "Quan sát" |
| `AC-35.1.7` | Doanh nghiệp điều chỉnh ô (Khách hàng, Đọc ghi chú nội bộ) của vai trò Marketing thành Chỉ của mình qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02` | A thêm một Nhân viên Marketing vào đội ngũ với vai trò "Kinh doanh chính" | Thêm được; người đó đọc được ghi chú "Nội bộ đội bán hàng" của riêng bản ghi đó |
| `AC-35.1.4` | Nhân viên Marketing là thành viên "Quan sát" | Mở ghi chú "Nội bộ đội bán hàng" của bản ghi | Không đọc được |
| `AC-35.1.5` | Nhân viên Hỗ trợ C là thành viên đội ngũ | Mở ghi chú "Nội bộ đội bán hàng" của bản ghi đó | Đọc được |
| `AC-35.1.6` | Gói tiêu chuẩn, đội ngũ đã có 10 thành viên | Thêm thành viên thứ 11 | Từ chối, nêu giới hạn của gói |
| `AC-35.2.1` | C là thành viên Chỉ đọc | C tìm cách thêm người khác vào đội ngũ | Không khả dụng |
| `AC-35.3.1` | Quyền của D đặt hết hiệu lực sau 30 ngày | Qua ngày thứ 30 | D không còn truy cập được; lịch sử bản ghi và nhật ký ghi nhận thu hồi; quyền của B và C không đổi |
| `AC-35.3b.1` | C gửi "Yêu cầu quyền truy cập" loại xin quyền sửa, không ai xử lý | Quá hai lần thời hạn | Chỉ có leo thang; C không được tự cấp quyền sửa |
| `AC-35.4.1` | Chị Mai do A (phòng khác) phụ trách, gửi tin nhắn qua trò chuyện trực tuyến; C tiếp nhận | C mở hồ sơ, dòng thời gian và khung ngữ cảnh | C xem được cả ba; kênh liên lạc che một phần; KYC che hoàn toàn; lượt truy cập có trong nhật ký |
| `AC-35.4.2` | Tiếp nối AC-35.4.1 | C tìm cách sửa tên, giai đoạn hoặc thẻ của chị Mai | Không sửa được |
| `AC-35.4.3` | Tiếp nối AC-35.4.1, chị Mai nói "đừng gửi email tiếp thị cho tôi nữa" | C hạ đồng thuận email xuống Từ chối nhận tin | Thực hiện được; nhật ký ghi nhận |
| `AC-35.4.4` | Tiếp nối AC-35.4.3 | C tìm cách nâng lại lên Đồng ý nhận tin | Bị từ chối |
| `AC-35.4.5` | Hội thoại đã đóng | C mở lại hồ sơ chị Mai và thử hạ đồng thuận | Chỉ thấy thông tin tối thiểu theo `BR-17.3`; không thực hiện được thao tác hạ đồng thuận |
| `AC-35.5.1` | Quản trị viên đặt lượt chặn tường minh không cho D truy cập một khách hàng | A thêm D vào Đội ngũ phụ trách của khách đó | D vẫn không truy cập được; A được báo lượt chia sẻ không có hiệu lực |
| `AC-35.5.2` | B là thành viên mức Chỉnh sửa | B xem trường KYC | Trường vẫn che hoàn toàn |
| `AC-35.6.1` | Nhật ký tạm thời không ghi được | A thu hồi quyền của D trong Đội ngũ phụ trách | D mất truy cập ngay; khi nhật ký phục hồi, lượt thu hồi có trong nhật ký |
| `AC-35.6.2` | Nhật ký tạm thời không ghi được | A thêm E vào Đội ngũ phụ trách | Không thực hiện được; màn hình nêu lý do và đề nghị thử lại |

**Tham chiếu:** [ADR-0007](../docs/adr/0007-record-sharing-and-permission-precedence-contract.md) — hợp đồng nghiệp vụ về chia sẻ bản ghi và thứ tự ưu tiên quyền.

---

#### FEAT-36 — Ghi chú & Ghi nhận Hoạt động Khách hàng

**Mô tả nghiệp vụ:** Quản lý ghi chú nội bộ và bản ghi hoạt động (cuộc gọi, cuộc họp, email đã gửi) gắn với khách hàng — nguồn dữ liệu chính của Dòng thời gian 360 độ.

**Vai trò sử dụng chính:** Mọi người dùng có quyền xem bản ghi tương ứng.

**Bối cảnh nghiệp vụ:** Ghi chú được viện dẫn ở nhiều nơi (nguồn sự kiện của `FEAT-27`, hành động nhanh tại `BR-02.2`, ghi chú ghim tại `BR-28.1`, bằng chứng liên hệ tại `BR-31.8`). Trong vận hành thật, ghi chú chứa nội dung thương mại nhạy cảm ("khách sẵn sàng trả tới 800 triệu", "đang so sánh với đối thủ X"); nếu không quy định rõ ai đọc được và ai xóa được, nhân viên sẽ xóa lịch sử trước khi nghỉ việc hoặc ngừng ghi chú thật — làm rỗng giá trị cốt lõi mà `FEAT-27` và `KPI-02` hướng tới.

**Điều kiện tiên quyết:** Người tạo ghi chú có ô (Khách hàng, Xem) bao phủ bản ghi hoặc quyền đọc tự động; người đọc ghi chú chịu phạm vi đọc tại `BR-36.1`.

**Luồng chính:**

1. Người dùng tạo ghi chú, chọn phạm vi đọc (mặc định theo `CFG-36-01`).
2. Hệ thống tự sinh bản ghi hoạt động từ cuộc gọi, email, tin nhắn, cuộc hẹn trong hệ thống (`BR-36.5`).
3. Người dùng ghim ghi chú, bổ sung nội dung hoặc ẩn ghi chú theo `BR-36.2` – `BR-36.4`.

**Quy tắc nghiệp vụ:**

- **`BR-36.1` (Phạm vi đọc):** Mỗi ghi chú có một trong **ba** phạm vi:
  - **Nội bộ đội bán hàng (mặc định):** người có ô (Khách hàng, Đọc ghi chú nội bộ) — thao tác đặc thù, Mục 5.1 — bao phủ bản ghi đọc được; thành viên Đội ngũ phụ trách (`FEAT-35`) đọc được ghi chú của riêng bản ghi đó khi ô này của họ khác Không có; Người có toàn quyền đọc được. Theo ma trận mặc định, Người phụ trách và quản lý của họ (vai trò Quản lý, mức Đơn vị và các đơn vị con) đọc được; **Marketing và Quản lý Marketing có ô này = Không có nên không đọc được — kể cả khi là thành viên đội ngũ** (`BR-35.1`). Quyền đọc tự động (`BR-35.4`) không bao giờ mở phạm vi này.
  - **Chung:** mọi người có quyền xem bản ghi đều đọc được, gồm Marketing và tuyến Hỗ trợ.
  - **Giới hạn:** chỉ người tạo, Người phụ trách bản ghi, các cấp trên trong chuỗi quản lý trực tiếp của Người phụ trách, và Người có toàn quyền.

  Phạm vi mặc định là tham số cấu hình (Phụ lục B, `CFG-36-01`).

  **Lý do nghiệp vụ:** Nếu mặc định để toàn tổ chức đọc được — nhất là khi có người xem toàn tổ chức như Marketing — nhân viên sẽ ngừng ghi chú thật hoặc ghi vào sổ riêng. Người phân khúc cần dữ liệu phân khúc, không cần nội dung thương lượng giá. Gắn quyền đọc vào một ô riêng thay vì tên vai trò để doanh nghiệp tự quyết ai đọc được, và để mọi vai trò tự tạo được xử lý cùng một cách.

- **`BR-36.2` (Sửa ghi chú):** Người tạo sửa được nội dung trong **24 giờ** đầu (Phụ lục B, `CFG-36-02`). Sau thời hạn đó chỉ được **bổ sung** nội dung mới, không sửa nội dung cũ.

  **Lý do nghiệp vụ:** Lịch sử trao đổi chỉ có giá trị làm căn cứ khi không bị viết lại sau khi sự việc đã diễn ra.

- **`BR-36.3` (Không xóa cứng) — sàn bắt buộc:** Ghi chú và bản ghi hoạt động **không được xóa vĩnh viễn** bởi người dùng thường. Thao tác "Xóa" chỉ **ẩn** ghi chú khỏi dòng thời gian, giữ nguyên nội dung và ghi nhật ký người ẩn (`NFR-07`). Quản trị viên xem được ghi chú đã ẩn. Ngoại lệ duy nhất là khi thực thi quyền chủ thể dữ liệu (`BR-33.8`).

  **Lý do nghiệp vụ:** Ghi chú là lịch sử trao đổi với khách; nếu xóa được, nhân viên sắp nghỉ việc có thể xóa dấu vết, và doanh nghiệp mất căn cứ khi có tranh chấp.

- **`BR-36.4` (Ghi chú ghim):** Người phụ trách bản ghi và người có ô (Khách hàng, Sửa) bao phủ bản ghi ghim được tối đa **3 ghi chú** lên đầu hồ sơ; các ghi chú ghim là nội dung hiển thị trong Ngữ cảnh Khách hàng một chạm (`BR-28.1`), sau khi lọc theo `BR-36.7`.

  **Lý do nghiệp vụ:** Ghi chú ghim là những gì mọi người phục vụ khách phải biết trước tiên; quá nhiều ghi chú ghim thì không còn gì nổi bật.

- **`BR-36.5` (Bản ghi hoạt động tự động):** Hoạt động phát sinh trong hệ thống (cuộc gọi đã thực hiện kèm thời lượng, email đã gửi, tin nhắn đã gửi, cuộc hẹn đã tạo) được tự động ghi thành bản ghi hoạt động và **không cho sửa nội dung**. Đây là nguồn sinh bằng chứng liên hệ nhóm 1 (`BR-31.8`); bằng chứng nhóm 2 cũng được ghi thành bản ghi hoạt động nhưng đánh dấu rõ là do người dùng khai báo, kèm người xác nhận.

  **Lý do nghiệp vụ:** Bằng chứng liên hệ chỉ đáng tin khi không tạo khống và không sửa được.

- **`BR-36.6` (Thuộc phạm vi dữ liệu cá nhân):** Nội dung ghi chú và hoạt động thuộc phạm vi phải xử lý khi thực thi quyền xóa của chủ thể dữ liệu (`BR-33.8`).

  **Lý do nghiệp vụ:** Ghi chú thường chứa thông tin cá nhân của khách; bỏ ngoài phạm vi xóa thì yêu cầu xóa không bao giờ hoàn tất thật.

- **`BR-36.7` (Ghi chú trong Ngữ cảnh Khách hàng một chạm):** Khung ngữ cảnh là cơ chế truy cập chính của Nhân viên Hỗ trợ (`FEAT-28`), nhưng phạm vi mặc định của ghi chú là "Nội bộ đội bán hàng" mà tuyến Hỗ trợ không đọc được. Quy tắc:
  - Khung ngữ cảnh **lọc ghi chú theo phạm vi đọc của người xem** (`BR-36.1`).
  - Người ghim ghi chú được chọn **"Cho phép tuyến Hỗ trợ đọc"** trên từng ghi chú ghim, để chia sẻ đúng thông tin cần cho việc phục vụ (ví dụ "khách đang chờ xử lý khiếu nại lô hàng tháng 8") mà không mở nội dung thương lượng giá.
  - Phạm vi ghi chú đọc được **qua quyền đọc tự động** là tham số cấu hình (Phụ lục B, `CFG-36-03`), mặc định: phạm vi "Chung" cộng các ghi chú ghim đã được đánh dấu cho phép; quyền đọc tự động không bao giờ mở phạm vi "Nội bộ đội bán hàng".

  **Lý do nghiệp vụ:** Không có quy tắc này, khung ngữ cảnh luôn rỗng phần ghi chú với đúng đối tượng nó phục vụ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-36.1.1` | A ghi chú "Khách sẵn sàng trả tới 800 triệu" trên khách mình phụ trách, cấu hình mặc định | Nhân viên Marketing mở hồ sơ | Ghi chú có phạm vi "Nội bộ đội bán hàng"; Marketing không đọc được nội dung |
| `AC-36.1.2` | Cùng ghi chú | Quản lý Kinh doanh của A và Quản trị viên mở hồ sơ | Đọc được |
| `AC-36.1.3` | A tạo ghi chú phạm vi "Giới hạn" | Thành viên Đội ngũ phụ trách (không phải Quản lý) mở hồ sơ | Không đọc được |
| `AC-36.2.1` | A tạo ghi chú lúc 09:00 | A sửa lúc 11:00 cùng ngày | Sửa được |
| `AC-36.2.2` | Cùng ghi chú | A sửa lúc 15:00 hôm sau (sau 30 giờ) | Không sửa được nội dung cũ; chỉ bổ sung được nội dung mới |
| `AC-36.3.1` | Ghi chú của A | A bấm "Xóa" | Ghi chú biến mất khỏi dòng thời gian; nhật ký ghi A đã ẩn và thời điểm; Quản trị viên vẫn xem được |
| `AC-36.3.2` | Người dùng thường | Tìm thao tác xóa vĩnh viễn ghi chú | Không tồn tại |
| `AC-36.4.1` | Hồ sơ đã có 3 ghi chú ghim | A ghim ghi chú thứ 4 | Từ chối, nêu giới hạn 3 ghi chú ghim |
| `AC-36.5.1` | A gọi khách qua hệ thống, cuộc gọi 3 phút | Mở dòng thời gian | Có bản ghi hoạt động cuộc gọi kèm thời lượng 3 phút; không có thao tác sửa nội dung |
| `AC-36.5.2` | A khai báo gặp khách ngoài hệ thống, Quản lý xác nhận | Mở dòng thời gian | Bản ghi hoạt động được đánh dấu "do người dùng khai báo" kèm tên người xác nhận |
| `AC-36.7.1` | Hồ sơ có 3 ghi chú ghim phạm vi "Nội bộ đội bán hàng", 1 ghi chú được đánh dấu "Cho phép tuyến Hỗ trợ đọc" | Nhân viên Hỗ trợ đang xử lý hội thoại mở khung ngữ cảnh | Chỉ thấy 1 ghi chú ghim được phép |
| `AC-36.7.2` | Hồ sơ có một ghi chú ghim phạm vi "Chung", không đánh dấu "Cho phép tuyến Hỗ trợ đọc" | Nhân viên Hỗ trợ đang xử lý hội thoại mở khung ngữ cảnh | Thấy ghi chú ghim đó, vì phạm vi "Chung" thuộc phạm vi đọc mặc định của tuyến Hỗ trợ |

---

## 4. Yêu cầu phi chức năng

### 4.1 Hiệu năng

**Điều kiện đo chung.** Mọi ngưỡng dưới đây được đo tại phía máy chủ (không tính thời gian dựng giao diện trên trình duyệt), trên môi trường nghiệm thu có cấu hình tương đương môi trường vận hành thật, với **50 người dùng đồng thời**, bằng công cụ đo tải do Trưởng nhóm Kiểm thử chỉ định, trên tập dữ liệu mẫu chuẩn: **1.000.000 khách hàng, 100.000 doanh nghiệp, và hồ sơ dùng để đo dòng thời gian có 500 sự kiện** (thêm một hồ sơ cực biên 10.000 sự kiện đo riêng, báo cáo tách biệt). Mỗi ngưỡng phải đạt trong **3 lần đo liên tiếp**.

- **`NFR-01` (Tìm kiếm & lọc danh bạ):** Tìm kiếm khách hàng theo tên, email, số điện thoại hoặc lọc theo danh sách phản hồi dưới **300 mili giây với 95% lượt truy vấn**.
- **`NFR-02` (Dòng thời gian 360 độ & Ngữ cảnh Khách hàng):** Phản hồi dưới **150 mili giây với 95% lượt truy vấn** — đây là **ngưỡng nghiệm thu duy nhất** cho cả hai chức năng. Mức 50 mili giây với 50% lượt truy vấn tại `BR-28.2` là mục tiêu tối ưu, không phải tiêu chí nghiệm thu.
- **`NFR-03` (Tốc độ nhập khẩu):** Tiến trình nhập khẩu xử lý tối thiểu **1.000 dòng/giây** với tệp dung lượng lớn.

### 4.2 Độ tin cậy & Toàn vẹn Dữ liệu

- **`NFR-04` (Gộp & hoàn tác gộp toàn vẹn):** Gộp và hoàn tác gộp phải cùng thành công hoặc cùng thất bại như một đơn vị duy nhất. Nếu có lỗi ở bất kỳ bước chuyển giao nào, toàn bộ thao tác được hủy hoàn toàn, không để lại trạng thái dang dở; trường hợp gián đoạn ngoài ý muốn được xử lý theo `FEAT-21`.
- **`NFR-05` (Bảo toàn sổ cái gộp):** Mục sổ cái gộp được lưu vĩnh viễn và không bị xóa kể cả khi Bản ghi Chính bị xóa mềm, chỉ chịu ngoại lệ khử định danh tại `BR-33.8`.

### 4.3 An toàn & Bảo mật

- **`NFR-06` (Bảo vệ dữ liệu nhạy cảm):** Chính sách phân quyền trường được thực thi **đúng theo `FEAT-04`** — mức hiển thị của mỗi trường do nhóm trường (`BR-04.1`) và quan hệ của người xem với bản ghi (`BR-04.3`) quyết định, không do một quy tắc riêng ở mục này. Yêu cầu phi chức năng ở đây là: chính sách phải được thực thi **trên chính dữ liệu hệ thống trả ra**, sao cho dữ liệu vượt mức hiển thị cho phép **không bao giờ rời khỏi hệ thống** — kể cả khi giao diện bị can thiệp, và kể cả qua tệp xuất — và mọi lượt nâng mức hiển thị (`BR-04.4`) đều để lại dấu vết theo `NFR-07`.

- **`NFR-07` (Nhật ký kiểm toán):** Danh mục dưới đây là **nguồn duy nhất** về các thao tác bắt buộc ghi nhật ký kiểm toán; một quy tắc viện dẫn `NFR-07` mà thao tác của nó không có trong danh mục là lỗi tài liệu.
  1. Mở khóa mặt nạ (`BR-04.4`).
  2. Hành động liên lạc trong hệ thống (`BR-04.6`).
  3. Xuất dữ liệu (`BR-25.3`).
  4. Gộp bản ghi, gồm các bước chuyển giai đoạn sinh từ gộp (`BR-12.6`) và việc xử lý giao dịch gộp bị gián đoạn (`BR-21.2`).
  5. Hoàn tác gộp.
  6. Xóa bản ghi, gồm xác nhận xóa sau chốt an toàn (`BR-05.6`).
  7. Khôi phục từ Thùng rác.
  8. Hoàn tác Chuyển đổi Tiềm năng (`BR-14.2`).
  9. Chọn tạo Cơ hội riêng khi doanh nghiệp đã có Cơ hội đang mở trên cùng phễu (`BR-14.3`).
  10. Thay đổi quy tắc chấm điểm (`BR-15.4`).
  11. Chuyển giao quyền phụ trách (`BR-34.7`).
  12. Chia sẻ bản ghi, thêm thành viên Đội ngũ phụ trách, và mọi lượt truy cập theo quyền đọc tạm — gồm quyền đọc tự động của tuyến Hỗ trợ và quyền tự cấp khi yêu cầu quá hạn (`BR-35.6`, `BR-17.2c`).
  13. Đọc hồ sơ khách hàng **ngoài phạm vi phụ trách** — lượt xem chỉ được phép nhờ mức Xem trên Khách hàng rộng hơn mức Sửa của chính người đó (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.9`). Áp như nhau cho mọi vai trò có cấu hình "xem rộng, sửa hẹp", dù là vai trò dựng sẵn — Nhân viên Kinh doanh (Xem = Đơn vị của mình, Sửa = Chỉ của mình), Marketing (Xem = Toàn workspace, Sửa = Không có), Kiểm toán — hay vai trò doanh nghiệp tự tạo. Quản trị viên và Chủ sở hữu không thuộc sự kiện này vì mọi thao tác của họ đã được phủ bởi các sự kiện khác và bởi `NFR-14`. Khối lượng nhật ký sinh ra ở đây được chấp nhận có chủ đích: đó là cái giá của việc cấp tầm nhìn rộng cho một vai trò không phụ trách những bản ghi đó, và là căn cứ duy nhất trả lời được câu hỏi ai đã đọc hồ sơ của một khách hàng khi có khiếu nại.
  14. Ẩn ghi chú (`BR-36.3`).
  15. Nâng mức đồng thuận từ mọi nguồn tác động (`BR-30.10`).
  16. Sửa nguồn gốc theo lô (`BR-32.3b`).
  17. Đọc nhật ký kiểm toán ở cả hai mức có quyền (`NFR-14`).
  18. Mọi thay đổi trạng thái của bản ghi yêu cầu xin quyền đọc/sửa, yêu cầu nhận bàn giao, đề nghị gộp (`BR-35.3b`).
  19. Bỏ qua cảnh báo trùng lặp khi tenant cấu hình "chỉ cảnh báo" (`BR-17.2`).
  20. Xác nhận "Đây là người khác dùng chung định danh này" (`BR-17.2`).
  21. Gắn nhãn Định danh dùng chung cho lô nhập khẩu chọn "Tạo bản ghi mới dù trùng" (`BR-23.3`).
  22. Khai báo và xác nhận bằng chứng liên hệ nhóm 2 (`BR-31.8`).
  23. Đánh dấu, gỡ dấu và duyệt "Lead rác" (`BR-12.4b`).
  24. Phê duyệt và thu hồi phê duyệt Chiến dịch Tái tiếp cận (`BR-12.5b`).
  25. Dỡ sớm biện pháp phòng ngừa khi không xác minh được chủ thể dữ liệu (`BR-33.7` (b)).
  26. Thay đổi tham số cấu hình (Phụ lục B).
  27. Xử lý yêu cầu chủ thể dữ liệu (`FEAT-33`), gồm gắn/dỡ Hạn chế xử lý (`BR-30.6`) và hạ đồng thuận theo yêu cầu của khách (`BR-35.4` (a)).
  28. Thao tác trên hàng đợi khách hàng tiềm năng: nhận việc, gán từ hàng đợi, trả về hàng đợi, đổi đơn vị tiếp nhận của nguồn (`BR-31.9`); tạo, sửa, tạm dừng quy tắc phân bổ và đổi người chịu trách nhiệm quy tắc (`BR-31.10`).
  29. Tạo, gán lại và kết thúc bản ghi giữ chỗ (`BR-23.5`).
  30. Xử lý bản ghi của người bị tạm ngưng hoặc rời workspace — giữ nguyên, chuyển tạm, trả về hàng đợi (`BR-34.4`).

  **Thu hẹp không bị chặn khi nhật ký lỗi:** thao tác chỉ thu hẹp điều người khác được làm hoặc được thấy — thu hồi chia sẻ, gỡ thành viên Đội ngũ phụ trách, hết hiệu lực quyền đọc tự động hay quyền đọc tạm, tạm ngưng thành viên, hạ đồng thuận, gắn Hạn chế xử lý — có hiệu lực ngay cả khi nhật ký tạm thời không ghi được, và bản ghi nhật ký được ghi bù với thời điểm thực khi nhật ký phục hồi (ADR-0010). Mọi thao tác khác trong danh mục không thực hiện được khi chưa ghi được nhật ký.

  Mỗi bản ghi nhật ký lưu: người thực hiện, thời điểm, bản ghi bị tác động, loại thao tác và **tên các trường bị tác động**.

  **Giới hạn nội dung — sàn bắt buộc:** nhật ký **không được lưu giá trị thật của các trường nhạy cảm** thuộc ba nhóm tại `BR-04.1`. Với mở khóa mặt nạ, nhật ký chỉ ghi "đã mở khóa trường Số điện thoại của bản ghi X", không ghi chính số điện thoại đó. Với thay đổi dữ liệu không nhạy cảm (giai đoạn vòng đời, người phụ trách, thẻ, tham số cấu hình), nhật ký lưu giá trị trước và sau.

  **Lý do nghiệp vụ:** Nếu lưu giá trị thật, nhật ký trở thành kho dữ liệu cá nhân lớn nhất của phân hệ và là đường đi vòng qua chính sách che mặt nạ — người xem được nhật ký sẽ đọc được giá trị mà chính họ không có quyền mở khóa.

- **`NFR-08` (Thời hạn lưu nhật ký kiểm toán):** Nhật ký được lưu tối thiểu **24 tháng** (gói tiêu chuẩn) và **60 tháng** (gói Enterprise). Trong thời hạn này nhật ký **không cho sửa hoặc xóa từng bản ghi**, kể cả bởi Chủ sở hữu — ngoại lệ duy nhất là **khử định danh** khi thực thi quyền chủ thể dữ liệu (`BR-33.8`), do Quản trị viên cùng Người phụ trách Bảo vệ Dữ liệu thực hiện và để lại một bản ghi nhật ký về chính việc khử định danh đó.

- **`NFR-14` (Kiểm soát truy cập nhật ký kiểm toán) — sàn bắt buộc:** Nhật ký kiểm toán có **ba mức truy cập**:

| Mức | Ai được cấp | Phạm vi đọc |
| --- | --- | --- |
| **Toàn phần** | Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu | Toàn bộ nhật ký, truy vấn theo khoảng thời gian và theo bản ghi |
| **Theo bản ghi đang xử lý** | Quản trị viên, **chỉ trong phạm vi một bản ghi đang có yêu cầu chủ thể dữ liệu hoặc thao tác gộp/khôi phục đang thực hiện** | Chỉ nhật ký của đúng bản ghi đó, chỉ trong thời gian yêu cầu còn mở — mức tối thiểu để thực hiện nghĩa vụ khử định danh (`BR-33.8`) và tra soát khi hoàn tác gộp |
| **Không truy cập** | Mọi người khác: mọi vai trò dựng sẵn — kể cả Kiểm toán và Kiểm toán quyền, vốn chỉ đọc nhật ký quyền theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-41` chứ không đọc nhật ký kiểm toán của phân hệ — và mọi vai trò tự tạo | — |

  **Mọi lượt đọc nhật ký**, ở cả hai mức có quyền, đều được ghi nhật ký. Không hỗ trợ xuất toàn bộ nhật ký ra tệp trừ khi có phê duyệt kép theo quy tắc dưới.

  **Quy tắc thay thế người thứ hai:** khi tenant không chỉ định Người phụ trách Bảo vệ Dữ liệu, trách nhiệm thuộc Chủ sở hữu (Mục 2.2) — nếu áp nguyên văn "Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu cùng phê duyệt" thì hai người sụp về một. Khi đó người thứ hai là **một Quản trị viên khác, không phải người đang thực hiện thao tác**. Quy tắc thay thế này áp cho mọi chỗ tài liệu yêu cầu "hai người khác nhau" hoặc "phê duyệt kép" (`BR-25.4`, `BR-33.7`, `BR-33.8`, `NFR-08`, `NFR-14`, Phụ lục B).

  **Lý do nghiệp vụ:** Nhật ký kiểm toán chứa dấu vết truy cập vào toàn bộ khách hàng; nếu mở rộng quyền đọc, chính nhật ký trở thành công cụ khai thác dữ liệu.

### 4.4 Khả dụng, Sao lưu & Phục hồi

- **`NFR-09` (Mức độ khả dụng):** Phân hệ cam kết mức khả dụng tối thiểu **99,9% mỗi tháng**, không tính thời gian bảo trì có thông báo trước tối thiểu 48 giờ.
- **`NFR-10` (Sao lưu & phục hồi thảm họa):** Mức mất dữ liệu tối đa cho phép là **15 phút**; thời gian phục hồi mục tiêu là **4 giờ**. Quy trình phục hồi được diễn tập kiểm chứng tối thiểu **2 lần/năm**. Bản sao lưu được lưu theo cơ chế **cuốn vòng 35 ngày** — đây là con số mà bảng phạm vi xóa tại `BR-33.8` dựa vào, nên hai nơi phải giữ cùng một giá trị.

### 4.5 Khả năng mở rộng & Giới hạn dung lượng

- **`NFR-11` (Giới hạn theo gói dịch vụ):** Hệ thống áp dụng và hiển thị rõ các giới hạn sau:

| Giới hạn | Gói tiêu chuẩn | Gói Enterprise |
| --- | --- | --- |
| Số khách hàng tối đa mỗi không gian làm việc | **500.000** | 5.000.000 |
| Số doanh nghiệp tối đa mỗi không gian làm việc | **50.000** | 500.000 |
| Số liên kết doanh nghiệp tối đa trên một khách hàng | **20** | 50 |
| Số thẻ phân loại tối đa trên một bản ghi | **50** | 100 |
| Số bản ghi tối đa mỗi lần xuất dữ liệu | **50.000** | 200.000 |
| Số thành viên tối đa trong một Đội ngũ phụ trách (`BR-35.1`) | **10** | 25 |

  Khi đạt **80%** giới hạn, Chủ sở hữu nhận cảnh báo để nâng gói. Vượt giới hạn do thao tác gộp được xử lý theo `BR-19.10` (không chặn gộp).

### 4.6 Đa ngôn ngữ & Định dạng theo vùng

- **`NFR-12` (Đa ngôn ngữ & hướng hiển thị):** Toàn bộ giao diện và thông báo nghiệp vụ hỗ trợ tối thiểu **tiếng Việt, tiếng Anh và tiếng Ả Rập**; tiếng Ả Rập bắt buộc hỗ trợ bố cục hiển thị từ phải sang trái. Tên riêng của khách hàng hiển thị đúng dấu và đúng ký tự gốc, không bị chuyển tự tự động.
- **`NFR-13` (Định dạng theo vùng):** Số điện thoại, ngày tháng, đơn vị tiền tệ và múi giờ hiển thị theo thiết lập vùng của từng không gian làm việc; dữ liệu được lưu theo một chuẩn quốc tế thống nhất để báo cáo đa vùng nhất quán.

---

## 5. Ma trận quyền truy cập tính năng

Mục này khai báo phần mà [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) giao cho phân hệ: các ô của loại dữ liệu Khách hàng và Doanh nghiệp, các thao tác đặc thù của phân hệ (`FEAT-24` của tài liệu đó), ma trận mặc định của các vai trò dựng sẵn (`FEAT-29` của tài liệu đó) và các quyền quản trị của phân hệ. Năm mức truy cập và mọi quy tắc phân giải phạm vi theo đúng tài liệu đó; Người có toàn quyền (Quản trị viên, Chủ sở hữu) có mức Toàn workspace ở mọi ô và mọi quyền quản trị dưới đây, trừ nơi Sàn bắt buộc nêu khác.

### 5.1 Ma trận ô mặc định của vai trò dựng sẵn

*Viết tắt trong bảng:* "Của mình" = Chỉ của mình; "Đơn vị" = Đơn vị của mình; "Đơn vị + con" = Đơn vị và các đơn vị con; "Toàn WS" = Toàn workspace; "—" = Không có.

**Loại dữ liệu Khách hàng:**

| Thao tác | Nhân viên Kinh doanh | Nhân viên Hỗ trợ | Quản lý | Marketing | Quản lý Marketing | Chỉ xem bản ghi được giao | Kiểm toán | Kiểm toán quyền |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Xem | Đơn vị | Đơn vị | Đơn vị + con | Toàn WS | Toàn WS | Của mình | Toàn WS | — |
| Tạo | Có | — | Có | — | Có | — | — | — |
| Sửa | Của mình | — | Đơn vị + con | — | Đơn vị | — | — | — |
| Xoá | — | — | Đơn vị | — | — | — | — | — |
| Xuất | Của mình (`BR-25.5`) | — | — | — | — | — | — | — |
| Nhập | — | — | — | — | Đơn vị | — | — | — |
| Gán người phụ trách | Của mình | — | Đơn vị + con | — | Đơn vị | — | — | — |
| *Thao tác đặc thù:* Chuyển giai đoạn vòng đời (`BR-02.2`) | Của mình | — | Đơn vị + con | — | — | — | — | — |
| *Thao tác đặc thù:* Gắn thẻ phân loại (`BR-03.1`) | Của mình | — | Đơn vị + con | Toàn WS | Toàn WS | — | — | — |
| *Thao tác đặc thù:* Ghi nhận đồng thuận — nâng mức kèm bằng chứng (`BR-30.10`) | Của mình | — | Đơn vị + con | Toàn WS | Toàn WS | — | — | — |
| *Thao tác đặc thù:* Đọc ghi chú nội bộ (`BR-36.1`) | Của mình | Của mình | Đơn vị + con | — | — | Của mình | — | — |
| *Thao tác đặc thù:* Mở khóa mặt nạ — Nhóm 1, Nhóm 2 (`BR-04.4`) | — | — | — | — | — | — | — | — |
| *Thao tác đặc thù:* Mở khóa Định danh KYC (`BR-04.4`; sàn `BR-04.7` — tối đa Chỉ của mình, cấp cần BVDL phê duyệt) | — | — | — | — | — | — | — | — |

**Loại dữ liệu Doanh nghiệp:** các ô Xem, Tạo, Sửa, Xoá, Xuất, Nhập, Gán người phụ trách mang cùng giá trị như dòng tương ứng của Khách hàng; Doanh nghiệp không có thao tác đặc thù.

Ghi chú về các ô:

- Các ô tuân `BR-25.1` của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md): không thao tác nào rộng hơn Xem cùng dòng. Các thao tác đặc thù hiển thị thành dòng riêng ở chế độ Cơ bản.
- **Điểm chi tiết khác mức đặt sẵn chung của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`, có chủ đích:**
  - **Nhân viên Kinh doanh — (Khách hàng, Xuất) = Chỉ của mình:** nhu cầu hằng ngày (danh sách đi gặp khách, danh sách mời hội thảo) mà không có đường xuất hợp lệ thì nhân viên chép màn hình ra bảng tính, ngoài mọi nhật ký (`BR-25.5`); vai trò vì vậy hiển thị "Tùy chỉnh" ở chế độ Cơ bản.
  - **Nhân viên Kinh doanh — (Doanh nghiệp, Xuất) = Chỉ của mình:** cùng lý do; danh sách doanh nghiệp mình phụ trách là thông tin pháp nhân, không chứa trường nhạy cảm của hệ thống (`BR-04.7`).
  - **Marketing — Gắn thẻ phân loại và Ghi nhận đồng thuận ở mức Toàn workspace** dù các thao tác khác trên Khách hàng là Không có: phân khúc theo thẻ và quản lý đồng thuận theo kênh là công việc cốt lõi của vai trò trên toàn tập khách; hai thao tác này không đổi dữ liệu liên hệ, giai đoạn hay người phụ trách, và nâng đồng thuận luôn cần bằng chứng (`BR-30.10`).
- Hạ mức đồng thuận và gắn Hạn chế xử lý **không** cần ô Ghi nhận đồng thuận: ai tiếp cận được bản ghi — kể cả qua quyền đọc tự động — đều thực hiện được ngay (Nguyên tắc 3, `BR-35.4` (a)).
- Tạo ghi chú và bản ghi hoạt động không phải sửa hồ sơ khách hàng: người xem được bản ghi — kể cả qua quyền đọc tự động — đều tạo được (`FEAT-36`).
- Mở khóa mặt nạ không mặc định ở vai trò nào; doanh nghiệp cấp có chủ đích cho người cần, trong giới hạn hạn mức `CFG-04-02`. Năng lực xuất nhóm Định danh KYC không có ô và không cấp được qua vai trò (`BR-25.4`, sàn bắt buộc).

### 5.2 Quyền quản trị của phân hệ

Các quyền dạng có/không, không gắn với bản ghi, xuất hiện trong danh mục quyền quản trị của workspace (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-24`). Quyền chỉ cho phép thao tác trên những bản ghi mà ô nêu ở cột "Phạm vi áp dụng" của người giữ quyền bao phủ.

| Quyền quản trị | Thao tác được phép | Phạm vi áp dụng | Mặc định có ở vai trò dựng sẵn |
| --- | --- | --- | --- |
| **Duyệt vòng đời khách hàng** | Lùi giai đoạn (`BR-12.7`), chuyển Disqualified và mở lại (`BR-12.4`), duyệt Lead rác (`BR-12.4b`), sang Nurturing nhánh điểm nguội (`BR-16.4`), Nurturing → Lead (`BR-16.5`), quyết định sau cảnh báo tái phân loại (`BR-12.3c`) | Ô (Khách hàng, Chuyển giai đoạn vòng đời) | Quản lý |
| **Hoàn tác Chuyển đổi Tiềm năng** | `BR-14.2`, `BR-14.5` | Đồng thời ô (Khách hàng, Sửa), (Cơ hội, Xoá) và (Doanh nghiệp, Xoá) bao phủ các thực thể bị tác động; (Cơ hội, Sửa) cho nhánh gắn vào Cơ hội sẵn có (`BR-14.5` (2)) | Quản lý |
| **Cấu hình phân bổ khách hàng tiềm năng** | Tạo, sửa, xóa quy tắc phân bổ (`BR-31.5`, `BR-31.10`) | Ô (Khách hàng, Gán) bao phủ đơn vị tiếp nhận | Quản lý |
| **Xác nhận liên hệ ngoài hệ thống** | Xác nhận bằng chứng nhóm 2 (`BR-31.8`) | Ô (Khách hàng, Gán) | Quản lý |
| **Phê duyệt Tái tiếp cận — phía quan hệ khách hàng** | Một trong hai chữ ký của Chiến dịch Tái tiếp cận (`BR-12.5b` (a)) | Ô (Khách hàng, Gán) bao phủ tập khách | Quản lý |
| **Phê duyệt Tái tiếp cận — phía nội dung** | Chữ ký còn lại của Chiến dịch Tái tiếp cận (`BR-12.5b` (a)) | Ô (Khách hàng, Xem) bao phủ tập khách | Quản lý Marketing |
| **Cấu hình chấm điểm tiềm năng** | Sửa trọng số, tiêu chí, ngưỡng và suy giảm (`BR-15.4`, `CFG-15-01`, `CFG-15-02`, `CFG-16-01`) | Toàn workspace | Quản lý Marketing |
| **Xem cấu hình chấm điểm** | Chỉ xem cấu hình chấm điểm (`BR-15.4`) | Toàn workspace | Marketing, Quản lý Marketing |
| **Xem báo cáo nguồn gốc** | Báo cáo nguồn gốc và phân bổ doanh thu theo kênh (`BR-32.4`) | Toàn workspace | Marketing, Quản lý Marketing |
| **Tiếp nhận yêu cầu chủ thể dữ liệu** | Tạo bản ghi theo dõi yêu cầu và thực thi ngay hai loại thu hẹp (`FEAT-33`) | Ô (Khách hàng, Xem) hoặc quyền đọc tự động | Nhân viên Hỗ trợ, Quản lý |
| **Quét trùng lặp toàn workspace** | Công cụ quét và xử lý Đề nghị gộp (`BR-17.4`, `BR-35.3b`) | Toàn workspace | Không vai trò dựng sẵn nào — doanh nghiệp cấp cho người làm Quản trị Chất lượng Dữ liệu |
| **Hoàn tác gộp** | Hoàn tác gộp và xử lý giao dịch gộp bị gián đoạn (`BR-20.1`, `BR-21.1`) | Ô (Khách hàng, Xoá) bao phủ cả hai bản ghi | Không vai trò dựng sẵn nào |
| **Sửa nguồn gốc theo lô** | `BR-32.3b` | Toàn workspace | Không vai trò dựng sẵn nào |

Các năng lực sau **không** là quyền quản trị cấp được — chúng là Sàn bắt buộc chỉ thuộc Người có toàn quyền: loại khách đã trả tiền vì gian lận (`BR-12.8`); thực thi bản sao, chỉnh sửa, xóa vĩnh viễn và đóng yêu cầu chủ thể dữ liệu (`BR-33.1`, `BR-33.2`); xuất nhóm Định danh KYC (`BR-25.4`). Phê duyệt xuất vượt ngưỡng và khai báo nghỉ phép thay đi theo **quan hệ quản lý trực tiếp** (`BR-25.4`, `BR-34.6`), không theo quyền quản trị; quản lý không đồng ý với một lượt chuyển theo đề nghị thì giao lại bằng ô Gán theo `BR-34.1`.

### 5.3 Ma trận tính năng theo vai trò dựng sẵn

Bảng dưới là hệ quả của 5.1 và 5.2 trên từng tính năng, để người đọc tra một chỗ; nếu bảng này và 5.1, 5.2 nói khác nhau thì 5.1, 5.2 đúng và bảng này phải sửa.

| Mã FEAT | Tính năng | Nhân viên Kinh doanh | Nhân viên Hỗ trợ | Quản lý | Marketing | Quản lý Marketing | Quản trị viên | Chủ sở hữu | Tiến trình Hệ thống |
| --- | --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `FEAT-01` | Tạo & Quản lý Khách hàng | Xem Đơn vị; tạo; sửa Của mình | Xem Đơn vị | Xem, sửa Đơn vị + con; xóa Đơn vị | Xem Toàn WS | Xem Toàn WS; tạo; sửa Đơn vị | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-02` | Hồ sơ 360 độ | Xem Đơn vị | Xem Đơn vị + đọc tự động (`BR-35.4`) | Đơn vị + con | Xem Toàn WS | Xem Toàn WS | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-03` | Thẻ phân loại hàng loạt | Của mình | — | Đơn vị + con | Toàn WS | Toàn WS | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-04` | Mở khóa mặt nạ dữ liệu | Khi được cấp ô Mở khóa | Khi được cấp ô Mở khóa | Khi được cấp ô Mở khóa | Khi được cấp ô Mở khóa | Khi được cấp ô Mở khóa | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-05` | Thùng rác & Phục hồi Khách hàng | — | — | Xóa, khôi phục Đơn vị (`BR-05.3`), chịu `BR-05.6` | — | — | **Toàn quyền** | **Toàn quyền** | Dọn dẹp tự động (`BR-05.4`, chịu `BR-05.6`) |
| `FEAT-06` | Tạo & Quản lý Doanh nghiệp | Xem Đơn vị; tạo; sửa Của mình | Xem Đơn vị | Xem, sửa Đơn vị + con; xóa Đơn vị | Xem Toàn WS | Xem Toàn WS; tạo; sửa Đơn vị | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-07` | Cây Doanh nghiệp Mẹ – Con | Theo ô Doanh nghiệp; chỉ số tài chính theo ô Cơ hội (`BR-07.4`) | Xem cấu trúc theo ô Doanh nghiệp (`BR-07.4`) | Theo ô Doanh nghiệp và Cơ hội (`BR-07.4`) | Xem cấu trúc, không xem chỉ số tài chính hợp nhất (`BR-07.4` (c)) | Xem cấu trúc, không xem chỉ số tài chính hợp nhất (`BR-07.4` (c)) | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-08` | Hồ sơ Doanh nghiệp | Xem Đơn vị | Xem Đơn vị | Đơn vị + con | Xem Toàn WS | Xem Toàn WS | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-09` | Thùng rác & Phục hồi Doanh nghiệp | — | — | Xóa, khôi phục Đơn vị (`BR-09.2`) | — | — | **Toàn quyền** | **Toàn quyền** | Dọn dẹp tự động (`BR-05.4`) |
| `FEAT-10` | Quan hệ Đa Doanh nghiệp | Xem Đơn vị; sửa Của mình | Xem Đơn vị + đọc tự động (`BR-35.4`, khung liên kết của hồ sơ 360 — `BR-02.1`) | Đơn vị + con | Xem Toàn WS | Xem Toàn WS; sửa Đơn vị | **Toàn quyền** | **Toàn quyền** | Cập nhật trạng thái tiếp cận khi liên kết Đã nghỉ việc (`BR-10.4`) |
| `FEAT-11` | Quan hệ Giữa các Cá nhân | Xem Đơn vị; sửa Của mình | Xem Đơn vị + đọc tự động (`BR-35.4`, khung liên kết — `BR-02.1`) | Đơn vị + con | Xem Toàn WS | Xem Toàn WS; sửa Đơn vị | **Toàn quyền** | **Toàn quyền** | Ghi chiều ngược (`BR-11.2`) |
| `FEAT-12` | Vòng đời & Ma trận Chuyển đổi | Của mình, chỉ bước tiến lên (nguyên tắc 2; lên SQL cần thẩm định — `BR-15.6`); đánh dấu "Lead rác" chờ duyệt (`BR-12.4b`) | Chỉ xem giai đoạn (`BR-28.1`); không chuyển giai đoạn (`BR-02.2`) | Đơn vị + con, cộng quyền Duyệt vòng đời khách hàng (5.2); đồng phê duyệt Chiến dịch Tái tiếp cận (`BR-12.5b`) | Không chuyển giai đoạn thủ công (`BR-02.2`) — chỉ xem | Cấu hình thăng hạng tự động (`BR-15.4`, `BR-15.5`); không chuyển thủ công (`BR-02.2`); đồng phê duyệt Chiến dịch Tái tiếp cận (`BR-12.5b`) | **Toàn quyền** (gồm ngoại lệ gian lận `BR-12.8`) | **Toàn quyền** (gồm ngoại lệ gian lận `BR-12.8`) | Bước chuyển tự sinh (`BR-12.9`), sang Nurturing khi mọi Cơ hội Thua (`BR-12.3`), thăng MQL theo ngưỡng (`BR-15.5`) |
| `FEAT-13` | Lịch sử Giai đoạn | Xem Đơn vị | Xem Đơn vị | Đơn vị + con | Xem Toàn WS | Xem Toàn WS | **Toàn quyền** | **Toàn quyền** | Ghi tự động |
| `FEAT-14` | Chuyển đổi Tiềm năng | Của mình | — | Chuyển đổi Đơn vị + con; Hoàn tác Chuyển đổi trong Đơn vị — phần giao của ô Sửa Khách hàng với ô Xoá Cơ hội, Doanh nghiệp mức Đơn vị (`BR-14.5`) | — | Đơn vị (ô Sửa) | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-15` | Chấm điểm Tiềm năng | Xem điểm (`BR-15.4`) | Xem điểm (`BR-15.4`) | Xem điểm (`BR-15.4`) | Xem cấu hình, không sửa (`BR-15.4`) | Cấu hình quy tắc (`BR-15.4`) | Cấu hình quy tắc (`BR-15.4`) | Cấu hình quy tắc (`BR-15.4`) | Tính điểm tự động |
| `FEAT-16` | Suy giảm Điểm | — | — | Xử lý danh sách đề xuất Nurturing (`BR-16.4`), đưa Nurturing về Lead (`BR-16.5`) | — | Cấu hình mốc và tỷ lệ (`CFG-16-01`) | **Toàn quyền** | **Toàn quyền** | Áp suy giảm hằng ngày (`BR-16.1`, `BR-16.2`) |
| `FEAT-17` | Kiểm tra Trùng lặp | **Cho phép** (cảnh báo khi tạo/sửa) | **Cho phép** (cảnh báo khi tạo/sửa) | **Cho phép** (cảnh báo khi tạo/sửa) | **Cho phép** (cảnh báo khi tạo/sửa) | **Cho phép** (cảnh báo khi tạo/sửa) | **Cho phép**, gồm công cụ quét toàn không gian làm việc (`BR-17.4`) | **Cho phép**, gồm công cụ quét (`BR-17.4`) | Kiểm tra tức thì (`BR-17.1`) |
| `FEAT-18` | Xem trước Tác động Gộp | — | — | Đơn vị (ô Xoá) | — | — | **Cho phép** | **Cho phép** | — |
| `FEAT-19` | Thực thi Gộp | — | — | Đơn vị (ô Xoá, cả hai bản ghi — `BR-19.1`) | — | — | **Cho phép** | **Cho phép** | — |
| `FEAT-20` | Hoàn tác Gộp | — | — | — | — | — | **Cho phép** | **Cho phép** | — |
| `FEAT-21` | Khôi phục Gộp bị Gián đoạn | — | — | — | — | — | **Cho phép** | **Cho phép** | Liệt kê giao dịch gián đoạn (`BR-21.2`) |
| `FEAT-22` | Tải tệp Nhập khẩu | — | — | — | — | Đơn vị (ô Nhập) | **Cho phép** | **Cho phép** | Tự xóa tệp gốc hết hạn (`BR-33.8`) |
| `FEAT-23` | Trợ lý Ánh xạ Cột | — | — | — | — | Đơn vị (ô Nhập); cột Người phụ trách chịu ô Gán (`BR-23.5`) | **Cho phép** | **Cho phép** | — |
| `FEAT-24` | Xử lý Nhập & Báo cáo Lỗi | — | — | — | — | Đơn vị (ô Nhập) | **Cho phép** | **Cho phép** | Xử lý nền theo hàng đợi |
| `FEAT-25` | Xuất Dữ liệu | Của mình, chịu hạn mức ngày, không xuất KYC (`BR-25.5`) | — | Khi được cấp ô Xuất, không xuất KYC (`BR-25.4`) | Khi được cấp ô Xuất, không xuất KYC (`BR-25.4`) | Khi được cấp ô Xuất, không xuất KYC (`BR-25.4`) | **Cho phép**, gồm KYC khi có Người phụ trách Bảo vệ Dữ liệu đồng phê duyệt (`BR-25.4`) | **Cho phép**, gồm KYC khi có đồng phê duyệt (`BR-25.4`) | Tạo tệp nền (`BR-25.1`) |
| `FEAT-26` | Danh sách Hiển thị Dùng chung | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-27` | Dòng thời gian 360 độ | Xem Đơn vị | Xem Đơn vị + đọc tự động (`BR-35.4`) | Đơn vị + con | Xem Toàn WS | Xem Toàn WS | **Toàn quyền** | **Toàn quyền** | Hợp nhất sự kiện (`BR-27.1`) |
| `FEAT-28` | Ngữ cảnh Khách hàng Một chạm | Xem Đơn vị | Xem Đơn vị + đọc tự động (`BR-35.4`) — cơ chế truy cập chính | Đơn vị + con | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-29` | Kênh liên lạc & Trạng thái Tiếp cận | Xem Đơn vị; sửa Của mình | Xem Đơn vị + đọc tự động (`BR-35.4`) | Đơn vị + con | Xem Toàn WS | Xem Toàn WS; sửa Đơn vị | **Toàn quyền** | **Toàn quyền** | Cập nhật trạng thái tiếp cận (`BR-29.2`, `BR-29.3`) |
| `FEAT-30` | Đồng thuận & Định danh Dùng chung | Ghi nhận Của mình | Hạ đồng thuận trên bản ghi xem được hoặc đang có vé/hội thoại mở (`BR-35.4` (a)) | Ghi nhận Đơn vị + con | Ghi nhận Toàn WS | Ghi nhận Toàn WS; không đổi được `CFG-30-01`, `CFG-30-02` | **Toàn quyền** | **Toàn quyền** | Mặc định nhóm Tiếp thị khi thiếu khai báo (`BR-30.9`) |
| `FEAT-31` | Phân bổ Tiềm năng Tự động | Nhận việc từ hàng đợi của đơn vị mình (`BR-31.9` (c)) | — | Cấu hình quy tắc trong phạm vi ô Gán (`BR-31.5`, `BR-31.10`); gán từ hàng đợi | — | Nhận việc, gán từ hàng đợi trong Đơn vị (ô Gán) | **Cho phép**, gồm đổi đơn vị tiếp nhận của nguồn (`BR-31.9` (a)) | **Cho phép**, gồm đổi đơn vị tiếp nhận của nguồn | Thực thi phân bổ và thu hồi (`BR-31.1` – `BR-31.7`, `BR-31.10`) |
| `FEAT-32` | Theo dõi Nguồn gốc | Xem trường trên hồ sơ (`BR-32.1`) | Xem trường trên hồ sơ (`BR-32.1`) | Xem trường trên hồ sơ (`BR-32.1`) | Xem báo cáo (`BR-32.4`) | Xem báo cáo và phân tích hiệu quả đầu tư (`BR-32.4`) | **Toàn quyền**, gồm sửa nguồn theo lô (`BR-32.3b`) | **Toàn quyền**, gồm sửa nguồn theo lô (`BR-32.3b`) | Ghi nhận tự động (`BR-32.2`) |
| `FEAT-33` | Quyền Chủ thể Dữ liệu | — | Tiếp nhận, ghi nhận; gắn Hạn chế xử lý (`BR-30.6`) và hạ đồng thuận (`BR-30.10`) trên bản ghi xem được hoặc đang có vé/hội thoại mở (`BR-35.4` (a)); không thực thi ba loại còn lại (`BR-33.2`, `BR-33.7`) | Tiếp nhận, ghi nhận; gắn Hạn chế xử lý và hạ đồng thuận trong Đơn vị + con; không thực thi ba loại còn lại (`BR-33.2`, `BR-33.7`) | — | — | **Cho phép** (xử lý, xóa vĩnh viễn, đóng yêu cầu — `BR-33.1`) | **Cho phép** (xử lý, xóa vĩnh viễn, đóng yêu cầu — `BR-33.1`) | Khử định danh/xóa theo thời hạn lưu (`BR-01.5b`, `BR-33.6`); tự dỡ biện pháp phòng ngừa hết hạn (`BR-33.7` (a)) hoặc khi khách xác minh thành công (`BR-33.7` (e)) |
| `FEAT-34` | Chuyển giao & Bàn giao | Đề nghị chuyển bản ghi mình phụ trách, hiệu lực khi người nhận chấp nhận (`BR-34.1b`); tự khai báo nghỉ phép (`BR-34.6`) | Tự khai báo nghỉ phép (`BR-34.6`); bàn giao ngang nếu có bản ghi phụ trách (`BR-34.1b`) | Chuyển giao và giao lại Đơn vị + con bằng ô Gán theo `BR-34.1`; chọn cách xử lý bản ghi khi cấp dưới đổi Đơn vị chính (`BR-34.9`); với cấp dưới: khai báo nghỉ phép thay (`BR-34.6`) | Tự khai báo nghỉ phép (`BR-34.6`); bàn giao ngang nếu có bản ghi phụ trách (`BR-34.1b`) | Chuyển giao Đơn vị (ô Gán); tự khai báo nghỉ phép (`BR-34.6`) | **Toàn quyền**, gồm xử lý bản ghi khi tạm ngưng và rời workspace (`BR-34.4`) | **Toàn quyền** | Bật/tắt trạng thái không khả dụng tự động (`BR-34.6`) |
| `FEAT-35` | Chia sẻ & Đội ngũ Phụ trách | Bản ghi mình phụ trách (`BR-35.2`) | Nhận quyền đọc tự động (`BR-35.4`); chia sẻ bản ghi mình phụ trách nếu có (`BR-35.2`) | Đơn vị + con (ô Gán, `BR-35.2`) | Chia sẻ bản ghi mình phụ trách nếu có; được thêm vào đội ngũ chỉ với vai trò "Quan sát" (`BR-35.1`) | Chia sẻ trong Đơn vị (ô Gán); được thêm vào đội ngũ chỉ với vai trò "Quan sát" (`BR-35.1`) | **Toàn quyền** | **Toàn quyền** | Cấp/thu hồi quyền đọc tự động (`BR-35.4`); thu hồi chia sẻ hết hạn (`BR-35.3`) |
| `FEAT-36` | Ghi chú & Hoạt động | Tạo trên bản ghi xem được; đọc ghi chú nội bộ Của mình (`BR-36.1`) | Tạo trên bản ghi xem được hoặc đọc tự động; đọc ghi chú "Chung" và ghi chú ghim đã mở cho tuyến Hỗ trợ (`BR-36.7`); đọc ghi chú "Nội bộ đội bán hàng" của bản ghi mình phụ trách hoặc là thành viên đội ngũ (`BR-36.1`) | Đơn vị + con | Chỉ ghi chú "Chung" (`BR-36.1`) | Chỉ ghi chú "Chung" (`BR-36.1`) | **Toàn quyền** | **Toàn quyền** | Tự sinh bản ghi hoạt động (`BR-36.5`) |

**Ghi chú về ma trận:**

1. **Từ vựng:** mức truy cập trong ô theo đúng [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4 (viết tắt như 5.1). **"Khi được cấp ô …"** — không mặc định ở vai trò dựng sẵn, chỉ có khi doanh nghiệp cấp. **"+ đọc tự động (`BR-35.4`)"** — ngoài mức thông thường, còn đọc được (có nhật ký) khách đang có vé/hội thoại mở mà mình đang xử lý. **"Cho phép" / "Toàn quyền"** — có quyền thực hiện; "Toàn quyền" gồm cả cấu hình và các ngoại lệ nêu trong ô. **"—"** — không có quyền. Phần điều kiện vượt ra ngoài từ vựng này trong một ô bắt buộc dẫn chiếu mã `BR` (Mục 2.4, Nguyên tắc 1).

2. **Tầm nhìn rộng, sửa hẹp:** Marketing xem Toàn workspace nhưng không sửa; Nhân viên Kinh doanh xem cả đơn vị nhưng chỉ sửa bản ghi mình phụ trách. Mỗi lượt xem ngoài phạm vi phụ trách đều ghi nhật ký (`NFR-07` mục 13), áp theo cấu hình ô, không theo tên vai trò.

3. **Cột Tiến trình Hệ thống** ghi phần việc hệ thống thực hiện tự động, song song với thao tác của người dùng — không phải một cột loại trừ các cột còn lại. Với `FEAT-16` và `FEAT-31`, không vai trò nào thực thi bằng thao tác tay ngoài nhận việc và gán từ hàng đợi; các cột vai trò chỉ thể hiện quyền cấu hình hoặc xử lý kết quả.

4. **Ba vai trò dựng sẵn không có cột riêng ở 5.3:** **Chỉ xem bản ghi được giao** — chỉ xem các tính năng đọc (`FEAT-02`, `08`, `13`, `27`, `28`, `29`) trên bản ghi mình phụ trách, không có thao tác ghi nào; **Kiểm toán** — xem toàn workspace ở các tính năng đọc, không mở khóa mặt nạ, không xuất, không đọc ghi chú nội bộ và nhật ký kiểm toán của phân hệ (`NFR-14`); **Kiểm toán quyền** — không truy cập tính năng nào của phân hệ.

5. **Vai trò chức năng** (Quản lý Khách hàng Hiện hữu, Quản trị Chất lượng Dữ liệu) và chức danh Người phụ trách Bảo vệ Dữ liệu không có cột riêng vì không phải vai trò phân quyền; quyền hạn theo vai trò gốc và các quyền quản trị được cấp (Mục 2.2).

6. **`FEAT-33`:** người tiếp nhận được thực thi ngay hai loại yêu cầu **chỉ thu hẹp** phạm vi xử lý và **đảo lại được** — gắn Hạn chế xử lý và hạ đồng thuận — vì bảng loại yêu cầu tại `FEAT-33` cam kết thực thi "Tức thì"; nếu phải chờ Người có toàn quyền thì cam kết đó không có người thực thi. Đóng yêu cầu, phát hành phản hồi chính thức và ba loại yêu cầu còn lại (bản sao, chỉnh sửa, xóa vĩnh viễn) chỉ thuộc Người có toàn quyền, tuân thủ `BR-33.2`, `BR-33.7`.

7. **Quyền theo quan hệ với bản ghi** (Người phụ trách, thành viên Đội ngũ phụ trách, người đang xử lý vé/hội thoại, thành viên đơn vị tiếp nhận với bản ghi chưa phân công, chính người dùng tự khai báo) là trục cộng thêm vào quyền theo vai trò, theo Nguyên tắc 2 tại Mục 2.4 và thứ tự hợp nhất [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.6`.

8. **Mặc định và sàn:** toàn bộ ô và quyền quản trị ở 5.1, 5.2 là ma trận mặc định của các vai trò dựng sẵn. Doanh nghiệp điều chỉnh từng ô của vai trò dựng sẵn qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02` (`BR-29.4` của tài liệu đó), hoặc sao chép vai trò rồi sửa; mọi vai trò tự tạo đặt được mức riêng cho từng thao tác theo cùng mô hình. Các ràng buộc mang nhãn "sàn bắt buộc" trong các quy tắc nghiệp vụ của tài liệu này — gồm nhóm Định danh KYC (`BR-01.5b`) và quyền đọc nhật ký kiểm toán (`NFR-14`) mà [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.3` dẫn chiếu — không điều chỉnh vượt được.

---

## 6. Kịch bản chấp nhận tổng hợp (UAT)

**Quy ước nghiệm thu các quy tắc theo mốc thời gian dài.** Nhiều quy tắc có mốc tính bằng tuần, tháng hoặc năm (Thùng rác 30 ngày, hoàn tác gộp 90 ngày, Hồ sơ Tạm 90 ngày và trần 18 tháng, định danh KYC 24 tháng, dữ liệu không hoạt động 36 tháng). Các mốc này không thể chờ đủ thời gian thực trong một chu kỳ nghiệm thu, nên được nghiệm thu theo một trong hai cách, và bằng chứng của cả hai đều hợp lệ:

1. **Môi trường nghiệm thu được phép đặt tham số thời gian dưới sàn — trừ Kịch bản 22.** Trên môi trường phi vận hành, miền giá trị của các tham số **thời gian** tại Phụ lục B được mở rộng xuống mức phút/giờ để mô phỏng thời gian trôi. **Ngoại lệ tuyệt đối:** Kịch bản 22 tồn tại để chứng minh sàn không thể bị vi phạm, nên **bắt buộc chạy trên môi trường mang cấu hình vận hành thật**, nơi toàn bộ miền giá trị và sàn của Phụ lục B có hiệu lực đầy đủ.
2. **Cơ chế dịch thời gian của môi trường nghiệm thu**, nếu môi trường hỗ trợ.

Người kiểm thử ghi rõ trong biên bản đã dùng cách nào và giá trị tham số đã đặt.

### Kịch bản 1: Tạo mới Khách hàng Cá nhân & Tra cứu Hồ sơ 360 độ

1. Nhân viên kinh doanh bấm "Thêm khách hàng", nhập họ tên "Trần Thị Mai", email "mai.tran@vinafoods.vn", số điện thoại "0908123456", công ty "Công ty CP Thực phẩm Vina".
2. **Kỳ vọng:** Lưu thành công; số điện thoại được chuẩn hóa thành +84908123456; giai đoạn mặc định Lead (`BR-12.10`); Loại khách hàng theo mặc định của tenant (`BR-01.6`); Người phụ trách là nhân viên tạo và Đơn vị tổ chức là đơn vị của nhân viên tạo (`BR-01.3`); màn hình Hồ sơ 360 độ mở ra. Vì là Người phụ trách, nhân viên thấy **đầy đủ** số điện thoại và email công việc mà không cần mở khóa — cột (A) của `BR-04.3`.

---

### Kịch bản 2: Chuyển đổi Khách hàng Tiềm năng Một thao tác

1. Nhân viên thẩm định Lead "Trần Thị Mai" đã sẵn sàng mua hàng, bấm "Chuyển đổi Tiềm năng".
2. Trong hộp thoại: chọn tạo doanh nghiệp mới "Công ty CP Thực phẩm Vina"; chọn tạo Cơ hội "Hợp đồng Cung ứng Nông sản Q3", giá trị dự kiến 500.000.000 VND, giai đoạn "Đề xuất báo giá".
3. Bấm "Xác nhận chuyển đổi".
4. **Kỳ vọng:** Liên hệ lên Opportunity (bước nhảy bậc hợp lệ theo `BR-12.9`), doanh nghiệp và Cơ hội 500 triệu được tạo, nhân viên được chuyển tới màn hình Cơ hội. Lịch sử giai đoạn ghi sự kiện nguồn "Chuyển đổi Tiềm năng".
5. **Kịch bản phụ (tạo Cơ hội trực tiếp):** Nhân viên mở một khách ở MQL và tạo Cơ hội trực tiếp từ hồ sơ.
6. **Kỳ vọng:** Không có lỗi "Bước chuyển giai đoạn không hợp lệ"; khách tự động lên Opportunity (`BR-12.2`, `BR-12.9`).
7. **Kịch bản phụ (doanh nghiệp đã có Cơ hội đang mở — `BR-14.3`):** Một tuần sau, nhân viên khác cùng đơn vị — có ô (Cơ hội, Sửa) bao phủ Cơ hội ở bước 2 — chuyển đổi Lead "Nguyễn Văn Bình" (đã do nhân viên này phụ trách) cũng thuộc "Công ty CP Thực phẩm Vina", chọn cùng phễu với Cơ hội ở bước 2.
8. **Kỳ vọng:** Hộp thoại hiển thị Cơ hội "Hợp đồng Cung ứng Nông sản Q3" đang mở và **mặc định gắn Bình vào Cơ hội đó**, không tạo Cơ hội thứ hai. Dự báo doanh thu vẫn là 500 triệu. Người phụ trách của Cơ hội không đổi. Bình lên Opportunity (`BR-14.4`). **Đối chiếu:** nếu Cơ hội đó nằm ngoài ô (Cơ hội, Sửa) của nhân viên, hộp thoại không gợi ý và không tiết lộ nó; Cơ hội mới được tạo.
9. **Kịch bản phụ (chủ động tạo riêng):** Nhân viên xác nhận vẫn muốn tạo Cơ hội riêng cho Bình vì là dòng sản phẩm khác.
10. **Kỳ vọng:** Cơ hội thứ hai được tạo, và **quyết định được ghi nhật ký** kèm người thực hiện.

---

### Kịch bản 3: Nhận diện Trùng lặp, Gộp Bản ghi & Hoàn tác Gộp

1. Nhân viên tạo khách hàng mới với email "mai.tran@vinafoods.vn". **Kỳ vọng:** Trùng theo Tiêu chí chắc chắn → **chặn tạo mới** theo chính sách mặc định `BR-17.2`, dẫn nhân viên tới bản ghi hiện hữu; không sinh bản ghi trùng.
2. **Thiết lập tiền đề cho bước gộp:** Bản ghi trùng B tồn tại từ một nguồn hợp lệ theo `BR-17.2b` — một trong: (a) dữ liệu lịch sử nhập trước khi áp chính sách; (b) một lô nhập chọn "Tạo bản ghi mới dù trùng" theo `BR-23.3`; (c) bản ghi chỉ khớp Tiêu chí tham khảo (cùng họ tên và công ty, khác email).
3. Quản trị viên mở công cụ gộp giữa bản ghi A (cũ) và B (trùng), xem trước, chọn A làm Bản ghi Chính và xác nhận gộp.
4. **Kỳ vọng gộp:** B vào Thùng rác; toàn bộ ghi chú và công việc của B chuyển sang A; Sổ cái Hoàn tác Gộp có một mục mới.
5. Quản trị viên mở Lịch sử gộp và bấm "Hoàn tác gộp".
6. **Kỳ vọng hoàn tác:** B được khôi phục nguyên vẹn; công việc cũ của B trở về đúng B.

---

### Kịch bản 4: Nhập khẩu 10.000 dòng có Tự động Ánh xạ & Báo cáo Lỗi

1. Quản trị viên tải lên tệp "Danh_sach_khach_hang_2026.xlsx" dung lượng 15 MB chứa 10.000 dòng thu được từ một hội thảo.
2. Trợ lý ánh xạ nhận diện: "Họ và tên" → Họ tên, "Điện thoại" → Số điện thoại, "Email" → Email, "Công ty" → Tên doanh nghiệp (`BR-23.1`, `BR-23.2`).
3. **Ba khai báo bắt buộc trước khi chạy:** (a) chiến lược trùng lặp — chọn "Bỏ qua bản ghi trùng" (`BR-23.3`); (b) không chọn chiến lược cập nhật nên không cần trường tra cứu (`BR-23.4`); (c) Cơ sở đồng thuận từ A.11 — chọn "Dữ liệu từ sự kiện có phiếu đồng ý" (`BR-30.4`). Giao diện không cho bỏ trống các lựa chọn này.
4. **Kỳ vọng nếu chọn "Không có cơ sở đồng thuận" ở (c):** vẫn nhập được, toàn bộ lô nhận Chưa có đồng thuận, và kết quả nêu rõ lô không nhận tiếp thị trên các kênh doanh nghiệp yêu cầu Đồng ý trước (`BR-30.4`).
5. **Kỳ vọng về cưỡng chế đồng thuận (`BR-30.10`):** ở một lô khác chọn chiến lược cập nhật, một dòng mang "Đồng ý" cho bản ghi đang Từ chối — bản ghi **giữ Từ chối** và dòng đó có ghi chú "Không nâng được mức đồng thuận — thiếu bằng chứng của chủ thể".
6. Bấm "Bắt đầu nhập khẩu".
7. **Kỳ vọng:** Tiến trình chạy nền, tiến độ đạt 100%. Kết quả: 9.850 dòng thành công, 150 dòng lỗi.
8. Quản trị viên tải tệp báo cáo lỗi.
9. **Kỳ vọng:** Mỗi dòng lỗi có nguyên nhân cụ thể thuộc danh sách tại `BR-24.2`; không gộp thành một nguyên nhân chung.
10. **Kỳ vọng bổ sung (`BR-15.7` (a)):** 9.850 bản ghi mới **không** thăng hạng MQL tự động trong 24 giờ đầu và **không** tính vào cam kết thời gian phản hồi cho tới khi có tương tác đầu tiên — kể cả bản ghi đủ điểm hồ sơ.
11. **Kịch bản phụ (cột Người phụ trách và bản ghi giữ chỗ — `BR-23.5`):** Tệp có cột Người phụ trách: 500 dòng gán cho nhân viên N đang chờ chấp nhận lời mời (Đơn vị chính trong lời mời "Kinh doanh – Đà Nẵng"), 3 dòng gán cho người đang Tạm ngưng. **Kỳ vọng:** 3 dòng vào báo cáo lỗi kèm lý do; 500 bản ghi là bản ghi giữ chỗ cho N — người có mức Toàn workspace không thuộc nhóm ngoại lệ cũng không thấy; khi N chấp nhận với vai trò Nhân viên Kinh doanh, 500 bản ghi tự gán cho N; nếu N từ chối, chúng thành bản ghi chờ phân công của "Kinh doanh – Đà Nẵng".

---

### Kịch bản 5: Thiết lập Quan hệ Đa Doanh nghiệp

1. Ông "Nguyễn Văn Hùng" là Tổng giám đốc tại "Công ty Đầu tư Hùng Cường", đồng thời là Thành viên HĐQT tại "Ngân hàng Thương mại Á Châu".
2. Nhân viên mở hồ sơ ông Hùng → thẻ "Doanh nghiệp trực thuộc" → "Thêm liên kết".
3. Chọn "Ngân hàng Thương mại Á Châu", chức danh "Thành viên HĐQT", vai trò "Cố vấn" từ A.5.
4. **Kỳ vọng:** Hồ sơ ông Hùng hiển thị đủ hai công ty; hồ sơ Ngân hàng Á Châu có ông Hùng trong danh sách nhân sự.

---

### Kịch bản 6: Bảo vệ Dữ liệu Nhạy cảm & Mở khóa Mặt nạ

1. **Tiền đề cấu hình:** Chủ sở hữu cùng Người phụ trách Bảo vệ Dữ liệu đã **bật nhóm Định danh KYC** kèm khai báo mục đích (`BR-01.5b`, `CFG-01-02`).
2. **Quản lý Kinh doanh B** — bản ghi "Trần Thị Mai" nằm trong "Đơn vị của mình" của B, nhưng B **không phải Người phụ trách** và **không** thuộc Đội ngũ phụ trách — mở hồ sơ (do nhân viên A phụ trách). B **chưa được cấp** quyền Mở khóa mặt nạ. *Chọn vai trò này vì cột (B) đòi một người trong phạm vi dữ liệu nhưng không phải Người phụ trách và không thuộc Đội ngũ phụ trách; với Nhân viên Kinh doanh, "Của mình" chỉ gồm bản ghi mình phụ trách và bản ghi được chia sẻ — mà được chia sẻ lại đưa họ vào Đội ngũ phụ trách.*
3. **Kỳ vọng (cột B):** Số điện thoại hiện "090****567", email công việc hiện "m***@vinafoods.vn", Số Căn cước công dân **che hoàn toàn**.
4. **Kỳ vọng đối chiếu khi nhóm KYC tắt:** nếu tenant không bật nhóm KYC, các trường thuộc nhóm không được lưu và không hiển thị với mọi vai trò — mức "Ẩn trường" áp cho cả bốn cột (`BR-01.5b`).
5. **Kỳ vọng đối chiếu (cột A):** Nhân viên A mở cùng hồ sơ và thấy **đầy đủ** số điện thoại và email công việc mà không cần mở khóa.
6. Một Quản lý Kinh doanh khác, **đã được cấp** quyền Mở khóa mặt nạ, mở hồ sơ và bấm biểu tượng mở khóa lúc 14:30.
7. **Kỳ vọng:** Số điện thoại hiện đầy đủ "0908123456"; nhật ký ghi Quản lý đã mở khóa **trường nào** của bản ghi nào lúc 14:30 — **không** lưu giá trị thật (`BR-04.4`, `NFR-07`).
8. **Kịch bản phụ (hạn mức — `BR-04.5`):** Cùng người dùng mở khóa liên tiếp tới bản ghi thứ 51 trong ngày.
9. **Kỳ vọng:** Thao tác mở khóa bị **tạm chặn đến hết ngày**; Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu nhận cảnh báo; lượt này có trong báo cáo truy cập bất thường. Màn hình cấu hình **không** có lựa chọn "không giới hạn".
10. **Kịch bản phụ (liên lạc không cần mở khóa — `BR-04.6`):** B bấm "Gọi" ngay trên hồ sơ dù số điện thoại đang che một phần.
11. **Kỳ vọng:** Cuộc gọi thực hiện được, không cần mở khóa, không tính vào hạn mức mở khóa, nhưng hành động liên lạc **được ghi nhật ký**.
12. **Kịch bản phụ (ba mức đọc nhật ký — `NFR-14`):** Lần lượt bốn người mở nhật ký kiểm toán của bản ghi "Trần Thị Mai": Quản lý Kinh doanh của A; Quản trị viên khi **không** có yêu cầu nào đang mở trên bản ghi; Quản trị viên khi **đang** xử lý một yêu cầu chủ thể dữ liệu trên đúng bản ghi đó; Người phụ trách Bảo vệ Dữ liệu.
13. **Kỳ vọng:** Quản lý Kinh doanh không truy cập được. Quản trị viên khi không có yêu cầu mở không truy cập được; khi đang xử lý yêu cầu thì chỉ đọc được nhật ký của đúng bản ghi đó và chỉ trong thời gian yêu cầu còn mở. Người phụ trách Bảo vệ Dữ liệu đọc được toàn phần. Mọi lượt đọc đều **sinh một bản ghi nhật ký mới**. Thử cấu hình mở quyền đọc toàn phần cho Quản lý Kinh doanh — **bị từ chối**, nêu rõ `NFR-14` là quy tắc cố định (phép thử C theo Kịch bản 22).
14. **Kịch bản phụ (quyền xuất của Nhân viên Kinh doanh — `BR-25.5`, `BR-25.4`):** Nhân viên A xuất danh sách khách mình phụ trách, lần lượt thử: (i) chọn thêm trường Số Căn cước công dân; (ii) xuất 2.001 bản ghi trong một ngày; (iii) xin phê duyệt của Quản lý Kinh doanh rồi xuất tiếp.
15. **Kỳ vọng:** (i) trường Số Căn cước công dân **không có** trong danh sách trường chọn được, và không có đường phê duyệt nào mở được. (ii) Lần xuất làm vượt hạn mức 2.000 bản ghi/ngày (`CFG-25-02`) bị giữ lại và chuyển thành yêu cầu phê duyệt gửi **quản lý trực tiếp của A** — không phải Quản lý Marketing. (iii) Sau phê duyệt, A xuất tiếp được nhưng tổng trong ngày **không vượt hai lần hạn mức**. Cả ba lần thao tác đều được ghi nhật ký (`BR-25.3`).

---

### Kịch bản 7: Hoàn tác Chuyển đổi trong 24 giờ

1. Lúc 09:00, nhân viên chuyển đổi nhầm Lead "Lê Văn Bình", tạo doanh nghiệp mới "Công ty XYZ" và Cơ hội "Cơ hội ABC" — chưa thao tác gì thêm trên Cơ hội.
2. Lúc 10:30 cùng ngày, Quản lý Kinh doanh phát hiện sai sót, mở Cơ hội "Cơ hội ABC" và bấm "Hoàn tác Chuyển đổi".
3. **Kỳ vọng:** Cho phép vì Cơ hội chưa có hoạt động. "Cơ hội ABC" và "Công ty XYZ" bị xóa mềm; ông Bình trở về đúng Lead — **không** có lỗi bước chuyển không hợp lệ (`BR-12.9`) và **không** bị hỏi lý do hạ hạng (`BR-12.7`), chỉ bắt buộc chọn Lý do hoàn tác từ A.15; nhật ký ghi đầy đủ.
4. **Kịch bản phụ (từ chối hoàn tác):** Nếu trước bước 2 nhân viên đã thêm 1 ghi chú vào Cơ hội, hệ thống từ chối với thông báo "Không thể hoàn tác: Cơ hội đã phát sinh hoạt động".
5. **Kịch bản phụ (hết hạn):** Lúc 09:05 hôm sau, hành động "Hoàn tác Chuyển đổi" không còn hiển thị.

---

### Kịch bản 8: Suy giảm Điểm Tiềm năng theo Thời gian

1. **Tiền đề:** Khách "Phạm Thị Lan" có **Điểm Hồ sơ 20** và **Điểm Tương tác 40** (tổng 60, đang ở MQL). Tương tác gần nhất là **ngày D**; bản ghi chưa từng bị suy giảm.
2. Tiến trình chạy lúc 02:00 **ngày D+14** (theo múi giờ không gian làm việc).
3. **Kỳ vọng:** Điểm Tương tác 40 → **36**; tổng **56**, vẫn trên Ngưỡng MQL.
4. Tiến trình chạy các ngày **D+15 đến D+29**.
5. **Kỳ vọng:** Điểm Tương tác **giữ nguyên 36** (`BR-16.1`).
6. Tiến trình chạy lúc 02:00 **ngày D+30**.
7. **Kỳ vọng:** Điểm Tương tác 36 → **27** (`BR-16.2`); tổng **47**.
8. **Kỳ vọng — không tự hạ giai đoạn (`BR-16.4`):** dù tổng điểm rơi dưới Ngưỡng MQL ở bất kỳ lần chạy nào, hệ thống **không** tự hạ giai đoạn; riêng nhánh điểm nguội, việc sang Nurturing cần thao tác tường minh của Quản lý Kinh doanh. Nhánh "toàn bộ Cơ hội Thua" tại `BR-12.3` do hệ thống tự chuyển và được kiểm tại Kịch bản 12.
9. **Kỳ vọng sàn điểm (`BR-16.3`):** qua nhiều khoảng không tương tác liên tiếp, Điểm Tương tác tiến về 0 nhưng **không bao giờ** âm.
10. **Kỳ vọng khi tổng điểm rơi dưới 40 (`BR-16.4`):** hồ sơ mang cảnh báo "Đã nguội" và vào danh sách đề xuất chuyển Nurturing; Quản lý Kinh doanh chuyển thì bắt buộc chọn lý do từ A.16. **Đối chiếu:** khách ở Customer/Evangelist hoặc Opportunity **không** vào danh sách này; với Opportunity, khi toàn bộ Cơ hội Thua thì lý do lấy từ A.2 theo `BR-12.3`.

---

### Kịch bản 9: Phân bổ Khách hàng Tiềm năng theo Vùng địa lý

1. Một khách mới được tạo tự động từ biểu mẫu website với quốc gia Việt Nam, tỉnh/thành Đà Nẵng, không có người tạo trực tiếp. Biểu mẫu có đơn vị tiếp nhận "Kinh doanh" (gồm các đơn vị con); quy tắc vùng địa lý do Quản lý M chịu trách nhiệm, ô (Khách hàng, Gán) của M = Đơn vị và các đơn vị con tại "Kinh doanh".
2. Hệ thống áp thứ tự ưu tiên tại `BR-31.3b`: kiểm tra Người phụ trách hiện hữu (không khớp vì là khách mới), rồi quy tắc vùng địa lý.
3. **Kỳ vọng:** Có nhân viên phụ trách Miền Trung đang khả dụng → khách được gán trực tiếp cho nhân viên đó, không qua chia vòng.
4. **Kịch bản phụ (không khớp quy tắc nào):** Một khách khác từ trợ lý trò chuyện tự động không có thông tin quốc gia/tỉnh/ngành khớp quy tắc nào, và không còn nhân viên khả dụng trong nhóm.
5. **Kỳ vọng:** Khách nằm lại trong hàng đợi "Chưa phân công" của đơn vị tiếp nhận của trợ lý trò chuyện (`BR-31.4`, `BR-31.9`); người phụ trách đơn vị đó nhận thông báo. Một nhân viên của đơn vị có (Khách hàng, Xem) = Chỉ của mình vẫn thấy khách trong hàng đợi và bấm "Nhận việc" được vì ô Gán = Chỉ của mình; sau khi nhận, khách thuộc Đơn vị chính của người nhận.
6. **Kịch bản phụ (chống trùng chủ — `BR-31.6`):** Một khách mới từ biểu mẫu website có email trùng chị "Trần Thị Mai" do nhân viên A phụ trách.
7. **Kỳ vọng:** Không tạo bản ghi mới, không chia cho người khác; yêu cầu được ghi vào dòng thời gian của chị Mai; A nhận thông báo và một Yêu cầu chờ xử lý được tạo.
8. **Kịch bản phụ (phân bổ chịu ô Gán — `BR-31.10`):** Quản lý M, ô (Khách hàng, Gán) = Đơn vị và các đơn vị con tại "Miền Trung", tạo quy tắc vùng địa lý cho hàng đợi của biểu mẫu. **Kỳ vọng:** M chỉ chọn được người nhận thuộc "Miền Trung" và các đơn vị con; nhân viên "Miền Nam" không có trong danh sách. Khi M bị tạm ngưng, quy tắc tạm dừng, khách mới nằm lại hàng đợi chưa phân công, và người phụ trách đơn vị tiếp nhận cùng Người có toàn quyền nhận thông báo.

---

### Kịch bản 10: Đồng thuận theo Kênh & Nguyên tắc Nghiêm ngặt nhất khi Gộp

1. Chị "Trần Thị Mai" (bản ghi A) Đồng ý nhận tin qua **email, SMS và Zalo**. Chị bấm "Hủy nhận tin" trong một email chiến dịch.
2. **Kỳ vọng:** Chỉ kênh email chuyển sang Từ chối nhận tin; SMS và Zalo vẫn Đồng ý. Bằng chứng gồm thời điểm, nguồn (liên kết Hủy nhận tin trong email — A.8), phiên bản điều khoản và tiến trình ghi nhận (`BR-30.3`).
3. Ngay sau đó chị gửi một vé hỗ trợ qua email. **Kỳ vọng:** Nhân viên Hỗ trợ **vẫn gửi được** phản hồi vé qua email, vì phản hồi vé thuộc nhóm Giao dịch & Dịch vụ (`BR-30.5`).
4. **Kỳ vọng — mặc định an toàn và trần người nhận (`BR-30.9`, `BR-30.7` (b)):** gửi một lượt thư **không khai báo nhóm mục đích** — hệ thống xếp vào nhóm Tiếp thị và **chặn** với chị Mai. Gửi một lượt thư cá nhân cho **11 người nhận** — vượt trần `CFG-30-02`, lượt gửi thuộc nhóm Tiếp thị và chị Mai bị loại khỏi danh sách nhận. Mở cấu hình `CFG-30-02` thử đặt **20** — bị từ chối, miền chỉ nhận 1–10. Tìm cấu hình cho nhóm Tiếp thị thoát chi phối của Từ chối nhận tin (`CFG-30-01`) — **không tồn tại**.
5. Cùng lúc, Nhân viên Kinh doanh gửi **thư báo giá** cho chị Mai. **Kỳ vọng:** thư gửi được và **không** bị trần 5 người nhận, vì thư báo giá thuộc nhóm Giao dịch & Dịch vụ (`BR-30.5`, `BR-30.7`). Chị Mai không nhận email chiến dịch tiếp thị nào.
6. Sau đó phát hiện bản ghi B trùng của chị Mai (từ nguồn hợp lệ theo `BR-17.2b` — ví dụ nhập danh bạ sự kiện bằng một email khác), trong đó kênh email Đồng ý nhận tin.
7. Quản trị viên chọn **B làm Bản ghi Chính** và gộp; tại Xem trước chủ động chọn giữ "Đồng ý" của B.
8. **Kỳ vọng (bắt buộc):** Sau gộp, kênh email của Bản ghi Chính vẫn là **Từ chối nhận tin** — hệ thống ghi đè lựa chọn thủ công theo `BR-19.6` và hiển thị giải thích. Bằng chứng đồng thuận của **cả hai** bản ghi còn đầy đủ.
9. **Kịch bản phụ (chặn gộp):** Nếu B đang có yêu cầu xóa dữ liệu chưa hoàn tất, thao tác gộp bị từ chối kèm thông báo phải xử lý xong yêu cầu trước (`BR-19.6`, `BR-33.4`).

---

### Kịch bản 11: Xóa mềm Khách hàng, Ảnh hưởng Thực thể Con & Phục hồi

1. Ông "Lê Văn Bình" đang có 1 vé hỗ trợ mở và 1 Cơ hội mở. Quản trị viên xóa ông Bình.
2. **Kỳ vọng:** Bản ghi vào Thùng rác, biến mất khỏi danh sách và báo cáo thông thường. Vé và Cơ hội **không bị xóa** mà mang nhãn "Khách hàng trong thùng rác"; gửi phản hồi công khai trên vé bị khóa (`BR-05.5`).
3. Quản trị viên mở Thùng rác, thấy bản ghi kèm ngày xóa và người xóa, bấm "Khôi phục".
4. **Kỳ vọng:** Bản ghi trở lại nguyên vẹn, nhãn cảnh báo được gỡ, phản hồi công khai mở lại, nhật ký ghi thao tác khôi phục.
5. **Kịch bản phụ (xóa doanh nghiệp có đa liên kết — `BR-09.1`):** Xóa "Công ty A" đang là Doanh nghiệp chính của ông Bình, trong khi ông còn liên kết đang hoạt động với "Công ty B".
6. **Kỳ vọng:** Liên kết với Công ty A chuyển Tạm ngưng (không mất chức danh); Công ty B trở thành Doanh nghiệp chính; ông Bình không bị xóa và không vào danh sách "Liên hệ chưa gắn doanh nghiệp".

---

### Kịch bản 12: Ma trận Chuyển đổi & Xử lý khi Mọi Cơ hội Thất bại

1. Chị "Nguyễn Thị Hoa" ở Opportunity với 2 Cơ hội đang mở, chưa từng là Customer.
2. Cả 2 Cơ hội bị đóng Thua.
3. **Kỳ vọng:** **Không** hạ về Lead/MQL; chuyển sang Nurturing và **bắt buộc** nhân viên chọn Lý do không chuyển đổi từ **A.2** (`BR-12.3`) — không phải A.16.
4. Nhân viên thử chuyển chị Hoa từ Nurturing sang Evangelist.
5. **Kỳ vọng:** **Bị từ chối** "Bước chuyển giai đoạn không hợp lệ", nêu các giai đoạn hợp lệ từ Nurturing (Lead, MQL, SQL, Opportunity, Customer, Disqualified). Evangelist chỉ đến được từ Customer.
6. **Kỳ vọng đối chiếu (nguyên tắc 1):** nếu chị Hoa phát sinh một Cơ hội mới, hệ thống **tự động** chuyển Nurturing → Opportunity không báo lỗi; nếu Cơ hội đó Thắng, tự chuyển tiếp lên Customer (`BR-12.9`).
7. **Kịch bản phụ (khách chính thức không bị hạ hạng):** Chị "Trần Thị Mai" đã là Customer có thêm 1 Cơ hội bán thêm bị Thua.
8. **Kỳ vọng:** Giai đoạn vẫn là Customer (`BR-12.3`).
9. **Kịch bản phụ (hạ hạng có kiểm soát):** Nhân viên Kinh doanh thử hạ một khách từ MQL về Lead.
10. **Kỳ vọng:** Không khả dụng với Nhân viên; khi Quản lý Kinh doanh thực hiện, bắt buộc chọn lý do từ A.3 và lịch sử giai đoạn ghi nhận (`BR-12.7`).
11. **Kịch bản phụ (Lead rác — `BR-12.4b`):** Nhân viên nhận Lead tên "asdf asdf", số "0000000000", bấm **"Lead rác"** với lý do "Thông tin giả/Spam/Lừa đảo".
12. **Kỳ vọng ngay lập tức:** Đồng hồ cam kết **dừng**; bản ghi **ra khỏi mẫu đo `KPI-03`**; không thu hồi/phân bổ lại. Giai đoạn **vẫn đứng nguyên**, chưa phải Disqualified.
13. **Kỳ vọng sau 5 ngày làm việc không ai duyệt:** bản ghi **vẫn đứng nguyên giai đoạn**, **không** tự sang Disqualified; hàng đợi chờ duyệt leo thang lên Quản trị viên kèm báo cáo tồn đọng. Quản lý duyệt thì bản ghi mới sang Disqualified; Quản lý từ chối thì dấu "Lead rác" được gỡ và đồng hồ chạy lại từ thời điểm từ chối.
14. **Kỳ vọng đối chiếu (phạm vi):** thử "Lead rác" trên một hồ sơ ở Customer — hành động **không khả dụng**; loại khách đã trả tiền vì gian lận chỉ Quản trị viên/Chủ sở hữu làm được (`BR-12.8`).

---

### Kịch bản 13: Thăng hạng Tự động theo Ngưỡng điểm & Chuyển giao Marketing → Kinh doanh

1. **Tiền đề:** Lead "Phạm Văn Nam" có tổng 35 điểm, gồm **Điểm Hồ sơ 35** (email doanh nghiệp +10, số di động +10, ngành mục tiêu +15) và **Điểm Tương tác 0**.
2. Khách mở email chiến dịch (+5) rồi nhấp liên kết (+10) → tổng 50, Điểm Tương tác 15.
3. **Kỳ vọng:** Thỏa **cả hai** điều kiện (`BR-15.5`) → **tự động** lên MQL, Marketing nhận thông báo; lịch sử ghi người thực hiện "Hệ thống".
4. **Kỳ vọng đối chiếu điều kiện kép (`BR-15.7`):** nếu khách chỉ mở email (Điểm Tương tác 5, tổng 40) thì **không** thăng hạng dù tổng đã đạt 40.
5. Khách đặt lịch trình diễn sản phẩm (+30), tổng 80.
6. **Kỳ vọng:** Vượt Ngưỡng SQL → nhãn "Sẵn sàng chuyển Sales", vào hàng đợi thẩm định, **không** tự lên SQL (`BR-15.6`).
7. **Kịch bản phụ (`BR-15.3`):** Khách mở cùng một email 5 lần trong ngày.
8. **Kỳ vọng:** Chỉ cộng điểm **1 lần** cho "mở email" trong ngày; tổng không vượt 100.
9. **Kịch bản phụ (`BR-15.7`):** Quản trị viên nhập 10.000 danh bạ hội thảo, trong đó 3.000 bản ghi có Điểm Hồ sơ 40 và Điểm Tương tác 0.
10. **Kỳ vọng:** **Không** bản ghi nào trong 3.000 lên MQL; toàn bộ lô không thăng hạng tự động trong 24 giờ đầu và không tính vào cam kết phản hồi cho tới khi có tương tác đầu tiên.

---

### Kịch bản 14: Nhân viên Nghỉ việc & Bàn giao Danh bạ

1. Nhân viên A nghỉ việc, đang phụ trách 450 khách hàng, 20 doanh nghiệp, 8 Cơ hội mở và 3 vé hỗ trợ mở.
2. Quản trị viên mở quy trình rời workspace cho A với lựa chọn "Tạm ngưng ngay".
3. **Kỳ vọng:** A bị tạm ngưng và đăng xuất **ngay**, không bị chặn vì còn bản ghi (`BR-34.4` (a)); nút Gỡ bị vô hiệu cho tới khi bước "450 khách hàng, 20 doanh nghiệp" hoàn tất (`BR-34.4` (c)). Khách hàng tiềm năng chưa chốt mà A nhận từ hàng đợi biểu mẫu có thêm lựa chọn "Trả về hàng đợi" (`BR-31.9` (d)); khách do A tự tạo thì không.
4. Quản lý Kinh doanh (ô Gán bao phủ khách của A) lọc "Người phụ trách là A", chọn chuyển cho B — B Đang hoạt động và có ô (Khách hàng, Sửa) khác Không có — phạm vi "kèm Cơ hội và Vé đang mở".
5. **Kỳ vọng:** Bước xem trước hiển thị đúng 450, 20, 8, 3 (`BR-34.2`) và danh sách các thực thể sẽ bị bỏ qua vì Quản lý thiếu ô Gán của loại dữ liệu đó hoặc B không đạt điều kiện nhận (`BR-34.3`) — ví dụ 3 vé hỗ trợ khi Quản lý có (Vé hỗ trợ, Gán) = Không có. Sau xác nhận, mọi thực thể đủ điều kiện sang B, vé vẫn thuộc đơn vị tiếp nhận của hàng đợi; B nhận thông báo; nhật ký ghi đủ danh sách bản ghi đã chuyển và bị bỏ qua (`BR-34.7`).
6. **Kỳ vọng bổ sung:** Báo cáo "Bản ghi không có Người phụ trách hoạt động" (`BR-34.5`) không còn bản ghi nào của A; nút Gỡ khả dụng sau khi các bước bắt buộc khác của quy trình rời workspace hoàn tất.
7. **Kịch bản phụ (tự khai báo nghỉ phép — `BR-34.6`):** Lần lượt **bốn** người tự khai báo nghỉ phép 5 ngày kèm người xử lý thay: một Nhân viên Kinh doanh, một Nhân viên Hỗ trợ, một Nhân viên Marketing, một Quản lý Marketing. **Kỳ vọng:** cả bốn lưu thành công; trong khoảng nghỉ họ không nhận phân bổ mới (`BR-31.1`) và yêu cầu chờ xử lý chuyển cho người xử lý thay (`BR-31.6`), quyền phụ trách chính không đổi. **Đối chiếu:** quản lý trực tiếp khai báo được thay cho cấp dưới; Nhân viên Kinh doanh không khai báo được thay cho đồng nghiệp.
8. **Kịch bản phụ (không khả dụng tự động):** Nhân viên E đi công tác dài, **không đăng nhập 14 ngày liên tiếp** (`CFG-34-02` mặc định), không khai báo nghỉ phép.
9. **Kỳ vọng:** E ở trạng thái **không khả dụng**; người xử lý thay mặc định là Quản lý trực tiếp của E (`BR-34.6` (a)); E và Quản lý nhận thông báo (`BR-34.6` (b)); E không nhận phân bổ mới và yêu cầu chờ xử lý của khách do E phụ trách chuyển ngay cho Quản lý. **Không** bản ghi nào đổi Người phụ trách.
10. **Kỳ vọng khi E quay lại:** ngay ở lần đăng nhập kế tiếp, trạng thái không khả dụng **tự hết hiệu lực** (`BR-34.6` (c)).

---

### Kịch bản 15: Tư vấn viên Truy cập Ngữ cảnh Khách hàng ngoài Phạm vi Dữ liệu

1. Chị "Trần Thị Mai" (do nhân viên A ở phòng khác phụ trách) gửi tin nhắn qua trò chuyện trực tuyến.
2. Tư vấn viên C tiếp nhận hội thoại và mở khung Ngữ cảnh Khách hàng.
3. **Kỳ vọng:** C **xem được** hồ sơ 360, dòng thời gian và khung ngữ cảnh của chị Mai dù bản ghi ngoài phạm vi thông thường của C (`BR-35.4`). Khung phản hồi trong ngưỡng 150 mili giây (`NFR-02`).
4. **Kỳ vọng về bảo mật:** C **không sửa được dữ liệu nghiệp vụ** (tên, kênh liên lạc, giai đoạn, người phụ trách, thẻ) — ngoại lệ duy nhất là hai thao tác tại `BR-35.4` (a), kiểm ở bước 5. Số điện thoại và email công việc **che một phần** theo **cột (C)** — đủ để xác minh và liên lạc trong hệ thống (`BR-04.6`); KYC che hoàn toàn; lượt truy cập được ghi nhật ký (`BR-35.4` (c)).
5. **Kỳ vọng về ngoại lệ ghi (`BR-35.4` (a)):** trong hội thoại, chị Mai nói "đừng gửi email tiếp thị cho tôi nữa". C hạ đồng thuận email xuống Từ chối nhận tin — **được phép**; C gắn được Hạn chế xử lý nếu khách yêu cầu; mỗi lượt ghi nhật ký. **Đối chiếu:** C thử nâng lại lên Đồng ý nhận tin — **bị từ chối** (`BR-30.10`).
6. Hội thoại được đóng.
7. **Kỳ vọng:** Quyền đọc của C tự hết hiệu lực (`BR-35.4` (d)). C thử hạ đồng thuận lần nữa — **bị từ chối**. C mở lại hồ sơ chị Mai — thấy **thông tin tối thiểu để nhận diện** (tên viết tắt, tên Người phụ trách, đơn vị phụ trách, thời điểm tương tác gần nhất) kèm **ba hành động** "Yêu cầu quyền truy cập", "Yêu cầu nhận bàn giao", "Đề nghị gộp"; **không** có thông báo "không có quyền truy cập" (`BR-17.3`).
8. **Kỳ vọng phân luồng (`BR-17.3`, `BR-35.3b`):** C bấm "Đề nghị gộp" — yêu cầu gửi tới **Quản trị Chất lượng Dữ liệu hoặc Quản trị viên**; A chỉ nhận thông báo. Quá hạn thì **chỉ leo thang**, hệ thống **không** tự gộp.
9. C bấm "Yêu cầu quyền truy cập" (xin quyền đọc) và không ai phản hồi.
10. **Kỳ vọng (`BR-17.2c`):** quá 4 giờ làm việc, yêu cầu leo thang lên Quản lý Kinh doanh của A; quá thời hạn thứ hai, hệ thống **tự cấp quyền đọc tạm có ghi nhật ký** cho C, hiệu lực tối đa 7 ngày.

---

### Kịch bản 16: Gộp Khách hàng Chính thức với Lead trùng — Bảo vệ Giai đoạn và Đồng thuận

1. Ông "Nguyễn Văn Hùng" là Customer (bản ghi A, do nhân viên A phụ trách, email Từ chối nhận tin). Có bản ghi B trùng ở Lead (email Đồng ý nhận tin, do nhân viên B phụ trách) — phát sinh hợp lệ theo `BR-17.2b`: ông từng để lại thông tin ở hội thảo bằng email cá nhân khác email công việc trên A, nên không bị chặn khi nhập; hai bản ghi chỉ được nhận ra là trùng qua số điện thoại đã chuẩn hóa.
2. Quản trị viên gộp, **chọn B (Lead) làm Bản ghi Chính**, tại Xem trước chủ động chọn giữ giai đoạn Lead và "Đồng ý".
3. **Kỳ vọng (không thể ghi đè):** Bản ghi sau gộp ở **Customer** (`BR-19.8`) và email **Từ chối nhận tin** (`BR-19.6`); hệ thống giải thích hai lựa chọn thủ công đã bị ghi đè kèm lý do.
4. **Kỳ vọng về quyền phụ trách:** Người phụ trách sau gộp là người của bản ghi có tương tác gần nhất (`BR-19.9`); **cả A và B đều nhận thông báo**; nhật ký ghi đầy đủ.
5. **Kỳ vọng về dữ liệu:** Điểm tiềm năng lấy giá trị cao hơn; thẻ được hợp nhất; nguồn gốc của Bản ghi Chính giữ nguyên, nguồn gốc bản ghi phụ lưu trong sổ cái gộp (`BR-19.5`, `BR-19.10`).
6. Sau 45 ngày, Quản trị viên phát hiện gộp sai và bấm "Hoàn tác gộp".
7. **Kỳ vọng:** Thành công vì còn trong thời hạn 90 ngày và bản ghi phụ chưa bị dọn dẹp (`BR-20.3`, `BR-05.6` (b)). Email công việc rời B và trở về A (bản ghi phụ); nếu trong 45 ngày không có lượt ghi nhận đồng thuận nào, A trở về email Từ chối nhận tin và B trở về email Đồng ý như ảnh chụp trước khi gộp (`BR-20.2`).

---

### Kịch bản 17: Yêu cầu Xóa Dữ liệu Cá nhân của Chủ thể Dữ liệu

1. Một người tự nhận là chị "Lê Thị Hồng" gửi email yêu cầu xóa toàn bộ dữ liệu cá nhân. Chị Hồng ở Customer và còn 1 hợp đồng hiệu lực.
2. Nhân viên Hỗ trợ tiếp nhận và ghi nhận yêu cầu.
3. **Kỳ vọng:** Nhân viên Hỗ trợ tạo được bản ghi theo dõi (loại "Xóa vĩnh viễn", hạn 30 ngày) nhưng **không** thực thi được thao tác xóa (Mục 5, ghi chú 5).
4. Quản trị viên mở yêu cầu và thử xóa ngay.
5. **Kỳ vọng:** Bị **chặn** cho tới khi ghi nhận phương thức xác minh danh tính (`BR-33.7`); vì chị Hồng là Customer, còn cần **hai người khác nhau** (người xác minh và người phê duyệt).
6. Sau khi xác minh qua email đã xác thực của chính hồ sơ, Quản trị viên tiếp tục xử lý.
7. **Kỳ vọng (từ chối một phần):** Vì còn hợp đồng hiệu lực, hệ thống **từ chối một phần** theo `BR-33.3`: xóa dữ liệu tiếp thị và kênh liên lạc không cần thiết, giữ dữ liệu tối thiểu phục vụ hợp đồng, bắt buộc ghi lý do từ chối một phần.
8. **Kỳ vọng về phạm vi xóa — kiểm chứng theo đúng 14 hàng của bảng `BR-33.8`:** hệ thống phát hành **Biên bản Hoàn tất Xử lý** liệt kê từng hàng kèm trạng thái và số lượng đối tượng đã xử lý. Người kiểm thử đối chiếu từng hàng:
   - Hàng 1: mở lại hồ sơ → phần dữ liệu không thuộc nghĩa vụ hợp đồng không còn.
   - Hàng 2: tra dòng thời gian và ghi chú → không còn nội dung chứa dữ liệu cá nhân ngoài phần được giữ.
   - Hàng 3: mở ảnh chụp trong sổ cái gộp → còn cấu trúc, không còn dữ liệu cá nhân.
   - Hàng 4: dùng lại đường tải một **tệp xuất** còn hạn → bị từ chối.
   - Hàng 5: tra nhật ký kiểm toán → còn mã bản ghi và loại thao tác, không còn dữ liệu nhận diện.
   - Hàng 6: tra bằng chứng đồng thuận → còn thời điểm, nguồn, phiên bản điều khoản.
   - Hàng 7: bản sao lưu → xác nhận khách nằm trong danh sách yêu cầu xóa sẽ chạy lại khi phục hồi, và mô phỏng một lần phục hồi để thấy dữ liệu đã xóa không quay lại; biên bản **không** tuyên bố bản sao lưu "đã xóa xong".
   - Hàng 8: tệp nhập khẩu gốc → không còn tải về được; biên bản ghi đã xóa (hoặc đã tự xóa khi hết thời hạn `CFG-22-01`).
   - Hàng 9: dùng lại đường tải **tệp báo cáo lỗi nhập khẩu** → bị từ chối.
   - Hàng 10: tra nhật ký xuất → có tập trường và tập bản ghi từng được xuất.
   - Hàng 11: nội dung hội thoại đa kênh → đã xóa hoặc khử định danh, biên bản nêu số hội thoại đã xử lý; phần đang bị Tạm dừng xóa theo yêu cầu pháp lý mang trạng thái **Đang tạm dừng theo yêu cầu pháp lý** kèm căn cứ, không ghi là đã xóa.
   - Hàng 12: nội dung vé hỗ trợ → biên bản thể hiện đúng quy tắc tại hàng 12 của `BR-33.8`; khi phân hệ Vé hỗ trợ chưa có quy tắc thực thi quyền xóa theo khách hàng, biên bản **nêu rõ phần này nằm ngoài phạm vi được bảo đảm** — một biên bản im lặng về hàng này là **không đạt**.
   - Hàng 13: tra sổ cái của các chiến dịch khách từng nhận → không còn tìm được khách; số liệu tổng hợp của chiến dịch không đổi; khách không còn trong chuỗi nuôi dưỡng nào.
   - Hàng 14: với mọi điểm đến của khách → biên bản ghi "Được giữ theo nghĩa vụ pháp lý" cho dấu vết chặn gửi, không chứa điểm đến ở dạng đọc được.
9. **Kỳ vọng về văn bản tự do:** với ghi chú và dòng thời gian, hệ thống liệt kê **danh sách hữu hạn** các mục gắn với khách và yêu cầu người xử lý chọn cho từng mục: **ẩn toàn bộ mục** hoặc **thay bằng ghi chú vô danh**; hệ thống **không** tự nhận diện phần nào là dữ liệu cá nhân.

---

### Kịch bản 18: Ghi chú & Bản ghi Hoạt động

1. Nhân viên A ghi chú trên khách mình phụ trách: "Khách sẵn sàng trả tới 800 triệu, đang so sánh với đối thủ X".
2. **Kỳ vọng (`BR-36.1`):** Ghi chú nhận phạm vi mặc định **"Nội bộ đội bán hàng"**. Nhân viên Marketing mở hồ sơ **không** đọc được; Quản lý Kinh doanh của A và Quản trị viên đọc được.
3. Sau 2 giờ, A sửa ghi chú. Sau 30 giờ, A thử sửa tiếp.
4. **Kỳ vọng (`BR-36.2`):** Lần sửa giờ thứ 2 thành công. Lần sửa giờ thứ 30 bị từ chối; chỉ **bổ sung** được nội dung mới.
5. A bấm "Xóa" ghi chú.
6. **Kỳ vọng (`BR-36.3`):** Ghi chú chỉ bị **ẩn**, nội dung còn nguyên; nhật ký ghi ai ẩn và lúc nào; Quản trị viên vẫn xem được. Không có đường nào cho người dùng thường xóa vĩnh viễn.
7. A bấm "Gọi" cho khách, cuộc gọi kéo dài 3 phút.
8. **Kỳ vọng (`BR-36.5`):** Hệ thống tự sinh bản ghi hoạt động kèm thời lượng, không sửa được, và được công nhận là bằng chứng liên hệ nhóm 1 (`BR-31.8`).
9. A thử ghim 4 ghi chú.
10. **Kỳ vọng (`BR-36.4`):** Chỉ ghim được tối đa 3; các ghi chú ghim hiển thị trong khung Ngữ cảnh Khách hàng sau khi lọc theo phạm vi đọc của người xem (`BR-36.7`).

---

### Kịch bản 19: Đội ngũ Phụ trách & Chia sẻ có thời hạn

1. "Tập đoàn Đại Việt" do nhân viên A phụ trách; còn có Quản lý Khách hàng Hiện hữu B, nhân viên hỗ trợ C và kế toán công nợ D cùng phục vụ.
2. A thêm B, C, D vào Đội ngũ phụ trách với vai trò từ A.13 và mức quyền: B Chỉnh sửa, C Chỉ đọc, D Chỉ đọc; quyền của D hết hiệu lực sau 30 ngày.
3. **Kỳ vọng (`BR-35.1`, `BR-35.2`, `BR-35.3`):** Cả ba truy cập được hồ sơ dù ngoài phạm vi thông thường — được chia sẻ đưa họ vào phạm vi dữ liệu của bản ghi này, nên họ thuộc cột (A) hoặc (B) của `BR-04.3`, không thuộc cột (D). B sửa được; C và D chỉ đọc; C không chia sẻ tiếp được.
4. **Kỳ vọng về mặt nạ (`BR-04.3`, `BR-04.5b`):** **B** (Chỉnh sửa) thấy **đầy đủ** kênh liên lạc công việc — cột (A). **C và D** (Chỉ đọc) thấy **che một phần** — cột (B); vai trò "Kế toán công nợ" của D không thay đổi kết luận này.
5. **Kỳ vọng về kiểm soát cột (A) — sàn bắt buộc (`BR-04.5b`):** mọi lượt thêm thành viên đều có nhật ký (`BR-35.6`). Hạn mức `CFG-04-04` đếm **theo người được thêm vào**. Hai phép đo: **(i)** A thêm B vào 60 bản ghi và thêm C vào 60 bản ghi khác ở mức Chỉnh sửa — **không** cảnh báo, vì mỗi người nhận ở mức 60/100 dù A đã thao tác 120 lượt; **(ii)** A thêm B vào 60 bản ghi và Quản lý Kinh doanh thêm **chính B** vào 50 bản ghi khác trong cùng tháng — **có** cảnh báo gửi Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu, vì B đã chạm 110 bản ghi. Màn hình cấu hình `CFG-04-04` **không** có lựa chọn "không giới hạn". Cuối tháng, Người phụ trách Bảo vệ Dữ liệu nhận **báo cáo phơi bày định kỳ**.
6. Sau 30 ngày, hệ thống rà soát quyền hết hạn.
7. **Kỳ vọng (`BR-35.3`):** Quyền của D tự thu hồi, ghi vào lịch sử bản ghi và nhật ký (`BR-35.6`); quyền của B và C không đổi.

---

### Kịch bản 20: Chốt An toàn trước khi Dọn dẹp Vĩnh viễn Thùng rác

1. Khách "Vũ Minh Đức" bị xóa mềm, còn 1 Cơ hội trị giá 800 triệu **đang mở** và 1 vé hỗ trợ **đang mở**.
2. Đủ 30 ngày, tiến trình dọn dẹp chạy.
3. **Kỳ vọng (`BR-05.6` (a)):** Bản ghi **không** bị xóa vĩnh viễn; vào danh sách **"Cần xử lý trước khi dọn dẹp"** kèm thông báo cho Quản trị viên nêu rõ Cơ hội và vé đang vướng.
4. Quản trị viên đóng vé, chuyển Cơ hội sang khách hàng khác, rồi xác nhận xóa kèm lý do.
5. **Kỳ vọng:** Bản ghi được xóa vĩnh viễn và ghi nhật ký (`NFR-07`).
6. **Kịch bản phụ (`BR-05.6` (b)):** Một bản ghi phụ bị xóa mềm **do gộp** cách đây 40 ngày cũng đến hạn dọn dẹp.
7. **Kỳ vọng:** Không bị xóa vì còn trong thời hạn hoàn tác gộp 90 ngày (`BR-20.3`); hoàn tác gộp vẫn hoạt động. Sau ngày thứ 90, hành động hoàn tác không còn hiển thị và bản ghi mới vào diện dọn dẹp, trong khi sổ cái gộp vẫn được giữ (`NFR-05`).

---

### Kịch bản 21: Vòng đời Hồ sơ Khách hàng Tạm & Các mốc Lưu trữ Dài hạn

*Nghiệm thu theo quy ước về mốc thời gian dài ở đầu Mục 6.*

1. **Tiền đề:** Một khách ẩn danh nhắn tin qua trò chuyện trực tuyến và được tạo thành **Hồ sơ Khách hàng Tạm** (`BR-01.1b`), không có email và số điện thoại; cửa sổ chat đã hiển thị thông báo ghi nhận phiên (`BR-01.1c`).
2. **Kỳ vọng:** Hồ sơ **không có giai đoạn vòng đời** (`BR-12.10`), **không** thuộc mẫu đo `KPI-01` (`BR-17.4`), và **không** vào danh sách phân khúc chiến dịch nào.
3. **Nhánh A — chưa từng có nhân viên phản hồi.** Dịch mốc tới **ngày thứ 91** kể từ tương tác gần nhất (`CFG-33-01` = 90 ngày).
4. **Kỳ vọng (`BR-33.6`, nhánh thứ nhất):** Hồ sơ tạm **tự động bị xóa** — ngoại lệ (b) tại `BR-33.5`. Thử đặt `CFG-33-01` = **200 ngày** — **bị từ chối** vì vượt miền 30–150; đặt = **150 ngày** trong khi `CFG-33-03` ở **6 tháng** (180 ngày) — **được chấp nhận**, vì khoảng cách 180 − 150 = 30 ngày đúng bằng khoảng cách tối thiểu mà ràng buộc chéo đòi hỏi.
5. **Nhánh B — đã có nhân viên phản hồi.** Một hồ sơ tạm khác có nhân viên đã trả lời. Dịch mốc tới **ngày thứ 91**.
6. **Kỳ vọng:** Hồ sơ **không** bị xóa, chỉ vào **danh sách rà soát thủ công** (`BR-33.6`).
7. Dịch tiếp tới **tháng thứ 19** kể từ tương tác gần nhất mà không ai quyết định (`CFG-33-03` = 18 tháng).
8. **Kỳ vọng (trần lưu tuyệt đối — sàn bắt buộc):** Hệ thống **tự động khử định danh**: xóa định danh thiết bị/kênh chat và mọi dữ liệu nhận diện, **giữ** nội dung hội thoại ở dạng vô danh. Thử đặt `CFG-33-03` = **36 tháng** hoặc "vô hạn" — **bị từ chối cả hai**, miền chỉ nhận 6–18 tháng.
9. **Nhánh C — trần lưu nhóm Định danh KYC.** Một khách ở Customer có nhóm KYC đã bật và hợp đồng gần nhất kết thúc tại mốc T. Dịch tới **tháng thứ 25** kể từ T (`CFG-01-03` = 24 tháng).
10. **Kỳ vọng (`BR-01.5b` — sàn bắt buộc):** Các trường KYC **bị khử vĩnh viễn**, hồ sơ và dữ liệu kinh doanh **giữ nguyên**. Tìm lựa chọn "vô hạn" cho `CFG-01-03` — **không tồn tại**; đặt **121 tháng** — **bị từ chối**, miền chỉ nhận 1–120 tháng.
11. **Nhánh D — dữ liệu không hoạt động.** Một hồ sơ đã định danh không có tương tác trong **37 tháng** (`CFG-33-02` = 36 tháng).
12. **Kỳ vọng (`BR-33.5` — hành vi cố định):** Hồ sơ **không** bị tự xóa, chỉ vào **danh sách rà soát**. Không tồn tại lựa chọn "tự động xóa khi hết thời hạn rà soát" ở bất kỳ mức phân quyền nào, kể cả Chủ sở hữu.
13. **Kỳ vọng đối chiếu sáu ngoại lệ (`BR-33.5`):** đúng **sáu** ngoại lệ — (f) khử định danh sổ cái chiến dịch hết hạn lưu, kiểm tại `campaigns-srs.md` `AC-32.3.1`; (a) khử KYC ở bước 10; (b) xóa Hồ sơ Tạm chưa có nhân viên phản hồi ở bước 4; (c) khử định danh Hồ sơ Tạm chạm trần ở bước 8; (d) dọn Thùng rác quá hạn (kiểm tại Kịch bản 20, gồm các chốt an toàn `BR-05.6`); (e) tự xóa tệp nhập khẩu gốc hết thời hạn và tài liệu xác minh danh tính sau 30 ngày — tải một tệp nhập khẩu, dịch tới ngày thứ 31 và xác nhận không còn tải về được. **Không** có tiến trình tự động xóa nào khác.

---

### Kịch bản 22: Kiểm chứng Sàn bắt buộc của Toàn bộ Tham số Cấu hình

*Mục đích: chứng minh không ai — kể cả người có thẩm quyền cao nhất — đặt được giá trị vi phạm sàn của bất kỳ tham số nào. Cách chạy: với **mỗi tham số**, đăng nhập bằng một tài khoản **chỉ giữ đúng quyền** khai ở cột "Thẩm quyền thay đổi" của Phụ lục B, không có toàn quyền (chạy bằng Chủ sở hữu sẽ không phát hiện được lỗi phân quyền, vì Chủ sở hữu bao trùm mọi quyền). Với **mọi dòng có dấu "+"**, chuẩn bị **cả hai tài khoản** vì dấu này luôn mang nghĩa "và" — kể cả khi hai quyền ngang cấp (dòng `CFG-12-02` với "Duyệt vòng đời khách hàng + Cấu hình chấm điểm tiềm năng": chạy bằng cả hai). Nếu vai trò thứ hai không tồn tại trên tenant đang kiểm thử, dùng quy tắc thay thế người thứ hai (`NFR-14`) và ghi rõ vào biên bản. Tham số đã có bước kiểm chi tiết ở kịch bản khác được ghi rõ để không kiểm trùng.*

1. **Phép thử A — vượt miền:** nhập một giá trị **ngoài miền** khai tại Phụ lục B. **Kỳ vọng:** bị từ chối ngay tại màn hình nhập, nêu rõ miền hợp lệ; giá trị cũ không đổi.
2. **Phép thử B — "không giới hạn":** tìm trong giao diện một lựa chọn "không giới hạn", "vô hạn", "tắt kiểm soát" hoặc tương đương. **Kỳ vọng:** với mọi tham số mức **Có sàn bắt buộc**, lựa chọn đó **không tồn tại** — không hiển thị, chứ không phải bị từ chối sau khi chọn.
3. **Phép thử C — vi phạm nội dung sàn:** nhập giá trị **trong miền** nhưng vi phạm điều kiện ở cột "Mức độ tự do". **Kỳ vọng:** bị từ chối, nêu đúng quy tắc bị vi phạm kèm mã `BR`.
4. **Đối tượng kiểm — toàn bộ tham số mức "Có sàn bắt buộc":**

| Tham số | Nội dung sàn phải kiểm | Ghi chú |
| --- | --- | --- |
| `CFG-01-02` | Bật nhóm Định danh KYC mà **bỏ trống mục đích** → từ chối | — |
| `CFG-01-03` | Tìm "vô hạn" → không tồn tại; đặt 121 tháng → từ chối (miền 1–120 tháng) | Đã kiểm tại Kịch bản 21 bước 10 |
| `CFG-04-01` | Đặt Nhóm 3 (KYC) lên "Che một phần" ở bất kỳ cột nào → từ chối. Đặt cột (B), (C) hoặc (D) lên "Đầy đủ" → từ chối. Đặt cột (D) lên "Che một phần" → từ chối (trần của cột D là "Che hoàn toàn"). Đặt cột (A) lên "Đầy đủ" cho Nhóm 1 hoặc Nhóm 2 → **chấp nhận**. Đặt cột (A) lên "Đầy đủ" cho Nhóm 3 → từ chối | Sàn ba tầng, chưa kiểm ở kịch bản khác |
| `CFG-04-02` | Tìm lựa chọn "không giới hạn" → không tồn tại | Đã kiểm tại Kịch bản 6 bước 9 |
| `CFG-04-03` | Cấu hình để hành động liên lạc **không ghi nhật ký** → không tồn tại lựa chọn đó (`NFR-07`) | Nội dung sàn là nghĩa vụ ghi nhật ký |
| `CFG-04-04` | Cấu hình để lượt thêm thành viên Đội ngũ phụ trách **không ghi nhật ký** → không tồn tại (`BR-35.6`) | Phần hạn mức đã kiểm tại Kịch bản 19 bước 5 |
| `CFG-05-01` | Đặt **90 ngày** trong khi `CFG-20-01` đang ở **90 ngày** → **chấp nhận** (điều kiện là "không cao hơn"). Kiểm chứng không tồn tại giá trị nào trong miền 30–90 vi phạm ràng buộc chéo | Ràng buộc chéo đã đóng bằng miền |
| [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02` (điều chỉnh ô của vai trò dựng sẵn) | Tìm năng lực xuất nhóm Định danh KYC để cấp cho Marketing hay một vai trò tự tạo → **không tồn tại** (`BR-25.4`, neo vào sàn `BR-01.5b`); cấp quyền đọc nhật ký kiểm toán cho vai trò Quản lý (`NFR-14`, cố định) → **từ chối**. Đặt ô (Khách hàng, Sửa) của Marketing rộng hơn ô (Khách hàng, Xem) → **từ chối**, nêu rõ ô vi phạm (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.1`). Đối chứng: điều chỉnh ô (Khách hàng, Chuyển giai đoạn vòng đời) cho Marketing (`BR-02.2`, **không** phải sàn) → **chấp nhận**; thu hẹp ô (Khách hàng, Xem) của Marketing về Đơn vị của mình → **chấp nhận**, và giữ nguyên sau khi bản phát hành đồng bộ vai trò dựng sẵn | Tham số thuộc tài liệu Phân quyền; ở đây chỉ kiểm sàn của phân hệ |
| `CFG-12-01` | Bật bước Customer → Lead → từ chối (`BR-12.3`). Cho bước loại khách không cần quyền Duyệt vòng đời khách hàng → từ chối (`BR-12.4`). Cho người không có toàn quyền loại Customer/Evangelist/Churned vì gian lận → từ chối (`BR-12.8`) | Ba sàn trong một tham số |
| `CFG-12-02` | Đặt giai đoạn khởi tạo là Customer, Evangelist, Churned hoặc Disqualified → từ chối | — |
| `CFG-15-01` | Đặt điều kiện Điểm Tương tác tối thiểu của MQL về **0** → từ chối (`BR-15.7`). Đặt ba ngưỡng không tăng dần → từ chối | — |
| `CFG-15-02` | Đặt thời gian hoãn thăng hạng dữ liệu nhập khẩu dưới **12 giờ** → từ chối | — |
| `CFG-16-01` | Đặt mốc thứ hai **nhỏ hơn hoặc bằng** mốc thứ nhất → từ chối | Ràng buộc thứ tự |
| `CFG-17-01` | Bật dùng Tiêu chí tham khảo để chặn hoặc gộp tự động → không tồn tại lựa chọn đó (`BR-17.1`) | — |
| `CFG-20-01` | Đặt **90 ngày** trong khi `CFG-05-01` ở **90 ngày** → **chấp nhận**. Kiểm chứng không tồn tại giá trị nào trong miền 90–365 nhỏ hơn giá trị lớn nhất của `CFG-05-01` | Ràng buộc chéo đã đóng bằng miền |
| `CFG-22-01` | Đặt 90 ngày → từ chối (miền 7–30 ngày) | — |
| `CFG-25-01` | Đặt **4.999** → từ chối (miền từ 5.000). Đặt **5.000** trong khi `CFG-25-02` cũng ở 5.000 → **chấp nhận** | Ràng buộc chéo đã đóng bằng miền |
| `CFG-25-02` | Tìm lựa chọn cho Nhân viên Kinh doanh xuất trường KYC → không tồn tại. Kiểm chứng giá trị lớn nhất của miền (5.000) bằng giá trị nhỏ nhất của `CFG-25-01` | Phần KYC đã kiểm tại Kịch bản 6 bước 15 |
| `CFG-30-01` | Bật cho nhóm Tiếp thị thoát chi phối của Từ chối nhận tin → không tồn tại | Đã kiểm tại Kịch bản 10 bước 4 |
| `CFG-30-02` | Đặt 20 → từ chối (miền 1–10) | Đã kiểm tại Kịch bản 10 bước 4 |
| `CFG-31-01` | Cấu hình để ghi chú tay đơn thuần được tính là bằng chứng liên hệ → không tồn tại (`BR-31.8`) | — |
| `CFG-31-02` | Bỏ quy tắc Người phụ trách hiện hữu khỏi vị trí ưu tiên số 1 → từ chối | — |
| `CFG-31-04` | Cấu hình để bằng chứng nhóm 2 **không cần người xác nhận**, hoặc để người khai báo tự xác nhận → không tồn tại (`BR-31.8`) | — |
| `CFG-33-01` | Đặt **200 ngày** → từ chối (miền 30–150). Đặt **150 ngày** trong khi `CFG-33-03` ở **6 tháng = 180 ngày** → **chấp nhận** | Điểm cực biên đã kiểm tại Kịch bản 21 bước 4 |
| `CFG-33-02` | Bật "tự động xóa khi hết thời hạn rà soát" → không tồn tại (`BR-33.5`) | Đã kiểm tại Kịch bản 21 bước 12 |
| `CFG-33-03` | Đặt 36 tháng hoặc "vô hạn" → từ chối (miền 6–18 tháng) | Đã kiểm tại Kịch bản 21 bước 8 |
| `CFG-33-04` | Tìm lựa chọn "vô hạn" → không tồn tại; đặt 60 ngày → từ chối (miền 7–30 ngày) | — |
| `CFG-36-02` | Cấu hình cho phép **xóa cứng** ghi chú sau khi hết thời hạn sửa → không tồn tại (`BR-36.3`) | Nội dung sàn là cấm xóa cứng |
| `CFG-36-03` | Tìm lựa chọn mở phạm vi "Nội bộ đội bán hàng" cho người đọc qua quyền đọc tự động → không tồn tại (`BR-36.7`) | Mở ghi chú nội bộ cho một vai trò là điều chỉnh ô (Khách hàng, Đọc ghi chú nội bộ), không thuộc tham số này |

5. **Kiểm các quy tắc mức "Cố định" (hàng cuối Phụ lục B):** với từng quy tắc ở hàng đó, tìm trong **toàn bộ** giao diện cấu hình của mọi vai trò — kể cả Chủ sở hữu — một tham số điều chỉnh được nội dung hàng đó khóa. **Kỳ vọng:** không tồn tại. Hai trường hợp có phần cấu hình được là hợp lệ: `BR-33.7` có `CFG-33-04` (thời hạn biện pháp phòng ngừa) và `BR-33.8` có `CFG-22-01` (thời hạn lưu tệp nhập khẩu gốc) — phần bị khóa là nghĩa vụ và phạm vi, không phải thời hạn.
6. **Kỳ vọng về nhật ký:** mọi lượt thay đổi tham số **thành công** ở bước 1–5 đều có bản ghi nhật ký theo sự kiện "Thay đổi tham số cấu hình" của `NFR-07`, ghi người thực hiện, thời điểm, tham số và giá trị trước/sau. Lượt **bị từ chối** không sinh bản ghi nhật ký, vì không phải một thay đổi đã xảy ra.

---

## 7. Nhu cầu nghiệp vụ chưa chốt được phương án

Mục này chỉ chứa các điểm **chưa quyết định được điều gì là đúng về mặt nghiệp vụ**, hoặc cần người có thẩm quyền ngoài nhóm soạn tài liệu quyết định. Những yêu cầu đã chốt phương án đều nằm trong Mục 3 dưới dạng `FEAT`/`BR` bắt buộc; các quyết định đã chốt trước đây được ghi tại Phụ lục C. "Hướng có thể" dưới đây chỉ là các lựa chọn để người quyết định cân nhắc, **không** phải phương án đã chọn.

**7.1 Tự động làm giàu dữ liệu doanh nghiệp.** Có tích hợp nguồn dữ liệu bên thứ ba để tự động điền tên công ty, địa chỉ, ngành nghề từ mã số thuế hoặc tên miền không. Chưa chốt vì kéo theo chi phí thuê dữ liệu và câu hỏi dữ liệu bên thứ ba có được ghi đè dữ liệu người dùng nhập tay hay không. Cần quyết định: Product Owner cùng người phê duyệt ngân sách của khối Kinh doanh.

**7.2 Hiệu chỉnh bộ ngưỡng điểm mặc định.** Bộ ngưỡng 40/70/85 tại `BR-15.5` là giả định ban đầu, chưa được kiểm chứng bằng tỷ lệ chuyển đổi thực tế. Tenant tự chỉnh được qua `CFG-15-01`, nhưng giá trị mặc định chuẩn hệ thống cần được xem lại khi có dữ liệu vận hành. Cần quyết định: Quản lý Marketing cùng Quản lý Kinh doanh.

**7.3 Thời hạn xử lý yêu cầu chủ thể dữ liệu.** Các mốc tại bảng loại yêu cầu của `FEAT-33` (30 ngày cho bản sao và xóa, 15 ngày cho chỉnh sửa) theo thông lệ GDPR. Pháp luật bảo vệ dữ liệu cá nhân tại Việt Nam có thể đặt mốc ngắn hơn cho một số loại yêu cầu. Nhóm soạn tài liệu không có thẩm quyền chốt; hướng có thể là lập bảng đối chiếu và lấy mốc ngắn hơn làm cam kết hệ thống. Cho tới khi có xác nhận, các mốc này không được đưa vào hợp đồng, điều khoản dịch vụ hay chính sách quyền riêng tư công bố ra ngoài. Cần quyết định: Pháp chế cùng Người phụ trách Bảo vệ Dữ liệu.

**7.4 Quy trình xử lý sự cố rò rỉ dữ liệu cá nhân.** Tài liệu có cơ chế phát hiện dấu hiệu (cảnh báo truy cập bất thường `BR-04.5`, `KPI-06`) nhưng chưa có quy tắc về xác định sự cố, phân loại mức độ, mốc thời gian thông báo cho cơ quan quản lý và cho chủ thể dữ liệu, vai trò chịu trách nhiệm và mẫu nội dung thông báo. Cần quyết định: Pháp chế, Người phụ trách Bảo vệ Dữ liệu, Chủ sở hữu.

**7.5 Nơi lưu và chuyển dữ liệu xuyên biên giới.** `NFR-12`, `NFR-13` mở phạm vi vận hành đa vùng (gồm tiếng Ả Rập, hàm ý thị trường Trung Đông) nhưng chưa có quy định về nơi lưu dữ liệu khách hàng và điều kiện chuyển dữ liệu ra ngoài lãnh thổ. Hướng có thể: Pháp chế xác định yêu cầu theo từng thị trường mục tiêu, hoặc nội dung này được giao cho một tài liệu khác và dẫn chiếu tại đây. Cần quyết định: Pháp chế cùng Trưởng nhóm Kỹ thuật.

**7.6 Ràng buộc toàn vẹn tối thiểu của ma trận chuyển đổi cấu hình được.** `CFG-12-01` cho tenant bật/tắt từng bước chuyển, với ba sàn hiện có (không hạ Customer/Evangelist, không nới quyền loại khách, không nới quyền loại vì gian lận) và nguyên tắc 1 luôn thắng. Chưa quy định tenant có được tắt các bước chuyển tự động khác — thăng MQL theo ngưỡng điểm (`BR-15.5`), sang Nurturing khi mọi Cơ hội Thua (`BR-12.3`) — hay tắt mọi lối ra khỏi Nurturing/Disqualified hay không. Nếu được, một cấu hình có thể khiến hồ sơ mắc kẹt vĩnh viễn ở một giai đoạn. Cần quyết định: Product Owner.

**7.7 Suy giảm điểm khi khách im lặng kéo dài sau mốc thứ hai.** `BR-16.1`, `BR-16.2` quy định hai mốc (14 ngày và 30 ngày), mỗi mốc áp một lần trong một khoảng không tương tác liên tục. Chưa quy định khi khách tiếp tục im lặng sau mốc 30 ngày (ví dụ 90, 180 ngày) thì Điểm Tương tác tiếp tục giảm hay giữ nguyên. Nếu giữ nguyên, một khách im lặng một năm vẫn giữ khoảng 2/3 Điểm Tương tác cũ. Cần quyết định: Quản lý Marketing.

**7.8 Xuất dữ liệu phục vụ chiến dịch ngoài hệ thống của Marketing.** Mục 2.2 nêu Marketing xuất danh sách khách hàng phục vụ chiến dịch, trong khi `NFR-06` và `BR-25.1` bắt buộc tệp xuất tuân theo mức che của người xuất — với Marketing là cột (B), tức kênh liên lạc che một phần. Tệp xuất vì vậy dùng được cho phân tích nhưng không dùng được để gửi chiến dịch ngoài hệ thống. Chưa chốt: đây là hệ quả có chủ đích (mọi lượt gửi phải qua hệ thống để chịu `BR-30.5`), hay cần một đường xuất có kiểm soát riêng. Cần quyết định: Quản lý Marketing cùng Người phụ trách Bảo vệ Dữ liệu.

**7.9 Cấu trúc cây doanh nghiệp khi Doanh nghiệp mẹ bị xóa.** `FEAT-07` và `FEAT-09` chưa quy định khi một doanh nghiệp ở giữa cây (có mẹ và có con) bị xóa mềm hoặc xóa vĩnh viễn thì các công ty con được nối lên cấp trên, tạm tách khỏi cây, hay chặn xóa cho tới khi xử lý cấu trúc; và báo cáo hợp nhất tập đoàn tính thế nào trong thời gian đó. Cần quyết định: Product Owner.

---

## 8. Phụ lục A — Danh mục Dữ liệu Chuẩn

> Các danh mục dưới đây là **giá trị nghiệp vụ hiển thị cho người dùng**, bắt buộc chọn từ danh sách (không nhập tự do) để bảo đảm thống kê và báo cáo được. Đây không phải thiết kế dữ liệu; cách tổ chức lưu trữ thuộc thẩm quyền đội phát triển.

**A.1 Lý do Loại khách hàng** — `BR-12.4`, `BR-12.4b`, `BR-12.8`. Hai nhóm có nhãn, vì thẩm quyền sử dụng khác nhau:

- **Nhóm Thương mại** (5 giá trị): Sai ngành/không thuộc tập khách hàng mục tiêu · Không đủ ngân sách · Không có nhu cầu thực · Đã là khách hàng của đối thủ với hợp đồng dài hạn · Ngoài vùng phục vụ.
- **Nhóm Gian lận & Dữ liệu không hợp lệ** (3 giá trị, gọi tắt "nhóm Gian lận"): Thông tin giả/Spam/Lừa đảo · Trùng lặp với bản ghi khác · Không thuộc đối tượng đủ điều kiện pháp lý.

*Ai dùng nhóm nào:* `BR-12.4` (Quản lý Kinh doanh trở lên, giai đoạn tiền bán hàng) dùng được **cả 8 giá trị**. `BR-12.4b` (Nhân viên đánh dấu nhanh) chỉ dùng **2 giá trị** "Thông tin giả/Spam/Lừa đảo" và "Trùng lặp với bản ghi khác" — giá trị thứ ba của nhóm Gian lận đòi đánh giá pháp lý vượt thẩm quyền nhân viên. `BR-12.8` (Quản trị viên/Chủ sở hữu, với Customer/Evangelist/Churned) dùng **cả 3 giá trị** nhóm Gian lận; nhóm Thương mại không dùng được cho ba giai đoạn này.

**A.2 Lý do Không chuyển đổi** — `BR-12.3`: Hết ngân sách kỳ này · Chờ phê duyệt nội bộ · Chưa đúng thời điểm/hoãn sang kỳ sau · Thua đối thủ về giá · Thua đối thủ về tính năng · Thiếu người ra quyết định · Dự án bị tạm dừng · Không phản hồi sau nhiều lần liên hệ.

**A.3 Lý do Hạ hạng Giai đoạn** — `BR-12.7`: Thẩm định lại không đủ điều kiện · Thông tin ban đầu không chính xác · Khách hàng thay đổi nhu cầu · Điểm tiềm năng không phản ánh thực tế · Sai sót nhập liệu.

**A.4 Vai trò Liên hệ trên Cơ hội** — `BR-19.4`: Danh mục **do từng Không gian làm việc tự định nghĩa**, dùng chung với [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) (`BR-22.1` của tài liệu đó); giá trị mặc định chuẩn hệ thống khi khởi tạo Không gian làm việc mới: Người ra quyết định · Người ủng hộ nội bộ · Người thẩm định kỹ thuật · Người ảnh hưởng · Người thực hiện mua hàng. *Thứ tự ưu tiên khi gộp (`BR-19.4`) là thứ tự Quản trị viên sắp xếp cho danh mục này, không phải một danh sách cố định riêng.*

**A.5 Vai trò Liên kết Doanh nghiệp** — `BR-10.1`: Chính · Phụ · Cố vấn · Cổ đông · Đại diện pháp luật. *"Đã nghỉ việc" không phải vai trò mà là một Trạng thái liên kết (A.5b).*

**A.5b Trạng thái Liên kết Doanh nghiệp** — `BR-10.1`, `BR-09.1`: Đang công tác · Đã nghỉ việc · Tạm ngưng (doanh nghiệp liên kết đang nằm trong Thùng rác theo `BR-09.1` (a)).

**A.6 Loại Quan hệ Cá nhân** — `BR-11.1`: Quản lý trực tiếp / Cấp dưới · Người giới thiệu / Được giới thiệu · Thành viên gia đình · Đối tác kinh doanh · Trợ lý / Người đại diện.

**A.7 Kênh Nguồn gốc Khách hàng** — `BR-32.1`: Website · Quảng cáo Facebook · Quảng cáo Google · Zalo · Giới thiệu · Sự kiện/Hội thảo · Tiếp cận chủ động · Nhập khẩu từ tệp · Đối tác · Không xác định.

**A.8 Nguồn thu thập Đồng thuận** — `BR-30.3`: Biểu mẫu đăng ký trên website · Hộp thoại đồng ý trên trò chuyện trực tuyến · Phiếu đồng ý tại sự kiện · Nhập khẩu từ tệp có khai báo cơ sở · Ghi nhận thủ công bởi nhân viên · Liên kết Hủy nhận tin trong email · Liên kết hủy nhận tin trong chiến dịch (mọi kênh, `campaigns-srs.md` `BR-26.4`) · Hủy nhận bằng một thao tác từ giao diện hộp thư · Từ khóa từ chối trong tin trả lời · Tư vấn viên ghi nhận từ chối · Người nhận chặn doanh nghiệp trên nền tảng nhắn tin · Người nhận tắt tin tiếp thị trên nền tảng · Đồng ý trên Tài khoản Chính thức hoặc trong tin nhắn nền tảng ([`campaigns-srs.md`](./campaigns-srs.md) `BR-09.3`) · Nhà mạng hoặc nền tảng báo số điện thoại đổi chủ ([`campaigns-srs.md`](./campaigns-srs.md) `BR-28.4`) · Khiếu nại thư rác · Yêu cầu trực tiếp của khách hàng · **Yêu cầu chưa xác minh được danh tính** (dùng cho lượt hạ mức đồng thuận do biện pháp phòng ngừa tại `BR-33.7` (d)) · **Tự khôi phục khi dỡ biện pháp phòng ngừa** (`BR-33.7` (e)).

**A.9 Giai đoạn Vòng đời** — `FEAT-12`: 10 giá trị và các bước chuyển hợp lệ tại Ma trận Chuyển đổi Giai đoạn, `FEAT-12`.

**A.10 Trạng thái Tiếp cận** — `BR-29.2`, `BR-10.4`: Đã xác thực · Không tiếp cận được (trạng thái kỹ thuật: email hỏng, số không tồn tại) · Chưa kiểm tra · **Không còn hiệu lực** (lý do nghiệp vụ: địa chỉ không còn thuộc về khách — khách đã rời doanh nghiệp sở hữu địa chỉ theo `BR-10.4`, hoặc số điện thoại đã đổi chủ theo `campaigns-srs.md` `BR-28.4`; đảo lại được, khác "Không tiếp cận được").

**A.11 Cơ sở Đồng thuận cho lô Nhập khẩu** — `BR-30.4`: Khách hàng đã đăng ký trực tiếp · Dữ liệu từ sự kiện có phiếu đồng ý · Quan hệ hợp đồng hiện hữu (**không** tạo Đồng ý nhận tin nhóm Tiếp thị — các kênh của lô nhận Chưa có đồng thuận; chỉ là căn cứ cho nhóm Giao dịch & Dịch vụ) · Không có cơ sở đồng thuận (**không phải giá trị mặc định** — người nhập bắt buộc tự chọn; khi chọn, toàn bộ lô nhận Chưa có đồng thuận).

**A.12 Nhóm Mục đích Gửi tin** — `BR-30.5`: Tiếp thị & Quảng bá (chịu chi phối của Từ chối nhận tin) · Giao dịch & Dịch vụ · Liên lạc 1-1 do nhân viên chủ động.

**A.13 Vai trò tham gia Đội ngũ phụ trách** — `BR-35.1`: Kinh doanh chính · Hỗ trợ kỹ thuật · Quản lý khách hàng · Kế toán công nợ · Quan sát.

**A.14 Loại Khách hàng** — `BR-01.6`: Khách hàng Doanh nghiệp (B2B) · Khách hàng Cá nhân tiêu dùng (B2C).

**A.15 Lý do Hoàn tác Chuyển đổi** — `BR-14.2`: Chuyển đổi nhầm bản ghi · Khách hàng chưa thực sự đủ điều kiện · Thông tin doanh nghiệp sai · Trùng với Cơ hội đã có · Yêu cầu của Quản lý.

**A.16 Lý do chuyển sang Nuôi dưỡng** — `BR-16.4`, ma trận `FEAT-12`: Điểm tương tác nguội dưới ngưỡng · Khách hàng đề nghị liên hệ lại sau · Chưa đúng thời điểm ngân sách · Không phản hồi sau nhiều lần liên hệ · Chuyển sang chăm sóc bằng chiến dịch định kỳ. *Phân biệt với A.2 (toàn bộ Cơ hội Thua — `BR-12.3`) và A.3 (bước lùi trên phễu tuyến tính — `BR-12.7`).*

**A.17 Lý do Mở lại bản ghi đã bị Loại** — `BR-12.4`, ma trận `FEAT-12` dòng Disqualified: Thông tin liên lạc đã được xác minh lại · Khách hàng chủ động liên hệ trở lại · Đã xác định trước đây loại nhầm · Doanh nghiệp tái cấu trúc, người liên hệ nay hợp lệ · Có bằng chứng mới từ nguồn khác. *Ghi nhận căn cứ đảo ngược một quyết định loại; là dữ liệu để rà soát chất lượng quyết định loại của từng Quản lý.*

**A.18 Lý do Quay lại Phễu từ Nuôi dưỡng** — `BR-16.5`, ma trận `FEAT-12`: Khách hàng chủ động liên hệ trở lại · Có tương tác mới với nội dung tiếp thị · Đã qua thời điểm khách đề nghị liên hệ lại · Ngân sách của khách đã được duyệt · Người liên hệ mới tại doanh nghiệp cũ. *Chiều ngược lại của A.16; phân biệt với A.3.*

---

## 9. Phụ lục B — Danh mục Tham số Cấu hình theo Không gian làm việc

**Nguyên tắc:** Mọi quy tắc nghiệp vụ có nhiều hướng xử lý hợp lý tùy tenant là một tham số cấu hình cấp không gian làm việc, kèm giá trị mặc định chuẩn hệ thống; tenant tự điều chỉnh mà không cần thay đổi hệ thống hay chờ phát hành phiên bản mới. Phụ lục này là **nguồn duy nhất** về giá trị mặc định và miền giá trị; quy tắc trong thân tài liệu nêu giá trị mặc định chỉ để dễ đọc, và nếu hai nơi khác nhau thì đó là lỗi tài liệu phải sửa.

**Quy ước thẩm quyền:** cột "Thẩm quyền thay đổi" nêu **quyền cần có** để đổi tham số — một quyền quản trị của phân hệ (Mục 5.2), một cấp bậc thành viên hay một chức danh trách nhiệm — kèm vai trò dựng sẵn mặc định giữ quyền đó trong ngoặc; tên vai trò không phải điều kiện. **Đây là một trục quyền riêng, độc lập với mức truy cập trên dữ liệu:** người giữ quyền Cấu hình phân bổ khách hàng tiềm năng mà chỉ có mức "Đơn vị của mình" trên dữ liệu vẫn đặt được các mốc thời hạn phản hồi cho cả tenant, vì đó là quyết định nghiệp vụ thuộc chuyên môn của họ. **Chủ sở hữu và Quản trị viên bao trùm mọi quyền quản trị của phân hệ** — đổi được mọi tham số mà một quyền quản trị đổi được. Dấu **"+"** luôn có nghĩa **"và"**: mọi vai trò nối bằng "+" phải cùng phê duyệt một lượt thay đổi. "+ Người phụ trách Bảo vệ Dữ liệu" và "+ Quản trị Chất lượng Dữ liệu" là **người phê duyệt thứ hai bắt buộc** — thiếu thì không thực hiện được, kể cả bởi Chủ sở hữu. Khi vế thứ hai là Quản trị viên hoặc Chủ sở hữu, dấu "+" chỉ mang tính liệt kê, vế đứng trước tự thực hiện được.

**Khi vai trò thứ hai không tồn tại hoặc trùng người:** áp quy tắc thay thế người thứ hai tại `NFR-14` — người thứ hai là **một Quản trị viên khác** với người thực hiện. Yêu cầu bất biến là **luôn có hai người khác nhau đứng tên**, không phải hai chức danh cụ thể; nếu không, một tenant chưa chỉ định Người phụ trách Bảo vệ Dữ liệu sẽ không đổi được các tham số mức "Có sàn bắt buộc" cần người phê duyệt thứ hai.

**Ba mức độ tự do:** **Tự do** — đặt giá trị bất kỳ trong miền. **Có sàn bắt buộc** — điều chỉnh được nhưng không nới lỏng dưới ngưỡng an toàn do phân hệ khai báo. **Cố định** — không cấu hình được, vì liên quan cam kết với chủ thể dữ liệu hoặc toàn vẹn dữ liệu. Sàn không gắn với quy định của một quốc gia cụ thể; doanh nghiệp tự xác định nghĩa vụ áp dụng cho mình và đặt tham số trong miền cho phép.

*Viết tắt trong bảng:* "BVDL" = Người phụ trách Bảo vệ Dữ liệu; "QTCLDL" = Quản trị Chất lượng Dữ liệu.

| Mã tham số | Quy tắc liên quan | Nội dung cấu hình | Mặc định chuẩn hệ thống | Miền giá trị | Thẩm quyền thay đổi | Mức độ tự do |
| --- | --- | --- | --- | --- | --- | --- |
| `CFG-01-01` | `BR-01.6` | Loại khách hàng mặc định khi tạo mới | Khách hàng Doanh nghiệp (B2B) | B2B / B2C / Bắt buộc người dùng chọn | Chủ sở hữu | Tự do |
| `CFG-01-02` | `BR-01.5b` | Bật/tắt nhóm trường Định danh KYC và mục đích sử dụng | **Tắt** | Bật (kèm khai báo mục đích) / Tắt | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — bật thì bắt buộc khai báo mục đích |
| `CFG-01-03` | `BR-01.5b` | Thời hạn lưu nhóm Định danh KYC sau khi hợp đồng gần nhất kết thúc | 24 tháng | 1 – 120 tháng | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — hết hạn buộc tự khử định danh, không có lựa chọn "vô hạn" |
| `CFG-04-01` | `BR-04.3` | Chính sách che mặt nạ theo nhóm trường và quan hệ với bản ghi | Đúng bảng tại `BR-04.3` | Ma trận 3 nhóm trường × 4 cột quan hệ (A)(B)(C)(D), mỗi ô một trong 4 mức hiển thị tại `BR-04.2` | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — ba sàn: **(i)** Nhóm 3 (KYC) chỉ ở mức Che hoàn toàn hoặc Ẩn trường ở **cả bốn** cột, chỉ mở được bằng quyền chuyên biệt theo `BR-04.4`, có nhật ký và chịu hạn mức `CFG-04-02`; **(ii)** cột (D) không cao hơn Che hoàn toàn cho bất kỳ nhóm nào; **(iii)** cột (B) và cột (C) không cao hơn Che một phần. Hệ quả: cột (A) là cột duy nhất nhận được mức Đầy đủ, và cột (A) đã chịu kiểm soát riêng tại `BR-04.5b` |
| `CFG-04-02` | `BR-04.5` | Hạn mức mở khóa mặt nạ mỗi người mỗi ngày | 50 bản ghi/ngày | 10 – 200 | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — không có lựa chọn "không giới hạn"; trần tuyệt đối 200 |
| `CFG-04-03` | `BR-04.6` | Hạn mức hành động liên lạc trong hệ thống mỗi người mỗi ngày | 200 lượt/ngày | 50 – 1.000 | Chủ sở hữu | **Có sàn bắt buộc** — mọi lượt liên lạc luôn ghi nhật ký (`NFR-07`) |
| `CFG-04-04` | `BR-04.5b` | Số bản ghi tối đa một người được thêm vào Đội ngũ phụ trách ở mức Chỉnh sửa mỗi tháng | 100 bản ghi/tháng | 20 – 500 | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — mọi lượt thêm thành viên luôn ghi nhật ký (`BR-35.6`) |
| `CFG-05-01` | `BR-05.4` | Thời hạn lưu bản ghi trong Thùng rác trước khi xóa vĩnh viễn | 30 ngày | 30 – 90 ngày (giới hạn trên theo gói dịch vụ) | Chủ sở hữu | **Có sàn bắt buộc** — không được cao hơn `CFG-20-01` |
| — (dẫn chiếu) | Mục 5 | Điều chỉnh từng ô của vai trò dựng sẵn trên Khách hàng và Doanh nghiệp, gồm thao tác đặc thù của phân hệ — **không còn là tham số của phân hệ này**; thực hiện qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02` | Ma trận Mục 5.1 | Theo tài liệu Phân quyền | Theo tài liệu Phân quyền | **Có sàn bắt buộc** — sàn của phân hệ: không cấp được năng lực xuất nhóm Định danh KYC (`BR-25.4`), không mở được quyền đọc nhật ký kiểm toán (`NFR-14`), ô mở khóa nhóm KYC chịu `CFG-04-01` |
| `CFG-12-01` | `FEAT-12` | Ma trận Chuyển đổi Giai đoạn — các bước chuyển được phép | Đúng ma trận tại `FEAT-12` | Từng bước chuyển bật/tắt | Quyền Duyệt vòng đời khách hàng (mặc định Quản lý) | **Có sàn bắt buộc** — ba sàn: không cho hạ Customer/Evangelist về giai đoạn tiền bán hàng (`BR-12.3`, nguyên tắc 4); bước loại khách luôn cần quyền Duyệt vòng đời khách hàng (`BR-12.4`); loại Customer/Evangelist/Churned vì gian lận chỉ do Người có toàn quyền (`BR-12.8`) |
| `CFG-12-02` | `BR-12.10` | Giai đoạn mặc định theo từng nguồn tạo | Thủ công/Biểu mẫu website/Hội thoại/Nhập khẩu → Lead; Đăng ký bản tin → Subscriber; Hồ sơ Tạm → chưa gán | "Chưa gán giai đoạn" (chỉ cho nguồn Hồ sơ Tạm), hoặc một trong sáu giai đoạn tiền bán hàng | Quyền Duyệt vòng đời khách hàng + Quyền Cấu hình chấm điểm tiềm năng (mặc định Quản lý + Quản lý Marketing) | **Có sàn bắt buộc** — không được đặt Customer, Evangelist, Churned hay Disqualified: một bản ghi vừa tạo chưa thể đã trả tiền hay đã rời bỏ |
| `CFG-14-01` | `BR-14.2`, `BR-14.5` | Thời hạn được Hoàn tác Chuyển đổi | 24 giờ | 1 – 168 giờ | Quyền Hoàn tác Chuyển đổi Tiềm năng (mặc định Quản lý) | Tự do |
| `CFG-15-01` | `BR-15.5` | Bộ ngưỡng điểm MQL / SQL / Ưu tiên cao | 40 / 70 / 85, kèm Điểm Tương tác ≥ 15 cho MQL | 0 – 100 mỗi ngưỡng, tăng dần | Quyền Cấu hình chấm điểm tiềm năng (mặc định Quản lý Marketing) | **Có sàn bắt buộc** — điều kiện Điểm Tương tác tối thiểu không được đặt về 0 (`BR-15.7`) |
| `CFG-15-02` | `BR-15.7` | Hoãn thăng hạng cho dữ liệu nhập khẩu; chống thông báo lặp; độ trễ đánh giá lại | 24 giờ / 1 lần trong 30 ngày / 7 ngày | 12 – 168 giờ; 1 – 90 ngày; 0 – 30 ngày | Quyền Cấu hình chấm điểm tiềm năng (mặc định Quản lý Marketing) | **Có sàn bắt buộc** — hoãn thăng hạng không dưới 12 giờ (`BR-15.7` (a)) |
| `CFG-16-01` | `BR-16.1`, `BR-16.2` | Các mốc ngày không tương tác và tỷ lệ suy giảm | 14 ngày −10%; 30 ngày −25% | 7 – 90 ngày mỗi mốc, mốc thứ hai luôn lớn hơn mốc thứ nhất; 0 – 50% mỗi tỷ lệ | Quyền Cấu hình chấm điểm tiềm năng (mặc định Quản lý Marketing) | **Có sàn bắt buộc** — hai mốc không được bằng nhau hay đảo thứ tự |
| `CFG-17-01` | `BR-17.2` | Chính sách khi phát hiện trùng theo Tiêu chí chắc chắn | Chặn tạo bản ghi mới | Chặn / Cảnh báo và vẫn cho lưu (có nhật ký) | Chủ sở hữu + QTCLDL | **Có sàn bắt buộc** — Tiêu chí tham khảo không bao giờ được dùng để chặn hoặc gộp tự động (`BR-17.1`) |
| `CFG-19-01` | `BR-19.9` | Cách xác định Người phụ trách sau gộp | Người phụ trách của bản ghi có tương tác gần nhất | Tương tác gần nhất / Bản ghi Chính / Bắt buộc chỉ định thủ công | Quyền Cấu hình phân bổ khách hàng tiềm năng (mặc định Quản lý) | Tự do |
| `CFG-20-01` | `BR-20.3` | Thời hạn được hoàn tác gộp | 90 ngày | 90 – 365 ngày | Chủ sở hữu | **Có sàn bắt buộc** — lớn hơn hoặc bằng `CFG-05-01`; miền bắt đầu từ 90 ngày để mọi tổ hợp với miền của `CFG-05-01` đều hợp lệ |
| `CFG-22-01` | `BR-33.8` | Thời hạn lưu tệp nhập khẩu gốc sau khi nhập xong | 30 ngày | 7 – 30 ngày | Quản trị viên + BVDL | **Có sàn bắt buộc** — trần 30 ngày tuyệt đối; hết hạn buộc tự xóa |
| `CFG-22-02` | `BR-22.2` | Dung lượng tệp nhập khẩu tối đa mỗi lần | 50 MB | 10 – 200 MB (theo gói dịch vụ) | Chủ sở hữu | Tự do |
| `CFG-25-01` | `BR-25.4` | Ngưỡng số bản ghi mỗi lần xuất cần phê duyệt trước | 5.000 bản ghi | 5.000 – 50.000 | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — lần xuất chứa trường KYC luôn cần phê duyệt bất kể số lượng |
| `CFG-25-02` | `BR-25.5`, `BR-25.4` | Hạn mức xuất dữ liệu mỗi người mỗi ngày của người có ô (Khách hàng, Xuất) = Chỉ của mình | 2.000 bản ghi/ngày | 200 – 5.000 (không cao hơn giá trị nhỏ nhất của `CFG-25-01`, nên mọi tổ hợp đều hợp lệ) | Chủ sở hữu | **Có sàn bắt buộc** — không ai ở mức này xuất được trường KYC; mọi lần xuất luôn ghi nhật ký (`BR-25.3`) |
| `CFG-30-01` | `BR-30.5` | Nhóm Liên lạc 1-1 có chịu chi phối của Từ chối nhận tin hay không | Không chịu, nhưng bắt buộc ghi nhật ký | Có / Không | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — nhóm Tiếp thị luôn chịu chi phối, không cấu hình được |
| `CFG-30-02` | `BR-30.7` | Số người nhận tối đa của một lượt gửi nhóm Liên lạc 1-1 | 5 | 1 – 10 | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — trần tuyệt đối 10; trên mức đó là gửi hàng loạt và buộc thuộc nhóm Tiếp thị |
| `CFG-30-03` | `BR-30.4` | Số bản ghi tối đa của một lô nhập khẩu tạo Đồng ý nhận tin mà không cần Người phụ trách Bảo vệ Dữ liệu chấp thuận | 1.000 | 0 – 10.000 (0 nghĩa là mọi lô đều cần chấp thuận) | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — trần 10.000 |
| `CFG-31-01` | `BR-31.7`, `BR-31.8`, `BR-17.2c`, `BR-35.3b` | Các mốc thời hạn phản hồi theo mức ưu tiên; số lần thu hồi tự động tối đa | 1 / 4 / 24 giờ làm việc; tối đa 2 lần | 0,5 – 72 giờ; 0 – 5 lần | Quyền Cấu hình phân bổ khách hàng tiềm năng (mặc định Quản lý) | **Có sàn bắt buộc** — ghi chú tay đơn thuần không bao giờ được tính là bằng chứng liên hệ (`BR-31.8`) |
| `CFG-31-02` | `BR-31.3b` | Thứ tự ưu tiên giữa các quy tắc phân bổ | Người phụ trách hiện hữu → Vùng địa lý → Ngành nghề → Chia vòng lần lượt → Chưa phân công | Sắp xếp lại các vị trí (2) đến (4) | Quyền Cấu hình phân bổ khách hàng tiềm năng + Quản trị viên | **Có sàn bắt buộc** — Người phụ trách hiện hữu luôn ở vị trí số 1 (`BR-31.6`) |
| `CFG-31-03` | `BR-31.7b` | Lịch làm việc: múi giờ, ngày làm việc, giờ bắt đầu/kết thúc, ngày lễ | Thứ Hai – Thứ Sáu 08:00 – 17:30, không ngày lễ | Tự khai báo; tối thiểu một ngày làm việc trong tuần, giờ kết thúc sau giờ bắt đầu | Chủ sở hữu | Tự do |
| `CFG-31-04` | `BR-31.8` | Số lần dùng bằng chứng liên hệ nhóm 2 mỗi người mỗi tháng | 10 lần/tháng | 0 – 50 | Quyền Cấu hình phân bổ khách hàng tiềm năng (mặc định Quản lý) | **Có sàn bắt buộc** — luôn cần người giữ quyền Xác nhận liên hệ ngoài hệ thống, khác người khai báo, xác nhận; luôn thống kê riêng trong `KPI-03` |
| `CFG-33-01` | `BR-33.6` | Thời hạn dọn dẹp Hồ sơ Tạm không có tương tác | 90 ngày kể từ tương tác gần nhất | 30 – 150 ngày | Quản trị viên + BVDL | **Có sàn bắt buộc** — phải nhỏ hơn `CFG-33-03` ít nhất 30 ngày (quy đổi 1 tháng = 30 ngày, Mục 2.3); miền dừng ở 150 ngày để mọi tổ hợp với giá trị nhỏ nhất 180 ngày của `CFG-33-03` đều hợp lệ |
| `CFG-33-02` | `BR-33.5` | Thời hạn rà soát dữ liệu khách hàng không hoạt động | 36 tháng | 12 – 84 tháng | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — thời hạn cấu hình được, nhưng hành vi "không tự động xóa" là cố định |
| `CFG-33-03` | `BR-33.6` | Trần lưu tuyệt đối cho Hồ sơ Tạm đã có tương tác của nhân viên | 18 tháng kể từ tương tác gần nhất | 6 – 18 tháng | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — hết trần buộc tự khử định danh, không có lựa chọn "vô hạn" |
| `CFG-33-04` | `BR-33.7` | Thời hạn tối đa của biện pháp phòng ngừa khi không xác minh được chủ thể | 30 ngày | 7 – 30 ngày | Quản trị viên + BVDL | **Có sàn bắt buộc** — không có lựa chọn "vô hạn"; hết hạn buộc tự dỡ và bắt buộc thông báo Người phụ trách |
| `CFG-34-01` | `BR-34.3` | Phạm vi thực thể con mặc định khi chuyển giao | Kèm Cơ hội và Vé hỗ trợ đang mở | Chỉ bản ghi / Kèm thực thể đang mở / Kèm Cơ hội đang mở và mọi Vé hỗ trợ (Cơ hội đã đóng không bao giờ chuyển) | Người có toàn quyền | Tự do |
| `CFG-34-02` | `BR-34.6` | Số ngày không đăng nhập liên tiếp để tự bật trạng thái không khả dụng | 14 ngày | 3 – 60 ngày | Chủ sở hữu | Tự do |
| `CFG-36-01` | `BR-36.1` | Phạm vi đọc mặc định của ghi chú mới | Nội bộ đội bán hàng | Nội bộ đội bán hàng / Chung / Giới hạn | Chủ sở hữu | Tự do |
| `CFG-36-02` | `BR-36.2` | Thời hạn người tạo được sửa ghi chú | 24 giờ | 0 – 168 giờ | Chủ sở hữu | **Có sàn bắt buộc** — hết thời hạn chỉ được bổ sung; không bao giờ được xóa cứng (`BR-36.3`) |
| `CFG-36-03` | `BR-36.7` | Phạm vi ghi chú đọc được qua quyền đọc tự động (`BR-35.4`) | "Chung" + ghi chú ghim đã được đánh dấu "Cho phép tuyến Hỗ trợ đọc" | Chỉ "Chung" / "Chung" + ghi chú ghim được phép | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — quyền đọc tự động không bao giờ mở phạm vi "Nội bộ đội bán hàng". Ai đọc được ghi chú nội bộ theo vai trò là ô (Khách hàng, Đọc ghi chú nội bộ) tại Mục 5.1, không phải tham số này; tư cách thành viên Đội ngũ phụ trách mở ghi chú nội bộ của riêng bản ghi đó khi ô này khác Không có (`BR-36.1`) |
| — | `BR-19.6`, `BR-19.8`, `BR-30.5` (nhóm Tiếp thị), `BR-30.9`, `BR-30.10`, `BR-33.7` (nghĩa vụ xác minh và quy tắc hai người — không gồm thời hạn biện pháp phòng ngừa, cấu hình qua `CFG-33-04`), `BR-33.8` (phạm vi xóa và các sàn liên tài liệu — không gồm thời hạn lưu tệp nhập khẩu gốc, cấu hình qua `CFG-22-01`), `BR-34.4`, `BR-36.3`, `NFR-07` (giới hạn nội dung nhật ký), `NFR-14` | Bảo vệ đồng thuận tiếp thị, cưỡng chế đồng thuận trên mọi nguồn, giai đoạn khách đang trả tiền, xác minh chủ thể dữ liệu, phạm vi xóa, chốt bàn giao, chống xóa cứng ghi chú, giới hạn nội dung và quyền đọc nhật ký | Theo đúng quy tắc | — | — | **Cố định** — không cấu hình được ở mọi mức |

*Ghi chú phân loại:* cụm "sàn bắt buộc" trong tên một quy tắc ở thân tài liệu nghĩa là **quy tắc có một phần không được nới lỏng**; nó không tự quyết định mức độ tự do ở bảng này. Quy tắc có phần điều chỉnh được thì xuất hiện ở một dòng tham số với mức "Có sàn bắt buộc"; quy tắc **toàn bộ** không có gì để điều chỉnh thì nằm ở hàng cuối với mức "Cố định". Ba quy tắc hỗn hợp có mặt ở cả hai: `BR-33.7` (nghĩa vụ xác minh cố định · thời hạn biện pháp phòng ngừa qua `CFG-33-04`), `BR-33.8` (phạm vi xóa cố định · thời hạn lưu tệp gốc qua `CFG-22-01`), `BR-30.5` (nhóm Tiếp thị luôn chịu chi phối · nhóm Liên lạc 1-1 cấu hình qua `CFG-30-01`).

**Quản trị cấu hình:** mọi thay đổi tham số được ghi nhật ký (`NFR-07`) gồm người đổi, giá trị trước và sau, thời điểm. Thay đổi chỉ áp dụng **từ thời điểm đổi trở đi**, không hồi tố dữ liệu đã xử lý. Tham số mức "Có sàn bắt buộc" hiển thị rõ ngưỡng sàn trên màn hình cấu hình, và hệ thống từ chối giá trị vi phạm sàn ngay tại màn hình.

**Hằng số vận hành cấp hệ thống (không cấu hình theo tenant):** thời hạn hiệu lực 24 giờ của đường tải tệp xuất và tệp báo cáo lỗi (`BR-25.2`, `BR-24.2`), thời hạn 7 ngày của quyền đọc tạm tự cấp (`BR-17.2c`); hạn của đề nghị chuyển bản ghi theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-25-01` (`BR-34.1b`), thời hạn xóa tài liệu xác minh danh tính 30 ngày (`BR-01.5b`), các giới hạn theo gói dịch vụ (`NFR-11`) và chu kỳ sao lưu 35 ngày (`NFR-10`) áp dụng thống nhất cho mọi không gian làm việc cùng gói.

---

## 10. Phụ lục C — Nhật ký Mâu thuẫn & Quyết định đã chốt

Phụ lục này ghi các mâu thuẫn nội tại đã được giải quyết và các quyết định đã chốt, để lần rà soát sau không lật lại. Nội dung chi tiết của từng quyết định nằm tại quy tắc tương ứng ở Mục 3 – 5.

| # | Mâu thuẫn / câu hỏi | Cách xử lý đã chốt | Nơi có hiệu lực |
| --- | --- | --- | --- |
| C.1 | Phạm vi dữ liệu của vai trò Marketing | Ô (Khách hàng, Xem) của vai trò dựng sẵn Marketing mặc định Toàn workspace, các thao tác khác trên Khách hàng Không có; tenant siết lại bằng cách điều chỉnh đúng ô đó qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02`. Mọi vai trò tự tạo cũng đặt được "xem rộng, sửa hẹp"; mỗi lượt đọc ngoài phạm vi phụ trách đều ghi nhật ký | Mục 5, `NFR-07` mục 13, [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.7`, `BR-35.9` |
| C.2 | Quyền nhập khẩu dữ liệu của Marketing | Ô (Khách hàng, Nhập): Marketing mặc định Không có, Quản lý Marketing mặc định Đơn vị của mình (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`); doanh nghiệp cấp thêm qua [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02`; mọi lô bắt buộc khai báo cơ sở đồng thuận | Mục 5.1, `BR-30.4` |
| C.3 | Mức độ phụ thuộc vào tài liệu Phân quyền khi chia sẻ bản ghi | Chia sẻ nới rộng phạm vi dữ liệu, không cấp năng lực vai trò (quyền hiệu lực là phần giao); không nới mức che trường; không vượt lượt chặn tường minh | `BR-35.1`, `BR-35.5`; [ADR-0007](../docs/adr/0007-record-sharing-and-permission-precedence-contract.md) |
| C.4 | Hai sàn liên tài liệu cho việc xóa theo quyền chủ thể (Hộp thư Đa kênh, Vé hỗ trợ) | Hộp thư Đa kênh đã có quy tắc thỏa sàn; Vé hỗ trợ khi chưa có quy tắc thỏa sàn thì Biên bản bắt buộc nêu rõ phần chưa được bảo đảm; bổ sung trạng thái "Đang tạm dừng theo yêu cầu pháp lý"; tham chiếu liên tài liệu luôn ghi đủ tên tệp | `BR-33.8`; [ADR-0008](../docs/adr/0008-data-subject-deletion-contract-omnichat-tickets.md) |
| C.5 | Xung đột trường nhiều giá trị (nhiều email/số điện thoại) khi gộp | Giữ toàn bộ kênh hợp lệ, người dùng chỉ định một kênh chính mỗi loại | `BR-19.10` |
| C.6 | Thời hạn Thùng rác 30 hay 90 ngày | Mặc định 30 ngày, tối đa 90 ngày theo gói, không cao hơn thời hạn hoàn tác gộp | `BR-05.4`, `CFG-05-01` |
| C.7 | Lượt gửi tin từ các kênh chưa khai báo nhóm mục đích | Mọi kênh gửi phải khai báo nhóm; lượt không khai báo mặc định thuộc nhóm Tiếp thị | `BR-30.5`, `BR-30.9` |
| C.8 | Kịch bản xóa theo quyền chủ thể vừa "từ chối một phần" vừa kỳ vọng "hồ sơ không còn tồn tại" | Kỳ vọng tại hàng 1 được đối chiếu theo `BR-33.3`: phần dữ liệu phục vụ nghĩa vụ hợp đồng được giữ | Kịch bản 17 |
| C.9 | `FEAT-21` (khôi phục giao dịch gộp dở dang) và `NFR-04` (gộp luôn cùng thành công hoặc cùng thất bại) | `NFR-04` là yêu cầu; `FEAT-21` là quy trình đưa một giao dịch bị gián đoạn ngoài ý muốn về một trong hai trạng thái toàn vẹn, kèm danh sách giao dịch cần xử lý | `BR-21.1`, `BR-21.2`, `NFR-04` |
| C.10 | Quy tắc đường quay lại phễu mang mã của `FEAT-16` nhưng trình bày tại `FEAT-12` | Trình bày tại `FEAT-16`; ma trận tại `FEAT-12` dẫn chiếu | `BR-16.5` |
| C.11 | Tên gọi "hết ngày", "mỗi tháng" và quy đổi tháng sang ngày chưa được chốt ở một chỗ | Hạn mức theo ngày/tháng dương lịch, thời hạn tính bằng tháng quy đổi 1 tháng = 30 ngày, theo múi giờ không gian làm việc | Mục 2.3 |
| C.12 | Lịch làm việc cấu hình tự do có thể không có ngày làm việc nào, làm mọi thời hạn tính bằng giờ làm việc không bao giờ đến hạn | Lịch hợp lệ phải có ít nhất một ngày làm việc và giờ kết thúc sau giờ bắt đầu | `BR-31.7b`, `CFG-31-03` |
| C.13 | Quyết định "vẫn tạo Cơ hội riêng" tại `BR-14.3` phải ghi nhật ký nhưng không có trong danh mục sự kiện kiểm toán | Bổ sung vào danh mục | `NFR-07` mục 9 |
| C.14 | Danh mục Vai trò Liên hệ trên Cơ hội (A.4): cố định ở đây hay do tenant định nghĩa như [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) quy định | Do tenant tự định nghĩa, dùng chung với `deals-pipeline-srs.md` (`BR-22.1` của tài liệu đó); thứ tự ưu tiên khi gộp là thứ tự tenant tự sắp cho danh mục, không phải danh sách cố định riêng | `BR-19.4`, A.4 |
| C.15 | Căn cứ nào ("chắc chắn" hay "tham khảo") cho trùng lặp doanh nghiệp giữa mã số thuế và tên miền website | Mã số thuế là Tiêu chí chắc chắn (chặn tạo mới); tên miền là Tiêu chí tham khảo (chỉ cảnh báo) — tên miền dùng chung hợp lệ giữa công ty mẹ và công ty con | `BR-06.2` |
| C.16 | Đơn vị tổ chức của khách hàng gán theo người tạo hay theo Người phụ trách hiện tại, nhất quán với [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) | Theo Người phụ trách hiện tại, chuyển theo ngay khi bàn giao — cùng Nguyên tắc 4 của `deals-pipeline-srs.md` | `BR-01.3`, `BR-34.1` |
| C.17 | Giai đoạn Opportunity khi Cơ hội mở duy nhất biến mất mà không qua đóng Thua (xóa mềm, chuyển sang khách khác, gỡ liên kết) | Áp đúng cách xử lý của `BR-12.3` cho khách chưa từng là Customer: tự động chuyển Nurturing kèm Lý do không chuyển đổi | `BR-12.3b` |
| C.18 | Hoàn tác Chuyển đổi khi đã gắn Liên hệ vào một Cơ hội đang mở sẵn có, thay vì tạo Cơ hội mới | Không xóa Cơ hội/Doanh nghiệp sẵn có (đã tồn tại từ trước, có thể phục vụ thương vụ khác); chỉ gỡ liên kết Liên hệ khỏi Cơ hội đó, với điều kiện Liên hệ chưa có hoạt động riêng trên Cơ hội | `BR-14.5` |
| C.19 | Giai đoạn Customer khi Cơ hội Thắng duy nhất từng đưa khách lên Customer bị tái phân loại thành Thua ([`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `FEAT-21`) | Không tự động hạ giai đoạn (nguyên tắc 4 thắng, vì đây là sửa sai sót ghi nhận quá khứ chứ không phải sự kiện thương mại mới); bắt buộc cảnh báo Quản lý Kinh doanh rà soát thủ công, chỉ Quản lý mới quyết định hạ giai đoạn | `BR-12.3c` |
| C.20 | Chưa có đồng thuận luôn chặn nhóm Tiếp thị trong khi bộ quy tắc tuân thủ là chính sách của từng doanh nghiệp | Chưa có đồng thuận chỉ chặn trên kênh doanh nghiệp yêu cầu Đồng ý trước ([`campaigns-srs.md`](./campaigns-srs.md) `BR-30.1`, `CFG-CAMP-74`) | `BR-30.1` |
| C.21 | Lô nhập khẩu "Không có cơ sở đồng thuận" bị gắn Từ chối nhận tin dù khách chưa từng từ chối, trái lý do của `BR-30.1` và lệch A.11 | Lô nhận Chưa có đồng thuận; chính sách gửi tiếp thị quyết định việc nhận tiếp thị | `BR-30.4`, A.11 |
| C.22 | Mục 7.7 (hoàn tác gộp và đồng thuận) và 7.8 (đồng thuận khi biện pháp phòng ngừa hết hạn) của v7.0 còn để mở dù đã có quy tắc | Đã chốt tại `BR-20.2` (mức chặt nhất) và `BR-30.10`, `BR-33.7` (e) — mỗi kênh trở về đúng trạng thái trước khi áp; bỏ khỏi Mục 7 | `BR-20.2`, `BR-30.10`, `BR-33.7` |
| C.23 | Tự khôi phục đồng thuận khi dỡ biện pháp phòng ngừa trái nguyên tắc bất biến của `BR-30.10`; ghi đè thay đổi đồng thuận thật xảy ra trong thời gian áp; hoàn tác gộp chép trạng thái của biện pháp sang bản ghi khác | Ngoại lệ tường minh tại `BR-30.10`; (e) chỉ trả về kênh còn mang lượt hạ mức của biện pháp, dỡ sớm khôi phục Đồng ý là căn cứ, hết hạn cần chính chủ xác nhận lại; (f) chặn hoàn tác gộp trong thời gian áp | `BR-30.10`, `BR-33.7` |
| C.24 | Chặn hoàn tác gộp trong thời gian áp biện pháp phòng ngừa làm hết thời hạn 90 ngày, mất vĩnh viễn quyền hoàn tác | Thời hạn hoàn tác và bảo vệ khỏi dọn dẹp kéo dài thêm đúng bằng thời gian bị chặn | `BR-20.3`, `BR-33.7` (f) |
| C.25 | Hoàn tác gộp sau khi biện pháp phòng ngừa kết thúc vẫn chép Từ chối và Hạn chế xử lý của biện pháp theo quy tắc mức chặt nhất | `BR-20.2` loại trừ lượt hạ mức của biện pháp phòng ngừa đã kết thúc | `BR-20.2`, `BR-33.7` |
| C.26 | Hoàn tác gộp làm mất dấu "tự khôi phục"; lượt rút đồng thuận đã xác minh vẫn mang nguồn chưa xác minh nên bị bỏ khi hoàn tác | Xác minh thành công ghi lại lượt hạ mức với nguồn "Yêu cầu trực tiếp của khách hàng"; `BR-20.2` coi "tự khôi phục" là mức chặt hơn | `BR-20.2`, `BR-33.7` (e) |
| C.27 | Gộp làm mất dấu "tự khôi phục"; dỡ Hạn chế xử lý vô điều kiện khi xác minh thành công; "ghi lại nguồn" đọc thành sửa bằng chứng không sửa được | Thứ tự mức chặt dùng chung tại `BR-19.6` (đã thay bởi C.28); `BR-30.6` giữ Hạn chế xử lý đã xác minh và có thuộc tính nguồn; `BR-33.7` (e) tạo lượt ghi nhận mới | `BR-19.6`, `BR-20.2`, `BR-30.6`, `BR-33.7` |
| C.28 | Dùng chung một thứ tự mức chặt cho gộp và hoàn tác gộp: gộp làm mất Đồng ý hợp lệ khi gặp Chưa có đồng thuận; hoàn tác gộp bỏ sót lượt đổi chủ số và không áp được loại trừ của biện pháp phòng ngừa | `BR-19.6` quy tắc gộp riêng (Đồng ý thắng Chưa có trừ khi hạ mức mới hơn); `BR-20.2` áp theo thời gian các lượt chặt hơn, bỏ qua lượt chưa xác minh của biện pháp đã kết thúc | `BR-19.6`, `BR-20.2` |
| C.29 | Hoàn tác gộp để Bản ghi Chính giữ Đồng ý mượn bằng chứng của bản ghi phụ; gộp gỡ được dấu "tự khôi phục" bằng bằng chứng do nhân viên ghi; gộp không giữ Hạn chế xử lý; hoàn tác áp lượt khôi phục về Chưa có và lượt của địa chỉ khác | `BR-20.2` tính lại cả Bản ghi Chính từ ảnh chụp, bỏ mọi lượt khôi phục của biện pháp trừ dấu, áp theo đúng điểm đến; `BR-19.6` nhánh 2 chỉ nhận xác nhận của chính chủ qua điểm đến, thêm dòng Hạn chế xử lý | `BR-19.6`, `BR-20.2` |
| C.30 | Tính lại Bản ghi Chính khi hoàn tác gộp làm mất xác nhận Đồng ý của chính chủ trong thời gian gộp; chưa rõ lượt cấp kênh áp cho điểm đến nào và điểm đến của bản ghi phụ đi đâu | `BR-20.2` nhận thêm lượt nới mức là hành vi của chính chủ trên đúng điểm đến; lượt cấp kênh áp cho kênh, lượt theo địa chỉ áp cho địa chỉ; điểm đến của bản ghi phụ trở về bản ghi phụ | `BR-20.2`, `BR-18.2` |
| C.31 | Kịch bản 16 ghi nhầm bản ghi; "mọi lượt" đi theo điểm đến mâu thuẫn bộ lọc trạng thái; gộp xét đổi chủ số theo kênh; hoàn tác đặt lại Hạn chế xử lý đã được chính chủ rút | `BR-20.2`: lượt đi theo làm lịch sử, trạng thái theo bộ lọc; điểm đến mới ở lại Bản ghi Chính; dỡ Hạn chế hợp lệ được áp; `BR-19.6` nhánh 3 xét theo cùng địa chỉ | `BR-19.6`, `BR-20.2` |
| C.32 | Bảng tóm tắt gộp thiếu điều kiện "cùng địa chỉ"; gộp có thể bị hiểu là tạo lượt đồng thuận mới; dỡ Hạn chế xử lý khi hoàn tác chưa giới hạn theo bản ghi | Bảng `BR-18.2` sửa; gộp không tạo lượt mới; dỡ Hạn chế chỉ áp cho bản ghi vốn giữ kênh đã xác minh | `BR-18.2`, `BR-19.6`, `BR-20.2` |
| C.33 | Chỉ vai trò Marketing có được "xem toàn bộ, không sửa"; doanh nghiệp không cấu hình được cho vai trò tự tạo | Mức truy cập theo từng thao tác trên từng loại dữ liệu cho mọi vai trò (ADR-0009); "Xem toàn bộ" là giá trị ô; nhật ký đọc ngoài phạm vi áp theo cấu hình ô, không theo tên vai trò | Mục 5, [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02`, `NFR-07` mục 13 |
| C.34 | Tài liệu tự định nghĩa bốn mức phạm vi, trong đó "Đơn vị của mình" gồm cả đơn vị cấp dưới — khác định nghĩa của tài liệu Phân quyền, nên cùng một người có hai tập bản ghi khác nhau ở hai phân hệ | Dùng đúng năm mức và định nghĩa "Bản ghi thuộc một đơn vị" của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) Mục 1.4, `FEAT-34`; tài liệu này không định nghĩa lại; vai trò Quản lý dùng mức Đơn vị và các đơn vị con | `BR-01.3`, `BR-01.4`, Mục 1.4, Mục 5 |
| C.35 | Tham số điều chỉnh ô vai trò dựng sẵn nằm ở tài liệu này trong khi bản chất là tham số chung của mọi loại dữ liệu | Chuyển về [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-29-02`; bỏ khỏi Phụ lục B, giữ một dòng dẫn chiếu kèm sàn của phân hệ; mọi chỗ dẫn chiếu trong tài liệu trỏ tới tham số đó | Phụ lục B, Mục 5, Kịch bản 22 |
| C.36 | Cột vai trò của ma trận dùng tên tự đặt (Quản lý Kinh doanh, Nhân viên Marketing…) không khớp danh sách vai trò dựng sẵn; thiếu ba vai trò dựng sẵn | Cột ma trận dùng đúng tên vai trò dựng sẵn của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`; Mục 2.2 ghi tên gọi trong quy tắc tương ứng với vai trò nào; khai báo đủ ô cho Chỉ xem bản ghi được giao, Kiểm toán, Kiểm toán quyền | Mục 2.2, Mục 5 |
| C.37 | Ma trận mặc định lệch mức đặt sẵn của tài liệu Phân quyền (Nhân viên Kinh doanh chỉ xem của mình, Nhân viên Hỗ trợ tạo được khách hàng, Marketing mặc định nhập/xuất) | Căn theo mức mặc định chung của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-29`; phân hệ khai báo chi tiết theo loại dữ liệu và thao tác đặc thù. Hai điểm chi tiết có chủ đích: Nhân viên Kinh doanh có (Khách hàng, Xuất) = Chỉ của mình vì `BR-25.5`; Marketing có thao tác đặc thù Gắn thẻ phân loại và Ghi nhận đồng thuận ở mức Toàn workspace vì đây là công việc phân khúc và đồng thuận cốt lõi của vai trò | Mục 5.1 |
| C.38 | Nhiều năng lực gắn cứng với tên vai trò ("Quản lý Kinh doanh trở lên", "Marketing không đọc ghi chú", "Marketing chỉ được thêm làm Quan sát", "riêng vai trò Marketing không xem chỉ số tài chính", người phê duyệt xuất theo cặp vai trò) nên vai trò tự tạo không cấu hình được tương đương | Mọi năng lực là ô (gồm thao tác đặc thù tại Mục 5.1) hoặc quyền quản trị của phân hệ (Mục 5.2), áp như nhau cho mọi vai trò; phê duyệt và khai báo thay đi theo quan hệ quản lý trực tiếp; quy ước đọc tên vai trò tại Mục 2.2 | Mục 2.2, Mục 5, `BR-02.2`, `BR-07.4`, `BR-12.4`, `BR-14.2`, `BR-15.4`, `BR-25.4`, `BR-31.5`, `BR-31.8`, `BR-32.4`, `BR-35.1`, `BR-36.1` |
| C.39 | Hàng đợi "Chưa phân công" không thuộc đơn vị nào; khách hàng tiềm năng chưa có người phụ trách không thuộc phạm vi của ai nên đội kinh doanh không thấy để nhận | Hàng đợi theo đơn vị tiếp nhận của từng nguồn; khách hàng tiềm năng là bản ghi chờ phân công; thành viên đơn vị tiếp nhận thấy và nhận việc bằng ô Gán; khai báo giai đoạn chưa chốt và không có chuyển hàng đợi (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.10` – `BR-35.14`) | `BR-31.4`, `BR-31.9`, `BR-01.3` |
| C.40 | Phân bổ tự động có thể giao khách cho người ngoài phạm vi của người cấu hình quy tắc, và tiếp tục chạy khi người đó đã bị thu hẹp quyền hay nghỉ việc | Phân bổ là thao tác Gán thay người chịu trách nhiệm quy tắc, chịu ô Gán và phần giao của phạm vi lúc thiết lập với phạm vi hiện tại; quy tắc tạm dừng khi người đó tạm ngưng hay rời đi | `BR-31.10` |
| C.41 | Nhập tệp không quy định người phụ trách là người chưa vào workspace, không có đường đưa bản ghi vào hàng đợi | Bản ghi giữ chỗ thuộc Đơn vị chính ghi trong lời mời, chặn với mọi người trừ nhóm ngoại lệ; cột Đơn vị tiếp nhận đưa bản ghi vào hàng đợi; cột Người phụ trách chịu ô Gán | `BR-23.5`, `BR-24.2` |
| C.42 | Chặn vô hiệu hóa người dùng khi còn bản ghi trái quy tắc tạm ngưng luôn thực hiện ngay của tài liệu Phân quyền | Tạm ngưng không bị chặn; xử lý bản ghi (giữ nguyên, chuyển tạm, trả về hàng đợi) là thao tác riêng sau đó; chỉ gỡ khỏi workspace mới bị chặn khi còn bản ghi; bỏ danh sách "Chờ bàn giao lại" | `BR-34.4`, Kịch bản 14 |
| C.43 | Thao tác thu hồi chia sẻ hay tạm ngưng có thể bị chặn khi nhật ký tạm lỗi | Thu hẹp không bị chặn, nhật ký ghi bù; nới rộng vẫn đóng khi lỗi ([ADR-0010](../docs/adr/0010-revocation-not-blocked-by-audit-failure.md)) | `NFR-07`, `BR-35.6`, `BR-34.4` |
| C.44 | Trường nhạy cảm của phân hệ chưa được khai báo với khung che dữ liệu dùng chung; trợ lý AI có thể nhận giá trị đầy đủ khi người dùng ở cột (A) | Khai báo trường nhạy cảm của hệ thống, mẫu che riêng, quyền xem đầy đủ; Doanh nghiệp không có trường nhạy cảm của hệ thống; AI luôn nhận giá trị đã che (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `FEAT-40`) | `BR-04.7` |
| C.45 | Sàn của nhóm Định danh KYC và quyền đọc nhật ký kiểm toán được tài liệu Phân quyền dẫn chiếu bằng mã | Giữ nguyên mã `BR-01.5b` và `NFR-14` là Sàn bắt buộc của phân hệ; bổ sung xuất nhóm KYC là năng lực không cấp được qua vai trò | `BR-01.5b`, `NFR-14`, `BR-25.4` |
| C.46 | "Bắt buộc chỉ định theo pháp luật", "sàn pháp lý" ngầm khóa nghĩa vụ theo một quốc gia | Doanh nghiệp tự xác định nghĩa vụ áp dụng cho mình và chịu trách nhiệm; tài liệu dùng "Sàn bắt buộc" do phân hệ khai báo | Mục 2.2, `BR-30.5`, Phụ lục B |
| C.47 | Ngưỡng 14 ngày không đăng nhập để tự bật trạng thái không khả dụng là con số cố định, trong khi nhịp làm việc mỗi doanh nghiệp khác nhau | Thành tham số `CFG-34-02`, mặc định 14 ngày | `BR-34.6` |
| C.48 | Thẩm quyền đổi tham số ghi theo tên vai trò | Ghi theo quyền quản trị của phân hệ, kèm vai trò dựng sẵn mặc định trong ngoặc | Phụ lục B, Kịch bản 22 |
| C.49 | Ô Gán cho giao bản ghi cho bất kỳ ai, kể cả ra ngoài phạm vi của người gán; ô Gán mức Chỉ của mình hiểu là được giao bản ghi của mình | Theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`: chỉ giao sang người mà bản ghi vẫn nằm trong mức của người gán; Chỉ của mình chỉ nhận việc về mình; điều kiện người nhận tập trung tại `BR-34.1`; Người phụ trách luôn có hai đường — trả về hàng đợi, đề nghị chuyển | `BR-34.1`, `BR-34.1b`, `BR-31.4`, `BR-31.9` (c) |
| C.50 | Đề nghị chuyển giới hạn "cùng nhóm/đơn vị" mơ hồ và không quy định điều kiện của người nhận, hạn của đề nghị | *(Đã thay bởi C.60)* Người nhận tự đạt điều kiện nhận việc lúc chấp nhận; hạn theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `CFG-25-01` | `BR-34.1b`, Phụ lục B |
| C.51 | Yêu cầu phát sinh của khách có người phụ trách đang tạm ngưng bị phân bổ lại cho người khác, trái quy tắc giữ nguyên người phụ trách khi tạm ngưng | Không phân bổ lại; yêu cầu sang người xử lý thay hoặc quản lý trực tiếp; bỏ nhánh "đã rời" vì không xảy ra | `BR-31.6`, `BR-34.4` (b) |
| C.52 | Gắn vào Cơ hội sẵn có khi chuyển đổi xét cả Cơ hội ngoài phạm vi, tự chọn khi có nhiều Cơ hội; chuyển đổi được bản ghi chưa ai nhận | Chỉ xét Cơ hội trong ô (Cơ hội, Sửa) của người chuyển đổi, bắt buộc chọn khi có nhiều, giữ Người phụ trách Cơ hội (thống nhất [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `BR-33.1`); bản ghi chưa có Người phụ trách phải được nhận việc trước | `BR-14.3`, `BR-14.6` |
| C.53 | Hoàn tác Chuyển đổi xóa mềm Cơ hội và Doanh nghiệp chỉ dựa trên quyền trên Khách hàng; xóa Cơ hội khi hoàn tác lại kích hoạt chuyển Nurturing | Mỗi thực thể bị xóa mềm hay gỡ liên kết cần ô của chính loại dữ liệu đó, thiếu thì không hoàn tác một phần; `BR-12.3b` không áp cho hoàn tác | `BR-14.2`, `BR-14.5`, `BR-12.3b` |
| C.54 | Chuyển giao kèm Cơ hội và Vé chỉ xét quyền trên Khách hàng; vé đổi đơn vị theo người phụ trách | Cần ô Gán của từng loại dữ liệu; vé là bản ghi công việc ở lại đơn vị tiếp nhận; thực thể không đủ điều kiện vào danh sách bỏ qua | `BR-34.3`, Kịch bản 14 |
| C.55 | Ô mở khóa nhóm Định danh KYC chưa có trần và chưa có thẩm quyền bổ sung khi cấp | Trần Chỉ của mình; mỗi lượt cấp cần Người phụ trách Bảo vệ Dữ liệu phê duyệt; thu hẹp tự do | `BR-04.7`, Mục 5.1 |
| C.56 | Miền thời hạn lưu KYC 6–24 tháng khóa thay doanh nghiệp một con số | Miền 1–120 tháng; sàn chỉ là phải có thời hạn và hết hạn buộc tự khử định danh | `CFG-01-03`, `BR-01.5b` |
| C.57 | Lô nhập chạy nền tiếp tục với quyền cũ sau khi người nhập bị thu hẹp quyền hay tạm ngưng | Lô là tiến trình chạy thay người nhập theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-35.8` | `BR-22.1`, `BR-24.2` |
| C.58 | Chuyển kèm thực thể con có thể đổi người phụ trách cơ hội đã đóng, và phần cơ hội không theo quy tắc bàn giao của phân hệ Cơ hội | Cơ hội đã đóng không bao giờ đổi người phụ trách; phần cơ hội tuân [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `BR-37.1` (lý do, thông báo đơn vị nhận, việc nhắc và người theo dõi đi theo) và quy tắc đích trên ô (Cơ hội, Gán người phụ trách) | `BR-34.1`, `BR-34.3`, `CFG-34-01` |
| C.59 | Chuyển đổi "gán Người phụ trách thống nhất" là đường vòng của ô Gán | Liên hệ giữ người phụ trách; Doanh nghiệp, Cơ hội tạo mới do người chuyển đổi phụ trách; thay đổi khác qua ô Gán | `BR-14.7` |
| C.60 | Đề nghị chuyển của Người phụ trách bị giới hạn trong cùng đơn vị và có thêm quyền thu hồi riêng của quản lý, khác điều kiện nhận việc của tài liệu Phân quyền và của phân hệ Cơ hội | Người nhận tự đạt điều kiện nhận việc của [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6` (xem được qua chia sẻ và Đội ngũ phụ trách) cộng ô Sửa khác Không có; không giới hạn đơn vị; báo quản lý trực tiếp của người đề nghị, không có thời hạn thu hồi; đề nghị chỉ chuyển bản ghi khách hàng hoặc doanh nghiệp | `BR-34.1b` |
| C.61 | Điều kiện hoàn tác chuyển đổi nằm rải ở hai phân hệ (quyết định chung với phân hệ Cơ hội) | `BR-14.5` là danh sách duy nhất, gồm Liên hệ chưa có hoạt động và gỡ nguồn gốc bổ sung; [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `BR-04.5` dẫn chiếu; Cơ hội xóa do hoàn tác không khôi phục được | `BR-14.2`, `BR-14.5`, Mục 5.2 |
| C.62 | Thông báo và đề xuất trả bản ghi khi người phụ trách tạm ngưng chưa thống nhất với phân hệ Cơ hội | Giữ nguyên thì báo quản lý trực tiếp hoặc quản lý tạm thời, không báo người tạm ngưng; khi kích hoạt lại chỉ đề xuất trả bản ghi người xử lý thay vẫn đang phụ trách | `BR-31.6`, `BR-34.4` (b) |
| C.63 | Sàn ô mở khóa KYC chưa nói có áp lên Người có toàn quyền hay không | Áp cả lên Người có toàn quyền: chỉ mở khóa từng bản ghi, có nhật ký và hạn mức | `BR-04.7` |
| C.64 | Quản lý không đồng ý với một lượt chuyển theo đề nghị mà không có quyền thu hồi riêng thì không biết làm gì | Giao lại bằng ô Gán theo `BR-34.1`; Mục 5 không ghi quyền thu hồi | Mục 5.2, 5.3 |
| C.65 | Chuyển phòng của Người phụ trách không có quy tắc cho Khách hàng và Doanh nghiệp | Ba lựa chọn tường minh không chọn sẵn; chặn "đi theo người" khi mất ô Sửa; thống nhất [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) `BR-37.3` | `BR-34.9` |
| C.66 | "Đề nghị chuyển giao" của người ngoài phạm vi trùng tên với đề nghị chuyển của Người phụ trách; ai chấp thuận chưa rõ | Đổi tên thành "Yêu cầu nhận bàn giao"; chỉ người có ô Gán đạt `BR-34.1` với người yêu cầu là người nhận mới chấp thuận; Người phụ trách chỉ có Gán Chỉ của mình chuyển tiếp lên quản lý | `BR-17.2c`, `BR-17.3`, `BR-35.3b` |
| C.67 | Cảnh báo trùng và màn hình ngoài phạm vi lộ thông tin nhận diện của bản ghi bị chặn hay giữ chỗ | Chỉ nhãn "Bị hạn chế truy cập" và "Đề nghị gộp" tới Người có toàn quyền (theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-39.4`) | `BR-17.3` |
| C.68 | Điều kiện người nhận đòi "ô Xem bao phủ bản ghi"; quy trình rời workspace đòi người thực hiện có ô Gán | Ô Xem khác Không có như [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-25.6`; trong rời workspace và tạm ngưng chỉ áp điều kiện người nhận theo [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) `BR-43.2`, `BR-43.4` | `BR-34.1`, `BR-34.4` (c) |
