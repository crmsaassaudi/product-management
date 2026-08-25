# CRM Product Glossary

Thuật ngữ nghiệp vụ dùng chung cho các SRS/spec trong `product-management`, được chốt trong quá trình viết và review các tài liệu đó. Đây là nguồn canonical — các SRS chỉ trích lại phần liên quan trong mục "Thuật ngữ" của mình, không định nghĩa lại khác đi.

## Object Manager

**Nhóm quyền (Group)**:
Một nhóm người dùng do quản trị viên tenant tạo ra để gán cấu hình chung (ví dụ phân quyền trường, danh sách hiển thị). Một người dùng có thể thuộc nhiều nhóm cùng lúc.
_Avoid_: Role, permission group (khác Vai trò/Role hệ thống — nhóm ở đây là đối tượng do tenant admin tự tạo, không phải vai trò cấp hệ thống).

**Phân quyền trường (Field-Level Security – FLS)**:
Cơ chế quyết định một người được làm gì với một trường — áp dụng theo Nhóm quyền, độc lập với quyền truy cập cả bản ghi. Gồm **hai chiều độc lập**: Mức truy cập (Xem & Sửa / Chỉ xem / Ẩn) và Mức hiển thị giá trị (Hiện đầy đủ / Che một phần / Che hoàn toàn), hợp nhất độc lập trên từng chiều — xem [ADR-0001](./docs/adr/0001-group-policy-conflict-resolution.md), mục Bổ sung 2026-08-23 (đã duyệt 2026-08-24).
_Avoid_: Permission, ACL (ACL kiểm soát bản ghi; FLS kiểm soát trường bên trong bản ghi — hai khái niệm khác nhau).

**Chính sách phân giải xung đột nhóm quyền (Group Policy Conflict Resolution)**:
Quy tắc quyết định cấu hình nào thắng khi một người dùng thuộc nhiều nhóm có cấu hình khác nhau trên cùng một trường. Chiến lược đang áp dụng là **hạn chế thắng (deny-override)** cho thuộc tính bảo mật, áp dụng độc lập trên từng chiều của FLS; và **cộng gộp (additive)** cho thuộc tính chất lượng dữ liệu (bắt buộc nhập) — nhưng cộng gộp **không** áp dụng khi người dùng không có quyền nhập chính trường đó, khi ấy ràng buộc bắt buộc được miễn trừ và bản ghi bị gắn cờ thiếu dữ liệu. Ngoài ra, **vắng mặt cấu hình không phải là sự cho phép**, và quy tắc này không áp cho tác vụ tự động (được miễn trừ FLS) lẫn quản trị viên tenant (không bị FLS giới hạn). Xem [ADR-0001](./docs/adr/0001-group-policy-conflict-resolution.md) — cả phần gốc lẫn bốn điều khoản bổ sung 2026-08-23 (mô hình hai chiều, miễn trừ ràng buộc bắt buộc, vắng mặt cấu hình, phạm vi chủ thể) đều đã duyệt và có hiệu lực ngang nhau.
_Avoid_: "Additive permissions" dùng chung cho mọi thuộc tính — trong hệ thống này hai nhóm thuộc tính có chiến lược hợp nhất khác nhau, không nên gộp chung một tên.

**Danh sách hiển thị dùng chung (Shared List View)**:
Cấu hình bảng danh sách bản ghi (cột, thứ tự) do quản trị viên tạo và gán cho một hay nhiều Nhóm quyền, quản lý trong Object Manager.
_Avoid_: "List view" trống không kèm tính từ — dễ nhầm với Bộ lọc/view cá nhân.

**Bộ lọc/view cá nhân (Personal View)**:
Bộ lọc hoặc bộ cột hiển thị do một người dùng cuối tự lưu cho riêng mình, độc lập với Danh sách hiển thị dùng chung. Nằm ngoài phạm vi cấu hình của Object Manager.
_Avoid_: List view (dễ nhầm với Danh sách hiển thị dùng chung — luôn phân biệt rõ "dùng chung" vs "cá nhân").

**Nhật ký kiểm toán cấu hình (Configuration Audit Trail)**:
Bản ghi tự động lưu vết ai đã thay đổi cấu hình gì trong Object Manager và vào lúc nào, dùng để điều tra sự cố (ví dụ một nhóm người dùng đột nhiên mất quyền xem một trường).
_Avoid_: "Activity log", "history" chung chung — dùng đúng tên này để phân biệt với log hoạt động nghiệp vụ khác (ví dụ lịch sử thay đổi trên một bản ghi Deal).

**Bắt buộc khi tạo mới (Required on Create)**:
Trường bắt buộc phải có giá trị ngay lúc tạo bản ghi — tập con của "bắt buộc", loại trừ các trường chỉ có ý nghĩa/bắt buộc sau khi bản ghi đã tồn tại.
_Avoid_: "Required" dùng lẫn cho cả hai trường hợp mà không phân biệt thời điểm áp dụng.

## IAM & Phân quyền Workspace

**Quyền Ủy thác (Delegated Grant Authority)**:
Một quyền hệ thống riêng biệt, do Owner/Admin chủ động cấp, cho phép người giữ nó gán các Vai trò/Nhóm đã tồn tại sẵn trong workspace cho thành viên khác mà không bị giới hạn bởi năng lực quyền hạn của chính bản thân (miễn trừ nguyên tắc "không vượt ceiling"). Không áp dụng cho việc tạo Vai trò/Nhóm mới, không áp dụng cho Cấp quyền tạm thời, và không cho phép thay đổi Cấp bậc thành viên (Owner/Admin/Member).
_Avoid_: "Quyền tạm thời"/"Role Assignment" (tên khác của một tính năng đã có sẵn — cấp có thời hạn, cần phê duyệt; Quyền Ủy thác là cấp vĩnh viễn, không cần phê duyệt, chỉ miễn trừ ràng buộc ceiling). Xem [ADR-0002](./docs/adr/0002-delegated-grant-authority-ceiling-exception.md).

**Cấp bậc thành viên (Membership Tier)** vs **Vai trò (Role)**:
Hai trục hoàn toàn độc lập trong một workspace — Cấp bậc thành viên (Owner/Admin/Member) quyết định có toàn quyền hay không; Vai trò là tập hợp quyền hạn chi tiết chỉ có ý nghĩa ở cấp Member. Một thay đổi tác động tới trục này không mặc nhiên tác động tới trục kia.
_Avoid_: Dùng lẫn "vai trò" để chỉ cả hai trục trong văn bản nghiệp vụ (ví dụ khi liệt kê phạm vi một nhật ký/audit log) — luôn nêu rõ đang nói tới Cấp bậc thành viên hay Vai trò.

**Nguyên tắc đóng cho Nhật ký cấu hình quyền (Closure Rule)**:
Nguyên tắc xác định phạm vi "Nhật ký thay đổi cấu hình quyền" (audit log Fail-closed, lưu 2 năm): bất kỳ thao tác nào làm thay đổi ai-được-làm-gì hoặc ai-thấy-gì trong workspace đều mặc định thuộc diện này, trừ thao tác xem trước/mô phỏng và thao tác chỉ đọc. Dùng nguyên tắc này thay vì liệt kê tĩnh để tránh bỏ sót tính năng phân quyền mới phát sinh sau này.
_Avoid_: Coi danh sách ví dụ minh hoạ (Vai trò, Nhóm, Đơn vị tổ chức...) là danh sách đóng kín — đó chỉ là ví dụ, nguyên tắc mới là điều khoản ràng buộc thật. Xem [ADR-0003](./docs/adr/0003-permission-config-audit-log-fail-closed.md) cho quyết định Fail-closed đi kèm.

## Omnichat

**Hồ sơ khách hàng tạm (Provisional Customer Record)**:
Một hồ sơ khách hàng do hệ thống tự tạo ngay khi nhận tin nhắn từ một người gửi chưa xác định được danh tính CRM thật (chưa khớp email/số điện thoại/liên kết thủ công nào). Được thay thế bằng liên kết tới hồ sơ khách hàng thật khi có đủ căn cứ. Chuẩn rủi ro áp dụng thống nhất cho mọi kênh: chỉ được **tự động** gộp khi định danh người gửi do chính nền tảng kênh xác minh là của một cá nhân (hiện chỉ WhatsApp) **và** định danh đó chưa được đánh dấu là Định danh dùng chung; mọi trường hợp còn lại chỉ hiển thị gợi ý để Agent xác nhận. Mọi lần gộp đều phải gỡ lại được.
_Avoid_: "Shadow contact", "khách vãng lai" — dùng đúng tên này để nhất quán giữa các SRS omnichannel.

**Định danh dùng chung (Shared Identifier)**:
Một số điện thoại hoặc email được đánh dấu là không thuộc riêng một cá nhân — số tổng đài của đại lý, máy bàn văn phòng, email chung của một phòng ban. Không bao giờ được dùng làm căn cứ tự động gộp hồ sơ khách hàng, kể cả trên kênh có định danh xác thực mạnh.
_Avoid_: "Số chung", "số công ty" — dùng đúng tên này vì nó là một trạng thái do người dùng chủ động đánh dấu, không phải một suy đoán của hệ thống.

**Ngưỡng an toàn đóng hàng loạt (Bulk-Close Safety Threshold)**:
Cơ chế giới hạn số lượng hội thoại được tự động đóng trong một khoảng thời gian ngắn của một tenant/kênh, để một lỗi cấu hình hoặc sự cố kênh không âm thầm đóng hàng loạt hội thoại đang cần xử lý. Khi chạm ngưỡng, việc đóng bị hoãn lại và được thử lại sau, không hủy bỏ.
_Avoid_: "Circuit breaker", "rate limit" — đây là khái niệm nghiệp vụ (bảo vệ khách hàng khỏi bị đóng hội thoại oan), không phải thuật ngữ hạ tầng.

**Lời mời nhận hội thoại (Conversation Offer)**:
Một đề nghị có thời hạn gửi tới một Agent cụ thể để nhận xử lý một hội thoại đang chờ. Agent phải phản hồi (nhận/từ chối) trong thời hạn; hết hạn hoặc từ chối thì hội thoại được mời tới Agent phù hợp tiếp theo.
_Avoid_: "Offer/lease", "work item" — dùng "Lời mời nhận hội thoại" trong mọi tài liệu nghiệp vụ.

**Ưu tiên người phụ trách trước đó (Previous-Assignee Priority / Sticky Routing)**:
Quy tắc ưu tiên định tuyến hội thoại mới của một khách hàng trở lại đúng Agent đã từng phụ trách khách hàng đó gần đây, nếu Agent đó còn khả năng nhận thêm việc — nhằm giữ mạch tương tác quen thuộc cho khách hàng.
_Avoid_: "Sticky assignee/sticky routing" trong văn bản nghiệp vụ — chỉ dùng trong tài liệu kỹ thuật.

**Thuộc tính kênh (Channel Attributes)**:
Tập đặc điểm nghiệp vụ mà mỗi kênh khai báo khi được kết nối: trò chuyện đồng thời hay không đồng thời, có áp Cửa sổ phản hồi hay không, có tin nhắn mẫu được nền tảng phê duyệt trước hay không, định danh người gửi có được nền tảng xác minh là của một cá nhân hay không, và khả năng hiển thị tin nhắn dạng nút bấm. Mọi quy tắc nghiệp vụ trong SRS đều viện dẫn thuộc tính, không viện dẫn tên kênh — để thêm một kênh mới không phải sửa quy tắc.
_Avoid_: Liệt kê tên kênh ("hiện chỉ WhatsApp", "gộp chung Facebook, Instagram, Zalo...") bên trong một quy tắc nghiệp vụ — danh sách kênh thay đổi, quy tắc thì không nên.

**Lý do xử lý (Disposition)**:
Mã phân loại kết quả một hội thoại, do doanh nghiệp tự định nghĩa theo phân cấp và Agent chọn khi giải quyết. Là căn cứ duy nhất để trả lời câu hỏi "khách hàng liên hệ vì chuyện gì" trong báo cáo. Khác **Nhãn (Tag)** ở chỗ mỗi hội thoại chỉ có một lý do xử lý ở mỗi chu kỳ giải quyết, còn nhãn thì gắn được nhiều và gắn được bất cứ lúc nào.
_Avoid_: "Lý do đóng", "kết quả" chung chung — và không dùng lẫn với **lý do chuyển tiếp**, vốn là một danh mục riêng.

**Xử lý sau hội thoại (Wrap-up)**:
Khoảng thời gian một Agent hoàn tất phần việc còn lại của hội thoại vừa kết thúc (chọn lý do xử lý, ghi chú, cập nhật hồ sơ khách hàng) trước khi được phân công việc mới. Là thời gian làm việc thật, phải tính vào thời gian xử lý trung bình.
_Avoid_: Coi đây là thời gian nghỉ — nếu không tách thành một trạng thái riêng, việc mới sẽ ập tới ngay khi Agent vừa đóng hội thoại cũ và phần ghi nhận bị bỏ qua.

**Tỷ lệ khách bỏ cuộc (Abandonment Rate)**:
Tỷ lệ khách hàng chủ động rời đi trước khi có Agent nào tiếp nhận hội thoại của họ. Cùng với tỷ lệ đáp ứng cam kết thời gian phản hồi, đây là hai chỉ số cốt lõi đo năng lực phục vụ — vì tỷ lệ đáp ứng chỉ nói về những khách đã được phục vụ.
_Avoid_: Gộp khách bỏ cuộc vào nhóm "hội thoại không hoạt động rồi tự đóng" — như vậy một doanh nghiệp thiếu người vẫn nhìn thấy chỉ số đẹp.

**Tạm dừng xóa theo yêu cầu pháp lý (Legal Hold)**:
Trạng thái đặt lên dữ liệu của một hội thoại hoặc một khách hàng đang trong diện tranh chấp/điều tra, làm ngưng hiệu lực của cả thời hạn lưu trữ lẫn yêu cầu xóa dữ liệu cá nhân, cho tới khi được gỡ. Mỗi lần đặt/gỡ phải ghi rõ ai đặt và căn cứ gì.
_Avoid_: Coi đây là một ngoại lệ kỹ thuật của chính sách lưu trữ — đây là một quyết định có chủ thể chịu trách nhiệm, phải soi lại được.

**Cửa sổ phản hồi (Reply Window)**:
Khoảng thời gian, tính từ tin nhắn gần nhất của khách hàng, mà Agent còn được phép chủ động gửi tin nhắn tự do cho khách trên một kênh nhắn tin. Sau khi hết cửa sổ, một số kênh (ví dụ WhatsApp) chỉ cho gửi tin dạng mẫu đã được phê duyệt trước; một số kênh khác (Email, Live Chat, Telegram) không có giới hạn này.
_Avoid_: "Reply window" tiếng Anh trong văn bản nghiệp vụ tiếng Việt — dùng "Cửa sổ phản hồi".

## Billing & Subscription

**Hồ sơ thanh toán (Billing Account)**:
Danh tính thương mại của một doanh nghiệp — tên pháp lý, quốc gia, địa chỉ xuất hóa đơn, mã số thuế, tiền tệ, múi giờ cắt kỳ và người phụ trách thanh toán. Là cầu nối duy nhất giữa danh tính vận hành (workspace) và danh tính người mua. Mỗi doanh nghiệp có đúng một hồ sơ; tiền tệ và múi giờ cắt kỳ cố định trong suốt vòng đời đăng ký.
_Avoid_: Dùng lẫn "tenant"/"workspace"/"customer" khi nói về bên trả tiền — workspace là nơi làm việc, hồ sơ thanh toán là bên mua; hai thứ có vòng đời và quy tắc phân quyền khác nhau.

**Gói cước (Plan)** và **Phiên bản gói (Plan Version)**:
Gói cước là tập điều kiện thương mại được bán (phí thuê bao theo chu kỳ, hạn mức bao gồm theo từng loại tiêu dùng, đơn giá vượt). Phiên bản gói là một ảnh chụp bất biến của tập điều kiện đó — mỗi lần thay đổi giá hay hạn mức tạo ra một phiên bản mới, và doanh nghiệp đã đăng ký giữ nguyên phiên bản họ đã ký. Giá thương lượng riêng cho một doanh nghiệp cũng biểu diễn dưới dạng một phiên bản gói riêng, không sửa đè lên gói niêm yết.
_Avoid_: "Đổi giá gói" như một thao tác đơn lẻ — trong hệ thống này không có thao tác đó, chỉ có "phát hành phiên bản mới".

**Loại tiêu dùng tính phí (Billable Usage Type)**:
Một thứ đo được và tính tiền theo mức sử dụng, có mã định danh ổn định (ví dụ hội thoại được tạo, tin nhắn mẫu được gửi). Mỗi loại khai báo đơn vị đo, cách gộp trong kỳ và thời điểm tính phí. Mã đã phát hành không bao giờ đổi tên và không tái sử dụng; đổi bản chất đo lường bắt buộc tạo mã mới, vì mọi hóa đơn lịch sử đều tham chiếu tới mã cũ.
_Avoid_: "Metric", "usage code" trong văn bản nghiệp vụ tiếng Việt; và tránh gọi tên một loại cụ thể bên trong một quy tắc — quy tắc luôn viện dẫn thuộc tính của loại tiêu dùng, để thêm loại mới không phải sửa quy tắc.

**Sự kiện tính phí (Billing Event)**:
Bản ghi chỉ-thêm-mới do một module nghiệp vụ phát sinh khi một việc đáng tính phí xảy ra, gồm doanh nghiệp, loại tiêu dùng, mã tham chiếu tới đối tượng gốc, số lượng và thời điểm phát sinh. Là ranh giới duy nhất giữa nghiệp vụ CRM và lớp thương mại: module nghiệp vụ không biết gì về giá, hạn mức hay hóa đơn. Sự kiện đã ghi không sửa và không xóa.
_Avoid_: Coi đây là một bản ghi kỹ thuật nội bộ — nó là chứng cứ để trả lời khiếu nại hóa đơn, nên vòng đời và thời hạn lưu của nó là cam kết nghiệp vụ. Xem [ADR-0004](./docs/adr/0004-billing-engine-boundary.md).

**Thời điểm tính phí (Billable Moment)**:
Khoảnh khắc chính xác một việc trở thành đáng tính phí, định nghĩa riêng cho từng loại tiêu dùng, bằng ngôn ngữ nghiệp vụ và kiểm chứng được từ bên ngoài. Ví dụ: một tin nhắn mẫu được tính khi nền tảng kênh xác nhận đã tiếp nhận để gửi — không phải khi Agent bấm gửi. Một loại tiêu dùng chưa có định nghĩa này thì chưa được phát hành để bán.
_Avoid_: Mô tả mơ hồ kiểu "khi gửi tin nhắn" — mỗi từ mơ hồ ở đây là một khiếu nại hóa đơn về sau.

**Bút toán đảo (Reversal)**:
Cách duy nhất để hủy hiệu lực một sự kiện tính phí đã ghi nhận — tạo một bản ghi ngược tham chiếu tới sự kiện gốc, giữ nguyên lịch sử và thay đổi kết quả. Đảo trong kỳ chưa chốt làm giảm trực tiếp tiêu dùng của kỳ; đảo sau khi hóa đơn đã phát hành phải đi qua chứng từ ghi có. Căn cứ đảo là một danh sách đóng, không phải quyết định tùy tình huống.
_Avoid_: "Sửa lại"/"xóa sự kiện" — hai thao tác này không tồn tại trong hệ thống.

**Hạn mức bao gồm (Included Quota)** và **Chính sách chạm trần (Quota Policy)**:
Hạn mức bao gồm là lượng tiêu dùng đã nằm trong phí thuê bao của một kỳ, không cộng dồn sang kỳ sau trừ khi gói ghi rõ. Chính sách chạm trần là điều xảy ra khi dùng hết: chặn, cho vượt và tính phí, hoặc cho vượt tới trần cứng rồi chặn. Mỗi hạn mức gắn với đúng một chính sách — sản phẩm không có hành vi mặc định ngầm.
_Avoid_: Chặn theo chính sách này ở chiều tiếp nhận hoạt động do khách hàng của doanh nghiệp khởi xướng — chặn chỉ áp cho hành vi doanh nghiệp chủ động thực hiện, nếu không việc kiểm soát chi phí bị đổ lên đầu một bên thứ ba vô can.

**Trần chi phí vượt (Overage Ceiling)**:
Mức phí vượt tối đa của một doanh nghiệp trong một kỳ, mặc định tính theo bội số của phí thuê bao. Nó có hai cách vận hành tùy nguồn phát sinh tiêu dùng: với tiêu dùng do doanh nghiệp chủ động tạo ra, đây là **trần chặn** — chạm trần thì dừng và chờ doanh nghiệp xác nhận; với tiêu dùng do khách hàng của doanh nghiệp khởi xướng, chặn bị cấm nên đây là **trần tính tiền** — hoạt động vẫn tiếp nhận và vẫn ghi nhận, nhưng phần vượt trần không vào hóa đơn khi chưa có xác nhận và trở thành chi phí nhà cung cấp tự chịu, phải đo và báo cáo cùng giá vốn tiêu dùng.
_Avoid_: Nói "trần chi phí vượt" như một cơ chế duy nhất — nói vậy là ngầm hứa một mức chặn mà ở chiều tiếp nhận hệ thống không có quyền thực thi, và biến một khoản lỗ có thật của nhà cung cấp thành một con số không ai theo dõi. Xem BR-16.5 và BR-16.8 trong [`billing-subscription-srs.md`](./srs/billing-subscription-srs.md).

**Đình chỉ dịch vụ (Suspension)**:
Trạng thái hạn chế một doanh nghiệp sau khi chu trình nhắc nợ kết thúc mà chưa thu được tiền. Chỉ chặn các hành vi chủ động phát sinh chi phí mới (gửi tin đi, chạy chiến dịch, thêm người dùng); vẫn tiếp nhận và lưu hoạt động do khách hàng của doanh nghiệp khởi xướng, vẫn cho xuất dữ liệu, không xóa dữ liệu, và khôi phục tự động ngay khi thanh toán thành công.
_Avoid_: Coi đình chỉ là "khóa tài khoản" — giữ dữ liệu làm con tin không phải công cụ thu nợ, và nó biến tranh chấp về tiền thành tranh chấp pháp lý.

**Người phụ trách thanh toán (Billing Contact)**:
Vai trò trong một doanh nghiệp chịu trách nhiệm về hóa đơn, phương thức thanh toán và khiếu nại. Có thể là một người không tham gia vận hành CRM hằng ngày. Quyền xem dữ liệu tài chính của vai trò này **không** kéo theo quyền đọc nội dung nghiệp vụ: khi đối chiếu một dòng hóa đơn, họ thấy số lượng và định danh đối tượng nhưng không đọc được nội dung hội thoại.
_Avoid_: Gộp vai trò này vào Chủ workspace — chúng có thể là hai người, và hệ thống không bao giờ được ở trạng thái không có ai nhận thông báo về tiền.
