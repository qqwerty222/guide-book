## What is it?

Tool that can give you ecnrypted connection to shell of remote machine via internet. (Secure shell)

***
## Installation

### Install client on Ubuntu 
```
bohdan@test-host:~$ sudo apt-get install ssh
```

### Install openssh server
```
bohdan@test-dns:~$ sudo apt-get install openssh-server
```
- turn on ssh server
```
bohdan@test-dns:~$ sudo systemctl start ssh
bohdan@test-dns:~$ sudo systemctl status ssh
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2023-01-18 11:33:14 UTC; 2h 36min ago
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
usage: ssh [-46AaCfGgKkMNnqsTtVvXxYy] [-B bind_interface]
           [-b bind_address] [-c cipher_spec] [-D [bind_address:]port]
           [-E log_file] [-e escape_char] [-F configfile] [-I pkcs11]
           [-i identity_file] [-J [user@]host[:port]] [-L address]
           [-l login_name] [-m mac_spec] [-O ctl_cmd] [-o option] [-p port]
           [-Q query_option] [-R address] [-S ctl_path] [-W host:port]
           [-w local_tun[:remote_tun]] destination [command [argument ...]]
```

***
## Basic commands

- [[#^1968c8|Connect to remote machine]]


- ###  Connect to remote machine ^1968c8
```
bohdan@test-host:~$ ssh bohdan@10.0.2.6
...
bohdan@10.0.2.6's password:
Welcome to Ubuntu 22.04.1 LTS (GNU/Linux 5.15.0-57-generic x86_64)
  System load:  0.0                Processes:               110
  Usage of /:   44.4% of 11.21GB   Users logged in:         1
  Memory usage: 13%                IPv4 address for enp0s3: 10.0.2.6
  Swap usage:   0%
bohdan@test-dns:~$
```

