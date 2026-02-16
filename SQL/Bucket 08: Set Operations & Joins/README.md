

<h1 style="color:red">1. Set Operators</h1>

Set operators are used to combine the result sets of two or more `SELECT` statements into a single result. While **Joins** combine columns horizontally, **Set Operators** combine rows vertically.


### **1. Rules for Using Set Operators**

To use any set operator, the following conditions must be met:

* Both queries must have the **same number of columns**.
* The columns in the corresponding positions must have **compatible data types**.
* The column names in the final result are taken from the **first** `SELECT` statement.


### **2. Types of Set Operators**

#### **A. UNION**

Combines the results of two queries and **removes duplicate rows**.

* **Syntax:** `SELECT col FROM table1 UNION SELECT col FROM table2;`

#### **B. UNION ALL**

Combines the results of two queries and **keeps all rows**, including duplicates.

* **Performance:** Faster than `UNION` because it doesn't spend time searching for duplicates.
* **Syntax:** `SELECT col FROM table1 UNION ALL SELECT col FROM table2;`

#### **C. INTERSECT**

Returns only the rows that are **common** to both queries.

* *Note: MySQL versions prior to 8.0.31 do not support this directly; you must use an `IN` or `EXISTS` clause instead.*
* **Syntax:** `SELECT col FROM table1 INTERSECT SELECT col FROM table2;`

#### **D. EXCEPT (or MINUS)**

Returns rows from the first query that are **not present** in the second query.

* *Note: Similar to INTERSECT, this was added in MySQL 8.0.31.*
* **Syntax:** `SELECT col FROM table1 EXCEPT SELECT col FROM table2;`


### **3. Summary Comparison**

| Operator | Duplicates? | Result |
| --- | --- | --- |
| **`UNION`** | Removed | Everything from both (Distinct) |
| **`UNION ALL`** | Kept | Everything from both (Total) |
| **`INTERSECT`** | N/A | Only common elements |
| **`EXCEPT`** | N/A | Elements in A but not in B |


### **4. Usage Tip**

If you want to sort the final combined result, the `ORDER BY` clause must be placed at the very end of the **last** `SELECT` statement.


---
<h1 style="color:red">2. Union Operator</h1>

The **`UNION`** operator is a set operator used to combine the result sets of two or more `SELECT` statements into a single, unified result set.


### **1. Core Functionality**

* **Combines Rows:** It stacks the results of one query on top of another.
* **Removes Duplicates:** By default, `UNION` performs a distinct operation, meaning it removes any identical rows found in the combined results.
* **Vertical Integration:** Unlike a `JOIN` (which adds columns side-by-side), `UNION` adds rows one after another.


### **2. Rules for Usage**

For a `UNION` to work, the queries must follow these strict rules:

1. **Column Count:** Every `SELECT` statement must have the **same number of columns**.
2. **Data Types:** Corresponding columns must have **compatible data types** (e.g., if column 1 in Query A is a string, column 1 in Query B should also be a string).
3. **Column Order:** The columns must be in the same order in each `SELECT` statement.


### **3. Basic Syntax**

```sql
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;

```

* **Example:** To get a unique list of cities where both customers and suppliers are located:
```sql
SELECT City FROM Customers
UNION
SELECT City FROM Suppliers;

```




### **4. UNION vs. UNION ALL**

* **`UNION`**: Filters the result set for unique rows (Slower due to the "Distinct" check).
* **`UNION ALL`**: Includes every row from both queries, including duplicates (Faster because no filtering occurs).


### **5. Key Characteristics**

* **Column Headers:** The column names in the final result are taken from the **first** `SELECT` statement.
* **Sorting:** If you want to sort the combined result, you must place the `ORDER BY` clause at the very end of the **entire** statement (after the last `SELECT`).
* **Filtering:** You can use `WHERE` clauses within each individual `SELECT` statement to filter data before it is combined.


### **Summary Table**

| Feature | `UNION` | `UNION ALL` |
| --- | --- | --- |
| **Duplicates** | Automatically removed | Kept in the result |
| **Performance** | Slower (requires sorting/filtering) | Faster (direct append) |
| **Common Use** | Creating unique lists | Merging large datasets where duplicates matter |


---
<h1 style="color:red">3. Union all operator</h1>

The **`UNION ALL`** operator is used to combine the result sets of two or more `SELECT` statements into one. Unlike the standard `UNION` operator, it **includes all duplicates** in the final output.


### **1. Core Functionality**

* **Combines All Rows:** It simply appends the results of the second query to the end of the first.
* **Retains Duplicates:** If a row exists in both tables, or twice in the same table, it will appear multiple times in the result.
* **High Performance:** Because it does not scan for duplicates or perform a "distinct" sort, it is significantly **faster** than `UNION`.


### **2. Strict Requirements**

To use `UNION ALL`, your queries must match in:

1. **Number of Columns:** Both queries must select the same number of fields.
2. **Data Types:** Corresponding columns must have compatible types.
3. **Order:** Columns must be in the same logical order.


### **3. Basic Syntax**

```sql
SELECT column_name(s) FROM table1
UNION ALL
SELECT column_name(s) FROM table2;

```

**Example:**
If Table A has `[1, 2]` and Table B has `[2, 3]`:

* `UNION` returns: `[1, 2, 3]`
* `UNION ALL` returns: `[1, 2, 2, 3]`


### **4. UNION vs. UNION ALL**

| Feature | `UNION` | `UNION ALL` |
| --- | --- | --- |
| **Duplicates** | Removed | **Kept** |
| **Speed** | Slower (requires overhead to find duplicates) | **Faster** (no processing required) |
| **Memory Usage** | Higher (stores data to compare) | **Lower** |
| **Result Size** | Usually smaller | Usually larger |


### **5. Key Usage Tips**

* **When to use:** Use `UNION ALL` when you know there are no duplicates, or when you specifically want to see every instance of a record (e.g., merging transaction logs from two different months).
* **Sorting:** To sort the entire combined list, place the `ORDER BY` clause only after the **final** `SELECT` statement.
* **Aliases:** The column names in the final result are inherited from the **first** query.


---

<h1 style="color:red">4. Intersect operator</h1>

### **`INTERSECT` Operator**

The **`INTERSECT`** operator is a set operator used to return only the rows that are **common** to two or more `SELECT` statements.



### **1. Core Functionality**

* **Finds Matches:** It compares the results of two queries and outputs only the records that appear in **both**.
* **Distinct Results:** By default, it removes duplicate rows from the final result set.
* **Logic:** It acts like the "AND" of set operators (e.g., "Show me values that are in Query A **AND** in Query B").


### **2. Rules for Usage**

Just like `UNION`, `INTERSECT` requires:

1. **Same Column Count:** Both queries must select the same number of columns.
2. **Compatible Data Types:** Corresponding columns must have similar data types.
3. **Same Order:** Columns must be listed in the same order in both `SELECT` statements.


### **3. Syntax (MySQL 8.0.31+ Only)**

Modern versions of MySQL support this directly:

```sql
SELECT column_name(s) FROM table1
INTERSECT
SELECT column_name(s) FROM table2;

```

**Example:** To find customers who are also suppliers:

```sql
SELECT ContactName FROM Customers
INTERSECT
SELECT ContactName FROM Suppliers;

```


### **4. How to use INTERSECT in Older MySQL Versions**

Since older versions of MySQL do not support the `INTERSECT` keyword, you must achieve the same result using an **`IN`** clause or an **`INNER JOIN`**.

* **Using `IN`:**
```sql
SELECT DISTINCT column_name FROM table1
WHERE column_name IN (SELECT column_name FROM table2);

```


* **Using `INNER JOIN`:**
```sql
SELECT DISTINCT t1.column_name 
FROM table1 t1
INNER JOIN table2 t2 ON t1.column_name = t2.column_name;

```




### **5. Summary Table**

| Operator | Result | Duplicates |
| --- | --- | --- |
| **`UNION`** | All rows from both tables | Removed |
| **`INTERSECT`** | **Only rows present in both** | Removed |
| **`EXCEPT`** | Rows in Table A but not in B | Removed |


---


<h1 style="color:red">5. Minus operator</h1>

In SQL, the **`MINUS`** operator (known as **`EXCEPT`** in MySQL and standard SQL) is used to return all unique rows from the first `SELECT` statement that are **not** present in the second `SELECT` statement.


### **1. Core Functionality**

* **Finds Differences:** It effectively "subtracts" the results of the second query from the first.
* **Distinct Results:** It automatically removes duplicate rows from the final result.
* **Direction Matters:** The order of the queries is critical. `A MINUS B` is not the same as `B MINUS A`.


### **2. Syntax (MySQL 8.0.31+)**

While Oracle uses the keyword `MINUS`, MySQL uses the standard keyword **`EXCEPT`**.

```sql
SELECT column_name FROM TableA
EXCEPT
SELECT column_name FROM TableB;

```

**Example:** To find products that have been defined in the system but **never** ordered:

```sql
SELECT ProductID FROM Products
EXCEPT
SELECT ProductID FROM Orders;

```


### **3. How to use MINUS in Older MySQL Versions**

If you are using a version of MySQL older than 8.0.31, you must simulate this behavior using a **`LEFT JOIN`** or the **`NOT IN`** operator.

* **Using `LEFT JOIN` (Recommended for Performance):**
```sql
SELECT t1.column_name 
FROM TableA t1
LEFT JOIN TableB t2 ON t1.id = t2.id
WHERE t2.id IS NULL;

```


* **Using `NOT IN`:**
```sql
SELECT column_name FROM TableA
WHERE column_name NOT IN (SELECT column_name FROM TableB);

```




### **4. Strict Requirements**

To use `EXCEPT` / `MINUS`, the queries must align:

1. **Same Column Count:** Both queries must select the same number of columns.
2. **Compatible Data Types:** Corresponding columns must have the same or similar data types.
3. **Order:** Columns must be in the same logical order in both statements.


### **5. Summary Comparison**

| Operator | Result |
| --- | --- |
| **`UNION`** | Rows in A **OR** B (Combined) |
| **`INTERSECT`** | Rows in A **AND** B (Common) |
| **`EXCEPT / MINUS`** | Rows in A **BUT NOT** B (Difference) |


---

<h1 style="color:red">6. tables and aliases</h1>

In SQL, **Tables** are the physical storage units for data, while **Aliases** are temporary names given to tables or columns to make queries more readable and efficient.


### **1. What is an Alias?**

An alias is a shorthand name that exists only for the duration of a specific query. You create an alias using the **`AS`** keyword (though `AS` is optional in most databases).

* **Column Alias:** Used to give a descriptive heading to a result or a calculated field.
* **Table Alias:** Used to shorten a table name, especially useful when joining multiple tables.


### **2. Column Aliases**

Column aliases are perfect for renaming columns in your output for better clarity, or for naming results of functions (like `SUM` or `COUNT`).

* **Syntax:** `SELECT column_name AS alias_name FROM table_name;`
* **Example:**
```sql
SELECT Name AS Student_Name, DOB AS Birthday 
FROM Students;

```


* **Note:** If the alias contains spaces, you must enclose it in quotes: `AS "Full Name"`.


### **3. Table Aliases**

Table aliases are used to give a table a temporary name (usually 1–2 letters). They are essential when you need to distinguish between columns that have the same name in different tables.

* **Syntax:** `SELECT alias.column FROM table_name AS alias;`
* **Example:**
```sql
SELECT s.Name, c.CourseName
FROM Students AS s
JOIN Courses AS c ON s.CourseID = c.ID;

```




### **4. Why Use Aliases?**

* **Readability:** It makes complex queries with multiple joins much easier to follow.
* **Ambiguity Resolution:** When two tables have a column named `ID`, aliases help the database know exactly which one you mean (`s.ID` vs `c.ID`).
* **Formatting:** It allows you to create professional-looking report headers directly from the SQL query.
* **Calculations:** Essential for naming derived columns.
* *Example:* `SELECT (Price * Quantity) AS Total_Cost FROM Orders;`




### **5. Summary Table**

| Type | Purpose | Example |
| --- | --- | --- |
| **Column Alias** | Renames a column in the output. | `SELECT Age AS Student_Age` |
| **Table Alias** | Provides a shorthand for a table name. | `FROM Employees AS e` |


---
<h1 style="color:red">7. Joins</h1>

A **JOIN** is an SQL operation used to combine rows from two or more tables based on a related column between them (usually a **Foreign Key**). While Set Operators (like UNION) combine data vertically, Joins combine data **horizontally**, adding more columns to your result set.



### **1. Common Types of Joins**

#### **A. INNER JOIN**

The most common join. It returns records only when there is a match in **both** tables. If a row in Table A doesn't have a corresponding match in Table B, it is excluded.

* **Use Case:** To find all students who are assigned to a specific class.

#### **B. LEFT (OUTER) JOIN**

Returns **all** records from the left table and the matched records from the right table. If there is no match, the result from the right side will contain `NULL`.

* **Use Case:** To list all students, including those who haven't registered for any courses yet.

#### **C. RIGHT (OUTER) JOIN**

Returns **all** records from the right table and the matched records from the left table. If there is no match, the result from the left side will contain `NULL`.

* **Use Case:** To list all courses, including those that currently have no students enrolled.

#### **D. FULL (OUTER) JOIN**

Returns all records when there is a match in either the left or right table.

* *Note: MySQL does not support this directly; you must use a `UNION` of a `LEFT` and `RIGHT` join.*


### **2. Special Joins**

* **CROSS JOIN:** Returns the **Cartesian Product** of the two tables (every row from Table A is paired with every row from Table B). It does not require an `ON` clause.
* **SELF JOIN:** A regular join, but the table is joined with itself. This is useful for hierarchical data, such as an `Employees` table where one column lists the `ManagerID` (who is also an employee).


### **3. Basic Syntax**

```sql
SELECT table1.column, table2.column
FROM table1
JOIN_TYPE table2 ON table1.common_column = table2.common_column;

```

**Example (Inner Join):**

```sql
SELECT Orders.OrderID, Customers.CustomerName
FROM Orders
INNER JOIN Customers ON Orders.CustomerID = Customers.CustomerID;

```


### **4. Summary Table**

| Join Type | Match Requirement | Result of Non-Match |
| --- | --- | --- |
| **`INNER`** | Both tables must match. | Row is hidden. |
| **`LEFT`** | Left table is preserved. | `NULL` for right table columns. |
| **`RIGHT`** | Right table is preserved. | `NULL` for left table columns. |
| **`CROSS`** | No match required. | Every combination of rows. |



---

