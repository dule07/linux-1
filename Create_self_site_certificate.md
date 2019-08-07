# Hướng dẫn tạo certificate 
 - Bước 1: Cài đặt opensll
 ```
 yum install mod_ssl openssl  
  ```
- Bước 2: Tạo private key (.key) và certificate (.csr). 
```
[root@localhost ~]#  openssl req -newkey rsa:2048 -nodes -keyout private.key -x509 -days 365 -out certificate.csr
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
  * Trong ddos
- Bước 3: Review lại certificate
```
openssl x509 -text -noout -in certificate.pem
```
