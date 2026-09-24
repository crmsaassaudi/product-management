# SRS — Phân hệ Quản lý Khách hàng & Danh bạ Doanh nghiệp (Contacts & Accounts Management)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA (Version 7.0) |
| **Module** | CRM — Phân hệ Quản lý Khách hàng & Danh bạ Doanh nghiệp (Contacts & Accounts Management) |
| **Ngày cập nhật** | 2026-09-24 |
| **Phiên bản** | v7.0 (Chuẩn hóa Nghiệp vụ Thuần túy — Thay thế v6.4) |
| **Neo mã nguồn** | Chưa xác định — tài liệu đặc tả trạng thái nghiệp vụ mục tiêu, không neo vào một phiên bản triển khai cụ thể |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`object-manager-srs.md`](./object-manager-srs.md), [`omnichat-srs.md`](./omnichat-srs.md), [`onboarding-srs.md`](./onboarding-srs.md), [`deals-pipeline-srs.md`](./deals-pipeline-srs.md), [`tickets-srs.md`](./tickets-srs.md), [`tasks-srs.md`](./tasks-srs.md), [`campaigns-srs.md`](./campaigns-srs.md), [ADR-0007](../docs/adr/0007-record-sharing-and-permission-precedence-contract.md), [ADR-0008](../docs/adr/0008-data-subject-deletion-contract-omnichat-tickets.md) |

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
- Mô hình vai trò, phạm vi dữ liệu theo cây tổ chức và thứ tự hợp nhất quyền — thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md).
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
| **Đồng thuận Nhận tin** | Trạng thái **Đồng ý nhận tin** hoặc **Từ chối nhận tin** của khách hàng đối với thư tiếp thị, ghi nhận độc lập cho từng kênh. |
| **Bằng chứng Đồng thuận** | Bộ dữ liệu chứng minh một lần thay đổi đồng thuận: thời điểm, nguồn thu thập, nội dung điều khoản đã đồng ý và người ghi nhận. |
| **Định danh dùng chung** | Nhãn đặt lên một email hoặc số điện thoại mà nhiều người khác nhau hợp lệ cùng dùng (tổng đài, lễ tân, vợ chồng), để hệ thống không coi các bản ghi đó là trùng. |
| **Trạng thái Hạn chế xử lý** | Trạng thái đặt lên hồ sơ khi chủ thể dữ liệu yêu cầu hạn chế xử lý: dữ liệu được giữ nhưng dừng mọi hoạt động tiếp thị và tự động hóa (`BR-30.6`). |
| **Quyền Chủ thể Dữ liệu** | Các quyền của khách hàng đối với dữ liệu cá nhân của chính họ: yêu cầu bản sao, chỉnh sửa, xóa vĩnh viễn, hạn chế xử lý và rút lại đồng thuận. Khác hoàn toàn với Thùng rác nội bộ — xóa theo quyền chủ thể dữ liệu là nghĩa vụ pháp lý và không thể phục hồi. |
| **Khử định danh** | Xóa phần dữ liệu cho phép nhận ra một người cụ thể, giữ lại phần giá trị kinh doanh hoặc thống kê ở dạng vô danh. |
| **Mặt nạ dữ liệu** | Cơ chế che một phần hoặc toàn bộ giá trị trường nhạy cảm theo quan hệ của người xem với bản ghi (`FEAT-04`). **Mở khóa mặt nạ** là thao tác có kiểm soát để xem giá trị đầy đủ. |
| **Đội ngũ phụ trách** | Nhóm người cùng phục vụ một khách hàng bên cạnh Người phụ trách, mỗi người có vai trò tham gia và mức quyền Chỉ đọc hoặc Chỉnh sửa (`FEAT-35`). |
| **Người phụ trách** | Nhân viên chịu trách nhiệm chính đối với một khách hàng. Mỗi bản ghi có tối đa một Người phụ trách. |
| **Vai trò chức năng** | Trách nhiệm nghiệp vụ được giao cho một người (Quản lý Khách hàng Hiện hữu, Quản trị Chất lượng Dữ liệu, Người phụ trách Bảo vệ Dữ liệu) nhưng không phải một vai trò phân quyền riêng; quyền hạn theo vai trò gốc được cấp. |
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

| Vai trò | Quyền hạn và trách nhiệm nghiệp vụ |
| --- | --- |
| **Nhân viên Kinh doanh** | Tạo mới, chăm sóc khách hàng cá nhân và doanh nghiệp trong phạm vi của mình, cập nhật giai đoạn vòng đời theo chiều tiến lên, đánh dấu Lead rác, chuyển đổi tiềm năng, bàn giao ngang cho đồng nghiệp cùng nhóm, theo dõi dòng thời gian tương tác. |
| **Nhân viên Hỗ trợ Khách hàng** | Tra cứu ngữ cảnh khách hàng khi tiếp nhận hội thoại hoặc vé hỗ trợ, ghi nhận ghi chú và hoạt động phục vụ khách. Bao gồm cả Tư vấn viên trò chuyện trực tuyến — cùng một vai trò, chỉ khác kênh phục vụ. Vì quyền phụ trách bản ghi thường thuộc đội kinh doanh (`BR-01.3`), vai trò này truy cập hồ sơ chủ yếu qua quyền đọc tự động khi có vé/hội thoại đang mở (`BR-35.4`); quyền đó là **chỉ đọc**, trừ hai thao tác thu hẹp phạm vi xử lý dữ liệu (gắn Hạn chế xử lý, hạ đồng thuận). |
| **Quản lý Kinh doanh** | Phân công và chuyển giao khách hàng (`FEAT-34`), phê duyệt các bước lùi giai đoạn và loại khách (`BR-12.4`, `BR-12.7`), duyệt Lead rác, hoàn tác chuyển đổi (`BR-14.2`), xác nhận bằng chứng liên hệ ngoài hệ thống (`BR-31.8`), cấu hình quy tắc phân bổ, duyệt xuất dữ liệu vượt hạn mức của nhân viên, theo dõi báo cáo danh bạ trong phạm vi đơn vị. |
| **Nhân viên Marketing** | Xuất danh sách khách hàng phục vụ chiến dịch, **xem** (không sửa) cấu hình chấm điểm, ghi nhận đồng thuận nhận tin theo kênh, gắn thẻ phân loại phục vụ phân khúc, xem báo cáo chuyển đổi và báo cáo nguồn gốc. **Không có quyền Xóa, Gộp hay Hoàn tác gộp bản ghi.** |
| **Quản lý Marketing** | Toàn bộ chức năng Marketing, cộng: cấu hình chấm điểm tiềm năng, ngưỡng thăng hạng và suy giảm điểm; duyệt xuất dữ liệu lớn của Nhân viên Marketing; đồng phê duyệt Chiến dịch Tái tiếp cận khách đã rời bỏ; xem báo cáo nguồn gốc và phân bổ doanh thu theo kênh. |
| **Quản trị viên Không gian làm việc** | Quản trị cấu hình trường dữ liệu, thực thi gộp và hoàn tác gộp, khôi phục giao dịch gộp bị gián đoạn, nhập/xuất dữ liệu hàng loạt, xử lý yêu cầu quyền chủ thể dữ liệu, loại khách đã trả tiền khi phát hiện gian lận. |
| **Chủ sở hữu Không gian làm việc** | Toàn quyền quản trị danh bạ, xem toàn bộ dữ liệu tổ chức, cấu hình chính sách bảo mật dữ liệu nhạy cảm và khai báo lịch làm việc của không gian làm việc. |
| **Tiến trình Hệ thống** | Tự động tính điểm tiềm năng và suy giảm điểm, sinh bước chuyển giai đoạn từ sự kiện Cơ hội bán hàng, phân bổ khách hàng tiềm năng, xử lý nhập/xuất theo hàng đợi, dọn dẹp Thùng rác quá hạn, khử định danh theo thời hạn lưu, cấp và thu hồi quyền đọc tự động. |

> **Ghi chú:** Tám vai trò trên tạo thành các cột của Ma trận phân quyền tại Mục 5. Đây là **vai trò nghiệp vụ** dùng để diễn đạt yêu cầu; "Quản trị viên" và "Chủ sở hữu" là **cấp bậc thành viên** của không gian làm việc. Mô hình vai trò, cấp bậc thành viên và cách phân giải quyền thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md).

**Vai trò chức năng (không có cột riêng trong Ma trận phân quyền):**

| Vai trò chức năng | Trách nhiệm | Quyền hạn áp dụng |
| --- | --- | --- |
| **Quản lý Khách hàng Hiện hữu** | Nhân viên hoặc Quản lý Kinh doanh được gán làm Người phụ trách của một khách hàng từ giai đoạn Customer trở lên. Chịu trách nhiệm duy trì, gia hạn và bán mở rộng; là người nhận thông báo khi khách chuyển sang Churned (`BR-12.5`). | Theo vai trò gốc (Nhân viên hoặc Quản lý Kinh doanh). |
| **Quản trị Chất lượng Dữ liệu** | Người được Chủ sở hữu hoặc Quản trị viên chỉ định rà soát trùng lặp định kỳ, chuẩn hóa dữ liệu, xử lý hồ sơ tạm tồn dư, xử lý Đề nghị gộp và giám sát chỉ số chất lượng dữ liệu (Mục 2.6). Trong tổ chức nhỏ, do Quản trị viên kiêm nhiệm. | Theo vai trò gốc được cấp. |
| **Người phụ trách Bảo vệ Dữ liệu** | **Bắt buộc chỉ định nếu tổ chức thuộc diện phải có theo pháp luật bảo vệ dữ liệu cá nhân.** Giám sát xử lý yêu cầu quyền chủ thể dữ liệu (`FEAT-33`), đồng phê duyệt các tham số có sàn pháp lý, nhận cảnh báo truy cập bất thường và báo cáo phơi bày dữ liệu. Nếu tổ chức không chỉ định, trách nhiệm thuộc Chủ sở hữu, và mọi nơi yêu cầu "hai người khác nhau" áp quy tắc thay thế tại `NFR-14`. | Theo vai trò gốc được cấp (thường là Quản trị viên). |

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
Không tiến trình tự động nào được xóa hồ sơ khách hàng đã định danh, ngoài đúng năm ngoại lệ có chủ đích liệt kê tại `BR-33.5` — mỗi ngoại lệ hoặc chỉ khử phần định danh mà giữ giá trị kinh doanh, hoặc chỉ thực thi một quyết định xóa mà con người đã đưa ra trước đó.

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

- **`BR-01.3` (Người phụ trách & đơn vị tổ chức khi tạo mới):** Khi tạo mới, người tạo tự động được gán làm Người phụ trách, và Đơn vị tổ chức của bản ghi được gán theo Đơn vị tổ chức của người tạo, trừ khi người có quyền chỉ định khác.

- **`BR-01.4` (Phạm vi dữ liệu):** Người dùng chỉ xem và sửa được khách hàng thuộc phạm vi dữ liệu được gán, theo bốn mức từ hẹp tới rộng: **Chỉ của mình** / **Của mình + cấp dưới và đơn vị của mình** / **Cả nhánh đơn vị** / **Toàn Không gian làm việc**. Định nghĩa từng mức và cách phân giải khi một người giữ nhiều vai trò thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md). Khi người dùng mở một bản ghi ngoài phạm vi, hệ thống xử lý theo `BR-17.3`.

- **`BR-01.5` (Nhóm trường Định danh KYC tùy chọn):** Ngoài các kênh liên lạc, hồ sơ khách hàng hỗ trợ lưu **tùy chọn** nhóm trường định danh nhạy cảm phục vụ xác thực hợp đồng: Số Căn cước công dân, Số Hộ chiếu, Ngày cấp, Nơi cấp. Nhóm trường này áp dụng che mặt nạ theo `FEAT-04` và không bắt buộc nhập khi tạo mới.

- **`BR-01.5b` (Mục đích, điều kiện bật và thời hạn lưu nhóm Định danh KYC) — sàn bắt buộc:**
  - **Mục đích duy nhất được phép:** xác thực danh tính phục vụ ký kết, thực hiện hợp đồng và nghĩa vụ định danh khách hàng theo quy định. **Không** được dùng cho tiếp thị, phân khúc, chấm điểm hay báo cáo.
  - **Điều kiện bật:** nhóm trường **mặc định tắt**. Chỉ Chủ sở hữu cùng Người phụ trách Bảo vệ Dữ liệu bật được, và phải khai báo mục đích sử dụng khi bật (Phụ lục B, `CFG-01-02`).
  - **Khi nhóm trường tắt:** các trường thuộc nhóm **không được lưu** và **không hiển thị với mọi vai trò** — mức "Ẩn trường" áp cho cả bốn cột của bảng `BR-04.3`, kể cả người có quyền chuyên biệt trên nhóm KYC.
  - **Thời hạn lưu:** tối đa **24 tháng** kể từ khi hợp đồng gần nhất của khách hàng kết thúc (Phụ lục B, `CFG-01-03`). Hết thời hạn, hệ thống **tự động khử vĩnh viễn phần định danh** — giữ hồ sơ khách hàng, chỉ xóa các trường KYC. Đây là ngoại lệ có chủ đích (a) của nguyên tắc "hệ thống không tự động xóa" tại `BR-33.5`.
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
| `AC-01.4.1` | Nhân viên A có phạm vi "Chỉ của mình", phụ trách 3 khách hàng, được chia sẻ 1 khách hàng; phòng có 200 khách hàng | A mở danh sách khách hàng | Chỉ thấy đúng 4 khách hàng |
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

**Quy tắc nghiệp vụ:**

- **`BR-02.1` (Bố cục chuẩn):** Hồ sơ gồm ba khu vực: **khung tóm tắt** (thông tin chính, điểm tiềm năng, trạng thái liên lạc), **khu vực trung tâm** (Dòng thời gian 360 độ, các thẻ Ghi chú, Công việc, Vé hỗ trợ, Cơ hội) và **khung liên kết** (doanh nghiệp trực thuộc, mối quan hệ cá nhân). Mọi giá trị hiển thị trên hồ sơ tuân theo chính sách che mặt nạ tại `FEAT-04`.

- **`BR-02.2` (Hành động nhanh & phân quyền):** Hồ sơ cho phép thực hiện nhanh: gửi email, gọi điện, tạo ghi chú, tạo công việc, tạo vé hỗ trợ. Hai hành động bị kiểm soát theo quyền:
  - **(a) "Tạo cơ hội mới"** chỉ hiển thị khi người dùng có quyền tạo Cơ hội bán hàng (quyền này thuộc [`deals-pipeline-srs.md`](./deals-pipeline-srs.md)). Người không có quyền — tiêu biểu là Nhân viên Hỗ trợ — thấy hành động **"Gợi ý Cơ hội"** thay thế, để chuyển nhu cầu mua cho đội kinh doanh.
  - **(b) "Chuyển giai đoạn vòng đời"** chỉ hiển thị cho các vai trò được ma trận Mục 5 (dòng `FEAT-12`) cấp quyền chuyển giai đoạn thủ công: Nhân viên Kinh doanh, Quản lý Kinh doanh, Quản trị viên và Chủ sở hữu. Theo **mặc định chuẩn hệ thống**, Nhân viên Hỗ trợ, Nhân viên Marketing và Quản lý Marketing **không** có quyền này. Đây là mặc định, **không phải sàn bắt buộc**: tenant nới được qua `CFG-05-02`.

  **Lý do nghiệp vụ:** Tuyến Hỗ trợ tiếp xúc khách nhưng không thẩm định được mức độ sẵn sàng mua; Marketing có tầm nhìn toàn tổ chức ở dạng chỉ đọc, nên nếu được chuyển giai đoạn thủ công thì một người có thể đổi giai đoạn của mọi bản ghi trong tổ chức. Giai đoạn của các bản ghi do Marketing nuôi dưỡng vẫn tiến lên được **qua đường tự động** theo ngưỡng điểm (`BR-15.5`).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-02.1.1` | Khách hàng có 2 cơ hội, 1 vé hỗ trợ, 3 ghi chú, 1 liên kết doanh nghiệp | Người phụ trách mở hồ sơ | Thấy đủ ba khu vực; các thẻ Cơ hội, Vé hỗ trợ, Ghi chú hiển thị đúng số lượng; khung liên kết có doanh nghiệp trực thuộc |
| `AC-02.2.1` | Nhân viên Hỗ trợ không có quyền tạo Cơ hội bán hàng | Mở hồ sơ khách hàng | Không có "Tạo cơ hội mới"; có "Gợi ý Cơ hội" |
| `AC-02.2.2` | Nhân viên Marketing, cấu hình mặc định | Mở hồ sơ khách hàng | Không có hành động "Chuyển giai đoạn vòng đời" |
| `AC-02.2.3` | Nhân viên Kinh doanh là Người phụ trách | Mở hồ sơ khách hàng | Có hành động "Chuyển giai đoạn vòng đời" |
| `AC-02.2.4` | Tenant nới quyền chuyển giai đoạn thủ công cho Marketing qua `CFG-05-02` | Nhân viên Marketing mở hồ sơ | Có hành động "Chuyển giai đoạn vòng đời" |

---

#### FEAT-03 — Quản lý Thẻ phân loại Hàng loạt

**Mô tả nghiệp vụ:** Gắn hoặc gỡ nhiều thẻ phân loại cho một hoặc hàng loạt khách hàng cùng lúc, phục vụ lọc và phân khúc chiến dịch.

**Vai trò sử dụng chính:** Nhân viên Kinh doanh, Nhân viên Marketing, Quản trị viên.

**Quy tắc nghiệp vụ:**

- **`BR-03.1` (Gắn/gỡ hàng loạt):** Người dùng chọn nhiều khách hàng trên danh sách và gắn hoặc gỡ thẻ cho toàn bộ lựa chọn trong một thao tác. Thao tác chỉ áp dụng trên các bản ghi thuộc phạm vi dữ liệu của người thực hiện.

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

---

#### FEAT-04 — Bảo vệ Dữ liệu Nhạy cảm & Mở khóa Mặt nạ

**Mô tả nghiệp vụ:** Bảo vệ dữ liệu cá nhân nhạy cảm bằng cơ chế che mặt nạ phân tầng theo **quan hệ của người xem với bản ghi**, thay vì che đồng loạt. Nguyên tắc nền tảng: tách **"quyền sử dụng để liên lạc"** (nhân viên phục vụ khách cần dùng số điện thoại hằng ngày) khỏi **"quyền xem giá trị thật"** (chỉ cần khi thực sự phải đọc, và luôn để lại dấu vết).

> **Tính năng này là nguồn duy nhất về chính sách che mặt nạ dữ liệu trong toàn tài liệu.** Mọi nơi khác nói về mặt nạ (`NFR-06`, `BR-35.4`, `BR-28.1`, các kịch bản UAT) dẫn chiếu về đây và không phát biểu lại chính sách theo cách riêng.

**Vai trò sử dụng chính:** Mọi người dùng xem hồ sơ khách hàng (chịu chi phối của chính sách); riêng thao tác mở khóa yêu cầu **quyền Mở khóa mặt nạ** được cấp riêng.

**Quy tắc nghiệp vụ:**

- **`BR-04.1` (Ba nhóm trường nhạy cảm):** Dữ liệu nhạy cảm được phân đúng ba nhóm:
  - **Nhóm 1 — Kênh liên lạc công việc:** email theo tên miền doanh nghiệp, số điện thoại di động và số điện thoại bàn dùng cho công việc.
  - **Nhóm 2 — Kênh liên lạc cá nhân:** email cá nhân (tên miền dịch vụ thư công cộng), số điện thoại được khách hàng khai báo là riêng tư.
  - **Nhóm 3 — Định danh KYC:** Số Căn cước công dân, Số Hộ chiếu, Ngày cấp, Nơi cấp (`BR-01.5`).

- **`BR-04.2` (Bốn mức hiển thị):** **Đầy đủ** (thấy trọn giá trị) · **Che một phần** (ví dụ "090****567", "m***@vinafoods.vn" — đủ để nhận diện và đối chiếu với khách, không đủ để sao chép sử dụng) · **Che hoàn toàn** (chỉ hiện dấu hiệu có dữ liệu, không hiện ký tự nào) · **Ẩn trường** (không hiển thị trường trên giao diện).

- **`BR-04.3` (Chính sách hiển thị theo quan hệ với bản ghi):** Chính sách mặc định chuẩn hệ thống như bảng dưới; tenant cấu hình được trong giới hạn sàn (Phụ lục B, `CFG-04-01`).

| Nhóm trường | (A) Người phụ trách & thành viên Đội ngũ phụ trách ở mức **Chỉnh sửa** (điều kiện tại `BR-04.5b`) | (B) Người trong phạm vi dữ liệu, **gồm thành viên Đội ngũ phụ trách ở mức Chỉ đọc** | (C) Người có **quyền đọc tạm**: đang xử lý vé/hội thoại của khách (`BR-35.4`), hoặc được hệ thống tự cấp khi yêu cầu quyền truy cập quá hạn hai lần (`BR-17.2c`) | (D) Người ngoài phạm vi dữ liệu |
| --- | --- | --- | --- | --- |
| **1. Kênh liên lạc công việc** | **Đầy đủ** | Che một phần | Che một phần | Che hoàn toàn |
| **2. Kênh liên lạc cá nhân** | Che một phần | Che một phần | Che một phần | Che hoàn toàn |
| **3. Định danh KYC** | Che hoàn toàn | Che hoàn toàn | Che hoàn toàn | Ẩn trường |

  **Lý do nghiệp vụ của từng cột:** (A) Nếu che cả kênh liên lạc công việc với chính người phụ trách, tổ chức buộc phải cấp quyền Mở khóa mặt nạ cho toàn bộ đội kinh doanh ngay tuần đầu — mặt nạ thành hình thức và `KPI-06` mất khả năng phát hiện bất thường. (B) Người trong phạm vi nhưng không trực tiếp phục vụ khách chỉ cần nhận diện, không cần sao chép. (C) Nhân viên Hỗ trợ đang xử lý vé/hội thoại cần đủ thông tin để **xác minh đúng người** và gọi lại khi chat bị ngắt; nếu che hoàn toàn thì họ phải hỏi lại khách số điện thoại mà hệ thống đã có và tổ chức lại phải cấp quyền mở khóa cho toàn tuyến Hỗ trợ. (D) Người không có quan hệ công việc nào với bản ghi không có nhu cầu nghiệp vụ để thấy dữ liệu liên lạc.

  Chính sách áp dụng đồng nhất ở **mọi nơi giá trị xuất hiện**: hồ sơ 360 độ, danh sách khách hàng, danh sách nhân sự trên hồ sơ doanh nghiệp (`BR-08.1`), khung Ngữ cảnh Khách hàng (`BR-28.1`), kết quả tìm kiếm và tệp dữ liệu xuất (`FEAT-25`, `NFR-06`).

- **`BR-04.4` (Mở khóa có kiểm toán — lối mở duy nhất):** Người dùng có quyền Mở khóa mặt nạ được nâng mức hiển thị lên **Đầy đủ** cho các trường Nhóm 1 và Nhóm 2 **trong phạm vi dữ liệu của mình**, và cho Nhóm 3 nếu được cấp thêm quyền chuyên biệt trên nhóm Định danh KYC. Mỗi lượt mở khóa bắt buộc ghi nhật ký theo `NFR-07`. Quyền Mở khóa mặt nạ **không** mở được dữ liệu ở cột (D) — người ngoài phạm vi phải xin quyền truy cập theo `BR-17.3` trước.

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

---

#### FEAT-05 — Thùng rác Khách hàng & Phục hồi Bản ghi

**Mô tả nghiệp vụ:** Khi xóa một khách hàng, hệ thống xóa mềm và đưa vào Thùng rác trong một thời hạn lưu; người có thẩm quyền khôi phục được nguyên vẹn bản ghi.

**Vai trò sử dụng chính:** Người có **quyền Xóa** trên khách hàng, Quản trị viên.

**Quy tắc nghiệp vụ:**

- **`BR-05.1` (Xóa mềm):** Bản ghi bị xóa được ẩn khỏi toàn bộ danh sách, tìm kiếm và báo cáo thông thường, ghi nhận thời điểm xóa và người xóa.

- **`BR-05.2` (Màn hình Thùng rác):** Màn hình Thùng rác liệt kê các bản ghi đã xóa kèm ngày xóa, người xóa và ngày dự kiến xóa vĩnh viễn.

- **`BR-05.3` (Khôi phục):** Người có quyền Xóa khôi phục được bản ghi; bản ghi trở lại nguyên vẹn cùng toàn bộ liên kết dữ liệu cũ.

- **`BR-05.4` (Dọn dẹp vĩnh viễn):** Tiến trình hệ thống tự động xóa vĩnh viễn bản ghi nằm trong Thùng rác quá thời hạn lưu — mặc định **30 ngày** ở gói tiêu chuẩn, nâng được tối đa 90 ngày ở gói Enterprise (Phụ lục B, `CFG-05-01`). Việc dọn dẹp chịu các chốt an toàn tại `BR-05.6`. Nhật ký kiểm toán về việc xóa được giữ lại sau khi bản ghi bị xóa vĩnh viễn.

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

**Quy tắc nghiệp vụ:**

- **`BR-06.1` (Thông tin doanh nghiệp):** Gồm Tên công ty (bắt buộc), Tên thương mại/viết tắt, Mã số thuế hoặc mã định danh doanh nghiệp, Ngành nghề kinh doanh, Quy mô nhân sự, Doanh thu hằng năm, Website, Địa chỉ trụ sở, Số điện thoại tổng đài.

- **`BR-06.2` (Căn cứ nhận diện trùng):** Mã số thuế hoặc tên miền website là căn cứ để hệ thống tự động kiểm tra trùng lặp doanh nghiệp khi tạo mới và khi nhập khẩu; khi phát hiện trùng, hệ thống cảnh báo kèm doanh nghiệp đã có.

  **Lý do nghiệp vụ:** Tên công ty không phải căn cứ tin cậy ("Cty CP ABC" và "ABC Corp" là một), trong khi mã số thuế là định danh pháp lý duy nhất của pháp nhân.

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

**Quy tắc nghiệp vụ:**

- **`BR-07.1` (Một doanh nghiệp mẹ):** Mỗi doanh nghiệp khai báo được tối đa một Doanh nghiệp Mẹ.

- **`BR-07.2` (Chống vòng lặp):** Hệ thống ngăn mọi quan hệ vòng tròn, trực tiếp hoặc gián tiếp: nếu A là mẹ của B thì B không thể là mẹ của A; nếu A là mẹ của B và B là mẹ của C thì C không thể là mẹ của A.

  **Lý do nghiệp vụ:** Một vòng lặp khiến sơ đồ cây và báo cáo hợp nhất không có điểm gốc — doanh số của tập đoàn bị cộng lặp vô hạn hoặc không tính được.

- **`BR-07.3` (Báo cáo hợp nhất):** Người dùng xem được sơ đồ cây tổ chức và báo cáo tổng doanh số, số lượng cơ hội hợp nhất của toàn bộ tập đoàn.

- **`BR-07.4` (Phạm vi dữ liệu trong báo cáo hợp nhất):** Báo cáo hợp nhất áp dụng **phạm vi dữ liệu của người xem** (`BR-01.4`) làm tiêu chí nền, với **đúng một ngoại lệ theo vai trò** tại (c):
  - **(a)** Số liệu hợp nhất luôn tính trên đúng tập công ty nằm trong phạm vi dữ liệu của người xem, kèm ghi chú rõ khi tập đó nhỏ hơn toàn tập đoàn: "Số liệu hiển thị theo phạm vi dữ liệu của bạn — không phải toàn tập đoàn".
  - **(b)** Người xem thấy số liệu hợp nhất **đầy đủ toàn tập đoàn** khi và chỉ khi phạm vi dữ liệu của họ bao trùm toàn bộ cây — theo ma trận Mục 5 là Quản trị viên, Chủ sở hữu, và Quản lý Kinh doanh khi cây nằm trọn trong phạm vi đơn vị của họ.
  - **(c) Riêng vai trò Marketing:** dù có phạm vi đọc toàn tổ chức, Marketing **chỉ xem cấu trúc pháp nhân, không xem chỉ số tài chính hợp nhất**.
  - **(d)** Sơ đồ cây hiển thị đầy đủ cấu trúc pháp nhân (tên công ty mẹ/con) vì đây là thông tin nhận diện, nhưng chỉ số tài chính của công ty ngoài phạm vi bị ẩn.

  **Lý do nghiệp vụ:** Báo cáo hợp nhất không được trở thành đường đọc doanh số của các đơn vị mà người xem không có quyền. Marketing cần phân khúc theo tập đoàn, không cần phân tích doanh thu.

- **`BR-07.5` (Giới hạn số cấp):** Cây tổ chức có tối đa **5 cấp** (Tập đoàn → Tổng công ty → Công ty thành viên → Chi nhánh → Đơn vị trực thuộc). Thiết lập vượt 5 cấp bị từ chối kèm đề nghị tổ chức lại cấu trúc.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-07.1.1` | Doanh nghiệp B chưa có doanh nghiệp mẹ | Khai báo A là Doanh nghiệp Mẹ của B | Lưu thành công; sơ đồ cây hiển thị A là mẹ của B |
| `AC-07.2.1` | A là mẹ của B | Khai báo B là mẹ của A | Từ chối, nêu rõ sẽ tạo quan hệ vòng tròn |
| `AC-07.2.2` | A là mẹ của B, B là mẹ của C | Khai báo C là mẹ của A | Từ chối, nêu rõ sẽ tạo quan hệ vòng tròn |
| `AC-07.3.1` | Tập đoàn có 3 công ty con, mỗi công ty có cơ hội đã thắng | Chủ sở hữu mở báo cáo hợp nhất | Tổng doanh số bằng tổng của cả tập đoàn |
| `AC-07.4.1` | Quản lý Kinh doanh chỉ có 2 trong 3 công ty con trong phạm vi | Mở báo cáo hợp nhất | Số liệu chỉ cộng 2 công ty; có ghi chú "Số liệu hiển thị theo phạm vi dữ liệu của bạn — không phải toàn tập đoàn" |
| `AC-07.4.2` | Tiếp nối AC-07.4.1 | Mở sơ đồ cây | Thấy tên cả 3 công ty con; chỉ số tài chính của công ty ngoài phạm vi bị ẩn |
| `AC-07.4.3` | Nhân viên Marketing | Mở sơ đồ cây và báo cáo hợp nhất của tập đoàn | Thấy cấu trúc pháp nhân; không thấy bất kỳ chỉ số tài chính hợp nhất nào |
| `AC-07.5.1` | Cây đã có đủ 5 cấp | Gắn một doanh nghiệp làm con của đơn vị ở cấp 5 | Từ chối, nêu giới hạn 5 cấp |

---

#### FEAT-08 — Hồ sơ Chi tiết Doanh nghiệp & Danh sách Nhân sự Liên hệ

**Mô tả nghiệp vụ:** Màn hình 360 độ của doanh nghiệp hiển thị danh sách nhân sự liên hệ thuộc công ty, các Cơ hội bán hàng, Vé hỗ trợ và Dòng thời gian tương tác của toàn bộ nhân sự trực thuộc.

**Vai trò sử dụng chính:** Mọi người dùng có quyền xem doanh nghiệp.

**Quy tắc nghiệp vụ:**

- **`BR-08.1` (Danh sách nhân sự):** Hiển thị nhân sự liên hệ kèm chức danh, số điện thoại, email và đánh dấu ai là **Người liên hệ chính** của doanh nghiệp. Kênh liên lạc trong danh sách tuân theo chính sách che mặt nạ tại `BR-04.3` theo quan hệ của người xem với từng khách hàng.

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

**Quy tắc nghiệp vụ:**

- **`BR-10.1` (Nội dung một liên kết):** Mỗi liên kết giữa một khách hàng cá nhân và một doanh nghiệp ghi nhận: Chức danh, Phòng ban công tác, **Vai trò liên kết** (Phụ lục A, A.5), Ngày bắt đầu, Ngày kết thúc và **Trạng thái liên kết** (Phụ lục A, A.5b: Đang công tác / Đã nghỉ việc / Tạm ngưng).

- **`BR-10.2` (Doanh nghiệp chính):** Mỗi khách hàng có tối đa **một** Doanh nghiệp chính, dùng để hiển thị mặc định trên danh sách và báo cáo tổng quan. Đặt một liên kết khác làm chính thì liên kết cũ tự động mất trạng thái chính.

- **`BR-10.3` (Quản lý liên kết):** Người dùng thêm, sửa và gỡ liên kết doanh nghiệp ngay trên hồ sơ khách hàng. Số liên kết tối đa trên một khách hàng theo gói dịch vụ tại `NFR-11`. Liên kết hiển thị ở cả hai phía: trên hồ sơ khách hàng và trong danh sách nhân sự của doanh nghiệp.

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

**Quy tắc nghiệp vụ:**

- **`BR-11.1` (Loại quan hệ):** Chọn từ danh mục chuẩn (Phụ lục A, A.6): Quản lý trực tiếp / Cấp dưới · Người giới thiệu / Được giới thiệu · Thành viên gia đình · Đối tác kinh doanh · Trợ lý / Người đại diện.

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

**Quy tắc nghiệp vụ:**

- **`BR-12.1` (Ghi nhận mỗi lần chuyển giai đoạn):** Mọi bước chuyển giai đoạn tuân thủ ma trận trên; mỗi lần chuyển, hệ thống ghi nhận thời điểm chuyển và người thực hiện (hoặc "Hệ thống" kèm sự kiện nguồn) vào Lịch sử giai đoạn (`FEAT-13`).

- **`BR-12.2` (Tự động nâng cấp từ sự kiện Cơ hội bán hàng):** Khi một khách hàng được tạo Cơ hội bán hàng mới, giai đoạn tự động nâng lên tối thiểu Opportunity. Khi Cơ hội chuyển sang Thắng, giai đoạn tự động nâng lên Customer. Sự kiện tạo Cơ hội và đóng thắng do phân hệ Cơ hội bán hàng phát ra — xem [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) (`BR-18.4` của tài liệu đó).

- **`BR-12.3` (Nhiều cơ hội & xử lý khi mọi cơ hội thất bại):**
  - Khách hàng đã đạt Customer (có ít nhất một Cơ hội Thắng) **không bị hạ hạng** khi các cơ hội bán thêm tiếp theo bị Thua.
  - Khách hàng chưa từng là Customer: khi **toàn bộ** Cơ hội đều Thua, hệ thống **không tự hạ về Lead/MQL** mà tự chuyển sang Nurturing và yêu cầu nhân viên kinh doanh chọn **Lý do không chuyển đổi** từ A.2 để Marketing có kịch bản tái tiếp cận phù hợp.

  **Lý do nghiệp vụ:** Hạ một khách đã trả tiền về tiền bán hàng chỉ vì một đơn bán thêm thất bại sẽ đẩy họ vào chiến dịch săn khách mới và làm sai báo cáo doanh thu. Với khách chưa mua, thất bại của cơ hội không có nghĩa là khách hết tiềm năng — họ cần được nuôi dưỡng, không bị loại.

- **`BR-12.4` (Chuyển Disqualified):** Chỉ Quản lý Kinh doanh trở lên mới được chuyển khách hàng ở giai đoạn tiền bán hàng sang Disqualified, bắt buộc chọn lý do loại từ A.1. Mở lại một bản ghi Disqualified về Lead hoặc Nurturing cũng chỉ Quản lý Kinh doanh trở lên thực hiện, bắt buộc chọn lý do từ A.17.

  **Lý do nghiệp vụ:** Loại khách là quyết định rút một bản ghi khỏi mọi chiến dịch và mọi phân bổ; nếu nhân viên tự loại được, khách khó chăm sóc sẽ bị loại để làm đẹp chỉ số cá nhân.

- **`BR-12.4b` (Đánh dấu nhanh Lead rác bởi nhân viên):** **Ngoại lệ của `BR-12.4` cho hai lý do hiển nhiên** trong A.1: "Thông tin giả/Spam/Lừa đảo" và "Trùng lặp với bản ghi khác". Nhân viên Kinh doanh được **đánh dấu "Lead rác"** với hai lý do này; việc đánh dấu có hiệu lực **ngay lập tức** ở ba mặt: (a) **đình chỉ đồng hồ cam kết thời gian phản hồi** (`BR-31.7`); (b) **loại bản ghi khỏi mẫu đo `KPI-03`**; (c) **dừng thu hồi và phân bổ lại**. Mọi lượt đánh dấu, dỡ dấu và duyệt đều ghi nhật ký (`NFR-07`).

  **Phạm vi áp dụng:** chỉ sáu giai đoạn tiền bán hàng. Hồ sơ ở Customer/Evangelist/Churned không thuộc phạm vi — nhóm lý do gian lận với các giai đoạn đó thuộc `BR-12.8`.

  **Xử lý sau khi đánh dấu:** Quản lý Kinh doanh duyệt theo lô. Việc chuyển bản ghi sang Disqualified **luôn cần thao tác tường minh của Quản lý Kinh doanh trở lên** — không có cơ chế mặc định chấp thuận. Nếu Quản lý không xử lý trong **5 ngày làm việc** (`BR-31.7b`), hệ thống **giữ nguyên** hiệu lực ba mặt của dấu "Lead rác", bản ghi **đứng nguyên giai đoạn hiện tại**, và hàng đợi chờ duyệt được **leo thang** lên Quản trị viên kèm báo cáo tồn đọng. Nếu Quản lý từ chối, dấu "Lead rác" bị gỡ và đồng hồ cam kết chạy lại từ thời điểm từ chối.

  **Lý do nghiệp vụ:** Spam và trùng lặp là hai lý do loại phổ biến nhất hằng ngày. Nếu cả hai đều đòi Quản lý duyệt trước khi có hiệu lực, mỗi Lead rác sẽ đi qua ba nhân viên (hai lần thu hồi) và làm bẩn chỉ số của cả ba, còn Quản lý thành người bấm nút cho từng dòng — dẫn tới cách lách là gửi email rỗng cho mọi Lead mới để có bằng chứng liên hệ, đúng hành vi `BR-31.8` muốn ngăn.

- **`BR-12.5` (Chuyển Churned):** Khi khách hàng chuyển sang Churned, hệ thống tự động thông báo Quản lý Khách hàng Hiện hữu và Quản lý Kinh doanh phụ trách, đồng thời dừng toàn bộ chiến dịch tiếp thị tự động đối với khách hàng đó.

- **`BR-12.5b` (Chiến dịch Tái tiếp cận — đường về Nurturing của khách đã rời bỏ):** Một bản ghi Churned chỉ được đưa trở lại Nurturing khi thuộc một **Chiến dịch Tái tiếp cận (Win-Back) đã được phê duyệt**. Ba ràng buộc:
  - **(a) Người phê duyệt:** **Quản lý Marketing cùng Quản lý Kinh doanh phụ trách tập khách đó** — một bên chịu trách nhiệm nội dung tiếp cận, một bên chịu trách nhiệm quan hệ khách hàng.
  - **(b) Ghi nhận:** phê duyệt được ghi trên chính chiến dịch kèm phạm vi tập khách, thời hạn hiệu lực và người phê duyệt, và ghi nhật ký (`NFR-07`).
  - **(c) Không ghi đè đồng thuận:** phê duyệt chiến dịch không thay đổi trạng thái đồng thuận — khách đang Từ chối nhận tin vẫn không nhận thư nhóm Tiếp thị (`BR-30.5`, `BR-30.10`).

  **Lý do nghiệp vụ:** Nhóm khách này đã chủ động chấm dứt quan hệ, nên tiếp cận lại có rủi ro pháp lý và thương hiệu cao hơn chiến dịch thông thường. Không có quy tắc này thì cụm từ "chiến dịch đã được phê duyệt" không xác định được ai phê duyệt và theo quy trình nào.

- **`BR-12.6` (Hiệu lực của Ma trận Chuyển đổi):** Ma trận áp dụng cho mọi nguồn tác động: thao tác thủ công, gộp bản ghi (`BR-19.8`), nhập khẩu hàng loạt, tích hợp từ hệ thống ngoài và chuyển đổi tự động. Bước chuyển ngoài ma trận bị từ chối kèm thông báo nêu rõ giai đoạn hiện tại và các giai đoạn hợp lệ có thể chuyển đến. Với nhập khẩu hàng loạt, dòng chứa bước chuyển không hợp lệ được ghi vào báo cáo lỗi (`BR-24.2`) thay vì làm dừng cả lô.

  **Ngoại lệ duy nhất — nguyên tắc 1:** Bước chuyển do hệ thống tự sinh từ sự kiện Cơ hội bán hàng (`BR-12.9`) và bước chuyển do nguyên tắc giai đoạn tiến xa nhất khi gộp (`BR-19.8`) **luôn hợp lệ theo thiết kế**, trong hai tình huống: **(i)** tenant cấu hình lại ma trận theo `CFG-12-01` và vô tình tắt một bước chuyển thuộc nguyên tắc 1 — nguyên tắc 1 vẫn thắng; **(ii)** thao tác gộp sinh ra bước chuyển theo `BR-19.8` — mọi bước chuyển thuộc nhóm này hợp lệ, **bất kể ma trận có liệt kê hay không**. Các ô "chỉ qua gộp" trong ma trận là ví dụ tường minh của nhóm (ii), không phải danh sách đóng.

  **Lý do nhóm (ii) nằm ngoài ma trận:** giai đoạn sau gộp không phải quyết định của người dùng về giai đoạn mà là hệ quả bắt buộc của `BR-19.8` — người dùng chỉ quyết định gộp hai bản ghi nào, giai đoạn kết quả **không thể ghi đè thủ công**. Nếu buộc liệt kê, ma trận phải chứa gần như mọi cặp giai đoạn và mất ý nghĩa kiểm soát đối với thao tác thủ công. Đổi lại, mỗi bước chuyển sinh từ gộp **bắt buộc** ghi nhật ký kèm thông báo cho Người phụ trách rà soát, và hoàn tác được trong thời hạn hoàn tác gộp (`BR-20.3`).

- **`BR-12.7` (Hạ hạng bắt buộc ghi lý do):** Mọi bước lùi về giai đoạn thấp hơn trên phễu tuyến tính, kể cả Evangelist → Customer, bắt buộc chọn lý do từ A.3 và chỉ dành cho Quản lý Kinh doanh trở lên. Lịch sử hạ hạng được ghi nhận riêng để phân tích chất lượng thẩm định của đội ngũ (`FEAT-13`). **Ngoại lệ duy nhất:** bước về Lead hoặc Subscriber do Hoàn tác Chuyển đổi (`BR-14.2`) dùng lý do hoàn tác riêng.

  **Lý do nghiệp vụ:** Bước lùi không có lý do làm mất khả năng phân biệt giữa thẩm định sai, khách đổi nhu cầu và nhập liệu nhầm — ba vấn đề cần ba cách khắc phục khác nhau.

- **`BR-12.8` (Ngoại lệ gian lận với khách đã trả tiền):** Khi phát hiện một khách hàng ở Customer, Evangelist **hoặc Churned** là gian lận (thông tin giả, mạo danh, lừa đảo), **chỉ Quản trị viên hoặc Chủ sở hữu** được chuyển sang Disqualified, chỉ với lý do thuộc nhóm Gian lận của A.1. Hệ thống bắt buộc hiển thị cảnh báo về ảnh hưởng tới báo cáo doanh thu đã ghi nhận và yêu cầu xác nhận hai bước. Quản lý Kinh doanh **không** có quyền này. Ba giai đoạn thuộc quy tắc này và sáu giai đoạn tiền bán hàng thuộc `BR-12.4` hợp thành đủ chín giai đoạn có thể bị loại; giai đoạn thứ mười là chính Disqualified.

  **Lý do nghiệp vụ:** Loại một khách đã từng trả tiền tác động tới doanh thu đã ghi nhận; đây phải là quyết định hiếm, của cấp quản trị, và chỉ với lý do gian lận — một khách đã trả tiền không thể bị loại vì "không đủ ngân sách".

- **`BR-12.9` (Bước chuyển do hệ thống sinh ra từ sự kiện Cơ hội bán hàng):** Các bước chuyển lên Opportunity khi Cơ hội mở đầu tiên được tạo, lên Customer khi có Cơ hội Thắng, và về Lead hoặc Subscriber khi Hoàn tác Chuyển đổi (trả về đúng giai đoạn trước khi chuyển đổi) được coi là **hợp lệ theo thiết kế** và không bị ma trận chặn, kể cả khi nhảy nhiều bậc (ví dụ Subscriber → Opportunity). Các bước chuyển này được ghi vào lịch sử giai đoạn với người thực hiện "Hệ thống" kèm sự kiện nguồn.

  **Lý do nghiệp vụ:** Opportunity được định nghĩa là "đang có ít nhất một Cơ hội bán hàng mở"; khi thực tế đã có Cơ hội thì giai đoạn buộc phải phản ánh đúng thực tế đó, nếu không chính tính năng Chuyển đổi Tiềm năng bị ma trận chặn.

- **`BR-12.10` (Giai đoạn mặc định khi tạo mới):** Giai đoạn khi khởi tạo bản ghi được gán tự động theo nguồn tạo (Phụ lục B, `CFG-12-02`), mặc định chuẩn hệ thống:
  - Tạo thủ công, tạo từ biểu mẫu website, tạo từ hội thoại, nhập khẩu từ tệp → **Lead**;
  - Đăng ký nhận bản tin/tài liệu (không thể hiện nhu cầu mua) → **Subscriber**;
  - Hồ sơ Khách hàng Tạm (`BR-01.1b`) → chưa gán giai đoạn cho tới khi trở thành khách hàng chính thức.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-12.1.1` | Khách hàng ở Lead | Nhân viên chuyển lên SQL sau thẩm định | Lịch sử giai đoạn có dòng Lead → SQL kèm thời điểm và tên nhân viên |
| `AC-12.2.1` | Khách hàng ở MQL | Nhân viên tạo Cơ hội bán hàng trực tiếp từ hồ sơ | Không có lỗi "bước chuyển không hợp lệ"; giai đoạn tự động lên Opportunity |
| `AC-12.2.2` | Khách hàng ở Opportunity, có một Cơ hội đang mở | Cơ hội được đóng Thắng | Giai đoạn tự động lên Customer |
| `AC-12.3.1` | Khách hàng ở Customer, có thêm một Cơ hội bán thêm | Cơ hội bán thêm bị đóng Thua | Giai đoạn vẫn là Customer |
| `AC-12.3.2` | Khách hàng ở Opportunity, chưa từng là Customer, có 2 Cơ hội đang mở | Cả hai Cơ hội bị đóng Thua | Giai đoạn chuyển sang Nurturing; nhân viên được yêu cầu chọn Lý do không chuyển đổi từ A.2 trước khi lưu |
| `AC-12.3.3` | Khách hàng ở Opportunity có 2 Cơ hội đang mở | Một Cơ hội bị đóng Thua, Cơ hội còn lại vẫn mở | Giai đoạn vẫn là Opportunity |
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

**Quy tắc nghiệp vụ:**

- **`BR-13.1` (Nội dung ghi nhận):** Mỗi lần chuyển giai đoạn ghi nhận: giai đoạn trước, giai đoạn sau, thời gian đã ở giai đoạn cũ (theo ngày và giờ), lý do chuyển (nếu có), người thực hiện hoặc "Hệ thống" kèm sự kiện nguồn.

- **`BR-13.2` (Tra cứu lịch sử):** Người dùng xem lịch sử giai đoạn ngay trên hồ sơ khách hàng, theo thứ tự thời gian.

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

**Luồng chính:**

1. Người dùng bấm **"Chuyển đổi Tiềm năng"** trên hồ sơ khách hàng tiềm năng.
2. Hộp thoại chuyển đổi hiển thị ba lựa chọn:
   - **Liên hệ:** nâng cấp bản ghi hiện tại thành Liên hệ chính thức. Giai đoạn đích: SQL nếu không tạo Cơ hội kèm theo; Opportunity nếu có Cơ hội. Cả hai bước chuyển đều hợp lệ kể cả khi khách đang ở Subscriber/Lead/MQL — nhánh Opportunity theo `BR-12.9`, nhánh SQL theo `BR-15.6` và đã có trong ma trận.
   - **Doanh nghiệp:** liên kết với một doanh nghiệp đã có, tạo mới doanh nghiệp từ tên công ty của khách, hoặc — với khách loại B2C — chọn "Không liên kết Doanh nghiệp" (`BR-01.6`).
   - **Cơ hội bán hàng:** tùy chọn tạo ngay một Cơ hội (tên, giá trị dự kiến, phễu, giai đoạn khởi đầu). Nếu doanh nghiệp được liên kết **đã có Cơ hội đang mở trên cùng phễu**, hệ thống hiển thị Cơ hội đó và mặc định gắn Liên hệ vào Cơ hội sẵn có (`BR-14.3`).
3. Người dùng bấm "Xác nhận chuyển đổi".
4. Hệ thống cập nhật Liên hệ, tạo/liên kết Doanh nghiệp, tạo Cơ hội mới **hoặc** gắn Liên hệ vào Cơ hội sẵn có, gán Người phụ trách thống nhất, rồi chuyển người dùng tới Cơ hội tương ứng.

**Quy tắc nghiệp vụ:**

- **`BR-14.1` (Toàn vẹn khi chuyển đổi):** Toàn bộ các bước tại luồng chính **cùng thành công hoặc cùng thất bại**. Nếu bất kỳ bước nào thất bại, không có Liên hệ, Doanh nghiệp hay Cơ hội nào ở trạng thái dang dở, và giai đoạn của khách giữ nguyên như trước khi chuyển đổi.

  **Lý do nghiệp vụ:** Một chuyển đổi dở dang (đã tạo doanh nghiệp nhưng chưa tạo cơ hội) để lại bản ghi mồ côi mà người dùng không biết phải dọn hay chuyển đổi lại, và lần chuyển đổi lại sẽ sinh trùng.

- **`BR-14.2` (Hoàn tác Chuyển đổi):** Trong vòng **24 giờ** sau khi chuyển đổi thành công (Phụ lục B, `CFG-14-01`), Quản lý Kinh doanh trở lên được **Hoàn tác Chuyển đổi**, với điều kiện Cơ hội vừa tạo **chưa có hoạt động thực tế nào** (chưa có ghi chú, chưa chuyển giai đoạn bán hàng, chưa đính kèm tài liệu). Khi hoàn tác:
  - (a) Cơ hội vừa tạo bị xóa mềm;
  - (b) Doanh nghiệp vừa tạo bị xóa mềm nếu chưa có khách hàng nào khác liên kết;
  - (c) Liên hệ trở về **đúng giai đoạn trước khi chuyển đổi** — bước chuyển hợp lệ theo `BR-12.9`, **không** yêu cầu lý do hạ hạng theo `BR-12.7`, nhưng bắt buộc chọn **Lý do hoàn tác** từ A.15;
  - (d) Điểm tiềm năng và nguồn gốc tiếp thị được giữ nguyên;
  - (e) Toàn bộ thao tác được ghi nhật ký (`NFR-07`).

  **Lý do nghiệp vụ:** Chuyển đổi nhầm là sai sót thường gặp; không có đường hoàn tác thì cách duy nhất là xóa tay từng bản ghi, làm hỏng lịch sử giai đoạn và nguồn gốc của khách. Giới hạn "chưa có hoạt động" bảo đảm hoàn tác không xóa mất công việc thật đã làm trên Cơ hội.

- **`BR-14.3` (Chống tạo trùng Cơ hội khi chuyển đổi):** Trước khi tạo Cơ hội mới, hệ thống bắt buộc kiểm tra doanh nghiệp được liên kết đã có **Cơ hội nào đang mở trên cùng phễu đích** hay chưa.
  - **Nếu có:** mặc định gắn Liên hệ vào Cơ hội đang mở đó, không tạo Cơ hội mới; người thực hiện được thông báo rõ đang gắn vào Cơ hội nào.
  - **Nếu người dùng vẫn muốn tạo riêng:** được chủ động chọn tạo Cơ hội mới, và quyết định này được ghi nhật ký để rà soát chất lượng dữ liệu.
  - Phạm vi kiểm tra là **theo từng phễu**, không phải toàn doanh nghiệp — một doanh nghiệp có thể có song song một Cơ hội bán mới và một Cơ hội gia hạn ở hai phễu khác nhau.

  **Lý do nghiệp vụ:** Không có quy tắc này, mỗi lần một nhân sự khác của cùng doanh nghiệp được chuyển đổi sẽ sinh thêm một Cơ hội trùng trên cùng thương vụ — dự báo doanh thu bị thổi phồng nhiều lần, và hai nhân viên có thể cùng đàm phán một hợp đồng mà không biết nhau. Quy tắc tương ứng phía Cơ hội bán hàng tại [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) (`FEAT-33` của tài liệu đó).

- **`BR-14.4` (Giai đoạn khi gắn vào Cơ hội sẵn có):** Khi Liên hệ được gắn vào một Cơ hội đang mở thay vì tạo Cơ hội mới, giai đoạn của Liên hệ vẫn được nâng lên Opportunity theo `BR-12.2`, vì thực tế thương mại (người này đang tham gia một thương vụ) là như nhau ở cả hai nhánh.

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
| `AC-14.3.1` | Doanh nghiệp Vina có Cơ hội "Cung ứng Q3" đang mở trên Phễu A | Chuyển đổi Lead "Nguyễn Văn Bình" của Vina, chọn Phễu A | Hộp thoại hiển thị Cơ hội "Cung ứng Q3" và mặc định gắn Bình vào đó; không phát sinh Cơ hội thứ hai; dự báo doanh thu không tăng |
| `AC-14.3.2` | Tiếp nối AC-14.3.1 | Nhân viên chọn vẫn tạo Cơ hội riêng | Cơ hội thứ hai được tạo; nhật ký ghi quyết định kèm người thực hiện |
| `AC-14.3.3` | Doanh nghiệp Vina có Cơ hội đang mở trên Phễu A | Chuyển đổi một Lead của Vina, chọn Phễu B | Cơ hội mới được tạo trên Phễu B, không có gợi ý gắn vào Cơ hội ở Phễu A |
| `AC-14.4.1` | Tiếp nối AC-14.3.1 | Xem giai đoạn của Bình | Bình ở Opportunity |

---

#### FEAT-31 — Phân bổ Khách hàng Tiềm năng Tự động

**Mô tả nghiệp vụ:** Khi khách hàng tiềm năng được tạo tự động từ các kênh số (biểu mẫu website, tích hợp từ hệ thống ngoài, trợ lý trò chuyện tự động, quảng cáo) mà không có người tạo trực tiếp, hệ thống tự động phân bổ Người phụ trách theo bộ quy tắc định sẵn.

**Vai trò sử dụng chính:** Tiến trình Hệ thống (thực thi), Quản lý Kinh doanh (cấu hình quy tắc), Quản trị viên.

**Quy tắc nghiệp vụ:**

- **`BR-31.1` (Chia vòng lần lượt):** Khi không có quy tắc đặc biệt nào khớp, hệ thống chia khách hàng tiềm năng lần lượt đều nhau cho các thành viên kinh doanh **đang khả dụng** trong nhóm. Người đang ở trạng thái "không khả dụng" (`BR-34.6`) không nhận phân bổ mới.

- **`BR-31.2` (Theo vùng địa lý):** Nếu khách hàng tiềm năng có thông tin quốc gia hoặc tỉnh/thành, ưu tiên phân bổ cho nhân viên phụ trách vùng địa lý tương ứng.

- **`BR-31.3` (Theo ngành nghề):** Nếu khách hàng tiềm năng có thông tin ngành nghề, ưu tiên phân bổ cho nhân viên chuyên ngành tương ứng.

- **`BR-31.3b` (Thứ tự ưu tiên giữa các quy tắc):** Khi khớp nhiều quy tắc cùng lúc, hệ thống áp dụng theo thứ tự giảm dần: **(1)** Người phụ trách hiện hữu (`BR-31.6` — ưu tiên tuyệt đối); **(2)** Vùng địa lý; **(3)** Ngành nghề; **(4)** Chia vòng lần lượt; **(5)** Hàng đợi "Chưa phân công" (`BR-31.4`). Thứ tự từ (2) đến (4) là tham số cấu hình (Phụ lục B, `CFG-31-02`); vị trí (1) cố định.

  **Lý do nghiệp vụ:** Có tổ chức phân đội theo ngành trước, có tổ chức phân theo vùng trước; nhưng không tổ chức nào muốn một khách đang có người phụ trách lại bị chia cho người thứ hai.

- **`BR-31.4` (Khi không khớp quy tắc nào):** Khi không có quy tắc nào khớp hoặc không có nhân viên khả dụng, khách hàng tiềm năng được đưa vào hàng đợi **"Chưa phân công"** và Quản lý Kinh doanh nhận thông báo để phân công thủ công.

- **`BR-31.5` (Quyền cấu hình):** Quy tắc phân bổ chỉ được tạo, sửa, xóa bởi Quản lý Kinh doanh, Quản trị viên và Chủ sở hữu — khớp ma trận Mục 5 dòng `FEAT-31`.

- **`BR-31.6` (Ưu tiên tuyệt đối cho Người phụ trách hiện hữu):** Trước khi áp bất kỳ quy tắc phân bổ nào, hệ thống bắt buộc kiểm tra trùng lặp (`FEAT-17`). Nếu khách hàng tiềm năng mới trùng với một bản ghi đã có **Người phụ trách đang hoạt động**, hệ thống:
  - **không tạo bản ghi mới** và **không phân bổ cho người khác** — tương tác mới được ghi vào Dòng thời gian của bản ghi hiện hữu;
  - thông báo cho Người phụ trách hiện hữu: "Khách hàng bạn đang phụ trách vừa phát sinh yêu cầu mới từ kênh [tên kênh]";
  - nếu Người phụ trách hiện hữu đã rời tổ chức hoặc bị vô hiệu hóa, bản ghi được phân bổ lại theo quy tắc thông thường kèm ghi chú lý do chuyển giao.

  Để "chống trùng chủ" không thành chỗ mất doanh thu, yêu cầu mới trên bản ghi đã có chủ bắt buộc sinh ra một **"Yêu cầu chờ xử lý"** có cam kết thời gian riêng, áp cùng bộ thời hạn tại `BR-31.7` theo mức ưu tiên của bản ghi. Quá hạn thì leo thang lên Quản lý Kinh doanh, và Quản lý được **chỉ định người xử lý thay** (không đổi Người phụ trách chính, có thể dùng Đội ngũ phụ trách tại `FEAT-35`). Người phụ trách đang nghỉ phép hoặc "không khả dụng" (`BR-34.6`) thì yêu cầu được chuyển ngay cho người xử lý thay.

  **Lý do nghiệp vụ:** Bảo đảm không có chuyện hai nhân viên cùng liên hệ một khách hàng (Mục 2.1, vấn đề 2). Nhưng khách cũ quay lại hỏi mua thêm là nguồn nhu cầu chất lượng cao nhất — nếu không có thời hạn và không ai được phép nhận thay, yêu cầu sẽ nằm im vô thời hạn trong dòng thời gian.

- **`BR-31.7` (Cam kết thời gian phản hồi & thu hồi khách bị bỏ quên):** Sau khi được phân bổ, khách hàng tiềm năng phải được liên hệ lần đầu — bằng một bằng chứng được công nhận tại `BR-31.8` — trong thời hạn cam kết mặc định (Phụ lục B, `CFG-31-01`):

| Mức ưu tiên | Dải điểm tiềm năng | Thời hạn phản hồi lần đầu | Hành động khi quá hạn |
| --- | --- | --- | --- |
| **Ưu tiên cao** | Từ Ngưỡng Ưu tiên cao trở lên (mặc định ≥ 85) | **1 giờ làm việc** | Nhắc người phụ trách và thông báo Quản lý Kinh doanh |
| **Thông thường** | Từ Ngưỡng MQL tới dưới Ngưỡng Ưu tiên cao (mặc định 40–84) | **4 giờ làm việc** | Nhắc người phụ trách |
| **Thấp** | Dưới Ngưỡng MQL (mặc định < 40) | **24 giờ làm việc** | Ghi vào báo cáo tồn đọng |

  Ba dải điểm **luôn được tính lại từ hai ngưỡng tại `CFG-15-01`**, không đóng cứng con số, nên khi tenant hiệu chỉnh ngưỡng thì ba dải vẫn kề nhau và không chồng lấn.

  Nếu khách vẫn không được phản hồi sau **hai lần thời hạn** nêu trên, hệ thống tự động **thu hồi và phân bổ lại** cho thành viên khác theo `BR-31.1`, ghi lý do "Quá hạn phản hồi" vào lịch sử và thông báo Quản lý Kinh doanh. Tối đa **2 lần thu hồi tự động** cho mỗi khách; đến lần thứ ba, khách được đưa vào hàng đợi "Chưa phân công" để Quản lý Kinh doanh phân công thủ công và chịu trách nhiệm. Đồng hồ cam kết **dừng** khi bản ghi bị đánh dấu Lead rác (`BR-12.4b`) hoặc bị gắn Hạn chế xử lý (`BR-30.6`), và không áp dụng cho bản ghi nhập khẩu trước khi có tương tác đầu tiên (`BR-15.7` (a)).

  **Lý do nghiệp vụ:** Tốc độ phản hồi lần đầu là yếu tố quyết định tỷ lệ chuyển đổi của khách hàng tiềm năng. Giới hạn số lần thu hồi để khách không bị quay vòng vô hạn giữa các nhân viên mà không ai chịu trách nhiệm.

- **`BR-31.7b` (Lịch làm việc dùng để tính thời hạn):** Mọi thời hạn tính bằng "giờ làm việc" hoặc "ngày làm việc" trong tài liệu (`BR-12.4b`, `BR-17.2c`, `BR-31.6`, `BR-31.7`, `BR-34.1b`, `BR-35.3b`) được tính theo **Lịch làm việc của không gian làm việc**: múi giờ, các ngày làm việc trong tuần, giờ bắt đầu và kết thúc mỗi ngày, và danh mục ngày lễ theo từng năm. Lịch do Chủ sở hữu khai báo (Phụ lục B, `CFG-31-03`); khi chưa khai báo, hệ thống dùng mặc định Thứ Hai – Thứ Sáu, 08:00 – 17:30 theo múi giờ của không gian làm việc, không có ngày lễ. Lịch hợp lệ phải có **ít nhất một ngày làm việc trong tuần** và giờ kết thúc **sau** giờ bắt đầu.

  **Lý do nghiệp vụ:** Không có định nghĩa này thì hai người kiểm thử tính ra hai thời điểm quá hạn khác nhau. Một lịch không có ngày làm việc nào sẽ khiến mọi thời hạn tính bằng giờ làm việc không bao giờ đến hạn, vô hiệu hóa toàn bộ cơ chế leo thang và thu hồi.

- **`BR-31.8` (Bằng chứng "đã liên hệ lần đầu"):** Chỉ các bằng chứng sau được công nhận:
  - **Nhóm 1 — bằng chứng hệ thống tự sinh:** cuộc gọi có ghi nhận thời lượng, email đã gửi từ hệ thống, tin nhắn đã gửi trên kênh đã tích hợp, hoặc cuộc hẹn đã được tạo với khách hàng. Các bằng chứng này do `FEAT-36` sinh tự động (`BR-36.5`) nên không tạo khống được.
  - **Nhóm 2 — liên hệ ngoài hệ thống có xác nhận của Quản lý:** gặp trực tiếp, khách chỉ trả lời qua kênh cá nhân của nhân viên, hoặc nhân viên gọi bằng số không tích hợp. Nhân viên khai báo và **Quản lý Kinh doanh xác nhận**; mỗi nhân viên dùng tối đa **10 lần/tháng** (Phụ lục B, `CFG-31-04`), có ghi nhật ký, và được **thống kê riêng** trong `KPI-03`.
  - **Không được công nhận:** ghi chú thủ công đơn thuần không kèm bằng chứng nhóm 1 hoặc nhóm 2.
  - **Khi không gian làm việc chưa tích hợp kênh nào** sinh được bằng chứng nhóm 1, cơ chế **thu hồi tự động tại `BR-31.7` không được kích hoạt** — hệ thống chỉ nhắc nhở và ghi vào báo cáo tồn đọng.

  **Lý do nghiệp vụ:** Nếu ghi chú tay được tính, nhân viên chỉ cần gõ "đã gọi, không bắt máy" là đạt cam kết, và `KPI-03` đạt trên giấy trong khi khách chưa được liên hệ. Ngược lại, thu hồi khách khỏi người đang thực sự làm việc chỉ vì hệ thống không nhìn thấy công việc đó sẽ khiến khách nhận cuộc gọi thứ hai từ cùng công ty.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-31.1.1` | Nhóm có 3 nhân viên khả dụng, không có quy tắc vùng/ngành khớp | 3 khách hàng tiềm năng mới lần lượt đổ về | Mỗi nhân viên nhận đúng 1 khách |
| `AC-31.1.2` | Một trong 3 nhân viên đang nghỉ phép (không khả dụng) | 2 khách mới đổ về | Hai khách được chia cho 2 nhân viên còn lại; người nghỉ phép không nhận |
| `AC-31.2.1` | Có nhân viên phụ trách Miền Trung đang khả dụng | Khách mới từ biểu mẫu website có tỉnh/thành Đà Nẵng | Khách được gán cho nhân viên Miền Trung, không qua chia vòng |
| `AC-31.3b.1` | Tenant đặt thứ tự Ngành nghề trước Vùng địa lý | Khách mới khớp cả quy tắc vùng và quy tắc ngành | Khách được gán theo quy tắc ngành |
| `AC-31.4.1` | Khách mới không có thông tin khớp quy tắc nào và không còn nhân viên khả dụng | Khách đổ về | Khách vào hàng đợi "Chưa phân công"; Quản lý Kinh doanh nhận thông báo |
| `AC-31.5.1` | Nhân viên Kinh doanh | Mở màn hình quy tắc phân bổ | Không sửa được quy tắc |
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

**Quy tắc nghiệp vụ:**

- **`BR-32.1` (Kênh nguồn gốc):** Mỗi khách hàng ghi nhận **Kênh nguồn gốc chính** (Phụ lục A, A.7, gồm cả giá trị "Không xác định" dùng cho `KPI-08`) và **Chi tiết kênh con**.

- **`BR-32.2` (Tham số chiến dịch):** Khi khách được tạo qua biểu mẫu website hoặc tích hợp từ hệ thống ngoài, hệ thống tự động ghi nhận toàn bộ tham số chiến dịch (UTM) đi kèm: nguồn, phương tiện, tên chiến dịch, nội dung và từ khóa.

- **`BR-32.3` (Không ghi đè nguồn gốc):** Kênh nguồn gốc và tham số chiến dịch được ghi nhận **một lần** tại thời điểm tạo bản ghi và **không được ghi đè** sau đó bởi bất kỳ nguồn nào — kể cả biểu mẫu gửi lại, nhập khẩu theo chiến lược cập nhật hay gộp bản ghi (`BR-19.5`) — theo nguyên tắc **ghi nhận điểm chạm đầu tiên**. Người dùng nghiệp vụ chỉ xem, không sửa.

  **Lý do nghiệp vụ:** Nếu nguồn gốc bị ghi đè bởi lần tương tác sau, mọi khách hàng đều sẽ "đến từ" kênh cuối cùng họ chạm vào, và ngân sách tiếp thị bị phân bổ sai cho kênh thu hoạch thay vì kênh tạo nhu cầu.

- **`BR-32.3b` (Sửa sai nguồn gốc do lỗi kỹ thuật):** Ngoại lệ duy nhất của `BR-32.3`: khi có **bằng chứng lỗi hệ thống** làm ghi nhận sai nguồn gốc trên diện rộng (biểu mẫu cấu hình sai tham số, một lô nhập khẩu ánh xạ lệch cột nguồn, tích hợp lỗi khiến hàng loạt bản ghi rơi vào "Không xác định"), **Quản trị viên hoặc Chủ sở hữu** được sửa nguồn gốc theo lô, với đủ bốn điều kiện: **(a)** bắt buộc nhập lý do và mô tả bằng chứng lỗi; **(b)** ghi nhật ký (`NFR-07`); **(c)** **giữ nguyên giá trị gốc trong lịch sử bản ghi**; **(d)** báo cáo phân tích nguồn gốc và phân bổ doanh thu theo kênh nêu rõ **số bản ghi đã được sửa nguồn** trong kỳ.

  **Lý do nghiệp vụ:** Không có ngoại lệ này thì cách duy nhất để chữa số liệu sai là xóa và tạo lại bản ghi, làm mất dòng thời gian, điểm tiềm năng và lịch sử giai đoạn — thiệt hại lớn hơn nhiều so với việc sai nguồn.

- **`BR-32.4` (Quyền xem báo cáo nguồn gốc):** Chỉ Nhân viên Marketing trở lên được xem báo cáo phân tích nguồn gốc khách hàng và phân bổ doanh thu theo kênh; các vai trò khác chỉ xem trường nguồn gốc trên hồ sơ.

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

**Quy tắc nghiệp vụ:**

- **`BR-15.1` (Điểm Hồ sơ):** Với khách loại B2B, mặc định: có email doanh nghiệp hợp lệ +10; có số điện thoại di động +10; có chức danh quản lý cấp cao (Giám đốc, Phó Chủ tịch, lãnh đạo cấp cao) +20; thuộc ngành nghề mục tiêu +15. Khách loại B2C áp bộ tiêu chí riêng, không dùng "email doanh nghiệp" và "chức danh quản lý" (`BR-01.6`).

- **`BR-15.2` (Điểm Tương tác):** Mặc định: mở email chiến dịch +5 mỗi lần; nhấp liên kết trong email/tin nhắn +10 mỗi lần; gửi tin nhắn qua trò chuyện trực tuyến hoặc ứng dụng nhắn tin +15; đặt lịch hẹn hoặc tham gia buổi trình diễn sản phẩm +30.

- **`BR-15.3` (Trần điểm & tần suất cộng điểm):** Điểm tiềm năng bằng Điểm Hồ sơ cộng Điểm Tương tác (sau khi đã áp suy giảm theo `FEAT-16`), chặn trong khoảng 0–100. Mỗi loại hành vi tương tác chỉ được cộng điểm **tối đa một lần mỗi ngày** cho mỗi khách hàng.

  **Lý do nghiệp vụ:** Không giới hạn tần suất thì một khách mở cùng một email mười lần (hoặc một công cụ tự động mở thư) sẽ thành "khách nóng" giả, chiếm chỗ ưu tiên của khách thật.

- **`BR-15.4` (Cấu hình quy tắc chấm điểm):** Các mức điểm tại `BR-15.1`, `BR-15.2` là giá trị mặc định, cấu hình được theo không gian làm việc. Quản lý Marketing, Quản trị viên và Chủ sở hữu được sửa trọng số, thêm/xóa tiêu chí qua màn hình Cấu hình Quy tắc Chấm điểm — khớp ma trận Mục 5 dòng `FEAT-15`. Nhân viên Marketing chỉ xem, không sửa. Mọi thay đổi được ghi nhật ký và **áp dụng từ lượt tính điểm kế tiếp**, không tính lại điểm đã có.

  **Lý do nghiệp vụ:** Tính lại toàn bộ điểm cũ sau mỗi lần chỉnh trọng số sẽ làm hàng loạt khách đột ngột vượt hoặc rơi khỏi ngưỡng, phát sinh thông báo và thăng hạng hàng loạt không phản ánh hành vi thật nào.

- **`BR-15.5` (Ngưỡng điểm thăng hạng vòng đời):** Điểm tiềm năng gắn với giai đoạn vòng đời qua bộ ngưỡng mặc định (Phụ lục B, `CFG-15-01`):

| Ngưỡng | Điều kiện điểm | Hành vi hệ thống |
| --- | --- | --- |
| **Ngưỡng MQL** | Tổng **≥ 40** **và** Điểm Tương tác **≥ 15** | Khách ở Subscriber, Lead hoặc Nurturing tự động thăng hạng lên MQL; Marketing nhận thông báo. Điều kiện kép là bắt buộc (`BR-15.7`) |
| **Ngưỡng SQL (sẵn sàng chuyển kinh doanh)** | Tổng **≥ 70** | Khách ở MQL được đánh dấu **"Sẵn sàng chuyển Sales"** và đưa vào hàng đợi thẩm định; **không** tự động lên SQL (`BR-15.6`) |
| **Ngưỡng Ưu tiên cao** | Tổng **≥ 85** | Gắn nhãn **"Khách hàng nóng"**, ưu tiên hiển thị đầu hàng đợi phân bổ |
| **Dưới Ngưỡng MQL** | Tổng **< 40** | Không tự động thăng hạng; tiếp tục nuôi dưỡng qua chiến dịch định kỳ |

  Việc thăng hạng tự động tuân thủ ma trận tại `FEAT-12` — ma trận đã cho phép Subscriber → MQL, Lead → MQL và Nurturing → MQL.

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

**Quy tắc nghiệp vụ:**

- **`BR-16.1` (Mốc suy giảm thứ nhất):** Tiến trình hệ thống chạy lúc **02:00 hằng ngày** theo múi giờ không gian làm việc (Mục 2.3). Nếu khách hàng không có tương tác nào trong **14 ngày**, Điểm Tương tác bị giảm **10%** so với điểm hiện có, làm tròn xuống số nguyên. Mốc 14 ngày chỉ trừ **một lần** cho tới khi chạm mốc thứ hai, không trừ lặp lại mỗi ngày.

- **`BR-16.2` (Mốc suy giảm thứ hai):** Nếu không có tương tác trong **30 ngày**, Điểm Tương tác bị giảm tiếp **25%** so với điểm hiện có, làm tròn xuống. Mỗi mốc áp dụng một lần trong mỗi khoảng không tương tác liên tục; khi khách có tương tác mới, việc đếm ngày không tương tác bắt đầu lại từ đầu. Các mốc và tỷ lệ là tham số cấu hình (Phụ lục B, `CFG-16-01`).

- **`BR-16.3` (Sàn điểm):** Điểm tiềm năng không bao giờ nhận giá trị âm; sàn là 0.

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

**Mô tả nghiệp vụ:** Cảnh báo trùng lặp tức thì khi người dùng đang nhập thông tin, và cung cấp công cụ quét trùng lặp toàn không gian làm việc cho Quản trị viên và Quản trị Chất lượng Dữ liệu.

**Vai trò sử dụng chính:** Mọi người dùng tạo hoặc sửa khách hàng, Quản trị viên, Quản trị Chất lượng Dữ liệu.

**Quy tắc nghiệp vụ:**

- **`BR-17.1` (Tiêu chí trùng lặp & mức độ tin cậy):**
  - **Tiêu chí chắc chắn:** trùng khớp chính xác địa chỉ email, hoặc số điện thoại đã chuẩn hóa (`BR-01.2`).
  - **Tiêu chí tham khảo:** trùng khớp họ tên kết hợp tên công ty (với khách B2C: họ tên kết hợp ngày sinh hoặc địa chỉ — `BR-01.6`). Tiêu chí này có tỷ lệ nhận diện sai cao với dữ liệu tiếng Việt (hai người khác nhau cùng tên tại một doanh nghiệp lớn; "Cty CP ABC" và "ABC Corp" là một công ty nhưng không khớp chữ), nên **tuyệt đối không dùng làm căn cứ gộp tự động** và **không bao giờ dùng để chặn tạo bản ghi**.

- **`BR-17.2` (Chính sách xử lý khi phát hiện trùng):** Tham số cấu hình (Phụ lục B, `CFG-17-01`), mặc định chuẩn hệ thống:
  - **Trùng theo Tiêu chí chắc chắn → chặn tạo bản ghi mới.** Hệ thống không cho lưu, thay vào đó dẫn người dùng tới bản ghi hiện hữu để bổ sung thông tin hoặc ghi nhận tương tác mới. Tenant đổi được sang "Chỉ cảnh báo, vẫn cho lưu"; khi đó mỗi lượt bỏ qua cảnh báo được ghi nhật ký.
  - **Trùng theo Tiêu chí tham khảo → chỉ cảnh báo mềm**, kèm danh sách bản ghi nghi trùng; người dùng vẫn lưu được.
  - **Lối ra khi thực sự là hai người khác nhau:** ngay trên màn hình cảnh báo, người tạo được chọn **"Đây là người khác dùng chung định danh này"**. Hệ thống cho tạo bản ghi mới, **tự động gắn nhãn Định danh dùng chung** cho email/số điện thoại đó (`BR-30.2`) và ghi nhật ký người xác nhận. Lối ra này không đòi đổi chính sách của cả tenant.

  **Lý do nghiệp vụ:** Chặn cứng không có lối ra thì các tình huống hằng ngày — hai vợ chồng dùng chung email, hai người cùng số tổng đài, khách cá nhân dùng số của người thân — sẽ không tạo được bản ghi thứ hai, và nhân viên lách bằng cách thêm dấu chấm vào email hoặc bỏ trống email, làm `KPI-01` tệ hơn chính điều quy tắc muốn bảo vệ.

- **`BR-17.2b` (Các nguồn thực tế sinh ra bản ghi trùng):** Chặn tại màn hình tạo mới **không** loại bỏ được bản ghi trùng khỏi hệ thống, vì bản ghi trùng còn đến từ: nhập khẩu hàng loạt theo chiến lược người dùng chọn (`BR-23.3`); tích hợp và các kênh tự động có dữ liệu lệch định dạng; dữ liệu lịch sử tạo trước khi áp chính sách hoặc trong thời gian tenant cấu hình "chỉ cảnh báo"; trùng theo Tiêu chí tham khảo (vốn không bao giờ bị chặn); bản ghi từng được đánh dấu Định danh dùng chung nhưng sau đó xác định lại là cùng một người. Đây là lý do các tính năng gộp bản ghi (`FEAT-18`, `19`, `20`) vẫn cần thiết.

- **`BR-17.2c` (Cam kết thời gian cho ba loại yêu cầu tại `BR-17.3`):** Ba hành động tại `BR-17.3` dùng chung thời hạn với `BR-31.7` — mặc định **4 giờ làm việc** (Phụ lục B, `CFG-31-01`) — nhưng hành vi khi quá hạn khác nhau:
  - **(i) Yêu cầu quyền truy cập:** quá hạn thì leo thang lên Quản lý Kinh doanh của Người phụ trách; nếu Quản lý cũng không xử lý trong thời hạn thứ hai, hệ thống **tự cấp quyền đọc tạm có ghi nhật ký** theo cơ chế `BR-35.4` (cột (C) của `BR-04.3`). Quyền tự cấp này có hiệu lực **7 ngày** hoặc hết sớm hơn khi Người phụ trách/Quản lý xử lý yêu cầu, tùy điều kiện nào đến trước; hết hạn mà yêu cầu chưa được xử lý thì yêu cầu đóng với lý do **"Không được xử lý"** và người yêu cầu phải tạo yêu cầu mới.
  - **(ii) Đề nghị chuyển giao:** chỉ leo thang lên Quản lý Kinh doanh, **không** có hành vi tự động nào, vì chuyển giao quyền phụ trách luôn cần quyết định của con người (`FEAT-34`).
  - **(iii) Đề nghị gộp:** leo thang lên Quản trị Chất lượng Dữ liệu hoặc Quản trị viên, **không** có hành vi tự động nào, vì gộp tác động khó đảo ngược lên đồng thuận và giai đoạn của cả hai bản ghi (`BR-19.6`, `BR-19.8`).

  **Lý do nghiệp vụ:** Nếu yêu cầu không có thời hạn và không ai buộc phải trả lời, nhân viên đang có khách trước mặt sẽ bỏ dùng và quay về cách lách tại `BR-17.2`. Quyền tự cấp phải có hạn, nếu không một quyền cấp vì im lặng sẽ tồn tại vĩnh viễn.

- **`BR-17.3` (Hiển thị bản ghi ngoài phạm vi dữ liệu):** Áp dụng cho **cả hai** tình huống: (i) bản ghi trùng được cảnh báo khi tạo/sửa nhưng nằm ngoài phạm vi dữ liệu của người dùng; (ii) người dùng mở trực tiếp một bản ghi ngoài phạm vi (kể cả khi vừa mất quyền đọc tạm theo `BR-35.4` (d)). Hệ thống **không** hiển thị "không có quyền truy cập" mà hiển thị **thông tin tối thiểu để nhận diện**: tên khách hàng dạng viết tắt, tên Người phụ trách, Đơn vị tổ chức phụ trách và thời điểm tương tác gần nhất, kèm **ba hành động**:
  - **"Yêu cầu quyền truy cập"** (xin quyền đọc hoặc quyền sửa) và **"Đề nghị chuyển giao"** — gửi tới Người phụ trách hiện hữu và Quản lý Kinh doanh của họ;
  - **"Đề nghị gộp"** — gửi tới **Quản trị Chất lượng Dữ liệu hoặc Quản trị viên**; Người phụ trách hiện hữu chỉ nhận thông báo để biết, không phải người duyệt.

  Cả ba tạo ra một bản ghi yêu cầu có vòng đời và cam kết thời gian theo `BR-35.3b`.

  **Lý do nghiệp vụ:** Người **duy nhất** nhìn thấy trùng lặp trong vận hành là nhân viên đang làm việc với khách, nhưng quyền gộp đòi quyền Xóa mà Nhân viên Kinh doanh không có. Nếu nhân viên nhận cảnh báo trùng mà không biết là ai và không có đường báo, cách lách phổ biến nhất là thêm dấu chấm vào email để lưu cho xong — và `KPI-01` không thể đạt với khối dữ liệu lịch sử tại `BR-17.2b`. Gộp tác động lên đồng thuận và giai đoạn của cả hai bản ghi nên vượt thẩm quyền của một người phụ trách.

- **`BR-17.4` (Định nghĩa đo Tỷ lệ trùng lặp):** Phục vụ `KPI-01`: tỷ lệ trùng lặp = **số bản ghi dư thừa** chia cho **tổng số bản ghi đang hoạt động** — cùng đơn vị là bản ghi, không phải cặp. Các bản ghi đang hoạt động (chưa xóa mềm) khớp nhau theo Tiêu chí chắc chắn được nhóm thành từng cụm; cụm gồm *n* bản ghi đóng góp *n − 1* bản ghi dư thừa. Loại khỏi phép đo: bản ghi mang nhãn **Định danh dùng chung** (`BR-30.2`) và **Hồ sơ Khách hàng Tạm** (`BR-01.1b`). Công cụ quét trùng lặp toàn không gian làm việc hiển thị kết quả theo đúng các cụm này.

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
| `AC-17.2c.4` | Đề nghị chuyển giao không được phản hồi | Quá hai lần thời hạn | Chỉ có leo thang; Người phụ trách không đổi |
| `AC-17.2c.5` | Đề nghị gộp không được phản hồi | Quá hai lần thời hạn | Chỉ có leo thang tới Quản trị Chất lượng Dữ liệu/Quản trị viên; hai bản ghi không bị gộp |
| `AC-17.3.1` | Bản ghi trùng thuộc phòng khác, ngoài phạm vi người tạo | Cảnh báo trùng xuất hiện | Thấy tên viết tắt, tên Người phụ trách, đơn vị phụ trách, thời điểm tương tác gần nhất và ba hành động; không thấy "không có quyền truy cập" |
| `AC-17.3.2` | Tiếp nối AC-17.3.1 | Bấm "Đề nghị gộp" | Yêu cầu được gửi tới Quản trị Chất lượng Dữ liệu hoặc Quản trị viên; Người phụ trách hiện hữu chỉ nhận thông báo |
| `AC-17.4.1` | Không gian làm việc có 1.000 bản ghi hoạt động thuộc diện đo, trong đó một cụm 3 bản ghi trùng email và một cụm 2 bản ghi trùng số điện thoại; ngoài ra có 2 bản ghi dùng chung một email đã gắn nhãn Định danh dùng chung (không thuộc diện đo) | Quản trị viên chạy công cụ quét trùng lặp | Kết quả hiển thị 2 cụm; tỷ lệ trùng lặp là 3/1.000 = 0,3% |

---

#### FEAT-18 — Xem trước Tác động Gộp Bản ghi

**Mô tả nghiệp vụ:** Trước khi gộp hai bản ghi, hệ thống hiển thị màn hình Xem trước nêu rõ giá trị nào được giữ, giá trị nào bị thay thế và số lượng bản ghi con (Cơ hội, Vé hỗ trợ, Công việc, Ghi chú) sẽ được chuyển sang Bản ghi Chính.

**Vai trò sử dụng chính:** Người có quyền Xóa trên khách hàng/doanh nghiệp, Quản trị viên, Quản trị Chất lượng Dữ liệu.

**Quy tắc nghiệp vụ:**

- **`BR-18.1` (So sánh hai cột):** Màn hình Xem trước hiển thị bảng so sánh hai cột của hai bản ghi và số lượng bản ghi con của từng bản ghi sẽ được chuyển giao.

- **`BR-18.2` (Quyền chọn trường và các trường bị cưỡng chế):** Người thực hiện chủ động chọn từng trường muốn giữ từ bản ghi nào — **trừ các trường dưới đây, hệ thống tự quyết định và khóa lựa chọn thủ công**, hiển thị rõ lý do ngay tại màn hình:

| Trường bị cưỡng chế | Quy tắc áp dụng | Quy tắc nguồn |
| --- | --- | --- |
| Trạng thái đồng thuận từng kênh | Trạng thái nghiêm ngặt nhất thắng: Từ chối nhận tin thắng Đồng ý nhận tin | `BR-19.6` |
| Trạng thái tiếp cận của cùng một địa chỉ | Thứ tự thắng: Không tiếp cận được > Không còn hiệu lực > Chưa kiểm tra > Đã xác thực. "Không tiếp cận được" thắng tất cả (trạng thái kỹ thuật, không đảo được). "Không còn hiệu lực" thắng "Đã xác thực" và "Chưa kiểm tra" vì nó ghi nhận một sự kiện nghiệp vụ đã xảy ra và vẫn đảo lại được thủ công (`BR-10.4` (a)) | `BR-19.6`, `BR-10.4` |
| Nhãn Định danh dùng chung | Có ở bất kỳ bản ghi nào thì được giữ | `BR-19.6` |
| Bằng chứng đồng thuận | Giữ đầy đủ của **cả hai** bản ghi | `BR-19.6` |
| Giai đoạn vòng đời | Giai đoạn tiến xa nhất thắng | `BR-19.8` |
| Nguồn gốc và tham số chiến dịch | Giữ của Bản ghi Chính; của bản ghi phụ được lưu vào sổ cái gộp | `BR-19.5` |
| Điểm tiềm năng | Lấy giá trị cao hơn, không cộng dồn | `BR-19.10` |
| Thẻ phân loại | Hợp nhất toàn bộ | `BR-19.10` |
| Vai trò liên hệ trên Cơ hội | Vai trò có thứ bậc ưu tiên cao nhất | `BR-19.4` |

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

**Quy tắc nghiệp vụ:**

- **`BR-19.1` (Quyền hạn bắt buộc):** Thao tác gộp yêu cầu **quyền Xóa**, vì bản ghi phụ bị xóa mềm sau khi gộp.

- **`BR-19.2` (Chuyển giao toàn bộ dữ liệu liên quan):** Toàn bộ ghi chú, công việc, vé hỗ trợ, cơ hội bán hàng, lịch sử hội thoại và mối quan hệ của bản ghi phụ được chuyển sang Bản ghi Chính.

- **`BR-19.3` (Ghi sổ cái gộp):** Mỗi lần gộp tạo một mục trong Sổ cái Hoàn tác Gộp, ghi: Bản ghi Chính, bản ghi phụ, **ảnh chụp dữ liệu gốc** của bản ghi phụ tại thời điểm gộp, người thực hiện và thời điểm.

- **`BR-19.4` (Xung đột vai trò liên hệ trên cùng Cơ hội):** Khi hai bản ghi cùng tham gia một Cơ hội với vai trò khác nhau, hệ thống giữ vai trò có thứ bậc ưu tiên cao nhất theo danh mục A.4: Người ra quyết định > Người ủng hộ nội bộ > Người thẩm định kỹ thuật > Người ảnh hưởng > Người thực hiện mua hàng.

- **`BR-19.5` (Bảo toàn nguồn gốc khi gộp):** Nguồn gốc và tham số chiến dịch (`BR-32.1`, `BR-32.2`) của Bản ghi Chính luôn được giữ, **không bị thay thế** bởi dữ liệu của bản ghi phụ. Nguồn gốc của bản ghi phụ được lưu đầy đủ trong ảnh chụp tại sổ cái gộp (`BR-19.3`) để đối chiếu báo cáo phân bổ doanh thu đa nguồn, dù không hiển thị trên hồ sơ Bản ghi Chính.

- **`BR-19.6` (Trạng thái nghiêm ngặt nhất cho đồng thuận) — sàn bắt buộc, nghĩa vụ pháp lý:** Khi hai bản ghi có trạng thái đồng thuận đối lập trên cùng một kênh, Bản ghi Chính sau khi gộp **bắt buộc nhận trạng thái Từ chối nhận tin**, bất kể bản ghi nào được chọn làm chính và bất kể người dùng chọn gì ở bước Xem trước. Quy tắc **không thể ghi đè thủ công**. Tương tự:
  - Trạng thái "Không tiếp cận được" (`FEAT-29`) luôn thắng "Đã xác thực" và "Chưa kiểm tra" cho cùng một địa chỉ.
  - Nhãn Định danh dùng chung (`BR-30.2`) có ở bất kỳ bản ghi nào thì được giữ.
  - Bằng chứng đồng thuận (`BR-30.3`) của **cả hai** bản ghi được giữ đầy đủ, không ghi đè.
  - Nếu **bất kỳ bản ghi nào trong cặp gộp** đang có yêu cầu quyền chủ thể dữ liệu chưa hoàn tất (`FEAT-33`), thao tác gộp **bị chặn** cho đến khi yêu cầu được xử lý xong.

  **Lý do nghiệp vụ:** Gửi tin cho người đã từ chối là vi phạm cam kết đồng thuận, và gộp là thao tác dễ vô tình "hồi sinh" một đồng ý cũ nhất. Bằng chứng của cả hai bản ghi là căn cứ chứng minh cơ sở xử lý dữ liệu khi bị khiếu nại. Gộp trong lúc đang xử lý yêu cầu chủ thể dữ liệu làm mất khả năng xác định yêu cầu áp dụng trên phần dữ liệu nào.

- **`BR-19.7` (Xung đột liên kết doanh nghiệp khi gộp):**
  - **Cùng một doanh nghiệp, chức danh khác nhau:** giữ **một** liên kết, ưu tiên chức danh của liên kết có ngày bắt đầu **muộn hơn** (phản ánh vị trí hiện tại); chức danh cũ lưu vào lịch sử liên kết.
  - **Hai Doanh nghiệp chính khác nhau:** Doanh nghiệp chính của Bản ghi Chính được giữ; liên kết của bản ghi phụ trở thành liên kết phụ. Người thực hiện chỉ định lại được tại bước Xem trước.
  - **Liên kết đã kết thúc (Đã nghỉ việc):** chuyển giao nguyên trạng, không tự kích hoạt lại.

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

**Quy tắc nghiệp vụ:**

- **`BR-20.1` (Điểm thao tác):** Người có quyền mở **Lịch sử gộp** trên hồ sơ Bản ghi Chính và bấm **"Hoàn tác gộp"** trên mục sổ cái tương ứng.

- **`BR-20.2` (Nội dung khôi phục):** Hệ thống khôi phục bản ghi phụ đã bị xóa mềm, trả lại các trường dữ liệu theo ảnh chụp trong sổ cái và trả các bản ghi con về đúng bản ghi sở hữu ban đầu.

- **`BR-20.3` (Thời hạn hoàn tác & bảo vệ khỏi dọn dẹp):** Hoàn tác gộp có hiệu lực trong **90 ngày** kể từ thời điểm gộp (Phụ lục B, `CFG-20-01`). Trong thời hạn này, bản ghi phụ **được loại khỏi dọn dẹp vĩnh viễn** của `BR-05.4` (xem `BR-05.6` (b)). Quá thời hạn, hành động "Hoàn tác gộp" không còn hiển thị và bản ghi phụ mới vào diện dọn dẹp; sổ cái gộp vẫn được lưu vĩnh viễn (`NFR-05`).

  **Lý do nghiệp vụ:** Hoàn tác gộp là cơ chế an toàn cốt lõi của phân hệ. Không có quy tắc này, một lần gộp sai phát hiện ở tháng thứ hai sẽ khôi phục về một bản ghi rỗng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-20.1.1` | Quản lý Kinh doanh có quyền Xóa | Mở Lịch sử gộp | Thấy lịch sử; không có hành động "Hoàn tác gộp" |
| `AC-20.2.1` | B đã được gộp vào A cách đây 45 ngày; 2 công việc của B đã chuyển sang A | Quản trị viên bấm "Hoàn tác gộp" | B trở lại hoạt động với dữ liệu theo ảnh chụp; 2 công việc trở về B |
| `AC-20.3.1` | Gộp cách đây 89 ngày | Mở Lịch sử gộp | Có hành động "Hoàn tác gộp" |
| `AC-20.3.2` | Gộp cách đây 91 ngày | Mở Lịch sử gộp | Không còn hành động "Hoàn tác gộp"; mục sổ cái vẫn xem được |

---

#### FEAT-21 — Khôi phục Giao dịch Gộp bị Gián đoạn

**Mô tả nghiệp vụ:** Công cụ xử lý sự cố khi một giao dịch gộp bị gián đoạn giữa chừng (mất kết nối, sự cố hệ thống) trước khi hoàn tất.

**Vai trò sử dụng chính:** Quản trị viên.

**Quy tắc nghiệp vụ:**

- **`BR-21.1` (Đưa về một trong hai trạng thái toàn vẹn):** Quản trị viên xử lý giao dịch gộp bị gián đoạn bằng một trong hai cách: **hoàn tất nốt** các bước chuyển giao bản ghi con còn dang dở, hoặc **đưa cả hai bản ghi về trạng thái trước khi gộp**. Kết quả cuối cùng luôn là một trong hai trạng thái toàn vẹn — đã gộp xong hoặc chưa gộp — đúng yêu cầu `NFR-04`; không bao giờ tồn tại trạng thái một phần bản ghi con đã chuyển, một phần chưa.

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

**Vai trò sử dụng chính:** Người có **quyền Nhập dữ liệu** trên khách hàng/doanh nghiệp, Quản trị viên.

**Quy tắc nghiệp vụ:**

- **`BR-22.1` (Xử lý nền):** Tệp tải lên được tiếp nhận và xử lý nền theo hàng đợi; người dùng không phải chờ trên màn hình, và tiến trình nhập không làm chậm các thao tác khác của người dùng trong không gian làm việc. Tệp gốc được lưu trong thời hạn tại `BR-33.8` rồi tự động xóa.

- **`BR-22.2` (Giới hạn dung lượng):** Dung lượng tối đa mỗi lần tải lên mặc định **50 MB** (Phụ lục B, `CFG-22-02`). Tệp vượt giới hạn bị từ chối ngay tại bước tải lên, kèm hướng dẫn chia nhỏ tệp.

  **Lý do nghiệp vụ:** Báo lỗi dung lượng sau khi người dùng đã chờ tải lên và ánh xạ cột là lãng phí thời gian; giới hạn phải được thể hiện trước khi họ bắt đầu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-22.1.1` | Người có quyền Nhập dữ liệu | Tải tệp 15 MB chứa 10.000 dòng, bắt đầu nhập, rồi chuyển sang màn hình khác | Tiến trình tiếp tục chạy nền; người dùng thao tác bình thường; theo dõi được tiến độ khi quay lại |
| `AC-22.2.1` | Giới hạn 50 MB | Chọn tệp 60 MB để tải lên | Bị từ chối ngay khi chọn tệp, kèm hướng dẫn chia nhỏ; không chuyển sang bước ánh xạ cột |
| `AC-22.2.2` | Màn hình tải tệp | Quan sát trước khi chọn tệp | Giới hạn dung lượng hiện hành được hiển thị |
| `AC-22.2.3` | Người dùng không có quyền Nhập dữ liệu | Tìm chức năng nhập khẩu | Không khả dụng |

---

#### FEAT-23 — Trợ lý Tự động Ánh xạ Cột Dữ liệu

**Mô tả nghiệp vụ:** Trợ lý trực quan tự động đọc tiêu đề cột trong tệp và đề xuất ánh xạ với các trường tương ứng (Họ tên, Số điện thoại, Email, Tên công ty, Chức danh, Địa chỉ).

**Vai trò sử dụng chính:** Người dùng thực hiện nhập dữ liệu.

**Quy tắc nghiệp vụ:**

- **`BR-23.1` (Nhận diện tiêu đề cột):** Tự động nhận diện tiêu đề cột phổ biến bằng tiếng Việt, tiếng Ả Rập và tiếng Anh (ví dụ "Số điện thoại", "Phone", "Mobile", "Email", "Họ tên", "Full Name").

- **`BR-23.2` (Chỉnh ánh xạ):** Người dùng sửa được ánh xạ thủ công hoặc bỏ qua các cột không cần nhập. Cột không ánh xạ được trường nào thuộc nhóm Định danh KYC khi nhóm này đang tắt (`BR-01.5b`).

- **`BR-23.3` (Chiến lược xử lý trùng lặp cho từng lô):** Người dùng chọn cho mỗi lô: **Bỏ qua bản ghi trùng** (mặc định) hoặc **Cập nhật dữ liệu mới vào bản ghi cũ**. Lựa chọn **"Tạo bản ghi mới dù trùng"** chỉ khả dụng khi người thực hiện xác nhận lô dữ liệu thuộc trường hợp Định danh dùng chung (lối ra tại `BR-17.2`) — khi đó các bản ghi tạo ra tự động mang nhãn Định danh dùng chung và lượt xác nhận được ghi nhật ký. Chiến lược cập nhật không thay đổi được nguồn gốc (`BR-32.3`), không nâng được mức đồng thuận (`BR-30.10`) và không tạo được bước chuyển giai đoạn ngoài ma trận (`BR-12.6`).

  **Lý do nghiệp vụ:** Kênh nhập khẩu không được trở thành đường đi vòng qua chính sách chặn trùng, cam kết đồng thuận và ma trận vòng đời.

- **`BR-23.4` (Trường tra cứu bắt buộc khi cập nhật):** Khi chọn chiến lược cập nhật, người dùng **bắt buộc** chọn trường dùng để tìm bản ghi cũ: **(a)** mã khách hàng nội bộ (chính xác nhất); **(b)** địa chỉ email; **(c)** số điện thoại. Dòng không khớp bản ghi nào được xử lý như tạo mới và được đánh dấu cảnh báo trong báo cáo kết quả.

  **Lý do nghiệp vụ:** Cập nhật mà không nói rõ tìm bản ghi cũ theo gì sẽ ghi đè nhầm dữ liệu của người khác khi tên trùng nhau.

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

---

#### FEAT-24 — Xử lý Nhập khẩu theo Hàng đợi & Báo cáo Lỗi Chi tiết

**Mô tả nghiệp vụ:** Tiến trình nền xử lý tệp theo từng phần, kiểm tra tính hợp lệ từng dòng, nhập các dòng hợp lệ và xuất báo cáo chi tiết các dòng lỗi để người dùng sửa.

**Vai trò sử dụng chính:** Tiến trình Hệ thống, người dùng thực hiện nhập dữ liệu.

**Quy tắc nghiệp vụ:**

- **`BR-24.1` (Theo dõi tiến độ):** Người dùng theo dõi tiến độ gần như tức thời: số dòng đã xử lý, số dòng thành công, số dòng lỗi.

- **`BR-24.2` (Tệp báo cáo lỗi):** Nếu có dòng lỗi, hệ thống tạo tệp báo cáo lỗi, mỗi dòng lỗi kèm nguyên nhân cụ thể, và cấp đường tải về có hiệu lực **24 giờ** (thống nhất `BR-25.2`). Các nguyên nhân lỗi gồm: sai định dạng email; số điện thoại không chuẩn hóa được; **thiếu cả email lẫn số điện thoại** (vi phạm `BR-01.1` — thiếu họ tên **không** phải lỗi); bước chuyển giai đoạn không hợp lệ (`BR-12.6`); thiếu trường tra cứu khi chọn chiến lược cập nhật (`BR-23.4`); trùng lặp bị chặn theo chính sách (`BR-17.2`).

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

**Vai trò sử dụng chính:** Người có **quyền Xuất dữ liệu**, Nhân viên Kinh doanh (trong giới hạn `BR-25.5`), Quản trị viên.

**Quy tắc nghiệp vụ:**

- **`BR-25.1` (Xuất nền, đúng mức hiển thị):** Việc xuất chạy nền theo hàng đợi, hỗ trợ hàng chục nghìn bản ghi mà không làm chậm hệ thống; số bản ghi tối đa mỗi lần xuất theo gói dịch vụ (`NFR-11`). Giá trị trong tệp xuất tuân theo **đúng mức hiển thị của người xuất** tại `BR-04.3` (`NFR-06`).

- **`BR-25.2` (Bảo vệ đường tải về):** Đường tải về có hiệu lực **24 giờ**, **chỉ dùng được một lần**, và tên tệp được chuẩn hóa an toàn. Cùng thời hạn áp dụng cho đường tải tệp báo cáo lỗi nhập khẩu (`BR-24.2`).

- **`BR-25.3` (Nhật ký xuất dữ liệu):** Mỗi lần xuất bắt buộc ghi: người xuất, thời điểm, bộ lọc đã dùng, **danh sách trường được xuất** và **danh sách bản ghi được xuất**. Nhật ký xuất chịu cùng chế độ kiểm soát truy cập như nhật ký kiểm toán (`NFR-14`).

  **Lý do nghiệp vụ:** Đây là căn cứ chính để trả lời câu hỏi "dữ liệu của khách hàng đã ra ngoài những đâu" khi thực thi quyền chủ thể dữ liệu (`BR-33.8`) và khi có nghi vấn lấy dữ liệu hàng loạt.

- **`BR-25.4` (Ngưỡng xuất lớn cần phê duyệt):** Một lần xuất bắt buộc được phê duyệt **trước khi tệp được tạo** khi thỏa bất kỳ điều kiện nào dưới đây:
  - **Với các vai trò có quyền Xuất dữ liệu đầy đủ** (Quản lý Kinh doanh, Nhân viên và Quản lý Marketing, Quản trị viên, Chủ sở hữu): lần xuất **vượt 5.000 bản ghi** (Phụ lục B, `CFG-25-01`).
  - **Với Nhân viên Kinh doanh:** lần xuất làm vượt **giá trị nhỏ hơn** giữa ngưỡng xuất lớn (`CFG-25-01`) và hạn mức ngày (`CFG-25-02`) — cần Quản lý Kinh doanh phê duyệt từng lần, và một lần phê duyệt **không được nâng tổng lượng xuất trong ngày lên quá hai lần hạn mức ngày**.
  - **Trường thuộc nhóm Định danh KYC — giới hạn theo mục đích, không theo hạn mức:** chỉ **Quản trị viên và Chủ sở hữu** xuất được, mỗi lần bắt buộc có **Người phụ trách Bảo vệ Dữ liệu đồng phê duyệt** (hoặc người thứ hai theo quy tắc thay thế tại `NFR-14`) kèm khai báo mục đích đúng với mục đích đã đăng ký tại `BR-01.5b`. **Nhân viên Kinh doanh, Quản lý Kinh doanh, Nhân viên Marketing và Quản lý Marketing không xuất được nhóm trường này trong bất kỳ trường hợp nào.**

  **Người phê duyệt là quản lý trực tiếp theo tuyến báo cáo của người xuất:** Quản lý Kinh doanh phê duyệt cho Nhân viên Kinh doanh; Quản lý Marketing phê duyệt cho Nhân viên Marketing; Chủ sở hữu phê duyệt cho Quản trị viên và các vai trò quản lý; với chính Chủ sở hữu, người phê duyệt là Người phụ trách Bảo vệ Dữ liệu (hoặc người thứ hai theo `NFR-14`). **Không vai trò nào tự phê duyệt lần xuất của chính mình.**

  **Lý do nghiệp vụ:** Xuất là thao tác đưa dữ liệu cá nhân ra khỏi hệ thống; một lần xuất chạm ngưỡng luôn phải có hai người khác nhau đứng tên. Phê duyệt theo tuyến báo cáo vì buộc đội kinh doanh xin phê duyệt của Marketing là sai tuyến và sẽ bị bỏ qua trong thực tế. Nhóm KYC bị khóa theo mục đích: mở đường phê duyệt cho Marketing sẽ vô hiệu chính giới hạn mục đích tại `BR-01.5b` đúng ở điểm dữ liệu rời khỏi hệ thống.

- **`BR-25.5` (Quyền xuất dữ liệu của Nhân viên Kinh doanh):** Nhân viên Kinh doanh được xuất dữ liệu **trong phạm vi dữ liệu của mình**, với ba ràng buộc: **(a)** hạn mức mặc định **2.000 bản ghi/người/ngày** (Phụ lục B, `CFG-25-02`), chỉ vượt được khi có phê duyệt theo `BR-25.4`; **(b)** **không** xuất được trường thuộc nhóm Định danh KYC; **(c)** mọi lần xuất ghi nhật ký theo `BR-25.3`.

  **Lý do nghiệp vụ:** Nhu cầu hằng ngày (in danh sách khách để đi gặp, chuẩn bị danh sách mời hội thảo) nếu không có đường hợp lệ thì cách làm thật sẽ là sao chép màn hình vào bảng tính hoặc chụp màn hình — hoàn toàn không có nhật ký, phá vỡ `BR-25.3`.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-25.1.1` | Quản trị viên xuất 30.000 bản ghi | Bấm xuất | Tệp được tạo nền; người dùng nhận thông báo khi sẵn sàng |
| `AC-25.1.2` | Người xuất ở cột (B) của `BR-04.3` | Mở tệp xuất | Số điện thoại, email ở mức che một phần như trên màn hình |
| `AC-25.2.1` | Tệp xuất sẵn sàng | Tải về lần thứ nhất, rồi dùng lại cùng đường tải | Lần thứ nhất thành công; lần thứ hai bị từ chối |
| `AC-25.2.2` | Tệp xuất sẵn sàng lúc 10:00, chưa tải | Mở đường tải lúc 10:05 hôm sau | Bị từ chối vì hết hiệu lực |
| `AC-25.3.1` | Nhân viên Marketing xuất 800 bản ghi với 5 trường | Người có quyền đọc nhật ký toàn phần mở nhật ký xuất | Thấy người xuất, thời điểm, bộ lọc, đủ 5 trường và 800 bản ghi |
| `AC-25.4.1` | Quản lý Kinh doanh xuất 6.000 bản ghi | Bấm xuất | Tệp chưa được tạo; yêu cầu phê duyệt gửi Chủ sở hữu |
| `AC-25.4.2` | Nhân viên Marketing xuất 6.000 bản ghi | Bấm xuất | Yêu cầu phê duyệt gửi Quản lý Marketing |
| `AC-25.4.3` | Chủ sở hữu xuất 6.000 bản ghi | Bấm xuất | Yêu cầu phê duyệt gửi Người phụ trách Bảo vệ Dữ liệu; Chủ sở hữu không tự phê duyệt được |
| `AC-25.4.4` | Quản trị viên chọn trường Số Căn cước công dân khi xuất | Bấm xuất, không khai báo mục đích | Không tạo được yêu cầu; bắt buộc khai báo mục đích và cần Người phụ trách Bảo vệ Dữ liệu đồng phê duyệt |
| `AC-25.4.5` | Quản lý Marketing mở danh sách trường xuất | Tìm trường thuộc nhóm KYC | Không có trường KYC nào để chọn |
| `AC-25.5.1` | Nhân viên Kinh doanh A đã xuất 1.500 bản ghi hôm nay, hạn mức 2.000 | Xuất thêm 600 bản ghi | Lần xuất bị giữ lại và chuyển thành yêu cầu phê duyệt gửi Quản lý Kinh doanh (không gửi Quản lý Marketing) |
| `AC-25.5.2` | Tiếp nối AC-25.5.1, Quản lý đã phê duyệt | A xuất tiếp tới tổng 4.001 bản ghi trong ngày | Phần vượt 4.000 bản ghi (hai lần hạn mức) không xuất được |
| `AC-25.5.3` | A mở danh sách trường xuất | Tìm trường Số Căn cước công dân | Không có trong danh sách; không có đường phê duyệt nào mở được |

---

#### FEAT-26 — Danh sách Hiển thị Dùng chung

**Mô tả nghiệp vụ:** Hiển thị danh bạ theo các bộ lọc lưu sẵn, ví dụ "Khách hàng của tôi", "Khách hàng tiềm năng mới trong tuần", "Khách hàng VIP", "Khách hàng chưa có hoạt động trong 30 ngày", và chia sẻ bộ lọc cho đồng nghiệp.

**Vai trò sử dụng chính:** Mọi người dùng trong không gian làm việc.

**Quy tắc nghiệp vụ:**

- **`BR-26.1` (Nội dung một danh sách):** Một danh sách lưu cấu hình cột hiển thị, điều kiện lọc kết hợp (**và** / **hoặc**) và thứ tự sắp xếp.

- **`BR-26.2` (Chia sẻ cấu hình, không chia sẻ dữ liệu):** Danh sách dùng chung chỉ chia sẻ **cấu hình bộ lọc**; mỗi người mở danh sách chỉ thấy các bản ghi thuộc phạm vi dữ liệu của chính mình, với mức che theo `BR-04.3`.

  **Lý do nghiệp vụ:** Nếu chia sẻ danh sách đồng nghĩa với chia sẻ dữ liệu, một thao tác chia sẻ bộ lọc sẽ vượt qua toàn bộ mô hình phạm vi dữ liệu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-26.1.1` | Người dùng lọc "Giai đoạn là MQL **và** (tỉnh/thành là Hà Nội **hoặc** Đà Nẵng)", chọn 5 cột, sắp xếp theo điểm giảm dần | Lưu thành danh sách, mở lại hôm sau | Danh sách giữ đúng điều kiện, cột và thứ tự sắp xếp |
| `AC-26.2.1` | Quản lý Kinh doanh chia sẻ danh sách "Khách VIP phòng 1" cho Nhân viên A có phạm vi "Chỉ của mình" | A mở danh sách | A chỉ thấy các khách VIP do A phụ trách hoặc được chia sẻ cho A |

---

### Nhóm I — Dòng thời gian 360 độ & Ngữ cảnh Khách hàng

#### FEAT-27 — Dòng thời gian Hoạt động Hợp nhất 360 độ

**Mô tả nghiệp vụ:** Một luồng duy nhất tập hợp mọi sự kiện liên quan đến khách hàng, theo thứ tự thời gian mới nhất trước.

**Vai trò sử dụng chính:** Mọi người dùng có quyền xem khách hàng.

**Quy tắc nghiệp vụ:**

- **`BR-27.1` (Các nguồn sự kiện hợp nhất):**
  - **Ghi chú:** trao đổi nội bộ của nhân viên (`FEAT-36`).
  - **Vé hỗ trợ:** yêu cầu hỗ trợ và khiếu nại của khách.
  - **Cơ hội bán hàng:** cơ hội đang mở và lịch sử chuyển giai đoạn bán hàng.
  - **Công việc & lịch hẹn:** cuộc gọi, cuộc họp, việc cần làm.
  - **Hội thoại đa kênh:** các phiên trò chuyện qua trò chuyện trực tuyến và các ứng dụng nhắn tin đã tích hợp.
  - **Thay đổi trạng thái:** đổi giai đoạn vòng đời, gộp bản ghi, thay đổi Người phụ trách.

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

**Quy tắc nghiệp vụ:**

- **`BR-28.1` (Nội dung khung ngữ cảnh):** Hiển thị đồng thời: thông tin liên hệ, doanh nghiệp trực thuộc, giai đoạn vòng đời, **3 Cơ hội bán hàng gần nhất**, **3 Vé hỗ trợ gần nhất** và các ghi chú ghim. Mức hiển thị của thông tin liên hệ áp đúng `FEAT-04` theo quan hệ của người xem với bản ghi — với Nhân viên Hỗ trợ đang xử lý vé/hội thoại là **cột (C)**; khung ngữ cảnh **không** có chính sách che riêng. Ghi chú ghim được lọc theo phạm vi đọc của người xem (`BR-36.7`).

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

**Quy tắc nghiệp vụ:**

- **`BR-29.1` (Một kênh chính mỗi loại):** Mỗi loại kênh có tối đa **một** kênh chính. Đặt một kênh khác làm chính thì kênh cũ tự động mất trạng thái chính.

- **`BR-29.2` (Bốn trạng thái tiếp cận):** Theo danh mục A.10: ba trạng thái kỹ thuật — **Đã xác thực** (đã gửi nhận thành công), **Không tiếp cận được** (email hỏng hoặc số không tồn tại), **Chưa kiểm tra** — và một trạng thái nghiệp vụ **Không còn hiệu lực** do `BR-10.4` sinh ra khi khách rời doanh nghiệp sở hữu địa chỉ. Thứ tự thắng khi gộp quy định tại `BR-18.2`.

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

**Quy tắc nghiệp vụ:**

- **`BR-30.1` (Đồng thuận theo từng kênh):** Trạng thái **Đồng ý nhận tin** / **Từ chối nhận tin** được lưu **độc lập cho từng kênh** (email, tin nhắn SMS, WhatsApp, Zalo và các kênh tích hợp khác). Khách từ chối trên một kênh chỉ tác động lên kênh đó, trừ khi khách chọn **"Hủy nhận tin trên toàn bộ mọi kênh"**. Phạm vi tác động của Từ chối nhận tin quy định tại `BR-30.5` — chặn nhóm thư Tiếp thị, không chặn mọi loại thư.

- **`BR-30.2` (Định danh dùng chung):** Một số điện thoại hoặc email được đánh dấu **Định danh dùng chung** (ví dụ số tổng đài, số lễ tân, email gia đình) để hệ thống không coi các khách hàng khác nhau dùng chung định danh đó là trùng lặp và không gợi ý gộp họ.

- **`BR-30.3` (Bằng chứng đồng thuận):** Mỗi lần trạng thái đồng thuận thay đổi, hệ thống bắt buộc lưu bộ bằng chứng **không sửa được** gồm: **(a)** thời điểm ghi nhận; **(b)** nguồn thu thập — **bắt buộc chọn từ A.8**, không nhập tự do; **(c)** nội dung điều khoản khách đã đồng ý (phiên bản văn bản đồng thuận tại thời điểm đó); **(d)** người hoặc tiến trình ghi nhận. Bằng chứng được lưu **vĩnh viễn** kể cả khi khách đổi trạng thái nhiều lần, chỉ chịu ngoại lệ khử định danh tại `BR-33.8`.

  **Lý do nghiệp vụ:** Khi bị khiếu nại, doanh nghiệp phải chứng minh được cơ sở xử lý dữ liệu tại đúng thời điểm gửi tin; bằng chứng sửa được hoặc chỉ lưu trạng thái cuối cùng thì không chứng minh được gì.

- **`BR-30.4` (Đồng thuận qua nhập khẩu hàng loạt):** Dữ liệu nhập khẩu **không được** mặc định nhận trạng thái Đồng ý nhận tin. Người nhập khẩu **bắt buộc chọn** Cơ sở đồng thuận cho lô từ A.11 — giao diện không cho bỏ trống. Danh mục có sẵn giá trị **"Không có cơ sở đồng thuận"** để khai báo trung thực; khi chọn giá trị này (hoặc cơ sở khai báo không đủ chứng minh đồng thuận tiếp thị), toàn bộ lô nhận Từ chối nhận tin cho nhóm Tiếp thị và chỉ được liên hệ theo nhóm Giao dịch & Dịch vụ (`BR-30.5`).

  **Lý do nghiệp vụ:** Danh bạ mua về hoặc thu thập không rõ nguồn là nguồn vi phạm đồng thuận phổ biến nhất; mặc định Đồng ý sẽ biến mỗi lần nhập khẩu thành một lần vi phạm hàng loạt.

- **`BR-30.5` (Phân loại mục đích gửi tin — phạm vi chi phối của Từ chối nhận tin) — sàn bắt buộc:** Mọi thư/tin nhắn gửi ra từ hệ thống bắt buộc thuộc **một** trong ba nhóm mục đích (A.12). Mọi phân hệ và kênh gửi tin phải khai báo nhóm mục đích cho từng lượt gửi. Trạng thái Từ chối nhận tin (kể cả "Hủy nhận tin trên toàn bộ mọi kênh") **chỉ chi phối nhóm Tiếp thị**:

| Nhóm mục đích | Nội dung thuộc nhóm | Chịu chi phối của Từ chối nhận tin? |
| --- | --- | :---: |
| **Tiếp thị & Quảng bá** | Bản tin định kỳ, chiến dịch khuyến mãi, thư nuôi dưỡng tự động, mời sự kiện thương mại, thư tái tiếp cận | **Có — chặn tuyệt đối** |
| **Giao dịch & Dịch vụ** | Phản hồi vé hỗ trợ, xác nhận đơn hàng, hóa đơn/nhắc thanh toán, thông báo bảo trì, cảnh báo bảo mật, thông báo pháp lý bắt buộc, **thư báo giá, thư hợp đồng, thư xác nhận cuộc hẹn, thư trả lời một yêu cầu do chính khách hàng đưa ra** | Không |
| **Liên lạc 1-1 do nhân viên chủ động** | Email/tin nhắn nhân viên gửi trực tiếp trong quá trình phục vụ khách, thỏa **đồng thời cả bốn tiêu chí** tại `BR-30.7` | Không (mặc định), nhưng **bắt buộc ghi nhật ký** |

  Tenant **không được** cấu hình để nhóm Tiếp thị thoát khỏi chi phối của Từ chối nhận tin (sàn pháp lý); tenant **được** cấu hình nhóm Liên lạc 1-1 có chịu chi phối hay không (Phụ lục B, `CFG-30-01`).

  **Lý do nghiệp vụ:** Nếu Từ chối nhận tin chặn tất cả, khách bấm "hủy nhận bản tin" hôm nay rồi mai gửi vé hỗ trợ sẽ không được trả lời — sự cố phục vụ khách xảy ra ngay tuần đầu; khách đang thương lượng hợp đồng không nhận được báo giá, và nhân viên sẽ gửi từ hộp thư cá nhân, đưa nội dung thương lượng ra khỏi hệ thống. Ngược lại, nếu không phân loại rõ, hệ thống sẽ gửi thư tiếp thị cho người đã từ chối.

- **`BR-30.6` (Trạng thái Hạn chế xử lý):** Khi khách yêu cầu hạn chế xử lý (`FEAT-33`), hồ sơ mang trạng thái **Hạn chế xử lý**:
  - **Dừng:** toàn bộ nhóm thư Tiếp thị; chấm điểm và suy giảm điểm (`FEAT-15`, `FEAT-16`); thăng hạng vòng đời **do ngưỡng điểm** (`BR-15.5`); thu hồi và phân bổ lại tự động (`BR-31.7`); **đồng hồ cam kết phản hồi lần đầu** (`BR-31.7`) — bản ghi vì vậy bị loại khỏi mẫu đo `KPI-03`; đưa vào danh sách phân khúc chiến dịch.
  - **Vẫn chạy:** nhóm thư Giao dịch & Dịch vụ, để doanh nghiệp thực hiện nghĩa vụ hợp đồng; và **các bước chuyển giai đoạn theo nguyên tắc 1** (`BR-12.2`, `BR-12.9`).
  - **Dỡ trạng thái:** khi **chính chủ thể dữ liệu rút lại yêu cầu qua kênh đã xác minh** theo `BR-33.7` — do **Quản trị viên** thực hiện một mình; hoặc theo nhánh dỡ sớm biện pháp phòng ngừa tại `BR-33.7` (b) — cần **Quản trị viên cùng Người phụ trách Bảo vệ Dữ liệu**. Cả hai nhánh ghi nhật ký (`NFR-07`) và làm đồng hồ cam kết chạy lại từ thời điểm dỡ.

  **Lý do nghiệp vụ:** Hệ thống không được thúc nhân viên liên hệ một người vừa yêu cầu hạn chế xử lý. Nguyên tắc 1 chỉ ghi nhận một thực tế thương mại đã xảy ra, không phải hoạt động xử lý dữ liệu cho mục đích tiếp thị — nếu dừng cả nhóm này, hồ sơ của một khách đang ký hợp đồng sẽ đứng sai giai đoạn. Thẩm quyền dỡ khác nhau theo nhánh vì nhánh thứ nhất làm đúng ý nguyện vừa được xác minh của chủ thể, còn nhánh thứ hai dỡ một biện pháp bảo vệ mà chủ thể chưa xác minh được danh tính. Không có đường dỡ thì một khách đổi ý bị đóng băng vĩnh viễn.

- **`BR-30.7` (Tiêu chí quan sát được của nhóm Liên lạc 1-1) — sàn bắt buộc:** Một lượt gửi chỉ thuộc nhóm Liên lạc 1-1 khi thỏa **đồng thời cả bốn** tiêu chí:
  - **(a)** do một người dùng thật thực hiện, không do tiến trình tự động hay lịch gửi;
  - **(b)** số người nhận **tối đa 5** trong một lượt gửi (Phụ lục B, `CFG-30-02`);
  - **(c)** **không** dùng **mẫu chiến dịch** do Marketing tạo trong công cụ chiến dịch — mẫu thư nghiệp vụ cá nhân do nhân viên hoặc đội kinh doanh soạn (thư theo dõi sau cuộc gọi, thư giới thiệu, thư hỏi lịch gặp) **vẫn được phép**;
  - **(d)** **không gửi theo lô cho toàn bộ một danh sách** — việc mở một danh sách hiển thị rồi chọn thủ công vài khách hàng cụ thể **vẫn thỏa** tiêu chí này.

  Lượt gửi không thỏa đủ bốn tiêu chí **bắt buộc thuộc nhóm Tiếp thị**. Thư báo giá, thư hợp đồng, thư xác nhận cuộc hẹn và thư trả lời yêu cầu của khách thuộc nhóm Giao dịch & Dịch vụ (`BR-30.5`), không xét theo bốn tiêu chí này và không chịu trần số người nhận.

  **Lý do nghiệp vụ:** Không có bộ tiêu chí quan sát được, một chiến dịch tiếp thị chỉ cần gửi từ tài khoản nhân viên là ra khỏi tầm chi phối của Từ chối nhận tin, biến cam kết "không thể ghi đè" tại `BR-19.6` thành hình thức.

- **`BR-30.8` (Giám sát nhóm Liên lạc 1-1):** Hằng tháng, hệ thống báo cáo cho Người phụ trách Bảo vệ Dữ liệu và Chủ sở hữu khối lượng thư nhóm Liên lạc 1-1 đã gửi tới các khách đang Từ chối nhận tin, chia theo người gửi; khối lượng vượt mức bất thường được cảnh báo để rà soát dấu hiệu lách quy tắc.

- **`BR-30.9` (Mặc định an toàn khi thiếu khai báo nhóm) — sàn bắt buộc:** Mọi lượt gửi **không khai báo nhóm mục đích** được mặc định xếp vào **nhóm Tiếp thị**.

  **Lý do nghiệp vụ:** Đây là mặc định an toàn nhất về pháp lý, để cam kết tại `BR-19.6` có cơ chế cưỡng chế thật mà không phụ thuộc việc mọi kênh gửi tin đã khai báo nhóm đầy đủ hay chưa.

- **`BR-30.10` (Cưỡng chế đồng thuận trên mọi nguồn tác động) — sàn bắt buộc:** Bảng trường bị cưỡng chế tại `BR-18.2` chỉ điều chỉnh thao tác gộp; quy tắc này mở rộng cơ chế cưỡng chế ra mọi nguồn tác động còn lại:
  - **Nguyên tắc bất biến:** chỉ **hành vi của chính chủ thể dữ liệu** (tự đăng ký, tự bấm liên kết xác nhận, tự trả lời trên kênh của mình) hoặc **bằng chứng đồng thuận mới hợp lệ theo `BR-30.3`** mới **nâng** được từ Từ chối nhận tin lên Đồng ý nhận tin.
  - **Nhập khẩu theo chiến lược cập nhật (`BR-23.3`):** **không bao giờ** hạ mức nghiêm ngặt của bản ghi hiện hữu. Bản ghi đang Từ chối mà dòng nhập vào là Đồng ý thì giữ Từ chối, và dòng đó được ghi vào báo cáo kết quả với ghi chú "Không nâng được mức đồng thuận — thiếu bằng chứng của chủ thể".
  - **Chỉnh sửa thủ công bởi bất kỳ vai trò nào có quyền ghi trên bản ghi** — Nhân viên và Quản lý Kinh doanh, Nhân viên và Quản lý Marketing, Nhân viên Hỗ trợ khi tiếp nhận yêu cầu của khách: được **hạ** xuống Từ chối nhận tin tự do; **nâng** lên Đồng ý nhận tin bắt buộc kèm bằng chứng theo `BR-30.3` với nguồn thu thập từ A.8 — không có bằng chứng thì giao diện không cho lưu.
  - Mọi lượt nâng mức đồng thuận, từ bất kỳ nguồn nào, đều được ghi nhật ký (`NFR-07`).

  **Lý do nghiệp vụ:** Không có quy tắc này, cam kết "Từ chối nhận tin không thể bị ghi đè" chỉ đúng với thao tác gộp, trong khi hai đường vào phổ biến hơn — nhập khẩu và sửa tay — vẫn hở.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-30.1.1` | Chị Mai Đồng ý nhận tin qua email, SMS và Zalo | Chị bấm liên kết "Hủy nhận tin" trong một email chiến dịch | Chỉ kênh email chuyển sang Từ chối nhận tin; SMS và Zalo vẫn Đồng ý |
| `AC-30.1.2` | Cùng bối cảnh | Chị chọn "Hủy nhận tin trên toàn bộ mọi kênh" | Cả ba kênh chuyển sang Từ chối nhận tin |
| `AC-30.2.1` | Hai khách hàng dùng chung số tổng đài đã được đánh dấu Định danh dùng chung | Quản trị viên chạy công cụ quét trùng lặp | Hai khách không bị xếp vào cụm trùng |
| `AC-30.3.1` | Tiếp nối AC-30.1.1 | Mở lịch sử đồng thuận của chị Mai | Có bằng chứng gồm thời điểm, nguồn "Liên kết Hủy nhận tin trong email", phiên bản điều khoản và tiến trình ghi nhận; không có thao tác sửa bằng chứng |
| `AC-30.3.2` | Chị Mai đổi trạng thái email ba lần trong năm | Mở lịch sử đồng thuận | Thấy đủ ba bộ bằng chứng |
| `AC-30.4.1` | Màn hình nhập khẩu | Bỏ trống Cơ sở đồng thuận, bấm bắt đầu | Không bắt đầu được; yêu cầu chọn từ A.11 |
| `AC-30.4.2` | Chọn "Không có cơ sở đồng thuận" | Chạy nhập | Toàn bộ lô Từ chối nhận tin cho nhóm Tiếp thị; báo cáo kết quả nêu rõ cảnh báo này |
| `AC-30.5.1` | Chị Mai đang Từ chối nhận tin qua email | Chị gửi vé hỗ trợ và nhân viên phản hồi qua email | Thư phản hồi được gửi tới chị |
| `AC-30.5.2` | Cùng bối cảnh | Một bản tin định kỳ được gửi | Chị không nhận được |
| `AC-30.5.3` | Cùng bối cảnh | Nhân viên Kinh doanh gửi thư báo giá cho chị | Thư được gửi; không bị trần 5 người nhận |
| `AC-30.5.4` | Chủ sở hữu mở cấu hình mục đích gửi tin | Tìm lựa chọn cho nhóm Tiếp thị không chịu chi phối của Từ chối nhận tin | Không tồn tại lựa chọn này |
| `AC-30.6.1` | Khách đang ở Lead, được gắn Hạn chế xử lý | Khách mở email và nhấp liên kết; quan sát điểm, phân khúc, hạn phản hồi | Điểm không thay đổi; khách không thăng hạng; không xuất hiện trong danh sách phân khúc chiến dịch; không có hạn phản hồi đang chạy; bản ghi không thuộc mẫu đo `KPI-03` |
| `AC-30.6.2` | Tiếp nối AC-30.6.1 | Nhân viên gửi xác nhận đơn hàng cho khách | Thư được gửi |
| `AC-30.6.3` | Tiếp nối AC-30.6.1 | Một Cơ hội của khách được đóng Thắng | Giai đoạn vẫn lên Customer |
| `AC-30.6.4` | Tiếp nối AC-30.6.1 | Khách rút lại yêu cầu qua kênh đã xác minh; Quản trị viên dỡ trạng thái | Trạng thái được dỡ; nhật ký ghi nhận; đồng hồ cam kết chạy lại từ thời điểm dỡ |
| `AC-30.6.5` | Hạn chế xử lý đang áp do biện pháp phòng ngừa (chưa xác minh được danh tính) | Quản trị viên tìm cách dỡ một mình | Không dỡ được khi chưa có Người phụ trách Bảo vệ Dữ liệu cùng phê duyệt |
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
| `AC-30.10.3` | Cùng bối cảnh | Nhân viên sửa lên Đồng ý nhận tin kèm bằng chứng, nguồn "Ghi nhận thủ công bởi nhân viên", phiên bản điều khoản | Lưu được; nhật ký ghi lượt nâng mức |
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

**Quy tắc nghiệp vụ:**

- **`BR-33.1` (Tiếp nhận & theo dõi):** Mỗi yêu cầu là một bản ghi theo dõi riêng, gắn với khách hàng liên quan, ghi: loại yêu cầu, ngày tiếp nhận, hạn xử lý, người xử lý, trạng thái (Đã tiếp nhận / Đang xử lý / Đã hoàn tất / Bị từ chối kèm lý do). Hệ thống cảnh báo khi yêu cầu sắp đến hạn. Chỉ Quản trị viên và Chủ sở hữu đóng được yêu cầu và phát hành phản hồi chính thức cho khách.

- **`BR-33.2` (Xóa vĩnh viễn có kiểm soát):** Chỉ Quản trị viên hoặc Chủ sở hữu thực hiện, bắt buộc xác nhận hai bước và **không qua Thùng rác**. Sau khi hoàn tất, hệ thống chỉ giữ một bản ghi tối thiểu chứng minh đã thực hiện nghĩa vụ (mã bản ghi đã xóa, loại yêu cầu, thời điểm hoàn tất, người thực hiện) — không chứa dữ liệu cá nhân.

- **`BR-33.3` (Ngoại lệ nghĩa vụ lưu trữ):** Khách còn nghĩa vụ hợp đồng, hóa đơn hoặc tranh chấp pháp lý đang xử lý thì yêu cầu xóa bị **từ chối một phần** theo cơ sở "nghĩa vụ pháp lý phải lưu trữ": hệ thống xóa dữ liệu tiếp thị và dữ liệu liên hệ không cần thiết, giữ dữ liệu tối thiểu phục vụ nghĩa vụ pháp lý, và bắt buộc ghi rõ lý do từ chối một phần trong bản ghi theo dõi để phản hồi khách.

- **`BR-33.4` (Chặn xung đột với gộp):** Bản ghi đang có yêu cầu chủ thể dữ liệu chưa hoàn tất không được gộp (thống nhất `BR-19.6`).

- **`BR-33.5` (Chính sách lưu trữ dữ liệu không hoạt động):** Khách hàng không phát sinh tương tác nào trong **36 tháng** liên tục và không ở Customer/Evangelist được đưa vào danh sách đề xuất rà soát lưu trữ (Phụ lục B, `CFG-33-02`). Quản trị viên quyết định lưu trữ dài hạn hoặc xóa. Hệ thống **không tự động xóa** hồ sơ khách hàng đã định danh còn nằm ngoài Thùng rác — hành vi này **cố định**.

  **Năm ngoại lệ có chủ đích**, mỗi ngoại lệ chỉ xóa hoặc khử phần định danh chứ không xóa giá trị kinh doanh, hoặc chỉ thực thi một quyết định xóa mà con người đã đưa ra:
  - **(a)** tự động khử định danh nhóm Định danh KYC khi hết thời hạn lưu (`BR-01.5b`);
  - **(b)** tự động xóa Hồ sơ Khách hàng Tạm chưa từng có nhân viên phản hồi (`BR-33.6`, nhánh thứ nhất);
  - **(c)** tự động khử định danh Hồ sơ Khách hàng Tạm khi chạm trần lưu tuyệt đối (`BR-33.6`);
  - **(d)** dọn dẹp Thùng rác quá thời hạn (`BR-05.4`) — dữ liệu mà con người đã quyết định xóa, chịu toàn bộ chốt an toàn tại `BR-05.6`;
  - **(e)** tự động xóa **tệp nhập khẩu gốc** khi hết thời hạn lưu (`BR-33.8`, `CFG-22-01`) và **tài liệu xác minh danh tính** thu theo `BR-33.7` sau 30 ngày kể từ khi yêu cầu hoàn tất (`BR-01.5b`) — tệp phục vụ một tiến trình đã kết thúc, không phải hồ sơ khách hàng.

  Ngoài năm ngoại lệ này, không tiến trình nào được tự động xóa dữ liệu khách hàng.

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

  **Khi không xác minh được — biện pháp phòng ngừa thay cho từ chối:** hệ thống **không** từ chối rồi bỏ mặc, mà áp **biện pháp phòng ngừa tạm thời**: chuyển toàn bộ kênh sang Từ chối nhận tin cho nhóm Tiếp thị và gắn Hạn chế xử lý (`BR-30.6`), đồng thời phản hồi nêu rõ cần bổ sung gì để thực hiện được yêu cầu. Biện pháp phòng ngừa chịu bốn ràng buộc:
  - **(a) Thời hạn:** tối đa **30 ngày** (Phụ lục B, `CFG-33-04`). Hết thời hạn mà khách không bổ sung xác minh, biện pháp phòng ngừa tự động được dỡ và yêu cầu đóng với lý do "Không xác minh được danh tính".
  - **(b) Thẩm quyền dỡ sớm:** Quản trị viên **cùng** Người phụ trách Bảo vệ Dữ liệu (hoặc người thứ hai theo `NFR-14`), khi xác định người yêu cầu không phải chủ thể dữ liệu hoặc khi khách xác nhận không có yêu cầu nào. Việc dỡ được ghi nhật ký.
  - **(c) Bắt buộc thông báo:** Người phụ trách bản ghi được thông báo khi biện pháp phòng ngừa được áp, nêu rõ lý do và thời hạn.
  - **(d) Ghi nhận đúng bản chất:** bằng chứng đồng thuận cho lượt hạ mức này dùng nguồn **"Yêu cầu chưa xác minh được danh tính"** (A.8), **không** ghi là "Yêu cầu trực tiếp của khách hàng".

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
| `AC-33.7.5` | Tiếp nối AC-33.7.2, xác định người gửi không phải chủ thể | Quản trị viên và Người phụ trách Bảo vệ Dữ liệu cùng dỡ sớm | Biện pháp được dỡ; nhật ký ghi hai người thực hiện |
| `AC-33.8.1` | Hoàn tất xóa theo quyền chủ thể cho một khách có dữ liệu ở đủ 12 nơi lưu | Mở Biên bản Hoàn tất Xử lý | Biên bản có đủ 12 hàng, mỗi hàng có trạng thái và số lượng đối tượng đã xử lý; không có câu "đã xóa xong" |
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

**Vai trò sử dụng chính:** Chính người dùng — bất kể vai trò — (tự khai báo nghỉ phép và người xử lý thay), Người phụ trách hiện tại (bàn giao ngang cho đồng nghiệp cùng nhóm), Quản lý Kinh doanh (trong phạm vi đơn vị), Quản trị viên, Chủ sở hữu.

**Bối cảnh nghiệp vụ:** Nhân viên nghỉ việc là sự kiện hằng tháng ở mọi đội kinh doanh. Vì phạm vi truy cập bị giới hạn theo `BR-01.4`, danh bạ của nhân viên rời đi sẽ thành vùng chết nếu không có công cụ bàn giao: đồng nghiệp không thấy, Quản lý chỉ thấy trong phạm vi đơn vị, và tổ chức buộc phải cấp quyền xem toàn bộ cho tất cả để chữa cháy — phá vỡ mô hình phân quyền tại Mục 5.

**Quy tắc nghiệp vụ:**

- **`BR-34.1` (Chuyển giao đơn lẻ):** Trên hồ sơ khách hàng hoặc doanh nghiệp, người có quyền đổi được Người phụ trách. Hệ thống ghi vào lịch sử bản ghi và thông báo cho cả người giao và người nhận.

- **`BR-34.1b` (Bàn giao ngang giữa đồng nghiệp):** **Người phụ trách hiện tại** tự khởi tạo được "Đề nghị chuyển giao" cho một đồng nghiệp **trong cùng nhóm/đơn vị tổ chức**, không cần Quản lý thực hiện thay. Chuyển giao có hiệu lực khi **người nhận chấp nhận**; hệ thống thông báo cho Quản lý Kinh doanh, và Quản lý được **thu hồi trong 3 ngày làm việc** nếu không đồng ý. Chuyển giao ra ngoài nhóm/đơn vị vẫn phải do Quản lý trở lên thực hiện.

  **Lý do nghiệp vụ:** Hoán đổi khách giữa hai nhân viên (đổi địa bàn, khách quen của đồng nghiệp, khách yêu cầu đổi người phụ trách) xảy ra liên tục; nếu mọi lượt đều qua Quản lý thì Quản lý thành người chuyển bản ghi hộ, và trong lúc chờ, đồng nghiệp không thấy bản ghi nên khách gọi vào không ai có ngữ cảnh — dẫn tới việc chuyển thông tin khách qua kênh chat nội bộ, đưa dữ liệu ra khỏi hệ thống.

- **`BR-34.2` (Chuyển giao hàng loạt):** Chọn nhiều bản ghi theo bộ lọc (Người phụ trách, Đơn vị tổ chức, Giai đoạn vòng đời, Thẻ) và chuyển giao đồng thời. **Bắt buộc có bước xem trước** hiển thị tổng số khách hàng, số doanh nghiệp, số Cơ hội đang mở và số Vé hỗ trợ đang mở bị ảnh hưởng trước khi xác nhận.

- **`BR-34.3` (Phạm vi thực thể con):** Người thực hiện chọn một trong ba mức: **(a)** chỉ khách hàng/doanh nghiệp; **(b)** kèm Cơ hội và Vé hỗ trợ **đang mở**; **(c)** kèm toàn bộ, gồm cả thực thể đã đóng. Mặc định là **(b)** (Phụ lục B, `CFG-34-01`).

- **`BR-34.4` (Chốt an toàn khi vô hiệu hóa người dùng) — sàn bắt buộc:** Hệ thống **không cho phép** vô hiệu hóa một người dùng khi người đó còn là Người phụ trách của bất kỳ bản ghi nào, cho tới khi người thực hiện chỉ định người nhận bàn giao. Khi cần vô hiệu hóa gấp (ví dụ rủi ro bảo mật), hệ thống cho phép **bàn giao tạm về Quản lý trực tiếp** của người đó làm mặc định, và đưa toàn bộ bản ghi vào danh sách **"Chờ bàn giao lại"**.

  **Lý do nghiệp vụ:** Vô hiệu hóa trước, bàn giao sau là cách chắc chắn nhất để tạo ra hàng trăm bản ghi vô chủ mà không ai biết cho tới khi khách phàn nàn.

- **`BR-34.5` (Báo cáo bản ghi vô chủ):** Báo cáo thường trực **"Bản ghi không có Người phụ trách hoạt động"** (người phụ trách đã bị vô hiệu hóa, đã rời tổ chức, hoặc để trống), phục vụ Quản lý và Quản trị Chất lượng Dữ liệu rà soát định kỳ.

- **`BR-34.6` (Nghỉ phép & ủy quyền tạm):** **Chính người dùng — bất kể vai trò** — tự khai báo được khoảng thời gian nghỉ phép kèm người xử lý thay; Quản lý Kinh doanh khai báo được thay cho thành viên trong nhóm mình. Trong khoảng đó, người dùng được coi là **"không khả dụng"**: không nhận khách hàng tiềm năng mới (`BR-31.1`), yêu cầu chờ xử lý chuyển ngay cho người xử lý thay (`BR-31.6`), nhưng **quyền phụ trách chính không thay đổi**. Trạng thái không khả dụng cũng được áp **tự động** khi người dùng không đăng nhập quá **14 ngày liên tiếp**. Với nhánh tự động — vốn không có ai được khai báo làm người xử lý thay:
  - **(a)** người xử lý thay mặc định là **Quản lý trực tiếp** của người đó, thống nhất `BR-34.4`;
  - **(b)** hệ thống **bắt buộc thông báo** cho chính người dùng và cho Quản lý khi trạng thái được bật;
  - **(c)** trạng thái **tự hết hiệu lực ngay ở lần đăng nhập kế tiếp**.

  **Lý do nghiệp vụ:** Không có ba quy tắc này thì yêu cầu chờ xử lý tại `BR-31.6` không có đích, và một nhân viên đi công tác dài sẽ bị âm thầm loại khỏi vòng phân bổ trong khi yêu cầu của khách cũ họ phụ trách nằm im.

- **`BR-34.7` (Kiểm toán):** Mọi thao tác chuyển giao (đơn lẻ và hàng loạt) được ghi nhật ký (`NFR-07`): người thực hiện, người giao, người nhận, số lượng và danh sách bản ghi bị ảnh hưởng, thời điểm.

- **`BR-34.8` (Ghi nhận người xử lý thay để tính thành tích):** Khi một người **không phải Người phụ trách chính** xử lý một Yêu cầu chờ xử lý (`BR-31.6`) hoặc một Cơ hội phát sinh từ yêu cầu đó, hệ thống ghi nhận **"Người xử lý"** riêng biệt với Người phụ trách trên bản ghi yêu cầu. Báo cáo thành tích thể hiện được cả hai vai.

  **Lý do nghiệp vụ:** Khách cũ hỏi mua thêm là nơi sinh tranh chấp nội bộ đầu tiên về thành tích và hoa hồng; không ghi nhận thì hai người tranh nhau một đơn mà không có căn cứ phân xử, hoặc không ai nhận vì biết không được tính công.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-34.1.1` | Quản lý Kinh doanh mở hồ sơ khách do A phụ trách | Đổi Người phụ trách sang B | B là Người phụ trách; A và B đều nhận thông báo; lịch sử bản ghi ghi nhận |
| `AC-34.1b.1` | A và C cùng nhóm | A gửi Đề nghị chuyển giao một khách cho C | Khách vẫn do A phụ trách cho tới khi C chấp nhận; sau khi C chấp nhận, C là Người phụ trách; Quản lý nhận thông báo |
| `AC-34.1b.2` | Tiếp nối AC-34.1b.1, sau 2 ngày làm việc | Quản lý thu hồi chuyển giao | Khách trở lại do A phụ trách |
| `AC-34.1b.3` | Tiếp nối AC-34.1b.1, đã qua 3 ngày làm việc | Quản lý tìm hành động thu hồi | Không còn khả dụng |
| `AC-34.1b.4` | A và D thuộc hai đơn vị khác nhau | A tìm cách gửi Đề nghị chuyển giao cho D | Không chọn được D; chuyển giao ra ngoài đơn vị cần Quản lý thực hiện |
| `AC-34.2.1` | A phụ trách 450 khách hàng, 20 doanh nghiệp, 8 Cơ hội mở, 3 Vé mở | Quản lý lọc "Người phụ trách là A", chọn chuyển cho B | Bước xem trước hiển thị đúng 450, 20, 8, 3 trước khi xác nhận |
| `AC-34.3.1` | Tiếp nối AC-34.2.1, dùng mức mặc định | Xác nhận | Khách hàng, doanh nghiệp, 8 Cơ hội mở và 3 Vé mở chuyển sang B; các Cơ hội đã đóng của A giữ nguyên |
| `AC-34.4.1` | A còn phụ trách 450 khách hàng | Quản trị viên vô hiệu hóa tài khoản A | Bị chặn, yêu cầu chỉ định người nhận bàn giao |
| `AC-34.4.2` | Cùng bối cảnh, cần vô hiệu hóa gấp | Quản trị viên chọn bàn giao tạm về Quản lý trực tiếp | A bị vô hiệu hóa; các bản ghi thuộc Quản lý trực tiếp và có trong danh sách "Chờ bàn giao lại" |
| `AC-34.5.1` | Sau khi bàn giao toàn bộ bản ghi của A | Mở báo cáo "Bản ghi không có Người phụ trách hoạt động" | Không còn bản ghi nào thuộc A |
| `AC-34.6.1` | Một Nhân viên Kinh doanh, một Nhân viên Hỗ trợ, một Nhân viên Marketing và một Quản lý Marketing | Mỗi người tự khai báo nghỉ phép 5 ngày kèm người xử lý thay | Cả bốn lưu thành công; trong khoảng nghỉ không ai nhận phân bổ mới; quyền phụ trách chính của họ không đổi |
| `AC-34.6.2` | Quản lý Kinh doanh | Khai báo nghỉ phép thay cho một thành viên trong nhóm | Lưu thành công |
| `AC-34.6.3` | Nhân viên Kinh doanh | Tìm cách khai báo nghỉ phép thay cho đồng nghiệp | Không khả dụng |
| `AC-34.6.4` | Nhân viên E không đăng nhập 14 ngày liên tiếp, không khai báo nghỉ phép | Qua mốc 14 ngày | E ở trạng thái không khả dụng; người xử lý thay là Quản lý trực tiếp; E và Quản lý nhận thông báo; không bản ghi nào đổi Người phụ trách |
| `AC-34.6.5` | Tiếp nối AC-34.6.4 | E đăng nhập lại | Trạng thái không khả dụng hết ngay; E trở lại vòng phân bổ mà không cần Quản trị viên can thiệp |
| `AC-34.7.1` | Sau chuyển giao hàng loạt tại AC-34.3.1 | Người có quyền đọc nhật ký mở nhật ký | Thấy người thực hiện, A, B, số lượng và danh sách bản ghi, thời điểm |
| `AC-34.8.1` | A nghỉ phép, B xử lý một Yêu cầu chờ xử lý và tạo Cơ hội từ đó | Mở báo cáo thành tích | Cơ hội hiển thị A là Người phụ trách và B là Người xử lý |

---

#### FEAT-35 — Chia sẻ Bản ghi & Đội ngũ Phụ trách Khách hàng

**Mô tả nghiệp vụ:** Cho phép nhiều người cùng phục vụ một khách hàng với các mức quyền khác nhau, bổ sung cho mô hình một Người phụ trách.

**Vai trò sử dụng chính:** Người phụ trách bản ghi (chia sẻ bản ghi mình phụ trách), Nhân viên Hỗ trợ (đối tượng chính nhận quyền đọc tự động — `BR-35.4`), Quản lý Kinh doanh, Quản trị viên, Chủ sở hữu.

**Bối cảnh nghiệp vụ:** Một khách hàng doanh nghiệp lớn được phục vụ đồng thời bởi nhân viên kinh doanh, Quản lý Khách hàng Hiện hữu, nhân viên hỗ trợ và kế toán. Do Người phụ trách được gán cho người tạo (`BR-01.3`) — thực tế luôn là nhân viên kinh doanh — phạm vi "của mình" của Nhân viên Hỗ trợ gần như rỗng, khiến Ngữ cảnh Khách hàng một chạm (`FEAT-28`) không dùng được cho đúng đối tượng nó được thiết kế, và `KPI-02` không thể đạt.

**Quy tắc nghiệp vụ:**

- **`BR-35.1` (Đội ngũ phụ trách):** Mỗi khách hàng/doanh nghiệp có thể có một Đội ngũ phụ trách gồm nhiều thành viên (số tối đa theo gói tại `NFR-11`), mỗi thành viên mang một **vai trò tham gia** từ A.13 (Kinh doanh chính, Hỗ trợ kỹ thuật, Quản lý khách hàng, Kế toán công nợ, Quan sát) và một **mức quyền**: **Chỉ đọc** hoặc **Chỉnh sửa**. Mức quyền là **trần trên của lượt chia sẻ, không phải một lượt cấp quyền**: thành viên không có năng lực sửa theo vai trò thì dù được thêm ở mức Chỉnh sửa vẫn không sửa được — quyền hiệu lực là **phần giao** của năng lực vai trò và mức chia sẻ. Màn hình phải nói rõ điều này khi xảy ra.

  **Vai trò nào được thêm vào đội ngũ:** mọi vai trò đều được, nhưng **Nhân viên và Quản lý Marketing chỉ được thêm với vai trò tham gia "Quan sát"** — khi đó họ áp cột (B) của bảng che mặt nạ như mọi thành viên Chỉ đọc (`BR-04.5b`), và **vẫn không đọc được** ghi chú phạm vi "Nội bộ đội bán hàng" (`BR-36.1`). Các vai trò khác — kể cả Nhân viên Hỗ trợ — được thêm với bất kỳ vai trò tham gia nào, và khi đó đọc được ghi chú "Nội bộ đội bán hàng" **của riêng bản ghi đó**.

  **Lý do nghiệp vụ:** Nếu người mời tin rằng mình đã cấp quyền sửa còn người được mời thấy hệ thống từ chối, cả hai sẽ coi đó là lỗi. Marketing có tầm nhìn toàn tổ chức, nên nếu tư cách thành viên đội ngũ mở thêm quyền đọc nội dung thương lượng thì lệnh cấm tại `BR-36.1` và sàn của `CFG-36-03` bị vô hiệu chỉ bằng thao tác thêm thành viên.

- **`BR-35.2` (Quyền chia sẻ):** Người phụ trách bản ghi và Quản lý Kinh doanh thêm/bớt thành viên trong phạm vi của mình; Quản trị viên và Chủ sở hữu thao tác trên mọi bản ghi. Thành viên mức Chỉ đọc **không** được chia sẻ tiếp. Được chia sẻ một bản ghi đưa người đó **vào phạm vi dữ liệu của bản ghi ấy**.

- **`BR-35.3` (Chia sẻ có thời hạn):** Mỗi lượt chia sẻ được đặt ngày hết hiệu lực; hết hạn thì quyền tự động thu hồi và ghi vào lịch sử bản ghi.

- **`BR-35.3b` (Vòng đời của Yêu cầu quyền truy cập, Đề nghị chuyển giao và Đề nghị gộp):** Ba hành động tại `BR-17.3` tạo ra một **bản ghi yêu cầu** có vòng đời riêng, không chỉ là một thông báo. Ba hành động sinh **bốn loại yêu cầu** vì "Yêu cầu quyền truy cập" cho chọn xin quyền đọc hoặc quyền sửa:
  - **Nội dung:** người yêu cầu, khách hàng liên quan, loại yêu cầu (xin quyền đọc / xin quyền sửa / đề nghị chuyển giao / đề nghị gộp), lý do, thời điểm tạo, hạn xử lý, người xử lý, trạng thái (Chờ xử lý / Đã chấp thuận / Đã từ chối kèm lý do / Tự động cấp quyền đọc tạm do quá hạn — trạng thái cuối **chỉ áp dụng cho loại xin quyền đọc**).
  - **Người xử lý:** xin quyền đọc/sửa và đề nghị chuyển giao — Người phụ trách hiện hữu, hoặc Quản lý Kinh doanh của họ sau khi leo thang; đề nghị gộp — Quản trị Chất lượng Dữ liệu hoặc Quản trị viên.
  - **Cam kết thời gian và hành vi khi quá hạn:** theo `BR-17.2c`. Hệ thống **không** tự cấp quyền sửa, **không** tự chuyển giao và **không** tự gộp trong bất kỳ trường hợp nào.
  - **Kiểm toán:** mọi thay đổi trạng thái của bản ghi yêu cầu được ghi nhật ký (`NFR-07`).

- **`BR-35.4` (Quyền đọc tự động cho tuyến Hỗ trợ):** Khi một Vé hỗ trợ hoặc Hội thoại đa kênh **đang mở** được gắn với một khách hàng, nhân viên đang xử lý vé/hội thoại đó **tự động có quyền đọc** hồ sơ 360, Dòng thời gian và Ngữ cảnh Khách hàng của khách đó trong suốt thời gian vé/hội thoại còn mở, kể cả khi bản ghi nằm ngoài phạm vi dữ liệu thông thường của họ. Quyền này:
  - **(a)** là quyền **đọc**, không cho sửa dữ liệu nghiệp vụ — **ngoại lệ duy nhất** là hai thao tác của `FEAT-33` mà tuyến Hỗ trợ thực thi được ngay khi khách yêu cầu: **gắn Hạn chế xử lý** (`BR-30.6`) và **hạ đồng thuận xuống Từ chối nhận tin** (`BR-30.10`). Ngoại lệ chỉ có hiệu lực trong thời gian vé/hội thoại còn mở, và mỗi lượt đều ghi nhật ký;
  - **(b)** áp **cột (C)** của bảng `BR-04.3` — kênh liên lạc che một phần, đủ để xác minh đúng người và liên lạc trong hệ thống theo `BR-04.6`; định danh KYC vẫn che hoàn toàn;
  - **(c)** **bắt buộc ghi nhật ký truy cập** để phát hiện lạm dụng;
  - **(d)** tự động hết hiệu lực khi vé/hội thoại đóng.

  **Lý do nghiệp vụ:** Đây là cơ chế giải quyết trực tiếp bế tắc của `FEAT-28`, thay cho việc cấp quyền xem toàn bộ cho mọi nhân viên hỗ trợ. Hai thao tác ghi được mở vì chúng chỉ thu hẹp phạm vi xử lý, đảo lại được, và là điều kiện để cam kết "Tức thì" tại `FEAT-33` có người thực thi — khách nói "đừng gửi tin cho tôi nữa" ngay trong hội thoại mà người đang nói chuyện với khách không làm được gì thì cam kết đó chỉ có trên giấy.

- **`BR-35.5` (Phụ thuộc tài liệu Phân quyền):** Cơ chế thực thi phạm vi dữ liệu và thứ tự ưu tiên giữa quyền theo vai trò, quyền theo phạm vi tổ chức và quyền chia sẻ bản ghi thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md). Tài liệu này ràng buộc ba điểm: **(i)** chia sẻ nới rộng **phạm vi dữ liệu**, không nới rộng **năng lực theo vai trò**; **(ii)** chia sẻ **không nới lỏng bất kỳ mức che trường nào** — chính sách tại `FEAT-04` áp nguyên cho thành viên Đội ngũ phụ trách; **(iii)** một lượt chia sẻ **không vượt được** lượt chặn tường minh trên bản ghi do quản trị viên đặt (`BR-39.1` của tài liệu Phân quyền) — chia sẻ không lấy đi quyền nào, nhưng cũng không gỡ được một lệnh chặn đặt vì lý do pháp lý hoặc hợp đồng.

- **`BR-35.6` (Kiểm toán):** Mọi thao tác chia sẻ, thu hồi chia sẻ và mọi lượt truy cập theo quyền đọc tự động tại `BR-35.4` được ghi nhật ký (`NFR-07`).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-35.1.1` | Tập đoàn Đại Việt do A phụ trách | A thêm B (Quản lý khách hàng, Chỉnh sửa), C (Hỗ trợ kỹ thuật, Chỉ đọc), D (Kế toán công nợ, Chỉ đọc) | Cả ba truy cập được hồ sơ; B sửa được; C, D chỉ đọc |
| `AC-35.1.2` | Một thành viên có vai trò hệ thống không có năng lực sửa khách hàng được thêm ở mức Chỉnh sửa | Thành viên đó mở hồ sơ | Không sửa được; màn hình giải thích quyền hiệu lực là phần giao giữa năng lực vai trò và mức chia sẻ |
| `AC-35.1.3` | A thêm một Nhân viên Marketing vào đội ngũ | Mở danh sách vai trò tham gia | Chỉ chọn được "Quan sát" |
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

**Tham chiếu:** [ADR-0007](../docs/adr/0007-record-sharing-and-permission-precedence-contract.md) — hợp đồng nghiệp vụ về chia sẻ bản ghi và thứ tự ưu tiên quyền.

---

#### FEAT-36 — Ghi chú & Ghi nhận Hoạt động Khách hàng

**Mô tả nghiệp vụ:** Quản lý ghi chú nội bộ và bản ghi hoạt động (cuộc gọi, cuộc họp, email đã gửi) gắn với khách hàng — nguồn dữ liệu chính của Dòng thời gian 360 độ.

**Vai trò sử dụng chính:** Mọi người dùng có quyền xem bản ghi tương ứng.

**Bối cảnh nghiệp vụ:** Ghi chú được viện dẫn ở nhiều nơi (nguồn sự kiện của `FEAT-27`, hành động nhanh tại `BR-02.2`, ghi chú ghim tại `BR-28.1`, bằng chứng liên hệ tại `BR-31.8`). Trong vận hành thật, ghi chú chứa nội dung thương mại nhạy cảm ("khách sẵn sàng trả tới 800 triệu", "đang so sánh với đối thủ X"); nếu không quy định rõ ai đọc được và ai xóa được, nhân viên sẽ xóa lịch sử trước khi nghỉ việc hoặc ngừng ghi chú thật — làm rỗng giá trị cốt lõi mà `FEAT-27` và `KPI-02` hướng tới.

**Quy tắc nghiệp vụ:**

- **`BR-36.1` (Phạm vi đọc):** Mỗi ghi chú có một trong **ba** phạm vi:
  - **Nội bộ đội bán hàng (mặc định):** Người phụ trách, thành viên Đội ngũ phụ trách (`FEAT-35`) — theo tư cách thành viên, bất kể vai trò hệ thống —, Quản lý Kinh doanh của họ, Quản trị viên và Chủ sở hữu đọc được. **Nhân viên và Quản lý Marketing không đọc được — kể cả khi là thành viên đội ngũ** (`BR-35.1`).
  - **Chung:** mọi người có quyền xem bản ghi đều đọc được, gồm Marketing và tuyến Hỗ trợ.
  - **Giới hạn:** chỉ người tạo, Người phụ trách bản ghi và Quản lý trở lên.

  Phạm vi mặc định là tham số cấu hình (Phụ lục B, `CFG-36-01`).

  **Lý do nghiệp vụ:** Nếu mặc định để toàn tổ chức đọc được — nhất là khi Marketing có tầm nhìn toàn tổ chức — nhân viên sẽ ngừng ghi chú thật hoặc ghi vào sổ riêng. Marketing cần dữ liệu phân khúc, không cần nội dung thương lượng giá.

- **`BR-36.2` (Sửa ghi chú):** Người tạo sửa được nội dung trong **24 giờ** đầu (Phụ lục B, `CFG-36-02`). Sau thời hạn đó chỉ được **bổ sung** nội dung mới, không sửa nội dung cũ.

  **Lý do nghiệp vụ:** Lịch sử trao đổi chỉ có giá trị làm căn cứ khi không bị viết lại sau khi sự việc đã diễn ra.

- **`BR-36.3` (Không xóa cứng) — sàn bắt buộc:** Ghi chú và bản ghi hoạt động **không được xóa vĩnh viễn** bởi người dùng thường. Thao tác "Xóa" chỉ **ẩn** ghi chú khỏi dòng thời gian, giữ nguyên nội dung và ghi nhật ký người ẩn (`NFR-07`). Quản trị viên xem được ghi chú đã ẩn. Ngoại lệ duy nhất là khi thực thi quyền chủ thể dữ liệu (`BR-33.8`).

- **`BR-36.4` (Ghi chú ghim):** Người phụ trách bản ghi và Quản lý trở lên ghim được tối đa **3 ghi chú** lên đầu hồ sơ; các ghi chú ghim là nội dung hiển thị trong Ngữ cảnh Khách hàng một chạm (`BR-28.1`), sau khi lọc theo `BR-36.7`.

- **`BR-36.5` (Bản ghi hoạt động tự động):** Hoạt động phát sinh trong hệ thống (cuộc gọi đã thực hiện kèm thời lượng, email đã gửi, tin nhắn đã gửi, cuộc hẹn đã tạo) được tự động ghi thành bản ghi hoạt động và **không cho sửa nội dung**. Đây là nguồn sinh bằng chứng liên hệ nhóm 1 (`BR-31.8`); bằng chứng nhóm 2 cũng được ghi thành bản ghi hoạt động nhưng đánh dấu rõ là do người dùng khai báo, kèm người xác nhận.

  **Lý do nghiệp vụ:** Bằng chứng liên hệ chỉ đáng tin khi không tạo khống và không sửa được.

- **`BR-36.6` (Thuộc phạm vi dữ liệu cá nhân):** Nội dung ghi chú và hoạt động thuộc phạm vi phải xử lý khi thực thi quyền xóa của chủ thể dữ liệu (`BR-33.8`).

- **`BR-36.7` (Ghi chú trong Ngữ cảnh Khách hàng một chạm):** Khung ngữ cảnh là cơ chế truy cập chính của Nhân viên Hỗ trợ (`FEAT-28`), nhưng phạm vi mặc định của ghi chú là "Nội bộ đội bán hàng" mà tuyến Hỗ trợ không đọc được. Quy tắc:
  - Khung ngữ cảnh **lọc ghi chú theo phạm vi đọc của người xem** (`BR-36.1`).
  - Người ghim ghi chú được chọn **"Cho phép tuyến Hỗ trợ đọc"** trên từng ghi chú ghim, để chia sẻ đúng thông tin cần cho việc phục vụ (ví dụ "khách đang chờ xử lý khiếu nại lô hàng tháng 8") mà không mở nội dung thương lượng giá.
  - Phạm vi ghi chú tuyến Hỗ trợ đọc được là tham số cấu hình (Phụ lục B, `CFG-36-03`), mặc định: phạm vi "Chung" cộng các ghi chú ghim đã được đánh dấu cho phép.

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

- **NFR-01 (Tìm kiếm & lọc danh bạ):** Tìm kiếm khách hàng theo tên, email, số điện thoại hoặc lọc theo danh sách phản hồi dưới **300 mili giây với 95% lượt truy vấn**.
- **NFR-02 (Dòng thời gian 360 độ & Ngữ cảnh Khách hàng):** Phản hồi dưới **150 mili giây với 95% lượt truy vấn** — đây là **ngưỡng nghiệm thu duy nhất** cho cả hai chức năng. Mức 50 mili giây với 50% lượt truy vấn tại `BR-28.2` là mục tiêu tối ưu, không phải tiêu chí nghiệm thu.
- **NFR-03 (Tốc độ nhập khẩu):** Tiến trình nhập khẩu xử lý tối thiểu **1.000 dòng/giây** với tệp dung lượng lớn.

### 4.2 Độ tin cậy & Toàn vẹn Dữ liệu

- **NFR-04 (Gộp & hoàn tác gộp toàn vẹn):** Gộp và hoàn tác gộp phải cùng thành công hoặc cùng thất bại như một đơn vị duy nhất. Nếu có lỗi ở bất kỳ bước chuyển giao nào, toàn bộ thao tác được hủy hoàn toàn, không để lại trạng thái dang dở; trường hợp gián đoạn ngoài ý muốn được xử lý theo `FEAT-21`.
- **NFR-05 (Bảo toàn sổ cái gộp):** Mục sổ cái gộp được lưu vĩnh viễn và không bị xóa kể cả khi Bản ghi Chính bị xóa mềm, chỉ chịu ngoại lệ khử định danh tại `BR-33.8`.

### 4.3 An toàn & Bảo mật

- **NFR-06 (Bảo vệ dữ liệu nhạy cảm):** Chính sách phân quyền trường được thực thi **đúng theo `FEAT-04`** — mức hiển thị của mỗi trường do nhóm trường (`BR-04.1`) và quan hệ của người xem với bản ghi (`BR-04.3`) quyết định, không do một quy tắc riêng ở mục này. Yêu cầu phi chức năng ở đây là: chính sách phải được thực thi **trên chính dữ liệu hệ thống trả ra**, sao cho dữ liệu vượt mức hiển thị cho phép **không bao giờ rời khỏi hệ thống** — kể cả khi giao diện bị can thiệp, và kể cả qua tệp xuất — và mọi lượt nâng mức hiển thị (`BR-04.4`) đều để lại dấu vết theo `NFR-07`.

- **NFR-07 (Nhật ký kiểm toán):** Danh mục dưới đây là **nguồn duy nhất** về các thao tác bắt buộc ghi nhật ký kiểm toán; một quy tắc viện dẫn `NFR-07` mà thao tác của nó không có trong danh mục là lỗi tài liệu.
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
  13. Đọc hồ sơ khách hàng nằm ngoài phạm vi dữ liệu được gán bởi vai trò có tầm nhìn toàn tổ chức — cụ thể là Nhân viên và Quản lý Marketing (`CFG-05-02`). Quản trị viên và Chủ sở hữu không thuộc sự kiện này vì mọi thao tác của họ đã được phủ bởi các sự kiện khác và bởi `NFR-14`. Khối lượng nhật ký sinh ra ở đây được chấp nhận có chủ đích: đó là cái giá của việc cấp tầm nhìn toàn tổ chức cho một vai trò không phụ trách bản ghi nào, và là căn cứ duy nhất trả lời được câu hỏi ai đã đọc hồ sơ của một khách hàng khi có khiếu nại.
  14. Ẩn ghi chú (`BR-36.3`).
  15. Nâng mức đồng thuận từ mọi nguồn tác động (`BR-30.10`).
  16. Sửa nguồn gốc theo lô (`BR-32.3b`).
  17. Đọc nhật ký kiểm toán ở cả hai mức có quyền (`NFR-14`).
  18. Mọi thay đổi trạng thái của bản ghi yêu cầu xin quyền đọc/sửa, đề nghị chuyển giao, đề nghị gộp (`BR-35.3b`).
  19. Bỏ qua cảnh báo trùng lặp khi tenant cấu hình "chỉ cảnh báo" (`BR-17.2`).
  20. Xác nhận "Đây là người khác dùng chung định danh này" (`BR-17.2`).
  21. Gắn nhãn Định danh dùng chung cho lô nhập khẩu chọn "Tạo bản ghi mới dù trùng" (`BR-23.3`).
  22. Khai báo và xác nhận bằng chứng liên hệ nhóm 2 (`BR-31.8`).
  23. Đánh dấu, gỡ dấu và duyệt "Lead rác" (`BR-12.4b`).
  24. Phê duyệt và thu hồi phê duyệt Chiến dịch Tái tiếp cận (`BR-12.5b`).
  25. Dỡ sớm biện pháp phòng ngừa khi không xác minh được chủ thể dữ liệu (`BR-33.7` (b)).
  26. Thay đổi tham số cấu hình (Phụ lục B).
  27. Xử lý yêu cầu chủ thể dữ liệu (`FEAT-33`), gồm gắn/dỡ Hạn chế xử lý (`BR-30.6`) và hạ đồng thuận theo yêu cầu của khách (`BR-35.4` (a)).

  Mỗi bản ghi nhật ký lưu: người thực hiện, thời điểm, bản ghi bị tác động, loại thao tác và **tên các trường bị tác động**.

  **Giới hạn nội dung — sàn bắt buộc:** nhật ký **không được lưu giá trị thật của các trường nhạy cảm** thuộc ba nhóm tại `BR-04.1`. Với mở khóa mặt nạ, nhật ký chỉ ghi "đã mở khóa trường Số điện thoại của bản ghi X", không ghi chính số điện thoại đó. Với thay đổi dữ liệu không nhạy cảm (giai đoạn vòng đời, người phụ trách, thẻ, tham số cấu hình), nhật ký lưu giá trị trước và sau.

  **Lý do nghiệp vụ:** Nếu lưu giá trị thật, nhật ký trở thành kho dữ liệu cá nhân lớn nhất của phân hệ và là đường đi vòng qua chính sách che mặt nạ — người xem được nhật ký sẽ đọc được giá trị mà chính họ không có quyền mở khóa.

- **NFR-08 (Thời hạn lưu nhật ký kiểm toán):** Nhật ký được lưu tối thiểu **24 tháng** (gói tiêu chuẩn) và **60 tháng** (gói Enterprise). Trong thời hạn này nhật ký **không cho sửa hoặc xóa từng bản ghi**, kể cả bởi Chủ sở hữu — ngoại lệ duy nhất là **khử định danh** khi thực thi quyền chủ thể dữ liệu (`BR-33.8`), do Quản trị viên cùng Người phụ trách Bảo vệ Dữ liệu thực hiện và để lại một bản ghi nhật ký về chính việc khử định danh đó.

- **NFR-14 (Kiểm soát truy cập nhật ký kiểm toán) — sàn bắt buộc:** Nhật ký kiểm toán có **ba mức truy cập**:

| Mức | Ai được cấp | Phạm vi đọc |
| --- | --- | --- |
| **Toàn phần** | Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu | Toàn bộ nhật ký, truy vấn theo khoảng thời gian và theo bản ghi |
| **Theo bản ghi đang xử lý** | Quản trị viên, **chỉ trong phạm vi một bản ghi đang có yêu cầu chủ thể dữ liệu hoặc thao tác gộp/khôi phục đang thực hiện** | Chỉ nhật ký của đúng bản ghi đó, chỉ trong thời gian yêu cầu còn mở — mức tối thiểu để thực hiện nghĩa vụ khử định danh (`BR-33.8`) và tra soát khi hoàn tác gộp |
| **Không truy cập** | Mọi vai trò nghiệp vụ khác (Quản lý Kinh doanh, Marketing, Nhân viên Kinh doanh, Nhân viên Hỗ trợ) | — |

  **Mọi lượt đọc nhật ký**, ở cả hai mức có quyền, đều được ghi nhật ký. Không hỗ trợ xuất toàn bộ nhật ký ra tệp trừ khi có phê duyệt kép theo quy tắc dưới.

  **Quy tắc thay thế người thứ hai:** khi tenant không chỉ định Người phụ trách Bảo vệ Dữ liệu, trách nhiệm thuộc Chủ sở hữu (Mục 2.2) — nếu áp nguyên văn "Chủ sở hữu và Người phụ trách Bảo vệ Dữ liệu cùng phê duyệt" thì hai người sụp về một. Khi đó người thứ hai là **một Quản trị viên khác, không phải người đang thực hiện thao tác**. Quy tắc thay thế này áp cho mọi chỗ tài liệu yêu cầu "hai người khác nhau" hoặc "phê duyệt kép" (`BR-25.4`, `BR-33.7`, `BR-33.8`, `NFR-08`, `NFR-14`, Phụ lục B).

  **Lý do nghiệp vụ:** Nhật ký kiểm toán chứa dấu vết truy cập vào toàn bộ khách hàng; nếu mở rộng quyền đọc, chính nhật ký trở thành công cụ khai thác dữ liệu.

### 4.4 Khả dụng, Sao lưu & Phục hồi

- **NFR-09 (Mức độ khả dụng):** Phân hệ cam kết mức khả dụng tối thiểu **99,9% mỗi tháng**, không tính thời gian bảo trì có thông báo trước tối thiểu 48 giờ.
- **NFR-10 (Sao lưu & phục hồi thảm họa):** Mức mất dữ liệu tối đa cho phép là **15 phút**; thời gian phục hồi mục tiêu là **4 giờ**. Quy trình phục hồi được diễn tập kiểm chứng tối thiểu **2 lần/năm**. Bản sao lưu được lưu theo cơ chế **cuốn vòng 35 ngày** — đây là con số mà bảng phạm vi xóa tại `BR-33.8` dựa vào, nên hai nơi phải giữ cùng một giá trị.

### 4.5 Khả năng mở rộng & Giới hạn dung lượng

- **NFR-11 (Giới hạn theo gói dịch vụ):** Hệ thống áp dụng và hiển thị rõ các giới hạn sau:

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

- **NFR-12 (Đa ngôn ngữ & hướng hiển thị):** Toàn bộ giao diện và thông báo nghiệp vụ hỗ trợ tối thiểu **tiếng Việt, tiếng Anh và tiếng Ả Rập**; tiếng Ả Rập bắt buộc hỗ trợ bố cục hiển thị từ phải sang trái. Tên riêng của khách hàng hiển thị đúng dấu và đúng ký tự gốc, không bị chuyển tự tự động.
- **NFR-13 (Định dạng theo vùng):** Số điện thoại, ngày tháng, đơn vị tiền tệ và múi giờ hiển thị theo thiết lập vùng của từng không gian làm việc; dữ liệu được lưu theo một chuẩn quốc tế thống nhất để báo cáo đa vùng nhất quán.

---

## 5. Ma trận quyền truy cập tính năng

| Mã FEAT | Tính năng | Nhân viên KD | Nhân viên Hỗ trợ | Quản lý KD | Nhân viên Marketing | Quản lý Marketing | Quản trị viên | Chủ sở hữu | Tiến trình Hệ thống |
| --- | --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `FEAT-01` | Tạo & Quản lý Khách hàng | Của mình | Của mình | Đơn vị của mình | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-02` | Hồ sơ 360 độ | Của mình | Của mình + đọc tự động (`BR-35.4`) | Đơn vị của mình | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-03` | Thẻ phân loại hàng loạt | Của mình | Của mình | Đơn vị của mình | **Cho phép** | **Cho phép** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-04` | Mở khóa mặt nạ dữ liệu | Có quyền Mở khóa mặt nạ | Có quyền Mở khóa mặt nạ | Có quyền Mở khóa mặt nạ | Có quyền Mở khóa mặt nạ | Có quyền Mở khóa mặt nạ | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-05` | Thùng rác & Phục hồi Khách hàng | — | — | Có quyền Xóa, Đơn vị của mình; khôi phục theo `BR-05.3`, chịu `BR-05.6` | — | — | **Toàn quyền** | **Toàn quyền** | Dọn dẹp tự động (`BR-05.4`, chịu `BR-05.6`) |
| `FEAT-06` | Tạo & Quản lý Doanh nghiệp | Của mình | Của mình | Đơn vị của mình | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-07` | Cây Doanh nghiệp Mẹ – Con | Của mình | Của mình | Đơn vị của mình | Xem cấu trúc, không xem chỉ số tài chính hợp nhất (`BR-07.4` (c)) | Xem cấu trúc, không xem chỉ số tài chính hợp nhất (`BR-07.4` (c)) | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-08` | Hồ sơ Doanh nghiệp | Của mình | Của mình | Đơn vị của mình | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-09` | Thùng rác & Phục hồi Doanh nghiệp | — | — | Có quyền Xóa, Đơn vị của mình (`BR-09.2`) | — | — | **Toàn quyền** | **Toàn quyền** | Dọn dẹp tự động (`BR-05.4`) |
| `FEAT-10` | Quan hệ Đa Doanh nghiệp | Của mình | Của mình + đọc tự động (`BR-35.4`, khung liên kết của hồ sơ 360 — `BR-02.1`) | Đơn vị của mình | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** | Cập nhật trạng thái tiếp cận khi liên kết Đã nghỉ việc (`BR-10.4`) |
| `FEAT-11` | Quan hệ Giữa các Cá nhân | Của mình | Của mình + đọc tự động (`BR-35.4`, khung liên kết — `BR-02.1`) | Đơn vị của mình | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** | Ghi chiều ngược (`BR-11.2`) |
| `FEAT-12` | Vòng đời & Ma trận Chuyển đổi | Của mình, chỉ bước tiến lên (nguyên tắc 2; lên SQL cần thẩm định — `BR-15.6`); đánh dấu "Lead rác" chờ duyệt (`BR-12.4b`) | Chỉ xem giai đoạn trong khung ngữ cảnh (`BR-28.1`); không chuyển giai đoạn (`BR-02.2`) | Đơn vị của mình, gồm bước lùi (`BR-12.7`), Disqualified và duyệt Lead rác (`BR-12.4`, `BR-12.4b`), mở lại Disqualified (`BR-12.4`), sang Nurturing nhánh điểm nguội (`BR-16.4`), Nurturing → Lead (`BR-16.5`), đồng phê duyệt Chiến dịch Tái tiếp cận (`BR-12.5b`) | Không chuyển giai đoạn thủ công (`BR-02.2`) — chỉ xem | Cấu hình thăng hạng tự động (`BR-15.4`, `BR-15.5`); không chuyển thủ công (`BR-02.2`), không lùi (`BR-12.7`), không loại (`BR-12.4`); đồng phê duyệt Chiến dịch Tái tiếp cận (`BR-12.5b`) | **Toàn quyền** (gồm ngoại lệ gian lận `BR-12.8`) | **Toàn quyền** (gồm ngoại lệ gian lận `BR-12.8`) | Bước chuyển tự sinh (`BR-12.9`), sang Nurturing khi mọi Cơ hội Thua (`BR-12.3`), thăng MQL theo ngưỡng (`BR-15.5`) |
| `FEAT-13` | Lịch sử Giai đoạn | Của mình | Của mình | Đơn vị của mình | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** | Ghi tự động |
| `FEAT-14` | Chuyển đổi Tiềm năng | Của mình | — | Đơn vị của mình, gồm Hoàn tác Chuyển đổi (`BR-14.2`) | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-15` | Chấm điểm Tiềm năng | Xem điểm, không sửa quy tắc (`BR-15.4`) | Xem điểm, không sửa quy tắc (`BR-15.4`) | Xem điểm, không sửa quy tắc (`BR-15.4`) | Xem cấu hình, không sửa (`BR-15.4`) | Cấu hình quy tắc (`BR-15.4`) | Cấu hình quy tắc (`BR-15.4`) | Cấu hình quy tắc (`BR-15.4`) | Tính điểm tự động |
| `FEAT-16` | Suy giảm Điểm | — | — | Xử lý danh sách đề xuất Nurturing (`BR-16.4`), đưa Nurturing về Lead (`BR-16.5`) | — | — | **Toàn quyền** | **Toàn quyền** | Áp suy giảm hằng ngày (`BR-16.1`, `BR-16.2`) |
| `FEAT-17` | Kiểm tra Trùng lặp | **Cho phép** (cảnh báo khi tạo/sửa) | **Cho phép** (cảnh báo khi tạo/sửa) | **Cho phép** (cảnh báo khi tạo/sửa) | **Cho phép** (cảnh báo khi tạo/sửa) | **Cho phép** (cảnh báo khi tạo/sửa) | **Cho phép**, gồm công cụ quét toàn không gian làm việc (`BR-17.4`) | **Cho phép**, gồm công cụ quét (`BR-17.4`) | Kiểm tra tức thì (`BR-17.1`) |
| `FEAT-18` | Xem trước Tác động Gộp | — | — | Có quyền Xóa | — | — | **Cho phép** | **Cho phép** | — |
| `FEAT-19` | Thực thi Gộp | — | — | Có quyền Xóa | — | — | **Cho phép** | **Cho phép** | — |
| `FEAT-20` | Hoàn tác Gộp | — | — | — | — | — | **Cho phép** | **Cho phép** | — |
| `FEAT-21` | Khôi phục Gộp bị Gián đoạn | — | — | — | — | — | **Cho phép** | **Cho phép** | Liệt kê giao dịch gián đoạn (`BR-21.2`) |
| `FEAT-22` | Tải tệp Nhập khẩu | — | — | Có quyền Nhập dữ liệu | Có quyền Nhập dữ liệu | Có quyền Nhập dữ liệu | **Cho phép** | **Cho phép** | Tự xóa tệp gốc hết hạn (`BR-33.8`) |
| `FEAT-23` | Trợ lý Ánh xạ Cột | — | — | Có quyền Nhập dữ liệu | Có quyền Nhập dữ liệu | Có quyền Nhập dữ liệu | **Cho phép** | **Cho phép** | — |
| `FEAT-24` | Xử lý Nhập & Báo cáo Lỗi | — | — | Có quyền Nhập dữ liệu | Có quyền Nhập dữ liệu | Có quyền Nhập dữ liệu | **Cho phép** | **Cho phép** | Xử lý nền theo hàng đợi |
| `FEAT-25` | Xuất Dữ liệu | Của mình, chịu hạn mức ngày, không xuất KYC (`BR-25.5`) | — | Có quyền Xuất dữ liệu, không xuất KYC; duyệt lần xuất vượt hạn mức của Nhân viên KD (`BR-25.4`) | Có quyền Xuất dữ liệu, không xuất KYC (`BR-25.4`) | Có quyền Xuất dữ liệu, duyệt xuất lớn của Nhân viên Marketing; không xuất KYC (`BR-25.4`) | **Cho phép**, gồm KYC khi có Người phụ trách Bảo vệ Dữ liệu đồng phê duyệt (`BR-25.4`) | **Cho phép**, gồm KYC khi có đồng phê duyệt; duyệt xuất lớn cho Quản trị viên và các vai trò quản lý (`BR-25.4`) | Tạo tệp nền (`BR-25.1`) |
| `FEAT-26` | Danh sách Hiển thị Dùng chung | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-27` | Dòng thời gian 360 độ | Của mình | Của mình + đọc tự động (`BR-35.4`) | Đơn vị của mình | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** | Hợp nhất sự kiện (`BR-27.1`) |
| `FEAT-28` | Ngữ cảnh Khách hàng Một chạm | Của mình | Của mình + đọc tự động (`BR-35.4`) — cơ chế truy cập chính | Đơn vị của mình | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-29` | Kênh liên lạc & Trạng thái Tiếp cận | Của mình | Của mình + đọc tự động (`BR-35.4`) | Đơn vị của mình | Xem toàn bộ | Xem toàn bộ | **Toàn quyền** | **Toàn quyền** | Cập nhật trạng thái tiếp cận (`BR-29.2`, `BR-29.3`) |
| `FEAT-30` | Đồng thuận & Định danh Dùng chung | Của mình | Của mình, cộng hạ đồng thuận trên bản ghi đang có vé/hội thoại mở (`BR-35.4` (a)) | Đơn vị của mình | **Cho phép** | **Cho phép** trên toàn tổ chức; không đổi được `CFG-30-01`, `CFG-30-02` | **Toàn quyền** | **Toàn quyền** | Mặc định nhóm Tiếp thị khi thiếu khai báo (`BR-30.9`) |
| `FEAT-31` | Phân bổ Tiềm năng Tự động | — | — | **Cho phép** cấu hình quy tắc (`BR-31.5`) | — | — | **Cho phép** | **Cho phép** | Thực thi phân bổ và thu hồi (`BR-31.1` – `BR-31.7`) |
| `FEAT-32` | Theo dõi Nguồn gốc | Xem trường trên hồ sơ (`BR-32.1`) | Xem trường trên hồ sơ (`BR-32.1`) | Xem trường trên hồ sơ (`BR-32.1`) | Xem báo cáo (`BR-32.4`) | Xem báo cáo và phân tích hiệu quả đầu tư (`BR-32.4`) | **Toàn quyền**, gồm sửa nguồn theo lô (`BR-32.3b`) | **Toàn quyền**, gồm sửa nguồn theo lô (`BR-32.3b`) | Ghi nhận tự động (`BR-32.2`) |
| `FEAT-33` | Quyền Chủ thể Dữ liệu | — | Tiếp nhận, ghi nhận; gắn Hạn chế xử lý (`BR-30.6`) và hạ đồng thuận (`BR-30.10`) trên bản ghi Của mình hoặc đang có vé/hội thoại mở (`BR-35.4` (a)); không thực thi ba loại còn lại (`BR-33.2`, `BR-33.7`) | Tiếp nhận, ghi nhận; gắn Hạn chế xử lý và hạ đồng thuận trong Đơn vị của mình; không thực thi ba loại còn lại (`BR-33.2`, `BR-33.7`) | — | — | **Cho phép** (xử lý, xóa vĩnh viễn, đóng yêu cầu — `BR-33.1`) | **Cho phép** (xử lý, xóa vĩnh viễn, đóng yêu cầu — `BR-33.1`) | Khử định danh/xóa theo thời hạn lưu (`BR-01.5b`, `BR-33.6`); tự dỡ biện pháp phòng ngừa hết hạn (`BR-33.7` (a)) |
| `FEAT-34` | Chuyển giao & Bàn giao | Bàn giao ngang trên bản ghi mình phụ trách, hiệu lực khi người nhận chấp nhận (`BR-34.1b`); tự khai báo nghỉ phép (`BR-34.6`) | Tự khai báo nghỉ phép (`BR-34.6`); bàn giao ngang nếu có bản ghi phụ trách (`BR-34.1b`) | **Cho phép** (Đơn vị của mình; chốt `BR-34.4`; thu hồi bàn giao ngang — `BR-34.1b`; khai báo nghỉ phép thay thành viên — `BR-34.6`) | Tự khai báo nghỉ phép (`BR-34.6`); bàn giao ngang nếu có bản ghi phụ trách (`BR-34.1b`) | Tự khai báo nghỉ phép (`BR-34.6`); bàn giao ngang nếu có bản ghi phụ trách (`BR-34.1b`) | **Toàn quyền** | **Toàn quyền** | Bật/tắt trạng thái không khả dụng tự động (`BR-34.6`) |
| `FEAT-35` | Chia sẻ & Đội ngũ Phụ trách | Bản ghi mình phụ trách (`BR-35.2`) | Nhận quyền đọc tự động (`BR-35.4`); chia sẻ bản ghi mình phụ trách nếu có (`BR-35.2`) | **Cho phép** (Đơn vị của mình) | Chia sẻ bản ghi mình phụ trách nếu có (`BR-35.2`); được thêm vào đội ngũ chỉ với vai trò "Quan sát" (`BR-35.1`) | Chia sẻ bản ghi mình phụ trách nếu có (`BR-35.2`); được thêm vào đội ngũ chỉ với vai trò "Quan sát" (`BR-35.1`) | **Toàn quyền** | **Toàn quyền** | Cấp/thu hồi quyền đọc tự động (`BR-35.4`); thu hồi chia sẻ hết hạn (`BR-35.3`) |
| `FEAT-36` | Ghi chú & Hoạt động | Của mình | Của mình + đọc tự động (`BR-35.4`); theo vai trò chỉ đọc ghi chú "Chung" và ghi chú ghim đã mở cho tuyến Hỗ trợ (`BR-36.7`); đọc thêm ghi chú "Nội bộ đội bán hàng" của bản ghi mà họ là thành viên đội ngũ (`BR-36.1`) | Đơn vị của mình | Chỉ ghi chú "Chung" (`BR-36.1`) | Chỉ ghi chú "Chung" (`BR-36.1`) | **Toàn quyền** | **Toàn quyền** | Tự sinh bản ghi hoạt động (`BR-36.5`) |

**Ghi chú về ma trận:**

1. **Từ vựng chuẩn** (định nghĩa một lần, dùng cho mọi ô):

| Giá trị trong ô | Nghĩa | Quy tắc nguồn |
| --- | --- | --- |
| **Của mình** | Các bản ghi người dùng là Người phụ trách, cộng các bản ghi được chia sẻ tới họ | `BR-01.4`, `BR-35.2` |
| **Đơn vị của mình** | Toàn bộ bản ghi thuộc Đơn vị tổ chức của người dùng và các đơn vị cấp dưới | `BR-01.4` |
| **Xem toàn bộ** | Đọc toàn tổ chức, **không** kèm quyền sửa; mỗi lượt đọc ngoài phạm vi được gán ghi nhật ký (`NFR-07`, mục 13) | `BR-01.4`, `CFG-05-02` |
| **Có quyền [tên quyền]** | Chỉ dùng được khi vai trò được cấp đúng quyền chuyên biệt đó (Mở khóa mặt nạ, Xóa, Nhập dữ liệu, Xuất dữ liệu); không mặc định theo vai trò | — |
| **Cho phép** / **Toàn quyền** | Có quyền thực hiện; "Toàn quyền" gồm cả cấu hình và các ngoại lệ nêu trong ô | — |
| **+ đọc tự động (`BR-35.4`)** | Ngoài phạm vi thông thường, còn có quyền đọc có ghi nhật ký đối với khách đang có vé/hội thoại mở mà mình đang xử lý | `BR-35.4` |
| **—** | Không có quyền | — |

   Ô chỉ dùng từ vựng chuẩn thì không cần dẫn chiếu mã `BR`; phần điều kiện vượt ra ngoài từ vựng chuẩn trong một ô bắt buộc dẫn chiếu mã `BR` (Mục 2.4, Nguyên tắc 1).

2. **Tầm nhìn của Marketing:** "Xem toàn bộ" của Marketing khác "Của mình"/"Đơn vị của mình" của Kinh doanh — Marketing cần tầm nhìn toàn tổ chức để phân khúc chiến dịch, nhưng không sửa được trừ khi ô ghi rõ "Cho phép"/"Toàn quyền".

3. **Cột Tiến trình Hệ thống** ghi phần việc hệ thống thực hiện tự động, song song với thao tác của người dùng — không phải một cột loại trừ bảy cột còn lại. Với `FEAT-16` và `FEAT-31`, không vai trò nào thực thi bằng thao tác tay; các cột vai trò chỉ thể hiện quyền cấu hình hoặc xử lý kết quả. Quyền đổi mốc và tỷ lệ suy giảm nằm ở `CFG-16-01`, không nằm trong ma trận.

4. **Vai trò chức năng** (Quản lý Khách hàng Hiện hữu, Quản trị Chất lượng Dữ liệu, Người phụ trách Bảo vệ Dữ liệu) không có cột riêng vì không phải vai trò phân quyền độc lập; quyền hạn theo vai trò gốc được cấp (Mục 2.2).

5. **`FEAT-33`:** Nhân viên Hỗ trợ và Quản lý Kinh doanh được tiếp nhận, ghi nhận yêu cầu, và thực thi ngay hai loại yêu cầu **chỉ thu hẹp** phạm vi xử lý và **đảo lại được** — gắn Hạn chế xử lý và hạ đồng thuận — vì bảng loại yêu cầu tại `FEAT-33` cam kết thực thi "Tức thì"; nếu phải chờ Quản trị viên thì cam kết đó không có người thực thi. Đóng yêu cầu, phát hành phản hồi chính thức và ba loại yêu cầu còn lại (bản sao, chỉnh sửa, xóa vĩnh viễn) chỉ thuộc Quản trị viên và Chủ sở hữu, tuân thủ `BR-33.2`, `BR-33.7`.

6. **Quyền theo quan hệ với bản ghi** (Người phụ trách, thành viên Đội ngũ phụ trách, người đang xử lý vé/hội thoại, chính người dùng tự khai báo) là trục cộng thêm vào quyền theo vai trò, theo Nguyên tắc 2 tại Mục 2.4.

7. **Mặc định và sàn:** toàn bộ các ô là giá trị mặc định chuẩn hệ thống; tenant cấu hình lại được qua `CFG-05-02`, trừ các ràng buộc mang nhãn "sàn bắt buộc" trong các quy tắc nghiệp vụ.

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
7. **Kịch bản phụ (doanh nghiệp đã có Cơ hội đang mở — `BR-14.3`):** Một tuần sau, nhân viên khác chuyển đổi Lead "Nguyễn Văn Bình" cũng thuộc "Công ty CP Thực phẩm Vina", chọn cùng phễu với Cơ hội ở bước 2.
8. **Kỳ vọng:** Hộp thoại hiển thị Cơ hội "Hợp đồng Cung ứng Nông sản Q3" đang mở và **mặc định gắn Bình vào Cơ hội đó**, không tạo Cơ hội thứ hai. Dự báo doanh thu vẫn là 500 triệu. Bình lên Opportunity (`BR-14.4`).
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
4. **Kỳ vọng nếu chọn "Không có cơ sở đồng thuận" ở (c):** vẫn nhập được, nhưng toàn bộ lô Từ chối nhận tin cho nhóm Tiếp thị, và kết quả nêu rõ cảnh báo (`BR-30.4`).
5. **Kỳ vọng về cưỡng chế đồng thuận (`BR-30.10`):** ở một lô khác chọn chiến lược cập nhật, một dòng mang "Đồng ý" cho bản ghi đang Từ chối — bản ghi **giữ Từ chối** và dòng đó có ghi chú "Không nâng được mức đồng thuận — thiếu bằng chứng của chủ thể".
6. Bấm "Bắt đầu nhập khẩu".
7. **Kỳ vọng:** Tiến trình chạy nền, tiến độ đạt 100%. Kết quả: 9.850 dòng thành công, 150 dòng lỗi.
8. Quản trị viên tải tệp báo cáo lỗi.
9. **Kỳ vọng:** Mỗi dòng lỗi có nguyên nhân cụ thể thuộc danh sách tại `BR-24.2`; không gộp thành một nguyên nhân chung.
10. **Kỳ vọng bổ sung (`BR-15.7` (a)):** 9.850 bản ghi mới **không** thăng hạng MQL tự động trong 24 giờ đầu và **không** tính vào cam kết thời gian phản hồi cho tới khi có tương tác đầu tiên — kể cả bản ghi đủ điểm hồ sơ.

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
15. **Kỳ vọng:** (i) trường Số Căn cước công dân **không có** trong danh sách trường chọn được, và không có đường phê duyệt nào mở được. (ii) Lần xuất làm vượt hạn mức 2.000 bản ghi/ngày (`CFG-25-02`) bị giữ lại và chuyển thành yêu cầu phê duyệt gửi **Quản lý Kinh doanh** — không phải Quản lý Marketing. (iii) Sau phê duyệt, A xuất tiếp được nhưng tổng trong ngày **không vượt hai lần hạn mức**. Cả ba lần thao tác đều được ghi nhật ký (`BR-25.3`).

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

1. Một khách mới được tạo tự động từ biểu mẫu website với quốc gia Việt Nam, tỉnh/thành Đà Nẵng, không có người tạo trực tiếp.
2. Hệ thống áp thứ tự ưu tiên tại `BR-31.3b`: kiểm tra Người phụ trách hiện hữu (không khớp vì là khách mới), rồi quy tắc vùng địa lý.
3. **Kỳ vọng:** Có nhân viên phụ trách Miền Trung đang khả dụng → khách được gán trực tiếp cho nhân viên đó, không qua chia vòng.
4. **Kịch bản phụ (không khớp quy tắc nào):** Một khách khác từ trợ lý trò chuyện tự động không có thông tin quốc gia/tỉnh/ngành khớp quy tắc nào, và không còn nhân viên khả dụng trong nhóm.
5. **Kỳ vọng:** Khách vào hàng đợi "Chưa phân công"; Quản lý Kinh doanh nhận thông báo (`BR-31.4`).
6. **Kịch bản phụ (chống trùng chủ — `BR-31.6`):** Một khách mới từ biểu mẫu website có email trùng chị "Trần Thị Mai" do nhân viên A phụ trách.
7. **Kỳ vọng:** Không tạo bản ghi mới, không chia cho người khác; yêu cầu được ghi vào dòng thời gian của chị Mai; A nhận thông báo và một Yêu cầu chờ xử lý được tạo.

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
2. Quản trị viên thử vô hiệu hóa tài khoản A ngay.
3. **Kỳ vọng:** Bị **chặn**, yêu cầu chỉ định người nhận bàn giao (`BR-34.4`); nếu cần gấp, cho phép bàn giao tạm về Quản lý trực tiếp của A và đưa toàn bộ vào danh sách "Chờ bàn giao lại".
4. Quản lý Kinh doanh lọc "Người phụ trách là A", chọn chuyển cho B, phạm vi "kèm Cơ hội và Vé đang mở".
5. **Kỳ vọng:** Bước xem trước hiển thị đúng 450, 20, 8, 3 (`BR-34.2`). Sau xác nhận, toàn bộ sang B; B nhận thông báo; nhật ký ghi đủ danh sách bản ghi (`BR-34.7`).
6. **Kỳ vọng bổ sung:** Báo cáo "Bản ghi không có Người phụ trách hoạt động" (`BR-34.5`) không còn bản ghi nào của A.
7. **Kịch bản phụ (tự khai báo nghỉ phép — `BR-34.6`):** Lần lượt **bốn** người tự khai báo nghỉ phép 5 ngày kèm người xử lý thay: một Nhân viên Kinh doanh, một Nhân viên Hỗ trợ, một Nhân viên Marketing, một Quản lý Marketing. **Kỳ vọng:** cả bốn lưu thành công; trong khoảng nghỉ họ không nhận phân bổ mới (`BR-31.1`) và yêu cầu chờ xử lý chuyển cho người xử lý thay (`BR-31.6`), quyền phụ trách chính không đổi. **Đối chiếu:** Quản lý Kinh doanh khai báo được thay cho thành viên trong nhóm; Nhân viên Kinh doanh không khai báo được thay cho đồng nghiệp.
8. **Kịch bản phụ (không khả dụng tự động):** Nhân viên E đi công tác dài, **không đăng nhập 14 ngày liên tiếp**, không khai báo nghỉ phép.
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
7. **Kỳ vọng:** Quyền đọc của C tự hết hiệu lực (`BR-35.4` (d)). C thử hạ đồng thuận lần nữa — **bị từ chối**. C mở lại hồ sơ chị Mai — thấy **thông tin tối thiểu để nhận diện** (tên viết tắt, tên Người phụ trách, đơn vị phụ trách, thời điểm tương tác gần nhất) kèm **ba hành động** "Yêu cầu quyền truy cập", "Đề nghị chuyển giao", "Đề nghị gộp"; **không** có thông báo "không có quyền truy cập" (`BR-17.3`).
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
7. **Kỳ vọng:** Thành công vì còn trong thời hạn 90 ngày và bản ghi phụ chưa bị dọn dẹp (`BR-20.3`, `BR-05.6` (b)).

---

### Kịch bản 17: Yêu cầu Xóa Dữ liệu Cá nhân của Chủ thể Dữ liệu

1. Một người tự nhận là chị "Lê Thị Hồng" gửi email yêu cầu xóa toàn bộ dữ liệu cá nhân. Chị Hồng ở Customer và còn 1 hợp đồng hiệu lực.
2. Nhân viên Hỗ trợ tiếp nhận và ghi nhận yêu cầu.
3. **Kỳ vọng:** Nhân viên Hỗ trợ tạo được bản ghi theo dõi (loại "Xóa vĩnh viễn", hạn 30 ngày) nhưng **không** thực thi được thao tác xóa (Mục 5, ghi chú 5).
4. Quản trị viên mở yêu cầu và thử xóa ngay.
5. **Kỳ vọng:** Bị **chặn** cho tới khi ghi nhận phương thức xác minh danh tính (`BR-33.7`); vì chị Hồng là Customer, còn cần **hai người khác nhau** (người xác minh và người phê duyệt).
6. Sau khi xác minh qua email đã xác thực của chính hồ sơ, Quản trị viên tiếp tục xử lý.
7. **Kỳ vọng (từ chối một phần):** Vì còn hợp đồng hiệu lực, hệ thống **từ chối một phần** theo `BR-33.3`: xóa dữ liệu tiếp thị và kênh liên lạc không cần thiết, giữ dữ liệu tối thiểu phục vụ hợp đồng, bắt buộc ghi lý do từ chối một phần.
8. **Kỳ vọng về phạm vi xóa — kiểm chứng theo đúng 12 hàng của bảng `BR-33.8`:** hệ thống phát hành **Biên bản Hoàn tất Xử lý** liệt kê từng hàng kèm trạng thái và số lượng đối tượng đã xử lý. Người kiểm thử đối chiếu từng hàng:
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
10. **Kỳ vọng (`BR-01.5b` — sàn bắt buộc):** Các trường KYC **bị khử vĩnh viễn**, hồ sơ và dữ liệu kinh doanh **giữ nguyên**. Thử đặt `CFG-01-03` = **60 tháng** hoặc "vô hạn" — **bị từ chối cả hai**, miền chỉ nhận 6–24 tháng.
11. **Nhánh D — dữ liệu không hoạt động.** Một hồ sơ đã định danh không có tương tác trong **37 tháng** (`CFG-33-02` = 36 tháng).
12. **Kỳ vọng (`BR-33.5` — hành vi cố định):** Hồ sơ **không** bị tự xóa, chỉ vào **danh sách rà soát**. Không tồn tại lựa chọn "tự động xóa khi hết thời hạn rà soát" ở bất kỳ mức phân quyền nào, kể cả Chủ sở hữu.
13. **Kỳ vọng đối chiếu năm ngoại lệ (`BR-33.5`):** đúng **năm** ngoại lệ — (a) khử KYC ở bước 10; (b) xóa Hồ sơ Tạm chưa có nhân viên phản hồi ở bước 4; (c) khử định danh Hồ sơ Tạm chạm trần ở bước 8; (d) dọn Thùng rác quá hạn (kiểm tại Kịch bản 20, gồm các chốt an toàn `BR-05.6`); (e) tự xóa tệp nhập khẩu gốc hết thời hạn và tài liệu xác minh danh tính sau 30 ngày — tải một tệp nhập khẩu, dịch tới ngày thứ 31 và xác nhận không còn tải về được. **Không** có tiến trình tự động xóa nào khác.

---

### Kịch bản 22: Kiểm chứng Sàn bắt buộc của Toàn bộ Tham số Cấu hình

*Mục đích: chứng minh không ai — kể cả người có thẩm quyền cao nhất — đặt được giá trị vi phạm sàn của bất kỳ tham số nào. Cách chạy: với **mỗi tham số**, đăng nhập bằng **vai trò thấp nhất** khai ở cột "Thẩm quyền thay đổi" của Phụ lục B (chạy bằng Chủ sở hữu sẽ không phát hiện được lỗi phân quyền, vì Chủ sở hữu bao trùm mọi vai trò cấp dưới). Với **mọi dòng có dấu "+"**, chuẩn bị **cả hai tài khoản** vì dấu này luôn mang nghĩa "và" — kể cả khi hai vai trò ngang cấp (dòng `CFG-12-02` với "Quản lý Kinh doanh + Quản lý Marketing": chạy bằng cả hai). Nếu vai trò thứ hai không tồn tại trên tenant đang kiểm thử, dùng quy tắc thay thế người thứ hai (`NFR-14`) và ghi rõ vào biên bản. Tham số đã có bước kiểm chi tiết ở kịch bản khác được ghi rõ để không kiểm trùng.*

1. **Phép thử A — vượt miền:** nhập một giá trị **ngoài miền** khai tại Phụ lục B. **Kỳ vọng:** bị từ chối ngay tại màn hình nhập, nêu rõ miền hợp lệ; giá trị cũ không đổi.
2. **Phép thử B — "không giới hạn":** tìm trong giao diện một lựa chọn "không giới hạn", "vô hạn", "tắt kiểm soát" hoặc tương đương. **Kỳ vọng:** với mọi tham số mức **Có sàn bắt buộc**, lựa chọn đó **không tồn tại** — không hiển thị, chứ không phải bị từ chối sau khi chọn.
3. **Phép thử C — vi phạm nội dung sàn:** nhập giá trị **trong miền** nhưng vi phạm điều kiện ở cột "Mức độ tự do". **Kỳ vọng:** bị từ chối, nêu đúng quy tắc bị vi phạm kèm mã `BR`.
4. **Đối tượng kiểm — toàn bộ tham số mức "Có sàn bắt buộc":**

| Tham số | Nội dung sàn phải kiểm | Ghi chú |
| --- | --- | --- |
| `CFG-01-02` | Bật nhóm Định danh KYC mà **bỏ trống mục đích** → từ chối | — |
| `CFG-01-03` | Đặt 60 tháng hoặc "vô hạn" → từ chối (miền 6–24 tháng) | Đã kiểm tại Kịch bản 21 bước 10 |
| `CFG-04-01` | Đặt Nhóm 3 (KYC) lên "Che một phần" ở bất kỳ cột nào → từ chối. Đặt cột (B), (C) hoặc (D) lên "Đầy đủ" → từ chối. Đặt cột (D) lên "Che một phần" → từ chối (trần của cột D là "Che hoàn toàn"). Đặt cột (A) lên "Đầy đủ" cho Nhóm 1 hoặc Nhóm 2 → **chấp nhận**. Đặt cột (A) lên "Đầy đủ" cho Nhóm 3 → từ chối | Sàn ba tầng, chưa kiểm ở kịch bản khác |
| `CFG-04-02` | Tìm lựa chọn "không giới hạn" → không tồn tại | Đã kiểm tại Kịch bản 6 bước 9 |
| `CFG-04-03` | Cấu hình để hành động liên lạc **không ghi nhật ký** → không tồn tại lựa chọn đó (`NFR-07`) | Nội dung sàn là nghĩa vụ ghi nhật ký |
| `CFG-04-04` | Cấu hình để lượt thêm thành viên Đội ngũ phụ trách **không ghi nhật ký** → không tồn tại (`BR-35.6`) | Phần hạn mức đã kiểm tại Kịch bản 19 bước 5 |
| `CFG-05-01` | Đặt **90 ngày** trong khi `CFG-20-01` đang ở **90 ngày** → **chấp nhận** (điều kiện là "không cao hơn"). Kiểm chứng không tồn tại giá trị nào trong miền 30–90 vi phạm ràng buộc chéo | Ràng buộc chéo đã đóng bằng miền |
| `CFG-05-02` | Cấp quyền xuất Định danh KYC cho Marketing (`BR-25.4`, neo vào sàn `BR-01.5b`) → **từ chối**; cấp quyền đọc nhật ký kiểm toán cho Quản lý Kinh doanh (`NFR-14`, cố định) → **từ chối**. Đối chứng: cấp quyền chuyển giai đoạn thủ công cho Marketing (`BR-02.2`, **không** phải sàn) → **chấp nhận** | Hai ô bị chặn và một ô đối chứng được phép |
| `CFG-12-01` | Bật bước Customer → Lead → từ chối (`BR-12.3`). Hạ quyền loại khách xuống Nhân viên → từ chối (`BR-12.4`). Hạ quyền loại Customer/Evangelist/Churned xuống dưới Quản trị viên → từ chối (`BR-12.8`) | Ba sàn trong một tham số |
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
| `CFG-31-04` | Cấu hình để bằng chứng nhóm 2 **không cần Quản lý xác nhận** → không tồn tại (`BR-31.8`) | — |
| `CFG-33-01` | Đặt **200 ngày** → từ chối (miền 30–150). Đặt **150 ngày** trong khi `CFG-33-03` ở **6 tháng = 180 ngày** → **chấp nhận** | Điểm cực biên đã kiểm tại Kịch bản 21 bước 4 |
| `CFG-33-02` | Bật "tự động xóa khi hết thời hạn rà soát" → không tồn tại (`BR-33.5`) | Đã kiểm tại Kịch bản 21 bước 12 |
| `CFG-33-03` | Đặt 36 tháng hoặc "vô hạn" → từ chối (miền 6–18 tháng) | Đã kiểm tại Kịch bản 21 bước 8 |
| `CFG-33-04` | Tìm lựa chọn "vô hạn" → không tồn tại; đặt 60 ngày → từ chối (miền 7–30 ngày) | — |
| `CFG-36-02` | Cấu hình cho phép **xóa cứng** ghi chú sau khi hết thời hạn sửa → không tồn tại (`BR-36.3`) | Nội dung sàn là cấm xóa cứng |
| `CFG-36-03` | Tìm lựa chọn mở phạm vi "Nội bộ đội bán hàng" theo vai trò cho Marketing hoặc tuyến Hỗ trợ → không tồn tại (`BR-36.1`) | — |

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

**7.6 Danh mục vai trò liên hệ trên Cơ hội: cố định hay do tenant định nghĩa.** Tài liệu này dùng danh mục cố định năm giá trị (A.4) và một thứ tự ưu tiên cố định khi gộp (`BR-19.4`), trong khi [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) (`BR-22.1` của tài liệu đó) và [`CONTEXT.md`](../CONTEXT.md) quy định danh mục **do từng không gian làm việc tự định nghĩa** và dùng chung với hồ sơ khách hàng. Hai tài liệu đang nói khác nhau về cùng một danh mục. Nếu danh mục do tenant định nghĩa, `BR-19.4` cần một cách xác định thứ tự ưu tiên khi gộp (ví dụ tenant sắp thứ tự trong danh mục, hoặc bắt buộc người gộp chọn khi xung đột). Cần quyết định: Product Owner của hai phân hệ.

**7.7 Chính sách xử lý trùng lặp doanh nghiệp.** `BR-06.2` dùng mã số thuế **hoặc** tên miền website làm căn cứ nhận diện trùng, nhưng chưa quy định căn cứ nào là "chắc chắn" (được chặn tạo mới như `BR-17.2`) và căn cứ nào chỉ để cảnh báo. Tên miền website thường dùng chung giữa công ty mẹ và các công ty con, nên nếu coi là căn cứ chắc chắn thì sẽ chặn chính việc tạo công ty con cần cho `FEAT-07`. Hướng có thể: mã số thuế là căn cứ chắc chắn, tên miền chỉ cảnh báo. Cần quyết định: Product Owner cùng Quản trị Chất lượng Dữ liệu.

**7.8 Đơn vị tổ chức của khách hàng khi đổi Người phụ trách.** `BR-01.3` gán Đơn vị tổ chức theo **người tạo**, trong khi [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) (Nguyên tắc 4 và `BR-01.2` của tài liệu đó) quy định Đơn vị tổ chức của Cơ hội **đi theo Người phụ trách hiện tại**. Tài liệu này chưa quy định khi chuyển giao (`FEAT-34`) thì Đơn vị tổ chức của khách hàng có đổi theo người nhận hay không; nếu không đổi, Quản lý của người nhận không thấy khách hàng trong "Đơn vị của mình". Cần quyết định: Product Owner cùng chủ tài liệu [`iam-tenant-authorization.md`](./iam-tenant-authorization.md).

**7.9 Giai đoạn Opportunity khi Cơ hội mở duy nhất biến mất mà không đóng Thua.** `BR-12.3` chỉ quy định trường hợp **toàn bộ Cơ hội Thua**. Chưa có quy tắc cho các trường hợp Cơ hội mở duy nhất không còn gắn với khách mà không qua đóng Thua: Cơ hội bị xóa mềm, được chuyển sang khách hàng khác, khách bị gỡ khỏi Cơ hội. Khi đó khách đứng ở Opportunity trong khi định nghĩa giai đoạn là "đang có ít nhất một Cơ hội mở". Hướng có thể: xử lý như `BR-12.3` (sang Nurturing kèm lý do), hoặc đưa vào danh sách rà soát cho Quản lý. Cần quyết định: Product Owner cùng Quản lý Kinh doanh.

**7.10 Hoàn tác Chuyển đổi ở nhánh gắn vào Cơ hội sẵn có.** `BR-14.2` chỉ đặc tả nhánh tạo Cơ hội mới (xóa mềm Cơ hội vừa tạo, điều kiện "Cơ hội chưa có hoạt động"). Khi chuyển đổi đã gắn Liên hệ vào một Cơ hội đang mở sẵn có (`BR-14.3`), Cơ hội đó thuộc một thương vụ có trước và không được xóa, nên chưa rõ hoàn tác sẽ làm gì với liên kết giữa Liên hệ và Cơ hội, và điều kiện "chưa có hoạt động" áp lên cái gì. Liên quan tới `7.9` và tới nhu cầu tách lại cơ hội sau khi gộp tự động tại Mục 7 của [`deals-pipeline-srs.md`](./deals-pipeline-srs.md). Cần quyết định: Product Owner của hai phân hệ.

**7.11 Tái phân loại Cơ hội Thắng thành Thua và giai đoạn Customer.** [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) (`FEAT-21` của tài liệu đó) cho phép tái phân loại một Cơ hội đã Thắng thành Thua. Nếu đó là Cơ hội Thắng duy nhất đã đưa khách lên Customer, nguyên tắc 1 (giai đoạn phản ánh thực tế — khách chưa từng mua) và nguyên tắc 4 (Customer được bảo vệ tuyệt đối) cho hai kết luận ngược nhau. Cần quyết định: Product Owner cùng Quản lý Kinh doanh.

**7.12 Ràng buộc toàn vẹn tối thiểu của ma trận chuyển đổi cấu hình được.** `CFG-12-01` cho tenant bật/tắt từng bước chuyển, với ba sàn hiện có (không hạ Customer/Evangelist, không nới quyền loại khách, không nới quyền loại vì gian lận) và nguyên tắc 1 luôn thắng. Chưa quy định tenant có được tắt các bước chuyển tự động khác — thăng MQL theo ngưỡng điểm (`BR-15.5`), sang Nurturing khi mọi Cơ hội Thua (`BR-12.3`) — hay tắt mọi lối ra khỏi Nurturing/Disqualified hay không. Nếu được, một cấu hình có thể khiến hồ sơ mắc kẹt vĩnh viễn ở một giai đoạn. Cần quyết định: Product Owner.

**7.13 Hoàn tác gộp và trạng thái đồng thuận.** `BR-20.2` khôi phục bản ghi phụ theo ảnh chụp tại thời điểm gộp. Nếu bản ghi phụ khi đó Đồng ý nhận tin, khôi phục nguyên ảnh chụp sẽ **nâng** đồng thuận mà không có hành vi mới của chủ thể — trái `BR-30.10`. Ngoài ra chưa quy định một lượt Từ chối nhận tin mà khách thực hiện **sau** khi gộp (ghi trên Bản ghi Chính) được áp cho bản ghi nào khi hoàn tác. Hướng có thể: khi hoàn tác, đồng thuận của cả hai bản ghi lấy trạng thái nghiêm ngặt nhất giữa ảnh chụp và hiện trạng. Cần quyết định: Người phụ trách Bảo vệ Dữ liệu cùng Product Owner.

**7.14 Đồng thuận khi biện pháp phòng ngừa hết hạn.** `BR-33.7` (a) quy định hết 30 ngày thì biện pháp phòng ngừa tự động được dỡ. Biện pháp gồm hai phần: Hạn chế xử lý và chuyển các kênh sang Từ chối nhận tin. Chưa rõ khi dỡ, các kênh có trở lại trạng thái Đồng ý trước đó hay không; nếu có, đó là một lượt nâng đồng thuận không có hành vi của chủ thể (trái `BR-30.10`); nếu không, một yêu cầu mạo danh vẫn rút vĩnh viễn khách khỏi thư tiếp thị — đúng rủi ro mà ràng buộc (a) muốn tránh. Cần quyết định: Người phụ trách Bảo vệ Dữ liệu cùng Pháp chế.

**7.15 Suy giảm điểm khi khách im lặng kéo dài sau mốc thứ hai.** `BR-16.1`, `BR-16.2` quy định hai mốc (14 ngày và 30 ngày), mỗi mốc áp một lần trong một khoảng không tương tác liên tục. Chưa quy định khi khách tiếp tục im lặng sau mốc 30 ngày (ví dụ 90, 180 ngày) thì Điểm Tương tác tiếp tục giảm hay giữ nguyên. Nếu giữ nguyên, một khách im lặng một năm vẫn giữ khoảng 2/3 Điểm Tương tác cũ. Cần quyết định: Quản lý Marketing.

**7.16 Xuất dữ liệu phục vụ chiến dịch ngoài hệ thống của Marketing.** Mục 2.2 nêu Marketing xuất danh sách khách hàng phục vụ chiến dịch, trong khi `NFR-06` và `BR-25.1` bắt buộc tệp xuất tuân theo mức che của người xuất — với Marketing là cột (B), tức kênh liên lạc che một phần. Tệp xuất vì vậy dùng được cho phân tích nhưng không dùng được để gửi chiến dịch ngoài hệ thống. Chưa chốt: đây là hệ quả có chủ đích (mọi lượt gửi phải qua hệ thống để chịu `BR-30.5`), hay cần một đường xuất có kiểm soát riêng. Cần quyết định: Quản lý Marketing cùng Người phụ trách Bảo vệ Dữ liệu.

**7.17 Cấu trúc cây doanh nghiệp khi Doanh nghiệp mẹ bị xóa.** `FEAT-07` và `FEAT-09` chưa quy định khi một doanh nghiệp ở giữa cây (có mẹ và có con) bị xóa mềm hoặc xóa vĩnh viễn thì các công ty con được nối lên cấp trên, tạm tách khỏi cây, hay chặn xóa cho tới khi xử lý cấu trúc; và báo cáo hợp nhất tập đoàn tính thế nào trong thời gian đó. Cần quyết định: Product Owner.

---

## 8. Phụ lục A — Danh mục Dữ liệu Chuẩn

> Các danh mục dưới đây là **giá trị nghiệp vụ hiển thị cho người dùng**, bắt buộc chọn từ danh sách (không nhập tự do) để bảo đảm thống kê và báo cáo được. Đây không phải thiết kế dữ liệu; cách tổ chức lưu trữ thuộc thẩm quyền đội phát triển.

**A.1 Lý do Loại khách hàng** — `BR-12.4`, `BR-12.4b`, `BR-12.8`. Hai nhóm có nhãn, vì thẩm quyền sử dụng khác nhau:

- **Nhóm Thương mại** (5 giá trị): Sai ngành/không thuộc tập khách hàng mục tiêu · Không đủ ngân sách · Không có nhu cầu thực · Đã là khách hàng của đối thủ với hợp đồng dài hạn · Ngoài vùng phục vụ.
- **Nhóm Gian lận & Dữ liệu không hợp lệ** (3 giá trị, gọi tắt "nhóm Gian lận"): Thông tin giả/Spam/Lừa đảo · Trùng lặp với bản ghi khác · Không thuộc đối tượng đủ điều kiện pháp lý.

*Ai dùng nhóm nào:* `BR-12.4` (Quản lý Kinh doanh trở lên, giai đoạn tiền bán hàng) dùng được **cả 8 giá trị**. `BR-12.4b` (Nhân viên đánh dấu nhanh) chỉ dùng **2 giá trị** "Thông tin giả/Spam/Lừa đảo" và "Trùng lặp với bản ghi khác" — giá trị thứ ba của nhóm Gian lận đòi đánh giá pháp lý vượt thẩm quyền nhân viên. `BR-12.8` (Quản trị viên/Chủ sở hữu, với Customer/Evangelist/Churned) dùng **cả 3 giá trị** nhóm Gian lận; nhóm Thương mại không dùng được cho ba giai đoạn này.

**A.2 Lý do Không chuyển đổi** — `BR-12.3`: Hết ngân sách kỳ này · Chờ phê duyệt nội bộ · Chưa đúng thời điểm/hoãn sang kỳ sau · Thua đối thủ về giá · Thua đối thủ về tính năng · Thiếu người ra quyết định · Dự án bị tạm dừng · Không phản hồi sau nhiều lần liên hệ.

**A.3 Lý do Hạ hạng Giai đoạn** — `BR-12.7`: Thẩm định lại không đủ điều kiện · Thông tin ban đầu không chính xác · Khách hàng thay đổi nhu cầu · Điểm tiềm năng không phản ánh thực tế · Sai sót nhập liệu.

**A.4 Vai trò Liên hệ trên Cơ hội** — `BR-19.4`: Người ra quyết định · Người ủng hộ nội bộ · Người thẩm định kỹ thuật · Người ảnh hưởng · Người thực hiện mua hàng. *Thứ tự ưu tiên khi gộp theo đúng trình tự liệt kê. Xem Mục 7.6.*

**A.5 Vai trò Liên kết Doanh nghiệp** — `BR-10.1`: Chính · Phụ · Cố vấn · Cổ đông · Đại diện pháp luật. *"Đã nghỉ việc" không phải vai trò mà là một Trạng thái liên kết (A.5b).*

**A.5b Trạng thái Liên kết Doanh nghiệp** — `BR-10.1`, `BR-09.1`: Đang công tác · Đã nghỉ việc · Tạm ngưng (doanh nghiệp liên kết đang nằm trong Thùng rác theo `BR-09.1` (a)).

**A.6 Loại Quan hệ Cá nhân** — `BR-11.1`: Quản lý trực tiếp / Cấp dưới · Người giới thiệu / Được giới thiệu · Thành viên gia đình · Đối tác kinh doanh · Trợ lý / Người đại diện.

**A.7 Kênh Nguồn gốc Khách hàng** — `BR-32.1`: Website · Quảng cáo Facebook · Quảng cáo Google · Zalo · Giới thiệu · Sự kiện/Hội thảo · Tiếp cận chủ động · Nhập khẩu từ tệp · Đối tác · Không xác định.

**A.8 Nguồn thu thập Đồng thuận** — `BR-30.3`: Biểu mẫu đăng ký trên website · Hộp thoại đồng ý trên trò chuyện trực tuyến · Phiếu đồng ý tại sự kiện · Nhập khẩu từ tệp có khai báo cơ sở · Ghi nhận thủ công bởi nhân viên · Liên kết Hủy nhận tin trong email · Yêu cầu trực tiếp của khách hàng · **Yêu cầu chưa xác minh được danh tính** (dùng cho lượt hạ mức đồng thuận do biện pháp phòng ngừa tại `BR-33.7` (d)).

**A.9 Giai đoạn Vòng đời** — `FEAT-12`: 10 giá trị và các bước chuyển hợp lệ tại Ma trận Chuyển đổi Giai đoạn, `FEAT-12`.

**A.10 Trạng thái Tiếp cận** — `BR-29.2`, `BR-10.4`: Đã xác thực · Không tiếp cận được (trạng thái kỹ thuật: email hỏng, số không tồn tại) · Chưa kiểm tra · **Không còn hiệu lực** (lý do nghiệp vụ: khách đã rời doanh nghiệp sở hữu địa chỉ, theo `BR-10.4`; đảo lại được, khác "Không tiếp cận được").

**A.11 Cơ sở Đồng thuận cho lô Nhập khẩu** — `BR-30.4`: Khách hàng đã đăng ký trực tiếp · Dữ liệu từ sự kiện có phiếu đồng ý · Quan hệ hợp đồng hiện hữu · Không có cơ sở đồng thuận (**không phải giá trị mặc định** — người nhập bắt buộc tự chọn; khi chọn, toàn bộ lô Từ chối nhận tin cho nhóm Tiếp thị).

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

**Quy ước thẩm quyền:** cột "Thẩm quyền thay đổi" nêu **vai trò thấp nhất** được đổi tham số. **Đây là một trục quyền riêng, độc lập với ma trận Mục 5:** ma trận quy định ai dùng được **tính năng** trên dữ liệu nghiệp vụ; cột này quy định ai đổi được **tham số cấu hình** của tính năng đó — một Quản lý Kinh doanh chỉ có "Đơn vị của mình" trên dữ liệu vẫn có thể đặt các mốc thời hạn phản hồi cho cả tenant, vì đó là quyết định nghiệp vụ thuộc chuyên môn của họ. **Chủ sở hữu và Quản trị viên bao trùm mọi vai trò cấp dưới** — đổi được mọi tham số mà một vai trò nghiệp vụ đổi được. Dấu **"+"** luôn có nghĩa **"và"**: mọi vai trò nối bằng "+" phải cùng phê duyệt một lượt thay đổi. "+ Người phụ trách Bảo vệ Dữ liệu" và "+ Quản trị Chất lượng Dữ liệu" là **người phê duyệt thứ hai bắt buộc** — thiếu thì không thực hiện được, kể cả bởi Chủ sở hữu. Khi vai trò thứ hai là Quản trị viên hoặc Chủ sở hữu, dấu "+" chỉ mang tính liệt kê, vai trò đứng trước tự thực hiện được.

**Khi vai trò thứ hai không tồn tại hoặc trùng người:** áp quy tắc thay thế người thứ hai tại `NFR-14` — người thứ hai là **một Quản trị viên khác** với người thực hiện. Yêu cầu bất biến là **luôn có hai người khác nhau đứng tên**, không phải hai chức danh cụ thể; nếu không, một tenant chưa chỉ định Người phụ trách Bảo vệ Dữ liệu sẽ không đổi được các tham số có sàn pháp lý.

**Ba mức độ tự do:** **Tự do** — đặt giá trị bất kỳ trong miền. **Có sàn bắt buộc** — điều chỉnh được nhưng không nới lỏng dưới ngưỡng an toàn/pháp lý. **Cố định** — không cấu hình được, vì liên quan nghĩa vụ pháp lý hoặc toàn vẹn dữ liệu.

*Viết tắt trong bảng:* "BVDL" = Người phụ trách Bảo vệ Dữ liệu; "QTCLDL" = Quản trị Chất lượng Dữ liệu.

| Mã tham số | Quy tắc liên quan | Nội dung cấu hình | Mặc định chuẩn hệ thống | Miền giá trị | Thẩm quyền thay đổi | Mức độ tự do |
| --- | --- | --- | --- | --- | --- | --- |
| `CFG-01-01` | `BR-01.6` | Loại khách hàng mặc định khi tạo mới | Khách hàng Doanh nghiệp (B2B) | B2B / B2C / Bắt buộc người dùng chọn | Chủ sở hữu | Tự do |
| `CFG-01-02` | `BR-01.5b` | Bật/tắt nhóm trường Định danh KYC và mục đích sử dụng | **Tắt** | Bật (kèm khai báo mục đích) / Tắt | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — bật thì bắt buộc khai báo mục đích |
| `CFG-01-03` | `BR-01.5b` | Thời hạn lưu nhóm Định danh KYC sau khi hợp đồng gần nhất kết thúc | 24 tháng | 6 – 24 tháng | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — hết hạn buộc tự khử định danh, không có lựa chọn "vô hạn" |
| `CFG-04-01` | `BR-04.3` | Chính sách che mặt nạ theo nhóm trường và quan hệ với bản ghi | Đúng bảng tại `BR-04.3` | Ma trận 3 nhóm trường × 4 cột quan hệ (A)(B)(C)(D), mỗi ô một trong 4 mức hiển thị tại `BR-04.2` | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — ba sàn: **(i)** Nhóm 3 (KYC) chỉ ở mức Che hoàn toàn hoặc Ẩn trường ở **cả bốn** cột, chỉ mở được bằng quyền chuyên biệt theo `BR-04.4`, có nhật ký và chịu hạn mức `CFG-04-02`; **(ii)** cột (D) không cao hơn Che hoàn toàn cho bất kỳ nhóm nào; **(iii)** cột (B) và cột (C) không cao hơn Che một phần. Hệ quả: cột (A) là cột duy nhất nhận được mức Đầy đủ, và cột (A) đã chịu kiểm soát riêng tại `BR-04.5b` |
| `CFG-04-02` | `BR-04.5` | Hạn mức mở khóa mặt nạ mỗi người mỗi ngày | 50 bản ghi/ngày | 10 – 200 | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — không có lựa chọn "không giới hạn"; trần tuyệt đối 200 |
| `CFG-04-03` | `BR-04.6` | Hạn mức hành động liên lạc trong hệ thống mỗi người mỗi ngày | 200 lượt/ngày | 50 – 1.000 | Chủ sở hữu | **Có sàn bắt buộc** — mọi lượt liên lạc luôn ghi nhật ký (`NFR-07`) |
| `CFG-04-04` | `BR-04.5b` | Số bản ghi tối đa một người được thêm vào Đội ngũ phụ trách ở mức Chỉnh sửa mỗi tháng | 100 bản ghi/tháng | 20 – 500 | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — mọi lượt thêm thành viên luôn ghi nhật ký (`BR-35.6`) |
| `CFG-05-01` | `BR-05.4` | Thời hạn lưu bản ghi trong Thùng rác trước khi xóa vĩnh viễn | 30 ngày | 30 – 90 ngày (giới hạn trên theo gói dịch vụ) | Chủ sở hữu | **Có sàn bắt buộc** — không được cao hơn `CFG-20-01` |
| `CFG-05-02` | Mục 5 | Ma trận phân quyền theo vai trò, gồm phạm vi dữ liệu và quyền nhập/xuất của Marketing | Đúng ma trận Mục 5 | Từng ô điều chỉnh được | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — không được nới lỏng các ràng buộc có "sàn bắt buộc" trong các quy tắc |
| `CFG-12-01` | `FEAT-12` | Ma trận Chuyển đổi Giai đoạn — các bước chuyển được phép | Đúng ma trận tại `FEAT-12` | Từng bước chuyển bật/tắt | Quản lý Kinh doanh | **Có sàn bắt buộc** — ba sàn: không cho hạ Customer/Evangelist về giai đoạn tiền bán hàng (`BR-12.3`, nguyên tắc 4); không nới quyền loại khách dưới Quản lý Kinh doanh (`BR-12.4`); không nới quyền loại Customer/Evangelist/Churned vì gian lận dưới Quản trị viên (`BR-12.8`) |
| `CFG-12-02` | `BR-12.10` | Giai đoạn mặc định theo từng nguồn tạo | Thủ công/Biểu mẫu website/Hội thoại/Nhập khẩu → Lead; Đăng ký bản tin → Subscriber; Hồ sơ Tạm → chưa gán | "Chưa gán giai đoạn" (chỉ cho nguồn Hồ sơ Tạm), hoặc một trong sáu giai đoạn tiền bán hàng | Quản lý Kinh doanh + Quản lý Marketing | **Có sàn bắt buộc** — không được đặt Customer, Evangelist, Churned hay Disqualified: một bản ghi vừa tạo chưa thể đã trả tiền hay đã rời bỏ |
| `CFG-14-01` | `BR-14.2` | Thời hạn được Hoàn tác Chuyển đổi | 24 giờ | 1 – 168 giờ | Quản lý Kinh doanh | Tự do |
| `CFG-15-01` | `BR-15.5` | Bộ ngưỡng điểm MQL / SQL / Ưu tiên cao | 40 / 70 / 85, kèm Điểm Tương tác ≥ 15 cho MQL | 0 – 100 mỗi ngưỡng, tăng dần | Quản lý Marketing | **Có sàn bắt buộc** — điều kiện Điểm Tương tác tối thiểu không được đặt về 0 (`BR-15.7`) |
| `CFG-15-02` | `BR-15.7` | Hoãn thăng hạng cho dữ liệu nhập khẩu; chống thông báo lặp; độ trễ đánh giá lại | 24 giờ / 1 lần trong 30 ngày / 7 ngày | 12 – 168 giờ; 1 – 90 ngày; 0 – 30 ngày | Quản lý Marketing | **Có sàn bắt buộc** — hoãn thăng hạng không dưới 12 giờ (`BR-15.7` (a)) |
| `CFG-16-01` | `BR-16.1`, `BR-16.2` | Các mốc ngày không tương tác và tỷ lệ suy giảm | 14 ngày −10%; 30 ngày −25% | 7 – 90 ngày mỗi mốc, mốc thứ hai luôn lớn hơn mốc thứ nhất; 0 – 50% mỗi tỷ lệ | Quản lý Marketing | **Có sàn bắt buộc** — hai mốc không được bằng nhau hay đảo thứ tự |
| `CFG-17-01` | `BR-17.2` | Chính sách khi phát hiện trùng theo Tiêu chí chắc chắn | Chặn tạo bản ghi mới | Chặn / Cảnh báo và vẫn cho lưu (có nhật ký) | Chủ sở hữu + QTCLDL | **Có sàn bắt buộc** — Tiêu chí tham khảo không bao giờ được dùng để chặn hoặc gộp tự động (`BR-17.1`) |
| `CFG-19-01` | `BR-19.9` | Cách xác định Người phụ trách sau gộp | Người phụ trách của bản ghi có tương tác gần nhất | Tương tác gần nhất / Bản ghi Chính / Bắt buộc chỉ định thủ công | Quản lý Kinh doanh | Tự do |
| `CFG-20-01` | `BR-20.3` | Thời hạn được hoàn tác gộp | 90 ngày | 90 – 365 ngày | Chủ sở hữu | **Có sàn bắt buộc** — lớn hơn hoặc bằng `CFG-05-01`; miền bắt đầu từ 90 ngày để mọi tổ hợp với miền của `CFG-05-01` đều hợp lệ |
| `CFG-22-01` | `BR-33.8` | Thời hạn lưu tệp nhập khẩu gốc sau khi nhập xong | 30 ngày | 7 – 30 ngày | Quản trị viên + BVDL | **Có sàn bắt buộc** — trần 30 ngày tuyệt đối; hết hạn buộc tự xóa |
| `CFG-22-02` | `BR-22.2` | Dung lượng tệp nhập khẩu tối đa mỗi lần | 50 MB | 10 – 200 MB (theo gói dịch vụ) | Chủ sở hữu | Tự do |
| `CFG-25-01` | `BR-25.4` | Ngưỡng số bản ghi mỗi lần xuất cần phê duyệt trước | 5.000 bản ghi | 5.000 – 50.000 | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — lần xuất chứa trường KYC luôn cần phê duyệt bất kể số lượng |
| `CFG-25-02` | `BR-25.5`, `BR-25.4` | Hạn mức xuất dữ liệu của Nhân viên Kinh doanh mỗi người mỗi ngày | 2.000 bản ghi/ngày | 200 – 5.000 (không cao hơn giá trị nhỏ nhất của `CFG-25-01`, nên mọi tổ hợp đều hợp lệ) | Chủ sở hữu | **Có sàn bắt buộc** — vai trò này không bao giờ xuất được trường KYC; mọi lần xuất luôn ghi nhật ký (`BR-25.3`) |
| `CFG-30-01` | `BR-30.5` | Nhóm Liên lạc 1-1 có chịu chi phối của Từ chối nhận tin hay không | Không chịu, nhưng bắt buộc ghi nhật ký | Có / Không | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — nhóm Tiếp thị luôn chịu chi phối, không cấu hình được |
| `CFG-30-02` | `BR-30.7` | Số người nhận tối đa của một lượt gửi nhóm Liên lạc 1-1 | 5 | 1 – 10 | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — trần tuyệt đối 10; trên mức đó là gửi hàng loạt và buộc thuộc nhóm Tiếp thị |
| `CFG-31-01` | `BR-31.7`, `BR-31.8`, `BR-17.2c`, `BR-35.3b` | Các mốc thời hạn phản hồi theo mức ưu tiên; số lần thu hồi tự động tối đa | 1 / 4 / 24 giờ làm việc; tối đa 2 lần | 0,5 – 72 giờ; 0 – 5 lần | Quản lý Kinh doanh | **Có sàn bắt buộc** — ghi chú tay đơn thuần không bao giờ được tính là bằng chứng liên hệ (`BR-31.8`) |
| `CFG-31-02` | `BR-31.3b` | Thứ tự ưu tiên giữa các quy tắc phân bổ | Người phụ trách hiện hữu → Vùng địa lý → Ngành nghề → Chia vòng lần lượt → Chưa phân công | Sắp xếp lại các vị trí (2) đến (4) | Quản lý Kinh doanh + Quản trị viên | **Có sàn bắt buộc** — Người phụ trách hiện hữu luôn ở vị trí số 1 (`BR-31.6`) |
| `CFG-31-03` | `BR-31.7b` | Lịch làm việc: múi giờ, ngày làm việc, giờ bắt đầu/kết thúc, ngày lễ | Thứ Hai – Thứ Sáu 08:00 – 17:30, không ngày lễ | Tự khai báo; tối thiểu một ngày làm việc trong tuần, giờ kết thúc sau giờ bắt đầu | Chủ sở hữu | Tự do |
| `CFG-31-04` | `BR-31.8` | Số lần dùng bằng chứng liên hệ nhóm 2 mỗi người mỗi tháng | 10 lần/tháng | 0 – 50 | Quản lý Kinh doanh | **Có sàn bắt buộc** — luôn cần Quản lý xác nhận và luôn thống kê riêng trong `KPI-03` |
| `CFG-33-01` | `BR-33.6` | Thời hạn dọn dẹp Hồ sơ Tạm không có tương tác | 90 ngày kể từ tương tác gần nhất | 30 – 150 ngày | Quản trị viên + BVDL | **Có sàn bắt buộc** — phải nhỏ hơn `CFG-33-03` ít nhất 30 ngày (quy đổi 1 tháng = 30 ngày, Mục 2.3); miền dừng ở 150 ngày để mọi tổ hợp với giá trị nhỏ nhất 180 ngày của `CFG-33-03` đều hợp lệ |
| `CFG-33-02` | `BR-33.5` | Thời hạn rà soát dữ liệu khách hàng không hoạt động | 36 tháng | 12 – 84 tháng | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — thời hạn cấu hình được, nhưng hành vi "không tự động xóa" là cố định |
| `CFG-33-03` | `BR-33.6` | Trần lưu tuyệt đối cho Hồ sơ Tạm đã có tương tác của nhân viên | 18 tháng kể từ tương tác gần nhất | 6 – 18 tháng | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — hết trần buộc tự khử định danh, không có lựa chọn "vô hạn" |
| `CFG-33-04` | `BR-33.7` | Thời hạn tối đa của biện pháp phòng ngừa khi không xác minh được chủ thể | 30 ngày | 7 – 30 ngày | Quản trị viên + BVDL | **Có sàn bắt buộc** — không có lựa chọn "vô hạn"; hết hạn buộc tự dỡ và bắt buộc thông báo Người phụ trách |
| `CFG-34-01` | `BR-34.3` | Phạm vi thực thể con mặc định khi chuyển giao | Kèm Cơ hội và Vé hỗ trợ đang mở | Chỉ bản ghi / Kèm thực thể đang mở / Kèm toàn bộ | Quản lý Kinh doanh | Tự do |
| `CFG-36-01` | `BR-36.1` | Phạm vi đọc mặc định của ghi chú mới | Nội bộ đội bán hàng | Nội bộ đội bán hàng / Chung / Giới hạn | Chủ sở hữu | Tự do |
| `CFG-36-02` | `BR-36.2` | Thời hạn người tạo được sửa ghi chú | 24 giờ | 0 – 168 giờ | Chủ sở hữu | **Có sàn bắt buộc** — hết thời hạn chỉ được bổ sung; không bao giờ được xóa cứng (`BR-36.3`) |
| `CFG-36-03` | `BR-36.1`, `BR-36.7` | Phạm vi đọc ghi chú theo vai trò của Marketing và của tuyến Hỗ trợ | Marketing: chỉ "Chung". Tuyến Hỗ trợ: "Chung" + ghi chú ghim đã được đánh dấu cho phép | Chỉ "Chung" / "Chung" + ghi chú ghim được phép | Chủ sở hữu + BVDL | **Có sàn bắt buộc** — không có lựa chọn mở phạm vi "Nội bộ đội bán hàng" **theo vai trò** cho Marketing hay tuyến Hỗ trợ. Sàn chặn theo vai trò, không chặn theo tư cách thành viên đội ngũ: Nhân viên Hỗ trợ là thành viên Đội ngũ phụ trách của một bản ghi vẫn đọc được ghi chú nội bộ của riêng bản ghi đó (`BR-36.1`) — đó là quyền trên từng bản ghi, có nhật ký và thu hồi được |
| — | `BR-19.6`, `BR-19.8`, `BR-30.5` (nhóm Tiếp thị), `BR-30.9`, `BR-30.10`, `BR-33.7` (nghĩa vụ xác minh và quy tắc hai người — không gồm thời hạn biện pháp phòng ngừa, cấu hình qua `CFG-33-04`), `BR-33.8` (phạm vi xóa và các sàn liên tài liệu — không gồm thời hạn lưu tệp nhập khẩu gốc, cấu hình qua `CFG-22-01`), `BR-34.4`, `BR-36.3`, `NFR-07` (giới hạn nội dung nhật ký), `NFR-14` | Bảo vệ đồng thuận tiếp thị, cưỡng chế đồng thuận trên mọi nguồn, giai đoạn khách đang trả tiền, xác minh chủ thể dữ liệu, phạm vi xóa, chốt bàn giao, chống xóa cứng ghi chú, giới hạn nội dung và quyền đọc nhật ký | Theo đúng quy tắc | — | — | **Cố định** — không cấu hình được ở mọi mức |

*Ghi chú phân loại:* cụm "sàn bắt buộc" trong tên một quy tắc ở thân tài liệu nghĩa là **quy tắc có một phần không được nới lỏng**; nó không tự quyết định mức độ tự do ở bảng này. Quy tắc có phần điều chỉnh được thì xuất hiện ở một dòng tham số với mức "Có sàn bắt buộc"; quy tắc **toàn bộ** không có gì để điều chỉnh thì nằm ở hàng cuối với mức "Cố định". Ba quy tắc hỗn hợp có mặt ở cả hai: `BR-33.7` (nghĩa vụ xác minh cố định · thời hạn biện pháp phòng ngừa qua `CFG-33-04`), `BR-33.8` (phạm vi xóa cố định · thời hạn lưu tệp gốc qua `CFG-22-01`), `BR-30.5` (nhóm Tiếp thị luôn chịu chi phối · nhóm Liên lạc 1-1 cấu hình qua `CFG-30-01`).

**Quản trị cấu hình:** mọi thay đổi tham số được ghi nhật ký (`NFR-07`) gồm người đổi, giá trị trước và sau, thời điểm. Thay đổi chỉ áp dụng **từ thời điểm đổi trở đi**, không hồi tố dữ liệu đã xử lý. Tham số mức "Có sàn bắt buộc" hiển thị rõ ngưỡng sàn trên màn hình cấu hình, và hệ thống từ chối giá trị vi phạm sàn ngay tại màn hình.

**Hằng số vận hành cấp hệ thống (không cấu hình theo tenant):** thời hạn hiệu lực 24 giờ của đường tải tệp xuất và tệp báo cáo lỗi (`BR-25.2`, `BR-24.2`), thời hạn 7 ngày của quyền đọc tạm tự cấp (`BR-17.2c`), thời hạn xóa tài liệu xác minh danh tính 30 ngày (`BR-01.5b`), các giới hạn theo gói dịch vụ (`NFR-11`) và chu kỳ sao lưu 35 ngày (`NFR-10`) áp dụng thống nhất cho mọi không gian làm việc cùng gói.

---

## 10. Phụ lục C — Nhật ký Mâu thuẫn & Quyết định đã chốt

Phụ lục này ghi các mâu thuẫn nội tại đã được giải quyết và các quyết định đã chốt, để lần rà soát sau không lật lại. Nội dung chi tiết của từng quyết định nằm tại quy tắc tương ứng ở Mục 3 – 5.

| # | Mâu thuẫn / câu hỏi | Cách xử lý đã chốt | Nơi có hiệu lực |
| --- | --- | --- | --- |
| C.1 | Phạm vi dữ liệu của vai trò Marketing | Tham số `CFG-05-02`: mặc định Marketing xem toàn bộ ở dạng chỉ đọc, mỗi lượt đọc bản ghi ngoài phạm vi gán đều ghi nhật ký; tenant siết lại được | Mục 5, `NFR-07` mục 13 |
| C.2 | Quyền nhập khẩu dữ liệu của Marketing | Tham số `CFG-05-02`: mặc định cho phép khi được cấp quyền Nhập dữ liệu, bắt buộc khai báo cơ sở đồng thuận | Mục 5, `BR-30.4` |
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
