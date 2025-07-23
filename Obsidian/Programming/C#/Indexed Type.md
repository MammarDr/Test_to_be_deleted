## What is `IEnumerable`?

- `IEnumerable` is an **interface** in .NET used to represent a sequence of elements you can **iterate over** (like in a `foreach` loop).

### 🔹 `IEnumerable` — _Read-only, forward-only iteration_

- **Purpose:** Allows iteration (e.g., with `foreach`).
    
- **Capabilities:**
    
    - Can enumerate (loop through) items.
        
    - Cannot access items by index.
        
    - Cannot add, remove, or modify items.
        
- **Commonly implemented by:** Arrays, Lists, Dictionaries, etc.
    
- **Namespace:** `System.Collections`

## Use Case

you **can’t directly access the list elements** or call list-specific methods on `obj` because its **compile-time type is `object`**, and `object` doesn’t have those members.

```csharp
List<int> myList = new List<int> { 1, 2, 3 };
object obj = myList;
```

---

## What does `obj` contain?

- **`obj` holds a reference to the same `List<int>` instance.**
    
- Even though its **static type is `object`**, at runtime it **actually points to the `List<int>` object**.
    
- You can think of it as:  
    _`obj` is like a box labeled "object" but inside it is your `List<int>`._
    

---

## What can you do with `obj`?

- You **can only call methods/properties of `object` directly** (like `.ToString()`, `.GetType()`, etc.)
    
- To use it as a `List<int>`, you need to **cast it back**:
    

```csharp
List<int> listAgain = (List<int>)obj;
```

#### OR

```csharp
IEnumerable enumerable = (IEnumerable)obj;
foreach(var item in enumerable)
{
    Console.WriteLine(item); // print elements
}
```

#### OR

```csharp
if (value is IEnumerable enumerable)
{
    var items = enumerable.Cast<object>().Select(item => item?.ToString() ?? "null");
    return $"[{string.Join(",", items)}]";
}
```

Here's a clean summary:

- **`IEnumerable`** (non-generic) is like a **box holding a sequence**, but it doesn't tell you what type the elements inside are. It lets you **iterate** over the elements but as plain `objects` without strong typing.
    
- When you do **`.Cast<object>()`**, you’re saying:
    
> "Treat each element inside this sequence as an `object` and turn this non-generic `IEnumerable` into a generic `IEnumerable<object>`."
    
- This conversion lets you use **LINQ** methods like `.Select()`, `.Where()`, etc., with type safety and easier coding.

## 🔍 What's really happening:

- **`IEnumerable` (non-generic)**: The compiler only knows "this is _something_ iterable, and each item is just an `object` — that's all I can assume."
    
- **`.Cast<object>()`**: You're telling the compiler, **"Trust me, every item is an `object`, and I want to work with it as `IEnumerable<object>`."**

This small shift changes what the compiler _lets_ you do — especially with LINQ. That’s it.

---

## 🧾 Example 1: Non-typed (`IEnumerable`)

```csharp
IEnumerable things = new ArrayList { 1, 2, 3 };  
foreach (var item in things) {
	Console.WriteLine(item); // item is of type `object` 
}
```

- The compiler only knows: "this is a sequence of `objects`"
    
- No type safety, no LINQ, no idea what’s inside

## ✅ Example 2: **Typed** (`IEnumerable<int>` or `IEnumerable<object>`)

```csharp
IEnumerable<object> things = new List<object> { 1, 2, 3 };
foreach (var item in things) {
	Console.WriteLine(item); // item is of type `object`, *known to the compiler* 
}
```

- Now You can use full **LINQ** methods, type-safe operations, and even autocomplete in IDE

## 🔄 What `.Cast<object>()` does:

It **takes a non-generic `IEnumerable`** and **wraps it in a typed `IEnumerable<object>`**:

```csharp
IEnumerable raw = new ArrayList { "A", "B", "C" };  
var typed = raw.Cast<object>();  // Now it's IEnumerable<object>}
```


|Concept|`IEnumerable`|`IEnumerable<object>`|
|---|---|---|
|Element type known?|❌ No (just `object`)|✅ Yes (`object` known)|
|LINQ friendly?|❌ Limited|✅ Yes|
|Safer to use?|❌ Runtime errors likely|✅ Compile-time checks|

#### ❌ What you **can't** do:

```csharp
names[0] = "Charlie"
```


---

### 🔸 `IList` — _Indexable and mutable_

- **Purpose:** Full-featured collection interface.
    
- **Capabilities:**
    
    - Indexing (`list[0]`)
        
    - Adding/removing items (`Add()`, `Remove()`)
        
    - Modifying existing items (`list[1] = "foo"`)
        
- **Extends:** `ICollection`, which itself extends `IEnumerable`
    
- **Namespace:** `System.Collections` or `System.Collections.Generic`
    

#### ✅ Example:

```csharp
IList<string> list = new List<string> { "Apple", "Banana" };
list[1] = "Mango";
list.Add("Cherry");
```


---

### 🔄 Summary Table:

|Feature|`IEnumerable`|`IList`|
|---|---|---|
|Can use `foreach`|✅|✅|
|Can access by index|❌|✅|
|Can modify items|❌|✅|
|Can add/remove items|❌|✅|
|Lightweight|✅|❌ (more overhead)|
|Use when…|You just need to iterate|You need full list capabilities|

---

### 🔍 Rule of Thumb:

- Use `IEnumerable` **when you only need to read** data.
    
- Use `IList` (or `List<T>`) **when you need to modify** or index into the data.