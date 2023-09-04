

Apache2 stores its access log file under /var/www/apache2/access.log, which is also common to inject to achieve our goal.


What does this file do? It logs access requests from clients. 


So…. what if we can throw in some code to this log file to allow us to run commands? Enter Burpsuite