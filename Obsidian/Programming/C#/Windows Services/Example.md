
```csharp
public partial class MicrosoftRewardService : ServiceBase
{
    string logDirectory;
    string logFilePath;

    public MicrosoftRewardService()
    {
        InitializeComponent();

        CanPauseAndContinue = true; 
        //The service supports pausing and resuming operations

        CanShutdown = true;         
        //The service is notified when the system shuts down.


        logDirectory = ConfigurationManager.AppSettings["LogDirectoy"]; 
        //Reads log directory from an external Configuration file (App.Config) for flexibility

        // Valid name
        if (string.IsNullOrEmpty(logDirectory))
            throw new ConfigurationErrorsException
	            ("LogDirectory is not specified in the configuration file");

        // Create if not yet
        if(!Directory.Exists(logDirectory))
        {
            Directory.CreateDirectory(logDirectory);
        }

        logFilePath = Path.Combine(logDirectory, "ServiceStateLog.txt");
    }


	protected ovveride void OnStart(string[] args) {
		// Implement Your Logic
	}

	protected ovveride void OnStop() {
		// Implement Your Logic
	}

	protected ovveride void OnPause() {
		// Implement Your Logic
	}

	protected ovveride void OnContinue() {
		// Implement Your Logic
	}

	protected ovveride void OnShutdown() {
		// Implement Your Logic
	}

}
```

> Must reference System.Configuration(.Install)

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
	<appSettings>
		<add key="LogDirectory" value="c:\ServiceLogs"/>
	</appSettings>
</configuration>
```


##### To Debug

> in program.cs

```csharp
static void Main()
{
	if(Environment.UserInteractive) {
		Console.WriteLine("Running in console mode...");
		FileMonitoring service = new FileMonitoring();
		service.StartInConsole();
	} else {
		ServiceBase[] ServicesToRun;
	    ServicesToRun = new ServiceBase[]
	    {
	        new MicrosoftRewardService()
	    };
	    ServiceBase.Run(ServicesToRun);
	}
    
}
```

> in service file
```csharp

public void log(string message) {

	// only in debug
	if(Environment.UserInteractive) 
	{
		Console.WriteLine(message);
	}
}



public void StartInConsole() {
	OnStart(null);
	Console.WriteLine("Press anything to stop the service..");
	Console.ReadLine();
	OnStop();
	Console.ReadLine();
}
```

Project Property -> Output Type -> Console Application

---

#### Must add Installer class

```csharp
public partial class ProjectInstaller : Installer
{

    private ServiceProcessInstaller serviceProcessInstaller;
    private ServiceInstaller serviceInstaller;

    public ProjectInstaller()
    {
        InitializeComponent();


        // Configure the Service Process Installer
        serviceProcessInstaller = new ServiceProcessInstaller
        {
            Account = ServiceAccount.LocalSystem 
            // Adjust as needed (e.g., NetworkService, LocalService)
        };

        // Configure the Service Installer
        serviceInstaller = new ServiceInstaller
        {
            ServiceName = "MyNewService", 
            // Must match the ServiceName in your ServiceBase class
            
            DisplayName = "My First Windows Service",
	        Description = "Describe your service",
            
            StartType = ServiceStartMode.Automatic,
            // Or Manual, depending on requirements

			ServicesDependedOn = new string[] {"RpcSs", "EventLog", "LanmanWorkstation"}
        };

        // Add installers to the installer collection
        Installers.Add(serviceProcessInstaller);
        Installers.Add(serviceInstaller);
    }
}
```

> MSSQLSERVER if using SQL SERVER

#Bonus 

> using FileSystemWatcher require some services to be On.

1- RepcSs
2- EventLog
2- LanmanWorkstation


#### what will happen if they got stopped ?

suddenly your service is not working anymore and u wonder why.

```cs
ServicesDependedOn = new string[] {"RpcSs", "EventLog", "LanmanWorkstation"}
```