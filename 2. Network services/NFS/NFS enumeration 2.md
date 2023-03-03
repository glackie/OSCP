We can use 

	/usr/sbin/showmount -e [IP]

to show list of NFS shares of an Ip using showmount



How to mount the share to our machine 
	First, use "_mkdir /tmp/mount_" to create a directory on your machine to mount the share to. This is in the /tmp directory- so be aware that it will be removed on restart.


	now use 
	
	sudo mount -t nfs IP:share /tmp/mount/ -nolock

Here we can get to see multiple files shared by NFS server
These shares can contain some juicy contents such as ssh keys etc

	dont forget to use
	ls -al 
	to show hidden files as well