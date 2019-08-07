# Hướng dẫn tạo certificate 
## Bước 1: Cài đặt opensll
 ```
 yum install mod_ssl openssl  
  ```
## Bước 2: Tạo private key (.key) và certificate (.csr) 
```
[root@localhost ~]#  sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout selfsigned.key -out selfsigned.crt
Generating a 2048 bit RSA private key
..................................................................+++
......................................................................+++
writing new private key to 'key.pem'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:VN
State or Province Name (full name) []:Hanoi
Locality Name (eg, city) [Default City]:Hanoi
Organization Name (eg, company) [Default Company Ltd]:Test
Organizational Unit Name (eg, section) []:Infra
Common Name (eg, your name or your server's hostname) []:
Email Address []:ha@test.com
```
 - req: Sử dụng chuẩn X509 cho Certificate signing request (CSR). X509 là 1 chuẩn public key mà SSL và TLS sử dụng
 - nodes: Báo cho openSSL bỏ qua passphrase. Nếu có pass thì khi webservice khởi động phải nhập pass vào
 - days 365: Thời hạn của chứng chỉ. Sau 1 năm thì chứng chỉ mất giá trị
 - newkey -rsa:2048: Tạo key luôn trong 1 lệnh và sử dụng mã hóa RSA 2048bit
 - keyout: Out của private key
 - out: Out của certificate 
## Bước 3: Review lại certificate
```
openssl x509 -text -noout -in certificate.pem
```
