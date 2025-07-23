
---



·        **Reference data types** (Advanced) **require user allocation, while Primitive data types (Basic) are allocated by the compiler.**

### **Primitive vs. Reference Data Types**

| **Characteristic** | **Primitive Data Types**                        | **Reference Data Types**                      |     |
| ------------------ | ----------------------------------------------- | --------------------------------------------- | --- |
| **Allocation**     | Compiler-allocated                              | dynamic allocation                            |     |
| **Examples**       | `boolean`, `byte`, `int`, `float`, `char`, etc. | User-defined (objects, arrays, strings, etc.) |     |
| **Storage**        | Holds actual data (value)                       | Holds a pointer pointing to data              |     |
| **Capacity**       | Hold Single value                               | hold multiple values                          |     |
| **Memory**         | Low memory usage                                | High memory usage                             |     |
| **Speed**          | Faster access                                   | Slower (indirect access via pointer)          |     |

```cpp 
	int age = 5;  // Stored directly on the stack
//  Memory:
//  age[5]
```

```cpp
	Point* p1 = new Point(5, 10);  // Pointer on stack, data on heap
//  Memory:
//  p1[0x1234] → Heap[0x1234][{5,10}]
```



---


### **Key Concepts**

1. **Stack vs. Heap**
    
    - **Primitive**: Stored on the **stack** (fixed size, fast access).
        
    - **Reference**: A **pointer** is stored on the stack, while the **actual data** resides on the **heap**.  (flexible size, slower access).
        
2. Reference Vs **Composite/User-Defined Types**
    
    - **Reference Type:**
	    - Only Hold Pointers.
	    -  Stored in Heap.
		
    - **Composite Type:**
	    - **May contain actual data** (not just pointers).
		- **Storage depends on language semantics**:


#### **Examples**

|Scenario|Shallow Copy?|Deep Copy?|Type Classification|
|---|---|---|---|
|**C++ `std::string*`**|✅ Yes|❌ No|Reference (Pointer)|
|**C++ `std::string`**|❌ No|✅ Yes|Composite (Value)|
|**Java `String`**|✅ Yes|❌ No|Reference|
|**C `struct`**|❌ No|✅ Yes|Composite (Value)|
|**Java `int[]`**|✅ Yes|❌ No|Reference (Array)|



---


### **Exceptions**

- **Optimizations**: Some languages (e.g., Java) may store small objects on the stack (escape analysis).


### **Key Takeaways**

- **Composite Types**: Combine multiple primitives/references (e.g., `struct`).
    
- **User-Defined Types**: Custom abstractions (e.g., `enum`, `record`).


### **Visual Summary**

Primitive: [Stack][5]  
Reference: [Stack][0x1234] → [Heap][5, "data", ...]  
Composite: [Stack][5, 0x5678] → (Mixed storage)



