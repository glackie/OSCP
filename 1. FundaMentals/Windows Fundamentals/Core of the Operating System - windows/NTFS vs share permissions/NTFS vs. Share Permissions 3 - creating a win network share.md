To get a solid fundamental understanding of SMB and it's relationship to NTFS, we will create a network share on the `Windows 10 target box`.

In this case, we will create a shared folder by first creating a new folder on the Windows 10 desktop.

>Keep in mind that in most large enterprise environments, shares are created on a Storage Area Network (SAN), Network Attached Storage device (NAS), or a separate partition on drives accessed via a server operating system like Windows Server.
>If we ever come across shares on a desktop operating system, it will either be a small business or it could be a beachhead system used by a penetration tester or malicious attacker to gather and exfiltrate data.

We are going to use the `Advanced Sharing` option to configure our share.

Similar to NTFS permissions, there is an `access control list` (`ACL`) for shared resources. 
We can consider this the SMB permissions list. Keep in mind that with shared resources, both the SMB and NTFS permissions lists apply to every resource that gets shared in Windows.

The ACL contains `access control entries` (`ACEs`). Typically these ACEs are made up of `users` & `groups` (also called security principals) as they are a suitable mechanism for managing and tracking access to shared resources.


Notice the default access control entry and permissions settings.

#### Share Permissions ACL (Sharing Tab)

![share permissions](https://academy.hackthebox.com/storage/modules/49/share_permissions.png)

For now, we are going to apply these settings to test the effect of this ACL and the permissions applied as-is. 

We will test connectivity from the my kali system (my own) by opening terminal and using `smbclient`.

