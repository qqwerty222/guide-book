## What is it?

It is a tool that used to scanning IP addresses and ports in a network or detectinstalled applications.

***
## Installation

### Install on Ubuntu 
```
bohdan@test-host:~$ sudo apt-get install nmap
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
nmap [Scan Type(s)] [Options] {target specification}
```

***
## Basic commands

- Scan Subnet
- Scan host

- ###  Scan subnet  ^b97030
```
bohdan@test-host:~$ nmap 10.0.2.0/24
Starting Nmap 7.80 ( https://nmap.org ) at 2023-01-18 15:54 UTC
Nmap scan report for _gateway (10.0.2.1)
Host is up (0.0020s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
53/tcp open  domain

Nmap scan report for 10.0.2.5
Host is up (0.0025s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
22/tcp open  ssh

Nmap scan report for 10.0.2.15
Host is up (0.0028s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

- ### Scan host ^a22c53
```
bohdan@test-host:~$ nmap -Pn 10.0.2.6
Starting Nmap 7.80 ( https://nmap.org ) at 2023-01-18 15:57 UTC
Nmap scan report for 10.0.2.6
Host is up (0.0024s latency).
Not shown: 997 filtered ports
PORT   STATE SERVICE
22/tcp open  ssh
23/tcp open  telnet
53/tcp open  domain
```