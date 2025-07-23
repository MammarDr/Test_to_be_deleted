
 **Transaction** in SQL is a series of database operations that are treated as **a single logical unit**. It ensures that either all operations within it are executed or none are.
 
**Transactions** in SQL adhere to **ACID properties** - Atomicity, Consistency, Isolation, Durability.

**ACID** is an acronym that stands for Atomicity, Consistency, Isolation, and Durability. It's a set of properties that guarantee that database transactions are processed reliably.

1. Atomicity: This ensures that all operations within a transaction are treated as a single unit. Either all of them are executed successfully, or none are. If any part of the transaction fails, the entire transaction is rolled back (undone), maintaining data integrity.
2. Consistency: Consistency ensures that a transaction brings the database from one valid state to another. Integrity constraints are maintained so that the database remains consistent before and after the transaction.
3. Isolation: Isolation ensures that transactions are securely and independently processed at the same time without interference, but the results of the transaction are as if the transactions were processed sequentially. This prevents transactions from reading intermediate (and possibly inconsistent) data.
4. Durability: Durability guarantees that once a transaction has been committed, it will remain so, even in the event of a system failure. This means that the changes made by the transaction are permanently stored in the database. In practical terms, this means that the database system has mechanisms in place, such as writing to a transaction log, that ensure the permanence of the transaction's effects.

Together, these properties ensure that database transactions are executed safely, reliably, and in a way that preserves the integrity of the database.


```sql
BEGIN TRANSACTION
	BEGIN TRY
		UPDATE Accounts SET Balance = Balance - 100 WHERE AccountID = 1;
		IF @@ROWCOUNT = 0 THROW 50001, 'Account 1 not found', 1;
		UPDATE Accounts SET Balance = Balance + 100 WHERE AccountID = 2;
		IF @@ROWCOUNT = 0 THROW 50001, 'Account 1 not found', 1;
		INSERT INTO Transactions(FromAccount, ToAccount, Amount, Date) 
				VALUES(1,2, 100, GETDATE());

		COMMIT;
	END TRY
	BEGIN CATCH
		ROLLBACK;
		PRINT 'Error Transaction';
	END CATCH
```
> `COMMIT` confirms the transaction; `ROLLBACK` undoes it in case of errors> 

> Check `@@ROWCOUNT` after `UPDATE`/`DELETE`**

My Note:
Transaction preform locking table which prevent others from making changes so its better to keep code short to minimize the block time.