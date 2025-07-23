
### **Java:**

- **All non-static methods are virtual by default.**
    
- That means **every instance method can be overridden** unless you mark it `final` (to prevent overriding) or `static` (which can't be overridden).
    
- So, **polymorphism is the default behavior** in Java for instance methods.
    

---

### **C#:**

- **Instance methods are non-virtual by default.**
    
- To allow overriding and polymorphism, you **must explicitly mark a method as `virtual`** in the base class.
    
- The derived class uses `override` to provide a new implementation.
    
- If you try to define a method with the same name without `virtual` in the base, you can hide it with `new`, but that’s **not polymorphism** — it’s hiding.

### Why this difference?

C# was designed with explicitness and performance in mind:

- Virtual calls have a small runtime cost (because of dynamic dispatch).
    
- So, **C# forces you to opt in to polymorphism** for methods you expect to override.
    

```csharp
class Base
{
    public void Greet()    // NOT virtual by default
    {
        Console.WriteLine("Hello from Base");
    }
}

class Derived : Base
{
    public new void Greet()    // hides base method, but no polymorphism
    {
        Console.WriteLine("Hello from Derived");
    }
}

class Program
{
    static void Main()
    {
        Base b = new Derived();
        b.Greet();              // Prints: Hello from Base  <-- NOT Derived!
    }
}
```

Java decided to make polymorphism the default, so it's easier to override but with less explicit control.

```java
class Base {
    void greet() {              // implicitly virtual in Java
        System.out.println("Hello from Base");
    }
}

class Derived extends Base {
    @Override
    void greet() {              // override is allowed without any base modifier
        System.out.println("Hello from Derived");
    }
}
```