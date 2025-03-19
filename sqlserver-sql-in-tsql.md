![Tinitiate SQLSERVER Training](./sqlserver_tinitiate.png)

# T-SQL Server
&copy; TINITIATE.COM

##### [Back To Context](./README.md)
* SQL in TSQL, here we demonstrate the following
  * DQL in TSQL
  * DML in TSQL

```sql

-- SQL in TSQL
    BEGIN
    -- Data Input Variables
    DECLARE @l_in_emp_id INT = 2;
    DECLARE @l_in_emp_name VARCHAR(100) = 'John Doe';
    DECLARE @l_in_job_title VARCHAR(100) = 'Analyst';
    DECLARE @l_in_salary DECIMAL(10,2) = 75000.00;
    DECLARE @l_in_dept_id INT = 1;

    -- Code Variables
    DECLARE @emp_id INT;
    DECLARE @emp_count INT;

    -- DQL: Get employee count and print it
    SELECT @emp_count = COUNT(*) 
    FROM emp;

    PRINT CONCAT('Number of Employees: ', @emp_count);

    -- DML: Insert or update employee data
    SELECT @emp_id = emp_id
    FROM emp
    WHERE emp_id = @l_in_emp_id;

    IF @@rowcount = 0 -- If no employee found with the given ID
    BEGIN
        PRINT CONCAT('Employee: ', @l_in_emp_id, ' does not exist, Adding now!');
        INSERT INTO emp (emp_id, emp_name, job_title, salary, dept_id)
        VALUES (@l_in_emp_id, @l_in_emp_name, @l_in_job_title, @l_in_salary, @l_in_dept_id);
        PRINT CONCAT('Rows Inserted: ', @@rowcount);
    END
    ELSE
    BEGIN
        PRINT CONCAT('Employee: ', @l_in_emp_id, ' exists, Updating with new values');
        UPDATE emp
        SET emp_name = @l_in_emp_name,
            job_title = @l_in_job_title,
            salary = @l_in_salary,
            dept_id = @l_in_dept_id
        WHERE emp_id = @l_in_emp_id;
    END
END

-- Check Updated Data
SELECT emp_id, emp_name, job_title, salary 
FROM emp
WHERE emp_id = 2;


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
