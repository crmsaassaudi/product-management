# srs/

Software Requirements Specification (SRS) cho các module **đã triển khai xong nhưng chưa từng có SRS** - viết ngược từ code thực tế (reverse-engineered), không phải spec viết trước khi code.

Khác với [`specs/`](../specs/README.md) (spec/PRD nháp *trước khi* code, dùng để bàn bạc/lên issue), tài liệu ở đây mô tả **hành vi hiện tại của hệ thống**, dùng làm:

- Nguồn tham chiếu duy nhất khi onboard người mới vào module.
- Baseline để đánh giá thay đổi có phải là "regression" hay "cải tiến có chủ đích".
- Input cho audit/security review sau này (không phải thay thế audit report - xem `docs/audit/` ở từng repo triển khai).

## Quy ước

- Tên file: `<module-slug>-srs.md`.
- **Khung mục cố định, theo đúng thứ tự** (xem `iam-tenant-authorization.md` làm mẫu tham chiếu):
  1. Giới thiệu (Mục đích, Phạm vi, Đối tượng đọc, Thuật ngữ & viết tắt, Tài liệu tham khảo)
  2. Tổng quan nghiệp vụ (Vấn đề module giải quyết, Vai trò người dùng, Nhóm tính năng)
  3. Đặc tả yêu cầu chức năng (chia theo nhóm chức năng nếu cần, mỗi tính năng là một mục `FEAT-xx`)
  4. Yêu cầu phi chức năng
  5. Ma trận quyền truy cập tính năng
  6. Kịch bản chấp nhận tổng hợp
  7. Giới hạn hiện tại & vấn đề tồn đọng
- **Đánh số `FEAT-xx` liên tục xuyên suốt cả tài liệu** (không reset theo từng nhóm/chương, không dùng ký hiệu riêng kiểu `A1`/`BR-CHN-01`). Nếu tài liệu có nhiều nhóm chức năng, dùng tiêu đề nhóm (`## A. TÊN NHÓM`) để phân đoạn, nhưng mã `FEAT-xx` vẫn tăng dần đều qua các nhóm. Quy tắc nghiệp vụ trong một FEAT đánh số `BR-xx.n` theo đúng số FEAT chứa nó (vd. `BR-05.1`, `BR-05.2`).
- **Nhãn trạng thái ngay sau tiêu đề mỗi FEAT**, chỉ 2 giá trị: `` `[Đã triển khai]` `` hoặc `` `[Yêu cầu mới]` `` — không dùng emoji hay câu dẫn giải xen giữa tiêu đề và nội dung. Nếu chỉ một BR cụ thể trong một FEAT `[Đã triển khai]` là quyết định mới, gắn nhãn `` `[Yêu cầu mới]` `` riêng ngay sau mã BR đó; các BR không có nhãn kế thừa trạng thái của FEAT chứa nó.
- **Không kể chuyện quá trình** (phiên rà soát, ai grilling ai, diff giữa các phiên bản tài liệu, việc AI tự tạo issue…) trong thân tài liệu — những thông tin đó thuộc lịch sử Git/issue tracker, không phải nội dung SRS. Header + đoạn "Ghi chú về nguồn gốc tài liệu" ở đầu file là nơi DUY NHẤT được phép nêu ngắn gọn nguồn gốc/quy ước nhãn.
- **Link GitHub issue/ADR luôn đặt ở cuối mục**, dưới một dòng `**Tham chiếu:**` riêng — không chèn link hay số issue giữa câu mô tả nghiệp vụ. Không tạo phụ lục roadmap riêng liệt kê lại các issue theo độ ưu tiên (đó là việc của issue tracker, không phải SRS) — mỗi FEAT/BR tự mang tham chiếu của nó.
- **Chỉ một mục tổng hợp duy nhất cho các vấn đề chưa quyết định**, ở cuối tài liệu (Mục 7) — không lặp lại một khối "Vấn đề tồn đọng" sau mỗi nhóm chức năng. Mục 7 chỉ chứa các điểm **thực sự chưa có quyết định**; các điểm đã chốt phương án nhưng chưa code thuộc về nhãn `[Yêu cầu mới]` ở Mục 3, không lặp lại ở đây.
- SRS **nên** neo vào một **commit cụ thể** của (các) repo triển khai (ghi ở đầu file) khi người viết có quyền truy cập repo đó để lấy hash thật — SRS mô tả trạng thái tại thời điểm đó, không tự động đúng mãi mãi. Không bịa hash khi không xác minh được; ghi rõ "chưa xác định" thay vì bỏ trống âm thầm. Khi code đổi đáng kể, cập nhật SRS (và neo lại commit mới nếu có) trong cùng PR hoặc issue theo dõi riêng, đừng để SRS trôi khỏi code.
- Nếu module đã có audit report riêng (ví dụ `docs/audit/OBJECT_MANAGER_AUDIT.md` ở `crm-api`), SRS **không lặp lại** danh sách defect - chỉ dẫn chiếu và tóm tắt phần còn ảnh hưởng đến hành vi hiện tại ở mục "Giới hạn đã biết".
- SRS viết theo văn phong Business Analyst: theo tính năng/use case, quy tắc nghiệp vụ bằng ngôn ngữ nghiệp vụ. Vẫn cần đọc code/khảo sát hệ thống thật trước khi viết (để mô tả đúng hành vi), nhưng phần đó không xuất hiện trong thân tài liệu. **Ranh giới cụ thể của "ngôn ngữ nghiệp vụ" và cách tự kiểm tra: xem mục [Ngôn ngữ nghiệp vụ](#ngôn-ngữ-nghiệp-vụ-ranh-giới-bắt-buộc) bên dưới — đây là lỗi tái diễn nhiều lần, không phải khuyến nghị văn phong.**
- Thuật ngữ chốt trong lúc viết/review SRS ghi vào [`../CONTEXT.md`](../CONTEXT.md) (glossary dùng chung cho mọi SRS ở đây, theo nhóm subheading từng module) - không định nghĩa lại thuật ngữ khác đi giữa các SRS.
- Quyết định khó đảo ngược/có đánh đổi thật phát sinh trong lúc review (ví dụ đổi chính sách phân giải xung đột phân quyền) ghi thành ADR tại [`../docs/adr/`](../docs/adr/), đánh số tuần tự - xem tiêu chí "khi nào cần ADR" trong skill `domain-modeling`.


## Ngôn ngữ nghiệp vụ (ranh giới bắt buộc)

> **Vì sao có mục này:** Đây là lỗi đã tái diễn qua nhiều phiên bản SRS, kể cả các tài liệu tự gắn nhãn "đã qua N vòng review, không còn lỗi". Nguyên nhân gốc: SRS được viết **ngược từ code ra** thay vì từ nghiệp vụ xuống, nên tài liệu mô tả *hệ thống đang được xây dựng thế nào* thay vì *doanh nghiệp cần điều gì là đúng*. Hệ quả không chỉ là văn phong xấu — tài liệu viết theo tên trường dữ liệu **tự sinh ra mâu thuẫn nghiệp vụ mà không ai phát hiện được**, vì người đọc không còn nhìn thấy ý định phía sau quy tắc.

### Cấm tuyệt đối trong thân tài liệu

Không dùng ở bất kỳ mục nào từ Mục 1 đến Mục 7 (Phụ lục có ngoại lệ hẹp, xem dưới):

| Loại | Ví dụ SAI | Viết lại thành |
| --- | --- | --- |
| Tên trường dữ liệu | `ownerId`, `dueDate`, `completedAt`, `isTerminal`, `deletedAt` | Người phụ trách, Hạn chót, Thời điểm kết thúc, Trạng thái kết thúc, Thời điểm xóa mềm |
| Tên class / service / component | `TaskReminderService`, `ListViewsService`, `TaskCard.tsx` | Tiến trình nhắc việc, Danh sách lọc nâng cao, Thẻ công việc |
| Tên hạ tầng / công nghệ | Redis, MongoDB, Socket.IO, WebSocket, ObjectId, cron | Khóa chống chạy trùng, hệ quản trị dữ liệu, kênh thời gian thực, định danh, tiến trình nền định kỳ |
| Đường dẫn API / endpoint | `GET /api/v1/tasks/recycle-bin` | Màn hình Thùng rác |
| Công thức điều kiện | `now() > dueDate AND isTerminal == false` | Phát biểu bằng lời: "khi đã qua hạn chót **và** công việc chưa ở trạng thái kết thúc" |
| Tên bảng / collection | `task_statuses`, `activity_logs` | Danh mục trạng thái công việc, Nhật ký tương tác |
| Trích dẫn code / `file:line` | `tasks.service.ts:358` | *(không có cách viết lại — thuộc backlog, không thuộc SRS)* |

**Ngoại lệ duy nhất:** Phụ lục "Danh mục Khái niệm Nghiệp vụ" được phép nêu ánh xạ sang tên kỹ thuật, **nhưng phải ghi rõ ngay đầu phụ lục rằng đây là mô tả nghiệp vụ, không phải thiết kế dữ liệu, và không mang tính ràng buộc**. Cách tổ chức lưu trữ thuộc thẩm quyền đội phát triển.

### Mỗi quy tắc nghiệp vụ phải trả lời được "vì sao"

Một `BR-xx.n` chỉ mô tả *hệ thống làm gì* là chưa đủ. Với mọi quy tắc có ràng buộc, cấm đoán hoặc đánh đổi, phải có một đoạn **Lý do nghiệp vụ** nêu điều gì hỏng nếu không có quy tắc đó.

Đây không phải yêu cầu hình thức. Quy tắc không có lý do là quy tắc **không ai dám sửa và không ai biết khi nào được miễn trừ** — đó chính là cách các mâu thuẫn hình thành: một quy tắc chặn field X vì lý do A, vô tình chặn luôn nghiệp vụ B hoàn toàn chính đáng, và không ai nhận ra vì lý do A chưa từng được viết ra.

### Mỗi FEAT phải có bảng Tiêu chí Chấp nhận (AC)

Mỗi FEAT `[Đã triển khai]` hoặc thuộc phạm vi phát hành phải có bảng AC với đúng ba cột: **Bối cảnh — Hành động — Kết quả mong đợi**. Mã AC đánh theo BR mà nó kiểm chứng (`AC-05.3.1` kiểm chứng `BR-05.3`).

Yêu cầu về nội dung AC:
- Mô tả **điều người dùng quan sát được**, không mô tả trạng thái nội bộ của hệ thống.
- Phủ cả **luồng thất bại và ca biên**, không chỉ happy path.
- Nếu một quy tắc áp dụng trên nhiều màn hình (ví dụ "không báo quá hạn" áp dụng trên Danh sách, Lịch biểu, Bảng trạng thái, bộ đếm), phải có AC riêng cho **từng màn hình** — đây là nơi lỗi hay lọt nhất.
- Nếu quy tắc có ràng buộc phía máy chủ, phải có AC cho **cả phía giao diện**: giao diện phải thể hiện ràng buộc trước khi người dùng thao tác, không để người dùng nhập xong mới báo lỗi.

### Tự kiểm tra trước khi coi là xong

Chạy lệnh sau trên tài liệu vừa viết; kết quả phải **trống**:

```bash
grep -nE '\.tsx|\.ts:|[A-Z][a-zA-Z]+Service\b|Redis|MongoDB|BullMQ|Socket\.IO|WebSocket|ObjectId|GET /|POST /|PATCH /|__v|[a-z]+[A-Z][a-zA-Z]*Id\b|isTerminal|deletedAt|completedAt|dueDate|\bcron\b' <file>-srs.md
```

Mẫu này bắt đúng các lỗi thật và đã được hiệu chỉnh để không báo nhầm các thuật ngữ nghiệp vụ hợp lệ như *Service Account* hay *Service Level Agreement*. Nếu kết quả có dòng mà bạn cho là hợp lệ, hãy viết lại câu đó thay vì nới mẫu — gần như luôn có cách diễn đạt bằng ngôn ngữ nghiệp vụ.

Kiểm tra thêm bằng mắt:
- Mọi `BR-xx.n` được tham chiếu đều có định nghĩa tương ứng trong tài liệu.
- Mọi `FEAT-xx` được nhắc tới đều có trong bảng tổng hợp.
- Mã AC không trùng nhau.
- Số nhóm chức năng ghi ở Mục 1.2 khớp số nhóm thực có.
- Danh sách vai trò ở Ma trận phân quyền khớp danh sách vai trò ở mục Vai trò người dùng.

### Rà soát mâu thuẫn nghiệp vụ nội tại

Trước khi chốt, phải chủ động soát các lớp mâu thuẫn sau — kinh nghiệm cho thấy chúng gần như luôn tồn tại và **không tự lộ ra khi đọc tuần tự**:

1. **Quy tắc cấm vs tính năng cho phép.** Quét mọi BR có tính chất "chặn/khóa/không được phép", đối chiếu với mọi FEAT cho phép thao tác trên cùng đối tượng. Ví dụ đã gặp: quy tắc khóa sửa đổi công việc đã kết thúc vs tính năng dời hạn hàng loạt.
2. **Quy tắc tạo vòng luẩn quẩn.** Kiểm tra: khi quy tắc A yêu cầu "muốn làm X phải làm Y trước", thì Y có phá hỏng điều mà A đang bảo vệ không? Ví dụ đã gặp: muốn đổi người phụ trách phải mở lại công việc, nhưng mở lại xóa mất mốc hoàn thành — chính là dữ liệu quy tắc đó muốn giữ.
3. **Khái niệm gộp chung nhưng nghiệp vụ khác nhau.** Ví dụ đã gặp: "trạng thái kết thúc" gộp cả Hoàn thành và Hủy bỏ, khiến việc bị hủy vẫn tính vào báo cáo năng suất.
4. **Danh mục cấu hình được nhưng không có ràng buộc toàn vẹn.** Nếu tài liệu cho tenant tự định nghĩa danh mục (trạng thái, giai đoạn, phân loại), bắt buộc phải có FEAT quy định ràng buộc tối thiểu — tenant không được tự cấu hình đến mức tê liệt vận hành.
5. **Cơ chế tự động thiếu chiều ngược.** Mọi quy tắc "khi X thì hệ thống tự động làm Y" phải trả lời: khi X bị hoàn tác / bị hủy / bị xóa thì Y ra sao?
6. **Ca biên thời gian chưa phủ hết.** Lặp theo tháng phải trả lời ca ngày 29/30/31; lặp theo năm phải trả lời 29/02; khoảng lặp lớn hơn 1 phải có ví dụ riêng.
7. **Múi giờ.** Mọi tài liệu có khái niệm quá hạn, cuối ngày, cuối tháng hoặc giờ chạy tiến trình nền **bắt buộc** chốt múi giờ áp dụng ngay ở Mục 2.

Mâu thuẫn phát hiện được mà **chưa có thẩm quyền quyết** thì hỏi chủ tài liệu trước khi viết tiếp — không tự chọn một hướng rồi viết tiếp như thể đã được chốt. Mâu thuẫn đã giải quyết ghi vào một phụ lục nhật ký ở cuối tài liệu (mâu thuẫn cũ + cách xử lý), để lần review sau không lật lại quyết định đã chốt.

### SRS là chuẩn, code phải tuân theo

Khi phát hiện code làm khác tài liệu, **mặc định là code sai**, không phải tài liệu sai. Chỉ sửa tài liệu khi đối chiếu cho thấy chính nghiệp vụ trong tài liệu mới là điều vô lý.

Hệ quả bắt buộc:
- **Không đưa bảng đối chiếu sai lệch mã nguồn vào SRS.** Danh sách "chỗ nào code đang làm sai, ở tệp nào, dòng bao nhiêu" thuộc về file backlog riêng hoặc issue tracker. Đưa vào SRS thì số dòng lệch ngay khi code đổi, và tài liệu mất uy tín.
- **Không hạ chuẩn nghiệp vụ cho khớp hiện trạng code.** Nếu code chưa làm được, đó là khoảng cách cần lấp, không phải lý do sửa đặc tả.
- Nhãn trạng thái tính năng phải **kiểm chứng với code thật** trước khi gắn, không tin nhãn của phiên bản trước.

## Danh mục tài liệu SRS

| File | Phân hệ / Module | Trạng thái | Ngày cập nhật |
| --- | --- | --- | --- |
| [`onboarding-srs.md`](./onboarding-srs.md) | Tiếp nhận & Khởi tạo Không gian làm việc (Onboarding & Provisioning) | Version 2.0 (Target Standard) | 2026-08-28 |
| [`contacts-srs.md`](./contacts-srs.md) | Quản lý Khách hàng & Danh bạ Doanh nghiệp (Contacts & Accounts) | Version 2.2 (Standardized Business SRS) | 2026-08-29 |
| [`deals-pipeline-srs.md`](./deals-pipeline-srs.md) | Quản lý Cơ hội & Phễu Bán hàng (Deals & Pipelines) | Version 6.0 (Chuẩn hóa Nghiệp vụ Thuần túy) | 2026-09-23 |
| [`tickets-srs.md`](./tickets-srs.md) | Quản lý Vé Hỗ trợ & Dịch vụ Khách hàng (Tickets & Customer Service) | Version 2.2 (Standardized Business SRS) | 2026-08-29 |
| [`tasks-srs.md`](./tasks-srs.md) | Quản lý Công việc, Lịch trình & Ghi nhận Tương tác (Tasks, Calendar & Activity Logging) | Version 6.0 (Chuẩn hóa Nghiệp vụ Thuần túy) | 2026-09-17 |
| [`campaigns-srs.md`](./campaigns-srs.md) | Quản lý Chiến dịch Tiếp thị & Truyền thông Đa kênh (Marketing Campaigns) | Version 2.2 (Standardized Business SRS) | 2026-08-29 |
| [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) | Phân quyền & Quản trị Không gian làm việc (IAM & ABAC) | Baseline | 2026-08-25 |
| [`object-manager-srs.md`](./object-manager-srs.md) | Quản trị Đối tượng & Bố cục Trường dữ liệu (Object Manager & FLS) | Baseline | 2026-08-24 |
| [`omnichat-srs.md`](./omnichat-srs.md) | Hội thoại Đa kênh & Hộp thư Tiếp nhận (Omnichannel Inbox) | Baseline | 2026-08-24 |
