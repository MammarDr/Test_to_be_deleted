
> Delegates are immutable in C#

> Delegates enable you to pass methods as parameter 
##### **How to Create a Custom Delegates in C#?**

When it comes to creating a custom delegate, is a very simple process. So, a custom delegate can be defined by using the delegate keyword. And it is a kind of magic keyword because behind the scene, when the compiler sees the delegate keyword, it actually generates a class that inherits from some other .NET Framework delegate classes and that will be covered in a moment.

The syntax to create a delegate in C# is very much like the abstract method declaration. In abstract method declaration, we use the abstract keyword whereas, in delegate, we need to use the delegate keyword.

```csharp
// Delegate is a blueprint for the method
delegate <Return Type>    <Delegate Name>        (       Parameter List       );
delegate      void      WorkPerformedHandler     (int hours, WorkType workType);
```

The delegate signature and handler method signature must be matched. As we have defined the delegate with two parameters of int and WorkType type. Now, if the handler method wants to receive the data from the pipeline, then the handler must have the same number, type, and order of parameters as the delegate.
### **What Does "Signature" Mean in Delegates and Event Handlers?**

In C#, a **method signature** or **delegate signature** refers to the **unique identifier of a method** based on:

1. **Return type** (e.g., `void`, `int`, `string`)
    
2. **Method name** (e.g., `OnClick`, `CalculateTotal`)
    
3. **Parameter types and order** (e.g., `(string, int)` vs. `(int, string)`)

```csharp
	delegate int myDelegate(int x);
	
	public int call(int c) {...}
	
    sq1 myDelegate = new sq1(call);
	  // OR
	sq1 myDelegate = call;
	
	myDelegate(5);

//  # multicast delegate

	/* myDelegate = method || NULL; - If not initiated */ 
	myDeleage += (x , y) => x + y; // myDelegate require a value before ** += ** 
```

[Keep going](https://dotnettutorials.net/lesson/delegates-csharp/)


# **Types of delegates :**

## 🔄 **Delegation (concept)**

**Delegation** is a design principle or pattern where one object hands off (or “delegates”) responsibility for a task to another object.

> Think: “I’ll let someone else do this on my behalf.”

---

## 🧑‍💼 **Delegator**

The **delegator** is the object that **passes responsibility** to another object.

- It doesn't do the work itself.
    
- Instead, it calls methods on another object (the delegate) to do the job.
    

**Example:**  
In an event system, the **publisher** is often the **delegator** — it lets subscribed handlers (delegates) do the work.

---

## 🧑‍🔧 **Delegate**

The **delegate** is the object or method that **performs the work** on behalf of the delegator.

- In C#, a `delegate` is also a **type-safe function pointer** — but in the broader design pattern sense, the **delegate** is the receiver of the work.


#Logger

```csharp
public class Logger
{
	// This defines a **type** that can **reference any method** that
	// matches this signature
    public delegate void LogAction(string message);

    private readonly LogAction _log;

	// function pointer recieve methods
    public Logger(LogAction action)
    {
	    // only assigning `_log` **once**, inside the constructor — readonly
        _log = action ?? throw new ArgumentNullException(nameof(action));
    }

    public void Log(string message)
    {
	    // Event Raiser
        _log?.Invoke($"[{DateTime.Now}] {message}");
    }
}


void SystemLog(string message) {...}

Logger sysLog = new Logger(SystemLog); // ← Subscriber added to delegate
sysLog.Log("Error");
```

> The `?.` ensures no crash if there are no subscribers

#Multicast-Delegate
In C#, a multicast delegate is a special type of delegate that can **reference multiple methods** **and invoke them in a single call**.

```csharp
	private LogAction _log;

	_log += action ?? throw new ArgumentNullException(nameof(action));

```

the compiler **creates a new delegate object** that:

1. Contains all the methods from the current `_log` invocation list.
    
2. Adds the new `action` to the end.
    
3. Assigns this new delegate back to `_log`.


#Func-Delegate 
The `Func` delegate is a predefined delegate type in C# (default delegate) that represents a method that takes zero or more input parameters and returns a value.

- `Func<TResult>`: Represents a method that takes no parameters and returns a result of type `TResult`.

- `Func<T1, T2, ..., Tn, TResult>`: Represents a method that takes n parameters of types `T` and returns a result of type `TResult`.
	- Func<int, int>
	- Func<string, int, bool>
	- Func<double, double, double, double>


```csharp
	delegate int sq1(int x);
	Func<int> SQ2 = approve;
    Func<double, int> SQ3 = decline;
    Func<int, int, int> SQ4;

	int call(int x) {...}

	int approve()   {...}

	int decline(double x) {...}

	int add(int x, int y) {...}

    sq1 SQ1 = new sq1(call);
	SQ1(5);

	sq2();

	sq3(5.5);

	SQ4 = add;
	SQ4(2, 0);
```

### Key points:

- Use `delegate` only **to declare new delegate types**. 


#Action-Delegate
In simple words: it's a shortcut for normal delegate like the Func Delegate but without returning value.

```csharp
Action myAct1on = remove;
Action<int> myAct2on = disply;
Action<double, double> myAct3on = add;

myAct1on();
myAct2on(5);
myAct3on(0.4, 1);

public void remove()                {...}
public void disply(int x)           {...}
public void add(double x, double y) {...}
```

#Predicat-Action
In simple words: it's a shortcut for a delegate function that takes one parameter and return boolean.

```csharp
Predicate<int> myPredicate = isExist;

myPredicate(5);

public bool isExist(int x) {...}
```


## Clarify more about New & Old:


```csharp
public event Action<string, T>? ItemChanged;
```

Or more formally:

```csharp
public delegate void CollectionChangedEventHandler(string action, T? item);
public event CollectionChangedEventHandler? CollectionChanged;
```


### 🔹 1. **Delegate Declaration**
```csharp
public delegate void CollectionChangedEventHandler(string action, T? item);
```


This is like defining:

- A **"function contract"** (just like an interface or abstract method)
    
- Any method matching this signature:
    

```csharp
void Something(string action, T? item)
```


...can be used with this delegate.

---

### 🔹 2. **Event Declaration**

```csharp
public event CollectionChangedEventHandler? CollectionChanged;
```

This:

- Declares a field named `CollectionChanged`
    
- Its **type is the delegate**