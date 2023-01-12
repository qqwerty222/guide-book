***

# What is DNS?

Domain Name System collect domain names with their ip addresses.

When you trying to reach website using domain name your computer asks DNS what ip address this domain has, and after reach website using ip address of the domain.

![[DNS.png]]
***

# Create DNS server using bind9

## Configuration
- Windows 10
- Oracle VM VirtualBox 7.0.4
- NAT network
	- dns ip  - 10.0.2.6
	- host ip - 10.0.2.5
- Ubuntu 22.04 Server as guest OS

## Set up bind9 DNS server on virtual machine

- (DNS VM) Update apt packages and install bind9
```
bohdan@test-dns:~$ sudo apt update
...
bohdan@test-dns:~$ sudo apt install bind9
...
```

- (DNS VM) Ask for google.com ip address from localhost, make sure bind9 is working
```
bohdan@test-dns:~$ nslookup google.com 127.0.0.1                                                                     Server:         127.0.0.1                                                                                            Address:        127.0.0.1#53                                                                                                                                                                                                              Non-authoritative answer:                                                                                            Name:   google.com                                                                                                   Address: 142.250.203.206                                                                                             Name:   google.com                                                                                                   Address: 2a00:1450:401b:810::200e  
```

- (DNS VM) Allow firewall to use Bind9
```
bohdan@test-dns:~$ sudo ufw allow Bind9                                                                              Rules updated                                                                                                        Rules updated (v6)   
```

- (DNS VM) Edit Bind9 configuration file
	- block listen-on specify requests from what ip addresses bind9 will listen.
	- block forwarders contain ip addresses of DNS servers to which request will be redirected if bind9 doesn't know required domain name.
```sh
# /etc/bind/named.conf.options

options {
    
    listen-on {
        10.0.2.0/24;
    };

    # allow-query { any; }; uncomment to allow requests for anyone

    forwarders {
    8.8.8.8;
    8.8.8.4;
    };

}
```

- (DNS VM) Check syntax of bind9 configuration
	- it shows only errors, if all is correct nothing will be printed.
```
bohdan@test-dns:~$ named-checkconf
bohdan@test-dns:~$
```

- (DNS VM) Restart bind9 to apply changes
```
bohdan@test-dns:~$ sudo systemctl restart bind9
bohdan@test-dns:~$ sudo systemctl status bind9
● named.service - BIND Domain Name Server
     Loaded: loaded (/lib/systemd/system/named.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2023-01-12 14:33:25 UTC; 6s ago
...
```

- (Host VM) Make request to dns 
```
bohdan@test-host:~$ nslookup google.com 10.0.2.6
Server:         10.0.2.6 
Address:        10.0.2.6#53

Non-authoritative answer:
Name:   google.com
Address: 216.58.208.206
Name:   google.com
Address: 2a00:1450:401b:805::200e
```

### Set default DNS server

- (Host VM) Change /etc/resolv.conf
	- changes in this file will be overwrited after every reboot
```
#/etc/resolv.conf
...
nameserver 10.0.2.6
...
```

- (Host VM) Install resolvconf service
```
bohdan@test-host:/$ sudo apt install resolvconf
...
```

- (Host VM) Make resolvconf start every boot
```
bohdan@test-host:/$ sudo systemctl enable resolvconf
```

- (Host VM) Change resolvconf configuration
```
# /etc/resolvconf/resolv.conf.d/head
...
nameserver 10.0.2.6
...
```

- (Host VM) Restart resolvconf to apply changes
```
bohdan@test-host:/$ sudo systemctl restart resolvconf
```

- (Host VM) Use nslookup to see what DNS server current machine use
```
bohdan@test-host:/$ nslookup google.com
Server:         10.0.2.6
Address:        10.0.2.6#53
```