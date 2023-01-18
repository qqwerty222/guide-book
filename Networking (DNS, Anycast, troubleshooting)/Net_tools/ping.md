## What is it?

It is a tool that can check connection to hostname or ip address.

Ping use special packets ICMP

***
## Installation

### Install on Ubuntu 
```
bohdan@test-host:~$ sudo apt-get install iputils-ping
```

### Configuration
- Windows 10
- Oracle VM VirtualBox 7.0.4
- NAT network
	- DNS ip    - 10.0.2.6 (ns1.testdomain.com)
	- host ip   - 10.0.2.5
- Ubuntu 22.04 Server as guest OS

***
## Syntax

```
ping [options] <destination>
```

***
## Basic commands

- Check connection to hostname
- Ping several times
- Ping in broadcast mode

- ###  Ping hostname ^d18198
	- Each row of input include: 
		- size of the packet
		- hostname 
		- ip address 
		- number of packet and time of response

	 - At the end you can see detailed statistic include: 
		- how many packets sended 
		- how many packets received
		- percent of packets that didn't returned
		- total time
```
bohdan@test-host:~$ ping ns1.testdomain.com
PING ns1.testdomain.com (10.0.2.6) 56(84) bytes of data.
64 bytes from ns1.testdomain.com (10.0.2.6): icmp_seq=1 ttl=64 time=0.827 ms
64 bytes from ns1.testdomain.com (10.0.2.6): icmp_seq=2 ttl=64 time=1.72 ms
64 bytes from ns1.testdomain.com (10.0.2.6): icmp_seq=3 ttl=64 time=1.70 ms
...
--- ns1.testdomain.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 0.827/1.415/1.722/0.416 ms
```

- ### Ping several times ^1484e1
	- -c means count
```
bohdan@test-host:~$ ping -c 5 10.0.2.6
PING 10.0.2.6 (10.0.2.6) 56(84) bytes of data.
64 bytes from 10.0.2.6: icmp_seq=1 ttl=64 time=0.813 ms
64 bytes from 10.0.2.6: icmp_seq=2 ttl=64 time=1.57 ms
64 bytes from 10.0.2.6: icmp_seq=3 ttl=64 time=1.72 ms
64 bytes from 10.0.2.6: icmp_seq=4 ttl=64 time=2.05 ms
64 bytes from 10.0.2.6: icmp_seq=5 ttl=64 time=1.93 ms

--- 10.0.2.6 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4006ms
rtt min/avg/max/mdev = 0.813/1.618/2.050/0.435 ms
```

- ### Ping in broadcast mode ^57a302
	- -b enable broadcast

Firstly disable broadcast ignoring. You need to do this on all of target machines.
```
bohdan@test-dns:~$ sudo sysctl net.ipv4.icmp_echo_ignore_broadcasts=0
net.ipv4.icmp_echo_ignore_broadcasts = 0
```

Next try broadcast again.  255.255.255.255 is a special address that means everyone.
```
bohdan@test-host:~$ ping -b 255.255.255.255
WARNING: pinging broadcast address
PING 255.255.255.255 (255.255.255.255) 56(84) bytes of data.
64 bytes from 10.0.2.5: icmp_seq=1 ttl=64 time=0.017 ms
64 bytes from 10.0.2.6: icmp_seq=1 ttl=64 time=0.662 ms
64 bytes from 10.0.2.15: icmp_seq=1 ttl=64 time=0.662 ms
```
As you can see I got output from 3 machines, Host(my current machine), DNS and Nginx.
