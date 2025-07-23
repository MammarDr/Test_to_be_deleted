> TSQL is programing language, extended from SQL

T-SQL, or Transact-SQL, is an extension of SQL (Structured Query Language) developed by Microsoft. It's used primarily in Microsoft SQL Server and Azure SQL Database. T-SQL not only supports the standard SQL commands for database interaction but also introduces several additional features tailored to Microsoft's database platforms.

- Transact-SQL (T-SQL) is a programming language used to manage and manipulate relational databases. It is a proprietary language developed by Microsoft and is the primary language used for programming Microsoft SQL Server.  
    
- T-SQL is an extension of the SQL (Structured Query Language) standard and adds additional functionality and control over data and database objects. It supports a wide range of operations including data definition, data manipulation, data control, and data query.  
    
- T-SQL also supports programming constructs such as variables, loops, and conditional statements, making it a powerful tool for database programming and management. Additionally, T-SQL has built-in functions for performing tasks such as string manipulation, mathematical operations, and date and time calculations.  
    
- T-SQL allows the creation of stored procedures and functions, which can improve code reusability, enhance security, and provide better performance. Stored procedures can be precompiled, leading to faster execution, and they can be called from various applications.  
    
- T-SQL supports explicit transaction control using keywords like BEGIN TRANSACTION, COMMIT, and ROLLBACK. This helps ensure data integrity by allowing developers to define and manage transaction boundaries explicitly.  
    
- T-SQL provides robust error-handling mechanisms, allowing developers to catch and handle errors gracefully. TRY...CATCH blocks can be used to encapsulate code where errors may occur, making it easier to identify and address issues.  
    
- T-SQL supports both DML and DDL operations, allowing developers to not only query and manipulate data but also define and modify the structure of the database. This comprehensive support streamlines the development and maintenance of database applications.  
    
- T-SQL provides a range of security features, including the ability to define user roles, grant permissions, and implement encryption. This ensures that data is accessed and modified only by authorized users, contributing to a secure database environment.  
    

Overall T-SQL is a powerful and versatile language that is widely used in enterprise environments for managing and manipulating relational databases.

In Oracle there is **PL/SQL**

**T-SQL** (**T**ransact-**SQL**) and **PL/SQL** (**P**rocedural **L**anguage/**SQL**) are both extensions of SQL, the standard language for interacting with relational databases. While they share some similarities, they are distinct in several ways, primarily because they are designed for use with different database management systems.

### Database Compatibility

- T-SQL: Primarily used with **Microsoft SQL Server**.
- PL/SQL: Developed by Oracle Corporation for use with **Oracle Database**.


```sql
-- Comment

--- Declare Variables ---
DECLARE @DepartmentID INT;
DECLARE @StartDate DATE, @EndDate DATE;
DECLARE @EndDate DATE = '2023-12-31';
DECLARE @TotalEmployees INT, @DepartmentName VARCHAR(50);


--- Initialize Variables ---
SET @DepartmentID = 3;
SET @DepartmentName = 'IT'
SET @StartDate = '2023-01-01'


--- Retrieve Data ---
SELECT @DepartmentName = Name FROM Departments
WHERE DepartmentID = @DepartmentID;

--- Calculate --
SELECT @TotalEmployees = COUNT(*) FROM Employees
WHERE DepartmentID = @DepartmentID
AND HireDate BETWEEN @StartDate AND @EndDate;

--- Print --
PRINT 'Department Report';
PRINT 'Department Name: ' + @DepartmentName;
PRINT 'Reporting Period: ' + CAST(@StartDate AS VARCHAR) + ' to ' + CAST(@EndDate AS VARCHAR);
PRINT 'Total Employees Hired in ' + CAST(YEAR(@StartDate) AS VARCHAR) + ': ' + CAST(@TotalEmployees AS VARCHAR);
```
