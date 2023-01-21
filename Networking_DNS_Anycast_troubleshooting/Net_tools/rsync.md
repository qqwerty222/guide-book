## What is it?

Tool that can synchronize files.

***
## Installation

### Install on Ubuntu 
```
bohdan@test-host:~$ sudo apt-get install rsync
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
Usage: rsync [OPTION]... SRC [SRC]... DEST
  or   rsync [OPTION]... SRC [SRC]... [USER@]HOST:DEST
  or   rsync [OPTION]... SRC [SRC]... [USER@]HOST::DEST
  or   rsync [OPTION]... SRC [SRC]... rsync://[USER@]HOST[:PORT]/DEST
  or   rsync [OPTION]... [USER@]HOST:SRC [DEST]
  or   rsync [OPTION]... [USER@]HOST::SRC [DEST]
  or   rsync [OPTION]... rsync://[USER@]HOST[:PORT]/SRC [DEST]
```

***
## Basic commands

- Synchronize 2 directories
- Syncronize 2 dirs with metadata (creation time, permissions etc.)
- Shaow detailed info while synchronization
- Synchronize dir on remote host

- ###  Synchronize 2 directories
	- -r recursively
```
bohdan@test-host:~$ mkdir dir
bohdan@test-host:~$ touch dir/testfile
bohdan@test-host:~$ mkdir dir2
bohdan@test-host:~$ rsync -r dir/ dir2
bohdan@test-host:~$ ls dir2
testfile
```

- ### Syncronize 2 dirs with metadata (creation time, permissions etc.)
	- -a archive
```
bohdan@test-host:~$ ls -l dir2
total 0
-rw-rw-r-- 1 bohdan bohdan 0 Jan 18 21:57 testfile
bohdan@test-host:~$ rsync -a dir/ dir2
bohdan@test-host:~$ ls -l dir2
total 0
-rw-rw-r-- 1 bohdan bohdan 0 Jan 18 21:35 testfile
bohdan@test-host:~$
```

- ### Show detailed info while synchronization
	- -v verbose
```
bohdan@test-host:~$ touch dir/file{1..10}
bohdan@test-host:~$ rsync -av dir/ dir2
sending incremental file list
./
file1
file10
file2
file3
file4
file5
file6
file7
file8
file9

sent 611 bytes  received 209 bytes  1,640.00 bytes/sec
total size is 0  speedup is 0.00
```

- ### Synchronize dir on remote host
```
bohdan@test-host:~$ mkdir local_dir
bohdan@test-dns:~$ mkdir remote_dir
bohdan@test-host:~$ touch local_dir/{1..10}
bohdan@test-host:~$ rsync -a ~/local_dir/ bohdan@10.0.2.6:~/remote_dir

bohdan@test-dns:~$ ls remote_dir
1  10  2  3  4  5  6  7  8  9
```