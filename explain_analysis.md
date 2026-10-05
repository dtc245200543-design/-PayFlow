# Phân tích EXPLAIN trước và sau tối ưu

Truy vấn cũ sử dụng `YEAR(created_at)` và `MONTH(created_at)` trong điều kiện `WHERE`. Đây là truy vấn Non-SARGable vì hàm được áp dụng trực tiếp lên cột `created_at`, khiến MySQL khó sử dụng B-Tree Index để tìm kiếm theo khoảng thời gian. Vì vậy, kế hoạch thực thi có thể xuất hiện `type = ALL`, nghĩa là quét toàn bộ bảng Transactions.

Để tối ưu, tạo Composite Index:

`idx_type_date(transaction_type, created_at)`

Sau đó thay điều kiện YEAR/MONTH bằng khoảng thời gian:

`created_at >= '2026-06-01 00:00:00' AND created_at < '2026-07-01 00:00:00'`

Cách viết này SARGable và cho phép MySQL sử dụng Index để tìm phạm vi dữ liệu phù hợp. Trong EXPLAIN sau tối ưu, cột `possible_keys` và `key` có thể xuất hiện `idx_type_date`, trong khi `type` thường chuyển sang `range` hoặc dạng truy cập chỉ mục phù hợp. Giá trị `rows` cũng giảm đáng kể so với việc quét toàn bộ bảng.

Kết quả là lượng dữ liệu cần đọc giảm, giúp giảm CPU, thời gian truy vấn và tranh chấp tài nguyên.
