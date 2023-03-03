Crontab is always worth checking as it can sometimes lead to easy privilege escalation vectors. The following scenario is not uncommon in companies that do not have a certain cyber security maturity level:

1.  System administrators need to run a script at regular intervals.
2.  They create a cron job to do this
3.  After a while, the script becomes useless, and they delete it  
    
4.  They do not clean the relevant cron job

This change management issue leads to a potential exploit leveraging cron jobs.


  

![](https://i.imgur.com/SovymJL.png)

  

  

The example above shows a similar situation where the antivirus.sh script was deleted, but the cron job still exists.  
If the full path of the script is not defined (as it was done for the backup.sh script), cron will refer to the paths listed under the PATH variable in the /etc/crontab file. In this case, we should be able to create a script named “antivirus.sh” under our user’s home folder and it should be run by the cron job.  

  

The file on the target system should look familiar:  

![](https://i.imgur.com/SHknR87.png)

>In the odd event you find an existing script or task attached to a cron job, it is always worth spending time to understand the function of the script and how any tool is used within the context. For example, tar, 7z, rsync, etc., can be exploited using their wildcard feature.


