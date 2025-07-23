
In C#, `App.config` refers to the configuration file associated with a .NET application. The configuration file is an XML file that contains settings and configuration information for an application. The file is named `App.config` during development, and when the application is compiled, the settings are often moved to the application's executable file with a `.config` extension (e.g., `YourAppName.exe.config`).

Here are some key points about `App.config`:

1. Settings: It is commonly used to store settings such as connection strings, app settings, and other configuration parameters that can be changed without modifying the code.
2. XML Format: The `App.config` file is an XML file. It consists of elements and attributes that define various settings for the application.
3. Configuration Sections: It allows you to define custom configuration sections for your application. These sections can be used to organize and group related settings.
4. ConfigurationManager Class: In C#, you can access the settings defined in `App.config` using the `ConfigurationManager` class, which is part of the `System.Configuration` namespace. This class provides methods and properties to read configuration settings.  
    

If you are using the .NET Framework and still facing issues with `ConfigurationManager`, there are a few more steps you can take to troubleshoot the problem:

- Check System.Configuration Reference:
    - Right-click on your project in Visual Studio.
    - Choose "Add" -> "Reference..."
    - In the Reference Manager, make sure that `System.Configuration` is checked.
- Verify Namespace and Using Directive:
- Ensure that your code file has the following using directive:

using System.Configuration;

- Framework Version:
    - Confirm that your project is targeting a version of the .NET Framework that supports `ConfigurationManager`. Open your project properties and check the "Target framework" setting. It should be set to a version of the .NET Framework (e.g., .NET Framework 4.x).
- Clean and Rebuild:
    - Try cleaning and rebuilding your solution. Right-click on your solution in Solution Explorer and choose "Clean," then right-click again and choose "Rebuild."
- Restart Visual Studio:
    - Sometimes, issues can be resolved by restarting Visual Studio.

  

Remember that the `App.config` is primarily used during development. In production, the settings are often moved to the application's actual configuration file.


```csharp
<?xml version="1.0" encoding="utf-8" ?>
<configuration>

  <!-- 1. App Settings -->
  <appSettings>
    <add key="AppName" value="My Custom Serializer" />
    <add key="MaxItems" value="100" />
  </appSettings>

  <!-- 2. Connection Strings -->
  <connectionStrings>
    <add name="DefaultConnection" 
         connectionString="Server=.;Database=Your_DB;Trusted_Connection=True;"  // "." Local
         providerName="System.Data.SqlClient" />
  </connectionStrings>

  <!-- 3. Logging (Example with NLog, can be other loggers) -->
  <configSections>
    <section name="nlog" type="NLog.Config.ConfigSectionHandler, NLog" requirePermission="false" />
  </configSections>

  <nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <targets>
      <target name="file" xsi:type="File" fileName="log.txt" />
    </targets>
    <rules>
      <logger name="*" minlevel="Info" writeTo="file" />
    </rules>
  </nlog>

  <!-- 4. Startup Configuration -->
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
  </startup>

  <!-- 5. System.ServiceModel (WCF Clients or Services) -->
  <system.serviceModel>
    <client>
      <endpoint address="http://localhost:1234/MyService"
                binding="basicHttpBinding"
                contract="IMyService" />
    </client>
  </system.serviceModel>

  <!-- 6. User-defined custom configuration sections (optional) -->
  <!-- Example:
  <mySection>
    <serializer type="json" />
  </mySection>
  -->

</configuration>

```


## [ref]([How to use App.config Examlpe | ProgrammingAdvices](https://programmingadvices.com/courses/16-oop-in-c21/lectures/50575485))


> Live update of app.config
```csharp
 private void Initiate()
 {
     stSettings = new settings();

     var config = ConfigurationManager.OpenExeConfiguration(ConfigurationUserLevel.None);

     stSettings.connectionString = config.ConnectionStrings.ConnectionStrings["DefaultConnection"]?.ConnectionString;

     if (string.IsNullOrEmpty(stSettings.connectionString))
         stSettings.connectionString = "Server=.;Database=Contacts;Trusted_Connection=True;";


     stSettings.saveDirectory = config.AppSettings.Settings["saveDirectory"].Value;
     if (string.IsNullOrEmpty(stSettings.saveDirectory))
         stSettings.saveDirectory = "C:\\CodingEnviorenment\\DatabaseBackup";


     stSettings.logDirectory = config.AppSettings.Settings["logDirectory"].Value;
     if (string.IsNullOrEmpty(stSettings.logDirectory))
         stSettings.logDirectory = "C:\\CodingEnviorenment\\DatabaseBackup\\logs";


     if (Int32.TryParse(config.AppSettings.Settings["intervalInMinutes"].Value, out int result))
         stSettings.intervalInMinutes = result;
     else
         stSettings.intervalInMinutes = 60;


     Directory.CreateDirectory(stSettings.logDirectory);
 }
```