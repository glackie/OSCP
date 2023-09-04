
Here while solving [[Chill Hack]] i came acros this output of `sudo -l`


```
anurodh@ubuntu:/home/apaar$ sudo -l
Matching Defaults entries for anurodh on ubuntu:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User anurodh may run the following commands on ubuntu:
    (apaar : ALL) NOPASSWD: /home/apaar/.helpline.sh
```


Now we are able to run `.helpline.sh` with `different user` privleges and as a different user named `apaar`


we use the command

```
anurodh@ubuntu:/home/apaar$ sudo -u apaar ./.helpline.sh 
```


