
<h1 style="color:red">1. Where Clause</h1>

The `WHERE` clause is used to **filter records**. It ensures that only the rows that fulfill a specific condition are retrieved.

### **1. Basic Syntax**

It appears after the `FROM` clause but before `ORDER BY` or `LIMIT`.

```sql
SELECT Column1, Column2 
FROM TableName 
WHERE Condition;

```



### **2. Common Operators**

| Operator | Description | Example |
| --- | --- | --- |
| `=` | Equal to | `WHERE City = 'London'` |
| `!=` or `<>` | Not equal to | `WHERE Status != 'Active'` |
| `>`, `<` | Greater/Less than | `WHERE Price > 100` |
| `>=`, `<=` | Range comparison | `WHERE Age >= 18` |
| **`BETWEEN`** | Within a range | `WHERE Price BETWEEN 10 AND 50` |
| **`LIKE`** | Search for a pattern | `WHERE Name LIKE 'S%'` |
| **`IN`** | Multiple possible values | `WHERE Country IN ('USA', 'UK')` |



### **3. Logical Operators (Multiple Conditions)**

You can combine multiple filters using:

* **`AND`**: Displays a record if **all** conditions are true.
* **`OR`**: Displays a record if **any** condition is true.
* **`NOT`**: Displays a record if the condition is **not** true.

### **4. Important Note: Quotes**

* **Text/Strings:** Must be enclosed in single quotes (e.g., `'France'`).
* **Numbers:** Should **not** be in quotes (e.g., `100`).

---
<h1 style="color:red">2. Using Relational Operators in WHERE clause condition</h1>

Relational operators (also called comparison operators) allow you to compare a column value against a specific value or another column.

### **1. The Operators**

| Operator | Meaning | Example |
| --- | --- | --- |
| **`=`** | Equal to | `WHERE Age = 25` |
| **`>`** | Greater than | `WHERE Price > 100` |
| **`<`** | Less than | `WHERE Stock < 10` |
| **`>=`** | Greater than or equal to | `WHERE Rating >= 4.5` |
| **`<=`** | Less than or equal to | `WHERE Quantity <= 5` |
| **`<>`** or **`!=`** | Not equal to | `WHERE Status <> 'Retired'` |



### **2. Key Rules**

* **Data Types:** * **Numbers:** Use directly (`WHERE Salary > 50000`).
* **Strings:** Use single quotes (`WHERE City = 'Paris'`).
* **Dates:** Use single quotes in standard format (`WHERE JoinDate > '2023-01-01'`).


* **Nulls:** Relational operators **do not work** with `NULL`. To check for empty values, you must use `IS NULL` or `IS NOT NULL` instead of `= NULL`.



### **3. Logical Combination**

You can use these operators alongside **AND** / **OR** to create complex filters:

> `SELECT * FROM Products WHERE Price >= 50 AND Category = 'Electronics';`


---
<h1 style="color:red">3. Using Logical Operators(AND, OR, NOT)</h1>

Logical operators are used to combine multiple conditions in a `WHERE` clause to narrow down search results.

### **1. The AND Operator**

Displays a record if **all** conditions separated by `AND` are TRUE.

* **Usage:** Used to find rows that meet multiple criteria simultaneously.
* **Example:** `SELECT * FROM Products WHERE Category = 'Tech' AND Price < 100;`
*(Only cheap tech items appear).*

### **2. The OR Operator**

Displays a record if **any** of the conditions separated by `OR` is TRUE.

* **Usage:** Used when you have multiple acceptable options.
* **Example:** `SELECT * FROM Customers WHERE City = 'Berlin' OR City = 'Munich';`
*(Customers from either city appear).*

### **3. The NOT Operator**

Displays a record if the condition is **NOT** TRUE.

* **Usage:** Used to exclude specific data.
* **Example:** `SELECT * FROM Employees WHERE NOT Department = 'Sales';`
*(Everyone except sales staff appears).*



### **Operator Truth Table**

| Operator | Condition 1 | Condition 2 | Result |
| --- | --- | --- | --- |
| **AND** | TRUE | TRUE | **TRUE** |
| **AND** | TRUE | FALSE | **FALSE** |
| **OR** | TRUE | FALSE | **TRUE** |
| **OR** | FALSE | FALSE | **FALSE** |

### **4. Combining Operators (Operator Precedence)**

When using both `AND` and `OR`, SQL processes `AND` first. Use **parentheses `()**` to force the order of evaluation.

* **Good Practice:** `WHERE Country = 'USA' AND (City = 'NY' OR City = 'LA');`


---

<h1 style="color:red">4. Between Operator</h1>

The **`BETWEEN`** operator filters a result set within a specific range. It is shorthand for using `>=` and `<=` together.

### **1. Basic Syntax**

The values can be numbers, text, or dates.

```sql
SELECT ColumnNames
FROM TableName
WHERE Column BETWEEN Value1 AND Value2;

```



### **2. Key Characteristics**

* **Inclusive:** The range includes both the start and end values (e.g., `BETWEEN 10 AND 20` includes 10 and 20).
* **Alternative:** `Price BETWEEN 10 AND 20` is identical to `Price >= 10 AND Price <= 20`.
* **Text/Dates:** It works alphabetically for strings and chronologically for dates.
* *Example:* `WHERE Date BETWEEN '2023-01-01' AND '2023-12-31';`





### **3. The NOT BETWEEN Operator**

Used to find values **outside** of the range.

* **Example:** `SELECT * FROM Products WHERE Price NOT BETWEEN 50 AND 100;`
*(Returns items that are either cheaper than 50 or more expensive than 100).*



### **4. Best Practice**

Always place the **lower value first** and the **higher value second**.

* **Correct:** `BETWEEN 10 AND 50`
* **Incorrect:** `BETWEEN 50 AND 10` (This will usually return zero results).


---


<h1 style="color:red">5. Using Between Operator with text</h1>

When using `BETWEEN` with text, SQL filters results based on **alphabetical (lexicographical) order**.

### **1. Basic Syntax**

```sql
SELECT * FROM Customers
WHERE CustomerName BETWEEN 'A' AND 'L';

```



### **2. How It Works**

* **Alphabetical Range:** It selects all values starting with the letters in the range.
* **Inclusive/Exclusive Trap:** It is inclusive of the first value but can be tricky with the end value.
* `BETWEEN 'A' AND 'L'` includes everything starting with 'A' through 'K', and exactly the letter 'L'.
* **Crucial:** A name like 'London' will **not** be included because 'London' comes *after* the single letter 'L' alphabetically.



### **3. Best Practice for Text**

To ensure you capture everything through the letter 'L', use a value that covers the full range:

* `WHERE CustomerName BETWEEN 'A' AND 'Lz';` (This catches 'London').
* Alternatively, use relational operators: `WHERE CustomerName >= 'A' AND CustomerName < 'M';`



### **4. NOT BETWEEN with Text**

Used to find values that fall outside the specified alphabetical range.

* **Example:** `WHERE City NOT BETWEEN 'A' AND 'M';`
*(Returns cities starting with N through Z).*


---

<h1 style="color:red">6. In Operator</h1>

The **`IN`** operator allows you to specify multiple values in a `WHERE` clause. It is a shorthand for multiple `OR` conditions.

### **1. Basic Syntax**

Instead of writing `WHERE Country = 'USA' OR Country = 'UK' OR Country = 'France'`, you use:

```sql
SELECT * FROM Customers
WHERE Country IN ('USA', 'UK', 'France');

```



### **2. Key Benefits**

* **Readability:** Much cleaner than long strings of `OR` statements.
* **Maintenance:** Easier to add or remove values from the list.
* **Subqueries:** Can be used with another query to find values dynamically.
* *Example:* `WHERE ID IN (SELECT ID FROM Orders);`





### **3. The NOT IN Operator**

Used to exclude specific values from the result.

* **Example:** `SELECT * FROM Products WHERE Category NOT IN ('Toys', 'Books');`
*(Returns everything except Toys and Books).*



### **4. Technical Nuance: NULLs**

* If the list inside the `IN` operator contains a `NULL`, it will not match `NULL` values in the table.
* If using **`NOT IN`** and the list contains a `NULL`, the entire query will return **zero results**. Always handle `NULL` values separately using `IS NOT NULL`.



---

<h1 style="color:red">7. Like Operator and Wildcard Characters</h1>

The **`LIKE`** operator is used in a `WHERE` clause to search for a specific pattern in a column.

### **1. The Two Wildcards**

SQL uses two symbols to define patterns:

* **`%` (Percent Sign):** Represents zero, one, or multiple characters.
* **`_` (Underscore):** Represents a **single** character.



### **2. Common Pattern Examples**

| Pattern | Meaning | Example Match |
| --- | --- | --- |
| `'a%'` | Starts with "a" | Apple, Alphabet |
| `'%a'` | Ends with "a" | Pizza, Data |
| `'%or%'` | Contains "or" anywhere | Order, World, Fork |
| `'_r%'` | "r" in the 2nd position | Oracle, Iron |
| `'a_%_%'` | Starts with "a" and at least 3 chars long | Adult, Array |
| `'a%o'` | Starts with "a" and ends with "o" | Audio, Alibi |



### **3. Syntax**

```sql
SELECT * FROM Customers
WHERE City LIKE 'Lon%';

```

### **4. The NOT LIKE Operator**

Used to find records that **do not** match the pattern.

* `WHERE Name NOT LIKE 'S%';` (Finds everyone whose name doesn't start with S).



### **5. Case Sensitivity**

* **MySQL/SQL Server:** Usually case-insensitive (`'a%'` finds 'Apple' and 'apple').
* **PostgreSQL:** `LIKE` is case-sensitive. Use **`ILIKE`** for case-insensitive searching.


---

<h1 style="color:red">8. Using wildcards as normal characters</h1>


When you need to search for an actual **`%`** or **`_`** in your data (instead of using them as wildcards), you must use an **Escape Character**.

### **1. The Problem**

If you run `WHERE Discount LIKE '10%'`, SQL thinks you want anything starting with "10". It won't look specifically for the percent sign.

### **2. The Solution: `ESCAPE` Clause**

You pick a character (usually `!`, `\`, or `/`) to act as a "shield." Any wildcard immediately following that character is treated as normal text.

**Syntax:**

```sql
SELECT * FROM Coupons
WHERE Discount LIKE '10!%' ESCAPE '!';

```

* The `!` tells SQL: "The very next character is just a symbol, not a wildcard."
* Result: Matches exactly **'10%'**.



### **3. Common Examples**

| Goal | Pattern | Explanation |
| --- | --- | --- |
| Find **"50%"** | `LIKE '50!%' ESCAPE '!'` | Escapes the `%` symbol. |
| Find **"User_1"** | `LIKE 'User!_1' ESCAPE '!'` | Escapes the `_` symbol. |
| Find **"A%B_C"** | `LIKE 'A!%B!_C' ESCAPE '!'` | Escapes both symbols. |



### **4. Database Differences**

* **MySQL/PostgreSQL:** Often use the backslash `\` as a default escape character, so you don't always have to define it with the `ESCAPE` clause.
* **Best Practice:** Always define your escape character explicitly (like the example above) to make your code work on any database system.

---


