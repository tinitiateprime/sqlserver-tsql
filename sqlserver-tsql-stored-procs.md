![Tinitiate SQLSERVER Training](./sqlserver_tinitiate.png)

# T-SQL Server
&copy; TINITIATE.COM

##### [Back To Context](./README.md)


# Stored Procedures

## Procedure with no parameters
* create a stored procedure without any parameters if you want to perform a specific operation that does not require any input.

``` sql
-- Create a stored procedure without parameters
    CREATE PROCEDURE employees.GetAllEmployees
    AS
    BEGIN
        SELECT empno, ename, job
        FROM employees.emp;
    END;
```
```sql
-- Execute the stored procedure
    EXEC employees.GetAllEmployees;
```    

* We create a stored procedure named employees.GetAllEmployees without any parameters.
* The procedure contains a simple SELECT statement that retrieves the employee ID, employee name, and job title from the emp table.
* To execute the stored procedure, we use the EXEC statement followed by the name of the procedure.

## Procedure with Input Parameter
 Create stored procedures with input parameters to pass values to the procedure when it is executed.

## Procedure to Get Employees by Department
* Create a stored procedure that returns the details of employees who work   
  in a specific department, identified by the department name.

```sql
-- Create a stored procedure with an input parameter for the department name
CREATE PROCEDURE employees.usp_UpdateEmployee
    @empno INT,
    @new_sal DECIMAL(10,2),
    @new_deptno INT
AS
BEGIN
    UPDATE employees.emp
    SET sal = @new_sal,
        deptno = @new_deptno
    WHERE empno = @empno;

    PRINT CONCAT('Employee ', @empno, ' updated successfully.');
END;
```
* Execute Procedure

```sql
-- Execute the stored procedure with the empno = 7521 and update sal and deptno
EXEC usp_UpdateEmployee @empno = 7521, @new_sal = 1250, @new_deptno = 30;
```

## Procedure with Output param

* Create stored procedures with output parameters that allow you to return a value back to the caller. Output parameters can be particularly useful for returning status information, counts, or values calculated within the procedure.

Example: Procedure to Count Employees in a Specific Department
Scenario , Create a stored procedure that counts the number of employees in a specified department and returns this count as an output parameter.

```sql
-- Create a stored procedure with an input and output parameter
   CREATE PROCEDURE employees.GetEmployeeCountByDepartment
    @DeptID INT,
    @EmployeeCount INT OUTPUT
AS
BEGIN
    SELECT @EmployeeCount = COUNT(*)
    FROM employees.emp
    WHERE deptno = @DeptID;
END;



```

```sql
-- Declare a variable to hold the output value
DECLARE @Count INT;  -- Declare the output variable

EXEC employees.GetEmployeeCountByDepartment 
     @Deptno = 10, 
     @EmployeeCount = @Count OUTPUT;

PRINT CONCAT('Number of Employees in Department 10: ', @Count);



* We create a stored procedure named employees.GetEmployeeCountByDepartment with two parameters:

    * @Deptno: An input parameter of type INT that specifies which department to count employees in.
    * @EmployeeCount: An output parameter of type INT that will hold the count of employees.
* Inside the procedure, a SELECT statement calculates the number of employees in the specified department (@Deptno) and assigns this count to the output parameter (@EmployeeCount).

* To execute the procedure, you must declare a variable (@Count) to hold the output value.

* The stored procedure is then called using EXEC, specifying the input parameter and passing the variable as the output parameter with the OUTPUT keyword.

* After execution, the value of the output parameter is stored in the variable @Count, which can then be used in the calling environment, such as displaying the result.

## Multiple ways to execute procs

* The stored procedure employees.GetEmployeeCountByDepartment which includes both an input parameter and an output parameter, there are several methods you can use depending on your environment and specific requirements. Below are multiple ways to execute this procedure in SQL Server:

### Using T-SQL in SQL Server Management Studio (SSMS)
To execute this procedure from SSMS or any other T-SQL interface, you can use the following approach:

```sql
    DECLARE @EmployeeCountResult INT;
    EXEC employees.GetEmployeeCountByDepartment @Deptno = 1, @EmployeeCount = @EmployeeCountResult OUTPUT;

    -- To see the result
    SELECT @EmployeeCountResult AS EmployeeCount;
```
This code declares a variable to hold the output, executes the procedure with a specific department ID, and retrieves the count of employees in that department.

### Using a SQL Script or Batch
* You can execute the procedure within a SQL script or batch. This is useful when you need to run this as part of larger database operations:

```sql
    BEGIN
        DECLARE @Result INT;
        EXEC employees.GetEmployeeCountByDepartment @DeptID = 2, @EmployeeCount = @Result OUTPUT;
        PRINT 'Number of employees in department 2: ' + CAST(@Result AS VARCHAR(10));
    END
```    
### From Another Stored Procedure
* You can call this procedure from another stored procedure. This is useful for modular programming in SQL Server:

```sql
       CREATE PROCEDURE employees.CheckDepartmentSize
    AS
    BEGIN
        DECLARE @EmployeeCount INT;
        EXEC employees.GetEmployeeCountByDepartment @Deptid = 30, @EmployeeCount = @EmployeeCount OUTPUT;
        
        IF @EmployeeCount > 4
            PRINT 'Large Department';
        ELSE
            PRINT 'Small or Medium Department';
    END
```
### Scheduling with SQL Server Agent

You might want to schedule this procedure to run at specific intervals using SQL Server Agent. This is useful for regular audits or reports:
* Create a new Job in SQL Server Agent.
* Add a step with type "Transact-SQL script (T-SQL)".
* Enter the script to execute the procedure similarly to the T-SQL examples  
  above.
* Define the schedule according to your needs (daily, weekly, etc.).


## Default values for params

 You can specify default values for the parameters of stored procedures and user-defined functions. This feature allows you to make the parameters optional, meaning that if a value for a parameter is not provided when the procedure or function is called, the default value will be used instead. This can simplify the calling code and provide flexibility.

### Setting Default Values for Parameters
Here's how to define default values for parameters in SQL Server:

```sql
    -- Example of a stored procedure with default parameters
    CREATE PROCEDURE employees.GetEmployees
        @DeptID INT = NULL,            -- Default value is NULL
        @Status VARCHAR(100) = 'Active' -- Default value is 'Active'
    AS
    BEGIN
        SELECT emp_name, emp_id, dept_id, status
        FROM emp
        WHERE (dept_id = @DeptID OR @DeptID IS NULL)
        AND status = @Status;
    END
```
In this example:

* The @DeptID parameter has a default value of NULL. If @DeptID is not provided when the procedure is called, the query will ignore the department filter.
* The @Status parameter defaults to 'Active'. If @Status is not provided, the procedure will filter employees where the status is 'Active'.

### Calling the Stored Procedure with Default Parameters

You can call this stored procedure in various ways depending on whether you want to use the default values or provide specific values:

* Using Default Values for Both Parameters:

```sql
EXEC employees.GetEmployees;
```
* Providing a Value for @DeptID Only:

```sql
EXEC employees.GetEmployees @DeptID = 10;
```

* Providing a Value for @Status Only:

```sql
EXEC employees.GetEmployees @Status = 'Retired';
```

* Providing Values for Both Parameters:
```sql
EXEC employees.GetEmployees @DeptID = 10, @Status = 'Retired';
```

* Providing Values Using Named Parameters (Out of Order):
```sql
EXEC employees.GetEmployees @Status = 'Retired', @DeptID = 10;
```

### Benefits of Default Parameters
Default parameters in SQL Server provide several benefits:

* Simplicity: Reduces the number of overloaded procedures/functions needed to handle different scenarios.

* Flexibility: Allows the caller to specify only the parameters they are interested in, using defaults for others.

* Maintainability: Makes the code easier to maintain and update since changes to default logic can be centralized in the procedure/function definition.
