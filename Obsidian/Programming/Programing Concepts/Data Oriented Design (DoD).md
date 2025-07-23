
![[Pasted image 20250706123116.png]]


|Type|Size (Bytes)|
|---|---|
|`byte`|1|
|`short`|2|
|`int`|4|
|`long`|8|
|`float`|4|
|`double`|8|
|`char`|2 (Unicode)|
|`bool`|1 (logically, might use more)|



“use exceptions only for abnormal situations"
> An example of where you probably wouldn't want to use exceptions is in a containers `find` function when the searched-for element isn't found, since that's not an abnormal situation.


## [Structure Padding]([Structure Padding in C - YouTube](https://www.youtube.com/watch?v=aROgtACPjjg))

Processor doesn't read 1 byte at a time from memory. it reads 1 word at a time. (32bit processor read word size of 4 bytes each cycle)

> Size (in bytes) simply refers to **how many bytes a variable or type takes in memory**.

> **Alignment** is how the CPU organizes data in memory to access it efficiently. Most CPUs are **faster** when data is stored at certain boundaries — for example, an `int` is better placed at a 4-byte aligned address. (address is divisible by 4)

#### 🔸 Padding

To make that work, the compiler may **insert padding bytes** so each field starts at a correctly aligned offset.

Using the same struct:

```csharp
struct MyStruct {     
int a;      // offset 0 (4 bytes)
byte b;     // offset 4 (1 byte)
// padding 3 bytes added to align total size }
```

**Total size = 8 bytes**, not 5.

---

### 🔹 **Why does it matter?**

1. Performance — proper alignment avoids CPU penalties.
    
2. Interop — when communicating with C/C++ or hardware, you need exact sizes.
    
3. Memory efficiency — if you want tight packing, you might rearrange fields.

> **Offset** is the **position (in bytes)** of a field **from the start** of the structure.

So if `int a` starts at byte 0, then its **offset is 0**.

If `byte b` is placed right after it (at byte 4), its **offset is 4**.

| Byte Index | 0   | 1   | 2   | 3   | 4   | 5   | 6   | 7   |
| ---------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Field      | a   | a   | a   | a   | b   | P   | P   | P   |

### 🔹 Why add padding at the end?

To ensure **the next struct in an array** of `struct B[]` is correctly aligned. If the total size were just 5, and the next element started at offset 5, the next `int` (in `b`) would be **misaligned**, causing a CPU penalty (or crash on some architectures).


```csharp
struct Optimized {
    u64 b;
    u32 a;
    u32 c;
};
```

- `u32` (4 bytes): must be stored at an address divisible by **4**
    
- `u64` (8 bytes): must be stored at an address divisible by **8**

| Offset         | Field | Size         | Notes                               |
| -------------- | ----- | ------------ | ----------------------------------- |
| 0              | a     | 4            | u32                                 |
| 4              | —     | 4            | padding for u64 alignment           |
| 8              | b     | 8            | u64                                 |
| 16             | c     | 4            | u32                                 |
| 20             | —     | 4            | padding for struct alignment (to 8) |
| **Total Size** |       | **24 bytes** |                                     |
**Natural Alignment =** 8 Bytes

the next struct will start with offset - 24

--


```csharp
struct Optimized {
    u32 a;   // 4 bytes
    u32 b;   // 4 bytes
    u64 c;   // 8 bytes
};
```

| Field          | Offset | Size             | Notes                                |
| -------------- | ------ | ---------------- | ------------------------------------ |
| `a`            | `0`    | 4                | OK                                   |
| `b`            | `4`    | 4                | OK                                   |
| `c`            | `8`    | 8                | OK (starts at offset divisible by 8) |
| **Total Size** |        | **16 bytes**<br> |                                      |
**Natural Alignment =** 8 Bytes

-- 

```csharp
struct Optimized {
    u32 a;
    u32 c;
    u64 b;
    bool y;
};
```



|Field|Offset|Size|Notes|
|---|---|---|---|
|`a`|0|4|aligned (ok)|
|`b`|4|4|aligned (ok)|
|`c`|8|8|aligned (ok)|
|`d`|16|1|aligned (ok, but... )|
|pad|17–23|7|added to make total size multiple of 8|

**Size is 24, not 20**, because:

- `uint64_t` requires 8-byte alignment
    
- Struct size must be multiple of the largest alignment (8)
    
- Padding is added to ensure this



[MultiArrayList(Struct of Arrays)](https://youtu.be/IroPQ150F6c?si=IYgoMRjF1JeyFzNe&t=1229) can eliminate padding.