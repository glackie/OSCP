
[[Lunizz CTF]] - leant this here

https://siunam321.github.io/ctf/tryhackme/Lunizz-CTF/

---


According to [Wikipedia](https://en.wikipedia.org/wiki/Bcrypt) about bcrypt, the hash is:

```
$2<a/b/x/y>$[cost]$[22 character salt][31 character hash]
```

**For example, with input password abc123xyz, cost 12, and a random salt, the output of bcrypt is the string:**

```
$2a$12$R9h/cIPz0gi.URNNX3kh2OPST9/PgBkqquzi.Ss7KIUgO2t0jWMUW
\__/\/ \____________________/\_____________________________/
Alg Cost      Salt                        Hash
```


Now for this specific machine this python scirpt will be used to crack the hash

```
#!/usr/bin/env python3

import bcrypt
import base64

salt = b'your_bcrypt_salt'
bcrypt_hash = b'complete_bcrypt_hash'

with open('/usr/share/wordlists/rockyou.txt', 'r', encoding='latin-1') as f:
	for word in f.readlines():
		passw = word.strip().encode('ascii',      'ignore')
		b64str = base64.b64encode(passw)
		hashAndSalt = bcrypt.hashpw(b64str, salt)
		print('\r', end='') # Clear previous line
		print(f'[*] Cracking hash: {hashAndSalt}', end='')

		if bcrypt_hash == hashAndSalt:
			print('\n[+] Cracked!')
			print(f'[+] Before hashed: {passw}')
			print(f'[+] After hashed: {hashAndSalt}')
			exit()
```


- Also found another python script for same purpose
```
#!/usr/bin/env python3

import bcrypt
import base64


wordlist = []

with open('list.txt','rt') as file:
    f = file.readlines()
    wordlist = [ x.strip() for x in f ]

salt = b'$2b$12$SVInH5XmuS3C7eQkmqa6UO'
passwd = b'$2b$12$SVInH5XmuS3C7eQkmqa6UOM6sDIuumJPrvuiTr.Lbz3GCcUqdf.z6'

for word in wordlist:
    password = word
    bpass = password.encode('utf-8')
    passed= str(base64.b64encode(bpass))

    hashAndSalt = bcrypt.hashpw(passed.encode(), salt)
    
    #print(hashAndSalt)
    
    if hashAndSalt == passwd:
        print(f'found: {word}')
        break
```

but before this, here list.txt is ``sed -n '7288400,7288830p' /usr/share/wordlists/rockyou.txt > list.txt``


- In my case the `b'$2b$12$LJ3m4rzPGmuN1U/h0IO55.3h9WhI/A0Rcbchmvk10KWRMWe4me81e'`

https://github.com/xnomas/TryHackMe-Writeups/blob/main/Lunizz%20CTF/README.md - used this writeup


22 characters is salt and remaining 31 characters is the hash

salt - `$2b$12$LJ3m4rzPGmuN1U/h0IO55.`

hash - `3h9WhI/A0Rcbchmvk10KWRMWe4me81e`

- adjusting this to the above python script 

```
#!/usr/bin/env python3

import bcrypt
import base64


wordlist = []

with open('list.txt','rt') as file:
    f = file.readlines()
    wordlist = [ x.strip() for x in f ]

salt = b'$2b$12$LJ3m4rzPGmuN1U/h0IO55.'
passwd = b'$2b$12$LJ3m4rzPGmuN1U/h0IO55.3h9WhI/A0Rcbchmvk10KWRMWe4me81e'

for word in wordlist:
    password = word
    bpass = password.encode('utf-8')
    passed= str(base64.b64encode(bpass))

    hashAndSalt = bcrypt.hashpw(passed.encode(), salt)
    
    #print(hashAndSalt)
    
    if hashAndSalt == passwd:
        print(f'found: {word}')
        break
```
this did not work now trying this

```
#!/usr/bin/env python3

import bcrypt
import base64

salt = b'$2b$12$LJ3m4rzPGmuN1U/h0IO55.'
bcrypt_hash = b'$2b$12$LJ3m4rzPGmuN1U/h0IO55.3h9WhI/A0Rcbchmvk10KWRMWe4me81e'

with open('/usr/share/wordlists/rockyou.txt', 'r', encoding='latin-1') as f:
	for word in f.readlines():
		passw = word.strip().encode('ascii',      'ignore')
		b64str = base64.b64encode(passw)
		hashAndSalt = bcrypt.hashpw(b64str, salt)
		print('\r', end='') # Clear previous line
		print(f'[*] Cracking hash: {hashAndSalt}', end='')

		if bcrypt_hash == hashAndSalt:
			print('\n[+] Cracked!')
			print(f'[+] Before hashed: {passw}')
			print(f'[+] After hashed: {hashAndSalt}')
			exit()
```

took me approx 5 min and cracked the hash


```
─[glack@parrot]─[~/Desktop/THM/lunizz]
└──╼ $sudo python3 bcrypt_crack.py 
[*] Cracking hash: b'$2b$12$LJ3m4rzPGmuN1U/h0IO55.3h9WhI/A0Rcbchmvk10KWRMWe4me81e'
[+] Cracked!
[+] Before hashed: b'bowwow'
[+] After hashed: b'$2b$12$LJ3m4rzPGmuN1U/h0IO55.3h9WhI/A0Rcbchmvk10KWRMWe4me81e'
```