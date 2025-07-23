
```csharp
public static Point operator +(Point p1, Point p2)
{
	return new Point(p1.X + p2.X, p1.Y + p2.Y);
}

public static Point operator -(Point p1, Point p2)
{
	return new Point(p1.X - p2.X, p1.Y - p2.Y);
}

// Overloading the == operator for fraction equality
public static bool operator == (Point p1, Point p2)
{
	return (p1.X == p2.X) && (p1.Y == p2.Y);
}

// Overloading the != operator for fraction equality
public static bool operator !=(Point p1, Point p2)
{
	return (p1.X != p2.X) || (p1.Y != p2.Y);
}

// Overriding ToString for better readability
public override string ToString()
{
return $"({X}, {Y})";
}
```


> == and != must be together

### **Can I overload all operators in C#?**  

No, you cannot overload all operators in C#. There are certain operators that you can't overload, and some that have restrictions on their overloading. Here are some points to keep in mind:

####  Operators You Cannot Overload:

`&&` (conditional AND)

`||` (conditional OR)

`?:` (conditional)

`sizeof` (size of)

`typeof` (type of)

`->` (member access)

`.` (member access)

`checked` and `unchecked`

####  Operators with Restrictions:
`=` (assignment) can be overloaded only by defining a user-defined conversion to the type of the left-hand operand.
`?:` (conditional) can be overloaded only if the types of the second and third operands are the same.

####  Unary Operators Restrictions:
`+` (unary plus) and `-` (unary minus) cannot be overloaded for `bool`, `byte`, `sbyte`, `ushort`, `short`, `uint`, `int`, `ulong`, or `long`.


```csharp
public class MyNumber
{
    public int Value;

    public MyNumber(int value)
    {
        Value = value;
    }

    // Unary minus
    public static MyNumber operator -(MyNumber x)
    {
        return new MyNumber(-x.Value);
    }

    // Unary plus
    public static MyNumber operator +(MyNumber x)
    {
        return new MyNumber(+x.Value);
    }

	// Error - must not be primitive 
	public static MyNumber operator +(int x)
    {
        return new MyNumber(x);
    }
}
```

####  Binary Operators Restrictions:
The assignment operators (`+=`, `-=`, `*=`, `/=`, and so on) can be overloaded, but their behavior is determined by the behavior of the corresponding binary operator.


You cannot overload `+=`, `-=`, etc.. directly.

#### ❌ Not Valid:

```csharp
public static MyClass operator +=(MyClass a, MyClass b) // ❌ Invalid
```

#### ✅ Valid:

```csharp
public static MyClass operator +(MyClass a, MyClass b) {     
	return new MyClass(a.Value + b.Value); 
}
```


Then `+=` will work **implicitly**:

> myObj1 += myObj2;


####  Equality and Comparison Operators Restrictions:

 The `==` and `!=` operators must be overloaded together. The same applies to `<`, `>`, `<=`, and `>=` when overloading.

```csharp
public static bool operator ==(MyType a, MyType b) => a.Value == b.Value;
public static bool operator !=(MyType a, MyType b) => a.Value != b.Value;
```

> ⚠️ You should also override `Equals()` and `GetHashCode()`.

if not, it can inconsistency break things like `Dictionary`, `HashSet`, or LINQ methods like `.Distinct()`.

```csharp
public override bool Equals(object obj)
    {
        if (obj is MyType other)
            return this.Value == other.Value;
        return false;
    }
    
public override int GetHashCode() => Value.GetHashCode();
```

####  Indexing Operator Restrictions:

The `[]` operator can only be overloaded if the containing type is an array or an indexer property.

```csharp
public class MyCollection
{
    private int[] data = new int[10];

    // Indexer property
    public int this[int index]
    {
        get => data[index];
        set => data[index] = value;
    }
}

var col = new MyCollection();
col[0] = 42;              // ✅ uses indexer
Console.WriteLine(col[0]); // Output: 42
```
####  Conversion Operators Restrictions:

`implicit` and `explicit` conversion operators can be overloaded with certain restrictions. They must involve user-defined types and follow specific rules.


```csharp
public class MyType
{
    public int Value;

    // Implicit conversion from int to MyType
    public static implicit operator MyType(int val) => new MyType { Value = val };

    // Explicit conversion from MyType to int
    public static explicit operator int(MyType obj) => obj.Value;
}

// Implicit conversion: int → MyType
    MyType a = 42;  // No cast needed
    Console.WriteLine($"a.Value = {a.Value}");  // Output: a.Value = 42

    // Explicit conversion: MyType → int
    int b = (int)a;  // Cast is required
    Console.WriteLine($"b = {b}");  // Output: b = 42

    // You can also go back and forth
    MyType c = 100;
    int d = (int)c;
    
```