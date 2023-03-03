Netcat can also be used to transfer files, both text and binary, from one computer to another.

To send a file from our Kali virtual machine to the Windows system, we initiate a setup that is similar to the previous chat example, with some slight differences. 

On the Windows machine, we will set up a Netcat listener on port 4444 and redirect any output into a file called incoming.exe:

	C:\Users\offsec> nc -nlvp 4444 > incoming.exe listening on [any] 4444 ..

On the Kali system, we will push the wget.exe file to the Windows machine through TCP port 4444:


	kali@kali:~$ locate wget.exe /usr/share/windows-resources/binaries/wget.exe 
	
	kali@kali:~$ nc -nv 10.11.0.22 4444 < /usr/share/windows-resources/binaries/wget.exe (UNKNOWN) [10.11.0.22] 4444 (?) open

The connection is received by Netcat on the Windows machine as shown below:

	C:\Users\offsec> nc -nlvp 4444 > incoming.exe listening on [any] 4444 ... connect to [10.11.0.22] from  <UNKNOWN) [10.11.0.4] 43459
	^C
	C:\Users\offsec>

Notice that we have not received any feedback from Netcat about our file upload progress. 

In this case, since the file we are uploading is small, we can just wait a few seconds, then check whether the file has been fully uploaded to the Windows machine by attempting to run it:

	C:\Users\offsec> incoming.exe -h GNU Wget 1.9.1, a non-interactive network retriever. Usage: incoming [OPTION]... [URL]...

