There will come moments when we want to change specific names in the whole file or standard input. One of the tools we can use for this is the stream editor called `sed`. 

One of the most common uses of this is substituting text. Here, `sed` looks for patterns we have defined in the form of regular expressions (regex) and replaces them with another pattern that we have also defined. Let us stick to the last results and say we want to replace the word "`bin`" with "`HTB`."

	Syntax and usage
The "`s`" flag at the beginning stands for the substitute command. 

Then we specify the pattern we want to replace. After the slash (`/`), we enter the pattern we want to use as a replacement in the third position. Finally, we use the "`g`" flag, which stands for replacing all matches.

```shell-session
glack@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}' | sed 's/bin/HTB/g'

root /HTB/bash
sync /HTB/sync
mrb3n /HTB/bash
cry0l1t3 /HTB/bash
htb-student /HTB/bash
``` 

another example:

	kali@kali:~$ echo "I need to try hard" | sed 's/hard/harder/' 
	
	I need to try harder

