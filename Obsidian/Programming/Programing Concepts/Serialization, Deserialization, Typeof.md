
# **What is serialization ?**

• Serialization in C# refers to the process of converting an object or a data structure into a format that can be easily stored, transmitted, or reconstructed

# **Purpose of Serialization?**
• The primary purpose of serialization is to persistently store the state of an object or to send it over a network.

# **What is Deserialization?**
• The reverse process, which involves reconstructing the object from its serialized form, is called deserialization.

## XML Format:
• XML Serialization: Objects are serialized into XML format, which is both human-readable and platform-independent.
• XML serialization is commonly used when interoperability with other systems is required.
• Library to use:
System.Xml.Serialization

```csharp
using System.Xml.Serialization;
using System.IO;

[Serializable]
public class Person {
	[XmlElement("FullName")]
	public string Name {get; set;}
	
	[XmlElement("PlaceOfBirth")]
	public string City {get; set;}
	
	[XmlElement("ZipCode")]
	public int PostalCode {get; set;}

	[XmlIgnore]
	public int Weight {get; set;}
}

Person person = new Person { Name = "Ahmed", City = "Oued", PostalCode = 1006 };

XmlSerializer serializer = new XmlSerializer(typeof(Person));

using (TextWriter writer = new StreamWriter("file.xml"))
{
    serializer.Serialize(writer, person);   
}

using(TextReader reader = new StreamReader("file.xml"))
{
    Person deperson = (Person)serializer.Deserialize(reader);
    Console.WriteLine($"{deperson.Name}, {deperson.City}, {deperson.PostalCode}");
}
```

If you **don’t use `[XmlIgnore]`**, the property **will be included** in XML serialization and deserialization, as long as it’s:

- `public`, and
    
- has a public getter and setter.

## JSON Format:
• JSON Serialization: Similar to XML, JSON is a human-readable and lightweight data interchange format.
• It is commonly used for web APIs and AJAX requests.
• Library to use:
System.Runtime.Serialization.Json

```csharp
using System.Runtime.Serialization.Json;
using System.IO;

DataContractJsonSerializer serializer = new DataContractJsonSerializer(typeof(Person));
  using (MemoryStream stream = new MemoryStream())
  {
      serializer.WriteObject(stream, person);
      string jsonString = System.Text.Encoding.UTF8.GetString(stream.ToArray()
      // Save the JSON string to a file (optional)
      File.WriteAllText("person.json", jsonString);
  
  // Deserialize the object back
  using (FileStream stream = new FileStream("person.json", FileMode.Open))
  {
      Person deserializedPerson = (Person)serializer.ReadObject(stream);
      Console.WriteLine($'Name: {deserializedPerson.Name},
					    'Age: {deserializedPerson.Age}');
  }
```

## Binary Format :
• Binary Serialization: This format is efficient but not human-readable.
• It's suitable for saving object state within the same platform.
• Library to use:
System.Runtime.Serialization.Formatters.Binary

```csharp
using System.IO;
using System.Runtime.Serialization.Formatters.Binary;

BinaryFormatter formatter = new BinaryFormatter();
        using (FileStream stream = new FileStream("person.bin", FileMode.Create))
        {
            formatter.Serialize(stream, person);
        }


        // Deserialize the object back
        using (FileStream stream = new FileStream("person.bin", FileMode.Open))
        {
            Person deserializedPerson = (Person)formatter.Deserialize(stream);
            Console.WriteLine($"Name: {deserializedPerson.Name}, Age: {deserializedPerson.Age}");
           Console.ReadKey();
        }
```

# **When to use Serialization?**
• Data Persistence
• Communication Between Applications
• Cross-Language Communication
• Web Development
• Many other places.




## Deserialization

```json
{
"isDoneToday" : false,
"LatestDate" : "2025-07-16"
}
```

## ✅ Option 1: Using `System.Text.Json`

### 1. Define a C# class:

```json
public class StatusInfo {

	public bool isDoneToday { get; set; }
	
	public string LatestDate { get; set; } 
	
}
```


### 2. Read JSON into the class:

```json
using System.Text.Json;

// Your JSON string 
string json = @"{""isDoneToday"" : false, ""LatestDate"" : ""2025-07-16""}";  

// Deserialize it 
StatusInfo data = JsonSerializer.Deserialize<StatusInfo>(json);  

// Access the data 
Console.WriteLine(data.isDoneToday);  // false
Console.WriteLine(data.LatestDate);  // 2025-07-16
```

---

## ✅ Option 2: Read without defining a class

```json
using System.Text.Json;

// Parse into JsonDocument 
string json = @"{""isDoneToday"" : false, ""LatestDate"" : ""2025-07-16""}"; 
using JsonDocument doc = JsonDocument.Parse(json); 

bool isDone = doc.RootElement.GetProperty("isDoneToday").GetBoolean();
string date = doc.RootElement.GetProperty("LatestDate").GetString();  

Console.WriteLine(isDone); // false
Console.WriteLine(date);   // 2025-07-16
```


---

## 🧠 Summary

|Method|Use When…|
|---|---|
|`JsonSerializer.Deserialize<T>`|You have a matching C# class|
|`JsonDocument.Parse()`|You want quick/low-overhead access|

#Bonus

## 🔍 Key Differences

|Feature|`StreamWriter` to file|`MemoryStream`|
|---|---|---|
|Storage|On disk|In RAM|
|Speed|Slower (disk I/O)|Faster (memory access)|
|Persistence|Persistent|Temporary (lost on app exit)|
|Use for Text?|Yes, via `StreamWriter`|Yes, with `StreamWriter` too|
|Use for Binary?|No|Yes|
## ✅ `MemoryStream stream = new MemoryStream()`

### What it does:

- Creates a stream that **lives in memory** (RAM).
    
- Acts like a file but doesn't touch the disk.
    
- Stores **binary data** in a temporary, fast-access buffer.
    

### Use case:

- Working with **temporary data**, serialization, or transformation before saving or sending.
    
- Useful when you want to avoid I/O overhead.


### Combine MemoryStream + StreamWriter

You can even write text to a `MemoryStream` using a `StreamWriter`:

```csharp
using (var memStream = new MemoryStream()) {
	using (var writer = new StreamWriter(memStream)) {    
		writer.Write("Text written to memory!");     
		writer.Flush(); // Important!          
		// Reset stream position to read     
		memStream.Position = 0;     
		var result = Encoding.UTF8.GetString(memStream.ToArray()); 
		Console.WriteLine(result); 
	}
}
```

**MemoryStream** opens a chunk of memory (RAM) that acts like a virtual file or data buffer. It lets you **read and write data sequentially** just like a file stream, but **all happens in memory**, without touching the disk.

So you can think of it as:

- A **temporary, fast, virtual file** stored in RAM.
    
- Ideal for scenarios where you want file-like operations but don't want the overhead or side effects of disk I/O.
    

---

### Key points:

- **No physical file created** on disk.
    
- Data stored is **volatile** — lost when the program exits or the stream is disposed.
    
- Great for **quick data processing, transformation, or network transfer**.
    
- Supports writing **text or binary data** (with helpers like `StreamWriter` or directly writing bytes).


## ✅ `FileStream Vs TextReader`

### FileStream :

- **Details:**
    
    - Reads or writes bytes directly.
        
    - More low-level, handles bytes, no text encoding applied automatically.
        
    - You control buffer sizes, reading chunks, and encoding manually.
        
- **Use case:**  
    When you want to process binary data or have fine control over file access.

### TextReader :

- **Details:**
    
    - Reads characters, not bytes.
        
    - Automatically decodes bytes to characters based on encoding.
        
    - Easier for reading text files line-by-line or as a string.
        
- **Use case:**  
    When you want to read text data (like JSON, XML, CSV) conveniently.

| Aspect       | FileStream                         | StreamReader (TextReader)        |
| ------------ | ---------------------------------- | -------------------------------- |
| Data Type    | Bytes                              | Characters (text)                |
| Encoding     | Not handled automatically          | Handles encoding (UTF-8 default) |
| Level        | Low-level                          | High-level, easier for text      |
| Common Usage | Binary files, fine-grained control | Text files, easier text reading  |



### (Read/Append)AllText :

```csharp
// Approach 3 – File.ReadAllText()
string content = File.ReadAllText(@"C:\file.txt");

string content = File.AppendAllText(@"C:\file.txt", message);

```

| Feature                        | `StreamReader` + loop (`Append`)        | `File.ReadAllText()`             |
| ------------------------------ | --------------------------------------- | -------------------------------- |
| **Control over line-by-line**  | ✅ Yes (you get each line separately)    | ❌ No (whole file in one string)  |
| **Memory efficiency**          | ✅ Better for huge files                 | ❌ Loads entire file into memory  |
| **Simplicity**                 | ❌ More code                             | ✅ Very simple                    |
| **Line breaks preserved**      | ✅ With `AppendLine()`                   | ✅ Yes                            |
| **Performance on small files** | ❌ Slightly slower due to loop overhead  | ✅ Fast for small/medium files    |
| **Custom processing**          | ✅ You can modify each line as it's read | ❌ You'd have to split the string |