An alternative to curl is the tool `wget`. 

With this tool, we can download files from FTP or HTTP servers directly from the terminal and serves as a good download manager.

If we use wget in the same way, the difference to curl is that the website content is downloaded and stored locally, as shown in the following example.

```shell-session
glack@htb[/htb]$ wget http://localhost

--2020-05-15 17:43:52--  http://localhost/
Resolving localhost (localhost)... 127.0.0.1
Connecting to localhost (localhost)|127.0.0.1|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 10918 (11K) [text/html]
Saving to: 'index.html'

index.html                 100%[=======================================>]  10,66K  --.-KB/s    in 0s      

2020-05-15 17:43:52 (33,0 MB/s) - ‘index.html’ saved [10918/10918]
```


