***
# What is?

Ansible is automated configuration management tool. Using it you can implement IaaC.

***
## What is IaaC?:

Infrastructure as a Code - method of managing infrastructure configuration using machine and human-readable file. Coding

***

# Implement IaaC to set up HDD using Ansible 

### Task configure LVM on a new disk:
- add PV, VG, LV 
- formats volume as ext4
- mount as replacement of /var (preserving data)
- create /etc/fstab entry
- make sure that OS is still working and write to logs

### Acceptance criteria
- Code should implement only once
- OS works without any issues,
	- logs stored into /var/log
	- daemons are able to write logs
	- root user can read logs
- /var is mounted by /etc/fstab and OS is resistant to situation when a disk is disconnected
	- logs continuity is doesn't matter
- code stored in git

### Configuration
- Windows 10
- Oracle VM VirtualBox 7.0.4
	- Ubuntu 22.04 Server as guest OS
- NAT network
	- host ip    - 10.0.2.5
	- test-hdd - 10.0.2.8
- Ansible 2.14.1
- Ansible (python package) 7.1.0

***
## Perform task manually

#### Login as root
```
bohdan@testhdd:~$ sudo -i
```

#### Change system to single-user mod
```
bohdan@testhdd:~$ sudo init 1
```

#### Check connected disks
- * special symbol that can be replaced by any char while OS searching for files
- main disk is divided on 3 partitions (sda, sda1, sda2, sda3)
- new disk is /dev/sdb
```
bohdan@testhdd:~$ sudo ls /dev/sd*
/dev/sda  /dev/sda1  /dev/sda2  /dev/sda3  /dev/sdb
```

#### Initialize /dev/sdb as PV (Physical Volume)
```
bohdan@testhdd:~$ sudo pvcreate /dev/sdb
  Physical volume "/dev/sdb" successfully created.
```

#### Check available PVs in system
```
bohdan@testhdd:~$ sudo pvs
  PV         VG        Fmt  Attr PSize   PFree
  /dev/sda3  ubuntu-vg lvm2 a--  <23.00g 11.50g
  /dev/sdb             lvm2 ---    5.00g  5.00g
```

#### Create VG (Volume Group)
```
bohdan@testhdd:~$ sudo vgcreate vg01 /dev/sdb
  Volume group "vg01" successfully created
```

#### Check available VGs in system
```
bohdan@testhdd:~$ sudo vgs
  VG        #PV #LV #SN Attr   VSize   VFree
  ubuntu-vg   1   1   0 wz--n- <23.00g 11.50g
  vg01        1   0   0 wz--n-  <5.00g <5.00g
```

#### Activate VG
- set "-a n" to activate
- set "-a y" to deactivate
```
bohdan@testhdd:~$ sudo vgchange -a n vg01
  0 logical volume(s) in volume group "vg01" now active
```

#### Create LV (Logical Volume)
```
bohdan@testhdd:~$ sudo lvcreate -L 4096MB -n lv01 vg01
  Logical volume "lv01" created.
```

#### Check available LVs in the system
```
bohdan@testhdd:~$ sudo lvdisplay
  --- Logical volume ---
  LV Path                /dev/vg01/lv01
  LV Name                lv01
  VG Name                vg01
  LV UUID                ZMPB14-Vv4z-xqDH-aVH2-282m-RjL0-XcoumX
  LV Write Access        read/write
  LV Creation host, time testhdd, 2023-01-20 14:40:01 +0000
  LV Status              available
  # open                 0
  LV Size                4.00 GiB
  Current LE             1024
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:1
```

#### Format into ext4
```
bohdan@testhdd:~$ sudo mkfs.ext4 /dev/vg01/lv01
mke2fs 1.46.5 (30-Dec-2021)
Creating filesystem with 1048576 4k blocks and 262144 inodes
Filesystem UUID: 04c2909a-6a7d-4671-abf5-c9ec0ee5e9f8
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done
```

#### Create rescue.target
```
bohdan@testhdd:~$ sudo systemctl set-default rescue.target
Created symlink /etc/systemd/system/default.target → /lib/systemd/system/rescue.target.
```

#### Mount new volume to temporary location
```
bohdan@testhdd:~$ sudo mkdir /mnt/tmp_vol
bohdan@testhdd:~$ sudo mount -t ext4 /dev/vg01/lv01 /mnt/tmp_vol/
```

#### Move /var/log to temporary location
```
bohdan@testhdd:~$ sudo mv /var/log/* /mnt/tmp_vol
bohdan@testhdd:~$ ls /mnt/tmp_vol
alternatives.log  btmp                   dmesg       dmesg.3.gz  installer  lastlog     ubuntu-advantage.log
apt               cloud-init.log         dmesg.0     dmesg.4.gz  journal    lost+found  unattended-upgrades
auth.log          cloud-init-output.log  dmesg.1.gz  dpkg.log    kern.log   private     wtmp
bootstrap.log     dist-upgrade           dmesg.2.gz  faillog     landscape  syslog
```

#### Unmount temp volume
```
bohdan@testhdd:~$ sudo umount /mnt/tmp_vol
```

#### Edit /etc/fstab and reload configuration 
- /etc/fstab is file-instruction for Linux to disk mounting
```
/etc/fstab
...
/dev/vg01/lv01 /var/log ext4 defaults 0 0

dohdan@testhdd:~$ sudo mount -a
bohdan@testhdd:~$ ls /var/log
alternatives.log  btmp                   dmesg       dmesg.3.gz  installer  lastlog     ubuntu-advantage.log
apt               cloud-init.log         dmesg.0     dmesg.4.gz  journal    lost+found  unattended-upgrades
auth.log          cloud-init-output.log  dmesg.1.gz  dpkg.log    kern.log   private     wtmp
bootstrap.log     dist-upgrade           dmesg.2.gz  faillog     landscape  syslog
```

#### Reload fstab file
***
## Install ansible on control node (host)

#### Ensure that you have python 3.9+ installed
```
bohdan@test-host:~$ python3 --version
Python 3.10.6
```

#### Ensure that pip is installed
```
bohdan@test-host:~$ python3 -m pip -V
/usr/bin/python3: No module named pip
```

#### Install ansible
```
bohdan@test-host:~$ sudo apt-get install ansible
```

#### Install pip
```
bohdan@test-host:~$ sudo apt-get install python3-pip
bohdan@test-host:~$ python3 -m pip -V
pip 22.0.2 from /usr/lib/python3/dist-packages/pip (python 3.10)
```

####  Installing ansible python package
- Ansible is written on python, and it needs python to work.
```
bohdan@test-host:~$ python3 -m pip install --user ansible
```

***
## Create ansible project dir

It is a directory where i will store configuration files and playbooks.

#### Clone empty repository created for ansible project
```
bohdan@test-host:~$ git clone https://github.com/qqwerty222/ansible-project.git
bohdan@test-host:~$ cd ansible-project
```

#### Create "group_vars" and "roles" dirs
```
bohdan@test-host:~/ansible-project$ mkdir group_vars roles
bohdan@test-host:~/ansible-project$ ls
group_vars  roles
bohdan@test-host:~/ansible-project$ mkdir roles/test_hdd
bohdan@test-host:~/ansible-project$ touch group_vars/test_hdd
```

***
## Create and connect VHD

#### Open VirtualBox and go to Media settings 
1) Extend "Tools" block
2) Click on "Media"
![Open media](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/open_media%20.png)

####  Choose type of Virtual Hard Disk
1) Click on "Create" button
2) Select "VHD (Virtual Hard Disk)"
3) Click "Next"
![Choose type](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/choose_disk.png)

#### Set pre-allocated Full Size
- means that size of your VHD will be fixed.
1) Put check on "Pre-allocate Full Size"
2) Click "Next"
![Choose type](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/check_size.png)

#### Choose size of the disk
1) Put name for your disk if needed
2) Set size for your disk
3) Click "Finish"
![Choose size](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/choose_size.png)

#### Check creation of the VHD
![Check disk](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/finish.png)

#### Connect VHD to VM
1) Select your VM in the left bar
2) Click "Settings"
3) Click "Storage"
4) Click "Add VHD" button
5) Select VHD and click "Choose"
6) Click "Ok"
![Add VHD](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/Add_VHD.png)

