- How to get list of servics running on a windows system

```
PS C:\Users\htb-student> get-service
```


- Now to set names of services with update in their name

```
PS C:\Users\htb-student> get-service -name '**update**'
(there is a * in update word - dont forget)
```

- How to open computer management using run 

`compmgmt.msc
`

- How to get SID of users in the windows system

>note: you need to open cmd as administrator

```
C:\WINDOWS\system32>WMIC useraccount get name,sid
```


- How to create a new user in windows

```
click Control Panel, click User Accounts and Family Safety, and then click User Accounts. Click Manage another account.
```

- How to create new users and goups?

```
you can use computer management tool in windows to do the following
1. creating users
2. creating groups and adding users to the group
```







