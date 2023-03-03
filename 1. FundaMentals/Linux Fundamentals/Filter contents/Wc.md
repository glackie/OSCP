Last but not least, it will often be useful to know how many successful matches we have. To avoid counting the lines or characters manually, we can use the tool `wc`. With the "`-l`" option, we specify that only the lines are counted.

```shell-session
glack@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}' | wc -l

5
```

useful options for wc

 -c, --bytes            print the byte counts

 -m, --chars            print the character counts

 -l, --lines            print the newline counts

 -w, --words            print the word counts