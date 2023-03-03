**Leverage LD_PRELOAD**

On some systems, you may see the LD_PRELOAD environment option.

![[Pasted image 20220924171817.png]]


LD_PRELOAD is a function that allows any program to use shared libraries. This [blog post](https://rafalcieslak.wordpress.com/2013/04/02/dynamic-linker-tricks-using-ld_preload-to-cheat-inject-features-and-investigate-programs/) will give you an idea about the capabilities of LD_PRELOAD. If the "env_keep" option is enabled we can generate a shared library which will be loaded and executed before the program is run. Please note the LD_PRELOAD option will be ignored if the real user ID is different from the effective user ID.

The steps of this privilege escalation vector can be summarized as follows;

1.  Check for LD_PRELOAD (with the env_keep option)
2.  Write a simple C code compiled as a share object (.so extension) file
3.  Run the program with sudo rights and the LD_PRELOAD option pointing to our .so file 