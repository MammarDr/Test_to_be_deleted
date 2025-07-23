### ❌ The Mistake :


```csharp
foreach (var v in tempList) {
tempList.Remove(v);    // ❌ BAD: modifying the list you're looping over
}
```
``

- **Problem:** You're modifying `tempList` **while** looping through it.
    
- **Result:** .NET throws `InvalidOperationException` — modifying a collection during `foreach` is not allowed because it can corrupt the iteration process.
    

---

### ✅ The Correct Way :

```csharp
List<object> toRemove = new List<object>();  // collect items to remove 

foreach (var v in tempList) {
	toRemove.Add(v); // ✅ safe — no modifications to tempList 
}  
//  now remove them 
foreach (var item in toRemove) {
	tempList.Remove(item); // ✅ safe — not inside the loop over tempList 
}
```

### ✅ Safe Alternatives:

1. **Use a `for` loop (backwards):**
    

```csharp
for (int i = list.Count - 1; i >= 0; i--) {
	if (ShouldRemove(list[i]))
	    list.RemoveAt(i);
}
```

``

2. **Use `Where` and `ToList()` to filter:**
    

```csharp
list = list.Where(x => !ShouldRemove(x)).ToList();
```


3. **Use a temporary list (like you did):**
    

```csharp
List<T> toRemove = new List<T>(); 

foreach (var item in list)     
	if (ShouldRemove(item)) toRemove.Add(item);
	
foreach (var item in toRemove)
	list.Remove(item);
}
```

