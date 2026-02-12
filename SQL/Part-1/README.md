## **Database Concepts Overview**

1. Database
2. DBMS
3. Data Models
4. RDBMS

---

### **1. Database**

* A place to store **structured data**.
* Used for storing related data such as **Employee ID, Employee Name, Employee Salary, Department**, etc.

### **2. DBMS (Database Management System)**

* **Definition:** Software that stores data in an organized way, making it easier to create, retrieve, and update the data.
* **Data Flow:**
* `Application` -> `DBMS Software` -> (stores data) -> `Database`
* `Application` <- `DBMS Software` <- (reads data) <- `Database`


* **Examples:** MySQL, Oracle, MongoDB, Cassandra.

---

### **3. Data Models**

* Defines how related data is connected to each other and stored inside the database.

**Types of Data Models:**

1. **Hierarchical Model**
2. **Network Model**
3. **Entity-Relationship Model**
4. **Relational Model**

> **Note:** DBMS software can use any of the above models. The **Relational Model** is the most popular, where data is stored in **tables** consisting of **rows and columns**.

---

### **4. RDBMS (Relational DBMS)**

* A specific type of software that strictly follows the **Relational Model**.
* All RDBMS software stores data in the form of **tables**.
* **Examples:** Oracle, PostgreSQL, DB2, MySQL.

---

### **What is SQL?**

* **SQL (Structured Query Language):** The programming language used specifically for **RDBMS** software (not all DBMS).
* **Capabilities of SQL:**
* **Insert Data** (Store the data)
* **Retrieve Data**
* **Update Data**
* **Delete Data**
* and many more.


---

## **SQL Resources & Syntax Basics**

### **Practice Tool**

To practice SQL without installing any software, use the W3Schools editor:

* **URL:** [W3Schools SQL Tryit Editor](https://www.w3schools.com/sql/trysql.asp?filename=trysql_asc)

---

### **Commands vs. Keywords in SQL**

| Category | Definition | Examples |
| --- | --- | --- |
| **Commands** | **Actions** that perform operations on the data or structure. | `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `CREATE` |
| **Keywords** | **Reserved words** used within commands to help construct the statement. | `FROM`, `WHERE`, `AND`, `OR`, `ORDER BY`, `NULL` |

> **In short:** Commands tell SQL **what to do**, while keywords help **structure** how the operation is carried out.

---

### **Example Breakdown**

Consider the following statement:
`SELECT name, age FROM users WHERE age > 18 AND status IS NOT NULL ORDER BY name;`

* **Command:** `SELECT`
* **Keywords:** `FROM`, `WHERE`, `AND`, `IS`, `NOT`, `NULL`, `ORDER BY`

---

### **Detailed Keyword/Command List**

**1. Primary Commands:**

* **SELECT:** Retrieves data from the database.
* **INSERT:** Adds new data.
* **UPDATE:** Modifies existing data.
* **DELETE:** Removes data.
* **CREATE:** Creates database objects (like tables).

**2. Essential Keywords:**

* **FROM:** Specifies which table to look in.
* **WHERE:** Filters results based on a condition.
* **AND / OR:** Logical operators used to combine conditions.
* **ORDER BY:** Sorts the resulting data.
* **NULL:** Represents a missing or empty value.

---

## **The SELECT Command**

### **Purpose**

Retrieves data from database tables. It is the most commonly used command in SQL.

### **Basic Syntax**

```sql
SELECT column1, column2 FROM table_name;

```

---

### **Key Variations**

| Variation | Example Code | Description |
| --- | --- | --- |
| **Select All** | `SELECT * FROM Customers;` | Uses the asterisk (`*`) wildcard to fetch every column. |
| **Specific Columns** | `SELECT Name, Email FROM Customers;` | Fetches only the data you actually need. |
| **Filter (WHERE)** | `SELECT * FROM Customers WHERE Age > 25;` | Limits results to rows meeting a specific condition. |
| **Unique (DISTINCT)** | `SELECT DISTINCT City FROM Customers;` | Removes duplicate values from the results. |
| **Sort (ORDER BY)** | `SELECT * FROM Customers ORDER BY Name ASC;` | Sorts data alphabetically or numerically (`ASC` or `DESC`). |
| **Restrict (LIMIT)** | `SELECT * FROM Customers LIMIT 10;` | Returns only the first 10 rows. |
| **Aggregates** | `SELECT COUNT(*), AVG(Salary) FROM Employees;` | Performs calculations like count, average, or sum. |
| **Group (GROUP BY)** | `SELECT City, COUNT(*) FROM Customers GROUP BY City;` | Groups rows that have the same values into summary rows. |
| **Joins** | `SELECT Orders.OrderID, Customers.Name FROM Orders JOIN Customers ON Orders.CustomerID = Customers.CustomerID;` | Combines rows from two or more tables based on a related column. |
| **Aliases (AS)** | `SELECT Name AS CustomerName FROM Customers;` | Renames a column or table temporarily for the result set. |

---

### **Common Operators in WHERE**

* **Comparison:** `=`, `!=` (or `<>`), `>`, `<`, `>=`, `<=`
* **Range & List:** `BETWEEN`, `IN`, `LIKE`, `IS NULL`
* **Logical:** `AND`, `OR`, `NOT`

---

### **Execution Order**

It is important to note that SQL does not process clauses in the order they are written. The database follows this sequence:

1. **FROM** (Identify the table)
2. **WHERE** (Filter rows)
3. **GROUP BY** (Aggregate data)
4. **HAVING** (Filter grouped data)
5. **SELECT** (Return the final columns/aliases)
6. **ORDER BY** (Sort the results)
7. **LIMIT** (Restrict the final count)

---

## **The DISTINCT Keyword**

### **Purpose**

The `DISTINCT` keyword is used to remove duplicate rows from query results, ensuring that only unique values are returned.

---

### **Examples**

**1. Without DISTINCT**

```sql
SELECT city FROM users;

```

* **Result:** `New York`, `London`, `New York`, `Paris`, `London` (duplicates included)

**2. With DISTINCT**

```sql
SELECT DISTINCT city FROM users;

```

* **Result:** `New York`, `London`, `Paris` (duplicates removed)

---

### **Advanced Usage**

* **Multiple Columns:**
```sql
SELECT DISTINCT city, country FROM users;

```


* This returns unique **combinations** of city and country. If two rows have the same city but different countries, both will be shown.


* **With COUNT (Aggregation):**
```sql
SELECT COUNT(DISTINCT city) FROM users;

```


* This calculates the total number of unique cities in the table.



---

### **Key Point**

> **Important:** `DISTINCT` applies to the **entire row** specified in the SELECT clause, not just the first column following it.

---

## **SQL Case Sensitivity & Semicolons**

### **1. Case Sensitivity**

SQL is **case-insensitive**, meaning the database engine treats uppercase and lowercase letters the same for commands, table names, and column names.

**The following three queries will execute exactly the same:**

* **Mixed Case:** `Select Country, PostalCode From Customers;`
* **Lower Case:** `select country, postalcode from customers;`
* **Upper Case:** `SELECT COUNTRY, POSTALCODE FROM CUSTOMERS;`

> **Best Practice:** > It is standard professional practice to write **SQL keywords in UPPERCASE** and **identifiers (table/column names) in PascalCase or lowercase**.
> **Example:** `SELECT Country, PostalCode FROM Customers;`

---

### **2. The Semicolon ( ; )**

**Is it mandatory?**

* **For single statements:** Not always mandatory in many database systems (like MySQL or SQL Server).
* **For multiple statements:** **Yes.** It is required to separate distinct commands sent in a single batch.

**Why you should always use it:**

* **Good Habit:** It clearly marks the end of a command.
* **Compatibility:** Some database systems (like Oracle) or specific tools require it.
* **Prevents Errors:** It ensures your code doesn't break when you add a second or third statement to your script.

---
## **The WHERE Clause**

### **Purpose**

The `WHERE` clause is used to filter records. it ensures that only the rows that fulfill a specified condition are returned.

### **Syntax**

```sql
SELECT column1, column2 
FROM table_name 
WHERE condition;

```

---

### **Comparison Operators**

These operators are used to compare a column value against a specific value.

| Operator | Description |
| --- | --- |
| `=` | Equal to |
| `!=` or `<>` | Not equal to |
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal to |
| `<=` | Less than or equal to |

**Examples:**

* `SELECT * FROM Employees WHERE Age > 30;`
* `SELECT * FROM Products WHERE Price <= 100;`
* `SELECT * FROM Customers WHERE City = 'London';`

---

### **Logical Operators**

Used to combine multiple conditions in a single `WHERE` clause.

* **AND:** All conditions must be true.
* `SELECT * FROM Employees WHERE Age > 25 AND Salary > 50000;`


* **OR:** At least one condition must be true.
* `SELECT * FROM Customers WHERE City = 'London' OR City = 'Paris';`


* **NOT:** Negates a condition.
* `SELECT * FROM Products WHERE NOT Price > 100;`



---

### **Special Operators**

* **BETWEEN:** Filters values within a specific range (inclusive).
* `SELECT * FROM Products WHERE Price BETWEEN 10 AND 50;`


* **IN:** Specifies multiple possible values for a column.
* `SELECT * FROM Customers WHERE City IN ('London', 'Paris', 'Berlin');`


* **LIKE:** Searches for a specified pattern in a column.
* `'J%'` → Starts with J
* `'%son'` → Ends with son
* `'%an%'` → Contains "an"


* **IS NULL / IS NOT NULL:** Checks for empty (null) or non-empty values.
* `SELECT * FROM Customers WHERE Email IS NULL;`



---

### **Combining Conditions**

You can use parentheses to group conditions and control the order of evaluation.

```sql
SELECT * FROM Employees 
WHERE (Age > 25 AND City = 'London') 
   OR (Salary > 60000 AND Department = 'IT');

```

---

### **Key Points**

* **Filtering Order:** `WHERE` filters rows **BEFORE** any grouping (`GROUP BY`) occurs.
* **Versatility:** Can be used with `SELECT`, `UPDATE`, and `DELETE` statements.
* **Data Types:** * **Text values** must be enclosed in single quotes: `'London'`.
* **Numeric values** do not need quotes: `100`.



---

## **The BETWEEN Operator**

### **Purpose**

Selects values within a specified range. This operator is highly efficient and makes your SQL queries much easier to read.

### **Syntax**

```sql
SELECT column_name 
FROM table_name 
WHERE column_name BETWEEN value1 AND value2;

```

---

### **Key Points**

* **Inclusive:** It includes both boundary values (`value1` and `value2`).
* **Versatile:** It works seamlessly with **numbers**, **text**, and **dates**.
* **Logic:** It is functionally equivalent to using `column >= value1 AND column <= value2`.

---

### **Examples**

#### **1. Numbers**

```sql
SELECT * FROM Products WHERE Price BETWEEN 10 AND 50;

```

* **Result:** Returns products with prices from 10 to 50 (including exactly 10 and 50).

#### **2. Dates**

```sql
SELECT * FROM Orders 
WHERE OrderDate BETWEEN '2024-01-01' AND '2024-12-31';

```

* **Result:** Returns all orders placed during the entire year of 2024.

#### **3. Text (Alphabetical Order)**

```sql
SELECT * FROM Customers 
WHERE Name BETWEEN 'A' AND 'M';

```

* **Result:** Returns names starting with the letters A through M.

---

### **NOT BETWEEN**

If you want to find values **outside** of a specific range, use `NOT BETWEEN`:

```sql
SELECT * FROM Products WHERE Price NOT BETWEEN 10 AND 50;

```

* **Logic:** Returns products where the price is either **less than 10** or **greater than 50**.

---

### **Comparison & Readability**

| Method | Syntax |
| --- | --- |
| **Using BETWEEN** | `WHERE Age BETWEEN 25 AND 35` |
| **Using Comparison** | `WHERE Age >= 25 AND Age <= 35` |

> **Pro Tip:** Always prefer `BETWEEN` for ranges. It is "cleaner" and reduces the risk of logic errors (like forgetting to include the `=` sign in `>=`).

---

### **Common Use Cases**

* **Price ranges:** `BETWEEN 100 AND 500`
* **Date ranges:** `BETWEEN '2024-01-01' AND '2024-06-30'`
* **Age groups:** `BETWEEN 18 AND 65`
* **Score ranges:** `BETWEEN 70 AND 100`

---

## **The ORDER BY Clause**

### **Purpose**

Sorts query results in either ascending or descending order. This is essential for organizing data into meaningful lists, such as leaderboards or chronological records.

### **Syntax**

```sql
SELECT column1, column2 
FROM table_name 
ORDER BY column1 [ASC|DESC];

```

---

### **ASC vs DESC**

| Type | Order | Data Behavior |
| --- | --- | --- |
| **ASC** (Ascending) | Smallest to Largest | **Numbers:** 1, 2, 3... <br>

<br> **Text:** A, B, C... <br>

<br> **Dates:** Oldest to Newest |
| **DESC** (Descending) | Largest to Smallest | **Numbers:** 10, 9, 8... <br>

<br> **Text:** Z, Y, X... <br>

<br> **Dates:** Newest to Oldest |

> **Note:** `ASC` is the default setting. If you do not specify an order, SQL will automatically sort in ascending order.

---

### **Key Examples**

**1. Single Column Sorting**

* `SELECT * FROM Employees ORDER BY Salary;` (Default Ascending)
* `SELECT * FROM Employees ORDER BY Salary DESC;` (High to Low)

**2. Multiple Columns**

```sql
SELECT * FROM Employees 
ORDER BY Department ASC, Salary DESC;

```

* **Result:** First sorts by **Department** (A-Z). If multiple employees are in the same department, it then sorts those employees by **Salary** (High to Low).

**3. By Column Position**

* `SELECT Name, Age, Salary FROM Employees ORDER BY 3 DESC;`
* **Result:** Sorts by the 3rd column listed in the `SELECT` statement (`Salary`).

---

### **Advanced Use Cases**

* **Text Sorting:**
* `SELECT * FROM Customers ORDER BY Name ASC;` (A to Z)


* **Date Sorting:**
* `SELECT * FROM Orders ORDER BY OrderDate DESC;` (Newest first)


* **Combined with WHERE:**
```sql
SELECT * FROM Products 
WHERE Price > 100 
ORDER BY Price DESC;

```



---

### **Key Points to Remember**

* **Execution Order:** `ORDER BY` is executed **last** (after `WHERE`, `GROUP BY`, and `HAVING`).
* **Flexibility:** You can sort by columns that are **not** included in your `SELECT` list.
* **NULLs:** Handling of `NULL` values varies by database, but they typically appear first in `ASC` and last in `DESC`.

### **Common Practical Applications**

* **Top Earners:** `ORDER BY Salary DESC LIMIT 10`
* **Alphabetical Rosters:** `ORDER BY Name ASC`
* **Leaderboards:** `ORDER BY Score DESC`

---
