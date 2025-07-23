### 🔷 **Abstract Class**

An **abstract class** is a class that **cannot be instantiated** on its own and is meant to be **inherited** by other classes. It can contain:

- **Abstract methods** (without implementation)
    
- **Concrete methods** (with implementation)
    
- **Fields and constructors**
    

#### Use when:

- You want to **provide common behavior** to multiple related classes
    
- You want to **partially implement** a base class


### 🟩 **Interface**

An **interface** is a contract that specifies a set of **abstract methods** (and since Java 8, **default** and **static methods**) that implementing classes **must provide**.

>Interfaces **cannot have fields** (except constants) and **cannot have constructors**.

> Interface enable you to pass methods as parameter 
#### Use when:

- You want to **specify what a class must do**, not how
    
- You want **multiple inheritance of type**, since a class can implement multiple interfaces

#### **Difference:**

| **Feature**              | **Interface**                                      | **Abstract Class**                                       |     
| ------------------------ | -------------------------------------------------- | -------------------------------------------------------- |  
| **Methods**              | All abstract                                       | Can have both abstract and concrete methods.             |     
| **Multiple Inheritance** | Supported (a class can implement many interfaces). | Not supported (a class extends only one abstract class). |     
| **Fields**               | Only `public static final` constants.              | Can have instance variables (`private`, `protected`).    |     
| **Constructors**         | Not allowed.                                       | Allowed.                                                 |     
| **Purpose**              | Define a _contract_ (what an object can do).       | Share common code among related classes.                 |     
| **keyword**              | implement                                          | extend                                                   |     

#### **Abstract Classes Implementing Interfaces**

When a class **implements an interface** but does **not provide implementations for all the interface's methods**, it must be declared as an **abstract class**. Here's why:

```java
	// Interface with two methods
	interface Flyable {
	    void fly();      // Abstract method (no body)
	    void land();    // Abstract method (no body)
	}
	
	// Abstract class implements Flyable but doesn't provide ALL method bodies
	abstract class Bird implements Flyable {
	    // Implements ONLY fly(), not land()
	    @Override
	    public void fly() {
	        System.out.println("Bird is flying!");
	    }
	    // land() is NOT implemented here → Bird must be abstract
	}
	
	// Concrete subclass completes the contract
	class Sparrow extends Bird {
	    @Override
	    public void land() {
	        System.out.println("Sparrow lands gently.");
	    }
	}
```


### **. Interfaces vs. Classes**

- **Interfaces** can only:
    
    - Extend **other interfaces** (multiple inheritance allowed).
        
    - Declare abstract methods, `default` methods, or constants.
        
- **Classes** (concrete/abstract) can:
    
    - Extend **one class** (single inheritance).
        
    - Implement **multiple interfaces**.


