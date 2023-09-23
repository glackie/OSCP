
According to [Wikipedia](https://en.wikipedia.org/wiki/SSH_(Secure_Shell)) SSH or Secure Shell is a cryptographic network protocol for operating network services securely over an unsecured network. Typical applications include remote command-line, login, and remote command execution, but any network service can be secured with SSH.

Some important flags that will be used in this task are below.

  

|   |   |
|---|---|
|Flag|Description|
|-i|If you want to access a remote server using a private key.|
|-L|For local port forwarding. Followed by  <br><br>local_port:remote_address:remote_port|
|-R|For remote port forwarding. Followed by  <br><br>port:local_address:local_port|
|-D|For Dynamic port forwarding. Creates a socks proxy on localhost. Followed by  <br><br>local_PORT|
|-N|Do not execute a remote command.  This is useful for just forwarding ports|

![](https://www.tunnelsup.com/images/ssh-local1.png)

In the above picture the user from blue server wants to connect to port 80 on the red server but the port is blocked by the firewall. 

User can connect through ssh and create a tunnel which would allow him to connect to port 80 on the red server.

In this case user can use Local port forwarding to connect the port on the red server to his local machine.  

To complete this task:

1. Setup **Dynamic Port Forwarding** using SSH.  
    **HINT:** `-i id_rsa -D 1337`  
    
2. Set up proxychains for the **Dynamic Port Forwarding**. Ensure you have commented out `socks4 127.0.0.1 9050` in your proxychains configuration and add `socks5 127.0.0.1 1337` to the end of configuration file (`/etc/proxychains.conf`).  
    The file name may vary depending on the distro you are using.  
    ![](https://imgur.com/eAPXSMq.png)  
    
3. Run a port scan to enumerate internal ports on the server using proxychains. If you use Nmap your command should look like this `proxychains nmap -sT 127.0.0.1` .
4. After finding the port of the webserver, perform **Local Port Forwarding** to that port using SSH with the **-L** flag.  
    **HINT**: `-i id_rsa -L 80:127.0.0.1:(remote port)` (Try using with sudo)


