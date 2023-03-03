we can use `services.msc` to view and manage just about every detail regarding all services. 

Let's take a closer look at the service associated with `Windows Update` (`wuauserv`).

![services.msc](https://academy.hackthebox.com/storage/modules/49/service_properties.png)

Make a note of the different properties available for viewing and configuration. 

Knowing the service name is especially useful when using command-line tools to examine and manage services. 

Path to the executable is the full path to the program and command to execute when the service starts. 

If the NTFS permissions of the destination directory are configured with weak permissions, an attacker could replace the original executable with one created for malicious purposes.

![Log On tab](https://academy.hackthebox.com/storage/modules/49/logon.png)

Most services run with LocalSystem privileges by default which is the highest level of access allowed on an individual Windows OS.

Not all applications need Local System account-level permissions, so it is beneficial to perform research on a case-by-case basis when considering installing new applications in a Windows environment. 

It is a good practice to identify applications that can run with the least privileges possible to align with the principle of least privilege.

[Here is one breakdown of the principle of least privilege](https://us-cert.cisa.gov/bsi/articles/knowledge/principles/least-privilege)

Notable built-in service accounts in Windows:

-   LocalService
    
-   NetworkService
    
-   LocalSystem
    

>Note: We can also create new accounts and use them for the sole purpose of running a service.

![Recovery tab](https://academy.hackthebox.com/storage/modules/49/recovery_screen.png)

The recovery tab allows steps to be configured should a service fail.
Notice how this service can be set to run a program after the first failure. 
This is yet another vector that an attacker could use to run malicious programs by utilizing a legitimate service.