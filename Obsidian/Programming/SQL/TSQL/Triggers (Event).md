
# ✅ What is a Trigger?

A **trigger** is a special kind of stored procedure that **automatically executes** when a specific **event** (like `INSERT`, `UPDATE`, or `DELETE`) occurs on a table or view.

Triggers are defined **at the table or view level** and are tightly bound to the data modification events they watch.

---

# 📌 Common Use Cases

Triggers can be useful in various scenarios, including:

### 1. **Enforcing Business Rules**

- Ensure data integrity by validating inputs before changes are committed.
    
- Example: Preventing salary increases beyond a certain percentage.
    

### 2. **Auditing and Logging**

- Record who made changes, when, and what changed.
    
- Useful for maintaining historical or compliance logs.
    

### 3. **Data Synchronization**

- Automatically replicate changes to another table or system to maintain consistency.
    

### 4. **Automatic Updates**

- Update related tables or fields when data is changed (e.g., auto-calculating totals or updating timestamps).
    

---

# ⚙️ Trigger Components

Every trigger has **three main components**:

| Component             | Description                                                                                                   |
| --------------------- | ------------------------------------------------------------------------------------------------------------- |
| **Trigger Event**     | The action that activates the trigger: `INSERT`, `UPDATE`, or `DELETE`                                        |
| **Trigger Condition** | The optional condition that determines if the trigger should execute (e.g., only if a certain column changed) |
| **Trigger Action**    | The SQL statements that run when the trigger is fired (e.g., inserting into a log table)                      |

---

# 🧱 Types of Triggers

Triggers are categorized by **when** they execute:

### 1. **AFTER Triggers (default)**

- Run **after** the triggering action completes (i.e., after insert/update/delete).
    
- Commonly used for:
    
    - Logging
        
    - Audit trails
        
    - Enforcing constraints that rely on finalized data
        

**Examples:**

- `AFTER INSERT`
    
- `AFTER UPDATE`
    
- `AFTER DELETE`
    

---

### 2. **INSTEAD OF Triggers**

- Run **instead of** the triggering action.
    
- Commonly used on **views** or when you want to override the default behavior.
    

**Example Use Cases:**

- Preventing deletion of archived records
    
- Allowing complex insert/update logic on views


# 📝 Example: AFTER INSERT Trigger

```sql
CREATE TRIGGER trg_AfterInsertStudent ON Students
AFTER INSERT
AS
BEGIN
    INSERT INTO StudentInsertLog(StudentID, Name, Subject, Grade)
    SELECT StudentID, Name, Subject, Grade FROM inserted;
END;
```

>Changes are registered in temporary table 'inserted' then applied to the main table

# 📝 Example: AFTER UPDATE Trigger

```sql
CREATE TRIGGER trg_AfterUpdateStudent ON Students
AFTER UPDATE
AS
BEGIN
    IF UPDATE(Grade)
    BEGIN
        INSERT INTO StudentUpdateLog(StudentID, OldGrade, NewGrade)
        SELECT i.StudentID, d.Grade AS OldGrade, i.Grade AS NewGrade
        FROM inserted i
        INNER JOIN deleted d ON i.StudentID = d.StudentID;
    END
END;
```
>Just **remove the `IF UPDATE(...)`** when update all. 
>If one of many : UPDATE(X) OR UPDATE(Y) OR UPDATE(Z)

If you want to check that **both were changed**, you’ll need to check values explicitly:

```sql
IF EXISTS (
SELECT 1
FROM inserted i
JOIN deleted d ON i.StudentID = d.StudentID
WHERE i.Grade != d.Grade AND i.Status != d.Status )
```

# 📝 Example: AFTER DELETE Trigger

```sql
CREATE TRIGGER trg_AfterDeleteStudent ON Students
AFTER DELETE
AS
BEGIN
    INSERT INTO StudentDeleteLog(StudentID, Name, Subject, Grade)
    SELECT StudentID, Name, Subject, Grade FROM deleted;
END;
```

#### Temporary tables :
- inserted
- deleted

#### **Usage in Different DML Operations**:

- **INSERT**:
    - `inserted` contains the new data being inserted.
- **DELETE****:**
    - `deleted` contains the data being removed.
- **UPDATE****:**
    - `inserted` holds the updated (new) version of the data.
    - `deleted` holds the original data before the update.


### What are Instead Of Triggers?

Instead Of Triggers are a **type of database** trigger in T-SQL that override the default action of INSERT, UPDATE, or DELETE statements on a table or view. These triggers are essential when you need to perform custom operations or validations that are not achievable with standard SQL constraints or rules.

#### **Key Characteristics:**

1. Override Default Actions: They replace the standard data modification operation with custom logic defined in the trigger.
2. Versatile Use Cases: Ideal for complex validations, data transformations, and enforcing business rules.
3. Special Tables - `inserted` and `deleted`: These triggers can access the `inserted` and `deleted` tables for context about the data changes.

#### **How They Work:**

- When a data modification statement is executed on a table with an Instead Of Trigger, the trigger's code runs instead of the standard operation.
- The trigger can include any T-SQL code, allowing for extensive customization and control over the data.
- Unlike AFTER triggers, Instead Of Triggers can prevent the standard operation from occurring by not including it in their logic.

#### **Typical Use Cases:**

1. Complex Operations on Views: Implementing updateable views based on multiple tables.
2. Data Validation: Enforcing complex validation rules before data changes are committed.
3. Data Transformation: Modifying data before it is inserted or updated to meet specific criteria.
4. Soft Deletes: Marking records as inactive instead of physically deleting them.


# 📝 Example: INSTED DELETE Trigger


```sql
CREATE TRIGGER trg_InsteadOfDeleteStudent ON Students
INSTEAD OF DELETE
AS
BEGIN
    -- Marking the record as inactive instead of deleting
    UPDATE Students
    SET IsActive = 0
    FROM Students S
    INNER JOIN deleted D ON S.StudentID = D.StudentID;
END;
```


# 📝 Example: INSTED UPDATE Trigger

```sql
CREATE TRIGGER trg_UpdateStudentView ON StudentView
INSTEAD OF UPDATE
AS
BEGIN
    -- Update PersonalInfo
    UPDATE PersonalInfo
    SET Name = I.Name, Address = I.Address
    FROM PersonalInfo
    INNER JOIN inserted I ON PersonalInfo.StudentID = I.StudentID;


    -- Update AcademicInfo
    UPDATE AcademicInfo
    SET Course = I.Course, Grade = I.Grade
    FROM AcademicInfo
    INNER JOIN inserted I ON AcademicInfo.StudentID = I.StudentID;
END;
```


# 📝 Example: INSTED INSERT Trigger

```sql
CREATE TRIGGER trg_InsertStudentView ON StudentView
INSTEAD OF INSERT
AS
BEGIN
    -- Insert into PersonalInfo
    INSERT INTO PersonalInfo (StudentID, Name, Address)
    SELECT StudentID, Name, Address FROM inserted;


    -- Insert into AcademicInfo
    INSERT INTO AcademicInfo (StudentID, Course, Grade)
    SELECT StudentID, Course, Grade FROM inserted;
END;
```