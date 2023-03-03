Lets jump back to our xfreerdp session with our Windows 10 target box and take a look at the NTFS permissions on the Company Data folder.

![ntfs](https://academy.hackthebox.com/storage/modules/49/ntfs.png)

There's more granular control with NTFS permissions that can be applied to users and groups. Anytime we see a gray checkmark next to a permission, it was inherited from a parent directory. By default, all NTFS permissions are inherited from the parent directory. In the Windows world, the `C:\ drive` is the parent directory to rule all directories unless a system administrator were to disable inheritance inside a newly created folder’s advanced Security settings.

In many cases, the system administrator(s) of an organization would be responsible for deciding what permissions a user or group of users gets over network resources. 

This is why many spear-phishing attacks are directed at system administrators and other IT leaders. They have lots of influence over what is allowed in the environments they oversee, even more so than an organization's non-technical c-level leaders in many cases. 

For example, the doctors or executives working in a hospital will not have administrative rights over the network, but the system administrators will.

Now lets give the Everyone group `Full control` at the share level and test the impact of the change by trying to create a mount point to the share from the Desktop of our Pwnbox

#### Mounting to the Share

  Mounting to the Share

```shell-session
glack@htb[/htb]$ sudo mount -t cifs -o username=htb-student,password=Academy_WinFun! //ipaddoftarget/"Company Data" /home/user/Desktop/
```

If this command is not working check the syntax. If the syntax is correct yet the command is still working, `cifs-utils` may need to be installed. This can be done with the following command

sudo mount -t cifs -o username=htb-student,password=Academy_WinFun! //ipaddoftarget/"Company Data" /home/user/Desktop/

>This is just an example and it didnt work whn i tried it on htb pawnbox because cifs-utils was not being installed in pwnbox so i am just countinuing without this activity


The `net share` command allows us to view all the shared folders on the system. Notice the share we created and also the C:\ drive.

`Do you remember us sharing the C:\ drive?`

We didn't manually share C:. The most important drive with the most critical files on a Windows system is shared via SMB at install. This means anyone with the proper access could remotely access the entire C:\ of each Windows system on a network.

We can also see the share we created.

#### Displaying Shares using net share

  Displaying Shares using net share

```cmd-session
C:\Users\htb-student> net share

Share name   Resource                        Remark

-------------------------------------------------------------------------------
C$           C:\                             Default share
IPC$                                         Remote IPC
ADMIN$       C:\WINDOWS                      Remote Admin
Company Data C:\Users\htb-student\Desktop\Company Data

The command completed successfully.
```

`Computer Management` is another tool we can use to identify and monitor shared resources on a Windows system.

#### Monitoring Shares from Computer Management

![computer management](https://academy.hackthebox.com/storage/modules/49/computer_management.png)

We can poke around in `Shares`, `Sessions`, and `Open Files` to get an idea of what information this provides us. Should there be a situation where we assist an individual or organization with responding to a breach related to SMB, these are some great places to check and start to understand how the breach may have happened and what may have been left behind.


