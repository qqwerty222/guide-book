## What is it?

Tool that used in configuring network interfaces

***
## Installation

### Install on Ubuntu 
```
bohdan@test-host:~$ sudo apt-get install iproute2
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
Usage: ip [ OPTIONS ] OBJECT { COMMAND | help }
       ip [ -force ] -batch filename

where  OBJECT := { address | addrlabel | fou | help | ila | ioam | l2tp | link |
                   macsec | maddress | monitor | mptcp | mroute | mrule |
                   neighbor | neighbour | netconf | netns | nexthop | ntable |
                   ntbl | route | rule | sr | tap | tcpmetrics |
                   token | tunnel | tuntap | vrf | xfrm }

       OPTIONS := { -V[ersion] | -s[tatistics] | -d[etails] | -r[esolve] |
                    -h[uman-readable] | -iec | -j[son] | -p[retty] |
                    -f[amily] { inet | inet6 | mpls | bridge | link } |
                    -4 | -6 | -M | -B | -0 |
                    -l[oops] { maximum-addr-flush-attempts } | -br[ief] |
                    -o[neline] | -t[imestamp] | -ts[hort] | -b[atch] [filename] |
                    -rc[vbuf] [size] | -n[etns] name | -N[umeric] | -a[ll] |
                    -c[olor]}
```

***
## Basic commands

- [[#^a12c02|Show available net interfaces]]
- [[#^1dd654|Show current neighbour table]]
- [[#^0cacf1|Show table routes]]

- ###  Show available net interfaces ^a12c02
	- 1: lo loopback interface, points on current machine
	- 2: wire interface that connect machine to other networks
```
bohdan@test-host:~$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:44:3e:d4 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.5/24 metric 100 brd 10.0.2.255 scope global dynamic enp0s3
       valid_lft 322sec preferred_lft 322sec
    inet6 fe80::a00:27ff:fe44:3ed4/64 scope link
       valid_lft forever preferred_lft forever
```

- ### Show current neighbour table ^1dd654
```
bohdan@test-host:~$ ip neigh
10.0.2.6 dev enp0s3 lladdr 08:00:27:fe:47:51 STALE
10.0.2.1 dev enp0s3 lladdr 52:54:00:12:35:00 STALE
10.0.2.3 dev enp0s3 lladdr 08:00:27:47:9b:ad STALE
10.0.2.2 dev enp0s3 lladdr 52:54:00:12:35:00 DELAY
```

- ### Show table routes ^0cacf1
```
bohdan@test-host:~$ ip route
default via 10.0.2.1 dev enp0s3 proto dhcp src 10.0.2.5 metric 100
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.5 metric 100
10.0.2.1 dev enp0s3 proto dhcp scope link src 10.0.2.5 metric 100
10.10.10.11 via 10.0.2.1 dev enp0s3 proto dhcp src 10.0.2.5 metric 100
10.10.10.10 via 10.0.2.1 dev enp0s3 proto dhcp src 10.0.2.5 metric 100
```