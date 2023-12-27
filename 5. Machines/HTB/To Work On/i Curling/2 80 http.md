medium worldlist ferox buster find this

some useful directories


──(root💀kali)-[~]
└─# feroxbuster -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://10.10.10.150 -f -n 



Found other login portal at:

200      GET      109l      348w     5110c http://10.10.10.150/administrator/



remaining directories are blank


200      GET        0l        0w        0c http://10.10.10.150/configuration.php



got another useful directory with extension php but its blank




on the main website, this is written at the bottom


© 2023 Cewl Curling site! -

this is maybe a custom one because didnt find anything on web


also found a usernmae on the index.php


- Floris



Base on the the data checked out the cewl tool here

https://www.kali.org/tools/cewl/


maybe we have to create a wordlist




