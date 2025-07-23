
### Parallel.For (Multi-threading)

`Parallel.For` is a method in C# that allows you to execute a for loop in parallel, making it easier to perform parallel operations on a collection or for a specific number of iterations.  

**Example:**

Here's an example demonstrating how to use `Parallel.For`:

```csharp
using System;
using System.Threading.Tasks;


class Program
{
    static void Main()
    {
        // Define the number of iterations
        int numberOfIterations = 10;


        // Use Parallel.For to execute the loop in parallel
        Parallel.For(0, numberOfIterations, i =>
        {
            Console.WriteLine($"Executing iteration {i} on thread {Task.CurrentId}");
            // Simulate some work
            Task.Delay(1000).Wait();
        });


        Console.WriteLine("All iterations completed.");
    }
}
```

In this example:

1. `Parallel.For` is used to execute iterations in parallel from 0 to `numberOfIterations - 1`.
2. `Parallel.For` will handle the distribution of these iterations across multiple threads.
3. After `Parallel.For` completes, a message is printed to indicate that all iterations are done.

Note: The actual order of execution of the iterations may vary each time you run the program, as `Parallel.For` will execute them concurrently on different threads.

**Same Example using separate function:**


```csharp
using System;
using System.Threading.Tasks;


class Program
{
    static void Main()
    {
        // Define the number of iterations
        int numberOfIterations = 10;


        // Use Parallel.For to execute the loop in parallel
        Parallel.For(0, numberOfIterations, ProcessIteration);


        Console.WriteLine("All iterations completed.");
    }


    static void ProcessIteration(int i)
    {
        Console.WriteLine($"Executing iteration {i} on thread {Task.CurrentId}");
        // Simulate some work
        Task.Delay(1000).Wait();
    }
}
```




### Parallel.ForEach:

`Parallel.ForEach` is used in C# to execute a parallel loop over a collection or an enumerable. It can efficiently process items in parallel, improving performance for suitable tasks.

Here's an example demonstrating `Parallel.ForEach`:


```csharp
using System;
using System.Collections.Generic;
using System.Net;
using System.Threading;
using System.Threading.Tasks;


class Program
{
    static List<string> urls = new List<string>
    {
        "https://www.cnn.com",
        "https://www.amazon.com",
        "https://www.programmingadvices.com"
    };


    static void Main()
    {
       
        // Use Parallel.ForEach to download the web pages concurrently
        Parallel.ForEach(urls, url =>
        {
             DownloadContent(url);
            
        });


        Console.WriteLine("Done!");
        Console.ReadKey();    
    }


    static void DownloadContent(string url)
    {
        string content;


        using (WebClient client = new WebClient())
        {
            // Simulate some work by adding a delay
            Thread.Sleep(100);


            // Download the content of the web page
            content = client.DownloadString(url);
        }


     Console.WriteLine( $"{url}: {content.Length} characters downloaded");
    }
}
```


### Parallel.Invoke

`Parallel.Invoke` allows you to execute multiple actions in parallel.  

#### Parallel.Invoke - Example

```csharp
using System;
using System.Threading.Tasks;


class Program
{
    static void Main()
    {
        Parallel.Invoke(
            () => Console.WriteLine($"Action 1 on thread {Task.CurrentId}"),
            () => Console.WriteLine($"Action 2 on thread {Task.CurrentId}"),
            () => Console.WriteLine($"Action 3 on thread {Task.CurrentId}")
        );
    }
}
```


#### Explanation:

This code executes three actions in parallel. Each action is a lambda expression that prints a message including the ID of the thread it's running on.

  

**Simpler way:**

### Parallel.Invoke - Example2

  

`Parallel.Invoke`, you can define separate methods for each function and then call these methods within `Parallel.Invoke`. Here's the modified example:

```csharp
using System;
using System.Threading.Tasks;


class Program
{
    static void Main()
    {
        // Run the functions in parallel
        Console.WriteLine("Starting parallel functions.");
        Parallel.Invoke(Function1, Function2, Function3);
        Console.WriteLine("All parallel functions are completed.");
    }


    static void Function1()
    {
        Console.WriteLine("Function 1 is starting.");
        Task.Delay(1000).Wait(); // Simulating work
        Console.WriteLine("Function 1 is completed.");
    }


    static void Function2()
    {
        Console.WriteLine("Function 2 is starting.");
        Task.Delay(1000).Wait(); // Simulating work
        Console.WriteLine("Function 2 is completed.");
    }


    static void Function3()
    {
        Console.WriteLine("Function 3 is starting.");
        Task.Delay(1000).Wait(); // Simulating work
        Console.WriteLine("Function 3 is completed.");
    }
}
```


In this example:

1. `Function1`, `Function2`, and `Function3` are defined as separate static methods in the `Program` class.
2. Each function simulates some work by calling `Task.Delay`.
3. `Parallel.Invoke` is used to execute these methods in parallel. The methods are passed as arguments to `Parallel.Invoke` without using lambda expressions.
4. The program prints messages to the console indicating the start and completion of each function, as well as the overall completion of all functions.