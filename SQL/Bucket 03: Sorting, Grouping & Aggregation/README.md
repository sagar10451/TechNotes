

<h1 style="color:red">1. Order By Clause(ASC, DESC)</h1>

The **`ORDER BY`** clause is used to sort the result set in either ascending or descending order.

### **1. Basic Syntax**

It is placed after the `WHERE` clause (if any) and before the `LIMIT` clause.

```sql
SELECT Column1, Column2 
FROM TableName
ORDER BY Column1 [ASC | DESC];

```


### **2. ASC vs. DESC**

* **`ASC` (Default):** Sorts data from smallest to largest (A to Z, 1 to 10). You don't *have* to type it; SQL assumes it.
* **`DESC`:** Sorts data from largest to smallest (Z to A, 10 to 1).

**Examples:**

* `ORDER BY Price ASC;` → Cheapest first.
* `ORDER BY Price DESC;` → Most expensive first.



### **3. Sorting by Multiple Columns**

You can sort by more than one column. SQL will sort by the first column, and for any rows where that value is the same, it will sort by the second column.

**Example:**

```sql
SELECT * FROM Employees
ORDER BY Department ASC, Salary DESC;

```

*(Groups employees by department alphabetically, then shows the highest earners within each department first).*


### **4. Sorting by Column Position**

Instead of the column name, you can use its numeric position in the `SELECT` list.

* `SELECT Name, Age FROM Students ORDER BY 2;` (Sorts by Age).


### **5. Handling NULLs**

* In **MySQL/SQL Server**, `NULL` values appear **first** in `ASC` order.
* In **PostgreSQL/Oracle**, `NULL` values appear **last** in `ASC` order.


---
<h1 style="color:red">2. avg(), max(), min(), count(), sum()</h1>

These are called **Aggregate Functions**. They perform a calculation on a set of values and return a **single value**.


### **1. The "Big 5" Functions**

| Function | Description | Example |
| --- | --- | --- |
| **`COUNT()`** | Returns the number of rows | `SELECT COUNT(*) FROM Orders;` |
| **`SUM()`** | Returns the total sum of a numeric column | `SELECT SUM(Salary) FROM Employees;` |
| **`AVG()`** | Returns the average value | `SELECT AVG(Price) FROM Products;` |
| **`MIN()`** | Returns the smallest value | `SELECT MIN(Stock) FROM Inventory;` |
| **`MAX()`** | Returns the largest value | `SELECT MAX(Score) FROM Tests;` |


### **2. Important Rules**

* **NULL Values:** * `SUM`, `AVG`, `MIN`, and `MAX` **ignore** NULL values.
* `COUNT(*)` counts every row (including NULLs), but `COUNT(ColumnName)` ignores NULLs.


* **Data Types:** * `SUM` and `AVG` only work on **numeric** columns.
* `MIN`, `MAX`, and `COUNT` work on numbers, strings, and dates.


* **Aliasing:** Since these functions create a "new" column in the result, it’s best practice to use an alias for clarity:
* `SELECT AVG(Price) AS AveragePrice FROM Products;`




### **3. Using with DISTINCT**

You can combine these to calculate unique values only:

* `SELECT COUNT(DISTINCT Country) FROM Customers;`
*(Counts how many different countries are in the table).*



---
<h1 style="color:red">3. Group by clause</h1>

The **`GROUP BY`** clause groups rows that have the same values in specified columns into "summary" rows. It is almost always used with **Aggregate Functions** (`COUNT`, `SUM`, `AVG`, etc.).

### **1. Basic Syntax**

The `GROUP BY` statement comes after the `WHERE` clause and before `ORDER BY`.

```sql
SELECT Column, AggregateFunction(Column)
FROM TableName
WHERE Condition
GROUP BY Column;

```


### **2. How It Works**

It collapses multiple rows into a single row for each group.

* **Example:** To find how many customers are in each country:
```sql
SELECT Country, COUNT(CustomerID)
FROM Customers
GROUP BY Country;

```




### **3. Key Rules**

* **The "Select" Rule:** Every column in the `SELECT` list that is **not** part of an aggregate function **must** be included in the `GROUP BY` clause.
* **Filtering Groups:** You cannot use `WHERE` to filter aggregate results (like "groups with more than 5 items"). For that, you must use the **`HAVING`** clause.
* **NULLs:** If a column contains `NULL` values, `GROUP BY` will collect them into a single `NULL` group.


### **4. Grouping by Multiple Columns**

You can group by more than one column to get more specific summaries.

* **Example:** `GROUP BY Country, City;`
*(This shows totals for each city within each country).*




---

<h1 style="color:red">4. Having Clause</h1>

The **`HAVING`** clause is used to filter the results of a **`GROUP BY`** query. It works exactly like a `WHERE` clause, but for **groups** rather than individual rows.

### **1. Why use `HAVING`?**

The `WHERE` keyword cannot be used with aggregate functions (like `SUM` or `COUNT`).

* **Wrong:** `WHERE COUNT(OrderID) > 5` (SQL will throw an error).
* **Right:** `HAVING COUNT(OrderID) > 5`.


### **2. Basic Syntax**

It always follows the `GROUP BY` clause.

```sql
SELECT Column, AggregateFunction(Column)
FROM TableName
GROUP BY Column
HAVING Condition;

```


### **3. Key Difference: WHERE vs. HAVING**

| Feature | `WHERE` | `HAVING` |
| --- | --- | --- |
| **Applied to** | Individual rows | Groups (Summary rows) |
| **Placement** | Before `GROUP BY` | After `GROUP BY` |
| **Aggregates** | Cannot use `SUM`, `AVG`, etc. | **Can** use `SUM`, `AVG`, etc. |


### **4. Example in Action**

To find countries that have more than 10 customers:

```sql
SELECT Country, COUNT(CustomerID)
FROM Customers
GROUP BY Country
HAVING COUNT(CustomerID) > 10;

```


### **5. Can you use both?**

Yes. Use `WHERE` to filter rows **before** they are grouped, and `HAVING` to filter the groups **after** the calculation.

* **Example:** Find departments where the total salary is > 100k, but only include "Full-time" employees.
* `WHERE Status = 'Full-time'`
* `GROUP BY Department`
* `HAVING SUM(Salary) > 100000`




---


<h1 style="color:red">5. Sequence of using where, group by, having and order by clause</h1>

When writing a single SQL query, the clauses must follow a **strict logical order**. If you mix them up, the database will return a syntax error.

### **1. The Mandatory Sequence**

1. **`SELECT`** — Choose columns.
2. **`FROM`** — Choose table.
3. **`WHERE`** — Filter raw rows (cannot use aggregates).
4. **`GROUP BY`** — Categorize the filtered rows.
5. **`HAVING`** — Filter the categories/groups (can use aggregates).
6. **`ORDER BY`** — Sort the final results.
7. **`LIMIT`** — Restrict the number of rows returned.


### **2. Practical Example**

Find countries with more than 3 customers, but only count customers with an ID greater than 2, and sort them:

```sql
SELECT Country, COUNT(CustomerID)
FROM Customers
WHERE CustomerID > 2
GROUP BY Country
HAVING COUNT(CustomerID) > 3
ORDER BY COUNT(CustomerID) DESC;

```


### **3. Why this order? (Conceptual Logic)**

Think of it like a funnel:

* **WHERE** cuts out the noise first.
* **GROUP BY** buckets the remaining data.
* **HAVING** throws away buckets that don't meet your totals.
* **ORDER BY** just cleans up the presentation at the very end.


### **4. Memory Hack**

Use the acronym: **S**ome **F**ish **W**iggle **G**ently **H**iding **O**ut **L**ate.
(**S**elect, **F**rom, **W**here, **G**roup By, **H**aving, **O**rder By, **L**imit).

---

<h1 style="color:red">6. Arithmetic Operators</h1>

Arithmetic operators are used to perform mathematical calculations on numeric data within your SQL queries.

### **1. Common Operators**

| Operator | Description | Example |
| --- | --- | --- |
| **`+`** | Addition | `Price + Tax` |
| **`-`** | Subtraction | `Price - Discount` |
| **`*`** | Multiplication | `Price * Quantity` |
| **`/`** | Division | `Total / 2` |
| **`%`** or `MOD` | Modulo (Remainder) | `10 % 3` (Result: 1) |


### **2. Where to use them?**

You can use these operators in both the **`SELECT`** and **`WHERE`** clauses.

* **In SELECT (Calculated Columns):**
```sql
SELECT ProductName, Price * 1.10 AS PriceWithTax
FROM Products;

```


* **In WHERE (Filtering):**
```sql
SELECT * FROM Employees
WHERE (Salary / 12) > 5000;

```




### **3. Order of Operations (PEMDAS)**

SQL follows standard math rules: Multiplication and Division are handled before Addition and Subtraction. Use **Parentheses `()**` to control the order.

* `Price + Shipping * 0.1` (Calculates 10% of shipping first).
* `(Price + Shipping) * 0.1` (Calculates 10% of the total sum).


### **4. Technical Nuances**

* **NULLs:** Any arithmetic operation performed on a `NULL` value results in `NULL`.
* `100 + NULL = NULL`.


* **Division by Zero:** Most RDBMS will throw an error or return `NULL` if you try to divide by zero.


---



