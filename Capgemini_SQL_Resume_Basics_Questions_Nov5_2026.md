# CAPGEMINI SQL QUICK PREPARATION
## Resume-level SQL | 5 November 2026

**Purpose:** SQL is on your resume, so be ready to explain the basics and write common queries independently. This is a focused revision pack, not an advanced SQL course.

**Suggested time:** 45–60 minutes for a first pass, followed by hands-on query practice.

## 1. SQL command categories

| Category | Purpose | Commands |
|---|---|---|
| DQL | Retrieve data | `SELECT` |
| DML | Modify table data | `INSERT`, `UPDATE`, `DELETE` |
| DDL | Define/change structure | `CREATE`, `ALTER`, `DROP`, `TRUNCATE` |
| DCL | Control permissions | `GRANT`, `REVOKE` |
| TCL | Manage transactions | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

## 2. Sample tables

Use these tables for the examples and exercises.

### employees

| employee_id | name | department_id | salary | city |
|---:|---|---:|---:|---|
| 1 | Asha | 10 | 50000 | Jaipur |
| 2 | Rohan | 20 | 65000 | Delhi |
| 3 | Meera | 10 | 55000 | Jaipur |
| 4 | Kabir | 30 | 45000 | Mumbai |
| 5 | Isha | 20 | 70000 | Delhi |
| 6 | Dev | NULL | 40000 | Jaipur |

### departments

| department_id | department_name |
|---:|---|
| 10 | Engineering |
| 20 | Analytics |
| 30 | HR |
| 40 | Finance |

## 3. Essential queries

### SELECT and DISTINCT

```sql
SELECT * FROM employees;

SELECT name, salary
FROM employees;

SELECT DISTINCT city
FROM employees;
```

### WHERE and filtering

```sql
SELECT *
FROM employees
WHERE salary > 50000;

SELECT *
FROM employees
WHERE city = 'Jaipur' AND salary >= 45000;

SELECT *
FROM employees
WHERE city IN ('Jaipur', 'Delhi');

SELECT *
FROM employees
WHERE salary BETWEEN 45000 AND 65000;

-- Names beginning with A
SELECT *
FROM employees
WHERE name LIKE 'A%';

SELECT *
FROM employees
WHERE department_id IS NULL;
```

**Remember:** Use `IS NULL` / `IS NOT NULL`, not `= NULL`.

### ORDER BY and LIMIT

```sql
SELECT name, salary
FROM employees
ORDER BY salary DESC;

SELECT name, salary
FROM employees
ORDER BY salary ASC
LIMIT 3;
```

`ASC` = ascending; `DESC` = descending. `LIMIT` is supported by MySQL.

### Aggregate functions

```sql
SELECT COUNT(*) FROM employees;
SELECT SUM(salary) FROM employees;
SELECT AVG(salary) FROM employees;
SELECT MIN(salary) FROM employees;
SELECT MAX(salary) FROM employees;
```

- `COUNT(*)` counts rows.
- `COUNT(column)` counts non-NULL values in that column.
- `SUM`, `AVG`, `MIN` and `MAX` generally ignore NULL values.

### GROUP BY and HAVING

```sql
SELECT city, COUNT(*) AS employee_count
FROM employees
GROUP BY city;

SELECT city, AVG(salary) AS average_salary
FROM employees
GROUP BY city;

SELECT city, AVG(salary) AS average_salary
FROM employees
GROUP BY city
HAVING AVG(salary) > 50000;
```

- `GROUP BY` creates groups for aggregate calculations.
- `WHERE` filters rows before grouping.
- `HAVING` filters groups after aggregation.

### JOINS

```sql
-- Only employees with a matching department
SELECT e.name, d.department_name
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id;

-- All employees, even without a matching department
SELECT e.name, d.department_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id;
```

- `INNER JOIN`: returns matching rows from both tables.
- `LEFT JOIN`: preserves every left-table row; unmatched right-side columns become NULL.
- `RIGHT JOIN`: preserves every right-table row.
- `CROSS JOIN`: returns combinations of rows from both tables.

### INSERT, UPDATE and DELETE

```sql
INSERT INTO employees (employee_id, name, department_id, salary, city)
VALUES (7, 'Nina', 10, 52000, 'Jaipur');

UPDATE employees
SET salary = 56000
WHERE employee_id = 1;

DELETE FROM employees
WHERE employee_id = 7;
```

**Safety habit:** Before running `UPDATE` or `DELETE`, test the same `WHERE` condition using `SELECT`. Omitting `WHERE` can affect every row.

### Subquery and CASE

```sql
-- Employees earning more than the overall average
SELECT name, salary
FROM employees
WHERE salary > (
    SELECT AVG(salary) FROM employees
);

SELECT name, salary,
       CASE
           WHEN salary >= 65000 THEN 'High'
           WHEN salary >= 45000 THEN 'Medium'
           ELSE 'Low'
       END AS salary_band
FROM employees;
```

## 4. Frequently asked SQL interview questions

Try answering aloud before reading the short answers.

1. **WHERE vs HAVING?** `WHERE` filters rows before grouping; `HAVING` filters groups after aggregation.
2. **DELETE vs TRUNCATE vs DROP?** `DELETE` removes selected rows; `TRUNCATE` removes all rows but keeps the table structure; `DROP` removes the table itself. Transaction/rollback behaviour can depend on the database engine and version.
3. **Primary key vs Foreign key?** A primary key uniquely identifies a row and cannot be NULL. A foreign key references a key in another or the same table and enforces referential integrity.
4. **INNER JOIN vs LEFT JOIN?** INNER returns matching rows; LEFT preserves all rows from the left table and fills unmatched right columns with NULL.
5. **UNION vs UNION ALL?** UNION removes duplicate result rows; UNION ALL retains duplicates.
6. **What is NULL?** Missing, unknown or unavailable data; it is not zero or an empty string. Use `IS NULL`.
7. **COUNT(*) vs COUNT(column)?** COUNT(*) counts rows; COUNT(column) counts non-NULL values.
8. **What is GROUP BY?** Groups rows with the same value(s) for aggregate calculations.
9. **What is a constraint?** A rule that maintains valid data: PRIMARY KEY, FOREIGN KEY, UNIQUE, NOT NULL, CHECK and DEFAULT.
10. **What is normalization?** Organising relational data to reduce redundancy and avoid insertion, update and deletion anomalies.
11. **What is an index?** A data structure that can speed up retrieval, at the cost of storage and additional write overhead.
12. **What is a subquery?** A query nested inside another SQL statement.
13. **CHAR vs VARCHAR?** CHAR is fixed-length; VARCHAR is variable-length up to its declared maximum.
14. **What is a transaction?** A logical unit of database work. ACID means Atomicity, Consistency, Isolation and Durability.
15. **Logical query processing order?** A useful simplified order is `FROM/JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT`. This is logical order, not necessarily the physical execution order.

## 5. Hands-on practice questions

Use the sample tables above. Write each query yourself before checking the answer key.

### Beginner — SELECT and WHERE

1. Display all columns from `employees`.
2. Display only employee names and salaries.
3. Display unique cities.
4. Find employees who live in Jaipur.
5. Find employees whose salary is greater than 50000.
6. Find employees whose salary is between 45000 and 65000.
7. Find employees who live in Jaipur or Delhi.
8. Find employees whose names begin with `M`.
9. Find employees whose `department_id` is NULL.
10. Display employees ordered by salary from highest to lowest.

### Intermediate — aggregation and grouping

11. Count the total number of employees.
12. Find the maximum salary.
13. Find the average salary.
14. Find the total salary paid.
15. Count employees in each city.
16. Find the average salary in each department.
17. Display departments whose average salary is greater than 55000.
18. Find cities having at least two employees.

### Joins and subqueries

19. Display each employee's name and department name.
20. Display all employees, including those without a matching department.
21. Find employees earning more than the overall average salary.
22. Find the highest salary in each department.
23. Find departments that currently have no employees.

### Interview-style query challenges

24. Find the second-highest **distinct** salary.

```sql
SELECT MAX(salary) AS second_highest_salary
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
```

25. Find duplicate city values and their counts.

```sql
SELECT city, COUNT(*) AS city_count
FROM employees
GROUP BY city
HAVING COUNT(*) > 1;
```

26. Find the employee(s) with the highest salary.

```sql
SELECT *
FROM employees
WHERE salary = (SELECT MAX(salary) FROM employees);
```

27. Find the number of employees in each department, including departments with zero employees.

```sql
SELECT d.department_name, COUNT(e.employee_id) AS employee_count
FROM departments AS d
LEFT JOIN employees AS e
    ON d.department_id = e.department_id
GROUP BY d.department_id, d.department_name;
```

## 6. Mini mock test

**Suggested time: 20 minutes.** Attempt without looking at the notes.

1. Which clause filters groups? A) WHERE B) HAVING C) ORDER BY D) DISTINCT
2. Which expression correctly checks for NULL? A) `= NULL` B) `IS NULL` C) `== NULL` D) `LIKE NULL`
3. Which join preserves every row from the left table? A) INNER B) CROSS C) LEFT D) SELF
4. Which command removes the table definition? A) DELETE B) TRUNCATE C) DROP D) UPDATE
5. Which counts non-NULL values in a specified column? A) COUNT(*) B) COUNT(column) C) SUM(column) D) DISTINCT
6. Which set operator retains duplicates? A) UNION B) UNION ALL C) INTERSECT D) DISTINCT
7. Which clause sorts results? A) GROUP BY B) HAVING C) ORDER BY D) WHERE
8. Which key uniquely identifies a row? A) Foreign key B) Primary key C) Index only D) Default
9. Which clause groups rows for aggregate calculations? A) GROUP BY B) ORDER BY C) LIMIT D) DISTINCT
10. What does `AVG(salary)` do with NULL values? A) Treats them as zero B) Ignores them C) Always errors D) Counts them as one

### Answer key

1. B · 2. B · 3. C · 4. C · 5. B · 6. B · 7. C · 8. B · 9. A · 10. B

## 7. Practice websites

| Resource | Best use |
|---|---|
| [SQLBolt](https://sqlbolt.com/lessons) | Short interactive lessons and exercises |
| [SQL Practice](https://www.sql-practice.com/) | SELECT, filtering, joins and aggregation |
| [HackerRank SQL](https://www.hackerrank.com/domains/sql) | Online-judge SQL questions |
| [LeetCode SQL 50](https://leetcode.com/studyplan/top-sql-50/) | Structured interview-style practice |

## 8. Completion checklist

- [ ] I can write SELECT queries without help.
- [ ] I understand WHERE, AND, OR, IN, BETWEEN and LIKE.
- [ ] I can use ORDER BY and LIMIT.
- [ ] I can use COUNT, SUM, AVG, MIN and MAX.
- [ ] I understand GROUP BY and HAVING.
- [ ] I can write INNER JOIN and LEFT JOIN.
- [ ] I understand INSERT, UPDATE and DELETE.
- [ ] I can explain primary key and foreign key.
- [ ] I can explain DELETE vs TRUNCATE vs DROP.
- [ ] I can explain NULL, constraints and normalization at a basic level.
- [ ] I attempted the mini mock test.
- [ ] I solved at least 10 hands-on queries independently.

**Definition of done:** You can explain the basic concepts in simple language and write common queries without copying. For resume-level preparation, prioritise correctness and confidence over rushing into advanced SQL.
