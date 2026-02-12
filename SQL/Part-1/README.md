# Database Concepts

## Topics Covered
1. Database
2. DBMS
3. Data Models
4. RDBMS

## 1. Database
- Place to store structured data
- Storing the related data like Employee ID, Employee Name, Employee Salary, Department, etc.

## 2. DBMS
DBMS = Database Management System

- Software that stores the data in the database in an organized way, which makes it easier to create, retrieve, update the data.

### Data Flow
Application -> DBMS Software -> stores data -> Database
Application <- DBMS Software (reads data from) <- Database


### Examples of DBMS Software
- MySQL
- Oracle
- MongoDB
- Cassandra

## 3. Data Models
How related data is connected to each other and stored inside the database.

### Types of Data Model
1. Hierarchical model
2. Network Model
3. Entity-Relationship model
4. Relational Model

DBMS software can use any of the above data models to store data in the database.

Most popular data model used by DBMS software is Relational Model, where data is stored in form of tables. Table contains rows and columns.

## 4. RDBMS
RDBMS = Relational DBMS

This is also a software, but this software only follows the Relational Model to store the data. I.e all RDBMS software will store the data in the form of tables.

### Examples of RDBMS
- Oracle
- PostgreSQL
- DB2
- MySQL

## What is SQL?
SQL is programming language for RDBMS software. (NOT DBMS only RDBMS)

SQL = Structured Query Language

### Operations Using SQL
- Insert Data (store the data)
- Retrieve Data
- Updating data
- Deleting data
- And many more


____
# Practice SQL

Practice SQL without installing any Software: https://www.w3schools.com/sql/trysql.asp?filename=trysql_asc

______
# Difference Between Commands and Keywords in SQL

## Commands
Actions that perform operations

- SELECT - retrieves data
- INSERT - adds new data
- UPDATE - modifies existing data
- DELETE - removes data
- CREATE - creates database objects

## Keywords
Reserved words used within commands

- FROM - specifies table
- WHERE - filters results
- AND, OR - logical operators
- ORDER BY - sorts results
- NULL - represents no value

## Example Showing Both

```sql
SELECT name, age FROM users WHERE age > 18 AND status IS NOT NULL ORDER BY name;
Command: SELECT

Keywords: FROM, WHERE, AND, IS, NOT, NULL, ORDER BY

In Short
Commands tell SQL what to do, keywords help construct the statement.
```
_____

SELECT Command

Purpose
Retrieves data from database tables.

Basic Syntax
SELECT column1, column2 FROM table_name;

Copy
sql
Key Variations
1. Select all columns:

SELECT * FROM Customers;

Copy
sql
2. Select specific columns:

SELECT Name, Email FROM Customers;

Copy
sql
3. With WHERE (filter):

SELECT * FROM Customers WHERE Age > 25;

Copy
sql
4. With DISTINCT (unique values):

SELECT DISTINCT City FROM Customers;

Copy
sql
5. With ORDER BY (sort):

SELECT * FROM Customers ORDER BY Name ASC;

Copy
sql
6. With LIMIT (restrict rows):

SELECT * FROM Customers LIMIT 10;

Copy
sql
7. With aggregate functions:

SELECT COUNT(*), AVG(Salary) FROM Employees;

Copy
sql
8. With GROUP BY:

SELECT City, COUNT(*) FROM Customers GROUP BY City;

Copy
sql
9. With JOIN:

SELECT Orders.OrderID, Customers.Name 
FROM Orders 
JOIN Customers ON Orders.CustomerID = Customers.CustomerID;

Copy
sql
10. With aliases:

SELECT Name AS CustomerName, Age AS CustomerAge FROM Customers;

Copy
sql
Common Operators in WHERE
=, !=, >, <, >=, <=

BETWEEN, IN, LIKE, IS NULL

AND, OR, NOT

Execution Order
FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT
____

DISTINCT Keyword in SQL
Purpose: Removes duplicate rows from query results.
Examples:
Without DISTINCT:
SELECT city FROM users;

Result: 
New York, London, New York, Paris, London (duplicates included)

With DISTINCT:

SELECT DISTINCT city FROM users;

Result: 
New York, London, Paris (duplicates removed)

Multiple columns:

SELECT DISTINCT city, country FROM users;

Returns unique combinations of city AND country.
With COUNT:
SELECT COUNT(DISTINCT city) FROM users;

Counts unique cities only.
Key Point: DISTINCT applies to the entire row, not individual columns.
_______

SQL is not case sensitive
Select Country, PostalCode From Customers; (mixed case)
select country, postalcode from customers; (lower case)
SELECT COUNTRY, POASTALCODE FROM CUSTOMERS; (upper case)

SQL is case insensitive, i.e all the above commands will execute. 
best practice: 
SELECT Country, PostalCode FROM Customers;
_______

Is it mandatory to end the SQL statements with semicolon symbol?

Not always mandatory for single statements.

but strongly recommended to Always use semicolon (;) - it's a good habit, ensures compatibility, and prevents errors when adding more statements.
_______
WHERE Clause:

Purpose
Filters records based on specified conditions.

Syntax
SELECT column1, column2 
FROM table_name 
WHERE condition;

Copy
sql
Comparison Operators
=       Equal to
!=, <>  Not equal to
>       Greater than
<       Less than
>=      Greater than or equal to
<=      Less than or equal to

Copy
Examples:

SELECT * FROM Employees WHERE Age > 30;
SELECT * FROM Products WHERE Price <= 100;
SELECT * FROM Customers WHERE City = 'London';

Copy
sql
Logical Operators
AND - All conditions must be true

SELECT * FROM Employees WHERE Age > 25 AND Salary > 50000;

Copy
sql
OR - At least one condition must be true

SELECT * FROM Customers WHERE City = 'London' OR City = 'Paris';

Copy
sql
NOT - Negates a condition

SELECT * FROM Products WHERE NOT Price > 100;

Copy
sql
Special Operators
BETWEEN - Range of values

SELECT * FROM Products WHERE Price BETWEEN 10 AND 50;

Copy
sql
IN - Multiple possible values

SELECT * FROM Customers WHERE City IN ('London', 'Paris', 'Berlin');

Copy
sql
LIKE - Pattern matching

SELECT * FROM Customers WHERE Name LIKE 'J%';  -- Starts with J
SELECT * FROM Customers WHERE Name LIKE '%son'; -- Ends with son
SELECT * FROM Customers WHERE Name LIKE '%an%'; -- Contains an

Copy
sql
IS NULL / IS NOT NULL - Check for null values

SELECT * FROM Customers WHERE Email IS NULL;
SELECT * FROM Customers WHERE Phone IS NOT NULL;

Copy
sql
Combining Conditions
SELECT * FROM Employees 
WHERE (Age > 25 AND City = 'London') 
   OR (Salary > 60000 AND Department = 'IT');

Copy
sql
Key Points
WHERE filters rows BEFORE grouping

Use with SELECT, UPDATE, DELETE

Text values need single quotes: 'London'

Numbers don't need quotes: 100
_______

BETWEEN Operator 
Purpose
Selects values within a specified range (inclusive of both endpoints).

Syntax
SELECT column_name 
FROM table_name 
WHERE column_name BETWEEN value1 AND value2;

Copy
sql
Key Points
Inclusive: Includes both boundary values (value1 and value2)

Works with numbers, text, and dates

Equivalent to: column >= value1 AND column <= value2

Examples
1. Numbers:

SELECT * FROM Products WHERE Price BETWEEN 10 AND 50;

Copy
sql
Returns products with price from 10 to 50 (including 10 and 50).

2. Dates:

SELECT * FROM Orders 
WHERE OrderDate BETWEEN '2024-01-01' AND '2024-12-31';

Copy
sql
Returns orders from entire year 2024.

3. Text (alphabetical order):

SELECT * FROM Customers 
WHERE Name BETWEEN 'A' AND 'M';

Copy
sql
Returns names starting with A through M.

NOT BETWEEN
Excludes values in the range:

SELECT * FROM Products WHERE Price NOT BETWEEN 10 AND 50;

Copy
sql
Returns products with price < 10 or > 50.

Comparison
Using BETWEEN:

WHERE Age BETWEEN 25 AND 35

Copy
sql
Equivalent to:

WHERE Age >= 25 AND Age <= 35

Copy
sql
Common Use Cases
Price ranges: BETWEEN 100 AND 500

Date ranges: BETWEEN '2024-01-01' AND '2024-12-31'

Age groups: BETWEEN 18 AND 65

Score ranges: BETWEEN 70 AND 100

Remember: BETWEEN is cleaner and more readable than using >= and <= operators.
______
ORDER BY Clause
Purpose
Sorts query results in ascending or descending order.

Syntax
SELECT column1, column2 
FROM table_name 
ORDER BY column1 [ASC|DESC];

Copy
sql
ASC vs DESC
ASC (Ascending) - Default, smallest to largest

Numbers: 1, 2, 3, 4...

Text: A, B, C, D...

Dates: oldest to newest

DESC (Descending) - Largest to smallest

Numbers: 10, 9, 8, 7...

Text: Z, Y, X, W...

Dates: newest to oldest

Examples
1. Single column (ascending - default):

SELECT * FROM Employees ORDER BY Salary;
SELECT * FROM Employees ORDER BY Salary ASC;  -- Same as above

Copy
sql
2. Single column (descending):

SELECT * FROM Employees ORDER BY Salary DESC;

Copy
sql
3. Multiple columns:

SELECT * FROM Employees 
ORDER BY Department ASC, Salary DESC;

Copy
sql
First sorts by Department (A-Z), then within each department sorts by Salary (high to low).

4. By column position:

SELECT Name, Age, Salary FROM Employees ORDER BY 3 DESC;

Copy
sql
Sorts by 3rd column (Salary) in descending order.

5. With WHERE:

SELECT * FROM Products 
WHERE Price > 100 
ORDER BY Price DESC;

Copy
sql
6. Text sorting:

SELECT * FROM Customers ORDER BY Name ASC;  -- A to Z
SELECT * FROM Customers ORDER BY Name DESC; -- Z to A

Copy
sql
7. Date sorting:

SELECT * FROM Orders ORDER BY OrderDate DESC;  -- Newest first
SELECT * FROM Orders ORDER BY OrderDate ASC;   -- Oldest first

Copy
sql
Multiple Column Example
SELECT * FROM Students 
ORDER BY Grade DESC, Name ASC;

Copy
sql
Result: Highest grades first, within same grade alphabetically by name.

Key Points
ASC is default (can be omitted)

Always executed last (after WHERE, GROUP BY, HAVING)

Can sort by columns not in SELECT list

NULL values: typically appear first in ASC, last in DESC (database-dependent)

Common Use Cases
Top earners: ORDER BY Salary DESC LIMIT 10

Alphabetical list: ORDER BY Name ASC

Recent orders: ORDER BY OrderDate DESC

Leaderboard: ORDER BY Score DESC
