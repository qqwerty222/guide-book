## Plan

- [Plan](#plan)
- [Databases](#databases)
  - [Create database](#create-database)
  - [Delete database](#delete-database)
- [Tables](#tables)
  - [Create table](#create-table)
  - [Delete table](#delete-table)
  - [Delete data from the table](#delete-data-from-the-table)
  - [Add column to existing table](#add-column-to-existing-table)
  - [Delete column from existing table](#delete-column-from-existing-table)
  - [Modify column in existing table](#modify-column-in-existing-table)
- [Constraints](#constraints)
  - [NOT NULL / UNIQUE](#not-null--unique)
  - [PRIMARY KEY / FOREIGN KEY](#primary-key--foreign-key)
  - [CHECK](#check)
  - [DEFAULT](#default)
  - [CREATE INDEX (allow db work faster)](#create-index-allow-db-work-faster)
- [Special Fields](#special-fields)
  - [AUTO\_INCREMENT](#auto_increment)

---
## Databases

### Create database
```sql
mysql> CREATE DATABASE test1;
```

### Delete database
```sql
mysql> DROP DATABASE test1;
```

---
## Tables
### Create table
```sql
mysql> CREATE TABLE users(
    -> UserID int, 
    -> Name varchar(20)
    -> );
```

### Delete table
```sql
mysql> DROP TABLE users;
```

### Delete data from the table
```sql
mysql> TRUNCATE TABLE users;
```

### Add column to existing table
```sql
mysql> ALTER TABLE users 
    -> ADD email varchar(20);
```

### Delete column from existing table
```sql
mysql> ALTER TABLE users 
    -> DROP COLUMN UserID;
```

### Modify column in existing table
```sql
mysql> ALTER TABLE users  
    -> MODIFY COLUMN Name varchar(25);
```

---
## Constraints 

- NOT NULL
- UNIQUE
- PRIMARY KEY
- FOREIGN KEY
- CHECK
- DEFAULT
- CREATE INDEX
  
### NOT NULL / UNIQUE
```sql
mysql> CREATE TABLE users(
    -> ID int NOT NULL,
    -> Name varchar(20),
    -> UNIQUE (ID)
    -> );
```

### PRIMARY KEY / FOREIGN KEY
```sql
mysql> CREATE TABLE users(
    -> ID int NOT NULL,
    -> Name varchar(20) NOT NULL,
    -> PRIMARY KEY (ID)
    -> );

mysql> CREATE TABLE orders(
    -> OrderID int NOT NULL,
    -> ProductName varchar(20) NOT NULL,
    -> CustomerID int,
    -> PRIMARY KEY (OrderID),
    -> FOREIGN KEY (CustomerID) REFERENCES users(ID)
    -> );
```

### CHECK
```sql
mysql> CREATE TABLE users(
    -> Name varchar(20) NOT NULL,
    -> Age int,
    -> CHECK (Age>=18)
    -> );
```

### DEFAULT
```sql
mysql> CREATE TABLE users(
    -> Name varchar(20) DEFAULT 'New user'
    -> );
```

### CREATE INDEX (allow db work faster)

```sql
mysql> CREATE TABLE users(
    -> Name varchar(20) NOT NULL,
    -> ID int NOT NULL,
    -> PRIMARY KEY (ID)
    -> );
```

```sql
mysql> CREATE UNIQUE INDEX user_id 
    -> ON users (ID);
```

---
## Special Fields
### AUTO_INCREMENT
Column field will be filled automatically when you add new entry to another columns
```sql
mysql> CREATE TABLE users(
    -> ID int NOT NULL AUTO_INCREMENT,
    -> Name varchar(20) NOT NULL,
    -> PRIMARY KEY (ID)
    -> );
```

Change start number to AUTO_INCREMENT
```sql
mysql> ALTER TABLE users AUTO_INCREMENT=100;
```

