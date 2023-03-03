smtp-user-enum

Another tool that can be used is the **smtp-user-enum** which provides 3 methods of user enumeration.The commands that this tool is using in order to verify usernames are the EXPN,VRFY and RCPT.

Below is an example of a scan with the VRFY command which discovered the following usernames.

![](https://pentestlab.files.wordpress.com/2012/11/smtp2.jpeg?w=760 "SMTP User Enumeration via smtp-user-enum")

Syntax : 

smtp-user-enum -M VRFY -U /usr/wordlist -t 10.10.198.88


-M - which method to use

-U - username or wordlist of ussernames

-t - target IP



Added below resource as it seems pretty usefull

https://fareedfauzi.gitbook.io/oscp-notes/services-enumeration/smtp