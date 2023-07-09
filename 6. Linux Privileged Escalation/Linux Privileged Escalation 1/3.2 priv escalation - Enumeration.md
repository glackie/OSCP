### etc/issue

>Systems can also be identified by looking at the `/etc/issue` file. This file usually contains some information about the operating system but can easily be customized or changes. While on the subject, any file containing system information can be customized or changed. For a clearer understanding of the system, it is always good to look at all of these.

### ps Command

The `ps` command is an effective way to see the running processes on a Linux system.

The output of the `ps` (Process Status) will show the following;

-   PID: The process ID (unique to the process)
-   TTY: Terminal type used by the user
-   Time: Amount of CPU time used by the process (this is NOT the time this process has been running for)
-   CMD: The command or executable running (will NOT display any command line parameter)

The “ps” command provides a few useful options.

-   `ps -A`: View all running processes
-   `ps axjf`: View process tree (see the tree formation until `ps axjf` is run below)

`ps aux`: The `aux` option will show processes for all users (a), display the user that launched the process (u), and show processes that are not attached to a terminal (x). Looking at the ps aux command output, we can have a better understanding of the system and potential vulnerabilities.