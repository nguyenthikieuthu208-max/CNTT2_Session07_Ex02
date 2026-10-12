**Bài 2 : Phân tích Lỗi 404 từ File Log Máy chủ** 

**Phần 1: Phân tích sai lầm về bản chất câu lệnh tìm kiếm**

Sai lầm cốt lõi của An nằm ở việc nhầm lẫn giữa việc tìm kiếm tên tệp tin (Metadata) và tìm kiếm nội dung văn bản bên trong tệp tin (Content).

Bản chất của Get-ChildItem: Đây là lệnh dùng để liệt kê danh sách các tệp tin và thư mục đang nằm trong một đường dẫn cụ thể (tương tự lệnh dir của CMD hoặc ls của Linux). Khi An sử dụng tham số \-Filter "\*404\*", hệ điều hành sẽ quét xem có tệp tin nào có tên chứa chuỗi ký tự "404" hay không (ví dụ: log\_404.txt). Do các tệp tin trong hệ thống chỉ có tên thông thường như access.log, kết quả trả về trống rỗng là điều tất yếu. Lệnh này hoàn toàn không có khả năng "đi sâu vào bên trong" để đọc nội dung file.

Bản chất và lọc chuỗi ký tự bên trong nội dung của tệp tin (tương tự như lệnh findstr của CMD hoặc grep của Linux). Lệnh này sẽ mở tệp tin ra, quét qua từng dòng dữ liệu và trích xuất chính xác các dòng có chứa từ khóa mục tiêu (ERROR, 404) mà kỹ sư yêu cầu.

**Phần 2: Sửa lỗi và viết lại câu lệnh PowerShell chuẩn**

Để lọc toàn bộ các dòng chứa từ khóa "ERROR" trong tệp tin access.log và hiển thị kèm theo số thứ tự dòng để phục vụ tra cứu, An cần sử dụng câu lệnh Select-String theo cú pháp chuẩn dưới đây:

powershell  
Select-String \-Path "access.log" \-Pattern "ERROR"

Hãy thận trọng khi sử dụng mã.

* Giải thích cơ chế hoạt động:

  \-Path "access.log": Chỉ định tệp tin nhật ký mục tiêu cần quét dữ liệu.

  \-Pattern "ERROR": Khai báo từ khóa cốt lõi cần chắt lọc.

  Hiển thị số dòng: Mặc định, cấu trúc đầu ra của lệnh Select-String trong PowerShell cực kỳ thông minh. Nó sẽ tự động in kết quả ra màn hình theo định dạng chuẩn hóa: Tên\_file:Số\_dòng:Nội\_dung\_dòng\_chứa\_từ\_khóa (Ví dụ: access.log:1425: \[ERROR\] Page not found). Do đó, kỹ sư hệ thống có thể nhìn thấy ngay số dòng chính xác để mở file đối chiếu mà không cần cấu hình thêm cờ lệnh phức tạp nào khác.

