# Cấu hình Nginx

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
