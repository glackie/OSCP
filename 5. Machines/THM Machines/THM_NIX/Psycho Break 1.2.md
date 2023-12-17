## Nmap

```
nmap -sV -sT -A 10.10.133.65                                                                                              ✔  4s    21:10:46   
Starting Nmap 7.93 ( https://nmap.org ) at 2023-12-03 21:17 IST
Nmap scan report for 10.10.133.65
Host is up (0.13s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     ProFTPD 1.3.5a
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 442ffb3bf395c3c6df31d6e09e999242 (RSA)
|   256 922436917adb62d2b9bb43eb589b5014 (ECDSA)
|_  256 3404df1354218d377ff80a65934775d0 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Welcome To Becon Mental Hospital
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```


### HTTP

- source of html reveals  a comment
```
<!-- Sebastian sees a path through the darkness which leads to a room => /sadistRoom -->
```