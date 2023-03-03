Sc can also be used to configure and manage services. 

Let's experiment with a few commands.

```cmd-session
C:\Users\htb-student>sc qc wuauserv
```

The `sc qc` command is used to query the service. 

This is where knowing the names of services can come in handy. 

If we wanted to query a service on a device over the network, we could specify the hostname or IP address immediately after `sc`.

```cmd-session
C:\Users\htb-student>sc //hostname or ip of box query ServiceName
```


We can also use sc to start and stop services.

```cmd-session
C:\Users\htb-student> sc stop wuauserv

[SC] OpenService FAILED 5:

Access is denied.
```

Notice how we are denied access from performing this action without running it within an administrative context.

If we run a command prompt with `elevated privileges`, we will be permitted to complete this action.

If we were investigating a situation where we suspected that the system had malware, sc would give us the ability to quickly search and analyze commonly targeted services and newly created services.

It’s also much more script-friendly than utilizing GUI tools like `services.msc`.


