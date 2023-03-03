Cron jobs are used to run scripts or binaries at specific times.

By default, they run with the privilege of their owners and not the current user.

While properly configured cron jobs are not inherently vulnerable, they can provide a privilege escalation vector under some conditions.


	The idea is quite simple; if there is a scheduled task that runs with root privileges and we can change the script that will be run, then our script will run with root privileges.


Cron job configurations are stored as crontabs (cron tables) to see the next time and date the task will run.  
  
Each user on the system have their crontab file and can run specific tasks whether they are logged in or not. As you can expect, our goal will be to find a cron job set by root and have it run our script, ideally a shell.  
  
Any user can read the file keeping system-wide cron jobs under `/etc/crontab`

