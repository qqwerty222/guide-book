## What is it?

It is a tool that can show you detailed information about connection using SSL/TLS certificates

***
## Installation

### Install on Ubuntu 
```
sudo apt-get install openssl
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
openssl s_client [options] [host:port]
```

***
## Basic commands

- Connect to hostname 
- Specify certificate version for connection
- Show entire certificate chain 

- ###  Connect to hostname 
	- -connect 
```
bohdan@test-host:~$ openssl s_client -connect google.com:443
CONNECTED(00000003)
depth=2 C = US, O = Google Trust Services LLC, CN = GTS Root R1
verify return:1
depth=1 C = US, O = Google Trust Services LLC, CN = GTS CA 1C3
verify return:1
depth=0 CN = *.google.com
verify return:1
---
Certificate chain
 0 s:CN = *.google.com
...
```

- ### Specify certificate version for connection
```
bohdan@test-host:~$ openssl s_client -connect google.com:443 -tls1_3
CONNECTED(00000003)
SSL handshake has read 6745 bytes and written 324 bytes
Verification: OK
---
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
...
```

- ### Show entire certificate chain 
```
bohdan@test-host:~$ openssl s_client -connect google.com:443 -showcerts
CONNECTED(00000003)
```