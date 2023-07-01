As suggested by the description, Netcat can run in either client or server mode. To begin, let’s look 
at the client mode.

We can use client mode to connect to any TCP/UDP port, allowing us to:
• Check if a port is open or closed.
• Read a banner from the service listening on a port.
• Connect to a network service manually.

Let’s begin by using Netcat (nc) to check if TCP port 110 (the POP3 mail service) is open on one 
of the lab machines. We will supply several arguments: the -n option to skip DNS name 
resolution; -v to add some verbosity; the destination IP address; and the destination port number:

	kali@kali:~$ nc -nv 10.11.0.22 110 
	
	(UNKNOWN) [10.11.0.22] 110 (pop3) open +OK POP3 server lab ready <00003.1277944@lab>

Listing 77 tells us several things. First, the TCP connection to 10.11.0.22 on port 110 (10.11.0.22:110 in standard nomenclature) succeeded, so Netcat reports the remote port asopen. Next, the server responded to our connection by “talking back to us”, printed out the server welcome message, and prompted us to log in, which is standard behavior for POP3 services.

