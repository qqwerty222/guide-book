## What is it?

It is a tool that can show you detailed information about hosts

***
## Installation

### Install on Ubuntu 
```
sudo apt-get install host
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
host [-aCdilrTvVw] [-c class] [-N ndots] [-t type] [-W time]
     [-R number] [-m flag] [-p port] hostname [server]
```

***
## Basic commands

- Show info about domain name
- Show info about ip address
- Show detailed info
- Specify type of record
- List all hosts
- Specify amount retries in case of unsuccsesfull one

- ###  Show info about domain name
```
bohdan@test-host:~$ host testdomain.com
testdomain.com has address 10.0.2.15
```

- ### Show info about ip address
```
bohdan@test-host:~$ host 10.0.2.15
15.2.0.10.in-addr.arpa domain name pointer testdomain.com.
15.2.0.10.in-addr.arpa domain name pointer www.testdomain.com.
```

- ### Show detailed info
	- -a all
```
bohdan@test-host:~$ host -a testdomain.com
Trying "testdomain.com"
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 18314
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

;; QUESTION SECTION:
;testdomain.com.                        IN      ANY

;; ANSWER SECTION:
testdomain.com.         604800  IN      A       10.0.2.15
testdomain.com.         604800  IN      SOA     testdomain.com. root.testdomain.com. 2 604800 86400 2419200 604800
testdomain.com.         604800  IN      NS      ns1.testdomain.com.

;; ADDITIONAL SECTION:
ns1.testdomain.com.     604800  IN      A       10.0.2.6

Received 123 bytes from 10.0.2.6#53 in 12 ms
```

- ### Specify type of record
	- -t type
```
bohdan@test-host:~$ host -t NS testdomain.com
testdomain.com name server ns1.testdomain.com.
```

- ### List all hosts
```
bohdan@test-host:~$ host -l testdomain.com
testdomain.com has address 10.0.2.15
testdomain.com name server ns1.testdomain.com.
ns1.testdomain.com has address 10.0.2.6
www.testdomain.com has address 10.0.2.15
```

- ### Specify amount retries in case of first unsuccsesfull
	- -R retry
```
bohdan@test-host:~$ host -R 3 testdomain.com
testdomain.com has address 10.0.2.15
```

