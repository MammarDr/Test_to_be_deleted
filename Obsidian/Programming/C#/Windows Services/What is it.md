
#### What is Windows Service?
> W + R then services.ms


#### Definition: A Windows Service is:

A long-running executable application that runs in the background of the Windows operating system.
Unlike regular applications, it doesn’t require user
interaction and can be started automatically when the computer boots up.
Running continuously without a user interface


##### Which component manages the lifecycle of a Windows Service?
> Service Control Manager (SCM)

##### Which lifecycle states can a Windows Service have?
> Running, Paused, Stopped, Restarting


### Requirments :
> VSC 22 & .NET Desktop


1. Create Windows Service(.NET Framework)
2. [Follow this]([Installing and Running Your Service | ProgrammingAdvices](https://programmingadvices.com/courses/2662580/lectures/58840670))

### Service Start-Up types

![[Pasted image 20250717111546.png]]

### Installing and Running Your Service

```cmd
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\InstallUtil.exe "MyNewService.exe"
```
> the " " are used in case the name include spaces "My New Service.exe"

### Start a Service

```cmd
sc start "YourServiceName"
```

### Stop a Service

```cmd
sc stop "YourServiceName"
```

### Delete a Service

```cmd
sc delete "YourServiceName"
```

### Edit a Service Start-Up Type
```cmd
sc config "YourServiceName" start=auto
sc config "YourServiceName" start=delayed-auto
sc config "YourServiceName" start=manual
```


#### Add Dependency to a Service
```cmd
sc config <ServiceName> depend= <Dependency1>/<Dependency2>
```

### Using PowerShell

```powershell
Start-Service  -Name  "MyFirstWinService"
Stop-Service   -Name  "MyFirstWinService"
Get-Service    -Name  "MyFirstWinService"

Set-Service -Name "YourServiceName" -StartupType AutomaticDelayedStart
```

### Kill a Process

```cmd
taskkill /f /pid <ProcessID>
```


### Task Scheduler
> can make you customize how and when the service or app can run or stop.


**Built-in Windows services** are pre-installed services that are part of the Windows operating system. These services are designed to handle core system tasks, such as managing hardware, networks, security, and user sessions.


## Service Types

### **Automatic Services**
### **Automatic (Delayed Start) Services**
### **Manual Services**
### **Disabled Services**

### **Standalone Services**
**Description**: These services operate independently without relying on other services.

### **Dependent Services**
- **Description**: These services depend on other services to function. They cannot start or run unless their dependencies are active.

> Get service dependencies.
```cmd
sc qc Spooler
```

### **Interactive Services**
- **Description**: These services interact with the desktop or user interface. They are rare in modern systems due to security restrictions.
- **Examples**:
    - Legacy backup or monitoring services that display progress on the user's desktop.

### **Triggered Start Services**
- **Description**: Start automatically in response to specific triggers, such as network availability or device insertion.

### **Network Services**
- **Description**: Provide networking capabilities and manage communication protocols.

### **Background Services**
- **Description**: Run in the background to handle system-level tasks or application-specific functions.


###  What Are Service Priorities?

Service priorities determine the **order of execution** and the **resource allocation** for Windows Services. Each service runs as a process or thread, and Windows assigns it a **priority level** to control how much CPU time it receives relative to other processes.

![[Pasted image 20250720144519.png]]


#### **Change the Priority**:
```cmd
wmic process where name="MyService.exe" CALL setpriority 128
```


- Priority Codes:
    - **256**: Real-Time
    - **128**: High
    - **32768**: Above Normal
    - **32**: Normal
    - **16384**: Below Normal
    - **64**: Idle


```csharp

protected ovveride OnStart() {
	Process process = Process.GetCurrentProcess();
	process.PriorityClass = ProcessPriorityClass.High;
}
           
```

### Recovery Options in Windows Services

![[Pasted image 20250721090239.png]]

sc failure <ServiceName\>
reset= <TimeInSeconds\>
actions= <Action1\>/<Delay1\>/<Action2\>/<Delay2\>/<Action3\>/<Delay3\>

```cmd
sc failure MyCustomService reset= 86400 actions= restart/60000/restart/60000/""/0
```


```csharp
namespace FaultHandlingService
{
    public partial class MyCustomService : ServiceBase
    {
        private string logFilePath = @"C:\ServiceLogs\RecoveryLog.txt";

        public MyCustomService()
        {
            InitializeComponent();
        }

        protected override void OnStart(string[] args)
        {
            LogEvent("Service Started");

            // Start a background task with fault handling
            Thread workerThread = new Thread(WorkerTask);
            workerThread.Start();
        }

        protected override void OnStop()
        {
            LogEvent("Service Stopped");
        }

        private void WorkerTask()
        {
            try
            {
                // Simulate work
                while (true)
                {
                    LogEvent("Service is running...");
                    Thread.Sleep(5000);

                    // Simulate a failure
                    throw new Exception("Simulated error for testing recovery.");
                }
            }
            catch (Exception ex)
            {
                LogEvent($"Error: {ex.Message}");
                // Exit the process to simulate failure
                Environment.Exit(1);
            }
        }

        private void LogEvent(string message)
        {
            string logMessage = $"[{DateTime.Now:yyyy-MM-dd HH:mm:ss}] {message}";
            File.AppendAllText(logFilePath, logMessage + Environment.NewLine);
        }

        public static void Main()
        {
            ServiceBase.Run(new MyCustomService());
        }
    }
}
```

### Differences Between Local and Network Services

![[Pasted image 20250720164434.png]]
---
```csharp
public class ProjectInstaller : Installer
{
    private ServiceProcessInstaller processInstaller;
    private ServiceInstaller serviceInstaller;

    public ProjectInstaller()
    {
        // Use NetworkService account
        processInstaller = new ServiceProcessInstaller
        {
            Account = ServiceAccount.NetworkService
        };

        // Define the service
        serviceInstaller = new ServiceInstaller
        {
            ServiceName = "MyCustomNetworkService",
            DisplayName = "My Custom Network Service",
            StartType = ServiceStartMode.Automatic
            ServicesDependedOn = new string[] { "RpcSs", "EventLog" } // Dependencies
        };

        Installers.Add(processInstaller);
        Installers.Add(serviceInstaller);
    }
```


---

## **What is the Service Lifecycle?**

The **service lifecycle** refers to the various stages a Windows Service goes through during its operation.
The **Services Control Manager (SCM)** is a core component of the Windows operating system responsible for managing the lifecycle of all Windows services


| **Action**             | **Status (State)** | **Event Triggered**          | **Description**                                                     |
| ---------------------- | ------------------ | ---------------------------- | ------------------------------------------------------------------- |
| System boots           | `Stopped`          | –                            | The service exists but hasn’t started yet.                          |
| Service is started     | `Start Pending`    | `StartService()`             | The service is initializing (constructor, `OnStart`).               |
| Startup completes      | `Running`          | –                            | Service is now actively running and responding to commands.         |
| User pauses service    | `Pause Pending`    | `SERVICE_CONTROL_PAUSE`      | The SCM requests the service to pause.                              |
| Pause completes        | `Paused`           | –                            | The service has stopped processing temporarily but stays in memory. |
| User resumes service   | `Continue Pending` | `SERVICE_CONTROL_CONTINUE`   | The SCM tells the service to resume activity.                       |
| Resume completes       | `Running`          | –                            | The service returns to the running state.                           |
| User stops service     | `Stop Pending`     | `SERVICE_CONTROL_STOP`       | The SCM instructs the service to stop (`OnStop`).                   |
| Stop completes         | `Stopped`          | –                            | The service has fully shut down.                                    |
| Service is restarted   | `Start Pending`    | `StartService()` again       | Reinitializing the service.                                         |
| Service is deleted     | –                  | `sc delete` or via code      | Removes service entry (but only after stop).                        |
| Service fails to start | `Stopped`          | Exception during `OnStart()` | The service crashes or returns an error during startup.             |
| Service crashes        | `Stopped`          | Unhandled Exception          | The service process dies unexpectedly.                              |
| Startup type: Disabled | `Stopped`          | –                            | The service is disabled and cannot be started until re-enabled.     |
> OnShutdown can be used to make your logic if the user is shuting down the pc, thanks to windows informing services.

#Bonus 

| Feature           | PowerShell                                  | CMD (Command Prompt)                  |
| ----------------- | ------------------------------------------- | ------------------------------------- |
| **Purpose**       | Advanced scripting & automation shell       | Basic command-line interpreter        |
| **Language**      | Uses **PowerShell language** (object-based) | Uses **batch** scripting (text-based) |
| **Output Type**   | **Objects** (rich data)                     | **Plain text**                        |
| **Extensibility** | Very extensible (via .NET, modules)         | Very limited                          |
