## Nmap

```
─[glack@parrot]─[~/Desktop/THM]
└──╼ $nmap -sV -sT -A 10.10.222.224
Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-02 19:21 IST
Nmap scan report for 10.10.222.224
Host is up (0.13s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 23.09 seconds
```

## HTTP

```
─[glack@parrot]─[~/Desktop/THM]
└──╼ $feroxbuster -u http://10.10.222.224/ -w /usr/share/wordlists/small.txt -n -x html,php
```

found login at

```
401       14l       54w      460c http://10.10.195.171/webdav
```


Used write up and found 


A basic search for `webdav default credentials` will lead us to [this page](http://xforeveryman.blogspot.com/2012/01/helper-webdav-xampp-173-default.html) and give us the default credentials: `wampp:xampp`.


```
wampp:$apr1$Wm2VTkFL$PVNRQv7kzqXQIHe14qKA91
```


https://hashcat.net/wiki/doku.php?id=example_hashes

From here i found out this is a 

|   |   |
|---|---|
|1600|Apache $apr1$ MD5, md5apr1, MD5 (APR) 2|

