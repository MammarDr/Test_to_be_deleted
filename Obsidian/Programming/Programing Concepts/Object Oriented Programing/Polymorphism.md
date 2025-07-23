Polymorphism (derived from the Greek for "many forms") **where a single interface or method can behave differently based on the object it acts upon**. 

### **Types of Polymorphism**

#### **1. Compile-Time Polymorphism (Static/Early Binding)**

- Resolved during **compilation**.
    
- Includes:
    
    - **Method Overloading** (same method name, different parameters).
        
    - **Operator Overloading** (e.g., `+` for numbers vs. strings).

```java
	class MathOps {
	
    // Overloaded methods (compile-time)
    int add(int a, int b) { return a + b; }
    double add(double a, double b) { return a + b; }
    
	}
```

#### **2. Runtime Polymorphism (Dynamic/Late Binding)**

- Resolved during **execution**.
    
- Achieved via:
    
    - **Method Overriding** (subclass redefines a superclass method).
        
    - **Interface/Abstract Class Implementations**.

### Java:

```java
	class Animal {
    void sound() { System.out.println("Some noise"); }
	}
	
	class Dog extends Animal {
		int age;
	    @Override
	    void sound() { System.out.println("Bark!"); }  // Runtime decision
	}
	
	Animal a = new Dog();
	a.sound();  // Outputs "Bark!"
				// Can't access "age"
```

### C++:

```cpp
	class Set {
    virtual void get() = 0;       // Pure virtual function (makes `Set` abstract)
    virtual void add(int x) = 0;  // Pure virtual function
    virtual void remove(int x);   // Virtual function (not pure)
	};
	
	// `Set` is an abstract class because it has pure virtual functions.
	// Objects of `Set` cannot be created.
	
	// ---
	
	class Tip : public Set {
	    // Derived class that does NOT override all pure virtual functions.
	    // Thus, `Tip` is ALSO an abstract class.
	    // Uncommenting the line below would enforce abstraction:
	    // virtual void required() = 0; 
	};
	
	// ---
	
	class hashSet : public Tip {
	public:
	    void get() override {};         // Overrides `Set::get()` (polymorphism)
	    void get(int x) {};             // Overloads `get()` (compile-time)
	    void add(int x) override {};    // Overrides `Set::add()`
	    void remove(int x) override {}; // Overrides `Set::remove()`
	};
	
	// `hashSet` is a concrete class (all pure virtual functions are implemented).
	
	// ---
	
	// Usage Examples:
	hashSet a;                  // Valid: `hashSet` is concrete.
	
	Set* map = new hashSet();   // Valid: Polymorphism (pointer to base class).
	map->get();                 // Calls `hashSet::get()` at runtime.
	
	// Set s;                   // Error: `Set` is abstract.
	// Tip t;                   // Error: `Tip` is abstract.
```


### **Key Mechanisms**

- **Inheritance**: Subclasses override superclass methods.
    
- **Interfaces**: Multiple classes implement the same method differently.
    
- **Virtual Tables (vtables)**: Used in C++/Java to dynamically resolve method calls.


### **Supported Languages**

|Language|Runtime Polymorphism?|Compile-Time Polymorphism?|
|---|---|---|
|Java/C#|Yes (inheritance)|Yes (overloading)|
|C++|Yes (with `virtual`)|Yes (overloading/operators)|
|Python|Yes (duck typing)|No (but defaults to runtime)|
|JavaScript|Yes (prototypes)|No|

```java
	Animal animal = new Canard();  // Polymorphic assignment
	if (animal instanceof Animal && animal instanceof Canard) { ... }  
	// Type check for current class & super class
```
