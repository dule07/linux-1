# OpenLdap
## Lý thuyết
- Lightweight Directory Access Protocol: là một giao thức phát triển trên chuẩn X500
- Là một giao thức dạng client-server sử dụng để truy cập một dịch vụ thư mục
- Là một giao thức cho phép người dùng xác định cấu trúc và đặc điểm của thông tin trong thư mục.
- Các ứng dụng để triển khai LDAP : OpenLDAP, OPENDNS. Active Directory,….
## File LDIF
- LDIF (LDAP Interchange Format) được định nghĩa trong RFC 2849, là một chuẩn định dạng file text lưu trữ thông tin cấu hình LDAP và nội dung thư mục.
- File LDIF thường được sử dụng để import dữ liệu mới vào trong directory của chúng ta hoặc thay đổi dữ liệu đã có. Dữ liệu có trong file LDIF cần phải tuân theo luật schema của LDAP directory.
## Cài đặt
### Cài đặt gói
```
yum install epel-release -y
yum -y install openldap compat-openldap openldap-clients openldap-servers openldap-servers-sql openldap-devel
```
### Start dịch vụ
```
systemctl start slapd
systemctl enable slapd
```
### Cấu hình. Thư mục config /etc/openldap/slapd.d/cn\=config/
- Đặt mật khẩu admin(root) của Ldap. Lưu lại chuỗi mật khẩu
```
slappasswd -h {SSHA} -s passwordhere
```
- Tạo file db.ldif. Thay chuỗi trong olcRootPW bằng mật khẩu gen ở trên. Verify trong olcDatabase\=\{2\}hdb.ldif
```
vim db.ldif
dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcSuffix
olcSuffix: dc=sun-asterisk,dc=com

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=ldapadm,dc=sun-asterisk,dc=com

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootPW
olcRootPW: {SSHA}V9ANz6mNI8nbcX0HyQFSTtLpU0T6aNUG
```
- Import config vào LDAP Server
```
ldapmodify -Y EXTERNAL  -H ldapi:/// -f db.ldif
```
- Tạo file monitor.ldif. Verify trong olcDatabase={1}monitor.ldif
```
vim monitor.ldif
dn: olcDatabase={1}monitor,cn=config
changetype: modify
replace: olcAccess
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external, cn=auth" read by dn.base="cn=ldapadm,dc=sun-asterisk,dc=com" read by * none
```
- Import config vào LDAP Server
```
ldapmodify -Y EXTERNAL  -H ldapi:/// -f monitor.ldif
```
### Tạo Database LDAP
- Sao chép file cấu hình, import schema
```
cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG
chown ldap:ldap /var/lib/ldap/*
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif 
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif
```
- Tạo file base 
```
vim base.ldif
dn: dc=sun-asterisk,dc=com
dc: sun-asterisk
objectClass: top
objectClass: domain

dn: cn=ldapadm ,dc=sun-asterisk,dc=com
objectClass: organizationalRole
cn: ldapadm
description: LDAP Manager

dn: ou=People,dc=sun-asterisk,dc=com
objectClass: organizationalUnit
ou: People

dn: ou=Group,dc=sun-asterisk,dc=com
objectClass: organizationalUnit
ou: Group
```
- Tạo cấu trúc thư mục
```
ldapadd -x -W -D "cn=ldapadm,dc=sun-asterisk,dc=com" -f base.ldif
```
### Tạo user
- Tạo file chứa thông tin user
```
vim hoangha.ldif
dn: uid=hoangha,ou=People,dc=sun-asterisk,dc=com
objectClass: top
objectClass: account
objectClass: posixAccount
objectClass: shadowAccount
cn: hoangha
uid: hoangha
uidNumber: 9999
gidNumber: 100
homeDirectory: /home/hoangha
loginShell: /bin/bash
gecos: Hoang Ha [Admin (at) Sun-Asterisk]
userPassword: {crypt}x
shadowLastChange: 17058
shadowMin: 0
shadowMax: 99999
shadowWarning: 7
```
- Thêm user
```
ldapadd -x -W -D "cn=ldapadm,dc=sun-asterisk,dc=com" -f hoangha.ldif
```
- Gán password cho user
```
ldappasswd -s passworduserhere -W -D "cn=ldapadm,dc=sun-asterisk,dc=com" -x "uid=hoangha,ou=People,dc=sun-asterisk,dc=com"
```
- Verify lại user
```
ldapsearch -x cn=hoangha -b dc=sun-asterisk,dc=com
# extended LDIF
#
# LDAPv3
# base <dc=sun-asterisk,dc=com> with scope subtree
# filter: cn=hoangha
# requesting: ALL
#

# hoangha, People, sun-asterisk.com
dn: uid=hoangha,ou=People,dc=sun-asterisk,dc=com
objectClass: top
objectClass: account
objectClass: posixAccount
objectClass: shadowAccount
cn: hoangha
uid: hoangha
uidNumber: 9999
gidNumber: 100
homeDirectory: /home/hoangha
loginShell: /bin/bash
gecos: Hoang Ha [Admin (at) Sun-Asterisk]
shadowLastChange: 17058
shadowMin: 0
shadowMax: 99999
shadowWarning: 7
userPassword:: e1NTSEF9c2MvS2xYbEVsSlVTbXhRQUYrbUNRYkJTMXE1QUFKbkM=

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```
- Xóa entry
```
ldapdelete -W -D "cn=ldapadm,dc=sun-asterisk,dc=com" "uid=hoangha,ou=People,dc=sun-asterisk,dc=com"
```
