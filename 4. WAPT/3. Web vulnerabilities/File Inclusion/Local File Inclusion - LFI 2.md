In this task, we go a little bit deeper into LFI. 

We discussed a couple of techniques to bypass the filter within the include function.

**1.** In the first two cases, we checked the code for the web app, and then we knew how to exploit it. 

However, in this case, we are performing black box testing, in which we don't have the source code. 

In this case, errors are significant in understanding how the data is passed and processed into the web app.

In this scenario, we have the following entry point:  
http://webapp.thm/index.php?lang=EN

If we enter an invalid input, such as THM, we get the following error

```php
Warning: include(languages/THM.php): failed to open stream: No such file or directory in /var/www/html/THM-4/index.php on line 12
```


The error message discloses significant information. 

By entering THM as input, an error message shows what the include function looks like: 

include(languages/THM.php);. 

If you look at the directory closely, we can tell the function includes files in the languages directory is adding  .php at the end of the entry. 

Thus the valid input will be something as follows:  

index.php?lang=EN, 

where the file EN is located inside the given languages directory and named  EN.php. 

Also, the error message disclosed another important piece of information about the full web application directory path which is 
	/var/www/html/THM-4/

To exploit this, we need to use the ../ trick, as described in the directory traversal section, to get out the current folder. 

Let's try the following:  

http://webapp.thm/index.php?lang=../../../../etc/passwd

Note that we used 4 ../ because we know the path has four levels /var/www/html/THM-4. 

But we still receive the following error:  

```php
Warning: include(languages/../../../../../etc/passwd.php): failed to open stream: No such file or directory in /var/www/html/THM-4/index.php on line 12
```

It seems we could move out of the PHP directory but still, the include function reads the input with .php at the end! 
This tells us that the developer specifies the file type to pass to the include function. 

>To bypass this scenario, we can use the NULL BYTE, which is %00.  

Using null bytes is an injection technique where URL-encoded representation such as %00 or 0x00 in hex with user-supplied data to terminate strings. 

You could think of it as trying to trick the web app into disregarding whatever comes after the Null Byte.  

By adding the Null Byte at the end of the payload, we tell the  include function to ignore anything after the null byte which may look like:

include("languages/../../../../../etc/passwd%00").".php"); 

which equivalent to →
include("languages/../../../../../etc/passwd");

>NOTE: the %00 trick is fixed and not working with PHP 5.3.4 and above.  

Now apply what we showed in Lab #3, and try to read files /etc/passwd, answer question #1 below.


