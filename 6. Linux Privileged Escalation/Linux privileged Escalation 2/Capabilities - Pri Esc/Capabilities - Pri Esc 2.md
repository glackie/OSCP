When run as an unprivileged user, `getcap -r /` will generate a huge amount of errors, so it is good practice to redirect the error messages to /dev/null.

	Please note that neither vim nor its copy has the SUID bit set. This privilege escalation vector is therefore not discoverable when enumerating files looking for SUID.

GTFObins has a good list of binaries that can be leveraged for privilege escalation if we find any set capabilities.  
  
We notice that vim can be used with the following command and payload:  
  

This will launch a root shell as seen below;

  ![[Pasted image 20220925184247.png]]

This will launch a root shell as seen below;

![[Pasted image 20220925184258.png]]


> first check which version of python is installed on the system and according make changes to your commmand

> Eg - commands mentioned in above image has py
> But when I checked for installed python version using"which python3". I go to know python3 is installed
> So i chanegs py to py3 in my priv esc command
