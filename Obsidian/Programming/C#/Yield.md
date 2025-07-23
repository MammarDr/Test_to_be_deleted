
`yield return` can **only** be used inside methods or properties that return:

- `IEnumerable`
    
- `IEnumerable<T>`
    
- `IEnumerator`
    
- `IEnumerator<T>`
    

But `ICollection<TKey>` is not an iterator — it includes other members like `Add`, `Count`, `Clear`, etc.

