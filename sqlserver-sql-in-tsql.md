![Tinitiate SQLSERVER Training](./sqlserver_tinitiate.png)

# SQL in TSQL
&copy; TINITIATE.COM

##### [Back To Context](./README.md)
* SQL in TSQL, here we demonstrate the following
  * **DQL** in TSQL
  * **DML** in TSQL

```sql

-- SQL in TSQL
   BEGIN
    -- Data Input Variables
    DECLARE @l_in_emp_id INT = 7499;
    DECLARE @l_in_emp_name VARCHAR(100) = 'allen';
    DECLARE @l_in_job_title VARCHAR(100) = 'Analyst';
    DECLARE @l_in_salary DECIMAL(10,2) = 7500.00;
    DECLARE @l_in_dept_id INT = 30;

    -- Code Variables
    DECLARE @emp_id INT;
    DECLARE @emp_count INT;

    -- DQL: Get employee count and print it
    SELECT @emp_count = COUNT(*) 
    FROM employees.emp;

    PRINT CONCAT('Number of Employees: ', @emp_count);

    -- DML: Insert or update employee data
    SELECT @emp_id = empno
    FROM employees.emp
    WHERE empno = @l_in_emp_id;

    IF @@rowcount = 0 -- If no employee found with the given ID
    BEGIN
        PRINT CONCAT('Employee: ', @l_in_emp_id, ' does not exist, Adding now!');
        INSERT INTO employees.emp (empno, ename, job, sal, deptno)
        VALUES (@l_in_emp_id, @l_in_emp_name, @l_in_job_title, @l_in_salary, @l_in_dept_id);
        PRINT CONCAT('Rows Inserted: ', @@rowcount);
    END
    ELSE
    BEGIN
        PRINT CONCAT('Employee: ', @l_in_emp_id, ' exists, Updating with new values');
        UPDATE employees.emp
        SET ename = @l_in_emp_name,
            job = @l_in_job_title,
            sal = @l_in_salary,
            deptno = @l_in_dept_id
        WHERE empno = @l_in_emp_id;
    END
END
-- Check Updated Data
SELECT empno, ename, job, sal
FROM employees.emp
WHERE empno =7499;


```

* This T-SQL script performs the following actions to manage employee data in the emp table:

Declare Input Variables:
The script starts by declaring input parameters representing the employee's details, such as emp_id, emp_name, job_title, salary, and dept_id.

Get Employee Count:
It retrieves the total number of employees in the emp table and prints the count.

Check if Employee Exists:
The script checks if an employee with the given emp_id already exists:

If the employee does not exist, it prints a message and inserts the new employee record into the table.
If the employee exists, it prints a message and updates the employee’s details with the new values.
Confirmation of Operation:
After performing the insert or update, it prints the number of rows affected.

Retrieve Updated Record:
Finally, it selects and displays the updated record for the employee with emp_id = 2 to verify the changes.
