
#### Declaring Variables
• Syntax: DECLARE @VariableName DataType;
• Example: DECLARE @EmployeeName VARCHAR(50);
• Here, @EmployeeName is a variable of the type VARCHAR (a string data type) with a maximum length of 50 characters


#### Assigning Values to Variables
You can set a value to a declared variable using the SET or SELECT statement.
• Using SET: SET @EmployeeName = 'John Doe’;
• Using SELECT: SELECT @EmployeeName = 'John Doe';

#### Using Variables
• Once declared and assigned, you can use variables in your T-SQL code wherever you might use literals or expressions.
• Example in a query: SELECT * FROM Employees WHERE Name = @EmployeeName;

#### Variable Scope
• T-SQL variables are local to the batch, stored procedure, or trigger in which they are declared.
• They cease to exist once the batch or procedure completes.


#### Special Variables
• @@IDENTITY: Contains the last-inserted identity value.
• @@ROWCOUNT: Contains the number of rows affected by the last statement.



### IF Statement in T-SQL

The IF statement in T-SQL is a control-of-flow language construct that allows you to execute or skip a statement block based on a specified condition. It is akin to "if-then" logic found in many programming languages.

#### Syntax of IF Statement

```sql
IF condition
BEGIN
    -- Statements to execute if the condition is true
END -- Batch
ELSE
BEGIN
    -- Statements to execute if the condition is false
END
```

- `condition`: A boolean expression that evaluates to true or false.
- `BEGIN` and `END`: Define the start and end of a block of statements.

#### Simple IF Statement
```sql
Declare @a int, @b int;
set @a = 20;
set @b=10;

IF (@a > @b and @a > 10) or @a = @b or NOT(@a < 1)
	BEGIN
		PRINT 'Yes A is greater than B'
	END

IF @@Error <> 0
BEGIN
		PRINT 'Error!'
END

IF EXISTS (SELECT * FROM Employees WHERE Name = 'John Smith')
```
> not equal : <> |  != 

### Limitations and Best Practices
1. Immediate Check: Always check `@@ERROR` immediately after the statement you're interested in, because its value is reset after each SQL statement.

2. Superseded by TRY...CATCH: In modern T-SQL programming, `@@ERROR` is often replaced by the more robust TRY...CATCH construct, which offers better error handling capabilities.

3. Not Always Reliable: `@@ERROR` might not catch all types of errors, especially those that are severe enough to terminate the connection.

4. Use in Transactions: In transaction processing, use `@@ERROR` to decide whether to commit or roll back a transaction.



##### In T-SQL, the `CASE` statement is primarily used within the context of queries, such as `SELECT`, `UPDATE`, `INSERT`, and `DELETE` statements. It is not used as a standalone control-of-flow structure like `IF` or `WHILE`

#### Syntax of Simple CASE (SWITCH Equivalent)

```sql
CASE input_expression
    WHEN expression1 THEN result1
    WHEN expression2 THEN result2
    ...
    ELSE default_result
END

SELECT 
    EmployeeID,
    CASE DepartmentID
        WHEN 1 THEN 'Engineering'
        WHEN 2 THEN 'Human Resources'
        WHEN 3 THEN 'Sales'
        ELSE 'Other'
    END AS DepartmentName
FROM Employees;
```
 > input_expression: The expression to compare against the `WHEN` expressions.

#### Syntax of Searched CASE

```sql
CASE
    WHEN boolean_expression1 THEN result1
    WHEN boolean_expression2 THEN result2
    ...
    ELSE default_result
END

SELECT 
    EmployeeID,
    CASE 
        WHEN Salary <= 30000 THEN 'Entry Level'
        WHEN Salary BETWEEN 30001 AND 60000 THEN 'Mid Level'
        WHEN Salary > 60000 THEN 'Senior Level'
        ELSE 'Not Specified'
    END AS EmployeeLevel
FROM Employees;
```
>  Each `WHEN` clause contains a Boolean expression.


#### Using CASE in ORDER BY (Custom Sorting)

```sql
SELECT Name, Salary
FROM Employees
ORDER BY 
    CASE 
        WHEN Salary > 50000 THEN 1
        ELSE 2
    END, SaleAmount DESC;
    
```

#### CASE in UPDATE Statements (Conditional Data Modification)

```sql
UPDATE Employees2
SET Salary = 
    CASE 
        WHEN PerformanceRating > 90 THEN Salary * 1.15
        WHEN PerformanceRating BETWEEN 75 AND 90 THEN Salary * 1.10
		WHEN PerformanceRating BETWEEN 50 AND 74 THEN Salary * 1.05
        ELSE Salary
    END;
```

#### Nested `CASE` statements:

```sql
SELECT
    Name,
    Department,
    Salary,
    PerformanceRating,
    Bonus = CASE 
           WHEN Department = 'Sales' THEN
           WHEN Department = 'HR' THEN
               CASE 
                   WHEN PerformanceRating > 90 THEN Salary * 0.10
                   WHEN PerformanceRating BETWEEN 75 AND 90 THEN Salary * 0.08
                   ELSE Salary * 0.04
               END
           ELSE
               CASE 
                   WHEN PerformanceRating > 90 THEN Salary * 0.08
                   WHEN PerformanceRating BETWEEN 75 AND 90 THEN Salary * 0.06
                   ELSE Salary * 0.03
               END
            END
FROM Employees2;
```

#### Complicated `CASE` statements:

```sql
SELECT
    PerformanceCategory,
    COUNT(*) AS NumberOfEmployees,
    AVG(Salary) AS AverageSalary
FROM
    (SELECT
        Name,
        Salary,
        CASE
            WHEN PerformanceRating >= 80 THEN 'High'
            WHEN PerformanceRating >= 60 THEN 'Medium'
            ELSE 'Low'
        END AS PerformanceCategory
    FROM Employees2) AS PerformanceTable
GROUP BY PerformanceCategory;
```


### While Loop :

``

```sql
DECLARE @Condition INT = 5;

WHILE @Condition
BEGIN
PRINT CAST(@Condition AS VARCHAR);
SET @Condition = @Condition - 1;
END
```


```sql
DECLARE @MaxEmployeeID INT = SELECT MAX(EmployeeID) FROM Employees;
DECLARE @MinEmployeeID INT = SELECT MIN(EmployeeID) FROM Employees;

WHILE @MinEmployeeID IS NOT NULL AND @MaxEmployeeID >= @MinEmployeeID
BEGIN
	SELECT Name FROM Employee WHERE EmployeeID = @MinEmployeeID;
	PRINT Name;
	
	SET @MinEmployeeID = SELECT MIN(EmployeeID) WHERE
								@MinEmployeeID < EmployeeID;
END
```

```sql
DECLARE @Balance DECIMAL(10, 2) = 950.00; -- Example balance
DECLARE @Withdrawal DECIMAL(10, 2) = 100.00; -- Withdrawal amount

--Loop with Conditional Exit
WHILE @Balance > 0
BEGIN
    -- Perform a withdrawal
    SET @Balance = @Balance - @Withdrawal;

    IF @Balance < 0
		BEGIN
			PRINT 'Insufficient funds for withdrawal.';
			BREAK;
		END
	ELSE
		BEGIN
			PRINT 'New Balance: ' + CAST(@Balance AS VARCHAR);
			CONTINUE;
		END

    
END
```

```sql
DECLARE @row INT = 1;
-- Example 4 - Nested While Loops - 10 x 10 Multiplication Table
DECLARE @col INT;
DECLARE @result INT;

WHILE @row <= 10
BEGIN
    SET @col = 1;
    WHILE @col <= 10
    BEGIN
        SET @result = @row * @col;
        PRINT CAST(@row AS VARCHAR) + ' x ' + CAST(@col AS VARCHAR) + ' = ' + CAST(@result AS VARCHAR);
        SET @col = @col + 1;
    END
    SET @row = @row + 1;
END
```


```sql
-- 10 x 10 Matrix Multiplication Table

DECLARE @row INT = 1;
DECLARE @col INT;
DECLARE @result INT;
DECLARE @rowString VARCHAR(255);
DECLARE @headerString VARCHAR(255);

-- Create the header row for the columns
SET @headerString = CHAR(9); -- Starting with a tab for the row header space
SET @col = 1;
WHILE @col <= 10
BEGIN
    SET @headerString = @headerString + CAST(@col AS VARCHAR) + CHAR(9); -- Append column headers
    SET @col = @col + 1;
END
PRINT @headerString;

-- Generate the multiplication table
WHILE @row <= 10
BEGIN
    SET @col = 1;
    SET @rowString = CAST(@row AS VARCHAR) + CHAR(9); -- Start each row with the row number

    WHILE @col <= 10
    BEGIN
        SET @result = @row * @col;
        SET @rowString = @rowString + CAST(@result AS VARCHAR) + CHAR(9); -- Append multiplication results
        SET @col = @col + 1;
    END

    PRINT @rowString; -- Print the row
    SET @row = @row + 1;
END


```


### **TRY...CATCH in T-SQL**

The primary mechanism for error handling in T-SQL is the `TRY...CATCH` construct.

- TRY Block: You place the T-SQL code that might cause an error inside a `TRY` block. If an error occurs, execution is passed to the associated `CATCH` block.
- CATCH Block: The `CATCH` block contains code that runs if an error occurs in the `TRY` block. It can log the error, roll back transactions, and take other appropriate actions.

#### **Syntax**
```sql
BEGIN TRY
    -- T-SQL statements that may cause an error
END TRY
BEGIN CATCH
    -- Error handling code
END CATCH
```


#### **Error Functions**

Within the `CATCH` block, you can use functions to get detailed error information:

- `ERROR_NUMBER()`: Returns the error number.
- `ERROR_SEVERITY()`: Returns the severity.
- `ERROR_STATE()`: Returns the error state number.
- `ERROR_PROCEDURE()`: Returns the name of the stored procedure or trigger where the error occurred. OR NULL
- `ERROR_LINE()`: Returns the line number where the error occurred.
- `ERROR_MESSAGE()`: Returns the complete text of the error message.

#### **Example: Using TRY...CATCH**

```sql
-- Assume we have a table called 'Employees' with a unique constraint on 'EmployeeID'
CREATE TABLE Employees3 (
    EmployeeID INT PRIMARY KEY,
    Name NVARCHAR(100),
    Position NVARCHAR(100)
);


BEGIN TRY
    -- Insert a record into the Employees table
    INSERT INTO Employees3 (EmployeeID, Name, Position) VALUES (1, 'John Doe', 'Sales Manager');
    
    -- Attempt to insert a duplicate record which will cause an error
    INSERT INTO Employees3 (EmployeeID, Name, Position) VALUES (1, 'Jane Smith', 'Marketing Manager');

	IF 1 = 1
	BEGIN
		throw 5100, 'Custom error exception', 1;
	END
	
END TRY
BEGIN CATCH
    -- Handle the error
    PRINT 'An error occurred: ' + ERROR_MESSAGE();
    -- Rollback the transaction if any
END CATCH
```

> THROW [ error_number, message, state ];

- error_number: A constant or variable between 50,000 and 2,147,483,647.
- message: The error message text. It should be a string less than 2048 characters.
- state: A constant or variable between 0 and 255.


#### @@ROWCOUNT 
```sql
UPDATE Employees SET DepartmentID = 3 WHERE DepartmentID =4;

SELECT @@ROWCOUNT AS RowsAffected;
```


#### Table Variables
• Table variables in T-SQL are used to store a set of records temporarily, they have some distinct characteristics and are suitable for different scenarios.


**Advantages**
• Performance: For small datasets, table variables can be faster since they are stored in memory and not written to disk.
• Transaction Log: Operations on table variables generate fewer log records. This can be beneficial in terms of performance.
• Scope: The scope of a table variable is limited to the batch, stored procedure, or function in which it is defined. This can simplify transaction management and error handling.

**Limitations of Table Variables**
• Indexing: By default, you can only create a primary key index at the time of declaration. Additional indexing options are limited.
• Statistics: Lack of statistics can lead to suboptimal query plans for large data sets.

```sql
DECLARE @EmployeeTable TABLE (
	ID INT,
	NAME VARCHAR(30),
	Department VARCHAR(60)
);

INSERT INTO @EmployeesTable (EmployeeId, Name, Department)
	VALUES (10, 'Mohammed', 'Marketing');

INSERT INTO @EmployeesTable (EmployeeId, Name, Department)
	VALUES (11, 'Ali', 'Sales');


SELECT * FROM @EmployeesTable WHERE Department = 'Sales';

	-- No need to drop the table variable
	-- The table variable @EmployeesTable automatically goes out of scope and is deallocated
	-- at the end of the execution of the batch, stored procedure, or function
```

>Changes are not logged, can't be rolled back



#### Temporary Tables

• Temporary tables in T-SQL are used to store and process intermediate results.
• These tables are created in the tempdb database and are automatically deleted when they are no longer used.
• Temporary tables are particularly useful in complex SQL operations where intermediate results need to be stored temporarily.

**Advantages**
• Performance: Can improve performance in complex queries by breaking them into simpler parts.
• Complex Data Processing: Useful for storing Intermediate results in complex data processing.
• Transaction Management: Changes in a temporary table are not logged extensively, which can be beneficial in large transactions.


**Types**
• Local Temporary Tables: Created with a single hash (#) symbol. Visible only to the connection that creates it and are deleted when the connection is closed.
• Syntax: CREATE TABLE **#TempTable (...)**
• Global Temporary Tables: Created with a double hash (##) symbol. Visible to all connections and are deleted when the last connection using it is closed.
• Syntax: CREATE TABLE **##TempTable (...)**


```sql
-- Create a local temporary table named #EmployeesTemp
	-- This table will be stored in the tempdb database and is visible only to this session
	CREATE TABLE #EmployeesTemp (
		EmployeeId INT,
		Name VARCHAR(100),
		Department VARCHAR(50)
	);

	-- Insert a records into the #EmployeesTemp table
	INSERT INTO #EmployeesTemp (EmployeeId, Name, Department)
	VALUES (10, 'Mohammed', 'Marketing');

	INSERT INTO #EmployeesTemp (EmployeeId, Name, Department)
	VALUES (11, 'Ali', 'Sales');

	-- Query the table
	SELECT * FROM #EmployeesTemp WHERE Department = 'Sales';

	-- Drop (delete) the temporary table #EmployeesTemp
	-- This is a good practice to clean up, although the table would automatically be deleted
	-- when the session ends
	DROP TABLE #EmployeesTemp;

```


>Local temporary tables are deleted when the connection is closed, while global temporary tables are deleted when the last connection using them is closed



#### Temp Table vs Variable Table

- Lifetime:
    - Temporary Tables: Exist until they are explicitly dropped using the `DROP TABLE` command or until the session/connection that created them is closed.
    - Table Variables: Automatically cleaned up at the end of the batch, function, or stored procedure in which they are defined.
    
- Performance and Usage:
    - Temporary Tables: Suitable for larger datasets and complex operations, like joining with other tables. They support indexes, statistics, and can result in better query performance for large data sets.
    - Table Variables: Better for smaller datasets and simpler operations. They have lower overhead but lack some of the optimizations available to temporary tables, like precompiled execution plans and statistics.

- Transaction Logs:
    - Temporary Tables: Fully logged in the transaction log, which can impact performance for large data manipulation operations.
    - Table Variables: Have minimal logging and do not participate in transactions. This means that if a transaction is rolled back, changes made to a table variable within that transaction are not rolled back.

- Use Cases:
    - Temporary Tables: Ideal for complex operations, temporary storage of data that requires rollback capabilities, and when working with a large number of rows.
    - Table Variables: Useful for quick, temporary storage of a small amount of data that does not require transactional rollbacks or heavy-duty operations.




Dynamic SQL Execution: Using `EXEC` or `sp_executesql` to execute dynamically built SQL strings