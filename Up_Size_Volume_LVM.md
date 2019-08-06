# Tăng dụng lượng volume sử dụng LVM
## I. Tăng ổ cứng đã có sẵn trên ảo hóa
- Bước 1: Kiểm tra device type có phải LVM
```
[root@localhost ~]# lsblk 
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk 
├─sda1            8:1    0  953M  0 part /boot
└─sda2            8:2    0 19.1G  0 part 
  ├─centos-root 253:0    0 18.1G  0 lvm  /
  └─centos-swap 253:1    0  956M  0 lvm  [SWAP]
sr0              11:0    1 1024M  0 rom  
```
- Bước 2: Tăng dung lượng ổ cứng trên VMware. Khởi động lại VM
* Kiểm tra lại thông tin disk
```
[root@localhost ~]# lsblk 
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   40G  0 disk 
├─sda1            8:1    0  953M  0 part /boot
└─sda2            8:2    0 19.1G  0 part 
  ├─centos-root 253:0    0 18.1G  0 lvm  /
  └─centos-swap 253:1    0  956M  0 lvm  [SWAP]
sr0              11:0    1  918M  0 rom  
```
Thấy sda tăng từ 20GB lên 40GB
- Bước 3:

