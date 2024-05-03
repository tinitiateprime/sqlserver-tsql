![Tinitiate SQLSERVER Training](./sqlserver_tinitiate.png)

# T-SQL Server
&copy; TINITIATE.COM

##### [Back To Context](./README.md)

# Pipeline Functions

In SQL Server, the concept of pipelined table functions, as seen in Oracle, where rows are returned one at a time as they are processed, does not exist exactly in the same manner. However, SQL Server offers table-valued functions (TVFs), which serve a similar purpose by allowing the creation of functions that return tables, which can then be used in SQL queries like regular tables.

While SQL Server's TVFs do not stream results row by row in real-time, they can still be used to build modular, reusable components that handle data in stages, somewhat similar to a pipeline. Below, I will demonstrate how to create and use multi-statement table-valued functions (TVFs) that mimic the behavior of pipelined functions by processing data in stages.



## Creating a Multi-statement TVF to Aggregate Bill Details
Let’s create a TVF that aggregates bill details to compute the total amounts per bill.

```sql

CREATE FUNCTION dbo.ufn_GetBillTotals(@Date DATE)
RETURNS @BillTotals TABLE
(
    BillID INT,
    TotalAmount DECIMAL(10, 2)
)
AS
BEGIN
    INSERT INTO @BillTotals (BillID, TotalAmount)
    SELECT bill_id, SUM(line_total)
    FROM BillDetails AS bd
    JOIN Bill AS b ON bd.bill_id = b.bill_id
    WHERE b.bill_date = @Date
    GROUP BY bd.bill_id;

    RETURN;
END;
```
## Creating a Second TVF to Combine with Customer Information
Next, we can use another function to join this aggregated data with customer information:

```sql

CREATE FUNCTION dbo.ufn_GetCustomerBillTotals(@Date DATE)
RETURNS TABLE
AS
RETURN
(
    SELECT c.CustomerName, c.ContactInfo, bt.TotalAmount
    FROM dbo.ufn_GetBillTotals(@Date) AS bt
    JOIN Bill AS b ON bt.BillID = b.bill_id
    JOIN Customers AS c ON b.customer_id = c.customer_id
);
```
## How to Use These Functions
You can use these functions in your queries to process and retrieve data in a pipeline-like manner:

```sql
SELECT *
FROM dbo.ufn_GetCustomerBillTotals('2023-01-01');
```


## Summary
In SQL Server does not provide true pipelined table functions that stream data row-by-row, using table-valued functions allows for similarly structured data processing. These functions can help manage complex data transformations and aggregations by encapsulating SQL logic into manageable, reusable components, similar to how you might structure a pipeline.