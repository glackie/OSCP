Windows Remote Management, or WinRM, is a Windows-native built-in remote management protocol  
that basically uses Simple Object Access Protocol to interact with remote computers and servers, as well as  
Operating Systems and applications. 


WinRM allows the user to :  
→ Remotely communicate and interface with hosts  
→ Execute commands remotely on systems that are not local to you but are network accessible.  
→ Monitor, manage and configure servers, operating systems and client machines from a remote location.  


As a pentester, this means that if we can find credentials (typically username and password) for a user who  
has remote management privileges, we can potentially get a PowerShell shell on the host.