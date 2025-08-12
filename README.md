# MySQL Subqueries and Nested Queries Project

## Objective

This project demonstrates how to use **subqueries and nested queries** in MySQL for advanced SQL logic. It includes examples using:

- Scalar subqueries
- Correlated subqueries
- Subqueries with `IN`, `EXISTS`, `=`
- Subqueries in `SELECT`, `WHERE`, and `FROM` clauses

---

## Database Schema

### Tables:

- **departments**
- **employees**

### Relationships:

- `employees.department_id` is a foreign key referencing `departments.department_id`

---

## Setup Instructions

### 1. Create the Database

```sql
create database company;
use  company;
create table departments(department_id int primary key ,department_name varchar(50),location varchar(50));
create table employees (employee_id INT primary key,name varchar(50),salary decimal(10,2),department_id int, foreign key (department_id)  REFERENCES departments(department_id));
desc departments;
+-----------------+-------------+------+-----+---------+-------+
| Field           | Type        | Null | Key | Default | Extra |
+-----------------+-------------+------+-----+---------+-------+
| department_id   | int         | NO   | PRI | NULL    |       |
| department_name | varchar(50) | YES  |     | NULL    |       |
| location        | varchar(50) | YES  |     | NULL    |       |
+-----------------+-------------+------+-----+---------+-------+
3 rows in set (0.08 sec)

insert into departments (department_id, department_name, location) values(10, 'HR', 'Hyderabad');
insert into departments (department_id, department_name, location) values(20, 'IT', 'Hyderabad');
insert into departments (department_id, department_name, location) values(30, 'Finance', 'Hyderabad');

desc employees;
+---------------+---------------+------+-----+---------+-------+
| Field         | Type          | Null | Key | Default | Extra |
+---------------+---------------+------+-----+---------+-------+
| employee_id   | int           | NO   | PRI | NULL    |       |
| name          | varchar(50)   | YES  |     | NULL    |       |
| salary        | decimal(10,2) | YES  |     | NULL    |       |
| department_id | int           | YES  | MUL | NULL    |       |
+---------------+---------------+------+-----+---------+-------+
4 rows in set (0.01 sec)

insert into employees (employee_id, name, salary, department_id) values(2, 'Ram', 55000, 10);
insert into employees (employee_id, name, salary, department_id) values(2, 'Ram', 55000, 10);
insert into employees (employee_id, name, salary, department_id) values(3, 'Abhi', 70000, 20);
insert into employees (employee_id, name, salary, department_id) values(4, 'Pandu', 48000, 30);

select * from departments;
+---------------+-----------------+-----------+
| department_id | department_name | location  |
+---------------+-----------------+-----------+
|            10 | HR              | Hyderabad |
|            20 | IT              | Hyderabad |
|            30 | Finance         | Hyderabad |
+---------------+-----------------+-----------+
3 rows in set (0.00 sec)

select * from employees;
+-------------+--------+----------+---------------+
| employee_id | name   | salary   | department_id |
+-------------+--------+----------+---------------+
|           1 | Prasad | 60000.00 |            10 |
|           2 | Ram    | 55000.00 |            10 |
|           3 | Abhi   | 70000.00 |            20 |
|           4 | Pandu  | 48000.00 |            30 |
+-------------+--------+----------+---------------+
4 rows in set (0.00 sec)

select name,salary,(select department_name from departments where departments.department_id = employees.department_id) AS department from employees;
+--------+----------+------------+
| name   | salary   | department |
+--------+----------+------------+
| Prasad | 60000.00 | HR         |
| Ram    | 55000.00 | HR         |
| Abhi   | 70000.00 | IT         |
| Pandu  | 48000.00 | Finance    |
+--------+----------+------------+
4 rows in set (0.01 sec)

select name from employees WHERE department_id IN (select department_id from departments WHERE location = 'Hyderabad');
+--------+
| name   |
+--------+
| Prasad |
| Ram    |
| Abhi   |
| Pandu  |
+--------+
4 rows in set (0.01 sec)

select name, salary from employees WHERE salary = (select max(salary) from employees);
+------+----------+
| name | salary   |
+------+----------+
| Abhi | 70000.00 |
+------+----------+
1 row in set (0.01 sec)

select department_id, department_name from departments d WHERE EXISTS (select 1 from employees e WHERE e.department_id = d.department_id);
+---------------+-----------------+
| department_id | department_name |
+---------------+-----------------+
|            10 | HR              |
|            20 | IT              |
|            30 | Finance         |
+---------------+-----------------+
3 rows in set (0.00 sec)

select name, department_id, salary from employees e WHERE salary > (select avg (salary) from employees WHERE department_id = e.department_id);
+--------+---------------+----------+
| name   | department_id | salary   |
+--------+---------------+----------+
| Prasad |            10 | 60000.00 |
+--------+---------------+----------+
1 row in set (0.01 sec)

 select department_id, avg_salary from(select department_id, avg(salary) as avg_salary from employees GROUP BY department_id) as dept_avg       WHERE avg_salary > 60000;
+---------------+--------------+
| department_id | avg_salary   |
+---------------+--------------+
|            20 | 70000.000000 |
+---------------+--------------+
1 row in set (0.01 sec)
