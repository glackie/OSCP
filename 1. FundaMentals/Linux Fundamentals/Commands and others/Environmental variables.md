When opening a terminal window, a new Bash process, which has its own environment variables, is initialized. 

These variables are a form of global storage for various settings inherited by any applications that are run during that terminal session. 

One of the most commonly-referenced environment variables is PATH, which is a colon-separated list of directory paths that Bash will search through whenever a command is run without a full path.

We can view the contents of a given environment variable with the echo command followed by the “$” character and an environment variable name. For example, let’s take a look at the contents of the PATH environment variable

	kali@kali:~$ echo $PATH 
	/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin


Some other useful environment variables include USER, PWD, and HOME, which hold the values of the current terminal user’s username, present working directory, and home directory respectively:

<mark style="background: #FF5582A6;">An environment variable can be defined with the export command. </mark>

For example, if we are scanning a target and don’t want to type in the system’s IP address repeatedly, we can quickly assign it an environment variable and use that instead:

Example: 

	kali@kali:~$ export b=10.11.1.220
	
	kali@kali:~$ ping -c 2 $b 
	
	PING 10.11.1.220 (10.11.1.220) 56(84) bytes of data. 64 bytes from 10.11.1.220: icmp_seq=1 ttl=62 time=2.23 ms 64 bytes from 10.11.1.220: icmp_seq=2 ttl=62 time=1.56 ms --- 10.11.1.220 ping statistics --- 2 packets transmitted, 2 received, 0% packet loss, time 1002ms rtt min/avg/max/mdev = 1.563/1.900/2.238/0.340 ms


There are many other environment variables defined by default in Kali Linux. We can view these by running env at the command line:

	kali@kali:~$ env 
	
	SHELL=/bin/bash 
	PWD=/home/kali XDG_SESSION_DESKTOP=lightdm-xsession LOGNAME=kali XDG_SESSION_TYPE=x11 XAUTHORITY=/home/kali/.Xauthority XDG_GREETER_DATA_DIR=/var/lib/lightdm/data/kali HOME=/home/kali ... TERM=xterm-256color USER=kali ...