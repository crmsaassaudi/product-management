# product-management

Đây là nơi tạo và theo dõi **issue GitHub cho feature** của toàn bộ tổ chức `crmsaassaudi` (crm-api, crm-web, crm-manager-api, crm-manager-web, crm-bot, crm-auth, và các repo khác). Repo này **không chứa code ứng dụng** - nó chỉ là quy trình quản lý feature xuyên project.

- Quy tắc tạo/đóng issue, quy ước `id` feature: xem [`PROCESS.md`](./PROCESS.md).
- Định nghĩa "done" áp dụng chung cho mọi feature (baseline toàn tổ chức): xem [`AGENTS.md`](./AGENTS.md).
- Tạo issue feature mới: dùng template tại [`.github/ISSUE_TEMPLATE/feature.yml`](./.github/ISSUE_TEMPLATE/feature.yml) (hoặc `gh issue create --repo crmsaassaudi/product-management`).
- Spec/PRD nháp trước khi lên issue: thư mục [`specs/`](./specs/).
- Theo dõi feature chạm nhiều repo cùng lúc: [`initiatives-index.json`](./initiatives-index.json).

Xem thêm `e:\CRM\AGENTS.md` (root router) để biết cách quy trình này khớp với harness của từng project con.
