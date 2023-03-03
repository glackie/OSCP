

how to login into a  machine using a private RSA key

glack@htbs: ssh -i <username>@<IP> 


> Note: sometimes you may find the username  in id_rsa.pub file

>Note: Dont forget to change the permission of id_rsa file to avoid any hassle to 600

	you can use the below command

┌──(root💀kali)-[~/Desktop/THM]
└─# chmod 400 id_rsa\(1\) 


