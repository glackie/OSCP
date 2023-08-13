Windows PowerShell is a command shell that was designed by Microsoft to be more geared towards system administrators. PowerShell, like the Windows command line, has an interactive command prompt as well as a powerful scripting environment. PowerShell is built on top of the .NET Framework, which is used for building and running applications on Windows. This makes it a very powerful tool for interfacing directly with the operating system.

## Cmdlets

PowerShell utilizes [cmdlets](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/cmdlet-overview?view=powershell-7), which are small single-function tools built into the shell. 
There are more than 100 core cmdlets, and many additional ones have been written, or we can author our own to perform more complex tasks. PowerShell also supports both simple and complex scripts used for system administration tasks, automation, and more.

Cmdlets are in the form of `Verb-Noun`. For example, the command `Get-ChildItem` can be used to list our current directory. 

Cmdlets also take arguments or flags. We can type `Get-ChildItem -` and hit the tab key to iterate through the arguments. 

A command such as `Get-ChildItem -Recurse` will show us the contents of our current working directory and all subdirectories. 

Another example would be `Get-ChildItem -Path C:\Users\Administrator\Documents` to get the contents of another directory.

Finally, we can combine arguments such as this to list all subdirectories' contents within another directory recursively: `Get-ChildItem -Path C:\Users\Administrator\Downloads -Recurse`.

## Aliases

Many cmdlets in PowerShell also have aliases. 

For example, the aliases for the cmdlet `Set-Location`, to change directories, is either `cd` or `sl`. Meanwhile, the aliases for `Get-ChildItem` are `ls` and `gci`. 
We can view all available aliases by typing `Get-Alias`.

>We can also set up our own aliases with `New-Alias` and get the alias for any cmdlet with `Get-Alias -Name`.

PowerShell has a help system for cmdlets, functions, scripts, and concepts. 

This is not installed by default, but we can either run the `Get-Help <cmdlet-name> -Online` command to open the online help for a cmdlet or function in our web browser. 

We can type `Update-Help` to download and install help files locally.

Typing a command such as `Get-Help Get-AppPackage` will return just the partial help unless the Help files are installed. 


