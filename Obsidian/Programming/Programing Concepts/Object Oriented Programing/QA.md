
### ❌ `data.count` is a **property**, not a variable

In C#, `ref` requires a **variable with a definite memory location**, like a field or local variable. Properties are really **methods under the hood**, so:

```csharp
script(ref data.count); // ❌ Not allowed
```
