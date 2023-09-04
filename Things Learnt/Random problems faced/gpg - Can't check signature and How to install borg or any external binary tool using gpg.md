
- Here i was trying to install a tool named borg
- https://asciinema.org/a/133291


- Downloaded the file
```
gpg: Can't check signature: No public ke
```

- Downloaded the public key

```
wget -q --show-progress https://github.com/borgbackup/borg/releases/download/1.1.0b6/borg-linux64.asc
```

- On verifying the key
```
gpg --verify borg-linux64.asc
```

here we get an RSA key and error 

`gpg:                using RSA key 243ACFA951F78E01`
`gpg: Can't check signature: No public key`

Here https://stackoverflow.com/questions/25074877/cant-check-signature-public-key-not-found I found the solution

- First we have to get the keys using the RSA key
```
┌─[glack@parrot]─[~/Desktop/THM]
└──╼ $gpg --receive-keys 243ACFA951F78E01
```


- And then we verify the public key again
```
─[glack@parrot]─[~/Desktop/THM]
└──╼ $gpg --verify borg-linux64.asc
```


- Then we add the binary to the `PATH`

```
┌─[glack@parrot]─[~/Desktop/THM]
└──╼ $sudo cp borg-linux64 /usr/local/bin/borg
[sudo] password for glack: 

┌─[glack@parrot]─[~/Desktop/THM]
└──╼ $sudo cp borg-linux64 /usr/local/bin/borg


sudo chmod 755 /usr/local/bin/borg
```