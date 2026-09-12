---
status: proposed
---

# Hợp đồng nghiệp vụ giữa Contacts và IAM: cơ chế chia sẻ bản ghi và thứ tự ưu tiên quyền

**Bối cảnh:** `contacts-srs.md` BR-35.5 giao cơ chế thực thi phạm vi dữ liệu và thứ tự ưu tiên giữa ba nguồn quyền cho `iam-tenant-authorization.md`, và tự giới hạn mình ở việc nêu nhu cầu nghiệp vụ. Vấn đề #8 mục 7 đặt thời hạn chốt **trước khi mở phạm vi phát triển Nhóm K** — tức chặn cả FEAT-34, FEAT-35 và FEAT-36, không riêng FEAT-35.

Khảo sát tài liệu IAM cho một kết quả quan trọng hơn dự kiến: **cơ chế quyền trên một bản ghi cụ thể đã tồn tại và đã triển khai** — FEAT-39, với BR-39.1 → BR-39.4. Nên câu hỏi thật không phải "xây cơ chế gì", mà là **"FEAT-35 có phải là FEAT-39 hay không"**. Câu trả lời là không, và chỗ hai bên khác nhau chính là chỗ hai tài liệu đang bất đồng mà chưa ai phát hiện.

## Hai xung đột thật, phát hiện khi khảo sát

**Xung đột 1 — BR-39.1 và BR-35.5 đọc ngược nhau.**

- BR-39.1 (IAM): *"Quyền chặn tường minh trên bản ghi luôn thắng tuyệt đối."*
- BR-35.5 (Contacts): chia sẻ bản ghi *"chỉ nới rộng, không bao giờ thu hẹp"*.

Cả hai cùng nói về quyền trên một bản ghi cụ thể, và chúng **thật sự mâu thuẫn** nếu coi Đội ngũ Phụ trách là một dạng của FEAT-39. Điều khoản 1 dưới đây tách hai cơ chế ra, và điều khoản 4 chốt thứ tự khi cả hai cùng có mặt.

**Xung đột 2 — hai nguyên tắc hợp nhất trái chiều.**

- [ADR-0001](./0001-group-policy-conflict-resolution.md) chốt **hạn chế thắng** (deny-override) khi các nguồn quyền bất đồng, vì một người bị thêm vào nhóm khác vì lý do không liên quan không nên vô tình được mở khóa dữ liệu nhạy cảm.
- BR-35.5 chốt **chỉ nới rộng**.

Hai câu này chỉ mâu thuẫn nếu đọc chúng như cùng trả lời một câu hỏi. Thực ra chúng trả lời hai câu hỏi khác nhau, và điều khoản 3 chốt ranh giới đó.

## Quyết định

### 1. Đội ngũ Phụ trách (FEAT-35) và Quyền đặc cách trên bản ghi (FEAT-39) là **hai cơ chế khác nhau**

Đây là điều khoản nền: mọi điều còn lại phụ thuộc vào việc tách bạch này.

| | **FEAT-39 — Quyền đặc cách** | **FEAT-35 — Đội ngũ Phụ trách** |
| --- | --- | --- |
| Ai đặt được | Người có quyền *"Quản lý cấu hình hệ thống"* | Người phụ trách bản ghi, Quản lý Kinh doanh (BR-35.2) |
| Mục đích | **Ngoại lệ quản trị** — khách VIP chỉ 2 người được động vào | **Cộng tác vận hành** — nhiều bộ phận cùng phục vụ một khách |
| Hai chiều | **Có** — cấp **và chặn** (BR-39.1) | **Chỉ cấp** (BR-35.5) |
| Thời hạn | Không có | Có (BR-35.3) |
| Tần suất | Hiếm, có chủ đích | Thường xuyên, theo công việc hằng ngày |

Lý do bắt buộc phải tách: nếu Đội ngũ Phụ trách chạy trên FEAT-39, thì **mọi người phụ trách bản ghi đều trở thành người có quyền chặn** — họ sẽ dùng được BR-39.1 để chặn chính Quản lý Kinh doanh hay Quản trị viên khỏi bản ghi mình phụ trách. Một cơ chế cộng tác không được phép trở thành công cụ chống giám sát.

### 2. Bốn trục quyền, và mỗi trục trả lời một câu hỏi riêng

| Trục | Câu hỏi nó trả lời | Tài liệu sở hữu |
| --- | --- | --- |
| **1. Năng lực theo vai trò** | Người này **được phép làm gì** trong workspace? | `iam-tenant-authorization.md` |
| **2. Phạm vi dữ liệu** | Người này **nhìn thấy những bản ghi nào** theo mặc định? | `iam-tenant-authorization.md` |
| **3. Nguồn phạm vi bổ sung** | Ngoài phạm vi mặc định, người này còn được thêm **bản ghi cụ thể nào**? | Cơ chế: IAM (FEAT-39 mở rộng) · Chính sách: `contacts-srs.md` (FEAT-35) |
| **4. Phân quyền trường** | Trên một bản ghi đã tiếp cận được, **từng trường** hiển thị ra sao? | SRS Object Manager + [ADR-0001](./0001-group-policy-conflict-resolution.md) |

Bốn trục **hợp nhất theo thứ tự trên**, và mỗi trục chỉ được phép hẹp hơn hoặc bằng trục đứng trước nó.

### 3. Chia sẻ bản ghi nới rộng **phạm vi dữ liệu**, không nới rộng **năng lực vai trò**

Đây là điều khoản then chốt, và là cách BR-35.5 hoà giải với ADR-0001.

Chia sẻ một bản ghi **thêm bản ghi đó vào phạm vi dữ liệu** của người được chia sẻ. Nó **không** cấp cho họ bất kỳ năng lực nào mà vai trò của họ chưa có.

Diễn giải cụ thể:

- Người không có quyền `contacts:edit` theo vai trò, khi được thêm vào Đội ngũ Phụ trách ở mức **Chỉnh sửa**, **vẫn không sửa được**. Mức "Chỉnh sửa" của FEAT-35 là **trần trên của lượt chia sẻ**, không phải một lượt cấp quyền.
- Ngược lại, người có `contacts:edit` nhưng bản ghi nằm ngoài phạm vi dữ liệu, khi được chia sẻ ở mức Chỉnh sửa, **sửa được** — vì cả hai điều kiện cùng thoả.
- Quyền hiệu lực trên một bản ghi được chia sẻ là **giao** của năng lực vai trò và mức chia sẻ, không phải hợp.

Không có điều khoản này, chia sẻ bản ghi trở thành đường vòng quanh mô hình phân quyền: bất kỳ ai phụ trách một bản ghi đều có thể cấp cho đồng nghiệp năng lực mà quản trị viên đã cố tình không cấp. Tài liệu IAM đã đóng đúng lỗ hổng này ở FEAT-11 — *"muốn cấp thêm một quyền đơn lẻ ngoài vai trò → không hỗ trợ"* — và chia sẻ bản ghi không được phép mở lại.

### 4. Quyền chặn tường minh (BR-39.1) thắng một lượt chia sẻ (BR-35.5)

Đây là lời giải cho **xung đột 1**, và nó phải được phát biểu tường minh vì hai tài liệu hiện đang nói ngược nhau.

Khi một bản ghi vừa có lượt chặn theo FEAT-39, vừa có người trong Đội ngũ Phụ trách: **lượt chặn thắng**. Người bị chặn không tiếp cận được bản ghi dù họ có trong Đội ngũ.

Vì sao chọn chiều này, dù BR-35.5 nói "không bao giờ thu hẹp":

- Hai điều khoản không cùng cấp. BR-39.1 là **quyết định của quản trị viên workspace**, có chủ đích, hiếm, và thường mang nghĩa pháp lý hoặc hợp đồng (khách VIP, hồ sơ đang tranh chấp, `RESTRICTED` theo BR-30.6). BR-35.5 là **quyết định vận hành hằng ngày** của một người phụ trách bản ghi.
- Nếu chiều ngược lại, quyết định hiếm-và-có-chủ-đích sẽ bị vô hiệu hoá bởi thao tác thường-ngày: bất kỳ người phụ trách nào cũng có thể gỡ một lượt chặn của quản trị viên chỉ bằng cách thêm người đó vào Đội ngũ.
- Cách đọc đúng của BR-35.5 vì vậy là: **một lượt chia sẻ không bao giờ thu hẹp quyền — nhưng nó cũng không phải công cụ để vượt một lượt chặn.** Lượt chặn không phải do chia sẻ tạo ra; nó có sẵn và độc lập.

**Điều kiện bắt buộc kèm theo:** khi một lượt chặn làm vô hiệu một lượt chia sẻ, hệ thống phải **nói rõ với người chia sẻ** rằng lượt chia sẻ không có hiệu lực và vì sao (ở mức không lộ nội dung bản ghi). Nếu không, người chia sẻ tin rằng đồng nghiệp đã có quyền truy cập, và công việc dừng ở một chỗ không ai biết là đang dừng.

### 5. "Chỉ nới rộng" của BR-35.5 nói về **phạm vi**, "hạn chế thắng" của ADR-0001 nói về **trường**

Hai nguyên tắc áp lên hai trục khác nhau nên không bao giờ gặp nhau:

- **Trục phạm vi dữ liệu (trục 2 và 3):** cộng gộp. Một lượt chia sẻ chỉ có thể **thêm** bản ghi vào tầm nhìn, không bao giờ lấy đi bản ghi mà người dùng vốn đã thấy theo vai trò hoặc đơn vị tổ chức. Đây là BR-35.5.
- **Trục phân quyền trường (trục 4):** hạn chế thắng. Sau khi đã tiếp cận được bản ghi, việc từng trường hiện ra sao hoàn toàn do ADR-0001 quyết định, và **tư cách thành viên Đội ngũ Phụ trách không nới lỏng được bất kỳ mức che nào**.

Hệ quả cụ thể và cố ý: một người được chia sẻ bản ghi ở mức Chỉnh sửa **vẫn thấy định danh KYC bị che hoàn toàn** nếu nhóm quyền của họ che trường đó. Chia sẻ mở cánh cửa vào bản ghi; nó không mở két sắt bên trong.

### 6. Quyền đọc tự động của tuyến Hỗ trợ (BR-35.4) là chia sẻ có điều kiện, không phải ngoại lệ

BR-35.4 cấp quyền đọc theo sự kiện (vé/hội thoại đang mở) thay vì theo thao tác của con người. Về mô hình, nó là **một lượt chia sẻ mức Chỉ đọc có điều kiện tự động**, chịu đúng ba điều khoản trên:

- Nới rộng phạm vi dữ liệu, không cấp năng lực mới. Hai thao tác ghi mà BR-35.4(a) cho phép — gắn `RESTRICTED` và hạ đồng thuận — **không phải ngoại lệ của điều khoản 3**: chúng vẫn đòi vai trò Hỗ trợ phải có năng lực tương ứng; điều BR-35.4 mở là **phạm vi**, cho phép thực hiện chúng trên bản ghi ngoài phạm vi gán.
- Che mặt nạ theo cột (C) của BR-04.3 là một quyết định của **trục 4**, và đã đúng với điều khoản 5.
- Tự hết hiệu lực khi sự kiện đóng (BR-35.4d) — tương đương ngày hết hiệu lực của BR-35.3.

### 7. Ranh giới trách nhiệm hai tài liệu

**`iam-tenant-authorization.md` sở hữu:**

- Danh mục năng lực (permission keys) và ánh xạ vai trò → năng lực.
- Cơ chế tính phạm vi dữ liệu theo đơn vị tổ chức và nhóm.
- **Hàm hợp nhất bốn trục** và thứ tự áp dụng của chúng.
- Cơ chế lưu và thực thi quyền trên một bản ghi cụ thể (FEAT-39), **mở rộng để nhận một lượt cấp có thời hạn và có mức trần do phân hệ nghiệp vụ sinh ra** — phần mở rộng này là việc IAM phải làm; cơ chế nền đã có.

**`contacts-srs.md` sở hữu:**

- Ai được chia sẻ cho ai, ở mức nào, trong bao lâu (BR-35.1 → BR-35.3).
- Các điều kiện sinh ra một lượt chia sẻ tự động (BR-35.4).
- Vai trò tham gia Đội ngũ (danh mục A.13) và nghĩa vụ kiểm toán (BR-35.6).

**Không tài liệu nào sở hữu một mình:** thứ tự ưu tiên khi bốn trục bất đồng. Đó là nội dung của chính ADR này, và cả hai tài liệu dẫn chiếu về đây.

## Phương án đã cân nhắc

- **Phương án 1 — Chia sẻ bản ghi cấp cả năng lực lẫn phạm vi ("chia sẻ là một lượt cấp quyền mini").** Bị từ chối: biến mỗi người phụ trách bản ghi thành một quản trị viên phân quyền không được kiểm soát, và mở lại đúng lỗ hổng mà IAM FEAT-11 đã đóng. Cũng khiến "Đội ngũ Phụ trách" trở thành đường leo thang đặc quyền, ngược [ADR-0002](./0002-delegated-grant-authority-ceiling-exception.md).

- **Phương án 2 — Áp hạn chế thắng cho cả trục phạm vi.** Bị từ chối vì phá chính mục đích của FEAT-35: nếu một lượt chia sẻ có thể bị nhóm quyền "phủ quyết", thì chia sẻ bản ghi không giải quyết được bế tắc truy cập của tuyến Hỗ trợ — bế tắc mà FEAT-35 được sinh ra để giải. Đây cũng không phải điều ADR-0001 nói: quyết định gốc nói về **cấu hình phân quyền trường giữa các nhóm**, không nói về việc bản ghi nào lọt vào tầm nhìn.

- **Phương án 3 — Bốn trục tách biệt, mỗi trục một nguyên tắc hợp nhất riêng (được chọn).** Giữ nguyên hiệu lực của cả ADR-0001 lẫn BR-35.5 mà không phải sửa câu nào của hai bên, vì làm rõ rằng chúng chưa từng nói về cùng một thứ.

## Hệ quả

**Với `iam-tenant-authorization.md` — phải bổ sung trước khi mở Nhóm K:**

1. **Mở rộng FEAT-39** để nhận một lượt cấp do phân hệ nghiệp vụ sinh ra, với ba thuộc tính mà FEAT-39 hiện chưa có: **mức trần** (Chỉ đọc / Chỉnh sửa), **thời hạn** (BR-35.3), và **nguồn sinh** (thủ công theo FEAT-35, hay tự động theo BR-35.4). Cơ chế nền đã triển khai; đây là phần mở rộng, không phải xây mới.
2. **BR-39.1 phải nói rõ phạm vi của "thắng tuyệt đối"**: nó thắng mọi lượt **cấp**, gồm cả lượt cấp do FEAT-35 sinh ra. Câu hiện tại chỉ đối chiếu với "vai trò/phạm vi dữ liệu thông thường" nên không trả lời được tình huống này.
3. **Phát biểu hàm hợp nhất bốn trục** và điều khoản 3 (giao, không phải hợp) thành quy tắc nghiệp vụ trong thân tài liệu IAM, để phần dùng nghiệm thu không phải tra cứu sang ADR — đúng cách ADR-0001 đã được đưa vào thân SRS Object Manager.
4. **FEAT-15 (Xem quyền hiệu lực)** phải nêu được **vì sao** một người tiếp cận được một bản ghi: theo vai trò, theo đơn vị, theo lượt cấp đặc cách, hay theo một lượt chia sẻ — và nếu là chia sẻ thì lượt nào, ai chia sẻ, hết hạn khi nào. Không có điều này, một quyền truy cập bất thường không truy được nguồn, và nghĩa vụ kiểm toán tại BR-35.6 không có dữ liệu để thực hiện.
5. **BR-39.3 áp luôn cho lượt cấp của FEAT-35.** Quy tắc "có tác dụng đồng bộ ở mọi nơi bản ghi xuất hiện — List View, Export, Dashboard, Related List" đã được viết cho FEAT-39 và chính là thứ FEAT-35 cần; nếu không nói rõ, một người trong Đội ngũ Phụ trách sẽ mở được bản ghi qua đường dẫn trực tiếp nhưng không tìm thấy nó trong danh sách.

**Với `contacts-srs.md`:**

6. BR-35.1 phải nói rõ mức "Chỉnh sửa" là **trần trên của lượt chia sẻ**, không phải một lượt cấp quyền — câu chữ hiện tại có thể đọc thành cấp quyền sửa.
7. BR-35.5 bổ sung: lượt chia sẻ **không vượt được** một lượt chặn tường minh theo BR-39.1 của tài liệu IAM.
8. Vấn đề #8 mục 7 chuyển sang **"Đã chốt"**, dẫn chiếu ADR này.

**Rủi ro đã biết và chấp nhận:** điều khoản 3 tạo ra tình huống một người được mời vào Đội ngũ ở mức Chỉnh sửa nhưng vẫn không sửa được, và điều khoản 4 tạo ra tình huống một lượt chia sẻ hoàn toàn không có hiệu lực. Cả hai đều đúng theo thiết kế, và cả hai đều **hỏng nếu màn hình im lặng**: người mời sẽ tin mình đã cấp quyền, người được mời thấy hệ thống lỗi, và không ai biết công việc đang dừng ở đâu. Chi phí của việc giữ mô hình phân quyền không bị đi vòng được trả bằng thông báo rõ ràng, không bằng cách nới điều khoản.

**Xác nhận:** ☐ Chờ Chủ sở hữu tài liệu IAM xác nhận (mục 10 `contacts-srs.md`).
