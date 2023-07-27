## A. Enumeration

## A.1 nmap

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 6d2c401b6c157cfcbf9b5522612a56fc (RSA)
|   256 ff893298f4779c0939f5af4a4f08d6f5 (ECDSA)
|_  256 899263e71d2b3aaf6cf939565b557ef9 (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel


PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)
```


## A.2 HTTP

1. index.html is a default apache server installation
2. found nothing in source code and robots.txt
3. ran a gobuster to find directories

```
┌─[glack@parrot]─[~/Desktop/THM/Lian_Yu]
└──╼ $gobuster dir -u http://10.10.34.3/ -w /usr/share/wordlists/small.txt -x php,html -t 60
```

found a login page

```
/administrator.php    (Status: 200) [Size: 409]
```

now as per THM room, we can use sqlmap for sql injection attacks, so lets try that

Note: sqlmap is not allowed on the OSCP exam, so i will limit my use of this tool for this room only.

SQLmap basically automates all of the exploit.


Stopped here because didnt wanna continue with aoutmated tools