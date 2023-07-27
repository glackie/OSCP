### Nmap

```
─[glack@parrot]─[~/Desktop/THM]
└──╼ $nmap -sV -sT -A 10.10.157.217
Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-28 03:27 IST
Nmap scan report for 10.10.157.217
Host is up (0.14s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=RetroWeb
| Not valid before: 2023-07-26T16:25:18
|_Not valid after:  2024-01-25T16:25:18
| rdp-ntlm-info: 
|   Target_Name: RETROWEB
|   NetBIOS_Domain_Name: RETROWEB
|   NetBIOS_Computer_Name: RETROWEB
|   DNS_Domain_Name: RetroWeb
|   DNS_Computer_Name: RetroWeb
|   Product_Version: 10.0.14393
|_  System_Time: 2023-07-27T16:27:58+00:00
|_ssl-date: 2023-07-27T16:28:05+00:00; -5h30m06s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

### HTTP

1. Windows IIS server 10.0 in use (wappalyzer)
2. Anywhere i click redirects me to `https://www.iis.net/?utm_medium=iis-deployment`
3. feroxbuster with small wordlist reveals

```
200       98l      845w     7447c http://10.10.157.217/retro/README.html
301        2l       10w      161c http://10.10.157.217/retro/wp-content
```

and more
