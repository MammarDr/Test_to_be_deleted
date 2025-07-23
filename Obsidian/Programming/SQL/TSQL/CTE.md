## 📘 What are CTEs?

A **Common Table Expression (CTE)** in T-SQL is a **temporary result set** that you can reference within a `SELECT`, `INSERT`, `UPDATE`, or `DELETE` statement.

### 🔍 Key Characteristics:

- Defined using the `WITH` keyword.
    
- Exists only during the **execution of the query**.
    
- Improves **query readability** and helps break down complex logic.
    

---

## 🔄 CTEs vs Subqueries

|Feature|CTEs|Subqueries|
|---|---|---|
|**Readability**|Easier to read and structure|Can become nested and hard to follow|
|**Reusability**|Can be referenced multiple times in a query|Typically used once per query|
|**Syntax**|Defined at the beginning of the query|Embedded within clauses (e.g., WHERE)|

---

## 🆚 CTEs vs Temporary Tables

|Feature|CTEs|Temporary Tables|
|---|---|---|
|**Persistence**|Exists only during the query execution|Lives in tempdb for the session or scope|
|**Storage**|Not stored as an object|Stored as a physical object in tempdb|
|**Use Case**|Ideal for short-term, in-query operations|Better for complex operations or reuse|

---

## ✅ Best Practices for Using CTEs

1. **Readability**
    
    - Use CTEs to simplify long or nested queries for easier understanding.
        
2. **Performance Awareness**
    
    - While CTEs can make queries cleaner, they might not always be the most efficient — especially with **large datasets**.
        
3. **Recursive CTEs**
    
    - When using recursive CTEs, ensure proper **termination conditions** to avoid infinite loops.
        
    - SQL Server enforces a default recursion limit (usually **100**) which can be changed using `OPTION (MAXRECURSION N)`.

```sql
WITH DepartmentSales AS
(
	SELECT Name, SUM(Sales) AS TotalSales
	FROM Departments
	GROUP BY Name
) 
SELECT Name, TotalSales FROM DepartmentSales:
```


## 🔄 Recursion

```sql
WITH Numbers AS (
    SELECT 1 AS Number
    UNION ALL
    SELECT Number + 1 FROM Numbers WHERE Number < 1000
)
SELECT * FROM Numbers
OPTION (MAXRECURSION 1000);
```

Let's clarify why this recursive logic **works inside a CTE**, but **won’t work as-is in a normal stored procedure or batch**.

---

## 🔁 Why It Works in a CTE:

The line:

```sql
SELECT Number + 1 FROM Numbers WHERE Number < 10
```



works because it’s part of a **recursive CTE**, which is a **special construct in T-SQL** designed to:

1. **Reference itself** (`Numbers` refers to `Numbers`)
    
2. **Repeat** execution until a stopping condition is met (`WHERE Number < 10`)
    
3. **Build up results** from each step
    

SQL Server understands recursive CTEs and manages the recursion **internally** — like a loop — with rules, stack control, and a recursion limit.

---

## 🚫 Why It Doesn’t Work in a Normal Procedure or Batch

In regular SQL code, like a stored procedure or a batch:

```sql
SELECT Number + 1 FROM Numbers WHERE Number < 10;
```

would **not work** because:

### 1. **CTEs allow self-reference — regular queries don’t**

- Outside a recursive CTE, a table or result set **cannot reference itself** in the same scope.
    
- You would get an error: _"Invalid object name 'Numbers'"_ or infinite loop logic.
    

### 2. **No built-in recursion mechanism**

- In procedural languages (like Python or C), you can call a function from itself.
    
- In SQL, you don’t have that kind of recursion **except inside recursive CTEs**.
    

### 3. **You’d need explicit loops in procedures**

To simulate the same effect in a procedure, you'd need to use a `WHILE` loop or a recursive procedure.


### Ex #2: 

```sql
-- Anchor
SELECT EmployeeID, ManagerID, Name,
       CAST(Name AS VARCHAR(MAX)) AS Hierarchy,
       0 AS Level
FROM Employees
WHERE ManagerID IS NULL

UNION ALL

-- Recursion
SELECT e.EmployeeID, e.ManagerID, e.Name,
       CAST(eth.Hierarchy + ' -> ' + e.Name AS VARCHAR(MAX)),
       eth.Level + 1
FROM Employees e
JOIN EmployeeTreeHierarchy eth ON e.ManagerID = eth.EmployeeID
```

What happens if Peter (a subordinate of David) appears first in the table — before David — during recursion?

## 🔁 Recursive CTEs Do Not Care About Table Order

### ✅ Key point:

> Recursive CTEs **do not process rows based on their order in the underlying table** — they process **hierarchically**, not sequentially.

---

### 📘 Let’s say your table `Employees7` has:

|EmployeeID|Name|ManagerID|
|---|---|---|
|3|Peter|2|
|2|David|1|
|1|Alice|NULL|

Even though **Peter** is physically listed before **David**, the recursive CTE will **not** process Peter **until** his manager (David) has been processed, because:


```sql
INNER JOIN EmployeeTreeHierarchy ETH ON e.ManagerID = ETH.EmployeeID
```

means:

- SQL Server starts from the **anchor** (Alice with `ManagerID IS NULL`).
    
- Then finds employees where `ManagerID = Alice's EmployeeID (1)` → **David**.
    
- Then finds `ManagerID = David's EmployeeID (2)` → **Peter**.

## 🎯 So yes:

> It's like a graph traversal (recursive walk). If a new node (e.g., Peter) connects to a node not yet in the path (e.g., David), **SQL Server waits** until the parent node is reached — then picks up Peter.

And when it's done adding Peter, **it returns to previous levels** to check for more children. Exactly like **graph recursion**.



### Ex #3 :

```sql
DECLARE @StartDate DATE = '2023-01-01'; -- Start of the date range
DECLARE @EndDate DATE = '2023-01-31';   -- End of the date range


WITH DateSeries AS (
    -- Anchor member: Start with the initial date
    SELECT @StartDate AS DateValue


    UNION ALL


    -- Recursive member: Add one day in each iteration
    SELECT DATEADD(day, 1, DateValue)
    FROM DateSeries
    WHERE DateValue < @EndDate
)
SELECT DateValue
FROM DateSeries;
```

## Ex #4 :

```sql
WITH DuplicateEmails AS (
    SELECT 
        Email, 
        COUNT(*) AS DuplicateEmail
    FROM Contacts
    GROUP BY Email
    HAVING COUNT(*) > 1
)
SELECT c.ContactID, c.Name, c.Email
FROM Contacts c
INNER JOIN DuplicateEmails de ON c.Email = de.Email;
```

## Ex #5 :

```sql
WITH SalesTotals AS (
    SELECT 
        EmployeeID, 
        SUM(SaleAmount) AS TotalSales
    FROM SalesRecords
    GROUP BY EmployeeID
), RankedSales AS (
    SELECT
        EmployeeID,
        TotalSales,
        RANK() OVER (ORDER BY TotalSales DESC) AS SalesRank
    FROM SalesTotals
)
SELECT EmployeeID, TotalSales, SalesRank
FROM RankedSales;
```