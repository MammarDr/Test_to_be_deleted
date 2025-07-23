In Object-Oriented Programming, a **class** is a blueprint or template for creating objects. It defines properties (called attributes or fields) and behaviors (called methods or functions) that the objects will inherit.

An **object** is a concrete instance of a class that occupies memory and can perform the operations defined by the class. Each object holds its own copy of the class’s non-static data members(attributes).

## Constructor
A **constructor** is a special method used to **create and initialize an object** of a class.

```cpp
#include <iostream>
using namespace std;

class Car {
public:
    string model; // attribute OR field OR instance variable
    int year;

	Car() {
		this.model = " ";
		this.year = 0000;
	}
	
	Car(Car& oldObj) { 
		this.model = oldObj.model;
		this.year = oldObj.year;
	}

    void drive() {
        cout << "Driving the car..." << endl;
    } // method OR function
};

int main() {
    Car myCar; // object OR instance
    myCar.model = "Toyota";
    myCar.year = 2020;
    myCar.drive();

	Car newCar = myCar; // BOTH POINT TO SAME OBJECT IN HEAP MEMEORY
	Car newCar = new Car(myCar); // CLONE/COPY
}
```

>Here, `myCar` is an object of type `Car`, with its own `model` and `year`.


<h1>Objects in memory</h1>

**Each instance has its own space in memory, only member functions are shared among all objects.**
<br>
![[Pasted image 20250510152440.png]]

A **concrete class** is a class that:

1. **Can be instantiated** (you can create objects of it).
    
2. **Implements all [[Inheritance|Inherited]] abstract methods** (if any).
    
3. **Has no pure virtual functions** (in C++) or unimplemented abstract methods (in Java/C#).


```csharp
object obj = "hello";
```

The **variable type** is `object`, but the **runtime type** is `string`.
- `obj` can't use `string` methods because they don't exist in the runtime-type.
- If a method exists in both `object` and `string` (e.g. `ToString()`), **the string version is called** — thanks to **runtime [[Programing Concepts/Object Oriented Programing/Polymorphism|Polymorphism]]**.

#### **1. What is the difference between a class and an object?**

- **Class**: A blueprint or template that defines the structure (attributes) and behavior (methods) of an object. Example:
 
- **Object**: A concrete instance of a class, created in memory. Example:

**Key Difference**:

- A class is a **logical concept** (design).
    
- An object is a **physical reality** (runtime instance).


```csharp
public class MyEventArgs : EventArgs
{
    public int Value { get; set; } // Must have a public setter
}

// Usage
var args = new MyEventArgs { Value = 42 };
```


### What is a static class in C#?

A `static` class:

- **Cannot be instantiated** (`new` is not allowed).
    
- **Cannot be inherited**.
    
- Can only contain **static members** (methods, properties, fields).
    
- Is often used as a **utility/helper container** (e.g., `Math`, `File`, etc.)

```csharp
public static class MathUtils
{
    public static int Add(int x, int y) => x + y;
}

int sum = MathUtils.Add(3, 4);  // No need to create an object
```


### Anonymous class

```csharp
object anonymous = new { waranaty = "2Years", Color = "Black" }
```


### Constructor chain

```csharp
public MyList() : this(4) { }

public MyList(uint capacity)
{
    EnsureWritable();
    _capacity = capacity;
    _count = 0;
    _items = new T[capacity];
}


var list = new MyList();

```

Output will be:

```csharp
MyList(4) // first
MyList()  // last
```

**the parameterized constructor runs first**, then the body of the parameterless one.