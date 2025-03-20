![Tinitiate SQLSERVER Training](./sqlserver_tinitiate.png)

# T-SQL Server
&copy; TINITIATE.COM

##### [Back To Context](./README.md)
# Conditional Statements in SQL Server 

* Conditional statements in SQL Server, such as IF...ELSE, CASE, and IIF, are useful for executing different SQL statements based on specific conditions. Here's how you can use these statements with the emp, dept, and sal tables:

## IF...ELSE Statement
 The IF...ELSE statement allows you to execute a block of SQL statements if a condition is true, and another block if the condition is false.

Example: Check if a Department Exists
 ```sql
    DECLARE @DeptName VARCHAR(100) = 'IT';

    IF EXISTS (SELECT 1 FROM employees.dept WHERE dname = @DeptName)
        BEGIN
            PRINT 'The department exists.';
        END
    ELSE
        BEGIN
            PRINT 'The department does not exist.';
    END
```
In this example, we check if there is a department named 'IT' in the dept table. If it exists, we print a message indicating that the department exists. Otherwise, we print a message indicating that it does not exist.

## CASE Statement
 The CASE statement is used for conditional logic within a SELECT query. It evaluates a list of conditions and returns a result for the first condition that is true.

Example: Categorize Employees Based on sal
 ```sql
    SELECT 
        ename,
        CASE 
            WHEN sal < 50000 THEN 'Low'
            WHEN sal BETWEEN 50000 AND 100000 THEN 'Medium'
            ELSE 'High'
        END AS salCategory
    FROM employees.emp;
```
In this example, we categorize employees into 'Low', 'Medium', and 'High' sal groups based on their sal in the emp table.

## IIF Function
 The IIF function is a shorthand way to write a simple IF...ELSE condition. It takes three arguments: a condition, a true value, and a false value.

Example: Check if sal is Above Average
```sql

    SELECT 
        ename,
        sal,
        IIF(sal > (SELECT AVG(sal) FROM emp), 'Above Average', 'Below Average') AS salStatus
    FROM employees.emp;
```
In this example, we use the IIF function to check if each employee's sal is above the average sal in the emp table. The result is a column that indicates whether each employee's sal is 'Above Average' or 'Below Average'.
