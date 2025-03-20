![Tinitiate SQLSERVER Training](./sqlserver_tinitiate.png)

# T-SQL Server
&copy; TINITIATE.COM

##### [Back To Context](./README.md)
# Anonymous Block

* An anonymous block, often referred to as an anonymous code block or batch, is a segment of code that does not have a routine name (unlike stored procedures or functions) and is executed as a script. SQL Server executes these blocks as T-SQL batches.
* An anonymous block in SQL Server typically doesn't use a specific syntax to define the start and end of the block like you might find in PL/SQL (Oracle) or other database systems. However, you can use BEGIN and END to group statements logically, which is useful for control-of-flow purposes within the batch.

Below is how to create an anonymous block to perform a transaction that involves the tables you defined (products, customers, bill, billdetails). 

```sql
BEGIN TRANSACTION;

BEGIN TRY
    -- Insert Employee 1
    DECLARE @EmpId1 INT = 7900;
    DECLARE @EmpName1 VARCHAR(100) = 'James';
    DECLARE @Job1 VARCHAR(50) = 'CLERK';
    DECLARE @Salary1 DECIMAL(10, 2) = 950.00;
    DECLARE @DeptId1 INT = 30;

    INSERT INTO employees.emp (empno, ename, job, sal, deptno)
    VALUES (@EmpId1, @EmpName1, @Job1, @Salary1, @DeptId1);

    -- Insert Employee 2
    DECLARE @EmpId2 INT = 7901;
    DECLARE @EmpName2 VARCHAR(100) = 'Miller';
    DECLARE @Job2 VARCHAR(50) = 'CLERK';
    DECLARE @Salary2 DECIMAL(10, 2) = 1300.00;
    DECLARE @DeptId2 INT = 10;

    INSERT INTO employees.emp (empno, ename, job, sal, deptno)
    VALUES (@EmpId2, @EmpName2, @Job2, @Salary2, @DeptId2);

    -- Insert Employee 3
    DECLARE @EmpId3 INT = 7902;
    DECLARE @EmpName3 VARCHAR(100) = 'Clark';
    DECLARE @Job3 VARCHAR(50) = 'MANAGER';
    DECLARE @Salary3 DECIMAL(10, 2) = 2450.00;
    DECLARE @DeptId3 INT = 10;

    INSERT INTO employees.emp (empno, ename, job, sal, deptno)
    VALUES (@EmpId3, @EmpName3, @Job3, @Salary3, @DeptId3);

    -- If everything is successful
    COMMIT TRANSACTION;
    PRINT 'Employee transaction completed successfully.';
END TRY
BEGIN CATCH
    -- Rollback if any error occurs
    ROLLBACK TRANSACTION;
    PRINT 'Error occurred: ' + ERROR_MESSAGE();
END CATCH;

```

**Transaction Management:** This script uses a transaction to ensure that all operations are completed successfully or, in case of an error, all operations are rolled back. This is essential to maintain data integrity.
**Error Handling:** The TRY...CATCH block is used to catch and handle errors. If an error occurs, the transaction is rolled back and an error message is printed.
**Variables:** Variables are used to store values for customer ID, product details, and billing information. This makes the script easier to read and modify.
