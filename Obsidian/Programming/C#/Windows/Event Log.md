
in computing, an event log is a file or database used to store events that occur in a system. These events can include information about errors, warnings, system events, user activities, and more. Event logs are commonly used for troubleshooting, monitoring, and auditing purposes.

  

To run the Windows Event Viewer, you can follow these steps:

## 1. Open the Start Menu:

-  In Windows 10, you can click on the Windows icon in the bottom left corner of your screen.

## 2. Search for "Event Viewer":

-  Type "Event Viewer" into the search bar.

## 3. Open Event Viewer:

-  Click on the "Event Viewer" app that appears in the search results.

## 4. Navigate to Event Logs:

-  In the Event Viewer window, you'll see a left-hand navigation pane. Click on "Windows Logs" to expand it.

## 5. Choose a Log:

-  Under "Windows Logs," you'll find several logs like "Application," "Security," "Setup," "System," and more. Choose the log you're interested in viewing. For the example provided earlier, you would likely find the log entry in the "Application" log.

## 6. View Log Entries:

-  Click on the log you selected, and you'll see a list of log entries in the center pane. Look for the entry created by your C# program.

The logs in the Event Viewer contain information about various events on your system, including system errors, warnings, information events, and more. You can filter, sort, and search for specific events within the Event Viewer interface to help you locate the information you need.


### ⚠️ Administrator Rights

```csharp
using System.Diagnostics;

// Specify the source name for the event log 
string sourceName = "MyApp";

// Create the event source if it does not exist 
if (!EventLog.SourceExists(sourceName)) {
	EventLog.CreateEventSource(sourceName, "Application");
	Console.WriteLine("Event source created."); 
}
// Log an information event 
EventLog.WriteEntry(sourceName, "This is an Information event.", 
				    EventLogEntryType.Information);

EventLog.WriteEntry(sourceName, "This is an Warning event.", 
				    EventLogEntryType.Warning);
				    
EventLog.WriteEntry(sourceName, "This is an Error event.", 
				    EventLogEntryType.Error);
				    
Console.WriteLine("Event written to the log.");
```
