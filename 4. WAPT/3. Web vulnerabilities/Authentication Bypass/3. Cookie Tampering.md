
Examining and editing the cookies set by the web server during your online session can have multiple outcomes, such as unauthenticated access, access to another user's account, or elevated privileges. If you need a refresher on cookies, check out the [HTTP In Detail](https://tryhackme.com/room/httpindetail) room on task 6.

  

**Plain Text**

The contents of some cookies can be in plain text, and it is obvious what they do. Take, for example, if these were the cookie set after a successful login:

**Set-Cookie: logged_in=true; Max-Age=3600; Path=/**  
****Set-Cookie: admin=false; Max-Age=3600; Path=/****

We see one cookie (logged_in), which appears to control whether the user is currently logged in or not, and another (admin), which controls whether the visitor has admin privileges. Using this logic, if we were to change the contents of the cookies and make a request we'll be able to change our privileges.


First, we'll start just by requesting the target page:


           `user@tryhackme$ curl http://10.10.138.116/cookie-test`

We can see we are returned a message of: **Not Logged In**

Now we'll send another request with the logged_in cookie set to true and the admin cookie set to false:


           `user@tryhackme$ curl -H "Cookie: logged_in=true; admin=false" http://10.10.138.116/cookie-test`

We are given the message: **Logged In As A User**

Finally, we'll send one last request setting both the logged_in and admin cookie to true:


         curl -H "Cookie: logged_in=true; admin=true" http://10.10.138.116/cookie-test`


```shell-session
curl -H "Cookie: logged_in=true; admin=true" http://10.10.138.116/cookie-test
```


**Hashing**  

Sometimes cookie values can look like a long string of random characters; these are called hashes which are an irreversible representation of the original text.

Examples are 
1. md5
2. sha256
3. sha512
4. sha1

Even though the hash is irreversible, the same output is produced every time, which is helpful for us as services such as [https://crackstation.net/](https://crackstation.net/) keep databases of billions of hashes and their original strings.


**Encoding**

Encoding is similar to hashing in that it creates what would seem to be a random string of text, but in fact, the encoding is reversible. 

So it begs the question, what is the point in encoding? Encoding allows us to convert binary data into human-readable text that can be easily and safely transmitted over mediums that only support plain text ASCII characters.  
  
Common encoding types are base32 which converts binary data to the characters A-Z and 2-7, and base64 which converts using the characters a-z, A-Z, 0-9,+, / and the equals sign for padding.

  
Take the below data as an example which is set by the web server upon logging in:

****Set-Cookie:** session=eyJpZCI6MSwiYWRtaW4iOmZhbHNlfQ==; Max-Age=3600; Path=/**

This string base64 decoded has the value of **{"id":1,"admin": false}** we can then encode this back to base64 encoded again but instead setting the admin value to true, which now gives us admin access.