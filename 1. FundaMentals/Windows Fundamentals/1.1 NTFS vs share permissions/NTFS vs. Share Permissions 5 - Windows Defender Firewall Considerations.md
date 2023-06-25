It is the Windows Defender Firewall that could potentially be blocking access to the SMB share. 

Since we are connecting from a Linux-based system the firewall has blocked access from any device that is not joined to the same `workgroup`. 

The primary difference between a workgroup and a Windows Domain in terms of authentication, is 

with a workgroup the local SAM database is used and in a Windows Domain a centralized network-based database (Active Directory) is used.

We must know this information when attempting to logon & authenticate with a Windows system. 

Consider where the htb-student account is hosted to properly connect to the target.

>when i completely disabled the firewall in windows system I was able to access the smb share file


In terms of the firewall blocking connections, this can be tested by completely deactivating each firewall profile in Windows or by enabling specific predefined inbound firewall rules in the `Windows Defender Firewall advanced security settings`. Like most firewalls, Windows Defender Firewall permits or denies traffic (access & connection requests in this case) flowing `inbound` &/or `outbound`

The different inbound and outbound rules are associated with the different firewall profiles in defender.

Windows Defender Firewall Profiles:

-   `Public`
-   `Private`
-   `Domain`
