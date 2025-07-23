```sql
CREATE PROCEDURE SP_AddNewPerson
    @FirstName NVARCHAR(100),
    @LastName NVARCHAR(100),
    @Email NVARCHAR(255),
    @NewPersonID INT OUTPUT
AS
BEGIN
    INSERT INTO People (FirstName, LastName, Email)
    VALUES (@FirstName, @LastName, @Email);


    SET @NewPersonID = SCOPE_IDENTITY();
END
```

This procedure inserts a new person into the `People` table and returns the new person's ID.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;


class Program
{
    static void Main()
    {
    
        string connectionString = "YourConnectionStringHere";
        
        using(SqlConnection connection = new SqlConnection(connectionString);
        
        using SqlCommand command = new SqlCommand("SP_AddNewPerson", connection);
        
        command.CommandType = CommandType.StoredProcedure;


        // Add parameters
        command.Parameters.AddWithValue("@FirstName", "John");
        command.Parameters.AddWithValue("@LastName", "Doe");
        command.Parameters.AddWithValue("@Email", "john.doe@example.com");
        
        SqlParameter outputIdParam = 
	        new SqlParameter("@NewPersonID", SqlDbType.Int)
        {
            Direction = ParameterDirection.Output //  ADO.NET bring output back
        };
        command.Parameters.Add(outputIdParam);


        // Execute
        connection.Open();
        command.ExecuteNonQuery();

        // Retrieve the ID of the new person
        int newPersonID = (int)command.Parameters["@NewPersonID"].Value;
        Console.WriteLine($"New Person ID: {newPersonID}");


        connection.Close();
    }
}
```


we have a stored procedure named `SP_CheckPersonExists` in SQL Server:

```sql
CREATE PROCEDURE SP_CheckPersonExists
    @PersonID INT
AS
BEGIN
    IF EXISTS(SELECT * FROM People WHERE PersonID = @PersonID)
        RETURN 1;  -- Person exists
    ELSE
        RETURN 0;  -- Person does not exist
END
```
This procedure checks if a person exists in the `People` table using `PersonID`.

```csharp
public static void CheckPersonExists(int PersonID)
{
     
    try   
	{
     
	string connectionString = 
     
	"Server=.;Database=C21_DB1;User Id = sa; password=sa123456;";
     
	using (SqlConnection connection = new SqlConnection(connectionString))
	{
     
		connection.Open();
		
     
		using (SqlCommand command =
     
		new SqlCommand("SP_CheckPersonExists", connection))
		{
     
			command.CommandType = CommandType.StoredProcedure;
			
     
			command.Parameters.AddWithValue("@PersonID",		                                                 (object) PersonID ?? DBNull.Value);
			
     
			SqlParameter returnParameter = 
     
			new SqlParameter("@ReturnVal", SqlDbType.Int)
     
			{
     
				Direction = ParameterDirection.ReturnValue
     
			};
			
     
			command.Parameters.Add(returnParameter);
     
			command.ExecuteNonQuery();
			
     
			bool personExists = (int)returnParameter.Value == 1;
			
     
			Console.WriteLine($" Person Exist   {personExists}");
			
     
			connection.Close();
			
     
		}
     
	}
	
     
	} catch(Exception ex) 
			Console.WriteLine($" Person doesn't Exist   {ex.Message}");
	
	
}
```