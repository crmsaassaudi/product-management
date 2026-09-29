---
status: accepted
---

# Mức truy cập theo từng thao tác trên từng loại dữ liệu

**Bối cảnh:** Khi xử lý #312, vai trò Marketing cần "xem toàn bộ khách hàng nhưng không sửa được ai" (`contacts-srs.md` Mục 5, CFG-05-02). Mô hình vai trò hiện tại không diễn đạt được điều này. Cách vá tạm thời là gắn cứng cho riêng vai trò mẫu Marketing, kèm một công tắc riêng trong CFG-05-02. Cách này vi phạm nguyên tắc: logic khác nhau theo doanh nghiệp phải là cấu hình, không được gắn cứng. Một doanh nghiệp tự tạo vai trò "Chăm sóc khách hàng" với cùng nhu cầu sẽ không có cách nào cấu hình.

## Vấn đề gốc

Hiện nay mỗi vai trò mang **hai thứ tách rời nhau**:

1. **Danh sách quyền dạng có/không**: được xem khách hàng, được sửa khách hàng, được xuất khách hàng…
2. **Một phạm vi dữ liệu duy nhất** (Chỉ của mình → Toàn workspace), áp chung cho **mọi thao tác trên mọi loại dữ liệu**.

Vì phạm vi chỉ có một, vai trò không nói được các câu mà doanh nghiệp thực sự cần:

| Nhu cầu thật | Mô hình hiện tại |
| --- | --- |
| Marketing **xem** toàn bộ khách hàng, **sửa** thì không | Không diễn đạt được |
| Nhân viên kinh doanh **xem** khách của cả đơn vị, chỉ **sửa** khách của mình | Không diễn đạt được |
| Quản lý **xem** cả nhánh, nhưng chỉ **xuất** dữ liệu của đơn vị mình | Không diễn đạt được |
| Hỗ trợ xem ticket toàn workspace, cơ hội chỉ của mình | Làm được qua cấu hình phạm vi theo loại dữ liệu (FEAT-34), nhưng cấu hình này áp cho **cả workspace**, không riêng vai trò nào |

## Các CRM lớn giải bài toán này thế nào

| Sản phẩm | Cách làm |
| --- | --- |
| **Microsoft Dynamics 365** | Mỗi vai trò là một **ma trận**: dòng là loại dữ liệu; cột là thao tác (Tạo, Đọc, Ghi, Xóa, Gán, Chia sẻ…). Mỗi ô là một **mức**: Không / Người dùng / Đơn vị / Đơn vị và đơn vị con / Toàn tổ chức. Người giữ nhiều vai trò nhận mức cao nhất ở từng ô. |
| **HubSpot** | Với mỗi loại dữ liệu, các quyền **Xem**, **Sửa**, **Xóa** được chọn mức riêng: Tất cả / Của nhóm / Của mình / Không. |
| **Salesforce** | Có **mức nền của tổ chức** cho từng loại dữ liệu (công khai đọc, công khai đọc-ghi, riêng tư). Quyền theo loại dữ liệu nằm trong hồ sơ quyền và bộ quyền bổ sung, có quyền riêng "Xem tất cả" và "Sửa tất cả". Trên đó là cây vai trò và quy tắc chia sẻ để nới thêm. |

Cả ba cùng đi đến một kết luận: **phạm vi không thuộc về vai trò nói chung, mà thuộc về từng cặp (thao tác, loại dữ liệu)**. Dynamics và HubSpot đặt mức ngay trong ô quyền. Salesforce đạt kết quả tương tự bằng mức nền theo loại dữ liệu, cộng các quyền riêng "Xem tất cả" và "Sửa tất cả".

## Quyết định (đề xuất)

### 1. Vai trò là ma trận "loại dữ liệu × thao tác", mỗi ô là một mức truy cập

- **Dòng:** các loại dữ liệu có bản ghi được phụ trách: Khách hàng (gồm khách hàng tiềm năng), Công ty, Cơ hội, Ticket, Công việc, Hội thoại, Chiến dịch…
- **Cột:** các thao tác trên bản ghi: Xem, Tạo, Sửa, Xóa, Xuất, Nhập, Gán người phụ trách, cùng các thao tác đặc thù của từng loại (Phát sóng chiến dịch, Chuyển giai đoạn…).
- **Ô:** một trong các mức, theo đúng tên gọi hiện có của SRS: **Không có** / **Chỉ của mình** / **Của mình + cấp dưới và đơn vị của mình** / **Cả nhánh đơn vị** / **Toàn Không gian làm việc**.
- Quyền **không gắn với bản ghi** (quản lý cấu hình hệ thống, xem nhật ký kiểm toán, quản lý vai trò…) giữ dạng **có/không** như hiện nay.

Ví dụ, vai trò mẫu Marketing sẽ có những ô sau:

| Loại dữ liệu | Xem | Tạo | Sửa | Xóa | Xuất | Phát sóng |
| --- | --- | --- | --- | --- | --- | --- |
| Khách hàng | Toàn workspace | Không | Không | Không | Không | — |
| Chiến dịch | Đơn vị của mình | Có | Đơn vị của mình | Đơn vị của mình | Không | Không *(cấp riêng)* |

"Marketing xem toàn bộ khách hàng, chỉ đọc" không còn là một ngoại lệ gắn cứng nữa, mà chỉ là **giá trị của hai ô**. Vai trò bất kỳ do doanh nghiệp tự tạo cũng đặt được đúng các ô đó.

### 2. Các ràng buộc hợp lệ khi cấu hình

- **R1. Không sửa được thứ mình không thấy:** mức của Sửa, Xóa, Xuất, Gán và các thao tác đặc thù **không được rộng hơn** mức Xem của cùng loại dữ liệu. Màn hình từ chối lưu và nêu rõ ô vi phạm.
- **R2. Không tạo vai trò mạnh hơn người tạo** (FEAT-25, FEAT-26): áp theo **từng ô**. Người tạo hoặc sửa vai trò chỉ đặt được mức bằng hoặc hẹp hơn mức hiệu lực của chính họ ở ô đó. Thu hẹp luôn được phép.
- **R3. Sàn bắt buộc giữ nguyên:** các ô mang nhãn sàn trong SRS (cấm Marketing đọc Định danh KYC, quyền đọc nhật ký kiểm toán theo NFR-14…) không cấu hình vượt được, dù qua vai trò tự tạo hay qua CFG-05-02.

### 3. Hợp nhất khi một người giữ nhiều vai trò

- **Từng ô lấy mức rộng nhất** trong các vai trò người đó giữ (giữ đúng BR-35.1). Việc hợp nhất làm theo từng ô: một vai trò có Sửa rộng không làm Xem của vai trò khác rộng theo, và ngược lại.
- Hệ quả quan trọng: người giữ cả Marketing (Xem khách hàng: Toàn workspace; Sửa: Không) và Nhân viên Kinh doanh (Xem và Sửa khách hàng: Đơn vị) có **Xem toàn workspace, Sửa trong đơn vị**. Họ không bao giờ sửa được khách hàng của đơn vị khác.

### 4. Vị trí trong thứ tự ưu tiên đã chốt (ADR-0007)

ADR-0007 điều khoản 2 định nghĩa trục (1) là **năng lực theo vai trò** và trục (2) là **phạm vi dữ liệu**. ADR này **gộp hai trục đó thành một, tính theo từng thao tác**: "vai trò cho phép thao tác X trên loại Y ở mức Z". Các trục còn lại giữ nguyên:

- **Phạm vi theo loại dữ liệu của workspace (FEAT-34)** là **mức nền** (giống mức nền tổ chức của Salesforce):
  - Chế độ "công khai đọc" nâng mức Xem của mọi vai trò lên Toàn workspace cho loại dữ liệu đó. Nó không nâng các thao tác khác.
  - Mức nền chỉ dùng khi vai trò không khai báo ô đó. Nếu vai trò đã khai báo rõ, giữ đúng BR-34.1: mức nền không thu hẹp mà cũng không âm thầm mở rộng.
- **Người phụ trách đơn vị** (BR-34.3), **quy tắc chia sẻ**, **quyền đặc cách trên bản ghi** (FEAT-39, gồm lượt cấp do phân hệ sinh ra theo BR-39.5) và **chính sách truy cập nâng cao** (FEAT-36) giữ nguyên vai trò của chúng: nới thêm hoặc chặn thêm trên nền đó.
- **Phân quyền trường** (FEAT-40) giữ nguyên là trục cuối.

### 5. Thực thi: mỗi yêu cầu dùng mức của đúng thao tác nó khai báo

- Mỗi thao tác của hệ thống đã khai báo sẵn nó cần quyền gì, ví dụ "Sửa trên Khách hàng". Khi xử lý một yêu cầu, hệ thống lấy **mức của đúng ô đó** làm phạm vi cho mọi truy vấn trên loại dữ liệu này trong yêu cầu. Thao tác Sửa tìm và cập nhật bản ghi theo mức Sửa; danh sách và trang chi tiết dùng mức Xem; xuất dữ liệu dùng mức Xuất.
- Cách làm này thay cho cách phân biệt theo kiểu yêu cầu HTTP (đọc hay ghi) mà #312 đang dùng tạm. Nó chính xác hơn, và xử lý được cả những thao tác đọc gửi dưới dạng POST (tìm kiếm, xem trước đối tượng chiến dịch).
- **Tiến trình nền** (gửi chiến dịch, tự động hóa) ghi lại thao tác và mức tại thời điểm được khởi chạy, và chỉ chạy trong đúng mức đó.
- **Bản ghi thuộc loại dữ liệu khác trong cùng yêu cầu** (ví dụ mở một cơ hội và xem khách hàng liên kết) dùng mức Xem của loại dữ liệu kia.
- **Nhật ký "đọc ngoài phạm vi"** (`contacts-srs.md` NFR-07 mục 13) được định nghĩa chung, không gắn tên Marketing: một lượt Xem được phép **chỉ nhờ** mức Xem rộng hơn mức Sửa của chính người đó trên loại dữ liệu ấy. Vai trò nào có "xem rộng, sửa hẹp" cũng được ghi nhật ký như nhau.

### 6. Vai trò mẫu và CFG-05-02

- Vai trò mẫu của hệ thống (FEAT-29) là **ma trận mặc định**. Chúng vẫn khóa, không sửa trực tiếp; muốn khác thì **nhân bản rồi sửa** (FEAT-25, FEAT-26). Bản nhân bản mang theo toàn bộ ma trận.
- **CFG-05-02** trở thành "điều chỉnh từng ô trên ma trận của vai trò mẫu", dùng **đúng mô hình ô và mức** như trên và vẫn giới hạn bởi sàn R3. Công tắc riêng "Marketing xem toàn bộ khách hàng" bị bỏ: doanh nghiệp chỉ cần đặt ô "Khách hàng × Xem" của Marketing về "Đơn vị của mình".
- Khi đồng bộ vai trò mẫu (BR-29.1), hệ thống tự thêm các ô mới do bản phát hành bổ sung, và **giữ nguyên các ô doanh nghiệp đã điều chỉnh**. Việc này đã được bảo đảm trong #312.

### 7. Chuyển đổi dữ liệu hiện có (không đổi hành vi của ai)

- Mỗi quyền có/không hiện có trên một loại dữ liệu được đổi thành một ô, với mức bằng **phạm vi dữ liệu hiện tại của vai trò**. Quyền không có thành "Không có". Sau khi chuyển, mọi người dùng thấy và làm được **đúng như trước**.
- Phần "phạm vi đọc riêng" của vai trò Marketing (#312) đổi thành ô "Khách hàng × Xem = Toàn workspace".
- Công tắc `marketingDataScope` trong CFG-05-02 đổi thành điều chỉnh ô tương ứng.

## Hệ quả

**Được:**
- Mọi nhu cầu "thấy rộng, sửa hẹp" và "xuất hẹp hơn xem" đều cấu hình được cho **mọi vai trò của mọi doanh nghiệp**, không cần gắn cứng thêm ngoại lệ nào.
- Một cơ chế thay cho ba: phạm vi chung của vai trò, phạm vi đọc riêng của #312, và công tắc Marketing.
- Tăng mức ở một ô không tự làm tăng mức ở ô khác, nên không có đường mở rộng quyền ngầm.

**Phải trả:**
- Màn hình vai trò chuyển thành dạng lưới. Cần thiết kế lưới dễ dùng: gán nhanh một mức cho cả dòng, tô màu theo mức, và giữ màn hình xem quyền hiệu lực (FEAT-15).
- Bộ nhớ đệm quyền và phạm vi phải lưu theo ô thay vì theo vai trò.
- Mọi route cần khai báo đúng thao tác. Phần lớn đã có; những route chưa khai báo phải bổ sung, và mặc định là mức **hẹp nhất** chứ không phải rộng nhất.

## Việc cần làm tiếp nếu ADR được chấp nhận

1. Cập nhật `iam-tenant-authorization.md` theo đúng chuẩn SRS: FEAT-24, FEAT-25, FEAT-26, FEAT-29, FEAT-34, FEAT-35, Mục 5, AC tương ứng. Cập nhật `contacts-srs.md` CFG-05-02, Phụ lục C.1 và NFR-07 mục 13.
2. Tạo issue ở `product-management` cho phần triển khai (crm-api, crm-web), chia bước:
   - (a) mô hình ô, hợp nhất theo ô và chuyển đổi dữ liệu;
   - (b) thực thi theo thao tác của yêu cầu và tiến trình nền;
   - (c) màn hình ma trận vai trò;
   - (d) chuyển CFG-05-02 sang điều chỉnh theo ô.
3. Thay phần tạm của #312 (phạm vi đọc riêng, phân biệt theo kiểu yêu cầu HTTP, công tắc Marketing) bằng cơ chế mới ở bước (a), (b) và (d).
