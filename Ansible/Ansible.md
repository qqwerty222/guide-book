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

- Create and connect VHD
- Perform task manually
- Install ansible on control node
- Prepare ansible project dir
- Create playbook
- Test playbook

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
![Add VHD](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/connect_vhd.png)

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
- \* special symbol that can be replaced by any char while OS searching for files
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
- "mount -a" reload fstab file
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
## Prepare ansible project dir

It is a directory where playbooks and config files are stored.

Final directory tree
- ansible.cfg is file where links to inventory and ansible-vault password file is stored
	- hosts is file where groups of ip or dns addresses of target machines is stored
	- group_vars/servers/.vault is where password for secrets file stored

- chdsk.yml main ansible playbook that activates roles
	- roles is directory where playbooks for groups of target machines is stored
	- test-hdd is dir of role ...

- group_vars is directory that stores secrets and variables for different groups.
	- group_vars/servers/secrets is file for servers group secrets.

- .gitignore stores files that will be ignored by git.
	- group_vars/.vault in .gitignore file
```
├── ansible.cfg
├── chdsk.yml
├── .git
├── .gitignore
├── group_vars
│   ├── servers
│   │   └── secrets
│   └── .vault
├── hosts
└── roles
    └── test-hdd
        ├── handlers
        │   └── main.yml
        └── tasks
            └── main.yml
```

#### Clone empty repository created for ansible project
```
bohdan@test-host:~$ git clone https://github.com/qqwerty222/ansible-project.git
bohdan@test-host:~$ cd ansible-project
```

#### Create hosts file and ansible.cfg
```
bohdan@test-host:~/ansible-project$ cat > hosts
[servers]
10.0.2.8
bohdan@test-host:~/ansible-project$ cat > ansible.cfg
[defaults]
inventory = ./hosts
vault_password_file = ./group_vars/.vault
```

#### Create chdsk.yml
```
bohdan@test-host:~/ansible-project$ cat > chdsk.yml
---

- name: prepare disk and mount it to /var/log dir
  hosts: servers
  remote_user: bohdan

  roles:
    - test-hdd
```

#### Create "group_vars" and "roles" dirs
```
bohdan@test-host:~/ansible-project$ mkdir group_vars roles
bohdan@test-host:~/ansible-project$ mkdir roles/test_hdd
bohdan@test-host:~/ansible-project$ mkdir roles/test-hdd/handlers
bohdan@test-host:~/ansible-project$ mkdir roles/test-hdd/tasks
bohdan@test-host:~/ansible-project$ mkdir group_vars/servers
```

#### Create secrets file for test-hdd, using ansible-vault
- ansible-vault encrypt file, after you can view or edit this file only using ansible
	- ansible-vault edit ...
	- ansible-vault view ...
```
bohd@test-host:~/ansible-project$ ansible-vault create group_vars/servers/secrets

ansible_user: bohdan
ansible_password: "userpass"
ansible_become_password: "userpass"

bohdan@test-host:~/ansible-project$ cat group_vars/servers/secrets
$ANSIBLE_VAULT;1.1;AES256
66666161613964643966343633613939313136313339353532336334386363376431306564616136
3635396662313534636334636330633030656233643863370a616139646231383931656462303666
...
bohdan@test-host:~/ansible-project$ cat > group_vars/servers/.vault
vaultpass
```

***
## Create tasks for playbook

#### First part of playbook to create Logical Volume on new disk
```
# roles/test_hdd/tasks/main.yml

---
- name: Create partition
  parted:
    device: '/dev/{{ dev_name }}'
    number: 1
    flags: [ lvm ]
    state: present
    part_end: 5GB

- name: Create volume group
  lvg:
    vg: vg01
    pvs: '/dev/{{ dev_name }}1'

- name: Create logical group
  lvol:
    vg: vg01
    lv: lv01
    size: 4096m

- name: Format into ext4
  filesystem:
    fstype: ext4
    dev: /dev/vg01/lv01
```

#### Second part to mount new LV to temporary dir
```
- name: Create temporary volume
  file:
    path: /mnt/temp_vol
    state: directory

- name: Mount lv to /mnt/temp_vol
  mount:
    path: /mnt/temp_vol
    src: /dev/vg01/lv01
    fstype: ext4
    state: mounted
```

#### Third part of the playbook to replace /var/log
```
- name: 'Copy {{ source_dir }} to /mnt/temp_vol'
  copy:
    src: '{{ source_dir }}/'
    dest: /mnt/temp_vol/
    backup: yes
    remote_src: yes

- name: Remove original '{{ source_dir }}'
  file:
    path: '{{ source_dir }}'
    state: absent

- name: Create empty '{{ source_dir }}' as mount point
  file:
    path: '{{ source_dir }}'
    state: directory

- name: Unmount /mnt/temp_vol
  mount:
    path: /mnt/temp_vol
    state: unmounted

- name: Edit /etc/fstab
  lineinfile:
    dest: /etc/fstab
    line: '/dev/vg01/lv01 {{ source_dir }} ext4 defaults 0 0'

- name: Reload fstab
  command: mount -a

- name: Change to multi.user mode
  command: init 3
```

To check is all ok with /var/log directory, use debug module.
And to make tasks file more clear, separate debug module into handlers files.

#### Create handlers file
- all tasks will be triggered by 'notify: show log'
- firstly execute 'date' 'df' and 'tail syslog' commands and save output into variables
- after print these variables using 'debug' module.
- add 'listen' to make same trigger for all the commands.
```
# roles/test-hdd/handlers/main.yml
---
- name: Check current time
  command: date +%X
  register: current_time
  listen: 'show log'

- name: Check current mount
  command: 'df {{ source_dir }}'
  register: current_mount
  listen: 'show log'

- name: Check /var/log/syslog
  command: tail /var/log/syslog
  register: last_syslog
  listen: 'show log'

- name: Show current time and mount
  debug:
    msg:
    - 'Start time: {{ current_time.stdout_lines}}'
    - 'Current mount to {{ source_dir }}:'
    - '{{ current_mount.stdout_lines }}'
    - 'Last 10 logs from /var/log/syslog:'
    - '{{ last_syslog.stdout_lines }}'
  listen: 'show log'
```

#### Add handlers notifiers into tasks
- to get logs before changes and after, add notify statements on the start and end of the file
- use 'meta' module to make notifiers permanent. (by default they trigger only on changes)
```
# roles/test-hdd/tasks/main.yml
---
- name: Set single.user mode
  command: init 1
  notify: "show log"

- name: Show start log
  meta: flush_handlers

...

- name: Restart machine
  reboot:
  notify: "show log"

- name: Show finish log
  meta: flush_handlers

```

#### Add variables to main playbook file
- to make playbook more flexible, make source dir and hdd device name variable.
```
chdsk.yml
---
- name: Prepare new disk and remount some dir to it
  hosts: servers
  remote_user: bohdan
  become: true
  vars:
    dev_name: sdb
    source_dir: /var/log
  roles:
    - test-hdd
```

***
## Test playbook

- Start ansible playbook
```
bohdan@test-host:~/ansible-project$ ansible-playbook chdsk.yml

PLAY [Prepare new disk and remount some dir to it] *****************************

TASK [Gathering Facts] ********************************************************************************
ok: [10.0.2.8]
...
TASK [test-hdd : Create partition] *********************************************************************************changed: [10.0.2.8]

TASK [test-hdd : Create volume group] *********************************************************************************changed: [10.0.2.8]
...
```

- Start log
```
RUNNING HANDLER [test-hdd : Show current time and mount] ***********************
ok: [10.0.2.8] => {
    "msg": [
        "Start time: ['02:01:07 PM']",
        "Current mount to /var/log:",
        [
            "Filesystem            1K-blocks  Used    Available Use% Mounted on",
            "/dev/./vg-ubuntu--lv  11758760   4932448 6207204   45%  /"
        ],
        "Last 10 logs from /var/log/syslog:",
        [
            "Jan 24 14:01:06 testhdd systemd[1]: Stopping Getty on tty1...",
            "Jan 24 14:01:06 testhdd ModemManager[757]: ModemManager is ...,
            "Jan 24 14:01:06 testhdd systemd[1]: Stopping LVM event ...",
            ...
        ]
    ]
}
```

- Finish log
```
RUNNING HANDLER [test-hdd : Show current time and mount] ***********************
ok: [10.0.2.8] => {
    "msg": [
        "Start time: ['02:02:10 PM']",
        "Current mount to /var/log:",
        [
            "Filesystem            1K-blocks  Used Available Use% Mounted on",
            "/dev/mapper/vg01-lv01   4046560 77308   3743156   3% /var/log"
        ],
        [
        [
            "Jan 24 14:02:09 testhdd systemd[1]: snapd.service: Deactivated ...
            "Jan 24 14:02:09 testhdd systemd[1]: snapd.service: Consumed ...
            "Jan 24 14:02:10 testhdd python3[1217]: ansible-ansible.legacy ...
        ]
    ]
}			
```

As you can see from df output in finish log /var/log mounted to vg01-lv01 it is lvm created by this playbook. Also on the output from syslog you can see that new logs was written after restart into /var/log.