Encapsulation is the concept of **bundling data (attributes) and methods (functions) into a single unit (class)** while restricting direct access to some of it components and exposing a **clean, purpose-driven** interface.

### Encapsulation’s Core Goal

- **Protect data integrity** OR **Data Protection** (e.g., `private` fields).
    
- **Bundle data + methods** that operate on it (a class).
    
- **Simplification**


Data integrity is achieved using **access specifiers**:

- `private`: Only accessible within the class
    
- `protected`: Accessible within the class and derived classes
    
- `public`: Accessible from anywhere

```cpp
class Account {
	private:
	    double balance;  // Hidden complexity
	
	public:
	    Account(double initial) {
	        balance = initial;
	    }
	
	    void deposit(double amount) {
	        if (amount > 0) balance += amount;
	    }
	
	    double getBalance() {
	        return balance; // Read-only access
	    }
};

```

Here, `balance` is encapsulated — external code must use `deposit()` or `getBalance()` to interact with it.

### So, are access specifiers part of encapsulation?

✅ **Yes**, they help enforce it.  
But encapsulation is **more than just access control**—it’s about **designing classes to protect and manage their data responsibly**.


### What Is `ICollection<T>.Add`?

### This method:

```csharp
void ICollection<T>.Add(T item)
```

Means:

> “I am implementing the `Add` method from the `ICollection<T>` interface, but I only want it accessible when using the object as an `ICollection<T>`.”

its also avoid method name clashes in multiple interfaces.

```csharp
list.Add(5); // ❌ Compile-time error
((ICollection<int>)list).Add(5); // Accepted - Affect original object
```