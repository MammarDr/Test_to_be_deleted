Lambda expressions are often preferred over normal (named) functions in specific scenarios because they offer some advantages, such as **conciseness, inline definition, and the ability to create anonymous functions.** Here are a few reasons why you might choose to use lambda expressions over normal functions:

1. **Conciseness:** Lambda expressions are typically more concise than defining a separate function, especially for small, simple operations. This leads to more readable and less verbose code.
2. **Anonymous Functions:** Lambda expressions allow you to create anonymous functions on the fly without needing to declare a separate named function. This is particularly useful when you need a function for a one-time or specific purpose.
3. **Inline Definitions:** Lambda expressions can be defined inline within a statement or method call, making it easy to pass functions as arguments to methods or use them in LINQ queries without defining separate functions.
4. **Readability:** In some cases, a lambda expression's compact and in-place definition can improve code readability, as it keeps the code closer to the point where the function is used.
5. **Functional Programming:** Lambda expressions align with functional programming principles, which can lead to more expressive and declarative code in certain contexts, such as LINQ queries and event handling.
6. **Flexibility:** Lambda expressions can capture variables from their surrounding scope (known as closures), allowing you to create more flexible and context-aware functions.

While lambda expressions offer these advantages, it's important to note that there are cases where defining a separate named function is more appropriate. You would typically use named functions when you need to reuse the same logic in multiple places, promote code organization, or when the function is more complex and requires detailed documentation.


```csharp
Func<int, int> myFunc = (x,y) => X + y;

Action myAction = () => Console.WriteLine("Lambda adbmal");

int x = 5;
passLambda(_ => x);  // ignores lambda param, returns external x


//🔹 Inline in LINQ:

var numbers = new List<int> { 1, 2, 3, 4, 5 };
var evenNumbers = numbers.Where(n => n % 2 == 0);

//🔹 Inline in Event Handling:

button.Click += (sender, e) => Console.WriteLine("Button clicked!");

```


### **Named Functions vs. Lambda Expressions**


## ✅ **Named Functions vs. Lambda Expressions in C#**

Understanding when to use **named functions** and when to use **lambda expressions** helps you write clean, maintainable, and efficient code.

---

### 🟦 Named Functions

**✔️ Use When:**

- Logic is complex.
    
- You need to **reuse** the function.
    
- You want to enhance **readability** and **self-documenting** code.


**🔹 Benefits:**

- Clear intent through function names.
    
- Easier to debug and test.
    
- Better separation of concerns.

**🔹 Example:**

```csharp
int Square(int num)
{
    return num * num;
}

// Usage
int result = Square(5);
Console.WriteLine("Square of number: " + result);
```


### 🟩 Lambda Expressions

**✔️ Use When:**

- Logic is short and simple.
    
- Function is used **only once** or **inline**.
    
- You want **concise and expressive** syntax.
    

**🔹 Benefits:**

- Great for inline operations like LINQ, event handlers, callbacks.
    
- Reduces boilerplate code.
    

**🔹 Example:**
```csharp
Func<int, int> square = (int num) => num * num;

// Usage
int result = square(5);
Console.WriteLine("Square of number: " + result);
```


### ⚙️ Performance Note

- **No major difference** for simple cases.
    
- For repeated use or complex logic, **named functions are often better for readability and stack trace clarity**.
    
- **Lambda expressions** can capture local variables (closures), which may have **hidden performance and memory costs** if not used carefully.

| Use Case                  | Preferred Approach |
| ------------------------- | ------------------ |
| Reusable logic            | Named Function     |
| Complex or multi-line     | Named Function     |
| Short, inline logic       | Lambda Expression  |
| LINQ, event handlers      | Lambda Expression  |
| Debuggable, testable code | Named Function     |