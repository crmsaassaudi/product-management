# SRS — Quy trình Tiếp nhận Khách hàng, Thành viên & Khởi tạo Không gian làm việc (Onboarding & Workspace Provisioning)

| | |
| --- | --- |
| **Loại tài liệu** | Software Requirements Specification — Đặc tả Yêu cầu Nghiệp vụ Mục tiêu (To-Be Standard PM/BA, Version 2) |
| **Module** | CRM — Phân hệ Tiếp nhận Khách hàng & Khởi tạo Không gian làm việc (Onboarding & Workspace Provisioning) |
| **Ngày cập nhật** | 2026-08-28 |
| **Phiên bản** | v2.0 (To-Be Target Architecture) |
| **Tài liệu liên quan** | [`CONTEXT.md`](../CONTEXT.md) (glossary), [`iam-tenant-authorization.md`](./iam-tenant-authorization.md), [`billing-subscription-srs.md`](./billing-subscription-srs.md) |

## Ghi chú về nguồn gốc tài liệu

Tài liệu này là **Phiên bản 2.0 (To-Be Target)** được chuyển hóa từ Phiên bản 1.0 (As-Is Baseline) sau quá trình rà soát và nghiên cứu chuyên sâu từ góc độ **Product Management / Business Analysis (PM/BA)** kết hợp đối chiếu chuẩn mực các hệ thống B2B SaaS CRM hàng đầu quốc tế (HubSpot, Salesforce, Pipedrive, Zoho CRM).

Phiên bản 2.0 đã loại bỏ toàn bộ các logic kỹ thuật hạn chế của hiện trạng (như việc gán cứng gói Free không qua dùng thử, lãng phí dữ liệu quy mô nhân sự, thiếu công cụ làm sạch dữ liệu mẫu, thông điệp khởi tạo quá nặng tính kỹ thuật) và bổ sung đầy đủ các năng lực nghiệp vụ trọng yếu: **Kích hoạt 14 ngày dùng thử miễn phí gói Pro (14-Day Free Trial)**, **Cá nhân hoá phễu bán hàng theo Ngành nghề**, **Bảng tiến độ tiếp nhận tương tác trong ứng dụng (In-App Onboarding Checklist & FTUX)**, **Trình quản lý & xóa sạch dữ liệu mẫu 1-Click (Sample Data Purge)**, **Hỗ trợ Tên miền riêng doanh nghiệp (Custom Domain CNAME)** và **Quy trình chào mừng thành viên mới cá nhân hóa (Teammate FTUX)**.

**Quy ước nhãn trạng thái:** Mỗi tính năng (FEAT) và quy tắc nghiệp vụ (BR) được gắn nhãn trạng thái:
- **`[Đã triển khai]`** — Phản ánh các tính năng nền tảng đã sẵn sàng và đang vận hành thực tế trong hệ thống.
- **`[Yêu cầu mới]`** — Các tính năng và quy tắc nâng cấp chuẩn Business To-Be được bổ sung để hoàn thiện trải nghiệm tiếp nhận khách hàng toàn diện.

---

## 1. Giới thiệu

### 1.1 Mục đích

Đặc tả chi tiết toàn bộ hành trình trải nghiệm người dùng và chu trình vận hành của phân hệ Tiếp nhận khách hàng (Onboarding) và Khởi tạo không gian làm việc (Workspace Provisioning):
1. **Khách hàng tự đăng ký (Product-Led Growth — PLG):** Khách vãng lai đăng ký tài khoản → khai báo hồ sơ doanh nghiệp & lựa chọn ngành nghề → định danh không gian làm việc → tự động kích hoạt **14 ngày dùng thử miễn phí gói Chuyên nghiệp (Pro Trial)** → hệ thống điều phối chuỗi khởi tạo tài nguyên ngầm đa dịch vụ (Keycloak, MongoDB, Chatbot) → thiết lập cấu hình nền tảng và nạp dữ liệu mẫu phù hợp ngành → dẫn dắt người dùng qua **Bảng tiến độ tiếp nhận tương tác (In-App Checklist)** để đạt trạng thái kích hoạt giá trị sản phẩm tức thì (Aha Moment).
2. **Khách hàng doanh nghiệp được cấp trước (Sales-Led Growth — SLG / White-Glove):** Đội ngũ kinh doanh/vận hành khởi tạo trước không gian làm việc cho doanh nghiệp qua cổng quản trị nội bộ → thiết lập trần quyền mở rộng và chính sách bảo mật doanh nghiệp (SSO/SAML, Custom Domain CNAME) → gửi thư mời kích hoạt không mật khẩu qua email.
3. **Thành viên mới được mời vào tổ chức (Member Onboarding & FTUX):** Thành viên nhận email mời mang nhận diện thương hiệu công ty → truy cập trang chào mừng thành viên (Welcome Teammate) → tự đặt mật khẩu và làm quen với vị trí phòng ban, người quản lý trực tiếp và vai trò được phân công.
4. **Quản trị vòng đời dùng thử, chuyển đổi & vận hành:** Quản lý đếm ngược thời gian dùng thử, chuỗi email nuôi dưỡng tự động (Drip Campaign), công cụ làm sạch dữ liệu mẫu 1-click, giám sát sức khỏe cấu hình (`setup-health`), chuyển nhượng quyền sở hữu 2 bên và tiến trình tự động dọn dẹp tài khoản mồ côi.

### 1.2 Phạm vi

Tài liệu bao gồm 10 nhóm chức năng nghiệp vụ cốt lõi:
- **Nhóm A: Đăng ký Tài khoản & Kích hoạt Dùng thử (PLG Onboarding Wizard):** Thu thập thông tin tài khoản, tự động kích hoạt 14-day Free Trial, mật khẩu an toàn và đăng nhập nhanh mạng xã hội.
- **Nhóm B: Hồ sơ Doanh nghiệp, Ngành nghề & Bản địa hoá:** Khai báo tên tổ chức, lựa chọn ngành nghề kinh doanh, quy mô nhân sự và thiết lập ngôn ngữ/múi giờ/tiền tệ địa phương.
- **Nhóm C: Định danh Không gian làm việc & Tên miền Riêng:** Chuẩn hoá tên miền phụ (subdomain) URL-safe tiếng Việt, kiểm tra giữ chỗ tạm thời (TTL 30 phút), bảo vệ subdomain hệ thống và cấu hình **Tên miền riêng tùy chỉnh (Custom Domain CNAME)**.
- **Nhóm D: Khởi tạo Không gian làm việc Bất đồng bộ (Provisioning Saga):** Trải nghiệm màn hình chờ truyền cảm hứng, chuỗi điều phối 10 bước ngầm đa dịch vụ, tích hợp Chatbot và cơ chế hoàn tác bù trừ (Compensating Rollback).
- **Nhóm E: Thiết lập Phễu Bán hàng & Quản lý Dữ liệu Mẫu theo Ngành:** Khởi tạo phễu bán hàng theo ngành nghề, vai trò hệ thống, cơ cấu tổ chức và **Trình quản lý nạp/xóa sạch dữ liệu mẫu 1-Click (Sample Data Purge)**.
- **Nhóm F: Trải nghiệm Hướng dẫn Sau Đăng nhập (In-App FTUX & Checklist):** Màn hình chào mừng (Welcome Modal), Bảng tiến độ 5 bước bắt đầu nhanh (In-App Checklist) và hướng dẫn tính năng tương tác (Product Tour).
- **Nhóm G: Tiếp nhận, Chào mừng & Phân quyền Thành viên Mới:** Mời thành viên theo email có thương hiệu, Welcome Teammate Screen, phân bổ phòng ban/quản lý tự động và vai trò tối thiểu an toàn.
- **Nhóm H: Khởi tạo Khách hàng Doanh nghiệp (SLG / White-Glove):** Cổng quản trị nội bộ cấp phát SLG, kích hoạt quản trị viên không mật khẩu và cấp phát trần quyền tính năng mở rộng.
- **Nhóm I: Quản trị Vòng đời Dùng thử & Chuyển đổi Nâng cấp:** Quản lý đếm ngược ngày dùng thử, chuỗi email nuôi dưỡng tự động (Drip Campaigns) và thông báo nhắc nâng cấp gói cước.
- **Nhóm J: Quản trị Vận hành Tổ chức, Sức khỏe Cấu hình & Chuyển giao:** Công cụ chẩn đoán cấu hình (`setup-health`), chuyển nhượng quyền sở hữu 2 bên (72h TTL) và tiến trình định kỳ dọn rác tài khoản mồ côi (24h).

**Ngoài phạm vi (thuộc về các tài liệu SRS chuyên biệt khác):**
- **Nghiệp vụ Nhập/Xuất danh bạ khách hàng nâng cao & Ánh xạ trường dữ liệu (Contact Import & Field Mapping Wizard):** Thuộc về SRS Quản lý Khách hàng & Danh bạ (`contacts-srs.md`). Onboarding chỉ đóng vai trò trigger điều hướng người dùng tới tính năng này trong Checklist.
- **Nghiệp vụ Cấu hình Kênh giao tiếp Omni-channel & Kịch bản Chatbot chi tiết:** Thuộc về SRS Kênh giao tiếp Đa kênh (`omni-channel-srs.md`) và SRS Livechat Widget (`livechat-widget-srs.md`). Onboarding chỉ thực hiện cấp phát không gian làm việc bot liên kết và điều hướng thiết lập kênh ban đầu.
- **Nghiệp vụ Quản trị Phễu bán hàng chi tiết, Trường tùy chỉnh & Tự động hóa bán hàng:** Thuộc về SRS Quản lý Phễu Bán hàng (`deals-pipeline-srs.md`). Onboarding chỉ khởi tạo cấu trúc phễu chuẩn ban đầu (Baseline Seeding).
- **Chi tiết Giao dịch thanh toán, Cổng thanh toán thẻ tín dụng & Hóa đơn thuế:** Thuộc về SRS Quản trị Đăng ký & Thanh toán (`billing-subscription-srs.md`). Onboarding chỉ kích hoạt thời hạn dùng thử (Trial) và hiển thị nút điều hướng nâng cấp.
- **Chi tiết Ma trận Phân quyền ABAC chuyên sâu & Chính sách truy cập dữ liệu:** Thuộc về SRS Quản lý Định danh & Phân quyền (`iam-tenant-authorization.md`). Onboarding chỉ thiết lập vai trò tối thiểu nền tảng (Least-Privilege Baseline) và định vị phòng ban gốc.

### 1.3 Đối tượng đọc

- **Product Owner / Business Analyst:** Nguồn tài liệu đặc tả mục tiêu chuẩn mực dùng để nghiệm thu tính năng, phân rã user stories và quản lý backlog phát triển.
- **Đội ngũ Phát triển (Frontend / Backend / Fullstack):** Căn cứ thiết kế kiến trúc hệ thống, xây dựng API, thiết kế giao diện UI/UX và logic nghiệp vụ.
- **Đội ngũ Kiểm thử (QA/QC):** Căn cứ thiết kế ma trận kịch bản kiểm thử (Test Matrix) và kiểm thử chấp nhận người dùng (UAT).
- **Đội ngũ Kinh doanh & Marketing (Sales / Growth / Customer Success):** Nắm rõ hành trình khách hàng để tối ưu phễu chuyển đổi, kịch bản nuôi dưỡng email và quy trình chăm sóc khách hàng doanh nghiệp lớn.

### 1.4 Thuật ngữ & Viết tắt

| Thuật ngữ | Định nghĩa nghiệp vụ |
| --- | --- |
| **Không gian làm việc (Workspace / Tenant)** | Môi trường dữ liệu và vận hành độc lập hoàn toàn của một doanh nghiệp/tổ chức trên hệ thống CRM. |
| **Dùng thử Miễn phí 14 ngày (14-Day Free Trial)** | Chính sách tự động cấp quyền trải nghiệm toàn bộ tính năng cao cấp của gói Chuyên nghiệp trong 14 ngày ngay sau khi đăng ký, không cần nhập thẻ tín dụng. |
| **Tên miền phụ tổ chức (Tenant Subdomain / Alias)** | Định danh URL duy nhất đại diện cho workspace (ví dụ: `acme.crmsaudi.dev`). |
| **Tên miền Riêng Tùy chỉnh (Custom Domain CNAME)** | Tính năng cho phép doanh nghiệp trỏ tên miền thương hiệu riêng (ví dụ: `crm.acmecorp.com`) vào hệ thống CRM. |
| **Bảng tiến độ Tiếp nhận Tương tác (In-App Checklist & FTUX)** | Danh mục 5 hành động cốt lõi hiển thị trên màn hình chính sau đăng nhập lần đầu giúp người dùng làm quen và kích hoạt sản phẩm. |
| **Trình Quản lý Dữ liệu Mẫu & Xóa 1-Click (Sample Data Purge)** | Công cụ cho phép người dùng chủ động xóa sạch toàn bộ dữ liệu mẫu ban đầu chỉ bằng 1 nút bấm khi bắt đầu nhập dữ liệu thật. |
| **Phễu Bán hàng Đặc thù theo Ngành (Industry-Specific Pipeline)** | Phễu bán hàng có các giai đoạn được cấu hình phù hợp riêng cho từng ngành nghề (Bất động sản, Bán lẻ, Dịch vụ B2B, Tài chính). |
| **Cảnh báo Khách hàng Doanh nghiệp Tiềm năng (Enterprise Sales Alert)** | Thông báo tức thì gửi tới đội ngũ Sales nội bộ khi có doanh nghiệp quy mô lớn (`200+`) đăng ký tài khoản mới. |
| **Chuỗi Email Nuôi dưỡng Tự động (Onboarding Drip Campaign)** | Chuỗi email tự động gửi vào các ngày 1, 3, 7 và 12 để hướng dẫn và thúc đẩy khách hàng hoàn tất kích hoạt tính năng. |
| **Chuỗi giao dịch bù trừ (Provisioning Saga & Compensation)** | Chuỗi tác vụ phân tán ngầm qua Keycloak, MongoDB, Chatbot và Redis có khả năng tự phục hồi hoặc hoàn tác bù trừ khi gặp sự cố. |
| **Chuyển nhượng Quyền sở hữu (Two-Party Ownership Handover)** | Quy trình chuyển giao vai trò Owner giữa 2 thành viên trong tổ chức, yêu cầu người nhận xác nhận trong vòng 72 giờ. |

---

## 2. Tổng quan nghiệp vụ

### 2.1 Vấn đề mà module giải quyết

Phân hệ Onboarding giải quyết 4 bài toán sống còn trong kinh doanh B2B SaaS:
1. **Tối ưu hóa Thời gian Tiếp cận Giá trị (Time-to-Value — TTV):** Đưa người dùng từ bước đăng ký đến khoảnh khắc "Aha Moment" (nhìn thấy phễu bán hàng và khách hàng mẫu đúng ngành nghề của mình) trong vòng dưới **90 giây**.
2. **Thúc đẩy Tỷ lệ Kích hoạt & Chuyển đổi Trả phí (Product-Led Conversion):** Thay vì gán gói Free nghèo nàn tính năng, tự động trao quyền trải nghiệm 14 ngày gói Pro đầy đủ năng lực, kết hợp Bảng hướng dẫn tương tác (Checklist) và chuỗi Email nuôi dưỡng (Drip Campaigns) để tối đa hóa tỷ lệ chuyển đổi sang hợp đồng trả phí.
3. **Bảo toàn Tính Chuyên nghiệp & Nhận diện Thương hiệu Doanh nghiệp:** Hỗ trợ tên miền riêng tùy chỉnh (CNAME), thư mời thành viên có thương hiệu công ty và trải nghiệm chào mừng nhân viên mới (Teammate FTUX) chuyên nghiệp.
4. **Vận hành Hệ thống Sạch sẽ, Ổn định & An toàn:** Tự động thu hồi tài khoản bỏ rơi sau 24h, hoàn tác tài nguyên rác phân tán, chẩn đoán sức khỏe cấu hình phòng chống màn hình trắng và chuyển nhượng quyền sở hữu minh bạch có kiểm toán.

### 2.2 Vai trò người dùng (Actor)

| Actor | Mô tả vai trò và quyền hạn |
| --- | --- |
| **Khách vãng lai (Public Visitor)** | Người dùng chưa đăng nhập, tiếp cận trang đăng ký để tạo tài khoản và dùng thử sản phẩm. |
| **Người dùng đang Onboard (Onboarding User)** | Người đã hoàn thành Bước 1 (tài khoản) đang trong quá trình khai báo doanh nghiệp và mục tiêu sử dụng. |
| **Chủ sở hữu Không gian làm việc (Tenant Owner)** | Người đăng ký khởi tạo workspace, giữ quyền cấu hình tối cao và là người nhận chính sách dùng thử 14 ngày. |
| **Quản trị viên Không gian làm việc (Tenant Admin)** | Người được ủy quyền quản trị tổ chức, mời thành viên và thiết lập cấu hình nghiệp vụ. |
| **Thành viên Không gian làm việc (Tenant Member)** | Nhân viên được mời tham gia tổ chức, thao tác dữ liệu theo vai trò và phòng ban được phân công. |
| **Quản trị viên Nền tảng (Platform Super Admin)** | Đội ngũ vận hành nội bộ, có quyền cấp phát SLG cho khách hàng lớn và theo dõi phễu chuyển đổi toàn hệ thống. |
| **Tiến trình Hệ thống (System Engine / Workers / Cron)** | Các tiến trình tự động ngầm điều phối khởi tạo saga, gửi email nuôi dưỡng, chẩn đoán cấu hình và dọn dẹp tài nguyên. |

### 2.3 Bảng tổng hợp 32 tính năng nghiệp vụ To-Be

| Nhóm | Mã FEAT | Tên tính năng nghiệp vụ | Trạng thái |
| --- | --- | --- | --- |
| **A. Đăng ký & Dùng thử (PLG)** | `FEAT-01` | Đăng ký thông tin tài khoản & Xác thực an toàn (Bước 1) | `[Đã triển khai]` |
| | `FEAT-02` | Kích hoạt tự động 14 ngày Dùng thử Miễn phí gói Pro (14-Day Free Trial) | `[Yêu cầu mới]` |
| | `FEAT-03` | Đăng nhập & Đăng ký nhanh qua Google / Microsoft (Social SSO) | `[Yêu cầu mới]` |
| **B. Doanh nghiệp & Ngành nghề** | `FEAT-04` | Khai báo thông tin doanh nghiệp & Quy mô nhân sự (Bước 2) | `[Đã triển khai]` |
| | `FEAT-05` | Lựa chọn Ngành nghề kinh doanh & Mục tiêu sử dụng (Bước 3) | `[Yêu cầu mới]` |
| | `FEAT-06` | Thiết lập Bản địa hoá Nền tảng Ban đầu (Ngôn ngữ, Múi giờ, Tiền tệ) | `[Yêu cầu mới]` |
| | `FEAT-07` | Cảnh báo Khách hàng Doanh nghiệp Tiềm năng Quy mô lớn (Enterprise Sales Alert) | `[Yêu cầu mới]` |
| **C. Định danh & Tên miền** | `FEAT-08` | Tự động chuẩn hoá & Gợi ý tên miền phụ thông minh | `[Đã triển khai]` |
| | `FEAT-09` | Chỉnh sửa tên miền phụ & Kiểm tra xung đột nghiêm ngặt | `[Đã triển khai]` |
| | `FEAT-10` | Giữ chỗ tên miền phụ tạm thời (TTL 30 phút) & Bảo vệ tên miền hệ thống | `[Đã triển khai]` |
| | `FEAT-11` | Cấu hình Tên miền Riêng Tùy chỉnh Doanh nghiệp (Custom Domain CNAME) | `[Yêu cầu mới]` |
| **D. Khởi tạo Bất đồng bộ (Saga)** | `FEAT-12` | Màn hình chờ khởi tạo truyền cảm hứng (Progress Storytelling) | `[Yêu cầu mới]` |
| | `FEAT-13` | Điều phối chuỗi khởi tạo tài nguyên phân tán 10 bước ngầm | `[Đã triển khai]` |
| | `FEAT-14` | Hoàn tác bù trừ tự động khi gặp lỗi vĩnh viễn (Rollback Saga) | `[Đã triển khai]` |
| | `FEAT-15` | Khởi tạo không gian làm việc Chatbot liên kết (`crm-bot`) | `[Đã triển khai]` |
| **E. Phễu Bán hàng & Dữ liệu Mẫu** | `FEAT-16` | Khởi tạo Phễu bán hàng & Quy trình hỗ trợ theo đặc thù Ngành nghề | `[Yêu cầu mới]` |
| | `FEAT-17` | Thiết lập vai trò hệ thống, cơ cấu tổ chức & nhóm chủ sở hữu | `[Đã triển khai]` |
| | `FEAT-18` | Nạp Dữ liệu Mẫu cá nhân hoá theo Ngành nghề & Bản địa hoá (SAR/VND) | `[Yêu cầu mới]` |
| | `FEAT-19` | Trình Quản lý Dữ liệu Mẫu & Nút bấm Xóa sạch 1-Click (Sample Data Purge) | `[Yêu cầu mới]` |
| **F. Hướng dẫn Sau Đăng nhập** | `FEAT-20` | Màn hình Chào mừng Lần đầu Đăng nhập (Welcome Modal & Value Proposition) | `[Yêu cầu mới]` |
| | `FEAT-21` | Bảng tiến độ Tiếp nhận Tương tác 5 bước (In-App Onboarding Checklist) | `[Yêu cầu mới]` |
| | `FEAT-22` | Hướng dẫn Tính năng Trực quan Tương tác (Interactive Product Tour) | `[Yêu cầu mới]` |
| **G. Tiếp nhận Thành viên Mới** | `FEAT-23` | Thư mời thành viên mới mang dấu ấn thương hiệu doanh nghiệp | `[Yêu cầu mới]` |
| | `FEAT-24` | Trải nghiệm Chào mừng Thành viên Mới (Welcome Teammate FTUX) | `[Yêu cầu mới]` |
| | `FEAT-25` | Tự động phân bổ phòng ban, quản lý trực tiếp & vai trò tối thiểu an toàn | `[Đã triển khai]` |
| **H. Khởi tạo Doanh nghiệp (SLG)** | `FEAT-26` | Cổng cấp phát không gian làm việc SLG cho Quản trị Nền tảng | `[Đã triển khai]` |
| | `FEAT-27` | Kích hoạt tài khoản quản trị viên doanh nghiệp không mật khẩu qua email | `[Đã triển khai]` |
| | `FEAT-28` | Cấp phát trần quyền tính năng mở rộng cho doanh nghiệp | `[Đã triển khai]` |
| **I. Vòng đời Dùng thử & Nuôi dưỡng** | `FEAT-29` | Thanh trạng thái đếm ngược ngày dùng thử & Nút nâng cấp trực tiếp | `[Yêu cầu mới]` |
| | `FEAT-30` | Chuỗi Email Nuôi dưỡng Tự động theo Hành vi (Onboarding Drip Campaigns) | `[Yêu cầu mới]` |
| **J. Vận hành, Sức khỏe & Chuyển giao** | `FEAT-31` | Giám sát sức khỏe cấu hình phòng chống màn hình trắng (`setup-health`) | `[Đã triển khai]` |
| | `FEAT-32` | Chuyển nhượng quyền sở hữu không gian làm việc 2 bên (72h TTL) | `[Đã triển khai]` |

---

## 3. Đặc tả yêu cầu chức năng

## A. ĐĂNG KÝ TÀI KHOẢN & KÍCH HOẠT DÙNG THỬ (PLG ONBOARDING WIZARD)

### FEAT-01 — Đăng ký thông tin tài khoản & Xác thực an toàn (Bước 1) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khách vãng lai đăng ký tài khoản ban đầu bằng Họ tên, Email doanh nghiệp và Mật khẩu. Hệ thống kích hoạt phiên làm việc và kiểm tra an toàn danh tính.

**Actor:** Khách vãng lai.

**Quy tắc nghiệp vụ:**
- `BR-01.1 (Kiểm tra trùng lặp email)`: Nếu email đã tồn tại trong hệ thống xác thực (Keycloak) hoặc cơ sở dữ liệu người dùng, hệ thống từ chối tạo mới và yêu cầu chuyển sang trang Đăng nhập.
- `BR-01.2 (Độ mạnh mật khẩu)`: Mật khẩu tối thiểu 8 ký tự, bắt buộc chứa chữ hoa, chữ thường, chữ số và ký tự đặc biệt.
- `BR-01.3 (An toàn danh tính)`: Mật khẩu được mã hóa an toàn trên Keycloak và xóa ngay lập tức khỏi bộ nhớ ứng dụng client sau khi gửi.
- `BR-01.4 (Khởi tạo phiên tiếp nhận)`: Tạo bản ghi người dùng ở trạng thái `INCOMPLETE_ONBOARDING` và lưu phiên tiếp nhận tạm thời trên Redis (TTL 24 giờ).

---

### FEAT-02 — Kích hoạt tự động 14 ngày Dùng thử Miễn phí gói Pro (14-Day Free Trial) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Mọi không gian làm việc đăng ký tự phục vụ (PLG) đều được hệ thống tự động kích hoạt **14 ngày dùng thử miễn phí gói Pro** ngay khi hoàn tất khởi tạo, không yêu cầu thẻ tín dụng, cho phép trải nghiệm toàn bộ tính năng cao cấp.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-02.1 (Tự động kích hoạt gói dùng thử)`: Bản ghi tenant được thiết lập `subscriptionPlan = 'PRO'` kèm trường `trialEndsAt = createdAt + 14 ngày`.
- `BR-02.2 (Hạn mức dùng thử cao cấp)`: Cấp hạn mức lưu trữ **10 GB** (thay vì 1GB của gói Free), mở toàn bộ các kênh kết nối Omni-channel và tính năng tự động hóa bot.
- `BR-02.3 (Chính sách không rủi ro)`: Không yêu cầu người dùng nhập thông tin thanh toán (thẻ tín dụng) trong suốt thời gian dùng thử.

---

### FEAT-03 — Đăng nhập & Đăng ký nhanh qua Google / Microsoft (Social SSO) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép khách hàng đăng ký và đăng nhập 1 chạm bằng tài khoản Google Workspace hoặc Microsoft Entra ID (Office 365) để rút ngắn tối đa thời gian tạo tài khoản.

**Actor:** Khách vãng lai.

**Quy tắc nghiệp vụ:**
- `BR-03.1`: Tự động trích xuất Họ tên, Địa chỉ Email đã xác thực và Ảnh đại diện (Avatar) từ nhà cung cấp danh tính Google/Microsoft.
- `BR-03.2`: Tự động bỏ qua bước nhập mật khẩu và đưa người dùng tiến thẳng vào Bước 2 (Khai báo doanh nghiệp).

---

## B. HỒ SƠ DOANH NGHIỆP, NGÀNH NGHỀ & BẢN ĐỊA HOÁ

### FEAT-04 — Khai báo thông tin doanh nghiệp & Quy mô nhân sự (Bước 2) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Thu thập tên doanh nghiệp và quy mô đội ngũ để định hình cơ cấu phòng ban và nhu cầu sử dụng.

**Actor:** Người dùng đang Onboard.

**Quy tắc nghiệp vụ:**
- `BR-04.1`: Tên công ty bắt buộc có độ dài từ 2 đến 150 ký tự.
- `BR-04.2`: Quy mô nhân sự chọn 1 trong 4 nhóm: `1–10` (Nhóm nhỏ), `11–50` (Doanh nghiệp nhỏ), `51–200` (Doanh nghiệp vừa), `200+` (Doanh nghiệp lớn).
- `BR-04.3`: Quy mô `1-10` sẽ khởi tạo cấu hình gọn nhẹ không chia nhỏ phòng ban; quy mô lớn hơn sẽ sinh cây phòng ban theo ngành nghề.

---

### FEAT-05 — Lựa chọn Ngành nghề kinh doanh & Mục tiêu sử dụng (Bước 3) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Người dùng lựa chọn Ngành nghề kinh doanh cốt lõi (Bất động sản, Bán lẻ/Thương mại, Dịch vụ B2B, Tài chính/Bảo hiểm) kết hợp Mục tiêu sử dụng để hệ thống tự động cấu hình phễu bán hàng và dữ liệu mẫu chuẩn ngành.

**Actor:** Người dùng đang Onboard.

**Quy tắc nghiệp vụ:**
- `BR-05.1 (Danh mục Ngành nghề)`: Cung cấp danh mục lựa chọn trực quan:
  - **Bất động sản (`real_estate`):** Phễu theo dự án, bảng hàng, đàm phán cọc, ký hợp đồng chuyển nhượng.
  - **Dịch vụ B2B & Phần mềm (`b2b_services`):** Phễu khảo sát nhu cầu, Demo giải pháp, Báo giá, Đàm phán, Ký kết.
  - **Bán lẻ & Thương mại (`retail_ecommerce`):** Phễu chăm sóc khách hàng VIP, xử lý đơn hàng lớn, tái mua hàng.
  - **Tài chính, Bảo hiểm & Tư vấn (`finance_insurance`):** Phễu thẩm định hồ sơ, phê duyệt tín dụng, ký kết hợp đồng.
  - **Tổng hợp / Ngành nghề khác (`general`):** Phễu bán hàng tiêu chuẩn đa năng.
- `BR-05.2 (Tự động kích hoạt khởi tạo)`: Ngay khi người dùng chọn Ngành nghề, hệ thống tự động chuyển sang Màn hình chờ khởi tạo mà không cần nút bấm phụ.

---

### FEAT-06 — Thiết lập Bản địa hoá Nền tảng Ban đầu (Ngôn ngữ, Múi giờ, Tiền tệ) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Tự động nhận diện hoặc cho phép lựa chọn vị trí địa lý để thiết lập Ngôn ngữ hiển thị, Múi giờ và Đồng tiền mặc định phù hợp với quốc gia của khách hàng (đặc biệt tối ưu cho thị trường Ả Rập Xê Út và Việt Nam).

**Actor:** Tiến trình Hệ thống, Người dùng đang Onboard.

**Quy tắc nghiệp vụ:**
- `BR-06.1 (Thị trường Ả Rập Xê Út - KSA)`: Tự động đề xuất Locale tiếng Ả Rập (`ar`), Múi giờ `Asia/Riyadh` (GMT+3), Định dạng ngày `DD/MM/YYYY` và Tiền tệ `SAR` (Saudi Riyal).
- `BR-06.2 (Thị trường Việt Nam)`: Tự động đề xuất Locale tiếng Việt (`vi`), Múi giờ `Asia/Ho_Chi_Minh` (GMT+7), Định dạng ngày `DD/MM/YYYY` và Tiền tệ `VND` (Đồng).
- `BR-06.3 (Thị trường Quốc tế)`: Mặc định Locale tiếng Anh (`en`), Múi giờ `UTC`, Tiền tệ `USD`.

---

### FEAT-07 — Cảnh báo Khách hàng Doanh nghiệp Tiềm năng Quy mô lớn (Enterprise Sales Alert) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khi phát hiện khách hàng đăng ký có quy mô nhân sự lớn (`200+`), hệ thống tự động phát sinh thông báo ưu tiên (Lead Alert) tới đội ngũ kinh doanh nội bộ để phân bổ chuyên viên tư vấn hỗ trợ trực tiếp.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-07.1`: Phát sinh sự kiện nội bộ `lead.enterprise_signup` khi `teamSize === '200+'`.
- `BR-07.2`: Tự động gửi thông báo qua kênh vận hành nội bộ (Slack / Email điều hành) kèm thông tin: Tên công ty, Email quản trị viên, Ngành nghề và Tên miền phụ.

---

## C. ĐỊNH DANH KHÔNG GIAN LÀM VIỆC & TÊN MIỀN RIÊNG

### FEAT-08 — Tự động chuẩn hoá & Gợi ý tên miền phụ thông minh `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động sinh tên miền phụ gợi ý từ tên doanh nghiệp theo chuẩn URL-safe quốc tế và xử lý tiếng Việt hoàn hảo.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-08.1`: Chuyển đổi chính xác chữ `Đ/đ` thành `D/d`, loại bỏ dấu thanh tiếng Việt qua chuẩn Unicode NFD.
- `BR-08.2`: Chuyển toàn bộ thành chữ thường (`a-z`, `0-9`), thay thế khoảng trắng và ký tự đặc biệt bằng dấu gạch ngang (`-`), cắt tỉa đầu cuối, độ dài từ 3 đến 50 ký tự.
- `BR-08.3`: Tự động bổ sung hậu tố ngẫu nhiên tối đa 5 lần nếu tên miền gợi ý bị trùng với tổ chức khác đã đăng ký.

---

### FEAT-09 — Chỉnh sửa tên miền phụ & Kiểm tra xung đột nghiêm ngặt `[Đã triển khai]`

**Mô tả nghiệp vụ:** Người dùng có quyền tự gõ tên miền phụ mong muốn. Hệ thống kiểm tra tính hợp lệ và báo lỗi trực tiếp nếu trùng lặp.

**Actor:** Người dùng đang Onboard.

**Quy tắc nghiệp vụ:**
- `BR-09.1`: Tên miền phụ tự nhập phải khớp chuẩn `^[a-z0-9](?:[a-z0-9-]{1,48}[a-z0-9])?$`.
- `BR-09.2`: Khi người dùng **tự nhập** tên miền mà bị trùng, hệ thống **bắt buộc báo lỗi xung đột trực tiếp** (`ALIAS_ALREADY_EXISTS`), tuyệt đối không tự ý gắn thêm hậu tố ngẫu nhiên làm biến dạng tên thương hiệu người dùng đã chọn.

---

### FEAT-10 — Giữ chỗ tên miền phụ tạm thời (TTL 30 phút) & Bảo vệ tên miền hệ thống `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cơ chế khóa giữ chỗ chống chiếm đoạt tên miền phụ và chặn cứng danh mục tên miền hệ thống.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-10.1 (Chặn tên miền hệ thống)`: Cấm đăng ký các định danh hệ thống: `api`, `admin`, `auth`, `www`, `mail`.
- `BR-10.2 (Thời gian giữ chỗ)`: Khóa giữ chỗ trong `tenant_alias_reservations` có hiệu lực tự động hủy sau **30 phút** qua MongoDB TTL Index nếu saga không hoàn tất.
- `BR-10.3 (Xác nhận vĩnh viễn)`: Khi khởi tạo thành công, hệ thống chuyển trạng thái sang `CONFIRMED` và xóa trường `expiresAt` để giữ chỗ vĩnh viễn cho tenant.

---

### FEAT-11 — Cấu hình Tên miền Riêng Tùy chỉnh Doanh nghiệp (Custom Domain CNAME) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cho phép doanh nghiệp sử dụng tên miền thương hiệu riêng của mình (ví dụ `crm.acmecorp.com`) thay cho subdomain mặc định (`acmecorp.crmsaudi.dev`).

**Actor:** Quản trị viên Workspace, Chủ sở hữu Workspace.

**Quy tắc nghiệp vụ:**
- `BR-11.1 (Xác thực bản ghi DNS)`: Doanh nghiệp trỏ bản ghi CNAME từ tên miền riêng về địa chỉ đích của hệ thống CRM.
- `BR-11.2 (Cấp phát SSL Tự động)`: Hệ thống tự động kiểm tra bản ghi DNS và cấp phát chứng chỉ bảo mật SSL/TLS miễn phí (Let's Encrypt / Cloudflare SSL).
- `BR-11.3 (Định tuyến thông minh)`: Khi người dùng truy cập qua tên miền riêng, hệ thống tự động nhận diện đúng workspace và áp dụng giao diện nhận diện thương hiệu tương ứng.

---

## D. KHỞI TẠO KHÔNG GIAN LÀM VIỆC BẤT ĐỒNG BỘ (PROVISIONING SAGA)

### FEAT-12 — Màn hình chờ khởi tạo truyền cảm hứng (Progress Storytelling) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Màn hình chờ trực quan với thanh tiến trình mượt mà và các thông điệp nghiệp vụ truyền cảm hứng, xóa bỏ hoàn toàn các thuật ngữ kỹ thuật khó hiểu.

**Actor:** Người dùng đang Onboard.

**Quy tắc nghiệp vụ:**
- `BR-12.1 (Thông điệp nghiệp vụ)`: Thay thế các nhãn kỹ thuật bằng chuỗi thông điệp trải nghiệm:
  - 10%: Đang chuẩn bị không gian làm việc cho doanh nghiệp của bạn…
  - 30%: Đang thiết lập phễu bán hàng & quy trình chăm sóc khách hàng…
  - 60%: Đang cấu hình bảo mật & phân quyền đội ngũ…
  - 80%: Đang nạp dữ liệu mẫu và chuẩn bị bảng hướng dẫn…
  - 100%: Không gian làm việc đã sẵn sàng! Chào mừng bạn gia nhập CRM Saudi.
- `BR-12.2 (Trải nghiệm chờ)`: Hiển thị thanh tiến trình hoạt ảnh, thông tin ngắn về giá trị sản phẩm và tự động chuyển hướng sau 2 giây khi hoàn tất.

---

### FEAT-13 — Điều phối chuỗi khởi tạo tài nguyên phân tán 10 bước ngầm `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tiến trình ngầm (BullMQ Worker) điều phối chuỗi giao dịch phân tán 10 bước đảm bảo tính lũy tiến (Idempotent) và an toàn toàn vẹn dữ liệu.

**Actor:** Tiến trình Hệ thống.

**Chi tiết chuỗi 10 bước:**
1. Khóa giữ chỗ tên miền phụ (Reserve Alias).
2. Tạo Tổ chức xác thực trên Keycloak (KC Organization).
3. Tạo/Đồng bộ Tài khoản xác thực trên Keycloak (KC User).
4. Phân bổ Người dùng vào Tổ chức Keycloak (Add User to Org).
5. Tạo bản ghi Workspace trong MongoDB (`tenants`) với trạng thái `PROVISIONING` và gói cước dùng thử `PRO`.
6. Cấu hình tư cách thành viên `OWNER` trong hồ sơ người dùng (`users`) và hoàn tất onboarding.
7. Xác lập quyền Chủ sở hữu workspace (`ownerId`).
8. Khởi tạo Không gian làm việc Chatbot liên kết (`crm-bot`).
9. Xác nhận giữ chỗ tên miền phụ vĩnh viễn (`CONFIRMED`).
10. Chuyển trạng thái tenant sang `READY` và phát sự kiện `tenant.created` để kích hoạt nạp cấu hình và dữ liệu nền tảng.

---

### FEAT-14 — Hoàn tác bù trừ tự động khi gặp lỗi vĩnh viễn (Rollback Saga) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khi saga thất bại vĩnh viễn sau các lần thử lại, hệ thống tự động hoàn tác bù trừ theo thứ tự ngược lại để dọn sạch 100% tài nguyên rác phân tán.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-14.1`: Thứ tự hoàn tác: Xóa Bot Workspace → Xóa tư cách thành viên/Người dùng cục bộ → Xóa bản ghi Tenant MongoDB → Xóa Tổ chức Keycloak → Xóa Tài khoản Keycloak → Xóa bản ghi giữ chỗ Alias.
- `BR-14.2`: Hoàn tác chỉ thực hiện ở lần thử thất bại cuối cùng, bảo toàn trạng thái trung gian trong các lần thử lại của BullMQ.

---

### FEAT-15 — Khởi tạo không gian làm việc Chatbot liên kết (`crm-bot`) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Tự động cấp phát không gian làm việc chatbot độc lập trên dịch vụ `crm-bot` và liên kết với CRM.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-15.1`: Giao tiếp an toàn qua endpoint nội bộ `/api/internal/workspaces/provision` kèm khóa bí mật `CRM_BOT_INTERNAL_SECRET`.
- `BR-15.2`: Đảm bảo tính lũy tiến — nếu gọi lại sẽ trả về bot workspace hiện hữu thay vì tạo mới.

---

## E. THIẾT LẬP PHỄU BÁN HÀNG & QUẢN LÝ DỮ LIỆU MẪU THEO NGÀNH

### FEAT-16 — Khởi tạo Phễu Bán hàng & Quy trình hỗ trợ theo đặc thù Ngành nghề `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Tự động tạo Phễu bán hàng (Pipeline) có các giai đoạn phù hợp chính xác với Ngành nghề kinh doanh mà khách hàng đã chọn ở Bước 3.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-16.1 (Ngành Bất động sản)`: Khởi tạo phễu "Quy trình Môi giới & Bán Bất động sản" gồm 5 giai đoạn: Tiếp nhận nhu cầu → Khảo sát thực địa/Dự án → Đặt cọc giữ chỗ → Ký hợp đồng mua bán → Bàn giao & Thu phí.
- `BR-16.2 (Ngành Dịch vụ B2B & Phần mềm)`: Khởi tạo phễu "Quy trình Bán hàng Doanh nghiệp B2B" gồm 5 giai đoạn: Khảo sát nhu cầu → Trình bày giải pháp/Demo → Đề xuất báo giá → Đàm phán hợp đồng → Thành công (Closed Won) / Thất bại.
- `BR-16.3 (Ngành Bán lẻ & Thương mại)`: Khởi tạo phễu "Quy trình Bán buôn & Khách hàng Lớn" gồm: Tiếp cận đại lý → Báo giá sỉ → Thỏa thuận chiết khấu → Đơn hàng thử nghiệm → Khách hàng thân thiết.
- `BR-16.4`: Tạo Quy trình Tiếp nhận & Xử lý Vé Hỗ trợ (Ticket Workflow) chuẩn gồm 5 trạng thái: Mới tiếp nhận, Đang xử lý, Đang chờ khách hàng, Đã giải quyết, Đã đóng.

---

### FEAT-17 — Thiết lập vai trò hệ thống, cơ cấu tổ chức & nhóm chủ sở hữu `[Đã triển khai]`

**Mô tả nghiệp vụ:** Khởi tạo 6 vai trò hệ thống dựng sẵn kèm phạm vi dữ liệu, đơn vị tổ chức gốc HQ, các phòng ban con theo ngành và nhóm Owner.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-17.1`: Tạo 6 vai trò: Quản trị viên (Admin), Quản lý Kinh doanh, Nhân viên Kinh doanh, Quản lý Hỗ trợ, Nhân viên Hỗ trợ, Chỉ đọc (Read Only).
- `BR-17.2`: Tạo đơn vị tổ chức gốc `HQ` gán Owner làm Trưởng đơn vị. Tự động sinh các phòng ban con (`SALES`, `SUPPORT`, `MARKETING`, `OPERATIONS`) dựa trên quy mô và ngành nghề.
- `BR-17.3`: Tạo nhóm người dùng `"Owner"` và thêm tài khoản của Owner vào nhóm.

---

### FEAT-18 — Nạp Dữ liệu Mẫu cá nhân hoá theo Ngành nghề & Bản địa hoá (SAR/VND) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Nạp các bản ghi Khách hàng (Contacts), Doanh nghiệp (Accounts) và Cơ hội (Deals) mẫu mang ngữ cảnh thực tế của ngành nghề và bản địa hoá theo ngôn ngữ/tiền tệ của quốc gia.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-18.1`: Đối với thị trường Ả Rập: Doanh nghiệp và Khách hàng mẫu mang tên tiếng Ả Rập/Anh thực tế tại Riyadh/Jeddah, tiền tệ `SAR`.
- `BR-18.2`: Đối với thị trường Việt Nam: Doanh nghiệp và Khách hàng mẫu mang tên tiếng Việt thực tế tại Hà Nội/TP.HCM, tiền tệ `VND`.
- `BR-18.3`: Dữ liệu mẫu được gắn cờ hệ thống `isSample = true` để phục vụ công cụ quản lý và xóa sạch dữ liệu mẫu sau này.

---

### FEAT-19 — Trình Quản lý Dữ liệu Mẫu & Nút bấm Xóa sạch 1-Click (Sample Data Purge) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cung cấp thanh công cụ nổi (Banner) và nút bấm trong Cài đặt cho phép người dùng chủ động **Xóa sạch toàn bộ dữ liệu mẫu (1-Click Sample Data Purge)** khi sẵn sàng vận hành dữ liệu thật.

**Actor:** Chủ sở hữu Workspace, Quản trị viên Workspace.

**Quy tắc nghiệp vụ:**
- `BR-19.1 (Thanh banner dữ liệu mẫu)`: Khi không gian làm việc còn chứa dữ liệu mẫu, hiển thị banner thông báo màu xanh nhạt: *"Bạn đang xem dữ liệu mẫu để làm quen hệ thống. Bạn có thể xóa dữ liệu mẫu bất kỳ lúc nào để bắt đầu nhập dữ liệu thật."* kèm nút **"Xóa dữ liệu mẫu"**.
- `BR-19.2 (Hành động xóa sạch nguyên tử)`: Khi bấm xác nhận xóa dữ liệu mẫu:
  - Hệ thống thực thi xóa toàn bộ các bản ghi Contacts, Accounts, Deals có cờ `isSample === true`.
  - Giữ nguyên toàn bộ cấu hình phễu bán hàng, quy trình ticket, vai trò và phòng ban đã thiết lập.
  - Tự động ẩn banner dữ liệu mẫu.

---

## F. TRẢI NGHIỆM HƯỚNG DẪN SAU ĐĂNG NHẬP (IN-APP FTUX & CHECKLIST)

### FEAT-20 — Màn hình Chào mừng Lần đầu Đăng nhập (Welcome Modal) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khi Chủ sở hữu đăng nhập vào workspace lần đầu tiên, hiển thị cửa sổ Chào mừng trang trọng giới thiệu các giá trị cốt lõi và nút bắt đầu hành trình khám phá.

**Actor:** Chủ sở hữu Workspace.

**Quy tắc nghiệp vụ:**
- `BR-20.1`: Hiển thị Welcome Modal kèm lời chào mừng cá nhân hóa: *"Chào mừng [Họ tên] đến với không gian làm việc [Tên công ty]!"*.
- `BR-20.2`: Cung cấp 2 lựa chọn: **"Bắt đầu thiết lập nhanh (Khuyên dùng)"** (mở Onboarding Checklist) hoặc **"Tôi muốn tự khám phá"** (đóng modal).

---

### FEAT-21 — Bảng tiến độ Tiếp nhận Tương tác 5 bước (In-App Onboarding Checklist) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Một widget tiến độ hiển thị ở góc màn hình chính gồm 5 nhiệm vụ then chốt giúp người dùng làm chủ hệ thống trong ngày đầu tiên, kèm thanh phần trăm hoàn thành (0% -> 100%).

**Actor:** Chủ sở hữu Workspace, Quản trị viên Workspace.

**Chi tiết 5 nhiệm vụ bắt đầu nhanh:**
1. **Nhiệm vụ 1: Kết nối Kênh liên lạc đầu tiên** (Cài đặt Livechat widget lên website hoặc kết nối Email/WhatsApp) → Nhấp vào chuyển thẳng đến Cài đặt Kênh.
2. **Nhiệm vụ 2: Mời ít nhất 1 đồng nghiệp vào tổ chức** → Nhấp vào mở hộp thoại Mời thành viên.
3. **Nhiệm vụ 3: Nhập danh bạ khách hàng thật (hoặc tạo 1 liên hệ mới)** → Nhấp vào mở giao diện Thêm khách hàng.
4. **Nhiệm vụ 4: Tạo hoặc chuyển giai đoạn 1 Cơ hội bán hàng trên Phễu** → Nhấp vào mở bảng Kanban Deal.
5. **Nhiệm vụ 5: Tải ứng dụng di động hoặc tiện ích mở rộng** → Cung cấp mã QR tải app.

**Quy tắc nghiệp vụ:**
- `BR-21.1`: Mỗi khi người dùng hoàn thành 1 hành động thực tế trong hệ thống, hệ thống tự động đánh dấu tích xanh và tăng thanh tiến trình (mỗi bước 20%).
- `BR-21.2`: Khi đạt 100%, hiển thị hiệu ứng chúc mừng và cấp chứng nhận khởi tạo thành công (hoặc tặng thêm ngày dùng thử khích lệ). Người dùng có quyền ẩn checklist khi đã hoàn thành.

---

### FEAT-22 — Hướng dẫn Tính năng Trực quan Tương tác (Interactive Product Tour) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Cung cấp các điểm sáng tương tác (Spotlight / Tooltips) giới thiệu các khu vực chức năng chính trên giao diện (Hộp thư Omni-channel, Bảng Kanban Bán hàng, Báo cáo Doanh số) khi người dùng truy cập từng trang lần đầu.

**Actor:** Mọi người dùng trong Workspace.

**Quy tắc nghiệp vụ:**
- `BR-22.1`: Tour tương tác ngắn gọn (tối đa 3 bước mỗi trang), có nút "Bỏ qua" bất kỳ lúc nào.
- `BR-22.2`: Lưu trạng thái đã xem vào bộ nhớ người dùng để không hiển thị lặp lại gây phiền toái.

---

## G. TIẾP NHẬN, CHÀO MỪNG & PHÂN QUYỀN THÀNH VIÊN MỚI (MEMBER ONBOARDING)

### FEAT-23 — Thư mời thành viên mới mang dấu ấn thương hiệu doanh nghiệp `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Thư mời gửi tới thành viên mới có nhận diện thương hiệu rõ ràng (Logo công ty, Tên công ty mời, Tên người mời) thay vì email hệ thống chung chung.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-23.1`: Email mang tiêu đề: *"[Tên người mời] đã mời bạn tham gia không gian làm việc [Tên công ty] trên CRM Saudi"*.
- `BR-23.2`: Nội dung nêu rõ: Tên phòng ban phân bổ, Vai trò được giao, Nút bấm an toàn dùng 1 lần dẫn thẳng về trang Chào mừng Thành viên của subdomain doanh nghiệp.

---

### FEAT-24 — Trải nghiệm Chào mừng Thành viên Mới (Welcome Teammate FTUX) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Khi thành viên mới bấm vào thư mời, giao diện hiển thị trang Chào mừng đồng nghiệp trang trọng, hỗ trợ tự đặt mật khẩu riêng và giới thiệu ngắn về phòng ban/quản lý trực tiếp của họ.

**Actor:** Thành viên Workspace mới.

**Quy tắc nghiệp vụ:**
- `BR-24.1`: Thành viên tự nhập mật khẩu cá nhân mới (thỏa mãn tiêu chí an toàn).
- `BR-24.2`: Hiển thị thẻ tóm tắt: *"Bạn đã gia nhập phòng ban: [Tên phòng ban] | Quản lý trực tiếp: [Tên quản lý] | Vai trò: [Tên vai trò]"*.
- `BR-24.3`: Đăng nhập thẳng vào màn hình làm việc cá nhân, nhìn thấy ngay các dữ liệu thuộc phòng ban của mình mà không gặp tình trạng màn hình trắng.

---

### FEAT-25 — Tự động phân bổ phòng ban, quản lý trực tiếp & vai trò tối thiểu an toàn `[Đã triển khai]`

**Mô tả nghiệp vụ:** Đảm bảo nguyên tắc bảo mật quyền tối thiểu (Least-Privilege Baseline) và luôn định vị thành viên mới vào cây tổ chức.

**Actor:** Tiến trình Hệ thống.

**Quy tắc nghiệp vụ:**
- `BR-25.1 (Quyền tối thiểu mặc định)`: Luôn tự động gán vai trò Chỉ đọc (`sys.read_only`) nếu người mời không chọn vai trò cụ thể, tuyệt đối không để rỗng vai trò (`roleIds = []`).
- `BR-25.2 (Kế thừa phòng ban & Quản lý)`: Tự động gán phòng ban của người mời và đặt người mời làm Quản lý trực tiếp nếu không chỉ định, đảm bảo kích hoạt đúng phạm vi hiển thị `ORG_UNIT` và `SUBORDINATES`.

---

## H. KHỞI TẠO KHÁCH HÀNG DOANH NGHIỆP (SLG / WHITE-GLOVE)

### FEAT-26 — Cổng cấp phát không gian làm việc SLG cho Quản trị Nền tảng `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép đội ngũ vận hành nội bộ khởi tạo trước workspace cho khách hàng doanh nghiệp lớn thông qua API bảo mật nội bộ (`POST /api/v1/internal/tenants/provision`).

**Actor:** Quản trị viên Nền tảng.

**Quy tắc nghiệp vụ:**
- `BR-26.1`: Bảo vệ bằng `X-Internal-Api-Key`, hỗ trợ chỉ định gói cước tùy chỉnh (`Enterprise`), thiết lập hạn mức lưu trữ và quyền tính năng mở rộng ngay từ khi tạo.
- `BR-26.2`: Tác vụ được ghi nhận vào `provisioning_jobs` với nguồn `SLG` và đẩy vào hàng đợi BullMQ để worker xử lý ngầm.

---

### FEAT-27 — Kích hoạt tài khoản quản trị viên doanh nghiệp không mật khẩu qua email `[Đã triển khai]`

**Mô tả nghiệp vụ:** Gửi thư mời kích hoạt chính thức tới quản trị viên doanh nghiệp, sử dụng liên kết an toàn của Keycloak để khách hàng tự đặt mật khẩu riêng lần đầu.

**Actor:** Quản trị viên Nền tảng.

**Quy tắc nghiệp vụ:**
- `BR-27.1`: Kích hoạt hành động `UPDATE_PASSWORD` của Keycloak, đường dẫn hoàn tất trỏ về trang đăng nhập của subdomain doanh nghiệp.

---

### FEAT-28 — Cấp phát trần quyền tính năng mở rộng cho doanh nghiệp `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cho phép cấp thêm các quyền tính năng cao cấp (`FEATURE_PERMISSIONS`) vào trần quyền hạn của tenant và làm mới bộ nhớ đệm phân quyền tức thì.

**Actor:** Quản trị viên Nền tảng.

**Quy tắc nghiệp vụ:**
- `BR-28.1`: Cấp/thu hồi quyền qua API nội bộ, tự động xóa cache phân quyền `AuthzPermissionCacheService` và phát sự kiện cập nhật quyền tức thời.

---

## I. QUẢN TRỊ VÒNG ĐỜI DÙNG THỬ & NUÔI DƯỠNG

### FEAT-29 — Thanh trạng thái đếm ngược ngày dùng thử & Nút nâng cấp trực tiếp `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Hiển thị thanh thông báo đếm ngược số ngày dùng thử còn lại trên thanh tiêu đề ứng dụng (Header Banner), kèm nút nâng cấp trực tiếp sang hợp đồng chính thức.

**Actor:** Mọi người dùng trong Workspace (Đặc biệt là Chủ sở hữu & Quản trị viên).

**Quy tắc nghiệp vụ:**
- `BR-29.1 (Hiển thị đếm ngược)`: Hiển thị nhãn: *"Dùng thử Pro: Còn [N] ngày"* kèm nút **"Nâng cấp ngay"**.
- `BR-29.2 (Cảnh báo hết hạn)`: Khi thời gian dùng thử còn dưới 3 ngày, thanh thông báo chuyển sang màu vàng cam nổi bật.
- `BR-29.3 (Chính sách khi hết hạn 14 ngày dùng thử)`:
  - Nếu khách hàng không nâng cấp trả phí, workspace được tự động chuyển về gói **Miễn phí cơ bản (`FREE`)**.
  - Các tính năng nâng cao (Chatbot nâng cao, Báo cáo chuyên sâu, Dung lượng vượt quá 1GB) tạm thời bị khóa cho đến khi nâng cấp, nhưng toàn bộ dữ liệu lịch sử của khách hàng **luôn được bảo toàn nguyên vẹn**, không bị xóa.

---

### FEAT-30 — Chuỗi Email Nuôi dưỡng Tự động theo Hành vi (Onboarding Drip Campaigns) `[Yêu cầu mới]`

**Mô tả nghiệp vụ:** Hệ thống tự động gửi chuỗi email thông minh theo các mốc thời gian để hướng dẫn và kích thích người dùng hoàn tất các mốc kích hoạt sản phẩm.

**Actor:** Tiến trình Hệ thống.

**Lịch trình chuỗi email:**
- **Ngày 1 (Ngay sau đăng ký):** Email chào mừng, tóm tắt thông tin workspace và liên kết tới Bảng tiến độ 5 bước bắt đầu nhanh.
- **Ngày 3:** Email hướng dẫn tối ưu phễu bán hàng và nhập dữ liệu khách hàng.
- **Ngày 7:** Email giới thiệu tính năng kết nối đa kênh Omni-channel và tự động hóa Chatbot.
- **Ngày 12 (Còn 2 ngày hết hạn dùng thử):** Email thông báo sắp kết thúc thời gian dùng thử, tóm tắt các kết quả đạt được trong tuần qua và hướng dẫn nâng cấp gói dịch vụ.

---

## J. VẬN HÀNH, SỨC KHỎE CẤU HÌNH & CHUYỂN GIAO SỞ HỮU

### FEAT-31 — Giám sát sức khỏe cấu hình phòng chống màn hình trắng (`setup-health`) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Cung cấp công cụ chẩn đoán sức khỏe cấu hình định kỳ (`GET /api/v1/tenants/setup-health`) phát hiện 4 nhóm nguy cơ gây ẩn dữ liệu ngoài ý muốn.

**Actor:** Quản trị viên Workspace, Chủ sở hữu Workspace.

**Quy tắc nghiệp vụ:**
- `BR-31.1`: Phát hiện các lỗi: Thành viên thiếu phòng ban (Chặn), Thành viên thiếu vai trò (Chặn), Vai trò thiếu phạm vi dữ liệu (Cảnh báo), Chưa phân nhánh cây tổ chức (Cảnh báo).
- `BR-31.2`: Trả về đường dẫn khắc phục trực tiếp trong trang Cài đặt để quản trị viên xử lý nhanh chóng.

---

### FEAT-32 — Chuyển nhượng quyền sở hữu không gian làm việc 2 bên (72h TTL) `[Đã triển khai]`

**Mô tả nghiệp vụ:** Quy trình chuyển giao vai trò Chủ sở hữu (Owner) giữa 2 thành viên trong workspace có thời hạn bảo vệ 72 giờ và ghi nhật ký kiểm toán bảo mật Fail-closed.

**Actor:** Chủ sở hữu Workspace, Thành viên được chỉ định.

**Quy tắc nghiệp vụ:**
- `BR-32.1`: Chủ sở hữu hiện tại gửi yêu cầu chuyển nhượng, chọn vai trò mới của mình (`ADMIN` hoặc `MEMBER`).
- `BR-32.2`: Yêu cầu có thời hạn **72 giờ** (`TRANSFER_TTL_MS = 72h`). Quyền sở hữu chỉ chính thức chuyển giao khi người nhận bấm Xác nhận.
- `BR-32.3`: Chủ sở hữu có thể chủ động hủy yêu cầu bất kỳ lúc nào khi còn ở trạng thái `pending`. Nếu quá 72h, hệ thống tự động đánh dấu hết hạn (`expired`).

---

## 4. Yêu cầu phi chức năng

### 4.1 Hiệu năng & Khả năng đáp ứng (Performance)
- **NFR-01 (Thời gian hoàn tất Onboarding Wizard):** Thời gian phản hồi qua từng bước biểu mẫu đăng ký < **400ms** (p95).
- **NFR-02 (Thời gian Khởi tạo Không gian làm việc):** Toàn bộ chuỗi 10 bước saga ngầm hoàn tất dưới **12 giây** trong điều kiện tải thông thường.
- **NFR-03 (Tối ưu hóa Tải lại trang - F5 Resilience):** Phục hồi trạng thái biểu mẫu từ Redis qua API `/onboarding/context` dưới **100ms**.

### 4.2 Độ tin cậy & Toàn vẹn Dữ liệu (Reliability & ACID)
- **NFR-04 (Tính lũy tiến Idempotency):** Mọi tác vụ trong chuỗi khởi tạo và seeding dữ liệu đảm bảo chạy lại nhiều lần an toàn mà không sinh bản ghi trùng lặp.
- **NFR-05 (Giao dịch nguyên tử ACID):** Các thao tác tạo Tenant, Membership và Phân quyền thực thi trong Database Transaction duy nhất.
- **NFR-06 (Hoàn tác sạch sẽ 100%):** Khi saga gặp lỗi vĩnh viễn, toàn bộ tài nguyên trên Keycloak, MongoDB, Bot Workspace và Khóa giữ chỗ Alias được thu hồi triệt để.

### 4.3 An toàn & Bảo mật (Security)
- **NFR-07 (Bảo vệ thông tin cá nhân PII & Mật khẩu):** Mật khẩu băm an toàn trên Keycloak, cookie phiên làm việc mang đủ cờ `HttpOnly`, `SameSite=Lax`, `Secure`.
- **NFR-08 (Mã định danh bảo mật):** Mã `provisioningId` sử dụng định dạng ULID ngẫu nhiên bảo mật cao, tự hủy sau 24 giờ.
- **NFR-09 (Bảo vệ API Nội bộ):** Toàn bộ API khởi tạo SLG và Webhook giao tiếp bot được bảo vệ bằng khóa mạng nội bộ bí mật (`X-Internal-Api-Key`, `x-crm-internal-secret`).

---

## 5. Ma trận quyền truy cập tính năng

| Mã FEAT | Tên tính năng nghiệp vụ | Khách vãng lai | Người dùng Onboard | Thành viên (Member) | Quản trị viên (Admin) | Chủ sở hữu (Owner) | Quản trị Nền tảng (Super Admin) |
| --- | --- | :---: | :---: | :---: | :---: | :---: | :---: |
| `FEAT-01` | Đăng ký tài khoản ban đầu | **Cho phép** | — | — | — | — | — |
| `FEAT-02` | Tự động kích hoạt 14-day Trial | *Hệ thống* | *Hệ thống* | — | — | — | *Hệ thống* |
| `FEAT-03` | Đăng ký nhanh Google/Microsoft | **Cho phép** | — | — | — | — | — |
| `FEAT-04` | Khai báo công ty & Quy mô | — | **Cho phép** | — | — | — | — |
| `FEAT-05` | Chọn Ngành nghề & Mục tiêu | — | **Cho phép** | — | — | — | — |
| `FEAT-06` | Thiết lập Bản địa hoá (i18n) | *Hệ thống* | **Cho phép** | — | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-07` | Cảnh báo Enterprise Sales Alert | *Hệ thống* | *Hệ thống* | — | — | — | *Hệ thống* |
| `FEAT-08` | Tự sinh Subdomain thông minh | **Cho phép** | **Cho phép** | — | — | — | **Cho phép** |
| `FEAT-09` | Chỉnh sửa Subdomain | — | **Cho phép** | — | — | — | **Cho phép** |
| `FEAT-10` | Khóa giữ chỗ Subdomain 30p | *Hệ thống* | *Hệ thống* | — | — | — | *Hệ thống* |
| `FEAT-11` | Cấu hình Custom Domain CNAME | — | — | — | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-12` | Màn hình chờ truyền cảm hứng | — | **Cho phép** | — | — | — | **Cho phép** |
| `FEAT-13` | Thực thi Saga 10 bước ngầm | *Hệ thống* | *Hệ thống* | — | — | — | *Hệ thống* |
| `FEAT-14` | Hoàn tác bù trừ Saga | *Hệ thống* | *Hệ thống* | — | — | — | *Hệ thống* |
| `FEAT-15` | Khởi tạo Bot Workspace | *Hệ thống* | *Hệ thống* | — | — | — | *Hệ thống* |
| `FEAT-16` | Tạo Pipeline theo Ngành nghề | *Hệ thống* | *Hệ thống* | — | — | — | *Hệ thống* |
| `FEAT-17` | Khởi tạo Roles & Org Tree | *Hệ thống* | *Hệ thống* | — | — | — | *Hệ thống* |
| `FEAT-18` | Nạp Dữ liệu Mẫu theo Ngành | *Hệ thống* | *Hệ thống* | — | — | — | *Hệ thống* |
| `FEAT-19` | Xóa sạch Dữ liệu Mẫu 1-Click | — | — | — | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-20` | Welcome Modal lần đầu đăng nhập | — | — | — | — | **Cho phép** | — |
| `FEAT-21` | In-App Onboarding Checklist | — | — | — | **Cho phép** | **Cho phép** | — |
| `FEAT-22` | Interactive Product Tour | — | — | **Cho phép** | **Cho phép** | **Cho phép** | — |
| `FEAT-23` | Email Mời thành viên có thương hiệu | — | — | Có quyền* | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-24` | Welcome Teammate Screen (FTUX) | — | — | **Cho phép** | **Cho phép** | — | — |
| `FEAT-25` | Gán vai trò tối thiểu & Phòng ban | *Hệ thống* | — | — | *Hệ thống* | *Hệ thống* | *Hệ thống* |
| `FEAT-26` | Cổng cấp phát SLG nội bộ | — | — | — | — | — | **Cho phép** |
| `FEAT-27` | Gửi thư mời kích hoạt SLG | — | — | — | — | — | **Cho phép** |
| `FEAT-28` | Cấp quyền tính năng mở rộng | — | — | — | — | — | **Cho phép** |
| `FEAT-29` | Thanh đếm ngược Trial & Nâng cấp | — | — | **Cho phép** | **Cho phép** | **Cho phép** | — |
| `FEAT-30` | Chuỗi Email Nuôi dưỡng Tự động | *Hệ thống* | — | — | — | — | *Hệ thống* |
| `FEAT-31` | Giám sát sức khỏe cấu hình | — | — | — | **Cho phép** | **Cho phép** | **Cho phép** |
| `FEAT-32` | Chuyển nhượng quyền sở hữu | — | — | — | — | **Cho phép** | **Cho phép** |

*\*Ghi chú: Thành viên cấp `MEMBER` chỉ có thể mời người khác nếu được gán vai trò có chứa quyền `users:create` và bị giới hạn bởi trần quyền hạn của bản thân.*

---

## 6. Kịch bản chấp nhận tổng hợp (UAT)

### Kịch bản 1: Đăng ký Tự phục vụ Ngành Bất động sản & Kích hoạt 14 Ngày Dùng thử
1. **Khách hàng thao tác:** Khách hàng tại Ả Rập Xê Út truy cập `/onboarding`, nhập Họ tên "Ahmed Al-Otaibi", Email `ahmed@riyadh-realty.sa`, Mật khẩu an toàn.
2. **Khai báo Doanh nghiệp:** Nhập tên công ty "Riyadh Realty", chọn quy mô `11-50`, hệ thống gợi ý subdomain `riyadh-realty`.
3. **Chọn Ngành nghề:** Chọn ngành "Bất động sản" (`real_estate`).
4. **Kỳ vọng Khởi tạo:** 
   - Hệ thống tự động kích hoạt **14 ngày dùng thử miễn phí gói Pro** (`trialEndsAt = now + 14 ngày`).
   - Tự động thiết lập Locale tiếng Ả Rập (`ar`), Tiền tệ `SAR`, Múi giờ `Asia/Riyadh`.
   - Sinh phễu bán hàng "Quy trình Môi giới & Bán Bất động sản" kèm các dự án và khách hàng mẫu bất động sản tại Riyadh.
   - Chuyển hướng người dùng sang `https://riyadh-realty.crmsaudi.dev/login`.
5. **Kỳ vọng Đăng nhập:** Đăng nhập thành công, thấy Welcome Modal, thanh trạng thái *"Dùng thử Pro: Còn 14 ngày"* và Bảng tiến độ 5 bước bắt đầu nhanh.

---

### Kịch bản 2: Sử dụng & Xóa sạch Dữ liệu Mẫu 1-Click (Sample Data Purge)
1. Chủ sở hữu workspace "Riyadh Realty" sau khi khám phá các tính năng của hệ thống qua dữ liệu mẫu, quyết định bắt đầu nhập danh bạ khách hàng thật.
2. Trên màn hình chính, Chủ sở hữu bấm vào nút **"Xóa dữ liệu mẫu"** trên thanh banner màu xanh.
3. Hộp thoại xác nhận hiển thị cảnh báo: *"Hành động này sẽ xóa vĩnh viễn toàn bộ các liên hệ, công ty và cơ hội mẫu ban đầu nhưng giữ nguyên quy trình phễu và cài đặt của bạn."*.
4. Chủ sở hữu bấm "Xác nhận xóa".
5. **Kỳ vọng:** Toàn bộ dữ liệu mẫu biến mất, danh sách trở về trạng thái sạch sẽ sẵn sàng nhập dữ liệu thật, banner biến mất.

---

### Kịch bản 3: Trải nghiệm Tiếp nhận Thành viên Mới (Teammate FTUX)
1. Chủ sở hữu gửi lời mời tham gia tới chuyên viên kinh doanh `khalid@riyadh-realty.sa` vào phòng Kinh doanh (`SALES`) với vai trò "Sales Representative".
2. Khalid nhận được email mang nhận diện thương hiệu của "Riyadh Realty".
3. Khalid bấm vào liên kết trong email, được dẫn tới trang Chào mừng Thành viên tại `https://riyadh-realty.crmsaudi.dev/welcome`.
4. Khalid tự đặt mật khẩu riêng và bấm "Bắt đầu làm việc".
5. **Kỳ vọng:** Khalid đăng nhập thành công, thấy ngay các dữ liệu cơ hội bán hàng thuộc phòng Kinh doanh, có tên người quản lý trực tiếp là Ahmed và có đầy đủ quyền thao tác theo đúng vai trò Sales Rep.

---

### Kịch bản 4: Cấu hình Tên miền Riêng Doanh nghiệp (Custom Domain CNAME)
1. Quản trị viên doanh nghiệp vào Cài đặt → Tên miền tổ chức → nhập tên miền riêng mong muốn: `crm.riyadhrealty.com`.
2. Hệ thống hiển thị hướng dẫn cấu hình bản ghi DNS: `CNAME crm.riyadhrealty.com -> custom.crmsaudi.dev`.
3. Quản trị viên cấu hình DNS tại nhà cung cấp tên miền của mình và bấm "Xác thực cấu hình".
4. **Kỳ vọng:** Hệ thống xác thực bản ghi DNS thành công, tự động cấp phát chứng chỉ bảo mật SSL/TLS. Kể từ thời điểm này, toàn bộ nhân viên có thể đăng nhập trực tiếp qua `https://crm.riyadhrealty.com`.

---

### Kịch bản 5: Đăng ký Doanh nghiệp Lớn & Cảnh báo Kinh doanh Tự động
1. Một khách hàng đăng ký với tên "Tập đoàn Đại Dương", quy mô nhân sự chọn `200+` và email `giamdoc@daiduonggroup.vn`.
2. **Kỳ vọng:**
   - Hệ thống tự động tạo workspace thành công kèm cây tổ chức đầy đủ (HQ, Sales, Support, Marketing, Operations).
   - Tiến trình hệ thống tự động gửi thông báo ưu tiên (Enterprise Sales Alert) vào kênh điều hành nội bộ của CRM Saudi để chuyên viên bán hàng cấp cao chủ động liên hệ tư vấn hợp đồng Enterprise.

---

### Kịch bản 6: Tự động Thu hồi Tài khoản Bỏ rơi sau 24 Giờ (Orphan Cleanup)
1. Một người dùng vãng lai tạo tài khoản ở Bước 1 nhưng đóng trình duyệt và không tiếp tục tạo workspace.
2. Sau 24 giờ, tiến trình `OrphanCleanupCron` kích hoạt chu kỳ quét định kỳ 6 tiếng/lần.
3. **Kỳ vọng:** Hệ thống tự động phát hiện tài khoản mồ côi, xóa tài khoản trên Keycloak và cơ sở dữ liệu MongoDB, giải phóng email để có thể sử dụng lại trong tương lai.

---

## 7. Giới hạn hiện tại & Vấn đề chính sách cần quyết định tiếp

Phần này tổng hợp các vấn đề chính sách nghiệp vụ kinh doanh cần Ban Giám đốc và Đội ngũ Chiến lược sản phẩm tiếp tục chuẩn hoá chi tiết:

1. **Chính sách Gia hạn Dùng thử (Trial Extension Policy):**
   - *Vấn đề:* Có cho phép đội ngũ Sales/Support cấp thêm 7 ngày dùng thử (Extension) cho các khách hàng doanh nghiệp tiềm năng chưa kịp đánh giá xong trong 14 ngày hay không?
   - *Đề xuất PM:* Cho phép Quản trị Nền tảng (Super Admin) qua cổng quản trị nội bộ gia hạn thêm tối đa 1 lần (thêm 7 hoặc 14 ngày).
2. **Chính sách Giữ Dữ liệu sau khi Hết hạn Dùng thử (Data Retention Grace Period):**
   - *Vấn đề:* Nếu khách hàng hết 14 ngày dùng thử mà không nâng cấp và tài khoản bị hạ về Free, dữ liệu vượt hạn mức (ví dụ lưu trữ > 1GB) sẽ được xử lý như thế nào?
   - *Đề xuất PM:* Cho phép thời gian ân hạn 30 ngày (Grace Period) để khách hàng tải dữ liệu về hoặc nâng cấp; sau 30 ngày hệ thống mới khóa thao tác tải tệp mới.
3. **Tích hợp Cổng Đăng ký Tên miền Tự động (Domain Registrar Integration):**
   - *Vấn đề:* Khách hàng chưa có tên miền riêng có nhu cầu mua trực tiếp tên miền ngay trong giao diện Onboarding không?
   - *Đề xuất PM:* Tạm thời hỗ trợ trỏ CNAME có sẵn trong giai đoạn hiện tại; tích hợp mua tên miền trực tiếp sẽ đưa vào lộ trình dài hạn.
