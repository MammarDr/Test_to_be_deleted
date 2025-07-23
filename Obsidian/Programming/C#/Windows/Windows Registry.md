The Windows Registry is a hierarchical database that stores configuration settings and options on Microsoft Windows operating systems. It's used to store information about the system, applications, and user preferences. In C#, you can interact with the Windows Registry using the `Microsoft.Win32` namespace.

Here are some key aspects of the Windows Registry:

1. Hierarchy: The Registry is organized into keys, subkeys, and values, forming a hierarchical structure. Keys are analogous to folders, subkeys are subfolders, and values are the actual data or configuration settings.
2. Centralized Configuration: The Registry consolidates system and application settings, making it a centralized location for configuration information. This centralized approach helps ensure consistency and allows for easy retrieval and modification of settings.
3. System and User Settings: The Registry stores both system-wide settings (applicable to all users) and user-specific settings. User-specific settings are stored in "HKEY_CURRENT_USER," while system-wide settings are stored in various other root keys like "HKEY_LOCAL_MACHINE."
4. Start-up Configuration: The Registry is used to store information about programs and services that start automatically when the system boots. This includes settings related to startup programs and services.

“HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run”

1. Hardware Configuration: Information about installed hardware components, device drivers, and their configurations is stored in the Registry. This includes details about connected devices, hardware settings, and driver configurations.

###   

Note: Modifying the Windows Registry can have significant consequences, so it's crucial to be careful and ensure that you have the necessary permissions. Always back up the Registry before making any changes.

> Local Machine Data is global 

> Current User Data target specific person

## Write

```csharp
using Microsoft.Win32;
using System;


class Program
{
    static void Main()
    {
        // Specify the Registry key and path
        string keyPath = @"HKEY_LOCAL_MACHINE\SOFTWARE\YourSoftware";
        string valueName = "YourValueName";
        string valueData = "YourValueData";


        try
        {
            // Write the value to the Registry
            Registry.SetValue(keyPath, valueName, valueData, RegistryValueKind.String);


            Console.WriteLine($"{valueName} successfully written to the Registry.");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"An error occurred: {ex.Message}");
        }
    }
}
```

## Read

```csharp
// Specify the Registry key and path
string keyPath = @"HKEY_LOCAL_MACHINE\SOFTWARE\YourSoftware";
string valueName = "YourValueN
try
{
    // Read the value from the Registry
    string value = Registry.GetValue(keyPath, valueName, null) as st
    if (value != null)
    {
        Console.WriteLine($"The value of {valueName} is: {value}");
    }
    else
    {
        Console.WriteLine($"Value {valueName} not found in the Registry.");
    }
}
catch (Exception ex)
{
    Console.WriteLine($"An error occurred: {ex.Message}");
}
```

# Delete [ref]([Delete value from Registry - Example | ProgrammingAdvices](https://programmingadvices.com/courses/16-oop-in-c21/lectures/50682166))

```csharp
// Specify the registry key path and value name
string keyPath = @"SOFTWARE\YourSoftware";
string valueName = "YourValueName";


try
 {
 // Open the registry key in read/write mode with explicit registry view
using (RegistryKey baseKey = RegistryKey.OpenBaseKey(RegistryHive.CurrentUser, RegistryView.Registry64))
    {
     using (RegistryKey key = baseKey.OpenSubKey(keyPath, true))
            {
             if (key != null)
                {
                  // Delete the specified value
                     key.DeleteValue(valueName);
                     Console.WriteLine($"Successfully deleted value '{valueName}'
				                         from registry key '{keyPath}'");
                 }
                 else Console.WriteLine($"Registry key '{keyPath}' not found");
            }
        }
catch (UnauthorizedAccessException)
    {
       Console.WriteLine("UnauthorizedAccessException: Run the program with
					           administrative privileges.");
    }
catch (Exception ex)
   {
         Console.WriteLine($"An error occurred: {ex.Message}");
   }
```
## Permission 

### ✅ `HKEY_CURRENT_USER` (HKCU)

- **No administrator rights required**
    
- You **can delete keys/values freely** within your own user profile.
    
- Example: A normal user can delete `HKCU\Software\MyApp`.
    

---

### ⚠️ `HKEY_LOCAL_MACHINE` (HKLM)

- **Yes, administrator rights are typically required**
    
- You **need elevated privileges** to modify or delete most keys under `HKLM`, especially:
    
    - `HKLM\SOFTWARE`
        
    - `HKLM\SYSTEM`


Solution :

1. Run Visual Studio as Administrator:

- - Right-click on the Visual Studio icon.
    - Select "Run as administrator."
    - This will ensure that any application you run from Visual Studio inherits administrative privileges.

2. Require Administrative Privileges in Your Application:

- - In your C# application, you can include a manifest file that requests administrative privileges.
    - Create a file named `app.manifest` in your project (if it doesn't already exist).
- Add the following contents to the manifest file:

<?xml version="1.0" encoding="utf-8"?>
<assembly manifestVersion="1.0" xmlns="urn:schemas-microsoft-com:asm.v1">
  <assemblyIdentity version="1.0.0.0" processorArchitecture="X86" name="YourAppName" type="win32" />
  <trustInfo xmlns="urn:schemas-microsoft-com:asm.v2">
    <security>
      <requestedPrivileges>
        <requestedExecutionLevel level="requireAdministrator" uiAccess="false" />
      </requestedPrivileges>
    </security>
  </trustInfo>
</assembly>

- - Replace "YourAppName" with the actual name of your application.

1. Embed Manifest in Your Application:

- - Open your project properties in Visual Studio.
    - Go to the "Application" tab.
    - Set "Manifest" to the path of your `app.manifest` file.

Now, when you run your application, it will prompt for administrative privileges. This ensures that your application has the necessary permissions to write to the local machine registry.



### What is the purpose of the WOW64 subsystem in a 64-bit version of Windows?

To provide compatibility for 32-bit applications on a 64-bit system

## 🔄 Why Redirection Happens for `HKEY_LOCAL_MACHINE\SOFTWARE` (HKLM\SOFTWARE) but Not for User Keys

#### ✅ Summary:

- **Redirection for 32-bit apps** occurs under `HKEY_LOCAL_MACHINE\SOFTWARE` on 64-bit Windows to **avoid conflicts** with 64-bit apps.
    
- **User-specific settings** under `HKEY_CURRENT_USER` are not redirected because they're **isolated per user** and **don’t cause cross-architecture conflicts**.
    

---

### 🔍 Detailed Explanation:

#### 📁 `HKEY_LOCAL_MACHINE\SOFTWARE` (HKLM\SOFTWARE)

- This key stores **system-wide settings**.
    
- On **64-bit Windows**, both 32-bit and 64-bit apps may write to this location.
    
- To prevent them from overwriting or interfering with each other’s settings, **registry redirection** is used.
    

> 🧠 A 32-bit app writing to `HKLM\SOFTWARE` is **silently redirected** to:  
> `HKLM\SOFTWARE\WOW6432Node`

This redirection is managed by the **Windows Registry Redirector**.

---

#### 🧍‍♂️ `HKEY_CURRENT_USER` (HKCU)

- This key stores **user-specific settings**, scoped to the **current user’s profile**.
    
- There is **no need for redirection** because:
    
    - Each user already has a separate logical space.
        
    - There is **no risk** of 32-bit and 64-bit apps clashing over global configuration data.
        
    - The settings are not shared across processes with different architectures.
        

---

### 🛡️ Why Not Redirect HKCU?

- Redirection would be **redundant** and **over-complicate** user settings.
    
- 32-bit and 64-bit applications accessing HKCU are **still acting on the same user account** — thus, logically, their user settings can and should be shared.
    

---

### 📌 Conclusion

|Key Location|Redirected?|Why?|
|---|---|---|
|`HKLM\SOFTWARE`|✅ Yes|Prevent 32-bit and 64-bit apps from conflicting|
|`HKCU` (User-specific)|❌ No|User scope is isolated and safe from conflicts|