## What is it? 

**dig is a flexible tool that helps in process of DNS servers troubleshooting.

It performs DNS lookups and displays the answers that are returned from the name server(s) that were queried.

Unless it is told to query a specific name server, dig tries each of the servers listed in /etc/resolv.conf. If no usable server addresses are found, dig sends the query to the local host. When no command-line arguments or options are given, dig performs an NS query for "." (the root).

***
## Installation

It is a part of dnsutils package, and you can install it using package manager.

Install on Ubuntu for example:
```
sudo apt-get install dnsutils
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
dig [@global-server] [domain] [q-type] [q-class] {q-opt}
            {global-d-opt} host [@local-server] {local-d-opt}
            [ host [@local-server] {local-d-opt} [...]]

dig [server] [name] [type]
```

- server   is the name or IP address of the name server to query.
	- When the supplied server argument is a hostname, dig resolves that name before querying that name server.
- name   is the name of the resource record that is to be looked up.
-  type    indicates what type of query is required - ANY, A, MX, SIG, etc.
	- If no type argument is supplied, dig performs a lookup for an A record.
***

## Basic commands

- Get default info
- Get short answer
- Get mail domains (MX Records)
- Get only answer section
- Get all of the DNS records types
- Get trace path to the record
- Get domain by ip address (Reverse DNS Lookup)

### Default info
^02c363
```
bohdan@test-host:~$ dig google.com

; <<>> DiG 9.18.1-1ubuntu1.2-Ubuntu <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 30073
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: e5516aa220b1a6a90100000063c46ad5c277fa4dc0d1369c (good)
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             133     IN      A       142.250.203.206

;; Query time: 8 msec
;; SERVER: 10.0.2.6#53(10.0.2.6) (UDP)
;; WHEN: Sun Jan 15 21:06:29 UTC 2023
;; MSG SIZE  rcvd: 83
```
- In first section
	- version of the dig "9.18.1-1ubuntu1.2-Ubuntu"
	- "global options: +cmd" means info will be displayed in command prompt
	- some info about status of the request to DNS
- ANSWER SECTION:
	- google.com - domain name
	- 133 - TTL 
	- IN - class of the record, IN is standart for Internet
	- A - type of record, maps a domain name to ip address
	- 142.250.203.206 - is ip address of the server.
- SERVER: DNS server that answered on request. Default DNS server for current machine

- ### Get short answer
^ce2c24
```
bohdan@test-host:~$ dig google.com +short
142.250.203.206
```

- ### Get mail domains (MX DNS record)
^85c251
```
bohdan@test-host:~$ dig google.com MX

; <<>> DiG 9.18.1-1ubuntu1.2-Ubuntu <<>> google.com MX
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 1819
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 672a6e3d172193fe0100000063c46da7a2d9c9a3f94a8ba7 (good)
;; QUESTION SECTION:
;google.com.                    IN      MX

;; ANSWER SECTION:
google.com.             114     IN      MX      10 smtp.google.com.

;; Query time: 28 msec
;; SERVER: 10.0.2.6#53(10.0.2.6) (UDP)
;; WHEN: Sun Jan 15 21:18:31 UTC 2023
;; MSG SIZE  rcvd: 88
```
Short version
```
bohdan@test-host:~$ dig google.com MX +short
10 smtp.google.com.
```

- ### Get only ANSWER SECTION
^9017ef
```
bohdan@test-host:~$ dig google.com +noall +answer
google.com.             300     IN      A       142.250.203.206
bohdan@test-host:~$
```

- ### Get all the DNS records types
^bd7d74
```
bohdan@test-host:~$ dig google.com ANY +noall +answer
google.com.             40      IN      MX      10 smtp.google.com.
google.com.             178     IN      A       142.250.203.206
```

- ### Get trace path 
^e3117e
```
bohdan@test-host:~$ dig google.com +trace

; <<>> DiG 9.18.1-1ubuntu1.2-Ubuntu <<>> google.com +trace
;; global options: +cmd
.                       514869  IN      NS      f.root-servers.net.
.                       514869  IN      NS      l.root-servers.net.
...
```

- ### Get domain by ip address (Reverse DNS Lookup)
^d0762d
```
bohdan@test-host:~$ dig -x 10.0.2.6 +short
ns1.testdomain.com.
```

