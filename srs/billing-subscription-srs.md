# SRS — Billing & Subscription

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — chuẩn nghiệp vụ cho một module chưa xây dựng (xem "Ghi chú về nguồn gốc tài liệu") |
| **Module** | Billing & Subscription — đăng ký gói cước, đo lường tiêu dùng, lập hóa đơn, thu tiền và quản lý vòng đời thương mại của từng doanh nghiệp (tenant) dùng CRM |
| **Ngày viết** | 2026-08-24 |
| **Sửa đổi lần cuối** | 2026-08-25 — rà soát toàn văn, bổ sung FEAT-18b và các quy tắc còn thiếu (xem "Nhật ký sửa đổi") |
| **Trạng thái phạm vi** | **ĐÃ ĐÓNG (frozen)** kể từ 2026-08-25. Phạm vi nghiệp vụ của module này không được mở rộng hay sửa đổi thêm; mọi yêu cầu phát sinh về sau đi qua quy trình thay đổi có kiểm soát và tạo phiên bản tài liệu mới, không sửa tại chỗ. Các câu hỏi ở Mục 7.2 là **quyết định cần chốt**, không phải phạm vi cần mở. |
| **Neo phiên bản hệ thống** | Chưa xác định — module chưa có phần triển khai nào tại thời điểm viết. Khi những hạng mục đầu tiên lên môi trường thật, tài liệu PHẢI được neo lại vào commit cụ thể của (các) repo triển khai. |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary, mục "Billing & Subscription") · [`omnichat-srs.md`](./omnichat-srs.md) · [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) |

## Ghi chú về nguồn gốc tài liệu

Khác với các SRS còn lại trong thư mục này (viết ngược từ hệ thống đã vận hành), tài liệu này được viết **trước khi xây dựng**: CRM hiện đã có Omnichat, IAM/workspace và Object Manager đang chạy nhưng chưa có bất kỳ cơ chế thu tiền nào. Vì vậy **toàn bộ Mục 3 mang nhãn `[Yêu cầu mới]`** — không có mục nào được xác minh khớp với hệ thống đang chạy. Nhãn `[Đã triển khai]` sẽ chỉ xuất hiện khi từng hạng mục thực sự lên môi trường thật và được đối chiếu lại.

**Nguyên tắc biên soạn:** đây là tài liệu nghiệp vụ. Nó đặc tả *hệ thống phải làm gì cho nhà cung cấp CRM và cho doanh nghiệp trả tiền*, không đặc tả *hệ thống được xây dựng bằng cách nào*. Cụ thể, tài liệu **không nêu tên hệ tính phí, cổng thanh toán hay công cụ đo lường nào** trong thân các quy tắc — lựa chọn đó là một quyết định kiến trúc riêng, ghi tại [ADR-0004](../docs/adr/0004-billing-engine-boundary.md), và có thể thay đổi mà không làm sai một quy tắc nghiệp vụ nào ở đây. Mọi quy tắc PHẢI diễn đạt được bằng ngôn ngữ nghiệp vụ và kiểm chứng được bằng hành vi quan sát từ bên ngoài.

Các ngưỡng thời gian, số lần, số lượng, đơn giá đều là **tham số cấu hình thương mại**, không phải hằng số của sản phẩm; tài liệu chỉ cố định một con số khi con số đó chính là cam kết với người trả tiền.

**Quy ước nhãn trạng thái:** mỗi tính năng (FEAT) được gắn nhãn ngay sau tiêu đề — `[Đã triển khai]` (đã xác minh khớp hệ thống đang chạy) hoặc `[Yêu cầu mới]` (đã chốt phương án nghiệp vụ, chưa xây dựng). Tham chiếu GitHub issue/ADR tương ứng ghi ở cuối mục, dưới dòng **Tham chiếu**.

---

## 1. Giới thiệu

### 1.1 Mục đích

Tài liệu đặc tả toàn bộ yêu cầu chức năng và phi chức năng của module **Billing & Subscription** — lớp thương mại của CRM đa doanh nghiệp, chịu trách nhiệm trả lời ba câu hỏi: **một doanh nghiệp đang mua gì**, **họ đã dùng bao nhiêu**, và **họ phải trả bao nhiêu, đã trả chưa**.

Tài liệu phục vụ hai nhóm mục tiêu đối lập nhau mà thiết kế PHẢI cân bằng: nhà cung cấp CRM cần **thu đúng và thu đủ** phần chi phí biến đổi mình đã bỏ ra (tin nhắn mẫu trả cho nền tảng kênh, dung lượng, token AI...), còn doanh nghiệp trả tiền cần **hiểu được, kiểm chứng được và dự đoán được** hóa đơn của mình. Một hệ thống tính phí đúng về số học nhưng doanh nghiệp không tự đối chiếu được là một hệ thống thất bại — vì mỗi hóa đơn không giải thích được đều trở thành một cuộc khiếu nại.

### 1.2 Phạm vi

**Vòng đời thương mại của một doanh nghiệp:** danh mục gói cước và phiên bản gói, hồ sơ thanh toán, dùng thử và chuyển đổi sang trả phí, đăng ký gói, nâng/hạ gói giữa kỳ, add-on và mua thêm hạn mức, chiết khấu và giá thương lượng riêng, hủy đăng ký.

**Đo lường tiêu dùng:** danh mục loại tiêu dùng tính phí và mã định danh, phát sinh Sự kiện tính phí từ các module nghiệp vụ của CRM, định nghĩa thời điểm tính phí của từng loại, tính phí theo số người dùng, chuyển sự kiện sang hệ tính phí, ghi nhận bù và sự kiện đến muộn, điều chỉnh/hủy hiệu lực sự kiện đã ghi nhận, đối soát, hạn mức bao gồm và phí vượt, theo dõi tiêu dùng và cảnh báo ngưỡng.

**Hóa đơn và thu tiền:** lập và phát hành hóa đơn cuối kỳ, vòng đời và trạng thái của chứng từ đã lập, thuế và yêu cầu hóa đơn hợp lệ, phương thức thanh toán và thu tiền tự động, nhắc nợ khi thanh toán thất bại, đình chỉ và khôi phục dịch vụ, ghi có và hoàn tiền, đa tiền tệ.

**Minh bạch và vận hành:** cổng thanh toán tự phục vụ, truy vết từ dòng hóa đơn xuống đối tượng gốc và xử lý khiếu nại, công cụ quản trị của nhà cung cấp, nhật ký kiểm toán thương mại, báo cáo doanh thu và sức khỏe đăng ký, thông báo về thanh toán, và nguyên tắc cách ly ảnh hưởng giữa lớp tính phí và nghiệp vụ CRM.

**Ngoài phạm vi:**

- **Nghiệp vụ tạo ra tiêu dùng.** Cách một hội thoại được tiếp nhận, phân công và đóng thuộc [`omnichat-srs.md`](./omnichat-srs.md); tài liệu này chỉ nói hội thoại đó tạo ra bao nhiêu đơn vị tính phí và tại thời điểm nào.
- **Mô hình workspace, vai trò và cấp bậc thành viên.** Thuộc [`iam-tenant-authorization.md`](./iam-tenant-authorization.md); tài liệu này chỉ bổ sung một vai trò mới (Người phụ trách thanh toán) và quy định ai thấy được dữ liệu tài chính.
- **Chiến lược định giá** — mức giá cụ thể của từng gói, mức chiết khấu, chính sách khuyến mãi. Tài liệu này đặc tả *hệ thống phải cho phép biểu diễn và thực thi những mô hình giá nào*, không quyết định con số.
- **Kế toán nội bộ của nhà cung cấp** — sổ cái, đối chiếu ngân hàng, quyết toán thuế. Hệ thống cung cấp chứng từ và số liệu đầu ra; việc hạch toán do hệ thống kế toán riêng đảm nhiệm.
- **Hoa hồng đại lý và bán lại qua đối tác** — xem Mục 7.1.

### 1.3 Đối tượng đọc

- Business Analyst / Product Owner: căn cứ để lên issue và xếp thứ tự xây dựng.
- Kỹ sư phát triển: hiểu nhà cung cấp và doanh nghiệp trả tiền cần gì trước khi bắt tay xây dựng — tài liệu này nói *phải làm gì*, phương án xây dựng do đội phát triển quyết định sau và không nằm trong phạm vi tài liệu.
- QA: căn cứ viết test case chấp nhận, đặc biệt cho các quy tắc về không tính trùng, không mất sự kiện và bất biến chứng từ.
- Đội kinh doanh và chăm sóc khách hàng: hiểu chính xác hệ thống hứa gì với doanh nghiệp về hóa đơn, hạn mức và đình chỉ — để không hứa vượt.
- Kế toán/tài chính của nhà cung cấp: kiểm tra tài liệu có đáp ứng yêu cầu chứng từ và ghi nhận doanh thu hay không **trước khi** xây dựng.

### 1.4 Thuật ngữ & viết tắt

| Thuật ngữ | Giải thích |
| --- | --- |
| **Doanh nghiệp (Tenant)** | Một tổ chức khách hàng dùng CRM, có dữ liệu cách ly hoàn toàn với tổ chức khác. Là chủ thể trả tiền trong tài liệu này. |
| **Nhà cung cấp** | Đơn vị vận hành CRM và phát hành hóa đơn cho doanh nghiệp. Nhân sự của nhà cung cấp có các vai trò riêng, tách biệt hoàn toàn với vai trò bên trong workspace của doanh nghiệp. |
| **Hồ sơ thanh toán (Billing Account)** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Gói cước (Plan)** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Phiên bản gói (Plan Version)** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Đăng ký (Subscription)** | Quan hệ thương mại đang hiệu lực giữa một hồ sơ thanh toán và một phiên bản gói, có chu kỳ và trạng thái riêng — xem FEAT-04. |
| **Chu kỳ tính phí (Billing Period)** | Khoảng thời gian một hóa đơn bao trùm, mốc bắt đầu cố định theo ngày kích hoạt đăng ký. |
| **Loại tiêu dùng tính phí (Billable Usage Type)** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Sự kiện tính phí (Billing Event)** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Thời điểm tính phí (Billable Moment)** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Mã tham chiếu (Reference)** | Định danh của đối tượng nghiệp vụ gốc sinh ra một sự kiện tính phí (một hội thoại, một tin nhắn mẫu, một lượt chạy quy trình). Là căn cứ duy nhất để không tính phí trùng — xem BR-12.1. |
| **Hạn mức bao gồm (Included Quota)** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Phí vượt hạn mức (Overage)** | Phần tiền phát sinh cho lượng tiêu dùng vượt quá hạn mức bao gồm trong kỳ, tính theo đơn giá công bố của phiên bản gói đang áp dụng. |
| **Trần chi phí vượt (Overage Ceiling)** | Mức phí vượt tối đa của một doanh nghiệp trong một kỳ. Với tiêu dùng do doanh nghiệp chủ động tạo ra, đây là **trần chặn**; với tiêu dùng do khách hàng của doanh nghiệp khởi xướng, đây là **trần tính tiền** và phần vượt trở thành chi phí nhà cung cấp tự chịu — xem BR-16.5 và BR-16.8. |
| **Chính sách chạm trần (Quota Policy)** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Thời hạn chốt kỳ (Period Close Window)** | Khoảng ân hạn sau ngày cuối kỳ, trong đó sự kiện đến muộn vẫn được quy về đúng kỳ đó trước khi hóa đơn được phát hành — xem BR-13.2. |
| **Bút toán đảo (Reversal)** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Đối soát tiêu dùng (Usage Reconciliation)** | Việc so khớp định kỳ giữa số đối tượng nghiệp vụ có thật trong CRM và số đơn vị tính phí mà lớp thương mại đang ghi nhận — xem FEAT-15. |
| **Ngày tới hạn (Due Date)** | Ngày một hóa đơn phải được thanh toán, tính từ ngày hóa đơn thực sự phát hành cộng khoảng ân hạn thanh toán của phương thức đang áp dụng. Là mốc khởi phát duy nhất của chu trình nhắc nợ — xem BR-18.9. |
| **Nhắc nợ (Dunning)** | Chu trình tự động gồm các lần thử thu lại và các lần thông báo, kích hoạt khi một hóa đơn tới hạn chưa thu được — xem FEAT-21. |
| **Đình chỉ dịch vụ (Suspension)** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Chứng từ ghi có (Credit Note)** | Chứng từ giảm trừ một hóa đơn đã phát hành. Là cách duy nhất để sửa một hóa đơn đã phát hành — xem BR-18.2 và FEAT-23. |
| **Người phụ trách thanh toán (Billing Contact)** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Doanh thu định kỳ hàng tháng (MRR)** | Phần doanh thu đến từ phí thuê bao cố định, quy về mức tháng. Luôn tách bạch với doanh thu tiêu dùng — xem BR-30.2. |
| **Tỷ lệ rời bỏ (Churn)** | Tỷ lệ doanh nghiệp ngừng trả tiền trong kỳ, đo theo cả số lượng doanh nghiệp và theo giá trị doanh thu mất đi. |
| **Giá vốn tiêu dùng (Usage Cost of Goods)** | Chi phí nhà cung cấp thực trả cho bên thứ ba để phục vụ một đơn vị tiêu dùng (phí nền tảng kênh, phí tin nhắn, phí token AI, phí lưu trữ) — xem BR-30.3. |

### 1.5 Tài liệu tham khảo

- [`CONTEXT.md`](../CONTEXT.md) — glossary thuật ngữ nghiệp vụ dùng chung cho các SRS trong `product-management`, mục "Billing & Subscription".
- [`omnichat-srs.md`](./omnichat-srs.md) — nguồn phát sinh hai loại tiêu dùng của giai đoạn đầu; đặc biệt Mục 7.2 câu 1 (đơn vị công việc của Omnichat) là điều kiện tiên quyết của FEAT-10.
- [`iam-tenant-authorization.md`](./iam-tenant-authorization.md) — mô hình workspace, cấp bậc thành viên và vai trò, làm nền cho Mục 5.
- [ADR-0003](../docs/adr/0003-permission-config-audit-log-fail-closed.md) — Nguyên tắc đóng và quy tắc Fail-closed cho nhật ký cấu hình quyền; FEAT-29 áp dụng lại đúng nguyên tắc này cho nhật ký thương mại.
- [ADR-0004](../docs/adr/0004-billing-engine-boundary.md) — ranh giới trách nhiệm giữa CRM và hệ tính phí bên ngoài, và lý do CRM không tự tính tiền.

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

CRM hiện phục vụ nhiều doanh nghiệp trên cùng một hệ thống nhưng chưa có cách nào thu tiền theo mức độ sử dụng. Ba hệ quả cụ thể:

- **Chi phí biến đổi đang chảy một chiều.** Mỗi tin nhắn mẫu WhatsApp gửi đi là một khoản nhà cung cấp thực trả cho nền tảng kênh. Không đo được thì không thu lại được, và một doanh nghiệp dùng gấp mười lần doanh nghiệp khác vẫn trả như nhau.
- **Không có công cụ nào để bán gói.** Không có khái niệm gói cước, hạn mức, chu kỳ — nên mọi thỏa thuận thương mại nằm ngoài hệ thống, thực thi bằng niềm tin và bằng thao tác thủ công.
- **Không có số liệu để ra quyết định giá.** Không biết một doanh nghiệp tiêu thụ bao nhiêu, loại tiêu dùng nào có biên lợi nhuận âm, doanh nghiệp nào sắp rời bỏ.

Module này giải quyết bằng cách đặt **một lớp đo lường trung lập** giữa nghiệp vụ CRM và việc tính tiền: các module nghiệp vụ chỉ tuyên bố "một việc đáng tính phí vừa xảy ra", còn toàn bộ chuyện giá, hạn mức, hóa đơn và thu tiền nằm ở lớp thương mại phía sau. Nhờ vậy, đổi mô hình giá không phải sửa nghiệp vụ, và thêm một nguồn doanh thu mới không phải sửa quy tắc đã có.

**Mục tiêu kinh doanh và chỉ số thành công.** Bốn mục tiêu dưới đây là căn cứ xếp thứ tự ưu tiên khi phải chọn giữa các yêu cầu trong tài liệu này; một yêu cầu không phục vụ mục tiêu nào trong số đó cần được chất vấn lại trước khi đưa vào kế hoạch.

| # | Mục tiêu | Đo bằng |
| --- | --- | --- |
| 1 | **Thu đúng phần đã phục vụ — không thất thoát và không thu oan** | Chênh lệch đối soát giữa tiêu dùng thực tế và tiêu dùng đã tính phí; tỷ lệ giá trị bị đảo/ghi có trên tổng doanh thu; số hóa đơn phải phát hành lại |
| 2 | **Doanh nghiệp tự hiểu và tự kiểm chứng được hóa đơn của mình** | Tỷ lệ hóa đơn phát sinh khiếu nại; tỷ lệ khiếu nại kết luận "hệ thống tính đúng nhưng khách không hiểu"; tỷ lệ thao tác thương mại doanh nghiệp tự làm được không cần liên hệ nhà cung cấp |
| 3 | **Thu được tiền mà không mất khách vì cách đòi** | Tỷ lệ thu thành công ở lần thử đầu; tỷ lệ thu hồi được sau nhắc nợ; tỷ lệ rời bỏ có nguyên nhân từ đình chỉ nhầm hoặc đình chỉ quá tay |
| 4 | **Nhà cung cấp nhìn được sức khỏe kinh doanh mà không cần chờ kế toán chốt sổ** | Độ trễ của số liệu doanh thu; biên lợi nhuận theo từng loại tiêu dùng; tỷ lệ chuyển đổi từ dùng thử sang trả phí |

### 2.2 Nguyên tắc phân định trách nhiệm

Ba nguyên tắc dưới đây chi phối toàn bộ Mục 3 và là lý do tài liệu được cấu trúc như hiện tại.

| Nguyên tắc | Nội dung | Hệ quả ràng buộc |
| --- | --- | --- |
| **CRM là nguồn sự thật về việc đã xảy ra** | CRM sở hữu doanh nghiệp, người dùng, hội thoại, tin nhắn, quy trình, chiến dịch — và là nơi duy nhất biết một việc đáng tính phí có thực sự xảy ra hay không | Mọi con số tính phí PHẢI đối chiếu ngược được về một đối tượng có thật trong CRM (BR-10.8, FEAT-27) |
| **CRM không sở hữu logic tính tiền** | Giá, hạn mức, chu kỳ, thuế, hóa đơn, thu tiền không phải kiến thức của module nghiệp vụ | Đổi mô hình giá hoặc đổi hệ tính phí KHÔNG ĐƯỢC buộc phải sửa module Omnichat, Workflow hay Campaign (BR-02.4, NFR-12) |
| **Lớp sự kiện tính phí là ranh giới duy nhất** | Module nghiệp vụ chỉ phát sinh Sự kiện tính phí; không module nghiệp vụ nào được biết tới sự tồn tại của gói cước, hạn mức hay hóa đơn | Việc kiểm soát chi phí thực thi tại lớp thương mại, không rải rác trong nghiệp vụ (FEAT-16, FEAT-32) |

### 2.3 Vai trò người dùng

| Vai trò | Thuộc về | Quan tâm chính |
| --- | --- | --- |
| **Thành viên** | Doanh nghiệp | Làm việc bình thường; không thấy dữ liệu tài chính, nhưng PHẢI biết khi một thao tác của mình bị chặn vì lý do hạn mức |
| **Quản trị viên tenant** | Doanh nghiệp | Quản lý người dùng — thao tác trực tiếp ảnh hưởng tới phí theo số người dùng (FEAT-11) |
| **Chủ workspace (Owner)** | Doanh nghiệp | Chịu trách nhiệm cuối cùng về quan hệ thương mại: chọn gói, hủy, nhận mọi thông báo về nợ và đình chỉ |
| **Người phụ trách thanh toán** | Doanh nghiệp | Xem hóa đơn, quản lý phương thức thanh toán, theo dõi tiêu dùng, khiếu nại. Có thể không phải người vận hành CRM hằng ngày |
| **Chăm sóc khách hàng nhà cung cấp** | Nhà cung cấp | Trả lời câu hỏi về hóa đơn, tra cứu tình trạng một doanh nghiệp; không tự ý thay đổi tiền |
| **Kế toán nhà cung cấp** | Nhà cung cấp | Phát hành hóa đơn, cấp ghi có, hoàn tiền, ghi nhận thanh toán thủ công, chốt số liệu doanh thu |
| **Quản trị nhà cung cấp** | Nhà cung cấp | Quản lý danh mục gói cước, danh mục loại tiêu dùng, giá thương lượng riêng, chính sách nhắc nợ và đình chỉ |
| **Tác vụ tự động** | Hệ thống | Ghi nhận tiêu dùng, chốt kỳ, thu tiền, nhắc nợ, đình chỉ, khôi phục — luôn để lại vết như một chủ thể có danh tính |

### 2.4 Nhóm tính năng

| Nhóm | Phạm vi | Mã |
| --- | --- | --- |
| **A. Nền tảng thương mại** | Gói cước, danh mục tiêu dùng, hồ sơ thanh toán, vòng đời đăng ký, dùng thử, đổi gói, add-on, chiết khấu | FEAT-01 → FEAT-08 |
| **B. Đo lường tiêu dùng** | Phát sinh, chuyển giao, ghi nhận bù, điều chỉnh và đối soát sự kiện; hạn mức và theo dõi | FEAT-09 → FEAT-17 |
| **C. Hóa đơn & thu tiền** | Lập hóa đơn, vòng đời chứng từ, thuế, thu tiền, nhắc nợ, đình chỉ, ghi có, hủy, đa tiền tệ | FEAT-18 → FEAT-25 (gồm FEAT-18b) |
| **D. Minh bạch, vận hành & tuân thủ** | Cổng tự phục vụ, truy vết và khiếu nại, công cụ nhà cung cấp, nhật ký, báo cáo, thông báo, cách ly ảnh hưởng | FEAT-26 → FEAT-32 |

### 2.5 Bản đồ phụ thuộc & thứ tự triển khai

Mục này không thêm yêu cầu nào. Nó nêu **quan hệ phụ thuộc bắt buộc** giữa các tính năng, để việc xếp thứ tự xây dựng không phụ thuộc trí nhớ của người lập kế hoạch. Một tính năng KHÔNG ĐƯỢC coi là hoàn tất khi tính năng nó phụ thuộc chưa hoàn tất — con số nó tạo ra sẽ đúng trên môi trường thử và sai trên môi trường thật.

| Tính năng | Phụ thuộc bắt buộc | Vì sao là phụ thuộc, không phải liên quan |
| --- | --- | --- |
| FEAT-01 (gói cước) | FEAT-02 | Không có danh mục loại tiêu dùng thì không khai được hạn mức và đơn giá vượt (BR-01.1) |
| FEAT-09 (phát sinh sự kiện) | FEAT-02, FEAT-10 | Không có mã định danh và định nghĩa thời điểm tính phí thì sự kiện không có nghĩa (BR-02.5) |
| FEAT-12 (chuyển giao) | FEAT-09 | Chuyển giao cái chưa được ghi nhận là vô nghĩa |
| FEAT-13 (đến muộn & ranh giới kỳ) | FEAT-12 | Thời hạn chốt kỳ chỉ có tác dụng khi đã có cơ chế tồn đọng và chuyển giao lại |
| FEAT-15 (đối soát) | FEAT-12, FEAT-14 | So khớp phải trừ được bút toán đảo, nếu không mọi lần đảo đều thành một chênh lệch giả |
| FEAT-16 (hạn mức & phí vượt) | FEAT-01, FEAT-04, FEAT-12 | Cần điều kiện gói, trạng thái đăng ký và số liệu tiêu dùng cùng lúc mới ra được quyết định chặn/cho phép |
| FEAT-17 (theo dõi & cảnh báo) | FEAT-16, FEAT-12 | Không có mức tồn đọng thì không thực hiện được BR-17.3 |
| FEAT-18 (lập & phát hành hóa đơn) | FEAT-13, FEAT-15, FEAT-19 | Thời hạn chốt kỳ, cổng chặn đối soát và thủ tục chứng từ đều nằm trước bước phát hành |
| **FEAT-18b (vòng đời hóa đơn)** | FEAT-18, FEAT-19 | Tập trạng thái chỉ định nghĩa được sau khi biết thủ tục bắt buộc của thị trường |
| FEAT-20 (thu tiền) | FEAT-18b | Không có ngày tới hạn và trạng thái chứng từ thì không biết khi nào được phép thu |
| FEAT-21 (nhắc nợ) | FEAT-18b, BR-18.9 | Ngày tới hạn là mốc khởi phát duy nhất (BR-21.9) |
| FEAT-22 (đình chỉ & khôi phục) | FEAT-21 | Đình chỉ là bước cuối của chu trình nhắc nợ |
| FEAT-23 (ghi có & hoàn tiền) | FEAT-14, FEAT-18b | Ghi có luôn tham chiếu tới một hóa đơn đã phát hành và tới sự kiện gốc đã bị đảo |
| FEAT-27 (truy vết & khiếu nại) | FEAT-12 (BR-12.6), FEAT-18b | Không giữ đủ sự kiện gốc thì không đi xuống chi tiết được |
| FEAT-30 (báo cáo) | FEAT-18b, BR-16.8 | Doanh thu ghi theo kỳ dịch vụ cần chứng từ; giá vốn cần con số tự chịu tách riêng |
| FEAT-29 (nhật ký) | — | Không phụ thuộc ai, nhưng mọi tính năng thuộc diện quyết định thương mại phụ thuộc nó (BR-29.2) |

**Quyết định chặn ánh xạ vào tính năng nào** (Mục 7.2): câu 1 → FEAT-10 và FEAT-02; câu 2 → FEAT-02 và FEAT-30; câu 3 và 4 → FEAT-18b, FEAT-19, FEAT-20, FEAT-23; câu 13 → FEAT-04 và FEAT-06. Trước khi mở issue xây dựng cho một tính năng, PHẢI kiểm tra quyết định chặn tương ứng đã có kết luận.

---

## 3. Đặc tả yêu cầu chức năng

## A. NỀN TẢNG THƯƠNG MẠI

### FEAT-01 — Danh mục gói cước & phiên bản gói `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép nhà cung cấp định nghĩa những gì mình bán — mỗi gói cước gồm phí thuê bao cố định theo chu kỳ, tập hạn mức bao gồm theo từng loại tiêu dùng, và đơn giá phần vượt hạn mức. Yêu cầu cốt lõi của mục này không phải là "tạo được gói", mà là **một doanh nghiệp đã mua rồi thì điều kiện thương mại của họ không tự thay đổi dưới chân họ**.

**Actor:** Quản trị nhà cung cấp.

**Luồng chính:**

1. Quản trị nhà cung cấp tạo một gói cước mới, khai báo chu kỳ, tiền tệ, phí thuê bao và số người dùng bao gồm.
2. Với từng loại tiêu dùng lấy từ danh mục (FEAT-02), khai báo hạn mức bao gồm, đơn giá vượt và chính sách chạm trần.
3. Xem trước toàn bộ nội dung gói đúng như doanh nghiệp sẽ nhìn thấy.
4. Phát hành gói. Từ thời điểm này gói bán được cho doanh nghiệp mới.
5. Khi cần thay đổi điều kiện thương mại, tạo **phiên bản mới** của gói thay vì sửa gói đang bán.

**Quy tắc nghiệp vụ:**

- BR-01.1: Mỗi phiên bản gói PHẢI khai báo đầy đủ: chu kỳ tính phí, tiền tệ, phí thuê bao mỗi kỳ, số người dùng bao gồm, và với từng loại tiêu dùng — hạn mức bao gồm, đơn giá vượt, chính sách chạm trần. Không trường nào được để trống ngầm hiểu là "không giới hạn".
- BR-01.2: Một phiên bản gói đã có ít nhất một doanh nghiệp đăng ký KHÔNG ĐƯỢC sửa giá, hạn mức hay chính sách tại chỗ. Mọi thay đổi tạo ra một phiên bản mới; các doanh nghiệp đang dùng giữ nguyên phiên bản họ đã ký cho tới khi họ chủ động đổi gói hoặc tới khi hết hiệu lực cam kết đã thỏa thuận.
- BR-01.3: Thu hồi một gói chỉ chặn việc bán mới. Doanh nghiệp đang dùng gói đó KHÔNG ĐƯỢC tự động bị chuyển sang gói khác, và PHẢI được thông báo trước theo thời hạn đã cam kết nếu nhà cung cấp muốn kết thúc phiên bản đó.
- BR-01.4: Mỗi hạn mức trong gói PHẢI gắn với đúng một chính sách chạm trần theo BR-16.2 — sản phẩm KHÔNG ĐƯỢC có hành vi mặc định ngầm khi hết hạn mức.
- BR-01.5: Doanh nghiệp PHẢI xem lại được toàn văn điều kiện thương mại của phiên bản gói mình đang dùng, đúng như tại thời điểm đăng ký, trong suốt thời gian đăng ký còn hiệu lực và trong thời hạn khiếu nại sau đó.
- BR-01.6: Một hạn mức được quảng cáo là "không giới hạn" PHẢI kèm một ngưỡng sử dụng hợp lý định lượng được và ghi rõ điều gì xảy ra khi vượt ngưỡng đó. Không có ngưỡng thì nhà cung cấp không kiểm soát được chi phí biến đổi và doanh nghiệp cũng không biết mình đang được hứa điều gì.
- BR-01.7: Gói cước PHẢI khai báo được ở nhiều tiền tệ như những phiên bản độc lập, không quy đổi tự động theo tỷ giá — xem BR-25.2.
- BR-01.8: Thay đổi danh mục gói cước KHÔNG ĐƯỢC yêu cầu triển khai lại phần mềm; đây là thao tác cấu hình của người làm thương mại, không phải việc của đội phát triển.

**Tiêu chí chấp nhận:**

- Sửa giá một gói đang có khách không làm thay đổi hóa đơn của bất kỳ doanh nghiệp nào đã đăng ký trước đó.
- Mỗi hạn mức trong mọi gói đang phát hành đều trả lời được câu hỏi "hết hạn mức thì sao" mà không cần hỏi lại người xây dựng.
- Doanh nghiệp đăng ký từ 12 tháng trước vẫn xem lại được đúng điều kiện họ đã ký.

**Tham chiếu:** [ADR-0004](../docs/adr/0004-billing-engine-boundary.md).

---

### FEAT-02 — Danh mục loại tiêu dùng tính phí `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Định nghĩa tập hợp những thứ có thể tính phí theo mức sử dụng, mỗi loại có một mã định danh ổn định. Đây là từ vựng chung giữa nghiệp vụ CRM và lớp thương mại; mã sai một lần là sai vĩnh viễn vì mọi hóa đơn lịch sử đều tham chiếu tới nó.

**Actor:** Quản trị nhà cung cấp.

**Luồng chính:**

1. Quản trị nhà cung cấp khai báo một loại tiêu dùng mới: mã định danh, tên hiển thị cho doanh nghiệp, đơn vị đo, cách gộp trong kỳ.
2. Khai báo định nghĩa nghiệp vụ một câu cho "một đơn vị" và thời điểm tính phí tương ứng (FEAT-10).
3. Khai báo cách doanh nghiệp tự đối chiếu con số này với dữ liệu họ nhìn thấy trong CRM.
4. Phát hành. Từ thời điểm này loại tiêu dùng đưa được vào gói cước.

**Quy tắc nghiệp vụ:**

- BR-02.1: Mỗi loại tiêu dùng PHẢI có một mã định danh duy nhất, đặt theo quy ước `<đối tượng>_<hành động ở thể hoàn thành>`. Mã đã phát hành KHÔNG ĐƯỢC đổi tên, KHÔNG ĐƯỢC tái sử dụng cho mục đích khác, và KHÔNG ĐƯỢC xóa khỏi danh mục kể cả khi ngừng bán.
- BR-02.2: Khi bản chất đo lường của một loại tiêu dùng thay đổi (đổi đơn vị, đổi thời điểm tính phí, đổi cách gộp), PHẢI tạo một mã mới và ngừng bán mã cũ. KHÔNG ĐƯỢC giữ nguyên mã cũ với ý nghĩa mới — làm vậy khiến báo cáo so sánh giữa các kỳ trở thành số liệu sai mà không ai phát hiện được.
- BR-02.3: Mỗi loại tiêu dùng PHẢI khai báo cách gộp trong kỳ, chọn một trong: cộng dồn số lượng, đếm số đối tượng duy nhất, lấy giá trị cao nhất tại bất kỳ thời điểm nào trong kỳ, hoặc lấy giá trị tại thời điểm cuối kỳ.
- BR-02.4: Thêm một loại tiêu dùng mới KHÔNG ĐƯỢC yêu cầu sửa bất kỳ quy tắc nghiệp vụ nào khác trong tài liệu này, và KHÔNG ĐƯỢC yêu cầu sửa module nghiệp vụ nào ngoài chính module phát sinh ra nó. Mọi quy tắc trong tài liệu này viện dẫn **thuộc tính của loại tiêu dùng**, KHÔNG viện dẫn tên một loại cụ thể.
- BR-02.5: Một loại tiêu dùng chỉ được phát hành khi đã trả lời được cả **ba** câu hỏi: **thời điểm tính phí là gì** (FEAT-10), **doanh nghiệp tự kiểm chứng con số này bằng cách nào** (BR-10.8), và **nhà cung cấp bị bên thứ ba tính tiền theo đơn vị nào, chênh lệch với đơn vị bán ra là bao nhiêu, bên nào chịu phần chênh** (BR-10.4). Chưa trả lời được câu nào thì chưa được đưa vào gói bán. Câu thứ ba nằm trong gate này chứ không nằm riêng ở FEAT-10, vì nếu chỉ ghi nó ở chỗ khác thì việc phát hành một loại tiêu dùng có biên lợi nhuận âm sẽ phụ thuộc vào việc ai đó nhớ ra, thay vì bị chính quy trình chặn lại.
- BR-02.6: Danh mục của giai đoạn đầu gồm hai loại: một đơn vị ứng với một hội thoại được tạo, và một đơn vị ứng với một tin nhắn mẫu được gửi đi. Danh mục dự kiến mở rộng — thư điện tử gửi đi, tin nhắn SMS gửi đi, lượt chạy quy trình tự động, tin nhắn chiến dịch gửi đi, token AI tiêu thụ, dung lượng lưu trữ, phút gọi thoại — được liệt kê để định hình mô hình, KHÔNG được coi là cam kết phạm vi của giai đoạn đầu.
- BR-02.7: Tên hiển thị của loại tiêu dùng trên hóa đơn và trên màn hình theo dõi PHẢI dùng ngôn ngữ của người trả tiền, không dùng mã định danh kỹ thuật.

**Tiêu chí chấp nhận:**

- Mọi loại tiêu dùng đang phát hành đều có định nghĩa "một đơn vị là gì" viết được trong một câu mà người không làm kỹ thuật đọc hiểu.
- Thêm một loại tiêu dùng thứ ba vào danh mục không tạo ra thay đổi nào ở FEAT-12 → FEAT-18.
- Không có hai loại tiêu dùng nào đã phát hành mang cùng một mã.

---

### FEAT-03 — Hồ sơ thanh toán của doanh nghiệp `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Mỗi doanh nghiệp dùng CRM có một hồ sơ thanh toán — nơi tập trung thông tin pháp lý để xuất hóa đơn, tiền tệ, múi giờ cắt kỳ và người chịu trách nhiệm về tiền. Hồ sơ này là cầu nối duy nhất giữa danh tính vận hành (workspace) và danh tính thương mại (người mua).

**Actor:** Chủ workspace, Người phụ trách thanh toán, Kế toán nhà cung cấp.

**Luồng chính:**

1. Khi một doanh nghiệp được tạo trong CRM, hệ thống tạo sẵn hồ sơ thanh toán tương ứng ở trạng thái chưa hoàn tất.
2. Chủ workspace bổ sung tên pháp lý, quốc gia, địa chỉ xuất hóa đơn, mã số thuế nếu có, và chỉ định Người phụ trách thanh toán.
3. Hệ thống xác định tiền tệ và múi giờ cắt kỳ theo quốc gia đã khai, cho phép điều chỉnh trước khi đăng ký gói trả phí.
4. Hồ sơ chuyển sang trạng thái đủ điều kiện đăng ký gói trả phí.

**Quy tắc nghiệp vụ:**

- BR-03.1: Một doanh nghiệp có đúng một hồ sơ thanh toán tại một thời điểm, và một hồ sơ thanh toán thuộc đúng một doanh nghiệp. Mô hình gộp nhiều doanh nghiệp vào một hồ sơ (tập đoàn, đại lý bán lại) nằm ngoài phạm vi — xem Mục 7.
- BR-03.2: Doanh nghiệp PHẢI dùng được CRM ở mức dùng thử mà chưa cần hoàn tất hồ sơ thanh toán. Hồ sơ chỉ bắt buộc hoàn tất trước khi chuyển sang trả phí.
- BR-03.3: Tiền tệ và múi giờ cắt kỳ, một khi đăng ký trả phí đã kích hoạt, KHÔNG ĐƯỢC thay đổi trong suốt vòng đời đăng ký đó — đổi được thì mọi hóa đơn lịch sử mất tính so sánh và ranh giới kỳ trở nên tùy tiện.
- BR-03.4: Thông tin xuất hóa đơn PHẢI sửa được bởi doanh nghiệp cho tới trước thời điểm kỳ được chốt. Sau khi hóa đơn đã phát hành, sửa thông tin chỉ áp dụng cho các hóa đơn về sau; hóa đơn cũ điều chỉnh theo FEAT-23.
- BR-03.5: Người phụ trách thanh toán PHẢI chỉ định được và PHẢI có thể là một người khác Chủ workspace, kể cả một người không tham gia vận hành CRM hằng ngày. Nếu chưa chỉ định ai, Chủ workspace mặc nhiên giữ vai trò này — hệ thống KHÔNG ĐƯỢC ở trạng thái không có ai nhận thông báo về tiền.
- BR-03.6: Xóa hoặc vô hiệu hóa người dùng đang giữ vai trò Người phụ trách thanh toán PHẢI buộc chỉ định người thay thế trước khi hoàn tất.
- BR-03.7: Hồ sơ thanh toán và mọi dữ liệu tài chính gắn với nó chỉ hiển thị cho các vai trò được phép theo Mục 5; Thành viên thường KHÔNG ĐƯỢC thấy phí, hóa đơn hay tình trạng nợ của doanh nghiệp mình.

**Tiêu chí chấp nhận:**

- Một doanh nghiệp mới tạo dùng được ngay các tính năng ở mức dùng thử mà không phải nhập thông tin thanh toán.
- Không tồn tại doanh nghiệp trả phí nào mà không có người nhận thông báo về nợ.
- Đổi quốc gia trong hồ sơ sau khi đã trả phí không làm thay đổi tiền tệ của các kỳ đang chạy.

---

### FEAT-04 — Vòng đời đăng ký gói `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Quản lý trạng thái quan hệ thương mại của một doanh nghiệp theo thời gian, để mọi phần còn lại của hệ thống — kiểm tra hạn mức, chặn thao tác, lập hóa đơn, thu tiền — đều đọc từ một nguồn duy nhất.

**Actor:** Chủ workspace, Kế toán nhà cung cấp, Tác vụ tự động.

**Luồng chính:**

1. Doanh nghiệp chọn một phiên bản gói và xác nhận đăng ký.
2. Hệ thống kiểm tra điều kiện: hồ sơ thanh toán hoàn tất, có phương thức thanh toán hợp lệ (hoặc thỏa thuận trả sau).
3. Đăng ký kích hoạt; ngày kích hoạt trở thành mốc cố định cắt chu kỳ cho mọi kỳ về sau.
4. Cuối mỗi chu kỳ, hệ thống chốt kỳ, lập hóa đơn (FEAT-18) và thu tiền (FEAT-20), rồi mở chu kỳ tiếp theo.

**Quy tắc nghiệp vụ:**

- BR-04.1: Một đăng ký PHẢI ở đúng một trạng thái tại một thời điểm, và tập trạng thái PHẢI đủ để phân biệt năm tình huống khác nhau về mặt nghiệp vụ: **đang dùng thử** (chưa phát sinh nghĩa vụ trả tiền), **đã hết dùng thử chưa chuyển đổi** (còn sống nhưng bị hạn chế theo BR-05.4, chưa từng phát sinh nghĩa vụ trả tiền), **đang hiệu lực** (bình thường), **đang nợ quá hạn** (còn dùng được, đang trong chu trình nhắc nợ), **đang bị đình chỉ** (bị hạn chế theo FEAT-22). Trạng thái kết thúc gồm **đã hủy** và **đã hết hạn**.
- BR-04.1b: Mức giá KHÔNG ĐƯỢC biểu diễn thành một trạng thái vòng đời. Một gói miễn phí vĩnh viễn là một phiên bản gói có phí thuê bao bằng không kèm hạn mức riêng (FEAT-01), đăng ký của nó ở trạng thái **đang hiệu lực** như mọi đăng ký khác, và nó vẫn được phát hành hóa đơn tổng bằng không theo BR-18.7. Trộn hai trục này lại thì mỗi lần thêm một mức giá lại phải thêm một trạng thái, và mọi quy tắc viện dẫn trạng thái đều phải viết lại.
- BR-04.2: Mốc cắt chu kỳ cố định theo ngày kích hoạt và KHÔNG ĐƯỢC trôi khi doanh nghiệp đổi gói, bị đình chỉ rồi khôi phục, hoặc thanh toán muộn. Ngày cắt kỳ trôi là nguyên nhân phổ biến nhất khiến doanh nghiệp bị tính phí hai lần trong một tháng dương lịch.
- BR-04.3: Khi ngày kích hoạt không tồn tại trong một tháng nào đó (ví dụ ngày 31), kỳ đó cắt vào ngày cuối cùng của tháng, và mốc gốc KHÔNG ĐƯỢC thay đổi theo.
- BR-04.4: Chuyển trạng thái đăng ký PHẢI luôn có nguyên nhân ghi nhận được — do doanh nghiệp thao tác, do tác vụ tự động theo chính sách, hay do nhân sự nhà cung cấp can thiệp có lý do (FEAT-29).
- BR-04.5: Trạng thái đăng ký PHẢI đọc được ngay bên trong CRM khi cần ra quyết định chặn hay cho phép một thao tác, KHÔNG ĐƯỢC phụ thuộc vào việc hỏi một hệ thống bên ngoài tại thời điểm đó — xem BR-32.4.
- BR-04.6: Một doanh nghiệp có đúng một đăng ký gói chính tại một thời điểm. Các khoản mua thêm (add-on, gói hạn mức bổ sung) gắn vào đăng ký đó, không tạo thành đăng ký song song — xem FEAT-07.
- BR-04.7: Ngừng cung cấp dịch vụ do hết hạn hay do hủy KHÔNG ĐƯỢC xóa dữ liệu của doanh nghiệp — xem BR-22.1 và BR-24.3.

**Tiêu chí chấp nhận:**

- Một doanh nghiệp đăng ký ngày 31/01 bị tính phí đúng một lần trong tháng 02 và mốc cắt kỳ vẫn là 31 ở những tháng có ngày 31.
- Đình chỉ 10 ngày rồi khôi phục không làm ngày cắt kỳ dịch chuyển.
- Ở bất kỳ thời điểm nào, tra một doanh nghiệp đều trả lời được ngay "họ đang ở trạng thái nào, vì sao, từ khi nào".

---

### FEAT-05 — Dùng thử & chuyển đổi sang trả phí `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép một doanh nghiệp mới dùng sản phẩm trong một khoảng thời gian giới hạn trước khi phải trả tiền, và chuyển đổi sang trả phí một cách không gây bất ngờ.

**Actor:** Chủ workspace, Tác vụ tự động.

**Luồng chính:**

1. Doanh nghiệp mới được tạo và tự động vào trạng thái dùng thử với hạn mức của gói dùng thử.
2. Hệ thống thông báo trước khi hết hạn dùng thử theo lịch đã cấu hình.
3. Doanh nghiệp chọn gói trả phí và hoàn tất hồ sơ thanh toán, hoặc không làm gì.
4. Hết hạn: nếu đã chọn gói, đăng ký trả phí kích hoạt; nếu chưa, doanh nghiệp chuyển sang trạng thái hạn chế theo BR-05.4.

**Quy tắc nghiệp vụ:**

- BR-05.1: Thời hạn và hạn mức của giai đoạn dùng thử là tham số cấu hình, đặt được khác nhau cho từng chiến dịch bán hàng, và PHẢI hiển thị rõ cho doanh nghiệp ngay từ đầu.
- BR-05.2: Tiêu dùng phát sinh trong giai đoạn dùng thử vẫn PHẢI được ghi nhận đầy đủ như tiêu dùng có tính phí, chỉ khác là không tạo ra nghĩa vụ trả tiền. Không ghi nhận thì không đo được mức sử dụng thật của nhóm dùng thử, và không so sánh được với hành vi sau khi trả tiền.
- BR-05.3: Hệ thống PHẢI thông báo trước khi hết hạn dùng thử ít nhất hai lần, và lần cuối cùng PHẢI nêu rõ ngày hết hạn cùng điều gì sẽ xảy ra sau đó.
- BR-05.4: Hết hạn dùng thử mà không chuyển đổi thì đăng ký chuyển sang trạng thái **đã hết dùng thử chưa chuyển đổi** (BR-04.1) — một trạng thái còn sống, KHÔNG phải trạng thái kết thúc. Doanh nghiệp KHÔNG ĐƯỢC mất dữ liệu và PHẢI vẫn truy cập được để xuất dữ liệu của mình. Các hành vi phát sinh chi phí mới bị chặn theo đúng danh sách của BR-22.2.
- BR-05.4b: Việc tiếp nhận hoạt động do khách hàng của doanh nghiệp khởi xướng vẫn PHẢI tiếp tục theo BR-22.3 trong một thời hạn ân hạn đã công bố trước. Hết thời hạn đó, các kênh kết nối PHẢI được **ngắt có thông báo trước** cho doanh nghiệp, đủ sớm để họ chuyển hướng khách hàng của mình. Đây là điểm duy nhất trong tài liệu được phép dừng chiều tiếp nhận, vì tại đây không có quan hệ trả tiền nào để bảo vệ và cũng không có nguồn thu nào bù cho chi phí mà nhà cung cấp thực trả cho nền tảng kênh. Ngắt kết nối có báo trước khác về bản chất với việc âm thầm nuốt tin nhắn — cách thứ hai bị cấm ở mọi trạng thái. Độ dài thời hạn ân hạn: xem Mục 7.2.
- BR-05.5: Chuyển đổi sang trả phí trước khi hết hạn dùng thử KHÔNG ĐƯỢC làm mất phần thời gian dùng thử còn lại — chu kỳ trả phí đầu tiên bắt đầu khi giai đoạn dùng thử kết thúc, trừ khi doanh nghiệp chủ động chọn bắt đầu ngay.
- BR-05.6: Nếu chính sách yêu cầu khai báo phương thức thanh toán ngay từ khi bắt đầu dùng thử, hệ thống PHẢI nêu rõ ngày sẽ trừ tiền lần đầu tiên trước khi doanh nghiệp xác nhận. Khoản trừ tiền đầu tiên KHÔNG ĐƯỢC là một bất ngờ.
- BR-05.7: Một doanh nghiệp KHÔNG ĐƯỢC dùng thử lại nhiều lần liên tiếp bằng cách tạo workspace mới với cùng thông tin liên hệ; hệ thống PHẢI phát hiện được và chuyển sang xử lý thủ công thay vì tự động cấp thêm.

**Tiêu chí chấp nhận:**

- Không có doanh nghiệp nào bị trừ tiền lần đầu mà trước đó không nhận được thông báo nêu rõ ngày và số tiền.
- Doanh nghiệp hết hạn dùng thử không chuyển đổi vẫn tải được dữ liệu của mình.
- Số liệu tiêu dùng của nhóm dùng thử so sánh trực tiếp được với nhóm trả phí trên cùng một thước đo.

---

### FEAT-06 — Nâng gói, hạ gói & tính phí phần lẻ giữa kỳ `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép doanh nghiệp đổi gói giữa chu kỳ mà không phải chờ hết kỳ, với nguyên tắc xử lý tiền rõ ràng và nhất quán ở cả hai chiều.

**Actor:** Chủ workspace, Người phụ trách thanh toán.

**Luồng chính:**

1. Doanh nghiệp chọn phiên bản gói mới.
2. Hệ thống hiển thị **trước khi xác nhận**: số tiền phải trả thêm ngay (nếu có), ngày hiệu lực, hạn mức mới, và điều gì xảy ra với phần tiêu dùng đã phát sinh trong kỳ.
3. Doanh nghiệp xác nhận.
4. Hệ thống áp dụng theo đúng thời điểm hiệu lực của chiều nâng hoặc chiều hạ.

**Quy tắc nghiệp vụ:**

- BR-06.1: **Nâng gói có hiệu lực ngay.** Phần chênh lệch phí thuê bao được tính theo số ngày còn lại của kỳ và thu ngay hoặc cộng vào hóa đơn kỳ hiện tại.
- BR-06.2: **Hạ gói có hiệu lực từ đầu kỳ kế tiếp.** Doanh nghiệp giữ nguyên hạn mức và quyền lợi của gói cũ tới hết kỳ đã trả tiền; KHÔNG hoàn tiền phần chênh lệch. Cho hạ gói có hiệu lực ngay kèm hoàn tiền tạo ra kẽ hở nâng-rồi-hạ trong ngày để hưởng hạn mức mà không trả đủ.
- BR-06.3: Đổi gói KHÔNG ĐƯỢC đặt lại mốc cắt chu kỳ (BR-04.2) và KHÔNG ĐƯỢC xóa lượng tiêu dùng đã ghi nhận trong kỳ.
- BR-06.4: Khi nâng gói giữa kỳ, hạn mức bao gồm của kỳ hiện tại được tính theo tỷ lệ thời gian của từng gói trong kỳ, không cấp trọn hạn mức của gói mới cho cả kỳ. Quy tắc này PHẢI được nêu rõ ở bước xem trước.
- BR-06.5: Nếu lượng tiêu dùng đã phát sinh trong kỳ vượt hạn mức của gói sắp hạ xuống, hệ thống PHẢI cảnh báo trước khi xác nhận và nêu rõ phần vượt sẽ bị tính phí ở kỳ nào.
- BR-06.6: Quy tắc này áp cho mọi **tài nguyên tĩnh có trần** — số người dùng đang hoạt động hôm nay, và mọi tài nguyên cùng bản chất được đưa vào danh mục về sau (dung lượng lưu trữ, số kênh kết nối). Hạ gói xuống mức có trần thấp hơn lượng đang chiếm dụng PHẢI bị chặn cho tới khi doanh nghiệp tự giảm xuống dưới trần, hoặc PHẢI nêu rõ phần vượt sẽ bị tính phí — KHÔNG ĐƯỢC tự động vô hiệu hóa người dùng, xóa dữ liệu hay ngắt kết nối để cho vừa gói. Quy tắc viết theo thuộc tính chứ không theo tên một tài nguyên cụ thể là yêu cầu của BR-02.4.
- BR-06.6b: Điều kiện của BR-06.6 PHẢI được kiểm lại tại mốc cắt kỳ, không chỉ tại lúc doanh nghiệp bấm hạ gói. Nếu tới mốc cắt kỳ mà điều kiện chưa thỏa, thao tác hạ gói KHÔNG có hiệu lực, đăng ký giữ nguyên phiên bản gói cũ, và doanh nghiệp PHẢI được thông báo trước mốc đó. Để thao tác hạ gói có hiệu lực rồi cưỡng chế tài nguyên cho vừa là cách hệ thống tự ra một quyết định mà đáng lẽ doanh nghiệp phải ra.
- BR-06.7: Mỗi lần đổi gói PHẢI lưu lại phiên bản gói trước, phiên bản gói sau, thời điểm hiệu lực và số tiền phát sinh (FEAT-29).
- BR-06.8: Đổi gói CHỈ thực hiện được giữa các phiên bản gói **cùng tiền tệ với hồ sơ thanh toán đang áp dụng**. Danh sách gói hiển thị ở bước chọn PHẢI đã lọc theo tiền tệ đó. Đổi tiền tệ không phải một thao tác đổi gói: nó đòi hỏi kết thúc đăng ký hiện tại và lập đăng ký mới theo BR-25.1, vì tiền tệ cố định trong suốt vòng đời một đăng ký (BR-03.3). Không nêu rõ điều này thì màn hình chọn gói sẽ bày ra những phiên bản gói mà chọn vào là vi phạm một quy tắc ở tài liệu khác.

**Tiêu chí chấp nhận:**

- Số tiền hiển thị ở bước xem trước bằng đúng số tiền xuất hiện trên hóa đơn sau đó.
- Nâng rồi hạ trong cùng một ngày không tạo ra lợi ích tài chính cho doanh nghiệp.
- Không thao tác đổi gói nào làm một người dùng đang hoạt động bị vô hiệu hóa mà không có ai quyết định.
- Một thao tác hạ gói chưa thỏa điều kiện tài nguyên tĩnh không bao giờ có hiệu lực âm thầm ở ngày cắt kỳ; doanh nghiệp luôn biết trước là nó sẽ không có hiệu lực.

---

### FEAT-07 — Add-on & mua thêm hạn mức `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép doanh nghiệp mua thêm quyền lợi ngoài gói mà không phải nâng cả gói — thêm chỗ người dùng, thêm hạn mức tiêu dùng cho một kỳ, hoặc bật một tính năng tính phí riêng.

**Actor:** Chủ workspace, Người phụ trách thanh toán.

**Quy tắc nghiệp vụ:**

- BR-07.1: Hai hình thái add-on PHẢI phân biệt rõ và hiển thị khác nhau cho doanh nghiệp: **định kỳ** (lặp lại mỗi kỳ tới khi hủy) và **một lần** (chỉ áp dụng cho kỳ đang chạy).
- BR-07.2: Add-on mua thêm hạn mức có hiệu lực ngay và cộng vào hạn mức của kỳ hiện tại; phần hạn mức mua thêm chưa dùng hết KHÔNG cộng dồn sang kỳ sau trừ khi điều kiện add-on ghi rõ.
- BR-07.3: Add-on KHÔNG ĐƯỢC tạo chu kỳ tính phí riêng — mọi khoản đều rơi vào hóa đơn của chu kỳ chính (BR-04.6).
- BR-07.4: Hủy một add-on định kỳ có hiệu lực từ đầu kỳ kế tiếp, theo cùng nguyên tắc hạ gói ở BR-06.2.
- BR-07.5: Mỗi add-on PHẢI xuất hiện như một dòng riêng trên hóa đơn, không gộp vào phí thuê bao.

**Tiêu chí chấp nhận:**

- Doanh nghiệp chạm trần hạn mức giữa kỳ mua thêm được hạn mức và dùng tiếp trong vòng vài phút, không cần liên hệ nhà cung cấp.
- Hóa đơn tách rõ phần gói và từng add-on.

---

### FEAT-08 — Chiết khấu, khuyến mãi & giá thương lượng riêng `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép đội kinh doanh áp dụng điều kiện thương mại khác giá niêm yết cho một doanh nghiệp cụ thể, mà vẫn giữ được tính kiểm soát và truy vết.

**Actor:** Quản trị nhà cung cấp, Kế toán nhà cung cấp.

**Quy tắc nghiệp vụ:**

- BR-08.1: Ba hình thái ưu đãi PHẢI phân biệt: **chiết khấu theo tỷ lệ phần trăm**, **giảm trừ số tiền cố định**, và **giá thương lượng riêng** (thay hẳn bảng giá cho một doanh nghiệp).
- BR-08.2: Mỗi ưu đãi PHẢI khai báo phạm vi áp dụng (áp cho phí thuê bao, cho phí vượt, hay cho cả hai) và thời hạn hiệu lực. Ưu đãi không có thời hạn PHẢI là một lựa chọn có chủ đích, không phải giá trị mặc định khi bỏ trống.
- BR-08.3: Giá thương lượng riêng PHẢI biểu diễn dưới dạng một phiên bản gói riêng có ngày hiệu lực, KHÔNG ĐƯỢC sửa đè lên gói niêm yết chung — xem BR-01.2 và BR-28.4.
- BR-08.4: Ưu đãi vượt ngưỡng giá trị đã cấu hình PHẢI có người phê duyệt khác người đề nghị.
- BR-08.5: Mọi ưu đãi PHẢI hiển thị thành một dòng riêng trên hóa đơn, ghi rõ căn cứ áp dụng. Một doanh nghiệp KHÔNG ĐƯỢC nhìn thấy tổng tiền thấp hơn giá niêm yết mà không biết vì sao.
- BR-08.6: Khi một ưu đãi có thời hạn sắp hết, hệ thống PHẢI thông báo cho doanh nghiệp trước khi hóa đơn đầu tiên không còn ưu đãi được phát hành.
- BR-08.7: Ưu đãi hết hạn KHÔNG ĐƯỢC tự động gia hạn.
- BR-08.8: Khi một doanh nghiệp thỏa mãn nhiều ưu đãi cùng lúc trên cùng một phạm vi áp dụng (BR-08.2), mặc định là **loại trừ lẫn nhau**: chỉ ưu đãi có lợi nhất cho doanh nghiệp được áp, các ưu đãi còn lại vẫn hiển thị nhưng ghi rõ là không áp dụng cùng lúc. Việc cho phép cộng dồn PHẢI là một khai báo có chủ đích trên từng ưu đãi, không phải hành vi mặc định. Khi đã khai báo cộng dồn, trình tự áp là cố định: **giảm trừ số tiền cố định trước, chiết khấu theo tỷ lệ phần trăm sau, tính trên số dư còn lại**. KHÔNG ĐƯỢC cộng các tỷ lệ phần trăm lại với nhau rồi áp một lần — hai cách cho ra hai con số khác nhau và cách cộng tỷ lệ không giải thích được cho người trả tiền.
- BR-08.9: Giá trị dùng để so với ngưỡng phê duyệt ở BR-08.4 là **giá trị sau khi đã phân giải theo BR-08.8**, không phải tổng danh nghĩa của các ưu đãi được đề nghị. Không cố định điểm này thì cùng một hồ sơ có thể vừa cần vừa không cần phê duyệt tùy cách người ta cộng.

**Tiêu chí chấp nhận:**

- Một doanh nghiệp có giá riêng vẫn nhận đúng cơ chế hóa đơn, hạn mức và nhắc nợ như mọi doanh nghiệp khác.
- Không có hóa đơn nào chứa khoản giảm trừ không giải thích được căn cứ.
- Tra được đầy đủ danh sách doanh nghiệp đang hưởng ưu đãi, ai duyệt, hết hạn khi nào.

---
## B. ĐO LƯỜNG TIÊU DÙNG

### FEAT-09 — Phát sinh Sự kiện tính phí từ nghiệp vụ CRM `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Mỗi khi một việc đáng tính phí xảy ra trong một module nghiệp vụ, CRM ghi lại một Sự kiện tính phí. Đây là ranh giới duy nhất giữa nghiệp vụ và thương mại: module nghiệp vụ tuyên bố "việc này vừa xảy ra, thuộc doanh nghiệp này, ứng với đối tượng này", và dừng lại ở đó — nó không biết gì về giá, hạn mức hay hóa đơn.

**Actor:** Tác vụ tự động (thay mặt module nghiệp vụ).

**Luồng chính:**

1. Một việc đáng tính phí xảy ra và đạt tới thời điểm tính phí đã định nghĩa (FEAT-10).
2. Hệ thống ghi một Sự kiện tính phí với: doanh nghiệp, loại tiêu dùng, mã tham chiếu tới đối tượng gốc, số lượng, thời điểm phát sinh nghiệp vụ.
3. Sự kiện được lưu lại tại CRM ở trạng thái chờ chuyển giao.
4. Việc chuyển giao sang lớp thương mại diễn ra tách rời (FEAT-12), không nằm trong luồng phục vụ người dùng.

**Quy tắc nghiệp vụ:**

- BR-09.1: Mỗi Sự kiện tính phí PHẢI ghi đủ, không trường nào suy diễn được về sau: doanh nghiệp, loại tiêu dùng, mã tham chiếu tới đối tượng gốc, số lượng, **thời điểm phát sinh nghiệp vụ**, thời điểm hệ thống ghi nhận, và trạng thái chuyển giao.
- BR-09.2: **Thời điểm phát sinh nghiệp vụ** và **thời điểm ghi nhận** là hai mốc khác nhau và PHẢI lưu riêng. Việc quy sự kiện về kỳ nào luôn dùng mốc thứ nhất (BR-13.1); việc phát hiện tồn đọng dùng khoảng cách giữa hai mốc.
- BR-09.3: Việc ghi Sự kiện tính phí KHÔNG ĐƯỢC làm chậm hay làm hỏng nghiệp vụ đang phục vụ khách hàng của doanh nghiệp — xem BR-32.1.
- BR-09.4: Ngược lại, nếu không ghi được sự kiện, hệ thống KHÔNG ĐƯỢC im lặng bỏ qua. Tình huống "không ghi được" PHẢI trở thành "ghi bù sau" kèm cảnh báo, KHÔNG ĐƯỢC trở thành "miễn phí" — xem BR-32.2.
- BR-09.5: Sự kiện tính phí là bản ghi **chỉ thêm mới**. Sau khi ghi, nội dung của nó KHÔNG ĐƯỢC sửa và KHÔNG ĐƯỢC xóa; hủy hiệu lực thực hiện bằng bút toán đảo theo FEAT-14.
- BR-09.6: Sự kiện phát sinh từ môi trường thử nghiệm, tài khoản trình diễn hoặc tài khoản nội bộ của nhà cung cấp PHẢI phân biệt được và KHÔNG ĐƯỢC lẫn vào số liệu tính phí lẫn số liệu doanh thu (BR-30.5).
- BR-09.7: Sự kiện luôn thuộc về đúng một doanh nghiệp. Một sự kiện không xác định được doanh nghiệp PHẢI bị giữ lại để xử lý thủ công, KHÔNG ĐƯỢC gán cho một doanh nghiệp mặc định nào.

**Tiêu chí chấp nhận:**

- Không thao tác nghiệp vụ nào của người dùng cuối chậm đi hay thất bại vì việc ghi nhận tính phí.
- Với mỗi đối tượng nghiệp vụ đáng tính phí trong CRM, tra ngược được đã có sự kiện tương ứng hay chưa và đang ở trạng thái nào.

**Tham chiếu:** [ADR-0004](../docs/adr/0004-billing-engine-boundary.md).

---

### FEAT-10 — Thời điểm tính phí của từng loại tiêu dùng `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Định nghĩa chính xác khoảnh khắc một việc trở thành đáng tính phí, cho từng loại tiêu dùng. Đây là mục có ảnh hưởng tài chính lớn nhất trong tài liệu: định nghĩa lệch một chút là chênh lệch nhân lên theo số lượng, và mọi tranh chấp hóa đơn cuối cùng đều quy về mục này.

**Actor:** Quản trị nhà cung cấp (định nghĩa), Tác vụ tự động (thực thi).

**Quy tắc nghiệp vụ:**

- BR-10.1: Mỗi loại tiêu dùng PHẢI có đúng một định nghĩa thời điểm tính phí, viết bằng ngôn ngữ nghiệp vụ và kiểm chứng được từ bên ngoài bằng dữ liệu doanh nghiệp nhìn thấy được. Định nghĩa PHẢI được công bố cho doanh nghiệp, không phải kiến thức nội bộ.
- BR-10.2: **Một hội thoại** được tính đúng một đơn vị tại thời điểm hội thoại được tạo trong hệ thống. Hội thoại được mở lại trong thời hạn tái mở đã cấu hình KHÔNG phát sinh đơn vị mới; mở lại sau thời hạn đó được coi là một hội thoại mới. Đơn vị "một hội thoại" hiện gắn với một phiên trên một kênh — định nghĩa này phụ thuộc vào một quyết định còn treo ở Omnichat, xem Mục 7.2.
- BR-10.3: **Một tin nhắn mẫu** được tính đúng một đơn vị tại thời điểm nền tảng kênh xác nhận đã tiếp nhận để gửi. KHÔNG tính tại thời điểm Agent bấm gửi, và KHÔNG tính khi nền tảng từ chối tiếp nhận. Tin nhắn đã tiếp nhận nhưng sau đó nền tảng báo không gửi được tới người nhận được xử lý theo BR-14.3.
- BR-10.4: Đơn vị tính phí đối với doanh nghiệp PHẢI cùng đơn vị mà nhà cung cấp bị bên thứ ba tính tiền. Nếu hai đơn vị khác nhau — ví dụ nền tảng kênh tính theo cửa sổ hội thoại còn hệ thống đếm theo từng tin nhắn gửi đi — thì chênh lệch đó PHẢI được nêu rõ trong định nghĩa loại tiêu dùng và PHẢI xác định bên nào chịu phần chênh. KHÔNG ĐƯỢC để chênh lệch này tồn tại ngầm: nó là nguồn lỗ âm thầm hoặc nguồn thu oan, tùy chiều lệch.
- BR-10.5: Hoạt động do doanh nghiệp bị động tiếp nhận, không do doanh nghiệp chủ động tạo ra, mà bị đánh dấu là lạm dụng — hội thoại bị đánh dấu spam, hội thoại từ người gửi bị chặn — KHÔNG ĐƯỢC tính phí. Nếu đã ghi nhận trước khi bị đánh dấu, sự kiện PHẢI bị đảo theo FEAT-14.
- BR-10.6: Tiêu dùng phát sinh do lỗi của chính hệ thống nhà cung cấp — vòng lặp tự động hóa do lỗi, gửi lặp do sự cố, tiêu dùng sinh ra trong lúc khắc phục sự cố — KHÔNG ĐƯỢC tính phí cho doanh nghiệp.
- BR-10.7: Tiêu dùng phát sinh từ thao tác của nhân sự nhà cung cấp khi hỗ trợ một doanh nghiệp PHẢI phân biệt được với tiêu dùng do chính doanh nghiệp tạo ra, và chính sách tính phí cho nhóm này PHẢI được quyết định rõ thay vì để mặc định.
- BR-10.8: Với mọi loại tiêu dùng, doanh nghiệp PHẢI tự đối chiếu được con số bị tính với danh sách đối tượng gốc trong CRM của mình — cùng khoảng thời gian, cùng bộ lọc, ra cùng con số. Một loại tiêu dùng không thỏa mãn điều kiện này KHÔNG ĐƯỢC phát hành (BR-02.5).
- BR-10.9: Thay đổi định nghĩa thời điểm tính phí của một loại đang bán KHÔNG ĐƯỢC áp dụng hồi tố và PHẢI thông báo trước cho doanh nghiệp; nếu thay đổi làm đổi bản chất đo lường thì phải tạo mã mới theo BR-02.2.

**Tiêu chí chấp nhận:**

- Một doanh nghiệp lọc danh sách hội thoại của mình trong kỳ ra đúng con số xuất hiện trên hóa đơn.
- Tin nhắn mẫu bị nền tảng kênh từ chối không xuất hiện trên hóa đơn.
- Với mỗi loại tiêu dùng đang bán, trả lời được ngay "nhà cung cấp bị tính tiền theo đơn vị nào" và "chênh lệch với đơn vị bán ra là bao nhiêu".

**Tham chiếu:** BR-10.2 phụ thuộc [`omnichat-srs.md`](./omnichat-srs.md) Mục 7.2 câu 1 → issue [#90](https://github.com/crmsaassaudi/product-management/issues/90).

---

### FEAT-11 — Tính phí theo số người dùng `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Xác định số người dùng bị tính phí của một doanh nghiệp trong một kỳ, và điều gì xảy ra khi vượt số người dùng bao gồm trong gói.

**Actor:** Quản trị viên tenant, Tác vụ tự động.

**Luồng chính:**

1. Khi một kỳ mở, hệ thống ghi nhận **mốc đầu kỳ**: số người dùng đang ở trạng thái hoạt động tại thời điểm kỳ bắt đầu (BR-11.8b).
2. Trong kỳ, mỗi lần một người dùng chuyển sang trạng thái hoạt động hoặc rời khỏi trạng thái hoạt động, hệ thống ghi một Sự kiện tính phí tương ứng (BR-11.8).
3. Khi Quản trị viên tenant thêm người dùng vượt số người dùng bao gồm trong gói, hệ thống thực thi chính sách chạm trần đã khai của gói và nêu rõ lý do (BR-11.6, BR-11.7).
4. Cuối kỳ, số người dùng bị tính phí của kỳ được suy ra từ mốc đầu kỳ cộng chuỗi sự kiện trong kỳ, theo cách gộp đã khai của loại tiêu dùng (BR-11.2).

**Quy tắc nghiệp vụ:**

- BR-11.1: **Người dùng bị tính phí** là người dùng ở trạng thái hoạt động trong workspace, bất kể có đăng nhập trong kỳ hay không. Việc có dùng hay không không đổi được nghĩa vụ trả tiền — chỗ ngồi đã được giữ.
- BR-11.2: Số người dùng của một kỳ tính theo **mức cao nhất tại bất kỳ thời điểm nào trong kỳ**, không tính theo số tại thời điểm cuối kỳ. Chọn cách này vì hai lý do: doanh nghiệp tự kiểm chứng được bằng danh sách người dùng của mình, và nó không tạo kẽ hở bật/tắt người dùng quanh ngày chốt kỳ. Đánh đổi được chấp nhận là một người dùng thêm vào vài ngày cuối kỳ vẫn tính trọn kỳ — xem Mục 7.2.
- BR-11.3: Vô hiệu hóa một người dùng giữa kỳ KHÔNG hoàn tiền phần còn lại của kỳ, và có hiệu lực giảm phí từ kỳ kế tiếp.
- BR-11.4: Vô hiệu hóa người dùng KHÔNG ĐƯỢC xóa dữ liệu người đó đã tạo, và KHÔNG ĐƯỢC làm mất lịch sử gắn với họ.
- BR-11.5: Tài khoản kỹ thuật — tài khoản dành cho tích hợp, cho tác vụ tự động, cho bot — KHÔNG tính là người dùng bị tính phí, nhưng PHẢI khai báo rõ là tài khoản kỹ thuật, PHẢI bị giới hạn số lượng, và PHẢI không đăng nhập được bằng giao diện người dùng thông thường. Không có ba ràng buộc này thì mọi doanh nghiệp đều có thể gắn nhãn kỹ thuật cho người thật.
- BR-11.6: Khi vượt số người dùng bao gồm trong gói, hệ thống PHẢI thực thi đúng chính sách chạm trần đã khai báo của gói (BR-16.2) — hoặc chặn thêm người dùng mới, hoặc cho thêm và tính phí phần vượt. KHÔNG ĐƯỢC âm thầm cho thêm mà không tính phí, cũng KHÔNG ĐƯỢC âm thầm chặn mà không nói rõ lý do.
- BR-11.7: Khi chính sách là chặn, thông báo hiển thị cho Quản trị viên tenant PHẢI nêu rõ đang chạm giới hạn nào, giới hạn là bao nhiêu, và cách xử lý (nâng gói, mua thêm chỗ, vô hiệu hóa người khác).
- BR-11.8: Số người dùng bị tính phí là một đại lượng dẫn xuất theo kỳ, nhưng nó vẫn PHẢI đi qua đúng ranh giới của FEAT-09 như mọi loại tiêu dùng khác. Đối tượng gốc ở đây là **từng lần trạng thái hoạt động của một người dùng thay đổi, theo cả hai chiều** — chuyển sang hoạt động và rời khỏi trạng thái hoạt động — mỗi lần mang một mã tham chiếu riêng theo BR-12.1. Ghi nhận một chiều là không đủ: thiếu chiều rời đi thì không dựng lại được số ghế đang chiếm dụng tại từng thời điểm, và một người bị bật rồi tắt rồi bật lại sẽ bị đếm thành nhiều người. KHÔNG ĐƯỢC thay chuỗi sự kiện này bằng một con số tổng chụp tại một thời điểm bất kỳ: con số tổng không tái lập được (NFR-8), không đối soát ngược về đối tượng có thật được (FEAT-15), và doanh nghiệp không tự đối chiếu được với danh sách người dùng của mình (BR-10.8).
- BR-11.8b: Một người dùng đã ở trạng thái hoạt động **từ trước khi kỳ bắt đầu** không tạo ra thay đổi trạng thái nào trong kỳ, nên chuỗi sự kiện của riêng kỳ đó không mô tả được số ghế đang chiếm dụng. Vì vậy mỗi kỳ PHẢI mở bằng một **mốc đầu kỳ** ghi số người dùng đang hoạt động tại thời điểm kỳ bắt đầu. Mốc này KHÔNG phải con số tổng bị cấm ở BR-11.8, với đúng một điều kiện bắt buộc: nó PHẢI dựng lại được từ chính chuỗi thay đổi trạng thái của các kỳ trước, và khi dựng lại PHẢI ra đúng giá trị đã ghi. Một mốc đầu kỳ lấy bằng cách đếm trực tiếp danh sách người dùng tại thời điểm đó — không đối chiếu được với chuỗi — là con số tổng bị cấm, chỉ đổi tên.
- BR-11.9: Số người dùng bị tính phí của **bất kỳ kỳ nào trong quá khứ** PHẢI dựng lại được từ mốc đầu kỳ cộng chuỗi thay đổi trạng thái của kỳ đó, ra đúng con số đã xuất hiện trên hóa đơn (NFR-8). Nếu hai cách tính cho ra hai kết quả khác nhau thì chuỗi sự kiện là căn cứ, và chênh lệch PHẢI được xử lý như một chênh lệch đối soát theo FEAT-15 chứ KHÔNG ĐƯỢC bỏ qua — đây là loại tiêu dùng duy nhất mà con số bán ra là đại lượng dẫn xuất, nên nó cũng là chỗ dễ lệch âm thầm nhất.

**Tiêu chí chấp nhận:**

- Doanh nghiệp đếm số người dùng hoạt động của mình ra đúng con số trên hóa đơn.
- Bật một người dùng vào ngày 5 rồi tắt vào ngày 6 vẫn được tính vào mức cao nhất của kỳ.
- Không tồn tại doanh nghiệp nào vượt số người dùng bao gồm mà không có phí vượt tương ứng hoặc không có chặn tương ứng.
- Con số người dùng bị tính phí của một kỳ trong quá khứ dựng lại được từ mốc đầu kỳ cộng chuỗi thay đổi trạng thái người dùng, không phụ thuộc vào một ảnh chụp nào.
- Một doanh nghiệp không thay đổi người dùng nào trong suốt một kỳ vẫn bị tính đúng số ghế của kỳ đó, dù kỳ đó không phát sinh sự kiện thay đổi trạng thái nào.
- Một người dùng bị bật, tắt rồi bật lại trong cùng một kỳ được tính là một ghế, không phải hai.

---

### FEAT-12 — Chuyển sự kiện sang lớp thương mại `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Đưa các Sự kiện tính phí đã ghi tại CRM sang nơi tính tiền, với hai cam kết đối nghịch phải cùng đúng: **không mất sự kiện nào** và **không tính phí sự kiện nào hai lần**.

**Actor:** Tác vụ tự động, Kế toán nhà cung cấp (khi cần can thiệp).

**Quy tắc nghiệp vụ:**

- BR-12.1: Mỗi sự kiện PHẢI mang mã tham chiếu duy nhất tới đối tượng nghiệp vụ gốc. Chuyển giao lại một sự kiện đã chuyển thành công, dù bao nhiêu lần, KHÔNG ĐƯỢC tạo thêm đơn vị tính phí nào. Đây là một cam kết nghiệp vụ, không phải một chi tiết kỹ thuật: nó là điều kiện để hệ thống dám thử lại khi không chắc chắn.
- BR-12.2: Sự kiện chưa chuyển giao được PHẢI được giữ lại và thử lại theo lịch, cho tới khi thành công hoặc tới khi cần can thiệp thủ công. KHÔNG ĐƯỢC bỏ sự kiện nào một cách âm thầm.
- BR-12.3: Sự kiện thất bại quá số lần cho phép PHẢI được đưa vào một danh sách chờ xử lý thủ công, kèm nguyên nhân, và PHẢI tạo cảnh báo tới đội vận hành nhà cung cấp nêu rõ số lượng và những doanh nghiệp bị ảnh hưởng.
- BR-12.4: Kết quả tính phí KHÔNG ĐƯỢC phụ thuộc vào thứ tự các sự kiện đến nơi. Một sự kiện đến trước hay sau sự kiện khác không được làm đổi tổng tiền của kỳ.
- BR-12.5: Mức tồn đọng sự kiện chưa chuyển giao PHẢI theo dõi được liên tục, và PHẢI cảnh báo khi vượt ngưỡng — đặc biệt trước thời điểm chốt kỳ. Chốt kỳ với dữ liệu còn tồn đọng tạo ra hóa đơn thiếu, và hóa đơn thiếu chỉ sửa được bằng chứng từ điều chỉnh, tốn kém hơn nhiều so với việc lùi vài giờ.
- BR-12.6: Mọi sự kiện PHẢI được lưu tại CRM ít nhất bằng thời hạn khiếu nại hóa đơn (BR-27.2) cộng thêm biên an toàn, để phục vụ đối soát (FEAT-15) và truy vết (FEAT-27).

**Tiêu chí chấp nhận:**

- Gián đoạn kết nối tới lớp thương mại trong nhiều giờ không làm mất sự kiện nào và không tạo phí trùng khi khôi phục.
- Chốt kỳ bị chặn khi còn tồn đọng vượt ngưỡng, kèm thông báo nêu rõ đang thiếu bao nhiêu và của những doanh nghiệp nào.

---

### FEAT-13 — Sự kiện đến muộn, ranh giới kỳ & ghi nhận bù `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Quy định sự kiện thuộc về kỳ nào, và xử lý những sự kiện đến sau khi kỳ đã kết thúc. Đây là nơi phát sinh phần lớn các lỗi tính phí khó phát hiện, vì chúng chỉ xuất hiện quanh ranh giới kỳ.

**Actor:** Tác vụ tự động, Kế toán nhà cung cấp.

**Quy tắc nghiệp vụ:**

- BR-13.1: Sự kiện được quy về kỳ theo **thời điểm phát sinh nghiệp vụ**, KHÔNG theo thời điểm hệ thống nhận được. Một hội thoại tạo lúc 23h50 ngày cuối kỳ thuộc kỳ đó, kể cả khi sự kiện tới nơi lúc 00h10 hôm sau.
- BR-13.2: Mỗi kỳ có một **thời hạn chốt kỳ** sau ngày cuối kỳ. Sự kiện thuộc kỳ đó, đến trong thời hạn chốt, vẫn được quy về đúng kỳ đó. Thời hạn này là tham số cấu hình và PHẢI đủ dài để bao được độ trễ thông thường của các nền tảng bên thứ ba.
- BR-13.3: Sự kiện đến sau khi kỳ đã chốt và hóa đơn đã phát hành KHÔNG ĐƯỢC làm thay đổi hóa đơn đó. Nó được đưa vào kỳ kế tiếp và PHẢI ghi rõ trên hóa đơn kỳ kế tiếp là phần phát sinh thuộc kỳ trước — doanh nghiệp KHÔNG ĐƯỢC nhìn thấy một khoản tăng đột biến không giải thích được.
- BR-13.3b: Phần phát sinh thuộc kỳ trước ở BR-13.3 PHẢI được tính theo **điều kiện thương mại có hiệu lực tại kỳ mà sự kiện thuộc về** — phiên bản gói, hạn mức, đơn giá vượt và các ưu đãi của kỳ đó — chứ KHÔNG theo điều kiện của kỳ mà nó xuất hiện trên hóa đơn. Nếu giữa hai kỳ doanh nghiệp đã nâng gói (BR-06.1) hoặc một ưu đãi đã hết hạn (BR-08.7), việc tính theo điều kiện kỳ sau chính là áp giá hồi tố ngược, điều NFR-16 cấm. Dòng tương ứng trên hóa đơn PHẢI nêu rõ kỳ áp dụng và điều kiện đã dùng, để doanh nghiệp không phải suy đoán vì sao cùng một loại tiêu dùng lại có hai đơn giá trên một hóa đơn.
- BR-13.4: Múi giờ dùng để cắt kỳ là múi giờ khai báo trong hồ sơ thanh toán của doanh nghiệp, cố định trong suốt vòng đời đăng ký (BR-03.3).
- BR-13.5: Ghi nhận bù sau sự cố PHẢI dùng lại đúng mã tham chiếu gốc của các đối tượng nghiệp vụ, để không tạo phí trùng với phần đã ghi nhận được trước đó (BR-12.1).
- BR-13.6: Mỗi lần ghi nhận bù PHẢI để lại vết đầy đủ: ai thực hiện, khi nào, phạm vi thời gian nào, những doanh nghiệp nào, tổng số đơn vị bổ sung, và căn cứ (FEAT-29).
- BR-13.7: Khi phần bổ sung của một doanh nghiệp vượt ngưỡng đã cấu hình so với mức thông thường của họ, hệ thống PHẢI dừng lại chờ người xác nhận thay vì tự động đưa vào hóa đơn.

**Tiêu chí chấp nhận:**

- Sự kiện phát sinh cuối kỳ, đến nơi đầu kỳ sau, xuất hiện trên hóa đơn của kỳ trước nếu còn trong thời hạn chốt.
- Không hóa đơn nào đã phát hành bị thay đổi số tiền bởi một sự kiện đến muộn.
- Mỗi khoản thuộc kỳ trước xuất hiện trên hóa đơn kỳ sau đều được ghi chú rõ.

---

### FEAT-14 — Điều chỉnh & hủy hiệu lực sự kiện đã ghi nhận `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Xử lý các trường hợp một tiêu dùng đã ghi nhận nhưng không nên tính phí — hội thoại hóa ra là spam, tin nhắn hóa ra không gửi được, sự cố hệ thống, hoặc quyết định miễn trừ thương mại.

**Actor:** Kế toán nhà cung cấp, Tác vụ tự động.

**Luồng chính:**

1. Một căn cứ hợp lệ để hủy hiệu lực xuất hiện (tự động phát hiện hoặc do người đề nghị).
2. Hệ thống tạo một bút toán đảo tham chiếu tới sự kiện gốc, ghi rõ căn cứ.
3. Nếu kỳ chứa sự kiện gốc chưa chốt, lượng tiêu dùng của kỳ giảm tương ứng.
4. Nếu kỳ đã phát hành hóa đơn, hệ thống tạo yêu cầu ghi có theo FEAT-23.

**Quy tắc nghiệp vụ:**

- BR-14.1: Sự kiện đã ghi nhận KHÔNG ĐƯỢC sửa và KHÔNG ĐƯỢC xóa. Hủy hiệu lực luôn thực hiện bằng một bút toán đảo tham chiếu tới sự kiện gốc — lịch sử giữ nguyên, kết quả thay đổi.
- BR-14.2: Đảo trong kỳ chưa chốt làm giảm trực tiếp lượng tiêu dùng của kỳ. Đảo sau khi kỳ đã phát hành hóa đơn KHÔNG ĐƯỢC sửa hóa đơn đó mà PHẢI tạo chứng từ ghi có (BR-18.2).
- BR-14.3: Các căn cứ đảo hợp lệ PHẢI được liệt kê giới hạn, và mỗi bút toán đảo PHẢI thuộc đúng một căn cứ: (a) đối tượng gốc bị đánh dấu là lạm dụng theo BR-10.5; (b) bên thứ ba xác nhận việc phục vụ không thực hiện được sau khi đã ghi nhận; (c) sự cố của hệ thống nhà cung cấp theo BR-10.6; (d) quyết định miễn trừ thương mại.
- BR-14.4: Đảo với căn cứ "quyết định miễn trừ thương mại" vượt ngưỡng giá trị đã cấu hình PHẢI có người phê duyệt khác người đề nghị.
- BR-14.5: Tổng giá trị đã đảo trong kỳ, tách theo từng căn cứ, PHẢI xem được như một chỉ số vận hành. Tỷ lệ đảo tăng bất thường ở một căn cứ là dấu hiệu định nghĩa thời điểm tính phí đang sai, không phải chuyện của riêng kế toán.
- BR-14.6: Việc phát hiện đối tượng gốc bị đánh dấu lạm dụng PHẢI kích hoạt đảo tự động, không chờ người thao tác — nếu không, doanh nghiệp phải tự phát hiện mình bị tính phí oan rồi mới khiếu nại.
- BR-14.7: Bút toán đảo trong kỳ chưa chốt PHẢI **hoàn lại phần hạn mức tương ứng ngay lập tức**, không chờ chốt kỳ. Lượng tiêu dùng là căn cứ duy nhất để thực thi hạn mức (FEAT-16), nên nếu đảo giảm lượng tiêu dùng mà không giảm mức đã chiếm hạn mức thì một doanh nghiệp bị một đợt tin nhắn rác vừa không bị tính tiền (BR-10.5) vừa vẫn bị chặn thao tác — tức vẫn chịu trọn hậu quả của việc mình không gây ra. Hệ quả đi kèm: phần trần chi phí vượt đã bị chiếm dụng bởi các sự kiện bị đảo cũng được hoàn lại tương ứng (BR-16.5).
- BR-14.8: Việc hoàn hạn mức ở BR-14.7 KHÔNG tự động hủy hay hoàn tiền một add-on mà doanh nghiệp đã mua trước đó vì tưởng mình hết hạn mức — phần hạn mức mua thêm đi theo điều kiện của chính add-on đó (BR-07.2). Nhưng khi một đợt đảo tự động theo BR-14.6 xảy ra trong cùng kỳ với một lần mua thêm hạn mức, hệ thống PHẢI thông báo cho Người phụ trách thanh toán để họ tự quyết định, và trường hợp này PHẢI đếm được như một chỉ số vận hành theo BR-14.5. Không có quy tắc này thì hệ thống vừa bán thêm hạn mức cho một nhu cầu do lỗi phía mình tạo ra, vừa không ai nhìn thấy điều đó đang lặp lại.

**Tiêu chí chấp nhận:**

- Một hội thoại bị đánh dấu spam trong kỳ không xuất hiện trên hóa đơn kỳ đó, không cần ai can thiệp.
- Một hội thoại bị đánh dấu spam sau khi hóa đơn đã phát hành tạo ra chứng từ ghi có dẫn ngược được về hội thoại đó.
- Không có cách nào làm mất dấu một sự kiện đã ghi nhận.
- Một doanh nghiệp đang bị chặn vì hết hạn mức, sau khi các hội thoại rác trong kỳ bị đảo, thao tác lại được ngay mà không cần ai can thiệp và không cần mua thêm gì.

---

### FEAT-15 — Đối soát tiêu dùng `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** So khớp định kỳ giữa số đối tượng nghiệp vụ có thật trong CRM và số đơn vị tính phí lớp thương mại đang ghi nhận, để phát hiện lệch **trước khi** hóa đơn ra khỏi hệ thống.

**Actor:** Tác vụ tự động, Kế toán nhà cung cấp.

**Quy tắc nghiệp vụ:**

- BR-15.1: Hệ thống PHẢI đối soát định kỳ theo từng doanh nghiệp và từng loại tiêu dùng, ít nhất một lần mỗi ngày và bắt buộc một lần trước khi chốt mỗi kỳ.
- BR-15.2: Kết quả đối soát PHẢI nêu được cả hai chiều lệch và không gộp chúng lại: **tính thiếu** (có đối tượng trong CRM nhưng chưa thành đơn vị tính phí) và **tính thừa** (có đơn vị tính phí nhưng không tìm thấy đối tượng gốc). Hai chiều có nguyên nhân và hệ quả khác nhau, xử lý khác nhau.
- BR-15.3: Chênh lệch vượt ngưỡng đã cấu hình PHẢI chặn việc phát hành hóa đơn của doanh nghiệp đó và tạo cảnh báo, cho tới khi có người xử lý (BR-18.5). Hóa đơn sai gửi đi rồi tốn kém hơn nhiều so với việc phát hành muộn một ngày.
- BR-15.4: Mỗi lần đối soát PHẢI lưu lại kết quả, kể cả khi khớp hoàn toàn — đây là bằng chứng để trả lời khiếu nại và để phát hiện xu hướng lệch tăng dần.
- BR-15.5: Xu hướng chênh lệch theo thời gian PHẢI xem được, không chỉ ảnh chụp tại một thời điểm. Một mức lệch nhỏ nhưng tăng đều nguy hiểm hơn một lần lệch lớn đơn lẻ.

**Tiêu chí chấp nhận:**

- Mọi kỳ được phát hành hóa đơn đều có kết quả đối soát tương ứng nằm trong ngưỡng cho phép.
- Khi có chênh lệch, xác định được ngay chiều lệch, doanh nghiệp nào, loại tiêu dùng nào, quy mô bao nhiêu.

---

### FEAT-16 — Hạn mức bao gồm, phí vượt & chính sách chạm trần `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Quy định điều gì xảy ra khi một doanh nghiệp dùng hết phần bao gồm trong gói. Mục này quyết định trải nghiệm ở đúng khoảnh khắc nhạy cảm nhất của quan hệ thương mại, nên nó có một ràng buộc mà mọi phần còn lại phải tôn trọng: **việc kiểm soát chi phí không được đổ lên đầu khách hàng của doanh nghiệp**.

**Actor:** Thành viên, Quản trị viên tenant, Chủ workspace, Người phụ trách thanh toán, Tác vụ tự động.

**Luồng chính:**

1. Một thao tác sắp phát sinh tiêu dùng. Hệ thống xác định thao tác thuộc chiều nào: do doanh nghiệp chủ động thực hiện, hay do khách hàng của doanh nghiệp khởi xướng (BR-16.3).
2. Với chiều do khách hàng khởi xướng: **luôn tiếp nhận và luôn ghi nhận**, không có bước kiểm tra chặn nào. Việc kiểm soát chi phí ở chiều này diễn ra ở khâu tính tiền, không ở khâu phục vụ (BR-16.8).
3. Với chiều do doanh nghiệp chủ động: hệ thống đối chiếu khối lượng của thao tác — với thao tác theo lô là **toàn bộ khối lượng dự kiến của lô** (BR-16.9) — với hạn mức bao gồm còn lại và phần trần chi phí vượt còn lại.
4. Đủ hạn mức bao gồm: cho phép, không thông báo gì.
5. Hết hạn mức bao gồm: thực thi chính sách chạm trần của hạn mức đó (BR-16.2), trong khuôn khổ trần chi phí vượt của doanh nghiệp (BR-16.2b).
6. Chạm trần chi phí vượt: dừng lại, cảnh báo, và chờ xác nhận theo BR-16.5b. Người thao tác không có quyền xem dữ liệu tài chính chỉ nhận thông báo theo BR-16.6, không thấy số tiền.

**Quy tắc nghiệp vụ:**

- BR-16.1: Hạn mức bao gồm tính theo từng kỳ và KHÔNG cộng dồn sang kỳ sau, trừ khi điều kiện gói ghi rõ là có cộng dồn cùng thời hạn hiệu lực của phần cộng dồn.
- BR-16.2: Mỗi hạn mức gắn với đúng một trong ba chính sách chạm trần: **chặn** (không cho phát sinh thêm), **cho vượt và tính phí** (không giới hạn trên, trong khuôn khổ BR-16.5 và BR-16.8), hoặc **cho vượt tới trần cứng rồi chặn**.
- BR-16.2b: Hai cơ chế trần cùng tồn tại và PHẢI có thứ tự ưu tiên cố định, vì chúng khác đơn vị và khác phạm vi: trần cứng của BR-16.2 tính bằng **đơn vị tiêu dùng** và áp cho **một hạn mức**; trần của BR-16.5 tính bằng **tiền** và áp cho **toàn bộ doanh nghiệp trong một kỳ**. Thứ tự áp là: **cái nào chạm trước thì cái đó dừng thao tác**. Cụ thể, một hạn mức khai chính sách "cho vượt và tính phí" vẫn bị dừng khi trần chi phí vượt của doanh nghiệp đã chạm — cụm "không giới hạn trên" ở BR-16.2 là không giới hạn *theo số đơn vị của riêng hạn mức đó*, không phải không giới hạn theo tiền. Không cố định điểm này thì hai người đọc cùng tài liệu sẽ xây ra hai hành vi khác nhau ở đúng khoảnh khắc nhạy cảm nhất của quan hệ thương mại.
- BR-16.3: Chính sách chặn CHỈ được áp cho các hành vi **do doanh nghiệp chủ động thực hiện** — gửi tin nhắn mẫu, chạy chiến dịch, chạy quy trình tự động, thêm người dùng. Chính sách chặn KHÔNG ĐƯỢC áp cho việc tiếp nhận hoạt động **do khách hàng của doanh nghiệp khởi xướng**: tin nhắn khách hàng gửi tới PHẢI luôn được tiếp nhận, lưu lại và hiển thị cho Agent, kể cả khi hạn mức hội thoại đã hết. Chặn ở chiều này biến một vấn đề thương mại giữa nhà cung cấp và doanh nghiệp thành thiệt hại cho một bên thứ ba vô can, và doanh nghiệp sẽ chỉ phát hiện ra khi đã mất khách. Ngoại lệ được liệt kê giới hạn, gồm đúng hai trường hợp và không mở rộng thêm: (a) ngắt kênh có thông báo trước sau khi hết thời hạn ân hạn của một tài khoản **chưa từng trả tiền** (BR-05.4b); (b) ngắt kênh có thông báo trước sau khi một quan hệ trả tiền **đã kết thúc** — đăng ký đã hủy hoặc đã hết hạn (BR-24.10). Cả hai đều là kết thúc một quan hệ chứ không phải một biện pháp kiểm soát chi phí bên trong một quan hệ đang có; đó là điều phân biệt chúng với mọi tình huống còn lại. Đình chỉ vì nợ KHÔNG thuộc nhóm này (BR-22.3): ở đó quan hệ vẫn tồn tại và doanh nghiệp vẫn có thể trả tiền để khôi phục.
- BR-16.4: Doanh nghiệp PHẢI được báo **trước khi** bắt đầu phát sinh phí vượt, không chỉ báo sau khi đã phát sinh (FEAT-17).
- BR-16.5: Mỗi doanh nghiệp PHẢI có một trần chi phí vượt cho mỗi kỳ, mặc định tính theo bội số của phí thuê bao. **Với các loại tiêu dùng phát sinh từ hành vi doanh nghiệp chủ động thực hiện**, chạm trần này thì hệ thống PHẢI dừng lại, cảnh báo, và yêu cầu doanh nghiệp xác nhận trước khi tiếp tục phát sinh thêm. Không có trần thì một sự cố tự động hóa hoặc một chiến dịch cấu hình sai có thể tạo ra hóa đơn gấp nhiều lần bình thường — và khoản đó thực tế không thu được, chỉ tạo ra một cuộc tranh chấp. Với các loại tiêu dùng phát sinh từ hoạt động do khách hàng của doanh nghiệp khởi xướng, trần này KHÔNG thực thi được bằng cách chặn — xem BR-16.8.
- BR-16.5b: Việc xác nhận ở BR-16.5 PHẢI là việc doanh nghiệp **nâng trần lên một mức mới cụ thể do chính họ nhập**, có hiệu lực cho phần còn lại của kỳ, và chạm mức mới đó thì hệ thống lại dừng và lại hỏi. Xác nhận KHÔNG ĐƯỢC mang nghĩa "bỏ trần cho tới hết kỳ": nếu một lần bấm đồng ý gỡ được trần thì mọi bảo vệ của BR-16.5 chấm dứt ngay tại lần chạm đầu tiên, và một quy trình tự động chạy sai vẫn nhân chi phí lên tới cuối kỳ — đúng tình huống mà quy tắc này được lập ra để chặn. Mỗi lần nâng trần là một quyết định thương mại làm thay đổi số tiền phải trả, nên PHẢI để lại vết theo BR-29.1 và PHẢI do vai trò được phép theo Mục 5 thực hiện.
- BR-16.6: Khi một thao tác bị chặn vì hạn mức, thông báo hiển thị cho người thao tác PHẢI nêu rõ đang chạm hạn mức nào và ai trong doanh nghiệp xử lý được — KHÔNG ĐƯỢC báo lỗi chung chung khiến người dùng tưởng hệ thống hỏng.
- BR-16.7: Kiểm tra hạn mức PHẢI cho ra cùng một kết quả dù thao tác đến từ giao diện người dùng, từ tích hợp bên ngoài hay từ tác vụ tự động.
- BR-16.8: BR-16.3 cấm chặn ở chiều tiếp nhận, nên với các loại tiêu dùng phát sinh từ hoạt động do khách hàng của doanh nghiệp khởi xướng, trần của BR-16.5 PHẢI vận hành như một **trần tính tiền**, không phải một trần chặn: hoạt động vẫn được tiếp nhận và vẫn được ghi nhận đầy đủ (BR-32.2), nhưng phần vượt quá trần trong kỳ KHÔNG ĐƯỢC đưa vào hóa đơn khi doanh nghiệp chưa xác nhận riêng cho phần đó. Phần chênh trở thành **chi phí nhà cung cấp tự chịu**, PHẢI được đo riêng, PHẢI tạo cảnh báo vận hành và PHẢI báo cáo cùng giá vốn tiêu dùng (BR-30.3). Không có quy tắc này thì một đợt tin nhắn rác gửi tới một doanh nghiệp tạo ra một khoản nhà cung cấp thực trả cho nền tảng kênh mà vừa không chặn được (BR-16.3), vừa không tính lại cho doanh nghiệp được (BR-10.5) — tức một lỗ thất thu không có trần, đúng thứ mà BR-16.5 được lập ra để chặn. Chính sách xử lý khi mức tự chịu này vượt ngưỡng đã cấu hình PHẢI được quyết định rõ thay vì để mặc định — xem Mục 7.2.
- BR-16.9: Một thao tác phát sinh tiêu dùng theo lô — chiến dịch gửi hàng loạt, nhập liệu hàng loạt, lượt chạy quy trình trên một tập bản ghi — PHẢI được đối chiếu **toàn bộ khối lượng dự kiến của lô** với phần hạn mức còn lại và phần trần chi phí vượt còn lại **trước khi bắt đầu**. Nếu không đủ, lô KHÔNG ĐƯỢC khởi động rồi dừng giữa chừng: hoặc bị từ chối, hoặc chỉ chạy sau khi doanh nghiệp xác nhận trên một con số cụ thể (BR-26.5). Việc xác nhận số tiền là của vai trò được phép thấy dữ liệu tài chính theo Mục 5; người thao tác không thuộc nhóm đó chỉ nhận thông báo theo BR-16.6 — nêu rõ đang chạm hạn mức nào và ai trong doanh nghiệp quyết định được — chứ KHÔNG ĐƯỢC nhìn thấy số tiền (BR-26.3). Một chiến dịch gửi được một nửa rồi dừng gây thiệt hại lớn hơn một chiến dịch không gửi — nó chia tập khách hàng thành hai nhóm nhận thông điệp khác nhau và doanh nghiệp không biết ranh giới nằm ở đâu. Quy tắc này bổ sung cho BR-32.3, vốn chỉ quy định trường hợp *không tra được* hạn mức, chứ không quy định trường hợp tra được và biết là không đủ.
- BR-16.10: **Trần tính tiền của BR-16.8 được thực thi ở lớp thương mại, không ở lớp ghi nhận.** Đây là quy tắc duy nhất trong tài liệu buộc một lượng tiêu dùng có thật không xuất hiện trên hóa đơn, nên phải nói rõ ai làm việc đó. Hai hệ quả bắt buộc: (a) cụm "vẫn được ghi nhận đầy đủ" ở BR-16.8 bao gồm **cả việc chuyển giao sang lớp thương mại** theo FEAT-12 — giữ sự kiện lại không chuyển giao để tránh bị tính tiền là vi phạm NFR-7 và làm đối soát (FEAT-15) báo lệch giả ở mọi doanh nghiệp bị vượt trần; (b) việc cắt phần vượt trần ra khỏi hóa đơn là một **quy tắc tính tiền**, thuộc về nơi sở hữu giá và hạn mức, KHÔNG ĐƯỢC đặt vào các module nghiệp vụ của CRM — đặt ở đó thì nghiệp vụ phải biết đơn giá và trần, phá nguyên tắc nền ở Mục 2.2. Phần bị cắt ra PHẢI xuất hiện đầy đủ trong số liệu tiêu dùng mà doanh nghiệp xem được (FEAT-17), kèm ghi chú rõ là phần này không được tính tiền trong kỳ — doanh nghiệp KHÔNG ĐƯỢC thấy một khoảng trống không giải thích được giữa lượng dùng và lượng bị tính.

**Tiêu chí chấp nhận:**

- Một doanh nghiệp hết hạn mức hội thoại vẫn nhận được tin nhắn của khách hàng và Agent vẫn đọc được.
- Phần tiêu dùng vượt trần bị loại khỏi hóa đơn vẫn hiện đủ trên màn hình theo dõi tiêu dùng của doanh nghiệp, kèm ghi chú vì sao nó không được tính tiền.
- Một xác nhận nâng trần chỉ có hiệu lực tới mức đã nhập; chạm mức đó hệ thống dừng lại lần nữa.
- Không doanh nghiệp nào nhận hóa đơn có phí vượt mà trước đó không nhận cảnh báo.
- Một quy trình tự động chạy sai không thể tạo ra phí vượt không giới hạn.
- Một chiến dịch không đủ hạn mức bị từ chối trọn vẹn hoặc chạy trọn vẹn sau khi doanh nghiệp xác nhận — không tồn tại ca gửi được một phần rồi dừng.
- Phần tiêu dùng do khách hàng của doanh nghiệp khởi xướng vượt trần của một kỳ không xuất hiện trên hóa đơn khi chưa có xác nhận, và luôn xuất hiện trong số liệu giá vốn của nhà cung cấp.

---

### FEAT-17 — Theo dõi tiêu dùng & cảnh báo ngưỡng `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho doanh nghiệp nhìn thấy mình đang dùng tới đâu và sẽ phải trả khoảng bao nhiêu, đủ sớm để còn kịp làm gì đó.

**Actor:** Chủ workspace, Người phụ trách thanh toán, Quản trị viên tenant.

**Quy tắc nghiệp vụ:**

- BR-17.1: Doanh nghiệp PHẢI xem được, cho kỳ đang chạy: lượng tiêu dùng hiện tại theo từng loại, hạn mức tương ứng, phần đã vượt nếu có, và **ước tính số tiền của kỳ đang chạy**.
- BR-17.2: Con số ước tính PHẢI ghi rõ là ước tính và PHẢI nêu độ mới của dữ liệu. Số liệu về tiền KHÔNG ĐƯỢC để người đọc tưởng là chính xác tuyệt đối và tức thời trong khi thực tế có độ trễ.
- BR-17.3: Khi số liệu đang thiếu vì còn tồn đọng chưa chuyển giao (BR-12.5), màn hình PHẢI nói rõ là đang thiếu thay vì hiển thị như đầy đủ. Một con số thiếu mà người đọc biết là thiếu ít nguy hại hơn một con số thiếu mà người đọc tin là đủ.
- BR-17.4: Cảnh báo ngưỡng PHẢI cấu hình được theo từng loại tiêu dùng, mặc định tại 80% và 100% hạn mức, gửi tới Người phụ trách thanh toán và Chủ workspace.
- BR-17.5: Doanh nghiệp PHẢI xem được lịch sử tiêu dùng của các kỳ trước để so sánh và dự đoán, tối thiểu 12 kỳ gần nhất hoặc toàn bộ thời gian sử dụng nếu ngắn hơn.
- BR-17.6: Màn hình theo dõi PHẢI cho phép đi từ một con số tổng xuống danh sách đối tượng gốc trong phạm vi quyền của người xem (BR-27.5).

**Tiêu chí chấp nhận:**

- Doanh nghiệp trả lời được câu hỏi "tháng này tôi sẽ phải trả khoảng bao nhiêu" mà không cần liên hệ nhà cung cấp.
- Khi hệ thống đang tồn đọng sự kiện, không có màn hình nào hiển thị số liệu thiếu như số liệu đủ.

---
## C. HÓA ĐƠN & THU TIỀN

### FEAT-18 — Lập & phát hành hóa đơn cuối kỳ `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Chốt một chu kỳ thành một chứng từ mà doanh nghiệp đọc hiểu, kế toán chấp nhận và hệ thống không sửa lại được.

**Actor:** Tác vụ tự động, Kế toán nhà cung cấp.

**Luồng chính:**

1. Kỳ kết thúc; hệ thống chờ hết thời hạn chốt kỳ (BR-13.2).
2. Hệ thống chạy đối soát (FEAT-15) và dựng bản nháp hóa đơn.
3. Kế toán nhà cung cấp soát bản nháp trên các trường hợp cần soát theo chính sách.
4. Hóa đơn được phát hành, gửi tới doanh nghiệp và chuyển sang bước thu tiền (FEAT-20).

**Quy tắc nghiệp vụ:**

- BR-18.1: Trước khi phát hành, PHẢI có bản nháp xem trước được — cho cả một doanh nghiệp cụ thể lẫn cho toàn bộ đợt chốt kỳ. Chạy thử toàn đợt là công cụ duy nhất phát hiện được lỗi hệ thống trước khi nó nhân lên trên hàng trăm hóa đơn.
- BR-18.2: Hóa đơn đã phát hành là **bất biến**. Mọi điều chỉnh về sau thực hiện bằng chứng từ ghi có hoặc bằng một hóa đơn mới (FEAT-23). KHÔNG ĐƯỢC sửa số tiền, sửa dòng, hay xóa một hóa đơn đã phát hành.
- BR-18.3: Hóa đơn PHẢI tách bạch từng nhóm khoản mục và không gộp: phí thuê bao (kèm kỳ áp dụng), từng loại phí vượt (kèm số lượng, hạn mức bao gồm và đơn giá), từng add-on, từng khoản ưu đãi kèm căn cứ, phần phát sinh thuộc kỳ trước theo BR-13.3, thuế, phần bù trừ từ số dư có nếu có (BR-23.5), và **ngày tới hạn thanh toán** (BR-18.9).
- BR-18.4: Mỗi dòng tiêu dùng trên hóa đơn PHẢI dẫn xuống được danh sách đối tượng gốc tạo nên con số đó (FEAT-27).
- BR-18.5: Hệ thống KHÔNG ĐƯỢC phát hành hóa đơn cho một doanh nghiệp mà kết quả đối soát của kỳ đó đang báo chênh lệch vượt ngưỡng, hoặc còn sự kiện tồn đọng vượt ngưỡng. Trường hợp này PHẢI chuyển sang xử lý có người soát, không tự động bỏ qua và cũng không tự động phát hành.
- BR-18.6: Số hóa đơn PHẢI liên tục, không trùng và không nhảy khoảng vô căn cứ. Khi một hóa đơn bị hủy trước khi phát hành, số đó KHÔNG ĐƯỢC tái sử dụng. Ở những thị trường mà số hoặc mã hóa đơn do cơ quan thuế cấp (BR-19.3), dãy số do cơ quan đó quy định là dãy số có hiệu lực; hệ thống KHÔNG ĐƯỢC duy trì một dãy số riêng song song để rồi hai dãy không khớp nhau khi đối chiếu.
- BR-18.7: Hóa đơn có tổng bằng không vẫn PHẢI được phát hành và gửi (ví dụ doanh nghiệp đang trong kỳ được miễn phí hoàn toàn) — để doanh nghiệp có chứng từ liên tục và để lịch sử không bị đứt quãng.
- BR-18.8: Ngôn ngữ và định dạng số/tiền tệ trên hóa đơn PHẢI theo hồ sơ thanh toán của doanh nghiệp, không theo cấu hình của nhà cung cấp.
- BR-18.9: Mỗi hóa đơn PHẢI có một **ngày tới hạn thanh toán**, bằng ngày hóa đơn thực sự phát hành cộng khoảng ân hạn thanh toán của phương thức đang áp dụng. Ba ràng buộc đi kèm: (a) mốc đếm là ngày **thực sự phát hành** — ở thị trường mà hóa đơn chỉ được coi là phát hành sau khi cơ quan thuế cấp mã (BR-19.3), ngày tới hạn tính từ thời điểm đó chứ KHÔNG từ ngày chốt kỳ, vì doanh nghiệp không thể trả một hóa đơn chưa nhận được; (b) khoảng ân hạn khác nhau theo phương thức và PHẢI khai báo rõ, không để mặc định ngầm; (c) ngày tới hạn PHẢI xuất hiện trên hóa đơn và trong thông báo phát hành hóa đơn (BR-31.4). Đây là mốc khởi phát duy nhất của FEAT-21 — trước đây tài liệu viện dẫn "tới hạn" mà không có quy tắc nào định nghĩa nó. Con số ân hạn: xem Mục 7.2.
- BR-18.10: Việc làm tròn PHẢI thực hiện ở **cấp độ từng dòng khoản mục trước khi cộng tổng**, KHÔNG ĐƯỢC cộng các giá trị chưa làm tròn rồi mới làm tròn ở tổng. Trình tự bắt buộc: làm tròn từng dòng → cộng thành tổng trước thuế → áp thuế trên tổng đã làm tròn → làm tròn phần thuế → cộng ra tổng phải trả. Độ chính xác lấy theo tiền tệ của hồ sơ thanh toán (ví dụ đồng Việt Nam không có phần thập phân, đô la Mỹ có hai chữ số). Phương pháp làm tròn là **làm tròn nửa lên** cho toàn hệ thống; đây là một con số cố định trong tài liệu chứ không phải tham số, vì nó quyết định trực tiếp số tiền người trả phải trả và do đó là một cam kết, không phải một lựa chọn kỹ thuật. Quy tắc này áp cho cả phần chênh lệch tính theo tỷ lệ thời gian khi đổi gói (BR-06.1, BR-06.4). Không có nó, tiêu chí "doanh nghiệp tự tính lại được tổng tiền từ các dòng" không kiểm chứng được và mọi chênh lệch một đơn vị tiền đều trở thành một khiếu nại.

**Tiêu chí chấp nhận:**

- Không hóa đơn nào đã phát hành thay đổi nội dung về sau.
- Một doanh nghiệp cầm hóa đơn tự tính lại được tổng tiền từ các dòng trên đó, ra đúng tới đơn vị tiền nhỏ nhất của tiền tệ đang dùng.
- Mọi hóa đơn đã phát hành đều có ngày tới hạn hiển thị trên chứng từ, và không hóa đơn nào bước vào chu trình nhắc nợ trước ngày đó.
- Đợt chốt kỳ có lỗi hệ thống bị phát hiện ở bước chạy thử, không phải ở bước khách hàng khiếu nại.

---

### FEAT-18b — Vòng đời & trạng thái hóa đơn `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Định nghĩa tập trạng thái của một hóa đơn và các chuyển đổi hợp lệ giữa chúng, để mọi phần còn lại của hệ thống — thu tiền, nhắc nợ, đình chỉ, ghi có, khiếu nại — đọc từ một nguồn duy nhất. FEAT-04 làm việc này cho **đăng ký**; mục này làm đúng việc đó cho **chứng từ**. Không có nó thì các quy tắc ở FEAT-19 → FEAT-23 và FEAT-27 đang viện dẫn những trạng thái chưa ai khai báo, và hai người đọc cùng tài liệu sẽ dựng ra hai vòng đời khác nhau.

**Actor:** Tác vụ tự động, Kế toán nhà cung cấp.

**Luồng chính:**

1. Kỳ chốt, hệ thống dựng hóa đơn ở trạng thái **nháp**; nó xem trước và soát được, chưa có hiệu lực với doanh nghiệp (BR-18.1).
2. Nếu thị trường yêu cầu thủ tục bắt buộc với cơ quan thuế, hóa đơn chuyển sang **chờ thủ tục bắt buộc** và dừng lại ở đó cho tới khi thủ tục hoàn tất (BR-19.3).
3. Hóa đơn chuyển sang **đã phát hành**: nhận số hóa đơn, có ngày phát hành và ngày tới hạn, gửi tới doanh nghiệp, và từ đây trở thành bất biến (BR-18.2).
4. Khi nghĩa vụ được giải quyết trọn vẹn — bằng tiền thu được, bằng số dư có bù trừ, bằng chứng từ ghi có, hoặc vì tổng phải trả bằng không — hóa đơn chuyển sang **đã thanh toán**.
5. Một hóa đơn còn ở nháp mà không được phát hành thì kết thúc ở **đã hủy nháp**; số đã cấp cho nó không được dùng lại (BR-18.6).

**Quy tắc nghiệp vụ:**

- BR-18b.1: Một hóa đơn PHẢI ở đúng một trạng thái tại một thời điểm, và tập trạng thái PHẢI đủ để phân biệt năm tình huống khác nhau về mặt nghiệp vụ: **nháp** (đã dựng, chưa có hiệu lực với doanh nghiệp, còn sửa và còn hủy được), **chờ thủ tục bắt buộc** (đã chốt nội dung nhưng chưa được coi là phát hành theo quy định của thị trường), **đã phát hành** (bất biến, đã gửi, đã có ngày tới hạn), **đã thanh toán** (nghĩa vụ đã được giải quyết trọn vẹn), và **đã hủy nháp** (kết thúc, chưa từng có hiệu lực).
- BR-18b.2: Chỉ có **một đường vào** trạng thái đã phát hành, và đường đó đi qua đầy đủ các cổng chặn đã quy định: đối soát trong ngưỡng và tồn đọng trong ngưỡng (BR-18.5), thủ tục bắt buộc đã hoàn tất nếu thị trường yêu cầu (BR-19.3). Một hóa đơn đã phát hành KHÔNG BAO GIỜ quay lại nháp, KHÔNG BAO GIỜ bị xóa và KHÔNG BAO GIỜ đổi nội dung — mọi điều chỉnh đi qua chứng từ ghi có hoặc một hóa đơn mới (BR-18.2, NFR-9).
- BR-18b.3: Trong trạng thái chờ thủ tục bắt buộc, hóa đơn KHÔNG ĐƯỢC gửi cho doanh nghiệp, KHÔNG ĐƯỢC thu tiền, KHÔNG ĐƯỢC tính ngày tới hạn và KHÔNG ĐƯỢC bước vào chu trình nhắc nợ (BR-19.3, BR-18.9). Thủ tục thất bại PHẢI tạo cảnh báo và chuyển sang xử lý có người soát; KHÔNG ĐƯỢC để hóa đơn treo im lặng ở trạng thái này quá thời hạn đã cấu hình.
- BR-18b.4: **Quá hạn không phải một trạng thái**, nó là một thuộc tính dẫn xuất từ việc so ngày hiện tại với ngày tới hạn của một hóa đơn đang ở trạng thái đã phát hành. Không vai trò nào và không tác vụ nào được đánh dấu một hóa đơn là quá hạn sớm hơn hoặc muộn hơn mốc đó. Cố định điểm này vì nếu quá hạn là một cờ được set thì chu trình nhắc nợ có thể khởi phát bằng một thao tác sai thay vì bằng thời gian, và BR-21.9 mất hiệu lực.
- BR-18b.5: Hóa đơn có **tổng phải trả bằng không** tại thời điểm phát hành chuyển thẳng sang đã thanh toán, bỏ qua bước thu tiền và không bao giờ vào chu trình nhắc nợ (BR-20.11, BR-18.7).
- BR-18b.6: **Khiếu nại gắn với từng dòng của hóa đơn, không phải với cả hóa đơn.** Một hóa đơn đang có khiếu nại mở KHÔNG chuyển sang một trạng thái riêng; phần giá trị bị khiếu nại được tạm dừng thu tiền theo BR-27.3 và BR-27.7, còn phần không bị khiếu nại vẫn tới hạn và vẫn thu bình thường. Biểu diễn khiếu nại thành một trạng thái của cả chứng từ sẽ khiến một khiếu nại vài chục nghìn đồng chặn việc thu toàn bộ hóa đơn — và đó chính là kẽ hở hoãn thanh toán mà BR-27.7 lập ra để chặn.
- BR-18b.7: Việc chuyển sang đã thanh toán PHẢI ghi rõ nghĩa vụ được giải quyết bằng đường nào — tiền thu được, số dư có bù trừ (BR-23.5), chứng từ ghi có (BR-23.1), dung sai thanh toán (BR-20.8), hay tổng bằng không (BR-18b.5). Gộp mọi đường lại thành một trạng thái không phân biệt thì báo cáo doanh thu (FEAT-30) không tách được phần thực thu với phần giảm trừ, và đó là hai con số không được lẫn.
- BR-18b.8: Mọi chuyển trạng thái của hóa đơn PHẢI có nguyên nhân ghi nhận được và thuộc diện nhật ký thương mại theo BR-29.1b — kể cả khi do tác vụ tự động thực hiện. Phát hành một hóa đơn và ghi nhận nó đã thanh toán đều là quyết định thương mại làm thay đổi nghĩa vụ của một doanh nghiệp.
- BR-18b.9: Doanh nghiệp PHẢI nhìn thấy tình trạng hóa đơn của mình bằng ngôn ngữ họ hiểu, phân biệt được tối thiểu: chưa tới hạn, đã tới hạn chưa thanh toán, đang có khiếu nại chưa kết luận, đã thanh toán, và đã được giảm trừ bằng chứng từ ghi có. Trạng thái nội bộ nào không có ý nghĩa với người trả tiền thì KHÔNG ĐƯỢC hiển thị ra ngoài — nhưng cũng KHÔNG ĐƯỢC gộp hai tình huống khác nghĩa vào cùng một nhãn.

**Tiêu chí chấp nhận:**

- Với một hóa đơn bất kỳ, tra ra được ngay nó đang ở trạng thái nào, vào trạng thái đó từ khi nào, vì nguyên nhân gì và do ai.
- Không hóa đơn nào ở thị trường có thủ tục bắt buộc được gửi cho doanh nghiệp hoặc bị nhắc nợ trước khi thủ tục đó hoàn tất.
- Một khiếu nại trên một dòng không làm dừng việc thu tiền của các dòng còn lại trên cùng hóa đơn.
- Không tồn tại hóa đơn nào ở trạng thái quá hạn trong khi ngày tới hạn của nó chưa qua.
- Không tồn tại số hóa đơn nào được cấp hai lần, kể cả cho một hóa đơn đã hủy nháp.

**Tham chiếu:** tập trạng thái phụ thuộc quyết định về thị trường phát hành và thủ tục bắt buộc — xem Mục 7.2 câu 3.

---

### FEAT-19 — Thuế & yêu cầu hóa đơn hợp lệ `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Đảm bảo chứng từ phát hành ra hợp lệ theo quy định nơi phát hành và nơi người mua cư trú.

**Actor:** Kế toán nhà cung cấp, Tác vụ tự động.

**Quy tắc nghiệp vụ:**

- BR-19.1: Hồ sơ thanh toán PHẢI ghi quốc gia, địa chỉ xuất hóa đơn và mã số thuế (nếu có). Đây là căn cứ xác định thuế suất, không phải thông tin trang trí.
- BR-19.2: Thuế áp theo quy định có hiệu lực **tại thời điểm phát hành hóa đơn**, không theo quy định tại thời điểm ký gói. Thay đổi thuế suất KHÔNG ĐƯỢC áp hồi tố lên hóa đơn đã phát hành.
- BR-19.3: Nếu quy định nơi phát hành yêu cầu hóa đơn điện tử phải được cơ quan thuế cấp mã hoặc phê duyệt trước khi gửi người mua, thì hóa đơn KHÔNG ĐƯỢC coi là đã phát hành, KHÔNG ĐƯỢC gửi cho doanh nghiệp và KHÔNG ĐƯỢC khởi động chu trình thu tiền trước khi thủ tục đó hoàn tất. Trường hợp thủ tục thất bại PHẢI có quy trình xử lý riêng và cảnh báo, KHÔNG ĐƯỢC im lặng để hóa đơn treo.
- BR-19.4: Doanh nghiệp PHẢI tự sửa được thông tin xuất hóa đơn cho tới trước thời điểm chốt kỳ (BR-03.4).
- BR-19.5: Chứng từ ghi có PHẢI tuân thủ cùng bộ yêu cầu hợp lệ như hóa đơn của cùng quốc gia.
- BR-19.6: Khi cùng một sản phẩm bán ở nhiều quốc gia có quy tắc chứng từ khác nhau, sự khác nhau đó PHẢI nằm ở cấu hình theo quốc gia, KHÔNG ĐƯỢC nằm ở việc xây riêng luồng nghiệp vụ cho từng thị trường.

**Tiêu chí chấp nhận:**

- Không hóa đơn nào được gửi cho doanh nghiệp khi thủ tục bắt buộc với cơ quan thuế chưa hoàn tất.
- Bổ sung một quốc gia bán hàng mới không kéo theo thay đổi ở các FEAT khác.

**Tham chiếu:** danh sách quốc gia phát hành và yêu cầu tương ứng chưa được chốt — xem Mục 7.2.

---

### FEAT-20 — Phương thức thanh toán & thu tiền `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Thu tiền hóa đơn đã phát hành, qua thanh toán tự động hoặc qua các hình thức thủ công dành cho khách hàng doanh nghiệp lớn.

**Actor:** Người phụ trách thanh toán, Kế toán nhà cung cấp, Tác vụ tự động.

**Quy tắc nghiệp vụ:**

- BR-20.1: Thông tin thẻ và tài khoản thanh toán KHÔNG ĐƯỢC lưu trong CRM dưới bất kỳ hình thức nào, kể cả dạng che một phần. CRM chỉ giữ lại phần thông tin đủ để người dùng nhận ra phương thức nào đang dùng.
- BR-20.2: Doanh nghiệp PHẢI có ít nhất một phương thức thanh toán hợp lệ trước khi đăng ký trả phí kích hoạt, trừ trường hợp đã thỏa thuận hình thức trả sau.
- BR-20.3: Hình thức thanh toán thủ công (chuyển khoản, hóa đơn trả sau theo hợp đồng) PHẢI được hỗ trợ. Việc ghi nhận một khoản đã thu bằng hình thức này PHẢI do người có thẩm quyền thực hiện, kèm căn cứ đối chiếu, và để lại vết (FEAT-29).
- BR-20.4: Phương thức thanh toán sắp hết hiệu lực PHẢI được nhắc trước, đủ sớm để đổi kịp trước kỳ thu tiền tiếp theo.
- BR-20.5: Mọi thay đổi phương thức thanh toán PHẢI thông báo tới Người phụ trách thanh toán và Chủ workspace — kể cả khi do chính một trong hai người đó thực hiện.
- BR-20.6: Một khoản thu thành công PHẢI luôn gắn với đúng một hóa đơn cụ thể. KHÔNG ĐƯỢC để tồn tại lâu dài một khoản thu không xác định được trả cho hóa đơn nào.
- BR-20.7: Khi doanh nghiệp trả thừa hoặc trả trước, phần dôi ra PHẢI ghi nhận thành số dư có của doanh nghiệp và tự động bù trừ vào hóa đơn kế tiếp, không tự động hoàn tiền và không bỏ qua.
- BR-20.8: Chiều ngược lại — **trả thiếu một khoản nhỏ** — PHẢI có quy tắc riêng, không được rơi thẳng vào chu trình nhắc nợ. Với thanh toán qua chuyển khoản, số tiền về tài khoản nhà cung cấp thường hụt so với hóa đơn vì phí trung gian hoặc chênh lệch quy đổi ở phía ngân hàng gửi. Hệ thống PHẢI có một **ngưỡng dung sai thanh toán**; khoản thiếu nằm trong ngưỡng thì hóa đơn được coi là đã thanh toán đủ và phần hụt được ghi nhận thành một khoản chi phí riêng, có vết (FEAT-29). Không có quy tắc này thì một khách hàng lớn bị đình chỉ vì thiếu vài đơn vị tiền — thiệt hại lớn hơn nhiều lần khoản hụt. Ngưỡng cụ thể: xem Mục 7.2.
- BR-20.9: Dung sai của BR-20.8 KHÔNG ĐƯỢC trở thành một mức giảm giá ngầm. Ngưỡng PHẢI là một số tuyệt đối nhỏ theo từng tiền tệ chứ không phải một tỷ lệ trên giá trị hóa đơn, và việc một doanh nghiệp trả thiếu trong ngưỡng lặp lại nhiều kỳ liên tiếp PHẢI được phát hiện và chuyển sang xử lý có người soát.
- BR-20.10: Khoản tiền nhận được mà chưa khớp được với hóa đơn nào — chuyển khoản thiếu nội dung, sai số tham chiếu, gộp nhiều hóa đơn vào một lệnh — PHẢI được giữ ở một trạng thái chờ khớp và chuyển cho người có thẩm quyền xử lý, KHÔNG ĐƯỢC tự động gán cho hóa đơn cũ nhất và cũng KHÔNG ĐƯỢC bỏ qua. Đây là cùng nguyên tắc đã áp cho sự kiện không xác định được doanh nghiệp ở BR-09.7: thà giữ lại chờ người quyết định còn hơn đoán sai rồi để lệch âm thầm.
- BR-20.11: Hóa đơn có **tổng phải trả bằng không** — vì bản thân kỳ đó không phát sinh khoản nào (BR-18.7), hoặc vì số dư có đã bù trừ hết (BR-23.5) — PHẢI tự động chuyển sang trạng thái đã thanh toán ngay khi phát hành, KHÔNG ĐƯỢC đưa vào chu trình thu tiền và KHÔNG ĐƯỢC đưa vào chu trình nhắc nợ. Một lệnh thu số tiền bằng không thường bị từ chối ở phía cổng thanh toán, và nếu hệ thống hiểu lần từ chối đó là một lần thu thất bại thì doanh nghiệp sẽ nhận thư nhắc nợ và có thể bị đình chỉ vì một hóa đơn họ không nợ gì.

**Tiêu chí chấp nhận:**

- Không tồn tại bất kỳ dữ liệu thẻ nào trong phạm vi CRM.
- Mọi khoản đã thu đều đối chiếu được về một hóa đơn.
- Doanh nghiệp trả bằng chuyển khoản nhận được cùng chất lượng chứng từ và thông báo như doanh nghiệp thanh toán tự động.
- Một khách hàng chuyển khoản bị ngân hàng trung gian trừ phí không bao giờ bước vào chu trình nhắc nợ vì phần hụt đó.
- Không tồn tại khoản tiền đã nhận nào bị tự động gán cho một hóa đơn mà không có người xác nhận.

---

### FEAT-21 — Thanh toán thất bại & nhắc nợ `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Chu trình thu hồi khi một hóa đơn tới hạn chưa được thanh toán, với nguyên tắc thu được tiền mà không làm hỏng quan hệ khách hàng.

**Actor:** Tác vụ tự động, Người phụ trách thanh toán, Chăm sóc khách hàng nhà cung cấp.

**Luồng chính:**

1. Lần thu đầu tiên thất bại; đăng ký chuyển sang trạng thái nợ quá hạn.
2. Hệ thống gửi thông báo lần đầu, nêu rõ nguyên nhân và cách xử lý.
3. Hệ thống thử thu lại theo lịch đã cấu hình, mỗi lần kèm một thông báo.
4. Hết chu trình mà chưa thu được, hệ thống thông báo trước rồi chuyển sang đình chỉ (FEAT-22).

**Quy tắc nghiệp vụ:**

- BR-21.1: Lịch thử lại và nội dung từng lần nhắc PHẢI cấu hình được và PHẢI khác nhau theo phân khúc doanh nghiệp — một khách hàng lớn trả bằng chuyển khoản không nên nhận cùng chuỗi nhắc như một tài khoản tự phục vụ thẻ hỏng.
- BR-21.2: Mỗi lần thất bại PHẢI ghi nhận nguyên nhân ở mức phân nhóm được (không đủ số dư, phương thức bị từ chối, phương thức hết hiệu lực, lỗi phía cổng thanh toán), vì mỗi nhóm dẫn tới một việc cần làm khác nhau. Thông báo gửi doanh nghiệp PHẢI nêu đúng việc cần làm, không chỉ nêu "thanh toán thất bại".
- BR-21.3: Lỗi thuộc về phía hệ thống hoặc phía cổng thanh toán KHÔNG ĐƯỢC tính vào số lần thất bại dẫn tới đình chỉ.
- BR-21.4: Thông báo nhắc nợ PHẢI gửi tới cả Người phụ trách thanh toán và Chủ workspace. Một doanh nghiệp KHÔNG ĐƯỢC bị đình chỉ vì thông báo chỉ đến một hộp thư mà người đó đang nghỉ.
- BR-21.5: Doanh nghiệp PHẢI thanh toán lại được ngay từ chính thông báo nhận được, không phải tự tìm đường trong sản phẩm.
- BR-21.6: Trước khi đình chỉ, hệ thống PHẢI gửi một thông báo riêng nêu rõ ngày đình chỉ và hệ quả cụ thể, cách thời điểm đình chỉ ít nhất khoảng thời gian đã cấu hình.
- BR-21.7: Khi doanh nghiệp đang khiếu nại một phần của hóa đơn, chu trình nhắc nợ PHẢI tạm dừng theo BR-27.3.
- BR-21.8: Thu tiền thành công ở bất kỳ bước nào PHẢI kết thúc ngay chu trình nhắc nợ và đưa đăng ký về trạng thái hiệu lực.
- BR-21.9: Chu trình nhắc nợ CHỈ khởi phát sau **ngày tới hạn** của hóa đơn (BR-18.9), không sớm hơn, kể cả khi một lần thu tự động đã thất bại trước đó. Lần thu thất bại trước ngày tới hạn được xử lý như một cảnh báo về phương thức thanh toán (BR-21.2) chứ không phải một bước nhắc nợ. Hóa đơn có tổng phải trả bằng không không bao giờ bước vào chu trình này (BR-20.11).

**Tiêu chí chấp nhận:**

- Không doanh nghiệp nào bị đình chỉ mà trước đó chưa nhận thông báo nêu rõ ngày và hệ quả.
- Một sự cố phía cổng thanh toán không đẩy bất kỳ doanh nghiệp nào tới gần đình chỉ hơn.
- Thanh toán thành công đưa doanh nghiệp trở lại bình thường ngay, không cần ai thao tác.
- Không có thư nhắc nợ nào được gửi cho một hóa đơn chưa tới hạn hoặc một hóa đơn tổng bằng không.

---

### FEAT-22 — Đình chỉ & khôi phục dịch vụ `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Hạn chế dịch vụ khi doanh nghiệp không thanh toán, theo cách gây áp lực thương mại đủ mạnh nhưng không phá hủy dữ liệu và không biến khách hàng của doanh nghiệp thành nạn nhân.

**Actor:** Tác vụ tự động, Kế toán nhà cung cấp, Chăm sóc khách hàng nhà cung cấp.

**Quy tắc nghiệp vụ:**

- BR-22.1: Đình chỉ KHÔNG ĐƯỢC xóa dữ liệu, và KHÔNG ĐƯỢC chặn doanh nghiệp xuất dữ liệu của chính họ. Giữ dữ liệu làm con tin không phải công cụ thu nợ hợp lệ, và nó biến một tranh chấp về tiền thành một tranh chấp pháp lý.
- BR-22.2: Danh sách những gì bị chặn khi đình chỉ PHẢI được liệt kê rõ, công bố cho doanh nghiệp từ trước, và nhất quán giữa mọi doanh nghiệp. Nhóm bị chặn là **các hành vi chủ động phát sinh chi phí mới**: gửi tin nhắn đi, chạy chiến dịch, chạy quy trình tự động phát sinh chi phí, thêm người dùng mới, mua thêm add-on.
- BR-22.3: Việc **tiếp nhận** hoạt động do khách hàng của doanh nghiệp khởi xướng PHẢI tiếp tục trong thời gian đình chỉ: tin nhắn khách hàng gửi tới vẫn được nhận và lưu, để khi doanh nghiệp thanh toán xong không mất một khoảng dữ liệu và không mất khách. Tiêu dùng phát sinh trong thời gian này vẫn được ghi nhận và tính vào hóa đơn kế tiếp — đây là "tính sau", không phải "miễn phí" (BR-32.2).
- BR-22.4: Khôi phục PHẢI diễn ra tự động ngay khi khoản nợ được thanh toán, không chờ thao tác thủ công của nhà cung cấp và không giới hạn theo giờ làm việc.
- BR-22.5: Nhà cung cấp PHẢI hoãn được đình chỉ cho một doanh nghiệp cụ thể. Mỗi lần hoãn PHẢI có lý do bắt buộc, có thời hạn, và để lại vết — hoãn vô thời hạn không phải một lựa chọn.
- BR-22.6: Sau thời hạn đình chỉ kéo dài đã công bố, dữ liệu chuyển sang diện chờ xóa. Việc này PHẢI có thông báo trước theo đúng thời hạn công bố và PHẢI cho doanh nghiệp cơ hội tải dữ liệu về. KHÔNG ĐƯỢC xóa dữ liệu trong im lặng.
- BR-22.7: Trong thời gian đình chỉ, Chủ workspace và Người phụ trách thanh toán PHẢI luôn đăng nhập được để xem hóa đơn, thanh toán và xuất dữ liệu.

**Tiêu chí chấp nhận:**

- Một doanh nghiệp bị đình chỉ 30 ngày rồi thanh toán vẫn thấy đầy đủ tin nhắn khách hàng gửi trong 30 ngày đó.
- Không có dữ liệu nào bị xóa mà doanh nghiệp không được báo trước và không được cơ hội tải về.
- Thanh toán lúc 2 giờ sáng ngày nghỉ vẫn khôi phục dịch vụ ngay.

---

### FEAT-23 — Ghi có, hoàn tiền & điều chỉnh hóa đơn `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Xử lý mọi trường hợp phải trả lại tiền hoặc giảm trừ nghĩa vụ đã phát hành, theo một con đường duy nhất và có kiểm soát.

**Actor:** Kế toán nhà cung cấp.

**Quy tắc nghiệp vụ:**

- BR-23.1: Mọi khoản giảm trừ sau khi hóa đơn đã phát hành PHẢI đi qua chứng từ ghi có tham chiếu tới hóa đơn gốc và tới dòng cụ thể được giảm.
- BR-23.2: **Ghi có vào số dư** và **hoàn tiền thật về phương thức thanh toán** là hai việc khác nhau, PHẢI phân biệt rõ trong hệ thống và trong thông báo gửi doanh nghiệp. Doanh nghiệp tưởng được hoàn tiền nhưng thực tế chỉ được ghi có là một trong những hiểu lầm gây khiếu nại thường xuyên nhất.
- BR-23.3: Ghi có hoặc hoàn tiền vượt ngưỡng giá trị đã cấu hình PHẢI có người phê duyệt khác người đề nghị.
- BR-23.4: Ghi có phát sinh từ bút toán đảo (FEAT-14) PHẢI dẫn ngược được về đúng những sự kiện gốc đã bị đảo.
- BR-23.5: Số dư có của doanh nghiệp PHẢI tự động bù trừ vào hóa đơn kế tiếp và PHẢI hiển thị cho doanh nghiệp, không phải một khoản ẩn chỉ nhà cung cấp biết.
- BR-23.6: Khi doanh nghiệp chấm dứt quan hệ mà còn số dư có, chính sách xử lý phần dư đó PHẢI được công bố trước, không quyết định theo từng trường hợp.
- BR-23.7: Một khoản hoàn tiền thật PHẢI có **thời hạn hoàn tất được công bố trước**, tính từ khi quyết định hoàn tiền được duyệt, và thông báo gửi doanh nghiệp PHẢI nêu mốc dự kiến cụ thể chứ không dừng ở việc báo đã duyệt. Nếu một phần thời gian nằm ngoài kiểm soát của nhà cung cấp vì phụ thuộc cổng thanh toán hoặc ngân hàng, phần đó PHẢI được nêu rõ là bao lâu và vì sao — doanh nghiệp không thấy tiền về mà không biết bao giờ về sẽ mở khiếu nại, và khiếu nại đó tốn kém hơn chính khoản hoàn. Con số cụ thể: xem Mục 7.2.
- BR-23.8: Ghi có vào số dư có hiệu lực ngay khi chứng từ ghi có được phát hành, không chờ chu kỳ tiếp theo, và số dư mới PHẢI hiển thị cho doanh nghiệp ngay tại thời điểm đó (BR-23.5). Đây là điểm khác biệt vận hành rõ nhất giữa ghi có và hoàn tiền, và là phần doanh nghiệp cần thấy để hiểu vì sao hai việc không giống nhau.

**Tiêu chí chấp nhận:**

- Mỗi chứng từ ghi có truy được về hóa đơn gốc, dòng gốc và căn cứ.
- Doanh nghiệp luôn biết mình đang được hoàn tiền hay được ghi có, và số dư hiện tại là bao nhiêu.
- Mọi thông báo về một khoản hoàn tiền đều nêu mốc dự kiến tiền về, không chỉ nêu đã duyệt.

---

### FEAT-24 — Hủy đăng ký & kết thúc quan hệ thương mại `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép doanh nghiệp chấm dứt quan hệ một cách rõ ràng, và đảm bảo phần dữ liệu của họ được xử lý đúng cam kết.

**Actor:** Chủ workspace, Kế toán nhà cung cấp.

**Quy tắc nghiệp vụ:**

- BR-24.1: Hủy có hiệu lực vào cuối kỳ đã trả tiền. Doanh nghiệp giữ nguyên quyền lợi tới hết kỳ đó. Hủy có hiệu lực ngay chỉ thực hiện khi doanh nghiệp chủ động yêu cầu và chấp nhận không hoàn phần đã trả.
- BR-24.2: Phí vượt hạn mức và các khoản phát sinh tới thời điểm hủy vẫn PHẢI được lập hóa đơn và thanh toán. Hủy KHÔNG ĐƯỢC là cách thoát khỏi nghĩa vụ đã phát sinh.
- BR-24.3: Sau khi hủy, doanh nghiệp PHẢI xuất được toàn bộ dữ liệu của mình trong một khoảng thời gian đã công bố trước.
- BR-24.4: Hệ thống PHẢI hỏi lý do hủy để phục vụ phân tích rời bỏ (BR-30.1), nhưng KHÔNG ĐƯỢC bắt buộc doanh nghiệp phải liên hệ ai đó, gọi điện, hay chờ phản hồi mới hủy được.
- BR-24.5: Doanh nghiệp PHẢI hủy được thao tác hủy trước thời điểm hiệu lực, và việc đó đưa đăng ký về nguyên trạng.
- BR-24.6: Đăng ký lại trong thời hạn còn giữ dữ liệu PHẢI khôi phục nguyên trạng dữ liệu và cấu hình, không phải tạo lại từ đầu.
- BR-24.7: Sau khi hết thời hạn giữ dữ liệu, việc xóa PHẢI được thực hiện thật và PHẢI có bằng chứng hoàn tất — trừ phần chứng từ tài chính bắt buộc lưu theo NFR-17, và trong phạm vi ranh giới mà NFR-18 đã nêu.
- BR-24.8: Đăng ký lại theo BR-24.6 khôi phục dữ liệu và cấu hình, nhưng **KHÔNG khôi phục điều kiện thương mại cũ**. Đây là một đăng ký mới: áp phiên bản gói đang được bán tại thời điểm đăng ký lại, mốc cắt chu kỳ tính theo ngày kích hoạt mới (BR-04.2), và các ưu đãi đã hết hạn KHÔNG sống lại (BR-08.7). Phiên bản gói cũ chỉ được chọn lại nếu nó còn đang bán — gói đã thu hồi thì không bán mới được, kể cả cho người từng dùng (BR-01.3). Điều kiện áp dụng PHẢI hiển thị rõ trước khi xác nhận, vì doanh nghiệp quay lại thường mặc định là giá cũ còn nguyên. Nếu muốn giữ giá cũ để mời khách quay lại thì đó là một ưu đãi có thời hạn theo FEAT-08, có người duyệt và có vết — không phải một hành vi mặc định của hệ thống.
- BR-24.9: Hủy đăng ký chính chấm dứt luôn mọi add-on định kỳ gắn với nó (BR-04.6); add-on KHÔNG ĐƯỢC tiếp tục tính phí sau khi đăng ký chính kết thúc. Add-on một lần đã dùng trong kỳ vẫn phải thanh toán theo BR-24.2, và phần hạn mức mua thêm chưa dùng hết KHÔNG được hoàn tiền, KHÔNG chuyển sang đăng ký mới khi đăng ký lại — trừ khi điều kiện của chính add-on đó ghi rõ, theo BR-07.2.
- BR-24.10: Sau khi đăng ký đã kết thúc — đã hủy hết kỳ đã trả, hoặc đã hết hạn — việc **tiếp nhận** hoạt động do khách hàng của doanh nghiệp khởi xướng vẫn PHẢI tiếp tục trong một thời hạn ân hạn đã công bố trước, để doanh nghiệp không mất một khoảng dữ liệu và có thời gian chuyển hướng khách hàng của mình. Hết thời hạn đó, các kênh kết nối PHẢI được **ngắt có thông báo trước**, đủ sớm để họ báo lại cho khách hàng của họ. Đây là ngoại lệ thứ hai và cuối cùng của nguyên tắc không-chặn-chiều-tiếp-nhận (BR-16.3), và nó tồn tại vì đúng lý do của BR-05.4b: khi quan hệ trả tiền đã kết thúc thì không còn nguồn thu nào bù cho chi phí mà nhà cung cấp thực trả cho nền tảng kênh, và giữ kênh mở vô thời hạn cho một tài khoản không còn trả tiền là một khoản lỗ không có trần. Ngắt kết nối có báo trước khác về bản chất với việc âm thầm nuốt tin nhắn — cách thứ hai bị cấm ở mọi trạng thái. Thời hạn ân hạn này PHẢI trùng với hoặc ngắn hơn thời hạn giữ dữ liệu ở BR-24.3, và độ dài cụ thể: xem Mục 7.2.
- BR-24.11: Ngắt kênh theo BR-24.10 KHÔNG ĐƯỢC xóa dữ liệu, KHÔNG ĐƯỢC chặn việc xuất dữ liệu (BR-24.3), và KHÔNG ĐƯỢC cản trở việc đăng ký lại theo BR-24.6. Khi doanh nghiệp đăng ký lại trong thời hạn giữ dữ liệu, các kênh PHẢI kết nối lại được, nhưng việc kết nối lại là một thao tác có chủ đích của doanh nghiệp chứ KHÔNG tự động khôi phục — kênh tự sống lại mà chủ workspace không biết là một rủi ro về phía họ, không phải một tiện lợi.

**Tiêu chí chấp nhận:**

- Doanh nghiệp hủy được trong sản phẩm mà không cần liên hệ ai.
- Không tồn tại doanh nghiệp đã kết thúc đăng ký nào còn kênh kết nối hoạt động quá thời hạn ân hạn đã công bố.
- Không doanh nghiệp nào bị ngắt kênh mà trước đó không nhận thông báo nêu rõ ngày ngắt và hệ quả.
- Doanh nghiệp hủy nhầm rồi khôi phục trong ngày không mất gì.
- Không tồn tại dữ liệu doanh nghiệp đã hủy quá thời hạn giữ mà chưa xóa.
- Không tồn tại add-on nào còn phát sinh phí sau khi đăng ký chính đã kết thúc.
- Doanh nghiệp đăng ký lại luôn nhìn thấy điều kiện thương mại sẽ áp dụng trước khi xác nhận, kể cả khi nó khác điều kiện cũ.

---

### FEAT-25 — Đa tiền tệ & giá theo vùng `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Bán được ở nhiều thị trường với đơn giá riêng cho từng thị trường, mà không tạo ra sự mập mờ về số tiền doanh nghiệp phải trả.

**Actor:** Quản trị nhà cung cấp, Kế toán nhà cung cấp.

**Quy tắc nghiệp vụ:**

- BR-25.1: Tiền tệ gắn với hồ sơ thanh toán và cố định trong vòng đời đăng ký (BR-03.3). Đổi tiền tệ đòi hỏi kết thúc đăng ký hiện tại và lập đăng ký mới.
- BR-25.2: Giá PHẢI niêm yết riêng cho từng tiền tệ như những phiên bản gói độc lập. KHÔNG ĐƯỢC quy đổi tự động theo tỷ giá tại thời điểm xuất hóa đơn — làm vậy khiến doanh nghiệp nhận hóa đơn khác nhau mỗi kỳ mà không hiểu vì sao, và khiến giá không còn là một cam kết.
- BR-25.3: Hóa đơn, thanh toán, ghi có và số dư của một doanh nghiệp luôn cùng một tiền tệ, không trộn.
- BR-25.4: Báo cáo doanh thu của nhà cung cấp quy về một tiền tệ báo cáo duy nhất, và PHẢI nêu rõ tỷ giá cùng ngày áp dụng để số liệu tái lập được.

**Tiêu chí chấp nhận:**

- Doanh nghiệp trả cùng một số tiền mỗi kỳ khi không đổi gói và không phát sinh phí vượt, bất kể tỷ giá biến động.
- Báo cáo doanh thu tổng hợp tái lập lại được từ các hóa đơn gốc và tỷ giá đã ghi.

---

## D. MINH BẠCH, VẬN HÀNH & TUÂN THỦ

### FEAT-26 — Cổng thanh toán tự phục vụ `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Nơi doanh nghiệp tự làm mọi việc liên quan tới quan hệ thương mại của mình mà không cần liên hệ nhà cung cấp.

**Actor:** Chủ workspace, Người phụ trách thanh toán.

**Quy tắc nghiệp vụ:**

- BR-26.1: Doanh nghiệp PHẢI tự làm được, tối thiểu: xem gói và điều kiện đang áp dụng, xem tiêu dùng kỳ hiện tại và lịch sử, xem và tải hóa đơn cùng chứng từ ghi có, đổi phương thức thanh toán, nâng gói, mua add-on, sửa thông tin xuất hóa đơn, chỉ định Người phụ trách thanh toán, hạ gói và hủy.
- BR-26.2: Hạ gói và hủy KHÔNG ĐƯỢC bị làm khó hơn nâng gói một cách có chủ đích — không thêm bước, không bắt liên hệ, không ẩn sâu hơn.
- BR-26.3: Chỉ các vai trò được phép theo Mục 5 thấy được thông tin tài chính. Thành viên thường KHÔNG ĐƯỢC thấy hóa đơn, số tiền hay tình trạng nợ, nhưng PHẢI nhận được thông báo rõ ràng khi một thao tác của họ bị chặn vì hạn mức (BR-16.6).
- BR-26.4: Hóa đơn tải về PHẢI ở định dạng dùng được cho kế toán và PHẢI kèm được bản dữ liệu chi tiết để đối chiếu bằng công cụ.
- BR-26.5: Mọi thao tác làm thay đổi nghĩa vụ tài chính PHẢI có bước xác nhận nêu rõ hệ quả bằng số tiền cụ thể trước khi thực hiện.

**Tiêu chí chấp nhận:**

- Không thao tác thương mại thông thường nào buộc doanh nghiệp phải liên hệ nhà cung cấp.
- Một thành viên thường không tìm thấy đường nào để nhìn thấy hóa đơn của doanh nghiệp mình.

---

### FEAT-27 — Truy vết từ hóa đơn xuống đối tượng gốc & xử lý khiếu nại `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Trả lời câu hỏi "vì sao tôi phải trả số tiền này" bằng dữ liệu cụ thể, và xử lý tranh chấp theo một quy trình có kết luận.

**Actor:** Người phụ trách thanh toán, Chăm sóc khách hàng nhà cung cấp, Kế toán nhà cung cấp.

**Luồng chính:**

1. Doanh nghiệp mở một dòng trên hóa đơn và xem danh sách đối tượng gốc tạo nên con số đó.
2. Nếu không đồng ý, doanh nghiệp mở khiếu nại cho chính dòng đó, nêu lý do.
3. Nhà cung cấp đối chiếu và kết luận theo BR-27.4.
4. Kết luận được thông báo kèm căn cứ; nếu có giảm trừ thì tạo chứng từ ghi có (FEAT-23).

**Quy tắc nghiệp vụ:**

- BR-27.1: Từ mỗi dòng tiêu dùng trên hóa đơn, người có quyền PHẢI xem được danh sách đối tượng gốc tạo nên con số đó, trong suốt thời hạn khiếu nại. Con số tổng không kèm khả năng đi xuống chi tiết là con số không kiểm chứng được.
- BR-27.2: Thời hạn khiếu nại một hóa đơn PHẢI được công bố trước và tính từ ngày phát hành.
- BR-27.3: Khi một khiếu nại đang mở, chu trình nhắc nợ và đình chỉ cho hóa đơn đó PHẢI tạm dừng tới khi có kết luận, trong khuôn khổ thời hạn và ngưỡng của BR-27.7. Phần hóa đơn không bị khiếu nại vẫn tới hạn bình thường. Đình chỉ một doanh nghiệp trong lúc chính nhà cung cấp chưa trả lời được khiếu nại của họ là cách nhanh nhất để mất khách và để tranh chấp leo thang.
- BR-27.4: Mỗi khiếu nại PHẢI kết thúc bằng đúng một trong ba kết luận, kèm căn cứ ghi lại được: giữ nguyên có giải thích, giảm trừ một phần, hoặc giảm trừ toàn phần.
- BR-27.5: Danh sách đối tượng gốc PHẢI tôn trọng quyền xem dữ liệu nghiệp vụ. Người phụ trách thanh toán KHÔNG đương nhiên có quyền đọc nội dung hội thoại — họ chỉ được thấy phần thông tin tối thiểu đủ để đối chiếu số lượng (định danh đối tượng, thời điểm, loại tiêu dùng). Quyền xem dữ liệu tài chính và quyền xem dữ liệu khách hàng là hai trục độc lập.
- BR-27.6: Tỷ lệ hóa đơn phát sinh khiếu nại và phân bố nguyên nhân PHẢI theo dõi được như một chỉ số chất lượng của chính hệ thống tính phí.
- BR-27.7: Mỗi khiếu nại PHẢI có một **thời hạn kết luận cam kết**, công bố trước cùng lúc với thời hạn khiếu nại (BR-27.2). Việc tạm dừng chu trình thu tiền ở BR-27.3 chỉ kéo dài trong thời hạn đó; quá hạn mà nhà cung cấp chưa kết luận thì PHẢI leo thang tới người có thẩm quyền chứ KHÔNG ĐƯỢC để treo vô hạn theo cả hai chiều. Khi số khiếu nại đang mở hoặc tỷ trọng giá trị bị khiếu nại của một doanh nghiệp vượt ngưỡng đã cấu hình, các khiếu nại tiếp theo của doanh nghiệp đó PHẢI chuyển sang xử lý có người soát thay vì tự động tạm dừng chu trình thu tiền. Không có giới hạn này thì mở một khiếu nại mỗi kỳ trở thành cách hoãn thanh toán vô thời hạn — đúng loại lỗ hổng mà BR-05.7 đã chặn ở phía dùng thử.

**Tiêu chí chấp nhận:**

- Mọi dòng trên mọi hóa đơn trong thời hạn khiếu nại đều đi xuống được danh sách chi tiết.
- Người phụ trách thanh toán không có quyền đọc hội thoại vẫn đối chiếu được số lượng nhưng không đọc được nội dung.
- Không doanh nghiệp nào bị đình chỉ trong lúc khiếu nại chưa được trả lời.
- Không có khiếu nại nào ở trạng thái mở quá thời hạn kết luận cam kết mà không được leo thang.

**Tham chiếu:** BR-27.5 liên quan [ADR-0001](../docs/adr/0001-group-policy-conflict-resolution.md) và [`omnichat-srs.md`](./omnichat-srs.md) NFR-8.

---

### FEAT-28 — Công cụ quản trị của nhà cung cấp `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Nơi nhân sự nhà cung cấp nhìn và can thiệp vào quan hệ thương mại với từng doanh nghiệp, trong giới hạn có kiểm soát.

**Actor:** Chăm sóc khách hàng nhà cung cấp, Kế toán nhà cung cấp, Quản trị nhà cung cấp.

**Quy tắc nghiệp vụ:**

- BR-28.1: Với một doanh nghiệp bất kỳ, hệ thống PHẢI hiển thị được toàn cảnh trong một chỗ: gói và điều kiện đang áp dụng, trạng thái đăng ký, tiêu dùng kỳ hiện tại, lịch sử hóa đơn và thanh toán, tình trạng nợ, khiếu nại đang mở, và lịch sử thay đổi thương mại.
- BR-28.2: Mọi thao tác can thiệp — gia hạn thủ công, cấp ghi có, miễn khoản phải trả, hoãn đình chỉ, đổi điều kiện giá, ghi nhận thanh toán thủ công — PHẢI bắt buộc nhập lý do và PHẢI để lại vết (FEAT-29). Không có thao tác can thiệp nào được phép không có lý do.
- BR-28.3: Nhân sự nhà cung cấp KHÔNG ĐƯỢC đọc nội dung nghiệp vụ của doanh nghiệp (nội dung hội thoại, dữ liệu khách hàng) từ công cụ này. Việc hỗ trợ về hóa đơn không đòi hỏi quyền đó, và gộp hai quyền lại tạo ra một rủi ro không cần thiết.
- BR-28.4: Thay đổi điều kiện giá cho một doanh nghiệp cụ thể PHẢI ở dạng phiên bản gói riêng có ngày hiệu lực (BR-08.3), KHÔNG ĐƯỢC sửa đè lên điều kiện đang áp dụng.
- BR-28.5: Quyền can thiệp PHẢI phân tách theo vai trò: người trả lời khách hàng không đương nhiên là người quyết định giảm tiền.
- BR-28.6: Công cụ PHẢI cho phép xem danh sách doanh nghiệp theo các tiêu chí vận hành cần thiết — đang nợ, sắp hết dùng thử, sắp đình chỉ, đang khiếu nại, tiêu dùng tăng bất thường — chứ không chỉ tra cứu từng doanh nghiệp một.

**Tiêu chí chấp nhận:**

- Không thao tác can thiệp nào tồn tại trong hệ thống mà không có lý do và người thực hiện.
- Nhân viên hỗ trợ trả lời được câu hỏi về hóa đơn mà không cần quyền đọc hội thoại.

---

### FEAT-29 — Nhật ký kiểm toán thay đổi thương mại `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Lưu vết mọi thay đổi ảnh hưởng tới số tiền một doanh nghiệp phải trả, để mọi con số đều truy được về một người và một thời điểm.

**Actor:** Kế toán nhà cung cấp, Quản trị nhà cung cấp, Chủ workspace.

**Quy tắc nghiệp vụ:**

- BR-29.1: **Nguyên tắc đóng.** Bất kỳ **quyết định thương mại** nào làm thay đổi số tiền một doanh nghiệp phải trả, hoặc thay đổi ai được xem/làm gì với dữ liệu tài chính, đều mặc định thuộc diện ghi vết — trừ thao tác xem trước/mô phỏng và thao tác chỉ đọc. Dùng nguyên tắc này thay cho một danh sách liệt kê tĩnh, để tính năng thương mại phát sinh về sau không bị bỏ sót.
- BR-29.1b: **Ranh giới của nguyên tắc đóng.** Quyết định thương mại thuộc diện này kể cả khi do tác vụ tự động thực hiện — chốt kỳ, phát hành hóa đơn, thu tiền, chuyển trạng thái đăng ký, đình chỉ, khôi phục, đảo tự động theo BR-14.6. Nhưng **việc ghi một Sự kiện tính phí không phải một quyết định thương mại** và KHÔNG thuộc diện này: nó là ghi nhận một việc đã xảy ra, tính toàn vẹn của nó được bảo đảm bằng cơ chế riêng — chỉ thêm mới (BR-09.5), đủ trường (BR-09.1), không mất và không trùng (FEAT-12), đối soát (FEAT-15). Không kẻ ranh giới này thì nguyên tắc đóng phủ luôn một dòng sự kiện tự động khối lượng lớn, và BR-29.2 sẽ mâu thuẫn trực tiếp với BR-09.3 và BR-32.1.
- BR-29.2: **Fail-closed.** Khi không ghi được vết của một quyết định thương mại, quyết định đó KHÔNG ĐƯỢC thực hiện. Một thay đổi về tiền không có vết là một thay đổi không bảo vệ được khi có tranh chấp. Fail-closed KHÔNG ĐƯỢC áp theo cách làm gián đoạn nghiệp vụ CRM hay chặn việc ghi nhận tiêu dùng: trường hợp không ghi nhận được đi theo BR-09.4 và BR-32.2 — ghi bù kèm cảnh báo, không phải chặn thao tác của người dùng cuối.
- BR-29.3: Mỗi bản ghi PHẢI nêu: ai thực hiện (kể cả khi là tác vụ tự động), khi nào, doanh nghiệp nào, thay đổi gì (giá trị trước và sau), và lý do khi thao tác thuộc diện bắt buộc nhập lý do.
- BR-29.4: Không vai trò nào — kể cả Quản trị nhà cung cấp — được sửa hoặc xóa nội dung nhật ký này.
- BR-29.5: Thời hạn lưu nhật ký PHẢI ít nhất bằng thời hạn lưu chứng từ kế toán theo quy định nơi phát hành hóa đơn, và ít nhất bằng thời hạn khiếu nại cộng biên an toàn.
- BR-29.6: Chủ workspace PHẢI xem được phần nhật ký liên quan tới chính doanh nghiệp mình — ai đổi gói, ai đổi phương thức thanh toán, ai chỉ định Người phụ trách thanh toán.

**Tiêu chí chấp nhận:**

- Với mọi khoản chênh lệch giữa hai hóa đơn liên tiếp, tra được nguyên nhân từ nhật ký.
- Không tồn tại quyết định thương mại nào làm thay đổi số tiền mà không có bản ghi tương ứng.
- Nhật ký thương mại ngừng ghi được không làm gián đoạn bất kỳ thao tác nghiệp vụ nào của người dùng cuối.

**Tham chiếu:** áp dụng lại nguyên tắc của [ADR-0003](../docs/adr/0003-permission-config-audit-log-fail-closed.md) cho phạm vi thương mại — nhưng phạm vi rộng hơn nguyên bản, nên BR-29.1b phải kẻ lại ranh giới: ADR-0003 áp fail-closed cho thao tác cấu hình quyền, vốn tần suất thấp và luôn có người ngồi trước màn hình; phạm vi thương mại còn chứa một dòng sự kiện tự động khối lượng lớn mà nguyên tắc đó không được phép chạm tới.

---

### FEAT-30 — Báo cáo doanh thu & sức khỏe đăng ký `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cung cấp cho nhà cung cấp bức tranh kinh doanh đủ để ra quyết định về giá, về sản phẩm và về khách hàng.

**Actor:** Kế toán nhà cung cấp, Quản trị nhà cung cấp.

**Quy tắc nghiệp vụ:**

- BR-30.1: Bộ chỉ số tối thiểu PHẢI có: doanh thu định kỳ hàng tháng, doanh thu tiêu dùng, tổng doanh thu, số doanh nghiệp đang trả phí, doanh thu bình quân trên một doanh nghiệp, tỷ lệ rời bỏ theo số lượng và theo doanh thu, doanh thu mở rộng từ nâng gói và từ phí vượt, tỷ lệ chuyển đổi từ dùng thử, và phân bố lý do hủy.
- BR-30.2: Doanh thu định kỳ và doanh thu tiêu dùng PHẢI là hai con số tách bạch, KHÔNG ĐƯỢC gộp vào một chỉ số duy nhất. Gộp lại làm chỉ số trở nên biến động theo mùa vụ sử dụng và mất hoàn toàn ý nghĩa dự báo — vốn là lý do duy nhất người ta dùng chỉ số này.
- BR-30.3: Giá vốn tiêu dùng phải trả cho bên thứ ba PHẢI ghi nhận được theo từng loại tiêu dùng, để tính được biên lợi nhuận theo loại tiêu dùng và theo doanh nghiệp. Một loại tiêu dùng hoặc một doanh nghiệp đang có biên âm PHẢI nhìn thấy được, không phải phát hiện ra vào cuối năm tài chính. Phần chi phí nhà cung cấp tự chịu theo BR-16.8 PHẢI là một con số riêng trong cùng báo cáo này, không gộp vào giá vốn chung — nó là khoản duy nhất phát sinh mà không có dòng doanh thu đối ứng, nên gộp lại là mất đúng thứ cần theo dõi.
- BR-30.4: Doanh thu ghi nhận theo **kỳ dịch vụ**, không theo thời điểm thu tiền. Một doanh nghiệp trả trước 12 tháng KHÔNG ĐƯỢC làm doanh thu của tháng thu tiền tăng vọt.
- BR-30.5: Báo cáo PHẢI loại trừ được tài khoản nội bộ, tài khoản trình diễn và tài khoản được miễn phí hoàn toàn (BR-09.6), và mặc định là loại trừ.
- BR-30.6: Mọi chỉ số PHẢI đi xuống được danh sách doanh nghiệp cấu thành nên nó. Một chỉ số không đi xuống được chi tiết thì không hành động được.
- BR-30.7: Số liệu báo cáo PHẢI tái lập được từ chứng từ gốc tại bất kỳ thời điểm nào trong quá khứ, không phải một ảnh chụp chỉ đúng lúc xem.

**Tiêu chí chấp nhận:**

- Trả lời được "loại tiêu dùng nào đang lỗ" mà không cần tính tay ngoài hệ thống.
- Doanh thu báo cáo của một kỳ trong quá khứ hôm nay xem lại vẫn ra đúng con số như khi kỳ đó vừa chốt.

---

### FEAT-31 — Thông báo về thanh toán `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Đảm bảo doanh nghiệp luôn biết trước những gì sắp xảy ra với tiền của họ.

**Actor:** Tác vụ tự động.

**Quy tắc nghiệp vụ:**

- BR-31.1: Danh sách sự kiện bắt buộc thông báo: sắp hết dùng thử, hóa đơn mới phát hành, **hóa đơn sắp tới hạn thanh toán** (BR-18.9), thu tiền thành công, thu tiền thất bại và từng bước nhắc nợ, sắp bị đình chỉ, đã bị đình chỉ, đã khôi phục, chạm ngưỡng tiêu dùng, chạm trần chi phí vượt, phương thức thanh toán sắp hết hiệu lực, thay đổi gói, ưu đãi sắp hết hạn, chứng từ ghi có được phát hành.
- BR-31.2: Thông báo về tiền KHÔNG ĐƯỢC trộn vào cùng luồng thông báo nghiệp vụ hằng ngày — chúng có người nhận khác, mức độ khẩn khác, và không được để bị bỏ qua cùng những thông báo thường nhật.
- BR-31.3: Doanh nghiệp KHÔNG ĐƯỢC tắt các thông báo về hóa đơn, về nhắc nợ và về đình chỉ. Các thông báo còn lại tắt được.
- BR-31.4: Mọi thông báo về một khoản tiền PHẢI nêu số tiền cụ thể, kỳ áp dụng, và hành động cần làm nếu có — không dừng ở việc báo rằng "có thay đổi".
- BR-31.5: Thông báo PHẢI dùng ngôn ngữ theo hồ sơ thanh toán của doanh nghiệp.
- BR-31.6: Mỗi thông báo đã gửi PHẢI lưu lại được để đối chiếu khi doanh nghiệp nói mình không nhận được.

**Tiêu chí chấp nhận:**

- Với mỗi lần đình chỉ, tra được chuỗi thông báo đã gửi trước đó và thời điểm từng cái.
- Không thông báo về tiền nào chỉ nói "có thay đổi" mà không nêu số tiền.

---

### FEAT-32 — Cách ly ảnh hưởng giữa lớp tính phí và nghiệp vụ `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Quy định cách hệ thống ứng xử khi lớp tính phí gặp sự cố, để một vấn đề về tiền không trở thành một vấn đề về phục vụ khách hàng — và ngược lại, để một sự cố không trở thành một khoản thất thu âm thầm.

**Actor:** Tác vụ tự động, Kế toán nhà cung cấp.

**Quy tắc nghiệp vụ:**

- BR-32.1: Sự cố của lớp tính phí KHÔNG ĐƯỢC làm gián đoạn nghiệp vụ CRM. Tin nhắn khách hàng vẫn được tiếp nhận, hội thoại vẫn xử lý được, người dùng vẫn làm việc được. Sự kiện tính phí được giữ lại tại CRM và chuyển giao khi khôi phục (FEAT-12).
- BR-32.2: Ngược lại, sự cố KHÔNG ĐƯỢC dẫn tới việc bỏ qua ghi nhận. Tình huống "hiện không tính phí được" PHẢI trở thành "tính phí sau", KHÔNG ĐƯỢC trở thành "miễn phí".
- BR-32.3: Khi không tra được hạn mức tại thời điểm cần quyết định, hệ thống ứng xử theo mức rủi ro chi phí của thao tác: thao tác chi phí thấp hoặc do khách hàng của doanh nghiệp khởi xướng thì **cho phép và ghi nhận** kèm cảnh báo vận hành; thao tác chi phí cao và do doanh nghiệp chủ động phát động hàng loạt (chiến dịch, gửi hàng loạt) thì **tạm hoãn** và thông báo, không thực hiện trong mù.
- BR-32.4: Trạng thái đăng ký và trạng thái đình chỉ PHẢI đọc được ngay bên trong CRM khi cần thực thi, không phụ thuộc vào việc hỏi hệ thống bên ngoài tại thời điểm đó (BR-04.5).
- BR-32.5: Mọi lần hệ thống rơi vào các tình huống ở BR-32.2 và BR-32.3 PHẢI được ghi nhận và tổng hợp lại được, để đội vận hành biết quy mô ảnh hưởng thay vì chỉ biết là "có sự cố".
- BR-32.6: Sau khi khôi phục, hệ thống PHẢI tự đối soát lại phạm vi thời gian bị ảnh hưởng (FEAT-15) trước khi kỳ liên quan được chốt.

**Tiêu chí chấp nhận:**

- Lớp tính phí ngừng hoạt động hoàn toàn trong nhiều giờ mà không có Agent nào bị ảnh hưởng khi phục vụ khách hàng.
- Sau sự cố, không có khoảng thời gian nào bị tính phí thiếu mà không ai biết.
- Một chiến dịch gửi hàng loạt không được phát động khi hệ thống không xác định được hạn mức.

**Tham chiếu:** [ADR-0004](../docs/adr/0004-billing-engine-boundary.md).

---
## 4. Yêu cầu phi chức năng

### 4.1 Bảo mật & Phân quyền

- **NFR-1 (Cách ly dữ liệu tài chính theo doanh nghiệp):** Hóa đơn, tiêu dùng, điều kiện giá và tình trạng nợ của một doanh nghiệp không bao giờ hiển thị hoặc trộn lẫn với dữ liệu của doanh nghiệp khác — ở màn hình, ở báo cáo, ở thông báo, ở tệp xuất ra.
- **NFR-2 (Không lưu dữ liệu thanh toán):** Thông tin thẻ và tài khoản thanh toán không được lưu trong phạm vi CRM dưới bất kỳ hình thức nào, kể cả dạng che một phần (BR-20.1).
- **NFR-3 (Quyền tài chính tách khỏi quyền vận hành):** Quyền xem dữ liệu tài chính và quyền xem dữ liệu nghiệp vụ là hai trục độc lập. Có quyền ở trục này không đương nhiên có quyền ở trục kia — theo cả hai chiều (BR-27.5, BR-28.3).
- **NFR-4 (Nhân sự nhà cung cấp không đọc dữ liệu nghiệp vụ của khách):** Không vai trò nào của nhà cung cấp đọc được nội dung hội thoại hay dữ liệu khách hàng của một doanh nghiệp thông qua các công cụ thương mại.
- **NFR-5 (Giá riêng của một doanh nghiệp là thông tin nhạy cảm):** Điều kiện giá thương lượng riêng của một doanh nghiệp không được lộ ra cho doanh nghiệp khác qua bất kỳ đường nào, kể cả qua báo cáo tổng hợp hay tệp xuất.

### 4.2 Toàn vẹn & Chính xác

- **NFR-6 (Không tính phí trùng):** Một việc đáng tính phí không bao giờ được tính hai lần, kể cả khi hệ thống thử lại nhiều lần vì không chắc chắn. Đây là điều kiện để hệ thống dám thử lại, và do đó là điều kiện của NFR-7.
- **NFR-7 (Không mất tiêu dùng):** Mọi việc đáng tính phí đã xảy ra đều phải đến được lớp thương mại — có thể chậm hơn bình thường, nhưng không được mất. Nếu việc chuyển giao bị chậm bất thường, hệ thống phải cảnh báo thay vì im lặng.
- **NFR-8 (Con số phải tái lập được):** Mọi con số trên một hóa đơn phải dựng lại được từ dữ liệu gốc tại bất kỳ thời điểm nào về sau, ra đúng kết quả cũ. Một hệ thống tính phí không tái lập được là một hệ thống không bảo vệ được khi có tranh chấp.
- **NFR-9 (Bất biến của chứng từ đã phát hành):** Hóa đơn và chứng từ ghi có đã phát hành không bao giờ thay đổi nội dung. Mọi điều chỉnh tạo ra chứng từ mới tham chiếu tới chứng từ cũ.
- **NFR-10 (Số liệu thiếu phải nhìn thấy được là đang thiếu):** Khi dữ liệu tiêu dùng chưa đầy đủ, mọi màn hình và báo cáo hiển thị nó phải nói rõ điều đó. Một con số thiếu mà người đọc tin là đủ gây thiệt hại lớn hơn một con số thiếu mà người đọc biết là thiếu.

### 4.3 Vận hành & Hiệu năng

- **NFR-11 (Lỗi tính phí không lan sang nghiệp vụ):** Sự cố ở lớp thương mại không làm chậm, không làm hỏng và không chặn bất kỳ thao tác nghiệp vụ nào của người dùng cuối (FEAT-32).
- **NFR-12 (Thay đổi thương mại không cần triển khai lại phần mềm):** Thêm gói, sửa giá, đổi hạn mức, đổi chính sách nhắc nợ, thêm loại tiêu dùng vào một gói — tất cả phải là thao tác cấu hình của người làm thương mại. Mỗi lần đổi giá phải chờ một đợt phát hành phần mềm là một hạn chế kinh doanh, không chỉ là bất tiện kỹ thuật.
- **NFR-13 (Chốt kỳ hoàn thành trong cửa sổ cam kết):** Việc chốt kỳ và phát hành hóa đơn cho toàn bộ doanh nghiệp phải hoàn thành trong cửa sổ thời gian đã cam kết với kế toán, và cửa sổ đó không được nới ra khi số doanh nghiệp tăng lên.
- **NFR-19 (Đường thanh toán và khôi phục luôn sẵn sàng):** Cổng tự phục vụ, luồng thanh toán và việc khôi phục dịch vụ sau khi thu được tiền phải sẵn sàng liên tục, không phụ thuộc giờ làm việc hay ngày làm việc của nhà cung cấp. Đây không phải một mong muốn vận hành mà là điều kiện để hai cam kết ở Mục 3 giữ được: BR-22.4 hứa khôi phục ngay khi thanh toán, BR-21.5 hứa doanh nghiệp trả được ngay từ chính thông báo nhận được. Một doanh nghiệp đang bị đình chỉ và muốn trả tiền vào tối thứ Bảy là tình huống bình thường, không phải ngoại lệ. Mức sẵn sàng cam kết cụ thể: xem Mục 7.2.
- **NFR-14 (Một doanh nghiệp không ảnh hưởng doanh nghiệp khác):** Khối lượng tiêu dùng tăng đột biến của một doanh nghiệp không được làm chậm việc ghi nhận, đối soát hay chốt kỳ của doanh nghiệp khác.

### 4.4 Minh bạch với người trả tiền

- **NFR-15 (Mọi con số giải thích được bằng ngôn ngữ khách hàng hiểu):** Mỗi khoản trên hóa đơn phải giải thích được bằng một câu mà người không làm kỹ thuật đọc hiểu, và đi xuống được danh sách chi tiết trong thời hạn khiếu nại.
- **NFR-16 (Không có thay đổi giá hồi tố và không có phí ẩn):** Không điều kiện thương mại nào được áp dụng ngược trở lại một kỳ đã qua, và không khoản nào xuất hiện trên hóa đơn mà doanh nghiệp không có cách biết trước.

### 4.5 Lưu trữ & Tuân thủ

- **NFR-17 (Chứng từ lưu đủ thời hạn luật định):** Hóa đơn, chứng từ ghi có, bằng chứng thanh toán và nhật ký thương mại được lưu ít nhất bằng thời hạn quy định nơi phát hành hóa đơn.
- **NFR-18 (Yêu cầu xóa dữ liệu cá nhân không xóa chứng từ bắt buộc lưu):** Khi thực hiện một yêu cầu xóa dữ liệu cá nhân, phần chứng từ tài chính bắt buộc lưu theo luật vẫn được giữ, nhưng phạm vi dữ liệu cá nhân còn lại trong đó phải được thu hẹp tới mức tối thiểu mà quy định cho phép, và ranh giới này phải được nêu rõ với người yêu cầu. Đây là điểm xung đột thật giữa quyền được xóa và nghĩa vụ lưu chứng từ — hệ thống phải xử lý có chủ đích, không để mỗi bên tự hiểu một cách.
- **NFR-18b (Yêu cầu xóa dữ liệu cá nhân áp cả lên Sự kiện tính phí):** Sự kiện tính phí không phải chứng từ tài chính, nhưng nó mang mã tham chiếu trỏ tới một đối tượng nghiệp vụ có thật và phải được lưu ít nhất bằng thời hạn khiếu nại (BR-12.6). Khi thực hiện một yêu cầu xóa dữ liệu cá nhân, phần dữ liệu định danh trong và quanh sự kiện tính phí phải được thu hẹp tới mức tối thiểu, nhưng **số lượng đơn vị tính phí và khả năng đối soát tổng của một kỳ không được mất** — nếu không, một yêu cầu xóa sẽ làm một hóa đơn đã phát hành không còn tái lập được (NFR-8) và một khiếu nại đang mở không còn căn cứ trả lời (BR-27.1). Ranh giới cụ thể giữa phần thu hẹp được và phần bắt buộc giữ phải được nêu rõ với người yêu cầu, giống cách NFR-18 làm với chứng từ tài chính.

### 4.6 Tổng hợp thời hạn lưu trữ

Bảng này không thêm yêu cầu nào — nó gom các mốc lưu trữ đang nằm rải rác lại một chỗ, vì mỗi mốc đều là một cam kết với bên ngoài và đều kéo theo chi phí vận hành. Cột cuối cho thấy phần lớn các mốc này **chưa có con số**, và chúng phụ thuộc lẫn nhau: thời hạn khiếu nại quyết định thời hạn lưu sự kiện, thời hạn lưu sự kiện quyết định khả năng truy vết.

| Đối tượng lưu trữ | Căn cứ | Thời hạn tối thiểu | Trạng thái |
| --- | --- | --- | --- |
| Hóa đơn, chứng từ ghi có, bằng chứng thanh toán | NFR-17 | Theo quy định nơi phát hành hóa đơn | Chưa chốt — phụ thuộc Mục 7.2 câu 3 |
| Nhật ký kiểm toán thương mại | BR-29.5 | ≥ thời hạn lưu chứng từ kế toán, và ≥ thời hạn khiếu nại + biên an toàn | Chưa chốt — phụ thuộc câu 3 và câu 10 |
| Sự kiện tính phí | BR-12.6 | ≥ thời hạn khiếu nại hóa đơn + biên an toàn | Chưa chốt — phụ thuộc câu 10 |
| Kết quả từng lần đối soát | BR-15.4, BR-15.5 | Đủ dài để nhìn được xu hướng lệch giữa các kỳ, và bao được thời hạn khiếu nại | **Chưa có quy tắc — cần chốt** |
| Nội dung từng thông báo đã gửi | BR-31.6 | Đủ dài để đối chiếu khi doanh nghiệp nói mình không nhận được, tối thiểu bao được chu trình nhắc nợ và thời hạn khiếu nại | **Chưa có quy tắc — cần chốt** |
| Điều kiện thương mại của phiên bản gói đã ký | BR-01.5 | Suốt thời gian đăng ký còn hiệu lực + thời hạn khiếu nại sau đó | Chưa chốt — phụ thuộc câu 10 |
| Lịch sử tiêu dùng doanh nghiệp xem được | BR-17.5 | 12 kỳ gần nhất, hoặc toàn bộ thời gian sử dụng nếu ngắn hơn | Đã chốt |
| Dữ liệu doanh nghiệp sau khi hủy | BR-24.3, BR-24.7 | Thời hạn đã công bố trước | Chưa chốt — Mục 7.2 câu 9 |
| Dữ liệu doanh nghiệp sau đình chỉ kéo dài | BR-22.6 | Thời hạn đã công bố trước | Chưa chốt — Mục 7.2 câu 9 |

---

## 5. Ma trận quyền truy cập tính năng

Ký hiệu: ✅ được phép · ⚪ chỉ được phép khi được cấp thêm quyền · ❌ không được phép · – không áp dụng. Bốn vai trò đầu thuộc doanh nghiệp; ba vai trò sau thuộc nhà cung cấp.

| Tính năng | Thành viên | Quản trị viên tenant | Chủ workspace | Người phụ trách thanh toán | CSKH nhà cung cấp | Kế toán nhà cung cấp | Quản trị nhà cung cấp |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Quản lý danh mục gói cước & phiên bản gói (FEAT-01) | ❌ | ❌ | ❌ | ❌ | ❌ | ⚪ | ✅ |
| Quản lý danh mục loại tiêu dùng (FEAT-02) | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| Sửa thông tin xuất hóa đơn (FEAT-03) | ❌ | ❌ | ✅ | ✅ | ❌ | ⚪ | ❌ |
| Chỉ định Người phụ trách thanh toán (BR-03.5) | ❌ | ❌ | ✅ | ❌ | ❌ | ❌ | ❌ |
| Đăng ký gói, nâng gói, mua add-on (FEAT-04, 06, 07) | ❌ | ❌ | ✅ | ✅ | ❌ | ⚪ | ❌ |
| Hạ gói, hủy đăng ký (FEAT-06, FEAT-24) | ❌ | ❌ | ✅ | ⚪ | ❌ | ⚪ | ❌ |
| Áp chiết khấu / giá thương lượng riêng (FEAT-08) | ❌ | ❌ | ❌ | ❌ | ❌ | ⚪ | ✅ |
| Thêm/vô hiệu hóa người dùng (ảnh hưởng phí — FEAT-11) | ❌ | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
| Xem tiêu dùng & ước tính tiền kỳ hiện tại (FEAT-17) | ❌ | ⚪ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Xem & tải hóa đơn, chứng từ ghi có (FEAT-26) | ❌ | ⚪ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Quản lý phương thức thanh toán (FEAT-20) | ❌ | ❌ | ✅ | ✅ | ❌ | ⚪ | ❌ |
| Ghi nhận thanh toán thủ công (BR-20.3) | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ⚪ |
| Phát hành hóa đơn, chạy thử đợt chốt kỳ (FEAT-18) | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ⚪ |
| Tạo bút toán đảo sự kiện (FEAT-14) | ❌ | ❌ | ❌ | ❌ | ⚪ | ✅ | ⚪ |
| Cấp ghi có / hoàn tiền (FEAT-23) | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ⚪ |
| Phê duyệt ghi có/ưu đãi vượt ngưỡng (BR-08.4, BR-23.3) | ❌ | ❌ | ❌ | ❌ | ❌ | ⚪ | ✅ |
| Mở khiếu nại hóa đơn (FEAT-27) | ❌ | ❌ | ✅ | ✅ | – | – | – |
| Kết luận khiếu nại (BR-27.4) | ❌ | ❌ | ❌ | ❌ | ⚪ | ✅ | ✅ |
| Đi xuống danh sách đối tượng gốc từ dòng hóa đơn (BR-27.1) | ❌ | ⚪ | ⚪ | ✅ | ✅ | ✅ | ✅ |
| Đọc nội dung nghiệp vụ của đối tượng gốc (BR-27.5) | – | – | ⚪ | ❌ | ❌ | ❌ | ❌ |
| Hoãn đình chỉ cho một doanh nghiệp (BR-22.5) | ❌ | ❌ | ❌ | ❌ | ⚪ | ✅ | ✅ |
| Cấu hình chính sách nhắc nợ & đình chỉ (FEAT-21, 22) | ❌ | ❌ | ❌ | ❌ | ❌ | ⚪ | ✅ |
| Xem đối soát tiêu dùng (FEAT-15) | ❌ | ❌ | ❌ | ❌ | ⚪ | ✅ | ✅ |
| Ghi nhận bù sự kiện (FEAT-13) | ❌ | ❌ | ❌ | ❌ | ❌ | ⚪ | ✅ |
| Xem báo cáo doanh thu toàn hệ thống (FEAT-30) | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ |
| Tra nhật ký thương mại của chính doanh nghiệp mình (BR-29.6) | ❌ | ❌ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Tra nhật ký thương mại toàn hệ thống (FEAT-29) | ❌ | ❌ | ❌ | ❌ | ⚪ | ✅ | ✅ |
| Xác nhận tiếp tục phát sinh khi chạm trần / duyệt lô vượt hạn mức (BR-16.5, BR-16.8, BR-16.9) | ❌ | ❌ | ✅ | ✅ | ❌ | ❌ | ❌ |
| Xem tình trạng tồn đọng & độ mới của số liệu tiêu dùng của chính doanh nghiệp (BR-17.3, NFR-10) | ❌ | ⚪ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Nhận thông báo bắt buộc về tiền — không tắt được (BR-31.3, BR-21.4) | ❌ | ❌ | ✅ | ✅ | – | – | – |
| Xuất dữ liệu của doanh nghiệp **trong lúc đang bị đình chỉ** (BR-22.1, BR-22.7) | ❌ | ⚪ | ✅ | ✅ | ❌ | ❌ | ❌ |
| Truy cập công cụ quản trị của nhà cung cấp (FEAT-28) | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ | ✅ |
| Cấu hình thuế & yêu cầu chứng từ theo quốc gia (FEAT-19) | ❌ | ❌ | ❌ | ❌ | ❌ | ⚪ | ✅ |

Năm nguyên tắc đọc ma trận này:

1. **Thành viên thường không thấy tiền.** Cột đầu tiên gần như toàn ❌ là có chủ đích: dữ liệu thương mại không phải thứ mọi người trong một doanh nghiệp cần thấy. Nhưng Thành viên vẫn PHẢI nhận được lý do rõ ràng khi thao tác của họ bị chặn vì hạn mức (BR-16.6) — không thấy tiền không có nghĩa là bị chặn trong mù.
2. **Quyền tài chính và quyền nghiệp vụ không kéo theo nhau.** Người phụ trách thanh toán đi xuống được danh sách đối tượng gốc để đối chiếu số lượng nhưng không đọc được nội dung; Chủ workspace đọc được nội dung nếu vai trò nghiệp vụ của họ cho phép, không phải vì họ là người trả tiền.
3. **Người trả lời khách hàng không phải người quyết định giảm tiền.** Chăm sóc khách hàng nhà cung cấp tra cứu được mọi thứ cần để trả lời, nhưng mọi thao tác làm thay đổi số tiền đều nằm ở Kế toán hoặc cần phê duyệt của Quản trị nhà cung cấp.
4. **Không ai xóa được vết của chính mình.** Không vai trò nào, kể cả Quản trị nhà cung cấp, sửa hay xóa được nội dung của FEAT-29.
5. **Tác vụ tự động không phải một vai trò được cấp quyền.** Mục 2.3 liệt kê tám vai trò, ma trận này chỉ có bảy cột — vì Tác vụ tự động không đứng ở đây. Nó không được cấp quyền, nó **thi hành chính sách đã cấu hình**, và mọi hành động của nó đều để lại vết như một chủ thể có danh tính (BR-29.1b, BR-04.4). Hệ quả ràng buộc: một hành động mà con người không được phép làm theo ma trận này thì KHÔNG ĐƯỢC thực hiện gián tiếp bằng cách để một tác vụ tự động làm thay. Tác vụ tự động mở rộng phạm vi *thời điểm* hành động (2 giờ sáng, ngày nghỉ — BR-22.4, NFR-19), không mở rộng phạm vi *thẩm quyền*.

---

## 6. Kịch bản chấp nhận tổng hợp

1. **Hóa đơn đầu tiên không phải một bất ngờ:** Một doanh nghiệp hết hạn dùng thử, chuyển sang gói trả phí. Trước ngày trừ tiền đầu tiên họ đã nhận thông báo nêu rõ ngày và số tiền; hóa đơn phát hành ra đúng bằng số đó, và mọi dòng trên hóa đơn đều đi xuống được danh sách chi tiết.
2. **Khách hàng của doanh nghiệp không phải chịu hậu quả của tranh chấp thương mại:** Một doanh nghiệp dùng hết hạn mức hội thoại giữa kỳ. Khách hàng của họ vẫn nhắn tin tới và Agent vẫn nhận được đầy đủ; điều bị chặn là việc doanh nghiệp chủ động phát động chiến dịch gửi hàng loạt. Doanh nghiệp nhận cảnh báo, mua thêm hạn mức trong vài phút và tiếp tục bình thường.
3. **Tin nhắn rác không trở thành hóa đơn:** Một đợt tin nhắn rác tạo ra hàng trăm hội thoại trong một đêm. Agent đánh dấu spam; các hội thoại đó tự động bị đảo khỏi lượng tiêu dùng của kỳ, không cần ai liên hệ nhà cung cấp. Hóa đơn cuối kỳ không chứa chúng, và doanh nghiệp vẫn xem được quy mô spam mình đã nhận.
4. **Spam phát hiện muộn vẫn được xử lý đúng:** Cùng tình huống trên nhưng việc đánh dấu spam diễn ra sau khi hóa đơn đã phát hành. Hóa đơn không bị sửa; hệ thống tạo chứng từ ghi có dẫn ngược được tới đúng các hội thoại đó, và doanh nghiệp được thông báo rõ là ghi có chứ không phải hoàn tiền.
5. **Gián đoạn kết nối không tạo thất thu cũng không tạo phí trùng:** Lớp thương mại không nhận được sự kiện trong sáu giờ. Trong suốt thời gian đó Agent làm việc bình thường, không ai nhận ra có sự cố. Khi khôi phục, toàn bộ sự kiện tồn đọng được chuyển giao; đối soát sau đó cho kết quả khớp, không có đơn vị nào bị tính hai lần.
6. **Sự kiện cuối kỳ vào đúng kỳ của nó:** Một hội thoại phát sinh lúc 23h55 ngày cuối kỳ, sự kiện tới lớp thương mại lúc 00h20 hôm sau. Vì còn trong thời hạn chốt kỳ, nó vào hóa đơn của kỳ vừa kết thúc, không đẩy sang kỳ sau.
7. **Chốt kỳ dừng lại khi số liệu chưa sạch:** Đợt chốt kỳ phát hiện một doanh nghiệp có chênh lệch đối soát vượt ngưỡng. Hóa đơn của riêng doanh nghiệp đó bị giữ lại kèm cảnh báo cho kế toán; các doanh nghiệp còn lại phát hành bình thường. Không có hóa đơn sai nào rời khỏi hệ thống.
8. **Nâng gói giữa kỳ không tạo ra tranh cãi về tiền:** Doanh nghiệp nâng gói vào ngày 15 của chu kỳ. Màn hình xem trước nêu rõ số tiền chênh lệch phải trả ngay, hạn mức mới của phần còn lại của kỳ, và mốc cắt kỳ giữ nguyên. Hóa đơn sau đó khớp đúng con số đã xem trước.
9. **Hạ gói không tạo kẽ hở:** Doanh nghiệp nâng lên gói cao vào đầu tháng, dùng gần hết hạn mức lớn, rồi hạ về gói cũ vào cuối tháng. Việc hạ chỉ có hiệu lực từ kỳ sau và không hoàn tiền, nên không có lợi ích tài chính nào phát sinh từ thao tác này.
10. **Một quy trình tự động chạy sai không tạo ra hóa đơn khổng lồ:** Một cấu hình tự động hóa bị lỗi bắt đầu phát sinh tiêu dùng liên tục. Khi chạm trần chi phí vượt của kỳ, hệ thống dừng lại, cảnh báo doanh nghiệp và yêu cầu xác nhận trước khi tiếp tục. Doanh nghiệp phát hiện lỗi và sửa cấu hình; khoản phát sinh dừng ở mức trần, không nhân lên tới cuối kỳ.
11. **Đình chỉ không phá hủy quan hệ khách hàng của doanh nghiệp:** Một doanh nghiệp thanh toán thất bại và đi hết chu trình nhắc nợ rồi bị đình chỉ. Trong 20 ngày đình chỉ, tin nhắn khách hàng gửi tới vẫn được nhận và lưu; doanh nghiệp không gửi đi được. Khi thanh toán vào lúc 2 giờ sáng, dịch vụ khôi phục ngay và Agent thấy đủ toàn bộ tin nhắn của 20 ngày đó.
12. **Khiếu nại đang mở thì không bị đình chỉ:** Doanh nghiệp khiếu nại một dòng phí vượt trên hóa đơn. Chu trình nhắc nợ và đình chỉ cho hóa đơn đó tạm dừng cho tới khi có kết luận; kết luận là giảm trừ một phần, hệ thống tạo chứng từ ghi có và chu trình tiếp tục cho phần còn lại.
13. **Người phụ trách thanh toán đối chiếu được mà không đọc được nội dung:** Kế toán của một doanh nghiệp mở dòng "hội thoại" trên hóa đơn, thấy danh sách hội thoại kèm thời điểm và định danh, đếm ra đúng con số bị tính — nhưng không đọc được nội dung trao đổi của bất kỳ hội thoại nào, vì họ không có vai trò nghiệp vụ tương ứng.
14. **Doanh nghiệp tự rời đi mà không mất dữ liệu:** Một doanh nghiệp quyết định ngừng dùng. Họ hủy ngay trong sản phẩm, chọn lý do, giữ nguyên quyền lợi tới hết kỳ đã trả, và tải toàn bộ dữ liệu về trong thời hạn đã công bố. Ba tháng sau họ quay lại trong thời hạn giữ dữ liệu và mọi thứ còn nguyên.
15. **Nhà cung cấp phát hiện được sản phẩm nào đang lỗ:** Báo cáo cho thấy doanh thu tiêu dùng của một loại tiêu dùng tăng mạnh nhưng biên lợi nhuận của chính loại đó âm, vì giá vốn trả cho bên thứ ba cao hơn đơn giá đang bán. Vấn đề được phát hiện trong kỳ, không phải vào cuối năm tài chính.
16. **Đổi giá không đụng tới khách cũ:** Nhà cung cấp tăng giá niêm yết của một gói. Các doanh nghiệp đã đăng ký trước đó vẫn nhận đúng hóa đơn theo điều kiện họ đã ký, và xem lại được nguyên văn điều kiện đó. Chỉ doanh nghiệp đăng ký mới chịu mức giá mới.
17. **Lô gửi hàng loạt không bao giờ đi được một nửa:** Doanh nghiệp phát động một chiến dịch 10.000 tin nhắn mẫu trong khi hạn mức và trần chi phí vượt còn lại chỉ đủ cho 4.000. Hệ thống đối chiếu toàn bộ khối lượng của lô trước khi khởi động, nêu con số cụ thể và dừng lại chờ quyết định. Doanh nghiệp mua thêm hạn mức rồi chạy trọn lô — không có nhóm khách hàng nào nhận được nửa chiến dịch.
18. **Đợt spam làm nhà cung cấp tốn tiền, nhưng khoản đó nhìn thấy được:** Một đợt tin nhắn rác đẩy tiêu dùng hội thoại của một doanh nghiệp vượt trần chi phí vượt của kỳ. Tin nhắn khách hàng vẫn được tiếp nhận vì không được phép chặn chiều này; phần vượt trần không vào hóa đơn của doanh nghiệp vì họ chưa xác nhận; và toàn bộ khoản nhà cung cấp thực trả cho nền tảng kênh hiện ra như một con số riêng trong báo cáo giá vốn ngay trong kỳ — không phải một khoản lỗ phát hiện vào cuối năm.
19. **Khách lớn chuyển khoản thiếu vài đơn vị tiền không bị coi là con nợ:** Một doanh nghiệp chuyển khoản trả hóa đơn nhưng ngân hàng trung gian trừ phí, số về tới nơi hụt một khoản nhỏ. Khoản hụt nằm trong ngưỡng dung sai nên hóa đơn được ghi nhận đã thanh toán đủ, phần hụt vào một khoản chi phí có vết, và không có thư nhắc nợ nào được gửi. Nếu việc này lặp lại nhiều kỳ, kế toán nhận được cảnh báo để xử lý với khách — chứ hệ thống không tự nới thành một mức giảm giá ngầm.
20. **Mọi khoản chênh lệch đều truy được về một người:** Kế toán so hai hóa đơn liên tiếp của một doanh nghiệp và thấy chênh nhau một khoản. Tra nhật ký thương mại ra ngay: một nhân viên đã áp một ưu đãi vào ngày cụ thể, với lý do cụ thể, và người phê duyệt là ai.
21. **Trần chi phí vượt không bị vô hiệu chỉ bằng một lần bấm đồng ý:** Một cấu hình tự động hóa lỗi đẩy chi phí chạm trần của kỳ. Doanh nghiệp xác nhận nâng trần lên một mức mới do họ tự nhập để công việc không đứng lại. Cấu hình vẫn còn lỗi và chi phí chạm luôn mức mới đó trong cùng ngày — hệ thống dừng lại lần nữa và hỏi lại. Doanh nghiệp nhận ra bất thường ở lần hỏi thứ hai và đi tìm nguyên nhân, thay vì phát hiện vào cuối kỳ khi nhìn hóa đơn.
22. **Quan hệ kết thúc thì chi phí cũng kết thúc, nhưng không đột ngột:** Một doanh nghiệp hủy đăng ký và hết kỳ đã trả. Trong thời hạn ân hạn đã công bố, khách hàng của họ vẫn nhắn tới được và dữ liệu vẫn nguyên; họ nhận thông báo nêu rõ ngày các kênh sẽ ngắt. Tới ngày đó kênh ngắt, nhưng dữ liệu vẫn tải về được và họ vẫn đăng ký lại được trong thời hạn giữ dữ liệu. Không có tin nhắn nào bị nuốt im lặng ở bất kỳ thời điểm nào của quá trình.
23. **Số ghế của một kỳ ba năm trước dựng lại được:** Kế toán cần đối chiếu một hóa đơn cũ. Con số người dùng bị tính phí của kỳ đó được dựng lại từ mốc đầu kỳ cộng chuỗi thay đổi trạng thái người dùng, ra đúng con số đã in trên hóa đơn — kể cả với một kỳ mà doanh nghiệp không bật hay tắt người dùng nào.

---

## 7. Giới hạn hiện tại & vấn đề tồn đọng

Mục này nêu hai loại nội dung: **ranh giới phạm vi đã chốt** (để tránh kỳ vọng sai khi tư vấn khách hàng và khi lập kế hoạch) và **các câu hỏi chưa có quyết định** (cần chốt trước hoặc trong lúc xây dựng). Những điểm đã chốt phương án nhưng chưa xây dựng nằm ở Mục 3 dưới nhãn `[Yêu cầu mới]`, không lặp lại ở đây.

### 7.1 Ranh giới phạm vi đã chốt

1. **Một doanh nghiệp là một người mua.** Mô hình nhiều doanh nghiệp gộp vào một hóa đơn — tập đoàn nhiều công ty con, đơn vị dịch vụ khách hàng thuê ngoài phục vụ nhiều khách hàng doanh nghiệp trên cùng hệ thống — nằm ngoài phạm vi (BR-03.1). Đây là một giới hạn đáng lưu ý vì [`omnichat-srs.md`](./omnichat-srs.md) đã xác định nhóm khách hàng thuê ngoài là một phân khúc mục tiêu; nếu bán cho nhóm đó, mô hình người mua phải mở rộng trước.
2. **Chưa có bán qua đại lý/đối tác.** Không có khái niệm hoa hồng, không có bảng giá riêng theo đại lý, không có hóa đơn phát hành thay mặt bên thứ ba.
3. **Chưa có định giá theo bậc thang.** Mô hình giá được hỗ trợ là phí thuê bao cố định cộng hạn mức bao gồm cộng đơn giá vượt phẳng. Đơn giá giảm dần theo sản lượng, giá theo bậc, giá theo gói dung lượng mua sẵn chưa nằm trong phạm vi.
4. **Chưa có số dư trả trước dạng ví.** Doanh nghiệp không nạp trước một khoản để trừ dần; số dư có chỉ phát sinh từ ghi có và từ trả thừa (BR-20.7, BR-23.5).
5. **Chưa có tự phục vụ hợp đồng năm và báo giá.** Hợp đồng nhiều năm, cam kết sản lượng tối thiểu, quy trình báo giá/duyệt mua của phía khách hàng đều xử lý ngoài hệ thống.
6. **Chưa có thuế nhiều cấp trong một quốc gia.** Mô hình thuế hỗ trợ một mức theo quốc gia người mua; các thị trường có thuế theo bang/tỉnh chồng lên nhau chưa nằm trong phạm vi (FEAT-19).
7. **Chưa có dự báo chi phí cho doanh nghiệp.** Doanh nghiệp xem được tiêu dùng hiện tại và lịch sử (FEAT-17) nhưng hệ thống không dự báo hóa đơn cuối kỳ dựa trên xu hướng.

### 7.2 Câu hỏi chưa có quyết định

Các câu 1 → 4 và câu 13 là các quyết định chặn: chưa chốt thì không thiết kế được phần lõi. Các câu còn lại chốt được song song với việc xây dựng.

| # | Câu hỏi cần quyết định | Vì sao chưa quyết được là rủi ro | Liên quan |
| --- | --- | --- | --- |
| 1 | **Một "hội thoại tính phí" là một phiên trên một kênh, hay một vụ việc của một con người?** BR-10.2 hiện tạm lấy định nghĩa thứ nhất vì đó là hành vi hệ thống hiện tại. | Đây là chính câu hỏi còn treo ở [`omnichat-srs.md`](./omnichat-srs.md) Mục 7.2 câu 1, và giờ nó có hệ quả tiền bạc trực tiếp: cùng một khách hàng nhắn trên hai kênh sẽ bị tính hai đơn vị. Nếu Omnichat về sau đổi sang mô hình vụ việc, đơn giá, hạn mức của mọi gói đã bán và mọi số liệu so sánh giữa các kỳ đều phải làm lại. Đổi mã loại tiêu dùng theo BR-02.2 là bắt buộc trong trường hợp đó. | BR-10.2, BR-02.2, FEAT-01 |
| 2 | **Đơn vị nhà cung cấp bị nền tảng kênh tính tiền có trùng với đơn vị bán ra cho doanh nghiệp không, và ai chịu phần chênh?** | BR-10.4 buộc phải trả lời nhưng câu trả lời chưa có. Nếu nền tảng tính theo cửa sổ hội thoại còn hệ thống bán theo từng tin nhắn mẫu, mỗi đơn vị bán ra có thể sinh lãi hoặc lỗ tùy mật độ sử dụng của từng doanh nghiệp — và không ai phát hiện ra cho tới khi đối chiếu hóa đơn của nền tảng với doanh thu. | BR-10.4, BR-30.3 |
| 3 | **Hóa đơn phát hành ở quốc gia nào, và quốc gia đó yêu cầu gì?** Cần chốt pháp nhân phát hành, danh sách thị trường bán, và với mỗi thị trường là yêu cầu hóa đơn điện tử, thuế suất, thời hạn lưu chứng từ. | Đây là ràng buộc tiên quyết, không phải chi tiết hoàn thiện: một số thị trường yêu cầu hóa đơn được cơ quan thuế cấp mã trước khi gửi khách (BR-19.3), điều này chèn thêm một bước bắt buộc vào giữa "chốt kỳ" và "thu tiền" và làm đổi cả luồng của FEAT-18 → FEAT-21. Phát hiện muộn thì phải làm lại phần lõi. | FEAT-19, BR-18.5, NFR-17 |
| 4 | **Bên nào đứng tên bán hàng với người mua cuối?** Nhà cung cấp tự phát hành hóa đơn, hay một bên trung gian đứng tên và chịu nghĩa vụ thuế. | Quyết định này chi phối FEAT-19 (ai kê khai thuế), FEAT-23 (ai hoàn tiền), FEAT-20 (dòng tiền chảy qua đâu) và cả nội dung hiển thị trên hóa đơn. Chọn sau khi đã xây dựng thì phải sửa cả ba. | FEAT-19, FEAT-20, FEAT-23 |
| 5 | **Số người dùng tính theo mức cao nhất trong kỳ hay theo tỷ lệ thời gian?** BR-11.2 tạm chọn mức cao nhất. | Cách hiện tại đơn giản và chống lách, nhưng một khách hàng lớn thêm 30 người dùng vào ngày cuối kỳ sẽ trả trọn kỳ cho cả 30 — điều mà đội bán hàng gần như chắc chắn sẽ phải thương lượng lại. Cần chốt xem có mở cơ chế tính theo tỷ lệ thời gian cho một phân khúc hay không, và nếu có thì đó là ngoại lệ theo hợp đồng hay là hành vi chuẩn. **Phần cơ chế đo lường đã được chốt** ở BR-11.8, BR-11.8b và BR-11.9 (sự kiện hai chiều cộng mốc đầu kỳ, dựng lại được cho mọi kỳ quá khứ) — câu hỏi còn lại thuần túy là một lựa chọn thương mại, và nó đổi được mà không phải đổi cách đo. | BR-11.2, BR-11.8b, FEAT-08 |
| 6 | **Trần chi phí vượt mặc định là bao nhiêu?** BR-16.5 buộc phải có một trần nhưng chưa có con số. | Đặt quá thấp thì doanh nghiệp đang dùng bình thường bị dừng giữa chừng; quá cao thì trần không bảo vệ được ai. Con số này là một cam kết với khách hàng, không phải một tham số kỹ thuật đặt đại rồi chỉnh sau. | BR-16.5 |
| 7 | **Chính sách hoàn tiền công bố ra bên ngoài là gì?** Có hoàn tiền khi hủy giữa kỳ không, có thời hạn dùng thử hoàn tiền không. | BR-24.1 hiện chọn không hoàn tiền phần đã trả. Đây là một cam kết thương mại phải công bố trước khi bán, và đổi về sau thì phải áp dụng cho cả khách cũ. | BR-24.1, FEAT-23 |
| 8 | **Dùng thử có bắt buộc khai báo phương thức thanh toán từ đầu không?** | Bắt buộc thì tỷ lệ đăng ký thử giảm nhưng tỷ lệ chuyển đổi tăng và ít tài khoản rác hơn; không bắt buộc thì ngược lại. Quyết định này đổi cả luồng FEAT-05 lẫn cách chống lạm dụng ở BR-05.7. | FEAT-05, BR-05.6 |
| 9 | **Thời hạn giữ dữ liệu sau khi hủy và sau khi đình chỉ kéo dài là bao lâu?** | BR-22.6 và BR-24.3 đều viện dẫn một thời hạn "đã công bố" nhưng thời hạn đó chưa tồn tại. Chưa có con số thì không viết được điều khoản dịch vụ, và cũng không có mốc để xóa dữ liệu thật. | BR-22.6, BR-24.3, BR-24.7 |
| 10 | **Thời hạn khiếu nại một hóa đơn là bao lâu, thời hạn kết luận một khiếu nại là bao lâu, và ngưỡng nào thì chuyển sang xử lý có người soát?** | Con số thứ nhất quyết định trực tiếp thời gian phải giữ dữ liệu chi tiết đủ để truy vết (BR-12.6, BR-27.1) — tức là quyết định một phần chi phí vận hành; đặt sau khi đã xây dựng thì có nguy cơ dữ liệu đã bị dọn trước khi khiếu nại tới. Hai con số sau quyết định BR-27.7 có hiệu lực thật hay chỉ là một câu chữ: thiếu chúng thì việc tạm dừng thu tiền ở BR-27.3 không có điểm kết thúc, và mở khiếu nại đều đặn trở thành cách hoãn thanh toán vô thời hạn. | BR-27.2, BR-27.7, BR-12.6, BR-29.5 |
| 11 | **Tiêu dùng phát sinh từ thao tác hỗ trợ của nhân sự nhà cung cấp có tính phí không?** | BR-10.7 buộc phải quyết định rõ thay vì để mặc định. Tính phí thì khách phàn nàn vì bị tính cho việc mình không làm; không tính thì cần cơ chế phân biệt đáng tin, nếu không đây là một đường lách. | BR-10.7 |
| 12 | **Doanh nghiệp đang trong diện tạm dừng xóa theo yêu cầu pháp lý có được đình chỉ và xóa dữ liệu theo chu trình nợ không?** | Omnichat đã có khái niệm Tạm dừng xóa theo yêu cầu pháp lý, nhưng chưa xác định nó tương tác thế nào với chu trình đình chỉ và xóa dữ liệu vì nợ. Hai chính sách này có thể xung đột trực tiếp trên cùng một tập dữ liệu. | BR-22.6, NFR-18, [`omnichat-srs.md`](./omnichat-srs.md) |
| 13 | **Có bán gói theo chu kỳ khác tháng không, và đổi chu kỳ giữa chừng thì xử lý thế nào?** BR-01.1 cho phép một phiên bản gói khai báo chu kỳ riêng, nên gói năm bán được ngay cả khi Mục 7.1 câu 5 đã loại trừ phần *tự phục vụ* hợp đồng năm. | Đây là quyết định chặn vì BR-04.2 không định nghĩa được khi độ dài kỳ thay đổi: không thể vừa giữ nguyên mốc cắt kỳ vừa nhân kỳ lên 12 lần, nên chuyển tháng→năm chưa xếp được vào chiều nâng (BR-06.1) hay chiều hạ (BR-06.2). Nó còn kéo theo BR-24.1 — hủy giữa hợp đồng năm nghĩa là giữ quyền lợi tới 11 tháng còn lại — nên phải chốt cùng chính sách hoàn tiền ở câu 7. Chọn sau khi đã xây dựng thì phải sửa lại lõi cắt kỳ. | BR-04.2, BR-06.1, BR-06.2, BR-24.1, Mục 7.1 câu 5 |
| 14 | **Khi phần chi phí nhà cung cấp tự chịu theo BR-16.8 vượt ngưỡng cấu hình thì làm gì?** Ngưỡng là bao nhiêu, và biện pháp tiếp theo là gì — thương lượng, đổi điều kiện gói, hay một biện pháp tạm thời có thông báo. | BR-16.8 buộc phải đo và cảnh báo khoản này, nhưng chưa nói làm gì với nó. Để trống thì con số vẫn hiện ra hằng ngày mà không ai có thẩm quyền hành động, và trần của BR-16.5 chỉ còn tác dụng với một nửa danh mục tiêu dùng. Đây cũng là chỗ chính sách chống lạm dụng chiều tiếp nhận phải nằm, nếu có. | BR-16.8, BR-16.5, BR-30.3 |
| 15 | **Thời hạn ân hạn tiếp nhận sau khi hết dùng thử là bao lâu?** BR-05.4b cho phép ngắt kênh có thông báo sau thời hạn này nhưng chưa có con số. | Đây là ngoại lệ duy nhất của nguyên tắc không-chặn-chiều-tiếp-nhận (BR-16.3), nên độ dài của nó là một cam kết công bố ra bên ngoài, không phải tham số kỹ thuật. Quá ngắn thì doanh nghiệp đang cân nhắc mua bị mất khách; quá dài thì nhà cung cấp trả tiền nền tảng kênh vô thời hạn cho một tài khoản không bao giờ trả tiền. | BR-05.4b, BR-22.3, BR-16.3 |
| 16 | **Khoảng ân hạn thanh toán của từng phương thức là bao nhiêu ngày?** BR-18.9 buộc phải có nhưng chưa có con số. | Đây là mốc khởi phát của toàn bộ FEAT-21 và FEAT-22, nên thiếu nó thì hai tính năng đó không viết được test. Nó cũng là một cam kết trong hợp đồng với khách doanh nghiệp trả sau, không phải tham số điều chỉnh sau. Cần chốt riêng cho thanh toán tự động và cho chuyển khoản, và cân nhắc mốc tính khi hóa đơn phải chờ cơ quan thuế cấp mã (BR-19.3). | BR-18.9, FEAT-21, BR-19.3 |
| 17 | **Ngưỡng dung sai thanh toán là bao nhiêu, theo từng tiền tệ?** | BR-20.8 cần ngưỡng này để một khách chuyển khoản bị trừ phí trung gian không rơi vào chu trình nhắc nợ. Đặt quá thấp thì quy tắc vô dụng; quá cao thì nó thành một mức giảm giá ngầm mà không ai duyệt — BR-20.9 chặn hướng lạm dụng nhưng không thay được con số. | BR-20.8, BR-20.9 |
| 18 | **Có mở cơ chế cộng dồn ưu đãi không, và cho những loại nào?** BR-08.8 mặc định loại trừ lẫn nhau. | Mặc định hiện tại an toàn và giải thích được cho khách, nhưng đội kinh doanh sẽ cần cộng dồn trong ít nhất một tình huống — ưu đãi theo chiến dịch chồng lên chiết khấu hợp đồng. Chốt sớm thì đây là một khai báo trên từng ưu đãi; chốt muộn thì thành một loạt ngoại lệ xử lý tay ngoài hệ thống. | BR-08.8, BR-08.9, BR-08.4 |
| 19 | **Thời hạn hoàn tất một khoản hoàn tiền là bao lâu?** BR-23.7 buộc phải công bố trước nhưng chưa có con số, và cần tách phần nằm trong kiểm soát của nhà cung cấp với phần phụ thuộc cổng thanh toán hoặc ngân hàng. | Đây là cam kết công bố ra bên ngoài, cùng hạng với thời hạn khiếu nại. Không có nó thì mỗi khoản hoàn tiền chậm đều biến thành một khiếu nại mới, và chăm sóc khách hàng không có câu trả lời nào để đưa ra ngoài câu "đang xử lý". | BR-23.7, BR-23.2, BR-27.2 |
| 20 | **Mức sẵn sàng cam kết cho đường thanh toán và khôi phục là bao nhiêu?** NFR-19 đòi hỏi sẵn sàng liên tục nhưng chưa có mức cam kết. | Con số này quyết định chi phí vận hành và quyết định luôn hai lời hứa ở BR-21.5 và BR-22.4 có thực thi được không. Hứa khôi phục ngay lúc 2 giờ sáng mà không có mức cam kết nào đỡ phía sau thì lời hứa đó chỉ đúng cho tới sự cố đầu tiên. | NFR-19, BR-22.4, BR-21.5 |
| 21 | **Cửa sổ chốt kỳ cam kết với kế toán là bao lâu, và tính từ mốc nào?** NFR-13 viện dẫn "cửa sổ thời gian đã cam kết" nhưng cửa sổ đó chưa tồn tại. | Một yêu cầu phi chức năng không có đại lượng đo là một yêu cầu không nghiệm thu được: không có con số thì không ai biết đợt chốt kỳ chạy 4 giờ là đạt hay không đạt, và cũng không có căn cứ để nói cửa sổ đó "không được nới ra khi số doanh nghiệp tăng lên". Nó còn phải cộng thêm phần chờ thủ tục bắt buộc nếu thị trường yêu cầu (BR-19.3). | NFR-13, BR-18.1, FEAT-18b |
| 22 | **Ngưỡng nào định nghĩa "một doanh nghiệp không ảnh hưởng doanh nghiệp khác"?** NFR-14 cấm việc một doanh nghiệp tăng đột biến làm chậm doanh nghiệp khác nhưng không nêu mức. | Cùng lý do câu 21. Thêm nữa, ngưỡng này quyết định một lựa chọn kiến trúc không đảo ngược rẻ: có cần cách ly luồng ghi nhận theo từng doanh nghiệp hay không. Phát hiện muộn nghĩa là phải làm lại phần chuyển giao khi đã có khách hàng lớn. | NFR-14, FEAT-12, NFR-13 |
| 23 | **Thời hạn lưu kết quả đối soát và lưu nội dung thông báo đã gửi là bao lâu?** BR-15.4 và BR-31.6 đều buộc phải lưu nhưng không nêu thời hạn. | Hai bản ghi này là bằng chứng để trả lời khiếu nại: đối soát chứng minh con số đúng, thông báo chứng minh doanh nghiệp đã được báo trước khi bị đình chỉ. Dọn sớm hơn thời hạn khiếu nại thì đúng lúc cần nhất lại không còn — xem bảng Mục 4.6. | BR-15.4, BR-15.5, BR-31.6, BR-27.2 |
| 24 | **Thời hạn ân hạn tiếp nhận sau khi đăng ký đã kết thúc là bao lâu?** BR-24.10 cho phép ngắt kênh có thông báo sau thời hạn này nhưng chưa có con số. | Đây là ngoại lệ thứ hai của nguyên tắc không-chặn-chiều-tiếp-nhận (BR-16.3), nên độ dài của nó là một cam kết công bố ra bên ngoài. Quá ngắn thì doanh nghiệp vừa hủy đã mất khách ngay; quá dài thì nhà cung cấp trả tiền nền tảng kênh cho một quan hệ đã chấm dứt. Cần chốt cùng câu 9 vì hai thời hạn phải nhất quán với nhau. | BR-24.10, BR-24.3, câu 9, câu 15 |

**Tham chiếu:** các câu 1 → 4 và câu 13 cần được chốt trước khi mở issue xây dựng cho Nhóm A và Nhóm B; câu 1 gắn với issue [#90](https://github.com/crmsaassaudi/product-management/issues/90) của Omnichat. Các câu 21 và 22 cần được chốt trước khi chốt thiết kế kiến trúc, vì chúng quyết định lựa chọn không đảo ngược rẻ về cách cách ly và cách chốt kỳ.

---

## 8. Nhật ký sửa đổi

| Ngày | Nội dung | Lý do |
| --- | --- | --- |
| 2026-08-24 | Bản đầu tiên — FEAT-01 → FEAT-32, NFR-1 → NFR-19, Mục 5, 6, 7. | Đặc tả nghiệp vụ cho một module chưa xây dựng. |
| 2026-08-25 | Rà soát toàn văn trước khi đóng phạm vi. **Bổ sung:** FEAT-18b (vòng đời & trạng thái hóa đơn); Mục 2.5 (bản đồ phụ thuộc & thứ tự triển khai); Mục 4.6 (tổng hợp thời hạn lưu trữ); Mục 8 này. **Sửa quy tắc:** BR-02.5 (thêm điều kiện thứ ba của gate phát hành), BR-06.8, BR-11.8 (sự kiện hai chiều), BR-11.8b, BR-11.9, BR-13.3b, BR-14.7, BR-14.8, BR-16.2b, BR-16.3 (liệt kê giới hạn hai ngoại lệ), BR-16.5b, BR-16.10, BR-24.10, BR-24.11, NFR-18b. **Sửa Mục 5:** thêm ba hàng và nguyên tắc thứ năm. **Sửa Mục 7.2:** thêm câu 21 → 24, cập nhật câu 5. | Đóng các lỗ hổng phát hiện khi rà soát: hai chỗ quy tắc không cùng đúng được (cách đo số ghế; ai thực thi trần tính tiền), một thực thể thiếu vòng đời (hóa đơn), một lỗ chi phí chưa bịt (kênh của tài khoản đã kết thúc quan hệ), và các cam kết chưa có cơ chế hoặc chưa có đại lượng đo. |

**Từ 2026-08-25, phạm vi tài liệu đã đóng.** Mọi thay đổi về sau đi qua quy trình thay đổi có kiểm soát và ghi vào bảng này, kèm lý do và người quyết định.
