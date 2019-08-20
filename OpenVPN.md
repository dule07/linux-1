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
![img](images/keysopenvpn.PNG)
## Cấu hình port forwarding. Dùng để Lan Routing
Thêm tham số sau vào sysctl
```
vim /etc/sysctl.conf
net.ipv4.ip_forward = 1
sysctl -p (để cho các thông số có hiệu lực)
```
## Cấu hình server
 - Copy file mẫu
```
cp /root/openvpn-2.3.18/sample/sample-config-files/server.conf /etc/openvpn/
```
- Sửa cấu hình
```
vim /etc/openvpn/server.conf 
local 192.168.1.200 (chọn card mạng user quay VPN đến, có thể không cần option này)
port 1194 (OpenVPN sẽ listen ở port này)
proto udp (protocol udp)
dev tun (dùng tunnel, nếu dùng theo bridge chọn dev tap0 và những config khác sẽ khác với tunnel)
ca /etc/openvpn/easy-rsa/keys/keys/ca.crt 
cert /etc/openvpn/easy-rsa/keys/openvpnserver.crt
key /etc/openvpn/easy-rsa/keys/openvpnserver.key
dh /etc/openvpn/easy-rsa/keys/dh1024.pem
server 10.8.0.0 255.255.255.0 (khai báo dãy IP cần cấp cho VPN Client, mặc định VPN Server sẽ lấy .1)
push “route 172.16.0.0 255.255.255.0” (lệnh này sẽ đẩy route mạng 172.16.0.0 đến Client)
client-config-dir ccd (dùng để khai báo cấp IP tĩnh cho VPN Client - Tùy chọn)
client-to-client (cho phép các VPN client nhìn thấy nhau, mặc định client chỉ thấy server - Tùy chọn)
push “redirect-gateway” (mọi traffic của VPN Client – http, dns, ftp, … đều thông qua đuờng Tunnel. 
Khác với lệnh push route, chỉ những traffic đi vào mạng nội bộ mới thông qua Tunnel, khi dùng lệnh này 
yêu cầu bên trong mạng nội bộ cần có NAT Server, DNS Server)
push “dhcp-option DNS (WINS) 10.8.0.1” đẩy DNS or WINS config vào VPN Client
```
