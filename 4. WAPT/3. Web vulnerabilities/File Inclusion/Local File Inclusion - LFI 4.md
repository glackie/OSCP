
**4.** Finally, we'll discuss the case where the developer forces the include to read from a defined directory! 

For example, if the web application asks to supply input that has to include a directory such as: 

http://webapp.thm/index.php?lang=languages/EN.php 

then, to exploit this, we need to include the directory in the payload like so: 

?lang=languages/../../../../../etc/passwd.