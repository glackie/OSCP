Typically, you will have gained some initial credentials from enumerating other services that you can then use to enumerate and exploit the MySQL service.

1. For manual testing:

	nmap's mysql-enum script (in nmap)



`sudo apt install default-mysql-client`

example enumeration

	root@ip-10-10-211-200:~# mysql -h 10.10.41.252 -u root -p

also try to guess the password as `root`

Now using metasploit:

> auxiliary(admin/mysql/mysql_sql) > options

set USERNAME root

set PASSWORD password


run and we get
[*] 10.10.41.252:3306 -  | 5.7.29-0ubuntu0.18.04.1 |

	version details of sql

now 
>set SQL show databases

we get lsit of databases

[*] 10.10.41.252:3306 - Sending statement: 'show databases'...
[*] 10.10.41.252:3306 -  | information_schema |
[*] 10.10.41.252:3306 -  | mysql |
[*] 10.10.41.252:3306 -  | performance_schema |
[*] 10.10.41.252:3306 -  | sys |
[*] Auxiliary module execution completed








