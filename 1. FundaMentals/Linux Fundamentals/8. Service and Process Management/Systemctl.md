The Apache HTTP service is often used during a penetration test, either for hosting a site, or providing a platform for downloading files to a victim machine. 

The HTTP service is TCP-based and listens by default on port 80. To start the HTTP service in Kali, we can use systemctl as we did when starting the SSH service, replacing the service name with “apache2”


	kali@kali:~$ sudo systemctl start apache

Most services in Kali Linux are operated in much the same way as SSH and HTTP, through their service or init scripts. To see a table of all available services, run systemctl with the list-unit-files option:


	kali@kali:~$ systemctl list-unit-files

