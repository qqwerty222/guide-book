## What is it?

Tool that can show you status of processes that current system running. (Process Status).

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
ps [options]
```

***
## Basic commands

- Show processes current shell runs
- Show detailed info
- Show all processes
- Show processes owned by current user
- Search for processes

- ###  Show processes current shell runs 
	- PID - process id 
	- TTY - terminal type that user logged into
	- TIME - amount of CPU time this process has been running
		- 00:00:00 doesn't mean that process doesn't use CPU, maybe some parent process use
	- CMD - command that launched process
```
bohdan@test-dns:~$ ps
    PID TTY          TIME CMD
   1059 pts/0    00:00:00 bash
   1139 pts/0    00:00:00 ps
```

- ### Show detailed info
	- -F extra full
```
UID          PID    PPID  C    SZ   RSS PSR STIME TTY          TIME CMD
bohdan      1059    1058  0  2183  5588   0 10:30 pts/0    00:00:00 -bash
bohdan      1317    1059  0  2517  1588   0 11:18 pts/0    00:00:00 ps -F
```

- ### Show all processes
	- -e either
	- -A all (identical to -e)
	- -a except session leaders  and non-terminal commands
```
bohdan@test-dns:~$ ps -e
    PID TTY          TIME CMD
      1 ?        00:00:01 systemd
      2 ?        00:00:00 kthreadd
      3 ?        00:00:00 rcu_gp
      4 ?        00:00:00 rcu_par_gp
      5 ?        00:00:00 slub_flushwq
    ...

	bohdan@test-dns:~$ ps -a
    PID TTY          TIME CMD
   1290 pts/0    00:00:00 ps
```

- ### Show processes owned by current user
```
bohdan@test-dns:~$ ps -x
    PID TTY      STAT   TIME COMMAND
    956 ?        Ss     0:00 /lib/systemd/systemd --user
    957 ?        S      0:00 (sd-pam)
   1058 ?        R      0:00 sshd: bohdan@pts/0
   1059 pts/0    Ss     0:00 -bash
   1294 pts/0    R+     0:00 ps -x
```

- ### Search for process by name
	- -C process name
	- -p process ID
	- -G group name
	- -g group ID
```
bohdan@test-dns:~$ ps -C bash
    PID TTY          TIME CMD
   1059 pts/0    00:00:00 bash

bohdan@test-dns:~$ ps -p 1059 1058 956
    PID TTY      STAT   TIME COMMAND
    956 ?        Ss     0:00 /lib/systemd/systemd --user
   1058 ?        S      0:00 sshd: bohdan@pts/0
   1059 pts/0    Ss     0:00 -bash

bohdan@test-dns:~$ ps -G bohdan
    PID TTY          TIME CMD
    956 ?        00:00:00 systemd
    957 ?        00:00:00 (sd-pam)
   1058 ?        00:00:00 sshd
   1059 pts/0    00:00:00 bash
   1299 pts/0    00:00:00 ps
   
bohdan@test-dns:~$ ps -g 1
    PID TTY          TIME CMD
      1 ?        00:00:02 systemd
```

