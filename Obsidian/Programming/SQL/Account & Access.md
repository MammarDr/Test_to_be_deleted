
```sql
CREATE LOGIN myappuser WITH PASSWORD = 'StrongPassword123!';
CREATE USER myappuser FOR LOGIN myappuser;

```

```sql
USE Contacts;

CREATE USER shadow FOR LOGIN shadow; -- maps login to DB
EXEC sp_addrolemember 'db_owner', shadow; -- gives full access
```