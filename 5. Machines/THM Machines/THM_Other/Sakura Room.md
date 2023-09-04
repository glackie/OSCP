
### Tip Off
- Downloaded the image using
```shell
wget https://raw.githubusercontent.com/OsintDojo/public/3f178408909bc1aae7ea2f51126984a8813b0901/sakurapwnedletter.svg
```

- found nothing embedded and also in the metadata

```
┌─[glack@parrot]─[~/Desktop/THM/sakura]
└──╼ $exiftool sakurapwnedletter.svg
```

- Used the writeup here, The image had some binary data into it, we can get the data using `inspect` and it translates to `A picture is worth 1000 words but metadata is worth far more`

- This was also included in the metadata `inkscape:export-filename="/home/SakuraSnowAngelAiko/Desktop/pwnedletter.png"` , i should have got this one.
- The ans is `SakuraSnowAngelAiko`


### RECONNAISSANCE

- Got the real name of hacker on https://twitter.com/AiKOABE3
`Aiko Abe`

- Found these encoded text on his twitter

`0a5c6e136a98a60b8a21643ce8c15a74`
and 
`b2b37b3c106eb3f86e2340a3050968e2`

- Got stuck in find the email used this writeup https://www.advisor-bm.com/post/consider-the-use-of-osint-to-identify-the-identity-of-the-hacker-in-a-particular-case, found out about the gpg key decoder and got the email ID of hacker
`SakuraSnowAngel83@protonmail.com`


### UNVEIL

- Used this writeup to identify the changed of user's Etheruem wallet address

https://www.advisor-bm.com/post/consider-the-use-of-osint-to-identify-the-identity-of-the-hacker-in-a-particular-case

`0xa102397dbeeBeFD8cD2F73A89122fCdB53abB6ef`

i could have also found this in the history of github commit `stratum://0xa102397dbeeBeFD8cD2F73A89122fCdB53abB6ef.Aiko:pswd@eu1.ethermine.org:4444`

again lack of impatience from my end


- other answers were (did not use writeup for this)
`ethermine` `tether`
### TAUNT
- first answer is `SakuraLoverAiko`
- Had to use the hint for this one, ans is 
```
deepv2w7p33xa4pwxzwi2ps4j62gfxpyp44ezjbmpttxz3owlsp4ljid.onion/show.php?md5=b2b37b3c106eb3f86e2340a3050968e2
```

also use hint for this one


`84:AF:EC:34:FC:F8`
### HOMEBOUND
- Used google image search for this `lake inawashiro`

- Also tried google reverse image search for other questions but was getting wrong at every attempt
- Used writeup for these
`DCA`, `HND` and `Hirosaki`

