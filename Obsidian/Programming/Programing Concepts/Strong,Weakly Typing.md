
### Strong Typing

- A language or piece of code is **strongly typed** when the **type of every variable and expression is known and enforced at compile time**.
    
- You **must** declare what type things are, and the compiler **checks types for you**, catching mistakes early.
    
- Examples: C#, Java, Rust.
    

**In C#:**

```csharp
List<int> numbers = new List<int>();  // numbers is strongly typed as List of int 
int first = numbers[0];               // compiler knows elements are ints`
```



You get **compile-time safety** — you can’t accidentally put a string into `numbers`, or treat an int as a string without explicit conversion.

---

### Weak Typing

- A language or code is **weakly typed** when types are **not strictly enforced**, and many type conversions happen implicitly or at runtime.
    
- Mistakes like mixing types can cause errors at runtime instead of compile time.
    
- Examples: JavaScript, Python, PHP.
    

**In a weakly typed way:**

```js
let x = "5"; 
x = x + 1;  // "51" because 1 is converted to string`
```
Types are flexible but sometimes confusing or error-prone.


