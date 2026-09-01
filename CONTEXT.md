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

## Onboarding & Khởi tạo Không gian làm việc

**Quy trình Đăng ký Tự phục vụ (Self-Serve / PLG Onboarding)**:
Quy trình nhiều bước (multi-step wizard) cho phép khách hàng tự đăng ký tài khoản, khai báo thông tin doanh nghiệp, chọn mục tiêu sử dụng và chờ hệ thống tự động khởi tạo không gian làm việc (workspace) mà không cần sự can thiệp thủ công của đội ngũ vận hành nền tảng.

**Quy trình Khởi tạo Doanh nghiệp (Enterprise / SLG Onboarding)**:
Quy trình khởi tạo không gian làm việc dành cho khách hàng doanh nghiệp lớn thông qua cổng quản trị nội bộ hoặc API hệ thống, tạo trước tài khoản quản trị chưa có mật khẩu và gửi email kích hoạt bảo mật để thiết lập mật khẩu lần đầu.

**Tên miền phụ tổ chức (Tenant Subdomain / Alias)**:
Chuỗi định danh duy nhất toàn hệ thống đại diện cho không gian làm việc của một tổ chức trên Internet (ví dụ: `acme.crmsaudi.dev`). Có thể được sinh tự động từ tên doanh nghiệp (hỗ trợ chuyển đổi tiếng Việt có dấu) hoặc do người dùng tự chỉnh sửa theo quy chuẩn URL-safe.

**Chuỗi giao dịch bù trừ (Provisioning Saga & Compensation Rollback)**:
Mô hình điều phối chuỗi tác vụ khởi tạo không gian làm việc phân tán qua nhiều hệ thống độc lập (Keycloak, MongoDB, crm-bot, Redis). Nếu một bước gặp lỗi không thể khắc phục, hệ thống sẽ thực thi các tác vụ hoàn tác (compensating transactions) theo thứ tự ngược lại để dọn sạch tài nguyên rác và đảm bảo tính nhất quán dữ liệu.

**Khởi tạo Cấu hình Nền tảng (Baseline Seeding)**:
Tập hợp các bước tự động thiết lập cấu hình nghiệp vụ chuẩn ngay sau khi không gian làm việc được tạo thành công, bao gồm thiết lập CRM mặc định, quy trình bán hàng (Deal Pipeline & Stages), quy trình hỗ trợ (Ticket Workflow), quy tắc điều phối (Assignment Rules), vai trò hệ thống dựng sẵn (System Roles), đơn vị tổ chức gốc (HQ) và nhóm chủ sở hữu (Owner Group).

**Dữ liệu Mẫu Định hướng (Tailored Sample Data)**:
Tập hợp dữ liệu mẫu (Khách hàng, Doanh nghiệp, Cơ hội bán hàng) được khởi tạo tự động phù hợp với mục tiêu sử dụng (`onboardingGoal`) mà người dùng đã chọn trong quy trình đăng ký, giúp rút ngắn thời gian tiếp cận giá trị sản phẩm (Time-to-Value).

**Tài khoản Khởi tạo Dở dang / Mồ côi (Orphan Account)**:
Tài khoản người dùng đã bắt đầu bước 1 của quy trình đăng ký nhưng rời bỏ mà không hoàn tất việc tạo không gian làm việc. Được hệ thống tự động quét và thu hồi định kỳ sau 24 giờ để tránh lãng phí định danh và tài nguyên.

**Bộ chọn Không gian làm việc (Workspace Switcher / Tenant Picker)**:
Giao diện hiển thị danh sách tất cả các không gian làm việc mà một người dùng đang là thành viên, cho phép chuyển đổi qua lại giữa các tổ chức hoặc tự động điều hướng trực tiếp vào không gian làm việc nếu người dùng chỉ thuộc đúng 1 tổ chức.

**Dùng thử Miễn phí 14 ngày (14-Day Free Trial)**:
Chính sách tự động cấp quyền trải nghiệm toàn bộ các tính năng cao cấp của gói chuyên nghiệp (Pro/Enterprise) trong 14 ngày ngay sau khi đăng ký tự phục vụ, không yêu cầu thẻ tín dụng, nhằm tối đa hóa cơ hội chứng minh giá trị sản phẩm trước khi chuyển đổi trả phí.

**Bảng tiến độ Tiếp nhận Tương tác (In-App Onboarding Checklist & FTUX)**:
Thành phần giao diện tương tác hiển thị trên màn hình chính sau khi đăng nhập lần đầu, gồm danh sách 5 tác vụ cốt lõi (kết nối kênh, mời đồng nghiệp, nhập danh bạ, tạo cơ hội, tải ứng dụng) kèm thanh phần trăm hoàn thành và phần thưởng khích lệ để dẫn dắt người dùng đạt trạng thái kích hoạt (Product Activation).

**Trình Quản lý Dữ liệu Mẫu & Xóa 1-Click (Sample Data Manager & Purge)**:
Tính năng cho phép người dùng chủ động bật/tắt hiển thị hoặc xóa sạch toàn bộ các bản ghi dữ liệu mẫu (Contacts, Accounts, Deals) đã nạp ban đầu chỉ bằng 1 thao tác bấm nút khi doanh nghiệp sẵn sàng đưa dữ liệu kinh doanh thật vào vận hành.

**Tên miền Riêng Tùy chỉnh (Custom Domain CNAME)**:
Khả năng cho phép tổ chức sử dụng tên miền thương hiệu riêng của doanh nghiệp (ví dụ: `crm.congty.vn`) thay cho tên miền phụ mặc định (`congty.crmsaudi.dev`), thông qua việc cấu hình bản ghi DNS CNAME và cơ chế tự động cấp phát chứng chỉ bảo mật SSL/TLS.

**Phễu Bán hàng Đặc thù theo Ngành (Industry-Specific Pipeline)**:
Quy trình các giai đoạn bán hàng được tùy biến cấu trúc tự động dựa trên ngành nghề kinh doanh mà khách hàng đã chọn (Bất động sản, Bán lẻ, Dịch vụ B2B, Tài chính), thay vì chỉ áp dụng một phễu bán hàng chung chung cho mọi lĩnh vực.

**Cảnh báo Khách hàng Doanh nghiệp Tiềm năng (Enterprise Sales Alert)**:
Cơ chế tự động chấm điểm và phát sinh thông báo tức thì tới đội ngũ kinh doanh nội bộ khi một khách hàng đăng ký có quy mô nhân sự lớn (`200+`) hoặc thuộc ngành mục tiêu chiến lược, giúp đội ngũ bán hàng chủ động liên hệ tư vấn chuyên sâu.

**Chiến dịch Email Nuôi dưỡng Tự động (Onboarding Drip Email Campaign)**:
Chuỗi thông điệp email được hệ thống tự động gửi định kỳ vào các mốc thời gian then chốt (ngày 1, ngày 3, ngày 7, ngày 12) sau khi đăng ký, cung cấp hướng dẫn nghiệp vụ và thúc đẩy người dùng hoàn thành các mốc kích hoạt sản phẩm.

## Quản lý Khách hàng & Danh bạ (Contacts & Accounts)

**Khách hàng Cá nhân (Contact)**:
Thực thể đại diện cho một con người cụ thể trong CRM (khách hàng tiềm năng, người liên hệ của doanh nghiệp, người mua lẻ, đối tác). Lưu trữ thông tin định danh, các kênh liên lạc có thể tiếp cận, lịch sử tương tác và mối quan hệ với các doanh nghiệp/cá nhân khác.

**Tổ chức / Doanh nghiệp (Account)**:
Thực thể đại diện cho một pháp nhân, công ty, tập đoàn hoặc cơ quan tổ chức mà doanh nghiệp đang có quan hệ kinh doanh. Quản lý thông tin mã số thuế, ngành nghề, quy mô, doanh thu, cây cấu trúc Công ty Mẹ - Công ty Con và danh sách các nhân sự liên hệ thuộc tổ chức.

**Giai đoạn Vòng đời Khách hàng (Lifecycle Stage)**:
Trạng thái định vị mức độ gắn kết và trưởng thành của khách hàng trong hành trình chuyển đổi doanh nghiệp (chuẩn 7 giai đoạn: *Subscriber -> Lead -> Marketing Qualified Lead / MQL -> Sales Qualified Lead / SQL -> Opportunity -> Customer -> Evangelist*).

**Chuyển đổi Khách hàng Tiềm năng (Lead Qualification & Conversion)**:
Quy trình nghiệp vụ thẩm định và nâng cấp một Khách hàng tiềm năng (Lead) đã đủ điều kiện kinh doanh thành Liên hệ chính thức (Contact), tự động liên kết hoặc tạo mới Doanh nghiệp (Account) và tạo Cơ hội bán hàng (Deal) tương ứng chỉ bằng một thao tác nguyên tử.

**Nhận diện & Gộp Trùng lặp (Duplicate Detection & Merge)**:
Cơ chế tự động phát hiện các bản ghi trùng nhau (theo Email, Số điện thoại, Mã số thuế) và cho phép người dùng xem trước (Preview Merge), lựa chọn bản ghi chính (Master Record), kế thừa dữ liệu và chuyển giao toàn bộ lịch sử tương tác/bản ghi con trước khi gộp.

**Sổ cái Hoàn tác Gộp (Unmerge Ledger)**:
Cơ chế lưu trữ vết lịch sử gộp bản ghi cho phép người dùng có thẩm quyền đảo ngược hoàn toàn một giao dịch gộp trước đó (Unmerge), khôi phục lại bản ghi đã mất và phân bổ lại đúng các mối liên kết gốc.

**Mối quan hệ Đa tổ chức (Multi-Affiliations)**:
Khả năng liên kết một cá nhân (Contact) với nhiều doanh nghiệp (Accounts) khác nhau cùng lúc với các chức danh, vai trò (Chính / Phụ / Cố vấn) và khoảng thời gian công tác riêng biệt.

**Quan hệ Giữa các Cá nhân (Person Relations)**:
Liên kết mạng lưới quan hệ trực tiếp giữa hai con người trong CRM (Quản lý trực tiếp / Reports-to, Người giới thiệu / Referred-by, Thành viên gia đình / Household, Đối tác kinh doanh / Partner).

**Dòng thời gian Hoạt động 360 độ (360-Degree Unified Timeline)**:
Bảng luồng thông tin hợp nhất hiển thị toàn bộ lịch sử tương tác của khách hàng (Email, Cuộc gọi, Ghi chú, Tin nhắn đa kênh, Vé hỗ trợ, Cơ hội bán hàng, Nhiệm vụ, Lịch sử đổi giai đoạn) theo thứ tự thời gian đảo ngược.

**Điểm Tiềm năng Khách hàng (Lead Score)**:
Điểm số định lượng tự động tính toán dựa trên mức độ phù hợp hồ sơ (Profile Fit) và mức độ tương tác thực tế (Engagement Activity), có cơ chế suy giảm điểm theo thời gian (Score Decay) để ưu tiên chăm sóc các cơ hội nóng.

**Khách hàng Đã rời bỏ (Churned Customer / Former Customer)**:
Trạng thái vòng đời của một khách hàng cá nhân hoặc doanh nghiệp đã từng mua hàng nhưng sau đó hủy hợp đồng, chấm dứt gói thuê bao hoặc không còn phát sinh bất kỳ giao dịch nào trong thời gian dài. Khách hàng ở trạng thái này bị loại khỏi các chiến dịch tiếp thị thông thường và chỉ được tiếp cận qua các chiến dịch giữ chân/tái kích hoạt (Win-Back Campaigns) được phê duyệt riêng.

**Lead Bị loại (Disqualified Lead)**:
Trạng thái vòng đời dành cho các khách hàng tiềm năng không phù hợp với tiêu chí khách hàng mục tiêu (sai ngành nghề, không đủ ngân sách, thông tin liên lạc giả mạo, spam). Lead bị loại được lưu trữ để phân tích chất lượng nguồn marketing nhưng bị loại bỏ khỏi danh sách phân bổ cho nhân viên kinh doanh.

**Phân bổ Lead Tự động (Lead Routing / Auto-Assignment)**:
Quy tắc tự động gán Người phụ trách (Owner) cho các khách hàng tiềm năng mới đổ về từ các kênh số (Website form, Chatbot, Facebook Ads, API) dựa trên thuật toán chia đều vòng (Round-robin), phân chia theo vùng địa lý (Territory) hoặc chuyên môn ngành nghề (Industry specialization).

**Tham số Nguồn gốc Tiếp thị (UTM Source Tracking)**:
Tập hợp các tham số theo dõi nguồn gốc (`utm_source`, `utm_medium`, `utm_campaign`, `utm_term`, `utm_content`) được hệ thống tự động ghi nhận tại thời điểm Lead đăng ký lần đầu để phục vụ phân tích hiệu quả kênh tiếp thị (Marketing Attribution) và tính toán tỷ suất sinh lời trên chi phí (ROI).

**Trợ lý Nhập Dữ liệu Thông minh (Smart Data Import Wizard)**:
Trình nhập khẩu dữ liệu từ tệp Excel (.xlsx) hoặc CSV dung lượng lớn (tới 50MB) qua hàng đợi bất đồng bộ, có tính năng tự động nhận diện cột (Auto Field Mapping), kiểm tra tính hợp lệ từng dòng và xuất tệp báo cáo lỗi chi tiết.

**Trạng thái Đồng thuận & Khả năng Tiếp cận (Consent & Deliverability)**:
Theo dõi tình trạng đồng thuận nhận tin quảng bá/tiếp thị (Opt-in Consent theo chuẩn GDPR/Anti-spam), đánh dấu kênh chính (Primary) và trạng thái kỹ thuật của địa chỉ liên lạc (Verified / Bounced / Inactive).

## Quản lý Cơ hội & Phễu Bán hàng (Deals & Pipelines)

**Cơ hội Bán hàng (Deal / Opportunity)**:
Thực thể đại diện cho một giao dịch kinh doanh tiềm năng giữa doanh nghiệp và khách hàng cá nhân hoặc tổ chức, có giá trị tiền tệ dự kiến, ngày dự kiến đóng và gắn liền với một giai đoạn cụ thể trên phễu bán hàng.

**Phễu Bán hàng (Sales Pipeline)**:
Quy trình trực quan hóa toàn bộ các bước từ khi tiếp cận cơ hội đến khi chốt hợp đồng thành công. Một không gian làm việc có thể sở hữu nhiều phễu bán hàng độc lập (Multiple Pipelines) cho các dòng sản phẩm, dịch vụ hoặc thị trường khác nhau.

**Giai đoạn Bán hàng & Xác suất Thành công (Stage & Win Probability)**:
Các cột mốc tuần tự trên phễu bán hàng (ví dụ: *Tiếp cận -> Khảo sát nhu cầu -> Báo giá -> Đàm phán -> Ký hợp đồng / Thất bại*), mỗi giai đoạn được gán một tỷ lệ xác suất thành công từ 0% đến 100% để tính toán dự báo doanh thu.

**Bảng Kanban Cơ hội (Deals Kanban Board)**:
Giao diện trực quan dạng bảng thẻ kéo thả phân chia theo các cột giai đoạn bán hàng, hiển thị số lượng và tổng giá trị cơ hội trên từng cột theo thời gian thực.

**Thời gian Lưu tại Giai đoạn (Time in Stage / Stage Duration)**:
Chỉ số đo lường chính xác số ngày/giờ mà một cơ hội bán hàng đã nằm yên tại một giai đoạn cụ thể, dùng để phát hiện điểm nghẽn quy trình và tính toán vận tốc bán hàng (Sales Velocity).

**Cơ hội Nguội Lạnh (Stale Deal)**:
Cơ hội bán hàng không có bất kỳ hoạt động tương tác nào (không có cuộc gọi, email, ghi chú hay chuyển giai đoạn) vượt quá ngưỡng thời gian quy định (ví dụ >14 ngày), được hệ thống tự động đánh dấu cảnh báo để người phụ trách kịp thời xử lý.

**Nhắc nhở Chăm sóc Tiếp theo (Follow-up Reminder)**:
Thời điểm cam kết tương tác tiếp theo với khách hàng (`nextFollowUpAt`) do nhân viên kinh doanh thiết lập, hệ thống sẽ tự động phát sinh thông báo nhắc nhở trước hạn chót để không bao giờ bỏ quên khách hàng.

**Lý do Thất bại (Loss Reason)**:
Danh mục nguyên nhân được chuẩn hóa (ví dụ: *Giá quá cao, Chọn đối thủ cạnh tranh, Hết ngân sách, Không có nhu cầu*) bắt buộc người dùng phải khai báo khi chuyển cơ hội sang trạng thái Đóng Thất bại (Closed Lost) để phục vụ phân tích cải tiến sản phẩm.

**Doanh thu Dự báo có Trọng số (Weighted Pipeline Forecast)**:
Doanh thu kỳ vọng được tính bằng tổng của: $\text{Giá trị cơ hội} \times \text{Xác suất thành công của giai đoạn}$ ($\sum (\text{Value} \times \text{Probability})$) theo từng tháng hoặc quý.

**Vai trò Liên hệ trong Cơ hội (Contact Roles on Deals)**:
Khả năng gắn nhiều nhân sự liên hệ vào cùng một Cơ hội bán hàng với các vai trò quyết định khác nhau (Người ra quyết định / Decision Maker, Người đánh giá kỹ thuật / Technical Evaluator, Người bảo trợ nội bộ / Champion, Người mua hàng / Buyer).

**Lưu trữ & Di chuyển Phễu (Pipeline Archival & Migration)**:
Quy trình đóng một phễu bán hàng không còn sử dụng, yêu cầu di chuyển toàn bộ các cơ hội đang mở sang một phễu khác hoặc đóng băng chúng ở chế độ chỉ đọc (Read-Only) để bảo toàn lịch sử báo cáo tài chính.

**Danh mục Sản phẩm & Chi tiết Báo giá trên Deal (Deal Products & Line Items)**:
Danh sách các mặt hàng, gói dịch vụ đính kèm trong một Cơ hội bán hàng, bao gồm mã SKU, tên sản phẩm, số lượng, đơn giá niêm yết, tỷ lệ chiết khấu (%) và thuế suất (%), được dùng làm căn cứ tự động tính toán tổng giá trị giao dịch (`value`).

**Lý do Thành công (Win Reason)**:
Danh mục nguyên nhân chuẩn hóa (ví dụ: *Giá cả cạnh tranh, Tính năng vượt trội, Uy tín thương hiệu, Dịch vụ chăm sóc xuất sắc*) được ghi nhận khi chuyển cơ hội sang trạng thái Đóng Thành công (Closed Won) để phân tích chiến lược kinh doanh.

**Ma trận Ánh xạ Giai đoạn (Stage Mapping Matrix)**:
Bảng quy chuẩn thiết lập tương quan giữa các giai đoạn của phễu cũ và phễu mới khi thực hiện di chuyển phễu bán hàng, đảm bảo các cơ hội được di chuyển vào đúng giai đoạn tương đương thay vì dồn tất cả về một giai đoạn duy nhất.

**Điều kiện Chuyển Giai đoạn (Stage-Gate Rules / Stage Entry Requirements)**:
Bộ quy tắc kiểm soát chất lượng dữ liệu bắt buộc phải hoàn thành (nhập các trường bắt buộc, đính kèm tài liệu hợp đồng, hoặc có phê duyệt của quản lý) trước khi cơ hội bán hàng được phép chuyển từ giai đoạn này sang giai đoạn kế tiếp trên phễu.

**Tạm ngưng Cơ hội (On Hold Deal Status)**:
Trạng thái đặt lên cơ hội bán hàng khi thương vụ bị hoãn tạm thời do khách hàng chờ ngân sách hoặc chờ duyệt nội bộ, cho phép ẩn khỏi dự báo doanh thu (Forecast) và tạm ngưng cảnh báo cơ hội nguội mà không phải đánh dấu Thất bại.

**Người cộng tác Cơ hội (Deal Collaborator)**:
Nhân sự thuộc các phòng ban hỗ trợ (Pre-sales, Kỹ thuật, Pháp chế, Kế toán) được thêm vào cơ hội bán hàng để cùng xem thông tin, thêm ghi chú và trao đổi nội bộ nhưng không có quyền thay đổi giá trị, chiết khấu hoặc chuyển giai đoạn bán hàng.

**Quy trình Phê duyệt Chiết khấu (Discount Approval Workflow)**:
Quy trình kiểm soát giá bán yêu cầu nhân viên kinh doanh phải gửi yêu cầu và nhận được sự phê duyệt của Quản lý bán hàng hoặc Giám đốc trước khi áp dụng mức chiết khấu vượt trần quy định cho khách hàng.

## Quản lý Vé Hỗ trợ & Dịch vụ Khách hàng (Tickets & Customer Service)

**Vé Hỗ trợ (Ticket / Support Case)**:
Thực thể đại diện cho một yêu cầu trợ giúp, phản ánh sự cố kỹ thuật, thắc mắc hoặc khiếu nại của khách hàng gửi tới doanh nghiệp qua các kênh liên lạc (Email, Livechat, WhatsApp, Biểu mẫu, Điện thoại), có mã định danh duy nhất (ví dụ: `TK-10023`) và được theo dõi từ khi tiếp nhận đến khi xử lý hoàn tất.

**Cam kết Chất lượng Dịch vụ (SLA - Service Level Agreement)**:
Chính sách thỏa thuận về thời gian xử lý yêu cầu giữa doanh nghiệp và khách hàng, bao gồm hai chỉ số cốt lõi: **Thời hạn Phản hồi Đầu tiên (First Response Time Due)** và **Thời hạn Giải quyết Xong (Resolution Time Due)** tùy theo mức độ ưu tiên của vé.

**Vi phạm SLA (SLA Breach)**:
Trạng thái cảnh báo khi nhân viên hỗ trợ không phản hồi hoặc không giải quyết xong vé trong khoảng thời gian cam kết của chính sách SLA, dùng để kích hoạt quy trình leo thang quản lý (Escalation).

**Tạm dừng / Tiếp tục Tính giờ SLA (SLA Pause & Resume)**:
Cơ chế tự động đóng băng đồng hồ đếm ngược SLA khi vé chuyển sang trạng thái "Đang chờ khách hàng phản hồi" hoặc "Chờ bên thứ ba", và tiếp tục đếm giờ khi khách hàng phản hồi lại, đảm bảo tính công bằng khi đánh giá KPI nhân viên.

**Khảo sát Mức độ Hài lòng (CSAT - Customer Satisfaction Score)**:
Khảo sát đánh giá chất lượng dịch vụ (thang điểm 1-5 sao kèm nhận xét) được hệ thống tự động gửi tới khách hàng ngay sau khi vé hỗ trợ được đánh dấu Đã giải quyết (Resolved).

**Mã Phân loại Giải pháp (Resolution Code)**:
Danh mục nguyên nhân & giải pháp chuẩn hóa (ví dụ: *Đã hướng dẫn sử dụng, Đã sửa lỗi phần mềm, Lỗi do cấu hình người dùng, Hoàn tiền*) bắt buộc nhân viên hỗ trợ phải khai báo khi giải quyết vé để phục vụ phân tích chất lượng sản phẩm.

**Cấu trúc Vé Cha - Vé Con (Parent-Child Ticket Hierarchy)**:
Mô hình liên kết một sự cố lớn (Vé Cha / Major Incident) với nhiều yêu cầu khiếu nại của từng khách hàng riêng lẻ (Vé Con / Sub-tickets), cho phép cập nhật trạng thái và phản hồi hàng loạt tới tất cả các vé con khi sự cố cha được khắc phục.

**Gộp Vé Hỗ trợ (Ticket Merge)**:
Thao tác hợp nhất các vé hỗ trợ trùng lặp từ cùng một khách hàng về một vé duy nhất (Master Ticket), chuyển toàn bộ lịch sử trao đổi và đóng vé phụ để tránh trùng lặp công việc cho đội ngũ hỗ trợ.

**Ghi chú Nội bộ vs Phản hồi Công khai (Internal Note vs Public Reply)**:
Hai chế độ trao đổi trên vé hỗ trợ: *Ghi chú Nội bộ* chỉ hiển thị cho nhân viên trong công ty để phối hợp xử lý; *Phản hồi Công khai* sẽ gửi trực tiếp thông điệp tới khách hàng qua email/kênh chat.

**Lịch làm việc trong Cam kết SLA (SLA Operating Hours & Business Calendar)**:
Khung thời gian được tính vào đồng hồ đếm ngược SLA, hỗ trợ 2 chế độ: Hỗ trợ liên tục 24/7 (mọi ngày, kể cả ngày nghỉ/lễ) cho mức độ Khẩn cấp; và Giờ hành chính 8x5 (08:00 - 17:30 Thứ 2 - Thứ 6) cho các mức độ thông thường, tự động tạm dừng tính giờ vào ban đêm, cuối tuần và các ngày lễ quốc gia.

**Phân bổ Vé Tự động (Ticket Auto-Assignment)**:
Quy tắc tự động gán vé hỗ trợ mới tiếp nhận cho nhóm kỹ năng chuyên môn phù hợp và điều phối theo thuật toán chia đều (Round-robin) dựa trên khối lượng công việc hiện tại của nhân viên hỗ trợ.

**Trạng thái Chờ Bên thứ ba (Pending 3rd Party)**:
Trạng thái đặt lên vé hỗ trợ khi tiến độ giải quyết phụ thuộc vào phản hồi từ nhà cung cấp bên ngoài (Vendor, Nhà mạng viễn thông, Đối tác vận chuyển), cho phép tự động tạm dừng đồng hồ tính SLA để không phạt oan nhân viên hỗ trợ.

**Ma trận Leo thang SLA (SLA Escalation Matrix)**:
Bộ quy tắc phân cấp hành động tự động (gửi cảnh báo quản lý, tự động chuyển quyền sở hữu vé, gắn cờ ưu tiên khẩn cấp) khi một vé hỗ trợ tiếp cận hoặc vượt quá giới hạn thời gian cam kết chất lượng dịch vụ.

## Quản lý Công việc & Hoạt động (Tasks & Activities)

**Công việc / Tác vụ (Task / To-do)**:
Thực thể đại diện cho một hành động cần hoàn thành của nhân viên (gọi điện thoại, gửi báo giá, họp trực tuyến, demo sản phẩm, ký hợp đồng), có tiêu đề, mô tả, hạn chót (`dueDate`), mức độ ưu tiên và người chịu trách nhiệm thực thi (`ownerId`).

**Loại Hoạt động Bán hàng (Activity Type)**:
Phân loại chuẩn hóa các hình thức tương tác với khách hàng: **Cuộc gọi (Call)**, **Email**, **Cuộc họp (Meeting)**, **Trình diễn Sản phẩm (Demo)**, **Chăm sóc Tiếp theo (Follow-up)**, **Việc cần làm (To-do)**.

**Công việc Lặp lại Định kỳ (Recurring Task)**:
Quy tắc tự động sinh ra công việc mới theo chu kỳ định sẵn (Hằng ngày / Hằng tuần / Hằng tháng / Hằng năm) sau khi công việc kỳ trước được đánh dấu Hoàn tất hoặc theo lịch cố định (ví dụ: Chăm sóc khách hàng VIP định kỳ ngày 15 hằng tháng).

**Hạn chót & Nhắc nhở Tự động (Due Date & Task Reminder)**:
Mốc thời gian cam kết hoàn thành công việc kèm thời điểm phát chuông thông báo nhắc nhở (`reminderAt`) trước thời hạn (15 phút, 1 giờ, 1 ngày) để nhân viên không bao giờ bỏ sót công việc quan trọng.

**Trạng thái Công việc (Task Lifecycle Status)**:
Vòng đời thực thi của một nhiệm vụ: **Chờ thực hiện (`PENDING`)** -> **Đang thực hiện (`IN_PROGRESS`)** -> **Đã hoàn thành (`COMPLETED`)** hoặc **Đã hủy (`CANCELLED`)**.

**Liên kết Đa Thực thể (Multi-Entity Association)**:
Khả năng gắn một công việc vào đồng thời nhiều thực thể nghiệp vụ liên quan: vừa thuộc về một Khách hàng cá nhân (`contactId`), vừa thuộc Doanh nghiệp (`accountId`), vừa phục vụ Cơ hội bán hàng (`dealId`) hoặc xử lý Vé hỗ trợ (`ticketId`).

**Nhật ký Hoạt động (Activity Feed)**:
Luồng dữ liệu lưu vết chi tiết từng sự kiện tương tác phát sinh (Ai đã gọi cho ai lúc mấy giờ, kết quả cuộc gọi ra sao, email đã gửi với nội dung gì) để toàn bộ đội ngũ nắm bắt tiến độ công việc chung.

**Người theo dõi Công việc (Task Watcher / Collaborator)**:
Thành viên nội bộ được gắn vào công việc để theo dõi tiến độ và nhận thông báo khi công việc hoàn thành hoặc thay đổi hạn chót mà không phải là người trực tiếp chịu trách nhiệm thực thi.

**Danh sách Kiểm tra Công việc (Task Checklist)**:
Tập hợp các đầu mục việc con cần hoàn thành bên trong một nhiệm vụ chính, cho phép theo dõi tiến độ % và áp dụng ràng buộc bắt buộc hoàn thành tất cả các mục trước khi đóng nhiệm vụ.

## Quản lý Chiến dịch Tiếp thị & Truyền thông Đa kênh (Marketing Campaigns)

**Chiến dịch Tiếp thị Đa kênh (Marketing Campaign)**:
Thực thể đại diện cho một đợt phát sóng thông điệp hàng loạt (quảng bá sản phẩm, bản tin ưu đãi, thông báo bảo trì, chúc mừng sinh nhật) tới một tập đối tượng khách hàng mục tiêu thông qua các kênh Email, WhatsApp, Zalo hoặc SMS.

**Kênh Phát sóng Tiếp thị (Campaign Broadcast Channels)**:
Các phương thức truyền thông được hỗ trợ trong chiến dịch: **Email Marketing**, **WhatsApp Broadcast**, **Zalo ZNS / Zalo OA**, **SMS Brandname**.

**Phân khúc Khách hàng Mục tiêu (Audience Segmentation)**:
Bộ lọc động kết hợp nhiều tiêu chí linh hoạt (Thẻ phân loại, Giai đoạn vòng đời, Điểm tiềm năng, Trường tùy biến, Khu vực địa lý) để xác định danh sách khách hàng nhận tin.

**Số lượng Tiếp cận Khả dụng (Estimated Reachable Audience)**:
Chỉ số tính toán số lượng khách hàng thực tế có thể nhận tin nhắn sau khi đã tự động loại trừ các địa chỉ bị hỏng (`BOUNCED`), khách hàng đã từ chối nhận tin (`OPT_OUT`) hoặc thiếu định danh hợp lệ của kênh tương ứng.

**Gửi Thử nghiệm (Test Send)**:
Tính năng cho phép người tạo chiến dịch gửi trước 1 tin nhắn thử nghiệm tới địa chỉ cá nhân của mình để kiểm tra hiển thị nội dung, hình ảnh và nút liên kết thực tế trước khi bấm phát sóng chính thức.

**Sổ cái Người nhận Tin (Campaign Send Ledger)**:
Bảng lưu trữ chi tiết nhật ký trạng thái gửi tới từng khách hàng riêng lẻ (`PENDING`, `SENT`, `DELIVERED`, `OPENED`, `CLICKED`, `FAILED`, `BOUNCED`, `REFUSED`) kèm lý do lỗi chi tiết nếu gửi không thành công.

**Chỉ số Đo lường Hiệu quả Tiếp thị (Campaign Performance Metrics)**:
Tập hợp các chỉ số theo dõi thời gian thực: Tỷ lệ gửi thành công (Delivery Rate), Tỷ lệ mở xem (Open Rate), Tỷ lệ nhấp liên kết (Click-Through Rate - CTR), Tỷ lệ hỏng (Bounce Rate) và Tỷ lệ hủy nhận tin (Unsubscribe Rate).

**Cơ chế Chống Thư rác & Hủy Đăng ký (Anti-Spam & Unsubscribe Compliance)**:
Quy chuẩn bắt buộc tự động chèn liên kết hủy đăng ký (Unsubscribe Link) vào chân trang email và cơ chế tự động chặn gửi tới những khách hàng đã hủy nhận tin theo chuẩn GDPR/CAN-SPAM.

**Hủy nhận tin theo từng Kênh riêng biệt (Channel-Specific Opt-out)**:
Cơ chế cho phép khách hàng hủy nhận tin trên một kênh cụ thể (ví dụ: không nhận Email quảng cáo) nhưng vẫn duy trì đồng thuận nhận tin qua các kênh khác (Zalo, SMS, WhatsApp), tránh việc mất liên lạc hoàn toàn với khách hàng.

**Giới hạn Tần suất Tiếp cận (Frequency Capping / Anti-Fatigue)**:
Quy tắc giới hạn số lượng thông điệp tiếp thị tối đa mà một khách hàng có thể nhận trong một khoảng thời gian (ví dụ: tối đa 2 tin/tuần/kênh), tự động loại trừ các khách hàng đã chạm ngưỡng khỏi tệp phát sóng để bảo vệ trải nghiệm khách hàng.

**Dự phòng Kênh Gửi Tin (Channel Fallback)**:
Cơ chế tự động chuyển hướng gửi tin nhắn sang kênh thay thế dự phòng (ví dụ: Zalo gửi thất bại -> chuyển sang SMS -> chuyển sang Email) khi kênh phát sóng chính gặp lỗi kỹ thuật hoặc bị từ chối phát sóng.

**Khử trùng lặp Danh sách Người nhận (Audience Deduplication)**:
Cơ chế tự động loại trừ các bản ghi trùng lặp trong tệp phát sóng khi một khách hàng thuộc về nhiều phân khúc (segments) khác nhau trong cùng một chiến dịch, đảm bảo khách hàng chỉ nhận tối đa 1 tin nhắn duy nhất.

**Phê duyệt Kép Chiến dịch (Dual Approval / Four-Eyes Principle)**:
Chính sách an toàn truyền thông bắt buộc người duyệt phát sóng chiến dịch phải là một nhân sự quản lý độc lập khác với người biên soạn bản nháp, ngăn ngừa rủi ro phát sóng nhầm nội dung sai lệch ra diện rộng.

