## Plan
- [Plan](#plan)
- [Test table](#test-table)
- [INSERT](#insert)
  - [INSERT INTO](#insert-into)
- [SELECT / WHERE](#select--where)
  - [SELECT](#select)
  - [WHERE](#where)
- [AND / OR / NOT, Operators](#and--or--not-operators)
  - [AND](#and)
  - [OR](#or)
  - [NOT](#not)
  - [Combine AND OR NOT](#combine-and-or-not)
- [ORDER](#order)
  - [Ascending ORDER](#ascending-order)
  - [Descending ORDER](#descending-order)
- [UPDATE / DELETE](#update--delete)
- [UPDATE](#update)
- [DELETE](#delete)

---
## Test table

| CustomerID | Name | City | Country |
|------------|------|------|---------|
|1           |David |Berlin|Germany  |
|2           |Jake  |Los Angeles| USA|
|3           |Thomas|London|UK       |
|4           |Christina|Lulea| Sweden|

---
## INSERT 

### INSERT INTO
```sql
mysql> INSERT INTO users (Name, City, Country) VALUES ('John', 'Los Angeles', 'USA');
```

---
## SELECT / WHERE 

### SELECT 
```sql
mysql> SELECT Name, Country FROM users;
+-----------+---------+
| Name      | Country |
+-----------+---------+
| David     | Germany |
| Jake      | USA     |
| Thomas    | UK      |
| Christina | Sweden  |
| John      | USA     |
+-----------+---------+
```

```sql
mysql> SELECT * FROM users;
+------------+-----------+-------------+---------+
| CustomerID | Name      | City        | Country |
+------------+-----------+-------------+---------+
|          1 | David     | Berlin      | Germany |
|          2 | Jake      | Los Angeles | USA     |
|          3 | Thomas    | London      | UK      |
|          4 | Christina | Lulea       | Sweden  |
|          5 | John      | Los Angeles | USA     |
+------------+-----------+-------------+---------+
```

### WHERE
```sql
mysql> SELECT CustomerID, Name FROM users WHERE City='London';
+------------+--------+
| CustomerID | Name   |
+------------+--------+
|          3 | Thomas |
+------------+--------+
```

```sql
mysql> SELECT * FROM users WHERE City='London';
+------------+--------+--------+---------+
| CustomerID | Name   | City   | Country |
+------------+--------+--------+---------+
|          3 | Thomas | London | UK      |
+------------+--------+--------+---------+
```

Available operators:
- =
- \>
- <
- \>=
- <=
- <>          
  (not equal, in some verions can be !=)
- BETWEEN     
  WHERE CustomerID BETWEEN 4 AND 6;
- LIKE      
  WHERE City LIKE 's%';
- IN  
  WHERE City IN ('Paris','London');


---
## AND / OR / NOT, Operators

### AND
```sql
mysql> SELECT * FROM users WHERE Country='Germany' AND City='Berlin';
+------------+-------+--------+---------+
| CustomerID | Name  | City   | Country |
+------------+-------+--------+---------+
|          1 | David | Berlin | Germany |
+------------+-------+--------+---------+
```

### OR
```sql
mysql> SELECT * FROM users WHERE Country='Kiev' OR City='London';
+------------+--------+--------+---------+
| CustomerID | Name   | City   | Country |
+------------+--------+--------+---------+
|          3 | Thomas | London | UK      |
+------------+--------+--------+---------+
```

### NOT
```sql
mysql> SELECT * FROM users WHERE NOT Country='UK';
+------------+-----------+-------------+---------+
| CustomerID | Name      | City        | Country |
+------------+-----------+-------------+---------+
|          1 | David     | Berlin      | Germany |
|          2 | Jake      | Los Angeles | USA     |
|          4 | Christina | Lulea       | Sweden  |
|          5 | John      | Los Angeles | USA     |
+------------+-----------+-------------+---------+
```

### Combine AND OR NOT
```sql
mysql> SELECT * FROM users WHERE Country='Germany' AND (City='Berlin' OR City='Stuttgart');
+------------+-------+--------+---------+
| CustomerID | Name  | City   | Country |
+------------+-------+--------+---------+
|          1 | David | Berlin | Germany |
+------------+-------+--------+---------+
```

```sql
mysql> SELECT * FROM users WHERE NOT Country='Germany' AND NOT Country='USA';
+------------+-----------+--------+---------+
| CustomerID | Name      | City   | Country |
+------------+-----------+--------+---------+
|          3 | Thomas    | London | UK      |
|          4 | Christina | Lulea  | Sweden  |
+------------+-----------+--------+---------+
```

---
## ORDER

### Ascending ORDER
```sql
mysql> SELECT * FROM users ORDER BY CustomerID;
+------------+-----------+-------------+---------+
| CustomerID | Name      | City        | Country |
+------------+-----------+-------------+---------+
|          1 | David     | Berlin      | Germany |
|          2 | Jake      | Los Angeles | USA     |
|          3 | Thomas    | London      | UK      |
|          4 | Christina | Lulea       | Sweden  |
+------------+-----------+-------------+---------+
```

```sql
mysql> SELECT * FROM users ORDER BY City;
+------------+-----------+-------------+---------+
| CustomerID | Name      | City        | Country |
+------------+-----------+-------------+---------+
|          1 | David     | Berlin      | Germany |
|          3 | Thomas    | London      | UK      |
|          2 | Jake      | Los Angeles | USA     |
|          5 | John      | Los Angeles | USA     |
|          4 | Christina | Lulea       | Sweden  |
+------------+-----------+-------------+---------+
```

### Descending ORDER
```sql
mysql> SELECT * FROM users ORDER BY CustomerID DESC;
+------------+-----------+-------------+---------+
| CustomerID | Name      | City        | Country |
+------------+-----------+-------------+---------+
|          4 | Christina | Lulea       | Sweden  |
|          3 | Thomas    | London      | UK      |
|          2 | Jake      | Los Angeles | USA     |
|          1 | David     | Berlin      | Germany |
+------------+-----------+-------------+---------+
```

```sql
mysql> SELECT * FROM users ORDER BY City DESC;
+------------+-----------+-------------+---------+
| CustomerID | Name      | City        | Country |
+------------+-----------+-------------+---------+
|          4 | Christina | Lulea       | Sweden  |
|          2 | Jake      | Los Angeles | USA     |
|          5 | John      | Los Angeles | USA     |
|          3 | Thomas    | London      | UK      |
|          1 | David     | Berlin      | Germany |
+------------+-----------+-------------+---------+
```

---
## UPDATE / DELETE

## UPDATE
```sql
mysql> UPDATE users SET City='San Francisco' WHERE CustomerID=3;
mysql> SELECT * FROM users WHERE CustomerID=5;
+------------+------+---------------+---------+
| CustomerID | Name | City          | Country |
+------------+------+---------------+---------+
|          5 | John | San Francisco | USA     |
+------------+------+---------------+---------+
```

## DELETE
```sql
mysql> DELETE FROM users WHERE CustomerID=5;
mysql> SELECT * FROM users;
+------------+-----------+-------------+---------+
| CustomerID | Name      | City        | Country |
+------------+-----------+-------------+---------+
|          1 | David     | Berlin      | Germany |
|          2 | Jake      | Los Angeles | USA     |
|          3 | Thomas    | London      | UK      |
|          4 | Christina | Lulea       | Sweden  |
+------------+-----------+-------------+---------+

