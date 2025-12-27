<h1 align="center">PostgreSQL Notes</h1>

- [Introduction](#introduction)
    - [Data Types:](#data-types)
    - [Create and Drop DB/table:](#create-and-drop-dbtable)

# Introduction
PostgreSQL is an open-source, object-relational database management system (ORDBMS).

### Data Types: 
- Integer: 
![alt text](./images/integer.png)

- Boolean: true, false, null

- Character: 

![alt text](./images/character.png)

- Date:

![alt text](./images/date.png)

- UUID:

![alt text](./images/uuid.png)

### Create and Drop DB/table:

- Create and delete DB: 

```sql
create database univercity
drop database univercity
```
- create and delete table:

![alt text](./images/createTable-syntax.png)

```sql
create table students (
	id serial,
	name varchar(50),
	age int,
	isActive boolean,
	dob date
)

-- drop table students
drop table if exists students
```

