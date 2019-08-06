# Tăng dụng lượng volume sử dụng LVM
## I. Tăng ổ cứng đã có sẵn trên ảo hóa
- Bước 1: Kiểm tra device type có phải LVM
```
[root@localhost ~]# lsblk 
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   40G  0 disk 
├─sda1            8:1    0  953M  0 part /boot
├─sda2            8:2    0 19.1G  0 part 
│ ├─centos-root 253:0    0 38.1G  0 lvm  /
│ └─centos-swap 253:1    0  956M  0 lvm  [SWAP]
└─sda3            8:3    0   20G  0 part 
  └─centos-root 253:0    0 38.1G  0 lvm  /
sr0              11:0    1  918M  0 rom  

```
- Bước 2: Tăng dung lượng ổ cứng trên VMware. Khởi động lại VM
  * Kiểm tra lại thông tin disk
```
[root@localhost ~]# lsblk 
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   50G  0 disk 
├─sda1            8:1    0  953M  0 part /boot
├─sda2            8:2    0 19.1G  0 part 
│ ├─centos-root 253:0    0 38.1G  0 lvm  /
│ └─centos-swap 253:1    0  956M  0 lvm  [SWAP]
└─sda3            8:3    0   20G  0 part 
  └─centos-root 253:0    0 38.1G  0 lvm  /
sr0              11:0    1  918M  0 rom  
```
Thấy sda tăng từ 40GB lên 50GB
- Bước 3: Tạo 1 partition mới với định dạng LVM
```
 fdisk /dev/sda
 Command (m for help): n
 Select (default e): p
 Command (m for help): p
   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *           1         122      975872   83  Linux
/dev/sda2             122        2611    19994624   8e  Linux LVM
/dev/sda3            2611        5222    20971520   8e  Linux LVM
/dev/sda4            5222        6527    10485087+  83  Linux
Command (m for help): t
Partition number (1-4, default 4): 4
Hex code (type L to list all codes): 8e
Command (m for help): p
   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *           1         122      975872   83  Linux
/dev/sda2             122        2611    19994624   8e  Linux LVM
/dev/sda3            2611        5222    20971520   8e  Linux LVM
/dev/sda4            5222        6527    10485087+  8e  Linux LVM
```



