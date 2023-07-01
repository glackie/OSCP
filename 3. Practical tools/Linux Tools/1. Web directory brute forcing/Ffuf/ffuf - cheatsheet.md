cheatsheet basically means what the are the flags i have used till now and will be using 


>-w - wordlist flag - mention wordlist path. after path type colon and set fuzzing keyword

 -w /usr/share/wordlist.txt:FUZZ

>-u - mention url along with fuzzing keyword(this will specify where the words in wordlsit will be used)

 -u http://{IP}:{port}/FUZZ
>-t - this options defined how many request we will make when attaking. however we should be carefull while defining this because we do not want to dos the server

-t 1000

>We can always use two wordlists and have a unique keyword for each, and then do `FUZZ_1.FUZZ_2` to fuzz for both.

>In `ffuf`, we can enable recursive scanning with the `-recursion` flag, and we can specify the depth with the `-recursion-depth` flag. 

>If we specify `-recursion-depth 1`, it will only fuzz the main directories and their direct sub-directories. If any sub-sub-directories are identified (like `/login/user`, it will not fuzz them for pages). When using recursion in `ffuf`, we can specify our extension with `-e .php`


>we will also add the flag `-v` to output the full URLs. Otherwise, it may be difficult to tell which `.php` file lies under which directory.


>if we use the `sub-domain fuzzing`, we would only be able to identify public sub-domains but will not identify any sub-domains that are not public.

>we can use the `-H` flag to specify a header and will use the `FUZZ` keyword within it, as follows:

ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'

>Let us first start with fuzzing for `GET` requests, which are usually passed right after the URL, with a `?` symbol, like:

   `http://admin.academy.htb:PORT/admin/admin.php?param1=key`.

>The main difference between `POST` requests and `GET` requests is that `POST` requests are not passed with the URL and cannot simply be appended after a `?` symbol. 


>  -d                  POST data

we use this flag to send POST requests to server using ffuf

-d 'FUZZ=key'

>  -X                  HTTP method to use

we can use this flag to use any HTTP method we want to use































