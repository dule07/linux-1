# Tăng dung lượng partition sử dụng LVM (VMware)
## Một số câu lệnh
   * pvdisplay: Hiện thị list physical volume
   * vgdisplay: Hiện thị list volume group
   * lvdisplay: Hiện thị list logical volume
### Lưu ý
   * Một hardisk chỉ tạo tối đa 4 phân vùng Primary. Nếu đã tạo đủ 4 phân vùng thì nên thêm một ổ cứng mới. Sử dụng fdisk   
### Thực hiện
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
- Bước 3: Tạo 1 partition mới với định dạng LVM. Phân vùng mới là /dev/sda4
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
Command (m for help): t (Change type partition)
Partition number (1-4, default 4): 4 (Select partition)
Hex code (type L to list all codes): 8e (Set Type LVM)
Command (m for help): p
   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *           1         122      975872   83  Linux
/dev/sda2             122        2611    19994624   8e  Linux LVM
/dev/sda3            2611        5222    20971520   8e  Linux LVM
/dev/sda4            5222        6527    10485087+  8e  Linux LVM
Command (m for help): w
```
- Bước 4: Reboot để có hiệu lực. Trước khi reboot không thấy sda4
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
- Bước 5: Thêm phân vùng trên thành Physical volume
```
[root@localhost ~]# lsblk 
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   50G  0 disk 
├─sda1            8:1    0  953M  0 part /boot
├─sda2            8:2    0 19.1G  0 part 
│ ├─centos-root 253:0    0 38.1G  0 lvm  /
│ └─centos-swap 253:1    0  956M  0 lvm  [SWAP]
├─sda3            8:3    0   20G  0 part 
│ └─centos-root 253:0    0 38.1G  0 lvm  /
└─sda4            8:4    0   10G  0 part 
sr0              11:0    1  918M  0 rom  
[root@localhost ~]# pvcreate /dev/sda4
```
- Bước 6: Thêm Physical volume vào Volume group
```
[root@localhost ~]# vgextend centos /dev/sda4
```
- Bước 7: Mở rộng logical volume
```
[root@localhost ~]# lvextend -l+100%FREE /dev/centos/root
  Size of logical volume centos/root changed from <38.13 GiB (9761 extents) to 48.12 GiB (12320 extents).
  Logical volume centos/root successfully resized.
```
- Bước 8: Cập nhật lại tệp hệ thống
```
[root@localhost ~]# resize2fs /dev/centos/root
resize2fs 1.42.9 (28-Dec-2013)
Filesystem at /dev/centos/root is mounted on /; on-line resizing required
old_desc_blocks = 5, new_desc_blocks = 7
The filesystem on /dev/centos/root is now 12615680 blocks long.

[root@localhost ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   48G  1.2G   44G   3% /
devtmpfs                 475M     0  475M   0% /dev
tmpfs                    487M     0  487M   0% /dev/shm
tmpfs                    487M  7.6M  479M   2% /run
tmpfs                    487M     0  487M   0% /sys/fs/cgroup
/dev/sda1                950M  133M  817M  15% /boot
tmpfs                     98M     0   98M   0% /run/user/0
[root@localhost ~]# lsblk  -f
NAME            FSTYPE      LABEL           UUID                                   MOUNTPOINT
sda                                                                                
├─sda1          xfs                         aa57d022-88a1-4abb-ae0d-e184061d90fb   /boot
├─sda2          LVM2_member                 FP9XtM-WI9T-b2q9-jpki-dYFE-J0k2-qL5GJ3 
│ ├─centos-root ext4                        b5938001-2df1-4643-ac6d-57d4ce1ab29d   /
│ └─centos-swap swap                        c9fe9e48-2752-409b-ac0c-eca677dc0b5d   [SWAP]
├─sda3          LVM2_member                 AqJZmR-LjbR-eEaG-7dJH-dUab-Y5SB-3WzYXf 
│ └─centos-root ext4                        b5938001-2df1-4643-ac6d-57d4ce1ab29d   /
└─sda4          LVM2_member                 b9qw2E-rjpt-orOM-ecoH-nRPI-UgGw-iMZpTk 
  └─centos-root ext4                        b5938001-2df1-4643-ac6d-57d4ce1ab29d   /

```

