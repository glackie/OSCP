The comm command66 compares two text files, displaying the lines that are unique to each one, as well as the lines they have in common. It outputs three space-offset columns: the first contains lines that are unique to the first file or argument; the second contains lines that are unique to the second file or argument; and the third column contains lines that are shared by both files. The -n switch, where “n” is either 1, 2, or 3, can be used to suppress one or more columns, depending on the need. Let’s take a look at an example:

	kali@kali:~$ cat scan-a.txt 192.168.1.1 192.168.1.2
	192.168.1.3 
	192.168.1.4 
	192.168.1.5

	kali@kali:~$ cat scan-b.txt
192.168.1.1
192.168.1.3
192.168.1.4
192.168.1.5
192.168.1.6

	kali@kali:~$ comm scan-a.txt scan-b.txt
