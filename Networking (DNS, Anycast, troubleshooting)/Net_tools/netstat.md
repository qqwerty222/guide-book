## What is it?

Tool that shows network connections, routing tables, interface statistics, multicast memberships

***
## Installation

### Install on Ubuntu 
```
bohdan@test-host:~$ sudo apt-get install net-tools
```

### Configuration
- Windows 10
- Oracle VM VirtualBox 7.0.4
- NAT network
	- DNS ip    - 10.0.2.6
	- host ip   - 10.0.2.5
- Ubuntu 22.04 Server as guest OS

***
## Syntax

```
netstat [-vWeenNcCF] [<Af>] -r
netstat [-vWnNcaeol] [<Socket> ...]
netstat { [-vWeenNac] -i | [-cnNe] -M | -s [-6tuw] }
netstat {-V|--version|-h|--help}
```

***
## Basic commands

- [[#^7273ad|Show currently opened on listening tcp/udp ports]]

- ###  Show currently opened on listening tcp/udp ports ^7273ad
	- -t tcp 
	- -u udp 
	- -p port
	- -l listen
	- -n names
	- to get information about Program names use sudo 
```
bohdan@test-dns:~$ sudo netstat -tupln
[sudo] password for bohdan:
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address     Foreign Address         State       PID/P name
tcp        0      0 10.0.2.6:53       0.0.0.0:*               LISTEN      770/named
tcp        0      0 127.0.0.1:953     0.0.0.0:*               LISTEN      770/named
tcp        0      0 0.0.0.0:22        0.0.0.0:*               LISTEN      3285/sshd: 
tcp        0      0 127.0.0.53:53     0.0.0.0:*               LISTEN      698/resolve
tcp6       0      0 :::23             :::*                    LISTEN      2422/xinetd
tcp6       0      0 :::22             :::*                    LISTEN      3285/sshd: 
udp        0      0 10.0.2.6:68       0.0.0.0:*                           696/network
udp        0      0 10.0.2.6:53       0.0.0.0:*                           770/named
udp        0      0 127.0.0.53:53     0.0.0.0:*                           698/resolve
```

- ### Pull statistics about protocol
	- -s statistic
```
bohdan@test-host:~$ netstat -s
Ip:
    Forwarding: 2
    17544 total packets received
    0 forwarded
    0 incoming packets discarded
    17542 incoming packets delivered
    19561 requests sent out
    20 outgoing packets dropped
Icmp:
    4611 ICMP messages received
    0 input ICMP message failed
    ICMP input histogram:
        destination unreachable: 40
        timeout in transit: 3885
        echo requests: 11
        echo replies: 675
    5360 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 40
        echo requests: 5309
        echo replies: 11
...
```

- ### Show input/outputs by interface
	- -i interface
```
bohdan@test-host:~$ netstat -i
Kernel Interface table
Iface      MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
enp0s3    1500    27092      0      0 0         19706      0      0      0 BMRU
lo       65536      110      0      0 0           110      0      0      0 LRU
```