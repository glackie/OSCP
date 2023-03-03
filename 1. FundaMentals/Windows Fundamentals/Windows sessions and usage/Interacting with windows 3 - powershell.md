## Running Scripts

The PowerShell ISE (Integrated Scripting Environment) allows users to write PowerShell scripts on the fly. 

It also has an autocomplete/lookup function for PowerShell commands. The PowerShell ISE allows us to write and run scripts in the same console, which allows for quick debugging.

We can run PowerShell scripts in a variety of ways. If we know the functions, we can run the script either locally or after loading into memory with a download cradle

**Policy** - **Description**

`AllSigned` - All scripts can run, but a trusted publisher must sign scripts and configuration files. This includes both remote and local scripts. We receive a prompt before running scripts signed by publishers that we have not yet listed as either trusted or untrusted.

`Bypass` - No scripts or configuration files are blocked, and the user receives no warnings or prompts.

`Default` - This sets the default execution policy, Restricted for Windows desktop machines and RemoteSigned for Windows servers.

`RemoteSigned` - Scripts can run but requires a digital signature on scripts that are downloaded from the internet. Digital signatures are not required for scripts that are written locally.

`Restricted` - This allows individual commands but does not allow scripts to be run. All script file types, including configuration files (`.ps1xml`), module script files (`.psm1`), and PowerShell profiles (`.ps1`) are blocked.

`Undefined` - No execution policy is set for the current scope. If the execution policy for ALL scopes is set to undefined, then the default execution policy of `Restricted` will be used.

`Unrestricted` - This is the default execution policy for non-Windows computers, and it cannot be changed. This policy allows for unsigned scripts to be run but warns the user before running scripts that are not from the local intranet zone.

Below is an example of the current execution policy for all scopes.

```powershell-session
PS C:\htb> Get-ExecutionPolicy -List
```

The execution policy is not meant to be a security control that restricts user actions. 

A user can easily bypass the policy by either typing the script contents directly into the PowerShell window, downloading and invoking the script, or specifying the script as an encoded command. 

It can also be bypassed by adjusting the execution policy (if the user has the proper rights) or setting the execution policy for the current process scope (which can be done by almost any user as it does not require a configuration change and will only be set for the duration of the user's session).