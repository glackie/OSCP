While working on a penetration test, it’s important to keep a record of commands that have been entered into the shell. Fortunately, Bash maintains a history of commands that have been entered, which can be displayed with the history command.

	kali@kali:~$ history 
	
	1 cat /etc/lsb-release 2 clear 3 history


Rather than re-typing a long command from our history, we can make use of the history expansion facility. For example, looking back at Listing 34, there are three commands in our history with a line number preceding each one. To re-run the first command, we simply type the ! character followed by the line number, in this case 1, to execute the cat /etc/lsb-release command:

	kali@kali:~$ !1 
	cat /etc/lsb-release
	DISTRIB_ID=Kali DISTRIB_RELEASE=kali-rolling DISTRIB_CODENAME=kali-rolling DISTRIB_DESCRIPTION="Kali GNU/Linux Rolling"

By default, the command history is saved to the .bash_history file in the user home directory. Two environment variables control the history size: HISTSIZE and HISTFILESIZE

HISTSIZE controls the number of commands stored in memory for the current session

HISTFILESIZE configures how many commands are kept in the history file. These variables can be edited according to our needs and saved to the Bash configuration file (.bashrc)