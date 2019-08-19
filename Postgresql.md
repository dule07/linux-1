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
sudo systemctl restart postgresql-11.service
systemctl enable postgresql-11.service
```
### Cấu hình listen trong file `/var/lib/pgsql/11/data/postgresql.conf`
```
/var/lib/pgsql/11/data/postgresql.conf 
```
### Truy cập Postgre. 1 User postgres của OS dùng để truy cập database, 1 User postgres của PostgreSQL dùng để thực hiện các query lên database 
```
su - postgres
psql postgres
```
### Đặt mật khẩu cho user. Nếu muốn Client phải điền mật khẩu thì điền *md5*. Nếu muốn Client không phải điền mật khẩu thì điền *trust*
```
ALTER USER Postgres WITH PASSWORD 'password';
[root@localhost ~]# vim /var/lib/pgsql/11/data/pg_hba.conf
local   all             all                                     md5
host    all             all             127.0.0.1/32            trust
host    all             all             ::1/128                 trust
host    all             all             192.168.108.0/24        md5
```
### Tạo, xóa database/table
```
create database hello with owner postgres ;
\l #Hiện danh sách database
\c hello # kết nối tới database hello
CREATE TABLE test (id int, first_name varchar, last_name varchar); 
\dt #Hiện danh sách table
INSERT INTO test VALUES (1, 'ABC', '123');
select * from test;
drop table test; #Xóa tables
drop database hello; # Xóa database
```
### Tạo user, gán quyền, đổi mật khẩu
```
create user test with password 'test123';
grant all privileges on database mydb to test;
alter user myuser with password 'hoangha1908';
```
## Backup database
```
pg_dump -U <DB Username> <DB Name> > backup.sql
```
