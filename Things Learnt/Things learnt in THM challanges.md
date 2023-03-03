1. use burp, browser, and also also to manipulate the request

2. try all options 
3. remember to check the source code to get the input paramater in form
4. try other http methods as well
5. dont forget to check the cookie
6. try directory traversal attack in cookie value after authentication
7. when the input is filtered try other http methods 

8. In some cases when i was trying with curl i got the flag but when i tried to do the same with burp i was not getting the flag
then i finally got the answer to it

eg 

curl http://10.10.87.97/challenges/chall3.php -X POST -d 'method=POST&file=../../../../etc/flag3%00' --trace-ascii - 

Above commands was giving me the flag but when i tried to do the same with burp

by only changing the method to POST and changing **file** parameter value to "../../../../etc/flag3%00"

after checking how the curl was sending data i noticed that curl was sending two parameters as body parameter and not query parameters

i tried to do the same in burp. I also added a new header in burp i.e.

>Content-Type: application/x-www-form-urlencoded

adding above header is the key 

and boom i got The flag with both curl and burpsuite

 I tried the same with browser developer tools and boom - got the flag


