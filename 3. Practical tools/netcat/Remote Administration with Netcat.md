One of the most useful features of Netcat is its ability to do command redirection. 
The netcattraditional version of Netcat (compiled with the “-DGAPING_SECURITY_HOLE” flag) enables the -e option, which executes a program after making or receiving a successful connection. 

This powerful feature opened up all sorts of interesting possibilities from a security perspective and is therefore not available in most modern Linux/BSD systems.

However, due to the fact that Kali Linux is a penetration testing distribution, the Netcat version included in Kali supports the -e option.
When enabled, this option can redirect the input, output, and error messages of an executable to a TCP/UDP port rather than the default console. 

For example, consider the cmd.exe executable. By redirecting stdin, stdout, and stderr to the network, we can bind cmd.exe to a local port. Anyone connecting to this port will be presented with a command prompt on the target computer. To clarify this, let’s run through a few more scenarios involving Bob and Alice.

(netcat Bind shell scenario)

(netcat reverse shell scenario)

