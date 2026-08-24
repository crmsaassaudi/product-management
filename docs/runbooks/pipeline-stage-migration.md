# Runbook — Đợt chuyển đổi Giai đoạn Cơ hội vào Pipeline

| | |
| --- | --- |
| **Loại tài liệu** | Runbook vận hành — điều kiện nghiệm thu, không phải hướng dẫn kỹ thuật |
| **Phạm vi** | MIG-04, MIG-05, MIG-06 của SRS Object Manager Mục 7.3 |
| **Issue** | [#54](https://github.com/crmsaassaudi/product-management/issues/54) (tách từ [#31](https://github.com/crmsaassaudi/product-management/issues/31)) |
| **Ngày cập nhật** | 2026-08-24 |

## Trạng thái

Phần **kỹ thuật** đã xong: cấu trúc dữ liệu mới, công cụ chuyển đổi có chế độ chạy thử và chế độ khôi phục, đối chiếu số liệu trước/sau (MIG-01, MIG-02, MIG-03), **đóng băng thao tác Cơ hội trong lúc ánh xạ (MIG-04)** và **bản đối chiếu Stage cũ → Stage mới theo từng tenant (MIG-06)**.

Phần **vận hành** đã nghiệm thu ở mức **cơ chế**: đợt diễn tập khôi phục đã chạy trên bản sao cấu hình thật của môi trường dev ngày 2026-08-24 — xem [biên bản MIG-05](./mig-05-rehearsal-record-2026-08-24.md). Lần diễn tập đó phát hiện **hai lỗi nghiêm trọng** trong công cụ ([#60](https://github.com/crmsaassaudi/product-management/issues/60)), cả hai đều để công cụ tự báo cáo là thành công; cả hai đã được xử lý và diễn tập lại sạch.

Còn lại là phần **theo từng tenant**, không phải phần cơ chế: đối chiếu trên dữ liệu Cơ hội thật của tenant đó và gửi bản đối chiếu cho Admin của họ. Xem *Điều kiện công bố hoàn tất*.

## MIG-04 — Hành vi hệ thống trong thời gian chuyển đổi

**Quyết định: chặn ghi, không cho ghi song song.**

Trong lúc công cụ ánh xạ Stage, mọi thao tác sửa Cơ hội — bao gồm chuyển giai đoạn — bị từ chối với thông báo nói rõ đây là việc theo kế hoạch và sẽ xong trong ít phút.

**Vì sao chặn chứ không cho ghi rồi hòa giải sau:** một thao tác chuyển giai đoạn rơi vào giữa cửa sổ ánh xạ được **ghi theo cấu trúc cũ** và **đọc theo cấu trúc mới**. Cơ hội khi đó trỏ tới một Stage mà Pipeline của nó không chứa — đúng thứ MIG-01 sinh ra để chứng minh là không thể xảy ra — và tệ hơn, phần đối chiếu số liệu sau đó vẫn **khớp**, vì tổng số Cơ hội và tổng giá trị không đổi. Sai lệch nằm ở một bản ghi và không ai phát hiện.

Không dùng cơ chế so sánh phiên bản để hòa giải: hai bên ghi **không thống nhất về hình dạng của bản ghi**, nên không có gì để so sánh.

**Cách vận hành:**

- Công cụ tự lấy khóa đóng băng trước khi ghi và **luôn** nhả khóa khi kết thúc, kể cả khi chạy lỗi. Một khóa bị bỏ quên sẽ khóa mọi Cơ hội của workspace, mà người nhận ra điều đó lại chính là khách hàng vừa được chạy migration.
- Chế độ `--verify` và `--dry-run` **không** lấy khóa: cả hai không ghi gì, và đóng băng một workspace chỉ để đọc là cái giá không đổi lấy điều gì.
- Truyền `--reason="dự kiến 15 phút"` để thông báo từ chối nói được thời lượng — người đặt khóa là người duy nhất biết con số đó.
- **Nghiệm thu bằng một thao tác ghi thật bị từ chối, không bằng dòng nhật ký "đã đóng băng".** *Đã đặt khóa* và *khóa có hiệu lực* là hai việc khác nhau: lần diễn tập đầu tiên cho thấy khóa được ghi vào một nơi hệ thống không đọc tới, nên nhật ký đủ cả hai dòng đặt và nhả khóa trong khi mọi thao tác chuyển giai đoạn vẫn ghi được suốt cửa sổ chuyển đổi.

**Cần chốt trước mỗi đợt:** khung giờ chạy (ngoài giờ làm việc của tenant, theo múi giờ của họ), và thời lượng cam kết để đưa vào `--reason`.

## MIG-05 — Diễn tập phương án khôi phục

**Đã diễn tập ngày 2026-08-24** trên bản sao cấu hình thật của môi trường dev. Biên bản: [`mig-05-rehearsal-record-2026-08-24.md`](./mig-05-rehearsal-record-2026-08-24.md).

Chạy bằng `npm run rehearse:pipeline-stages`. Bộ diễn tập sao workspace sang một cơ sở dữ liệu riêng, **chỉ đọc nguồn**, rồi gọi đúng công cụ mà đợt chạy thật sẽ gọi — chuyển đổi, rồi khôi phục — và **so khớp từng thuộc tính của từng Giai đoạn** với bản chụp lấy trước đó. Nó từ chối chạy nếu bản sao được trỏ vào chính cơ sở dữ liệu nguồn.

**Vì sao phải so khớp từng thuộc tính, không dựa vào đối chiếu số liệu tổng:** lần diễn tập đầu tiên khôi phục đủ 11 Giai đoạn, đối chiếu MIG-03 báo *khớp hoàn toàn*, và đã xóa vĩnh viễn bảy thuộc tính — trong đó có cờ *Giai đoạn còn dùng hay không*. Số liệu tổng không thể phát hiện điều đó, vì số Giai đoạn và số Cơ hội vẫn đúng.

**Biên bản diễn tập phải ghi:** ngày, môi trường và nguồn bản sao, số Cơ hội và tổng giá trị trước/sau khôi phục, kết quả so khớp từng thuộc tính, và mọi sai lệch cùng cách xử lý.

## MIG-06 — Thông báo cho Tenant Admin

Công cụ in **bản đối chiếu theo từng tenant**: mỗi Stage cũ đi về Pipeline nào, theo đúng thứ tự Admin đọc quy trình của mình — theo Pipeline, rồi theo thứ tự Stage bên trong.

Bản đối chiếu được lấy **trước** khi ánh xạ, vì sau đó `deal_stages` rỗng và ánh xạ không còn để dựng lại.

Đối chiếu tổng số liệu (MIG-03) chứng minh **không mất gì**. Nó không nói cho Tenant Admin biết điều gì đã đổi **với riêng họ** — và đó mới là thứ họ phải đối chiếu với quy trình bán hàng của mình trước khi đồng ý rằng đợt chuyển đổi đã xong.

**Cần làm trước mỗi đợt:** gửi từng Admin bản đối chiếu của chính tenant họ (không gửi bản gộp — bản gộp không gửi được cho ai), kèm khung giờ chạy và thời lượng dự kiến.

## Điều kiện công bố hoàn tất

- [x] **Diễn tập khôi phục trên bản sao, có biên bản (MIG-05)** — 2026-08-24, [biên bản](./mig-05-rehearsal-record-2026-08-24.md). Nghiệm thu **cơ chế**; phần theo từng tenant vẫn ở các mục dưới.
- [ ] Chạy `--verify` trên dữ liệu Cơ hội thật của tenant, duyệt sai lệch dự báo trọng số (MIG-03) — sai lệch chỉ được chấp nhận khi có một thay đổi xác suất đã được duyệt.
- [ ] Chạy lại bộ diễn tập trên bản sao dữ liệu **Cơ hội thật** của tenant đó.
- [ ] Gửi bản đối chiếu Stage cũ → Stage mới cho từng Tenant Admin và nhận xác nhận (MIG-06).
- [ ] Chạy thật trong khung giờ đã chốt, xác nhận khóa đã nhả sau khi chạy (MIG-04).
- [ ] Chạy `--verify` lại sau khi chạy thật, đối chiếu khớp.
