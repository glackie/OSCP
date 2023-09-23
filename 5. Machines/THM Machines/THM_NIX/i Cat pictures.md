
### Nmap

- took few attempts for
```
   │   ~/Desktop/THM/cat-pictures ▓▒░ nmap -p- -T5 --min-rate=10000 10.10.34.173

PORT     STATE SERVICE
22/tcp   open  ssh
8080/tcp open  http-proxy
```


it gives out his error

`Note: Host seems down. If it is really up, but blocking our ping probes, try -Pn`


it takes some time for machine to run properly

- only two ports are open

regenerated the onvp config and still not able to ping the machine


- Had to use the attackbox now they are reachable

- restarted the machine and good to go

### HTTP

- ran a feroxbuster command and got this

```
301        7l       20w      241c http://10.10.31.35:8080/download
200      319l      854w        0c http://10.10.31.35:8080/index.php
301        7l       20w      237c http://10.10.31.35:8080/docs
403        7l       20w      199c http://10.10.31.35:8080/files
200      305l      900w        0c http://10.10.31.35:8080/search.php
403        7l       20w      199c http://10.10.31.35:8080/common.php
301       12l       22w      462c http://10.10.31.35:8080/faq.php
403        7l       20w      199c http://10.10.31.35:8080/store
```

 again got some connectivity issue, changed my VPN server to US

 still getting connectivity errors

- on viewing
http://10.10.31.35:8080/download - this is blank
http://10.10.1.64:8080/files - this is forbidden

stopped here due to connectivity issues