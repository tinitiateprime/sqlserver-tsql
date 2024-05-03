![Tinitiate SQLSERVER Training](./sqlserver_tinitiate.png)

# T-SQL Server
&copy; TINITIATE.COM

##### [Back To Context](./README.md)

# Bulk Collect

In SQL Server, the equivalent of Oracle's BULK COLLECT operation, which is typically used to fetch a large volume of data into collections in PL/SQL, can be achieved using different methods depending on what exactly you are trying to accomplish in terms of performance optimization and data processing. In SQL Server, similar results are often obtained through set-based operations, temp tables, table variables, or using bulk insert features for large data transfers. Let's explore a few methods that mimic the behavior of BULK COLLECT in the SQL Server environment.

# Using Table Variables for Bulk Data Handling
If you need to process or manipulate large datasets after fetching them, using table variables (or temp tables) can be an effective approach. Here, I'll demonstrate how to use a table variable to bulk collect data from a query and then perform additional operations on that data.

## Fetching Bill Details Into a Table Variable
Let's say you want to load all bill details for a particular day into memory and then process them in some way within your T-SQL script.

```sql

DECLARE @BillDetails TABLE (
    BillDetailID INT,
    BillID INT,
    ProductID INT,
    Quantity INT,
    LineTotal DECIMAL(10, 2)
);

-- Insert data into the table variable
INSERT INTO @BillDetails (BillDetailID, BillID, ProductID, Quantity, LineTotal)
SELECT bd.billdetail_id, bd.bill_id, bd.product_id, bd.quantity, bd.line_total
FROM billdetails bd
JOIN bill b ON bd.bill_id = b.bill_id
WHERE b.bill_date = '2024-01-01';  -- Example date

-- Now you can use the @BillDetails table variable to perform further operations
SELECT * FROM @BillDetails;

```

## Bulk Insert
If you're importing data from an external source into SQL Server, BULK INSERT is a highly efficient way to load large volumes of data. It's similar to BULK COLLECT in terms of efficiency for data loading but is used primarily for importing data from a file.

### Bulk Inserting Data from a File
```sql

BULK INSERT products
FROM 'C:\path\to\your\datafile.csv'
WITH (
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
    FIRSTROW = 2
);
```
### Using the OUTPUT Clause
The OUTPUT clause can capture the results of insert, update, delete, and merge statements into a table variable, temp table, or for further use in your SQL batch or application.

Example: Capturing Inserted Records
```sql

DECLARE @InsertedRecords TABLE (
    BillDetailID INT,
    LineTotal DECIMAL(10, 2)
);

INSERT INTO billdetails (bill_id, product_id, quantity, line_total)
OUTPUT INSERTED.billdetail_id, INSERTED.line_total INTO @InsertedRecords
VALUES (1, 101, 2, 19.98), (1, 102, 1, 29.99);

-- Now you can use the @InsertedRecords table variable
SELECT * FROM @InsertedRecords;
```






