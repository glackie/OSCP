Specific results with different characters may be separated as delimiters. 

Here it is handy to know how to remove specific delimiters and show the words on a line in a specified position. 

One of the tools that can be used for this is `cut`. Therefore we use the option "`-d`" and set the delimiter to the colon character (`:`) and define with the option "`-f`" the position in the line we want to output.

```shell-session
glack@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | cut -d":" -f1

root
sync
mrb3n
cry0l1t3
htb-student
```

useful flags:

-f, --fields=LIST

-d, --delimiter=DELIM   use DELIM instead of TAB for field delimiter

