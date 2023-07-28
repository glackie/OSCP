2. In this section, the developer decided to filter keywords to avoid disclosing sensitive information! 

The /etc/passwd file is being filtered. 
There are two possible methods to bypass the filter. 

First, by using the NullByte %00 
or 
the current directory trick at the end of the filtered keyword /.. 

The exploit will be similar to 
http://webapp.thm/index.php?lang=/etc/passwd/. 

We could also use 

http://webapp.thm/index.php?lang=/etc/passwd%00.

To make it clearer, if we try this concept in the file system using cd .., it will get you back one step; however, if you do cd ., It stays in the current directory.  

Similarly, if we try  /etc/passwd/.., it results to be  /etc/ and that's because we moved one to the root.  

Now if we try  /etc/passwd/., 
the result will be  /etc/passwd since dot refers to the current directory.

Now apply this technique in Lab #4 and figure out to read /etc/passwd.

**3.** Next, in the following scenarios, the developer starts to use input validation by filtering some keywords. 

Let's test out and check the error message!  

http://webapp.thm/index.php?lang=../../../../etc/passwd  

We got the following error!  

```php
Warning: include(languages/etc/passwd): failed to open stream: No such file or directory in /var/www/html/THM-5/index.php on line 15
```

  
If we check the warning message in the include(languages/etc/passwd) section, 

we know that the web application replaces the ../ with the empty string. 

There are a couple of techniques we can use to bypass this.

First, we can send the following payload to bypass it: ....//....//....//....//....//etc/passwd

Why did this work?

This works because the PHP filter only matches and replaces the first subset string ../ it finds and doesn't do another pass, leaving what is pictured below.  

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5d617515c8cd8348d0b4e68f/room-content/30d3bf0341ba99485c5f683a416a056d.png)  

Try out Lab #5 and try to read /etc/passwd and bypass the filter!


**4.** Finally, we'll discuss the case where the developer forces the include to read from a defined directory!

For example, if the web application asks to supply input that has to include a directory such as: http://webapp.thm/index.php?lang=languages/EN.php 


then, to exploit this, we need to include the directory in the payload like so: ?lang=languages/../../../../../etc/passwd.