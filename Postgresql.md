# Cài đặt, cấu hình Postgre
## Cài đặt, cấu hình ban đầu
### Cài đặt phần mềm
```
rpm -Uvh https://yum.postgresql.org/11/redhat/rhel-7-x86_64/pgdg-centos11-11-2.noarch.rpm
yum install postgresql11-server postgresql11-contrib postgresql11-devel
```
### khởi tạo thư mục dữ liệu và các tập tin cấu hình khác trên hệ thống của PostgreSQL
```
/usr/pgsql-11/bin/postgresql-11-setup initdb
```
### Khởi động service
```
sudo systemctl start postgresql-11.service
systemctl enable postgresql-11.service
```
### Truy cập Postgre
```
su - postgres
psql postgres
```
