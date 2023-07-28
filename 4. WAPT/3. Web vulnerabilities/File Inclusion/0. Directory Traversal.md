Path Traversal  

Also known as Directory traversal, a web security vulnerability allows an attacker to read operating system resources, such as local files on the server running an application.

The attacker exploits this vulnerability by manipulating and abusing the web application's URL to locate and access files or directories stored outside the application's root directory.

Path traversal vulnerabilities occur when the user's input is passed to a function such as file_get_contents in PHP. 
It's important to note that the function is not the main contributor to the vulnerability. 

Often poor input validation or filtering is the cause of the vulnerability. 

In PHP, you can use the file_get_contents to read the content of a file. You can find more information about the function [here](https://www.php.net/manual/en/function.file-get-contents.php).

The following graph shows how a web application stores files in /var/www/app. 
The happy path would be the user requesting the contents of userCV.pdf from a defined path /var/www/app/CVs.  

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5d617515c8cd8348d0b4e68f/room-content/45d9c1baacda290c1f95858e27f740c9.png)We can test out the URL parameter by adding payloads to see how the web application behaves. 
Path traversal attacks, also known as the dot-dot-slash attack, take advantage of moving the directory one step up using the double dots ../. 

If the attacker finds the entry point, which in this case  is get.php?file=, 

then the attacker may send something as follows, http://webapp.thm/get.php?file=../../../../etc/passwd  

Suppose there isn't input validation, and instead of accessing the PDF files at /var/www/app/CVs location, the web application retrieves files from other directories, which in this case /etc/passwd.

Each .. entry moves one directory until it reaches the root directory /.
Then it changes the directory to /etc, and from there, it read the passwd file.  

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5d617515c8cd8348d0b4e68f/room-content/3037513935e3242f74bd0fe97833b5ac.png)

As a result, the web application sends back the file's content to the user.

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5d617515c8cd8348d0b4e68f/room-content/c12d34456ebe25bafffeb829c58f98c0.png)  

Similarly, if the web application runs on a Windows server, the attacker needs to provide Windows paths. 

For example, if the attacker wants to read the boot.ini file located in c:\boot.ini, then the attacker can try the following depending on the target OS version:

http://webapp.thm/get.php?file=../../../../boot.ini 

or

http://webapp.thm/get.php?file=../../../../windows/win.ini  

The same concept applies here as with Linux operating systems, where we climb up directories until it reaches the root directory, which is usually c:\.  

Sometimes, developers will add filters to limit access to only certain files or directories. 

Below are some common OS files you could use when testing.

**Location** - **Description**  

/etc/issue - contains a message or system identification to be printed before the login prompt.  

/etc/profile - controls system-wide default variables, such as Export variables, File creation mask (umask), Terminal types, Mail messages to indicate when new mail has arrived

/proc/version - specifies the version of the Linux kernel  

/etc/passwd - has all registered user that has access to a system  

/etc/shadow - contains information about the system's users' passwords  

/root/.bash_history - contains the history commands for root user  

/var/log/dmessage - contains global system messages, including the messages that are logged during system startup  

/var/mail/root - all emails for root user

/root/.ssh/id_rsa - Private SSH keys for a root or any known valid user on the server

/var/log/apache2/access.log - the accessed requests for Apache  webserver

C:\boot.ini - contains the boot options for computers with BIOS firmware

