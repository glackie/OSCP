go to the `/etc/ssh/sshd_config` file and edit it using your favourite text editor (remember to use sudo). 


Find the line that says `#PasswordAuthentication yes` and change it to `PasswordAuthentication no` (remove the # sign and change yes to no). 


Next, find the line that says `Include /etc/ssh/sshd_config.d/*.conf` and change it to `#Include /etc/ssh/sshd_config.d/*.conf` (add a # sign at the beginning). 


Save the file, then enter the command `sudo systemctl restart ssh`