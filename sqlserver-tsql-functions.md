![Tinitiate SQLSERVER Training](./sqlserver_tinitiate.png)

# T-SQL Server
&copy; TINITIATE.COM

##### [Back To Context](./README.md)


# Functions in SQL Server


## Function with Input Parameter in SQL Server

 In SQL Server, you can create functions that accept input parameters, allowing you to pass values to the function and perform operations based on those values. 

 Example: Function to Get Employee Details by Department ID
 
 Scenario
    You want to create a function that returns the details of employees who work in a specific department, identified by the department ID.

```sql
-- Create a table-valued function that takes a department ID as input
CREATE FUNCTION employees.GetEmployeesByDepartment (@Deptno INT)
RETURNS TABLE
AS
RETURN
    SELECT empno, ename, job
    FROM employees.emp
    WHERE deptno = @Deptno;
```
* Execute the function
```sql
-- Use the function to get employee details for department 1
SELECT * FROM employees.GetEmployeesByDepartment(10);
```

We create a table-valued function named employees.GetEmployeesByDepartment that takes an integer parameter @Deptno, representing the department ID.
The function returns a table that contains the employee ID, employee name, and job title for all employees who work in the department specified by @Deptno.
The WHERE clause in the SELECT statement filters the employees based on the department ID.
To use the function, we call it in a SELECT statement, passing the desired department ID as an argument. In this case, we pass 1 to get the details of employees in department 1.

## Function with Default Parameter Value :
 We can assign default values to parameters in stored procedures and functions. When a default value is specified for a parameter, you can omit that parameter when calling the procedure or function, and the default value will be used instead.

``` sql
-- Create a table-valued function with a default parameter value
       CREATE FUNCTION employees.GetEmployeesByDepartment (@Deptno INT = NULL)
    RETURNS TABLE
    AS
    RETURN
        SELECT empno, ename, job
        FROM employees.emp
        WHERE Deptno = ISNULL(@Deptno, Deptno);
```

* Execute the function
``` sql
-- Use the function without specifying a department ID
SELECT * FROM employees.GetEmployeesByDepartment(NULL);
-- Use the function with a specific department ID
SELECT * FROM employees.GetEmployeesByDepartment(10);
```
 We create a table-valued function named employees.GetEmployeesByDepartment with an  integer parameter @Deptno. We assign a default value of NULL to @Deptno.
 The function returns a table containing the employee ID, employee name, and  job title.
 The WHERE clause uses the ISNULL function to check if @Deptno is NULL. If it  is, the condition Deptno = Deptno is effectively ignored, and employees  from all departments are returned. If @Deptno is not NULL, only employees  from the specified department are returned.
 When calling the function, you can either omit the parameter to get  employees from all departments or provide a specific department ID to get  employees from that department. 

## Explain Return

In SQL Server, the RETURN statement is used in functions to exit the function and return a value to the caller. The value returned by the function can be a scalar value or a table, depending on the type of function.

### Scalar Functions
* A scalar function returns a single value (e.g., an integer, a decimal, or a string). In a scalar function, the RETURN statement is used to specify the value that should be returned.

```sql

CREATE FUNCTION GetEmployeeCount()
RETURNS INT
AS
BEGIN
    DECLARE @Count INT;

    -- Count the number of employees
    SELECT @Count = COUNT(*) FROM employees.emp;

    -- Return the count
    RETURN @Count;
END
```

### Table-Valued Functions

A table-valued function returns a table. In a table-valued function, the RETURN statement is used to return a table variable or to conclude a RETURN clause that contains a SELECT statement.

* Inline Table-Valued Function
In an inline table-valued function, the RETURN statement directly contains a SELECT statement that produces the result set.

```sql
    CREATE FUNCTION GetEmployeesByDepartment(@Deptno INT)
    RETURNS TABLE
    AS
    RETURN (
        SELECT ename, job FROM employees.emp WHERE Deptno = @Deptno
    )
```
* In this example, the GetEmployeesByDepartment function returns a table    
  containing the names and job titles of employees in a specified department.

* Multi-Statement Table-Valued Function
 In a multi-statement table-valued function, the RETURN statement is used to return a declared table variable.

```sql
    CREATE FUNCTION GetAllEmployees()
    RETURNS @EmployeeTable TABLE (ename VARCHAR(100), job VARCHAR(100))
    AS
    BEGIN
        -- Insert data into the table variable
        INSERT INTO @EmployeeTable (ename, job)
        SELECT ename, job FROM employees.emp;

        -- Return the table variable
        RETURN;
    END
```
* Execute the function

```sql
-- Execute the multi-statement table-valued function
SELECT * FROM employees.GetAllEmployees();

```

Key Points
In scalar functions, the RETURN statement specifies the single value to be returned.
In table-valued functions, the RETURN statement is used to return a result set or a table variable.
The RETURN statement immediately exits the function and returns control to the caller.


## Execute the Function with Begin and End

```sql
    BEGIN
        -- Execute a scalar function and store the result in a variable
        DECLARE @TotalEmployees INT;
        SET @TotalEmployees = employees.GetTotalEmployeeCount();
        PRINT 'Total number of employees: ' + CAST(@TotalEmployees AS VARCHAR);
    END
```

* Executing a Table-Valued Function

```sql

    BEGIN
        -- Execute a table-valued function and use the result set directly
        SELECT * FROM employees.GetEmployeesByDepartment(1);

        -- Alternatively, store the result set in a table variable for further processing
        DECLARE @EmployeeDetails TABLE (ename VARCHAR(100), job VARCHAR(100));
        INSERT INTO @EmployeeDetails
        SELECT * FROM employees.GetEmployeesByDepartment(10);

        SELECT * FROM @EmployeeDetails;
    END


```
