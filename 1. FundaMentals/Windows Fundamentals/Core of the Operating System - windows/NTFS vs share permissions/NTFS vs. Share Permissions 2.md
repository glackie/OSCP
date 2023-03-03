#### NTFS special permissions

Permission

Description

`Full control` - Users are permitted or denied permissions to add, edit, move, delete files & folders as well as change NTFS permissions that apply to all permitted folders

`Traverse folder / execute file` - Users are permitted or denied permissions to access a subfolder within a directory structure even if the user is denied access to contents at the parent folder level. Users may also be permitted or denied permissions to execute programs

`List folder/read data` - Users are permitted or denied permissions to view files and folders contained in the parent folder. Users can also be permitted to open and view files

`Read attributes` - Users are permitted or denied permissions to view basic attributes of a file or folder. Examples of basic attributes: system, archive, read-only, and hidden

`Read extended attributes` - Users are permitted or denied permissions to view extended attributes of a file or folder. Attributes differ depending on the program

`Create files/write data` - Users are permitted or denied permissions to create files within a folder and make changes to a file

`Create folders/append data` - Users are permitted or denied permissions to create subfolders within a folder. Data can be added to files but pre-existing content cannot be overwritten

`Write attributes` - Users are permitted or denied to change file attributes. This permission does not grant access to creating files or folders

`Write extended attributes` - Users are permitted or denied permissions to change extended attributes on a file or folder. Attributes differ depending on the program

`Delete subfolders and files` - Users are permitted or denied permissions to delete subfolders and files. Parent folders will not be deleted

`Delete` - Users are permitted or denied permissions to delete parent folders, subfolders and files.

`Read permissions` - Users are permitted or denied permissions to read permissions of a folder

`Change permissions` -- Users are permitted or denied permissions to change permissions of a file or folder

`Take ownership` - Users are permitted or denied permission to take ownership of a file or folder. The owner of a file has full permissions to change any permissions


>Keep in mind that NTFS permissions apply to the system where the folder and files are hosted. 
>Folders created in NTFS inherit permissions from parent folders by default. 
>It is possible to disable inheritance to set custom permissions on parent and subfolders, as we will do later in this module.


>The share permissions apply when the folder is being accessed through SMB, typically from a different system over the network.
>This means someone logged in locally to the machine or via RDP can access the shared folder and files by simply navigating to the location on the file system and only need to consider NTFS permissions.
>The permissions at the NTFS level provide administrators much more granular control over what users can do within a folder or file.