got this error when configuring the PHP server

[[4.2 PHP Mysql Authentication]]


- chatgpt gave this as one of the solution 

```
**Missing MySQLi Extension**:

- Make sure that the MySQLi extension is enabled in your PHP configuration. You can check your `php.ini` file to ensure that `mysqli` is not commented out (e.g., `extension=mysqli`).
- If you're running PHP on a web server, check with your hosting provider to ensure that the MySQLi extension is available and enabled.
```



```
  │   ~ ▓▒░ find / -name php.ini -ls 2>/dev/null        ░▒▓ ✔ │ 18:57:56   
   500060     72 -rw-r--r--   1 glack    glack       72556 Aug 21 02:24 /home/glack/Desktop/THM/eramba/docker/php/php.ini
     3712     72 -rw-r--r--   1 root     root        73002 Feb 23  2023 /etc/php/7.4/apache2/php.ini
     3713     72 -rw-r--r--   1 root     root        72600 Feb 23  2023 /etc/php/7.4/cli/php.ini

```

- commented out the required line 
```
. To enable the MySQLi extension, you should remove the semicolon (`;`) at the beginning of the line to uncomment it.
```

`extension=mysqli`

- again got an error 
```
PHP Warning:  PHP Startup: Unable to load dynamic library 'mysqli' (tried: /usr/lib/php/20190902/mysqli (/usr/lib/php/20190902/mysqli: cannot open shared object file: No such file or directory), /usr/lib/php/20190902/mysqli.so (/usr/lib/php/20190902/mysqli.so: cannot open shared object file: No such file or directory)) in Unknown on line 0
```


- used chatgpt and followed the steps

```
   │   ~ ▓▒░ find / -name mysqli.so -ls 2>/dev/null
```

- didn't find this so installed the php-mysql extension

```
sudo apt install php-mysqli
```

- now able to find the extension path
```
find / -name mysqli.so -ls 2>/dev/null                                                              ░▒▓ ✔ │ 27s   │ 19:58:42   
   925889    144 -rw-r--r--   1 root     root       145512 Jun  9 22:21 /usr/lib/php/20190902/mysqli.so
```

- checking for php ini path using
`php --ini`


- again got some errors in the vscode terminal and used chatgpt again

- reinstalled the php-mysql extension and got it working

```
sudo apt-get remove php7.4-mysqli 
sudo apt-get install php7.4-mysqli
```

or

```
sudo apt-get remove php7.4-mysql
sudo apt-get install php7.4-mysql
```

