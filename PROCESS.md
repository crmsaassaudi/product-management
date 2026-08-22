# Quy Trình Issue-ID Xuyên Project

Đây là **bản duy nhất** của quy tắc "id feature = số issue GitHub". `crm-api/AGENTS.md`, `crm-web/AGENTS.md`, và root `e:\CRM\AGENTS.md` trỏ tới file này thay vì lặp lại toàn văn - sửa quy tắc thì sửa ở đây, không sửa từng bản sao.

> **Hiệu lực từ 2026-08-19.** Feature nào đã có issue tạo trước ngày này (kể cả `feat-001`..`feat-022` ở crm-api/crm-web, đánh số tuần tự không tương ứng issue GitHub) giữ nguyên, không hồi tố.

## Làm Việc Song Song (Parallel Sessions)

Khi nhiều phiên chạy song song, mỗi phiên làm trên **1 feature riêng, 1 git worktree/branch riêng** - không bao giờ sửa trực tiếp trên `main`. Vấn đề cần tránh: 2 phiên tự sinh `id` cục bộ cùng lúc sẽ đụng nhau vì không có gì đồng bộ giữa 2 phiên đó.

Giải pháp: dùng **GitHub issue ở `crmsaassaudi/product-management` làm nguồn cấp `id` duy nhất cho TOÀN BỘ tổ chức** (không phải issue ở repo triển khai), vì GitHub cấp số issue một cách nguyên tử trên server dùng chung.

1. **Trước khi bắt đầu một feature chưa có trong `feature_list.json` của repo triển khai**: tạo issue tại `product-management`, gắn label repo bị ảnh hưởng:

   ```
   gh issue create --repo crmsaassaudi/product-management \
     --title "<tên feature>" --assignee @me --label "repo:<tên-repo>"
   ```

2. **Đặt `id` của feature bằng chính số issue đó**: `feat-<số-issue>` (ví dụ issue #482 → `id: "feat-482"`).
3. **Đặt tên branch theo cùng số đó**: `feat/<số-issue>-<slug-ngắn>`, ở đúng repo triển khai (không phải ở `product-management`).
4. **Claim = tự gán issue cho mình** (`--assignee @me` ở bước 1). Trước khi chọn một feature, kiểm tra issue tương ứng ở `product-management` còn assignee trống hay không - đây là điểm tra duy nhất, không cần soi từng repo.
5. **Đóng issue - feature chỉ chạm 1 repo**: PR ở repo triển khai dùng footer `Closes crmsaassaudi/product-management#<số-issue>` (GitHub hỗ trợ auto-close chéo repo khi actor có quyền push cả 2 repo). Nếu không tự đóng (cơ chế cross-repo đôi khi không nhất quán), đóng thủ công bằng `gh issue close crmsaassaudi/product-management#<N> --reason completed`.
6. **Đóng issue - feature chạm nhiều repo**: KHÔNG dùng `Closes` ở bất kỳ PR nào trong số các repo liên quan (tránh đóng sớm khi mới 1/N repo xong). Mọi PR chỉ tham chiếu `crmsaassaudi/product-management#<N>` (link, không auto-close). Issue giữ 1 checklist các repo/PR liên quan trong mô tả; phiên nào merge PR cuối cùng chịu trách nhiệm soát checklist rồi tự đóng issue bằng `gh issue close`.
7. Đồng thời cập nhật `status` trong `feature_list.json` của (các) repo triển khai khi hoàn thành.

## Branch Cho Fix/Chore Phát Sinh (Không Phải Feature Mới)

Quy tắc "branch = `feat/<số-issue>-<slug>`" ở trên mô tả luồng feature đã có issue tạo sẵn từ đầu. Nhưng **mọi branch** - kể cả `fix/...`/`chore/...`/`refactor/...` phát sinh giữa chừng (bug tự phát hiện, cải tiến hạ tầng, dọn dẹp không nằm trong issue nào) - cũng phải đính kèm số issue trong tên, để sau này tra ngược được nguồn gốc. Không có ngoại lệ "quá nhỏ nên khỏi cần số".

1. **Fix/chore là hệ quả trực tiếp của một issue đang/đã làm** (ví dụ: phát hiện bug ở chính PR vừa merge cho issue đó): dùng lại số issue đó. Ví dụ: đang làm issue #2, phát hiện bug ở phần vừa implement → branch `fix/2-<slug-ngắn>`.
2. **Fix/chore không sinh ra từ issue cụ thể nào** (người dùng yêu cầu trực tiếp ngoài luồng issue, hoặc tự phát hiện không liên quan feature đang làm): tạo issue mới ở `product-management` trước, như bước 1 ở mục "Làm Việc Song Song" bên trên, để lấy số nguyên tử - rồi mới đặt tên branch theo số đó (`fix/<N>-<slug>` hoặc `chore/<N>-<slug>`).
3. Quy tắc đóng issue (mục 5-6 ở trên) áp dụng y hệt cho các branch này khi có `Closes` phù hợp - một fix/chore gắn số issue của chính feature gốc thường KHÔNG tự đóng issue đó lần nữa (issue đã đóng khi feature merge); chỉ tham chiếu `crmsaassaudi/product-management#<N>` trong message, không lặp lại `Closes`.

## Feature Chạm Nhiều Project (Cross-Project Feature)

Ví dụ: đổi API ở `crm-api` + UI ở `crm-web` cho cùng 1 tính năng.

1. Tạo **1 GitHub issue duy nhất** ở `crmsaassaudi/product-management`, gắn label mọi repo liên quan (ví dụ `repo:crm-api` + `repo:crm-web`).
2. Chọn 1 **repo chính** - thường là repo sở hữu contract/dữ liệu (thường là backend, vì frontend phụ thuộc vào nó). Repo chính dùng số issue đó làm `id` feature (`feat-<N>`) và tên branch cho `feature_list.json` của nó; các repo còn lại cũng dùng cùng `id`/tên branch cho `feature_list.json` riêng của chúng nếu cần track.
3. Ở **mọi** repo liên quan, PR chỉ **tham chiếu** bằng `crmsaassaudi/product-management#<số-issue>` - không dùng `Closes #N` ở bất kỳ đâu (xem mục 6 ở trên).
4. Mỗi repo verify và commit **độc lập** theo `init.sh`/quy tắc commit của chính nó - không có gate chung nào chờ cả các repo cùng xong mới merge, trừ khi người dùng yêu cầu khác.
5. Ghi lại initiative vào [`initiatives-index.json`](./initiatives-index.json) của `product-management`: `{id, title, primary_repo, affected_repos, status, links[]}` - chỉ dùng cho feature nhiều repo, feature 1-repo không cần entry ở đây (tránh 2 nguồn sự thật).

## Format Issue

Ưu tiên dùng template [`feature.yml`](./.github/ISSUE_TEMPLATE/feature.yml) khi tạo issue qua giao diện GitHub. Khi tạo qua `gh issue create` (CLI), nhớ thêm label `repo:<tên-repo>` cho từng repo bị ảnh hưởng để lọc được.
