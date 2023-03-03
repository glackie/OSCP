Listening on a TCP/UDP port using Netcat is useful for network debugging of client applications, or otherwise receiving a TCP/UDP network connection. Let’s try implementing a simple chat service involving two machines, using Netcat both as a client and as a server. 

On a Windows machine with IP address 10.11.0.22, we set up Netcat to listen for incoming connections on TCP port 4444. We will use the -n option to disable DNS name resolution, -l to create a listener, -v to add some verbosity, and -p to specify the listening port number:

	C:\Users\offsec> nc -nlvp 4444 listening on [any] 4444 ...

Now that we have bound port 4444 on this Windows machine to Netcat, let’s connect to that port from our Linux machine and enter a line of text:

	kali@kali:~$ nc -nv 10.11.0.22 4444
	
	 (UNKNOWN) [10.11.0.22] 4444 (?) open This chat is from the linux machine

