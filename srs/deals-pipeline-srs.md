# SRS — Phân hệ Quản lý Cơ hội & Phễu Bán hàng (Deals & Pipelines Management)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA (Version 6.0) |
| **Module** | CRM — Phân hệ Quản lý Cơ hội & Phễu Bán hàng (Deals & Pipelines Management) |
| **Ngày cập nhật** | 2026-09-23 |
| **Phiên bản** | v6.0 (Chuẩn hóa Nghiệp vụ Thuần túy — Thay thế toàn bộ v5.1, đối chiếu lại với mã nguồn thực tế) |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`contacts-srs.md`](./contacts-srs.md), [`tickets-srs.md`](./tickets-srs.md), [`tasks-srs.md`](./tasks-srs.md), [`campaigns-srs.md`](./campaigns-srs.md), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`object-manager-srs.md`](./object-manager-srs.md) |

## Ghi chú về phiên bản v6.0

Phiên bản này **viết lại toàn bộ** tài liệu theo đúng vai trò của một SRS nghiệp vụ, thay thế v5.1.

**Nguyên tắc biên soạn — tài liệu là chuẩn, không phải bản ghi chép hiện trạng:**

Tài liệu này đặc tả **trạng thái nghiệp vụ mục tiêu (To-Be)** — điều doanh nghiệp cần hệ thống làm đúng, không phải điều hệ thống đang làm. Mọi quy tắc trong Mục 3 đều là **yêu cầu bắt buộc**, bất kể phần triển khai hiện tại đã đáp ứng hay chưa. Nơi nào mã nguồn làm khác tài liệu, **mã nguồn phải được sửa theo tài liệu** — kể cả khi điều đó có nghĩa là làm lại một phần đáng kể.

Hệ quả trực tiếp:

- Tài liệu **không hạ chuẩn nghiệp vụ** để khớp với giới hạn kỹ thuật hiện có. Một quy tắc đúng về nghiệp vụ thì được viết đúng, dù phần triển khai còn xa.
- Tài liệu **không mô tả hiện trạng mã nguồn** trong thân đặc tả (tên tệp, số dòng, "hiện chưa làm được X"). Việc đo khoảng cách giữa đặc tả và triển khai thuộc backlog kỹ thuật riêng.
- **Nhãn / không được dùng trong tài liệu này.** Hai nhãn đó mô tả trạng thái công việc, không phải nội dung nghiệp vụ, và chúng khiến người đọc hiểu nhầm rằng một quy tắc "chưa triển khai" thì chưa bắt buộc. Thay vào đó, những nhu cầu **chưa đủ chín muồi để chốt phương án nghiệp vụ** được gom riêng tại Mục 7 — đó là khác biệt duy nhất có ý nghĩa: *đã chốt được điều đúng là gì* hay *chưa chốt được*.

**Ba thay đổi về bản chất so với v5.1:**

1. **Loại bỏ hoàn toàn ngôn ngữ kỹ thuật khỏi phần thân.** v5.1 đặc tả quy tắc bằng tên trường dữ liệu, tên thành phần phần mềm, công thức toán học và tên hạ tầng. Toàn bộ quy tắc nay được phát biểu bằng ngôn ngữ người dùng nghiệp vụ.

2. **Mỗi quy tắc đều kèm lý do nghiệp vụ và Tiêu chí Chấp nhận kiểm chứng được.** Quy tắc không có lý do là quy tắc không ai dám sửa và không ai biết khi nào được miễn trừ.

3. **Giải quyết dứt điểm các mâu thuẫn nội tại và các quy tắc sai nghiệp vụ của v5.1**, đặc biệt: xác suất giai đoạn Thắng (phải là 100%, không cấu hình được), xử lý lịch chăm sóc quá hạn lâu (phải leo thang, không phải ngừng nhắc), và đường vòng đổi kết quả Thắng/Thua qua thao tác hàng loạt.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả toàn bộ nghiệp vụ quản trị Cơ hội Bán hàng và Phễu Bán hàng trong hệ thống CRM B2B SaaS:

1. **Quản trị Vòng đời Cơ hội Bán hàng:** Theo dõi giao dịch từ lúc tiếp nhận đến khi chốt hợp đồng thành công hoặc thất bại.
2. **Quản trị Nhiều Phễu Bán hàng Độc lập:** Vận hành song song nhiều quy trình bán hàng cho các dòng sản phẩm, phân khúc khách hàng hoặc kênh phân phối khác nhau.
3. **Bảng Kanban Trực quan:** Không gian làm việc kéo thả, hiển thị tổng số lượng và tổng giá trị theo thời gian thực trên từng giai đoạn.
4. **Kiểm soát Chất lượng Dữ liệu qua Rào cản Giai đoạn:** Bắt buộc điền đủ trường dữ liệu quan trọng trước khi một cơ hội được coi là đủ điều kiện ở một giai đoạn, chống báo cáo phễu ảo.
5. **Đo lường Vận tốc Bán hàng:** Đo số ngày cơ hội lưu lại tại từng giai đoạn, xác định điểm nghẽn và tỷ lệ rơi rụng của phễu.
6. **Dự báo Doanh thu có Trọng số:** Tính doanh thu kỳ vọng theo giá trị nhân xác suất thắng của giai đoạn hiện tại.
7. **Nhắc nhở Chăm sóc Tiếp theo:** Chủ động nhắc người phụ trách đúng thời điểm đã hẹn với khách hàng.
8. **Phân tích Thắng/Thua:** Bắt buộc khai báo lý do khi đóng cơ hội thất bại, phục vụ cải tiến bán hàng.
9. **Toàn vẹn Dữ liệu khi Thay đổi Cấu trúc:** Bảo vệ dữ liệu khi phễu bị đóng, giai đoạn bị xóa, nhân sự phụ trách rời tổ chức hoặc nhiều quản trị viên cùng sửa cấu hình.
10. **Liên kết với các Phân hệ khác:** Kết nối với Khách hàng, Vé hỗ trợ và Công việc để người bán hàng luôn có đủ bối cảnh.

### 1.2 Phạm vi

Tài liệu bao gồm 11 nhóm chức năng:

- **Nhóm A — Quản trị Cơ hội Bán hàng:** Tạo mới, cập nhật, đa tiền tệ, bảo vệ dữ liệu tài chính nhạy cảm, thùng rác và phục hồi.
- **Nhóm B — Quản trị Phễu Bán hàng & Giai đoạn:** Nhiều phễu độc lập, cấu hình giai đoạn & xác suất thắng, sắp xếp thứ tự, đóng phễu an toàn, xóa giai đoạn an toàn.
- **Nhóm C — Bảng Kanban Cơ hội:** Tổng hợp giá trị thời gian thực, phân trang, kéo thả chuyển giai đoạn, bộ lọc.
- **Nhóm D — Lịch sử Giai đoạn & Vận tốc Bán hàng:** Ghi nhận thời gian lưu tại từng giai đoạn, phân tích điểm nghẽn và tỷ lệ rơi rụng.
- **Nhóm E — Nhắc nhở Chăm sóc & Cảnh báo Cơ hội Nguội:** Lịch hẹn chăm sóc tiếp theo, quét nhắc việc tự động, nhận diện cơ hội bị bỏ quên.
- **Nhóm F — Thắng/Thua:** Đóng cơ hội thành công, bắt buộc lý do khi thất bại.
- **Nhóm G — Vai trò Liên hệ & Nguồn gốc Tiếp thị:** Gắn vai trò người mua vào cơ hội, theo dõi nguồn gốc chiến dịch.
- **Nhóm H — Dự báo Doanh thu:** Doanh thu có trọng số theo xác suất giai đoạn, báo cáo hiệu suất người phụ trách.
- **Nhóm I — Thao tác Hàng loạt & Nhập/Xuất Dữ liệu:** Cập nhật/gắn thẻ/xóa hàng loạt, nhập-xuất dữ liệu dung lượng lớn qua hàng đợi.
- **Nhóm J — Dòng thời gian & Toàn vẹn Cấu trúc:** Dòng thời gian hợp nhất, đóng băng ghi dữ liệu khi di chuyển dữ liệu nền, khóa đồng thời khi sửa cấu hình.
- **Nhóm K — Cộng tác, Bàn giao & Vòng đời Mở rộng:** Người theo dõi cơ hội, chuyển cơ hội sang phễu khác, bàn giao khi thay đổi nhân sự, tái mở cơ hội đã thua.

**Ngoài phạm vi (thuộc về các tài liệu SRS chuyên biệt khác):**

- Quản trị hồ sơ Khách hàng & Danh bạ — thuộc [`contacts-srs.md`](./contacts-srs.md).
- Quản trị Vé Hỗ trợ & cam kết chất lượng dịch vụ — thuộc [`tickets-srs.md`](./tickets-srs.md).
- Quản lý Công việc & Lịch hoạt động — thuộc [`tasks-srs.md`](./tasks-srs.md).
- Phát sóng Chiến dịch Tiếp thị — thuộc [`campaigns-srs.md`](./campaigns-srs.md).
- Cấu hình danh mục trạng thái, trường dữ liệu tùy biến và chính sách phân quyền trường — thuộc [`object-manager-srs.md`](./object-manager-srs.md). Phân hệ này chỉ đặc tả **cách Cơ hội bán hàng sử dụng** các cấu hình đó.
- Phân giải phạm vi dữ liệu theo vai trò và đơn vị tổ chức — thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md).

**Chưa có trong phạm vi phát hành nào (nhu cầu nghiệp vụ thật nhưng chưa được thiết kế chi tiết):** Bảng giá & Chi tiết Dòng sản phẩm (CPQ), Quy trình Phê duyệt Chiết khấu, Trạng thái Tạm ngưng Cơ hội, Phân chia Doanh số giữa nhiều người phụ trách, Nhóm Dự báo theo mức độ tin cậy, Hạn ngạch Doanh số. Các nhu cầu này được ghi nhận tại Mục 7, không đặc tả chi tiết trong tài liệu này vì chưa có quyết định thiết kế đủ chín muồi để viết Tiêu chí Chấp nhận.

### 1.3 Đối tượng đọc

- **Product Owner / Business Analyst:** Căn cứ chốt phạm vi phát hành và thẩm định quy trình nghiệp vụ.
- **Đội ngũ Phát triển:** Căn cứ duy nhất để xây dựng và sửa chữa chức năng. Khi mã nguồn khác tài liệu, mã nguồn phải sửa theo tài liệu.
- **Đội ngũ Đảm bảo Chất lượng:** Căn cứ thiết kế kịch bản kiểm thử. Mỗi Tiêu chí Chấp nhận (AC) là một ca kiểm thử.
- **Giám đốc Kinh doanh & Quản lý Kinh doanh:** Căn cứ vận hành phễu bán hàng và đo lường hiệu suất đội ngũ.

### 1.4 Thuật ngữ nghiệp vụ

| Thuật ngữ | Định nghĩa nghiệp vụ |
| --- | --- |
| **Cơ hội Bán hàng** | Thực thể đại diện cho một giao dịch kinh doanh tiềm năng giữa doanh nghiệp và khách hàng, có giá trị tiền tệ dự kiến, ngày dự kiến đóng và gắn liền với một giai đoạn cụ thể trên phễu bán hàng. |
| **Phễu Bán hàng** | Quy trình gồm các giai đoạn tuần tự từ khi tiếp nhận cơ hội đến khi hoàn tất hợp đồng. Một không gian làm việc vận hành được nhiều phễu độc lập. |
| **Giai đoạn Phễu** | Một bước xác định trong phễu bán hàng, có xác suất thắng mặc định và có thể có điều kiện dữ liệu bắt buộc trước khi được coi là đủ điều kiện. |
| **Xác suất Thắng** | Tỷ lệ phần trăm khả năng chốt thành công được gán cho từng giai đoạn (0%–100%), dùng làm căn cứ tính doanh thu dự báo. |
| **Bảng Kanban Cơ hội** | Giao diện dạng cột thẻ kéo thả, mỗi cột là một giai đoạn, hiển thị số lượng và tổng giá trị cơ hội theo thời gian thực. |
| **Rào cản Giai đoạn** | Bộ điều kiện dữ liệu bắt buộc phải thỏa mãn trước khi một cơ hội được coi là đủ điều kiện ở một giai đoạn. |
| **Người phụ trách** | Nhân viên chịu trách nhiệm xử lý và chốt một cơ hội bán hàng. Một cơ hội có tối đa một người phụ trách. |
| **Thời gian Lưu tại Giai đoạn** | Khoảng thời gian một cơ hội đã nằm tại một giai đoạn cụ thể trước khi chuyển sang giai đoạn khác, dùng để phát hiện điểm nghẽn quy trình. |
| **Doanh thu Dự báo có Trọng số** | Tổng giá trị các cơ hội đang mở, mỗi cơ hội được nhân với xác suất thắng của giai đoạn hiện tại, theo từng giai đoạn hoặc toàn phễu. |
| **Lịch Chăm sóc Tiếp theo** | Thời điểm người phụ trách cam kết liên hệ lại khách hàng, được hệ thống chủ động nhắc khi đến hạn. |
| **Lý do Thất bại** | Nội dung giải thích bắt buộc phải khai báo khi một cơ hội chuyển sang trạng thái thất bại. |
| **Vai trò Liên hệ trong Cơ hội** | Vai trò của một nhân sự phía khách hàng tham gia vào một cơ hội (ví dụ Người ra quyết định, Người bảo trợ nội bộ), do từng Không gian làm việc tự định nghĩa danh mục. |
| **Đóng băng Ghi dữ liệu khi Di chuyển Dữ liệu nền** | Trạng thái tạm thời chặn mọi thay đổi trên Cơ hội bán hàng trong lúc một tiến trình di chuyển dữ liệu quy mô lớn đang chạy, để tránh ghi đè xung đột. |
| **Không gian làm việc** | Phạm vi dữ liệu của một doanh nghiệp khách hàng. Dữ liệu giữa các không gian làm việc tuyệt đối không nhìn thấy nhau. |

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà phân hệ giải quyết

1. **Thất lạc cơ hội & thiếu lịch chăm sóc tiếp theo.** Nhân viên quên liên hệ lại khách hàng sau khi gửi báo giá, không có lịch hẹn rõ ràng, dẫn đến khách hàng rơi vào tay đối thủ.
2. **Báo cáo phễu ảo.** Nhân viên kéo cơ hội sang giai đoạn cuối dù chưa hoàn thành công việc thực chất của giai đoạn trước, khiến ban giám đốc đánh giá sai khả năng chốt hợp đồng thật.
3. **Điểm nghẽn quy trình bán hàng không được đo lường.** Không phát hiện được cơ hội bị tắc ở bước nào và mất trung bình bao lâu để chốt một hợp đồng.
4. **Đứt gãy dữ liệu khi thay đổi cấu trúc phễu.** Đóng một phễu cũ hoặc xóa một giai đoạn có thể làm mất dấu các cơ hội đang mở nếu không có quy trình di chuyển an toàn.
5. **Mất dấu nguyên nhân thất bại.** Khi mất hợp đồng, không thu thập được lý do cụ thể để cải tiến chiến lược bán hàng.
6. **Xung đột dữ liệu khi vận hành song song.** Nhiều quản trị viên cùng sửa cấu hình phễu, hoặc tiến trình xử lý dữ liệu nền chạy đồng thời với người dùng đang thao tác, có thể ghi đè lẫn nhau nếu không có cơ chế bảo vệ.
7. **Tạo trùng cơ hội khi tiếp nhận khách hàng tiềm năng.** Một khách hàng có thể bị tạo nhiều cơ hội trùng lặp trên cùng phễu nếu quy trình chuyển đổi không kiểm tra cơ hội đang mở sẵn có.

### 2.2 Vai trò người dùng

| Vai trò | Quyền hạn và trách nhiệm nghiệp vụ |
| --- | --- |
| **Nhân viên Kinh doanh** | Tạo mới cơ hội, cập nhật thông tin, kéo thả chuyển giai đoạn, ghi nhận hoạt động, đặt lịch chăm sóc tiếp theo, đóng cơ hội thắng hoặc thua. |
| **Quản lý Kinh doanh** | Toàn bộ quyền của Nhân viên Kinh doanh trên phạm vi phòng ban, cộng thao tác hàng loạt, xem báo cáo vận tốc/dự báo của phòng ban, và cấu hình ngưỡng nguội lạnh (`CFG-DEAL-04`, khi `FEAT-17` được triển khai) — ngoại lệ duy nhất cho phép vai trò này chỉnh một tham số cấu hình, vì đây là quyết định vận hành bán hàng thường nhật, không phải cấu hình cấu trúc hệ thống. |
| **Giám đốc Kinh doanh** | Xem và điều phối cơ hội trên toàn không gian làm việc, xem báo cáo dự báo doanh thu hợp nhất. |
| **Quản trị viên Không gian làm việc** | Cấu hình phễu, giai đoạn, xác suất thắng, rào cản giai đoạn, danh mục vai trò liên hệ, thực hiện đóng/di chuyển phễu và xóa giai đoạn. |
| **Chủ sở hữu Không gian làm việc** | Toàn quyền kiểm soát mọi dữ liệu và cấu hình trong không gian làm việc. |
| **Tiến trình Hệ thống** | Quét và gửi nhắc nhở lịch chăm sóc, ghi nhận lịch sử giai đoạn, dọn dẹp thùng rác quá hạn lưu trữ, đồng bộ tên doanh nghiệp khi thông tin doanh nghiệp thay đổi. |

> **Ghi chú:** Sáu vai trò trên tạo thành các cột của Ma trận phân quyền tại Mục 5. Đây là **vai trò nghiệp vụ** dùng để diễn đạt yêu cầu, không phải danh sách vai trò hệ thống: "Quản lý Kinh doanh" và "Giám đốc Kinh doanh" là hai mức quản lý bán hàng mà mỗi doanh nghiệp tự dựng từ vai trò và phạm vi dữ liệu của nền tảng; "Quản trị viên" và "Chủ sở hữu" là **cấp bậc thành viên** chứ không phải vai trò. Mô hình vai trò, cấp bậc thành viên và cách phân giải quyền thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md).

### 2.3 Quy ước thời gian nghiệp vụ

Toàn bộ mốc thời gian nghiệp vụ trong tài liệu này — *cơ hội nguội lạnh*, *quá hạn lịch chăm sóc*, *giờ chạy tiến trình nền* — được xác định theo **múi giờ cấu hình của Không gian làm việc**, không theo múi giờ máy chủ và không theo múi giờ của từng người dùng, nhất quán với quy ước đã áp dụng ở [`tasks-srs.md`](./tasks-srs.md#23-quy-ước-thời-gian-nghiệp-vụ).

**Lý do nghiệp vụ:** Một lịch chăm sóc "9 giờ sáng mai" phải quá hạn vào cùng một thời điểm đối với mọi thành viên trong cùng đội bán hàng. Nếu tính theo múi giờ cá nhân, báo cáo của Quản lý Kinh doanh sẽ không đối chiếu được giữa các nhân viên.

### 2.4 Nguyên tắc nghiệp vụ nền tảng

**Nguyên tắc 1 — Trạng thái đóng của cơ hội có hai nhánh loại trừ lẫn nhau.**
Một cơ hội đang mở kết thúc theo đúng một trong hai nhánh: **Thắng** (chốt thành công) hoặc **Thua** (không chốt được). Không có trạng thái đóng thứ ba lẫn lộn giữa hai nhánh này. Việc mở lại một cơ hội đã đóng để tái phân loại (từ Thắng sang Thua hoặc ngược lại) là một nghiệp vụ tách biệt, có quyền hạn riêng, không phải thao tác sửa đổi hằng ngày (`BR-01.6`).

**Nguyên tắc 2 — Rào cản giai đoạn bảo vệ chất lượng dữ liệu, không bảo vệ quy trình phê duyệt.**
Rào cản giai đoạn (Mục 3, `FEAT-13`) chỉ đảm bảo các trường dữ liệu quan trọng đã được điền trước khi cơ hội được coi là đủ điều kiện ở một giai đoạn. Đây không phải là cơ chế phê duyệt có người ký duyệt — nhu cầu đó (ví dụ phê duyệt chiết khấu) là một nghiệp vụ khác, chưa được thiết kế (xem Mục 7).

**Nguyên tắc 3 — Không quá một người chịu trách nhiệm.**
Một cơ hội bán hàng không bao giờ có nhiều hơn một **người phụ trách** cùng lúc — trách nhiệm chốt số không được phân tán. Điều này **không ngăn** nhiều người cùng tham gia một cơ hội: chia sẻ doanh số giữa nhiều người có công (`FEAT-24`) và cho người ngoài phạm vi dữ liệu xem một cơ hội cụ thể (`FEAT-35`) là hai trục khác, không đụng tới nguyên tắc một người chịu trách nhiệm.

**Nguyên tắc 4 — Đơn vị tổ chức của cơ hội đi theo người phụ trách, không theo người tạo.**
Một cơ hội phải hiện diện trong phạm vi quản lý của người thực sự chịu trách nhiệm xử lý nó. Khi người phụ trách thay đổi, đơn vị tổ chức của cơ hội chuyển theo (`BR-01.2`).

**Nguyên tắc 5 — Thay đổi cấu trúc phễu không được làm mất dấu dữ liệu đang mở.**
Đóng một phễu hoặc xóa một giai đoạn không bao giờ khiến các cơ hội đang mở biến mất khỏi tầm nhìn quản lý; mọi thay đổi cấu trúc phễu đều bắt buộc chỉ định điểm đến cho dữ liệu hiện có (`FEAT-08`, `FEAT-09`).

### 2.5 Bảng tổng hợp tính năng nghiệp vụ

| Nhóm | Mã | Tên tính năng nghiệp vụ |
| --- | --- | --- |
| **A. Quản trị Cơ hội Bán hàng** | `FEAT-01` | Tạo mới & Quản lý Cơ hội Bán hàng |
| | `FEAT-02` | Quản lý Đa Tiền tệ |
| | `FEAT-03` | Bảo vệ Dữ liệu Tài chính Nhạy cảm |
| | `FEAT-04` | Thùng rác & Phục hồi Cơ hội |
| **B. Phễu Bán hàng & Giai đoạn** | `FEAT-05` | Quản trị Nhiều Phễu Bán hàng Độc lập |
| | `FEAT-06` | Thiết lập Giai đoạn, Xác suất Thắng & Thứ tự |
| | `FEAT-07` | Đóng Phễu An toàn & Di chuyển Cơ hội |
| | `FEAT-08` | Xóa Giai đoạn An toàn |
| **C. Bảng Kanban Cơ hội** | `FEAT-09` | Bảng Kanban Tổng hợp Giá trị Thời gian thực |
| | `FEAT-10` | Kéo thả Chuyển Giai đoạn |
| | `FEAT-11` | Bộ lọc trên Bảng Kanban |
| **D. Lịch sử Giai đoạn & Vận tốc** | `FEAT-12` | Ghi nhận Lịch sử Chuyển Giai đoạn |
| | `FEAT-13` | Rào cản Điều kiện Chuyển Giai đoạn |
| | `FEAT-14` | Phân tích Vận tốc Bán hàng & Điểm nghẽn |
| **E. Nhắc nhở & Cảnh báo Nguội** | `FEAT-15` | Thiết lập Lịch Chăm sóc Tiếp theo |
| | `FEAT-16` | Quét & Nhắc nhở Lịch Chăm sóc Đến hạn |
| | `FEAT-17` | Nhận diện Cơ hội Nguội Lạnh |
| **F. Thắng/Thua** | `FEAT-18` | Đóng Cơ hội Thành công |
| | `FEAT-19` | Đóng Cơ hội Thất bại & Bắt buộc Lý do |
| | `FEAT-20` | Danh mục Lý do Thất bại Chuẩn hóa |
| | `FEAT-21` | Tái phân loại Cơ hội Đã đóng |
| **G. Vai trò Liên hệ & Nguồn gốc** | `FEAT-22` | Gắn Vai trò Liên hệ vào Cơ hội |
| | `FEAT-23` | Theo dõi Nguồn gốc Tiếp thị |
| | `FEAT-24` | Phân chia Doanh số Đồng phụ trách |
| **H. Dự báo Doanh thu** | `FEAT-25` | Dự báo Doanh thu có Trọng số |
| | `FEAT-26` | Báo cáo Hiệu suất Người phụ trách & Nguồn |
| | `FEAT-27` | Hạn ngạch Doanh số |
| **I. Thao tác Hàng loạt & Nhập/Xuất** | `FEAT-28` | Cập nhật, Gắn thẻ & Xóa Hàng loạt |
| | `FEAT-29` | Nhập / Xuất Dữ liệu Dung lượng lớn qua Hàng đợi |
| **J. Dòng thời gian & Toàn vẹn Cấu trúc** | `FEAT-30` | Dòng thời gian Hoạt động Hợp nhất |
| | `FEAT-31` | Cảnh báo Vé Hỗ trợ Khẩn cấp trên Kanban |
| | `FEAT-32` | Đóng băng Ghi dữ liệu khi Di chuyển Dữ liệu nền |
| | `FEAT-33` | Chống Tạo Cơ hội Trùng khi Chuyển đổi |
| | `FEAT-34` | Khóa Đồng thời khi Sửa Cấu hình Phễu |
| **K. Cộng tác, Bàn giao & Vòng đời Mở rộng** | `FEAT-35` | Người Theo dõi Cơ hội |
| | `FEAT-36` | Chuyển Cơ hội sang Phễu khác |
| | `FEAT-37` | Bàn giao Cơ hội khi Thay đổi Nhân sự |
| | `FEAT-38` | Tái mở Cơ hội đã Thua |

**Tổng kết phạm vi:** 38 tính năng nghiệp vụ, **tất cả đều là yêu cầu bắt buộc** của đặc tả này. Các nhu cầu chưa chốt được phương án nghiệp vụ được gom riêng tại Mục 7 và không mang mã `FEAT`.

---

## 3. Đặc tả yêu cầu chức năng

### Nhóm A — Quản trị Cơ hội Bán hàng

#### FEAT-01 — Tạo mới & Quản lý Cơ hội Bán hàng

**Mô tả nghiệp vụ:** Nhân viên kinh doanh tạo, xem, sửa cơ hội bán hàng, liên kết với khách hàng cá nhân và/hoặc doanh nghiệp.

**Quy tắc nghiệp vụ:**

- **`BR-01.1` (Thông tin tối thiểu):** Bắt buộc có Tên cơ hội, Phễu bán hàng và Giai đoạn khởi đầu.

- **`BR-01.2` (Người phụ trách & đơn vị tổ chức):** Người tạo mặc định được gán làm người phụ trách, trừ khi người tạo có quyền chỉ định người khác — việc chỉ định người phụ trách khác với chính mình là một quyền hạn tách biệt khỏi quyền tạo/sửa cơ hội thông thường. Đơn vị tổ chức của cơ hội luôn theo **người phụ trách hiện tại**, không theo người tạo.

  **Lý do nghiệp vụ:** Nếu đơn vị tổ chức lấy theo người tạo, khi Quản lý phòng A giao việc cho nhân viên phòng B, cơ hội sẽ nằm trong phạm vi phòng A — Trưởng phòng B không nhìn thấy cơ hội mà nhân viên mình đang xử lý. Tách quyền "chỉ định người phụ trách khác mình" khỏi quyền sửa thông thường để một nhân viên bình thường không thể tự đẩy trách nhiệm cơ hội của mình sang người khác mà không có sự chấp thuận của cấp quản lý.

- **`BR-01.3` (Liên kết Khách hàng & Doanh nghiệp):** Một cơ hội liên kết được với một Doanh nghiệp và/hoặc một hoặc nhiều Khách hàng cá nhân. Khi gắn Doanh nghiệp, tên doanh nghiệp hiển thị trên cơ hội được đồng bộ tự động và **tiếp tục đồng bộ mỗi khi tên doanh nghiệp gốc thay đổi sau này**, không chỉ tại thời điểm gắn.

  **Lý do nghiệp vụ:** Nếu chỉ đồng bộ một lần tại thời điểm gắn, đổi tên doanh nghiệp (sáp nhập, đổi thương hiệu) sẽ để lại tên cũ trên mọi cơ hội lịch sử, gây nhầm lẫn khi tra cứu báo cáo theo tên doanh nghiệp.

- **`BR-01.4` (Chống tạo cơ hội trùng lặp):** Hệ thống từ chối tạo một cơ hội mới có cùng tên và cùng Doanh nghiệp với một cơ hội **đang mở** đã tồn tại, trừ khi người dùng chủ động xác nhận vẫn muốn tạo cơ hội riêng biệt.

  **Lý do nghiệp vụ:** Chống trùng lặp dữ liệu khi nhiều nhân viên cùng thao tác trên một khách hàng, hoặc khi quy trình chuyển đổi khách hàng tiềm năng chạy nhiều lần do thao tác nhầm. Cơ hội trùng không bị chặn tuyệt đối vì có tình huống hợp lệ cần nhiều cơ hội song song với cùng một doanh nghiệp (ví dụ hai dòng sản phẩm khác nhau) — xem thêm `FEAT-33` cho nghiệp vụ chống trùng khi chuyển đổi từ khách hàng tiềm năng.

- **`BR-01.5` (Phân quyền truy cập):** Cơ hội bán hàng chịu mô hình phạm vi dữ liệu chung của nền tảng, gồm bốn mức từ hẹp tới rộng: **Chỉ của mình** / **Của mình + cấp dưới và đơn vị của mình** / **Cả nhánh đơn vị** / **Toàn Không gian làm việc**. Người dùng không xem hoặc sửa được cơ hội ngoài phạm vi của mình. Định nghĩa đầy đủ từng mức, cách phân giải khi một người giữ nhiều vai trò, và quyền cấu hình phạm vi thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) — phân hệ này chỉ viện dẫn, không định nghĩa lại.

  Quyền xem một cơ hội **cụ thể** ngoài phạm vi vẫn có thể được cấp riêng theo từng bản ghi qua Người theo dõi Cơ hội (`FEAT-35`) — đây là một trục cộng thêm, không phải ngoại lệ của phạm vi.

- **`BR-01.6` (Hai nhánh đóng loại trừ lẫn nhau):** Xem Nguyên tắc 1 (Mục 2.4). Chi tiết luồng đóng cơ hội tại `FEAT-18`/`FEAT-19`; tái phân loại cơ hội đã đóng tại `FEAT-21`.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-01.1.1` | Màn hình tạo cơ hội | Nhập tên, chọn phễu và giai đoạn khởi đầu, lưu | Tạo thành công |
| `AC-01.1.2` | Màn hình tạo cơ hội | Bỏ trống tên cơ hội, lưu | Từ chối, báo rõ trường bắt buộc |
| `AC-01.2.1` | Nhân viên A không có quyền chỉ định người phụ trách khác | Tạo cơ hội, không chọn người phụ trách | Cơ hội tự động gán người phụ trách là A |
| `AC-01.2.2` | Quản lý phòng A giao cơ hội cho nhân viên B thuộc phòng B | Lưu cơ hội với người phụ trách là B | Cơ hội thuộc phòng B; Trưởng phòng B nhìn thấy cơ hội trong danh sách phòng mình |
| `AC-01.2.3` | Cơ hội đang thuộc phòng B | Đổi người phụ trách sang nhân viên phòng C | Cơ hội chuyển sang thuộc phòng C ngay lập tức |
| `AC-01.3.1` | Cơ hội đang gắn Doanh nghiệp X, tên hiển thị là "Công ty X" | Doanh nghiệp X đổi tên thành "Công ty X Mới" | Tên hiển thị trên cơ hội tự động cập nhật thành "Công ty X Mới" |
| `AC-01.4.1` | Cơ hội "Hợp đồng Q3" đang mở, gắn Doanh nghiệp Y | Tạo cơ hội mới cùng tên "Hợp đồng Q3" cùng gắn Doanh nghiệp Y | Từ chối, gợi ý cơ hội trùng đã tồn tại, cho phép xác nhận vẫn tạo riêng |
| `AC-01.4.2` | Tiếp nối AC-01.4.1 | Người dùng xác nhận vẫn muốn tạo cơ hội riêng | Tạo thành công, cả hai cơ hội cùng tồn tại độc lập |
| `AC-01.5.1` | Nhân viên A có phạm vi dữ liệu Cá nhân, đang phụ trách 3 cơ hội; phòng ban có tổng 20 cơ hội | A mở danh sách cơ hội | Chỉ thấy đúng 3 cơ hội mình phụ trách |
| `AC-01.5.2` | Tiếp nối AC-01.5.1 | A mở trực tiếp đường dẫn tới một cơ hội của đồng nghiệp cùng phòng | Từ chối truy cập, không tiết lộ sự tồn tại của cơ hội đó |
| `AC-01.5.3` | Trưởng phòng B có phạm vi dữ liệu Phòng ban | Mở danh sách cơ hội | Thấy toàn bộ cơ hội của mọi nhân viên trong phòng B, không thấy cơ hội phòng khác |
| `AC-01.5.4` | Nhân viên A phạm vi Cá nhân | A cố gắng sửa một cơ hội ngoài phạm vi qua thao tác hàng loạt (`FEAT-28`) | Cơ hội đó vào danh sách bị bỏ qua, không bị sửa |

---

#### FEAT-02 — Quản lý Đa Tiền tệ

**Mô tả nghiệp vụ:** Cho phép mỗi cơ hội bán hàng ghi nhận giá trị bằng một loại tiền tệ do người dùng chọn.

**Quy tắc nghiệp vụ:**

- **`BR-02.1` (Tiền tệ theo cơ hội):** Mỗi cơ hội lưu đúng một mã tiền tệ chuẩn quốc tế.

- **`BR-02.2` (Đồng tiền cơ sở và quy đổi bắt buộc trong báo cáo hợp nhất):** Mỗi Không gian làm việc khai báo **đúng một đồng tiền cơ sở** (Phụ lục B, `CFG-DEAL-08`). Mọi báo cáo tổng hợp nhiều cơ hội — dự báo doanh thu, hiệu suất người phụ trách, tổng giá trị đầu cột Kanban — **bắt buộc quỹ đổi toàn bộ giá trị về đồng tiền cơ sở trước khi cộng dồn**. Tuyệt đối không cộng trực tiếp các giá trị khác loại tiền tệ.

  **Lý do nghiệp vụ:** Cộng thẳng 100 USD với 100 VND tạo ra một con số vô nghĩa. Một cảnh báo "số liệu đang trộn tiền tệ" không cứu được tình huống này: báo cáo dự báo doanh thu là căn cứ cam kết số với ban giám đốc, nên một con số không dùng được thì báo cáo không dùng được, dù có ghi chú bên cạnh hay không.

- **`BR-02.3` (Chốt tỷ giá khi đóng cơ hội):** Khi một cơ hội được đóng (Thắng hoặc Thua), tỷ giá quy đổi tại thời điểm đóng được **chốt cứng vào cơ hội đó**. Biến động tỷ giá sau này không bao giờ làm thay đổi giá trị quy đổi của các cơ hội đã đóng trong báo cáo lịch sử.

  **Lý do nghiệp vụ:** Nếu không chốt tỷ giá, doanh số đã chốt của một quý đã khóa sổ sẽ tự thay đổi mỗi lần tỷ giá biến động — báo cáo tài chính lịch sử mất tính ổn định và không đối chiếu được với sổ sách kế toán.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-02.1.1` | Tạo cơ hội | Chọn tiền tệ USD, nhập giá trị | Lưu thành công với tiền tệ USD |
| `AC-02.2.1` | Đồng tiền cơ sở là VND. Có hai cơ hội: một 1.000 USD, một 10 triệu VND | Mở báo cáo dự báo doanh thu tổng hợp | Tổng hiển thị bằng VND, đã quy đổi cơ hội USD theo tỷ giá; không có số tổng nào cộng thẳng 1.000 với 10.000.000 |
| `AC-02.2.2` | Cùng bối cảnh AC-02.2.1 | Mở Bảng Kanban, xem tổng giá trị đầu cột chứa cả hai cơ hội | Tổng đầu cột cũng đã quy đổi về VND |
| `AC-02.3.1` | Một cơ hội 1.000 USD được đóng thắng khi tỷ giá là 25.000 VND/USD | Sau đó tỷ giá đổi thành 26.000, mở lại báo cáo doanh số của kỳ đã đóng | Giá trị quy đổi của cơ hội đó vẫn là 25 triệu VND, không bị tính lại theo tỷ giá mới |

---

#### FEAT-03 — Bảo vệ Dữ liệu Tài chính Nhạy cảm

**Mô tả nghiệp vụ:** Ẩn giá trị và xác suất thắng của cơ hội đối với người dùng không có quyền xem đầy đủ dữ liệu tài chính.

**Quy tắc nghiệp vụ:**

- **`BR-03.1` (Che số liệu theo quyền):** Giá trị cơ hội và xác suất thắng thuộc nhóm dữ liệu nhạy cảm do hệ thống định nghĩa sẵn. Người dùng không có quyền "xem đầy đủ" tương ứng thấy hai trường này bị **ẩn hoàn toàn** — không hiển thị bất kỳ chữ số nào, chứ không phải che bớt một phần. Quyền "xem đầy đủ" là một quyền hạn **riêng biệt**, không tự động đi kèm quyền xem hồ sơ cơ hội nói chung. Cơ chế che, danh mục trường nhạy cảm và cách cấp quyền "xem đầy đủ" thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md); phân hệ này chỉ đặc tả **phạm vi áp dụng trên dữ liệu Cơ hội bán hàng**.

  **Lý do ẩn hoàn toàn thay vì che một phần:** Với một con số tiền tệ, che một phần vẫn làm lộ độ lớn của giao dịch — biết một hợp đồng có chử số hàng tỷ hay hàng triệu đã là thông tin cạnh tranh đáng kể. Khác với số điện thoại hay email (che một phần vẫn đủ để đối chiếu đúng người), giá trị hợp đồng không có nhu cầu đối chiếu tương tự nên không có lý do để lộ một phần.

  **Lý do nghiệp vụ:** Giá trị hợp đồng là dữ liệu tài chính nhạy cảm nhất trên một cơ hội. Tách quyền xem hồ sơ khỏi quyền xem số liệu thật cho phép nhân sự hỗ trợ (ví dụ Pre-sales) tham gia xử lý cơ hội mà không cần biết giá trị hợp đồng.

- **`BR-03.2` (Che nhất quán trên mọi nơi số liệu xuất hiện):** Việc che số liệu tài chính áp dụng đồng nhất ở **mọi nơi giá trị cơ hội xuất hiện**, không chỉ trên hồ sơ chi tiết: thẻ trên Bảng Kanban, **tổng giá trị đầu mỗi cột Kanban** (`FEAT-09`), danh sách cơ hội, báo cáo dự báo doanh thu (`FEAT-25`), báo cáo hiệu suất (`FEAT-26`), dòng thời gian (`FEAT-30`) và **tệp dữ liệu xuất ra** (`FEAT-29`).

  **Lý do nghiệp vụ:** Che số liệu chỉ trên màn hình chi tiết là vô nghĩa nếu người dùng vẫn đọc được con số thật ở chỗ khác. Nguy hiểm nhất là hai đường rò rỉ gián tiếp: **tổng giá trị đầu cột Kanban** (cột chỉ có một cơ hội thì tổng chính là giá trị cơ hội đó) và **tệp xuất dữ liệu** (một lần xuất là mang toàn bộ số liệu ra ngoài hệ thống). Một đường rò duy nhất làm vô hiệu toàn bộ cơ chế.

- **`BR-03.3` (Hệ quả trên lọc và sắp xếp):** Với người dùng đang bị che giá trị cơ hội, hệ thống **không cho lọc hoặc sắp xếp danh sách theo giá trị**.

  **Lý do nghiệp vụ:** Sắp xếp tăng dần theo giá trị rồi đọc thứ tự là suy ra được tương quan độ lớn giữa các cơ hội; lọc theo khoảng giá trị rồi thu hẹp dần là dò ra được con số thật. Hai thao tác này vô hiệu hóa việc che dù giá trị không bao giờ hiện ra màn hình.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-03.1.1` | Người dùng có quyền xem cơ hội nhưng không có quyền xem đầy đủ dữ liệu tài chính | Mở hồ sơ cơ hội | Giá trị và xác suất thắng bị ẩn hoàn toàn — không hiển thị chữ số nào |
| `AC-03.1.2` | Người dùng có cả hai quyền | Mở hồ sơ cơ hội | Giá trị hiển thị đầy đủ |
| `AC-03.2.1` | Người dùng không có quyền xem đầy đủ | Mở Bảng Kanban | Giá trị trên từng thẻ bị ẩn |
| `AC-03.2.2` | Người dùng không có quyền xem đầy đủ, cột "Đàm phán" chỉ có đúng 1 cơ hội | Mở Bảng Kanban, nhìn tổng giá trị đầu cột | Tổng giá trị đầu cột cũng bị ẩn — không suy ra được giá trị cơ hội duy nhất trong cột |
| `AC-03.2.3` | Người dùng không có quyền xem đầy đủ | Mở báo cáo dự báo doanh thu (`FEAT-25`) | Số liệu dự báo bị ẩn, không đọc được giá trị thật |
| `AC-03.2.4` | Người dùng không có quyền xem đầy đủ nhưng có quyền xuất dữ liệu | Xuất danh sách cơ hội ra tệp | Cột giá trị trong tệp xuất không chứa số thật |
| `AC-03.2.5` | Người dùng không có quyền xem đầy đủ | Mở báo cáo hiệu suất người phụ trách (`FEAT-26`) | Các cột giá trị tiền tệ bị ẩn |
| `AC-03.2.6` | Người dùng không có quyền xem đầy đủ | Mở danh sách cơ hội và dòng thời gian của một cơ hội | Giá trị bị ẩn ở cả hai màn hình |
| `AC-03.3.1` | Người dùng không có quyền xem đầy đủ | Mở danh sách cơ hội, thử sắp xếp theo cột giá trị | Chức năng sắp xếp theo giá trị không khả dụng |
| `AC-03.3.2` | Cùng bối cảnh AC-03.3.1 | Thử lọc theo khoảng giá trị | Chức năng lọc theo giá trị không khả dụng |

---

#### FEAT-04 — Thùng rác & Phục hồi Cơ hội

**Mô tả nghiệp vụ:** Xóa mềm cơ hội bán hàng, lưu trữ tạm thời và cho phép phục hồi.

**Quy tắc nghiệp vụ:**

- **`BR-04.1` (Xóa mềm):** Cơ hội bị xóa được ẩn khỏi Kanban, báo cáo và tìm kiếm nhưng vẫn được lưu trữ trong một thời hạn nhất định.

- **`BR-04.2` (Thời hạn lưu trữ):** Thời hạn lưu trong thùng rác trước khi xóa vĩnh viễn là **30 ngày**, áp dụng thống nhất trên toàn hệ thống ở phiên bản hiện tại — chưa cấu hình được riêng theo từng Không gian làm việc (xem Phụ lục B, `CFG-DEAL-01`).

- **`BR-04.3` (Xóa vĩnh viễn có cascade rõ ràng):** Khi hết thời hạn lưu trữ, cơ hội bị xóa vĩnh viễn theo đúng một quy tắc cho từng loại dữ liệu liên quan: Vé hỗ trợ liên kết được gỡ liên kết nhưng **không** bị xóa theo; Công việc liên kết được gỡ liên kết; các bản ghi trên dòng thời gian hoạt động bị xóa cùng cơ hội; nhật ký kiểm toán liên quan **được giữ lại vĩnh viễn** bất kể cơ hội đã bị xóa.

  **Lý do nghiệp vụ:** Vé hỗ trợ và Công việc là bản ghi độc lập có vòng đời riêng của khách hàng — xóa cơ hội không có nghĩa là các tương tác đó chưa từng xảy ra. Nhật ký kiểm toán phải sống lâu hơn bản ghi mà nó ghi nhận, nếu không việc điều tra "ai đã xóa cơ hội này" sẽ tự mất bằng chứng ngay khi cơ hội biến mất hoàn toàn.

- **`BR-04.4` (Phục hồi):** Phục hồi cơ hội trả nó về đúng vị trí giai đoạn cũ với toàn bộ lịch sử giai đoạn còn nguyên vẹn.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-04.1.1` | Cơ hội đang mở, có gắn 1 Vé hỗ trợ | Xóa cơ hội | Cơ hội biến mất khỏi Kanban/báo cáo; Vé hỗ trợ vẫn tồn tại độc lập, không còn liên kết |
| `AC-04.2.1` | Cơ hội đã ở thùng rác đúng 30 ngày | Tiến trình dọn dẹp chạy | Cơ hội bị xóa vĩnh viễn; nhật ký kiểm toán về việc xóa vẫn tra cứu được |
| `AC-04.3.1` | Cơ hội đã ở thùng rác đủ thời hạn, có gắn 1 Công việc đang mở | Tiến trình dọn dẹp chạy | Công việc vẫn tồn tại trong danh sách việc cần làm, không còn liên kết tới cơ hội đã xóa |
| `AC-04.3.2` | Cơ hội đã ở thùng rác đủ thời hạn, có dòng thời gian với nhiều ghi chú/hoạt động | Tiến trình dọn dẹp chạy | Toàn bộ bản ghi dòng thời gian của cơ hội đó bị xóa theo, không còn tra cứu được |
| `AC-04.4.1` | Cơ hội đang ở thùng rác, trước đó ở giai đoạn "Đàm phán" | Phục hồi | Cơ hội quay lại đúng giai đoạn "Đàm phán", lịch sử giai đoạn đầy đủ |

---

### Nhóm B — Phễu Bán hàng & Giai đoạn

#### FEAT-05 — Quản trị Nhiều Phễu Bán hàng Độc lập

**Mô tả nghiệp vụ:** Cho phép doanh nghiệp vận hành song song nhiều quy trình bán hàng độc lập.

**Quy tắc nghiệp vụ:**

- **`BR-05.1` (Một phễu mặc định duy nhất):** Mỗi Không gian làm việc luôn duy trì đúng một phễu được đánh dấu mặc định.

- **`BR-05.2` (Khóa đồng thời khi sửa cấu hình):** Xem `FEAT-34`.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-05.1.1` | Không gian làm việc có 1 phễu mặc định | Đặt phễu khác làm mặc định | Phễu cũ tự động mất trạng thái mặc định, chỉ còn đúng 1 phễu mặc định |

---

#### FEAT-06 — Thiết lập Giai đoạn, Xác suất Thắng & Thứ tự

**Mô tả nghiệp vụ:** Định nghĩa các giai đoạn tuần tự trong phễu, gán xác suất thắng và sắp xếp thứ tự.

**Quy tắc nghiệp vụ:**

- **`BR-06.1` (Xác suất thắng):** Là một giá trị phần trăm từ 0% đến 100%, do Quản trị viên gán cho từng giai đoạn. Ở phạm vi hiện tại, hệ thống chấp nhận cả giá trị lẻ (ví dụ 33%); việc có bắt buộc số nguyên tròn hay không là một lựa chọn hiển thị/quy đổi báo cáo, không phải một ràng buộc toàn vẹn dữ liệu.

- **`BR-06.2` (Một giai đoạn không được vừa là Thắng vừa là Thua):** Một giai đoạn không được đồng thời đánh dấu vừa là giai đoạn Thắng vừa là giai đoạn Thua.

- **`BR-06.2b` (Hai giai đoạn kết thúc bắt buộc):** Mỗi phễu phải có đúng hai giai đoạn kết thúc: một giai đoạn Thắng (xác suất 100%) và một giai đoạn Thua (xác suất 0%) — không thiếu, không thừa. Hệ thống từ chối lưu một phễu không thỏa điều kiện này.

  **Lý do nghiệp vụ:** Thiếu ràng buộc này để lại rủi ro dữ liệu thật: một phễu không có giai đoạn Thắng khiến không cơ hội nào trong phễu đó có thể đóng thắng đúng quy trình (`BR-18.3` yêu cầu chuyển sang "giai đoạn Thắng đã cấu hình"); nhiều hơn một giai đoạn Thắng làm mơ hồ báo cáo dự báo doanh thu (`FEAT-25`) vì không rõ giai đoạn Thắng nào là chuẩn để tính xác suất 100%.

- **`BR-06.3` (Sắp xếp thứ tự):** Quản trị viên kéo thả đổi thứ tự các giai đoạn; toàn bộ thứ tự được cập nhật trong một thao tác duy nhất.

- **`BR-06.4` (Khóa nhảy cóc giai đoạn) [tham số cấu hình theo phễu]:** Mỗi phễu có thể bật quy tắc **không cho phép nhảy cóc** — chỉ được chuyển sang giai đoạn liền kề tiếp theo, không được bỏ qua giai đoạn ở giữa khi đang tiến lên. Quy tắc này **không áp dụng** khi lùi giai đoạn hoặc khi đóng cơ hội (Thắng/Thua có thể xảy ra từ bất kỳ giai đoạn nào). Mặc định **tắt** (Phụ lục B, `CFG-DEAL-02`).

  **Lý do nghiệp vụ:** Có doanh nghiệp coi việc nhảy cóc giai đoạn là dấu hiệu bỏ qua bước thẩm định quan trọng (ví dụ bỏ qua bước khảo sát kỹ thuật để nhảy thẳng vào đàm phán), cần khóa cứng theo từng phễu để đảm bảo tuân thủ quy trình. Doanh nghiệp khác có quy trình bán hàng linh hoạt, một cơ hội có thể đến thẳng từ giai đoạn đàm phán do khách hàng đã làm việc trước đó ở kênh khác — khóa cứng sẽ cản trở vận hành thật. Đây là tham số theo từng phễu (không phải toàn không gian làm việc) vì một doanh nghiệp có thể có phễu bán mới cần tuân thủ chặt và phễu gia hạn hợp đồng cần linh hoạt hơn.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-06.1.1` | Quản trị viên cấu hình giai đoạn | Nhập xác suất thắng là 150% | Từ chối, nêu rõ miền hợp lệ 0–100% |
| `AC-06.1.2` | Quản trị viên cấu hình giai đoạn | Nhập xác suất thắng là số âm | Từ chối |
| `AC-06.2.1` | Quản trị viên cấu hình một giai đoạn | Cố gắng đánh dấu giai đoạn đó vừa là Thắng vừa là Thua | Từ chối |
| `AC-06.2b.1` | Quản trị viên tạo phễu mới, chưa đánh dấu giai đoạn nào là Thắng hoặc Thua | Cố gắng lưu phễu | Từ chối, yêu cầu đủ cả giai đoạn Thắng và Thua |
| `AC-06.2b.2` | Phễu đã có 1 giai đoạn Thắng, chưa có giai đoạn Thua nào | Cố gắng lưu phễu | Từ chối, báo thiếu giai đoạn Thua |
| `AC-06.2b.3` | Quản trị viên cố gắng đánh dấu một giai đoạn thứ hai là Thắng trong khi phễu đã có 1 giai đoạn Thắng | Lưu | Từ chối, báo phễu chỉ được có đúng một giai đoạn Thắng |
| `AC-06.4.1` | Phễu bật khóa nhảy cóc, cơ hội đang ở giai đoạn 2/5 | Cố gắng chuyển thẳng sang giai đoạn 4 | Từ chối, yêu cầu qua giai đoạn 3 trước |
| `AC-06.4.2` | Cùng bối cảnh AC-06.4.1 | Đóng cơ hội thắng ngay từ giai đoạn 2 | Cho phép — khóa nhảy cóc không áp dụng khi đóng cơ hội |
| `AC-06.4.3` | Cùng bối cảnh AC-06.4.1 | Lùi cơ hội từ giai đoạn 2 về giai đoạn 1 | Cho phép — khóa nhảy cóc chỉ áp dụng khi tiến lên |

---

#### FEAT-07 — Đóng Phễu An toàn & Di chuyển Cơ hội

**Mô tả nghiệp vụ:** Khi ngừng sử dụng một phễu, bắt buộc quản trị viên chỉ định phương án xử lý cho toàn bộ cơ hội đang mở.

**Quy tắc nghiệp vụ:**

- **`BR-07.1` (Hai phương án loại trừ lẫn nhau):** Quản trị viên chọn đúng một trong hai: **(a)** thiết lập bảng ghép từng giai đoạn cũ sang giai đoạn tương ứng của một phễu khác, di chuyển toàn bộ cơ hội đang mở theo bảng ghép đó; hoặc **(b)** đóng băng toàn bộ cơ hội trong phễu ở chế độ chỉ đọc để bảo toàn lịch sử báo cáo, không di chuyển đi đâu.

- **`BR-07.2` (Chặn đóng phễu còn dữ liệu mở mà chưa chọn phương án):** Hệ thống từ chối yêu cầu đóng phễu nếu phễu còn cơ hội đang mở mà chưa chọn một trong hai phương án ở `BR-07.1`.

- **`BR-07.3` (Toàn vẹn tuyệt đối khi đóng phễu):** Toàn bộ thao tác đóng phễu — di chuyển cơ hội sang phễu mới (hoặc đóng băng chỉ đọc) và đánh dấu phễu cũ đã đóng — phải **cùng thành công hoặc cùng thất bại như một đơn vị duy nhất**. Không bao giờ được phép tồn tại trạng thái trung gian: cơ hội đã chuyển đi nhưng phễu cũ vẫn mở, hoặc phễu đã đóng nhưng cơ hội còn kẹt lại.

  **Lý do nghiệp vụ:** Một trạng thái trung gian ở đây nghĩa là toàn bộ phễu bán hàng của một doanh nghiệp rơi vào trạng thái không xác định giữa chừng — cơ hội mất dấu, báo cáo sai, và không có cách nào biết chắc dữ liệu nào đã chuyển, dữ liệu nào chưa. Đây là thao tác ít khi chạy nhưng khi chạy thì đụng tới toàn bộ dữ liệu bán hàng, nên không chấp nhận được rủi ro mất nhất quán.

- **`BR-07.4` (Không hỗ trợ mở lại phễu đã đóng):** Ở phạm vi phát hành hiện tại, một phễu đã đóng (dù theo phương án di chuyển hay đóng băng chỉ đọc) **không có thao tác đưa trở lại trạng thái hoạt động**. Đây là quyết định có chủ đích, không phải một khoảng trống bị bỏ sót: cho phép mở lại một phễu đã di chuyển hết dữ liệu đi nơi khác sẽ tạo ra một phễu rỗng gây nhầm lẫn, còn mở lại một phễu đang đóng băng chỉ đọc thì không rõ các cơ hội đã bị đóng băng có nên tiếp tục vận hành bình thường trở lại hay không — quyết định này chưa chín muồi. Nếu về sau phát sinh nhu cầu mở lại phễu, cần bổ sung `FEAT` riêng, không mở rộng ngầm ý nghĩa của `FEAT-07`.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-07.1.1` | Phễu A còn 10 cơ hội đang mở | Chọn di chuyển sang Phễu B theo bảng ghép giai đoạn, đóng Phễu A | 10 cơ hội chuyển sang đúng giai đoạn tương ứng ở Phễu B; Phễu A đóng |
| `AC-07.1.2` | Phễu A còn 10 cơ hội đang mở | Chọn đóng băng chỉ đọc, đóng Phễu A | 10 cơ hội giữ nguyên vị trí, chuyển chỉ đọc; Phễu A đóng |
| `AC-07.2.1` | Phễu A còn cơ hội đang mở | Cố gắng đóng phễu mà không chọn phương án nào | Từ chối, yêu cầu chọn phương án xử lý dữ liệu |

---

#### FEAT-08 — Xóa Giai đoạn An toàn

**Mô tả nghiệp vụ:** Khi xóa một giai đoạn, bắt buộc chỉ định giai đoạn tiếp nhận các cơ hội đang có trong giai đoạn đó.

**Quy tắc nghiệp vụ:**

- **`BR-08.1` (Bắt buộc giai đoạn đích):** Bắt buộc chỉ định một giai đoạn đích hợp lệ trong cùng phễu. Toàn bộ cơ hội tại giai đoạn bị xóa được chuyển sang giai đoạn đích trước khi xóa.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-08.1.1` | Giai đoạn "Khảo sát" có 5 cơ hội | Xóa giai đoạn, chọn giai đoạn đích "Báo giá" | 5 cơ hội chuyển sang "Báo giá"; giai đoạn "Khảo sát" bị xóa |
| `AC-08.1.2` | Giai đoạn "Khảo sát" có 5 cơ hội | Cố gắng xóa mà không chọn giai đoạn đích | Từ chối |

---

### Nhóm C — Bảng Kanban Cơ hội

#### FEAT-09 — Bảng Kanban Tổng hợp Giá trị Thời gian thực

**Mô tả nghiệp vụ:** Hiển thị tổng số lượng và tổng giá trị cơ hội trên từng cột giai đoạn, có áp dụng phân quyền dữ liệu.

**Quy tắc nghiệp vụ:**

- **`BR-09.1` (Tổng số phản ánh đúng phạm vi quyền):** Số liệu tổng trên đầu mỗi cột chỉ tính các cơ hội mà người xem có quyền nhìn thấy, khớp chính xác với bộ lọc đang áp dụng.

- **`BR-09.2` (Tải thẻ theo từng cột độc lập):** Danh sách thẻ cơ hội trong mỗi cột được tải riêng biệt, cuộn thêm khi cần, không phụ thuộc vào việc tải các cột khác.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-09.1.1` | Cột "Đàm phán" có 200 cơ hội, người xem có phạm vi dữ liệu Cá nhân và chỉ phụ trách 50 trong số đó | Mở Kanban | Tổng số trên đầu cột hiển thị 50, và tổng giá trị cũng chỉ cộng dồn 50 cơ hội đó |
| `AC-09.2.1` | Một cột có nhiều cơ hội hơn số thẻ tải lần đầu | Cuộn xuống cuối cột đó | Tải thêm thẻ của riêng cột đó; các cột khác không bị tải lại và giữ nguyên vị trí cuộn |

---

#### FEAT-10 — Kéo thả Chuyển Giai đoạn

**Mô tả nghiệp vụ:** Nhân viên kéo thả thẻ cơ hội từ cột này sang cột khác để cập nhật tiến độ.

**Quy tắc nghiệp vụ:**

- **`BR-10.1` (Kiểm tra rào cản trước khi chuyển):** Kéo thả kích hoạt kiểm tra Rào cản Giai đoạn (`FEAT-13`). Nếu đủ điều kiện, hệ thống cập nhật giai đoạn, ghi nhận thời gian đã lưu ở giai đoạn cũ và thêm một bản ghi vào lịch sử giai đoạn (`FEAT-12`).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-10.1.1` | Cơ hội đủ điều kiện rào cản | Kéo thả sang cột kế tiếp | Chuyển thành công, lịch sử giai đoạn ghi nhận thêm 1 dòng |

---

#### FEAT-11 — Bộ lọc trên Bảng Kanban

**Mô tả nghiệp vụ:** Lọc nhanh cơ hội hiển thị trên Kanban theo các tiêu chí thường dùng.

**Quy tắc nghiệp vụ:**

- **`BR-11.1` (Tiêu chí lọc sẵn có):** Lọc theo "Cơ hội của tôi", theo trạng thái lịch chăm sóc tiếp theo (quá hạn/hôm nay/chưa đặt lịch), và theo từ khóa tìm kiếm.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-11.1.1` | Danh sách có cơ hội quá hạn lịch chăm sóc | Lọc theo "Quá hạn chăm sóc" | Chỉ hiện các cơ hội có lịch chăm sóc đã quá hạn |

---

### Nhóm D — Lịch sử Giai đoạn & Vận tốc

#### FEAT-12 — Ghi nhận Lịch sử Chuyển Giai đoạn

**Mô tả nghiệp vụ:** Tự động lưu lại mỗi lần chuyển giai đoạn: thời điểm, người thực hiện và thời gian đã lưu ở giai đoạn trước đó.

**Quy tắc nghiệp vụ:**

- **`BR-12.1` (Bất biến có giới hạn):** Mỗi bản ghi lịch sử giai đoạn là bất biến — không sửa, không xóa. Hệ thống giữ lại **100 bản ghi gần nhất** cho mỗi cơ hội; các cơ hội đổi giai đoạn ít hơn con số này có lịch sử đầy đủ tuyệt đối.

  **Lý do nghiệp vụ:** Giới hạn số bản ghi ngăn một cơ hội bị đổi giai đoạn qua lại liên tục (thao tác nhầm, thử nghiệm cấu hình) làm phình to vô hạn dữ liệu lịch sử. 100 lần chuyển giai đoạn vượt xa số bước thực tế của bất kỳ phễu bán hàng nào trong vận hành bình thường.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-12.1.1` | Cơ hội chuyển giai đoạn 3 lần | Xem lịch sử giai đoạn | Hiển thị đủ 3 dòng, mỗi dòng có thời điểm, người thực hiện, thời gian lưu ở giai đoạn trước |

---

#### FEAT-13 — Rào cản Điều kiện Chuyển Giai đoạn

**Mô tả nghiệp vụ:** Quản trị viên cấu hình danh sách trường dữ liệu bắt buộc phải có giá trị trước khi một cơ hội được coi là đủ điều kiện ở một giai đoạn.

**Quy tắc nghiệp vụ:**

- **`BR-13.1` (Trường bắt buộc theo giai đoạn):** Mỗi giai đoạn cấu hình được một danh sách trường dữ liệu (kể cả trường tùy biến) bắt buộc phải có giá trị. Áp dụng cả khi tạo cơ hội thẳng vào giai đoạn đó lẫn khi chuyển giai đoạn tới. Việc khai báo danh sách trường và màn hình cấu hình thuộc [`object-manager-srs.md`](./object-manager-srs.md); phân hệ này đặc tả **cách rào cản được áp dụng trên cơ hội bán hàng**.

- **`BR-13.1b` (Miễn trừ khi người dùng không có quyền nhập trường):** Nếu một trường bắt buộc bị ẩn hoặc chỉ cho xem đối với chính người đang thao tác, hệ thống **không được ép họ nhập** trường đó. Cơ hội vẫn được lưu và được **gắn cờ thiếu dữ liệu bắt buộc**, để người có thẩm quyền bổ sung sau. Ràng buộc vẫn áp dụng đầy đủ với người có quyền nhập trường đó.

  **Lý do nghiệp vụ:** Không có miễn trừ này, một nhân viên bị che trường giá trị sẽ không bao giờ lưu nổi cơ hội ở giai đoạn yêu cầu giá trị — quy tắc bảo mật và quy tắc chất lượng dữ liệu khóa chặt nhau, người dùng không có lối ra. Cờ thiếu dữ liệu giữ được cả hai mục tiêu: không rò rỉ dữ liệu, cũng không để bản ghi thiếu sót trôi đi âm thầm.

- **`BR-13.2` (Tích lũy khi nhảy cóc):** Khi một cơ hội chuyển qua nhiều giai đoạn cùng lúc (bỏ qua các giai đoạn ở giữa), điều kiện bắt buộc của **toàn bộ các giai đoạn bị bỏ qua** đều phải được thỏa mãn, không chỉ giai đoạn đích.

  **Lý do nghiệp vụ:** Nếu chỉ kiểm tra điều kiện của giai đoạn đích, một cơ hội có thể nhảy thẳng từ giai đoạn đầu tới giai đoạn cuối mà bỏ qua toàn bộ yêu cầu dữ liệu của các bước ở giữa — đúng lỗ hổng mà rào cản giai đoạn được tạo ra để chặn.

- **`BR-13.3` (Từ chối và liệt kê thiếu sót):** Khi không đủ điều kiện, hệ thống từ chối chuyển giai đoạn và liệt kê rõ từng trường còn thiếu.

- **`BR-13.4` (Ba loại điều kiện rào cản):** Một giai đoạn cấu hình được ba loại điều kiện, dùng riêng lẻ hoặc kết hợp:
  - **Trường dữ liệu bắt buộc** — danh sách trường phải có giá trị (`BR-13.1`).
  - **Tài liệu bắt buộc đính kèm** — ví dụ vào giai đoạn ký kết phải có bản hợp đồng đã ký.
  - **Vai trò liên hệ bắt buộc** — ví dụ vào giai đoạn đàm phán phải khai báo ít nhất một Người ra quyết định (`FEAT-22`).

  **Lý do nghiệp vụ:** Ba loại điều kiện này chặn ba rủi ro khác nhau. Trường dữ liệu chống báo cáo rỗng; tài liệu chống việc ghi nhận thắng mà không có căn cứ pháp lý; vai trò liên hệ chống việc đàm phán với người không có thẩm quyền quyết định — nguyên nhân thua thầu phổ biến nhất trong bán hàng doanh nghiệp.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-13.1.1` | Giai đoạn "Báo giá" bắt buộc có Ngày dự kiến đóng | Chuyển cơ hội chưa có Ngày dự kiến đóng vào "Báo giá" | Từ chối, báo thiếu Ngày dự kiến đóng |
| `AC-13.2.1` | Giai đoạn 2 bắt buộc trường X, giai đoạn 3 bắt buộc trường Y. Cơ hội đang ở giai đoạn 1, chưa có cả X lẫn Y | Chuyển thẳng từ giai đoạn 1 sang giai đoạn 4 | Từ chối, liệt kê thiếu cả trường X và Y |
| `AC-13.1b.1` | Giai đoạn "Báo giá" bắt buộc có Giá trị cơ hội; nhân viên K không có quyền nhập trường giá trị | K chuyển cơ hội vào "Báo giá" | Cho phép lưu; cơ hội được gắn cờ thiếu dữ liệu bắt buộc |
| `AC-13.1b.2` | Tiếp nối AC-13.1b.1 | Quản lý có quyền nhập giá trị mở danh sách cơ hội thiếu dữ liệu | Thấy cơ hội đó trong danh sách để bổ sung |
| `AC-13.1b.3` | Tiếp nối AC-13.1b.2 | Quản lý nhập giá trị hợp lệ | Cờ thiếu dữ liệu tự động biến mất |
| `AC-13.3.1` | Cơ hội thiếu 3 trường bắt buộc của giai đoạn đích | Chuyển giai đoạn | Thông báo liệt kê đủ **cả ba** tên trường còn thiếu trong cùng một lần báo, không báo lần lượt từng trường |

---

#### FEAT-14 — Phân tích Vận tốc Bán hàng & Điểm nghẽn

**Mô tả nghiệp vụ:** Báo cáo cho biết số ngày trung bình để chốt một hợp đồng, và giai đoạn nào đang là điểm nghẽn của phễu.

**Quy tắc nghiệp vụ:**

- **`BR-14.1` (Vận tốc chốt hợp đồng):** Báo cáo tính số ngày trung bình và số ngày trung vị từ khi tạo cơ hội tới khi Đóng Thành công.

- **`BR-14.2` (Phân tích điểm nghẽn theo giai đoạn):** Với mỗi giai đoạn, báo cáo cho biết: số cơ hội đã từng vào giai đoạn đó, số cơ hội hiện đang ở đó, số ngày trung bình/trung vị lưu tại giai đoạn đó, và tỷ lệ chuyển tiếp thành công từ giai đoạn liền trước.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-14.1.1` | Có 20 cơ hội đã Đóng Thành công trong quý | Mở báo cáo vận tốc | Hiển thị đúng số ngày trung bình và trung vị tính trên 20 cơ hội đó |
| `AC-14.2.1` | Giai đoạn "Đàm phán" có tỷ lệ chuyển tiếp thấp bất thường | Mở báo cáo điểm nghẽn | Giai đoạn "Đàm phán" hiển thị tỷ lệ chuyển tiếp thấp, dễ nhận diện là điểm nghẽn |

---

### Nhóm E — Nhắc nhở Chăm sóc & Cảnh báo Cơ hội Nguội

#### FEAT-15 — Thiết lập Lịch Chăm sóc Tiếp theo

**Mô tả nghiệp vụ:** Người phụ trách đặt thời điểm cam kết liên hệ lại khách hàng.

**Quy tắc nghiệp vụ:**

- **`BR-15.1` (Mặc định khi tạo mới):** Việc hệ thống có **tự động đặt** lịch chăm sóc cho một cơ hội mới khi người dùng bỏ trống hay không là tham số cấu hình theo Không gian làm việc (Phụ lục B, `CFG-DEAL-07`), **mặc định tắt**. Khi được bật, khoảng thời gian từ lúc tạo tới lịch chăm sóc đầu tiên là một tham số riêng, mặc định **24 giờ** (Phụ lục B, `CFG-DEAL-03`).

  **Lý do nghiệp vụ:** Tự đặt lịch cho mọi cơ hội khiến mỗi cơ hội mới đều sinh ra một lần nhắc và một Công việc thật (`BR-16.2`) sau đúng một ngày. Với đội nhập hàng chục cơ hội mỗi tuần — trong đó nhiều cơ hội là khách hàng dài hạn chưa cần chăm sóc ngay — danh sách việc cần làm nhanh chóng đầy những lời nhắc vô nghĩa. Hệ quả là nhân viên học được rằng nhắc việc từ hệ thống là nhiễu và bỏ qua luôn cả những lời nhắc thật — đúng thất bại mà toàn bộ Nhóm E sinh ra để ngăn chặn. Doanh nghiệp nào có quỳ đạo chăm sóc dày đặc và muốn ép nhịp thì bật tham số này lên.

- **`BR-15.2` (Dời lịch sau khi đã có Công việc nhắc):** Nếu người phụ trách dời lịch chăm sóc tiếp theo sang một thời điểm khác **sau khi** Công việc nhắc nhở tương ứng đã được tạo (`BR-16.2`) nhưng **trước khi** Công việc đó được xử lý, Công việc cũ phải được cập nhật theo hạn mới hoặc hủy và thay bằng một Công việc mới đúng hạn — không được để cả Công việc cũ (theo lịch đã dời) và lịch mới cùng tồn tại song song, gây người phụ trách nhận việc nhắc cho một lịch hẹn không còn hiệu lực.

  **Lý do nghiệp vụ:** Nếu không xử lý chiều ngược này, một hành động hợp lý của người dùng (dời lịch vì khách hàng xin dời hẹn) sẽ để lại một Công việc "rác" nhắc sai thời điểm, làm người phụ trách mất niềm tin vào danh sách việc cần làm và có thể liên hệ khách hàng nhầm thời điểm đã thống nhất lại.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-15.1.1` | `CFG-DEAL-07` ở mặc định (tắt). Tạo cơ hội mới, không tự đặt lịch chăm sóc | Lưu | Cơ hội không có lịch chăm sóc nào; không phát sinh nhắc việc hay Công việc tự động nào sau đó |
| `AC-15.1.2` | Quản lý bật `CFG-DEAL-07`, `CFG-DEAL-03` để mặc định 24 giờ. Tạo cơ hội mới, không tự đặt lịch | Lưu | Hệ thống tự gán lịch chăm sóc sau 24 giờ kể từ thời điểm tạo |
| `AC-15.1.3` | `CFG-DEAL-07` đang bật | Người dùng tự chọn lịch chăm sóc là 3 ngày sau | Lưu đúng lựa chọn của người dùng, không bị ghi đè bởi mặc định 24 giờ |
| `AC-15.2.1` | Lịch chăm sóc đã đến hạn, Công việc nhắc đã được tạo nhưng chưa xử lý | Người phụ trách dời lịch chăm sóc sang 3 ngày sau | Công việc nhắc cũ được cập nhật sang hạn mới (hoặc hủy và tạo lại), không còn Công việc nào nhắc theo hạn cũ đã dời |

---

#### FEAT-16 — Quét & Nhắc nhở Lịch Chăm sóc Đến hạn

**Mô tả nghiệp vụ:** Tiến trình nền định kỳ quét các cơ hội có lịch chăm sóc đã đến hạn và chưa được nhắc, để phát thông báo tới người phụ trách.

**Quy tắc nghiệp vụ:**

- **`BR-16.1` (Chu kỳ quét):** Tiến trình quét chạy mỗi **5 phút**, là hằng số vận hành hệ thống, không cấu hình được theo từng Không gian làm việc.

- **`BR-16.2` (Kết quả khi đến hạn):** Khi phát hiện lịch chăm sóc đến hạn, hệ thống đồng thời: phát thông báo trong ứng dụng tới người phụ trách, **và tạo một Công việc mới giao cho người phụ trách đó** để việc chăm sóc xuất hiện trong danh sách việc cần làm hằng ngày, không chỉ là một thông báo thoáng qua.

- **`BR-16.3` (Chống nhắc trùng):** Mỗi lịch chăm sóc chỉ được nhắc đúng một lần, kể cả khi có nhiều lượt quét chạy đồng thời.

- **`BR-16.4` (Leo thang thay vì im lặng khi quá hạn lâu):** Một lịch chăm sóc quá hạn càng lâu mà chưa được xử lý thì càng phải được làm nổi bật, không phải càng bị làm ngơ. Khi một lịch chăm sóc vượt quá một ngưỡng quá hạn đáng kể mà người phụ trách vẫn chưa xử lý, hệ thống phải **báo lên cấp quản lý trực tiếp** của người đó thay vì ngừng nhắc.

  **Lý do nghiệp vụ:** Mục tiêu số một của phân hệ này là chống thất lạc cơ hội (Mục 2.1, vấn đề 1). Một lịch hẹn quá hạn 10 ngày chưa xử lý không phải là việc "đã quá cũ để quan tâm" — đó chính xác là cơ hội đang bị bỏ rơi, tình huống rủi ro nhất mà quản lý cần biết nhất. Tự động im lặng đúng lúc rủi ro cao nhất khiến một nhân viên đi công tác hoặc nghỉ ốm một tuần quay lại là mất sạch dấu vết nhắc nhở. Ngưỡng leo thang là một lựa chọn vận hành khác nhau giữa các doanh nghiệp nên phải là tham số cấu hình theo Không gian làm việc (Phụ lục B, `CFG-DEAL-06`), không phải hằng số hệ thống.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-16.2.1` | Lịch chăm sóc đến đúng hạn | Tiến trình quét chạy | Người phụ trách nhận thông báo trong ứng dụng VÀ có một Công việc mới trong danh sách việc cần làm |
| `AC-16.3.1` | Hai lượt quét chạy gần như đồng thời cho cùng một lịch chăm sóc | Cả hai lượt quét xử lý | Chỉ có đúng một thông báo và một Công việc được tạo, không trùng lặp |
| `AC-16.4.1` | Lịch chăm sóc đã quá hạn vượt ngưỡng leo thang, người phụ trách chưa xử lý | Tiến trình quét chạy | Quản lý trực tiếp của người phụ trách nhận được thông báo leo thang; cơ hội không biến mất khỏi danh sách cần theo dõi |
| `AC-16.4.2` | Cùng bối cảnh AC-16.4.1 | Quản lý mở danh sách cơ hội của phòng ban | Cơ hội có lịch chăm sóc quá hạn vượt ngưỡng hiển thị dấu hiệu leo thang, lọc riêng được |

---

#### FEAT-17 — Nhận diện Cơ hội Nguội Lạnh

**Mô tả nghiệp vụ:** Tự động phát hiện và đánh dấu các cơ hội đang mở nhưng không có bất kỳ tương tác nào trong một khoảng thời gian dài, để người phụ trách và quản lý kịp thời can thiệp trước khi cơ hội nguội tắt hẳn.

**Quy tắc nghiệp vụ:**

- **`BR-17.1` (Ngưỡng nguội lạnh):** Một cơ hội đang mở không có tương tác nào vượt quá một ngưỡng số ngày thì được đánh dấu là nguội lạnh. Ngưỡng này là tham số cấu hình theo Không gian làm việc, đề xuất mặc định **14 ngày** (Phụ lục B, `CFG-DEAL-04`).

- **`BR-17.2` (Hành vi công nhận là "có tương tác"):** Các hành vi sau làm mới thời điểm tương tác gần nhất và gỡ cờ nguội lạnh nếu đang có: ghi nhận cuộc gọi/cuộc họp/email trên cơ hội, chuyển giai đoạn, nhận phản hồi mới từ khách hàng qua kênh liên lạc. **Việc chỉnh sửa các trường thông tin nội bộ (ví dụ đổi thẻ phân loại, sửa mô tả nội bộ) không được tính là tương tác** — quy tắc này khác với hành vi làm mới thời điểm tương tác gần nhất khi cập nhật cơ hội nói chung, vốn hiện coi mọi lần sửa là một tương tác.

  **Lý do nghiệp vụ:** Nếu bất kỳ thao tác sửa nào (kể cả một quản trị viên sửa lại mô tả nội bộ vì lỗi chính tả) cũng được tính là "cơ hội đang được chăm sóc", cơ chế cảnh báo nguội lạnh sẽ mất hoàn toàn ý nghĩa cảnh báo — một cơ hội có thể bị bỏ quên thật sự về mặt bán hàng nhưng vẫn liên tục được "làm mới" bởi các thao tác quản trị không liên quan tới khách hàng.

- **`BR-17.3` (Hiển thị):** Cơ hội nguội lạnh hiển thị dấu hiệu cảnh báo trực quan trên thẻ Kanban, kèm số ngày không có tương tác.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-17.1.1` | Cơ hội đang mở, không tương tác 16 ngày, ngưỡng cấu hình 14 ngày | Mở Kanban | Thẻ hiển thị cảnh báo nguội lạnh kèm "16 ngày không hoạt động" |
| `AC-17.2.1` | Cơ hội đang bị đánh dấu nguội lạnh | Ghi nhận một cuộc gọi mới trên cơ hội | Cờ nguội lạnh biến mất ngay lập tức |
| `AC-17.2.2` | Cơ hội đang bị đánh dấu nguội lạnh | Quản trị viên chỉ sửa thẻ phân loại nội bộ, không liên hệ khách hàng | Cờ nguội lạnh **không** biến mất |


---

### Nhóm F — Thắng/Thua

#### FEAT-18 — Đóng Cơ hội Thành công

**Mô tả nghiệp vụ:** Khi thương vụ chốt thành công, chuyển cơ hội sang trạng thái Thắng.

**Quy tắc nghiệp vụ:**

- **`BR-18.1` (Điều kiện đóng thắng):** Bắt buộc giá trị cơ hội lớn hơn 0, có ngày dự kiến đóng, và có đúng một liên hệ chính được xác định rõ (một Khách hàng cá nhân liên kết trực tiếp, hoặc một Vai trò liên hệ được đánh dấu là liên hệ chính — xem `FEAT-22`).

- **`BR-18.2` (Bắt buộc có người phụ trách khi đóng thắng) [tham số cấu hình theo Không gian làm việc]:** Mặc định, hệ thống yêu cầu cơ hội phải có người phụ trách trước khi được phép đóng thắng. Đây là tham số cấu hình theo Không gian làm việc (Phụ lục B, `CFG-DEAL-05`), mặc định **bật**.

  **Lý do nghiệp vụ:** Một doanh nghiệp coi việc đóng thắng một cơ hội không ai phụ trách là dấu hiệu dữ liệu bất thường cần chặn lại để buộc gán người trước. Doanh nghiệp khác có quy trình cho phép cơ hội "Chưa phân công" được xử lý và đóng bởi bất kỳ ai trong hàng đợi chung, nên cần tắt ràng buộc này.

- **`BR-18.3` (Kết quả sau khi đóng thắng):** Ghi nhận thời điểm thắng và chuyển cơ hội sang giai đoạn Thắng đã cấu hình của phễu. Giai đoạn Thắng luôn mang xác suất **100%** (`BR-06.2b`) — một hợp đồng đã chốt không còn là một khả năng để ước lượng, nên đây là hằng số nghiệp vụ, không phải một lựa chọn cấu hình.

  **Lý do nghiệp vụ:** Nếu cho phép giai đoạn Thắng mang xác suất khác 100%, cơ hội đã thắng sẽ tiếp tục được nhân trọng số trong dự báo doanh thu (`BR-25.1`) — tức doanh thu đã chốt bị tính thiếu một cách có hệ thống. Điều này cũng tạo ra cách xử lý bất đối xứng vô lý với nhánh thua, vốn bị loại hẳn khỏi dự báo (`BR-19.2`).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-18.1.1` | Cơ hội chưa có ngày dự kiến đóng | Cố gắng đóng thắng | Từ chối, báo thiếu ngày dự kiến đóng |
| `AC-18.2.1` | Cơ hội "Chưa phân công", tham số `CFG-DEAL-05` đang bật (mặc định) | Cố gắng đóng thắng | Từ chối, yêu cầu gán người phụ trách trước |
| `AC-18.2.2` | Quản trị viên tắt `CFG-DEAL-05` | Đóng thắng cơ hội "Chưa phân công" | Cho phép |
| `AC-18.3.1` | Cơ hội đủ điều kiện, giai đoạn Thắng có xác suất cấu hình 100% | Đóng thắng | Ghi nhận thời điểm thắng, chuyển giai đoạn Thắng |

---

#### FEAT-19 — Đóng Cơ hội Thất bại & Bắt buộc Lý do

**Mô tả nghiệp vụ:** Khi giao dịch thất bại, bắt buộc khai báo lý do trước khi hoàn tất đóng.

**Quy tắc nghiệp vụ:**

- **`BR-19.1` (Bắt buộc khai báo lý do):** Bắt buộc nhập nội dung lý do thất bại. Ở phạm vi phát hành hiện tại, lý do được nhập dưới dạng văn bản tự do — chưa có danh mục chuẩn hóa để chọn (xem `FEAT-20`).

- **`BR-19.2` (Kết quả sau khi đóng thua):** Ghi nhận thời điểm thua, chuyển sang giai đoạn Thua đã cấu hình, cơ hội bị loại khỏi mọi tính toán dự báo doanh thu.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-19.1.1` | Đóng cơ hội thất bại | Không nhập lý do | Từ chối |
| `AC-19.2.1` | Đóng cơ hội thất bại thành công | Mở báo cáo dự báo doanh thu | Cơ hội đã đóng thua không xuất hiện trong dự báo |

---

#### FEAT-20 — Danh mục Lý do Thất bại Chuẩn hóa

**Mô tả nghiệp vụ:** Cho phép Quản trị viên định nghĩa một danh mục lý do thất bại chuẩn hóa để nhân viên chọn khi đóng thua, thay vì nhập văn bản tự do, phục vụ phân tích nguyên nhân thất bại có cấu trúc.

**Quy tắc nghiệp vụ:**

- **`BR-20.1` (Danh mục do tenant định nghĩa, có ràng buộc toàn vẹn tối thiểu):** Quản trị viên tạo, sửa, vô hiệu hóa các lý do thất bại chuẩn (ví dụ "Giá cao hơn ngân sách", "Chọn đối thủ cạnh tranh", "Hết ngân sách", "Không có nhu cầu"). Cùng nguyên tắc với danh mục vai trò liên hệ (`BR-22.1`): một lý do **đang được sử dụng** trên ít nhất một cơ hội đã đóng thua không được xóa hẳn — chỉ được vô hiệu hóa, giữ nguyên trên các bản ghi lịch sử đã gắn.

- **`BR-20.2` (Bắt buộc chọn từ danh mục khi đóng thua):** Khi danh mục đã được cấu hình và có ít nhất một lý do đang hoạt động, đóng thua bắt buộc chọn từ danh mục thay vì nhập tự do; có thể kèm ghi chú giải thích bổ sung.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-20.1.1` | Quản trị viên tạo danh mục lý do thất bại | Thêm lý do "Giá cao hơn ngân sách" | Lưu thành công, xuất hiện trong danh sách chọn khi đóng thua |
| `AC-20.2.1` | Danh mục đã có lý do hoạt động | Đóng cơ hội thất bại | Bắt buộc chọn một lý do từ danh mục, không cho nhập tự do |


---

#### FEAT-21 — Tái phân loại Cơ hội Đã đóng

**Mô tả nghiệp vụ:** Cho phép người có thẩm quyền mở lại một cơ hội đã đóng để sửa kết quả (ví dụ đã đóng thua nhầm, thực ra vẫn còn cơ hội đàm phán lại; hoặc đã đóng thắng nhầm giao dịch).

**Quy tắc nghiệp vụ:**

- **`BR-21.1` (Quyền hạn tách biệt):** Tái phân loại một cơ hội đã đóng là một quyền hạn **riêng biệt**, không tự động đi kèm quyền sửa hoặc quyền chuyển giai đoạn cơ hội thông thường.

  **Lý do nghiệp vụ:** Xem Nguyên tắc 1 (Mục 2.4). Kết quả Thắng/Thua của một cơ hội đã đóng là căn cứ báo cáo doanh số đã chốt — cho phép mọi người có quyền sửa cơ hội thông thường tự ý đảo ngược kết quả sẽ làm dữ liệu báo cáo lịch sử không còn đáng tin.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-21.1.1` | Nhân viên kinh doanh có quyền sửa cơ hội nhưng không có quyền tái phân loại | Cố gắng mở lại cơ hội đã đóng | Từ chối |
| `AC-21.1.2` | Quản lý Kinh doanh có quyền tái phân loại | Mở lại cơ hội đã đóng thua | Cho phép, cơ hội quay lại trạng thái đang mở |

---

### Nhóm G — Vai trò Liên hệ & Nguồn gốc

#### FEAT-22 — Gắn Vai trò Liên hệ vào Cơ hội

**Mô tả nghiệp vụ:** Gắn nhiều nhân sự phía khách hàng vào một cơ hội với vai trò cụ thể trong quá trình ra quyết định mua hàng.

**Quy tắc nghiệp vụ:**

- **`BR-22.1` (Danh mục vai trò do tenant định nghĩa, có ràng buộc toàn vẹn tối thiểu):** Danh mục vai trò liên hệ (ví dụ Người ra quyết định, Người bảo trợ nội bộ, Người đánh giá kỹ thuật) do từng Không gian làm việc tự định nghĩa, thêm/sửa/vô hiệu hóa tùy nhu cầu — không phải danh sách cố định của hệ thống. Danh mục này dùng chung với vai trò liên hệ trên hồ sơ Khách hàng (xem [`contacts-srs.md`](./contacts-srs.md)). Một vai trò **đang được sử dụng** trên ít nhất một cơ hội không được xóa hẳn khỏi danh mục — chỉ được vô hiệu hóa (ẩn khỏi lựa chọn cho bản ghi mới, giữ nguyên trên các bản ghi đã gắn).

  **Lý do nghiệp vụ:** Nếu cho xóa cứng một vai trò đang gắn trên dữ liệu thật, các cơ hội đang tham chiếu vai trò đó sẽ mất dấu vết vai trò liên hệ đã khai báo, phá vỡ điều kiện đóng thắng đã ghi nhận trước đó tại `BR-18.1`.

- **`BR-22.2` (Một liên hệ chính duy nhất):** Mỗi cơ hội có tối đa một liên hệ được đánh dấu là liên hệ chính, dùng làm căn cứ xác định điều kiện đóng thắng tại `BR-18.1`.

- **`BR-22.3` (Vai trò liên hệ làm điều kiện rào cản giai đoạn):** Một giai đoạn có thể đặt điều kiện bắt buộc khai báo một vai trò liên hệ cụ thể trước khi cơ hội được chuyển vào — xem `BR-13.4`.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-22.1.1` | Quản trị viên định nghĩa vai trò mới "Người vận hành hệ thống" | Gắn vai trò này cho một liên hệ trên cơ hội | Lưu thành công |
| `AC-22.2.1` | Cơ hội đã có 1 liên hệ chính | Đánh dấu một liên hệ khác là liên hệ chính | Liên hệ cũ tự động mất trạng thái chính, chỉ còn 1 liên hệ chính |

---

#### FEAT-23 — Theo dõi Nguồn gốc Tiếp thị

**Mô tả nghiệp vụ:** Ghi nhận các tham số nguồn gốc tiếp thị trên cơ hội để đo lường hiệu quả kênh.

**Quy tắc nghiệp vụ:**

- **`BR-23.1` (Tham số nguồn gốc):** Cơ hội lưu các tham số nguồn gốc chiến dịch tiếp thị đã đưa khách hàng tới hệ thống, dùng để lọc và nhóm trong báo cáo nguồn gốc (`FEAT-26`).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-23.1.1` | Khách hàng tiềm năng đến từ một chiến dịch quảng cáo có gắn tham số nguồn | Chuyển đổi thành cơ hội | Tham số nguồn gốc được kế thừa sang cơ hội, lọc được trong báo cáo |

---

#### FEAT-24 — Phân chia Doanh số Đồng phụ trách

**Mô tả nghiệp vụ:** Cho phép chia tỷ lệ ghi nhận doanh số giữa nhiều nhân viên cùng tham gia chốt một hợp đồng lớn.

**Quy tắc nghiệp vụ:**

- **`BR-24.1` (Tổng tỷ lệ bằng 100%):** Tổng tỷ lệ phân chia giữa các nhân viên tham gia một cơ hội phải bằng chính xác 100%. Mặc định khi chưa phân chia: người phụ trách nhận 100%.

- **`BR-24.2` (Chốt tỷ lệ khi đóng thắng):** Tỷ lệ phân chia tại thời điểm cơ hội được đóng thắng được **chốt cứng**. Thay đổi người tham gia hay tỷ lệ sau đó không làm thay đổi doanh số đã ghi nhận, trừ khi cơ hội được tái phân loại theo `FEAT-21`.

  **Lý do nghiệp vụ:** Tỷ lệ phân chia là căn cứ tính hoa hồng. Nếu sửa được tự do sau khi đã chốt, doanh số của một kỳ đã khóa sổ có thể bị viết lại — tranh chấp hoa hồng không có căn cứ phân xử.

- **`BR-24.3` (Người tham gia rời tổ chức):** Khi một người có tỷ lệ phân chia rời khỏi Không gian làm việc, phần doanh số đã ghi nhận cho họ **ở các cơ hội đã đóng thắng được giữ nguyên** phục vụ báo cáo lịch sử. Với các cơ hội **đang mở**, tỷ lệ của họ được chuyển cho người phụ trách và được ghi dấu vết.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-24.1.1` | Cơ hội có 2 người tham gia | Đặt tỷ lệ 70%/30% | Lưu thành công |
| `AC-24.1.2` | Cơ hội có 2 người tham gia | Đặt tỷ lệ 70%/20% (tổng 90%) | Từ chối, báo tổng tỷ lệ phải bằng 100% |
| `AC-24.1.3` | Cơ hội mới tạo, chưa phân chia | Xem thông tin phân chia doanh số | Người phụ trách nhận 100% |
| `AC-24.2.1` | Cơ hội chia 70%/30% cho A và B, đã đóng thắng | Quản lý đổi tỷ lệ thành 50%/50% | Từ chối — tỷ lệ đã chốt khi đóng thắng, không sửa được |
| `AC-24.3.1` | Nhân viên B có 30% trên một cơ hội đã thắng và 20% trên một cơ hội đang mở | B rời khỏi Không gian làm việc | Cơ hội đã thắng giữ nguyên 30% cho B trong báo cáo lịch sử; cơ hội đang mở chuyển 20% đó sang người phụ trách, có dấu vết |


---

### Nhóm H — Dự báo Doanh thu

#### FEAT-25 — Dự báo Doanh thu có Trọng số

**Mô tả nghiệp vụ:** Tính doanh thu kỳ vọng từ các cơ hội đang mở, theo giá trị cơ hội nhân với xác suất thắng của giai đoạn hiện tại.

**Quy tắc nghiệp vụ:**

- **`BR-25.1` (Công thức):** Doanh thu dự báo của một giai đoạn bằng tổng của (giá trị từng cơ hội đang ở giai đoạn đó nhân với xác suất thắng của giai đoạn đó). Doanh thu dự báo toàn phễu là tổng doanh thu dự báo của tất cả các giai đoạn đang mở.

- **`BR-25.2` (Không quy đổi tiền tệ):** Xem `BR-02.2` — báo cáo này chịu cùng giới hạn về trộn lẫn tiền tệ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-25.1.1` | Giai đoạn "Đàm phán" (xác suất 60%) có 2 cơ hội trị giá 100 và 200 | Mở báo cáo dự báo | Doanh thu dự báo của giai đoạn = 60% × (100 + 200) = 180 |

---

#### FEAT-26 — Báo cáo Hiệu suất Người phụ trách & Nguồn

**Mô tả nghiệp vụ:** Báo cáo tổng hợp hiệu suất chốt hợp đồng theo từng người phụ trách và theo nguồn gốc tiếp thị.

**Quy tắc nghiệp vụ:**

- **`BR-26.1` (Báo cáo theo người phụ trách):** Tổng hợp số cơ hội, tổng giá trị và tỷ lệ thắng theo từng người phụ trách trong một khoảng thời gian.

- **`BR-26.2` (Báo cáo theo nguồn gốc):** Tổng hợp số cơ hội và tổng giá trị theo nguồn gốc tiếp thị (`FEAT-23`).

- **`BR-26.3` (Báo cáo tuổi cơ hội):** Phân nhóm cơ hội đang mở theo khoảng số ngày đã tồn tại thành bốn nhóm liền kề không chồng lấn (ví dụ nhóm "mới", "trong tháng", "trong quý", "tồn đọng lâu"), giúp nhận diện cơ hội tồn đọng lâu. Ranh giới cụ thể giữa các nhóm là chi tiết triển khai, không phải quy tắc nghiệp vụ ràng buộc — điều quan trọng về nghiệp vụ là có đúng bốn nhóm liền kề và nhóm cuối cùng luôn là "trên 90 ngày" để đánh dấu rủi ro tồn đọng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-26.1.1` | Trong kỳ báo cáo, nhân viên A có 5 cơ hội đã đóng: 3 Thắng, 2 Thua (không còn cơ hội đang mở nào) | Mở báo cáo hiệu suất người phụ trách | Tỷ lệ thắng của A là 60% (3 chia 5 cơ hội đã đóng) |
| `AC-26.1.2` | Nhân viên A có 3 cơ hội Thắng, 2 Thua và 4 cơ hội **đang mở** trong kỳ | Mở báo cáo hiệu suất | Tỷ lệ thắng vẫn là 60% — cơ hội đang mở không tính vào mẫu số |
| `AC-26.2.1` | Có cơ hội đến từ hai nguồn tiếp thị khác nhau | Mở báo cáo theo nguồn gốc | Mỗi nguồn hiển thị đúng số lượng và tổng giá trị cơ hội thuộc nguồn đó |
| `AC-26.3.1` | Có cơ hội đang mở ở các mốc tuổi khác nhau, trong đó có cơ hội tồn đọng hơn 90 ngày | Mở báo cáo tuổi cơ hội | Hiển thị đúng bốn nhóm liền kề không chồng lấn; mỗi cơ hội đếm đúng một lần; nhóm cuối gồm các cơ hội trên 90 ngày |

---

#### FEAT-27 — Hạn ngạch Doanh số

**Mô tả nghiệp vụ:** Thiết lập mục tiêu doanh số theo kỳ cho từng nhân viên hoặc phòng ban, theo dõi tỷ lệ hoàn thành.

**Quy tắc nghiệp vụ:**

- **`BR-27.1` (Mục tiêu theo kỳ):** Quản lý Kinh doanh hoặc Giám đốc Kinh doanh đặt một mục tiêu doanh số cho một nhân viên hoặc phòng ban trong một kỳ (tháng/quý) xác định. Mỗi nhân viên/phòng ban chỉ có đúng một mục tiêu cho một kỳ — không cho đặt trùng.

- **`BR-27.2` (Căn cứ tính hoàn thành):** Tỷ lệ hoàn thành tính trên **giá trị các cơ hội đã đóng thắng có thời điểm thắng rơi trong kỳ**, quy đổi về đồng tiền cơ sở (`BR-02.2`) và phân bổ theo tỷ lệ chia doanh số (`FEAT-24`). Cơ hội đang mở không tính vào số đã đạt.

- **`BR-27.3` (Độ phủ phễu):** Bên cạnh tỷ lệ hoàn thành, báo cáo hiển thị **độ phủ phễu** — tỷ lệ giữa tổng giá trị các cơ hội **đang mở có ngày dự kiến đóng trong kỳ** so với phần mục tiêu còn thiếu.

  **Lý do nghiệp vụ:** Tỷ lệ hoàn thành chỉ nói về quá khứ — biết đã đạt 40% giữa kỳ không cho biết có kịp về đích hay không. Độ phủ phễu là chỉ số điều hành: phần còn thiếu có đủ cơ hội đang mở để lấp hay không. Thiếu chỉ số này, quản lý chỉ phát hiện hụt số khi kỳ đã kết thúc.

- **`BR-27.4` (Đổi phòng ban giữa kỳ):** Khi một nhân viên đổi phòng ban giữa kỳ, doanh số đã chốt trước thời điểm chuyển **vẫn thuộc về phòng ban cũ**; doanh số chốt sau đó thuộc phòng ban mới.

  **Lý do nghiệp vụ:** Nếu toàn bộ doanh số chuyển theo người, báo cáo đã công bố của phòng cũ tự thay đổi hồi tố — trưởng phòng cũ mất thành tích đã đạt, trưởng phòng mới được cộng thành tích không phải của mình.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-27.1.1` | Quản lý đặt mục tiêu quý cho nhân viên A là 500 triệu | Nhân viên A đóng thắng các cơ hội tổng 300 triệu trong quý | Báo cáo hiển thị tỷ lệ hoàn thành 60% |
| `AC-27.1.2` | Nhân viên A đã có mục tiêu cho quý này | Quản lý cố gắng đặt thêm một mục tiêu khác cho cùng nhân viên A, cùng quý | Từ chối, báo đã có mục tiêu cho kỳ này |
| `AC-27.1.3` | Nhân viên A có mục tiêu quý nhưng không đóng thắng cơ hội nào trong quý | Xem báo cáo hạn ngạch | Hiển thị tỷ lệ hoàn thành 0%, không lỗi hiển thị |
| `AC-27.2.1` | Nhân viên A đóng thắng một cơ hội 1 tỷ nhưng chỉ được chia 60% doanh số | Xem báo cáo hạn ngạch của A | Ghi nhận 600 triệu cho A, không phải 1 tỷ |
| `AC-27.2.2` | Nhân viên A có một cơ hội 500 triệu đang mở, chưa đóng | Xem báo cáo hạn ngạch | 500 triệu này không tính vào số đã đạt |
| `AC-27.3.1` | Mục tiêu quý 1 tỷ, đã đạt 400 triệu, còn 900 triệu cơ hội đang mở dự kiến đóng trong quý | Xem báo cáo hạn ngạch | Hiển thị độ phủ phễu 1,5 lần so với 600 triệu còn thiếu |
| `AC-27.4.1` | Nhân viên A thuộc phòng X, chốt 300 triệu, sau đó chuyển sang phòng Y và chốt thêm 200 triệu trong cùng kỳ | Xem báo cáo hạn ngạch theo phòng ban | Phòng X ghi nhận 300 triệu, phòng Y ghi nhận 200 triệu |


---

### Nhóm I — Thao tác Hàng loạt & Nhập/Xuất Dữ liệu

#### FEAT-28 — Cập nhật, Gắn thẻ & Xóa Hàng loạt

**Mô tả nghiệp vụ:** Chọn nhiều cơ hội cùng lúc để chuyển giai đoạn, đổi người phụ trách, gắn thẻ phân loại hoặc xóa.

**Quy tắc nghiệp vụ:**

- **`BR-28.1` (Bỏ qua bản ghi không đủ quyền, không chặn toàn bộ):** Kiểm tra phân quyền trên từng cơ hội trong lô. Các bản ghi không đủ quyền được đưa vào danh sách bị bỏ qua kèm lý do, không làm gián đoạn xử lý các bản ghi còn lại.

- **`BR-28.2` (Loại trừ cơ hội đã đóng khỏi chuyển giai đoạn hàng loạt):** Thao tác "chuyển giai đoạn hàng loạt" **không áp dụng** cho các cơ hội đã ở trạng thái Thắng hoặc Thua. Nếu lô được chọn có lẫn cơ hội đã đóng, các cơ hội đó được đưa vào danh sách bị bỏ qua kèm lý do, không được chuyển giai đoạn.

  **Lý do nghiệp vụ:** Nếu không có ràng buộc này, thao tác chuyển giai đoạn hàng loạt trở thành một đường vòng để đảo ngược kết quả Thắng/Thua của cơ hội đã đóng mà không cần đi qua quyền tái phân loại riêng biệt tại `BR-21.1` — vô hiệu hóa chính mục đích của quyền hạn tách biệt đó. Đổi người phụ trách hoặc gắn thẻ hàng loạt trên cơ hội đã đóng không có cùng rủi ro này nên vẫn được phép.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-28.1.1` | Chọn 10 cơ hội, người dùng chỉ có quyền sửa 8 trong số đó | Thực hiện đổi người phụ trách hàng loạt | 8 cơ hội cập nhật thành công, 2 cơ hội còn lại vào danh sách bị bỏ qua kèm lý do |
| `AC-28.2.1` | Chọn 5 cơ hội, trong đó 1 cơ hội đã Đóng Thắng | Thực hiện chuyển giai đoạn hàng loạt sang "Đàm phán" | 4 cơ hội chuyển giai đoạn thành công; cơ hội đã đóng vào danh sách bị bỏ qua kèm lý do, giữ nguyên trạng thái Thắng |
| `AC-28.2.2` | Cùng bối cảnh AC-28.2.1 | Thực hiện đổi người phụ trách hàng loạt (không phải chuyển giai đoạn) | Cả 5 cơ hội cập nhật người phụ trách thành công, kể cả cơ hội đã đóng |

---

#### FEAT-29 — Nhập / Xuất Dữ liệu Dung lượng lớn qua Hàng đợi

**Mô tả nghiệp vụ:** Nhập danh sách cơ hội từ tệp bảng tính, hoặc xuất dữ liệu cơ hội ra tệp tải về, xử lý qua hàng đợi nền cho tệp dung lượng lớn.

**Quy tắc nghiệp vụ:**

- **`BR-29.1` (Giới hạn dung lượng nhập):** Tệp nhập tối đa 50MB mỗi lần tải lên, là giới hạn vận hành hệ thống.

- **`BR-29.2` (Kiểm tra khi nhập):** Từ chối ánh xạ dữ liệu vào các trường được bảo vệ; phát hiện trùng lặp theo tên cơ hội; chặn nhập dữ liệu đè lên cơ hội đã ở giai đoạn đóng.

- **`BR-29.3` (Đường dẫn tải xuất có thời hạn):** Tệp xuất được cấp một đường dẫn tải về có thời hạn hiệu lực, sau đó tự động hết hạn.

- **`BR-29.4` (Quyền nhập/xuất tách biệt):** Thực hiện nhập hoặc xuất dữ liệu hàng loạt là một quyền hạn **riêng biệt**, không tự động đi kèm quyền xem hoặc sửa cơ hội thông thường.

  **Lý do nghiệp vụ:** Xuất dữ liệu hàng loạt ra một tệp tải về là điểm rò rỉ dữ liệu có rủi ro cao hơn hẳn việc xem từng cơ hội riêng lẻ trên giao diện — một người có quyền xem cơ hội trong phạm vi phòng ban không mặc nhiên được phép mang toàn bộ dữ liệu đó ra khỏi hệ thống dưới dạng tệp.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-29.1.1` | Tệp nhập 60MB | Tải lên | Từ chối ngay, báo vượt giới hạn dung lượng |
| `AC-29.2.1` | Tệp nhập có dòng ánh xạ vào cơ hội đã đóng thắng | Chạy nhập | Dòng đó bị từ chối, báo cáo lỗi chi tiết ghi rõ lý do |
| `AC-29.3.1` | Xuất dữ liệu xong, nhận đường dẫn tải về | Truy cập đường dẫn sau khi hết hạn | Từ chối truy cập |
| `AC-29.4.1` | Người dùng có quyền xem cơ hội trong phạm vi phòng ban nhưng không có quyền xuất dữ liệu | Cố gắng xuất danh sách cơ hội | Từ chối; chức năng xuất không khả dụng trên giao diện |

---

### Nhóm J — Dòng thời gian & Toàn vẹn Cấu trúc

#### FEAT-30 — Dòng thời gian Hoạt động Hợp nhất

**Mô tả nghiệp vụ:** Hiển thị toàn bộ lịch sử tương tác trên một cơ hội theo thứ tự thời gian.

**Quy tắc nghiệp vụ:**

- **`BR-30.1` (Nguồn hợp nhất):** Dòng thời gian gộp hoạt động ghi nhận thủ công, ghi chú, vé hỗ trợ liên quan, công việc liên quan, cuộc gọi, cuộc họp, email và lịch sử chuyển giai đoạn — theo đúng thứ tự thời gian xảy ra.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-30.1.1` | Cơ hội có cả ghi chú, 1 lần chuyển giai đoạn và 1 vé hỗ trợ liên kết | Mở dòng thời gian | Cả ba loại sự kiện hiển thị đúng thứ tự thời gian xảy ra |

---

#### FEAT-31 — Cảnh báo Vé Hỗ trợ Khẩn cấp trên Kanban

**Mô tả nghiệp vụ:** Khi một cơ hội có khách hàng đang gặp sự cố kỹ thuật nghiêm trọng chưa được giải quyết, cảnh báo ngay trên thẻ Kanban để nhân viên kinh doanh không tiếp tục đàm phán chốt hợp đồng mà bỏ qua rủi ro kỹ thuật đang mở.

**Quy tắc nghiệp vụ:**

- **`BR-31.1` (Điều kiện cảnh báo):** Nếu cơ hội có ít nhất một Vé hỗ trợ đang mở ở mức độ ưu tiên cao nhất, thẻ cơ hội trên Kanban hiển thị dấu hiệu cảnh báo trực quan.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-31.1.1` | Cơ hội có 1 Vé hỗ trợ đang mở ở mức ưu tiên cao nhất | Mở Kanban | Thẻ cơ hội hiển thị dấu hiệu cảnh báo |
| `AC-31.1.2` | Tiếp nối AC-31.1.1 | Vé hỗ trợ được đóng | Dấu hiệu cảnh báo biến mất khỏi thẻ |


---

#### FEAT-32 — Đóng băng Ghi dữ liệu khi Di chuyển Dữ liệu nền

**Mô tả nghiệp vụ:** Khi đội vận hành nền tảng chạy một tiến trình di chuyển dữ liệu quy mô lớn giữa các hệ thống, tạm thời chặn mọi thay đổi trên Cơ hội bán hàng để tránh xung đột ghi đè.

**Quy tắc nghiệp vụ:**

- **`BR-32.1` (Chặn ghi trong lúc đóng băng):** Khi một Không gian làm việc đang trong trạng thái đóng băng di chuyển dữ liệu, mọi yêu cầu sửa đổi cơ hội của người dùng bị từ chối với thông báo rõ ràng là hệ thống đang bảo trì dữ liệu, không phải lỗi.

  **Lý do nghiệp vụ:** Nếu người dùng vẫn sửa được dữ liệu trong lúc một tiến trình nền đang di chuyển hàng loạt bản ghi, thay đổi của người dùng có thể bị tiến trình nền ghi đè mất mà không có cảnh báo — một dạng mất dữ liệu âm thầm nguy hiểm hơn nhiều so với việc từ chối tạm thời và rõ ràng.

- **`BR-32.2` (Chiều ngược — luôn có lối gỡ):** Trạng thái đóng băng luôn là tạm thời và phải được đội vận hành nền tảng chủ động gỡ khi tiến trình di chuyển hoàn tất hoặc thất bại. Không tồn tại tình huống một Không gian làm việc bị kẹt vĩnh viễn ở trạng thái đóng băng — nếu tiến trình di chuyển thất bại giữa chừng, đội vận hành phải gỡ cờ đóng băng trước khi xử lý sự cố, không để người dùng bị chặn ghi dữ liệu vô thời hạn.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-32.1.1` | Không gian làm việc đang trong trạng thái đóng băng di chuyển dữ liệu | Người dùng cố gắng sửa một cơ hội | Từ chối, thông báo hệ thống đang bảo trì dữ liệu |
| `AC-32.1.2` | Không gian làm việc đang đóng băng | Người dùng chỉ **xem** danh sách cơ hội và Bảng Kanban | Vẫn xem được bình thường — đóng băng chỉ chặn ghi, không chặn đọc |
| `AC-32.2.1` | Tiến trình di chuyển dữ liệu kết thúc, đội vận hành gỡ cờ đóng băng | Người dùng sửa lại cơ hội | Lưu thành công như bình thường |

---

#### FEAT-33 — Chống Tạo Cơ hội Trùng khi Chuyển đổi

**Mô tả nghiệp vụ:** Khi chuyển đổi một khách hàng tiềm năng thành cơ hội bán hàng, tự động phát hiện nếu doanh nghiệp liên quan đã có sẵn một cơ hội đang mở trên cùng phễu, để tránh tạo cơ hội trùng làm phồng dự báo doanh thu.

**Quy tắc nghiệp vụ:**

- **`BR-33.1` (Gộp vào cơ hội có sẵn theo mặc định):** Nếu doanh nghiệp liên quan đã có một cơ hội đang mở trên cùng phễu, hệ thống mặc định gắn khách hàng tiềm năng vào cơ hội đó thay vì tạo mới.

- **`BR-33.2` (Cho phép chủ động tạo riêng):** Người thực hiện chuyển đổi có thể chủ động chọn vẫn tạo một cơ hội riêng biệt; quyết định này được ghi nhận lại.

  **Lý do nghiệp vụ:** Gộp mặc định chống được tình trạng một doanh nghiệp có nhiều cơ hội trùng lặp làm phồng dự báo doanh thu ảo, đồng thời vẫn giữ lối thoát cho các trường hợp hợp lệ cần cơ hội riêng (ví dụ hai dòng sản phẩm độc lập đàm phán song song).

- **`BR-33.3` (Chưa hỗ trợ tách lại sau khi đã gộp):** Ở phạm vi phát hành hiện tại, sau khi một khách hàng tiềm năng đã được gộp vào một cơ hội có sẵn theo `BR-33.1`, hệ thống **chưa có thao tác tách trở lại** thành một cơ hội riêng biệt nếu sau đó phát hiện việc gộp là không phù hợp. Đây là khoảng trống nghiệp vụ được ghi nhận, không phải quyết định có chủ đích — khác với `BR-07.4` (không hỗ trợ mở lại phễu đã đóng, vốn là quyết định có chủ đích). Nhu cầu tách lại (tương tự sổ cái hoàn tác gộp đã có cho Khách hàng/Doanh nghiệp — xem [`contacts-srs.md`](./contacts-srs.md)) được ghi nhận tại Mục 7.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-33.1.1` | Doanh nghiệp X đã có cơ hội đang mở trên Phễu A | Chuyển đổi một khách hàng tiềm năng mới thuộc Doanh nghiệp X vào Phễu A | Khách hàng tiềm năng được gắn vào cơ hội có sẵn, không tạo cơ hội mới |
| `AC-33.2.1` | Cùng bối cảnh AC-33.1.1 | Người thực hiện chủ động chọn "vẫn tạo cơ hội riêng" | Tạo một cơ hội mới độc lập, quyết định được ghi nhận |

---

#### FEAT-34 — Khóa Đồng thời khi Sửa Cấu hình Phễu

**Mô tả nghiệp vụ:** Khi hai quản trị viên cùng mở và sửa cấu hình một phễu bán hàng cùng lúc, ngăn người sửa sau ghi đè âm thầm lên thay đổi của người sửa trước.

**Quy tắc nghiệp vụ:**

- **`BR-34.1` (Phát hiện xung đột):** Mỗi lần lưu cấu hình phễu mang theo phiên bản dữ liệu đã đọc trước đó. Nếu phiên bản đó không còn khớp với phiên bản mới nhất (đã có người khác sửa trong lúc chờ), hệ thống từ chối lưu và báo rõ ai đã sửa gần nhất.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-34.1.1` | Quản trị viên A và B cùng mở cấu hình một phễu. A lưu thay đổi trước | B cố gắng lưu thay đổi của mình dựa trên phiên bản cũ | Từ chối, báo rõ A đã sửa gần nhất, yêu cầu B tải lại trước khi sửa tiếp |

---

### Nhóm K — Cộng tác, Bàn giao & Vòng đời Mở rộng

#### FEAT-35 — Người Theo dõi Cơ hội

**Mô tả nghiệp vụ:** Cho phép thêm nhân sự từ phòng ban khác (tư vấn giải pháp, kỹ thuật, pháp chế, tài chính) vào một cơ hội cụ thể để họ theo dõi và phối hợp, mà không cần mở rộng phạm vi dữ liệu của họ ra toàn bộ cơ hội khác.

**Quy tắc nghiệp vụ:**

- **`BR-35.1` (Quyền xem theo từng cơ hội):** Người được thêm làm Người theo dõi nhìn thấy **đúng cơ hội đó**, kể cả khi cơ hội nằm ngoài phạm vi dữ liệu thông thường của họ. Việc này không mở rộng phạm vi của họ sang bất kỳ cơ hội nào khác.

  **Lý do nghiệp vụ:** Một thương vụ lớn luôn cần nhiều bộ phận cùng tham gia. Nếu cách duy nhất để họ xem được là nới phạm vi dữ liệu, doanh nghiệp buộc phải chọn giữa cản trở công việc và mở toang dữ liệu bán hàng cho người không cần biết — cả hai đều sai. Quyền theo từng bản ghi giải quyết đúng nhu cầu mà không phá phạm vi.

- **`BR-35.2` (Quyền hạn giới hạn):** Người theo dõi **xem** hồ sơ, **thêm ghi chú** và **nhận thông báo** về cơ hội. Họ **không** được chuyển giai đoạn, không sửa giá trị, không đóng cơ hội và không xóa. Việc họ có xem được số liệu tài chính hay không vẫn tuân theo `FEAT-03` — thêm làm Người theo dõi không tự động cấp quyền xem giá trị.

- **`BR-35.3` (Không thay thế trách nhiệm):** Cơ hội vẫn chỉ có đúng một Người phụ trách (Nguyên tắc 3). Người theo dõi không phải người chịu trách nhiệm và không xuất hiện trong báo cáo hiệu suất với tư cách người chốt.

- **`BR-35.4` (Dấu vết):** Mỗi lượt thêm hoặc gỡ Người theo dõi đều được ghi lại kèm người thực hiện và thời điểm.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-35.1.1` | Nhân viên kỹ thuật K có phạm vi dữ liệu Chỉ của mình, không phụ trách cơ hội nào | Người phụ trách thêm K làm Người theo dõi một cơ hội | K nhìn thấy đúng cơ hội đó trong danh sách của mình |
| `AC-35.1.2` | Tiếp nối AC-35.1.1 | K mở danh sách cơ hội | K chỉ thấy cơ hội được thêm, không thấy cơ hội khác cùng phòng ban của người phụ trách |
| `AC-35.2.1` | K là Người theo dõi một cơ hội | K thử kéo thẻ sang giai đoạn khác | Từ chối; thao tác chuyển giai đoạn không khả dụng với K |
| `AC-35.2.2` | K là Người theo dõi, không có quyền xem đầy đủ dữ liệu tài chính | K mở hồ sơ cơ hội | Giá trị vẫn bị ẩn theo `BR-03.1` |
| `AC-35.2.3` | K là Người theo dõi | K thêm một ghi chú nội bộ | Lưu thành công, ghi chú hiện trên dòng thời gian |
| `AC-35.3.1` | Cơ hội có 1 Người phụ trách và 3 Người theo dõi, đã đóng thắng | Mở báo cáo hiệu suất người phụ trách | Chỉ Người phụ trách được ghi nhận, 3 Người theo dõi không xuất hiện |

---

#### FEAT-36 — Chuyển Cơ hội sang Phễu khác

**Mô tả nghiệp vụ:** Cho phép chuyển một cơ hội đang mở sang một phễu bán hàng khác khi nhận ra phễu ban đầu không phù hợp, mà không phải xóa đi tạo lại.

**Quy tắc nghiệp vụ:**

- **`BR-36.1` (Chỉ định giai đoạn đích):** Người thực hiện bắt buộc chọn giai đoạn cụ thể trong phễu mới để cơ hội chuyển tới. Hệ thống không tự đoán giai đoạn tương đương.

  **Lý do nghiệp vụ:** Hai phễu khác nhau có số giai đoạn và ý nghĩa giai đoạn khác nhau; đoán sai sẽ đẩy một cơ hội mới khảo sát vào thẳng giai đoạn đàm phán, làm sai lệch dự báo doanh thu ngay lập tức.

- **`BR-36.2` (Giữ nguyên lịch sử):** Toàn bộ lịch sử giai đoạn ở phễu cũ được giữ nguyên, không bị xóa hay viết lại. Lần chuyển phễu được ghi vào lịch sử như một sự kiện riêng, nêu rõ phễu cũ, phễu mới và lý do.

- **`BR-36.3` (Áp dụng rào cản của phễu mới):** Cơ hội phải thỏa rào cản giai đoạn (`FEAT-13`) của giai đoạn đích ở phễu mới. Nếu chưa đủ điều kiện, hệ thống từ chối và liệt kê phần còn thiếu.

- **`BR-36.4` (Chỉ áp dụng cho cơ hội đang mở):** Cơ hội đã đóng không được chuyển phễu, vì điều đó viết lại bối cảnh của một kết quả đã chốt.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-36.1.1` | Cơ hội đang ở Phễu SMB, giai đoạn "Báo giá" | Chuyển sang Phễu Enterprise, chọn giai đoạn "Khảo sát nhu cầu" | Chuyển thành công, cơ hội nằm đúng giai đoạn đã chọn |
| `AC-36.1.2` | Cùng bối cảnh | Chuyển phễu mà không chọn giai đoạn đích | Từ chối, yêu cầu chọn giai đoạn |
| `AC-36.2.1` | Tiếp nối AC-36.1.1 | Mở lịch sử giai đoạn của cơ hội | Thấy đầy đủ các bước đã đi ở Phễu SMB, cộng một dòng ghi nhận việc chuyển sang Phễu Enterprise kèm lý do |
| `AC-36.3.1` | Giai đoạn đích ở phễu mới bắt buộc có Ngày dự kiến đóng; cơ hội chưa có | Chuyển phễu | Từ chối, báo thiếu Ngày dự kiến đóng |
| `AC-36.4.1` | Cơ hội đã đóng thắng | Chuyển sang phễu khác | Từ chối |

---

#### FEAT-37 — Bàn giao Cơ hội khi Thay đổi Nhân sự

**Mô tả nghiệp vụ:** Chuyển toàn bộ cơ hội của một nhân viên sang người khác khi nhân viên đó nghỉ việc, chuyển bộ phận hoặc nghỉ dài ngày.

**Quy tắc nghiệp vụ:**

- **`BR-37.1` (Bàn giao hàng loạt theo người):** Quản lý hoặc Quản trị viên chọn một người phụ trách và chuyển toàn bộ cơ hội **đang mở** của người đó sang một hoặc nhiều người kế nhiệm trong một thao tác, bắt buộc ghi lý do.

- **`BR-37.2` (Cơ hội đã đóng giữ nguyên người phụ trách):** Cơ hội **đã đóng** không đổi người phụ trách khi bàn giao, và **giữ nguyên đơn vị tổ chức tại thời điểm đóng** — đây là ngoại lệ có chủ đích của Nguyên tắc 4.

  **Lý do nghiệp vụ:** Người phụ trách trên một cơ hội đã thắng là căn cứ tính hoa hồng và là thành tích đã ghi nhận của người đó lẫn phòng ban họ khi đó. Chuyển nó theo người kế nhiệm sẽ viết lại lịch sử doanh số: người đã nghỉ mất thành tích, người kế nhiệm được cộng doanh số chưa từng làm, và báo cáo quý đã công bố tự thay đổi hồi tố.

- **`BR-37.3` (Không để cơ hội mất người phụ trách âm thầm):** Khi một nhân viên bị gỡ khỏi Không gian làm việc mà chưa bàn giao, các cơ hội đang mở của họ chuyển sang trạng thái **Chưa phân công**, **vẫn giữ nguyên đơn vị tổ chức cũ** để cấp quản lý của đơn vị đó tiếp tục nhìn thấy, và hệ thống thông báo cho quản lý trực tiếp để xử lý.

  **Lý do nghiệp vụ:** Nếu cơ hội chưa phân công rơi ra khỏi phạm vi phòng ban, trưởng phòng mất dấu toàn bộ cơ hội của nhân viên vừa nghỉ — đúng lúc cần bàn giao gấp nhất. Giữ đơn vị tổ chức cũ là cách duy nhất để chúng không biến mất khỏi tầm quản lý.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-37.1.1` | Nhân viên A đang phụ trách 40 cơ hội đang mở và 15 cơ hội đã đóng | Quản lý bàn giao toàn bộ cơ hội của A sang nhân viên B, nhập lý do | 40 cơ hội đang mở chuyển sang B; 15 cơ hội đã đóng vẫn ghi A là người phụ trách |
| `AC-37.2.1` | Tiếp nối AC-37.1.1, A thuộc phòng X, B thuộc phòng Y | Mở báo cáo doanh số quý trước của phòng X | Doanh số từ 15 cơ hội đã đóng của A vẫn thuộc phòng X, không chuyển sang phòng Y |
| `AC-37.3.1` | Nhân viên A thuộc phòng X bị gỡ khỏi Không gian làm việc mà chưa bàn giao | Tiến trình hệ thống xử lý | Cơ hội đang mở của A chuyển sang Chưa phân công, vẫn hiển thị trong danh sách của phòng X; quản lý phòng X nhận thông báo |

---

#### FEAT-38 — Tái mở Cơ hội đã Thua

**Mô tả nghiệp vụ:** Khi một khách hàng từng thua quay lại có nhu cầu, tạo một cơ hội mới có liên kết tới cơ hội thua trước đó, thay vì sửa lại kết quả cũ.

**Quy tắc nghiệp vụ:**

- **`BR-38.1` (Tạo mới, không sửa cũ):** Nghiệp vụ này **tạo một cơ hội mới**, giữ nguyên cơ hội thua cũ cùng lý do thất bại của nó. Cơ hội mới mang liên kết tham chiếu tới cơ hội cũ.

  **Lý do nghiệp vụ:** Khách quay lại sau khi thua là một lần bán mới, không phải việc sửa sai một kết quả cũ. Dùng đường tái phân loại (`FEAT-21`) sẽ xóa mất kết quả thua khỏi lịch sử — làm hỏng tỷ lệ thắng và làm mất dữ liệu phân tích nguyên nhân thất bại, vốn là lý do tồn tại của `FEAT-19`.

- **`BR-38.2` (Kế thừa bối cảnh):** Cơ hội mới kế thừa khách hàng, doanh nghiệp và vai trò liên hệ từ cơ hội cũ để người bán không phải nhập lại, nhưng **không kế thừa** giá trị, giai đoạn hay ngày dự kiến đóng.

- **`BR-38.3` (Hiển thị quan hệ hai chiều):** Cả hai cơ hội đều hiển thị liên kết tới nhau, để người bán thấy được lịch sử đã từng thua vì lý do gì trước khi tiếp cận lại.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-38.1.1` | Cơ hội X đã đóng thua với lý do "Giá cao hơn ngân sách" | Người bán tái mở cơ hội từ X | Tạo cơ hội mới Y đang mở; X vẫn ở trạng thái thua, giữ nguyên lý do thất bại |
| `AC-38.1.2` | Tiếp nối AC-38.1.1 | Mở báo cáo hiệu suất của kỳ chứa X | X vẫn được tính là một cơ hội thua trong tỷ lệ thắng của kỳ đó |
| `AC-38.2.1` | Tiếp nối AC-38.1.1 | Xem cơ hội Y vừa tạo | Y đã có sẵn khách hàng, doanh nghiệp và vai trò liên hệ của X; giá trị và ngày dự kiến đóng để trống |
| `AC-38.3.1` | Tiếp nối AC-38.1.1 | Mở hồ sơ cơ hội X | Thấy liên kết tới cơ hội Y; mở Y cũng thấy liên kết ngược về X kèm lý do thua cũ |

---

## 4. Yêu cầu phi chức năng

### 4.1 Hiệu năng

- **NFR-01 (Tốc độ Bảng Kanban):** Tải tổng hợp và tải danh sách thẻ trên Kanban phản hồi trong thời gian ngắn kể cả khi một phễu có hàng chục nghìn cơ hội.
- **NFR-02 (Ổn định khi cuộn sâu):** Hiệu năng cuộn tải thêm thẻ trên Kanban không suy giảm khi người dùng đã cuộn qua nhiều trang.

### 4.2 Độ tin cậy & Toàn vẹn Dữ liệu

- **NFR-03 (Bất biến của lịch sử giai đoạn):** Bản ghi lịch sử chuyển giai đoạn không thể bị chỉnh sửa hoặc xóa (trong giới hạn số bản ghi tại `BR-12.1`).
- **NFR-04 (Toàn vẹn khi di chuyển phễu):** Thao tác đóng phễu kèm di chuyển hàng loạt cơ hội phải thực thi như một đơn vị nguyên tử duy nhất — xem `BR-07.3`.
- **NFR-05 (Toàn vẹn khi xóa vĩnh viễn):** Thao tác xóa vĩnh viễn một cơ hội cùng dữ liệu liên quan tại `BR-04.3` phải thực thi trong một giao dịch nguyên tử duy nhất.

### 4.3 An toàn & Bảo mật

- **NFR-06 (Phân quyền dữ liệu & che số liệu tài chính):** Áp dụng nghiêm ngặt phạm vi dữ liệu (`BR-01.5`) và che số liệu tài chính (`FEAT-03`). Người dùng không có quyền không thể xem hoặc kéo thả cơ hội ngoài phạm vi.
- **NFR-07 (Bảo mật đường dẫn xuất dữ liệu):** Đường dẫn tải tệp xuất được bảo vệ và tự động hết hiệu lực sau thời hạn quy định (`BR-29.3`).

---

## 5. Ma trận quyền truy cập tính năng

| Mã FEAT | Tên tính năng nghiệp vụ | Nhân viên KD | Quản lý KD | Giám đốc KD | Quản trị viên | Chủ sở hữu | Tiến trình Hệ thống |
| --- | --- | :---: | :---: | :---: | :---: | :---: | :---: |
| `FEAT-01` | Tạo & Quản lý Cơ hội | Chỉ của mình | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | Đồng bộ tên tự động |
| `FEAT-02` | Đa Tiền tệ | Chỉ của mình | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-03` | Xem số liệu tài chính đầy đủ | Cần quyền riêng | Cần quyền riêng | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-04` | Thùng rác & Phục hồi | — | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | Dọn dẹp tự động |
| `FEAT-05` | Quản trị Phễu | Xem danh sách | Xem danh sách | Xem danh sách | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-06` | Cấu hình Giai đoạn & Xác suất | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-07` | Đóng Phễu & Di chuyển | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-08` | Xóa Giai đoạn | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-09` | Xem Bảng Kanban | Chỉ của mình | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-10` | Kéo thả Chuyển Giai đoạn | Chỉ của mình | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-11` | Bộ lọc Kanban | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | — |
| `FEAT-12` | Xem Lịch sử Giai đoạn | Chỉ của mình | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | Ghi tự động |
| `FEAT-13` | Cấu hình Rào cản Giai đoạn | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-14` | Báo cáo Vận tốc & Điểm nghẽn | — | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-15` | Đặt Lịch Chăm sóc | Chỉ của mình | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-16` | Nhận Nhắc nhở Lịch Chăm sóc | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | Quét & tạo tự động |
| `FEAT-18` | Đóng Cơ hội Thắng | Chỉ của mình | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-19` | Đóng Cơ hội Thua | Chỉ của mình | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-21` | Tái phân loại Cơ hội Đã đóng | — | Cần quyền riêng | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-22` | Gắn Vai trò Liên hệ | Chỉ của mình | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-23` | Xem Nguồn gốc Tiếp thị | Chỉ của mình | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-25` | Xem Dự báo Doanh thu | — | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-26` | Báo cáo Hiệu suất & Nguồn | — | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-28` | Thao tác Hàng loạt | — | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-29` | Nhập / Xuất Dữ liệu | — | Cần quyền riêng | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-30` | Dòng thời gian Hợp nhất | Chỉ của mình | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-32` | Đóng băng Di chuyển Dữ liệu nền | — | — | — | — | — | **Toàn quyền** |
| `FEAT-33` | Chuyển đổi Khách hàng Tiềm năng | Chỉ của mình | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-34` | Sửa Cấu hình Phễu (khóa đồng thời) | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-35` | Thêm/gỡ Người Theo dõi Cơ hội | Chỉ của mình | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-36` | Chuyển Cơ hội sang Phễu khác | Chỉ của mình | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-37` | Bàn giao Cơ hội khi Thay đổi Nhân sự | — | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | Gỡ người phụ trách tự động |
| `FEAT-38` | Tái mở Cơ hội đã Thua | Chỉ của mình | Đơn vị của mình | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |

*(Ghi chú: "Chỉ của mình" = chỉ cơ hội do chính mình phụ trách; "Đơn vị của mình" = cơ hội của mọi người trong đơn vị tổ chức mình quản lý, gồm cả cấp dưới trực tiếp và gián tiếp — tên gọi các mức phạm vi theo `iam-tenant-authorization.md`; "Cần quyền riêng" = quyền hạn tách biệt khỏi quyền xem/sửa dữ liệu thông thường, phải cấp riêng — xem lý do nghiệp vụ tại `BR-01.2`, `BR-03.1`, `BR-21.1`. Cột "Tiến trình Hệ thống" chỉ có giá trị khi vai trò người dùng hoàn toàn không thao tác trực tiếp (`FEAT-32`) hoặc khi Tiến trình Hệ thống thực hiện một phần việc tự động song song với thao tác của người dùng (`FEAT-04`, `FEAT-12`, `FEAT-16`) — không phải một cột loại trừ 5 cột còn lại. Người Theo dõi Cơ hội (`FEAT-35`) là một trục quyền riêng theo từng bản ghi, cộng thêm vào phạm vi dữ liệu chứ không thay thế nó.)*

---

## 6. Kịch bản chấp nhận tổng hợp (UAT)

### Kịch bản 1: Tạo Cơ hội, Kéo thả Kanban tới Đóng Thắng

1. Nhân viên kinh doanh tạo cơ hội "Hợp đồng Bản quyền CRM", gắn Doanh nghiệp "Công ty Đại Phát", đặt ngày dự kiến đóng.
2. Kéo thả cơ hội qua các giai đoạn: Tiếp cận → Trình bày giải pháp → Đàm phán, mỗi lần chuyển đều đủ điều kiện rào cản giai đoạn.
3. Đóng thắng cơ hội ở giai đoạn cuối.
4. **Kỳ vọng:** Cơ hội chuyển sang trạng thái Thắng, ghi nhận thời điểm thắng, lịch sử giai đoạn đầy đủ 3 lần chuyển.

---

### Kịch bản 2: Chặn Chuyển Giai đoạn khi Thiếu Trường Bắt buộc

1. Quản trị viên cấu hình giai đoạn "Báo giá" bắt buộc phải có Ngày dự kiến đóng.
2. Nhân viên kéo một cơ hội chưa có Ngày dự kiến đóng vào giai đoạn "Báo giá".
3. **Kỳ vọng:** Hệ thống chặn thao tác, giữ nguyên vị trí cũ, báo rõ thiếu Ngày dự kiến đóng.

---

### Kịch bản 3: Đóng Cơ hội Thất bại, Bắt buộc Khai báo Lý do

1. Nhân viên kéo một cơ hội vào cột "Đóng Thua".
2. Hộp thoại bắt buộc nhập lý do thất bại xuất hiện.
3. Nhân viên nhập lý do, xác nhận.
4. **Kỳ vọng:** Cơ hội chuyển sang Thua, loại khỏi dự báo doanh thu.

---

### Kịch bản 4: Đóng Phễu, Di chuyển Cơ hội theo Ma trận Ánh xạ

1. Quản trị viên đóng Phễu A, còn 10 cơ hội đang mở.
2. Thiết lập ma trận ánh xạ từng giai đoạn của Phễu A sang giai đoạn tương ứng của Phễu B.
3. Xác nhận đóng.
4. **Kỳ vọng:** 10 cơ hội chuyển đúng sang giai đoạn tương ứng ở Phễu B, Phễu A đóng, không mất dữ liệu nào.

---

### Kịch bản 5: Nhắc nhở Lịch Chăm sóc Tự động Tạo Công việc

1. Nhân viên đặt lịch chăm sóc tiếp theo cho một cơ hội vào 9:00 sáng mai.
2. Đến đúng 9:00 sáng hôm sau, tiến trình quét phát hiện lịch hẹn đến hạn.
3. **Kỳ vọng:** Nhân viên nhận thông báo trong ứng dụng, đồng thời có một Công việc mới xuất hiện trong danh sách việc cần làm.

---

### Kịch bản 6: Xóa Giai đoạn, Chuyển Cơ hội sang Giai đoạn Đích

1. Quản trị viên xóa giai đoạn "Khảo sát" đang có 5 cơ hội, chỉ định giai đoạn đích "Báo giá".
2. **Kỳ vọng:** 5 cơ hội chuyển sang "Báo giá", giai đoạn "Khảo sát" bị xóa, không cơ hội nào bị mất dấu.

---

### Kịch bản 7: Chuyển đổi Khách hàng Tiềm năng, Chống Tạo Cơ hội Trùng

1. Doanh nghiệp X đã có một cơ hội đang mở trên Phễu A.
2. Nhân viên chuyển đổi một khách hàng tiềm năng mới thuộc Doanh nghiệp X, chọn Phễu A.
3. **Kỳ vọng:** Hệ thống gợi ý cơ hội có sẵn, mặc định gắn khách hàng tiềm năng vào cơ hội đó thay vì tạo mới; nhân viên vẫn có lựa chọn tạo riêng nếu chủ động xác nhận.

---

### Kịch bản 8: Đóng băng Ghi dữ liệu trong lúc Di chuyển Dữ liệu nền

1. Đội vận hành nền tảng khởi động một tiến trình di chuyển dữ liệu quy mô lớn cho một Không gian làm việc.
2. Trong lúc tiến trình đang chạy, một nhân viên cố gắng sửa một cơ hội.
3. **Kỳ vọng:** Yêu cầu sửa bị từ chối với thông báo rõ ràng hệ thống đang bảo trì dữ liệu, không phải lỗi từ phía người dùng.

---

## 7. Nhu cầu nghiệp vụ chưa chốt được phương án

Mục này chỉ chứa các nhu cầu **chưa quyết định được điều gì là đúng về mặt nghiệp vụ**. Những yêu cầu đã chốt phương án đều nằm trong Mục 3 dưới dạng `FEAT`/`BR` bắt buộc, kể cả khi phần triển khai chưa theo kịp.

1. **Bảng giá & Chi tiết Dòng sản phẩm (CPQ).** Hiện một cơ hội chỉ mang một giá trị tổng. Nhu cầu tách theo từng sản phẩm/dịch vụ, số lượng, đơn giá, chiết khấu và thuế là có thật, nhưng kéo theo một loạt quyết định chưa ngã ngũ: chiết khấu tính theo từng dòng hay theo tổng đơn, quy tắc làm tròn thuế, và bảng giá có phiên bản theo thời gian hay không. Chưa đủ cơ sở để đặc tả.

2. **Quy trình Phê duyệt Chiết khấu Đa cấp.** Phụ thuộc trực tiếp vào mục 1 — chưa có cơ cấu chiết khấu chi tiết thì chưa xác định được đối tượng cần phê duyệt là gì. Hoãn tới khi CPQ được chốt.

3. **Trạng thái Tạm ngưng Cơ hội (On Hold).** Khách hàng hoãn kế hoạch mua ngoài ý muốn là tình huống thật và phổ biến. Chưa quyết định được: tạm ngưng là **nhánh trạng thái thứ ba** bên cạnh đang mở và đã đóng, hay chỉ là **một cờ** trên cơ hội đang mở. Lựa chọn này chi phối hàng loạt quy tắc khác — cơ hội tạm ngưng có bị tính vào dự báo doanh thu không, có bị cảnh báo nguội lạnh (`FEAT-17`) không, có nằm trong mẫu số tỷ lệ thắng (`BR-26.1`) không — nên phải chốt trước khi viết đặc tả.

   *Rủi ro của việc chưa có:* nhân viên buộc phải đóng thua các cơ hội chỉ đang bị hoãn, làm tỷ lệ thắng và phân tích nguyên nhân thất bại sai lệch có hệ thống.

4. **Nhóm Dự báo theo mức độ tin cậy (Forecast Categories).** Nhu cầu phân loại cơ hội theo mức độ chắc chắn ("cam kết chốt trong kỳ" so với "có triển vọng") tách biệt khỏi xác suất theo giai đoạn. Chưa quyết định: ai là người gán nhãn (người bán tự khai hay quản lý thẩm định), và nhãn đó thay thế hay bổ sung cho xác suất giai đoạn trong công thức dự báo (`BR-25.1`).

5. **So sánh dự báo giữa các thời điểm.** Câu hỏi thường trực trong họp phễu hàng tuần là "tuần trước dự báo 8 tỷ, nay còn 6,5 tỷ — phần nào rơi ra". Trả lời được câu này đòi hỏi lưu lại ảnh chụp dự báo theo từng mốc thời gian và phân loại biến động (cơ hội mới vào, đẩy sang kỳ sau, thắng, thua). Chưa quyết định tần suất chụp ảnh và thời hạn lưu.

6. **Thanh toán và ghi nhận doanh thu theo nhiều đợt.** Một hợp đồng chia thành nhiều đợt nghiệm thu đặt ra câu hỏi doanh thu rơi vào kỳ dự báo nào. Hiện một cơ hội chỉ có một ngày dự kiến đóng nên toàn bộ giá trị dồn vào một kỳ. Chưa quyết định: tách thành nhiều cơ hội con, hay giữ một cơ hội với nhiều mốc ghi nhận doanh thu.

7. **Tách lại cơ hội sau khi đã tự động gộp khi chuyển đổi (`BR-33.3`).** Chưa quyết định có cần một cơ chế hoàn tác đầy đủ (tương tự sổ cái hoàn tác gộp của hồ sơ Khách hàng) hay chỉ cần hướng dẫn thao tác thủ công.

---

## 8. Phụ lục A — Danh mục Khái niệm Nghiệp vụ

> Phụ lục này mô tả khái niệm nghiệp vụ ánh xạ sang tên gọi thường dùng trong hệ thống, **không phải thiết kế dữ liệu và không mang tính ràng buộc**. Cách tổ chức lưu trữ thực tế thuộc thẩm quyền đội phát triển.

| Khái niệm nghiệp vụ | Mô tả |
| --- | --- |
| **Cơ hội Bán hàng** | Bản ghi giao dịch tiềm năng trung tâm: tên, phễu, giai đoạn, giá trị, tiền tệ, ngày dự kiến đóng, người phụ trách, đơn vị tổ chức, liên hệ/doanh nghiệp liên quan, kết quả đóng (Thắng/Thua) và thời điểm tương ứng. |
| **Phễu Bán hàng** | Danh sách các giai đoạn tuần tự, có đúng một phễu mặc định trong mỗi Không gian làm việc. |
| **Giai đoạn Phễu** | Một bước trong phễu: tên, xác suất thắng, thứ tự, danh sách trường dữ liệu bắt buộc, và (tùy chọn) là giai đoạn Thắng hoặc Thua. |
| **Lịch sử Giai đoạn** | Danh sách bất biến (giới hạn 100 bản ghi gần nhất) ghi lại mỗi lần chuyển giai đoạn: giai đoạn cũ, giai đoạn mới, thời điểm, người thực hiện, thời gian đã lưu ở giai đoạn cũ. |
| **Vai trò Liên hệ trên Cơ hội** | Danh sách liên hệ gắn vào cơ hội kèm vai trò và cờ đánh dấu liên hệ chính. |
| **Danh mục Vai trò Liên hệ** | Danh mục do tenant tự định nghĩa, dùng chung giữa Cơ hội và hồ sơ Khách hàng. |
| **Đóng băng Di chuyển Dữ liệu nền** | Cờ theo Không gian làm việc, khi bật sẽ chặn mọi yêu cầu sửa cơ hội của người dùng. |
| **Khóa Đồng thời Cấu hình Phễu** | Số hiệu phiên bản đi kèm mỗi lần lưu cấu hình phễu, dùng để phát hiện xung đột ghi đè giữa hai người sửa cùng lúc. |

---

## 9. Phụ lục B — Danh mục Tham số Cấu hình theo Không gian làm việc

| Mã tham số | Quy tắc liên quan | Nội dung cấu hình | Mặc định chuẩn hệ thống | Miền giá trị | Thẩm quyền thay đổi | Mức độ tự do |
| --- | --- | --- | --- | --- | --- | --- |
| `CFG-DEAL-01` | `BR-04.2` | Thời hạn lưu cơ hội trong thùng rác trước khi xóa vĩnh viễn | 30 ngày | 30–90 ngày | Chủ sở hữu Không gian làm việc | **Có sàn bắt buộc** — không được đặt dưới 30 ngày |
| `CFG-DEAL-02` | `BR-06.4` | Bật/tắt khóa nhảy cóc giai đoạn (theo từng phễu) | Tắt | Bật / Tắt | Quản trị viên Không gian làm việc | **Tự do** |
| `CFG-DEAL-03` | `BR-15.1` | Khoảng thời gian mặc định tới lịch chăm sóc tiếp theo khi tạo cơ hội mới | 24 giờ | 1–168 giờ | Quản lý Kinh doanh | **Tự do** |
| `CFG-DEAL-04` | `BR-17.1` | Ngưỡng số ngày không tương tác để đánh dấu Cơ hội Nguội Lạnh | 14 ngày | 1–90 ngày | Quản lý Kinh doanh | **Tự do** |
| `CFG-DEAL-05` | `BR-18.2` | Bắt buộc có người phụ trách trước khi cho phép đóng thắng | Bật | Bật / Tắt | Quản trị viên Không gian làm việc | **Tự do** |
| `CFG-DEAL-06` | `BR-16.4` | Ngưỡng số ngày quá hạn lịch chăm sóc trước khi báo leo thang lên quản lý | 7 ngày | 1–30 ngày | Quản lý Kinh doanh | **Tự do** |
| `CFG-DEAL-07` | `BR-15.1` | Tự động đặt lịch chăm sóc mặc định khi tạo cơ hội mới | Tắt *(không tự đặt)* | Bật / Tắt | Quản lý Kinh doanh | **Tự do** |
| `CFG-DEAL-08` | `BR-02.2` | Đồng tiền cơ sở dùng để hợp nhất mọi báo cáo tổng hợp | Theo quốc gia đăng ký của doanh nghiệp | Một mã tiền tệ chuẩn quốc tế | Chủ sở hữu Không gian làm việc | **Cố định sau khi có dữ liệu** — xem ghi chú |

*Ghi chú về thẩm quyền:* Thẩm quyền đổi tham số là **một trục quyền riêng**, không suy ra được từ ma trận phân quyền trên dữ liệu nghiệp vụ tại Mục 5. Các tham số điều chỉnh **nhịp độ vận hành bán hàng** (`CFG-DEAL-03`, `04`, `06`, `07`) thuộc thẩm quyền Quản lý Kinh doanh vì đây là lựa chọn điều hành đội thường nhật. Các tham số **ràng buộc toàn vẹn dữ liệu hoặc quy trình** (`CFG-DEAL-02`, `05`) thuộc Quản trị viên. Hai tham số chạm tới dữ liệu tài chính và khả năng mất dữ liệu (`CFG-DEAL-01`, `08`) thuộc Chủ sở hữu.

*Ghi chú về `CFG-DEAL-01`:* sàn 30 ngày là thời gian tối thiểu để một doanh nghiệp phát hiện và khôi phục việc xóa nhầm. Cho phép hạ thấp hơn sẽ biến thùng rác thành một cơ chế hình thức không cứu được dữ liệu thật.

*Ghi chú về `CFG-DEAL-08`:* đồng tiền cơ sở **không được đổi** một khi Không gian làm việc đã có cơ hội đã đóng, vì mọi giá trị lịch sử đã được chốt quy đổi theo đồng tiền đó (`BR-02.3`); đổi đồng tiền cơ sở sẽ khiến toàn bộ báo cáo doanh số lịch sử mất ý nghĩa. Việc đổi chỉ khả thi khi không gian làm việc chưa phát sinh cơ hội đã đóng nào.

*Ghi chú về hằng số vận hành:* Chu kỳ quét lịch chăm sóc (`BR-16.1`, 5 phút), giới hạn dung lượng nhập (`BR-29.1`, 50MB), giới hạn số bản ghi thao tác hàng loạt và thời hạn hiệu lực đường dẫn tải xuất (`BR-29.3`) là **hằng số vận hành cấp hệ thống**, áp dụng thống nhất cho mọi Không gian làm việc — không đưa vào bảng tham số theo tenant vì đây là giới hạn kỹ thuật bảo vệ hạ tầng dùng chung, không phải một lựa chọn nghiệp vụ khác nhau giữa các doanh nghiệp.
