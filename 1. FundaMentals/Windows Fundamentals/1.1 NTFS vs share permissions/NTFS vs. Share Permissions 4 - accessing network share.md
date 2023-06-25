>Note: A server is technically a software function used to service the requests of a client. In this case, the Pwnbox is our client, and the Windows 10 target box is our server.


#### Using smbclient to Connect to the Share

  Using smbclient to Connect to the Share

```shell-session
glack@htb[/htb]$ smbclient -L IPaddressOfTarget -U htb-student
Enter WORKGROUP\htb-student's password: 
```

What could potentially block us from accessing this share if all our entries are correct and our permissions list has the Everyone group present with at least Read permissions?



do_connect: Connection to 10.129.132.66 failed (Error NT_STATUS_IO_TIMEOUT)

>read next note


