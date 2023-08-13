Using the `Get-Acl` PowerShell cmdlet, we can examine service permissions by targeting the path of a specific service in the registry.

```powershell-session
PS C:\Users\htb-student> Get-ACL -Path HKLM:\System\CurrentControlSet\Services\wuauserv | Format-List
```


Notice how this command returns specific account permissions in an easy-to-read format and in SDDL. 

Also, the SID that represents each security principal (User and/or Group) is present in the SDDL. 

This is something we do not get when running `sc` from the command prompt.

Knowing how to interact with services and their associated permissions from the command line makes it easier to script out these tasks. 

While it is good to know how to perform these tasks from the GUI, it does not scale well as we start getting into larger network environments and Domains.