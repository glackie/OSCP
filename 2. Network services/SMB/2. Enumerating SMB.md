Typically, there are SMB share drives on a server that can be connected to and used to view or transfer files. 
SMB can often be a great starting point for an attacker looking to discover sensitive information — you'd be surprised what is sometimes included on these shares.

<mark style="background: #FF5582A6;">**Enum4Linux**</mark>

Enum4linux is a tool used to enumerate SMB shares on both Windows and Linux systems. It is basically a wrapper around the tools in the Samba package and makes it easy to quickly extract information from the target pertaining to SMB.

The syntax of Enum4Linux is nice and simple: **"enum4linux [options] ip"**


**TAG**            **FUNCTION**  

-U             get userlist  
-M             get machine list  
-N             get namelist dump (different from -U and-M)  
-S             get sharelist  
-P             get password policy information  
-G             get group and member list

-a             all of the above (full basic enumeration)

<mark style="background: #FF5582A6;">HOW to list down shares using SMB client</mark>


┌──(root💀kali)-[~]
└─# smbclient -L \\10.129.73.155  