## A. Enumeration

### Nmap

```
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)

80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))

```

### HTTP

1. Nothing in the feroxbuster and gobuster output.
2. Robots.txt does not exists
3. At first i missed this comment in the index source code
![[Pasted image 20230725034941.png]]

4. reference to a file named thm.jpg, when trying to access we get this error
![[Pasted image 20230725035032.png]]

5. downloaded the files using
```
─[glack@parrot]─[~/Desktop/THM/madness]
└──╼ $wget http://10.10.99.98/thm.jpg

```
6. this is not a jpg file
```
─[glack@parrot]─[~/Desktop/THM/madness]
└──╼ $file thm.jpg 
thm.jpg: data

```

7. hexedit revealed this is a png format
![[Pasted image 20230725035403.png]]

8. Renamed the file
```
┌─[glack@parrot]─[~/Desktop/THM/madness]
└──╼ $mv thm.jpg thm.png

```

but still not able to view the image
9. The contents of the files were not readable and seemed like data in encrypted format, however two strings were still readable
![[Pasted image 20230725035739.png]]

- just a dead end
10. Used the writeup here, we can change the contents of iamge headers to jpg bytes using hexedit
then, we can view the actual image

![[thm.jpg]]

/th1s_1s_h1dd3n

11. upon viewing the page we have to guess the secret in order to move further. There is a guess in the comment that secret is between 0-99

Things i tried:
burp intruder attacks with
```
GET /th1s_1s_h1dd3n?secret=§0§ 
```

and

```
GET /th1s_1s_h1dd3n/§0§
```

and also some other variation but no luck so going for the writeup here, i just missed the response, i was on the right track

12. secret=73
`Urgh, you got it right! But I won't tell you who I am! y2RPJ4QaPF!B`

13. We can extract the hidden content in thm.jpg
```
─[glack@parrot]─[~/Desktop/THM/madness]
└──╼ $steghide extract -sf thm.jpg 
Enter passphrase: 
Warning: unknown JFIF revision number 17.01
wrote extracted data to "hidden.txt".
```

```
─[glack@parrot]─[~/Desktop/THM/madness]
└──╼ $cat hidden.txt 
Fine you found the password! 

Here's a username 

wbxre

I didn't say I would make it easy for you!

```

hydra bruteforce get's you nowhere

this do not work
```
─[✗]─[glack@parrot]─[~/Desktop/THM/madness]
└──╼ $feroxbuster -u http://10.10.175.208/th1s_1s_h1dd3n/ -n -w /usr/share/wordlists/small.txt -x wbxre,php

```


```
GET /th1s_1s_h1dd3n?secret=wbxre
```
ran this again and checked responses for other requests as well(0-99)

14. got stuck here again and used the writeup

turns out the password was hidden in the main image of the machine URL, really a mad box

```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/madness]
└──╼ $steghide extract -sf madness.jpg 
Enter passphrase: 
wrote extracted data to "password.txt".
```



15. And we log into the machine and get our flag
```
┌─[glack@parrot]─[~/Desktop/THM/madness]
└──╼ $ssh joker@10.10.175.208

*axA&GF8dP
```

```
joker@ubuntu:~$ cat user.txt
THM{d5781e53b130efe2f94f9b0354a5e4ea}
```

## C. Priv Esc

had to use the writeup here again

SUID bit had one odd file named 

`screen`

it was a vulnerable version and we can escalatae our priv using this script

https://www.exploit-db.com/exploits/41154


```
# cat /root/root.txt
THM{5ecd98aa66a6abb670184d7547c8124a}

```