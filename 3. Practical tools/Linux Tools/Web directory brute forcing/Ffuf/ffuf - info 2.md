## Fuzzing

The term `fuzzing` refers to a testing technique that sends various types of user input to a certain interface to study how it would react. 

If we were fuzzing for SQL injection vulnerabilities, we would be sending random special characters and seeing how the server would react. 

If we were fuzzing for a buffer overflow, we would be sending long strings and incrementing their length to see if and when the binary would break.

We usually utilize pre-defined wordlists of commonly used terms for each type of test for web fuzzing to see if the webserver would accept them. 

This is done because web servers do not usually provide a directory of all available links and domains (unless terribly configured), and so we would have to check for various links and see which ones return pages. 

For example, if we visit [https://www.hackthebox.eu/doesnotexist](https://www.hackthebox.eu/doesnotexist), we would get an HTTP code `404 Page Not Found`, and see the below page:

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_HTB_404.jpg)

However, if we visit a page that exists, like `/login`, we would get the login page and get an HTTP code `200 Found`, and see the below page:

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_HTB_login.jpg)

This is the basic idea behind web fuzzing for pages and directories. 

Still, we cannot do this manually, as it will take forever. This is why we have tools that do this automatically, efficiently, and very quickly. S

uch tools send hundreds of requests every second, study the response HTTP code, and determine whether the page exists or not. 

Thus, we  can quickly determine what pages exist and then manually examine them to see their content.