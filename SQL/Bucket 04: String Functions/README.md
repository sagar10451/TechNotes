

<h1 style="color:red">1. MySQL Build in Functions</h1>

MySQL functions are pre-defined tools used to manipulate data. They are generally categorized into **String**, **Numeric**, and **Date** functions.

### **1. String Functions**

Used to modify text data.

* **`CONCAT(a, b)`**: Joins two or more strings.
* `CONCAT('Hello', 'World')`  'HelloWorld'


* **`UPPER()` / `LOWER()**`: Changes text case.
* **`LENGTH()`**: Returns the number of characters.
* **`SUBSTRING(str, start, len)`**: Extracts a specific part of a string.
* **`TRIM()`**: Removes leading/trailing spaces.

### **2. Numeric Functions**

Used for mathematical operations.

* **`ABS(x)`**: Returns the absolute (positive) value.
* **`ROUND(x, d)`**: Rounds to  decimal places.
* **`CEIL(x)`**: Rounds **up** to the nearest integer.
* **`FLOOR(x)`**: Rounds **down** to the nearest integer.
* **`SQRT(x)`**: Returns the square root.

### **3. Date & Time Functions**

Crucial for handling timestamps.

* **`CURDATE()`**: Returns current date (`YYYY-MM-DD`).
* **`NOW()`**: Returns current date and time.
* **`DATE_FORMAT(date, format)`**: Formats date for display.
* `DATE_FORMAT(NOW(), '%Y')`  '2026'


* **`DATEDIFF(d1, d2)`**: Returns number of days between two dates.


### **4. Advanced/System Functions**

* **`COALESCE(list)`**: Returns the first **non-null** value in a list. Great for handling missing data.
* **`IFNULL(val, default)`**: Returns a default value if the input is `NULL`.
* **`USER()`**: Returns the current database user.
* **`DATABASE()`**: Returns the name of the current database.


---
<h1 style="color:red">2. upper() MySQL string functions</h1>

### **`UPPER()` Function**

The `UPPER()` function (also known as `UCASE()`) converts a string to **all uppercase** letters.


### **1. Syntax**

```sql
SELECT UPPER(column_name) FROM table_name;

```

### **2. Practical Examples**

* **Direct String:**
`SELECT UPPER('sql is fun');`    `'SQL IS FUN'`
* **Table Column:**
`SELECT UPPER(CustomerName) FROM Customers;`

### **3. Key Characteristics**

* **Non-Alphas:** Numbers and special characters (like `!`, `#`, or `123`) are **not affected**.
* **NULLs:** If you pass `NULL` to the function, it returns `NULL`.
* **Case Insensitivity:** It is often used in a `WHERE` clause to perform **case-insensitive searches**.

**Example for Searching:**

```sql
SELECT * FROM Users 
WHERE UPPER(Email) = 'JOHN@EXAMPLE.COM';

```

*(This ensures you find the user regardless of whether they typed "John", "JOHN", or "john").*


---
<h1 style="color:red">3. lower() MySQL String function</h1>

### **`LOWER()` Function**

The `LOWER()` function (also known as `LCASE()`) converts a string to **all lowercase** letters.


### **1. Syntax**

```sql
SELECT LOWER(column_name) FROM table_name;

```

### **2. Practical Examples**

* **Direct String:** `SELECT LOWER('SQL IS FUN');`  `'sql is fun'`
* **Table Column:**
`SELECT LOWER(ProductName) AS lower_name FROM Products;`

### **3. Key Characteristics**

* **Standardization:** Frequently used to "clean" data or normalize it before comparing values.
* **Non-Alphas:** It ignores numbers and symbols (e.g., `'User123!'` becomes `'user123!'`).
* **NULL Handling:** Returns `NULL` if the input is `NULL`.


### **4. Real-World Use Case**

Use it in the `WHERE` clause to ensure you catch results regardless of how they were entered in the database:

```sql
SELECT * FROM Employees 
WHERE LOWER(LastName) = 'smith';

```

*(This will return 'Smith', 'SMITH', or 'sMiTh').*


---

<h1 style="color:red">4. Length() MySQL String Function</h1>

### **`LENGTH()` Function**

The `LENGTH()` function returns the length of a string measured in **bytes**.


### **1. Syntax**

```sql
SELECT LENGTH(column_name) FROM table_name;

```

### **2. Practical Examples**

* **Direct String:** `SELECT LENGTH('SQL');`  `3`
* **With Spaces:** `SELECT LENGTH('SQL Pro');`  `7` (Spaces are counted).
* **In WHERE Clause:** Find usernames that are too short:
```sql
SELECT UserName FROM Users WHERE LENGTH(UserName) < 5;

```




### **3. Key Distinctions**

* **`LENGTH()` vs. `CHAR_LENGTH()`:** * `LENGTH()` measures **bytes**.
* `CHAR_LENGTH()` measures **characters**.
* *Note:* For standard English letters, they are the same. For multi-byte characters (like Emojis or certain Asian characters), `LENGTH()` will return a higher number.


* **NULLs:** If the input is `NULL`, the result is `NULL`.


### **4. Usage Tip**

It is often used with `ORDER BY` to sort results by their size:
`SELECT City FROM Customers ORDER BY LENGTH(City) DESC;`
*(Shows the longest city names first).*


---


<h1 style="color:red">5. instr() MySQL String Function</h1>

### **`INSTR()` Function**

The `INSTR()` function returns the **position** (index) of the first occurrence of a substring within a string.


### **1. Syntax**

```sql
SELECT INSTR(string, substring);

```

* **`string`**: The main text to search within.
* **`substring`**: The text you are looking for.

### **2. Practical Examples**

* **Finding a letter:** `SELECT INSTR('Hello', 'e');`  **2**
* **Finding a word:** `SELECT INSTR('SQL is fun', 'fun');`  **8**
* **Not found:** `SELECT INSTR('SQL', 'z');`  **0** (If the substring isn't there, it returns zero).


### **3. Key Characteristics**

* **1-Based Indexing:** Unlike many programming languages (which start at 0), SQL starts counting at **1**.
* **Case Insensitivity:** By default, `INSTR()` is case-insensitive in MySQL (e.g., searching for 'a' will find 'A').
* **First Occurrence:** It only returns the position of the **first** match it finds.


### **4. Usage Tip**

It is often used in the `WHERE` clause as an alternative to `LIKE`.

* `SELECT * FROM Emails WHERE INSTR(Email, '@') > 0;`
*(Checks if an email contains the '@' symbol).*


---

<h1 style="color:red">6. substr() MySQL String Function</h1>

### **`SUBSTR()` Function**

The `SUBSTR()` function (also known as `SUBSTRING()`) extracts a specific portion of a string based on a starting position and a defined length.


### **1. Syntax**

```sql
SELECT SUBSTR(string, start, length);

```

* **`string`**: The original text.
* **`start`**: The position to begin extraction (**1** is the first character).
* **`length`**: (Optional) The number of characters to extract. If omitted, it returns everything from the start position to the end.

### **2. Practical Examples**

* **Basic Extraction:** `SELECT SUBSTR('MySQL is Great', 1, 5);`  `'MySQL'`
* **Extract to End:** `SELECT SUBSTR('MySQL is Great', 10);`  `'Great'`
* **Negative Start:** `SELECT SUBSTR('Hello World', -5);`  `'World'`
*(A negative value starts counting from the **end** of the string).*


### **3. Key Characteristics**

* **1-Based Indexing:** Just like `INSTR()`, the first letter is at position 1.
* **Versatility:** Useful for cleaning data (e.g., extracting the area code from a phone number or the domain from an email).


### **4. Usage Tip**

You can combine `SUBSTR()` with `INSTR()` for dynamic extraction. For example, to get everything before the `@` in an email:

```sql
SELECT SUBSTR(Email, 1, INSTR(Email, '@') - 1) FROM Users;

```


---
<h1 style="color:red">7. concat() MySQL String Function</h1>

### **`CONCAT()` Function**

The `CONCAT()` function is used to join two or more strings together into a single string.


### **1. Syntax**

```sql
SELECT CONCAT(string1, string2, ..., stringN);

```

* You can join any number of strings (from two to hundreds).
* Arguments can be column names, literal strings, or numbers.

### **2. Practical Examples**

* **Combining Names:** `SELECT CONCAT(FirstName, ' ', LastName) AS FullName FROM Employees;`
* **Adding Static Text:** `SELECT CONCAT('Price: $', Price) FROM Products;`
* **Multiple Strings:**
`SELECT CONCAT('User: ', Username, ' (ID: ', UserID, ')') FROM Users;`


### **3. Key Characteristics**

* **NULL Handling:** If **any** of the strings being concatenated is `NULL`, the entire result becomes `NULL`.
* *Example:* `CONCAT('Hello', NULL)`  `NULL`.


* **Automatic Type Conversion:** If you include a number, MySQL automatically converts it to a string.
* **Alternative (`CONCAT_WS`):** If you want to use a separator (like a comma) between many items, use `CONCAT_WS()` (Concat With Separator).
* `SELECT CONCAT_WS('-', '2026', '02', '16');`  `'2026-02-16'`




### **4. Common Use Case**

It is frequently used to format output for reports, such as creating full addresses or descriptive labels directly in the SQL result.


---
<h1 style="color:red">8. trim() MySQL String Function</h1>

### **`TRIM()` Function**

The `TRIM()` function is used to remove leading and trailing spaces (or other specified characters) from a string.


### **1. Basic Syntax**

By default, it removes extra spaces from both sides of a string.

```sql
SELECT TRIM('  SQL is fun  '); 
-- Result: 'SQL is fun'

```


### **2. Advanced Syntax**

You can specify which side to trim from and which character to remove.

```sql
SELECT TRIM([LEADING | TRAILING | BOTH] [remstr] FROM str);

```

* **`LEADING`**: Removes characters from the start only.
* **`TRAILING`**: Removes characters from the end only.
* **`BOTH`**: Removes characters from both ends (default).
* **`remstr`**: The specific character you want to remove (if not space).

### **3. Practical Examples**

* **Removing specific characters:**
`SELECT TRIM(BOTH '#' FROM '###Hello###');`  `'Hello'`
* **Removing leading zeros:**
`SELECT TRIM(LEADING '0' FROM '000123');`  `'123'`
* **Cleaning user input:**
`SELECT * FROM Users WHERE TRIM(Username) = 'JohnDoe';`


### **4. Related Functions**

If you only need to clean one side, MySQL provides two shorthand functions:

* **`LTRIM(str)`**: Removes spaces from the **Left** side only.
* **`RTRIM(str)`**: Removes spaces from the **Right** side only.


### **5. Key Characteristics**

* **Internal Spaces:** `TRIM()` does **not** remove spaces in the middle of a string (e.g., `'A  B'` stays `'A  B'`).
* **NULLs:** Returns `NULL` if the input is `NULL`.



---


