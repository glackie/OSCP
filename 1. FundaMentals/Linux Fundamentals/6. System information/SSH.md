`Secure Shell` (`SSH`) refers to a protocol that allows clients to access and execute commands or actions on remote computers.

 On Linux-based hosts and servers running or another Unix-like operating system, SSH is one of the permanently installed standard tools and is the preferred choice for many administrators to configure and maintain a computer through remote access.

Syntax

glack@htb: ssh <username>@<IP addr>


Example: 

how to login into a  machine using a private RSA key

glack@htbs: ssh -i <username>@<IP> 

-I pkcs11
             Specify the PKCS#11 shared library ssh should use to communicate with a PKCS#11
             token providing the user's private RSA key.

	Note: username will be there in id_rsa.pub file


