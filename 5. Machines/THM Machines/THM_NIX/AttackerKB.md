## Nmap

```
[parrot]─[23:08-29/06]─[/home/glack]
└╼glack$nmap -sV -sT -A  10.10.165.126 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-29 23:08 IST
Nmap scan report for 10.10.165.126
Host is up (0.48s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b74cd0bde27b1b15722764562915ea23 (RSA)
|   256 b78523114f44fa22008e40775ecf287c (ECDSA)
|_  256 a9fe4b82bf893459365becdac2d395ce (ED25519)
10000/tcp open  http    MiniServ 1.890 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```


**HTTP Certificate:**

After viewing the certificate i found a hostname of 

```
root@source
```



**Attacker KB website**

Found out about vulnerability details other than standard websites like exploitdb

https://attackerkb.com/


So most of the other questions for this machine on THM are theory base and enumeration based, and at the end was only able to find a metasploit exploit for it

CVE number for this exploit is 

```
[CVE-2019-15107](https://attackerkb.com/search?q=CVE-2019-15107)
```


So i used metasploit
set the options and got the root shell, it was pretty easy 
