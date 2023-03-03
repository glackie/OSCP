The favicon is a small icon displayed in the browser's address bar or tab used for branding a website.

![[Pasted image 20220921170628.png]]


Sometimes when frameworks are used to build a website, a favicon that is part of the installation gets leftover, and if the website developer doesn't replace this with a custom one, this can give us a clue on what framework is in use. 

OWASP host a database of common framework icons that you can use to check against the targets favicon [https://wiki.owasp.org/index.php/OWASP_favicon_database](https://wiki.owasp.org/index.php/OWASP_favicon_database). Once we know the framework stack, we can use external resources to discover more about it



Example:

```shell-session
user@machine$ curl https://static-labs.tryhackme.cloud/sites/favicon/images/favicon.ico | md5sum
```

If you run the following command on the AttackBox, it will download the favicon and get its md5 hash value which you can then lookup on the  
[https://wiki.owasp.org/index.php/OWASP_favicon_database](https://wiki.owasp.org/index.php/OWASP_favicon_database).


