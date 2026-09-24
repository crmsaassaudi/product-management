# AGENTS.md

File này dành cho agent làm việc với feature/issue được quản lý qua `product-management`. Repo này không có code để build/test - không có `init.sh`/`feature_list.json` của riêng nó. Xem [`README.md`](./README.md) cho tổng quan, [`PROCESS.md`](./PROCESS.md) cho quy tắc tạo/đóng issue (id, branch, cross-repo).

## Định Nghĩa "Done" Áp Dụng Chung (Baseline Definition of Done)

Đây là tiêu chí **tối thiểu** cho MỌI feature, bất kể triển khai ở repo nào. Đây là mức sàn chung cho toàn tổ chức - repo triển khai vẫn có thể có `AGENTS.md` riêng với tiêu chí cụ thể/chặt hơn (crm-api, crm-web đã làm vậy với chu trình TDD red→green riêng) - baseline này không thay thế, chỉ đảm bảo không repo nào thấp hơn mức này.

Một feature chỉ được coi là "done" khi TẤT CẢ điều sau đều đúng:

- [ ] **Unit test liên quan pass** - chạy theo verification command của (các) repo triển khai (`./init.sh` hoặc tương đương)
- [ ] **Không ảnh hưởng tính năng khác** - toàn bộ test suite hiện có của repo vẫn xanh, không chỉ test mới viết cho feature này (regression, không chỉ happy-path của phần vừa thêm)
- [ ] **Tiêu chí hoàn thành bổ sung trong issue (nếu có) đã được đáp ứng** - xem mục dưới
- [ ] **Verification bắt buộc đã thực sự chạy** (lint/test/build) - không khai "done" khi chưa chạy, không tự suy diễn là sẽ pass
- [ ] **Issue được đóng/tham chiếu đúng quy tắc trong `PROCESS.md`** - `Closes crmsaassaudi/product-management#N` cho feature 1 repo, chỉ tham chiếu (không auto-close) cho feature nhiều repo
- [ ] **Không đóng cứng lựa chọn lẽ ra thuộc về tenant** - mọi quyết định nghiệp vụ có nhiều hướng hợp lý tuỳ tenant đều đã thành tham số cấu hình có mặc định, xem mục dưới
- [ ] **Trạng thái đã cập nhật** trong `feature_list.json` của repo triển khai (và `initiatives-index.json` ở đây nếu là feature nhiều repo)

## Tiêu Chí Hoàn Thành Trong Issue Chỉ Là Bổ Sung

Field "Tiêu chí hoàn thành bổ sung" trong issue/template `feature.yml` **không lặp lại baseline ở trên** - baseline áp dụng mặc định cho mọi issue, không cần ghi lại. Field đó chỉ dùng để ghi điều kiện **đặc thù** của riêng feature đang tạo (ví dụ: endpoint cụ thể phải trả về gì, hành vi UI cụ thể nào, ngưỡng hiệu năng cụ thể). Nếu feature không có gì đặc thù ngoài baseline, để trống field đó.

## Quyết Định Nghiệp Vụ Nhiều Hướng Phải Là Tham Số Cấu Hình Theo Tenant

Đây là sản phẩm đa tenant: mỗi tenant có nghiệp vụ, quy mô và ràng buộc tuân thủ khác nhau. **Mọi quy tắc nghiệp vụ có nhiều hướng xử lý đều hợp lý tuỳ tenant thì phải được triển khai thành tham số cấu hình cấp không gian làm việc, kèm một giá trị mặc định chuẩn hệ thống — không được đóng cứng (hard-code) lựa chọn vào mã nguồn.** Mặc định là để tenant chạy được ngay mà không phải cấu hình gì; tham số là để tenant tự điều chỉnh mà **không cần sửa mã nguồn hay chờ phát hành phiên bản mới**.

**Dấu hiệu nhận biết** một quyết định thuộc diện này — khi trả lời "vì sao chọn hướng A" mà lý do là *tập quán nghiệp vụ, khẩu vị rủi ro, quy mô đội ngũ hoặc chính sách nội bộ* chứ không phải *nghĩa vụ pháp lý hoặc toàn vẹn dữ liệu*, thì đó là tham số cấu hình. Các diện hay gặp: giá trị mặc định khi tạo bản ghi, ngưỡng và hạn mức, thời hạn/thời gian chờ, bật/tắt một nhóm trường hoặc một cơ chế tự động, cách xử lý khi phát hiện trùng lặp, phạm vi hiển thị mặc định, vai trò thấp nhất được thực hiện một thao tác.

**Ba mức độ tự do** phải được gán rõ cho từng tham số:

- **Tự do** — tenant đặt giá trị bất kỳ trong miền cho phép.
- **Có sàn bắt buộc** — tenant điều chỉnh được nhưng không được nới lỏng dưới ngưỡng an toàn/pháp lý đã quy định.
- **Cố định** — không cấu hình được, vì liên quan nghĩa vụ pháp lý hoặc toàn vẹn dữ liệu.

"Cố định" là **ngoại lệ phải giải trình**, không phải lựa chọn mặc định khi ngại làm cấu hình. Không được dùng nhãn này để hợp thức hoá một giá trị đóng cứng chỉ vì chưa kịp làm màn hình cài đặt.

**Khi viết SRS:** mỗi tham số phải có mặt trong danh mục tham số cấu hình theo tenant của chính tài liệu đó (một phụ lục riêng — xem mẫu "Phụ lục B" trong [`srs/contacts-srs.md`](./srs/contacts-srs.md)), đủ các cột: mã tham số, quy tắc nghiệp vụ liên quan, nội dung cấu hình, giá trị mặc định chuẩn hệ thống, miền giá trị cho phép, vai trò thấp nhất được thay đổi, mức độ tự do. Quy tắc nghiệp vụ nào nêu một con số hay một lựa chọn cụ thể thì phải trỏ về mã tham số tương ứng, **không phát biểu lại giá trị ở hai nơi** — đây là nguồn mâu thuẫn tái diễn qua các vòng review. Thẩm quyền đổi tham số là **một trục quyền riêng**, không suy ra được từ ma trận phân quyền trên dữ liệu nghiệp vụ.

**Khi triển khai:** giá trị phải đọc từ cấu hình của tenant, lấy mặc định chuẩn hệ thống làm giá trị dự phòng, không phải hằng số nằm trong mã. Thêm tham số mới phải kèm đường backfill cho tenant hiện hữu — seeder chỉ chạy cho tenant mới, tenant cũ sẽ thiếu giá trị nếu không backfill. Test phải phủ **ít nhất hai giá trị khác nhau** của tham số: test chỉ chạy đúng giá trị mặc định không phân biệt được cấu hình thật với hằng số đóng cứng.

## Khi Viết Hoặc Sửa SRS

SRS (`srs/*-srs.md`) có bộ quy tắc riêng, **bắt buộc đọc trước khi chạm vào bất kỳ file SRS nào**: [`srs/README.md`](./srs/README.md).

Hai điều hay sai nhất, đã tái diễn qua nhiều phiên bản:

1. **Viết SRS bằng ngôn ngữ code thay vì ngôn ngữ nghiệp vụ.** Tên trường dữ liệu, tên service, tên hạ tầng, công thức điều kiện, đường dẫn API đều bị cấm trong thân tài liệu. Xem mục "Ngôn ngữ nghiệp vụ (ranh giới bắt buộc)" trong `srs/README.md`, có lệnh `grep` tự kiểm tra — kết quả phải trống trước khi coi là xong.

2. **Coi code là chuẩn và sửa SRS cho khớp code.** Ngược lại mới đúng: **SRS là chuẩn, code sai thì sửa code**. Không đưa bảng đối chiếu sai lệch mã nguồn (tên tệp, số dòng) vào SRS — đó là việc của backlog/issue tracker.

Ngoài ra, trước khi chốt một SRS phải chủ động rà 7 lớp mâu thuẫn nghiệp vụ nội tại liệt kê trong `srs/README.md` (quy tắc cấm vs tính năng cho phép, vòng luẩn quẩn, khái niệm gộp chung, danh mục thiếu ràng buộc toàn vẹn, cơ chế tự động thiếu chiều ngược, ca biên thời gian, múi giờ). Nhãn trạng thái tính năng phải kiểm chứng với code thật, **không tin nhãn của phiên bản trước** — kể cả khi tài liệu tự ghi "đã qua N vòng review, không còn lỗi".

### SRS Là Đặc Tả Mục Tiêu Tuyệt Đối (To-Be) — Không Được Suy Luận Từ Hiện Trạng Code

Đây là nguyên tắc tách biệt với mục 2 ở trên, dễ nhầm nên phải nêu riêng: mục 2 nói về việc **không hạ chuẩn khi phát hiện code sai lệch**; nguyên tắc này nói về việc **không được dùng code làm căn cứ để suy luận, giới hạn hay định hình nội dung nghiệp vụ của SRS ngay từ đầu**, kể cả khi không phát hiện sai lệch nào.

- Khi viết mới hoặc review một SRS, xuất phát điểm và căn cứ duy nhất là **nhu cầu nghiệp vụ mong muốn** (nói chuyện với người yêu cầu, đối chiếu SRS khác, suy luận từ mô hình nghiệp vụ) — **không phải** đọc code hiện có rồi suy ngược ra "chắc nghiệp vụ muốn vậy" hoặc thu hẹp phạm vi đặc tả cho vừa với những gì code đã làm được.
- **Cấm tuyệt đối** trong thân SRS: nhãn trạng thái triển khai kiểu `[Đã triển khai]`/`[Yêu cầu mới]`/`[Chưa có trong hệ thống]`, câu văn dạng "hiện tại hệ thống chưa/đã...", bảng đối chiếu mã nguồn, hoặc bất kỳ hình thức neo nội dung nghiệp vụ vào giới hạn kỹ thuật hiện tại. SRS mô tả **một trạng thái đích duy nhất**, không mô tả lộ trình hay khoảng cách với hiện trạng.
- Đối chiếu code (nếu cần) chỉ phục vụ **kiểm chứng nhãn** đã dùng đúng thuật ngữ nghiệp vụ đang tồn tại (ví dụ: tên trạng thái, tên vai trò) để tránh bịa ra khái niệm không khớp hệ thống — không phải để quyết định phạm vi hay mức độ chặt của một quy tắc nghiệp vụ. Nếu code hiện tại làm được ít hơn những gì SRS yêu cầu, đó là việc phải lập issue triển khai/sửa code, **không phải lý do để viết lại SRS cho khớp**.
- Áp dụng cho mọi vòng review: người review SRS không được hạ mức nghiêm trọng của một phát hiện, hoặc gợi ý nới lỏng một quy tắc, chỉ vì "code hiện tại chưa làm được đến thế".
