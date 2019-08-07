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

