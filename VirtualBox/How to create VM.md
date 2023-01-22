***
## Start virtual machine in Oracle Virtual Box 7.04

#### Create new VM
1) Click "Machine" in the top bar
2) Click "New..."
![Create VM](https://github.com/qqwerty222/obsidian/blob/main/VirtualBox/screenshots/create_vm.png)

#### Choose OS for VM
1) Pick the name for VM
2) Choose image (downloaded from Ubuntu website)
3) Check "Skip Unattended Installation"
4) Click "Next" button
![Choose_os](https://github.com/qqwerty222/obsidian/blob/main/VirtualBox/screenshots/choose_os.png)

#### Choose CPU and RAM 
1) Choose RAM
2) Choose CPU
3) Click "Next"
![Choose CPU RAM](https://github.com/qqwerty222/obsidian/blob/main/VirtualBox/screenshots/choose_cpu_ram.png)

#### Choose disk space for OS
1) Choose amount of space (25Gb is enough)
2) Put check on "Pre-allocate Full Size", I am not going to expand it.
3) Click "Next"
![Choose Disk Space](https://github.com/qqwerty222/obsidian/blob/main/VirtualBox/screenshots/choose_space.png)

#### Finish VM creation
1) Click "Finish"
![Choose Disk Space](https://github.com/qqwerty222/obsidian/blob/main/VirtualBox/screenshots/finish_installation.png)

***
## Create Nat Network and start VM

#### Create Network
1) Click on drop menu on "Tools" button
2) Click Network
![Open Net Settings](https://github.com/qqwerty222/obsidian/blob/main/VirtualBox/screenshots/open_networks.png)
1) Click "NAT Networks" 
2) Click "Create"
![Create Net](https://github.com/qqwerty222/obsidian/blob/main/VirtualBox/screenshots/create_net.png)

#### Connect to NAT Network
- Do this, if you want your VMs to be in one network
1) Click on your VM
2) Click "Settings"
3) Click "Network"
4) Choose "NAT Network" and select name of network you created
5) Click "Advanced" and select "Promiscuous Mode" Allow All.
- select "Allow VMs" if you are not going to ssh from host
6) Click "Ok"
![Connect to NAT](https://github.com/qqwerty222/obsidian/blob/main/VirtualBox/screenshots/connect_to_nat.png)

#### Start VM
1) Click on your VM in the left bar
2) Click "Start" button
![Start VM](https://github.com/qqwerty222/obsidian/blob/main/VirtualBox/screenshots/start_vm%20.png)
- Just follow installation instructions, pick "Install OpenSSH server"

#### Check ip address
![Check ip](https://github.com/qqwerty222/obsidian/blob/main/VirtualBox/screenshots/check_ipv4.png)

***
## ssh into VM

### If you want to ssh into VM from host machine:

#### Set up port forwarding from host to VM
1) Open "Tools" drop menu
2) Click "Network"
3) Select your Nat Network
4) Click "Properties"
5) Click "Add" and fill 
	- protocol "TCP" 
	- Host Port any you want (2225 doesn't work)
	- Guest IP is ip of your target VM
	- Guest Port 22, default ssh port
6) Click "Apply"
![Add port](https://github.com/qqwerty222/obsidian/blob/main/VirtualBox/screenshots/add_port.png)

#### Open cmd and connect
- type "ssh -p2222 bohdan@localhost" 
	- where -p is a port you selected in "Set up port forwarding ..." section
	- bohdan@localhost is username chosen while OS installation in VM
	- after print "yes" and password you selected while OS installation in VM
```
ssh -p2227 bohdan@localhost
```

### If you want to ssh into VM from another VM in this network

- type "ssh bohdan@10.0.2.8"
	- 10.0.2.8 is ip address of VM
	- bohdan@localhost is username chosen while OS installation in VM
	- after print "yes" and password you selected while OS installation in VM
```
bohdan@test-host:~$ ssh bohdan@10.0.2.8
```



