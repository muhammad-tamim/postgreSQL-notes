<h1 align="center">PostgreSQL Notes</h1>

- [Introduction](#introduction)
    - [Data Types:](#data-types)
    - [Create and Drop DB/table:](#create-and-drop-dbtable)
    - [column constraints:](#column-constraints)
    - [data insert to table:](#data-insert-to-table)
    - [Alter:](#alter)
    - [SELECT:](#select)
      - [Scalar Function:](#scalar-function)
      - [Aggregate Function:](#aggregate-function)

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

### Alter:
![alt text](./images/alter.png)

```sql
CREATE TABLE stu(
	id SERIAL,
	name VARCHAR(30),
	email TEXT,
	age INT,
	status TEXT
)
```

- Rename table:

```sql
ALTER TABLE stu RENAME to students
```

- add a table level constrains:

```sql
ALTER TABLE students ADD CONSTRAINT unique_students_email UNIQUE (email)
```

```sql
ALTER TABLE students ADD CONSTRAINT primary_students_id PRIMARY KEY (id)
```

- add column: 

```sql
ALTER TABLE students ADD COLUMN isMarid BOOLEAN
```

- drop Column: 

```sql
ALTER TABLE students DROP COLUMN isMarid
```

- Rename a column: 

```sql
ALTER TABLE students RENAME COLUMN name TO username
```

- Modify a constrains: 

```sql
ALTER TABLE students ALTER COLUMN username TYPE VARCHAR(50) 
```

- add a constrains: 

```sql
ALTER TABLE students ALTER COLUMN email SET NOT NULL
```

```sql
ALTER TABLE students ALTER COLUMN status SET DEFAULT 'active'
```

- drop a constrains: 

```sql
ALTER TABLE students ALTER COLUMN email DROP NOT NULL
```

### SELECT:

![alt text](./images/select.png)

 ```sql
 CREATE TABLE students (
	student_id SERIAL PRIMARY KEY,
	first_name VARCHAR(50) NOT NULL,
	last_name VARCHAR(50) NOT NULL,
	age INT,
	grade CHAR(2),
	course VARCHAR(50),
	email VARCHAR(20) UNIQUE,
	dob DATE,
	blood_group VARCHAR(5),
	country VARCHAR(50)
)
 ```

```sql
INSERT INTO students
(first_name, last_name, age, grade, course, email, dob, blood_group, country)
VALUES
('Tamim', 'Islam', 22, 'A',  'Computer Science',        'tamim@gmail.com',     '2003-05-12', 'O+',  'Bangladesh'),
('Ayesha', 'Rahman', 21, 'A+', 'Software Engineering', 'ayesha@gmail.com',    '2004-02-18', 'A+',  'Bangladesh'),
('Rahul', 'Sharma', 23, 'B+', 'Information Technology','rahul@gmail.com',     '2002-09-30', 'B+',  'India'),
('Sara', 'Khan', 20, 'A',  'Data Science',             'sara@gmail.com',      '2005-01-10', 'AB+', 'Pakistan'),
('John', 'Doe', 24, 'B',  'Computer Engineering',     'john@gmail.com',      '2001-07-25', 'O-',  'USA'),

('Hasan', 'Ali', 22, 'A-', 'Cyber Security',           'hasan@gmail.com',     '2003-03-14', 'B+',  'Bangladesh'),
('Nabila', 'Hossain', 21, 'A', 'Computer Science',    'nabila@gmail.com',    '2004-06-02', 'O+',  'Bangladesh'),
('Arif', 'Ahmed', 23, 'B', 'Information Systems',     'arif@gmail.com',      '2002-11-19', 'A-',  'Bangladesh'),
('Priya', 'Verma', 22, 'A+', 'Software Engineering',  'priya@gmail.com',     '2003-01-08', 'B+',  'India'),
('Rohit', 'Kumar', 24, 'C+', 'Computer Science',      'rohit@gmail.com',     '2001-12-15', 'O+',  'India'),

('Fatima', 'Noor', 20, 'A', 'Data Analytics',          'fatima@gmail.com',    '2005-04-22', 'AB-', 'Pakistan'),
('Imran', 'Sheikh', 23, 'B+', 'Computer Engineering', 'imran@gmail.com',     '2002-08-05', 'O+',  'Pakistan'),
('Zara', 'Malik', 21, 'A+', 'Artificial Intelligence','zara@gmail.com',      '2004-09-11', 'A+',  'UK'),
('Daniel', 'Smith', 25, 'B', 'Information Technology','daniel@gmail.com',    '2000-02-27', 'O-',  'USA'),
('Emily', 'Brown', 22, 'A', 'Data Science',            'emily@gmail.com',     '2003-10-03', 'B-',  'USA'),

('Michael', 'Johnson', 24, 'C', 'Computer Science',   'michael@gmail.com',   '2001-01-19', 'A+',  'USA'),
('Sophia', 'Wilson', 21, 'A+', 'Software Engineering','sophia@gmail.com',    '2004-07-07', 'O+',  'Canada'),
('Liam', 'Martin', 23, 'B+', 'Cloud Computing',       'liam@gmail.com',      '2002-05-28', 'AB+', 'Canada'),
('Olivia', 'Taylor', 22, 'A', 'Cyber Security',       'olivia@gmail.com',    '2003-03-09', 'O-',  'Canada'),
('Noah', 'Anderson', 24, 'B', 'Information Systems',  'noah@gmail.com',      '2001-06-17', 'B+',  'Australia'),

('Ethan', 'Thomas', 23, 'A-', 'Computer Engineering','ethan@gmail.com',      '2002-12-01', 'O+',  'Australia'),
('Mia', 'Moore', 21, 'A+', 'Data Science',             'mia@gmail.com',       '2004-04-14', 'A-',  'Australia'),
('Lucas', 'Jackson', 22, 'B+', 'Software Engineering','lucas@gmail.com',     '2003-08-21', 'AB+', 'Germany'),
('Amelia', 'White', 20, 'A', 'Artificial Intelligence','amelia@gmail.com',   '2005-09-02', 'O+',  'Germany'),
('Benjamin', 'Harris', 25, 'C+', 'Computer Science', 'benjamin@gmail.com',   '2000-11-11', 'B-',  'Germany'),

('Henry', 'Clark', 24, 'B', 'Information Technology', 'henry@gmail.com',     '2001-02-06', 'O-',  'France'),
('Ella', 'Lewis', 22, 'A+', 'Software Engineering',  'ella@gmail.com',       '2003-06-30', 'A+',  'France'),
('Jack', 'Walker', 23, 'B+', 'Cyber Security',        'jack@gmail.com',       '2002-10-18', 'AB-', 'France'),
('Grace', 'Hall', 21, 'A', 'Data Science',             'grace@gmail.com',     '2004-01-26', 'O+',  'France');
```

- SELECT: 

use * to get all of data:
```sql
select * from students 
```

use specific column name to see specific data:

```sql
select first_name, age from students 
```

- alias: 

```sql
select first_name as "First Name", age as user_age from students 
```

- Sorting:

```sql
SELECT * FROM students ORDER BY age DESC 
```

```sql
SELECT * FROM students ORDER BY age ASC
```

- Distinct: 

Get unique countries:

```sql
SELECT DISTINCT country FROM students 
```

- filtering:

filtering with =:

```sql
SELECT * FROM students WHERE country = 'USA'
```

```sql
SELECT first_name, age, course, country FROM students WHERE country = 'USA'
```

filtering with and: 

```sql
SELECT * FROM students WHERE country = 'USA' or country = 'UK'
```

filtering with and:

```sql
SELECT * FROM students WHERE (grade = 'A' or grade = 'B') and (course = 'Computer Science' or course = 'Computer Engineering')
```

```sql
SELECT * FROM students WHERE (country = 'USA' or grade = 'UK') and age = 22 
```

filtering with comparison operator: 

```sql
SELECT * FROM students WHERE age >= 20
```

```sql
SELECT * FROM students WHERE country != 'USA'
-- != or <>
```

filtering with between:

```sql
SELECT * FROM students WHERE age between 20 and 22
-- age >= 20 && age <= 22
```

filtering with in: 

```sql
-- SELECT * FROM students WHERE country = 'USA' or country = 'UK' or country = 'Bangladesh'

SELECT * FROM students WHERE country in ('USA', 'UK', 'Bangladesh')
```

- Like: 

```sql
SELECT * FROM students WHERE first_name like 'A%'
-- first letter will be A
```

```sql
SELECT * FROM students WHERE first_name like '%a'
-- Last letter will be a
```

```sql
SELECT * FROM students WHERE first_name like '%a___'
-- start with a and max 3 letter after a Tamim
```

- ILike: 

same as like but case insensitive

```sql
SELECT * FROM students WHERE email ILIKE 'A%'
```

- NOT: 

```sql
SELECT * FROM students WHERE NOT country = 'Bangladesh'
```

#### Scalar Function: 

![alt text](./images/scaler.png)

- upper, lower, concat, length

```sql
SELECT UPPER(first_name) as first_name_upper, first_name FROM students
```

```sql
SELECT LOWER(first_name) as first_name_upper, first_name FROM students
```

```sql
SELECT CONCAT(first_name, last_name) as full_name, first_name FROM students
```

#### Aggregate Function: 

![alt text](./images/aggregate.png)

- avg, min, max, sum, count 

```sql
SELECT AVG(age) as avg_age FROM students
```

```sql
SELECT MAX(age) FROM students
```

```sql
SELECT COUNT(first_name) FROM students
-- or
-- SELECT COUNT(*) FROM students
```

