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
- [ ] **Trạng thái đã cập nhật** trong `feature_list.json` của repo triển khai (và `initiatives-index.json` ở đây nếu là feature nhiều repo)

## Tiêu Chí Hoàn Thành Trong Issue Chỉ Là Bổ Sung

Field "Tiêu chí hoàn thành bổ sung" trong issue/template `feature.yml` **không lặp lại baseline ở trên** - baseline áp dụng mặc định cho mọi issue, không cần ghi lại. Field đó chỉ dùng để ghi điều kiện **đặc thù** của riêng feature đang tạo (ví dụ: endpoint cụ thể phải trả về gì, hành vi UI cụ thể nào, ngưỡng hiệu năng cụ thể). Nếu feature không có gì đặc thù ngoài baseline, để trống field đó.
