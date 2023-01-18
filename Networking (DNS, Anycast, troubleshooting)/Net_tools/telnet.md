## What is it?

Tool used to connect command prompt of remote machines. It has no encryption, so was replaced by ssh.

***
## Installation

### Install client on Ubuntu 
```
bohdan@test-host:~$ sudo apt-get install telnet
```

### Install telnet server 
- install server daemons
```
bohdan@test-dns:~$ sudo apt-get install telnetd xinetd
```
- start service
```
bohdan@test-dns:~$ sudo systemctl start xinetd.service
```
- create server config
```
bohdan@test-dns:~$ sudo vim /etc/xinetd.d/telnet

service telnet
{
disable = no
flags = REUSE
socket_type = stream
wait = no
user = root
server = /usr/sbin/in.telnetd
log_on_failure += USERID
}
```
- restart to apply changes
```
bohdan@test-dns:~$ sudo systemctl restart xinetd
```
- open default port for telnet
```
bohdan@test-dns:~$ sudo ufw allow 23
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
telnet [-4] [-6] [-8] [-E] [-L] [-a] [-d] [-e char] [-l user]
        [-n tracefile] [ -b addr ] [-r] [host-name [port]]
```

***
## Basic commands

- [[#^27ee13|Connect to remote machine]]

- ### Connect to remote machine ^27ee13
```
bohdan@test-host:~$ telnet 10.0.2.6
Trying 10.0.2.6...
Connected to 10.0.2.6.
Escape character is '^]'.
Ubuntu 22.04.1 LTS
test-dns login: bohdan
Password:
Welcome to Ubuntu 22.04.1 LTS (GNU/Linux 5.15.0-57-generic x86_64)

...

  System information as of Wed Jan 18 01:16:28 PM UTC 2023

  System load:  0.07080078125      Processes:               110
  Usage of /:   44.4% of 11.21GB   Users logged in:         1
  Memory usage: 13%                IPv4 address for enp0s3: 10.0.2.6
  Swap usage:   0%
...
bohdan@test-dns:~$ exit
```

