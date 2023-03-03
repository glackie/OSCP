SMTP stands for "Simple Mail Transfer Protocol". It is utilised to handle the sending of emails.

In order to support email services, a protocol pair is required, comprising of SMTP and POP/IMAP. 
Together they allow the user to send outgoing mail and retrieve incoming mail, respectively.

The SMTP service has two internal commands that allow the enumeration of users: 

	VRFY (confirming the names of valid users) and

	EXPN (which reveals the actual address of user’s aliases and lists of e-mail (mailing lists). 

	Using these SMTP commands, we can reveal a list of valid users

The SMTP server performs three basic functions:

	-    It verifies who is sending emails through the SMTP server.
	-    It sends the outgoing mail
	-    If the outgoing mail can't be delivered it sends the message back to the sender
