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
- SRS viết theo văn phong Business Analyst: theo tính năng/use case, quy tắc nghiệp vụ bằng ngôn ngữ nghiệp vụ - **không** trích dẫn code, tên hàm, hay `file:line`. Vẫn cần đọc code/khảo sát hệ thống thật trước khi viết (để mô tả đúng hành vi), nhưng phần đó không xuất hiện trong thân tài liệu.
- Thuật ngữ chốt trong lúc viết/review SRS ghi vào [`../CONTEXT.md`](../CONTEXT.md) (glossary dùng chung cho mọi SRS ở đây, theo nhóm subheading từng module) - không định nghĩa lại thuật ngữ khác đi giữa các SRS.
- Quyết định khó đảo ngược/có đánh đổi thật phát sinh trong lúc review (ví dụ đổi chính sách phân giải xung đột phân quyền) ghi thành ADR tại [`../docs/adr/`](../docs/adr/), đánh số tuần tự - xem tiêu chí "khi nào cần ADR" trong skill `domain-modeling`.
