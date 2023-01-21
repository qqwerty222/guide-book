## What is it?

This tool is replacement for netstat. It can dump socket statistics and provide detailed information about sockets that communicate with another systems. 

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
 ss [ OPTIONS ] [ FILTER ]
```

***
## Basic commands

- Show opened to connection sockets
- Show all connections
- Show listening sockets
- Show tcp connections
- Show connections to a specific address


- ###  Show opened to connection sockets
	- Netid - type of connection (u_str - unix stream, u_dgr - unix domain socket, TCP, UDP ...)
	- State - can be ESTAB - Established, UNCONN - Unconnected, LISTEN - Listening
	- Recv-Q - number of received packages in queue
	- Send-Q - number of sended packages in queue
	- Peer Address: Port Process - address and port of remote machine
```
bohdan@test-host:~$ ss
Netid State  Recv-Q Send-Q              Local Address:Port           Peer Address:Port
u_dgr ESTAB  0      0               /run/systemd/notify 18041                * 0
u_dgr ESTAB  0      0      /run/systemd/journal/dev-log 18067                * 0
u_dgr ESTAB  0      0       /run/systemd/journal/socket 18069                * 0
u_str ESTAB  0      0       /run/systemd/journal/stdout 20135                * 20084
u_dgr ESTAB  0      0                                 * 18525                * 18524
u_str ESTAB  0      0                                 * 20528                * 20527
...
```

- ### Show all connections
	- -a all
```
bohdan@test-host:~$ ss -a
Netid  State    Recv-Q   Send-Q  Local Address:Port            Peer Address:Port
nl     UNCONN   0        0       rtnl:systemd-resolve/634      *
nl     UNCONN   0        0       rtnl:kernel                   *
...
```

- ### Show listening sockets
	- - l listening
```
Netid  State    Recv-Q   Send-Q  Local Address:Port            Peer Address:Port
nl     UNCONN   0        0       rtnl:systemd-resolve/634      *
nl     UNCONN   0        0       rtnl:kernel                   *
...
```

- ### Show tcp connections
```
bohdan@test-host:~$ ss -t
State    Recv-Q   Send-Q  Local Address:Port            Peer Address:Port
ESTAB    0        52      10.0.2.5:ssh                  10.0.2.2:55238
```

- ### Show connections to a specific address
```
bohdan@test-host:~$ ss dst 10.0.2.2
Netid  State    Recv-Q   Send-Q  Local Address:Port            Peer Address:Port
tcp    ESTAB    0        52      10.0.2.5:ssh                  10.0.2.2:55238
```