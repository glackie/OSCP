Services are a major component of the Windows operating system. They allow for the creation and management of long-running processes. Windows services can be started automatically at system boot without user intervention. These services can continue to run in the background even after the user logs out of their account on the system.


Applications can also be created to install as a service, such as a network monitoring application installed on a server. 

Services on Windows are responsible for many functions within the Windows operating system, such as networking functions, performing system diagnostics, managing user credentials, controlling Windows updates, and more.

Windows services are managed via the Service Control Manager (SCM) system, accessible via the `services.msc` MMC add-in.

>services.msc - we can run this command in run to open the tab

>We can also run "msconfig" for additional 
>msconfig is use to run system configuration

This add-in provides a GUI interface for interacting with and managing services and displays information about each installed service. This information includes the service Name, Description, Status, Startup Type, and the user that the service runs under.

 is also possible to query and manage services via the command line using `sc.exe` using [PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview?view=powershell-7) cmdlets such as `Get-Service`.

```powershell-session
PS C:\htb> Get-Service | ? {$_.Status -eq "Running"} | select -First 2 |fl
```

