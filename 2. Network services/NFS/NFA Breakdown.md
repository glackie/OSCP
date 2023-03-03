Basically jab koi system

NFS   ke thorugh files share karra ho aur mujhe use files ko access karna hai to me

woh shared files ko apne devices pe mount kar dunga aur access kar paunga ki jaise woh mere local files hi hai

aur fir woh files ko apne mount directory se access kar paunga

par ye check karnekeliye ki target machine ne konse files ya path ko nfs ke thrugh share kiya hai woh check karneke liye ye command use karunga

	/usr/sbin/showmount -e [IP]



command: 

	sudo mount -t nfs IP:share /tmp/mount/ -nolock

type of device to mount, then specifying that it's NFS

IP:share - The IP Address of the NFS server, and the name of the share we wish to mount

-nolock - Specifies not to use NLM locking

