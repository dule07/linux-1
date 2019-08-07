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
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header Host $http_host;
proxy_set_header X-Forwarded-Proto $scheme;
        }
```
## Cấu hình chạy SSL

