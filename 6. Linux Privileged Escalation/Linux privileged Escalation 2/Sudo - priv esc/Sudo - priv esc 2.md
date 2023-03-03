**Leverage application functions**  

Some applications will not have a known exploit within this context. Such an application you may see is the Apache2 server.

In this case, we can use a "hack" to leak information leveraging a function of the application. As you can see below, Apache2 has an option that supports loading alternative configuration files (`-f` : specify an alternate ServerConfigFile).


Loading the `/etc/shadow` file using this option will result in an error message that includes the first line of the `/etc/shadow` file.





