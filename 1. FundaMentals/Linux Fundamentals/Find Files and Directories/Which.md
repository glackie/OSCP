One of the common tools is `which`. This tool returns the path to the file or link that should be executed. 

This allows us to determine if specific programs, like cURL, netcat, wget, python, gcc, are available on the operating system. 

Let us use it to search for Python in our interactive instance.

```shell-session
glack@htb[/htb]$ which python

/usr/bin/python
```

If the program we search for does not exist, no results will be displayed.

