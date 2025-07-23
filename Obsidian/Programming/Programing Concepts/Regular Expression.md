
## RegEX


 * ^ - Starts with
 * $ - Ends with
 * [] - Range
 * () - Group
 * . - Single character once
 * + - one or more characters in a row
 * ? - optional preceding character match
 * \ - escape character
 * \n - New line
 * \d - Digit
 * \D - Non-digit
 * \s - White space
 * \S - non-white space
 * \w - alphanumeric/underscore character (word chars)
 * \W - non-word characters
 * {x,y} - Repeat low (x) to high (y) (no "y" means at least x, no ",y" means that many)
 * (x|y) - Alternative - x or y
 * 
 * [^x] - Anything but x (where x is whatever character you want)



`.*` → Matches **zero or more** of **any character** (except line breaks):
- `.` means **any character** (except `\n`).
- `*` means **zero or more times**.

`\/\*[\s\S]*?\*\/` → **multi-line comments**
- `[\s\S]` matches **everything** (spaces or not, newlines included).
- `*?`  **non-greedy**, so it stops at the first `*/` — avoids overmatching multiple comments.

Without it, If there are multiple `/*...*/` in a file, it may match **from the first `/*` to the last `*/`**, which is wrong.

```csharp
using System.Text.RegularExpressions;

// (440) 555-1212
// 12-34567890
// 123 87876

string toSearch = File.ReadAllText("test.txt");

// This pattern matches phone numbers in the following patterns
string pattern = @"\(?\d{3}\)?(-|.|\s)?\d{3}(-|.)?\d{4}";

MatchCollection matches = Regex.Matches(toSearch, pattern);

foreach (Match match in matches)
{
    Console.WriteLine(match.Value);
}

string pattern = @"(\s|^)Tim(\s|$)";
string toSearch = "Tim Corey";

Console.WriteLine("Tim Corey: " + Regex.IsMatch("Tim Corey", pattern));

Console.WriteLine("Timothy Corey: " + Regex.IsMatch("Timothy Corey", pattern));

Console.WriteLine("Always Tim: " + Regex.IsMatch("Always Tim", pattern));

Console.WriteLine("I Am Tim Corey: " + Regex.IsMatch("I Am Tim Corey", pattern));


Regex test = new Regex(pattern);

for (int i = 0; i < 100000; i++)
{
    test.IsMatch("I Am Tim Corey");
}


```

[ref]([RegExr: Learn, Build, & Test RegEx](https://regexr.com/))


```
// (http(s)?:\/\/)?(?:www\.|\w+\.)*(\w+\.)+(?:[A-Za-z]{2,}|[0-9]*)(:[0-9]+)*(\/\w+)*((\?\w+\=\w+)+(\&\w+\=\w+)*)*(\#\w+)?

// (http(s)?:\/\/)?(?:www\.|\w+\.)*(?:[A-Za-z\-]+\.|[0-9]+)+(?:[A-Za-z]{2,}|[0-9]*)(\:[0-9]+)*(\/\w*)*((\?\w+\=\w+)+(\&\w+\=\w+)*)*(\#\w+)?



```

```
http://example.com
https://sub.example.com
http://example.com:8080
https://example.com/path/to/resource
https://example.com/search?q=test&lang=en
https://example.com/page#section
example.com

http://exa$mple.com
http://example .com
http://example.invalidtld
http://

http://192.168.1.1
https://example.xyz
https://example.com/:942412
https://example.com/path/to/resource?name=John+Doe
https://xn--fsq.com (Punycode for https://例.com)

```


### 🔥 Why Regex is Bad for JSON Parsing:

#### ❌ 1. **JSON Allows Nesting (Regex Can’t Handle That)**

#### ❌ 2. **Edge Cases Break It**

JSON allows:

- Escaped characters inside strings (`\"`, `\\`, `\n`)
    
- Unicode characters
    
- Numbers, null, booleans
#### ❌ 3. **Unmaintainable and Fragile**

Even if you hack a regex that works for simple cases, it will break as soon as:

- You add new JSON features
    
- You deal with invalid JSON
    
- You want to modify values