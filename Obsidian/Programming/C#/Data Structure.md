
## ✅ .NET Collection Interface Hierarchy (from lowest to highest)

### 🔽 1. **`IEnumerable<T>`**

- The **base** interface.
    
- Only requires:
    
    csharp
    
    CopyEdit
    
    `IEnumerator<T> GetEnumerator();`
    
- Enables `foreach`.
    
- Does **not** provide any way to add/remove/modify.
    

```csharp
public interface IEnumerable<out T> : IEnumerable {     IEnumerator<T> GetEnumerator(); }
```

---

### 🔼 2. **`ICollection<T>`**

- Extends `IEnumerable<T>`.
    
- Adds the ability to:
    
    - Add
        
    - Remove
        
    - Clear
        
    - Count
        
    - Check `IsReadOnly`
        


```csharp
public interface ICollection<T> : IEnumerable<T> {
	int Count { get; }
	bool IsReadOnly { get; }
	void Add(T item);
	bool Remove(T item);
	void Clear(); 
	bool Contains(T item); 
	void CopyTo(T[] array, int arrayIndex);
}
```

---

### 🔼 3. Specializations of ICollection<T\>

#### ✅ `IList<T>`

- Adds **indexing**:
    
    - Get/set by index
        
    - Insert at position
        
    - Remove at position
        


```csharp
public interface IList<T> : ICollection<T>
{
    T this[int index] { get; set; }
    void Insert(int index, T item);
    void RemoveAt(int index);
    int IndexOf(T item);
}
```


#### ✅ `ISet<T>`

- Adds **set-specific operations**:
    
    - No duplicates
        
    - Set logic: `UnionWith`, `IntersectWith`, `SetEquals`, etc.
        


```csharp
public interface ISet<T> : ICollection<T>
{
    bool Add(T item); // differs from void ICollection<T>.Add
    void UnionWith(IEnumerable<T> other);
    void IntersectWith(IEnumerable<T> other);
    void ExceptWith(IEnumerable<T> other);
    bool SetEquals(IEnumerable<T> other);
    // etc.
}
```

🧠 Note: `IList<T>` and `ISet<T>` are both **branches** of `ICollection<T>` — they don’t inherit from each other.

---

### 🔼 4. **IDictionary<TKey, TValue>**

- Also inherits `ICollection<KeyValuePair<TKey, TValue>>`.
    
- Adds key/value access.
    

---

## 🧭 Diagram Summary

```php
IEnumerable<T>
   └── ICollection<T>
         ├── IList<T>          (ordered, indexed)
         ├── ISet<T>           (unordered, unique)
         └── ICollection<KeyValuePair<TKey,TValue>>
              └── IDictionary<TKey,TValue> (map)
```

---

## 🔥 Your Takeaway

|Interface|Key Features|Typical Class|
|---|---|---|
|`IEnumerable<T>`|`foreach` only|`IEnumerable<T>`|
|`ICollection<T>`|Add, Remove, Count|`List<T>`, `HashSet<T>`|
|`IList<T>`|Index access|`List<T>`|
|`ISet<T>`|Uniqueness + set operations|`HashSet<T>`|
|`IDictionary<,>`|Key/Value pair + lookup|`Dictionary<TKey,T>`|

the **core logic** of implementing `IEnumerable<T>` really is just about enabling **iteration** and **integration with collection-based features**.

```csharp
MyList<int> myList = new MyList<int> { 1, 2, 3, 4 };
// Enable { ... } & Foreach & LINQ Methods on the object

 public IEnumerator<T> GetEnumerator()
 {
     for (int i = 0; i < _count; i++)
         yield return _items[i];
 }

IEnumerator IEnumerable.GetEnumerator() => GetEnumerator(); // non-generic version

```

## 🔁 `IEnumerable<T>` vs `IEnumerator<T>`

|Interface|Role|
|---|---|
|`IEnumerable<T>`|Represents a **sequence** of elements. It exposes one method: `GetEnumerator()`|
|`IEnumerator<T>`|Represents the **iterator** over that sequence. It handles the actual movement and access logic|

### 📚 Inherited

|Interface|Purpose|
|---|---|
|`IEnumerable<T>`|`foreach`, LINQ|
|`ICollection<T>`|Count, Add, Remove|
|`IList<T>`|Indexing `[i]`, Insert, etc.|
|`IReadOnlyList<T>`|Expose a read-only view|

### 📚 Inheritance hierarchy:

```csharp
IEnumerable<T>
	↳ ICollection<T>
		↳ IList<T>`
```

### Indexing

```csharp
public T this[int index]
{
    get
    {
        if (index < 0 || index >= _capacity)
            throw new ArgumentOutOfRangeException();
        return _items[index];
    }
    set
    {
        if (index < 0 || index >= _capacity)
            throw new ArgumentOutOfRangeException();
        _items[index] = value;
    }
}
```


What are List?

List<T> is a generic collection class in the

.NET framework. It's used to store a collection

of objects of the same type (T stands for the

type parameter).

Unlike arrays, List<T> is dynamic, meaning it

can automatically resize as needed.




What is Hastable?



A hashtable, also known as a hash map, is a data

structure that implements an associative array

abstract data type, a structure that can map

keys to values.



It uses a hash function to compute an index into

an array of buckets or slots, from which the

desired value can be found.


What is Hastable in C#?

•

A Hashtable in C# is a collection that stores

key-value pairs, organized based on the hash

code of the key.

•

It resides in the System.Collections namespace

•

Designed for scenarios where quick searches,

additions, and deletions are crucial.

•

Unlike generic collections, Hashtable allows for

keys and values of any type, adding versatility

but requiring careful handling of data types,

remember Boxing/Unboxing.


•

Non-Generic: Operates on objects of any type,

requiring casting when retrieving elements.

Boxing/Unboxing

Dectionary is advnced hashtable that supports generic data types (Type Saftey)

Performance:
•

Dictionary : Offers fast access to elements based on keys. The

performance of searching for a key is close to O(1), making it

highly efficient for lookups.

•

HashTable : also provides fast access to elements. However, the

need for boxing and unboxing when working with value types can

affect performance


cluster is the idea of inserting a key at used index and the need to iterate of long indices to find empty space


load factor is ..




`HashSet<T>` in C# is a powerful collection for storing unique elements. It is particularly useful when you need to ensure no duplicates, perform set operations, and when the order of elements is not a concern.

Remember, `HashSet<T>` does not support indexing, so if you need to access elements by index, consider using other collections like `List<T>`.



### `SortedList<TKey, TValue>` - Array

- Type: A generic collection that stores key-value pairs sorted by keys. It is a combination of an array and a hashtable.
- Ordering: The elements in a `SortedList<TKey, TValue>` are automatically sorted by the key. You cannot insert elements at a specific position as their position is determined by the key.
- Performance: Adding, removing, and accessing elements can be fast if the collection is not large, as it uses binary search to find keys. However, the performance can degrade as the collection grows due to the cost of maintaining order.
- Use Case: Use `SortedList<TKey, TValue>` when you need a collection of key-value pairs that must be sorted by key and you frequently need to search elements by key.

SortedDictionary<TKey, TValue> - Tree



Differences between

SortedDictionary and SortedList:

•

Implementation:

•

SortedDictionary: Implemented as a binary search tree.

•

SortedList: Implemented as an array of key

V

value pairs.

•

Performance Characteristics:

•

SortedDictionary offers efficient key

V

based operations

with O(log n) complexity.

•

SortedList provides efficient indexed access with O(log n)

complexity for searching and insertion but may incur

overhead during insertion/removal.

•

Memory Usage:

•

SortedDictionary typically consumes more memory due to its

tree structure.

•


•

Insertion and Removal:

•

SortedList: Insertions and removals may require shifting

elements in the underlying array to maintain the sorted

order. This operation has a time complexity of O(n) in the

worst

V

case scenario because it may involve copying

elements.

•

SortedDictionary: Insertions and removals have a time

complexity of O(log n) due to the underlying binary search

tree structure. This makes SortedDictionary more efficient

for these operations, especially for larger collections.

•

Search:

•

Both data structures offer efficient search operations.

SortedList uses binary search (O(log n)) for indexed

access, while SortedDictionary also has O(log n)

complexity for searching by key


•

Memory Usage:

•

SortedList: Typically consumes less memory compared to

SortedDictionary because it uses an array structure to

store elements.

•

SortedDictionary: May consume more memory due to the

overhead of maintaining a binary search tree.

•

Index-Based Access:

•

SortedList: Provides efficient indexed access similar

to arrays with O(log n) complexity.

•

SortedDictionary: Does not support index

V

based access

directly; you must access elements by their keys,

which may involve searchin



Conclusion:

•

Considering these factors, if your application involves

frequent insertions and removals with a relatively small

collection size or if memory efficiency is a concern,

SortedList might be a better choice.

•

On the other hand, if you require efficient search

operations, especially with larger collections, or if

memory usage is not a primary concern, SortedDictionary

could be more suitable.

•

Ultimately, the choice between SortedDictionary and

SortedList should be based on your specific use case,

considering factors like the size of the collection, the

frequency of different operations, and memory

constraint


SortedList may have better memory efficiency, especially

for large collections.




What is SortedSet?

•

SortedSet is a collection class in C# that

represents a sorted set of elements.

•

SortedSet is part of the

System.Collections.Generic namespace in .NET.

•

It stores unique elements in sorted order,

allowing for efficient search, insertion, and

removal operations

Characteristics of SortedSet

•

Stores unique elements in sorted order.

•

Provides fast search, insertion, and removal

operations.

•

Automatically maintains sorted order as elements

are added or removed.

•

Does not allow duplicate elements.














What is ArrayList?

•

ArrayList is a dynamic array that can hold

elements of any data type.

•

It's part of the System.Collections namespace.

•

Unlike arrays, ArrayList can grow dynamically as

elements are added, but it does not

automatically decrease its capacity when

elements are removed



Dynamic Sizing: Unlike arrays, ArrayList automatically

increases its size as elements are added. However, it does

not automatically decrease its capacity when elements are

removed.

•

Heterogeneous Collection: ArrayList can hold elements of

different data types. This flexibility allows for the storage

of different types of objects within the same collection.

•

Methods and Properties: ArrayList provides various methods

and properties to manipulate and access elements, such as

Add(), Remove(), Insert(), Count, and Capacity.

•

Memory Management: Although ArrayList does not automatically

decrease its capacity when elements are removed, you can use

the TrimToSize() method to reduce the capacity to match the

number of elements stored in the list.


What is the key feature of ObservableCollection?

It automatically notifies about any changes made to the collection

`List<T>` (Generic) vs `ArrayList` (Non-Generic)

|Feature|`List<T>`|`ArrayList`|
|---|---|---|
|Type-safe|✅ Yes|❌ No|
|Generic|✅ Yes|❌ No|
|Performance|✅ Faster|❌ Slower for values|
|Value types|✅ Native|❌ Needs boxing|
|Compile-time safety|✅|❌ Runtime risk|
|Modern usage|✅ Recommended|❌ Obsolete|


•

BitArray: A class in the System.Collections

namespace that represents a collection of bits.

•

Efficient Storage: BitArray allows for efficient

storage and manipulation of binary data,

offering a compact representation of boolean

values


Jagged Array are special type of array in C# that store arrays as their elements.

```csharp
	int[][ ] arr = new int[3][];
```



heap data structure is same as binary heap 
allow O(1) to access smaller or largest element, whish is crucial for priority based tasks.