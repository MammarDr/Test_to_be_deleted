
```csharp
using System;


public class Utility
{
    public static T Swap<T>(ref T first, ref T second)
    {
        T temp = first;
        first = second;
        second = temp;
        return temp;
    }
}

public class List<T>
{
	private T myObj;

	List(T obj) {
		myObj = obj;
	}
}


class Program
{
    static void Main()
    {
        // Usage with integers
        int a = 5, b = 10;
        Utility.Swap(ref a, ref b); // a = 10, b = 5

        // Usage with strings
        string x = "Hello", y = "World";
        Utility.Swap(ref x, ref y); // x = "World", y = "Hello"


		List<string> wrapper = new List<string>(x)
    }
}
```

What is the purpose of generics in C#?

- To create reusable, type-safe components