# SRS — Phân hệ Quản lý Công việc & Hoạt động (Tasks & Activities Management)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Chuẩn PM/BA (Version 2.0) |
| **Module** | CRM — Phân hệ Quản lý Công việc & Hoạt động (Tasks & Activities Management) |
| **Ngày cập nhật** | 2026-08-28 |
| **Phiên bản** | v2.0 (Target Standard) |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`contacts-srs.md`](./contacts-srs.md), [`deals-pipeline-srs.md`](./deals-pipeline-srs.md), [`tickets-srs.md`](./tickets-srs.md), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) |

## Ghi chú về nguồn gốc tài liệu

Tài liệu này được xây dựng thông qua quy trình chuẩn hoá 4 bước:
1. **Khảo sát toàn diện mã nguồn thực tế (As-Is):** Khảo sát toàn bộ hệ thống API, schemas, recurring task engines, task reminder services, activity log services, bulk processors và export workers trong `crm-api` (`src/tasks/`, `src/activity-log/`) và `crm-web` (`src/features/tasks/`).
2. **Rà soát & Đối chiếu Chuyên sâu:** Kiểm tra chéo từng cơ chế sinh việc lặp lại định kỳ (`RecurringTaskService`), quét gửi thông báo nhắc nhở trước hạn chót (`TaskReminderService`), liên kết đa thực thể (`contactId`, `accountId`, `dealId`, `ticketId`) và cập nhật dòng thời gian tương tác.
3. **Chuẩn hoá Nghiệp vụ Business First:** Đối chiếu với các chuẩn mực B2B SaaS CRM hàng đầu thế giới (HubSpot Tasks & Sales Sequences, Salesforce Activities & Calendar, Pipedrive Activities & Scheduler, Asana/Monday.com Task Management), loại bỏ các hạn chế kỹ thuật và hoàn thiện trải nghiệm quản lý công việc liền mạch.
4. **Đóng băng Đặc tả Mục tiêu:** Hoàn thiện bộ 30 tính năng nghiệp vụ cốt lõi, ma trận phân quyền, 6 kịch bản UAT và danh mục chính sách nghiệp vụ.

**Quy ước nhãn trạng thái:** Mỗi tính năng (FEAT) và quy tắc nghiệp vụ (BR) được gắn nhãn trạng thái:
- **`[Đã triển khai]`** — Phản ánh các tính năng nền tảng đã sẵn sàng và đang vận hành thực tế trong hệ thống.
- **`[Yêu cầu mới]`** — Các tính năng và quy tắc nâng cấp chuẩn Business To-Be được bổ sung để hoàn thiện trải nghiệm quản trị công việc toàn diện.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả chi tiết toàn bộ nghiệp vụ quản trị Công việc (Tasks) và Hoạt động kinh doanh (Activities) trong hệ thống CRM B2B SaaS:
1. **Quản trị Công việc & Tác vụ (Tasks Management):** Lên kế hoạch, giao việc, phân loại mức độ ưu tiên, theo dõi hạn chót và trạng thái thực hiện của mọi nhân viên.
2. **Chuẩn hóa Hoạt động Kinh doanh (Sales Activities):** Quản lý 6 loại hoạt động cốt lõi: Cuộc gọi (Call), Email, Cuộc họp (Meeting), Trình diễn Sản phẩm (Demo), Chăm sóc Tiếp theo (Follow-up) và Việc cần làm (To-do).
3. **Tự động hóa Công việc Lặp lại Định kỳ (Recurring Tasks):** Tự động sinh công việc mới theo chu kỳ định sẵn (Hằng ngày, Hằng tuần, Hằng tháng, Hằng năm) để duy trì quy trình chăm sóc khách hàng đều đặn.
4. **Nhắc nhở Tự động & Chống Bỏ quên (Reminders & Overdue Tracking):** Phát thông báo chuông và gửi email nhắc nhở trước hạn chót (15 phút, 1 giờ, 1 ngày) và gắn cờ cảnh báo công việc quá hạn.
5. **Liên kết Đa Thực thể Liền mạch (Multi-Entity Association):** Kết nối công việc đồng thời với Khách hàng cá nhân (Contact), Doanh nghiệp (Account), Cơ hội bán hàng (Deal) và Vé hỗ trợ (Ticket).
6. **Không gian Làm việc Thông minh (My Tasks Board & Calendar View):** Cung cấp các góc nhìn làm việc tối ưu: Bảng theo hạn chót (Hôm nay, Quá hạn, Tuần này), Danh sách lọc tùy chỉnh và Lịch biểu trực quan (Calendar View).
7. **Nhật ký Hoạt động Hợp nhất (Activity Feed & Audit Log):** Lưu vết toàn bộ lịch sử tương tác của nhân viên với khách hàng theo thứ tự thời gian.
8. **Đo lường Năng suất Làm việc (Productivity Reports):** Báo cáo số lượng cuộc gọi, cuộc họp đã thực hiện, tỷ lệ hoàn thành đúng hạn và bảng xếp hạng nỗ lực của nhân viên kinh doanh.

### 1.2 Phạm vi

Tài liệu bao gồm 10 nhóm chức năng cốt lõi:
- **Nhóm A: Quản trị Công việc (Tasks CRUD):** Tạo mới, cập nhật, chi tiết, hạn chót (`dueDate`), mức độ ưu tiên và Thùng rác phục hồi.
- **Nhóm B: Vòng đời & Trạng thái Công việc (Task Lifecycle):** Vòng đời 4 trạng thái (`PENDING`, `IN_PROGRESS`, `COMPLETED`, `CANCELLED`), tự động ghi nhận `completedAt`, đánh dấu hoàn thành 1-click.
- **Nhóm C: Phân loại Hoạt động Bán hàng (Sales Activity Types):** 6 loại hoạt động chuẩn (Call, Meeting, Email, Demo, Follow-up, To-do), ghi nhận kết quả tương tác.
- **Nhóm D: Công việc Lặp lại Định kỳ (Recurring Tasks):** Cấu hình lặp lại hằng ngày/tuần/tháng/năm, tự động sinh công việc kỳ tiếp theo khi hoàn tất.
- **Nhóm E: Nhắc nhở Tự động & Quản lý Quá hạn (Reminders & Overdue Tracking):** Đặt lịch nhắc nhở trước hạn (`reminderAt`), thông báo chuông In-App, cảnh báo quá hạn màu đỏ.
- **Nhóm F: Liên kết Đa Thực thể (Multi-Entity Association):** Gắn công việc vào Contact, Account, Deal, Ticket.
- **Nhóm G: Bảng Làm việc Cá nhân & Lịch Biểu (My Tasks & Calendar View):** Bảng làm việc theo ngày, bộ lọc "Việc của tôi", giao diện Lịch trực quan.
- **Nhóm H: Nhật ký Hoạt động Hợp nhất (Activity Feed & Logging):** Ghi nhận hoạt động nhanh trực tiếp trên hồ sơ khách hàng/deal, tự động làm mới thời gian hoạt động (`touchActivity`).
- **Nhóm I: Thao tác Hàng loạt & Xuất Dữ liệu (Bulk Operations & Export):** Đổi người phụ trách hàng loạt, hoàn thành hàng loạt, xóa hàng loạt, xuất file CSV bảo mật qua token.
- **Nhóm J: Báo cáo Năng suất Hoạt động (Activity Productivity Reports):** Báo cáo khối lượng cuộc gọi/cuộc họp, tỷ lệ hoàn thành đúng hạn và bảng theo dõi năng suất.

**Ngoài phạm vi (thuộc về các tài liệu SRS chuyên biệt khác):**
- **Nghiệp vụ Quản trị Cơ hội Bán hàng & Phễu Bán hàng:** Thuộc về [`deals-pipeline-srs.md`](./deals-pipeline-srs.md).
- **Nghiệp vụ Quản lý Vé Hỗ trợ & SLA Dịch vụ:** Thuộc về [`tickets-srs.md`](./tickets-srs.md).
- **Nghiệp vụ Quản lý Hồ sơ Khách hàng & Doanh nghiệp:** Thuộc về [`contacts-srs.md`](./contacts-srs.md).

### 1.3 Đối tượng đọc

- **Product Owner / Business Analyst:** Căn cứ thiết kế backlog, quy trình nghiệp vụ và tiêu chí nghiệm thu phân hệ công việc.
- **Đội ngũ Phát triển (Frontend / Backend):** Căn cứ thiết kế API, schemas, tiến trình worker sinh việc định kỳ (`cron`) và trải nghiệm bảng công việc.
- **Đội ngũ Kiểm thử (QA/QC):** Thiết kế bộ kịch bản kiểm thử chức năng và kiểm thử tự động cho toàn bộ các quy tắc sinh việc và nhắc việc.
- **Quản lý Kinh doanh & Nhân viên:** Nắm rõ cách thức lập kế hoạch công việc hằng ngày và tiêu chuẩn báo cáo hoạt động.

### 1.4 Thuật ngữ & Viết tắt

| Thuật ngữ | Định nghĩa nghiệp vụ |
| --- | --- |
| **Công việc / Tác vụ (Task)** | Hành động cần thực hiện của nhân viên (gọi điện, họp, gửi báo giá, demo) có hạn chót cụ thể. |
| **Loại Hoạt động (Activity Type)** | Phân loại hình thức tương tác: Cuộc gọi, Email, Cuộc họp, Demo, Chăm sóc tiếp theo, Việc cần làm. |
| **Công việc Lặp lại (Recurring Task)** | Quy tắc tự động tạo mới công việc theo chu kỳ định kỳ (ngày, tuần, tháng, năm). |
| **Hạn chót (Due Date)** | Ngày và giờ cam kết phải hoàn thành công việc. |
| **Nhắc nhở (Task Reminder)** | Thời điểm hệ thống phát thông báo cảnh báo trước khi đến hạn chót. |
| **Nhật ký Hoạt động (Activity Feed)** | Luồng ghi nhận chi tiết toàn bộ các tương tác đã diễn ra với khách hàng. |
| **Liên kết Đa Thực thể** | Khả năng gắn 1 việc vào đồng thời Khách hàng, Doanh nghiệp, Cơ hội bán hàng và Vé hỗ trợ. |

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

Trong hoạt động kinh doanh và dịch vụ khách hàng hằng ngày:
- Nhân viên kinh doanh thường xuyên quên lịch gọi lại (Follow-up), quên gửi báo giá đúng hẹn hoặc bỏ sót lịch hẹn họp với khách hàng quan trọng.
- Người quản lý không nắm được hôm nay nhân viên đã gọi bao nhiêu cuộc, đã gặp bao nhiêu khách hàng, tỷ lệ hoàn thành công việc đúng hạn là bao nhiêu.
- Công việc định kỳ (ví dụ: gọi điện chăm sóc khách hàng VIP vào ngày 15 hằng tháng) phải tạo thủ công lặp đi lặp lại rất dễ bị sót.
- Lịch sử tương tác (gọi điện trao đổi những gì, thống nhất điều khoản nào) bị ghi chép rời rạc trong sổ tay cá nhân thay vì lưu trữ tập trung trên hồ sơ khách hàng.

Module Tasks & Activities giải quyết toàn diện các vấn đề trên bằng cách xây dựng một trung tâm điều hành công việc trực quan, tự động nhắc nhở trước hạn chót, tự động sinh việc định kỳ và hợp nhất nhật ký tương tác vào dòng thời gian 360 độ của CRM.

### 2.2 Vai trò người dùng (Actor)

| Actor | Mô tả vai trò và quyền hạn |
| --- | --- |
| **Nhân viên Kinh doanh / Hỗ trợ (Team Member)** | Tạo công việc cá nhân, nhận việc được giao, hoàn thành công việc 1-click, ghi nhận nhật ký cuộc gọi/họp và nhận thông báo nhắc việc. |
| **Quản lý Đội ngũ (Team Lead / Sales Manager)** | Giao việc cho nhân viên trong nhóm, theo dõi bảng tiến độ công việc chung, duyệt dời hạn chót và xem báo cáo năng suất. |
| **Giám đốc Kinh doanh (VP of Sales)** | Xem báo cáo tổng hợp nỗ lực hoạt động toàn công ty (Tổng số cuộc gọi, cuộc họp, tỷ lệ hoàn thành đúng hạn). |
| **Người theo dõi / Cộng tác (Task Watcher / Collaborator)** | Được gắn vào công việc để theo dõi tiến độ, nhận thông báo khi công việc hoàn thành hoặc thay đổi hạn chót, có quyền thêm ghi chú nội bộ nhưng không phải người chịu trách nhiệm chính. |
| **Quản trị viên Không gian làm việc (Tenant Admin)** | Cấu hình danh mục loại hoạt động, quyền truy cập công việc và xuất dữ liệu công việc toàn tổ chức. |
| **Chủ sở hữu Không gian làm việc (Tenant Owner)** | Toàn quyền xem và quản trị toàn bộ công việc và nhật ký hoạt động của không gian làm việc. |
| **Tiến trình Hệ thống (System Engine / Background Workers)** | Tự động quét gửi thông báo nhắc nhở (`TaskReminderService`), tự động sinh công việc định kỳ (`RecurringTaskService`) và dọn dẹp thùng rác. |

### 2.3 Bảng tổng hợp 30 tính năng nghiệp vụ

| Nhóm | Mã FEAT | Tên tính năng nghiệp vụ | Trạng thái |
| --- | --- | --- | --- |
| **A. Quản trị Công việc** | `FEAT-01` | Tạo mới & Quản lý Công việc (Task CRUD) | `[Đã triển khai]` |
| | `FEAT-02` | Hồ sơ Chi tiết Công việc (Task Detail View) | `[Đã triển khai]` |
| | `FEAT-03` | Quản lý Mức độ Ưu tiên Công việc (Low, Medium, High, Urgent) | `[Đã triển khai]` |
| | `FEAT-04` | Thiết lập Hạn chót & Giờ hoàn thành (Due Date & Due Time) | `[Đã triển khai]` |
| | `FEAT-05` | Thùng rác Công việc & Phục hồi Bản ghi (Task Recycle Bin & Restore) | `[Đã triển khai]` |
| **B. Vòng đời & Trạng thái** | `FEAT-06` | Quản trị Vòng đời 4 Trạng thái Công việc (Pending, In Progress, Completed, Cancelled)| `[Đã triển khai]` |
| | `FEAT-07` | Đánh dấu Hoàn thành Công việc 1-Click (1-Click Mark Completed) | `[Đã triển khai]` |
| | `FEAT-08` | Tự động Ghi nhận Thời điểm Hoàn tất (Auto Stamp CompletedAt) | `[Đã triển khai]` |
| | `FEAT-09` | Khôi phục Trạng thái Chưa hoàn tất (Undo Completion) | `[Đã triển khai]` |
| **C. Phân loại Hoạt động** | `FEAT-10` | Chuẩn hóa 6 Loại Hoạt động Bán hàng (Call, Meeting, Email, Demo, Follow-up, To-do)| `[Đã triển khai]` |
| | `FEAT-11` | Ghi nhận Kết quả Cuộc gọi & Thời lượng Trao đổi (Call Outcome & Duration) | `[Yêu cầu mới]` |
| | `FEAT-12` | Ghi nhận Biên bản Cuộc họp & Địa điểm Gặp gỡ (Meeting Minutes & Location)| `[Yêu cầu mới]` |
| **D. Công việc Lặp lại Định kỳ** | `FEAT-13` | Thiết lập Quy tắc Công việc Lặp lại Định kỳ (Recurring Task Config) | `[Đã triển khai]` |
| | `FEAT-14` | Tự động Sinh Công việc Kỳ tiếp theo khi Hoàn thành (Auto Generate Next Instance)| `[Đã triển khai]` |
| | `FEAT-15` | Giới hạn Số lần Lặp hoặc Ngày Kết thúc Chu kỳ (Recurrence End Condition)| `[Đã triển khai]` |
| **E. Nhắc nhở & Quá hạn** | `FEAT-16` | Thiết lập Thời điểm Nhắc nhở Tự động (Task Reminder Scheduling) | `[Đã triển khai]` |
| | `FEAT-17` | Tiến trình Quét & Bắn Thông báo Nhắc việc Đúng giờ (Reminder Worker) | `[Đã triển khai]` |
| | `FEAT-18` | Tự động Nhận diện & Đánh dấu Công việc Quá hạn (Overdue Tasks Detection)| `[Đã triển khai]` |
| **F. Liên kết Đa Thực thể** | `FEAT-19` | Liên kết Công việc với Khách hàng Cá nhân & Doanh nghiệp (Contact/Account Link)| `[Đã triển khai]` |
| | `FEAT-20` | Liên kết Công việc với Cơ hội Bán hàng (Deal-Task Linkage) | `[Đã triển khai]` |
| | `FEAT-21` | Liên kết Công việc với Vé Hỗ trợ Kỹ thuật (Ticket-Task Linkage) | `[Đã triển khai]` |
| **G. Bảng Làm việc & Lịch biểu** | `FEAT-22` | Bảng Công việc Thông minh theo Hạn chót (Today, Overdue, Upcoming Views)| `[Đã triển khai]` |
| | `FEAT-23` | Giao diện Lịch Biểu Công việc Trực quan (Tasks Calendar View) | `[Yêu cầu mới]` |
| | `FEAT-24` | Bộ lọc Phân loại Công việc theo Người phụ trách & Mức độ Ưu tiên | `[Đã triển khai]` |
| **H. Nhật ký Hoạt động** | `FEAT-25` | Ghi nhanh Hoạt động Trực tiếp trên Hồ sơ Khách hàng / Deal (Quick Activity Log)| `[Đã triển khai]` |
| | `FEAT-26` | Tự động Đánh thức Hoạt động Giao dịch khi Ghi nhận Việc (`touchActivity`) | `[Đã triển khai]` |
| **I. Thao tác Hàng loạt & Xuất** | `FEAT-27` | Hoàn thành, Đổi Người phụ trách & Xóa Hàng loạt (Bulk Task Operations) | `[Đã triển khai]` |
| | `FEAT-28` | Xuất Danh sách Công việc Dạng Luồng CSV Bảo mật qua Token (Export Tasks)| `[Đã triển khai]` |
| **J. Báo cáo Năng suất** | `FEAT-29` | Báo cáo Khối lượng Hoạt động Cuộc gọi / Cuộc họp theo Nhân viên | `[Đã triển khai]` |
| | `FEAT-30` | Báo cáo Tỷ lệ Hoàn thành Đúng hạn & Năng suất Bán hàng (On-Time Completion Rate)| `[Đã triển khai]` |
| **K. Mở rộng Tiện ích Công việc** | `FEAT-31` | Danh sách Kiểm tra & Đính kèm Tệp Tài liệu (Task Checklists & File Attachments)| `[Yêu cầu mới]` |

---

## 3. Đặc tả yêu cầu chức năng

## A. QUẢN TRỊ CÔNG VIỆC (TASKS MANAGEMENT)

### FEAT-01 — Tạo mới & Quản lý Công việc (Task CRUD) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép người dùng tạo mới nhiệm vụ cho bản thân hoặc giao việc cho đồng nghiệp trong không gian làm việc.

**Actor:** Nhân viên Kinh doanh, Nhân viên Hỗ trợ, Quản lý Đội ngũ.

**Quy tắc nghiệp vụ:**
- `BR-01.1 (Thông tin bắt buộc)`: Tiêu đề công việc (`title`), Hạn chót (`dueDate`), Người phụ trách (`ownerId`).
- `BR-01.2 (Quyền sở hữu)`: Người tạo mặc định là người phụ trách trừ khi chỉ định thành viên khác; Đơn vị tổ chức (`orgUnitId`) được tự động gán theo phòng ban của người phụ trách.

---

### FEAT-02 — Hồ sơ Chi tiết Công việc (Task Detail View) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Màn hình xem chi tiết thông tin công việc, danh sách các thực thể liên quan (Khách hàng, Deal, Vé hỗ trợ), lịch nhắc nhở và lịch sử thay đổi.

**Actor:** Mọi người dùng có quyền xem Task.

---

### FEAT-03 — Quản lý Mức độ Ưu tiên Công việc `[Đã triển khai]`

**Mô tả nghiệp vụ:** Phân cấp độ khẩn cấp của công việc qua 4 mức độ:
- **`LOW` (Thấp):** Việc phụ, có thể dời lịch nếu bận.
- **`MEDIUM` (Trung bình):** Công việc tiêu chuẩn hằng ngày (Mặc định).
- **`HIGH` (Cao):** Việc quan trọng cần hoàn thành đúng hạn.
- **`URGENT` (Khẩn cấp):** Sự cố nóng, hợp đồng lớn cần xử lý ngay lập tức.

---

### FEAT-04 — Thiết lập Hạn chót & Giờ hoàn thành (Due Date & Time) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép chọn Ngày đến hạn (`dueDate`) và Giờ cụ thể (`dueTime` — ví dụ: `15:30 29/08/2026`).

**Actor:** Người tạo/sửa Task.

---

### FEAT-05 — Thùng rác Công việc & Phục hồi Bản ghi (Task Recycle Bin & Restore) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi xóa công việc, hệ thống thực hiện Xóa mềm (Soft Delete) và lưu trữ trong Thùng rác 30 ngày. Cho phép phục hồi lại qua API `POST /api/v1/tasks/:id/restore`.

**Actor:** Quản trị viên Workspace, Người có quyền `delete` trên Tasks.

---

## B. VÒNG ĐỜI & TRẠNG THÁI CÔNG VIỆC (TASK LIFECYCLE)

### FEAT-06 — Quản trị Vòng đời 4 Trạng thái Công việc `[Đã triển khai]`

**Mô tả nghiệp vụ:** Quản lý tiến độ thực thi qua 4 trạng thái chuẩn:
1. **Chờ thực hiện (`PENDING`):** Việc đã lên lịch, chưa bắt đầu làm.
2. **Đang thực hiện (`IN_PROGRESS`):** Nhân viên đang xử lý công việc.
3. **Đã hoàn thành (`COMPLETED`):** Công việc đã thực hiện xong.
4. **Đã hủy (`CANCELLED`):** Công việc không còn cần thiết thực hiện.

---

### FEAT-07 — Đánh dấu Hoàn thành Công việc 1-Click `[Đã triển khai]`

**Mô tả nghiệp vụ:** Trên danh sách công việc hoặc thẻ lịch biểu, nhân viên chỉ cần nhấp vào hộp kiểm (Checkbox) tròn để đánh dấu hoàn thành nhiệm vụ ngay lập tức.

**Actor:** Người phụ trách công việc.

---

### FEAT-08 — Tự động Ghi nhận Thời điểm Hoàn tất (Auto Stamp CompletedAt) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi trạng thái chuyển sang `COMPLETED`, hệ thống tự động ghi nhận `completedAt = now()`.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-08.1`: So sánh `completedAt` với `dueDate` để xác định công việc được hoàn thành **Đúng hạn (On-Time)** hay **Trễ hạn (Late)** cho báo cáo KPI.

---

### FEAT-09 — Khôi phục Trạng thái Chưa hoàn tất (Undo Completion) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Nếu bấm nhầm hoàn thành, nhân viên có thể bỏ chọn hộp kiểm để đưa công việc quay lại trạng thái `PENDING`, trường `completedAt` tự động xóa về `null`.

**Actor:** Người phụ trách công việc.

---

## C. PHÂN LOẠI HOẠT ĐỘNG BÁN HÀNG (SALES ACTIVITY TYPES)

### FEAT-10 — Chuẩn hóa 6 Loại Hoạt động Bán hàng `[Đã triển khai]`

**Mô tả nghiệp vụ:** Phân loại công việc theo đúng tính chất hoạt động:
- **`CALL` (Cuộc gọi):** Gọi điện thoại tư vấn, gọi chăm sóc định kỳ.
- **`EMAIL` (Email):** Soạn và gửi email báo giá, tài liệu.
- **`MEETING` (Cuộc họp):** Họp trực tiếp hoặc họp online qua Zoom/Google Meet.
- **`DEMO` (Trình diễn giải pháp):** Buổi trình diễn tính năng sản phẩm cho khách hàng.
- **`FOLLOW_UP` (Chăm sóc tiếp theo):** Tái liên lạc sau cuộc họp hoặc báo giá.
- **`TODO` (Việc cần làm):** Các tác vụ nội bộ khác.

---

### FEAT-11 — Ghi nhận Kết quả Cuộc gọi & Thời lượng Trao đổi `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khi hoàn tất công việc loại `CALL`, cho phép nhân viên chọn Kết quả cuộc gọi (*Đã kết nối thành công, Khách bận gọi lại sau, Sai số, Không nghe máy*) và nhập thời lượng cuộc gọi (phút).

**Actor:** Nhân viên Kinh doanh.

---

### FEAT-12 — Ghi nhận Biên bản Cuộc họp & Địa điểm Gặp gỡ `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khi hoàn tất công việc loại `MEETING`, cho phép lưu trữ tóm tắt biên bản cuộc họp, danh sách người tham dự và địa điểm tổ chức.

**Actor:** Nhân viên Kinh doanh.

---

## D. CÔNG VIỆC LẶP LẠI ĐỊNH KỲ (RECURRING TASKS)

### FEAT-13 — Thiết lập Quy tắc Công việc Lặp lại Định kỳ `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép thiết lập tần suất lặp lại cho công việc:
- **Hằng ngày (`DAILY`):** Lặp lại mỗi N ngày.
- **Hằng tuần (`WEEKLY`):** Lặp lại vào các ngày thứ trong tuần (ví dụ: Thứ Hai và Thứ Năm).
- **Hằng tháng (`MONTHLY`):** Lặp lại vào ngày cố định trong tháng (ví dụ: Ngày 15 hàng tháng).
- **Hằng năm (`YEARLY`):** Lặp lại vào ngày này mỗi năm (ví dụ: Ngày sinh nhật khách hàng / Ngày tái ký hợp đồng).

**Actor:** Người tạo Task.

---

### FEAT-14 — Tự động Sinh Công việc Kỳ tiếp theo khi Hoàn thành `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi người dùng đánh dấu Hoàn thành (`COMPLETED`) công việc kỳ hiện tại, tiến trình `RecurringTaskService` tự động tính toán hạn chót mới và sinh ra một bản ghi công việc mới cho kỳ tiếp theo với đầy đủ thông tin và các liên kết thực thể cũ.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-14.1 (Tính Hạn chót Kỳ tiếp theo)`: Hạn chót của công việc kỳ tiếp theo được tính bằng: `Hạn chót cũ + Khoảng cách chu kỳ` (ví dụ: `dueDate_Next = originalDueDate + 7 days`), không phụ thuộc vào thời điểm nhân viên bấm hoàn thành thực tế, nhằm bảo toàn đúng lịch định kỳ chuẩn.
- `BR-14.2 (Xử lý Ngày cuối tháng) [Yêu cầu mới]`: Đối với công việc lặp hằng tháng vào ngày 29, 30 hoặc 31, nếu tháng tiếp theo có ít ngày hơn (ví dụ tháng 2 chỉ có 28/29 ngày, tháng 4 chỉ có 30 ngày), hệ thống tự động gán hạn chót vào ngày cuối cùng của tháng đó.

---

### FEAT-15 — Giới hạn Số lần Lặp & Xử lý Khi Hủy Chu kỳ `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép cấu hình điều kiện dừng lặp lại: **Không bao giờ dừng**, **Dừng sau N lần lặp** hoặc **Dừng vào ngày `endDate`**, và quy tắc xử lý khi người dùng hủy một công việc định kỳ.

**Actor:** Người tạo Task, Người phụ trách.

**Quy tắc nghiệp vụ:**
- `BR-15.1 (Điều kiện dừng chu kỳ)`: Khi đạt số lần lặp tối đa hoặc vượt quá `endDate`, chuỗi lặp tự động kết thúc và không sinh thêm công việc mới.
- `BR-15.2 (Xử lý khi Hủy Công việc Định kỳ) [Yêu cầu mới]`: Khi người dùng chuyển trạng thái công việc định kỳ sang `CANCELLED`, hệ thống hiển thị hộp thoại xác nhận:
  - **Lựa chọn 1 (Hủy riêng kỳ này):** Chỉ hủy công việc hiện tại, hệ thống vẫn tự động sinh công việc cho kỳ tiếp theo theo đúng lịch.
  - **Lựa chọn 2 (Dừng toàn bộ chuỗi lặp):** Hủy công việc hiện tại và vô hiệu hóa vĩnh viễn cấu hình lặp lại.

---

## E. NHẮC NHỞ TỰ ĐỘNG & QUẢN LÝ QUÁ HẠN (REMINDERS & OVERDUE)

### FEAT-16 — Thiết lập Thời điểm Nhắc nhở Tự động `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép chọn thời điểm gửi thông báo nhắc nhở trước hạn chót: *Đúng giờ hạn chót, Trước 15 phút, Trước 1 giờ, Trước 1 ngày* hoặc tùy chỉnh thời điểm `reminderAt`.

**Actor:** Người tạo/sửa Task.

---

### FEAT-17 — Tiến trình Quét & Bắn Thông báo Nhắc việc Đúng giờ `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tiến trình `TaskReminderService` định kỳ quét mỗi phút các công việc có `reminderAt <= now()` và `reminderSentAt === null` để gửi thông báo chuông In-App và thông báo đẩy tới người phụ trách.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-17.1`: Đánh dấu `reminderSentAt = now()` để đảm bảo mỗi nhắc nhở chỉ gửi đúng 1 lần duy nhất.

---

### FEAT-18 — Tự động Nhận diện & Đánh dấu Công việc Quá hạn `[Đã triển khai]`

**Mô tả nghiệp vụ:** Nếu thời điểm hiện tại `now > dueDate` mà trạng thái vẫn là `PENDING` hoặc `IN_PROGRESS`, công việc được hệ thống tự động gắn cờ **Quá hạn (Overdue)**.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-18.1 (Mốc thời gian tính Quá hạn)`: Nếu `dueDate` chỉ chứa ngày (không có giờ cụ thể), thời điểm bắt đầu tính quá hạn được xác định là từ **23:59:59 của ngày đó theo múi giờ của Workspace**. Hạn chót trên giao diện tự động chuyển sang màu đỏ đậm kèm dòng chữ "Quá hạn X ngày" để cảnh báo khẩn cấp.
- `BR-18.2 (Xử lý khi Người phụ trách bị Vô hiệu hóa) [Yêu cầu mới]`: Khi tài khoản của Người phụ trách (`ownerId`) bị vô hiệu hóa (deactivated) hoặc chuyển trạng thái nghỉ việc, toàn bộ công việc đang mở (`PENDING`, `IN_PROGRESS`) của người đó tự động chuyển vào hàng đợi "Công việc Chưa phân công" (Unassigned Tasks) và hệ thống phát cảnh báo đến Quản lý trực tiếp (`managerId`) để kịp thời phân công lại.

---

## F. LIÊN KẾT ĐA THỰC THỂ (MULTI-ENTITY ASSOCIATION)

### FEAT-19 — Liên kết Công việc với Khách hàng Cá nhân & Doanh nghiệp `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép gắn công việc vào một Khách hàng cá nhân (`contactId`) và/hoặc Doanh nghiệp (`accountId`). Công việc sẽ tự động xuất hiện trên dòng thời gian 360 độ của khách hàng đó.

**Actor:** Nhân viên Kinh doanh, Nhân viên Hỗ trợ.

---

### FEAT-20 — Liên kết Công việc với Cơ hội Bán hàng (Deal-Task Linkage) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Gắn công việc trực tiếp vào một Cơ hội bán hàng (`dealId`) để theo dõi các hành động thúc đẩy chốt hợp đồng.

**Actor:** Nhân viên Kinh doanh.

---

### FEAT-21 — Liên kết Công việc với Vé Hỗ trợ Kỹ thuật (Ticket-Task Linkage) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Gắn công việc vào một Vé hỗ trợ (`ticketId`) (ví dụ: giao nhiệm vụ cho kỹ thuật viên kiểm tra máy chủ của khách hàng).

**Actor:** Nhân viên Hỗ trợ.

---

## G. BẢNG LÀM VIỆC & LỊCH BIỂU (MY TASKS & CALENDAR VIEW)

### FEAT-22 — Bảng Công việc Thông minh theo Hạn chót `[Đã triển khai]`

**Mô tả nghiệp vụ:** Không gian làm việc cá nhân phân nhóm công việc trực quan theo 4 mục:
- **Quá hạn (Overdue):** Các việc chưa hoàn thành đã quá hạn chót (Cần ưu tiên xử lý ngay).
- **Hôm nay (Today):** Các việc cần hoàn thành trong ngày hôm nay.
- **Tuần này (This Week):** Các việc đến hạn trong 7 ngày tới.
- **Tương lai & Chưa có hạn (Upcoming & Later):** Các việc dài hạn.

---

### FEAT-23 — Giao diện Lịch Biểu Công việc Trực quan (Tasks Calendar View) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Chế độ hiển thị dạng Lịch (Tháng / Tuần / Ngày) giúp nhân viên và quản lý nhìn thấy trực quan mật độ lịch hẹn, cuộc gọi và cuộc họp trong tháng để tránh quá tải lịch làm việc.

**Actor:** Mọi người dùng có quyền xem Task.

---

### FEAT-24 — Bộ lọc Phân loại Công việc theo Người phụ trách & Mức độ `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép lọc nhanh danh sách công việc: "Việc của tôi", "Việc của nhóm tôi", lọc theo Loại hoạt động (chỉ hiện Cuộc gọi), lọc theo Mức độ ưu tiên.

**Actor:** Mọi người dùng có quyền xem Task.

---

## H. NHẬT KÝ HOẠT ĐỘNG (ACTIVITY FEED & LOGGING)

### FEAT-25 — Ghi nhanh Hoạt động Trực tiếp trên Hồ sơ Khách hàng / Deal `[Đã triển khai]`

**Mô tả nghiệp vụ:** Trên màn hình chi tiết Contact hoặc Deal, cung cấp thanh ghi nhanh: "Ghi nhận cuộc gọi", "Ghi nhận cuộc họp", "Thêm ghi chú".

**Actor:** Nhân viên Kinh doanh.

---

### FEAT-26 — Tự động Đánh thức Hoạt động Giao dịch (`touchActivity`) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi một hoạt động hoặc công việc liên kết với Deal được hoàn tất, hệ thống tự động cập nhật `lastActivityAt = now()`, xóa bỏ cảnh báo Cơ hội Nguội (Stale Deal) của thương vụ đó.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-26.1`: Thao tác hoàn thành task liên kết trực tiếp với Deal lập tức xóa bỏ cờ Stale Deal và đưa cơ hội trở lại danh sách hoạt động tích cực.
- `BR-26.2 (Kế thừa Đánh thức từ Contact Chính) [Yêu cầu mới]`: Khi một công việc được hoàn thành trên hồ sơ của một Khách hàng cá nhân (Contact), nếu khách hàng đó đang là Người liên hệ chính (`isPrimary = true`) của một hoặc nhiều Deal đang mở, hệ thống tự động kích hoạt `touchActivity` làm mới `lastActivityAt` cho toàn bộ các Deal đó.

---

## I. THAO TÁC HÀNG LOẠT & XUẤT DỮ LIỆU (BULK & EXPORT)

### FEAT-27 — Hoàn thành, Đổi Người phụ trách & Xóa Hàng loạt `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép chọn nhiều công việc cùng lúc để hoàn thành hàng loạt (`PATCH /api/v1/tasks/bulk`), chuyển giao cho nhân viên khác hoặc xóa hàng loạt (`POST /api/v1/tasks/bulk-delete`).

**Actor:** Quản lý Đội ngũ, Quản trị viên Workspace.

---

### FEAT-28 — Xuất Danh sách Công việc Dạng Luồng CSV Bảo mật `[Đã triển khai]`

**Mô tả nghiệp vụ:** Xuất danh sách công việc ra tệp CSV theo các bộ lọc tùy chỉnh qua hàng đợi bất đồng bộ và mã token tải về an toàn trong 24 giờ.

**Actor:** Quản trị viên Workspace, Người có quyền `export` trên Tasks.

---

## J. BÁO CÁO NĂNG SUẤT HOẠT ĐỘNG (PRODUCTIVITY REPORTS)

### FEAT-29 — Báo cáo Khối lượng Hoạt động Cuộc gọi / Cuộc họp theo Nhân viên `[Đã triển khai]`

**Mô tả nghiệp vụ:** Thống kê tổng số lượng cuộc gọi đã gọi, số cuộc họp đã tham gia, số email đã gửi của từng nhân viên kinh doanh theo Ngày, Tuần, Tháng.

**Actor:** Quản lý Đội ngũ, Giám đốc Kinh doanh.

---

### FEAT-30 — Báo cáo Tỷ lệ Hoàn thành Đúng hạn & Năng suất Bán hàng `[Đã triển khai]`

**Mô tả nghiệp vụ:** Đo lường tỷ lệ hoàn thành công việc đúng hạn (% On-Time Completion Rate), số lượng việc bị trễ hạn và thời gian trung bình để hoàn thành một nhiệm vụ của từng nhân viên.

**Actor:** Quản lý Đội ngũ, Giám đốc Kinh doanh.

---

## K. MỞ RỘNG TIỆN ÍCH CÔNG VIỆC (EXTENDED PRODUCTIVITY)

### FEAT-31 — Danh sách Kiểm tra & Đính kèm Tệp Tài liệu (Task Checklists & File Attachments) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép người phụ trách chia nhỏ một công việc phức tạp thành các đầu mục kiểm tra nhỏ (Checklists) và đính kèm các tệp tài liệu liên quan trực tiếp vào bản ghi công việc.

**Actor:** Người tạo/phụ trách công việc, Người theo dõi (Watcher).

**Quy tắc nghiệp vụ:**
- `BR-31.1 (Danh sách Checklist)`: Mỗi công việc hỗ trợ tối đa 20 đầu mục kiểm tra (Checklist items). Mỗi item có: Nội dung mô tả, Hộp kiểm hoàn thành (Checkbox), Thứ tự hiển thị. Tiến độ hoàn thành hiển thị theo tỷ lệ % (ví dụ: "3/5 mục - 60%").
- `BR-31.2 (Ràng buộc Hoàn thành Task)`: Người tạo có thể bật cấu hình "Bắt buộc hoàn thành tất cả Checklist items trước khi đóng Task". Khi bật, nhân viên không thể bấm `COMPLETED` nếu còn ít nhất 1 checklist item chưa được tích chọn.
- `BR-31.3 (Đính kèm Tệp)`: Hỗ trợ đính kèm tối đa 5 tệp tài liệu (PDF, Word, Excel, Hình ảnh) với tổng dung lượng không quá 25MB cho mỗi Task. Tệp đính kèm được quét mã độc tự động trước khi lưu trữ an toàn.
- `BR-31.4 (Người theo dõi - Watchers)`: Cho phép gắn danh sách người theo dõi (`watchers[]`). Người theo dõi nhận thông báo khi: (a) Task được hoàn thành; (b) Hạn chót bị dời quá 24h; (c) Có bình luận/ghi chú mới trên task.

---

## 4. Yêu cầu phi chức năng

### 4.1 Hiệu năng & Khả năng đáp ứng (Performance)
- **NFR-01 (Thời gian tải danh sách công việc):** Tải danh sách công việc và bảng phân loại theo hạn chót phản hồi dưới **200ms** (p95).
- **NFR-02 (Độ chính xác Tiến trình Nhắc việc):** Worker quét nhắc nhở chạy đúng chu kỳ 1 phút/lần, phát thông báo đúng giờ hẹn không trễ quá 60 giây.
- **NFR-03 (Tốc độ Hoàn thành 1-Click):** Thao tác bấm hoàn thành công việc phản hồi giao diện tức thì (Optimistic UI update dưới **50ms**).

### 4.2 Độ tin cậy & Toàn vẹn Dữ liệu (Reliability & ACID)
- **NFR-04 (An toàn Sinh việc Định kỳ):** Tiến trình sinh việc lặp lại đảm bảo tính Idempotency, không bao giờ sinh trùng lặp 2 bản ghi cho cùng một chu kỳ lặp.
- **NFR-05 (Bảo toàn Liên kết Thực thể):** Khi một Contact hoặc Deal bị xóa mềm, các công việc liên quan vẫn bảo toàn định danh tham chiếu và được khôi phục đồng bộ khi phục hồi thực thể chính.

### 4.3 An toàn & Bảo mật (Security)
- **NFR-06 (Bảo mật Phân quyền ABAC):** Áp dụng nghiêm ngặt phạm vi dữ liệu (Cá nhân / Phòng ban / Cây phòng ban / Toàn tổ chức). Nhân viên chỉ thấy và quản lý các công việc trong phạm vi được phân công.
- **NFR-07 (Bảo vệ Mặt nạ Dữ liệu FLS):** Các trường dữ liệu nhạy cảm liên kết hiển thị trên task tuân thủ chính sách FLS của đối tượng gốc.

---

## 5. Ma trận quyền truy cập tính năng

| Mã FEAT | Tên tính năng nghiệp vụ | Nhân viên (Sales Rep) | Nhân viên Hỗ trợ | Quản lý Đội ngũ | Quản trị viên (Admin) | Chủ sở hữu (Owner) |
| --- | --- | :---: | :---: | :---: | :---: | :---: |
| `FEAT-01` | Tạo & Quản lý Task | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-02` | Xem Chi tiết Task | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-03` | Quản lý Mức độ Priority | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-04` | Thiết lập Hạn chót Due Date | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-05` | Thùng rác & Phục hồi Task | — | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-06` | Cập nhật Trạng thái Task | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-07` | Hoàn thành 1-Click | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-08` | Tự động Ghi nhận CompletedAt| *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-09` | Khôi phục Chưa hoàn tất | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-10` | Chọn Loại Hoạt động | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-11` | Ghi Kết quả Cuộc gọi | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-12` | Ghi Biên bản Cuộc họp | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-13` | Cấu hình Việc Lặp lại | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-14` | Tự động Sinh Việc Lặp | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-15` | Điều kiện Dừng Lặp lại | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-16` | Thiết lập Nhắc nhở | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-17` | Nhận Thông báo Nhắc việc | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-18` | Nhận diện Quá hạn | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-19` | Liên kết Contact/Account | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-20` | Liên kết Cơ hội Bán hàng | Scope gán | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-21` | Liên kết Vé Hỗ trợ | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-22` | Bảng Việc theo Hạn chót | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-23` | Giao diện Lịch Biểu | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-24` | Bộ lọc Thông minh Task | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-25` | Ghi Nhanh Hoạt động | Scope gán | Scope gán | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-26` | Đánh thức Deal (`touchActivity`)| *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-27` | Thao tác Hàng loạt Bulk | — | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-28` | Xuất File CSV qua Token | — | — | Có quyền `export`| **Toàn quyền** | **Toàn quyền** |
| `FEAT-29` | Báo cáo Khối lượng Hoạt động| — | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |
| `FEAT-30` | Báo cáo Đúng hạn & Năng suất | — | — | Scope phòng ban | **Toàn quyền** | **Toàn quyền** |

---

## 6. Kịch bản chấp nhận tổng hợp (UAT)

### Kịch bản 1: Tạo mới Công việc, Đặt Hạn chót & Hoàn thành 1-Click
1. Nhân viên kinh doanh tạo công việc: "Gọi điện tư vấn báo giá gói Enterprise", Hạn chót: `16:00 ngày 29/08/2026`, Loại: `CALL`, Mức độ: `HIGH`, gắn Khách hàng "Nguyễn Văn Hùng".
2. Công việc hiển thị trên Bảng việc mục "Hôm nay" với biểu tượng điện thoại màu đỏ cam.
3. Sau khi gọi điện xong lúc 15:45, nhân viên nhấp vào hộp kiểm tròn bên cạnh tên công việc.
4. **Kỳ vọng:** Công việc chuyển sang trạng thái `COMPLETED` kèm hiệu ứng gạch ngang, ghi nhận `completedAt = 15:45:00 29/08/2026` (Đúng hạn), tự động cập nhật nhật ký hoạt động trên hồ sơ ông Hùng.

---

### Kịch bản 2: Công việc Lặp lại Định kỳ Hằng tuần (Recurring Weekly Task)
1. Nhân viên thiết lập công việc: "Gửi báo cáo tiến độ tuần cho khách hàng", Hạn chót lần đầu: `17:00 Thứ Sáu 29/08`, Thiết lập lặp lại: `WEEKLY` (Mỗi tuần 1 lần vào Thứ Sáu).
2. Chiều Thứ Sáu lúc 16:30, nhân viên gửi báo cáo và bấm "Hoàn thành" công việc kỳ hiện tại.
3. **Kỳ vọng:** Công việc kỳ hiện tại chuyển sang `COMPLETED`. Tiến trình `RecurringTaskService` tự động sinh ra một công việc mới: "Gửi báo cáo tiến độ tuần cho khách hàng" với Hạn chót: `17:00 Thứ Sáu tuần sau 05/09/2026` ở trạng thái `PENDING`.

---

### Kịch bản 3: Nhắc nhở Tự động Trước Hạn chót 15 Phút
1. Nhân viên tạo công việc: "Họp Demo trực tuyến với Ban Giám đốc Khách hàng", Hạn chót: `10:00 sáng mai`, Đặt nhắc nhở: `Trước 15 phút` (`reminderAt = 09:45`).
2. Đúng 09:45 sáng hôm sau, tiến trình `TaskReminderService` quét phát hiện lịch hẹn.
3. **Kỳ vọng:** Hệ thống phát chuông và hiển thị thông báo góc màn hình: "Nhắc việc: [Họp Demo trực tuyến với Ban Giám đốc Khách hàng] sẽ bắt đầu sau 15 phút!".

---

### Kịch bản 4: Cảnh báo Công việc Quá hạn (Overdue)
1. Công việc "Gửi hợp đồng mẫu" có Hạn chót là `12:00 hôm qua`. Đến thời điểm hiện tại nhân viên chưa bấm hoàn tất.
2. Nhân viên mở Bảng làm việc cá nhân.
3. **Kỳ vọng:** Công việc tự động nằm trong mục đầu bảng màu đỏ đậm **"Quá hạn (1 ngày)"**, dòng hạn chót hiển thị màu đỏ cảnh báo.

---

### Kịch bản 5: Ghi nhanh Hoạt động & Đánh thức Cơ hội Nguội Lạnh (Touch Activity)
1. Cơ hội bán hàng "Hợp đồng Phần mềm 500 triệu" đang bị gắn cờ "Cơ hội Nguội (15 ngày không có tương tác)".
2. Nhân viên mở hồ sơ Deal, bấm "Ghi nhận cuộc gọi", nhập nội dung: "Đã liên hệ lại với Giám đốc kỹ thuật, khách hàng đồng ý họp vào Thứ Ba tuần tới", chọn kết quả cuộc gọi "Đã kết nối thành công".
3. **Kỳ vọng:** Hệ thống lưu 1 dòng nhật ký vào Dòng thời gian Deal, tự động làm mới `lastActivityAt = now()` và lập tức xóa bỏ cờ cảnh báo Cơ hội Nguội trên bảng Kanban Deal.

---

### Kịch bản 6: Hoàn thành & Chuyển giao Công việc Hàng loạt (Bulk Operations)
1. Quản lý đội ngũ chọn 5 công việc đang mở của nhân viên A (đã nghỉ phép đột xuất).
2. Quản lý chọn "Thao tác hàng loạt" -> "Chuyển giao người phụ trách" -> Chọn nhân viên B.
3. Bấm "Xác nhận".
4. **Kỳ vọng:** Toàn bộ 5 công việc được chuyển sang cho nhân viên B phụ trách, gửi thông báo giao việc tới nhân viên B và cập nhật quyền truy cập tức thì.

---

## 7. Giới hạn hiện tại & Vấn đề chính sách cần quyết định tiếp

1. **Đồng bộ Lịch Hai Chiều với Google Calendar & Microsoft Outlook (2-Way Calendar Sync):**
   - *Vấn đề:* Có nên tự động đồng bộ các cuộc họp và công việc có giờ cụ thể sang Google Calendar / Outlook Calendar của người dùng không?
   - *Đề xuất PM:* Đưa tính năng Tích hợp Lịch bên ngoài vào lộ trình nâng cấp quý tiếp theo.
2. **Quy trình Mẫu Công việc Tự động (Sales Sequences / Task Templates):**
   - *Vấn đề:* Khi một Deal chuyển sang giai đoạn "Báo giá", có nên tự động sinh ra một chuỗi 3 công việc mẫu (*Gửi báo giá trong 2h -> Gọi xác nhận sau 1 ngày -> Follow-up sau 3 ngày*) không?
   - *Đề xuất PM:* Tích hợp với phân hệ Tự động hóa Quy trình (Workflow Automations) để kích hoạt chuỗi công việc tự động.
3. **Chính sách Tự động Hủy Công việc khi Deal Đóng (Auto-Cancel Tasks on Deal Close):**
   - *Vấn đề:* Khi một Deal bị đánh dấu `Closed Lost`, các công việc chưa hoàn thành của Deal đó có nên tự động chuyển sang `CANCELLED` không?
   - *Đề xuất PM:* Hiển thị hộp thoại hỏi người dùng: *"Bạn có muốn hủy 2 công việc chưa hoàn thành của cơ hội này không?"* để người dùng chủ động quyết định.
