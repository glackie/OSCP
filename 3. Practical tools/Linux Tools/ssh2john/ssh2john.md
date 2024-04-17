Here i used ssh2john because i was asked to  give the passphrase for id_rsa file

```
┌──(root💀kali)-[~/Desktop/THM]
└─# ssh -i id_rsa kay@10.10.220.173 
Enter passphrase for key 'id_rsa': 
```
which obviously i didnt knew

so i followed a machine writeup which used ssh2john and steps are below

```
┌──(root💀kali)-[~/Desktop/THM]
└─# ssh2john id_rsa > forjohn2.txt
```




Followed with

```
john forjohn2.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```
 

```
ssh -i id_rsa kay@10.10.220.173 
Enter passphrase for key 'id_rsa': 
```


## For Parrot OS

Turns out it is not installed in Parrot OS

To use ssh2jon in parrot OS

I used the below command

```
sudo python2 /usr/share/john/ssh2john.py secretKey > forjohn.txt
```

Turns out it does not works on python3

```
john forjohn.txt
```

And i got the Result

```
Proceeding with wordlist:/usr/share/john/password.lst, rules:Wordlist
letmein          (secretKey)
Proceeding with incremental:ASCII
letmein          (secretKey)

```


----

#### SSH2John Error

- incase you come across this 
```
john forjohn.hash --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
No password hashes left to crack (see FAQ
```

- Try
```
john --show forjohn.hash              ✔  18:32:19   
id_rsa:james13

1 password hash cracked, 0 left

```
