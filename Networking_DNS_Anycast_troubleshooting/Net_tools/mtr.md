## What is it?

It is a tool that combine functionality of ping and traceroute. 
Shows ping statistic for different hosts in real-time

***
## Installation

### Install on Ubuntu 
```
bohdan@test-host:~$ sudo apt-get install mtr
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
 mtr [options] hostname
```

***
## Basic commands

- Ping route to hostname]]

- ###  Ping route to hostname ^96e040
	- This program can be managed in real-time
```
bohdan@test-host:~$ mtr 10.0.2.6
                                               My traceroute  [v0.95]
test-host (10.0.2.5) -> 10.0.2.6 (10.0.2.6)                                                 2023-01-18T15:23:56+0000
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                                                            Packets               Pings
Host                                      Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. ns1.testdomain.com                    0.0%     2    1.3   1.1   1.0   1.3   0.2

```
