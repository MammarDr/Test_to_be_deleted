#### Reflection in C# refers to the ability of a program to inspect its own structure, metadata, and behavior at runtime.

Reflection in C# refers to the ability of a program to inspect, query, and interact with the metadata of types and members in an assembly at runtime. With reflection, you can obtain information about types, fields, properties, methods, and other members of your code dynamically, without knowing them at compile time.

With reflection, you can dynamically load assemblies, examine and create types, and invoke methods or access properties, all without knowing them at compile-time. It provides a way to manipulate types, objects, and members dynamically.

  
> Reflection is used to inspect private members in run-time

#### Key aspects of reflection in C# include:

1. Type Information: You can retrieve information about a type, such as its name, namespace, methods, properties, fields, events, and more.
2. Instantiation: You can create an instance of a type dynamically at runtime using the `Activator.CreateInstance` method.
3. Method Invocation: You can invoke methods on an object dynamically, even if you don't know the method at compile time, using the `Invoke` method.
4. Property and Field Access: You can get and set the values of properties and fields dynamically.

Reflection is powerful but comes with some trade-offs:

- Performance: Reflection can have a performance overhead compared to statically typed code because it bypasses some of the optimizations performed by the compiler.
- Type Safety: Since reflection allows dynamic interaction with types, there's a risk of runtime errors if the code is not carefully designed.

  

**Practical uses of reflection:**

- One practical use of reflection in C# is in the development of frameworks and tools where the structure of types is not known at compile time. Reflection enables these frameworks to dynamically discover and interact with types provided by user code. One such example is in the development of dependency injection containers.
- Another practical use of reflection in C# is in the development of serialization and deserialization frameworks. These frameworks convert objects into a format that can be easily stored, transmitted, or reconstructed, and reflection is often used to dynamically inspect and manipulate the structure of objects.
- Another practical use of reflection in C# is in the development of unit testing frameworks. Unit testing frameworks often utilize reflection to dynamically discover and execute test methods within test classes.
- Another practical use of reflection in C# is in the development of data access frameworks or Object-Relational Mapping (ORM) tools. Reflection can be employed to dynamically map object properties to database fields, allowing for flexible and generic data access without explicit knowledge of the underlying database schema.
- Another practical use of reflection in C# is in the development of user interface frameworks or libraries that need to dynamically generate or bind UI elements based on the properties of data objects.
- Another practical use of reflection in C# is in the implementation of extensibility mechanisms, where the application can dynamically discover and load extensions or plugins at runtime without having to know about them at compile time.
- Another practical use of reflection in C# is in the development of attribute-based validation frameworks. By utilizing custom attributes and reflection, you can create a system where validation rules are associated with class properties, making it easier to define and enforce data validation rules.
- Another practical use of reflection in C# is in the development of code generation tools or frameworks. Reflection allows you to inspect and analyze the structure of types at runtime, and this capability is often leveraged in code generation scenarios.
- Another practical use of reflection in C# is in certain design patterns.
- Reflection can also be utilized in the context of documentation generation, where you dynamically extract information about types, methods, properties, and other elements in your codebase to generate documentation automatically.
- and may other uses :-)

# Type

In .NET, **each data type has exactly one `Type` instance** that describes it at runtime.

### Why only one instance per type?

Because `System.Type` holds **metadata** about the type:

- Its name
    
- Its properties
    
- Its base type
    
- Methods, fields, etc.
    

That information doesn’t change, so there’s no need to recreate it every time.

### You can prove it:

```csharp
Type t1 = typeof(string); 
Type t2 = "hello".GetType();
Console.WriteLine(object.ReferenceEquals(t1, t2)); // True ✅
```

# GetType()

### **Every object in .NET carries its type metadata at runtime**

When the CLR allocates an object (like a `string`, `int[]`, `Person`, etc.), it stores a **hidden reference to the `Type`** in the object’s internal structure (called the "method table pointer" or "type handle").

This is built right into the memory layout of every object.

```csharp
object x = "hello";
Console.WriteLine(x.GetType());  // Outputs: System.String

Type str = container.GetType("System.String");
```

Even though `x` is typed as `object`, the actual memory contains metadata that says: "I'm a string," and `GetType()` reads that.


# TypeOf

In C#, `typeof(Person)` is an operator used to get the **`Type` object** that represents the `Person` class at runtime.

```csharp
Type personType = typeof(Person);
Console.WriteLine(personType.FullName); // e.g., "MyNamespace.Person"
```

## 🔍 When to Use `typeof(...)`

| Use Case                                       | Example                                    |
| ---------------------------------------------- | ------------------------------------------ |
| **Reflection** – get info about class members  | `personType.GetProperties()`               |
| **Attributes** – pass a type to an attribute   | `[XmlInclude(typeof(Student))]`            |
| **Comparisons** – check types at runtime       | `if (someObj.GetType() == typeof(Person))` |
| **Generic constraints** – access type metadata | `Activator.CreateInstance(typeof(T))`      |

## 🔄 `typeof(Person)` vs `instance.GetType()`

|Feature|`typeof(TypeName)`|`instance.GetType()`|
|---|---|---|
|**When?**|**Compile-time**|**Runtime**|
|**Input**|A **type name**|An **object instance**|
|**Output**|A `Type` instance|A `Type` instance|
|**Example**|`typeof(string)` → `System.String`|`"hi".GetType()` → `System.String`|
|**Use Case**|When you already **know the type**|When you want to **inspect an object**|
|**Static/Dynamic**|Static type info|Dynamic (actual) type info|
## Example: Using with Reflection

```csharp
Type type = typeof(Person);
foreach (var prop in type.GetProperties())
{
    Console.WriteLine(prop.Name);
}
```


concept of `typeof` (getting a type or class information) **exists in many programming languages**, although the **syntax and usage vary**.


## 🧠 what's going on in this example?

```csharp
object obj = new { Warranty = "2 years", Color = "Black" };

obj.GetType() != typeof(object) // True

<AnonymousType>{ string Warranty, string Color } // Actual Type
```


- **Declared type**: `object`
    
- **Actual runtime type**: a **compiler-generated anonymous class** with two properties.
    

# Assembly

`Assembly` is the compiled unit (DLL or EXE) which is like a **container** or **package** that holds many types, including `System.String`.

```csharp
Assembly asm = typeof(string).Assembly; // hold String DLL
Console.WriteLine(asm.FullName); // Shows assembly name
Console.WriteLine(asm.Location); // Shows file path (e.g., System.Private.CoreLib.dll)
```

### Internally:

When you use `typeof(string).Assembly`, the .NET runtime gives you a handle to the loaded assembly _in memory_. It's already been loaded and parsed by the Common Language Runtime (CLR), so you're dealing with a high-level object, not raw DLL structures.

If you're looking to load an assembly from a file manually, you can use:

```csharp
Assembly loaded = Assembly.LoadFrom("path_to_dll.dll");
```


Now, about this line:

```csharp
Type stringType = asm.GetType("System.String");
```

### What does it do?

- It asks the `asm` assembly (the container) to **find and return the `Type` object** that corresponds to the type named `"System.String"`.
    
- The `Type` object describes the metadata of the class — such as its methods, properties, constructors, base class, interfaces, etc.

### In summary:

- `Assembly` holds many type objects.
    
- `GetType("System.String")` lets you retrieve the `Type` object for `System.String` **from the assembly**, so you can work with it dynamically.
    
- This is useful for **reflection**, **dynamic loading**, **plugins**, or situations where types are unknown at compile time.


```csharp
// Both represent the same type:
Type t1 = typeof(string);
Type t2 = typeof(string).Assembly.GetType("System.String");

// They are equal:
Console.WriteLine(t1 == t2);  // True
```


## Display all string methods

```csharp

string GetParameterList(ParameterInfo[] parameters)
    {
        return string.Join(", ", parameters.Select(parameter => 
						        $"{parameter.ParameterType} {parameter.Name}"));
    }
   
Assembly asm = typeof(string).Assembly;

Type stringType = asm.GetType("System.String");


 if (stringType != null)
 {
     Console.WriteLine($"Methods of the System.String class:\n")
     // Get all public methods of the System.String class
     var stringMethods = 
     stringType.GetMethods(BindingFlags.Public  |  BindingFlags.Instance)
               .OrderBy(method => method.Name)
     foreach (var method in stringMethods)
     {
         Console.WriteLine($"\t{method.ReturnType}
			              '{method.Name}'
		                  '({GetParameterList(method.GetParameters())})");'
     }
 }
 else
 {
     Console.WriteLine("System.String type not found.");
     Console.ReadKey()

  } 
```

## Resolved at runtime:

```csharp
Assembly asm = Assembly.LoadFrom("MyPlugin.dll");
string typeName = GetTypeNameFromConfig(); // e.g. "MyPlugin.Foo"
Type pluginType = asm.GetType(typeName);

if (pluginType != null) {
    // create instance, invoke methods, etc.
}
```

Here:

- You only know the assembly (`MyPlugin.dll`), loaded dynamically
    
- You don't know the types inside it until runtime
    
- You use `GetType(string)` with a _variable_ type name to find types

## Full Implementation

```csharp

namespace Crypto {

public class baseClass
    {

        public int baseINT;

        public string baseStr {  get; set; }

        public void baseMethod()
        {
            Console.WriteLine("base method has been triggered\n");
        }
    }

public class MyClass : baseClass
{
    public int field;  <=== Field - GetField()
    
    public int Id { get; set; }   <==== Property - GetProperty
    public string Name { get; set; }
    public string Description { get; set; }

    public bool IsPublic { get; set; }

    public void method1() <=== Field - GetMethod()
    {
        Console.WriteLine("method1 has been triggered\n");
    }

    public void method2(int provider, bool somalia)
    {
        Console.WriteLine($"method2 has been triggered by {provider}\n");
    }
}

public class Program {

 static public StringBuilder GetProperties(ParameterInfo[] prop)
{
   StringBuilder stringBuilder = new StringBuilder();
         for(int i = 0; i < prop.Length; i++)
            {
                stringBuilder.Append($"{prop[i].ParameterType} {prop[i].Name}");
                if(i + 1 != prop.Length) stringBuilder.Append(", ");
            }

    return stringBuilder;
 }

static void Main(string[] args)
{

	Type MyClassType = typeof(MyClass);

    Console.WriteLine(MyClassType.Name);
    Console.WriteLine(MyClassType.FullName); // namespace.na
    Console.WriteLine("\nProperties:");
    
    foreach (PropertyInfo p in MyClassType.GetProperties(BindingFlags.Public   |
												        BindingFlags.Instance |
												        BindingFlags.DeclaredOnly)) {
      Console.WriteLine($"{p.Name} {p.PropertyType} {p.DeclaringType} {p.GetType()}");
            }

    Console.WriteLine("\nFields:");

	// Default: returns public instance fields, including inherited ones
    foreach (FieldInfo f in MyClassType.GetFields()) {
       Console.WriteLine($"{f.Name}  {f.FieldType}  {f.DeclaringType}{f.GetType()}");
            }

    Console.WriteLine("\nMethods:");
    
    foreach (var m in MyClassType.GetMethods(BindingFlags.Public      |
										    BindingFlags.Instance    |
										    BindingFlags.DeclaredOnly)) {
     if (!m.IsSpecialName) { // Excludes get_*, set_*, op_*, etc.
         
       Console.WriteLine($"{m.ReturnType}
  	                 " {m.Name} (
  	                 " {GetProperties(m.GetParameters())})");
         }
    }

	object MyObject = Activator.CreateInstance(MyClassType);
	
	MyClassType.GetField("baseINT")?.SetValue(MyObject, 10);
	
	MyClassType.GetProperty("Id")?.SetValue(MyObject, 5);
	((MyClass)MyObject).Name = "Maamar";
	MyClassType.GetProperty("Description")?.SetValue(MyObject,
						   "journey of the unknown, is it the path ?");
	((MyClass)MyObject).IsPublic = true;
	
	
	Console.WriteLine("\nDisplay Fields:");
	Console.WriteLine(MyClassType.GetField("baseINT").GetValue(MyObject));
	
	
	Console.WriteLine("\nDisplay Properties:");
	Console.WriteLine(MyClassType.GetProperty("Id").GetValue(MyObject));
	Console.WriteLine(((MyClass)MyObject).Name);
	Console.WriteLine(MyClassType.GetProperty("Description")?.GetValue(MyObject));
	Console.WriteLine(((MyClass)MyObject).IsPublic ? "Public" : "Not Public");
	
	
	Console.WriteLine("\nDisplay Methods:");
	
	MyClassType.GetMethod("method1").Invoke(MyObject, null);
	
	object[] parameters = { 5, false };
	
	MyClassType.GetMethod("method2").Invoke(MyObject, parameters);
       
		}
    }
}
```
### BindingFlags.Default

![[Pasted image 20250530080147.png]]

#### The name `.Instance` in `BindingFlags.Instance` refers to **instance members** — meaning **members that belong to an object**, **not the class itself**.

---

### 🔍 Why it's called `.Instance`

In reflection (and in general C# terminology):

- `Instance` = **Non-static**
    
- `Static` = Belongs to the **type/class** itself


#### When you use **`BindingFlags.DeclaredOnly`**, you're opting out of the _default binding behavior_, so you **must explicitly specify** all the flags you want, such as `Instance`, `Static`, `Public`, or `NonPublic`.

### ✅ With `DeclaredOnly` — you must be explicit:

```csharp
typeof(MyClass).GetFields(BindingFlags.Instance  |
						BindingFlags.Public    | 
						BindingFlags.NonPublic | 
						BindingFlags.DeclaredOnly );`
```



