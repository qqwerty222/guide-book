## What is it?

It is a data transfer tool

***
## Installation

### Install on Ubuntu 
```
bohdan@test-host:~$ sudo apt-get install curl
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
curl [options] [URL...]
```

***
## Basic commands

- Send get request to site
- Download file using curl
- Continue stopped download
- Limit upper bound of the data transfer

- ###  Send get request to site
```
bohdan@test-host:~$ curl google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
```

- ### Download file using curl
	- -O saves the downloaded file
```
bohdan@test-host:~$ curl -O https://curl.se/download/curl-7.87.0.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 4190k  100 4190k    0     0  5759k      0 --:--:-- --:--:-- --:--:-- 5756k
```

```
bohdan@test-host:~$ curl -# -O https://curl.se/download/curl-7.87.0.tar.gz
###########################################################################100.0%
bohdan@test-host:~$ ls
curl-7.87.0.tar.gz  sharedfile  testfile
```

```
bohdan@test-host:~$ curl --silent https://curl.se/download/curl-7.87.0.tar.gz
```

- ### Continues stopped downloading
```
bohdan@test-host:~$ curl -# -O https://curl.se/download/curl-7.87.0.tar.gz
###############################################                            58.9%
^C
bohdan@test-host:~$ curl -C - -O https://curl.se/download/curl-7.87.0.tar.gz
** Resuming transfer from byte position 3530752
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  742k  100  742k    0     0  1579k      0 --:--:-- --:--:-- --:--:-- 1579k
```

- ### Limit upper bound of the data transfer
```
bohdan@test-host:~$ curl --limit-rate 100k -O https://curl.se/download/curl-7.87.0.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 4190k  100 4190k    0     0   129k      0  0:00:32  0:00:32 --:--:--  305k
```