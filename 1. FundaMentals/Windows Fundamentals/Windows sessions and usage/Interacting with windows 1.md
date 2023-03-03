## Windows Command Line

 In Windows operating systems, the main two ways to interact with the system from the command line are via the Command Prompt (CMD) and PowerShell.

The [Windows Command Reference](https://download.microsoft.com/download/5/8/9/58911986-D4AD-4695-BF63-F734CD4DF8F2/ws-commands.pdf) from Microsoft is a comprehensive A-Z command reference which includes an overview, usage examples, and command syntax for most Windows commands, and familiarity with it is recommended.

## CMD

The Command Prompt (cmd.exe) is used to enter and execute commands. A user can enter one-off commands such as `ipconfig` to view IP address information or perform more advanced tasks such as setting up scheduled tasks or creating scripts and batch files. The Command prompt can be opened from the Start Menu, by typing `cmd` in the run dialogue box, or by directly launching the binary from `C:\Windows\system32\cmd.exe`.

After launching `cmd.exe` we can type `help` to see a listing of available commands.


For more information about a specific command, we can type `help <command name>`.

Note that certain commands have their own help menus, which can be accessed by typing `<command> /?`. 

example: 
```cmd-session
C:\htb> ipconfig /?
```

