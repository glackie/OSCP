The `Server Message Block protocol` (`SMB`) is used in Windows to connect shared resources like files and printers. It is used in large, medium, and small enterprise environments. See the image below to visualize this concept:

![smb diagram](https://academy.hackthebox.com/storage/modules/49/smb_diagram.png)

>NTFS permissions and share permissions are often understood to be the same. Please know that they are not the same but often apply to the same shared resource. 

Let’s take a look at the individual permissions that can be set to secure/grant objects access to a network share hosted on a Windows OS running the NTFS file system.

#### Share permissions

Permission - Description

`Full Control` - Users are permitted to perform all actions given by Change and Read permissions as well as change permissions for NTFS files and subfolders

`Change` - Users are permitted to read, edit, delete and add files and subfolders

`Read` - Users are allowed to view file & subfolder contents


#### NTFS Basic permissions

`Full Control` - Users are permitted to add, edit, move, delete files & folders as well as change NTFS permissions that apply to all allowed folders

`Modify` - Users are permitted or denied permissions to view and modify files and folders. This includes adding or deleting files

`Read & Execute` - Users are permitted or denied permissions to read the contents of files and execute programs

`List folder contents` - Users are permitted or denied permissions to view a listing of files and subfolders

`Read` - Users are permitted or denied permissions to read the contents of files

`Write` - Users are permitted or denied permissions to write changes to a file and add new files to a folder

`Special Permissions` - A variety of advanced permissions options

