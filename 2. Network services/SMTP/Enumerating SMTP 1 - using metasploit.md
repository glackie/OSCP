Poorly configured or vulnerable mail servers can often provide an initial foothold into a network, but prior to launching an attack, we want to fingerprint the server to make our targeting as precise as possible. 

	We're going to use the "smtp_version" module in MetaSploit to do this. 
	As its name implies, it will scan a range of IP addresses and determine the version of any mail servers it encounters.

The SMTP service has two internal commands that allow the enumeration of users: VRFY (confirming the names of valid users) and EXPN (which reveals the actual address of user’s aliases and lists of e-mail (mailing lists). Using these SMTP commands, we can reveal a list of valid users

	Metasploit comes to the rescue again, providing a handy module appropriately called "_smtp_enum_" that will do the legwork for us! Using the module is a simple matter of feeding it a host or range of hosts to scan and a wordlist containing usernames to enumerate.

 There are other, non-metasploit tools such as smtp-user-enum that work even better for enumerating OS-level user accounts on Solaris via the SMTP service. 
 Enumeration is performed by inspecting the responses to VRFY, EXPN, and RCPT TO commands.
 This technique could be adapted in future to work against other vulnerable SMTP daemons