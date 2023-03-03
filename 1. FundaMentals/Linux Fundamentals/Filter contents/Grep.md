More often, we will only search for specific results that contain patterns we have defined. One of the most used tools for this is `grep`, which offers many different features. 

Accordingly, we can search for users who have the default shell "`/bin/bash`" set as an example.

```shell-session
glack@htb[/htb]$ cat /etc/passwd | grep "/bin/bash"

root:x:0:0:root:/root:/bin/bash
mrb3n:x:1000:1000:mrb3n:/home/mrb3n:/bin/bash
cry0l1t3:x:1001:1001::/home/cry0l1t3:/bin/bash
htb-student:x:1002:1002::/home/htb-student:/bin/bash
```

Another possibility is to exclude specific results. For this, the option "`-v`" is used with `grep`.

In the next example, we exclude all users who have disabled the standard shell with the name "`/bin/false`" or "`/usr/bin/nologin`".

```shell-session
glack@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin"

root:x:0:0:root:/root:/bin/bash
sync:x:4:65534:sync:/bin:/bin/sync
postgres:x:111:117:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
user6:x:1000:1000:,,,:/home/user6:/bin/bash
```

Some of the most commonly used switches include -r for recursive searching and -i to ignore text case

