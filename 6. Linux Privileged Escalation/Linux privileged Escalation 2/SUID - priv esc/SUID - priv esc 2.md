The SUID bit set for the nano text editor allows us to create, edit and read files using the file owner’s privilege. Nano is owned by root, which probably means that we can read and edit files at a higher privilege level than our current user has. At this stage, we have two basic options for privilege escalation: reading the `/etc/shadow` file or adding our user to `/etc/passwd`.



We see that the nano text editor has the SUID bit set by running the `find / -type f -perm -04000 -ls 2>/dev/null` command.


  
`nano /etc/shadow` will print the contents of the `/etc/shadow` file.


We can now use the unshadow tool to create a file crackable by John the Ripper. To achieve this, unshadow needs both the `/etc/shadow` and `/etc/passwd` files.


in this example I was able to use base64 commands with SUID bit set


filename was flag3.txt

I was not able to access
 this file with my current user privileges but when i run the command


>base64 flag3.txt | base64 -d


I was able to see the contents of flag3.txt without root privileges

