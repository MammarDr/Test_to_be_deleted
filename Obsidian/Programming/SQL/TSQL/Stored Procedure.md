
**Stored procedures** in T-SQL are a powerful feature of **SQL Server**. They allow you to encapsulate SQL code, which can be executed repeatedly. Stored procedures are beneficial for several reasons:

- **Performance**: They are compiled and stored in the database, leading to faster execution times.
- **Security**: They provide an additional layer of security by restricting direct access to the data.
- **Maintainability**: Centralizing business logic in stored procedures makes changes easier and more consistent.

## CREATE SP:

```sql
CREATE PROCEDURE SP_AddNewPerson
	@Name VARCHAR(30),
	@DepartmentID INT,
	@HireDate Date,
	@NewPersonID INT OUTPUT
AS
BEGIN
	INSERT INTO Employees(Name, DepartmentID, HireDate)
		VALUES(@Name, @DepartmentID, @HireDate);

	SET @NewPersonID = SCOPE_IDENTITY();
END;
```

> USE ALTER KEYWORD TO EDIT SP

## EXECUTE SP:

```sql
DECLARE @PersonID INT;
EXEC SP_AddNewPerson 
    @FirstName = 'John', 
    @LastName = 'Doe', 
    @Email = 'john.doe@example.com',
    @NewPersonID = @PersonID OUTPUT;

SELECT @PersonID AS NewPersonID;
```


## RETURN STATMENT:

```sql
CREATE PROCEDURE SP_CheckPersonExists
    @PersonID INT
AS
BEGIN
    IF EXISTS(SELECT * FROM People WHERE PersonID = @PersonID)
        RETURN 1;  -- Person exists
    ELSE
        RETURN 0;  -- Person does not exist
END

DECLARE @Result INT;
EXEC @Result = SP_CheckPersonExists @PersonID = 123; -- Replace 123 with the actual PersonID


IF @Result = 1
    PRINT 'Person exists.';
ELSE
    PRINT 'Person does not exist.';

DROP PROCEDURE SP_CheckPersonExists;
```


The `RETURN` statement in a **stored procedure** is used to **return an integer value** to the **calling program** or **batch**. It does **not** return data like a `SELECT` or `OUTPUT` parameter — instead, it's usually used to indicate **success**, **failure**, or a **status code**.



## What is a Table-Valued Parameter (TVP)?

A **TVP** allows you to pass a **table (rows and columns)** as a parameter to a stored procedure or function.

```sql
CREATE TYPE EmployeeTableType AS TABLE
(
    EmployeeID INT,
    Name NVARCHAR(50),
    DepartmentID INT
);


CREATE PROCEDURE SP_AddEmployees
    @EmployeeList EmployeeTableType READONLY
AS
BEGIN
    INSERT INTO Employees (EmployeeID, Name, DepartmentID)
    SELECT EmployeeID, Name, DepartmentID
    FROM @EmployeeList;
END

```
🔸 Note: TVPs must be marked as `READONLY`.

## ⚠️ Gotchas

- TVPs must be **`READONLY`** – you can’t update them in the procedure
    
- You need to **match the column names and types exactly**
    
- Not supported in all versions — make sure you're using **SQL Server 2008+**

```csharp
DataTable employeeTable = new DataTable();
        employeeTable.Columns.Add("EmployeeID", typeof(int));
        employeeTable.Columns.Add("Name", typeof(string));
        employeeTable.Columns.Add("DepartmentID", typeof(int));

        // Add rows
        employeeTable.Rows.Add(1, "Alice", 10);
        employeeTable.Rows.Add(2, "Bob", 20);

		// Call SP
```

## Returning Multiple Result Sets(ADO.NET):

```sql
CREATE PROCEDURE GetEmployeeData
AS
BEGIN
    SELECT * FROM Employees;         -- First result set
    SELECT * FROM Departments;       -- Second result set
END
```


```csharp
SqlDataReader reader = cmd.ExecuteReader();

// First result set
while (reader.Read()) {
    // Read employees
}

reader.NextResult(); // Move to second result set

while (reader.Read()) {
    // Read departments
}
```

## Use `sp_executesql` with Parameters (if you _must_ use dynamic SQL)

If dynamic SQL is necessary (e.g., dynamic column/table names), **use `sp_executesql` with parameters**, not string concatenation.

### ✅ Example:

```sql
DECLARE @sql NVARCHAR(MAX);

SET @sql = N'SELECT * FROM Users WHERE Name = @name';

EXEC sp_executesql @sql, N'@name NVARCHAR(100)', @name = @SearchText
```

> EXEC sp_helptext 'SP_NAME'


```sql
CREATE PROCEDURE GenerateDynamicSQL1
    @TableName NVARCHAR(128)
AS
BEGIN
    DECLARE @SQL NVARCHAR(MAX);
    SET @SQL = 'SELECT * FROM ' + @TableName;
    EXECUTE(@SQL);
END

CREATE PROCEDURE GenerateDynamicSQL2
    @TableName NVARCHAR(128)
AS
BEGIN
    DECLARE @SQL NVARCHAR(MAX);
    SET @SQL = N'SELECT * FROM ' + QUOTENAME(@TableName);
    EXEC sp_executesql @SQL;
END

USE [C21_DB1]
GO

DECLARE @RC int
DECLARE @TableName nvarchar(128)

-- TODO: Set parameter values here.

set @TableName='Students';

EXECUTE @RC = [dbo].[GenerateDynamicSQL1] 
   @TableName


EXECUTE @RC = [dbo].[GenerateDynamicSQL2] 
   @TableName

GO
```

`QUOTENAME()` is the built-in SQL Server function that **safely escapes object names** like table names, column names, or schemas.

## 🚫 Why You Should Avoid `sp_` Prefix

### 🔴 1. **SQL Server Looks in `master` First**

When a procedure name starts with `sp_`, SQL Server:

- **First searches the `master` database** for a system stored procedure
    
- Then looks in the current database if not found
    

This causes:

- **Unnecessary overhead**
    
- **Slower execution time**
    
- Even possible **wrong procedure** execution (if names conflict)
    

---

### 🔴 2. **Conflicts with System Procedures**

`sp_` is reserved for **system stored procedures**, like:


SP is different than Function in T-SQL, Functions can be called in query but SP cannot be called separately using Exec command.
