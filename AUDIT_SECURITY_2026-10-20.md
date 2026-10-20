# VIETLIST — kiểm tra an toàn 2026-10-20

Nhánh backup: backup-vietlist-20261020. Không chỉnh sửa main hoặc dữ liệu Supabase.

## Phát hiện từ index.html
- 8 tên hàm định nghĩa lặp: openM, closeM, openAccount, cloudLogout, refreshOnlinePosts, openOnlineDetail, showMyOnlinePosts, realPublishPost.
- Có cả localStorage và Supabase cho bài đăng / yêu thích. Cần xác định đường xử lý đang được sử dụng trước khi bỏ bất cứ hàm nào.
- Giao diện có thao tác sửa/ẩn/xóa bài qua Supabase; việc kiểm soát quyền bắt buộc phải ở Supabase RLS, không chỉ ở JavaScript.
- Các thao tác Admin gọi RPC; cần kiểm tra quyền thực thi RPC và kiểm tra admin trên máy chủ.
- Mã chứa Supabase publishable key, đây là loại khóa dành cho frontend; tuyệt đối không đưa service_role key vào HTML.

## Checklist an toàn trước khi triển khai
1. Xuất schema và policies của posts, post_images, vl_user_favorites, reports, profiles, storage.objects; đối chiếu auth.uid() với chủ sở hữu.
2. Kiểm tra RLS đang bật trên mọi bảng công khai có dữ liệu người dùng; thử tài khoản A sửa/xóa bài B (phải bị từ chối).
3. Kiểm tra RPC quản trị xác minh quyền ở database, không tin tham số user_id từ trình duyệt.
4. Kiểm tra người dùng bị khóa không thể tạo bài bằng REST trực tiếp.
5. Kiểm tra đồng bộ đăng nhập/đăng xuất, yêu thích và bài đăng giữa hai thiết bị.
6. Chạy thử UI và hồi quy chức năng rồi mới gỡ bỏ định nghĩa hàm lặp.

Không tự động áp dụng SQL chưa được kiểm tra vào production. Không xóa dữ liệu.
