
### Nmap

```
map -sV -sT -A 10.10.103.127                                                           ░▒▓ ✔ │ 42s   │ 02:59:11   
Starting Nmap 7.93 ( https://nmap.org ) at 2023-09-16 02:59 IST
Nmap scan report for 10.10.103.127
Host is up (0.13s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION
8080/tcp open  http    Node.js Express framework
|_http-title: VulnNet &ndash; Your reliable news source &ndash; Try Now!

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 51.69 seconds
```


### HTTP

- ran a feroxbuster on `http://10.10.103.127:8080`

found nothing but a login page


- on googling node js express exploits i came across this 
https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/nodejs-express



