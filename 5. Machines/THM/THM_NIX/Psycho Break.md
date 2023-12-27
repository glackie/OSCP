## Nmap


Port 21,22 an 80 are open


## Web

/sadistRoom - Get this in the source code


Easily gets the locker room key - 532219a04ab7a02b56faafbec1a4c1ea



Tizmg_nv_zxxvhh_gl_gsv_nzk_kovzhv - Decoded this using Alphabetical substituion on https://cryptii.com/

Tizmg_nv_zxxvhh_gl_gsv_nzk_kovzhv  - Grant_me_access_to_the_map_please


Now for the Abandoned Room used this writeup for some guidance - https://github.com/0xNirvana/Writeups/blob/master/TryHackMe/Easy/psychoBreak/psychobreak.md


Found another directory at http://10.10.80.200/SafeHeaven after using the medium wordlist


301        9l       28w      324c http://10.10.80.200/SafeHeaven/keeper


Did the reverse image search(St. Augustine Lighthouse) and got the key to Abandoned Room - 48ee41458eb0b43bf82b986cecf3af01


Now here I used the write up again - to understand the hint

http://10.10.80.200/abandonedRoom/be8bc662d1e36575a52da40beba38275/herecomeslara.php?shell=ls%20..


I had to put this at the end of the url
?shell=ls ..

Here i get access to another URL 
http://10.10.80.200/abandonedRoom/680e89809965ec41e64dc7e447f175ab/


Now i get to the Help Me part

Downloaded the zip file, unzipped and found out there is some contents in the Table.jpg using

```
bin walk -e Table.jpeg
```

i the extracted data, I can see a .wav file which contained morse code - 

Used this website to decode the morse code - https://morsecode.world/international/decoder/audio-decoder-adaptive.html

Decoded - SHOWME

Again needed some help here and used the writeup

Extracted the contents of Joseph.jpeg and got ftp credentails


we get two files, now we have to run this program with each word in the file and to do this i got this python script from the writeup

```
import os
import subprocess
import sys

f = open("random.dic", "r")

keys = f.readlines()

for key in keys:
	key = str(key.replace("\n", ""))
	print (key)
	subprocess.run(["./program", key])%
```

After running the program i got a hit at"kidman"

but it said to decode a string - 
55 444 3 6 2 66 7777 7 2 7777 7777 9 666 777 3 444 7777 7777 666 7777 8 777 2 66 4 33


I tried my standard methods and non worked except this 

https://www.dcode.fr/multitap-abc-cipher

KIDMANSPASSWORDISSOSTRANGE



## Gaining Access

I logged into the system using above credentails for kidman and got he the user flag

## Priv ESC

cat /etc/crontab revelas a python scipt was running with root privileges

I used the writeup and changed the contents of the using using

```
nano /var/.the_eye_of_ruvik.py
```


Added below line and got the root flag without the root access

```
subprocess.call("cat /root/root.txt > /home/kidman/.the_eye.txt",shell=True)
```


For the bonus part we had to delete all ruviks files for the target system



