In C#, attributes provide a way to add metadata to your code. They are used to provide additional information about elements in your code, such as classes, methods, properties, and so on. Attributes are defined using square brackets `[]` and are placed above the code element they are associated with.

Here's a basic example of using attributes in C#:
```csharp
using System.Diagnostics;

[Serializable]
public class MyClass
{
	[NonSerialized]
	public String Text {get; set;}
	
    [Obsolete("This method is deprecated. Use NewMethod instead.")]
    public void DeprecatedMethod()
    {
        // Deprecated method implementation
    }


    [Conditional("DEBUG")] // debug is defined; degine DEBUG
    public void DebugMethod()
    {
        // Code to be executed only in debug mode
    }
}
```

In this example:

- The `Serializable` attribute is applied to the `MyClass` class, indicating that instances of this class can be serialized.
- The `Obsolete` attribute is applied to the `DeprecatedMethod` method, marking it as deprecated and providing a message that suggests using the `NewMethod` instead.
- The `Conditional` attribute is applied to the `DebugMethod` method, indicating that the method should only be called if the symbol `DEBUG` is defined during compilation.

Attributes play a crucial role in enhancing code readability, providing additional information, and enabling frameworks and tools to understand and process your code more effectively. They are widely used in areas like serialization, documentation, testing, and more.



##   Custom Attribute

In C#, a custom attribute is a user-defined metadata that you can apply to elements in your code, such as classes, methods, properties, or parameters. Attributes provide a way to add declarative information to your code, which can be used by the runtime, tools, or other code to perform specific actions or make decisions.

To define a custom attribute, you create a class that inherits from the `System.Attribute` class or one of its derived classes. The attribute class can then be applied to various elements in your code using square brackets.

Here's a simple example of a custom attribute:

```csharp
using System;
using System.Reflection;

[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = true)]
public class MyCustomAttribute : Attribute
{
    public string Description { get; }


    public MyCustomAttribute(string description)
    {
        Description = description;
    }
}


[MyCustom("This is a class attribute")]
class MyClass
{
    [MyCustom("This is a method attribute")]
    [MyCustom("This is another attribute")]
    public void MyMethod()
    {
        // Method implementation
    }
}

// Get the type of MyClass
Type type = typeof(MyClass);

// Get class-level attributes / GetCustomAttributes(AttributeType,inherit)
// For classes, `inherit = true` has no effect — class-level attributes are not inherited
object[] classAttributes = type.GetCustomAttributes(typeof(MyCustomAttribute), false);

foreach (MyCustomAttribute attribute in classAttributes)
{
    Console.WriteLine($"Class Attribute: {attribute.Description}");
}

// Get method-level attributes
MethodInfo methodInfo = type.GetMethod("MyMethod"); // First Method Only
object[] methodAttributes = methodInfo.GetCustomAttributes(typeof(MyCustomAttribute), false);
foreach (MyCustomAttribute attribute in methodAttributes)
{
    Console.WriteLine($"Method Attribute: {attribute.Description}");
}

```


- `AttributeUsage`:
    
    - `AttributeTargets.Class | AttributeTargets.Method`: You can apply this attribute to classes and methods.
        
    - `AllowMultiple = true`: You can apply it **more than once** to the same element.


- `MyCustomAttribute` inherits from `System.Attribute`, so it's a valid custom attribute.

However, when you **use** the attribute in code (e.g., in `[MyCustom("...")]`), the compiler allows you to **omit** the `"Attribute"` suffix.

|Declared Attribute Name|Valid Usage Syntax|
|---|---|
|`MyCustomAttribute`|`[MyCustom]` or `[MyCustomAttribute]`|
|`AuthorizeAttribute`|`[Authorize]` or `[AuthorizeAttribute]`|


## Validation Attribute

```csharp

[AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
public class RangeAttribute : Attribute
{
    public int Min { get; }
    public int Max { get; }

    public string ErrorMessage { get; set; }

    public RangeAttribute(int min, int max)
    {
        Min = min;
        Max = max;
    }
}

public class Person
{
    [Range(18, 99, ErrorMessage = "Age must be between 18 and 99.")]
    public int Age { get; set; }

    [Range(20, 30, ErrorMessage = "Experience must be between 20 and 30.")]
    public int Experience  { get; set; }


    public string Name { get; set; }
}

public class ValidationExample
{
    public static void Main()
    {
        Person person = new Person { Age = 125, Name = "Mohammed", Experience=15 };

        if (ValidatePerson(person)) Console.WriteLine("Person is valid.");
        else Console.WriteLine("Validation failed.");
    }

    static bool ValidatePerson(Person person)
    {
        Type type = typeof(Person);
        
        foreach (var property in type.GetProperties())
        {
            // Check for RangeAttribute on properties
            if (Attribute.IsDefined(property, typeof(RangeAttribute)))
            {
                var rangeAttribute = (RangeAttribute)Attribute.GetCustomAttribute(property, typeof(RangeAttribute));
                
                int? value = (int?)property.GetValue(person);

				if (!value.HasValue) return false;

                // Perform validation
                if (value < rangeAttribute.Min || value > rangeAttribute.Max)
                {
                    Console.WriteLine($"Validation failed for property '{property.Name}': {rangeAttribute.ErrorMessage}");
                    return false;
                }
            }
        }

        return true;
    }
}

```

![[Pasted image 20250530095100.png]]

![[Pasted image 20250530095644.png]]

### ✅ When to Use Attribute-Based Validation:

|Scenario|Why|
|---|---|
|**Model-first development**|Validation rules live close to the data structure. Easy to read & maintain.|
|**Simple, declarative rules**|Great for common constraints: `[Required]`, `[Range]`, `[StringLength]`, etc.|
|**ASP.NET MVC or Web API**|Built-in support for validation attributes during model binding.|
|**Consistency across many similar models**|Reuse common validation patterns with custom attributes.|

### 🚫 Downsides:

- Less flexible for dynamic or runtime rules.
    
- Can't easily handle relationships between fields (e.g., `StartDate < EndDate`) unless you write custom attributes.
    
- Hard to decouple from your model — might mix validation logic into your data layer.

### ✅ When to Use Generic Validation:

|Scenario|Why|
|---|---|
|**Validation depends on dynamic data or config**|E.g., rules loaded from a database or config file.|
|**Validation rules are user-defined at runtime**|Good for admin panels, form builders, etc.|
|**Multiple validation layers or contexts**|Different validation rules for "Create", "Update", or "Admin View".|
|**Separation of concerns**|Keeps validation logic out of your data model.|
|**Complex cross-field logic**|Easier to write and test custom logic between multiple fields.|

### 🚫 Downsides:

- More boilerplate.
    
- Harder to reuse simple rules (like `[Required]`).
    
- More difficult to integrate with UI validation unless explicitly supported.


## 🆚 **Side-by-Side Comparison**

|Feature|Attribute-Based Validation|Generic Validation|
|---|---|---|
|Easy to apply & maintain|✅ Yes|❌ Manual effort|
|Runtime flexibility|❌ No|✅ Yes|
|Works with ASP.NET MVC model binding|✅ Built-in|❌ Manual|
|Supports dynamic models|❌ No|✅ Yes|
|Complex, cross-field logic|❌ Hard|✅ Easy|
|Separation from data models|❌ No|✅ Yes|

### ✅ Rule: C# Automatically Strips the `Attribute` Suffix

In C#, when you're **declaring** an attribute class, you must name it something that ends in `"Attribute"` (by convention and requirement). MyCustomAttribute => MyCustom

You can then use reflection or other mechanisms to access and utilize the information provided by these custom attributes at runtime. Custom attributes are often used for various purposes such as code generation, documentation, or influencing the behavior of frameworks and libraries.