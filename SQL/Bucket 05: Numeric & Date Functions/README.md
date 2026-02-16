

<h1 style="color:red">1. abs() MySQL Numeric Function</h1>

### **`ABS()` Function**

The `ABS()` function returns the **absolute (positive) value** of a number. It effectively removes the negative sign if one exists.

### **1. Syntax**

```sql
SELECT ABS(numeric_value);

```

### **2. Practical Examples**

* **Negative Number:** `SELECT ABS(-25);`  **25**
* **Positive Number:** `SELECT ABS(25);`  **25**
* **Zero:** `SELECT ABS(0);`  **0**
* **Calculation:** `SELECT ABS(10 - 50);`  **40**


### **3. Common Use Cases**

* **Calculating Differences:** Finding the distance between two values where you don't know which one is larger (e.g., `ABS(Stock_Level - Reorder_Point)`).
* **Financial Reports:** Used to display losses or debts as positive integers for specific formatting requirements.
* **Geographical Data:** Calculating the absolute distance between two coordinates.


### **4. Key Characteristics**

* **Data Types:** Works on all numeric types (integers, decimals, floats).
* **NULLs:** Like most math functions, `ABS(NULL)` returns `NULL`.



---
<h1 style="color:red">2. mod() MySQL Numeric Function</h1>

### **`MOD()` Function**

The `MOD()` function returns the **remainder** of one number divided by another. It is the mathematical equivalent of the `%` operator.


### **1. Syntax**

```sql
SELECT MOD(n, m);
-- or
SELECT n % m;

```

* **`n`**: The dividend (the number to be divided).
* **`m`**: The divisor (the number to divide by).

### **2. Practical Examples**

* **Basic Remainder:** `SELECT MOD(10, 3);`  **1** (Because , leaving  left over).
* **Exact Division:** `SELECT MOD(20, 5);`  **0** (Because 20 is perfectly divisible by 5).
* **Decimal Values:** `SELECT MOD(10.5, 3);`  **1.5**


### **3. Common Use Cases**

* **Odd or Even Check:** * `WHERE MOD(ID, 2) = 0` (Finds **Even** IDs).
* `WHERE MOD(ID, 2) <> 0` (Finds **Odd** IDs).


* **Batching/Scheduling:** To pick every Nth row from a dataset (e.g., `MOD(RowNumber, 10) = 0` to get every 10th record).
* **Time Conversions:** Converting total seconds into minutes and remaining seconds.


### **4. Key Characteristics**

* **Divisor Zero:** If the divisor (`m`) is **0**, MySQL returns `NULL` because division by zero is undefined.
* **Negative Numbers:** The result takes the sign of the **dividend** (`n`).
* `MOD(-10, 3)`  **-1**
* `MOD(10, -3)`  **1**




---
<h1 style="color:red">3. greatest() and least() MySQL Numeric Functions</h1>

### **`GREATEST()` and `LEAST()` Functions**

Unlike `MAX()` and `MIN()`, which look at values across **multiple rows**, `GREATEST()` and `LEAST()` compare values across **multiple columns or expressions** within the same row.


### **1. The GREATEST() Function**

Returns the largest value from the list of arguments provided.

* **Syntax:** `SELECT GREATEST(value1, value2, ...);`
* **Example:** `SELECT GREATEST(10, 20, 5, 30);`  **30**
* **Use Case:** Comparing different price points for a single product (e.g., MSRP vs. Retail Price).
* `SELECT ProductName, GREATEST(RegularPrice, SalePrice) FROM Products;`



### **2. The LEAST() Function**

Returns the smallest value from the list of arguments provided.

* **Syntax:** `SELECT LEAST(value1, value2, ...);`
* **Example:** `SELECT LEAST(1.5, 0.5, 2.0);`  **0.5**
* **Use Case:** Finding the lowest cost among various shipping options for one order.


### **3. Key Differences: vs. MAX/MIN**

| Function | Scope | Comparison Direction |
| --- | --- | --- |
| **`MAX()` / `MIN()**` | Aggregate (Vertical) | Compares a single column across many rows. |
| **`GREATEST()` / `LEAST()**` | Row-level (Horizontal) | Compares multiple columns/values in one row. |


### **4. Key Characteristics**

* **Data Types:** They work with numbers, strings, and dates.
* `LEAST('Apple', 'Banana', 'Cherry')`  **'Apple'** (alphabetical).


* **The NULL Trap:** If **any** value in the list is `NULL`, both functions will return `NULL`.
* *Tip:* Use `COALESCE()` or `IFNULL()` if your data might have missing values.


* **Mixed Types:** If you mix integers and strings, MySQL converts them to a common type (usually a double or string) to compare.


---

<h1 style="color:red">4. truncate() MySQL Numeric Function</h1>

### **`TRUNCATE()` Function**

The `TRUNCATE()` function is used to shorten a number to a specified number of decimal places **without rounding**.

Unlike `ROUND()`, which looks at the next digit to decide whether to go up or down, `TRUNCATE()` simply "cuts off" the unwanted digits.


### **1. Syntax**

```sql
SELECT TRUNCATE(number, decimals);

```

* **`number`**: The numeric value you want to truncate.
* **`decimals`**: The number of decimal places to keep.


### **2. Practical Examples**

* **Positive Decimals:**
`SELECT TRUNCATE(15.789, 2);`  **15.78** *(Note: Even though 15.789 is closer to 15.79, it just cuts the 9 off).*
* **Zero Decimals:**
`SELECT TRUNCATE(15.789, 0);`  **15**
* **Negative Decimals:**
`SELECT TRUNCATE(157.89, -1);`  **150** *(A negative value truncates digits to the left of the decimal point).*


### **3. TRUNCATE() vs. ROUND()**

| Value | Function | Result | Action |
| --- | --- | --- | --- |
| `1.99` | `ROUND(1.99, 1)` | **2.0** | Rounds up because .09 > .05 |
| `1.99` | `TRUNCATE(1.99, 1)` | **1.9** | Simply discards the .09 |


### **4. Key Characteristics**

* **Mandatory Arguments:** Unlike `ROUND()`, the second argument (decimals) is **required**. You cannot leave it blank.
* **No Rounding Logic:** It does not matter if the next digit is a 9 or a 1; it is always discarded.
* **NULL Handling:** If either the number or the decimal argument is `NULL`, the function returns `NULL`.

---


<h1 style="color:red">5. power() and sqrt() MySQL Numeric Functions</h1>

### **`POW()` and `SQRT()` Functions**

These functions are used for exponentiation and square root calculations, essential for advanced mathematical modeling and data analysis.


### **1. The `POW()` Function**

The `POW()` function (also known as `POWER()`) returns the value of a number raised to the power of another number.

* **Syntax:** `SELECT POW(base, exponent);`
* **Example (Square):** `SELECT POW(4, 2);`  **16** ()
* **Example (Cube):** `SELECT POW(2, 3);`  **8** ()
* **Example (Negative Exponent):** `SELECT POW(10, -1);`  **0.1** ()


### **2. The `SQRT()` Function**

The `SQRT()` function returns the square root of a non-negative number.

* **Syntax:** `SELECT SQRT(number);`
* **Example:** `SELECT SQRT(25);`  **5**
* **Example (Decimal):** `SELECT SQRT(2);`  **1.4142...**


### **3. Key Characteristics**

| Feature | `POW(x, y)` | `SQRT(x)` |
| --- | --- | --- |
| **Negative Inputs** | Allowed (if mathematically valid) | Returns **NULL** (Square root of negative is invalid in SQL) |
| **Result Type** | Usually a Float/Double | Usually a Float/Double |
| **Relationship** | `POW(x, 0.5)` is same as `SQRT(x)` | Specific case of `POW` |


### **4. Practical Use Case**

These are often used in geometric calculations, such as the **Pythagorean theorem** to find the distance between two points :

```sql
SELECT SQRT(POW(x_dist, 2) + POW(y_dist, 2)) AS distance FROM Coordinates;

```


---

<h1 style="color:red">6. current_data(), curdate(), current_time(), curtime(), sysdate()</h1>

These MySQL functions are used to retrieve the current date and time from the server. They are essential for tracking when records are created or updated.


### **1. Date Functions**

Both functions return the current date in `'YYYY-MM-DD'` format.

* **`CURDATE()`**: The standard way to get today's date.
* **`CURRENT_DATE()` / `CURRENT_DATE**`: Exactly the same as `CURDATE()`.
* **Example:** `SELECT CURDATE();`  `'2026-02-16'`

### **2. Time Functions**

Both functions return the current time in `'HH:MM:SS'` format.

* **`CURTIME()`**: The standard way to get the current time.
* **`CURRENT_TIME()` / `CURRENT_TIME**`: Exactly the same as `CURTIME()`.
* **Example:** `SELECT CURTIME();`  `'14:03:37'`

### **3. `SYSDATE()` vs `NOW()**`

While `SYSDATE()` returns the current date and time (`'YYYY-MM-DD HH:MM:SS'`), it has a specific behavior regarding execution timing.

* **`SYSDATE()`**: Returns the time at which the function **actually executes**.
* **`NOW()`**: Returns the time at which the **query started** executing.
* **Example:** If you have a very large query that takes 10 seconds to run, `SYSDATE()` might show a time 10 seconds later than `NOW()`.


### **Summary Table**

| Function | Returns | Format Example |
| --- | --- | --- |
| **`CURDATE()`** | Date Only | `'2026-02-16'` |
| **`CURTIME()`** | Time Only | `'14:03:37'` |
| **`SYSDATE()`** | Date + Time | `'2026-02-16 14:03:37'` |

### **4. Practical Use Case**

Calculating how many days have passed since an order was placed:

```sql
SELECT OrderID, DATEDIFF(CURDATE(), OrderDate) AS DaysSinceOrder
FROM Orders;

```

---
<h1 style="color:red">7. year(), month(), day(), etc</h1>

These functions are used to extract a specific part of a date or datetime value. They are extremely helpful for generating reports based on time periods (like monthly sales or yearly growth).


### **1. Core Extraction Functions**

| Function | What it extracts | Example (for '2026-02-16') |
| --- | --- | --- |
| **`YEAR()`** | The 4-digit year | `2026` |
| **`MONTH()`** | The month number (1–12) | `2` |
| **`DAY()`** | The day of the month (1–31) | `16` |
| **`HOUR()`** | The hour (0–23) | `14` |
| **`MINUTE()`** | The minute (0–59) | `3` |
| **`SECOND()`** | The second (0–59) | `46` |


### **2. Naming Functions**

Instead of numbers, these return the actual names of the days or months.

* **`MONTHNAME()`**: Returns the full name of the month.
* `SELECT MONTHNAME('2026-02-16');`  `'February'`


* **`DAYNAME()`**: Returns the name of the day of the week.
* `SELECT DAYNAME('2026-02-16');`  `'Monday'`




### **3. Day-Specific Functions**

* **`DAYOFWEEK()`**: Returns the index (1 = Sunday, 2 = Monday, ..., 7 = Saturday).
* **`DAYOFYEAR()`**: Returns the day number of the year (1–366).
* `SELECT DAYOFYEAR('2026-02-16');`  `47`


* **`LAST_DAY()`**: Returns the last date of the month for a given date.
* `SELECT LAST_DAY('2026-02-16');`  `'2026-02-28'`




### **4. Practical Use Cases**

* **Filtering by Year:**
```sql
SELECT * FROM Orders WHERE YEAR(OrderDate) = 2025;

```


* **Grouping by Month:**
```sql
SELECT MONTHNAME(OrderDate), SUM(TotalAmount)
FROM Orders
GROUP BY MONTH(OrderDate);

```





---


