so basically i used this commands as per writeup instruction to extract a hidden file in an image


```
binwalk -e cutie.png
```




in my case i had to run it as


┌──(root💀kali)-[~/Desktop/THM]
└─# binwalk -e cutie.png --run-as=root



