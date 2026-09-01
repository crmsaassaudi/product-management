# THIẾT KẾ KIẾN TRÚC: HỆ THỐNG BILLING & SUBSCRIPTION

| | |
| --- | --- |
| **Loại tài liệu** | Thiết kế kiến trúc — sơ đồ luồng và ranh giới khối. **Không đặc tả cấu trúc dữ liệu chi tiết, không đặc tả mã nguồn.** |
| **Trạng thái** | Proposed / Ready for Architecture Review |
| **Ngày viết** | 2026-08-25, cập nhật 2026-08-29 (§2.1: bỏ bản chụp/danh mục gói phía CRM, xem lý do trong mục đó) |
| **Căn cứ nghiệp vụ** | [`billing-subscription-srs.md`](../srs/billing-subscription-srs.md) — **phạm vi đã đóng từ 2026-08-25** |
| **Quyết định kiến trúc** | [`ADR-0004`](../docs/adr/0004-billing-engine-boundary.md) — trạng thái `accepted-with-open-commercial-decision` |
| **Phạm vi triển khai** | `crm-api` · `crm-manager-api` · `crm-web` · `crm-manager-web` · `crm-lago` (vận hành hệ tính phí) |

## Tài liệu này nói gì và không nói gì

**Nói:** ranh giới khối, hướng phụ thuộc, luồng dữ liệu, thực thể nào do ai sở hữu, cơ chế nào đỡ yêu cầu phi chức năng nào, và những quyết định kiến trúc chưa chốt.

**Không nói:** trường dữ liệu, kiểu dữ liệu, tên lớp, chữ ký hàm, chỉ mục cơ sở dữ liệu, mã nguồn. Những thứ đó được quyết định **tại thời điểm lên plan cho từng hạng mục**, dựa trên quy tắc nghiệp vụ tương ứng trong SRS và trên ràng buộc kiến trúc trong tài liệu này. Đóng băng chúng ở đây chỉ có tác dụng khóa sớm những lựa chọn mà lúc đó mới có đủ thông tin để quyết.

**Cách dùng khi bắt đầu một hạng mục:** đọc Mục 2.5 của SRS để biết hạng mục phụ thuộc cái gì → kiểm tra Mục 8 của tài liệu này xem có quyết định treo nào chặn không → đọc các BR liên quan → lên plan chi tiết.

---

## 1. RANH GIỚI & NGUYÊN TẮC

### 1.1 Năm nguyên tắc nền

1. **CRM là nguồn sự thật về việc đã xảy ra.** CRM ghi nhận việc gì đã xảy ra, cho doanh nghiệp nào, vào thời điểm nào. Không module nghiệp vụ nào chứa kiến thức về giá, hạn mức, thuế, chu kỳ hay hóa đơn.
2. **Hệ tính phí sở hữu logic thương mại.** Danh mục gói, phiên bản gói, tổng hợp tiêu dùng, phần lẻ giữa kỳ, hạn mức bao gồm, phí vượt, phát hành hóa đơn bất biến.
3. **Cổng thanh toán sở hữu giao dịch tiền tệ.** Phương thức thanh toán, thu tiền, hoàn tiền, lưu giữ thông tin thẻ. CRM tuyệt đối không lưu dữ liệu thẻ (NFR-2).
4. **Lớp sự kiện tính phí là ranh giới duy nhất.** Giao tiếp giữa CRM và hệ tính phí luôn bất đồng bộ, qua Outbox và chuyển giao có tính bất biến khi lặp lại.
5. **Thực thi cục bộ, không phụ thuộc mạng ra ngoài tại runtime.** Mọi quyết định chặn hay cho phép tại runtime đọc từ trạng thái cục bộ của CRM (BR-04.5, BR-32.4).

### 1.2 Ba đường giao tiếp, ba tính chất khác nhau

| Đường | Hướng | Tính chất | Hỏng thì sao |
| --- | --- | --- | --- |
| **Chuyển giao sự kiện** | CRM → Hệ tính phí | Bất đồng bộ, chỉ thêm mới, lặp lại không sinh phí trùng | Sự kiện nằm chờ trong CRM, nghiệp vụ không bị ảnh hưởng (BR-32.1) |
| **Đồng bộ trạng thái thương mại** | Hệ tính phí / Cổng thanh toán → CRM | Bất đồng bộ, có bù bằng quét chủ động định kỳ | CRM dùng trạng thái gần nhất và tự bù khi khôi phục (ADR-0004 §5) |
| **Thao tác thương mại theo yêu cầu** | CRM → Hệ tính phí | Đồng bộ, do người dùng khởi xướng, chấp nhận thất bại và báo lỗi | Người dùng thấy lỗi rõ ràng; **không** thao tác nghiệp vụ nào bị ảnh hưởng |

Đường thứ ba là đường duy nhất được phép gọi đồng bộ ra ngoài, và nó **chỉ xuất hiện trong các thao tác thương mại có chủ đích** — xem gói, xem trước phần lẻ khi đổi gói, mở cổng quản lý thẻ. Nó KHÔNG ĐƯỢC xuất hiện trên đường phục vụ nghiệp vụ hằng ngày và KHÔNG ĐƯỢC xuất hiện trên đường đọc màn hình mà doanh nghiệp mở thường xuyên.

---

## 2. NGUỒN SỰ THẬT & SỞ HỮU DỮ LIỆU

| Thực thể | Sở hữu | Vai trò bên còn lại | Cơ chế đồng bộ |
| --- | --- | --- | --- |
| Sự kiện tính phí | **CRM** | Hệ tính phí nhận để tổng hợp | Outbox đẩy + mã tham chiếu chống trùng |
| Danh mục loại tiêu dùng | **CRM (quản trị nhà cung cấp)** | Hệ tính phí nhận bản đồng bộ | Đẩy khi phát hành, một chiều |
| Danh mục gói, phiên bản gói & add-on | **Hệ tính phí (Lago)** | CRM không giữ bản sao nào, kể cả bất biến | CRM đọc trực tiếp qua Khối 9 mỗi khi cần hiển thị/xác nhận (Đường thứ ba, §1.2) |
| Vòng đời đăng ký — tính toán thương mại | **Hệ tính phí** | — | — |
| Vòng đời đăng ký — trạng thái thực thi runtime | **CRM** | — | Webhook + quét chủ động định kỳ |
| Hóa đơn & chứng từ ghi có | **Hệ tính phí** | CRM giữ bản chiếu chỉ đọc | Webhook theo từng chuyển trạng thái |
| Phương thức thanh toán & giao dịch | **Cổng thanh toán** | CRM chỉ giữ trạng thái và mã tham chiếu | Webhook |
| Nhật ký kiểm toán thương mại | **CRM** | — | — |
| Khiếu nại hóa đơn | **CRM** | Kết luận giảm trừ đẩy sang hệ tính phí thành chứng từ ghi có | CRM đẩy khi có kết luận |

### 2.1 Một chỗ cố ý lệch khỏi mô hình "một chủ sở hữu duy nhất" — và một chỗ đã từng cân nhắc rồi bỏ

**Đã cân nhắc và BỎ (quyết định 2026-08-29): không để CRM giữ bất kỳ bản sao nào của danh mục gói.** Bản nháp đầu (2026-08-25) từng đề xuất CRM tự khai báo gói và giữ bản chụp bất biến điều kiện đã ký, với lý do BR-01.5 (doanh nghiệp xem lại được nguyên văn điều kiện đã ký) và NFR-8 (mọi số trên hóa đơn tái lập được) — sợ nếu điều kiện gói chỉ sống trong Lago thì đổi nhà cung cấp sau này sẽ mất khả năng tái lập hóa đơn cũ. Đề xuất đó bị bác: giữ một bản sao ở CRM — dù chỉ đọc — vẫn là một module billing thứ hai, đúng kiểu phân mảnh mà ADR-0004 muốn tránh. Quyết định cuối: **danh mục gói, phiên bản gói và add-on sống DUY NHẤT trong Lago.** CRM (`crm-manager-api`, `crm-manager-web`) không lưu, không cache — mỗi lần cần hiển thị hay xác nhận điều kiện gói (màn hình quản trị gói, xem lại hợp đồng đã ký) đều gọi thẳng Lago qua Khối 9, theo đúng "Đường thứ ba" ở §1.2 (đồng bộ, do người dùng khởi xướng, không nằm trên đường phục vụ nghiệp vụ hằng ngày). Rủi ro mất khả năng tái lập hóa đơn cũ nếu đổi nhà cung cấp khỏi Lago được **chấp nhận có chủ đích** — xem lại ở §8.2 — và giảm nhẹ một phần nhờ chính Lago tự giữ lịch sử phiên bản gói bất biến (BR-01.2) trong khi còn dùng Lago.

**Còn giữ lại: trạng thái đăng ký có hai chủ sở hữu, phân theo trách nhiệm.** Hệ tính phí sở hữu phần tính toán và lịch chuyển trạng thái theo hợp đồng thương mại; CRM sở hữu phần trạng thái dùng để ra quyết định chặn hay cho phép tại runtime. Đây KHÔNG phải một bản sao dữ liệu thương mại (không phải giá, không phải điều kiện gói) — chỉ là một ảnh chụp rất mỏng (ví dụ: đang đình chỉ hay không, còn hạn mức bao nhiêu) để trả lời "ngay bây giờ có cho họ gửi tin không" mà không được phép chờ mạng (BR-04.5, BR-32.4). Hai câu hỏi khác nhau: "theo hợp đồng thì họ đang ở đâu" (hỏi Lago) và "ngay bây giờ có chặn không" (đọc cục bộ) — giữ cả hai không phải phân mảnh, mà là SRS bắt buộc (BR-04.5 cấm phụ thuộc mạng tại thời điểm quyết định).

---

## 3. BẢN ĐỒ KHỐI MODULE

```text
╔══════════════════════════════════════════════════════════════════════════════════╗
║                             CRM — RANH GIỚI NGHIỆP VỤ                            ║
║                                                                                  ║
║  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  ║
║  │  Omnichat    │  │  Chiến dịch  │  │  Tự động hóa │  │  Quản trị người dùng │  ║
║  │              │  │  & Bot       │  │  & Quy trình │  │  (IAM)               │  ║
║  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └──────────┬───────────┘  ║
║         │                 │                 │                     │              ║
║      hai chiều         hai chiều         hai chiều              một chiều        ║
║   hỏi ↕ tuyên bố     hỏi ↕ tuyên bố    hỏi ↕ tuyên bố          tuyên bố          ║
║         │                 │                 │                     │              ║
╟─────────┼─────────────────┼─────────────────┼─────────────────────┼──────────────╢
║         ▼                 ▼                 ▼                     ▼              ║
║  ┌────────────────────────────────┐   ┌──────────────────────────────────────┐   ║
║  │   KHỐI 1  Cổng thực thi        │   │   KHỐI 2  Cổng ghi nhận              │   ║
║  │           chính sách           │   │                                      │   ║
║  │  • trả lời "cho phép / chặn /  │   │  • nhận tuyên bố "việc này đã xảy ra"│   ║
║  │    cần xác nhận"               │   │  • ghi bền vững, chỉ thêm mới        │   ║
║  │  • đọc cục bộ, không ra mạng   │   │  • không bao giờ ném lỗi ngược lên   │   ║
║  │  • phân tầng theo rủi ro       │   │    module nghiệp vụ                  │   ║
║  └───────────────▲────────────────┘   └────────────────┬─────────────────────┘   ║
║                  │ đọc                                 │ ghi                     ║
║  ┌───────────────┴─────────────────────────────────────▼─────────────────────┐   ║
║  │   KHỐI 3  Trạng thái thương mại cục bộ                                    │   ║
║  │   • ảnh chụp đăng ký & hạn mức (mỏng, chỉ để chặn/cho phép runtime)       │   ║
║  │   • kho sự kiện tính phí (chỉ thêm mới)  • bản chiếu hóa đơn (chỉ đọc)    │   ║
║  │   (KHÔNG có danh mục gói/add-on — sống duy nhất ở Lago, xem §2.1)         │   ║
║  └───────────────▲──────────────────────────────────▲───────────────────────┘    ║
║                  │                                  │                            ║
║  ┌───────────────┴──────────┐  ┌────────────────────┴────────────────────────┐   ║
║  │  KHỐI 4  Đối soát        │  │  KHỐI 5  Chuyển giao & Đồng bộ              │   ║
║  │  • so khớp CRM ↔ engine  │  │  • đẩy sự kiện theo lô, có thứ tự thử lại   │   ║
║  │  • cổng chặn phát hành   │  │  • nhận webhook, chống xử lý lặp            │   ║
║  │  • lưu vết mọi lần soát  │  │  • quét chủ động định kỳ để bù webhook mất  │   ║
║  └──────────────────────────┘  └────────────────────┬────────────────────────┘   ║
║                                                     │                            ║
║  ┌──────────────────────────┐  ┌───────────────────┐│  ┌──────────────────────┐  ║
║  │  KHỐI 6  Nhắc nợ &       │  │ KHỐI 7  Nhật ký   ││  │ KHỐI 8  Cổng tự      │  ║
║  │          Đình chỉ        │  │  kiểm toán        ││  │  phục vụ & Quản trị  │  ║
║  │  • máy trạng thái        │  │  • nguyên tắc đóng ││  │  • màn hình tenant   │  ║
║  │  • tạm dừng khi khiếu nại│  │  • fail-closed     ││  │  • màn hình nhà c/c  │  ║
║  └──────────────────────────┘  └───────────────────┘│  └──────────────────────┘  ║
╚═════════════════════════════════════════════════════┼════════════════════════════╝
                                                      │  Bộ chuyển đổi
                              ┌───────────────────────┴──────────────────────┐
                              │  KHỐI 9  Bộ chuyển đổi nhà cung cấp          │
                              │  Thành phần DUY NHẤT biết tên hệ tính phí    │
                              │  và cổng thanh toán cụ thể                   │
                              └───────┬──────────────────────────┬───────────┘
                                      │                          │
                          ┌───────────▼──────────┐   ┌───────────▼──────────┐
                          │   HỆ TÍNH PHÍ        │   │  CỔNG THANH TOÁN     │
                          │   (giai đoạn đầu:    │   │  (giai đoạn đầu:     │
                          │    Lago, tự vận hành)│   │   chưa chốt — Mục 8) │
                          │  • tổng hợp tiêu dùng│   │  • lưu giữ thẻ       │
                          │  • tính phần lẻ kỳ   │   │  • thu tiền          │
                          │  • phát hành hóa đơn │   │  • hoàn tiền         │
                          └──────────────────────┘   └──────────────────────┘
```

### 3.1 Trách nhiệm từng khối

| Khối | Trách nhiệm | Ràng buộc bắt buộc |
| --- | --- | --- |
| **1 — Cổng thực thi chính sách** | Trả lời "cho phép / chặn / cần xác nhận" cho một thao tác sắp phát sinh tiêu dùng | Đọc hoàn toàn cục bộ. Không bao giờ gọi ra ngoài. Kết quả giống nhau dù thao tác đến từ giao diện, tích hợp hay tác vụ tự động (BR-16.7) |
| **2 — Cổng ghi nhận** | Nhận tuyên bố "một việc đáng tính phí đã xảy ra", ghi bền vững | Không bao giờ ném lỗi ngược lên module nghiệp vụ (BR-09.3). Ghi không được thì phải thành "ghi bù sau kèm cảnh báo", không được thành im lặng (BR-09.4) |
| **3 — Trạng thái thương mại cục bộ** | Giữ mọi thứ mà khối 1 cần đọc và khối 4 cần đối chiếu | Kho sự kiện chỉ thêm mới. Bản chiếu hóa đơn chỉ đọc. Bản chụp điều kiện gói bất biến |
| **4 — Đối soát** | So khớp CRM với hệ tính phí, chặn phát hành khi lệch vượt ngưỡng | Là cổng chặn bắt buộc trước mỗi lần chốt kỳ, không phải một báo cáo tham khảo (BR-15.3, BR-18.5) |
| **5 — Chuyển giao & Đồng bộ** | Đẩy sự kiện ra, nhận trạng thái về, bù phần thất lạc | Chuyển giao lại không được sinh phí trùng. Webhook phải chống xử lý lặp. Quét chủ động phải bù được webhook mất |
| **6 — Nhắc nợ & Đình chỉ** | Máy trạng thái theo ngày tới hạn | Chỉ khởi phát sau ngày tới hạn (BR-21.9). Tạm dừng khi có khiếu nại mở (BR-27.3). Khôi phục tự động 24/7 (BR-22.4) |
| **7 — Nhật ký kiểm toán** | Ghi vết mọi quyết định thương mại | Fail-closed: không ghi được vết thì không thực hiện quyết định (BR-29.2). **Nhưng không phủ lên dòng sự kiện tính phí** (BR-29.1b) |
| **8 — Cổng tự phục vụ & Quản trị** | Màn hình cho doanh nghiệp và cho nhà cung cấp | Phân quyền theo Mục 5 của SRS. Quyền tài chính và quyền nghiệp vụ là hai trục độc lập (NFR-3) |
| **9 — Bộ chuyển đổi** | Thành phần duy nhất biết tên nhà cung cấp cụ thể | Không khối nào khác, không màn hình nào, được gọi thẳng ra ngoài |

### 3.2 Hai quy tắc phụ thuộc không được vi phạm

**Module nghiệp vụ chỉ phụ thuộc khối 1 và khối 2.** Chúng không biết khối 3 → 9 tồn tại. Đây là điều làm cho việc đổi mô hình giá không phải sửa Omnichat (BR-02.4, NFR-12).

**Chỉ khối 9 biết tên nhà cung cấp.** Không controller, không màn hình, không tác vụ nền nào khác được gọi thẳng ra ngoài. Vi phạm điều này là mất luôn khả năng hoán đổi mà ADR-0004 đặt ra.

### 3.3 Khối 9 trong thực tế triển khai — app nào gọi, gọi gì, ai nhận webhook

Khối 9 là một **khái niệm kiến trúc**, không phải một dịch vụ chạy riêng. `crm-api` và `crm-manager-api` là hai ứng dụng triển khai độc lập (hai tiến trình, hai repo), nên Khối 9 tồn tại thành **hai cài đặt mỏng**, một trong mỗi app — không dùng chung tiến trình, nhưng cùng tuân một hợp đồng khái niệm (đổi Lago sang engine khác nghĩa là sửa đúng hai chỗ này, không sửa rải rác). Cả hai cài đặt đều **không lưu bất kỳ dữ liệu thương mại nào** — chỉ chuyển tiếp lời gọi.

**Ai gọi khối 9 của ai:**

| App | Vai trò | Gọi Lago để làm gì | Đồng bộ/Bất đồng bộ |
| --- | --- | --- | --- |
| `crm-api` | Runtime tenant-facing | Đẩy sự kiện tiêu dùng (Luồng A, đã có: `LagoMeteringAdapter`) · tự phục vụ: xem gói hiện tại, đăng ký, đổi gói, xem trước phần lẻ, xem hóa đơn, mở cổng thẻ (Luồng B/C nhánh tenant) · nhận webhook · quét chủ động định kỳ để bù Khối 3 | Đẩy sự kiện: bất đồng bộ. Tự phục vụ: đồng bộ, do tenant khởi xướng (Đường thứ ba, §1.2) |
| `crm-manager-api` | Quản trị nhà cung cấp | Tạo/sửa/phát hành gói, add-on, ưu đãi · tra cứu subscription/hóa đơn của mọi tenant cho màn hình vận hành · đối soát (Khối 4) · phát hành chứng từ ghi có theo kết luận khiếu nại | Toàn bộ đồng bộ, do nhân sự nhà cung cấp khởi xướng — không có webhook riêng (xem lý do bên dưới) |
| `crm-web` | Giao diện tenant | **Không bao giờ gọi Lago.** Luôn gọi API của `crm-api`, thứ tự: `crm-web → crm-api → Khối 9 (crm-api) → Lago` | — |
| `crm-manager-web` | Giao diện quản trị | **Không bao giờ gọi Lago.** Luôn gọi API của `crm-manager-api`, thứ tự: `crm-manager-web → crm-manager-api → Khối 9 (crm-manager-api) → Lago` | — |

**Webhook chỉ có MỘT nơi nhận: `crm-api`.** Lý do: Khối 3 (ảnh chụp runtime — đình chỉ, hạn mức) sống ở `crm-api` và là thứ cần cập nhật nhanh nhất khi Lago báo thay đổi. Nếu `crm-manager-api` cũng tự đăng ký nhận cùng một webhook thì có hai nơi xử lý cùng một sự kiện — hai cơ chế chống trùng, hai chỗ để lệch pha nhau (đúng vấn đề "một lớp không đủ" ở §6.1, nhân đôi thay vì giải quyết). `crm-manager-api` không cần webhook: màn hình quản trị không nhạy độ trễ như đường phục vụ khách hàng, nên đọc trực tiếp từ Lago theo yêu cầu (đồng bộ) hoặc qua vòng đối soát định kỳ của chính nó (Khối 4) — không qua `crm-api`.

**Giao diện khái niệm của mỗi cài đặt Khối 9** (tinh chỉnh từ phác thảo `BillingProviderAdapter` ở ADR-0004 §4, phản ánh mô hình tập trung hoàn toàn vào Lago đã chốt ở §2.1 — đây vẫn là danh sách trách nhiệm, không phải chữ ký hàm cuối cùng):

*Phía `crm-api` (runtime + tự phục vụ tenant):*

- Đẩy một sự kiện tiêu dùng (đã có).
- Lấy gói & điều kiện đang áp dụng của một tenant (đọc trực tiếp, không cache — thay cho "bản chụp điều kiện gói" đã bỏ ở §2.1).
- Đăng ký gói mới / đổi gói / xem trước phần lẻ giữa kỳ cho một tenant.
- Liệt kê hóa đơn, lấy hóa đơn sắp tới của một tenant.
- Mở phiên quản lý phương thức thanh toán (ủy quyền sang cổng thanh toán, không đi qua Lago).
- Nhận một webhook đã xác thực chữ ký, trả về ngay sau khi ghi nhận đã nhận (chống xử lý lặp), xử lý cập nhật Khối 3 ở tiến trình nền.
- Kéo một ảnh chụp trạng thái subscription/hạn mức của một tenant (dùng cho quét chủ động định kỳ, Luồng F).

*Phía `crm-manager-api` (quản trị nhà cung cấp):*

- Tạo / sửa (khi còn nháp) / phát hành / thu hồi một phiên bản gói.
- Tạo add-on, ưu đãi.
- Tra cứu subscription/hóa đơn theo tenant hoặc theo lô (cho màn hình vận hành, báo cáo).
- Phát hành chứng từ ghi có tham chiếu một hóa đơn, sau khi có kết luận khiếu nại hoặc miễn trừ thương mại.
- Kéo dữ liệu tiêu dùng/hóa đơn của Lago theo lô, phục vụ Khối 4 (đối soát định kỳ).

Không cài đặt nào có phương thức tính chu kỳ, tính phần lẻ hay tính tiền — những phép tính đó xảy ra bên trong Lago, hai cài đặt trên chỉ hỏi kết quả.

---

## 4. SƠ ĐỒ LUỒNG

### 4.1 Luồng A — Ghi nhận và chuyển giao sự kiện tiêu dùng

Luồng đi qua nhiều nhất và là luồng không được phép làm chậm nghiệp vụ.

```text
  Module nghiệp vụ                CRM                          Hệ tính phí
  ───────────────                 ───                          ───────────
        │
   [Thời điểm tính phí đạt tới]
   (BR-10.2 · BR-10.3 · BR-11.8)
        │
        │  tuyên bố: doanh nghiệp, loại,
        │  mã tham chiếu, số lượng,
        │  thời điểm phát sinh
        ├──────────────────────────►┌─────────────────┐
        │                           │ KHỐI 2 Ghi nhận │
        │                           └────────┬────────┘
        │                                    │ ghi bền vững, chỉ thêm mới
        │                                    ▼
        │                           ┌──────────────────┐
        │◄──────────────────────────┤  chờ chuyển giao │
        │  trả về ngay, luôn thành  └────────┬─────────┘
        │  công dưới góc nhìn của            │
        │  module nghiệp vụ                  │  BẤT ĐỒNG BỘ — tách hoàn toàn
        │                                    │  khỏi luồng phục vụ người dùng
   [nghiệp vụ đi tiếp]                       ▼
                                    ┌──────────────────┐
                                    │ KHỐI 5 Chuyển    │
                                    │ giao theo lô     │
                                    └────────┬─────────┘
                                             │  mang theo mã tham chiếu
                                             ├───────────────────────►  nhận & tổng hợp
                                             │                              │
                                             │◄─────────────────────────────┘
                                             │  thành công → đánh dấu đã chuyển
                                             │
                        ┌────────────────────┴────────────────────┐
                        │                                         │
                  [thất bại tạm thời]                    [thất bại quá số lần]
                        │                                         │
                        ▼                                         ▼
                thử lại theo lịch giãn dần          ┌──────────────────────────┐
                sự kiện vẫn an toàn trong CRM       │ danh sách chờ xử lý tay  │
                (BR-12.2 · BR-32.1)                 │ + cảnh báo nêu rõ số     │
                                                    │ lượng và doanh nghiệp    │
                                                    │ bị ảnh hưởng (BR-12.3)   │
                                                    └──────────────────────────┘
```

**Ba tính chất kiến trúc của luồng này:**

- **Không có lời gọi đồng bộ ra ngoài ở bất kỳ đâu trên nhánh trái.** Đây là điều làm BR-09.3 và BR-32.1 đúng được.
- **Mã tham chiếu tới đối tượng nghiệp vụ gốc là cam kết, không phải chi tiết kỹ thuật.** Nó cho phép hệ thống dám thử lại khi không chắc chắn (BR-12.1, NFR-6). Chống trùng cần **hai lớp độc lập**: một ở CRM khi ghi, một ở hệ tính phí khi nhận — một lớp không đủ vì hai bên có thể mất đồng bộ.
- **Nhánh "ghi không được" phải tồn tại.** Sự cố ở khối 2 KHÔNG ĐƯỢC dẫn tới việc bỏ qua ghi nhận (BR-09.4, BR-32.2). Cơ chế cụ thể quyết định lúc lên plan; ràng buộc kiến trúc là nó phải bền vững hơn kho sự kiện chính và không nằm cùng điểm hỏng với kho đó.

### 4.2 Luồng B — Kiểm tra chính sách trước một thao tác

Luồng quyết định trải nghiệm ở đúng khoảnh khắc nhạy cảm nhất của quan hệ thương mại.

```text
                        [Một thao tác sắp phát sinh tiêu dùng]
                                        │
                        ┌───────────────┴────────────────┐
                        │  Thao tác thuộc chiều nào?     │
                        └───────────────┬────────────────┘
                                        │
              ┌─────────────────────────┴─────────────────────────┐
              │                                                   │
    KHÁCH HÀNG CỦA DOANH NGHIỆP                        DOANH NGHIỆP CHỦ ĐỘNG
        KHỞI XƯỚNG (inbound)                              (outbound)
              │                                                   │
              ▼                                                   ▼
   ┌──────────────────────┐                        ┌──────────────────────────┐
   │ KHÔNG có bước kiểm   │                        │ Đọc trạng thái đăng ký   │
   │ tra chặn nào         │                        │ và hạn mức — CỤC BỘ      │
   │ (BR-16.3)            │                        └────────────┬─────────────┘
   │                      │                                     │
   │ Luôn tiếp nhận       │                    ┌────────────────┴──────────────┐
   │ Luôn ghi nhận        │                    │                               │
   │ Luôn chuyển giao     │            [đọc được]                    [KHÔNG đọc được]
   │ (BR-16.10)           │                    │                               │
   └──────────┬───────────┘                    │                               ▼
              │                                │                  ┌────────────────────────┐
              │  Kiểm soát chi phí ở           │                  │ Phân tầng theo rủi ro  │
              │  khâu tính tiền, không         │                  │ chi phí (BR-32.3):     │
              │  ở khâu phục vụ                │                  │ • chi phí thấp → cho   │
              │                                │                  │   phép + cảnh báo      │
              ▼                                │                  │ • lô lớn chủ động →    │
   ┌──────────────────────┐                    │                  │   TẠM HOÃN, không      │
   │ Phần vượt trần trong │                    │                  │   chạy trong mù        │
   │ kỳ bị hệ tính phí    │                    │                  └────────────────────────┘
   │ loại khỏi hóa đơn.   │                    │
   │ Vẫn hiện đủ trên     │      ┌─────────────┴─────────────┐
   │ màn hình theo dõi.   │      │  Thao tác đơn hay theo lô?│
   │ Ghi thành chi phí    │      └─────────────┬─────────────┘
   │ nhà c/c tự chịu      │                    │
   │ (BR-16.8 · BR-30.3)  │        ┌───────────┴───────────┐
   └──────────────────────┘        │                       │
                                 ĐƠN                     THEO LÔ
                                   │                       │
                                   │                       ▼
                                   │        ┌──────────────────────────────┐
                                   │        │ Đối chiếu TOÀN BỘ khối lượng │
                                   │        │ dự kiến trước khi khởi động  │
                                   │        │ (BR-16.9)                    │
                                   │        │ Không bao giờ chạy nửa chừng │
                                   │        └──────────────┬───────────────┘
                                   │                       │
                                   └───────────┬───────────┘
                                               │
                    ┌──────────────────────────┼──────────────────────────┐
                    │                          │                          │
              [còn hạn mức]           [hết hạn mức bao gồm]        [chạm trần chi phí]
                    │                          │                          │
                    ▼                          ▼                          ▼
              cho phép,          áp chính sách của hạn mức       dừng, cảnh báo,
              không báo gì       (BR-16.2), trong khuôn khổ      chờ xác nhận nâng
                                 trần doanh nghiệp (BR-16.2b)    trần lên MỘT MỨC
                                             │                   MỚI CỤ THỂ (BR-16.5b)
                                             ▼                          │
                                  ┌──────────────────────┐              │
                                  │ Thông báo phân theo  │◄─────────────┘
                                  │ quyền người thao tác │
                                  │ • có quyền tài chính │
                                  │   → thấy số tiền     │
                                  │ • không có quyền     │
                                  │   → chỉ biết chạm    │
                                  │     hạn mức nào và   │
                                  │     ai xử lý được    │
                                  │   (BR-16.6 · BR-26.3)│
                                  └──────────────────────┘
```

**Ba tính chất kiến trúc:**

- **Nhánh inbound không có điểm quyết định nào.** Không phải "kiểm tra rồi cho qua" mà là **không có bước kiểm tra**. Một nhánh có điểm quyết định là một nhánh có thể hỏng thành chặn.
- **Cache trống không phải một trạng thái cho qua.** Nó là một trạng thái phải phân tầng. Nguồn dự phòng của trạng thái đình chỉ là ảnh chụp bền vững trong khối 3, không phải giá trị mặc định.
- **Số tiền là dữ liệu tài chính, phân quyền ngay ở tầng trả kết quả.** Không đẩy việc che số tiền lên tầng giao diện — một tích hợp bên ngoài sẽ bỏ qua tầng đó.

### 4.3 Luồng C — Chốt kỳ, đối soát và phát hành hóa đơn

Luồng ít chạy nhất nhưng sai một lần thì tốn kém nhất.

```text
   [Kỳ kết thúc theo múi giờ của hồ sơ thanh toán — BR-13.4]
                       │
                       ▼
   ┌───────────────────────────────────────┐
   │ CHỜ HẾT THỜI HẠN CHỐT KỲ              │   ← hứng sự kiện đến muộn,
   │ (BR-13.2)                             │     quy về kỳ theo THỜI ĐIỂM
   └───────────────────┬───────────────────┘     PHÁT SINH (BR-13.1)
                       ▼
   ┌───────────────────────────────────────┐
   │ KHỐI 4 — ĐỐI SOÁT BẮT BUỘC            │
   │ • nêu riêng hai chiều lệch (BR-15.2)  │
   │ • lưu kết quả kể cả khi khớp (BR-15.4)│
   └───────────────────┬───────────────────┘
                       │
        ┌──────────────┴───────────────┐
        │                              │
   [trong ngưỡng]              [vượt ngưỡng HOẶC còn tồn đọng]
        │                              │
        │                              ▼
        │              ┌──────────────────────────────────┐
        │              │ CHẶN PHÁT HÀNH — chỉ doanh       │
        │              │ nghiệp này. Cảnh báo kế toán.    │
        │              │ Các doanh nghiệp còn lại phát    │
        │              │ hành bình thường (BR-18.5)       │
        │              └──────────────────────────────────┘
        ▼
   ┌───────────────────────────────────────┐
   │ Hệ tính phí dựng hóa đơn NHÁP         │  ← toán thương mại (làm tròn từng
   │ (BR-18.1 — chạy thử được cả đợt)      │    dòng, thứ tự áp ưu đãi)
   └───────────────────┬───────────────────┘    thuộc về đây, không thuộc CRM
                       │
                       │   nhánh “chờ thủ tục bắt buộc” đã bỏ ngày 2026-08-26:
                       │   hóa đơn là chứng từ nội bộ (BR-18.0), nháp đi thẳng
                       │   sang đã phát hành
                       ▼
   ┌───────────────────────────────────────┐
   │ ĐÃ PHÁT HÀNH — BẤT BIẾN từ đây        │
   │ • có số hóa đơn, ngày phát hành       │
   │ • NGÀY TỚI HẠN = ngày THỰC SỰ phát    │
   │   hành + ân hạn của phương thức       │
   │   (BR-18.9) ← mốc khởi phát DUY NHẤT  │
   │   của chu trình nhắc nợ               │
   └───────────────────┬───────────────────┘
                       │
        ┌──────────────┴──────────────┐
        │                             │
  [tổng > 0]                    [tổng = 0]
        │                             │
        ▼                             ▼
   sang Luồng D              ĐÃ THANH TOÁN ngay,
   (thu tiền)                không vào chu trình thu tiền,
                             không vào nhắc nợ (BR-20.11)
```

**Hai tính chất kiến trúc:**

- **Đối soát là cổng chặn, không phải báo cáo.** Nó nằm **trên** đường phát hành, không nằm cạnh. Chặn theo từng doanh nghiệp chứ không chặn cả đợt.
- **Ngày tới hạn sinh ra ở đây, không sinh ra ở khối 6.** Khối nhắc nợ chỉ **đọc** nó. Nếu khối nhắc nợ tự suy ra ngày tới hạn thì thị trường có thủ tục bắt buộc sẽ bị nhắc nợ cho một hóa đơn khách chưa nhận được.

### 4.4 Luồng D — Thu tiền, nhắc nợ, đình chỉ, khôi phục

```text
                        ┌────────────────────────────┐
                        │      ĐANG HIỆU LỰC         │
                        └──────────────┬─────────────┘
                                       │
                    ┌──────────────────┴──────────────────┐
                    │  Thu tiền thất bại — TRƯỚC ngày     │
                    │  tới hạn?                           │
                    └──────────────────┬──────────────────┘
                                       │
                    ┌──────────────────┴──────────────────┐
                    │ CÓ → chỉ là cảnh báo về phương thức │
                    │ thanh toán, KHÔNG phải một bước     │
                    │ nhắc nợ (BR-21.9)                   │
                    └─────────────────────────────────────┘
                                       │
                          [đã qua ngày tới hạn & chưa thu được]
                                       ▼
                        ┌────────────────────────────┐
                        │      NỢ QUÁ HẠN            │
                        │  • lịch thử lại cấu hình   │
                        │    được THEO PHÂN KHÚC     │
                        │    (BR-21.1)               │
                        │  • mỗi lần một thông báo   │
                        │    nêu ĐÚNG việc cần làm   │
                        │    (BR-21.2)               │
                        │  • gửi cả Chủ workspace và │
                        │    Người phụ trách thanh   │
                        │    toán (BR-21.4)          │
                        │  • Inbound VÀ Outbound     │
                        │    đều còn hoạt động       │
                        └───┬──────────────────┬─────┘
                            │                  │
              ┌─────────────┘                  │
              │                    ┌───────────┴───────────┐
              │                    │  Có khiếu nại mở?     │
              │                    └───────────┬───────────┘
              │                                │
              │              ┌─────────────────┴─────────────────┐
              │              │ CÓ → tạm dừng chu trình cho PHẦN  │
              │              │ bị khiếu nại, trong thời hạn kết  │
              │              │ luận cam kết. Phần còn lại vẫn    │
              │              │ tới hạn bình thường               │
              │              │ (BR-27.3 · BR-27.7 · BR-18b.6)    │
              │              └─────────────────┬─────────────────┘
              │                                │
              │              [hết lịch thử lại & hết ân hạn]
              │                                │
              │              [ĐÃ gửi thông báo riêng nêu rõ ngày
              │               đình chỉ và hệ quả — BR-21.6]
              │                                ▼
              │                    ┌────────────────────────────┐
              │                    │       ĐANG ĐÌNH CHỈ        │
              │                    │  CHẶN: các hành vi chủ động│
              │                    │  phát sinh chi phí mới     │
              │                    │  (BR-22.2)                 │
              │                    │  KHÔNG CHẶN:               │
              │                    │  • tiếp nhận inbound       │
              │                    │    (BR-22.3) — tính vào    │
              │                    │    hóa đơn kỳ sau          │
              │                    │  • xuất dữ liệu (BR-22.1)  │
              │                    │  • Chủ workspace & Người   │
              │                    │    phụ trách thanh toán    │
              │                    │    vẫn đăng nhập (BR-22.7) │
              │                    └───────┬──────────────┬─────┘
              │                            │              │
              │  [thu được tiền — bất kỳ bước nào]        │
              │  KHÔNG phụ thuộc giờ làm việc             │
              │  (BR-21.8 · BR-22.4 · NFR-19)             │
              └────────────┬───────────────┘              │
                           ▼                   [quá thời hạn đình chỉ
                ┌────────────────────┐          kéo dài đã công bố]
                │  ĐANG HIỆU LỰC     │                    │
                │  Khôi phục tức thì │                    ▼
                └────────────────────┘         ┌──────────────────────┐
                                               │  CHỜ XÓA             │
                                               │  • thông báo trước   │
                                               │  • cho cơ hội tải về │
                                               │    (BR-22.6)         │
                                               └──────────────────────┘

  ┌──────────────────────────────────────────────────────────────────────────┐
  │ RÀNG BUỘC XUYÊN SUỐT: lỗi thuộc phía hệ thống hoặc phía cổng thanh toán  │
  │ KHÔNG ĐƯỢC tính vào số lần thất bại dẫn tới đình chỉ (BR-21.3). Nguyên   │
  │ nhân từng lần thất bại phải phân nhóm được, vì mỗi nhóm dẫn tới một việc │
  │ cần làm khác nhau — và vì một sự cố cổng thanh toán không được đẩy toàn  │
  │ bộ khách hàng tới gần đình chỉ hơn.                                       │
  └──────────────────────────────────────────────────────────────────────────┘
```

### 4.5 Luồng E — Bút toán đảo

```text
   [Căn cứ hủy hiệu lực xuất hiện]
   • đối tượng gốc bị đánh dấu lạm dụng → TỰ ĐỘNG, không chờ người (BR-14.6)
   • bên thứ ba xác nhận không phục vụ được
   • sự cố của hệ thống nhà cung cấp
   • quyết định miễn trừ thương mại → vượt ngưỡng thì cần người duyệt khác
                       │
                       ▼
   ┌───────────────────────────────────────────────┐
   │ Tạo sự kiện ĐẢO tham chiếu tới sự kiện gốc    │
   │ Sự kiện gốc KHÔNG bị sửa, KHÔNG bị xóa        │
   │ (BR-14.1 · BR-09.5)                           │
   └───────────────────┬───────────────────────────┘
                       │
        ┌──────────────┴───────────────┐
        │                              │
   [kỳ CHƯA chốt]                 [kỳ ĐÃ phát hành hóa đơn]
        │                              │
        ▼                              ▼
   ┌─────────────────────┐   ┌──────────────────────────────┐
   │ Giảm trực tiếp lượng│   │ Hóa đơn KHÔNG bị sửa         │
   │ tiêu dùng của kỳ    │   │ Tạo yêu cầu CHỨNG TỪ GHI CÓ  │
   │                     │   │ dẫn ngược được về đúng những │
   │ ĐỒNG THỜI hoàn lại  │   │ sự kiện đã đảo               │
   │ phần HẠN MỨC và     │   │ (BR-14.2 · BR-23.1 · BR-23.4)│
   │ phần TRẦN đã chiếm  │   └──────────────────────────────┘
   │ (BR-14.7)           │
   └─────────┬───────────┘
             │
             ▼
   ┌───────────────────────────────────────────────┐
   │ Chuyển giao đảo sang hệ tính phí qua CÙNG một │
   │ đường của Luồng A                             │
   │ Không chuyển giao đảo = số hai bên vĩnh viễn  │
   │ lệch nhau = đối soát chặn hóa đơn của mọi     │
   │ doanh nghiệp từng bị spam                     │
   └───────────────────────────────────────────────┘
```

**Tính chất kiến trúc:** đảo đi qua **cùng một đường chuyển giao** với sự kiện thường, không có đường riêng. Đường riêng nghĩa là hai cơ chế chống trùng, hai cơ chế thử lại, hai chỗ để hỏng.

### 4.6 Luồng F — Đồng bộ hai chiều và bù phần thất lạc

```text
    HỆ TÍNH PHÍ / CỔNG THANH TOÁN                      CRM
    ─────────────────────────────                      ───
                  │
                  │  webhook: hóa đơn dựng xong,
                  │  hóa đơn phát hành, thu tiền
                  │  thành công, thu tiền thất bại,
                  │  đăng ký đổi trạng thái
                  ├──────────────────────────►┌────────────────────────────┐
                  │                           │ Ghi nhận ĐÃ NHẬN trước khi │
                  │                           │ xử lý — chống xử lý lặp    │
                  │                           │ (ADR-0004 §5.1)            │
                  │                           └─────────────┬──────────────┘
                  │                                         │
                  │                                         ▼
                  │                           ┌────────────────────────────┐
                  │                           │ Cập nhật khối 3            │
                  │                           │ → khối 1 đọc được ngay     │
                  │                           │ → khối 6 phản ứng          │
                  │                           └────────────────────────────┘
                  │
                  │      ┌─────────────────────────────────────────────┐
                  │◄─────┤ QUÉT CHỦ ĐỘNG ĐỊNH KỲ                       │
                  │      │ Phạm vi tối thiểu:                          │
                  ├─────►│ • doanh nghiệp đang nợ hoặc đang nhắc nợ    │
                  │      │ • doanh nghiệp đang đình chỉ                │
                  │      │ • doanh nghiệp có ảnh chụp cục bộ đã cũ     │
                  │      │ • một lượt bắt buộc trước mỗi giờ chốt kỳ   │
                  │      └─────────────────────────────────────────────┘
```

**Hai tính chất kiến trúc:**

- **Webhook là đường nhanh, quét chủ động là đường đúng.** Hệ thống phải đúng ngay cả khi mọi webhook đều mất.
- **Ảnh chụp cục bộ hết hạn không được hiểu là "không có vấn đề gì".** Một doanh nghiệp đang đình chỉ mà ảnh chụp hết hạn phải nằm trong phạm vi quét, và trong lúc chờ quét thì nguồn đọc là bản bền vững ở khối 3 chứ không phải giá trị mặc định.

### 4.7 Luồng G — Truy vết từ dòng hóa đơn xuống đối tượng gốc

```text
   [Người phụ trách thanh toán mở một dòng trên hóa đơn]
                       │
                       ▼
   ┌───────────────────────────────────────────────┐
   │ Đọc bản chiếu hóa đơn — CỤC BỘ                │  ← KHÔNG gọi ra ngoài.
   │ (khối 3)                                      │    Đây là màn hình doanh
   └───────────────────┬───────────────────────────┘    nghiệp mở thường xuyên;
                       │                                gọi ra ngoài ở đây là
                       ▼                                đưa một điểm hỏng bên
   ┌───────────────────────────────────────────────┐    ngoài vào đường đọc
   │ Truy kho sự kiện theo doanh nghiệp + loại     │
   │ tiêu dùng + khoảng kỳ của dòng đó             │
   └───────────────────┬───────────────────────────┘
                       │
                       ▼
   ┌───────────────────────────────────────────────┐
   │ LỌC THEO HAI TRỤC QUYỀN ĐỘC LẬP (NFR-3)       │
   │ • quyền tài chính  → thấy được danh sách,     │
   │   đếm ra đúng con số trên hóa đơn             │
   │ • quyền nghiệp vụ  → mới đọc được nội dung    │
   │ Không có trục hai: chỉ thấy định danh, thời   │
   │ điểm, loại, số lượng (BR-27.5)                │
   └───────────────────┬───────────────────────────┘
                       │
        ┌──────────────┴───────────────┐
        │                              │
   [khớp — đóng]              [không đồng ý → mở khiếu nại
                               CHO CHÍNH DÒNG ĐÓ]
                                       │
                                       ▼
                       ┌───────────────────────────────────┐
                       │ • tạm dừng thu tiền phần bị khiếu │
                       │   nại, trong thời hạn kết luận    │
                       │ • quá hạn → LEO THANG, không treo │
                       │ • vượt ngưỡng số khiếu nại của    │
                       │   doanh nghiệp → chuyển sang xử   │
                       │   lý có người soát, không tự động │
                       │   tạm dừng nữa (BR-27.7)          │
                       └───────────────────────────────────┘
```

---

## 5. THỰC THỂ Ở MỨC KIẾN TRÚC

Bảng này nêu **thực thể nào phải tồn tại và tính chất bất biến của nó**. Trường dữ liệu, kiểu dữ liệu và cấu trúc lưu trữ quyết định lúc lên plan cho từng hạng mục.

| Thực thể | Thuộc khối | Tính chất bắt buộc | Căn cứ |
| --- | --- | --- | --- |
| Sự kiện tính phí | 3 | Chỉ thêm mới. Mang mã tham chiếu duy nhất tới đối tượng gốc. Hai mốc thời gian tách bạch: phát sinh nghiệp vụ và ghi nhận. Không bao giờ sửa, không bao giờ xóa | BR-09.1 · BR-09.5 · BR-12.1 |
| Sự kiện cách ly | 3 | Nơi giữ sự kiện không xác định được doanh nghiệp, chờ xử lý tay. Không được gán doanh nghiệp mặc định | BR-09.7 |
| Hồ sơ thanh toán | 3 | Một doanh nghiệp một hồ sơ. Tiền tệ và múi giờ cắt kỳ bất biến trong vòng đời đăng ký | BR-03.1 · BR-03.3 |
| Danh mục loại tiêu dùng | 3 | **Là dữ liệu cấu hình, không phải mã nguồn.** Mã đã phát hành không đổi tên, không tái sử dụng, không xóa. Mang cách gộp trong kỳ | BR-02.1 · BR-02.3 · NFR-12 |
| Danh mục gói, phiên bản gói & add-on | **Lago, không thuộc CRM** | Là dữ liệu cấu hình. Phiên bản đã có khách không sửa tại chỗ. CRM đọc trực tiếp khi cần (§2.1), không cache | BR-01.2 · BR-01.8 |
| Ảnh chụp đăng ký & hạn mức | 3 | Đọc được cục bộ với độ trễ thấp, không phụ thuộc mạng ra ngoài. Mỏng — chỉ đủ để chặn/cho phép, KHÔNG chứa điều kiện gói. Có nguồn bền vững phía sau lớp đọc nhanh | BR-04.5 · BR-32.4 |
| Thay đổi đã lên lịch | 3 | Hạ gói, hủy add-on, hủy đăng ký — có hiệu lực đầu kỳ sau. Điều kiện phải được **kiểm lại tại mốc cắt kỳ**, không thỏa thì không có hiệu lực | BR-06.2 · BR-06.6b · BR-07.4 |
| Bản chiếu hóa đơn | 3 | Chỉ đọc. Mang ngày tới hạn và trạng thái theo FEAT-18b. CRM không tự phát hành, không tự sửa | BR-18.2 · FEAT-18b |
| Chứng từ ghi có & biến động số dư có | 3 | Số dư có là **chuỗi biến động**, không phải một con số bị ghi đè — nếu không thì không tái lập được | BR-20.7 · BR-23.5 · NFR-8 |
| Khoản thu | 3 | Luôn gắn với đúng một hóa đơn. Khoản chưa khớp được phải có trạng thái chờ, không tự gán | BR-20.6 · BR-20.10 |
| Khiếu nại | 3 | Gắn với **từng dòng** hóa đơn. Có thời hạn kết luận cam kết và đường leo thang | BR-18b.6 · BR-27.7 |
| Bản ghi webhook đã nhận | 5 | Ghi nhận trước khi xử lý, chống xử lý lặp | ADR-0004 §5.1 |
| Kết quả đối soát | 4 | Lưu kể cả khi khớp. Nêu riêng hai chiều lệch. Xem được xu hướng theo thời gian | BR-15.2 · BR-15.4 · BR-15.5 |
| Nhật ký kiểm toán thương mại | 7 | Không sửa, không xóa bởi bất kỳ vai trò nào. Chủ thể có thể là người **hoặc** tác vụ tự động — cấu trúc phải đỡ được cả hai, nếu không fail-closed sẽ chặn mọi việc chạy nền | BR-29.3 · BR-29.4 · BR-29.1b |
| Nhật ký thông báo đã gửi | 6 | Đối chiếu được khi doanh nghiệp nói mình không nhận được | BR-31.6 |
| Chi phí nhà cung cấp tự chịu | 4 | Đo riêng, không gộp vào giá vốn chung — là khoản duy nhất phát sinh mà không có dòng doanh thu đối ứng | BR-16.8 · BR-30.3 |

---

## 6. CHIẾN LƯỢC XUYÊN SUỐT

### 6.1 Chống trùng hai lớp

Chống trùng phải có **hai lớp độc lập**: một khi CRM ghi nhận, một khi hệ tính phí nhận. Một lớp không đủ vì hai bên có thể mất đồng bộ mà không bên nào biết. Đây là điều kiện để hệ thống dám thử lại khi không chắc chắn (NFR-6 → NFR-7).

### 6.2 Ghi trước, chuyển sau

Không thao tác nghiệp vụ nào được chờ một hệ thống bên ngoài. Sự kiện ghi bền vững tại CRM trước, chuyển giao sau, hoàn toàn tách rời. Đây là điều làm cho NFR-11 và BR-32.1 đúng được.

### 6.3 Ba mức ứng xử khi hỏng

| Tình huống | Ứng xử | Cấm |
| --- | --- | --- |
| Không chuyển giao được sự kiện | Giữ lại, thử lại theo lịch, vượt số lần thì vào danh sách chờ xử lý tay kèm cảnh báo | Bỏ qua im lặng (BR-12.2) |
| Không ghi nhận được sự kiện | Ghi bù sau kèm cảnh báo, có cơ chế bền vững phía sau | Biến thành "miễn phí" (BR-09.4 · BR-32.2) |
| Không tra được hạn mức | Phân tầng theo rủi ro chi phí: chi phí thấp hoặc inbound thì cho phép và ghi nhận kèm cảnh báo; lô lớn chủ động thì tạm hoãn | Cho qua đồng loạt, hoặc chặn đồng loạt (BR-32.3) |

Mọi lần rơi vào ba tình huống này PHẢI tổng hợp lại được, để đội vận hành biết **quy mô ảnh hưởng** chứ không chỉ biết là "có sự cố" (BR-32.5).

### 6.4 Fail-closed có ranh giới

Không ghi được vết của một **quyết định thương mại** thì không thực hiện quyết định đó. Nhưng nguyên tắc này **không phủ lên dòng sự kiện tính phí** — ghi một sự kiện là ghi nhận một việc đã xảy ra, không phải một quyết định thương mại (BR-29.1b). Không kẻ ranh giới này thì fail-closed sẽ chặn thẳng vào luồng phục vụ khách hàng, mâu thuẫn với BR-09.3 và BR-32.1.

### 6.5 Cách ly giữa các doanh nghiệp

Khối lượng tăng đột biến của một doanh nghiệp không được làm chậm việc ghi nhận, đối soát hay chốt kỳ của doanh nghiệp khác (NFR-14). Điều này ràng buộc cách khối 5 tổ chức việc chuyển giao — không phải một hàng đợi phẳng dùng chung. **Ngưỡng cụ thể chưa có** (SRS Mục 7.2 câu 22) và nó quyết định một lựa chọn không đảo ngược rẻ, nên phải chốt trước khi xây khối 5.

### 6.6 Quan sát được

Ba chỉ số phải nhìn thấy liên tục, không phải tra khi có sự cố:

1. **Mức tồn đọng sự kiện chưa chuyển giao**, có cảnh báo trước giờ chốt kỳ (BR-12.5).
2. **Xu hướng chênh lệch đối soát theo thời gian** — một mức lệch nhỏ nhưng tăng đều nguy hiểm hơn một lần lệch lớn đơn lẻ (BR-15.5).
3. **Tổng giá trị đã đảo trong kỳ, tách theo từng căn cứ** — tỷ lệ đảo tăng bất thường ở một căn cứ là dấu hiệu định nghĩa thời điểm tính phí đang sai (BR-14.5).

Chỉ số 1 và 3 phải chảy ra tới màn hình của doanh nghiệp dưới dạng "số liệu đang thiếu", không chỉ ở màn hình vận hành (BR-17.3, NFR-10).

---

## 7. ÁNH XẠ YÊU CẦU PHI CHỨC NĂNG → CƠ CHẾ

| NFR | Cơ chế kiến trúc chịu trách nhiệm | Kiểm chứng bằng |
| --- | --- | --- |
| NFR-1 Cách ly dữ liệu tài chính | Khối 8 phân quyền theo Mục 5 SRS; lọc ngay ở tầng trả kết quả, không ở tầng giao diện | Thử truy cập chéo doanh nghiệp qua mọi đường: màn hình, tích hợp, tệp xuất |
| NFR-2 Không lưu dữ liệu thanh toán | Toàn bộ luồng thẻ nằm ở cổng thanh toán; khối 9 không nhận và không truyền dữ liệu thẻ | Rà toàn bộ kho dữ liệu CRM |
| NFR-3 Quyền tài chính tách quyền nghiệp vụ | Hai trục quyền độc lập, kiểm ở Luồng G | Người có quyền tài chính đếm được nhưng không đọc được nội dung |
| NFR-6 Không tính phí trùng | Chống trùng hai lớp (§6.1) | Gửi lặp cùng mã tham chiếu qua nhiều tiến trình song song |
| NFR-7 Không mất tiêu dùng | Ghi trước chuyển sau + thử lại + danh sách chờ xử lý tay | Ngắt hệ tính phí nhiều giờ, đối soát sau khi khôi phục |
| NFR-8 Con số tái lập được | Kho sự kiện chỉ thêm mới ở CRM + lịch sử phiên bản gói bất biến giữ trong Lago (BR-01.2) + biến động số dư dạng chuỗi. **Chỉ đúng khi còn dùng Lago** — xem rủi ro chấp nhận ở §8.2 | Dựng lại một hóa đơn cũ từ dữ liệu gốc, so với bản đã phát hành |
| NFR-9 Bất biến chứng từ | Hóa đơn do hệ tính phí sở hữu; CRM chỉ giữ bản chiếu chỉ đọc | Thử sửa một hóa đơn đã phát hành qua mọi đường |
| NFR-10 Số liệu thiếu phải thấy được | Mức tồn đọng chảy ra tới màn hình doanh nghiệp (§6.6) | Tạo tồn đọng, kiểm mọi màn hình hiển thị tiêu dùng |
| NFR-11 Lỗi tính phí không lan sang nghiệp vụ | Không lời gọi đồng bộ ra ngoài trên đường nghiệp vụ (§1.2, Luồng A) | Ngắt hệ tính phí, đo độ trễ và tỷ lệ lỗi của nghiệp vụ |
| NFR-12 Thay đổi thương mại không cần triển khai lại | Danh mục gói và danh mục loại tiêu dùng là **dữ liệu**, không phải mã | Thêm một loại tiêu dùng và một gói mới, không chạm mã nguồn |
| NFR-13 Chốt kỳ trong cửa sổ cam kết | Chốt kỳ chặn theo từng doanh nghiệp, không chặn cả đợt (Luồng C) | **Chưa kiểm chứng được — chưa có con số** (SRS Mục 7.2 câu 21) |
| NFR-14 Một doanh nghiệp không ảnh hưởng doanh nghiệp khác | Cách ly ở khối 5 (§6.5) | **Chưa kiểm chứng được — chưa có ngưỡng** (SRS Mục 7.2 câu 22) |
| NFR-15 Mọi con số giải thích được | Luồng G, dựa trên kho sự kiện giữ đủ thời hạn | Mọi dòng trên mọi hóa đơn trong thời hạn khiếu nại đi xuống được chi tiết |
| NFR-16 Không có giá hồi tố | Lịch sử phiên bản gói bất biến trong Lago + BR-13.3b (khoản kỳ trước tính theo điều kiện kỳ đó) | Đổi giá gói rồi kiểm hóa đơn của khách cũ |
| NFR-17 Chứng từ lưu đủ thời hạn luật định | Bảng thời hạn lưu trữ ở Mục 4.6 của SRS | **Phần lớn chưa chốt** — xem bảng đó |
| NFR-18 / 18b Xóa dữ liệu cá nhân | Thu hẹp dữ liệu định danh nhưng giữ được số lượng và khả năng đối soát tổng | Thực hiện một yêu cầu xóa, kiểm hóa đơn cũ còn tái lập được không |
| NFR-19 Đường thanh toán luôn sẵn sàng | Khôi phục tự động không phụ thuộc giờ làm việc (Luồng D) | **Chưa có mức cam kết** (SRS Mục 7.2 câu 20) |

---

## 8. RỦI RO KIẾN TRÚC & QUYẾT ĐỊNH TREO

`ADR-0004` đang ở trạng thái `accepted-with-open-commercial-decision`. Phần ranh giới đã khóa; phần dưới đây chưa.

### 8.1 Quyết định chặn — phân định theo mốc, không theo giai đoạn

Bảng này thu hẹp lần đầu ngày 2026-08-25, thu hẹp tiếp ngày 2026-08-26 khi phạm vi bỏ lớp pháp lý và thuế (K2b và K2c không còn). Bản trước gắn nhãn chặn cho cả cụm tính năng, dẫn tới việc thuế và nhắc nợ trông như điều kiện tiên quyết của toàn bộ lớp thương mại — chúng không phải. **Cái chặn là những điểm không sửa được sau, và hầu hết là quyết định thiết kế chứ không phải tính năng.**

| # | Quyết định | Chặn ở mốc nào | Nếu chốt muộn |
| --- | --- | --- | --- |
| **K1a** | **Hệ nào cấp số hóa đơn** — nơi phát hành và giữ dãy số chứng từ | **Hóa đơn thật đầu tiên gửi khách thật** | BR-18.2 + BR-18.6: chứng từ đã phát hành bất biến, dãy số liên tục. Đổi nơi cấp số sau đó = hai dãy song song, và không đánh số lại được chứng từ đã gửi khách |
| **K1b** | **Ai chạy chu trình nhắc nợ** | Trước khi xây khối 6 | Không chặn phần lõi. Trước đó kế toán theo dõi hóa đơn quá hạn thủ công. Ràng buộc duy nhất phải giữ từ sớm: cổng thanh toán tắt cơ chế thử lại của chính nó ngay từ đầu, nếu không sau này có hai lịch chạy song song |
| **K2a** | **Hóa đơn có trường ngày tới hạn, neo vào ngày THỰC SỰ phát hành** | **Hóa đơn thật đầu tiên** | Hóa đơn bất biến. Không có ngày tới hạn thì chứng từ cũ vĩnh viễn không có mốc khởi phát cho nhắc nợ. Neo vào ngày chốt kỳ vẫn sai kể cả khi không còn thủ tục thuế: soát nháp và phát hành theo đợt vẫn tách hai mốc ra, và doanh nghiệp mất bấy nhiêu ngày ân hạn. **Đây là điểm dự trữ duy nhất còn lại** |
| **K3** | **Có bán gói theo chu kỳ khác tháng không** (SRS Mục 7.2 câu 13) | Trước khi xây khối 3 | Cách biểu diễn mốc cắt kỳ khác hẳn nhau giữa hai phương án; đổi sau khi đã có khách là làm lại lõi cắt kỳ |
| **K4** | **Ngưỡng cách ly giữa các doanh nghiệp** (SRS Mục 7.2 câu 22) | Trước khi xây khối 5 | Quyết định có cần cách ly luồng chuyển giao theo doanh nghiệp hay không — làm lại sau khi đã có khách hàng lớn là đắt |
| **K5** | **Cửa sổ chốt kỳ cam kết** (SRS Mục 7.2 câu 21) | Trước khi vận hành chốt kỳ ở quy mô thật | Không có con số thì không thiết kế được mức song song của đợt chốt kỳ và không nghiệm thu được NFR-13 |

**Nguyên tắc rút ra:** một hạng mục hoãn được khi nó *thêm vào* hệ thống đang chạy mà không phải sửa dữ liệu đã sinh ra. Nhắc nợ thỏa điều kiện đó — với đúng **hai** điểm dự trữ **K1a** và **K2a**: một quyết định về nơi cấp số, và một trường ngày trên chứng từ. K2b và K2c đã bỏ ngày 2026-08-26 cùng FEAT-19: hóa đơn là chứng từ nội bộ (BR-18.0), không có thủ tục nào chèn vào giữa chốt kỳ và thu tiền.

### 8.2 Rủi ro đã nhận diện, có phương án giảm thiểu

| Rủi ro | Giảm thiểu |
| --- | --- |
| Đổi nhà cung cấp về sau làm mất khả năng tái lập hóa đơn cũ | **Rủi ro chấp nhận có chủ đích** (quyết định 2026-08-29, §2.1) — đổi lại là tránh được một module billing thứ hai ở CRM. Nếu về sau rời Lago, phải xuất/lưu trữ lịch sử phiên bản gói của Lago TRƯỚC khi tắt Lago, như một bước di trú rõ ràng — không phải một cơ chế kiến trúc thường trực |
| Webhook thất lạc làm trạng thái cục bộ sai | Quét chủ động định kỳ, phạm vi tối thiểu nêu ở Luồng F |
| Ảnh chụp cục bộ hết hạn bị hiểu là "không có vấn đề" | Nguồn dự phòng là bản bền vững ở khối 3, không phải giá trị mặc định (Luồng B) |
| Đối soát báo lệch giả vì hai bên chia kỳ khác nhau | Ranh giới kỳ phải đồng nhất hai bên; kiểm điều này ở bước tích hợp đầu tiên, không để tới lúc chốt kỳ thật |
| Bút toán đảo không tới được hệ tính phí | Đảo đi cùng đường chuyển giao với sự kiện thường (Luồng E) |
| Toán thương mại giao cho hệ tính phí nhưng không ai xác nhận cấu hình đúng | Làm tròn từng dòng nửa lên (BR-18.10) và thứ tự áp ưu đãi (BR-08.8) phải có kiểm chứng đối chứng ở giai đoạn 2, không chỉ tin vào cấu hình |

---

## 9. LỘ TRÌNH THEO GIAI ĐOẠN

Thứ tự dưới đây bám bản đồ phụ thuộc ở Mục 2.5 của SRS, và bám một nguyên tắc xếp thứ tự: **đưa vòng đời thương mại chạy được end-to-end trước; tuân thủ pháp lý và thu hồi nợ thêm vào sau khi hệ thống đã lên.** Điều đó chỉ an toàn nhờ ba điểm dự trữ K1a, K2a, K2b ở Mục 8.1 — chúng rẻ ở Giai đoạn 2 và không trả nổi ở Giai đoạn 4.

```text
GĐ1  đo lường            →  không thất thoát tiêu dùng, không ảnh hưởng nghiệp vụ
GĐ2  vòng đời thương mại  →  setup gói · bán gói · gia hạn · đổi gói · add-on
                             · hạn mức · hóa đơn · thu tiền · tự phục vụ
                             ◄── MỐC BÁN ĐƯỢC
GĐ3  đối soát & minh bạch →  bảo vệ toàn vẹn doanh thu, trả lời được khiếu nại
GĐ4  thu hồi nợ          →  nhắc nợ · đình chỉ
                             THÊM VÀO hệ thống đang chạy
```

### Giai đoạn 1 — Nền tảng đo lường

**Mục tiêu:** không thất thoát dữ liệu tiêu dùng, không ảnh hưởng hiệu năng nghiệp vụ.
**Khối:** 2, 3 (kho sự kiện, danh mục), 5 (chiều đẩy), 9 (phần chuyển giao sự kiện).
**Điều kiện tiên quyết:** K3 và K4 đã chốt. SRS Mục 7.2 câu 1 và 2 đã chốt (nếu không, mã loại tiêu dùng có nguy cơ phải bỏ theo BR-02.2).
**Nghiệm thu:** ngắt hệ tính phí nhiều giờ — không mất sự kiện nào, không tính trùng khi khôi phục, không thao tác nghiệp vụ nào chậm đi.

### Giai đoạn 2 — Vòng đời thương mại ◄ mốc bán được

**Mục tiêu:** một doanh nghiệp tự đi hết vòng — chọn gói, dùng, chạm hạn mức, mua thêm add-on, nâng gói, nhận hóa đơn, trả tiền, gia hạn sang kỳ sau — mà không cần nhân viên nhà cung cấp can thiệp bước nào.
**Khối:** 1, 3 (đăng ký & hạn mức, bản chiếu hóa đơn), 8 (cổng tự phục vụ), 9 (phần thanh toán, phần đọc danh mục gói trực tiếp từ Lago).
**Điều kiện tiên quyết:** K1a đã chốt trước hóa đơn thật đầu tiên; K2a và K2b được xác nhận trong thiết kế FEAT-18b.
**Chưa cần ở giai đoạn này:** tích hợp cơ quan thuế, chu trình nhắc nợ tự động, đình chỉ tự động. Thu tiền chưa được thì kế toán theo dõi danh sách hóa đơn quá hạn và đòi thủ công — với vài chục doanh nghiệp đầu tiên đó là cách đúng, không phải cách tạm.
**Nghiệm thu:** vòng đời trên chạy trọn vẹn không cần can thiệp; đặt hạn mức về không thì khách hàng của doanh nghiệp vẫn nhắn tới được và một lô gửi hàng loạt bị từ chối **trọn vẹn** thay vì chạy nửa chừng; mọi hóa đơn phát hành ra đều đã mang ngày tới hạn dù chưa có chu trình nhắc nợ nào chạy.

### Giai đoạn 3 — Đối soát & minh bạch

**Mục tiêu:** bảo vệ tính toàn vẹn doanh thu, trả lời được khiếu nại, kiểm toán được.
**Khối:** 4, 7, 8 (truy vết & quản trị nhà cung cấp).
**Điều kiện tiên quyết:** K5 đã chốt. SRS Mục 7.2 câu 10 và 23 đã chốt (thời gian giữ dữ liệu chi tiết — chốt muộn thì dữ liệu có nguy cơ đã bị dọn trước khi khiếu nại tới).
**Hai ngoại lệ phải kéo lên Giai đoạn 2 ở bản tối thiểu:**

- **Đối soát** — BR-18.5 cấm phát hành hóa đơn khi lệch vượt ngưỡng, nên bản tối thiểu (đếm hai bên, lệch quá ngưỡng thì giữ hóa đơn của riêng doanh nghiệp đó) phải đi cùng bước phát hành đầu tiên. Phần báo cáo xu hướng để lại đây.
- **Nhật ký kiểm toán** — BR-29.2 quy định không ghi được vết thì không thực hiện quyết định thương mại, mà Giai đoạn 2 đã đầy quyết định thương mại. Vết không dựng lại được từ quá khứ.

**Nghiệm thu:** một đợt chốt kỳ có doanh nghiệp lệch vượt ngưỡng — hóa đơn của riêng doanh nghiệp đó bị giữ lại, các doanh nghiệp còn lại phát hành bình thường.

### Giai đoạn 4 — Thu hồi nợ

**Mục tiêu:** tự động hóa việc thu hồi nợ khi số lượng doanh nghiệp vượt quá khả năng theo dõi thủ công.
**Khối:** 6.
**Điều kiện tiên quyết:** K1b đã chốt. **Và quan trọng hơn: hai điểm dự trữ K1a và K2a đã đúng từ Giai đoạn 2** — nếu không, giai đoạn này không còn là "thêm vào" mà thành "sửa lại dữ liệu lịch sử", và hóa đơn đã phát hành thì không sửa được.
**Nghiệm thu:** chu trình nhắc nợ khởi phát đúng theo ngày tới hạn đã in trên hóa đơn phát hành từ Giai đoạn 2, không phải một mốc tính lại về sau; hóa đơn cũ **không bị thay đổi nội dung**.

### Lớp pháp lý đã ra khỏi phạm vi — chỗ để nó gắn vào sau vẫn phải giữ

Ngày 2026-08-26 phạm vi thu hẹp: hóa đơn của hệ thống là **chứng từ nội bộ** (BR-18.0), không phải chứng từ hợp lệ theo quy định thuế. FEAT-19 đã xóa khỏi SRS.

Điều đó **không xóa nghĩa vụ pháp lý** nếu doanh nghiệp phát hành chứng từ cho khách hàng thật; nó chỉ đưa nghĩa vụ đó ra khỏi tài liệu này, thành một lớp riêng nằm trên, có tài liệu riêng khi đến lúc.

Ba tính chất dưới đây là chỗ để lớp đó gắn vào mà không phải sửa dữ liệu lịch sử. Chúng nằm trong phạm vi hiện tại và **không được bỏ vì "giờ chỉ là hóa đơn nội bộ"**:

- hóa đơn đã phát hành **bất biến** (BR-18.2)
- dãy số **liên tục, không tái sử dụng** kể cả cho hóa đơn đã hủy nháp (BR-18.6)
- ngày tới hạn neo vào **ngày thực sự phát hành** (BR-18.9)

Chứng từ pháp lý về sau mang dãy số riêng của nó và tham chiếu ngược về số hóa đơn nội bộ — không thay thế, không sửa vào.
