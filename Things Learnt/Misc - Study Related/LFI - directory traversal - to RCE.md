
- We may have to use that LFI vulnerability to get remote code execution though various methods eg. remote file inclusion, php wrappers(expect://,php://input) code execution, log poisoning, leaked phpinfo file etc.

https://musyokaian.medium.com/team-tryhackme-walkthrough-a7ec9eb9adb7


- The wordlist i used was from Seclists.

`SecLists/Fuzzing/LFI/LFI-gracefulsecurity-linux.txt`

- in this example the vulnerable URL is `http://dev.team.thm/script.php?page=teamshare.php../../../../../etc/passwd`

and get this `sshd_config` file contents `/etc/ssh/sshd_config`