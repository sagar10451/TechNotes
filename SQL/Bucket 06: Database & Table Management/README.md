
<h1 style="color:red">1. Installing MySQL server and workbench</h1>

Installing MySQL involves two main components: the **Server** (the engine that stores and manages data) and the **Workbench** (the visual "dashboard" used to write queries and design databases).


### **1. Downloading the Installer**

* Go to the [MySQL Official Downloads page](https://dev.mysql.com/downloads/installer/).
* Choose the **MySQL Installer for Windows** (or the relevant DMG for macOS).
* **Web Community vs. Community:** The "Web" version is a small file that downloads components as you go; the "Community" version is a larger file containing everything offline.

### **2. Installation Steps (Using MySQL Installer)**

When you run the installer, follow these steps:

1. **Choosing Setup Type:** Select **"Developer Default"** (installs both Server and Workbench) or **"Custom"** if you only want specific tools.
2. **Check Requirements:** The installer will check for necessary software (like Microsoft Visual C++). Click **Execute** to install missing items.
3. **Installation:** Click **Execute** to begin downloading/installing the MySQL Server and Workbench.


### **3. Configuration (Crucial Steps)**

After installation, the installer will guide you through the setup:

* **Type and Networking:** Keep defaults (Port **3306**).
* **Authentication Method:** Choose **"Use Strong Password Encryption"** (recommended).
* **Accounts and Roles:** * Create a **Root Password**. **Do not forget this password!** It is the "Master Key" to your database.
* You can optionally add a standard "User" account here for daily tasks.


* **Windows Service:** Keep "Run as a Windows Service" checked so MySQL starts automatically when you turn on your computer.


### **4. Launching MySQL Workbench**

Once finished, open **MySQL Workbench**:

1. Click on the **"Local Instance MySQL80"** box on the home screen.
2. Enter the **Root Password** you created earlier.
3. You are now ready to write SQL!


### **5. Verification**

To ensure everything is working, type this in the Workbench query tab and click the lightning bolt icon:

```sql
SELECT version();

```

If it returns the version number (e.g., `8.0.x`), your installation is successful.


---
<h1 style="color:red">2. Creating, Deleting, Viewing and using Databases</h1>

In SQL, a **Database** is a container that holds tables, views, and other related data objects. Managing them involves a few simple "Data Definition Language" (DDL) commands.

### **1. Creating a Database**

This command initializes a new, empty database.

* **Syntax:** `CREATE DATABASE database_name;`
* **Example:** `CREATE DATABASE SchoolDB;`
* **Note:** Database names should be unique and usually don't contain spaces.


### **2. Viewing Databases**

To see a list of all databases currently available on your server:

* **Syntax:** `SHOW DATABASES;`
* **What it does:** It displays a table of all existing database names, including system-defined ones like `information_schema` and `mysql`.


### **3. Using a Database**

Before you can create tables or query data, you must tell the server which database you want to work with.

* **Syntax:** `USE database_name;`
* **Example:** `USE SchoolDB;`
* **Tip:** In MySQL Workbench, the "active" database name will appear in **bold** in the schemas sidebar.


### **4. Deleting (Dropping) a Database**

This command permanently removes the database and **all tables/data inside it**.

* **Syntax:** `DROP DATABASE database_name;`
* **Example:** `DROP DATABASE SchoolDB;`
* **Warning:** There is no "Undo" or "Recycle Bin" for this command. Use it with extreme caution.


### **5. Best Practice: "IF EXISTS" and "IF NOT EXISTS"**

To prevent errors if a database already exists (or doesn't exist), use these safety clauses:

* **Creation:** `CREATE DATABASE IF NOT EXISTS SchoolDB;`
* **Deletion:** `DROP DATABASE IF EXISTS SchoolDB;`



---
<h1 style="color:red">3. Creating, viewing, Describing and Deleting Tables</h1>

Once you have selected a database with the `USE` command, you can begin managing **Tables**, which are the structures that actually hold your data in rows and columns.

### **1. Creating a Table**

To create a table, you must define the **table name**, **column names**, and **data types** for each column.

* **Syntax:**
```sql
CREATE TABLE table_name (
    column1 datatype constraints,
    column2 datatype constraints,
    ...
);

```


* **Example:**
```sql
CREATE TABLE Students (
    ID INT PRIMARY KEY,
    Name VARCHAR(50),
    Age INT,
    Email VARCHAR(100)
);

```




### **2. Viewing Tables**

To see a list of all tables present in the currently selected database:

* **Command:** `SHOW TABLES;`
* **What it does:** Returns a simple list of names for every table in that database.


### **3. Describing a Table (Checking Structure)**

If you need to see the "blueprint" of a table (what columns it has, their data types, and which one is the Primary Key):

* **Command:** `DESC table_name;` (or `DESCRIBE table_name;`)
* **Example:** `DESC Students;`
* **Output:** It shows a table with fields: `Field`, `Type`, `Null`, `Key`, `Default`, and `Extra`.


### **4. Deleting a Table**

This permanently removes the table structure and all the data stored within it.

* **Command:** `DROP TABLE table_name;`
* **Example:** `DROP TABLE Students;`
* **Safety Tip:** Use `DROP TABLE IF EXISTS table_name;` to avoid an error if the table has already been deleted.


### **5. Truncating a Table (Bonus)**

If you want to delete **all the data** inside a table but keep the table structure itself for future use:

* **Command:** `TRUNCATE TABLE table_name;`
* **Difference:** `DROP` kills the table; `TRUNCATE` just empties it.



---

<h1 style="color:red">4. Data Types</h1>

Data types define what kind of value a column can hold. Choosing the correct data type is crucial for database performance and data integrity.


### **1. Numeric Data Types**

Used for storing whole numbers and decimals.

* **`INT`**: Standard integer. Stores whole numbers (e.g., `1`, `100`, `-500`).
* **`DECIMAL(p, s)`**: Fixed-point numbers where precision is critical (e.g., Money).
* *Example:* `DECIMAL(10, 2)` can store up to 10 digits total, with 2 after the decimal (e.g., `99999999.99`).


* **`FLOAT` / `DOUBLE**`: Floating-point numbers for approximate values or scientific data.


### **2. String (Text) Data Types**

Used for names, addresses, and descriptions.

* **`CHAR(n)`**: Fixed-length string. If you define `CHAR(10)` and store "Hi", it still uses 10 bytes of space (faster for very short, consistent data like ISO country codes).
* **`VARCHAR(n)`**: Variable-length string. If you define `VARCHAR(100)` and store "Hi", it only uses 2 bytes of space. Most common for names/emails.
* **`TEXT`**: Used for long-form data like blog posts or descriptions (up to 65,535 characters).


### **3. Date and Time Data Types**

Used for timestamps and scheduling.

* **`DATE`**: Stores only the date (`YYYY-MM-DD`).
* **`TIME`**: Stores only the time (`HH:MM:SS`).
* **`DATETIME`**: Stores both date and time (`YYYY-MM-DD HH:MM:SS`).
* **`YEAR`**: Stores a 4-digit year.


### **4. Miscellaneous Types**

* **`BOOLEAN`**: Technically stored as `TINYINT(1)` in MySQL (0 for False, 1 for True).
* **`BLOB`**: Binary Large Object. Used for storing images, PDFs, or audio files directly in the database.
* **`ENUM`**: A string object with a value chosen from a list of permitted values (e.g., `ENUM('Small', 'Medium', 'Large')`).


### **Summary Table**

| Category | Most Common Type | Best Use Case |
| --- | --- | --- |
| **Numbers** | `INT` | IDs, Quantities, Ages |
| **Text** | `VARCHAR` | Names, Emails, Addresses |
| **Money** | `DECIMAL` | Prices, Salaries, Balances |
| **Dates** | `DATE` / `DATETIME` | Birthdays, Order timestamps |


---


<h1 style="color:red">5. Rename Statement and To Keyword</h1>

In MySQL, the **`RENAME`** statement is used to change the name of an existing table. The **`TO`** keyword is a mandatory part of this syntax, acting as the bridge between the old name and the new name.

### **1. Renaming a Table**

This is the most common use. It is a "Data Definition Language" (DDL) operation.

* **Syntax:**
```sql
RENAME TABLE old_table_name TO new_table_name;

```


* **Example:**
```sql
RENAME TABLE Students TO EnrolledStudents;

```




### **2. Renaming Multiple Tables**

One of the unique features of `RENAME TABLE` is that you can rename several tables in a single statement.

* **Example:**
```sql
RENAME TABLE Users TO Customers, 
             Orders TO SalesData;

```




### **3. Renaming a Column**

While the `RENAME TABLE` command is for entire tables, if you want to rename a specific **column**, you use the `ALTER TABLE` statement combined with the `RENAME COLUMN` keywords (available in MySQL 8.0+).

* **Syntax:**
```sql
ALTER TABLE table_name RENAME COLUMN old_col_name TO new_col_name;

```


* **Example:**
```sql
ALTER TABLE Students RENAME COLUMN Age TO StudentAge;

```




### **4. Key Rules and Characteristics**

* **Database Active:** You must have the necessary permissions (ALTER and DROP) on the table to rename it.
* **Integrity:** When you rename a table, any **Views** or **Stored Procedures** that reference the old name will break and must be updated manually.
* **No Data Loss:** Renaming is a structural change; the data rows inside the table remain untouched.
* **Atomicity:** If you rename multiple tables in one line, the operation is atomic (it either all happens or none of it happens).


### **5. Moving Tables Between Databases**

You can actually use the `RENAME` statement to move a table from one database to another on the same server.

* **Example:**
```sql
RENAME TABLE database1.MyTable TO database2.MyTable;

```




---

<h1 style="color:red">6. Alter Statement, add, modify column, rename column and drop column keywords</h1>

The **`ALTER TABLE`** statement is used to change the structure of an existing table. It allows you to add, delete, or modify columns and constraints without losing the data already stored in the table.


### **1. ADD Column**

Used to add a new column to an existing table.

* **Syntax:** `ALTER TABLE table_name ADD column_name datatype;`
* **Example:** `ALTER TABLE Students ADD Email VARCHAR(100);`
* **Note:** By default, the new column is added at the end. You can use `FIRST` or `AFTER existing_column` to control placement.


### **2. MODIFY COLUMN**

Used to change the **data type** or **constraints** of an existing column (e.g., changing `VARCHAR(50)` to `VARCHAR(100)`).

* **Syntax:** `ALTER TABLE table_name MODIFY COLUMN column_name new_datatype;`
* **Example:** `ALTER TABLE Students MODIFY COLUMN Age TINYINT;`


### **3. RENAME COLUMN**

Used to change the name of a specific column.

* **Syntax:** `ALTER TABLE table_name RENAME COLUMN old_name TO new_name;`
* **Example:** `ALTER TABLE Students RENAME COLUMN Name TO FullName;`


### **4. DROP COLUMN**

Used to permanently delete a column and all the data within it from the table.

* **Syntax:** `ALTER TABLE table_name DROP COLUMN column_name;`
* **Example:** `ALTER TABLE Students DROP COLUMN Email;`


### **Summary Table**

| Action | Keyword | Purpose |
| --- | --- | --- |
| **Add New** | `ADD` | Inserts a new column into the structure. |
| **Change Type** | `MODIFY` | Changes the definition/data type of a column. |
| **Change Name** | `RENAME` | Gives an existing column a new name. |
| **Remove** | `DROP` | Deletes a column and its data forever. |


### **Key Rules**

* **Data Loss:** Be careful with `DROP COLUMN`; there is no undo button.
* **Compatibility:** When using `MODIFY`, ensure the existing data is compatible with the new type (e.g., don't change `VARCHAR` to `INT` if the column contains letters).
* **Locking:** On very large tables, `ALTER` operations can lock the table, meaning other users cannot write to it until the change is finished.


---
<h1 style="color:red">7. Indexes</h1>

An **Index** is a powerful database object used to speed up the retrieval of rows from a table. Think of it like the **index at the back of a book**: instead of reading every single page to find a topic, you look it up in the index and jump straight to the correct page.


### **1. How It Works**

Without an index, the database must perform a **Full Table Scan** (checking every row). With an index, the database uses a search tree structure (typically a **B-Tree**) to find the data in a fraction of the time.


### **2. Types of Indexes**

* **Primary Index:** Automatically created when you define a `PRIMARY KEY`. It uniquely identifies each row and is the fastest search path.
* **Unique Index:** Ensures that all values in a column are distinct (e.g., Email or Phone Number).
* **Single-Column Index:** Created on just one column.
* **Composite (Multiple-Column) Index:** Created on two or more columns combined. Useful for queries that frequently filter by both columns (e.g., `WHERE LastName='Smith' AND FirstName='John'`).
* **Full-Text Index:** Used for searching complex text or long strings (keywords inside a blog post).


### **3. Basic Syntax**

* **Create an Index:**
```sql
CREATE INDEX idx_lastname ON Employees(LastName);

```


* **View Indexes for a table:**
```sql
SHOW INDEX FROM Employees;

```


* **Delete an Index:**
```sql
DROP INDEX idx_lastname ON Employees;

```




### **4. The "Cost" of Indexing**

While indexes make **Read** operations (`SELECT`) faster, they make **Write** operations (`INSERT`, `UPDATE`, `DELETE`) slightly slower. This is because the database must update the index every time the data changes.

> **Rule of Thumb:** Index columns that are frequently used in `WHERE`, `JOIN`, and `ORDER BY` clauses, but avoid over-indexing tables that are updated constantly.


### **5. Summary Table**

| Feature | Without Index | With Index |
| --- | --- | --- |
| **Search Speed** | Slow (Full Table Scan) | Very Fast (Pointer lookup) |
| **Disk Space** | Less | More (Index files take space) |
| **Insert/Update** | Faster | Slightly Slower |


---
<h1 style="color:red">8. Database Objects</h1>

In a database management system, **Database Objects** are the underlying structures used to store, manage, and access data. Each object serves a specific purpose in organizing information or improving performance.


### **1. Tables**

The most fundamental object. Tables store data in a structured format consisting of **rows** (records) and **columns** (attributes).

* **Role:** The primary container for all data.

### **2. Views**

A "virtual table" based on the result-set of an SQL query. It does not store data itself but provides a way to look at data from one or more tables.

* **Role:** Simplifies complex queries and provides a layer of security by hiding sensitive columns.
* **Example:** A view that shows only employee names and departments, but hides their salaries.

### **3. Indexes**

A pointer structure associated with a table to speed up data retrieval.

* **Role:** Reduces the time taken to search for specific records (like a book's index).

### **4. Stored Procedures**

A prepared SQL code that you can save and reuse. Instead of writing the same query over and over, you call the procedure.

* **Role:** Improves performance and ensures consistency in logic.

### **5. Triggers**

A special type of stored procedure that automatically "fires" (executes) when an event occurs in the database (like `INSERT`, `UPDATE`, or `DELETE`).

* **Role:** Used for data validation, auditing, or maintaining complex business rules.

### **6. Sequences**

An object used to generate a unique set of integers, typically used for primary keys.

* **Role:** Ensures every new record gets a unique ID automatically.


### **Summary Table**

| Object | Purpose | Storage |
| --- | --- | --- |
| **Table** | Store data | Physical storage |
| **View** | Simplify/Secure access | Logic only (Virtual) |
| **Index** | Speed up search | Physical storage |
| **Trigger** | Automated action | Logic only |



---

