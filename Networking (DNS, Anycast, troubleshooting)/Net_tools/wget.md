## What is it?

Wget is a utility for non-interactive download of files from the Web.

***
## Installation

### Install on Ubuntu 
```
bohdan@test-host:~$ sudo apt-get install wget
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
wget [OPTION]... [URL]...
```

***
## Basic commands

- Download file
- Continue filed download
- Download site

- ###  Download file
```
bohdan@test-host:~$ wget https://curl.se/download/curl-7.87.0.tar.gz
--2023-01-18 21:12:47--  https://curl.se/download/curl-7.87.0.tar.gz
Resolving curl.se (curl.se)... 151.101.65.91, 151.101.1.91, 151.101.193.91, ...
Connecting to curl.se (curl.se)|151.101.65.91|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 4291127 (4.1M) [application/x-gzip]
Saving to: ‘curl-7.87.0.tar.gz’

curl-7.87.0.tar.gz   100%[======================>]   4.09M  1.98MB/s    in 2.1s

2023-01-18 21:12:49 (1.98 MB/s) - ‘curl-7.87.0.tar.gz’ saved [4291127/4291127]
```


- ### Continue failed download
	- -c continue
```
bohdan@test-host:~$ wget https://curl.se/download/curl-7.87.0.tar.gz
--2023-01-18 21:15:26--  https://curl.se/download/curl-7.87.0.tar.gz
Resolving curl.se (curl.se)... 151.101.193.91, 151.101.65.91, 151.101.1.91, ...
Connecting to curl.se (curl.se)|151.101.193.91|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 4291127 (4.1M) [application/x-gzip]
Saving to: ‘curl-7.87.0.tar.gz’

curl-7.87.0.tar.gz    28%[=====>                 ]   1.19M  1.43MB/s               ^C
bohdan@test-host:~$ wget -c https://curl.se/download/curl-7.87.0.tar.gz
--2023-01-18 21:15:34--  https://curl.se/download/curl-7.87.0.tar.gz
Resolving curl.se (curl.se)... 151.101.65.91, 151.101.129.91, 151.101.193.91, ...
Connecting to curl.se (curl.se)|151.101.65.91|:443... connected.
HTTP request sent, awaiting response... 206 Partial Content
Length: 4291127 (4.1M), 2892343 (2.8M) remaining [application/x-gzip]
Saving to: ‘curl-7.87.0.tar.gz’

curl-7.87.0.tar.gz   100%[+++++++===============>]   4.09M  12.3MB/s    in 0.2s

2023-01-18 21:15:34 (12.3 MB/s) - ‘curl-7.87.0.tar.gz’ saved [4291127/4291127]

bohdan@test-host:~$
```

- ### Download site
```
bohdan@test-host:~$ wget -m --no-parent www.speedtest.net/
--2023-01-18 21:23:52--  http://www.speedtest.net/
Resolving www.speedtest.net (www.speedtest.net)... 104.16.210.12, 104.16.209.12
Connecting to www.speedtest.net (www.speedtest.net)|104.16.210.12|:80... connected.
HTTP request sent, awaiting response... 301 Moved Permanently
Location: https://www.speedtest.net/ [following]
--2023-01-18 21:23:52--  https://www.speedtest.net/
Connecting to www.speedtest.net (www.speedtest.net)|104.16.210.12|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [text/html]
Saving to: ‘www.speedtest.net/index.html’

bohdan@test-host:~$ ls www.speedtest.net
apps  de  favicon.ico  id          it  ko  pl  robots.txt  th       zh-Hant
ar    es  fr           index.html  ja  nl  pt  sv          zh-Hans
bohdan@test-host:~$
```