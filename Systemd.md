# Systemd
## Khái niệm
- Bất cứ một chương trình nào trong linux đều cần được thực thi dưới dạng 1 một tiến trình, systemd cũng vậy
- Systemd cung cấp một chương trình đặc biệt /sbin/init và là chương trình đầu tiên khởi động trong hệ thống (PID=1)
- Và nó sẽ giữ vai trò kích hoạt các file cấu hình, chương trình khác trong hệ thống để hoàn tất công đoạn khởi động
## Thành phần
- systemctl: dùng để quản lý trạng thái các dịch vụ (start/stop/status ..)
- journald: dùng để quản lý nhật ký hoạt động của hệ thống (khi cần show log)
- logind, networkd ....
## Unit file
- Tất cả các chương trình được quản lý bởi systemd sẽ thực thi dưới dạng deamon/background và được cấu hình thành 1 file config gọi là unit file. Unit file gồm 12 loại
