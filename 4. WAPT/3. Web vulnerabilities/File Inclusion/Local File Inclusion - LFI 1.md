
### Local File Inclusion (﻿LFI)

LFI attacks against web applications are often due to a developers' lack of security awareness.

With PHP, using functions such as include, require, include_once, and require_once often contribute to vulnerable web applications. 

In this room, we'll be picking on PHP, but it's worth noting LFI vulnerabilities also occur when using other languages such as ASP, JSP, or even in Node.js apps. 

LFI exploits follow the same concepts as path traversal.  

In this section, we will walk you through various LFI scenarios and how to exploit them.﻿

**1.** Suppose the web application provides two languages, and the user can select between the EN and AR

```php
<?PHP 
	include($_GET["lang"]);
?>
```

The PHP code above uses a GET request via the URL parameter "lang" to include the file of the page. 

The call can be done by sending the following HTTP request as follows: 

http://webapp.thm/index.php?lang=EN.php

to load the English page

or

http://webapp.thm/index.php?lang=AR.php 

to load the Arabic page, 

<mark style="background: #FF5582A6;">where EN.php and AR.php files exist in the same directory.</mark>

<mark style="background: #FF5582A6;">Theoretically, we can access and display any readable file on the server from the code above if there isn't any input validation. </mark>

Let's say we want to read the /etc/passwd file, which contains sensitive information about the users of the Linux operating system, we can try the following:

http://webapp.thm/get.php?file=/etc/passwd 

In this case, it works because there isn't a directory specified in the include function and no input validation.

Now apply what we discussed and try to read /etc/passwd file. 
  

**2.** Next, In the following code, the developer decided to specify the directory inside the function.

```php
<?PHP 
	include("languages/". $_GET['lang']); 
?>
```

In the above code, the developer decided to use the include function to call PHP pages in the languages directory only via lang parameters.  

If there is no input validation, the attacker can manipulate the URL by replacing the lang input with other OS-sensitive files such as /etc/passwd.

Again the payload looks similar to the path traversal, but the include function allows us to include any called files into the current page.

The following will be the exploit:

http://webapp.thm/index.php?lang=../../../../etc/passwd