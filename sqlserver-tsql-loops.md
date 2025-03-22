![Tinitiate SQLSERVER Training](./sqlserver_tinitiate.png)

# T-SQL Server
&copy; TINITIATE.COM

##### [Back To Context](./README.md)
# Loops in SQL Server
In SQL Server, loops are used to execute a block of code repeatedly based on a condition. 

## FOR Loop
SQL Server does not have a traditional FOR loop like some other programming languages. However, you can achieve similar functionality using a WHILE loop with a counter variable.

Example: Print Names of First 5 Employers
``` sql
DECLARE @Counter INT = 1;
DECLARE @empno INT;
DECLARE @ename VARCHAR(50);

WHILE @Counter <= 5
BEGIN
    -- Fetch the nth row based on ordering
    SELECT TOP 1 @empno = empno, @ename = ename
    FROM employees.emp
    WHERE empno NOT IN (
        SELECT TOP (@Counter - 1) empno FROM employees.emp ORDER BY empno
    )
    ORDER BY empno;

    PRINT @ename;  -- Print one name at a time

    SET @Counter = @Counter + 1;
END;


```
In this example, we use a WHILE loop to print the names of the first 5 employers from the employer table. The loop runs as long as the @Counter variable is less than or equal to 5.

## WHILE Loop
 The WHILE loop executes a block of code repeatedly as long as a specified condition is true.

Example: Decrease Prices of All Parts by 10%
```sql
DECLARE @EmpNo INT;

-- Get the first empno of employees with job 'CLERK'
SELECT @EmpNo = MIN(empno)
FROM employees.emp
WHERE job = 'CLERK';

WHILE @EmpNo IS NOT NULL
BEGIN
    -- Update salary by 10% only for the current CLERK
    UPDATE employees.emp
    SET sal = sal * 1.10
    WHERE empno = @EmpNo;

    -- Move to the next CLERK employee
    SELECT @EmpNo = MIN(empno)
    FROM employees.emp
    WHERE job = 'CLERK' AND empno > @EmpNo;
END;

```    
In this example, we use a WHILE loop to increase the salary of all employees in the emp table by 10%. The loop continues until there are no more employees to update.

## DO WHILE Loop
 SQL Server does not have a built-in DO WHILE loop, but you can simulate it using a WHILE loop with a BEGIN...END block.

Example: Add New Parts Until 20 Parts Exist
```sql
 DECLARE @SalesmanCount INT;
DECLARE @ProcessedCount INT = 0;
DECLARE @EmpNo INT;

-- Get the total number of SALESMAN employees
SELECT @SalesmanCount = COUNT(*) FROM employees.emp WHERE job = 'SALESMAN';

-- Loop until all SALESMAN employees are processed
WHILE @ProcessedCount < @SalesmanCount
BEGIN
    -- Get the next SALESMAN empno that is not processed
    SELECT TOP 1 @EmpNo = empno 
    FROM employees.emp
    WHERE job = 'SALESMAN' 
      AND empno NOT IN (SELECT TOP (@ProcessedCount) empno FROM employees.emp WHERE job = 'SALESMAN' ORDER BY empno)
    ORDER BY empno;
    
    -- Update salary by 10% for this SALESMAN
    UPDATE employees.emp
    SET sal = sal * 1.10
    WHERE empno = @EmpNo;

    -- Increment the processed count
    SET @ProcessedCount = @ProcessedCount + 1;
END;
```    
In this example, we use a WHILE loop to simulate a DO-WHILE loop by continuously updating the salary of employees with the job 'SALESMAN' in the employees table. The loop increases the salary by 10% for each SALESMAN and checks the condition at the end of each iteration, ensuring that the update operation is executed at least once before re-evaluating the loop condition.
