# MySQL Note

## Programming

### Basics 

Check if MySQL server is running or not: `ps -ef | grep mysqld`

Start MySQL service: `service mysql start  `

Start MySQL shell: `mysql -u root -p `

**Use database**: `use <database_name>;`

**Get table structure**: `desc <table_name>;` or `show columns from <table_name>; `

Know how this table was created: `show create table <table_name>;`

**Show**: 

```mysql
show database; 

show tables; 
```

**Create**: 

```mysql
create database <database_name>; 

create table employee if not exists (
   empId INT NOT NULL AUTO_INCREMENT, -- if you want to start from 100, write '... AUTO_INCREMENT = 100'
   empName VARCHAR(50) NOT NULL,
   salary DECIMAL NOT NULL,
   deptId INT NOT NULL,
   PRIMARY KEY (empId)
);
```

**Alter**:

```mysql
-- Add a new column. 
alter table employee add sex varchar(10);
-- Add a new column at the first. 
alter table employee add rowId int first; 
-- Add a new column after a specific column. 
alter table employee add sex varchar(10) after empName; 


-- Drop a column.
alter table employee drop rowId;


-- Modify data type of the column. 
alter table employee modify <column_name> <new_data_type>;
-- Modify column name (may as well as data type, null / not null, default value). 
alter table employee change <column_name> <new_column_name> <new_data_type> [null / not null] [default 100]; 
-- Set default value of a column. 
alter table employee alter <column_name> set default <value>; 
-- Drop default value of a column. 
alter table employee alter <column_name> drop default; 


-- Rename a table. 
alter table employee rename to emp;
```

If you want to reposition a column, drop it first then add it. 

**Drop**: 

```mysql
drop database <database_name>;

drop table <table_name>;
```

**Insert into**:

```mysql
insert into employee (empId, empName, salary, deptId) values (1, 'Zhang', 100, 3);
```

**Select ... from**: 

```mysql
-- Return 10 rows from the 3rd row (excluding 3rd). 
-- Note: keep the order of 'limit' and 'offset'.
select * from employee limit 10 offset 3; 
-- same as 
select * from employee limit 3, 10; 


-- Join two tables.
select * from employee e join department d on e.deptId = d.deptId;
-- same as (for inner join or self join)
select * from employee e, department d where e.deptId = d.deptId;


-- REGEXP 
-- empName containing 'an'
select * from employee where empName regexp 'an';
-- same as 
select * from employee where empName like '%an%';
-- starting with 'an': '^an'
-- ending with 'an': 'an$'
-- any single character: . 
-- 0 or more characters: *
-- 1 or more characters: + 
```

**Update ... set**:

```mysql
update employee set salary = 600 where empName='Li';
```

**Delete from**:

```mysql
delete from employee where empId = 1;
```

---

### Transactions

If you plan to use transactions in your MySQL programming, then you need to create your tables in a special way. There are many types of tables, which support transactions, but the **most popular** one is **InnoDB**. 

```mysql
create table employee(
   empId INT NOT NULL AUTO_INCREMENT,
   empName VARCHAR(50) NOT NULL,
   salary DECIMAL NOT NULL,
   deptId INT NOT NULL,
   PRIMARY KEY (empId)
) TYPE = InnoDB;
```

By default, MySQL runs with **autocommit** mode enabled. This means that as soon as you execute a statement that updates (modifies) a table, MySQL stores the update on disk to make it permanent. The change cannot be rolled back. To disable autocommit mode, use the START TRANSACTION statement. 

```mysql
-- start a new transaction
start transaction;
 
-- get latest order number
select @orderNumber := max(orderNUmber) 
from orders;
-- set new order number
set @orderNumber = @orderNumber  + 1;
 
-- insert a new order for customer 145
insert into orders(orderNumber,
                   orderDate,
                   requiredDate,
                   shippedDate,
                   status,
                   customerNumber)
values(@orderNumber,
       now(),
       date_add(now(), INTERVAL 5 DAY),
       date_add(now(), INTERVAL 2 DAY),
       'In Process',
        145);
-- insert 2 order line items
insert into orderdetails(orderNumber,
                         productCode,
                         quantityOrdered,
                         priceEach,
                         orderLineNumber)
values(@orderNumber,'S18_1749', 30, '136', 1),
      (@orderNumber,'S18_2248', 50, '55.09', 2); 
-- commit changes    
commit;   
```

---

### Stored Procedures

**Create a stored procedure**: 

```mysql
 DELIMITER //
 CREATE PROCEDURE GetAllProducts()
   BEGIN
   	SELECT * FROM products;
   END //
 DELIMITER ;
```

The `DELIMITER` statement changes the standard delimiter which is semicolon ( `;` ) to another (`//` in this case). Then the stored procedure will be passed to the server as a whole. The last command ( `DELIMITER;` ) changes the delimiter back to the semicolon (`;`).  

**Call a stored procedure**: `CALL <stored_procedure_name>(); `

---

### Variables

```mysql
-- Set a variable. 
SET @var_name = value; 
-- same as
SELECT @var_name := value;


-- Assign the query result to a variable. 
SET @var_name = (SELECT COUNT(*) FROM mytable);
-- same as 
SELECT COUNT(*) FROM mytable INTO @var_name;
```

---

### Index

```mysql
-- Create a unique index, which means that two rows cannot have the same index value.
-- Omit the UNIQUE keyword to create a simple index, which allows duplicate values in a table.
create unique index empName_index on employee (empName); 

-- Add an index. 
alter table employee add index empName_index (empName);

-- Display index. 
-- The vertical-format output (specified by \G).
show index from <table_name>\G;
```

---

### Handle Duplicates

#### Prevent Duplicates 

```mysql
-- Use UNIQUE. 
CREATE TABLE person_tbl (
   first_name CHAR(20) NOT NULL,
   last_name CHAR(20) NOT NULL,
   sex CHAR(10)
   UNIQUE (last_name, first_name)
);
```

#### Insert Duplicates 

```mysql
-- Use REPLACE rather than INSERT.
-- If the record is new, it is inserted just as with INSERT. If it is a duplicate, the new record replaces the old one.
REPLACE INTO person_tbl (last_name, first_name) VALUES( 'Ajay', 'Kumar');

-- Use INSERT IGNORE rather than INSERT.
-- If the record is new, it is inserted just as with INSERT. If it is a duplicate, the new record will be discarded.
INSERT IGNORE INTO person_tbl (last_name, first_name) VALUES( 'Ajay', 'Kumar');
```

#### Remove Duplicates 

If you have duplicate records in a table and you want to remove all the duplicate records from that table, 

Solution 1: using table replacement.

```mysql 
-- 1. Create a temp table with unduplicated data from the original table. 
CREATE TABLE tmp SELECT last_name, first_name, sex
	FROM person_tbl;
	GROUP BY (last_name, first_name);
-- 2. Drop the original table.
DROP TABLE person_tbl;
-- 3. Rename the temp table to the name of original table.
ALTER TABLE tmp RENAME TO person_tbl;
```

Solution 2: add an INDEX or a PRIMARY KEY to the original table.

```mysql
-- The IGNORE keyword tells MySQL to discard duplicates silently without generating an error.
ALTER IGNORE TABLE person_tbl ADD PRIMARY KEY (last_name, first_name);
```

