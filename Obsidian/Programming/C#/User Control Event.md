```csharp
public partial class Calculator : UserControl
{
    public Calculator()
    {
        InitializeComponent();
    }
	// Event Handler Delegate With Paramater
    public event Action<int> onCalculationComplete;
	// Method to trigger event
    protected virtual void CalculationComplete(int res)
    {
        Action<int> handler = onCalculationComplete;
        if (handler != null)
        {
            handler(res);
        }
}
```

### **`Virtual` Modifier**

- This allows derived classes to **override** the method and customize its behavior.
    
- **Why?**
    
    - A child class might need to:
        
        - Add extra logic before/after invoking the event.
            
        - Conditionally suppress the event.
            
        - Log or validate the result (`res`) before propagation.
            

---

### Example: Overriding in a Derived Class


```csharp
class AdvancedCalculator : CalculatorBase  
{
    protected override void CalculationComplete(int res)
    {
        // Custom logic before event
        if (res < 0) 
        {
            Logger.LogWarning("Negative result detected!");
        }

        // Call base implementation (triggers the event)
        base.CalculationComplete(res);  

        // Custom logic after event
        Analytics.Track("CalculationComplete", res);  
    }
```


### Thread-Safety Note

The code uses the `handler = onCalculationComplete` pattern to avoid race conditions if the event is modified mid-invocation. This is a **thread-safe** way to raise events in C#.

```csharp
Action<int> handler = onCalculationComplete;  // Local copy for safety
if (handler != null) handler(res);           // Null check
```


