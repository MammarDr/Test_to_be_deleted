ADO.NET (Activex Data Object) is a set of classes(Framework), that can be used to interact with data sources like Databases and XML files.

Some .Net applications that use ADO.NET to connect to a database, execute commands, and retrieve data.
ASP.NET Web Applications
Windows Applications
Console Applications


```cs
using System;
using System.Data;
using System.Data.SqlClient;

class Program
{
    static void Main()
    {
        string connectionString = "your_connection_string_here";
        using (SqlConnection connection = new SqlConnection(connectionString))
        {
            connection.Open();

            using (SqlCommand command = new SqlCommand("YourStoredProcedureName", connection))
            {
                command.CommandType = CommandType.StoredProcedure;

                // Add parameters if needed
                command.Parameters.AddWithValue("@ParameterName", "ParameterValue");

                // Add a return value parameter
                SqlParameter returnValue = new SqlParameter();
                returnValue.Direction = ParameterDirection.ReturnValue;
                command.Parameters.Add(returnValue);

                // Execute the command
                command.ExecuteNonQuery();

                // Retrieve the return value
                int result = (int)returnValue.Value;
                Console.WriteLine("Return Value: " + result);
            }
        }
    }
}

```



```csharp
try
{
    using (SqlConnection connection = new SqlConnection(stSettings.connectionString))
    {
        connection.Open();

        // Step 1: Get current DB name
        string dbName;
        using (SqlCommand getName = new SqlCommand("SELECT DB_NAME()", connection))
        {
            dbName = (string)getName.ExecuteScalar();
        }

        // Step 2: Backup
        string backupPath = @"C:\Backups\" + dbName + "_backup.bak";
        string backupSql = $"BACKUP DATABASE [{dbName}] TO DISK = @path";

        using (SqlCommand backupCmd = new SqlCommand(backupSql, connection))
        {
            backupCmd.Parameters.AddWithValue("@path", backupPath);
            backupCmd.ExecuteNonQuery(); // use ExecuteNonQuery for backup
        }
    }
}
catch (Exception e)
{
    _log("Connection to the database has failed: " + e.Message);
}
```