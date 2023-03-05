```
sudo apt update
sudo apt  install bluetooth bluez bluez-tools rfkill
```



Thne start the bluetooth service 

```
$ systemctl is-enabled bluetooth.service 
enabled

$ systemctl start bluetooth.service


$ systemctl status bluetooth.service

```


Then search for bluetooth manager in application and open it



and connect your device




For audio, go to upper right corner in audia settings and specifically select bluetooth device



