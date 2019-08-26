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
### Cấu hình
- Đặt mật khẩu admin(root) của Ldap. Lưu lại chuỗi mật khẩu
```
slappasswd -h {SSHA} -s passwordhere
```
- Tạo file db.ldif. Thay chuỗi trong olcRootPW bằng mật khẩu gen ở trên
```
vim db.ldif
dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcSuffix
olcSuffix: dc=itzgeek,dc=local

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=ldapadm,dc=itzgeek,dc=local

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootPW
olcRootPW: {SSHA}N/wVupN6juHSv+lZa+zEjRc8blZIdImo
```
- Import config vào LDAP Server
```
ldapmodify -Y EXTERNAL  -H ldapi:/// -f db.ldif
```
- Tạo file monitor.ldif
```
vim monitor.ldif
dn: olcDatabase={1}monitor,cn=config
changetype: modify
replace: olcAccess
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external, cn=auth" read by dn.base="cn=ldapadm,dc=itzgeek,dc=local" read by * none
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
dn: dc=itzgeek,dc=local
dc: itzgeek
objectClass: top
objectClass: domain

dn: cn=ldapadm ,dc=itzgeek,dc=local
objectClass: organizationalRole
cn: ldapadm
description: LDAP Manager

dn: ou=People,dc=itzgeek,dc=local
objectClass: organizationalUnit
ou: People

dn: ou=Group,dc=itzgeek,dc=local
objectClass: organizationalUnit
ou: Group
```
- Tạo cấu trúc thư mục
```
ldapadd -x -W -D "cn=ldapadm,dc=itzgeek,dc=local" -f base.ldif
```
### Tạo user
- Tạo file chứa thông tin user
```
vim raj.ldif
dn: uid=raj,ou=People,dc=itzgeek,dc=local
objectClass: top
objectClass: account
objectClass: posixAccount
objectClass: shadowAccount
cn: raj
uid: raj
uidNumber: 9999
gidNumber: 100
homeDirectory: /home/raj
loginShell: /bin/bash
gecos: Raj [Admin (at) ITzGeek]
userPassword: {crypt}x
shadowLastChange: 17058
shadowMin: 0
shadowMax: 99999
shadowWarning: 7
```
- Thêm user
```
ldapadd -x -W -D "cn=ldapadm,dc=itzgeek,dc=local" -f raj.ldif
```
- Gán password cho user
```
ldappasswd -s passworduserhere -W -D "cn=ldapadm,dc=itzgeek,dc=local" -x "uid=raj,ou=People,dc=itzgeek,dc=local"
```
- Verify lại user
```
ldapsearch -x cn=raj -b dc=itzgeek,dc=local
# extended LDIF
#
# LDAPv3
# base <dc=itzgeek,dc=local> with scope subtree
# filter: cn=raj
# requesting: ALL
#

# raj, People, itzgeek.local
dn: uid=raj,ou=People,dc=itzgeek,dc=local
objectClass: top
objectClass: account
objectClass: posixAccount
objectClass: shadowAccount
cn: raj
uid: raj
uidNumber: 9999
gidNumber: 100
homeDirectory: /home/raj
loginShell: /bin/bash
gecos: Raj [Admin (at) ITzGeek]
shadowLastChange: 17058
shadowMin: 0
shadowMax: 99999
shadowWarning: 7
userPassword:: e1NTSEF9MkE2eUhIS0pJQVRnMHBCdkpVWjR5Q3JvTkJLTzdBTWY=

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```
- Xóa entry
```
ldapdelete -W -D "cn=ldapadm,dc=itzgeek,dc=local" "uid=raj,ou=People,dc=itzgeek,dc=local"
```
