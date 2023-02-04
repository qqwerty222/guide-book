***
SysV is technology of initialization of processes in Linux.
It is using runlevels to define which services will be loaded or killed in different situations

### SysV main folder /etc/init.d

```
root@host:/# ls -l /etc/init.d
total 128
-rw-r--r-- 1 root root 2427 Jan 19  2016 README
-rwxr-xr-x 1 root root 1275 Jan 19  2016 bootmisc.sh
-rwxr-xr-x 1 root root 3807 Jan 19  2016 checkfs.sh
-rwxr-xr-x 1 root root 1098 Jan 19  2016 checkroot-bootclean.sh
-rwxr-xr-x 1 root root 9353 Jan 19  2016 checkroot.sh
-rwxr-xr-x 1 root root 1336 Jan 19  2016 halt
```
There you can see some scripts and README file that describes SysV runlevels.

### SysV runlevels
|0|S
|-|-|
| | |