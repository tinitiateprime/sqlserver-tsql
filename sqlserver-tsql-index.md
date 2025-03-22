![Tinitiate SQLSERVER Training](./sqlserver_tinitiate.png)

# T-SQL Server
&copy; TINITIATE.COM

##### [Back To Context](./README.md)


# SQL SERVER Indexes

## What is an Index in Sql Server?
An index is like the index in a book — it helps you find data faster without scanning the entire table.
It improves query performance, especially for large datasets.

## Types of Indexes in Sql Server with Examples

### Clustered Index Example:
```sql
CREATE CLUSTERED INDEX IX_Emp_Dept
ON employees(deptno);
```
* Data is physically sorted by deptno.

### Non-Clustered Index Example:
```sql
CREATE NONCLUSTERED INDEX IX_Emp_Job
ON employees(job);
```
* Faster searches when filtering by job title.
 
### Unique Index Example:

```sql
CREATE UNIQUE INDEX IX_Emp_Email
ON employees(email);
```
* Ensures no two employees have the same email.

### Filtered Index Example (Selective):

```sql
CREATE NONCLUSTERED INDEX IX_Emp_Active
ON employees(status)
WHERE status = 'Active';
```
* Boosts performance when querying only active employees.

### Composite (Multi-Column) Index Example:

```sql
CREATE NONCLUSTERED INDEX IX_Emp_Dept_Job
ON employees(deptno, job);
```

* Helps queries filtering on both deptno and job.

### Columnstore Index Example (Data Warehousing):

```sql
CREATE CLUSTERED COLUMNSTORE INDEX IX_Emp_Columnstore
ON employees;
```

* Perfect for large tables with reporting queries.

### Full-Text Index Example (Text Search):

```sql
CREATE FULLTEXT INDEX ON employees(bio)
KEY INDEX PK_Emp;
```

* Allows advanced text search like CONTAINS(bio, 'developer')

### Bonus: Indexed View Example (```sql Server’s Materialized View)
```sql
CREATE VIEW vw_emp_summary
WITH SCHEMABINDING
AS
SELECT deptno, COUNT_BIG(*) AS emp_count
FROM dbo.emp
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


