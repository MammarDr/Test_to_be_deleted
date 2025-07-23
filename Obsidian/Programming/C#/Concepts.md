
# **Using :**

## C# using - To Import Library

In C#, we use the using keyword to import external resources (namespaces, classes, etc) inside a program. For example,

```csharp
// using System namespace
using System;

namespace Program {

  class Program1 {
    static void Main(string[] args) {
      Console.WriteLine("Hello World!");   
    }
  }
}
```


## C# using to create an alias

We can also create aliases with the help of `using` in C#. For example,

```csharp
// creating alias for System.Console
using Koko = System.Console;

namespace HelloWorld {

  class Program {
    static void Main(string[] args) {

      // using Koko alias instead of System.Console
      Koko.WriteLine("Hello World!");    
      Koko.ReadKey();
    }
  }
}
```

## C# using static directive

In C#, we can also import classes in our program. Once we import these classes, we can use the static members (fields, methods) of the class.

We use the `using static` directive to import classes in our program.

**Math class is static so :**

```csharp
using System;

// using static directive
using static System.Math;

namespace Program {  

  class Program1  {  
    public static void Main(string[] args)  {  
       	 
      double n  = Sqrt(9);
      Console.WriteLine("Square root of 9 is " + n);  
      	 
    }  
  }  
}
```

## C# using for Resource Management:

The `using` statement is commonly used for resource management, such as working with objects that implement the `IDisposable` interface. The `IDisposable` interface provides a method called `Dispose` that allows you to release unmanaged resources or perform cleanup operations. By using the `using` statement, you can ensure that the `Dispose` method is called automatically when the block of code is exited, even if an exception is thrown.

Here's an example of how the `using` statement is used for resource management:

```csharp
using (var resource = new DisposableResource())
{
    // Use the resource
    // The Dispose method will be automatically called when this block is exited.
}
```
  

**Examples of using the Using statement:**

1- File I/O - Reading and writing to a text file:

```csharp
using (var reader = new StreamReader("example.txt"))
{
    string line;
    while ((line = reader.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
// The StreamReader will be automatically closed and resources released.
```


2-Database Connection - Connecting to a SQL Server database using `SqlConnection`:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Open();
    // Execute SQL commands
}
// The SqlConnection will be automatically closed and resources released.
```
  

3- Network Resources - Working with a network stream:

```csharp
using (var client = new TcpClient("example.com", 80))
{
    using (var stream = client.GetStream())
    {
        // Read and write to the network stream
    }
}
// Both TcpClient and NetworkStream will be automatically closed and resources released.
```


4- Working with IDisposable Objects - Using custom objects that implement `IDisposable`:

```csharp
using (ResourceType resource = new ResourceType())
{
    // Code that uses the resource
    // The resource will be automatically disposed when the block is exited
}
```

  

If you have a custom class that needs to be used with the using statement, make sure it implements IDisposable and properly handles resource cleanup in its Dispose method, as demonstrated in the previous response.  

In all these examples, the `using` statement ensures that the resources are properly released and disposed of when they go out of scope, making the code cleaner and more reliable.

  

Here are a few reasons why using `using` is recommended for database connections:

1. Resource Cleanup: Database connections often involve unmanaged resources, such as network connections or file handles. The `Dispose` method is where you release these resources. If you don't properly release these resources, it can lead to issues like connection leaks, where the database server runs out of available connections.
2. Timely Release of Resources: By using the `using` statement, you guarantee that the `Dispose` method is called when the code block is exited. This ensures that resources are released in a timely manner, even if an exception occurs within the block.
3. Easier Code Maintenance: Using `using` makes the code cleaner and more readable. It clearly indicates the scope in which the resource is used, making it easier for developers to understand when the resource is acquired and when it is released.

```csharp
string connectionString = "Server=your_server;Database=your_database;User Id=your_username;Password=your_password;";

try
{
    using (SqlConnection connection = new SqlConnection(connectionString)) <=====
    {
        connection.Open();
        string query = "SELECT FirstName, LastName FROM Employee";
        using (SqlCommand command = new SqlCommand(query, connection)) <=====
        {
            using (SqlDataReader reader = command.ExecuteReader()) <=====
            {
                if (reader.HasRows)
                {
                    while (reader.Read())
                    {
                        string firstName = reader["FirstName"].ToString();
                        string lastName = reader["LastName"].ToString();
                        Console.WriteLine($"Name: {firstName} {lastName}");
                    }
                }
                else
                {
                    Console.WriteLine("No rows found.");
                }
            } // Disposal
        } // Disposal
    } // Disposal
}
catch (SqlException ex)
{
    Console.WriteLine("Database connection error: " + ex.Message);
}

```


# **Nullable Data Types:**

In C#, a nullable data type is a value type that can also be assigned a value of `null`. This is achieved by using the `Nullable<T>` structure or its shorthand notation `T?`, where `T` is the underlying value type. This is particularly useful when dealing with database values or scenarios where a variable may legitimately have no value.

Here's an example using `Nullable<T>` with an `int`:

```csharp

 // Declare a nullable int using Nullable<T>
 Nullable<int> nullableInt = null;
 
 // Shorthand notation using int?
 int? nullableInt = null;
 
 // Check if the nullable ints have values
 if (nullableInt.HasValue) { NOT NULL }
 else { IS NULL }

 
 // Using the null-coalescing operator
 int result = nullableInt2 ?? 0;
 Console.WriteLine("Using null-coalescing operator: " + result);
 
 // Using the null-conditional operator
 // first ? prevent ToString() being called
 // no exception, return NULL
 // ?? take that value and provide default N/A
 string stringValue = nullableInt2?.ToString() ?? "N/A";
 Console.WriteLine("String representation: " + (stringValue ?? "null"));
 Console.ReadKey();  

 public void procedure(int? x) {
	 if(x.hasValue) x = 10;
 }


object x = 42;
object y = null;
object z = "string";

int? temp = (int?)x; // 42
int? temp = (int?)y; // NULL
int? temp = (int?)z; // ❌ Throws InvalidCastException

```

In this example:

- `Nullable<int>` and `int?` are both used to declare nullable integers.
- `HasValue` property is used to check if the nullable value has a value.
- The null-coalescing operator (`??`) is used to provide a default value if the nullable value is `null`.
- The null-conditional operator (`?.`) is used to access a property or method only if the nullable value is not `null`.

Using `int?` is more common and convenient for declaring nullable integers in C#. You can apply the concept of nullable types to other value types as well, such as `DateTime?`, `float?`, etc., depending on your requirements.

 > Meaning of `(int?)x`:

- It **tries to cast `x`** to an `int?` (nullable int).
    
- If `x` is:
    
    - `null` → the result is `null`
        
    - a value that can be converted to `int` → the result is that int value
        
    - something that **can't be converted** → it throws an `InvalidCastException`
-


## [User Control Library]([Introduction | ProgrammingAdvices](https://programmingadvices.com/courses/16-oop-in-c21/lectures/51619562))
## [Custom Controls]([Introduction to Custom Controls | ProgrammingAdvices](https://programmingadvices.com/courses/16-oop-in-c21/lectures/51620955))
