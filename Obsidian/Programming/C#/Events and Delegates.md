
##### **What is an Event?**

Events are notifications. In simple words, we can say that it’s a message that goes out to one or more people (we can also say them as subscribers) who are listening. Obviously, it’s not going to be people in .NET, it will be objects.

![[Pasted image 20250526124552.png]]

![[Pasted image 20250526124625.png]]

![[Pasted image 20250526124401.png]]

# **Role of Delegates in C#:**
>the delegate is nothing but the pipeline between an Event Raiser and an Event Handler.

### **What is a Delegate Exactly in .NET Framework?**
- A **delegate** is a **type-safe function pointer**—it holds a reference to a method with a specific signature.
    
- Acts as a **"pipeline"** between an **event raiser** (publisher) and **event handler** (subscriber).
    

#### **2. Key Concepts**

- **Function Pointer** → Points to a method, allowing indirect invocation.
    
- **Multicast Delegate** → The underlying .NET class that supports **multiple subscribers**.
    
    - Maintains an **invocation list** (a list of subscribed methods).
        
    - When an event is raised, **all methods in the list are called in order**.

```csharp
// 1. Define delegate  
public delegate void ProcessOrderDelegate(Order order);  

// 2. Create event  
public event ProcessOrderDelegate OrderProcessed;  

// 3. Subscribe methods  
OrderProcessed += SendConfirmationEmail;  
OrderProcessed += UpdateInventory;  

// 4. Trigger event  
OrderProcessed?.Invoke(new Order());
```
#### **> Key Takeaways**

✔ Delegates **enable events** by connecting publishers and subscribers.  
✔ **MulticastDelegate** handles **multiple subscribers** via an invocation list.  
✔ Delegates ensure **type safety**—only methods matching the signature can subscribe.  
✔ Used extensively in **events, callbacks, and LINQ**.


![[Pasted image 20250526125941.png]]

**Now,** the reason we call delegate as **a function pointer** is because an event handler in C# is a function or in the **.NET Framework term,** we call it a method. And the delegate is pointing to the event handler or you can say **point to a function** and when the delegate is called, the function or method or event handler is pointing is going to be executed and this is the reason why we call it a **function pointer.**

![[Pasted image 20250526131950.png]]

>So, you can see that, without delegates, events don’t have a lot of purposes. Even if you fire an event on its own without a delegate, you don’t have a way to get data from Point A to Point B.

## **Event Handler :**
Event Handler is a method in C#, that is responsible for receiving and processing the data it gets from the delegate.

![[Pasted image 20250526132620.png]]

> **void** method(**object** sender, **EventArgs** e);

![[Pasted image 20250526132802.png]]


```csharp
// MY EVENT ARGS
public class CalculationCompleted : EventArgs
{
    public int val1 { get; set; }
    public int val2 { get; set; }
}

// MY DELEGATE
public event EventHandler<CalculationCompleted> onCalculationComplete;

// MY EVENT RAISER
protected virtual void CalculationComplete(int v1, int v2)
{
    CalculationComplete(new CalculationCompleted { val1 = v1, val2 = v2 });
}

protected virtual void CalculationComplete(CalculationCompleted e)
{
    onCalculationComplete?.Invoke(this, e);
}
```


```csharp
public class TemperatureChangedEventArgs : EventArgs
{
    public double oldTemperature { get; set; }
    public double newTemperature { get; set; }  
    public double diff { get; set; }   

}

public class Thermostat
{
    public event EventHandler<TemperatureChangedEventArgs> TemparatureChanged;

    private double oldTemperature = 0;
    private double currTemperature = 0;

    public void SetTemperature(double newTemperature)
    {
        if(newTemperature != oldTemperature)
        {
            oldTemperature = currTemperature;
            currTemperature = newTemperature;
            OnTemperatureChanged(oldTemperature, currTemperature);

        }
    }

    private void OnTemperatureChanged(double oldTemp, double newTemp)
    {
        OnTemperatureChanged(new TemperatureChangedEventArgs
         { oldTemperature = oldTemp,
           newTemperature = newTemp,
           diff = (newTemp - oldTemp)
        });
    }

    private void OnTemperatureChanged(TemperatureChangedEventArgs e)
    {
        TemparatureChanged?.Invoke(this, e);
    }
}

public class Display
{
    public void Subscribe(Thermostat obj)
    {
        obj.TemparatureChanged += HandleTemperatureChange;
    }

    public void HandleTemperatureChange(object sender, TemperatureChangedEventArgs e)
    {
        Console.WriteLine("Thermotast LA/2");
        Console.WriteLine("New Temperature " + e.newTemperature);
        Console.WriteLine("Old Temperature " + e.oldTemperature);
        Console.WriteLine("Difference " + e.diff);
    }
}

public class Program
    {
        static void Main(string[] args)
        {
	        Thermostat thermostat = new Thermostat();
	        Display display = new Display();
	        display.Subscribe(thermostat);
	        th.SetTemperature(20);
        }
    }
```
# *References:*
[Events, Delegates, and Event Handlers in C#](https://dotnettutorials.net/lesson/events-delegates-and-event-handler-in-csharp/)