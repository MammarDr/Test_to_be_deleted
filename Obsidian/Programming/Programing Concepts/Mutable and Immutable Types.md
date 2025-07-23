
## Mutable and Immutable :

#### • In C#, types are classified as either mutable or immutable based on whether their instances can be modified after they are created.

#### • Understanding the difference between mutable and immutable types is crucial for designing robust and maintainable code.


## 🔄 Mutable Types

**Definition:**  
Mutable types are types whose instances **can be modified** after they are created.

### 🔹 Characteristics:

- Fields or properties **can be changed**.
    
- Changes affect the current instance's state.
    
- Often used in scenarios requiring frequent updates.
    

### 🔹 Examples:

- Classes
    
- Arrays
    
- Custom objects with settable properties
    

---

## 🔒 Immutable Types

**Definition:**  
Immutable types are types whose instances **cannot be modified** after creation.

### 🔹 Characteristics:

- Fields or properties are **read-only**.
    
- Any "modification" creates a **new instance**.
    
- Promotes safety and predictability.
    

### 🔹 Examples:

- `string`
    
- `System.Tuple<>`
    
- Primitive value types (`int`, `double`, etc.)
    
- Custom objects with all fields set as `readonly` or `get-only` (e.g., an immutable `Person` class)
    

---

## ⚖️ Pros and Cons

### 🟢 Mutable Types

**Pros:**

- More flexible for state changes
    
- Potentially more memory-efficient in state-heavy applications
    

**Cons:**

- Risk of unintended side effects
    
- Can be harder to track and debug state
    

### 🟢 Immutable Types

**Pros:**

- Safer and less error-prone
    
- Easier to reason about, test, and maintain
    

**Cons:**

- Every change requires creating a new instance
    
- Can lead to higher memory usage in some scenarios
    

---

## 🧭 Guidelines

- **Favor Immutability**:  
    When possible, use immutable types to avoid bugs from unexpected state changes.
    
- **Use Mutability When Needed**:  
    For scenarios involving frequent updates or performance-critical operations, mutability can be more practical.


## StringBuilder Vs String

```csharp
Stopwatch stopwatch = new Stopwatch();

stopwatch.Start();
ConcatenateString(200000);
stopwatch.Stop();

Console.WriteLine(stopwatch.ElapsedMilliseconds); // 4701 ms

stopwatch.Restart();
ConcatenateStringBuilder(200000);
stopwatch.Stop();

Console.WriteLine(stopwatch.ElapsedMilliseconds); // 1ms
```

