## What is it?

Table of processes. Tool that can show you detailed information about processes in real-time.

***
## Installation

### Install on Ubuntu 
```
bohdan@test-dns:~$ sudo apt-get install procps
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
top -hv | -bcEeHiOSs1 -d secs -n max -u|U user -p pid(s) -o field -w [cols]
```

***
## Basic commands

- Start top

- ###  Start top
	- PID - process id 
	- USER - user that runs process
	- PR - priority
	- NI - a nicer representation of priority
	- VIRT  - virtual memory size
	- RES - resident memory in KiB
	- SHR - shared memory in KiB
	- S - process state (status)
	- %CPU - cpu usage
	- %MEM - memory usage
	- TIME+ - total cpu time
	- CMD - command that launched process
```
bohdan@test-dns:~$ top
top - 11:53:02 up  1:23,  1 user,  load average: 0.00, 0.00, 0.00
Tasks: 104 total,   1 running, 103 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  6.2 sy,  0.0 ni, 87.5 id,  0.0 wa,  0.0 hi,  6.2 si,  0.0 st
MiB Mem :   1976.0 total,   1395.6 free,    201.0 used,    379.5 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   1626.2 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
      1 root      20   0  100792  11664   8340 S   0.0   0.6   0:02.05 systemd
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kthreadd
      3 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_gp
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_par_gp
```
