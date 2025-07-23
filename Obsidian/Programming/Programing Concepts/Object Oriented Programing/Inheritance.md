**Inheritance** enables a class (derived class) to inherit members and behaviors from another class (base class). 


##### **What is the advantage of inheritance?**
1. **Code Reusability**: Avoid duplication by inheriting fields/methods from a parent class
    
2. **Polymorphism**: Treat objects uniformly (e.g., `Animal animal = new Dog(); animal.sound()`).
    
3. **Logical Hierarchy**: Models real-world relationships (e.g., `Vehicle` → `Car` → `ElectricCar`).

Types of inheritance in C++:

- Single
    
- Multiple
    
- Multilevel
    
- Hierarchical
    
- Hybrid (combination)

<br>

![[Pasted image 20250510163539.png]]

Access types (`public`, `protected`, `private`) determine the visibility of inherited members.

![[Pasted image 20250510163505.png]]

| Inheritance Type      | Private Members | Protected Members | Public Members |
| --------------------- | --------------- | ----------------- | -------------- |
| Public Inheritance    | Inaccessible    | Protected         | Public         |
| Private Inheritance   | Inaccessible    | Private           | Private        |
| Protected Inheritance | Inaccessible    | Protected         | Protected      |

|Modifier|Access Level|
|---|---|
|`private`|Only within the class itself|
|`protected`|Class itself + subclasses + classes in the same package|

### Friend Class/Function

o   A friend function in C++ is defined as a function that can access private, protected and public members of a class.
```cpp
	class mainClass {
		private:
			static int x;

		friend class relative;
		friend int print(mainClass& obj);
	}

	class relative {
		int y = mainClass::x;
		void display(mainClass& obj) {
			obj.x = 10;
		}
	};

	relative c1;
	c1.x; // FORBIDDEN
		  // IF ANOTHER CLASS INHERIT rlative
		  // THEY CANT ACCESS mainClass x

	int print(mainClass& obj) {
		return obj.x;
	}  // FRIEND FUNCTION
```


### Virtual - Abstract Function :

- A virtual function is a member function in the base class that we expect to redefine in derived classes.

- Basically, a virtual function is used in the base class in order to ensure that the function is overridden. This especially applies to cases where a pointer of base class points to an object of a derived class.

```cpp
	class clsPerson {
		public virtual void Print() { cout << "I'm Person!"; }
	};

	class clsEmpolyee : public clsPerson {
		public void Print() { cout << "I'm Employee!"; }
	}

	// Early-Static Binding
	clsEmployee Employee;
	Employee.Print(); // I'm Employee

	// Late-Dynamic Binding
	clsPerson* Person = &Employee;
	Person->Print(), // I'm Person!
```


#### **Is Derived class able to not explicitly call super class's constructor ?**

- If a subclass constructor does **not explicitly call** the superclass constructor, the superclass constructor is **implicitly called**
<br>

```cpp
	class Animal {
		int val() { return 1; }
	};
	
	class Dog : public Animal {
	public:
	    Dog() {
	        cout << "Dog constructor\n";
	    }
	};
```

- `Animal` has **no user-defined constructor**, so the compiler provides a **default constructor**.
    
- `Dog` inherits from `Animal`, and its constructor **implicitly calls `Animal`'s default constructor** before executing its own body.
    
- Since there are **no special initialization requirements**, the code compiles and works fine.


- A `ClassCastException` occurs during **explicit casts** (e.g., `(Subclass) superclassObj`), not implicit conversions.
    
- Implicit conversions happen with primitive types (e.g., `int` to `double`), not objects.


### `final` classes block inheritance:

```java
	final class ImmutablePerson {
	    private final String name;  // Immutable field
	
	    public ImmutablePerson(String name) {
	        this.name = name; // Set once (in constructor)
	    }
	
	    public String getName() {
	        return name; // Read-only access
	    }
	}
```
### **Key Implications**

✅ **Thread-Safe**: Immutable fields are automatically thread-safe.  
✅ **Predictable State**: The field’s value never changes after creation.  
✅ **Cache-Friendly**: Safe to use as a key in `HashMap` or in `HashSet`


### **Prototypal Inheritance vs. Classical Inheritance**

|Feature|**Prototypal Inheritance (JavaScript)**|**Classical Inheritance (C#, Java, C++)**|
|---|---|---|
|**Core Mechanism**|Objects inherit directly from other objects (prototypes).|Classes inherit from other classes (blueprint-based).|
|**Syntax**|Uses `Object.create()` or constructor functions.|Uses `class` and `extends` keywords.|
|**Inheritance Model**|Dynamic, runtime-based.|Static, compile-time-based.|
|**Flexibility**|More flexible—objects can modify prototypes at runtime.|Less flexible—structure is fixed after compilation.|
|**Performance**|Slightly slower due to dynamic lookup.|Faster due to static optimization.|
|**Example**|`js const parent = { greet() { console.log("Hi!"); } }; const child = Object.create(parent); child.greet(); // "Hi!"`|`csharp class Parent { public void Greet() => Console.WriteLine("Hi!"); } class Child : Parent {} var child = new Child(); child.Greet(); // "Hi!"`|

### **Key Differences**

1. **Prototypal (JavaScript)**
    
    - Objects **link to prototypes** (not classes).
        
    - Supports **dynamic inheritance** (modify prototypes on the fly).
        
    - No formal classes (ES6 `class` is syntactic sugar over prototypes).
        
2. **Classical (C#, Java, C++)**
    
    - Classes **define rigid hierarchies**.
        
    - Requires `extends` for inheritance.
        
    - No runtime changes to inheritance chains.
        

### **When to Use Which?**

- **Prototypal**: Dynamic apps (e.g., UI frameworks, plugins).
    
- **Classical**: Large-scale OOP systems needing strict structure. 



### Check if a Class inherit another Class

```csharp
public abstract class Comparer<TKey>
{
    public static Comparer<TKey> Default
    {
        get
        {
        /*
        ** Can an object of TKey be assigned to a variable of type IComparable<TKey>
        ** Aka..
	    ** Does TKey implement IComparable<TKey>
        */
            if (typeof(IComparable<TKey>).IsAssignableFrom(typeof(TKey)))
                return new GenericComparer<TKey>();
            else if (typeof(IComparable).IsAssignableFrom(typeof(TKey)))
                return new ObjectComparer<TKey>();
            else
                throw new InvalidOperationException
                ($"Type '{typeof(TKey)}' must implement IComparable 
                or IComparable<{typeof(TKey).Name}>");
        }
    }

    public abstract int Compare(TKey x, TKey y);
}

public class GenericComparer<TKey> : Comparer<TKey> 
{
    public override int Compare(TKey x, TKey y)
    {
        if (x == null)
        {
            return y == null ? 0 : -1;
        }

        return ((IComparable<TKey>)x).CompareTo(y);
    }
}

public class ObjectComparer<TKey> : Comparer<TKey>
{
    public override int Compare(TKey x, TKey y)
    {
        if (x == null)
        {
            return y == null ? 0 : -1;
        }

        return ((IComparable)x).CompareTo(y);
    }
}
```

> since any class inherit Comparer<TKey\> is forced to implement Compare(TKey, TValue) {}
   we are essentially making sure the target class implemented the behavior we asked for.

### Constraint

```csharp
class MySortedList<TKey, TValue>
: IDictionary<TKey, TValue>, IDictionary, IReadOnlyDictionary<TKey, TValue>
where TKey : notnull 

// Nullable datatypes are not desired
```

