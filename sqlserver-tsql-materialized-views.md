![Tinitiate SQLSERVER Training](./sqlserver_tinitiate.png)

# T-SQL Server
&copy; TINITIATE.COM

##### [Back To Context](./README.md)

SQL Server doesn't support the CREATE MATERIALIZED VIEW syntax like Oracle.
Instead, Indexed Views serve as SQL Server’s Materialized View equivalent.

How? ➔ Once you create a unique clustered index on a view, SQL Server materializes (stores) the result physically on disk and keeps it updated when the underlying table changes.

Example Using EMP Table in SQL Server
Let’s create a summary materialized view that stores employee count and average salary by department.

* Step 1: Create the View with SCHEMABINDING
```sql
CREATE VIEW employees.vw_emp_summary
WITH SCHEMABINDING
AS
SELECT deptno,
       COUNT_BIG(*) AS emp_count,  -- COUNT_BIG() is required
       AVG(CAST(sal AS FLOAT)) AS avg_sal
FROM employees.emp
GROUP BY deptno;

```

* Step 2: Create a Unique Clustered Index (Materializing the View)
```sql
CREATE UNIQUE CLUSTERED INDEX idx_vw_emp_summary
ON employees.vw_emp_summary(deptno);
```
✅ After this step, the view is materialized — the result set is physically stored and auto-updated when the emp table changes.

✅ Query the Materialized View
```sql
SELECT * FROM employees.vw_emp_summary;
```
![image](https://github.com/user-attachments/assets/8fcfb344-a4d0-4ba1-a760-cf7b244a4612)


![image](https://github.com/user-attachments/assets/0f133310-8a14-4c8d-89a5-666ae0bd6081)

![image](https://github.com/user-attachments/assets/e12adf92-8292-439a-ac76-19ce00a1835b)

✅ Summary Example (Materialized View on EMP table)
```sql

-- Materialized view equivalent
CREATE VIEW employees.vw_emp_summary
WITH SCHEMABINDING
AS
SELECT deptno, COUNT_BIG(*) AS emp_count, AVG(CAST(sal AS FLOAT)) AS avg_sal
FROM employees.emp
GROUP BY deptno;
```

```sql
CREATE UNIQUE CLUSTERED INDEX idx_vw_emp_summary
ON employees.vw_emp_summary(deptno);
```
✅ Now SQL Server stores the result like a materialized view and keeps it updated automatically.

Let me know if you need more real-world examples or a deep dive into refresh strategies or optimization! 🚀
