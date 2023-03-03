WMI is a subsystem of PowerShell that provides system administrators with powerful tools for system monitoring. 

The goal of WMI is to consolidate device and application management across corporate networks. 

WMI is a core part of the Windows operating system and has come pre-installed since Windows 2000. 

It is made up of the some components(no in this notes but can be found online)

Some of the uses for WMI are:

-   Status information for local/remote systems
-   Configuring security settings on remote machines/applications
-   Setting and changing user and group permissions
-   Setting/modifying system properties
-   Code execution
-   Scheduling processes
-   Setting up logging

These tasks can all be performed using a combination of PowerShell and the WMI Command-Line Interface (WMIC). 

WMI can be run via the Windows command prompt by typing `WMIC` to open an interactive shell or by running a command directly such as `wmic computersystem get name` to get the hostname. 

We can view a listing of WMIC commands and aliases by typing `WMIC /?`.

WMIC uses aliases and associated verbs, adverbs, and switches. 

```cmd-session
C:\htb> wmic os list brief
```

The above command example uses `LIST` to show data and the adverb `BRIEF` to provide just the core set of properties. 

An in-depth listing of verbs, switches, and adverbs is available [here](https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmic). 

WMI can be used with PowerShell by using the `Get-WmiObject` [module](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-wmiobject?view=powershell-5.1). 

This module is used to get instances of WMI classes or information about available classes. 

This module can be used against local or remote machines.

Here we can get information about the operating system.

```powershell-session
PS C:\htb> Get-WmiObject -Class Win32_OperatingSystem | select SystemDirectory,BuildNumber,SerialNumber,Version | ft
```

We can also use the `Invoke-WmiMethod` [module](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/invoke-wmimethod?view=powershell-5.1), which is used to call the methods of WMI objects. A simple example is renaming a file. We can see that the command completed properly because the `ReturnValue` is set to 0.

```powershell-session
PS C:\htb> Invoke-WmiMethod -Path "CIM_DataFile.Name='C:\users\public\spns.csv'" -Name Rename -ArgumentList "C:\Users\Public\kerberoasted_users.csv"
```

