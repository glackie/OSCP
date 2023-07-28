How to decrypt files using gpg and asc files



For **[GnuPG](http://www.gnupg.org/)**, it's done like this.

Import the key:

gpg --import key.asc

Decrypt the file:

gpg --decrypt file.pgp

---

For **[PGP Command Line](http://www.pgp.com/products/commandline/)**, it's mostly the same:

pgp --import key.asc

pgp --decrypt file.pgp

        if the private_key.asc file has  a passphrase we can maybe crack it using the gpg2john utility

Example of it is below

```
┌[parrot]─[18:34-01/07]─[/home/glack/Desktop/THM/anonforce]
└╼glack$gpg2john private.asc > forjohn.txt

File private.asc

```


```
┌[parrot]─[18:35-01/07]─[/home/glack/Desktop/THM/anonforce]
└╼glack$john forjohn.txt --wordlist=/usr/share/wordlists/rockyou.txt 

```