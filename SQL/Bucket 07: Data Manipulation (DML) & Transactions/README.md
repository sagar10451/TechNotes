

<h1 style="color:red">1. Insert into Statements for inserting data into tables</h1>

The **`INSERT INTO`** statement is a Data Manipulation Language (DML) command used to add new rows of data to an existing table.

### **1. Basic Syntax (Specifying Columns)**

This is the safest method because it maps values directly to specific columns.

```sql
INSERT INTO TableName (Column1, Column2, Column3)
VALUES (Value1, Value2, Value3);

```

* **Example:**
`INSERT INTO Students (ID, Name, Age) VALUES (101, 'Rahul', 20);`


### **2. Short Syntax (All Columns)**

If you are adding values for **every single column** in the exact order they were created, you can skip the column names.

```sql
INSERT INTO TableName
VALUES (Value1, Value2, Value3);

```

* **Warning:** If the table structure changes (e.g., a new column is added), this query will break.


### **3. Inserting Multiple Rows**

You can insert several records in a single command by separating value sets with commas. This is much faster than running multiple individual queries.

```sql
INSERT INTO Students (ID, Name, Age)
VALUES 
(102, 'Sanya', 21),
(103, 'Amit', 22),
(104, 'Vijay', 20);

```


### **4. Key Rules for Inserting**

* **Data Types:** String and Date values must be enclosed in **single quotes** (`'...'`). Numeric values do not need quotes.
* **Constraints:** If a column is marked as `NOT NULL` and has no default value, you **must** provide a value for it.
* **Auto-Increment:** If a column (like an ID) is set to `AUTO_INCREMENT`, you can either omit it from the column list or pass `NULL`/`0` to let the database handle the numbering.


### **5. Inserting from Another Table**

You can copy data from one table directly into another using a subquery.

```sql
INSERT INTO Archive_Students
SELECT * FROM Students WHERE GraduationYear = 2025;

```


---
<h1 style="color:red">2. Null Value, Is Null operator, and is not null operator</h1>

In database terminology, **NULL** represents a missing, unknown, or inapplicable value. It is important to remember that **NULL is not the same as zero or an empty string.**


### **1. What is a NULL Value?**

* **Definition:** A field with a NULL value is a field with no value.
* **Comparison:** You cannot use arithmetic comparison operators (, , ) with NULL because "unknown" cannot be equal to anything, even another "unknown."
* **Storage:** It acts as a placeholder for data that hasn't been provided yet.


### **2. IS NULL Operator**

Since you cannot use `=`, you must use the `IS NULL` operator to test for empty fields.

* **Syntax:** `SELECT column_name FROM table_name WHERE column_name IS NULL;`
* **Example:** To find all students who haven't provided an email address:
```sql
SELECT Name FROM Students WHERE Email IS NULL;

```




### **3. IS NOT NULL Operator**

This operator is used to filter records that **do** have a value in a specific column.

* **Syntax:** `SELECT column_name FROM table_name WHERE column_name IS NOT NULL;`
* **Example:** To get a list of all students who have a phone number on file:
```sql
SELECT Name FROM Students WHERE PhoneNumber IS NOT NULL;

```




### **4. Key Characteristics**

* **The "Unknown" Logic:** In SQL, `NULL = NULL` results in "Unknown" (False), which is why specialized operators are required.
* **Constraints:** You can prevent NULLs in your data by using the `NOT NULL` constraint when creating a table.
* **Aggregates:** Most aggregate functions (like `SUM()`, `AVG()`, and `COUNT()`) **ignore** NULL values. For example, `COUNT(Column)` only counts rows where that column is not NULL.


### **5. Summary Table**

| Comparison | Result | Correct Operator |
| --- | --- | --- |
| `WHERE Score = NULL` | **Incorrect** (No rows returned) | `IS NULL` |
| `WHERE Score != NULL` | **Incorrect** (No rows returned) | `IS NOT NULL` |
| `WHERE Score IS NULL` | **Correct** | Matches empty fields |



---
<h1 style="color:red">3. Delete Statement</h1>

The **`DELETE`** statement is a Data Manipulation Language (DML) command used to remove existing records from a table.


### **1. Basic Syntax**

To delete specific rows, you must use a `WHERE` clause.

```sql
DELETE FROM TableName 
WHERE condition;

```

* **Example:**
`DELETE FROM Students WHERE StudentID = 101;`


### **2. Deleting All Records**

If you omit the `WHERE` clause, **all rows** in the table will be deleted, but the table structure (columns, indexes, and constraints) will remain.

```sql
DELETE FROM Students;

```

> **Warning:** Always double-check your `WHERE` clause before executing a delete. Once data is deleted, it cannot be undone unless you are using transactions.


### **3. DELETE vs. TRUNCATE**

While both remove data, they behave differently behind the scenes:

| Feature | DELETE | TRUNCATE |
| --- | --- | --- |
| **Type** | DML (Data Manipulation) | DDL (Data Definition) |
| **Speed** | Slower (removes row by row) | Faster (deallocates pages) |
| **Where Clause** | Supported (can delete specific rows) | Not supported (deletes everything) |
| **Auto-Increment** | Does NOT reset the counter | Resets the counter to 1 |
| **Logging** | Logs each row removal | Logs page deallocation |


### **4. Key Characteristics**

* **Rollback:** In a transactional database, a `DELETE` operation can be rolled back (undone) if it hasn't been committed.
* **Triggers:** `DELETE` will fire any "ON DELETE" triggers associated with the table.
* **Foreign Keys:** If the row you are deleting is referenced by another table, the delete may fail (Restricted) or cascade (Delete related rows) depending on your **Foreign Key** settings.


### **5. Safe Delete Tip**

Before running a delete, run a `SELECT` with the same condition to verify which rows will be affected:

```sql
-- Step 1: Verify
SELECT * FROM Students WHERE LastLogin < '2024-01-01';

-- Step 2: Delete
DELETE FROM Students WHERE LastLogin < '2024-01-01';

```


---

<h1 style="color:red">4. Update statement and Set Keyword</h1>

The **`UPDATE`** statement is a Data Manipulation Language (DML) command used to modify existing records in a table. The **`SET`** keyword is used to specify exactly which columns should be changed and what the new values should be.


### **1. Basic Syntax**

To update specific rows, you combine `UPDATE`, `SET`, and `WHERE`.

```sql
UPDATE TableName
SET Column1 = Value1, Column2 = Value2
WHERE condition;

```

* **Example:** `UPDATE Students SET Age = 21, City = 'New York' WHERE ID = 101;`


### **2. The Role of the SET Keyword**

* The `SET` clause tells the database: "Change the data in these specific fields."
* You can update multiple columns at once by separating them with a **comma**.
* You can use the column's existing value in the calculation.
* *Example:* `SET Salary = Salary * 1.10` (increases salary by 10%).




### **3. The Importance of the WHERE Clause**

The `WHERE` clause determines which rows are updated.

* **With WHERE:** Only the rows meeting the condition are changed.
* **Without WHERE:** **Every single row** in the table will be updated with the new values.
* *Danger:* `UPDATE Employees SET Salary = 50000;` (This gives everyone the exact same salary).




### **4. Key Characteristics**

* **Atomic Operation:** If an update fails for one row (due to a constraint), the entire statement usually fails, leaving the data unchanged.
* **Data Consistency:** You cannot set a value that violates column constraints (e.g., putting a string into an `INT` column).
* **Audit Triggers:** Many databases use triggers to automatically record the old value in a separate table before the `UPDATE` overwrites it.


### **5. Best Practices**

1. **Select before Update:** Run a `SELECT` with your `WHERE` clause first to make sure you are targeting the correct records.
2. **Use Primary Keys:** When updating a single record, always use the `PRIMARY KEY` (like ID) in the `WHERE` clause to avoid accidental changes to similar rows.


### **Summary Table**

| Component | Purpose | Required? |
| --- | --- | --- |
| **`UPDATE`** | Identifies the table to modify. | Yes |
| **`SET`** | Lists columns and their new values. | Yes |
| **`WHERE`** | Filters which rows to update. | No (but highly recommended) |


---


<h1 style="color:red">5. Set Autocommit</h1>

In MySQL, **Autocommit** is a system variable that determines whether changes made by SQL statements (like `INSERT`, `UPDATE`, or `DELETE`) are saved to the database automatically and immediately.


### **1. What is Autocommit?**

* **When ON (Default):** Every single SQL statement is treated as a complete **transaction**. As soon as you press enter/execute, the change is permanent.
* **When OFF:** Changes are "pending." They are not permanently saved until you explicitly issue a `COMMIT` command. If you change your mind, you can use `ROLLBACK` to undo the pending changes.


### **2. Syntax and Usage**

* **Check current status:**
`SELECT @@autocommit;` (1 means ON, 0 means OFF).
* **Turn Autocommit OFF:**
`SET AUTOCOMMIT = 0;` (or `SET AUTOCOMMIT = OFF;`)
* **Turn Autocommit ON:**
`SET AUTOCOMMIT = 1;` (or `SET AUTOCOMMIT = ON;`)


### **3. The Workflow when Autocommit is OFF**

When you disable autocommit, you enter a manual transaction mode:

1. **Execute changes:** `UPDATE Accounts SET Balance = Balance - 100 WHERE ID = 1;`
2. **Verify:** The change is visible *to you*, but not to other users yet.
3. **Finalize:**
* To Save: `COMMIT;`
* To Undo: `ROLLBACK;`




### **4. Why Use It?**

* **Data Safety:** It acts as a "safety net." If you accidentally run a `DELETE` without a `WHERE` clause, you can `ROLLBACK` to recover your data (if autocommit was off).
* **Atomicity:** It allows you to group multiple statements together. For example, in a bank transfer, you want to ensure both "Subtract from A" and "Add to B" happen together. If one fails, you rollback both.


### **5. Key Characteristics**

* **Session-Based:** Changing autocommit usually only affects your current connection/window.
* **DDL Exception:** Commands like `CREATE`, `ALTER`, and `DROP` (Data Definition Language) cause an **implicit commit**. They are saved immediately even if autocommit is OFF.


### **Summary Table**

| Feature | Autocommit ON (Default) | Autocommit OFF |
| --- | --- | --- |
| **Saving** | Instant and automatic | Manual (requires `COMMIT`) |
| **Undo** | Impossible (without backups) | Possible (using `ROLLBACK`) |
| **Best for** | Simple, daily queries | Complex, multi-step transactions |


---

<h1 style="color:red">6. commit statement</h1>

### **`COMMIT` Statement**

The `COMMIT` statement is a Transaction Control Language (TCL) command used to **permanently save** all changes made during the current transaction to the database.


### **1. Functionality**

When a transaction is in progress (and `AUTOCOMMIT` is turned off), changes like `INSERT`, `UPDATE`, or `DELETE` are stored in a temporary buffer. The `COMMIT` command:

* Makes all pending changes **permanent**.
* Makes the changes **visible** to all other database users.
* Releases any **locks** held on the affected rows, allowing other transactions to access them.


### **2. Syntax**

```sql
COMMIT;

```

### **3. Transaction Workflow**

1. **Start:** `SET AUTOCOMMIT = 0;` (Begin manual transaction).
2. **Execute:** `UPDATE Accounts SET Balance = Balance - 500 WHERE ID = 101;`
3. **Execute:** `UPDATE Accounts SET Balance = Balance + 500 WHERE ID = 102;`
4. **Finalize:** `COMMIT;` (Both updates are now saved forever).


### **4. Key Characteristics**

* **Point of No Return:** Once a `COMMIT` is executed, you **cannot** use the `ROLLBACK` command to undo those specific changes.
* **Implicit Commit:** Certain commands (DDL) like `CREATE`, `DROP`, or `ALTER` will automatically trigger an implicit commit, saving any pending DML changes immediately.
* **Consistency:** It ensures that a group of related operations are treated as a single unit of work (Atomicity).


### **5. Comparison: COMMIT vs. ROLLBACK**

| Feature | `COMMIT` | `ROLLBACK` |
| --- | --- | --- |
| **Action** | Saves changes permanently. | Undoes pending changes. |
| **Visibility** | Changes become public. | Changes are discarded. |
| **State** | Ends the current transaction. | Ends the current transaction. |


### **Summary Tip**

Think of `COMMIT` as the **"Save" button** in a document. Until you hit save, your edits exist only in the computer's temporary memory; once you hit save, they are written to the disk.


---
<h1 style="color:red">7. Rollback Statement</h1>

### **`ROLLBACK` Statement**

The `ROLLBACK` statement is a Transaction Control Language (TCL) command used to **undo** changes that have not yet been permanently saved to the database.


### **1. Functionality**

If a mistake is made during a transaction, or if an error occurs, `ROLLBACK` allows you to revert the data to its **last committed state**.

* It cancels all `INSERT`, `UPDATE`, or `DELETE` operations performed since the last `COMMIT`.
* It releases any database **locks** on the rows involved.


### **2. Syntax**

```sql
ROLLBACK;

```

### **3. Transaction Workflow**

1. **Start:** `SET AUTOCOMMIT = 0;`
2. **Mistake:** `DELETE FROM Employees;` (Forgot the `WHERE` clause!)
3. **Panic/Verify:** You notice the table is empty in your current view.
4. **Recovery:** `ROLLBACK;` (All employee records are restored).


### **4. Rollback to Savepoint**

You don't always have to undo the *entire* transaction. You can use **Savepoints** to roll back to a specific "bookmark" within the transaction.

* **Create Bookmark:** `SAVEPOINT sp1;`
* **Undo to Bookmark:** `ROLLBACK TO sp1;`


### **5. Key Characteristics**

* **Time Limit:** You can only roll back changes that have not been **committed** yet. Once `COMMIT` is executed, the `ROLLBACK` command has no effect on those changes.
* **Session Bound:** A rollback only affects the changes made in your specific database session/connection.
* **Automatic Rollback:** Many database systems will perform an automatic rollback if a connection is lost or the system crashes in the middle of a transaction.


### **Summary Table**

| Feature | `ROLLBACK` | `COMMIT` |
| --- | --- | --- |
| **Result** | Changes are discarded. | Changes are saved. |
| **Data State** | Reverts to previous state. | Sets a new permanent state. |
| **Undo Ability** | It IS the undo button. | Prevents future undoing. |



---
<h1 style="color:red">8. Truncate Statement</h1>

### **`TRUNCATE` Statement**

The `TRUNCATE TABLE` statement is a Data Definition Language (**DDL**) command used to delete all rows from a table while keeping the table structure intact. It is often described as a "reset" for a table.


### **1. Syntax**

```sql
TRUNCATE TABLE table_name;

```

### **2. Key Characteristics**

* **Fast Execution:** It is much faster than `DELETE` because it deallocates the data pages instead of deleting rows one by one.
* **Resets Auto-Increment:** It resets the `AUTO_INCREMENT` counter back to **1** (or the defined seed value).
* **Minimal Logging:** It logs the deallocation of pages rather than individual row deletions, making it more efficient for large datasets.
* **Implicit Commit:** Since it is a DDL command, it triggers an **implicit commit**. You generally cannot `ROLLBACK` a truncate operation in MySQL.


### **3. TRUNCATE vs. DELETE vs. DROP**

| Feature | `TRUNCATE` | `DELETE` | `DROP` |
| --- | --- | --- | --- |
| **Category** | DDL | DML | DDL |
| **Action** | Removes all rows | Removes specific/all rows | Deletes entire table |
| **Structure** | Kept | Kept | Removed |
| **Speed** | Very Fast | Slow (row-by-row) | Fast |
| **`WHERE` clause** | Not allowed | Supported | Not allowed |
| **Rollback** | No (mostly) | Yes | No |


### **4. Constraints and Rules**

* **Foreign Keys:** You cannot `TRUNCATE` a table if it is referenced by a `FOREIGN KEY` constraint in another table (unless you disable foreign key checks).
* **Triggers:** `TRUNCATE` does **not** fire any `ON DELETE` triggers because it doesn't operate on individual rows.


### **5. Summary Tip**

Use `TRUNCATE` when you want to **empty a table completely** and start fresh (like clearing a temporary log table). Use `DELETE` when you need to remove specific records or need the ability to undo the action.


---
<h1 style="color:red">9. Insert into</h1>

The **`INSERT INTO`** statement is a Data Manipulation Language (DML) command used to add new records (rows) to an existing table in a database.


### **1. Basic Syntax**

There are two primary ways to write an `INSERT` statement:

#### **A. Specifying Columns (Recommended)**

You specify both the column names and the values to be inserted. This is safer if the table structure changes later.

```sql
INSERT INTO table_name (column1, column2, column3)
VALUES (value1, value2, value3);

```

#### **B. Values Only**

If you are adding values for **all** columns in the exact order they appear in the table, you can omit the column names.

```sql
INSERT INTO table_name
VALUES (value1, value2, value3);

```


### **2. Practical Examples**

* **Inserting a single row:**
`INSERT INTO Students (ID, Name, Age) VALUES (1, 'Alice', 20);`
* **Inserting multiple rows at once:**
You can separate sets of values with commas to improve performance.
```sql
INSERT INTO Students (ID, Name, Age)
VALUES 
(2, 'Bob', 22),
(3, 'Charlie', 21),
(4, 'Diana', 23);

```




### **3. Key Characteristics**

* **Data Types:** Numeric values do not need quotes, but **Strings** (VARCHAR) and **Dates** must be enclosed in single quotes (e.g., `'Alice'`, `'2026-02-16'`).
* **Null Values:** If a column allows `NULL` and you don't provide a value, it will be filled with `NULL`.
* **Auto-Increment:** If a column (like an ID) is set to `AUTO_INCREMENT`, you can skip it in your column list, and the database will generate the next number automatically.


### **4. Inserting from Another Table**

You can use the `INSERT INTO ... SELECT` statement to copy data from one table to another.

```sql
INSERT INTO Archived_Students
SELECT * FROM Students WHERE GraduationYear = 2025;

```


---
<h1 style="color:red">10. Inserting Null</h1>

When inserting data into a table, you may encounter situations where certain information is unknown or not applicable. In such cases, you can insert a **NULL** value.


### **1. Methods of Inserting NULL**

There are two primary ways to ensure a column receives a NULL value:

#### **A. Implicitly (Omission)**

If you omit a column from the `INSERT` statement, and that column has no `DEFAULT` value defined, MySQL will automatically insert `NULL`.

```sql
-- The 'Email' column is omitted, so it becomes NULL
INSERT INTO Students (ID, Name) 
VALUES (105, 'Suresh');

```

#### **B. Explicitly (Directly)**

You can explicitly type the keyword `NULL` in the `VALUES` list.

```sql
-- Specifically stating that Email is NULL
INSERT INTO Students (ID, Name, Email) 
VALUES (106, 'Meena', NULL);

```


### **2. Requirements for Inserting NULL**

For an insertion of `NULL` to succeed, the column must meet specific criteria:

* **No "NOT NULL" Constraint:** The column must be allowed to accept nulls. If you try to insert `NULL` into a column defined as `NOT NULL`, MySQL will throw an error.
* **Primary Keys:** You **cannot** insert `NULL` into a column defined as a `PRIMARY KEY`.


### **3. NULL vs. Empty String/Zero**

It is a common mistake to confuse `NULL` with other empty-looking values:

| Value | Meaning | Type |
| --- | --- | --- |
| **`NULL`** | No value/Unknown | Absence of data |
| **`''`** | Empty String | Text data with zero length |
| **`0`** | Zero | Numeric data |


### **4. Practical Use Case**

If you have a form where a user leaves the "Secondary Phone Number" blank, the backend SQL should insert a `NULL` rather than a `0`, so that aggregate functions like `COUNT()` don't miscalculate the actual number of phones on file.


---
