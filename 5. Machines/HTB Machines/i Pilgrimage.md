## Nmap

Ports 22 and 80 are open


## Web directories

```index.php            (Status: 200) [Size: 7621]
/login.php            (Status: 200) [Size: 6166]
/register.php         (Status: 200) [Size: 6173]
/assets               (Status: 301) [Size: 169] [--> http://pilgrimage.htb/assets/]
/logout.php           (Status: 302) [Size: 0] [--> /]                              
/vendor               (Status: 301) [Size: 169] [--> http://pilgrimage.htb/vendor/]
/dashboard.php        (Status: 302) [Size: 0] [--> /login.php]                     
/tmp                  (Status: 301) [Size: 169] [--> http://pilgrimage.htb/tmp/]   

```


Whatever image to inject into the system it gives the output of 

[http://pilgrimage.htb/shrunk/6498372050753.jpeg](http://pilgrimage.htb/shrunk/6498372050753.jpeg)


I learned about magic bytes and change the bytes of php file to jpeg file using this resource

https://en.wikipedia.org/wiki/List_of_file_signatures


I successfully uploaded the file on the machine, however i was not able to get a reverse shell and got stuck here with no witeup so will continue this later