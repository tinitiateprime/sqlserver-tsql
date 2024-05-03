![Tinitiate SQLSERVER Training](./sqlserver_tinitiate.png)

# T-SQL Server
&copy; TINITIATE.COM

##### [Back To Context](./README.md)

# Associative Array

SQL Server does not support associative arrays or index-by tables as some other database systems do, such as Oracle with its PL/SQL collections. However, you can simulate the behavior of associative arrays using different SQL Server features depending on your specific requirements. Here are a few methods to emulate associative arrays in SQL Server:

## Using Table Variables
You can create a table variable that acts like an associative array. A table variable can store pairs of keys and values.

Example:
Suppose you want to create an associative array for temporary lookup purposes, mapping product_id to quantity.

```sql

DECLARE @ProductQuantities TABLE (
    ProductID INT PRIMARY KEY,
    Quantity INT
);

-- Insert values into the table variable
INSERT INTO @ProductQuantities (ProductID, Quantity) VALUES (1, 10);
INSERT INTO @ProductQuantities (ProductID, Quantity) VALUES (2, 20);
INSERT INTO @ProductQuantities (ProductID, Quantity) VALUES (3, 30);

-- Retrieve a value
SELECT Quantity FROM @ProductQuantities WHERE ProductID = 2;
This acts like an associative array where you can quickly look up the quantity by the product ID.
```
## Using Temporary Tables
For more extensive data manipulation and when you expect a larger volume of data, a temporary table can be used similarly to a table variable but with better performance for larger datasets.

```sql

CREATE TEMPORARY TABLE #ProductQuantities (
    ProductID INT PRIMARY KEY,
    Quantity INT
);

-- Insert values
INSERT INTO #ProductQuantities (ProductID, Quantity) VALUES (1, 100);
INSERT INTO #ProductQuantities (ProductID, Quantity) VALUES (2, 200);

-- Usage
SELECT Quantity FROM #ProductQuantities WHERE ProductID = 1;
```
## Using JSON to Simulate Associative Arrays
With SQL Server 2016 and later, JSON functions allow for storing and querying JSON data, which can simulate an associative array.

```sql

DECLARE @Json NVARCHAR(MAX) = N'{"1": 10, "2": 20, "3": 30}';

-- Insert a new key-value pair
SET @Json = JSON_MODIFY(@Json, '$."4"', 40);

-- Retrieve a value
SELECT JSON_VALUE(@Json, '$.2') AS Quantity;
```
This approach treats the JSON object keys as associative array indices and the values as the data.

## Using XML
Similar to JSON, XML can be used to simulate associative arrays in SQL Server.

```sql

DECLARE @XmlData XML = '<Items><Item Key="1" Value="100"/><Item Key="2" Value="200"/></Items>';

-- Add a new item
SET @XmlData.modify('insert <Item Key="3" Value="300"/> into (/Items)[1]');

-- Retrieve an item
DECLARE @Key INT = 2;
SELECT @XmlData.value('(/Items/Item[@Key=sql:variable("@Key")]/@Value)[1]', 'INT') AS Value;

```

# VARRAY 

In SQL Server, there isn't direct support for VARRAYs (variable-size arrays) as you would find in Oracle. However, if you need to handle an ordered collection of elements associated with a specific entity, such as products related to a bill, you can simulate the behavior of VARRAYs using different strategies. I'll provide an example of how you might approach this using a JSON array, which can effectively manage an ordered list within a single column, emulating the VARRAY functionality.

## Simulating VARRAY Using JSON in SQL Server
This method involves using a JSON column to store an array of data, which can represent multiple related records for a single entity. In your scenario, this could be particularly useful if, for instance, you wanted to store multiple product IDs associated with a single bill without creating multiple rows in a table.

However, since your schema already appropriately uses normalized tables for storing bills and their details (which is the recommended method in relational databases), let's assume you have a use case where you want to store multiple contact information entries for a customer in a single column, emulating a VARRAY.

### Alter the Customers Table to Include a JSON Column
First, you would modify the customers table to include a JSON column that can store multiple pieces of contact information:

```sql

ALTER TABLE customers
ADD contact_info_json NVARCHAR(MAX);
```
### Inserting Data into the JSON Column
Here's how you could insert multiple contact details into the new JSON column for a customer:

```sql

UPDATE customers
SET contact_info_json = JSON_QUERY('["john.doe@example.com", "jane.doe@example.com"]')
WHERE customer_id = 1;
```
In this example, JSON_QUERY is used with an array of strings. This function ensures that the inserted JSON is properly formatted.

### Adding More Contact Information
To add more contact information to the existing JSON array:

```sql

DECLARE @NewContact NVARCHAR(100) = 'contact@example.com';

UPDATE customers
SET contact_info_json = JSON_MODIFY(contact_info_json, 'append $', @NewContact)
WHERE customer_id = 1;

```
This uses JSON_MODIFY with the 'append' option to add a new element to the JSON array.

### Querying Data from the JSON Column
To retrieve and use the contact information stored in the JSON column:

```sql

SELECT customer_id, JSON_VALUE(contact_info_json, '$[0]') AS primary_contact
FROM customers
WHERE customer_id = 1;

-- To get all contacts
SELECT customer_id, [value] AS contact
FROM customers
CROSS APPLY OPENJSON(contact_info_json)
WHERE customer_id = 1;
```
OPENJSON provides a table-like view of the JSON array, making it easy to work with each item individually.

### Advantages of Using JSON for VARRAY-like Data
**Flexibility:** JSON fields can store an array of elements in a single column, closely mimicking the behavior of a VARRAY.

**Dynamic Schema:** You can store different types of data and change the number of elements without altering the database schema.
Standard SQL Operations: You can use standard SQL functions to manipulate and query JSON data.


# Nested Table

The concept of "nested tables" doesn't exist in the same way. SQL Server does not inherently support nested tables as a datatype. However, you can implement similar functionality through the use of structured data types like JSON or XML to emulate nested behavior or by using related tables with appropriate foreign keys, which is more in line with the relational database paradigm.


##  Using JSON to Emulate Nested Tables
Assuming you want to store each bill along with its bill details in a nested JSON format directly in the bill table (though not recommended for normal operations as it defeats indexing and relational integrity checks):

### Altering the bill Table to Add a JSON Column
First, let's add a JSON column to the bill table to store the nested bill details:

```sql
ALTER TABLE bill
ADD bill_details_json NVARCHAR(MAX);
```

## Populating the JSON Column
When creating a new bill and its details, you could format the details as JSON and insert them into the bill table:

```sql

DECLARE @BillId INT = 1;
DECLARE @BillDetailsJSON NVARCHAR(MAX) = 
    JSON_QUERY('[
        {"product_id": 101, "quantity": 2, "line_total": 19.98},
        {"product_id": 102, "quantity": 1, "line_total": 9.99}
    ]');

UPDATE bill
SET bill_details_json = @BillDetailsJSON
WHERE bill_id = @BillId;
```

### Querying Nested JSON Data
To access this nested data, you can use SQL Server's JSON functions:

```sql

SELECT 
    bill_id, 
    JSON_VALUE(bill_details_json, '$[0].product_id') AS first_product_id,
    JSON_VALUE(bill_details_json, '$[0].quantity') AS first_product_quantity
FROM 
    bill
WHERE 
    bill_id = 1;
```    
##  Using XML to Emulate Nested Tables
Alternatively, you could use XML to store nested data:

### Altering the bill Table to Add an XML Column
```sql

ALTER TABLE bill
ADD bill_details_xml XML;
```
### Populating the XML Column
```sql

DECLARE @BillId INT = 1;
DECLARE @BillDetailsXML XML = 
    '<BillDetails>
        <Detail product_id="101" quantity="2" line_total="19.98" />
        <Detail product_id="102" quantity="1" line_total="9.99" />
    </BillDetails>';

UPDATE bill
SET bill_details_xml = @BillDetailsXML
WHERE bill_id = @BillId;
```

### Querying Nested XML Data
```sql

SELECT
    bill_id,
    bill_details_xml.value('(/BillDetails/Detail[1]/@product_id)[1]', 'INT') AS first_product_id,
    bill_details_xml.value('(/BillDetails/Detail[1]/@quantity)[1]', 'INT') AS first_product_quantity
FROM 
    bill
WHERE 
    bill_id = 1;
```
 