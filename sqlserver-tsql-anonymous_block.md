![Tinitiate SQLSERVER Training](./sqlserver_tinitiate.png)

# T-SQL Server
&copy; TINITIATE.COM

##### [Back To Context](./README.md)
# Anonymous Block

* An anonymous block, often referred to as an anonymous code block or batch, is a segment of code that does not have a routine name (unlike stored procedures or functions) and is executed as a script. SQL Server executes these blocks as T-SQL batches.
* An anonymous block in SQL Server typically doesn't use a specific syntax to define the start and end of the block like you might find in PL/SQL (Oracle) or other database systems. However, you can use BEGIN and END to group statements logically, which is useful for control-of-flow purposes within the batch.

Below is how to create an anonymous block to perform a transaction that involves the tables you defined (products, customers, bill, billdetails). 

```sql
BEGIN TRANSACTION

BEGIN TRY
    -- Insert a customer
    DECLARE @CustomerId INT = 1;
    DECLARE @CustomerName VARCHAR(100) = 'John Doe';
    DECLARE @ContactInfo VARCHAR(200) = 'john.doe@example.com';

    INSERT INTO customers (customer_id, customer_name, contact_info)
    VALUES (@CustomerId, @CustomerName, @ContactInfo);

    -- Insert a product
    DECLARE @ProductId INT = 101;
    DECLARE @ProductName VARCHAR(100) = 'Widget';
    DECLARE @Price DECIMAL(10, 2) = 29.99;

    INSERT INTO products (product_id, product_name, price)
    VALUES (@ProductId, @ProductName, @Price);

    -- Create a bill
    DECLARE @BillId INT = 501;
    DECLARE @BillDate DATE = GETDATE();
    DECLARE @TotalAmount DECIMAL(10, 2) = @Price;  -- Assuming quantity of 1 for simplicity

    INSERT INTO bill (bill_id, customer_id, bill_date, total_amount)
    VALUES (@BillId, @CustomerId, @BillDate, @TotalAmount);

    -- Insert bill details
    DECLARE @BillDetailId INT = 1001;
    DECLARE @Quantity INT = 1;
    DECLARE @LineTotal DECIMAL(10, 2) = @Price * @Quantity;

    INSERT INTO billdetails (billdetail_id, bill_id, product_id, quantity, line_total)
    VALUES (@BillDetailId, @BillId, @ProductId, @Quantity, @LineTotal);

    -- Commit the transaction
    COMMIT TRANSACTION;

    PRINT 'Transaction completed successfully.';
END TRY
BEGIN CATCH
    -- Handle the error
    ROLLBACK TRANSACTION;
    PRINT 'Error occurred: ' + ERROR_MESSAGE();
END CATCH
```

**Transaction Management:** This script uses a transaction to ensure that all operations are completed successfully or, in case of an error, all operations are rolled back. This is essential to maintain data integrity.
**Error Handling:** The TRY...CATCH block is used to catch and handle errors. If an error occurs, the transaction is rolled back and an error message is printed.
**Variables:** Variables are used to store values for customer ID, product details, and billing information. This makes the script easier to read and modify.
