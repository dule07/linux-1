# Redis Cache Server
## Lý thuyết
Là hệ thống lưu trữ dữ liệu trên Ram tương tự như Memcached. Hỗ trợ tốt nhiều dạng cấu trúc dữ liệu như keys/values, hash, list, set ...
## Cài đặt
### Để php có thể làm việc được với Redis cần cài thêm extension php
```
yum install epel-release
rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
yum --enablerepo=remi,remi-php71 install redis php-pecl-redis
service php-fpm restart
systemctl restart redis
```
Kiểm tra dịch vụ. Trả về `PONG` là ok
```
[root@localhost ~]# redis-cli ping
PONG
```
### Cấu hình
File config /etc/redis.conf
  - Bảo mật chỉ cho phép truy cập Redis bằng địa chỉ IP Loopback
```bind 127.0.0.1```
  - Thiết lập số lượng kết nối tối đa vào cùng 1 thời điểm, mặc định là 10000
```maxclients 10000```
  - Thiết lập giới hạn bộ nhớ tối đa cho Redis Server, ví dụ 1GB
```maxmemory 1024mb```

Thêm đoạn sau vào file sysctl để tránh bị crash server khi memory thấp
```
vim /etc/sysctl.conf
vm.overcommit_memory = 1
net.core.somaxconn = 65535
```
Chạy lệnh sau khi khởi động server. Chèn vào */etc/rc.local*
```
/usr/bin/echo never > /sys/kernel/mm/transparent_hugepage/enabled
```
### Đặt mật khẩu
Đặt mật khẩu trong file redis.conf
```
requirepass d32b7a32ee2a1f75689cdb147982a56654786b4daeb5d2b55a2acce135bbb9cf
```
Kiểm tra bằng cách ping thử, khi chưa nhập password sẽ báo lỗi 
```
redis-cli
127.0.0.1:6379> ping
(error) NOAUTH Authentication required.
127.0.0.1:6379> auth d32b7a32ee2a1f75689cdb147982a56654786b4daeb5d2b55a2acce135bbb9cf
OK
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> 
```
