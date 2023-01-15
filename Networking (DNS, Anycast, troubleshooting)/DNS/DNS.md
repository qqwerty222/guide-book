***

# What is DNS?

Domain Name System collect domain names with their ip addresses.

When you are trying to reach a website using a domain name, your computer asks DNS what ip address this domain has, and after reach the website using ip address of the domain.

![[DNS.png|400]]

---
## DNS records:

The most common types of DNS records:
| DNS Record | Function |
|--------------|-----------|
| A | Used to point a domain or subdomain to an ip address |
| CNAME | Used to point a domain or subdomain to another hostname |
| MX | Mail Exchanger, used to help route email |
| TXT | Used to store text-based information ( verify domain ownership at exmp. ) |

DNS record is a type of data that server use to store information about domain names, ip addresses etc. 

---
## DNS Subdomains:

| www. | google | .com | 
|-----|--------|-----|
| subdomain | domain | TLD - top-level domain |

Subdomains are used to create children domains and control them separately. 
Subdomains examples: 
- help.google.com 
- support.google.com
- mail.google.com

---
## DNS Zone file

It is a text file that maps domain names and ip addresses.
It is human-readable and can be manually edited.
DNS Zones can be forward or reverse.

- ### Forward DNS Lookup Zone
	 This zone contains all the records of domain names to their IP addresses.

	- Define forward zone in bind9 configuration:
	 ```
	#/etc/bind/named.conf.local
	zone "testdomain.com" {
        type master;
        file "/etc/bind/zones/forward.testdomain.com";
	};
	```
	- type "master" means that current machine is main for this zone
	 ^4f9b7a

	 - **Forward DNS Lookup zone configuration 
	 ```
	;
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     testdomain.com. root.testdomain.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL

	; Define the default name server to ns1.testdomain.com
	# dns server who is responsible for this zone
	@       IN      NS      ns1.testdomain.com.

	; Resolve ns1 to server IP address
	; A record for the main DNS
	ns1     IN      A       10.0.2.6

	; Other domains for testdomain.com
	; Create subdomain www
	www     IN      A       10.0.2.15

	# @ means no subdomain, just testdomain.com
	@       IN      A       10.0.2.15
	```
	 - $TTL - Time To Live parameter specify how long any of the records will be stored in cache.
	 - SOA - Start of Authority is a metadata, there can be stored primary name server and email of admin. It also defines records related to its serial number and how long to cache the records.
	 - ; Refresh - how often secondary DNS server will update information from there.
	 - ; Retry - how long to wait before update if previous was unsuccesfull
	 - ; Expire - time after what zone will expire
	 - ; Negative cache TTL - what minimal time record must be stored before remove


- ### Reverse DNS Lookup Zone
	 This zone contains all the records of IP addresses  to their domains.

	- Define reverse zone in bind9 config: ^d498f5
	 ```
	#define reverse zone for 10.0.2.0/24
	zone "2.0.10.in-addr.arpa" {
        type master;
        file "/etc/bind/zones/reverse.testdomain.com";
		};
	```
	- type "master" means that current machine is main for this zone
	
	 - **Reverse DNS lookup zone configuration ^8dd642
	 ```
	#/etc/bind/zones/reverse.testdomain.com
	;
	; BIND reverse data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     testdomain.com. root.testdomain. (
                              1         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL

	; Name server Info for ns1.testdomain.com
	@       IN      NS      ns1.testdomain.com.
	6       IN      PTR     ns1.testdomain.com.
	15      IN      PTR     testdomain.com.
	15      IN      PTR     www.testdomain.com.
	```
	 - All is same as forward zone but in first column we specify last digit of ip address.
	 - And instead of A record use PTR record (pointer)




***

# Create DNS server using bind9

## Configuration
- Windows 10
- Oracle VM VirtualBox 7.0.4
- NAT network
	- DNS ip    - 10.0.2.6
	- host ip   - 10.0.2.5
	- nginx ip - 10.0.2.15
- Ubuntu 22.04 Server as guest OS

## Set up web server(nginx), DNS server(bind9), host

### Set up web server using nginx

#### Install nginx 
- (Ngx VM) Install nginx 
```
bohdan@test-ngx:~$ sudo apt install nginx
```

- (Ngx VM) Test nginx
	- after installation, nginx will show welcome page on localhost:80
```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
```

- (Ngx VM) Create dir for index.html
```
bohdan@test-ngx:~$ sudo mkdir /www
```

- (Ngx VM) Create index file
```
#/www/index.html
<!doctype html>

<html>
        <head>
                <title> Hi it is nginx web server on 10.0.2.15 </title>
        </head>
</html>
```

- (Ngx VM) Set up nginx to listen testdomain.com:80 and answer simple index.html
```
#/etc/nginx/nginx.conf
events {
        worker_connections 1024;
}

http {
        server {
                listen 80;
                server_name testdomain.com;

                root /www;
                index index.html;
        }
}

```

- (Ngx VM) Restart nginx to apply changes
```
bohdan@test-ngx:~$ sudo systemctl restart nginx
bohdan@test-ngx:~$ sudo systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2023-01-13 15:33:37 UTC; 6s ago
...
```

### Set up DNS server using bind9

#### Install bind9 
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
	- block "listen-on" specify requests from what ip addresses bind9 will listen.
		- in my case DNS listens devices from local network.
	- block "forwarders" contain ip addresses of DNS servers to which the request will be redirected if bind9 doesn't know a required domain name.
```sh
# /etc/bind/named.conf.options

options {
    ...
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

#### Set up DNS zones and records
- (DNS VM) Configure bind9 to work only with ipv4 networks.
	- You can do it right after installation of bind9
```
#/etc/default/named
...
# startup options for the server
OPTIONS="-4 -u bind "
...
```

- (DNS VM) Create [[DNS#^4f9b7a|Forward DNS Lookup Zone]]
```
#/etc/bind/named.conf.local
...
zone "testdomain.com" {
        type master;
        file "/etc/bind/zones/forward.testdomain.com";
};
```

- (DNS VM) Create [[DNS#^d498f5|Reverse DNS Lookup Zone]]
	- 2.0.10.X is from 10.0.2.0/24
```
#/etc/bind/named.conf.local
...
zone "2.0.10.in-addr.arpa" {
        type master:
        file "/etc/bind/zones/reverse.testdomain.com";
```

- (DNS VM) Check configuration file syntax
	- if all is correct, there won't be any input
```
bohdan@test-dns:~$ named-checkconf
```

- (DNS VM) Create folder for Zone db
	- -p create parent folder if needed
``` 
bohdan@test-dns:~$ sudo mkdir -p /etc/bind/zones
```

- (DNS VM) Copy default forward zone configuration file as a template
``` 
bohdan@test-dns:~$ sudo cp /etc/bind/db.local \
	/etc/bind/zones/forward.testdomain.com
```

- (DNS VM) Copy default reverse zone configuration file as a template
```
bohdan@test-dns:~$ sudo cp /etc/bind/db.127 \
	/etc/bind/zones/reverse.testdomain.com
```

- (DNS VM) Check is files were copied
```
bohdan@test-dns:~$ ls /etc/bind/zones/
forward.testdomain.com  reverse.testdomain.com
```

- (DNS VM) Set up [[DNS#^4f9b7a|Forward DNS Lookup Zone]]
```
# /etc/bind/zones/forward.testdomain.com
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     testdomain.com. root.testdomain.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL

; Define the default name server to ns1.testdomain.com
@       IN      NS      ns1.testdomain.com.

; Resolve ns1 to server IP address
; A record for the main DNS
ns1     IN      A       10.0.2.6

; Other domains for testdomain.com
; Create subdomain www
www     IN      A       10.0.2.15
@       IN      A       10.0.2.15
```

- (DNS VM) Set up [[DNS#^8dd642|Reverse DNS Lookup Zone]]
```
/etc/bind/zones/reverse.testdomain.com
;
; BIND reverse data file for local loopback interface
;
$TTL    604800
@       IN      SOA     testdomain.com. root.testdomain. (
                              1         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL

; Name server Info for ns1.testdomain.com
@       IN      NS      ns1.testdomain.com.
6       IN      PTR     ns1.testdomain.com.
15      IN      PTR     testdomain.com.
15      IN      PTR     www.testdomain.com.

```

- (DNS VM) Check main configuration file syntax
``` 
bohdan@test-dns:~$ named-checkconf
```

- (DNS VM) Check forward zone configuration file syntax
```
bohdan@test-dns:~$ named-checkzone testdomain.com /etc/bind/zones/forward.testdomain.com
zone testdomain.com/IN: loaded serial 2
OK
```

- (DNS VM) Check reverse zone configuration file syntax
```
bohdan@test-dns:~$ named-checkzone testdomain.com /etc/bind/zones/reverse.testdomain.com
zone testdomain.com/IN: loaded serial 1
OK
```

- (DNS VM) Restart bind9 to apply configuration changes
```
bohdan@test-dns:~$ sudo systemctl restart named
bohdan@test-dns:~$ sudo systemctl status named
● named.service - BIND Domain Name Server
     Loaded: loaded (/lib/systemd/system/named.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2023-01-13 15:56:28 UTC; 10s ago
```

- (DNS VM) Check is firewall know bind9 service
```
bohdan@test-dns:~$ sudo ufw app list
Available applications:
  Bind9
  OpenSSH
```

- (DNS VM) Check firewall status
```
bohdan@test-dns:~$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
Bind9                      ALLOW       Anywhere
Bind9 (v6)                 ALLOW       Anywhere (v6)
```

- (DNS VM) Request ip address of testdomain.com from my DNS
	- 4th Block "ANSWER SECTION" show ip address of testdomain.com
```
bohdan@test-dns:~$ dig @10.0.2.6 testdomain.com

; <<>> DiG 9.18.1-1ubuntu1.2-Ubuntu <<>> @10.0.2.6 testdomain.com
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 47784
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 72dac55ba433177f0100000063c183f8a7e24224cbb2d1dc (good)
;; QUESTION SECTION:
;testdomain.com.                        IN      A

;; ANSWER SECTION:
testdomain.com.         604800  IN      A       10.0.2.15

;; Query time: 0 msec
;; SERVER: 10.0.2.6#53(10.0.2.6) (UDP)
;; WHEN: Fri Jan 13 16:16:56 UTC 2023
;; MSG SIZE  rcvd: 87
```

- (DNS VM) Request ip address of subdomain www.testdomain.com from my DNS
```
bohdan@test-dns:~$ dig @10.0.2.6 www.testdomain.com

; <<>> DiG 9.18.1-1ubuntu1.2-Ubuntu <<>> @10.0.2.6 www.testdomain.com
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 25536
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 8247b2b6767df29c0100000063c1849c9c5a5c47f8fd6cb6 (good)
;; QUESTION SECTION:
;www.testdomain.com.            IN      A

;; ANSWER SECTION:
www.testdomain.com.     604800  IN      A       10.0.2.15

;; Query time: 0 msec
;; SERVER: 10.0.2.6#53(10.0.2.6) (UDP)
;; WHEN: Fri Jan 13 16:19:40 UTC 2023
;; MSG SIZE  rcvd: 91
```

### Test DNS using Host VM
- (Host VM) Make request to DNS 
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

- (Host VM) Change /etc/resolv.conf
	- changes in this file will be overwrote after every reboot
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
bohdan@test-host:/$ nslookup 
Server:         10.0.2.6
Address:        10.0.2.6#53
```

- (Host VM) Check testdomain.com using nslookup 
```
bohdan@test-host:~$ nslookup testdomain.com
Server:         10.0.2.6
Address:        10.0.2.6#53

Name:   testdomain.com
Address: 10.0.2.15
...
```

- (Host VM) Check subdomain www.testdomain.com using nslookup
```
bohdan@test-host:~$ nslookup www.testdomain.com
Server:         10.0.2.6
Address:        10.0.2.6#53

Name:   www.testdomain.com
Address: 10.0.2.15
...
```

- (Host VM) Get answer from my web server using domain name
```
bohdan@test-host:~$ curl testdomain.com
<!doctype html>

<html>
        <head>
                <title> Hi it is nginx web server on 10.0.2.15 </title>
        </head>
</html>

bohdan@test-host:~$ curl www.testdomain.com
<!doctype html>

<html>
        <head>
                <title> Hi it is nginx web server on 10.0.2.15 </title>
        </head>
</html>
```