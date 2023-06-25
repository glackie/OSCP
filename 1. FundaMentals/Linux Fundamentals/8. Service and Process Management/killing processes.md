A process can be in the following states:

-   Running
-   Waiting (waiting for an event or system resource)
-   Stopped
-   Zombie (stopped but still has an entry in the process table).

Processes can be controlled using `kill`, `pkill`, `pgrep`, and `killall`. To interact with a process, we must send a signal to it. We can view all signals with the following command:


For example, if a program were to freeze, we could force to kill it with the following command:

```shell-session
glack@htb[/htb]$ kill 9 <PID> 
```

