
In SQL, `GO` **is not a SQL command itself** — it's a **batch separator** used by certain tools like:

- **SQL Server Management Studio (SSMS)**
    
- **sqlcmd**
    
- **Azure Data Studio**
    

### 🔧 What it does:

`GO` tells the tool to **send the current batch of SQL statements** to the server for execution.

### Key Notes:

- `GO` is **not part of the SQL language**; it's a **command recognized by the client tool**, not the database engine.
    
- You **can't use `GO` inside stored procedures**, functions, or scripts run through tools that don’t recognize it.
    
- Each batch is compiled separately. So, **variables declared before `GO` won't exist after it.**


Example of variable scope loss:
```sql
DECLARE @x INT = 5;
GO
PRINT @x; 
-- Error: Must declare the scalar variable "@x"
```

### Visual Analogy:

Think of a batch like a sealed envelope sent to SQL Server.
✅ Both are in the same envelope → it works.
❌ Now they're in different envelopes → `@x` is undefined in the second batch.