i used zip2john to crack an password protected zip file using below command


─(root💀kali)-[~/Desktop/THM/_cutie.png.extracted]
└─# zip2john 8702.zip > zip.hash



and used the exported file in john the ripper

┌──(root💀kali)-[~/Desktop/THM/_cutie.png.extracted]
└─# john zip.hash  