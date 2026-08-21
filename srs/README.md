# srs/

Software Requirements Specification (SRS) cho các module **đã triển khai xong nhưng chưa từng có SRS** - viết ngược từ code thực tế (reverse-engineered), không phải spec viết trước khi code.

Khác với [`specs/`](../specs/README.md) (spec/PRD nháp *trước khi* code, dùng để bàn bạc/lên issue), tài liệu ở đây mô tả **hành vi hiện tại của hệ thống**, dùng làm:

- Nguồn tham chiếu duy nhất khi onboard người mới vào module.
- Baseline để đánh giá thay đổi có phải là "regression" hay "cải tiến có chủ đích".
- Input cho audit/security review sau này (không phải thay thế audit report - xem `docs/audit/` ở từng repo triển khai).

## Quy ước

- Tên file: `<module-slug>-srs.md`.
- Mỗi SRS phải neo vào một **commit cụ thể** của (các) repo triển khai (ghi ở đầu file) - SRS mô tả trạng thái tại thời điểm đó, không tự động đúng mãi mãi. Khi code đổi đáng kể, cập nhật SRS trong cùng PR hoặc issue theo dõi riêng, đừng để SRS trôi khỏi code.
- Mọi yêu cầu (FR/NFR) nên có trích dẫn `file:line` ở repo triển khai để truy vết - đây là tài liệu bám sát code thật, không phải mô tả trừu tượng.
- Nếu module đã có audit report riêng (ví dụ `docs/audit/OBJECT_MANAGER_AUDIT.md` ở `crm-api`), SRS **không lặp lại** danh sách defect - chỉ dẫn chiếu và tóm tắt phần còn ảnh hưởng đến hành vi hiện tại ở mục "Giới hạn đã biết".
- SRS viết theo văn phong Business Analyst: theo tính năng/use case, quy tắc nghiệp vụ bằng ngôn ngữ nghiệp vụ - **không** trích dẫn code, tên hàm, hay `file:line`. Vẫn cần đọc code/khảo sát hệ thống thật trước khi viết (để mô tả đúng hành vi), nhưng phần đó không xuất hiện trong thân tài liệu.
- Thuật ngữ chốt trong lúc viết/review SRS ghi vào [`../CONTEXT.md`](../CONTEXT.md) (glossary dùng chung cho mọi SRS ở đây, theo nhóm subheading từng module) - không định nghĩa lại thuật ngữ khác đi giữa các SRS.
- Quyết định khó đảo ngược/có đánh đổi thật phát sinh trong lúc review (ví dụ đổi chính sách phân giải xung đột phân quyền) ghi thành ADR tại [`../docs/adr/`](../docs/adr/), đánh số tuần tự - xem tiêu chí "khi nào cần ADR" trong skill `domain-modeling`.
