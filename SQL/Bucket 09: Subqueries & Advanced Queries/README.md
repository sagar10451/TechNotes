

<h1 style="color:red">1. Sub Query</h1>

A **Subquery** (also known as an **Inner Query** or **Nested Query**) is a query within another SQL query. It is used to return data that will be used in the main (outer) query as a condition to further restrict the data to be retrieved.


### **1. Basic Structure**

A subquery is always enclosed in **parentheses** and typically executes first. The result of the subquery is then passed to the outer query.

```sql
SELECT column_name 
FROM table_name 
WHERE column_name OPERATOR (SELECT column_name FROM table_name WHERE condition);

```


### **2. Types of Subqueries**

#### **A. Single-Row Subquery**

Returns only one row and one column. It is used with comparison operators like `=`, `>`, `<`, `>=`, `<=`.

* **Example:** Find employees whose salary is greater than the average salary.
```sql
SELECT Name FROM Employees 
WHERE Salary > (SELECT AVG(Salary) FROM Employees);

```



#### **B. Multi-Row Subquery**

Returns multiple rows but a single column. It is used with operators like `IN`, `ANY`, or `ALL`.

* **Example:** Find products belonging to categories that are "Electronic".
```sql
SELECT ProductName FROM Products 
WHERE CategoryID IN (SELECT CategoryID FROM Categories WHERE Name = 'Electronics');

```



#### **C. Correlated Subquery**

A subquery that refers to a column in the outer query. Unlike standard subqueries, it executes once for every row processed by the outer query.

* **Example:** Find employees who earn more than the average salary in *their specific* department.


### **3. Where can you place a Subquery?**

* **In the `WHERE` clause:** To filter data (most common).
* **In the `FROM` clause:** To treat the subquery result as a temporary table (often called a **Derived Table**).
* *Note:* Derived tables must be given an **alias**.


* **In the `SELECT` clause:** To return a specific value for every row in the output.


### **4. Subquery vs. Join**

While both can often achieve the same result, they differ in performance and readability:

| Feature | Subquery | Join |
| --- | --- | --- |
| **Readability** | Easier for beginners to understand "nested" logic. | Can become complex with many tables. |
| **Performance** | Often slower (especially correlated ones). | Generally faster as the engine optimizes joins better. |
| **Purpose** | Best for returning a single value or a list. | Best for combining columns from multiple tables. |


### **5. Key Rules**

* Subqueries must be enclosed in parentheses.
* A subquery can be nested inside another subquery.
* The `ORDER BY` command cannot be used in a subquery (unless it's in the `FROM` clause).


---
<h1 style="color:red">2. In Operator</h1>

The **`IN`** operator is a logical operator used in a `WHERE` clause to check if a value matches any value in a specified list or the result of a subquery. It is essentially a shorthand for multiple `OR` conditions.


### **1. Basic Syntax**

You provide a comma-separated list of values inside parentheses.

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1, value2, ...);

```

**Example:**
Instead of writing `WHERE City = 'Delhi' OR City = 'Mumbai' OR City = 'Pune'`, you write:

```sql
SELECT * FROM Customers
WHERE City IN ('Delhi', 'Mumbai', 'Pune');

```


### **2. Using IN with a Subquery**

The `IN` operator is most powerful when used to filter data based on the results of another query.

```sql
SELECT * FROM Orders
WHERE CustomerID IN (SELECT CustomerID FROM Customers WHERE Country = 'India');

```


### **3. The NOT IN Operator**

By adding the `NOT` keyword, you can filter for records that do **not** match any value in the list.

* **Example:** `SELECT * FROM Students WHERE Grade NOT IN ('A', 'B');`
* **Warning:** If the list or subquery contains a **NULL** value, `NOT IN` will return no rows. Always ensure your subquery filters out NULLs when using `NOT IN`.


### **4. Key Characteristics**

* **Readability:** Makes code much cleaner than using multiple `OR` statements.
* **Flexibility:** Can be used with strings, numbers, and dates.
* **Performance:** Generally more efficient than multiple `OR` conditions, especially when the list is long.


### **5. Summary Table**

| Operator | Logic | Matches |
| --- | --- | --- |
| **`IN`** | Matches any value in the list. | `Value1 OR Value2 OR Value3` |
| **`NOT IN`** | Excludes all values in the list. | `Value != Value1 AND Value != Value2` |


---
<h1 style="color:red">3. using IN operator with multi row sub query</h1>

When a subquery returns more than one row of data, standard comparison operators like `=` or `>` will fail and throw an error. The **`IN`** operator is the most common way to handle these **Multi-Row Subqueries**.


### **1. How it Works**

The inner query (subquery) executes first and generates a list of values. The outer query then treats that list exactly like a hard-coded set of values, checking each row in the main table against that list.


### **2. Basic Syntax**

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name IN (
    SELECT column_name 
    FROM another_table 
    WHERE condition
);

```


### **3. Practical Example**

**Scenario:** You want to find all employees who work in departments located in 'Bengaluru'.

```sql
SELECT Name, Salary 
FROM Employees
WHERE DeptID IN (
    SELECT DeptID 
    FROM Departments 
    WHERE Location = 'Bengaluru'
);

```

* **The Inner Query:** Finds all `DeptID`s located in Bengaluru (e.g., `10, 12, 15`).
* **The Outer Query:** Becomes `WHERE DeptID IN (10, 12, 15)`.


### **4. Key Rules for Multi-Row Subqueries**

* **Column Matching:** The subquery must return only **one column**. If you try to select multiple columns in the subquery while using `IN`, the database will return an error.
* **Data Types:** The data type of the column in the outer query must match the data type of the column returned by the subquery.
* **The NULL Trap:** If your subquery returns a `NULL` value:
* **`IN`** will still work for other matches.
* **`NOT IN`** will return **nothing** (empty result set) because it cannot prove that a value is "not equal" to an unknown (`NULL`).




### **5. Comparison: IN vs. ANY vs. ALL**

| Operator | Usage | Meaning |
| --- | --- | --- |
| **`IN`** | Common | Equal to **any** value in the list. |
| **`ANY`** | Specific | Returns true if the condition is met for **at least one** value in the list. |
| **`ALL`** | Specific | Returns true only if the condition is met for **every** value in the list. |



---

<h1 style="color:red">4. using any operator in multi row sub query</h1>

The **`ANY`** operator is used in a `WHERE` or `HAVING` clause to compare a single value against a range of values returned by a multi-row subquery. It returns true if the comparison is true for **at least one** value in the result set.



### **1. How it Works**

The `ANY` operator must be preceded by a standard comparison operator: `=`, `<>`, `!=`, `>`, `<`, `>=`, or `<=`.

* **`= ANY`**: This is exactly the same as the **`IN`** operator.
* **`> ANY`**: Means "greater than the minimum value" in the list.
* **`< ANY`**: Means "less than the maximum value" in the list.



### **2. Basic Syntax**

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name operator ANY (SELECT column_name FROM table_name WHERE condition);

```


### **3. Practical Example**

**Scenario:** Find products that have a price greater than **any** product in the 'Office Supplies' category.

```sql
SELECT ProductName, Price
FROM Products
WHERE Price > ANY (
    SELECT Price 
    FROM Products 
    WHERE Category = 'Office Supplies'
);

```

* If 'Office Supplies' prices are `[10, 50, 100]`, the condition `> ANY` will return any product with a price greater than **10** (the minimum).


### **4. ANY vs. ALL**

It is helpful to compare `ANY` with its counterpart, `ALL`:

| Condition | Meaning for `ANY` | Meaning for `ALL` |
| --- | --- | --- |
| **`> (list)`** | Greater than the **minimum** value. | Greater than the **maximum** value. |
| **`< (list)`** | Less than the **maximum** value. | Less than the **minimum** value. |
| **`= (list)`** | Matches **any** value (same as `IN`). | Matches **all** values (rarely used). |


### **5. Key Characteristics**

* **Boolean Result:** The operator evaluates to `TRUE` if the subquery returns at least one row that satisfies the condition.
* **Empty Subquery:** If the subquery returns no rows, the condition evaluates to `FALSE`.
* **NULLs:** If the subquery contains `NULL` values and no match is found among the non-null values, the result is `UNKNOWN`.




---


<h1 style="color:red">5. using ALL operators with multi row sub query</h1>

The **`ALL`** operator is used to compare a single value against every value returned by a multi-row subquery. For the condition to be true, the comparison must be true for **all** values in the result set.


### **1. How it Works**

The `ALL` operator must be used with a standard comparison operator: `=`, `!=`, `>`, `<`, `>=`, or `<=`. It acts as a logical "AND" across the entire list.

* **`> ALL`**: Means "greater than the **maximum** value" in the list.
* **`< ALL`**: Means "less than the **minimum** value" in the list.
* **`!= ALL`**: This is exactly the same as the **`NOT IN`** operator.


### **2. Basic Syntax**

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name operator ALL (
    SELECT column_name 
    FROM table_name 
    WHERE condition
);

```


### **3. Practical Example**

**Scenario:** Find the employee(s) who earn more than **all** employees in the 'Sales' department.

```sql
SELECT Name, Salary
FROM Employees
WHERE Salary > ALL (
    SELECT Salary 
    FROM Employees 
    WHERE DeptName = 'Sales'
);

```

* If Sales salaries are `[3000, 4000, 5000]`, the condition `> ALL` only returns employees earning more than **5000**.


### **4. Summary Comparison: ANY vs. ALL**

| Operator | Comparison | Logic |
| --- | --- | --- |
| **`> ANY`** | Greater than the **minimum** | At least one match. |
| **`> ALL`** | Greater than the **maximum** | Must beat every value. |
| **`< ANY`** | Less than the **maximum** | At least one match. |
| **`< ALL`** | Less than the **minimum** | Must be lower than every value. |


### **5. Key Behaviors & Warnings**

* **Empty Subquery:** If the subquery returns no rows, the `ALL` condition is **always TRUE** (because there are no values to fail the comparison).
* **The NULL Trap:** If the subquery contains even one `NULL` value, the `ALL` condition will evaluate to `UNKNOWN`. This often results in **no rows** being returned.
* *Tip:* Always filter for `WHERE column IS NOT NULL` in your subquery when using `ALL`.




---

<h1 style="color:red">6. Exists operator</h1>

### **`EXISTS` Operator**

The **`EXISTS`** operator is a logical operator used to test for the existence of any record in a subquery. It is typically used in a **Correlated Subquery** to check if a relationship exists between tables.


### **1. How it Works**

* **Boolean Return:** `EXISTS` returns **TRUE** if the subquery returns one or more rows, and **FALSE** if the subquery returns no rows.
* **Efficiency:** It is highly efficient because the database stops searching as soon as it finds the **first** matching row (it doesn't need to process the entire subquery result set).
* **Select `*` is Okay:** In `EXISTS`, it doesn’t matter what columns the subquery returns (`SELECT 1` or `SELECT *` are common), because it only cares if *any* row exists.


### **2. Basic Syntax**

```sql
SELECT column_name(s)
FROM table_name
WHERE EXISTS (
    SELECT 1 
    FROM another_table 
    WHERE table_name.id = another_table.fk_id
);

```


### **3. Practical Example**

**Scenario:** Find all customers who have placed at least one order.

```sql
SELECT CustomerName 
FROM Customers c
WHERE EXISTS (
    SELECT 1 
    FROM Orders o 
    WHERE o.CustomerID = c.CustomerID
);

```


### **4. EXISTS vs. IN**

While they often yield the same result, they work differently:

| Feature | `EXISTS` | `IN` |
| --- | --- | --- |
| **Data Handling** | Checks for existence (stops at first match). | Fetches the entire list from the subquery first. |
| **Performance** | Faster for large datasets or complex subqueries. | Faster for small, static lists of values. |
| **NULL Handling** | Not affected by NULL values in the subquery. | Can return `UNKNOWN` if the subquery contains NULLs. |


### **5. The NOT EXISTS Operator**

The `NOT EXISTS` operator is the opposite; it returns TRUE only if the subquery returns **zero** rows.

* **Use Case:** Finding "Exclusion" data.
* **Example:** Find customers who have **never** placed an order.
```sql
SELECT CustomerName 
FROM Customers c
WHERE NOT EXISTS (
    SELECT 1 
    FROM Orders o 
    WHERE o.CustomerID = c.CustomerID
);

```




### **Summary Tip**

Use `EXISTS` when you are checking for a relationship between two tables (like "Has an order" or "Has a record"), especially if the tables are large.


---
<h1 style="color:red">7. using sub queries for retrieving the records from multiple tables</h1>

Using subqueries to retrieve data from multiple tables is a powerful alternative to using Joins. It allows you to use the result of one query (the "inner" query) to filter the results of another query (the "outer" query).


### **1. How it Works**

When you use a subquery for multiple tables, the inner query fetches a list of identifiers (like IDs) from a secondary table. The outer query then uses those IDs to pull the full records from the primary table.


### **2. Common Methods**

#### **A. Using the `IN` Operator**

This is the most common method for retrieving records from Table A based on a condition in Table B.

* **Example:** Find all customers who have placed an order.
```sql
SELECT * FROM Customers
WHERE CustomerID IN (SELECT CustomerID FROM Orders);

```



#### **B. Using the `EXISTS` Operator**

This is often used for high-performance retrieval when you only need to know if a related record exists, rather than bringing back the actual data from the second table.

* **Example:** Find employees who belong to a department located in 'London'.
```sql
SELECT * FROM Employees e
WHERE EXISTS (
    SELECT 1 FROM Departments d 
    WHERE d.DeptID = e.DeptID AND d.Location = 'London'
);

```



#### **C. In the `FROM` Clause (Derived Tables)**

You can treat a subquery as a temporary table and "retrieve" from it just like a real table.

* **Example:**
```sql
SELECT TempTable.AvgSalary
FROM (SELECT AVG(Salary) AS AvgSalary FROM Employees) AS TempTable;

```




### **3. Advantages of Subqueries for Multiple Tables**

* **Isolation:** You can test the inner query independently to ensure it returns the correct list of IDs.
* **Readability:** For many, nested logic ("Get X where ID is in this list...") is more intuitive than the "flat" logic of a Join.
* **Aggregation:** Subqueries are excellent when you need to retrieve records based on an aggregate value (like a `SUM` or `AVG`) from another table, which is difficult with a simple Join.


### **4. Subqueries vs. Joins for Retrieval**

| Feature | Subquery Retrieval | Join Retrieval |
| --- | --- | --- |
| **Data Source** | Usually returns columns from **only the outer table**. | Can return columns from **all joined tables**. |
| **Structure** | Vertical/Nested logic. | Horizontal/Combined logic. |
| **Performance** | Can be slower if the inner query is large. | Usually more efficient for large-scale data retrieval. |


### **5. Summary Checklist**

1. Ensure the subquery returns only **one column** if using `IN`, `ANY`, or `ALL`.
2. Use an **alias** if placing the subquery in the `FROM` clause.
3. Use a **Correlated Subquery** if the inner query needs to reference a column from the outer table.


---
<h1 style="color:red">8. using multiple sub queries in a single SQL statement</h1>

Using **multiple subqueries** in a single statement allows you to perform complex data analysis by breaking the logic into smaller, manageable parts. You can use multiple subqueries in different clauses (like `SELECT`, `FROM`, and `WHERE`) or nest them within each other.


### **1. Common Ways to Use Multiple Subqueries**

#### **A. Subqueries at the Same Level**

You can use multiple independent subqueries in the `WHERE` clause to filter data based on several different criteria from different tables.

* **Example:** Find employees who work in the 'Sales' department **AND** earn more than the average company salary.
```sql
SELECT Name FROM Employees
WHERE DeptID = (SELECT DeptID FROM Departments WHERE DeptName = 'Sales') -- Subquery 1
  AND Salary > (SELECT AVG(Salary) FROM Employees);                     -- Subquery 2

```



#### **B. Nested Subqueries (Subquery within a Subquery)**

Here, the innermost query executes first, passes its result to the middle query, which then passes its result to the outermost query.

* **Example:** Find customers who placed orders for products in the 'Electronics' category.
```sql
SELECT CustomerName FROM Customers
WHERE CustomerID IN (
    SELECT CustomerID FROM Orders WHERE OrderID IN (
        SELECT OrderID FROM OrderDetails WHERE Category = 'Electronics' -- Innermost
    )
);

```




### **2. Using Subqueries in Different Clauses**

A single statement can have one subquery in the `SELECT` list and another in the `WHERE` clause.

* **Example:**
```sql
SELECT Name, 
       (SELECT MAX(Salary) FROM Employees) AS Max_Comp_Salary -- Subquery in SELECT
FROM Employees
WHERE DeptID IN (SELECT DeptID FROM Departments WHERE Location = 'NY'); -- Subquery in WHERE

```




### **3. Performance Considerations**

While using multiple subqueries is powerful, it can impact performance:

* **Execution Plan:** Each independent subquery adds to the processing time.
* **Correlated Subqueries:** If multiple subqueries are "correlated" (reference the outer query), the database has to run them for every single row in the main table, which can be very slow.
* **Alternative:** Often, multiple subqueries can be rewritten as **JOINS** or **Common Table Expressions (CTEs)** for better speed.


### **4. Summary Table**

| Subquery Arrangement | Logic | Best Used For... |
| --- | --- | --- |
| **Parallel (Same Level)** | Query A AND Query B | Filtering by multiple unrelated conditions. |
| **Nested** | Query A inside Query B | Filtering by a chain of related dependencies. |
| **Mixed Clauses** | SELECT query + WHERE query | Generating reports with "Grand Totals" and specific filters. |


### **5. Key Rules**

1. **Enclose each** subquery in its own set of parentheses.
2. **Naming:** If you use a subquery in the `FROM` clause, you **must** give it an alias.
3. **Return Types:** Ensure the inner query returns the correct number of values (single vs. multiple rows) expected by the outer query's operator (`=` vs. `IN`).



