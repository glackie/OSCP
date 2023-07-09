### netstat

The `netstat` command can be used with several different options to gather information on existing connections.

-   `netstat -a`: shows all listening ports and established connections.
-   `netstat -at` or `netstat -au` can also be used to list TCP or UDP protocols respectively.
-   `netstat -l`: list ports in “listening” mode. These ports are open and ready to accept incoming connections.  
- netstat -lt - This can be used with the “t” option to list only ports that are listening using the TCP protocol

`netstat -s`: list network usage statistics by protocol (below) This can also be used with the `-t` or `-u` options to limit the output to a specific protocol.

`netstat -tp`: list connections with the service name and PID information.

	Note: if PID/program name is bland the it means process is run by other user and to view the PID we need to run the commands using root privileges

`netstat -i`: Shows interface statistics.


`netstat -ano` which could be broken down as follows;

-   `-a`: Display all sockets
-   `-n`: Do not resolve names
-   `-o`: Display timers


