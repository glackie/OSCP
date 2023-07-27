### Nmap
Only port 22, 80 and 443 are open

### HTTP
1. Robots.txt

```
ser-agent: *
fsocity.dic
key-1-of-3.txt
```


Found a 86 word dictionary in fsocity.dic
Found the 1st key in /key-1-of-3.txt


### WPlogin
Found a login Page at

http://10.10.212.50/wp-login.php

However default creds were not working and got dictionary was too big for brute force attack using burp suite

Got stuck here and went for the writeup 


Got to know that username we had to was "elliot"


And I also figured out my wpscan error and used below command to get valid credentials

```
wpscan --url http://10.10.212.50/wp-login.php -P ufsociety.list --usernames elliot
```

after getting the credentails I read and tried multiple writeups, i tried multiple exploitations


Upload a php reverse shell in upload  plugin failed

and had to use another writeup for detailed walkthrough 

https://unicornsec.com/home/tryhackme-mr-robot-ctf

Used this resource 


### Shell

Used previously mentioned resource for getting a shell

Changed the content of a 404.php file

Generated an error to view the 404 page and trigger the exploit

	Started a netcat listener before this

And i got a shell as deamon user

```$ whoami
daemon
```


Gettting shell as robot

in the /home directory of robot we find a file named

password.raw-md5


I cracked the hash inside the file using

```
hashcat -m 0 robot.hash -a 0 /usr/share/wordlists/rockyou.txt 
```


```
c3fcd3d76192e4007dfb496cca67e13b:abcdefghijklmnopqrstuvwxyz
```


### Priv ESC

ound something interesting in /etc/crontab


```
bitnami cd /opt/bitnami/stats && ./agent.bin --run -D
```


Got stuck here and went back to the writeup


Truns out I missed the nmap in SUID find command output


used [[nmap - SUID]] to escalate privileges on the machine and get the root flag


