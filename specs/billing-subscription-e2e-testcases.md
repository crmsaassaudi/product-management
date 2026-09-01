# BỘ E2E TEST CASES TOÀN DIỆN: BILLING & SUBSCRIPTION SYSTEM

> **Tài liệu tham chiếu liên quan:**  
> - Business Spec (SRS): [`product-management/srs/billing-subscription-srs.md`](../product-management/srs/billing-subscription-srs.md)  
> - Technical Spec: [`product-management/specs/billing-technical-spec.md`](../product-management/specs/billing-technical-spec.md)  
> - System Architecture: [`docs/14-billing.md`](./14-billing.md)  

**Vai trò:** Principal QA Engineer & Billing Domain Expert  
**Phạm vi hệ thống:** CRM, Multi-tenant Core, Billing Event Outbox/Ingestion Engine, Usage Metering, Add-on Engine, Invoice Portal, Billing Provider (Lago), Payment Provider (Stripe).  
**Tiêu chí trọng tâm:** Tính toàn vẹn tài chính (Financial Integrity), Chống thất thoát doanh thu (Revenue Leakage), Idempotency, Khả năng tự phục hồi (Self-healing), Chống Race Condition và Đảm bảo ranh giới cô lập Multi-Tenant (Tenant Isolation).

---

## MỤC LỤC PHÂN VÙNG KIỂM THỬ

1. [Tenant Onboarding & Initialization](#1-tenant-onboarding)
2. [Trial Lifecycle & Boundary Controls](#2-trial-lifecycle)
3. [Purchase Subscription & Payment Gateway Interaction](#3-purchase-subscription)
4. [Plan Upgrade & Proration Math](#4-plan-upgrade)
5. [Plan Downgrade & Entitlement Deprovisioning](#5-plan-downgrade)
6. [Cancel Subscription Lifecycle](#6-cancel-subscription)
7. [Reactivate Subscription](#7-reactivate-subscription)
8. [Payment Failure, Dunning & Suspension](#8-payment-failure-dunning--suspension)
9. [Invoice State Machine & Tax/Line Item Precision](#9-invoice-lifecycle)
10. [Credit Note, Refunds & Financial Adjustments](#10-credit-note--refunds)
11. [Add-on Purchase, Quota & Renewal](#11-add-on-purchase)
12. [Metered Usage Ingestion & Aggregation](#12-metered-usage)
13. [Billing Event Idempotency & Replay Defense](#13-billing-event-idempotency)
14. [Event Reversal & Usage Rollback](#14-event-reversal)
15. [Webhook Resilience & Out-of-Order Handling](#15-webhook-resilience)
16. [Webhook Lost & Self-Healing Reconciliation](#16-webhook-lost-scenario)
17. [Billing Provider (Lago) Outage & Offline Outbox](#17-billing-provider-outage)
18. [Payment Provider (Stripe) Outage & Circuit Breaking](#18-payment-provider-outage)
19. [Three-Way Financial Reconciliation](#19-reconciliation)
20. [Race Conditions & Concurrency Hazards](#20-race-conditions)
21. [Multi-Tenant Data & Quota Isolation](#21-multi-tenant-isolation)
22. [Security, Immutability & Financial Audit Trails](#22-security--financial-integrity)
23. [Disaster Recovery & Chaos Fault Injection](#23-disaster-recovery)
24. [Performance, Burst & High-Scale Ingestion](#24-performance--scale)

---

## 1. Tenant Onboarding

## Test Case

ID: TC-ONB-001  
Priority: P0 (Blocker)  
Feature: Tenant Onboarding - Happy Path with Default Free/Trial Plan  

Preconditions:
- CRM API, Lago API, và Stripe API ở trạng thái healthy.
- Gói `Trial-14d` được cấu hình sẵn trên Lago với full feature entitlements và hạn mức quota: 5 agents, 10,000 AI tokens, 500 conversations.

Steps:
1. Gửi request `POST /api/v1/tenants` tạo tenant mới: `{ "name": "Acme Corp", "slug": "acme", "adminEmail": "admin@acme.com" }`.
2. Kiểm tra database CRM (`tenants`, `subscriptions`).
3. Kiểm tra Lago API (`GET /api/v1/customers/acme`, `GET /api/v1/subscriptions`).
4. Kiểm tra Stripe API (`GET /v1/customers?email=admin@acme.com`).
5. Gọi `GET /api/v1/tenants/acme/entitlements` từ CRM.

Expected Result:
- CRM tạo tenant record với status `ACTIVE`.
- Stripe tạo Customer record với metadata `tenantId: "acme"`, lưu `stripeCustomerId` vào CRM.
- Lago tạo Customer với `external_id: "acme"`, gắn subscription gói `Trial-14d` với `trial_end` chính xác 14 ngày sau.
- CRM trả về entitlements đúng: 5 agents, 10,000 AI tokens.

Business Risk:
- Sai lệch ID giữa CRM, Lago và Stripe dẫn đến orphan customer, không thể thu tiền hoặc ghi nhận usage khi chuyển sang paid plan.

Automation Candidate: Yes (API Integration / Playwright E2E)

---

## Test Case

ID: TC-ONB-002  
Priority: P1 (Critical)  
Feature: Tenant Onboarding - Explicit No-Plan Provisioning (Hard Paywall)  

Preconditions:
- Tenant onboarding flow hỗ trợ cờ `requireSubscription: true` không cấp trial tự động.

Steps:
1. Gửi `POST /api/v1/tenants` với `{ "name": "Zero Corp", "slug": "zerocorp", "plan": null }`.
2. Truy cập các API nghiệp vụ: gửi tin nhắn, tạo agent, gọi AI chatbot.

Expected Result:
- Tenant được tạo thành công ở trạng thái `INACTIVE_SUBSCRIPTION` / `NO_PLAN`.
- Stripe Customer và Lago Customer được tạo sẵn (sẵn sàng cho purchase).
- Mọi API nghiệp vụ trả về HTTP `402 Payment Required` với mã lỗi `BILLING_SUBSCRIPTION_REQUIRED`.

Business Risk:
- Rò rỉ tài nguyên (Security/Revenue Leakage) do hệ thống quên kiểm tra paywall đối với tenant không có plan.

Automation Candidate: Yes (API Integration)

---

## Test Case

ID: TC-ONB-003  
Priority: P0 (Blocker)  
Feature: Tenant Onboarding - Idempotent Retry on Network Flake  

Preconditions:
- Client gửi request có HTTP Header `Idempotency-Key: idemp-onb-9901`.

Steps:
1. Gửi `POST /api/v1/tenants` với `Idempotency-Key: idemp-onb-9901`.
2. Ngắt kết nối mạng ngay khi CRM đang tương tác với Stripe.
3. Gửi lại chính xác request trên với cùng `Idempotency-Key` 3 lần liên tiếp.

Expected Result:
- CRM nhận diện `Idempotency-Key`, không tạo 2 tenant trong DB.
- Không tạo duplicated customer trên Stripe/Lago (chỉ có 1 customer duy nhất ứng với `external_id`).
- Cả 3 request retry đều nhận lại cùng response payload `200 OK` (hoặc `201 Created`) với cùng `tenantId`.

Business Risk:
- Nhân bản tài khoản, duplicate subscription charge, xung đột slug database.

Automation Candidate: Yes (API Integration)

---

## Test Case

ID: TC-ONB-004  
Priority: P1 (Critical)  
Feature: Tenant Onboarding - Duplicate Slug / Domain Collision  

Preconditions:
- Đã tồn tại tenant với slug `beta-corp`.

Steps:
1. Gửi request tạo tenant mới với slug trùng `beta-corp` nhưng email khác `admin2@beta.com`.

Expected Result:
- CRM từ chối với HTTP `409 Conflict`.
- Không có customer hay subscription rác nào được khởi tạo trên Lago/Stripe.

Business Risk:
- Ghi đè thông tin khách hàng, rò rỉ dữ liệu giữa các doanh nghiệp trùng tên.

Automation Candidate: Yes (API Integration)

---

## Test Case

ID: TC-ONB-005  
Priority: P0 (Blocker)  
Feature: Tenant Onboarding - Third-Party Failure Rollback (Stripe/Lago Fail)  

Preconditions:
- Mock Stripe API trả về HTTP `500 Internal Server Error` hoặc Lago API timeout.

Steps:
1. Gửi request tạo tenant `POST /api/v1/tenants`.
2. Quan sát cơ chế Saga / Two-phase rollback của CRM.

Expected Result:
- Transaction trong CRM DB bị rollback hoàn toàn (không lưu tenant ở trạng thái nửa vời).
- Nếu Lago customer đã lỡ tạo trước khi Stripe fail, CRM outbox gửi compensating transaction xóa/vô hiệu hóa Lago customer.
- Client nhận mã lỗi `503 Service Unavailable` kèm thông báo thử lại sau.

Business Risk:
- Dữ liệu bị phân mảnh (Ghost records), CRM ghi nhận có tenant nhưng không có billing profile, gây crash khi chạy billing job.

Automation Candidate: Yes (Integration Test with WireMock/Mountebank)

---

## Test Case

ID: TC-ONB-006  
Priority: P1 (Critical)  
Feature: Tenant Onboarding - CRM Crash & Recovery Mid-Saga  

Preconditions:
- Kill process CRM API chính xác tại thời điểm vừa tạo DB record nhưng chưa nhận phản hồi từ Lago.

Steps:
1. Trigger tenant creation qua webhook/API.
2. Force kill node process `crm-api`.
3. Khởi động lại `crm-api` và background worker.
4. Kiểm tra Outbox Job / Reconciliation Worker.

Expected Result:
- Background reconciliation worker quét DB thấy tenant ở trạng thái `PROVISIONING_PENDING`.
- Tự động resume saga: gọi Lago API để hoàn tất tạo subscription hoặc rollback an toàn.
- Tenant chuyển sang `ACTIVE` mà không cần người dùng thao tác lại.

Business Risk:
- Khách hàng đăng ký bị treo tiền hoặc treo tài khoản vô thời hạn mà CSKH không biết.

Automation Candidate: Yes (Chaos / Fault Injection Test)

---

## 2. Trial Lifecycle

## Test Case

ID: TC-TRL-001  
Priority: P1 (Critical)  
Feature: Trial Duration Enforcement (7 Days vs 14 Days)  

Preconditions:
- Plan A có trial 7 ngày (`trial_period: 7`).
- Plan B có trial 14 ngày (`trial_period: 14`).

Steps:
1. Onboard Tenant 1 với Plan A lúc T0.
2. Onboard Tenant 2 với Plan B lúc T0.
3. Kiểm tra metadata và `trial_end` trên Lago và CRM DB.

Expected Result:
- Tenant 1 có `trial_end = T0 + 7 days` (chính xác đến giây).
- Tenant 2 có `trial_end = T0 + 14 days`.
- Entitlements được mở khóa đầy đủ theo đúng định mức trial.

Business Risk:
- Cấu hình sai thời gian trial dẫn đến khách hàng bị trừ tiền sớm hoặc lạm dụng dùng thử miễn phí quá hạn.

Automation Candidate: Yes (API Integration)

---

## Test Case

ID: TC-TRL-002  
Priority: P0 (Blocker)  
Feature: Trial Natural Expiration at Exact Boundary (00:00 UTC)  

Preconditions:
- Tenant `trial-exp` có trial kết thúc lúc `2026-08-27T00:00:00Z`.
- Thời gian hệ thống giả lập: `2026-08-26T23:59:59Z` -> `2026-08-27T00:00:01Z`.

Steps:
1. Lúc `23:59:59Z`: Tenant gửi tin nhắn và gọi API AI chatbot.
2. Fast-forward clock sang `00:00:01Z`.
3. Lago trigger webhook `subscription.trial_ended` hoặc CRM cron job quét trial.
4. Tenant tiếp tục gửi tin nhắn và tạo agent mới.

Expected Result:
- Lúc `23:59:59Z`: Request xử lý thành công `200 OK`.
- Lúc `00:00:01Z`: Subscription chuyển sang `TRIAL_EXPIRED`.
- Mọi request nghiệp vụ tạo mới bị chặn ngay lập tức với HTTP `402 Payment Required`.
- Không phát sinh charge tiền tự động nếu chưa cấu hình thẻ tín dụng.

Business Risk:
- Lỗ hổng thời gian (Time-drift) cho phép người dùng tiếp tục tiêu thụ tài nguyên đắt đỏ (AI tokens, WhatsApp SMS) sau khi hết trial.

Automation Candidate: Yes (Time-travel / Clock-mocking Integration Test)

---

## Test Case

ID: TC-TRL-003  
Priority: P1 (Critical)  
Feature: Trial Expiration Mid-Session while Agent is Actively Chatting  

Preconditions:
- Agent của Tenant đang mở WebSocket session và thực hiện livechat với khách hàng.
- Trial hết hạn đúng thời điểm hội thoại đang diễn ra.

Steps:
1. Mở kết nối livechat qua WebSocket.
2. Trial của tenant chạm mốc kết thúc.
3. Agent cố gắng gửi tin nhắn tiếp theo trong room hiện tại.

Expected Result:
- WebSocket server nhận broadcast subscription expired.
- Tin nhắn gửi đi bị từ chối kèm event thông báo `SUBSCRIPTION_EXPIRED`.
- Không ngắt đột ngột gây crash client app, UI hiển thị modal thông báo nâng cấp gói cho agent.

Business Risk:
- Lọt metered usage (message count) sau khi subscription đã chết, gây sai lệch thống kê và chi phí hạ tầng.

Automation Candidate: Yes (E2E WebSocket / Integration Test)

---

## Test Case

ID: TC-TRL-004  
Priority: P0 (Blocker)  
Feature: Seamless Trial to Paid Plan Conversion before Expiration  

Preconditions:
- Tenant đang ở ngày thứ 5 của gói Trial 14 ngày.
- Đã tiêu thụ 3,000 / 10,000 AI tokens.

Steps:
1. Tenant thực hiện nâng cấp lên gói trả phí `Pro Monthly` ($99/tháng).
2. Nhập thẻ thanh toán và checkout thành công.
3. Kiểm tra quota và subscription state.

Expected Result:
- Trial kết thúc ngay lập tức; Subscription chuyển sang `ACTIVE` (Paid).
- Stripe charge ngay lập tức $99 cho kỳ đầu tiên.
- Quota được reset hoặc cộng dồn chính xác theo chính sách của gói Pro (ví dụ: cấp mới 100,000 tokens).
- Dữ liệu lịch sử tin nhắn, cấu hình agent trong thời gian trial được giữ nguyên 100%.

Business Risk:
- Mất dữ liệu khách hàng khi chuyển đổi gói, hoặc bị double charge cả tiền trial lẫn tiền gói.

Automation Candidate: Yes (E2E Integration)

---

## 3. Purchase Subscription

## Test Case

ID: TC-SUB-001  
Priority: P0 (Blocker)  
Feature: Purchase Monthly / Annual Subscription - Happy Path  

Preconditions:
- Tenant đang ở trạng thái `NO_PLAN` hoặc `TRIAL_EXPIRED`.
- Thẻ test Stripe hợp lệ (`pm_card_visa`).

Steps:
1. Gửi request `POST /api/v1/billing/subscriptions` với `{ "planCode": "pro_annual", "paymentMethodId": "pm_card_visa" }`.
2. Kiểm tra Stripe PaymentIntent, Stripe Invoice.
3. Kiểm tra Lago Subscription, CRM Tenant Entitlements.

Expected Result:
- Stripe tạo Invoice, PaymentIntent thành công với trạng thái `succeeded`.
- Lago kích hoạt subscription `pro_annual` với chu kỳ 1 năm (`billing_cycle: yearly`).
- CRM cập nhật trạng thái `status: ACTIVE`, cấp quyền Pro entitlements (ví dụ: 20 agents, unlimited conversations, 500k AI tokens).
- Gửi email hóa đơn PDF cho admin tenant.

Business Risk:
- Trừ tiền khách hàng nhưng không kích hoạt được dịch vụ trong CRM do lỗi đồng bộ state.

Automation Candidate: Yes (E2E API Test)

---

## Test Case

ID: TC-SUB-002  
Priority: P0 (Blocker)  
Feature: Subscription Purchase with Synchronous Payment Failure  

Preconditions:
- Thẻ test Stripe bị từ chối (`pm_card_chargeCustomerFail` hoặc thẻ hết hạn `pm_card_expiredCard`).

Steps:
1. Thực hiện checkout gói `Pro Monthly` với thẻ lỗi.

Expected Result:
- Stripe trả về lỗi `card_declined` hoặc `expired_card`.
- CRM bắt exception và trả về HTTP `400 Bad Request` kèm thông báo lỗi chi tiết cho user.
- Subscription trong CRM và Lago KHÔNG được kích hoạt (vẫn giữ nguyên trạng thái cũ).
- Không tạo hóa đơn `PAID` ảo trên hệ thống.

Business Risk:
- Kích hoạt dịch vụ khi chưa nhận được tiền thực tế (Revenue Leakage).

Automation Candidate: Yes (API Integration Test)

---

## Test Case

ID: TC-SUB-003  
Priority: P0 (Blocker)  
Feature: Subscription Purchase with Stripe 3D-Secure (3DS) Authentication  

Preconditions:
- Thẻ test Stripe yêu cầu xác thực 3DS (`pm_card_authenticationRequiredOnSetup`).

Steps:
1. Gửi request tạo subscription.
2. Nhận response từ CRM chứa `client_secret` và trạng thái `REQUIRES_ACTION`.
3. Client mô phỏng hoàn thành 3DS popup challenge trên Stripe.
4. Stripe gửi webhook `payment_intent.succeeded` và `invoice.paid`.

Expected Result:
- CRM giữ subscription ở trạng thái trung gian `PENDING_AUTHENTICATION`.
- Sau khi 3DS pass và nhận webhook, CRM tự động chuyển subscription sang `ACTIVE`.
- Giao diện người dùng nhận được notification kích hoạt thành công.

Business Risk:
- Rớt đơn hàng tại bước 3DS, subscription bị kẹt ở trạng thái pending mãi mãi.

Automation Candidate: Yes (E2E Playwright / Webhook Mocking)

---

## Test Case

ID: TC-SUB-004  
Priority: P1 (Critical)  
Feature: Concurrent / Duplicate Click "Purchase" Race Condition  

Preconditions:
- Người dùng bấm nút "Thanh toán" liên tục 5 lần trong vòng 200ms.

Steps:
1. Bắn đồng thời 5 request `POST /api/v1/billing/subscriptions` với cùng payload từ 5 thread song song.

Expected Result:
- Hệ thống áp dụng Distributed Lock (Redis Lock) dựa trên `tenantId`.
- Chỉ 1 request đầu tiên được xử lý và charge tiền Stripe thành công.
- 4 request còn lại bị từ chối với HTTP `409 Conflict` hoặc trả về kết quả của transaction đầu tiên nhờ Idempotency Key.
- Chỉ có duy nhất 1 Invoice và 1 Subscription được tạo trên Stripe/Lago.

Business Risk:
- Khách hàng bị trừ tiền 5 lần cho cùng 1 gói dịch vụ (Double/Multiple Charging), gây khiếu nại chargeback và phạt từ Visa/Mastercard.

Automation Candidate: Yes (Concurrency Test với K6 / Artillery)

---

## 4. Plan Upgrade

## Test Case

ID: TC-UPG-001  
Priority: P0 (Blocker)  
Feature: Mid-Billing-Cycle Upgrade with Proration (Basic -> Pro)  

Preconditions:
- Tenant đang dùng gói `Basic Monthly` ($30/tháng, 30 ngày/kỳ), đã dùng được 15 ngày (đã tiêu thụ $15).
- Tenant yêu cầu nâng cấp lên `Pro Monthly` ($90/tháng).
- Chính sách: Proration tức thì.

Steps:
1. Vào ngày thứ 15 của kỳ, gửi request nâng cấp `POST /api/v1/billing/subscription/upgrade` sang `Pro Monthly`.
2. Kiểm tra hóa đơn tính toán proration tạo ra từ Lago/Stripe.
3. Kiểm tra quyền hạn (Entitlements) và Quota mới trên CRM.

Expected Result:
- Số tiền dư chưa dùng của gói Basic: -$15 (Credit).
- Số tiền 15 ngày còn lại của gói Pro: +$45.
- Số tiền phải thanh toán ngay lập tức: $45 - $15 = $30.
- Stripe charge chính xác $30.
- Entitlements được nâng ngay lập tức lên cấp Pro (Quota tăng tương ứng).
- Invoice hiển thị 2 line items proration rõ ràng.

Business Risk:
- Sai công thức Proration dẫn đến tính thiếu tiền của khách hàng hoặc thu vượt mức gây mất uy tín.

Automation Candidate: Yes (Financial Calculation Test)

---

## Test Case

ID: TC-UPG-002  
Priority: P1 (Critical)  
Feature: Immediate Upgrade Seconds After Purchase (Zero-Usage Proration)  

Preconditions:
- Tenant vừa mua `Basic Monthly` ($30) cách đây 5 phút.
- Chưa phát sinh bất kỳ metered usage nào.

Steps:
1. Gửi request upgrade lên `Pro Monthly` ($90).

Expected Result:
- Hệ thống tính toán credit hoàn lại gần như trọn vẹn ($30).
- Số tiền charge thêm chính xác: $90 - $30 = $60.
- Thời hạn chu kỳ được đồng bộ lại chuẩn xác theo billing cycle anchor.

Business Risk:
- Lỗi làm tròn số (Rounding error / Precision issue) gây lệch 1 cent hoặc tính đè 100% gói mới mà không trừ gói cũ.

Automation Candidate: Yes (API Integration)

---

## 5. Plan Downgrade

## Test Case

ID: TC-DNG-001  
Priority: P0 (Blocker)  
Feature: Downgrade at Period End (Enterprise -> Basic)  

Preconditions:
- Tenant đang dùng gói `Enterprise` ($500/tháng), kỳ thanh toán còn 10 ngày nữa mới kết thúc.

Steps:
1. Gửi request downgrade `POST /api/v1/billing/subscription/downgrade` sang `Basic` với tùy chọn `at_period_end: true`.
2. Kiểm tra quyền hạn trong 10 ngày còn lại.
3. Kiểm tra thời điểm sau khi kết thúc chu kỳ.

Expected Result:
- Trong 10 ngày còn lại: Tenant vẫn giữ nguyên toàn bộ quyền hạn và quota của gói Enterprise.
- Subscription state trên Lago/CRM ghi nhận `PENDING_DOWNGRADE`.
- Tại ngày cuối kỳ: Subscription tự động chuyển sang `Basic`, không hoàn tiền chênh lệch của kỳ cũ.
- Hóa đơn kỳ tiếp theo được phát hành với giá của gói Basic ($30).

Business Risk:
- Cắt quyền Enterprise của khách hàng ngay lập tức khi họ đã trả trước đủ tiền cho cả tháng.

Automation Candidate: Yes (API & Scheduler Test)

---

## Test Case

ID: TC-DNG-002  
Priority: P1 (Critical)  
Feature: Immediate Downgrade with Resource Cap Overflow Handling  

Preconditions:
- Gói `Enterprise` cho phép 50 agents. Tenant hiện đang có 20 agents hoạt động.
- Gói `Basic` chỉ cho phép tối đa 3 agents.
- Thực hiện Downgrade ngay lập tức.

Steps:
1. Gửi request downgrade ngay lập tức về `Basic`.

Expected Result:
- Hệ thống kiểm tra điều kiện chuyển gói: Nếu số lượng agent hiện tại (20) vượt quá giới hạn gói mới (3), hệ thống trả về cảnh báo HTTP `422 Unprocessable Entity` yêu cầu tenant deactivate/xóa bớt 17 agents trước.
- HOẶC (theo policy hệ thống): Cho phép downgrade nhưng tự động khóa 17 agents theo quy tắc LIFO và chỉ cho phép 3 agents gần nhất hoạt động.

Business Risk:
- Khách hàng hạ gói giá rẻ nhưng vẫn tiếp tục sử dụng vượt mức tài nguyên của gói cao cấp.

Automation Candidate: Yes (API Integration)

---

## 6. Cancel Subscription

## Test Case

ID: TC-CNC-001  
Priority: P0 (Blocker)  
Feature: Cancel Subscription at Period End  

Preconditions:
- Tenant đang có subscription `Pro Monthly` trả phí, hạn đến ngày `2026-09-15`.

Steps:
1. Tenant bấm "Hủy gói" trên giao diện Billing Settings (`POST /api/v1/billing/subscription/cancel`, payload `{ "immediately": false }`).
2. Kiểm tra subscription status trên CRM, Lago, Stripe.
3. Fast-forward thời gian qua ngày `2026-09-15`.

Expected Result:
- CRM cập nhật trạng thái `CANCEL_AT_PERIOD_END` (`cancel_at: 2026-09-15`).
- Stripe Subscription set `cancel_at_period_end = true`.
- Khách hàng vẫn dùng bình thường đến hết `2026-09-15`.
- Đúng `2026-09-15T00:00:00Z`: Webhook trigger chuyển trạng thái thành `CANCELED`, revoke toàn bộ quyền Pro, không phát sinh hóa đơn gia hạn tự động.

Business Risk:
- Tiếp tục tự động trừ tiền thẻ tín dụng của khách sau khi họ đã yêu cầu hủy gói (Vi phạm nghiêm trọng quy định Stripe & Payment Card Schemes).

Automation Candidate: Yes (E2E Integration)

---

## Test Case

ID: TC-CNC-002  
Priority: P1 (Critical)  
Feature: Immediate Cancellation & Usage Rejection  

Preconditions:
- Tenant yêu cầu hủy gói ngay lập tức (`immediately: true`).

Steps:
1. Gửi request hủy ngay lập tức.
2. Gửi request tạo conversation hoặc tin nhắn qua API CRM ngay sau đó 1 giây.

Expected Result:
- Subscription chuyển sang `CANCELED` ngay lập tức.
- Stripe subscription hủy ngay lập tức.
- Request tạo conversation bị từ chối với HTTP `402 Payment Required`.
- Không phát sinh thêm bất kỳ metered event nào vào Lago.

Business Risk:
- Hệ thống tiếp tục nhận và xử lý billing events sau khi đã hủy, gây sai lệch báo cáo kế toán.

Automation Candidate: Yes (API Integration)

---

## 7. Reactivate Subscription

## Test Case

ID: TC-RCT-001  
Priority: P1 (Critical)  
Feature: Reactivate Subscription Before Expiration Date  

Preconditions:
- Subscription đang ở trạng thái `CANCEL_AT_PERIOD_END`, còn 5 ngày nữa mới chính thức hết hạn.

Steps:
1. Tenant chọn "Hủy bỏ yêu cầu hủy gói" (`POST /api/v1/billing/subscription/reactivate`).

Expected Result:
- Cờ `cancel_at_period_end` trên Stripe và Lago được gỡ bỏ (`false`).
- Subscription trở lại trạng thái `ACTIVE` bình thường.
- Chu kỳ thanh toán và ngày gia hạn tự động tiếp theo giữ nguyên không thay đổi.
- Không thu thêm bất kỳ khoản phí kích hoạt lại nào.

Business Risk:
- Kích hoạt lại tạo ra 1 subscription mới song song, dẫn đến khách hàng bị trừ tiền 2 lần vào chu kỳ tới.

Automation Candidate: Yes (API Integration)

---

## Test Case

ID: TC-RCT-002  
Priority: P1 (Critical)  
Feature: Resubscribe Churned Tenant After Expiration  

Preconditions:
- Tenant đã bị `CANCELED` hoàn toàn cách đây 30 ngày.

Steps:
1. Tenant đăng nhập và chọn mua lại gói `Pro Monthly`.
2. Thực hiện thanh toán với thẻ mới.

Expected Result:
- Hệ thống khởi tạo một subscription mới gắn vào customer ID cũ.
- Trừ tiền chu kỳ mới thành công.
- Khôi phục quyền hạn đầy đủ cho tenant.

Business Risk:
- Lỗi xung đột state máy trạng thái (FSM) khi cố gắng chuyển từ `CANCELED` về `ACTIVE` trên cùng 1 record cũ.

Automation Candidate: Yes (API Integration)

---

## 8. Payment Failure, Dunning & Suspension

## Test Case

ID: TC-PAY-001  
Priority: P0 (Blocker)  
Feature: Renewal Payment Failure & Dunning Schedule (Day 1, 3, 5, 7)  

Preconditions:
- Subscription đến ngày gia hạn tự động. Thẻ tín dụng của khách bị hết hạn / hết tiền.

Steps:
1. Stripe kích hoạt recurring charge thất bại, bắn webhook `invoice.payment_failed` lần 1 (Day 0).
2. CRM nhận webhook và cập nhật subscription sang `PAST_DUE`.
3. Mô phỏng Stripe retry lần 2 (Day 3), lần 3 (Day 5), lần 4 (Day 7) đều thất bại.

Expected Result:
- Day 0: Gửi email cảnh báo thanh toán thất bại cho admin tenant, kích hoạt **Grace Period** (vẫn cho dùng tính năng).
- Day 3 & Day 5: Tiếp tục gửi email nhắc nhở dunning.
- Day 7 (Sau max retries): Stripe đánh dấu Invoice `uncollectible`.
- CRM nhận webhook cuối cùng, tự động chuyển tenant sang trạng thái `SUSPENDED` (Khóa toàn bộ quyền truy cập và API).

Business Risk:
- Khóa tài khoản khách hàng ngay lập tức ở lần lỗi đầu tiên (gây ức chế và churn) HOẶC cho khách dùng miễn phí mãi mãi dù không thu được tiền.

Automation Candidate: Yes (Webhook & State Machine Integration Test)

---

## Test Case

ID: TC-PAY-002  
Priority: P1 (Critical)  
Feature: Successful Payment Recovery During Grace Period  

Preconditions:
- Tenant đang ở trạng thái `PAST_DUE` (Ngày thứ 2 của Grace Period).

Steps:
1. Tenant vào portal cập nhật thẻ thanh toán mới hợp lệ.
2. CRM gọi Stripe retry payment trên invoice đang pending.
3. Stripe charge thành công và gửi webhook `invoice.payment_succeeded`.

Expected Result:
- Invoice chuyển sang `PAID`.
- Subscription chuyển ngay từ `PAST_DUE` về `ACTIVE`.
- Hủy toàn bộ lịch dunning và reset bộ đếm cảnh báo.
- Dịch vụ của tenant hoạt động liền mạch không bị gián đoạn.

Business Risk:
- Tiền đã trừ thành công trên thẻ mới nhưng CRM vẫn giữ trạng thái `PAST_DUE` và khóa tài khoản khách hàng sau 7 ngày.

Automation Candidate: Yes (E2E Integration)

---

## 9. Invoice Lifecycle

## Test Case

ID: TC-INV-001  
Priority: P0 (Blocker)  
Feature: Invoice State Machine Flow (Draft -> Issued -> Paid / Draft_Cancelled) [BR-18b.1, BR-18.2]  

Preconditions:
- Lago và CRM đã cấu hình chu kỳ xuất hóa đơn tự động.

Steps:
1. Hết chu kỳ billing, hệ thống gom usage và tạo `Draft Invoice`.
2. Trigger phát hành hóa đơn (Issuance / Finalization) -> nhận số hóa đơn liên tục `INV-YYYY-XXXXXX` (BR-18.6).
3. Thu tiền thành công -> Chuyển sang `PAID`.
4. Trường hợp hủy: Hóa đơn còn ở `DRAFT` bị hủy chuyển sang `DRAFT_CANCELLED`; hóa đơn đã `ISSUED` là bất biến, muốn điều chỉnh phải phát hành `Credit Note` (FEAT-23, BR-18.2).

Expected Result:
- State Machine chuyển đổi hợp lệ: `DRAFT` -> `ISSUED` (hoặc `FINALIZED`) -> `PAID`.
- Hóa đơn nháp hủy: `DRAFT` -> `DRAFT_CANCELLED` (số hóa đơn không được tái sử dụng - BR-18.6).
- Không thể đảo ngược từ `ISSUED` hoặc `PAID` quay lại `DRAFT` (BR-18b.2).
- Hóa đơn đã `ISSUED` không thể bị xóa cứng khỏi DB (chỉ có thể issue Credit Note theo FEAT-23).
- Trạng thái quá hạn (Past Due) là thuộc tính dẫn xuất từ `dueDate`, không phải state lưu trong DB (BR-18b.4).

Business Risk:
- Vi phạm quy định kế toán khi cho phép xóa hoặc sửa đổi hóa đơn đã phát hành.

Automation Candidate: Yes (State Machine Unit/Integration Test)

---

## Test Case

ID: TC-INV-002  
Priority: P0 (Blocker)  
Feature: Flat Overage, Included Quota & Half-Up Line Item Precision [BR-01.1, BR-18.10, BR-18.0]  

Preconditions:
- Gói `Pro`: $100.00/tháng (bao gồm 1,000 tin nhắn mẫu WhatsApp).
- Metered Usage: 1,500 tin nhắn (1,000 bao gồm + 500 tin nhắn vượt @ $0.05/tin).
- Add-on: 2 Extra Agents = $20.00/agent = $40.00.
- Currency: USD (Hóa đơn chứng từ nội bộ, không có thuế VAT theo BR-18.0).

Steps:
1. Tổng hợp số liệu tiêu dùng và yêu cầu phát hành hóa đơn cuối kỳ.
2. Thực hiện làm tròn nửa lên (Half-up) từng dòng trước khi cộng tổng theo BR-18.10.

Expected Result:
- Base Plan (Gói Pro): $100.00
- Add-on (2 Extra Agents): $40.00
- Metered Usage Overage (500 tin vượt * $0.05): $25.00
- **Subtotal / Total Invoice Amount:** $165.00
- Không có lỗi sai lệch 1 cent do float rounding (áp dụng Decimal Half-up Rounding per line item).
- Không có dòng tính thuế VAT (Chứng từ nội bộ theo BR-18.0).

Business Risk:
- Sai lệch số tiền gây thất thoát doanh thu hoặc tranh chấp hóa đơn với khách hàng.

Automation Candidate: Yes (Precision Math Unit Test)

---

## 10. Credit Note & Refunds

## Test Case

ID: TC-CRD-001  
Priority: P1 (Critical)  
Feature: Partial Credit Note Issuance & Line Item Tracing [BR-23.1, BR-23.4, FEAT-23]  

Preconditions:
- Hóa đơn đã thanh toán trị giá $200.00 (gồm $150.00 Subscription + $50.00 Usage do spam).

Steps:
1. CSKH/Kế toán thực hiện giảm trừ $50.00 từ CRM Dashboard do phát hiện tin nhắn spam (BR-10.5, BR-14.3).
2. Kiểm tra phát hành Chứng từ ghi có (Credit Note) tại Lago / CRM.

Expected Result:
- Lago / CRM sinh `Credit Note` tương ứng với giá trị $50.00 gắn link trực tiếp tới dòng hóa đơn gốc và sự kiện đảo gốc (BR-23.4).
- Hóa đơn ban đầu giữ nguyên số tiền gốc (bất biến - BR-18.2).
- Số dư khả dụng của invoice giảm còn $150.00; số dư có được hoàn về tài khoản tenant để bù trừ kỳ sau (BR-23.5) hoặc hoàn tiền thật nếu có yêu cầu.

Business Risk:
- Giảm trừ không sinh Credit Note dẫn đến báo cáo doanh thu không khớp với dữ liệu kế toán và lịch sử giao dịch.

Automation Candidate: Yes (API Integration Test)

---

## Test Case

ID: TC-CRD-002  
Priority: P0 (Blocker)  
Feature: Full Refund and Revocation Audit Trail  

Preconditions:
- Khách hàng mới mua gói trong vòng 24h và yêu cầu hoàn tiền 100% (Money-back guarantee).

Steps:
1. Thực hiện full refund toàn bộ giá trị hóa đơn.
2. Kiểm tra quyền hạn của tenant và audit log.

Expected Result:
- Toàn bộ hóa đơn được hoàn tất qua Credit Note full amount.
- Subscription bị đóng hoặc chuyển về `FREE/TRIAL_EXPIRED`.
- Toàn bộ quyền hạn bị thu hồi ngay lập tức.
- Ghi nhận đầy đủ audit trail: Người thực hiện refund, lý do, timestamp, Stripe refund ID, Lago credit note ID.

Business Risk:
- Khách hàng được hoàn lại tiền nhưng vẫn tiếp tục sử dụng dịch vụ trả phí không bị chặn.

Automation Candidate: Yes (E2E Integration)

---

## 11. Add-on Purchase

## Test Case

ID: TC-ADD-001  
Priority: P1 (Critical)  
Feature: Mid-Cycle Add-on Purchase (Extra AI Tokens & Extra Agents)  

Preconditions:
- Tenant đang dùng gói `Pro` có sẵn 5 agents và 100k tokens.
- Mua thêm Add-on: "Gói 5 Extra Agents" ($25/tháng) và "Gói 500k AI Tokens" ($50 one-off).

Steps:
1. Gửi request `POST /api/v1/billing/addons/purchase` với 2 add-on trên.
2. Kiểm tra việc charge tiền tức thì trên Stripe.
3. Kiểm tra quota trên CRM.

Expected Result:
- Stripe charge ngay lập tức số tiền tương ứng (tính proration cho recurring add-on nếu mua giữa tháng).
- CRM cập nhật ngay lập tức: Số lượng agent tối đa tăng từ 5 -> 10; Hạn mức AI token tăng thêm 500k tokens.
- Add-on "5 Extra Agents" được gắn vào chu kỳ gia hạn tiếp theo; Add-on "500k AI Tokens" là non-recurring (chỉ dùng đến khi hết).

Business Risk:
- Khách hàng trả tiền mua thêm tài nguyên nhưng hệ thống không mở thêm quota khiến công việc kinh doanh của họ bị ngưng trệ.

Automation Candidate: Yes (API Integration)

---

## Test Case

ID: TC-ADD-002  
Priority: P1 (Critical)  
Feature: Add-on Quota Exhaustion & Hard-Limit Enforcement  

Preconditions:
- Tenant đã mua thêm add-on 50,000 AI tokens (tổng quota: 150,000 tokens).

Steps:
1. Gửi liên tục các request AI chat để tiêu thụ hết 150,000 tokens.
2. Gửi request AI chat thứ 150,001.

Expected Result:
- Từ token thứ 1 đến 150,000: Thực thi bình thường.
- Tại token 150,001: Trả về HTTP `429 Too Many Requests` hoặc `402 Payment Required` với mã `QUOTA_EXCEEDED`.
- UI hiển thị cảnh báo hết quota và gợi ý mua thêm add-on.
- Không cho phép âm quota trừ khi tenant bật tính năng "Auto-Overage Billing".

Business Risk:
- Tràn quota không bị chặn khiến nhà cung cấp chịu chi phí OpenAI/Anthropic API khổng lồ mà không thu được tiền từ khách.

Automation Candidate: Yes (Integration Test)

---

## 12. Metered Usage

## Test Case

ID: TC-MTR-001  
Priority: P0 (Blocker)  
Feature: Ingestion of All Usage Types (Conversations, Messages, AI Tokens, Storage)  

Preconditions:
- Tenant đang hoạt động với đầy đủ 4 loại metered metric cấu hình trên Lago:
  1. `conversation_created` (Counter)
  2. `message_template_sent` (Counter)
  3. `ai_token_used` (Sum)
  4. `storage_gb` (Gauge / Maximum)

Steps:
1. Tạo 1 conversation mới.
2. Gửi 5 tin nhắn template.
3. Chạy 1 AI prompt tiêu tốn 1,450 tokens.
4. Upload file làm tăng dung lượng lưu trữ thêm 2.5 GB.
5. CRM ghi nhận Billing Event vào Outbox và đồng bộ sang Lago API.

Expected Result:
- Lago nhận đầy đủ 4 events tương ứng với các giá trị: `conversation_created: 1`, `message_template_sent: 5`, `ai_token_used: 1450`, `storage_gb: 2.5`.
- Số liệu trên Lago Dashboard khớp 100% với database nội bộ CRM.
- Không làm trễ thời gian phản hồi của API chính (> 50ms overhead là fail).

Business Risk:
- Bỏ sót usage dẫn đến việc xuất hóa đơn thiếu tiền metered, thất thoát trực tiếp doanh thu biên.

Automation Candidate: Yes (End-to-End Metering Test)

---

## Test Case

ID: TC-MTR-002  
Priority: P0 (Blocker)  
Feature: High-Volume Usage Burst Processing  

Preconditions:
- Hệ thống tiếp nhận burst 50,000 tin nhắn trong vòng 10 giây từ một chiến dịch broadcast của tenant.

Steps:
1. Bắn 50,000 message events vào CRM Event Ingestion Queue (Redis/BullMQ).
2. Theo dõi worker gom batch (Batching Worker) đẩy sang Lago API (`POST /api/v1/events/batch`).

Expected Result:
- Queue xử lý mượt mà, không bị tràn bộ nhớ (OOM).
- Batching Worker gom 100-500 events/batch gửi sang Lago để tránh rate limit của Lago API.
- Đảm bảo 50,000 events được ingest thành công 100% (zero event drop).

Business Risk:
- Sập hàng đợi, mất mát hàng chục ngàn events tính tiền của các đợt gửi tin nhắn marketing quy mô lớn.

Automation Candidate: Yes (Performance/Load Test với K6)

---

## 13. Billing Event Idempotency

## Test Case

ID: TC-IDP-001  
Priority: P0 (Blocker)  
Feature: Single vs Replay Billing Events (1x, 2x, 10x, 100x Replays)  

Preconditions:
- Sự kiện gửi 1 tin nhắn trả phí có payload:
```json
{
  "tenantId": "T1",
  "usageType": "message_template_sent",
  "referenceId": "MSG_001_UNIQUE_HASH",
  "units": 1,
  "timestamp": 1771977600
}
```

Steps:
1. Gửi event lần 1 vào CRM Billing Ingestion endpoint.
2. Gửi lại chính xác event trên lần 2, lần 10, và liên tiếp 100 lần.
3. Kiểm tra tổng số usage ghi nhận trong CRM DB và trên Lago.

Expected Result:
- Lần 1: Event được chấp nhận (`200 OK` hoặc `202 Accepted`), usage tăng +1.
- Từ lần 2 đến lần 100: Hệ thống nhận diện `referenceId` (Transaction Key) đã tồn tại trong DB / Redis Deduplication Cache.
- Trả về `200 OK` (idempotent response) nhưng **KHÔNG ĐƯỢC TĂNG USAGE**.
- Tổng usage trên Lago sau 100 lần gửi đúng bằng 1.

Business Risk:
- Khách hàng bị tính tiền 100 lần cho 1 tin nhắn duy nhất do mạng retry, dẫn đến kiện cáo và mất khách hàng.

Automation Candidate: Yes (API Idempotency Automation Suite)

---

## Test Case

ID: TC-IDP-002  
Priority: P0 (Blocker)  
Feature: High-Concurrency Duplicate Ingestion Race Condition  

Preconditions:
- 10 threads đồng thời gửi cùng 1 `referenceId: "EVENT_CONCURRENT_001"` tại cùng 1 mili-giây.

Steps:
1. Thực hiện parallel HTTP requests bắn cùng event payload.

Expected Result:
- Nhờ cơ chế Database Unique Constraint (`tenant_id`, `reference_id`) kết hợp Redis Distributed Lock, chỉ có duy nhất 1 thread thực hiện ghi DB thành công.
- 9 threads còn lại bị chặn hoặc duplicate key catch an toàn, không có 2 bản ghi nào lọt qua.
- Usage chỉ tăng chính xác 1 đơn vị.

Business Risk:
- Race condition vượt qua kiểm tra trùng lặp thông thường nếu không có unique index cứng ở tầng Database.

Automation Candidate: Yes (Concurrency Test)

---

## 14. Event Reversal

## Test Case

ID: TC-REV-001  
Priority: P1 (Critical)  
Feature: Message Delivery Failure Usage Reversal  

Preconditions:
- Hệ thống trừ trước 1 usage `message_template_sent` khi tenant bấm gửi.
- Nhà mạng viễn thông (Twilio/WhatsApp Meta) phản hồi tin nhắn bị `UNDELIVERED` / `REJECTED` do số điện thoại không tồn tại.

Steps:
1. CRM nhận webhook thất bại từ WhatsApp/Twilio.
2. CRM phát sinh sự kiện Reversal Event:
```json
{
  "tenantId": "T1",
  "usageType": "message_template_sent",
  "referenceId": "MSG_001_REVERSAL",
  "reversalOf": "MSG_001_UNIQUE_HASH",
  "units": -1
}
```
3. Gửi sang Lago / Adjust internal ledger.

Expected Result:
- Lago ghi nhận event điều chỉnh giảm trừ (hoặc Credit Adjustment).
- Usage ròng (Net Usage) của tenant cho tin nhắn lỗi quay về 0.
- Báo cáo cuối tháng không tính tiền tin nhắn không gửi được.

Business Risk:
- Thu tiền các tin nhắn lỗi do nhà mạng viễn thông từ chối, vi phạm SLA cam kết với khách hàng.

Automation Candidate: Yes (Event-driven Integration Test)

---

## 15. Webhook Resilience

## Test Case

ID: TC-WBH-001  
Priority: P0 (Blocker)  
Feature: Webhook Idempotency under Repeated Delivery (Stripe/Lago 10x Duplicates)  

Preconditions:
- Stripe gửi webhook `invoice.paid` (`id: "evt_123_test"`) cho hóa đơn $1,000.

Steps:
1. Gửi webhook payload lần 1 tới CRM endpoint `/api/v1/webhooks/stripe`.
2. Giả lập Stripe không nhận kịp response `200 OK` trong 3 giây và gửi lại webhook đó 10 lần liên tiếp.

Expected Result:
- Webhook processor lưu `evt_123_test` vào bảng `processed_webhooks`.
- Lần 1: Xử lý kích hoạt gói, ghi nhận doanh thu, gửi email xác nhận.
- 9 lần sau: Phát hiện event ID đã được xử lý, trả về `200 OK` ngay lập tức và **BỎ QUA**, không chạy lại logic nghiệp vụ.
- Khách hàng không nhận 10 email trùng lặp, không bị gia hạn gói 10 lần.

Business Risk:
- Duplicate provisioning, duplicate emails, duplicate reward points/commissions.

Automation Candidate: Yes (Webhook Test Suite)

---

## Test Case

ID: TC-WBH-002  
Priority: P0 (Blocker)  
Feature: Out-of-Order Webhook Delivery  

Preconditions:
- Do độ trễ mạng Internet, webhook `subscription.updated` (kỳ mới) đến TRƯỚC webhook `subscription.created` (kỳ cũ).

Steps:
1. Gửi webhook B (Timestamp T2, Version 2) tới CRM.
2. Sau đó mới gửi webhook A (Timestamp T1, Version 1) tới CRM.

Expected Result:
- CRM kiểm tra `event_timestamp` hoặc `version` của payload.
- Khi webhook B đến: Cập nhật state lên Version 2.
- Khi webhook A đến sau: Phát hiện Version 1 cũ hơn state hiện tại (Version 2), hệ thống ghi log và bỏ qua không ghi đè (No State Regression).
- State cuối cùng của Subscription luôn ở Version 2.

Business Risk:
- State Machine bị thụt lùi (State Regression), ví dụ: Gói đã active nhưng bị ghi đè ngược về pending khiến khách hàng bị mất quyền truy cập.

Automation Candidate: Yes (Asynchronous Webhook Simulator)

---

## Test Case

ID: TC-WBH-003  
Priority: P0 (Blocker)  
Feature: Webhook Signature Verification & Tamper Defense  

Preconditions:
- Secret key webhook Stripe: `whsec_test_secret_123`.

Steps:
1. Hacker gửi giả mạo webhook `invoice.paid` với body hợp lệ nhưng không có header `Stripe-Signature`.
2. Hacker gửi webhook với signature bị sai lệch / hash bằng secret key khác.

Expected Result:
- CRM Webhook Guard chặn ngay từ middleware, trả về HTTP `400 Bad Request` hoặc `401 Unauthorized`.
- Không có bất kỳ thay đổi nào xảy ra trong database.
- Ghi log bảo mật cảnh báo hành vi giả mạo webhook.

Business Risk:
- Hacker giả mạo webhook `invoice.paid` để kích hoạt miễn phí gói Enterprise trị giá hàng chục ngàn USD.

Automation Candidate: Yes (Security API Test)

---

## 16. Webhook Lost Scenario

## Test Case

ID: TC-WBL-001  
Priority: P0 (Blocker)  
Feature: Webhook Lost - Periodic Active Polling Reconciliation  

Preconditions:
- Khách hàng thanh toán thành công $500 trên Stripe Checkout Portal.
- Đường truyền Internet giữa Stripe và CRM bị đứt cáp, toàn bộ webhook từ Stripe bị mất (Drop 100%).

Steps:
1. Stripe Invoice đã ở trạng thái `paid`, nhưng CRM Subscription vẫn ở trạng thái `INCOMPLETE` / `PENDING_PAYMENT`.
2. Trigger cron job định kỳ chạy mỗi 15 phút: `BillingReconciliationWorker`.

Expected Result:
- Worker quét các subscription đang ở trạng thái `PENDING_PAYMENT` quá 10 phút.
- Chủ động gọi Stripe API: `GET /v1/invoices?customer=xxx` và `GET /v1/subscriptions/xxx`.
- Phát hiện Invoice thực tế đã `PAID` trên Stripe.
- Worker tự động cập nhật trạng thái CRM Subscription sang `ACTIVE`, bổ sung audit log `UPDATED_VIA_RECONCILIATION_CRON`.
- Khách hàng được kích hoạt dịch vụ tự động mà không cần liên hệ Support.

Business Risk:
- Khách hàng đã bị trừ tiền trên tài khoản ngân hàng nhưng ngồi chờ hàng giờ không thấy tài khoản CRM mở, gây bức xúc tột độ.

Automation Candidate: Yes (Cron Job Reconciliation Test)

---

## 17. Billing Provider Outage

## Test Case

ID: TC-OUT-001  
Priority: P0 (Blocker)  
Feature: Lago Provider Outage - Offline Outbox & Zero-Data-Loss  

Preconditions:
- Toàn bộ hạ tầng Lago API bị sập (trả về HTTP `502 Bad Gateway` hoặc Timeout).
- Hàng ngàn agent của các tenant vẫn đang gửi tin nhắn và dùng AI chatbot bình thường.

Steps:
1. Giả lập Lago API down hoàn toàn trong 2 tiếng.
2. Các tenant tiếp tục thao tác tạo 10,000 billing events trong CRM.
3. Kiểm tra tính sẵn sàng của CRM API và bảng `billing_events_outbox`.

Expected Result:
- CRM API **KHÔNG ĐƯỢC CRASH** và không được làm gián đoạn luồng chat của người dùng.
- Toàn bộ 10,000 events được ghi an toàn vào DB cục bộ (`billing_events_outbox` với status `PENDING`).
- Background worker sử dụng Exponential Backoff + Circuit Breaker để tạm ngưng spam Lago API khi đang down.

Business Risk:
- Lỗi của 1 nhà cung cấp thứ ba (Billing vendor) làm sập toàn bộ hệ thống lõi CRM và trải nghiệm chat của khách hàng.

Automation Candidate: Yes (Fault Injection & Resilience Test)

---

## Test Case

ID: TC-OUT-002  
Priority: P0 (Blocker)  
Feature: Lago Recovery & Outbox Queue Drain  

Preconditions:
- 10,000 events đang bị ứ đọng trong `billing_events_outbox` do sự cố ở TC-OUT-001.

Steps:
1. Phục hồi Lago API hoạt động bình thường trở lại.
2. Background outbox worker phát hiện Lago đã healthy.

Expected Result:
- Worker tự động drain queue theo từng batch có kiểm soát rate limit.
- Toàn bộ 10,000 events được đẩy sang Lago thành công, chuyển status outbox sang `PROCESSED`.
- Số liệu usage trên Lago bắt kịp (catch-up) 100% với CRM, không bị mất bất kỳ 1 event nào.

Business Risk:
- Mất mát dữ liệu sau sự cố hạ tầng, không thể khôi phục lại lịch sử sử dụng để tính tiền.

Automation Candidate: Yes (Recovery Automation Test)

---

## 18. Payment Provider Outage

## Test Case

ID: TC-OUT-003  
Priority: P0 (Blocker)  
Feature: Stripe API Outage during Recurring Renewal Runs  

Preconditions:
- Đến giờ chạy batch gia hạn subscription tự động của 1,000 tenants.
- Stripe API bị timeout / trả về lỗi 5xx.

Steps:
1. Trigger job gia hạn subscription.
2. Mô phỏng Stripe API bị lỗi toàn diện.

Expected Result:
- Hệ thống bắt lỗi `StripeConnectionException` / `StripeAPIException`.
- Không đánh dấu bừa bãi các subscription là `FAILED` hay `SUSPENDED`.
- Giữ nguyên trạng thái `ACTIVE` cho khách hàng, ghi log lỗi và đưa các transaction này vào retry queue sau 2 giờ.
- Gửi alert khẩn cấp tới kênh Slack/PagerDuty của đội SRE.

Business Risk:
- Khóa nhầm hàng ngàn khách hàng VIP đang trả phí chỉ vì lỗi mạng tạm thời của Stripe.

Automation Candidate: Yes (Integration Fault Injection)

---

## 19. Reconciliation

## Test Case

ID: TC-REC-001  
Priority: P0 (Blocker)  
Feature: Three-Way End-of-Day Financial Reconciliation (CRM vs Lago vs Stripe)  

Preconditions:
- Dữ liệu hoạt động trong ngày của 100 tenants:
  - CRM DB ghi nhận: 1,000,000 messages.
  - Lago ghi nhận: 1,000,000 metered units.
  - Stripe xử lý: 150 invoices thành công với tổng số tiền $45,000.

Steps:
1. Chạy reconciliation batch job cuối ngày: `DailyThreeWayReconJob`.
2. Đối soát:
   - CRM Outbox Events vs Lago Metered Usage.
   - Lago Finalized Invoices vs Stripe Invoices/Charges.

Expected Result:
- Báo cáo khớp 100% (Zero Discrepancy).
- Hệ thống sinh file audit report `recon-2026-08-26.json` với trạng thái `MATCHED`.
- Không có orphan invoice hoặc missing payment.

Business Risk:
- Không phát hiện được việc thất thoát doanh thu hoặc tính dư tiền kéo dài nhiều ngày cho đến khi khách hàng kiểm toán.

Automation Candidate: Yes (Data Pipeline & Reconciliation Test)

---

## Test Case

ID: TC-REC-002  
Priority: P0 (Blocker)  
Feature: Anomaly Detection - Identifying Missing Events & Amount Mismatches  

Preconditions:
- Cố tình chèn lỗi dữ liệu:
  - CRM có 1,000 events gửi tin nhắn nhưng do lỗi mạng chỉ có 950 events sang được Lago (Lệch 50 events).
  - 1 hóa đơn trên Lago trị giá $100 nhưng trên Stripe chỉ charge $90.

Steps:
1. Thực thi `DailyThreeWayReconJob`.

Expected Result:
- Job phát hiện chính xác:
  - Cảnh báo `MISSING_LAGO_EVENTS`: 50 events cụ thể kèm theo danh sách `referenceId`.
  - Cảnh báo `AMOUNT_MISMATCH`: Invoice #INV-9901 lệch $10.
- Tự động sinh ticket kiểm toán cho Finance/DevOps team kèm chi tiết nguyên nhân.
- Không tự ý silent-fail hoặc bỏ qua sự chênh lệch.

Business Risk:
- Lệch sổ sách kế toán, rủi ro pháp lý kiểm toán doanh nghiệp khi IPO hoặc thanh tra tài chính.

Automation Candidate: Yes (Automated Anomaly Injection Test)

---

## 20. Race Conditions

## Test Case

ID: TC-RAC-001  
Priority: P0 (Blocker)  
Feature: Simultaneous Plan Upgrade + Add-on Purchase + Automated Invoice Finalization  

Preconditions:
- Tenant đang có subscription chuẩn bị kết thúc chu kỳ lúc 23:59:59.

Steps:
1. Tại thời điểm đúng 23:59:59, kích hoạt đồng thời 3 luồng:
   - Luồng 1: Admin thực hiện Upgrade plan (`Basic` -> `Pro`).
   - Luồng 2: Admin thứ 2 thực hiện Mua Add-on 5 Extra Agents.
   - Luồng 3: Lago Cron Engine chạy ngầm tự động finalize invoice của chu kỳ cũ.

Expected Result:
- Cơ chế Lock cấp Subscription ngăn chặn xung đột dữ liệu.
- Các thao tác được sắp xếp tuần tự (Serialized execution) hoặc thao tác sau chờ thao tác trước hoàn thành.
- Hóa đơn chu kỳ cũ được chốt đúng với số liệu trước upgrade.
- Giao dịch Upgrade và Add-on được tính tiền chính xác cho chu kỳ mới.
- Không phát sinh double billing, không xảy ra deadlock database.

Business Risk:
- Deadlock server, crash worker hoặc tính sai tiền hóa đơn do đọc dữ liệu bẩn (Dirty Read / Phantoms).

Automation Candidate: Yes (High-Stress Concurrency Test)

---

## Test Case

ID: TC-RAC-002  
Priority: P1 (Critical)  
Feature: Concurrent Seat / Agent Addition Exceeding Plan Quota  

Preconditions:
- Gói hiện tại của Tenant chỉ còn trống đúng 1 chỗ (Available Seats: 1).

Steps:
1. Hai quản trị viên cùng lúc bấm "Thêm Agent mới" tại cùng một giây từ 2 trình duyệt khác nhau.

Expected Result:
- Sử dụng Database Pessimistic/Optimistic Locking:
  - Admin A thêm thành công (Available seats trở về 0).
  - Admin B nhận thông báo lỗi HTTP `409 Conflict` / `QUOTA_EXCEEDED` yêu cầu mua thêm seats.
- Tuyệt đối không cho phép tạo 2 agents vượt quá hạn mức tối đa của gói.

Business Risk:
- Khách hàng lách luật dùng vượt quota mà không phải trả tiền mua thêm ghế (Seat expansion revenue leakage).

Automation Candidate: Yes (Parallel API Automation Test)

---

## 21. Multi-Tenant Isolation

## Test Case

ID: TC-MTN-001  
Priority: P0 (Blocker)  
Feature: Strict Cross-Tenant Billing & Invoice Data Isolation  

Preconditions:
- Tenant A (`tenant_a`) có Invoice #INV-A01.
- Tenant B (`tenant_b`) có Invoice #INV-B01.

Steps:
1. Đăng nhập bằng tài khoản Admin của Tenant A.
2. Gửi request xem chi tiết hóa đơn: `GET /api/v1/billing/invoices/INV-A01` -> Kiểm tra kết quả.
3. Cố tình sửa URL để xem hóa đơn của Tenant B: `GET /api/v1/billing/invoices/INV-B01`.
4. Cố tình gọi API thanh toán hóa đơn của Tenant B bằng Payment Method của Tenant A.

Expected Result:
- Bước 2: Trả về chi tiết hóa đơn của Tenant A `200 OK`.
- Bước 3: Hệ thống từ chối truy cập ngay lập tức với HTTP `403 Forbidden` hoặc `404 Not Found`.
- Bước 4: Chặn đứng hành vi cross-tenant charging.
- Toàn bộ query database bắt buộc phải có điều kiện `WHERE tenant_id = :currentTenantId`.

Business Risk:
- Lỗ hổng IDOR nghiêm trọng làm lộ thông tin tài chính, địa chỉ công ty, số tiền thanh toán của doanh nghiệp khác.

Automation Candidate: Yes (Security Multi-Tenant Test Suite)

---

## Test Case

ID: TC-MTN-002  
Priority: P0 (Blocker)  
Feature: No Metering Leakage under High Interleaved Multi-Tenant Traffic  

Preconditions:
- Tenant A gửi 1,000 messages/giây.
- Tenant B gửi 500 messages/giây.
- Cả 2 luồng request chạy song song, xen kẽ liên tục vào chung một cụm CRM Ingestion Service.

Steps:
1. Bơm đồng thời 100,000 events từ cả 2 tenants.
2. Kiểm tra tổng kết metered events sau khi hoàn thành.

Expected Result:
- Toàn bộ 1,000 messages của Tenant A được gán chính xác `tenantId = tenant_a`.
- Toàn bộ 500 messages của Tenant B được gán chính xác `tenantId = tenant_b`.
- Không có bất kỳ event nào của Tenant A bị ghi nhầm vào tài khoản của Tenant B hoặc ngược lại (Zero cross-tenant contamination).

Business Risk:
- Doanh nghiệp này phải trả tiền cho lưu lượng sử dụng của doanh nghiệp khác, gây phá hủy uy tín và nguy cơ kiện tụng pháp lý.

Automation Candidate: Yes (Load & Isolation Test)

---

## 22. Security & Financial Integrity

## Test Case

ID: TC-SEC-001  
Priority: P0 (Blocker)  
Feature: Immutability of Finalized Invoices and Committed Billing Events  

Preconditions:
- Invoice #INV-2026-001 đã ở trạng thái `FINALIZED` hoặc `PAID`.
- Billing Event `MSG_999` đã được commit vào ledger.

Steps:
1. Hacker cố tình gửi request `PUT /api/v1/billing/invoices/INV-2026-001` để sửa `amount` từ $1,000 xuống $1.
2. Cố tình gửi request `DELETE /api/v1/billing/events/MSG_999`.
3. Kiểm tra cơ sở dữ liệu.

Expected Result:
- API trả về HTTP `405 Method Not Allowed` hoặc `400 Bad Request` (Không cung cấp endpoint sửa/xóa hóa đơn đã chốt).
- Database áp dụng trigger / rule ngăn chặn `UPDATE` trên bảng `billing_ledger` đã finalized (Append-only Ledger pattern).
- Dữ liệu tài chính giữ nguyên vẹn 100%.

Business Risk:
- Thao túng số liệu kế toán, gian lận nội bộ hoặc bị tấn công thay đổi dữ liệu doanh thu.

Automation Candidate: Yes (Security & Database Integrity Test)

---

## Test Case

ID: TC-SEC-002  
Priority: P0 (Blocker)  
Feature: Cryptographic Proof & Complete Audit Trail for Financial Actions  

Preconditions:
- Các thao tác: Nâng gói, Hoàn tiền, Issue Credit Note, Cập nhật phương thức thanh toán.

Steps:
1. Thực hiện lần lượt các thao tác tài chính trên.
2. Truy vấn bảng `billing_audit_logs`.

Expected Result:
- Mọi thao tác đều sinh bản ghi audit log chứa:
  - `actor_id` (User ID hoặc System Worker)
  - `ip_address`, `user_agent`
  - `action` (`PLAN_UPGRADED`, `REFUND_ISSUED`, ...)
  - `before_state` và `after_state` (JSON diff)
  - `timestamp` chuẩn UTC
- Log không thể bị xóa hoặc sửa bởi bất kỳ user nào qua API thông thường.

Business Risk:
- Không thể điều tra khi xảy ra gian lận tài chính hoặc tranh chấp khiếu nại với khách hàng.

Automation Candidate: Yes (Audit Trail Automation Test)

---

## 23. Disaster Recovery

## Test Case

ID: TC-DIS-001  
Priority: P0 (Blocker)  
Feature: Database / Redis Mid-Transaction Crash & ACID Rollback  

Preconditions:
- Hệ thống đang trong quá trình thực thi phân bổ quota và trừ tiền: Đã charge Stripe xong nhưng đang ghi dữ liệu vào PostgreSQL/MongoDB thì DB bị kill đột ngột (`SIGKILL`).

Steps:
1. Bơm request thanh toán và inject lệnh kill DB node chính xác tại transaction commit phase.
2. DB tự động failover sang Replica node.
3. CRM kết nối lại và kiểm tra tính toàn vẹn dữ liệu.

Expected Result:
- Transaction chưa commit đầy đủ trên DB cũ được rollback an toàn (Không lưu dữ liệu rác).
- Hệ thống phát hiện Stripe đã charge nhưng CRM chưa kích hoạt subscription nhờ cơ chế Outbox / Two-Phase Reconciliation.
- CRM tự động hoàn tất kích hoạt sau khi phục hồi kết nối DB (Eventual Consistency).

Business Risk:
- Mất mát dữ liệu giao dịch tài chính khi hạ tầng gặp sự cố phần cứng đột ngột.

Automation Candidate: Yes (Chaos Engineering Test với Chaos Mesh / Litmus)

---

## Test Case

ID: TC-DIS-002  
Priority: P1 (Critical)  
Feature: Network Partition (Split-Brain) Between CRM, Lago, and Stripe  

Preconditions:
- Giả lập rớt mạng hoàn toàn giữa Data Center của CRM và Stripe/Lago trong 30 phút.

Steps:
1. Cắt đứt kết nối mạng ra ngoài Internet từ CRM Worker.
2. Các tenant vẫn tương tác nội bộ.
3. Phục hồi lại mạng sau 30 phút.

Expected Result:
- CRM chuyển sang chế độ tự bảo vệ: Cho phép phục vụ tenant dựa trên cached entitlements trong Redis.
- Khi mạng phục hồi, background sync workers tự động đồng bộ lại toàn bộ trạng thái mà không gây xung đột dữ liệu hay duplicate request.

Business Risk:
- Treo toàn bộ hệ thống CRM chỉ vì mạng quốc tế chập chờn.

Automation Candidate: Yes (Chaos Network Partition Test)

---

## 24. Performance & Scale

## Test Case

ID: TC-PRF-001  
Priority: P1 (Critical)  
Feature: High-Scale Ingestion Benchmark (10,000 Tenants, 1M Events/Day, 100K Invoices/Month)  

Preconditions:
- Môi trường Staging/Stress Testing cấu hình tương đương Production.
- 10,000 tenants hoạt động đồng thời.

Steps:
1. Sử dụng K6 bắn tải 1,000,000 billing events phân bổ trong 1 ngày (đạt đỉnh 1,000 events/giây trong giờ cao điểm).
2. Chạy batch phát hành 100,000 hóa đơn cuối tháng.
3. Đo lường các chỉ số: Throughput, Latency p95/p99, CPU/Memory, Thời gian hoàn thành Reconciliation.

Expected Result:
- API Ingestion Latency: p95 < 50ms, p99 < 150ms.
- Zero dropped events (Tỷ lệ lỗi 0.00%).
- Batch Finalize 100,000 invoices hoàn thành trong vòng dưới 2 giờ mà không làm nghẽn các tác vụ API thông thường.
- Reconciliation job cho 1 triệu events hoàn tất trong dưới 15 phút.

Business Risk:
- Hệ thống bị quá tải, nghẽn hàng đợi hóa đơn vào ngày cuối tháng khiến doanh nghiệp không thể thu tiền đúng hạn từ khách hàng.

Automation Candidate: Yes (Continuous Performance & Benchmark Suite)

---

## MA TRẬN PHÂN LOẠI & CHIẾN LƯỢC TỰ ĐỘNG HÓA (AUTOMATION STRATEGY)

| Nhóm Kiểm Thử | Tổng số Test Cases | Tỷ lệ P0 (Blocker) | Công cụ / Framework đề xuất | Tần suất chạy |
| :--- | :---: | :---: | :--- | :--- |
| **Core Lifecycle (1-7)** | 18 | 70% | Vitest / Supertest (API), Playwright (E2E) | Mỗi Pull Request & Nightly |
| **Payment, Invoice & Credit (8-11)** | 9 | 80% | Stripe Mock, WireMock (Lago), Precision Math Units | Mỗi Pull Request |
| **Metering & Idempotency (12-14)** | 6 | 100% | Redis Lock Simulator, BullMQ In-memory test | Mỗi Pull Request |
| **Webhooks & Resilience (15-18)** | 8 | 90% | Webhook Delivery Simulator, WireMock Faults | Nightly & Pre-release |
| **Reconciliation & Auditing (19, 22)**| 4 | 100% | Scheduled Data Pipeline Checkers | Daily trên Staging / Prod |
| **Concurrency & Multi-Tenant (20, 21)**| 4 | 100% | K6, Parallel Thread Pool Workers | Pre-release / Weekly |
| **Disaster & Chaos (23)** | 2 | 50% | Chaos Mesh, Docker Compose Network Cutters | Monthly Staging Runs |
| **Performance & Scale (24)** | 1 | 0% | K6 Distributed Load Generators | Pre-release Major Versions |

---

## 25. Coupons & Promotional Discounts

## Test Case

ID: TC-CPN-001  
Priority: P1 (Critical)  
Feature: Percentage-Based & Fixed-Amount Coupon Application  

Preconditions:
- Coupon `PROMO20` (Giảm 20% vĩnh viễn).
- Coupon `WELCOME50` (Giảm $50 cho tháng đầu tiên - Once).
- Gói mua: `Pro Monthly` ($100/tháng).

Steps:
1. Tenant áp dụng coupon `PROMO20` khi checkout gói Pro.
2. Kiểm tra số tiền charge kỳ đầu và các kỳ gia hạn tiếp theo.
3. Tenant khác áp dụng coupon `WELCOME50` khi checkout gói Pro.
4. Kiểm tra kỳ đầu và kỳ gia hạn thứ 2 của Tenant 2.

Expected Result:
- Tenant 1: Kỳ đầu charge $80.00; Các kỳ tiếp theo charge đều đặn $80.00.
- Tenant 2: Kỳ đầu charge $50.00 ($100 - $50); Kỳ thứ 2 tự động quay về giá gốc $100.00.
- Line items hiển thị rõ ràng discount và tax được tính trên giá sau giảm (Subtotal after discount).

Business Risk:
- Coupon dùng 1 lần (Once) tiếp tục áp dụng cho các tháng sau gây thất thoát doanh thu định kỳ.

Automation Candidate: Yes (API Integration Test)

---

## Test Case

ID: TC-CPN-002  
Priority: P1 (Critical)  
Feature: Expired / Invalid / Stacked Coupon Validation  

Preconditions:
- Coupon `SUMMER2025` đã hết hạn vào ngày 2025-08-31.

Steps:
1. Tenant cố tình nhập coupon `SUMMER2025` khi checkout.
2. Tenant cố tình áp dụng cùng lúc 2 coupon `PROMO20` và `WELCOME50` vào 1 đơn hàng.

Expected Result:
- Bước 1: Hệ thống từ chối với HTTP `422 Unprocessable Entity` kèm lỗi `COUPON_EXPIRED`.
- Bước 2: Hệ thống từ chối xếp chồng coupon (No coupon stacking), chỉ chấp nhận 1 coupon có giá trị tốt nhất hoặc coupon nhập sau cùng.

Business Risk:
- Khách hàng lách luật xếp chồng nhiều voucher để giảm giá 100% hoặc làm giá đơn hàng bị âm.

Automation Candidate: Yes (API Validation Test)

---

## 26. Payment Method Management & Off-Session 3DS

## Test Case

ID: TC-PMD-001  
Priority: P0 (Blocker)  
Feature: Default Payment Method Switch & Old Card Deletion  

Preconditions:
- Tenant có Card A là Default Payment Method đang gắn với Subscription.

Steps:
1. Tenant thêm Card B mới vào ví thanh toán và chọn làm "Default".
2. Tenant thực hiện xóa Card A khỏi hệ thống.
3. Kích hoạt chu kỳ gia hạn tiếp theo.

Expected Result:
- Card B trở thành nguồn thanh toán mặc định trên Stripe Customer.
- Xóa Card A thành công vì tài khoản vẫn còn Card B làm backup.
- Nếu tenant chỉ có 1 thẻ duy nhất đang gánh active subscription, hệ thống từ chối xóa thẻ trừ khi họ hủy subscription trước.
- Chu kỳ gia hạn tiếp theo tự động charge thành công trên Card B.

Business Risk:
- Khách xóa sạch thẻ đang trả phí khiến chu kỳ sau không thể auto-debit, hệ thống rơi vào trạng thái nợ xấu (Bad Debt).

Automation Candidate: Yes (API Integration Test)

---

## Test Case

ID: TC-PMD-002  
Priority: P0 (Blocker)  
Feature: Off-Session Recurring 3DS Challenge (Merchant-Initiated Transaction - MIT)  

Preconditions:
- Ngân hàng phát hành thẻ yêu cầu xác thực 3DS đột xuất cho giao dịch gia hạn tự động chạy ngầm lúc 03:00 sáng (Off-session charge).

Steps:
1. Stripe kích hoạt recurring payment intent off-session.
2. Ngân hàng trả về trạng thái `requires_action` (3DS SCA required).
3. Stripe gửi webhook `invoice.payment_action_required`.

Expected Result:
- CRM không đánh dấu giao dịch là `FAILED` ngay lập tức.
- CRM gửi email khẩn cấp chứa link bảo mật tới trang xác thực 3DS của Stripe (`hosted_invoice_url` / `payment_intent.client_secret`).
- Cho phép khách hàng 3 ngày (Grace Period) để click link và hoàn tất 3DS trên điện thoại.
- Sau khi khách pass 3DS, invoice chuyển sang `PAID` và subscription duy trì `ACTIVE`.

Business Risk:
- Tự động hủy gói của khách hàng VIP khi ngân hàng của họ yêu cầu bảo mật 2 lớp 3DS cho giao dịch định kỳ.

Automation Candidate: Yes (Webhook & 3DS Off-Session Flow Test)

---

## 27. Sequential Invoice Numbering, Internal Compliance & Multi-Currency

## Test Case

ID: TC-SEQ-001  
Priority: P1 (Critical)  
Feature: Continuous Sequential Invoice Numbering & Multi-Currency Isolation [BR-18.6, BR-18.7, BR-25.1-25.4]  

Preconditions:
- Tenant doanh nghiệp sử dụng tiền tệ USD (`tenant_us`).
- Tenant doanh nghiệp sử dụng tiền tệ SAR (`tenant_sa`).
- Tenant doanh nghiệp sử dụng tiền tệ VND (`tenant_vn`).

Steps:
1. Phát hành hóa đơn kỳ mới cho từng tenant.
2. Kiểm tra định dạng số hóa đơn, tính liên tục của dãy số và định dạng tiền tệ.
3. Hủy một bản nháp hóa đơn (Draft cancellation) và kiểm tra số tiếp theo.

Expected Result:
- Mỗi hóa đơn nhận được số hóa đơn liên tục, không trùng lặp (ví dụ: `INV-2026-000041`, `INV-2026-000042`).
- Bản nháp bị hủy: số hóa đơn đã cấp bị hủy cùng và KHÔNG được tái sử dụng (BR-18.6). Hóa đơn tiếp theo nhận số kế tiếp (`INV-2026-000044`).
- Tiền tệ gắn chặt với Billing Account: VND không có phần thập phân (0 decimals), USD/SAR có 2 chữ số thập phân (BR-18.10, BR-25.1). Không có quy đổi tự động tỷ giá làm trôi số tiền phải trả (BR-25.2).

Business Risk:
- Trùng số hóa đơn hoặc nhảy khoảng vô căn cứ vi phạm tính toàn vẹn kiểm toán nội bộ.

Automation Candidate: Yes (Sequential Engine Integration Test)

---

## 28. Enterprise Custom Contracts & Net-D Invoicing

## Test Case

ID: TC-ENT-001  
Priority: P1 (Critical)  
Feature: Enterprise Offline Bank Transfer with Net-30 Payment Terms  

Preconditions:
- Khách hàng Enterprise ký hợp đồng $50,000/năm với điều khoản thanh toán chuyển khoản ngân hàng sau 30 ngày (Net-30).

Steps:
1. Admin tạo Subscription Enterprise trên CRM Manager với `payment_method: bank_transfer`, `payment_terms: net_30`.
2. Hệ thống phát hành hóa đơn ở trạng thái `OPEN` với `due_date = issuance_date + 30 days`.
3. Cấp quyền truy cập Enterprise đầy đủ cho khách hàng ngay lập tức.
4. Kế toán nhận tiền ngân hàng sau 20 ngày và thực hiện đối soát thủ công (`POST /api/v1/billing/invoices/{id}/mark-paid`).

Expected Result:
- Khách hàng sử dụng dịch vụ bình thường trong 30 ngày chờ thanh toán.
- Sau khi kế toán xác nhận khớp lệnh chuyển khoản, invoice chuyển sang `PAID`.
- Nếu quá 30 ngày chưa nhận được tiền, hệ thống gửi email cảnh báo quá hạn (Overdue) và sau 45 ngày tự động chuyển sang `SUSPENDED`.

Business Risk:
- Khách hàng Enterprise không có thẻ tín dụng bị hệ thống tự động khóa tài khoản sau 1 ngày do nhầm lẫn với luồng thẻ thông thường.

Automation Candidate: Yes (Enterprise Workflow E2E Test)

---

## 29. Financial Compliance, Audit & Data Archival

## Test Case

ID: TC-CMP-001  
Priority: P0 (Blocker)  
Feature: 7-Year Immutable Financial Data Retention & GDPR Anonymization  

Preconditions:
- Tenant yêu cầu xóa tài khoản vĩnh viễn theo quyền "Right to be Forgotten" (GDPR).

Steps:
1. Admin kích hoạt luồng `GDPR Erasure Request` cho Tenant.
2. Kiểm tra database CRM, Lago, Stripe và Audit Logs.

Expected Result:
- Dữ liệu định danh cá nhân (PII): Tên, email, số điện thoại, tin nhắn chat được ẩn danh hóa (Anonymized / Pseudonymized) thành `anon_user_xxx@deleted.local`.
- **DỮ LIỆU TÀI CHÍNH BẮT BUỘC ĐƯỢC GIỮ NGUYÊN:** Toàn bộ Invoice records, Transaction IDs, Billing Events hashes, Credit Notes vẫn được lưu trữ bất biến (Immutable) phục vụ kiểm toán tài chính 7-10 năm theo luật định.
- Không thể đảo ngược hoặc xóa cứng các bảng tài chính.

Business Risk:
- Vi phạm luật kiểm toán tài chính nếu xóa hóa đơn, hoặc vi phạm luật GDPR nếu không ẩn danh hóa thông tin cá nhân.

Automation Candidate: Yes (Database Compliance & Privacy Test)

---

## 30. Test Data Matrix & Gateway Fixtures

### 30.1. Stripe Test Card Numbers Fixture Catalog

| Thẻ Test Stripe | Mục Đích Kiểm Thử | Hành Vi Kỳ Vọng |
| :--- | :--- | :--- |
| `pm_card_visa` | Happy Path Thanh Toán Thường | Trả về status `succeeded` tức thì. |
| `pm_card_authenticationRequiredOnSetup` | Bắt buộc 3DS Challenge | Trả về status `requires_action` (3DS popup). |
| `pm_card_chargeCustomerFail` | Thẻ bị từ chối chung (Card Declined) | Trả về mã lỗi `generic_decline` HTTP 400. |
| `pm_card_insufficientFunds` | Tài khoản không đủ số dư | Trả về mã lỗi `insufficient_funds` -> Kích hoạt Dunning. |
| `pm_card_expiredCard` | Thẻ đã hết hạn | Trả về mã lỗi `expired_card` -> Nhắc đổi thẻ mới. |
| `pm_card_processingError` | Lỗi mạng cổng thanh toán | Trả về lỗi `processing_error` -> Kích hoạt Exponential Retry. |

### 30.2. Lago Mock Event Schemas

```json
{
  "transaction_id": "evt_msg_template_20260826_001",
  "external_customer_id": "tenant_acme_corp",
  "code": "message_template_sent",
  "timestamp": 1771977600,
  "properties": {
    "units": 1,
    "channel": "whatsapp",
    "country_code": "VN"
  }
}
```
