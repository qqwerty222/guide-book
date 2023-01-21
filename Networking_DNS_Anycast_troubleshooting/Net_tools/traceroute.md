## What is it?

It is a tool that shows you throw what hosts your packets goes before reaching a destination, also measures delays between points.

***
## Installation

### Install on Ubuntu 
```
bohdan@test-host:~$ sudo apt-get install traceroute
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
traceroute [ -46dFITnreAUDV ] [ -f first_ttl ] [ -g gate,... ] [ -i device ] [ -m max_ttl ] [ -N squeries ] [ -p port ] [ -t tos ] [ -l flow_label ] [ -w MAX,HERE,NEAR ] [ -q nqueries ] [ -s src_addr ] [ -z sendwait ] [ --fwmark=num ] host [ packetlen ]
```

***
## Basic commands

- [[#^eff3b6|Check path to hostname]]

- ###  Check path to hostname ^eff3b6
	- -n means doesn't show domain names
	- -q ping every host only one time (3 by default)
	- \* means that host isn't response, but it is reachable
```
bohdan@test-host:~$ -n -q1 google.com                                                                                                                                         
traceroute to google.com (216.58.215.110), 30 hops max, 60 byte packets
 1  10.0.2.1  0.197 ms
 2  *
 3  89.75.24.24  18.654 ms
 4  84.116.253.98  25.468 ms
 5  *
 6  84.116.138.102  31.361 ms
 7  72.14.203.234  29.095 ms
 8  *
 9  142.250.37.209  33.482 ms
10  216.58.215.110  28.974 ms
```

