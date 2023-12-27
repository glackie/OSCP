
---

- connected to the machine
```
   │   ~/De/THM/investigating_windows ▓▒░ xfreerdp /v:10.10.223.202 /u:Administrator /p:letmein123!
```

---

- opening the cmd gives us the windows 

version
```
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.
```

- we can also use `systeminfo` to get these details

---

- We can also see the on boot up the machine is trying to connect to `10.34.2.3`

- Also noticed after few minutes `C:/TMP/mim.exe` file is running with administrative privileges and a powershell promt is also opening

---

- used this command to find john's last logon details
```
C:\Users\Administrator>net user john
```

---
- used this command to find all users in the system

```
C:\Users\Administrator>net users

Administrator            DefaultAccount           Guest
Jenny                    John
```

used this for each user to find the users groups for all

```
C:\Users\Administrator>net user john
```
 
- Users `Jenny, Guest` are in the administrative group

- We can also use “`Computer Management`” tool from the “Server Manager” (or with a right-click on the Windows logo) and went to the “`Local Users and Groups`”.

---


- Now for scheduled tasks, found this commands that exports all schedules tasks in a csv file `
```cmd
schtasks.exe /query /V /FO CSV > scheduledTasks.csv
```

- another way i found on the google is 
```
To find scheduled tasks in Windows, there are several ways.2 One way is to go to the **Control Panel > [System & Security ] > Administrative Tools > Task Scheduler**.
```

- Task scheduler opens up, Clicked on `Task Status` then on `Active Taks`, found this task named `GameOVer` and has these details
    - Task will run as Administrator and will only run when the user is logged in
    - After the task is trigerred it will run after every five minutes
    - Action is that it will run a program at `C:\TMP\mim.exe` with the arguments `sekurlsa::LogonPasswords > C:\TMP\o.txt` 
    - It will only start the Task when computer is in AD power supply
    - Task can also be run on demand
    - History of the tasks is disabled


- One more task with name as `Clean file system` and Descriptions as `A task to clean old files of the system`
    - It run the program `C:\TMP\nc.ps1` with the arguments `-l 1348`
    - This malicious command listens on input argument `-l 1348` after running

---

- when running `net user Jenny` we find out the last logon of Jenny is `Never` but how could that be because it says Jenny last password was set on `3/2/2019`

- This means that the compromise had taken place on `3/2/2019` where the attacker may have changed `Jenny's` password and last logon details

---

- for the next question i opened the `Windows Event Viewer` tool from administrative tools. 
- As i already knew the attack took place on `3/2/2019`. Checked for security logs of this date.
- Used the hint that `49` in the last is where i have to look and found two alerts. 
- First one was for ``


```
Description:
An account was successfully logged on.

New Logon:
	Security ID:		SYSTEM
	Account Name:		SYSTEM
	Account Domain:		NT AUTHORITY

This event is generated when a logon session is created. It is generated on the computer that was accessed.

The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe.

The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
```

- The second log stated that 


```
Task Category: Special Logon
Description:
Special privileges assigned to new logon.

Subject:
	Security ID:		SYSTEM
	Account Name:		SYSTEM
	Account Domain:		NT AUTHORITY
	Logon ID:		0x3E7
```


`Date:          3/2/2019 4:04:49 PM`

---


- Now going thorough the `System` logs i found an infomration log with the content
```
The system time has changed to ‎2019‎-‎03‎-‎02T16:04:46.017240200Z from ‎2019‎-‎03‎-‎02T16:04:46.017240200Z.

Change Reason: System time adjusted to the new time zone.
```

- I remembered in [[Blueprint]] , a tool named `mimikatz` was used to dump password or hashes in the windows machine
- It is the right ans but i am not able to get the logs for it. Found nothing in events search.

---

- Used writeup for this 
- Found out the Windows hosts file (equivalent to Linux /etc/hosts) `C:\Windows\System32\drivers\etc\hosts`

- Here i saw some entries for google.com which should not be there
```
76.32.97.132 google.com
76.32.97.132 www.google.com
```

---

- Again used writeup for this. The questions was `What was the extension name of the shell uploaded via the servers website?`

- I didn't get the questions at first. Also noticed that there are multiple entries for `127.0.0.1` in the hosts file

- Asked this to ChatGPT - `where Document Root of your Website in windows`. In short the ans is 
    - Apache HTTP Server - C:\\Apache24\\htdocs 
    - IIS - C:\\inetpub\\wwwroot
    - Nginx - C:/nginx/html;
    - XAMPP - C:\\xampp\\htdocs

- So i found my ans in `C:\inetpub\wwwroot
- There were 3 files with `GIF` and `jsp` exetensions

---

- What was the last port the attacker opened? - Again used the writeup for this question 
- We can open the firewall dashbaord via administrative tools and in the last set Firewall rule we get out ans
- Details are 
    - Name - Allow outside connections for development
    - Port - 1337


---

- Now for the last questions, i asked ChatGPT `How to view DNS cache in windows server 2016`
- We can use the `ipconfig /displaydns` command to get contents of the DNS resolver cache, including the resolved DNS entries and their respective Time-to-Live (TTL) values.

- in the last section we can see that `google.com` site was resolved and the user was redirected to the  malicious IP 
```
 google.com
    ----------------------------------------
    Record Name . . . . . : google.com
    Record Type . . . . . : 1
    Time To Live  . . . . : 86400
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 76.32.97.132
```
