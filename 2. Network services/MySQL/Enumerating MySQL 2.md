<mark style="background: #BBFABBA6;">Using nmap</mark>

	nmap's mysql-enum script (in nmap)

<mark style="background: #BBFABBA6;">using mysql client</mark>

>log into the sql servers using below command

(root💀kali)-[~]
└─# mysql -h 10.129.184.127 -u root    


>once you have the database console you cna list down datbases and schemas using below commands

>Note: dont forget the     ;       after the command

MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| htb                |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+


https://mariadb.com/kb/en/show-databases/




