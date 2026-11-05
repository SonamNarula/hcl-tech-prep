# CAPGEMINI SQL QUICKBOOK
## MySQL basics • Theory Q&A • Query practice • Solutions
**Date:** 5 November 2026 | **Dialect:** MySQL 8.x  
**Goal:** Resume-level SQL revision for a campus assessment and basic technical interview.

> Focus on basic queries and being able to explain them. Execute every query yourself; don't just read the answers.

---

## 1. SQL fundamentals

**SQL (Structured Query Language)** is used to define, retrieve, manipulate and control data in relational databases.

- **Database:** organised collection of data.
- **Table:** data arranged in rows and columns.
- **Row/record:** one complete entry.
- **Column/field:** one attribute.
- **Schema:** logical structure of database objects.
- **DBMS:** software to manage databases.
- **RDBMS:** DBMS that organises data in related tables.

### SQL command categories

| Category | Purpose | Commands |
|---|---|---|
| DDL | Define/change structure | `CREATE`, `ALTER`, `DROP`, `TRUNCATE` |
| DML | Modify table data | `INSERT`, `UPDATE`, `DELETE` |
| DQL | Retrieve data | `SELECT` |
| DCL | Manage permissions | `GRANT`, `REVOKE` |
| TCL | Manage transactions | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

## 2. Practice database

Run this setup in MySQL. All questions and answers below use these tables.

```sql
CREATE DATABASE capgemini_sql;
USE capgemini_sql;

CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(50) NOT NULL
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    department_id INT,
    salary DECIMAL(10,2),
    city VARCHAR(50),
    age INT,
    manager_id INT,
    FOREIGN KEY (department_id)
        REFERENCES departments(department_id)
);

INSERT INTO departments VALUES
(10, 'IT'), (20, 'HR'), (30, 'Sales'), (40, 'Finance');

INSERT INTO employees VALUES
(1, 'Asha', 10, 60000, 'Jaipur', 24, NULL),
(2, 'Ravi', 20, 45000, 'Delhi', 27, 1),
(3, 'Meera', 10, 75000, 'Jaipur', 29, 1),
(4, 'Kabir', 30, 50000, 'Mumbai', 26, 2),
(5, 'Nisha', 20, 52000, 'Delhi', 25, 2),
(6, 'Arjun', 30, 65000, 'Jaipur', 31, 4),
(7, 'Tara', NULL, 40000, 'Pune', 23, NULL);
```

## 3. SELECT, DISTINCT and aliases

```sql
-- All columns
SELECT * FROM employees;

-- Selected columns
SELECT name, salary FROM employees;

-- Unique cities
SELECT DISTINCT city FROM employees;

-- Column alias
SELECT name, salary AS monthly_salary
FROM employees;
```

`DISTINCT` removes duplicate result rows. When multiple columns are selected, it applies to the combination of those columns.

## 4. WHERE and filtering

```sql
SELECT * FROM employees WHERE salary > 50000;

SELECT * FROM employees
WHERE department_id = 10 AND salary > 60000;

SELECT * FROM employees
WHERE city = 'Jaipur' OR city = 'Delhi';

SELECT * FROM employees
WHERE city IN ('Jaipur', 'Delhi');

SELECT * FROM employees
WHERE salary BETWEEN 45000 AND 65000;

SELECT * FROM employees
WHERE name LIKE 'A%';

SELECT * FROM employees
WHERE department_id IS NULL;
```

| Operator / pattern | Meaning |
|---|---|
| `=`, `<>`, `!=` | Equal / not equal |
| `>`, `<`, `>=`, `<=` | Comparisons |
| `AND`, `OR`, `NOT` | Combine/negate conditions |
| `IN (a,b)` | Matches any listed value |
| `BETWEEN a AND b` | Inclusive range |
| `LIKE 'A%'` | Starts with A |
| `LIKE '%a'` | Ends with a |
| `LIKE '%ar%'` | Contains ar |
| `LIKE '_a%'` | Second character is a |
| `IS NULL` | Value is NULL |

`%` means zero or more characters; `_` means exactly one character. Do not write `column = NULL`; use `IS NULL` or `IS NOT NULL`.

## 5. ORDER BY and LIMIT

```sql
SELECT * FROM employees ORDER BY salary ASC;
SELECT * FROM employees ORDER BY salary DESC;

SELECT * FROM employees
ORDER BY department_id ASC, salary DESC;

-- Three highest-paid employees
SELECT * FROM employees
ORDER BY salary DESC
LIMIT 3;

-- Skip 2 rows, then return 3
SELECT * FROM employees
ORDER BY employee_id
LIMIT 3 OFFSET 2;
```

## 6. Aggregate functions

| Function | Meaning |
|---|---|
| `COUNT(*)` | Counts all rows |
| `COUNT(column)` | Counts non-NULL values in column |
| `SUM(column)` | Sum of non-NULL values |
| `AVG(column)` | Average of non-NULL values |
| `MIN(column)` | Minimum |
| `MAX(column)` | Maximum |

```sql
SELECT COUNT(*) FROM employees;
SELECT AVG(salary) FROM employees;
SELECT MIN(salary), MAX(salary), SUM(salary)
FROM employees;
```

## 7. GROUP BY and HAVING

`GROUP BY` makes groups of rows with the same value(s), usually for aggregation.

```sql
-- Number of employees in each department
SELECT department_id, COUNT(*) AS employee_count
FROM employees
GROUP BY department_id;

-- Average salary per department
SELECT department_id, AVG(salary) AS average_salary
FROM employees
GROUP BY department_id;

-- Departments having at least 2 employees
SELECT department_id, COUNT(*) AS employee_count
FROM employees
GROUP BY department_id
HAVING COUNT(*) >= 2;
```

**WHERE vs HAVING**
- `WHERE` filters individual rows before grouping.
- `HAVING` filters groups after grouping and can use aggregate expressions.

```sql
SELECT department_id, AVG(salary) AS avg_salary
FROM employees
WHERE salary >= 45000
GROUP BY department_id
HAVING AVG(salary) > 50000;
```

## 8. JOINS

A JOIN combines related rows from two or more tables using a condition.

### INNER JOIN — matching rows only

```sql
SELECT e.name, d.department_name
FROM employees e
INNER JOIN departments d
    ON e.department_id = d.department_id;
```

### LEFT JOIN — all left rows, matching right rows

```sql
SELECT e.name, d.department_name
FROM employees e
LEFT JOIN departments d
    ON e.department_id = d.department_id;
```

Unmatched right-side columns are NULL.

### RIGHT JOIN — all right rows

```sql
SELECT e.name, d.department_name
FROM employees e
RIGHT JOIN departments d
    ON e.department_id = d.department_id;
```

### SELF JOIN — table joined with itself

```sql
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m
    ON e.manager_id = m.employee_id;
```

## 9. INSERT, UPDATE and DELETE

```sql
INSERT INTO employees
(employee_id, name, department_id, salary, city, age, manager_id)
VALUES (8, 'Ira', 40, 58000, 'Jaipur', 28, 1);

UPDATE employees
SET salary = 62000
WHERE employee_id = 8;

DELETE FROM employees
WHERE employee_id = 8;
```

**Safety:** Always check the `WHERE` clause before running UPDATE or DELETE. Without it, every row may be affected.

## 10. Keys and constraints

| Constraint/key | Meaning |
|---|---|
| PRIMARY KEY | Uniquely identifies each row; cannot be NULL |
| FOREIGN KEY | References a key in another table; enforces referential integrity |
| UNIQUE | Prevents duplicate values |
| NOT NULL | Disallows NULL |
| CHECK | Enforces a condition |
| DEFAULT | Supplies a default value |

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE,
    name VARCHAR(50) NOT NULL,
    age INT CHECK (age >= 16),
    city VARCHAR(50) DEFAULT 'Jaipur'
);
```

A primary key may contain multiple columns (composite key). A table has one primary-key constraint, but can have multiple UNIQUE and FOREIGN KEY constraints. MySQL permits multiple NULL values in a UNIQUE index.

## 11. Subqueries

A subquery is a query nested inside another query.

```sql
-- Employees earning more than the overall average
SELECT name, salary
FROM employees
WHERE salary > (
    SELECT AVG(salary) FROM employees
);

-- Employees in IT
SELECT name
FROM employees
WHERE department_id = (
    SELECT department_id
    FROM departments
    WHERE department_name = 'IT'
);
```

---

# 12. Theory interview Q&A

### Q1. What is SQL?
**Answer:** SQL stands for Structured Query Language. It is used to create database structures, retrieve data, insert or modify records, and manage permissions and transactions in relational databases.

### Q2. What is MySQL?
**Answer:** MySQL is a relational database management system (RDBMS) that uses SQL. SQL is the language; MySQL is the database software.

### Q3. DBMS vs RDBMS?
**Answer:** DBMS is software for managing data. An RDBMS stores data in related tables and supports relationships using keys. MySQL is an RDBMS.

### Q4. What is a primary key?
**Answer:** A primary key uniquely identifies each row. It cannot be NULL. It can consist of one column or multiple columns.

### Q5. What is a foreign key?
**Answer:** A foreign key is a column or set of columns that references a key in another table, helping maintain referential integrity.

### Q6. Primary key vs UNIQUE key?
**Answer:** Both enforce uniqueness. A primary key cannot be NULL and identifies each row. A table has one primary-key constraint but can have multiple UNIQUE constraints. In MySQL, a UNIQUE index permits multiple NULL values.

### Q7. What is NULL?
**Answer:** NULL represents a missing, unknown or unavailable value. It is not zero or an empty string. Test it using `IS NULL` / `IS NOT NULL`.

### Q8. What is a constraint?
**Answer:** A rule applied to table columns to maintain data accuracy and integrity. Examples: PRIMARY KEY, FOREIGN KEY, UNIQUE, NOT NULL, CHECK and DEFAULT.

### Q9. What is normalization?
**Answer:** Normalization organises tables to reduce unnecessary duplication and prevent insertion, update and deletion anomalies.
- **1NF:** Each cell contains an atomic value; no repeating groups.
- **2NF:** In 1NF, and every non-key attribute depends on the whole candidate key (no partial dependency on part of a composite key).
- **3NF:** In 2NF, and non-key attributes do not have transitive dependencies on candidate keys.

### Q10. What is a JOIN?
**Answer:** A JOIN combines related rows from tables using a matching condition, commonly a primary-key/foreign-key relationship.

### Q11. INNER JOIN vs LEFT JOIN?
**Answer:** INNER JOIN returns only matching rows. LEFT JOIN returns all rows from the left table and matching rows from the right; unmatched right-side columns are NULL.

### Q12. WHERE vs HAVING?
**Answer:** WHERE filters rows before grouping. HAVING filters groups after GROUP BY and can use aggregate expressions.

### Q13. DELETE vs TRUNCATE vs DROP?
**Answer:**
- `DELETE` removes rows and can use WHERE.
- `TRUNCATE TABLE` removes all rows but keeps the table structure.
- `DROP TABLE` removes the table and its structure.
In MySQL, TRUNCATE is DDL and causes an implicit commit; it is not generally rolled back like a normal transactional DELETE.

### Q14. UNION vs UNION ALL?
**Answer:** UNION combines result sets and removes duplicate rows. UNION ALL keeps duplicates and is generally faster when duplicate removal is unnecessary. Each SELECT must return the same number of compatible columns.

### Q15. COUNT(*) vs COUNT(column)?
**Answer:** COUNT(*) counts all rows. COUNT(column) counts only rows where that column is not NULL.

### Q16. What is GROUP BY?
**Answer:** GROUP BY groups rows sharing the same value(s), allowing aggregate functions such as COUNT, SUM and AVG to be calculated for each group.

### Q17. What is an index?
**Answer:** An index is a data structure that helps the database find rows faster. It uses storage and can slow INSERT, UPDATE and DELETE because indexes also need updating.

### Q18. What is a subquery?
**Answer:** A query nested inside another query. Its result can be used by the outer query for filtering or retrieval.

### Q19. What is a view?
**Answer:** A view is a named query that behaves like a virtual table. It generally stores the query definition rather than a separate copy of the result.

### Q20. What is a transaction?
**Answer:** A transaction is a logical unit of database work. Changes can be committed or uncommitted changes can be rolled back.

### Q21. Explain ACID.
**Answer:**
- **Atomicity:** All operations succeed or none do.
- **Consistency:** A transaction preserves database rules and constraints.
- **Isolation:** Concurrent transactions are controlled to avoid improper interference.
- **Durability:** Committed changes persist, subject to database durability configuration.

### Q22. COMMIT vs ROLLBACK?
**Answer:** COMMIT makes the current transaction's changes permanent. ROLLBACK undoes changes in the current uncommitted transaction.

### Q23. CHAR vs VARCHAR?
**Answer:** CHAR is fixed-length; VARCHAR is variable-length. VARCHAR is useful for text whose length varies.

### Q24. Database vs table?
**Answer:** A database is a container for organised data and database objects. A table is one structure inside a database, containing rows and columns.

### Q25. What is an alias?
**Answer:** A temporary name given to a column or table within a query. Example: `SELECT salary AS monthly_salary FROM employees;`.

---

# 13. Query practice — solve before checking

Use the sample tables from Section 2.

## A. SELECT and filtering
1. Display all employee records.
2. Display only employee names and salaries.
3. Display unique cities.
4. Find employees earning more than 50,000.
5. Find employees living in Jaipur.
6. Find employees living in Jaipur or Delhi.
7. Find employees whose salary is between 45,000 and 65,000.
8. Find employees whose names start with A.
9. Find employees whose department_id is NULL.
10. Find employees whose department_id is 10, 20 or 30.

## B. Sorting and aggregation
11. Display employees in descending order of salary.
12. Display the three highest-paid employees.
13. Count all employees.
14. Find the average salary.
15. Find maximum and minimum salary.
16. Count employees in each department.
17. Find average salary in each department.
18. Display departments having at least two employees.
19. Find total salary per department.
20. Count employees in each city.

## C. JOIN and subquery
21. Display each employee's name with department name.
22. Display all departments, including departments with no employees.
23. Find employees without a matching department.
24. Display employee name and manager name.
25. Find employees earning more than average salary.
26. Find employees who belong to IT.

## D. Data modification
27. Insert a new employee with sample values.
28. Update one employee's salary using employee_id.
29. Delete the sample employee you inserted.

---

# 14. Query practice — answer key

### 1. All records
```sql
SELECT * FROM employees;
```

### 2. Names and salaries
```sql
SELECT name, salary FROM employees;
```

### 3. Unique cities
```sql
SELECT DISTINCT city FROM employees;
```

### 4. Salary above 50,000
```sql
SELECT * FROM employees WHERE salary > 50000;
```

### 5. Employees in Jaipur
```sql
SELECT * FROM employees WHERE city = 'Jaipur';
```

### 6. Jaipur or Delhi
```sql
SELECT * FROM employees WHERE city IN ('Jaipur', 'Delhi');
```

### 7. Salary between 45,000 and 65,000
```sql
SELECT * FROM employees WHERE salary BETWEEN 45000 AND 65000;
```

### 8. Names starting with A
```sql
SELECT * FROM employees WHERE name LIKE 'A%';
```

### 9. NULL department
```sql
SELECT * FROM employees WHERE department_id IS NULL;
```

### 10. Department 10, 20 or 30
```sql
SELECT * FROM employees WHERE department_id IN (10, 20, 30);
```

### 11. Salary descending
```sql
SELECT * FROM employees ORDER BY salary DESC;
```

### 12. Three highest-paid employees
```sql
SELECT * FROM employees ORDER BY salary DESC LIMIT 3;
```

### 13. Total employees
```sql
SELECT COUNT(*) FROM employees;
```

### 14. Average salary
```sql
SELECT AVG(salary) FROM employees;
```

### 15. Maximum and minimum
```sql
SELECT MAX(salary), MIN(salary) FROM employees;
```

### 16. Count per department
```sql
SELECT department_id, COUNT(*) AS employee_count
FROM employees
GROUP BY department_id;
```

### 17. Average salary per department
```sql
SELECT department_id, AVG(salary) AS avg_salary
FROM employees
GROUP BY department_id;
```

### 18. Departments with at least two employees
```sql
SELECT department_id, COUNT(*) AS employee_count
FROM employees
GROUP BY department_id
HAVING COUNT(*) >= 2;
```

### 19. Total salary per department
```sql
SELECT department_id, SUM(salary) AS total_salary
FROM employees
GROUP BY department_id;
```

### 20. Count per city
```sql
SELECT city, COUNT(*) AS employee_count
FROM employees
GROUP BY city;
```

### 21. Employee and department
```sql
SELECT e.name, d.department_name
FROM employees e
INNER JOIN departments d
    ON e.department_id = d.department_id;
```

### 22. All departments, including empty ones
```sql
SELECT d.department_name, e.name
FROM departments d
LEFT JOIN employees e
    ON d.department_id = e.department_id;
```

### 23. Employees without a matching department
```sql
SELECT e.name
FROM employees e
LEFT JOIN departments d
    ON e.department_id = d.department_id
WHERE d.department_id IS NULL;
```

### 24. Employee and manager
```sql
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m
    ON e.manager_id = m.employee_id;
```

### 25. Above-average salary
```sql
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

### 26. Employees in IT
```sql
SELECT e.name
FROM employees e
JOIN departments d
    ON e.department_id = d.department_id
WHERE d.department_name = 'IT';
```

### 27. Insert
```sql
INSERT INTO employees
(employee_id, name, department_id, salary, city, age, manager_id)
VALUES (8, 'Ira', 40, 58000, 'Jaipur', 28, 1);
```

### 28. Update
```sql
UPDATE employees
SET salary = 62000
WHERE employee_id = 8;
```

### 29. Delete
```sql
DELETE FROM employees
WHERE employee_id = 8;
```

---

# 15. Output-based quick test

Assume the original sample data is unchanged.

**Q1**
```sql
SELECT COUNT(*) FROM employees;
```
**Answer:** `7`

**Q2**
```sql
SELECT COUNT(department_id) FROM employees;
```
**Answer:** `6` — one department_id is NULL.

**Q3**
```sql
SELECT COUNT(DISTINCT city) FROM employees;
```
**Answer:** `4` — Jaipur, Delhi, Mumbai, Pune.

**Q4**
```sql
SELECT MAX(salary) FROM employees;
```
**Answer:** `75000`

**Q5**
```sql
SELECT COUNT(*) FROM employees WHERE city = 'Jaipur';
```
**Answer:** `3`

**Q6**
```sql
SELECT COUNT(*) FROM departments;
```
**Answer:** `4`

---

# 16. Common mistakes

1. Writing `= NULL` instead of `IS NULL`.
2. Forgetting WHERE in UPDATE or DELETE.
3. Using WHERE to filter aggregate results instead of HAVING.
4. Forgetting the JOIN condition and accidentally producing a Cartesian product.
5. Assuming COUNT(column) counts NULL values.
6. Forgetting BETWEEN includes both boundaries.
7. Forgetting quotes around string values.
8. Confusing primary and foreign keys.
9. Assuming UNION and UNION ALL behave identically.
10. Using SELECT * when only a few columns are needed.

---

# 17. Practice websites

| Resource | Use | Link |
|---|---|---|
| SQLBolt | Interactive beginner lessons | https://sqlbolt.com/lessons |
| SQL Practice | Query exercises | https://www.sql-practice.com/ |
| HackerRank SQL | Assessment-style SQL practice | https://www.hackerrank.com/domains/sql |
| W3Schools SQL | Syntax reference and editor | https://www.w3schools.com/sql/ |
| LeetCode SQL 50 | Structured SQL practice | https://leetcode.com/studyplan/top-sql-50/ |

**Suggested order:** SQLBolt → SQL Practice → HackerRank basic SQL. Use SQL 50 if time remains.

---

# 18. Final checklist

- [ ] Explain SQL, MySQL, DBMS and RDBMS.
- [ ] Explain DDL, DML, DQL, DCL and TCL.
- [ ] Write SELECT, DISTINCT and aliases.
- [ ] Filter using WHERE, AND, OR, IN, BETWEEN and LIKE.
- [ ] Handle NULL correctly.
- [ ] Sort with ORDER BY and limit results.
- [ ] Use COUNT, SUM, AVG, MIN and MAX.
- [ ] Use GROUP BY and HAVING.
- [ ] Explain and write INNER JOIN and LEFT JOIN.
- [ ] Write basic INSERT, UPDATE and DELETE safely.
- [ ] Explain primary key, foreign key and constraints.
- [ ] Explain DELETE vs TRUNCATE vs DROP.
- [ ] Explain UNION vs UNION ALL.
- [ ] Explain normalization at a basic level.
- [ ] Explain indexes, views, subqueries and ACID at a basic level.
- [ ] Solve at least 20 practice questions without looking at the answer key.

## What is enough for this preparation?

You should be able to read a basic SQL prompt, identify the table/columns/conditions, write the query independently, and explain common SQL concepts in simple language.

**Priority:** Basic queries + joins + grouping + theory answers. Master these before spending time on advanced SQL.
