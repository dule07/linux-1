# OPENVPN
## Cài đặt
### Tải source
```
yum install epel-release -y
yum install openssl pam openssl-devel  pam-devel net-tools gcc cmake -y
wget http://www.oberhumer.com/opensource/lzo/download/lzo-2.10.tar.gz
wget https://build.openvpn.net/downloads/releases/openvpn-2.3.18.tar.gz
wget https://build.openvpn.net/downloads/releases/easy-rsa-2.3.3_master.tar.gz
```
### Cài đặt phần mềm
```
tar -xvzf lzo-2.10.tar.gz
cd lzo-2.10
./configure (kiểm tra các thư viện đã đủ chưa)
make (thực hiện biên dịch)
make install (thực hiện cài đặt)

tar -xvzf openvpn-2.3.18.tar.gz
cd openvpn-2.3.18
./configure (kiểm tra các thư viện đã đủ chưa)
make (thực hiện biên dịch)
make install (thực hiện cài đặt)
```
### Tạo thư mục và chuyển vị trí tool easy 
Thư mục gốc sẽ là /etc/openvpn
```
mkdir /etc/openvpn
tar -xvzf easy-rsa-2.3.3_master.tar.gz
cp -r /root/easy-rsa-2.3.3_master/easy-rsa/ /etc/openvpn/
cd /etc/openvpn/easy-rsa/2.0/
mv * ../ (move toàn bộ file trong thư mục 2.0/ ra thư mục easy-rsa/)
cd /etc/openvpn/easy-rsa/  
mkdir keys (tạo thư mục /etc/openvpn/easy-rsa/keys để chứa keys, certificate)
vim /etc/openvpn/easy-rsa/vars (sửa các thông số mặc định hoặc có thể bỏ qua bước này, dùng thông số mặc định)
```
### Tạo CA Certificate Server và Key
Tạo  Root Cerificate
```
/etc/openvpn/easy-rsa
source vars
./clean-all #Chỉ chạy 1 lần đầu tiên. Nó sẽ xóa toàn bộ key trong thư mục keys
./pkitool --keysize 4096 --initca
```
Chạy xong sẽ sinh ra 2 file *ca.crt* và *ca.key* trong thư mục keys
Tạo Server Certificate
```
 ./build-key-server openvpnserver
 ```
Chạy xong sẽ sinh ra 3 file *openvpnserver.crt*, *openvpnserver.csr*, *openvpnserver.key* trong thư mục keys
Tạo Diffie Hellman ( DH ): băm keys
```
./build-dh
```
### Tạo Certificate và Key cho User (Mỗi user thì lặp lại quá trình này)
```
./build-key nguyen.hoang.ha
```
Chạy xong sẽ sinh ra 3 file *nguyen.hoang.ha.crt*, *nguyen.hoang.ha.csr*, *nguyen.hoang.ha.key* trong thư mục keys

