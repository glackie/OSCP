What is File inclusion?

This room aims to equip you with the essential knowledge to exploit file inclusion vulnerabilities, 

including Local File Inclusion (LFI), Remote File Inclusion (RFI), and directory traversal. 

Also, we will discuss the risk of these vulnerabilities if they're found and the required remediation. 

We provide some practical examples of each vulnerability as well as hands-on challenges.

In some scenarios, web applications are written to request access to files on a given system, including images, static text, and so on via parameters. 

Parameters are query parameter strings attached to the URL that could be used to retrieve data or perform actions based on user input. 

The following graph explains and breaking down the essential parts of the URL.

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5d617515c8cd8348d0b4e68f/room-content/dbf35cc4f35fde7a4327ad8b5a2ae2ec.png)  

For example, parameters are used with Google searching, where GET requests pass user input into the search engine. 

https://www.google.com/search?q=TryHackMe. If you are not familiar with the topic, you can view the [How The Web Works](https://tryhackme.com/module/how-the-web-works) module to understand the concept.  

Let's discuss a scenario where a user requests to access files from a webserver.

First, the user sends an HTTP request to the webserver that includes a file to display. 

For example, if a user wants to access and display their CV within the web application,

the request may look as follows, 

http://webapp.thm/get.php?file=userCV.pdf, where the file is the parameter and the userCV.pdf, is the required file to access.﻿

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5d617515c8cd8348d0b4e68f/room-content/dc22709e572d5de31ed4effb2ebc161f.png)  

Why do File inclusion vulnerabilities happen?﻿

File inclusion vulnerabilities are commonly found and exploited in various programming languages for web applications, such as PHP that are poorly written and implemented. 

The main issue of these vulnerabilities is the input validation, in which the user inputs are not sanitized or validated, and the user controls them.

When the input is not validated, the user can pass any input to the function, causing the vulnerability.


What is the risk of File inclusion?

It depends! If the attacker can use file inclusion vulnerabilities to read sensitive data. 

In that case, the successful attack causes to leak of sensitive data, including code and files related to the web application, credentials for back-end systems. 

Moreover, if the attacker somehow can write to the server such as  /tmp directory, then it is possible to gain remote command execution RCE. 

However, it won't be effective if file inclusion vulnerability is found with no access to sensitive data and no writing ability to the server.