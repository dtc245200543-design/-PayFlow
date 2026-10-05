# AI Prompt Log

## Prompt 1 – Tìm hiểu Non-SARGable

Trong MySQL, nếu tôi tạo Index cho một cột ngày tháng nhưng trong WHERE lại sử dụng YEAR(created_at) và MONTH(created_at), tại sao MySQL khó sử dụng Index?

### Kết quả tìm hiểu

Khi sử dụng hàm trên cột được Index, MySQL phải tính toán giá trị của hàm trên nhiều dòng trước khi xác định dòng phù hợp. Điều này làm giảm khả năng sử dụng cơ chế tìm kiếm của B-Tree Index. Cách viết này được gọi là Non-SARGable.

---

## Prompt 2 – Tìm hiểu SARGable

SARGable trong SQL là gì và tại sao nó quan trọng đối với Index?

### Kết quả tìm hiểu

SARGable là cách viết điều kiện cho phép hệ quản trị cơ sở dữ liệu sử dụng Index để tìm kiếm dữ liệu hiệu quả. Với cột DATETIME, thay vì YEAR(created_at) = 2026 và MONTH(created_at) = 6, có thể sử dụng một khoảng:

created_at >= '2026-06-01 00:00:00'
AND created_at < '2026-07-01 00:00:00'

---

## Prompt 3 – Composite Index

Khi tạo Composite Index trên (transaction_type, created_at), thứ tự các cột có quan trọng không?

### Kết quả tìm hiểu

Có. Thứ tự các cột trong Composite Index ảnh hưởng đến khả năng sử dụng Index. Với truy vấn của bài, transaction_type được lọc bằng điều kiện bằng (=), sau đó created_at được lọc theo khoảng thời gian. Vì vậy Index:

(transaction_type, created_at)

phù hợp với truy vấn.

---

## Prompt 4 – EXPLAIN

Trong EXPLAIN, các giá trị ALL, range và ref có ý nghĩa gì?

### Kết quả tìm hiểu

ALL thường biểu thị Full Table Scan. MySQL phải đọc toàn bộ bảng để tìm dữ liệu phù hợp.

range biểu thị việc truy cập một phạm vi của Index, phù hợp với điều kiện khoảng thời gian.

ref biểu thị việc sử dụng Index để tìm các dòng dựa trên giá trị được tham chiếu.

---

## Prompt 5 – Index và dữ liệu thay đổi liên tục

Nếu bảng Transactions có nhiều INSERT, UPDATE và DELETE, việc tạo quá nhiều Index có rủi ro gì?

### Kết quả tìm hiểu

Index giúp SELECT nhanh hơn nhưng mỗi khi dữ liệu thay đổi, MySQL cũng phải cập nhật các Index liên quan. Quá nhiều Index có thể làm tăng thời gian INSERT, UPDATE, DELETE và sử dụng thêm dung lượng lưu trữ.
