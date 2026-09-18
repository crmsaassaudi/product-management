# SRS — Phân hệ Quản lý Công việc, Lịch trình & Ghi nhận Tương tác Hoạt động (Tasks, Calendar & Activity Logging)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA (Version 6.0) |
| **Module** | CRM — Phân hệ Quản lý Công việc, Lịch trình & Ghi nhận Tương tác Hoạt động (Tasks, Calendar & Activity Logging) |
| **Ngày cập nhật** | 2026-09-17 |
| **Phiên bản** | v6.0 (Chuẩn hóa Nghiệp vụ Thuần túy — Thay thế toàn bộ v5.2) |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`contacts-srs.md`](./contacts-srs.md), [`deals-pipeline-srs.md`](./deals-pipeline-srs.md), [`tickets-srs.md`](./tickets-srs.md), [`campaigns-srs.md`](./campaigns-srs.md), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`object-manager-srs.md`](./object-manager-srs.md) |

## Ghi chú về phiên bản v6.0

Phiên bản này **viết lại toàn bộ** tài liệu theo đúng vai trò của một SRS nghiệp vụ. Ba thay đổi về bản chất so với v5.2:

1. **Loại bỏ hoàn toàn ngôn ngữ kỹ thuật khỏi phần thân.** Phiên bản cũ đặc tả quy tắc bằng tên trường dữ liệu, tên thành phần phần mềm, công thức điều kiện và tên hạ tầng. Những nội dung đó mô tả *hệ thống đang được xây dựng thế nào*, trong khi SRS phải mô tả *doanh nghiệp cần điều gì là đúng*. Toàn bộ quy tắc nay được phát biểu bằng ngôn ngữ người dùng nghiệp vụ. Ánh xạ sang tên trường dữ liệu được gom riêng tại Phụ lục A và **không phải là nội dung ràng buộc**.

2. **Mỗi quy tắc nghiệp vụ đều kèm Tiêu chí Chấp nhận (AC) kiểm chứng được.** Mỗi AC mô tả một tình huống nghiệp vụ cụ thể và kết quả mong đợi mà người dùng quan sát được, để QA nghiệm thu mà không cần đọc mã nguồn và Dev không phải tự suy diễn.

3. **Giải quyết dứt điểm các mâu thuẫn nội tại của v5.2.** Đặc biệt là cụm quy tắc vòng đời công việc — nơi phát sinh phần lớn mâu thuẫn. Ba quyết định nền tảng được chủ tài liệu chốt ngày 2026-09-17, ghi tại Mục 2.5.

**Nguyên tắc biên soạn:** Tài liệu mô tả **trạng thái nghiệp vụ mục tiêu (To-Be)**. Nơi nào hệ thống hiện tại đang làm khác đặc tả, phần triển khai phải được sửa để tuân theo tài liệu này, không phải ngược lại. Tài liệu **không ghi nhận hiện trạng mã nguồn**; công việc rà soát khoảng cách giữa đặc tả và triển khai thuộc về tài liệu backlog riêng.

---

## Quy ước Sử dụng Tài liệu

### 1. Hệ thống nhãn phạm vi phát hành

- **`[Phạm vi phát hành]`** — Thuộc phạm vi nghiệm thu của phiên bản hiện tại. Mọi quy tắc và AC đều là điều kiện bắt buộc để thông qua phát hành.
- **`[Roadmap v7.0]`** — Nhu cầu nghiệp vụ đã được ghi nhận nhưng **chính thức nằm ngoài phạm vi phát hành hiện tại**, không dùng làm tiêu chí chặn nghiệm thu.

### 2. Cách đọc Tiêu chí Chấp nhận (AC)

Mỗi AC gồm ba phần: **Bối cảnh** (tình huống nghiệp vụ trước khi hành động), **Hành động** (điều người dùng hoặc hệ thống làm), **Kết quả mong đợi** (điều quan sát được sau đó). AC được đánh mã theo quy tắc nghiệp vụ mà nó kiểm chứng.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả toàn bộ nghiệp vụ quản trị Công việc, Lịch trình hoạt động và Ghi nhận Tương tác Khách hàng trong hệ thống CRM B2B SaaS:

1. **Quản trị Công việc:** Quản lý việc cần làm, người chịu trách nhiệm, hạn chót, mức ưu tiên và tiến độ hoàn thành.
2. **Đa chế độ làm việc:** Ba cách nhìn cùng một khối công việc — Danh sách, Lịch biểu và Bảng trạng thái — phục vụ ba nhu cầu khác nhau: tra cứu, sắp xếp thời gian và theo dõi luồng xử lý.
3. **Gắn công việc vào ngữ cảnh khách hàng:** Mọi công việc đều có thể gắn vào Khách hàng, Doanh nghiệp, Cơ hội bán hàng hoặc Yêu cầu hỗ trợ, để người xử lý luôn có đủ bối cảnh.
4. **Đánh thức quan hệ khách hàng:** Khi nhân viên hoàn thành công việc chăm sóc, hệ thống tự ghi nhận quan hệ với khách hàng đó vẫn đang sống, gỡ bỏ cảnh báo nguội lạnh.
5. **Công việc định kỳ:** Tự động sinh việc theo chu kỳ, xử lý đúng các ca biên về ngày tháng mà không làm lệch lịch vận hành của doanh nghiệp.
6. **Nhắc việc:** Chủ động nhắc đúng người vào đúng thời điểm đã hẹn.
7. **Ghi nhận tương tác:** Lưu lại lịch sử mọi cuộc gọi, cuộc họp, email, tin nhắn và ghi chú đã phát sinh với khách hàng.
8. **Thùng rác và bảo lưu:** Xóa nhầm luôn khôi phục được trong thời hạn quy định.

### 1.2 Phạm vi

Tài liệu bao gồm 13 nhóm chức năng:

- **Nhóm A — Quản trị Công việc Cơ bản:** Tạo, sửa, xem, xóa công việc; gắn ngữ cảnh khách hàng; mức ưu tiên; hạn chót; hoàn tất nhanh.
- **Nhóm B — Đa Chế độ Hiển thị:** Danh sách lọc nâng cao, Lịch biểu, Bảng trạng thái kéo thả.
- **Nhóm C — Phân công & Cộng tác:** Giao việc, bàn giao khi thay đổi nhân sự, nhắc việc.
- **Nhóm D — Lặp lại & Tự động hóa:** Công việc định kỳ, sinh việc tự động từ quy trình.
- **Nhóm E — Ghi nhận Tương tác:** Cuộc gọi, cuộc họp, email, ghi chú, tin nhắn.
- **Nhóm F — Danh sách Kiểm tra & Rào cản:** *[Roadmap v7.0]*
- **Nhóm G — Cảnh báo Quá hạn & Đánh thức Quan hệ:** Quét hạn chót, đánh thức khách hàng khi hoàn thành việc.
- **Nhóm H — Thao tác Hàng loạt & Xuất Dữ liệu:** Cập nhật nhiều công việc cùng lúc, xuất dữ liệu có phân quyền.
- **Nhóm I — Thùng rác & Phục hồi:** Xóa mềm, khôi phục, tự động dọn dẹp theo thời hạn.
- **Nhóm J — Dòng thời gian Hợp nhất:** Lịch sử hoạt động 360 độ trên hồ sơ khách hàng và cơ hội bán hàng.
- **Nhóm K — Tối ưu Phân bổ & Kiểm soát Nâng cao:** *[Roadmap v7.0]*
- **Nhóm L — Nền tảng Cấu hình:** Ràng buộc toàn vẹn danh mục trạng thái và phân loại, trường dữ liệu tùy biến cho công việc.
- **Nhóm M — Nhập liệu & Thông báo Ngoài Ứng dụng:** *[Roadmap v7.0]*

**Ranh giới ngoài phạm vi:**

- Quản lý chi tiết hồ sơ khách hàng — thuộc [`contacts-srs.md`](./contacts-srs.md).
- Quản lý giai đoạn và phễu cơ hội bán hàng — thuộc [`deals-pipeline-srs.md`](./deals-pipeline-srs.md).
- Quản lý vé hỗ trợ và cam kết chất lượng dịch vụ — thuộc [`tickets-srs.md`](./tickets-srs.md).
- Hạ tầng tổng đài và ghi âm cuộc gọi — thuộc phân hệ tích hợp viễn thông riêng; phân hệ này chỉ tiếp nhận kết quả cuộc gọi và đường dẫn tệp ghi âm.
- Hạ tầng **gửi** email và tin nhắn (SMS, Zalo, WhatsApp), gồm cả việc theo dõi trạng thái mở xem email — thuộc [`campaigns-srs.md`](./campaigns-srs.md) và [`omnichat-srs.md`](./omnichat-srs.md). Phân hệ này chỉ **ghi nhận lại** nội dung đã trao đổi và trạng thái do các phân hệ đó cung cấp, không tự gửi.
- Cấu hình danh mục trạng thái, nhóm công việc, nguồn công việc và trường dữ liệu tùy biến — thuộc [`object-manager-srs.md`](./object-manager-srs.md). Phân hệ này chỉ đặc tả **cách công việc sử dụng** các danh mục đó và các ràng buộc toàn vẹn mà danh mục phải bảo đảm (Mục 3, FEAT-37).

### 1.3 Đối tượng đọc

- **Product Owner / Business Analyst:** Căn cứ chốt phạm vi phát hành và thẩm định quy trình nghiệp vụ.
- **Đội ngũ Phát triển:** Căn cứ duy nhất để xây dựng và sửa chữa chức năng. Khi mã nguồn khác tài liệu, mã nguồn phải sửa theo tài liệu.
- **Đội ngũ Đảm bảo Chất lượng:** Căn cứ thiết kế kịch bản kiểm thử. Mỗi AC là một ca kiểm thử.
- **Giám đốc Vận hành & Trưởng phòng Kinh doanh:** Căn cứ thiết lập quy trình làm việc chuẩn cho nhân viên.

### 1.4 Thuật ngữ nghiệp vụ

| Thuật ngữ | Định nghĩa nghiệp vụ |
| --- | --- |
| **Công việc** | Một nhiệm vụ cần làm trong tương lai, được giao cho một nhân viên cụ thể, có hạn chót và mức ưu tiên. |
| **Người phụ trách** | Nhân viên chịu trách nhiệm thực hiện và hoàn tất công việc. Một công việc có tối đa một người phụ trách; có thể tạm thời chưa có ai nếu Không gian làm việc cho phép (`CFG-TASK-18`). |
| **Nhật ký Tương tác** | Bản ghi lịch sử về một tương tác **đã diễn ra** với khách hàng. Khác với Công việc — vốn là việc **sẽ làm**. |
| **Ngữ cảnh khách hàng** | Thực thể CRM mà công việc được gắn vào: Khách hàng, Doanh nghiệp, Cơ hội bán hàng hoặc Yêu cầu hỗ trợ. |
| **Trạng thái Kết thúc** | Trạng thái biểu thị công việc không còn cần xử lý. Gồm hai nhánh phân biệt: **Hoàn thành** và **Hủy bỏ** (xem Mục 2.5, Quyết định 1). |
| **Đánh thức quan hệ** | Việc hệ thống tự ghi nhận rằng quan hệ với một khách hàng hoặc cơ hội vẫn đang được chăm sóc, nhằm gỡ cảnh báo nguội lạnh. |
| **Công việc định kỳ** | Công việc mẫu tự động sinh ra các công việc con theo chu kỳ định sẵn. |
| **Ngày neo** | Ngày trong tháng mà công việc định kỳ hằng tháng phải rơi vào, được giữ nguyên qua các tháng có số ngày khác nhau. |
| **Mức ưu tiên** | Thang bốn bậc phân loại độ khẩn cấp: Khẩn cấp, Cao, Trung bình, Thấp. |
| **Không gian làm việc** | Phạm vi dữ liệu của một doanh nghiệp khách hàng. Dữ liệu giữa các không gian làm việc tuyệt đối không nhìn thấy nhau. |

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà phân hệ giải quyết

1. **Bỏ sót hạn chót đã cam kết với khách hàng.** Nhân viên hứa gửi báo giá hoặc gọi lại nhưng không có công cụ nhắc tập trung, dẫn đến trễ hẹn và mất cơ hội.
2. **Báo cáo quá hạn sai thực tế.** Công việc đã làm xong vẫn bị cảnh báo đỏ, khiến quản lý mất niềm tin vào số liệu và phải tự kiểm đếm thủ công.
3. **Đứt gãy lịch sử khi thay đổi nhân sự.** Khi nhân viên nghỉ việc, lịch sử chăm sóc khách hàng thất lạc nếu không được lưu vào hồ sơ khách hàng.
4. **Lịch định kỳ bị trôi ngày.** Công việc cuối tháng nhảy dần từ ngày 31 về ngày 28 sau vài chu kỳ, gây bỏ sót các kỳ báo cáo tài chính quan trọng.
5. **Cơ hội bị cảnh báo nguội lạnh oan.** Nhân viên vẫn đang tích cực xử lý nhưng hệ thống không ghi nhận, khiến cơ hội bị gắn cờ sai và quản lý can thiệp không cần thiết.
6. **Việc bị hủy bị tính như việc đã làm.** Báo cáo năng suất bị thổi phồng vì không phân biệt được công việc hoàn thành và công việc hủy bỏ.

### 2.2 Vai trò người dùng

| Vai trò | Quyền hạn và trách nhiệm nghiệp vụ |
| --- | --- |
| **Nhân viên Kinh doanh** | Tạo công việc cá nhân, xử lý việc được giao, hoàn tất công việc, ghi nhận tương tác khách hàng. |
| **Chuyên viên Hỗ trợ** | Tiếp nhận và xử lý công việc phát sinh từ Yêu cầu hỗ trợ. |
| **Trưởng nhóm** | Giao việc trong nhóm, theo dõi tiến độ, điều phối lại công việc trong phạm vi nhóm. |
| **Quản lý Kinh doanh** | Toàn bộ quyền của Trưởng nhóm, trên phạm vi toàn phòng ban. |
| **Giám đốc Kinh doanh** | Xem và điều phối công việc trên toàn không gian làm việc. |
| **Quản trị viên Không gian làm việc** | Cấu hình danh mục trạng thái, nhóm công việc, nguồn công việc, trường dữ liệu tùy biến và chính sách lưu trữ. Thực hiện bàn giao công việc khi nhân sự thay đổi. |
| **Chủ sở hữu Không gian làm việc** | Toàn quyền kiểm soát mọi dữ liệu trong không gian làm việc. |
| **Tiến trình Hệ thống** | Nhắc việc đến hạn, sinh công việc định kỳ, dọn dẹp thùng rác quá hạn lưu trữ. |

> **Ghi chú:** Sáu vai trò người dùng đầu tiên cùng Chủ sở hữu tạo thành bảy cột của Ma trận phân quyền tại Mục 5. Vai trò chức năng và cách phân giải quyền chi tiết thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md).

### 2.3 Quy ước thời gian nghiệp vụ

Toàn bộ mốc thời gian nghiệp vụ trong tài liệu này — *quá hạn*, *ngày cuối tháng*, *đầu ngày*, *giờ chạy tiến trình nền* — được xác định theo **múi giờ cấu hình của Không gian làm việc**, không theo múi giờ máy chủ và không theo múi giờ của từng người dùng.

**Lý do nghiệp vụ:** Một công việc có hạn chót "hết ngày 31/03" phải quá hạn vào cùng một thời điểm đối với mọi thành viên trong cùng doanh nghiệp. Nếu tính theo múi giờ cá nhân, hai nhân viên cùng nhóm sẽ nhìn thấy hai trạng thái khác nhau trên cùng một công việc, và báo cáo của quản lý không thể đối chiếu được.

**Ràng buộc:** Mỗi Không gian làm việc bắt buộc có đúng một múi giờ được cấu hình (`CFG-TASK-01`). Giá trị mặc định khi khởi tạo là `GMT+7`. Việc thay đổi múi giờ chỉ ảnh hưởng tới cách diễn giải các mốc thời gian kể từ thời điểm thay đổi, không hồi tố các bản ghi lịch sử.

### 2.4 Nguyên tắc nghiệp vụ nền tảng

**Nguyên tắc 1 — Phân định Công việc và Nhật ký Tương tác.**
Công việc là việc **sẽ làm**: có người phụ trách, hạn chót, mức ưu tiên và trạng thái. Nhật ký Tương tác là bản ghi bất biến về việc **đã xảy ra**: một cuộc gọi đã gọi, một cuộc họp đã họp. Hai khái niệm không được trộn lẫn. Công việc khi hoàn thành sẽ **sinh ra** một bản ghi tương tác tương ứng trên hồ sơ khách hàng, nhưng bản thân công việc không phải là bản ghi tương tác.

**Nguyên tắc 2 — Trạng thái do doanh nghiệp tự định nghĩa.**
Hệ thống **không áp đặt** danh sách trạng thái cố định. Mỗi Không gian làm việc tự định nghĩa bộ trạng thái phù hợp quy trình riêng. Mọi quy tắc nghiệp vụ, báo cáo và giao diện phải căn cứ trên **tính chất** của trạng thái (kết thúc hay chưa, thuộc nhánh hoàn thành hay hủy bỏ, có phải mặc định không), tuyệt đối không căn cứ trên tên gọi của trạng thái.

**Nguyên tắc 3 — Không quá một người chịu trách nhiệm.**
Một công việc không bao giờ có nhiều hơn một người phụ trách cùng lúc, để trách nhiệm không bị phân tán. Nhu cầu nhiều người cùng tham gia một công việc được ghi nhận nhưng thuộc *[Roadmap v7.0]*.

Việc một công việc **tạm thời chưa có** người phụ trách nào là tập quán vận hành khác nhau giữa các doanh nghiệp — không phải ràng buộc toàn vẹn dữ liệu — nên được cấu hình theo Không gian làm việc (`BR-01.2`, `CFG-TASK-18`), không cố định cứng.

**Nguyên tắc 4 — Bảo toàn ngày neo của lịch định kỳ.**
Công việc định kỳ phải luôn quay về đúng ngày mà người dùng đã chọn ban đầu. Việc một tháng ngắn ngày buộc phải lùi ngày **chỉ ảnh hưởng tới kỳ đó**, không được làm lệch vĩnh viễn các kỳ sau.

**Nguyên tắc 5 — Dữ liệu lịch sử không bị viết lại âm thầm.**
Kết quả của công việc đã kết thúc là căn cứ đánh giá năng suất. Không ai được thay đổi *ai đã làm* và *đáng lẽ phải xong khi nào* mà không để lại dấu vết và không có chủ đích rõ ràng.

### 2.5 Ba quyết định nghiệp vụ nền tảng

*Chốt bởi chủ tài liệu ngày 2026-09-17. Ba quyết định này chi phối nhiều quy tắc trong tài liệu và giải quyết các mâu thuẫn tồn tại ở v5.2.*

#### Quyết định 1 — Tách Hoàn thành và Hủy bỏ thành hai nhánh kết thúc riêng biệt

**Vấn đề của v5.2:** Mọi trạng thái kết thúc đều được đóng dấu hoàn thành. Hệ quả: công việc bị hủy vẫn được tính vào báo cáo năng suất, làm tỷ lệ hoàn thành bị thổi phồng và đánh giá nhân viên sai lệch.

**Quyết định:** Trạng thái kết thúc có **hai nhánh phân biệt**:

| Nhánh | Ý nghĩa nghiệp vụ | Tính vào năng suất | Đánh thức quan hệ khách hàng |
| --- | --- | :---: | :---: |
| **Hoàn thành** | Công việc đã được thực hiện xong | Có | Có |
| **Hủy bỏ** | Công việc không còn cần thực hiện | Không | Không |

Cả hai nhánh đều thoát khỏi cảnh báo quá hạn và đều dừng nhắc việc. Khi Quản trị viên định nghĩa một trạng thái kết thúc, bắt buộc phải chỉ định nó thuộc nhánh nào.

#### Quyết định 2 — Bàn giao công việc là nghiệp vụ riêng, được miễn trừ quy tắc khóa sửa đổi

**Vấn đề của v5.2:** Quy tắc khóa sửa đổi chặn việc đổi người phụ trách trên công việc đã kết thúc. Nhưng khi nhân viên nghỉ việc, doanh nghiệp cần chuyển toàn bộ công việc — kể cả đã hoàn thành — sang người kế nhiệm. Cách duy nhất v5.2 cho phép là mở lại công việc, mà mở lại sẽ xóa mất mốc hoàn thành, tức là phá hỏng chính dữ liệu mà quy tắc khóa muốn bảo vệ. Đây là vòng luẩn quẩn.

**Quyết định:** Tách thành **hai nghiệp vụ khác nhau**:

- **Sửa đổi công việc** — thao tác hằng ngày của người dùng. Chịu ràng buộc khóa sửa đổi (FEAT-05).
- **Bàn giao công việc** — nghiệp vụ nhân sự do Quản trị viên hoặc Quản lý thực hiện. Chuyển được cả công việc đã kết thúc, giữ nguyên mốc hoàn thành, bắt buộc ghi lý do và để lại dấu vết (FEAT-38).

#### Quyết định 3 — Mốc thời gian theo múi giờ Không gian làm việc

Xem Mục 2.3.

### 2.6 Bảng tổng hợp tính năng nghiệp vụ

| Nhóm | Mã | Tên tính năng nghiệp vụ | Phạm vi |
| --- | --- | --- | :---: |
| **A. Quản trị Công việc** | `FEAT-01` | Tạo mới & Quản lý Công việc | `[Phạm vi phát hành]` |
| | `FEAT-02` | Gắn Công việc vào Ngữ cảnh Khách hàng | `[Phạm vi phát hành]` |
| | `FEAT-03` | Phân loại Mức độ Ưu tiên | `[Phạm vi phát hành]` |
| | `FEAT-04` | Quản lý Hạn chót & Cảnh báo Quá hạn | `[Phạm vi phát hành]` |
| | `FEAT-05` | Vòng đời Trạng thái & Bảo toàn Kết quả | `[Phạm vi phát hành]` |
| **B. Đa Chế độ Hiển thị** | `FEAT-06` | Danh sách Công việc Lọc Nâng cao | `[Phạm vi phát hành]` |
| | `FEAT-07` | Lịch biểu Ngày / Tuần / Tháng | `[Phạm vi phát hành]` |
| | `FEAT-08` | Bảng Theo dõi Luồng Xử lý | `[Phạm vi phát hành]` |
| **C. Phân công & Cộng tác** | `FEAT-09` | Giao việc & Thẩm định Người nhận | `[Phạm vi phát hành]` |
| | `FEAT-10` | Nhiều Người Cùng Tham gia Công việc | `[Roadmap v7.0]` |
| | `FEAT-11` | Nhắc việc Thời gian thực | `[Phạm vi phát hành]` |
| | `FEAT-38` | Bàn giao Công việc khi Thay đổi Nhân sự | `[Phạm vi phát hành]` |
| **D. Lặp lại & Tự động hóa** | `FEAT-12` | Công việc Định kỳ Bảo toàn Ngày neo | `[Phạm vi phát hành]` |
| | `FEAT-13` | Sinh Công việc Tự động từ Quy trình | `[Phạm vi phát hành]` |
| | `FEAT-14` | Chuỗi Công việc Chăm sóc Nối tiếp | `[Roadmap v7.0]` |
| | `FEAT-43` | Tạm dừng Chủ động Công việc Mẫu Định kỳ | `[Roadmap v7.0]` |
| **E. Ghi nhận Tương tác** | `FEAT-15` | Ghi nhận Cuộc gọi | `[Phạm vi phát hành]` |
| | `FEAT-16` | Ghi nhận Cuộc họp | `[Phạm vi phát hành]` |
| | `FEAT-17` | Ghi nhận Email | `[Phạm vi phát hành]` |
| | `FEAT-18` | Ghi chú Nội bộ | `[Phạm vi phát hành]` |
| | `FEAT-19` | Nhật ký Tin nhắn | `[Phạm vi phát hành]` |
| | `FEAT-20` | Cấu trúc Dữ liệu Chuyên biệt theo Loại Tương tác | `[Roadmap v7.0]` |
| **F. Danh sách Kiểm tra** | `FEAT-21` | Danh sách Mục con Kiểm tra | `[Roadmap v7.0]` |
| | `FEAT-22` | Rào cản Bắt buộc Hoàn thành Kiểm tra | `[Roadmap v7.0]` |
| | `FEAT-23` | Thanh Tiến độ Hoàn thành | `[Roadmap v7.0]` |
| **G. Cảnh báo & Đánh thức** | `FEAT-24` | Quét Hạn chót & Nhắc việc Tự động | `[Phạm vi phát hành]` |
| | `FEAT-25` | Đánh thức Quan hệ Khách hàng | `[Phạm vi phát hành]` |
| | `FEAT-26` | Bảng Điểm Năng suất Nhân viên | `[Roadmap v7.0]` |
| **H. Thao tác Hàng loạt** | `FEAT-27` | Cập nhật & Xóa Hàng loạt | `[Phạm vi phát hành]` |
| | `FEAT-28` | Dời Hạn chót Hàng loạt | `[Phạm vi phát hành]` |
| | `FEAT-29` | Đồng bộ Lịch Ngoài Hai chiều | `[Roadmap v7.0]` |
| | `FEAT-39` | Xuất Dữ liệu Công việc | `[Phạm vi phát hành]` |
| **I. Thùng rác** | `FEAT-30` | Thùng rác & Tự động Dọn dẹp | `[Phạm vi phát hành]` |
| **J. Dòng thời gian** | `FEAT-31` | Dòng thời gian Hợp nhất 360 độ | `[Phạm vi phát hành]` |
| | `FEAT-32` | Chặn Chuyển Giai đoạn khi Còn Việc Khẩn cấp | `[Roadmap v7.0]` |
| | `FEAT-33` | Công việc Riêng tư | `[Roadmap v7.0]` |
| **K. Tối ưu Phân bổ** | `FEAT-34` | Cân bằng Tải Công việc | `[Roadmap v7.0]` |
| | `FEAT-35` | Theo dõi Thời lượng Thực tế | `[Roadmap v7.0]` |
| | `FEAT-36` | Phê duyệt Hoàn tất Công việc Trọng yếu | `[Roadmap v7.0]` |
| **L. Nền tảng Cấu hình** | `FEAT-37` | Toàn vẹn Danh mục Trạng thái & Phân loại | `[Phạm vi phát hành]` |
| | `FEAT-40` | Trường Dữ liệu Tùy biến cho Công việc | `[Phạm vi phát hành]` |
| **M. Nhập liệu & Thông báo** | `FEAT-41` | Nhập Dữ liệu Công việc Hàng loạt | `[Roadmap v7.0]` |
| | `FEAT-42` | Nhắc việc qua Kênh Ngoài Ứng dụng | `[Roadmap v7.0]` |

**Tổng kết phạm vi:** 43 tính năng — **27 dòng thuộc phạm vi phát hành**, **16 thuộc Roadmap v7.0**.

*Ghi chú cách đếm:* FEAT-15 đến FEAT-19 là năm loại tương tác của cùng một năng lực Ghi nhận Tương tác, được đặc tả chung tại Mục 3 — bảng trên liệt kê riêng từng loại nên phạm vi phát hành có 27 dòng nhưng 23 mục đặc tả.

*So với v5.2: bổ sung 4 tính năng vốn đang vận hành nhưng chưa từng được đặc tả (FEAT-37 Toàn vẹn danh mục, FEAT-38 Bàn giao, FEAT-39 Xuất dữ liệu, FEAT-40 Trường tùy biến). FEAT-10 và FEAT-33 được đưa hẳn về Roadmap v7.0 thay vì nhãn mập mờ. Vòng review thứ hai bổ sung FEAT-41 và FEAT-42 — hai năng lực mọi CRM lớn đều có, được ghi nhận vào Roadmap để không bị bỏ quên. Vòng review thứ năm bổ sung FEAT-43 — nhu cầu tạm dừng chủ động công việc mẫu định kỳ, ghi nhận vào Roadmap v7.0 vì phiên bản hiện tại đã có lối ra tạm thời qua Thùng rác (`BR-12.7`).*

---

## 3. Đặc tả yêu cầu chức năng

*Mục này chỉ đặc tả các tính năng thuộc **phạm vi phát hành**. Nhóm F, K và M gồm toàn bộ tính năng Roadmap v7.0 nên không có mục đặc tả riêng ở đây — chúng được liệt kê ở bảng cuối Mục 3.*

### Nhóm A — Quản trị Công việc Cơ bản

#### FEAT-01 — Tạo mới & Quản lý Công việc `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Người dùng tạo, xem, sửa và xóa công việc cần làm.

**Quy tắc nghiệp vụ:**

- **`BR-01.1` (Thông tin tối thiểu để tạo công việc):** Một công việc chỉ được tạo khi có đủ: **Tiêu đề**, **Hạn chót** và **Mức ưu tiên**.
  - Tiêu đề không được để trống hoặc chỉ chứa khoảng trắng, và không vượt quá 500 ký tự. Hệ thống **không đặt độ dài tối thiểu** — thực tế vận hành cho thấy các tiêu đề ngắn như "Gọi A" hay "Ký HĐ" là hợp lệ và phổ biến.
  - Hạn chót được phép nằm trong quá khứ, phục vụ nghiệp vụ ghi nhận bổ sung công việc đã trễ.
  - Mức ưu tiên mặc định là **Trung bình** nếu người dùng không chọn (`CFG-TASK-02`).

- **`BR-01.2` (Người phụ trách khi bỏ trống):** Khi không có người phụ trách được chỉ định — do người dùng bỏ trống, hoặc do quy trình tự động hóa (FEAT-13) không xác định được người nhận — hệ thống xử lý theo cấu hình của Không gian làm việc (`CFG-TASK-18`):
  - **Tự gán người tạo** *(mặc định)* — hệ thống gán chính người tạo làm người phụ trách. Chỉ áp dụng khi có người tạo là một người dùng thật; quy trình tự động hóa không có người tạo thật nên xử lý như trường hợp "Cho phép chưa phân công" bên dưới bất kể cấu hình này.
  - **Bắt buộc chọn tường minh** — hệ thống từ chối lưu, yêu cầu chọn người phụ trách trước khi tiếp tục. Với công việc do quy trình tự động hóa tạo, quy trình bị chặn và ghi vào nhật ký để Quản trị viên xử lý (`BR-13.5`).
  - **Cho phép chưa phân công** — công việc được tạo ở trạng thái **"Chưa phân công"**, không có người phụ trách, chờ được phân công sau.

  **Lý do nghiệp vụ:** Đây là tập quán vận hành khác nhau giữa các doanh nghiệp, không phải ràng buộc toàn vẹn dữ liệu. Có doanh nghiệp mà một nhân viên hành chính hoặc Quản trị viên tạo hộ việc cho người khác gần như mọi lúc — nếu hệ thống tự gán mặc định cho người tạo, một lần quên chọn sẽ âm thầm tạo ra công việc gán sai người mà không ai để ý cho đến khi đã quá hạn; doanh nghiệp này cần bị chặn lưu để buộc chọn tường minh. Doanh nghiệp khác coi việc luôn có người phụ trách (kể cả là chính người tạo) quan trọng hơn, chấp nhận rủi ro gán nhầm thấp hơn rủi ro bỏ trống. Doanh nghiệp thứ ba vận hành theo mô hình hàng đợi — việc được tạo trước, Trưởng nhóm hoặc Quản lý triage và phân công sau — nên "chưa phân công" là trạng thái bình thường, không phải lỗi cần chặn.

- **`BR-01.3` (Đơn vị tổ chức của công việc):** Công việc thuộc về **đơn vị tổ chức của người phụ trách**, không phải của người tạo.

  **Lý do nghiệp vụ:** Công việc phải hiện diện trong phạm vi quản lý của người thực sự chịu trách nhiệm. Nếu lấy theo người tạo, khi Quản lý phòng A giao việc cho nhân viên phòng B, công việc sẽ nằm trong phạm vi phòng A — và **Trưởng phòng B không nhìn thấy việc mà nhân viên mình đang phải làm**. Đây là lỗi phân quyền nghiêm trọng với mô hình dữ liệu theo cây tổ chức.

  Khi người phụ trách thay đổi, đơn vị tổ chức của công việc chuyển theo. Trường hợp người phụ trách không thuộc đơn vị tổ chức nào, công việc chỉ hiển thị theo quyền sở hữu cá nhân.

  **Công việc "Chưa phân công" (`BR-01.2`):** Nếu có người tạo là một người dùng thật, công việc tạm thuộc đơn vị tổ chức của người tạo cho tới khi được phân công, rồi chuyển theo người phụ trách mới như bình thường. Nếu không có người tạo thật (quy trình tự động hóa), công việc không thuộc đơn vị tổ chức nào và hiển thị ở phạm vi **Toàn bộ** — cùng cách FEAT-13 đã được xếp trong Ma trận Phân quyền Mục 5.

  **Ngoại lệ duy nhất:** Công việc **đã kết thúc** được bàn giao theo FEAT-38 giữ nguyên đơn vị tổ chức cũ — xem `BR-38.5`.

- **`BR-01.4` (Phân loại công việc):** Người dùng chọn Nhóm công việc từ danh mục do Không gian làm việc định nghĩa (Gọi điện, Họp, Email, Việc cần làm…). Nhóm công việc quyết định loại bản ghi tương tác được sinh ra khi công việc hoàn thành (xem `BR-25.3`).

- **`BR-01.5` (Nguồn công việc):** Người dùng có thể ghi nhận nguồn phát sinh công việc từ danh mục do Không gian làm việc định nghĩa, phục vụ phân tích nguồn tải công việc.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-01.1.1` | Màn hình tạo công việc | Nhập tiêu đề "Gọi A", chọn hạn chót và mức ưu tiên, lưu | Tạo thành công. Tiêu đề 5 ký tự là hợp lệ |
| `AC-01.1.2` | Màn hình tạo công việc | Bỏ trống tiêu đề, lưu | Từ chối, thông báo rõ trường tiêu đề là bắt buộc |
| `AC-01.1.3` | Màn hình tạo công việc | Nhập tiêu đề chỉ gồm khoảng trắng, lưu | Từ chối như trường hợp bỏ trống |
| `AC-01.1.4` | Màn hình tạo công việc | Chọn hạn chót là ngày hôm qua, lưu | Tạo thành công, công việc hiển thị trạng thái quá hạn ngay |
| `AC-01.1.5` | Màn hình tạo công việc | Không chọn mức ưu tiên, lưu | Tạo thành công với mức ưu tiên Trung bình |
| `AC-01.2.1` | Nhân viên A tạo công việc, `CFG-TASK-18` đang ở mặc định "Tự gán người tạo" | Không chọn người phụ trách, lưu | Công việc có người phụ trách là chính A |
| `AC-01.2.2` | Quản trị viên đặt `CFG-TASK-18` = "Bắt buộc chọn tường minh" | Nhân viên A tạo công việc, không chọn người phụ trách, lưu | Từ chối, yêu cầu chọn người phụ trách trước khi lưu |
| `AC-01.2.3` | Quản trị viên đặt `CFG-TASK-18` = "Cho phép chưa phân công" | Nhân viên A tạo công việc, không chọn người phụ trách, lưu | Lưu thành công, công việc ở trạng thái "Chưa phân công", tạm thuộc đơn vị tổ chức của A |
| `AC-01.2.4` | `CFG-TASK-18` đang ở mặc định "Tự gán người tạo". Một quy trình tự động hóa tạo công việc, không cấu hình người nhận và bản ghi kích hoạt không có chủ sở hữu | Quy trình chạy | Công việc được tạo ở trạng thái "Chưa phân công" — không bị chặn, không tự gán cho ai vì không có người tạo thật. Công việc hiển thị phạm vi Toàn bộ, không thuộc đơn vị tổ chức nào |
| `AC-01.2.5` | `CFG-TASK-18` đang ở "Bắt buộc chọn tường minh". Cùng quy trình tự động hóa như trên | Quy trình chạy | Quy trình bị chặn, không tạo công việc. Sự kiện được ghi vào nhật ký quy trình để Quản trị viên xử lý (`BR-13.5`) |
| `AC-01.3.1` | Quản lý phòng A giao việc cho nhân viên B thuộc phòng B | Lưu công việc | Công việc thuộc phòng B. Trưởng phòng B nhìn thấy công việc trong danh sách của phòng mình |
| `AC-01.4.1` | Danh mục có các nhóm Gọi điện, Họp, Email | Tạo công việc và chọn nhóm "Họp" | Công việc lưu với nhóm "Họp". Khi hoàn thành sẽ sinh bản ghi loại cuộc họp theo `BR-25.3` |
| `AC-01.5.1` | Danh mục nguồn công việc có "Từ khiếu nại khách hàng" | Tạo công việc và chọn nguồn đó | Công việc lưu với nguồn đã chọn, và lọc được theo nguồn này trên Danh sách |
| `AC-01.3.2` | Công việc đang thuộc phòng B | Đổi người phụ trách sang nhân viên phòng C | Công việc chuyển sang thuộc phòng C. Trưởng phòng B không còn thấy, Trưởng phòng C bắt đầu thấy |

---

#### FEAT-02 — Gắn Công việc vào Ngữ cảnh Khách hàng `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Gắn công việc vào một thực thể CRM để người xử lý có đủ bối cảnh, và để công việc xuất hiện trên dòng thời gian của thực thể đó.

**Quy tắc nghiệp vụ:**

- **`BR-02.1` (Các loại ngữ cảnh được phép):** Công việc gắn được vào đúng **bốn** loại thực thể: **Khách hàng**, **Doanh nghiệp**, **Cơ hội bán hàng**, **Yêu cầu hỗ trợ**. Mỗi công việc gắn tối đa một thực thể. Công việc không gắn ngữ cảnh là hợp lệ — đó là việc nội bộ.

- **`BR-02.2` (Toàn vẹn liên kết):** Thực thể được gắn phải tồn tại và thuộc cùng Không gian làm việc với công việc. Hệ thống từ chối mọi liên kết tới thực thể không tồn tại hoặc thuộc không gian làm việc khác.

  **Lý do nghiệp vụ:** Đây là ranh giới cách ly dữ liệu giữa các doanh nghiệp khách hàng. Một liên kết xuyên không gian làm việc là sự cố rò rỉ dữ liệu, không phải lỗi nhập liệu.

- **`BR-02.3` (Ngữ cảnh bị xóa mềm):** Khi thực thể được gắn bị chuyển vào thùng rác, công việc **không bị xóa theo** và vẫn xử lý được bình thường, nhưng hiển thị nhãn cảnh báo cho biết ngữ cảnh đang nằm trong thùng rác. Khi thực thể được khôi phục, nhãn cảnh báo tự biến mất.

  **Lý do nghiệp vụ:** Xóa nhầm khách hàng là tình huống phổ biến. Nếu công việc biến mất theo, nhân viên mất luôn danh sách việc đang làm mà không hiểu vì sao.

- **`BR-02.4` (Thay đổi ngữ cảnh):** Người dùng được phép đổi hoặc gỡ ngữ cảnh của công việc đang mở. Công việc đã kết thúc không được đổi ngữ cảnh, vì điều đó viết lại lịch sử chăm sóc của khách hàng.

  **Cấu hình theo doanh nghiệp (`CFG-TASK-12`):** Doanh nghiệp có thể cho phép Quản trị viên đổi ngữ cảnh của công việc đã kết thúc, bắt buộc ghi lý do và để lại dấu vết. Mặc định là **cấm**.

  **Lý do nghiệp vụ:** Tình huống thật hay gặp là khách hàng cá nhân sau đó lập pháp nhân để ký hợp đồng, và toàn bộ lịch sử chăm sóc cần chuyển sang hồ sơ doanh nghiệp để hồ sơ giao dịch liền mạch. Không có lối ra này, cách duy nhất là mở lại công việc — mà mở lại sẽ phá mốc kết thúc, đúng vòng luẩn quẩn mà Quyết định 2 đã giải cho trường người phụ trách. Ngược lại, doanh nghiệp tính hoa hồng theo lịch sử chăm sóc trên cơ hội cần giữ lệnh cấm tuyệt đối. Dù cấu hình thế nào, ngữ cảnh mới vẫn phải thỏa `BR-02.2`.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-02.1.1` | Tạo công việc | Gắn vào một Cơ hội bán hàng | Lưu thành công. Công việc hiện trên dòng thời gian của cơ hội đó |
| `AC-02.1.2` | Tạo công việc | Không gắn ngữ cảnh nào | Lưu thành công, công việc là việc nội bộ |
| `AC-02.2.1` | Tạo công việc | Gắn vào khách hàng thuộc không gian làm việc khác | Từ chối, thông báo ngữ cảnh không hợp lệ. Không tiết lộ sự tồn tại của thực thể đó |
| `AC-02.3.1` | Công việc đang gắn khách hàng X | Khách hàng X bị chuyển vào thùng rác | Công việc vẫn còn, vẫn xử lý được, hiển thị nhãn cảnh báo ngữ cảnh trong thùng rác |
| `AC-02.3.2` | Tiếp nối AC-02.3.1 | Khôi phục khách hàng X từ thùng rác | Nhãn cảnh báo biến mất, công việc trở lại bình thường |
| `AC-02.4.1` | Công việc đã hoàn thành, gắn khách hàng X, tham số cho đổi ngữ cảnh đang **tắt** (mặc định) | Đổi ngữ cảnh sang khách hàng Y | Từ chối, thông báo không thể đổi ngữ cảnh của công việc đã kết thúc |
| `AC-02.4.2` | Cùng bối cảnh, Quản trị viên **bật** tham số cho đổi ngữ cảnh | Quản trị viên đổi ngữ cảnh sang khách hàng Y, nhập lý do | Lưu thành công. Dấu vết ghi ngữ cảnh cũ, ngữ cảnh mới, người thực hiện và lý do |
| `AC-02.4.3` | Cùng bối cảnh AC-02.4.2 | Nhân viên Kinh doanh thường thử đổi ngữ cảnh | Từ chối — chỉ Quản trị viên được phép, kể cả khi tham số đã bật |

---

#### FEAT-03 — Phân loại Mức độ Ưu tiên `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Phân loại công việc theo bốn bậc khẩn cấp, giúp nhân viên biết xử lý gì trước.

**Quy tắc nghiệp vụ:**

- **`BR-03.1` (Bốn mức ưu tiên cố định):** Hệ thống dùng đúng bốn mức, không cho Không gian làm việc tùy biến: **Khẩn cấp**, **Cao**, **Trung bình** (mặc định), **Thấp**.

  **Lý do nghiệp vụ:** Khác với trạng thái — vốn phản ánh quy trình riêng của từng doanh nghiệp — mức ưu tiên là thang đo phổ quát. Giữ cố định để báo cáo so sánh được giữa các phòng ban và để quy tắc tự động hóa dùng chung một ngôn ngữ.

- **`BR-03.2` (Thể hiện trực quan nhất quán):** Mỗi mức ưu tiên có một cách thể hiện trực quan **thống nhất trên mọi màn hình** — Danh sách, Lịch biểu, Bảng trạng thái, màn hình chi tiết và thông báo. Mức càng khẩn cấp càng nổi bật về thị giác. Màu sắc **không bao giờ là phương tiện truyền đạt duy nhất**; luôn kèm nhãn chữ, để người dùng khiếm khuyết phân biệt màu vẫn làm việc được.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-03.1.1` | Danh sách công việc | Lọc theo mức ưu tiên Khẩn cấp | Chỉ hiện công việc mức Khẩn cấp |
| `AC-03.2.1` | Cùng một công việc mức Khẩn cấp | Xem lần lượt trên Danh sách, Lịch biểu và Bảng theo dõi luồng xử lý | Cả ba nơi hiển thị **cùng một nhãn chữ** "Khẩn cấp" và cùng một cách thể hiện trực quan |
| `AC-03.2.2` | Bốn công việc, mỗi việc một mức ưu tiên | Xem trên Danh sách | Bốn mức phân biệt được với nhau; mức Khẩn cấp nổi bật nhất. Nhãn chữ luôn hiện kèm, không chỉ dựa vào màu |

---

#### FEAT-04 — Quản lý Hạn chót & Cảnh báo Quá hạn `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Xác định và hiển thị tình trạng quá hạn của công việc một cách chính xác, để quản lý tin được vào số liệu.

**Quy tắc nghiệp vụ:**

- **`BR-04.1` (Định nghĩa Quá hạn):** Một công việc **đang quá hạn** khi thỏa mãn **đồng thời** hai điều kiện:
  1. Thời điểm hiện tại đã vượt qua hạn chót *(theo múi giờ Không gian làm việc — Mục 2.3)*;
  2. Công việc **chưa ở trạng thái kết thúc** (chưa Hoàn thành và chưa Hủy bỏ).

- **`BR-04.2` (Công việc đã kết thúc không bao giờ quá hạn):** Công việc đã Hoàn thành hoặc đã Hủy bỏ **tuyệt đối không được hiển thị cảnh báo quá hạn**, bất kể hạn chót đã trôi qua bao lâu. Quy tắc này áp dụng **đồng nhất trên mọi chế độ xem**: Danh sách, Lịch biểu, Bảng trạng thái, bộ đếm quá hạn, báo cáo và thông báo.

  **Lý do nghiệp vụ:** Đây là nguyên nhân trực tiếp của vấn đề nghiệp vụ số 2 (Mục 2.1). Khi việc đã xong vẫn báo đỏ, quản lý mất niềm tin vào toàn bộ số liệu và quay lại kiểm đếm thủ công — phủ nhận giá trị của cả phân hệ.

- **`BR-04.3` (Nhận biết trạng thái kết thúc):** Việc xác định một công việc đã kết thúc hay chưa **phải căn cứ trên tính chất của trạng thái** mà công việc đang mang, không căn cứ trên tên trạng thái. Một doanh nghiệp đặt tên trạng thái là "Xong", "Đã nghiệm thu" hay "Closed" đều phải được nhận biết đúng.

- **`BR-04.4` (Phân biệt sắp đến hạn):** Công việc có hạn chót trong vòng 24 giờ tới (`CFG-TASK-05`) và chưa kết thúc được hiển thị ở mức cảnh báo nhẹ (khác màu với quá hạn), giúp nhân viên chủ động xử lý trước khi trễ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-04.1.1` | Công việc hạn chót hôm qua, trạng thái đang mở | Xem trên Danh sách | Hiển thị cảnh báo quá hạn |
| `AC-04.2.1` | Công việc hạn chót hôm qua, đã chuyển sang Hoàn thành | Xem trên Danh sách | **Không** có cảnh báo quá hạn. Hiển thị dấu hiệu đã hoàn thành |
| `AC-04.2.2` | Công việc hạn chót hôm qua, đã chuyển sang Hủy bỏ | Xem trên Danh sách | **Không** có cảnh báo quá hạn. Hiển thị dấu hiệu đã hủy |
| `AC-04.2.3` | Tiếp nối AC-04.2.1 | Xem cùng công việc đó trên Lịch biểu | **Không** có cảnh báo quá hạn |
| `AC-04.2.4` | Tiếp nối AC-04.2.1 | Xem bộ đếm quá hạn trên ô ngày của Lịch biểu | Bộ đếm **không** tính công việc đã hoàn thành này |
| `AC-04.2.5` | Tiếp nối AC-04.2.1 | Xem cùng công việc đó trên Bảng trạng thái | **Không** có cảnh báo quá hạn |
| `AC-04.3.1` | Không gian làm việc đổi tên trạng thái kết thúc từ "Hoàn thành" thành "Đã nghiệm thu" | Xem công việc mang trạng thái đó, hạn chót đã qua | Vẫn **không** báo quá hạn. Việc đổi tên không ảnh hưởng quy tắc |
| `AC-04.4.1` | Công việc hạn chót sau 3 giờ nữa, đang mở | Xem trên Danh sách | Hiển thị cảnh báo nhẹ sắp đến hạn, khác màu quá hạn |

---

#### FEAT-05 — Vòng đời Trạng thái & Bảo toàn Kết quả `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Quản trị việc chuyển trạng thái của công việc và bảo vệ tính toàn vẹn của dữ liệu lịch sử sau khi công việc kết thúc.

**Quy tắc nghiệp vụ:**

- **`BR-05.1` (Ghi nhận thời điểm kết thúc):** Khi công việc chuyển sang trạng thái kết thúc, hệ thống ghi nhận thời điểm kết thúc là thời điểm hiện tại và **dừng mọi nhắc việc chưa phát**.
  - Nếu trạng thái thuộc nhánh **Hoàn thành**, công việc được tính vào năng suất và kích hoạt đánh thức quan hệ khách hàng (FEAT-25).
  - Nếu trạng thái thuộc nhánh **Hủy bỏ**, công việc **không** tính vào năng suất và **không** đánh thức quan hệ khách hàng.

- **`BR-05.2` (Mở lại công việc):** Khi công việc chuyển từ trạng thái kết thúc trở về trạng thái đang mở, mốc kết thúc hiện hành bị gỡ bỏ và công việc quay lại luồng xử lý thông thường.

  **Lịch sử không bị mất:** Hệ thống lưu lại toàn bộ các lần kết thúc trước đó trong lịch sử chuyển trạng thái của công việc. Báo cáo "hoàn thành đúng hạn" căn cứ trên **lần hoàn thành gần nhất**, nhưng dữ liệu các lần trước vẫn tra cứu được.

  **Lý do nghiệp vụ:** Nếu mở lại xóa trắng mốc hoàn thành, một công việc bị mở lại rồi đóng lần nữa sẽ mất vĩnh viễn thông tin lần hoàn thành đầu tiên — mâu thuẫn trực tiếp với mục đích bảo vệ dữ liệu báo cáo của `BR-05.3`.

- **`BR-05.3` (Bảo toàn kết quả của công việc đã kết thúc):**

  **Ý định nghiệp vụ:** Kết quả của công việc đã kết thúc là căn cứ đánh giá năng suất nhân viên và đo thời gian xử lý. Không ai được phép âm thầm viết lại *ai đã làm việc đó* và *việc đó đáng lẽ phải xong khi nào*, trong khi công việc vẫn đang mang trạng thái kết thúc.

  **Quy tắc:**
  - Công việc đã kết thúc **vẫn được sửa** các thông tin mô tả: tiêu đề, nội dung mô tả, nhãn phân loại, nhóm công việc, trường dữ liệu tùy biến.
  - Công việc đã kết thúc **không được thay đổi**: **người phụ trách**, **hạn chót**, **mốc nhắc việc**.
  - Người dùng muốn thay đổi ba thông tin trên phải **mở lại công việc trước**. Mở lại và điều chỉnh trong **cùng một thao tác lưu** là hợp lệ.
  - **Miễn trừ:** Nghiệp vụ **Bàn giao công việc** (FEAT-38) được phép đổi người phụ trách trên công việc đã kết thúc. Đây là nghiệp vụ nhân sự có kiểm soát riêng, không phải thao tác sửa đổi thông thường.

  **Cấu hình theo doanh nghiệp (`CFG-TASK-11`):** Danh sách trường bị khóa điều chỉnh được — doanh nghiệp làm dịch vụ tính phí theo giờ thường khóa thêm **nhóm công việc** (vì nó quyết định loại bản ghi tương tác đã sinh), còn đội nhỏ làm việc nhanh có thể thu hẹp danh sách. Sàn bắt buộc: danh sách **không được để rỗng**, và **mốc kết thúc** cùng **lịch sử chuyển trạng thái** luôn khóa trong mọi cấu hình.

- **`BR-05.4` (Giao diện phải thể hiện ràng buộc):** Trên màn hình chỉnh sửa công việc đã kết thúc, **các trường đang bị khóa theo cấu hình hiện hành** (`CFG-TASK-11`) phải hiển thị ở dạng **không cho nhập**, kèm chỉ dẫn cách mở lại công việc. Hệ thống **không được** để người dùng nhập xong rồi mới báo lỗi. Khi doanh nghiệp thay đổi danh sách trường bị khóa, giao diện phải phản ánh đúng danh sách mới mà không cần thay đổi gì thêm.

  **Lý do nghiệp vụ:** Để người dùng điền đầy đủ một biểu mẫu rồi mới từ chối là lãng phí công sức và gây ức chế. Ràng buộc phải hiện diện ngay tại thời điểm người dùng bắt đầu thao tác.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-05.1.1` | Công việc đang mở, có nhắc việc hẹn ngày mai | Chuyển sang trạng thái Hoàn thành | Ghi nhận thời điểm hoàn thành. Nhắc việc ngày mai **không** phát |
| `AC-05.1.2` | Công việc gắn Cơ hội bán hàng | Chuyển sang trạng thái thuộc nhánh Hoàn thành | Cơ hội được đánh thức (FEAT-25) |
| `AC-05.1.3` | Công việc gắn Cơ hội bán hàng | Chuyển sang trạng thái thuộc nhánh Hủy bỏ | Cơ hội **không** được đánh thức và dòng thời gian của cơ hội **không** có bản ghi tương tác mới. Lọc danh sách theo nhánh kết thúc thì công việc nằm trong nhóm Hủy bỏ, không nằm trong nhóm Hoàn thành |
| `AC-05.2.1` | Công việc đã hoàn thành ngày 10/03 | Mở lại về trạng thái đang mở | Mốc hoàn thành hiện hành được gỡ. Công việc trở lại luồng xử lý |
| `AC-05.2.2` | Tiếp nối AC-05.2.1, hoàn thành lại ngày 15/03 | Xem lịch sử chuyển trạng thái | Thấy cả hai lần hoàn thành: 10/03 và 15/03. Báo cáo dùng mốc 15/03 |
| `AC-05.3.1` | Công việc đã kết thúc | Sửa tiêu đề và mô tả | Lưu thành công |
| `AC-05.3.2` | Công việc đã kết thúc | Đổi hạn chót | Từ chối: *"Không thể đổi hạn chót của công việc đã kết thúc. Hãy mở lại công việc trước."* |
| `AC-05.3.3` | Công việc đã kết thúc | Đổi người phụ trách qua màn hình sửa thông thường | Từ chối với thông báo tương tự |
| `AC-05.3.4` | Công việc đã kết thúc | Chuyển về trạng thái đang mở **và** đổi hạn chót trong cùng một lần lưu | Lưu thành công. Công việc đang mở với hạn chót mới |
| `AC-05.4.1` | Mở màn hình sửa một công việc đã kết thúc | Quan sát ba trường người phụ trách, hạn chót, nhắc việc | Cả ba hiển thị dạng không cho nhập, kèm chỉ dẫn mở lại công việc |
| `AC-05.4.2` | Tiếp nối AC-05.4.1 | Chuyển trạng thái về đang mở ngay trên biểu mẫu | Ba trường lập tức mở khóa cho nhập, không cần tải lại trang |
| `AC-05.4.3` | Quản trị viên thêm **nhóm công việc** vào danh sách trường bị khóa (`CFG-TASK-11`) | Mở màn hình sửa một công việc đã kết thúc | Nay có **bốn** trường hiển thị dạng không cho nhập, gồm cả nhóm công việc |
| `AC-05.4.4` | Quản trị viên thu hẹp danh sách chỉ còn **hạn chót** | Mở màn hình sửa một công việc đã kết thúc | Chỉ hạn chót bị khóa. Người phụ trách và mốc nhắc việc nhập được bình thường |
| `AC-05.3.5` | Quản trị viên thử xóa hết các trường khỏi danh sách bị khóa | Lưu cấu hình | Từ chối, vì danh sách không được để rỗng |

---

### Nhóm B — Đa Chế độ Hiển thị

#### FEAT-06 — Danh sách Công việc Lọc Nâng cao `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Chế độ xem dạng bảng phục vụ tra cứu, lọc và xử lý hàng loạt — nơi làm việc chính của quản lý.

**Quy tắc nghiệp vụ:**

- **`BR-06.1` (Bộ lọc nghiệp vụ):** Danh sách hỗ trợ lọc theo: người phụ trách, trạng thái, mức ưu tiên, nhóm công việc, nguồn công việc, khoảng hạn chót, nhãn phân loại, loại ngữ cảnh khách hàng, tình trạng quá hạn và từ khóa tìm kiếm tự do.
- **`BR-06.2` (Bộ lọc lưu sẵn):** Người dùng lưu lại tổ hợp bộ lọc thường dùng thành Bộ lọc lưu sẵn, đặt tên riêng và chọn chia sẻ cho nhóm hoặc giữ riêng tư.
- **`BR-06.3` (Tùy biến cột hiển thị):** Người dùng chọn cột nào hiển thị và thứ tự cột. Cấu hình cột là thiết lập cá nhân, không ảnh hưởng người khác. Các trường dữ liệu tùy biến (FEAT-40) cũng chọn được làm cột.
- **`BR-06.4` (Phân trang và sắp xếp):** Danh sách phân trang phía máy chủ. Người dùng sắp xếp theo bất kỳ cột nào có ý nghĩa thứ tự. Mặc định sắp xếp theo hạn chót tăng dần — việc gấp nhất lên trước.
- **`BR-06.5` (Phạm vi dữ liệu):** Danh sách chỉ hiển thị công việc mà người dùng có quyền xem theo Ma trận phân quyền (Mục 5). Bộ lọc không bao giờ là phương tiện mở rộng phạm vi dữ liệu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-06.1.1` | Danh sách công việc | Lọc đồng thời theo người phụ trách và tình trạng quá hạn | Chỉ hiện công việc thỏa mãn cả hai điều kiện |
| `AC-06.2.1` | Đã đặt bộ lọc phức tạp | Lưu thành Bộ lọc lưu sẵn tên "Việc gấp của tôi", chia sẻ cho nhóm | Thành viên nhóm mở được bộ lọc này và thấy dữ liệu trong phạm vi quyền **của chính họ** |
| `AC-06.3.1` | Danh sách công việc | Bỏ hiển thị cột Nguồn công việc, thêm cột trường tùy biến | Cột thay đổi theo lựa chọn. Người dùng khác không bị ảnh hưởng |
| `AC-06.4.1` | Mở danh sách lần đầu | Quan sát thứ tự | Sắp xếp theo hạn chót tăng dần |
| `AC-06.5.1` | Nhân viên chỉ có quyền xem việc của mình | Xóa hết mọi bộ lọc | Vẫn chỉ thấy việc của chính mình, không thấy việc người khác |
| `AC-06.5.2` | Nhân viên chỉ có quyền xem việc của mình | Chủ động đặt bộ lọc "người phụ trách" = một đồng nghiệp khác | Kết quả rỗng hoặc chỉ hiện việc của chính người lọc nếu trùng tên — bộ lọc không trả về việc của đồng nghiệp đó dưới bất kỳ hình thức nào |
| `AC-06.4.2` | Danh sách có 60 công việc, cỡ trang 25 | Mở danh sách và chuyển sang trang 2 rồi trang 3 | Trang 1 và 2 mỗi trang 25 dòng, trang 3 có 10 dòng. Không dòng nào lặp lại giữa các trang |
| `AC-06.4.3` | Danh sách đang sắp theo hạn chót tăng dần, có nhiều trang | Đổi sang sắp theo mức ưu tiên | Thứ tự áp dụng trên **toàn bộ** tập kết quả, không chỉ trang đang xem. Danh sách quay về trang 1 |
| `AC-06.1.2` | Danh sách công việc | Đặt bộ lọc không khớp bản ghi nào | Hiện thông báo không có công việc nào phù hợp, kèm lối xóa nhanh bộ lọc |

---

#### FEAT-07 — Lịch biểu Ngày / Tuần / Tháng `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Nhìn khối lượng công việc theo trục thời gian để sắp xếp lịch làm việc.

**Quy tắc nghiệp vụ:**

- **`BR-07.1` (Ba chế độ xem):** Lịch biểu cung cấp ba chế độ: **Ngày**, **Tuần**, **Tháng**. Công việc hiển thị tại vị trí tương ứng hạn chót của nó.
- **`BR-07.2` (Phân biệt trực quan):** Công việc trên lịch phân màu theo mức ưu tiên (`BR-03.2`) và thể hiện rõ công việc đã kết thúc (`BR-04.2`).
- **`BR-07.3` (Bộ đếm quá hạn theo ngày):** Mỗi ô ngày hiển thị số công việc **đang quá hạn** của ngày đó. Bộ đếm tuân thủ nghiêm ngặt `BR-04.1` — không tính công việc đã kết thúc.
- **`BR-07.4` (Dời hạn bằng kéo thả):** Kéo một công việc sang ô ngày khác sẽ đổi hạn chót sang ngày đó. Thao tác này chịu ràng buộc `BR-05.3` — công việc đã kết thúc không kéo được, và giao diện phải thể hiện điều đó bằng con trỏ không cho thả. Đây là một cách dời hạn chót nên mốc nhắc việc dời theo cùng khoảng, đúng `BR-11.1` — không cần thao tác thêm nào khác.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-07.1.1` | Lịch biểu | Chuyển lần lượt qua ba chế độ Ngày, Tuần, Tháng | Cả ba chế độ hiển thị đúng công việc trong khoảng thời gian tương ứng |
| `AC-07.2.1` | Ô ngày 10/03 có 4 công việc: 1 Khẩn cấp, 1 Thấp, 1 đã Hoàn thành, 1 đã Hủy bỏ | Xem Lịch biểu chế độ Tháng | Bốn công việc phân biệt được theo mức ưu tiên. Hai việc đã kết thúc thể hiện rõ là đã kết thúc và không mang cảnh báo quá hạn |
| `AC-07.3.1` | Ngày 10/03 có 3 việc quá hạn và 2 việc đã hoàn thành quá hạn | Xem bộ đếm ô ngày 10/03 | Bộ đếm hiển thị **3**, không phải 5 |
| `AC-07.4.1` | Công việc đang mở, hạn chót 10/03 | Kéo sang ô ngày 15/03 | Hạn chót đổi thành 15/03 |
| `AC-07.4.2` | Công việc đã hoàn thành | Thử kéo sang ngày khác | Không thả được. Con trỏ thể hiện thao tác bị chặn ngay khi bắt đầu kéo |
| `AC-07.4.3` | Công việc đang mở, hạn chót 10/03 09:00, mốc nhắc việc 08:45 (trước hạn 15 phút) | Kéo sang ô ngày 15/03 | Hạn chót đổi thành 15/03 09:00. Mốc nhắc việc dời theo thành 15/03 08:45, vẫn giữ đúng khoảng 15 phút (`BR-11.1`) |

---

#### FEAT-08 — Bảng Theo dõi Luồng Xử lý `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Bảng trực quan theo dõi luồng xử lý công việc, kéo thả để chuyển trạng thái.

**Quy tắc nghiệp vụ:**

- **`BR-08.1` (Cột dựng theo cấu hình doanh nghiệp):** Các cột được dựng từ danh mục trạng thái của Không gian làm việc, **chỉ lấy trạng thái đang hoạt động**, sắp xếp theo thứ tự mà Quản trị viên đã định nghĩa. Hệ thống tuyệt đối không áp đặt danh sách cột cố định.
- **`BR-08.2` (Kéo thả chuyển trạng thái):** Kéo thẻ sang cột khác chuyển công việc sang trạng thái đó và kích hoạt đầy đủ quy tắc vòng đời tại FEAT-05. Đây là thao tác **đơn lẻ**, nên khi kéo sang cột thuộc nhánh Hoàn thành, hệ thống sinh bản ghi tương tác theo `BR-25.3` như mọi cách hoàn thành công việc khác.
- **`BR-08.3` (Trạng thái bị vô hiệu hóa vẫn còn công việc):** Nếu một trạng thái bị vô hiệu hóa nhưng vẫn còn công việc mang trạng thái đó, bảng hiển thị thêm một cột đặc biệt **"Trạng thái ngừng sử dụng"** gom các công việc này, để chúng không biến mất khỏi tầm nhìn. Cột này chỉ nhận thao tác kéo **ra**, không cho kéo **vào**.

  **Lý do nghiệp vụ:** Nếu công việc biến mất khi Quản trị viên vô hiệu hóa trạng thái, doanh nghiệp mất dấu công việc đang dở mà không có cảnh báo nào.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-08.1.1` | Không gian làm việc định nghĩa 5 trạng thái, 1 trong đó bị vô hiệu hóa | Mở Bảng trạng thái | Hiện 4 cột đang hoạt động, đúng thứ tự Quản trị viên đã định nghĩa |
| `AC-08.1.2` | Quản trị viên đổi thứ tự trạng thái | Mở lại Bảng trạng thái | Thứ tự cột thay đổi theo |
| `AC-08.2.1` | Công việc ở cột "Đang làm" | Kéo sang cột thuộc nhánh Hoàn thành | Công việc chuyển trạng thái, ghi nhận mốc hoàn thành, đánh thức quan hệ khách hàng |
| `AC-08.3.1` | Trạng thái "Chờ duyệt" bị vô hiệu hóa nhưng còn 3 công việc mang trạng thái đó | Mở Bảng trạng thái | Hiện cột "Trạng thái ngừng sử dụng" chứa 3 công việc |
| `AC-08.3.2` | Tiếp nối AC-08.3.1 | Kéo một công việc từ cột đó sang cột đang hoạt động | Thành công. Kéo ngược lại vào cột đó thì không cho phép |

---

### Nhóm C — Phân công & Cộng tác

#### FEAT-09 — Giao việc & Thẩm định Người nhận `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Giao hoặc chuyển công việc đang mở cho nhân sự khác.

**Quy tắc nghiệp vụ:**

- **`BR-09.1` (Thẩm định người nhận việc):** Người nhận việc phải là tài khoản **đang hoạt động** và thuộc **cùng Không gian làm việc**. Hệ thống từ chối giao việc cho tài khoản đã bị vô hiệu hóa hoặc đã rời tổ chức.

  **Về điều kiện cùng Không gian làm việc:** Danh sách chọn người nhận chỉ liệt kê nhân sự thuộc Không gian làm việc hiện tại — đây là hệ quả trực tiếp của ranh giới cách ly dữ liệu (`BR-02.2`, `NFR-05`), không phải một bước kiểm tra riêng có thể thất bại. Điều kiện "đang hoạt động" mới là ràng buộc thực sự cần từ chối, vì tài khoản bị vô hiệu hóa vẫn có thể còn nằm trong cùng danh sách nội bộ.

  **Lý do nghiệp vụ:** Giao việc cho tài khoản đã khóa nghĩa là công việc đó không bao giờ được ai xử lý, nhưng vẫn nằm trong báo cáo như việc đang chạy.

- **`BR-09.2` (Người phụ trách bị vô hiệu hóa sau khi đã được giao việc):** Khi một tài khoản bị vô hiệu hóa, mọi công việc **đang mở** mà tài khoản đó phụ trách được tự động gắn nhãn **"Cần phân công lại"** và xuất hiện trong danh sách cảnh báo của Quản trị viên. Công việc **không bị xóa và không tự chuyển** cho người khác.

  **Lý do nghiệp vụ:** Hệ thống không được tự quyết ai là người kế nhiệm — đó là quyết định của con người. Nhưng cũng không được để công việc chìm vào im lặng.

  **Công việc mẫu định kỳ:** Nếu tài khoản bị vô hiệu hóa còn đang phụ trách công việc mẫu định kỳ, hệ thống **tạm dừng sinh kỳ mới** cho các mẫu đó và đưa chúng vào cùng danh sách "Cần phân công lại". Việc sinh kỳ chỉ tiếp tục sau khi có người phụ trách hợp lệ; các kỳ bị bỏ lỡ trong thời gian tạm dừng **không** được sinh bù, theo tinh thần `BR-12.7`.

  **Lý do nghiệp vụ:** Nếu vẫn sinh kỳ mới, hệ thống tự tạo ra công việc gán cho tài khoản đã khóa — vi phạm chính `BR-09.1`, và không ai xử lý những việc đó.

- **`BR-09.3` (Thông báo khi được giao việc):** Theo cấu hình của Không gian làm việc (`CFG-TASK-22`, mặc định **bật**), người nhận việc được thông báo ngay khi có việc mới được giao, kèm tiêu đề, hạn chót và mức ưu tiên.

  **Lý do nghiệp vụ:** Đây là khẩu vị về nhiễu thông báo, không phải toàn vẹn dữ liệu — công việc vẫn xuất hiện đầy đủ trên Danh sách/Lịch biểu dù có thông báo hay không. Doanh nghiệp có quy trình sinh nhiều công việc tự động mỗi ngày (FEAT-13) có thể muốn tắt để giảm nhiễu; doanh nghiệp còn lại muốn giữ thông báo tức thời cho mọi lần giao việc.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-09.1.1` | Màn hình giao việc | Chọn người nhận là tài khoản đã bị vô hiệu hóa | Từ chối, thông báo tài khoản không còn hoạt động. Danh sách chọn người nhận không liệt kê tài khoản bị vô hiệu hóa |
| `AC-09.2.1` | Nhân viên A đang phụ trách 5 việc đang mở và 10 việc đã xong | Quản trị viên vô hiệu hóa tài khoản A | 5 việc đang mở được gắn nhãn "Cần phân công lại" và hiện trong cảnh báo của Quản trị viên. 10 việc đã xong giữ nguyên |
| `AC-09.2.2` | Tiếp nối AC-09.2.1 | Quản trị viên xem danh sách cảnh báo | Thấy đủ 5 công việc kèm thông tin người phụ trách cũ |
| `AC-09.2.3` | A đang phụ trách 1 công việc mẫu định kỳ hằng tuần | Vô hiệu hóa tài khoản A, rồi chờ qua mốc sinh kỳ tiếp theo | **Không** có công việc con nào được sinh. Mẫu định kỳ xuất hiện trong danh sách "Cần phân công lại" |
| `AC-09.2.4` | Tiếp nối AC-09.2.3, đã bỏ lỡ 2 kỳ | Bàn giao mẫu định kỳ sang B | Việc sinh kỳ tiếp tục từ kỳ kế tiếp. **Không** sinh bù 2 kỳ đã lỡ |
| `AC-09.3.1` | Quản lý giao việc cho nhân viên B, `CFG-TASK-22` đang ở mặc định "Bật" | Lưu công việc | B nhận thông báo ngay, nội dung có tiêu đề, hạn chót và mức ưu tiên |
| `AC-09.3.2` | Quản trị viên tắt `CFG-TASK-22` | Quản lý giao việc cho nhân viên B | Lưu thành công. B **không** nhận thông báo, nhưng công việc vẫn hiện đầy đủ trên Danh sách và Lịch biểu của B |

---

#### FEAT-38 — Bàn giao Công việc khi Thay đổi Nhân sự `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Chuyển toàn bộ công việc của một nhân viên sang người kế nhiệm khi nhân viên nghỉ việc, chuyển bộ phận hoặc nghỉ dài hạn.

*Tính năng này giải quyết Quyết định 2 (Mục 2.5) — tách nghiệp vụ bàn giao khỏi thao tác sửa đổi thông thường.*

**Quy tắc nghiệp vụ:**

- **`BR-38.1` (Phạm vi bàn giao):** Người thực hiện bàn giao chọn phạm vi:
  - **Chỉ công việc đang mở** — mặc định, dùng khi nhân viên chuyển bộ phận;
  - **Toàn bộ công việc, gồm cả đã kết thúc** — dùng khi nhân viên rời tổ chức và cần chuyển giao trọn hồ sơ cho người kế nhiệm.

  Cả hai phạm vi đều **không bao gồm** công việc đang nằm trong Thùng rác (`BR-38.7`). Phạm vi "Toàn bộ" bao gồm cả công việc thuộc nhánh **Hủy bỏ** — người kế nhiệm cần thấy cả những việc đã được quyết định không làm, để không đề xuất lại.

- **`BR-38.2` (Miễn trừ quy tắc khóa sửa đổi):** Bàn giao **được phép** đổi người phụ trách của công việc đã kết thúc, miễn trừ `BR-05.3`. Việc bàn giao **không làm thay đổi** mốc kết thúc, lịch sử chuyển trạng thái, hạn chót hay bất kỳ dữ liệu kết quả nào.

  **Lý do nghiệp vụ:** Đây chính là lối ra khỏi vòng luẩn quẩn của v5.2 — cần chuyển người phụ trách mà không được mở lại công việc, vì mở lại sẽ phá hỏng dữ liệu báo cáo.

- **`BR-38.3` (Bắt buộc ghi nhận lý do và dấu vết):** Mỗi lần bàn giao bắt buộc nhập lý do. Hệ thống ghi nhận: ai thực hiện, thời điểm, người bàn giao đi, người nhận, số lượng công việc, lý do. Dấu vết này không xóa được.

- **`BR-38.4` (Bảo toàn ghi nhận năng suất lịch sử):** Bàn giao **không viết lại** số liệu năng suất đã ghi nhận cho người phụ trách cũ. Báo cáo năng suất theo kỳ đã chốt vẫn phản ánh đúng người thực sự đã làm việc đó.

  **Lý do nghiệp vụ:** Nếu bàn giao viết lại lịch sử năng suất, người kế nhiệm bỗng "có thành tích" của người tiền nhiệm, và báo cáo các kỳ trước thay đổi hồi tố — không chấp nhận được.

- **`BR-38.5` (Đơn vị tổ chức chuyển theo):** Công việc **đang mở** được bàn giao sẽ chuyển sang đơn vị tổ chức của người nhận, theo `BR-01.3`. Công việc **đã kết thúc** giữ nguyên đơn vị tổ chức cũ, để báo cáo lịch sử theo phòng ban không bị xáo trộn. **Đây là ngoại lệ được công nhận của `BR-01.3`.**

  **Ai còn nhìn thấy công việc đã kết thúc sau bàn giao:** Công việc đã kết thúc hiển thị cho người quản lý của **cả hai** đơn vị tổ chức — đơn vị cũ (nơi công việc được thực hiện) và đơn vị của người phụ trách hiện tại. Người phụ trách mới luôn nhìn thấy công việc mình đang đứng tên.

  **Lý do nghiệp vụ:** Không có quy tắc này, công việc rơi vào vùng mù: Trưởng phòng cũ không còn quản người phụ trách, Trưởng phòng mới không thấy vì công việc thuộc phòng cũ. Hồ sơ lịch sử vừa bàn giao lại trở thành thứ không ai truy cập được — đúng vấn đề nghiệp vụ số 3 mà FEAT-38 sinh ra để giải quyết.

- **`BR-38.6` (Bàn giao công việc mẫu định kỳ):** Công việc mẫu định kỳ được bàn giao **cùng với công việc đang mở** (thuộc phạm vi mặc định tại `BR-38.1`). Sau khi bàn giao, mọi kỳ phát sinh **từ thời điểm bàn giao trở đi** thuộc về người nhận; các công việc con **đã sinh ra trước đó** theo đúng phạm vi bàn giao đã chọn.

  **Lý do nghiệp vụ:** Công việc mẫu định kỳ không phải việc cần làm mà là một cam kết lặp lại — nếu không chuyển theo, hệ thống sẽ tiếp tục sinh việc gán cho người đã rời tổ chức, vi phạm trực tiếp `BR-09.1`.

- **`BR-38.7` (Công việc trong Thùng rác):** Công việc đang nằm trong Thùng rác **không** được bàn giao. Nếu công việc đó được khôi phục sau này, nó trở về với người phụ trách cũ và xuất hiện trong danh sách "Cần phân công lại" (`BR-09.2`) nếu người đó đã bị vô hiệu hóa.

- **`BR-38.8` (Quyền thực hiện):** Vai trò thấp nhất được thực hiện bàn giao do Không gian làm việc cấu hình (`CFG-TASK-19`), mặc định là Quản lý cấp phòng ban trở lên (trong phạm vi quản lý của mình), cộng với Quản trị viên Không gian làm việc và Chủ sở hữu ở mọi cấu hình.

  **Lý do nghiệp vụ:** Đây là chính sách phân cấp nội bộ, không phải nghĩa vụ pháp lý — đội ngũ nhỏ, cơ cấu phẳng có thể muốn để Trưởng nhóm tự bàn giao trong phạm vi nhóm mình mà không phải chờ Quản lý; doanh nghiệp khẩu vị rủi ro cao (tài chính, bảo hiểm) có thể muốn siết chặt hơn, chỉ Quản trị viên và Chủ sở hữu được làm. Dấu vết bắt buộc (`BR-38.3`) áp dụng như nhau ở mọi cấu hình nên không phát sinh rủi ro toàn vẹn dữ liệu khi nới lỏng.

- **`BR-38.9` (Thẩm định người nhận bàn giao):** Người nhận bàn giao phải thỏa cùng điều kiện thẩm định người nhận việc tại `BR-09.1`: là tài khoản **đang hoạt động** và thuộc **cùng Không gian làm việc**. Hệ thống từ chối bàn giao sang tài khoản đã bị vô hiệu hóa hoặc đã rời tổ chức.

  **Lý do nghiệp vụ:** Nếu không kiểm tra, một lần bàn giao có thể chuyển hàng chục công việc sang một tài khoản đã khóa — ngay sau khi bàn giao xong, toàn bộ số đó lại rơi vào diện "Cần phân công lại" theo `BR-09.2`, biến một nghiệp vụ nhân sự có chủ đích thành thao tác vô nghĩa và gây hiểu lầm rằng bàn giao đã thành công.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-38.1.1` | Nhân viên A có 5 việc đang mở, 20 việc đã xong | Bàn giao phạm vi "Chỉ công việc đang mở" sang B | 5 việc đang mở chuyển sang B. 20 việc đã xong vẫn thuộc A |
| `AC-38.1.2` | Cùng bối cảnh | Bàn giao phạm vi "Toàn bộ công việc" sang B | Cả 25 việc chuyển sang B |
| `AC-38.2.1` | Tiếp nối AC-38.1.2 | Mở một trong 20 việc đã xong | Người phụ trách là B. Mốc hoàn thành, hạn chót và lịch sử trạng thái **không đổi** |
| `AC-38.3.1` | Thực hiện bàn giao | Bỏ trống lý do | Từ chối, yêu cầu nhập lý do |
| `AC-38.3.2` | Sau khi bàn giao thành công | Xem nhật ký bàn giao | Thấy đủ: người thực hiện, thời điểm, A, B, số lượng, lý do |
| `AC-38.4.1` | A đã hoàn thành 20 việc trong Quý 1, sau đó bàn giao toàn bộ cho B | Mở lịch sử chuyển trạng thái của các việc đó | Vẫn ghi nhận **A** là người đưa công việc sang trạng thái Hoàn thành, kèm thời điểm gốc. Bàn giao không ghi đè dữ liệu này |
| `AC-38.5.1` | A thuộc phòng X, B thuộc phòng Y, bàn giao toàn bộ | Xem đơn vị tổ chức của công việc | Việc đang mở thuộc phòng Y. Việc đã kết thúc vẫn thuộc phòng X |
| `AC-38.6.1` | A phụ trách 1 công việc mẫu định kỳ hằng tuần và 3 công việc con đã sinh, trong đó 1 con đã kết thúc | Bàn giao A sang B, phạm vi **Chỉ công việc đang mở** | Mẫu định kỳ chuyển sang B. 2 công việc con đang mở chuyển sang B. 1 công việc con đã kết thúc **vẫn thuộc A** |
| `AC-38.6.2` | Tiếp nối AC-38.6.1 | Chờ tới kỳ phát sinh tiếp theo | Công việc con mới sinh ra có người phụ trách là **B** |
| `AC-38.6.3` | Cùng bối cảnh AC-38.6.1 | Bàn giao phạm vi **Toàn bộ công việc** | Mẫu định kỳ và **cả 3** công việc con đều chuyển sang B |
| `AC-38.7.1` | A có 4 công việc đang mở và 2 công việc nằm trong Thùng rác | Bàn giao toàn bộ sang B | Chỉ 4 công việc chuyển sang B. 2 công việc trong Thùng rác **vẫn thuộc A**, không xuất hiện trong kết quả bàn giao |
| `AC-38.7.2` | Tiếp nối AC-38.7.1, tài khoản A đã bị vô hiệu hóa | Khôi phục 1 công việc từ Thùng rác | Công việc trở về với A, đồng thời được gắn nhãn **"Cần phân công lại"** và hiện trong danh sách cảnh báo của Quản trị viên |
| `AC-38.8.1` | Nhân viên kinh doanh thường | Thử thực hiện bàn giao | Không thấy chức năng bàn giao trong giao diện |
| `AC-38.8.2` | Trưởng nhóm, `CFG-TASK-19` đang ở mặc định "Quản lý cấp phòng ban trở lên" | Thử thực hiện bàn giao | Không thấy chức năng bàn giao |
| `AC-38.8.4` | Quản trị viên đặt `CFG-TASK-19` = "Trưởng nhóm trở lên" | Trưởng nhóm bàn giao một nhân viên trong nhóm mình phụ trách | Thực hiện thành công |
| `AC-38.8.3` | Quản lý Kinh doanh phòng X | Thử bàn giao một nhân viên thuộc phòng Y | Từ chối, vì nằm ngoài phạm vi quản lý |
| `AC-38.9.1` | Màn hình bàn giao | Chọn người nhận là tài khoản đã bị vô hiệu hóa | Từ chối, thông báo tài khoản không còn hoạt động. Danh sách chọn người nhận không liệt kê tài khoản bị vô hiệu hóa |

---

#### FEAT-11 — Nhắc việc Thời gian thực `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Nhắc đúng người vào đúng thời điểm đã hẹn, để không bỏ lỡ cam kết với khách hàng.

**Quy tắc nghiệp vụ:**

- **`BR-11.1` (Đặt mốc nhắc việc):** Người dùng đặt một mốc nhắc việc cho công việc. Mốc nhắc **phải sớm hơn hoặc bằng** hạn chót. Hệ thống gợi ý mặc định là 15 phút trước hạn chót, điều chỉnh được theo cấu hình của Không gian làm việc (`CFG-TASK-03`).

  **Lý do nghiệp vụ:** Nhắc sau khi đã trễ hạn không còn là nhắc việc, mà là thông báo thất bại.

  **Khi hạn chót thay đổi:** Mỗi khi hạn chót được dời — sớm hơn hay muộn hơn, đơn lẻ hay hàng loạt — mốc nhắc việc **luôn dời theo cùng một khoảng**, giữ nguyên khoảng cách tương đối với hạn chót. Nếu việc dời đẩy mốc nhắc về quá khứ, hệ thống **xóa mốc nhắc** và báo cho người dùng biết để đặt lại nếu cần.

  **Lý do nghiệp vụ:** Mốc nhắc luôn mang ý nghĩa "nhắc tôi trước hạn bao lâu". Người đặt nhắc 15 phút trước hạn vẫn muốn được nhắc 15 phút trước hạn **mới**, chứ không phải giữ nguyên một mốc tuyệt đối nay đã lệch hẳn khỏi công việc. Quy tắc này thống nhất với `BR-28.4` áp dụng cho dời hạn hàng loạt.

  **Với dời hạn hàng loạt:** Thông báo được gộp thành **một thông báo duy nhất** nêu số lượng công việc bị xóa mốc nhắc, kèm danh sách tra cứu được — không phát một thông báo cho mỗi công việc.

- **`BR-11.2` (Người nhận nhắc việc):** Chuông nhắc chỉ đến **người phụ trách** công việc.

  **Cấu hình theo doanh nghiệp (`CFG-TASK-13`):** Doanh nghiệp có thể mở rộng để **quản lý trực tiếp** cũng nhận chuông khi công việc mức **Khẩn cấp** đã quá hạn. Mặc định là chỉ người phụ trách.

  **Lý do nghiệp vụ:** Với ngành có hạn chót mang nghĩa vụ pháp lý — bảo hiểm, tài chính, y tế — việc một hồ sơ khẩn cấp trễ hạn mà cấp trên không biết là rủi ro thật. Ngược lại, quản lý có 40 nhân viên mà nhận mọi chuông sẽ bỏ qua tất cả. Người nhận mở rộng vẫn phải có quyền xem công việc đó theo Ma trận Mục 5, nếu không chuông nhắc trở thành đường rò rỉ dữ liệu vòng qua `BR-31.4`.

- **`BR-11.3` (Nhắc việc có mặt trên toàn ứng dụng):** Chuông nhắc hiển thị ở bất kỳ màn hình nào người dùng đang mở, không chỉ khi đang ở màn hình danh sách công việc. Nội dung gồm tiêu đề, hạn chót và lối tắt mở thẳng công việc.

- **`BR-11.4` (Không nhắc lại việc đã kết thúc):** Công việc đã kết thúc không phát chuông nhắc, kể cả khi mốc nhắc đã được đặt trước đó.

- **`BR-11.5` (Nhắc việc đã phát không phát lại):** Mỗi mốc nhắc chỉ phát đúng một lần. Việc người dùng đang mở nhiều cửa sổ hay hệ thống có nhiều tiến trình xử lý không được làm người dùng nhận nhiều chuông cho cùng một mốc.

- **`BR-11.6` (Bình luận và đề cập đồng nghiệp):** *[Roadmap v7.0]*

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-11.1.1` | Công việc hạn chót 10:00 | Đặt mốc nhắc lúc 11:00 | Từ chối, thông báo mốc nhắc phải sớm hơn hoặc bằng hạn chót |
| `AC-11.1.2` | Công việc hạn chót 10/03, mốc nhắc 09/03 | Dời hạn chót về 05/03 | Mốc nhắc tự dời về 04/03, giữ khoảng cách 1 ngày trước hạn |
| `AC-11.1.3` | Công việc hạn chót 10/03, mốc nhắc 09/03. Hôm nay là 08/03 | Dời hạn chót về 08/03 | Mốc nhắc rơi vào quá khứ nên bị xóa. Hệ thống thông báo để người dùng đặt lại |
| `AC-11.2.1` | Việc của A, quản lý B đang xem | Đến mốc nhắc | Chỉ A nhận chuông. B không nhận |
| `AC-11.3.1` | A đang ở màn hình Cơ hội bán hàng | Đến mốc nhắc một công việc của A | Chuông hiện ngay trên màn hình đang mở, bấm vào mở thẳng công việc |
| `AC-11.4.1` | Công việc có mốc nhắc lúc 15:00, được hoàn thành lúc 14:00 | Đến 15:00 | Không có chuông nào phát |
| `AC-11.5.1` | A mở ứng dụng trên 2 trình duyệt | Đến mốc nhắc | A nhận đúng **một** chuông cho mốc đó |

---

### Nhóm D — Lặp lại & Tự động hóa

#### FEAT-12 — Công việc Định kỳ Bảo toàn Ngày neo `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Tự động sinh công việc theo chu kỳ, phục vụ các nghiệp vụ lặp lại như báo cáo định kỳ, chăm sóc khách hàng hằng tháng, đối soát cuối tháng.

**Quy tắc nghiệp vụ:**

- **`BR-12.1` (Các chu kỳ được hỗ trợ):** Hằng ngày, Hằng tuần, Hằng tháng, Hằng năm. Mỗi chu kỳ kèm khoảng lặp là số nguyên dương (ví dụ: 2 tuần một lần). Người dùng có thể đặt ngày kết thúc lặp lại.

- **`BR-12.2` (Bảo toàn ngày neo — quy tắc cốt lõi):**

  **Ý định nghiệp vụ:** Công việc định kỳ phải luôn quay về đúng ngày mà người dùng đã chọn ban đầu. Một tháng ngắn ngày buộc phải lùi ngày, nhưng việc lùi đó **chỉ có hiệu lực cho kỳ đó**.

  **Quy tắc:** Ngày neo là ngày của kỳ đầu tiên do người dùng chọn. Mỗi kỳ tiếp theo được tính **từ ngày neo gốc**, không tính từ kỳ liền trước. Nếu ngày neo không tồn tại trong tháng đích, kỳ đó rơi vào **ngày cuối cùng của tháng đích**, và **kỳ kế tiếp vẫn quay lại ngày neo gốc**.

  **Ví dụ chuẩn — ngày neo 31, chu kỳ hằng tháng:**

  | Kỳ | Tháng | Ngày phát sinh | Giải thích |
  | --- | --- | --- | --- |
  | 1 | 01/2026 | **31/01** | Ngày neo |
  | 2 | 02/2026 | **28/02** | Tháng 2 không có ngày 31, lùi về ngày cuối tháng |
  | 3 | 03/2026 | **31/03** | **Quay lại ngày neo**, không trôi thành 28/03 |
  | 4 | 04/2026 | **30/04** | Tháng 4 có 30 ngày, lùi về ngày cuối tháng |
  | 5 | 05/2026 | **31/05** | Quay lại ngày neo |

- **`BR-12.3` (Ca biên ngày 29 tháng 2 với chu kỳ hằng năm):** Công việc lặp hằng năm có ngày neo 29/02 sẽ rơi vào **28/02** trong các năm không nhuận, và **quay lại 29/02** khi gặp năm nhuận tiếp theo.

- **`BR-12.4` (Ca biên với khoảng lặp lớn hơn 1):** Quy tắc bảo toàn ngày neo áp dụng đồng nhất. Ví dụ ngày neo 31/01, lặp 2 tháng một lần: kỳ tiếp là 31/03 (không phải 28/03 hay 30/03), kỳ sau nữa là 31/05.

- **`BR-12.5` (Chu kỳ hằng tuần neo theo thứ):** Công việc lặp hằng tuần luôn rơi vào **cùng thứ trong tuần** với kỳ đầu tiên.

- **`BR-12.6` (Không sinh trùng lặp):** Mỗi kỳ chỉ sinh ra đúng **một** công việc, kể cả khi hệ thống vận hành nhiều tiến trình xử lý song song hoặc tiến trình bị chạy lại sau sự cố.

- **`BR-12.7` (Công việc mẫu vào thùng rác):** Khi công việc mẫu định kỳ bị chuyển vào thùng rác, hệ thống **ngừng sinh kỳ mới**. Các công việc con đã sinh ra trước đó **không bị ảnh hưởng** và vẫn xử lý bình thường. Nếu công việc mẫu được khôi phục, việc sinh kỳ mới tiếp tục từ kỳ kế tiếp tính theo ngày neo gốc — **không sinh bù** các kỳ đã bỏ lỡ trong thời gian nằm ở thùng rác.

  **Lý do nghiệp vụ:** Sinh bù hàng loạt công việc quá khứ khi khôi phục sẽ làm ngập danh sách việc của nhân viên bằng những việc không còn ý nghĩa.

  **Cấu hình theo doanh nghiệp (`CFG-TASK-14`):** Doanh nghiệp có lịch định kỳ là **nghĩa vụ hợp đồng** — ví dụ lịch bảo trì thiết bị công nghiệp — có thể bật sinh bù để giữ bằng chứng lịch đầy đủ khi khách hàng kiểm tra, chấp nhận các công việc đó hiện ra ở trạng thái quá hạn. Mặc định là **không sinh bù**. Khi bật, số kỳ sinh bù tối đa bằng thời hạn lưu trữ Thùng rác (`CFG-TASK-07`) và giới hạn này doanh nghiệp không chỉnh được, để một mẫu hằng ngày bị khôi phục sau một năm không sinh ra hàng trăm công việc.

  **Đây là cơ chế tạm dừng chính thức của phiên bản này:** Phiên bản hiện tại **không có** một thao tác "tạm dừng" riêng biệt cho công việc mẫu định kỳ. Đưa vào Thùng rác rồi khôi phục **là cách duy nhất** để tạm ngừng sinh kỳ trong một khoảng thời gian rồi tiếp tục. Doanh nghiệp cần lưu ý thao tác này chịu thời hạn lưu trữ (`CFG-TASK-07`) — nếu không khôi phục trước khi hết hạn, mẫu định kỳ bị xóa vĩnh viễn theo `BR-30.4`. Một cơ chế tạm dừng không qua Thùng rác được ghi nhận là nhu cầu nghiệp vụ hợp lý nhưng thuộc *[Roadmap v7.0]* (`FEAT-43`).

- **`BR-12.8` (Kế thừa thuộc tính):** Công việc con kế thừa từ mẫu: tiêu đề, mô tả, người phụ trách, đơn vị tổ chức, nhóm công việc, nguồn công việc, mức ưu tiên, nhãn, ngữ cảnh khách hàng và giá trị các trường dữ liệu tùy biến.

  **Trạng thái:** Công việc con luôn mang **trạng thái mặc định đang hiệu lực tại thời điểm sinh kỳ**, không kế thừa trạng thái hiện tại của mẫu. Nếu doanh nghiệp đổi trạng thái mặc định giữa chừng, các kỳ sinh sau đó dùng trạng thái mặc định mới; các kỳ đã sinh trước đó không bị ảnh hưởng.

  **Mốc nhắc việc:** Công việc con kế thừa **khoảng cách** giữa mốc nhắc và hạn chót của mẫu, không kế thừa mốc nhắc tuyệt đối. Nếu mẫu không đặt mốc nhắc, công việc con cũng không có.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-12.2.1` | Công việc mẫu "Báo cáo tài chính", hằng tháng, kỳ đầu 31/01/2026 | Chạy sinh kỳ tháng 2 | Công việc con hạn chót **28/02/2026** |
| `AC-12.2.2` | Tiếp nối AC-12.2.1 | Chạy sinh kỳ tháng 3 | Công việc con hạn chót **31/03/2026**, **không phải 28/03** |
| `AC-12.2.3` | Tiếp nối AC-12.2.2 | Chạy sinh kỳ tháng 4 | Công việc con hạn chót **30/04/2026** |
| `AC-12.2.4` | Tiếp nối AC-12.2.3 | Chạy sinh kỳ tháng 5 | Công việc con hạn chót **31/05/2026** |
| `AC-12.3.1` | Công việc mẫu hằng năm, kỳ đầu 29/02/2028 (năm nhuận) | Chạy sinh kỳ năm 2029 | Hạn chót **28/02/2029** |
| `AC-12.3.2` | Tiếp nối AC-12.3.1 | Chạy sinh tới năm 2032 (năm nhuận) | Hạn chót **29/02/2032** |
| `AC-12.4.1` | Ngày neo 31/01/2026, chu kỳ hằng tháng khoảng lặp 2 | Chạy sinh kỳ tiếp theo | Hạn chót **31/03/2026** |
| `AC-12.5.1` | Công việc mẫu hằng tuần, kỳ đầu là Thứ Hai | Chạy sinh 4 kỳ liên tiếp | Cả 4 kỳ đều rơi vào Thứ Hai |
| `AC-12.6.1` *(kiểm thử kỹ thuật)* | Kích hoạt tiến trình sinh kỳ **hai lần đồng thời** cho cùng một công việc mẫu đang đến kỳ | Quan sát danh sách công việc con | Chỉ **một** công việc con được tạo. Ca này cần môi trường kiểm thử dựng được nhiều tiến trình song song |
| `AC-12.7.1` | Công việc mẫu đã sinh 3 công việc con | Chuyển công việc mẫu vào thùng rác | 3 công việc con vẫn còn và xử lý được. Không sinh thêm kỳ mới |
| `AC-12.7.2` | Tiếp nối AC-12.7.1, sau 2 kỳ bị bỏ lỡ | Khôi phục công việc mẫu | Sinh tiếp từ kỳ kế tiếp theo ngày neo. **Không** sinh bù 2 kỳ đã lỡ |
| `AC-12.8.1` | Công việc mẫu đang ở trạng thái "Đang làm" | Sinh kỳ mới | Công việc con mang **trạng thái mặc định**, không phải "Đang làm" |
| `AC-12.8.2` | Mẫu định kỳ có đủ: mô tả, người phụ trách, nhóm công việc, nguồn công việc, mức ưu tiên Cao, hai nhãn, gắn Khách hàng X, một trường tùy biến có giá trị | Sinh kỳ mới | Công việc con mang đúng toàn bộ các thông tin trên, giống hệt mẫu |
| `AC-12.8.3` | Mẫu định kỳ có hạn chót và mốc nhắc đặt trước hạn 2 giờ | Sinh kỳ mới | Công việc con có mốc nhắc **trước hạn chót của chính nó 2 giờ**, không phải mốc nhắc tuyệt đối của mẫu |
| `AC-12.8.4` | Đã sinh kỳ tháng 3 với trạng thái mặc định là "Cần làm". Quản trị viên đổi trạng thái mặc định sang "Mới tiếp nhận" | Sinh kỳ tháng 4 | Công việc con tháng 4 mang trạng thái "Mới tiếp nhận". Công việc con tháng 3 **không đổi** |
| `AC-12.1.1` | Mẫu định kỳ hằng tháng, đặt ngày kết thúc lặp là 30/06 | Chạy sinh kỳ cho tháng 6 rồi tháng 7 | Kỳ tháng 6 được sinh. Kỳ tháng 7 **không** được sinh, mẫu ngừng hoạt động |
| `AC-12.1.2` | Mẫu định kỳ hằng tuần, khoảng lặp 2 | Chạy sinh 3 kỳ liên tiếp | Ba kỳ cách nhau đúng **hai tuần**, cùng thứ trong tuần |

---

#### FEAT-13 — Sinh Công việc Tự động từ Quy trình `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Quy trình tự động hóa của doanh nghiệp tạo công việc mà không cần con người thao tác — ví dụ: khi cơ hội chuyển sang giai đoạn Đàm phán thì tự tạo việc "Chuẩn bị hợp đồng" cho người phụ trách cơ hội.

**Quy tắc nghiệp vụ:**

- **`BR-13.1` (Công việc sinh tự động tuân thủ đầy đủ quy tắc nghiệp vụ):** Công việc do quy trình tạo ra phải chịu **toàn bộ** ràng buộc như công việc do người dùng tạo: thẩm định ngữ cảnh khách hàng (`BR-02.2`), thẩm định người phụ trách (`BR-09.1`), gán đơn vị tổ chức (`BR-01.3`), ghi nhận dấu vết.

  **Lý do nghiệp vụ:** Công việc xuất hiện mà không ai thao tác chính là loại công việc cần dấu vết nhất — vì khi có sự cố, không ai nhớ nó từ đâu ra.

- **`BR-13.2` (Ghi nhận nguồn gốc):** Công việc sinh tự động ghi rõ quy tắc nào đã tạo ra nó, hiển thị được trên màn hình chi tiết công việc.

- **`BR-13.3` (Công việc làm điều kiện kích hoạt quy trình):** Các sự kiện trên công việc — tạo mới, đổi trạng thái, đổi người phụ trách, hoàn thành, quá hạn — đều dùng được làm điều kiện kích hoạt cho quy trình tự động hóa khác.

- **`BR-13.5` (Xử lý khi quy trình không xác định được người phụ trách):** Áp dụng `BR-01.2`/`CFG-TASK-18` như công việc do người dùng tạo, với hai khác biệt vì quy trình tự động hóa không có người tạo là một người dùng thật:
  - Nếu cấu hình đang là **Tự gán người tạo**, công việc được tạo ở trạng thái **Chưa phân công** thay vì bị gán nhầm cho một tài khoản hệ thống.
  - Nếu cấu hình đang là **Bắt buộc chọn tường minh**, quy trình **không tạo công việc**, sự kiện được ghi vào nhật ký quy trình kèm tên quy trình để Quản trị viên xử lý.

- **`BR-13.4` (Chặn vòng lặp vô hạn):** Hệ thống phải phát hiện và chặn trường hợp một quy trình tạo công việc, công việc đó lại kích hoạt chính quy trình đó. Chuỗi kích hoạt nối tiếp bị dừng khi vượt **10 bước** (`CFG-TASK-17`), và sự kiện bị chặn được ghi vào nhật ký quy trình kèm tên quy trình gây ra vòng lặp.

  **Lý do nghiệp vụ:** Phải có một con số cụ thể thì đội phát triển mới cài đặt được và đội kiểm thử mới nghiệm thu được. Mười bước đủ rộng cho các chuỗi quy trình hợp lệ mà doanh nghiệp thực sự dùng, đồng thời đủ hẹp để chặn vòng lặp trước khi nó sinh ra hàng nghìn công việc rác.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-13.1.1` | Quy trình tạo việc gán cho tài khoản đã bị vô hiệu hóa | Quy trình chạy | Không tạo công việc. Ghi nhận lỗi vào nhật ký quy trình để Quản trị viên xử lý |
| `AC-13.2.1` | Công việc được tạo bởi quy trình "Cơ hội vào Đàm phán" | Mở chi tiết công việc | Thấy thông tin công việc do quy trình nào tạo ra |
| `AC-13.3.1` | Có quy trình kích hoạt khi công việc hoàn thành | Hoàn thành một công việc | Quy trình được kích hoạt |
| `AC-13.4.1` | Cấu hình một quy trình tạo công việc, và chính sự kiện tạo công việc đó lại kích hoạt quy trình này | Kích hoạt quy trình một lần | Hệ thống dừng chuỗi sau **10 bước**, không tạo thêm công việc nào nữa. Nhật ký quy trình ghi sự kiện bị chặn kèm tên quy trình gây vòng lặp |

---

### Nhóm E — Ghi nhận Tương tác Hoạt động

#### FEAT-15 đến FEAT-19 — Ghi nhận Tương tác trên Hồ sơ Khách hàng `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Lưu lại lịch sử mọi tương tác **đã diễn ra** với khách hàng, để khi bàn giao hoặc khi người khác tiếp nhận, toàn bộ bối cảnh vẫn còn nguyên.

| Mã | Loại tương tác | Thông tin nghiệp vụ ghi nhận |
| --- | --- | --- |
| `FEAT-15` | **Cuộc gọi** | Chiều gọi (đi/đến), thời lượng, kết quả cuộc gọi, nội dung trao đổi, đường dẫn tệp ghi âm nếu có |
| `FEAT-16` | **Cuộc họp** | Hình thức (trực tiếp/trực tuyến), thời gian, danh sách người tham dự, đường dẫn phòng họp, biên bản |
| `FEAT-17` | **Email** | Chủ đề, nội dung, người nhận, thời điểm gửi, trạng thái đã mở xem nếu theo dõi được |
| `FEAT-18` | **Ghi chú nội bộ** | Nội dung định dạng giàu, một hoặc nhiều tệp đính kèm |
| `FEAT-19` | **Tin nhắn** | Kênh (SMS / Zalo / WhatsApp), nội dung, chiều gửi |

**Quy tắc nghiệp vụ:**

- **`BR-15.1` (Bản ghi tương tác là bất biến):** Một khi đã ghi nhận, nội dung bản ghi tương tác **không được sửa và không được xóa**. Nếu ghi sai, người dùng tạo bản ghi đính chính mới; bản ghi gốc vẫn còn.

  **Lý do nghiệp vụ:** Đây là bằng chứng về điều đã xảy ra với khách hàng, thường được dùng khi có tranh chấp. Bằng chứng sửa được thì không còn là bằng chứng.

- **`BR-15.2` (Luôn gắn với một hồ sơ):** Mọi bản ghi tương tác phải gắn với Khách hàng, Doanh nghiệp hoặc Cơ hội bán hàng. Không có bản ghi tương tác trôi nổi.

- **`BR-15.3` (Ghi nhận người thực hiện và thời điểm):** Mỗi bản ghi lưu người thực hiện tương tác và thời điểm tương tác **thực tế diễn ra** — có thể khác thời điểm nhập liệu. Người dùng được phép nhập lùi thời điểm cho tương tác đã xảy ra trước đó.

- **`BR-15.4` (Kích hoạt đánh thức quan hệ):** Mọi bản ghi tương tác được tạo đều kích hoạt đánh thức quan hệ với thực thể liên quan (FEAT-25).

- **`BR-15.5` (Cấu trúc dữ liệu chuyên biệt theo loại):** *[Roadmap v7.0]* — Hiện tại các thông tin đặc thù được lưu dưới dạng nội dung tự do có cấu trúc nhẹ.

- **`BR-15.6` (Thu hồi bản ghi ghi nhầm):** Vai trò thấp nhất được thu hồi bản ghi tương tác do Không gian làm việc cấu hình (`CFG-TASK-20`), mặc định là Quản trị viên Không gian làm việc, cộng với Chủ sở hữu ở mọi cấu hình. Người có quyền thu hồi bắt buộc nhập lý do. Bản ghi bị thu hồi **không bị xóa**: nó biến mất khỏi dòng thời gian, nhưng vẫn tra cứu được đầy đủ trong dấu vết hệ thống cùng lý do thu hồi và người thực hiện.

  **Lý do nghiệp vụ về quyền thực hiện:** Cơ chế thu hồi tự nó đã bảo vệ toàn vẹn dữ liệu (không xóa, có dấu vết, bắt buộc lý do) bất kể ai thực hiện — giới hạn "ai được thu hồi" chỉ là chính sách phân cấp nội bộ. Doanh nghiệp nhỏ không có Quản trị viên chuyên trách có thể muốn để Quản lý hoặc Giám đốc Kinh doanh tự xử lý ngay bản ghi sai của phòng mình; doanh nghiệp tuân thủ chặt muốn giữ nguyên ở cấp cao nhất.

  **Thu hồi không hoàn tác việc đánh thức:** Thời điểm tương tác gần nhất của khách hàng **giữ nguyên** sau khi thu hồi — khách hàng không quay lại trạng thái nguội lạnh. Điều này thống nhất với `BR-25.4` và `BR-25.5`: việc đánh thức đã xảy ra là sự kiện quá khứ, không bao giờ hoàn tác.

  **Lý do nghiệp vụ:** Đưa một khách hàng trở lại trạng thái nguội lạnh vì lý do hành chính sẽ khiến đội kinh doanh lao vào chăm sóc một quan hệ thực ra vẫn đang được xử lý bình thường. Thu hồi là để sửa hồ sơ, không phải để đảo ngược thời gian.

  **Phạm vi áp dụng:** Thu hồi được cho **mọi** bản ghi tương tác, kể cả bản ghi tự sinh từ `BR-25.3`. Thu hồi bản ghi tự sinh **không** thay đổi trạng thái của công việc nguồn.

  **Lý do nghiệp vụ:** Tính bất biến tại `BR-15.1` là đúng và **không được cấu hình bật/tắt** — bằng chứng sửa được thì không còn là bằng chứng. Nhưng vận hành thật vẫn có ca một tích hợp lỗi ghi nhầm hàng trăm bản ghi vào sai hồ sơ khách hàng; nếu không có lối ra nào, hồ sơ đó nhiễm bẩn vĩnh viễn. Thu hồi có kiểm soát giải quyết được ca đó mà không phá nguyên tắc: dữ liệu không bao giờ mất, chỉ ngừng được coi là hợp lệ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-15.1.1` | Đã ghi nhận một cuộc gọi | Thử sửa nội dung bản ghi | Không có chức năng sửa. Chỉ có tùy chọn tạo bản ghi đính chính |
| `AC-15.2.1` | Màn hình ghi nhận tương tác | Lưu mà không chọn hồ sơ liên quan | Từ chối, yêu cầu chọn hồ sơ |
| `AC-15.3.1` | Hôm nay là 17/09, ghi nhận cuộc gọi đã diễn ra ngày 15/09 | Chọn thời điểm tương tác 15/09, lưu | Bản ghi hiện trên dòng thời gian tại vị trí ngày 15/09 |
| `AC-15.4.1` | Cơ hội bán hàng đang bị cảnh báo nguội lạnh | Ghi nhận một cuộc gọi trên cơ hội đó | Cảnh báo nguội lạnh biến mất |
| `AC-15.1.2` | Đã ghi nhận một cuộc gọi | Thử xóa bản ghi | Không có chức năng xóa với người dùng thường |
| `AC-15.1.3` | Hồ sơ Khách hàng X | Ghi nhận một cuộc gọi **đến** từ khách hàng: thời lượng 8 phút, kết quả "Không nghiệm thu, cần gọi lại" | Bản ghi hiện trên dòng thời gian, đánh dấu chiều **đến**, hiển thị đúng thời lượng và kết quả cuộc gọi |
| `AC-15.1.4` | Hồ sơ Khách hàng X | Ghi nhận một cuộc gọi kèm đường dẫn tệp ghi âm | Bản ghi hiện trên dòng thời gian, đường dẫn ghi âm mở nghe được |
| `AC-15.6.1` | Một tích hợp ghi nhầm 200 bản ghi vào sai khách hàng | Quản trị viên thu hồi các bản ghi đó, nhập lý do | Các bản ghi biến mất khỏi dòng thời gian của khách hàng đó |
| `AC-15.6.2` | Tiếp nối AC-15.6.1 | Quản trị viên mở dấu vết hệ thống | Vẫn tra cứu được đầy đủ nội dung bản ghi, người thu hồi, thời điểm và lý do |
| `AC-15.6.3` | Nhân viên Kinh doanh hoặc Chuyên viên Hỗ trợ, `CFG-TASK-20` đang ở mặc định | Tìm chức năng thu hồi bản ghi | Không thấy chức năng này. Chỉ Quản trị viên Không gian làm việc và Chủ sở hữu có |
| `AC-15.6.5` | Quản trị viên đặt `CFG-TASK-20` = "Quản lý cấp phòng ban trở lên" | Quản lý Kinh doanh thu hồi một bản ghi tương tác của phòng mình | Thực hiện thành công, nhập lý do |
| `AC-16.1.1` | Hồ sơ Khách hàng X | Ghi nhận một **cuộc họp**: hình thức trực tuyến, thời gian, hai người tham dự, đường dẫn phòng họp, biên bản | Bản ghi hiện trên dòng thời gian của X, hiển thị đúng hình thức, thời gian, cả hai người tham dự theo tên, đường dẫn phòng họp bấm vào được, và nội dung biên bản |
| `AC-16.1.2` | Màn hình ghi nhận cuộc họp | Lưu mà chỉ nhập hình thức và thời gian, bỏ trống người tham dự và biên bản | Lưu thành công — chỉ hồ sơ liên quan và loại tương tác là bắt buộc (`BR-15.2`) |
| `AC-16.1.3` | Hồ sơ Khách hàng X | Ghi nhận cuộc họp hình thức **trực tiếp** và một cuộc họp khác hình thức **trực tuyến** | Dòng thời gian phân biệt rõ hai hình thức bằng nhãn chữ, không chỉ bằng biểu tượng |
| `AC-17.1.1` | Hồ sơ Khách hàng X | Ghi nhận một **email đã gửi đi**: chủ đề, nội dung, người nhận, thời điểm gửi | Bản ghi hiện trên dòng thời gian của X, đánh dấu chiều **gửi đi**, hiển thị đúng người nhận |
| `AC-17.1.2` | Email đã ghi nhận, phân hệ gửi email báo về trạng thái đã mở xem | Xem bản ghi email đó | Hiển thị trạng thái đã mở xem. Phân hệ này chỉ hiển thị lại trạng thái nhận được, không tự theo dõi (Mục 1.2) |
| `AC-17.1.3` | Khách hàng X gửi một email đến hộp thư chung của doanh nghiệp | Ghi nhận email đó vào hồ sơ X | Bản ghi hiện trên dòng thời gian, đánh dấu chiều **nhận được**, hiển thị đúng địa chỉ người gửi là X, phân biệt được với các bản ghi chiều gửi đi |
| `AC-18.1.1` | Hồ sơ Khách hàng X | Ghi nhận một **ghi chú nội bộ** có định dạng giàu (in đậm, gạch đầu dòng) kèm một tệp đính kèm | Ghi chú hiện trên dòng thời gian, giữ nguyên định dạng in đậm và gạch đầu dòng khi mở lại, tệp đính kèm tải về được và mở đúng nội dung đã tải lên |
| `AC-18.1.2` | Hồ sơ Khách hàng X | Ghi nhận một ghi chú nội bộ kèm **hai** tệp đính kèm | Cả hai tệp đều hiện trên bản ghi, tải về được độc lập với nhau |
| `AC-19.1.1` | Hồ sơ Khách hàng X | Ghi nhận một **tin nhắn** kênh Zalo, chiều gửi đi, nội dung | Bản ghi hiện trên dòng thời gian, ghi rõ kênh Zalo và chiều gửi |
| `AC-19.1.2` | Đã ghi nhận tin nhắn ở ba kênh khác nhau trên cùng khách hàng: SMS, Zalo, WhatsApp | Lọc dòng thời gian theo loại tin nhắn | Hiện đủ ba bản ghi, mỗi bản ghi ghi rõ đúng tên kênh của nó (SMS, Zalo hoặc WhatsApp) |
| `AC-19.1.3` | Hồ sơ Khách hàng X | Ghi nhận một tin nhắn kênh SMS, chiều **đến** từ khách hàng | Bản ghi hiện trên dòng thời gian, ghi rõ kênh SMS và chiều đến |

---

### Nhóm G — Cảnh báo Quá hạn & Đánh thức Quan hệ

#### FEAT-24 — Quét Hạn chót & Nhắc việc Tự động `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Tiến trình nền chủ động phát chuông nhắc khi công việc đến mốc đã hẹn.

**Quy tắc nghiệp vụ:**

- **`BR-24.1` (Tần suất quét):** Hệ thống quét các mốc nhắc việc đến hạn theo chu kỳ **không quá 5 phút một lần**, bảo đảm độ trễ tối đa mà người dùng cảm nhận được là 5 phút.

- **`BR-24.2` (Điều kiện phát chuông):** Chuông chỉ phát cho công việc thỏa mãn đồng thời: đã đến mốc nhắc, chưa từng phát chuông cho mốc đó, công việc chưa kết thúc, công việc chưa bị xóa.

- **`BR-24.3` (Bỏ qua nhắc việc quá cũ):** Nếu hệ thống vừa phục hồi sau gián đoạn và phát hiện các mốc nhắc đã trễ **quá 24 giờ**, những mốc đó được **bỏ qua** thay vì phát dồn (`CFG-TASK-04`).

  **Lý do nghiệp vụ:** Phát hàng loạt chuông của các mốc đã trôi qua từ lâu chỉ gây nhiễu, không còn giá trị nhắc nhở, và làm người dùng bỏ qua cả những chuông thật sự quan trọng.

- **`BR-24.4` (Ghi nhận nhắc việc bị bỏ qua):** Các mốc nhắc bị bỏ qua theo `BR-24.3` được ghi vào nhật ký vận hành để Quản trị viên nắm được quy mô ảnh hưởng sau sự cố.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-24.1.1` | Công việc có mốc nhắc lúc 10:00 | Chờ tới 10:00 | Người phụ trách nhận chuông chậm nhất lúc 10:05 |
| `AC-24.2.1` | Công việc đã phát chuông lúc 10:00 | Tiến trình quét chạy lại lúc 10:05 | Không phát chuông lần hai |
| `AC-24.3.1` *(kiểm thử kỹ thuật)* | Một công việc có mốc nhắc cách đây 3 ngày và chưa từng phát chuông — dựng bằng cách tạo công việc rồi đặt mốc nhắc lùi về quá khứ trong môi trường kiểm thử | Kích hoạt tiến trình quét nhắc việc | Mốc nhắc đó **không** phát chuông |
| `AC-24.3.2` | Cùng bối cảnh, có mốc nhắc từ 2 giờ trước | Hệ thống phục hồi | Mốc nhắc đó **có** phát chuông |
| `AC-24.4.1` | Tiếp nối AC-24.3.1 | Quản trị viên xem nhật ký vận hành | Thấy số lượng nhắc việc đã bị bỏ qua |

---

#### FEAT-25 — Đánh thức Quan hệ Khách hàng `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Gỡ bỏ cảnh báo nguội lạnh khi có hoạt động chăm sóc thực sự phát sinh — giải quyết vấn đề nghiệp vụ số 5 (Mục 2.1).

**Quy tắc nghiệp vụ:**

- **`BR-25.1` (Đánh thức từ Nhật ký Tương tác):** Mỗi khi một bản ghi tương tác được tạo trên Khách hàng, Doanh nghiệp hoặc Cơ hội bán hàng, thực thể đó được ghi nhận là vừa có hoạt động chăm sóc.

- **`BR-25.2` (Đánh thức khi Hoàn thành Công việc):** Khi một công việc có gắn ngữ cảnh khách hàng chuyển sang trạng thái thuộc nhánh **Hoàn thành**, thực thể ngữ cảnh đó được đánh thức.

  **Chỉ nhánh Hoàn thành mới đánh thức.** Công việc chuyển sang nhánh **Hủy bỏ** **không** đánh thức quan hệ — hủy một việc không phải là chăm sóc khách hàng.

- **`BR-25.3` (Sinh bản ghi tương tác khi hoàn thành công việc):** Khi người dùng hoàn thành **từng công việc một** và công việc đó có gắn ngữ cảnh khách hàng, hệ thống tự sinh một bản ghi tương tác tương ứng trên dòng thời gian của thực thể đó. Loại bản ghi được xác định theo Nhóm công việc (`BR-01.4`): nhóm Gọi điện sinh bản ghi cuộc gọi, nhóm Họp sinh bản ghi cuộc họp, và tương tự.

  **Phạm vi áp dụng — chỉ thao tác đơn lẻ:** Quy tắc này áp dụng khi người dùng hoàn thành **một công việc tại một thời điểm**, bất kể làm từ màn hình nào: đánh dấu hoàn tất trên Danh sách, kéo thẻ sang cột kết thúc trên Bảng theo dõi, hay đổi trạng thái trong màn hình chi tiết. Ba trường hợp **không** sinh bản ghi tương tác:
  - Đổi trạng thái **hàng loạt** (`BR-27.6`);
  - Chuyển trạng thái do **vô hiệu hóa danh mục** (`BR-37.3`);
  - Công việc thuộc **nhóm đã được cấu hình tắt** (`BR-25.7`).

  **Lý do phân biệt:** Người dùng hoàn thành một công việc là hành động có chủ đích trên một việc cụ thể — họ vừa thực sự gọi cho khách hàng đó. Đóng hàng loạt hay dọn danh mục là thao tác quản trị trên một tập hợp, không tương ứng với bất kỳ tương tác thật nào.

  **Công việc không có Nhóm công việc:** Nhóm công việc là tùy chọn (`BR-01.4`). Nếu công việc không thuộc nhóm nào, hệ thống **không sinh bản ghi tương tác** — vì không xác định được đó là cuộc gọi, cuộc họp hay loại nào. Việc đánh thức quan hệ (`BR-25.2`) vẫn diễn ra bình thường, và dòng thời gian vẫn ghi nhận sự kiện công việc đã hoàn thành theo `BR-31.2`.

  **Nhóm công việc đã bị vô hiệu hóa:** Công việc vẫn giữ nhóm cũ nên vẫn sinh bản ghi đúng loại. Việc doanh nghiệp ngừng dùng một nhóm cho công việc **mới** không làm mất ý nghĩa của các công việc **đang có**.

  **Mỗi lần hoàn thành sinh một bản ghi:** Nếu công việc được mở lại rồi hoàn thành lần nữa, hệ thống sinh **một bản ghi tương tác mới** cho lần hoàn thành đó. Bản ghi của lần trước vẫn còn (`BR-25.4`).

  **Lý do nghiệp vụ:** Mở lại rồi hoàn thành lại thường có nghĩa nhân viên đã liên hệ khách hàng thêm một lần nữa — ví dụ gọi lần đầu khách không nghe máy, gọi lại hôm sau mới nói chuyện được. Đó là hai tương tác thật, và dòng thời gian phải phản ánh cả hai. Nếu chỉ ghi một lần, lịch sử chăm sóc sẽ thiếu đúng những nỗ lực mà nhân viên bỏ ra nhiều nhất.

  **Lý do nghiệp vụ:** Đây là cầu nối giữa Công việc (việc sẽ làm) và Nhật ký Tương tác (việc đã làm) theo Nguyên tắc 1. Không có quy tắc này, nhân viên phải nhập liệu hai lần cho cùng một hành động, và thực tế là họ sẽ không nhập lần thứ hai — dẫn tới đứt gãy lịch sử chăm sóc.

- **`BR-25.4` (Mở lại công việc không hoàn tác việc đánh thức):** Khi công việc đã hoàn thành bị mở lại, **không** hoàn tác việc đánh thức và **không** xóa bản ghi tương tác đã sinh.

  **Lý do nghiệp vụ:** Hoạt động chăm sóc đã thực sự diễn ra. Việc công việc được mở lại để làm thêm không phủ nhận điều đã xảy ra, và bản ghi tương tác là bất biến theo `BR-15.1`.

- **`BR-25.5` (Xóa công việc không hoàn tác việc đánh thức):** Tương tự `BR-25.4`.

- **`BR-25.6` (Tham số cấu hình đánh thức):** Việc tự động đánh thức khi hoàn thành công việc (`BR-25.2`) có thể **tắt** theo cấu hình của từng Không gian làm việc (`CFG-TASK-08`). Khi tắt, chỉ `BR-25.1` còn hiệu lực. Mặc định là **bật**.

- **`BR-25.7` (Tham số cấu hình sinh bản ghi tương tác):** Việc tự động sinh bản ghi tương tác (`BR-25.3`) có thể **tắt theo từng Nhóm công việc** (`CFG-TASK-09`), không tắt được toàn bộ. Mặc định: bật cho mọi nhóm.

  **Lý do nghiệp vụ:** Doanh nghiệp đã có tích hợp tổng đài tự ghi nhận mọi cuộc gọi sẽ bị trùng bản ghi nếu hệ thống sinh thêm khi công việc nhóm "Gọi điện" hoàn thành — họ cần tắt riêng nhóm đó. Nhưng cho phép tắt toàn bộ là để doanh nghiệp tự chọn lấy vấn đề nghiệp vụ số 3 (đứt gãy lịch sử chăm sóc) mà `BR-25.3` được viết ra để giải quyết. Vì vậy tham số này hẹp theo nhóm, không phải công tắc tổng.

  Khi một nhóm bị tắt sinh bản ghi, việc **đánh thức quan hệ** (`BR-25.2`) vẫn hoạt động bình thường — hai cơ chế độc lập với nhau.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-25.2.1` | Cơ hội bán hàng 20 ngày không có tương tác, đang bị cảnh báo nguội lạnh. Có công việc "Gọi tái kết nối" gắn cơ hội này | Hoàn thành công việc | Cảnh báo nguội lạnh biến mất ngay trên màn hình quản lý cơ hội |
| `AC-25.2.2` | Cùng bối cảnh | Chuyển công việc sang trạng thái nhánh Hủy bỏ | Cảnh báo nguội lạnh **vẫn còn**. Cơ hội không được đánh thức |
| `AC-25.2.3` | Công việc không gắn ngữ cảnh khách hàng | Hoàn thành công việc | Không có thực thể nào được đánh thức. Không phát sinh lỗi |
| `AC-25.3.1` | Công việc nhóm "Gọi điện", gắn Khách hàng X | Hoàn thành công việc | Dòng thời gian của X xuất hiện bản ghi loại cuộc gọi, ghi nhận đúng người thực hiện và thời điểm |
| `AC-25.3.2` | Công việc nhóm "Họp", gắn Cơ hội Y | Hoàn thành công việc từ màn hình chi tiết | Dòng thời gian của Y xuất hiện bản ghi loại cuộc họp |
| `AC-25.3.3` | Công việc nhóm "Gọi điện", gắn Khách hàng X, đang ở Bảng theo dõi luồng xử lý | Kéo thẻ sang cột thuộc nhánh Hoàn thành | Dòng thời gian của X xuất hiện bản ghi loại cuộc gọi — giống hệt khi hoàn thành từ Danh sách |
| `AC-25.3.4` | Công việc gắn Khách hàng X, không thuộc nhóm nào | Hoàn thành công việc | Quan hệ với X được đánh thức. Dòng thời gian ghi nhận sự kiện hoàn thành công việc nhưng **không** sinh bản ghi tương tác loại nào |
| `AC-25.3.5` | Công việc nhóm "Gọi điện" gắn Khách hàng X, đã hoàn thành ngày 10/03 và đã sinh 1 bản ghi | Mở lại công việc, rồi hoàn thành lại ngày 15/03 | Dòng thời gian của X có **hai** bản ghi cuộc gọi: một ngày 10/03, một ngày 15/03 |
| `AC-25.3.6` | Nhóm "Gọi điện" đã bị vô hiệu hóa, còn công việc đang mở thuộc nhóm đó | Hoàn thành công việc | Vẫn sinh bản ghi **loại cuộc gọi** như bình thường |
| `AC-15.6.4` | Bản ghi tương tác tự sinh từ một công việc đã hoàn thành, khách hàng X đang hết cảnh báo nguội lạnh | Quản trị viên thu hồi bản ghi đó | Bản ghi biến mất khỏi dòng thời gian. Khách hàng X **không** quay lại trạng thái nguội lạnh. Công việc nguồn **vẫn** ở trạng thái Hoàn thành |
| `AC-25.4.1` | Tiếp nối AC-25.3.1 | Mở lại công việc | Bản ghi tương tác **vẫn còn** trên dòng thời gian. Cơ hội không quay lại trạng thái nguội lạnh |
| `AC-25.6.1` | Không gian làm việc tắt tham số đánh thức tự động | Hoàn thành công việc gắn cơ hội | Cơ hội **không** được đánh thức. Ghi nhận tương tác thủ công vẫn đánh thức bình thường |
| `AC-25.7.1` | Tắt sinh bản ghi tương tác cho riêng nhóm "Gọi điện" | Hoàn thành công việc nhóm "Gọi điện" gắn khách hàng X | Dòng thời gian của X **không** có bản ghi mới. Quan hệ với X **vẫn** được đánh thức |
| `AC-25.7.2` | Cùng bối cảnh | Hoàn thành công việc nhóm "Họp" gắn khách hàng X | Dòng thời gian của X **có** bản ghi loại cuộc họp. Việc tắt chỉ áp dụng cho nhóm đã chọn |
| `AC-25.7.3` | Màn hình cấu hình | Tìm cách tắt sinh bản ghi tương tác cho toàn bộ nhóm công việc cùng lúc | Không có tùy chọn tắt toàn bộ. Chỉ chọn được theo từng nhóm |

---

### Nhóm H — Thao tác Hàng loạt & Xuất Dữ liệu

#### FEAT-27 — Cập nhật & Xóa Hàng loạt `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Xử lý nhiều công việc cùng lúc, phục vụ nhu cầu điều phối của quản lý mà không phải thao tác từng dòng.

**Quy tắc nghiệp vụ:**

- **`BR-27.1` (Giới hạn quy mô):** Mỗi thao tác hàng loạt xử lý tối đa **200 công việc** (`CFG-TASK-06`). Giới hạn này phải được **thể hiện rõ trên giao diện** ngay khi người dùng chọn vượt ngưỡng, không để người dùng thao tác xong mới báo lỗi.

- **`BR-27.2` (Kiểm soát quyền theo từng dòng):** Mỗi công việc trong lô được kiểm tra quyền độc lập. Công việc không đủ quyền được **bỏ qua** và liệt kê rõ trong kết quả, **không làm hủy toàn bộ lô**.

- **`BR-27.3` (Cảnh báo trước khi thực hiện — giải quyết mâu thuẫn với `BR-05.3`):** Khi lô được chọn có chứa công việc **đã kết thúc**, hệ thống **phải cảnh báo trước khi chạy** trong cả hai trường hợp sau:
  - Thao tác là **đổi người phụ trách hoặc đổi hạn chót** — nêu rõ số lượng công việc sẽ bị bỏ qua và lý do, kèm gợi ý dùng nghiệp vụ Bàn giao (FEAT-38) nếu mục đích là chuyển giao nhân sự.
  - Thao tác là **đổi trạng thái sang một trạng thái đang mở** — tức **mở lại hàng loạt**. Cảnh báo nêu rõ số lượng công việc đã kết thúc sẽ bị mở lại và hệ quả: mốc kết thúc hiện hành của chúng bị gỡ (`BR-05.2`). Thao tác này **bắt buộc nhập lý do** như khi đóng hàng loạt (`BR-27.7`).

  **Lý do nghiệp vụ:** Mở lại hàng loạt gỡ mốc kết thúc của hàng chục công việc cùng lúc, làm sai lệch mọi số liệu về thời gian xử lý và tỷ lệ hoàn thành đúng hạn. Đây là thao tác tác động mạnh ngang với đóng hàng loạt, và nằm trong quyền của Trưởng nhóm nên dễ chạm — không thể để nó diễn ra âm thầm.

  **Lý do nghiệp vụ:** Đây chính là mâu thuẫn của v5.2 — quy tắc khóa sửa đổi âm thầm loại bỏ công việc đã kết thúc khỏi lô, khiến quản lý thấy "cập nhật 143/200" mà không hiểu vì sao. Cảnh báo trước biến sự im lặng thành thông tin.

- **`BR-27.4` (Báo cáo kết quả chi tiết):** Sau khi chạy, hệ thống hiển thị: số công việc cập nhật thành công, danh sách công việc bị bỏ qua kèm lý do cụ thể cho từng công việc.

- **`BR-27.5` (Các thao tác hàng loạt được hỗ trợ):** Đổi trạng thái, đổi người phụ trách, đổi mức ưu tiên, đổi nhóm công việc, **đổi hạn chót** (FEAT-28), xóa vào thùng rác.

- **`BR-27.6` (Đổi trạng thái hàng loạt không sinh bản ghi tương tác):** Khi đổi trạng thái hàng loạt sang nhánh **Hoàn thành**, hệ thống **vẫn đánh thức quan hệ khách hàng** (`BR-25.2`) nhưng **không sinh bản ghi tương tác** (`BR-25.3`). Trước khi chạy, hệ thống nêu rõ điều này cho người thực hiện.

  **Lý do nghiệp vụ:** Bản ghi tương tác là bằng chứng về một tương tác **đã thực sự diễn ra** với khách hàng và không xóa được (`BR-15.1`). Đóng hàng loạt 200 công việc là thao tác dọn dẹp tồn đọng, không phải 200 cuộc gọi vừa được thực hiện. Sinh bản ghi trong trường hợp này là đưa 200 sự kiện chưa từng xảy ra vào hồ sơ khách hàng, vĩnh viễn không gỡ được — hỏng đúng thứ mà Nhóm E được xây để bảo vệ. Ngược lại, việc đánh thức quan hệ vẫn đúng, vì quan hệ đó thực sự vừa được xử lý.

- **`BR-27.7` (Bắt buộc ghi lý do khi đổi trạng thái hàng loạt):** Mọi thao tác **đổi trạng thái hàng loạt** đều bắt buộc nhập lý do (`CFG-TASK-16`), ghi vào dấu vết của từng công việc trong lô. Áp dụng cho cả ba chiều: đóng hàng loạt sang nhánh **Hoàn thành**, hủy hàng loạt sang nhánh **Hủy bỏ**, và **mở lại hàng loạt** về trạng thái đang mở. Các thao tác hàng loạt khác — đổi mức ưu tiên, đổi nhóm công việc — không bắt buộc.

  **Lý do nghiệp vụ:** Đóng hàng loạt tác động tới số liệu năng suất và trạng thái quan hệ khách hàng — nặng hơn dời hạn hàng loạt vốn đã bắt buộc lý do tại `BR-28.3`. Không có lý do thì sau này không ai biết 200 công việc đó được hoàn thành thật hay chỉ được dọn cho sạch bảng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-27.1.1` | Danh sách có 500 công việc, giới hạn lô đang đặt là 200 | Bấm "Chọn tất cả" | Hệ thống chọn 200 công việc đầu theo thứ tự đang hiển thị và báo ngay rằng chỉ 200 trong số 500 được chọn, kèm hướng dẫn thu hẹp bộ lọc để xử lý phần còn lại |
| `AC-27.1.2` | Danh sách có đúng 200 công việc | Chọn tất cả và đổi mức ưu tiên hàng loạt | Cả 200 công việc được xử lý, không có cảnh báo giới hạn |
| `AC-27.1.3` | Quản trị viên đặt `CFG-TASK-06` lên **trần tối đa 1000** | Danh sách có 1500 công việc, bấm "Chọn tất cả" | Hệ thống chọn đúng 1000 công việc đầu, báo rằng chỉ 1000 trong số 1500 được chọn |
| `AC-27.1.4` | Quản trị viên thử đặt `CFG-TASK-06` thành **1001** | Lưu cấu hình | Từ chối, thông báo giá trị vượt trần do nhà cung cấp quy định |
| `AC-27.2.1` | Chọn 50 công việc, trong đó 10 việc ngoài phạm vi quyền | Đổi trạng thái hàng loạt | 40 việc cập nhật thành công. 10 việc liệt kê trong danh sách bỏ qua với lý do ngoài phạm vi truy cập |
| `AC-27.3.1` | Chọn 200 công việc gồm 60 việc đã kết thúc | Chọn thao tác đổi người phụ trách và bấm nút thực hiện | **Trước khi chạy**, hiện cảnh báo: 60 việc sẽ bị bỏ qua vì đã kết thúc, kèm gợi ý dùng chức năng Bàn giao |
| `AC-27.3.2` | Tiếp nối AC-27.3.1 | Xác nhận tiếp tục | 140 việc cập nhật. Báo cáo liệt kê đủ 60 việc bị bỏ qua |
| `AC-27.4.1` | Sau mọi thao tác hàng loạt | Xem kết quả | Thấy số thành công và danh sách bỏ qua kèm lý do từng dòng |
| `AC-27.6.1` | Chọn 50 công việc đang mở, mỗi việc gắn một khách hàng **khác nhau**, tham số đánh thức đang bật | Đổi trạng thái hàng loạt sang nhánh Hoàn thành | 50 khách hàng/cơ hội liên quan được đánh thức. Dòng thời gian của họ **không** xuất hiện bản ghi tương tác nào |
| `AC-27.6.2` | Cùng bối cảnh | Quan sát màn hình trước khi xác nhận | Hệ thống nêu rõ thao tác này không sinh bản ghi tương tác |
| `AC-27.7.1` | Chọn 50 công việc, thao tác đổi trạng thái sang nhánh Hoàn thành | Bỏ trống lý do | Từ chối, yêu cầu nhập lý do |
| `AC-27.7.2` | Tiếp nối AC-27.7.1, đã nhập lý do và chạy xong | Mở dấu vết của một công việc trong lô | Thấy lý do đóng hàng loạt, người thực hiện và thời điểm |
| `AC-27.7.3` | Chọn 50 công việc, thao tác đổi mức ưu tiên | Không nhập lý do | Chạy bình thường, không yêu cầu lý do |
| `AC-27.3.3` | Chọn 100 công việc gồm 40 việc **đã kết thúc** | Chọn thao tác đổi trạng thái sang một trạng thái **đang mở**, bấm thực hiện | **Trước khi chạy**, cảnh báo: 40 việc sẽ bị mở lại và mốc kết thúc của chúng bị gỡ. Yêu cầu nhập lý do |
| `AC-27.3.4` | Tiếp nối AC-27.3.3, đã nhập lý do và xác nhận | Mở một trong 40 việc đó | Công việc ở trạng thái đang mở, không còn mốc kết thúc. Lịch sử chuyển trạng thái vẫn ghi lần kết thúc trước đó (`BR-05.2`) |
| `AC-27.7.4` | Chọn 30 công việc đang mở | Đổi trạng thái hàng loạt sang nhánh **Hủy bỏ**, bỏ trống lý do | Từ chối, yêu cầu nhập lý do |
| `AC-27.5.1` | Chọn 20 công việc | Lần lượt thử cả sáu thao tác hàng loạt: đổi trạng thái, đổi người phụ trách, đổi mức ưu tiên, đổi nhóm công việc, đổi hạn chót, xóa vào Thùng rác | Cả sáu thao tác đều thực hiện được và trả về báo cáo kết quả theo `BR-27.4` |

---

#### FEAT-28 — Dời Hạn chót Hàng loạt `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Dời hạn nhiều công việc cùng lúc khi có sự kiện bất khả kháng — nghỉ lễ đột xuất, sự cố hệ thống, thay đổi kế hoạch của khách hàng.

**Quy tắc nghiệp vụ:**

- **`BR-28.1` (Hai cách dời hạn):** Người dùng chọn một trong hai: **Dời tới một ngày cụ thể** (mọi công việc trong lô nhận cùng hạn chót mới), hoặc **Dời thêm một khoảng thời gian** (mỗi công việc giữ nguyên khoảng cách tương đối, ví dụ dời tất cả thêm 3 ngày).

  **Lý do nghiệp vụ:** Hai tình huống khác nhau. Nghỉ lễ 3 ngày thì dời thêm khoảng; dời toàn bộ về sau kỳ đóng sổ thì dời tới ngày cụ thể.

- **`BR-28.2` (Chỉ áp dụng cho công việc đang mở):** Theo `BR-05.3`, công việc đã kết thúc không dời hạn được. Áp dụng cảnh báo trước theo `BR-27.3`.

- **`BR-28.3` (Bắt buộc ghi lý do):** Dời hạn hàng loạt bắt buộc nhập lý do (`CFG-TASK-10`). Lý do được ghi vào dấu vết của **từng** công việc trong lô, để sau này tra cứu được vì sao hạn chót thay đổi.

  **Lý do nghiệp vụ:** Hạn chót là cam kết. Dời hàng loạt mà không có lý do sẽ khiến báo cáo đúng hạn mất ý nghĩa — ai cũng đúng hạn vì hạn liên tục bị dời.

- **`BR-28.4` (Giữ nguyên khoảng cách nhắc việc):** Khi hạn chót dời, mốc nhắc việc dời theo cùng khoảng, giữ nguyên khoảng cách với hạn chót.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-28.1.1` | Chọn 20 việc có hạn chót khác nhau | Dời tới ngày cụ thể 30/09 | Cả 20 việc có hạn chót 30/09 |
| `AC-28.1.2` | Cùng bối cảnh | Dời thêm 3 ngày | Mỗi việc lùi 3 ngày so với hạn chót cũ, giữ nguyên thứ tự tương đối |
| `AC-28.2.1` | Lô có việc đã kết thúc | Thực hiện dời hạn | Cảnh báo trước theo `BR-27.3`, các việc đã kết thúc bị bỏ qua và liệt kê |
| `AC-28.3.1` | Màn hình dời hạn hàng loạt | Bỏ trống lý do | Từ chối, yêu cầu nhập lý do |
| `AC-28.3.2` | Sau khi dời hạn thành công | Mở dấu vết của một công việc trong lô | Thấy ghi nhận hạn chót cũ, hạn chót mới, người thực hiện và lý do |
| `AC-28.4.1` | Công việc hạn chót 10:00, nhắc việc 09:45 | Dời thêm 1 ngày | Hạn chót thành 10:00 hôm sau, nhắc việc thành 09:45 hôm sau |

---

#### FEAT-39 — Xuất Dữ liệu Công việc `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Xuất danh sách công việc ra tệp để báo cáo hoặc phân tích ngoài hệ thống.

**Quy tắc nghiệp vụ:**

- **`BR-39.1` (Phạm vi dữ liệu xuất):** Tệp xuất **chỉ chứa công việc người dùng có quyền xem**. Xuất dữ liệu không bao giờ là đường vòng để lấy dữ liệu ngoài phạm vi quyền.
- **`BR-39.2` (Xuất theo bộ lọc hiện hành):** Dữ liệu xuất tuân theo đúng bộ lọc và cột đang hiển thị trên danh sách.
- **`BR-39.3` (Xử lý bất đồng bộ):** Với khối lượng lớn, việc xuất chạy nền và thông báo cho người dùng khi tệp sẵn sàng tải về.
- **`BR-39.4` (Ghi nhận dấu vết):** Mỗi lần xuất được ghi nhận: ai xuất, thời điểm, số lượng bản ghi, bộ lọc đã dùng.

  **Lý do nghiệp vụ:** Xuất dữ liệu là một trong những đường rò rỉ dữ liệu khách hàng phổ biến nhất. Phải truy vết được.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-39.1.1` | Nhân viên chỉ xem được việc của mình | Xuất dữ liệu | Tệp chỉ chứa việc của chính người đó |
| `AC-39.2.1` | Đang lọc theo mức ưu tiên Khẩn cấp | Xuất dữ liệu | Tệp chỉ chứa việc Khẩn cấp, đúng các cột đang hiển thị |
| `AC-39.4.1` | Sau khi xuất | Quản trị viên xem nhật ký xuất dữ liệu | Thấy người xuất, thời điểm, số bản ghi và bộ lọc đã dùng |
| `AC-39.3.1` | Bộ lọc khớp một lượng lớn công việc | Bấm xuất dữ liệu | Hệ thống báo việc xuất đang được xử lý nền, người dùng tiếp tục làm việc khác được. Khi tệp sẵn sàng, người dùng nhận thông báo kèm lối tải về |
| `AC-39.3.2` | Bộ lọc không khớp công việc nào | Bấm xuất dữ liệu | Hệ thống báo không có dữ liệu để xuất, không tạo tệp rỗng |

---

### Nhóm I — Thùng rác & Phục hồi

#### FEAT-30 — Thùng rác & Tự động Dọn dẹp `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Cho phép khôi phục công việc đã xóa nhầm trong thời hạn quy định.

**Quy tắc nghiệp vụ:**

- **`BR-30.1` (Xóa mềm):** Xóa công việc chuyển nó vào Thùng rác: ẩn khỏi mọi danh sách, lịch biểu, bảng trạng thái, báo cáo và dòng thời gian, nhưng dữ liệu vẫn còn nguyên.

  **Bản ghi tương tác đã sinh không bị ẩn theo:** Nếu công việc đã Hoàn thành và đã sinh bản ghi tương tác (`BR-25.3`), bản ghi đó **vẫn ở lại trên dòng thời gian** của khách hàng kể cả khi công việc bị xóa mềm hoặc xóa vĩnh viễn. Bản ghi mất liên kết tới công việc nguồn nhưng giữ nguyên nội dung, người thực hiện và thời điểm.

  **Lý do nghiệp vụ:** Bản ghi tương tác là bằng chứng về điều **đã xảy ra với khách hàng**, bất biến theo `BR-15.1` và không hoàn tác theo `BR-25.5`. Xóa một công việc là thao tác dọn dẹp danh sách việc cần làm, không phải tuyên bố rằng cuộc gọi hôm qua chưa từng diễn ra. Nếu bản ghi biến mất theo, lịch sử chăm sóc khách hàng có thể bị xóa gián tiếp bằng cách xóa công việc — vòng qua chính quy tắc cấm xóa bản ghi tương tác.
- **`BR-30.2` (Màn hình Thùng rác):** Hiển thị danh sách công việc đã xóa kèm thời điểm xóa, người xóa, và **số ngày còn lại trước khi bị xóa vĩnh viễn**.

  **Lý do nghiệp vụ:** Không hiển thị thời hạn còn lại thì người dùng không biết mình còn bao lâu để quyết định khôi phục.

- **`BR-30.3` (Khôi phục):** Khôi phục đưa công việc trở lại nguyên trạng: giữ nguyên trạng thái, người phụ trách, hạn chót, ngữ cảnh khách hàng và toàn bộ lịch sử. Hành động khôi phục được ghi dấu vết.

  **Không lặp lại các hệ quả tự động:** Khôi phục một công việc đã Hoàn thành **không** sinh thêm bản ghi tương tác mới và **không** đánh thức lại quan hệ khách hàng. Bản ghi cũ vẫn còn nguyên trên dòng thời gian theo `BR-30.1`.

  **Kiểm tra lại tính hợp lệ khi khôi phục:** Nếu người phụ trách của công việc được khôi phục đã bị vô hiệu hóa, công việc được gắn nhãn **"Cần phân công lại"** và đưa vào danh sách cảnh báo của Quản trị viên, theo `BR-09.2`. Nếu ngữ cảnh khách hàng đã bị xóa vĩnh viễn, công việc được khôi phục **không kèm ngữ cảnh** và hiển thị ghi chú cho biết ngữ cảnh cũ không còn tồn tại. Nếu trạng thái hoặc Nhóm công việc mà công việc đang mang đã bị **vô hiệu hóa** trong thời gian nó nằm trong Thùng rác, công việc vẫn được khôi phục nguyên trạng theo `BR-37.3`: giữ trạng thái hoặc nhóm đó, hiển thị ở cột "Trạng thái ngừng sử dụng" nếu là trạng thái, và không bị buộc chuyển sang giá trị khác chỉ vì đã từng ở Thùng rác.

  **Lý do nghiệp vụ:** Công việc có thể nằm trong Thùng rác nhiều tuần, trong thời gian đó nhân sự, dữ liệu khách hàng và danh mục cấu hình đều có thể thay đổi. Khôi phục nguyên trạng mà không kiểm tra lại sẽ đưa công việc về tay người đã rời tổ chức, trỏ tới một khách hàng không còn tồn tại, hoặc mang một trạng thái/nhóm mà doanh nghiệp tưởng đã dọn sạch khỏi hệ thống.

  **Dấu vết:** Mỗi lần khôi phục ghi nhận người thực hiện và thời điểm, theo `NFR-06`.

  **Lý do nghiệp vụ:** Các hệ quả tự động đã phát sinh một lần khi công việc hoàn thành và chưa bao giờ bị hoàn tác. Lặp lại chúng sẽ nhân đôi bản ghi cho cùng một tương tác có thật.
- **`BR-30.4` (Thời hạn lưu trữ):** Công việc nằm trong Thùng rác quá **30 ngày** bị xóa vĩnh viễn. Thời hạn này là tham số cấu hình theo Không gian làm việc (`CFG-TASK-07`).
- **`BR-30.5` (Dọn dẹp tự động):** Tiến trình dọn dẹp chạy **mỗi ngày một lần vào giờ thấp điểm** theo múi giờ Không gian làm việc. Trong môi trường nhiều tiến trình, chỉ một tiến trình thực hiện dọn dẹp tại một thời điểm để không xử lý trùng.
- **`BR-30.6` (Xóa vĩnh viễn thủ công):** Khi tính năng này đang bật (`CFG-TASK-15`), vai trò thấp nhất được xóa vĩnh viễn thủ công do Không gian làm việc cấu hình (`CFG-TASK-21`), mặc định là Quản lý cấp phòng ban trở lên. Người có quyền xóa được phép xóa vĩnh viễn ngay một công việc trong Thùng rác, không cần chờ hết 30 ngày. Thao tác này **yêu cầu xác nhận rõ ràng** và **không thể hoàn tác**.

  **Lý do nghiệp vụ về quyền thực hiện:** Đây là khẩu vị rủi ro dữ liệu điển hình, không phải nghĩa vụ pháp lý — một số doanh nghiệp muốn hạn chế thao tác không-hoàn-tác-được này ở cấp cao hơn (chỉ Quản trị viên và Chủ sở hữu), doanh nghiệp khác (đội nhỏ, ít phân cấp) muốn để cả Trưởng nhóm tự dọn Thùng rác trong phạm vi nhóm mình.
- **`BR-30.7` (Công việc mẫu định kỳ trong Thùng rác):** Áp dụng `BR-12.7`.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-30.1.1` | Công việc **đang mở**, hiển thị trên Lịch biểu và trên dòng thời gian khách hàng | Xóa công việc | **Mục công việc** biến mất khỏi Lịch biểu và khỏi dòng thời gian. Công việc xuất hiện trong Thùng rác |
| `AC-30.1.2` | Công việc **đã Hoàn thành**, đã sinh bản ghi tương tác loại cuộc gọi trên khách hàng X | Xóa công việc | Công việc biến mất khỏi Lịch biểu và Danh sách. **Bản ghi cuộc gọi vẫn còn** trên dòng thời gian của X, giữ nguyên người thực hiện và thời điểm |
| `AC-30.1.3` | Tiếp nối AC-30.1.2 | Xóa vĩnh viễn công việc đó | Bản ghi cuộc gọi **vẫn còn** trên dòng thời gian của X |
| `AC-25.5.1` | Cơ hội Y vừa được đánh thức do một công việc hoàn thành, đã hết cảnh báo nguội lạnh | Xóa công việc đó vào Thùng rác | Cơ hội Y **không** quay lại trạng thái nguội lạnh. Việc đánh thức không bị hoàn tác |
| `AC-30.2.1` | Công việc xóa cách đây 5 ngày, thời hạn lưu 30 ngày | Mở Thùng rác | Thấy công việc kèm thông tin còn 25 ngày trước khi xóa vĩnh viễn |
| `AC-30.3.1` | Công việc đã xóa, trước đó gắn Cơ hội Y và ở trạng thái "Đang làm" | Khôi phục | Công việc trở lại trạng thái "Đang làm", vẫn gắn Cơ hội Y, hiện lại trên dòng thời gian của Y |
| `AC-30.3.2` | Công việc **đã Hoàn thành** bị xóa, đã có bản ghi tương tác trên khách hàng X | Khôi phục | Công việc trở lại trạng thái đã Hoàn thành. Dòng thời gian của X **vẫn chỉ có một** bản ghi tương tác, không sinh thêm bản thứ hai |
| `AC-30.4.1` | Công việc nằm trong Thùng rác 31 ngày, thời hạn lưu trữ đang đặt 30 ngày | Tiến trình dọn dẹp chạy | Công việc bị xóa vĩnh viễn, không còn trong Thùng rác |
| `AC-30.4.2` | Công việc nằm trong Thùng rác 29 ngày | Tiến trình dọn dẹp chạy | Công việc **vẫn còn** trong Thùng rác, khôi phục được |
| `AC-30.5.1` *(kiểm thử kỹ thuật)* | Có 5 công việc quá hạn lưu trữ, môi trường dựng được nhiều tiến trình song song | Kích hoạt tiến trình dọn dẹp hai lần đồng thời | 5 công việc bị xóa đúng một lần. Không phát sinh lỗi do xử lý trùng |
| `AC-30.3.3` | Công việc trong Thùng rác, ngữ cảnh khách hàng của nó đã bị xóa vĩnh viễn | Khôi phục công việc | Công việc được khôi phục **không kèm ngữ cảnh**, hiển thị ghi chú cho biết ngữ cảnh cũ không còn tồn tại |
| `AC-30.3.4` | Công việc trong Thùng rác đang mang trạng thái "Chờ khách phản hồi". Trong lúc đó, Quản trị viên vô hiệu hóa trạng thái này | Khôi phục công việc | Công việc được khôi phục vẫn mang trạng thái "Chờ khách phản hồi", hiển thị ở cột "Trạng thái ngừng sử dụng". Không bị ép chuyển sang trạng thái khác |
| `AC-30.6.1` | Công việc trong Thùng rác, `CFG-TASK-21` đang ở mặc định "Quản lý cấp phòng ban trở lên" | Quản lý Kinh doanh chọn xóa vĩnh viễn | Yêu cầu xác nhận rõ ràng. Sau khi xác nhận, công việc biến mất hoàn toàn và không khôi phục được |
| `AC-30.6.2` | Cùng bối cảnh AC-30.6.1 | Trưởng nhóm chọn xóa vĩnh viễn | Không thấy chức năng này |

---

### Nhóm J — Dòng thời gian Hợp nhất

#### FEAT-31 — Dòng thời gian Hợp nhất 360 độ `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Tập hợp toàn bộ lịch sử tương tác và công việc liên quan tới một khách hàng hoặc cơ hội vào một dòng thời gian duy nhất — giải quyết vấn đề nghiệp vụ số 3 (Mục 2.1).

**Quy tắc nghiệp vụ:**

- **`BR-31.1` (Nội dung dòng thời gian):** Dòng thời gian của Khách hàng, Doanh nghiệp và Cơ hội bán hàng hiển thị theo thứ tự thời gian giảm dần: các bản ghi tương tác (FEAT-15..19), công việc được tạo, công việc hoàn thành, và các sự kiện vòng đời của chính thực thể đó.

- **`BR-31.2` (Công việc hiện trên dòng thời gian của ngữ cảnh):** Công việc có gắn ngữ cảnh khách hàng phải hiện trên dòng thời gian của thực thể đó, kèm trạng thái hiện tại, người phụ trách và hạn chót.

- **`BR-31.3` (Lọc theo loại):** Người dùng lọc dòng thời gian theo loại sự kiện để tra cứu nhanh.

- **`BR-31.4` (Phạm vi quyền xem):** Người dùng chỉ thấy trên dòng thời gian những mục mà họ có quyền xem. Mục ngoài phạm vi quyền **được ẩn hoàn toàn**, không hiển thị dạng rút gọn hay dạng bị che.

  **Lý do nghiệp vụ:** Hiển thị "có 3 mục bạn không được xem" đã là tiết lộ thông tin về khối lượng hoạt động của bộ phận khác.

- **`BR-31.5` (Dòng thời gian không mất mục khi bàn giao):** Khi công việc được bàn giao (FEAT-38) hoặc người phụ trách rời tổ chức, các mục trên dòng thời gian **giữ nguyên**, kèm tên người đã thực hiện tại thời điểm đó.

  **Phân biệt hai loại tên:** Bản ghi tương tác luôn hiển thị **người đã thực hiện** tương tác đó — không bao giờ đổi. Công việc trên dòng thời gian hiển thị **người phụ trách hiện tại**. Sau bàn giao, hai tên này có thể khác nhau, và giao diện phải nêu rõ đâu là người thực hiện, đâu là người đang phụ trách.

  **Lý do nghiệp vụ:** Người xem hồ sơ khách hàng cần biết cả hai: ai đã nói chuyện với khách hôm đó (để hỏi lại chi tiết) và ai đang chịu trách nhiệm bây giờ (để giao việc tiếp). Hiển thị một tên duy nhất sẽ sai một trong hai nhu cầu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-31.1.1` | Khách hàng X có 2 cuộc gọi, 1 ghi chú, 3 công việc | Mở dòng thời gian của X | Thấy đủ 6 mục, sắp xếp theo thời gian giảm dần |
| `AC-31.2.1` | Tạo công việc gắn Cơ hội Y | Mở dòng thời gian của Y | Thấy công việc vừa tạo kèm trạng thái, người phụ trách và hạn chót |
| `AC-31.2.2` | Tiếp nối AC-31.2.1 | Hoàn thành công việc đó | Dòng thời gian của Y ghi nhận sự kiện hoàn thành và bản ghi tương tác tương ứng (`BR-25.3`) |
| `AC-31.4.1` | Khách hàng X có công việc của phòng khác, người xem không có quyền | Mở dòng thời gian của X | Công việc đó **không** xuất hiện dưới bất kỳ hình thức nào |
| `AC-31.5.1` | Nhân viên A đã gọi cho khách X, sau đó A rời tổ chức và công việc được bàn giao cho B | Mở dòng thời gian của X | Bản ghi cuộc gọi vẫn còn, vẫn ghi tên A là người đã thực hiện |
| `AC-31.5.2` | Tiếp nối AC-31.5.1 | Xem mục **công việc** tương ứng trên cùng dòng thời gian | Mục công việc hiển thị **B** là người phụ trách hiện tại. Giao diện nêu rõ đâu là người đã thực hiện (A) và đâu là người đang phụ trách (B) |
| `AC-31.3.1` | Khách hàng X có 2 cuộc gọi, 1 ghi chú và 3 công việc trên dòng thời gian | Lọc dòng thời gian theo loại cuộc gọi | Chỉ hiện 2 bản ghi cuộc gọi |
| `AC-31.3.2` | Cùng bối cảnh AC-31.3.1 | Lọc dòng thời gian theo loại công việc | Chỉ hiện 3 mục công việc, không hiện cuộc gọi hay ghi chú |
| `AC-31.3.3` | Cùng bối cảnh AC-31.3.1 | Bỏ chọn bộ lọc, xem lại toàn bộ | Cả 6 mục hiện đầy đủ, đúng thứ tự thời gian giảm dần như trước khi lọc |

---

### Nhóm L — Nền tảng Cấu hình

#### FEAT-37 — Toàn vẹn Danh mục Trạng thái & Phân loại `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Bảo đảm danh mục trạng thái mà doanh nghiệp tự cấu hình luôn ở trạng thái vận hành được, không để cấu hình sai làm tê liệt phân hệ.

*Tính năng này lấp khoảng trống của v5.2: Nguyên tắc "trạng thái do doanh nghiệp tự định nghĩa" được nêu nhưng không có ràng buộc toàn vẹn nào đi kèm.*

**Quy tắc nghiệp vụ:**

- **`BR-37.1` (Ràng buộc tối thiểu của danh mục trạng thái):** Mỗi Không gian làm việc phải luôn có:
  - **Ít nhất một** trạng thái đang hoạt động **không** thuộc nhóm kết thúc;
  - **Đúng một** trạng thái được đánh dấu **mặc định** (trạng thái gán cho công việc mới tạo), và trạng thái này phải đang hoạt động, không thuộc nhóm kết thúc;
  - **Ít nhất một** trạng thái đang hoạt động thuộc nhánh **Hoàn thành**.

  Hệ thống **từ chối** mọi thao tác cấu hình làm vi phạm các ràng buộc trên, kèm giải thích rõ hậu quả.

  **Lý do nghiệp vụ:** Nếu không còn trạng thái kết thúc nào, không ai đóng được việc, mọi việc đều quá hạn vĩnh viễn, cơ chế đánh thức quan hệ khách hàng ngừng hoạt động. Nếu không có trạng thái mặc định, không tạo được công việc mới. Đây là những sự cố làm tê liệt vận hành của cả doanh nghiệp khách hàng.

- **`BR-37.2` (Phân nhánh trạng thái kết thúc):** Khi đánh dấu một trạng thái là kết thúc, Quản trị viên **bắt buộc** chỉ định nó thuộc nhánh **Hoàn thành** hay **Hủy bỏ** (Quyết định 1, Mục 2.5).

- **`BR-37.3` (Vô hiệu hóa trạng thái đang được sử dụng):** Khi Quản trị viên vô hiệu hóa một trạng thái vẫn còn công việc đang mang, hệ thống:
  - **Cảnh báo trước** số lượng công việc bị ảnh hưởng;
  - Yêu cầu chọn: **chuyển các công việc đó sang trạng thái khác**, hoặc **giữ nguyên** và chấp nhận chúng hiển thị ở cột "Trạng thái ngừng sử dụng" (`BR-08.3`);
  - Không bao giờ để công việc biến mất khỏi tầm nhìn.

  **Ràng buộc của phương án "chuyển sang trạng thái khác":** Việc chuyển này là thao tác quản trị dọn dẹp danh mục, **không phải** người dùng xử lý công việc. Vì vậy:
  - Trạng thái đích phải **cùng loại** với trạng thái bị vô hiệu hóa: đang mở chuyển sang đang mở, kết thúc chuyển sang kết thúc **cùng nhánh** (Hoàn thành sang Hoàn thành, Hủy bỏ sang Hủy bỏ). Hệ thống không cho chọn trạng thái đích khác loại.
  - Việc chuyển **không** ghi nhận lại thời điểm kết thúc, **không** gỡ mốc kết thúc hiện có, **không** đánh thức quan hệ khách hàng và **không** sinh bản ghi tương tác.

  **Lý do nghiệp vụ:** Nếu cho phép chuyển tự do, việc vô hiệu hóa một trạng thái kết thúc sẽ trở thành thao tác **mở lại hàng loạt** các công việc đã đóng — gỡ mốc kết thúc của hàng chục công việc mà không ai chủ ý làm vậy, phá đúng dữ liệu mà `BR-05.3` bảo vệ. Chiều ngược lại, chuyển sang nhánh Hoàn thành sẽ đóng hàng loạt công việc chưa làm xong và làm sai số liệu năng suất. Quản trị viên đang dọn danh mục, không đang quyết định công việc nào đã xong.

- **`BR-37.4` (Xóa danh mục đang được sử dụng):** Trạng thái, nhóm công việc hoặc nguồn công việc **đang được công việc sử dụng** thì không xóa được, chỉ vô hiệu hóa được.

- **`BR-37.5` (Không gian làm việc mới):** Khi khởi tạo Không gian làm việc mới, hệ thống tạo sẵn bộ danh mục mặc định thỏa mãn đầy đủ `BR-37.1`, để doanh nghiệp dùng được ngay.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-37.1.1` | Không gian làm việc chỉ còn một trạng thái nhánh Hoàn thành đang hoạt động | Vô hiệu hóa trạng thái đó | Từ chối, giải thích rằng sẽ không còn cách nào đóng công việc |
| `AC-37.1.2` | Đang có trạng thái mặc định là "Cần làm" | Đặt trạng thái khác làm mặc định | Trạng thái mặc định chuyển sang trạng thái mới. Vẫn chỉ có đúng một mặc định |
| `AC-37.1.3` | Danh mục đang có một trạng thái nhánh Hoàn thành | Đánh dấu trạng thái đó làm trạng thái mặc định, lưu cấu hình | Từ chối, giải thích công việc mới không thể sinh ra ở trạng thái đã kết thúc |
| `AC-37.1.4` | Danh mục chỉ còn một trạng thái đang mở đang hoạt động | Vô hiệu hóa trạng thái đó | Từ chối, giải thích rằng sẽ không còn trạng thái nào để tạo công việc mới |
| `AC-37.3.5` | Trạng thái sắp bị vô hiệu hóa đang là **trạng thái mặc định** | Vô hiệu hóa trạng thái này | Hệ thống yêu cầu chọn trạng thái mặc định mới trước, rồi mới cho vô hiệu hóa |
| `AC-37.3.6` | Trạng thái nhánh Hủy bỏ **duy nhất**, còn 5 công việc mang trạng thái đó | Vô hiệu hóa và chọn phương án chuyển sang trạng thái khác | Không có trạng thái đích hợp lệ nào để chọn. Hệ thống chỉ cho phương án **giữ nguyên**, 5 công việc chuyển vào cột "Trạng thái ngừng sử dụng" |
| `AC-37.2.1` | Tạo trạng thái mới, đánh dấu là kết thúc | Lưu mà chưa chọn nhánh | Từ chối, yêu cầu chọn Hoàn thành hoặc Hủy bỏ |
| `AC-37.3.1` | Trạng thái "Chờ duyệt" có 12 công việc | Vô hiệu hóa trạng thái này | Cảnh báo 12 công việc bị ảnh hưởng, yêu cầu chọn chuyển sang trạng thái khác hoặc giữ nguyên |
| `AC-37.3.2` | Tiếp nối AC-37.3.1, "Chờ duyệt" là trạng thái đang mở | Chọn chuyển sang trạng thái khác | Danh sách trạng thái đích **chỉ gồm các trạng thái đang mở**. Không chọn được trạng thái kết thúc |
| `AC-37.3.3` | Vô hiệu hóa một trạng thái thuộc nhánh Hoàn thành, còn 30 công việc đã đóng | Chọn chuyển sang trạng thái khác | Danh sách đích **chỉ gồm trạng thái thuộc nhánh Hoàn thành**. Sau khi chuyển, 30 công việc giữ nguyên mốc kết thúc cũ |
| `AC-37.3.4` | Tiếp nối AC-37.3.3 | Xem dòng thời gian của các khách hàng liên quan | **Không** có bản ghi tương tác mới nào được sinh. Quan hệ khách hàng **không** được đánh thức |
| `AC-37.4.1` | Nhóm công việc "Gọi điện" đang được 300 công việc sử dụng | Thử xóa nhóm này | Không cho xóa. Chỉ có tùy chọn vô hiệu hóa |
| `AC-37.5.1` | Khởi tạo Không gian làm việc mới | Vào màn hình công việc, tạo việc đầu tiên | Tạo được ngay, không cần cấu hình gì thêm |

---

#### FEAT-40 — Trường Dữ liệu Tùy biến cho Công việc `[Phạm vi phát hành]`

**Mô tả nghiệp vụ:** Cho phép doanh nghiệp bổ sung các trường thông tin riêng vào công việc, phù hợp đặc thù ngành nghề.

**Quy tắc nghiệp vụ:**

- **`BR-40.1` (Định nghĩa trường):** Quản trị viên định nghĩa trường tùy biến cho Công việc theo cơ chế chung của hệ thống, mô tả tại [`object-manager-srs.md`](./object-manager-srs.md).
- **`BR-40.2` (Sử dụng trong toàn phân hệ):** Trường tùy biến dùng được để: hiển thị trên biểu mẫu tạo/sửa và màn hình chi tiết, chọn làm cột trên Danh sách (`BR-06.3`), lọc dữ liệu, xuất dữ liệu (FEAT-39), và làm điều kiện trong quy trình tự động hóa.
- **`BR-40.3` (Trường bắt buộc và giới hạn quyền nhập):** Khi một trường tùy biến được đánh dấu bắt buộc nhưng người dùng **không có quyền nhập** trường đó, hệ thống vẫn cho lưu công việc và **gắn cờ thiếu dữ liệu bắt buộc**, để người có thẩm quyền bổ sung sau. Công việc bị gắn cờ phải tra cứu được qua bộ lọc riêng.

  **Lý do nghiệp vụ:** Không bao giờ được yêu cầu một người nhập trường mà chính họ không được phép nhìn thấy hoặc chỉnh sửa. Quy tắc này đồng bộ với chuẩn chung của hệ thống tại [`object-manager-srs.md`](./object-manager-srs.md).

- **`BR-40.4` (Sửa trường tùy biến trên công việc đã kết thúc):** Trường tùy biến **được phép** sửa trên công việc đã kết thúc, theo `BR-05.3` — chúng là thông tin mô tả, không phải dữ liệu kết quả.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-40.2.1` | Đã định nghĩa trường tùy biến "Mã hợp đồng" | Mở danh sách công việc | Chọn được "Mã hợp đồng" làm cột hiển thị và làm điều kiện lọc |
| `AC-40.2.2` | Đang hiển thị cột "Mã hợp đồng" trên danh sách | Xuất dữ liệu | Tệp xuất có cột "Mã hợp đồng" kèm giá trị của từng công việc |
| `AC-40.2.3` | Đã định nghĩa trường tùy biến "Mã hợp đồng" | Cấu hình một quy trình tự động hóa | Chọn được "Mã hợp đồng" làm điều kiện của quy trình |
| `AC-40.3.1` | Trường "Ngân sách" là bắt buộc, nhân viên A không có quyền nhập trường này | A tạo công việc | Lưu thành công. Công việc được gắn cờ thiếu dữ liệu bắt buộc |
| `AC-40.3.2` | Tiếp nối AC-40.3.1 | Quản lý lọc theo cờ thiếu dữ liệu | Thấy công việc đó trong danh sách cần bổ sung |
| `AC-40.4.1` | Công việc đã kết thúc | Sửa giá trị trường tùy biến | Lưu thành công |

---

### Các tính năng thuộc Roadmap v7.0

*Các tính năng dưới đây đã được ghi nhận nhu cầu nghiệp vụ nhưng **nằm ngoài phạm vi phát hành hiện tại**. Mô tả ở mức định hướng, chưa phải đặc tả đầy đủ, và **không dùng làm tiêu chí nghiệm thu**.*

| Mã | Tên tính năng | Nhu cầu nghiệp vụ |
| --- | --- | --- |
| `FEAT-10` | Nhiều Người Cùng Tham gia Công việc | Một công việc cần nhiều người phối hợp, ngoài người chịu trách nhiệm chính. Phá vỡ Nguyên tắc 3 nên cần thiết kế lại mô hình trách nhiệm trước khi triển khai |
| `FEAT-14` | Chuỗi Công việc Chăm sóc Nối tiếp | Hoàn thành việc này tự sinh việc kế tiếp theo kịch bản chăm sóc định sẵn |
| `FEAT-20` | Cấu trúc Dữ liệu Chuyên biệt theo Loại Tương tác | Mỗi loại tương tác có bộ trường riêng thay vì nội dung tự do |
| `FEAT-21` | Danh sách Mục con Kiểm tra | Chia công việc thành các mục con cần tích hoàn thành |
| `FEAT-22` | Rào cản Bắt buộc Hoàn thành Kiểm tra | Chặn đóng công việc khi chưa tích hết mục con |
| `FEAT-23` | Thanh Tiến độ Hoàn thành | Hiển thị phần trăm hoàn thành dựa trên mục con |
| `FEAT-26` | Bảng Điểm Năng suất Nhân viên | Xếp hạng hoạt động và năng suất theo kỳ |
| `FEAT-29` | Đồng bộ Lịch Ngoài Hai chiều | Đồng bộ với Google Calendar và Outlook |
| `FEAT-32` | Chặn Chuyển Giai đoạn khi Còn Việc Khẩn cấp | Không cho cơ hội sang giai đoạn mới khi còn việc khẩn cấp chưa đóng |
| `FEAT-33` | Công việc Riêng tư | Công việc chỉ người phụ trách và cấp trên trực tiếp nhìn thấy, tách khỏi quy tắc phạm vi dữ liệu thông thường |
| `FEAT-34` | Cân bằng Tải Công việc | Nhìn khối lượng việc của cả đội để phân bổ hợp lý |
| `FEAT-35` | Theo dõi Thời lượng Thực tế | Ghi nhận thời gian thực tế bỏ ra, phục vụ tính phí dịch vụ |
| `FEAT-36` | Phê duyệt Hoàn tất Công việc Trọng yếu | Công việc quan trọng cần cấp trên duyệt trước khi đóng |
| `BR-11.6` | Bình luận & Đề cập Đồng nghiệp *(quy tắc, không phải FEAT riêng)* | Trao đổi nội bộ trong chi tiết công việc, gắn thẻ đồng nghiệp |
| `FEAT-41` | Nhập Dữ liệu Công việc Hàng loạt | Nhập danh sách công việc từ tệp Excel/CSV. Mọi CRM lớn đều có; thiếu năng lực này thì không tiếp nhận được khách hàng đang có sẵn dữ liệu ở hệ thống cũ. Chiều ngược của FEAT-39 |
| `FEAT-42` | Nhắc việc qua Kênh Ngoài Ứng dụng | Gửi nhắc việc qua email hoặc thông báo đẩy trên thiết bị di động. Nhân viên kinh doanh phần lớn thời gian ở ngoài gặp khách và không mở ứng dụng — chuông chỉ tồn tại trong ứng dụng thì đúng lúc cần nhất lại không tới |

---

## 4. Yêu cầu phi chức năng

- **`NFR-01` (Tốc độ phản hồi các màn hình làm việc):** Các màn hình Danh sách, Lịch biểu và Bảng trạng thái phải hiển thị xong dữ liệu **dưới 2 giây** trong điều kiện vận hành thông thường, với khối lượng tới 500 công việc trong phạm vi xem.

  **Lý do nghiệp vụ:** Đây là màn hình nhân viên mở hàng chục lần mỗi ngày. Chậm hơn ngưỡng này, người dùng sẽ chuyển sang dùng công cụ ngoài hệ thống.

- **`NFR-02` (An toàn thao tác hàng loạt):** Thao tác hàng loạt ở quy mô tối đa cho phép (`CFG-TASK-06`) phải hoàn tất và trả kết quả chi tiết cho từng dòng. Sự cố ở một dòng không được làm hỏng các dòng còn lại.

- **`NFR-03` (Chống ghi đè khi nhiều người cùng sửa):** Khi hai người cùng sửa một công việc, hệ thống phải phát hiện xung đột và **cảnh báo người lưu sau**, thay vì âm thầm ghi đè thay đổi của người lưu trước. Người lưu sau được thông báo rõ rằng bản ghi đã thay đổi và cần tải lại.

  **Lý do nghiệp vụ:** Mất thay đổi âm thầm là loại lỗi người dùng không bao giờ phát hiện cho tới khi hậu quả đã xảy ra.

- **`NFR-04` (Tin cậy của tiến trình nền):** Các tiến trình nền — nhắc việc, sinh công việc định kỳ, dọn dẹp thùng rác — phải bảo đảm **mỗi việc chỉ được xử lý đúng một lần**, kể cả khi hệ thống vận hành nhiều tiến trình song song, bị khởi động lại giữa chừng hoặc phục hồi sau sự cố.

- **`NFR-05` (Cách ly dữ liệu giữa các Không gian làm việc):** Dữ liệu công việc của một doanh nghiệp khách hàng **tuyệt đối không** hiển thị hoặc liên kết được sang doanh nghiệp khác, trong mọi chức năng gồm cả tìm kiếm, xuất dữ liệu, dòng thời gian và tiến trình nền.

- **`NFR-06` (Truy vết thay đổi):** Mọi thay đổi trên công việc — tạo, sửa từng trường, đổi trạng thái, đổi người phụ trách, xóa, khôi phục, bàn giao — đều được ghi dấu vết gồm người thực hiện, thời điểm, giá trị trước và sau. Dấu vết không sửa và không xóa được.

---

## 5. Ma trận phân quyền nghiệp vụ

**Quy ước phạm vi dữ liệu:**

- **Cá nhân** — chỉ công việc do chính người dùng phụ trách;
- **Nhóm** — công việc của toàn bộ đơn vị tổ chức mà người dùng quản lý, gồm cả các đơn vị cấp dưới;
- **Phòng ban** — công việc của toàn bộ phòng ban;
- **Toàn bộ** — mọi công việc trong Không gian làm việc;
- **—** — không có quyền.

| Mã | Tính năng | Nhân viên KD | Chuyên viên HT | Trưởng nhóm | Quản lý KD | Giám đốc KD | Quản trị viên | Chủ sở hữu |
| --- | --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `FEAT-01` | Tạo & Quản lý Công việc | Cá nhân | Cá nhân | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `FEAT-02` | Gắn Ngữ cảnh Khách hàng | Cá nhân | Cá nhân | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `BR-02.4` | Đổi Ngữ cảnh của Việc đã Kết thúc *(quy tắc trong FEAT-02, khi `CFG-TASK-12` bật)* | — | — | — | — | — | Toàn bộ | Toàn bộ |
| `FEAT-03` | Đặt Mức Ưu tiên | Cá nhân | Cá nhân | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `FEAT-04` | Quản lý Hạn chót | Cá nhân | Cá nhân | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `FEAT-05` | Chuyển Trạng thái & Hoàn tất | Cá nhân | Cá nhân | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `FEAT-06` | Danh sách Lọc Nâng cao | Cá nhân | Cá nhân | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `FEAT-07` | Lịch biểu | Cá nhân | Cá nhân | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `FEAT-08` | Bảng Trạng thái | Cá nhân | Cá nhân | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `FEAT-09` | Giao việc | Cá nhân | Cá nhân | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `FEAT-11` | Nhận Nhắc việc | Cá nhân | Cá nhân | Cá nhân | Cá nhân | Cá nhân | Cá nhân | Cá nhân |
| `FEAT-12` | Thiết lập Công việc Định kỳ | Cá nhân | Cá nhân | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `FEAT-13` | Cấu hình Quy trình Sinh việc | — | — | — | — | Toàn bộ | Toàn bộ | Toàn bộ |
| `FEAT-15..19` | Ghi nhận Tương tác | Cá nhân | Cá nhân | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `BR-15.6` | Thu hồi Bản ghi Tương tác *(quy tắc trong FEAT-15..19)* — mức mặc định, mở rộng được theo `CFG-TASK-20`* | — | — | — | — | — | Toàn bộ | Toàn bộ |
| `FEAT-24` | Nhận Chuông Nhắc việc | Cá nhân | Cá nhân | Cá nhân | Cá nhân | Cá nhân | Cá nhân | Cá nhân |
| `FEAT-25` | Đánh thức Quan hệ *(tự động)* | — | — | — | — | — | — | — |
| `FEAT-27` | Cập nhật & Xóa Hàng loạt | — | — | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `FEAT-28` | Dời Hạn chót Hàng loạt | — | — | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `FEAT-30` | Thùng rác & Khôi phục | Cá nhân | Cá nhân | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `BR-30.6` | Xóa Vĩnh viễn Thủ công *(quy tắc trong FEAT-30)* — mức mặc định, mở rộng/thu hẹp được theo `CFG-TASK-21`* | — | — | — | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `CFG-TASK-*` | Cấu hình Tham số Không gian làm việc | — | — | — | — | — | Toàn bộ | Toàn bộ |
| `FEAT-31` | Dòng thời gian 360 độ | Cá nhân | Cá nhân | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `FEAT-37` | Cấu hình Danh mục Trạng thái | — | — | — | — | — | Toàn bộ | Toàn bộ |
| `FEAT-38` | Bàn giao Công việc — mức mặc định, mở rộng/thu hẹp được theo `CFG-TASK-19`* | — | — | — | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `FEAT-39` | Xuất Dữ liệu | — | — | Nhóm | Phòng ban | Toàn bộ | Toàn bộ | Toàn bộ |
| `FEAT-40` | Định nghĩa Trường Tùy biến | — | — | — | — | — | Toàn bộ | Toàn bộ |

**Ghi chú:**

1. `FEAT-25` là cơ chế tự động của hệ thống, không phải chức năng người dùng thao tác — không có cột quyền.
2. `FEAT-11` và `FEAT-24`: mọi vai trò chỉ nhận chuông nhắc cho **công việc của chính mình** (`BR-11.2`), không phụ thuộc phạm vi dữ liệu.
3. Chi tiết cách phân giải quyền, vai trò chức năng và xung đột chính sách nhóm — xem [`iam-tenant-authorization.md`](./iam-tenant-authorization.md).
4. Bảy cột vai trò trong ma trận tương ứng bảy vai trò người dùng tại Mục 2.2 (không tính Tiến trình Hệ thống).
5. Các dòng đánh dấu `*` thể hiện mức phân quyền **mặc định**, không phải cố định — Không gian làm việc điều chỉnh được qua tham số cấu hình tương ứng (Phụ lục B), theo đúng nguyên tắc "quyết định nghiệp vụ nhiều hướng phải là tham số cấu hình theo tenant".

---

## 6. Kịch bản nghiệm thu nghiệp vụ (UAT)

### UAT-01 — Công việc hoàn thành không bị báo quá hạn *(FEAT-04, FEAT-05)*

1. Tạo công việc "Gửi tài liệu giới thiệu sản phẩm", hạn chót là **hôm qua**, trạng thái "Cần làm".
2. **Kỳ vọng:** Công việc hiển thị cảnh báo quá hạn trên Danh sách, Lịch biểu và Bảng trạng thái.
3. Chuyển công việc sang trạng thái thuộc nhánh **Hoàn thành**.
4. **Kỳ vọng nghiệm thu:** Cảnh báo quá hạn **biến mất hoàn toàn trên cả ba chế độ xem**. Tiêu đề hiển thị dạng đã hoàn thành. Bộ đếm quá hạn trên ô ngày của Lịch biểu giảm đi 1.

### UAT-02 — Việc bị hủy không được tính là hoàn thành *(Quyết định 1, FEAT-05, FEAT-25)*

1. Tạo Cơ hội bán hàng chưa có tương tác 20 ngày, đang bị cảnh báo nguội lạnh.
2. Tạo công việc "Gọi tư vấn gói nâng cấp" gắn với cơ hội này.
3. Chuyển công việc sang trạng thái thuộc nhánh **Hủy bỏ**.
4. **Kỳ vọng nghiệm thu:** Công việc thoát khỏi cảnh báo quá hạn và ngừng nhắc việc. Nhưng: cơ hội **vẫn** mang cảnh báo nguội lạnh, và dòng thời gian của cơ hội **không** xuất hiện bản ghi tương tác mới.
5. Lọc danh sách công việc theo nhánh kết thúc.
6. **Kỳ vọng nghiệm thu:** Công việc này nằm trong nhóm **Hủy bỏ**, không nằm trong nhóm **Hoàn thành**. Đây là căn cứ để báo cáo năng suất (FEAT-26, Roadmap v7.0) sau này loại trừ đúng.

### UAT-03 — Lịch định kỳ ngày 31 không bị trôi *(FEAT-12)*

1. Tạo công việc mẫu "Báo cáo tài chính định kỳ", chu kỳ hằng tháng, hạn chót kỳ đầu **31/01/2026**.
2. Chạy sinh việc cho tháng 2. **Kỳ vọng:** hạn chót **28/02/2026**.
3. Chạy sinh việc cho tháng 3. **Kỳ vọng:** hạn chót **31/03/2026** — *không phải 28/03*.
4. Chạy sinh việc cho tháng 4. **Kỳ vọng:** hạn chót **30/04/2026**.
5. **Kỳ vọng nghiệm thu:** Chạy tiếp tháng 5 cho hạn chót **31/05/2026**. Ngày neo 31 được bảo toàn qua toàn bộ chuỗi, không trôi dần.

### UAT-04 — Hoàn thành công việc đánh thức cơ hội và ghi vào dòng thời gian *(FEAT-25, FEAT-31)*

1. Tạo Cơ hội bán hàng chưa có tương tác 20 ngày, đang bị cảnh báo nguội lạnh.
2. Tạo công việc "Gọi điện tái kết nối", nhóm công việc **Gọi điện**, gắn với cơ hội này.
3. Nhân viên gọi xong, đánh dấu công việc **Hoàn thành**.
4. **Kỳ vọng nghiệm thu:** Cảnh báo nguội lạnh trên cơ hội biến mất ngay. Dòng thời gian của cơ hội xuất hiện **bản ghi tương tác loại cuộc gọi**, ghi đúng người thực hiện và thời điểm.

### UAT-05 — Bảo vệ kết quả công việc đã kết thúc *(BR-05.3, BR-05.4)*

1. Mở màn hình sửa một công việc đang ở trạng thái **Hoàn thành**.
2. **Kỳ vọng:** Ba trường người phụ trách, hạn chót và mốc nhắc việc hiển thị dạng **không cho nhập**, kèm chỉ dẫn mở lại công việc. Các trường tiêu đề, mô tả, nhãn vẫn nhập được bình thường.
3. Sửa tiêu đề và lưu. **Kỳ vọng:** Lưu thành công.
4. Chuyển trạng thái về đang mở ngay trên biểu mẫu. **Kỳ vọng:** Ba trường lập tức mở khóa, không cần tải lại trang.
5. **Kỳ vọng nghiệm thu:** Đổi hạn chót và lưu cùng lúc với việc mở lại — lưu thành công, công việc ở trạng thái đang mở với hạn chót mới.

### UAT-06 — Bàn giao khi nhân viên nghỉ việc *(FEAT-38, Quyết định 2)*

1. Nhân viên A (phòng X) có 5 công việc đang mở và 20 công việc đã hoàn thành trong Quý 1.
2. Quản trị viên thực hiện **Bàn giao**, phạm vi **Toàn bộ công việc**, người nhận là B (phòng Y), lý do "A nghỉ việc từ 01/10".
3. **Kỳ vọng:** Cả 25 công việc chuyển sang B. Mở một việc đã hoàn thành: người phụ trách là B, nhưng **mốc hoàn thành, hạn chót và lịch sử trạng thái không đổi**.
4. **Kỳ vọng:** 5 việc đang mở thuộc phòng Y. 20 việc đã hoàn thành **vẫn thuộc phòng X**.
5. **Kỳ vọng:** Mở lịch sử chuyển trạng thái của một việc đã hoàn thành — vẫn ghi nhận **A** là người đã đưa công việc sang trạng thái Hoàn thành, kèm thời điểm gốc. Dữ liệu này không bị bàn giao viết lại, và là căn cứ để báo cáo năng suất (FEAT-26, Roadmap v7.0) sau này quy đúng công cho A.
6. **Kỳ vọng nghiệm thu:** Nhật ký bàn giao ghi đủ người thực hiện, thời điểm, A, B, số lượng và lý do. Trưởng phòng Y nhìn thấy cả 25 công việc mà B đang phụ trách, kể cả 20 việc còn thuộc phòng X (`BR-38.5`).

### UAT-07 — Thao tác hàng loạt trên lô hỗn hợp *(BR-27.3, FEAT-28)*

1. Lọc ra 200 công việc, trong đó có 60 việc đã kết thúc.
2. Chọn tất cả, chọn thao tác **đổi người phụ trách**.
3. **Kỳ vọng:** **Trước khi chạy**, hệ thống cảnh báo rõ 60 việc sẽ bị bỏ qua vì đã kết thúc, kèm gợi ý dùng chức năng Bàn giao nếu mục đích là chuyển giao nhân sự.
4. Xác nhận tiếp tục.
5. **Kỳ vọng nghiệm thu:** 140 việc cập nhật thành công. Báo cáo kết quả liệt kê đủ 60 việc bị bỏ qua kèm lý do cho từng dòng.

### UAT-08 — Giao việc cho nhân viên phòng khác *(BR-01.3)*

1. Quản lý phòng A tạo công việc và giao cho nhân viên B thuộc phòng B.
2. **Kỳ vọng:** Trưởng phòng B mở danh sách công việc của phòng mình và **nhìn thấy** công việc này.
3. Đổi người phụ trách sang nhân viên thuộc phòng C.
4. **Kỳ vọng nghiệm thu:** Trưởng phòng B không còn thấy công việc; Trưởng phòng C bắt đầu thấy.

### UAT-09 — Cấu hình danh mục trạng thái không làm tê liệt vận hành *(FEAT-37)*

1. Quản trị viên mở cấu hình danh mục trạng thái.
2. Thử vô hiệu hóa trạng thái nhánh **Hoàn thành** duy nhất đang hoạt động.
3. **Kỳ vọng:** Hệ thống **từ chối**, giải thích rằng sẽ không còn cách nào đóng công việc.
4. Vô hiệu hóa một trạng thái đang mở còn 12 công việc.
5. **Kỳ vọng:** Cảnh báo 12 công việc bị ảnh hưởng, yêu cầu chọn chuyển sang trạng thái khác hoặc giữ nguyên.
6. **Kỳ vọng nghiệm thu:** Chọn giữ nguyên — Bảng trạng thái hiển thị cột "Trạng thái ngừng sử dụng" chứa 12 công việc, kéo ra được nhưng không kéo vào được.

### UAT-10 — Phạm vi dữ liệu không bị rò rỉ qua bộ lọc và xuất dữ liệu *(BR-06.5, BR-39.1, BR-31.4)*

1. Đăng nhập bằng tài khoản Nhân viên Kinh doanh chỉ có phạm vi Cá nhân.
2. Xóa toàn bộ bộ lọc trên Danh sách. **Kỳ vọng:** Chỉ thấy công việc của chính mình.
3. Xuất dữ liệu. **Kỳ vọng:** Tệp chỉ chứa công việc của chính mình.
4. Mở dòng thời gian một khách hàng có công việc của phòng khác.
5. **Kỳ vọng nghiệm thu:** Công việc ngoài phạm vi **không xuất hiện dưới bất kỳ hình thức nào** — không hiện rút gọn, không hiện dạng bị che, không hiện số lượng.

### UAT-11 — Nhắc việc đúng người, đúng lúc, đúng một lần *(FEAT-11, FEAT-24)*

1. Tạo công việc của nhân viên A, đặt mốc nhắc lúc 10:00.
2. Thử đặt mốc nhắc **sau** hạn chót. **Kỳ vọng:** Bị từ chối.
3. A mở ứng dụng trên hai trình duyệt, đang ở màn hình Cơ hội bán hàng.
4. **Kỳ vọng:** Đến 10:00 (chậm nhất 10:05), A nhận **đúng một** chuông, hiện ngay trên màn hình đang mở, bấm vào mở thẳng công việc. Quản lý của A **không** nhận chuông.
5. **Kỳ vọng nghiệm thu:** Tạo công việc khác có mốc nhắc lúc 15:00, hoàn thành lúc 14:00 — đến 15:00 **không có chuông nào phát**.

### UAT-12 — Thùng rác bảo vệ dữ liệu xóa nhầm *(FEAT-30)*

1. Tạo công việc gắn Cơ hội Y, trạng thái "Đang làm", rồi xóa.
2. **Kỳ vọng:** Công việc biến mất khỏi Danh sách, Lịch biểu và dòng thời gian của Y. Xuất hiện trong Thùng rác kèm **số ngày còn lại** trước khi xóa vĩnh viễn.
3. Khôi phục công việc.
4. **Kỳ vọng nghiệm thu:** Công việc trở lại đúng trạng thái "Đang làm", vẫn gắn Cơ hội Y, hiện lại trên dòng thời gian của Y, toàn bộ lịch sử còn nguyên.

---
### UAT-13 — Nhân viên bị vô hiệu hóa, công việc không chìm vào im lặng *(FEAT-09, BR-09.2)*

1. Nhân viên A đang phụ trách 5 công việc đang mở, 10 công việc đã kết thúc và 1 công việc mẫu định kỳ hằng tuần.
2. Quản trị viên vô hiệu hóa tài khoản A.
3. **Kỳ vọng:** 5 công việc đang mở được gắn nhãn **"Cần phân công lại"** và xuất hiện trong danh sách cảnh báo của Quản trị viên. 10 công việc đã kết thúc giữ nguyên, không gắn nhãn.
4. **Kỳ vọng:** Công việc mẫu định kỳ **ngừng sinh kỳ mới** và cũng nằm trong danh sách cảnh báo.
5. Chờ qua mốc sinh kỳ tiếp theo.
6. **Kỳ vọng:** Không có công việc con nào được sinh và gán cho A.
7. Quản trị viên mở màn hình giao việc, tìm A trong danh sách người nhận.
8. **Kỳ vọng nghiệm thu:** A **không** xuất hiện trong danh sách chọn người nhận việc. Sau khi Quản trị viên bàn giao mẫu định kỳ sang B, việc sinh kỳ tiếp tục bình thường và **không sinh bù** các kỳ đã bỏ lỡ.

### UAT-14 — Ghi nhận tương tác thủ công, tính bất biến và thu hồi có kiểm soát *(FEAT-15..19, BR-15.1, BR-15.6)*

1. Nhân viên mở hồ sơ Khách hàng X, ghi nhận một **cuộc gọi** đã diễn ra: chiều gọi đi, thời lượng 12 phút, kết quả "Khách hẹn gọi lại tuần sau".
2. **Kỳ vọng:** Bản ghi xuất hiện trên dòng thời gian của X, ghi đúng người thực hiện và thời điểm.
3. Hôm nay là 17/09. Ghi nhận thêm một **cuộc họp** đã diễn ra ngày 15/09, chọn thời điểm tương tác là 15/09.
4. **Kỳ vọng:** Bản ghi cuộc họp nằm ở vị trí ngày 15/09 trên dòng thời gian, không phải 17/09.
5. Thử sửa nội dung bản ghi cuộc gọi ở bước 1.
6. **Kỳ vọng:** Không có chức năng sửa. Chỉ có tùy chọn tạo bản ghi đính chính mới.
7. Thử xóa bản ghi cuộc gọi đó.
8. **Kỳ vọng:** Không có chức năng xóa. Bản ghi gốc vẫn còn nguyên trên dòng thời gian. Quan hệ với Khách hàng X đã được đánh thức từ bước 1.
9. Nhân viên Kinh doanh thường thử tìm chức năng thu hồi bản ghi cuộc gọi đó.
10. **Kỳ vọng:** Không thấy chức năng này.
11. Quản trị viên Không gian làm việc thu hồi bản ghi cuộc gọi đó, nhập lý do "Ghi nhầm vào sai hồ sơ khách hàng".
12. **Kỳ vọng nghiệm thu:** Bản ghi biến mất khỏi dòng thời gian của X nhưng vẫn tra cứu được đầy đủ trong dấu vết hệ thống kèm lý do. Quan hệ với Khách hàng X **không** quay lại trạng thái nguội lạnh — việc đánh thức ở bước 1 không bị hoàn tác.

### UAT-15 — Công việc sinh tự động từ quy trình *(FEAT-13)*

1. Quản trị viên cấu hình một quy trình: khi Cơ hội bán hàng chuyển sang giai đoạn "Đàm phán" thì tạo công việc "Chuẩn bị hợp đồng" giao cho người phụ trách cơ hội.
2. Chuyển một cơ hội sang giai đoạn "Đàm phán".
3. **Kỳ vọng:** Công việc được tạo, gắn đúng cơ hội đó, giao đúng người phụ trách cơ hội, và thuộc đơn vị tổ chức của người đó (`BR-01.3`).
4. Mở chi tiết công việc vừa tạo.
5. **Kỳ vọng:** Thấy thông tin công việc do quy trình nào tạo ra (`BR-13.2`).
6. Vô hiệu hóa tài khoản người phụ trách cơ hội, rồi chuyển một cơ hội khác sang "Đàm phán".
7. **Kỳ vọng:** **Không** tạo công việc. Lỗi được ghi vào nhật ký quy trình để Quản trị viên xử lý.
8. **Kỳ vọng nghiệm thu:** Cấu hình một quy trình tự kích hoạt chính nó qua sự kiện công việc — hệ thống chặn sau ngưỡng an toàn và ghi nhật ký cảnh báo, không chạy vô hạn.

### UAT-16 — Đóng hàng loạt không tạo lịch sử chăm sóc giả *(BR-27.6, BR-27.7)*

1. Lọc ra 50 công việc đang mở, tất cả đều gắn ngữ cảnh khách hàng, nhóm công việc "Gọi điện".
2. Chọn tất cả, chọn thao tác đổi trạng thái sang nhánh **Hoàn thành**.
3. **Kỳ vọng:** Hệ thống yêu cầu nhập lý do trước khi chạy, và nêu rõ thao tác này **không sinh bản ghi tương tác**.
4. Nhập lý do "Dọn tồn đọng quý 3 theo chỉ đạo", xác nhận chạy.
5. **Kỳ vọng:** 50 công việc chuyển sang Hoàn thành. 50 khách hàng liên quan **được đánh thức**, thoát cảnh báo nguội lạnh.
6. **Kỳ vọng nghiệm thu:** Mở dòng thời gian của 3 khách hàng bất kỳ trong nhóm — **không** có bản ghi cuộc gọi nào được sinh thêm. Mở dấu vết của một công việc trong lô — thấy lý do đóng hàng loạt, người thực hiện và thời điểm.

---

## 7. Phụ lục A — Danh mục Khái niệm Nghiệp vụ

*Phụ lục này mô tả các khái niệm nghiệp vụ và thông tin mà hệ thống lưu giữ về chúng. **Đây là mô tả nghiệp vụ, không phải thiết kế dữ liệu kỹ thuật** — cách tổ chức lưu trữ thuộc thẩm quyền của đội ngũ phát triển, miễn là đáp ứng đầy đủ các quy tắc tại Mục 3.*

### Công việc

| Thông tin nghiệp vụ | Bắt buộc | Ý nghĩa |
| --- | :---: | --- |
| Không gian làm việc | Có | Doanh nghiệp sở hữu công việc |
| Tiêu đề | Có | Tên công việc, tối đa 500 ký tự |
| Mô tả chi tiết | Không | Nội dung yêu cầu công việc |
| Hạn chót | Có | Thời điểm công việc phải hoàn thành |
| Trạng thái | Có | Tham chiếu danh mục trạng thái của Không gian làm việc |
| Mức ưu tiên | Có | Một trong bốn mức tại `BR-03.1` |
| Nhóm công việc | Không | Tham chiếu danh mục nhóm công việc; quyết định loại tương tác sinh ra tại `BR-25.3` |
| Nguồn công việc | Không | Tham chiếu danh mục nguồn công việc |
| Người phụ trách | Có | Nhân viên chịu trách nhiệm; mặc định là người tạo (`BR-01.2`) |
| Đơn vị tổ chức | Không | Đơn vị của người phụ trách (`BR-01.3`); trống nếu người phụ trách không thuộc đơn vị nào |
| Ngữ cảnh khách hàng | Không | Một trong bốn loại thực thể tại `BR-02.1` |
| Nhãn phân loại | Không | Danh sách nhãn tự do phục vụ lọc nhanh |
| Mốc nhắc việc | Không | Thời điểm phát chuông nhắc; phải ≤ hạn chót (`BR-11.1`) |
| Thời điểm kết thúc | — | Hệ thống ghi tự động khi công việc vào trạng thái kết thúc |
| Lịch sử chuyển trạng thái | — | Toàn bộ các lần chuyển trạng thái, phục vụ `BR-05.2` |
| Cấu hình lặp lại | Không | Chu kỳ, khoảng lặp, ngày neo, ngày kết thúc lặp (FEAT-12) |
| Công việc mẫu nguồn | — | Với công việc sinh tự động từ chu kỳ, trỏ về công việc mẫu |
| Trường dữ liệu tùy biến | Không | Theo định nghĩa của Không gian làm việc (FEAT-40) |
| Cờ thiếu dữ liệu bắt buộc | — | Theo `BR-40.3` |
| Thời điểm xóa mềm | — | Có giá trị khi công việc nằm trong Thùng rác |
| Cần phân công lại | — | Cờ tự động khi người phụ trách bị vô hiệu hóa (`BR-09.2`) hoặc khi khôi phục về một người phụ trách đã bị vô hiệu hóa (`BR-30.3`, `BR-38.7`) |
| Thông tin tạo lập & cập nhật | — | Người tạo, thời điểm tạo, người cập nhật cuối, thời điểm cập nhật cuối |

### Trạng thái Công việc

| Thông tin nghiệp vụ | Ý nghĩa |
| --- | --- |
| Tên hiển thị | Tên do doanh nghiệp đặt |
| Màu sắc | Màu thể hiện trên Bảng trạng thái và Danh sách |
| Thứ tự | Vị trí cột trên Bảng trạng thái |
| Là trạng thái mặc định | Trạng thái gán cho công việc mới tạo; đúng một trạng thái mang cờ này (`BR-37.1`) |
| Là trạng thái kết thúc | Công việc mang trạng thái này không còn cần xử lý |
| Nhánh kết thúc | **Hoàn thành** hoặc **Hủy bỏ**; bắt buộc khi là trạng thái kết thúc (`BR-37.2`) |
| Đang hoạt động | Trạng thái còn được sử dụng hay đã ngừng |

### Nhóm Công việc & Nguồn Công việc

| Thông tin nghiệp vụ | Ý nghĩa |
| --- | --- |
| Tên hiển thị | Tên do doanh nghiệp đặt |
| Biểu tượng | Biểu tượng nhận diện trực quan (chỉ Nhóm công việc) |
| Loại tương tác sinh ra | Loại bản ghi tương tác tạo khi hoàn thành công việc thuộc nhóm này (`BR-25.3`, chỉ Nhóm công việc) |
| Đang hoạt động | Danh mục còn được sử dụng hay đã ngừng |

### Bản ghi Tương tác

| Thông tin nghiệp vụ | Ý nghĩa |
| --- | --- |
| Hồ sơ liên quan | Khách hàng, Doanh nghiệp hoặc Cơ hội bán hàng (`BR-15.2`) |
| Loại tương tác | Cuộc gọi, Cuộc họp, Email, Ghi chú, Tin nhắn |
| Người thực hiện | Nhân viên đã thực hiện tương tác |
| Thời điểm tương tác | Thời điểm tương tác **thực tế diễn ra**, có thể khác thời điểm nhập liệu (`BR-15.3`) |
| Nội dung | Thông tin chi tiết theo từng loại tương tác (FEAT-15..19) |
| Công việc nguồn | Với bản ghi sinh tự động từ công việc, trỏ về công việc đó (`BR-25.3`) |
| Trạng thái thu hồi | Có/Không; bản ghi bị thu hồi biến mất khỏi dòng thời gian nhưng vẫn tra cứu được trong dấu vết hệ thống (`BR-15.6`) |
| Lý do thu hồi & người thu hồi | Chỉ có giá trị khi bản ghi đã bị thu hồi (`BR-15.6`) |

### Nhật ký Bàn giao

| Thông tin nghiệp vụ | Ý nghĩa |
| --- | --- |
| Người thực hiện bàn giao | Nhân viên thực hiện thao tác bàn giao (`BR-38.8`) |
| Người bàn giao đi | Người phụ trách cũ |
| Người nhận | Người phụ trách mới |
| Thời điểm bàn giao | Thời điểm thao tác được thực hiện |
| Phạm vi đã chọn | Chỉ công việc đang mở, hoặc Toàn bộ công việc (`BR-38.1`) |
| Số lượng công việc | Số công việc bị ảnh hưởng bởi lần bàn giao này |
| Lý do | Bắt buộc nhập, không xóa được (`BR-38.3`) |

---

## 8. Phụ lục B — Tham số cấu hình theo Không gian làm việc

Mỗi doanh nghiệp khách hàng có quy trình riêng, nên mọi quy tắc có nhiều hướng xử lý hợp lý đều là tham số cấu hình, với giá trị mặc định là hướng chuẩn. Ba loại quy tắc **không** được đưa vào đây và không bao giờ cấu hình được:

- **Cách ly dữ liệu giữa các Không gian làm việc** (`BR-02.2`, `NFR-05`) và mọi quy tắc phân quyền (`BR-01.3`, `BR-27.2`, `BR-31.4`, `BR-39.1`).
- **Toàn vẹn dữ liệu lịch sử**: bảo toàn ngày neo (`BR-12.2`), lịch sử chuyển trạng thái (`BR-05.2`), tính bất biến của bản ghi tương tác (`BR-15.1`), bảo toàn ghi nhận năng suất khi bàn giao (`BR-38.4`), xử lý đúng một lần (`BR-12.6`, `NFR-04`).
- **Lịch vận hành hạ tầng**: tần suất chạy và giờ chạy của các tiến trình nền do nhà cung cấp quyết định, không phải nhu cầu nghiệp vụ của doanh nghiệp. Tài liệu chỉ cam kết **kết quả** người dùng cảm nhận được (`BR-24.1` độ trễ tối đa, `BR-30.5` chạy mỗi ngày một lần vào giờ thấp điểm).

| Mã | Tên tham số | Giá trị mặc định | Ý nghĩa nghiệp vụ | Sàn / Trần | Quy tắc |
| --- | --- | :---: | --- | --- | :---: |
| `CFG-TASK-01` | Múi giờ Không gian làm việc | `GMT+7` | Múi giờ chuẩn cho mọi mốc thời gian nghiệp vụ | Bắt buộc có giá trị | Mục 2.3 |
| `CFG-TASK-02` | Mức ưu tiên mặc định | Trung bình | Mức ưu tiên gán cho công việc mới khi người dùng không chọn | Chọn trong bốn mức tại `BR-03.1` | `BR-01.1` |
| `CFG-TASK-03` | Khoảng nhắc việc gợi ý | 15 phút | Số phút trước hạn chót được gợi ý làm mốc nhắc việc | Từ 1 phút đến 30 ngày | `BR-11.1` |
| `CFG-TASK-04` | Ngưỡng bỏ qua nhắc việc quá cũ | 24 giờ | Nhắc việc trễ quá ngưỡng này sẽ bị bỏ qua sau sự cố | Tối đa 72 giờ | `BR-24.3` |
| `CFG-TASK-05` | Ngưỡng cảnh báo sắp đến hạn | 24 giờ | Khoảng thời gian trước hạn chót bắt đầu hiển thị cảnh báo nhẹ | Tối thiểu 1 giờ — đặt hẹp hơn thì cảnh báo không đủ thời gian để nhân viên phản ứng. Tối đa 7 ngày — đặt rộng hơn thì mọi công việc đều luôn ở trạng thái sắp đến hạn và cảnh báo mất ý nghĩa | `BR-04.4` |
| `CFG-TASK-06` | Số công việc tối đa mỗi thao tác hàng loạt | 200 | Giới hạn quy mô một lô | Từ 1 đến **1000** (trần do nhà cung cấp đặt, doanh nghiệp không nâng được) | `BR-27.1` |
| `CFG-TASK-07` | Thời hạn lưu trữ Thùng rác | 30 ngày | Số ngày trước khi công việc bị xóa vĩnh viễn | Tối thiểu **7 ngày**, tối đa **365 ngày** | `BR-30.4` |
| `CFG-TASK-08` | Tự động đánh thức quan hệ khi hoàn thành công việc | Bật | Bật/tắt cơ chế `BR-25.2`. Không ảnh hưởng `BR-25.1` | — | `BR-25.6` |
| `CFG-TASK-09` | Nhóm công việc **không** sinh bản ghi tương tác | Không nhóm nào | Chọn các nhóm công việc mà việc hoàn thành sẽ không sinh bản ghi tương tác. Dùng khi doanh nghiệp đã có nguồn ghi nhận khác cho loại tương tác đó | Không tắt được cho toàn bộ nhóm cùng lúc | `BR-25.7` |
| `CFG-TASK-10` | Bắt buộc nhập lý do khi dời hạn hàng loạt | Bật | Khi tắt, lý do trở thành tùy chọn | — | `BR-28.3` |
| `CFG-TASK-11` | Nhóm trường bị khóa trên công việc đã kết thúc | Người phụ trách, Hạn chót, Mốc nhắc việc | Chọn các trường không được sửa khi công việc đã kết thúc | Không được để rỗng. Mốc kết thúc và lịch sử chuyển trạng thái luôn khóa, không nằm trong lựa chọn | `BR-05.3` |
| `CFG-TASK-12` | Cho phép đổi ngữ cảnh khách hàng của công việc đã kết thúc | Cấm | `Cấm` hoặc `Cho phép với Quản trị viên, bắt buộc ghi lý do`. Dùng khi khách hàng cá nhân chuyển thành pháp nhân và cần gộp hồ sơ | Ngữ cảnh mới luôn phải thỏa `BR-02.2` | `BR-02.4` |
| `CFG-TASK-13` | Người nhận nhắc việc ngoài người phụ trách | Chỉ người phụ trách | `Chỉ người phụ trách` hoặc `Thêm quản lý trực tiếp khi công việc mức Khẩn cấp đã quá hạn` | Người nhận mở rộng phải có quyền xem công việc đó theo Ma trận Mục 5 | `BR-11.2` |
| `CFG-TASK-14` | Sinh bù các kỳ đã lỡ khi khôi phục công việc mẫu định kỳ | Không sinh bù | Dùng khi lịch định kỳ là nghĩa vụ hợp đồng cần bằng chứng đầy đủ (ví dụ lịch bảo trì thiết bị) | Khi bật, sinh bù **tối đa 12 kỳ** bất kể chu kỳ là ngày, tuần, tháng hay năm; giới hạn này doanh nghiệp không chỉnh được | `BR-12.7` |
| `CFG-TASK-15` | Cho phép xóa vĩnh viễn thủ công | Bật | Khi tắt, mọi công việc phải nằm đủ thời hạn lưu trữ mới bị xóa | Khi tắt vẫn phải xóa được theo yêu cầu xóa dữ liệu cá nhân hợp pháp | `BR-30.6` |
| `CFG-TASK-16` | Bắt buộc nhập lý do khi đổi trạng thái hàng loạt | Bật | Áp dụng cho cả đóng, hủy và mở lại hàng loạt. Khi tắt, lý do trở thành tùy chọn | — | `BR-27.7` |
| `CFG-TASK-17` | Số bước tối đa của chuỗi quy trình nối tiếp | 10 bước | Ngưỡng chặn vòng lặp khi quy trình tự động sinh công việc, công việc lại kích hoạt quy trình khác | Tối đa 50 bước — trần do nhà cung cấp đặt | `BR-13.4` |
| `CFG-TASK-18` | Người phụ trách khi tạo công việc bỏ trống | Tự gán người tạo | `Tự gán người tạo` / `Bắt buộc chọn tường minh` / `Cho phép chưa phân công` — dùng khi doanh nghiệp thường tạo hộ việc cho người khác (bắt buộc chọn), hoặc vận hành theo mô hình hàng đợi triage sau (cho phép chưa phân công) | Quy trình tự động hóa (FEAT-13) không có người tạo thật nên luôn xử lý như "Cho phép chưa phân công" khi cấu hình đang là "Tự gán người tạo" | `BR-01.2`, `BR-13.5` |
| `CFG-TASK-19` | Vai trò thấp nhất được thực hiện bàn giao công việc | Quản lý cấp phòng ban trở lên | `Quản lý cấp phòng ban trở lên` / `Trưởng nhóm trở lên` / `Chỉ Quản trị viên và Chủ sở hữu` | Quản trị viên và Chủ sở hữu luôn có quyền ở mọi cấu hình | `BR-38.8` |
| `CFG-TASK-20` | Vai trò thấp nhất được thu hồi bản ghi tương tác | Quản trị viên Không gian làm việc | `Quản trị viên Không gian làm việc` / `Quản lý cấp phòng ban trở lên` / `Trưởng nhóm trở lên` | Chủ sở hữu luôn có quyền ở mọi cấu hình | `BR-15.6` |
| `CFG-TASK-21` | Vai trò thấp nhất được xóa vĩnh viễn thủ công | Quản lý cấp phòng ban trở lên | `Quản lý cấp phòng ban trở lên` / `Trưởng nhóm trở lên` / `Chỉ Quản trị viên và Chủ sở hữu` | Chỉ có hiệu lực khi `CFG-TASK-15` đang bật | `BR-30.6` |
| `CFG-TASK-22` | Thông báo khi được giao việc | Bật | Khi tắt, người nhận không nhận thông báo tức thời nhưng công việc vẫn hiện đầy đủ trên Danh sách/Lịch biểu | — | `BR-09.3` |

**Quyền cấu hình:** Chỉ Quản trị viên Không gian làm việc và Chủ sở hữu được thay đổi các tham số trên (Ma trận Mục 5). Mọi thay đổi được ghi dấu vết theo `NFR-06`.

---

## 9. Phụ lục C — Nhật ký giải quyết mâu thuẫn nghiệp vụ tại v6.0

*Ghi nhận các mâu thuẫn nội tại của v5.2 và cách v6.0 giải quyết, phục vụ truy vết quyết định.*

| # | Mâu thuẫn tại v5.2 | Cách giải quyết tại v6.0 |
| :---: | --- | --- |
| 1 | Quy tắc khóa sửa đổi chặn dời hạn, trong khi có tính năng dời hạn hàng loạt | `BR-27.3` bắt buộc cảnh báo trước khi chạy; FEAT-28 nêu rõ chỉ áp dụng cho công việc đang mở |
| 2 | Quy tắc khóa sửa đổi chặn đổi người phụ trách, khiến nghiệp vụ bàn giao nhân sự bế tắc | Quyết định 2: tách FEAT-38 Bàn giao thành nghiệp vụ riêng, miễn trừ `BR-05.3`, không phá dữ liệu kết quả |
| 3 | Mở lại công việc xóa trắng mốc hoàn thành, làm mất dữ liệu lịch sử mà chính quy tắc khóa muốn bảo vệ | `BR-05.2` bổ sung lịch sử chuyển trạng thái; mốc các lần hoàn thành trước vẫn tra cứu được |
| 4 | Danh mục trạng thái tự do cấu hình nhưng không có ràng buộc toàn vẹn, doanh nghiệp có thể tự làm tê liệt vận hành | FEAT-37 mới, đặc biệt `BR-37.1` và `BR-37.3` |
| 5 | Trạng thái kết thúc gộp chung Hoàn thành và Hủy bỏ, khiến việc bị hủy vẫn tính vào năng suất và đánh thức khách hàng | Quyết định 1: tách hai nhánh; `BR-05.1`, `BR-25.2`, `BR-37.2` |
| 6 | Quy tắc bảo toàn ngày neo chỉ đặc tả ca ngày 31 hằng tháng, bỏ trống các ca biên khác | `BR-12.2` đến `BR-12.5` phủ đủ: hằng năm 29/02, khoảng lặp lớn hơn 1, chu kỳ hằng tuần |
| 7 | Cơ chế đánh thức quan hệ không định nghĩa chiều ngược: mở lại, hủy bỏ, xóa công việc | `BR-25.2` (chỉ nhánh Hoàn thành), `BR-25.4`, `BR-25.5` |
| 8 | Không chốt múi giờ, khiến mọi quy tắc thời gian mơ hồ với khách hàng đa quốc gia | Quyết định 3 và Mục 2.3 |
| 9 | Đơn vị tổ chức của công việc không được định nghĩa rõ, gây lỗ hổng phân quyền khi giao việc chéo phòng ban | `BR-01.3` chốt theo người phụ trách, kèm `AC-01.3.1` và `AC-01.3.2` |
| 10 | Vai trò trong Ma trận phân quyền không khớp danh sách vai trò người dùng | Mục 5 dùng đúng bảy vai trò của Mục 2.2, kèm Ghi chú 4 |
| 11 | Một số tính năng gắn nhãn đã triển khai nhưng không có đặc tả và không có quy tắc để nghiệm thu | FEAT-13 và FEAT-31 được đặc tả đầy đủ kèm AC |
| 12 | Các năng lực đang vận hành nhưng không có trong tài liệu: nguồn công việc, trường tùy biến, xuất dữ liệu, bàn giao | Bổ sung `BR-01.5`, FEAT-37, FEAT-38, FEAT-39, FEAT-40 |
| 13 | Quy tắc khóa sửa đổi chỉ tồn tại ở phía máy chủ, người dùng nhập xong mới bị từ chối | `BR-05.4` bắt buộc giao diện thể hiện ràng buộc ngay từ đầu |
| 14 | Chỉ có 4 kịch bản nghiệm thu cho toàn bộ phân hệ, không phủ phân quyền và thao tác hàng loạt | Mục 6 mở rộng thành 16 kịch bản, phủ cả phân quyền, bàn giao, cấu hình danh mục và cách ly dữ liệu |
| 15 | Bảng đối chiếu sai lệch mã nguồn nằm trong SRS, tham chiếu tới vị trí tệp và số dòng | Đã gỡ khỏi SRS. Việc rà soát khoảng cách giữa đặc tả và triển khai thuộc tài liệu backlog riêng |

### Vòng review thứ hai — mâu thuẫn do chính v6.0 tạo ra và khoảng trống còn sót

*Ba lượt review độc lập với ba lăng kính khác nhau: khả năng cấu hình đa tenant, tính đầy đủ của danh mục tính năng, và mâu thuẫn nội tại.*

| # | Vấn đề phát hiện ở vòng 2 | Cách giải quyết |
| :---: | --- | --- |
| 16 | Đổi trạng thái hàng loạt sang nhánh Hoàn thành sẽ sinh hàng loạt bản ghi tương tác bất biến cho những cuộc gọi chưa từng diễn ra, và không xóa được | `BR-27.6` — thao tác hàng loạt vẫn đánh thức quan hệ nhưng không sinh bản ghi tương tác. `BR-27.7` bắt buộc ghi lý do khi đóng hàng loạt |
| 17 | `BR-37.3` cho chuyển công việc sang trạng thái bất kỳ khi vô hiệu hóa danh mục — biến thao tác dọn danh mục thành mở lại hoặc đóng hàng loạt ngoài ý muốn | `BR-37.3` bổ sung ràng buộc: trạng thái đích phải cùng loại và cùng nhánh; không ghi lại mốc kết thúc, không đánh thức, không sinh bản ghi |
| 18 | Bàn giao công việc mẫu định kỳ hoàn toàn chưa được định nghĩa; nhân viên nghỉ việc thì hệ thống vẫn sinh kỳ mới gán cho tài khoản đã khóa | `BR-38.6` (mẫu định kỳ chuyển theo công việc đang mở) và bổ sung vào `BR-09.2` (tạm dừng sinh kỳ, đưa vào danh sách cần phân công lại) |
| 19 | `BR-30.1` bảo ẩn công việc khỏi dòng thời gian, `BR-25.5` và `BR-15.1` bảo giữ bản ghi tương tác — hai người kiểm thử sẽ kết luận ngược nhau | `BR-30.1` nêu rõ bản ghi tương tác ở lại dòng thời gian kể cả khi công việc bị xóa vĩnh viễn. Bổ sung `AC-30.1.2`, `AC-30.1.3` |
| 20 | `BR-38.5` giữ đơn vị tổ chức cũ cho công việc đã kết thúc, tạo vùng mù: không quản lý phòng nào nhìn thấy | `BR-38.5` quy định công việc hiển thị cho quản lý của **cả hai** đơn vị; `BR-01.3` ghi rõ đây là ngoại lệ được công nhận |
| 21 | Dời hạn chót về sớm hơn có thể đẩy mốc nhắc việc thành muộn hơn hạn chót, vi phạm chính `BR-11.1` | `BR-11.1` bổ sung quy tắc mốc nhắc tự dời theo, hoặc bị xóa kèm thông báo nếu rơi vào quá khứ |
| 22 | Tham số cấu hình cho phép **tắt toàn bộ** cơ chế sinh bản ghi tương tác — tức cho doanh nghiệp tự chọn lấy vấn đề nghiệp vụ số 3 mà `BR-25.3` được viết ra để giải | `BR-25.7` — thu hẹp thành tắt **theo từng nhóm công việc**, không có công tắc tổng |
| 23 | Tính bất biến của bản ghi tương tác không có lối thoát nào cho ca tích hợp lỗi ghi nhầm hàng loạt vào sai hồ sơ | `BR-15.6` — cơ chế thu hồi có kiểm soát: dữ liệu không mất, chỉ ngừng được coi là hợp lệ. Tính bất biến vẫn không cấu hình được |
| 24 | Toàn bộ tham số cấu hình chỉ nằm ở Phụ lục B, không quy tắc nào dẫn chiếu ngược — người đọc quy tắc tưởng giá trị là cố định | Mọi quy tắc có giá trị cấu hình được nay đều dẫn mã tham số tương ứng |
| 25 | Ba tham số vốn là lịch vận hành hạ tầng (tần suất quét, giờ chạy dọn dẹp) bị để cho doanh nghiệp chỉnh, gây rủi ro quá tải lan sang doanh nghiệp khác | Gỡ khỏi Phụ lục B. Tài liệu chỉ cam kết kết quả người dùng cảm nhận được, không cam kết lịch chạy |
| 26 | Hai tham số thiếu sàn/trần, có thể bị đặt về giá trị vô hiệu hóa chính tính năng | Bổ sung cột Sàn/Trần: giới hạn lô tối đa 1000, thùng rác tối thiểu 7 ngày |
| 27 | Hai kịch bản nghiệm thu lấy báo cáo năng suất làm điều kiện, nhưng năng lực đó thuộc Roadmap — nghiệm thu qua màn hình không tồn tại | UAT-02 và UAT-06 chuyển sang kiểm chứng bằng nhánh kết thúc và lịch sử chuyển trạng thái, là thứ thực sự có ở phiên bản này |
| 28 | Không kịch bản nghiệm thu nào phủ FEAT-09, FEAT-13 và phần ghi nhận tương tác thủ công | Bổ sung UAT-13 đến UAT-16 |
| 29 | Ranh giới ngoài phạm vi nêu rõ hạ tầng tổng đài nhưng bỏ trống hạ tầng gửi email và tin nhắn, dù cùng tình huống | Mục 1.2 bổ sung ranh giới cho email và tin nhắn |
| 30 | Thiếu hai năng lực mà mọi CRM lớn đều có: nhập dữ liệu hàng loạt và nhắc việc qua kênh ngoài ứng dụng | Ghi nhận thành FEAT-41 và FEAT-42, thuộc Roadmap v7.0 |
| 31 | Hai tên tính năng mô tả giải pháp hoặc gây hiểu nhầm; quy tắc thể hiện mức ưu tiên ấn định màu cụ thể — là quyết định thiết kế, không phải quy tắc nghiệp vụ | Đổi tên FEAT-08 và FEAT-36; `BR-03.2` phát biểu lại theo yêu cầu nghiệp vụ |

---

### Vòng review thứ ba — kiểm tra chính các sửa đổi vòng 2 và chất lượng bộ Tiêu chí Chấp nhận

*Hai lượt review độc lập: một soi các quy tắc mới thêm ở vòng 2, một đánh giá bộ AC theo góc nhìn đội kiểm thử sẽ thực thi.*

| # | Vấn đề phát hiện ở vòng 3 | Cách giải quyết |
| :---: | --- | --- |
| 32 | Ranh giới giữa thao tác **đơn lẻ** và **hàng loạt** không được định nghĩa: cùng một hành vi hoàn thành công việc cho hai kết quả trái ngược trên dòng thời gian khách hàng tùy đường thao tác | `BR-25.3` nêu rõ phạm vi áp dụng theo **đường thao tác** và liệt kê đủ ba trường hợp loại trừ ngay tại chỗ; `BR-08.2` xác nhận kéo thả là thao tác đơn lẻ |
| 33 | **Mở lại hàng loạt** gỡ mốc kết thúc của hàng chục công việc mà không cảnh báo, không yêu cầu lý do — đúng lỗ hổng `BR-37.3` vừa vá ở vòng 2 nhưng qua một lối đi khác | `BR-27.3` mở rộng sang thao tác đổi trạng thái về trạng thái đang mở; `BR-27.7` áp dụng cho cả ba chiều đóng, hủy và mở lại |
| 34 | Công việc **không có Nhóm công việc** thì sinh bản ghi tương tác loại gì — ca xảy ra hằng ngày vì nhóm là tùy chọn, nhưng không quy tắc nào trả lời qua hai vòng | `BR-25.3` bổ sung: không sinh bản ghi, vẫn đánh thức quan hệ. Kèm quy định cho nhóm đã bị vô hiệu hóa |
| 35 | `BR-25.3` không quy định **tính lũy đẳng**: mở lại rồi hoàn thành lần nữa có sinh bản ghi thứ hai không | `BR-25.3` chốt mỗi lần hoàn thành sinh một bản ghi, kèm lý do nghiệp vụ về các lần liên hệ lặp lại |
| 36 | `BR-15.6` thu hồi bản ghi có **hoàn tác việc đánh thức** không — trái chiều trực tiếp với `BR-25.4` và `BR-25.5` | `BR-15.6` chốt: thu hồi không hoàn tác đánh thức, thống nhất nguyên tắc sự kiện quá khứ không đảo ngược |
| 37 | `BR-11.1` và `BR-28.4` quy định **ngược nhau** về việc dời mốc nhắc khi hạn chót dời ra xa; `AC-28.4.1` kiểm chứng theo `BR-28.4` | `BR-11.1` viết lại theo hướng dời **vô điều kiện**, thống nhất với `BR-28.4`. Bổ sung cách thông báo gộp khi dời hàng loạt |
| 38 | `BR-05.4` và các kịch bản nghiệm thu nói cứng "**ba trường** bị khóa" trong khi vòng 2 vừa cho phép cấu hình danh sách này | `BR-05.4` phát biểu theo cấu hình hiện hành; bổ sung AC kiểm chứng cả khi mở rộng lẫn thu hẹp danh sách |
| 39 | `BR-38.1` nói bàn giao "**toàn bộ công việc**" nhưng `BR-38.7` lại loại trừ Thùng rác — mâu thuẫn trong cùng một tính năng | `BR-38.1` nêu rõ cả hai phạm vi đều không gồm Thùng rác, và làm rõ phạm vi Toàn bộ có gồm nhánh Hủy bỏ |
| 40 | Khôi phục công việc từ Thùng rác **không kiểm tra lại tính hợp lệ**: có thể đưa công việc về tay tài khoản đã vô hiệu hóa hoặc trỏ tới khách hàng đã bị xóa vĩnh viễn | `BR-30.3` bổ sung quy tắc kiểm tra lại khi khôi phục, kèm dấu vết |
| 41 | Toàn bộ **bốn trên năm loại tương tác** (cuộc họp, email, ghi chú, tin nhắn) không có một tiêu chí chấp nhận nào — chỉ cuộc gọi được kiểm chứng | Bổ sung tiêu chí chấp nhận riêng cho từng loại tương tác |
| 42 | Hai quy tắc mới của vòng 2 là `BR-38.6` và `BR-38.7` không có tiêu chí chấp nhận nào; một mã tiêu chí bị đánh nhầm sang quy tắc khác | Bổ sung đủ tiêu chí cho cả hai; sửa mã bị đánh nhầm và bổ sung tiêu chí cho quy tắc về quyền bàn giao |
| 43 | Ngưỡng chặn vòng lặp quy trình tự động hóa không có con số cụ thể ở đâu — đội phát triển không cài đặt được, đội kiểm thử không nghiệm thu được | `BR-13.4` chốt ngưỡng 10 bước, đưa thành tham số `CFG-TASK-17` có trần nhà cung cấp |
| 44 | Trần chống bùng nổ của tham số sinh bù bị vô hiệu vì tính theo thời hạn Thùng rác (tối đa 365 ngày), lại nhầm lẫn đơn vị ngày và kỳ | Chốt trần cố định **12 kỳ** cho mọi chu kỳ |
| 45 | Hai tham số thiếu sàn/trần: khoảng nhắc gợi ý và ngưỡng cảnh báo sắp đến hạn — đặt quá rộng thì cảnh báo mất hết ý nghĩa | Bổ sung sàn/trần cho cả hai |
| 46 | Một số tiêu chí chấp nhận mô tả **trạng thái nội bộ** hoặc bối cảnh mà đội kiểm thử không dựng được qua giao diện | Viết lại kèm cách dựng bối cảnh, đánh dấu rõ những ca thuộc diện kiểm thử kỹ thuật |
| 47 | Nhiều quy tắc nhiều mệnh đề chỉ được kiểm chứng ở mệnh đề đầu: kế thừa thuộc tính công việc định kỳ, ngày kết thúc lặp, phân trang, lọc dòng thời gian, xuất bất đồng bộ, phân biệt người thực hiện và người phụ trách | Bổ sung tiêu chí chấp nhận cho từng mệnh đề còn thiếu. Số quy tắc không có tiêu chí giảm từ 16 xuống 5, và cả 5 đều có lý do chính đáng |
| 48 | Một tiêu chí vẫn lấy báo cáo năng suất làm căn cứ dù năng lực đó thuộc Roadmap — sót của chính vòng 2 | Chuyển sang kiểm chứng bằng nhánh kết thúc và dòng thời gian |
| 49 | Nhãn tự đánh giá của tài liệu sai lệch: ghi 12 kịch bản nghiệm thu trong khi thực tế có 16 | Sửa lại cho khớp nội dung |

**Kết quả sau ba vòng:** 113 quy tắc nghiệp vụ, **208 tiêu chí chấp nhận**, 16 kịch bản nghiệm thu, 17 tham số cấu hình. **108 trên 113 quy tắc có tiêu chí chấp nhận kiểm chứng**; 5 quy tắc còn lại thuộc Roadmap, được phủ gián tiếp, hoặc ủy quyền đặc tả sang tài liệu khác.

### Vòng review thứ tư — kiểm tra chính các sửa đổi vòng 3

*Hai lượt review độc lập: một soi lại các quy tắc vừa viết lại ở vòng 3 để tìm mâu thuẫn mới do chính vòng 3 tạo ra, một đánh giá độ phủ và chất lượng của 56 tiêu chí chấp nhận vừa bổ sung.*

| # | Vấn đề phát hiện ở vòng 4 | Cách giải quyết |
| :---: | --- | --- |
| 50 | `BR-30.3` (khôi phục từ Thùng rác) chỉ kiểm tra lại 2 trong 3 tình huống có thể phát sinh khi công việc nằm trong Thùng rác nhiều tuần: người phụ trách bị vô hiệu hóa, ngữ cảnh bị xóa vĩnh viễn — bỏ sót trường hợp **trạng thái hoặc Nhóm công việc bị vô hiệu hóa** trong lúc công việc nằm ở đó, dù log #40 tuyên bố đã giải quyết đầy đủ | `BR-30.3` bổ sung trường hợp thứ ba, dẫn chiếu `BR-37.3`: khôi phục vẫn giữ nguyên trạng thái/nhóm đã vô hiệu hóa, hiển thị ở cột "Trạng thái ngừng sử dụng", không ép chuyển đổi |
| 51 | Tuyên bố vòng 3 "bổ sung tiêu chí chấp nhận riêng cho từng loại tương tác" chỉ đúng về hình thức: Cuộc họp (`FEAT-16`), Email (`FEAT-17`), Ghi chú (`FEAT-18`) mỗi loại chỉ có 1-2 AC hời hợt, không kiểm chứng đặc thù nghiệp vụ thật — Email thiếu hẳn chiều **nhận**, Cuộc họp không phân biệt hình thức trực tiếp/trực tuyến, Ghi chú chỉ thử một tệp đính kèm | Bổ sung `AC-16.1.3` (phân biệt hình thức), `AC-17.1.3` (chiều nhận, địa chỉ người gửi), `AC-18.1.2` (nhiều tệp đính kèm). Mô tả nghiệp vụ `FEAT-18` sửa thành "một hoặc nhiều tệp đính kèm" để có căn cứ |
| 52 | `BR-31.3` (lọc dòng thời gian theo loại) chỉ có một AC duy nhất, thử lọc theo loại cuộc gọi — không kiểm chứng lọc theo công việc hay bỏ lọc, dù dòng thời gian hợp nhất gồm nhiều loại sự kiện khác nhau (`BR-31.1`) | Bổ sung `AC-31.3.2` (lọc theo công việc), `AC-31.3.3` (bỏ lọc, xem lại toàn bộ) |
| 53 | `UAT-14` mang tên "tính bất biến" nhưng chưa cập nhật để phủ `BR-15.6` — quy tắc thu hồi có kiểm soát mới thêm ở vòng 3, sửa đổi trực tiếp ý nghĩa "bất biến" bằng một ngoại lệ có kiểm soát | `UAT-14` đổi tên thành "...và thu hồi có kiểm soát", bổ sung bước 9-12: xác nhận người dùng thường không thấy chức năng thu hồi, Quản trị viên thu hồi thành công, và việc đánh thức không bị hoàn tác |

**Kết quả sau bốn vòng:** 113 quy tắc nghiệp vụ, **215 tiêu chí chấp nhận**, 16 kịch bản nghiệm thu, 17 tham số cấu hình. Vòng 4 không phát hiện mâu thuẫn nghiệp vụ mới trong các quy tắc đã viết lại ở vòng 3 (BR-25.3, BR-27.3/27.6/27.7, BR-15.6, BR-11.1/28.4, BR-05.4, BR-38.1/38.6/38.7/38.8, CFG-TASK-17 đều nhất quán khi đối chiếu chéo) — toàn bộ phát hiện của vòng 4 là lỗ hổng độ phủ AC còn sót, không phải mâu thuẫn business mới.

### Vòng review thứ năm — soát các phần chưa từng bị soi: Ma trận phân quyền, NFR, ranh giới Roadmap, Nhóm C/D

*Hai lượt review độc lập: một soi Ma trận phân quyền Mục 5 và Phụ lục A/B đối chiếu thân bài cùng các nhóm chức năng ít được chú ý (B, H, L); một soi Yêu cầu phi chức năng, ranh giới Roadmap v7.0 và cụm giao việc/bàn giao/định kỳ (Nhóm C, D).*

| # | Vấn đề phát hiện ở vòng 5 | Cách giải quyết |
| :---: | --- | --- |
| 54 | `BR-38` (Bàn giao) không có quy tắc thẩm định người **nhận** bàn giao tương đương `BR-09.1` (giao việc lần đầu) — có thể bàn giao hàng loạt công việc sang một tài khoản đã vô hiệu hóa, khiến toàn bộ lại rơi vào diện "Cần phân công lại" ngay sau khi bàn giao | Bổ sung `BR-38.9`: người nhận bàn giao phải thỏa cùng điều kiện thẩm định tại `BR-09.1`. Bổ sung `AC-38.9.1` |
| 55 | Hai quyền hẹp nằm trong quy tắc con — `BR-15.6` (thu hồi bản ghi, chỉ Quản trị viên+Chủ sở hữu) và `BR-02.4` (đổi ngữ cảnh việc đã kết thúc, chỉ Quản trị viên) — không có dòng riêng trong Ma trận phân quyền Mục 5, trong khi chính tài liệu đã có tiền lệ tách dòng cho trường hợp tương tự (`BR-30.6`) | Bổ sung hai dòng vào Ma trận Mục 5 cho `BR-02.4` và `BR-15.6`, theo đúng khuôn mẫu dòng `BR-30.6` đã có |
| 56 | Trần lô tối đa **1000** của `CFG-TASK-06` được công bố ở Phụ lục B nhưng không có AC nào kiểm chứng ở biên — toàn bộ AC của FEAT-27 chỉ thử mức mặc định 200 | Bổ sung `AC-27.1.3` (chọn tất cả khi trần đặt 1000, có 1500 việc) và `AC-27.1.4` (từ chối đặt 1001) |
| 57 | Không có cơ chế "tạm dừng" chủ động cho công việc mẫu định kỳ — chỉ có xóa vào Thùng rác (chịu rủi ro xóa vĩnh viễn theo `CFG-TASK-07`) hoặc tạm dừng tự động khi người phụ trách bị vô hiệu hóa (`BR-09.2`). Nhu cầu thật (nhân viên nghỉ phép, tạm ngưng dịch vụ định kỳ) nhưng chưa đủ cấp thiết để đặc tả ngay | `BR-12.7` nêu rõ Thùng rác là cơ chế tạm dừng chính thức của phiên bản này kèm rủi ro. Ghi nhận nhu cầu vào `FEAT-43` *[Roadmap v7.0]*, không mở luồng thao tác mới trong phạm vi phát hành |

**Kết quả sau năm vòng:** 114 quy tắc nghiệp vụ, **218 tiêu chí chấp nhận**, 16 kịch bản nghiệm thu, 17 tham số cấu hình, 43 tính năng (27 phát hành / 16 Roadmap v7.0). Vòng 5 không phát hiện mâu thuẫn business trong cụm quy tắc đã được năm vòng review liên tiếp bao phủ (vòng đời trạng thái, bàn giao, ghi nhận tương tác, thùng rác, đánh thức quan hệ) — toàn bộ phát hiện là lỗ hổng thẩm định/độ phủ ở các luồng phụ chưa từng bị soi trước đó (bàn giao, ma trận quyền, biên cấu hình, định kỳ).

### Vòng review thứ sáu — kiểm tra chính sửa đổi vòng 5, Phụ lục A, và nhất quán thuật ngữ toàn tài liệu

*Hai lượt review độc lập: một soi lại BR-38.9 và hai dòng ma trận mới thêm ở vòng 5, đối chiếu Phụ lục A với các khái niệm dữ liệu mới sinh ra; một săn lỗi thuộc loại chưa từng bị nhắm tới — nhất quán thuật ngữ xuyên tài liệu, toàn vẹn tham chiếu của 16 UAT, và rà lại chính các log Phụ lục C để tìm mẫu hình "tự đánh giá lạc quan hơn thực tế" đã từng xảy ra ở log #40 và #49.*

| # | Vấn đề phát hiện ở vòng 6 | Cách giải quyết |
| :---: | --- | --- |
| 58 | Phụ lục A chưa có mục ánh xạ cho hai khái niệm dữ liệu mới sinh ra ở vòng 3 và vòng 2: **trạng thái/lý do/người thu hồi** của bản ghi tương tác (`BR-15.6`) và **Nhật ký Bàn giao** (`BR-38.3`, một thực thể dữ liệu riêng chưa từng có bảng mô tả) | Bổ sung 2 dòng vào bảng "Bản ghi Tương tác"; thêm bảng mới "Nhật ký Bàn giao" vào Phụ lục A |
| 59 | Ghi chú cuối Mục 2.6 ("So với v5.2...") liệt kê các tính năng bổ sung qua từng vòng nhưng chưa nhắc `FEAT-43` (thêm ở vòng 5) | Bổ sung một câu về `FEAT-43` vào ghi chú, theo đúng văn phong đã dùng cho FEAT-41/42 |
| 60 | Từ "tenant" — thuật ngữ kỹ thuật — lọt vào thân `BR-03.1`, trong khi toàn tài liệu (110+ lần) và chính lời tuyên bố ở Ghi chú v6.0 đều dùng "Không gian làm việc" | Thay "tenant" bằng "Không gian làm việc" tại `BR-03.1` |
| 61 | Phụ lục A không có dòng ánh xạ cho cờ **"Cần phân công lại"** (`BR-09.2`) — một cờ trạng thái được dùng lại 7 lần xuyên Mục 3, cùng loại với "Cờ thiếu dữ liệu bắt buộc" vốn đã có dòng riêng | Bổ sung dòng "Cần phân công lại" vào bảng "Công việc" của Phụ lục A |
| 62 | Log #51 (vòng 4) tự nhận "bổ sung tiêu chí chấp nhận riêng cho **từng loại** tương tác" nhưng thực chỉ soi Cuộc họp/Email/Ghi chú — bỏ sót Cuộc gọi (`FEAT-15`) và Tin nhắn (`FEAT-19`) trong cùng nhóm. Cuộc gọi thiếu AC cho chiều **đến** và cho đường dẫn ghi âm dù bảng mô tả nghiệp vụ liệt kê rõ; Tin nhắn chỉ thử kênh Zalo, chưa xác nhận SMS/WhatsApp bằng tên kênh cụ thể | Bổ sung `AC-15.1.3` (chiều đến), `AC-15.1.4` (ghi âm), `AC-19.1.3` (SMS, chiều đến); làm rõ `AC-19.1.2` nêu đích danh ba kênh |

**Kết quả sau sáu vòng:** 114 quy tắc nghiệp vụ, **221 tiêu chí chấp nhận**, 16 kịch bản nghiệm thu, 17 tham số cấu hình, 43 tính năng (27 phát hành / 16 Roadmap v7.0). Vòng 6 không phát hiện mâu thuẫn business mới — toàn bộ phát hiện là khoảng trống tài liệu hóa (Phụ lục A) và độ phủ AC còn sót ở đúng loại lỗi mà vòng 4 tuyên bố đã giải quyết triệt để nhưng thực ra chỉ giải quyết một phần. Bốn tính năng thuộc phạm vi phát hành hoàn toàn chưa từng bị bất kỳ vòng nào trong sáu vòng nhắm tới trực tiếp — Danh sách lọc nâng cao (`FEAT-06`), Lịch biểu (`FEAT-07`), Nhật ký Tin nhắn (`FEAT-19`, nay đã được vá một phần ở log #62), Quét hạn chót & nhắc việc tự động (`FEAT-24`) — đây là ứng viên ưu tiên nếu tiếp tục vòng 7.

### Vòng review thứ bảy — soi ba tính năng chưa từng được nhắm tới, và tổng rà log Phụ lục C

*Hai lượt review độc lập: một soi kỹ FEAT-06 (Danh sách lọc nâng cao), FEAT-07 (Lịch biểu), FEAT-24 (Quét hạn chót & nhắc việc tự động) — ba tính năng vòng 6 xác định chưa từng bị soi; một rà lại toàn bộ 57 mục log trước đó, đối chiếu từng tuyên bố "đã giải quyết đầy đủ/cho mọi/cho cả hai" với nội dung thực tế hiện hành, để tìm thêm các trường hợp tự đánh giá lạc quan hơn thực tế.*

| # | Vấn đề phát hiện ở vòng 7 | Cách giải quyết |
| :---: | --- | --- |
| 63 | `BR-07.4` (dời hạn bằng kéo thả trên Lịch biểu) chỉ dẫn chiếu `BR-05.3`, không dẫn `BR-11.1` — không có AC nào xác nhận kéo thả cũng dời mốc nhắc việc theo cùng khoảng, dù về nguyên tắc `BR-11.1` đã bao trùm "mọi cách dời hạn chót" | `BR-07.4` bổ sung câu dẫn chiếu `BR-11.1`. Bổ sung `AC-07.4.3` kiểm chứng mốc nhắc dời theo khi kéo thả |
| 64 | `BR-06.5` (phạm vi dữ liệu, bộ lọc không mở rộng phạm vi) chỉ có AC thử trường hợp "xóa bộ lọc" — chưa thử ca dễ sai nhất: người dùng phạm vi Cá nhân chủ động đặt bộ lọc "người phụ trách" = người khác | Bổ sung `AC-06.5.2` |
| 65 | Log #45 (vòng 3) tuyên bố "bổ sung sàn/trần cho cả hai" tham số nhưng `CFG-TASK-05` (ngưỡng cảnh báo sắp đến hạn) trong Phụ lục B chỉ có trần (7 ngày), không có sàn — cùng mẫu hình tự đánh giá lạc quan đã lặp lại ở log #40, #49, #51 | Bổ sung sàn tối thiểu 1 giờ cho `CFG-TASK-05`, kèm lý do nghiệp vụ |

**Kết quả sau bảy vòng:** 114 quy tắc nghiệp vụ, **223 tiêu chí chấp nhận**, 16 kịch bản nghiệm thu, 17 tham số cấu hình, 43 tính năng (27 phát hành / 16 Roadmap v7.0). Vòng 7 không phát hiện mâu thuẫn business mới. Ba tính năng còn lại nghi ngờ chưa từng bị soi (FEAT-06, FEAT-07, FEAT-24) hóa ra đã được đặc tả chặt chẽ — phần lớn câu hỏi rà soát có câu trả lời tường minh sẵn trong tài liệu, chỉ 2 khoảng trống AC thật được tìm thấy. Việc rà toàn bộ log lịch sử chỉ còn phát hiện đúng một mẫu hình lạc quan sót lại (log #45), cho thấy tỷ lệ phát hiện mới trên mỗi vòng đang giảm dần — dấu hiệu hội tụ.

### Vòng review thứ tám — rà toàn bộ 114 quy tắc theo tiêu chí "cố định hay cấu hình theo tenant"

*Khởi phát từ việc chủ tài liệu chỉ ra `BR-01.2` bị cố định cứng một tập quán vận hành. Đây là lớp rà soát mà 7 vòng trước chưa từng làm có hệ thống — 7 vòng trước chỉ soi mâu thuẫn nội tại và độ phủ AC, không xét từng quy tắc theo đúng tiêu chí "cố định hay cấu hình" của `AGENTS.md` (mục "Quyết Định Nghiệp Vụ Nhiều Hướng Phải Là Tham Số Cấu Hình Theo Tenant"). Hai lượt review độc lập rà toàn bộ Mục 3, mỗi lượt phủ một nửa tài liệu.*

| # | Vấn đề phát hiện ở vòng 8 | Cách giải quyết |
| :---: | --- | --- |
| 66 | `BR-01.2` (người phụ trách mặc định khi bỏ trống) bị cố định cứng "luôn tự gán người tạo" — tập quán vận hành khác nhau giữa các doanh nghiệp (có nơi thường tạo hộ việc cho người khác và muốn bị chặn lưu nếu quên chọn), không phải ràng buộc toàn vẹn dữ liệu | Viết lại theo hai hướng cấu hình được (`CFG-TASK-18`): tự gán người tạo (mặc định, giữ nguyên hành vi cũ) hoặc bắt buộc chọn tường minh. Bổ sung `AC-01.2.2` |
| 67 | `BR-38.8` (quyền thực hiện bàn giao) cố định cứng "Quản lý cấp phòng ban trở lên" — chính sách phân cấp nội bộ, không phải nghĩa vụ pháp lý; đội nhỏ phẳng có thể muốn để Trưởng nhóm tự bàn giao, doanh nghiệp khẩu vị rủi ro cao muốn siết chỉ còn Quản trị viên/Chủ sở hữu | Thêm `CFG-TASK-19` với 3 mức, mặc định giữ nguyên hành vi cũ. Bổ sung `AC-38.8.4`. Cập nhật dòng ma trận Mục 5 kèm chú thích mức mặc định |
| 68 | `BR-15.6` (quyền thu hồi bản ghi tương tác) cố định cứng "chỉ Quản trị viên+Chủ sở hữu" — cơ chế thu hồi tự nó đã bảo vệ toàn vẹn (không xóa, có dấu vết, bắt buộc lý do) bất kể ai thực hiện, nên giới hạn người thực hiện chỉ là chính sách phân cấp | Thêm `CFG-TASK-20` với 3 mức, mặc định giữ nguyên hành vi cũ. Bổ sung `AC-15.6.5`. Cập nhật dòng ma trận Mục 5 |
| 69 | `BR-30.6` (quyền xóa vĩnh viễn thủ công) cố định cứng mức "Phòng ban" trong ma trận — khẩu vị rủi ro dữ liệu điển hình đối với thao tác không-hoàn-tác-được, không phải nghĩa vụ pháp lý | Thêm `CFG-TASK-21` với 3 mức, mặc định giữ nguyên hành vi cũ. Bổ sung `AC-30.6.2`. Cập nhật dòng ma trận Mục 5 |
| 70 | `BR-09.3` (thông báo khi được giao việc) là cơ chế tự động không có cách tắt — khẩu vị về nhiễu thông báo, không phải toàn vẹn dữ liệu; doanh nghiệp có quy trình tự động sinh nhiều việc/ngày (FEAT-13) muốn giảm nhiễu | Thêm `CFG-TASK-22` bật/tắt, mặc định bật (giữ nguyên hành vi cũ). Bổ sung `AC-09.3.2` |

**Không phát hiện thêm** ở các quy tắc còn lại của Mục 3 — phần lớn BR hoặc đã có CFG đúng chuẩn, hoặc thuộc ba loại được Phụ lục B loại trừ tường minh (cách ly dữ liệu/phân quyền nền tảng, toàn vẹn dữ liệu lịch sử, lịch vận hành hạ tầng).

**Kết quả sau tám vòng:** 114 quy tắc nghiệp vụ, **228 tiêu chí chấp nhận**, 16 kịch bản nghiệm thu, **22 tham số cấu hình**, 43 tính năng (27 phát hành / 16 Roadmap v7.0). Toàn bộ 5 phát hiện của vòng 8 đều là hành vi cố định cứng che giấu một quyết định nghiệp vụ đáng lẽ phải theo tenant — không phải mâu thuẫn logic hay lỗ hổng AC như các vòng trước. Mọi tham số mới đều đặt mặc định trùng khớp hành vi cũ, nên không thay đổi hành vi hệ thống cho tenant chưa cấu hình gì.

---

**— Hết —**
