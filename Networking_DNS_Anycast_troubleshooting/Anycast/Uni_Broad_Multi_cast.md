
***
# What is Unicast?

Unicast is a technology of data transferring. Transmission between no more than 2 machines, one single sender and one single receiver.

![Unicast](https://github.com/qqwerty222/obsidian/blob/main/Anycast/Unicast.png)

As you can see on the diagram above, all the machines connected to router 1. But it forwards data from Host only to Nginx because Host sent it to Nginx.

## Example:
Just ping one of the machines in unicast mode.
```
bohdan@test-host:~$ ping 10.0.2.15
PING 10.0.2.15 (10.0.2.15) 56(84) bytes of data.
64 bytes from 10.0.2.15: icmp_seq=1 ttl=64 time=0.650 ms
```

---
# What is Broadcast?

Broadcast is a technology of data transferring. Transmission between one machine and anyone in the network. There is one single sender and all the machines in the network are receivers.

![Broadcast](https://github.com/qqwerty222/obsidian/blob/main/Anycast/Broadcast.png)

As you can see on diagram, all the machines connected to router 1. And it forwards data from Host to everyone in the network, even Host because it also in this network.

## Example:
Try to broadcast to all the machines visible in the network.
```
bohdan@test-host:~$ ping -b 255.255.255.255
WARNING: pinging broadcast address
PING 255.255.255.255 (255.255.255.255) 56(84) bytes of data.
```
It didn't work because nowadays, all of modern machines ignore broadcast traffic by default.
- Broadcast is outdated and non-secure technology.

To disable broadcast ignoring, you can use next command. You need to do this on all target machines.
```
bohdan@test-dns:~$ sudo sysctl net.ipv4.icmp_echo_ignore_broadcasts=0
net.ipv4.icmp_echo_ignore_broadcasts = 0
```

Next try to broadcast again.
```
bohdan@test-host:~$ ping -b 255.255.255.255
WARNING: pinging broadcast address
PING 255.255.255.255 (255.255.255.255) 56(84) bytes of data.
64 bytes from 10.0.2.5: icmp_seq=1 ttl=64 time=0.017 ms
64 bytes from 10.0.2.6: icmp_seq=1 ttl=64 time=0.662 ms
64 bytes from 10.0.2.15: icmp_seq=1 ttl=64 time=0.662 ms
```
- 255.255.255.255 is a special address that means everyone.
As you can see I got output from 3 machines, Host(my current machine), DNS and Nginx.

---
## What is Multicast?

Multicast is a technology of data transferring. Transmission between one machine and anyone in the network. There is a one single sender and some of the machines in the network are receivers.

![Multicast](https://github.com/qqwerty222/obsidian/blob/main/Anycast/Multicast.png)

As you can see on the diagram, all the machines connected to router 1. And it forwards data from Host to Nginx and DNS only, because Host sent data only to them.

## Example:
Default ping command can't ping multiple IP's. But we can install nping, it is a part of nmap package.
```
bohdan@test-host:~$ sudo apt install nmap
```

Ping some addresses in multicast way.
```
bohdan@test-host:~$ sudo nping --icmp 10.0.2.6 10.0.2.15

Starting Nping 0.7.80 ( https://nmap.org/nping ) at 2023-01-16 14:10 UTC
SENT (0.0043s) ICMP [10.0.2.5 > 10.0.2.6 Echo request ...
RCVD (0.0051s) ICMP [10.0.2.6 > 10.0.2.5 Echo reply ...
SENT (1.0055s) ICMP [10.0.2.5 > 10.0.2.15 Echo request ...
RCVD (1.0081s) ICMP [10.0.2.15 > 10.0.2.5 Echo reply ...
```
- key "--icmp" default protocol for ping.

***
## Configuration
- Windows 10
- Oracle VM VirtualBox 7.0.4
- NAT network
	- DNS ip    - 10.0.2.6
	- host ip   - 10.0.2.5
	- nginx ip - 10.0.2.15
- Ubuntu 22.04 Server as guest OS

