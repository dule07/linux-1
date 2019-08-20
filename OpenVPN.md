# OPENVPN
## Cài đặt
### Tải source
```
yum install epel-release -y
yum install openssl pam openssl-devel  pam-devel net-tools gcc cmake -y
wget http://www.oberhumer.com/opensource/lzo/download/lzo-2.10.tar.gz
wget https://build.openvpn.net/downloads/releases/openvpn-2.4.5.tar.gz
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
