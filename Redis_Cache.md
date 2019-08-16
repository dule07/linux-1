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
Kiểm tra dịch vụ
```
[root@localhost ~]# redis-cli ping
PONG
```
