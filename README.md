<h1 align="center">PostgreSQL Notes</h1>

- [Introduction](#introduction)
    - [Data Types:](#data-types)
    - [Create and Drop DB/table:](#create-and-drop-dbtable)
    - [column constraints:](#column-constraints)
    - [data insert to table:](#data-insert-to-table)

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

### column constraints:

- NOT NULL

```sql
CREATE TABLE example (
    name VARCHAR(50) NOT NULL
)
```

- UNIQUE:

```sql
CREATE TABLE example (
    email VARCHAR(100) UNIQUE
)
```

- PRIMARY KEY:

```sql
CREATE TABLE example (
    student_id SERIAL PRIMARY KEY,
)
```

- Foreign key:

```sql
CREATE TABLE example (
    order_id SERIAL PRIMARY KEY,
    product_id INTEGER REFERENCES product(product_id)
)
```
![alt text](./images/foreign-key.png)

- DEFAULT:
  
```sql
CREATE TABLE example (
    status VARCHAR(20) DEFAULT 'active'
)
```

- CHECK


```sql
CREATE TABLE example (
    age INT CHECK (age >= 18) 
)
```

example: 

```sql
create table students(
	student_id SERIAL PRIMARY KEY,
	full_name VARCHAR(100) NOT NULL,
	email VARCHAR(100) UNIQUE,
	age INT CHECK (age >= 18),
	status VARCHAR(20) DEFAULT 'active'
)
```

```sql
CREATE TABLE students (
    student_id SERIAL,
    username VARCHAR(100) NOT NULL,
    email VARCHAR(100),
    age INT CHECK (age >= 18),
    status VARCHAR(20) DEFAULT 'active',

    PRIMARY KEY (student_id),
    UNIQUE (username, email)
);
```

### data insert to table:

- single row insert: 

```sql
CREATE TABLE person (
	id SERIAL PRIMARY KEY,
	username VARCHAR(50) UNIQUE,
	email VARCHAR(50) UNIQUE,
	age INT CHECK (age >= 20),
	isActive BOOLEAN DEFAULT true
);

INSERT INTO person (username, email, age)
VALUES ('tamim', 'tamim@gmail.com', 28);
```

- multi row insert:

```sql
INSERT INTO person (username, email, age)
VALUES
    ('alice', 'alice@gmail.com', 24),
    ('bob', 'bob@gmail.com', 30),
    ('charlie', 'charlie@gmail.com', 22);
```

