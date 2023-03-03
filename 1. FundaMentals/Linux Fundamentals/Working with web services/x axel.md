axel79 is a download accelerator that transfers a file from a FTP or HTTP server through multiple connections. This tool has a vast array of features, but the most common is -n, which is used to specify the number of multiple connections to use. In the following example, we are also using the -a option for a more concise progress indicator and -o to specify a different file name for the downloaded file

	kali@kali:~$ axel -a -n 20 -o report_axel.pdf https://www.offensivesecurity.com/reports/penetration-testing-sample-report-2013.pdf 
	
	Initializing download: https://www.offensive-security.com/reports/penetration-testingFile size: 27691955 bytes Opening output file report_axel.pdf Starting download