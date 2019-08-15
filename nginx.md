# Cấu hình Nginx

## Cấu hình cơ bản 
```
    server {
        listen       80;
        server_name  test;

       access_log    /var/log/nginx/test_access.log  main;
       error_log    /var/log/nginx/test_error.log;
        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

    }
```

## Cấu hình truyền real IP client sang backend
 - Bước 1: Kiểm tra module realip đã có trong nginx chưa
 ```
 2>&1 nginx -V | tr -- - '\n' | grep http_realip_module
 ```
 - Bước 2: Chèn đoạn mã sau vào block location
```
location / {
proxy_pass      http://192.168.108.129;
set_real_ip_from 0.0.0.0/0;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header Host $http_host;
        }
```
- Bước 3: Phía Backend cần format lại log để hiện thị giá trị trong `X-Forwarded-For`
- Đối với backend là Nginx
```
   log_format specialLog '$remote_addr forwarded for $http_x_real_ip - $remote_user [$time_local]  '
                          '"$request" $status $body_bytes_sent '
                          '"$http_referer" "$http_user_agent" "$http_x_forwarded_for"';
```
-  Đối với backend là Apache
```
 LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\" %{X-Forwarded-For}i" combined
```
## Cấu hình chạy SSL
- Trong block server trỏ tới file private key và certificate
```
 server {
    listen       443;
    server_name test1.com.vn;

    ssl on;
    ssl_certificate /etc/nginx/ssl/selfsigned.crt;
    ssl_certificate_key /etc/nginx/ssl/selfsigned.key;
    
    location / {
proxy_pass      http://192.168.108.129;
set_real_ip_from 0.0.0.0/0;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header Host $http_host;
        }
   }
```
## Cấu hình nén dữ liệu
- Thêm vào trong block http. Nếu muốn site nào off thì ném `gzip off` vào trong block server
```
gzip on;
gzip_disable "msie6";    ## disable with ie6

gzip_vary on;
gzip_proxied any;
gzip_comp_level 6;
gzip_buffers 16 8k;
gzip_http_version 1.1;
gzip_min_length 256;
gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;
```
-- Kiểm tra xem site đã enable gzip chưa
```
[root@localhost ~]# curl -H "Accept-Encoding: gzip" -I http://192.168.108.128/
HTTP/1.1 403 Forbidden
Server: nginx/1.12.2
Date: Thu, 08 Aug 2019 02:29:45 GMT
Content-Type: text/html; charset=UTF-8
Connection: keep-alive
Vary: Accept-Encoding
Last-Modified: Thu, 16 Oct 2014 13:20:58 GMT
ETag: W/"1321-5058a1e728280"
**Content-Encoding: gzip**
```
## Cấu hình timeout đối với backend
```
proxy_connect_timeout       600;
proxy_send_timeout          600;
proxy_read_timeout          600;
send_timeout                600;
```
## Cấu hình cache file
- Thêm vào block `http`
```
proxy_cache_path /etc/nginx/cache levels=1:2    keys_zone=STATIC:10m    inactive=24h  max_size=1g loader_threshold=300 loader_files=200;
```
 - Mỗi request sẽ có 1 key tương ứng
 - Thêm vào block `location` của site
```
proxy_cache STATIC;
proxy_cache_valid 200 302 10m;
proxy_cache_valid 404      1m;
proxy_cache_valid any 5m;
```
## Cấu hình redirect
- Chèn đoạn sau vào block server port 80 để redirect sang https 443
```
return 301 https://$server_name$request_uri;
```

## Cấu hình làm loadbalancer
```
upstream mywebapp1 {
    server x.x.x.x;
    server y.y.y.y;
}

 server {
        listen       80;
        server_name  test1.com.vn;
        access_log /var/log/nginx/test_access.log specialLog;
        error_log /var/log/nginx/test_error.log;

        location / {
        proxy_pass     http://mywebapp1;
        }
}
```
 - Round robin: Mặc định (không còn thêm gì)
 - Least Connections: Request tiếp theo sẽ đẩy cho Server có số active connection thấp nhất (least_conn;)
 - ip hash: Dựa vào IP client (3 octet để xác minh). Đảm bảo cùng client sẽ đẩy vào đúng server (ip_hash;)
 - Còn vài thuật toán nữa
 - Để maintain server backend thì thêm `down` (server backend3.example.com down;)
 - Thêm `weight` để đặt trọng số đối với round robin (server backend1.example.com weight=5;)

## Cài đặt và cấu hình với php-fpm
```
yum install epel-release
rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
rpm -Uvh http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
```
