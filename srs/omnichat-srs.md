# SRS — Omnichat

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — vừa mô tả hành vi hiện tại, vừa là chuẩn cho phát triển tiếp theo (xem "Ghi chú về nguồn gốc tài liệu") |
| **Module** | Omnichat — tiếp nhận, phân công, xử lý và trả lời hội thoại khách hàng qua nhiều kênh (Facebook, Instagram, WhatsApp, Zalo, TikTok, Telegram, Email, Live Chat website) |
| **Ngày viết** | 2026-08-22 |
| **Neo phiên bản hệ thống** | `crm-api` @ `6cb0f24657fbaa856b149df59905925562eb5416` · `crm-web` @ `e65eb38aca4993ff00d2e94d56387746f0ba22f3` · `livechat-widget` @ `acc00d01e10bebd92414207496578ea3cd29c21f` (2026-08-22) — tài liệu mô tả hành vi hệ thống tại các commit này, không tự động đúng mãi mãi |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary, mục "Omnichat") |

## Ghi chú về nguồn gốc tài liệu

Omnichat đã được xây dựng và đưa vào vận hành mà chưa từng có SRS. Bản gốc được viết bằng hai bước: (1) khảo sát hành vi hệ thống hiện tại qua đọc trực tiếp mã nguồn triển khai, sau đó (2) một vòng review với BA/PM để đối chiếu hành vi đó với kỳ vọng nghiệp vụ đúng của một hệ thống contact center, chốt lại những điểm cần thay đổi. Đây không phải tài liệu thiết kế kỹ thuật — không mô tả cơ sở dữ liệu, tên hàm, tên file, hay công nghệ hạ tầng, và không phải một vòng audit toàn diện: các mục `[Đã triển khai]` phản ánh đúng hiện trạng đã khảo sát qua đọc code; các mục/quy tắc `[Yêu cầu mới]` là các điểm BA/PM đã chốt bổ sung ở vòng review này; phần còn lại của tài liệu chưa được rà lại từng dòng.

**Quy ước nhãn trạng thái:** mỗi tính năng (FEAT) được gắn nhãn ngay sau tiêu đề:

- **[Đã triển khai]** — hành vi đã được xác minh khớp với hệ thống đang chạy tại các commit neo ở trên.
- **[Yêu cầu mới]** — đã được BA/PM chốt phương án nhưng hệ thống hiện tại chưa phản ánh đúng; đội phát triển cần lên kế hoạch xây dựng. Tham chiếu GitHub issue tương ứng (nếu có) được ghi ở cuối mục, dưới dòng **Tham chiếu**.

Trong một FEAT đã `[Đã triển khai]`, nếu một quy tắc nghiệp vụ (BR) cụ thể là quyết định mới, BR đó được đánh dấu riêng `[Yêu cầu mới]` ngay sau mã số — các BR không có nhãn kế thừa trạng thái của FEAT chứa nó. Với thay đổi tính năng trong tương lai, tài liệu này cần được cập nhật song song (và neo lại commit mới), không để trôi khỏi thực tế vận hành.

---

## 1. Giới thiệu

### 1.1 Mục đích

Tài liệu đặc tả toàn bộ yêu cầu chức năng và phi chức năng của module **Omnichat** — trung tâm tiếp nhận và xử lý hội thoại của một tổng đài đa kênh (contact center), cho phép doanh nghiệp tiếp nhận, phân công, trả lời và theo dõi chất lượng phục vụ khách hàng nhắn tin qua nhiều kênh khác nhau trong cùng một nơi làm việc duy nhất.

### 1.2 Phạm vi

Tài liệu bao trùm toàn bộ hành trình một hội thoại: kết nối kênh giao tiếp, tiếp nhận và nhận diện khách hàng, tự động phân công cho Agent, hàng đợi và lời mời nhận việc, chuyển tiếp giữa các Agent, cam kết thời gian phản hồi (SLA) và leo thang khi vi phạm, trả lời tự động bằng Bot và bàn giao cho người, gửi tin nhắn đi, tự động đóng hội thoại không hoạt động, khảo sát hài lòng khách hàng (CSAT), ghi chú/lịch sử xử lý, tìm kiếm tin nhắn, cấu hình hộp thư và mẫu tin nhắn nhanh, báo cáo vận hành và hiệu suất, giao diện xử lý hội thoại của Agent, và trải nghiệm trò chuyện của khách hàng trên widget Live Chat nhúng website.

**Ngoài phạm vi:**

- Cách kênh được cấu hình xác thực/bảo mật kỹ thuật với từng nhà cung cấp (Facebook, WhatsApp...) — đây là chi tiết triển khai, tài liệu này chỉ mô tả hành vi nghiệp vụ mà quản trị viên/Agent nhìn thấy.
- Chiến dịch gửi tin nhắn hàng loạt (Campaign/Broadcast) — chưa tồn tại trong hệ thống, xem Mục 7.
- Engine phân công việc dùng chung cho các đối tượng nghiệp vụ khác của CRM (Liên hệ, Tài khoản, Cơ hội, Ticket, Công việc) — Omnichat dùng chung hạ tầng phân công này cho việc gán hội thoại, nhưng tài liệu này chỉ đặc tả phần hành vi áp dụng cho hội thoại; hành vi phân công cho các đối tượng khác thuộc phạm vi một SRS riêng (xem Mục 7).
- Trung tâm cuộc gọi thoại (Voice/Call Center) — hiện chưa là một kênh của Omnichat.

### 1.3 Đối tượng đọc

- Business Analyst / Product Owner: hiểu đúng hành vi hiện tại trước khi đề xuất thay đổi.
- QA: làm căn cứ viết test case chấp nhận.
- Trưởng nhóm/Giám sát viên contact center: hiểu năng lực vận hành thực tế của hệ thống (định tuyến, SLA, báo cáo) khi tư vấn quy trình cho khách hàng doanh nghiệp.
- Kỹ sư phát triển: hiểu ý định nghiệp vụ trước khi đọc code — chi tiết triển khai kỹ thuật không nằm trong tài liệu này.

### 1.4 Thuật ngữ & viết tắt

| Thuật ngữ | Giải thích |
| --- | --- |
| **Hội thoại (Conversation)** | Một phiên trao đổi tin nhắn giữa một khách hàng và doanh nghiệp, gắn với đúng một kênh. Có vòng đời riêng: Đang mở, Tạm hoãn, Chờ đóng, Đã giải quyết, Đã đóng. |
| **Kênh (Channel)** | Một kết nối cụ thể tới một nền tảng giao tiếp (một Trang Facebook, một số WhatsApp, một hộp thư email...), thuộc về đúng một doanh nghiệp (tenant). |
| **Hộp thư/Nhóm hội thoại (Inbox)** | Một nhóm định tuyến gộp nhiều kênh và một tập Agent/nhóm được phép xử lý, có thể có chính sách định tuyến/SLA/Bot riêng khác với mặc định của doanh nghiệp. |
| **Hồ sơ khách hàng tạm** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Lời mời nhận hội thoại** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Ưu tiên người phụ trách trước đó** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Cửa sổ phản hồi** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Ngưỡng an toàn đóng hàng loạt** | Xem [`CONTEXT.md`](../CONTEXT.md). |
| **Cam kết thời gian phản hồi (SLA)** | Thời hạn nội bộ mà doanh nghiệp tự đặt ra cho việc phản hồi/giải quyết một hội thoại, dùng để đo và cảnh báo khi trễ hẹn — không phải cam kết hợp đồng với khách hàng. |
| **Leo thang (Escalation)** | Hành động tự động (cảnh báo, thông báo cấp quản lý, chuyển việc) được kích hoạt khi một hội thoại vi phạm SLA. |
| **Tự động đóng hội thoại (Auto-close)** | Cơ chế tự động chuyển một hội thoại không có hoạt động trong một khoảng thời gian sang trạng thái Đã giải quyết/Đã đóng, có cảnh báo trước cho khách hàng. |
| **Bot** | Kịch bản trả lời tự động (xây dựng ở một công cụ riêng ngoài Omnichat) có thể tiếp nhận và trả lời hội thoại thay Agent cho tới khi bàn giao lại cho người hoặc kết thúc. |
| **Bàn giao (Handoff)** | Thời điểm Bot chuyển quyền xử lý một hội thoại sang cho một Agent, một nhóm cụ thể, hoặc hàng đợi chung. |
| **CSAT (Customer Satisfaction)** | Điểm hài lòng (1–5) khách hàng tự chấm sau khi hội thoại được giải quyết. |
| **Mẫu tin nhắn nhanh (Canned Response)** | Nội dung soạn sẵn Agent có thể chèn nhanh vào hội thoại thay vì gõ lại từ đầu. |
| **Thời gian xử lý trung bình (AHT – Average Handle Time)** | Thời lượng trung bình một Agent dành để xử lý xong một hội thoại/công việc, dùng để hoạch định nhân sự. |

### 1.5 Tài liệu tham khảo

- [`CONTEXT.md`](../CONTEXT.md) — glossary thuật ngữ nghiệp vụ dùng chung cho các SRS trong `product-management`, mục "Omnichat".

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

Khách hàng ngày nay liên hệ doanh nghiệp qua rất nhiều kênh khác nhau — Facebook, Instagram, WhatsApp, Zalo, TikTok, Telegram, email, hoặc khung chat ngay trên website — và kỳ vọng được phản hồi nhanh, nhất quán, dù họ chọn kênh nào. Nếu mỗi kênh phải xử lý ở một công cụ riêng, doanh nghiệp không thể đảm bảo tốc độ phản hồi, dễ bỏ sót tin nhắn, và không có cách nào đo lường chất lượng phục vụ một cách tổng thể.

Omnichat giải quyết vấn đề này bằng cách gộp mọi kênh giao tiếp vào **một nơi làm việc duy nhất cho Agent**, tự động phân phối hội thoại đến đúng người có khả năng xử lý, đo lường thời gian phản hồi và mức độ hài lòng của khách hàng, và tự động xử lý những phần việc lặp lại (trả lời ngoài giờ, đóng hội thoại đã xong việc, nhắc nhở khi sắp trễ hẹn) để Agent tập trung vào việc trò chuyện thực sự.

### 2.2 Các kênh giao tiếp được hỗ trợ

| Kênh | Đặc điểm nghiệp vụ |
| --- | --- |
| **Facebook** | Tin nhắn qua Trang Facebook (Messenger). |
| **Instagram** | Tin nhắn qua tài khoản doanh nghiệp Instagram, bao gồm cả nhắc tên trong story và chia sẻ bài viết. |
| **WhatsApp** | Tin nhắn qua số WhatsApp Business; danh tính khách hàng chính là số điện thoại của họ. |
| **Zalo** | Tin nhắn qua Official Account (OA); liên kết media của Zalo hết hạn nhanh hơn các kênh khác nên hệ thống tự sao lưu lại. |
| **TikTok** | Tin nhắn trực tiếp qua tài khoản doanh nghiệp TikTok. |
| **Telegram** | Tin nhắn qua bot Telegram của doanh nghiệp. |
| **Email** | Nhận/gửi qua hộp thư doanh nghiệp; không giới hạn thời gian phản hồi. |
| **Live Chat** | Khung chat nhúng trực tiếp trên website doanh nghiệp; không giới hạn thời gian phản hồi. |

Trừ Email và Live Chat, các kênh nhắn tin còn lại đều có **Cửa sổ phản hồi** giới hạn 24 giờ kể từ tin nhắn cuối của khách hàng — sau đó Agent chỉ có thể chủ động liên hệ lại bằng tin nhắn mẫu đã được nền tảng kênh đó phê duyệt trước (hiện chỉ WhatsApp hỗ trợ loại tin nhắn mẫu này).

### 2.3 Vai trò người dùng (Actor)

| Actor | Vai trò trong module |
| --- | --- |
| **Khách hàng (Contact)** | Người nhắn tin qua một trong các kênh trên. Có thể là khách hàng đã có hồ sơ trong CRM, hoặc người lạ mới nhắn tin lần đầu (xem Hồ sơ khách hàng tạm). |
| **Agent** | Nhân viên trực tiếp trò chuyện, xử lý hội thoại: nhận việc, trả lời, chuyển tiếp, ghi chú, giải quyết/đóng hội thoại. |
| **Giám sát viên (Supervisor)** | Theo dõi hàng đợi và hiệu suất đội nhóm, nhận cảnh báo leo thang, có thể chuyển tiếp/gán lại hội thoại vượt quyền Agent thường, xem báo cáo. |
| **Quản trị viên (Admin)** | Cấu hình kênh, hộp thư, quy tắc phân công, chính sách SLA/leo thang/tự động đóng, mẫu tin nhắn. |
| **Bot** | Hệ thống trả lời tự động, tiếp nhận và xử lý hội thoại trước khi bàn giao cho người (nếu cần). |

### 2.4 Nhóm tính năng

1. Kết nối kênh giao tiếp
2. Tiếp nhận & nhận diện khách hàng
3. Vòng đời hội thoại
4. Tự động phân công hội thoại
5. Hàng đợi & lời mời nhận hội thoại
6. Trạng thái làm việc của Agent
7. Chuyển tiếp hội thoại
8. Cam kết thời gian phản hồi (SLA)
9. Leo thang khi vi phạm SLA
10. Tự động đóng hội thoại không hoạt động
11. Bot trả lời tự động & bàn giao cho Agent
12. Gửi tin nhắn cho khách hàng
13. Cập nhật thời gian thực cho Agent
14. Khảo sát hài lòng khách hàng (CSAT)
15. Ghi chú, cảm xúc & lịch sử xử lý
16. Tìm kiếm nội dung tin nhắn
17. Quản lý hộp thư (Inbox)
18. Mẫu tin nhắn nhanh (Canned Response)
19. Báo cáo vận hành Omnichannel
20. Báo cáo hiệu suất Agent
21. Giao diện xử lý hội thoại của Agent
22. Trải nghiệm trò chuyện của khách hàng trên Live Chat Widget

---

## 3. Đặc tả yêu cầu chức năng

### FEAT-01 — Kết nối kênh giao tiếp `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép quản trị viên kết nối tài khoản doanh nghiệp trên từng nền tảng (Facebook, Instagram, WhatsApp, Email) vào hệ thống, để bắt đầu tiếp nhận và trả lời tin nhắn của khách hàng qua kênh đó.

**Actor:** Quản trị viên.

**Luồng chính:**

1. Quản trị viên chọn loại kênh muốn kết nối.
2. Với Facebook/Instagram/WhatsApp: đăng nhập xác thực với nền tảng đó, chọn đúng Trang/tài khoản doanh nghiệp muốn kết nối, xác nhận.
3. Với Email: nhập thông tin hộp thư (địa chỉ, thông tin xác thực gửi/nhận).
4. Hệ thống xác nhận kết nối thành công, kênh chuyển sang trạng thái Đã kết nối và bắt đầu tiếp nhận tin nhắn.

**Quy tắc nghiệp vụ:**

- BR-01.1: Một kênh PHẢI thuộc đúng một doanh nghiệp tại một thời điểm — không được kết nối cùng một tài khoản kênh cho hai doanh nghiệp khác nhau.
- BR-01.2: Kênh PHẢI có 4 trạng thái: Đang chờ kết nối, Đã kết nối, Đã ngắt kết nối, Lỗi kết nối.
- BR-01.3: Hệ thống PHẢI tự động kiểm tra định kỳ tình trạng hoạt động của từng kênh đang kết nối, và cảnh báo cho quản trị viên khi kênh mất kết nối hoặc quyền truy cập sắp/đã hết hạn.
- BR-01.4: Ngắt kết nối một kênh PHẢI là thao tác có thể phục hồi (kết nối lại); xóa hẳn một kênh PHẢI yêu cầu ngắt kết nối trước.
- BR-01.5: Quản trị viên PHẢI cấu hình được theo từng kênh: tin nhắn tự động trả lời, giờ làm việc riêng (nếu khác giờ làm việc chung), và quy tắc phân công mặc định riêng cho kênh đó.
- BR-01.6: Với kênh Email, quản trị viên PHẢI cấu hình được riêng thông tin gửi/nhận thư.
- BR-01.7: Thông tin xác thực đăng nhập của từng kênh PHẢI được bảo mật, không bao giờ hiển thị lại cho quản trị viên sau khi đã lưu.

**Tiêu chí chấp nhận:**

- Kênh mới kết nối chuyển sang "Đã kết nối" và có thể nhận tin nhắn khách hàng ngay.
- Kênh mất kết nối/hết hạn quyền truy cập luôn tạo cảnh báo cho quản trị viên, không âm thầm ngừng hoạt động.

---

### FEAT-02 — Tiếp nhận & nhận diện khách hàng qua các kênh `[Đã triển khai]`

**Mô tả nghiệp vụ:** Nhận tin nhắn khách hàng gửi tới từ bất kỳ kênh nào trong 8 kênh hỗ trợ, hiển thị thống nhất trong một khung chat, và xác định đúng khách hàng đó là ai trong hệ thống CRM (nếu đã có hồ sơ).

**Actor:** Khách hàng (nguồn tin nhắn); hệ thống thực hiện tự động; Agent là người nhìn thấy kết quả nhận diện và có thể can thiệp.

**Luồng chính:**

1. Khách hàng gửi tin nhắn (văn bản, ảnh, video, tệp, vị trí, nhãn dán, hoặc bấm một nút tương tác) qua một trong 8 kênh.
2. Hệ thống xác thực tin nhắn thực sự đến từ đúng nền tảng kênh đó (chống giả mạo).
3. Hệ thống chuẩn hóa nội dung về định dạng hiển thị thống nhất.
4. Hệ thống tìm kiếm khách hàng đã có hồ sơ khớp với người gửi (theo liên kết đã lưu từ trước, hoặc theo email/số điện thoại tùy kênh).
5. Nếu tìm thấy, gắn tin nhắn vào đúng hồ sơ khách hàng đó; nếu không, tạo một Hồ sơ khách hàng tạm.
6. Tin nhắn được gắn vào hội thoại đang mở của khách hàng đó, hoặc tạo hội thoại mới nếu chưa có.

**Quy tắc nghiệp vụ:**

- BR-02.1: Hệ thống PHẢI xác thực mọi tin nhắn đến thực sự từ đúng nền tảng kênh trước khi xử lý, để chặn tin nhắn giả mạo.
- BR-02.2: Một tin nhắn được nền tảng kênh gửi lại nhiều lần do lỗi mạng (khi họ không chắc lần gửi trước có thành công không) PHẢI chỉ được ghi nhận đúng một lần trong hệ thống.
- BR-02.3: Với kênh WhatsApp, danh tính người gửi **chính là số điện thoại** của họ; hệ thống PHẢI tự động liên kết/gộp vào đúng hồ sơ khách hàng có cùng số điện thoại này — đây là cách nhận diện đáng tin cậy duy nhất cho kênh này.
- BR-02.4 `[Yêu cầu mới]`: Với mọi kênh khác ngoài WhatsApp (Facebook, Instagram, Zalo, TikTok, Email), hệ thống KHÔNG ĐƯỢC tự động gộp Hồ sơ khách hàng tạm vào một hồ sơ khách hàng có sẵn chỉ vì trùng số điện thoại/email. Thay vào đó, hệ thống PHẢI hiển thị gợi ý khớp cho Agent (ví dụ: "Có vẻ đây là [Tên khách hàng] đã có trong hệ thống") và chỉ thực hiện gộp khi Agent bấm xác nhận. Lý do: số điện thoại/email trùng nhau trên các kênh này không đủ tin cậy để tự động gộp — có thể là số hotline dùng chung, email của một công ty đối tác — nên rủi ro làm sai lệch dữ liệu khách hàng nếu gộp nhầm là không thể chấp nhận được.
- BR-02.5: Khi chưa nhận diện được khách hàng nào khớp, hệ thống PHẢI tự tạo một Hồ sơ khách hàng tạm để hội thoại có nơi gắn vào; Agent CÓ THỂ tự liên kết hội thoại đó với một hồ sơ CRM có sẵn, hoặc tạo hồ sơ CRM chính thức mới, bất kỳ lúc nào trong lúc trò chuyện.
- BR-02.6: Tệp đính kèm (ảnh/video/tài liệu) từ khách hàng PHẢI được sao lưu vào hệ thống lưu trữ của doanh nghiệp — vì liên kết gốc do một số nền tảng kênh cung cấp (đặc biệt Zalo) hết hạn rất nhanh, không thể dùng để xem lại lâu dài.
- BR-02.7: Nếu doanh nghiệp đang ngoài giờ làm việc và chưa có Agent nào xử lý hội thoại, hệ thống CÓ THỂ tự động gửi tin nhắn thông báo ngoài giờ cho khách hàng, theo cấu hình của doanh nghiệp/kênh.

**Tiêu chí chấp nhận:**

- Tin nhắn từ cả 8 kênh đều hiển thị được trong cùng một khung chat thống nhất cho Agent.
- Gửi lại một tin nhắn do lỗi mạng không tạo ra tin nhắn trùng lặp trên khung chat.
- Khách hàng WhatsApp từng nhắn tin trước đây luôn được nhận diện đúng là cùng một người khi nhắn lại.
- Khách hàng nhắn qua Facebook có số điện thoại trùng với một hồ sơ có sẵn **không** tự động gộp — chỉ hiển thị gợi ý cho Agent xác nhận.

**Tham chiếu:** BR-02.4 → issue [#15](https://github.com/crmsaassaudi/product-management/issues/15).

---

### FEAT-03 — Vòng đời hội thoại `[Đã triển khai]`

**Mô tả nghiệp vụ:** Mỗi hội thoại có một trạng thái rõ ràng phản ánh nó đang cần xử lý hay đã xong, giúp Agent và Giám sát viên biết việc gì cần làm và tránh bỏ sót.

**Actor:** Agent (thay đổi trạng thái thủ công); hệ thống (thay đổi trạng thái tự động theo quy tắc).

**Các trạng thái:** Đang mở, Tạm hoãn, Chờ đóng (đang trong thời gian ân hạn trước khi tự động đóng), Đã giải quyết, Đã đóng.

**Luồng chính:**

1. Hội thoại mới của một khách hàng bắt đầu ở trạng thái Đang mở.
2. Agent xử lý xong, đánh dấu Đã giải quyết (hoặc hệ thống tự động đóng — xem FEAT-10).
3. Nếu khách hàng nhắn tin lại: hội thoại đã Đã giải quyết được mở lại (nếu còn trong thời hạn cho phép) hoặc một hội thoại mới được tạo; hội thoại đã Đã đóng luôn tạo hội thoại mới.

**Quy tắc nghiệp vụ:**

- BR-03.1: Hội thoại mới của một khách hàng PHẢI bắt đầu ở trạng thái Đang mở.
- BR-03.2: Khi khách hàng nhắn tin lại vào một hội thoại Đã giải quyết, hệ thống PHẢI mở lại đúng hội thoại đó nếu còn trong khoảng thời gian cho phép mở lại (theo chính sách tự động đóng đã áp dụng cho hội thoại đó), hoặc tạo một hội thoại mới nếu đã quá hạn hoặc chính sách quy định luôn tạo hội thoại mới.
- BR-03.3: Khách hàng nhắn tin lại vào một hội thoại Đã đóng PHẢI luôn tạo một hội thoại mới — không bao giờ mở lại hội thoại đã đóng.
- BR-03.4: Khi một hội thoại được mở lại, hệ thống PHẢI xóa các thông tin giải quyết trước đó (người giải quyết, lý do, ghi chú giải quyết) và tự động tìm Agent xử lý nếu hội thoại chưa có ai đang phụ trách.
- BR-03.5 `[Yêu cầu mới]`: Hệ thống PHẢI lưu lại lý do cụ thể ngay trên chính hội thoại mỗi khi nó chuyển sang trạng thái Tạm hoãn (ví dụ: Agent tự tạm ẩn, ngoài giờ làm việc, hủy chờ đóng do không có hoạt động mới), để tra cứu/báo cáo được ngay từ hội thoại đó — hiện tại lý do chỉ được ghi vào nhật ký vận hành nội bộ, không tra cứu được từ chính hội thoại.
- BR-03.6: Mọi tin nhắn trong một hội thoại PHẢI được hiển thị đúng theo thứ tự thời gian thực tế phát sinh, kể cả khi một tin nhắn đến muộn hơn do lỗi mạng.

**Tiêu chí chấp nhận:**

- Khách nhắn lại sau khi hội thoại "Đã giải quyết", trong hạn mở lại → tiếp tục đúng hội thoại cũ.
- Khách nhắn lại sau khi hội thoại "Đã đóng", hoặc đã quá hạn mở lại → luôn là hội thoại mới.
- Lý do tạm hoãn tra cứu được ngay trên hội thoại, không cần tìm trong nhật ký hệ thống riêng.

**Tham chiếu:** BR-03.5 → issue [#16](https://github.com/crmsaassaudi/product-management/issues/16).

---

### FEAT-04 — Tự động phân công hội thoại `[Đã triển khai]`

**Mô tả nghiệp vụ:** Hệ thống tự động chọn đúng Agent phù hợp nhất để xử lý một hội thoại mới, dựa trên năng lực hỗ trợ kênh, tải công việc hiện tại, và quy tắc riêng doanh nghiệp tự định nghĩa — đảm bảo khách hàng được phục vụ nhanh nhất có thể mà không cần Agent tự tìm việc.

**Actor:** Hệ thống (tự động thực hiện); Quản trị viên (cấu hình quy tắc); Agent (người nhận việc).

**Luồng chính:**

1. Có hội thoại mới cần phân công (hoặc cần gán lại do Agent trước đó mất kết nối).
2. Hệ thống đánh giá các quy tắc phân công doanh nghiệp đã cấu hình, theo đúng thứ tự ưu tiên.
3. Xác định nhóm/Agent đích theo quy tắc khớp đầu tiên, hoặc theo cấu hình mặc định nếu không quy tắc nào khớp.
4. Lọc trong số đó những Agent đang online, đủ điều kiện hỗ trợ đúng kênh của hội thoại, và còn khả năng nhận thêm việc.
5. Chọn một Agent theo chiến lược đã cấu hình, rồi gửi lời mời nhận hội thoại (xem FEAT-05).

**Quy tắc nghiệp vụ:**

- BR-04.1: Hệ thống PHẢI chỉ chọn Agent đang ở trạng thái sẵn sàng nhận việc và còn khả năng nhận thêm (chưa đạt giới hạn số hội thoại tối đa được xử lý cùng lúc) — quy tắc này áp dụng cho **cả 4 chiến lược phân công** dùng cho hội thoại, không có ngoại lệ.
- BR-04.2 (Ưu tiên người phụ trách trước đó): Khách hàng cũ quay lại PHẢI được ưu tiên gán cho đúng Agent đã phụ trách họ gần đây, nếu Agent đó còn khả năng nhận thêm việc; nếu Agent đó không còn khả năng nhận thêm hoặc đã quá lâu không tương tác, hệ thống chuyển sang các chiến lược phân công thông thường.
- BR-04.3: Doanh nghiệp PHẢI cấu hình được quy tắc phân công theo điều kiện tùy chỉnh (kênh, nội dung tin nhắn, phân khúc khách hàng, giờ làm việc...), có thứ tự ưu tiên rõ ràng; một quy tắc không có điều kiện nào PHẢI được coi là quy tắc mặc định, luôn khớp nếu không quy tắc nào khác khớp trước.
- BR-04.4: Hệ thống PHẢI hỗ trợ 4 cách chọn Agent trong số các ứng viên đủ điều kiện: theo vòng lần lượt, người đang ít việc nhất, theo tải trọng số công việc, hoặc chỉ đẩy vào hàng đợi chờ Agent tự nhận.
- BR-04.5: Nếu không có Agent nào đủ điều kiện tại thời điểm phân công, hội thoại PHẢI được đưa vào hàng đợi chờ (xem FEAT-05) — không được bỏ sót không xử lý gì.
- BR-04.6: Mọi quyết định phân công (thành công hay không, và vì lý do gì) PHẢI được ghi lại đầy đủ để tra cứu sau này.

**Tiêu chí chấp nhận:**

- Hội thoại mới luôn được gán cho một Agent hoặc đưa vào hàng đợi — không bao giờ "biến mất" mà không ai biết.
- Khách hàng quen quay lại được ưu tiên gặp đúng Agent cũ nếu Agent đó còn khả năng nhận thêm việc.
- Lịch sử phân công của từng hội thoại tra cứu được đầy đủ.

---

### FEAT-05 — Hàng đợi & lời mời nhận hội thoại `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi một hội thoại chưa có Agent xử lý ngay, hệ thống xếp vào hàng đợi và chủ động mời từng Agent phù hợp lần lượt, thay vì để hội thoại nằm im chờ ai đó tình cờ nhìn thấy.

**Actor:** Hệ thống (tự động điều phối); Agent (nhận/từ chối lời mời, hoặc tự chọn nhận từ hàng đợi); Giám sát viên (theo dõi, can thiệp khi cần).

**Quy tắc nghiệp vụ:**

- BR-05.1: Hội thoại trong hàng đợi PHẢI được sắp xếp theo độ ưu tiên và thời gian chờ; hội thoại chờ càng lâu PHẢI được tự động tăng dần độ ưu tiên, để không bị "chìm" phía cuối hàng đợi.
- BR-05.2: Khi có Agent phù hợp đang rảnh, hệ thống PHẢI gửi Lời mời nhận hội thoại tới đúng một Agent tại một thời điểm; Agent có một khoảng thời gian nhất định để nhận hoặc từ chối lời mời đó.
- BR-05.3: Agent bỏ lỡ hoặc từ chối lời mời PHẢI được loại khỏi lượt mời kế tiếp cho đúng hội thoại đó (trong cùng vòng mời), và hội thoại PHẢI tiếp tục được mời cho Agent phù hợp khác; sau tối đa 3 lượt mời không thành công, hội thoại quay lại chờ ở hàng đợi.
- BR-05.4: Doanh nghiệp CÓ THỂ chọn một trong ba cách phân phối hội thoại đang chờ: tự động gán thẳng (không cần Agent xác nhận), gửi lời mời để Agent tự nhận/từ chối (mặc định), hoặc chỉ hiển thị trong hàng đợi để Agent chủ động chọn nhận.
- BR-05.5: Agent CÓ THỂ chủ động tự nhận một hội thoại đang chờ trong hàng đợi, miễn còn đủ điều kiện (đúng kênh được phép hỗ trợ, còn khả năng nhận thêm việc).
- BR-05.6: Khi một hội thoại chờ quá lâu vượt ngưỡng cấu hình, hệ thống PHẢI cảnh báo cho Giám sát viên, và CÓ THỂ tự động chuyển hội thoại đó sang một nhóm khác đang rảnh hơn.
- BR-05.7: Mặc định, hội thoại đang chờ trong hàng đợi (chưa ai nhận) PHẢI bị ẩn nội dung khỏi Agent thường cho tới khi được nhận — trừ Giám sát viên hoặc người có quyền xem báo cáo toàn cục — để tránh nhiều Agent cùng đọc và tranh giành một hội thoại.
- BR-05.8: Ngoài giờ làm việc, nếu doanh nghiệp cấu hình không nhận hội thoại mới vào hàng đợi, hệ thống PHẢI xử lý theo kịch bản ngoài giờ (thông báo tự động) thay vì tạo hàng đợi chờ.

**Tiêu chí chấp nhận:**

- Hội thoại chờ lâu tự động tăng ưu tiên, không bị bỏ quên.
- Lời mời bị bỏ lỡ luôn được chuyển tiếp cho Agent khác, không treo vô thời hạn.
- Agent thường không nhìn thấy trước nội dung hội thoại đang chờ người khác nhận.

---

### FEAT-06 — Trạng thái làm việc của Agent `[Đã triển khai]`

**Mô tả nghiệp vụ:** Theo dõi Agent nào đang thực sự sẵn sàng nhận việc, để hệ thống chỉ phân công hội thoại cho người có thể xử lý ngay, và cung cấp số liệu cho báo cáo thời gian làm việc.

**Actor:** Agent.

**Quy tắc nghiệp vụ:**

- BR-06.1: Agent PHẢI tự chọn trạng thái làm việc của mình (ví dụ: Sẵn sàng, Vắng mặt, Nghỉ giải lao, Đang họp, Đang đào tạo); trạng thái Ngoại tuyến chỉ do hệ thống tự đặt khi phát hiện Agent mất kết nối.
- BR-06.2: Agent chỉ nhận được hội thoại mới khi đồng thời thỏa cả ba điều kiện: đang chọn trạng thái Sẵn sàng, đang thực sự kết nối vào hệ thống, và chưa đạt giới hạn số hội thoại tối đa được xử lý cùng lúc.
- BR-06.3: Khi Agent mất kết nối đột ngột (rớt mạng, đóng trình duyệt) mà chưa kịp tự đổi trạng thái, hệ thống PHẢI có một khoảng thời gian gia hạn ngắn trước khi coi Agent đó là ngoại tuyến hẳn — tránh việc vội vàng chuyển hội thoại đang xử lý sang người khác chỉ vì mất kết nối tức thời.
- BR-06.4: Mọi lần đổi trạng thái làm việc của Agent PHẢI được ghi lại, phục vụ báo cáo thời gian làm việc theo ngày (xem FEAT-20).

**Tiêu chí chấp nhận:**

- Agent đặt trạng thái "Vắng mặt" thì không nhận được hội thoại mới nữa.
- Mất kết nối mạng trong thời gian ngắn không làm hội thoại đang xử lý bị chuyển đi ngay lập tức.

---

### FEAT-07 — Chuyển tiếp hội thoại `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép Agent chuyển một hội thoại đang xử lý sang một Agent khác hoặc một nhóm khác, khi cần chuyên môn khác, đổi ca làm việc, hoặc muốn xin ý kiến đồng nghiệp mà không rời khỏi hội thoại.

**Actor:** Agent (nguồn và đích); Giám sát viên (có thể khởi tạo/nhận thay).

**Luồng chính:**

1. Agent đang phụ trách chọn "Chuyển tiếp", chọn Agent cụ thể hoặc một nhóm, chọn kiểu chuyển tiếp: **Chuyển hẳn** (không cần bên kia xác nhận, dùng khi chuyển cho một nhóm hoặc do Giám sát viên thực hiện), **Chuyển có xác nhận** (bên nhận phải đồng ý mới chính thức đổi người phụ trách), hoặc **Xin ý kiến** (mời một đồng nghiệp cùng xem/tư vấn, người chuyển vẫn giữ hội thoại).
2. Với "Chuyển có xác nhận"/"Xin ý kiến": Agent đích nhận được yêu cầu, có thời hạn để đồng ý hoặc từ chối.
3. Nếu đồng ý, quyền phụ trách hội thoại (hoặc quyền tư vấn tạm thời) chuyển sang Agent đích.

**Quy tắc nghiệp vụ:**

- BR-07.1: Hệ thống PHẢI hỗ trợ 3 kiểu chuyển tiếp: Chuyển hẳn, Chuyển có xác nhận, Xin ý kiến.
- BR-07.2: Yêu cầu chuyển tiếp PHẢI chỉ định rõ Agent đích hoặc nhóm đích; chuyển cho một nhóm (không chỉ định Agent cụ thể) PHẢI luôn là kiểu Chuyển hẳn.
- BR-07.3: Chỉ Agent đang phụ trách hội thoại hoặc người có quyền quản lý phân công (Giám sát viên) mới được khởi tạo chuyển tiếp.
- BR-07.4: Yêu cầu Chuyển có xác nhận/Xin ý kiến PHẢI có thời hạn phản hồi; hết hạn mà chưa phản hồi thì yêu cầu tự động hủy.
- BR-07.5: Agent đích chỉ được nhận chuyển tiếp nếu còn khả năng nhận thêm việc; nếu không đủ khả năng tại đúng thời điểm xác nhận, hệ thống PHẢI từ chối và báo cho cả hai bên.
- BR-07.6: Trong lúc "Xin ý kiến" đang diễn ra, Agent gốc PHẢI vẫn là người chịu trách nhiệm chính; khi kết thúc, Agent gốc chọn giữ nguyên hoặc chuyển hẳn quyền phụ trách sang người vừa tư vấn.
- BR-07.7: Một hội thoại chỉ được có tối đa một yêu cầu chuyển tiếp đang chờ xử lý tại một thời điểm.
- BR-07.8: Nếu hội thoại được giải quyết/đóng trong lúc đang có yêu cầu chuyển tiếp treo, yêu cầu đó PHẢI tự động hủy.

**Tiêu chí chấp nhận:**

- Chuyển hẳn cho một nhóm không cần ai xác nhận, hội thoại vào hàng đợi/được gán ngay theo quy tắc của nhóm đó.
- Chuyển có xác nhận chỉ đổi người phụ trách sau khi Agent đích đồng ý.
- Yêu cầu chuyển tiếp quá hạn tự hủy, không treo vô thời hạn.

---

### FEAT-08 — Cam kết thời gian phản hồi (SLA) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Đo và cảnh báo thời gian doanh nghiệp phản hồi/giải quyết hội thoại của khách hàng, theo cam kết nội bộ doanh nghiệp tự đặt ra, để đảm bảo tốc độ phục vụ nhất quán.

**Actor:** Hệ thống (đo lường tự động); Quản trị viên (cấu hình cam kết); Agent/Giám sát viên (theo dõi đếm ngược).

**Luồng chính:**

1. Quản trị viên định nghĩa Chính sách SLA: áp dụng cho hội thoại (hoặc Ticket), loại cam kết (phản hồi lần đầu, phản hồi các lần sau, hoặc giải quyết xong), thời hạn theo từng phân khúc khách hàng.
2. Hội thoại mới tự động được gắn cam kết phản hồi lần đầu và cam kết giải quyết xong.
3. Khi khách hàng nhắn tin, đồng hồ đếm ngược phản hồi bắt đầu/khởi động lại; khi Agent trả lời, đồng hồ đó dừng lại và ghi nhận đã đáp ứng hay đã vi phạm.
4. Khi hội thoại Tạm hoãn, mọi đồng hồ đang chạy tạm dừng; khi trở lại Đang mở, đồng hồ tiếp tục từ thời gian còn lại.

**Quy tắc nghiệp vụ:**

- BR-08.1: Quản trị viên PHẢI định nghĩa được Chính sách SLA riêng theo từng phân khúc khách hàng, cho từng loại cam kết (phản hồi lần đầu, phản hồi các lần sau, giải quyết xong).
- BR-08.2: Một Hộp thư (Inbox) CÓ THỂ áp dụng một Chính sách SLA riêng, khác với chính sách mặc định của doanh nghiệp.
- BR-08.3: Hội thoại mới tạo hoặc vừa được mở lại PHẢI đồng thời có cam kết phản hồi lần đầu và cam kết giải quyết xong đang chạy.
- BR-08.4: Thời hạn cam kết PHẢI được tính theo giờ làm việc của doanh nghiệp (nếu doanh nghiệp có cấu hình giờ làm việc) — thời gian ngoài giờ làm việc không tính vào thời hạn.
- BR-08.5: Khi khách hàng nhắn tin, hệ thống PHẢI khởi động (hoặc khởi động lại) đúng loại đồng hồ cam kết phù hợp (lần đầu nếu chưa từng phản hồi, các lần sau nếu đã từng phản hồi).
- BR-08.6: Khi Agent trả lời, hệ thống PHẢI ghi nhận mốc phản hồi và dừng mọi đồng hồ phản hồi đang chạy — độc lập với việc hội thoại có Chính sách SLA áp dụng hay không (để luôn đo được tốc độ phản hồi thực tế phục vụ báo cáo).
- BR-08.7: Khi hội thoại chuyển Tạm hoãn, mọi đồng hồ đang chạy PHẢI tạm dừng; khi trở lại Đang mở, đồng hồ PHẢI tiếp tục tính từ thời gian còn lại, không tính thêm thời gian tạm dừng vào thời hạn.
- BR-08.8: Khi hội thoại Đã giải quyết/Đã đóng, cam kết giải quyết xong PHẢI được chốt lại; các cam kết phản hồi còn đang chạy PHẢI hủy.
- BR-08.9: Khi hội thoại mở lại, mọi cam kết cũ (kể cả đã hoàn thành/vi phạm) PHẢI được thay bằng một chu kỳ cam kết mới.
- BR-08.10: Agent/Giám sát viên PHẢI nhìn thấy một đồng hồ đếm ngược duy nhất trên mỗi hội thoại, hiển thị cam kết gần tới hạn nhất trong số các cam kết đang áp dụng.

**Tiêu chí chấp nhận:**

- Hội thoại mới luôn có đồng hồ đếm ngược cam kết phản hồi và giải quyết đang chạy.
- Tạm hoãn hội thoại dừng đếm ngược; mở lại tiếp tục đúng từ thời gian còn lại.
- Thời hạn cam kết tính đúng theo giờ làm việc, không bị "trôi" thêm vì thời gian ngoài giờ.

---

### FEAT-09 — Leo thang khi vi phạm SLA `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi một hội thoại có nguy cơ hoặc đã vi phạm cam kết thời gian phản hồi, hệ thống tự động thực hiện hành động cảnh báo phù hợp, để sự cố được xử lý trước khi ảnh hưởng tới khách hàng.

**Actor:** Hệ thống (tự động thực hiện); Quản trị viên (cấu hình chính sách); Giám sát viên/Quản lý (người nhận cảnh báo).

**Luồng chính:**

1. Quản trị viên định nghĩa Chính sách leo thang, gắn với một Chính sách SLA cụ thể: kích hoạt khi "sắp vi phạm" hay "đã vi phạm", sau bao lâu, và hành động cần thực hiện.
2. Khi hội thoại vi phạm/sắp vi phạm SLA tương ứng, hệ thống chờ đúng khoảng thời gian đã cấu hình rồi thực hiện hành động.
3. Nếu hội thoại đã được xử lý xong trước khi tới lượt thực hiện, hành động leo thang tự hủy.

**Quy tắc nghiệp vụ:**

- BR-09.1: Chính sách leo thang PHẢI gắn với đúng một Chính sách SLA, xác định độ trễ kích hoạt, và danh sách hành động cần thực hiện.
- BR-09.2: Hệ thống PHẢI hỗ trợ các hành động: đánh dấu cảnh báo trên hội thoại, thông báo cho một người cụ thể, thông báo cho cấp quản lý cao hơn (leo lên N cấp), hoặc tự động chuyển hội thoại cho người khác.
- BR-09.3: Trước khi thực hiện, hệ thống PHẢI kiểm tra hội thoại vẫn còn đang cần xử lý (chưa được giải quyết/đóng) — nếu đã xong việc, hành động leo thang không còn cần thiết và tự hủy.
- BR-09.4: Nếu hội thoại tiếp tục vi phạm nghiêm trọng hơn, hệ thống PHẢI thay thế hành động leo thang cũ đang chờ bằng hành động mới, không thực hiện chồng chéo nhiều lần cho cùng một lần vi phạm.
- BR-09.5: Thông báo leo thang PHẢI gửi trực tiếp tới đúng người được chỉ định (hoặc đúng cấp quản lý được xác định), không gửi tràn lan tới cả đội.

**Tiêu chí chấp nhận:**

- Hội thoại được giải quyết trước khi tới lượt leo thang thì không có cảnh báo nào được gửi.
- Người được chỉ định nhận cảnh báo nhận được thông báo đúng lúc, đúng nội dung hội thoại vi phạm.

---

### FEAT-10 — Tự động đóng hội thoại không hoạt động `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động chuyển các hội thoại không còn hoạt động (khách hàng không phản hồi, việc đã xong nhưng Agent quên đóng) sang trạng thái Đã giải quyết/Đã đóng sau một khoảng thời gian, giữ danh sách hội thoại luôn gọn gàng và phản ánh đúng việc thực sự cần xử lý — có cảnh báo trước cho khách hàng để không đóng đột ngột.

**Actor:** Hệ thống (tự động thực hiện); Quản trị viên (cấu hình chính sách); Agent (có thể can thiệp từng hội thoại cụ thể).

**Luồng chính:**

1. Quản trị viên định nghĩa Chính sách tự động đóng: điều kiện áp dụng, khoảng thời gian không hoạt động cho phép, có cảnh báo trước hay không, thời gian ân hạn sau cảnh báo, trạng thái đích (Đã giải quyết hoặc Đã đóng), và cách xử lý khi khách nhắn lại sau đó.
2. Khi một hội thoại đạt ngưỡng thời gian không hoạt động, nếu chính sách có bật cảnh báo, hệ thống gửi tin nhắn hỏi khách hàng còn cần hỗ trợ không, và chờ thêm một khoảng thời gian ân hạn.
3. Nếu khách hàng phản hồi hoặc có hoạt động mới trong lúc chờ, việc đóng bị hủy, hội thoại quay lại bình thường.
4. Nếu hết thời gian ân hạn mà không có phản hồi, hội thoại được đóng theo trạng thái đích đã cấu hình.

**Quy tắc nghiệp vụ:**

- BR-10.1: Quản trị viên PHẢI cấu hình được Chính sách tự động đóng theo điều kiện áp dụng (ví dụ theo kênh, theo tag), khoảng thời gian không hoạt động, và trạng thái đích.
- BR-10.2: Doanh nghiệp CÓ THỂ chọn "không bao giờ tự động đóng" cho một số nhóm hội thoại cụ thể (ví dụ hội thoại đang chờ xử lý nội bộ đặc biệt).
- BR-10.3: Nếu chính sách bật cảnh báo trước, hệ thống PHẢI gửi tin nhắn hỏi khách hàng trước khi đóng, và CÓ THỂ lặp lại cảnh báo một số lần theo cấu hình nếu khách hàng vẫn im lặng.
- BR-10.4: Bất kỳ hoạt động thực sự nào từ khách hàng hoặc Agent trong lúc đang chờ đóng PHẢI hủy việc đóng ngay lập tức, đưa hội thoại quay lại trạng thái xử lý bình thường — tin nhắn cảnh báo tự động của chính hệ thống không được tính là một "hoạt động" làm hủy việc đóng.
- BR-10.5: Agent PHẢI có thể tự đặt riêng cho một hội thoại cụ thể: miễn trừ tự động đóng, tạm hoãn việc đóng thêm một khoảng thời gian, hoặc ép đóng ngay vào một thời điểm chỉ định.
- BR-10.6: Khi hội thoại được đóng tự động dưới trạng thái Đã giải quyết, doanh nghiệp PHẢI cấu hình được cách xử lý nếu khách hàng nhắn lại sau đó: luôn mở lại hội thoại cũ, luôn tạo hội thoại mới, hoặc mở lại nếu trong một khoảng thời gian nhất định (nếu quá thời gian đó thì tạo mới).
- BR-10.7 `[Yêu cầu mới]`: Khi hệ thống tạm hoãn việc đóng tự động của một hội thoại vì đang có quá nhiều hội thoại cùng bị đóng trong một khoảng thời gian ngắn (Ngưỡng an toàn đóng hàng loạt, dùng để tránh sự cố kênh làm đóng oan hàng loạt hội thoại), hệ thống PHẢI đảm bảo tự động thử đóng lại hội thoại đó trong vòng **tối đa 10 phút** kể từ lúc bị hoãn — không được để hội thoại chờ vô thời hạn tới khi có hoạt động mới hoặc quản trị viên can thiệp thủ công.
- BR-10.8 `[Yêu cầu mới]`: Nhật ký vận hành của tính năng này PHẢI phân biệt rõ hai trường hợp khác nhau: "hoãn đóng vì đang bảo vệ hệ thống khỏi đóng hàng loạt" và "việc đóng bị lỡ vì sự cố kỹ thuật khác" — để Giám sát viên biết được liệu doanh nghiệp có đang thực sự gặp tình huống quá tải (ví dụ một kênh bị lỗi khiến hàng loạt hội thoại cùng lúc đủ điều kiện đóng) hay không, thay vì phải tự suy đoán.

**Tiêu chí chấp nhận:**

- Hội thoại nhận được cảnh báo trước khi bị đóng tự động (nếu chính sách bật cảnh báo), không bao giờ đóng đột ngột không báo trước.
- Khách hàng phản hồi trong lúc đang chờ đóng thì hội thoại được giữ lại bình thường.
- Hội thoại bị hoãn đóng do bảo vệ hệ thống luôn được thử đóng lại trong vòng tối đa 10 phút.
- Giám sát viên phân biệt được trong nhật ký: hoãn do bảo vệ hệ thống hay lỡ do sự cố kỹ thuật.

**Tham chiếu:** BR-10.7 → issue [#17](https://github.com/crmsaassaudi/product-management/issues/17). BR-10.8 → issue [#18](https://github.com/crmsaassaudi/product-management/issues/18).

---

### FEAT-11 — Bot trả lời tự động & bàn giao cho Agent `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép một kịch bản trả lời tự động (Bot) tiếp nhận và xử lý hội thoại thay Agent trong giai đoạn đầu (ví dụ chào hỏi, thu thập thông tin, trả lời câu hỏi thường gặp), rồi bàn giao đúng lúc cho Agent phù hợp khi cần con người xử lý tiếp.

**Actor:** Bot (hệ thống trả lời tự động, cấu hình ở một công cụ riêng); Agent (nhận bàn giao, có thể chủ động tắt/bật Bot).

**Luồng chính:**

1. Tin nhắn khách hàng mới tới, nếu kênh/hội thoại đang bật Bot, hệ thống chuyển cho Bot xử lý.
2. Bot trả lời một hoặc nhiều tin nhắn dựa trên kịch bản đã cấu hình.
3. Khi Bot xác định cần con người xử lý tiếp (ví dụ khách yêu cầu gặp nhân viên, hoặc kịch bản kết thúc), Bot bàn giao hội thoại — có thể chỉ định bàn giao cho một Agent cụ thể, một nhóm cụ thể, hoặc hàng đợi chung.
4. Agent PHẢI có thể chủ động tắt Bot bất kỳ lúc nào để tự tiếp quản hội thoại, và bật lại Bot nếu muốn giao lại.

**Quy tắc nghiệp vụ:**

- BR-11.1: Bot chỉ được kích hoạt cho hội thoại khi cả cấu hình cấp kênh và cấp hội thoại đều đang cho phép Bot hoạt động.
- BR-11.2: Doanh nghiệp CÓ THỂ cấu hình theo từng kênh một trong ba chế độ: Bot xử lý trước rồi luôn bàn giao cho người khi kết thúc kịch bản, Bot xử lý toàn bộ và chỉ bàn giao khi không có kịch bản phù hợp, hoặc tắt hẳn Bot (mọi hội thoại luôn cần Agent xử lý ngay từ đầu).
- BR-11.3: Khi Bot bàn giao có chỉ định Agent/nhóm cụ thể, hệ thống PHẢI kiểm tra Agent/nhóm đó còn hợp lệ và đủ điều kiện hỗ trợ đúng kênh; nếu không, hệ thống PHẢI tự chuyển về bàn giao cho hàng đợi chung thay vì bàn giao nhầm.
- BR-11.4: Agent PHẢI có thể chủ động tắt Bot để tự tiếp quản hội thoại ngay lập tức, và bật lại Bot khi muốn.
- BR-11.5: Một phản hồi từ Bot đến muộn sau khi Agent đã tiếp quản hoặc Bot đã bàn giao xong PHẢI bị bỏ qua, không được "hồi sinh" lại phiên Bot đã kết thúc.
- BR-11.6: Khi Agent trả lời một hội thoại mà Bot đang hoạt động, hệ thống PHẢI tự động tắt Bot cho hội thoại đó (trừ khi doanh nghiệp cấu hình khác), tránh việc Bot và Agent cùng trả lời chồng chéo.

**Tiêu chí chấp nhận:**

- Hội thoại bàn giao đúng người/nhóm được chỉ định khi hợp lệ; bàn giao vào hàng đợi chung khi chỉ định không còn hợp lệ.
- Agent tắt Bot là tiếp quản được ngay, không có phản hồi Bot nào chen vào sau đó.
- Agent trả lời trong lúc Bot đang chạy sẽ tự động tắt Bot cho hội thoại đó.

---

### FEAT-12 — Gửi tin nhắn cho khách hàng `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép Agent (hoặc Bot, hoặc hệ thống) gửi tin nhắn trả lời khách hàng qua đúng kênh của hội thoại, đảm bảo tin nhắn được gửi đáng tin cậy và tuân thủ giới hạn của từng nền tảng kênh.

**Actor:** Agent; Bot; Hệ thống (tin nhắn tự động như thông báo ngoài giờ, cảnh báo trước khi đóng, khảo sát CSAT).

**Luồng chính:**

1. Agent soạn tin nhắn (văn bản, ảnh/video/tệp, tin nhắn mẫu, hoặc tin nhắn có nút bấm tương tác) và gửi.
2. Hệ thống kiểm tra hội thoại còn được phép gửi tin (chưa đóng, còn trong Cửa sổ phản hồi nếu kênh có giới hạn).
3. Tin nhắn được gửi qua đúng kênh; trạng thái gửi được cập nhật cho Agent thấy (đang gửi → đã gửi → đã nhận → đã đọc, hoặc gửi lỗi).

**Quy tắc nghiệp vụ:**

- BR-12.1: Hệ thống PHẢI từ chối gửi tin nhắn vào một hội thoại Đã đóng.
- BR-12.2: Khi đã hết Cửa sổ phản hồi trên một kênh có giới hạn, hệ thống PHẢI ngăn gửi tin nhắn tự do; với kênh hỗ trợ tin nhắn mẫu đã phê duyệt trước (hiện chỉ WhatsApp), Agent CÓ THỂ vẫn gửi được bằng loại tin nhắn này để chủ động liên hệ lại.
- BR-12.3: Hệ thống PHẢI giới hạn số lượng nút bấm tối đa cho tin nhắn dạng tương tác trên các kênh có giới hạn kỹ thuật (ví dụ WhatsApp giới hạn 3 nút); với kênh không hỗ trợ định dạng tương tác, hệ thống PHẢI tự chuyển thành danh sách lựa chọn dạng văn bản đánh số.
- BR-12.4: Khi gửi lại một tin nhắn trước đó gửi thất bại chắc chắn (ví dụ lỗi nội dung không hợp lệ), hệ thống CHO PHÉP gửi lại; khi kết quả gửi trước đó không rõ ràng (ví dụ mất kết nối giữa chừng, không chắc nền tảng kênh đã nhận hay chưa), hệ thống KHÔNG ĐƯỢC tự động gửi lại — để tránh khách hàng nhận hai lần cùng một tin nhắn.
- BR-12.5: Khi Agent trả lời một hội thoại chưa có ai phụ trách, hệ thống PHẢI tự động gán hội thoại đó cho Agent vừa trả lời.
- BR-12.6: Với kênh WhatsApp, hệ thống PHẢI giới hạn tốc độ gửi tin theo đúng hạn mức nền tảng cho phép; tin nhắn trả lời trực tiếp cho khách hàng (không phải gửi hàng loạt) PHẢI luôn được ưu tiên gửi trước, không bao giờ bị trì hoãn vì lý do giới hạn tốc độ.
- BR-12.7: Tin nhắn hệ thống tự động gửi thay mặt doanh nghiệp (thông báo ngoài giờ, cảnh báo trước khi đóng, link khảo sát CSAT) PHẢI được đánh dấu khác với tin nhắn do Agent chủ động gửi, để không bị tính nhầm là hoạt động của Agent trong các báo cáo/quy tắc tự động đóng.
- BR-12.8: Nếu một tin nhắn ở trạng thái "đang gửi" quá lâu bất thường (nghi ngờ bị kẹt do sự cố), hệ thống PHẢI tự động đánh dấu gửi thất bại sau một khoảng thời gian hợp lý, để Agent biết cần gửi lại — không được để tin nhắn treo vô thời hạn ở trạng thái "đang gửi" khiến Agent tưởng đã gửi thành công.

**Tiêu chí chấp nhận:**

- Không gửi được tin nhắn vào hội thoại đã đóng.
- Hết Cửa sổ phản hồi trên kênh giới hạn thì chặn gửi tin tự do, gợi ý dùng tin nhắn mẫu nếu kênh hỗ trợ.
- Tin nhắn có kết quả gửi không rõ ràng không bao giờ tự động gửi lại (tránh gửi trùng cho khách).
- Tin nhắn tương tác/agent luôn được ưu tiên hơn tin nhắn hàng loạt khi kênh có giới hạn tốc độ gửi.

---

### FEAT-13 — Cập nhật thời gian thực cho Agent `[Đã triển khai]`

**Mô tả nghiệp vụ:** Mọi thay đổi liên quan tới hội thoại (tin nhắn mới, đổi người phụ trách, đổi trạng thái, lời mời nhận việc mới...) PHẢI hiển thị ngay lập tức trên màn hình Agent, không cần Agent tự làm mới trang, để không bỏ lỡ tin nhắn khách hàng hay xung đột thao tác với đồng nghiệp.

**Actor:** Agent; Giám sát viên; hệ thống (đẩy cập nhật tự động).

**Quy tắc nghiệp vụ:**

- BR-13.1: Mọi tin nhắn mới, thay đổi trạng thái, thay đổi người phụ trách, và lời mời nhận hội thoại PHẢI được đẩy ngay tới màn hình của (các) Agent liên quan mà không cần tải lại trang.
- BR-13.2: Agent chỉ được nhận cập nhật của những hội thoại mình có quyền xem — không nhận được thông tin của hội thoại thuộc kênh/nhóm mình không được phân công hỗ trợ.
- BR-13.3: Khi hai Agent cùng lúc cố nhận một hội thoại đang chờ trong hàng đợi, hệ thống PHẢI đảm bảo chỉ đúng một người nhận được, người còn lại nhận thông báo hội thoại đã có người nhận.
- BR-13.4: Khi một Agent đang soạn tin cho một hội thoại, hệ thống CÓ THỂ hiển thị cho các Agent khác biết hội thoại đang được ai đó soạn thảo, tránh hai người cùng trả lời chồng chéo.
- BR-13.5: Thông báo cá nhân (được yêu cầu chuyển tiếp, bị leo thang tới, được nhắc tên trong ghi chú...) PHẢI gửi đúng tới người liên quan, không thông báo tràn lan cho cả đội.

**Tiêu chí chấp nhận:**

- Tin nhắn khách hàng mới hiện ngay trên màn hình Agent đang phụ trách, không cần bấm làm mới.
- Hai Agent cùng bấm nhận 1 hội thoại đang chờ, chỉ đúng 1 người nhận được.

---

### FEAT-14 — Khảo sát hài lòng khách hàng (CSAT) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Sau khi một hội thoại được giải quyết, tự động mời khách hàng chấm điểm mức độ hài lòng, để doanh nghiệp đo lường chất lượng phục vụ không chỉ dựa vào tốc độ phản hồi.

**Actor:** Khách hàng (người chấm điểm); Hệ thống (tự động gửi khảo sát); Giám sát viên (xem báo cáo).

**Luồng chính:**

1. Hội thoại chuyển sang Đã giải quyết.
2. Hệ thống tự động gửi lời mời khảo sát cho khách hàng qua đúng kênh của hội thoại đó (hiển thị trực tiếp trên Live Chat, hoặc gửi đường dẫn khảo sát qua các kênh khác).
3. Khách hàng chấm điểm 1–5 sao, có thể kèm nhận xét.
4. Điểm số được ghi nhận, hiển thị cho Agent/Giám sát viên và tính vào báo cáo.

**Quy tắc nghiệp vụ:**

- BR-14.1: Hệ thống PHẢI tự động gửi lời mời khảo sát khi hội thoại chuyển sang Đã giải quyết, với các kênh có hỗ trợ khảo sát.
- BR-14.2: Đường dẫn khảo sát PHẢI chỉ dùng được một lần và có thời hạn hiệu lực (7 ngày); khách hàng không chấm điểm được hai lần cho cùng một lời mời, và đường dẫn hết hạn không dùng được nữa.
- BR-14.3: Hệ thống PHẢI cung cấp báo cáo CSAT: tổng số khảo sát đã gửi, tỷ lệ phản hồi, điểm trung bình, phân bố điểm theo từng mức, điểm trung bình theo Agent và theo kênh.
- BR-14.4 `[Yêu cầu mới]`: Điểm CSAT trung bình PHẢI được đưa vào **cùng Báo cáo vận hành Omnichannel tổng quan** (FEAT-19), theo cùng bộ lọc ngày/kênh mà các số liệu khác trong báo cáo đó đang dùng — không bắt Giám sát viên phải mở một màn hình riêng để đối chiếu số liệu chất lượng với số liệu khối lượng công việc.
- BR-14.5 `[Yêu cầu mới]`: Điểm CSAT trung bình theo từng Agent PHẢI xuất hiện như một cột trong **Báo cáo hiệu suất Agent** (FEAT-20), cạnh các chỉ số tốc độ xử lý, để không đánh giá năng suất Agent chỉ dựa trên tốc độ mà bỏ qua chất lượng phục vụ.

**Tiêu chí chấp nhận:**

- Khách hàng nhận được lời mời khảo sát ngay khi hội thoại được giải quyết.
- Một đường dẫn khảo sát chỉ chấm điểm được đúng một lần.
- Báo cáo vận hành Omnichannel và Báo cáo hiệu suất Agent đều hiển thị điểm CSAT trung bình, dùng chung bộ lọc với các số liệu khác trong cùng báo cáo.

**Tham chiếu:** BR-14.4 → issue [#19](https://github.com/crmsaassaudi/product-management/issues/19). BR-14.5 → issue [#20](https://github.com/crmsaassaudi/product-management/issues/20).

---

### FEAT-15 — Ghi chú, cảm xúc & lịch sử xử lý `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép Agent ghi chú nội bộ trong lúc xử lý hội thoại (không hiển thị cho khách hàng), phản ứng cảm xúc nhanh trên tin nhắn, và lưu lại toàn bộ lịch sử các mốc quan trọng của hội thoại để tra cứu sau này.

**Actor:** Agent (viết ghi chú, thả cảm xúc); Khách hàng (thả cảm xúc trên các kênh hỗ trợ); Hệ thống (tự động ghi lịch sử).

**Quy tắc nghiệp vụ:**

- BR-15.1: Ghi chú nội bộ PHẢI không hiển thị cho khách hàng dưới bất kỳ hình thức nào.
- BR-15.2: Agent CÓ THỂ ghim một ghi chú làm "Ghi chú bàn giao" nổi bật đầu hội thoại, hữu ích khi chuyển tiếp cho người khác; chỉ một ghi chú được ghim tại một thời điểm cho mỗi hội thoại.
- BR-15.3: Mỗi người (khách hàng hoặc Agent) chỉ được có một cảm xúc đang thả trên một tin nhắn tại một thời điểm; thả cảm xúc khác sẽ thay thế cảm xúc cũ.
- BR-15.4: Hệ thống PHẢI tự động ghi lại các mốc quan trọng của hội thoại (tạo mới, mở lại, đổi trạng thái, đổi người/nhóm phụ trách, thêm/xóa nhãn, vi phạm SLA, bị leo thang, bàn giao Bot...) thành một dòng thời gian xem lại được.
- BR-15.5: Việc ghi lại lịch sử xử lý KHÔNG ĐƯỢC làm gián đoạn hoạt động nghiệp vụ chính nếu bản thân việc ghi log gặp lỗi.

**Tiêu chí chấp nhận:**

- Ghi chú nội bộ không bao giờ lộ ra phía khách hàng.
- Mọi mốc quan trọng của hội thoại tra cứu được đầy đủ theo đúng trình tự thời gian.

---

### FEAT-16 — Tìm kiếm nội dung tin nhắn `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép Agent tìm lại một tin nhắn cụ thể trong lịch sử trò chuyện của một hội thoại hoặc của một khách hàng, thay vì phải cuộn tay qua toàn bộ lịch sử.

**Actor:** Agent.

**Quy tắc nghiệp vụ:**

- BR-16.1: Tìm kiếm PHẢI luôn giới hạn trong phạm vi một hội thoại cụ thể hoặc một khách hàng cụ thể — không hỗ trợ tìm kiếm tự do trên toàn bộ dữ liệu của doanh nghiệp, để tránh lộ thông tin ngoài phạm vi công việc của Agent.
- BR-16.2: Kết quả tìm kiếm PHẢI hiển thị đoạn trích ngắn quanh từ khóa để Agent nhận ra đúng ngữ cảnh trước khi mở toàn bộ tin nhắn.

**Tiêu chí chấp nhận:**

- Agent tìm được đúng tin nhắn cần tìm trong một hội thoại/khách hàng cụ thể mà không cần cuộn thủ công.

---

### FEAT-17 — Quản lý hộp thư (Inbox) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép doanh nghiệp gộp nhiều kênh vào một Hộp thư chung, gán riêng nhóm/Agent phụ trách và chính sách riêng (phân công, SLA, Bot) khác với mặc định toàn doanh nghiệp — phù hợp với doanh nghiệp có nhiều đội/nhiều dòng sản phẩm cùng dùng chung hệ thống.

**Actor:** Quản trị viên.

**Quy tắc nghiệp vụ:**

- BR-17.1: Một Hộp thư PHẢI gắn được với một hoặc nhiều nhóm/Agent được phép xử lý.
- BR-17.2: Một Hộp thư CÓ THỂ chỉ định riêng một Quy tắc phân công, một Chính sách SLA, và một cấu hình Bot khác với mặc định toàn doanh nghiệp; nếu không chỉ định, Hộp thư dùng theo cấu hình mặc định.
- BR-17.3: Việc đọc cấu hình riêng của Hộp thư KHÔNG ĐƯỢC làm gián đoạn xử lý hội thoại nếu có lỗi tra cứu — hệ thống PHẢI tự động dùng cấu hình mặc định trong trường hợp đó thay vì chặn hội thoại lại.

**Tiêu chí chấp nhận:**

- Hội thoại thuộc kênh nằm trong một Hộp thư có cấu hình riêng thì áp dụng đúng cấu hình riêng đó, không áp nhầm cấu hình mặc định.

---

### FEAT-18 — Mẫu tin nhắn nhanh (Canned Response) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép Agent soạn sẵn các nội dung trả lời hay dùng (câu chào, hướng dẫn thường gặp, chính sách đổi trả...) và chèn nhanh vào hội thoại bằng một lệnh gõ tắt, thay vì soạn lại từ đầu mỗi lần.

**Actor:** Agent; Quản trị viên (quản lý mẫu dùng chung).

**Quy tắc nghiệp vụ:**

- BR-18.1: Mẫu tin nhắn PHẢI có phạm vi hiển thị: chỉ riêng người tạo, dùng chung cho một nhóm, hoặc dùng chung toàn doanh nghiệp.
- BR-18.2: Mẫu tin nhắn CÓ THỂ gán một lệnh gõ tắt ngắn để chèn nhanh khi soạn tin.
- BR-18.3: Mẫu tin nhắn chỉ riêng người tạo thì chỉ người đó (hoặc quản trị viên) được sửa/xóa; mẫu dùng chung cho nhóm/toàn doanh nghiệp thì mọi người trong phạm vi đó đều sửa được.

**Tiêu chí chấp nhận:**

- Agent gõ lệnh tắt đã gán cho một mẫu, nội dung mẫu được chèn ngay vào ô soạn tin.

---

### FEAT-19 — Báo cáo vận hành Omnichannel `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp cho Giám sát viên bức tranh tổng thể về khối lượng, tốc độ và chất lượng xử lý hội thoại trên toàn doanh nghiệp, theo khoảng thời gian và bộ lọc tùy chọn.

**Actor:** Giám sát viên; Quản lý.

**Nội dung báo cáo:**

- Khối lượng hội thoại theo thời gian (mới tạo, đã giải quyết).
- Tỷ lệ hội thoại theo từng kênh.
- Tốc độ phản hồi/giải quyết (thời gian phản hồi lần đầu, thời gian chờ được gán, thời gian giải quyết), tỷ lệ đáp ứng cam kết SLA.
- Phân bố hội thoại theo trạng thái, theo lý do giải quyết.
- Khối lượng tin nhắn theo loại/chiều/kênh.
- Hiệu quả Bot: tỷ lệ hội thoại Bot tự giải quyết xong, tỷ lệ bàn giao cho người.
- Giờ cao điểm theo ngày trong tuần/giờ trong ngày.
- Phân tích theo nhãn (tag) gắn trên hội thoại.
- Tỷ lệ hội thoại được mở lại sau khi đã giải quyết.

**Quy tắc nghiệp vụ:**

- BR-19.1: Thời gian phản hồi lần đầu trung bình PHẢI loại trừ những hội thoại chưa từng được Agent nào phản hồi — không được tính các hội thoại đó là "0 phút", vì sẽ làm sai lệch số liệu theo hướng tốt hơn thực tế.
- BR-19.2: Mọi số liệu trong báo cáo PHẢI chỉ tổng hợp trên những hội thoại mà người xem báo cáo có quyền nhìn thấy (theo phạm vi dữ liệu được phân quyền), không lộ số liệu của hội thoại ngoài phạm vi.
- BR-19.3 `[Yêu cầu mới]`: Báo cáo PHẢI có thêm chỉ số điểm CSAT trung bình (xem BR-14.4), dùng chung khoảng thời gian/kênh/agent đang lọc với các chỉ số khác của báo cáo này.

**Tiêu chí chấp nhận:**

- Thay đổi bộ lọc ngày/kênh/agent áp dụng đồng thời cho mọi chỉ số trong báo cáo, bao gồm cả CSAT.
- Giám sát viên chỉ thấy số liệu tổng hợp trong phạm vi dữ liệu được phân quyền.

**Tham chiếu:** BR-19.3 → issue [#21](https://github.com/crmsaassaudi/product-management/issues/21).

---

### FEAT-20 — Báo cáo hiệu suất Agent `[Đã triển khai]`

**Mô tả nghiệp vụ:** Đo lường năng suất và chất lượng làm việc của từng Agent, phục vụ hoạch định nhân sự (cần bao nhiêu Agent để đáp ứng khối lượng công việc) và đánh giá hiệu suất công bằng.

**Actor:** Giám sát viên; Quản lý.

**Nội dung báo cáo:** thời gian Agent ở mỗi trạng thái làm việc trong ngày, thời gian xử lý trung bình, tỷ lệ thời gian bận việc so với thời gian trực tuyến, số lượng hội thoại/công việc đã xử lý, và bảng xếp hạng hiệu suất tổng hợp.

**Quy tắc nghiệp vụ:**

- BR-20.1: Báo cáo PHẢI tổng hợp năng suất theo 4 nhóm công việc: Live Chat, Nhắn tin qua mạng xã hội, Ticket hỗ trợ, và Email — giữ nguyên như đã có, chỉ bổ sung thêm ở BR-20.2.
- BR-20.2 `[Yêu cầu mới]`: Thời gian xử lý trung bình (AHT) PHẢI được tách riêng thành hai chỉ số cho nhóm công việc nhắn tin, thay vì gộp chung một con số duy nhất:
  - **AHT-Live Chat** — chỉ tính riêng kênh Live Chat trên website, vì đây là hình thức trò chuyện trực tiếp/đồng thời (khách hàng đang chờ ngay trên màn hình), thường xử lý trong vài phút.
  - **AHT-Nhắn tin mạng xã hội** — gộp chung Facebook, Instagram, WhatsApp, Zalo, TikTok, Telegram, vì đây đều là hình thức nhắn tin không đồng thời (khách hàng không nhất thiết chờ ngay), một hội thoại có thể kéo dài nhiều giờ hoặc nhiều ngày.

    Lý do tách: nếu gộp chung Live Chat và các kênh nhắn tin mạng xã hội vào một con số "AHT-Chat" duy nhất, kết quả trung bình sẽ bị méo do bản chất hai nhóm này rất khác nhau (đồng thời và không đồng thời), khiến số liệu không dùng được để hoạch định nhân sự chính xác.
  - Các chỉ số AHT theo Ticket hỗ trợ, Email, và (nếu áp dụng) Cuộc gọi vẫn giữ nguyên là các cột riêng như trước.
- BR-20.3 `[Yêu cầu mới]`: Điểm CSAT trung bình của từng Agent (xem BR-14.5) PHẢI xuất hiện trong bảng hiệu suất, cạnh các chỉ số tốc độ xử lý, để đánh giá năng suất luôn đi kèm với chất lượng phục vụ.
- BR-20.4: Bảng xếp hạng hiệu suất tổng hợp PHẢI loại trừ những Agent có quá ít thời gian trực tuyến hoặc quá ít việc đã xử lý trong kỳ báo cáo, thay vì xếp hạng không công bằng dựa trên quá ít dữ liệu.

**Tiêu chí chấp nhận:**

- Báo cáo hiển thị riêng biệt AHT-Live Chat và AHT-Nhắn tin mạng xã hội, không gộp chung một con số.
- Cột CSAT trung bình xuất hiện cho từng Agent trong cùng bảng hiệu suất.
- Agent có quá ít dữ liệu trong kỳ không xuất hiện trong bảng xếp hạng gây hiểu lầm.

**Tham chiếu:** BR-20.2 → issue [#22](https://github.com/crmsaassaudi/product-management/issues/22). BR-20.3 → issue [#23](https://github.com/crmsaassaudi/product-management/issues/23).

---

### FEAT-21 — Giao diện xử lý hội thoại của Agent `[Đã triển khai]`

**Mô tả nghiệp vụ:** Không gian làm việc chính của Agent — nơi xem danh sách hội thoại, trò chuyện với khách hàng, và thao tác mọi nghiệp vụ (gán, chuyển tiếp, ghi chú, đóng...) — được thiết kế để Agent xử lý nhanh, không bỏ sót, và luôn biết trạng thái hiện tại của từng hội thoại.

**Actor:** Agent.

**Luồng chính:**

1. Agent đăng nhập, thấy danh sách hội thoại được lọc theo phạm vi được phân công (của tôi, hàng đợi nhóm, theo kênh...).
2. Chọn một hội thoại để mở khung chat, xem lịch sử, thông tin khách hàng liên quan (cơ hội bán hàng, yêu cầu hỗ trợ đã có...).
3. Soạn và gửi tin nhắn, đính kèm file, chèn mẫu tin nhắn nhanh, thả cảm xúc, hoặc thực hiện các thao tác quản lý hội thoại (gán, chuyển tiếp, ghi chú, giải quyết/đóng).

**Quy tắc nghiệp vụ:**

- BR-21.1: Agent PHẢI lọc được danh sách hội thoại theo: trạng thái, người/nhóm được gán, kênh, nhãn, khách hàng VIP, có tin chưa đọc, đang vi phạm/sắp vi phạm SLA, và khoảng thời gian.
- BR-21.2: Một hội thoại Agent đang xem hoặc đang soạn dở tin nhắn PHẢI luôn hiển thị trong danh sách của Agent đó, kể cả khi vừa đổi trạng thái khiến nó không còn khớp bộ lọc đang chọn — tránh hội thoại "biến mất" đột ngột giữa lúc đang thao tác.
- BR-21.3: Agent PHẢI thao tác được các nghiệp vụ chính (gán/chuyển tiếp/giải quyết/tiếp quản) bằng phím tắt, không bắt buộc phải dùng chuột cho các thao tác lặp lại nhiều lần trong ngày.
- BR-21.4: Ô soạn tin PHẢI bị khóa và hiển thị rõ lý do khi: hết Cửa sổ phản hồi, hội thoại đang bị Agent khác khóa để soạn, Agent đang ngoại tuyến, hoặc Agent không có quyền trả lời — Agent phải biết ngay lý do, không đoán mò.
- BR-21.5: Khi giải quyết một hội thoại, doanh nghiệp CÓ THỂ yêu cầu Agent bắt buộc ghi lý do/ghi chú giải quyết trước khi xác nhận.
- BR-21.6: Hệ thống PHẢI phát âm thanh thông báo khi có tin nhắn khách hàng mới trong lúc Agent không đang xem đúng hội thoại đó hoặc không đang mở tab trình duyệt, để không bỏ lỡ.
- BR-21.7: Khi có lời mời nhận hội thoại mới, hệ thống PHẢI hiển thị rõ ràng với thời gian đếm ngược còn lại để phản hồi.
- BR-21.8: Agent CÓ THỂ tìm kiếm và liên kết/gộp một hội thoại của Hồ sơ khách hàng tạm với một khách hàng có sẵn trong CRM (thao tác xác nhận thủ công theo BR-02.4).
- BR-21.9: Với hội thoại đang chờ trong hàng đợi mà Agent chưa nhận, hệ thống PHẢI hiển thị số lượng khách đang chờ và thời gian chờ lâu nhất, để Agent chủ động nhận thêm việc khi rảnh.

**Tiêu chí chấp nhận:**

- Agent tìm được và xử lý hội thoại cần thiết mà không cần rời màn hình chính.
- Không có hội thoại nào "biến mất" bất ngờ khỏi danh sách trong lúc Agent đang thao tác trên nó.
- Ô soạn tin bị khóa luôn kèm lý do rõ ràng.

---

### FEAT-22 — Trải nghiệm trò chuyện của khách hàng trên Live Chat Widget `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp một khung chat nhúng trực tiếp trên website của doanh nghiệp để khách truy cập website có thể trò chuyện trực tiếp với Agent (hoặc Bot) mà không cần rời trang hay cài đặt thêm ứng dụng.

**Actor:** Khách truy cập website (Visitor).

**Luồng chính:**

1. Khách truy cập website thấy nút mở khung chat ở góc màn hình.
2. Mở khung chat, (tùy cấu hình) điền một số thông tin cơ bản trước khi bắt đầu (tên, email...).
3. Gửi tin nhắn, nhận phản hồi của Agent/Bot theo thời gian thực; có thể gửi kèm hình ảnh/tệp/ghi âm.
4. Khách hàng quay lại website (kể cả lần sau, kể cả đổi trang) vẫn tiếp tục đúng hội thoại đang dở, không phải bắt đầu lại từ đầu.

**Quy tắc nghiệp vụ:**

- BR-22.1: Widget PHẢI ghi nhớ đúng một khách truy cập qua nhiều lần ghé thăm website (kể cả tắt trình duyệt rồi mở lại), để hội thoại của họ luôn tiếp diễn đúng mạch, không bị xem là người mới mỗi lần.
- BR-22.2: Widget PHẢI hiển thị rõ trạng thái gửi của từng tin nhắn khách hàng gửi đi (đang gửi, đã gửi, đã nhận, đã đọc), và cho phép gửi lại nếu gửi thất bại.
- BR-22.3: Widget CÓ THỂ yêu cầu khách điền một số thông tin (tên, email, nhu cầu) trước khi bắt đầu trò chuyện lần đầu, theo cấu hình doanh nghiệp.
- BR-22.4: Khi ngoài giờ làm việc, widget PHẢI thông báo rõ cho khách hàng biết doanh nghiệp hiện không có ai trực tuyến.
- BR-22.5: Khi hội thoại kết thúc (khách hàng chủ động hoặc Agent đóng), widget PHẢI thông báo rõ và cho khách hàng bắt đầu một hội thoại mới nếu muốn tiếp tục.
- BR-22.6: Widget PHẢI cho khách hàng chấm điểm hài lòng (CSAT) ngay tại chỗ khi được mời, không cần rời khỏi website.
- BR-22.7: Widget PHẢI hoạt động đúng trên cả máy tính và thiết bị di động, tự thích ứng ngôn ngữ hiển thị theo ngôn ngữ trình duyệt của khách truy cập.

**Tiêu chí chấp nhận:**

- Khách truy cập quay lại website sau vài ngày vẫn thấy đúng lịch sử trò chuyện cũ.
- Tin nhắn gửi thất bại do mất mạng có thể gửi lại được, không mất nội dung đã soạn.
- Widget hiển thị đúng và dùng được trên di động.

---

## 4. Yêu cầu phi chức năng

### 4.1 Bảo mật & Phân quyền

- **NFR-1 (Xác thực nguồn tin nhắn):** Mọi tin nhắn tự xưng đến từ một kênh PHẢI được xác thực thực sự đến từ đúng nền tảng đó trước khi được xử lý và hiển thị cho Agent, tránh tin nhắn giả mạo.
- **NFR-2 (Cách ly dữ liệu theo doanh nghiệp):** Hội thoại, khách hàng, cấu hình của một doanh nghiệp không bao giờ hiển thị hoặc trộn lẫn với dữ liệu của doanh nghiệp khác.
- **NFR-3 (Phân quyền truy cập media/tệp):** Hình ảnh/tệp đính kèm trong hội thoại chỉ truy cập được bởi người có quyền xem đúng hội thoại/doanh nghiệp đó.
- **NFR-4 (Bảo mật thông tin xác thực kênh):** Thông tin đăng nhập/token kết nối kênh không bao giờ hiển thị lại cho người dùng sau khi đã lưu, và không bị lộ ra ngoài qua log lỗi hệ thống.

### 4.2 Toàn vẹn & Nhất quán dữ liệu

- **NFR-5 (Không xử lý trùng):** Một tin nhắn/sự kiện không bao giờ được xử lý hai lần dù nền tảng kênh gửi lại (redelivery) do nghi ngờ lỗi mạng.
- **NFR-6 (Không mất tin nhắn khi có sự cố):** Sự cố tạm thời (mất kết nối hạ tầng, một tiến trình xử lý bị dừng đột ngột) không được làm mất tin nhắn khách hàng — hệ thống PHẢI có cơ chế đảm bảo tin nhắn cuối cùng vẫn được xử lý xong, dù có thể chậm hơn bình thường.
- **NFR-7 (Không gửi trùng khi không chắc chắn):** Khi không rõ một tin nhắn gửi đi cho khách hàng đã thành công hay chưa (ví dụ mất kết nối giữa chừng), hệ thống KHÔNG ĐƯỢC tự động gửi lại — vì gửi trùng một tin nhắn cho khách hàng gây trải nghiệm xấu hơn việc Agent phải tự gửi lại thủ công.
- **NFR-8 (Nhất quán giữa các tầng phân quyền):** Trường/dữ liệu Agent không được phép xem theo phân quyền không được lộ ra ở bất kỳ nơi nào khác (báo cáo, tìm kiếm, thông báo realtime) ngoài đúng màn hình chính đã bị chặn.

### 4.3 Hiệu năng & Vận hành

- **NFR-9 (Không chặn xử lý chính vì tác vụ phụ):** Việc ghi log/thống kê không được làm chậm hoặc gián đoạn luồng xử lý tin nhắn/hội thoại chính nếu bản thân việc ghi log gặp lỗi.
- **NFR-10 (Cách ly sự cố theo doanh nghiệp):** Một doanh nghiệp có khối lượng hội thoại lớn đột biến không được làm chậm việc xử lý của các doanh nghiệp khác dùng chung hệ thống.
- **NFR-11 (Xử lý song song an toàn):** Nhiều Agent/nhiều tiến trình hệ thống cùng thao tác trên một hội thoại tại cùng thời điểm (ví dụ 2 Agent cùng bấm nhận 1 hội thoại đang chờ) không được gây ra kết quả sai (ví dụ cả hai đều nghĩ mình đã nhận).

### 4.4 Đa ngôn ngữ & Trải nghiệm

- **NFR-12 (Đa ngôn ngữ):** Giao diện Agent và widget Live Chat PHẢI hiển thị đúng theo ngôn ngữ người dùng đang chọn/trình duyệt của khách truy cập.

---

## 5. Ma trận quyền truy cập tính năng

| Tính năng | Agent | Giám sát viên | Quản trị viên |
| --- | :---: | :---: | :---: |
| Kết nối/cấu hình kênh | — | — | ✅ |
| Xử lý hội thoại được gán (trả lời, ghi chú, đóng) | ✅ | ✅ | — |
| Nhận hội thoại từ hàng đợi | ✅ | ✅ | — |
| Xem/can thiệp hội thoại đang chờ (chưa ai nhận) | — | ✅ | — |
| Chuyển tiếp hội thoại | ✅ (hội thoại của mình) | ✅ (mọi hội thoại) | — |
| Cấu hình quy tắc phân công/SLA/leo thang/tự động đóng | — | — | ✅ |
| Xem báo cáo vận hành & hiệu suất | — (giới hạn, nếu được cấp quyền) | ✅ | ✅ |
| Quản lý Hộp thư, mẫu tin nhắn dùng chung | — | — (trừ khi được cấp quyền) | ✅ |
| Đặt trạng thái làm việc của bản thân | ✅ | ✅ | ✅ |

---

## 6. Kịch bản chấp nhận tổng hợp

1. **Khách hàng cũ nhắn lại đúng người quen:** Một khách hàng đã từng được Agent A hỗ trợ qua Facebook Messenger nhắn tin lại trong vòng thời hạn ưu tiên. Agent A đang trực tuyến và còn khả năng nhận thêm việc → hội thoại tự động gán thẳng cho Agent A, không qua hàng đợi.
2. **Nhận diện sai kênh không tự động gộp nhầm:** Một khách hàng nhắn qua Instagram với số điện thoại trùng với một khách hàng VIP có sẵn trong CRM (thực chất là một người khác, trùng ngẫu nhiên số hotline). Hệ thống chỉ hiển thị gợi ý "có vẻ trùng khớp" cho Agent, không tự gộp — Agent kiểm tra thấy không đúng nên bỏ qua gợi ý, giữ nguyên là hai hồ sơ riêng biệt.
3. **Bot bàn giao đúng lúc, đúng người:** Khách hàng nhắn "tôi muốn gặp nhân viên" trong lúc đang trò chuyện với Bot. Bot bàn giao hội thoại; nếu người/nhóm được Bot chỉ định không còn hợp lệ (đã nghỉ việc/đổi ca), hệ thống tự chuyển hội thoại vào hàng đợi chung thay vì bàn giao vào chỗ trống.
4. **SLA tạm dừng đúng lúc khách hàng im lặng:** Agent trả lời xong, khách hàng không phản hồi trong 2 giờ, Agent chủ động Tạm hoãn hội thoại để làm việc khác. Đồng hồ cam kết phản hồi lần sau dừng lại trong lúc tạm hoãn; khi khách hàng nhắn lại, hội thoại tự động về Đang mở và đồng hồ tiếp tục.
5. **Tự động đóng có cảnh báo trước, không đóng đột ngột:** Hội thoại không hoạt động 24 giờ theo chính sách; hệ thống gửi tin nhắn hỏi khách còn cần hỗ trợ không, chờ thêm 30 phút. Khách phản hồi trong lúc chờ → hội thoại tiếp tục bình thường, không bị đóng.
6. **Hệ thống bảo vệ khi có sự cố kênh làm hàng loạt hội thoại cùng đủ điều kiện đóng:** Một sự cố kỹ thuật khiến rất nhiều hội thoại cùng lúc đạt ngưỡng tự động đóng trong vài giây. Ngưỡng an toàn đóng hàng loạt kích hoạt, hoãn bớt số lượng đóng cùng lúc; các hội thoại bị hoãn được tự động thử đóng lại trong vòng tối đa 10 phút sau đó, không bị bỏ quên.
7. **CSAT và khối lượng công việc nhìn được cùng một chỗ:** Giám sát viên mở Báo cáo vận hành Omnichannel để xem khối lượng hội thoại tuần này tăng đột biến trên kênh WhatsApp; ngay trong cùng báo cáo, thấy điểm CSAT trung bình của kênh đó cũng giảm — kết luận được ngay đội ngũ đang quá tải ảnh hưởng tới chất lượng, không cần đối chiếu chéo 2 báo cáo riêng.
8. **Hoạch định nhân sự dựa trên AHT đúng bản chất kênh:** Giám sát viên xem Báo cáo hiệu suất Agent để tính số Agent cần thêm cho đội Live Chat riêng biệt với đội Nhắn tin mạng xã hội, vì hai nhóm này có AHT rất khác nhau (vài phút so với vài giờ) — nếu bị gộp chung, con số trung bình sẽ không dùng được cho việc này.

---

## 7. Giới hạn hiện tại & vấn đề tồn đọng

Mục này nêu rõ ranh giới hiện tại để tránh kỳ vọng sai, không phải danh sách lỗi kỹ thuật (chi tiết kỹ thuật được theo dõi ở báo cáo audit riêng, xem Mục 1.5):

1. **Chưa tự động gộp được hai hội thoại trùng của cùng một khách hàng nhắn qua hai kênh khác nhau** (ví dụ cùng một người vừa nhắn Facebook vừa nhắn Zalo) thành một hồ sơ trò chuyện duy nhất — đây là một khoảng trống tính năng đã được ghi nhận, cần một quyết định sản phẩm riêng (cách hợp nhất lịch sử, cách xử lý khi đang có 2 Agent khác nhau phụ trách mỗi bên) trước khi xây dựng.
2. **Chưa có tính năng gửi tin nhắn hàng loạt/chiến dịch (Campaign, Broadcast)** qua các kênh omnichannel — Omnichat hiện chỉ phục vụ hội thoại hai chiều do khách hàng chủ động bắt đầu hoặc do Agent/Bot trả lời, chưa hỗ trợ doanh nghiệp chủ động nhắn hàng loạt cho danh sách khách hàng.
3. **Giám sát viên chưa có công cụ theo dõi trực tiếp một cuộc hội thoại đang diễn ra theo thời gian thực (live-monitor), cũng chưa hỗ trợ được trực tiếp cho Agent giữa chừng cuộc trò chuyện** (kiểu "nhắc bài" mà khách hàng không thấy) — hiện chỉ có báo cáo độ sâu hàng đợi và thời gian chờ ở mức tổng hợp.
4. **Một số hình thức tương tác đặc thù theo từng kênh chưa được xử lý như một hội thoại đầy đủ** — ví dụ trả lời story trên Instagram, bình luận hoặc tin nhắn phát sinh từ quảng cáo trên TikTok — các tương tác này hiện chưa có luồng xử lý nghiệp vụ riêng trong Omnichat.
5. **Cơ chế xác thực chữ ký webhook của kênh Zalo chưa được xác nhận hoạt động đúng trong môi trường thực tế** (cần kiểm thử với môi trường thử nghiệm chính thức của Zalo) — hạ tầng đã sẵn sàng nhưng chưa có xác nhận cuối cùng.
6. **Việc yêu cầu bảo mật (xác thực nguồn tin nhắn theo từng kênh riêng biệt) chưa rõ ràng đã áp dụng đầy đủ cho toàn bộ 8 kênh** — cụ thể là TikTok, Live Chat và Email chưa được xác nhận đầy đủ như các kênh Facebook/Instagram/WhatsApp/Zalo.

### Phụ thuộc hệ thống ngoài

Trong quá trình review, phát hiện một hành vi nằm **ngoài phạm vi Omnichat**: chiến lược phân công "người ít bận nhất" dùng cho các đối tượng nghiệp vụ khác của CRM (Liên hệ, Tài khoản, Cơ hội, Ticket, Công việc) không tôn trọng giới hạn tải tối đa của nhân viên khi chọn người nhận việc — một nhân viên đã đạt giới hạn công việc vẫn có thể được chọn nếu đang là người ít việc nhất trong nhóm. **Hành vi này không ảnh hưởng tới việc phân công hội thoại của Omnichat** (đã xác minh riêng: hội thoại luôn tôn trọng đúng giới hạn tải, xem BR-04.1) — đây là vấn đề của engine phân công dùng chung, cần được xem xét trong phạm vi SRS/backlog riêng của các đối tượng CRM nói trên, không thuộc trách nhiệm của tài liệu này.

**Tham chiếu:** → issue [#14](https://github.com/crmsaassaudi/product-management/issues/14).
