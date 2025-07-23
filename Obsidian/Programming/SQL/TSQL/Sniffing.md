
## 🔍 **What is Parameter Sniffing?**

**Parameter sniffing** happens when:

1. You execute a **stored procedure** with **specific parameter values**
    
2. SQL Server **"sniffs"** those values and creates an **execution plan** optimized for them
    
3. It **reuses** that plan for future executions — even if the new parameter values are very different
    

The problem?  
🔸That **original plan might not be good** for all parameter values, especially if the data is skewed.

---

## 📦 Real-World Example

Suppose you have this procedure:
```sql
CREATE PROCEDURE GetEmployeesByDept
    @DeptID INT
AS
BEGIN
    SELECT * FROM Employees WHERE DepartmentID = @DeptID;
END

```

Now assume:

- `DepartmentID = 5` has **100,000 rows**
    
- `DepartmentID = 2` has **only 3 rows**
    

---

### ✅ First Execution:

You run:

```sql
EXEC GetEmployeesByDept @DeptID = 2
```

SQL Server builds a plan assuming the result will be **small** (because it saw `2`).

✅ The plan might use a **nested loop** — efficient for small result sets.

---

### 🔁 Second Execution:

Now someone runs:

```sql
EXEC GetEmployeesByDept @DeptID = 5
```

❌ SQL Server **reuses the old plan** (optimized for 3 rows)  
But this time, it returns **100,000 rows** — and that **nested loop is terribly slow**.

👉 The plan is no longer appropriate — this is the **bad effect of parameter sniffing**.

---

## 📌 Summary:

|Aspect|Explanation|
|---|---|
|What is sniffed?|SQL Server “sniffs” (reads) parameter values during **compilation**|
|When it happens?|On **first execution** (or after cache eviction)|
|Why is it a problem?|One plan is reused for all values, even when values vary a lot|

---

## 🛠️ Fixes / Workarounds:

|Method|Description|
|---|---|
|**Option Recompile**|Add `OPTION (RECOMPILE)` so SQL Server compiles a new plan each time|
|**Local variables trick**|Assign param to a local variable, which prevents sniffing|
|**Optimize for hint**|Use `OPTIMIZE FOR` to control which value SQL optimizes for|
|**Plan guides / Rewriting proc**|More advanced; control or separate plans manually|

---

### 🔧 Example Fix — Option Recompile

```sql
SELECT * FROM Employees WHERE DepartmentID = @DeptID OPTION (RECOMPILE);
```

⚠️ Use with care — this trades CPU for flexibility (because it recompiles each time).

---

## ✅ TL;DR

> **Parameter sniffing** is when SQL Server builds a query plan based on the **first parameter values** it sees — and that plan may be **reused badly** for later inputs.

It’s not a bug — it’s a **performance optimization** that backfires when data is skewed.