---
status: accepted-with-open-commercial-decision
date: 2026-08-25
---

# ADR-0004: Ranh giới CRM ↔ Hệ tính phí: CRM đo lường, không tính tiền

## Bối cảnh & Vấn đề

CRM đang phục vụ nhiều doanh nghiệp trên cùng một hệ thống nhưng không có cơ chế thu tiền nào, trong khi chi phí biến đổi trả cho bên thứ ba (tin nhắn mẫu WhatsApp, và về sau là SMS, token AI, dung lượng) đang chảy một chiều. Khi xây dựng lớp thương mại, câu hỏi khó đảo ngược không phải "dùng công cụ nào" mà là **kiến thức về giá và trách nhiệm trạng thái được đặt ở đâu**. 

Chúng tôi quyết định: **CRM chịu trách nhiệm ghi nhận việc gì đã xảy ra và cho ai; toàn bộ kiến thức về giá, hạn mức, chu kỳ, thuế, hóa đơn và thu tiền nằm ngoài các module nghiệp vụ**, sau một ranh giới duy nhất là **Lớp sự kiện tính phí (Billing Event Boundary)**.

---

## Quyết định Kiến trúc (Architectural Decisions)

### 1. Ranh giới Trách nhiệm & Dòng dữ liệu
- **Module nghiệp vụ (Omnichat, Workflow, Campaign, IAM):** Chỉ phát sinh Sự kiện tính phí gồm `{ tenantId, usageTypeCode, referenceId, quantity, occurredAt }` và không biết gì thêm.
- **Sự kiện tính phí (Billing Event Store):** Được ghi nhận bền vững (durable write-ahead log) tại CRM trước, chuyển giao ra ngoài sau qua cơ chế Outbox bất đồng bộ, tách biệt hoàn toàn khỏi luồng phục vụ người dùng.
- **Bộ chuyển đổi tính phí (Billing Adapter):** Là thành phần duy nhất trong toàn hệ thống biết tới sự tồn tại của hệ tính phí bên ngoài; chịu trách nhiệm chuyển giao, thử lại và đảm bảo gửi lại cùng một mã tham chiếu không tạo phí trùng (Idempotency).
- **Hệ tính phí bên ngoài:** Sở hữu gói cước, hạn mức, tổng hợp tiêu dùng, tính toán phần lẻ giữa kỳ (proration) và phát hành hóa đơn.
- **Cổng thanh toán:** Sở hữu phương thức thanh toán của khách hàng, thu tiền định kỳ, thử lại và hoàn tiền. CRM tuyệt đối không lưu trữ dữ liệu thẻ.
- **Lựa chọn triển khai giai đoạn đầu:** Hệ tính phí là **Lago** (chạy độc lập qua Docker container chính hãng) và cổng thanh toán là **Stripe**. SRS [`billing-subscription-srs.md`](../../srs/billing-subscription-srs.md) được viết trung lập, không phụ thuộc vào tên nhà cung cấp cụ thể.

---

### 2. Quyết định về Nguồn sự thật (Source of Truth)

#### 2.1. Subscription Source of Truth: Mô hình Hybrid phân định theo trách nhiệm
- **Lago là Source of Truth cho Commercial Computation & Lifecycle:** Sở hữu định nghĩa gói cước, tính toán chu kỳ, proration khi nâng/hạ gói, hạn mức và lịch chuyển trạng thái theo hợp đồng thương mại.
- **CRM là Source of Truth cho Runtime Execution State:** Sở hữu bản sao snapshot cục bộ (tại Redis / MongoDB) về trạng thái hoạt động, cờ đình chỉ (`isSuspended`), và hạn mức tức thời để ra quyết định phân quyền/chặn thao tác với độ trễ < 5ms mà không có bất kỳ phụ thuộc mạng thời gian thực nào ra bên ngoài.
- **Nguyên tắc xử lý xung đột:** Khi mất kết nối giữa CRM và Lago, CRM áp dụng nguyên tắc **Fail-safe / Service Continuity**: ưu tiên giữ mạch phục vụ khách hàng, duy trì trạng thái cache gần nhất và ghi nhận sự kiện để đồng bộ bù sau.

#### 2.2. Invoice Source of Truth: Hệ tính phí bên ngoài (Lago)
- **Lago là Single Source of Truth cho Hóa đơn:** Sở hữu dãy số hóa đơn liên tục, chi tiết từng dòng (line items), tính toán thuế, trạng thái hóa đơn (`draft`, `finalized`, `paid`, `void`), tệp PDF hóa đơn và Chứng từ ghi có (Credit Notes).
- **CRM chỉ lưu Snapshot / Projection chỉ đọc (Read-only Reference):** CRM cache siêu dữ liệu hóa đơn để hiển thị trên cổng tự phục vụ của Tenant và phục vụ liên kết truy vết xuống sự kiện gốc (`FEAT-27`). CRM tuyệt đối không tự phát hành hay chỉnh sửa độc lập nội dung hóa đơn đã phát hành.

---

### 3. Nguyên tắc Bất biến Sự kiện & Bút toán đảo (Immutable Events & Reversal Principle)
- **Sự kiện tính phí là Bất biến (Immutable Append-only Log):** Một khi Sự kiện tính phí đã ghi vào CRM Event Store, nội dung của nó **KHÔNG BAO GIỜ ĐƯỢC SỬA HOẶC XÓA** (kể cả khi đối tượng nghiệp vụ gốc như hội thoại bị xóa, bị đánh dấu spam, hoặc tin nhắn mẫu bị nhà mạng từ chối gửi).
- **Điều chỉnh bằng Bút toán đảo (Reversal Event):** Mọi điều chỉnh, hủy hiệu lực đều phải tạo một sự kiện đảo mới mang cờ `reversalOfEventId` trỏ về sự kiện gốc kèm lý do bắt buộc (`BR-14.1`, `BR-14.3`).
- Đảo trong kỳ chưa chốt làm giảm trực tiếp lượng tiêu dùng lũy kế; đảo sau khi hóa đơn đã phát hành bắt buộc phải sinh Chứng từ ghi có (Credit Note) tại Lago (`BR-14.2`, `FEAT-23`).

---

### 4. Chiến lược Độc lập Nhà cung cấp (Billing Provider Adapter Contract)
Để chống Vendor Lock-in và bảo đảm tính hoán đổi (Swappability) khi chuyển từ Lago sang hệ khác (OpenMeter, KillBill, Stripe Billing, Zuora), CRM đóng gói toàn bộ giao tiếp qua một **Interface Contract duy nhất**:

```typescript
export interface BillingProviderAdapter {
  syncCustomer(account: BillingAccount): Promise<string>;
  createSubscription(tenantId: string, planCode: string, options?: any): Promise<SubscriptionResult>;
  sendMeteredEvent(event: BillingEvent): Promise<void>;
  calculateProration(upgradeDto: UpgradePlanDto): Promise<ProrationEstimate>;
  getUpcomingInvoice(tenantId: string): Promise<InvoiceDraftDto>;
  getInvoices(tenantId: string, query: PaginationQuery): Promise<PaginatedInvoices>;
  applyCreditNote(creditNote: CreditNoteRequest): Promise<CreditNoteResult>;
  syncSubscriptionState(tenantId: string): Promise<SubscriptionSnapshot>;
}
```
`LagoAdapter` là một implementation cụ thể của contract này. Không module nghiệp vụ hay controller nào trong CRM được gọi trực tiếp Lago SDK/API.

---

### 5. Khả năng phục hồi Webhook & Đối soát Hai chiều (Webhook Resiliency & Pull Sync)
Giao tiếp từ hệ tính phí/cổng thanh toán về CRM tiềm ẩn nguy cơ mất gói tin (dropped webhooks). Kiến trúc thiết lập cơ chế bảo vệ hai lớp:
1. **Xử lý Webhook Idempotent:** Mọi webhook (`invoice.created`, `invoice.paid`, `payment_failed`, `subscription.updated`) được ghi nhận `webhook_event_id` trước khi xử lý, chống xử lý lặp.
2. **Periodic Pull Reconciliation (Quét đồng bộ chủ động định kỳ):** CRM thiết lập cron worker định kỳ (mỗi 15 phút và 1 lần trước giờ chốt kỳ) chủ động gọi API của Lago/Stripe để đối chiếu trạng thái Subscription và Invoice của các tenant đang có công nợ hoặc đang trong chu trình nhắc nợ (Dunning), tự động bù đắp các webhook bị thất lạc.

---

## Phương án đã cân nhắc

- **Tự xây toàn bộ logic tính tiền trong CRM** — bị loại. Phần khó của billing không phải nhân số lượng với đơn giá, mà là phần lẻ giữa kỳ, ranh giới kỳ, chứng từ bất biến, ghi có, nhắc nợ, đa tiền tệ và tuân thủ hóa đơn theo từng quốc gia. Tự xây nghĩa là tự bảo trì vĩnh viễn một hệ kế toán, trong khi đây không phải năng lực cạnh tranh của sản phẩm.
- **Gọi thẳng hệ tính phí từ trong module nghiệp vụ, bỏ lớp sự kiện** — bị loại vì hai lý do: (1) Buộc luồng phục vụ khách hàng phụ thuộc vào hệ thống bên ngoài: hệ tính phí gián đoạn thì Agent không trả lời được khách hàng; (2) Rải kiến thức thương mại vào từng module, mỗi lần đổi mô hình giá lại phải sửa Omnichat, Workflow và Campaign cùng lúc.
- **Giao toàn bộ khâu bán hàng cho bên đứng tên (Merchant of Record — Paddle, Lemon Squeezy, FastSpring)** — bị loại. Phương án này gỡ được bài toán thuế quốc tế nhưng không phát hành được hóa đơn điện tử giá trị gia tăng (VAT) hợp lệ có mã của cơ quan thuế cho doanh nghiệp tại Việt Nam (`BR-19.3`).
- **Đặt lớp đo lường chuyên dụng (OpenMeter) vào ngay từ đầu** — bị loại cho giai đoạn đầu. Với hai loại tiêu dùng hiện tại (`conversation_created`, `message_template_sent`), Lago hoàn toàn đáp ứng tốt. Kiến trúc Billing Adapter giữ nguyên đường mở rộng sang OpenMeter trong tương lai mà không phải trả chi phí vận hành trước.

---

## Hệ quả & Ràng buộc Kỹ thuật

- **Nơi lưu trữ sự kiện tính phí:** Bảng `billing_events` trong CRM chỉ thêm mới, có trạng thái chuyển giao, lưu trữ tối thiểu bằng thời hạn khiếu nại hóa đơn để phục vụ đối soát (`FEAT-15`) và truy vết chi tiết (`FEAT-27`).
- **Idempotency Key là cam kết nghiệp vụ:** Mã tham chiếu đối tượng gốc `[tenantId:usageTypeCode:referenceId]` là cam kết bất biến cho phép hệ thống tự tin thử lại khi gặp sự cố mạng mà không sợ tính phí trùng (`BR-12.1`, `NFR-6`).
- **Đối soát định kỳ là cổng chặn bắt buộc:** So khớp số liệu giữa CRM và Lago là điều kiện tiên quyết trước khi phát hành hóa đơn; chênh lệch vượt ngưỡng sẽ kích hoạt cờ chặn hóa đơn (`BR-18.5`).
- **Thực thi cục bộ (Zero-dependency Runtime):** Trạng thái gói và hạn mức được lưu tại Redis để kiểm tra tức thời, đảm bảo chiều Inbound không bao giờ bị nghẽn (`BR-16.3`, `BR-32.4`).
- **Quyết định Mở về Cổng Thanh toán & Pháp nhân (Open Commercial Decision):**
  - Trạng thái ADR được đặt là `accepted-with-open-commercial-decision`.
  - Phần ranh giới kiến trúc và Engine tính phí (Billing Boundary) đã chốt và khóa hoàn toàn.
  - Phần Cổng thanh toán (Payment Gateway Boundary) và Hóa đơn điện tử phụ thuộc vào quyết định pháp nhân phát hành của Ban giám đốc và Kế toán (Việt Nam vs Quốc tế — SRS Mục 7.2 câu 3 & 4). Lớp `PaymentGatewayAdapter` được thiết kế theo Interface độc lập để sẵn sàng hỗ trợ song song Stripe (quốc tế) và cổng nội địa/HĐĐT khi có quyết định chính thức.
