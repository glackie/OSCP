- used this resource for the commands to be run

https://medium.com/@sherlock297/install-burp-suite-pro-on-kali-linux-5bef02afb8c3


- Download link

https://gofile.io/d/l6Bton


- extracted the zip file
```
   │   ~/Dow/burp suite pro ▓▒░ 7z e burpsuite_pro_v2023.8.7z
```


### JDK 18

instructions said to use java jdk 18


- Followed these steps
https://linuxhint.com/install-oracle-java-16-jdk-debian/

jdk download link
https://www.oracle.com/java/technologies/javase/jdk18-archive-downloads.html


- commands run

```
sudo apt install libc6-x32


   │   ~/Downloads ▓▒░ sudo dpkg -i jdk-18.0.2.1_linux-x64_bin.deb 

   │   ~/Downloads ▓▒░ export JAVA_HOME=/usr/lib/jvm/jdk-18

   │   ~/Downloads ▓▒░ export PATH=$JAVA_HOME/bin:$PATH
```

this created a double slash

```
  │   ~/Downloads ▓▒░ echo $PATH                        ░▒▓ ✔ │ 02:22:33   
/usr/lib/jvm/jdk-18//bin:/home/glack/.local/bin:/snap/bin:/usr/sandbox/:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/usr/share/games:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
```


- resolved this using

```
export PATH=/usr/lib/jvm/jdk-18/bin:/home/glack/.local/bin:/snap/bin:/usr/sandbox/:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/usr/share/games:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
```

now we get

```
   │   ~/Downloads ▓▒░ echo $PATH                        ░▒▓ ✔ │ 02:24:43   
/usr/lib/jvm/jdk-18/bin:/home/glack/.local/bin:/snap/bin:/usr/sandbox/:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/usr/share/games:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
```

now jdk is installed

we can confirm this by 
```
   │   ~/Downloads ▓▒░ java --version                ░▒▓ 127 ✘ │ 02:27:08   
java 18.0.2.1 2022-08-18
Java(TM) SE Runtime Environment (build 18.0.2.1+1-1)
Java HotSpot(TM) 64-Bit Server VM (build 18.0.2.1+1-1, mixed mode, sharing)
```

### Pro installation

- run the loader file
```
   │   ~/Dow/burp suite pro ▓▒░ java -jar burploader.jar
```

- this did not work now downlaoding a different one from 
```
https://www.nulled.to/topic/1538977-burpsuite-professional-download-2023101/?p=43835279
```



downlaod link
https://mega.nz/file/3jYTASaR#151G2aLep-KE25Jvksprnge7lhZ0sV7c_xwAcuJSyWQ


key
151G2aLep-KE25Jvksprnge7lhZ0sV7c_xwAcuJSyWQ

pass-
nulled.to


- extracted using 
```
  │   ~/Dow/burp suite pro ▓▒░ unrar e bp-pro-23-10-1.rar
```


- followed the request response and everything and got it running

added this to `.zshrc` file for quick access


```
alias burpro="/usr/lib/jvm/jdk-18/bin/java --add-opens=java.desktop/javax.swing=ALL-UNNAMED --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED --add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED --add-opens=java.base/jdk.internal.org.objectweb.asm.Opcodes=ALL-UNNAMED -javaagent:/home/glack/Downloads/burp_pro/burploader.jar -noverify -jar /home/glack/Downloads/burp_pro/burpsuite_pro_v2023.10.1.jar" 
```



```
alias burpro="/usr/lib/jvm/jdk-18/bin/java --add-opens=java.base/java.lang=ALL-UNNAMED javaagent:/home/glack/Downloads/burp_pro/burploader.jar -noverify -jar /home/glack/Downloads/burp_pro/burpsuite_pro_v2023.10.1.jar" 
```

this also did not work

- used this resource for reference 
https://github.com/GTekSD/BurpSuitePro-1.7.37-and-2022.8.5-Cracked

created a bash file 

`burp-suite-pro.sh`

with the contents

```
"/usr/lib/jvm/jdk-18/bin/java" "--add-opens=java.desktop/javax.swing=ALL-UNNAMED" "--add-opens=java.base/java.lang=ALL-UNNAMED" "--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED" "--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED" "--add-opens=java.base/jdk.internal.org.objectweb.asm.Opcodes=ALL-UNNAMED" "-javaagent:/home/glack/Downloads/burp_pro/burploader.jar" "-noverify" "-jar" "/home/glack/Downloads/burp_pro/burpsuite_pro_v2023.10.1.jar"
```


also created a alias in .zshrc

```
alias burpro="sh /home/glack/Desktop/burp-suite-pro.sh" 
```
