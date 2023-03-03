While CTF machines can have cron jobs running every minute or every 5 minutes, you will more often see tasks that run daily, weekly or monthly in penetration test engagements.


>cat /etc/crontab


![[Pasted image 20220925200953.png]]

You can see the `backup.sh` script was configured to run every minute. The content of the file shows a simple script that creates a backup of the prices.xls file.

![](https://i.imgur.com/qlDj93R.png)

As our current user can access this script, we can easily modify it to create a reverse shell, hopefully with root privileges.  
  
The script will use the tools available on the target system to launch a reverse shell.  
Two points to note;

1.  The command syntax will vary depending on the available tools. (e.g. `nc` will probably not support the `-e` option you may have seen used in other cases)
2.  We should always prefer to start reverse shells, as we not want to compromise the system integrity during a real penetration testing engagement.

The file should look like this;

  

![](https://i.imgur.com/579yg6H.png)  

We will now run a listener on our attacking machine to receive the incoming connection.

  

![](https://i.imgur.com/xwYXfY1.png)