
<h1 style="color:red">1. Integrity constraints</h1>

**Integrity Constraints** are a set of rules used to maintain the quality, accuracy, and reliability of data in a database. They ensure that data remains consistent during operations like inserting, updating, or deleting.


### **1. Types of Integrity Constraints**

#### **A. Domain Integrity**

Ensures that all data in a column falls within a specific range or format.

* **NOT NULL:** Prevents a column from having a `NULL` value.
* **CHECK:** Ensures that all values in a column satisfy a specific condition (e.g., `Age > 18`).
* **DEFAULT:** Provides a predefined value if no value is specified.

#### **B. Entity Integrity**

Ensures that each row in a table is uniquely identifiable and not null.

* **PRIMARY KEY:** A combination of `NOT NULL` and `UNIQUE`. It uniquely identifies each record in a table. Each table can have only one Primary Key.
* **UNIQUE:** Ensures that all values in a column are different, but it allows for one `NULL` value.

#### **C. Referential Integrity**

Maintains the relationship between tables.

* **FOREIGN KEY:** A column in one table that points to the Primary Key in another table. It prevents actions that would destroy links between tables and ensures that you cannot enter a value that doesn't exist in the parent table.


### **2. Why are they Important?**

* **Accuracy:** Prevents accidental entry of "garbage" data (e.g., a negative salary).
* **Consistency:** Ensures that if a record is deleted in a master table, related records in other tables are handled (via `ON DELETE CASCADE` or `SET NULL`).
* **Security:** Acts as a first line of defense against data corruption.


### **3. Summary Table**

| Constraint | Category | Function |
| --- | --- | --- |
| **NOT NULL** | Domain | Column cannot be empty. |
| **UNIQUE** | Entity | All values must be different. |
| **PRIMARY KEY** | Entity | Unique ID for a row (No Nulls). |
| **FOREIGN KEY** | Referential | Connects two tables. |
| **CHECK** | Domain | Values must meet a specific logical rule. |


### **4. Syntax Example**

```sql
CREATE TABLE Employees (
    ID INT PRIMARY KEY,               -- Entity Integrity
    Name VARCHAR(50) NOT NULL,        -- Domain Integrity
    Age INT CHECK (Age >= 18),        -- Domain Integrity
    DeptID INT,
    FOREIGN KEY (DeptID) REFERENCES Departments(ID) -- Referential Integrity
);

```


---
<h1 style="color:red">2. not null integrity constraints</h1>

### **NOT NULL Integrity Constraint**

The **`NOT NULL`** constraint is a rule applied to a specific column to ensure that it **cannot store a `NULL` value**. It forces the column to always contain data, making it a mandatory field.


### **1. Core Purpose**

* **Data Completeness:** It ensures that critical information (like a username, email, or ID) is never missing.
* **Logic Prevention:** It prevents errors in calculations and logic that occur when the database encounters an "unknown" (`NULL`) value.



### **2. How it Works**

* By default, all columns in a table can hold `NULL` values unless specified otherwise.
* When a column is defined as `NOT NULL`, any attempt to `INSERT` a new record or `UPDATE` an existing record without providing a value for that column will result in an **error**.



### **3. Syntax**

#### **A. At the time of Table Creation:**

```sql
CREATE TABLE Students (
    ID INT NOT NULL,
    Name VARCHAR(100) NOT NULL,
    Age INT
);

```

*In this example, `ID` and `Name` are mandatory, but `Age` can be left blank.*

#### **B. Adding to an Existing Table:**

To add a `NOT NULL` constraint to an existing column, you use the `ALTER TABLE` command:

```sql
ALTER TABLE Students
MODIFY Age INT NOT NULL;

```

> **Note:** This will fail if the column already contains any `NULL` values. You must fill those gaps first.



### **4. Key Characteristics**

* **Primary Keys:** Every `PRIMARY KEY` column is automatically `NOT NULL`. You do not need to specify both.
* **Interaction with DEFAULT:** Often used alongside the `DEFAULT` constraint. If a user doesn't provide a value, the `DEFAULT` fills it in, ensuring the `NOT NULL` rule is satisfied.
* **Empty Strings:** Be careful—an **empty string (`''`)** or a **zero (`0`)** is considered a value. A `NOT NULL` constraint will allow these, even though they might "look" empty to a human.



### **5. Summary Table**

| Aspect | Description |
| --- | --- |
| **Constraint Level** | Column Level |
| **Effect** | Rejects `NULL` values; accepts everything else. |
| **Primary Key** | Implicitly included. |
| **Check Command** | `DESCRIBE table_name;` (The 'Null' column will show 'NO'). |


---
<h1 style="color:red">3. unique integrity constraints</h1>

### **UNIQUE Integrity Constraint**

The **`UNIQUE`** constraint ensures that all values in a column are **different** from one another. It prevents duplicate entries in a specific field, ensuring that every piece of data in that column is a "one-of-a-kind" entry.



### **1. Core Purpose**

* **Uniqueness:** It is used for columns that are not the Primary Key but still require unique values, such as **Email Addresses**, **Phone Numbers**, or **Social Security Numbers**.
* **Data Integrity:** It prevents accidental duplication of records that should logically be unique.



### **2. UNIQUE vs. PRIMARY KEY**

While both enforce uniqueness, they have key differences:

| Feature | UNIQUE Constraint | PRIMARY KEY |
| --- | --- | --- |
| **Null Values** | Allows **one** `NULL` value (in most DBs). | Strictly **NOT NULL**. |
| **Quantity** | You can have multiple per table. | Only **one** per table. |
| **Purpose** | Prevents duplicate data. | Uniquely identifies a row. |



### **3. Syntax**

#### **A. During Table Creation**

You can define it at the column level or the table level.

```sql
CREATE TABLE Users (
    UserID INT PRIMARY KEY,
    Email VARCHAR(255) UNIQUE, -- Column level
    Username VARCHAR(50),
    UNIQUE (Username)          -- Table level
);

```

#### **B. Adding to an Existing Table**

```sql
ALTER TABLE Users
ADD CONSTRAINT UC_Email UNIQUE (Email);

```



### **4. Key Characteristics**

* **Index Creation:** When you define a `UNIQUE` constraint, the database automatically creates a **Unique Index** behind the scenes to speed up the process of checking for duplicates.
* **Composite Unique Constraint:** You can apply the constraint to a **combination** of columns. This means the individual columns can have duplicates, but the *pair* must be unique.
* *Example:* `UNIQUE (AreaCode, PhoneNumber)`


* **Handling Violations:** If you try to insert a value that already exists, the database will throw a "Duplicate entry" error and reject the operation.



### **5. Summary Table**

| Aspect | Description |
| --- | --- |
| **Effect** | Rejects identical values in the same column. |
| **NULL handling** | Allows a single NULL (representing an "unknown" unique value). |
| **Modification** | Can be dropped using `ALTER TABLE ... DROP INDEX`. |


---

<h1 style="color:red">4. Primary Key Integrity Constraint</h1>

### **PRIMARY KEY Integrity Constraint**

The **PRIMARY KEY** is a constraint that uniquely identifies each record in a database table. It is the most important constraint for ensuring **Entity Integrity**.


### **1. Core Characteristics**

A Primary Key must satisfy two strict rules:

1. **Unique:** No two rows can have the same Primary Key value.
2. **NOT NULL:** The column cannot contain `NULL` values. Every record must have a valid identifier.

> **Note:** A table can have **only one** Primary Key.


### **2. Why Use a Primary Key?**

* **Identification:** It allows you to target a specific row for updates or deletions without affecting others.
* **Relationships:** It acts as the "Target" for **Foreign Keys** in other tables, creating links between data.
* **Indexing:** The database automatically creates a **Clustered Index** on the Primary Key, making data retrieval extremely fast.


### **3. Syntax**

#### **A. At the time of Table Creation:**

```sql
CREATE TABLE Students (
    StudentID INT PRIMARY KEY, -- Column level
    Name VARCHAR(50) NOT NULL,
    Email VARCHAR(50)
);

```

#### **B. Composite Primary Key:**

If a single column isn't enough to uniquely identify a row, you can use a combination of columns.

```sql
CREATE TABLE Enrollment (
    StudentID INT,
    CourseID INT,
    EnrollmentDate DATE,
    PRIMARY KEY (StudentID, CourseID) -- Table level
);

```


### **4. Primary Key vs. Unique Key**

| Feature | PRIMARY KEY | UNIQUE KEY |
| --- | --- | --- |
| **Null Values** | Never allowed. | Allows one `NULL`. |
| **Limit** | Only one per table. | Multiple allowed per table. |
| **Purpose** | To identify the record. | To prevent duplicate data. |


### **5. Best Practices**

* **Keep it Simple:** Use a numeric ID (like `INT`) rather than long strings.
* **Avoid Changing it:** Primary keys should be "stable." Changing a PK value can break relationships in other tables.
* **Auto-Increment:** Use `AUTO_INCREMENT` (MySQL) or `IDENTITY` (SQL Server) to let the database generate unique IDs automatically.


---


<h1 style="color:red">5. Foreign Key Integrity Constraint</h1>

### **FOREIGN KEY Integrity Constraint**

A **FOREIGN KEY** is a column (or a group of columns) in one table that refers to the **PRIMARY KEY** in another table. It is the foundation of **Referential Integrity**, ensuring that the relationship between two tables remains consistent.


### **1. Core Purpose**

* **Linking Tables:** It connects a "Child" table (containing the foreign key) to a "Parent" table (containing the primary key).
* **Data Validation:** It ensures that you cannot add a record to the child table unless the corresponding value already exists in the parent table.
* **Preventing Orphaned Rows:** It prevents the deletion of a record in the parent table if that record is being referenced by rows in the child table.


### **2. Key Rules**

1. **Matching Types:** The data type of the Foreign Key must be exactly the same as the Primary Key it references.
2. **Nullability:** A Foreign Key *can* contain `NULL` values (unless defined as `NOT NULL`), indicating that there is no relationship for that specific row.
3. **Many-to-One:** Typically, many rows in the child table can reference a single row in the parent table.


### **3. Syntax**

#### **A. During Table Creation**

```sql
CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,
    OrderDate DATE,
    CustomerID INT,
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
);

```

#### **B. Adding to an Existing Table**

```sql
ALTER TABLE Orders
ADD CONSTRAINT FK_CustomerOrder
FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID);

```


### **4. Referential Actions (ON DELETE / ON UPDATE)**

You can define what happens to the child table when a row in the parent table is deleted or changed:

| Action | Result |
| --- | --- |
| **`CASCADE`** | If a parent row is deleted/updated, the child rows are automatically deleted/updated. |
| **`SET NULL`** | If a parent row is deleted, the foreign key in the child table is set to `NULL`. |
| **`RESTRICT`** | Prevents the deletion/update of the parent row if child records exist (Default). |
| **`NO ACTION`** | Similar to `RESTRICT`; it rejects the change if it violates integrity. |


### **5. Summary Table**

| Feature | Primary Key | Foreign Key |
| --- | --- | --- |
| **Location** | Parent Table | Child Table |
| **Uniqueness** | Must be Unique | Can have Duplicates |
| **Nulls** | Not Allowed | Allowed (usually) |
| **Purpose** | Identifies the record | Links to another record |


---

<h1 style="color:red">6. Check Integrity Constraint</h1>

### **CHECK Integrity Constraint**

The **`CHECK`** constraint is used to limit the value range that can be placed in a column. It ensures that all values in a row satisfy a specific logical condition (a Boolean expression).


### **1. Core Purpose**

* **Validation:** It acts as a gatekeeper, ensuring data meets specific business rules before it is saved (e.g., ensuring a price isn't negative).
* **Accuracy:** It maintains domain integrity by restricting data to valid, sensible entries.


### **2. How it Works**

* Whenever a row is **Inserted** or **Updated**, the database evaluates the `CHECK` condition.
* If the condition is **TRUE** or **UNKNOWN** (due to a `NULL`), the database allows the change.
* If the condition is **FALSE**, the database rejects the change and throws an error.


### **3. Syntax**

#### **A. Single Column Level**

Used when the rule applies to only one column.

```sql
CREATE TABLE Products (
    ID INT PRIMARY KEY,
    Price DECIMAL(10,2) CHECK (Price > 0)
);

```

#### **B. Table Level**

Used when the rule involves comparing **multiple columns**.

```sql
CREATE TABLE Employees (
    ID INT PRIMARY KEY,
    Min_Salary INT,
    Max_Salary INT,
    CONSTRAINT CHK_SalaryRange CHECK (Max_Salary >= Min_Salary)
);

```


### **4. Common Use Cases**

* **Numerical Ranges:** `Age >= 18` or `Salary > 0`.
* **List of Options:** `Gender IN ('Male', 'Female', 'Other')`.
* **Pattern Matching:** Ensuring a string starts with a specific character (using `LIKE`).
* **Date Comparisons:** Ensuring `EndDate > StartDate`.


### **5. Key Characteristics**

* **NULL Values:** By default, `CHECK` constraints treat `NULL` values as "Unknown" and allow them. If you want to block empty values, you must use `NOT NULL` along with `CHECK`.
* **MySQL Support:** Historically, older versions of MySQL (before 8.0.16) would parse the `CHECK` syntax but **ignore** it. In modern MySQL, it is fully enforced.
* **Dropping Constraints:** If a rule changes, you can remove the constraint:
```sql
ALTER TABLE Employees DROP CHECK CHK_SalaryRange;

```




### **Summary Table**

| Aspect | Description |
| --- | --- |
| **Logic** | Boolean Expression (True/False). |
| **Scope** | Can be one column or a comparison between columns. |
| **Result of Violation** | Database returns an error; data is not saved. |
| **Flexibility** | More flexible than `ENUM` because it allows complex logic. |


---
<h1 style="color:red">7. Default Integrity Constraint</h1>

### **DEFAULT Integrity Constraint**

The **`DEFAULT`** constraint is used to provide a **predefined value** for a column when no value is specified during an `INSERT` operation. It ensures that a column is never left unintentionally empty.


### **1. Core Purpose**

* **Automated Data Entry:** It simplifies input by filling in common values automatically (e.g., setting the current date or a default status).
* **Prevents NULLs:** When combined with `NOT NULL`, it ensures the column always contains data, even if the user forgets to provide it.


### **2. How it Works**

* If an `INSERT` statement omits the column, the database automatically applies the `DEFAULT` value.
* If the user explicitly provides a value (even a `NULL`), the `DEFAULT` is ignored.


### **3. Syntax**

#### **A. During Table Creation**

You can set defaults for strings, numbers, or system functions.

```sql
CREATE TABLE Orders (
    ID INT PRIMARY KEY,
    Status VARCHAR(20) DEFAULT 'Pending',
    OrderDate DATETIME DEFAULT CURRENT_TIMESTAMP,
    Quantity INT DEFAULT 1
);

```

#### **B. Adding to an Existing Table**

```sql
ALTER TABLE Orders
ALTER COLUMN Status SET DEFAULT 'Shipped';

```


### **4. Common Use Cases**

* **Status Flags:** Setting a new account to `Active` or a task to `Incomplete`.
* **Timestamps:** Automatically recording the exact time a record was created using `CURRENT_TIMESTAMP`.
* **Numeric Values:** Setting a default quantity to `1` or a discount to `0.0`.
* **Boolean/Logic:** Setting a `IsVerified` bit to `0` (False).


### **5. Key Characteristics**

* **Literal vs. Function:** Defaults can be constant values (e.g., `'Unknown'`) or system functions (e.g., `GETDATE()`).
* **Dropping a Default:** You can remove the default behavior without deleting the column:
```sql
ALTER TABLE Orders ALTER COLUMN Status DROP DEFAULT;

```


* **No Table Level:** Unlike `CHECK` or `UNIQUE`, the `DEFAULT` constraint is strictly a **column-level** property; it cannot compare data across multiple columns.


### **Summary Table**

| Aspect | Description |
| --- | --- |
| **Trigger** | Activated only when a column is omitted in an `INSERT` statement. |
| **Flexibility** | Accepts constants, expressions, or system functions. |
| **Interaction** | Works best with `NOT NULL` to ensure data consistency. |
| **Result** | No error is thrown; the "fallback" value is simply used. |



---
<h1 style="color:red">8. auto_increment</h1>

### **AUTO_INCREMENT Constraint**

The **`AUTO_INCREMENT`** attribute is used in MySQL to automatically generate a unique numeric value for a column whenever a new record is inserted. It is most commonly used on **Primary Key** columns.


### **1. Core Purpose**

* **Unique Identifiers:** It ensures every row gets a unique ID without requiring the user to manually track the last used number.
* **Identity Management:** It simplifies the process of creating "Surrogate Keys" (artificial IDs that have no business meaning but identify the row).


### **2. Rules for Usage**

1. **One Per Table:** A table can have only **one** `AUTO_INCREMENT` column.
2. **Indexing:** The column must be defined as a **Key** (usually the `PRIMARY KEY` or `UNIQUE` key).
3. **Data Type:** It can only be used on **Integer** data types (e.g., `INT`, `BIGINT`, `TINYINT`).


### **3. Basic Syntax**

```sql
CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY AUTO_INCREMENT,
    Name VARCHAR(100) NOT NULL
);

```

**How to Insert:**
When inserting data, you either omit the column or provide `NULL`/`0`. The database handles the rest:

```sql
INSERT INTO Employees (Name) VALUES ('Arjun'); -- ID 1 is assigned
INSERT INTO Employees (Name) VALUES ('Deepa'); -- ID 2 is assigned

```


### **4. Key Characteristics**

* **Starting Value:** By default, it starts at **1** and increments by **1**.
* **Manual Override:** You can manually insert a value into an `AUTO_INCREMENT` column. If you insert `100`, the next automatic value will be `101`.
* **Gaps in Sequences:** If a transaction fails or a row is deleted, the generated ID is "lost." This can result in gaps in the sequence (e.g., 1, 2, 4, 5).
* **Last Insert ID:** You can retrieve the most recently generated ID using the function `LAST_INSERT_ID()`.


### **5. Customizing the Sequence**

You can change the starting value or the increment step:

* **Change Start Value:**
```sql
ALTER TABLE Employees AUTO_INCREMENT = 1000;

```


* **Change Increment Step (Global):**
```sql
SET @@auto_increment_increment = 5;

```




### **Summary Table**

| Feature | Description |
| --- | --- |
| **Default Start** | 1 |
| **Default Step** | 1 |
| **Required Key** | Primary Key or Unique |
| **Allowed Types** | INT, SMALLINT, TINYINT, MEDIUMINT, BIGINT |


---


<h1 style="color:red">9. Single line and multi line comments</h1>

In SQL, comments are used to explain sections of code or to prevent the execution of specific statements during testing. Comments are ignored by the database engine.


### **1. Single-Line Comments**

Single-line comments are used for brief notes or to disable a single line of code. There are two common ways to write them:

* **Double Dash (`--`):** This is the SQL standard. Anything after the `--` on that line is ignored.
* *Note:* In many systems (like MySQL), the double dash must be followed by at least one space.
* **Example:** ```sql
SELECT * FROM Users; -- This retrieves all user data
```

```




* **Hash Sign (`#`):** Specific to MySQL. It does not require a trailing space.
* **Example:**
```sql
SELECT * FROM Products; # Fetch all products

```






### **2. Multi-Line Comments**

Multi-line comments (also called C-style comments) are used for long descriptions or to "comment out" large blocks of code across multiple lines.

* **Syntax:** Start with `/*` and end with `*/`.
* **Example:**
```sql
/* The following query retrieves 
   all active employees from the 
   Sales department.
*/
SELECT Name FROM Employees WHERE Dept = 'Sales';

```




### **3. Why Use Comments?**

* **Documentation:** Explaining why a complex `JOIN` or `SUBQUERY` was used.
* **Debugging:** Temporarily disabling a `WHERE` clause to see a larger dataset without deleting the code.
* **Maintenance:** Helping other developers (or your future self) understand the logic behind the script.



### **Summary Comparison**

| Type | Syntax | Usage |
| --- | --- | --- |
| **Single-Line** | `--` or `#` | Brief notes, end-of-line explanations. |
| **Multi-Line** | `/* ... */` | Detailed headers, disabling code blocks. |


---
<h1 style="color:red">10. AS Keyword</h1>

The **`AS`** keyword is used to create an **Alias**—a temporary name for a column or a table in the result set of a query. It is primarily used to make column headers more readable or to shorten long table names.



### **1. Column Aliases**

A column alias is used to rename a column in the output display. It is especially useful when dealing with calculated fields or aggregate functions.

* **Syntax:** `SELECT column_name AS alias_name FROM table_name;`
* **Example:** ```sql
SELECT SUM(Salary) AS Total_Department_Pay
FROM Employees;
```

```


* **Note:** If the alias contains spaces, you must enclose it in single or double quotes (e.g., `AS "Total Pay"`).



### **2. Table Aliases**

A table alias provides a shorthand name for a table. This is essential for **Joins** to avoid typing long table names repeatedly and to resolve ambiguity when two tables have columns with the same name.

* **Syntax:** `SELECT a.column1, b.column2 FROM TableA AS a JOIN TableB AS b ON a.id = b.id;`
* **Example:**
```sql
SELECT e.Name, d.DeptName
FROM Employees AS e
JOIN Departments AS d ON e.DeptID = d.ID;

```





### **3. Key Characteristics**

* **Temporary Nature:** The alias exists only for the duration of the query; it does not change the actual name in the database.
* **Optional Keyword:** In most SQL dialects (including MySQL, PostgreSQL, and SQL Server), the `AS` keyword is **optional**. You can simply put a space between the column name and the alias.
* *Example:* `SELECT Name Employee_Name FROM Users;`


* **Result Set Headers:** The alias name is what appears as the column header in your application or report.



### **4. Why Use the `AS` Keyword?**

* **Readability:** Gives meaningful names to results like `AVG(Price)` (e.g., `AS Average_Price`).
* **Conciseness:** Reduces the amount of code in complex queries with multiple joins.
* **Compatibility:** Allows you to satisfy specific column name requirements for front-end reporting tools.



### **Summary Table**

| Usage | Example | Purpose |
| --- | --- | --- |
| **Column Alias** | `SELECT DOB AS Birthday` | Renames the output column. |
| **Calculation Alias** | `SELECT (Qty * Price) AS Total` | Names a derived/calculated value. |
| **Table Alias** | `FROM Product_Catalog_2024 AS p` | Shortens table name for easier reference. |


---
<h1 style="color:red">11. IfNull() function</h1>

The **`IFNULL()`** function in MySQL is used to handle `NULL` values by replacing them with a specified default value. It allows you to provide a "fallback" so that your query results remain clean and usable.



### **1. Basic Syntax**

The function takes two arguments:

```sql
IFNULL(expression, alt_value)

```

* **`expression`**: The value or column you want to check for `NULL`.
* **`alt_value`**: The value to return if the expression is `NULL`.

**How it works:**

* If the expression is **NOT NULL**, it returns the expression itself.
* If the expression **IS NULL**, it returns the `alt_value`.



### **2. Practical Examples**

#### **A. Replacing NULLs in Reports**

If a customer doesn't have a phone number listed, you can display "N/A" instead of a blank space.

```sql
SELECT Name, IFNULL(PhoneNumber, 'N/A') AS Contact
FROM Customers;

```

#### **B. Preventing Math Errors**

`NULL` values in calculations often result in a `NULL` total. `IFNULL()` can treat a missing value as `0`.

```sql
SELECT ProductName, (Price + IFNULL(Tax, 0)) AS TotalPrice
FROM Products;

```



### **3. Key Characteristics**

* **Return Type:** It returns a value of the same data type as the first argument (or a compatible type).
* **Comparison:** Unlike the `COALESCE()` function (which can take multiple arguments), `IFNULL()` only accepts exactly **two** arguments.
* **Empty Strings vs. NULL:** `IFNULL()` treats an empty string (`''`) as a valid value, not as `NULL`. It will only trigger the replacement if the value is truly `NULL`.



### **4. IFNULL() vs. COALESCE()**

| Feature | `IFNULL()` | `COALESCE()` |
| --- | --- | --- |
| **Arguments** | Only 2 arguments allowed. | Multiple arguments (unlimited). |
| **Logic** | Returns 2nd if 1st is null. | Returns the **first non-null** value in the list. |
| **Standard** | Specific to MySQL. | Standard SQL (works in PostgreSQL, SQL Server, etc.). |



### **5. Summary Table**

| Input 1 | Input 2 | Result |
| --- | --- | --- |
| `NULL` | `'Default'` | `'Default'` |
| `'Hello'` | `'Default'` | `'Hello'` |
| `0` | `10` | `0` (0 is not NULL) |
| `''` | `'N/A'` | `''` (Empty string is not NULL) |


---

<h1 style="color:red">12. Case, When, Then and End Keywords</h1>

In SQL, the **`CASE`** statement is a powerful tool used to implement **if-then-else** logic within a query. It allows you to return specific values based on defined conditions.



### **1. Core Functionality**

* **Logic in Queries:** It evaluates conditions and returns a value as soon as a condition is met.
* **Default Handling:** If no conditions are met, it returns the value specified in the `ELSE` clause.
* **NULL Fallback:** If no `ELSE` part is defined and no conditions are met, it returns `NULL`.



### **2. Syntax Structure**

The statement must start with `CASE` and end with `END`.

```sql
SELECT column_name,
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ELSE resultN
END AS alias_name
FROM table_name;

```



### **3. Types of CASE Statements**

#### **A. Simple CASE**

Compares an expression to a set of simple values.

* **Example:**
```sql
SELECT Name,
CASE Rating
    WHEN 1 THEN 'Poor'
    WHEN 2 THEN 'Average'
    ELSE 'Excellent'
END AS Quality
FROM Products;

```



#### **B. Searched CASE**

Uses complex logic or ranges in the `WHEN` clause.

* **Example:**
```sql
SELECT Name, Salary,
CASE 
    WHEN Salary >= 100000 THEN 'High'
    WHEN Salary >= 50000 THEN 'Medium'
    ELSE 'Low'
END AS Salary_Bracket
FROM Employees;

```





### **4. Where to use CASE**

* **`SELECT` clause:** To create new categories or labels for data display.
* **`ORDER BY` clause:** To perform custom sorting (e.g., sorting 'High' priority before 'Low').
* **`GROUP BY` clause:** To group data into custom ranges for aggregation.



### **5. Summary Table**

| Keyword | Purpose |
| --- | --- |
| **`CASE`** | Starts the conditional block. |
| **`WHEN`** | Defines the condition to be checked. |
| **`THEN`** | Defines the value to return if the condition is TRUE. |
| **`ELSE`** | Defines the "fallback" value if no conditions match. |
| **`END`** | Closes the conditional block. |



### **Key Rules**

1. **Order Matters:** It returns the result for the **first** condition that is true. Subsequent conditions are ignored.
2. **Data Types:** All values in the `THEN` and `ELSE` branches must have compatible data types.
3. **Alias:** It is best practice to use `AS` after `END` to give the resulting column a readable name.


---


<h1 style="color:red">13. Delimiter</h1>

In SQL, a **Delimiter** is a character or string used to signal the end of an SQL statement. By default, most database tools (like MySQL) use the **semicolon (`;`)** as the delimiter.



### **1. Why Change the Delimiter?**

When writing **Stored Procedures**, **Triggers**, or **Functions**, you often need to include multiple SQL statements inside a single block (between `BEGIN` and `END`).

If you use the default `;` inside the block, the database engine will think the entire procedure has ended at the first semicolon it sees. Changing the delimiter allows you to "wrap" the entire block and send it to the server as a single unit.



### **2. Syntax for Changing Delimiter**

In MySQL, you use the `DELIMITER` command followed by the new symbol (usually `//` or `$$`).

```sql
DELIMITER //

CREATE PROCEDURE GetAllEmployees()
BEGIN
    SELECT * FROM Employees;
END //

DELIMITER ;

```



### **3. Key Steps in the Process**

1. **Switching:** Use `DELIMITER //` to tell the engine to ignore `;` for a moment.
2. **Writing:** Define your procedure, using `;` normally inside the body.
3. **Closing:** End the entire block with the new delimiter `//`.
4. **Resetting:** Always switch back to the default using `DELIMITER ;`.



### **4. Common Delimiter Symbols**

While you can technically use almost anything, the most common choices are:

* `//` (Double Forward Slash)
* `$$` (Double Dollar Sign)
* `//`



### **5. Summary Table**

| Aspect | Default Delimiter | Custom Delimiter |
| --- | --- | --- |
| **Character** | `;` | `//` or `$$` |
| **Purpose** | Ends single SQL statements. | Defines the boundary of complex blocks. |
| **Scope** | Standard queries (`SELECT`, `INSERT`). | Stored Procedures, Triggers, Functions. |



### **Key Rules**

* **No Space:** There is usually no space between the `DELIMITER` command and the new symbol in some environments, though `DELIMITER //` is standard.
* **Client Side:** The `DELIMITER` command is a **client-side** command (used in tools like MySQL Workbench or Command Line), not a part of the core SQL language.


---

<h1 style="color:red">14. Delimiter usage in stored Procedures</h1>

In MySQL, the **`DELIMITER`** command is essential when defining **Stored Procedures**. It allows you to temporarily redefine the character that signals the end of a command so that you can group multiple statements together.



### **1. The Problem: The Semicolon (`;`)**

By default, the database treats the semicolon as the end of a statement. A stored procedure contains multiple internal statements, each ending with a semicolon.

Without changing the delimiter, the database would stop reading at the very first semicolon it encounters inside the procedure's body, causing a **syntax error**.


### **2. The Solution: Temporary Redefinition**

You must change the delimiter to something unique (like `//` or `$$`) so the database treats the entire procedure—from `CREATE` to `END`—as one single block of code.

#### **Basic Workflow:**

1. **Change** the delimiter from `;` to `//`.
2. **Define** the procedure (using `;` freely inside).
3. **End** the procedure block with `//`.
4. **Reset** the delimiter back to `;`.


### **3. Syntax Example**

```sql
-- 1. Change delimiter
DELIMITER //

CREATE PROCEDURE GetHighSalaryEmployees()
BEGIN
    -- Internal statements use the standard semicolon
    SELECT * FROM Employees WHERE Salary > 50000;
    SELECT COUNT(*) FROM Employees WHERE Salary > 50000;
END // 
-- 2. Use the new delimiter to signal the end of the block

-- 3. Reset delimiter back to default
DELIMITER ;

```


### **4. Key Characteristics**

* **Client-Side Command:** `DELIMITER` is a command for the command-line client or MySQL Workbench, not a part of the standard SQL language.
* **Scope:** It only affects the current session.
* **Common Symbols:** While you can use almost any character, `//` and `$$` are the industry standards.


### **5. Summary Table**

| Component | Default (`;`) | Custom (`//`) |
| --- | --- | --- |
| **Inside Procedure** | Ends individual SQL queries. | Not used for end-of-block. |
| **Outside Procedure** | Signals the server to execute. | Used to signal end of the `CREATE` block. |
| **Logic** | Executes line-by-line. | Executes the whole block at once. |


---
<h1 style="color:red">15. Views</h1>

A **View** is a virtual table based on the result set of an SQL statement. It does not store data physically; instead, it provides a "window" through which you can view data stored in one or more real tables (Base Tables).


### **1. Core Concept**

* **Virtual Table:** It looks and acts like a table, with rows and columns, but the data is pulled dynamically from underlying tables whenever the view is queried.
* **Stored Query:** Essentially, a view is a **named query** stored in the database.


### **2. Why Use Views?**

* **Security:** You can restrict a user's access to only specific columns or rows of a table (e.g., hide the `Salary` column but show `Name` and `Dept`).
* **Simplicity:** It hides the complexity of difficult joins. Instead of writing a 10-line query every day, you can just `SELECT * FROM MyView`.
* **Consistency:** It provides a unified, "clean" version of data, even if the underlying table structures change.


### **3. Basic Syntax**

#### **A. Creating a View**

```sql
CREATE VIEW ViewName AS
SELECT column1, column2
FROM TableName
WHERE condition;

```

#### **B. Querying a View**

You use it exactly like a regular table:

```sql
SELECT * FROM ViewName;

```

#### **C. Dropping a View**

```sql
DROP VIEW ViewName;

```


### **4. Updatable vs. Read-Only Views**

Most views are used for reading data, but some can be used to update the underlying tables (Updatable Views).

* **Requirements for Updating:** * The view must reference only **one** table.
* It cannot contain functions like `SUM()`, `AVG()`, `DISTINCT`, or `GROUP BY`.
* It must contain all `NOT NULL` columns of the base table.




### **5. Summary Comparison**

| Feature | Base Table | View |
| --- | --- | --- |
| **Storage** | Occupies physical disk space. | Occupies no space (only the definition is stored). |
| **Data** | Contains actual data records. | Displays data from base tables. |
| **Persistence** | Data stays until deleted. | Reflects real-time changes in base tables. |


### **6. Key Limitations**

* **Performance:** Since it's a "query on a query," very complex views with multiple joins can sometimes be slower than direct table access.
* **Dependencies:** If you drop or rename a base table, any view depending on it will break.


---
<h1 style="color:red">16. Grant and Revoke Statements</h1>

In SQL, **GRANT** and **REVOKE** are the two primary commands used for **Data Control Language (DCL)**. They allow database administrators to manage user permissions and control who can see or modify data.


### **1. The GRANT Statement**

The `GRANT` command gives specific privileges (like reading, writing, or deleting) to a user or a group of users.

* **Syntax:**
```sql
GRANT privilege_name ON object_name TO user_name;

```


* **Example:** Give a user named 'John' the ability to view and add records to the 'Employees' table.
```sql
GRANT SELECT, INSERT ON Employees TO 'John'@'localhost';

```


* **WITH GRANT OPTION:** If this is added to the end of a `GRANT` statement, the user 'John' can then grant those same permissions to other users.


### **2. The REVOKE Statement**

The `REVOKE` command takes away previously granted permissions from a user.

* **Syntax:**
```sql
REVOKE privilege_name ON object_name FROM user_name;

```


* **Example:** Remove the ability to delete records from 'John'.
```sql
REVOKE DELETE ON Employees FROM 'John'@'localhost';

```




### **3. Common Privileges**

You can grant or revoke specific actions or use `ALL PRIVILEGES` for everything.

* **SELECT:** Permission to read data.
* **INSERT:** Permission to add new rows.
* **UPDATE:** Permission to modify existing data.
* **DELETE:** Permission to remove rows.
* **EXECUTE:** Permission to run a stored procedure.
* **ALL PRIVILEGES:** Grants all available permissions on the object.


### **4. Permission Levels**

Permissions can be managed at different layers of the database:

1. **Global Level:** Applies to all databases on the server.
2. **Database Level:** Applies to all tables within a specific database.
3. **Table Level:** Applies to a specific table.
4. **Column Level:** (Supported by some DBs) Limits access to specific sensitive columns.


### **5. Summary Table**

| Aspect | GRANT | REVOKE |
| --- | --- | --- |
| **Action** | Adds permissions. | Removes permissions. |
| **Keyword** | `TO` (followed by user). | `FROM` (followed by user). |
| **Purpose** | Enables user access. | Restricts or cancels user access. |
| **Pre-requisite** | User must exist. | Privilege must have been granted. |


### **Key Rules**

* **Administrative Rights:** Only users with `GRANT OPTION` or Superuser/Root status can execute these commands.
* **Immediate Effect:** In most modern databases, these changes take effect as soon as the command is executed (though some systems might require a `FLUSH PRIVILEGES` command).


---
<h1 style="color:red">17. Temporary Tables</h1>

**Temporary Tables** are special types of tables that allow you to store and process intermediate results. They exist only for a limited time and are automatically deleted by the database when they are no longer needed.


### **1. Core Concept**

* **Short-Lived:** In most databases (like MySQL), a temporary table is visible only to the **current session**.
* **Automatic Cleanup:** When you close your database connection or the session ends, the table and all its data are automatically dropped.
* **Privacy:** Since they are session-specific, two different users can create temporary tables with the same name without interfering with each other.


### **2. Basic Syntax**

#### **A. Creating a Temporary Table**

The syntax is almost identical to a regular `CREATE TABLE` statement, with the addition of the `TEMPORARY` keyword.

```sql
CREATE TEMPORARY TABLE Temp_Sales_Data (
    ProductID INT,
    TotalRevenue DECIMAL(10,2)
);

```

#### **B. Creating from an Existing Query**

This is the most common usage—saving a complex result set for further processing.

```sql
CREATE TEMPORARY TABLE Top_Customers AS
SELECT CustomerID, SUM(Amount) as TotalSpent
FROM Orders
GROUP BY CustomerID
HAVING TotalSpent > 5000;

```


### **3. Why Use Temporary Tables?**

* **Complex Multi-step Logic:** Instead of writing one massive, unreadable query with 5 joins, you can break the logic into steps and store intermediate data in a temp table.
* **Performance:** If you need to run multiple queries against a specific subset of a huge table, it is faster to pull that subset into a temporary table once.
* **Data Transformation:** They provide a safe "scratchpad" to manipulate data before inserting it into a permanent production table.


### **4. Temporary Tables vs. Regular Tables**

| Feature | Temporary Table | Permanent Table |
| --- | --- | --- |
| **Persistence** | Deleted when session ends. | Exists until explicitly dropped. |
| **Visibility** | Only visible to the creator. | Visible to all users with permission. |
| **Storage** | Usually stored in RAM or `tempdb`. | Stored on physical disk. |
| **Usage** | Intermediate processing. | Long-term data storage. |


### **5. Key Limitations**

* **Renaming:** In some databases (like MySQL), you cannot rename a temporary table using `RENAME TABLE`.
* **Self-Joins:** Some versions of MySQL do not allow you to join a temporary table with itself in the same query.
* **Dropped Connections:** If your network drops or the session times out, the data is lost immediately.


### **Summary Tip**

If you need to use the data across different sessions or by different users, use a **Regular Table**. If you just need a temporary "bucket" for a complex calculation happening *right now*, use a **Temporary Table**.


---
<h1 style="color:red">18. Show columns, show Indexes, Show Privileges and Show Grants statements</h1>

In MySQL, the `SHOW` statements are diagnostic commands used to retrieve metadata about database objects, structures, and security settings.


### **1. SHOW COLUMNS**

This statement displays detailed information about the columns in a given table, such as data types, nullability, and keys.

* **Syntax:** `SHOW COLUMNS FROM table_name;`
* **What it reveals:** Field name, Type, Null (Yes/No), Key (PRI/UNI/MUL), Default value, and Extra (like `auto_increment`).
* **Alternative:** `DESCRIBE table_name;` provides the exact same output.


### **2. SHOW INDEXES**

This command lists all the indexing information for a specific table. It is crucial for performance tuning.

* **Syntax:** `SHOW INDEX FROM table_name;`
* **What it reveals:** * **Table:** The name of the table.
* **Non_unique:** 0 if the index cannot contain duplicates, 1 if it can.
* **Key_name:** The name of the index (Primary keys are always named `PRIMARY`).
* **Column_name:** The column(s) associated with the index.
* **Cardinality:** An estimate of the number of unique values in the index.




### **3. SHOW PRIVILEGES**

This statement lists all the types of privileges (permissions) that the MySQL server supports. It is a reference command.

* **Syntax:** `SHOW PRIVILEGES;`
* **What it reveals:** A list of all possible privileges (like `SELECT`, `INSERT`, `UPDATE`, `GRANT OPTION`), the context they apply to (Table, Function, etc.), and a brief description of what each privilege allows.


### **4. SHOW GRANTS**

This statement displays the specific privileges and roles that have been assigned to a particular user account.

* **Syntax:** `SHOW GRANTS FOR 'username'@'hostname';`
* **Usage:**
* To see your own privileges: `SHOW GRANTS;`
* To see another user's privileges: `SHOW GRANTS FOR 'admin'@'localhost';`


* **What it reveals:** The exact `GRANT` statements required to recreate the user's current permission set.


### **Summary Table**

| Command | Purpose | When to Use |
| --- | --- | --- |
| **`SHOW COLUMNS`** | Check table structure. | When you forget data types or null constraints. |
| **`SHOW INDEXES`** | View keys/performance bits. | To check if a column is indexed for faster searching. |
| **`SHOW PRIVILEGES`** | See all possible permissions. | To see what types of access *can* be granted. |
| **`SHOW GRANTS`** | See user-specific permissions. | To troubleshoot why a user can't access a table. |


---
<h1 style="color:red">19. Using trim()</h1>

The **`TRIM()`** function is a string manipulation tool used to remove unwanted characters—most commonly extra whitespace—from the beginning, the end, or both sides of a string.


### **1. Basic Usage (Removing Spaces)**

By default, `TRIM()` removes leading and trailing spaces. It does **not** remove spaces in the middle of a string.

* **Syntax:** `TRIM(string)`
* **Example:**
```sql
SELECT TRIM('   SQL is Fun   ') AS Result;
-- Output: 'SQL is Fun'

```




### **2. Advanced Syntax (Removing Specific Characters)**

You can use `TRIM()` to remove specific characters other than spaces by using the `LEADING`, `TRAILING`, or `BOTH` keywords.

* **Syntax:** `TRIM([{BOTH | LEADING | TRAILING} [remstr] FROM] str)`

| Keyword | Description | Example | Result |
| --- | --- | --- | --- |
| **`LEADING`** | Removes characters from the start. | `TRIM(LEADING 'x' FROM 'xxxHello')` | `'Hello'` |
| **`TRAILING`** | Removes characters from the end. | `TRIM(TRAILING '!' FROM 'Hi!!!')` | `'Hi'` |
| **`BOTH`** | Removes characters from both ends. | `TRIM(BOTH '#' FROM '##Title##')` | `'Title'` |


### **3. Related Functions: LTRIM() and RTRIM()**

Most SQL dialects provide two specialized versions of TRIM for quick use:

* **`LTRIM(string)`**: Removes spaces only from the **Left** (start).
* **`RTRIM(string)`**: Removes spaces only from the **Right** (end).


### **4. Why Use TRIM()?**

* **Data Cleaning:** Users often accidentally press space before or after typing a value into a form. `TRIM()` ensures " admin" and "admin " are both treated as "admin".
* **Storage Optimization:** Removing unnecessary trailing spaces before saving to a database.
* **Join Accuracy:** Leading or trailing spaces can cause `JOIN` or `WHERE` clauses to fail because `'India'` is not equal to `'India '`.


### **5. Summary Table**

| Function | Action | Example Input | Example Output |
| --- | --- | --- | --- |
| `TRIM()` | Both sides | `'  test  '` | `'test'` |
| `LTRIM()` | Left side only | `'  test  '` | `'test  '` |
| `RTRIM()` | Right side only | `'  test  '` | `'  test'` |


---

