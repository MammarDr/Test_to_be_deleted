Abstraction is the concept of hiding complex implementation details and exposing only the essential features of an object. It focuses on what an object does rather than how it does it.

This is achieved using **access specifiers**:

- `Abstract Classes`   (partial implementation)
    
- `Interfaces`              (pure behavior contracts)
    
- `Methods`                     (hiding logic behind a name, like `calculate tax())
### Key Points:  

1. **Simplifies Complexity**:
    
    - Abstraction lets you work with high-level concepts (e.g., "start the engine") without worrying about low-level details (e.g., spark plugs, fuel injection).
        


<br>

```cpp
// Abstraction via an interface
interface Vehicle {
    void start();  // What it does (essential feature)
    void stop();   // How it's done is hidden
}

class Car implements Vehicle {
    public void start() { 
        System.out.println("Turn key, ignite engine");  // Hidden detail
    }
    public void stop() { 
        System.out.println("Press brake, shut off engine"); 
    }
}

// User only needs to know 'start()', not how it works
Vehicle myCar = new Car();
myCar.start();  // Focus on WHAT, not HOW
```

<br>

### Abstraction vs. Encapsulation:

- **[[Encapsulation]]**: Protects data by controlling access (hiding **data**).
    
- **Abstraction**: Simplifies behavior by hiding **implementation**.

### Access modifiers:
 **Access modifiers** (like `private`, `public`, `protected`) are primarily **tools for encapsulation**, but they can _indirectly support_ abstraction. Here's the breakdown:

```cpp
	class BankAccount {
    private double balance;  // Encapsulated (hidden) data

	// Abstraction: Exposes WHAT (not HOW)
    public void deposit(double amount) {  // Encapsulation: Controlled access
        if (amount > 0) toUserAccount(amount); // Hidden Complexity
    }

	private void toUserAccount(double amount) { // Hiding "HOW"
		this.balance += amount;
	}
}
```