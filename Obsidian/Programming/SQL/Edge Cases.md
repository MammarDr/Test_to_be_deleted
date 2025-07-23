
### ⚠️ Important Note (NULL values):

If some rows in the `Customer` table have `referee_id` as `NULL`, they will **not be included** in the result. This is because:

- `NULL != 2` results in **unknown**, not `TRUE`.

```sql
SELECT name FROM Customer
WHERE referee_id IS NULL OR referee_id != 2;
```

> NULL is not equal not even to itself

### Multiple Aggregation in one query:

| EmployeeID | Status  |
| ---------- | ------- |
| `101`      | Present |
| `102`      | Leave   |
| `103`      | Leave   |
| `101`      | Absent  |
| `101`      | Absent  |
| `103`      | Present |


```sql
SELECT
	EmployeeID,
    COUNT(CASE WHEN status = 'Present' THEN 1 END) AS Present_count,
    COUNT(CASE WHEN status = 'Leave' THEN 1 END) AS Leave_count,
	COUNT(CASE WHEN status = 'Absent' THEN 1 END) AS Absent_count
-- OR -- SUM(CASE WHEN Status = 'Leave' THEN 1 ELSE 0 END)	1&0 know!
FROM EmployeeAttendance
WHERE EmployeeID = 101
GROUP BY EmployeeID;
```


| EmployeeID | Present_count | Leave_count | Absent_count |
| ---------- | ------------- | ----------- | ------------ |
| `101`      | 1             | 0           | 2            |
| `102`      | 0             | 1           | 0            |
| `103`      | 1             | 1           | 0            |
  

### [197. Rising Temperature](https://leetcode.com/problems/rising-temperature/)

Write a solution to find all dates' `id` with higher temperatures compared to its previous dates (yesterday).
**Explanation:** 
In 2015-01-02, the temperature was higher than the previous day (10 -> 25).
In 2015-01-04, the temperature was higher than the previous day (20 -> 30).

```sql
SELECT w1.id FROM Weather w1
JOIN Weather w2 ON DATEDIFF(w1.recordDate, w2.recordDate) = 1 
AND w1.temperature > w2.temperature
```



### [1280. Students and Examinations](https://leetcode.com/problems/students-and-examinations/)

### 🔄 `JOIN ON 1 = 1` vs `CROSS JOIN`

```sql
SELECT S.student_id, student_name, SB.subject_name, COUNT(E.subject_name) AS attended_exams FROM Students S

JOIN Subjects SB ON 1 = 1
```

##### OR

```sql
SELECT S.student_id, student_name, SB.subject_name, COUNT(E.subject_name) AS attended_exams FROM Students S

CROSS JOIN Subjects SB 
```

### 📝  Note:

> `JOIN ON 1=1` is logically equivalent to `CROSS JOIN`. Both create all possible combinations between the two tables. Use `CROSS JOIN` for clarity.



### [570. Managers with at Least 5 Direct Reports](https://leetcode.com/problems/managers-with-at-least-5-direct-reports/)

```sql
SELECT name FROM (

    SELECT managerID, IFNULL(COUNT(managerID), 0) AS Reports

    FROM Employee

    GROUP BY managerID ) R1

JOIN Employee E ON R1.managerID = E.id

WHERE R1.Reports >= 5
```

##### OR

```sql
SELECT 
NAME
FROM Employee
WHERE ID IN (
    SELECT managerId
    FROM Employee
    GROUP BY managerId
    HAVING COUNT(*) >= 5)
```


### [1934. Confirmation Rate](https://leetcode.com/problems/confirmation-rate/)

### ✅ Option 1: Using `SUM` (preferred for conditional count)

```sql
SUM(CASE WHEN action = 'A' THEN 1 ELSE 0 END) AS confirmation
```
``

---

### ✅ Option 2: Using `COUNT`
```sql
COUNT(CASE WHEN action = 'A' THEN 1 END) AS confirmation
```


### ✅ Best Option : Using `AVG`

```sql
AVG(IF(C.action = 'confirmed', 1, 0))
```
``

🔍 What it does:
For each row:

> If action = 'confirmed', it returns 1

Otherwise, 

> it returns 0

Then AVG() calculates the average of all these 1s and 0s

📌 So yes — when the condition is not true, it still contributes 0 to the average.
And it's still counted in the denominator.



### [1193. Monthly Transactions I](https://leetcode.com/problems/monthly-transactions-i/)

```sql
SELECT DATE_FORMAT(trans_date, '%Y-%m') AS month, country,
COUNT(*) AS trans_count,
COUNT(CASE WHEN state = 'approved' THEN 1 END) AS approved_count,
SUM(amount) AS trans_total_amount,
SUM(CASE WHEN state = 'approved' THEN amount ELSE 0 END) AS approved_total_amount
FROM Transactions
GROUP BY country, month
```

