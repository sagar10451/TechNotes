
<h1 style="color:red">1. Database Concepts</h1>

### 1. Database (DB)

A structured collection of data stored and accessed electronically. Think of it as the **digital filing cabinet**.

* **Purpose:** To store, retrieve, and manage information efficiently.
* **Key Property:** Data is organized so that it can be easily managed and updated.

### 2. DBMS (Database Management System)

The **software** that interacts with users, applications, and the database itself to capture and analyze data.

* **Functions:** Data security, backup, integrity, and multi-user access control.
* **Examples:** MySQL, PostgreSQL, Oracle, MongoDB.

### 3. Data Models

A conceptual framework that defines how data is structured, stored, and manipulated.

* **Relational Model:** Data in tables (Rows/Columns). Most common.
* **Hierarchical Model:** Tree-like structure (Parent-Child).
* **Network Model:** Graph-like structure (Many-to-Many).
* **Object-Oriented:** Data represented as objects (like in coding).

### 4. RDBMS (Relational DBMS)

A DBMS based on the **Relational Model** (introduced by E.F. Codd).

* **Tables:** Data is stored in relations (tables).
* **Primary Key:** Unique identifier for each row.
* **Foreign Key:** Links two tables together.
* **ACID Properties:** Ensures transactions are processed reliably (Atomicity, Consistency, Isolation, Durability).

### 5. SQL (Structured Query Language)

The standard language used to communicate with an RDBMS.

* **Not a programming language:** It is a **declarative** language (you tell the DB *what* you want, not *how* to get it).
* **Main Categories:**
* **DDL (Definition):** `CREATE`, `DROP`, `ALTER` (Structure).
* **DML (Manipulation):** `INSERT`, `UPDATE`, `DELETE` (Data).
* **DQL (Query):** `SELECT` (Retrieval).
* **DCL (Control):** `GRANT`, `REVOKE` (Permissions).



---
<h1 style="color:red">2. SQL is not case sensitive</h1>

SQL keywords and names are generally case-insensitive, meaning the database treats SELECT and select as the same command.
Example of Interchangeable Syntax: All three of these will execute identically:
1. Select Country, PostalCode From Customers; (Mixed)
2. select country, postalcode from customers; (Lower)
3. SELECT COUNTRY, POSTALCODE FROM CUSTOMERS; (Upper)

The "Gold Standard" (Best Practice)
To make your code readable and maintainable, use PascalCase for identifiers and UPPERCASE for keywords:
SELECT Country, PostalCode FROM Customers;
Why follow this?
* Readability: Keywords stand out immediately from table names.
* Consistency: Makes debugging much faster in large scripts.
* Data Exception: Remember that while commands aren't case-sensitive, the actual data inside the rows (e.g., 'New York' vs 'new york') often is!

---
<h1 style="color:red">3. Semicolon</h1>

### **The SQL Semicolon (`;`) Rule**

**Is it mandatory?**
Only in some RDBMS (like PostgreSQL or Oracle). In others (like MySQL or SQL Server), it is often optional for single queries.

**Why use it?**

1. **Separation:** Required to distinguish between multiple queries in a single script.
2. **Portability:** Ensures your code works across all database software without modification.

> **Bottom Line:** Always use it. It’s a "best practice" that prevents errors when running batches of code.

---

<h1 style="color:red">4. Breaking the lengthy SQL statements into multiple lines</h1>

### **SQL Line Breaks & Readability**

**The Concept:**
SQL is **whitespace-independent**. You can write a query in one long line or break it across several.

**Why break lines?**

* **Clarity:** Long strings of code are hard to read and debug.
* **Structure:** Separating clauses (SELECT, FROM, WHERE) makes the logic "pop" visually.



**Example Transformation:**

* **Hard to read (Single Line):**
`SELECT CustomerID, CustomerName, ContactName, City FROM Customers WHERE Country = 'France' LIMIT 10;`
* **Easy to read (Multi-line):**
```sql
SELECT CustomerID, CustomerName, ContactName, City 
FROM Customers 
WHERE Country = 'France' 
LIMIT 10;

```



> **Best Practice:** Start every major clause (`FROM`, `WHERE`, `ORDER BY`, etc.) on a **new line** to keep your code professional and readable.

---


<h1 style="color:red">5. Types of SQL Statements</h1>

SQL statements are grouped into five main categories based on their functionality:

### **1. DDL (Data Definition Language)**

Defines or modifies the **structure** of the database (the "container").

* **`CREATE`**: Create databases or tables.
* **`ALTER`**: Modify existing structure (add/drop columns).
* **`DROP`**: Delete tables or databases entirely.
* **`TRUNCATE`**: Remove all records from a table (but keep the structure).

### **2. DML (Data Manipulation Language)**

Manages the **data** inside the tables.

* **`INSERT`**: Add new rows.
* **`UPDATE`**: Modify existing rows.
* **`DELETE`**: Remove specific rows.

### **3. DQL (Data Query Language)**

Used to **fetch** or retrieve data.

* **`SELECT`**: The most common command; pulls data based on criteria.

### **4. DCL (Data Control Language)**

Controls **security** and access permissions.

* **`GRANT`**: Give user access.
* **`REVOKE`**: Take away user access.

### **5. TCL (Transaction Control Language)**

Manages **transactions** (groups of DML tasks).

* **`COMMIT`**: Save changes permanently.
* **`ROLLBACK`**: Undo changes if an error occurs.
* **`SAVEPOINT`**: Create a "check-point" within a transaction.

---

<h1 style="color:red">6. Select Command</h1>

The **`SELECT`** command is the primary tool for retrieving (fetching) data from a database.

### **1. Basic Syntax**

To pull specific columns:

```sql
SELECT Column1, Column2 
FROM TableName;

```

To pull **all** columns (using the wildcard `*`):

```sql
SELECT * FROM TableName;

```



### **2. Essential Clauses**

A `SELECT` statement usually follows this order:

| Clause | Purpose | Example |
| --- | --- | --- |
| **`SELECT`** | Choose columns | `SELECT Name, Age` |
| **`FROM`** | Choose the table | `FROM Students` |
| **`WHERE`** | Filter rows | `WHERE Age > 18` |
| **`ORDER BY`** | Sort results | `ORDER BY Name ASC` |
| **`LIMIT`** | Restrict row count | `LIMIT 5` |



### **3. Key Features**

* **Aliases (`AS`):** Rename a column in the output for better readability.
* `SELECT CustomerName AS Client FROM Customers;`


* **Distinct (`DISTINCT`):** Remove duplicate values from the result.
* `SELECT DISTINCT Country FROM Customers;`


* **Calculations:** You can perform math directly in the select line.
* `SELECT Price * 1.10 FROM Products;`


---

<h1 style="color:red">7. Aliases for Table column names (AS keyword)</h1>

An **Alias** is a temporary name given to a table or a column to make the result set more readable or to simplify complex queries.

### **1. Column Aliases (`AS`)**

Used to rename a column in the query output. It does **not** change the actual column name in the database.

* **Syntax:**
```sql
SELECT ColumnName AS AliasName FROM TableName;

```


* **With Spaces:** Use double quotes or square brackets if the alias contains spaces.
```sql
SELECT CustomerName AS "Client Name" FROM Customers;

```





### **2. Table Aliases**

Used to give a table a shorthand name, which is extremely helpful when joining multiple tables.

* **Syntax:**
```sql
SELECT t.ColumnName FROM TableName AS t;

```


* **Example:**
```sql
SELECT o.OrderID, c.CustomerName 
FROM Orders AS o, Customers AS c 
WHERE o.CustomerID = c.CustomerID;

```





### **3. Key Rules**

* **Optional `AS`:** In most RDBMS, the `AS` keyword is optional.
* `SELECT Name AS Nickname` is the same as `SELECT Name Nickname`.


* **Scope:** Aliases only exist for the duration of that specific query.
* **Clarity:** Use them to clarify calculations (e.g., `SELECT Price * 1.1 AS "Taxed Price"`).

---

<h1 style="color:red">8. Distinct keyword</h1>

The `DISTINCT` keyword is used to remove duplicate records from your results, ensuring you only see **unique** values.

### **1. Basic Syntax**

It is placed immediately after the `SELECT` command.

```sql
SELECT DISTINCT Column1 FROM TableName;

```



### **2. Key Behaviors**

* **Single Column:** Returns every unique entry in that column.
* *Example:* `SELECT DISTINCT Country FROM Customers;` (Lists each country only once).


* **Multiple Columns:** The combination of values across all selected columns must be unique.
* *Example:* `SELECT DISTINCT City, Country FROM Customers;` (Returns unique city-country pairs).


* **NULL Values:** `DISTINCT` treats `NULL` as a unique value. If multiple rows have `NULL` in that column, only one `NULL` will be returned.



### **3. Difference: SELECT vs. SELECT DISTINCT**

| Command | Result |
| --- | --- |
| `SELECT Color` | Red, Blue, Red, Green, Blue |
| `SELECT DISTINCT Color` | Red, Blue, Green |

---

### **4. Best Practice**

Avoid using `DISTINCT` if your query logic naturally returns unique rows (like selecting by Primary Key). Using it unnecessarily can **slow down** your database because it requires an extra sorting step to find duplicates.

---

<h1 style="color:red">9. Limit Keyword</h1>

The `LIMIT` clause is used to specify the **maximum number of records** the database should return.

### **1. Basic Syntax**

It is placed at the very end of your SQL statement.

```sql
SELECT ColumnNames 
FROM TableName 
LIMIT Number;

```



### **2. Key Use Cases**

* **Performance:** Prevents the system from loading millions of rows when you only need a quick look.
* **Top Results:** Often used with `ORDER BY` to find the "Top 5" or "Bottom 10" entries.
* *Example:* `SELECT * FROM Products ORDER BY Price DESC LIMIT 5;` (The 5 most expensive products).


* **Pagination:** Used by websites to show "Page 1, 2, 3" by combining `LIMIT` with `OFFSET`.


### **3. The OFFSET Extension**

If you want to skip a specific number of rows before starting to return results:

* **Syntax:** `LIMIT 5 OFFSET 10;` (Skips the first 10 rows and returns the next 5).


### **4. Database Variations**

Be careful! The syntax for limiting results changes depending on the software you use:

* **MySQL/PostgreSQL:** `LIMIT 10`
* **SQL Server:** `SELECT TOP 10 Column FROM Table`
* **Oracle:** `FETCH FIRST 10 ROWS ONLY`

---


