### Nmap

```
┌─[glack@parrot]─[~]
└──╼ $nmap -sV -sT -A 10.10.78.129
Starting Nmap 7.93 ( https://nmap.org ) at 2023-09-05 02:48 IST
Nmap scan report for 10.10.78.129
Host is up (0.13s latency).
Not shown: 988 closed tcp ports (conn-refused)
PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  ssl/ms-wbt-server?
|_ssl-date: 2023-09-04T15:49:40+00:00; -5h30m00s from scanner time.
| ssl-cert: Subject: commonName=Dark-PC
| Not valid before: 2023-09-03T15:47:44
|_Not valid after:  2024-03-04T15:47:44
5357/tcp  open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Service Unavailable
8000/tcp  open  http               Icecast streaming media server
|_http-title: Site doesn't have a title (text/html).
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49158/tcp open  msrpc              Microsoft Windows RPC
49159/tcp open  msrpc              Microsoft Windows RPC
49160/tcp open  msrpc              Microsoft Windows RPC
Service Info: Host: DARK-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: Dark-PC
|   NetBIOS computer name: DARK-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2023-09-04T10:49:34-05:00
|_clock-skew: mean: -4h14m59s, deviation: 2h30m00s, median: -5h30m00s
|_nbstat: NetBIOS name: DARK-PC, NetBIOS user: <unknown>, NetBIOS MAC: 025f2cb80b37 (unknown)
| smb2-time: 
|   date: 2023-09-04T15:49:33
|_  start_date: 2023-09-04T15:47:43
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   210: 
|_    Message signing enabled but not required
```


