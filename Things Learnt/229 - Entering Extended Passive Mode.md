The server received the EPSV command. The server successfully opened a data socket, and is listening on the socket. The IP address associated with the listening socket is the same as the IP address used for the control connection.

port_number is the port number associated with the listening data socket.



What can you do about it?





<mark style="background: #BBFABBA6;">Fix was using the command 'passive off' </mark>



 Dont know the reason for below commands but only using "passive off after ftp login worked for me



Extra: 'binary', followed by 'dir'.