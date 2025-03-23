![Tinitiate SQLSERVER Training](./sqlserver_tinitiate.png)

# T-SQL Server
&copy; TINITIATE.COM

##### [Back To Context](./README.md)


# SQL SERVER Indexes

## What is an Index in Sql Server?
An index is like the index in a book — it helps you find data faster without scanning the entire table.
It improves query performance, especially for large datasets.

## Types of Indexes in Sql Server with Examples

### Clustered Index: 
* A Clustered Index determines the physical order of data in a table. There can only be one clustered index per table because the data rows are stored in this order.

* Use Case: Ideal for columns often used in range queries, sorting, or joining. Improves performance when querying by the indexed column.

* Example Wording: "Organizes the data rows in the table based on the indexed column, improving query speed when filtering or sorting by that column."


```sql
CREATE CLUSTERED INDEX IX_Emp_Dept
ON employees.emp(deptno);
```
* Data is physically sorted by deptno.

### Non-Clustered Index :

* A Non-Clustered Index creates a separate structure that contains the index key and a pointer to the data row. A table can have multiple non-clustered indexes.

* Use Case: Best for columns frequently used in WHERE clauses, JOIN conditions, or searches but not suited as the main sort order.

* Example Wording: "Speeds up searches and filters by creating a reference map for faster data retrieval without altering the data’s physical order."

```sql
CREATE NONCLUSTERED INDEX IX_Emp_Job
ON employees.emp(job);
```
* Faster searches when filtering by job title.
 
### Unique Index :
* A Unique Index ensures that all values in the indexed column(s) are unique — no duplicate values allowed.

* Use Case: Used to enforce data integrity (e.g., emails, usernames, or any unique field).

* Example Wording: "Enforces uniqueness in the column, ensuring no two records have the same value, thus maintaining data integrity."

```sql
CREATE UNIQUE INDEX IX_Emp_ename
ON employees(ename);
```
* Ensures no two employees have the same email.

### Filtered Index (Selective):
* A Filtered Index is a non-clustered index created on a subset of data based on a filter condition (WHERE clause).

* Use Case: Ideal for improving performance on queries that frequently access only specific rows, like active records.

* Example Wording: "Optimizes performance by indexing only rows that meet specific criteria, making targeted queries faster and efficient."

```sql
CREATE NONCLUSTERED INDEX IX_Emp_salesman
ON employees(status)
WHERE job = 'Salesman';
```
* Boosts performance when querying only active employees.

### Composite (Multi-Column) Index :

* An index created on multiple columns to improve performance on queries filtering or sorting by those columns together.

* Use Case: Useful when queries involve multiple columns in WHERE or JOIN conditions.

* Example Wording: "Boosts query performance when filtering or sorting by multiple related columns."

```sql
CREATE NONCLUSTERED INDEX IX_Emp_Dept_Job
ON employees(deptno, job);
```

* Helps queries filtering on both deptno and job.

### Columnstore Index Example (Data Warehousing):

* Stores data column-wise instead of row-wise, designed for large analytical queries common in data warehousing and reporting.

* Use Case: Best for big data scenarios involving complex aggregations and scans across large datasets.

* Example Wording: "Enhances performance for large-scale analytical queries by storing data in a highly compressed, column-based format."

```sql
CREATE CLUSTERED COLUMNSTORE INDEX IX_Emp_Columnstore
ON employees.emp;
```
* Converts the employees.emp table into a columnstore format.
* Stores data column-wise instead of row-wise.
* Highly efficient for data warehousing, reporting, and analytical queries (especially when scanning large datasets).
* Reduces storage space through compression.
* Great for SUM(), AVG(), GROUP BY, and JOIN operations on large tables.
* Perfect for large tables with reporting queries.

```sql
SELECT job, AVG(sal) AS avg_salary
FROM employees.emp
GROUP BY job;
```

* This type of query performs much faster with a Clustered Columnstore Index on large datasets.
* Once applied, the table becomes a columnstore structure — not suitable for OLTP (frequent inserts/updates).
* Best for read-heavy, analytical queries  

### Full-Text Index (Text Search):

* Enables advanced text searching capabilities on large text fields, supporting queries like CONTAINS or FREETEXT.

* Use Case: Ideal for searching unstructured text such as resumes, product descriptions, or bios.

* Example Wording: "Facilitates fast and efficient text-based searches within large textual columns."

```sql
CREATE FULLTEXT INDEX ON employees.emp(job)
KEY INDEX PK_emp;

SELECT empno, ename, job
FROM employees.emp
WHERE CONTAINS(job, 'manager');

```

* Allows advanced text search like CONTAINS(bio, 'developer')

### Indexed View Example (```sql Server’s Materialized View)
* Definition: An Indexed View physically stores the result set of the view and indexes it for faster retrieval — acts like a materialized view.

* Use Case: Useful for frequently run aggregate queries or complex joins.

* Example Wording: "Stores precomputed results of a view, significantly improving performance for complex queries and reports."

```sql
CREATE VIEW vw_emp_summary
WITH SCHEMABINDING
AS
SELECT deptno, COUNT_BIG(*) AS emp_count
FROM employees.emp
GROUP BY deptno;
```
```
CREATE UNIQUE CLUSTERED INDEX idx_vw_emp_summary
ON vw_emp_summary(deptno);
```
* Pre-computes and stores summary results for faster reporting.

### Why It Matters
* More indexes = faster reads but slower inserts/updates	Maintain balance
* Clustered index = 1 per table	Because it stores data physically
* Non-clustered index = Many per table	Helps optimize searches and joins
* Use INCLUDE() columns if needed	Add non-key columns to cover queries
### Sample INCLUDE Usage:
```sql
CREATE NONCLUSTERED INDEX IX_Emp_Job_Include
ON employees(job)
INCLUDE (ename, sal);
```
* Helps avoid extra lookups when selecting ename and sal.

### Best Practices:
* Always analyze which columns are frequently used in WHERE, JOIN, GROUP BY
* Don’t blindly create too many indexes — they slow down INSERT, UPDATE
* Understand when to use clustered vs. non-clustered
* Practice creating and testing filtered indexes and columnstore indexes for big data scenarios


