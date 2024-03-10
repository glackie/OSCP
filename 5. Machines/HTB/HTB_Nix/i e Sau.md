#### Nmap

```
nmap -sV -sT -A 10.10.11.224                                                               ✔  11:14:27   
Starting Nmap 7.94 ( https://nmap.org ) at 2023-12-28 11:18 IST
Nmap scan report for 10.10.11.224
Host is up (0.23s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT      STATE    SERVICE VERSION
22/tcp    open     ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 aa:88:67:d7:13:3d:08:3a:8a:ce:9d:c4:dd:f3:e1:ed (RSA)
|   256 ec:2e:b1:05:87:2a:0c:7d:b1:49:87:64:95:dc:8a:21 (ECDSA)
|_  256 b3:0c:47:fb:a2:f2:12:cc:ce:0b:58:82:0e:50:43:36 (ED25519)
80/tcp    filtered http
55555/tcp open     unknown
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     X-Content-Type-Options: nosniff
|     Date: Thu, 28 Dec 2023 05:50:04 GMT
|     Content-Length: 75
|     invalid basket name; the name does not match pattern: ^[wd-_\.]{1,250}$
|   GenericLines, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 302 Found
|     Content-Type: text/html; charset=utf-8
|     Location: /web
|     Date: Thu, 28 Dec 2023 05:49:33 GMT
|     Content-Length: 27
|     href="/web">Found</a>.
|   HTTPOptions: 
|     HTTP/1.0 200 OK
|     Allow: GET, OPTIONS
|     Date: Thu, 28 Dec 2023 05:49:34 GMT
|_    Content-Length: 0
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :

```

---

#### Port 55555


- Dirsearch gives this
```
[15:12:24] 200 -    7KB - /test
[15:12:25] 502 -   63B  - /test/reports
[15:12:25] 200 -    7KB - /test/
[15:12:25] 502 -   68B  - /test/version_tmp/
[15:12:25] 502 -   60B  - /test/tmp/
[15:12:32] 301 -   39B  - /web/  ->  /web
[15:12:32] 301 -   47B  - /web/bundles/  ->  /web/bundles
[15:12:32] 301 -   50B  - /web/phpMyAdmin/  ->  /web/phpMyAdmin
[15:12:32] 301 -   47B  - /web/uploads/  ->  /web/uploads
```