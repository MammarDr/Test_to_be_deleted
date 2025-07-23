## Definition

• Process: A process is an independent and self- contained **unit of execution** in a computer system. It is an instance of a running program that includes its own memory space, resources, and system state.

• Thread: A thread is **the smallest unit of execution** within a process. It shares the same resources and memory space as other threads within the same process.

---
## Memory Space

• Process: Each process has its own separate memory space. Processes do not share memory with other processes by default.

• Thread: Threads within the same process share the same memory space. They can directly access the memory of other threads within the same  process.


## Resource Allocation

• Process: Processes are allocated system resources, including CPU time, memory, file handles, and more. Each process operates Independently of other processes.

• Thread: Threads within a process share the same resources. They can efficiently communicate with each other through shared memory.


## Communication

• Process: Inter-process communication (IPC) mechanisms, such as message passing or shared memory, are required for processes to communicate with each other.

• Thread: Threads within the same process can communicate directly through shared variables and data structures. No special mechanisms are required for communication.


## Isolation

• Process: Processes are isolated from each other, meaning that the failure of one process does not affect the execution of other processes.

• Thread: Threads within the same process are not fully isolated. The failure of one thread can potentially affect the entire process.


## Overhead

• Process: Processes have a higher overhead compared to threads due to the isolation and resource allocation.

• Thread: Threads have lower overhead compared to processes because they share resources and memory.


## Creation and Termination

• Process: Creating and terminating processes is generally more time-consuming than creating and terminating threads.

• Thread: Creating and terminating threads is generally faster than creating and terminating processes.

---

## What is Multithreading?

• Multithreading in C# refers to the concurrent execution of multiple threads within the same application.

• A thread is the smallest unit of execution in a process.

• The program itself is a thread (Main Thread).

• Multithreading allows you to perform multiple tasks simultaneously, improving performance and responsiveness in certain scenarios.

• Threading in C# allows you to create and manage threads to execute multiple operations concurrently.

•Multithreading is a powerful technique that can significantly improve the performance of applications that can benefit from concurrent execution of tasks.

• However, it also introduces challenges such as synchronization and coordination between threads.

• Therefore, careful design and understanding of multithreading concepts are essential for creating robust multithreaded applications in
C# 
## Example of multithreaded Applications.
• Windows
• Chatting Applications
• Games
• Webservices
• And Many others

```csharp
class Program
{
    static void Main()
    {
        // Note that your program is the main thread.


        // Create a new thread and start it
        Thread t = new Thread(ThreadMethod1);
        t.Start();


        Thread t2 = new Thread((text) =>
		{
		    Console.Write("\n" + text);
		    for (int i = 0; i < 9 + 1; i++)
		    {
		        Thread.Sleep(1000);
		        sum += list[i];
		    }
		
		    Console.Write("\nThe Sum : " + sum);
		});
		t2.start("Working On The Sum");
		
        Console.ReadKey();
    }


    static void ThreadMethod1()
    {
        for (int i = 1; i <= 5; i++)
        {
            Console.WriteLine("Thread Method1: " + i);
            Thread.Sleep(1000);
        }
    }
}
```

### Important Methods and Properties

The Thread class provides several important members:

- `Start()`: Begins execution of the thread
- `Join()`: Blocks the calling thread until this thread terminates
- `Sleep()`: Suspends the thread for a specified duration
- `Abort()` (deprecated): Terminates the thread
- `IsAlive`: Property that indicates if the thread is running
- `IsBackground`: Property that determines if the thread is a background thread
- `Name`: Property to assign a name to the thread for debugging purposes
- `Priority`: Property to set the priority level of the thread

---

## **What is Race Condition?**

A race condition is a situation in concurrent(parallel) programming where the behavior of a program depends on the relative timing of events, such as the order in which threads are scheduled to run.

In other words, a race condition occurs when the correctness of a program's execution depends on the unpredictable interleaving of operations from multiple threads.

Race conditions can lead to unexpected and undesirable outcomes, including:

- Data corruption,
- Application crashes
- or other forms of incorrect behavior.

They are particularly common in multithreaded or parallel programming, where multiple threads execute concurrently and may access shared resources or variables.


### **Thread Synchronization:**  

Thread synchronization in C# refers to the coordination and control of the execution of multiple threads to ensure that they access shared resources or perform critical sections of code in a mutually exclusive and orderly manner. Without proper synchronization, multiple threads operating concurrently can lead to data corruption, race conditions, and unpredictable behavior.

In the context of multithreading and concurrent programming, "mutually exclusive" refers to a situation where only one thread at a time is allowed to execute a particular section of code or access a shared resource. The idea is to ensure that certain critical operations are performed atomically, without interference from other threads.  

Thread synchronization is crucial when multiple threads access shared resources to prevent race conditions and ensure data consistency. One common synchronization mechanism in C# is the `lock` statement.

Multithreading is a powerful technique that can significantly improve the performance of applications that can benefit from concurrent execution of tasks. However, it also introduces challenges such as synchronization and coordination between threads. Therefore, careful design and understanding of multithreading concepts are essential for creating robust multithreaded applications in C#.


```csharp
static int sharedCounter = 0;
static object lockObject = new object();


 
static void Main()
 
{
	// Create two threads that increment a shared counter
	Thread t1 = new Thread(IncrementCounter);
	Thread t2 = new Thread(IncrementCounter);
	
	t1.Start();
	t2.Start();
	
	// Wait for both threads to complete
	t1.Join();
	t2.Join();
	
	Console.WriteLine("Final Counter Value: " + sharedCounter);
 
	Console.ReadKey();
 
}

static void IncrementCounter()
{
	for (int i = 0; i < 100000; i++)
 
	{
		// Use lock to synchronize access to the shared counter
		lock (lockObject)
		{
			sharedCounter++;
		}
	}
}
```


- Creating the Lock Object:
    - `lockObject` is created as a simple object instance using `new object();`.
    - It is a dedicated object whose sole purpose is to be used as a synchronization object.

## Role : 

- Preventing Race Conditions
- Ensuring Data Consistency
- Avoiding Deadlocks

---
### 🔹 **What is a Thread Pool?**

A **thread pool** is a collection of pre-created threads that can be reused to perform multiple tasks. Instead of creating a new thread for every task (which is expensive), we enqueue tasks and let worker threads handle them.

---


![[Pasted image 20250606080257.png]]

### ✅ **Concurrency model** means:

> A **design approach** or **strategy** for **running multiple tasks** at the same time (or overlapping in time), and how a programming language or system **manages** that.

### 🔍 In simpler terms:

A **concurrency model** defines **how a program handles multiple things happening together** — such as handling user input while reading a file or serving multiple users at once.

### ✅ What "blocking" means
> **Blocking** = The current thread **waits and does nothing** until a task finishes.

|**Aspect**|**Synchronous**|**Asynchronous**|**Multithreading**|
|---|---|---|---|
|**Uses multiple threads?**|❌ No (single thread)|❌ Usually no (can run on one thread)|✅ Yes (multiple threads)|
|**Blocks main thread?**|✅ Yes|❌ No|❌ Not always, but threads can still block|
|**Resource usage**|❌ Inefficient (idle waiting)|✅ Very efficient (threads freed during I/O wait)|❌ Heavier (more memory & context switching)|
|**Good for**|Simple, small apps or step-by-step logic|I/O-bound (e.g., API, DB, file read/write)|CPU-bound (e.g., heavy computation, rendering)|
|**Code complexity**|✅ Very simple|⚠️ Moderate (needs async/await understanding)|❌ More complex (thread safety, race conditions)|
|**Scalability**|❌ Poor|✅ Excellent (handle thousands of I/O tasks easily)|⚠️ Limited (thread count bottlenecks)|

---


| Model              | Concurrency Type            | Blocking?          | Extra Threads?       |
| ------------------ | --------------------------- | ------------------ | -------------------- |
| **Synchronous**    | None                        | ✅ Blocking         | ❌ No                 |
| **Asynchronous**   | ✅ Concurrency               | ❌ Non-blocking     | ❌ Usually not        |
| **Multithreading** | ✅ Concurrency / Parallelism | ❌ Depends on logic | ✅ Yes (more threads) |


## Ex 01:

```csharp
static async Task Main()
    {
        // Create and run an asynchronous task
        Task<int> resultTask = PerformAsyncOperation();
        
        // Do some other work while waiting for the task to complete
        Console.WriteLine("Doing some other work...");


        // Wait for the task to complete and retrieve the result
        int result = await resultTask;


        // Process the result
        Console.WriteLine($"Result: {result}");
    }


    static async Task<int> PerformAsyncOperation()
    {
        // Simulate an asynchronous operation
        await Task.Delay(2000);
        
        // Return a result
        return 42;
    }
```

## Ex 02:

```csharp
 static async Task Main()
    {
     
        Console.WriteLine("Starting tasks...");

        // Start the first task to download and print content length from CNN
        Task task1 = DownloadAndPrintAsync("https://www.cnn.com");
        Console.WriteLine("Task 1 started...");

        // Start the second task to download and print content length from Amazon
        Task task2 = DownloadAndPrintAsync("https://www.amazon.com");
        Console.WriteLine("Task 2 started...");

        // Start the third task to download and print content length PA
        Task task3 = DownloadAndPrintAsync("https://www.ProgrammingAdvices.com");
        Console.WriteLine("Task 3 started...\n");

        // Wait for all tasks to complete
        await Task.WhenAll(task1, task2, task3);

        // Print a message indicating that all tasks have finished execution
        Console.WriteLine("\nDone, all tasks finished execution.");
        Console.ReadKey();
    }

    static async Task DownloadAndPrintAsync(string url)
    {
       
        string content;

        // Using statement ensures that the WebClient is disposed of properly
        using (WebClient client = new WebClient())
        {
            // Simulate some work by adding a delay
            await Task.Delay(100);

            // Download the content of the web page asynchronously
            content = await client.DownloadStringTaskAsync(url);
        }

        // Print the URL and the length of the downloaded content
        Console.WriteLine($"{url}: {content.Length} characters downloaded");
    }

```

## Ex 03:

```csharp
// Custom event arguments class
public class CustomEventArgs : EventArgs
{
    public int Parameter1 { get; }
    public string Parameter2 { get; }

    public CustomEventArgs(int param1, string param2)
    {
        Parameter1 = param1;
        Parameter2 = param2;
    }
}

class Program
{
    // Define a delegate for the callback
    public delegate void CallbackEventHandler(object sender, CustomEventArgs e);

    // Define an event based on the delegate
    public static event CallbackEventHandler CallbackEvent;

    static async Task Main()
    {
        // Subscribe to the event
        CallbackEvent += OnCallbackReceived;

        // Create and run a Task for the asynchronous operation, passing CallbackEvent as a parameter
        Task performTask = PerformAsyncOperation(CallbackEvent);

        // Do some other work while waiting for the task to complete
        Console.WriteLine("Doing some other work...");

        // Wait for the task to complete
        await performTask;

        Console.WriteLine("Done!");
        Console.ReadKey();
    }

    static async Task PerformAsyncOperation(CallbackEventHandler callback)
    {
        // Simulate an asynchronous operation
        await Task.Delay(2000);

        // Create event arguments with two parameters
        CustomEventArgs eventArgs = new CustomEventArgs(42, "Hello from event");

        // Check if the callback event is not null before invoking
        callback?.Invoke(null, eventArgs);
    }

    // Event handler for the CallbackEvent
    static void OnCallbackReceived(object sender, CustomEventArgs e)
    {
        Console.WriteLine($"Event received: Parameter 1 - {e.Parameter1}, Parameter 2 - {e.Parameter2}");
    }

```
