# Runbook — Đợt chuyển đổi Giai đoạn Cơ hội vào Pipeline

| | |
| --- | --- |
| **Loại tài liệu** | Runbook vận hành — điều kiện nghiệm thu, không phải hướng dẫn kỹ thuật |
| **Phạm vi** | MIG-04, MIG-05, MIG-06 của SRS Object Manager Mục 7.3 |
| **Issue** | [#54](https://github.com/crmsaassaudi/product-management/issues/54) (tách từ [#31](https://github.com/crmsaassaudi/product-management/issues/31)) |
| **Ngày cập nhật** | 2026-08-24 |

## Trạng thái

Phần **kỹ thuật** đã xong: cấu trúc dữ liệu mới, công cụ chuyển đổi có chế độ chạy thử và chế độ khôi phục, đối chiếu số liệu trước/sau (MIG-01, MIG-02, MIG-03), **đóng băng thao tác Cơ hội trong lúc ánh xạ (MIG-04)** và **bản đối chiếu Stage cũ → Stage mới theo từng tenant (MIG-06)**.

Phần **vận hành** chưa thể hoàn tất: hệ thống chưa golive nên **chưa có dữ liệu thật để chuyển và chưa có bản sao dữ liệu thật để diễn tập**. Đây là chốt chặn trước lần golive đầu tiên có dữ liệu Cơ hội thật, và trước mỗi đợt chuyển đổi của một tenant hiện hữu.

## MIG-04 — Hành vi hệ thống trong thời gian chuyển đổi

**Quyết định: chặn ghi, không cho ghi song song.**

Trong lúc công cụ ánh xạ Stage, mọi thao tác sửa Cơ hội — bao gồm chuyển giai đoạn — bị từ chối với thông báo nói rõ đây là việc theo kế hoạch và sẽ xong trong ít phút.

**Vì sao chặn chứ không cho ghi rồi hòa giải sau:** một thao tác chuyển giai đoạn rơi vào giữa cửa sổ ánh xạ được **ghi theo cấu trúc cũ** và **đọc theo cấu trúc mới**. Cơ hội khi đó trỏ tới một Stage mà Pipeline của nó không chứa — đúng thứ MIG-01 sinh ra để chứng minh là không thể xảy ra — và tệ hơn, phần đối chiếu số liệu sau đó vẫn **khớp**, vì tổng số Cơ hội và tổng giá trị không đổi. Sai lệch nằm ở một bản ghi và không ai phát hiện.

Không dùng cơ chế so sánh phiên bản để hòa giải: hai bên ghi **không thống nhất về hình dạng của bản ghi**, nên không có gì để so sánh.

**Cách vận hành:**

- Công cụ tự lấy khóa đóng băng trước khi ghi và **luôn** nhả khóa khi kết thúc, kể cả khi chạy lỗi. Một khóa bị bỏ quên sẽ khóa mọi Cơ hội của workspace, mà người nhận ra điều đó lại chính là khách hàng vừa được chạy migration.
- Chế độ `--verify` và `--dry-run` **không** lấy khóa: cả hai không ghi gì, và đóng băng một workspace chỉ để đọc là cái giá không đổi lấy điều gì.
- Truyền `--reason="dự kiến 15 phút"` để thông báo từ chối nói được thời lượng — người đặt khóa là người duy nhất biết con số đó.

**Cần chốt trước mỗi đợt:** khung giờ chạy (ngoài giờ làm việc của tenant, theo múi giờ của họ), và thời lượng cam kết để đưa vào `--reason`.

## MIG-05 — Diễn tập phương án khôi phục

**Chưa thực hiện được.** Cần một bản sao dữ liệu thật, mà hiện chưa tồn tại.

Công cụ khôi phục đã có (`--rollback`, đưa Stage trở lại `deal_stages`) và có kiểm thử, nhưng **chưa chạy trên bất kỳ môi trường nào có dữ liệu thật**. Một cơ chế khôi phục chưa từng được diễn tập là một cơ chế chưa ai biết có chạy hay không — và thời điểm phát hiện ra sẽ là lúc cần đến nó.

**Biên bản diễn tập phải ghi:** ngày, môi trường và nguồn bản sao, số Cơ hội và tổng giá trị trước/sau khôi phục, thời gian hoàn tất, và mọi sai lệch cùng cách xử lý.

## MIG-06 — Thông báo cho Tenant Admin

Công cụ in **bản đối chiếu theo từng tenant**: mỗi Stage cũ đi về Pipeline nào, theo đúng thứ tự Admin đọc quy trình của mình — theo Pipeline, rồi theo thứ tự Stage bên trong.

Bản đối chiếu được lấy **trước** khi ánh xạ, vì sau đó `deal_stages` rỗng và ánh xạ không còn để dựng lại.

Đối chiếu tổng số liệu (MIG-03) chứng minh **không mất gì**. Nó không nói cho Tenant Admin biết điều gì đã đổi **với riêng họ** — và đó mới là thứ họ phải đối chiếu với quy trình bán hàng của mình trước khi đồng ý rằng đợt chuyển đổi đã xong.

**Cần làm trước mỗi đợt:** gửi từng Admin bản đối chiếu của chính tenant họ (không gửi bản gộp — bản gộp không gửi được cho ai), kèm khung giờ chạy và thời lượng dự kiến.

## Điều kiện công bố hoàn tất

- [ ] Chạy `--verify` trên dữ liệu thật, duyệt sai lệch dự báo trọng số (MIG-03) — sai lệch chỉ được chấp nhận khi có một thay đổi xác suất đã được duyệt.
- [ ] Diễn tập khôi phục trên bản sao dữ liệu thật, có biên bản (MIG-05).
- [ ] Gửi bản đối chiếu Stage cũ → Stage mới cho từng Tenant Admin và nhận xác nhận (MIG-06).
- [ ] Chạy thật trong khung giờ đã chốt, xác nhận khóa đã nhả sau khi chạy (MIG-04).
- [ ] Chạy `--verify` lại sau khi chạy thật, đối chiếu khớp.
