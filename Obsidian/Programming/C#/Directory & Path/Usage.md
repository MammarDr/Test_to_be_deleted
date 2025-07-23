
> if u have file path

```csharp
string filePath = @"C:\MyFolder\data.txt";

// This gives you "C:\MyFolder"
string dir = Path.GetDirectoryName(filePath)!;

if (!Directory.Exists(dir))
    Directory.CreateDirectory(dir);  // Creates the folder if missing

File.WriteAllText(filePath, "your data here");
```


> if u have folder path

```csharp

string path = @"C:\CodingEnviorenment\Services";
if(!Directory.Exists(path))
    Directory.CreateDirectory(path);

File.WriteAllText(path + "file.txt", "your data here");
```