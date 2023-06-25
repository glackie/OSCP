`cURL` is a tool that allows us to transfer files from the shell over protocols like `HTTP`, `HTTPS`, `FTP`, `SFTP`, `FTPS`, or `SCP`. 

This tool gives us the possibility to control and test websites remotely. Besides the remote servers' content, we can also view individual requests to look at the client's and server's communication. 

```shell-session
glack@htb[/htb]$ curl http://localhost

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
  <!--
    Modified from the Debian original for Ubuntu
    Last updated: 2016-11-16
    See: https://launchpad.net/bugs/1288690
  -->
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Apache2 Ubuntu Default Page: It works</title>
    <style type="text/css" media="screen">
...SNIP...
```


We can get hash of an image 

example:

	curl https://static-labs.tryhackme.cloud/sites/favicon/images/favicon.ico | md5sum


here we get the imge using curl and pipe the output to get its "md5" hash


>-v flag will show the requset heders and response headers on the command output

In the previous section, we saw how using the `-v` flag with cURL shows us the full details of the HTTP request and response.

If we were only interested in seeing the response headers, then we can use the `-I` flag to send a `HEAD` request and only display the ==**response headers**==

Furthermore, we can use the `-i` flag to display both the headers and the response body 

The difference between the two is that `-I` sends a `HEAD` request (as will see in the next section), while `-i` sends any request we specify and prints the headers as well.

In addition to viewing headers, cURL also allows us to set request headers with the `-H` flag, as we will see in a later section. 

Some headers, like the `User-Agent` or `Cookie` headers, has their own flags. For example, we can use the `-A` to set our `User-Agent`, as follows:

```shell-session
curl https://www.inlanefreight.com -A 'Mozilla/5.0'
```



