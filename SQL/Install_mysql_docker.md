***
# What is?
MySQL is an open-source relational database management system. Based on SQL (Structured Query Language)

***
## Install MySQL in docker container

- Run container with MySQL
- Install mysql client
- Connect to db in docker using mysql client
- Create new admin account for database
---

### Run MySQL container  
```bash
╭─bohdan@PF2FXPPG ~
╰─$ docker run --name mysql -e MYSQL_ROOT_PASSWORD=password -d mysql:latest
```

---
### Install mysql client

Allow you to start your own server, or connect to another mysql servers

```bash
╭─bohdan@PF2FXPPG ~
╰─$ sudo apt-get install mysql-server
```

---
### Connect to mysql DB

Get ip address of docker container
```bash
╭─bohdan@PF2FXPPG ~
╰─$ docker inspect mysql | grep "IPAddress"
            "IPAddress": "172.17.0.2",
                    "IPAddress": "172.17.0.2",
```

Connect to mysql
```bash
╭─bohdan@PF2FXPPG ~
╰─$ mysql -u root -p --host=172.17.0.2
```

---
### Create new database and admin

Create new database
```sql
mysql> CREATE DATABASE testdb;
mysql> use testdb;
```

Create new user, 172.17.0.1 is docker gateway which you used to connect
```sql
mysql> CREATE USER 'admin1'@'172.17.0.1' IDENTIFIED BY 'password';
```

Give permission to manage database
```sql
mysql> GRANT CREATE, DROP, SELECT, UPDATE, DELETE ON testdb.* TO 'admin1'@'172.17.0.1';
mysql> FLUSH PRIVILEGES;
mysql> SHOW GRANTS FOR 'admin1'@'172.17.0.1';
+------------------------------------------------------------------------------------------+
| Grants for admin1@172.17.0.1                                                             |
+------------------------------------------------------------------------------------------+
| GRANT USAGE ON *.* TO `admin1`@`172.17.0.1`                                              |
| GRANT SELECT, UPDATE, DELETE, CREATE, DROP ON `testdb`.* TO `admin1`@`172.17.0.1` |
+------------------------------------------------------------------------------------------+
```

Log in to new user
```bash
╭─bohdan@PF2FXPPG ~/guide-book ‹main●› 
╰─$ mysql -u admin1 -p --host=172.17.0.2
```