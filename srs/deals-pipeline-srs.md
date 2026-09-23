# SRS — Phân hệ Quản lý Cơ hội & Phễu Bán hàng (Deals & Pipelines Management)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA (Version 6.0) |
| **Module** | CRM — Phân hệ Quản lý Cơ hội & Phễu Bán hàng (Deals & Pipelines Management) |
| **Ngày cập nhật** | 2026-09-23 |
| **Phiên bản** | v6.0 (Chuẩn hóa Nghiệp vụ Thuần túy — Thay thế toàn bộ v5.1, đối chiếu lại với mã nguồn thực tế) |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`contacts-srs.md`](./contacts-srs.md), [`tickets-srs.md`](./tickets-srs.md), [`tasks-srs.md`](./tasks-srs.md), [`campaigns-srs.md`](./campaigns-srs.md), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`object-manager-srs.md`](./object-manager-srs.md) |

## Ghi chú về phiên bản v6.0

Phiên bản này **viết lại toàn bộ** tài liệu theo đúng vai trò của một SRS nghiệp vụ, thay thế v5.1. Ba thay đổi về bản chất:

1. **Loại bỏ hoàn toàn ngôn ngữ kỹ thuật khỏi phần thân.** Phiên bản v5.1 đặc tả quy tắc bằng tên trường dữ liệu, tên class/service, công thức toán học và tên hạ tầng. Toàn bộ quy tắc nay được phát biểu bằng ngôn ngữ người dùng nghiệp vụ. Ánh xạ sang tên trường dữ liệu được gom riêng tại Phụ lục A và **không phải là nội dung ràng buộc**.

2. **Nhãn trạng thái được kiểm chứng lại với mã nguồn thực tế**, không kế thừa nhãn của v5.1. v5.1 tự nhận "đã qua 10 vòng review chéo độc lập, đạt 100% chuẩn nghiệp vụ sẵn sàng bàn giao" — nhãn đó **không đáng tin**: đối chiếu trực tiếp với mã nguồn tại `crm-api` cho thấy ít nhất bảy tính năng bị gắn sai nhãn theo cả hai chiều. Ba tính năng v5.1 ghi `[Yêu cầu mới]` (chưa làm) nhưng thực ra **đã vận hành**: Phân tích Vận tốc & Điểm nghẽn (FEAT-17), Dự báo Doanh thu có Trọng số (FEAT-28). Bốn tính năng v5.1 ghi `[Đã triển khai]` nhưng thực ra **chưa hề tồn tại trong hệ thống**: Nhận diện Cơ hội Nguội Lạnh (trước đây FEAT-20), Danh mục Lý do Thất bại & Đối thủ cạnh tranh (trước đây FEAT-24), Hạn ngạch Doanh số (trước đây một phần FEAT-30), và cảnh báo Vé hỗ trợ khẩn cấp trên Kanban (trước đây một phần FEAT-33). Ngoài ra, cam kết "giao dịch nguyên tử duy nhất" khi di chuyển phễu (v5.1 NFR-04) không đúng — thao tác này chạy hai bước tuần tự không bọc trong cùng một giao dịch.

3. **Bổ sung các cơ chế đang vận hành thật nhưng v5.1 chưa từng nhắc tới**: đóng băng ghi dữ liệu trong lúc di chuyển dữ liệu nền, chống tạo cơ hội trùng khi chuyển đổi từ khách hàng tiềm năng, dọn dẹp thùng rác có cascade rõ ràng theo từng loại dữ liệu liên quan, tự động gỡ người phụ trách khi nhân sự rời tổ chức, khóa nhảy cóc giai đoạn, khóa đồng thời khi hai quản trị viên cùng sửa cấu hình phễu, và cờ đánh dấu bản ghi thiếu dữ liệu bắt buộc do giới hạn phân quyền trường.

**Chi tiết đổi nhãn và mã FEAT so với v5.1:** v5.1 liệt kê 36 mã nhưng bốn mã (Line Items/CPQ, Tạm ngưng Cơ hội, Chiết khấu Đa cấp, một phần Forecast Categories) là nhu cầu chưa đủ chín muồi để đặc tả AC nên được rút gọn về ghi nhận tại Mục 7 thay vì giữ mã FEAT riêng. Đổi nhãn bảy tính năng theo bằng chứng đối chiếu mã nguồn 2026-09-23: FEAT-14 (trước đây FEAT-17) và FEAT-25 (trước đây FEAT-28) chuyển từ `[Yêu cầu mới]` sang `[Đã triển khai]`; FEAT-17 (trước đây FEAT-20), FEAT-20 (trước đây FEAT-24), FEAT-27 (trước đây một phần FEAT-30) và FEAT-31 (trước đây một phần BR-33.2) chuyển từ `[Đã triển khai]` sang `[Yêu cầu mới]`. Bổ sung bốn tính năng đang vận hành thật nhưng v5.1 chưa từng đặc tả: FEAT-21 (Tái phân loại Cơ hội Đã đóng), FEAT-32 (Đóng băng khi Di chuyển Dữ liệu nền), FEAT-33 (Chống Tạo Trùng khi Chuyển đổi), FEAT-34 (Khóa Đồng thời Cấu hình Phễu). Vòng review chéo độc lập sau khi viết xong v6.0 (2026-09-23) phát hiện thêm: `BR-06.2` của bản nháp đầu tiên khẳng định hệ thống từ chối lưu phễu thiếu giai đoạn Thắng/Thua — đối chiếu code cho thấy ràng buộc này **chưa được thực thi**, nên tách thành `BR-06.2` (ràng buộc có thật: một giai đoạn không được vừa Thắng vừa Thua) và `BR-06.2b` (`[Yêu cầu mới]`, ràng buộc đủ hai giai đoạn kết thúc).

**Nguyên tắc biên soạn:** Tài liệu mô tả **trạng thái nghiệp vụ mục tiêu (To-Be)** cho các tính năng gắn `[Yêu cầu mới]`, và **hành vi thực tế đang vận hành** cho các tính năng gắn `[Đã triển khai]`. Nơi nào hệ thống hiện tại đang làm khác đặc tả của một tính năng `[Đã triển khai]`, phần triển khai phải được sửa để tuân theo tài liệu này. Tài liệu **không đưa bảng đối chiếu sai lệch mã nguồn** (tên tệp, số dòng) — việc rà soát khoảng cách triển khai thuộc về backlog kỹ thuật riêng.

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

Tài liệu bao gồm 10 nhóm chức năng:

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

> **Ghi chú:** Sáu vai trò trên tạo thành các cột của Ma trận phân quyền tại Mục 5. Vai trò chức năng và cách phân giải quyền chi tiết thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md).

### 2.3 Quy ước thời gian nghiệp vụ

Toàn bộ mốc thời gian nghiệp vụ trong tài liệu này — *cơ hội nguội lạnh*, *quá hạn lịch chăm sóc*, *giờ chạy tiến trình nền* — được xác định theo **múi giờ cấu hình của Không gian làm việc**, không theo múi giờ máy chủ và không theo múi giờ của từng người dùng, nhất quán với quy ước đã áp dụng ở [`tasks-srs.md`](./tasks-srs.md#23-quy-ước-thời-gian-nghiệp-vụ).

**Lý do nghiệp vụ:** Một lịch chăm sóc "9 giờ sáng mai" phải quá hạn vào cùng một thời điểm đối với mọi thành viên trong cùng đội bán hàng. Nếu tính theo múi giờ cá nhân, báo cáo của Quản lý Kinh doanh sẽ không đối chiếu được giữa các nhân viên.

### 2.4 Nguyên tắc nghiệp vụ nền tảng

**Nguyên tắc 1 — Trạng thái đóng của cơ hội có hai nhánh loại trừ lẫn nhau.**
Một cơ hội đang mở kết thúc theo đúng một trong hai nhánh: **Thắng** (chốt thành công) hoặc **Thua** (không chốt được). Không có trạng thái đóng thứ ba lẫn lộn giữa hai nhánh này. Việc mở lại một cơ hội đã đóng để tái phân loại (từ Thắng sang Thua hoặc ngược lại) là một nghiệp vụ tách biệt, có quyền hạn riêng, không phải thao tác sửa đổi hằng ngày (`BR-01.6`).

**Nguyên tắc 2 — Rào cản giai đoạn bảo vệ chất lượng dữ liệu, không bảo vệ quy trình phê duyệt.**
Rào cản giai đoạn (Mục 3, `FEAT-13`) chỉ đảm bảo các trường dữ liệu quan trọng đã được điền trước khi cơ hội được coi là đủ điều kiện ở một giai đoạn. Đây không phải là cơ chế phê duyệt có người ký duyệt — nhu cầu đó (ví dụ phê duyệt chiết khấu) là một nghiệp vụ khác, chưa được thiết kế (xem Mục 7).

**Nguyên tắc 3 — Không quá một người chịu trách nhiệm.**
Một cơ hội bán hàng không bao giờ có nhiều hơn một người phụ trách cùng lúc. Nhu cầu chia sẻ doanh số giữa nhiều người cùng tham gia được ghi nhận nhưng chưa có thiết kế chi tiết (xem Mục 7).

**Nguyên tắc 4 — Đơn vị tổ chức của cơ hội đi theo người phụ trách, không theo người tạo.**
Một cơ hội phải hiện diện trong phạm vi quản lý của người thực sự chịu trách nhiệm xử lý nó. Khi người phụ trách thay đổi, đơn vị tổ chức của cơ hội chuyển theo (`BR-01.2`).

**Nguyên tắc 5 — Thay đổi cấu trúc phễu không được làm mất dấu dữ liệu đang mở.**
Đóng một phễu hoặc xóa một giai đoạn không bao giờ khiến các cơ hội đang mở biến mất khỏi tầm nhìn quản lý; mọi thay đổi cấu trúc phễu đều bắt buộc chỉ định điểm đến cho dữ liệu hiện có (`FEAT-08`, `FEAT-09`).

### 2.5 Bảng tổng hợp tính năng nghiệp vụ

| Nhóm | Mã | Tên tính năng nghiệp vụ | Trạng thái |
| --- | --- | --- | :---: |
| **A. Quản trị Cơ hội Bán hàng** | `FEAT-01` | Tạo mới & Quản lý Cơ hội Bán hàng | `[Đã triển khai]` |
| | `FEAT-02` | Quản lý Đa Tiền tệ | `[Đã triển khai]` |
| | `FEAT-03` | Bảo vệ Dữ liệu Tài chính Nhạy cảm | `[Đã triển khai]` |
| | `FEAT-04` | Thùng rác & Phục hồi Cơ hội | `[Đã triển khai]` |
| **B. Phễu Bán hàng & Giai đoạn** | `FEAT-05` | Quản trị Nhiều Phễu Bán hàng Độc lập | `[Đã triển khai]` |
| | `FEAT-06` | Thiết lập Giai đoạn, Xác suất Thắng & Thứ tự | `[Đã triển khai]` |
| | `FEAT-07` | Đóng Phễu An toàn & Di chuyển Cơ hội | `[Đã triển khai]` |
| | `FEAT-08` | Xóa Giai đoạn An toàn | `[Đã triển khai]` |
| **C. Bảng Kanban Cơ hội** | `FEAT-09` | Bảng Kanban Tổng hợp Giá trị Thời gian thực | `[Đã triển khai]` |
| | `FEAT-10` | Kéo thả Chuyển Giai đoạn | `[Đã triển khai]` |
| | `FEAT-11` | Bộ lọc trên Bảng Kanban | `[Đã triển khai]` |
| **D. Lịch sử Giai đoạn & Vận tốc** | `FEAT-12` | Ghi nhận Lịch sử Chuyển Giai đoạn | `[Đã triển khai]` |
| | `FEAT-13` | Rào cản Điều kiện Chuyển Giai đoạn | `[Đã triển khai]` |
| | `FEAT-14` | Phân tích Vận tốc Bán hàng & Điểm nghẽn | `[Đã triển khai]` |
| **E. Nhắc nhở & Cảnh báo Nguội** | `FEAT-15` | Thiết lập Lịch Chăm sóc Tiếp theo | `[Đã triển khai]` |
| | `FEAT-16` | Quét & Nhắc nhở Lịch Chăm sóc Đến hạn | `[Đã triển khai]` |
| | `FEAT-17` | Nhận diện Cơ hội Nguội Lạnh | `[Yêu cầu mới]` |
| **F. Thắng/Thua** | `FEAT-18` | Đóng Cơ hội Thành công | `[Đã triển khai]` |
| | `FEAT-19` | Đóng Cơ hội Thất bại & Bắt buộc Lý do | `[Đã triển khai]` |
| | `FEAT-20` | Danh mục Lý do Thất bại Chuẩn hóa | `[Yêu cầu mới]` |
| | `FEAT-21` | Tái phân loại Cơ hội Đã đóng | `[Đã triển khai]` |
| **G. Vai trò Liên hệ & Nguồn gốc** | `FEAT-22` | Gắn Vai trò Liên hệ vào Cơ hội | `[Đã triển khai]` |
| | `FEAT-23` | Theo dõi Nguồn gốc Tiếp thị | `[Đã triển khai]` |
| | `FEAT-24` | Phân chia Doanh số Đồng phụ trách | `[Yêu cầu mới]` |
| **H. Dự báo Doanh thu** | `FEAT-25` | Dự báo Doanh thu có Trọng số | `[Đã triển khai]` |
| | `FEAT-26` | Báo cáo Hiệu suất Người phụ trách & Nguồn | `[Đã triển khai]` |
| | `FEAT-27` | Hạn ngạch Doanh số | `[Yêu cầu mới]` |
| **I. Thao tác Hàng loạt & Nhập/Xuất** | `FEAT-28` | Cập nhật, Gắn thẻ & Xóa Hàng loạt | `[Đã triển khai]` |
| | `FEAT-29` | Nhập / Xuất Dữ liệu Dung lượng lớn qua Hàng đợi | `[Đã triển khai]` |
| **J. Dòng thời gian & Toàn vẹn Cấu trúc** | `FEAT-30` | Dòng thời gian Hoạt động Hợp nhất | `[Đã triển khai]` |
| | `FEAT-31` | Cảnh báo Vé Hỗ trợ Khẩn cấp trên Kanban | `[Yêu cầu mới]` |
| | `FEAT-32` | Đóng băng Ghi dữ liệu khi Di chuyển Dữ liệu nền | `[Đã triển khai]` |
| | `FEAT-33` | Chống Tạo Cơ hội Trùng khi Chuyển đổi | `[Đã triển khai]` |
| | `FEAT-34` | Khóa Đồng thời khi Sửa Cấu hình Phễu | `[Đã triển khai]` |

**Tổng kết phạm vi:** 34 tính năng — **28 đã triển khai**, **6 thuộc nhu cầu nghiệp vụ mới**. Chi tiết đổi nhãn so với v5.1 xem "Ghi chú về phiên bản v6.0" ở đầu tài liệu.

---

## 3. Đặc tả yêu cầu chức năng

### Nhóm A — Quản trị Cơ hội Bán hàng

#### FEAT-01 — Tạo mới & Quản lý Cơ hội Bán hàng `[Đã triển khai]`

**Mô tả nghiệp vụ:** Nhân viên kinh doanh tạo, xem, sửa cơ hội bán hàng, liên kết với khách hàng cá nhân và/hoặc doanh nghiệp.

**Quy tắc nghiệp vụ:**

- **`BR-01.1` (Thông tin tối thiểu):** Bắt buộc có Tên cơ hội, Phễu bán hàng và Giai đoạn khởi đầu.

- **`BR-01.2` (Người phụ trách & đơn vị tổ chức):** Người tạo mặc định được gán làm người phụ trách, trừ khi người tạo có quyền chỉ định người khác — việc chỉ định người phụ trách khác với chính mình là một quyền hạn tách biệt khỏi quyền tạo/sửa cơ hội thông thường. Đơn vị tổ chức của cơ hội luôn theo **người phụ trách hiện tại**, không theo người tạo.

  **Lý do nghiệp vụ:** Nếu đơn vị tổ chức lấy theo người tạo, khi Quản lý phòng A giao việc cho nhân viên phòng B, cơ hội sẽ nằm trong phạm vi phòng A — Trưởng phòng B không nhìn thấy cơ hội mà nhân viên mình đang xử lý. Tách quyền "chỉ định người phụ trách khác mình" khỏi quyền sửa thông thường để một nhân viên bình thường không thể tự đẩy trách nhiệm cơ hội của mình sang người khác mà không có sự chấp thuận của cấp quản lý.

- **`BR-01.3` (Liên kết Khách hàng & Doanh nghiệp):** Một cơ hội liên kết được với một Doanh nghiệp và/hoặc một hoặc nhiều Khách hàng cá nhân. Khi gắn Doanh nghiệp, tên doanh nghiệp hiển thị trên cơ hội được đồng bộ tự động và **tiếp tục đồng bộ mỗi khi tên doanh nghiệp gốc thay đổi sau này**, không chỉ tại thời điểm gắn.

  **Lý do nghiệp vụ:** Nếu chỉ đồng bộ một lần tại thời điểm gắn, đổi tên doanh nghiệp (sáp nhập, đổi thương hiệu) sẽ để lại tên cũ trên mọi cơ hội lịch sử, gây nhầm lẫn khi tra cứu báo cáo theo tên doanh nghiệp.

- **`BR-01.4` (Chống tạo cơ hội trùng lặp):** Hệ thống từ chối tạo một cơ hội mới có cùng tên và cùng Doanh nghiệp với một cơ hội **đang mở** đã tồn tại, trừ khi người dùng chủ động xác nhận vẫn muốn tạo cơ hội riêng biệt.

  **Lý do nghiệp vụ:** Chống trùng lặp dữ liệu khi nhiều nhân viên cùng thao tác trên một khách hàng, hoặc khi quy trình chuyển đổi khách hàng tiềm năng chạy nhiều lần do thao tác nhầm. Cơ hội trùng không bị chặn tuyệt đối vì có tình huống hợp lệ cần nhiều cơ hội song song với cùng một doanh nghiệp (ví dụ hai dòng sản phẩm khác nhau) — xem thêm `FEAT-33` cho nghiệp vụ chống trùng khi chuyển đổi từ khách hàng tiềm năng.

- **`BR-01.5` (Phân quyền truy cập):** Áp dụng phạm vi dữ liệu theo Cá nhân / Phòng ban / Cây phòng ban / Toàn tổ chức. Người dùng không có quyền không xem hoặc sửa được cơ hội ngoài phạm vi.

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

---

#### FEAT-02 — Quản lý Đa Tiền tệ `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép mỗi cơ hội bán hàng ghi nhận giá trị bằng một loại tiền tệ do người dùng chọn.

**Quy tắc nghiệp vụ:**

- **`BR-02.1` (Tiền tệ theo cơ hội):** Mỗi cơ hội lưu đúng một mã tiền tệ chuẩn quốc tế.

- **`BR-02.2` (Giới hạn của báo cáo hợp nhất) `[Yêu cầu mới]`:** Báo cáo tổng hợp nhiều cơ hội (dự báo doanh thu, phân tích vận tốc) hiện **cộng dồn trực tiếp giá trị mà không quy đổi về một loại tiền tệ chung**. Khi một không gian làm việc có cơ hội thuộc nhiều loại tiền tệ khác nhau, báo cáo phải hiển thị cảnh báo rõ ràng rằng số tổng đang trộn lẫn tiền tệ, để người xem không hiểu nhầm là số liệu đã quy đổi.

  **Lý do nghiệp vụ:** Trộn lẫn tiền tệ trong một số tổng duy nhất mà không cảnh báo là sai lệch báo cáo nghiêm trọng — 100 USD và 100 VND cộng thẳng vào nhau tạo ra một con số vô nghĩa. Quy đổi tự động về một đồng tiền cơ sở theo tỷ giá thời gian thực là nhu cầu thật nhưng chưa được thiết kế (bảng tỷ giá, thời điểm chốt tỷ giá khi đóng cơ hội, tần suất cập nhật) nên chưa đưa vào phạm vi phát hành này — xem Mục 7.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-02.1.1` | Tạo cơ hội | Chọn tiền tệ USD, nhập giá trị | Lưu thành công với tiền tệ USD |
| `AC-02.2.1` | Không gian làm việc có cơ hội bằng cả USD và VND | Mở báo cáo dự báo doanh thu tổng hợp | Báo cáo hiển thị cảnh báo trộn tiền tệ ngay trên màn hình tổng số |

---

#### FEAT-03 — Bảo vệ Dữ liệu Tài chính Nhạy cảm `[Đã triển khai]`

**Mô tả nghiệp vụ:** Che giá trị và xác suất thắng của cơ hội đối với người dùng không có quyền xem dữ liệu tài chính.

**Quy tắc nghiệp vụ:**

- **`BR-03.1` (Che số liệu theo quyền):** Người dùng không có quyền xem đầy đủ dữ liệu tài chính thấy giá trị cơ hội hiển thị dạng che một phần, không đọc được số thật. Quyền hiển thị đầy đủ số liệu (`gỡ mặt nạ`) là một quyền hạn **riêng biệt**, không tự động đi kèm quyền xem hồ sơ cơ hội nói chung.

  **Lý do nghiệp vụ:** Giá trị hợp đồng là dữ liệu tài chính nhạy cảm nhất trên một cơ hội. Tách quyền xem hồ sơ khỏi quyền xem số liệu thật cho phép nhân sự hỗ trợ (ví dụ Pre-sales) tham gia xử lý cơ hội mà không cần biết giá trị hợp đồng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-03.1.1` | Người dùng có quyền xem cơ hội nhưng không có quyền gỡ mặt nạ tài chính | Mở hồ sơ cơ hội | Giá trị hiển thị dạng che, không đọc được số thật |
| `AC-03.1.2` | Người dùng có cả hai quyền | Mở hồ sơ cơ hội | Giá trị hiển thị đầy đủ |

---

#### FEAT-04 — Thùng rác & Phục hồi Cơ hội `[Đã triển khai]`

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
| `AC-04.4.1` | Cơ hội đang ở thùng rác, trước đó ở giai đoạn "Đàm phán" | Phục hồi | Cơ hội quay lại đúng giai đoạn "Đàm phán", lịch sử giai đoạn đầy đủ |

---

### Nhóm B — Phễu Bán hàng & Giai đoạn

#### FEAT-05 — Quản trị Nhiều Phễu Bán hàng Độc lập `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép doanh nghiệp vận hành song song nhiều quy trình bán hàng độc lập.

**Quy tắc nghiệp vụ:**

- **`BR-05.1` (Một phễu mặc định duy nhất):** Mỗi Không gian làm việc luôn duy trì đúng một phễu được đánh dấu mặc định.

- **`BR-05.2` (Khóa đồng thời khi sửa cấu hình) `[Yêu cầu mới]`:** Xem `FEAT-34`.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-05.1.1` | Không gian làm việc có 1 phễu mặc định | Đặt phễu khác làm mặc định | Phễu cũ tự động mất trạng thái mặc định, chỉ còn đúng 1 phễu mặc định |

---

#### FEAT-06 — Thiết lập Giai đoạn, Xác suất Thắng & Thứ tự `[Đã triển khai]`

**Mô tả nghiệp vụ:** Định nghĩa các giai đoạn tuần tự trong phễu, gán xác suất thắng và sắp xếp thứ tự.

**Quy tắc nghiệp vụ:**

- **`BR-06.1` (Xác suất thắng):** Là một giá trị phần trăm từ 0% đến 100%, do Quản trị viên gán cho từng giai đoạn. Ở phạm vi hiện tại, hệ thống chấp nhận cả giá trị lẻ (ví dụ 33%); việc có bắt buộc số nguyên tròn hay không là một lựa chọn hiển thị/quy đổi báo cáo, không phải một ràng buộc toàn vẹn dữ liệu.

- **`BR-06.2` (Một giai đoạn không được vừa là Thắng vừa là Thua):** Một giai đoạn không được đồng thời đánh dấu vừa là giai đoạn Thắng vừa là giai đoạn Thua.

- **`BR-06.2b` (Hai giai đoạn kết thúc bắt buộc) `[Yêu cầu mới]`:** Mỗi phễu phải có đúng hai giai đoạn kết thúc: một giai đoạn Thắng (xác suất 100%) và một giai đoạn Thua (xác suất 0%) — không thiếu, không thừa. Ở phạm vi hiện tại, hệ thống **chưa kiểm tra** điều kiện này khi tạo hoặc lưu phễu — một phễu có thể được lưu thiếu hẳn giai đoạn Thắng, thiếu giai đoạn Thua, hoặc có nhiều hơn một giai đoạn mỗi loại.

  **Lý do nghiệp vụ:** Thiếu ràng buộc này để lại rủi ro dữ liệu thật: một phễu không có giai đoạn Thắng khiến không cơ hội nào trong phễu đó có thể đóng thắng đúng quy trình (`BR-18.3` yêu cầu chuyển sang "giai đoạn Thắng đã cấu hình"); nhiều hơn một giai đoạn Thắng làm mơ hồ báo cáo dự báo doanh thu (`FEAT-25`) vì không rõ giai đoạn Thắng nào là chuẩn để tính xác suất 100%.

- **`BR-06.3` (Sắp xếp thứ tự):** Quản trị viên kéo thả đổi thứ tự các giai đoạn; toàn bộ thứ tự được cập nhật trong một thao tác duy nhất.

- **`BR-06.4` (Khóa nhảy cóc giai đoạn) [tham số cấu hình theo phễu]:** Mỗi phễu có thể bật quy tắc **không cho phép nhảy cóc** — chỉ được chuyển sang giai đoạn liền kề tiếp theo, không được bỏ qua giai đoạn ở giữa khi đang tiến lên. Quy tắc này **không áp dụng** khi lùi giai đoạn hoặc khi đóng cơ hội (Thắng/Thua có thể xảy ra từ bất kỳ giai đoạn nào). Mặc định **tắt** (Phụ lục B, `CFG-DEAL-02`).

  **Lý do nghiệp vụ:** Có doanh nghiệp coi việc nhảy cóc giai đoạn là dấu hiệu bỏ qua bước thẩm định quan trọng (ví dụ bỏ qua bước khảo sát kỹ thuật để nhảy thẳng vào đàm phán), cần khóa cứng theo từng phễu để đảm bảo tuân thủ quy trình. Doanh nghiệp khác có quy trình bán hàng linh hoạt, một cơ hội có thể đến thẳng từ giai đoạn đàm phán do khách hàng đã làm việc trước đó ở kênh khác — khóa cứng sẽ cản trở vận hành thật. Đây là tham số theo từng phễu (không phải toàn không gian làm việc) vì một doanh nghiệp có thể có phễu bán mới cần tuân thủ chặt và phễu gia hạn hợp đồng cần linh hoạt hơn.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-06.2.1` | Quản trị viên cấu hình một giai đoạn | Cố gắng đánh dấu giai đoạn đó vừa là Thắng vừa là Thua | Từ chối |
| `AC-06.2b.1` *(tiêu chí nghiệm thu khi `BR-06.2b` được xây)* | Quản trị viên tạo phễu mới, chưa đánh dấu giai đoạn nào là Thắng hoặc Thua | Cố gắng lưu phễu | Từ chối, yêu cầu đủ cả giai đoạn Thắng và Thua |
| `AC-06.2b.2` *(tiêu chí nghiệm thu khi `BR-06.2b` được xây)* | Phễu đã có 1 giai đoạn Thắng, chưa có giai đoạn Thua nào | Cố gắng lưu phễu | Từ chối, báo thiếu giai đoạn Thua |
| `AC-06.2b.3` *(tiêu chí nghiệm thu khi `BR-06.2b` được xây)* | Quản trị viên cố gắng đánh dấu một giai đoạn thứ hai là Thắng trong khi phễu đã có 1 giai đoạn Thắng | Lưu | Từ chối, báo phễu chỉ được có đúng một giai đoạn Thắng |
| `AC-06.4.1` | Phễu bật khóa nhảy cóc, cơ hội đang ở giai đoạn 2/5 | Cố gắng chuyển thẳng sang giai đoạn 4 | Từ chối, yêu cầu qua giai đoạn 3 trước |
| `AC-06.4.2` | Cùng bối cảnh AC-06.4.1 | Đóng cơ hội thắng ngay từ giai đoạn 2 | Cho phép — khóa nhảy cóc không áp dụng khi đóng cơ hội |
| `AC-06.4.3` | Cùng bối cảnh AC-06.4.1 | Lùi cơ hội từ giai đoạn 2 về giai đoạn 1 | Cho phép — khóa nhảy cóc chỉ áp dụng khi tiến lên |

---

#### FEAT-07 — Đóng Phễu An toàn & Di chuyển Cơ hội `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi ngừng sử dụng một phễu, bắt buộc quản trị viên chỉ định phương án xử lý cho toàn bộ cơ hội đang mở.

**Quy tắc nghiệp vụ:**

- **`BR-07.1` (Hai phương án loại trừ lẫn nhau):** Quản trị viên chọn đúng một trong hai: **(a)** thiết lập bảng ghép từng giai đoạn cũ sang giai đoạn tương ứng của một phễu khác, di chuyển toàn bộ cơ hội đang mở theo bảng ghép đó; hoặc **(b)** đóng băng toàn bộ cơ hội trong phễu ở chế độ chỉ đọc để bảo toàn lịch sử báo cáo, không di chuyển đi đâu.

- **`BR-07.2` (Chặn đóng phễu còn dữ liệu mở mà chưa chọn phương án):** Hệ thống từ chối yêu cầu đóng phễu nếu phễu còn cơ hội đang mở mà chưa chọn một trong hai phương án ở `BR-07.1`.

- **`BR-07.3` (Không đảm bảo tính nguyên tử tuyệt đối) `[Yêu cầu mới]`:** Thao tác di chuyển hàng loạt cơ hội sang phễu mới và đánh dấu phễu cũ đã đóng hiện chạy thành hai bước tuần tự, **không được bọc trong cùng một giao dịch nguyên tử duy nhất**. Nếu tiến trình bị gián đoạn giữa hai bước, có thể xảy ra tình trạng cơ hội đã được di chuyển nhưng phễu cũ chưa được đánh dấu đóng (hoặc ngược lại). Trước khi coi tính năng này là hoàn thiện, phải bổ sung cơ chế đảm bảo cả hai bước cùng thành công hoặc cùng thất bại.

  **Lý do nghiệp vụ:** Đây là một khoảng hở toàn vẹn dữ liệu thật đang tồn tại. Ghi nhận công khai trong SRS thay vì im lặng để đội phát triển ưu tiên vá trước khi tính năng này được dùng ở quy mô dữ liệu lớn, nơi khả năng gián đoạn giữa hai bước tăng lên.

- **`BR-07.4` (Không hỗ trợ mở lại phễu đã đóng):** Ở phạm vi phát hành hiện tại, một phễu đã đóng (dù theo phương án di chuyển hay đóng băng chỉ đọc) **không có thao tác đưa trở lại trạng thái hoạt động**. Đây là quyết định có chủ đích, không phải một khoảng trống bị bỏ sót: cho phép mở lại một phễu đã di chuyển hết dữ liệu đi nơi khác sẽ tạo ra một phễu rỗng gây nhầm lẫn, còn mở lại một phễu đang đóng băng chỉ đọc thì không rõ các cơ hội đã bị đóng băng có nên tiếp tục vận hành bình thường trở lại hay không — quyết định này chưa chín muồi. Nếu về sau phát sinh nhu cầu mở lại phễu, cần bổ sung `FEAT` riêng, không mở rộng ngầm ý nghĩa của `FEAT-07`.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-07.1.1` | Phễu A còn 10 cơ hội đang mở | Chọn di chuyển sang Phễu B theo bảng ghép giai đoạn, đóng Phễu A | 10 cơ hội chuyển sang đúng giai đoạn tương ứng ở Phễu B; Phễu A đóng |
| `AC-07.1.2` | Phễu A còn 10 cơ hội đang mở | Chọn đóng băng chỉ đọc, đóng Phễu A | 10 cơ hội giữ nguyên vị trí, chuyển chỉ đọc; Phễu A đóng |
| `AC-07.2.1` | Phễu A còn cơ hội đang mở | Cố gắng đóng phễu mà không chọn phương án nào | Từ chối, yêu cầu chọn phương án xử lý dữ liệu |

---

#### FEAT-08 — Xóa Giai đoạn An toàn `[Đã triển khai]`

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

#### FEAT-09 — Bảng Kanban Tổng hợp Giá trị Thời gian thực `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hiển thị tổng số lượng và tổng giá trị cơ hội trên từng cột giai đoạn, có áp dụng phân quyền dữ liệu.

**Quy tắc nghiệp vụ:**

- **`BR-09.1` (Tổng số phản ánh đúng phạm vi quyền):** Số liệu tổng trên đầu mỗi cột chỉ tính các cơ hội mà người xem có quyền nhìn thấy, khớp chính xác với bộ lọc đang áp dụng.

- **`BR-09.2` (Tải thẻ theo từng cột độc lập):** Danh sách thẻ cơ hội trong mỗi cột được tải riêng biệt, cuộn thêm khi cần, không phụ thuộc vào việc tải các cột khác.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-09.1.1` | Cột "Đàm phán" có 200 cơ hội, người xem chỉ có quyền thấy 50 | Mở Kanban | Tổng số hiển thị trên đầu cột là 50, không phải 200 |

---

#### FEAT-10 — Kéo thả Chuyển Giai đoạn `[Đã triển khai]`

**Mô tả nghiệp vụ:** Nhân viên kéo thả thẻ cơ hội từ cột này sang cột khác để cập nhật tiến độ.

**Quy tắc nghiệp vụ:**

- **`BR-10.1` (Kiểm tra rào cản trước khi chuyển):** Kéo thả kích hoạt kiểm tra Rào cản Giai đoạn (`FEAT-13`). Nếu đủ điều kiện, hệ thống cập nhật giai đoạn, ghi nhận thời gian đã lưu ở giai đoạn cũ và thêm một bản ghi vào lịch sử giai đoạn (`FEAT-12`).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-10.1.1` | Cơ hội đủ điều kiện rào cản | Kéo thả sang cột kế tiếp | Chuyển thành công, lịch sử giai đoạn ghi nhận thêm 1 dòng |

---

#### FEAT-11 — Bộ lọc trên Bảng Kanban `[Đã triển khai]`

**Mô tả nghiệp vụ:** Lọc nhanh cơ hội hiển thị trên Kanban theo các tiêu chí thường dùng.

**Quy tắc nghiệp vụ:**

- **`BR-11.1` (Tiêu chí lọc sẵn có):** Lọc theo "Cơ hội của tôi", theo trạng thái lịch chăm sóc tiếp theo (quá hạn/hôm nay/chưa đặt lịch), và theo từ khóa tìm kiếm.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-11.1.1` | Danh sách có cơ hội quá hạn lịch chăm sóc | Lọc theo "Quá hạn chăm sóc" | Chỉ hiện các cơ hội có lịch chăm sóc đã quá hạn |

---

### Nhóm D — Lịch sử Giai đoạn & Vận tốc

#### FEAT-12 — Ghi nhận Lịch sử Chuyển Giai đoạn `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động lưu lại mỗi lần chuyển giai đoạn: thời điểm, người thực hiện và thời gian đã lưu ở giai đoạn trước đó.

**Quy tắc nghiệp vụ:**

- **`BR-12.1` (Bất biến có giới hạn):** Mỗi bản ghi lịch sử giai đoạn là bất biến — không sửa, không xóa. Hệ thống giữ lại **100 bản ghi gần nhất** cho mỗi cơ hội; các cơ hội đổi giai đoạn ít hơn con số này có lịch sử đầy đủ tuyệt đối.

  **Lý do nghiệp vụ:** Giới hạn số bản ghi ngăn một cơ hội bị đổi giai đoạn qua lại liên tục (thao tác nhầm, thử nghiệm cấu hình) làm phình to vô hạn dữ liệu lịch sử. 100 lần chuyển giai đoạn vượt xa số bước thực tế của bất kỳ phễu bán hàng nào trong vận hành bình thường.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-12.1.1` | Cơ hội chuyển giai đoạn 3 lần | Xem lịch sử giai đoạn | Hiển thị đủ 3 dòng, mỗi dòng có thời điểm, người thực hiện, thời gian lưu ở giai đoạn trước |

---

#### FEAT-13 — Rào cản Điều kiện Chuyển Giai đoạn `[Đã triển khai]`

**Mô tả nghiệp vụ:** Quản trị viên cấu hình danh sách trường dữ liệu bắt buộc phải có giá trị trước khi một cơ hội được coi là đủ điều kiện ở một giai đoạn.

**Quy tắc nghiệp vụ:**

- **`BR-13.1` (Trường bắt buộc theo giai đoạn):** Mỗi giai đoạn cấu hình được một danh sách trường dữ liệu (kể cả trường tùy biến) bắt buộc phải có giá trị. Áp dụng cả khi tạo cơ hội thẳng vào giai đoạn đó lẫn khi chuyển giai đoạn tới.

- **`BR-13.2` (Tích lũy khi nhảy cóc):** Khi một cơ hội chuyển qua nhiều giai đoạn cùng lúc (bỏ qua các giai đoạn ở giữa), điều kiện bắt buộc của **toàn bộ các giai đoạn bị bỏ qua** đều phải được thỏa mãn, không chỉ giai đoạn đích.

  **Lý do nghiệp vụ:** Nếu chỉ kiểm tra điều kiện của giai đoạn đích, một cơ hội có thể nhảy thẳng từ giai đoạn đầu tới giai đoạn cuối mà bỏ qua toàn bộ yêu cầu dữ liệu của các bước ở giữa — đúng lỗ hổng mà rào cản giai đoạn được tạo ra để chặn.

- **`BR-13.3` (Từ chối và liệt kê thiếu sót):** Khi không đủ điều kiện, hệ thống từ chối chuyển giai đoạn và liệt kê rõ từng trường còn thiếu.

- **`BR-13.4` (Phạm vi hiện tại — chỉ trường dữ liệu):** Ở phạm vi phát hành hiện tại, rào cản giai đoạn chỉ kiểm tra trường dữ liệu bắt buộc. Yêu cầu đính kèm tài liệu bắt buộc và yêu cầu khai báo vai trò liên hệ bắt buộc (ví dụ bắt buộc có Người ra quyết định trước khi vào giai đoạn đàm phán) là nhu cầu thật nhưng **chưa được triển khai** — ghi nhận tại Mục 7.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-13.1.1` | Giai đoạn "Báo giá" bắt buộc có Ngày dự kiến đóng | Chuyển cơ hội chưa có Ngày dự kiến đóng vào "Báo giá" | Từ chối, báo thiếu Ngày dự kiến đóng |
| `AC-13.2.1` | Giai đoạn 2 bắt buộc trường X, giai đoạn 3 bắt buộc trường Y. Cơ hội đang ở giai đoạn 1, chưa có cả X lẫn Y | Chuyển thẳng từ giai đoạn 1 sang giai đoạn 4 | Từ chối, liệt kê thiếu cả trường X và Y |

---

#### FEAT-14 — Phân tích Vận tốc Bán hàng & Điểm nghẽn `[Đã triển khai]`

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

#### FEAT-15 — Thiết lập Lịch Chăm sóc Tiếp theo `[Đã triển khai]`

**Mô tả nghiệp vụ:** Người phụ trách đặt thời điểm cam kết liên hệ lại khách hàng.

**Quy tắc nghiệp vụ:**

- **`BR-15.1` (Mặc định khi tạo mới):** Nếu người dùng không tự đặt, hệ thống tự động đề xuất lịch chăm sóc tiếp theo cách thời điểm tạo cơ hội một khoảng thời gian mặc định. Khoảng thời gian này là tham số cấu hình theo Không gian làm việc, mặc định **24 giờ** (Phụ lục B, `CFG-DEAL-03`).

- **`BR-15.2` (Dời lịch sau khi đã có Công việc nhắc) `[Yêu cầu mới]`:** Nếu người phụ trách dời lịch chăm sóc tiếp theo sang một thời điểm khác **sau khi** Công việc nhắc nhở tương ứng đã được tạo (`BR-16.2`) nhưng **trước khi** Công việc đó được xử lý, Công việc cũ phải được cập nhật theo hạn mới hoặc hủy và thay bằng một Công việc mới đúng hạn — không được để cả Công việc cũ (theo lịch đã dời) và lịch mới cùng tồn tại song song, gây người phụ trách nhận việc nhắc cho một lịch hẹn không còn hiệu lực.

  **Lý do nghiệp vụ:** Nếu không xử lý chiều ngược này, một hành động hợp lý của người dùng (dời lịch vì khách hàng xin dời hẹn) sẽ để lại một Công việc "rác" nhắc sai thời điểm, làm người phụ trách mất niềm tin vào danh sách việc cần làm và có thể liên hệ khách hàng nhầm thời điểm đã thống nhất lại.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-15.1.1` | Tạo cơ hội mới, không tự đặt lịch chăm sóc | Lưu | Hệ thống tự gán lịch chăm sóc sau 24 giờ kể từ thời điểm tạo |
| `AC-15.2.1` *(tiêu chí nghiệm thu khi `BR-15.2` được xây)* | Lịch chăm sóc đã đến hạn, Công việc nhắc đã được tạo nhưng chưa xử lý | Người phụ trách dời lịch chăm sóc sang 3 ngày sau | Công việc nhắc cũ được cập nhật sang hạn mới (hoặc hủy và tạo lại), không còn Công việc nào nhắc theo hạn cũ đã dời |

---

#### FEAT-16 — Quét & Nhắc nhở Lịch Chăm sóc Đến hạn `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tiến trình nền định kỳ quét các cơ hội có lịch chăm sóc đã đến hạn và chưa được nhắc, để phát thông báo tới người phụ trách.

**Quy tắc nghiệp vụ:**

- **`BR-16.1` (Chu kỳ quét):** Tiến trình quét chạy mỗi **5 phút**, là hằng số vận hành hệ thống, không cấu hình được theo từng Không gian làm việc.

- **`BR-16.2` (Kết quả khi đến hạn):** Khi phát hiện lịch chăm sóc đến hạn, hệ thống đồng thời: phát thông báo trong ứng dụng tới người phụ trách, **và tạo một Công việc mới giao cho người phụ trách đó** để việc chăm sóc xuất hiện trong danh sách việc cần làm hằng ngày, không chỉ là một thông báo thoáng qua.

- **`BR-16.3` (Chống nhắc trùng):** Mỗi lịch chăm sóc chỉ được nhắc đúng một lần, kể cả khi có nhiều lượt quét chạy đồng thời.

- **`BR-16.4` (Ngừng nhắc quá cũ):** Lịch chăm sóc đã quá hạn quá **7 ngày** mà chưa được xử lý thì không tiếp tục kích hoạt nhắc mới nữa, để tránh dội thông báo cho những lịch hẹn đã quá cũ; đây là hằng số vận hành hệ thống.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-16.2.1` | Lịch chăm sóc đến đúng hạn | Tiến trình quét chạy | Người phụ trách nhận thông báo trong ứng dụng VÀ có một Công việc mới trong danh sách việc cần làm |
| `AC-16.3.1` | Hai lượt quét chạy gần như đồng thời cho cùng một lịch chăm sóc | Cả hai lượt quét xử lý | Chỉ có đúng một thông báo và một Công việc được tạo, không trùng lặp |
| `AC-16.4.1` | Lịch chăm sóc đã quá hạn 10 ngày, chưa từng được nhắc | Tiến trình quét chạy | Không kích hoạt nhắc nhở mới cho lịch hẹn này |

---

#### FEAT-17 — Nhận diện Cơ hội Nguội Lạnh `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Tự động phát hiện và đánh dấu các cơ hội đang mở nhưng không có bất kỳ tương tác nào trong một khoảng thời gian dài, để người phụ trách và quản lý kịp thời can thiệp trước khi cơ hội nguội tắt hẳn.

**Quy tắc nghiệp vụ (đặc tả cho tính năng cần xây dựng):**

- **`BR-17.1` (Ngưỡng nguội lạnh):** Một cơ hội đang mở không có tương tác nào vượt quá một ngưỡng số ngày thì được đánh dấu là nguội lạnh. Ngưỡng này là tham số cấu hình theo Không gian làm việc, đề xuất mặc định **14 ngày** (Phụ lục B, `CFG-DEAL-04`).

- **`BR-17.2` (Hành vi công nhận là "có tương tác"):** Các hành vi sau làm mới thời điểm tương tác gần nhất và gỡ cờ nguội lạnh nếu đang có: ghi nhận cuộc gọi/cuộc họp/email trên cơ hội, chuyển giai đoạn, nhận phản hồi mới từ khách hàng qua kênh liên lạc. **Việc chỉnh sửa các trường thông tin nội bộ (ví dụ đổi thẻ phân loại, sửa mô tả nội bộ) không được tính là tương tác** — quy tắc này khác với hành vi làm mới thời điểm tương tác gần nhất khi cập nhật cơ hội nói chung, vốn hiện coi mọi lần sửa là một tương tác.

  **Lý do nghiệp vụ:** Nếu bất kỳ thao tác sửa nào (kể cả một quản trị viên sửa lại mô tả nội bộ vì lỗi chính tả) cũng được tính là "cơ hội đang được chăm sóc", cơ chế cảnh báo nguội lạnh sẽ mất hoàn toàn ý nghĩa cảnh báo — một cơ hội có thể bị bỏ quên thật sự về mặt bán hàng nhưng vẫn liên tục được "làm mới" bởi các thao tác quản trị không liên quan tới khách hàng.

- **`BR-17.3` (Hiển thị):** Cơ hội nguội lạnh hiển thị dấu hiệu cảnh báo trực quan trên thẻ Kanban, kèm số ngày không có tương tác.

**Tiêu chí Chấp nhận (tiêu chí nghiệm thu khi tính năng được xây dựng):**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-17.1.1` | Cơ hội đang mở, không tương tác 16 ngày, ngưỡng cấu hình 14 ngày | Mở Kanban | Thẻ hiển thị cảnh báo nguội lạnh kèm "16 ngày không hoạt động" |
| `AC-17.2.1` | Cơ hội đang bị đánh dấu nguội lạnh | Ghi nhận một cuộc gọi mới trên cơ hội | Cờ nguội lạnh biến mất ngay lập tức |
| `AC-17.2.2` | Cơ hội đang bị đánh dấu nguội lạnh | Quản trị viên chỉ sửa thẻ phân loại nội bộ, không liên hệ khách hàng | Cờ nguội lạnh **không** biến mất |

**Tham chiếu:** Ghi nhận từ đối chiếu mã nguồn 2026-09-23 — bản thân cơ chế nhận diện nguội lạnh (ngưỡng, cờ, bộ lọc) chưa tồn tại trong hệ thống ở thời điểm này, dù trường "thời điểm tương tác gần nhất" đã có sẵn trên mỗi cơ hội và được cập nhật bởi mọi thao tác sửa — hành vi cập nhật hiện tại rộng hơn `BR-17.2` mô tả và cần thu hẹp lại khi xây dựng tính năng này.

---

### Nhóm F — Thắng/Thua

#### FEAT-18 — Đóng Cơ hội Thành công `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi thương vụ chốt thành công, chuyển cơ hội sang trạng thái Thắng.

**Quy tắc nghiệp vụ:**

- **`BR-18.1` (Điều kiện đóng thắng):** Bắt buộc giá trị cơ hội lớn hơn 0, có ngày dự kiến đóng, và có đúng một liên hệ chính được xác định rõ (một Khách hàng cá nhân liên kết trực tiếp, hoặc một Vai trò liên hệ được đánh dấu là liên hệ chính — xem `FEAT-22`).

- **`BR-18.2` (Bắt buộc có người phụ trách khi đóng thắng) [tham số cấu hình theo Không gian làm việc]:** Mặc định, hệ thống yêu cầu cơ hội phải có người phụ trách trước khi được phép đóng thắng. Đây là tham số cấu hình theo Không gian làm việc (Phụ lục B, `CFG-DEAL-05`), mặc định **bật**.

  **Lý do nghiệp vụ:** Một doanh nghiệp coi việc đóng thắng một cơ hội không ai phụ trách là dấu hiệu dữ liệu bất thường cần chặn lại để buộc gán người trước. Doanh nghiệp khác có quy trình cho phép cơ hội "Chưa phân công" được xử lý và đóng bởi bất kỳ ai trong hàng đợi chung, nên cần tắt ràng buộc này.

- **`BR-18.3` (Kết quả sau khi đóng thắng):** Ghi nhận thời điểm thắng, giai đoạn chuyển sang giai đoạn Thắng đã cấu hình (xác suất theo đúng giá trị đã gán cho giai đoạn đó, không mặc định cứng 100% — Quản trị viên có thể tùy chỉnh xác suất của giai đoạn Thắng).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-18.1.1` | Cơ hội chưa có ngày dự kiến đóng | Cố gắng đóng thắng | Từ chối, báo thiếu ngày dự kiến đóng |
| `AC-18.2.1` | Cơ hội "Chưa phân công", tham số `CFG-DEAL-05` đang bật (mặc định) | Cố gắng đóng thắng | Từ chối, yêu cầu gán người phụ trách trước |
| `AC-18.2.2` | Quản trị viên tắt `CFG-DEAL-05` | Đóng thắng cơ hội "Chưa phân công" | Cho phép |
| `AC-18.3.1` | Cơ hội đủ điều kiện, giai đoạn Thắng có xác suất cấu hình 100% | Đóng thắng | Ghi nhận thời điểm thắng, chuyển giai đoạn Thắng |

---

#### FEAT-19 — Đóng Cơ hội Thất bại & Bắt buộc Lý do `[Đã triển khai]`

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

#### FEAT-20 — Danh mục Lý do Thất bại Chuẩn hóa `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép Quản trị viên định nghĩa một danh mục lý do thất bại chuẩn hóa để nhân viên chọn khi đóng thua, thay vì nhập văn bản tự do, phục vụ phân tích nguyên nhân thất bại có cấu trúc.

**Quy tắc nghiệp vụ (đặc tả cho tính năng cần xây dựng):**

- **`BR-20.1` (Danh mục do tenant định nghĩa, có ràng buộc toàn vẹn tối thiểu):** Quản trị viên tạo, sửa, vô hiệu hóa các lý do thất bại chuẩn (ví dụ "Giá cao hơn ngân sách", "Chọn đối thủ cạnh tranh", "Hết ngân sách", "Không có nhu cầu"). Cùng nguyên tắc với danh mục vai trò liên hệ (`BR-22.1`): một lý do **đang được sử dụng** trên ít nhất một cơ hội đã đóng thua không được xóa hẳn — chỉ được vô hiệu hóa, giữ nguyên trên các bản ghi lịch sử đã gắn.

- **`BR-20.2` (Bắt buộc chọn từ danh mục khi đóng thua):** Khi danh mục đã được cấu hình và có ít nhất một lý do đang hoạt động, đóng thua bắt buộc chọn từ danh mục thay vì nhập tự do; có thể kèm ghi chú giải thích bổ sung.

**Tiêu chí Chấp nhận (tiêu chí nghiệm thu khi tính năng được xây dựng):**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-20.1.1` | Quản trị viên tạo danh mục lý do thất bại | Thêm lý do "Giá cao hơn ngân sách" | Lưu thành công, xuất hiện trong danh sách chọn khi đóng thua |
| `AC-20.2.1` | Danh mục đã có lý do hoạt động | Đóng cơ hội thất bại | Bắt buộc chọn một lý do từ danh mục, không cho nhập tự do |

**Tham chiếu:** Xác nhận qua đối chiếu mã nguồn 2026-09-23 — hiện chưa có danh mục lý do thất bại nào tồn tại; cũng chưa có khái niệm "đối thủ cạnh tranh" trong hệ thống.

---

#### FEAT-21 — Tái phân loại Cơ hội Đã đóng `[Đã triển khai]`

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

#### FEAT-22 — Gắn Vai trò Liên hệ vào Cơ hội `[Đã triển khai]`

**Mô tả nghiệp vụ:** Gắn nhiều nhân sự phía khách hàng vào một cơ hội với vai trò cụ thể trong quá trình ra quyết định mua hàng.

**Quy tắc nghiệp vụ:**

- **`BR-22.1` (Danh mục vai trò do tenant định nghĩa, có ràng buộc toàn vẹn tối thiểu):** Danh mục vai trò liên hệ (ví dụ Người ra quyết định, Người bảo trợ nội bộ, Người đánh giá kỹ thuật) do từng Không gian làm việc tự định nghĩa, thêm/sửa/vô hiệu hóa tùy nhu cầu — không phải danh sách cố định của hệ thống. Danh mục này dùng chung với vai trò liên hệ trên hồ sơ Khách hàng (xem [`contacts-srs.md`](./contacts-srs.md)). Một vai trò **đang được sử dụng** trên ít nhất một cơ hội không được xóa hẳn khỏi danh mục — chỉ được vô hiệu hóa (ẩn khỏi lựa chọn cho bản ghi mới, giữ nguyên trên các bản ghi đã gắn).

  **Lý do nghiệp vụ:** Nếu cho xóa cứng một vai trò đang gắn trên dữ liệu thật, các cơ hội đang tham chiếu vai trò đó sẽ mất dấu vết vai trò liên hệ đã khai báo, phá vỡ điều kiện đóng thắng đã ghi nhận trước đó tại `BR-18.1`.

- **`BR-22.2` (Một liên hệ chính duy nhất):** Mỗi cơ hội có tối đa một liên hệ được đánh dấu là liên hệ chính, dùng làm căn cứ xác định điều kiện đóng thắng tại `BR-18.1`.

- **`BR-22.3` (Chưa có rào cản bắt buộc theo vai trò) `[Yêu cầu mới]`:** Yêu cầu bắt buộc phải khai báo một vai trò cụ thể (ví dụ bắt buộc có Người ra quyết định) trước khi vào một giai đoạn nhất định là nhu cầu thật nhưng **chưa được triển khai** — xem `BR-13.4`.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-22.1.1` | Quản trị viên định nghĩa vai trò mới "Người vận hành hệ thống" | Gắn vai trò này cho một liên hệ trên cơ hội | Lưu thành công |
| `AC-22.2.1` | Cơ hội đã có 1 liên hệ chính | Đánh dấu một liên hệ khác là liên hệ chính | Liên hệ cũ tự động mất trạng thái chính, chỉ còn 1 liên hệ chính |

---

#### FEAT-23 — Theo dõi Nguồn gốc Tiếp thị `[Đã triển khai]`

**Mô tả nghiệp vụ:** Ghi nhận các tham số nguồn gốc tiếp thị trên cơ hội để đo lường hiệu quả kênh.

**Quy tắc nghiệp vụ:**

- **`BR-23.1` (Tham số nguồn gốc):** Cơ hội lưu các tham số nguồn gốc chiến dịch tiếp thị đã đưa khách hàng tới hệ thống, dùng để lọc và nhóm trong báo cáo nguồn gốc (`FEAT-26`).

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-23.1.1` | Khách hàng tiềm năng đến từ một chiến dịch quảng cáo có gắn tham số nguồn | Chuyển đổi thành cơ hội | Tham số nguồn gốc được kế thừa sang cơ hội, lọc được trong báo cáo |

---

#### FEAT-24 — Phân chia Doanh số Đồng phụ trách `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép chia tỷ lệ ghi nhận doanh số giữa nhiều nhân viên cùng tham gia chốt một hợp đồng lớn.

**Quy tắc nghiệp vụ (đặc tả sơ bộ, cần hoàn thiện thiết kế trước khi triển khai):**

- **`BR-24.1` (Tổng tỷ lệ bằng 100%):** Tổng tỷ lệ phân chia giữa các nhân viên tham gia một cơ hội phải bằng chính xác 100%.

**Tiêu chí Chấp nhận (tiêu chí nghiệm thu khi tính năng được xây dựng):**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-24.1.1` | Cơ hội có 2 người tham gia | Đặt tỷ lệ 70%/30% | Lưu thành công |
| `AC-24.1.2` | Cơ hội có 2 người tham gia | Đặt tỷ lệ 70%/20% (tổng 90%) | Từ chối, báo tổng tỷ lệ phải bằng 100% |

**Tham chiếu:** Nhu cầu nghiệp vụ được ghi nhận nhưng thiết kế chi tiết (cách phân chia ảnh hưởng tới báo cáo hiệu suất tại `FEAT-26`, cách xử lý khi một người tham gia rời đơn vị) chưa hoàn thiện — xem Mục 7.

---

### Nhóm H — Dự báo Doanh thu

#### FEAT-25 — Dự báo Doanh thu có Trọng số `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tính doanh thu kỳ vọng từ các cơ hội đang mở, theo giá trị cơ hội nhân với xác suất thắng của giai đoạn hiện tại.

**Quy tắc nghiệp vụ:**

- **`BR-25.1` (Công thức):** Doanh thu dự báo của một giai đoạn bằng tổng của (giá trị từng cơ hội đang ở giai đoạn đó nhân với xác suất thắng của giai đoạn đó). Doanh thu dự báo toàn phễu là tổng doanh thu dự báo của tất cả các giai đoạn đang mở.

- **`BR-25.2` (Không quy đổi tiền tệ):** Xem `BR-02.2` — báo cáo này chịu cùng giới hạn về trộn lẫn tiền tệ.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-25.1.1` | Giai đoạn "Đàm phán" (xác suất 60%) có 2 cơ hội trị giá 100 và 200 | Mở báo cáo dự báo | Doanh thu dự báo của giai đoạn = 60% × (100 + 200) = 180 |

---

#### FEAT-26 — Báo cáo Hiệu suất Người phụ trách & Nguồn `[Đã triển khai]`

**Mô tả nghiệp vụ:** Báo cáo tổng hợp hiệu suất chốt hợp đồng theo từng người phụ trách và theo nguồn gốc tiếp thị.

**Quy tắc nghiệp vụ:**

- **`BR-26.1` (Báo cáo theo người phụ trách):** Tổng hợp số cơ hội, tổng giá trị và tỷ lệ thắng theo từng người phụ trách trong một khoảng thời gian.

- **`BR-26.2` (Báo cáo theo nguồn gốc):** Tổng hợp số cơ hội và tổng giá trị theo nguồn gốc tiếp thị (`FEAT-23`).

- **`BR-26.3` (Báo cáo tuổi cơ hội):** Phân nhóm cơ hội đang mở theo khoảng số ngày đã tồn tại thành bốn nhóm liền kề không chồng lấn (ví dụ nhóm "mới", "trong tháng", "trong quý", "tồn đọng lâu"), giúp nhận diện cơ hội tồn đọng lâu. Ranh giới cụ thể giữa các nhóm là chi tiết triển khai, không phải quy tắc nghiệp vụ ràng buộc — điều quan trọng về nghiệp vụ là có đúng bốn nhóm liền kề và nhóm cuối cùng luôn là "trên 90 ngày" để đánh dấu rủi ro tồn đọng.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-26.1.1` | Nhân viên A đóng thắng 3/5 cơ hội trong tháng | Mở báo cáo hiệu suất người phụ trách | Hiển thị đúng tỷ lệ thắng 60% cho nhân viên A |

---

#### FEAT-27 — Hạn ngạch Doanh số `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Thiết lập mục tiêu doanh số theo kỳ cho từng nhân viên hoặc phòng ban, theo dõi tỷ lệ hoàn thành.

**Quy tắc nghiệp vụ (đặc tả sơ bộ, cần hoàn thiện thiết kế trước khi triển khai):**

- **`BR-27.1` (Mục tiêu theo kỳ):** Quản lý Kinh doanh hoặc Giám đốc Kinh doanh đặt một mục tiêu doanh số cho một nhân viên hoặc phòng ban trong một kỳ (tháng/quý) xác định. Mỗi nhân viên/phòng ban chỉ có đúng một mục tiêu cho một kỳ — không cho đặt trùng.

**Tiêu chí Chấp nhận (tiêu chí nghiệm thu khi tính năng được xây dựng):**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-27.1.1` | Quản lý đặt mục tiêu quý cho nhân viên A là 500 triệu | Nhân viên A đóng thắng các cơ hội tổng 300 triệu trong quý | Báo cáo hiển thị tỷ lệ hoàn thành 60% |
| `AC-27.1.2` | Nhân viên A đã có mục tiêu cho quý này | Quản lý cố gắng đặt thêm một mục tiêu khác cho cùng nhân viên A, cùng quý | Từ chối, báo đã có mục tiêu cho kỳ này |
| `AC-27.1.3` | Nhân viên A có mục tiêu quý nhưng không đóng thắng cơ hội nào trong quý | Xem báo cáo hạn ngạch | Hiển thị tỷ lệ hoàn thành 0%, không lỗi hiển thị |

**Tham chiếu:** Nhu cầu nghiệp vụ được ghi nhận nhưng chưa có thiết kế chi tiết (chu kỳ đặt mục tiêu, cách xử lý khi nhân viên đổi phòng ban giữa kỳ, quan hệ với `FEAT-24`) — xem Mục 7. Xác nhận qua đối chiếu mã nguồn 2026-09-23: không tồn tại module nào cho hạn ngạch doanh số ở thời điểm hiện tại.

---

### Nhóm I — Thao tác Hàng loạt & Nhập/Xuất Dữ liệu

#### FEAT-28 — Cập nhật, Gắn thẻ & Xóa Hàng loạt `[Đã triển khai]`

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

#### FEAT-29 — Nhập / Xuất Dữ liệu Dung lượng lớn qua Hàng đợi `[Đã triển khai]`

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

---

### Nhóm J — Dòng thời gian & Toàn vẹn Cấu trúc

#### FEAT-30 — Dòng thời gian Hoạt động Hợp nhất `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hiển thị toàn bộ lịch sử tương tác trên một cơ hội theo thứ tự thời gian.

**Quy tắc nghiệp vụ:**

- **`BR-30.1` (Nguồn hợp nhất):** Dòng thời gian gộp hoạt động ghi nhận thủ công, ghi chú, vé hỗ trợ liên quan, công việc liên quan, cuộc gọi, cuộc họp, email và lịch sử chuyển giai đoạn — theo đúng thứ tự thời gian xảy ra.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-30.1.1` | Cơ hội có cả ghi chú, 1 lần chuyển giai đoạn và 1 vé hỗ trợ liên kết | Mở dòng thời gian | Cả ba loại sự kiện hiển thị đúng thứ tự thời gian xảy ra |

---

#### FEAT-31 — Cảnh báo Vé Hỗ trợ Khẩn cấp trên Kanban `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khi một cơ hội có khách hàng đang gặp sự cố kỹ thuật nghiêm trọng chưa được giải quyết, cảnh báo ngay trên thẻ Kanban để nhân viên kinh doanh không tiếp tục đàm phán chốt hợp đồng mà bỏ qua rủi ro kỹ thuật đang mở.

**Quy tắc nghiệp vụ (đặc tả cho tính năng cần xây dựng):**

- **`BR-31.1` (Điều kiện cảnh báo):** Nếu cơ hội có ít nhất một Vé hỗ trợ đang mở ở mức độ ưu tiên cao nhất, thẻ cơ hội trên Kanban hiển thị dấu hiệu cảnh báo trực quan.

**Tiêu chí Chấp nhận (tiêu chí nghiệm thu khi tính năng được xây dựng):**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-31.1.1` | Cơ hội có 1 Vé hỗ trợ đang mở ở mức ưu tiên cao nhất | Mở Kanban | Thẻ cơ hội hiển thị dấu hiệu cảnh báo |
| `AC-31.1.2` | Tiếp nối AC-31.1.1 | Vé hỗ trợ được đóng | Dấu hiệu cảnh báo biến mất khỏi thẻ |

**Tham chiếu:** Xác nhận qua đối chiếu mã nguồn 2026-09-23 — hiện tại màn hình chi tiết cơ hội đã hiển thị được danh sách vé hỗ trợ liên quan (phần còn lại của `FEAT-30`), nhưng chưa có cờ cảnh báo nào xuất hiện trên thẻ Kanban.

---

#### FEAT-32 — Đóng băng Ghi dữ liệu khi Di chuyển Dữ liệu nền `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi đội vận hành nền tảng chạy một tiến trình di chuyển dữ liệu quy mô lớn giữa các hệ thống, tạm thời chặn mọi thay đổi trên Cơ hội bán hàng để tránh xung đột ghi đè.

**Quy tắc nghiệp vụ:**

- **`BR-32.1` (Chặn ghi trong lúc đóng băng):** Khi một Không gian làm việc đang trong trạng thái đóng băng di chuyển dữ liệu, mọi yêu cầu sửa đổi cơ hội của người dùng bị từ chối với thông báo rõ ràng là hệ thống đang bảo trì dữ liệu, không phải lỗi.

  **Lý do nghiệp vụ:** Nếu người dùng vẫn sửa được dữ liệu trong lúc một tiến trình nền đang di chuyển hàng loạt bản ghi, thay đổi của người dùng có thể bị tiến trình nền ghi đè mất mà không có cảnh báo — một dạng mất dữ liệu âm thầm nguy hiểm hơn nhiều so với việc từ chối tạm thời và rõ ràng.

- **`BR-32.2` (Chiều ngược — luôn có lối gỡ):** Trạng thái đóng băng luôn là tạm thời và phải được đội vận hành nền tảng chủ động gỡ khi tiến trình di chuyển hoàn tất hoặc thất bại. Không tồn tại tình huống một Không gian làm việc bị kẹt vĩnh viễn ở trạng thái đóng băng — nếu tiến trình di chuyển thất bại giữa chừng, đội vận hành phải gỡ cờ đóng băng trước khi xử lý sự cố, không để người dùng bị chặn ghi dữ liệu vô thời hạn.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-32.1.1` | Không gian làm việc đang trong trạng thái đóng băng di chuyển dữ liệu | Người dùng cố gắng sửa một cơ hội | Từ chối, thông báo hệ thống đang bảo trì dữ liệu |

---

#### FEAT-33 — Chống Tạo Cơ hội Trùng khi Chuyển đổi `[Đã triển khai]`

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

#### FEAT-34 — Khóa Đồng thời khi Sửa Cấu hình Phễu `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi hai quản trị viên cùng mở và sửa cấu hình một phễu bán hàng cùng lúc, ngăn người sửa sau ghi đè âm thầm lên thay đổi của người sửa trước.

**Quy tắc nghiệp vụ:**

- **`BR-34.1` (Phát hiện xung đột):** Mỗi lần lưu cấu hình phễu mang theo phiên bản dữ liệu đã đọc trước đó. Nếu phiên bản đó không còn khớp với phiên bản mới nhất (đã có người khác sửa trong lúc chờ), hệ thống từ chối lưu và báo rõ ai đã sửa gần nhất.

**Tiêu chí Chấp nhận:**

| Mã AC | Bối cảnh | Hành động | Kết quả mong đợi |
| --- | --- | --- | --- |
| `AC-34.1.1` | Quản trị viên A và B cùng mở cấu hình một phễu. A lưu thay đổi trước | B cố gắng lưu thay đổi của mình dựa trên phiên bản cũ | Từ chối, báo rõ A đã sửa gần nhất, yêu cầu B tải lại trước khi sửa tiếp |

---

## 4. Yêu cầu phi chức năng

### 4.1 Hiệu năng

- **NFR-01 (Tốc độ Bảng Kanban):** Tải tổng hợp và tải danh sách thẻ trên Kanban phản hồi trong thời gian ngắn kể cả khi một phễu có hàng chục nghìn cơ hội.
- **NFR-02 (Ổn định khi cuộn sâu):** Hiệu năng cuộn tải thêm thẻ trên Kanban không suy giảm khi người dùng đã cuộn qua nhiều trang.

### 4.2 Độ tin cậy & Toàn vẹn Dữ liệu

- **NFR-03 (Bất biến của lịch sử giai đoạn):** Bản ghi lịch sử chuyển giai đoạn không thể bị chỉnh sửa hoặc xóa (trong giới hạn số bản ghi tại `BR-12.1`).
- **NFR-04 (Toàn vẹn khi di chuyển phễu — CHƯA đạt):** Thao tác di chuyển hàng loạt cơ hội khi đóng phễu hiện **chưa** được bọc trong một giao dịch nguyên tử duy nhất — xem `BR-07.3`. Đây là một khoảng cách cần lấp trước khi coi `FEAT-07` là hoàn thiện ở mức toàn vẹn dữ liệu.
- **NFR-05 (Toàn vẹn khi xóa vĩnh viễn):** Thao tác xóa vĩnh viễn một cơ hội cùng dữ liệu liên quan tại `BR-04.3` phải thực thi trong một giao dịch nguyên tử duy nhất.

### 4.3 An toàn & Bảo mật

- **NFR-06 (Phân quyền dữ liệu & che số liệu tài chính):** Áp dụng nghiêm ngặt phạm vi dữ liệu (`BR-01.5`) và che số liệu tài chính (`FEAT-03`). Người dùng không có quyền không thể xem hoặc kéo thả cơ hội ngoài phạm vi.
- **NFR-07 (Bảo mật đường dẫn xuất dữ liệu):** Đường dẫn tải tệp xuất được bảo vệ và tự động hết hiệu lực sau thời hạn quy định (`BR-29.3`).

---

## 5. Ma trận quyền truy cập tính năng

| Mã FEAT | Tên tính năng nghiệp vụ | Nhân viên KD | Quản lý KD | Giám đốc KD | Quản trị viên | Chủ sở hữu | Tiến trình Hệ thống |
| --- | --- | :---: | :---: | :---: | :---: | :---: | :---: |
| `FEAT-01` | Tạo & Quản lý Cơ hội | Phạm vi gán | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | Đồng bộ tên tự động |
| `FEAT-02` | Đa Tiền tệ | Phạm vi gán | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-03` | Xem số liệu tài chính đầy đủ | Cần quyền riêng | Cần quyền riêng | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-04` | Thùng rác & Phục hồi | — | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | Dọn dẹp tự động |
| `FEAT-05` | Quản trị Phễu | Xem danh sách | Xem danh sách | Xem danh sách | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-06` | Cấu hình Giai đoạn & Xác suất | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-07` | Đóng Phễu & Di chuyển | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-08` | Xóa Giai đoạn | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-09` | Xem Bảng Kanban | Phạm vi gán | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-10` | Kéo thả Chuyển Giai đoạn | Phạm vi gán | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-11` | Bộ lọc Kanban | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | — |
| `FEAT-12` | Xem Lịch sử Giai đoạn | Phạm vi gán | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | Ghi tự động |
| `FEAT-13` | Cấu hình Rào cản Giai đoạn | — | — | — | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-14` | Báo cáo Vận tốc & Điểm nghẽn | — | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-15` | Đặt Lịch Chăm sóc | Phạm vi gán | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-16` | Nhận Nhắc nhở Lịch Chăm sóc | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | Quét & tạo tự động |
| `FEAT-18` | Đóng Cơ hội Thắng | Phạm vi gán | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-19` | Đóng Cơ hội Thua | Phạm vi gán | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-21` | Tái phân loại Cơ hội Đã đóng | — | Cần quyền riêng | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-22` | Gắn Vai trò Liên hệ | Phạm vi gán | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-23` | Xem Nguồn gốc Tiếp thị | Phạm vi gán | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-25` | Xem Dự báo Doanh thu | — | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-26` | Báo cáo Hiệu suất & Nguồn | — | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-28` | Thao tác Hàng loạt | — | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-29` | Nhập / Xuất Dữ liệu | — | Cần quyền riêng | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-30` | Dòng thời gian Hợp nhất | Phạm vi gán | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-32` | Đóng băng Di chuyển Dữ liệu nền | — | — | — | — | — | **Toàn quyền** |
| `FEAT-33` | Chuyển đổi Khách hàng Tiềm năng | Phạm vi gán | Phạm vi phòng ban | **Toàn quyền** | **Toàn quyền** | **Toàn quyền** | — |
| `FEAT-34` | Sửa Cấu hình Phễu (khóa đồng thời) | — | — | — | **Toàn quyền** | **Toàn quyền** | — |

*(Ghi chú: Phạm vi gán = chỉ bản ghi được phân công trực tiếp; Phạm vi phòng ban = toàn bộ bản ghi trong phòng ban quản lý; "Cần quyền riêng" = quyền hạn tách biệt khỏi quyền xem/sửa dữ liệu thông thường, phải cấp riêng — xem lý do nghiệp vụ tại `BR-01.2`, `BR-03.1`, `BR-21.1`. Cột "Tiến trình Hệ thống" chỉ có giá trị khi vai trò người dùng hoàn toàn không thao tác trực tiếp (`FEAT-32`) hoặc khi Tiến trình Hệ thống thực hiện một phần việc tự động song song với thao tác của người dùng (`FEAT-04`, `FEAT-12`, `FEAT-16`) — không phải một cột loại trừ 5 cột còn lại. Các FEAT thuộc nhu cầu nghiệp vụ mới — FEAT-17, 20, 24, 27, 31 — không có ở ma trận này vì chưa triển khai.)*

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

## 7. Giới hạn hiện tại & Vấn đề chính sách cần quyết định tiếp

1. **Quy đổi đa tiền tệ về một đồng tiền cơ sở.** Hiện báo cáo hợp nhất chỉ cảnh báo trộn tiền tệ (`BR-02.2`) chứ chưa tự động quy đổi. Cần quyết định: nguồn tỷ giá, tần suất cập nhật, và thời điểm chốt tỷ giá cho các cơ hội đã đóng (để biến động tỷ giá tương lai không làm sai lệch báo cáo tài chính lịch sử).

2. **Bảng giá & Chi tiết Dòng sản phẩm (CPQ).** Hiện cơ hội chỉ có một trường giá trị tổng nhập trực tiếp, không có cơ cấu chi tiết theo từng sản phẩm/dịch vụ, số lượng, đơn giá, chiết khấu, thuế. Đây là nhu cầu lớn, kéo theo nhiều quyết định phụ thuộc (định dạng chiết khấu theo dòng hay theo tổng, quy tắc làm tròn thuế) nên chưa đủ chín muồi để đặc tả trong phiên bản này.

3. **Quy trình Phê duyệt Chiết khấu Đa cấp.** Phụ thuộc trực tiếp vào quyết định số 2 (không có cơ cấu chiết khấu chi tiết thì chưa xác định được cái gì cần phê duyệt). Hoãn tới khi CPQ được thiết kế.

4. **Trạng thái Tạm ngưng Cơ hội (On Hold).** Nhu cầu thật (khách hàng hoãn kế hoạch mua ngoài ý muốn) nhưng chưa quyết định: tạm ngưng có phải là nhánh trạng thái thứ ba ngoài "đang mở/đã đóng" hay chỉ là một cờ trên cơ hội đang mở; cách tương tác với `FEAT-17` (nhận diện nguội lạnh) khi cả hai đều chưa được xây.

5. **Phân chia Doanh số Đồng phụ trách (`FEAT-24`) và Hạn ngạch Doanh số (`FEAT-27`).** Hai nhu cầu liên quan chặt tới nhau (phân chia ảnh hưởng cách tính hạn ngạch) nhưng thiết kế chi tiết chưa hoàn thiện — đặc biệt là cách xử lý khi một người tham gia rời khỏi cơ hội hoặc rời tổ chức giữa kỳ.

6. **Nhóm Dự báo theo mức độ tin cậy (Forecast Categories).** Nhu cầu phân loại cơ hội thành các nhóm tin cậy khác nhau (ví dụ "chắc chắn chốt trong kỳ" và "có triển vọng") tách biệt khỏi xác suất theo giai đoạn — vẫn đang ở giai đoạn ý tưởng, chưa có quyết định về việc ai gán nhãn và nhãn đó ảnh hưởng báo cáo dự báo (`FEAT-25`) như thế nào.

7. **Rào cản giai đoạn theo tài liệu đính kèm và theo vai trò liên hệ bắt buộc (`BR-13.4`, `BR-22.3`).** Đã có quyết định chấp nhận nhu cầu, nhưng chưa thiết kế cách lưu trữ yêu cầu tài liệu và cách xác thực "đã đính kèm đúng loại tài liệu yêu cầu".

8. **Tách lại cơ hội sau khi đã tự động gộp khi chuyển đổi (`BR-33.3`).** Chưa có cơ chế hoàn tác việc gộp khách hàng tiềm năng vào một cơ hội có sẵn. Cần quyết định: có cần một sổ cái hoàn tác tương tự cơ chế đã có cho gộp Khách hàng/Doanh nghiệp hay không, hay chỉ cần hướng dẫn thao tác thủ công (tạo cơ hội mới, chuyển các bản ghi liên quan sang) là đủ.

9. **Hai giai đoạn kết thúc bắt buộc mỗi phễu (`BR-06.2b`).** Đã có quyết định chấp nhận nhu cầu (một phễu phải luôn có đúng một giai đoạn Thắng và một giai đoạn Thua), nhưng ràng buộc này chưa được thực thi trong hệ thống — cần bổ sung kiểm tra khi tạo/lưu phễu, và quyết định cách xử lý các phễu đã tồn tại từ trước mà chưa thỏa mãn điều kiện này (bắt buộc sửa ngay hay cho phép tồn tại tạm thời kèm cảnh báo).

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
| `CFG-DEAL-01` | `BR-04.2` | Thời hạn lưu cơ hội trong thùng rác trước khi xóa vĩnh viễn | 30 ngày | *(chưa cấu hình được theo tenant — hiện là hằng số hệ thống, ghi nhận tại Mục 7 để nâng cấp)* | — | **Cố định** (khoảng cách kỹ thuật cần lấp) |
| `CFG-DEAL-02` | `BR-06.4` | Bật/tắt khóa nhảy cóc giai đoạn (theo từng phễu) | Tắt | Bật / Tắt | Quản trị viên Không gian làm việc | **Tự do** |
| `CFG-DEAL-03` | `BR-15.1` | Khoảng thời gian mặc định tới lịch chăm sóc tiếp theo khi tạo cơ hội mới | 24 giờ | 1–168 giờ *(miền đề xuất — ở phạm vi phát hành hiện tại chưa được ép buộc kỹ thuật, xem ghi chú cuối bảng)* | Quản trị viên Không gian làm việc | **Tự do** |
| `CFG-DEAL-04` | `BR-17.1` | Ngưỡng số ngày không tương tác để đánh dấu Cơ hội Nguội Lạnh | 14 ngày *(đề xuất — tính năng chưa triển khai)* | 1–90 ngày | Quản lý Kinh doanh *(khác các tham số còn lại — xem lý do bên dưới bảng)* | **Tự do** |
| `CFG-DEAL-05` | `BR-18.2` | Bắt buộc có người phụ trách trước khi cho phép đóng thắng | Bật | Bật / Tắt | Quản trị viên Không gian làm việc | **Tự do** |

*Ghi chú:* Chu kỳ quét lịch chăm sóc (`BR-16.1`, 5 phút), ngưỡng ngừng nhắc lịch quá cũ (`BR-16.4`, 7 ngày), giới hạn dung lượng nhập (`BR-29.1`, 50MB), giới hạn số bản ghi thao tác hàng loạt và thời hạn hiệu lực đường dẫn tải xuất (`BR-29.3`) hiện là **hằng số vận hành cấp hệ thống**, áp dụng thống nhất cho mọi Không gian làm việc — không đưa vào bảng tham số theo tenant vì đây là giới hạn kỹ thuật bảo vệ hạ tầng dùng chung, không phải một lựa chọn nghiệp vụ khác nhau giữa các doanh nghiệp.

*Ghi chú về `CFG-DEAL-03`:* miền giá trị 1–168 giờ là đề xuất nghiệp vụ, hiện **chưa được ép buộc bằng kỹ thuật** — hệ thống hiện chấp nhận mọi giá trị số dương mà không chặn ngoài miền này. Trước khi coi tham số này là hoàn thiện, cần bổ sung việc kiểm tra miền giá trị khi lưu cấu hình.

*Ghi chú về thẩm quyền của `CFG-DEAL-04`:* đây là tham số duy nhất trong bảng có thẩm quyền thay đổi khác — Quản lý Kinh doanh thay vì Quản trị viên Không gian làm việc. Đây là quyết định có chủ đích: ngưỡng nguội lạnh là một lựa chọn vận hành bán hàng thường nhật (bao lâu thì coi là bị bỏ quên), gần với cách một Quản lý Kinh doanh điều chỉnh nhịp độ chăm sóc đội mình hơn là một quyết định cấu hình cấu trúc hệ thống — khác bản chất với `CFG-DEAL-02`/`03`/`05` (đều là quy tắc ràng buộc dữ liệu hoặc quy trình, thuộc thẩm quyền Quản trị viên). Khi `FEAT-17` được triển khai, vai trò "Quản lý Kinh doanh" cần được bổ sung vào mô tả quyền hạn tại Mục 2.2 để phản ánh đúng quyền cấu hình tham số này.
