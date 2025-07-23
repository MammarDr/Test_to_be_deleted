

## 🌀 What are Cursors in T-SQL?

A **cursor** is a database object used to **manipulate data row-by-row**, rather than all at once like typical SQL set-based operations.

### 🔍 Key Features:

- Allows **iteration** over rows returned by a query.
    
- Enables **row-level processing**.
    
- Useful when each row requires **individual logic** or attention.
    

---

## ❓ Why Use Cursors?

While SQL is optimized for **set-based operations**, cursors are useful in specific scenarios:

1. **Sequential Processing**  
    You need to process rows in a specific order, one at a time.
    
2. **Complex Per-Row Logic**  
    Business logic is too complex to express using set-based queries.
    
3. **Interactive Use Cases**  
    Applications that scroll or interact with one row at a time (e.g., user interfaces).
    

---

## 📂 Types of Cursors

|Cursor Type|Description|
|---|---|
|**Static**|Takes a snapshot of data when opened. Changes after opening are not visible.|
|**Dynamic**|Reflects real-time changes to the underlying data while open.|
|**Forward-Only**|Can only move forward through the data, one row at a time.|
|**Scrollable**|Allows movement both forward and backward through rows.|

---

## ⚠️ Performance Considerations

Cursors can be **resource-intensive**, especially in large datasets or high-traffic systems.

### 🔧 Key Issues:

1. **Overhead**  
    Cursors consume more memory and CPU than set-based operations.
    
2. **Locking and Concurrency**  
    May hold locks longer, affecting other queries.
    
3. **Better Alternatives**  
    Many cursor-based tasks can be rewritten using **set-based logic** or **window functions**.
    

---

## ✅ Best Practices for Using Cursors

- **Minimize Use**  
    Only use cursors when there's no clean set-based alternative.
    
- **Keep Transactions Short**  
    Shorten transaction duration to avoid locks and improve concurrency.
    
- **Choose Appropriate Cursor Type**  
    Use the **lightest-weight type** necessary (e.g., Forward-Only if scrolling isn't needed).
    
- **Close and Deallocate**  
    Always explicitly `CLOSE` and `DEALLOCATE` cursors to free resources.


## 📌 What is a Static Cursor?

A **Static Cursor** in T-SQL creates a **fixed snapshot** of the data at the time the cursor is opened.

### 🔍 Key Characteristics:

- Does **not reflect** any changes made to the underlying data **after the cursor is opened**.
    
- Provides a **stable and consistent dataset** throughout the cursor’s lifetime.
    
- Ideal for scenarios where **real-time data tracking is not required**.
    

---

## 🕒 When to Use Static Cursors?

Static cursors are useful when **data consistency** is more important than reflecting real-time updates.

### ✅ Common Use Cases:

1. **Report Generation**
    
    - Ensures consistent data throughout the duration of the report process.
        
2. **Data Analysis**
    
    - Allows working on a stable dataset for calculations, aggregations, etc.
        
3. **Non-Real-Time Needs**
    
    - Scenarios where current changes to data don’t matter for the task.
        

---

## ⚖️ Advantages & Disadvantages

### ✅ Advantages:

- **Data Consistency**  
    Ensures the data doesn’t change during the cursor operation.
    
- **Reduced Locking Overhead**  
    Because it doesn’t track live changes, fewer locks may be required.
    

---

### ❌ Disadvantages:

- **Higher Memory Usage**  
    Copies the entire result set into memory — may consume significant resources.
    
- **Not Suitable for Real-Time**  
    Won’t reflect the latest updates, inserts, or deletes made after the cursor starts.


```sql
-- Declare a static cursor named 'static_cursor'. 
-- A static cursor creates a snapshot of the result set at the time of cursor creation.
-- This cursor selects StudentID, Name, and Grade from the 'Students' table.
DECLARE static_cursor CURSOR STATIC FOR
SELECT StudentID, Name, Grade FROM dbo.Students;

-- Open the cursor. This initializes the cursor and makes it ready to use.
OPEN static_cursor;

-- Declare variables to store the data for each row fetched by the cursor.
-- @StudentID will hold the StudentID, @Name for the student's name, and @Grade for the grade.
DECLARE @StudentID int, @Name nvarchar(50), @Grade int;

-- Fetch the first row of data from the cursor and store it in the declared variables.
FETCH NEXT FROM static_cursor INTO @StudentID, @Name, @Grade;

-- Enter a loop that will continue as long as the previous fetch was successful.
-- @@FETCH_STATUS returns 0 if the fetch was successful.
WHILE @@FETCH_STATUS = 0
BEGIN
    -- Print the current row's student name and grade.
    PRINT 'Student Name: ' + @Name + ', Grade: ' + CAST(@Grade AS NVARCHAR(10));

    -- Fetch the next row of data from the cursor.
    -- The loop will continue until there are no more rows to fetch.
    FETCH NEXT FROM static_cursor INTO @StudentID, @Name, @Grade;
END

-- Once the loop is finished (no more rows to fetch), close the cursor.
-- Closing the cursor releases the current result set.
CLOSE static_cursor;

-- Deallocate the cursor.
-- This step is important to free up resources used by the cursor.
DEALLOCATE static_cursor;
```


## 🔄 What is a Dynamic Cursor?

A **Dynamic Cursor** in T-SQL provides a **live view** of the underlying data.

### 🔍 Key Characteristics:

- Reflects **real-time changes** in the database while the cursor is open.
    
- Updates, inserts, or deletes to the data are **immediately visible** through the cursor.
    
- Offers the most **up-to-date view** of a result set among all cursor types.
    

---

## 🕒 When to Use Dynamic Cursors?

Dynamic cursors are suitable when applications need to **react to changes as they happen**.

### ✅ Common Use Cases:

1. **Real-time Data Processing**
    
    - Ideal when your logic depends on the most current state of the data.
        
2. **Monitoring Changes**
    
    - Useful for audit systems, dashboards, or notifications that respond to live data modifications.
        

---

## ⚖️ Advantages & Disadvantages

### ✅ Advantages:

- **Real-Time Data Access**  
    Always shows the most current data available in the underlying tables.
    
- **Flexible and Reactive**  
    Supports responsive systems that depend on changing data.
    

---

### ❌ Disadvantages:

- **Performance Overhead**  
    Continuously tracking changes can lead to **higher CPU and memory usage**.
    
- **Increased Complexity**  
    More difficult to manage and debug compared to static cursors.


```sql
-- Declare a dynamic cursor named 'dynamic_cursor'.
-- A dynamic cursor reflects changes in the database while the cursor is open.
-- This cursor selects StudentID, Name, and Subject from the 'Students' table.
DECLARE dynamic_cursor CURSOR DYNAMIC FOR
SELECT StudentID, Name, Subject FROM dbo.Students;
```


## ➡️ What is a Forward-Only Cursor?

A **Forward-Only Cursor** is the **simplest and most efficient** type of cursor in SQL Server.

### 🔍 Key Characteristics:

- Can only **move forward** through the result set.
    
- Does **not support backward navigation** or jumping to specific rows.
    
- Designed for **one-pass, sequential reading** of data.
    
- Its simplicity typically results in **better performance**.
    

---

## 🕒 When to Use Forward-Only Cursors?

Forward-Only cursors are ideal when you need to process rows **sequentially** and **without complex logic**.

### ✅ Common Use Cases:

1. **Simple Data Retrieval**
    
    - Best for reading rows one at a time in a single direction.
        
2. **Performance Optimization**
    
    - Suitable when speed is a priority and advanced cursor features aren’t needed.
        

---

## ⚖️ Advantages & Disadvantages

### ✅ Advantages:

- **High Performance**  
    Minimal overhead makes it faster than other cursor types.
    
- **Ease of Use**  
    Simple to implement and understand for basic row-by-row operations.
    

---

### ❌ Disadvantages:

- **Limited Flexibility**  
    No support for backward navigation or random row access.
    
- **Not Suitable for Complex Tasks**  
    Lacks features required for advanced or interactive cursor scenarios.


```sql
DECLARE dynamic_cursor CURSOR DYNAMIC FOROWARD ONLY FOR
SELECT StudentID, Name, Subject FROM dbo.Students;
```


## 🔁 What is a Scrollable Cursor?

A **Scrollable Cursor** in T-SQL provides a **flexible way to navigate** through a result set in multiple directions.

### 🔍 Key Characteristics:

- Allows movement **both forward and backward** through rows.
    
- Supports **random access** — jumping to a specific row (e.g., first, last, absolute, relative).
    
- Ideal for **non-linear navigation** or **complex interaction** with row data.
    

---

## 🕒 When to Use Scrollable Cursors?

Scrollable cursors are best suited for applications or operations that require **advanced control** over row navigation.

### ✅ Common Use Cases:

1. **Flexible Data Access**
    
    - Navigate forward, backward, or to specific positions in the data.
        
2. **Non-Sequential Retrieval**
    
    - Access specific rows based on user input, paging logic, or business rules.
        

---

## ⚖️ Advantages & Disadvantages

### ✅ Advantages:

- **High Flexibility**  
    Move in any direction and access any row at any time.
    
- **Targeted Access**  
    Jump directly to rows without having to process the entire result set sequentially.
    

---

### ❌ Disadvantages:

- **Performance Overhead**  
    More resource-intensive than simpler cursor types due to added functionality.
    
- **Increased Complexity**  
    Requires more careful management and logic to use effectively.


```sql
DECLARE dynamic_cursor CURSOR DYNAMIC FOROWARD ONLY FOR
SELECT StudentID, Name, Subject FROM dbo.Students;

-- FETCH PRIOR FROM static_cursor INTO @StudentID, @Name, @Grade;
```
