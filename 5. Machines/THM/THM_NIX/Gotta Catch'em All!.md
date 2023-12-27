## Nmap

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 581475691ea9595fb23a691c6c785c27 (RSA)
|   256 23f5fbe757c2a53ec226290e74db37c2 (ECDSA)
|_  256 f19bb58ab929aab6aaa2524a6e6595c5 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Can You Find Them All?
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## HTTP

1. found these creds on the main page (Used write  up)

`    <pokemon>:<hack_the_pokemon>`

## shell

1. ssh in the machine 
```
─[glack@parrot]─[~/Desktop/THM/pokemon]
└──╼ $ssh pokemon@10.10.6.220
```

2. Found the first flag at 

```
pokemon@root:~/Desktop/P0kEmOn$ cat grass-type.txt 
50 6f 4b 65 4d 6f 4e 7b 42 75 6c 62 61 73 61 75 72 7d
```

3. Found another hint at 

```
pokemon@root:~/Videos/Gotta/Catch/Them/ALL!$ cat Could_this_be_what_Im_looking_for\?.cplusplus 
# include <iostream>

int main() {
	std::cout << "ash : pikapika"
	return 0;

```

4. Swtiched to ash and found this text file with content

```
$ cat roots-pokemon.txt
Pikachu!$
```

5. When switching to ask we are prompted a sudo command usage

```
pokemon@root:~/Videos/Gotta/Catch/Them/ALL!$ su ash
Password: 
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

```

6. We are able to run all commands as sudo

```
$ sudo -l
[sudo] password for ash: 
Matching Defaults entries for ash on root:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User ash may run the following commands on root:
    (ALL : ALL) ALL
$ sudo su
root@root:/home# id

```

7. found a flag at
```

root@root:/var/www/html# find / -name *fire*type* -ls 2>/dev/null

root@root:/var/www/html# cat /etc/why_am_i_here\?/fire-type.txt 
UDBrM20wbntDaGFybWFuZGVyfQ==
```

8. Also flag at

```
root@root:/var/www/html# ls
index.html  water-type.txt
```

Ceaser cipher - 12 shift

`Squirtle_SqUaD{Squirtle}`