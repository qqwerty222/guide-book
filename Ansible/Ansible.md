***
# What is?

Ansible is automated configuration management tool. Using it you can implement IaaC.

***
## What is IaaC?:

Infrastructure as a Code - method of managing infrastructure configuration using machine and human-readable file. Coding

***

# Implement IaaC to set up HDD using Ansible 

## Configuration
- Windows 10
- Oracle VM VirtualBox 7.0.4
- NAT network
	- host ip   - 10.0.2.5
	- dns ip - 10.0.2.6
- Ubuntu 22.04 Server as guest OS

***
## Create virtual HDD and VM

### Create virtual hdd in Oracle Virtual Box

#### Open VirtualBox and go to Media settings 
1) Extend "Tools" block
2) Click on "Media"
![Open Media](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/open_media.png)

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

#### Check creation of the vhd
![Check disk](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/finish.png)

***
### Start virtual machine in Oracle Virtual Box 7.04

#### Create new VM
1) Click "Machine" in the top bar
2) Click "New..."
![Create VM](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/create_vm.png)

#### Choose OS for VM
1) Pick the name for VM
2) Choose image (downloaded from Ubuntu website)
3) Check "Skip Unattended Installation"
4) Click "Next" button
![Choose_os](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/choose_os.png)

#### Choose CPU and RAM 
1) Choose RAM
2) Choose CPU
3) Click "Next"
![Choose CPU RAM](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/choose_cpu_ram.png)

#### Choose disk space for OS
1) Choose amount of space (25Gb is enough)
2) Put check on "Pre-allocate Full Size", I am not going expand it.
3) Click "Next"
![Choose Disk Space](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/choose_space.png)

#### Finish VM creation
1) Click "Finish"
![Choose Disk Space](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/choose_space.png)

***
## Start VM, connect via ssh and connect VHD

### Create Nat Network and start VM

#### Create Network
1) Click on drop menu on "Tools" button
2) Click Network
![Open Net Settings](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/open_networks.png)
1) Click "NAT Networks" 
2) Click "Create"
![Create Net](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/create_net.png)

#### Connect to NAT Network
- Do this, if you want your VMs be in one network
1) Click on your VM
2) Click "Settings"
3) Click "Network"
4) Choose "NAT Network"
5) Select name of network you created
6) Click "Ok"
![Connect to NAT](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/connect_to_nat.png)

#### Start VM
1) Click on your VM in the left bar
2) Click "Start" button
![Start VM](https://github.com/qqwerty222/obsidian/blob/main/Ansible/screenshots/start_vm.png)
- Just follow installation instructions, pick "Install OpenSSS server"
### 


***