User management is an essential part of Linux administration.

Sometimes we need to create new users or add other users to specific groups. 

Another possibility is to execute commands as a different user. 

After all, it is not too rare that users of only one specific group have the permissions to view or edit specific files or directories.

This, in turn, allows us to collect more information locally on the machine, which can be very important. 

Let us take a look at the following example of how to execute code as a different user.


#### Execution as a user

  Execution as a user

```shell-session
glack@htb[/htb]$ cat /etc/shadow

cat: /etc/shadow: Permission denied
```


#### Execution as root

  Execution as root

```shell-session
glack@htb[/htb]$ sudo cat /etc/shadow

root:<SNIP>:18395:0:99999:7:::
daemon:*:17737:0:99999:7:::
bin:*:17737:0:99999:7:::
<SNIP>
```

