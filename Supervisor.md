# Supervisor là một công cụ quản lý các process chạy trên Linux
- Đảm bảo một process nào đó luôn luôn chạy. Nếu vì lý do gì mà nó bị tắt thì Supervisor sẽ khởi động lại process đó
- Tự động chạy 1 process khi khởi động hệ thống
- Quản lý nhiều process dưới dạng một group các process, có thể bật/tắt cùng lúc
# Cài đặt
```
yum install supervisor -y
```
# Cấu hình
```
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=/root/sendmail.sh
autostart=true
autorestart=true
user=forge
numprocs=8
redirect_stderr=true
stdout_logfile=/home/forge/app.com/worker.log
```
- [program:tên bất kì] đặt tên cho program
- command: Là path tới program cần chạy, ví dụ là /root/sendmail.sh
- autostart: Quy định process có tự chạy ngay khi Supervisor được khởi động hay không. Thường thì Supervisor sẽ khởi động cùng OS ==> process có chạy khi boot không
- autorestart: Quy định process có tự động khởi động ngay sau khi nó bị tắt vì lý do nào đó (đã chạy xong, bị lỗi, bị timeout ...) hay không
- user: User dùng để thực thì process
- numprocs: là số lượng tiến trình của cùng process đó. 
- redirect_stderr: Nếu set = true thì stderr sẽ xuất vào file được chỉ định bởi *stdout_logfile*
- stdout_logfile: Vị trí file log. Nếu process xuất log ra màn hình như lệnh echo thì giờ đây sẽ chỉ đẩy vào file log
# Note
- Có vẻ sẽ phù hợp cho việc chạy ứng dụng trong Docker
- Khi mà có nhiều service cần run thì ta có thể chỉ cần viết 1 script để run supervisor, còn supervisor sẽ run các service còn lại
