## Plan

- [Plan](#plan)
- [Server list](#server-list)
- [Create configuration files](#create-configuration-files)
  - [Create dir for configuration files](#create-dir-for-configuration-files)
  - [Master (mysql-0) config](#master-mysql-0-config)
  - [Slave (mysql-1) config](#slave-mysql-1-config)
  - [Slave (mysql-2) config](#slave-mysql-2-config)
- [Run mysql containers](#run-mysql-containers)
  - [Master (mysql-0)](#master-mysql-0)
  - [Slave (mysql-1)](#slave-mysql-1)
  - [Slave (mysql-2)](#slave-mysql-2)
- [Connect mysql servers](#connect-mysql-servers)
  - [Create users for slaves](#create-users-for-slaves)
  - [Connect slaves to master](#connect-slaves-to-master)
- [Test replication](#test-replication)
  - [Fill table on master](#fill-table-on-master)
  - [Check table on slave](#check-table-on-slave)

---
## Server list

| Name | Ip address | Status |
|------|------------|--------|
| mysql-0 | 172.17.0.2 | Master | 
| mysql-1 | 172.17.0.3 | Slave  |
| mysql-2 | 172.17.0.4 | Slave  |

___
## Create configuration files

### Create dir for configuration files
```bash
╭─bohdan@PF2FXPPG ~
╰─$ mkdir mysql

╭─bohdan@PF2FXPPG ~
╰─$ mkdir mysql/mysql-0
    ...
```

### Master (mysql-0) config
```bash
# mysql-0/master.cnf

[mysqld]
skip-name-resolve
default_authentication_plugin=mysql_native_password

server-id       = 1
log_bin         = 1
binlog_format   = ROW
binlog_do_db    = defaultDB
```

### Slave (mysql-1) config
```bash
# mysql-1/slave.cnf

[mysqld]
skip-name-resolve
default_authentication_plugin=mysql_native_password

server-id = 2
log_bin = 1
binlog_do_db = defaultDB
```

### Slave (mysql-2) config
```bash
# mysql-2/slave.cnf

[mysqld]
skip-name-resolve
default_authentication_plugin=mysql_native_password

server-id = 3
log_bin = 1
binlog_do_db = defaultDB
```

---
## Run mysql containers

### Master (mysql-0)
```bash
╭─bohdan@PF2FXPPG ~/mysql
╰─$ docker run -d \
        --name mysql-0 \
        -v /home/bohdan/mysql/mysql-0/master.cnf:/etc/mysql/my.cnf \
        -e MYSQL_DATABASE=defaultDB \
        -e MYSQL_ROOT_PASSWORD=password \
        mysql
```

### Slave (mysql-1)
```bash
╭─bohdan@PF2FXPPG ~/mysql
╰─$ docker run -d \
        --name mysql-1\
        -v /home/bohdan/mysql/mysql-1/slave.cnf:/etc/mysql/my.cnf \
        -e MYSQL_DATABASE=defaultDB \
        -e MYSQL_ROOT_PASSWORD=password \
        mysql
```

### Slave (mysql-2)
```bash
╭─bohdan@PF2FXPPG ~/mysql
╰─$ docker run -d \
        --name mysql-2 \
        -v /home/bohdan/mysql/mysql-2/slave.cnf:/etc/mysql/my.cnf\
        -e MYSQL_DATABASE=defaultDB \
        -e MYSQL_ROOT_PASSWORD=password \
        mysql
```

--- 
## Connect mysql servers

### Create users for slaves
```bash
╭─bohdan@PF2FXPPG ~
╰─$ mysql -uroot -p --host 172.17.0.2
```

```sql
mysql> CREATE USER 'slave1'@'172.17.0.3' IDENTIFIED BY 'password';
mysql> CREATE USER 'slave2'@'172.17.0.4' IDENTIFIED BY 'password';

mysql> GRANT REPLICATION SLAVE ON *.* TO 'slave1'@'172.17.0.3';
mysql> GRANT REPLICATION SLAVE ON *.* TO 'slave2'@'172.17.0.4';
mysql> FLUSH PRIVILEGES;
```

### Connect slaves to master

mysql-1
```bash
╭─bohdan@PF2FXPPG ~
╰─$ mysql -uroot -p --host 172.17.0.3
```

```sql
mysql> CHANGE REPLICATION SOURCE TO
    -> SOURCE_HOST='172.17.0.2',
    -> SOURCE_USER='slave1',
    -> SOURCE_PASSWORD='password'
    -> ;

mysql> START REPLICA;
```

mysql-2
```bash
╭─bohdan@PF2FXPPG ~
╰─$ mysql -uroot -p --host 172.17.0.4
```

```sql
mysql> CHANGE REPLICATION SOURCE TO
    -> SOURCE_HOST='172.17.0.2',
    -> SOURCE_USER='slave2',
    -> SOURCE_PASSWORD='password'
    -> ;

mysql> START REPLICA;
```

---
## Test replication

### Fill table on master
```bash
╭─bohdan@PF2FXPPG ~/mysql
╰─$ mysql -uroot -p --host 172.17.0.2
```

```sql
mysql> use defaultDB;

mysql> CREATE TABLE test(
    -> ID int NOT NULL AUTO_INCREMENT,
    -> Name varchar(20) NOT NULL,
    -> PRIMARY KEY (ID)
    -> );

mysql> INSERT INTO test (Name) VALUES ('John'), ('Thomas'), ('David');
```

### Check table on slave
```bash
╭─bohdan@PF2FXPPG ~
╰─$ mysql -uroot -p --host 172.17.0.3
```

```sql
mysql> use defaultDB;

mysql> SELECT * FROM test;
+----+--------+
| ID | Name   |
+----+--------+
|  1 | John   |
|  2 | Thomas |
|  3 | David  |
+----+--------+
```