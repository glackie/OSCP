## Apache

![Apache](https://academy.hackthebox.com/storage/modules/75/apache.png)

[Apache](https://www.apache.org/) 'or `httpd`' is the most common web server on the internet, hosting more than `40%` of all internet websites. `Apache` usually comes pre-installed in most `Linux` distributions and can also be installed on Windows and macOS servers.

`Apache` is usually used with `PHP` for web application development, but it also supports other languages like `.Net`, `Python`, `Perl`, and even OS languages like `Bash` through `CGI`. Users can install a wide variety of `Apache` modules to extend its functionality and support more languages. For example, to support serving `PHP` files, users must install `PHP` on the back end server, in addition to installing the `mod_php` module for `Apache`.

`Apache` is an open-source project, and community users can access its source code to fix issues and look for vulnerabilities. It is well-maintained and regularly patched against vulnerabilities to keep it safe against exploitation. Furthermore, it is very well documented, making using and configuring different parts of the webserver relatively easy. `Apache` is commonly used by startups and smaller companies, as it is straightforward to develop for. Still, some big companies utilize Apache, including:

`Apple`

`Adobe`

`Baidu`

---

## NGINX

![NGINX](https://academy.hackthebox.com/storage/modules/75/nginx.png)

[NGINX](https://www.nginx.com/) is the second most common web server on the internet, hosting roughly `30%` of all internet websites. `NGINX` focuses on serving many concurrent web requests with relatively low memory and CPU load by utilizing an async architecture to do so. This makes `NGINX` a very reliable web server for popular web applications and top businesses worldwide, which is why it is the most popular web server among high traffic websites, with around 60% of the top 100,000 websites using `NGINX`.

`NGINX` is also free and open-source, which gives all the same benefits previously mentioned, like security and reliability. Some popular websites that utilize `NGINX` include:

`Google`

`Facebook`

`Twitter`

`Cisco`

`Intel`

`Netflix`

`HackTheBox`

---

## IIS

![iis](https://academy.hackthebox.com/storage/modules/75/iis.png)

[IIS (Internet Information Services)](https://en.wikipedia.org/wiki/Internet_Information_Services) is the third most common web server on the internet, hosting around `15%` of all internet web sites. `IIS` is developed and maintained by Microsoft and mainly runs on Microsoft Windows Servers. `IIS` is usually used to host web applications developed for the Microsoft .NET framework, but can also be used to host web applications developed in other languages like `PHP`, or host other types of services like `FTP`. Furthermore, `IIS` is very well optimized for Active Directory integration and includes features like `Windows Auth` for authenticating users using Active Directory, allowing them to automatically sign in to web applications.

Though not the most popular web server, many big organizations use `IIS` as their web server. Many of them use Windows Server on their back end or rely heavily on Active Directory within their organization. Some popular websites that utilize IIS include:

`Microsoft`

`Office365`

`Skype`

`Stack Overflow`

`Dell`

<mark style="background: #BBFABBA6;">Aside from these 3 web servers, there are many other commonly used web servers, like [Apache Tomcat]for `Java` web applications, and [Node.JS] for web applications developed using `JavaScript` on the back end.</mark>