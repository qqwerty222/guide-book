## What is it? 

Tool that can create connections between 2 clients using TCP or UDP.
Have 3 versions from BSD(I use), GNU and Nmap.

***
## Installation

### BSD netcat
- after installation open port 833 to use it in tests
```
bohdan@test-dns:~$ sudo apt-get install netcat
bohdan@test-host:~$ sudo ufw allow 833/tcp
Rules updated
```

### Nmap netcat
```
bohdan@test-host:~$ sudo apt-get install ncat
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
usage: nc [-46CDdFhklNnrStUuvZz] [-I length] [-i interval] [-M ttl]
          [-m minttl] [-O length] [-P proxy_username] [-p source_port]
          [-q seconds] [-s sourceaddr] [-T keyword] [-V rtable] [-W recvlimit]
          [-w timeout] [-X proxy_protocol] [-x proxy_address[:port]]
          [destination] [port]
```

***
## Basic commands

- Set up tcp connection
- Send file
- Execute commands
- Send your shell
 
 - ### Set up tcp connection (chat) ^c3742a
	 - "-l" means listen
	 - "-v" means verbose, get more info
```sh
bohdan@test-host:~$ sudo nc -lv 833
Listening on 0.0.0.0 833
Connection received on ns1.testdomain.com 47938
hi
```

```sh
bohdan@test-dns:~$ nc 10.0.2.5 833
hi
```

- ### Send file ^04b110
	- there we use Unix operators
	- > to save output in file
	- < to input file in comand
```sh
bohdan@test-host:~$ ls
bohdan@test-host:~$ sudo nc -vl 833 > sharedfile
Listening on 0.0.0.0 833
Connection received on ns1.testdomain.com 33950

bohdan@test-host:~$ ls
sharedfile
```

```sh
bohdan@test-dns:~$ cat > filetosend
just text

bohdan@test-dns:~$ nc 10.0.2.5 833 < filetosend
```

- ### Execute commands
- switch to nmap, because BSD version doen't have availiability to execute ^8907c0
- -e means execute
```sh
bohdan@test-host:~$ sudo ncat -vl 833 -e/bin/bash
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::833
Ncat: Listening on 0.0.0.0:833
Ncat: Connection from 10.0.2.6.
Ncat: Connection from 10.0.2.6:46876.

bohdan@test-host:~$ ls
sharedfile  testfile
```

```sh
bohdan@test-dns:~$ ncat 10.0.2.5 833
ls
sharedfile
touch testfile
```

- ### Send your shell ^2998f8
```sh
bohdan@test-host:~$ sudo ncat -lv 833
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::833
Ncat: Listening on 0.0.0.0:833
Ncat: Connection from 10.0.2.6.
Ncat: Connection from 10.0.2.6:50100.
ls
filetosend
```

```sh
bohdan@test-dns:~$ ncat 10.0.2.5 833 -e /bin/bash
```
