
### Built-In Functions:


#### Strings

```sql
--Common String Functions...

-- Using the LEN function to get the length of each employee's name
SELECT LEN(Name) AS NameLength FROM dbo.Employees2
GO

-- Converting employee names to uppercase using the UPPER function
SELECT UPPER(Name) AS UpperCaseName FROM dbo.Employees2
GO

-- Converting employee names to lowercase using the LOWER function
SELECT LOWER(Name) AS UpperCaseName FROM dbo.Employees2
GO

-- Extracting the first three characters of each name using SUBSTRING
SELECT SUBSTRING(Name, 1, 3) AS NameSubstring FROM dbo.Employees2
GO

-- Finding the position of '0' in each name using CHARINDEX
SELECT CHARINDEX('o', Name) AS CharPosition FROM dbo.Employees2
GO

-- Replacing 'Sales' with 'Marketing' in department names using REPLACE
SELECT REPLACE(Department, 'Sales', 'Marketing') AS NewDepartment FROM dbo.Employees2
GO

-- Concatenating the name and department with a hyphen in between using CONCAT
SELECT CONCAT(Name, ' - ', Department) AS ConcatenatedString FROM dbo.Employees2
GO

-- Practice Exercise: Format Name and Department in a specific format using CONCAT, UPPER, and LOWER
-- Objective: Format the Name and Department columns as a single string, where names are in uppercase, and department names are in lowercase, separated by a colon (:)
SELECT CONCAT(UPPER(Name), ' : ', LOWER(Department)) AS FormattedOutput FROM dbo.Employees2
GO

-- Extracting the first 3 characters from the left side of the employee's name using LEFT
SELECT LEFT(Name, 3) AS LeftSubstring FROM dbo.Employees2
GO

-- Extracting the last 3 characters from the right side of the employee's name using RIGHT
SELECT RIGHT(Name, 3) AS RightSubstring FROM dbo.Employees2
GO

-- Removing leading spaces from the employee's name using LTRIM
SELECT LTRIM(Name) AS NameWithNoLeadingSpaces FROM dbo.Employees2
GO

-- Removing trailing spaces from the employee's name using RTRIM
SELECT RTRIM(Name) AS NameWithNoTrailingSpaces FROM dbo.Employees2
GO


-- Removing spaces from the start and end of each name using LTRIM and RTRIM
SELECT LTRIM(RTRIM(Name)) AS TrimmedName FROM dbo.Employees2
GO

-- Removing both leading and trailing spaces from the employee's name using TRIM
SELECT TRIM(Name) AS NameWithNoSpaces FROM dbo.Employees2
GO
```

[More](https://learn.microsoft.com/en-us/sql/t-sql/functions/string-functions-transact-sql?view=sql-server-ver16)


#### Date

```sql
-- Getting the current system date and time
SELECT GETDATE() AS CurrentDateTime
GO

-- Getting the system date and time with fractional seconds and time zone offset
SELECT SYSDATETIME() AS SystemDateTime
GO

-- Adding 10 days to the current date
SELECT DATEADD(day, 10, GETDATE()) AS DatePlus10Days
GO

-- Calculating the difference in days between two dates
SELECT DATEDIFF(day, '2023-01-01', GETDATE()) AS DaysSinceStartOfYear
GO

-- Extracting the year part from the current date
SELECT DATEPART(year, GETDATE()) AS CurrentYear
GO

-- Getting the name of the current month
SELECT DATENAME(month, GETDATE()) AS CurrentMonthName
GO

-- Extracting the day from the current date
SELECT DAY(GETDATE()) AS CurrentDay
GO

-- Extracting the month from the current date
SELECT MONTH(GETDATE()) AS CurrentMonth
GO

-- Extracting the year from the current date
SELECT YEAR(GETDATE()) AS CurrentYear
GO

-- Converting a datetime to a different format,The third argument, 103, specifies the style code for the conversion. 
--In SQL Server, style code 103 represents the date format as DD/MM/YYYY. 
--This means that the resulting string will have the day, then the month, and finally the year, separated by forward slashes.
SELECT CONVERT(varchar, GETDATE(), 103) AS DateInDDMMYYYY
GO

-- Casting a datetime to a different data type
SELECT CAST(GETDATE() AS date) AS DateOnly
GO

-- Getting the last day of the current month
SELECT EOMONTH(GETDATE()) AS LastDayOfCurrentMonth
GO

```

[More](https://learn.microsoft.com/en-us/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=sql-server-ver16)



### Aggregate Functions:

```sql
-- Description: Count the number of employees in each department
SELECT Department, COUNT(*) AS EmployeeCount
FROM Employees2
GROUP BY Department;

-- Description: Calculate the total salary for each department
SELECT Department, SUM(Salary) AS TotalSalary
FROM Employees2
GROUP BY Department;

-- Description: Calculate the average performance rating for each department
SELECT Department, AVG(PerformanceRating) AS AvgPerformanceRating
FROM Employees2
GROUP BY Department;

-- Description: Find the lowest salary in the company
SELECT MIN(Salary) AS LowestSalary
FROM Employees2;

-- Description: Find the highest salary in the company
SELECT MAX(Salary) AS HighestSalary
FROM Employees2;

```



### What is Window Functions:

Windows functions (also known as windowed or analytic functions) in T-SQL allow you to perform calculations across a set of rows related to the current row within a result set.

**Types of Window Functions in T-SQL**

- Aggregate Functions: Used to perform calculations over a range of values. Examples include `SUM()`, `AVG()`, `COUNT()`, `MAX()`, `MIN()`.
- Ranking Functions: Used to assign a ranking or a row number to each row in a partition. Examples include `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `NTILE()`.
- Analytic Functions: Used for advanced analytical operations like moving averages or cumulative totals. Examples include `LEAD()`, `LAG()`, `FIRST_VALUE()`, `LAST_VALUE()`.

```sql

-- It assigns a unique row number (`RowNum`) to each row based on the order of the `Grade` column in **descending order** (from highest to lowest).
SELECT 
    StudentID, 
    Name, 
    Subject, 
    Grade,
    ROW_NUMBER() OVER (ORDER BY Grade DESC) AS RowNum
FROM 
    Students
-- 88 - 1 num
-- 88 - 2 num

SELECT 
    StudentID, 
    Name, 
    Subject, 
    Grade,
    RANK() OVER (ORDER BY Grade DESC) AS GradeRank
FROM 
    Students;
-- 88 - 1 rank
-- 88 - 1 rank
-- 87 - 3 rank ISSUE


SELECT 
    StudentID, 
    Name, 
    Grade,
    DENSE_RANK() OVER (ORDER BY Grade DESC) AS GradeRank
FROM 
    Students;
-- 88 - 1 rank
-- 88 - 1 rank
-- 87 - 2 rank FIXED



SELECT 
    StudentID, 
    Name, 
    Subject, 
    Grade,
    RANK() OVER (PARTITION BY Subject ORDER BY Grade DESC) AS GradeRank
FROM 
    Students;
-- English - 100 - 1
-- English - 77  - 2
-- Math    - 87  - 1


SELECT 
    StudentID, 
    Name, 
    Subject, 
    Grade,
    AVG(Grade) OVER (PARTITION BY Subject) AS SubjectAvgGrade,
    SUM(Grade) OVER (PARTITION BY Subject) AS SubjectTotalGrade
FROM 
    Students
ORDER BY Subject;
-- --
SELECT 
	Subject,
    AVG(Grade) AS SubjectAvgGrade,
    SUM(Grade) AS SubjectTotalGrade
FROM 
    Students
GROUP BY Subject
ORDER BY Subject;


SELECT 
    StudentID, 
    Name,
    LAG(Grade, 1) OVER (ORDER BY Grade DESC) AS PreviousGrade,
    Grade,
    LEAD(Grade, 1) OVER (ORDER BY Grade DESC) AS NextGrade
FROM 
    Students
ORDER BY Grade DESC;


```

**Understanding the Output**

Consider this set of grades: [95, 95, 90, 85, 85, 85, 80]

- Using `RANK()`, the ranks would be [1, 1, 3, 4, 4, 4, 7].
- Using `DENSE_RANK()`, the ranks would be [1, 1, 2, 3, 3, 3, 4].

```sql
DECLARE @PageNumber AS INT, @RowsPerPage AS INT;
SET @PageNumber = 2;  -- Set to the second page
SET @RowsPerPage = 3; -- Displaying 3 rows per page

SELECT StudentID, Name, Subject, Grade
FROM Students
ORDER BY StudentID
OFFSET (@PageNumber - 1) * @RowsPerPage ROWS -- Escape First 3 ROW
FETCH NEXT @RowsPerPage ROWS ONLY;           -- Fetch 3 ROWS
```


### Scalar Function

**scalar function** returns a **single value** (like `int`, `varchar`, `datetime`, etc.) — not a table.

```sql
CREATE FUNCTION dbo.GetAverageGrade(@subject NVARCHAR(50), @test INT)
RETURNS INT
AS
BEGIN
    DECLARE @averageGrade INT;


    SELECT @averageGrade = AVG(Grade)
    FROM Students
    WHERE Subject = @subject;


    RETURN @averageGrade;
END;


SELECT Name, dbo.GetAverageGrade(Subject, 0) AS AverageGrade
FROM Teachers;
```

## ⚠️ Notes

- **Must return exactly one value**
    
- Can be used in `SELECT`, `WHERE`, `JOIN`, etc.
    
- Cannot modify data (no `INSERT`, `UPDATE`, etc.)
    
- Use `SCHEMABINDING` if you want to prevent changes to the referenced objects


### Table-Valued Functions

Table-Valued Functions (TVFs) are user-defined functions in T-SQL that return tabular data as a result. They are a powerful feature in SQL Server that can be used to encapsulate complex queries and logic into reusable components. In this lesson,

**There are two types of TVFs in T-SQL:**

1. **Inline Table-Valued Functions (ITVFs):** These functions return a table variable inline and can be used in the FROM clause of a SELECT statement.
2. **Multi-Statement Table-Valued Functions (MTVFs):** These functions return a table variable using a series of SQL statements and can have multiple SELECT statements within them.

##### **inline Table-Valued Functions (ITVFs)**

```sql
CREATE FUNCTION dbo.GetStudentsBySubject
(
    @Subject NVARCHAR(50)
)
RETURNS TABLE
AS
RETURN
(
    SELECT *
    FROM Students
    WHERE Subject = @Subject
)
-- Body only accept the SELECT

-- Example : Join Students and Teachers based on shared subject
SELECT s.StudentID, s.Name AS StudentName, t.Name AS TeacherName, s.Grade
FROM dbo.GetStudentsBySubject('Math') s
JOIN Teachers t ON s.Subject = t.Subject
```

You cannot use UPDATE or INSERT statements in Inline Table-Valued Functions.
ITVFs are designed to be read-only, and they return a table variable that is essentially a result of a single SELECT statement. Since these functions are read-only, they cannot modify the data in the database, which means you cannot perform data manipulation operations like INSERT, UPDATE, DELETE, or MERGE within them.


##### **Multi-Statement Table-Valued Functions (MTVFs)**

Multi-Statement Table-Valued Functions (MTVFs) offer a powerful way to encapsulate complex logic and return tabular data. MTVFs can be used to perform multiple SQL statements, manipulate data, and return the result as a table.

```sql
CREATE FUNCTION dbo.GetTopPerformingStudents()
RETURNS @Result TABLE (
    StudentID INT,
    Name NVARCHAR(50),
    Subject NVARCHAR(50),
    Grade INT
)
AS
BEGIN
    INSERT INTO @Result (StudentID, Name, Subject, Grade)
    SELECT TOP 3 StudentID, Name, Subject, Grade
    FROM Students
    ORDER BY Grade DESC;

    RETURN;
END;
```