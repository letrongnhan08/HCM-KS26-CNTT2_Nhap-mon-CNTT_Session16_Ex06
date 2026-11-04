**Bài 6 : Kiểm tra và tối ưu hiệu năng Portal** 

**Phần 1\. Phân tích** 

- Nguyên nhân chính nút thắt cổ chai (bottleneck) làm chậm biểu đồ Waterfall :  
+ **Lỗi 404 Not Found:** Các dữ liệu ảnh bản đồ bị tô đỏ do đứt link,đường link lỗi cho dữ liệu ảnh không tồn tại portal.greenlog.vn tải lâu làm lãng phí thời gian của shipper.  
+ **Ảnh quá nặng:** File hình ảnh thô chưa nén tốn hết băng thông mạng, làm chậm quá trình xử lý CSS/JS. Khiến Rendering Engine không thể vẽ giao diện nếu chưa nhận đủ các file này với mã 200 OK.

**Phần 2\. Triển khai**

\- Quy trình 3 bước tối ưu hiệu năng:

1. **Sửa lỗi đứt link (404):** cập nhật lại đường dẫn ảnh bản đồ chính xác,Đảm bảo CSS/JS ra (Status 200 OK).  
2. **Nén tài nguyên hình ảnh:** giảm dung lượng file, chuyển sang định dạng tối ưu cho web như ảnh WebP hoặc AVIF để giảm dung lượng để thanh Waterfall không bị kéo dài.  
3. **Bật Browser Cache:** cấu hình máy chủ Web Server cho phép trình duyệt lưu lại các tài nguyên cache tĩnh (ảnh, CSS, JS) vào bộ nhớ đệm.

**Phần 3\. Kiểm thử** 

-Khi reload trang (tắt Disable cache) mà nhận mã 304 Not Modified:
- Giúp tiết kiệm băng thông tối đa thông qua yêu cầu xác thực nhỏ (có mã định danh như ETag hoặc If-Modified-Since) lên server, server sẽ chỉ phản hồi phần Header mà không truyền tải lại toàn bộ nội dung file
- Tăng tốc độ thời gian phản hồi (Response Time) hiển thị giúp tiết kiệm thời gian cho Shipper
- Giảm tải cho Server: Giảm lượng băng thông tiêu thụ và số lượng xử lý đồng thời trên máy chủ backend/CDN