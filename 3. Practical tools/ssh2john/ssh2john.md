Here i used ssh2john because i was asked to  give the passphrase for id_rsa file

┌──(root💀kali)-[~/Desktop/THM]
└─# ssh -i id_rsa kay@10.10.220.173 
Enter passphrase for key 'id_rsa': 

which obviously i didnt knew

so i followed a machine writeup which used ssh2john and steps are below


┌──(root💀kali)-[~/Desktop/THM]
└─# ssh2john id_rsa > forjohn2.txt



Followed with

──(root💀kali)-[~/Desktop/THM]
└─# john forjohn2.txt --wordlist=/usr/share/wordlists/rockyou.txt   


┌──(root💀kali)-[~/Desktop/THM]
└─# ssh -i id_rsa kay@10.10.220.173 
Enter passphrase for key 'id_rsa': 


