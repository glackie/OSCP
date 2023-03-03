**Method**

We're going to be exploiting an anonymous FTP login, to see what files we can access- and if they contain any information that might allow us to pop a shell on the system.

This is a common pathway in CTF challenges, and mimics a real-life careless implementation of FTP servers.

	Make sure ftp client is isntalled on server

we can check to see if we are able to login anonymously to the FTP server. 

We can do this using by typing 

"_ftp [IP]_" into the console, and 
entering "anonymous", and no password when prompted.

	Use help to get a list of commands that we can type in ftp console

<mark style="background: #BBFABBA6;">Commonly used commands are</mark>

	NOTE: remember the commands and file name are case sensitive, so remember to re check if getting error

> get

>ls





