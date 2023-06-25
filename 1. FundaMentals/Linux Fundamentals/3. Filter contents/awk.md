AWK is a programming language designed for text processing and is typically used as a data extraction and reporting tool. It is also extremely powerful and can be quite complex, so we will only scratch the surface here. A commonly used switch with awk6 is -F, which is the field separator, and the print command, which outputs the result text


example: 

	kali@kali:~$ echo "hello::there::friend" 
             | awk -F "::" '{print $1, $3}'
	
	hello friend

In Listing 47, we echoed a line and piped it to awk to extract the first ($1) and third ($3) fields using :: as a field separator. 

>The most prominent difference between the cut and awk examples we used is that cut can only accept a single character as a field delimiter, while awk, as shown in Listing 47, is much more flexible. 


As a general rule of thumb, when you start having a command involving multiple cut operations, you may want to consider switching to awk.